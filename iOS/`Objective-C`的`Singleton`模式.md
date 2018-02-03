# `Objective-C`的`Singleton`模式

## Overview

### Singleton 模式的定义

```
// 确保一个类有且仅有唯一一个单例, 并且提供一个全局的访问点.
Ensures a class has only one instance, and provide a global point of access.
```

### Objective-C 开发
Singleton 模式是 iOS 开发中非常有用的设计模式, 可用于对象之间方便地共享(或传递)数据. 相对于对象之间手动传递数据, Singleton 模式更加的方便直接, 不需要对象之间有任何的联系.

---

## Implementation

### 技术要点

1. 仅有一个对象 -> 用一个全局的东西保存这个对象.
2. 全局的访问点 -> 工厂方法创建对象.
3. 线程安全 -> `dispatch_once`

### Show code

```

+ (SQISingleton *)shareInstance {

    static SQISingleton * s_instance_sqi_singleton = nil ;
    
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        if (s_instance_sqi_manager == nil) {
            s_instance_sqi_manager = [[SQISingleton alloc] init];
        }
    });
    return (SQISingleton *)s_instance_sqi_singleton;
}

```

### 关于`static`关键字 & `extern`关键字

1. static关键字修饰局部变量：①. 当static关键字修饰局部变量时，该局部变量只会初始化一次，在系统中只有一份内存; ②. static关键字不可以改变局部变量的作用域，但是可延长局部变量的生命周期，该变量直到整个项目结束的时候才会被销毁

2. static修饰的全局变量：作用域仅限于当前文件，外部类不可以访问到该变量

3. `extern-引用关键字`，当某一个全局变量，没有用static修饰时，其作用域为整个项目文件，若是在其他类想引用该变量，则用extern关键字，例如，想引用其他类的全局变量，int age = 10；则在当前类中实现，extern int age；也可以在外部修改该变量，extern int age = 40；，若某个文件中的全局变量不想被外界修改，则用static修饰该变量，则其作用域只限于该文件.

---

## 代码鲁棒性

看起来很完美了。可是Objective-C毕竟是Objective-C。别的语言，诸如C++，java，构造方法可以隐藏。Objective-C中的方法，实际上都是公开的，虽然我们提供了一个方便的工厂方法的访问入口，但是里面的alloc方法依旧是可见的，可以调用到的。也就是说，虽然你给了我一个工厂方法，调皮的小伙伴可能依旧会使用alloc的方式创建对象。这样会导致外面使用的时候，依旧可能创建多个实例。

关于这个事情的处理，可以分为两派。一个是冷酷派，技术上实现无论你怎么调用，我都给你同一个单例对象；一个是温柔派，是从编译器上给调皮的小伙伴提示，你不能这么造对象，温柔的指出有问题，但不强制约束。

### 冷酷派的实现

冷酷派的实现从OC的对象创建角度出发，就是把创建对象的各种入口给封死了。alloc，copy等等，无论是采用哪种方式创建，我都保证给出的对象是同一个。

由 Objective-C 的一些特性可以知道，在对象创建的时候，无论是 alloc 还是 new ，都会调用到 `allocWithZone:`方法。在通过拷贝的时候创建对象时，会调用到`-(id)copyWithZone:(NSZone *)zone`，`-(id)mutableCopyWithZone:(NSZone *)zone`方法。因此，可以重写这些方法，让创建的对象唯一.

```
+(id)allocWithZone:(NSZone *)zone{
    return [SQISingleton sharedInstance];
}
+(SQISingleton *) sharedInstance{
    static SQISingleton * s_instance_sqi_singleton = nil;
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        s_instance_sqi_singleton = [[super allocWithZone:nil] init];
    });
    return s_instance_sqi_singleton;
}
-(id)copyWithZone:(NSZone *)zone{
    return [SQISingleton sharedInstance];
}
-(id)mutableCopyWithZone:(NSZone *)zone{
    return [SQISingleton sharedInstance];
}
```

### 温柔派的实现

温柔派就直接告诉外面，alloc，new，copy，mutableCopy方法不可以直接调用。否则编译不过。

```
+(instancetype) alloc __attribute__((unavailable("call sharedInstance instead")));
+(instancetype) new __attribute__((unavailable("call sharedInstance instead")));
-(instancetype) copy __attribute__((unavailable("call sharedInstance instead")));
-(instancetype) mutableCopy __attribute__((unavailable("call sharedInstance instead")));
```

我个人的话比较喜欢采用温柔派的实现。不需要这么多复杂的实现。也让使用方有比较明确的概念这个是个单例，不要调皮。对于一般的业务场景是足够了的。

## 一劳永逸的方法

大神们把单例模式的各种套路封装成了宏。这样使用的时候，就不需要每个类都手动写一遍里面的重复代码了。省去了敲代码的时间。

以温柔派的为例，大概是这样子的。

```
// 定义
#define SQI_SINGLETON_DEF(_type_) + (_type_ *)sharedInstance;\
+(instancetype) alloc __attribute__((unavailable("call sharedInstance instead")));\
+(instancetype) new __attribute__((unavailable("call sharedInstance instead")));\
-(instancetype) copy __attribute__((unavailable("call sharedInstance instead")));\
-(instancetype) mutableCopy __attribute__((unavailable("call sharedInstance instead")));\

// 实现
#define SQI_SINGLETON_IMP(_type_) + (_type_ *)sharedInstance{\
static _type_ *theSharedInstance = nil;\
static dispatch_once_t onceToken;\
dispatch_once(&onceToken, ^{\
theSharedInstance = [[super alloc] init];\
});\
return theSharedInstance;\
}
```

那么，在定义和实现的时候就很简单了：

```
@interface SQISingleton : NSObject
    SQI_SINGLETON_DEF(SQISingleton);
@end
@implementation SQISingleton
    SQI_SINGLETON_IMP(SQISingleton);
@end
```

## 单例模式潜在的问题

Singleton 模式延长了对象的生命周期, 若单例对象本身比较大, 或者强引用了比较大的对象, 持久的内存占用是一个不得不考虑的问题.
