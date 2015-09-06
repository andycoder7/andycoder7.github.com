title: "HOW TO FUCK GFW"
date: 2015-09-06 20:11:36
tags: [FUCK,GFW]
categories: 干！就一个字
---

<center>*** I’m sorry, OPENSHIFT. ***</center>

## 0x00 搭建ssh代理服务

1 在浏览器中打开 [http://www.openshift.com](http://www.openshift.com)

2 注册一个账号，点击右上角的 [SIGN UP](https://www.openshift.com/app/account/new) 

![](/images/how-to-fuck-gfw/2.png)

3 填写邮箱、密码、确认密码、验证码，然后点击注册（SIGN UP）
<!-- more -->

![](/images/how-to-fuck-gfw/3.png)
![](/images/how-to-fuck-gfw/3_2.png)

4 进入邮箱，并点击链接，完成注册。

![](/images/how-to-fuck-gfw/4.png)

5 点击链接后，出现如下界面，点击“我接受（I Accept）”完成注。

![](/images/how-to-fuck-gfw/5.png)

6 自动跳转至如下页面，点击下方“创建你的第一个应用（Create your first application now）”, 由于是免费账号，所以最多只能创建三个，不会我们只要创建一个就够了

![](/images/how-to-fuck-gfw/6.png)

7 随便选一个即可，我选的是python2.7

![](/images/how-to-fuck-gfw/7.png)

8 填一下public url

![](/images/how-to-fuck-gfw/8.png)

9 Region就默认的us即可，点击创建

![](/images/how-to-fuck-gfw/9.png)

10 询问是否需要部署代码，选择不是现在，继续即可。

![](/images/how-to-fuck-gfw/10.png)

11 创建完毕后自动返回首页，点击设置（settings）添加公钥

![](/images/how-to-fuck-gfw/11.png)

12 看一下本机的公钥，默认是在 ~/.ssh/id_rsa.pub文件中，如果该文件不存在，甚至 ~/.ssh 文件夹不存在，则在终端输入 ssh-keygen， 然后一路回车即可自动生成。

![](/images/how-to-fuck-gfw/12.png)

13 把文件中的全部内容复制到下图蓝框中，然后点击保存（save）。

![](/images/how-to-fuck-gfw/13.png)

14 成功后如下图，然后点击Settings边上的 Applications

![](/images/how-to-fuck-gfw/14.png)

15 然后再点击刚创建的应用。

![](/images/how-to-fuck-gfw/15.png)

16 点击右侧的“Want to log in to your applicantion?”,自动弹出下面的文字和ssh地址。复制该ssh地址

![](/images/how-to-fuck-gfw/16.png)

17  在终端中输入：` ssh -CNg -D 7070 55eb31c87628e165a900012b@python-andycoder7.rhcloud.com -v`
    
    C表示压缩数据传输
    N表示不执行脚本或命令
    g表示允许远程主机连接转发端口
    D指定一个本机“动态”的应用程序转发端口，可以理解为本机监听的端口号
    v表示详细信息
    
![](/images/how-to-fuck-gfw/17.png)

18 输入yes继续连接，看到如下log，表示正常启动

![](/images/how-to-fuck-gfw/18.png)

## 0x01 配置本地浏览器（以Firefox为例）

1 打开Firefox，点击菜单，点击附加组件

![](/images/how-to-fuck-gfw/21.png)

2 在搜索中输入 autoproxy，并安装如下组件

![](/images/how-to-fuck-gfw/22.png)

3 安装完毕后要求重启浏览器，重启之。然后点击工具栏上“福”右侧的下拉按钮，点击首选项

![](/images/how-to-fuck-gfw/23.png)

4 在弹出的对话框中点击“代理服务器”下的“编辑代理服务器”

![](/images/how-to-fuck-gfw/24.png)

5 把第一行 Free Gate 中的 IP 设为运行ssh命令电脑的IP，在此处即为127.0.0.1，端口则填命令中 -D 参数后的那个端口号，在此处即为7070，后面选择 sock5，然后点击确定即可。

![](/images/how-to-fuck-gfw/25.png)

6 把默认代理设置为 Free Gate

![](/images/how-to-fuck-gfw/26.png)

7 为了测试方面，选用全局代理，然后在浏览器中输入 [https://www.youtube.com/watch?v=G1F223YWtN8&list=PL1xmVbigZck3XKBh5eejphxgF0Pe8U-UG](https://www.youtube.com/watch?v=G1F223YWtN8&list=PL1xmVbigZck3XKBh5eejphxgF0Pe8U-UG) （九评共产党）

![](/images/how-to-fuck-gfw/27.png)

终端输出的log：

![](/images/how-to-fuck-gfw/28.png)

## 0x02 结语

<p style="font-size:40px; text-align:center;">FUCK GFW<p>

<p style="font-size:40px; text-align:center;">不自由 毋宁死<p>

<p style="font-size:20px; text-align:center;">Thank you very much, OPENSHIFT.<p>

<p style="font-size:14px; text-align:center;">如果大家都拿來滥用，肯定會遭到封殺。非开发者们，请远离OpenShift。<p>
