# 键值编码(KVC / Key-Value Coding) (上)

## Overview

1. KVC是通过对`NSObject`进行扩展来实现的.
2. Objective-C中有个显式的`NSKeyValueCoding`的类别, 而Swift没有, 也不需要. 所以对于所有继承了NSObject的类型, 也就是几乎所有的`Objective-C对象`都能使用KVC (一些纯Swift类和结构体是不支持KVC的).

## The most important four methods in KVC

```
// 直接通过Key来取值
- (nullable id)valueForKey:(NSString *)key;  
// 直接通过Key来设值                        
- (void)setValue:(nullable id)value forKey:(NSString *)key;
// 通过KeyPath来取值          
- (nullable id)valueForKeyPath:(NSString *)keyPath;  
// 通过KeyPath来设值                
- (void)setValue:(nullable id)value forKeyPath:(NSString *)keyPath;  
```

## Other methods

```
// 默认返回YES，表示如果没有找到Set<Key>方法的话，会按照_key，_iskey，key，iskey的顺序搜索成员，设置成NO就不这样搜索
+ (BOOL)accessInstanceVariablesDirectly;

// KVC提供属性值正确性验证的API，它可以用来检查set的值是否正确、为不正确的值做一个替换值或者拒绝设置新值并返回错误原因。
- (BOOL)validateValue:(inout id __nullable * __nonnull)ioValue forKey:(NSString *)inKey error:(out NSError **)outError;

// 这是集合操作的API，里面还有一系列这样的API，如果属性是一个NSMutableArray，那么可以用这个方法来返回。
- (NSMutableArray *)mutableArrayValueForKey:(NSString *)key;

// 如果Key不存在，且无法搜索到任何和Key有关的字段或者属性，则会调用这个方法，默认是抛出异常。
- (nullable id)valueForUndefinedKey:(NSString *)key;

// 和上一个方法一样，但这个方法是设值。
- (void)setValue:(nullable id)value forUndefinedKey:(NSString *)key;

// 如果你在SetValue方法时面给Value传nil，则会调用这个方法
- (void)setNilValueForKey:(NSString *)key;

// 输入一组key, 返key-value字典, 用于Model转字典.
- (NSDictionary<NSString *, id> *)dictionaryWithValuesForKeys:(NSArray<NSString *> *)keys;
```

苹果对一些容器类比如NSArray或者NSSet等，KVC有着特殊的实现。建议有基础的或者英文好的开发者直接去看苹果的官方文档，相信你会对KVC的理解更上一个台阶.

---

## KVC是怎么寻找Key的

### 设值优先级

1. 尝试通过`setKey:`方法(setter方法)进行设值.
2. 若没有找到`setKey:`方法，KVC机制会检查`+(BOOL)accessInstanceVariablesDirectly`方法的返回值(默认为Yes), 顾名思义, 该方法是询问是否直接访问带有下划线的实例变量.
3. 如果你重写了该方法让其返回NO的话，那么此时KVC会执行`setValue:forUndefinedKey:`方法.
4. 若`+(BOOL)accessInstanceVariablesDirectly`方法返回Yes, 则KVC机制会搜索该类里面有没有名为`_key`的成员变量，无论该变量是在类接口处定义，还是在类实现处定义，也无论用了什么样的访问修饰符，只在存在以_key命名的变量，KVC都可以对该成员变量赋值.
5. 若`_key`成员变量没有找到, 则KVC会按先后顺序查找`_isKey`, `key`, `isKey`成员变量.
6. 若以上都没有找到, 则最终调用`setValue:forUndefinedKey:`方法(默认实现 -> 抛出异常).

### 禁用KVC设值

*如果开发者想让这个类禁用KVC里，那么重写`+ (BOOL)accessInstanceVariablesDirectly`方法让其返回NO即可，这样的话如果KVC没有找到set<Key>:属性名时，会直接用setValue：forUndefinedKey：方法。*

### KVC取值策略

1. 按顺序查找`getter`方法, `- getKey`, `- key`, `- isKey`, 若返回 BOOL 或者 Int 等值类型, 会将其包装成一个 NSNumber 对象.
2. 若没找到, 进行后续复杂处理...😂

## KVC中的键路径 - `keyPath`

用来简化KVC基本方法的嵌套调用, 主要用于深层属性值的动态获取和赋值.

## KVC的处理异常

### 没有找到对应的key

```
// 赋值异常
setValue:forUndefinedKey:
```

### Value值为nil

```
// 为了避免Crash, 建议复写该方法
setNilValueForKey:
```

## 关于`非OC对象`和自定义对象的注意点

### 非OC对象

1. KVC在动态取值的时候, 遇到值类型, 会自动封装成NSNumber对象, 遇到结构体类型, 会自动封装成NSValue对象.
2. KVC在动态赋值的时候, 强制不允许传递非对象类型的value.

### 自定义对象

API传递进去和取出来的都是id类型，所以需要开发者自己担保类型的正确性，运行时Objective-C在发送消息的会检查类型，如果错误会直接抛出异常。



