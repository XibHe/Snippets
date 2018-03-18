# Snippets
记录一些奇妙的代码片段

### 数组求和
```objectivec
NSMutableArray *tempTotalPriceArray = [NSMutableArray array];
float tempAllPrices = [[_tempTotalPriceArray valueForKeyPath:@"@sum.floatValue"] floatValue];
```
### NSCharacterSet
根据NSCharacterSet匹配到的字符，分割字符串

```objectivec
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

```objectivec
    CGFloat x = ceilf(percentX * tabFrame.size.width);
    CGFloat y = ceilf(0.1 * tabFrame.size.height);
```

### 基于UINavigationController + UIViewController自定义的UITabBarController切换控件

如标题所说，在自定义UITabBarController后，需求需要根据不同的情况，切换不同的UIViewController。但是，在初始化UITabBarController时，已经将需要切换的5个UIViewController，addChildViewController到了自定义UITabBarController中。这是就需要使用，

```objectivec
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

```objectivec
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

```objectivec
- (CGRect)boundingRectWithSize:(CGSize)size options:(NSStringDrawingOptions)options attributes:(NSDictionary *)attributes context:(NSStringDrawingContext *)context；
```

```objectivec
CGSize infoSize = CGSizeMake(tableView.frame.size.width, 1000);
CGSize boundingRectWithSize = [_leftPrice.text boundingRectWithSize: infoSize options:NSStringDrawingUsesLineFragmentOrigin | NSStringDrawingUsesFontLeading  attributes:@{NSFontAttributeName:kFont(11)} context:nil].size;
CLog(@"boundingRectWithSize = %@",NSStringFromCGSize(boundingRectWithSize));
```
1. 参数1: 自适应尺寸,提供一个宽度,去自适应高度
2. 参数2:自适应设置 (以行为矩形区域自适应,以字体字形自适应)
3. 参数3:文字属性,通常这里面需要知道是字体大小
4. 参数4:绘制文本上下文,做底层排版时使用,填nil即可

### 使用CAGradientLayer实现颜色渐变

```objectivec
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

```objectivec
NSString *JsToGetHTMLSource = @"document.getElementsByTagName('html')[0].innerHTML";    
NSString *HTMLSource = [webView stringByEvaluatingJavaScriptFromString:JsToGetHTMLSource];
NSLog(@"%@",HTMLSource);继承UITableViewController,更改tableview样式
```

获取页面的代码body的内容：

```objectivec
NSString *JsToGetHTMLSource = @"document.body.innerHTML"; 
NSString *pageSource = [webView stringByEvaluatingJavaScriptFromString:JsToGetHTMLSource];
NSLog(@"pagesource:%@", pageSource);
```

### 继承UITableViewController,更改tableview样式

```objectivec
- (instancetype)initWithStyle:(UITableViewStyle)style {
    return [super initWithStyle:UITableViewStylePlain];
}
```

### 获取当前最顶层的ViewController

实现方法

```objectivec
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

```objectivec
UIViewController *topmostVC = [self topViewController];
```

### 修改系统相册返回按钮颜色

```objectivec
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

```objectivec
[myObject isKindOfClass:[UIImageView class]];
```

### 解决当前页面系统默认侧滑返回手势与页面上其他手势冲突

在viewDidLoad方法中添加该行代码:

```objectivec
[self.collectionView.panGestureRecognizer requireGestureRecognizerToFail:self.navigationController.interactivePopGestureRecognizer];
```
这行代码告诉collectionView手势识别需要等到导航栏侧滑返回手势失败，才能继续，强制侧滑手势优先级高于collectionView上的其他手势。

