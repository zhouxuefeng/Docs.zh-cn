---
title: "ASP.NET 核心中间件"
author: rick-anderson
description: "说明中间件和请求管道。"
keywords: "ASP.NET 核心，中间件，管道、 委托"
ms.author: riande
manager: wpickett
ms.date: 08/14/2017
ms.topic: article
ms.assetid: db9a86ab-46c2-40e0-baed-86e38c16af1f
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/middleware
ms.openlocfilehash: cb39d74b9293b3ab341beba08d2f0af90261ca5f
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2017
---
# <a name="aspnet-core-middleware-fundamentals"></a>ASP.NET 核心中间件基础知识

<a name=fundamentals-middleware></a>

通过[Rick Anderson](https://twitter.com/RickAndMSFT)和[Steve Smith](https://ardalis.com/)

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/sample)

## <a name="what-is-middleware"></a>什么是中间件

中间件是汇集到以处理请求和响应的一个应用程序管道的软件。 每个组件：

* 可以选择是否要将请求传递到管道中的下一个组件。
* 之前和之后调用管道中的下一个组件，可以执行工作。 

使用请求委托来生成请求管道。 请求委托处理每个 HTTP 请求。

请求中使用委托来配置[运行](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.runextensions)，[映射](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapextensions)，和[使用](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.useextensions)扩展方法。 一个单独的请求委托，它可将指定的在行作为匿名方法 （称为中，线中间件），或可以在可重用的类中定义它。 这些可重用的类和行在匿名方法*中间件*，或*中间件组件*。 在请求管道中的每个中间件组件负责调用管道中的下一个组件，或如果相应短路链。

[迁移到中间件的 HTTP 模块](../migration/http-modules.md)说明请求管道中 ASP.NET Core 和早期版本之间的差异，并提供更多的中间件示例。

## <a name="creating-a-middleware-pipeline-with-iapplicationbuilder"></a>使用 IApplicationBuilder 创建中间件管道

ASP.NET 核心请求管道由请求委托，调用一次是在另一个，如图所示 （执行如下所示的黑色箭头的线程） 的顺序组成：

![显示请求到达、 处理通过三个中间件，以及使应用程序的响应的请求处理模式。 每个中间件运行其逻辑，并将传递对 next （） 语句处的下一步中间件的请求。 第三个中间件处理请求时后, 它是左手返回到用于其他处理每个 next （） 语句后又在离开形式对客户端的响应的应用程序之前之前的两个中间件。](middleware/_static/request-delegate-pipeline.png)

每个委托可以执行操作之前和之后的下一步的委托。 委托还可以决定不将请求传递给下一步短路请求管道调用的委托。 因为它可让不必要的工作以避免，短路很理想。 例如，静态文件中间件可以返回静态文件的请求和短路管道的其余部分。 异常处理委托需要调用尽早在管道，因此它们可能会捕获更高版本管道阶段中发生的异常。

最简单可能的 ASP.NET Core 应用程序设置了处理所有请求的单个请求委托。 这种情况下不包括实际请求管道。 相反，单个的匿名函数中称为到每个 HTTP 请求的响应。

[!code-csharp[Main](middleware/sample/Middleware/Startup.cs)]

第一个[应用。运行](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.runextensions)委托终止管道。

你可以链接多个请求委托连同[应用。使用](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.useextensions)。 `next`参数表示管道中的下一步委托。 (请记住，则你可以绕过通过管道*不*调用*下一步*参数。)你可以通常执行操作之前和之后的下一步的委托，如本示例所示：

[!code-csharp[Main](middleware/sample/Chain/Startup.cs?name=snippet1)]

