---
layout: post
title: "怎样在github上创建Blog"
date: 2013-12-17 00:02:27 +0800
comments: true
categories: iOS git github octopress
---

必须的东西

1.安装git

2.建立github账号

3.在github上建立username.github.io为名的仓库

然后运行下面的shell命令
{% highlight shell %}
git clone git://github.com/imathis/octopress.git octopress
cd octopress
bundle install
rake install //安装默认模板
rake setup_github_pages //按照提示填入信息
{% endhighlight %}
打开source/_config.yml填入Blog信息
{% highlight shell %}
rake generate
rake deploy //按照提示填入信息
rake new_post["title"] //创建新的文章
rake generate
rake preview
rake deploy
{% endhighlight %}

