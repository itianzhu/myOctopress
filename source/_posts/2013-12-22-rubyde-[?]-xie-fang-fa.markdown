---
layout: post
title: "Ruby的一些方法"
date: 2013-12-22 21:06:53 +0800
comments: true
categories: Ruby
---

```
ObjectSpace.each_object(Complex) {|x| puts x }

num.kind_of? Numeric #只要是类的父类即可
num.instance_of? Numeric #只能是实例所属类

Fixnum.ancestors #返回父类和模块的列表


```
