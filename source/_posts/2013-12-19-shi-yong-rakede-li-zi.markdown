---
layout: post
title: "使用rake的例子"
date: 2013-12-19 22:50:57 +0800
comments: true
categories: ruby rake
---

假设复制了一段代码到文件test/file.rb中(一定要在一个空文件夹里实验)，但是行首的行号也被复制了，现在需要把行号去掉
```
1 ruby1
2 ruby1
3 ruby1
4 ruby1
5 ruby1
```

在那个文件目录下，新建一个文件Rakefile写入以下代码
{% highlight ruby %}
desc "delete lineno"
task :delete_lineno do
  files = Dir['*.rb']
  files.each do |sourceFileName|
    newFileName = ["new_",sourceFileName].join("")
    open(sourceFileName,"r") do |sourceFile|
      open(newFileName,"w") do |newFile|
        while line = sourceFile.gets
          newFile.puts line.split[1]
        end
      end
    end
  end
end
{% endhighlight %}
然后再当前目录下执行
```
rake delete_no
```
在代码中没有使用File.open而是直接open，这是因为rake自动导入File。  
在文件中就会创建一个新的new_file.rb为需要的内容
要查看当前rake有哪些方法可以运行
```
rake -T
rake delete_no  # delete lineno
```

