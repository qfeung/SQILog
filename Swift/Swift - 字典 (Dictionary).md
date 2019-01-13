## 字典 (Dictionary)

> 1. 存储同类型值的容器;
> 2. 键(key)是字典中的这个值数据(value)的唯一标识符;
> 3. 目前 Swift 的 Dictionary 类型被桥接到 Foundation 的 NSDictionary 类;
> 4. 一个字典的 Key 类型必须遵循 Hashable 协议，就像 Set 的值类型;

### 创建

```
// 1. 创建一个可变的空字典
var dictName = [Int: String]()
// 2. 使用字典字面量创建(: [String: String]可省略)
var dictName: [String: String] = ["key1": "Value1", "key2": "Value2"]
```

### 添加

```
// 1. 使用下标语法
dictName["newKey"] = "newValue"
// 2. 使用特定方法
updateValue(_:forKey:)
```

### 删除

```
// 1. 使用下表语法
dictName["oldKey"] = nil
// 2. 使用特定的方法
removeValue(forKey:)
```

### 修改

```
// 1. 使用下标语法
dictName["oldKey"] = "newValue"
// 2. 使用特定方法
updateValue(_:forKey:)
```

### 遍历

```
/*
 * 1. 遍历键值对
 * 我们可以使用 for-in 循环来遍历某个字典中的键值对。每一个字典中的数据项都以 (key, value) 元组形式返回，并且我们可以使用临时常量或者变量来分解这些元组
 */
 for (keyName, valueName) in dictName {
    print("\(keyName): \(valueName)")
}

/*
 * 2. 遍历字典的键或者值
 * 通过访问 keys 或者 values 属性
 */
 for keyDesc in dictName.keys {
    print("Key: \(keyDesc)")
}

for valueDesc in dictName.values {
    print("Value: \(valueDesc)")
}
```

### Others

```
// 1. 通过字典的只读属性 count 来获取某个字典的数据项数量
dictName.count
// 2. 布尔属性 isEmpty 检查 count 是否为0
dictName.isEmpty
// 3. Swift 的字典类型是无序集合类型。为了以特定的顺序遍历字典的键或值，可以对字典的 keys 或 values 属性使用 sorted() 方法
```

