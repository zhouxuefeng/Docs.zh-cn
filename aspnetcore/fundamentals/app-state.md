---
title: "在 ASP.NET 核心中的会话和应用程序状态"
author: rick-anderson
description: "保留应用程序和用户 （会话） 状态请求之间的方法。"
keywords: "ASP.NET 核心，应用程序状态、 会话状态、 查询字符串，发布"
ms.author: riande
manager: wpickett
ms.date: 10/08/2017
ms.topic: article
ms.assetid: 18cda488-0769-4cb9-82f6-4c6685f2045d
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/app-state
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d4d10ef45d562f34c3f8b5ce025abaf763c862d3
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2017
---
# <a name="introduction-to-session-and-application-state-in-aspnet-core"></a><span data-ttu-id="2ed00-104">简介中 ASP.NET Core 的会话和应用程序状态</span><span class="sxs-lookup"><span data-stu-id="2ed00-104">Introduction to session and application state in ASP.NET Core</span></span>

<span data-ttu-id="2ed00-105">通过[Rick Anderson](https://twitter.com/RickAndMSFT)， [Steve Smith](https://ardalis.com/)，和[Diana LaRose](https://github.com/DianaLaRose)</span><span class="sxs-lookup"><span data-stu-id="2ed00-105">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), and [Diana LaRose](https://github.com/DianaLaRose)</span></span>

<span data-ttu-id="2ed00-106">HTTP 是无状态的协议。</span><span class="sxs-lookup"><span data-stu-id="2ed00-106">HTTP is a stateless protocol.</span></span> <span data-ttu-id="2ed00-107">Web 服务器将每个 HTTP 请求作为独立的请求，并不会保留用户从以前的请求的值。</span><span class="sxs-lookup"><span data-stu-id="2ed00-107">A web server treats each HTTP request as an independent request and does not retain user values from previous requests.</span></span> <span data-ttu-id="2ed00-108">本文讨论保留应用程序和请求之间的会话状态的不同方式。</span><span class="sxs-lookup"><span data-stu-id="2ed00-108">This article discusses different ways to preserve application and session state between requests.</span></span> 

## <a name="session-state"></a><span data-ttu-id="2ed00-109">会话状态</span><span class="sxs-lookup"><span data-stu-id="2ed00-109">Session state</span></span>

<span data-ttu-id="2ed00-110">会话状态是 ASP.NET Core 中的一项功能，可用于在用户浏览 Web 应用时保存和存储用户数据。</span><span class="sxs-lookup"><span data-stu-id="2ed00-110">Session state is a feature in ASP.NET Core that you can use to save and store user data while the user browses your web app.</span></span> <span data-ttu-id="2ed00-111">包含服务器上的字典或哈希表，会话状态保持跨从浏览器的请求数据。</span><span class="sxs-lookup"><span data-stu-id="2ed00-111">Consisting of a dictionary or hash table on the server, session state persists data across requests from a browser.</span></span> <span data-ttu-id="2ed00-112">缓存支持会话数据。</span><span class="sxs-lookup"><span data-stu-id="2ed00-112">The session data is backed by a cache.</span></span>

<span data-ttu-id="2ed00-113">ASP.NET 核心通过提供包含会话 ID，它使用每个请求向服务器发送的 cookie 的客户端维护会话状态。</span><span class="sxs-lookup"><span data-stu-id="2ed00-113">ASP.NET Core maintains session state by giving the client a cookie that contains the session ID, which is sent to the server with each request.</span></span> <span data-ttu-id="2ed00-114">服务器使用的会话 ID 来获取会话数据。</span><span class="sxs-lookup"><span data-stu-id="2ed00-114">The server uses the session ID to fetch the session data.</span></span> <span data-ttu-id="2ed00-115">因为会话 cookie 是特定于浏览器，你不能在浏览器中共享会话。</span><span class="sxs-lookup"><span data-stu-id="2ed00-115">Because the session cookie is specific to the browser, you cannot share sessions across browsers.</span></span> <span data-ttu-id="2ed00-116">仅当浏览器会话结束时，将删除会话 cookie。</span><span class="sxs-lookup"><span data-stu-id="2ed00-116">Session cookies are deleted only when the browser session ends.</span></span> <span data-ttu-id="2ed00-117">如果收到过期的会话 cookie，创建使用相同的会话 cookie 的新会话。</span><span class="sxs-lookup"><span data-stu-id="2ed00-117">If a cookie is received for an expired session, a new session that uses the same session cookie  is created.</span></span> 

<span data-ttu-id="2ed00-118">服务器将保留上次请求后的有限时间的会话。</span><span class="sxs-lookup"><span data-stu-id="2ed00-118">The server retains a session for a limited time after the last request.</span></span> <span data-ttu-id="2ed00-119">你可以将会话超时设置，或使用 20 分钟的默认值。</span><span class="sxs-lookup"><span data-stu-id="2ed00-119">You can either set the session timeout or use the default value of 20 minutes.</span></span> <span data-ttu-id="2ed00-120">会话状态非常适合用于存储用户数据的特定于特定会话，但并不需要永久保留。</span><span class="sxs-lookup"><span data-stu-id="2ed00-120">Session state is ideal for storing user data that is specific to a particular session but doesn’t need to be persisted permanently.</span></span> <span data-ttu-id="2ed00-121">数据从存储中删除后备或者当您调用`Session.Clear`或会话数据存储中存储的到期时。</span><span class="sxs-lookup"><span data-stu-id="2ed00-121">Data is deleted from the backing store either when you call `Session.Clear` or when the session expires in the data store.</span></span> <span data-ttu-id="2ed00-122">关闭浏览器时，或删除会话 cookie 时，服务器不知道。</span><span class="sxs-lookup"><span data-stu-id="2ed00-122">The server does not know when the browser is closed or when the session cookie is deleted.</span></span>

> [!WARNING]
> <span data-ttu-id="2ed00-123">不要在会话中存储敏感数据。</span><span class="sxs-lookup"><span data-stu-id="2ed00-123">Do not store sensitive data in session.</span></span> <span data-ttu-id="2ed00-124">客户端可能会不关闭浏览器并清除会话 cookie （和某些浏览器保持会话 cookie 存在跨 windows）。</span><span class="sxs-lookup"><span data-stu-id="2ed00-124">The client might not close the browser and clear the session cookie (and some browsers keep session cookies alive across windows).</span></span> <span data-ttu-id="2ed00-125">另外，会话可能不是限制为单个用户;下一步的用户可能会继续与同一会话中。</span><span class="sxs-lookup"><span data-stu-id="2ed00-125">Also, a session might not be restricted to a single user; the next user might continue with the same session.</span></span>

<span data-ttu-id="2ed00-126">内存中的会话提供程序将会话数据存储在本地服务器上。</span><span class="sxs-lookup"><span data-stu-id="2ed00-126">The in-memory session provider stores session data on the local server.</span></span> <span data-ttu-id="2ed00-127">如果你计划在服务器场上运行你的 web 应用，你必须使用粘性会话将特定服务器的每个会话进行连接。</span><span class="sxs-lookup"><span data-stu-id="2ed00-127">If you plan to run your web app on a server farm, you must use sticky sessions to tie each session to a specific server.</span></span> <span data-ttu-id="2ed00-128">Windows Azure 网站平台默认为粘性会话应用程序请求路由 （ARR）。</span><span class="sxs-lookup"><span data-stu-id="2ed00-128">The Windows Azure Web Sites platform defaults to sticky sessions (Application Request Routing or ARR).</span></span> <span data-ttu-id="2ed00-129">但是，粘性会话可以影响可伸缩性，并使 web 应用程序更新变得复杂。</span><span class="sxs-lookup"><span data-stu-id="2ed00-129">However, sticky sessions can affect scalability and complicate web app updates.</span></span> <span data-ttu-id="2ed00-130">更好的选择是使用 Redis 或 SQL Server 分布式缓存，这不需要粘性会话。</span><span class="sxs-lookup"><span data-stu-id="2ed00-130">A better option is to use the Redis or SQL Server distributed caches, which don't require sticky sessions.</span></span> <span data-ttu-id="2ed00-131">有关详细信息，请参阅[使用分布式缓存](xref:performance/caching/distributed)。</span><span class="sxs-lookup"><span data-stu-id="2ed00-131">For more information, see [Working with a Distributed Cache](xref:performance/caching/distributed).</span></span> <span data-ttu-id="2ed00-132">有关服务提供商设置的详细信息，请参阅[配置会话](#configuring-session)本文后续部分中。</span><span class="sxs-lookup"><span data-stu-id="2ed00-132">For details on setting up service providers, see [Configuring Session](#configuring-session) later in this article.</span></span>


<a name="temp"></a>
## <a name="tempdata"></a><span data-ttu-id="2ed00-133">TempData</span><span class="sxs-lookup"><span data-stu-id="2ed00-133">TempData</span></span>

<span data-ttu-id="2ed00-134">ASP.NET 核心 MVC 公开[TempData](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData)属性[控制器](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.controller?view=aspnetcore-2.0)。</span><span class="sxs-lookup"><span data-stu-id="2ed00-134">ASP.NET Core MVC exposes the [TempData](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.controller?view=aspnetcore-2.0).</span></span> <span data-ttu-id="2ed00-135">此属性可存储数据，直至数据被读取。</span><span class="sxs-lookup"><span data-stu-id="2ed00-135">This property stores data until it is read.</span></span> <span data-ttu-id="2ed00-136">`Keep` 和 `Peek` 方法可用于检查数据，而不执行删除。</span><span class="sxs-lookup"><span data-stu-id="2ed00-136">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="2ed00-137">`TempData`当超过单个请求所需要的数据，则很适合用于重定向。</span><span class="sxs-lookup"><span data-stu-id="2ed00-137">`TempData` is particularly useful for redirection, when data is needed for more than a single request.</span></span> <span data-ttu-id="2ed00-138">`TempData`是提供程序实现 TempData，例如，使用 cookie 或会话状态。</span><span class="sxs-lookup"><span data-stu-id="2ed00-138">`TempData` is implemented by TempData providers, for example, using either cookies or session state.</span></span>

<a name="tempdata-providers"></a>
### <a name="tempdata-providers"></a><span data-ttu-id="2ed00-139">TempData 提供程序</span><span class="sxs-lookup"><span data-stu-id="2ed00-139">TempData providers</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="2ed00-140">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="2ed00-140">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="2ed00-141">ASP.NET 核心 2.0 及更高版本，基于 cookie 的 TempData 提供程序使用默认情况下在 cookie 中存储 TempData。</span><span class="sxs-lookup"><span data-stu-id="2ed00-141">In ASP.NET Core 2.0 and later, the cookie-based TempData provider is used by default to store TempData in cookies.</span></span>

<span data-ttu-id="2ed00-142">使用编码的 cookie 数据[Base64UrlTextEncoder](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.webutilities.base64urltextencoder?view=aspnetcore-2.0)。</span><span class="sxs-lookup"><span data-stu-id="2ed00-142">The cookie data is encoded with the [Base64UrlTextEncoder](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.webutilities.base64urltextencoder?view=aspnetcore-2.0).</span></span> <span data-ttu-id="2ed00-143">因为 cookie 被加密，并分块，一个 cookie 大小在 1.x 不适用于 ASP.NET Core 中找到的限制。</span><span class="sxs-lookup"><span data-stu-id="2ed00-143">Because the cookie is encrypted and chunked, the single cookie size limit found in ASP.NET Core 1.x does not apply.</span></span> <span data-ttu-id="2ed00-144">因为压缩 encryped 数据会导致安全问题如未压缩的 cookie 数据[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))和[违反](https://wikipedia.org/wiki/BREACH_(security_exploit))攻击。</span><span class="sxs-lookup"><span data-stu-id="2ed00-144">The cookie data is not compressed because compressing encryped data can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span> <span data-ttu-id="2ed00-145">基于 cookie 的 TempData 提供程序的详细信息，请参阅[CookieTempDataProvider](https://github.com/aspnet/Mvc/blob/dev/src/Microsoft.AspNetCore.Mvc.ViewFeatures/ViewFeatures/CookieTempDataProvider.cs)。</span><span class="sxs-lookup"><span data-stu-id="2ed00-145">For more information on the cookie-based TempData provider, see [CookieTempDataProvider](https://github.com/aspnet/Mvc/blob/dev/src/Microsoft.AspNetCore.Mvc.ViewFeatures/ViewFeatures/CookieTempDataProvider.cs).</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="2ed00-146">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="2ed00-146">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="2ed00-147">在 ASP.NET Core 1.0 和 1.1 中，会话状态 TempData 提供程序是默认值。</span><span class="sxs-lookup"><span data-stu-id="2ed00-147">In ASP.NET Core 1.0 and 1.1, the session state TempData provider is the default.</span></span>

--------------

### <a name="choosing-a-tempdata-provider"></a><span data-ttu-id="2ed00-148">选择 TempData 提供程序</span><span class="sxs-lookup"><span data-stu-id="2ed00-148">Choosing a TempData provider</span></span>

<span data-ttu-id="2ed00-149">例如，选择 TempData 提供程序涉及几个注意事项：</span><span class="sxs-lookup"><span data-stu-id="2ed00-149">Choosing a TempData provider involves several considerations, such as:</span></span>

1. <span data-ttu-id="2ed00-150">应用程序已是否用于其他目的使用会话状态？</span><span class="sxs-lookup"><span data-stu-id="2ed00-150">Does the application already use session state for other purposes?</span></span> <span data-ttu-id="2ed00-151">如果是这样，使用会话状态 TempData 提供程序具有不到 （除了外的数据大小） 应用程序会增加成本。</span><span class="sxs-lookup"><span data-stu-id="2ed00-151">If so, using the session state TempData provider has no additional cost to the application (aside from the size of the data).</span></span>
2. <span data-ttu-id="2ed00-152">没有应用程序使用 TempData 仅谨慎相对较少的数据 （最多 500 个字节）？</span><span class="sxs-lookup"><span data-stu-id="2ed00-152">Does the application use TempData only sparingly, for relatively small amounts of data (up to 500 bytes)?</span></span> <span data-ttu-id="2ed00-153">如果因此，cookie TempData 提供程序将添加到执行 TempData 每个请求的较小的成本。</span><span class="sxs-lookup"><span data-stu-id="2ed00-153">If so, the cookie TempData provider will add a small cost to each request that carries TempData.</span></span> <span data-ttu-id="2ed00-154">如果没有，会话状态 TempData 提供程序将是有益避免往返大量的数据在每个请求，直到使用 TempData。</span><span class="sxs-lookup"><span data-stu-id="2ed00-154">If not, the session state TempData provider can be beneficial to avoid round-tripping a large amount of data in each request until the TempData is consumed.</span></span>
3. <span data-ttu-id="2ed00-155">在 web 场 （多个服务器） 可以运行应用程序？</span><span class="sxs-lookup"><span data-stu-id="2ed00-155">Does the application run in a web farm (multiple servers)?</span></span> <span data-ttu-id="2ed00-156">如果这样，则无需为使用 cookie TempData 提供程序的其他配置。</span><span class="sxs-lookup"><span data-stu-id="2ed00-156">If so, there is no additional configuration needed to use the cookie TempData provider.</span></span>

> [!NOTE]
> <span data-ttu-id="2ed00-157">大多数 web 客户端 （例如 web 浏览器） 强制执行的每个 cookie 和 / 或 cookie，总数的最大大小限制。</span><span class="sxs-lookup"><span data-stu-id="2ed00-157">Most web clients (such as web browsers) enforce limits on the maximum size of each cookie, the total number of cookies, or both.</span></span> <span data-ttu-id="2ed00-158">因此，在使用 cookie TempData 提供程序，请验证应用程序不会超过这些限制。</span><span class="sxs-lookup"><span data-stu-id="2ed00-158">Therefore, when using the cookie TempData provider, verify the app won't exceed these limits.</span></span> <span data-ttu-id="2ed00-159">请考虑算加密的开销和分块的数据的总大小。</span><span class="sxs-lookup"><span data-stu-id="2ed00-159">Consider the total size of the data, accounting for the overheads of encryption and chunking.</span></span>

<span data-ttu-id="2ed00-160">若要配置的 TempData 提供程序应用程序，注册中的 TempData 提供程序实现`ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="2ed00-160">To configure the TempData provider for an application, register a TempData provider implementation in `ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services
        .AddMvc()
        .AddSessionStateTempDataProvider();

    // The Session State TempData Provider requires adding the session state service
    services.AddSession();
}
```

## <a name="query-strings"></a><span data-ttu-id="2ed00-161">查询字符串</span><span class="sxs-lookup"><span data-stu-id="2ed00-161">Query strings</span></span>

<span data-ttu-id="2ed00-162">你可以为到另一个从一个请求将其添加到新请求的查询字符串传递数据的量有限。</span><span class="sxs-lookup"><span data-stu-id="2ed00-162">You can pass a limited amount of data from one request to another by adding it to the new request’s query string.</span></span> <span data-ttu-id="2ed00-163">这可用于以允许具有嵌入的状态，以通过电子邮件或社交网络共享的链接的持久方式捕获状态。</span><span class="sxs-lookup"><span data-stu-id="2ed00-163">This is useful for capturing state in a persistent manner that allows links with embedded state to be shared through email or social networks.</span></span> <span data-ttu-id="2ed00-164">但是，为此，你应永远不会使用查询字符串的敏感数据。</span><span class="sxs-lookup"><span data-stu-id="2ed00-164">However, for this reason,  you should never use query strings for sensitive data.</span></span> <span data-ttu-id="2ed00-165">除了轻松地共享，包括查询字符串中的数据可以创建的机会[跨站点请求伪造 (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF))攻击，可以欺骗用户访问恶意网站时进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="2ed00-165">In addition to being easily shared, including data in query strings can create opportunities for [Cross-Site Request Forgery (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) attacks, which can trick users into visiting malicious sites while authenticated.</span></span> <span data-ttu-id="2ed00-166">然后，攻击者可能窃取用户从应用程序的数据，或需要代表用户恶意操作。</span><span class="sxs-lookup"><span data-stu-id="2ed00-166">Attackers can then steal user data from your app or take malicious actions on behalf of the user.</span></span> <span data-ttu-id="2ed00-167">任何保留的应用程序或会话状态必须防止 CSRF 攻击。</span><span class="sxs-lookup"><span data-stu-id="2ed00-167">Any preserved application or session state must protect against CSRF attacks.</span></span> <span data-ttu-id="2ed00-168">CSRF 攻击的详细信息，请参阅[防止跨站点请求伪造 (XSRF/CSRF) 攻击中 ASP.NET Core](../security/anti-request-forgery.md)。</span><span class="sxs-lookup"><span data-stu-id="2ed00-168">For more information on CSRF attacks, see [Preventing Cross-Site Request Forgery (XSRF/CSRF) Attacks in ASP.NET Core](../security/anti-request-forgery.md).</span></span>

## <a name="post-data-and-hidden-fields"></a><span data-ttu-id="2ed00-169">后期数据和隐藏的字段</span><span class="sxs-lookup"><span data-stu-id="2ed00-169">Post data and hidden fields</span></span>

<span data-ttu-id="2ed00-170">可以保存在隐藏的表单字段数据，并且将其重新发布了下一个请求。</span><span class="sxs-lookup"><span data-stu-id="2ed00-170">Data can be saved in hidden form fields and posted back on the next request.</span></span> <span data-ttu-id="2ed00-171">这是常见的多页的窗体。</span><span class="sxs-lookup"><span data-stu-id="2ed00-171">This is common in multipage forms.</span></span> <span data-ttu-id="2ed00-172">但是，客户端可能可以篡改数据，因为服务器必须始终重新验证它。</span><span class="sxs-lookup"><span data-stu-id="2ed00-172">However, because the  client can potentially tamper with the data, the server must always revalidate it.</span></span> 

## <a name="cookies"></a><span data-ttu-id="2ed00-173">Cookie</span><span class="sxs-lookup"><span data-stu-id="2ed00-173">Cookies</span></span>

<span data-ttu-id="2ed00-174">Cookie 提供了如何在 web 应用程序中存储特定于用户的数据。</span><span class="sxs-lookup"><span data-stu-id="2ed00-174">Cookies provide a way to store user-specific data in web applications.</span></span> <span data-ttu-id="2ed00-175">因为与每个请求一起发送 cookie，则其大小应保持在最低限度。</span><span class="sxs-lookup"><span data-stu-id="2ed00-175">Because cookies are sent with every request, their size should be kept to a minimum.</span></span> <span data-ttu-id="2ed00-176">理想情况下，仅标识符应存储在 cookie 中，与存储在服务器上的实际数据。</span><span class="sxs-lookup"><span data-stu-id="2ed00-176">Ideally, only an identifier should be stored in a cookie with the actual data stored on the server.</span></span> <span data-ttu-id="2ed00-177">大多数浏览器将限制为 4096 个字节的 cookie。</span><span class="sxs-lookup"><span data-stu-id="2ed00-177">Most browsers restrict cookies to 4096 bytes.</span></span> <span data-ttu-id="2ed00-178">此外，仅有限的数量的 cookie 可为每个域。</span><span class="sxs-lookup"><span data-stu-id="2ed00-178">In addition, only a limited number of cookies are available for each domain.</span></span>  

<span data-ttu-id="2ed00-179">Cookie 是易被篡改，因为它们必须在服务器上验证。</span><span class="sxs-lookup"><span data-stu-id="2ed00-179">Because cookies are subject to tampering, they must be validated on the server.</span></span> <span data-ttu-id="2ed00-180">尽管在客户端的 cookie 持续性是受影响用户干预，到期，但它们通常是客户端上的数据暂留的最持久形式。</span><span class="sxs-lookup"><span data-stu-id="2ed00-180">Although the durability of the cookie on a client is subject to user intervention and expiration, they are generally the most durable form of data persistence on the client.</span></span>

<span data-ttu-id="2ed00-181">通常使用 cookie 以进行个性化设置，其中的已知用户自定义内容。</span><span class="sxs-lookup"><span data-stu-id="2ed00-181">Cookies are often used for personalization, where content is customized for a known user.</span></span> <span data-ttu-id="2ed00-182">因为用户仅标识并且未经过身份验证在大多数情况下，你通常可以通过将用户名称、 帐户名称或唯一的用户 ID （例如 GUID) 存储在 cookie 中保护 cookie。</span><span class="sxs-lookup"><span data-stu-id="2ed00-182">Because the user is only identified and not authenticated in most cases, you can typically secure a cookie by storing the user name, account name, or a unique user ID (such as a GUID) in the cookie.</span></span> <span data-ttu-id="2ed00-183">然后可以使用 cookie 来访问站点的用户个性化设置基础结构。</span><span class="sxs-lookup"><span data-stu-id="2ed00-183">You can then use the cookie to access the user personalization infrastructure of a site.</span></span>

## <a name="httpcontextitems"></a><span data-ttu-id="2ed00-184">HttpContext.Items</span><span class="sxs-lookup"><span data-stu-id="2ed00-184">HttpContext.Items</span></span>

<span data-ttu-id="2ed00-185">`Items`集合是存储的数据的正确位置仅需要同时处理一个特定的请求。</span><span class="sxs-lookup"><span data-stu-id="2ed00-185">The `Items` collection is a good location to store data that is needed only while processing one particular request.</span></span> <span data-ttu-id="2ed00-186">每个请求之后，集合的内容将被放弃。</span><span class="sxs-lookup"><span data-stu-id="2ed00-186">The collection's contents are discarded after each request.</span></span> <span data-ttu-id="2ed00-187">`Items`集合最用作一种方法的组件或中间件进行通信时它们在请求过程的时间内运行的不同时间点，并且具有无法直接将参数传递。</span><span class="sxs-lookup"><span data-stu-id="2ed00-187">The `Items` collection is best used as a way for components or middleware to communicate when they operate at different points in time during a request and have no direct way to pass parameters.</span></span> <span data-ttu-id="2ed00-188">有关详细信息，请参阅[使用 HttpContext.Items](#working-with-httpcontextitems)，本文稍后的。</span><span class="sxs-lookup"><span data-stu-id="2ed00-188">For more information, see [Working with HttpContext.Items](#working-with-httpcontextitems), later in this article.</span></span>

## <a name="cache"></a><span data-ttu-id="2ed00-189">缓存</span><span class="sxs-lookup"><span data-stu-id="2ed00-189">Cache</span></span>

<span data-ttu-id="2ed00-190">缓存是一种高效的方式来存储和检索数据。</span><span class="sxs-lookup"><span data-stu-id="2ed00-190">Caching is an efficient way to store and retrieve data.</span></span> <span data-ttu-id="2ed00-191">你可以控制基于时间和其他注意事项的缓存项的生存期。</span><span class="sxs-lookup"><span data-stu-id="2ed00-191">You can control the lifetime of cached items based on time and other considerations.</span></span> <span data-ttu-id="2ed00-192">详细了解[Caching](../performance/caching/index.md)。</span><span class="sxs-lookup"><span data-stu-id="2ed00-192">Learn more about [Caching](../performance/caching/index.md).</span></span>

<a name="session"></a>
## <a name="working-with-session-state"></a><span data-ttu-id="2ed00-193">使用会话状态</span><span class="sxs-lookup"><span data-stu-id="2ed00-193">Working with Session State</span></span>

### <a name="configuring-session"></a><span data-ttu-id="2ed00-194">配置会话</span><span class="sxs-lookup"><span data-stu-id="2ed00-194">Configuring Session</span></span>

<span data-ttu-id="2ed00-195">`Microsoft.AspNetCore.Session`包提供用于管理会话状态的中间件。</span><span class="sxs-lookup"><span data-stu-id="2ed00-195">The `Microsoft.AspNetCore.Session` package provides middleware for managing session state.</span></span> <span data-ttu-id="2ed00-196">若要启用会话中间件，`Startup`必须包含：</span><span class="sxs-lookup"><span data-stu-id="2ed00-196">To enable the session middleware, `Startup`must contain:</span></span>

- <span data-ttu-id="2ed00-197">任一[IDistributedCache](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.distributed.idistributedcache)内存缓存。</span><span class="sxs-lookup"><span data-stu-id="2ed00-197">Any of the [IDistributedCache](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.distributed.idistributedcache) memory caches.</span></span> <span data-ttu-id="2ed00-198">`IDistributedCache`实现用于作为后备存储会话。</span><span class="sxs-lookup"><span data-stu-id="2ed00-198">The `IDistributedCache` implementation is used as a backing store for session.</span></span>
- <span data-ttu-id="2ed00-199">[AddSession](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions#Microsoft_Extensions_DependencyInjection_SessionServiceCollectionExtensions_AddSession_Microsoft_Extensions_DependencyInjection_IServiceCollection_) ，调用要求 NuGet 包"Microsoft.AspNetCore.Session"。</span><span class="sxs-lookup"><span data-stu-id="2ed00-199">[AddSession](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions#Microsoft_Extensions_DependencyInjection_SessionServiceCollectionExtensions_AddSession_Microsoft_Extensions_DependencyInjection_IServiceCollection_) call, which requires NuGet package "Microsoft.AspNetCore.Session".</span></span>
- <span data-ttu-id="2ed00-200">[UseSession](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions#methods_)调用。</span><span class="sxs-lookup"><span data-stu-id="2ed00-200">[UseSession](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions#methods_) call.</span></span>

<span data-ttu-id="2ed00-201">下面的代码演示如何设置内存中的会话提供程序。</span><span class="sxs-lookup"><span data-stu-id="2ed00-201">The following code shows how to set up the in-memory session provider.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="2ed00-202">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="2ed00-202">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

[!code-csharp[Main](app-state/sample/src/WebAppSessionDotNetCore2.0App/Startup.cs?highlight=11-19,24)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="2ed00-203">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="2ed00-203">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

[!code-csharp[Main](app-state/sample/src/WebAppSession/Startup.cs?highlight=11-19,24)]

---

<span data-ttu-id="2ed00-204">你可以引用会话中的从`HttpContext`后安装和配置它。</span><span class="sxs-lookup"><span data-stu-id="2ed00-204">You can reference Session from `HttpContext` once it is installed and configured.</span></span>

<span data-ttu-id="2ed00-205">如果你尝试访问`Session`之前`UseSession`已调用，该异常`InvalidOperationException: Session has not been configured for this application or request`引发。</span><span class="sxs-lookup"><span data-stu-id="2ed00-205">If you try to access `Session` before `UseSession` has been called, the exception `InvalidOperationException: Session has not been configured for this application or request` is thrown.</span></span>

<span data-ttu-id="2ed00-206">如果你尝试创建一个新`Session`（即，已创建任何会话 cookie） 已经开始写入后`Response`流处理时，异常`InvalidOperationException: The session cannot be established after the response has started`引发。</span><span class="sxs-lookup"><span data-stu-id="2ed00-206">If you try to create a new `Session` (that is, no session cookie has been created) after you have already begun writing to the `Response` stream, the exception `InvalidOperationException: The session cannot be established after the response has started` is thrown.</span></span> <span data-ttu-id="2ed00-207">可以在 web 服务器日志; 中找到的异常它将不会显示在浏览器。</span><span class="sxs-lookup"><span data-stu-id="2ed00-207">The exception can be found in the web server log; it will not be displayed in the browser.</span></span>

### <a name="loading-session-asynchronously"></a><span data-ttu-id="2ed00-208">以异步方式加载会话</span><span class="sxs-lookup"><span data-stu-id="2ed00-208">Loading Session asynchronously</span></span> 

<span data-ttu-id="2ed00-209">ASP.NET 核心中的默认会话提供程序，则从基础加载会话记录[IDistributedCache](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.distributed.idistributedcache)以异步方式存储才[ISession.LoadAsync](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.isession#Microsoft_AspNetCore_Http_ISession_LoadAsync)之前显式调用方法 `TryGetValue`， `Set`，或`Remove`方法。</span><span class="sxs-lookup"><span data-stu-id="2ed00-209">The default session provider in ASP.NET Core loads the session record from the underlying [IDistributedCache](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.caching.distributed.idistributedcache) store asynchronously only if the [ISession.LoadAsync](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.isession#Microsoft_AspNetCore_Http_ISession_LoadAsync) method is explicitly called before  the `TryGetValue`, `Set`, or `Remove` methods.</span></span> <span data-ttu-id="2ed00-210">如果`LoadAsync`则不会先调用，基础会话记录是否同步加载，这可能影响应用程序能够扩展。</span><span class="sxs-lookup"><span data-stu-id="2ed00-210">If `LoadAsync` is not called first, the underlying session record is loaded synchronously, which could potentially impact the ability of the app to scale.</span></span>

<span data-ttu-id="2ed00-211">若要让应用程序强制实施此模式，包装[DistributedSessionStore](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.session.distributedsessionstore)和[DistributedSession](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.session.distributedsession)实施服务，如果引发异常的版本和`LoadAsync`方法不是之前调用`TryGetValue`， `Set`，或`Remove`。</span><span class="sxs-lookup"><span data-stu-id="2ed00-211">To have applications enforce this pattern, wrap the [DistributedSessionStore](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.session.distributedsessionstore) and [DistributedSession](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.session.distributedsession) implementations with versions that throw an exception if the `LoadAsync` method is not called before `TryGetValue`, `Set`, or `Remove`.</span></span> <span data-ttu-id="2ed00-212">在服务容器中注册的已包装的版本。</span><span class="sxs-lookup"><span data-stu-id="2ed00-212">Register the wrapped versions in the services container.</span></span>

### <a name="implementation-details"></a><span data-ttu-id="2ed00-213">实现详细信息</span><span class="sxs-lookup"><span data-stu-id="2ed00-213">Implementation Details</span></span>

<span data-ttu-id="2ed00-214">会话使用 cookie 跟踪和标识来自单个浏览器的请求。</span><span class="sxs-lookup"><span data-stu-id="2ed00-214">Session uses a cookie to track and identify requests from a single browser.</span></span> <span data-ttu-id="2ed00-215">默认情况下，此 cookie 名为"。AspNet.Session"，并使用的路径"/"。</span><span class="sxs-lookup"><span data-stu-id="2ed00-215">By default, this cookie is named ".AspNet.Session", and it uses a path of "/".</span></span> <span data-ttu-id="2ed00-216">Cookie 默认未指定域，因为它不将提供给客户端脚本的页上 (因为`CookieHttpOnly`默认为`true`)。</span><span class="sxs-lookup"><span data-stu-id="2ed00-216">Because the cookie default does not specify a domain, it is not made available to the client-side script on the page (because `CookieHttpOnly` defaults to `true`).</span></span>

<span data-ttu-id="2ed00-217">若要重写会话默认值，使用`SessionOptions`:</span><span class="sxs-lookup"><span data-stu-id="2ed00-217">To override session defaults, use `SessionOptions`:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="2ed00-218">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="2ed00-218">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

[!code-csharp[Main](app-state/sample/src/WebAppSessionDotNetCore2.0App/StartupCopy.cs?name=snippet1&highlight=8-12)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="2ed00-219">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="2ed00-219">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

[!code-csharp[Main](app-state/sample/src/WebAppSession/StartupCopy.cs?name=snippet1&highlight=8-12)]

---

<span data-ttu-id="2ed00-220">服务器使用`IdleTimeout`属性来确定并放弃其内容之前，会话可以保持空闲的时间长短。</span><span class="sxs-lookup"><span data-stu-id="2ed00-220">The server uses the `IdleTimeout` property to determine how long a session can be idle before its contents are abandoned.</span></span> <span data-ttu-id="2ed00-221">此属性是独立于 cookie 到期时间。</span><span class="sxs-lookup"><span data-stu-id="2ed00-221">This property is independent of the cookie expiration.</span></span> <span data-ttu-id="2ed00-222">传递的会话中间件 （读取或写入） 每个请求将重置超时。</span><span class="sxs-lookup"><span data-stu-id="2ed00-222">Each request that passes through the Session middleware (read from or written to) resets the timeout.</span></span>

<span data-ttu-id="2ed00-223">因为`Session`是*非锁定*，如果两个请求都试图修改的会话中，最后一个内容重写第一个。</span><span class="sxs-lookup"><span data-stu-id="2ed00-223">Because `Session` is *non-locking*, if two requests both attempt to modify the contents of session, the last one overrides the first.</span></span> <span data-ttu-id="2ed00-224">`Session`作为实现*连贯会话*，这意味着所有内容都存储在一起。</span><span class="sxs-lookup"><span data-stu-id="2ed00-224">`Session` is implemented as a *coherent session*, which means that all the contents are stored together.</span></span> <span data-ttu-id="2ed00-225">正在修改的会话 （不同的密钥） 的不同部分的两个请求仍可能会影响每个其他。</span><span class="sxs-lookup"><span data-stu-id="2ed00-225">Two requests that are modifying different parts of the session (different keys) might still impact each other.</span></span>

### <a name="setting-and-getting-session-values"></a><span data-ttu-id="2ed00-226">设置和获取会话值</span><span class="sxs-lookup"><span data-stu-id="2ed00-226">Setting and getting Session values</span></span>

<span data-ttu-id="2ed00-227">通过访问会话`Session`属性`HttpContext`。</span><span class="sxs-lookup"><span data-stu-id="2ed00-227">Session is accessed through the `Session` property on `HttpContext`.</span></span> <span data-ttu-id="2ed00-228">此属性是[ISession](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.isession)实现。</span><span class="sxs-lookup"><span data-stu-id="2ed00-228">This property is an [ISession](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.isession) implementation.</span></span>

<span data-ttu-id="2ed00-229">下面的示例演示了设置和获取 int 和字符串：</span><span class="sxs-lookup"><span data-stu-id="2ed00-229">The following example shows setting and getting an int and a string:</span></span>

[!code-csharp[Main](app-state/sample/src/WebAppSession/Controllers/HomeController.cs?range=8-27,49)]

<span data-ttu-id="2ed00-230">如果你添加的以下扩展方法，你可以设置并获取可序列化的对象添加到会话：</span><span class="sxs-lookup"><span data-stu-id="2ed00-230">If you add the following extension methods, you can set and get serializable objects to Session:</span></span>

[!code-csharp[Main](app-state/sample/src/WebAppSession/Extensions/SessionExtensions.cs)]

<span data-ttu-id="2ed00-231">下面的示例演示如何设置和获取可序列化的对象：</span><span class="sxs-lookup"><span data-stu-id="2ed00-231">The following sample shows how to set and get a serializable object:</span></span>

[!code-csharp[Main](app-state/sample/src/WebAppSession/Controllers/HomeController.cs?name=snippet2)]


## <a name="working-with-httpcontextitems"></a><span data-ttu-id="2ed00-232">使用 HttpContext.Items</span><span class="sxs-lookup"><span data-stu-id="2ed00-232">Working with HttpContext.Items</span></span>

<span data-ttu-id="2ed00-233">`HttpContext`抽象类型的字典集合提供支持`IDictionary<object, object>`、 调用`Items`。</span><span class="sxs-lookup"><span data-stu-id="2ed00-233">The `HttpContext` abstraction provides support for a dictionary collection of type `IDictionary<object, object>`, called `Items`.</span></span> <span data-ttu-id="2ed00-234">此集合是从开始处可用*HttpRequest*并且末尾的每个请求将被丢弃。</span><span class="sxs-lookup"><span data-stu-id="2ed00-234">This collection is available from the start of an *HttpRequest* and is discarded at the end of each request.</span></span> <span data-ttu-id="2ed00-235">通过将值分配给某一键控的项，或通过请求特定键的值，你可以访问它。</span><span class="sxs-lookup"><span data-stu-id="2ed00-235">You can access it by  assigning a value to a keyed entry, or by requesting the value for a particular key.</span></span>

<span data-ttu-id="2ed00-236">在下面，示例[中间件](middleware.md)添加`isVerified`到`Items`集合。</span><span class="sxs-lookup"><span data-stu-id="2ed00-236">In the sample below, [Middleware](middleware.md) adds `isVerified` to the `Items` collection.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // perform some verification
    context.Items["isVerified"] = true;
    await next.Invoke();
});
```

