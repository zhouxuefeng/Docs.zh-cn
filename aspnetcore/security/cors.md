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
# <a name="enabling-cross-origin-requests-cors"></a><span data-ttu-id="a92c1-103">启用跨源请求 (CORS)</span><span class="sxs-lookup"><span data-stu-id="a92c1-103">Enabling Cross-Origin Requests (CORS)</span></span>

<span data-ttu-id="a92c1-104">通过[Mike Wasson](https://github.com/mikewasson)， [Shayne 贝叶](https://twitter.com/spboyer)，和[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="a92c1-104">By [Mike Wasson](https://github.com/mikewasson), [Shayne Boyer](https://twitter.com/spboyer), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="a92c1-105">浏览器安全阻止到另一个域进行 AJAX 请求 web 页。</span><span class="sxs-lookup"><span data-stu-id="a92c1-105">Browser security prevents a web page from making AJAX requests to another domain.</span></span> <span data-ttu-id="a92c1-106">此限制称为*同源策略*，，可防止恶意站点读取另一个站点中的敏感数据。</span><span class="sxs-lookup"><span data-stu-id="a92c1-106">This restriction is called the *same-origin policy*, and prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="a92c1-107">但是，有时你可能想要允许对你的 web API 进行的跨源请求其他站点。</span><span class="sxs-lookup"><span data-stu-id="a92c1-107">However, sometimes you might want to let other sites make cross-origin requests to your web API.</span></span>

<span data-ttu-id="a92c1-108">[跨域资源共享](http://www.w3.org/TR/cors/)(CORS) 是一种 W3C 标准，允许服务器放宽了同源策略。</span><span class="sxs-lookup"><span data-stu-id="a92c1-108">[Cross Origin Resource Sharing](http://www.w3.org/TR/cors/) (CORS) is a W3C standard that allows a server to relax the same-origin policy.</span></span> <span data-ttu-id="a92c1-109">使用 CORS，服务器可以显式允许某些跨源请求时拒绝其他人。</span><span class="sxs-lookup"><span data-stu-id="a92c1-109">Using CORS, a server can explicitly allow some cross-origin requests while rejecting others.</span></span> <span data-ttu-id="a92c1-110">CORS 是更安全、 更灵活比早期技术如[JSONP](https://wikipedia.org/wiki/JSONP)。</span><span class="sxs-lookup"><span data-stu-id="a92c1-110">CORS is safer and more flexible than earlier techniques such as [JSONP](https://wikipedia.org/wiki/JSONP).</span></span> <span data-ttu-id="a92c1-111">本主题演示如何在 ASP.NET Core 应用程序中启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="a92c1-111">This topic shows how to enable CORS in an ASP.NET Core application.</span></span>

## <a name="what-is-same-origin"></a><span data-ttu-id="a92c1-112">什么是"相同源"？</span><span class="sxs-lookup"><span data-stu-id="a92c1-112">What is "same origin"?</span></span>

<span data-ttu-id="a92c1-113">如果它们具有相同的方案、 主机和端口，两个 Url 将具有相同的原点。</span><span class="sxs-lookup"><span data-stu-id="a92c1-113">Two URLs have the same origin if they have identical schemes, hosts, and ports.</span></span> <span data-ttu-id="a92c1-114">([RFC 6454](http://tools.ietf.org/html/rfc6454))</span><span class="sxs-lookup"><span data-stu-id="a92c1-114">([RFC 6454](http://tools.ietf.org/html/rfc6454))</span></span>

<span data-ttu-id="a92c1-115">这些两个 Url 具有相同的来源：</span><span class="sxs-lookup"><span data-stu-id="a92c1-115">These two URLs have the same origin:</span></span>

* `http://example.com/foo.html`

* `http://example.com/bar.html`

<span data-ttu-id="a92c1-116">这些 Url 有两个比以前的不同来源：</span><span class="sxs-lookup"><span data-stu-id="a92c1-116">These URLs have different origins than the previous two:</span></span>

* <span data-ttu-id="a92c1-117">`http://example.net`-不同的域</span><span class="sxs-lookup"><span data-stu-id="a92c1-117">`http://example.net` - Different domain</span></span>

* <span data-ttu-id="a92c1-118">`http://www.example.com/foo.html`的不同子域</span><span class="sxs-lookup"><span data-stu-id="a92c1-118">`http://www.example.com/foo.html` - Different subdomain</span></span>

* <span data-ttu-id="a92c1-119">`https://example.com/foo.html`-不同的方案</span><span class="sxs-lookup"><span data-stu-id="a92c1-119">`https://example.com/foo.html` - Different scheme</span></span>

* <span data-ttu-id="a92c1-120">`http://example.com:9000/foo.html`-不同的端口</span><span class="sxs-lookup"><span data-stu-id="a92c1-120">`http://example.com:9000/foo.html` - Different port</span></span>

> [!NOTE]
> <span data-ttu-id="a92c1-121">比较来源时，Internet Explorer 不考虑端口。</span><span class="sxs-lookup"><span data-stu-id="a92c1-121">Internet Explorer does not consider the port when comparing origins.</span></span>

## <a name="setting-up-cors"></a><span data-ttu-id="a92c1-122">CORS 设置</span><span class="sxs-lookup"><span data-stu-id="a92c1-122">Setting up CORS</span></span>

<span data-ttu-id="a92c1-123">若要设置你的应用程序的 CORS 添加`Microsoft.AspNetCore.Cors`包到你的项目。</span><span class="sxs-lookup"><span data-stu-id="a92c1-123">To set up CORS for your application add the `Microsoft.AspNetCore.Cors` package to your project.</span></span>

<span data-ttu-id="a92c1-124">添加会在 Startup.cs 的 CORS 服务：</span><span class="sxs-lookup"><span data-stu-id="a92c1-124">Add the CORS services in Startup.cs:</span></span>

[!code-csharp[Main](cors/sample/CorsExample1/Startup.cs?name=snippet_addcors)]

## <a name="enabling-cors-with-middleware"></a><span data-ttu-id="a92c1-125">启用 CORS 的中间件</span><span class="sxs-lookup"><span data-stu-id="a92c1-125">Enabling CORS with middleware</span></span>

<span data-ttu-id="a92c1-126">若要启用 CORS 整个应用程序添加 CORS 中间件请求管道使用`UseCors`扩展方法。</span><span class="sxs-lookup"><span data-stu-id="a92c1-126">To enable CORS for your entire application add the CORS middleware to your request pipeline using the `UseCors` extension method.</span></span> <span data-ttu-id="a92c1-127">请注意 CORS 中间件，必须在任何定义的终结点之前你想要支持跨源请求 （例如应用程序中。</span><span class="sxs-lookup"><span data-stu-id="a92c1-127">Note that the CORS middleware must precede any defined endpoints in your app that you want to support cross-origin requests (ex.</span></span> <span data-ttu-id="a92c1-128">对任何调用之前`UseMvc`)。</span><span class="sxs-lookup"><span data-stu-id="a92c1-128">before any call to `UseMvc`).</span></span>

<span data-ttu-id="a92c1-129">添加 CORS 中间件使用时，可以指定跨域策略`CorsPolicyBuilder`类。</span><span class="sxs-lookup"><span data-stu-id="a92c1-129">You can specify a cross-origin policy when adding the CORS middleware using the `CorsPolicyBuilder` class.</span></span> <span data-ttu-id="a92c1-130">有两种方法可以实现此目的。</span><span class="sxs-lookup"><span data-stu-id="a92c1-130">There are two ways to do this.</span></span> <span data-ttu-id="a92c1-131">第一种是使用 lambda 调用 UseCors:</span><span class="sxs-lookup"><span data-stu-id="a92c1-131">The first is to call UseCors with a lambda:</span></span>

[!code-csharp[Main](cors/sample/CorsExample1/Startup.cs?highlight=11,12&range=22-38)]

<span data-ttu-id="a92c1-132">**注意：**而无需尾部反斜杠，必须指定的 URL (`/`)。</span><span class="sxs-lookup"><span data-stu-id="a92c1-132">**Note:** The URL must be specified without a trailing slash (`/`).</span></span> <span data-ttu-id="a92c1-133">如果 URL 终止与`/`，比较将返回`false`并且将返回没有标头。</span><span class="sxs-lookup"><span data-stu-id="a92c1-133">If the URL terminates with `/`, the comparison will return `false` and no header will be returned.</span></span>

<span data-ttu-id="a92c1-134">Lambda 采用`CorsPolicyBuilder`对象。</span><span class="sxs-lookup"><span data-stu-id="a92c1-134">The lambda takes a `CorsPolicyBuilder` object.</span></span> <span data-ttu-id="a92c1-135">你将找到一份[配置选项](#cors-policy-options)本主题中更高版本。</span><span class="sxs-lookup"><span data-stu-id="a92c1-135">You'll find a list of the [configuration options](#cors-policy-options) later in this topic.</span></span> <span data-ttu-id="a92c1-136">在此示例中，该策略允许来自的跨域请求`http://example.com`和其他的原点。</span><span class="sxs-lookup"><span data-stu-id="a92c1-136">In this example, the policy allows cross-origin requests from `http://example.com` and no other origins.</span></span>

<span data-ttu-id="a92c1-137">请注意 CorsPolicyBuilder 有 fluent API，因此你可以将方法调用的链接：</span><span class="sxs-lookup"><span data-stu-id="a92c1-137">Note that CorsPolicyBuilder has a fluent API, so you can chain method calls:</span></span>

[!code-csharp[Main](../security/cors/sample/CorsExample3/Startup.cs?highlight=3&range=29-32)]

<span data-ttu-id="a92c1-138">第二种方法是定义一个或多个命名的 CORS 策略，并在运行时按名称然后选择的策略。</span><span class="sxs-lookup"><span data-stu-id="a92c1-138">The second approach is to define one or more named CORS policies, and then select the policy by name at run time.</span></span>

[!code-csharp[Main](cors/sample/CorsExample2/Startup.cs?name=snippet_begin)]

<span data-ttu-id="a92c1-139">此示例添加名为"AllowSpecificOrigin"的 CORS 策略。</span><span class="sxs-lookup"><span data-stu-id="a92c1-139">This example adds a CORS policy named "AllowSpecificOrigin".</span></span> <span data-ttu-id="a92c1-140">若要选择的策略，将名称传递给`UseCors`。</span><span class="sxs-lookup"><span data-stu-id="a92c1-140">To select the policy, pass the name to `UseCors`.</span></span>

## <a name="enabling-cors-in-mvc"></a><span data-ttu-id="a92c1-141">在 MVC 启用 CORS</span><span class="sxs-lookup"><span data-stu-id="a92c1-141">Enabling CORS in MVC</span></span>

<span data-ttu-id="a92c1-142">MVC 或者可用于应用每个操作，每个控制器，或全局范围内的所有控制器的特定 CORS。</span><span class="sxs-lookup"><span data-stu-id="a92c1-142">You can alternatively use MVC to apply specific CORS per action, per controller, or globally for all controllers.</span></span> <span data-ttu-id="a92c1-143">使用 MVC 若要启用 CORS 时使用相同的 CORS 服务，但不是 CORS 中间件。</span><span class="sxs-lookup"><span data-stu-id="a92c1-143">When using MVC to enable CORS the same CORS services are used, but the CORS middleware is not.</span></span>

### <a name="per-action"></a><span data-ttu-id="a92c1-144">每个操作</span><span class="sxs-lookup"><span data-stu-id="a92c1-144">Per action</span></span>

<span data-ttu-id="a92c1-145">若要指定特定的操作的 CORS 策略添加`[EnableCors]`属性设为该操作。</span><span class="sxs-lookup"><span data-stu-id="a92c1-145">To specify a CORS policy for a specific action add the `[EnableCors]` attribute to the action.</span></span> <span data-ttu-id="a92c1-146">指定策略名称。</span><span class="sxs-lookup"><span data-stu-id="a92c1-146">Specify the policy name.</span></span>

[!code-csharp[Main](cors/sample/CorsMVC/Controllers/ValuesController.cs?name=EnableOnAction)]

### <a name="per-controller"></a><span data-ttu-id="a92c1-147">每个控制器</span><span class="sxs-lookup"><span data-stu-id="a92c1-147">Per controller</span></span>

<span data-ttu-id="a92c1-148">若要指定一个特定的控制器的 CORS 策略添加`[EnableCors]`到控制器类属性。</span><span class="sxs-lookup"><span data-stu-id="a92c1-148">To specify the CORS policy for a specific controller add the `[EnableCors]` attribute to the controller class.</span></span> <span data-ttu-id="a92c1-149">指定策略名称。</span><span class="sxs-lookup"><span data-stu-id="a92c1-149">Specify the policy name.</span></span>

[!code-csharp[Main](cors/sample/CorsMVC/Controllers/ValuesController.cs?name=EnableOnController)]

### <a name="globally"></a><span data-ttu-id="a92c1-150">全局</span><span class="sxs-lookup"><span data-stu-id="a92c1-150">Globally</span></span>

<span data-ttu-id="a92c1-151">你可以启用 CORS 全局范围内的所有控制器的添加`CorsAuthorizationFilterFactory`到全局筛选器集合的筛选器：</span><span class="sxs-lookup"><span data-stu-id="a92c1-151">You can enable CORS globally for all controllers by adding the `CorsAuthorizationFilterFactory` filter to the global filter collection:</span></span>

[!code-csharp[Main](cors/sample/CorsMVC/Startup2.cs?name=snippet_configureservices)]

<span data-ttu-id="a92c1-152">优先顺序是： 操作，控制器，全局。</span><span class="sxs-lookup"><span data-stu-id="a92c1-152">The precedence order is: Action, controller, global.</span></span> <span data-ttu-id="a92c1-153">操作级别的策略优先于控制器级别的策略，并控制器级别策略优先于全局策略。</span><span class="sxs-lookup"><span data-stu-id="a92c1-153">Action-level policies take precedence over controller-level policies, and controller-level policies take precedence over global policies.</span></span>

### <a name="disable-cors"></a><span data-ttu-id="a92c1-154">禁用 CORS</span><span class="sxs-lookup"><span data-stu-id="a92c1-154">Disable CORS</span></span>

<span data-ttu-id="a92c1-155">若要禁用 CORS 控制器或操作，使用`[DisableCors]`属性。</span><span class="sxs-lookup"><span data-stu-id="a92c1-155">To disable CORS for a controller or action, use the `[DisableCors]` attribute.</span></span>

[!code-csharp[Main](cors/sample/CorsMVC/Controllers/ValuesController.cs?name=DisableOnAction)]

## <a name="cors-policy-options"></a><span data-ttu-id="a92c1-156">CORS 策略选项</span><span class="sxs-lookup"><span data-stu-id="a92c1-156">CORS policy options</span></span>

<span data-ttu-id="a92c1-157">本部分介绍你可以在 CORS 策略设置的各种选项。</span><span class="sxs-lookup"><span data-stu-id="a92c1-157">This section describes the various options that you can set in a CORS policy.</span></span>

* [<span data-ttu-id="a92c1-158">设置允许的来源</span><span class="sxs-lookup"><span data-stu-id="a92c1-158">Set the allowed origins</span></span>](#set-the-allowed-origins)

* [<span data-ttu-id="a92c1-159">设置允许的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="a92c1-159">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)

* [<span data-ttu-id="a92c1-160">设置允许的请求标头</span><span class="sxs-lookup"><span data-stu-id="a92c1-160">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)

* [<span data-ttu-id="a92c1-161">设置公开的响应标头</span><span class="sxs-lookup"><span data-stu-id="a92c1-161">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)

* [<span data-ttu-id="a92c1-162">在跨域请求中的凭据</span><span class="sxs-lookup"><span data-stu-id="a92c1-162">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)

* [<span data-ttu-id="a92c1-163">将预检过期时间设置</span><span class="sxs-lookup"><span data-stu-id="a92c1-163">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="a92c1-164">对于某些选项可能有有助于读取[如何 CORS 适用](#how-cors-works)第一个。</span><span class="sxs-lookup"><span data-stu-id="a92c1-164">For some options it may be helpful to read [How CORS works](#how-cors-works) first.</span></span>

### <a name="set-the-allowed-origins"></a><span data-ttu-id="a92c1-165">设置允许的来源</span><span class="sxs-lookup"><span data-stu-id="a92c1-165">Set the allowed origins</span></span>

<span data-ttu-id="a92c1-166">若要允许一个或多个特定来源：</span><span class="sxs-lookup"><span data-stu-id="a92c1-166">To allow one or more specific origins:</span></span>

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=19-23)]

<span data-ttu-id="a92c1-167">若要允许所有来源：</span><span class="sxs-lookup"><span data-stu-id="a92c1-167">To allow all origins:</span></span>

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs??range=27-31)]

<span data-ttu-id="a92c1-168">允许从任何源的请求之前应该认真考虑。</span><span class="sxs-lookup"><span data-stu-id="a92c1-168">Consider carefully before allowing requests from any origin.</span></span> <span data-ttu-id="a92c1-169">这意味着按原义任何网站可 AJAX 调用你的 API。</span><span class="sxs-lookup"><span data-stu-id="a92c1-169">It means that literally any website can make AJAX calls to your API.</span></span>

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="a92c1-170">设置允许的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="a92c1-170">Set the allowed HTTP methods</span></span>

<span data-ttu-id="a92c1-171">若要允许所有的 HTTP 方法：</span><span class="sxs-lookup"><span data-stu-id="a92c1-171">To allow all HTTP methods:</span></span>

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=44-49)]

<span data-ttu-id="a92c1-172">这会影响预检请求和访问-控制的允许的方法标头。</span><span class="sxs-lookup"><span data-stu-id="a92c1-172">This affects pre-flight requests and Access-Control-Allow-Methods header.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="a92c1-173">设置允许的请求标头</span><span class="sxs-lookup"><span data-stu-id="a92c1-173">Set the allowed request headers</span></span>

<span data-ttu-id="a92c1-174">CORS 预检请求可能包括一个访问控制的请求标头标头，列出由应用程序设置的 HTTP 标头 (所谓的"创作请求标头")。</span><span class="sxs-lookup"><span data-stu-id="a92c1-174">A CORS preflight request might include an Access-Control-Request-Headers header, listing the HTTP headers set by the application (the so-called "author request headers").</span></span>

<span data-ttu-id="a92c1-175">到白名单特定的标头：</span><span class="sxs-lookup"><span data-stu-id="a92c1-175">To whitelist specific headers:</span></span>

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=53-58)]

