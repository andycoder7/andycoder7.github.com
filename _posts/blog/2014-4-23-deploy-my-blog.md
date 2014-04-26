---
layout: post
title: 使用Godaddy+DNSPod+Github-page+jekyll+Disqus搭建个人博客
category: blog
---

**请看完全文之后再决定是否按照我的流程搭建你自己的Blog,本文只是讲了我个人的搭建流程,不一定适合你**

###缘起

---

接触github也不少时间了，虽然一直都没敢在上面提交什么项目，不过后来发现你可以放心的提交，因为根本就没人会来看你。。T_T 最近，突然发现github的项目通过`Automatic page generator`可以生成自己的主页。然后网上了解了一下，发现完全可以使用github-page这个功能实现自己的个人blog。优缺点总结如下：

#####优点：

- 完全免费
- 用git的方式来更新blog（这也算优点么。。如果你习惯了git的话。。应该算吧=。=）
- 文章内容使用Markdown编写（个人觉得这几个特点对于一个coder的blog来说太合适不过了）
- 自带一坨精美模板

#####缺点：

- 由于伟大的方校长（我说的是真心话，客观的说，方校长还是很伟大的，虽然给大家带来了很多不便。。-_-||），Blog页面访问速度。。大家都懂的，完全看方校长心情了
- 貌似用github-page做个人博客的话不容易被搜索引擎搜到（这点我没有证实，只是听说，如果你在google或者baidu看到我的blog的话。。就当我什么都没说吧，哈哈）
- 域名比较有特点。。一般是YOUR_GITHUB_USER_NAME.github.io，如果你觉得不爽的话，那就需要和我一样再买一个域名了。。当然这也不能算是它的缺点了，它也不想的是吧～
- 使用Jekyll不能评论（不过这个我们有解决方案～不怕不怕）

废话不多说，介绍一下我的配置流程吧：Godaddy+DNSPod+Github-page+jekyll+Disqus，我们一个一个来讲

###Godday

---

[Godaddy](http://www.godaddy.com),这是一个买域名的地方，不过是国外的，我当时没有货比三家，直接按照我网上找的blog的流程去了这里，你可以去国内的，听说比较便宜，我的[andycoder.me](http://andycoder.me)买的时候是$9.99/yr。对了当初选择它还有一个原因就是因为它支持支付宝付款,对于一个没有信用卡的大学生来说,实在是太贴心了!至于购买的流程的话应该不需要我赘述了吧,有点网购经验的人都懂的.注册一个用户,搜索你要的域名,购买,然后支付宝付款,over~

###DNSPod

---

[DNSPod](http://www.dnspod.cn),这是一个国内的做域名解析的公司,选择他们的原因只有一个,因为free~ 哈哈.话说回来,其实是因为有传说Godaddy的域名解析服务器被墙了,所以为了保险起见,我们还是在国内找一个内应吧.流程的话老规矩,注册,登录,然后在我的域名中添加你刚购买的域名,然后点进去,然后点击添加记录,然后我的设置是:在主机记录那添@,记录类型位A,在记录值那里填github-page的IP地址,我填的是:192.30.252.153,最新的IP地址可以在[github帮助文档的DNS errors](https://help.github.com/articles/my-custom-domain-isn-t-working)中找到.当然,到这里只是意味着,如果你的域名解析到了DNSPod,它会帮你转到github,但是它为什么回到DNSPod呢?这就需要我们在Godaddy那也做一些设置了,具体的设置步骤个参见[DNSPod的帮助文档](https://support.dnspod.cn/Kb/showarticle/tsid/42/).

###Github-page

---

到了这步,你在浏览器里输入你的域名的时候,已经可以通过Godaddy -> DNSPod -> Github了,但是这个域名该解析成哪个IP呢?你的个人blog又在哪里呢?这时候我们就需要了解github-page的功能了.首先,在[github](http://www.github.com)登录你的github账户,然后新建一个仓库,仓库名为你的用户名.github.com,例如说,我github的用户名是andycoder7,于是我新建的仓库名就应该是andycoder7.github.com.如果你只是要一个个人博客的话,可以进入刚建的那个仓库,然后点击边上的Setting,然后点击下面的`Automatic page generator`,然后按照它的流程下来你就会有一个精美的blog啦~当然,这时候你blog的域名还是苦逼的username.github.io,怎么把它改成我们自己购买的那个域名呢?很简单,只要在仓库中添加一个CNAME文件就可以了,注意这个CNAME文件名一定要全大写,并且要在仓库的一级目录下,至于文件内容么,就是你的购买的那个域名,这样github就知道这个域名是对应你的这个项目了,然后一切就联通起来啦,从Godaddy -> DNSPod -> Github -> 你的项目.到目前为止,你的个人博客算是基本ok了~(Ps:至于git的使用方法和配置什么的,相信这个大家都懂的吧,我就不在这多说了)

###jekyll

---

这玩意其实我到现在在没怎么搞懂,它是github page的模板系统,具体大家可以自行google,百度之.那有同学就会问,你没搞懂怎么弄的自己的Blog,这里我要诚实的和大家说一下,我的这个Blog的代码其实是copy[Beiyuu](https://github.com/beiyuu/beiyuu.github.com)的,然后修改之,当然你也可以自行研究Jekyll的结构语法,自己开发一个.如果不想那么麻烦,又觉得我的,额,其实是BeiYuu的博客风格还不错的话,可以直接fork我们的项目,然后修改项目名字,改成你的username.githuh.com就行了,当然,你需要把我们的博客内容删除,并且把其中的一些链接和统计代码删除了...

###Disqus

---

它可以通过在网页中加入JS代码从而提供评论功能,当然也是需要你注册的...纯正的github page是没有评论功能的,jekyll也只是生成静态网页,于是如果你需要评论功能的话,我的解决方案是使用Disqus,其实相当简单,你可通过这个[链接](http://disqus.com/admin/create/)来把Disqus添加进你的网站.

###统计功能

---

我使用的[百度统计](tongji.baidu.com),当然,你也可以使用google分析工具,这都无所谓的.注册 -> 填写相关信息 -> 获取到js代码 -> 添加到`_layouts/default.html`中. So easy有没有~

###最后

感谢Beiyuu,我搭建个人blog的时候主要是参照他的[这篇文章](http://beiyuu.com/github-pages/)的,当然代码也是他的代码改的,说出来真是有点不好意思呢...T_T

**本文仅供参考!!!**

*Andy(andy.at.working@gmail.com) 2014-04-23*
