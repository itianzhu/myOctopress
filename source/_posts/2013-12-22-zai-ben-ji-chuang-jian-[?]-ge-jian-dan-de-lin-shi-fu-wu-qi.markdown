---
layout: post
title: "在本机创建一个简单的临时服务器"
date: 2013-12-22 22:41:42 +0800
comments: true
categories: 
---

以前，我想要临时创建一个服务器供别人访问时，都会打开lamp，如果没有安装的话，还要安装半天，然后还有许多是不需要的东西。可以这样创建一个服务器,首先
```
gem install webrick
```
然后代码
```
require 'webrick'
server = WEBrcik::HTTPServer.new :port => 8000, :path => "./"
trap 'INT' do server.shutdowm end
server.start
```
这样就在当前命令行所在目录建立了一个静态目录。端口和根目录都可以改。
当然webrick不仅仅可以用来做静态访问，详情访问http://www.ruby-doc.org/stdlib-2.0/libdoc/webrick/rdoc/WEBrick.html。