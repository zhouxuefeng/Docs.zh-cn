---
title: "打开.NET (OWIN) 的 Web 界面"
author: ardalis
description: "若要打开.NET (OWIN) 的 Web 界面的介绍。"
keywords: "ASP.NET 核心，为使.NET，OWIN 打开 Web 接口"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 70c4e6bc-a773-4039-96ec-6fe557c9369d
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/owin
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9edacb494c38d7812f9e3826ab9277cd1dffd675
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2017
---
# <a name="introduction-to-open-web-interface-for-net-owin"></a><span data-ttu-id="4ff2d-104">若要打开.NET (OWIN) 的 Web 界面的简介</span><span class="sxs-lookup"><span data-stu-id="4ff2d-104">Introduction to Open Web Interface for .NET (OWIN)</span></span>

<span data-ttu-id="4ff2d-105">通过[Steve Smith](https://ardalis.com/)和[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4ff2d-105">By [Steve Smith](https://ardalis.com/) and  [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4ff2d-106">ASP.NET 核心支持打开的 Web 接口的.NET (OWIN)。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-106">ASP.NET Core supports the Open Web Interface for .NET (OWIN).</span></span> <span data-ttu-id="4ff2d-107">OWIN 允许 web 应用程序从 web 服务器分离。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-107">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="4ff2d-108">它定义的中间件来用于在管道中使用，以处理请求和响应关联的标准方法。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-108">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="4ff2d-109">ASP.NET Core 应用程序和中间件可以与基于 OWIN 的应用程序、 服务器和中间件互操作。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-109">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="4ff2d-110">OWIN 提供了允许与不同的对象模型一起使用的两个框架解除层。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-110">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="4ff2d-111">`Microsoft.AspNetCore.Owin`包提供了两个适配器实现：</span><span class="sxs-lookup"><span data-stu-id="4ff2d-111">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>
- <span data-ttu-id="4ff2d-112">OWIN 到 ASP.NET 核心</span><span class="sxs-lookup"><span data-stu-id="4ff2d-112">ASP.NET Core to OWIN</span></span> 
- <span data-ttu-id="4ff2d-113">OWIN 到 ASP.NET 核心</span><span class="sxs-lookup"><span data-stu-id="4ff2d-113">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="4ff2d-114">这允许基于 OWIN 兼容服务器/主机，或在 ASP.NET Core 上运行其他 OWIN 兼容组件托管的 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-114">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host, or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

<span data-ttu-id="4ff2d-115">注意： 使用这些适配器附带有一定的性能开销。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-115">Note: Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="4ff2d-116">使用仅 ASP.NET 核心组件的应用程序不应使用 Owin 包或适配器。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-116">Applications using only ASP.NET Core components should not use the Owin package or adapters.</span></span>

[<span data-ttu-id="4ff2d-117">查看或下载示例代码</span><span class="sxs-lookup"><span data-stu-id="4ff2d-117">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/owin/sample)

## <a name="running-owin-middleware-in-the-aspnet-pipeline"></a><span data-ttu-id="4ff2d-118">在 ASP.NET 管线中运行 OWIN 中间件</span><span class="sxs-lookup"><span data-stu-id="4ff2d-118">Running OWIN middleware in the ASP.NET pipeline</span></span>

<span data-ttu-id="4ff2d-119">ASP.NET 核心 OWIN 支持部署的一部分`Microsoft.AspNetCore.Owin`包。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-119">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="4ff2d-120">你可以通过安装此包导入你的项目的 OWIN 支持。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-120">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="4ff2d-121">OWIN 中间件符合[OWIN 规范](http://owin.org/spec/spec/owin-1.0.0.html)，这要求`Func<IDictionary<string, object>, Task>`接口和特定的密钥设置 (如`owin.ResponseBody`)。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-121">OWIN middleware conforms to the [OWIN specification](http://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="4ff2d-122">以下简单的 OWIN 中间件将显示"Hello World":</span><span class="sxs-lookup"><span data-stu-id="4ff2d-122">The following simple OWIN middleware displays "Hello World":</span></span>

```csharp
public Task OwinHello(IDictionary<string, object> environment)
{
    string responseText = "Hello World via OWIN";
    byte[] responseBytes = Encoding.UTF8.GetBytes(responseText);

    // OWIN Environment Keys: http://owin.org/spec/spec/owin-1.0.0.html
    var responseStream = (Stream)environment["owin.ResponseBody"];
    var responseHeaders = (IDictionary<string, string[]>)environment["owin.ResponseHeaders"];

    responseHeaders["Content-Length"] = new string[] { responseBytes.Length.ToString(CultureInfo.InvariantCulture) };
    responseHeaders["Content-Type"] = new string[] { "text/plain" };

    return responseStream.WriteAsync(responseBytes, 0, responseBytes.Length);
}


```

<span data-ttu-id="4ff2d-123">示例签名返回`Task`并接受`IDictionary<string, object>`OWIN 通过所需的方式。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-123">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="4ff2d-124">下面的代码演示如何添加`OwinHello`（上面所述） 向 ASP.NET 管道使用的中间件`UseOwin`扩展方法。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-124">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET pipeline with the `UseOwin` extension method.</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {"linenostart": 1}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#", "source": "/Users/shirhatti/src/Docs/aspnet/fundamentals/owin/sample/src/OwinSample/Startup.cs"} -->

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}


```

<span data-ttu-id="4ff2d-125">你可以配置在 OWIN 管道中进行其他操作。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-125">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="4ff2d-126">仅应在首次向响应流写入之前修改响应标头。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-126">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="4ff2d-127">多次调用`UseOwin`出于性能原因不建议这样做。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-127">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="4ff2d-128">如果组合在一起，OWIN 组件将最好地运行。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-128">OWIN components will operate best if grouped together.</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

```csharp
app.UseOwin(pipeline =>
{
    pipeline(next =>
    {
        // do something before
        return OwinHello;
        // do something after
    });
});
```

<a name=hosting-on-owin></a>

## <a name="using-aspnet-hosting-on-an-owin-based-server"></a><span data-ttu-id="4ff2d-129">使用基于 OWIN 的服务器上的 ASP.NET 宿主</span><span class="sxs-lookup"><span data-stu-id="4ff2d-129">Using ASP.NET Hosting on an OWIN-based server</span></span>

<span data-ttu-id="4ff2d-130">基于 OWIN 的服务器可以承载 ASP.NET 应用程序。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-130">OWIN-based servers can host ASP.NET applications.</span></span> <span data-ttu-id="4ff2d-131">此类的一台服务器是[Nowin](https://github.com/Bobris/Nowin)，.NET OWIN web 服务器。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-131">One such server is [Nowin](https://github.com/Bobris/Nowin), a .NET OWIN web server.</span></span> <span data-ttu-id="4ff2d-132">在本文示例中，我已包含引用 Nowin 并使用它来创建一个项目项目`IServer`能够自承载 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-132">In the sample for this article, I've included a project that references Nowin and uses it to create an `IServer` capable of self-hosting ASP.NET Core.</span></span>

[!code-csharp[Main](owin/sample/src/NowinSample/Program.cs?highlight=15)]

<span data-ttu-id="4ff2d-133">`IServer`是接口，需要`Features`属性和`Start`方法。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-133">`IServer` is an interface that requires an `Features` property and a `Start` method.</span></span>

<span data-ttu-id="4ff2d-134">`Start`负责配置和启动服务器，在这种情况下通过一系列从 IServerAddressesFeature 设置地址分析的 fluent API 调用。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-134">`Start` is responsible for configuring and starting the server, which in this case is done through a series of fluent API calls that set addresses parsed from the IServerAddressesFeature.</span></span> <span data-ttu-id="4ff2d-135">请注意的 fluent 配置`_builder`变量指定将由处理请求`appFunc`先前在方法中定义。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-135">Note that the fluent configuration of the `_builder` variable specifies that requests will be handled by the `appFunc` defined earlier in the method.</span></span> <span data-ttu-id="4ff2d-136">这`Func`称为上每个请求以处理传入的请求。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-136">This `Func` is called on each request to process incoming requests.</span></span>

<span data-ttu-id="4ff2d-137">我们还将添加`IWebHostBuilder`扩展，以便可以方便地添加和配置 Nowin 服务器。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-137">We'll also add an `IWebHostBuilder` extension to make it easy to add and configure the Nowin server.</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {"hl_lines": [11], "linenostart": 1}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#", "source": "/Users/shirhatti/src/Docs/aspnet/fundamentals/owin/sample/src/NowinSample/NowinWebHostBuilderExtensions.cs"} -->

```csharp
using System;
using Microsoft.AspNetCore.Hosting.Server;
using Microsoft.Extensions.DependencyInjection;
using Nowin;
using NowinSample;

namespace Microsoft.AspNetCore.Hosting
{
    public static class NowinWebHostBuilderExtensions
    {
        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder)
        {
            return builder.ConfigureServices(services =>
            {
                services.AddSingleton<IServer, NowinServer>();
            });
        }

        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder, Action<ServerBuilder> configure)
        {
            builder.ConfigureServices(services =>
            {
                services.Configure(configure);
            });
            return builder.UseNowin();
        }
    }
}
```

<span data-ttu-id="4ff2d-138">与此就地，所有，具有需要运行 ASP.NET 应用程序使用此自定义服务器调用扩展*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="4ff2d-138">With this in place, all that's required to run an ASP.NET application using this custom server to call the extension in *Program.cs*:</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {"hl_lines": [15], "linenostart": 1}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#", "source": "/Users/shirhatti/src/Docs/aspnet/fundamentals/owin/sample/src/NowinSample/Program.cs"} -->

```csharp

