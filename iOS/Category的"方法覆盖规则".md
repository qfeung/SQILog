## Category的"方法覆盖规则"

### 问题描述

#### 有一个类`MyClass`.

```
// MyClass.h文件
@interface MyClass : NSObject

+ (void)sqi_print;

@end

// MyClass.m文件
@implementation MyClass

+ (void)sqi_print {
    NSLog(@"-> MyClass");
}

@end
```

#### 新建一个 MyClass 的分类`SQI_CAT_ONE`

```
// MyClass+SQI_CAT_ONE.h文件
@interface MyClass (SQI_CAT_ONE)

+ (void)sqi_print;

@end

// MyClass+SQI_CAT_ONE.m文件
@implementation MyClass (SQI_CAT_ONE)

+ (void)sqi_print {
    NSLog(@"-> MyClass (SQI_CAT_ONE)");
}

@end
```

#### 再新建另一个 MyClass 的分类`SQI_CAT_TWO`

```
// MyClass+SQI_CAT_TWO.h文件
@interface MyClass (SQI_CAT_TWO)

+ (void)sqi_print;

@end

// MyClass+SQI_CAT_TWO.m文件
@implementation MyClass (SQI_CAT_TWO)

+ (void)sqi_print {
    NSLog(@"-> MyClass (SQI_CAT_TWO)");
}

@end
```

#### 在 ViewController 中引入两个分类

```
#import "ViewController.h"
#import "MyClass+SQI_CAT_TWO.h"
#import "MyClass+SQI_CAT_ONE.h"

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    // Do any additional setup after loading the view.
    [MyClass sqi_print];
}

@end
```

### 控制台打印结果

#### 情况1 -> MyClass+SQI_CAT_TWO.m文件后编译

![](https://github.com/qfeung/SQIMaterials/blob/master/Markdown/Category%E7%9A%84%22%E6%96%B9%E6%B3%95%E8%A6%86%E7%9B%96%E8%A7%84%E5%88%99%22/SQI_CAT_ONE_IN_FRONT.png?raw=true)

#### 情况1 -> 控制台打印

![](https://github.com/qfeung/SQIMaterials/blob/master/Markdown/Category%E7%9A%84%22%E6%96%B9%E6%B3%95%E8%A6%86%E7%9B%96%E8%A7%84%E5%88%99%22/%E6%89%93%E5%8D%B0SQI_CAT_TWO.png?raw=true)

#### 情况2 -> MyClass+SQI_CAT_ONE.m文件后编译

![](https://github.com/qfeung/SQIMaterials/blob/master/Markdown/Category%E7%9A%84%22%E6%96%B9%E6%B3%95%E8%A6%86%E7%9B%96%E8%A7%84%E5%88%99%22/SQI_CAT_ONE_AT_THE_BACK.png?raw=true)

#### 情况2 -> 控制台打印

![](https://github.com/qfeung/SQIMaterials/blob/master/Markdown/Category%E7%9A%84%22%E6%96%B9%E6%B3%95%E8%A6%86%E7%9B%96%E8%A7%84%E5%88%99%22/%E6%89%93%E5%8D%B0SQI_CAT_ONE.png?raw=true)

### 分类覆盖规则分析

1. App的启动的时候(load方法之前), 会把分类中的`类方法列表`和原类中的`类方法列表`拼接成一个新的`类方法列表`, 而且分类中的方法会放在新列表的前面.
2. 具体是哪一个分类中的方法处于更前面, 这取决于编译顺序.
3. 运行时在查找方法的时候是顺着方法列表的顺序查找的，它只要一找到对应名字的方法，就会罢休^_^，殊不知后面可能还有一样名字的方法。