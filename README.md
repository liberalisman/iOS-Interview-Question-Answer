
# Interview-Question & Answer （初级篇）


7月10日：（开中高级）
1. 增加了 **KVO & KVC** 的相关内容。






6月20日：
1. （第四题3小问）属性的默认关键字有更新，之前说的不全面，应该区分 **基本数据类型** 和 **对象类型**
2. （第五题） 答的也不全面，copy 是 **深拷贝** 还是 **浅拷贝**，取决于传过来的对象类型
        
----------------------



此篇是根据知名博主 [J-Knight](http://weibo.com/u/1929625262?from=feed&loc=nickname) 所提供的面试题目，所整理的答案，感谢 [J-Knight](http://weibo.com/u/1929625262?from=feed&loc=nickname) 的分享，[点击查看原文。](https://juejin.im/post/5938dfdb8d6d810058481572?utm_source=weibo&utm_campaign=user。) 

另外，我写此文的目的在于和广大的`iOS`开发者进行沟通交流，里面的内容有自己的理解，也有很大一部分参照网上的解释。很感谢之前的分享者，文末会附上相关的链接。如果在本文有理解不正确的地方，也希望大家多多指正。

面试题分为三个部分，我们先从基础开始。



## 基础
### 1. 为什么说Objective-C是一门动态的语言？
其实`Objective-C`是一门动态语言的用`运行时Runtime`可以更好地说明，但我看后面还有关于`运行时`的问题，在此处就先不展开了。

    1. 动态类型：例如“id”类型，动态类型属于弱类型，在运行时才决定消息的接收者
    2. 动态绑定：程序在运行时需要调用什么代码是在运行时决定的，而不是在编译时。
    3. 动态载入：程序在运行时的代码模块以及相关资源是在运行时添加的，而不是启动时就加载所有资源


### 2.简要概括一下 **MVC** 和 **MVVM**，**MVP**三种模式。

##### MVC
![](http://image.beekka.com/blog/2015/bg2015020105.png)

**`MVC`模式所有的模块通信都是单向的**（这一点个人持怀疑态度，希望大家提出意见）

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

关于这个问题我有一些疑惑，比如`delegate`里面也有决定头部视图显示什么，尾部视图显示什么的方法，按我的理解应该在`DataSource`才对，请大家指教。

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

*  **原子性--- nonatomic** 特质,在默认情况下，由编译器合成的方法会通过锁定机制确保其原子性`(atomicity)`。如果属性具备 `nonatomic` 特质，则不使用`自旋锁`。请注意，尽管没有名为`atomic`的特质(如果某属性不具备 `nonatomic` 特质，那它就是“原子的” ( `atomic`) )，但是仍然可以在属性特质中写明这一点，编译器不会报错。若是自己定义存取方法，那么就应该遵从与属性特质相符的原子性。

*  **读/写权限**---readwrite(读写)、readonly (只读)

*  **内存管理语义**---assign、strong、 weak、unsafe_unretained、copy
*  **方法名** - getter=<name> 、setter=<name>


##### 3.属性的默认关键字:
在 ARC 下，如果如果修饰的是 **Objective-C** 对象。

```objc
@property (atomic，strong,readwrite) UIView *view;
```
如果如果修饰的是基本数据类型。

```objc
@property (atomic，assign,readwrite) int num;
```



##### 4.“自动合成”( autosynthesis)

>完成属性定义后，编译器会自动编写访问这些属性所需的方法，此过程叫做**“自动合成”(autosynthesis)**。需要强调的是，这个过程由编译 器在编译期执行，所以编辑器里看不到这些“合成方法”(synthesized method)的源代码。除了生成方法代码** getter、setter** 之外，编译器还要自动向类中添加适当类型的实例变量，并且在属性名前面加下划线，以此作为实例变量的名字。
>也可以在类的实现代码里通过**@synthesize** 语法来指定实例变量的名字.


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

`NSString`有可变的子类`NSMutableString`。因为父类指针可以指向子类，避免`NSMutableString`给`NSString`赋值，造成原有的值被无形修改，所以用`Copy`修饰。

我们修饰 `NSString` 使用 `Copy` 关键字。

* 如果传进来的也是 `NSString`类型，这时候`Copy`作为指针拷贝，是浅拷贝，内容不会发生变化。

* 如果传进来的是 `NSMutableString`类型，这时候`Copy`作为内容拷贝，是深拷贝，在内存中新开辟出一块儿新的地址，防止原有的值被改变。


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
很遗憾，并不是。。虽然`atomic-原子性`能保证不同的线程同时访问一个属性的时候，它的`Setter`和`getter`方法会有序执行，但如果此时有另一个线程调用该属性的`Release`方法，还是会出问题的，因为`atomic-原子性`只能管好它的`Setter`和`getter`方法。

再者开锁是很耗性能的，所以在移动端，一般使用`nonatomic`,而`Mac OS`不涉及到性能瓶颈，所在在`Mac OS`上使用`atomic`。

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
    
    // 6.这个方法是当collectionView将停止滚动的时候调用,得到最终偏移量。我们可以重写它来实现, collectionView停在指定的位置(比如照片浏览的时候, 你可以通过这个实现居中显示照片...)
    public func targetContentOffsetForProposedContentOffset(proposedContentOffset: CGPoint, withScrollingVelocity velocity: CGPoint) -> CGPoint
```

### 11.用StoryBoard开发界面有什么弊端？如何避免？

其实关于用`StoryBoard`还是纯代码的开发方式，争吵声一直都存在，其实我个人并不反感`StoryBoard`,反而还挺喜欢。开发速度快，如果协调好，可以减轻很多工作量。不过关于`StoryBoard`这个话题如果展开的话还是比较大，建议大家读一下。喵神最近写的一篇文章，附上[原文链接](https://onevcat.com/2017/04/storyboard-argue/)，有异议的话也欢迎大家积极讨论。

### 12.进程和线程的区别？同步异步的区别？并行和并发的区别？

##### 1.进程和线程的区别？
> 进程:进程是指在系统中正在运行的一个应用程序。每个进程之间是独立的，每个进程均运行在其专用且受保护的内存空间内。
> 
> 线程：线程是进程的基本执行单元，一个进程的所有任务都在线程中执行。1个进程要想执行任务，必须得有线程，例如默认就是主线程。

##### 2.同步异步的区别？
> 同步函数：不具备开线程的能力，只能串行按顺序执行任务
> 
> 异步函数：具备开线程的能力，但并不是只要是异步函数就会开线程。

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
* SQLite 3 （FMDB）
* CoreData

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

这个问题没有想好该如何回答，希望大家指教。

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


关于中高级的问题，我会接下来做仔细的分析，我心里并没有十足的把握，或许上面的回答也是漏洞百出，但是希望各位同行能多多指教，指出我的不足，在此先行谢过。

在整理这篇答案的时候，借鉴了很多网上的资料，很杂，也很难一一列出。

但是关于**MVC** 和 **MVVM**，**MVP**模式的那篇，借鉴了阮一峰老师的一篇文章，写的浅显易懂，十分不错。

链接在此：[MVC，MVP 和 MVVM 的图示](http://www.ruanyifeng.com/blog/2015/02/mvcmvp_mvvm.html)

还有喵神的关于`storyBoard`那篇

链接在此：[再看关于 Storyboard 的一些争论](https://onevcat.com/2017/04/storyboard-argue/)



# Interview-Question & Answer （中高级）
## 1.KVC

关于`KVC`和`KVO`,我之前的总结文章有写过，但是实际上我在平日工作里，`KVC`和`KVO`使用的相对较少，不是`KVC`和`KVO`的功能不够强大，这实际上和项目的架构有比较大的关系，以前的我对于`KVC`和`KVO`的使用也是趋于表面，没有探究其内部真正的实现原理和进阶用法，这次总结正好给了我很好的学习机会，在此深入的总结一下`KVC`和`KVO`吧。

> **KVC**，即是指 **NSKeyValueCoding**，一个非正式的 **Protocol**，提供一种机制来间接访问对象的属性。**KVO** 就是基于 **KVC** 实现的关键技术之一。


### KVC在iOS中的定义

`Objective-C`中`KVC`的定义是对`NSObject`的扩展来实现的。所以对于所有继承了`NSObject`在类型，都可以使用`KVC`，下面是`KVC`最为重要的四个方法

```objc
- (nullable id)valueForKey:(NSString *)key;                          //直接通过Key来取值
- (void)setValue:(nullable id)value forKey:(NSString *)key;          //通过Key来设值
- (nullable id)valueForKeyPath:(NSString *)keyPath;                  //通过KeyPath来取值
- (void)setValue:(nullable id)value forKeyPath:(NSString *)keyPath;  //通过KeyPath来设值
```


一般来讲，**Obj-C** 对象中都会有一些属性。如代码所示

```objc
#import <Foundation/Foundation.h>
@interface Person : NSObject

/** name */
@property (nonatomic,copy  ) NSString *name;

/** Address */
@property (nonatomic,copy  ) NSString *address;

@end
```

<!-- more -->

上面的`Person`对象所拥有的两个属性，以 **KVC**的角度来看，就是`Person`对象的`name`,`address`这两个属性分别有一个`Value`对应他们的`Key`值。

* Key 是一个字符串类型。
* Value 可以为任何类型。

KVC 为存取值提供了两个最基础的方法。

```objc
    Person *man = [Person new];
    
    // 存值
    [man setValue:@"LiMing" forKey:@"name"];
    
    // 取值
    NSString *name = [man valueForKey:@"name"];
    
    NSLog(@"%@",name);
```

KVC 为了便于使用还提供了另外两个方法。

假设我们之前创建的这个对象有一个配偶，配偶也是一个Person对象,此时我们想在`man`这里读出`woman`的`name`属性

可以这样操作

```objc
    Person *woman = [Person new];
    
    man.spouse = woman;
    
    [man setValue:@"Lily" forKeyPath:@"spouse.name"];
    
    NSLog(@"%@",[man valueForKeyPath:@"spouse.name"]);
    
    //  Key 与 KeyPath 要区分开来
    //  Key 可以让你从一个对象中获取值
    //  KeyPath  可以让你通过连续的多个Key获取值，着多个key值用点号 “.” 分割连接起来
```

简单对比一下

```objc
    //  结果一样的，但是用 KeyPath 更简单
    [man valueForKeyPath:@"spouse.name"]

    [[man valueForKey:@"spouse"] valueForKey:@"name"];
```



### KVC寻找Key值过程

KVC在某种程度上提供了访问器的替代方案，不过只要有可能，KVC也是在访问器方法的帮助下工作。KVC按照以下顺序寻找Key值。
#### 1.赋值
当程序调用

```objc
- (void)setValue:(nullable id)value forKey:(NSString *)key;
```

##### 1.优先寻找访问器方法
程序会优先调用`setKey`的属性值方法，代码直接通过`Setter`方法完成设置。这里的`key`值指的是成员变量名，`Key`值首字母大写要符合`Setter`和`Getter`方法的命名规则。

##### 2.寻找_key
如果没有找到`setKey`的访问器方法，`KVC`机制会检查

```objc
+ (BOOL)accessInstanceVariablesDirectly
```
的返回值是否为`NO`，此方法默认返回的是`YES`。如果开发者重写了该方法让这个返回值为`NO`时，接下来`KVC`会直接调用

```objc
- (void)setValue:(id)value forUndefinedKey:(NSString *)key
```

这个时候如果你不做其他操作，就要报出异常了，所以一般人都不会这么做。

接下来`KVC`机制会搜索该类里面有没有`_key`的成员变量，无论你是在声明文件中定义，还是在实现文件中定义，也无论使用了什么样的属性修饰符，只要存在着`_key`命名的变量，`KVC`都可以对该成员变量赋值。

##### 3.寻找_isKey
如果该类即没有`setKey：`的访问器方法，也没有`_key`成员变量，`KVC`机制会搜索`_isKey`的成员变量。

##### 4.寻找Key和isKey
和上面一样，如果该类即没有`setKey：`的访问器方法，也没有`_key`和`_isKey`成员变量，`KVC`机制再会继续搜索`key`和`isKey`的成员变量。再给它们赋值。

如果上面列出的方法或者成员变量都不存在，系统将会执行该对象的`setValue：forUNdefinedKey：`方法，默认是抛出异常。

如果开发者想让这个类禁用`KVC`里，那么重写`+ (BOOL)accessInstanceVariablesDirectly`方法让其返回NO即可，这样的话如果`KVC`没有找到`set<Key>:`属性名时，会直接用`setValue：forUNdefinedKey：`方法。


#### 2.取值
当程序调用

```objc
- (nullable id)valueForKey:(NSString *)key;
```

##### 1.优先查找访问器的方法

首先按`getKey`、`key`、`isKey`的顺序查找`getter`方法，找到直接调用。如果是`bool`、`int`等内建值类型，会做`NSNumber`的转换。

##### 2.有序集合中查找

上面的`getter`没有找到，查找`countOfKey`、`objectInKeyAtIndex:`、`KeyAtIndexes`格式的方法。
如果`countOfKey`和另外两个方法中的一个找到，那么就会返回一个可以响应`NSArray`所有方法的代理集合。发送给这个代理集合的`NSArray`消息方法，就会以`countOfKey`、`objectInKeyAtIndex:`、`KeyAtIndexes`这几个方法组合的形式调用。还有一个可选的 `getKey:range:` 方法。

##### 3.无序集合中查找

还没查到，那么查找`countOfKey`、`enumeratorOfKey`、`memberOfKey:`格式的方法。
如果这三个方法都找到，那么就返回一个可以响应NSSet所有方法的代理集合。发送给这个代理集合的`NSSet`消息方法，就会以`countOfKey`、`enumeratorOfKey`、`memberOfKey:`组合的形式调用。

##### 4.搜索成员变量名
还是没查到，那么如果类方法`accessInstanceVariablesDirectly`返回`YES`，那么按`_key`，`_isKey`，`key`，`iskey`的顺序直接搜索成员名。

##### 5.报出异常
再找不到，调用`ValueForUndefinedKey:`，默认报出异常
 
### 针对集合的KVC
我们上面讲的`KVC`是一对一关系,比如`Person`类中的`name`属性。但也有一对多的关系，比如`Person`中有一个`friendsName`属性，保存的是一个人的所有朋友的名字，这时候就需要集合来处理了。

对于集合类的处理，我们有两种选择

#### 1.通过KVC将集合类先取出，然后在针对集合进行处理

#### 2.采用KVC提供的模板方法

##### 有序集合

这里面的**Key**，就是被监听的属性名称

```objc
-countOfKey  
//必须实现，对应于NSArray的基本方法count:  

- objectInKeyAtIndex:
- keyAtIndexes:  
//这两个必须实现一个，对应于 NSArray 的方法 objectAtIndex: 和 objectsAtIndexes: 
 
- getKey:range:  
//不是必须实现的，但实现后可以提高性能，其对应于 NSArray 方法 
- getObjects:range:  
  
- insertObject:inKeyAtIndex:  
- insertKey:atIndexes:  
//两个必须实现一个，类似于 NSMutableArray 的方法 insertObject:atIndex: 和 insertObjects:atIndexes:  

- removeObjectFromKeyAtIndex:  
- removeKeyAtIndexes:  
//两个必须实现一个，类似于 NSMutableArray 的方法 removeObjectAtIndex: 和 removeObjectsAtIndexes:  

- replaceObjectInKeyAtIndex:withObject:  
- replaceKeyAtIndexes:withKey:  
//可选的，如果在此类操作上有性能问题，就需要考虑实现之 
```
##### 无序集合

```objc
- countOfKey 
//必须实现，对应于NSArray的基本方法count: 
 
- objectInKeyAtIndex:  
- keyAtIndexes:  
//这两个必须实现一个，对应于 NSArray 的方法 objectAtIndex: 和 objectsAtIndexes:

- getKey:range:  
//不是必须实现的，但实现后可以提高性能，其对应于 NSArray 方法 
- getObjects:range:  
  
- insertObject:inKeyAtIndex:  
- insertKey:atIndexes:
//两个必须实现一个，类似于 NSMutableArray 的方法 insertObject:atIndex: 和 insertObjects:atIndexes:  

- removeObjectFromKeyAtIndex:  
- removeKeyAtIndexes:  
//两个必须实现一个，类似于 NSMutableArray 的方法 removeObjectAtIndex: 和 removeObjectsAtIndexes:

- replaceObjectInKeyAtIndex:withObject:  
- replaceKeyAtIndexes:withKey:  
//这两个都是可选的，如果在此类操作上有性能问题，就需要考虑实现之
```


### KVC对基本数据类型和结构体的支持

##### 1.对基本数据类型会以 NSNumber 进行包装

```objc
+ (NSNumber *)numberWithChar:(char)value;  
+ (NSNumber *)numberWithUnsignedChar:(unsigned char)value;  
+ (NSNumber *)numberWithShort:(short)value;  
+ (NSNumber *)numberWithUnsignedShort:(unsigned short)value;  
+ (NSNumber *)numberWithInt:(int)value;  
+ (NSNumber *)numberWithUnsignedInt:(unsigned int)value;  
+ (NSNumber *)numberWithLong:(long)value;  
+ (NSNumber *)numberWithUnsignedLong:(unsigned long)value;  
+ (NSNumber *)numberWithLongLong:(long long)value;  
+ (NSNumber *)numberWithUnsignedLongLong:(unsigned long long)value;  
+ (NSNumber *)numberWithFloat:(float)value;  
+ (NSNumber *)numberWithDouble:(double)value;  
+ (NSNumber *)numberWithBool:(BOOL)value;  
+ (NSNumber *)numberWithInteger:(NSInteger)value NS_AVAILABLE(10_5, 2_0);  
+ (NSNumber *)numberWithUnsignedInteger:(NSUInteger)value NS_AVAILABLE(10_5, 2_0);
```

##### 2.对结构体会以 NSValue 进行包装

```objc
+ (NSValue *)valueWithCGPoint:(CGPoint)point;  
+ (NSValue *)valueWithCGSize:(CGSize)size;  
+ (NSValue *)valueWithCGRect:(CGRect)rect;  
+ (NSValue *)valueWithCGAffineTransform:(CGAffineTransform)transform;  
+ (NSValue *)valueWithUIEdgeInsets:(UIEdgeInsets)insets;  
+ (NSValue *)valueWithUIOffset:(UIOffset)insets NS_AVAILABLE_IOS(5_0);  
```

所有的结构体都支持以`NSValue`进行封装




### KVC中的集合运算符

![](http://okhqmtd8q.bkt.clouddn.com/Interview-QA-Senior/1.png)

集合运算符是一个特殊的`KeyPath`，可以作为参数传递给`valueForKeyPath：`方法
##### 1.简单的集合运算符
简单的集合运算符有以下几个 `@avg`，`@count`，`@max`，`@min`，`@sum5`
##### 2.对象运算符
对象运算符有`@distinctUnionOfObjects`,
`@unionOfObjects`,这两个运算符返回的对象都是`NSArray`。

1.`@distinctUnionOfObjects`会将集合在剔除重复对象之后返回

2.`@unionOfObjects`会直接返回所有对象



### NSKeyValueCoding其他方法

```objc
+ (BOOL)accessInstanceVariablesDirectly;
//默认返回YES，表示如果没有找到SetKey方法的话，会按照_key，_iskey，key，iskey的顺序搜索成员，设置成NO就会直接抛出异常。

- (BOOL)validateValue:(inout id __nullable * __nonnull)ioValue forKey:(NSString *)inKey error:(out NSError **)outError;
//KVC提供属性值确认的API，它可以用来检查set的值是否正确、为不正确的值做一个替换值或者拒绝设置新值并返回错误原因。

- (NSMutableArray *)mutableArrayValueForKey:(NSString *)key;
//这是集合操作的API，里面还有一系列这样的API，如果属性是一个NSMutableArray，那么可以用这个方法来返回

- (nullable id)valueForUndefinedKey:(NSString *)key;
//如果Key不存在，且没有KVC无法搜索到任何和Key有关的字段或者属性，则会调用这个方法，默认是抛出异常

- (void)setValue:(nullable id)value forUndefinedKey:(NSString *)key;
//和上一个方法一样，只不过是设值。

- (void)setNilValueForKey:(NSString *)key;
//如果你在SetValue方法时面给Value传nil，则会调用这个方法

- (NSDictionary<NSString *, id> *)dictionaryWithValuesForKeys:(NSArray<NSString *> *)keys;
//输入一组key,返回该组key对应的Value，再转成字典返回，用于将Model转到字典。
```

没想到光一个`KVC`就写了这么多的内容，而越深入写就越觉得自己写的不过是皮毛，接下来再说说`KVO`吧。

## 2.KVO

### 1.认识KVO
`KVO`类似于观察者模式，我们利用简单的代码来了解什么是`KVO`。

```objc
//  注册一个Person类
#import <Foundation/Foundation.h>

@interface Person : NSObject
@property (nonatomic,copy) NSString *name;
@end

// 再注册一个Dog类
#import <Foundation/Foundation.h>

@interface Dog : NSObject
@property (nonatomic,copy  ) NSString *name;
@end
```

我们在`ViewController`中引入头文件，并创建两个全局的属性。我们希望`Person`作为`Dog`的观察者，当`Dog`的`name`属性发生变化的时候，`Person`可以第一时间知道。这时我们就可以运用`KVO`的技术。

```objc
    Person *p = [Person new];
    self.p = p;
    Dog *dog = [Dog new];
    self.dog = dog;
   
   // 成为其他对象的观察者要进行注册
   // KeyPath代表监听对象的具体属性
   // Observe就是观察者喽
   // Options可以指定管擦得值得新旧等
   // Context可以是任何对象，可以向观察者传递信息，也可以用指定的标识对不同的观察者进行区分
    [dog addObserver:p
          forKeyPath:@"name"
             options:(NSKeyValueObservingOptionNew | NSKeyValueObservingOptionOld)
             context:nil];

    dog.name = @"旺财";

```

监听选项`Options`是由枚举`NSKeyValueObservingOptions`定义的，他决定了哪些值可以被传入到观察者内部实现的方法中。

定义如下：

```objc
enum {
	   // 提供新值
   	NSKeyValueObservingOptionNew = 0x01,
   	
   	// 提供旧值
   	NSKeyValueObservingOptionOld = 0x02,
   	
   	// 添加观察者时立即发送一个通知给观察者，
   	// 并且是在注册观察者方法返回之前
   	NSKeyValueObservingOptionInitial = 0x04,
   	
   	// 如果指定，则在每次修改属性时，会在修改通知被发送之前预先发送一条通知给观察者，
   	// 这与-willChangeValueForKey:被触发的时间是相对应的。
   	// 这样，在每次修改属性时，实际上是会发送两条通知。
   	NSKeyValueObservingOptionPrior = 0x08 
};
typedef NSUInteger NSKeyValueObservingOptions;
//  选项值可以支持多个选项
```

注册之后，我们要在观察者内部实现如下方法

```objc
// 此时，当被观察者的属性发生变更，观察者就会自动调用如下方法
- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary<NSKeyValueChangeKey,id> *)change context:(void *)context {
    
    // keyPath被观察的属性值
    NSLog(@"keyPath = %@",keyPath);
    
    // object被观察的对象
    NSLog(@"object = %@",object);
    
    // 被观察属性值得变化，后面还会讲
    NSLog(@"change = %@",change);
    
    // 上下文，也可以是任意的额外数据
    // 这个Context的作用十分重要，我在后面会强调
    NSLog(@"context = %@",context);
}
// 我们通过这个方法，可以得到一些关键信息
```


`Change`选项，它记录了被监听属性的变化情况。可以通过key来获取值：

```objc

// 属性变化的类型，是一个NSNumber对象，包含NSKeyValueChange枚举相关的值
NSString *const NSKeyValueChangeKindKey;

// 属性的新值。当NSKeyValueChangeKindKey是 NSKeyValueChangeSetting，
// 且添加观察的方法设置了NSKeyValueObservingOptionNew时，我们能获取到属性的新值。
// 如果NSKeyValueChangeKindKey是NSKeyValueChangeInsertion或者NSKeyValueChangeReplacement，
// 且指定了NSKeyValueObservingOptionNew时，则我们能获取到一个NSArray对象，包含被插入的对象或
// 用于替换其它对象的对象。
NSString *const NSKeyValueChangeNewKey;

// 属性的旧值。当NSKeyValueChangeKindKey是 NSKeyValueChangeSetting，
// 且添加观察的方法设置了NSKeyValueObservingOptionOld时，我们能获取到属性的旧值。
// 如果NSKeyValueChangeKindKey是NSKeyValueChangeRemoval或者NSKeyValueChangeReplacement，
// 且指定了NSKeyValueObservingOptionOld时，则我们能获取到一个NSArray对象，包含被移除的对象或
// 被替换的对象。
NSString *const NSKeyValueChangeOldKey;

// 如果NSKeyValueChangeKindKey的值是NSKeyValueChangeInsertion、NSKeyValueChangeRemoval
// 或者NSKeyValueChangeReplacement，则这个key对应的值是一个NSIndexSet对象，
// 包含了被插入、移除或替换的对象的索引
NSString *const NSKeyValueChangeIndexesKey;

// 当指定了NSKeyValueObservingOptionPrior选项时，在属性被修改的通知发送前，
// 会先发送一条通知给观察者。我们可以使用NSKeyValueChangeNotificationIsPriorKey
// 来获取到通知是否是预先发送的，如果是，获取到的值总是@(YES)
NSString *const NSKeyValueChangeNotificationIsPriorKey;

```

`NSKeyValueChangeKindKey`的值取自于`NSKeyValueChange`，这是一个枚举值，定义如下

```objc
enum {
	// 设置一个新值。被监听的属性可以是一个对象，也可以是一对一关系的属性或一对多关系的属性。
   	NSKeyValueChangeSetting = 1,
   	
   	// 表示一个对象被插入到一对多关系的属性。
   	NSKeyValueChangeInsertion = 2,
   	
   	// 表示一个对象被从一对多关系的属性中移除。
   	NSKeyValueChangeRemoval = 3,
   	
   	// 表示一个对象在一对多的关系的属性中被替换
   	NSKeyValueChangeReplacement = 4
};
typedef NSUInteger NSKeyValueChange;
```

注意，观察者在不需要使用的时候一定要移除，否则会产生崩溃

```objc
- (void)dealloc {

    [self.dog removeObserver:self.p forKeyPath:@"name"];
}
```

通过上面简要的代码示例，我们可以得知，时运观察者只需要实现简单的几步。

1. 注册观察者
2. 观察者实现相应的方法
3. 移除观察者


### 2.KVC和KVO的实现原理
`KVC`和`KVO`是基于强大的`Runtime`来实现的。其中使用到的技术就是`isa-swilling`,`isa-swilling`这项技术也是一个重点，我们会在后续的`Runtime`部分会讲到。如果有看到此处不明白的同学也请保持耐心。

网上有一篇文章针对实现原理写的很好，[链接在此](http://blog.csdn.net/kesalin/article/details/8194240)。

整体来说就是，当某个类的对象第一次被观察时，系统会在运行期间动态的为这个类创建一个派生类，假如被监听类名为`ClassA`，那么派生类的名称就为`NSKVONotifying_ClassA`。

1.原有对象的`isa`指针会指向全新的派生类，派生类为了混淆，避免别人知道他不是原来的类，所以派生类重写了`Class`的类方法。

2.同时重写了`Dealloc`方法，用于资源的销毁处理。

3.还重写了`_isKVOA`,这个是一个标记，用于标示这个类是遵守`KVO`机制的。

4.最关键的是重写了被监听属性的`Setter`方法，这是实现`KVO`的关键。至于为什么，后面会讲解到。

简单的画了张图，可能会有助于理解。
![](http://okhqmtd8q.bkt.clouddn.com/Interview-QA-Senior/2.png)

我们上面讲重写了被观察对象属性的`Setter`方法是十分关键的，这就要说起另外两个十分重要的方法

```objc
// 在属性值即将被修改的时候，会调用这个方法
- (void)willChangeValueForKey:(NSString *)key;

// 在属性值已经被修改的时候，会调用这个方法
- (void)didChangeValueForKey:(NSString *)key;

// didChangeValueForKey:方法会显式的调用
- (void)observeValueForKeyPath:(NSString *)keyPath 
                      ofObject:(id)object 
                        change:(NSDictionary<NSKeyValueChangeKey,id> *)change 
                       context:(void *)context {
                       }
```

其实我个人猜测，重写`Setter`方法内部应该这样实现的

```objc
[self willChangeValueForKey:@"name"];
[super setName:name];
[self didChangeValueForKey:@"name"];
```

说到这里，相信你应该完整的明白KVO的实现机制了。

```objc
// 这才是KVO机制触发的关键
- (void)didChangeValueForKey:(NSString *)key;
```



### 3.调用KVO的三种方法

综合上面KVO的实现原理，我们可以得出如下结论：

##### 1.使用了KVC
使用了`KVC`，如果有`访问器方法`，则运行时会在访问器方法中调用`will/didChangeValueForKey:`方法；
没用访问器方法，运行时会在`setValue:forKey`方法中调用`will/didChangeValueForKey:`方法。

##### 2.有访问器方法
运行时会重写访问器方法调用`will/didChangeValueForKey:`方法。
因此，直接调用访问器方法改变属性值时，KVO也能监听到。

##### 3.直接调用
显式调用`will/didChangeValueForKey:`方法。

### 4.KVO自动通知、手动通知


通常意义下我们使用的都是自动通知，注册观察者之后，当触发`will/didChangeValueForKey:`方法后，观察者对象的`- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary<NSKeyValueChangeKey,id> *)change  context:(void *)context { }`方法会被调用。

如果像实现手动通知，我们需要借助一个额外的方法

```objc
+ (BOOL)automaticallyNotifiesObserversForKey:(NSString *)key
```

这个方法默认返回`YES`,用来标记`Key`指定的属性是否支持`KVO`，如果返回值为`NO`，则需要我们手动更新。


我们还是用我们最上面的例子，监听`Person`的`name`属性，不过这次我们采取手动通知的方式。

```objc
#import "Person.h"

