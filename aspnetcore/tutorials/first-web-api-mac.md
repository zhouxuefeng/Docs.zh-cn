---
title: "使用 ASP.NET Core 和 Visual Studio for Mac 创建 Web API"
description: "使用 ASP.NET Core MVC 和 Visual Studio for Mac 创建 Web API"
author: rick-anderson
ms.author: riande
ms.date: 09/15/2017
ms.topic: get-started-article
ms.prod: asp.net-core
uid: tutorials/first-web-api-mac
helpviewer_heywords: ASP.NET Core, WebAPI, Web API, REST, mac, macOS, HTTP, Service, HTTP Service
ms.technology: aspnet
keywords: "ASP.NET Core, WebAPI, Web API, REST, mac, macOS, HTTP, 服务, HTTP 服务"
manager: wpickett
ms.openlocfilehash: 6835cdefcc001452a3ffc8f4fd6a2f55f7274692
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2017
---
# <a name="create-a-web-api-with-aspnet-core-mvc-and-visual-studio-for-mac"></a><span data-ttu-id="efb40-104">使用 ASP.NET Core MVC 和 Visual Studio for Mac 创建 Web API</span><span class="sxs-lookup"><span data-stu-id="efb40-104">Create a Web API with ASP.NET Core MVC and Visual Studio for Mac</span></span>

<span data-ttu-id="efb40-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="efb40-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="efb40-106">在本教程中，将生成用于管理“待办事项”列表的 Web API。</span><span class="sxs-lookup"><span data-stu-id="efb40-106">In this tutorial, you’ll build a web API for managing a list of "to-do" items.</span></span> <span data-ttu-id="efb40-107">不会生成 UI。</span><span class="sxs-lookup"><span data-stu-id="efb40-107">You won’t build a UI.</span></span>

<span data-ttu-id="efb40-108">本教程有三个版本：</span><span class="sxs-lookup"><span data-stu-id="efb40-108">There are 3 versions of this tutorial:</span></span>

