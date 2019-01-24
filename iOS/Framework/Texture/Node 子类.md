## Node 子类

Texture 提供了下面这些 nodes.

相比于 UIKit 组件, 使用 nodes 的一个关键的优势是: 所有 nodes 脱离主线程执行布局和显示, 这就使得主线程可以立即响应用户的交互事件.

| Texture Node | UIKit Equivalent |
|---|---|
| ASDisplayNode | 代替 UIKit 中的 UIView, Texture 中的根 node, 所有其它 node 继承与它 |
| ASCellNode | 代替 UIKit 中的 UITableViewCell 和 UICollectionViewCell ASCellNodes 用于 ASTableNode, ASCollectionNode 和 ASPagerNode |
| ASScrollNode | 代替 UIKit 中的 UIScrollView, 对于创建一个自定义的可滑动的, 包含其它 nodes 的区域, 这个 node 是非常有用的 |
| ASEditableTextNode | 代替 UIKit 中的 UITextView | 
| ASTextNode | 代替 UIKit 中的 UILabel |
|ASImageNode, ASNetworkImageNode, ASMultiplexImageNode| 代替 UIKit 中的 UIImage |
|ASVideoNode| 代替 UIKit 中的 AVPlayerLayer |
|ASVideoPlayerNode| 代替 UIKit 中的 UIMoviePlayer |
|ASControlNode| 代替 UIKit 中的 UIControl |
|ASButtonNode| 代替 UIKit 中的 UIButton |
|ASMapNode| 代替 UIKit 中的 MKMapView |

尽管与UIKit组件具有粗略的等效性，但通常，Texture节点提供更高级的功能和便利性。 例如，`ASNetworkImageNode`执行自动加载和缓存管理，甚至支持渐进式jpeg和GIF动画。

`AsyncDisplayKitOverview`示例应用程序提供了上面列出的每个节点的基本实现。

## Node Inheritance Hierarchy

所有的 Texture 节点继承与 `ASDisplayNode`.

![](http://texturegroup.org/static/images/node-hierarchy.png)

以蓝色突出显示的节点是UIKit元素的同步包装器。 例如，`ASScrollNode`包装`UIScrollView`，`ASCollectionNode`包装`UICollectionView`。 `liveMapMode`中的`ASMapNode`是`UIMapView`的同步包装器。