@implementation Person

+ (BOOL)automaticallyNotifiesObserversForKey:(NSString *)key {

    BOOL automaic = NO;
    if ([key isEqualToString:@"name"])
    {
        automaic = NO;
    }
    else
    {
        // 此处需要注意，没有被处理的其他属性要调用父类的原有方法
        automaic = [super automaticallyNotifiesObserversForKey:key];
    }
    return automaic;
}
@end


```

这样我们就已经标记好当`Person`的`name`属性发生改变时，手动发送通知，代码如下：

```objc
@implementation Person

- (void)setName:(NSString *)name {
    
    if(name != _name)// 加一处判断，如果值相同，就无需发送通知了
    {   
        // 我们需要在值修改前调用`will...`方法
        [self willChangeValueForKey:@"name"];
        _name = name;
        // 我们还需要在修改后调用`did...`方法，显式调用观察者的方法
        [self didChangeValueForKey:@"name"];
    }
}
@end

```

手动发送通知一对一的操作方法如上，如果是一对多的案例，则可以使用如下方法

```objc
- (void)willChange:(NSKeyValueChange)change valuesAtIndexes:(NSIndexSet *)indexes forKey:(NSString *)key
- (void)didChange:(NSKeyValueChange)change valuesAtIndexes:(NSIndexSet *)indexes forKey:(NSString *)key
  
