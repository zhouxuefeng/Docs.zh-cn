---
title: "请求中的新 ASP.NET 核心功能"
author: ardalis
description: 
keywords: "ASP.NET 核心"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: d1fbd23c-2ff9-4216-b908-0201ff3afb7c
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/request-features
ms.openlocfilehash: e8d04ef7df34fe1421b2c52f137511fc6baae674
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="request-features-in-aspnet-core"></a><span data-ttu-id="ab094-103">请求中的新 ASP.NET 核心功能</span><span class="sxs-lookup"><span data-stu-id="ab094-103">Request Features in ASP.NET Core</span></span>

<span data-ttu-id="ab094-104">通过[Steve Smith](http://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="ab094-104">By [Steve Smith](http://ardalis.com)</span></span>

<span data-ttu-id="ab094-105">Web 服务器实现在接口中定义的与 HTTP 请求和响应相关的详细信息。</span><span class="sxs-lookup"><span data-stu-id="ab094-105">Web server implementation details related to HTTP requests and responses are defined in interfaces.</span></span> <span data-ttu-id="ab094-106">服务器实现和中间件使用这些接口来创建和修改应用程序的托管管道。</span><span class="sxs-lookup"><span data-stu-id="ab094-106">These interfaces are used by server implementations and middleware to create and modify the application's hosting pipeline.</span></span>

## <a name="feature-interfaces"></a><span data-ttu-id="ab094-107">功能接口</span><span class="sxs-lookup"><span data-stu-id="ab094-107">Feature interfaces</span></span>

<span data-ttu-id="ab094-108">ASP.NET 核心定义了多个中的 HTTP 功能接口`Microsoft.AspNetCore.Http.Features`用于服务器以确定它们支持的功能。</span><span class="sxs-lookup"><span data-stu-id="ab094-108">ASP.NET Core defines a number of HTTP feature interfaces in `Microsoft.AspNetCore.Http.Features` which are used by servers to identify the features they support.</span></span> <span data-ttu-id="ab094-109">以下功能接口处理请求，并返回响应：</span><span class="sxs-lookup"><span data-stu-id="ab094-109">The following feature interfaces handle requests and return responses:</span></span>

<span data-ttu-id="ab094-110">`IHttpRequestFeature`定义 HTTP 请求，包括协议、 路径、 查询字符串、 标头和正文的结构。</span><span class="sxs-lookup"><span data-stu-id="ab094-110">`IHttpRequestFeature` Defines the structure of an HTTP request, including the protocol, path, query string, headers, and body.</span></span>

<span data-ttu-id="ab094-111">`IHttpResponseFeature`定义 HTTP 响应，包括状态代码、 标头和响应的正文的结构。</span><span class="sxs-lookup"><span data-stu-id="ab094-111">`IHttpResponseFeature` Defines the structure of an HTTP response, including the status code, headers, and body of the response.</span></span>

<span data-ttu-id="ab094-112">`IHttpAuthenticationFeature`定义支持，用于标识用户基于`ClaimsPrincipal`并指定身份验证处理程序。</span><span class="sxs-lookup"><span data-stu-id="ab094-112">`IHttpAuthenticationFeature` Defines support for identifying users based on a `ClaimsPrincipal` and specifying an authentication handler.</span></span>

<span data-ttu-id="ab094-113">`IHttpUpgradeFeature`定义支持[HTTP 升级](https://tools.ietf.org/html/rfc2616.html#section-14.42)，它允许客户端指定的其他协议想要使用如果服务器想要切换协议。</span><span class="sxs-lookup"><span data-stu-id="ab094-113">`IHttpUpgradeFeature` Defines support for [HTTP Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), which allow the client to specify which additional protocols it would like to use if the server wishes to switch protocols.</span></span>

<span data-ttu-id="ab094-114">`IHttpBufferingFeature`定义禁用缓冲的请求和/或响应的方法。</span><span class="sxs-lookup"><span data-stu-id="ab094-114">`IHttpBufferingFeature` Defines methods for disabling buffering of requests and/or responses.</span></span>

<span data-ttu-id="ab094-115">`IHttpConnectionFeature`定义本地和远程地址和端口的属性。</span><span class="sxs-lookup"><span data-stu-id="ab094-115">`IHttpConnectionFeature` Defines properties for local and remote addresses and ports.</span></span>

<span data-ttu-id="ab094-116">`IHttpRequestLifetimeFeature`定义支持中止连接，或检测如果请求已被终止提前，例如为客户端断开连接。</span><span class="sxs-lookup"><span data-stu-id="ab094-116">`IHttpRequestLifetimeFeature` Defines support for aborting connections, or detecting if a request has been terminated prematurely, such as by a client disconnect.</span></span>

<span data-ttu-id="ab094-117">`IHttpSendFileFeature`定义以异步方式发送文件的方法。</span><span class="sxs-lookup"><span data-stu-id="ab094-117">`IHttpSendFileFeature` Defines a method for sending files asynchronously.</span></span>

<span data-ttu-id="ab094-118">`IHttpWebSocketFeature`定义支持 web 套接字的 API。</span><span class="sxs-lookup"><span data-stu-id="ab094-118">`IHttpWebSocketFeature` Defines an API for supporting web sockets.</span></span>

<span data-ttu-id="ab094-119">`IHttpRequestIdentifierFeature`添加可实现来唯一标识请求的属性。</span><span class="sxs-lookup"><span data-stu-id="ab094-119">`IHttpRequestIdentifierFeature` Adds a property that can be implemented to uniquely identify requests.</span></span>

<span data-ttu-id="ab094-120">`ISessionFeature`定义`ISessionFactory`和`ISession`支持用户会话的抽象。</span><span class="sxs-lookup"><span data-stu-id="ab094-120">`ISessionFeature` Defines `ISessionFactory` and `ISession` abstractions for supporting user sessions.</span></span>

<span data-ttu-id="ab094-121">`ITlsConnectionFeature`定义用于检索客户端证书的 API。</span><span class="sxs-lookup"><span data-stu-id="ab094-121">`ITlsConnectionFeature` Defines an API for retrieving client certificates.</span></span>

<span data-ttu-id="ab094-122">`ITlsTokenBindingFeature`定义使用的 TLS 标记绑定参数的方法。</span><span class="sxs-lookup"><span data-stu-id="ab094-122">`ITlsTokenBindingFeature` Defines methods for working with TLS token binding parameters.</span></span>

> [!NOTE]
> <span data-ttu-id="ab094-123">`ISessionFeature`不是一项服务器功能，但由实现`SessionMiddleware`(请参阅[管理应用程序状态](app-state.md))。</span><span class="sxs-lookup"><span data-stu-id="ab094-123">`ISessionFeature` is not a server feature, but is implemented by the `SessionMiddleware` (see [Managing Application State](app-state.md)).</span></span>

## <a name="feature-collections"></a><span data-ttu-id="ab094-124">功能集合</span><span class="sxs-lookup"><span data-stu-id="ab094-124">Feature collections</span></span>

<span data-ttu-id="ab094-125">`Features`属性`HttpContext`提供用于获取和设置当前请求的可用 HTTP 功能的接口。</span><span class="sxs-lookup"><span data-stu-id="ab094-125">The `Features` property of `HttpContext` provides an interface for getting and setting the available HTTP features for the current request.</span></span> <span data-ttu-id="ab094-126">由于功能集合是可变即使在请求的上下文中，中间件可以用于修改该集合并添加对其他功能的支持。</span><span class="sxs-lookup"><span data-stu-id="ab094-126">Since the feature collection is mutable even within the context of a request, middleware can be used to modify the collection and add support for additional features.</span></span>

## <a name="middleware-and-request-features"></a><span data-ttu-id="ab094-127">中间件和请求的功能</span><span class="sxs-lookup"><span data-stu-id="ab094-127">Middleware and request features</span></span>

<span data-ttu-id="ab094-128">负责创建功能集合服务器时，中间件可以添加到此集合和使用集合中的功能。</span><span class="sxs-lookup"><span data-stu-id="ab094-128">While servers are responsible for creating the feature collection, middleware can both add to this collection and consume features from the collection.</span></span> <span data-ttu-id="ab094-129">例如，`StaticFileMiddleware`访问`IHttpSendFileFeature`功能。</span><span class="sxs-lookup"><span data-stu-id="ab094-129">For example, the `StaticFileMiddleware` accesses the `IHttpSendFileFeature` feature.</span></span> <span data-ttu-id="ab094-130">如果存在该功能，它用于将从其物理路径发送请求的静态文件。</span><span class="sxs-lookup"><span data-stu-id="ab094-130">If the feature exists, it is used to send the requested static file from its physical path.</span></span> <span data-ttu-id="ab094-131">否则，较慢的替代方法用于将文件发送。</span><span class="sxs-lookup"><span data-stu-id="ab094-131">Otherwise, a slower alternative method is used to send the file.</span></span> <span data-ttu-id="ab094-132">如果可能，`IHttpSendFileFeature`允许操作系统打开文件并执行直接内核模式复制到网络卡。</span><span class="sxs-lookup"><span data-stu-id="ab094-132">When available, the `IHttpSendFileFeature` allows the operating system to open the file and perform a direct kernel mode copy to the network card.</span></span>

<span data-ttu-id="ab094-133">此外，中间件可以添加到服务器建立的功能集合。</span><span class="sxs-lookup"><span data-stu-id="ab094-133">Additionally, middleware can add to the feature collection established by the server.</span></span> <span data-ttu-id="ab094-134">甚至可以通过中间件，允许以增加服务器的功能的中间件替换为现有功能。</span><span class="sxs-lookup"><span data-stu-id="ab094-134">Existing features can even be replaced by middleware, allowing the middleware to augment the functionality of the server.</span></span> <span data-ttu-id="ab094-135">添加到集合的功能都可立即用于其他中间件或基础应用程序本身在请求管道的更高版本。</span><span class="sxs-lookup"><span data-stu-id="ab094-135">Features added to the collection are available immediately to other middleware or the underlying application itself later in the request pipeline.</span></span>

<span data-ttu-id="ab094-136">通过结合自定义服务器实现和特定的中间件增强功能，可构造精确的应用程序需要的功能集。</span><span class="sxs-lookup"><span data-stu-id="ab094-136">By combining custom server implementations and specific middleware enhancements, the precise set of features an application requires can be constructed.</span></span> <span data-ttu-id="ab094-137">这允许缺少功能而无需在服务器中，更改要添加的可确保仅功能的最少工作量公开，从而限制攻击面区域和提高性能。</span><span class="sxs-lookup"><span data-stu-id="ab094-137">This allows missing features to be added without requiring a change in server, and ensures only the minimal amount of features are exposed, thus limiting attack surface area and improving performance.</span></span>

## <a name="summary"></a><span data-ttu-id="ab094-138">摘要</span><span class="sxs-lookup"><span data-stu-id="ab094-138">Summary</span></span>

<span data-ttu-id="ab094-139">功能接口定义给定的请求可能支持的特定 HTTP 功能。</span><span class="sxs-lookup"><span data-stu-id="ab094-139">Feature interfaces define specific HTTP features that a given request may support.</span></span> <span data-ttu-id="ab094-140">服务器定义的功能，集合和该服务器时，支持的功能的初始集，但中间件可以用于增强这些功能。</span><span class="sxs-lookup"><span data-stu-id="ab094-140">Servers define collections of features, and the initial set of features supported by that server, but middleware can be used to enhance these features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ab094-141">其他资源</span><span class="sxs-lookup"><span data-stu-id="ab094-141">Additional Resources</span></span>

* [<span data-ttu-id="ab094-142">服务器</span><span class="sxs-lookup"><span data-stu-id="ab094-142">Servers</span></span>](servers/index.md)

* [<span data-ttu-id="ab094-143">中间件</span><span class="sxs-lookup"><span data-stu-id="ab094-143">Middleware</span></span>](middleware.md)

* [<span data-ttu-id="ab094-144">打开.NET (OWIN) 的 Web 界面</span><span class="sxs-lookup"><span data-stu-id="ab094-144">Open Web Interface for .NET (OWIN)</span></span>](owin.md)
