---
layout: post
title: 零长数组(Arrays of Length Zero)
category: tech_blog
---

##缘起

---

今天在和朋友聊天的时候突然料到了零长数组（arrays of length zero）或是叫可变数组，或者叫变长数组（flexible array），或者叫柔性数组，anyway，反正就是那个长度为0的数组。举个例子来说就是 `char contents[0]` 。作为一个对C不是很了解的人来说，很好奇这玩意是干嘛用的？于是写了一些简单的测试程序，并用gdb啥的看了一下内存使用，还是似懂非懂，最后妥协了，在万能的oschina中提出了我问题，我当时的题目是[长度为0的数组的size为什么不一定是0？](http://www.oschina.net/question/1470892_151368)。（*Ps：我这里不好意思的说一下，由于当时我想复杂了，各种想不通，竟然忘了对齐的问题。。。真是弱爆了*）虽然问题没有问到点上，但是还是有好心人给出了我要的答案：[C语言结构体里的成员数组和指针](http://coolshell.cn/articles/11377.html)。如果对这个问题感兴趣的同学也可以参考一下陈皓大大的这篇文章，相信会有所收获的：）

##原理

---

大家都知道在申明数组的时候方括号中的数字表示的数组的长度，也知道数组是从0开始的，所以如果申明长度为10的数组 `int num[10]` 的时候，`num[10]` 是不能用的，额，严格的来说，是可以用的，但是是数组越界问题。知道这些内容，对我们平时用用数组来说已经没什么问题了。但是，当数组长度为0时，这时，这个数组又是一个什么样的存在呢？我们可以简单的写段test试试：

    int num[0];
	printf("size of num: %d\n", sizeof(num));
	printf("address of num: %d\n", num);

我们发现输出的结果表示size为0,但是address是存在的。这是什么情况呢？为什么明明在内存中没有的东西会还会读取的到他的地址呢？平时都说数组和指针差不多，就算是指针，那至少也要在内存中申请一块int来存放地址吧。这到底是怎么回事呢？其实，问题就出在我们之前说的，指针和数组差不多，但只是差不多，并不是一样！他们的区别是什么？这需要从他们的汇编实现来说了，指针的汇编实现是mov，而数组是lea。lea的全称是load effective address，也就是把地址传过去。关于数组和指针的区别，在陈皓大大的[博客](http://coolshell.cn/articles/11377.html)里也有讲的很清楚了。所以在调用num的时候会返回num[0]的地址，但是在内存中并没有一个具体存在的指针指向num[0]。说的不清楚不知道你是否听懂了。

##用法

---

柔性数组一般可以用来动态的扩展struct，用gcc上的[例子](http://gcc.gnu.org/onlinedocs/gcc/Zero-Length.html)来说
	
	struct line {
		int length;
		char contents[0];
	};
	struct line *thisline = (struct line *)
		malloc (sizeof (struct line) + this_length);
	thisline->length = this_length;

相信代码已经能说明很多了吧，如果还是不理解的话，我再写一下这个struct的用法吧

	for (i = 0; i < (thisline->length - 1); i++) {
			thisline->contents[i] = 'a' + i%26;
	}
	thisline->contents[thisline->length - 1] = 0;
	//数组长10位，最后一位置为'\0'，所以应该只会输出9个字符
	puts(thisline->contents);

具体的test code可以在[github/tbox仓库里](https://github.com/andycoder7/tbox/blob/master/array_of_length_zero/test.c)中看到。

##拓展

---

我在理解柔性数组，唔。。。我还是觉得零长数组比较形象，我还是叫他零长数组吧。我在理解零长数组的时候也看到了其他一些有趣的东西，在这里和大家分享一下：一个就是我在oschina提的问题：

	typedef struct {
		uint8_t c1;
		uint8_t c2;
		uint8_t c3;
		int c4[0];
	} test;
	
	test c = {.c1 = 1, .c2 = 2, .c3 = 3};
	printf("address of c: %d\n", &c);
	printf("address of c1: %d\n", &(c.c1));
	printf("context of c1: %d\n", (c.c1));
	printf("address of c2: %d\n", &(c.c2));
	printf("context of c2: %d\n", (c.c2));
	printf("address of c3: %d\n", &(c.c3));
	printf("context of c3: %d\n", (c.c3));
	printf("address of c4: %d\n", &(c.c4));
	printf("context of c4: %d\n", (c.c4));
	printf("sizeof sturct is: %d\n", sizeof(test));

他的输出结果是：

    address of num: -1078654580
	address of c: -1078654596
	address of c1: -1078654596
	context of c1: 1
	address of c2: -1078654595
	context of c2: 2
	address of c3: -1078654594
	context of c3: 3
	address of c4: -1078654592
	context of c4: -1078654592
	sizeof sturct is: 4

**既然零长数组的在内存中不占用空间，那为什么struct的size或是4呢？**

另一个有意思的问题是：

	#include <stdio.h>
	struct str{
		int len;
		char s[0];
	};

	struct foo {
		struct str *a;
	};

	int main(int argc, char** argv) {
		struct foo f={0};
		if (f.a->s) {
			printf( f.a->s);
		}
		return 0;
	}

这段代码会core么？在VC++和GCC下都会在14行printf的时候crash掉，这是为什么呢？

##答案和零碎

---

- 第一个问题的答案是因为对齐,具体可参考陈皓大大的[深入理解C语言](http://coolshell.cn/articles/5761.html)一文
- 第二个问题的答案其实就是陈浩大大的博文讲的内容。不过建议大家先自己思考一下再去看。
- 对数组 `char c[10]` 来说，c和&c是一样的，都是c[0]的地址（我第一次知道，真是。。。囧）
- 我这里很多思想其实都是从陈皓大大的博文中自己转出来的，如果觉得我说的不清楚的，可以再去看一下陈皓大大的[博文](http://coolshell.cn/articles/11377.html)
- 我果然还是不会表述问题，不会写博客，第一次写，感脚好烂。。。希望对能对你有帮助

*Andy(andy.at.working@gmail.com) 2014-04-17*
