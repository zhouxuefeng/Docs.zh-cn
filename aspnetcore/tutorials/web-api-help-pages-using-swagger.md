---
title: "使用 Swagger 的 ASP.NET Core Web API 帮助页"
author: spboyer
description: "本教程提供添加 Swagger 以生成文档的演练和针对 Web API 应用程序的帮助页。"
keywords: "ASP.NET Core, Swagger, Swashbuckle, 帮助页, Web API"
ms.author: spboyer
manager: wpickett
ms.date: 09/01/2017
ms.topic: article
ms.assetid: 54bb961d-29d9-4dee-8e2c-a93fc33c16f2
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: 8a87972a7394246ece2af3485d93739975ba5383
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2017
---
# <a name="aspnet-core-web-api-help-pages-using-swagger"></a><span data-ttu-id="b4013-104">使用 Swagger 的 ASP.NET Core Web API 帮助页</span><span class="sxs-lookup"><span data-stu-id="b4013-104">ASP.NET Core Web API Help Pages using Swagger</span></span>

<a name="web-api-help-pages-using-swagger"></a>

<span data-ttu-id="b4013-105">作者：[Shayne Boyer](https://twitter.com/spboyer) 和 [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="b4013-105">By [Shayne Boyer](https://twitter.com/spboyer) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="b4013-106">对于构建消费应用程序的开发人员来说，了解 API 的各种方法是一个挑战。</span><span class="sxs-lookup"><span data-stu-id="b4013-106">Understanding the various methods of an API can be a challenge for a developer when building a consuming application.</span></span>

<span data-ttu-id="b4013-107">使用包含 .NET Core 实现 [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) 的 [Swagger](https://swagger.io/) 为 Web API 生成优秀的文档和帮助页与添加多个 NuGet 包并修改 Startup.cs 一样简单。</span><span class="sxs-lookup"><span data-stu-id="b4013-107">Generating good documentation and help pages for your Web API, using [Swagger](https://swagger.io/) with the .NET Core implementation [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore), is as easy as adding a couple of NuGet packages and modifying the *Startup.cs*.</span></span>

* <span data-ttu-id="b4013-108">[Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) 是一个开源项目，用于生成 ASP.NET Core Web API 的 Swagger 文档。</span><span class="sxs-lookup"><span data-stu-id="b4013-108">[Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) is an open source project for generating Swagger documents for ASP.NET Core Web APIs.</span></span>

* <span data-ttu-id="b4013-109">[Swagger](https://swagger.io/) 是 RESTful API 的一种计算机可读表示形式，为交互式文档、客户端 SDK 生成和可发现性提供支持。</span><span class="sxs-lookup"><span data-stu-id="b4013-109">[Swagger](https://swagger.io/) is a machine-readable representation of a RESTful API that enables support for interactive documentation, client SDK generation, and discoverability.</span></span>

<span data-ttu-id="b4013-110">本教程以[使用 ASP.NET Core MVC 和 Visual Studio 构建你的第一个 Web API](xref:tutorials/first-web-api) 为示例进行构建。</span><span class="sxs-lookup"><span data-stu-id="b4013-110">This tutorial builds on the sample on [Building Your First Web API with ASP.NET Core MVC and Visual Studio](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="b4013-111">如果想要按步骤操作，请在 [https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/sample](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/sample) 中下载该示例。</span><span class="sxs-lookup"><span data-stu-id="b4013-111">If you'd like to follow along, download the sample at [https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/sample](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/sample).</span></span>

## <a name="getting-started"></a><span data-ttu-id="b4013-112">入门</span><span class="sxs-lookup"><span data-stu-id="b4013-112">Getting Started</span></span>

<span data-ttu-id="b4013-113">Swashbuckle 有三个主要组成部分：</span><span class="sxs-lookup"><span data-stu-id="b4013-113">There are three main components to Swashbuckle:</span></span>

* <span data-ttu-id="b4013-114">`Swashbuckle.AspNetCore.Swagger`：将 `SwaggerDocument` 对象公开为 JSON 终结点的 Swagger 对象模型和中间件。</span><span class="sxs-lookup"><span data-stu-id="b4013-114">`Swashbuckle.AspNetCore.Swagger`: a Swagger object model and middleware to expose `SwaggerDocument` objects as JSON endpoints.</span></span>

* <span data-ttu-id="b4013-115">`Swashbuckle.AspNetCore.SwaggerGen`：Swagger 生成器，从你的路由、控制器和模型直接生成 `SwaggerDocument` 对象。</span><span class="sxs-lookup"><span data-stu-id="b4013-115">`Swashbuckle.AspNetCore.SwaggerGen`: a Swagger generator that builds `SwaggerDocument` objects directly from your routes, controllers, and models.</span></span> <span data-ttu-id="b4013-116">它通常与 Swagger 终结点中间件结合，以自动公开 Swagger JSON。</span><span class="sxs-lookup"><span data-stu-id="b4013-116">It's typically combined with the Swagger endpoint middleware to automatically expose Swagger JSON.</span></span>

* <span data-ttu-id="b4013-117">`Swashbuckle.AspNetCore.SwaggerUI`：Swagger UI 工具的嵌入式版本，它解释 Swagger JSON 以构建描述 Web API 功能的可自定义的丰富体验。</span><span class="sxs-lookup"><span data-stu-id="b4013-117">`Swashbuckle.AspNetCore.SwaggerUI`: an embedded version of the Swagger UI tool which interprets Swagger JSON to build a rich, customizable experience for describing the Web API functionality.</span></span> <span data-ttu-id="b4013-118">它包括针对公共方法的内置测试工具。</span><span class="sxs-lookup"><span data-stu-id="b4013-118">It includes built-in test harnesses for the public methods.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="b4013-119">NuGet 包</span><span class="sxs-lookup"><span data-stu-id="b4013-119">NuGet Packages</span></span>

<span data-ttu-id="b4013-120">可以使用以下方法来添加 Swashbuckle：</span><span class="sxs-lookup"><span data-stu-id="b4013-120">Swashbuckle can be added with the following approaches:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b4013-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b4013-121">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b4013-122">从“程序包管理器控制台”窗口：</span><span class="sxs-lookup"><span data-stu-id="b4013-122">From the **Package Manager Console** window:</span></span>

    ```powershell
    Install-Package Swashbuckle.AspNetCore
    ```

* <span data-ttu-id="b4013-123">从“管理 NuGet 程序包”对话框中：</span><span class="sxs-lookup"><span data-stu-id="b4013-123">From the **Manage NuGet Packages** dialog:</span></span>

     * <span data-ttu-id="b4013-124">右键单击“解决方案资源管理器” > “管理 NuGet 包”中的项目</span><span class="sxs-lookup"><span data-stu-id="b4013-124">Right-click your project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
     * <span data-ttu-id="b4013-125">将“包源”设置为“nuget.org”</span><span class="sxs-lookup"><span data-stu-id="b4013-125">Set the **Package source** to "nuget.org"</span></span>
     * <span data-ttu-id="b4013-126">在搜索框中输入“Swashbuckle.AspNetCore”</span><span class="sxs-lookup"><span data-stu-id="b4013-126">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
     * <span data-ttu-id="b4013-127">从“浏览”选项卡中选择“Swashbuckle.AspNetCore”包，然后单击“安装”</span><span class="sxs-lookup"><span data-stu-id="b4013-127">Select the "Swashbuckle.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b4013-128">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b4013-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b4013-129">右键单击“Solution Pad” > “添加包...”中的“包”文件夹</span><span class="sxs-lookup"><span data-stu-id="b4013-129">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="b4013-130">将“添加包”窗口的“源”下拉列表设置为“nuget.org”</span><span class="sxs-lookup"><span data-stu-id="b4013-130">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="b4013-131">在搜索框中输入 Swashbuckle.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="b4013-131">Enter Swashbuckle.AspNetCore in the search box</span></span>
* <span data-ttu-id="b4013-132">从结果窗格中选择“Swashbuckle.AspNetCore”包，然后单击“添加包”</span><span class="sxs-lookup"><span data-stu-id="b4013-132">Select the Swashbuckle.AspNetCore package from the results pane and click **Add Package**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b4013-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b4013-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b4013-134">从“集成终端”中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="b4013-134">Run the following command from the **Integrated Terminal**:</span></span>

```console
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b4013-135">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b4013-135">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b4013-136">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="b4013-136">Run the following command:</span></span>

```console
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore
```

---

## <a name="add-and-configure-swagger-to-the-middleware"></a><span data-ttu-id="b4013-137">向中间件添加和配置 Swagger</span><span class="sxs-lookup"><span data-stu-id="b4013-137">Add and configure Swagger to the middleware</span></span>

<span data-ttu-id="b4013-138">将 Swagger 生成器添加到 Startup.cs 的 `ConfigureServices` 方法中的服务集合：</span><span class="sxs-lookup"><span data-stu-id="b4013-138">Add the Swagger generator to the services collection in the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup2.cs?name=snippet_ConfigureServices&highlight=7-10)]

<span data-ttu-id="b4013-139">为 `Info` 类添加以下 using 语句：</span><span class="sxs-lookup"><span data-stu-id="b4013-139">Add the following using statement for the `Info` class:</span></span>

```csharp
using Swashbuckle.AspNetCore.Swagger;
```

<span data-ttu-id="b4013-140">在 Startup.cs 的 `Configure` 方法中，启用中间件为生成的 JSON 文档和 SwaggerUI 提供服务：</span><span class="sxs-lookup"><span data-stu-id="b4013-140">In the `Configure` method of *Startup.cs*, enable the middleware for serving the generated JSON document and the SwaggerUI:</span></span>

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup2.cs?name=snippet_Configure&highlight=4,7-10)]

<span data-ttu-id="b4013-141">启动应用，并导航到 `http://localhost:<random_port>/swagger/v1/swagger.json`。</span><span class="sxs-lookup"><span data-stu-id="b4013-141">Launch the app, and navigate to `http://localhost:<random_port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="b4013-142">随即显示生成的用于描述终结点的文档。</span><span class="sxs-lookup"><span data-stu-id="b4013-142">The generated document describing the endpoints appears.</span></span>

<span data-ttu-id="b4013-143">注意：Microsoft Edge、Google Chrome 和 Firefox 在本机显示 JSON 文档。</span><span class="sxs-lookup"><span data-stu-id="b4013-143">**Note:** Microsoft Edge, Google Chrome, and Firefox display JSON documents natively.</span></span> <span data-ttu-id="b4013-144">提供可设置文档格式的部件版式扩展，使文档更易于阅读。</span><span class="sxs-lookup"><span data-stu-id="b4013-144">There are extensions for Chrome that format the document for easier reading.</span></span> <span data-ttu-id="b4013-145">为简洁起见，对下面的示例内容进行了缩减。</span><span class="sxs-lookup"><span data-stu-id="b4013-145">*The following example is reduced for brevity.*</span></span>

```json
{
   "swagger": "2.0",
   "info": {
       "version": "v1",
       "title": "API V1"
   },
   "basePath": "/",
   "paths": {
       "/api/Todo": {
           "get": {
               "tags": [
                   "Todo"
               ],
               "operationId": "ApiTodoGet",
               "consumes": [],
               "produces": [
                   "text/plain",
                   "application/json",
                   "text/json"
               ],
               "responses": {
                   "200": {
                       "description": "Success",
                       "schema": {
                           "type": "array",
                           "items": {
                               "$ref": "#/definitions/TodoItem"
                           }
                       }
                   }
                }
           },
           "post": {
               ...
           }
       },
       "/api/Todo/{id}": {
           "get": {
               ...
           },
           "put": {
               ...
           },
           "delete": {
               ...
   },
   "definitions": {
       "TodoItem": {
           "type": "object",
            "properties": {
                "id": {
                    "format": "int64",
                    "type": "integer"
                },
                "name": {
                    "type": "string"
                },
                "isComplete": {
                    "default": false,
                    "type": "boolean"
                }
            }
       }
   },
   "securityDefinitions": {}
}
```

<span data-ttu-id="b4013-146">此文档驱动 Swagger UI，可以通过导航到 `http://localhost:<random_port>/swagger` 进行查看：</span><span class="sxs-lookup"><span data-stu-id="b4013-146">This document drives the Swagger UI, which can be viewed by navigating to `http://localhost:<random_port>/swagger`:</span></span>

![Swagger UI](web-api-help-pages-using-swagger/_static/swagger-ui.png)

<span data-ttu-id="b4013-148">`TodoController` 中的每个公共操作方法都可以从 UI 中进行测试。</span><span class="sxs-lookup"><span data-stu-id="b4013-148">Each public action method in `TodoController` can be tested from the UI.</span></span> <span data-ttu-id="b4013-149">单击方法名称可以展开该部分。</span><span class="sxs-lookup"><span data-stu-id="b4013-149">Click a method name to expand the section.</span></span> <span data-ttu-id="b4013-150">添加所有必要的参数，然后单击“试试看!”。</span><span class="sxs-lookup"><span data-stu-id="b4013-150">Add any necessary parameters, and click "Try it out!".</span></span>

![示例 Swagger GET 测试](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

## <a name="customization--extensibility"></a><span data-ttu-id="b4013-152">自定义项和扩展性</span><span class="sxs-lookup"><span data-stu-id="b4013-152">Customization & Extensibility</span></span>

<span data-ttu-id="b4013-153">Swagger 提供了为对象模型进行归档和自定义 UI 以匹配你的主题的选项。</span><span class="sxs-lookup"><span data-stu-id="b4013-153">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

### <a name="api-info-and-description"></a><span data-ttu-id="b4013-154">API 信息和说明</span><span class="sxs-lookup"><span data-stu-id="b4013-154">API Info and Description</span></span>

<span data-ttu-id="b4013-155">传递给 `AddSwaggerGen` 方法的配置操作可用于添加信息，如作者、许可证和说明：</span><span class="sxs-lookup"><span data-stu-id="b4013-155">The configuration action passed to the `AddSwaggerGen` method can be used to add information such as the author, license, and description:</span></span>

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup.cs?range=20-30,36)]

<span data-ttu-id="b4013-156">下图描绘了显示版本信息的 Swagger UI：</span><span class="sxs-lookup"><span data-stu-id="b4013-156">The following image depicts the Swagger UI displaying the version information:</span></span>

![包含版本信息的 Swagger UI：说明、作者以及查看更多链接](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="b4013-158">XML 注释</span><span class="sxs-lookup"><span data-stu-id="b4013-158">XML Comments</span></span>

<span data-ttu-id="b4013-159">可使用以下方法启用 XML 注释：</span><span class="sxs-lookup"><span data-stu-id="b4013-159">XML comments can be enabled with the following approaches:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b4013-160">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b4013-160">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b4013-161">右键单击“解决方案资源管理器”中的项目，然后选择“属性”</span><span class="sxs-lookup"><span data-stu-id="b4013-161">Right-click the project in **Solution Explorer** and select **Properties**</span></span>
* <span data-ttu-id="b4013-162">查看“生成”选项卡的“输出”部分下的“XML 文档文件”框：</span><span class="sxs-lookup"><span data-stu-id="b4013-162">Check the **XML documentation file** box under the **Output** section of the **Build** tab:</span></span>

![项目属性的“生成”选项卡](web-api-help-pages-using-swagger/_static/swagger-xml-comments.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b4013-164">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b4013-164">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b4013-165">打开“项目选项”对话框 >“生成” > “编译器”</span><span class="sxs-lookup"><span data-stu-id="b4013-165">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="b4013-166">查看“常规选项”部分下的“生成 xml 文档”框：</span><span class="sxs-lookup"><span data-stu-id="b4013-166">Check the **Generate xml documentation** box under the **General Options** section:</span></span>

![项目选项的“常规选项”部分](web-api-help-pages-using-swagger/_static/swagger-xml-comments-mac.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b4013-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b4013-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b4013-169">将以下代码片段手动添加到 .csproj 文件：</span><span class="sxs-lookup"><span data-stu-id="b4013-169">Manually add the following snippet to the *.csproj* file:</span></span>

[!code-xml[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/TodoApi.csproj?range=7-9)]

---

<span data-ttu-id="b4013-170">配置 Swagger 以使用生成的 XML 文件。</span><span class="sxs-lookup"><span data-stu-id="b4013-170">Configure Swagger to use the generated XML file.</span></span> <span data-ttu-id="b4013-171">对于 Linux 或非 Windows 操作系统，文件名和路径区分大小写。</span><span class="sxs-lookup"><span data-stu-id="b4013-171">For Linux or non-Windows operating systems, file names and paths can be case sensitive.</span></span> <span data-ttu-id="b4013-172">例如，“ToDoApi.XML”文件可以在 Windows 上找到，但无法在 CentOS 上找到。</span><span class="sxs-lookup"><span data-stu-id="b4013-172">For example, a *ToDoApi.XML* file would be found on Windows but not CentOS.</span></span>

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup.cs?name=snippet_ConfigureServices&highlight=20-22)]

<span data-ttu-id="b4013-173">在前面的代码中，`ApplicationBasePath` 获取应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="b4013-173">In the preceding code, `ApplicationBasePath` gets the base path of the app.</span></span> <span data-ttu-id="b4013-174">基路径用于查找 XML 注释文件。</span><span class="sxs-lookup"><span data-stu-id="b4013-174">The base path is used to locate the XML comments file.</span></span> <span data-ttu-id="b4013-175">“TodoApi.xml”仅适用于本示例，因为生成的 XML 注释文件的名称基于应用程序名称。</span><span class="sxs-lookup"><span data-stu-id="b4013-175">*TodoApi.xml* only works for this example, since the name of the generated XML comments file is based on the application name.</span></span>

<span data-ttu-id="b4013-176">通过向节标题添加说明，将三斜杠注释添加到方法增强了 Swagger UI：</span><span class="sxs-lookup"><span data-stu-id="b4013-176">Adding the triple-slash comments to the method enhances the Swagger UI by adding the description to the section header:</span></span>

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Delete&highlight=2)]

![显示 DELETE 方法的 XML 注释“删除特定 TodoItem”](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

<span data-ttu-id="b4013-179">UI 由生成的 JSON 文件驱动，还包含以下这些注释：</span><span class="sxs-lookup"><span data-stu-id="b4013-179">The UI is driven by the generated JSON file, which also contains these comments:</span></span>

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```

<span data-ttu-id="b4013-180">将 [<remarks>](https://docs.microsoft.com/dotnet/csharp/programming-guide/xmldoc/remarks) 标记添加到 `Create` 操作方法文档。</span><span class="sxs-lookup"><span data-stu-id="b4013-180">Add a [<remarks>](https://docs.microsoft.com/dotnet/csharp/programming-guide/xmldoc/remarks) tag to the `Create` action method documentation.</span></span> <span data-ttu-id="b4013-181">它可以补充 `<summary>` 标记中指定的信息，并提供更可靠的 Swagger UI。</span><span class="sxs-lookup"><span data-stu-id="b4013-181">It supplements information specified in the `<summary>` tag and provides a more robust Swagger UI.</span></span> <span data-ttu-id="b4013-182">`<remarks>` 标记内容可包含文本、JSON 或 XML。</span><span class="sxs-lookup"><span data-stu-id="b4013-182">The `<remarks>` tag content can consist of text, JSON, or XML.</span></span>

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

<span data-ttu-id="b4013-183">请注意带这些附加注释的 UI 增强。</span><span class="sxs-lookup"><span data-stu-id="b4013-183">Notice the UI enhancements with these additional comments.</span></span>

![显示包含其他注释的 Swagger UI](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="b4013-185">数据注释</span><span class="sxs-lookup"><span data-stu-id="b4013-185">Data Annotations</span></span>

<span data-ttu-id="b4013-186">使用 `System.ComponentModel.DataAnnotations` 中找到的属性来修饰模型，以帮助驱动 Swagger UI 组件。</span><span class="sxs-lookup"><span data-stu-id="b4013-186">Decorate the model with attributes, found in `System.ComponentModel.DataAnnotations`, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="b4013-187">将 `[Required]` 属性添加到 `TodoItem` 类的 `Name` 属性：</span><span class="sxs-lookup"><span data-stu-id="b4013-187">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Models/TodoItem.cs?highlight=10)]

<span data-ttu-id="b4013-188">此属性的状态更改 UI 行为并更改基础 JSON 架构：</span><span class="sxs-lookup"><span data-stu-id="b4013-188">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

<span data-ttu-id="b4013-189">将 `[Produces("application/json")]` 属性添加到 API 控制器。</span><span class="sxs-lookup"><span data-stu-id="b4013-189">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="b4013-190">这样做的目的是声明控制器的操作支持返回 application/json 的内容类型：</span><span class="sxs-lookup"><span data-stu-id="b4013-190">Its purpose is to declare that the controller's actions support a return a content type of *application/json*:</span></span>

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Controllers/TodoController.cs?name=snippet_TodoController&highlight=3)]

<span data-ttu-id="b4013-191">“响应内容类型”下拉列表选此内容类型作为控制器的默认 GET 操作：</span><span class="sxs-lookup"><span data-stu-id="b4013-191">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![包含默认响应内容类型的 Swagger UI](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

<span data-ttu-id="b4013-193">随着 Web API 中的数据注释的使用越来越多，UI 和 API 帮助页变得更具说明性和更为有用。</span><span class="sxs-lookup"><span data-stu-id="b4013-193">As the usage of data annotations in the Web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describing-response-types"></a><span data-ttu-id="b4013-194">描述响应类型</span><span class="sxs-lookup"><span data-stu-id="b4013-194">Describing Response Types</span></span>

<span data-ttu-id="b4013-195">消费应用程序开发人员最关心的问题是返回的内容和具体的响应类型和错误代码（如果不标准）。</span><span class="sxs-lookup"><span data-stu-id="b4013-195">Consuming developers are most concerned with what is returned &mdash; specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="b4013-196">这些问题在 XML 注释和数据批注中进行处理。</span><span class="sxs-lookup"><span data-stu-id="b4013-196">These are handled in the XML comments and data annotations.</span></span>

<span data-ttu-id="b4013-197">`Create` 操作在成功时返回 `201 Created`，或在发布的请求正文为 null 时返回 `400 Bad Request`。</span><span class="sxs-lookup"><span data-stu-id="b4013-197">The `Create` action returns `201 Created` on success or `400 Bad Request` when the posted request body is null.</span></span> <span data-ttu-id="b4013-198">如果 Swagger UI 中没有提供合适的文档，那么使用者会缺少对这些预期结果的了解。</span><span class="sxs-lookup"><span data-stu-id="b4013-198">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="b4013-199">在下面的示例中，通过添加突出显示的行解决了此问题：</span><span class="sxs-lookup"><span data-stu-id="b4013-199">That problem is fixed by adding the highlighted lines in the following example:</span></span>

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

<span data-ttu-id="b4013-200">Swagger UI 现在清楚地记录预期的 HTTP 响应代码：</span><span class="sxs-lookup"><span data-stu-id="b4013-200">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![Swagger UI 针对“响应消息”下的状态代码和原因显示 POST 响应类描述“返回新建的待办事项”和“400 - 如果该项为 null”](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

### <a name="customizing-the-ui"></a><span data-ttu-id="b4013-202">自定义 UI</span><span class="sxs-lookup"><span data-stu-id="b4013-202">Customizing the UI</span></span>

<span data-ttu-id="b4013-203">Stock UI 兼具功能性和可演示性，但是，为你的 API 构建文档页时，你希望呈现你的品牌或主题。</span><span class="sxs-lookup"><span data-stu-id="b4013-203">The stock UI is both functional and presentable; however, when building documentation pages for your API, you want it to represent your brand or theme.</span></span> <span data-ttu-id="b4013-204">使用 Swashbuckle 组件完成该任务需要添加资源以服务静态文件，然后构建文件夹结构以托管这些文件。</span><span class="sxs-lookup"><span data-stu-id="b4013-204">Accomplishing that task with the Swashbuckle components requires adding the resources to serve static files and then building the folder structure to host those files.</span></span>

<span data-ttu-id="b4013-205">如果面向 .NET Framework，则需要向项目添加 `Microsoft.AspNetCore.StaticFiles` NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="b4013-205">If targeting .NET Framework, add the `Microsoft.AspNetCore.StaticFiles` NuGet package to the project:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

<span data-ttu-id="b4013-206">启用静态文件中间件：</span><span class="sxs-lookup"><span data-stu-id="b4013-206">Enable the static files middleware:</span></span>

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup.cs?name=snippet_Configure&highlight=3)]

<span data-ttu-id="b4013-207">从 [Swagger UI GitHub 存储库](https://github.com/swagger-api/swagger-ui/tree/2.x/dist)中获取 dist 文件夹的内容。</span><span class="sxs-lookup"><span data-stu-id="b4013-207">Acquire the contents of the *dist* folder from the [Swagger UI GitHub repository](https://github.com/swagger-api/swagger-ui/tree/2.x/dist).</span></span> <span data-ttu-id="b4013-208">此文件夹包含 Swagger UI 页必需的资产。</span><span class="sxs-lookup"><span data-stu-id="b4013-208">This folder contains the necessary assets for the Swagger UI page.</span></span>

<span data-ttu-id="b4013-209">创建 wwwroot/swagger/ui 文件夹，然后将 dist 文件夹的内容复制到其中。</span><span class="sxs-lookup"><span data-stu-id="b4013-209">Create a *wwwroot/swagger/ui* folder, and copy into it the contents of the *dist* folder.</span></span>

<span data-ttu-id="b4013-210">使用下面的 CSS 创建 wwwroot/swagger/ui/css/custom.css 文件以自定义页面标题：</span><span class="sxs-lookup"><span data-stu-id="b4013-210">Create a *wwwroot/swagger/ui/css/custom.css* file with the following CSS to customize the page header:</span></span>

[!code-css[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/wwwroot/swagger/ui/css/custom.css)]

<span data-ttu-id="b4013-211">引用 index.html 文件中的“custom.css”：</span><span class="sxs-lookup"><span data-stu-id="b4013-211">Reference *custom.css* in the *index.html* file:</span></span>

[!code-html[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/wwwroot/swagger/ui/index.html?range=14)]

<span data-ttu-id="b4013-212">浏览到 `http://localhost:<random_port>/swagger/ui/index.html` 中的 index.html 页。</span><span class="sxs-lookup"><span data-stu-id="b4013-212">Browse to the *index.html* page at `http://localhost:<random_port>/swagger/ui/index.html`.</span></span> <span data-ttu-id="b4013-213">在标题文本框中输入 `http://localhost:<random_port>/swagger/v1/swagger.json`，然后单击“浏览”按钮。</span><span class="sxs-lookup"><span data-stu-id="b4013-213">Enter `http://localhost:<random_port>/swagger/v1/swagger.json` in the header's textbox, and click the **Explore** button.</span></span> <span data-ttu-id="b4013-214">生成的页面如下所示：</span><span class="sxs-lookup"><span data-stu-id="b4013-214">The resulting page looks as follows:</span></span>

![使用自定义标题的 Swagger UI](web-api-help-pages-using-swagger/_static/custom-header.png)

<span data-ttu-id="b4013-216">还可以对页面执行更多操作。</span><span class="sxs-lookup"><span data-stu-id="b4013-216">There is much more you can do with the page.</span></span> <span data-ttu-id="b4013-217">在 [Swagger UI GitHub 存储库](https://github.com/swagger-api/swagger-ui)中查看 UI 资源的完整功能。</span><span class="sxs-lookup"><span data-stu-id="b4013-217">See the full capabilities for the UI resources at the [Swagger UI GitHub repository](https://github.com/swagger-api/swagger-ui).</span></span>
