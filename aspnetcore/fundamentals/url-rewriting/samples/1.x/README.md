# <a name="aspnet-core-url-rewriting-sample-aspnet-core-1x"></a><span data-ttu-id="ed025-101">ASP.NET 核心 URL 重写示例 （ASP.NET 核心 1.x）</span><span class="sxs-lookup"><span data-stu-id="ed025-101">ASP.NET Core URL Rewriting Sample (ASP.NET Core 1.x)</span></span>

<span data-ttu-id="ed025-102">本示例演示使用 ASP.NET Core 1.x URL 重写中间件。</span><span class="sxs-lookup"><span data-stu-id="ed025-102">This sample illustrates usage of ASP.NET Core 1.x URL Rewriting Middleware.</span></span> <span data-ttu-id="ed025-103">应用程序演示 URL 重定向和 URL 重写选项。</span><span class="sxs-lookup"><span data-stu-id="ed025-103">The application demonstrates URL redirect and URL rewriting options.</span></span> <span data-ttu-id="ed025-104">有关 ASP.NET 核心 2.x 示例，请参阅[ASP.NET 核心 URL 重写示例 (ASP.NET Core 2.x)](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/2.x)。</span><span class="sxs-lookup"><span data-stu-id="ed025-104">For the ASP.NET Core 2.x sample, see [ASP.NET Core URL Rewriting Sample (ASP.NET Core 2.x)](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/2.x).</span></span>

<span data-ttu-id="ed025-105">运行示例时，响应将提供显示规则之一到请求的 URL 的应用时的重写或重定向 URL。</span><span class="sxs-lookup"><span data-stu-id="ed025-105">When running the sample, a response will be served that shows the rewritten or redirected URL when one of the rules is applied to a request URL.</span></span>

## <a name="examples-in-this-sample"></a><span data-ttu-id="ed025-106">在此示例中的示例</span><span class="sxs-lookup"><span data-stu-id="ed025-106">Examples in this sample</span></span>

* `AddRedirect("redirect-rule/(.*)", "$1")`
  - <span data-ttu-id="ed025-107">成功状态代码： 302 （发现）</span><span class="sxs-lookup"><span data-stu-id="ed025-107">Success status code: 302 (Found)</span></span>
  - <span data-ttu-id="ed025-108">示例 （重定向）： **/redirect-rule / {capture_group}**到**/redirected/ {capture_group}**</span><span class="sxs-lookup"><span data-stu-id="ed025-108">Example (redirect): **/redirect-rule/{capture_group}** to **/redirected/{capture_group}**</span></span>
* `AddRewrite(@"^rewrite-rule/(\d+)/(\d+)", "rewritten?var1=$1&var2=$2", skipRemainingRules: true)`
  - <span data-ttu-id="ed025-109">成功状态代码： 200 （正常）</span><span class="sxs-lookup"><span data-stu-id="ed025-109">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="ed025-110">示例 （重写）： **/rewrite-rule / {capture_group_1} / {capture_group_2}**到**/ 重写？ var1 = {capture_group_1} & var2 = {capture_group_2}**</span><span class="sxs-lookup"><span data-stu-id="ed025-110">Example (rewrite): **/rewrite-rule/{capture_group_1}/{capture_group_2}** to **/rewritten?var1={capture_group_1}&var2={capture_group_2}**</span></span>
* `AddApacheModRewrite(env.ContentRootFileProvider, "ApacheModRewrite.txt")`
  - <span data-ttu-id="ed025-111">成功状态代码： 302 （发现）</span><span class="sxs-lookup"><span data-stu-id="ed025-111">Success status code: 302 (Found)</span></span>
  - <span data-ttu-id="ed025-112">示例 （重定向）： **/apache-mod-rules-redirect / {capture_group}**到**/ 重定向？ id = {capture_group}**</span><span class="sxs-lookup"><span data-stu-id="ed025-112">Example (redirect): **/apache-mod-rules-redirect/{capture_group}** to **/redirected?id={capture_group}**</span></span>
* `AddIISUrlRewrite(env.ContentRootFileProvider, "IISUrlRewrite.xml")`
  - <span data-ttu-id="ed025-113">成功状态代码： 200 （正常）</span><span class="sxs-lookup"><span data-stu-id="ed025-113">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="ed025-114">示例 （重写）： **/iis-rules-rewrite / {capture_group}**到**/ 重写？ id = {capture_group}**</span><span class="sxs-lookup"><span data-stu-id="ed025-114">Example (rewrite): **/iis-rules-rewrite/{capture_group}** to **/rewritten?id={capture_group}**</span></span>
