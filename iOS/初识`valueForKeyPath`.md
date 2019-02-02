## 初识`valueForKeyPath:`

1. 官方文档:

```
// 返回由给定键路径标识的派生属性的值。
Returns the value for the derived property identified by a given key path.
```

2. 完整声明:

```
- (id)valueForKeyPath:(NSString *)keyPath;
```

3. 参数说明

```
// keyPath参数
// 大意: `relationship.property`这种形式的`键路径`(relationships可能有多个); 例如:"department.name"或者"department.manager.lastName".
A key path of the form relationship.property (with one or more relationships); for example “department.name” or “department.manager.lastName”.
```

4. 内部默认实现
对每一个`relationship`, 逐次调用`valueForKey:`.
![valueForKey:的默认实现](https://img-blog.csdnimg.cn/20190202163346520.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FmZXVuZw==,size_16,color_FFFFFF,t_70)