<span data-ttu-id="a92c1-176">若要允许所有创作请求标头：</span><span class="sxs-lookup"><span data-stu-id="a92c1-176">To allow all author request headers:</span></span>

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=62-67)]

<span data-ttu-id="a92c1-177">浏览器中都不完全一致它们如何设置访问控制的请求标头。</span><span class="sxs-lookup"><span data-stu-id="a92c1-177">Browsers are not entirely consistent in how they set Access-Control-Request-Headers.</span></span> <span data-ttu-id="a92c1-178">如果您设置标头为任何以外"*"，则应包含至少"接受"，"内容类型"，"源"和任何你想要支持的自定义标头。</span><span class="sxs-lookup"><span data-stu-id="a92c1-178">If you set headers to anything other than "*", you should include at least "accept", "content-type", and "origin", plus any custom headers that you want to support.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="a92c1-179">设置公开的响应标头</span><span class="sxs-lookup"><span data-stu-id="a92c1-179">Set the exposed response headers</span></span>

<span data-ttu-id="a92c1-180">默认情况下，浏览器不公开所有向应用程序的响应标头。</span><span class="sxs-lookup"><span data-stu-id="a92c1-180">By default, the browser does not expose all of the response headers to the application.</span></span> <span data-ttu-id="a92c1-181">(请参阅[http://www.w3.org/TR/cors/#simple-response-header](http://www.w3.org/TR/cors/#simple-response-header)。)默认为可用的响应标头是：</span><span class="sxs-lookup"><span data-stu-id="a92c1-181">(See [http://www.w3.org/TR/cors/#simple-response-header](http://www.w3.org/TR/cors/#simple-response-header).) The response headers that are available by default are:</span></span>

* <span data-ttu-id="a92c1-182">缓存控制</span><span class="sxs-lookup"><span data-stu-id="a92c1-182">Cache-Control</span></span>

* <span data-ttu-id="a92c1-183">内容语言</span><span class="sxs-lookup"><span data-stu-id="a92c1-183">Content-Language</span></span>

* <span data-ttu-id="a92c1-184">内容类型</span><span class="sxs-lookup"><span data-stu-id="a92c1-184">Content-Type</span></span>

* <span data-ttu-id="a92c1-185">过期</span><span class="sxs-lookup"><span data-stu-id="a92c1-185">Expires</span></span>

* <span data-ttu-id="a92c1-186">上次修改</span><span class="sxs-lookup"><span data-stu-id="a92c1-186">Last-Modified</span></span>

* <span data-ttu-id="a92c1-187">杂注</span><span class="sxs-lookup"><span data-stu-id="a92c1-187">Pragma</span></span>

<span data-ttu-id="a92c1-188">CORS 规范调用这些*简单响应标头*。</span><span class="sxs-lookup"><span data-stu-id="a92c1-188">The CORS spec calls these *simple response headers*.</span></span> <span data-ttu-id="a92c1-189">若要使应用程序的其他标头：</span><span class="sxs-lookup"><span data-stu-id="a92c1-189">To make other headers available to the application:</span></span>

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=71-76)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="a92c1-190">在跨域请求中的凭据</span><span class="sxs-lookup"><span data-stu-id="a92c1-190">Credentials in cross-origin requests</span></span>

<span data-ttu-id="a92c1-191">凭据需要 CORS 请求中的特殊处理。</span><span class="sxs-lookup"><span data-stu-id="a92c1-191">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="a92c1-192">默认情况下，浏览器不发送任何凭据与跨域请求。</span><span class="sxs-lookup"><span data-stu-id="a92c1-192">By default, the browser does not send any credentials with a cross-origin request.</span></span> <span data-ttu-id="a92c1-193">凭据包括 cookie 和 HTTP 身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="a92c1-193">Credentials include cookies as well as HTTP authentication schemes.</span></span> <span data-ttu-id="a92c1-194">若要发送的跨域请求的凭据，客户端必须设置为 true 的 XMLHttpRequest.withCredentials。</span><span class="sxs-lookup"><span data-stu-id="a92c1-194">To send credentials with a cross-origin request, the client must set XMLHttpRequest.withCredentials to true.</span></span>

<span data-ttu-id="a92c1-195">直接使用 XMLHttpRequest:</span><span class="sxs-lookup"><span data-stu-id="a92c1-195">Using XMLHttpRequest directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'http://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="a92c1-196">在 jQuery:</span><span class="sxs-lookup"><span data-stu-id="a92c1-196">In jQuery:</span></span>

```jQuery
$.ajax({
  type: 'get',
  url: 'http://www.example.com/home',
  xhrFields: {
    withCredentials: true
}
```

<span data-ttu-id="a92c1-197">此外，则服务器必须允许凭据。</span><span class="sxs-lookup"><span data-stu-id="a92c1-197">In addition, the server must allow the credentials.</span></span> <span data-ttu-id="a92c1-198">若要允许跨域凭据：</span><span class="sxs-lookup"><span data-stu-id="a92c1-198">To allow cross-origin credentials:</span></span>

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=80-85)]

