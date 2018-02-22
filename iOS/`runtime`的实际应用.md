# `runtime`的实际应用

```
你在项目中用过 runtime 吗？举个例子
```

## `runtime`的概念

runtime 是 OC底层的一套C语言的API（引入 <objc/runtime.h> 或<objc/message.h>）, 编译器最终都会将OC代码转化为运行时代码，通过终端命令编译.m 文件：clang -rewrite-objc xxx.m可以看到编译后的xxx.cpp（C++文件）.

## `runtime`的实际应用

### 动态交换两个方法的实现 - method swizzling

我们想要在一款 iOS app 中追踪每一个视图控制器被用户呈现了几次： 这可以通过在每个视图控制器的 viewDidAppear: 方法中添加追踪代码来实现，但这样会大量重复的样板代码。继承是另一种可行的方式，但是这要求所有被继承的视图控制器如 UIViewController, UITableViewController, UINavigationController 都在 viewDidAppear：实现追踪代码，这同样会造成很多重复代码。 幸运的是，这里有另外一种可行的方式：从 category 实现 method swizzling. 

#### 核心API

```
// 获得某个类的类方法
Method class_getClassMethod(Class cls , SEL name);

// 获取某个类的实例方法
Method class_getInstanceMethod(Class cls , SEL name);

// 交换两个方法的实现
void method_exchangeImplementations(Method m1, Method m2);
```

#### 实例代码

```
#import <objc/runtime.h>

@implementation UIViewController (Tracking)

+ (void)load {
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        Class class = [self class];

        SEL originalSelector = @selector(viewWillAppear:);
        SEL swizzledSelector = @selector(xxx_viewWillAppear:);

        Method originalMethod = class_getInstanceMethod(class, originalSelector);
        Method swizzledMethod = class_getInstanceMethod(class, swizzledSelector);

        // When swizzling a class method, use the following:
        // Class class = object_getClass((id)self);
        // ...
        // Method originalMethod = class_getClassMethod(class, originalSelector);
        // Method swizzledMethod = class_getClassMethod(class, swizzledSelector);

        BOOL didAddMethod =
            class_addMethod(class,
                originalSelector,
                method_getImplementation(swizzledMethod),
                method_getTypeEncoding(swizzledMethod));

        if (didAddMethod) {
            class_replaceMethod(class,
                swizzledSelector,
                method_getImplementation(originalMethod),
                method_getTypeEncoding(originalMethod));
        } else {
            method_exchangeImplementations(originalMethod, swizzledMethod);
        }
    });
}

#pragma mark - Method Swizzling

- (void)xxx_viewWillAppear:(BOOL)animated {
    [self xxx_viewWillAppear:animated];
    NSLog(@"viewWillAppear: %@", self);
}

@end
```

---

### 为分类添加属性

众所周知，分类中是无法设置属性的，如果在分类的声明中写@property 只能为其生成get 和 set 方法的声明，但无法生成成员变量，就是虽然点语法能调用出来，但程序执行后会crash.

#### 核心API

```
// 设置动态关联对象
void objc_setAssociatedObject(id object , const void *key ,id value ,objc_AssociationPolicy policy);
// 获取动态关联对象
id objc_getAssociatedObject(id object , const void *key);
```

#### 实例代码

```
@interface UIView (SQIAdditions)

@property(nonatomic, strong) NSArray <void (^)(void)>*sqi_handleBlockArr;

@end

@implementation UIView (SQIAdditions)

static char *HandleBlockArrKey = "HandleBlockArrKey";


- (void)setSqi_handleBlockArr:(NSArray<void (^)(void)> *)handleBlockArr {
    objc_setAssociatedObject(self, HandleBlockArrKey, handleBlockArr, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
}

- (NSArray<void (^)(void)> *)sqi_handleBlockArr {
    return objc_getAssociatedObject(self, HandleBlockArrKey);
}

@end

```

### 自定义对象的归档和解档

自定义对象如果想实现归档和解档, 必须遵守`NSCoding`协议, 必须实现encodeWithCoder，initWithCoder的两个方法告诉系统，你的对象里面到底有那些数据.

#### 核心API

```
// 获取某个类的所有成员变量
Ivar  _Nonnull * class_copyIvarList(Class cls, unsigned int *outCount);
// 获取成员变量的名字
const char *ivar_getName(Ivar v);
// 获取成员变量的类型
const char *ivar_getTypeEndcoding(Ivar v);
```

#### 实例代码

* 获取Person类中所有成员变量的名字和类型

```
unsigned int outCount = 0;
Ivar *ivars = class_copyIvarList([Person class], &outCount);

// 遍历所有成员变量
for (int i = 0; i < outCount; i++) {
    // 取出i位置对应的成员变量
    Ivar ivar = ivars[i];
    const char *name = ivar_getName(ivar);
    const char *type = ivar_getTypeEncoding(ivar);
    NSLog(@"成员变量名：%s 成员变量类型：%s",name,type);
}
// 注意释放内存！
free(ivars);
```

* 利用runtime 获取所有属性来重写归档解档方法

