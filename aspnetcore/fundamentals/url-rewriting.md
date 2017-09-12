---
title: "URL 重写在 ASP.NET 核心中的中间件"
author: guardrex
description: "URL 重写并包含有关如何在 ASP.NET Core 应用程序中使用 URL 重写中间件的说明将重定向简介。"
keywords: "ASP.NET 核心 URL 重写，URL 重写，重定向，URL 重定向，中间件，apache_mod URL"
ms.author: riande
manager: wpickett
ms.date: 08/17/2017
ms.topic: article
ms.assetid: e6130638-c410-4161-9921-b658ce988bd1
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/url-rewriting
ms.openlocfilehash: 05a92c4eee6b26e49831c11e1251aedba87ed717
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a>URL 重写在 ASP.NET 核心中的中间件

通过[Luke Latham](https://github.com/GuardRex)和[Mikael Mengistu](https://github.com/mikaelm12)

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)

URL 重写是一种修改的请求 Url 基于一个或多个预定义的规则的行为。 URL 重写创建资源位置和其地址之间的抽象，以便在位置和地址不紧密相关。 有几种方案，其中 URL 重写是有价值：
* 移动或替换暂时或永久性地同时保持稳定的定位符，对这些资源的服务器资源
* 拆分请求处理跨不同的应用程序或跨区域的一个应用程序
* 删除、 添加或重新组织对传入请求的 URL 段
* 优化搜索引擎搜索引擎优化 (SEO) 的公共 Url
* 允许使用友好的公共 Url，以便帮助预测以下链接来找到的内容的用户
* 将不安全来保护终结点的请求重定向
* 阻止映像 hotlinking

你可以定义了更改的 URL 中有多种，包括正则表达式、 Apache mod_rewrite 模块规则、 IIS 重写模块规则和使用自定义规则逻辑的规则。 本文档介绍了 URL 重写包含有关如何在 ASP.NET Core 应用中使用 URL 重写中间件的说明。

> [!NOTE]
> URL 重写时，可以减少应用的性能。 如果可行，您应限制数量和复杂度的规则。

## <a name="url-redirect-and-url-rewrite"></a>URL 重定向和 URL 重写
用词之间的差异*URL 重定向*和*URL 重写*可能看起来细微在第一个，但具有对资源提供给客户端的重要影响。 ASP.NET 核心 URL 重写中间件能够满足他们的两个需要。

A *URL 重定向*是客户端操作，其中将指示客户端访问在另一个地址的资源。 这需要到服务器，一次往返行程和返回到客户端的重定向 URL 显示在浏览器的地址栏中，当客户端对资源的新请求。 如果`/resource`是*重定向*到`/different-resource`，客户端请求`/resource`，服务器做出响应客户端应获取处的资源`/different-resource`与一个状态代码，指示重定向临时或永久。 客户端来执行重定向 URL 处的资源的新请求。

![WebAPI 服务终结点已被暂时更改从版本 1 (v1) 到服务器上的版本 2 (v2)。 客户端向版本 1 路径 /v1/api 的服务发出请求。 服务器发送回的 302 （发现） 响应服务的新的临时路径在版本 2 /v2/api。 客户端发出到重定向 URL 上的服务的第二个请求。 服务器响应 200 （正常） 状态代码。](url-rewriting/_static/url_redirect.png)