- (void)willChangeValueForKey:(NSString *)key withSetMutation:(NSKeyValueSetMutationKind)mutationKind usingObjects:(NSSet *)objects
- (void)didChangeValueForKey:(NSString *)key withSetMutation:(NSKeyValueSetMutationKind)mutationKind usingObjects:(NSSet *)objects
```

### 5.注册依赖键

实际开发过程中可能会遇到这种场景，某个变量的值取决于其他的值。

我们还是看一个例子吧：

```objc
// 声明一个Person类，有三个属性
#import <Foundation/Foundation.h>

@interface Person : NSObject

@property (nonatomic,copy) NSString *fullName;
@property (nonatomic,copy) NSString *firstName;
@property (nonatomic,copy) NSString *lastName;

@end

// 其中 fullName 取决于 firstName 和 lastName的值.
// 同时如果 firstName 和 lastName发生改变的话,fullName也会受到影响。

#import "Person.h"

@implementation Person

// 注册 fullName依赖于 firstName 和 lastName
+ (NSSet<NSString *> *)keyPathsForValuesAffectingFullName {

    return [NSSet setWithObjects:@"firstName",@"lastName",nil];
}

- (NSString *)fullName {

    NSString *tempName = _fullName;
    
    if (_firstName || _lastName)
    {
        tempName = [NSString stringWithFormat:@"%@-%@",_firstName,_lastName];
    }

    return tempName;
}
```


回到Controller：

```objc
- (void)viewDidLoad {
    
    [super viewDidLoad];
    
    Person *p = [Person new];
    self.p = p;
    
    [self.p  addObserver:self
        forKeyPath:NSStringFromSelector(@selector(name))
           options:NSKeyValueObservingOptionOld | NSKeyValueObservingOptionNew
           context:ContextMark];
    
    self.p.fullName = @"lilei";
    NSLog(@"fullName = %@",self.p.fullName);
    
    self.p.firstName = @"lala";
    NSLog(@"fullName = %@",self.p.fullName);

    self.p.lastName = @"papa";
    NSLog(@"fullName = %@",self.p.fullName);
}

