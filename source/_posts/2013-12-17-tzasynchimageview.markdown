---
layout: post
title: "TZAsynchImageView"
date: 2013-12-17 23:09:39 +0800
comments: true
categories: iOS UIImageView
---

非ARC，支持文件、内存缓存

###使用方法
{% codeblock lang:objc %}
TZAsynchImageView *imageView = [[[TZAsynchImageView alloc] initWithUrlString:@"url" frame:self.view.bounds placeholder:[UIImage imageNamed:@"p.jpg"] autorelease];
{% endcodeblock %}
or
{% codeblock lang:objc %}
[imageView setUrlString:@"url"];
[imageView setUrlString:@"url" placeholder:[UIImage imageNamed:@"p.jpg"]];
{% endcodeblock %}
清除文件缓存
{% codeblock lang:objc %}
[TZAsynchDownloader clearCacheFilesBefore:[NSDate date]];
[TZAsynchDownloaderclearAllCacheFiles];
{% endcodeblock %}
清除内存缓存,每次退出一个界面的时候，可以清理一次
{% codeblock lang:objc %}
[[TZAsynchDownloader getInstance] clearMemoryFiles];
{% endcodeblock %}
###原理
>1. 查找urlString是否在cache，如果不在到2，否则执行6
2. 查找urlString是否在文件中，如果不在到3，否则把图片加入cache，执行6
3. 注册kDownloadedImage的消息，下载单例开始下载
4. 下载完成，存入cache，存入文件，发出kDownloadedImage消息
5. 所有注册kDownloadedImage的imageview执行1
6. 返回图片

项目地址:https://github.com/itianzhu/TZAsynchImageView
