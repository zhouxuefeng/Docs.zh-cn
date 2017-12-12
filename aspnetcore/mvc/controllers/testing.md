---
title: "在 ASP.NET 核心的测试控制器逻辑"
author: ardalis
description: "了解如何在带有 Moq 和 xUnit 的 ASP.NET 核心中测试控制器逻辑。"
keywords: "ASP.NET 核心，控制器、 测试、 测试、 单元测试、 单元测试、 集成测试、 集成测试、 xUnit"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: dd4135ec-2b15-410c-b3fb-3d12eed4a1ac
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/testing
ms.openlocfilehash: aa60912e06946bd0df4936d33c88d3bf7b69984c
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
# <a name="testing-controller-logic-in-aspnet-core"></a>在 ASP.NET 核心的测试控制器逻辑

通过[Steve Smith](https://ardalis.com/)

小型，其注重用户界面顾虑，应为在 ASP.NET MVC 应用程序中的控制器。 处理非 UI 问题的大型控制器会测试和维护更加困难。

[查看或从 GitHub 下载示例](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/testing/sample)

## <a name="testing-controllers"></a>测试控制器

控制器是任何 ASP.NET 核心 MVC 应用程序的中央部分。 在这种情况下，你应具有它们的行为符合适用于你的应用程序的置信度。 自动的测试可以为您提供这种信心，并可以到达生产之前检测错误。 务必避免将放置在你的控制器中的不必要职责，并确保你测试只关注控制器职责。

控制器逻辑应该很小，并且不侧重于业务逻辑或基础结构问题 （例如，数据访问）。 测试控制器逻辑，不 framework。 测试如何控制器*行为*基于有效或无效的输入。 测试控制器响应基于业务它所执行的操作的结果。

典型控制器职责：

* 验证`ModelState.IsValid`。
* 如果返回错误响应`ModelState`无效。
* 从持久性存储中检索业务实体。
* 对业务实体执行操作。
* 将业务实体保存到持久性。
* 返回适当`IActionResult`。

## <a name="unit-testing"></a>单元测试

[单元测试](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test)涉及在从其基础结构和依赖项隔离中测试应用程序的一部分。 在测试单元测试控制器逻辑，只有单个操作的内容时，不的行为及其依赖项或框架本身。 作为你的单元测试控制器操作，请确保您仅关注其行为。 控制器单元测试可避免等[筛选器](filters.md)，[路由](../../fundamentals/routing.md)，或[模型绑定](../models/model-binding.md)。 通过将重点放在测试只是一件事，单元测试通常是编写简单且快速运行。 可以经常运行一组正确编写的单元测试，而无需多开销。 但是，单元测试不检测问题在组件之间的交互，该命令的用途是[集成测试](xref:mvc/controllers/testing#integration-testing)。

如果你在编写自定义筛选器、 路由等，您应该单元测试，但不是作为你的测试上的特定控制器操作的一部分。 应在隔离中对它们进行了测试。

> [!TIP]
> [创建和使用 Visual Studio 运行单元测试](https://docs.microsoft.com/visualstudio/test/unit-test-your-code)。

若要演示单元测试，请查看以下控制器。 它显示集体讨论会话的列表，并允许新集体讨论会话，以使用 POST 创建：

[!code-csharp[Main](testing/sample/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?highlight=12,16,21,42,43)]

以下控制器[显式依赖关系原则](http://deviq.com/explicit-dependencies-principle/)，应为其提供的实例的依赖关系注入`IBrainstormSessionRepository`。 这可以相当轻松地测试使用模拟对象框架，如[Moq](https://www.nuget.org/packages/Moq/)。 `HTTP GET Index`方法具有没有循环或分支和只能调用一种方法。 若要测试这`Index`方法，我们需要验证`ViewResult`返回，与`ViewModel`从该存储库的`List`方法。

[!code-csharp[Main](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?highlight=17-18&range=1-33,76-95)]

`HomeController` `HTTP POST Index` （如上所示） 的方法应验证：

* 操作方法会返回错误的请求`ViewResult`使用适当的数据时`ModelState.IsValid`是`false`

* `Add`调用在存储库上的方法和一个`RedirectToActionResult`使用正确的自变量返回时`ModelState.IsValid`为 true。

可以添加使用错误来测试无效模型状态`AddModelError`下面的第一个测试中所示。

[!code-csharp[Main](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?highlight=8,15-16,37-39&range=35-75)]

第一个测试确认时`ModelState`无效，相同`ViewResult`同样适用于返回`GET`请求。 请注意，测试不会尝试将一个无效的模型。 这就仍起作用，因为模型绑定不运行 (尽管[集成测试](xref:mvc/controllers/testing#integration-testing)将练习模型绑定)。 在这种情况下，未正在测试模型绑定。 这些单元测试仅测试中的操作方法的代码的用途。

第二个测试验证，当`ModelState`有效，新`BrainstormSession`添加 （通过存储库），并且该方法返回`RedirectToActionResult`具有预期的属性。 模拟不调用的调用是正常情况下将其忽略，但调用`Verifiable`末尾的安装程序调用允许它在测试中验证。 这通过调用完成`mockRepo.Verify`，如果预期的方法未调用，这将失败的测试。

> [!NOTE]
> 此示例中使用的 Moq 库便于在混合使用 （也称为"松散"mock 或存根） 的非可验证 mock 的验证，或"strict"，mock。 详细了解[自定义模型行为与 Moq](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior)。

在应用程序的另一个控制器显示与特定的集体会话相关的信息。 它包括一些逻辑以处理无效的 id 值：

[!code-csharp[Main](./testing/sample/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?highlight=19,20,21,22,25,26,27,28)]

控制器操作都有三种情况下，若要测试，每一个`return`语句：

[!code-csharp[Main](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?highlight=27,28,29,46,47,64,65,66,67,68)]

应用程序公开 web API （与集体会话和到会话中添加新的想法的方法相关联的建议列表） 作为功能：

<a name="ideas-controller"></a>

[!code-csharp[Main](testing/sample/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?highlight=21,22,27,30,31,32,33,34,35,36,41,42,46,52,65)]

`ForSession`方法返回的列表`IdeaDTO`类型。 避免直接通过 API 调用返回您的业务域实体，因为经常包括超过 API 客户端要求，它们与外部公开的 API 不必要地耦合应用程序的内部域模型的数据。 可以手动完成域实体与通过网络将返回的类型之间的映射 (使用 LINQ`Select`如此处所示) 或使用类似库[AutoMapper](https://github.com/AutoMapper/AutoMapper)

适用于单元测试`Create`和`ForSession`API 方法：

[!code-csharp[Main](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?highlight=18,23,29,33,38-39,43,50,58-59,68-70,76-78&range=1-83,121-135)]

如前所述，若要测试方法的行为时`ModelState`是无效的将模型错误添加到控制器中，为测试的一部分。 请勿尝试在你的单元测试中测试模型验证或模型绑定-只需测试时遇到与特定的操作方法的行为`ModelState`值。

第二个测试依赖于存储库返回 null，因此模拟的存储库配置为返回 null。 没有无需创建一个测试数据库 (在内存中或其他) 和构造的查询将返回此结果-它可以在单个语句中所示。

最后一次测试验证该存储库的`Update`调用方法。 正如我们做之前，使用调用 mock `Verifiable` ，然后模拟储存库的`Verify`调用方法来确认执行可验证的方法。 它不是单元测试有责任确保`Update`方法保存的数据; 为此，可以与的集成测试。

## <a name="integration-testing"></a>集成测试

[集成测试](../../testing/integration-testing.md)做是为了确保你的应用程序工作中的单独模块正确在一起。 通常情况下，你可以测试与单元测试的任何内容，你还可以测试与的集成测试，但反之不是如此。 但是，集成测试往往要比单元测试慢得多。 因此，最好测试你可以使用单元测试的任何和集成测试用于涉及多个协作者的方案。

尽管它们仍可能很有用，但模拟对象很少使用中的整体测试。 在单元测试，模拟对象是有效的方法来控制在要测试的单元外部协作者出于测试目的的行为方式。 在集成测试中，实际的协作者用于确认整个子系统一起工作正常。

### <a name="application-state"></a>应用程序状态

执行集成测试时的一个重要考虑因素是如何设置你的应用程序的状态。 测试需要运行独立于另一个，并且因此每个测试应该开头的应用程序中已知的状态。 如果你的应用程序不使用数据库或不具有任何持久性，这可能不是问题。 但是，大多数实际的应用保留到某种类型的数据存储区，其状态，因此任何所做的一个测试修改会影响另一个测试，除非重置数据存储区。 使用内置`TestServer`，到我们的集成测试，在承载 ASP.NET Core 应用程序中非常简单明了，但，不一定授予对要使用的数据的访问。 如果你使用的实际数据库，一种方法是让应用程序连接到测试数据库，该测试可以访问并确保将重置为已知的状态的每个测试执行之前。

在此示例应用程序中，我使用实体框架核心 InMemoryDatabase 支持，因此只是无法从我的测试项目连接到它。 相反，我公开`InitializeDatabase`从应用程序的方法`Startup`类，该类我调用是否在应用程序启动时`Development`环境。 我的集成测试自动从中受益，只要它们将环境设置为`Development`。 我不需要担心重置数据库，因为 InMemoryDatabase 应用程序重新启动每次重置。

`Startup`类：

[!code-csharp[Main](testing/sample/TestingControllersSample/src/TestingControllersSample/Startup.cs?highlight=19,20,34,35,43,52)]

你将看到`GetTestSession`常用在下面的集成测试的方法。

### <a name="accessing-views"></a>访问视图

每个集成测试类配置`TestServer`会运行 ASP.NET Core 应用。 默认情况下，`TestServer`承载它在何处运行-这种情况下，测试项目文件夹的文件夹中的 web 应用。 因此，当你尝试测试返回的控制器操作`ViewResult`，你可能会看到此错误：

```
The view 'Index' was not found. The following locations were searched:
(list of locations)
```

若要更正此问题，你需要配置服务器的内容的根，以便它可以找到所测试项目的视图。 这通过调用`UseContentRoot`中`TestFixture`类，如下所示：

[!code-csharp[Main](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/IntegrationTests/TestFixture.cs?highlight=30,33)]

`TestFixture`类负责配置和创建`TestServer`、 设置`HttpClient`与通信`TestServer`。 每个集成测试使用`Client`属性来连接到测试服务器和发出请求。

[!code-csharp[Main](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/IntegrationTests/HomeControllerTests.cs?highlight=20,26,29,30,31,35,38,39,40,41,44,47,48)]

在上面的第一个测试`responseString`保存从视图，可以进行检查以确定它是否包含预期的结果中的实际呈现 HTML。

第二个测试构造的窗体 POST 具有唯一的会话名称并发送到应用程序中，然后验证返回预期的重定向。

### <a name="api-methods"></a>API 方法

如果你的应用程序公开 web Api，它的自动测试的一个好办法确认它们按预期执行。 内置`TestServer`可以轻松地测试的 web Api。 如果你的 API 方法使用模型绑定，应始终检查`ModelState.IsValid`，而集成测试正确的位置，以确认你的模型验证工作正常。

下面的一组测试目标`Create`中的方法[IdeasController](xref:mvc/controllers/testing#ideas-controller)上面所示的类：

[!code-csharp[Main](testing/sample/TestingControllersSample/tests/TestingControllersSample.Tests/IntegrationTests/ApiIdeasControllerTests.cs)]

与不同的操作返回 HTML 视图的集成测试，返回结果的 web API 方法通常可以反序列化作为强类型化对象，如上述最后一次测试所示。 在这种情况下，测试反序列化将结果发送到`BrainstormSession`实例，并确认思路已正确添加到其集合的想法。

你将这篇文章中找到的集成测试的其他示例[示例项目](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/testing/sample)。
