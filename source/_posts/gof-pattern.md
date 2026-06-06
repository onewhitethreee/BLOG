---
uuid: 59bc8a50-61fb-11f1-9165-d7f4c103be04
title: gof_pattern
date: 2026-06-07 00:59:19
tags:
---

# Singleton 

例子：数据库连接管理器，全局只允许一个实例。

![](https://img.164314.xyz/2026/06/c7bb4874bbdd5d9a77b2bf4b09351887.png)

# Factory Method

例子：跨平台按钮，WindowsDialog 和 MacDialog 各自决定创建哪种 Button。


![](https://img.164314.xyz/2026/06/a7bd3f93f56b7ea1ddee841e047c21a7.png)

要点：

Dialog 是抽象父类，定义了 createButton() 抽象工厂方法
WindowsDialog 和 MacDialog 各自覆写，决定创建哪种 Button
父类 render() 调用 createButton()，但不知道具体类型——多态在这里发挥作用

# Abstract Factory

例子：跨平台UI工厂，确保 Button、Checkbox、Dialog 永远来自同一个平台族。

![](https://img.164314.xyz/2026/06/c4b995bd570d0b5ba272bf9298a28c95.png)

要点：

GUIFactory 接口定义了整族产品的创建方法
WindowsFactory 只创建 Windows 系列，MacFactory 只创建 Mac 系列，永远不会混用
客户端只依赖 GUIFactory 接口，不知道具体是哪个工厂

与 Factory Method 的区别一眼就能看出：Factory Method 管单个产品，Abstract Factory 管一整族。

# Builder

例子：构建复杂的 HTTP请求 对象，参数多且可选，最后调用 build() 生成成品。

![](https://img.164314.xyz/2026/06/fe2abcee1be663b9a57134fa64afd770.png)

HttpRequestBuilder 持有所有构建参数，每个 setter 返回 this 实现链式调用
build() 最后才创建 HttpRequest 成品，可以在这里做参数验证
Client 只和 Builder 打交道，不直接 new HttpRequest

与工厂模式的区别：工厂关注"创建哪种对象"，Builder 关注"一步步怎么造这个对象"。

# Decorator

例子：咖啡订购系统，SimpleCoffee 可以动态叠加 Milk、Sugar、Vanilla 配料。

![](https://img.164314.xyz/2026/06/09e0b007b269fbfc8cea76f2c985835e.png)

要点：

CoffeeDecorator 同时实现 Coffee 接口，又持有一个 Coffee 对象——这是Decorator的关键结构
Milk、Sugar、Vanilla 继承 CoffeeDecorator，各自在 cost() 里调用 coffee.cost() + 自己的价格
运行时可以任意套娃：new Sugar(new Milk(new SimpleCoffee()))

最关键的设计：装饰器和被装饰对象实现同一个接口，所以客户端感知不到差别。

# Adapter

例子：你的系统期望调用 pay()，但第三方支付库只有 payWithCard()，用Adapter做接口转换。

![](https://img.164314.xyz/2026/06/4416400261d68809e0cb6a0813790f15.png)

要点：

Client 只知道 PaymentService 接口，调用 pay()
PaymentAdapter 实现 PaymentService，内部持有 StripeLibrary
真正的转换发生在 pay() 内部：把 pay(amount) 翻译成 payWithCard(amount, "USD")
StripeLibrary 完全不需要修改

和Proxy的区别一眼可见：Proxy实现同一接口是为了控制访问，Adapter实现不同接口是为了翻译调用。

# Facade

例子：视频播放器，客户端只需调用 play()，Facade内部协调 VideoDecoder、AudioSync、BufferManager、DisplayRenderer 四个子系统。

![](https://img.164314.xyz/2026/06/bf722afd7a6df82cb2a6e9498f0b0b9c.png)

要点：

Client 只和 VideoPlayerFacade 打交道，调一个 play() 搞定一切
Facade内部持有四个子系统的引用，负责协调调用顺序
子系统类完全不变，客户端也感知不到它们的存在
虚线框表示子系统边界——客户端原则上不应该越过这条线

# Composite

文件系统，File 是叶子节点，Folder 是组合节点，两者实现同一个接口，客户端统一处理。

![](https://img.164314.xyz/2026/06/dd0554f38d2644006ea60c340c858250.png)

要点：

File 和 Folder 都实现 FileSystem 接口——客户端无需区分
Folder 持有 List<FileSystem>，可以包含 File 也可以包含另一个 Folder——这就是自引用
getSize() 在 Folder 里递归调用每个 child 的 getSize()，File 直接返回自己的大小——递归自然发生
虚线自引用箭头 ◇──▶ FileSystem 是Composite最标志性的UML特征

# Proxy

图片加载，ProxyImage 和 RealImage 实现同一接口，第一次调用才真正加载，之后缓存。

![](https://img.164314.xyz/2026/06/25a977be548ac7cdeb9e208a2949db85.png)


要点：

ProxyImage 和 RealImage 实现同一个 Image 接口——客户端完全感知不到差别
ProxyImage 内部持有 RealImage，但只有第一次 display() 时才真正创建它（懒加载）
控制逻辑（权限检查、缓存、懒加载）全部在 display() 里，对外透明

# Observer

例子：股票价格系统，StockModel 数据变化时自动通知 PriceChart、PriceLabel、AlertSystem 三个UI组件。

![](https://img.164314.xyz/2026/06/6b7d89dabbd6b32075f576150bf442bd.png)

要点：

StockModel 实现 Subject，内部维护一个 observers 列表
setPrice() 数据变化时自动调用 notifyObservers()，遍历列表调用每个 update()
PriceChart、PriceLabel、AlertSystem 各自实现 Observer，收到通知后自己决定怎么响应
StockModel 只知道 Observer 接口，完全不知道具体是哪些UI组件——解耦的核心

# Command 

例子：文本编辑器，CopyCommand、PasteCommand、DeleteCommand 封装操作，支持撤销/重做。

![](https://img.164314.xyz/2026/06/6011a940fd33e2019a6804914f38b4b6.png)

要点：

Command 接口统一定义 execute() 和 undo()，操作变成了对象
每个具体命令持有 Editor（接收者）和 backup（撤销用的状态备份）
Button（调用者）只知道 Command 接口，不知道具体是什么操作——完全解耦
CommandHistory 把执行过的命令压栈，undo() 时弹出调用 undo()

# Template Method

例子：饮料制作流程，BeverageTemplate 定义骨架，Tea 和 Coffee 只覆写不同的步骤。

![](https://img.164314.xyz/2026/06/6f277310c6f2803a0d36bdce00ade0ed.png)

要点：

prepare() 标记为 final——子类不能改变流程顺序，只能填空
boilWater() 和 pourInCup() 是固定步骤，在父类实现
brew() 是抽象方法——子类必须实现
addExtras() 是Hook——子类可选覆写，默认什么都不做
Tea 和 Coffee 只写自己不同的部分，重复代码全部留在父类

# Stete

例子：自动售货机，IdleState、HasCoinState、OutOfStockState 各自封装不同状态下的行为。

![](https://img.164314.xyz/2026/06/c733e416052d28c92c4b80ed62915b38.png)

要点：

VendingMachine 是Context，只持有当前 state: State，所有操作委托给它
每个具体状态类自己知道该怎么做，也自己触发状态转换（调用 m.setState(...)）
绿色箭头表示运行时的状态转换流向——这是State模式最标志性的特征
OutOfStockState 用coral色区分——它是异常状态，语义上与正常流程不同

# Strategy

例子：排序系统，Sorter 可以在运行时切换 BubbleSort、QuickSort、MergeSort 三种算法。

![](https://img.164314.xyz/2026/06/e3c24c23f82431b342c26c2735189e45.png)

要点：

Sorter 只持有 SortStrategy 接口引用，不知道具体是哪种算法
setStrategy() 让客户端在运行时随时切换算法——这是Strategy最核心的能力
三个具体策略完全独立，互不知晓，可以单独测试