<span data-ttu-id="a92c1-199">现在 HTTP 响应将包括一个访问控制的允许的凭据标头，它指示浏览器服务器允许跨域请求凭据。</span><span class="sxs-lookup"><span data-stu-id="a92c1-199">Now the HTTP response will include an Access-Control-Allow-Credentials header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="a92c1-200">如果浏览器发送凭据，但响应不包含有效的访问控制的允许的凭证标头，浏览器并不会对应用程序的响应，并且 AJAX 请求失败。</span><span class="sxs-lookup"><span data-stu-id="a92c1-200">If the browser sends credentials, but the response does not include a valid Access-Control-Allow-Credentials header, the browser will not expose the response to the application, and the AJAX request fails.</span></span>

<span data-ttu-id="a92c1-201">因为这意味着在另一个域网站可以不在用户不知情的情况下向用户的代表应用程序发送登录的用户的凭据，则要非常谨慎允许跨域凭据。</span><span class="sxs-lookup"><span data-stu-id="a92c1-201">Be very careful about allowing cross-origin credentials, because it means a website at another domain can send a logged-in user’s credentials to your app on the user’s behalf, without the user being aware.</span></span> <span data-ttu-id="a92c1-202">CORS 规范还状态到该设置来源"*"（所有来源） 无效的访问控制的允许的凭证标头是否存在。</span><span class="sxs-lookup"><span data-stu-id="a92c1-202">The CORS spec also states that setting origins to "*" (all origins) is invalid if the Access-Control-Allow-Credentials header is present.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="a92c1-203">将预检过期时间设置</span><span class="sxs-lookup"><span data-stu-id="a92c1-203">Set the preflight expiration time</span></span>