// 打印结果如下
fullName = lilei
fullName = lala-(null)
fullName = lala-papa
```

### 6.KVO使用中的"坑"

> 最近我在看这方面资料的时候，发现大家都以 `tableView`和`ContentOffset`作为例子。咱们就用这个最常见的控件来说明一下吧。

##### 1.keyPath为字符串

众所周知，KVO里面的`KeyPath`是`NSString`类型，结合`Obj-C`动态语言的特性，在编译时是不做检查的，只有运行到执行的时候，才会动态的去`方法列表`、`实例变量列表`中去查找，所以一旦我们写错了`KeyPath`，不运行的时候很难发现。

基于这个问题，我们用以下的方法规避

```objc
// 这样就不会写错了
NSStringFromSelector(@selector(contentSize))
```


##### 2.多层继承、共用同一个回调方法

假如父类的控制器监听了`tableView`的`ContentOffset`属性，同时该控制器还监听了其他控件的一些属性，但是同一个对象或者控制器作为多个对象属性的观察者，实际上最后调用的都是同一个回调方法`- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary<NSKeyValueChangeKey,id> *)change  context:(void *)context { }`，这样写极其容易混淆，所以我们为了解决这个问题，把代码写成如下的样子

```objc
- (void)observeValueForKeyPath:(NSString *)keyPath 
                      ofObject:(id)object
                        change:(NSDictionary *)change 
                       context:(void *)context
{
    if (object == _tableView && [keyPath isEqualToString:@"contentOffset"]) 
    {
        [self doSomethingWhenContentOffsetChanges];
 　　} 
}
```

但是光这样写是不全面的，因为当前的这个类很可能有父类，并且它的父类可能绑定了一些其他的KVO,上面的代码只有一个条件判断，一旦不成立，此次KVO的触发操作也就断了。而当前类无法捕捉的这个KVO事件很可能就在它的父类里，或者是父类的父类，上述操作，将这一链条截断，所以正确的方法应该如下：

```objc
- (void)observeValueForKeyPath:(NSString *)keyPath 
                      ofObject:(id)object
                        change:(NSDictionary *)change 
                       context:(void *)context
{
    if (object == _tableView && [keyPath isEqualToString:@"contentOffset"]) 
    {
        [self doSomethingWhenContentOffsetChanges];
 　　} 
 　　else
 　　{
 　　    [super observeValueForKeyPath:keyPath 
 　　                         ofObject:object 
 　　                           change:change 
 　　                          context:context];
 　　}
}

