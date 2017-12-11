目前为止, 在苹果的官方文档上, 已经找不到对这两个属性修饰符的介绍, 网络上对其细节说明也是千奇百怪, 各执一词.

## atomic(原子性)

1. 顾名思义, 表示`原子性`, 这个概念其实并不新鲜, 早在linux系统下编程本身也是有这个东西的, 所谓原子, 就是不可再化分, 已经是最小的操作单位(所谓操作指的是对内存的读写), 网上很多地方都在讨论 Objective-C 下的 atomic 不安全, 不能保证数据的并发性, 实际上有一点误导了大家, 认为 atomic 本身是不安全的. 实际上, 并非 atomic 不安全, 而是网上一些说法有问题.

2. `atomic`关键字会保证属性生成的成员变量在同一时间只能有一条线程对其进行`读/写操作`, 本质上是在 setter 方法和 getter 方法中添加了一个`锁`.

```
// @property(retain) UITextField *userName;
// 系统生成的代码如下：

- (UILabel *) titleLabel {
    UITextField *label = nil;
    @synchronized(self) {
        label = [[_titleLabel retain] autorelease];
    }
    return label;
}

- (void) setTitleLabel:(UILabel *)titleLabel {
    @synchronized(self) {
      [_titleLabel release];
      _titleLabel = [titleLabel retain];
    }
}
```
3. 这里需要说明的是 atomic 仅仅是保证了 setter 方法和 getter 方法的完整性, 确保任何线程在获取数据的时候, 都会得到一个完整的数据.

## nonatomic(非原子性)

1. 没有保证 getter 和 setter 存取方法的线程安全.

```
- (void)setCurrentImage:(UIImage *)currentImage
{
    if (_currentImage != currentImage) {
        [_currentImage release];
        _currentImage = [currentImage retain];
    }
}

- (UIImage *)currentImage
{
    return _currentImage;
}
```

## Others

1. atomic要比nonatomic慢大约20倍. 一般如果条件允许, 我们可以让服务器来进行加锁操作.
2. 虽然 atomic 保证了 setter & getter 的读写安全, 但不能保证其它方法造成的线程安全问题, 比如NSArry对象调用`addObject:`方法在 atomic 下是没有线程安全保证的.


