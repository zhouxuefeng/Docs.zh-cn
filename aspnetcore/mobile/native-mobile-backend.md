---
title: "创建本机移动应用程序的后端服务"
author: ardalis
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 3b6a32f2-5af9-4ede-9b7f-17ab300526d0
ms.technology: aspnet
ms.prod: asp.net-core
uid: mobile/native-mobile-backend
ms.openlocfilehash: 7b2b35b1519badbc8b8fbddab97a0c6336926185
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="creating-backend-services-for-native-mobile-applications"></a><span data-ttu-id="102a6-103">创建本机移动应用程序的后端服务</span><span class="sxs-lookup"><span data-stu-id="102a6-103">Creating Backend Services for Native Mobile Applications</span></span>

<span data-ttu-id="102a6-104">通过[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="102a6-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="102a6-105">移动应用可以轻松地与 ASP.NET Core 后端服务进行通信。</span><span class="sxs-lookup"><span data-stu-id="102a6-105">Mobile apps can easily communicate with ASP.NET Core backend services.</span></span>

[<span data-ttu-id="102a6-106">查看或下载后端服务的示例代码</span><span class="sxs-lookup"><span data-stu-id="102a6-106">View or download sample backend services code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/mobile/native-mobile-backend/sample)

## <a name="the-sample-native-mobile-app"></a><span data-ttu-id="102a6-107">示例本机移动应用程序</span><span class="sxs-lookup"><span data-stu-id="102a6-107">The Sample Native Mobile App</span></span>

<span data-ttu-id="102a6-108">本教程演示如何创建使用 ASP.NET 核心 MVC 以支持本机移动应用的后端服务。</span><span class="sxs-lookup"><span data-stu-id="102a6-108">This tutorial demonstrates how to create backend services using ASP.NET Core MVC to support native mobile apps.</span></span> <span data-ttu-id="102a6-109">它使用[Xamarin 窗体 ToDoRest 应用](https://developer.xamarin.com/guides/xamarin-forms/web-services/consuming/rest/)作为其本机客户端，其中包括 Android、 iOS、 Windows 通用和 Window Phone 设备的单独本机客户端。</span><span class="sxs-lookup"><span data-stu-id="102a6-109">It uses the [Xamarin Forms ToDoRest app](https://developer.xamarin.com/guides/xamarin-forms/web-services/consuming/rest/) as its native client, which includes separate native clients for Android, iOS, Windows Universal, and Window Phone devices.</span></span> <span data-ttu-id="102a6-110">你可以遵循链接本教程中，若要创建本机应用程序 （并安装所需的可用 Xamarin 工具），以及下载 Xamarin 示例解决方案。</span><span class="sxs-lookup"><span data-stu-id="102a6-110">You can follow the linked tutorial to create the native app (and install the necessary free Xamarin tools), as well as download the Xamarin sample solution.</span></span> <span data-ttu-id="102a6-111">Xamarin 示例包含一个 ASP.NET Web API 2 服务项目，本文中的 ASP.NET Core 应用替换 （需要进行任何更改客户端）。</span><span class="sxs-lookup"><span data-stu-id="102a6-111">The Xamarin sample includes an ASP.NET Web API 2 services project, which this article's ASP.NET Core app replaces (with no changes required by the client).</span></span>

![在 Android 智能手机上运行的执行操作的 Rest 应用程序](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a><span data-ttu-id="102a6-113">功能</span><span class="sxs-lookup"><span data-stu-id="102a6-113">Features</span></span>

<span data-ttu-id="102a6-114">ToDoRest 应用支持列出、 添加、 删除和更新待办事项。</span><span class="sxs-lookup"><span data-stu-id="102a6-114">The ToDoRest app supports listing, adding, deleting, and updating To-Do items.</span></span> <span data-ttu-id="102a6-115">每个项都有一个 ID、 名称、 说明以及，该值指示是否已完成的工作尚未属性。</span><span class="sxs-lookup"><span data-stu-id="102a6-115">Each item has an ID, a Name, Notes, and a property indicating whether it's been Done yet.</span></span>

<span data-ttu-id="102a6-116">主视图的项，如上所示，列出每个项的名称，并指示是否它是具有选中标记。</span><span class="sxs-lookup"><span data-stu-id="102a6-116">The main view of the items, as shown above, lists each item's name and indicates if it is done with a checkmark.</span></span>

<span data-ttu-id="102a6-117">点击`+`图标可打开添加项对话框：</span><span class="sxs-lookup"><span data-stu-id="102a6-117">Tapping the `+` icon opens an add item dialog:</span></span>

![添加项对话框](native-mobile-backend/_static/todo-android-new-item.png)

<span data-ttu-id="102a6-119">点击主列表屏幕上的项将打开在其中可以修改项的名称、 说明以及完成的设置，或删除的项目，可以编辑对话框：</span><span class="sxs-lookup"><span data-stu-id="102a6-119">Tapping an item on the main list screen opens up an edit dialog where the item's Name, Notes, and Done settings can be modified, or the item can be deleted:</span></span>

![编辑项对话框](native-mobile-backend/_static/todo-android-edit-item.png)

<span data-ttu-id="102a6-121">此示例是默认配置为使用后端服务托管在 developer.xamarin.com，这允许只读操作。</span><span class="sxs-lookup"><span data-stu-id="102a6-121">This sample is configured by default to use backend services hosted at developer.xamarin.com, which allow read-only operations.</span></span> <span data-ttu-id="102a6-122">若要针对在计算机上运行的下一节中创建的 ASP.NET Core 应用测试一下，你将需要更新应用程序的`RestUrl`常量。</span><span class="sxs-lookup"><span data-stu-id="102a6-122">To test it out yourself against the ASP.NET Core app created in the next section running on your computer, you'll need to update the app's `RestUrl` constant.</span></span> <span data-ttu-id="102a6-123">导航到`ToDoREST`项目，然后打开*Constants.cs*文件。</span><span class="sxs-lookup"><span data-stu-id="102a6-123">Navigate to the `ToDoREST` project and open the *Constants.cs* file.</span></span> <span data-ttu-id="102a6-124">替换`RestUrl`具有包含您的计算机的 IP 的 URL 地址 （不 localhost 或 127.0.0.1，因为此地址用于从设备模拟器中不是从您的计算机）。</span><span class="sxs-lookup"><span data-stu-id="102a6-124">Replace the `RestUrl` with a URL that includes your machine's IP address (not localhost or 127.0.0.1, since this address is used from the device emulator, not from your machine).</span></span> <span data-ttu-id="102a6-125">包括的端口号 (5000)。</span><span class="sxs-lookup"><span data-stu-id="102a6-125">Include the port number as well (5000).</span></span> <span data-ttu-id="102a6-126">若要测试的设备中运行你的服务，请确保你没有活动的防火墙阻止访问此端口。</span><span class="sxs-lookup"><span data-stu-id="102a6-126">In order to test that your services work with a device, ensure you don't have an active firewall blocking access to this port.</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "csharp"} -->

```csharp
// URL of REST service (Xamarin ReadOnly Service)
//public static string RestUrl = "http://developer.xamarin.com:8081/api/todoitems{0}";

// use your machine's IP address
public static string RestUrl = "http://192.168.1.207:5000/api/todoitems/{0}";
```

## <a name="creating-the-aspnet-core-project"></a><span data-ttu-id="102a6-127">创建 ASP.NET Core 项目</span><span class="sxs-lookup"><span data-stu-id="102a6-127">Creating the ASP.NET Core Project</span></span>

<span data-ttu-id="102a6-128">在 Visual Studio 中创建一个新的 ASP.NET 核心 Web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="102a6-128">Create a new ASP.NET Core Web Application in Visual Studio.</span></span> <span data-ttu-id="102a6-129">选择 Web API 模板和无身份验证。</span><span class="sxs-lookup"><span data-stu-id="102a6-129">Choose the Web API template and No Authentication.</span></span> <span data-ttu-id="102a6-130">将项目*ToDoApi*。</span><span class="sxs-lookup"><span data-stu-id="102a6-130">Name the project *ToDoApi*.</span></span>

![与选择的 Web API 项目模板的新建 ASP.NET Web 应用程序对话框](native-mobile-backend/_static/web-api-template.png)

<span data-ttu-id="102a6-132">应用程序应响应针对端口 5000 发出的所有请求。</span><span class="sxs-lookup"><span data-stu-id="102a6-132">The application should respond to all requests made to port 5000.</span></span> <span data-ttu-id="102a6-133">更新*Program.cs*包括`.UseUrls("http://*:5000")`来实现此目的：</span><span class="sxs-lookup"><span data-stu-id="102a6-133">Update *Program.cs* to include `.UseUrls("http://*:5000")` to achieve this:</span></span>

<span data-ttu-id="102a6-134">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Program.cs?range=10-16&highlight=3)]</span><span class="sxs-lookup"><span data-stu-id="102a6-134">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Program.cs?range=10-16&highlight=3)]</span></span>

