---
layout: post
title: "[翻译]Ruby中的Catch和Throw"
date: 2013-12-26 23:38:55 +0800
comments: true
categories: Ruby
---
 
[原文地址：http://www.jacopretorius.net/2012/01/catch-and-throw-in-ruby.html](http://www.jacopretorius.net/2012/01/catch-and-throw-in-ruby.html)


异常应该是意外的，Ruby也提供一种轻量的实现。
###例子
```
def get_number
  rand(100)
end
 
random_numbers = catch (:random_numbers) do
  result = []
  10.times do
    num = get_number
    throw :random_numbers if num < 10
    result << num
  end
  result
end
```
catch带一个参数创建了一个block，这个block在throw触发之前正常执行。  
当throw触发时，Ruby会以它带的参数在调用栈中查找对应的catch语句。然后回退到catch那里，并且停止执行block。  
在这个例子中，block的返回值有两种可能，如果没有小于10的数字触发throw，那么数组会返回。如果触发了throw，那么会返回nil。也可以改变这个返回值，例如，可以通过给throw设置第二个参数，返回一个空数组
```
random_numbers = catch (:random_numbers) do
  result = []
  10.times do
    num = get_number
    throw(:random_numbers, []) if num < 10
    result << num
  end
  result
end
```
###性能
会导致性能降低是不应该在代码中使用太多异常的原因之一。每一个异常的抛出都会建立一个栈跟踪信息。如果你的异常的确是意外，倒没什么。但是如果是用于循环中的流程控制，就会有性能问题。而catch-throw不用建立跟踪信息。
```
start = Time.now
10_000_000.times do |i|
  begin
    raise StandardError, "Error #{i}"
  rescue StandardError => error
    error.inspect
  end
end
puts "Raise&Rescue Operation took #{Time.now - start} seconds"
 
start = Time.now
10_000_000.times do |i|
  catch (:the_loop) do
    throw :the_loop
  end
end
puts "Catch&Throw Operation took #{Time.now - start} seconds"

```
![](https://lh4.googleusercontent.com/-Y0q4RW_h-xo/TxXvrMtCCtI/AAAAAAAAAkA/dNT8gKtYmE4/s800/Screen%252520Shot%2525202012-01-17%252520at%2525205.00.06%252520PM.png)
这里的性能差异明显。
###总结
catch-throw适用于退出嵌套的程序流。