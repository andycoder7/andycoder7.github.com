title: Shell 提示符中添加 git branch 信息
date: 2015-05-11 11:27:57
tags: [Shell, Git]
categories: 琐碎
---

## 0x00 背景

github 墙虽然没有刷起来, 但是 git 在项目中用到的还是挺多的, 经常需要在各个分支之间切换, 万一搞错分支真是会呵呵的. 这时候就想, 如果在 shell 的提示符上能实时显示出所在分支, 那该有多好. 事实证明, 有这种想法的 coder 绝对不止我一个...

### 0x01 实现方法

就我现在的了解, 要实现这个需求主要有两种方法:

 - bash + 修改 .bashrc + 修改 PS1
 - zsh + oh-my-zsh

<!-- more -->

## 0x10 Bash

很多 linux 系统默认的 Shell 就是 Bash, 可以通过` cat /etc/shells `来查看本机装了哪些 shell, 然后通过` echo $SHELL `来查看自己目前使用的那个 Shell.

一般来说, 要在 Bash 的提示符中自定义信息的话, 只要修改 PS1 这个变量即可. 在 man page 里, PS1 的定义是:

> PS1    The value of this parameter is expanded (see PROMPTING below) and used as the primary prompt string.  The default value is "\s-\v\$" .

大意是: PS1 这个参数的值是可扩展的( 具体的扩展说明参见下文对 PROMPTING 的说明), 并且是提示符的主要字符串. 其默认值是: `"\s-\v\$"`. 

说到这里, 估计大部分童鞋都和我一样...甚么鬼...甚么是叫可扩展的? 怎么扩展? PROMTING 是甚么? 提示符的主要字符串还好理解, 就是提示符那串东西的主要组成部分么, 但是那个默认值是甚么意思?

### 0x11 expanded

所谓可扩展的, 我个人理解是指可以被再次解析的, 也就是说, 这个字符串里有一些特定字符代表着别的意思, 就好像 C 语言里面的 printf 函数一样, 虽然我们在参数里面填的是" num is %d\n ", 但是其中`%d`和`\n` 都会被再次解析, 转换成数字和换行符. 在这里也是一样的, `\s` 表示 shell 的 name, `\v` 表示 bash 的版本, `\$` 就是$, 所以按照默认值的话, 你看到的提示符应该是这样的:

> bash-4.3$

但是我们的 shell 提示符在装系统的时候就已经被配置过了, 所以你看到已经不是原始的提示符了.

在系统中, 除了`\s`, `\v`之外, 系统还提供了很多的特定字符, 在 man page 中有一一说明:

![](/images/add-git-branch-info/man-page.png)

其中, 常用的一下有:

> \d ：代表日期，格式为weekday month date，例如："Mon Aug 1"
> \H ：完整的主机名称。例如：我的机器名称为：fc4.linux，则这个名称就是fc4.linux
> \h ：仅取主机的第一个名字，如上例，则为fc4，.linux则被省略
> \t ：显示时间为24小时格式，如：HH：MM：SS
> \T ：显示时间为12小时格式
> \A ：显示时间为24小时格式：HH：MM
> \u ：当前用户的账号名称
> \v ：BASH的版本信息
> \w ：完整的工作目录名称。家目录会以 ~代替
> \W ：利用basename取得工作目录名称，所以只会列出最后一个目录
> \# ：下达的第几个命令
> \$ ：提示字符，如果是root时，提示符为：# ，普通用户则为：$

对照上面, 我们也不难看书一般系统默认的提示符格式为: ` \u@\h:\w\$ `, 效果则是: 

> andy@andy-VirtualBox:~$

### 0x12 添加 git branch 信息

首先我们来看一下添加了 git branch 信息之后的效果:

![](/images/add-git-branch-info/bash.png)

所以我们现在还欠缺的只是 git branch 的名字和颜色了. 那么 git branch 的信息如何获取呢? 这个只要查询当前目录下的 .git 分支信息即可. 网上有很多时间方式, 用的最多的应该是这种:


```
## Parses out the branch name from .git/HEAD:
find_git_branch () {
    local dir=. head
    until [ "$dir" -ef / ]; do
        if [ -f "$dir/.git/HEAD" ]; then
            head=$(< "$dir/.git/HEAD")
                if [[ $head = ref:\ refs/heads/* ]]; then
                    git_branch="(${head#*/*/})"
                elif [[ $head != '' ]]; then
                    git_branch="(detached)"
                else
                    git_branch="(unknow)"
                fi
            return
        fi
        dir="../$dir"
    done
    git_branch=''
}
```

然后把这个函数添加到 PROMPT_COMMAND 中, 保证 bash 在创建提示符之前先执行该函数:

```
PROMPT_COMMAND="find_git_branch; $PROMPT_COMMAND"
```

最后就是修改 PS1 变量, 把获取到的 git branch 信息添加到进去即可:

```
PS1="\u@\h:\w\$git_branch\$ "
```

BTW, 以上这些代码全都是添加到`~/.bashrc`文件的最后即可, 至于为什么添加到这个文件, 不在本文讨论范围内.

### 0x13 添加颜色

到上面一小节结束的时候, 我们发现现在已经可以获取到 git branch 的信息了, 哪怕当前目录没有 .git 文件夹, 脚本也会自动的上级目录查找. 但是问题来了, 现在的整行提示符都是一个颜色的, 好难看, 于是我们就在想怎么才能给 git branch 信息加上颜色呢?

在 PS1 变量里设置颜色, 我们可以用以下格式实现:

```
\e[F;Bm 要改变颜色的部分 \e[m
```

其中 F 表示前景色, B 表示背景色或者代码, 当同时希望有背景色和特殊代码时, 已以`\e[F;B;Cm` 的形式, 其中 C 为代码 

```
前景 背景  颜色
---------------------------------------
 30   40   黑色
 31   41   紅色
 32   42   綠色
 33   43   黃色
 34   44   藍色
 35   45   紫紅色
 36   46   青藍色
 37   47   白色

 代码 意义
 -------------------------
 0 OFF
 1 高亮显示
 4 underline
 5 闪烁
 7 反白显示
 8 不可见
```

所以, 最终, 我们的 PS1 变为:

```
PS1="\u@\h:\w\e[33;1m\$git_branch\e[m\$ "
```

## 0x20 ZSH

ZSH 和 bash 一样, 也是一种 shell, 有传言说: zsh: The last shell you'll ever need! Z 是最后一个字母, 所以它是终极的 shell. zsh 相比于 bash 有很多的优化和改进, 但是由于 zsh 的配置较为复杂, 所以一种通用的做法是用 zsh + oh-my-zsh

oh-my-zsh 可以帮你很方便的配置 zsh, github 地址是: https://github.com/robbyrussell/oh-my-zsh

安装非常简单, 首先安装必要的组件: 

`sudo apt-get install git wget zsh`

然后自动获取并安装 zsh:

`wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O - | sh`

然后把默认的 shell 替换为 zsh:

`chsh -s /bin/zsh`

搞定, 用了 zsh 之后你就会发现, git branch 信息甚么的已经可以在提示符里看到了.