将请求重定向到另一个 URL，你可以指示重定向是永久或临时。 资源具有新的永久性 URL，其中你想使用 301 （永久移动） 状态代码，以指示客户端资源的所有将来的请求，应使用新的 URL。 *收到一个 301 状态代码时，客户端可能缓存响应。* 302 （找到） 状态代码使用其中重定向是临时或通常使用者若要更改，以便客户端不应存储并在将来重复使用重定向 URL。 有关详细信息，请参阅[RFC 2616： 状态代码定义](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。

A *URL 重写*是服务器端操作，以提供来自不同资源地址的资源。 重写 URL，则不需要保存/还原到服务器。 重写的 URL 不返回到客户端，不会显示在浏览器的地址栏中。 当`/resource`是*重写*到`/different-resource`，客户端请求`/resource`，和服务器*内部*提取处的资源`/different-resource`。 尽管客户端可能能够检索重写 URL 处的资源，则不会通知客户端在重写 URL 存在相应的资源时它将建立其请求并接收响应。

![WebAPI 服务终结点已从版本 1 (v1) 更改为服务器上的版本 2 (v2)。 客户端向版本 1 路径 /v1/api 的服务发出请求。 请求 URL 会被重写，来访问在版本 2 路径 /v2/api 服务。 在服务响应 200 （正常） 状态代码为客户端。](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a>URL 重写示例应用程序
你可以浏览 URL 重写中间件中使用的功能[URL 重写示例应用程序](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)。 应用适用重写和重定向规则，并显示重写或重定向 URL。

## <a name="when-to-use-url-rewriting-middleware"></a>何时使用 URL 重写中间件
当你无法使用时，使用 URL 重写中间件[URL 重写模块](https://www.iis.net/downloads/microsoft/url-rewrite)与 Windows Server 上的 IIS [Apache mod_rewrite 模块](https://httpd.apache.org/docs/2.4/rewrite/)Apache 服务器上[URL 重写上 Nginx](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)，或你的应用程序承载于[HTTP.sys 服务器](xref:fundamentals/servers/httpsys)(以前称为[WebListener](xref:fundamentals/servers/weblistener))。 使用基于服务器的 URL 重写中的技术 IIS、 Apache 或 Nginx 的主要原因是，中间件不支持这些模块的完整功能，但的中间件的性能可能不会与匹配的模块。 但是，有不适用于 ASP.NET 核心项目，如服务器模块的某些功能`IsFile`和`IsDirectory`IIS 重写模块的约束。 在这些情况下，改为使用该中间件。

## <a name="package"></a>Package
若要在你的项目中包含该中间件，添加到引用[ `Microsoft.AspNetCore.Rewrite` ](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite/)包。 此功能是可用于应用程序面向 ASP.NET 核心 1.1 或更高版本。

## <a name="extension-and-options"></a>扩展和选项
建立 URL 重写，并将规则重定向通过创建的实例`RewriteOptions`类使用的每个规则的扩展方法。 链接你想要处理它们的顺序中的多个规则。 `RewriteOptions`如将其添加到请求管道与传递到该 URL 重写中间件`app.UseRewriter(options);`。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1)]

---

### <a name="url-redirect"></a>URL 重定向
使用`AddRedirect`将请求重定向。 第一个参数包含为匹配的路径中的传入 URL 你正则表达式。 第二个参数是替换字符串。 第三个参数，如果存在，则指定的状态代码。 如果未指定的状态代码，则默认为 302 （找到），指示该资源暂时移动或替换。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1&highlight=5)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1&highlight=2)]

---

在开发人员工具启用浏览器，对具有路径示例应用程序发出请求`/redirect-rule/1234/5678`。 正则表达式匹配的请求路径上`redirect-rule/(.*)`，而路径被替换`/redirected/1234/5678`。 重定向 URL 发送回客户端与 302 （找到） 状态代码。 浏览器在浏览器的地址栏中的重定向 URL 发出新请求。 由于重定向 URL 上与不匹配的示例应用中的任何规则，第二次请求从应用程序接收响应 200 （正常） 和响应的正文显示重定向 URL。 URL 时，向服务器发出一次往返*重定向*。

> [!WARNING]
> 建立你重定向规则时务必小心。 对应用程序中，包括重定向后的每个请求，重定向规则进行评估。 很容易意外创建的无限重定向循环。

原始请求：`/redirect-rule/1234/5678`

![使用跟踪的请求和响应的开发人员工具的浏览器窗口](url-rewriting/_static/add_redirect.png)

包含在括号内的表达式的一部分被称作*捕获组*。 点 (`.`) 的表达式意味着*匹配任何字符*。 星号 (`*`) 指示*零次或多次匹配前面的字符*。 因此的 url 的最后两个路径段`1234/5678`，由捕获组捕获`(.*)`。 在请求 URL 后提供任何值`redirect-rule/`此单个捕获组捕获。

替换字符串中捕获的组注入到美元符号的字符串 (`$`) 跟捕获的序列号。 第一个捕获组值一起被获取`$1`、 与第二个`$2`，并将不断序列中你正则表达式的捕获组中。 没有只有一个捕获的组中的重定向规则正则表达式在示例应用中，因此替换字符串，这是中只有一个插入的组`$1`。 应用规则时，URL 将变为`/redirected/1234/5678`。