<span data-ttu-id="a92c1-204">访问控制的最长时间标头指定可以缓存预检请求的响应的时间就越长。</span><span class="sxs-lookup"><span data-stu-id="a92c1-204">The Access-Control-Max-Age header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="a92c1-205">若要设置此标头：</span><span class="sxs-lookup"><span data-stu-id="a92c1-205">To set this header:</span></span>

[!code-csharp[Main](cors/sample/CorsExample4/Startup.cs?range=89-94)]

<a name="cors-how-cors-works"></a>

## <a name="how-cors-works"></a><span data-ttu-id="a92c1-206">CORS 的工作原理</span><span class="sxs-lookup"><span data-stu-id="a92c1-206">How CORS works</span></span>

<span data-ttu-id="a92c1-207">本部分介绍在 CORS 请求中，在 HTTP 消息的级别会发生什么情况。</span><span class="sxs-lookup"><span data-stu-id="a92c1-207">This section describes what happens in a CORS request, at the level of the HTTP messages.</span></span> <span data-ttu-id="a92c1-208">请务必了解 CORS 的工作原理，以便你可以正确，配置你的 CORS 策略以及故障排除如果操作不能按预期工作。</span><span class="sxs-lookup"><span data-stu-id="a92c1-208">It’s important to understand how CORS works, so that you can configure your CORS policy correctly, and troubleshoot if things don’t work as you expect.</span></span>

