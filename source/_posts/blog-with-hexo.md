title: 用 HEXO 搭建个人博客
date: 2015-05-06 16:47:04
tags: [HEXO, GitHub, Blog]
categories: 琐碎
---

## 0x00 背景

老早之前选择 octopress 在 github 上搭建了自己的博客, 虽然说是 for hackers 的博客系统, 但是略觉得有点烦, 正好看到 HEXO. 各方面都满足了我的需求, 而且还很简单, 于是我就很没有骨气的变节了...

### 0x01 HEXO

关于 HEXO 的介绍和资料可以戳[这里][1], 总的来说优点有:

 - 操作简单, 使用方便
 - 主题数量和质量都 ok
 - 文档资料详细
 - gitpage + markdown

能提供的功能大致有:

<!-- more -->

 - 首页
 - 根据时间归档
 - 标签(云)
 - 分类
 - 订阅
 - 各种链接
 - 各种统计
 - 多说/ disqus 评论
 - swiftype 站内搜索等

基本上需要的功能都有了.

### 0x02 gitpage

gitpage 是 github 提供的 pages 功能, 可以用于展示项目和个人博客, 使用 Jekyll 静态模板系统, 所以不需要数据库, 大致上做的事情就是把你项目中的代码通过 jekyll 解析成一个个静态的 html 页面. 

要建立自己的 gitpage 也很简单, 在你的 github 上新建一个项目, 项目名为: YOUR_GITHUB_USERNAME.github.io, 然后就没有然后了...

当你访问 http://YOUR_GITHUB_UERNAME.github.io 时, 网页请求会发送到 github, 然后 github 会找到你的那个项目, 然后把里面的代码根据 jekyll 解析, 然后返回对应的 html 文件.

详情戳[这里][2]

## 0x10 搭建

个人习惯不同, 我习惯在虚拟机里搭建开发环境, 这样就方便我随便捣腾了, 你也可以直接在本机搭. 需要安装依赖软件只有 [git][3] 和 [nodejs][4]. 然后只需要` $ npm install -g hexo-cli`就好了, 其中 `-g` 参数表示 global, 不然 hexo-cli 只会装在当前目录下的 node_modules文件夹中.


### 0x11 初始化

安装完成后, 我们就可以开始享受了 hexo 了. 具体步骤的话, 当然是要先把自己在 github 上的那个项目先 clone 下来, 然后 cd 进入该文件夹, 然后 `hexo init` 初始化一下, 然后再` npm install `就 ok 啦.

不过这里似乎有个坑, 我不知道是不是只有我一个人遇到了, 当我在执行 hexo init 的时候, 我看到了:

> /usr/bin/env: node: No such file or directory

原因似乎是由于 nodejs 在安装完成后, 在/usr/bin/下的可执行文件是 nodejs, 而不是 node 了, 所以 hexo 只能说, oops 找不到 node. 所以我们只需要建一个叫 node 的软连接指向 nodejs 即可:

`sudo ln -s /usr/bin/nodejs /usr/bin/node `

### 0x12 目录结构

然后, 我们得到了如下的目录结构:

```
.
├── _config.yml
├── package.json
├── scaffolds
├── scripts
├── source
|   ├── _drafts
|   └── _posts
└── themes
```

#### _config.yml 

存放各种配置信息

#### package.json

各种组件的版本信息

#### scaffolds

我们用 hexo new 命令生成一篇博客的时候, 文件中的初始内容来自于该文件夹

#### scripts

用于扩展 hexo, 所有放在这里的javascript文件会被自动执行

#### source

_drafts 存放草稿, _posts 中则放正式的博客文章

#### themes

存放 hexo 的主题

### 0x13 配置

在 _config.yml 文件中要改的不多, 基本上: title/subtitle/description/author/language/timezone/url/theme/deploy

其中 language 如果是中文的话那就设成: zh-CN, 或者看 theme/[你的主题]/language/下有那些语言, 选一个填上好了.

timezone 的话戳[这里][5], 如果是中国的话, 那就填 Asia/Shanghai 好了.

至于 deploy, 因为我们用 github, 所以这里这么填好了:

```
deploy:
  type: git
  repository: YOUR_GITHUB_REPOSITORY
  branch: master
```

### 0x14 测试

hexo 帮我们把很多命令封装了起来, 所以基本上只需要: 

#### hexo new [layout] <title> 

创建新文章,  一般来说如果是正常写文章的话, layout 不需要填写, 直接 `hexo new <title>` 即可, 将会生成 source/_posts/<title>.md, 然后用 markdown 格式书写博客就好了. 可以把命令缩写为 ` hexo n <title>`

#### hexo generate

生成 jekyll 可以解析的静态页面, 生成的页面全部放在 public 文件夹下.

可以加两个参数, 一个是`-d`, 表示在生成之后 deploy 部署到 github 上, 另一个是 `-w` 表示watch, 查看有哪些文件修改了. 命令可以缩写为 ` hexo g `

#### hexo server

本地启动 web 服务, 用来预览博客生成部署之后的效果. 输入该命令后, 在浏览器中输入 http://localhost:4000 就可以访问了. 可以缩写成` hexo s `

#### hexo deploy

部署, 把 public 文件夹下的文件推送到项目仓库的 master 分支上(具体看配置文件中的设置). 缩写是 ` hexo d `

