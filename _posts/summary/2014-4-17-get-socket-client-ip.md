---
layout: post
title: Get Client IP from Socket
category: summary 
---

###缘起

---

这个问题在之前就解决了，但是一直都没有空把总结写了。今天总算可以小小休息一下，抽个时间把这个Summary秒了，不然总觉得不舒服:(

之前在写socket编程的时候，觉得也就那些东西了，但是实际上我不知道的还很多很多。。例如，在那么一天，我突然觉得我想知道一下客户端连上来时候他的IP是什么，然后我就动手开始写了一个test，然后我就囧了。怎么获取client的IP呢？我的第一反应是：不是有client的fd了么，有文件描述符的话应该都可以搞定了吧，毕竟在服务器端，服务器的fd是有bind一个IP和PORT的，所以我们有理由相信，client的fd也会有bind上IP和PORT信息，当然，也应该有相应的获取这些信息的函数接口。想的是很好，但当我写到 `int client = accept(fd, (struct sockaddr *) &addr, &len)` 的时候，呵呵，然后应该怎么办？

###解决过程

---

当我在baidu+google+man-page中各种找的时候，突然意识到accept这个函数，我的代码由于只是为了做快速测试，所以写的很cuo，一切为了快速验证，但是在我正常coding的时候，accept的参数我一般都会这么命名：`accept(server_fd, (struct sockaddr *) &client_addr, &addr_len)`大家有没有发现，我中间的那个参数写的是client_addr而不是server_addr，而且传的也是指针，这就意味着这个参数是可以把数据带出来的，那么这是不是就意味着这里就可以拿到client的IP和PORT了呢，（*PS:至于我正常写的时候为什么会写client_addr是因为我之前在刚开始写socket的时候，各种书上或者网上都是这么写的，当时还没有获取clientIP的需求，所以也一直都没有注意到他。。=_=|| *）那么想当然的，accept的第三个参数len也是用来存放addr的长度的咯。所以我们很自然的就有了下面的代码：

    int fd = open_listenfd(4900);
	struct sockaddr_in addr;
	socklen_t len = 0;
	bzero((char*)&addr, sizeof(addr));
	int client = accept(fd, (struct sockaddr *) &addr, &len);
	printf("%s\n", inet_ntoa(addr.sin_addr));

其中的open_listenfd函数是以前自己写的一个函数，因为每次用socket前面都要写一坨一样的code，所以就直接把他们合成一个函数了，具体的代码可以在[github/tbox仓库里](http://github.com/andycoder7/tbox/blob/master/get_socket_client_ip/test.c)看到。好了，这不是重点，重点是这段代码是不能用的，虽然编译不会有什么错误，但是你是拿不到clienct的信息的。这是为什么呢？于是，有开始各种百度谷歌manpage了，期间我在oschina上发现有为仁兄也遇到了相应的问题：[accept无法获取客户端ip](http://www.oschina.net/question/1166197_149234)，但是没人理他。。看看问题挺像的，本来以为可以找到答案了，可惜却是一场空，没办法，继续自己找。后来在accpet的man-page中找到了原因（不得不说，man-page真是个好东西～）

> The addrlen argument is a value-result argument: the caller must initialize it to contain the size (in bytes) of the structure pointed to by addr; on return it will contain the actual size of the peer address.

原来len变量不仅用于返回addr的长度，也要负责把addr的长度传进去，所以修正之后的代码应该是：

    int fd = open_listenfd(4900);
	struct sockaddr_in addr;
	socklen_t len = sizeof(addr);
	bzero((char*)&addr, sizeof(addr));
	int client = accept(fd, (struct sockaddr *) &addr, &len);
	printf("%s\n", inet_ntoa(addr.sin_addr));

经过修正之后，一切都正常了。

###拓展

---

之前在网上找资料的时候，我发现有不少人回答用getsockname()或者getpeername()函数来获取client的IP，这两个函数有什么区别呢？用他们获取IP和我们直接用accept获取IP又有什么区别？

	getsockname(client, (struct sockaddr *) &addr, &len);
	printf("%s\n", inet_ntoa(addr.sin_addr));
	getpeername(client, (struct sockaddr *) &addr, &len);
	printf("%s\n", inet_ntoa(addr.sin_addr));

大家可以自己想一下。答案我下面再说。

###答案和零碎

---

- 额，好吧，其实答案我还没完全搞清楚，等我搞清楚了再来吧答案补上哈:)
- 如果大家在运行[我的test](test.c)的程序，发现sock server输出是0.0.0.0。怎么回事？是程序有问题么？其实不是的，我们都知道，在socket服务器程序中，我们一般会bind本机IP，但是，由于一般本机IP不确定，或者会变，所以，一般bind本机IP都不直接写IP，而是使用宏`INADDR_ANY`，这个宏是什么呢？通过 `sudo grep -R "INADDR_ANY" /usr/include/` 我们可以找到这个宏其实就是0.0.0.0
- 千万不要以为自己都懂了，有可能你不知道的还有很多。

*Andy(andy.at.working@gmail.com) 2014-04-17*
