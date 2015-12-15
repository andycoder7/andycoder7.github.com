title: "UITableViewCellStyle"
date: 2015-12-15 11:21:08
tags: [objective-c, ios]
categories: ios
---

## 0x00 版本

1. Xcode 7.2
2. OS X 10.11.1
3. Simulator 9.2
4. Simulator iphone6s
5. IOS 9.2

## 0x10 UITableView

要加载添加一个UITableView，首先需要 alloc 一个 UITableView，可以是直接一个 UITableView 的对象，也可以是 UITableViewController 对象的属性

```c
UITableView *tv = [[UITableView alloc] initWithFrame:CGRectMake(0, 20, self.view.frame.size.width, self.view.frame.size.height)];

// 或者 
UITableViewController *tvc = [[UITableViewController alloc]init];
tvc.tableView = [[UITableView alloc]initWithFrame:CGRectMake(0, 20, self.view.frame.size.width, self.view.frame.size.height)];
```

<!-- more -->
然后指定其 delegate 和 dataSource，前者负责事件的响应，后者负责样式的描述。当然，被指定的对象一定要实现`<UITableViewDataSource,UITableViewDataSource>`协议

```c
@interface TableViewController ()<UITableViewDataSource,UITableViewDataSource>

@end

@implementation TableViewController

- (void)viewDidLoad {
    [super viewDidLoad];
}

- (void)didReceiveMemoryWarning {
    [super didReceiveMemoryWarning];
}

#pragma mark - Table view data source

//定义一个tableView中有多少个section
- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return 5;
}

//定义每一个section中有多少row，可以通过对参数section的判定返回不同的值
- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
    return 2;
}

//定义每个row中cell的内容
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    //用于资源reuse的标示符，必须要有，具体自行google
    static NSString *cellIdentifier = @"cellIdentifier";
    //初始化cell
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:cellIdentifier];

    if (cell == nil) {
        switch (indexPath.section) {
            case 0:
                //默认style，没有子标题
                cell = [[UITableViewCell alloc]initWithStyle:UITableViewCellStyleDefault reuseIdentifier:cellIdentifier];
                cell.textLabel.text = @"标题";
                cell.detailTextLabel.text = @"子标题";
                //右侧的附加类别显示为：箭头
                cell.accessoryType = UITableViewCellAccessoryDisclosureIndicator;
                break;
            case 1:
                //有子标题的style
                cell = [[UITableViewCell alloc]initWithStyle:UITableViewCellStyleSubtitle reuseIdentifier:cellIdentifier];
                cell.textLabel.text =@"标题";
                cell.detailTextLabel.text = @"子标题";
                //右侧的附加类别显示为：显示细节的按钮
                cell.accessoryType = UITableViewCellAccessoryDetailButton;
                break;
            case 2:
                //styleValue1 子标题在右侧
                cell = [[UITableViewCell alloc]initWithStyle:UITableViewCellStyleValue1 reuseIdentifier:cellIdentifier];
                cell.textLabel.text =@"标题";
                cell.detailTextLabel.text = @"子标题";
                //右侧的附加类别显示为：细节按钮+箭头
                cell.accessoryType = UITableViewCellAccessoryDetailDisclosureButton;
                break;
            case 3:
                //styleValue2 没有图片,子标题靠在标题右侧
                cell = [[UITableViewCell alloc]initWithStyle:UITableViewCellStyleValue2 reuseIdentifier:cellIdentifier];
                cell.textLabel.text =@"标题";
                cell.detailTextLabel.text = @"子标题";
                //右侧的附加类别显示为：勾选
                cell.accessoryType = UITableViewCellAccessoryCheckmark;
                break;
            case 4:{
                       //不设置style的话默认使用default Style，可以通过对cell addsubview 来自定义cell的内容
                       cell = [[UITableViewCell alloc]initWithFrame:CGRectMake(0, 0, self.view.frame.size.width, 35)];
                       UILabel *cellTitle = [[UILabel alloc]initWithFrame:CGRectMake(0, 0, self.view.frame.size.width, 35)];
                       [cellTitle setTextAlignment:NSTextAlignmentCenter];
                       cellTitle.text = @"标题";
                       [cell addSubview:cellTitle];
                       //右侧的附加类别也可以是一个自定义的view
                       cell.accessoryView = [[UISwitch alloc]init];
                       break;
                       //由于oc中不支持在case中申明变量，所以需要加这对大括号，如果要去掉的话，只要把cellTitle的申明放在外面就好了
                   }
            default:
                   break;
        }
    }
    //对所有的cell添加图片
    cell.imageView.image = [UIImage imageNamed:@"tableViewCellImage.png"];
    //对所有的cell的边框宽度设为1，方便看到边框的形状
    cell.layer.borderWidth = 1;
    //对边框的颜色进行设定
    [cell.layer setBorderColor:[UIColor lightGrayColor].CGColor];

    return cell;
}

//定义每个row的高度
- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath {
    return 35;
}
//定义每个section上方空白区域的高度
- (CGFloat)tableView:(UITableView *)tableView heightForHeaderInSection:(NSInteger)section {
    return 40;
}
//定义每个section上方空白区域的内容
- (UIView *)tableView:(UITableView *)tableView viewForHeaderInSection:(NSInteger)section {
    UIView *view = [[UIView alloc]initWithFrame:CGRectMake(0, 0, 375, 40)];
    UILabel *label = [[UILabel alloc]initWithFrame:CGRectMake(0, 20, 375, 20)];
    [label setTextColor:[UIColor redColor]];
    [label setTextAlignment:NSTextAlignmentCenter];
    switch (section) {
        case 0:
            label.text = @"UITableViewCellStyleDefault";
            break;
        case 1:
            label.text = @"UITableViewCellStyleSubtitle";
            break;
        case 2:
            label.text = @"UITableViewCellStyleValue1";
            break;
        case 3:
            label.text = @"UITableViewCellStyleValue2";
            break;
        case 4:
            label.text = @"自定义的view";
            break;
        default:
            break;
    }
    [view addSubview:label];
    return view;
}

@end
```

效果如图：

![UITableViewCellStyle](/images/uitableviewcellstyle/UITableViewCellStyle.png)

示例的代码:[https://github.com/andycoder7/IOStest/tree/master/testUITableView](https://github.com/andycoder7/IOStest/tree/master/testUITableView)
