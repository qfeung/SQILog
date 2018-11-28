## RN中import的写法


```
// ReactNative代码中，经常会发现这种写法
import React, { Component } from 'react';
```

上面的写法等同于如下写法：

```
// 导出 && 命名默认的`export`
import React from 'react';
// 导出指定名字的`export`
import { Component } from 'react';
```
---
作为一般规则，大多数模块将提供单个默认导出或命名导出列表。 模块同时提供默认导出和命名导出的情况有点不常见。 但是，如果有一个最常导入的功能，还有其他子功能，则将第一个导出为默认值，将其余的导出为命名导出是有效的设计。

---

参考链接: [using brackets with javascript import syntax](https://stackoverflow.com/questions/31096597/using-brackets-with-javascript-import-syntax)
