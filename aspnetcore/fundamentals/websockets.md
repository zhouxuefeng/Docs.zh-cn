---
title: "WebSockets 支持，在 ASP.NET 核心"
author: tdykstra
description: "什么是 Websocket 支持在 ASP.NET 核心以及如何使用它。"
keywords: "ASP.NET 核心 Websocket"
ms.author: tdykstra
manager: wpickett
ms.date: 03/25/2017
ms.topic: article
ms.assetid: 0e0fedcd-a7b4-4479-8ae0-36eab0229d7e
ms.technology: aspnet
ms.prod: aspnet-core
uid: fundamentals/websockets
ms.openlocfilehash: daa36d117749868e4bb9311a941b7751d7b64744
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="introduction-to-websockets-in-aspnet-core"></a><span data-ttu-id="5b804-104">在 ASP.NET Core Websocket 简介</span><span class="sxs-lookup"><span data-stu-id="5b804-104">Introduction to WebSockets in ASP.NET Core</span></span>

<span data-ttu-id="5b804-105">通过[Tom Dykstra](https://github.com/tdykstra)和[Andrew Stanton 护士](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="5b804-105">By [Tom Dykstra](https://github.com/tdykstra) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="5b804-106">此文章介绍了如何开始使用 ASP.NET Core 中的 Websocket。</span><span class="sxs-lookup"><span data-stu-id="5b804-106">This article explains how to get started with WebSockets in ASP.NET Core.</span></span> <span data-ttu-id="5b804-107">[WebSocket](https://en.wikipedia.org/wiki/WebSocket)是一个协议，通过 TCP 连接启用持久的双向通信通道。</span><span class="sxs-lookup"><span data-stu-id="5b804-107">[WebSocket](https://en.wikipedia.org/wiki/WebSocket) is a protocol that enables two-way persistent communication channels over TCP connections.</span></span> <span data-ttu-id="5b804-108">用于如聊天、 股票代码、 游戏的应用程序，你希望在 web 应用程序中的实时功能的任何位置。</span><span class="sxs-lookup"><span data-stu-id="5b804-108">It is used for applications such as chat, stock tickers, games, anywhere you want real-time functionality in a web application.</span></span>

<span data-ttu-id="5b804-109">[查看或下载的示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/websockets/sample)。</span><span class="sxs-lookup"><span data-stu-id="5b804-109">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/websockets/sample).</span></span> <span data-ttu-id="5b804-110">请参阅[后续步骤](#next-steps)部分以了解更多信息。</span><span class="sxs-lookup"><span data-stu-id="5b804-110">See the [Next Steps](#next-steps) section for more information.</span></span>


## <a name="prerequisites"></a><span data-ttu-id="5b804-111">先决条件</span><span class="sxs-lookup"><span data-stu-id="5b804-111">Prerequisites</span></span>

* <span data-ttu-id="5b804-112">ASP.NET 核心 1.1 （不会运行 1.0）</span><span class="sxs-lookup"><span data-stu-id="5b804-112">ASP.NET Core 1.1 (does not run on 1.0)</span></span>
* <span data-ttu-id="5b804-113">任何 ASP.NET Core 运行的操作系统：</span><span class="sxs-lookup"><span data-stu-id="5b804-113">Any OS that ASP.NET Core runs on:</span></span>
  
  * <span data-ttu-id="5b804-114">Windows 7 / Windows Server 2008 及更高版本</span><span class="sxs-lookup"><span data-stu-id="5b804-114">Windows 7 / Windows Server 2008 and later</span></span>
  * <span data-ttu-id="5b804-115">Linux</span><span class="sxs-lookup"><span data-stu-id="5b804-115">Linux</span></span>
  * <span data-ttu-id="5b804-116">macOS</span><span class="sxs-lookup"><span data-stu-id="5b804-116">macOS</span></span>

* <span data-ttu-id="5b804-117">**异常**： 如果在 IIS 中，使用的 Windows 上运行你的应用程序或使用 WebListener，必须使用：</span><span class="sxs-lookup"><span data-stu-id="5b804-117">**Exception**: If your app runs on Windows with IIS, or with WebListener, you must use:</span></span>

  * <span data-ttu-id="5b804-118">Windows 8 / Windows Server 2012 或更高版本</span><span class="sxs-lookup"><span data-stu-id="5b804-118">Windows 8 / Windows Server 2012 or later</span></span>
  * <span data-ttu-id="5b804-119">IIS 8 / IIS 8 Express</span><span class="sxs-lookup"><span data-stu-id="5b804-119">IIS 8 / IIS 8 Express</span></span>
  * <span data-ttu-id="5b804-120">必须在 IIS 中启用 WebSocket</span><span class="sxs-lookup"><span data-stu-id="5b804-120">WebSocket must be enabled in IIS</span></span>

* <span data-ttu-id="5b804-121">有关支持的浏览器，请参阅 http://caniuse.com/#feat=websockets。</span><span class="sxs-lookup"><span data-stu-id="5b804-121">For supported browsers, see http://caniuse.com/#feat=websockets.</span></span>

## <a name="when-to-use-it"></a><span data-ttu-id="5b804-122">何时使用</span><span class="sxs-lookup"><span data-stu-id="5b804-122">When to use it</span></span>

<span data-ttu-id="5b804-123">当你需要直接使用的套接字连接时，请使用 Websocket。</span><span class="sxs-lookup"><span data-stu-id="5b804-123">Use WebSockets when you need to work directly with a socket connection.</span></span> <span data-ttu-id="5b804-124">例如，你可能要进行实时的游戏需要最佳性能。</span><span class="sxs-lookup"><span data-stu-id="5b804-124">For example, you might need the best possible performance for a real-time game.</span></span>

<span data-ttu-id="5b804-125">[ASP.NET SignalR](https://docs.microsoft.com/aspnet/signalr/overview/getting-started/introduction-to-signalr)提供更丰富的实时功能，但它的应用程序模型仅在 ASP.NET 中，不带 ASP.NET Core 上运行。</span><span class="sxs-lookup"><span data-stu-id="5b804-125">[ASP.NET SignalR](https://docs.microsoft.com/aspnet/signalr/overview/getting-started/introduction-to-signalr) provides a richer application model for real-time functionality, but it runs only on ASP.NET, not ASP.NET Core.</span></span> <span data-ttu-id="5b804-126">SignalR Core 版本正处于开发;要了解其进度，请参阅[SignalR Core 的 GitHub 存储库](https://github.com/aspnet/SignalR)。</span><span class="sxs-lookup"><span data-stu-id="5b804-126">A Core version of SignalR is under development; to follow its progress, see the [GitHub repository for SignalR Core](https://github.com/aspnet/SignalR).</span></span>

<span data-ttu-id="5b804-127">如果你不想等待 SignalR Core，你现在可以使用 Websocket 直接。</span><span class="sxs-lookup"><span data-stu-id="5b804-127">If you don't want to wait for SignalR Core, you can use WebSockets directly now.</span></span> <span data-ttu-id="5b804-128">但是，你可能需要开发功能，它们提供将 SignalR，如：</span><span class="sxs-lookup"><span data-stu-id="5b804-128">But you might have to develop features that SignalR would provide, such as:</span></span>

* <span data-ttu-id="5b804-129">支持广泛的浏览器版本，通过使用自动回退到备用的传输方法。</span><span class="sxs-lookup"><span data-stu-id="5b804-129">Support for a broader range of browser versions by using automatic fallback to alternative transport methods.</span></span>
* <span data-ttu-id="5b804-130">当连接将自动重新连接。</span><span class="sxs-lookup"><span data-stu-id="5b804-130">Automatic reconnection when a connection drops.</span></span>
* <span data-ttu-id="5b804-131">支持的客户端调用方法的服务器上，反之亦然。</span><span class="sxs-lookup"><span data-stu-id="5b804-131">Support for clients calling methods on the server or vice versa.</span></span>
* <span data-ttu-id="5b804-132">缩放到多个服务器的支持。</span><span class="sxs-lookup"><span data-stu-id="5b804-132">Support for scaling to multiple servers.</span></span>

## <a name="how-to-use-it"></a><span data-ttu-id="5b804-133">如何使用它</span><span class="sxs-lookup"><span data-stu-id="5b804-133">How to use it</span></span>

* <span data-ttu-id="5b804-134">安装[Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/)包。</span><span class="sxs-lookup"><span data-stu-id="5b804-134">Install the [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) package.</span></span>
* <span data-ttu-id="5b804-135">配置该中间件。</span><span class="sxs-lookup"><span data-stu-id="5b804-135">Configure the middleware.</span></span>
* <span data-ttu-id="5b804-136">接受 WebSocket 请求。</span><span class="sxs-lookup"><span data-stu-id="5b804-136">Accept WebSocket requests.</span></span>
* <span data-ttu-id="5b804-137">发送和接收消息。</span><span class="sxs-lookup"><span data-stu-id="5b804-137">Send and receive messages.</span></span>

### <a name="configure-the-middleware"></a><span data-ttu-id="5b804-138">配置该中间件</span><span class="sxs-lookup"><span data-stu-id="5b804-138">Configure the middleware</span></span>

<span data-ttu-id="5b804-139">添加中的 Websocket 中间件`Configure`方法`Startup`类。</span><span class="sxs-lookup"><span data-stu-id="5b804-139">Add the WebSockets middleware in the `Configure` method of the `Startup` class.</span></span>

[!code-csharp[](websockets/sample/Startup.cs?name=UseWebSockets)]

<span data-ttu-id="5b804-140">可以配置下列设置：</span><span class="sxs-lookup"><span data-stu-id="5b804-140">The following settings can be configured:</span></span>

* <span data-ttu-id="5b804-141">`KeepAliveInterval`-如何频繁地发送到客户端，以确保代理使连接保持打开的"ping"帧。</span><span class="sxs-lookup"><span data-stu-id="5b804-141">`KeepAliveInterval` - How frequently to send "ping" frames to the client, to ensure proxies keep the connection open.</span></span>
* <span data-ttu-id="5b804-142">`ReceiveBufferSize`的用于接收数据的缓冲区大小。</span><span class="sxs-lookup"><span data-stu-id="5b804-142">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="5b804-143">只有高级的用户将需要更改此设置，以进行性能优化基于其数据的大小。</span><span class="sxs-lookup"><span data-stu-id="5b804-143">Only advanced users would need to change this, for performance tuning based on the size of their data.</span></span>

[!code-csharp[](websockets/sample/Startup.cs?name=UseWebSocketsOptions)]

### <a name="accept-websocket-requests"></a><span data-ttu-id="5b804-144">接受 WebSocket 请求</span><span class="sxs-lookup"><span data-stu-id="5b804-144">Accept WebSocket requests</span></span>

<span data-ttu-id="5b804-145">请求生命周期中某个位置更高版本 (后面`Configure`方法或 MVC 操作，例如中) 检查它是否是 WebSocket 请求并接受 WebSocket 请求。</span><span class="sxs-lookup"><span data-stu-id="5b804-145">Somewhere later in the request life cycle (later in the `Configure` method or in an MVC action, for example) check if it's a WebSocket request and accept the WebSocket request.</span></span>

<span data-ttu-id="5b804-146">此示例摘自更高版本在`Configure`方法。</span><span class="sxs-lookup"><span data-stu-id="5b804-146">This example is from later in the `Configure` method.</span></span>

[!code-csharp[](websockets/sample/Startup.cs?name=AcceptWebSocket&highlight=7)]

<span data-ttu-id="5b804-147">上任何 URL，也可以来自 WebSocket 请求，但此代码示例仅接受请求`/ws`。</span><span class="sxs-lookup"><span data-stu-id="5b804-147">A WebSocket request could come in on any URL, but this sample code only accepts requests for `/ws`.</span></span>

### <a name="send-and-receive-messages"></a><span data-ttu-id="5b804-148">发送和接收消息</span><span class="sxs-lookup"><span data-stu-id="5b804-148">Send and receive messages</span></span>

<span data-ttu-id="5b804-149">`AcceptWebSocketAsync`方法升级 TCP 连接到 WebSocket 连接并为你提供[WebSocket](https://docs.microsoft.com/dotnet/core/api/system.net.websockets.websocket)对象。</span><span class="sxs-lookup"><span data-stu-id="5b804-149">The `AcceptWebSocketAsync` method upgrades the TCP connection to a WebSocket connection and gives you a [WebSocket](https://docs.microsoft.com/dotnet/core/api/system.net.websockets.websocket) object.</span></span> <span data-ttu-id="5b804-150">使用 WebSocket 对象发送和接收消息。</span><span class="sxs-lookup"><span data-stu-id="5b804-150">Use the WebSocket object to send and receive messages.</span></span>

<span data-ttu-id="5b804-151">接受的 WebSocket 请求前面所示的代码将传递`WebSocket`对象传递给`Echo`方法; 如果此处的`Echo`方法。</span><span class="sxs-lookup"><span data-stu-id="5b804-151">The code shown earlier that accepts the WebSocket request passes the `WebSocket` object to an `Echo` method; here's the `Echo` method.</span></span> <span data-ttu-id="5b804-152">该代码将收到一条消息，并立即发送同一消息。</span><span class="sxs-lookup"><span data-stu-id="5b804-152">The code receives a message and immediately sends back the same message.</span></span> <span data-ttu-id="5b804-153">将一直留在循环执行该操作，直到客户端将关闭连接。</span><span class="sxs-lookup"><span data-stu-id="5b804-153">It stays in a loop doing that until the client closes the connection.</span></span> 

[!code-csharp[](websockets/sample/Startup.cs?name=Echo)]

<span data-ttu-id="5b804-154">当您在开始此循环之前接受 WebSocket 时，中间件管道结束。</span><span class="sxs-lookup"><span data-stu-id="5b804-154">When you accept the WebSocket before beginning this loop, the middleware pipeline ends.</span></span>  <span data-ttu-id="5b804-155">在关闭套接字后, 管道展开。</span><span class="sxs-lookup"><span data-stu-id="5b804-155">Upon closing the socket, the pipeline unwinds.</span></span> <span data-ttu-id="5b804-156">即，向前移动在管道中当接受 WebSocket，就像它在请求停止将命中 MVC 操作，例如时。</span><span class="sxs-lookup"><span data-stu-id="5b804-156">That is, the request stops moving forward in the pipeline when you accept a WebSocket, just as it would when you hit an MVC action, for example.</span></span>  <span data-ttu-id="5b804-157">但当你完成此循环，并关闭套接字，则继续备份管道处理请求。</span><span class="sxs-lookup"><span data-stu-id="5b804-157">But when you finish this loop and close the socket, the request proceeds back up the pipeline.</span></span>

## <a name="next-steps"></a><span data-ttu-id="5b804-158">后续步骤</span><span class="sxs-lookup"><span data-stu-id="5b804-158">Next steps</span></span>

<span data-ttu-id="5b804-159">[示例应用程序](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/websockets/sample)附带此文章是一个简单的回显应用程序。</span><span class="sxs-lookup"><span data-stu-id="5b804-159">The [sample application](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/websockets/sample) that accompanies this article is a simple echo application.</span></span> <span data-ttu-id="5b804-160">它具有一个网页，建立 WebSocket 连接，以及服务器只重新发送回客户端收到任何消息。</span><span class="sxs-lookup"><span data-stu-id="5b804-160">It has a web page that makes WebSocket connections, and the server just resends back to the client any messages it receives.</span></span> <span data-ttu-id="5b804-161">（它具有将设置为从与 IIS Express 的 Visual Studio 运行） 在命令提示符下运行它，然后导航到 http://localhost:5000/。</span><span class="sxs-lookup"><span data-stu-id="5b804-161">Run it from a command prompt (it's not set up to run from Visual Studio with IIS Express) and navigate to http://localhost:5000.</span></span> <span data-ttu-id="5b804-162">网页显示在左上方的连接状态：</span><span class="sxs-lookup"><span data-stu-id="5b804-162">The web page shows connection status at the upper left:</span></span>

![网页的初始状态](websockets/_static/start.png)

<span data-ttu-id="5b804-164">选择**连接**向显示的 URL 中发送的 WebSocket 请求。</span><span class="sxs-lookup"><span data-stu-id="5b804-164">Select **Connect** to send a WebSocket request to the URL shown.</span></span>  <span data-ttu-id="5b804-165">输入测试消息，然后选择**发送**。</span><span class="sxs-lookup"><span data-stu-id="5b804-165">Enter a test message and select **Send**.</span></span> <span data-ttu-id="5b804-166">完成后，选择**关闭套接字**。</span><span class="sxs-lookup"><span data-stu-id="5b804-166">When done, select **Close Socket**.</span></span> <span data-ttu-id="5b804-167">**通信日志**部分报告每次打开时，发送，并为它的关闭操作。</span><span class="sxs-lookup"><span data-stu-id="5b804-167">The **Communication Log** section reports each open, send, and close action as it happens.</span></span>

![网页的初始状态](websockets/_static/end.png)
