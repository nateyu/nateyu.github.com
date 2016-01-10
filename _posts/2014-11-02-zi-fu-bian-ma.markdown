---
layout: post
title: "字符编码"
date: 2014-11-02 21:30
comments: true
categories: linux
---

乱码是我们在使用计算机(不止编程)中经常遇到，如，网页显示乱码，Linux文本文件到Windows中乱码。最近花时间研究了一番，在这里做个总结。

字符编码就是要将字符转换为计算机里的二进制信息存储，当我们需要的时候读取的时候通过该编码方式将二进制转换为人能看懂的有意义的信息。字符编码需要经历一下几个过程：

1.字符表（Character repertoire）：知道一个系统需要支持哪些字符，比如，英文，中文日文和拉丁符号。     
2.编码字符集（CCS:Coded Character Set）：给字符表里的抽象字符编上一个数字，也就是字符集合到一个整数集合的映射。也就是给每一个要用到字符一个唯一的编号，这个是将字符到数字的一一映射。其中unicode就是这一层的，unicode是一个字符集。    
3.字符编码表（CEF:Character Encoding Form）：将字符集里字符对应的整数转换成有限长度的二进制位，便于计算机使用一定长度的二进制形式表示该整数。其中，utf-8, utf-16就是这个层面的。    
4.字符编码方案（CES:Character Encoding Scheme）：对于CEF得到的二进制值具体如何在计算机中进行存储，传输。因为存在大端小端的问题，这就会跟具体的操作系统相关了。这种解决方案称为字符编码方案。   


*ASCII*    
这个不用说是做早的字符编码方式，用1个字节表示128个英文字符95个可打印字符和33个控制字符，ASCII用7位来表示128个字符的8位用来奇偶校验。

*EASCII*    
随着计算机的发展到欧洲，128个字符已经不能满足使用了。于是人们就想到还有的8位没有用上，于是将第8位也用上总共字符集就翻倍了变成256，这个编码规则也常被称为EASCII。EASCII基本解决了整个西欧的字符编码问题。

*中文编码*    
中国的汉字数量远远超过ASCII码数量，有10万之多。1个字节最多只能表示256个字符，这个时候需要一种编码来解决中文字符，GB2312就是解决这个问题的。GB2312使用2个字节来编码汉字和符号，同时兼容ASCII码(ASCII还是一个字节存储)。中文编码还有BIG5, GBK和GB18030。他们的关系如下：

GB2312   
描述：国家简体中文字符集   
字节数：可变字节，ASCII用1个字节，汉字2字节   
范围：能表示7445个符号，包含6753个汉字   
兼容性： 兼容ASCII   

BIG5   
描述：统一繁体字符集   
字节数：2字节   
范围：能表示21886个符号   
兼容性： 兼容ASCII，但与GB2312冲突   

GBK   
描述：GB2312扩展，加入对繁体字的支持   
字节数：2字节   
范围：能表示21886个符号   
兼容性： 兼容GB2312   

GB18030   
描述：中日韩文字编码   
字节数：可变字节，1字节表示ASCII, 2字节和4字节   
范围：能表示27484个符号   
兼容性： 兼容GB2312   

*Unicode编码字符集*   
针对不同的语言采用不同的编码，有可能导致冲突与不兼容性，unicode统一编码世界所有的字符。Unicode字符集涵盖了目前人类使用的所有字符，并为每个字符进行统一编号，分配唯一的字符码（Code Point）。Unicode字符集将所有字符按照使用上的频繁度划分为17个层面（Plane），每个层面上有216=65536个字符码空间。

*Unicode编码字符集的实现方式*    
我们有了统一编码，但是怎样将Unicode编码存储到计算机里的呢？目前这种实现方式有utf-8, utf-16,utf-32和BOM。其中 utf-16是以2个字节存储utf-32是以4个字节存储，这样一来就有一个不好的地方，如ASCII字符本来是1个字节就够了，分配2个或4个字符会导致占用很多空间，导致普通文本的体积变大。这个时候utf-8恰好解决这个问题，utf-8以可变长度字节数来存储。

*utf-16、utf-32*   
utf(Unicode Transformation Format), 是规定如何将字符集里字符对应的整数转换成有限长度的二进制位以便存储。utf-16采用2个字节存贮unicode字符集。utf-32采用4个字节存贮unicode字符集。

*utf-8*   
utf-16、utf-32分别是采用固定的2个字节和4个字节存储，比较浪费空间。有没有一种能够根据字符大小可变的存储方式呢？utf-8就是这个编码方式，UTF-8用1～4个字节来表示字符。例如，原来的ASCII在utf8中继续采用1个字节， 而汉字则采用2个或3个字节。这样根据实际情况来采用可变长度编码大大节省了空间。

*Little endian和Big endian*   
一个字符可能占用多个字节, 在计算机中可以是'ABCD'也可以是'CDAB', 到底是采取什么顺序呢？实际上两者都有可能，并分别有不同的名字。如果存储为 AB CD，则称为Big Endian；如果存储为 CD AB，则称为Little Endian。因此，utf-16又有utf-16le, utf-16be。

*Mysql中的编码*   
查看MySQL中的字符编码：

```
show variables like 'character%';
```

得到如下结果：

```
character_set_client  utf8
character_set_connection  utf8
character_set_database  utf8
character_set_filesystem  binary
character_set_results utf8
character_set_server  utf8
character_set_system  utf8
character_sets_dir  /usr/local/Cellar/mysql/5.6.20/share/mysql/charsets/
```

其中，   
character_set_client: 为客户端编码方式， 也就是客户端连接向服务器发送请求，请求字符的编码方式；   
character_set_connection: 为建立连接使用的编码；   
character_set_database: 数据库的默认编码；   
character_set_results: 结果集的编码， 服务发送给客户端采用的编码方式；   
character_set_server: 数据库服务器的默认编码；  

导致我们查询结果乱码的主要是：set character_set_client，set character_set_connection，set character_set_results。