<a name=url-redirect-to-secure-endpoint></a>
### <a name="url-redirect-to-a-secure-endpoint"></a>URL 重定向到安全的终结点
使用`AddRedirectToHttps`将 HTTP 请求重定向到相同的主机，并使用 HTTPS 的路径 (`https://`)。 如果未提供的状态代码，该中间件将默认为 302 （找到）。 如果未提供端口，该中间件将默认为`null`，这意味着协议更改为`https://`和客户端访问端口 443 上的资源。 示例演示如何将状态代码设置为 301 （永久移动） 并将端口更改为 5001。
```csharp
var options = new RewriteOptions()
    .AddRedirectToHttps(301, 5001);

app.UseRewriter(options);
```
使用`AddRedirectToHttpsPermanent`将不安全的请求重定向到相同的主机，并使用安全 HTTPS 协议的路径 (`https://`端口 443 上)。 该中间件将状态代码设置为 301 （永久移动）。

示例应用程序均可演示如何使用`AddRedirectToHttps`或`AddRedirectToHttpsPermanent`。 添加扩展方法`RewriteOptions`。 对任何 URL 时的应用进行的不安全的请求。 关闭浏览器安全警告自签名的证书不受信任。

使用原始请求`AddRedirectToHttps(301, 5001)`:`/secure`

![使用跟踪的请求和响应的开发人员工具的浏览器窗口](url-rewriting/_static/add_redirect_to_https.png)

使用原始请求`AddRedirectToHttpsPermanent`:`/secure`

![使用跟踪的请求和响应的开发人员工具的浏览器窗口](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a>URL 重写
使用`AddRewrite`创建用于 Url 重写规则。 第一个参数将包含有关在传入的 URL 路径匹配你正则表达式。 第二个参数是替换字符串。 第三个参数， `skipRemainingRules: {true|false}`，是否要跳过其他重写规则，如果在应用的当前规则指示该中间件。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1&highlight=6)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1&highlight=3)]

---

原始请求：`/rewrite-rule/1234/5678`

![与跟踪请求和响应的开发人员工具的浏览器窗口](url-rewriting/_static/add_rewrite.png)

你注意到正则表达式中的第一件事是插入 (`^`) 开头的表达式。 这意味着，匹配的 URL 路径的开头开始。

重定向规则，与前面的示例中`redirect-rule/(.*)`，正则表达式的开头没有任何插入; 因此，任何字符可能优先于`redirect-rule/`中成功匹配的路径。

| 路径                               | 匹配 |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | 是   |
| `/my-cool-redirect-rule/1234/5678` | 是   |
| `/anotherredirect-rule/1234/5678`  | 是   |

重写规则， `^rewrite-rule/(\d+)/(\d+)`，仅在启动时出现匹配路径`rewrite-rule/`。 请注意匹配重写规则下面和上面的重定向规则之间的差异。

| 路径                              | 匹配 |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | 是   |
| `/my-cool-rewrite-rule/1234/5678` | No    |
| `/anotherrewrite-rule/1234/5678`  | No    |

以下`^rewrite-rule/`部分的表达式中，有两个捕获组， `(\d+)/(\d+)`。 `\d`表示*匹配数字 （数字）*。 加号 (`+`) 意味着*匹配一个或多个前面的字符*。 因此，该 URL 必须包含大量跟正斜杠跟另一个数字。 组被注入到重写 URL 作为这些捕获`$1`和`$2`。 重写规则替换字符串将放在查询字符串的捕获的组。 所请求的路径的`/rewrite-rule/1234/5678`重新编写，以便获取处的资源`/rewritten?var1=1234&var2=5678`。 如果出现在原始请求查询字符串，它被保留在 URL 重写时。

不没有到服务器以获取资源的任何往返。 如果该资源已存在，它提取并返回到客户端与为 200 （正常） 的状态代码。 因为客户端不重定向，则不会更改浏览器地址栏中的 URL。 就关心的是客户端，该 URL 重写操作永远不会出现。

> [!NOTE]
> 使用`skipRemainingRules: true`只要有可能，因为匹配规则是代价高昂的过程并减少应用程序响应时间。 最快的应用程序响应：
> * 订购到最不常匹配规则从最常匹配规则重写规则。
> * 在出现匹配项和所需的任何其他规则处理时，跳过剩余的规则进行处理。

