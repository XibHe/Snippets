# Snippets
记录一些奇妙的代码片段

### 数组求和
```
NSMutableArray *tempTotalPriceArray = [NSMutableArray array];
float tempAllPrices = [[_tempTotalPriceArray valueForKeyPath:@"@sum.floatValue"] floatValue];
```
### NSCharacterSet
根据NSCharacterSet匹配到的字符，分割字符串

```
    if ([_absoluteUrl rangeOfString:@"appLink/showBusyInfo?"].location != NSNotFound) {
        NSArray *absoluteArray = [_absoluteUrl componentsSeparatedByCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"?&"]];
        for (NSInteger i = 0; i < [absoluteArray count]; i++) {
            NSString *content = absoluteArray[i];
            if ([content rangeOfString:@"bid"].location != NSNotFound) {
                _bid = [content substringFromIndex:4];
                CLog(@"_bid = %@",_bid);
            }
        }
    }
```
### ceil
绘制角标提示视图时，在角标右侧偶尔会出现一条黑线。且有时在iPhone5s及以下机型不会出现, 只在iPhone6以上出现。这是因为计算出得size可能的值会10.3113325…… 这样的数, 而像素值显示的时候不可能出现显示半个像素的情况, 那么不足一个像素的值就会被忽略掉, 在分辨率较低的机型上不会出现, 而分辨率较高的则不会忽略, 就出现了黑线，于是采用ceil函数向上取整。

```
    CGFloat x = ceilf(percentX * tabFrame.size.width);
    CGFloat y = ceilf(0.1 * tabFrame.size.height);
```

### 基于UINavigationController + UIViewController自定义的UITabBarController切换控件

如标题所说，在自定义UITabBarController后，需求需要根据不同的情况，切换不同的UIViewController。但是，在初始化UITabBarController时，已经将需要切换的5个UIViewController，addChildViewController到了自定义UITabBarController中。这是就需要使用，

