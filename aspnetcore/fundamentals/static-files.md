---
title: "使用 ASP.NET Core 中的静态文件"
author: rick-anderson
description: "使用静态文件"
keywords: "ASP.NET 核心，静态文件、 静态资产，HTML、 CSS、 JavaScript"
ms.author: riande
manager: wpickett
ms.date: 4/07/2017
ms.topic: article
ms.assetid: e32245c7-4eee-4831-bd2e-915dbf9f5f70
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/static-files
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ea6c180332dd5ab3a7238dcd73a4a1c8534c6243
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="introduction-to-working-with-static-files-in-aspnet-core"></a><span data-ttu-id="ccd86-104">使用 ASP.NET Core 中的静态文件简介</span><span class="sxs-lookup"><span data-stu-id="ccd86-104">Introduction to working with static files in ASP.NET Core</span></span>

<a name=fundamentals-static-files></a>

<span data-ttu-id="ccd86-105">通过[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ccd86-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ccd86-106">静态文件，如 HTML、 CSS、 映像和 JavaScript，作为 ASP.NET Core 应用程序可以提供给客户端直接的资产。</span><span class="sxs-lookup"><span data-stu-id="ccd86-106">Static files, such as HTML, CSS, image, and JavaScript, are assets that an ASP.NET Core app can serve directly to clients.</span></span>

[<span data-ttu-id="ccd86-107">查看或下载示例代码</span><span class="sxs-lookup"><span data-stu-id="ccd86-107">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/static-files/sample)

## <a name="serving-static-files"></a><span data-ttu-id="ccd86-108">为静态文件提供服务</span><span class="sxs-lookup"><span data-stu-id="ccd86-108">Serving static files</span></span>

<span data-ttu-id="ccd86-109">静态文件通常位于`web root`(*\<内容根 > / wwwroot*) 文件夹。</span><span class="sxs-lookup"><span data-stu-id="ccd86-109">Static files are typically located in the `web root` (*\<content-root>/wwwroot*) folder.</span></span> <span data-ttu-id="ccd86-110">请参阅[内容根](xref:fundamentals/index#content-root)和[Web 根](xref:fundamentals/index#web-root)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="ccd86-110">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span> <span data-ttu-id="ccd86-111">通常设置为当前目录的内容的根，以便你的项目的`web root`将开发中找到。</span><span class="sxs-lookup"><span data-stu-id="ccd86-111">You generally set the content root to be the current directory so that your project's `web root` will be found while in development.</span></span>

<span data-ttu-id="ccd86-112">[!code-csharp[Main](../common/samples/WebApplication1/Program.cs?highlight=5&start=12&end=22)]</span><span class="sxs-lookup"><span data-stu-id="ccd86-112">[!code-csharp[Main](../common/samples/WebApplication1/Program.cs?highlight=5&start=12&end=22)]</span></span>

<span data-ttu-id="ccd86-113">可以下任何文件夹中存储静态文件`web root`和访问与该根的相对路径。</span><span class="sxs-lookup"><span data-stu-id="ccd86-113">Static files can be stored in any folder under the `web root` and accessed with a relative path to that root.</span></span> <span data-ttu-id="ccd86-114">例如，当创建默认 Web 应用程序项目中使用 Visual Studio，还有一些文件夹中创建*wwwroot*文件夹- *css*，*映像*，和*js*。</span><span class="sxs-lookup"><span data-stu-id="ccd86-114">For example, when you create a default Web application project using Visual Studio, there are several folders created within the *wwwroot*  folder - *css*, *images*, and *js*.</span></span> <span data-ttu-id="ccd86-115">用于访问中的图像的 URI*映像*子文件夹：</span><span class="sxs-lookup"><span data-stu-id="ccd86-115">The URI to access an image in the *images* subfolder:</span></span>

* `http://<app>/images/<imageFileName>`
* `http://localhost:9189/images/banner3.svg`

<span data-ttu-id="ccd86-116">在静态文件提供的顺序，你必须配置[中间件](middleware.md)将静态文件添加到管道。</span><span class="sxs-lookup"><span data-stu-id="ccd86-116">In order for static files to be served, you must configure the [Middleware](middleware.md) to add static files to the pipeline.</span></span> <span data-ttu-id="ccd86-117">可以通过上添加一个依赖项配置静态文件中间件*Microsoft.AspNetCore.StaticFiles*包到你的项目并调用`UseStaticFiles`扩展方法从`Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="ccd86-117">The static file middleware can be configured by adding a dependency on the *Microsoft.AspNetCore.StaticFiles* package to your project and then calling the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

<span data-ttu-id="ccd86-118">[!code-csharp[Main](../fundamentals/static-files/sample/StartupStaticFiles.cs?highlight=3&name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="ccd86-118">[!code-csharp[Main](../fundamentals/static-files/sample/StartupStaticFiles.cs?highlight=3&name=snippet1)]</span></span>

<span data-ttu-id="ccd86-119">`app.UseStaticFiles();`使中的文件`web root`(*wwwroot*默认情况下) servable。</span><span class="sxs-lookup"><span data-stu-id="ccd86-119">`app.UseStaticFiles();` makes the files in `web root` (*wwwroot* by default) servable.</span></span> <span data-ttu-id="ccd86-120">稍后我将介绍如何使其他目录内容与 servable `UseStaticFiles`。</span><span class="sxs-lookup"><span data-stu-id="ccd86-120">Later I'll show how to make other directory contents servable with `UseStaticFiles`.</span></span>

<span data-ttu-id="ccd86-121">必须包括 NuGet 包"Microsoft.AspNetCore.StaticFiles"。</span><span class="sxs-lookup"><span data-stu-id="ccd86-121">You must include the NuGet package "Microsoft.AspNetCore.StaticFiles".</span></span>

> [!NOTE]
> <span data-ttu-id="ccd86-122">`web root`默认为*wwwroot*目录中，但你可以设置`web root`目录`UseWebRoot`。</span><span class="sxs-lookup"><span data-stu-id="ccd86-122">`web root` defaults to the *wwwroot* directory, but you can set the `web root` directory with `UseWebRoot`.</span></span>

<span data-ttu-id="ccd86-123">假设你有想要提供的静态文件位于项目层次结构`web root`。</span><span class="sxs-lookup"><span data-stu-id="ccd86-123">Suppose you have a project hierarchy where the static files you wish to serve are outside the `web root`.</span></span> <span data-ttu-id="ccd86-124">例如: </span><span class="sxs-lookup"><span data-stu-id="ccd86-124">For example:</span></span>

* <span data-ttu-id="ccd86-125">wwwroot</span><span class="sxs-lookup"><span data-stu-id="ccd86-125">wwwroot</span></span>
  * <span data-ttu-id="ccd86-126">css</span><span class="sxs-lookup"><span data-stu-id="ccd86-126">css</span></span>
  * <span data-ttu-id="ccd86-127">图像</span><span class="sxs-lookup"><span data-stu-id="ccd86-127">images</span></span>
  * <span data-ttu-id="ccd86-128">...</span><span class="sxs-lookup"><span data-stu-id="ccd86-128">...</span></span>
* <span data-ttu-id="ccd86-129">MyStaticFiles</span><span class="sxs-lookup"><span data-stu-id="ccd86-129">MyStaticFiles</span></span>
  * <span data-ttu-id="ccd86-130">test.png</span><span class="sxs-lookup"><span data-stu-id="ccd86-130">test.png</span></span>

<span data-ttu-id="ccd86-131">请求访问*test.png*，配置静态文件中间件，如下所示：</span><span class="sxs-lookup"><span data-stu-id="ccd86-131">For a request to access *test.png*, configure the static files middleware as follows:</span></span>

<span data-ttu-id="ccd86-132">[!code-csharp[Main](../fundamentals/static-files/sample/StartupTwoStaticFiles.cs?highlight=5,6,7,8,9,10&name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="ccd86-132">[!code-csharp[Main](../fundamentals/static-files/sample/StartupTwoStaticFiles.cs?highlight=5,6,7,8,9,10&name=snippet1)]</span></span>

<span data-ttu-id="ccd86-133">对请求`http://<app>/StaticFiles/test.png`将提供*test.png*文件。</span><span class="sxs-lookup"><span data-stu-id="ccd86-133">A request to `http://<app>/StaticFiles/test.png` will serve the *test.png* file.</span></span>

<span data-ttu-id="ccd86-134">`StaticFileOptions()`可以设置响应标头。</span><span class="sxs-lookup"><span data-stu-id="ccd86-134">`StaticFileOptions()` can set response headers.</span></span> <span data-ttu-id="ccd86-135">例如，下面的代码将设置从提供的静态文件*wwwroot*文件夹和集`Cache-Control`标头以使它们为 10 分钟 （600 秒） 公开一个可缓存：</span><span class="sxs-lookup"><span data-stu-id="ccd86-135">For example, the code below sets up static file serving from the *wwwroot* folder and sets the `Cache-Control` header to make them publicly cacheable for 10 minutes (600 seconds):</span></span>

<span data-ttu-id="ccd86-136">[!code-csharp[Main](../fundamentals/static-files/sample/StartupAddHeader.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="ccd86-136">[!code-csharp[Main](../fundamentals/static-files/sample/StartupAddHeader.cs?name=snippet1)]</span></span>

![已添加显示的缓存控制标头的响应标头](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="ccd86-138">静态文件授权</span><span class="sxs-lookup"><span data-stu-id="ccd86-138">Static file authorization</span></span>

<span data-ttu-id="ccd86-139">静态文件模块提供**没有**授权检查。</span><span class="sxs-lookup"><span data-stu-id="ccd86-139">The static file module provides **no** authorization checks.</span></span> <span data-ttu-id="ccd86-140">由它提供任何文件包括正在*wwwroot*可公开访问。</span><span class="sxs-lookup"><span data-stu-id="ccd86-140">Any files served by it, including those under *wwwroot* are publicly available.</span></span> <span data-ttu-id="ccd86-141">若要为文件服务基于授权：</span><span class="sxs-lookup"><span data-stu-id="ccd86-141">To serve files based on authorization:</span></span>

* <span data-ttu-id="ccd86-142">将其外部存储*wwwroot*和访问静态文件中间件任何目录**和**</span><span class="sxs-lookup"><span data-stu-id="ccd86-142">Store them outside of *wwwroot* and any directory accessible to the static file middleware **and**</span></span>

* <span data-ttu-id="ccd86-143">通过返回的控制器操作提供它们`FileResult`应用授权的位置</span><span class="sxs-lookup"><span data-stu-id="ccd86-143">Serve them through a controller action, returning a `FileResult` where authorization is applied</span></span>

## <a name="enabling-directory-browsing"></a><span data-ttu-id="ccd86-144">启用目录浏览</span><span class="sxs-lookup"><span data-stu-id="ccd86-144">Enabling directory browsing</span></span>

<span data-ttu-id="ccd86-145">目录浏览可让你的 web 应用的用户查看的目录和指定的目录中的文件列表。</span><span class="sxs-lookup"><span data-stu-id="ccd86-145">Directory browsing allows the user of your web app to see a list of directories and files within a specified directory.</span></span> <span data-ttu-id="ccd86-146">默认情况下，出于安全原因禁用目录浏览 (请参阅[注意事项](#considerations))。</span><span class="sxs-lookup"><span data-stu-id="ccd86-146">Directory browsing is disabled by default for security reasons (see [Considerations](#considerations)).</span></span> <span data-ttu-id="ccd86-147">若要启用目录浏览，请调用`UseDirectoryBrowser`扩展方法从`Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="ccd86-147">To enable directory browsing, call the `UseDirectoryBrowser` extension method from  `Startup.Configure`:</span></span>

<span data-ttu-id="ccd86-148">[!code-csharp[Main](static-files/sample/StartupBrowse.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="ccd86-148">[!code-csharp[Main](static-files/sample/StartupBrowse.cs?name=snippet1)]</span></span>

<span data-ttu-id="ccd86-149">并添加所需的服务通过调用`AddDirectoryBrowser`扩展方法从`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ccd86-149">And add required services by calling `AddDirectoryBrowser` extension method from `Startup.ConfigureServices`:</span></span>

<span data-ttu-id="ccd86-150">[!code-csharp[Main](static-files/sample/StartupBrowse.cs?name=snippet2)]</span><span class="sxs-lookup"><span data-stu-id="ccd86-150">[!code-csharp[Main](static-files/sample/StartupBrowse.cs?name=snippet2)]</span></span>

<span data-ttu-id="ccd86-151">上面的代码中允许目录浏览的*wwwroot/images*文件夹使用 URL http://\<应用 > / MyImages，以链接到每个文件和文件夹：</span><span class="sxs-lookup"><span data-stu-id="ccd86-151">The code above allows directory browsing of the *wwwroot/images* folder using the URL http://\<app>/MyImages, with links to each file and folder:</span></span>

![目录浏览](static-files/_static/dir-browse.png)

<span data-ttu-id="ccd86-153">请参阅[注意事项](#considerations)上启用浏览时安全风险。</span><span class="sxs-lookup"><span data-stu-id="ccd86-153">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="ccd86-154">请注意两个`app.UseStaticFiles`调用。</span><span class="sxs-lookup"><span data-stu-id="ccd86-154">Note the two `app.UseStaticFiles` calls.</span></span> <span data-ttu-id="ccd86-155">服务的 CSS、 映像和中的 JavaScript 所需的第一个*wwwroot*文件夹，并为目录浏览的第二个调用*wwwroot/images*文件夹使用 URL http://\<应用> / MyImages:</span><span class="sxs-lookup"><span data-stu-id="ccd86-155">The first one is required to serve the CSS, images and JavaScript in the *wwwroot* folder, and the second call for directory browsing of the *wwwroot/images* folder using the URL http://\<app>/MyImages:</span></span>

<span data-ttu-id="ccd86-156">[!code-csharp[Main](static-files/sample/StartupBrowse.cs?highlight=3,5&name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="ccd86-156">[!code-csharp[Main](static-files/sample/StartupBrowse.cs?highlight=3,5&name=snippet1)]</span></span>

## <a name="serving-a-default-document"></a><span data-ttu-id="ccd86-157">为提供服务的默认文档</span><span class="sxs-lookup"><span data-stu-id="ccd86-157">Serving a default document</span></span>

<span data-ttu-id="ccd86-158">设置默认主页提供站点访问者在访问你的站点时的开端。</span><span class="sxs-lookup"><span data-stu-id="ccd86-158">Setting a default home page gives site visitors a place to start when visiting your site.</span></span> <span data-ttu-id="ccd86-159">为了使你的 Web 应用，以便为默认页上，而无需完全限定 URI 用户提供服务，在调用`UseDefaultFiles`扩展方法从`Startup.Configure`，如下所示。</span><span class="sxs-lookup"><span data-stu-id="ccd86-159">In order for your Web app to serve a default page without the user having to fully qualify the URI, call the `UseDefaultFiles` extension method from `Startup.Configure` as follows.</span></span>

<span data-ttu-id="ccd86-160">[!code-csharp[Main](../fundamentals/static-files/sample/StartupEmpty.cs?highlight=3&name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="ccd86-160">[!code-csharp[Main](../fundamentals/static-files/sample/StartupEmpty.cs?highlight=3&name=snippet1)]</span></span>

> [!NOTE]
> <span data-ttu-id="ccd86-161">`UseDefaultFiles`前必须调用`UseStaticFiles`用于默认文件。</span><span class="sxs-lookup"><span data-stu-id="ccd86-161">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="ccd86-162">`UseDefaultFiles`是不实际处理该文件的 URL 重新编写器。</span><span class="sxs-lookup"><span data-stu-id="ccd86-162">`UseDefaultFiles` is a URL re-writer that doesn't actually serve the file.</span></span> <span data-ttu-id="ccd86-163">你必须启用静态文件中间件 (`UseStaticFiles`) 来处理该文件。</span><span class="sxs-lookup"><span data-stu-id="ccd86-163">You must enable the static file middleware (`UseStaticFiles`) to serve the file.</span></span>

<span data-ttu-id="ccd86-164">与`UseDefaultFiles`，将搜索到的文件夹的请求：</span><span class="sxs-lookup"><span data-stu-id="ccd86-164">With `UseDefaultFiles`, requests to a folder will search for:</span></span>

* <span data-ttu-id="ccd86-165">default.htm</span><span class="sxs-lookup"><span data-stu-id="ccd86-165">default.htm</span></span>
* <span data-ttu-id="ccd86-166">default.html</span><span class="sxs-lookup"><span data-stu-id="ccd86-166">default.html</span></span>
* <span data-ttu-id="ccd86-167">index.htm</span><span class="sxs-lookup"><span data-stu-id="ccd86-167">index.htm</span></span>
* <span data-ttu-id="ccd86-168">index.html</span><span class="sxs-lookup"><span data-stu-id="ccd86-168">index.html</span></span>

<span data-ttu-id="ccd86-169">从列表中找到的第一个文件将提供服务，就像该请求是完全限定的 URI （尽管浏览器 URL 将继续显示所请求的 URI）。</span><span class="sxs-lookup"><span data-stu-id="ccd86-169">The first file found from the list will be served as if the request was the fully qualified URI (although the browser URL will continue to show the URI requested).</span></span>

<span data-ttu-id="ccd86-170">下面的代码演示如何更改默认的文件名为*mydefault.html*。</span><span class="sxs-lookup"><span data-stu-id="ccd86-170">The following code shows how to change the default file name to *mydefault.html*.</span></span>

<span data-ttu-id="ccd86-171">[!code-csharp[Main](static-files/sample/StartupDefault.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="ccd86-171">[!code-csharp[Main](static-files/sample/StartupDefault.cs?name=snippet1)]</span></span>

## <a name="usefileserver"></a><span data-ttu-id="ccd86-172">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="ccd86-172">UseFileServer</span></span>

<span data-ttu-id="ccd86-173">`UseFileServer`将功能组合`UseStaticFiles`， `UseDefaultFiles`，和`UseDirectoryBrowser`。</span><span class="sxs-lookup"><span data-stu-id="ccd86-173">`UseFileServer` combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="ccd86-174">下面的代码使静态文件和默认的文件以提供服务，但不允许目录浏览：</span><span class="sxs-lookup"><span data-stu-id="ccd86-174">The following code enables static files and the default file to be served, but does not allow directory browsing:</span></span>

```csharp
app.UseFileServer();
   ```

<span data-ttu-id="ccd86-175">以下代码启用静态文件，默认文件和目录浏览：</span><span class="sxs-lookup"><span data-stu-id="ccd86-175">The following code enables static files, default files and  directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
   ```

<span data-ttu-id="ccd86-176">请参阅[注意事项](#considerations)上启用浏览时安全风险。</span><span class="sxs-lookup"><span data-stu-id="ccd86-176">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span> <span data-ttu-id="ccd86-177">与`UseStaticFiles`， `UseDefaultFiles`，和`UseDirectoryBrowser`，如果你想要提供文件存在于外部`web root`，实例化和配置`FileServerOptions`将作为参数传递的对象`UseFileServer`。</span><span class="sxs-lookup"><span data-stu-id="ccd86-177">As with `UseStaticFiles`, `UseDefaultFiles`, and `UseDirectoryBrowser`, if you wish to serve files that exist outside the `web root`, you instantiate and configure an `FileServerOptions` object that you pass as a parameter to `UseFileServer`.</span></span> <span data-ttu-id="ccd86-178">例如，给定以下目录层次结构中你的 Web 应用：</span><span class="sxs-lookup"><span data-stu-id="ccd86-178">For example, given the following directory hierarchy in your Web app:</span></span>

* <span data-ttu-id="ccd86-179">wwwroot</span><span class="sxs-lookup"><span data-stu-id="ccd86-179">wwwroot</span></span>

  * <span data-ttu-id="ccd86-180">css</span><span class="sxs-lookup"><span data-stu-id="ccd86-180">css</span></span>

  * <span data-ttu-id="ccd86-181">图像</span><span class="sxs-lookup"><span data-stu-id="ccd86-181">images</span></span>

  * <span data-ttu-id="ccd86-182">...</span><span class="sxs-lookup"><span data-stu-id="ccd86-182">...</span></span>

* <span data-ttu-id="ccd86-183">MyStaticFiles</span><span class="sxs-lookup"><span data-stu-id="ccd86-183">MyStaticFiles</span></span>

  * <span data-ttu-id="ccd86-184">test.png</span><span class="sxs-lookup"><span data-stu-id="ccd86-184">test.png</span></span>

  * <span data-ttu-id="ccd86-185">default.html</span><span class="sxs-lookup"><span data-stu-id="ccd86-185">default.html</span></span>

<span data-ttu-id="ccd86-186">使用上面的层次结构示例中，你可能想要启用静态文件、 默认文件和浏览`MyStaticFiles`目录。</span><span class="sxs-lookup"><span data-stu-id="ccd86-186">Using the hierarchy example above, you might want to enable static files, default files, and browsing for the `MyStaticFiles` directory.</span></span> <span data-ttu-id="ccd86-187">在下面的代码段中，这来实现通过单个调用`FileServerOptions`。</span><span class="sxs-lookup"><span data-stu-id="ccd86-187">In the following code snippet, that is accomplished with a single call to `FileServerOptions`.</span></span>

<span data-ttu-id="ccd86-188">[!code-csharp[Main](static-files/sample/StartupUseFileServer.cs?highlight=5,6,7,8,9,10,11&name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="ccd86-188">[!code-csharp[Main](static-files/sample/StartupUseFileServer.cs?highlight=5,6,7,8,9,10,11&name=snippet1)]</span></span>

<span data-ttu-id="ccd86-189">如果`enableDirectoryBrowsing`设置为`true`需要调用`AddDirectoryBrowser`扩展方法从`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ccd86-189">If `enableDirectoryBrowsing` is set to `true` you are required to call `AddDirectoryBrowser` extension method from  `Startup.ConfigureServices`:</span></span>

<span data-ttu-id="ccd86-190">[!code-csharp[Main](static-files/sample/StartupUseFileServer.cs?name=snippet2)]</span><span class="sxs-lookup"><span data-stu-id="ccd86-190">[!code-csharp[Main](static-files/sample/StartupUseFileServer.cs?name=snippet2)]</span></span>

<span data-ttu-id="ccd86-191">使用的文件层次结构和上面的代码：</span><span class="sxs-lookup"><span data-stu-id="ccd86-191">Using the file hierarchy and code above:</span></span>

| <span data-ttu-id="ccd86-192">URI</span><span class="sxs-lookup"><span data-stu-id="ccd86-192">URI</span></span>            |                             <span data-ttu-id="ccd86-193">响应</span><span class="sxs-lookup"><span data-stu-id="ccd86-193">Response</span></span>  |
| ------- | ------|
| `http://<app>/StaticFiles/test.png`    |      <span data-ttu-id="ccd86-194">MyStaticFiles/test.png</span><span class="sxs-lookup"><span data-stu-id="ccd86-194">MyStaticFiles/test.png</span></span> |
| `http://<app>/StaticFiles`              |     <span data-ttu-id="ccd86-195">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="ccd86-195">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="ccd86-196">如果没有默认命名文件位于*MyStaticFiles*目录、 http://\<应用 > / StaticFiles 返回具有可单击链接列出的目录：</span><span class="sxs-lookup"><span data-stu-id="ccd86-196">If no default named files are in the *MyStaticFiles* directory, http://\<app>/StaticFiles returns the directory listing with clickable links:</span></span>

![静态文件列表](static-files/_static/db2.PNG)

> [!NOTE]
> <span data-ttu-id="ccd86-198">`UseDefaultFiles`和`UseDirectoryBrowser`需要 url http://\<应用 > / 不包含尾随斜杠和原因 StaticFiles 客户端将重定向到 http://\<应用 > /StaticFiles/ （添加尾部反斜杠）。</span><span class="sxs-lookup"><span data-stu-id="ccd86-198">`UseDefaultFiles` and `UseDirectoryBrowser` will take the url http://\<app>/StaticFiles without the trailing slash and cause a client side redirect to http://\<app>/StaticFiles/ (adding the trailing slash).</span></span> <span data-ttu-id="ccd86-199">如果没有在文档中的尾随斜杠相对 Url 将是不正确的。</span><span class="sxs-lookup"><span data-stu-id="ccd86-199">Without the trailing slash relative URLs within the documents would be incorrect.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="ccd86-200">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="ccd86-200">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="ccd86-201">`FileExtensionContentTypeProvider`类包含将文件扩展名映射到 MIME 内容类型的集合。</span><span class="sxs-lookup"><span data-stu-id="ccd86-201">The `FileExtensionContentTypeProvider` class contains a  collection that maps file extensions to MIME content types.</span></span> <span data-ttu-id="ccd86-202">在下面的示例中，于已知的 MIME 类型注册多个文件扩展名、".rtf"替换为，并删除".mp4"。</span><span class="sxs-lookup"><span data-stu-id="ccd86-202">In the following sample, several file extensions are registered to known MIME types, the ".rtf" is replaced, and ".mp4" is removed.</span></span>

<span data-ttu-id="ccd86-203">[!code-csharp[Main](../fundamentals/static-files/sample/StartupFileExtensionContentTypeProvider.cs?highlight=3,4,5,6,7,8,9,10,11,12,19&name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="ccd86-203">[!code-csharp[Main](../fundamentals/static-files/sample/StartupFileExtensionContentTypeProvider.cs?highlight=3,4,5,6,7,8,9,10,11,12,19&name=snippet1)]</span></span>

<span data-ttu-id="ccd86-204">请参阅[MIME 内容类型](http://www.iana.org/assignments/media-types/media-types.xhtml)。</span><span class="sxs-lookup"><span data-stu-id="ccd86-204">See   [MIME content types](http://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="ccd86-205">非标准的内容类型</span><span class="sxs-lookup"><span data-stu-id="ccd86-205">Non-standard content types</span></span>

<span data-ttu-id="ccd86-206">ASP.NET 静态文件中间件理解几乎 400 已知的文件内容类型。</span><span class="sxs-lookup"><span data-stu-id="ccd86-206">The ASP.NET static file middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="ccd86-207">如果用户请求了未知的文件类型的文件，静态文件中间件将返回 HTTP 404 （未找到） 响应。</span><span class="sxs-lookup"><span data-stu-id="ccd86-207">If the user requests a file of an unknown file type, the static file middleware returns a HTTP 404 (Not found) response.</span></span> <span data-ttu-id="ccd86-208">如果启用了目录浏览，将显示文件的链接，但的 URI 将返回 HTTP 404 错误。</span><span class="sxs-lookup"><span data-stu-id="ccd86-208">If directory browsing is enabled, a link to the file will be displayed, but the URI will return an HTTP 404 error.</span></span>

<span data-ttu-id="ccd86-209">以下代码启用为未知的类型，并将呈现为图像未知的文件。</span><span class="sxs-lookup"><span data-stu-id="ccd86-209">The following code enables serving unknown types and will render the unknown file as an image.</span></span>

<span data-ttu-id="ccd86-210">[!code-csharp[Main](static-files/sample/StartupServeUnknownFileTypes.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="ccd86-210">[!code-csharp[Main](static-files/sample/StartupServeUnknownFileTypes.cs?name=snippet1)]</span></span>

<span data-ttu-id="ccd86-211">使用上面的代码中，具有未知的内容类型的文件的请求将返回为映像。</span><span class="sxs-lookup"><span data-stu-id="ccd86-211">With the code above, a request for a file with an unknown content type will be returned as an image.</span></span>

>[!WARNING]
> <span data-ttu-id="ccd86-212">启用`ServeUnknownFileTypes`存在安全风险，并使用它阻止这么做。</span><span class="sxs-lookup"><span data-stu-id="ccd86-212">Enabling `ServeUnknownFileTypes` is a security risk and using it is discouraged.</span></span>  <span data-ttu-id="ccd86-213">`FileExtensionContentTypeProvider`（上文所述） 提供一个更安全的替代方法为文件使用非标准扩展提供服务。</span><span class="sxs-lookup"><span data-stu-id="ccd86-213">`FileExtensionContentTypeProvider`  (explained above) provides a safer alternative to serving files with non-standard extensions.</span></span>

### <a name="considerations"></a><span data-ttu-id="ccd86-214">注意事项</span><span class="sxs-lookup"><span data-stu-id="ccd86-214">Considerations</span></span>

>[!WARNING]
> <span data-ttu-id="ccd86-215">`UseDirectoryBrowser`和`UseStaticFiles`可能会泄漏机密。</span><span class="sxs-lookup"><span data-stu-id="ccd86-215">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="ccd86-216">我们建议你**不**启用目录浏览在生产环境中。</span><span class="sxs-lookup"><span data-stu-id="ccd86-216">We recommend that you **not** enable directory browsing in production.</span></span> <span data-ttu-id="ccd86-217">请注意你使用有关哪些目录的启用`UseStaticFiles`或`UseDirectoryBrowser`因为整个目录及其所有子目录将可访问。</span><span class="sxs-lookup"><span data-stu-id="ccd86-217">Be careful about which directories you enable with `UseStaticFiles` or `UseDirectoryBrowser` as the entire directory and all sub-directories will be accessible.</span></span> <span data-ttu-id="ccd86-218">我们建议你如保留其自己的目录中的公共内容*\<内容的根 > / wwwroot*、 离开应用程序视图、 配置文件，等等。</span><span class="sxs-lookup"><span data-stu-id="ccd86-218">We recommend keeping public content in its own directory such as *\<content root>/wwwroot*, away from application views, configuration files, etc.</span></span>

* <span data-ttu-id="ccd86-219">通过公开的内容的 Url`UseDirectoryBrowser`和`UseStaticFiles`受到的区分大小写和其基础文件系统的字符限制。</span><span class="sxs-lookup"><span data-stu-id="ccd86-219">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of their underlying file system.</span></span> <span data-ttu-id="ccd86-220">例如，Windows 不区分大小写，但不是 Mac 和 Linux。</span><span class="sxs-lookup"><span data-stu-id="ccd86-220">For example, Windows is case insensitive, but Mac and Linux are not.</span></span>

* <span data-ttu-id="ccd86-221">在 IIS 中承载的 ASP.NET Core 应用程序使用 ASP.NET 核心模块将所有请求都转发到包括静态文件的请求的应用程序。</span><span class="sxs-lookup"><span data-stu-id="ccd86-221">ASP.NET Core applications hosted in IIS use the ASP.NET Core Module to forward all requests to the application including requests for static files.</span></span> <span data-ttu-id="ccd86-222">因为它不会获取一个机会处理请求，它们均由 ASP.NET 核心模块处理之前未使用 IIS 静态文件处理程序。</span><span class="sxs-lookup"><span data-stu-id="ccd86-222">The IIS static file handler is not used because it doesn't get a chance to handle requests before they are handled by the ASP.NET Core Module.</span></span>

* <span data-ttu-id="ccd86-223">若要删除 IIS 静态文件处理程序 （在服务器或网站级别）：</span><span class="sxs-lookup"><span data-stu-id="ccd86-223">To remove the IIS static file handler (at the server or website level):</span></span>

     * <span data-ttu-id="ccd86-224">导航到**模块**功能</span><span class="sxs-lookup"><span data-stu-id="ccd86-224">Navigate to the **Modules** feature</span></span>

     * <span data-ttu-id="ccd86-225">选择**StaticFileModule**列表中</span><span class="sxs-lookup"><span data-stu-id="ccd86-225">Select **StaticFileModule** in the list</span></span>

     * <span data-ttu-id="ccd86-226">点击**删除**中**操作**侧栏</span><span class="sxs-lookup"><span data-stu-id="ccd86-226">Tap **Remove** in the **Actions** sidebar</span></span>

>[!WARNING]
> <span data-ttu-id="ccd86-227">如果启用了 IIS 静态文件处理程序**和**ASP.NET 核心模块 (ANCM) 未正确配置 (例如如果*web.config*未部署)，将提供静态文件。</span><span class="sxs-lookup"><span data-stu-id="ccd86-227">If the IIS static file handler is enabled **and** the ASP.NET Core Module (ANCM) is not correctly configured (for example if *web.config* was not deployed), static files will be served.</span></span>

* <span data-ttu-id="ccd86-228">代码文件 （包括 c# 和 Razor） 应放置在应用程序项目之外`web root`(*wwwroot*默认情况下)。</span><span class="sxs-lookup"><span data-stu-id="ccd86-228">Code files (including c# and Razor) should be placed outside of the app project's `web root` (*wwwroot* by default).</span></span> <span data-ttu-id="ccd86-229">这将创建应用程序的客户端内容和服务器端源代码，以防止服务器端代码将泄漏之间完全分离。</span><span class="sxs-lookup"><span data-stu-id="ccd86-229">This creates a clean separation between your app's client side content and server side source code, which prevents server side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ccd86-230">其他资源</span><span class="sxs-lookup"><span data-stu-id="ccd86-230">Additional Resources</span></span>

* [<span data-ttu-id="ccd86-231">中间件</span><span class="sxs-lookup"><span data-stu-id="ccd86-231">Middleware</span></span>](middleware.md)

* [<span data-ttu-id="ccd86-232">ASP.NET 核心简介</span><span class="sxs-lookup"><span data-stu-id="ccd86-232">Introduction to ASP.NET Core</span></span>](../index.md)