### <a name="apache-modrewrite"></a>Apache mod_rewrite
应用与 Apache mod_rewrite 规则`AddApacheModRewrite`。 请确保规则将文件部署的应用程序。 有关详细信息和 mod_rewrite 规则的示例，请参阅[Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

A`StreamReader`用于读取从规则*ApacheModRewrite.txt*规则文件。

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1&highlight=1,7)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

第一个参数的取值应`IFileProvider`，这可通过提供[依赖关系注入](dependency-injection.md)。 `IHostingEnvironment`注入提供`ContentRootFileProvider`。 第二个参数是你的规则文件，即路径*ApacheModRewrite.txt*示例应用中。

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1&highlight=4)]

---

示例应用程序将从请求重定向`/apache-mod-rules-redirect/(.\*)`到`/redirected?id=$1`。 响应状态代码为 302 （找到）。

[!code[Main](url-rewriting/samples/2.x/ApacheModRewrite.txt)]

原始请求：`/apache-mod-rules-redirect/1234`

![使用跟踪的请求和响应的开发人员工具的浏览器窗口](url-rewriting/_static/add_apache_mod_redirect.png)

##### <a name="supported-server-variables"></a>支持的服务器变量
该中间件支持以下 Apache mod_rewrite 服务器变量：
* CONN_REMOTE_ADDR
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_FORWARDED
* HTTP_HOST
* HTTP_REFERER
* HTTP_USER_AGENT
* HTTPS
* IPV6
* QUERY_STRING
* REMOTE_ADDR
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_METHOD
* REQUEST_SCHEME
* REQUEST_URI
* SCRIPT_FILENAME
* SERVER_ADDR
* SERVER_PORT
* SERVER_PROTOCOL
* 时间
* TIME_DAY
* TIME_HOUR
* TIME_MIN
* TIME_MON
* TIME_SEC
* TIME_WDAY
* TIME_YEAR

### <a name="iis-url-rewrite-module-rules"></a>IIS URL 重写模块规则
若要使用适用于 IIS URL 重写模块的规则，请使用`AddIISUrlRewrite`。 请确保规则将文件部署的应用程序。 不直接使用的中间件你*web.config*文件在 Windows Server IIS 上运行时。 使用 IIS，这些规则应存储之外你*web.config*以避免与 IIS 重写模块冲突。 有关详细信息和 IIS URL 重写模块规则的示例，请参阅[使用 Url 重写模块 2.0](https://docs.microsoft.com/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)和[URL 重写模块配置引用](https://docs.microsoft.com/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

A`StreamReader`用于读取从规则*IISUrlRewrite.xml*规则文件。

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1&highlight=2,8)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

第一个参数的取值应`IFileProvider`，而第二个参数是将 XML 规则文件，即路径*IISUrlRewrite.xml*示例应用中。

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1&highlight=5)]

---

示例应用程序重写来自请求`/iis-rules-rewrite/(.*)`到`/rewritten?id=$1`。 将响应发送到客户端 200 （正常） 状态代码。

[!code-xml[Main](url-rewriting/samples/2.x/IISUrlRewrite.xml)]

原始请求：`/iis-rules-rewrite/1234`

![与跟踪请求和响应的开发人员工具的浏览器窗口](url-rewriting/_static/add_iis_url_rewrite.png)

