---
layout: post 
title: Clash of Clans 一直在线脚本
category: blog
---

##缘起

---

玩COC也有段时间了，现在也已经是一个八本的中流砥柱，但是八本其实是一个很尴尬的时候，随便升级一个就要200w金或者水，这就意味着每次被打都好疼的。特别是在我190w的时候，每次看到被打都特别烦躁。身边有一个玩过Android的小伙伴也碰见了类似的情况，他是在七本出王的时候，为了那几点黑水。。。于是他写了一个coc一直在线的脚本，详情[见这](http://http://mikecoder.net/?post=89)，我当时没怎么在意，另外他的方法好像用到了一些工具，作为我这个没怎么接触过Android的人去套用他的方法就觉得有点得不偿失了。于是一直没管，直到现在。

介绍了背景，那么我们切入正题：如何COC不下线？玩过COC的小伙伴都知道这货动不动就try again，但是只要你一直在那里时不时点它一下，它就可以一直在线了。那么我们的问题就变成了怎么定时的点击它？作为一个基本没接触过Android开发的DS表示之前只听过有ADB这么一个Android DeBug的东东，然后baidu了一下，发现作为一个debug工具，它果然是可以触发点击事件的。而ADB这种东东又是可以硬盘版的，so nice有没有！

##我的解决方案

---

我的解决方案是在linux下的，win下的小伙伴么将就着参考一下吧，具体怎么在win下实现我就不知道了。

###ADB android SDK

首先，你要搞一个ADB（在我的github项目中有，[链接在这](https://github.com/andycoder7/teddy/tree/master/coc_online)），这样你才能在电脑上用terminal控制你的mobile phone。对了，你的系统最好时32bit的，如果是64bit的话有可能会提示你`adb: No such file or directory`,这时你就需要装一下32位兼容库了，ubuntu14.04 64bit上装32位兼容库可以参考我的这篇[博客](http://andycoder.me/fix-32bug-under-ubuntu1404/),我几次重装系统之后都是这么搞定32位的问题的。

其次，你的手机最好root了，不然ADB可能会拿不到授权。怎么root我就不说了。

现在，我们就可以开始实现我们的coc不下线脚本啦！

step1，在[这里](https://github.com/andycoder7/teddy/tree/master/coc_online)把我的coc_online和SDK下下来，或者你也可以把我整个teddy项目下下来，这里面还是有一些比较有意思的东东的。

step2，打开coc_online脚本，修改其中的SDK_PATH,改成你自己的目录，然后保存退出

step3，`sudo cp coc_online /usr/bin/coc_online`

step4, 连上手机，搞定授权，然后let's just `coc_online`

step5，如果要退出的话，`coc_online -e`，由于实际上是用的kill命令退出的，所以需要你输入密码

##最后

我的脚本和代码只是为了方便我自己，细节处理上没有下很多功夫，并且我表述的也不是很清晰，希望这篇博客可以有帮助到你！Thanks

*Andy(andy.at.working@gmail.com;andy.at.working@foxmail.com)*



