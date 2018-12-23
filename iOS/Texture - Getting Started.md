# 开始

Texture's basic unit is the `node`.  `ASDisplayNode` is an abstraction
over `UIView`, which in turn is an abstraction over `CALayer`.  Unlike views, which
can only be used on the main thread, nodes are thread-safe:  you can
instantiate and configure entire hierarchies of them in parallel on background
threads.

Texture 的基本单位是`node`. `ASDisplayNode`是`UIView`的抽象, 就像`UIView`是`CALayer`的抽象一样. 不同的是, 视图(这里指的是UIView的实例对象)只能够被用在主线程中, 而 nodes 是线程安全的: 您可以在后台的多个线程中并行地实例化和配置它们的整个层次结构.

To keep its user interface smooth and responsive, your app should render at 60
frames per second - the gold standard on iOS.  This means the main thread
has one-sixtieth of a second to push each frame.  That's 16 milliseconds to
execute all layout and drawing code!  And because of system overhead, your code
usually has less than ten milliseconds to run before it causes a frame drop.

为了保持用户界面的平滑和响应灵敏, 你的 App 应该以60帧每秒(iOS 中的黄金标准)的速率被渲染. 这就意味着主线程有 1 / 60 秒的时间去生成每一帧(大约16毫秒去执行所有布局和绘图代码!). 但是由于系统开销, 通常情况下, 你的代码只有不到10毫秒的运行时间(Tip: 用于生成1帧), 超过这个时间就会导致掉帧现象.

Texture lets you move image decoding, text sizing and rendering, and
other expensive UI operations off the main thread, to keep the main thread available to respond to user interaction.  Texture has other tricks up its
sleeve too... but we'll get to that later.

Texture 能够使图片解码, 文本大小调整, 渲染以及其它昂贵的UI操作脱离主线程, 这样能够使主线程及时响应用户交互. Texture 还有其它使用技巧, 我们稍后会谈到.

## Nodes - 节点

If you're used to working with views, you already know how to use nodes.  Most methods have a node equivalent and most `UIView` and `CALayer` properties are available as well.  In any case where there is a naming discrepancy (such as `.clipsToBounds` vs `.masksToBounds`), nodes will default to the `UIView` name.  The only exception is that nodes use position instead of center.

Of course, you can always access the underlying view or layer directly via `node.view` or `node.layer`, just make sure to do it on the main thread!

Texture offers a <a href = "node-overview.html">variety of nodes</a> to replace the majority of the UIKit components that you are used to. Large scale apps have been able to completely write their UI using just Texture nodes. 

如果你平时工作中对 views 很熟悉, 那么你已经知道如何使用 nodes 了. 大多数方法在 node 上都是等效的, 并且大多数`UIView`和`UILayer`的属性也可用. 在任何存在命名差异的情况下（例如`.clipsToBounds`对`.masksToBounds`），节点将默认为`UIView`名称。 唯一的例外是节点使用位置而不是中心。

当然，您始终可以通过`node.view`或`node.layer`直接访问底层视图或图层，只需确保在主线程上执行即可！

Texture 提供了[各种 node]() 来代替你习惯使用的大多数 UIKit 组件. 目前, 仅用 Texture 的 nodes, 完全可以编写大型 App 的UI.

## Node Containers - 节点容器

When converting an app to use Texture, a common mistake is to add nodes directly to an existing view hierarchy. Doing this will virtually guarantee that your nodes will flash as they are rendered.

Instead, you should add nodes as subnodes of one of the many node container classes. These containers are in charge of telling contained nodes what state they’re currently in so that data can be loaded and nodes can be rendered as efficiently as possible. You should think of these classes as the integration point between UIKit and Texture.

将应用程序转换为使用Texture时，常见的错误是将节点直接添加到现有视图层次结构中。 这样做几乎可以保证节点在渲染时会闪烁。

相反，您应该将节点添加为[许多节点容器类]()之一的子节点。 这些容器负责告知包含节点它们当前处于什么状态，以便可以加载数据并尽可能高效地呈现节点。 您应该将这些类视为UIKit和Texture之间的集成点。

## Layout Engine - 布局引擎

Texture’s layout engine is both one of its most powerful and one of its most unique features. Based on the `CSS FlexBox` model, it provides a declarative way of specifying a custom node’s size and layout of its subnodes. While all nodes are concurrently rendered by default, asynchronous measurement and layout are performed by providing an `ASLayoutSpec` for each node.

Texture的布局引擎既是其最强大的功能之一，也是其最独特的功能之一。 基于`CSS FlexBox`模型，它提供了一种声明性方法，用于指定自定义节点的大小和子节点的布局。 虽然默认情况下同时呈现所有节点，但可以通过为每个节点提供`ASLayoutSpec`来执行异步测量和布局。

## Advanced Developer Features - 高级开发者特性

Texture offers a variety of advanced developer features that cannot be found in UIKit or Foundation. Our developers have found that Texture allows simplifications in their architecture and improves developer velocity.

(Full list coming soon!)

Texture提供了各种高级开发人员功能，这些功能在UIKit或Foundation中找不到。 我们的开发人员已经发现Texture能够简化架构并提高开发人员效率。

(完整列表即将推出!)

## Adding Texture to your App

If you are new to Texture, we recommend that you check out our ASDKgram example app. We’ve created a handy guide (coming soon!) with step-by-step directions and a follow along example on how to add Texture to an app.

If you run into any problems along the way, reach out to us GitHub or the Texture Slack community for help.

如果您不熟悉 Texture，我们建议您查看我们的ASDKgram示例应用程序。 我们已经创建了一个方便的指南（即将推出！），其中包含一步一步的说明以及如何将 Texture 添加到应用程序的示例。

如果您遇到任何问题，请联系我们GitHub或Texture [Slack社区]()寻求帮助。



