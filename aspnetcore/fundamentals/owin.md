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
ms.openlocfilehash: 796d075d4d0c6b888be4fc2225fc482acdbad498
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="introduction-to-open-web-interface-for-net-owin"></a>若要打开.NET (OWIN) 的 Web 界面的简介

通过[Steve Smith](https://ardalis.com/)和[Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET 核心支持打开的 Web 接口的.NET (OWIN)。 OWIN 允许 web 应用程序从 web 服务器分离。 它定义的中间件来用于在管道中使用，以处理请求和响应关联的标准方法。 ASP.NET Core 应用程序和中间件可以与基于 OWIN 的应用程序、 服务器和中间件互操作。

OWIN 提供了允许与不同的对象模型一起使用的两个框架解除层。 `Microsoft.AspNetCore.Owin`包提供了两个适配器实现：
- OWIN 到 ASP.NET 核心 
- OWIN 到 ASP.NET 核心

这允许基于 OWIN 兼容服务器/主机，或在 ASP.NET Core 上运行其他 OWIN 兼容组件托管的 ASP.NET Core。

注意： 使用这些适配器附带有一定的性能开销。 使用仅 ASP.NET 核心组件的应用程序不应使用 Owin 包或适配器。

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/owin/sample)

## <a name="running-owin-middleware-in-the-aspnet-pipeline"></a>在 ASP.NET 管线中运行 OWIN 中间件

ASP.NET 核心 OWIN 支持部署的一部分`Microsoft.AspNetCore.Owin`包。 你可以通过安装此包导入你的项目的 OWIN 支持。

