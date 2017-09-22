---
title: "区域"
author: rick-anderson
description: "演示如何使用区域。"
keywords: "ASP.NET 核心，区域，路由视图"
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: 5e16d5e8-5696-4cb2-8ec7-d36be305c922
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/areas
ms.openlocfilehash: 0f388ba090ada11a0ac7937606cbcd5a89d6263e
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2017
---
# <a name="areas"></a><span data-ttu-id="a15a5-104">区域</span><span class="sxs-lookup"><span data-stu-id="a15a5-104">Areas</span></span>

<span data-ttu-id="a15a5-105">通过[Dhananjay Kumar](https://twitter.com/debug_mode)和[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a15a5-105">By [Dhananjay Kumar](https://twitter.com/debug_mode)  and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a15a5-106">区域是 ASP.NET MVC 功能用于为单独的命名空间 （用于路由） 和 （适用于视图） 的文件夹结构组织到组的相关的功能。</span><span class="sxs-lookup"><span data-stu-id="a15a5-106">Areas are an ASP.NET MVC feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="a15a5-107">使用区域创建层次结构用于通过添加另一个路由参数，路由`area`到`controller`和`action`。</span><span class="sxs-lookup"><span data-stu-id="a15a5-107">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action`.</span></span>

<span data-ttu-id="a15a5-108">区域提供的方法进行分区到较小功能分组中的大型 ASP.NET 核心 MVC Web 应用。</span><span class="sxs-lookup"><span data-stu-id="a15a5-108">Areas provide a way to partition a large ASP.NET Core MVC Web app into smaller functional groupings.</span></span> <span data-ttu-id="a15a5-109">一个区域实际上是在应用程序内的 MVC 结构。</span><span class="sxs-lookup"><span data-stu-id="a15a5-109">An area is effectively an MVC structure inside an application.</span></span> <span data-ttu-id="a15a5-110">在 MVC 项目中，逻辑组件，如模型、 控制器和视图保留在不同的文件夹和 MVC 使用命名约定来创建这些组件之间的关系。</span><span class="sxs-lookup"><span data-stu-id="a15a5-110">In an MVC project, logical components like Model, Controller, and View are kept in different folders, and MVC uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="a15a5-111">对于大型应用，它可能会更有利分区成单独的功能的高级别区域的应用程序。</span><span class="sxs-lookup"><span data-stu-id="a15a5-111">For a large app, it may be advantageous to partition the  app into separate high level areas of functionality.</span></span> <span data-ttu-id="a15a5-112">例如，电子商务应用程序与多个业务单位，例如签出、 计费和搜索等等。这些部门的每个具有其自己的逻辑组件视图、 控制器和模型。</span><span class="sxs-lookup"><span data-stu-id="a15a5-112">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search etc. Each of these units have their own logical component views, controllers, and models.</span></span> <span data-ttu-id="a15a5-113">在此方案中，您可以使用区域来以物理方式分区所在的项目中的业务组件。</span><span class="sxs-lookup"><span data-stu-id="a15a5-113">In this scenario, you can use Areas to physically partition the business components in the same project.</span></span>

<span data-ttu-id="a15a5-114">可以将一个区域定义为较小功能单元在 ASP.NET 核心 MVC 项目中具有自己的控制器、 视图和模型。</span><span class="sxs-lookup"><span data-stu-id="a15a5-114">An area can be defined as smaller functional units in an ASP.NET Core MVC project with its own set of controllers, views, and models.</span></span>

<span data-ttu-id="a15a5-115">请考虑使用区域在 MVC 项目时：</span><span class="sxs-lookup"><span data-stu-id="a15a5-115">Consider using Areas in an MVC project when:</span></span>

* <span data-ttu-id="a15a5-116">你的应用程序由多个应该逻辑上分隔的高级功能组件</span><span class="sxs-lookup"><span data-stu-id="a15a5-116">Your application is made of multiple high-level functional components that should be logically separated</span></span>

* <span data-ttu-id="a15a5-117">要进行分区 MVC 项目，以便可以独立从事每个功能区域</span><span class="sxs-lookup"><span data-stu-id="a15a5-117">You want to partition your MVC project so that each functional area can be worked on independently</span></span>

<span data-ttu-id="a15a5-118">区域功能：</span><span class="sxs-lookup"><span data-stu-id="a15a5-118">Area features:</span></span>

* <span data-ttu-id="a15a5-119">ASP.NET 核心 MVC 应用程序可以有任意数量的区域</span><span class="sxs-lookup"><span data-stu-id="a15a5-119">An ASP.NET Core MVC app can have any number of areas</span></span>

* <span data-ttu-id="a15a5-120">每个区域具有其自己的控制器、 模型和视图</span><span class="sxs-lookup"><span data-stu-id="a15a5-120">Each area has its own controllers, models, and views</span></span>

* <span data-ttu-id="a15a5-121">允许你将大型 MVC 项目组织到多个高级组件可以独立处理的</span><span class="sxs-lookup"><span data-stu-id="a15a5-121">Allows you to organize large MVC projects into multiple high-level components that can be worked on independently</span></span>

* <span data-ttu-id="a15a5-122">支持具有相同名称的多个控制器，只要它们具有不同*区域*</span><span class="sxs-lookup"><span data-stu-id="a15a5-122">Supports multiple controllers with the same name - as long as they have different *areas*</span></span>

<span data-ttu-id="a15a5-123">让我们看看示例来演示如何创建和使用区域。</span><span class="sxs-lookup"><span data-stu-id="a15a5-123">Let's take a look at an example to illustrate how Areas are created and used.</span></span> <span data-ttu-id="a15a5-124">假设你有具有的控制器和视图的两个不同的分组的应用商店应用： 产品和服务。</span><span class="sxs-lookup"><span data-stu-id="a15a5-124">Let's say you have a store app that has two distinct groupings of controllers and views: Products and Services.</span></span> <span data-ttu-id="a15a5-125">典型的文件夹结构，使用 MVC 区域类似于下面：</span><span class="sxs-lookup"><span data-stu-id="a15a5-125">A typical folder structure for that using MVC areas looks like below:</span></span>

* <span data-ttu-id="a15a5-126">项目名称</span><span class="sxs-lookup"><span data-stu-id="a15a5-126">Project name</span></span>

  * <span data-ttu-id="a15a5-127">区域</span><span class="sxs-lookup"><span data-stu-id="a15a5-127">Areas</span></span>

    * <span data-ttu-id="a15a5-128">产品</span><span class="sxs-lookup"><span data-stu-id="a15a5-128">Products</span></span>

      * <span data-ttu-id="a15a5-129">控制器</span><span class="sxs-lookup"><span data-stu-id="a15a5-129">Controllers</span></span>

        * <span data-ttu-id="a15a5-130">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="a15a5-130">HomeController.cs</span></span>

        * <span data-ttu-id="a15a5-131">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="a15a5-131">ManageController.cs</span></span>

      * <span data-ttu-id="a15a5-132">视图</span><span class="sxs-lookup"><span data-stu-id="a15a5-132">Views</span></span>

        * <span data-ttu-id="a15a5-133">主页</span><span class="sxs-lookup"><span data-stu-id="a15a5-133">Home</span></span>

          * <span data-ttu-id="a15a5-134">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="a15a5-134">Index.cshtml</span></span>

        * <span data-ttu-id="a15a5-135">管理</span><span class="sxs-lookup"><span data-stu-id="a15a5-135">Manage</span></span>

          * <span data-ttu-id="a15a5-136">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="a15a5-136">Index.cshtml</span></span>

    * <span data-ttu-id="a15a5-137">服务</span><span class="sxs-lookup"><span data-stu-id="a15a5-137">Services</span></span>

      * <span data-ttu-id="a15a5-138">控制器</span><span class="sxs-lookup"><span data-stu-id="a15a5-138">Controllers</span></span>

        * <span data-ttu-id="a15a5-139">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="a15a5-139">HomeController.cs</span></span>

      * <span data-ttu-id="a15a5-140">视图</span><span class="sxs-lookup"><span data-stu-id="a15a5-140">Views</span></span>

        * <span data-ttu-id="a15a5-141">主页</span><span class="sxs-lookup"><span data-stu-id="a15a5-141">Home</span></span>

          * <span data-ttu-id="a15a5-142">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="a15a5-142">Index.cshtml</span></span>

<span data-ttu-id="a15a5-143">当 MVC 尝试呈现的视图中一个区域后，默认情况下时，它将尝试查找在以下位置：</span><span class="sxs-lookup"><span data-stu-id="a15a5-143">When MVC tries to render a view in an Area, by default, it tries to look in the following locations:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
   /Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
   /Views/Shared/<Action-Name>.cshtml
   ```

<span data-ttu-id="a15a5-144">这些是可以通过更改的默认位置`AreaViewLocationFormats`上`Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions`。</span><span class="sxs-lookup"><span data-stu-id="a15a5-144">These are the default locations which can be changed via the `AreaViewLocationFormats` on the `Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions`.</span></span>

<span data-ttu-id="a15a5-145">例如，在下面的代码而不是让文件夹名称为区域，它已更改为类别。</span><span class="sxs-lookup"><span data-stu-id="a15a5-145">For example, in the below code instead of having the folder name as 'Areas', it has been changed to 'Categories'.</span></span>

```csharp
services.Configure<RazorViewEngineOptions>(options =>
   {
       options.AreaViewLocationFormats.Clear();
       options.AreaViewLocationFormats.Add("/Categories/{2}/Views/{1}/{0}.cshtml");
       options.AreaViewLocationFormats.Add("/Categories/{2}/Views/Shared/{0}.cshtml");
       options.AreaViewLocationFormats.Add("/Views/Shared/{0}.cshtml");
   });
   ```

<span data-ttu-id="a15a5-146">需要注意的一点是，结构*视图*文件夹是唯一一个被认为是重要此处和文件夹的其余部分的内容类似*控制器*和*模型*未**不**重要。</span><span class="sxs-lookup"><span data-stu-id="a15a5-146">One thing to note is that the structure of the *Views* folder is the only one which is considered important here and the content of the rest of the folders like *Controllers* and *Models* does **not** matter.</span></span> <span data-ttu-id="a15a5-147">例如，不需要具有*控制器*和*模型*在所有的文件夹。</span><span class="sxs-lookup"><span data-stu-id="a15a5-147">For example, you need not have a *Controllers* and *Models* folder at all.</span></span> <span data-ttu-id="a15a5-148">这样做的原因的内容*控制器*和*模型*只是代码将获取编译成.dll 文件作为内容位置*视图*之前，请求不是已进行查看。</span><span class="sxs-lookup"><span data-stu-id="a15a5-148">This works because the content of *Controllers* and *Models* is just code which gets compiled into a .dll where as the content of the *Views* is not until a request to that view has been made.</span></span>

<span data-ttu-id="a15a5-149">一旦您已经定义文件夹层次结构，你需要告诉 MVC 每个控制器关联的区域。</span><span class="sxs-lookup"><span data-stu-id="a15a5-149">Once you've defined the folder hierarchy, you need to tell MVC that each controller is associated with an area.</span></span> <span data-ttu-id="a15a5-150">执行此操作的修饰控制器名称与`[Area]`属性。</span><span class="sxs-lookup"><span data-stu-id="a15a5-150">You do that by decorating the controller name with the `[Area]` attribute.</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [4]}} -->

```csharp
...
   namespace MyStore.Areas.Products.Controllers
   {
       [Area("Products")]
       public class HomeController : Controller
       {
           // GET: /Products/Home/Index
           public IActionResult Index()
           {
               return View();
           }

           // GET: /Products/Home/Create
           public IActionResult Create()
           {
               return View();
           }
       }
   }
   ```

<span data-ttu-id="a15a5-151">设置适用于你新创建的区域的路由定义。</span><span class="sxs-lookup"><span data-stu-id="a15a5-151">Set up a route definition that works with your newly created areas.</span></span> <span data-ttu-id="a15a5-152">[路由到控制器操作](routing.md)文章将进入有关如何创建 route 定义，包括使用传统的路由，而不是属性的路由的详细信息。</span><span class="sxs-lookup"><span data-stu-id="a15a5-152">The [Routing to Controller Actions](routing.md) article goes into detail about how to create route definitions, including using conventional routes versus attribute routes.</span></span> <span data-ttu-id="a15a5-153">在此示例中，我们将使用的常规路由。</span><span class="sxs-lookup"><span data-stu-id="a15a5-153">In this example, we'll use a conventional route.</span></span> <span data-ttu-id="a15a5-154">为此，请打开*Startup.cs*文件并修改通过添加`areaRoute`名为路由定义下面。</span><span class="sxs-lookup"><span data-stu-id="a15a5-154">To do so, open the *Startup.cs* file and modify it by adding the `areaRoute` named route definition below.</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [4, 5, 6]}} -->

```csharp
...
   app.UseMvc(routes =>
   {
     routes.MapRoute(name: "areaRoute",
       template: "{area:exists}/{controller=Home}/{action=Index}");

     routes.MapRoute(
         name: "default",
         template: "{controller=Home}/{action=Index}/{id?}");
   });
   ```

<span data-ttu-id="a15a5-155">浏览到`http://<yourApp>/products`、`Index`操作方法`HomeController`中`Products`将调用区域。</span><span class="sxs-lookup"><span data-stu-id="a15a5-155">Browsing to `http://<yourApp>/products`, the `Index` action method of the `HomeController` in the `Products` area will be invoked.</span></span>

## <a name="link-generation"></a><span data-ttu-id="a15a5-156">链接生成</span><span class="sxs-lookup"><span data-stu-id="a15a5-156">Link Generation</span></span>

* <span data-ttu-id="a15a5-157">从区域中的操作生成链接基于控制器到同一个控制器中的另一个操作。</span><span class="sxs-lookup"><span data-stu-id="a15a5-157">Generating links from an action within an area based controller to another action within the same controller.</span></span>

  <span data-ttu-id="a15a5-158">假设当前请求的路径就像是`/Products/Home/Create`</span><span class="sxs-lookup"><span data-stu-id="a15a5-158">Let's say the current request's path is like `/Products/Home/Create`</span></span>

  <span data-ttu-id="a15a5-159">HtmlHelper 语法：`@Html.ActionLink("Go to Product's Home Page", "Index")`</span><span class="sxs-lookup"><span data-stu-id="a15a5-159">HtmlHelper syntax: `@Html.ActionLink("Go to Product's Home Page", "Index")`</span></span>

  <span data-ttu-id="a15a5-160">TagHelper 语法：`<a asp-action="Index">Go to Product's Home Page</a>`</span><span class="sxs-lookup"><span data-stu-id="a15a5-160">TagHelper syntax: `<a asp-action="Index">Go to Product's Home Page</a>`</span></span>

  <span data-ttu-id="a15a5-161">请注意，我们不需要提供的区域和控制器，值此处因为它们都已在当前请求的上下文中不可用。</span><span class="sxs-lookup"><span data-stu-id="a15a5-161">Note that we need not supply the 'area' and 'controller' values here as they are already available in the context of the current request.</span></span> <span data-ttu-id="a15a5-162">这些类型的值被称为`ambient`值。</span><span class="sxs-lookup"><span data-stu-id="a15a5-162">These kind of values are called `ambient` values.</span></span>

* <span data-ttu-id="a15a5-163">从区域中的操作生成链接基于到另一项操作的不同控制器上的控制器</span><span class="sxs-lookup"><span data-stu-id="a15a5-163">Generating links from an action within an area based controller to another action on a different controller</span></span>

  <span data-ttu-id="a15a5-164">假设当前请求的路径就像是`/Products/Home/Create`</span><span class="sxs-lookup"><span data-stu-id="a15a5-164">Let's say the current request's path is like `/Products/Home/Create`</span></span>

  <span data-ttu-id="a15a5-165">HtmlHelper 语法：`@Html.ActionLink("Go to Manage Products’  Home Page", "Index", "Manage")`</span><span class="sxs-lookup"><span data-stu-id="a15a5-165">HtmlHelper syntax: `@Html.ActionLink("Go to Manage Products’  Home Page", "Index", "Manage")`</span></span>

  <span data-ttu-id="a15a5-166">TagHelper 语法：`<a asp-controller="Manage" asp-action="Index">Go to Manage Products’  Home Page</a>`</span><span class="sxs-lookup"><span data-stu-id="a15a5-166">TagHelper syntax: `<a asp-controller="Manage" asp-action="Index">Go to Manage Products’  Home Page</a>`</span></span>

  <span data-ttu-id="a15a5-167">请注意，此处使用的区域的环境值但上面显式指定 controller 值。</span><span class="sxs-lookup"><span data-stu-id="a15a5-167">Note that here the ambient value of an 'area' is used but the 'controller' value is specified explicitly above.</span></span>

* <span data-ttu-id="a15a5-168">从区域中的操作生成的链接可基于另一项操作的控制器的其他控制器和的另一区域。</span><span class="sxs-lookup"><span data-stu-id="a15a5-168">Generating links from an action within an area based controller to another action on a different controller and a different area.</span></span>

  <span data-ttu-id="a15a5-169">假设当前请求的路径就像是`/Products/Home/Create`</span><span class="sxs-lookup"><span data-stu-id="a15a5-169">Let's say the current request's path is like `/Products/Home/Create`</span></span>

  <span data-ttu-id="a15a5-170">HtmlHelper 语法：`@Html.ActionLink("Go to Services’ Home Page", "Index", "Home", new { area = "Services" })`</span><span class="sxs-lookup"><span data-stu-id="a15a5-170">HtmlHelper syntax: `@Html.ActionLink("Go to Services’ Home Page", "Index", "Home", new { area = "Services" })`</span></span>

  <span data-ttu-id="a15a5-171">TagHelper 语法：`<a asp-area="Services" asp-controller="Home" asp-action="Index">Go to Services’ Home Page</a>`</span><span class="sxs-lookup"><span data-stu-id="a15a5-171">TagHelper syntax: `<a asp-area="Services" asp-controller="Home" asp-action="Index">Go to Services’ Home Page</a>`</span></span>

  <span data-ttu-id="a15a5-172">请注意，此处使用没有环境的值。</span><span class="sxs-lookup"><span data-stu-id="a15a5-172">Note that here no ambient values are used.</span></span>

* <span data-ttu-id="a15a5-173">从基于区域控制器中的一个操作的链接生成到不同的控制器上的另一个操作和**不**某一区域中。</span><span class="sxs-lookup"><span data-stu-id="a15a5-173">Generating links from an action within an area based controller to another action on a different controller and **not** in an area.</span></span>

  <span data-ttu-id="a15a5-174">HtmlHelper 语法：`@Html.ActionLink("Go to Manage Products’  Home Page", "Index", "Home", new { area = "" })`</span><span class="sxs-lookup"><span data-stu-id="a15a5-174">HtmlHelper syntax: `@Html.ActionLink("Go to Manage Products’  Home Page", "Index", "Home", new { area = "" })`</span></span>

  <span data-ttu-id="a15a5-175">TagHelper 语法：`<a asp-area="" asp-controller="Manage" asp-action="Index">Go to Manage Products’  Home Page</a>`</span><span class="sxs-lookup"><span data-stu-id="a15a5-175">TagHelper syntax: `<a asp-area="" asp-controller="Manage" asp-action="Index">Go to Manage Products’  Home Page</a>`</span></span>

  <span data-ttu-id="a15a5-176">由于我们想要生成指向非区域基于控制器操作，我们空区域此处的环境值。</span><span class="sxs-lookup"><span data-stu-id="a15a5-176">Since we want to generate links to a non-area based controller action, we empty the ambient value for 'area' here.</span></span>

## <a name="publishing-areas"></a><span data-ttu-id="a15a5-177">发布的区域</span><span class="sxs-lookup"><span data-stu-id="a15a5-177">Publishing Areas</span></span>

<span data-ttu-id="a15a5-178">所有`*.cshtml`和`wwwroot/**`文件将发布输出时`<Project Sdk="Microsoft.NET.Sdk.Web">`纳入*.csproj*文件。</span><span class="sxs-lookup"><span data-stu-id="a15a5-178">All `*.cshtml` and `wwwroot/**` files are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the *.csproj* file.</span></span>