#### 参考文档
[Use interactivepopgesturerecognizer with CollectionView with horizontal scroll when navigation bar is hidden](https://stackoverflow.com/questions/28076473/use-interactivepopgesturerecognizer-with-collectionview-with-horizontal-scroll-w)

### 真机调试时设置动画的执行速度

运行项目并开启debugger,在调用动画的方法上添加断点，然后在控制台中输入如下命令:

```objectivec
po [(CALayer *)[[[[UIApplication sharedApplication] windows] objectAtIndex:0] layer] setSpeed:.1f]
```
#### 参考文档
[Toggle slow animation while debugging with iOS Device
](https://stackoverflow.com/questions/10229375/toggle-slow-animation-while-debugging-with-ios-device)

### 设置UITableView分割线

```objectivec
// 分割线颜色
[_listTableView setSeparatorColor:[UIColor colorWithHexString:historySeparatorColor]];
// 分割线位置
[_listTableView setSeparatorInset:UIEdgeInsetsMake(0, screenWidth * 194 / 375 / 2, 0, 0)];
// 分割线样式
self.tableView.separatorStyle = UITableViewCellSeparatorStyleNone;
```

### 解决placeholder不居中的问题
创建一个继承于UITextField的新类，这里命名为CustomTextField,重写UITextField的一些函数来达到我们想要的效果。目前只有通过这种方式才能很准确的调整placeholder的位置。

需要重写的两个函数:

```objectivec
@implementation CustomTextField
// 返回placeholderLabel的bounds，改变返回值，是调整placeholderLabel的位置
- (CGRect)placeholderRectForBounds:(CGRect)bounds {
    return CGRectMake(0,0,self.bounds.size.width, self.bounds.size.height);
}
// 这个函数是调整placeholder在placeholderLabel中绘制的位置以及范围
- (void)drawPlaceholderInRect:(CGRect)rect {
    [super drawPlaceholderInRect:CGRectMake(0,0, self.bounds.size.width, self.bounds.size.height)];
}
@end
```

> 注意：被重写函数中的CGRectMake()的值是需要根据你自己的需求进行调整的，并不是固定值。其实不难发现通过这种方式我们可以调整placeholder到任意位置，不单单是居中效果。

#### 参考文档
[iOS 深挖placeholder设置](http://www.jianshu.com/p/b6b78f39b5e4)

### UITextField的UIControlEventEditingChanged事件执行两次的问题

```objectivec
[searchText addTarget:self action:@selector(textContentChanged:) forControlEvents:UIControlEventEditingChanged];

-(void)textContentChanged:(UITextField*)textFiled
{
NSLog( @"text changed111: %@", textFiled.text);
UITextRange *selectedRange = [textFiled markedTextRange];
if(selectedRange == nil || selectedRange.empty){
NSLog( @"text changed222: %@", textFiled.text);
 }
}
```
执行结果，text changed111会输出两次，text changed222只输出一次。selectedRange是获取你输出的部分。

### NSMutableArray-Add array at start

```objectivec
NSIndexSet *indexes = [NSIndexSet indexSetWithIndexesInRange:NSMakeRange(0,[newArray count])];
[oldArray insertObjects:newArray atIndexes:indexes];
```
#### 参考文档
[NSMutableArray-Add array at start](https://stackoverflow.com/questions/22492706/nsmutablearray-add-array-at-start)

**2017-11-28**
### iOS开发之夏令时,NSDateFormatter格式化失败
把1988-04-10类似的时间字符串使用NSDateFormatter转化成时间戳或者NSDate
测试发现iOS格式化某些特殊时间字符串到NSDate的时候会后会返回空值,比如 1988-04-10,1989-04-16,1989-04-16 12:12等时间。

```objectivec

  // 方案一:
    NSDateFormatter *formatter = [[NSDateFormatter alloc] init];
    formatter.dateFormat = @"yyyy-MM-dd";
    formatter.lenient = YES;
    [formatter setLocale:[NSLocale currentLocale]];
    NSDate *date = [formatter dateFromString:@"1988-04-10"];
    CLog(@"date = %@",date);
    date = 1988-04-09 16:00:00 +0000
 
   方案二: 
    //设置转换后的目标日期时区
    NSTimeZone *toTimeZone = [NSTimeZone localTimeZone];
    //转换后源日期与世界标准时间的偏移量
    NSInteger toGMTOffset = [toTimeZone secondsFromGMTForDate:[NSDate date]];
    NSDateFormatter *dateFormatter = [[NSDateFormatter alloc] init];
    dateFormatter.dateFormat = @"yyyy-MM-dd";
    [dateFormatter setTimeZone:[NSTimeZone timeZoneForSecondsFromGMT:toGMTOffset]];
    NSDate *date0 = [dateFormatter dateFromString:@"1988-04-10"];
    NSLog(@"timeZoneForSecondsFromGMT:%@", date0);
    
    //timeZoneForSecondsFromGMT:Sun Apr 10 01:00:00 1988
```
#### 参考资料 
[iOS开发之夏令时,NSDateFormatter格式化失败](http://www.skyfox.org/ios-formatter-daylight-saving-time.html)
[NSDate中夏令时的坑](http://blog.csdn.net/hanhailong18/article/details/78300257)

**2017-12-20**

### NSArray的方法reverseObjectEnumerator

按照索引号从大到小访问数组的元素，而不是从小到大访问数组的元素。

**2018-01-09**

### 文字加投影

```objectivec
    UIButton *startBtn = [UIButton buttonWithType:UIButtonTypeCustom];
    [startBtn setTitle:@"我要开通" forState:UIControlStateNormal];
    startBtn.titleLabel.shadowOffset = CGSizeMake(0, 2);
    [startBtn setTitleShadowColor:[GlobalMethod hexStringToColor:@"#29a9cc"] forState:UIControlStateNormal];

```

**2018-01-21**
### iOS 11.1 使用MJRefresh上拉加载更多时，多次调用上拉加载更多的方法

> iOS11 之后 selfSizzing 默认打开导致

// 添加以下代码

``` objectivec
self.tableView.estimatedRowHeight =0;
self.tableView.estimatedSectionHeaderHeight =0;
self.tableView.estimatedSectionFooterHeight =0;
```

参考: [MJRefresh Issues #1071](https://github.com/CoderMJLee/MJRefresh/issues/1071
) 

**2018-03-16**
### 小数向上，向下取整
小数向上取整，指小数部分直接进1， x=3.14，ceilf(x)=4

```objectivec
NSLog(@"%d",  (int)ceil(10 / 3.0));  //结果是4
```

小数向下取整，指直接去掉小数部分 x=3.14，floor(x)=3

```objectivec
NSLog(@"%d", (int) ceil(10 / 3));  //结果是3
```

**ceil(x)** 返回不小于x的最小整数值，然后转换为double型。

### Layer的两个属性，positon 和 anchorPoint(锚点)

```objectivec
@property CGPoint position;
```

> 用来设置CALayer在父层中的位置，以父层的左上角为原点(0, 0)

```objectivec
@property CGPoint anchorPoint;
```

> 称为“定位点”、“锚点”
决定着CALayer身上的哪个点会在position属性所指的位置，以自己的左上角为原点(0, 0)，它的x、y取值范围都是0~1，默认值为（0.5, 0.5）

示例，

![](https://upload-images.jianshu.io/upload_images/1574564-73850a05ad25828c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/611)

* 假设紫色图层的position是（50，50）
* 紫色图层的anchorPoint(锚点)位置是(0.5,0.5)
* 图一是原始图 
* 图二是即将移动的图
* 图三是移动完成的图

> 紫色图层自身的 **anchorPoint(锚点)** 必须和自身的 **position** 重合

参考: [Layer两个属性position和anchorPoint](https://www.jianshu.com/p/7703e6fc6191) 

**2018-03-18**
### 浮点数向上取整及其精确计算

这里的需求是计算手续费时，保留小数点后两位。并且当最后一位小数大于 **0** 时，要自动向前进一位。例如，**0.112** 最后一位小数是 **2**，向前自动进一位是 **0.12**。

这里最初是手写一个进位的算法，截取字符串去判断，不仅计算麻烦，影响效率，而且存在误差。与其一直去缝缝补补，还不如用系统提供给的 **API**。

最后，代码如下，

```objectivec

NSDecimalNumberHandler *roundUp = [NSDecimalNumberHandler
                                               decimalNumberHandlerWithRoundingMode:NSRoundUp
                                               scale:2
                                               raiseOnExactness:NO
                                               raiseOnOverflow:NO
                                               raiseOnUnderflow:NO
                                               raiseOnDivideByZero:YES];
            
NSDecimalNumber *a = [NSDecimalNumber decimalNumberWithString:_currentTF.text];
NSDecimalNumber *b = [NSDecimalNumber decimalNumberWithString:_billCharge];
NSDecimalNumber *multiply = [a decimalNumberByMultiplyingBy:b withBehavior:roundUp];
float billCharge = [multiply floatValue];
            
// 手续费最低为 0.10
if (billCharge <= 0.10) {
   billCharge = 0.10;
}
```

参考: [iOS 浮点数的精确计算和四舍五入问题](https://www.jianshu.com/p/946c4c4aff33) 