* `Add(RedirectXMLRequests)`
  - <span data-ttu-id="ed025-115">成功状态代码： 301 （永久移动）</span><span class="sxs-lookup"><span data-stu-id="ed025-115">Success status code: 301 (Moved Permanently)</span></span>
  - <span data-ttu-id="ed025-116">示例 （重定向）： **/file.xml**到**/xmlfiles/file.xml**</span><span class="sxs-lookup"><span data-stu-id="ed025-116">Example (redirect): **/file.xml** to **/xmlfiles/file.xml**</span></span>
* `Add(new RedirectPNGRequests(".png", "/png-images")))`<br>`Add(new RedirectPNGRequests(".jpg", "/jpg-images")))`
  - <span data-ttu-id="ed025-117">成功状态代码： 301 （永久移动）</span><span class="sxs-lookup"><span data-stu-id="ed025-117">Success status code: 301 (Moved Permanently)</span></span>
  - <span data-ttu-id="ed025-118">示例 （重定向）： **/image.png**到**/png-images/image.png**</span><span class="sxs-lookup"><span data-stu-id="ed025-118">Example (redirect): **/image.png** to **/png-images/image.png**</span></span>
  - <span data-ttu-id="ed025-119">示例 （重定向）： **/image.jpg**到**/jpg-images/image.jpg**</span><span class="sxs-lookup"><span data-stu-id="ed025-119">Example (redirect): **/image.jpg** to **/jpg-images/image.jpg**</span></span>

## <a name="using-a-physicalfileprovider"></a><span data-ttu-id="ed025-120">使用`PhysicalFileProvider`</span><span class="sxs-lookup"><span data-stu-id="ed025-120">Using a `PhysicalFileProvider`</span></span>
<span data-ttu-id="ed025-121">你还可以获取`IFileProvider`通过创建`PhysicalFileProvider`将传递到`AddApacheModRewrite()`和`AddIISUrlRewrite()`方法：</span><span class="sxs-lookup"><span data-stu-id="ed025-121">You can also obtain an `IFileProvider` by creating a `PhysicalFileProvider` to pass into the `AddApacheModRewrite()` and `AddIISUrlRewrite()` methods:</span></span>
```csharp
using Microsoft.Extensions.FileProviders;
PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
```
## <a name="secure-redirection-extensions"></a><span data-ttu-id="ed025-122">安全重定向扩展</span><span class="sxs-lookup"><span data-stu-id="ed025-122">Secure redirection extensions</span></span>
<span data-ttu-id="ed025-123">此示例包括`WebHostBuilder`配置应用程序以使用 Url (**https://localhost:5001**， **https://localhost**) 和测试证书 (**testCert.pfx**) 以帮助你浏览这些重定向方法。</span><span class="sxs-lookup"><span data-stu-id="ed025-123">This sample includes `WebHostBuilder` configuration for the app to use URLs (**https://localhost:5001**, **https://localhost**) and a test certificate (**testCert.pfx**) to assist you in exploring these redirect methods.</span></span> <span data-ttu-id="ed025-124">添加到其中任何`RewriteOptions()`中**Startup.cs**来研究它们的行为。</span><span class="sxs-lookup"><span data-stu-id="ed025-124">Add any of them to the `RewriteOptions()` in **Startup.cs** to study their behavior.</span></span>

<span data-ttu-id="ed025-125">方法</span><span class="sxs-lookup"><span data-stu-id="ed025-125">Method</span></span> | <span data-ttu-id="ed025-126">状态代码</span><span class="sxs-lookup"><span data-stu-id="ed025-126">Status Code</span></span> | <span data-ttu-id="ed025-127">端口</span><span class="sxs-lookup"><span data-stu-id="ed025-127">Port</span></span>
--- | :---: | :---:
`.AddRedirectToHttpsPermanent()` | <span data-ttu-id="ed025-128">301</span><span class="sxs-lookup"><span data-stu-id="ed025-128">301</span></span> | <span data-ttu-id="ed025-129">null (465)</span><span class="sxs-lookup"><span data-stu-id="ed025-129">null (465)</span></span>
`.AddRedirectToHttps()` | <span data-ttu-id="ed025-130">302</span><span class="sxs-lookup"><span data-stu-id="ed025-130">302</span></span> | <span data-ttu-id="ed025-131">null (465)</span><span class="sxs-lookup"><span data-stu-id="ed025-131">null (465)</span></span>
`.AddRedirectToHttps(301)` | <span data-ttu-id="ed025-132">301</span><span class="sxs-lookup"><span data-stu-id="ed025-132">301</span></span> | <span data-ttu-id="ed025-133">null (465)</span><span class="sxs-lookup"><span data-stu-id="ed025-133">null (465)</span></span>
`.AddRedirectToHttps(301, 5001)` | <span data-ttu-id="ed025-134">301</span><span class="sxs-lookup"><span data-stu-id="ed025-134">301</span></span> | <span data-ttu-id="ed025-135">5001</span><span class="sxs-lookup"><span data-stu-id="ed025-135">5001</span></span>
