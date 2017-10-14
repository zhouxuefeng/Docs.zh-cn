---
title: "启用跨源请求 (CORS)"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 05/17/2017
ms.topic: article
ms.assetid: f9d95e88-4d7e-4d0c-a8e1-47de1128d505
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/cors
ms.openlocfilehash: 0d1c34f5c82fe1a28a405617ea77084db899139b
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2017
---
# <a name="enabling-cross-origin-requests-cors"></a>启用跨源请求 (CORS)

通过[Mike Wasson](https://github.com/mikewasson)， [Shayne 贝叶](https://twitter.com/spboyer)，和[Tom Dykstra](https://github.com/tdykstra)

浏览器安全阻止到另一个域进行 AJAX 请求 web 页。 此限制称为*同源策略*，，可防止恶意站点读取另一个站点中的敏感数据。 但是，有时你可能想要允许对你的 web API 进行的跨源请求其他站点。

[跨域资源共享](http://www.w3.org/TR/cors/)(CORS) 是一种 W3C 标准，允许服务器放宽了同源策略。 使用 CORS，服务器可以显式允许某些跨源请求时拒绝其他人。 CORS 是更安全、 更灵活比早期技术如[JSONP](https://wikipedia.org/wiki/JSONP)。 本主题演示如何在 ASP.NET Core 应用程序中启用 CORS。

## <a name="what-is-same-origin"></a>什么是"相同源"？

如果它们具有相同的方案、 主机和端口，两个 Url 将具有相同的原点。 ([RFC 6454](http://tools.ietf.org/html/rfc6454))

这些两个 Url 具有相同的来源：

* `http://example.com/foo.html`

* `http://example.com/bar.html`

这些 Url 有两个比以前的不同来源：

* `http://example.net`-不同的域

* `http://www.example.com/foo.html`的不同子域

* `https://example.com/foo.html`-不同的方案

* `http://example.com:9000/foo.html`-不同的端口

> [!NOTE]
> 比较来源时，Internet Explorer 不考虑端口。

## <a name="setting-up-cors"></a>CORS 设置

若要设置你的应用程序的 CORS 添加`Microsoft.AspNetCore.Cors`包到你的项目。

添加会在 Startup.cs 的 CORS 服务：

[!code-csharp[Main](cors/sample/CorsExample1/Startup.cs?name=snippet_addcors)]

## <a name="enabling-cors-with-middleware"></a>启用 CORS 的中间件

若要启用 CORS 整个应用程序添加 CORS 中间件请求管道使用`UseCors`扩展方法。 请注意 CORS 中间件，必须在任何定义的终结点之前你想要支持跨源请求 （例如应用程序中。 对任何调用之前`UseMvc`)。

添加 CORS 中间件使用时，可以指定跨域策略`CorsPolicyBuilder`类。 有两种方法可以实现此目的。 第一种是使用 lambda 调用 UseCors:

[!code-csharp[Main](cors/sample/CorsExample1/Startup.cs?highlight=11,12&range=22-38)]

**注意：**而无需尾部反斜杠，必须指定的 URL (`/`)。 如果 URL 终止与`/`，比较将返回`false`并且将返回没有标头。

Lambda 采用`CorsPolicyBuilder`对象。 你将找到一份[配置选项](#cors-policy-options)本主题中更高版本。 在此示例中，该策略允许来自的跨域请求`http://example.com`和其他的原点。

请注意 CorsPolicyBuilder 有 fluent API，因此你可以将方法调用的链接：

[!code-csharp[Main](../security/cors/sample/CorsExample3/Startup.cs?highlight=3&range=29-32)]

第二种方法是定义一个或多个命名的 CORS 策略，并在运行时按名称然后选择的策略。

[!code-csharp[Main](cors/sample/CorsExample2/Startup.cs?name=snippet_begin)]

此示例添加名为"AllowSpecificOrigin"的 CORS 策略。 若要选择的策略，将名称传递给`UseCors`。

## <a name="enabling-cors-in-mvc"></a>在 MVC 启用 CORS

MVC 或者可用于应用每个操作，每个控制器，或全局范围内的所有控制器的特定 CORS。 使用 MVC 若要启用 CORS 时使用相同的 CORS 服务，但不是 CORS 中间件。

### <a name="per-action"></a>每个操作

若要指定特定的操作的 CORS 策略添加`[EnableCors]`属性设为该操作。 指定策略名称。

[!code-csharp[Main](cors/sample/CorsMVC/Controllers/ValuesController.cs?name=EnableOnAction)]

### <a name="per-controller"></a>每个控制器

若要指定一个特定的控制器的 CORS 策略添加`[EnableCors]`到控制器类属性。 指定策略名称。

[!code-csharp[Main](cors/sample/CorsMVC/Controllers/ValuesController.cs?name=EnableOnController)]

### <a name="globally"></a>全局

你可以启用 CORS 全局范围内的所有控制器的添加`CorsAuthorizationFilterFactory`到全局筛选器集合的筛选器：

[!code-csharp[Main](cors/sample/CorsMVC/Startup2.cs?name=snippet_configureservices)]

优先顺序是： 操作，控制器，全局。 操作级别的策略优先于控制器级别的策略，并控制器级别策略优先于全局策略。

### <a name="disable-cors"></a>禁用 CORS

若要禁用 CORS 控制器或操作，使用`[DisableCors]`属性。

[!code-csharp[Main](cors/sample/CorsMVC/Controllers/ValuesController.cs?name=DisableOnAction)]

## <a name="cors-policy-options"></a>CORS 策略选项

本部分介绍你可以在 CORS 策略设置的各种选项。

* [设置允许的来源](#set-the-allowed-origins)

* [设置允许的 HTTP 方法](#set-the-allowed-http-methods)

* [设置允许的请求标头](#set-the-allowed-request-headers)

* [设置公开的响应标头](#set-the-exposed-response-headers)

* [在跨域请求中的凭据](#credentials-in-cross-origin-requests)

* [将预检过期时间设置](#set-the-preflight-expiration-time)

对于某些选项可能有有助于读取[如何 CORS 适用](#how-cors-works)第一个。

### <a name="set-the-allowed-origins"></a>设置允许的来源

若要允许一个或多个特定来源：

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=19-23)]

若要允许所有来源：

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs??range=27-31)]

允许从任何源的请求之前应该认真考虑。 这意味着按原义任何网站可 AJAX 调用你的 API。

### <a name="set-the-allowed-http-methods"></a>设置允许的 HTTP 方法

若要允许所有的 HTTP 方法：

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=44-49)]

这会影响预检请求和访问-控制的允许的方法标头。

### <a name="set-the-allowed-request-headers"></a>设置允许的请求标头

CORS 预检请求可能包括一个访问控制的请求标头标头，列出由应用程序设置的 HTTP 标头 (所谓的"创作请求标头")。

到白名单特定的标头：

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=53-58)]

若要允许所有创作请求标头：

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=62-67)]

浏览器中都不完全一致它们如何设置访问控制的请求标头。 如果您设置标头为任何以外"*"，则应包含至少"接受"，"内容类型"，"源"和任何你想要支持的自定义标头。

### <a name="set-the-exposed-response-headers"></a>设置公开的响应标头

默认情况下，浏览器不公开所有向应用程序的响应标头。 (请参阅[http://www.w3.org/TR/cors/#simple-response-header](http://www.w3.org/TR/cors/#simple-response-header)。)默认为可用的响应标头是：

* 缓存控制

* 内容语言

* 内容类型

* 过期

* 上次修改

* 杂注

CORS 规范调用这些*简单响应标头*。 若要使应用程序的其他标头：

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=71-76)]

### <a name="credentials-in-cross-origin-requests"></a>在跨域请求中的凭据

凭据需要 CORS 请求中的特殊处理。 默认情况下，浏览器不发送任何凭据与跨域请求。 凭据包括 cookie 和 HTTP 身份验证方案。 若要发送的跨域请求的凭据，客户端必须设置为 true 的 XMLHttpRequest.withCredentials。

直接使用 XMLHttpRequest:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'http://www.example.com/api/test');
xhr.withCredentials = true;
```

在 jQuery:

```jQuery
$.ajax({
  type: 'get',
  url: 'http://www.example.com/home',
  xhrFields: {
    withCredentials: true
}
```

此外，则服务器必须允许凭据。 若要允许跨域凭据：

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=80-85)]

现在 HTTP 响应将包括一个访问控制的允许的凭据标头，它指示浏览器服务器允许跨域请求凭据。

如果浏览器发送凭据，但响应不包含有效的访问控制的允许的凭证标头，浏览器并不会对应用程序的响应，并且 AJAX 请求失败。

因为这意味着在另一个域网站可以不在用户不知情的情况下向用户的代表应用程序发送登录的用户的凭据，则要非常谨慎允许跨域凭据。 CORS 规范还状态到该设置来源"*"（所有来源） 无效的访问控制的允许的凭证标头是否存在。

### <a name="set-the-preflight-expiration-time"></a>将预检过期时间设置

访问控制的最长时间标头指定可以缓存预检请求的响应的时间就越长。 若要设置此标头：

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=89-94)]

<a name="cors-how-cors-works"></a>

## <a name="how-cors-works"></a>CORS 的工作原理

本部分介绍在 CORS 请求中，在 HTTP 消息的级别会发生什么情况。 请务必了解 CORS 的工作原理，以便你可以正确，配置你的 CORS 策略以及故障排除如果操作不能按预期工作。

CORS 规范引入了几个新的 HTTP 标头启用跨域请求。 如果浏览器支持 CORS，则将自动为跨源请求; 这些标头设置你不必执行任何特殊的 JavaScript 代码中。

下面是跨域请求的示例。 "源"标头提供了正在发出请求的站点的域：

```
GET http://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: http://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: http://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

如果服务器允许该请求，则将设置访问控制的允许的域标头。 此标头的值匹配的 Origin 标头，或是通配符值"*"，允许任何来源的含义。:

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: http://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

如果响应不包含访问控制的允许的域标头，AJAX 请求失败。 具体而言，浏览器不允许该请求。 即使服务器将返回成功的响应，浏览器不进行响应可供客户端应用程序。

### <a name="preflight-requests"></a>预检请求

对于某些 CORS 请求，浏览器发送一个其他的请求，调用"预检请求，"，再将发送实际请求的资源。 如果以下条件为真，浏览器可以跳过预检请求：

* 请求方法是 GET、 HEAD 或 POST、 和

* 应用程序不会设置以外，接受语言内容 Accept-language、 任何请求标头内容类型或最后一个事件 ID 和

* 内容类型标头 (如果设置) 是以下之一：

  * application/x-www-form-urlencoded

  * multipart/窗体的数据

  * 文本/无格式

有关请求标头规则适用于应用程序通过 XMLHttpRequest 对象上调用 setRequestHeader 将设置的标头。 （CORS 规范调用这些"作者请求标头"。）此规则不适用于浏览器可以设置，如用户代理、 主机或内容-长度标头。

下面是预检请求的示例：

```
OPTIONS http://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: http://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

预检请求使用 HTTP OPTIONS 方法。 它包括两个特殊的标头：

* 访问控制的请求的方法： 将用作实际请求 HTTP 方法。

* 访问-控制-请求的标头： 应用程序设置实际请求的请求标头的列表。 （同样，这不包括浏览器设置的标头。）

下面是假定服务器允许请求的示例响应：

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: http://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

响应包括列出了允许的方法的访问-控制的允许的方法标头和 （可选） 访问的控制的允许的标头标头，其中列出了允许的标头。 如果预检请求成功，则浏览器发送实际请求，如前面所述。
