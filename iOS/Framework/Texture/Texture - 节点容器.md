## 节点容器

### 在节点容器中使用节点

强烈推荐在节点容器中使用节点, Texture 提供了如下的节点容器.

| Texture 节点容器 | UIKit中的等价物 |
|---|---|
| ASCollectionNode | 代替 UIKit 中的 UICollectionView |
| ASPagerNode | 代替 UIKit 中的 UIPageViewController |
| ASTableNode | 代替 UIKit 中的 UITableView |
| ASViewController | 代替 UIKit 中的 UIViewController |
| ASNavigationController | 代替 UIKit 中的 UINavigationController, 实现了 [ASVisibility]() 协议 |
| ASTabBarController | 代替 UIKit 中的 UITabBarController, 实现了 [ASVisibility]() 协议 |

每个节点容器的文档中都突出显示了示例代码和特定示例项目。

### 使用节点容器可以获得什么？

节点容器自动管理其节点的[智能预加载]()。 这意味着所有节点的布局测量，数据获取，解码和渲染都将异步完成。 除了其他便利之外，这也是建议在容器节点内使用节点的原因。

请注意，尽管可以直接使用节点（没有Texture节点容器），但除非您添加其他调用，否则它们只会在屏幕上显示后开始显示（如UIKit所示）。 这可能导致性能下降和内容闪烁。