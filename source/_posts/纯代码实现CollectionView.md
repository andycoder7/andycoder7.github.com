title: "纯代码实现CollectionView"
date: 2015-11-30 08:50:19
tags: [objective-c, ios]
categories: ios
---

## 0x00 背景

本来应该是继续学习oc第N天系列的，不过我这人的性子不行。。喜欢知道一点就开始干了。。所以后面的知识也就变得不好整理了。在这段时间了解了一下关于oc中委托的的知识。这个还是很必要的。  

关于CollectionView没什么好多说的，简单粗暴的贴代码就好了

## 0x10 CollectionViewController.h

> CollectionViewController.h

```c
#import <UIKit/UIKit.h>

//这里继承的是UIViewController，不需要是UICollectionViewController类 ^_^ 但是要实现下面三个委托：
//UICollectionViewDelegate:     主要负责UICollectionView被选中时调用的方法等
//UICollectionViewDataSource:   主要负责给出CollectionView中的内容，包括多少个section/cell，以及每个cell中的内容
//UICollectionViewDelegateFlowLayout: 定义每个cell的大小，边框间距等
@interface CollectionViewController : UIViewController<UICollectionViewDelegate,UICollectionViewDataSource,UICollectionViewDelegateFlowLayout>

@end
```

<!-- more -->

## 0x20 CollectionViewController.m

> CollectionViewController.m

```c
#import "CollectionViewController.h"


@interface CollectionViewController ()
@end

@implementation CollectionViewController

- (void)viewDidLoad {
    [super viewDidLoad];
}

- (void)didReceiveMemoryWarning {
    [super didReceiveMemoryWarning];
}


#pragma mark <UICollectionViewDataSource>

//定义collectionView中Section的个数
- (NSInteger)numberOfSectionsInCollectionView:(UICollectionView *)collectionView {
    return 1;
}

//定义每个Section中Cell的个数
- (NSInteger)collectionView:(UICollectionView *)collectionView numberOfItemsInSection:(NSInteger)section {
    return 4;
}

//定义cell的相关属性（背景颜色等），并在cell中按需添加控件（label/button等）
- (UICollectionViewCell *)collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath {

    static NSString * CellIdentifier = @"CollectionViewCell";
    UICollectionViewCell * cell = [collectionView dequeueReusableCellWithReuseIdentifier:CellIdentifier forIndexPath:indexPath];

    //设置cell的背景颜色
    cell.backgroundColor = [UIColor whiteColor];
    //在cell中添加控件，在这里只是简单的添加了一个label
    UILabel *cellLabel = [[UILabel alloc] initWithFrame:CGRectMake(0, 0, 80, 80)];
    cellLabel.text = [NSString stringWithFormat:@"%ld",(long)indexPath.row];
    [cellLabel setTextColor:[UIColor blackColor]];
    [cellLabel setBackgroundColor:[UIColor lightGrayColor]];
    //清除cell中所有的原有控件
    for (id subView in cell.contentView.subviews) {
        [subView removeFromSuperview];
    }
    //添加刚刚定义的控件
    [cell.contentView addSubview:cellLabel];

    return cell;
}

#pragma mark <UICollectionViewDelegateFlowLayout>

//定义每个Item 的大小
- (CGSize)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout*)collectionViewLayout sizeForItemAtIndexPath:(NSIndexPath *)indexPath {
    return CGSizeMake(75, 80);
}

//定义每个UICollectionView 的 margin
-(UIEdgeInsets)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout *)collectionViewLayout insetForSectionAtIndex:(NSInteger)section {
    //分别是上下左右的间距
    return UIEdgeInsetsMake(5, 5, 5, 5);
}

#pragma mark <UICollectionViewDelegate>

//定义cell被选中时执行的操作（被touch的时候）
- (void)collectionView:(UICollectionView *)collectionView didSelectItemAtIndexPath:(nonnull NSIndexPath *)indexPath {
    NSLog(@"让我的cell变黄！！");
    UICollectionViewCell * cell = (UICollectionViewCell *)[collectionView cellForItemAtIndexPath:indexPath];
    for (id subview in cell.contentView.subviews) {
        [subview setBackgroundColor:[UIColor yellowColor]];
    }
}

//定义cell解除选中时执行的操作（其他cell被touch的时候，并不是touch松开的时候！！）
- (void)collectionView:(UICollectionView *)collectionView didDeselectItemAtIndexPath:(nonnull NSIndexPath *)indexPath {
    NSLog(@"让我的cell变回来！！");
    UICollectionViewCell * cell = (UICollectionViewCell *)[collectionView cellForItemAtIndexPath:indexPath];
    for (id subview in cell.contentView.subviews) {
        //我这里偷懒的把所有子控件都设为灰色。。但实际情况。。可能一个cell中有多个子控件，并且背景颜色各不相同
        [subview setBackgroundColor:[UIColor lightGrayColor]];
    }
}

@end
```

## 0x30 在view中添加collectionView

我是在一个tableView中嵌入的CollectionView，在其他地方用CollectionView也是类似的

```c
UIView *view = [[UIView alloc]init];
view.backgroundColor = [UIColor whiteColor];
//初始化collectionView的控制类，所有相关的代理方法都在该类中进行了实现
//cvc: @property (nonatomic, strong)CollectionViewController *cvc;
//cvc不能是局部变量，需要是类的属性之类的，否则在本方法结束后，cvc就会被释放掉，collectionView需要的一些委托也找不到对应的实现了
self.cvc = [[CollectionViewController alloc] init];

UICollectionViewFlowLayout *flowLayout =[[UICollectionViewFlowLayout alloc]init];
//设置滚动方式为垂直滚动（这个设置可要可不要）
[flowLayout setScrollDirection:UICollectionViewScrollDirectionVertical];
//初始化了collectionView类，定义了该view的大小，具体view中的内容在UICollectionViewDataSource的委托函数中实现
UICollectionView *ucv  =[[UICollectionView alloc]initWithFrame:CGRectMake(10, 0, self.view.frame.size.width-20, 80) collectionViewLayout:flowLayout];
//相关属性设置
[ucv setBackgroundColor:[UIColor whiteColor]];
//设置其委托和数据源的对象
//委托用来定义collectionView上的一些事件如何处理等
ucv.delegate = self.cvc;
//数据源用来定义collectionView中的内容
ucv.dataSource = self.cvc;
//注册Cell，必须要有，否则会抛出'NSInternalInconsistencyException'异常, 原因: 'could not dequeue a view of kind: 
//UICollectionElementKindCell with identifier UICollectionViewCell - must register a nib or a class for the identifier
//or connect a prototype cell in a storyboard'（其实我一点都看不懂这个原因。。=_=||）
//forCellWithReuseIdentifier的值一定要与CollectionViewController类中定义的identifier相同
[ucv registerClass:[UICollectionViewCell class] forCellWithReuseIdentifier:@"CollectionViewCell"];

[view addSubview:ucv];
```

## 0x40 总结

最喜欢贴代码的博客了。。写的再清楚还是没有代码清楚，自己领悟吧~ 

## 0x50 参考文献

1. [UICollectionView的使用方法及demo](http://blog.csdn.net/u011439689/article/details/39551163?utm_source=tuicool&utm_medium=referral)
