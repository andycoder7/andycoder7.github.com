Get Local IP Information by SIOCFIGCONF
===

###缘起

---
今天在coding的时候需要获得自己本机IP,本来以为是一件很简单的事情,用getsockname就可以搞定了呀~.但仔细想想之后,好像又不是那么回事.当我PC同时连上有线和无线的时候,有两个IP,如果我希望我拿到就是WIFI的那个IP,貌似就不是那么容易的事情了.于是继续google+baidu+man-page.最后决定用ioctl这个神器=.=

###收获
我主要参考的是这篇[文章](http://zhumeng8337797.blog.163.com/blog/static/1007689142012311082638/),很长..相当长...但是其实是把一样的内容重复了好几遍,我把链接放在这里只是因为我真的是看的它...

相关test代码,老习惯,我还是放在了[本目录下的test.c](test.c)中.ioctl没什么好讲的,这个神器大家都懂得~,总的来说,主要是两个结构体:`ifreq`和`ifconf`,他们都是在/usr/include/linux/if.h中定义的,额,反正在我的系统(ubuntu14.04)里是在这个位置的...下面是我节选出来的两个结构体的定义

struct ifreq:

    struct ifreq {
    #define IFHWADDRLEN	6
		union
		{
			char	ifrn_name[IFNAMSIZ];		/* if name, e.g. "en0" */
		} ifr_ifrn;

		union {
			struct	sockaddr ifru_addr;
			...
		} ifr_ifru;
	};

    #define ifr_name	ifr_ifrn.ifrn_name	/* interface name	*/
    #define	ifr_addr	ifr_ifru.ifru_addr	/* address		*/
    ...

从机构体中可以看出,ifreq包括interface(if是指interface,你不会不知道吧^.^)的name和IP,当然因为是个union,它也可以用来表示和存储其他信息,不过我们这里只用到了ifru_addr.所以,简而言之,这个结构体就是用来存储接口的相关信息的.(至于什么是接口,额...这个么..就是比如说你在ifconfig时看到的eth0或者wlan0什么的,对就是那货~)
    
struct ifconf:

    struct ifconf  {
    	int	ifc_len;			/* size of buffer	*/
    	union {
    		char *ifcu_buf;
    		struct ifreq *ifcu_req;
    	} ifc_ifcu;
    };
    #define	ifc_buf	ifc_ifcu.ifcu_buf		/* buffer address	*/
    #define	ifc_req	ifc_ifcu.ifcu_req		/* array of structures	*/
    
这个结构体是个好东西,它才是我们的关键,这里面用到了上面的那个结构体的指针,还有一个int ifc_len,你想到了什么?对,没错!ifc_len表示下面那个指针指向的内容有多长,指针么就是指向第一个struct ifreq的地方,我这里为什么要说是第一个呢,因为有了ifc_len,它还真可以变成好多个.如果你还没有理解的话,那我们画个图吧~

    ifc_len = 4*sizeof(struct ifreq);
	struct ifreq *ifcu_req
	               |          -----------------
	               |--------> |struct ifreq 1 |
                              -----------------
	                          |struct ifreq 2 |  <---------这个的地址是ifcu_req + sizeof(struct ifreq)
                              -----------------
	                          |struct ifreq 3 |  <---------这个的地址是ifcu_req + 2*sizeof(struct ifreq)
                              -----------------
	                          |struct ifreq 4 |  <---------这个的地址是ifcu_req + 3*sizeof(struct ifreq)
                              -----------------

所以,同理,通过,`ifc_len/sizeof(struct ifreq)` 我们就可以知道到底有几个interface了(千万不要以为你的系统里只有一个接口,一般你都会有俩~,一个是eth0或者wlan0,另一个是lo,当然不同的系统名字不一样,例如centos里可能是lo和ppp0什么的)

###拓展

---
1. 其实这三种类型: `sockaddr_in.sin_addr == long == unsigned char[4]`<br/>
*Ps:关于如何找到位置类型所在的头文件,然后查看其具体的定义,可以使用 `grep -R`,例如在这里,我们用 `grep -R 'sin_addr' /usr/include/` 找到这个变量在/usr/include/netinet/in.h中定义,然后就进入这个文件自己找吧:)*

2. 可以用这样的方式来查看IP地址: `printf("ip: %x 是: %3u.%3u.%3u.%3u",long, char[0], char[1], char[2], char[3]);`

3. 简单整理一下几个用于IP变量类型转换的函数: 

- inet_ntoa : long(网络字节序) -> char *
- inet_aton : char * -> long(网络字节序)

> inet_aton() converts the Internet host address cp from the IPv4 numbers-and-dots notation into binary form (**in network byte order**) and stores it in the structure that inp points to. 

- inet_addr : char * -> long(网络字节序) **有255bug**

> The inet_addr() function converts the Internet  host  address cp from IPv4  numbers-and-dots notation into binary data **in network byte order**.

- inet_network : char * -> long(主机字节序) **有255bug**

> The inet_network() function converts cp, a string in IPv4  numbers-and-dots  notation, into a number **in host byte order** suitable for use as an Internet  network  address.

- 这里所谓的255bug是只当IP为255.255.255.255时,函数会返回0xffffffff,这本身没有错,也应该是全f,但是,在函数的定义和说明中,当输入非法的时候,返回值为-1,于是,问题就出来了.inet_aton函数没有这个bug是因为它把返回0认为出错,非0表示正确...对于这种做法..我只能说..太贱了 ->_->

> inet_addr(): If the input is invalid, INADDR_NONE (usually -1) is returned. Use of this function is problematic because -1 is a valid address (255.255.255.255).
> inet_aton() returns nonzero if the address is valid, zero if not.

- 当然还有很多其他类似的,具体参考inet_addr的man-page吧: `man inet_addr`