```
// 设置不需要归解档的属性
- (NSArray *)ignoredNames {
    return @[@"_aaa",@"_bbb",@"_ccc"];
}

// 解档方法
- (instancetype)initWithCoder:(NSCoder *)aDecoder {
    if (self = [super initWithCoder:aDecoder]) {
        // 获取所有成员变量
        unsigned int outCount = 0;
        Ivar *ivars = class_copyIvarList([self class], &outCount);
        
        for (int i = 0; i < outCount; i++) {
            Ivar ivar = ivars[i];
            // 将每个成员变量名转换为NSString对象类型
            NSString *key = [NSString stringWithUTF8String:ivar_getName(ivar)];
            
            // 忽略不需要解档的属性
            if ([[self ignoredNames] containsObject:key]) {
                continue;
            }
            
            // 根据变量名解档取值，无论是什么类型
            id value = [aDecoder decodeObjectForKey:key];
            // 取出的值再设置给属性
            [self setValue:value forKey:key];
            // 这两步就相当于以前的 self.age = [aDecoder decodeObjectForKey:@"_age"];
        }
        free(ivars);
    }
    return self;
}

// 归档调用方法
- (void)encodeWithCoder:(NSCoder *)aCoder {
     // 获取所有成员变量
    unsigned int outCount = 0;
    Ivar *ivars = class_copyIvarList([self class], &outCount);
    for (int i = 0; i < outCount; i++) {
        Ivar ivar = ivars[i];
        // 将每个成员变量名转换为NSString对象类型
        NSString *key = [NSString stringWithUTF8String:ivar_getName(ivar)];
        
        // 忽略不需要归档的属性
        if ([[self ignoredNames] containsObject:key]) {
            continue;
        }
        
        // 通过成员变量名，取出成员变量的值
        id value = [self valueForKeyPath:key];
        // 再将值归档
        [aCoder encodeObject:value forKey:key];
        // 这两步就相当于 [aCoder encodeObject:@(self.age) forKey:@"_age"];
    }
    free(ivars);
}
```

依据上面的原理我们就可以给NSObject做一个分类，让我们不需要每次都写这么一长串代码，只要实现一小段代码就可以让一个对象具有归解档的能力。

#### 完整的分类代码

NSObject+SQIExtension.h

```
#import <Foundation/Foundation.h>

@interface NSObject (SQIExtension)

- (NSArray *)ignoredNames;
- (void)encode:(NSCoder *)aCoder;
- (void)decode:(NSCoder *)aDecoder;

@end
```

NSObject+SQIExtension.m

```
#import "NSObject+SQIExtension.h"
#import <objc/runtime.h>

@implementation NSObject (Extension)

- (void)decode:(NSCoder *)aDecoder {
    // 一层层父类往上查找，对父类的属性执行归解档方法
    Class c = self.class;
    while (c &&c != [NSObject class]) {
        
        unsigned int outCount = 0;
        Ivar *ivars = class_copyIvarList(c, &outCount);
        for (int i = 0; i < outCount; i++) {
            Ivar ivar = ivars[i];
            NSString *key = [NSString stringWithUTF8String:ivar_getName(ivar)];
            
            // 如果有实现该方法再去调用
            if ([self respondsToSelector:@selector(ignoredNames)]) {
                if ([[self ignoredNames] containsObject:key]) continue;
            }
            
            id value = [aDecoder decodeObjectForKey:key];
            [self setValue:value forKey:key];
        }
        free(ivars);
        c = [c superclass];
    }
    
}

- (void)encode:(NSCoder *)aCoder {
    // 一层层父类往上查找，对父类的属性执行归解档方法
    Class c = self.class;
    while (c &&c != [NSObject class]) {
        
        unsigned int outCount = 0;
        Ivar *ivars = class_copyIvarList([self class], &outCount);
        for (int i = 0; i < outCount; i++) {
            Ivar ivar = ivars[i];
            NSString *key = [NSString stringWithUTF8String:ivar_getName(ivar)];
            
            // 如果有实现该方法再去调用
            if ([self respondsToSelector:@selector(ignoredNames)]) {
                if ([[self ignoredNames] containsObject:key]) continue;
            }
            
            id value = [self valueForKeyPath:key];
            [aCoder encodeObject:value forKey:key];
        }
        free(ivars);
        c = [c superclass];
    }
}
@end
```

#### 分类的使用方法

在需要归解档的对象中实现下面方法即可：

```
// 设置需要忽略的属性
- (NSArray *)ignoredNames {
    return @[@"bone"];
}

// 在系统方法内来调用我们的方法
- (instancetype)initWithCoder:(NSCoder *)aDecoder {
    if (self = [super init]) {
        [self decode:aDecoder];
    }
    return self;
}

- (void)encodeWithCoder:(NSCoder *)aCoder {
    [self encode:aCoder];
}
```

这样看来，我们每次又要写同样的代码，我们可以将归解档两个方法封装为宏，在需要的地方一句宏搞定，如果有不需要归解档的属性就实现ignoredNames 方法，具体可以看我的demo，这个也是MJExtension中那个一句宏就可以解决归解档的实现原理。