using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Hosting;

namespace NowinSample
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = new WebHostBuilder()
                .UseNowin()
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseIISIntegration()
                .UseStartup<Startup>()
                .Build();

            host.Run();
        }
    }
}

```

<span data-ttu-id="4ff2d-139">了解有关 ASP.NET[服务器](servers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-139">Learn more about ASP.NET [Servers](servers/index.md).</span></span>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="4ff2d-140">基于 OWIN 的服务器上运行 ASP.NET 核心，并使用其 Websocket 支持</span><span class="sxs-lookup"><span data-stu-id="4ff2d-140">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="4ff2d-141">如何基于 OWIN 的服务器的功能的另一个示例可以利用 ASP.NET 核心是对等 Websocket 功能的访问。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-141">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="4ff2d-142">在前面的示例使用的.NET OWIN web 服务器具有用于 Web 套接字内置的这可以利用 ASP.NET Core 应用程序支持。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-142">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="4ff2d-143">下面的示例演示一个简单的 web 应用，支持 Web 套接字回显 Websocket 通过向服务器发送的所有内容。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-143">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {"hl_lines": [7, 9, 10], "linenostart": 1}, "backrefs": [], "dupnames": [], "linenos": true, "classes": [], "xml:space": "preserve", "language": "c#", "source": "/Users/shirhatti/src/Docs/aspnet/fundamentals/owin/sample/src/NowinWebSockets/Startup.cs"} -->

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) =>
        {
            if (context.WebSockets.IsWebSocketRequest)
            {
                WebSocket webSocket = await context.WebSockets.AcceptWebSocketAsync();
                await EchoWebSocket(webSocket);
            }
            else
            {
                await next();
            }
        });

        app.Run(context =>
        {
            return context.Response.WriteAsync("Hello World");
        });
    }

    private async Task EchoWebSocket(WebSocket webSocket)
    {
        byte[] buffer = new byte[1024];
        WebSocketReceiveResult received = await webSocket.ReceiveAsync(
            new ArraySegment<byte>(buffer), CancellationToken.None);

        while (!webSocket.CloseStatus.HasValue)
        {
            // Echo anything we receive
            await webSocket.SendAsync(new ArraySegment<byte>(buffer, 0, received.Count), 
                received.MessageType, received.EndOfMessage, CancellationToken.None);

            received = await webSocket.ReceiveAsync(new ArraySegment<byte>(buffer), 
                CancellationToken.None);
        }

        await webSocket.CloseAsync(webSocket.CloseStatus.Value, 
            webSocket.CloseStatusDescription, CancellationToken.None);
    }
}
```

