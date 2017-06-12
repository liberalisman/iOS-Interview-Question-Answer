# Interview-QA

此篇是根据知名博主 [J-Knight](http://weibo.com/u/1929625262?from=feed&loc=nickname) 所提供的面试题目，所整理的答案，感谢 [J-Knight](http://weibo.com/u/1929625262?from=feed&loc=nickname) 的分享，原文在此 https://juejin.im/post/5938dfdb8d6d810058481572?utm_source=weibo&utm_campaign=user。

另外，我写此文的目的在于和广大的`iOS`开发者进行沟通交流，里面的内容多是自己的理解，如果有不正确的地方，希望大家多多指正。

面试题分为三个部分，我们先从基础开始。

## 基础
1. 为什么说Objective-C是一门动态的语言？

    其实`Objective-C`是一门动态语言的用`运行时Runtime`可以很好地说明，因为我看后面还有关于运行时的问题，此处就不展开了。
    1. 动态类型：例如“id”类型，动态类型属于弱类型，在运行时才决定消息的接收者
    2. 动态绑定：程序在运行时需要调用什么代码是在运行时决定的，而不是在编译时。
    3. 动态载入：程序在运行时的代码模块以及相关资源是在运行时添加的，而不是启动时就加载所有资源

    