>[!WARNING]
> 不要调用`next.Invoke`响应发送到客户端后。 更改为`HttpResponse`响应启动后将引发异常。 例如，更改，例如设置标头、 状态代码等，将引发异常。 写入之后调用的响应正文`next`:
> - 可能会导致违反协议。 例如，编写多个规定`content-length`。
> - 可能会损坏正文格式。 例如，向 CSS 文件中写入一个 HTML 的页脚。
>
> [HttpResponse.HasStarted](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.features.httpresponsefeature#Microsoft_AspNetCore_Http_Features_HttpResponseFeature_HasStarted)是一个有用的提示，以指示是否在发送标头和/或写入正文。

## <a name="ordering"></a>订购

中间件组件将被添加中的顺序`Configure`方法定义在请求中，调用的顺序和响应相反的顺序。 此排序对于安全、 性能和功能至关重要。

Configure 方法 （如下所示） 将添加以下的中间件组件：

1. 异常/错误处理
2. 静态文件服务器
3. 身份验证
4. MVC

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseExceptionHandler("/Home/Error"); // Call first to catch exceptions
                                            // thrown in the following middleware.

    app.UseStaticFiles();                   // Return static files and end pipeline.

    app.UseIdentity();                     // Authenticate before you access
                                           // secure resources.

    app.UseMvcWithDefaultRoute();          // Add MVC to the request pipeline.
}
```

在上面的代码，`UseExceptionHandler`是添加到管道的第一个中间件组件-因此，它捕获在更高版本调用中发生的任何异常。

静态文件中间件称为尽早在管道中，因此它可以处理请求，而无需通过剩余组件短路。 静态文件中间件提供**没有**授权检查。 由它提供任何文件包括正在*wwwroot*，可公开访问。 请参阅[使用静态文件](xref:fundamentals/static-files)有关来保护静态文件的方法。

如果请求未由静态文件中间件，它将传递给该标识中间件 (`app.UseIdentity`)，这将执行身份验证。 标识不会短路未经身份验证的请求。 标识对请求进行身份验证，尽管仅后 MVC 选择特定控制器和操作时，才会发生授权 （和拒绝）。

下面的示例演示中间排序静态文件的请求将由前响应压缩中间件的静态文件中间件。 静态文件将不压缩此排序的中间件。 从 MVC 响应[UseMvcWithDefaultRoute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mvcapplicationbuilderextensions#Microsoft_AspNetCore_Builder_MvcApplicationBuilderExtensions_UseMvcWithDefaultRoute_Microsoft_AspNetCore_Builder_IApplicationBuilder_)可以压缩。

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseStaticFiles();         // Static files not compressed
                                  // by middleware.
    app.UseResponseCompression();
    app.UseMvcWithDefaultRoute();
}
```

<a name=middleware-run-map-use></a>

### <a name="use-run-and-map"></a>使用、 运行和映射

配置 HTTP 管道使用`Use`， `Run`，和`Map`。 `Use`方法则可以绕过管道 (即，如果它未调用`next`请求委托)。 `Run`是一种约定，并且可能会使某些中间件组件`Run[Middleware]`管道结束时运行的方法。

`Map*`扩展用作分支管道约定。 [映射](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapextensions)请求管道基于给定的请求路径的匹配项的分支。 如果请求路径开头的给定路径，则执行分支。

[!code-csharp[Main](middleware/sample/Chain/StartupMap.cs?name=snippet1)]

下表显示的请求和响应从`http://localhost:1234`使用前面的代码：

| 请求 | 响应 |
| --- | --- |
| localhost:1234 | 从非映射委托 hello。  |
| localhost:1234 / map1 | 测试 1 映射 |
| localhost:1234 / map2 | 测试 2 映射 |
| localhost:1234 / map3 | 从非映射委托 hello。  |

当`Map`是使用，从删除匹配的路径段`HttpRequest.Path`和追加到`HttpRequest.PathBase`为每个请求。

[MapWhen](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.mapwhenextensions)分支请求管道根据给定的谓词的结果。 类型的任何谓词`Func<HttpContext, bool>`可以用于将请求映射到管道的新分支。 在下面的示例中，谓词使用查询字符串变量的状态进行检测`branch`:

[!code-csharp[Main](middleware/sample/Chain/StartupMapWhen.cs?name=snippet1)]