* <span data-ttu-id="efb40-109">macOS：使用 Visual Studio for Mac 创建 Web API（本教程）</span><span class="sxs-lookup"><span data-stu-id="efb40-109">macOS: Web API with Visual Studio for Mac (This tutorial)</span></span>
* <span data-ttu-id="efb40-110">Windows：[使用 Visual Studio for Windows 创建 Web API](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="efb40-110">Windows: [Web API with Visual Studio for Windows](xref:tutorials/first-web-api)</span></span>
* <span data-ttu-id="efb40-111">macOS、Linux、Windows：[使用 Visual Studio Code 创建 Web API](xref:tutorials/web-api-vsc)</span><span class="sxs-lookup"><span data-stu-id="efb40-111">macOS, Linux, Windows: [Web API with Visual Studio Code](xref:tutorials/web-api-vsc)</span></span>

<!-- WARNING: The code AND images in this doc are used by uid: tutorials/web-api-vsc, tutorials/first-web-api-mac and tutorials/first-web-api. If you change any code/images in this tutorial, update uid: tutorials/web-api-vsc -->

[!INCLUDE[template files](../includes/webApi/intro.md)]

* <span data-ttu-id="efb40-112">请参阅 [Mac 或 Linux 上的 ASP.NET Core MVC 介绍](xref:tutorials/first-mvc-app-xplat/index)，获取使用永久数据库的示例。</span><span class="sxs-lookup"><span data-stu-id="efb40-112">See [Introduction to ASP.NET Core MVC on Mac or Linux](xref:tutorials/first-mvc-app-xplat/index) for an example that uses a persistent database.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="efb40-113">先决条件</span><span class="sxs-lookup"><span data-stu-id="efb40-113">Prerequisites</span></span>

<span data-ttu-id="efb40-114">安装以下组件：</span><span class="sxs-lookup"><span data-stu-id="efb40-114">Install the following:</span></span>

- [<span data-ttu-id="efb40-115">.NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="efb40-115">.NET Core SDK</span></span>](https://www.microsoft.com/net/core#macos)  
- [<span data-ttu-id="efb40-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="efb40-116">Visual Studio for Mac</span></span>](https://www.visualstudio.com/vs/visual-studio-mac/)

## <a name="create-the-project"></a><span data-ttu-id="efb40-117">创建项目</span><span class="sxs-lookup"><span data-stu-id="efb40-117">Create the project</span></span>

<span data-ttu-id="efb40-118">在 Visual Studio 中，选择“文件”>“新建解决方案”。</span><span class="sxs-lookup"><span data-stu-id="efb40-118">From Visual Studio, select **File > New Solution**.</span></span>

![macOS“新建解决方案”](first-web-api-mac/_static/sln.png)

<span data-ttu-id="efb40-120">选择“.NET Core App”>“ASP.NET Core Web API”>“下一步”。</span><span class="sxs-lookup"><span data-stu-id="efb40-120">Select **.NET Core App >  ASP.NET Core Web API > Next**.</span></span>

![macOS“新建项目”对话框](first-web-api-mac/_static/1.png)

<span data-ttu-id="efb40-122">输入“TodoApi”作为“项目名称”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="efb40-122">Enter **TodoApi** for the **Project Name**, and then select Create.</span></span>

![配置对话框](first-web-api-mac/_static/2.png)

### <a name="launch-the-app"></a><span data-ttu-id="efb40-124">启动应用</span><span class="sxs-lookup"><span data-stu-id="efb40-124">Launch the app</span></span>

<span data-ttu-id="efb40-125">在 Visual Studio 中，选择“运行”>“开始执行(调试)”启动应用。</span><span class="sxs-lookup"><span data-stu-id="efb40-125">In Visual Studio, select **Run > Start With Debugging** to launch the app.</span></span> <span data-ttu-id="efb40-126">Visual Studio 启动浏览器并导航到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="efb40-126">Visual Studio launches a browser and navigates to `http://localhost:5000`.</span></span> <span data-ttu-id="efb40-127">将收到 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="efb40-127">You get an HTTP 404 (Not Found) error.</span></span>  <span data-ttu-id="efb40-128">将 URL 更改为 `http://localhost:port/api/values`。</span><span class="sxs-lookup"><span data-stu-id="efb40-128">Change the URL to `http://localhost:port/api/values`.</span></span> <span data-ttu-id="efb40-129">将显示 `ValuesController` 数据：</span><span class="sxs-lookup"><span data-stu-id="efb40-129">The `ValuesController` data will be displayed:</span></span>

```
["value1","value2"]
```

### <a name="add-support-for-entity-framework-core"></a><span data-ttu-id="efb40-130">添加对 Entity Framework Core 的支持</span><span class="sxs-lookup"><span data-stu-id="efb40-130">Add support for Entity Framework Core</span></span>

<span data-ttu-id="efb40-131">安装 [Entity Framework Core InMemory](https://docs.microsoft.com/ef/core/providers/in-memory/) 数据库提供程序。</span><span class="sxs-lookup"><span data-stu-id="efb40-131">Install the [Entity Framework Core InMemory](https://docs.microsoft.com/ef/core/providers/in-memory/) database provider.</span></span> <span data-ttu-id="efb40-132">此数据库提供程序允许将 Entity Framework Core 和内存数据库一起使用。</span><span class="sxs-lookup"><span data-stu-id="efb40-132">This database provider allows Entity Framework Core to be used with an in-memory database.</span></span>

* <span data-ttu-id="efb40-133">从“项目”菜单中，选择“添加 NuGet 包”。</span><span class="sxs-lookup"><span data-stu-id="efb40-133">From the **Project** menu, select **Add NuGet Packages**.</span></span> 

  *  <span data-ttu-id="efb40-134">或者，可以右键单击“依赖项”，然后选择“添加包”。</span><span class="sxs-lookup"><span data-stu-id="efb40-134">Alternately, you can right-click **Dependencies**, and then select **Add Packages**.</span></span>

* <span data-ttu-id="efb40-135">在搜索框中输入 `EntityFrameworkCore.InMemory`。</span><span class="sxs-lookup"><span data-stu-id="efb40-135">Enter `EntityFrameworkCore.InMemory` in the search box.</span></span>
* <span data-ttu-id="efb40-136">选择 `Microsoft.EntityFrameworkCore.InMemory`，然后选择“添加包”。</span><span class="sxs-lookup"><span data-stu-id="efb40-136">Select `Microsoft.EntityFrameworkCore.InMemory`, and then select **Add Package**.</span></span>

### <a name="add-a-model-class"></a><span data-ttu-id="efb40-137">添加模型类</span><span class="sxs-lookup"><span data-stu-id="efb40-137">Add a model class</span></span>

<span data-ttu-id="efb40-138">模型是表示应用程序中的数据的对象。</span><span class="sxs-lookup"><span data-stu-id="efb40-138">A model is an object that represents the data in your application.</span></span> <span data-ttu-id="efb40-139">在此示例中，唯一的模型是待办事项。</span><span class="sxs-lookup"><span data-stu-id="efb40-139">In this case, the only model is a to-do item.</span></span>

<span data-ttu-id="efb40-140">添加名为“Models”的文件夹。</span><span class="sxs-lookup"><span data-stu-id="efb40-140">Add a folder named *Models*.</span></span> <span data-ttu-id="efb40-141">在解决方案资源管理器中，右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="efb40-141">In Solution Explorer, right-click the project.</span></span> <span data-ttu-id="efb40-142">选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="efb40-142">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="efb40-143">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="efb40-143">Name the folder *Models*.</span></span>

![新建文件夹](first-web-api-mac/_static/folder.png)

<span data-ttu-id="efb40-145">注意：可将模型类置于项目的任意位置，但按照惯例会使用“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="efb40-145">Note: You can put model classes anywhere in your project, but the *Models* folder is used by convention.</span></span>

<span data-ttu-id="efb40-146">添加 `TodoItem` 类。</span><span class="sxs-lookup"><span data-stu-id="efb40-146">Add a `TodoItem` class.</span></span> <span data-ttu-id="efb40-147">右键单击“Models”文件夹，然后选择“添加”>“新建文件”>“常规”>“空类”。</span><span class="sxs-lookup"><span data-stu-id="efb40-147">Right-click the *Models* folder and select **Add > New File > General > Empty Class**.</span></span> <span data-ttu-id="efb40-148">将此类命名为 `TodoItem`，然后选择“新建”。</span><span class="sxs-lookup"><span data-stu-id="efb40-148">Name the class `TodoItem`, and then select **New**.</span></span>

<span data-ttu-id="efb40-149">将生成的代码替换为：</span><span class="sxs-lookup"><span data-stu-id="efb40-149">Replace the generated code with:</span></span>

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="efb40-150">创建 `TodoItem` 时，数据库将生成 `Id`。</span><span class="sxs-lookup"><span data-stu-id="efb40-150">The database generates the `Id` when a `TodoItem` is created.</span></span>

### <a name="create-the-database-context"></a><span data-ttu-id="efb40-151">创建数据库上下文</span><span class="sxs-lookup"><span data-stu-id="efb40-151">Create the database context</span></span>

<span data-ttu-id="efb40-152">数据库上下文是为给定数据模型协调 Entity Framework 功能的主类。</span><span class="sxs-lookup"><span data-stu-id="efb40-152">The *database context* is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="efb40-153">将通过从 `Microsoft.EntityFrameworkCore.DbContext` 类派生的方式创建此类。</span><span class="sxs-lookup"><span data-stu-id="efb40-153">You create this class by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

<span data-ttu-id="efb40-154">将 `TodoContext` 类添加到“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="efb40-154">Add a `TodoContext` class to the *Models* folder.</span></span>

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoContext.cs)]

[!INCLUDE[Register the database context](../includes/webApi/register_dbContext.md)]

## <a name="add-a-controller"></a><span data-ttu-id="efb40-155">添加控制器</span><span class="sxs-lookup"><span data-stu-id="efb40-155">Add a controller</span></span>

<span data-ttu-id="efb40-156">在解决方案资源管理器的“控制器”文件夹中，添加 `TodoController` 类。</span><span class="sxs-lookup"><span data-stu-id="efb40-156">In Solution Explorer, in the *Controllers* folder, add the class `TodoController`.</span></span>

<span data-ttu-id="efb40-157">将生成的代码替换为以下内容（并添加右大括号）：</span><span class="sxs-lookup"><span data-stu-id="efb40-157">Replace the generated code with the following (and add closing braces):</span></span>

[!INCLUDE[code and get todo items](../includes/webApi/getTodoItems.md)]

### <a name="launch-the-app"></a><span data-ttu-id="efb40-158">启动应用</span><span class="sxs-lookup"><span data-stu-id="efb40-158">Launch the app</span></span>

<span data-ttu-id="efb40-159">在 Visual Studio 中，选择“运行”>“开始执行(调试)”启动应用。</span><span class="sxs-lookup"><span data-stu-id="efb40-159">In Visual Studio, select **Run > Start With Debugging** to launch the app.</span></span> <span data-ttu-id="efb40-160">Visual Studio 启动浏览器并导航到 `http://localhost:port`，其中“端口”是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="efb40-160">Visual Studio launches a browser and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span> <span data-ttu-id="efb40-161">将收到 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="efb40-161">You get an HTTP 404 (Not Found) error.</span></span>  <span data-ttu-id="efb40-162">将 URL 更改为 `http://localhost:port/api/values`。</span><span class="sxs-lookup"><span data-stu-id="efb40-162">Change the URL to `http://localhost:port/api/values`.</span></span> <span data-ttu-id="efb40-163">将显示 `ValuesController` 数据：</span><span class="sxs-lookup"><span data-stu-id="efb40-163">The `ValuesController` data will be displayed:</span></span>

```
["value1","value2"]
```

<span data-ttu-id="efb40-164">导航到 `http://localhost:port/api/todo` 处的 `Todo` 控制器：</span><span class="sxs-lookup"><span data-stu-id="efb40-164">Navigate to the `Todo` controller at`http://localhost:port/api/todo`:</span></span>

```
[{"key":1,"name":"Item1","isComplete":false}]
```

## <a name="implement-the-other-crud-operations"></a><span data-ttu-id="efb40-165">实现其他的 CRUD 操作</span><span class="sxs-lookup"><span data-stu-id="efb40-165">Implement the other CRUD operations</span></span>

<span data-ttu-id="efb40-166">我们将向控制器添加 `Create`、`Update` 和 `Delete` 方法。</span><span class="sxs-lookup"><span data-stu-id="efb40-166">We'll add `Create`, `Update`, and `Delete` methods to the controller.</span></span> <span data-ttu-id="efb40-167">这些是主题的变体，因此在这里只提供代码并突出显示主要的差异。</span><span class="sxs-lookup"><span data-stu-id="efb40-167">These are variations on a theme, so I'll just show the code and highlight the main differences.</span></span> <span data-ttu-id="efb40-168">添加或更改代码后生成项目。</span><span class="sxs-lookup"><span data-stu-id="efb40-168">Build the project after adding or changing code.</span></span>

### <a name="create"></a><span data-ttu-id="efb40-169">创建</span><span class="sxs-lookup"><span data-stu-id="efb40-169">Create</span></span>

[!code-csharp[Main](first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="efb40-170">这是 HTTP POST 方法，由 [`[HttpPost]`](/aspnet/core/api/microsoft.aspnetcore.mvc.httppostattribute) 特性指示的。</span><span class="sxs-lookup"><span data-stu-id="efb40-170">This is an HTTP POST method, indicated by the [`[HttpPost]`](/aspnet/core/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="efb40-171">[`[FromBody]`](/aspnet/core/api/microsoft.aspnetcore.mvc.frombodyattribute) 特性告诉 MVC 从 HTTP 请求正文获取待办事项的值。</span><span class="sxs-lookup"><span data-stu-id="efb40-171">The [`[FromBody]`](/aspnet/core/api/microsoft.aspnetcore.mvc.frombodyattribute) attribute tells MVC to get the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="efb40-172">`CreatedAtRoute` 方法返回 201 响应，这是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="efb40-172">The `CreatedAtRoute` method returns a 201 response, which is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="efb40-173">`CreatedAtRoute` 还会向响应添加位置标头。</span><span class="sxs-lookup"><span data-stu-id="efb40-173">`CreatedAtRoute` also adds a Location header to the response.</span></span> <span data-ttu-id="efb40-174">位置标头指定新建的待办事项的 URI。</span><span class="sxs-lookup"><span data-stu-id="efb40-174">The Location header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="efb40-175">请参阅 [10.2.2 201 已创建](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="efb40-175">See [10.2.2 201 Created](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

### <a name="use-postman-to-send-a-create-request"></a><span data-ttu-id="efb40-176">使用 Postman 发送创建请求</span><span class="sxs-lookup"><span data-stu-id="efb40-176">Use Postman to send a Create request</span></span>

* <span data-ttu-id="efb40-177">启动应用（“运行”>“开始执行(调试)”）。</span><span class="sxs-lookup"><span data-stu-id="efb40-177">Start the app (**Run > Start With Debugging**).</span></span>
* <span data-ttu-id="efb40-178">启动 Postman。</span><span class="sxs-lookup"><span data-stu-id="efb40-178">Start Postman.</span></span>

![Postman 控制台](first-web-api/_static/pmc.png)

* <span data-ttu-id="efb40-180">将 HTTP 方法设置为 `POST`</span><span class="sxs-lookup"><span data-stu-id="efb40-180">Set the HTTP method to `POST`</span></span>
* <span data-ttu-id="efb40-181">选择“正文”单选按钮</span><span class="sxs-lookup"><span data-stu-id="efb40-181">Select the **Body** radio button</span></span>
* <span data-ttu-id="efb40-182">选择“原始”单选按钮</span><span class="sxs-lookup"><span data-stu-id="efb40-182">Select the **raw** radio button</span></span>
* <span data-ttu-id="efb40-183">将类型设置为 JSON</span><span class="sxs-lookup"><span data-stu-id="efb40-183">Set the type to JSON</span></span>
* <span data-ttu-id="efb40-184">在键值编辑器中，输入一个待办事项，例如</span><span class="sxs-lookup"><span data-stu-id="efb40-184">In the key-value editor, enter a Todo item such as</span></span>

```json
{
    "name":"walk dog",
    "isComplete":true
}
```

* <span data-ttu-id="efb40-185">选择“发送”</span><span class="sxs-lookup"><span data-stu-id="efb40-185">Select **Send**</span></span>

* <span data-ttu-id="efb40-186">选择下窗格中的“标头”选项卡，然后复制“位置”标头：</span><span class="sxs-lookup"><span data-stu-id="efb40-186">Select the Headers tab in the lower pane and copy the **Location** header:</span></span>

![Postman 控制台的“标头”选项卡](first-web-api/_static/pmget.png)

<span data-ttu-id="efb40-188">可以使用此位置标头 URI 访问刚刚创建的资源。</span><span class="sxs-lookup"><span data-stu-id="efb40-188">You can use the Location header URI to access the resource you just created.</span></span> <span data-ttu-id="efb40-189">重新调用创建名为 `"GetTodo"` 路由时使用的 `GetById` 方法：</span><span class="sxs-lookup"><span data-stu-id="efb40-189">Recall the `GetById` method created the `"GetTodo"` named route:</span></span>

```csharp
[HttpGet("{id}", Name = "GetTodo")]
public IActionResult GetById(string id)
```

### <a name="update"></a><span data-ttu-id="efb40-190">更新</span><span class="sxs-lookup"><span data-stu-id="efb40-190">Update</span></span>

[!code-csharp[Main](first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="efb40-191">`Update` 与 `Create` 类似，但是使用的是 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="efb40-191">`Update` is similar to `Create`, but uses HTTP PUT.</span></span> <span data-ttu-id="efb40-192">响应是 [204（无内容）](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="efb40-192">The response is [204 (No Content)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="efb40-193">根据 HTTP 规范，PUT 请求需要客户端发送整个更新的实体，而不仅仅是增量。</span><span class="sxs-lookup"><span data-stu-id="efb40-193">According to the HTTP spec, a PUT request requires the client to send the entire updated entity, not just the deltas.</span></span> <span data-ttu-id="efb40-194">若要支持部分更新，请使用 HTTP PATCH。</span><span class="sxs-lookup"><span data-stu-id="efb40-194">To support partial updates, use HTTP PATCH.</span></span>

```json
{
  "key": 1,
  "name": "walk dog",
  "isComplete": true
}
```

![显示 204（无内容）响应的 Postman 控制台](first-web-api/_static/pmcput.png)

### <a name="delete"></a><span data-ttu-id="efb40-196">删除</span><span class="sxs-lookup"><span data-stu-id="efb40-196">Delete</span></span>

[!code-csharp[Main](first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="efb40-197">响应是 [204（无内容）](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="efb40-197">The response is [204 (No Content)](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

![显示 204（无内容）响应的 Postman 控制台](first-web-api/_static/pmd.png)

## <a name="next-steps"></a><span data-ttu-id="efb40-199">后续步骤</span><span class="sxs-lookup"><span data-stu-id="efb40-199">Next steps</span></span>

* [<span data-ttu-id="efb40-200">路由到控制器操作</span><span class="sxs-lookup"><span data-stu-id="efb40-200">Routing to Controller Actions</span></span>](xref:mvc/controllers/routing)
* <span data-ttu-id="efb40-201">有关部署 API 的信息，请参阅[发布和部署](../publishing/index.md)。</span><span class="sxs-lookup"><span data-stu-id="efb40-201">For information about deploying your API, see [Publishing and Deployment](../publishing/index.md).</span></span>
* [<span data-ttu-id="efb40-202">查看或下载示例代码</span><span class="sxs-lookup"><span data-stu-id="efb40-202">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/sample)
* [<span data-ttu-id="efb40-203">Postman</span><span class="sxs-lookup"><span data-stu-id="efb40-203">Postman</span></span>](https://www.getpostman.com/)
* [<span data-ttu-id="efb40-204">Fiddler</span><span class="sxs-lookup"><span data-stu-id="efb40-204">Fiddler</span></span>](https://www.telerik.com/download/fiddler)
