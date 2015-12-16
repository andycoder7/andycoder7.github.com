title: "UITableView中cell的显示混乱问题"
date: 2015-12-16 16:19:04
tags: [objective-c, ios]
categories: ios
---

## 0x00 问题描述

在上一篇博客中，简单的展示了不同的style的方式，当时coding的时候并没有注意到cell的reuse问题，在模拟器运行的时候也很正常。

但是，在后期进一步的coding的时候，偶然发现，当每个section的row大于3，更准确的说是当tableView中row的数量多到一屏显示不下时，就会出现显示错乱的bug，如下图：

![bug](/images/uitableviewcellbug/bug.png)

> 在自定义cellView的section中，有的row并没有按照代码中约定的添加自定义的view，而是显示了其他的样式

<!-- more -->

## 0x10 问题定位

通过对代码的debug，发现在加载自定义view的时候，cell并不是nil，所以并没有进入 `if(cell == nil)` 的判断，当然不会去创建自定义的view了，cell从reuse队列中获取到了对象。

由于是初学oc，所以很多代码写的自己都一知半解，例如说。。。***tableView中cell的reuse机制！***

### 0x11 reuse机制

由于通常使用TableView的时候，我们可能要准备显示上百个cell，或者说有上百row要显示，但是，同一时间，在屏幕上只能显示十个左右，如果每次在上下拉动的时候，都动态去创建对应的row的内容，就太浪费内存了。因此引入了reuse机制。

在获取对应的cell的时候，先去看看对应Identifier的reuse队列中是否存在对应的内容(这里的reuse队列并不是只放一个cell的实例，详细的后文再说)，如果有，就直接拿出来重用了，如果没有，就返回nil，然后进入 `if (cell == nil)` 判断逻辑，然后创建之，当该cell移动出屏幕的时候放入对应identifier的队列。

这里的Identifier是一串字符串，通常与indexpath的section和row没有关系，一般由类似 `static NSString *cellIdentifier = @"cellIdentifier";` 的代码定义

### 0x12 reuse队列

reuse队列的长度并不是固定的，一般是根据header/footer/row的高度确定的当前一屏能显示的cell的数量+1左右。

通过identifier去区分每个reuse队列，当当前tableView的cell的reuse Identifier都是同一个的时候，假设一屏能同时显示10个cell，这10个cell都是通过 `cell == nil`判断后创建出来的。

当下拉屏幕查看第11个时，第0个cell就被置入对应的identifier的reuse队列，然后在取第11个的时候，发现reuse队列的第0个是存在的，就会去把其拿出来重用，***所以，第18个cell会重用第7个cell，而不是第0个。***

## 0x20 问题解决

既然发现是reuse的问题，那么解决问题也很简单，对不同类别的cell设定不同的identifier即可。由于我的实例中，cell的分类是有section区分的，所以我只要在定义identifier的时候加上index的section属性即可。

`NSString *cellIdentifier = [NSString stringWithFormat:@"cellIdentifier%ld",(long)indexPath.section];`

BTW, 好像没有规定identifier一定要是static的变量。。虽然网上好多人都加上了。。T.T

> 心得体会： 不同内容的cell不要用同一个identifier，xcode才不会机智的在判定重用cell的时候帮你判定其对应的section和row呢！

## 0x30 tm还有问题

由于我自定义的内容是UISwitch，要是UIButton之类。。我可能就不会发现这个问题吧。。

当我把UISwitch打开了之后，把那个cell移出屏幕再移动回来发现。。UISwitch又变回关闭了。。你逗我。。。

### 0x31 还是问题定位

通过NSLog。。=_=|| 发现自定义的cell没有有重用，所以每次刷出来的时候，都是新的cell，当然UISwitch都会是关闭的啦。。

我一开始以为是row的数量太少，没有达到reuse的长度，所以把row的数量设置到了50个，然并卵。。测试发现，reuse队列并不一定需要充满，只要对应位置的cell在reuse队列中存在即可。

例如，identifier为default的reuse只有第0~7的cell，当前设定下，一屏可以放下10个cell，所以队列的长度为11左右。当第0个在移出屏幕时被加入reuse队列，当再移回第0个的时候，会去reuse查找第0个cell是否存在可以reuse的实例（而不用等到cell充满reuse的队列），如果有，则返回该实例。

然后注意到自己在实现自定义cell时的代码：

```
cell = [[UITableViewCell alloc]initWithFrame:CGRectMake(0, 0, self.view.frame.size.width, 35)];

//其他的cell的实例化代码：
cell = [[UITableViewCell alloc]initWithStyle:UITableViewCellStyleValue2 reuseIdentifier:cellIdentifier];
```

发现了什么？ 我在实现自定义cell的时候，没有加上reuseIdentifier。。那当然不会被纳入reuse体系啦。。。

### 0x32 解决问题

知道了问题出在哪，就好办了，那么。。解决方法也很简单，用defaultstyle实例化cell即可，反正加载的是自定义的view，style是什么并没有影响。

## 0x40 fuck 还有问题

当我把row置为50，title的内容是`[NSString stringWithFormat:@"标题%ld_%ld",(long)indexPath.section, (long)indexPath.row];`的时候，呵呵哒。

![bug2](/images/uitableviewcellbug/bug2.png)

仔细想一下。。还是重用的问题。由于重用了cell，所以在15之后不是16，而是之前的0

> 喵的竟然是1不是0.。只能说明上文的猜想是错的。。T.T 并不是严格按照顺序去reuse队列里重用cell的

### 0x41 解决办法么。。

只能在identifier上再加上row了咯。。=_=||

```
NSString *cellIdentifier = [NSString stringWithFormat:@"cellIdentifier%ld_%ld",(long)indexPath.section, (long)indexPath.row];
```

## 0x50 总结

不同的cell，千万不要给一样的identifier。。。

> ***BTW，本文中对reuse机制的说明都是个人猜测，并不一定是真的。。仅供参考！！***

测试代码参见：[https://github.com/andycoder7/IOStest/tree/UITableViewCellBug/testUITableView](https://github.com/andycoder7/IOStest/tree/UITableViewCellBug/testUITableView)