```
这样做这一链条就完整的保留了。

##### 3.观察者的注销

上面的方法做完之后还是有隐患的。我们知道KVO不用的时候是需要注销的。我们知道当你对同一个KVO注销两次的时候，系统默认是抛出异常的。

你可能会好奇，什么时候我会对同一个`Observer`注销多次呢？

这个时候我们可以想一下我们注销`Observer`的时机，是不是多在`Dealloc`方法中？

在`Obj-C`中，有很多系统的方法被重写时需要调用`super xxxxxxx`等方法，这是`Obj-C`的继承关系决定的。

例如：

```objc
// 在重写init方法时，我们要调用一下父类的init方法
- (instancetype)init {

    [super init];
}

// 布局子控件时，要调用一下父类的layoutSubviews方法
- (void)layoutSubviews {

    [super layoutSubviews];
}
```

还有些方法，不需要调用父类的方法，自动就会帮你调用，就如我们所说的`Dealloc`。其实只有在`ARC`模式下才不需要调用父类，`MRC`下的`Dealloc`还是要手动调用`super dealloc`的。


所以我们在注销观察者的时候就这么写

```objc
- (void)dealloc {

    [_tableView removeObserver:self forKeyPath:@"contentOffset"];
}
```

假设我们有三个类 `ClassA（父类）`，`ClassB（子类）`，`ClassC（孙子类）`。这三个类都作为观察者，观察`tableView`的`contentOffset`属性。


如果我们在`ClassC（孙子类）`的`Dealloc`方法中释放观察者

```objc
- (void)dealloc {

    [_tableView removeObserver:self forKeyPath:@"contentOffset"];
}
```
当`ClassC（孙子类）`的`Dealloc`执行完毕后，就会自动去`ClassB（子类）`的`Dealloc`方法中，释放观察者

```objc
- (void)dealloc {

    [_tableView removeObserver:self forKeyPath:@"contentOffset"];
}
```
这个时候就出现崩溃了，因为我们在前面提到过这样会导致相同的removeObserver被执行两次，于是导致crash。


##### 4.正确写法
针对这种类型的Crash，我们就要谈一下在注册`Observer`似的一个关键的参数`Context`，之前我是不知道这个`Context`是做啥用的，对于`KVO`的使用只是流于表面，所以对于这个神秘的`Context`的作用一直没有深究，现在我们将使用`Context`来为每一个`Observer`做区分，避免多次调用相同的`removeObserver`。


KVO的三个关键方法

```objc
//  注册观察者
- (void)addObserver:(NSObject *)observer forKeyPath:(NSString *)keyPath options:(NSKeyValueObservingOptions)options context:(nullable void *)context;