> [!NOTE]
> <span data-ttu-id="102a6-135">请确保您运行应用程序直接，而不是在 IIS Express 中，将默认情况下忽略非本地请求后面。</span><span class="sxs-lookup"><span data-stu-id="102a6-135">Make sure you run the application directly, rather than behind IIS Express, which ignores non-local requests by default.</span></span> <span data-ttu-id="102a6-136">运行`dotnet run`从命令提示符处，或从 Visual Studio 工具栏中的调试目标下拉列表中选择应用程序名称配置文件。</span><span class="sxs-lookup"><span data-stu-id="102a6-136">Run `dotnet run` from a command prompt, or choose the application name profile from the Debug Target dropdown in the Visual Studio toolbar.</span></span>

<span data-ttu-id="102a6-137">添加一个模型类来表示待办事项。</span><span class="sxs-lookup"><span data-stu-id="102a6-137">Add a model class to represent To-Do items.</span></span> <span data-ttu-id="102a6-138">标记所需字段使用`[Required]`属性：</span><span class="sxs-lookup"><span data-stu-id="102a6-138">Mark required fields using the `[Required]` attribute:</span></span>

<span data-ttu-id="102a6-139">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Models/ToDoItem.cs)]</span><span class="sxs-lookup"><span data-stu-id="102a6-139">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Models/ToDoItem.cs)]</span></span>

