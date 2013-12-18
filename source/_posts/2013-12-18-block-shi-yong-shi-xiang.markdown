---
layout: post
title: "block 使用事项"
date: 2013-12-18 22:27:48 +0800
comments: true
categories: iOS block GCD
---
block使用事项

1.block是NSObject的子类

2.运行时的block存在栈stack上，比如
{% codeblock lang:objc %}
void (^greetingBlock)(void);           //声明block变量
{  // scope begins, local variables pushed onto stack
      //如 果没有此块的话，不会引起下面的stack pop off
  greetingBlock = ^{                   //定义blcok变量
    NSLog(@"Hello, World!");
  };
}  // scope ends, stack variables (e.g. the block literal) popped off stack
greetingBlock();  // Block invocation may cause the program to crash!
{% endcodeblock %}

3.block有block_copy()和block_release()方法，来保存在堆上。比如
{% codeblock lang:objc %}
void (^greetingBlock)(void);
{
  greetingBlock = [^{
    NSLog(@"Hello, World!");
} copy]; }
greetingBlock();          // Block invocation works (uses heap storage)
[greetingBlock release];  // released block to prevent memory leak
{% endcodeblock %}
或者
{% codeblock lang:objc %}
void (^greetingBlock)(id salutation);
{
  greetingBloc
  k = Block_copy(^(id salutation){
  NSLog(@"%@, World!", salutation);
  });
}
greetingBlock(@"Hello");
Block_release(greetingBlock);
{% endcodeblock %}
4.__block变量的处理。在MRR下，不会自动retain这个_block变量。在ARC下，会自动retain，如果不要此默认行为，需要替换使用__weak。


5.在block中使用self,而不产生引用循环，。在MRR下,4里面说过，在这种情况下，_block变量不会retain，但是要注意当[self dealloc]之后不能再调用block：
{% codeblock lang:objc %}
__block ClassName *that = self;
self.block = ^(CGFloat percentComplete) {
    [that doSome];
}
{% endcodeblock %}
在ARC下，使用__weak,当对象dealloc之后，会自动置为nil。
{% codeblock lang:objc %}
__weak ClassName *that = self;
__unsafe_unretained ClassName *that = self;//for iOS 4.x and up
self.block = ^(CGFloat percentComplete) {
    ClassName *this = that;//(1)
    [that doSome];
}//(2)
{% endcodeblock %}
如果有第(1)步，则会在block中产生一个本地的__strong变量，而ARC中所有的变量默认都是__strong的，这样会导致这个变量retained，而这个this本地变量会在第(2)处被释放掉。
