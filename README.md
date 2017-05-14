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
