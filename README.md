# Interview-QA

此篇是根据知名博主 [J-Knight](http://weibo.com/u/1929625262?from=feed&loc=nickname) 所提供的面试题目，所整理的答案，感谢 [J-Knight](http://weibo.com/u/1929625262?from=feed&loc=nickname) 的分享，[点击产看原文。](https://juejin.im/post/5938dfdb8d6d810058481572?utm_source=weibo&utm_campaign=user。) 

另外，我写此文的目的在于和广大的`iOS`开发者进行沟通交流，里面的内容多是自己的理解，如果有不正确的地方，希望大家多多指正。

面试题分为三个部分，我们先从基础开始。

## 基础
### 1. 为什么说Objective-C是一门动态的语言？
其实`Objective-C`是一门动态语言的用`运行时Runtime`可以很好地说明，但我看后面还有关于`运行时`的问题，在此处就先不展开了。

    1. 动态类型：例如“id”类型，动态类型属于弱类型，在运行时才决定消息的接收者
    2. 动态绑定：程序在运行时需要调用什么代码是在运行时决定的，而不是在编译时。
    3. 动态载入：程序在运行时的代码模块以及相关资源是在运行时添加的，而不是启动时就加载所有资源


### 2.简要概括一下 **MVC** 和 **MVVM**，**MVP**模式

##### MVC
![](http://image.beekka.com/blog/2015/bg2015020105.png)

**`MVC`模式所有的模块通信都是单向的**
    1. `View`传递指令给`Controller`
    2. `Controller` 完成业务逻辑后，要求 `Model` 改变状态
    3. `Model` 将新的数据发送到 `View`，用户得到反馈

**还有一种是`Controller`直接接受指令**

![](http://image.beekka.com/blog/2015/bg2015020107.png)

##### MVP
**`MVP` 模式将 `Controller` 改名为 `Presenter`，同时改变了通信方向。**

![](http://image.beekka.com/blog/2015/bg2015020109.png)

1. 各部分之间的通信，都是双向的。
2. `View` 与 `Model` 不发生联系，都通过 `Presenter` 传递。
3. `View` 非常薄，不部署任何业务逻辑，称为"被动视图"（Passive View），即没有任何主动性，而 `Presenter`非常厚，所有逻辑都部署在那里。

##### MVVM
`MVVM` 模式将 `Presenter` 改名为 `ViewModel`，基本上与 `MVP` 模式完全一致。
![](http://image.beekka.com/blog/2015/bg2015020110.png)

唯一的区别是，它采用`双向绑定（data-binding）`：`View`的变动，自动反映在 `ViewModel`，反之亦然。`Angular` 和 `Ember` 都采用这种模式。


### 3.为什么代理要用weak？代理的delegate和dataSource有什么区别？block和代理的区别?

##### 1.首先，为什么代理要用weak？

**其实就是循环引用！！！**
我们一般在声明一个协议的时候，会定义一个代理属性，如果代理用的比较溜就会知道，一般都是别的类成为当前协议的代理，也就是说，代理实际是外部的一个类。代理属性的销毁不由当前协议类控制，而是由外部代理者自己控制。
如果在定义代理属性时，使用`Strong`，外界就无法销毁代理属性，造成循环引用，无法释放。

##### 2.代理的delegate和dataSource有什么区别。
`delegate` 和 `dataSource` 常见于`UITableView`和`UICollectionView`。
`dataSource`是数据源，决定了显示多少个`区域`，每个区域显示多少`行`，每行现实的`具体内容`,`头部,尾部`视图等。
`delegate`是交互行为的代理,比如`点击`，`取消选中`，`是否高亮`等等。

关于这个问题我有一些疑惑，比如`delegate`里面也有决定头部视图显示什么，尾部视图显示什么的方法，按我的理解应该在`DataSource`才对，请指教。

##### 3.block和代理的区别?

`Block`是带有局部变量的匿名函数，是一个代码段，`Block`更面向结果，他适合与状态无关的操作，例如直接返回某些值得时候，就比较适合用`Block`。

`delegate`回调则更加面向过程，例如执行的回调需要几个不同的步骤，这个时候使用`delegate`则更为合适


### 4.属性的实质是什么？包括哪几个部分？属性默认的关键字都有哪些？@dynamic关键字和@synthesize关键字是用来做什么的？



想深入了解，可以看一下详细的总结 : https://github.com/liberalisman/2018-Interview-Preparation#04-property

**1.实质就是 ivar（实例变量）、存取方法（access method ＝ getter + setter）。**

@property 的本质.

```objc
@property = ivar + getter + setter;
```

**2.属性可以拥有的特质分为四类:**

*  原子性--- nonatomic 特质,在默认情况下，由编译器合成的方法会通过锁定机制确保其原子性(atomicity)。如果属性具备 nonatomic 特质，则不使用自旋锁。请注意，尽管没有名为“atomic”的特质(如果某属性不具备 nonatomic 特质，那它就是“原子的” ( atomic) )，但是仍然可以在属性特质中写明这一点，编译器不会报错。若是自己定义存取方法，那么就应该遵从与属性特质相符的原子性。

*  读/写权限---readwrite(读写)、readonly (只读)

*  内存管理语义---assign、strong、 weak、unsafe_unretained、copy
*  方法名 - getter=<name> 、setter=<name>


**3.属性的默认关键字:**

```objc
@property (atomic，strong,readwrite) UIView *view;
```

**4.“自动合成”( autosynthesis)**

>完成属性定义后，编译器会自动编写访问这些属性所需的方法，此过程叫做“自动合成”(autosynthesis)。需要强调的是，这个过程由编译 器在编译期执行，所以编辑器里看不到这些“合成方法”(synthesized method)的源代码。除了生成方法代码 getter、setter 之外，编译器还要自动向类中添加适当类型的实例变量，并且在属性名前面加下划线，以此作为实例变量的名字。。也可以在类的实现代码里通过@synthesize 语法来指定实例变量的名字.


**5.@dynamic**
> 告诉编译器,属性的setter与getter方法由用户自己实现，不自动生成。

如果`@synthesize`和`@dynamic`都没写，那么默认的就是

```objc
@syntheszie var = _var;

// @synthesize的语义是如果你没有手动实现setter方法和getter方法，那么编译器会自动为你加上这两个方法。

// @dynamic告诉编译器,属性的setter与getter方法由用户自己实现，不自动生成。
```



**为了搞清属性是怎么实现的,反编译相关的代码,大致生成了五个东西**

```objc
1. OBJC_IVAR_$类名$属性名称 ：该属性的“偏移量” (offset)，这个偏移量是“硬编码” (hardcode)，表示该变量距离存放对象的内存区域的起始地址有多远。
2. setter 与 getter 方法对应的实现函数
3. ivar_list ：成员变量列表
4. method_list ：方法列表
5. prop_list ：属性列表
也就是说我们每次在增加一个属性,系统都会在 ivar_list 中添加一个成员变量的描述,
在 method_list 中增加 setter 与 getter 方法的描述,
在属性列表中增加一个属性的描述,
然后计算该属性在对象中的偏移量,
然后给出 setter 与 getter 方法对应的实现,
在 setter 方法中从偏移量的位置开始赋值,
在 getter 方法中从偏移量开始取值,
为了能够读取正确字节数,
系统对象偏移量的指针类型进行了类型强转.
```