如果你使用的服务器级规则配置会以意外方式影响你的应用程序有一个 active IIS 重写模块，你可以禁用 IIS 重写模块，以使应用程序。 有关详细信息，请参阅[禁用 IIS 模块](xref:hosting/iis-modules#disabling-iis-modules)。

#### <a name="unsupported-features"></a>不支持的功能

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

发布的中间件与 ASP.NET 核心 2.x 不支持以下 IIS URL 重写模块功能：
* 出站规则
* 自定义服务器变量
* 通配符
* LogRewrittenUrl

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

发布的中间件与 ASP.NET 核心 1.x 不支持以下 IIS URL 重写模块功能：
* 全局规则
* 出站规则
* 重写映射
* CustomResponse 操作
* 自定义服务器变量
* 通配符
* 操作： CustomResponse
* LogRewrittenUrl

---

#### <a name="supported-server-variables"></a>支持的服务器变量
该中间件支持以下 IIS URL 重写模块服务器变量：
* CONTENT_LENGTH
* P E
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_HOST
* HTTP_REFERER
* HTTP_URL
* HTTP_USER_AGENT
* HTTPS
* LOCAL_ADDR
* QUERY_STRING
* REMOTE_ADDR
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_URI

> [!NOTE]
> 你还可以获取`IFileProvider`通过`PhysicalFileProvider`。 此方法可能会提供更大的灵活性，你重写的位置规则文件。 请确保你重写规则文件部署到你提供的路径上的服务器。
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a>基于方法的规则
使用`Add(Action<RewriteContext> applyRule)`实现您自己的规则逻辑的方法中。 `RewriteContext`公开`HttpContext`在你的方法中使用。 `context.Result`确定其他管道处理。

| 上下文。结果                       | 操作                                                          |
| ------------------------------------ | --------------------------------------------------------------- |
| `RuleResult.ContinueRules`（默认值） | 继续应用规则                                         |
| `RuleResult.EndResponse`             | 停止应用规则和发送响应                       |
| `RuleResult.SkipRemainingRules`      | 停止应用规则并将上下文发送到下一步的中间件 |

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1&highlight=9)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1&highlight=6)]

---

示例应用演示的方法，将路径的请求重定向结束的*.xml*。 如果发出请求`/file.xml`，重定向到`/xmlfiles/file.xml`。 状态代码设置为 301 （永久移动）。 有关重定向，你必须显式设置响应; 的状态代码否则为返回 200 （正常） 状态代码和重定向不会发生客户端上。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/RewriteRules.cs?name=snippet1)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet2)]

---

原始请求：`/file.xml`

![使用跟踪的请求和响应，则为 file.xml 的开发人员工具的浏览器窗口](url-rewriting/_static/add_redirect_xml_requests.png)

### <a name="irule-based-rule"></a>基于 IRule 的规则
使用`Add(IRule)`派生自的类中实现您自己的规则逻辑`IRule`。 使用`IRule`基础上使用基于方法的规则方法提供了更大的灵活性。 在派生的类可能包括构造函数，其中你可以传入的参数`ApplyRule`方法。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1&highlight=10-11)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1&highlight=7-8)]

---

示例应用程序中的参数的值`extension`和`newPath`会检查，以满足几个条件。 `extension`必须包含一个值，和的值必须为*.png*， *.jpg*，或*.gif*。 如果`newPath`无效，`ArgumentException`引发。 如果发出请求*image.png*，重定向到`/png-images/image.png`。 如果发出请求*image.jpg*，重定向到`/jpg-images/image.jpg`。 状态代码设置为 301 （永久移动），和`context.Result`设置为停止处理规则和发送响应。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/RewriteRules.cs?name=snippet2)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/RewriteRule.cs?name=snippet1)]

---

原始请求：`/image.png`

![使用跟踪的请求和响应，则为 image.png 的开发人员工具的浏览器窗口](url-rewriting/_static/add_redirect_png_requests.png)

原始请求：`/image.jpg`

![使用跟踪的请求和响应，则为 image.jpg 的开发人员工具的浏览器窗口](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a>正则表达式示例

| 目标 | 正则表达式字符串 （& a)<br>匹配示例 | 替换字符串 （& a)<br>输出示例 |
| ---- | :-----------------------------: | :------------------------------------: |
| 重写到查询字符串的路径 | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| 条带尾部反斜杠 | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| 强制实施尾部反斜杠 | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| 避免重写特定的请求 | `(.*[^(\.axd)])$`<br>是的：`/resource.htm`<br>不：`/resource.axd` | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| 重新排列 URL 段 | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| 将 URL 段 | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

## <a name="additional-resources"></a>其他资源
* [应用程序启动](startup.md)
* [中间件](middleware.md)
* [.NET 中的正则表达式](/dotnet/articles/standard/base-types/regular-expressions)
* [正则表达式语言 - 快速参考](/dotnet/articles/standard/base-types/quick-ref)
* [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)
* [用于 Url 重写模块 2.0 （IIS)](https://docs.microsoft.com/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [URL 重写模块配置参考](https://docs.microsoft.com/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [IIS URL 重写模块论坛](https://forums.iis.net/1152.aspx)
* [保持简单 URL 结构](https://support.google.com/webmasters/answer/76329?hl=en)
* [10 URL 重写提示和技巧](http://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [以正斜杠或不以斜杠](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)
