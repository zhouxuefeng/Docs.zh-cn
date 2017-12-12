---
title: "创建本机移动应用程序的后端服务"
author: ardalis
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 3b6a32f2-5af9-4ede-9b7f-17ab300526d0
ms.technology: aspnet
ms.prod: asp.net-core
uid: mobile/native-mobile-backend
ms.openlocfilehash: be1cd9f4fe41f1a79669975cb6a89439cdd9e5c7
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
# <a name="creating-backend-services-for-native-mobile-applications"></a>创建本机移动应用程序的后端服务

通过[Steve Smith](https://ardalis.com/)

移动应用可以轻松地与 ASP.NET Core 后端服务进行通信。

[查看或下载后端服务的示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/mobile/native-mobile-backend/sample)

## <a name="the-sample-native-mobile-app"></a>示例本机移动应用程序

本教程演示如何创建使用 ASP.NET 核心 MVC 以支持本机移动应用的后端服务。 它使用[Xamarin 窗体 ToDoRest 应用](https://developer.xamarin.com/guides/xamarin-forms/web-services/consuming/rest/)作为其本机客户端，其中包括 Android、 iOS、 Windows 通用和 Window Phone 设备的单独本机客户端。 你可以遵循链接本教程中，若要创建本机应用程序 （并安装所需的可用 Xamarin 工具），以及下载 Xamarin 示例解决方案。 Xamarin 示例包含一个 ASP.NET Web API 2 服务项目，本文中的 ASP.NET Core 应用替换 （需要进行任何更改客户端）。

![在 Android 智能手机上运行的执行操作的 Rest 应用程序](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a>功能

ToDoRest 应用支持列出、 添加、 删除和更新待办事项。 每个项都有一个 ID、 名称、 说明以及，该值指示是否已完成的工作尚未属性。

主视图的项，如上所示，列出每个项的名称，并指示是否它是具有选中标记。

点击`+`图标可打开添加项对话框：

![添加项对话框](native-mobile-backend/_static/todo-android-new-item.png)

点击主列表屏幕上的项将打开在其中可以修改项的名称、 说明以及完成的设置，或删除的项目，可以编辑对话框：

![编辑项对话框](native-mobile-backend/_static/todo-android-edit-item.png)

此示例是默认配置为使用后端服务托管在 developer.xamarin.com，这允许只读操作。 若要针对在计算机上运行的下一节中创建的 ASP.NET Core 应用测试一下，你将需要更新应用程序的`RestUrl`常量。 导航到`ToDoREST`项目，然后打开*Constants.cs*文件。 替换`RestUrl`具有包含您的计算机的 IP 的 URL 地址 （不 localhost 或 127.0.0.1，因为此地址用于从设备模拟器中不是从您的计算机）。 包括的端口号 (5000)。 若要测试的设备中运行你的服务，请确保你没有活动的防火墙阻止访问此端口。

```csharp
// URL of REST service (Xamarin ReadOnly Service)
//public static string RestUrl = "http://developer.xamarin.com:8081/api/todoitems{0}";

// use your machine's IP address
public static string RestUrl = "http://192.168.1.207:5000/api/todoitems/{0}";
```

## <a name="creating-the-aspnet-core-project"></a>创建 ASP.NET Core 项目

在 Visual Studio 中创建一个新的 ASP.NET 核心 Web 应用程序。 选择 Web API 模板和无身份验证。 将项目*ToDoApi*。

![与选择的 Web API 项目模板的新建 ASP.NET Web 应用程序对话框](native-mobile-backend/_static/web-api-template.png)

应用程序应响应针对端口 5000 发出的所有请求。 更新*Program.cs*包括`.UseUrls("http://*:5000")`来实现此目的：

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Program.cs?range=10-16&highlight=3)]

> [!NOTE]
> 请确保您运行应用程序直接，而不是在 IIS Express 中，将默认情况下忽略非本地请求后面。 运行`dotnet run`从命令提示符处，或从 Visual Studio 工具栏中的调试目标下拉列表中选择应用程序名称配置文件。

添加一个模型类来表示待办事项。 标记所需字段使用`[Required]`属性：

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Models/ToDoItem.cs)]

API 方法需要某种方式处理数据。 使用相同`IToDoRepository`接口的原始 Xamarin 示例用途：

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Interfaces/IToDoRepository.cs)]

为此示例中，实现只需使用专用的项集合：

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Services/ToDoRepository.cs)]

配置中的实现*Startup.cs*:

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Startup.cs?highlight=6&range=29-35)]

现在，你已准备好创建*ToDoItemsController*。

