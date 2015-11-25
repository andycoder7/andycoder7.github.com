title: "学习oc第一天"
date: 2015-11-25 09:59:58
tags: [objective-c, ios]
categories: ios
---

## 0x00 背景

没啥，无聊想玩玩，有了mac总不能浪费吧，现在连真机调试都支持了，不用 xcode 写写 oc 实在是太对不起它了。

## 0x10 第一感觉和疑问

> OC/C/xcode/cocoa 的关系？

OC 是 C 的扩展，语法有一定的变动，新增了很多的概念，是用于开发苹果家各种设备的语言；XCode 是一个 IDE，类似于 windows 的 visual studio; cocoa 是用于开发UI的一个框架。

> OC 和 C 类似的地方？

- OC 也用 .h 作为头文件，但是源文件的后缀为 .m (message 或者 method 的意思吧)。在 OC 里是 `#import <>` 而不是 `#include <>`, import 能保证头文件只被包含一次。

- C 中的数据类型、表达式、运算符在 OC 中都支持，包括循环结构： for, while, do while, break, continue 分支结构： if, else, switch

- NSLog()函数与printf()函数类似，用于在终端输出信息，但是添加了一些别的，例如同时会输出时间戳等

> 为什么 OC 中很多函数或者类型都是 NS 开头？

<!-- more -->

Cocoa 是从1980年代由 NeXT 开发的编程环境 NeXTSTEP 和 OPENSTEP 演变而来，所以其类别之名皆以 NS 前缀(代表NeXTSTEP)。

> 经常会看到 `@"a test string"` 这种，这是什么？

在字符串前面加上 @， 表示是 NSString 类型， 在 NSLog 函数中使用 `%@` 来表述输出 NSString 类型的数据， 就像用 `%d` 来表示输出整形一样。由于 NSLog 函数的参数需要是 NSString 类型，所以代码看如下所示：

```c
    //示例代码
    NSString *test = @"test";
    NSLog(@"test string: %@\n", test);
    // 类似于 printf("test string: %@\n", test);
```

> 在 oc 中，id 是什么意思？

类似于 c 中的 `void *` 表示任意类的对象

## 0x20 OC 中类、方法

### 0x21 类的定义

```c
@interface person : NSObject // 为person类定义; NSObject 表示父类
// 似乎 oc 并不支持多继承 =.=
{
    // 这里是成员变量
    @public // 同样有public/protected/private/package四种
        NSString * _name;
        int _age;
    ...
}
// 这里是属性和方法
@property (nonatomic,weak) NSString *temp; // 这是属性
- (void) newPerson:(NSString *)name withAge(int)age // 这是方法

@end // 接口定义结束
```

- 关于成员变量与属性的区别，请参照[这篇博文][1]

- 关于属性中@property的各个属性值的含义，请参照[这篇博文][2]

### 0x22 类的方法的定义

形式大概是：`- (返回值类型) 方法名:(参数1类型)参数1 部分方法名2(参数2类型)参数2...` 

```c
// 部分方法名可以省略，但不建议
- (void) newPerson:(NSString *)name withAge(int)age
{
    //_name 和 _age 是类的成员变量，也可以通过self->_name的形式访问
    _name = name;
    _age = age;
}
```

最开始的那个 `-` 其实也可以是 `+`, 它们的区别是，前者是类的实例的方法，后者是类的方法；也可以理解为前者需要类实例化之后才能用，而后者是 static 方法。

### 0x23 点语法

oc 中的点语法不是访问成员变量，而是隐式的调用其get/set方法。

```c
int main(int argc, const char * argv[]) {
        @autoreleasepool {
            person *p = [[person alloc] init];
            [p newPerson:@"andy" withAge:1];
            // 这里是[]语法，用于方法调用的，等同于c++中的 p.newPerson("andy", 1);
            p._age = 2; // 这里会报错，因为没有定义局部变量 _age 的set方法
            p.temp = @"hehe"; // 对于 property属性 编译器会自动对其添加get/set方法（如果没有自定义的话）
        }
        return 0;
}
```

如果要自定义成员变量或者属性的 get/set 方法的话：

```c
// 自定义成员变量 _age 的 get 方法
// 方法的返回值为变量的类型，方法名与变量同名
- (int) _age
{
    return _age;
}
// 自定义属性 temp 的 set 方法
// 方法返回值为空，参数为属性类型，方法名为 set属性名，其中属性名首字母大写，
// 如果首字母为_或者已经是大写字母则不变化，例如：setTemp, set_age 等
- (void) setTemp:(NSString *)t
{
    temp = t;
    // 不能写成： self.temp = t
    // 因为self.temp是调用temp的set方法，所以等同于 setTemp(t)，所以会出现死循环，不断递归下去，直到爆栈
}
```

对属性和成员变量的自定义get/set方法规则是一样的，不同的是如果不自定义的话，编译器会自动帮属性生成get/set方法，但成员变量不会。

### 0x24 方括号语法

oc 中的方括号用于给某个对象发送消息，通知它该做什么，个人理解类似于去调用其方法。似乎在 oc 中通知对象执行某种操作，成为发送消息（也叫调用方法）

```c
person *p = [[person alloc] init];
[p newPerson:@"andy" withAge:1];
// 函数定义是：- (void) newPerson:(NSString *)name withAge(int)age
// 等同于c++中的 p.newPerson("andy", 1);
```

### 0x25 类的实现

oc 中类的实现用 `@implementation` 关键字，结束一样用 `@end`，然后把 .h 中定义的各种方法都给出具体的实现代码就好了~

```c
#import "person.h"

@implementation person

- (void) newPerson:(NSString *)temp withAge:(int)age
{
    self->_name = temp;
    self->_age = age;
}
- (int) _age
{
    return _age;
}
- (void) set_age:(int)age
{
    self->_age = age;
}
@end
```

## 0x30 block

oc 中 block 的概念类似于其他语言中的 lamda 表达式，用于实现匿名函数等，在用 AFnetworking 的时候第一次接触到，block 最大的特征是带有 ^， 我对 block 的了解来自以下几篇博文：

- [教你爱上Blocks][3] (讲了block的语法、省略句式和用法) 我主要看的就是这篇
- [OC中， block（块）的本质是什么？][4] (为什么在block不能给block外的局部变量赋值)
- [Blocks学习笔记总结][5] (网友对《Blocks Progromming Gude》学习的笔记总结，和自己理解的互相印证一下)

最后说一句。。其实贴链接而不是自己再讲一遍。。。只有一个原因。。那就是。。懒 =.=

[1]: http://blog.csdn.net/huang2009303513/article/details/38445593
[2]: http://blog.csdn.net/dqjyong/article/details/7668601
[3]: http://my.oschina.net/joanfen/blog/317644?fromerr=0QtSqBvw
[4]: http://www.zhihu.com/question/30779258
[5]: http://www.cnblogs.com/xinye/archive/2013/03/03/2941203.html