<span data-ttu-id="a92c1-209">CORS 规范引入了几个新的 HTTP 标头启用跨域请求。</span><span class="sxs-lookup"><span data-stu-id="a92c1-209">The CORS specification introduces several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="a92c1-210">如果浏览器支持 CORS，则将自动为跨源请求; 这些标头设置你不必执行任何特殊的 JavaScript 代码中。</span><span class="sxs-lookup"><span data-stu-id="a92c1-210">If a browser supports CORS, it sets these headers automatically for cross-origin requests; you don’t need to do anything special in your JavaScript code.</span></span>

<span data-ttu-id="a92c1-211">下面是跨域请求的示例。</span><span class="sxs-lookup"><span data-stu-id="a92c1-211">Here is an example of a cross-origin request.</span></span> <span data-ttu-id="a92c1-212">"源"标头提供了正在发出请求的站点的域：</span><span class="sxs-lookup"><span data-stu-id="a92c1-212">The "Origin" header gives the domain of the site that is making the request:</span></span>

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

<span data-ttu-id="a92c1-213">如果服务器允许该请求，则将设置访问控制的允许的域标头。</span><span class="sxs-lookup"><span data-stu-id="a92c1-213">If the server allows the request, it sets the Access-Control-Allow-Origin header.</span></span> <span data-ttu-id="a92c1-214">此标头的值匹配的 Origin 标头，或是通配符值"*"，允许任何来源的含义。:</span><span class="sxs-lookup"><span data-stu-id="a92c1-214">The value of this header either matches the Origin header, or is the wildcard value "*", meaning that any origin is allowed.:</span></span>

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