```
[self transitionFromViewController:oldController toViewController:newController duration:0.5 options:UIViewAnimationOptionTransitionCrossDissolve
animations:^{
   // no further animations required
}completion:^(BOOL finished) {
 [oldController removeFromParentViewController];     
 [newController didMoveToParentViewController:self];
}];
```
在新增的显示当前视图控制器与旧显示视图控制器二者间进行切换。
#### 参考文档
[Swapping child views in a container view
](http://stackoverflow.com/questions/19162874/swapping-child-views-in-a-container-view)

### 计算UILabel的行数

```
NSInteger _lineCount =  [self labelLineCount:addressWidth];
- (NSInteger)labelLineCount:(CGFloat)width
{
    NSInteger lineCount = 0;
    CGSize textSize = CGSizeMake(width, MAXFLOAT);
    NSInteger rHeight = lroundf([_addressLabel sizeThatFits:textSize].height);
    // 行间距
    NSInteger charSize = lroundf(13);
    lineCount = rHeight/charSize;
    CLog(@"No of lines: %ld",lineCount);
    return lineCount;
}
```
### 使用boundingRectWithSize计算内容高度
iOS7以前我们对UILabel进行根据内容自适应大小的时候会使用方法sizeWithFont:constrainedToSize:lineBreakMode，但是这个方法在iOS7之后就被Deprecated了。对此，iOS7提供了一个新的方法来替代它，就是NSString的成员方法：

```
- (CGRect)boundingRectWithSize:(CGSize)size options:(NSStringDrawingOptions)options attributes:(NSDictionary *)attributes context:(NSStringDrawingContext *)context；
```

```
CGSize infoSize = CGSizeMake(tableView.frame.size.width, 1000);
CGSize boundingRectWithSize = [_leftPrice.text boundingRectWithSize: infoSize options:NSStringDrawingUsesLineFragmentOrigin | NSStringDrawingUsesFontLeading  attributes:@{NSFontAttributeName:kFont(11)} context:nil].size;
CLog(@"boundingRectWithSize = %@",NSStringFromCGSize(boundingRectWithSize));
```
1. 参数1: 自适应尺寸,提供一个宽度,去自适应高度
2. 参数2:自适应设置 (以行为矩形区域自适应,以字体字形自适应)
3. 参数3:文字属性,通常这里面需要知道是字体大小
4. 参数4:绘制文本上下文,做底层排版时使用,填nil即可

### 使用CAGradientLayer实现颜色渐变

```
// 促销标签
UILabel *salesLabel = [[UILabel alloc] initWithFrame:CGRectMake(0, 0, screenWidth * 60 / 375 / 2, screenWidth * 26 / 375 / 2)];
salesLabel.text = [SalesTagsObject salesTagsText:leftModel.tags];
salesLabel.font = kFont(10);
salesLabel.textColor = [UIColor whiteColor];
salesLabel.textAlignment = NSTextAlignmentCenter;
[_leftImage addSubview:salesLabel];
// 促销标签渐变色
CAGradientLayer *gradientLayer = [CAGradientLayer layer];
gradientLayer.colors = @[(__bridge id)[UIColor colorWithHexString:@"#fd4e4e"].CGColor, (__bridge id)[UIColor colorWithHexString:@"#fe3a5a"].CGColor, (__bridge id)[UIColor colorWithHexString:@"#ff3153"].CGColor, (__bridge id)[UIColor colorWithHexString:@"#f6213a"].CGColor];
gradientLayer.locations = @[@0.0, @0.45, @0.8, @1.0];
gradientLayer.startPoint = CGPointMake(0, 0);
gradientLayer.endPoint = CGPointMake(1.0, 0);
gradientLayer.frame = CGRectMake(0, 0, screenWidth * 60 / 375 / 2, screenWidth * 26 / 375 / 2);
[salesLabel.layer addSublayer:gradientLayer];
```
1. colors 渐变的颜色
2. locations 渐变颜色的分割点
3. startPoint&endPoint 颜色渐变的方向，范围在(0,0)与(1.0,1.0)之间，如(0,0)(1.0,0)代表水平方向渐变,(0,0)(0,1.0)代表竖直方向渐变

### iOS通过js获取webview源代码
获取所有源代码：

```
NSString *JsToGetHTMLSource = @"document.getElementsByTagName('html')[0].innerHTML";    
NSString *HTMLSource = [webView stringByEvaluatingJavaScriptFromString:JsToGetHTMLSource];
NSLog(@"%@",HTMLSource);继承UITableViewController,更改tableview样式
```

获取页面的代码body的内容：

```
NSString *JsToGetHTMLSource = @"document.body.innerHTML"; 
NSString *pageSource = [webView stringByEvaluatingJavaScriptFromString:JsToGetHTMLSource];
NSLog(@"pagesource:%@", pageSource);
```

### 继承UITableViewController,更改tableview样式

```
- (instancetype)initWithStyle:(UITableViewStyle)style {
    return [super initWithStyle:UITableViewStylePlain];
}
```

### 获取当前最顶层的ViewController

实现方法

```
- (UIViewController *)topViewController {
    UIViewController *resultVC;
    resultVC = [self _topViewController:[[UIApplication sharedApplication].keyWindow rootViewController]];
    while (resultVC.presentedViewController) {
        resultVC = [self _topViewController:resultVC.presentedViewController];
    }
    return resultVC;
}

- (UIViewController *)_topViewController:(UIViewController *)vc {
    if ([vc isKindOfClass:[UINavigationController class]]) {
        return [self _topViewController:[(UINavigationController *)vc topViewController]];
    } else if ([vc isKindOfClass:[UITabBarController class]]) {
        return [self _topViewController:[(UITabBarController *)vc selectedViewController]];
    } else {
        return vc;
    }
    return nil;
}
```
使用方法

```
UIViewController *topmostVC = [self topViewController];
```

### 修改系统相册返回按钮颜色

```
 UIImagePickerController *picker = [[UIImagePickerController alloc] init];
 [picker.navigationBar setTintColor:[UIColor orangeColor]];
```

### iOS中isKindOfClass和isMemberOfClass的区别

相同点:
> 都是NSObject的比较Class的方法。

不同点:
>isKindOfClass:确定一个对象是否是一个类的成员，或者是派生自该类的成员。（确定一个对象是否是该类的实例，或者是该类子类的实例。）

>isMemberOfClass:确定一个对象是否是当前类的成员，<font color = #FA8072> 不可以用来确定 </font>一个对象是否是派生自该类的类的成员。

例如，如果想检测 myObject是不是UIImageView，用如下代码：

```
[myObject isKindOfClass:[UIImageView class]];
```

### 解决当前页面系统默认侧滑返回手势与页面上其他手势冲突

在viewDidLoad方法中添加该行代码:

```
[self.collectionView.panGestureRecognizer requireGestureRecognizerToFail:self.navigationController.interactivePopGestureRecognizer];
```
这行代码告诉collectionView手势识别需要等到导航栏侧滑返回手势失败，才能继续，强制侧滑手势优先级高于collectionView上的其他手势。

#### 参考文档
[Use interactivepopgesturerecognizer with CollectionView with horizontal scroll when navigation bar is hidden](https://stackoverflow.com/questions/28076473/use-interactivepopgesturerecognizer-with-collectionview-with-horizontal-scroll-w)

### 真机调试时设置动画的执行速度

运行项目并开启debugger,在调用动画的方法上添加断点，然后在控制台中输入如下命令:

```
po [(CALayer *)[[[[UIApplication sharedApplication] windows] objectAtIndex:0] layer] setSpeed:.1f]
```
#### 参考文档
[Toggle slow animation while debugging with iOS Device
](https://stackoverflow.com/questions/10229375/toggle-slow-animation-while-debugging-with-ios-device)

### 设置UITableView分割线

```
// 分割线颜色
[_listTableView setSeparatorColor:[UIColor colorWithHexString:historySeparatorColor]];
// 分割线位置
[_listTableView setSeparatorInset:UIEdgeInsetsMake(0, screenWidth * 194 / 375 / 2, 0, 0)];
// 分割线样式
self.tableView.separatorStyle = UITableViewCellSeparatorStyleNone;
```