<span data-ttu-id="2ed00-237">更高版本在管道中，另一个中间件无法访问它：</span><span class="sxs-lookup"><span data-stu-id="2ed00-237">Later in the pipeline, another middleware could access it:</span></span>

```csharp
app.Run(async (context) =>
{
    await context.Response.WriteAsync("Verified request? " + 
        context.Items["isVerified"]);
});
```

<span data-ttu-id="2ed00-238">为中间件将仅由单个应用，`string`密钥是可接受。</span><span class="sxs-lookup"><span data-stu-id="2ed00-238">For middleware that will only be used by a single app, `string` keys are acceptable.</span></span> <span data-ttu-id="2ed00-239">但是，将应用程序之间共享的中间件应使用唯一的对象键以避免任何机会键冲突。</span><span class="sxs-lookup"><span data-stu-id="2ed00-239">However, middleware that will be shared between applications should use unique object keys to avoid any chance of key collisions.</span></span> <span data-ttu-id="2ed00-240">如果你正在开发必须跨多个应用程序工作的中间件，使用如下所示中间件类中定义的唯一对象密钥：</span><span class="sxs-lookup"><span data-stu-id="2ed00-240">If you are developing middleware that must work across multiple applications, use a unique object key defined in your middleware class as shown below:</span></span>

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

