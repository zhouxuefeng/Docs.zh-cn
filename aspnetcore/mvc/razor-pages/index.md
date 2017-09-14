---
title: "ASP.NET Core 中的 Razor 页面介绍"
author: Rick-Anderson
description: "本文档提供有关使用 ASP.NET Core 中的 Razor 页面轻松开发聚焦于页的方案概述。"
keywords: "ASP.NET Core, Razor 页面"
ms.author: riande
manager: wpickett
ms.date: 09/12/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/razor-pages/index
ms.openlocfilehash: e9678279db85ec03616e693b9772c6ee71c4fef8
ms.sourcegitcommit: d2f705f7a8ef2c1a940f590e4de188621fd48d2a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2017
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="3bfa3-104">ASP.NET Core 中的 Razor 页面介绍</span><span class="sxs-lookup"><span data-stu-id="3bfa3-104">Introduction to Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="3bfa3-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="3bfa3-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="3bfa3-106">Razor 页面是 ASP.NET Core MVC 的一个新功能，它可以使基于页面的编码方式更简单高效。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-106">Razor Pages is a new feature of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="3bfa3-107">若要查找使用模型视图控制器方法的教程，请参阅 [ASP.NET Core MVC 入门](xref:tutorials/first-mvc-app/start-mvc)。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-107">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Getting started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<a name="prerequisites"></a>

## <a name="aspnet-core-20-prerequisites"></a><span data-ttu-id="3bfa3-108">ASP.NET Core 2.0 必备组件</span><span class="sxs-lookup"><span data-stu-id="3bfa3-108">ASP.NET Core 2.0 prerequisites</span></span>