<span data-ttu-id="102a6-140">API 方法需要某种方式处理数据。</span><span class="sxs-lookup"><span data-stu-id="102a6-140">The API methods require some way to work with data.</span></span> <span data-ttu-id="102a6-141">使用相同`IToDoRepository`接口的原始 Xamarin 示例用途：</span><span class="sxs-lookup"><span data-stu-id="102a6-141">Use the same `IToDoRepository` interface the original Xamarin sample uses:</span></span>

<span data-ttu-id="102a6-142">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Interfaces/IToDoRepository.cs)]</span><span class="sxs-lookup"><span data-stu-id="102a6-142">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Interfaces/IToDoRepository.cs)]</span></span>

<span data-ttu-id="102a6-143">为此示例中，实现只需使用专用的项集合：</span><span class="sxs-lookup"><span data-stu-id="102a6-143">For this sample, the implementation just uses a private collection of items:</span></span>

<span data-ttu-id="102a6-144">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Services/ToDoRepository.cs)]</span><span class="sxs-lookup"><span data-stu-id="102a6-144">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Services/ToDoRepository.cs)]</span></span>

<span data-ttu-id="102a6-145">配置中的实现*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="102a6-145">Configure the implementation in *Startup.cs*:</span></span>

<span data-ttu-id="102a6-146">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Startup.cs?highlight=6&range=29-35)]</span><span class="sxs-lookup"><span data-stu-id="102a6-146">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Startup.cs?highlight=6&range=29-35)]</span></span>