<span data-ttu-id="2ed00-241">其他代码可以访问存储中的值`HttpContext.Items`使用中间件类公开的密钥：</span><span class="sxs-lookup"><span data-stu-id="2ed00-241">Other code can access the value stored in `HttpContext.Items` using the key exposed by the middleware class:</span></span>

```csharp
public class HomeController : Controller
{
    public IActionResult Index()
    {
        string value = HttpContext.Items[SampleMiddleware.SampleKey];
    }
}
```

<span data-ttu-id="2ed00-242">此方法还具有消除重复的代码中的多个位置中的"神奇字符串"的优点。</span><span class="sxs-lookup"><span data-stu-id="2ed00-242">This approach also has the advantage of eliminating repetition of "magic strings" in multiple places in the code.</span></span>

<a name="appstate-errors"></a>

## <a name="application-state-data"></a><span data-ttu-id="2ed00-243">应用程序状态数据</span><span class="sxs-lookup"><span data-stu-id="2ed00-243">Application state data</span></span>

<span data-ttu-id="2ed00-244">使用[依赖关系注入](xref:fundamentals/dependency-injection)可向所有用户提供数据：</span><span class="sxs-lookup"><span data-stu-id="2ed00-244">Use [Dependency Injection](xref:fundamentals/dependency-injection) to make data available to all users:</span></span>

