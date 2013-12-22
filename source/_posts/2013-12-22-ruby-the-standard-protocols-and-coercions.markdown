---
layout: post
title: "Ruby The Standard Protocols and Coercions"
date: 2013-12-22 21:51:24 +0800
comments: true
categories: Ruby Coercions
---

##Standard Protocols
Ruby中定义了一些默认调用的方法，比如作为p 的参数时就会默认调用to_s
```
to_a
to_i
to_enum
to_hash
to_io
to_proc
#.......
```
下面是Symbol中实现的to_proc
```
def to_proc
  proc {|obj, *args| obj.send(self,*args)}
end
```
#####当用&调用时就会去调用这个方法，我们自己的类实现这个后，也可有这个功能
```
class A < String
  def to_proc
    proc {|obj, *args| obj.send(:length,*args)}
  end
end
a = A.new
words = %w{bbb cccc fffff}
words.map(&a)
```
##Coercions
在operate操作时，判定应该谁调用谁，所以有时候是参数调用了receiver。返回的第一个是参数，第二个是应该作为receiver。
```
1.coerce(2.3) # => [2.3, 1.0]
```
可以自己实现这个方法
```
class A
  def coerce other
    #===在Module上调用时，是子类的意思，
    #object上简单等同于==
    if Fixnum === other 
      [other,2]
    end
  end
end
a = A.new
1 + a
# => 3
a + 1 #如果要执行这个，先要def + other
```