<span data-ttu-id="102a6-147">现在，你已准备好创建*ToDoItemsController*。</span><span class="sxs-lookup"><span data-stu-id="102a6-147">At this point, you're ready to create the *ToDoItemsController*.</span></span>

> [!TIP]
> <span data-ttu-id="102a6-148">了解更多有关创建 web Api 中的[构建您第一个 Web API 使用 ASP.NET 核心 MVC 和 Visual Studio](../tutorials/first-web-api.md)。</span><span class="sxs-lookup"><span data-stu-id="102a6-148">Learn more about creating web APIs in [Building Your First Web API with ASP.NET Core MVC and Visual Studio](../tutorials/first-web-api.md).</span></span>

## <a name="creating-the-controller"></a><span data-ttu-id="102a6-149">创建控制器</span><span class="sxs-lookup"><span data-stu-id="102a6-149">Creating the Controller</span></span>

<span data-ttu-id="102a6-150">将新控制器添加到项目中， *ToDoItemsController*。</span><span class="sxs-lookup"><span data-stu-id="102a6-150">Add a new controller to the project, *ToDoItemsController*.</span></span> <span data-ttu-id="102a6-151">它应从 Microsoft.AspNetCore.Mvc.Controller 继承。</span><span class="sxs-lookup"><span data-stu-id="102a6-151">It should inherit from Microsoft.AspNetCore.Mvc.Controller.</span></span> <span data-ttu-id="102a6-152">添加`Route`特性以指示控制器将处理对路径开头的请求`api/todoitems`。</span><span class="sxs-lookup"><span data-stu-id="102a6-152">Add a `Route` attribute to indicate that the controller will handle requests made to paths starting with `api/todoitems`.</span></span> <span data-ttu-id="102a6-153">`[controller]`路线中的标记会被取代的控制器的名称 (省略`Controller`后缀)，并将全局路由特别有用。</span><span class="sxs-lookup"><span data-stu-id="102a6-153">The `[controller]` token in the route is replaced by the name of the controller (omitting the `Controller` suffix), and is especially helpful for global routes.</span></span> <span data-ttu-id="102a6-154">详细了解[路由](../fundamentals/routing.md)。</span><span class="sxs-lookup"><span data-stu-id="102a6-154">Learn more about [routing](../fundamentals/routing.md).</span></span>

<span data-ttu-id="102a6-155">控制器需要`IToDoRepository`到函数; 请求的控制器构造函数通过此类型的实例。</span><span class="sxs-lookup"><span data-stu-id="102a6-155">The controller requires an `IToDoRepository` to function; request an instance of this type through the controller's constructor.</span></span> <span data-ttu-id="102a6-156">在运行时，此实例将提供使用对框架的支持，[依赖关系注入](../fundamentals/dependency-injection.md)。</span><span class="sxs-lookup"><span data-stu-id="102a6-156">At runtime, this instance will be provided using the framework's support for [dependency injection](../fundamentals/dependency-injection.md).</span></span>

<span data-ttu-id="102a6-157">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=1-17&highlight=9,14)]</span><span class="sxs-lookup"><span data-stu-id="102a6-157">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=1-17&highlight=9,14)]</span></span>

<span data-ttu-id="102a6-158">此 API 支持四个不同的 HTTP 谓词执行对数据源执行 CRUD （创建、 读取、 更新、 删除） 操作。</span><span class="sxs-lookup"><span data-stu-id="102a6-158">This API supports four different HTTP verbs to perform CRUD (Create, Read, Update, Delete) operations on the data source.</span></span> <span data-ttu-id="102a6-159">最简单的这些是读取操作，它对应于 HTTP GET 请求。</span><span class="sxs-lookup"><span data-stu-id="102a6-159">The simplest of these is the Read operation, which corresponds to an HTTP GET request.</span></span>