<span data-ttu-id="a92c1-215">如果响应不包含访问控制的允许的域标头，AJAX 请求失败。</span><span class="sxs-lookup"><span data-stu-id="a92c1-215">If the response does not include the Access-Control-Allow-Origin header, the AJAX request fails.</span></span> <span data-ttu-id="a92c1-216">具体而言，浏览器不允许该请求。</span><span class="sxs-lookup"><span data-stu-id="a92c1-216">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="a92c1-217">即使服务器将返回成功的响应，浏览器不进行响应可供客户端应用程序。</span><span class="sxs-lookup"><span data-stu-id="a92c1-217">Even if the server returns a successful response, the browser does not make the response available to the client application.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="a92c1-218">预检请求</span><span class="sxs-lookup"><span data-stu-id="a92c1-218">Preflight Requests</span></span>

<span data-ttu-id="a92c1-219">对于某些 CORS 请求，浏览器发送一个其他的请求，调用"预检请求，"，再将发送实际请求的资源。</span><span class="sxs-lookup"><span data-stu-id="a92c1-219">For some CORS requests, the browser sends an additional request, called a "preflight request", before it sends the actual request for the resource.</span></span> <span data-ttu-id="a92c1-220">如果以下条件为真，浏览器可以跳过预检请求：</span><span class="sxs-lookup"><span data-stu-id="a92c1-220">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="a92c1-221">请求方法是 GET、 HEAD 或 POST、 和</span><span class="sxs-lookup"><span data-stu-id="a92c1-221">The request method is GET, HEAD, or POST, and</span></span>

