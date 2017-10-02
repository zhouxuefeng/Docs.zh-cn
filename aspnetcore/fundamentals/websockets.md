---
title: "WebSockets 支持，在 ASP.NET 核心"
author: tdykstra
description: "了解如何开始使用 ASP.NET Core 中的 Websocket。"
keywords: "ASP.NET 核心 Websocket"
ms.author: tdykstra
manager: wpickett
ms.date: 03/25/2017
ms.topic: article
ms.assetid: 0e0fedcd-a7b4-4479-8ae0-36eab0229d7e
ms.technology: aspnet
ms.prod: aspnet-core
uid: fundamentals/websockets
ms.openlocfilehash: 114d52d831668e5facd1142b5f9e5f68e7456f7e
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2017
---
# <a name="introduction-to-websockets-in-aspnet-core"></a>在 ASP.NET Core Websocket 简介

通过[Tom Dykstra](https://github.com/tdykstra)和[Andrew Stanton 护士](https://github.com/anurse)

此文章介绍了如何开始使用 ASP.NET Core 中的 Websocket。 [WebSocket](https://wikipedia.org/wiki/WebSocket)是一个协议，通过 TCP 连接启用持久的双向通信通道。 用于如聊天、 股票代码、 游戏的应用程序，你希望在 web 应用程序中的实时功能的任何位置。

[查看或下载的示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/websockets/sample)([如何下载](xref:tutorials/index#how-to-download-a-sample))。 请参阅[后续步骤](#next-steps)部分以了解更多信息。


## <a name="prerequisites"></a>先决条件

* ASP.NET 核心 1.1 （不会运行 1.0）
* 任何 ASP.NET Core 运行的操作系统：
  
  * Windows 7 / Windows Server 2008 及更高版本
  * Linux
  * macOS

* **异常**： 如果在 IIS 中，使用的 Windows 上运行你的应用程序或使用 WebListener，必须使用：

  * Windows 8 / Windows Server 2012 或更高版本
  * IIS 8 / IIS 8 Express
  * 必须在 IIS 中启用 WebSocket

* 有关支持的浏览器，请参阅 http://caniuse.com/#feat=websockets。

## <a name="when-to-use-it"></a>何时使用

当你需要直接使用的套接字连接时，请使用 Websocket。 例如，你可能要进行实时的游戏需要最佳性能。

[ASP.NET SignalR](https://docs.microsoft.com/aspnet/signalr/overview/getting-started/introduction-to-signalr)提供更丰富的实时功能，但它的应用程序模型仅在 ASP.NET 中，不带 ASP.NET Core 上运行。 SignalR Core 版本正处于开发;要了解其进度，请参阅[SignalR Core 的 GitHub 存储库](https://github.com/aspnet/SignalR)。

如果你不想等待 SignalR Core，你现在可以使用 Websocket 直接。 但是，你可能需要开发功能，它们提供将 SignalR，如：

* 支持广泛的浏览器版本，通过使用自动回退到备用的传输方法。
* 当连接将自动重新连接。
* 支持的客户端调用方法的服务器上，反之亦然。
* 缩放到多个服务器的支持。

## <a name="how-to-use-it"></a>如何使用它

* 安装[Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/)包。
* 配置该中间件。
* 接受 WebSocket 请求。
* 发送和接收消息。

### <a name="configure-the-middleware"></a>配置该中间件

添加中的 Websocket 中间件`Configure`方法`Startup`类。

[!code-csharp[](websockets/sample/Startup.cs?name=UseWebSockets)]

可以配置下列设置：

* `KeepAliveInterval`-如何频繁地发送到客户端，以确保代理使连接保持打开的"ping"帧。
* `ReceiveBufferSize`的用于接收数据的缓冲区大小。 只有高级的用户将需要更改此设置，以进行性能优化基于其数据的大小。

[!code-csharp[](websockets/sample/Startup.cs?name=UseWebSocketsOptions)]

### <a name="accept-websocket-requests"></a>接受 WebSocket 请求

请求生命周期中某个位置更高版本 (后面`Configure`方法或 MVC 操作，例如中) 检查它是否是 WebSocket 请求并接受 WebSocket 请求。

此示例摘自更高版本在`Configure`方法。

[!code-csharp[](websockets/sample/Startup.cs?name=AcceptWebSocket&highlight=7)]

上任何 URL，也可以来自 WebSocket 请求，但此代码示例仅接受请求`/ws`。

### <a name="send-and-receive-messages"></a>发送和接收消息

`AcceptWebSocketAsync`方法升级 TCP 连接到 WebSocket 连接并为你提供[WebSocket](https://docs.microsoft.com/dotnet/core/api/system.net.websockets.websocket)对象。 使用 WebSocket 对象发送和接收消息。

接受的 WebSocket 请求前面所示的代码将传递`WebSocket`对象传递给`Echo`方法; 如果此处的`Echo`方法。 该代码将收到一条消息，并立即发送同一消息。 将一直留在循环执行该操作，直到客户端将关闭连接。 

[!code-csharp[](websockets/sample/Startup.cs?name=Echo)]

当您在开始此循环之前接受 WebSocket 时，中间件管道结束。  在关闭套接字后, 管道展开。 即，向前移动在管道中当接受 WebSocket，就像它在请求停止将命中 MVC 操作，例如时。  但当你完成此循环，并关闭套接字，则继续备份管道处理请求。

## <a name="next-steps"></a>后续步骤

[示例应用程序](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/websockets/sample)附带此文章是一个简单的回显应用程序。 它具有一个网页，建立 WebSocket 连接，以及服务器只重新发送回客户端收到任何消息。 （它具有将设置为从与 IIS Express 的 Visual Studio 运行） 在命令提示符下运行它，然后导航到 http://localhost:5000/。 网页显示在左上方的连接状态：

![网页的初始状态](websockets/_static/start.png)

选择**连接**向显示的 URL 中发送的 WebSocket 请求。  输入测试消息，然后选择**发送**。 完成后，选择**关闭套接字**。 **通信日志**部分报告每次打开时，发送，并为它的关闭操作。

![网页的初始状态](websockets/_static/end.png)