OWIN 中间件符合[OWIN 规范](http://owin.org/spec/spec/owin-1.0.0.html)，这要求`Func<IDictionary<string, object>, Task>`接口和特定的密钥设置 (如`owin.ResponseBody`)。 以下简单的 OWIN 中间件将显示"Hello World":

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

示例签名返回`Task`并接受`IDictionary<string, object>`OWIN 通过所需的方式。

下面的代码演示如何添加`OwinHello`（上面所述） 向 ASP.NET 管道使用的中间件`UseOwin`扩展方法。

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

你可以配置在 OWIN 管道中进行其他操作。

> [!NOTE]
> 仅应在首次向响应流写入之前修改响应标头。

> [!NOTE]
> 多次调用`UseOwin`出于性能原因不建议这样做。 如果组合在一起，OWIN 组件将最好地运行。

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

## <a name="using-aspnet-hosting-on-an-owin-based-server"></a>使用基于 OWIN 的服务器上的 ASP.NET 宿主

基于 OWIN 的服务器可以承载 ASP.NET 应用程序。 此类的一台服务器是[Nowin](https://github.com/Bobris/Nowin)，.NET OWIN web 服务器。 在本文示例中，我已包含引用 Nowin 并使用它来创建一个项目项目`IServer`能够自承载 ASP.NET Core。

[!code-csharp[Main](owin/sample/src/NowinSample/Program.cs?highlight=15)]

`IServer`是接口，需要`Features`属性和`Start`方法。

`Start`负责配置和启动服务器，在这种情况下通过一系列从 IServerAddressesFeature 设置地址分析的 fluent API 调用。 请注意的 fluent 配置`_builder`变量指定将由处理请求`appFunc`先前在方法中定义。 这`Func`称为上每个请求以处理传入的请求。

我们还将添加`IWebHostBuilder`扩展，以便可以方便地添加和配置 Nowin 服务器。

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

与此就地，所有，具有需要运行 ASP.NET 应用程序使用此自定义服务器调用扩展*Program.cs*:

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

了解有关 ASP.NET[服务器](servers/index.md)。

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a>基于 OWIN 的服务器上运行 ASP.NET 核心，并使用其 Websocket 支持

如何基于 OWIN 的服务器的功能的另一个示例可以利用 ASP.NET 核心是对等 Websocket 功能的访问。 在前面的示例使用的.NET OWIN web 服务器具有用于 Web 套接字内置的这可以利用 ASP.NET Core 应用程序支持。 下面的示例演示一个简单的 web 应用，支持 Web 套接字回显 Websocket 通过向服务器发送的所有内容。

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

这[示例](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/owin/sample)使用相同配置`NowinServer`与之前的唯一的区别是在应用程序中的配置方式其`Configure`方法。 测试使用[简单 websocket 客户端](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en)演示应用程序：

![Web 套接字测试客户端](owin/_static/websocket-test.png)

## <a name="owin-environment"></a>OWIN 环境

你可以构造 OWIN 环境使用`HttpContext`。

<!-- literal_block {"ids": [], "names": [], "highlight_args": {}, "backrefs": [], "dupnames": [], "linenos": false, "classes": [], "xml:space": "preserve", "language": "c#"} -->

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a>OWIN 键

取决于 OWIN`IDictionary<string,object>`对象进行通信信息的整个 HTTP 请求/响应交换。 ASP.NET 核心实现下面列出的密钥。 请参阅[主规范、 扩展](http://owin.org/#spec)，和[OWIN 键指导原则和常见的键](http://owin.org/spec/spec/CommonKeys.html)。

### <a name="request-data-owin-v100"></a>请求的数据 (OWIN v1.0.0)

| 键               | 值 （类型） | 描述 |
| ----------------- | ------------ | ----------- |
| owin。RequestScheme | `String` |  |
| owin。RequestMethod  | `String` | |    
| owin。RequestPathBase  | `String` | |    
| owin。RequestPath | `String` | |     
| owin。RequestQueryString  | `String` | |    
| owin。RequestProtocol  | `String` | |    
| owin。RequestHeaders | `IDictionary<string,string[]>`  | |
| owin。RequestBody | `Stream`  | |

### <a name="request-data-owin-v110"></a>请求的数据 (OWIN v1.1.0)

| 键               | 值 （类型） | 描述 |
| ----------------- | ------------ | ----------- |
| owin。请求 Id | `String` | Optional |

### <a name="response-data-owin-v100"></a>响应数据 (OWIN v1.0.0)

| 键               | 值 （类型） | 描述 |
| ----------------- | ------------ | ----------- |
| owin。ResponseStatusCode | `int` | Optional |
| owin。ResponseReasonPhrase | `String` | Optional |
| owin。ResponseHeaders | `IDictionary<string,string[]>`  | |
| owin。ResponseBody | `Stream`  | |


### <a name="other-data-owin-v100"></a>其他数据 (OWIN v1.0.0)

| 键               | 值 （类型） | 描述 |
| ----------------- | ------------ | ----------- |
| owin。CallCancelled | `CancellationToken` |  |
| owin。版本  | `String` | |   


### <a name="common-keys"></a>常见的键

| 键               | 值 （类型） | 描述 |
| ----------------- | ------------ | ----------- |
| ssl。ClientCertificate | `X509Certificate` |  |
| ssl。LoadClientCertAsync  | `Func<Task>` | |    
| 服务器。RemoteIpAddress  | `String` | |    
| 服务器。端口远程端口 | `String` | |     
| 服务器。LocalIpAddress  | `String` | |    
| 服务器。LocalPort  | `String` | |    
| 服务器。IsLocal  | `bool` | |    
| 服务器。OnSendingHeaders  | `Action<Action<object>,object>` | |


### <a name="sendfiles-v030"></a>SendFiles v0.3.0

| 键               | 值 （类型） | 描述 |
| ----------------- | ------------ | ----------- |
| sendfile。SendAsync | 请参阅[委托签名](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) | 每个请求 |


### <a name="opaque-v030"></a>不透明 v0.3.0

| 键               | 值 （类型） | 描述 |
| ----------------- | ------------ | ----------- |
| 不透明。版本 | `String` |  |
| 不透明。升级 | `OpaqueUpgrade` | 请参阅[委托签名](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) |
| 不透明。流 | `Stream` |  |
| 不透明。CallCancelled | `CancellationToken` |  |


### <a name="websocket-v030"></a>WebSocket v0.3.0

| 键               | 值 （类型） | 描述 |
| ----------------- | ------------ | ----------- |
| websocket。版本 | `String` |  |
| websocket。接受 | `WebSocketAccept` | 请参阅[委托签名](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) |
| websocket。AcceptAlt |  | 非规范 |
| websocket。子协议 | `String` | 请参阅[RFC6455 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2)步骤 5.5 |
| websocket。SendAsync | `WebSocketSendAsync` | 请参阅[委托签名](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| websocket。ReceiveAsync | `WebSocketReceiveAsync` | 请参阅[委托签名](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| websocket。CloseAsync | `WebSocketCloseAsync` | 请参阅[委托签名](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| websocket。CallCancelled | `CancellationToken` |  |
| websocket。ClientCloseStatus | `int` | Optional |
| websocket。ClientCloseDescription | `String` | Optional |


## <a name="additional-resources"></a>其他资源

* [中间件](middleware.md)

* [服务器](servers/index.md)
