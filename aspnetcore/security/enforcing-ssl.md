---
title: "在 ASP.NET Core 应用程序实施 SSL"
author: rick-anderson
description: "演示如何要求在 ASP.NET Core SSL web 应用"
keywords: "ASP.NET 核心、 SSL、 HTTPS、 RequireHttpsAttribute、 IIS Express"
ms.author: riande
manager: wpickett
ms.date: 07/19/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/enforcing-ssl
ms.openlocfilehash: 6f2755a606000717ca8a57f045b1ef613c7f14f6
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
# <a name="enforcing-ssl-in-an-aspnet-core-app"></a>在 ASP.NET Core 应用程序实施 SSL

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

本文档说明如何：

- 要求将 SSL 用于所有请求 （仅适用于 HTTPS 请求）。
- 所有 HTTP 请求重都定向到 HTTPS。

## <a name="require-ssl"></a>要求 SSL

[RequireHttpsAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.requirehttpsattribute)用于要求 SSL。 你可以修饰控制器或具有此特性的方法，或可以将其应用全局如下所示：

以下代码添加到`ConfigureServices`中`Startup`:

[!code-csharp[Main](authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet2&highlight=4-)]

上面的突出显示的代码需要所有请求都使用`HTTPS`，因此 HTTP 请求将被忽略。 以下突出显示的代码将所有 HTTP 请求重都定向到 HTTPS:

[!code-csharp[Main](authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet_AddRedirectToHttps&highlight=7-)]

请参阅[URL 重写中间件](xref:fundamentals/url-rewriting)有关详细信息。

全局需要 HTTPS (`options.Filters.Add(new RequireHttpsAttribute());`) 是最佳安全方案。 应用`[RequireHttps]`特性应用到所有控制器不被视为尽可能安全全局需要 HTTPS。 你不能保证新控制器添加到你的应用程序将记住应用`[RequireHttps]`属性。