### <a name="reading-items"></a><span data-ttu-id="102a6-160">正在读取项目</span><span class="sxs-lookup"><span data-stu-id="102a6-160">Reading Items</span></span>

<span data-ttu-id="102a6-161">请求的项的列表，可使用 GET 请求到`List`方法。</span><span class="sxs-lookup"><span data-stu-id="102a6-161">Requesting a list of items is done with a GET request to the `List` method.</span></span> <span data-ttu-id="102a6-162">`[HttpGet]`属性`List`方法指示此操作应仅处理 GET 请求。</span><span class="sxs-lookup"><span data-stu-id="102a6-162">The `[HttpGet]` attribute on the `List` method indicates that this action should only handle GET requests.</span></span> <span data-ttu-id="102a6-163">此操作的路由是在控制器上指定的路由。</span><span class="sxs-lookup"><span data-stu-id="102a6-163">The route for this action is the route specified on the controller.</span></span> <span data-ttu-id="102a6-164">你不一定需要的操作名称用作的路由的一部分。</span><span class="sxs-lookup"><span data-stu-id="102a6-164">You don't necessarily need to use the action name as part of the route.</span></span> <span data-ttu-id="102a6-165">你只需确保每个操作都有唯一的和明确的路由。</span><span class="sxs-lookup"><span data-stu-id="102a6-165">You just need to ensure each action has a unique and unambiguous route.</span></span> <span data-ttu-id="102a6-166">路由属性可以在控制器和生成特定的路由的方法级别应用。</span><span class="sxs-lookup"><span data-stu-id="102a6-166">Routing attributes can be applied at both the controller and method levels to build up specific routes.</span></span>

<span data-ttu-id="102a6-167">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=19-23)]</span><span class="sxs-lookup"><span data-stu-id="102a6-167">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=19-23)]</span></span>

<span data-ttu-id="102a6-168">`List`方法返回 200 OK 响应代码和所有序列化为 JSON 的 ToDo 项。</span><span class="sxs-lookup"><span data-stu-id="102a6-168">The `List` method returns a 200 OK response code and all of the ToDo items, serialized as JSON.</span></span>

<span data-ttu-id="102a6-169">你可以测试新 API 方法使用多种工具，如[Postman](https://www.getpostman.com/docs/)，此处所示：</span><span class="sxs-lookup"><span data-stu-id="102a6-169">You can test your new API method using a variety of tools, such as [Postman](https://www.getpostman.com/docs/), shown here:</span></span>

![显示 todoitems 以及显示三个项返回的 JSON 响应正文的 GET 请求的 postman 控制台](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a><span data-ttu-id="102a6-171">创建项</span><span class="sxs-lookup"><span data-stu-id="102a6-171">Creating Items</span></span>

<span data-ttu-id="102a6-172">按照约定，创建新数据项映射到 HTTP POST 谓词。</span><span class="sxs-lookup"><span data-stu-id="102a6-172">By convention, creating new data items is mapped to the HTTP POST verb.</span></span> <span data-ttu-id="102a6-173">`Create`方法具有`[HttpPost]`属性应用于该对象，并接受`ToDoItem`实例。</span><span class="sxs-lookup"><span data-stu-id="102a6-173">The `Create` method has an `[HttpPost]` attribute applied to it, and accepts a `ToDoItem` instance.</span></span> <span data-ttu-id="102a6-174">由于`item`将在发布信息正文中传递自变量，则此参数用修饰`[FromBody]`属性。</span><span class="sxs-lookup"><span data-stu-id="102a6-174">Since the `item` argument will be passed in the body of the POST, this parameter is decorated with the `[FromBody]` attribute.</span></span>

<span data-ttu-id="102a6-175">在方法中，项会检查有效性和之前存在于数据存储，并且如果不出现任何问题，添加使用存储库。</span><span class="sxs-lookup"><span data-stu-id="102a6-175">Inside the method, the item is checked for validity and prior existence in the data store, and if no issues occur, it is added using the repository.</span></span> <span data-ttu-id="102a6-176">检查`ModelState.IsValid`执行[模型验证](../mvc/models/validation.md)，以及应该在每个 API 方法，接受用户输入来完成。</span><span class="sxs-lookup"><span data-stu-id="102a6-176">Checking `ModelState.IsValid` performs [model validation](../mvc/models/validation.md), and should be done in every API method that accepts user input.</span></span>

<span data-ttu-id="102a6-177">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=25-46)]</span><span class="sxs-lookup"><span data-stu-id="102a6-177">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=25-46)]</span></span>