<span data-ttu-id="4ff2d-144">这[示例](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/owin/sample)使用相同配置`NowinServer`与之前的唯一的区别是在应用程序中的配置方式其`Configure`方法。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-144">This [sample](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/owin/sample) is configured using the same `NowinServer` as the previous one - the only difference is in how the application is configured in its `Configure` method.</span></span> <span data-ttu-id="4ff2d-145">测试使用[简单 websocket 客户端](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en)演示应用程序：</span><span class="sxs-lookup"><span data-stu-id="4ff2d-145">A test using [a simple websocket client](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) demonstrates  the application:</span></span>

![Web 套接字测试客户端](owin/_static/websocket-test.png)

## <a name="owin-environment"></a><span data-ttu-id="4ff2d-147">OWIN 环境</span><span class="sxs-lookup"><span data-stu-id="4ff2d-147">OWIN environment</span></span>

<span data-ttu-id="4ff2d-148">你可以构造 OWIN 环境使用`HttpContext`。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-148">You can construct a OWIN environment using the `HttpContext`.</span></span>

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="4ff2d-149">OWIN 键</span><span class="sxs-lookup"><span data-stu-id="4ff2d-149">OWIN keys</span></span>

<span data-ttu-id="4ff2d-150">取决于 OWIN`IDictionary<string,object>`对象进行通信信息的整个 HTTP 请求/响应交换。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-150">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="4ff2d-151">ASP.NET 核心实现下面列出的密钥。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-151">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="4ff2d-152">请参阅[主规范、 扩展](http://owin.org/#spec)，和[OWIN 键指导原则和常见的键](http://owin.org/spec/spec/CommonKeys.html)。</span><span class="sxs-lookup"><span data-stu-id="4ff2d-152">See the [primary specification, extensions](http://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](http://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="4ff2d-153">请求的数据 (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="4ff2d-153">Request Data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="4ff2d-154">键</span><span class="sxs-lookup"><span data-stu-id="4ff2d-154">Key</span></span>               | <span data-ttu-id="4ff2d-155">值 （类型）</span><span class="sxs-lookup"><span data-stu-id="4ff2d-155">Value (type)</span></span> | <span data-ttu-id="4ff2d-156">描述</span><span class="sxs-lookup"><span data-stu-id="4ff2d-156">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="4ff2d-157">owin。RequestScheme</span><span class="sxs-lookup"><span data-stu-id="4ff2d-157">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="4ff2d-158">owin。RequestMethod</span><span class="sxs-lookup"><span data-stu-id="4ff2d-158">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="4ff2d-159">owin。RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="4ff2d-159">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="4ff2d-160">owin。RequestPath</span><span class="sxs-lookup"><span data-stu-id="4ff2d-160">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="4ff2d-161">owin。RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="4ff2d-161">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="4ff2d-162">owin。RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="4ff2d-162">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="4ff2d-163">owin。RequestHeaders</span><span class="sxs-lookup"><span data-stu-id="4ff2d-163">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="4ff2d-164">owin。RequestBody</span><span class="sxs-lookup"><span data-stu-id="4ff2d-164">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="4ff2d-165">请求的数据 (OWIN v1.1.0)</span><span class="sxs-lookup"><span data-stu-id="4ff2d-165">Request Data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="4ff2d-166">键</span><span class="sxs-lookup"><span data-stu-id="4ff2d-166">Key</span></span>               | <span data-ttu-id="4ff2d-167">值 （类型）</span><span class="sxs-lookup"><span data-stu-id="4ff2d-167">Value (type)</span></span> | <span data-ttu-id="4ff2d-168">描述</span><span class="sxs-lookup"><span data-stu-id="4ff2d-168">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="4ff2d-169">owin。请求 Id</span><span class="sxs-lookup"><span data-stu-id="4ff2d-169">owin.RequestId</span></span> | `String` | <span data-ttu-id="4ff2d-170">Optional</span><span class="sxs-lookup"><span data-stu-id="4ff2d-170">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="4ff2d-171">响应数据 (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="4ff2d-171">Response Data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="4ff2d-172">键</span><span class="sxs-lookup"><span data-stu-id="4ff2d-172">Key</span></span>               | <span data-ttu-id="4ff2d-173">值 （类型）</span><span class="sxs-lookup"><span data-stu-id="4ff2d-173">Value (type)</span></span> | <span data-ttu-id="4ff2d-174">描述</span><span class="sxs-lookup"><span data-stu-id="4ff2d-174">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="4ff2d-175">owin。ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="4ff2d-175">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="4ff2d-176">Optional</span><span class="sxs-lookup"><span data-stu-id="4ff2d-176">Optional</span></span> |
| <span data-ttu-id="4ff2d-177">owin。ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="4ff2d-177">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="4ff2d-178">Optional</span><span class="sxs-lookup"><span data-stu-id="4ff2d-178">Optional</span></span> |
| <span data-ttu-id="4ff2d-179">owin。ResponseHeaders</span><span class="sxs-lookup"><span data-stu-id="4ff2d-179">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="4ff2d-180">owin。ResponseBody</span><span class="sxs-lookup"><span data-stu-id="4ff2d-180">owin.ResponseBody</span></span> | `Stream`  | |


### <a name="other-data-owin-v100"></a><span data-ttu-id="4ff2d-181">其他数据 (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="4ff2d-181">Other Data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="4ff2d-182">键</span><span class="sxs-lookup"><span data-stu-id="4ff2d-182">Key</span></span>               | <span data-ttu-id="4ff2d-183">值 （类型）</span><span class="sxs-lookup"><span data-stu-id="4ff2d-183">Value (type)</span></span> | <span data-ttu-id="4ff2d-184">描述</span><span class="sxs-lookup"><span data-stu-id="4ff2d-184">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="4ff2d-185">owin。CallCancelled</span><span class="sxs-lookup"><span data-stu-id="4ff2d-185">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="4ff2d-186">owin。版本</span><span class="sxs-lookup"><span data-stu-id="4ff2d-186">owin.Version</span></span>  | `String` | |   


### <a name="common-keys"></a><span data-ttu-id="4ff2d-187">常见的键</span><span class="sxs-lookup"><span data-stu-id="4ff2d-187">Common Keys</span></span>

| <span data-ttu-id="4ff2d-188">键</span><span class="sxs-lookup"><span data-stu-id="4ff2d-188">Key</span></span>               | <span data-ttu-id="4ff2d-189">值 （类型）</span><span class="sxs-lookup"><span data-stu-id="4ff2d-189">Value (type)</span></span> | <span data-ttu-id="4ff2d-190">描述</span><span class="sxs-lookup"><span data-stu-id="4ff2d-190">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="4ff2d-191">ssl。ClientCertificate</span><span class="sxs-lookup"><span data-stu-id="4ff2d-191">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="4ff2d-192">ssl。LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="4ff2d-192">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="4ff2d-193">服务器。RemoteIpAddress</span><span class="sxs-lookup"><span data-stu-id="4ff2d-193">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="4ff2d-194">服务器。端口远程端口</span><span class="sxs-lookup"><span data-stu-id="4ff2d-194">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="4ff2d-195">服务器。LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="4ff2d-195">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="4ff2d-196">服务器。LocalPort</span><span class="sxs-lookup"><span data-stu-id="4ff2d-196">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="4ff2d-197">服务器。IsLocal</span><span class="sxs-lookup"><span data-stu-id="4ff2d-197">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="4ff2d-198">服务器。OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="4ff2d-198">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |


### <a name="sendfiles-v030"></a><span data-ttu-id="4ff2d-199">SendFiles v0.3.0</span><span class="sxs-lookup"><span data-stu-id="4ff2d-199">SendFiles v0.3.0</span></span>

| <span data-ttu-id="4ff2d-200">键</span><span class="sxs-lookup"><span data-stu-id="4ff2d-200">Key</span></span>               | <span data-ttu-id="4ff2d-201">值 （类型）</span><span class="sxs-lookup"><span data-stu-id="4ff2d-201">Value (type)</span></span> | <span data-ttu-id="4ff2d-202">描述</span><span class="sxs-lookup"><span data-stu-id="4ff2d-202">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="4ff2d-203">sendfile。SendAsync</span><span class="sxs-lookup"><span data-stu-id="4ff2d-203">sendfile.SendAsync</span></span> | <span data-ttu-id="4ff2d-204">请参阅[委托签名](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="4ff2d-204">See [delegate signature](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="4ff2d-205">每个请求</span><span class="sxs-lookup"><span data-stu-id="4ff2d-205">Per Request</span></span> |


### <a name="opaque-v030"></a><span data-ttu-id="4ff2d-206">不透明 v0.3.0</span><span class="sxs-lookup"><span data-stu-id="4ff2d-206">Opaque v0.3.0</span></span>

| <span data-ttu-id="4ff2d-207">键</span><span class="sxs-lookup"><span data-stu-id="4ff2d-207">Key</span></span>               | <span data-ttu-id="4ff2d-208">值 （类型）</span><span class="sxs-lookup"><span data-stu-id="4ff2d-208">Value (type)</span></span> | <span data-ttu-id="4ff2d-209">描述</span><span class="sxs-lookup"><span data-stu-id="4ff2d-209">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="4ff2d-210">不透明。版本</span><span class="sxs-lookup"><span data-stu-id="4ff2d-210">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="4ff2d-211">不透明。升级</span><span class="sxs-lookup"><span data-stu-id="4ff2d-211">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="4ff2d-212">请参阅[委托签名](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="4ff2d-212">See [delegate signature](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="4ff2d-213">不透明。流</span><span class="sxs-lookup"><span data-stu-id="4ff2d-213">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="4ff2d-214">不透明。CallCancelled</span><span class="sxs-lookup"><span data-stu-id="4ff2d-214">opaque.CallCancelled</span></span> | `CancellationToken` |  |


### <a name="websocket-v030"></a><span data-ttu-id="4ff2d-215">WebSocket v0.3.0</span><span class="sxs-lookup"><span data-stu-id="4ff2d-215">WebSocket v0.3.0</span></span>

| <span data-ttu-id="4ff2d-216">键</span><span class="sxs-lookup"><span data-stu-id="4ff2d-216">Key</span></span>               | <span data-ttu-id="4ff2d-217">值 （类型）</span><span class="sxs-lookup"><span data-stu-id="4ff2d-217">Value (type)</span></span> | <span data-ttu-id="4ff2d-218">描述</span><span class="sxs-lookup"><span data-stu-id="4ff2d-218">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="4ff2d-219">websocket。版本</span><span class="sxs-lookup"><span data-stu-id="4ff2d-219">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="4ff2d-220">websocket。接受</span><span class="sxs-lookup"><span data-stu-id="4ff2d-220">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="4ff2d-221">请参阅[委托签名](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="4ff2d-221">See [delegate signature](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="4ff2d-222">websocket。AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="4ff2d-222">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="4ff2d-223">非规范</span><span class="sxs-lookup"><span data-stu-id="4ff2d-223">Non-spec</span></span> |
| <span data-ttu-id="4ff2d-224">websocket。子协议</span><span class="sxs-lookup"><span data-stu-id="4ff2d-224">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="4ff2d-225">请参阅[RFC6455 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2)步骤 5.5</span><span class="sxs-lookup"><span data-stu-id="4ff2d-225">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="4ff2d-226">websocket。SendAsync</span><span class="sxs-lookup"><span data-stu-id="4ff2d-226">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="4ff2d-227">请参阅[委托签名](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="4ff2d-227">See [delegate signature](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="4ff2d-228">websocket。ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="4ff2d-228">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="4ff2d-229">请参阅[委托签名](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="4ff2d-229">See [delegate signature](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="4ff2d-230">websocket。CloseAsync</span><span class="sxs-lookup"><span data-stu-id="4ff2d-230">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="4ff2d-231">请参阅[委托签名](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="4ff2d-231">See [delegate signature](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="4ff2d-232">websocket。CallCancelled</span><span class="sxs-lookup"><span data-stu-id="4ff2d-232">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="4ff2d-233">websocket。ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="4ff2d-233">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="4ff2d-234">Optional</span><span class="sxs-lookup"><span data-stu-id="4ff2d-234">Optional</span></span> |
| <span data-ttu-id="4ff2d-235">websocket。ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="4ff2d-235">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="4ff2d-236">Optional</span><span class="sxs-lookup"><span data-stu-id="4ff2d-236">Optional</span></span> |


## <a name="additional-resources"></a><span data-ttu-id="4ff2d-237">其他资源</span><span class="sxs-lookup"><span data-stu-id="4ff2d-237">Additional Resources</span></span>

* [<span data-ttu-id="4ff2d-238">中间件</span><span class="sxs-lookup"><span data-stu-id="4ff2d-238">Middleware</span></span>](middleware.md)

* [<span data-ttu-id="4ff2d-239">服务器</span><span class="sxs-lookup"><span data-stu-id="4ff2d-239">Servers</span></span>](servers/index.md)
