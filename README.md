# Interview-Question & Answer （初级篇）

此篇是根据知名博主 [J-Knight](http://weibo.com/u/1929625262?from=feed&loc=nickname) 所提供的面试题目，所整理的答案，感谢 [J-Knight](http://weibo.com/u/1929625262?from=feed&loc=nickname) 的分享，[点击产看原文。](https://juejin.im/post/5938dfdb8d6d810058481572?utm_source=weibo&utm_campaign=user。) 

另外，我写此文的目的在于和广大的`iOS`开发者进行沟通交流，里面的内容有自己的理解，也有很大一部分参照网上的解释。很感谢之前的分享者，文末会附上相关的链接。如果在本文有理解不正确的地方，也希望大家多多指正。

面试题分为三个部分，我们先从基础开始。

[TOC]

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

##### 1.实质就是 ivar（实例变量）、存取方法（access method ＝ getter + setter）。

@property 的本质.

```objc
@property = ivar + getter + setter;
```

##### 2.属性可以拥有的特质分为四类:

*  原子性--- nonatomic 特质,在默认情况下，由编译器合成的方法会通过锁定机制确保其原子性(atomicity)。如果属性具备 nonatomic 特质，则不使用自旋锁。请注意，尽管没有名为“atomic”的特质(如果某属性不具备 nonatomic 特质，那它就是“原子的” ( atomic) )，但是仍然可以在属性特质中写明这一点，编译器不会报错。若是自己定义存取方法，那么就应该遵从与属性特质相符的原子性。

*  读/写权限---readwrite(读写)、readonly (只读)

*  内存管理语义---assign、strong、 weak、unsafe_unretained、copy
*  方法名 - getter=<name> 、setter=<name>


##### 3.属性的默认关键字:

```objc
@property (atomic，strong,readwrite) UIView *view;
```

##### 4.“自动合成”( autosynthesis)

>完成属性定义后，编译器会自动编写访问这些属性所需的方法，此过程叫做“自动合成”(autosynthesis)。需要强调的是，这个过程由编译 器在编译期执行，所以编辑器里看不到这些“合成方法”(synthesized method)的源代码。除了生成方法代码 getter、setter 之外，编译器还要自动向类中添加适当类型的实例变量，并且在属性名前面加下划线，以此作为实例变量的名字。。也可以在类的实现代码里通过@synthesize 语法来指定实例变量的名字.


##### 5.@dynamic
> 告诉编译器,属性的setter与getter方法由用户自己实现，不自动生成。

如果`@synthesize`和`@dynamic`都没写，那么默认的就是

```objc
@syntheszie var = _var;

// @synthesize的语义是如果你没有手动实现setter方法和getter方法，那么编译器会自动为你加上这两个方法。

// @dynamic告诉编译器,属性的setter与getter方法由用户自己实现，不自动生成。
```



##### 6.为了搞清属性是怎么实现的,反编译相关的代码,大致生成了五个东西

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


### 5.NSString为什么要用copy关键字，如果用strong会有什么问题？

`NSString`有可变的子类`NSMutableString`。因为父类指针可以指向子类，所以避免`NSMutableString`给`NSString`赋值，所以用`Copy`修饰。

`Copy`作为指针拷贝，是浅拷贝，保证了内容不会发生变化。此时如果使用`Strong`会在内存中新复制出一份。

但是如果可以保证，传过来的形参肯定不是`NSMutableString`的话，那么用`Strong`就可以，因为避免了`Copy`一次，反而提高了效率。

### 6.如何令自己所写的对象具有拷贝功能?

简单说就是遵守`NSCopying`,`NSMutableCopying`协议

并且实现`(id)copyWithZone:(NSZone *)zone`和`(id)mutableCopyWithZone:(NSZone *)zone`两个方法即可。

深入了解可看我的[其他文章](https://github.com/liberalisman/2018-Interview-Preparation#02-shallowcopy-deepcopy)。


### 7.`可变集合类` 和 `不可变集合类`的 `copy` 和 `mutablecopy` 有什么区别？如果是集合是内容复制的话，集合里面的元素也是内容复制么？


| 源对象类型 | 拷贝方式 | 副本对象类型 | 是否有新的对象 |
| --- | --- | --- | --- |
| NSArray | Copy | NSArray | NO |
| NSMutableArray | Copy | NSArray | YES |
| NSMutableArray | MutableCopy | NSMutableArray | YES |
| NSArray | MutableCopy | NSMutableArray | YES |


如果是集合内容复制，它的内容复制也分两种，一种是`单层复制`，一种是`完全复制`。上表的后三种全都是`单层内容复制`，只有最外面的容器被复制了，里面存储对象的指针地址不变。

### 8.为什么IBOutlet修饰的UIView使用weak关键字？
关于`IBOutlet`修饰的属性究竟是使用`strong`还是`weak`,网上的不同意见还是挺多的。

但我认为这可以分为两种情况：

1.如果从`storyBoard`或者`nib`拖出来的`插座属性`是`storyBoard`或者`nib`所直接拥有的，这个时候应该使用`Strong`修饰

2.如果是一个`storyBoard`或者`nib`的`子控件`再添加`子控件`,这个时候就应该用`weak`。

这么说可能比较不好理解。
![](http://okhqmtd8q.bkt.clouddn.com/Interview-QA/1.png)

此图控制器的View拖出来的线就是`strong`。
而如果往`View`上再次添加子控件的话，拖出来的线就是`weak`。

### 9.nonatomic和atomic的区别？atomic是绝对的线程安全么？为什么？如果不是，那应该如何实现？

##### 1.nonatomic和atomic的区别？
`atomic-原子性`

* 默认的属性
* 保证CPU在别的线程来访问这个属性之前，先执行完当前线程
* 速度较慢，因为要保证整体完成。

`nonatomic-非原子性`

* 非默认的属性
* 线程不安全，如果两个线程同时访问，会出问题
* 速度快

##### 2.atomic是绝对的线程安全么？如果不是，那应该如何实现？
很遗憾，并不是。一个线程在连续多次读取某个属性值的过程中有别的线程在同时改写该值，那么即便将属性声明为atomic，也还是会读取到不同的属性值。虽然`atomic-原子性`能保证不同的线程同时访问一个属性的时候，它的`Setter`和`getter`方法会有序执行，但如果此时有另一个线程调用该属性的`Release`方法，还是会出问题的，因为`atomic-原子性`只能管好它的`Setter`和`getter`方法。

因为开锁是很耗性能的，所以在移动端，一般使用`nonatomic`,而`Mac OS`不涉及到性能瓶颈，所在在`Mac OS`上使用`atomic`。

至于在`iOS`上保证属性在不同线程间访问的绝对安全，这块儿我暂时没有研究过，希望知道的朋友指教。



### 10.UICollectionView自定义layout如何实现？

自定义`Layout`需要实现以下几个步骤。

```swift
    // 1.collectionView每次需要重新布局(初始, layout 被设置为invalidated ...)的时候会首先调用这个方法prepareLayout()
    func prepareLayout()
    
    // 2.然后会调用layoutAttributesForElementsInRect(rect: CGRect)方法获取到rect范围内的cell的所有布局, 这个rect大家可以打印出来看下, 和collectionView的bounds不一样, size可能比collectionView大一些, 这样设计也许是为了缓冲
    func layoutAttributesForElementsInRect(rect: CGRect) -> [UICollectionViewLayoutAttributes]?
    
    // 3.当collectionView的bounds变化的时候会调用shouldInvalidateLayoutForBoundsChange(newBounds: CGRect)这个方法
    public func shouldInvalidateLayoutForBoundsChange(newBounds: CGRect) -> Bool
    
    // 4.需要设置collectionView 的滚动范围 collectionViewContentSize()
    // 自定义的时候, 必须重写这个方法, 并且返回正确的滚动范围, collectionView才能正常的滚动
    public func collectionViewContentSize() -> CGSize
    
    //  5.以下方法, Apple建议我们也重写, 返回正确的自定义对象的布局,因为有时候当collectionView执行一些操作(delete insert reload)等系统会调用这些方法获取布局, 如果没有重写, 可能发生意想不到的效果    
    // 自定义cell布局的时候重写
    public func layoutAttributesForItemAtIndexPath(indexPath: NSIndexPath) -> UICollectionViewLayoutAttributes?
    // 自定义SupplementaryView的时候重写
    public func layoutAttributesForSupplementaryViewOfKind(elementKind: String, atIndexPath indexPath: NSIndexPath) -> UICollectionViewLayoutAttributes?
    // 自定义DecorationView的时候重写
    public func layoutAttributesForDecorationViewOfKind(elementKind: String, atIndexPath indexPath: NSIndexPath) -> UICollectionViewLayoutAttributes?
    
    // 6.这个方法是当collectionView将停止滚动的时候调用, 我们可以重写它来实现, collectionView停在指定的位置(比如照片浏览的时候, 你可以通过这个实现居中显示照片...)
    public func targetContentOffsetForProposedContentOffset(proposedContentOffset: CGPoint, withScrollingVelocity velocity: CGPoint) -> CGPoint
```

### 11.用StoryBoard开发界面有什么弊端？如何避免？

其实关于`StoryBoard`还是出代码的开发方式，争吵声一直都存在，其实我个人并不反感`StoryBoard`,反而还挺喜欢。开发速度快，如果协调好，可以减轻很多工作量。不过关于`StoryBoard`这个话题如果展开的话还是比较大，建议大家读一下。喵神最近写的一篇文章，附上[原文链接](https://onevcat.com/2017/04/storyboard-argue/)，有异议的话也欢迎大家积极讨论。

### 12.进程和线程的区别？同步异步的区别？并行和并发的区别？

##### 1.进程和线程的区别？
> 进程:进程是指在系统中正在运行的一个应用程序。每个进程之间是独立的，每个进程均运行在其专用且受保护的内存空间内。
> 
> 线程：线程是进程的基本执行单元，一个进程的所有任务都在线程中执行。1个进程要想执行任务，必须得有线程，例如默认就是主线程。

##### 2.同步异步的区别？
> 同步函数：不具备开线程的能力，只能串行按顺序执行任务
> 
> 异步函数：具备开线程的能力，但不是只要是异步函数就会开线程。

##### 3.并行和并发的区别？
>并行：并行即同时执行。比如同时开启3条线程分别执行三个不同人物，这些任务执行时同时进行的。
>
>并发：并发指在同一时间里，CPU只能处理1条线程，只有1条线程在工作（执行）。多线程并发（同时）执行，其实是CPU快速地在多条线程之间调度（切换），如果CPU调度线程的时间足够快，就造成了多线程并发执行的假象。

### 13.线程间通信？

##### 1.NSThread

```objc
    // 第一种方式。
    [self performSelectorOnMainThread:@selector(showImage:) withObject:image waitUntilDone:YES];
    
    // 第二种方式
    [self.imageView performSelectorOnMainThread:@selector(setImage:) withObject:image waitUntilDone:YES];
```

##### 2.GCD

```objc
   //0.获取一个全局的队列
   dispatch_queue_t queue = dispatch_get_global_queue(0, 0);
   
   //1.先开启一个线程，把下载图片的操作放在子线程中处理
   dispatch_async(queue, ^{
      //2.下载图片
       NSURL *url = [NSURL URLWithString:@"http://h.hiphotos.baidu.com/zhidao/pic/item/6a63f6246b600c3320b14bb3184c510fd8f9a185.jpg"];
       NSData *data = [NSData dataWithContentsOfURL:url];
       UIImage *image = [UIImage imageWithData:data];
       NSLog(@"下载操作所在的线程--%@",[NSThread currentThread]);
       //3.回到主线程刷新UI
       dispatch_async(dispatch_get_main_queue(), ^{
          self.imageView.image = image;
          //打印查看当前线程
           NSLog(@"刷新UI---%@",[NSThread currentThread]);
       });
   });
   
   // GCD通过嵌套就可以实现线程间的通信。
```

##### 3.NSOperationQueue

```objc
    //1.创建队列
    NSOperationQueue *queue = [[NSOperationQueue alloc]init];

    //2.使用简便方法封装操作并添加到队列中
    [queue addOperationWithBlock:^{

        //3.在该block中下载图片
        NSURL *url = [NSURL URLWithString:@"http://news.51sheyuan.com/uploads/allimg/111001/133442IB-2.jpg"];
        NSData *data = [NSData dataWithContentsOfURL:url];
        UIImage *image = [UIImage imageWithData:data];
        NSLog(@"下载图片操作--%@",[NSThread currentThread]);

        //4.回到主线程刷新UI
        [[NSOperationQueue mainQueue] addOperationWithBlock:^{
            self.imageView.image = image;
            NSLog(@"刷新UI操作---%@",[NSThread currentThread]);
        }];
    }];
```

### 14.GCD的一些常用的函数？

##### 1.栅栏函数（控制任务的执行顺序）

```objc
    dispatch_barrier_async(queue, ^{
    
        NSLog(@"barrier");
    });
```

#####     2.延迟执行（延迟·控制在哪个线程执行）

```objc
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(2.0 * NSEC_PER_SEC)), dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        NSLog(@"---%@",[NSThread currentThread]);
    });
```

#####     3.一次性代码

```objc
   static dispatch_once_t onceToken;
   dispatch_once(&onceToken, ^{

       NSLog(@"-----");
   });
```

#####     4.快速迭代（开多个线程并发完成迭代操作）

```objc
    dispatch_apply(subpaths.count, queue, ^(size_t index) {
    });
```


#####     5.队列组（同栅栏函数）

```objc
    dispatch_group_t group = dispatch_group_create();
    // 队列组中的任务执行完毕之后，执行该函数
    dispatch_group_notify(dispatch_group_t group,dispatch_queue_t queue,dispatch_block_t block);

    // 进入群组和离开群组
    dispatch_group_enter(group);//执行该函数后，后面异步执行的block会被gruop监听
    dispatch_group_leave(group);//异步block中，所有的任务都执行完毕，最后离开群组
    //注意：dispatch_group_enter|dispatch_group_leave必须成对使用
```

### 15.如何使用队列来避免资源抢夺？

可以用**串行队列**或者是**同步锁**。保证在同一时间内，只有一条线程在访问资源。


### 16.数据持久化的几个方案

* plist文件（属性列表）
* preference（偏好设置）
* NSKeyedArchiver（归档）
* SQLite 3
* CoreData（FMDB）

在此不展开了，篇幅比较大，详情见我[另一篇文章](https://github.com/liberalisman/iOS-Summary-Part1#08-ios数据持久化方案)

### 17.说一下AppDelegate的几个方法？从后台到前台调用了哪些方法？第一次启动调用了哪些方法？从前台到后台调用了哪些方法？

1.应用程序启动，并进行初始化时候调用该方法：

```objc
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    }
```

 
2、应用进入前台并处于活动状态时候调用：

```objc
- (void)applicationDidBecomeActive:(UIApplication *)application {}
```
 
3、应用从活动状态进入到非活动状态：

```objc
- (void)applicationWillResignActive:(UIApplication *)application {}
```
 
4、应用进入到后台时候调用的方法:applicationDidEnterBackground：
 
 ```objc
- (void)applicationDidEnterBackground:(UIApplication *)application {}
 ```
 
5、应用进入到前台时候调用的方法:

```objc
- (void)applicationWillEnterForeground:(UIApplication *)application {}
```
 
6、应用被终止的状态:

```objc
- (void)applicationWillTerminate:(UIApplication *)application {}
```

### 18.NSCache优于NSDictionary的几点？

在做缓存时，优先使用`NSCache`而不是`NSDictionary`,我们熟悉的框架`SDWebimage`就是采用的`NSCache`。

`NSCache`优点如下：

1. 系统资源将要耗尽时，它可以自动删减缓存。
2. 可以设置最大缓存数量。
3. 可以设置最大占用内存值。
4. `NSCache`线程是安全的。

### 19.知不知道Designated Initializer？使用它的时候有什么需要注意的问题？

### 20.实现description方法能取到什么效果？

举例来说明吧

##### 1.我们创建一个自定义对象

```objc
@interface Person : NSObject

@property (nonatomic,copy  ) NSString *name;
@property (nonatomic,copy  ) NSString *hobbies;

@end
```

##### 2.在`ViewController`中，我们引用了`Person`类

```objc
- (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event
{
    Person *p = [[Person alloc] init];
    
    p.name = @"lili";
    
    p.hobbies = @"paint";
    
    NSLog(@"%@",p);
}
```
此时打印出来的结果如图：

```objc
2017-06-15 13:12:00.471 cop[1561:258406] <Person: 0x60800003dc80>
```

**是不是和你预计的效果还是差了一些？**

此时我们就需要重写对象的**`description`**方法

##### 3.此时在`Person`的`.m`文件中，我们进行如下操作：

```objc
#import "Person.h"

@implementation Person

- (NSString *)description {

    return [NSString stringWithFormat:@"_name = %@,_hobbies = %@",_name,_hobbies];
}
@end
```

再次打印

```objc
2017-06-15 13:21:20.132 cop[1593:275015] _name = lili,_hobbies = paint
```

 通过对比之后，大家一定就明白了


### 21.objc使用什么机制管理对象内存？

Objective-C使用`AEC自动引用计数`来有效的管理内存。

他遵循的原则是，谁引用，谁销毁。

`Retain`,`Copy`,`Alloc`,`New`等必然对应`Release`。

### 初级篇完结
起初乍一看感觉问题并不是很多，通过总结才发现面试官的准备十分充分，涵盖了很多方面，在总结的过程中，我也等于是复习了一遍。

目前针对初级篇的问题大致总结了一下，我看了中级以及高级的题目，**大致分为以下几类**：

* **Runtime**
* **RunLoop**
* **Block**
* **KVC & KVO**
* **三方框架的源代码解析（AFN、SDWebImage...)**
* **数据结构**

**再加上是基础题目里也有很多值得拓展的问题**

* **内存管理**
* **数据持久化**
* **多线程**
* **属性修饰符**
* **内存语义。。。。**


关于中高级的问题，我会每个话题单独开一篇来做仔细的分析，我心里并没有十足的把握，或许上面的回答也是漏洞百出，但是希望各位同行能多多指教，指出我的不足，在此先行谢过。

再次感谢 [J-Knight](http://weibo.com/u/1929625262?from=feed&loc=nickname) 童鞋准备的面试题，针对你的题目，写一篇答案，有些唐突，望见谅。


在整理这篇答案的时候，借鉴了很多网上的资料，很杂，也很难一一列出。

但是关于**MVC** 和 **MVVM**，**MVP**模式的那篇，借鉴了阮一峰老师的一篇文章，写的浅显易懂，十分不错。

链接在此：[MVC，MVP 和 MVVM 的图示](http://www.ruanyifeng.com/blog/2015/02/mvcmvp_mvvm.html)