#### 其他命令

主要用到的就上述那些命令, 其他命令戳[这里][6]

### 0x15 坑

在 deploy 的时候, 可能会碰到一个坑:

> ERROR Deployer not found: git

原因是在 hexo3.0之后, 不仅 github 的 deploy 的 type 从 github 改为了 git, 其中 deploy 的功能也被单独做成一个模块, 需要另外安装, 所以我们需要执行以下

`npm install hexo-deployer-git  --save`

其中` --save ` 参数是用于把模块的版本号添加到 package.json 文件中的依赖里( dependences ), 否则在安装完之后需要手动添加.

### 0x16 我各个软件和模块的版本

 - nodejs 0.10.25
 - git 1.9.1
 - hexo 3.0.1
 - os ubuntu14.04 x64 linux 3.13.0-24-generic
 - http_parser 1.0
 - v8: 3.14.5.9
 - ares: 1.10.0
 - uv: 0.10.23
 - zlib: 1.2.8
 - modules: 11
 - openssl: 1.0.1f
 
## 0x20 主题

hexo的主题有很多, 戳[这里][7], 还有[这里][8], 这里列出了很多 nice 的主题, 我自己也试了很多, 目前来说感觉比较好看的有四个. 如果你看中了那个主题, 只需要在 theme 目录下 `git clone 对应主题的仓库`, 然后把文件夹随便改个名字, 然后在外面的 `_config.yml` 文件中的 `theme:` 后面加上那个文件夹的名字即可.

### 0x21 phase

github地址: https://github.com/hexojs/hexo-theme-phase 要预览的话戳[这里][9]. 不过由于会去问 Google 要 jquery 文件, 所以不会科学上网的小伙伴...呵呵哒

![](/images/blog-with-hexo/phase.png)

这里放的是静图, 但实际上背景上的气泡和线条都会移动的, 所以效果很 nice, 不过成也动画, 败也动画, 当打开这个页面的时候, 我的 CPU 直接100+%了...

### 0x22 moretwo

github 地址: https://github.com/moretwo/hexo-theme 要预览的话戳[这里][10]. 主题应该是 casper, 但是我是从 moretwo 那里看到的, 所以我在这里就称之为 moretwo 了.

![](/images/blog-with-hexo/moretwo0.gif)
![](/images/blog-with-hexo/moretwo1.png)
![](/images/blog-with-hexo/moretwo2.png)

### 0x23 huno

github地址: https://github.com/someus/huno 这是源自 ghost 上的 nuo 主题, 搬到了 hexo, 所以就起名为 huno 了, 

![](/images/blog-with-hexo/huno1.png)
![](/images/blog-with-hexo/huno2.png)


### 0x24 mist(NexT)

github地址: https://github.com/iissnan/hexo-theme-next 预览的话我的博客就是. 不过你可能会觉得装完后还是有点区别. 虽然我们用的都是 NexT, 但是我在主题中的 _config.yml 文件中加了一行:

```
# Mist
#scheme: Mist
```

这在 README 文件中有说明哦.

## 0x30 其他

到这里基本上就都说的差不多了, 不过零碎的情况文中没有提到

### 0x31 About 页面

在换各种主题的时候, 有时候会碰到主题里面明明有 Archive 或者 Tags 或者 Categories 或者 About 等页面, 但是你会很囧的发现没法打开, 提示说:

> Cannot GET /about/

原因是你没有生成 about 页面, 只要` hexo new page about` 就可以了. 这个命令会在 source/ 目录下新建一个 about 文件夹, 然后建立一个 index.md 文件, 然后当` hexo g `生成的时候, 就会在 public/about/ 目录下生成一个 index.html 文件了, 然后就可以访问 about 页面了. 至于 about 页面的内容, 用 markdown 语法写在 index.md 中即可.

### 0x32 引用本地jquery

遇到一些主题使用了 google 的 jquery 的话, 没有关系, 用 `grep -R "google" .` 找到所有用了Google jquery 的地方, 然后把链接换成另外的 CDN 的地址好了, 或者把 jquery 下载到本地, 然后放到 theme/[ 你的主题]/ source/js/ 之类的地方, 然后把链接地址改成 `<%- config.root %>js/jquery.min.js`即可.

### 0x33 源码保存

通过` hexo d `, 我们会把 public 文件夹下的内容上传到 github 仓库的 master 分支, 那么问题来了, public 下的文件是保存了, 那其他文件怎么办嘞, 我们写博客, 换主题都是在其他文件下弄的呀. 所以我的个人习惯是新建一个 source 分支, 然后把代码都存在 source 分支上, 每次修改完, 或者写完新的博客, 就先 commit push 到远程的 source 分支, 然后在 hexo g -d 把页面生成推送到master 分支

## 0x40 结语

写的好累... Hope this arcitle will help you. That's all, thanks you.

[1]: http://hexo.io/docs/
[2]: https://pages.github.com/
[3]: http://git-scm.com/
[4]: https://nodejs.org/
[5]: https://en.wikipedia.org/wiki/List_of_tz_database_time_zones
[6]: http://hexo.io/docs/commands.html
[7]: http://hexo.io/themes/
[8]: http://www.zhihu.com/question/24422335/answer/46357100
[9]: http://hexo.io/hexo-theme-phase/
[10]: http://moretwo.github.io/about/2014-09-11-mabao/
