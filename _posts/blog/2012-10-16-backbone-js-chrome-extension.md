---
layout: post
title: 使用Backbone.js开发Chrome便签插件
description: 没有合适的插件，就只好自己动手了，同时也用Backbone.js来练练手。
category: blog
---

##Backbone.js简介
>Backbone.js gives structure to web applications by providing models with key-value binding and custom events, collections with a rich API of enumerable functions, views with declarative event handling, and connects it all to your existing API over a RESTful JSON interface.

Backbone算轻量级的MVC框架，他的优雅之处在于，他为复杂的代码约定了一种优美的组织方式。使用Backbone可以脱离处理复杂DOM的苦海。你的数据就是`Models`，`Collections`是`Models`的集合，`Models`的每一个变化都会触发`change`事件，`Views`用来响应数据的变化。就我的使用效果来说，对于相同功能的应用，代码量减少至少30%以上，更别说在可维护性方面带来的提高了。

相对于基本的HTML页面，Backbone.js的更适用于单页复杂应用(Single Page Application)。什么是单页复杂应用，比如Gmail、Google Reader、阿尔法城等，当然包括我将要讨论的便签插件。

##Notes页面准备

为了一些简单的自定义功能，还有这样的设置页：

总体来看，页面元素很简单，独立的三个模块：便签模板、关闭浮层、设置浮层。

为了不让过多的代码占据篇幅，简写如下，后面会给出源码地址：

样式我这样不懂审美的土鳖来说，是最困难的，只能根据直觉慢慢调整，还好CSS3方便了很多，不然最终肯定都是无聊的线框。不用兼容浏览器，那些漂亮的样式可以敞开了写，不过从设计角度来说，过于绚丽会导致操作效率降低，令用户产生厌烦，恰到好处是最好的，这方面我只到有直觉的水平，并无有价值的理论经验可分享。最终效果自己体会就好，关注如何实现，可以到源码中一看究竟。

##Backbone.js的Model
Model就是要操作对象的数据结构，存储需要用到的数据，基于这些数据，会有大量的交互、验证等等操作。

###Model声明
根据要做的便签的需要，数据结构定义为如下：
            
可以看到，我们定义了Note的Model的默认值，还有initialize和remove方法，当new一个Note对象时候，initialize方法会执行，默认的值也会赋给new的对象。

###Model值的set方法
也可以在new的时候修改覆盖默认值：

###Model值的get方法

###监听Model的change事件

要监听change事件，可以在initialize方法中做，也可以在实例化之后做：


###与服务端的交互
在这个应用中，无需与服务端交互，用了那个localStorage的补充插件之后，同样调用save()和destory()方法就好，该插件会自动完成相应的工作，真正与服务端的交互也很简单：

其中urlRoot用来定义服务端的RESTful的API接口。

Model还支持validate方法，可以对数据进行校验，校验不通过，则不能执行后续操作，在声明Model时候，增加validate方法就好：

##Backbone.js的Collection
Collection的概念很好理解，他就是Model的一个简单集合，举几个例子：

- Model是歌，Collection是专辑
- Model是动物，Collection是动物园
- Model是一个便签，Collection是所有便签（囧）

创建一个Collection，Model还是之前的便签的Model：


需要注意的是，我们的便签插件不需要与服务端交互，但是需要本地存储，所以使用localStorage。

##Backbone.js的View
好了，重头来了，View的声明和其他的基本上一样，一些参数查文档就很容易明白：

在这个应用中，分离了每个便签的View和整个app的View，这样做的好处是逻辑、代码更清晰。

###el属性
`this.el`是这个View的DOM引用，使用它可以方便的做很多操作DOM的事情。注意到在这个View的声明里面，定义了template函数，不适用Underscore自带的template的函数的原因是Chrome Manifest V2的版本里面不允许出现`new Function`，导致很多模板库不能使用，只好自己重写一个简单的。`template`在这个场景还是能非常方便的解决一些问题的。

使用`this.el`我们如何给View填充数据呢，很简单：

在`initialize`方法中，可以初始化这些事情，需要更多的初始化工作，继续写写下去就好。

###事件的监听
你肯定注意到了声明View代码中的下面这些：

这些用来给View绑定事件，就和平常使用jQuery一样的用法，相信你一看就明白。

###appView
`appView`并不是Backbone的内容，而是在这个应用中，我们用来粘合所有元素的一个容器，为了将整个流程串联起来，有一个总体的概念，我会详细解释这部分的代码：
