---
title: 'MVC , MVP ,MVVM的区别和联系'
date: 2016-05-30 11:47:04
categories:
  - 前端小记
  - 大犀牛JavaScript
tags:

---

<Excerpt in index | 首页摘要>
+ MVC , MVP , MVVM如何过渡

<!-- more -->
<The rest of contents | 余下全文>

<b>应好友之邀写一篇这三者的关系的文章，以下也纯属个人见解，不足之处请大家指出。</b>

## MVC,MVP,MVVM的区别和联系

> 先说一下三者的`共同点`

	也就是Model和ViewModel就是数据模型，同时，提供外部对应用程序数据的操作的接口，也可能在数据变化时发出变更通知。Model不依赖于View的实现，只要外部程序调用Model的接口就能够实现对数据的增删改查。
	View就是UI层，提供对最终用户的交互操作功能，包括UI展现代码及一些相关的界面逻辑代码。

> 三者的差异在于如何`粘合`View和Model，实现用户的交互操作以及变更通知
MVC,MVP,MVVM图示：

<div style="text-align: center">
	![MVC,MVP,MVVM图示](https://app.yinxiang.com/shard/s38/res/4718e3ce-e7a7-45f2-97af-f5b7fde91404.png)
</div>

## MVC和MVP的关系

> MVC 进化为 MVP（Model-View-Presenter）模式与WPF结合的应用方式时发展演变过来的一种新型架构框架

	我们都知道MVP是从经典的模式MVC演变而来，它们的基本思想有相通的地方：Controller/Presenter负责逻辑的处理，Model提供数据，View负责显示。作为一种新的模式，MVP与MVC有着一个重大的区别：在MVP中View并不直接使用Model，它们之间的通信是通过 Presenter (MVC中的Controller)来进行的，所有的交互都发生在Presenter内部，而在MVC中View会以观察者的身份监听Model的变化，直接从Model中读取数据而不是通过 Controller。


## MVVM和MVP的关系

	而 MVVM 模式将 Presenter 改名为 ViewModel，基本上与 MVP 模式完全一致。 唯一的区别是，它采用双向绑定（data-binding）：（View的Model就是包含View的一些数据属性和操作的这么一个东东）这种模式的关键技术就是数据绑定（data binding），View的变化会直接影响ViewModel，ViewModel的变化或者内容也会直接体现在View上。这种模式实际上是框架替应用开发者做了一些工作，开发者只需要较少的代码就能实现比较复杂的交互。


## MVVM的优点

1. 低耦合：View可以独立于Model变化和修改，同一个ViewModel可以被多个View复用；并且可以做到View和Model的变化互不影响；
1. 可重用性：可以把一些视图的逻辑放在ViewModel，让多个View复用；
1. 独立开发：开发人员可以专注与业务逻辑和数据的开发（ViewModel），界面设计人员可以专注于UI(View)的设计；
1. 可测试性：清晰的View分层，使得针对表现层业务逻辑的测试更容易，更简单。

> 在angular中MVVM模式主要分为`四部分`：

+ View：它专注于界面的显示和渲染，在angular中则是包含一堆声明式`Directive`的视图模板。
+ ViewModel：它是View和Model的粘合体，负责View和Model的交互和协作，它负责给View提供显示的数据，以及提供了View中Command事件操作Model的途径；在angular中`$scope`对象充当了这个ViewModel的角色；
+ Model：它是与应用程序的业务逻辑相关的数据的封装载体，它是业务领域的对象，Model并不关心会被如何显示或操作，所以模型也不会包含任何界面显示相关的逻辑。在web页面中，大部分Model都是来自Ajax的服务端返回数据或者是全局的配置对象；而angular中的`service`则是封装和处理这些与Model相关的业务逻辑的场所，这类的业务服务是可以被多个Controller或者其他service复用的领域服务。
+ Controller：这并不是MVVM模式的核心元素，但它负责ViewModel对象的初始化，它将组合一个或者多个service来获取业务领域Model放在ViewModel对象上，使得应用界面在启动加载的时候达到一种可用的状态。

﻿