> [!TIP]
> 了解更多有关创建 web Api 中的[构建您第一个 Web API 使用 ASP.NET 核心 MVC 和 Visual Studio](../tutorials/first-web-api.md)。

## <a name="creating-the-controller"></a>创建控制器

将新控制器添加到项目中， *ToDoItemsController*。 它应从 Microsoft.AspNetCore.Mvc.Controller 继承。 添加`Route`特性以指示控制器将处理对路径开头的请求`api/todoitems`。 `[controller]`路线中的标记会被取代的控制器的名称 (省略`Controller`后缀)，并将全局路由特别有用。 详细了解[路由](../fundamentals/routing.md)。

控制器需要`IToDoRepository`到函数; 请求的控制器构造函数通过此类型的实例。 在运行时，此实例将提供使用对框架的支持，[依赖关系注入](../fundamentals/dependency-injection.md)。

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=1-17&highlight=9,14)]

此 API 支持四个不同的 HTTP 谓词执行对数据源执行 CRUD （创建、 读取、 更新、 删除） 操作。 最简单的这些是读取操作，它对应于 HTTP GET 请求。

### <a name="reading-items"></a>正在读取项目

请求的项的列表，可使用 GET 请求到`List`方法。 `[HttpGet]`属性`List`方法指示此操作应仅处理 GET 请求。 此操作的路由是在控制器上指定的路由。 你不一定需要的操作名称用作的路由的一部分。 你只需确保每个操作都有唯一的和明确的路由。 路由属性可以在控制器和生成特定的路由的方法级别应用。

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=19-23)]

`List`方法返回 200 OK 响应代码和所有序列化为 JSON 的 ToDo 项。

你可以测试新 API 方法使用多种工具，如[Postman](https://www.getpostman.com/docs/)，此处所示：

![显示 todoitems 以及显示三个项返回的 JSON 响应正文的 GET 请求的 postman 控制台](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a>创建项

按照约定，创建新数据项映射到 HTTP POST 谓词。 `Create`方法具有`[HttpPost]`属性应用于该对象，并接受`ToDoItem`实例。 由于`item`将在发布信息正文中传递自变量，则此参数用修饰`[FromBody]`属性。

在方法中，项会检查有效性和之前存在于数据存储，并且如果不出现任何问题，添加使用存储库。 检查`ModelState.IsValid`执行[模型验证](../mvc/models/validation.md)，以及应该在每个 API 方法，接受用户输入来完成。

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=25-46)]

该示例使用枚举包含传递到移动客户端的错误代码：

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=91-99)]

测试添加新项使用 Postman 通过选择 POST 谓词提供请求正文中的 JSON 格式中的新对象。 你还应添加一个请求标头指定`Content-Type`的`application/json`。

![显示 POST 和响应的 postman 控制台](native-mobile-backend/_static/postman-post.png)

方法会在响应中返回新创建的项。

### <a name="updating-items"></a>更新项的项

修改记录使用 HTTP PUT 请求。 除了此更改之外`Edit`方法是几乎与`Create`。 请注意，如果未找到记录，则`Edit`操作，将返回`NotFound`(404) 响应。

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=48-69)]

若要使用 Postman 进行测试，更改到 PUT 谓词。 在请求正文中指定的更新的对象数据。

![显示 PUT 和响应的 postman 控制台](native-mobile-backend/_static/postman-put.png)

此方法返回`NoContent`(204) 响应成功时，为了与预先存在的 API 保持一致。

### <a name="deleting-items"></a>删除项目

删除记录可以通过向服务发出删除请求并传递要删除项的 ID。 当不存在的项的请求将收到更新后，`NotFound`响应。 请求成功，否则，会出现`NoContent`(204) 响应。

[!code-csharp[Main](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=71-88)]

请注意，在测试的删除功能时，没有任何要求在请求正文中。

![显示一个删除和响应的 postman 控制台](native-mobile-backend/_static/postman-delete.png)

## <a name="common-web-api-conventions"></a>常见的 Web API 约定

开发你的应用程序的后端服务时，你将想要附带一组一致的约定或处理的跨领域问题的策略。 例如，在上面所示服务中，为请求特定的记录，无法找到收到`NotFound`响应，而不是`BadRequest`响应。 同样，对此服务中，始终检查的绑定的模型类型传递的命令`ModelState.IsValid`并返回`BadRequest`无效的模型类型。

一旦您已经为您的 Api 来标识常见策略，你可以通常封装在[筛选器](../mvc/controllers/filters.md)。 详细了解[如何封装 ASP.NET 核心 MVC 应用程序中的公用 API 策略](https://msdn.microsoft.com/magazine/mt767699.aspx)。