<span data-ttu-id="102a6-178">该示例使用枚举包含传递到移动客户端的错误代码：</span><span class="sxs-lookup"><span data-stu-id="102a6-178">The sample uses an enum containing error codes that are passed to the mobile client:</span></span>

<span data-ttu-id="102a6-179">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=91-99)]</span><span class="sxs-lookup"><span data-stu-id="102a6-179">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=91-99)]</span></span>

<span data-ttu-id="102a6-180">测试添加新项使用 Postman 通过选择 POST 谓词提供请求正文中的 JSON 格式中的新对象。</span><span class="sxs-lookup"><span data-stu-id="102a6-180">Test adding new items using Postman by choosing the POST verb providing the new object in JSON format in the Body of the request.</span></span> <span data-ttu-id="102a6-181">你还应添加一个请求标头指定`Content-Type`的`application/json`。</span><span class="sxs-lookup"><span data-stu-id="102a6-181">You should also add a request header specifying a `Content-Type` of `application/json`.</span></span>

![显示 POST 和响应的 postman 控制台](native-mobile-backend/_static/postman-post.png)

<span data-ttu-id="102a6-183">方法会在响应中返回新创建的项。</span><span class="sxs-lookup"><span data-stu-id="102a6-183">The method returns the newly created item in the response.</span></span>

### <a name="updating-items"></a><span data-ttu-id="102a6-184">更新项的项</span><span class="sxs-lookup"><span data-stu-id="102a6-184">Updating Items</span></span>

<span data-ttu-id="102a6-185">修改记录使用 HTTP PUT 请求。</span><span class="sxs-lookup"><span data-stu-id="102a6-185">Modifying records is done using HTTP PUT requests.</span></span> <span data-ttu-id="102a6-186">除了此更改之外`Edit`方法是几乎与`Create`。</span><span class="sxs-lookup"><span data-stu-id="102a6-186">Other than this change, the `Edit` method is almost identical to `Create`.</span></span> <span data-ttu-id="102a6-187">请注意，如果未找到记录，则`Edit`操作，将返回`NotFound`(404) 响应。</span><span class="sxs-lookup"><span data-stu-id="102a6-187">Note that if the record isn't found, the `Edit` action will return a `NotFound` (404) response.</span></span>

<span data-ttu-id="102a6-188">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=48-69)]</span><span class="sxs-lookup"><span data-stu-id="102a6-188">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=48-69)]</span></span>

<span data-ttu-id="102a6-189">若要使用 Postman 进行测试，更改到 PUT 谓词。</span><span class="sxs-lookup"><span data-stu-id="102a6-189">To test with Postman, change the verb to PUT.</span></span> <span data-ttu-id="102a6-190">在请求正文中指定的更新的对象数据。</span><span class="sxs-lookup"><span data-stu-id="102a6-190">Specify the updated object data in the Body of the request.</span></span>

![显示 PUT 和响应的 postman 控制台](native-mobile-backend/_static/postman-put.png)

