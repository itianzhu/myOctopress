---
layout: post
title: "iOS使用protobuf传递聊天信息"
date: 2013-12-23 23:35:32 +0800
comments: true
categories: iOS protobuf im 聊天
---

protobuf是一种对象的序列化格式。
####使用
首先要创建定义文件,比如person.proto
```
message Person {
  required int32 id = 1;
  required string name = 2;
  optional string email = 3;
}
```
然后编译出定义文件的编译器。到[https://code.google.com/p/metasyntactic/downloads/list]()下载2.2.0的版本，然后执行
```
./autogen.sh

./configure

./make #在src文件夹下会生成protoc编译器

src/protoc --proto_path=src --objc_out=build/objc src/Person.proto
```
这样就会在src下生成person.p.m和person.p.h，这里面就有OC的Person类。  
然后到[https://github.com/itianzhu/protobuf-ios]()下载，并把runtime里面的工程，添加进入自己的工程，把上面生成的Person类文件也加入工程，导入#import "ProtocolBuffers.h"和person头文件就可以使用了
```
Person *person = [[[[Person builder] setId:1] setName:@"2"] build];
NSDate *data = [person data];
```