1. <span data-ttu-id="2ed00-245">定义包含数据的服务 (例如，一个名为的类`MyAppData`)。</span><span class="sxs-lookup"><span data-stu-id="2ed00-245">Define a service containing the data (for example, a class named `MyAppData`).</span></span>

```csharp
public class MyAppData
{
    // Declare properties/methods/etc.
} 
```
2. <span data-ttu-id="2ed00-246">添加到的服务类`ConfigureServices`(例如`services.AddSingleton<MyAppData>();`)。</span><span class="sxs-lookup"><span data-stu-id="2ed00-246">Add the service class to `ConfigureServices` (for example `services.AddSingleton<MyAppData>();`).</span></span>
3. <span data-ttu-id="2ed00-247">使用每个控制器中的数据服务类：</span><span class="sxs-lookup"><span data-stu-id="2ed00-247">Consume the data service class in each controller:</span></span>

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

### <a name="common-errors-when-working-with-session"></a><span data-ttu-id="2ed00-248">使用会话时的常见错误</span><span class="sxs-lookup"><span data-stu-id="2ed00-248">Common errors when working with session</span></span>

* <span data-ttu-id="2ed00-249">"无法解析为类型 Microsoft.Extensions.Caching.Distributed.IDistributedCache 的服务在尝试激活 Microsoft.AspNetCore.Session.DistributedSessionStore 时。"</span><span class="sxs-lookup"><span data-stu-id="2ed00-249">"Unable to resolve service for type 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' while attempting to activate 'Microsoft.AspNetCore.Session.DistributedSessionStore'."</span></span>

  <span data-ttu-id="2ed00-250">这通常由于不能配置至少一个`IDistributedCache`实现。</span><span class="sxs-lookup"><span data-stu-id="2ed00-250">This is usually caused by failing to configure at least one `IDistributedCache` implementation.</span></span> <span data-ttu-id="2ed00-251">有关详细信息，请参阅[使用分布式缓存](xref:performance/caching/distributed)和[中内存缓存](xref:performance/caching/memory)。</span><span class="sxs-lookup"><span data-stu-id="2ed00-251">For more information, see [Working with a Distributed Cache](xref:performance/caching/distributed) and [In memory caching](xref:performance/caching/memory).</span></span>

### <a name="additional-resources"></a><span data-ttu-id="2ed00-252">其他资源</span><span class="sxs-lookup"><span data-stu-id="2ed00-252">Additional Resources</span></span>


* [<span data-ttu-id="2ed00-253">ASP.NET 核心 1.x： 本文档中使用的代码示例</span><span class="sxs-lookup"><span data-stu-id="2ed00-253">ASP.NET Core 1.x: Sample code used in this document</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/app-state/sample/src/WebAppSession)
* [<span data-ttu-id="2ed00-254">ASP.NET 核心 2.x： 本文档中使用的代码示例</span><span class="sxs-lookup"><span data-stu-id="2ed00-254">ASP.NET Core 2.x: Sample code used in this document</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/app-state/sample/src/WebAppSessionDotNetCore2.0App)
