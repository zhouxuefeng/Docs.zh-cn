---
title: "请求中的新 ASP.NET 核心功能"
author: ardalis
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: d1fbd23c-2ff9-4216-b908-0201ff3afb7c
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/request-features
ms.openlocfilehash: a10aefe3819fb03019575c36274dd164faf7086c
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="request-features-in-aspnet-core"></a>请求中的新 ASP.NET 核心功能

通过[Steve Smith](https://ardalis.com/)

Web 服务器实现在接口中定义的与 HTTP 请求和响应相关的详细信息。 服务器实现和中间件使用这些接口来创建和修改应用程序的托管管道。

## <a name="feature-interfaces"></a>功能接口

ASP.NET 核心定义了多个中的 HTTP 功能接口`Microsoft.AspNetCore.Http.Features`用于服务器以确定它们支持的功能。 以下功能接口处理请求，并返回响应：

`IHttpRequestFeature`定义 HTTP 请求，包括协议、 路径、 查询字符串、 标头和正文的结构。

`IHttpResponseFeature`定义 HTTP 响应，包括状态代码、 标头和响应的正文的结构。

`IHttpAuthenticationFeature`定义支持，用于标识用户基于`ClaimsPrincipal`并指定身份验证处理程序。

`IHttpUpgradeFeature`定义支持[HTTP 升级](https://tools.ietf.org/html/rfc2616.html#section-14.42)，它允许客户端指定的其他协议想要使用如果服务器想要切换协议。

`IHttpBufferingFeature`定义禁用缓冲的请求和/或响应的方法。

`IHttpConnectionFeature`定义本地和远程地址和端口的属性。

`IHttpRequestLifetimeFeature`定义支持中止连接，或检测如果请求已被终止提前，例如为客户端断开连接。

`IHttpSendFileFeature`定义以异步方式发送文件的方法。

`IHttpWebSocketFeature`定义支持 web 套接字的 API。

`IHttpRequestIdentifierFeature`添加可实现来唯一标识请求的属性。

`ISessionFeature`定义`ISessionFactory`和`ISession`支持用户会话的抽象。

`ITlsConnectionFeature`定义用于检索客户端证书的 API。

`ITlsTokenBindingFeature`定义使用的 TLS 标记绑定参数的方法。

> [!NOTE]
> `ISessionFeature`不是一项服务器功能，但由实现`SessionMiddleware`(请参阅[管理应用程序状态](app-state.md))。

## <a name="feature-collections"></a>功能集合

`Features`属性`HttpContext`提供用于获取和设置当前请求的可用 HTTP 功能的接口。 由于功能集合是可变即使在请求的上下文中，中间件可以用于修改该集合并添加对其他功能的支持。

## <a name="middleware-and-request-features"></a>中间件和请求的功能

负责创建功能集合服务器时，中间件可以添加到此集合和使用集合中的功能。 例如，`StaticFileMiddleware`访问`IHttpSendFileFeature`功能。 如果存在该功能，它用于将从其物理路径发送请求的静态文件。 否则，较慢的替代方法用于将文件发送。 如果可能，`IHttpSendFileFeature`允许操作系统打开文件并执行直接内核模式复制到网络卡。

此外，中间件可以添加到服务器建立的功能集合。 甚至可以通过中间件，允许以增加服务器的功能的中间件替换为现有功能。 添加到集合的功能都可立即用于其他中间件或基础应用程序本身在请求管道的更高版本。

通过结合自定义服务器实现和特定的中间件增强功能，可构造精确的应用程序需要的功能集。 这允许缺少功能而无需在服务器中，更改要添加的可确保仅功能的最少工作量公开，从而限制攻击面区域和提高性能。

## <a name="summary"></a>摘要

功能接口定义给定的请求可能支持的特定 HTTP 功能。 服务器定义的功能，集合和该服务器时，支持的功能的初始集，但中间件可以用于增强这些功能。

## <a name="additional-resources"></a>其他资源

* [服务器](servers/index.md)

* [中间件](middleware.md)

* [.NET 的开放 Web 接口 (OWIN)](owin.md)