* <span data-ttu-id="a92c1-222">应用程序不会设置以外，接受语言内容 Accept-language、 任何请求标头内容类型或最后一个事件 ID 和</span><span class="sxs-lookup"><span data-stu-id="a92c1-222">The application does not set any request headers other than Accept, Accept-Language, Content-Language, Content-Type, or Last-Event-ID, and</span></span>

* <span data-ttu-id="a92c1-223">内容类型标头 (如果设置) 是以下之一：</span><span class="sxs-lookup"><span data-stu-id="a92c1-223">The Content-Type header (if set) is one of the following:</span></span>

  * <span data-ttu-id="a92c1-224">application/x-www-form-urlencoded</span><span class="sxs-lookup"><span data-stu-id="a92c1-224">application/x-www-form-urlencoded</span></span>

  * <span data-ttu-id="a92c1-225">multipart/窗体的数据</span><span class="sxs-lookup"><span data-stu-id="a92c1-225">multipart/form-data</span></span>

  * <span data-ttu-id="a92c1-226">文本/无格式</span><span class="sxs-lookup"><span data-stu-id="a92c1-226">text/plain</span></span>

<span data-ttu-id="a92c1-227">有关请求标头规则适用于应用程序通过 XMLHttpRequest 对象上调用 setRequestHeader 将设置的标头。</span><span class="sxs-lookup"><span data-stu-id="a92c1-227">The rule about request headers applies to headers that the application sets by calling setRequestHeader on the XMLHttpRequest object.</span></span> <span data-ttu-id="a92c1-228">（CORS 规范调用这些"作者请求标头"。）此规则不适用于浏览器可以设置，如用户代理、 主机或内容-长度标头。</span><span class="sxs-lookup"><span data-stu-id="a92c1-228">(The CORS specification calls these "author request headers".) The rule does not apply to headers the browser can set, such as User-Agent, Host, or Content-Length.</span></span>

