---
layout: post
title: "iOS7的切换动画"
date: 2013-12-20 22:56:45 +0800
comments: true
categories: iOS UINavigationController Transition
---

iOS7中自定义ViewController切换动画
一、简介


1. 实现UIViewControllerAnimatedTransitioning协议，这个协议里的方法具体实现动画
2. 实现UINavigationControllerDelegate协议，这个协议里的的方法返回实现上一个协议的实例
3. 子类UINavigationController,这个就是具有自定义切换功能的实例，所有在这个实例中进行的push、pop都会有自定义的动画


二、文件简介
{% codeblock lang:objc %}
ITCornerTransiton.m //实现简介第1点
ITNavigationDelegate.m //实现简介第2点
ITNavigationController.m //实现简介第3点
{% endcodeblock %}


三、注意事项


1. 简介第1点的实现代码
{% codeblock lang:objc %}
toVC.view.frame = self.toBeganFrame;
[contrainerView addSubview:toVC.view];//(1)
[UIView animateWithDuration:[self transitionDuration:transitionContext] animations:^{
    fromVC.view.frame = self.fromEndFrame;
    toVC.view.frame = contrainerView.frame;
} completion:^(BOOL finished) {
    [fromVC.view removeFromSuperview];//(2)
    [transitionContext completeTransition:YES];//(3)
}];
{% endcodeblock %}
(1)新的view一定要自己addSubView  
(2)原来的view一定要自己remove  
(3)一定要通知 完成  
2. 简介第3点实现代码
{% codeblock lang:objc %}
self.navDelegate = [[ITNavigationDelegate alloc] init];
((ITNavigationDelegate*)self.navDelegate).delegateType = DelegateTypeBackSlash;
self.delegate = self.navDelegate;
{% endcodeblock %}
在ARC模式下，由于self.delegate是assige的，导致此处创建的ITNavigationDelegate会在方法最后被释放掉，所以一定要自己创建的一个属性来保存


具体信息查看代码https://github.com/itianzhu/TransitionDemo.git