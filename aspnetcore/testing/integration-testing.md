---
title: "在 ASP.NET Core 中测试的集成"
author: ardalis
description: "如何使用 ASP.NET Core 的集成测试以确保应用程序的组件正常工作。"
keywords: "ASP.NET 核心集成测试"
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: 40d534f2-89b3-4b09-9c2c-3494bf9991c9
ms.technology: aspnet
ms.prod: asp.net-core
uid: testing/integration-testing
ms.openlocfilehash: 02018299c9bd1d194c2c70c14f518786e803d572
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="integration-testing-in-aspnet-core"></a><span data-ttu-id="f1103-104">在 ASP.NET Core 中测试的集成</span><span class="sxs-lookup"><span data-stu-id="f1103-104">Integration testing in ASP.NET Core</span></span>

<span data-ttu-id="f1103-105">通过[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="f1103-105">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="f1103-106">集成测试可确保应用程序的组件组合在一起时正常工作。</span><span class="sxs-lookup"><span data-stu-id="f1103-106">Integration testing ensures that an application's components function correctly when assembled together.</span></span> <span data-ttu-id="f1103-107">ASP.NET 核心支持的集成测试使用单元测试框架和内置测试 web 宿主可以用于处理请求而无需网络开销。</span><span class="sxs-lookup"><span data-stu-id="f1103-107">ASP.NET Core supports integration testing using unit test frameworks and a built-in test web host that can be used to handle requests without network overhead.</span></span>

[<span data-ttu-id="f1103-108">查看或下载示例代码</span><span class="sxs-lookup"><span data-stu-id="f1103-108">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/testing/integration-testing/sample)

## <a name="introduction-to-integration-testing"></a><span data-ttu-id="f1103-109">集成测试的简介</span><span class="sxs-lookup"><span data-stu-id="f1103-109">Introduction to integration testing</span></span>

<span data-ttu-id="f1103-110">集成测试来验证应用程序的不同部分正常会在一起。</span><span class="sxs-lookup"><span data-stu-id="f1103-110">Integration tests verify that different parts of an application work correctly together.</span></span> <span data-ttu-id="f1103-111">与不同[单元测试](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test)，集成测试经常涉及应用程序基础结构问题，如数据库、 文件系统、 网络资源，或 web 请求和响应。</span><span class="sxs-lookup"><span data-stu-id="f1103-111">Unlike [Unit testing](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test), integration tests frequently involve application infrastructure concerns, such as a database, file system, network resources, or web requests and responses.</span></span> <span data-ttu-id="f1103-112">单元测试使用 fakes 或使用模拟对象来替代这些问题，但集成测试的目的是确认系统按预期的这些系统。</span><span class="sxs-lookup"><span data-stu-id="f1103-112">Unit tests use fakes or mock objects in place of these concerns, but the purpose of integration tests is to confirm that the system works as expected with these systems.</span></span>

<span data-ttu-id="f1103-113">集成测试，因为在执行较大代码段，并且它们依赖于基础结构元素倾向于数量级慢于单元测试。</span><span class="sxs-lookup"><span data-stu-id="f1103-113">Integration tests, because they exercise larger segments of code and because they rely on infrastructure elements, tend to be orders of magnitude slower than unit tests.</span></span> <span data-ttu-id="f1103-114">因此，它是一个好办法限制多少集成测试编写，尤其是可以使用单元测试中测试相同的行为。</span><span class="sxs-lookup"><span data-stu-id="f1103-114">Thus, it's a good idea to limit how many integration tests you write, especially if you can test the same behavior with a unit test.</span></span>

> [!NOTE]
> <span data-ttu-id="f1103-115">如果某些行为可以使用单元测试或集成测试进行测试，更愿意相应单元测试，因为它将为几乎始终是更快。</span><span class="sxs-lookup"><span data-stu-id="f1103-115">If some behavior can be tested using either a unit test or an integration test, prefer the unit test, since it will be almost always be faster.</span></span> <span data-ttu-id="f1103-116">你可能有几十或数百个使用许多不同的输入的单元测试，但只需少量的集成测试介绍最重要的方案。</span><span class="sxs-lookup"><span data-stu-id="f1103-116">You might have dozens or hundreds of unit tests with many different inputs but just a handful of integration tests covering the most important scenarios.</span></span>

<span data-ttu-id="f1103-117">测试你自己的方法中的逻辑通常是域的单元测试。</span><span class="sxs-lookup"><span data-stu-id="f1103-117">Testing the logic within your own methods is usually the domain of unit tests.</span></span> <span data-ttu-id="f1103-118">测试你的应用程序在其框架，例如，使用 ASP.NET Core，或与数据库工作原理是集成测试的其中派上用场。</span><span class="sxs-lookup"><span data-stu-id="f1103-118">Testing how your application works within its framework, for example with ASP.NET Core, or with a database is where integration tests come into play.</span></span> <span data-ttu-id="f1103-119">它并不需要太多的集成测试，以确认你能够将行写入到数据库和读回。</span><span class="sxs-lookup"><span data-stu-id="f1103-119">It doesn't take too many integration tests to confirm that you're able to write a row to the database and read it back.</span></span> <span data-ttu-id="f1103-120">你不需要进行测试每个可能的排列的数据访问代码-只需测试足以为你提供你的应用程序正常的置信度。</span><span class="sxs-lookup"><span data-stu-id="f1103-120">You don't need to test every possible permutation of your data access code - you only need to test enough to give you confidence that your application is working properly.</span></span>

## <a name="integration-testing-aspnet-core"></a><span data-ttu-id="f1103-121">集成测试 ASP.NET 核心</span><span class="sxs-lookup"><span data-stu-id="f1103-121">Integration testing ASP.NET Core</span></span>

<span data-ttu-id="f1103-122">若要获取设置为运行的集成测试，你将需要创建的测试项目、 添加到 ASP.NET 核心 web 项目中，引用和安装的测试运行程序。</span><span class="sxs-lookup"><span data-stu-id="f1103-122">To get set up to run integration tests, you'll need to create a test project, add a reference to your ASP.NET Core web project, and install a test runner.</span></span> <span data-ttu-id="f1103-123">此过程所述[单元测试](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test)文档，以及运行测试和命名你的测试和测试类的建议的更多详细说明。</span><span class="sxs-lookup"><span data-stu-id="f1103-123">This process is described in the [Unit testing](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation, along with more detailed instructions on running tests and recommendations for naming your tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="f1103-124">分隔单元测试和集成测试使用不同的项目。</span><span class="sxs-lookup"><span data-stu-id="f1103-124">Separate your unit tests and your integration tests using different projects.</span></span> <span data-ttu-id="f1103-125">这有助于确保你不会意外地引入到你的单元测试的基础结构问题，并可以轻松地选择要运行的测试集。</span><span class="sxs-lookup"><span data-stu-id="f1103-125">This helps ensure you don't accidentally introduce infrastructure concerns into your unit tests and lets you easily choose which set of tests to run.</span></span>

### <a name="the-test-host"></a><span data-ttu-id="f1103-126">测试主机</span><span class="sxs-lookup"><span data-stu-id="f1103-126">The Test Host</span></span>

<span data-ttu-id="f1103-127">ASP.NET 核心包括可以添加到集成测试项目，并用于托管 ASP.NET Core 应用程序，而无需实际 web 宿主请求的服务测试的测试主机。</span><span class="sxs-lookup"><span data-stu-id="f1103-127">ASP.NET Core includes a test host that can be added to integration test projects and used to host ASP.NET Core applications, serving test requests without the need for a real web host.</span></span> <span data-ttu-id="f1103-128">提供的示例包括集成测试项目已配置为使用[xUnit](https://xunit.github.io)和测试主机。</span><span class="sxs-lookup"><span data-stu-id="f1103-128">The provided sample includes an integration test project which has been configured to use [xUnit](https://xunit.github.io) and the Test Host.</span></span> <span data-ttu-id="f1103-129">它使用`Microsoft.AspNetCore.TestHost`NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="f1103-129">It uses the `Microsoft.AspNetCore.TestHost` NuGet package.</span></span>

<span data-ttu-id="f1103-130">一次`Microsoft.AspNetCore.TestHost`包包括在项目中后，你将能够创建和配置`TestServer`在测试中。</span><span class="sxs-lookup"><span data-stu-id="f1103-130">Once the `Microsoft.AspNetCore.TestHost` package is included in the project, you'll be able to create and configure a `TestServer` in your tests.</span></span> <span data-ttu-id="f1103-131">下面的代码演示如何验证对站点的根目录的请求返回"Hello World ！"</span><span class="sxs-lookup"><span data-stu-id="f1103-131">The following test shows how to verify that a request made to the root of a site returns "Hello World!"</span></span> <span data-ttu-id="f1103-132">并且应成功运行针对默认值由 Visual Studio 创建的 ASP.NET 核心空 Web 模板。</span><span class="sxs-lookup"><span data-stu-id="f1103-132">and should run successfully against the default ASP.NET Core Empty Web template created by Visual Studio.</span></span>

<span data-ttu-id="f1103-133">[!code-csharp[Main](../testing/integration-testing/sample/test/PrimeWeb.IntegrationTests/PrimeWebDefaultRequestShould.cs?name=snippet_WebDefault&highlight=7,16,22)]</span><span class="sxs-lookup"><span data-stu-id="f1103-133">[!code-csharp[Main](../testing/integration-testing/sample/test/PrimeWeb.IntegrationTests/PrimeWebDefaultRequestShould.cs?name=snippet_WebDefault&highlight=7,16,22)]</span></span>

<span data-ttu-id="f1103-134">此测试使用排列 Act 断言模式。</span><span class="sxs-lookup"><span data-stu-id="f1103-134">This test is using the Arrange-Act-Assert pattern.</span></span> <span data-ttu-id="f1103-135">在创建的实例的构造函数中完成准备步骤`TestServer`。</span><span class="sxs-lookup"><span data-stu-id="f1103-135">The Arrange step is done in the constructor, which creates an instance of `TestServer`.</span></span> <span data-ttu-id="f1103-136">一个已配置`WebHostBuilder`将用于创建`TestHost`; 在此示例中，`Configure`方法从待测试 (SUT) 系统`Startup`类传递给`WebHostBuilder`。</span><span class="sxs-lookup"><span data-stu-id="f1103-136">A configured `WebHostBuilder` will be used to create a `TestHost`; in this example, the `Configure` method from the system under test (SUT)'s `Startup` class is passed to the `WebHostBuilder`.</span></span> <span data-ttu-id="f1103-137">此方法将用于配置的请求管道`TestServer`到 SUT 服务器将配置方式相同。</span><span class="sxs-lookup"><span data-stu-id="f1103-137">This method will be used to configure the request pipeline of the `TestServer` identically to how the SUT server would be configured.</span></span>

<span data-ttu-id="f1103-138">在测试的 Act 部分中，向发出请求`TestServer`"/"路径和响应的实例返回读入字符串。</span><span class="sxs-lookup"><span data-stu-id="f1103-138">In the Act portion of the test, a request is made to the `TestServer` instance for the "/" path, and the response is read back into a string.</span></span> <span data-ttu-id="f1103-139">此字符串与"Hello World ！"的预期字符串进行比较。</span><span class="sxs-lookup"><span data-stu-id="f1103-139">This string is compared with the expected string of "Hello World!".</span></span> <span data-ttu-id="f1103-140">如果它们匹配，则测试通过;否则，它将失败。</span><span class="sxs-lookup"><span data-stu-id="f1103-140">If they match, the test passes; otherwise, it fails.</span></span>

<span data-ttu-id="f1103-141">现在你可以添加了几个其他集成测试，以确认检查功能主要通过 web 应用程序能否正常运行：</span><span class="sxs-lookup"><span data-stu-id="f1103-141">Now you can add a few additional integration tests to confirm that the prime checking functionality works via the web application:</span></span>

<span data-ttu-id="f1103-142">[!code-csharp[Main](../testing/integration-testing/sample/test/PrimeWeb.IntegrationTests/PrimeWebCheckPrimeShould.cs?name=snippet_CheckPrime)]</span><span class="sxs-lookup"><span data-stu-id="f1103-142">[!code-csharp[Main](../testing/integration-testing/sample/test/PrimeWeb.IntegrationTests/PrimeWebCheckPrimeShould.cs?name=snippet_CheckPrime)]</span></span>

<span data-ttu-id="f1103-143">请注意，你实际上不尝试使用这些测试进行测试的质数检查程序的正确性但而应假定 web 应用程序执行的操作，你的预期。</span><span class="sxs-lookup"><span data-stu-id="f1103-143">Note that you're not really trying to test the correctness of the prime number checker with these tests but rather that the web application is doing what you expect.</span></span> <span data-ttu-id="f1103-144">你已为你提供中的置信度的单元测试覆盖率`PrimeService`，如你可以在此处看到：</span><span class="sxs-lookup"><span data-stu-id="f1103-144">You already have unit test coverage that gives you confidence in `PrimeService`, as you can see here:</span></span>

![测试资源管理器](integration-testing/_static/test-explorer.png)

<span data-ttu-id="f1103-146">你可以了解有关中的单元测试[单元测试](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test)文章。</span><span class="sxs-lookup"><span data-stu-id="f1103-146">You can learn more about the unit tests in the [Unit testing](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test) article.</span></span>

## <a name="refactoring-to-use-middleware"></a><span data-ttu-id="f1103-147">重构以使用中间件</span><span class="sxs-lookup"><span data-stu-id="f1103-147">Refactoring to use middleware</span></span>

<span data-ttu-id="f1103-148">重构是应用程序的代码更改为不会更改其行为改进其设计的过程。</span><span class="sxs-lookup"><span data-stu-id="f1103-148">Refactoring is the process of changing an application's code to improve its design without changing its behavior.</span></span> <span data-ttu-id="f1103-149">它应理想情况下进行一套传递测试，因为这些帮助确保系统的行为保持不变之前, 和之后所做的更改时。</span><span class="sxs-lookup"><span data-stu-id="f1103-149">It should ideally be done when there is a suite of passing tests, since these help ensure the system's behavior remains the same before and after the changes.</span></span> <span data-ttu-id="f1103-150">在其中检查逻辑质数实现中 web 应用程序的方式查看`Configure`方法，你会看到：</span><span class="sxs-lookup"><span data-stu-id="f1103-150">Looking at the way in which the prime checking logic is implemented in the web application's `Configure` method, you see:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }

    app.Run(async (context) =>
    {
        if (context.Request.Path.Value.Contains("checkprime"))
        {
            int numberToCheck;
            try
            {
                numberToCheck = int.Parse(context.Request.QueryString.Value.Replace("?", ""));
                var primeService = new PrimeService();
                if (primeService.IsPrime(numberToCheck))
                {
                    await context.Response.WriteAsync($"{numberToCheck} is prime!");
                }
                else
                {
                    await context.Response.WriteAsync($"{numberToCheck} is NOT prime!");
                }
            }
            catch
            {
                await context.Response.WriteAsync("Pass in a number to check in the form /checkprime?5");
            }
        }
        else
        {
            await context.Response.WriteAsync("Hello World!");
        }
    });
}
```

<span data-ttu-id="f1103-151">此代码有效，但它并不如何你想要在 ASP.NET Core 应用程序，即使简单，因为这是实现此类功能。</span><span class="sxs-lookup"><span data-stu-id="f1103-151">This code works, but it's far from how you would like to implement this kind of functionality in an ASP.NET Core application, even as simple as this is.</span></span> <span data-ttu-id="f1103-152">假设什么`Configure`方法将如下所示如果您需要将这么多代码添加到其中，每次您添加另一个 URL 终结点 ！</span><span class="sxs-lookup"><span data-stu-id="f1103-152">Imagine what the `Configure` method would look like if you needed to add this much code to it every time you add another URL endpoint!</span></span>

<span data-ttu-id="f1103-153">要考虑的一个选项添加[MVC](xref:mvc/overview)与应用程序创建控制器来处理主要检查。</span><span class="sxs-lookup"><span data-stu-id="f1103-153">One option to consider is adding [MVC](xref:mvc/overview) to the application and creating a controller to handle the prime checking.</span></span> <span data-ttu-id="f1103-154">但是，假定当前不需要任何其他 MVC 的功能，即一个位浪费。</span><span class="sxs-lookup"><span data-stu-id="f1103-154">However, assuming you don't currently need any other MVC functionality, that's a bit overkill.</span></span>

<span data-ttu-id="f1103-155">你可以但是，充分利用 ASP.NET Core[中间件](xref:fundamentals/middleware)，这将帮助我们封装检查其自己的类中的逻辑校准并更好地实现[关注点分离](http://deviq.com/separation-of-concerns/)中`Configure`方法。</span><span class="sxs-lookup"><span data-stu-id="f1103-155">You can, however, take advantage of ASP.NET Core [middleware](xref:fundamentals/middleware), which will help us encapsulate the prime checking logic in its own class and achieve better [separation of concerns](http://deviq.com/separation-of-concerns/) in the `Configure` method.</span></span>

<span data-ttu-id="f1103-156">你想要允许该中间件用于指定作为参数，因此中间件类需要的路径`RequestDelegate`和`PrimeCheckerOptions`其构造函数中的实例。</span><span class="sxs-lookup"><span data-stu-id="f1103-156">You want to allow the path the middleware uses to be specified as a parameter, so the middleware class expects a `RequestDelegate` and a `PrimeCheckerOptions` instance in its constructor.</span></span> <span data-ttu-id="f1103-157">如果请求的路径不匹配此中间件是配置需要，你只需调用链中的下一步的中间件并执行任何进一步操作。</span><span class="sxs-lookup"><span data-stu-id="f1103-157">If the path of the request doesn't match what this middleware is configured to expect, you simply call the next middleware in the chain and do nothing further.</span></span> <span data-ttu-id="f1103-158">中的实现代码的其余部分`Configure`现已在`Invoke`方法。</span><span class="sxs-lookup"><span data-stu-id="f1103-158">The rest of the implementation code that was in `Configure` is now in the `Invoke` method.</span></span>

> [!NOTE]
> <span data-ttu-id="f1103-159">因为取决于该中间件`PrimeService`服务，还所请求的构造函数使用此服务实例。</span><span class="sxs-lookup"><span data-stu-id="f1103-159">Since the middleware depends on the `PrimeService` service, you're also requesting an instance of this service with the constructor.</span></span> <span data-ttu-id="f1103-160">框架将提供此服务通过[依赖关系注入](xref:fundamentals/dependency-injection)，假设它已配置，例如，在`ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="f1103-160">The framework will provide this service via [Dependency Injection](xref:fundamentals/dependency-injection), assuming it has been configured, for example in `ConfigureServices`.</span></span>

<span data-ttu-id="f1103-161">[!code-csharp[Main](../testing/integration-testing/sample/src/PrimeWeb/Middleware/PrimeCheckerMiddleware.cs?highlight=39-63)]</span><span class="sxs-lookup"><span data-stu-id="f1103-161">[!code-csharp[Main](../testing/integration-testing/sample/src/PrimeWeb/Middleware/PrimeCheckerMiddleware.cs?highlight=39-63)]</span></span>

<span data-ttu-id="f1103-162">由于其路径与匹配时，此中间件将充当请求委托链中的终结点，因此时不需要调用`_next.Invoke`时此中间件将处理该请求。</span><span class="sxs-lookup"><span data-stu-id="f1103-162">Since this middleware acts as an endpoint in the request delegate chain when its path matches, there is no call to `_next.Invoke` when this middleware handles the request.</span></span>

<span data-ttu-id="f1103-163">使用位置和一些有用的扩展方法中创建以方便将其配置，重构此中间件`Configure`方法如下所示：</span><span class="sxs-lookup"><span data-stu-id="f1103-163">With this middleware in place and some helpful extension methods created to make configuring it easier, the refactored `Configure` method looks like this:</span></span>

<span data-ttu-id="f1103-164">[!code-csharp[Main](../testing/integration-testing/sample/src/PrimeWeb/Startup.cs?highlight=9&range=19-33)]</span><span class="sxs-lookup"><span data-stu-id="f1103-164">[!code-csharp[Main](../testing/integration-testing/sample/src/PrimeWeb/Startup.cs?highlight=9&range=19-33)]</span></span>

<span data-ttu-id="f1103-165">此重构，以下确信，web 应用程序仍然正常工作和前面一样，因为所有通过集成测试。</span><span class="sxs-lookup"><span data-stu-id="f1103-165">Following this refactoring, you're confident that the web application still works as before, since your integration tests are all passing.</span></span>

> [!NOTE]
> <span data-ttu-id="f1103-166">它是一个好办法之后完成重构，你的测试都通过将所做的更改提交到源代码管理。</span><span class="sxs-lookup"><span data-stu-id="f1103-166">It's a good idea to commit your changes to source control after you complete a refactoring and your tests pass.</span></span> <span data-ttu-id="f1103-167">如果您一起练习测试驱动开发，[考虑向你红-绿-重构循环添加提交](https://ardalis.com/rgrc-is-the-new-red-green-refactor-for-test-first-development)。</span><span class="sxs-lookup"><span data-stu-id="f1103-167">If you're practicing Test Driven Development, [consider adding Commit to your Red-Green-Refactor cycle](https://ardalis.com/rgrc-is-the-new-red-green-refactor-for-test-first-development).</span></span>

## <a name="resources"></a><span data-ttu-id="f1103-168">资源</span><span class="sxs-lookup"><span data-stu-id="f1103-168">Resources</span></span>

* [<span data-ttu-id="f1103-169">单元测试</span><span class="sxs-lookup"><span data-stu-id="f1103-169">Unit testing</span></span>](https://docs.microsoft.com/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [<span data-ttu-id="f1103-170">中间件</span><span class="sxs-lookup"><span data-stu-id="f1103-170">Middleware</span></span>](xref:fundamentals/middleware)
* [<span data-ttu-id="f1103-171">测试控制器</span><span class="sxs-lookup"><span data-stu-id="f1103-171">Testing controllers</span></span>](xref:mvc/controllers/testing)