<span data-ttu-id="102a6-192">此方法返回`NoContent`(204) 响应成功时，为了与预先存在的 API 保持一致。</span><span class="sxs-lookup"><span data-stu-id="102a6-192">This method returns a `NoContent` (204) response when successful, for consistency with the pre-existing API.</span></span>

### <a name="deleting-items"></a><span data-ttu-id="102a6-193">删除项目</span><span class="sxs-lookup"><span data-stu-id="102a6-193">Deleting Items</span></span>

<span data-ttu-id="102a6-194">删除记录可以通过向服务发出删除请求并传递要删除项的 ID。</span><span class="sxs-lookup"><span data-stu-id="102a6-194">Deleting records is accomplished by making DELETE requests to the service, and passing the ID of the item to be deleted.</span></span> <span data-ttu-id="102a6-195">当不存在的项的请求将收到更新后，`NotFound`响应。</span><span class="sxs-lookup"><span data-stu-id="102a6-195">As with updates, requests for items that don't exist will receive `NotFound` responses.</span></span> <span data-ttu-id="102a6-196">请求成功，否则，会出现`NoContent`(204) 响应。</span><span class="sxs-lookup"><span data-stu-id="102a6-196">Otherwise, a successful request will get a `NoContent` (204) response.</span></span>

<span data-ttu-id="102a6-197">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=71-88)]</span><span class="sxs-lookup"><span data-stu-id="102a6-197">[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=71-88)]</span></span>

<span data-ttu-id="102a6-198">请注意，在测试的删除功能时，没有任何要求在请求正文中。</span><span class="sxs-lookup"><span data-stu-id="102a6-198">Note that when testing the delete functionality, nothing is required in the Body of the request.</span></span>

![显示一个删除和响应的 postman 控制台](native-mobile-backend/_static/postman-delete.png)

## <a name="common-web-api-conventions"></a><span data-ttu-id="102a6-200">常见的 Web API 约定</span><span class="sxs-lookup"><span data-stu-id="102a6-200">Common Web API Conventions</span></span>

<span data-ttu-id="102a6-201">开发你的应用程序的后端服务时，你将想要附带一组一致的约定或处理的跨领域问题的策略。</span><span class="sxs-lookup"><span data-stu-id="102a6-201">As you develop the backend services for your app, you will want to come up with a consistent set of conventions or policies for handling cross-cutting concerns.</span></span> <span data-ttu-id="102a6-202">例如，在上面所示服务中，为请求特定的记录，无法找到收到`NotFound`响应，而不是`BadRequest`响应。</span><span class="sxs-lookup"><span data-stu-id="102a6-202">For example, in the service shown above, requests for specific records that weren't found received a `NotFound` response, rather than a `BadRequest` response.</span></span> <span data-ttu-id="102a6-203">同样，对此服务中，始终检查的绑定的模型类型传递的命令`ModelState.IsValid`并返回`BadRequest`无效的模型类型。</span><span class="sxs-lookup"><span data-stu-id="102a6-203">Similarly, commands made to this service that passed in model bound types always checked `ModelState.IsValid` and returned a `BadRequest` for invalid model types.</span></span>

<span data-ttu-id="102a6-204">一旦您已经为您的 Api 来标识常见策略，你可以通常封装在[筛选器](../mvc/controllers/filters.md)。</span><span class="sxs-lookup"><span data-stu-id="102a6-204">Once you've identified a common policy for your APIs, you can usually encapsulate it in a [filter](../mvc/controllers/filters.md).</span></span> <span data-ttu-id="102a6-205">详细了解[如何封装 ASP.NET 核心 MVC 应用程序中的公用 API 策略](https://msdn.microsoft.com/magazine/mt767699.aspx)。</span><span class="sxs-lookup"><span data-stu-id="102a6-205">Learn more about [how to encapsulate common API policies in ASP.NET Core MVC applications](https://msdn.microsoft.com/magazine/mt767699.aspx).</span></span>
