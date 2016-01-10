# Structure of jekyll
# .
# |-- _config.yml
# |-- _includes
# |-- _layouts
# |   |-- default.html
# |   `-- post.html
# |-- _posts
# |   |-- 2007-10-29-why-every-programmer-should-play-nethack.textile
# |   `-- 2009-04-26-barcamp-boston-4-roundup.textile
# |-- _site
# `-- index.html

require "bundler/setup"
Bundler.require :default, :development
require 'sass/plugin'
require 'sys/proctable'
include Sys

source_dir = '.'
posts_dir  = '_posts'
new_post_ext = 'markdown'
deploy_dir = '_site'
deploy_branch = 'deploy'

desc "Watch the site and regenerate when it changes"
task :update do
  Jekyll::Commands::Build.process({})
  # Jekyll::Site.new(Jekyll.configuration({})).process
end

desc "Preview blogs with jekyll"
task :preview => [:update] do
  exec_wrapping {
    Process.spawn({"OCTOPRESS_ENV"=>"preview"}, "jekyll --auto --server")
    Process.spawn("compass watch")
  }
end

desc "Begin a new post in #{source_dir}/#{posts_dir}"
task :post, :title do |t, args|
  if args.title
    title = args.title
  else
    title = get_stdin("Enter a title for your post: ")
  end

  mkdir_p "#{source_dir}/#{posts_dir}"
  filename = "#{source_dir}/#{posts_dir}/#{Time.now.strftime('%Y-%m-%d')}-#{title.to_url}.#{new_post_ext}"
  if File.exist?(filename)
    abort("rake aborted!") if ask("#{filename} already exists. Do you want to overwrite?", ['y', 'n']) == 'n'
  end
  puts "Creating new post: #{filename}"
  open(filename, 'w') do |post|
    post.puts "---"
    post.puts "layout: post"
    post.puts "title: \"#{title.gsub(/&/,'&amp;')}\""
    post.puts "date: #{Time.now.strftime('%Y-%m-%d %H:%M')}"
    post.puts "comments: true"
    post.puts "categories: "
    post.puts "---"
  end
end

desc "deploy public directory to github pages"
task :push => [:update]do
   system "git add ."
   system "git add -u"
   message = "Site updated at #{Time.now.utc}"
   puts message
   system "git commit -m \"#{message}\""
   system "git push origin HEAD:source"
   puts 'push deploy branch'
   if exist_branch?(deploy_branch)
      ptree = `git show-ref  -s refs/heads/#{deploy_branch}`
      commit = `git commit-tree HEAD:_site -p #{ptree.strip} -m \"#{message}\"`
      system "git update-ref refs/heads/#{deploy_branch} #{commit}"
   else
      puts "Creating a new branch #{deploy_branch}"
      commit = `git commit-tree source:_site -m \"#{message}\"`
      system "git branch #{deploy_branch} #{commit}"
   end
   system "git push origin #{deploy_branch}:master --force" if exist_branch?(deploy_branch)
end

def get_stdin(message)
  print message
  STDIN.gets.chomp
end

def exec_wrapping
  yield if block_given?

  pids = children_proc_of(Process.pid)
  trap("INT") {
    Process.kill(9, *pids) rescue Errno::ESRCH
    # Kill the children processes of compass and jekyll, ie,fsevent_watch on mac os
    Process.kill(9, *children_proc_of(*pids)) rescue Errno::ESRCH
    exit 0
  }
  pids.each { |pid| Process.wait(pid) }
end

def children_proc_of(*pids)
  tmp = pids.dup
  ProcTable.ps { |ps| pids << ps.pid if pids.include?(ps.ppid) }
  pids - tmp
end

def exist_branch?(branch)
   `git branch --list | grep #{branch}`.strip == branch
end
