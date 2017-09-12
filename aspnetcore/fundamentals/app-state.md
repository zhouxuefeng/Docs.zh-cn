---
title: "在 ASP.NET 核心中的会话和应用程序状态"
author: rick-anderson
description: "保留应用程序和用户 （会话） 状态请求之间的方法。"
keywords: "ASP.NET 核心，应用程序状态、 会话状态、 查询字符串，发布"
ms.author: riande
manager: wpickett
ms.date: 06/08/2017
ms.topic: article
ms.assetid: 18cda488-0769-4cb9-82f6-4c6685f2045d
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/app-state
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8b451bde1e3180d12781d55113638cc1a99182c8
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="introduction-to-session-and-application-state-in-aspnet-core"></a>简介中 ASP.NET Core 的会话和应用程序状态

通过[Rick Anderson](https://twitter.com/RickAndMSFT)， [Steve Smith](https://ardalis.com/)，和[Diana LaRose](https://github.com/DianaLaRose)

HTTP 是无状态的协议。 Web 服务器将每个 HTTP 请求作为独立的请求，并不会保留用户从以前的请求的值。 本文讨论保留应用程序和请求之间的会话状态的不同方式。 

## <a name="session-state"></a>会话状态

会话状态是 ASP.NET 核心技术，可用于保存和存储用户数据，而用户浏览你的 web 应用中的功能。 包含服务器上的字典或哈希表，会话状态保持跨从浏览器的请求数据。 缓存支持会话数据。

ASP.NET 核心通过提供包含会话 ID，它使用每个请求向服务器发送的 cookie 的客户端维护会话状态。 服务器使用的会话 ID 来获取会话数据。 因为会话 cookie 是特定于浏览器，你不能在浏览器中共享会话。 仅当浏览器会话结束时，将删除会话 cookie。 如果收到过期的会话 cookie，创建使用相同的会话 cookie 的新会话。 

服务器将保留上次请求后的有限时间的会话。 你可以将会话超时设置，或使用 20 分钟的默认值。 会话状态非常适合用于存储用户数据的特定于特定会话，但并不需要永久保留。 数据从存储中删除后备或者当您调用`Session.Clear`或会话数据存储中存储的到期时。 关闭浏览器时，或删除会话 cookie 时，服务器不知道。

> [!WARNING]
> 不要在会话中存储敏感数据。 客户端可能会不关闭浏览器并清除会话 cookie （和某些浏览器保持会话 cookie 存在跨 windows）。 另外，会话可能不是限制为单个用户;下一步的用户可能会继续与同一会话中。

内存中的会话提供程序将会话数据存储在本地服务器上。 如果你计划在服务器场上运行你的 web 应用，你必须使用粘性会话将特定服务器的每个会话进行连接。 Windows Azure 网站平台默认为粘性会话应用程序请求路由 （ARR）。 但是，粘性会话可以影响可伸缩性，并使 web 应用程序更新变得复杂。 更好的选择是使用 Redis 或 SQL Server 分布式缓存，这不需要粘性会话。 有关详细信息，请参阅[使用分布式缓存](xref:performance/caching/distributed)。 有关服务提供商设置的详细信息，请参阅[配置会话](#configuring-session)本文后续部分中。

本部分的其余部分介绍用于存储用户数据的选项。

<a name="temp"></a>
### <a name="tempdata"></a>TempData

ASP.NET 核心 MVC 公开[TempData](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller#Microsoft_AspNetCore_Mvc_Controller_TempData)属性[控制器](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller)。 此属性可存储数据，直至数据被读取。 `Keep` 和 `Peek` 方法可用于检查数据，而不执行删除。 `TempData`当超过单个请求所需要的数据，则很适合用于重定向。 `TempData`是基于会话状态。 

## <a name="cookie-based-tempdata-provider"></a>基于 cookie 的 TempData 提供程序 

ASP.NET 核心 1.1 和更高版本，你可以使用基于 cookie 的 TempData 提供程序在 cookie 中存储用户的 TempData。 若要启用基于 cookie 的 TempData 提供程序，注册`CookieTempDataProvider`服务`ConfigureServices`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    // Add CookieTempDataProvider after AddMvc and include ViewFeatures.
    // using Microsoft.AspNetCore.Mvc.ViewFeatures;
    services.AddSingleton<ITempDataProvider, CookieTempDataProvider>();
}
```

使用编码的 cookie 数据[Base64UrlTextEncoder](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.authentication.base64urltextencoder)。 因为 cookie 被加密，并分块，一个 cookie 大小限制不适用于。 未压缩的 cookie 数据，因为压缩 encryped 数据会导致安全问题如[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))和[违反](https://wikipedia.org/wiki/BREACH_(security_exploit))攻击。 基于 cookie 的 TempData 提供程序的详细信息，请参阅[CookieTempDataProvider](https://github.com/aspnet/Mvc/blob/dev/src/Microsoft.AspNetCore.Mvc.ViewFeatures/ViewFeatures/CookieTempDataProvider.cs)。

### <a name="query-strings"></a>查询字符串

你可以为到另一个从一个请求将其添加到新请求的查询字符串传递数据的量有限。 这可用于以允许具有嵌入的状态，以通过电子邮件或社交网络共享的链接的持久方式捕获状态。 但是，为此，你应永远不会使用查询字符串的敏感数据。 除了轻松地共享，包括查询字符串中的数据可以创建的机会[跨站点请求伪造 (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF))攻击，可以欺骗用户访问恶意网站时进行身份验证。 然后，攻击者可能窃取用户从应用程序的数据，或需要代表用户恶意操作。 任何保留的应用程序或会话状态必须防止 CSRF 攻击。 CSRF 攻击的详细信息，请参阅[防止跨站点请求伪造 (XSRF/CSRF) 攻击中 ASP.NET Core](../security/anti-request-forgery.md)。

### <a name="post-data-and-hidden-fields"></a>后期数据和隐藏的字段

可以保存在隐藏的表单字段数据，并且将其重新发布了下一个请求。 这是常见的多页的窗体。 但是，客户端可能可以篡改数据，因为服务器必须始终重新验证它。 

### <a name="cookies"></a>Cookie

Cookie 提供了如何在 web 应用程序中存储特定于用户的数据。 因为与每个请求一起发送 cookie，则其大小应保持在最低限度。 理想情况下，仅标识符应存储在 cookie 中，与存储在服务器上的实际数据。 大多数浏览器将限制为 4096 个字节的 cookie。 此外，仅有限的数量的 cookie 可为每个域。  

Cookie 是易被篡改，因为它们必须在服务器上验证。 尽管在客户端的 cookie 持续性是受影响用户干预，到期，但它们通常是客户端上的数据暂留的最持久形式。

通常使用 cookie 以进行个性化设置，其中的已知用户自定义内容。 因为用户仅标识并且未经过身份验证在大多数情况下，你通常可以通过将用户名称、 帐户名称或唯一的用户 ID （例如 GUID) 存储在 cookie 中保护 cookie。 然后可以使用 cookie 来访问站点的用户个性化设置基础结构。

### <a name="httpcontextitems"></a>HttpContext.Items

`Items`集合是存储的数据的正确位置仅需要同时处理一个特定的请求。 每个请求之后，集合的内容将被放弃。 `Items`集合最用作一种方法的组件或中间件进行通信时它们在请求过程的时间内运行的不同时间点，并且具有无法直接将参数传递。 有关详细信息，请参阅[使用 HttpContext.Items](#working-with-httpcontextitems)，本文稍后的。

### <a name="cache"></a>缓存

缓存是一种高效的方式来存储和检索数据。 你可以控制基于时间和其他注意事项的缓存项的生存期。 详细了解[Caching](../performance/caching/index.md)。

<a name=session></a>

## <a name="configuring-session"></a>配置会话

`Microsoft.AspNetCore.Session`包提供用于管理会话状态的中间件。 若要启用会话中间件，`Startup`必须包含：

- 任一[IDistributedCache](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.distributed.idistributedcache)内存缓存。 `IDistributedCache`实现用于作为后备存储会话。
- [AddSession](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions#Microsoft_Extensions_DependencyInjection_SessionServiceCollectionExtensions_AddSession_Microsoft_Extensions_DependencyInjection_IServiceCollection_) ，调用要求 NuGet 包"Microsoft.AspNetCore.Session"。
- [UseSession](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions#methods_)调用。

下面的代码演示如何设置内存中的会话提供程序。

[!code-csharp[Main](app-state/sample/src/WebAppSession/Startup.cs?highlight=11-19,24)]

你可以引用会话中的从`HttpContext`后安装和配置它。

如果你尝试访问`Session`之前`UseSession`已调用，该异常`InvalidOperationException: Session has not been configured for this application or request`引发。

如果你尝试创建一个新`Session`（即，已创建任何会话 cookie） 已经开始写入后`Response`流处理时，异常`InvalidOperationException: The session cannot be established after the response has started`引发。 可以在 web 服务器日志; 中找到的异常它将不会显示在浏览器。

### <a name="loading-session-asynchronously"></a>以异步方式加载会话 

ASP.NET 核心中的默认会话提供程序，则从基础加载会话记录[IDistributedCache](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.distributed.idistributedcache)以异步方式存储才[ISession.LoadAsync](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.isession#Microsoft_AspNetCore_Http_ISession_LoadAsync)之前显式调用方法 `TryGetValue`， `Set`，或`Remove`方法。 如果`LoadAsync`则不会先调用，基础会话记录是否同步加载，这可能影响应用程序能够扩展。

若要让应用程序强制实施此模式，包装[DistributedSessionStore](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.session.distributedsessionstore)和[DistributedSession](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.session.distributedsession)实施服务，如果引发异常的版本和`LoadAsync`方法不是之前调用`TryGetValue`， `Set`，或`Remove`。 在服务容器中注册的已包装的版本。

### <a name="implementation-details"></a>实现详细信息

会话使用 cookie 跟踪和标识来自单个浏览器的请求。 默认情况下，此 cookie 名为"。AspNet.Session"，并使用的路径"/"。 Cookie 默认未指定域，因为它不将提供给客户端脚本的页上 (因为`CookieHttpOnly`默认为`true`)。

若要重写会话默认值，使用`SessionOptions`:

[!code-csharp[Main](app-state/sample/src/WebAppSession/StartupCopy.cs?name=snippet1&highlight=8-12)]

服务器使用`IdleTimeout`属性来确定并放弃其内容之前，会话可以保持空闲的时间长短。 此属性是独立于 cookie 到期时间。 传递的会话中间件 （读取或写入） 每个请求将重置超时。

因为`Session`是*非锁定*，如果两个请求都试图修改的会话中，最后一个内容重写第一个。 `Session`作为实现*连贯会话*，这意味着所有内容都存储在一起。 正在修改的会话 （不同的密钥） 的不同部分的两个请求仍可能会影响每个其他。

## <a name="setting-and-getting-session-values"></a>设置和获取会话值

通过访问会话`Session`属性`HttpContext`。 此属性是[ISession](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.isession)实现。

下面的示例演示了设置和获取 int 和字符串：

[!code-csharp[Main](app-state/sample/src/WebAppSession/Controllers/HomeController.cs?name=snippet1)]

如果你添加的以下扩展方法，你可以设置并获取可序列化的对象添加到会话：

[!code-csharp[Main](app-state/sample/src/WebAppSession/Extensions/SessionExtensions.cs)]

下面的示例演示如何设置和获取可序列化的对象：

[!code-csharp[Main](app-state/sample/src/WebAppSession/Controllers/HomeController.cs?name=snippet2)]


## <a name="working-with-httpcontextitems"></a>使用 HttpContext.Items

`HttpContext`抽象类型的字典集合提供支持`IDictionary<object, object>`、 调用`Items`。 此集合是从开始处可用*HttpRequest*并且末尾的每个请求将被丢弃。 通过将值分配给某一键控的项，或通过请求特定键的值，你可以访问它。

在下面，示例[中间件](middleware.md)添加`isVerified`到`Items`集合。

```csharp
app.Use(async (context, next) =>
{
    // perform some verification
    context.Items["isVerified"] = true;
    await next.Invoke();
});
```

更高版本在管道中，另一个中间件无法访问它：

```csharp
app.Run(async (context) =>
{
    await context.Response.WriteAsync("Verified request? " + 
        context.Items["isVerified"]);
});
```

为中间件将仅由单个应用，`string`密钥是可接受。 但是，将应用程序之间共享的中间件应使用唯一的对象键以避免任何机会键冲突。 如果你正在开发必须跨多个应用程序工作的中间件，使用如下所示中间件类中定义的唯一对象密钥：

```csharp
public class SampleMiddleware
{
    public static readonly object SampleKey = new Object();

    public async Task Invoke(HttpContext httpContext)
    {
        httpContext.Items[SampleKey] = "some value";
        // additional code omitted
    }
}
```

其他代码可以访问存储中的值`HttpContext.Items`使用中间件类公开的密钥：

```csharp
public class HomeController : Controller
{
    public IActionResult Index()
    {
        string value = HttpContext.Items[SampleMiddleware.SampleKey];
    }
}
```

此方法还具有消除重复的代码中的多个位置中的"神奇字符串"的优点。

<a name=appstate-errors></a>

## <a name="application-state-data"></a>应用程序状态数据

使用[依赖关系注入](xref:fundamentals/dependency-injection)可向所有用户提供数据：

1. 定义包含数据的服务 (例如，一个名为的类`MyAppData`)。

```csharp
public class MyAppData
{
    // Declare properties/methods/etc.
} 
```
2. 添加到的服务类`ConfigureServices`(例如`services.AddSingleton<MyAppData>();`)。
3. 使用每个控制器中的数据服务类：

```csharp
public class MyController : Controller
{
    public MyController(MyAppData myService)
    {
        // Do something with the service (read some data from it, 
        // store it in a private field/property, etc.)
    }
} 
```

### <a name="common-errors-when-working-with-session"></a>使用会话时的常见错误

* "无法解析为类型 Microsoft.Extensions.Caching.Distributed.IDistributedCache 的服务在尝试激活 Microsoft.AspNetCore.Session.DistributedSessionStore 时。"

  这通常由于不能配置至少一个`IDistributedCache`实现。 有关详细信息，请参阅[使用分布式缓存](xref:performance/caching/distributed)和[中内存缓存](xref:performance/caching/memory)。

### <a name="additional-resources"></a>其他资源


* [本文档中使用的示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/app-state/sample/src/WebAppSession)
