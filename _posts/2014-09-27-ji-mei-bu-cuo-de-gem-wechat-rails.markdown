---
layout: post
title: "记一枚不错的Gem：wechat-rails"
date: 2014-09-27 23:04
comments: true
categories: ruby
---

很少因为某个gem写博客觉得只是某个工具，最近做微信发下一枚不错的gem, 不得不记一笔。

wechat-rails是一个微信开发者api接口的gem包，可以在rails和命令行中使用。它的主要优点有这么几个：

1. 提供命令行调用方式，大大方便了调试
2. DSL的事件处理方式，让开发者更关注具体业务逻辑
3. 最重要的作者对ruby的使用很熟练，gem设计很简洁明了

安装和使用自己去看他的readme不再这里重复了，我们来看看使用的几个要点。

###配置文件
命令行和rails的配置不一样，命令行需要在自己的home目录下面建立一个~/.wechat.yml文件，access_token是一个当前用户具有读写权限的文件目录，注意指定到具体的文件，用于存放获取的access_token。

```
  appid: "my_appid"
  secret: "my_secret"
  access_token: "/var/tmp/wechat_access_token"
```

rails配置是config/wechat.yml, 类似database.yml需要为每个环境分别配置。

```
default: &default
  appid: "app_id"
  secret: "app_secret"
  token:  "app_token"
  access_token: "/var/tmp/wechat_access_token"

production: 
  appid: <%= ENV['WECHAT_APPID'] %>
  secret: <%= ENV['WECHAT_APP_SECRET'] %>
  token:   <%= ENV['WECHAT_TOKEN'] %>
  access_token:  <%= ENV['WECHAT_ACCESS_TOKEN'] %>

staging: 
  <<: *default

development: 
  <<: *default

test: 
  <<: *default
```

###rails中使用
如果controller需要使用微信api, 可以直接在controller中声明。

```
class WechatsController < ApplicationController
  wechat_responder #将weichat-rails include进来

  on :text do |request, content|
    request.reply.text "echo: #{content}" #Just echo
  end

  on :text, with:"help" do |request, help|
    request.reply.text "help content" #回复帮助信息
  end

  on :fallback, respond: "fallback message"  
end
```

上面的代码，wechat_responder用于声明当前controller接受微信回调，one :text 代码块用于声明处理微信不同类型的回调消息，但是当两声类型明相同是只调用第一声明的代码块。fallback是默认处理方式。

###rails是如何调用api接口的
上面代码只是涉及到如何响应微信的回调请求，如果你想使用其他高级接口，比如：菜单修改，用户消息查询。怎么办呢？看看下面代码：

```
if defined? ActionController::Base
  class ActionController::Base
    def self.wechat_responder opts={}
      self.send(:include, Wechat::Responder)
      if (opts.empty?)
        self.wechat = Wechat.api
        self.token = Wechat.config.token
      else
        self.wechat = Wechat::Api.new(opts[:appid], opts[:secret], opts[:access_token])
        self.token = opts[:token]
      end
    end
  end
end
```
看到没有self.wechat = Wechat.api这个就是微信api接口实例，在当前controller中使用wechat就可以。如，获得关注者列表：

```
wechat.users
```

###wechat-rails是如何缓存access_token?
wechat-rails使用文件来缓存access_token， 也就是wechat.yml中配置的文件路径。wechat-rails会每次先读取这个文件的内容，当出现访问异常时，说明access_token过期了，就需要重新请求微信然后将新的access_token写入该文件。

这里有个问题，当我们在服务器集群上就不大好办了，除非你将access_token缓存文件置于共享目录。这样访问效率就低了。这里我的做法是使用memcache作为缓存，需要略作改进：

```
Wechat.class_eval do
  # 使用数据库存储微信appid, secret和token信息，同时支付多个微信号。
  # 因此，之前使用wechat.yml方式不再被支持
  def self.api
    raise 'we use database store token information, please use \'set_weixin_api :app_name\' inestad !'
  end
end

Wechat::AccessToken.class_eval do
  def cache_key
    "weixin/#{appid}"
  end

  # 使用Rails缓存来存储token, 覆盖原本的access_token使用文件存储方式
  def token
    @token_data = Rails.cache.read(cache_key) || refresh
    return valid_token(@token_data)
  end

  def refresh
    data = client.get("token", params:{grant_type: "client_credential", appid: appid, secret: secret})
    Rails.cache.write cache_key, data, expires_in: data["expires_in"]
    return @token_data = data
  end
end
```

将上面的代码至于config/initializers下面，就可以了。