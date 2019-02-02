## 子类化

创建子类时最重要的区别是您是编写ASViewController还是ASDisplayNode。 这听起来很明显，但由于其中一些差异很微妙，因此记住这一点至关重要。

### ASDisplayNode

虽然子类化节点类似于编写UIView子类，但仍需遵循一些指导原则，以确保您充分利用框架并使节点按预期运行。

#### -init

当使用`nodeBlocks`时, 此方法在后台线程被调用. 但是，因为在`-init`完成之前没有其他方法可以运行，所以在此方法中永远不要使用🔐。

要记住的最重要的事情是你的init方法必须能够在任何队列上被调用。最值得注意的是，这意味着您永远不应初始化任何UIKit对象，触摸节点的视图或图层（例如`node.layer.X`或`node.view.X`）或在其中添加任何手势识别器。相反，在`-didLoad`中执行这些操作。

#### -didLoad

这个方法在概念上类似于UIViewController的`-viewDidLoad`方法; 它被调用一次，并且是后备视图加载完成的点。它保证在**主线程**上被调用，并且是进行任何UIKit事物的适当位置（例如添加手势识别器，触摸视图/图层，初始化UIKit对象）。

#### -layoutSpecThatFits:

此方法定义布局并在**后台线程**上执行繁重的计算。 此方法是构建布局规范对象的位置，该对象将生成节点的大小，以及所有子节点的大小和位置。 这是您放置大部分布局代码的地方。

您创建的布局规范对象具有可延展性，直到它在此方法中返回为止。 在此之后，它将是不可变的。 重要的是要记住不要缓存布局规范以便以后使用，而是在必要时重新创建它们。

因为它是在后台线程上运行，所以不应在此处设置任何`node.view`或`node.layer`属性。 此外，除非您知道自己在做什么，否则请勿在此方法中创建任何节点。 此外，与其他方法覆盖不同，通过调用super来开始此方法不是必需的。

#### -layout

在这种方法中调用super是`layoutSpec`的结果被应用的地方;在使用此方法调用super之后，将计算布局规范，并且将测量和定位所有子节点。

`-layout`在概念上类似于UIViewController的`-viewWillLayoutSubviews`。这是更改隐藏属性，根据需要设置基于视图的属性（不是可布局属性）或设置背景颜色的好地方。您可以将背景颜色设置放在-layoutSpecThatFits：中，但可能存在计时问题。如果你碰巧使用任何UIViews，你可以在这里设置他们的`frames`。但是，您始终可以使用`-initWithViewBlock`创建节点包装器：然后在其他位置的后台线程上调整此大小。

此方法在**主线程**上被调用。然而，如果使用布局规范(`layout Specs`)，则不应过多依赖此方法，因为从主线程进行布局更为可取。不到十分之一的子类需要这个。

`-layout`的一个很好的用途是针对特定情况，在这种情况下，您希望子节点是您的确切大小。例如。当你想要一个collectionNode占据整个屏幕。布局规范不支持这种情况，并且在此方法中使用单行手动设置框架通常最简单：

```
subnode.frame = self.bounds;
```

如果你想在`ASViewController`中获得相同的效果，你可以在`-viewWillLayoutSubviews`中做同样的事情，除非你的节点是`initWithNode`中的节点：在这种情况下它会自动执行。

### ASViewController

`ASViewController`是一个常规的`UIViewController`子类，具有管理节点的特殊功能。 因为它是一个UIViewController子类，所以所有方法都是在主线程上被调用（并且你应该总是在主线程上创建一个ASViewController）。

#### -init

此方法只会被调用一次, 而且是在 ASViewController 生命周期的非常起始的时间点. 与UIViewController初始化一样，最佳做法是**永远不要**在此方法中访问`self.view`或`self.node.view`，因为它会强制提前创建视图。 而是在`-viewDidLoad`中执行任何视图访问。

ASViewController的指定初始化程序是`initWithNode：`。 典型的初始化程序看起来类似于下面的代码。 请注意在调用super之前如何创建ASViewController的节点。 ASViewController管理节点的方式与UIViewController管理视图的方式类似，但初始化略有不同。

```
- (instancetype)init
{
  _pagerNode = [[ASPagerNode alloc] init];
  self = [super initWithNode:_pagerNode];
  
  // setup any instance variables or properties here
  if (self) {
    _pagerNode.dataSource = self;
    _pagerNode.delegate = self;
  }
  
  return self;
}
```

#### -loadView

我们建议您不要使用此方法，因为它与`-viewDidLoad`没有特别的优势，并且有一些缺点。 但是，只要不将`self.view`属性设置为其他值，就可以安全使用。 对`[super loadView]`的调用会将它设置为node.view。

#### -viewDidLoad

在ASViewController的生命周期中，此方法在`-loadView`之后立即调用一次。 这是您应该访问节点视图的最早时间。 这是放置任何应该只运行一次并需要访问视图/图层的设置代码的好地方，例如添加手势识别器。

绝不应该在此方法中放置布局代码，因为几何体更改时不会再次调用它。 注意，对于UIViewController也是如此; 将布局代码放在此方法中是不好的做法，即使您当前没有期望更改几何体。

#### -viewWillLayoutSubviews

此方法与节点的-layout方法完全同时调用，并且可以在ASViewController的生命周期中多次调用; 每当ASViewController节点的边界发生变化（包括旋转，分屏，键盘演示）以及层次结构发生变化（儿童被添加，删除或更改大小）时，都会调用它。

为了保持一致性，最佳做法是将所有布局代码放在此方法中。 因为它不经常调用，所以即使不直接依赖于大小的代码也属于此处。

#### -viewWillAppear: / -viewDidDisappear:

这些方法在ASViewController节点出现在屏幕上（最早可见）之前以及从视图层次结构中删除之后（最早不再可见）调用。 这些方法提供了一个很好的机会来启动或停止与控制器的显示或解雇相关的动画。 这也是记录用户操作的好地方。

尽管可以多次调用这些方法并且几何信息可用，但不会对所有几何体更改调用它们，因此不应将其用于核心布局代码（超出特定动画所需的设置）。