#使用`UnitTest`进行单元测试

1. 单元测试就是为你的方法多专门写一个测试函数, 从最小单位开始监控来保证软件的质量.
2. 对于面向对象编程，最小单元就是方法，包括基类（超类）、抽象类、或者派生类（子类）中的方法。
3. 按照`Objc.io`文章的观点，如果我们代码之间的耦合度很小，那么可以将其分解成多个小部件，从而更易于测试。
4. `TDD(Test-Driven Development)`，即测试驱动开发，该模式要求开发者在编写某个功能的代码之前先将其测试代码写好，然后编写实现代码并进行测试，从而保证实现的代码不会出现问题。因此整个项目的开发进度将由测试来驱动，这有助于开发出高质量而又正确的代码，实现敏捷开发。（Objc.io上面的文章真的非常的好）

##UnitTest的历史
之前XCTest测试框架的一个分支, 原来Xcode中的单元测试和集成测试统称为`XCTest`(Xcode5.x开始有的), 再往前追溯, `XCTest`之前其实叫做`OCTest`. Xcode 7.x之后, XCTest细分为`UnitTest`(用于单元测试)和`UITest`(用于集成测试).

---

##UnitTest文件结构
不论项目开始就引入了`UnitTest`还是后来引入的, 在文件结构上都没有区别(想要知道怎样引入`UnitTest`自己去Google, 一搜一大片), 引入`UnitTest`之后, 在项目的主文件夹下, 会多出来一个文件名为"项目名拼接Test"的文件夹, 内部包含两个文件 -> ①与文件夹同名的`.m`文件, ②info.plist文件. 打开.m文件, 内容如下:

```
#import <XCTest/XCTest.h>

@interface SnowParadiseTests : XCTestCase

@end

@implementation SnowParadiseTests

- (void)setUp {
    [super setUp];
    // Put setup code here. This method is called before the invocation of each test method in the class.
    // 在此放置初始化代码. 此方法会在本类中的每个测试方法调用之前被调用.
}

- (void)tearDown {
    // Put teardown code here. This method is called after the invocation of each test method in the class.
    // 在此放置 teardown 代码, 此方法会在类中的每个测试方法调用之后被调用.
    [super tearDown];
}


- (void)testExample {
    // This is an example of a functional test case.
    // 这是一个测试用例的实例方法
    // Use XCTAssert and related functions to verify your tests produce the correct results.
    // 使用`XCTAssert`和相关的函数来保证你的测试用例产生正确的结果.
}

- (void)testPerformanceExample {
    // This is an example of a performance test case.
    // 这是一个检测代码执行性能的示例
    [self measureBlock:^{
        // Put the code you want to measure the time of here.
        // 放置你想测试执行效率的代码
    }];
}

@end
```