<span data-ttu-id="3bfa3-109">安装 [.NET Core](https://www.microsoft.com/net/core) 2.0.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-109">Install [.NET Core](https://www.microsoft.com/net/core) 2.0.0 or later.</span></span>

<span data-ttu-id="3bfa3-110">如果在使用 Visual Studio，请使用以下工作负载安装 [Visual Studio](https://www.visualstudio.com/vs/) 2017 版本 15.3 或更高版本：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-110">If you're using Visual Studio, install [Visual Studio](https://www.visualstudio.com/vs/) 2017 version 15.3 or later with the following workloads:</span></span>

* <span data-ttu-id="3bfa3-111">**ASP.NET 和 Web 开发**</span><span class="sxs-lookup"><span data-stu-id="3bfa3-111">**ASP.NET and web development**</span></span>
* <span data-ttu-id="3bfa3-112">**.NET Core 跨平台开发**</span><span class="sxs-lookup"><span data-stu-id="3bfa3-112">**.NET Core cross-platform development**</span></span>

<a name="rpvs17"></a>

## <a name="creating-a-razor-pages-project"></a><span data-ttu-id="3bfa3-113">创建 Razor 页面项目</span><span class="sxs-lookup"><span data-stu-id="3bfa3-113">Creating a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3bfa3-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3bfa3-114">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="3bfa3-115">请参阅 [Razor 页面入门](xref:tutorials/razor-pages/razor-pages-start)，获取关于如何使用 Visual Studio 创建 Razor 页面项目的详细说明。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-115">See [Getting started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project using Visual Studio.</span></span>

#   <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3bfa3-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3bfa3-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3bfa3-117">在命令行中运行 `dotnet new razor`。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-117">Run `dotnet new razor` from the command line.</span></span>

<span data-ttu-id="3bfa3-118">在 Visual Studio for Mac 中打开生成的 .csproj 文件。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-118">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3bfa3-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3bfa3-119">Visual Studio Code</span></span>](#tab/visual-studio-code) 

<span data-ttu-id="3bfa3-120">在命令行中运行 `dotnet new razor`。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-120">Run `dotnet new razor` from the command line.</span></span>

#   <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="3bfa3-121">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3bfa3-121">.NET Core CLI</span></span>](#tab/netcore-cli) 

<span data-ttu-id="3bfa3-122">在命令行中运行 `dotnet new razor`。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-122">Run `dotnet new razor` from the command line.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="3bfa3-123">Razor 页面</span><span class="sxs-lookup"><span data-stu-id="3bfa3-123">Razor Pages</span></span>

<span data-ttu-id="3bfa3-124">Startup.cs 中已启用 Razor 页面：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-124">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[main](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="3bfa3-125">请考虑一个基本页面：<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="3bfa3-125">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[main](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="3bfa3-126">上述代码看上去类似于一个 Razor 视图文件。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-126">The preceding code looks a lot like a Razor view file.</span></span> <span data-ttu-id="3bfa3-127">不同之处在于 `@page` 指令。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-127">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="3bfa3-128">`@page` 使文件转换为一个 MVC 操作 ，这意味着它将直接处理请求，而无需通过控制器处理。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-128">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="3bfa3-129">`@page` 必须是页面上的第一个 Razor 指令。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-129">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="3bfa3-130">`@page` 将影响其他 Razor 构造的行为。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-130">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="3bfa3-131">将在以下两个文件中显示使用 `PageModel` 类的类似页面。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-131">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="3bfa3-132">Pages/Index2.cshtml 文件：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-132">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[main](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="3bfa3-133">Pages/Index2.cshtml.cs“代码隐藏”文件：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-133">The *Pages/Index2.cshtml.cs* "code-behind" file:</span></span>

[!code-cs[main](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="3bfa3-134">按照惯例，`PageModel` 类文件的名称与追加 .cs 的 Razor 页面文件名称相同。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-134">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="3bfa3-135">例如，前面的 Razor 页面的名称为 Pages/Index2.cshtml。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-135">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="3bfa3-136">包含 `PageModel` 类的文件的名称为 Pages/Index2.cshtml.cs。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-136">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="3bfa3-137">页面的 URL 路径的关联由页面在文件系统中的位置决定。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-137">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="3bfa3-138">下表显示了 Razor 页面路径及匹配的 URL：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-138">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="3bfa3-139">文件名和路径</span><span class="sxs-lookup"><span data-stu-id="3bfa3-139">File name and path</span></span>               | <span data-ttu-id="3bfa3-140">匹配的 URL</span><span class="sxs-lookup"><span data-stu-id="3bfa3-140">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="3bfa3-141">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3bfa3-141">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="3bfa3-142">`/` 或 `/Index`</span><span class="sxs-lookup"><span data-stu-id="3bfa3-142">`/` or `/Index`</span></span> |
| <span data-ttu-id="3bfa3-143">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3bfa3-143">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="3bfa3-144">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3bfa3-144">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="3bfa3-145">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3bfa3-145">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="3bfa3-146">`/Store` 或 `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="3bfa3-146">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="3bfa3-147">注意：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-147">Notes:</span></span>

* <span data-ttu-id="3bfa3-148">默认情况下，运行时在“页面”文件夹中查找 Razor 页面文件。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-148">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="3bfa3-149">URL 未包含页面时，`Index` 为默认页面。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-149">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="writing-a-basic-form"></a><span data-ttu-id="3bfa3-150">编写基本窗体</span><span class="sxs-lookup"><span data-stu-id="3bfa3-150">Writing a basic form</span></span>

<span data-ttu-id="3bfa3-151">Razor 页面功能旨在简化 Web 浏览器常用的模式。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-151">Razor Pages features are designed to make common patterns used with web browsers easy.</span></span> <span data-ttu-id="3bfa3-152">[模型绑定](xref:mvc/models/model-binding)、[标记帮助程序](xref:mvc/views/tag-helpers/intro)和 HTML 帮助程序均只可用于 Razor 页面类中定义的属性。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-152">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="3bfa3-153">请参考为 `Contact` 模型实现基本的“联系我们”窗体的页面：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-153">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="3bfa3-154">在本文档中的示例中，`DbContext` 在 [Startup.cs](https://github.com/aspnet/Docs/blob/master/aspnetcore/mvc/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) 文件中进行初始化。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-154">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/aspnet/Docs/blob/master/aspnetcore/mvc/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-cs[main](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="3bfa3-155">数据模型：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-155">The data model:</span></span>

[!code-cs[main](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="3bfa3-156">Pages/Create.cshtml 视图文件：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-156">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="3bfa3-157">Pages/Create.cshtml.cs 代码隐藏视图文件：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-157">The *Pages/Create.cshtml.cs* code-behind file for the view:</span></span>

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="3bfa3-158">按照惯例，`PageModel` 类称为 `<PageName>Model`并且它与页面位于同一个命名空间中。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-158">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="3bfa3-159">使用 `PageModel` 代码隐藏文件支持单元测试，但是需要你编写显式构造函数和类。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-159">Using a `PageModel` code-behind file supports unit testing, but requires you to write an explicit constructor and class.</span></span> <span data-ttu-id="3bfa3-160">未使用 `PageModel` 代码隐藏文件的页面支持运行时编译，这在开发过程中可以作为一种优势。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-160">Pages without `PageModel` code-behind files support runtime compilation, which can be an advantage in development.</span></span>  <!-- review: advantage because you can make changes and refresh the browser without explicitly compiling the app -->

<span data-ttu-id="3bfa3-161">页面包含 `OnPostAsync` 处理程序方法，它在 `POST` 请求上运行（当用户发布窗体时）。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-161">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="3bfa3-162">可以为任何 HTTP 谓词添加处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-162">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="3bfa3-163">最常见的处理程序是：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-163">The most common handlers are:</span></span>

* <span data-ttu-id="3bfa3-164">`OnGet`，用于初始化页面所需的状态。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-164">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="3bfa3-165">[OnGet](#OnGet) 示例。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-165">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="3bfa3-166">`OnPost`，用于处理窗体提交。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-166">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="3bfa3-167">`Async` 命名后缀为可选，但是按照惯例通常会将它用于异步函数。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-167">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="3bfa3-168">前面示例中的 `OnPostAsync` 代码看上去与通常在控制器中编写的内容相似。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-168">The `OnPostAsync` code in the preceding example looks similar to what you would normally write in a controller.</span></span> <span data-ttu-id="3bfa3-169">前面的代码通常用于 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-169">The preceding code is typical for Razor Pages.</span></span> <span data-ttu-id="3bfa3-170">多数 MVC 基元（例如[模型绑定](xref:mvc/models/model-binding)、[验证](xref:mvc/models/validation)和操作结果）都是共享的。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-170">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results are shared.</span></span>  <!-- Review: Ryan, can we get a list of what is shared and what isn't? -->

<span data-ttu-id="3bfa3-171">之前的 `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-171">The previous `OnPostAsync` method:</span></span>

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="3bfa3-172">`OnPostAsync` 的基本流：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-172">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="3bfa3-173">检查验证错误。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-173">Check for validation errors.</span></span>

*  <span data-ttu-id="3bfa3-174">如果没有错误，则保存数据并重定向。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-174">If there are no errors, save the data and redirect.</span></span>
*  <span data-ttu-id="3bfa3-175">如果有错误，则再次显示页面并附带验证消息。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-175">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="3bfa3-176">客户端验证与传统的 ASP.NET Core MVC 应用程序相同。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-176">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="3bfa3-177">很多情况下，都会在客户端上检测到验证错误，并且从不将它们提交到服务器。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-177">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="3bfa3-178">成功输入数据后，`OnPostAsync` 处理程序方法调用 `RedirectToPage` 帮助程序方法来返回 `RedirectToPageResult` 的实例。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-178">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="3bfa3-179">`RedirectToPage` 是新的操作结果，类似于 `RedirectToAction` 或 `RedirectToRoute`，但是已针对页面进行自定义。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-179">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="3bfa3-180">在前面的示例中，它将重定向到根索引页 (`/Index`)。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-180">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="3bfa3-181">[页面 URL 生成](#url_gen)部分中详细介绍了 `RedirectToPage`。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-181">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="3bfa3-182">提交的窗体存在（已传递到服务器的）验证错误时，`OnPostAsync` 处理程序方法调用 `Page` 帮助程序方法。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-182">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="3bfa3-183">`Page` 返回 `PageResult` 的实例。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-183">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="3bfa3-184">返回 `Page` 的过程与控制器中的操作返回 `View` 的过程相似。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-184">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="3bfa3-185">`PageResult` 是处理程序方法的默认 <!-- Review  --> 返回类型。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-185">`PageResult` is the default <!-- Review  --> return type for a handler method.</span></span> <span data-ttu-id="3bfa3-186">返回 `void` 的处理程序方法将显示页面。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-186">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="3bfa3-187">`Customer` 属性使用 `[BindProperty]` 特性来选择加入模型绑定。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-187">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="3bfa3-188">默认情况下，Razor 页面只绑定带有非 GET 谓词的属性。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-188">Razor Pages, by default, bind properties only with non-GET verbs.</span></span> <span data-ttu-id="3bfa3-189">绑定属性可以减少需要编写的代码量。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-189">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="3bfa3-190">绑定通过使用相同的属性显示窗体字段 (`<input asp-for="Customer.Name" />`) 来减少代码，并接受输入。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-190">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name" />`) and accept the input.</span></span>

<span data-ttu-id="3bfa3-191">主页 (Index.cshtml)：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-191">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="3bfa3-192">Index.cshtml.cs 隐藏文件：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-192">The code behind *Index.cshtml.cs* file:</span></span>

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="3bfa3-193">Index.cshtml 文件包含以下标记来创建每个联系人项的编辑链接：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-193">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

```cshtml
<a asp-page="./Edit" asp-route-id="@contact.Id">edit</a>
```

<span data-ttu-id="3bfa3-194">[定位点标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper) 使用 [asp-route-{value}](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper#route) 属性生成“编辑”页面的链接。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-194">The [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper) used the [asp-route-{value}](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper#route) attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="3bfa3-195">此链接包含路由数据及联系人 ID。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-195">The link contains route data with the contact ID.</span></span> <span data-ttu-id="3bfa3-196">例如 `http://localhost:5000/Edit/1`。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-196">For example, `http://localhost:5000/Edit/1`.</span></span>

<span data-ttu-id="3bfa3-197">Pages/Edit.cshtml 文件：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-197">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="3bfa3-198">第一行包含 `@page "{id:int}"` 指令。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-198">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="3bfa3-199">路由约束 `"{id:int}"` 告诉页面接受包含 `int` 路由数据的页面请求。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-199">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="3bfa3-200">如果页面请求未包含可转换为 `int` 的路由数据，则运行时返回 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-200">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span>

<span data-ttu-id="3bfa3-201">Pages/Edit.cshtml.cs 文件：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-201">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="3bfa3-202">XSRF/CSRF 和 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="3bfa3-202">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="3bfa3-203">无需为[防伪验证](xref:security/anti-request-forgery)编写任何代码。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-203">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="3bfa3-204">Razor 页面自动将防伪标记生成过程和验证过程包含在内。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-204">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>
## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="3bfa3-205">将布局、分区、模板和标记帮助程序用于 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="3bfa3-205">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="3bfa3-206">页面可使用 Razor 视图引擎的所有功能。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-206">Pages work with all the features of the Razor view engine.</span></span> <span data-ttu-id="3bfa3-207">布局、分区、模板、标记帮助程序、_ViewStart.cshtml 和 _ViewImports.cshtml 的工作方式与它们在传统的 Razor 视图中的工作方式相同。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-207">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="3bfa3-208">我们来使用其中的一些功能来整理此页面。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-208">Let's declutter this page by taking advantage of some of those features.</span></span>

<span data-ttu-id="3bfa3-209">向 Pages/_Layout.cshtml 添加[布局页面](xref:mvc/views/layout)：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-209">Add a [layout page](xref:mvc/views/layout) to *Pages/_Layout.cshtml*:</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="3bfa3-210">[布局](xref:mvc/views/layout)：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-210">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="3bfa3-211">控制每个页面的布局（页面选择退出布局时除外）。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-211">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="3bfa3-212">导入 HTML 结构，例如 JavaScript 和样式表。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-212">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="3bfa3-213">请参阅[布局页面](xref:mvc/views/layout)了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-213">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="3bfa3-214">在 Pages/_ViewStart.cshtml 中设置 [Layout](xref:mvc/views/layout#specifying-a-layout) 属性：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-214">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="3bfa3-215">注意：布局位于“页面”文件夹中。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-215">**Note:** The layout is in the *Pages* folder.</span></span> <span data-ttu-id="3bfa3-216">页面按层次结构从当前页面的文件夹开始查找其他视图（布局、模板、分区）。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-216">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="3bfa3-217">可以从“页面”文件夹下的任意 Razor 页面使用“页面”文件夹中的布局。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-217">A layout in the *Pages* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="3bfa3-218">建议不要将布局文件放在“视图/共享”文件夹中。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-218">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="3bfa3-219">视图/共享 是一种 MVC 视图模式。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-219">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="3bfa3-220">Razor 页面旨在依赖文件夹层次结构，而非路径约定。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-220">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="3bfa3-221">Razor 页面中的视图搜索包含“页面”文件夹。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-221">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="3bfa3-222">用于 MVC 控制器和传统 Razor 视图的布局、模板和分区可直接工作。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-222">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="3bfa3-223">添加 Pages/_ViewImports.cshtml 文件：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-223">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="3bfa3-224">本教程的后续部分中将介绍 `@namespace`。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-224">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="3bfa3-225">`@addTagHelper` 指令将[内置标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/Index)引入“页面”文件夹中的所有页面。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-225">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="3bfa3-226">页面上显式使用 `@namespace` 指令后：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-226">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[main](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="3bfa3-227">此指令将为页面设置命名空间。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-227">The directive sets the namespace for the page.</span></span> <span data-ttu-id="3bfa3-228">`@model` 指令无需包含命名空间。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-228">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="3bfa3-229">_ViewImports.cshtml 中包含 `@namespace` 指令后，指定的命名空间将为在导入 `@namespace` 指令的页面中生成的命名空间提供前缀。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-229">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="3bfa3-230">生成的命名空间的剩余部分（后缀部分）是包含 _ViewImports.cshtml 的文件夹与包含页面的文件夹之间以点分隔的相对路径。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-230">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="3bfa3-231">例如，代码隐藏文件 Pages/Customers/Edit.cshtml.cs 显式设置命名空间：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-231">For example, the code behind file *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[main](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="3bfa3-232">Pages/_ViewImports.cshtml 文件设置以下命名空间：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-232">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="3bfa3-233">为 Pages/Customers/Edit.cshtml Razor 页面生成的命名空间与代码隐藏文件相同.</span><span class="sxs-lookup"><span data-stu-id="3bfa3-233">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the code behind file.</span></span> <span data-ttu-id="3bfa3-234">已对 `@namespace` 指令进行设计，因此添加到项目的 C# 类和页面生成的代码可直接工作，而无需添加代码隐藏文件的 `@using` 指令。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-234">The `@namespace` directive was designed so the C# classes added to a project and pages-generated code *just work* without having to add an `@using` directive for the code behind file.</span></span>

<span data-ttu-id="3bfa3-235">注意：`@namespace` 也可用于传统的 Razor 视图。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-235">**Note:** `@namespace` also works with conventional Razor views.</span></span>

<span data-ttu-id="3bfa3-236">原始的 Pages/Create.cshtml 视图文件：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-236">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="3bfa3-237">更新后的 Pages/Create.cshtml 视图文件：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-237">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="3bfa3-238">[Razor 页面初学者项目](#rpvs17)包含 Pages/_ValidationScriptsPartial.cshtml，它与客户端验证联合。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-238">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="3bfa3-239">页面的 URL 生成</span><span class="sxs-lookup"><span data-stu-id="3bfa3-239">URL generation for Pages</span></span>

<span data-ttu-id="3bfa3-240">之前显示的 `Create` 页面使用 `RedirectToPage`：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-240">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[main](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="3bfa3-241">应用具有以下文件/文件夹结构：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-241">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="3bfa3-242">/Pages</span><span class="sxs-lookup"><span data-stu-id="3bfa3-242">*/Pages*</span></span>

  * <span data-ttu-id="3bfa3-243">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="3bfa3-243">*Index.cshtml*</span></span>
  * <span data-ttu-id="3bfa3-244">/Customer</span><span class="sxs-lookup"><span data-stu-id="3bfa3-244">*/Customer*</span></span>

    * <span data-ttu-id="3bfa3-245">Create.cshtml</span><span class="sxs-lookup"><span data-stu-id="3bfa3-245">*Create.cshtml*</span></span>
    * <span data-ttu-id="3bfa3-246">Edit.cshtml</span><span class="sxs-lookup"><span data-stu-id="3bfa3-246">*Edit.cshtml*</span></span>
    * <span data-ttu-id="3bfa3-247">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="3bfa3-247">*Index.cshtml*</span></span>

<span data-ttu-id="3bfa3-248">成功后，Pages/Customers/Create.cshtml 和 Pages/Customers/Edit.cshtml 页面将重定向到 Pages/Index.cshtml。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-248">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="3bfa3-249">字符串 `/Index` 是用于访问上一页的 URI 的组成部分。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-249">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="3bfa3-250">可以使用字符串 `/Index` 生成 Pages/Index.cshtml 页面的 URI。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-250">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="3bfa3-251">例如: </span><span class="sxs-lookup"><span data-stu-id="3bfa3-251">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="3bfa3-252">页面名称是从根“/Pages”文件夹到页面的路径（包含前导 `/`，例如 `/Index`）。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-252">The page name is the path to the page from the root */Pages* folder (including a leading `/`, for example `/Index`).</span></span> <span data-ttu-id="3bfa3-253">相较于仅对 URL 硬编码，前面的 URL 生成示例的功能更加强大。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-253">The preceding URL generation samples are much more feature rich than just hardcoding a URL.</span></span> <span data-ttu-id="3bfa3-254">URL 生成使用[路由](xref:mvc/controllers/routing)，并且可以根据目标路径定义路由的方式生成参数并对参数编码。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-254">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="3bfa3-255">页面的 URL 生成支持相对名称。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-255">URL generation for pages supports relative names.</span></span> <span data-ttu-id="3bfa3-256">下表显示了 Pages/Customers/Create.cshtml 中不同的 `RedirectToPage` 参数选择的索引页：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-256">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="3bfa3-257">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="3bfa3-257">RedirectToPage(x)</span></span>| <span data-ttu-id="3bfa3-258">页</span><span class="sxs-lookup"><span data-stu-id="3bfa3-258">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="3bfa3-259">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="3bfa3-259">RedirectToPage("/Index")</span></span> | <span data-ttu-id="3bfa3-260">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="3bfa3-260">*Pages/Index*</span></span> |
| <span data-ttu-id="3bfa3-261">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="3bfa3-261">RedirectToPage("./Index");</span></span> | <span data-ttu-id="3bfa3-262">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="3bfa3-262">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="3bfa3-263">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="3bfa3-263">RedirectToPage("../Index")</span></span> | <span data-ttu-id="3bfa3-264">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="3bfa3-264">*Pages/Index*</span></span> |
| <span data-ttu-id="3bfa3-265">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="3bfa3-265">RedirectToPage("Index")</span></span>  | <span data-ttu-id="3bfa3-266">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="3bfa3-266">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="3bfa3-267">`RedirectToPage("Index")`、`RedirectToPage("./Index")` 和 `RedirectToPage("../Index")` 是相对名称。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-267">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="3bfa3-268">结合 `RedirectToPage` 参数与当前页的路径来计算目标页面的名称。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-268">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page. -- page name, not page path -->

<span data-ttu-id="3bfa3-269">构建结构复杂的站点时，相对名称链接很有用。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-269">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="3bfa3-270">如果使用相对名称链接文件夹中的页面，则可以重命名该文件夹。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-270">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="3bfa3-271">所有链接仍然有效（因为这些链接未包含此文件夹名称）。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-271">All the links still work (because they didn't include the folder name).</span></span>

## <a name="tempdata"></a><span data-ttu-id="3bfa3-272">TempData</span><span class="sxs-lookup"><span data-stu-id="3bfa3-272">TempData</span></span>

<span data-ttu-id="3bfa3-273">ASP.NET 在[控制器](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller)上公开了 [TempData](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller#Microsoft_AspNetCore_Mvc_Controller_TempData) 属性。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-273">ASP.NET Core exposes the [TempData](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="3bfa3-274">此属性可存储数据，直至数据被读取。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-274">This property stores data until it is read.</span></span> <span data-ttu-id="3bfa3-275">`Keep` 和 `Peek` 方法可用于检查数据，而不执行删除。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-275">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="3bfa3-276">多个请求需要数据时，`TempData` 有助于进行重定向。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-276">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="3bfa3-277">`[TempData]` 是 ASP.NET Core 2.0 中的新属性，在控制器和页面上受支持。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-277">The `[TempData]` attribute is new in ASP.NET Core 2.0 and is supported on controllers and pages.</span></span>

<span data-ttu-id="3bfa3-278">下面的代码使用 `TempData` 设置 `Message` 的值：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-278">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="3bfa3-279">Pages/Customers/Index.cshtml 文件中的以下标记使用 `TempData` 显示 `Message` 的值。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-279">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="3bfa3-280">Pages/Customers/Index.cshtml.cs 代码隐藏文件将 `[TempData]` 属性应用到 `Message` 属性。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-280">The *Pages/Customers/Index.cshtml.cs* code-behind file applies the `[TempData]` attribute to the `Message` property.</span></span>

```cs
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="3bfa3-281">请参阅 [TempData](xref:fundamentals/app-state#temp) 了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-281">See [TempData](xref:fundamentals/app-state#temp) for more information.</span></span>

<a name="mhpp"></a>
## <a name="multiple-handlers-per-page"></a><span data-ttu-id="3bfa3-282">针对一个页面的多个处理程序</span><span class="sxs-lookup"><span data-stu-id="3bfa3-282">Multiple handlers per page</span></span>

<span data-ttu-id="3bfa3-283">以下页面使用 `asp-page-handler` 标记帮助程序为两个页面处理程序生成标记：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-283">The following page generates markup for two page handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there is no `asp-` attribute   -->

<span data-ttu-id="3bfa3-284">前面示例中的窗体包含两个提交按钮，每个提交按钮均使用 `FormActionTagHelper` 提交到不同的 URL。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-284">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="3bfa3-285">`asp-page-handler` 是 `asp-page` 的配套属性。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-285">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="3bfa3-286">`asp-page-handler` 生成提交到页面定义的各个处理程序方法的 URL。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-286">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="3bfa3-287">未指定 `asp-page`，因为示例已链接到当前页面。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-287">`asp-page` is not specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="3bfa3-288">代码隐藏文件：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-288">The code-behind file:</span></span>

[!code-cs[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="3bfa3-289">前面的代码使用已命名处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-289">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="3bfa3-290">已命名处理程序方法通过采用名称中 `On<HTTP Verb>` 之后及 `Async` 之前的文本（如果有）创建。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-290">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="3bfa3-291">在前面的示例中，页面方法是 OnPost**JoinList**Async 和 OnPost**JoinListUC**Async。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-291">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="3bfa3-292">删除 OnPost 和 Async 后，处理程序名称为 `JoinList` 和 `JoinListUC`。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-292">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="3bfa3-293">使用前面的代码时，提交到 `OnPostJoinListAsync` 的 URL 路径为 `http://localhost:5000/Customers/CreateFATH?handler=JoinList`。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-293">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `http://localhost:5000/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="3bfa3-294">提交到 `OnPostJoinListUCAsync` 的 URL 路径为 `http://localhost:5000/Customers/CreateFATH?handler=JoinListUC`。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-294">The URL path that submits to `OnPostJoinListUCAsync` is `http://localhost:5000/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="customizing-routing"></a><span data-ttu-id="3bfa3-295">自定义路由</span><span class="sxs-lookup"><span data-stu-id="3bfa3-295">Customizing Routing</span></span>

<span data-ttu-id="3bfa3-296">如果你不喜欢 URL 中的查询字符串 `?handler=JoinList`，可以更改路由，将处理程序名称放在 URL 的路径部分。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-296">If you don't like the query string `?handler=JoinList` in the URL, you can change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="3bfa3-297">可以通过在 `@page` 指令后面添加使用双引号括起来的路由模板来自定义路由。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-297">You can customize the route by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[main](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="3bfa3-298">前面的路由将处理程序放在了 URL 路径中，而不是查询字符串中。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-298">The preceding route puts the handler name in the URL path instead of the query string.</span></span> <span data-ttu-id="3bfa3-299">`handler` 前面的 `?` 表示路由参数为可选。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-299">The `?` following `handler` means the route parameter is optional.</span></span>

<span data-ttu-id="3bfa3-300">可以使用 `@page` 将其他段和参数添加到页面的路由中。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-300">You can use `@page` to add additional segments and parameters to a page's route.</span></span> <span data-ttu-id="3bfa3-301">其中的任何内容均会被追加到页面的默认路由中。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-301">Whatever's there is **appended** to the default route of the page.</span></span> <span data-ttu-id="3bfa3-302">不支持使用绝对路径或虚拟路径更改页面的路由（例如 `"~/Some/Other/Path"`）。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-302">Using an absolute or virtual path to change the page's route (like `"~/Some/Other/Path"`) is not supported.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="3bfa3-303">配置和设置</span><span class="sxs-lookup"><span data-stu-id="3bfa3-303">Configuration and settings</span></span>

<span data-ttu-id="3bfa3-304">若要配置高级选项，请在 MVC 生成器上使用 `AddRazorPagesOptions` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="3bfa3-304">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-cs[main](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="3bfa3-305">目前，可以使用 `RazorPagesOptions` 设置页面的根目录，或者为页面添加应用程序模型约定。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-305">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="3bfa3-306">我们希望将来可以通过这种方式实现更多扩展功能。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-306">We hope to enable more extensibility this way in the future.</span></span>

<span data-ttu-id="3bfa3-307">若要预编译视图，请参阅 [Razor 视图编译](xref:mvc/views/view-compilation)。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-307">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="3bfa3-308">[下载或查看示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="3bfa3-308">[Download or view sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/razor-pages/index/sample).</span></span>

<span data-ttu-id="3bfa3-309">请参阅 [ASP.NET Core 中的 Razor 页面入门](xref:tutorials/razor-pages/razor-pages-start)，这篇文章以本文为基础编写。</span><span class="sxs-lookup"><span data-stu-id="3bfa3-309">See [Getting started with Razor Pages in ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>