<span data-ttu-id="a92c1-229">下面是预检请求的示例：</span><span class="sxs-lookup"><span data-stu-id="a92c1-229">Here is an example of a preflight request:</span></span>

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

<span data-ttu-id="a92c1-230">预检请求使用 HTTP OPTIONS 方法。</span><span class="sxs-lookup"><span data-stu-id="a92c1-230">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="a92c1-231">它包括两个特殊的标头：</span><span class="sxs-lookup"><span data-stu-id="a92c1-231">It includes two special headers:</span></span>

* <span data-ttu-id="a92c1-232">访问控制的请求的方法： 将用作实际请求 HTTP 方法。</span><span class="sxs-lookup"><span data-stu-id="a92c1-232">Access-Control-Request-Method: The HTTP method that will be used for the actual request.</span></span>

* <span data-ttu-id="a92c1-233">访问-控制-请求的标头： 应用程序设置实际请求的请求标头的列表。</span><span class="sxs-lookup"><span data-stu-id="a92c1-233">Access-Control-Request-Headers: A list of request headers that the application set on the actual request.</span></span> <span data-ttu-id="a92c1-234">（同样，这不包括浏览器设置的标头。）</span><span class="sxs-lookup"><span data-stu-id="a92c1-234">(Again, this does not include headers that the browser sets.)</span></span>

<span data-ttu-id="a92c1-235">下面是假定服务器允许请求的示例响应：</span><span class="sxs-lookup"><span data-stu-id="a92c1-235">Here is an example response, assuming that the server allows the request:</span></span>

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

<span data-ttu-id="a92c1-236">响应包括列出了允许的方法的访问-控制的允许的方法标头和 （可选） 访问的控制的允许的标头标头，其中列出了允许的标头。</span><span class="sxs-lookup"><span data-stu-id="a92c1-236">The response includes an Access-Control-Allow-Methods header that lists the allowed methods, and optionally an Access-Control-Allow-Headers header, which lists the allowed headers.</span></span> <span data-ttu-id="a92c1-237">如果预检请求成功，则浏览器发送实际请求，如前面所述。</span><span class="sxs-lookup"><span data-stu-id="a92c1-237">If the preflight request succeeds, the browser sends the actual request, as described earlier.</span></span>