// 观察者响应方法
- (void)observeValueForKeyPath:(nullable NSString *)keyPath ofObject:(nullable id)object change:(nullable NSDictionary<NSKeyValueChangeKey, id> *)change context:(nullable void *)context;

// 移除观察者（有两个方法）
- (void)removeObserver:(NSObject *)observer forKeyPath:(NSString *)keyPath context:(nullable void *)context NS_AVAILABLE(10_7, 5_0);

- (void)removeObserver:(NSObject *)observer forKeyPath:(NSString *)keyPath;
```

相比细心的同学已经看出来了，我们在**注册**、**响应**、**移除**的三个步骤里，都可以找到`Context`这个关键字。所以为了保持**注册**、**响应**、**移除**的一致性，正确的写法应该如下：


```objc
// 首先我们应在使用KVO的类中，创建一个独一无二的Context，用来和其他类进行区分
static Void *ContextMark = &ContextMark;

// 接下来注册的时候用
 [_tableView addObserver:self
              forKeyPath:NSStringFromSelector(@selector(contentSize))
                 options:(NSKeyValueObservingOptionNew | NSKeyValueObservingOptionOld)
                 context:ContextMark];
                 
// 响应的时候用
- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary<NSKeyValueChangeKey,id> *)change context:(void *)context {
    
    if (context == ContextMark)
    {
        // do someThing
    }
    else
    {
        [super observeValueForKeyPath:keyPath ofObject:object change:change context:context];
    }
}

// 注销的时候用
- (void)dealloc {
    [_tableView removeObserver:self 
                    forKeyPath:NSStringFromSelector(@selector(contentSize)) 
                       context:ContextMark];
}
```
如果还不放心，也可以使用`@try @catch`去捕获异常


### 7.总结、

![](http://imgsrc.baidu.com/forum/w=580/sign=bdaac71373094b36db921be593cd7c00/5635b9096b63f624e76700cf8244ebf81b4ca3ea.jpg)

`KVO`这套 API 真麻烦~~~~~  


