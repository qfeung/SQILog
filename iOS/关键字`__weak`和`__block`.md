#关键字`__weak`和`__block`

```
1. 同数据变量修饰符, 用于修饰变量(variable).
2. __weak 和 __block 的使用场景几乎与 block 息息相关. 而所谓 block, 就是 Objective-C 对于闭包的实现. 闭包就是没有名字的函数, 或者理解为指向函数的指针.
```
##`__weak`关键字

1. 主要用于防止 block 造成循环引用, 特别是 block 对 self 的引用.
	
	```
	__weak typeof(self) weakSelf = self;
	```
	
2. 代表`弱引用`.
3. 只能在 ARC 模式下使用, 只能修饰对象类型, 不能修饰基本数据类型.

##`__block`关键字

1. 主要用于在 block 内部, 修改外部变量的值 & 对外部变量进行重新赋值.
	
	```
	block可以访问外部变量(局部变量)(实际上访问的不是同一个变量), 但是不能修改. 如果想要在`block`内部修改外部变量, 需要使用`__block`修饰外部变量, 所以说:`__block`的功能是让`block`能够修改外部变量的值.
	```

2. `block`对外部变量的处理
	* 如果block内部存在外部变量的引用, block 默认是将其复制到其数据结构中来实现访问的.
	
	![const_non_local_variables](https://github.com/qfeung/SQIMaterials/raw/master/Markdown/%E5%85%B3%E9%94%AE%E5%AD%97%60__weak%60%E5%92%8C%60__block%60/const_non_local_variables.png)
	
	* 如果外部变量有`__block`修饰, block 对其访问是通过复制其引用地址来实现的.

	![mutable-non-local-variables](https://github.com/qfeung/SQIMaterials/raw/master/Markdown/%E5%85%B3%E9%94%AE%E5%AD%97%60__weak%60%E5%92%8C%60__block%60/mutable-non-local-variables.png)
	
3. `__block`对象在`block`中不会被`block`强引用一次, 所以不会造成循环引用.
4. `__block`不管是 ARC 还是 MRC 模式下都可以使用, 可以修饰对象, 还可以修饰基本数据类型。

##Others

1. 在一篇博客中, 看到了下面这样一句话, 但我感觉不准确.

```
_____block对象可以在block中被重新赋值，_____weak不可以。
```

2. 在一篇大神的博客中, 看到了这样一句话, 到现在还是一脸懵逼, 感觉没什么逻辑性可言(吐槽一下, 哈哈).

![__block的大神解释](https://github.com/qfeung/SQIMaterials/raw/master/Markdown/%E5%85%B3%E9%94%AE%E5%AD%97%60__weak%60%E5%92%8C%60__block%60/__block%E7%9A%84%E5%A4%A7%E7%A5%9E%E8%A7%A3%E9%87%8A.png)

3. 如果想要深入了解`block 对外部变量的处理`的细节, 可以自己写一个简单的demo, 然后通过clang进行手动编译, 处理细节一览无余. 这里推荐一篇写的不错的博客:[你真的理解__block修饰符的原理么？](http://blog.csdn.net/abc649395594/article/details/47086751)


