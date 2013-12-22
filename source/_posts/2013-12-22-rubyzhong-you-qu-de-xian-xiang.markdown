---
layout: post
title: "Ruby中有趣的现象"
date: 2013-12-22 20:11:03 +0800
comments: true
categories: Ruby Metaprogramming
---

######ruby命令执行实际上是在Object的类中执行的，其实定义的方法都是Object的private方法，然而我们又可以改变方法的可见性，而所有的类都是Object的子类，所以可以通过这个办法，给所有的类添加方法,而类本身是Class类的子类，所以所有的类也有了这个添加的方法
```
def mm
  p 'mm print'
end
public mm
'a'.mm # => "mm print"
2.mm # => "mm print"
class A
end
A.mm # => "mm print"
```