下表显示的请求和响应从`http://localhost:1234`使用前面的代码：

| 请求 | 响应 |
| --- | --- |
| localhost:1234 | 从非映射委托 hello。  |
| localhost:1234 /？ 分支 = master | 使用分支 = master|

`Map`支持嵌套，例如：

```csharp
app.Map("/level1", level1App => {
       level1App.Map("/level2a", level2AApp => {
           // "/level1/level2a"
           //...
       });
       level1App.Map("/level2b", level2BApp => {
           // "/level1/level2b"
           //...
       });
   });
   ```

`Map`此外可以匹配多个段，例如：

 ```csharp
app.Map("/level1/level2", HandleMultiSeg);
```

## <a name="built-in-middleware"></a>内置的中间件

ASP.NET 核心附带以下的中间件组件：

| 中间件 | 描述 |
| ----- | ------- |
| [身份验证](xref:security/authentication/identity) | 提供的身份验证支持。 |
| [CORS](xref:security/cors) | 配置跨域资源共享。 |
| [响应缓存](xref:performance/caching/middleware) | 提供对缓存响应支持。 |
| [响应压缩](xref:performance/response-compression) | 提供用于压缩响应的支持。 |
| [路由](xref:fundamentals/routing) | 定义和约束请求路由。 |
| [会话](xref:fundamentals/app-state) | 提供用于管理用户会话的支持。 |
| [静态文件](xref:fundamentals/static-files) | 为静态文件和目录浏览提供服务提供支持。 |
| [URL 重写中间件](xref:fundamentals/url-rewriting) | 用于重写 Url，并将请求重定向的支持。 |

<a name=middleware-writing-middleware></a>

## <a name="writing-middleware"></a>编写中间件

通常，中间件是封装在一个类，并且通过扩展方法公开。 请考虑以下中间件，它将为当前请求的区域性设置从查询字符串：

[!code-csharp[Main](middleware/sample/Culture/StartupCulture.cs?name=snippet1)]

注意： 上面的示例代码用于演示创建中间件组件。 请参阅[全球化和本地化](xref:fundamentals/localization)ASP.NET 核心内置的本地化支持。

你可以通过例如传递区域性中测试该中间件`http://localhost:7997/?culture=no`。

下面的代码将移动到的类的中间件委托：

[!code-csharp[Main](middleware/sample/Culture/RequestCultureMiddleware.cs)]

下面的扩展方法公开通过中间件[IApplicationBuilder](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.iapplicationbuilder):

[!code-csharp[Main](middleware/sample/Culture/RequestCultureMiddlewareExtensions.cs)]

下面的代码调用从中间件`Configure`:

[!code-csharp[Main](middleware/sample/Culture/Startup.cs?name=snippet1&highlight=5)]

中间件应遵循[显式依赖关系原则](http://deviq.com/explicit-dependencies-principle/)通过公开其依赖项在其构造函数。 每次构造中间件*应用程序生存期*。 请参阅*每个请求的依赖关系*下面如果你需要与请求中的中间件共享服务。

中间件组件可以解决通过构造函数参数的依赖关系注入从其依赖关系。 [`UseMiddleware<T>`](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.usemiddlewareextensions#methods_summary)此外可以接受其他参数直接。

### <a name="per-request-dependencies"></a>每个请求的依赖关系

中间件构造在应用启动时，不根据请求，因为*范围*生存期服务使用的中间件构造函数不能与其他依赖关系注入类型共享在每个请求过程。 如果必须共享*范围*服务中间件和其他类型之间，添加到这些服务`Invoke`方法的签名。 `Invoke`方法可以接受由依赖关系注入填充的附加参数。 例如: 

```c#
public class MyMiddleware
{
    private readonly RequestDelegate _next;

    public MyMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="resources"></a>资源

* [在本文档中使用的示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/sample)
* [将 HTTP 模块迁移到中间件](../migration/http-modules.md)
* [应用程序启动](startup.md)
* [请求功能](request-features.md)
