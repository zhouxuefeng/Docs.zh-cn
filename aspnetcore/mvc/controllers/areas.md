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
ms.openlocfilehash: 3096d6404ff9c7e34eefcfb1990e7bf1ccab27ba
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2017
---
# <a name="areas"></a>区域

通过[Dhananjay Kumar](https://twitter.com/debug_mode)和[Rick Anderson](https://twitter.com/RickAndMSFT)

区域是 ASP.NET MVC 功能用于为单独的命名空间 （用于路由） 和 （适用于视图） 的文件夹结构组织到组的相关的功能。 使用区域创建层次结构用于通过添加另一个路由参数，路由`area`到`controller`和`action`。

区域提供的方法进行分区到较小功能分组中的大型 ASP.NET 核心 MVC Web 应用。 一个区域实际上是在应用程序内的 MVC 结构。 在 MVC 项目中，逻辑组件，如模型、 控制器和视图保留在不同的文件夹和 MVC 使用命名约定来创建这些组件之间的关系。 对于大型应用，它可能会更有利分区成单独的功能的高级别区域的应用程序。 例如，电子商务应用程序与多个业务单位，例如签出、 计费和搜索等等。这些部门的每个具有其自己的逻辑组件视图、 控制器和模型。 在此方案中，您可以使用区域来以物理方式分区所在的项目中的业务组件。

可以将一个区域定义为较小功能单元在 ASP.NET 核心 MVC 项目中具有自己的控制器、 视图和模型。

请考虑使用区域在 MVC 项目时：

* 你的应用程序由多个应该逻辑上分隔的高级功能组件

* 要进行分区 MVC 项目，以便可以独立从事每个功能区域

区域功能：

* ASP.NET 核心 MVC 应用程序可以有任意数量的区域

* 每个区域具有其自己的控制器、 模型和视图

* 允许你将大型 MVC 项目组织到多个高级组件可以独立处理的

* 支持具有相同名称的多个控制器，只要它们具有不同*区域*

让我们看看示例来演示如何创建和使用区域。 假设你有具有的控制器和视图的两个不同的分组的应用商店应用： 产品和服务。 典型的文件夹结构，使用 MVC 区域类似于下面：

* 项目名称

  * 区域

    * 产品

      * 控制器

        * HomeController.cs

        * ManageController.cs

      * 视图

        * 主页

          * Index.cshtml

        * 管理

          * Index.cshtml

    * 服务

      * 控制器

        * HomeController.cs

      * 视图

        * 主页

          * Index.cshtml

当 MVC 尝试呈现的视图中一个区域后，默认情况下时，它将尝试查找在以下位置：

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
   /Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
   /Views/Shared/<Action-Name>.cshtml
   ```

这些是可以通过更改的默认位置`AreaViewLocationFormats`上`Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions`。

例如，在下面的代码而不是让文件夹名称为区域，它已更改为类别。

```csharp
services.Configure<RazorViewEngineOptions>(options =>
   {
       options.AreaViewLocationFormats.Clear();
       options.AreaViewLocationFormats.Add("/Categories/{2}/Views/{1}/{0}.cshtml");
       options.AreaViewLocationFormats.Add("/Categories/{2}/Views/Shared/{0}.cshtml");
       options.AreaViewLocationFormats.Add("/Views/Shared/{0}.cshtml");
   });
   ```

需要注意的一点是，结构*视图*文件夹是唯一一个被认为是重要此处和文件夹的其余部分的内容类似*控制器*和*模型*未**不**重要。 例如，不需要具有*控制器*和*模型*在所有的文件夹。 这样做的原因的内容*控制器*和*模型*只是代码将获取编译成.dll 文件作为内容位置*视图*之前，请求不是已进行查看。

一旦您已经定义文件夹层次结构，你需要告诉 MVC 每个控制器关联的区域。 执行此操作的修饰控制器名称与`[Area]`属性。

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

设置适用于你新创建的区域的路由定义。 [路由到控制器操作](routing.md)文章将进入有关如何创建 route 定义，包括使用传统的路由，而不是属性的路由的详细信息。 在此示例中，我们将使用的常规路由。 为此，请打开*Startup.cs*文件并修改通过添加`areaRoute`名为路由定义下面。

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

浏览到`http://<yourApp>/products`、`Index`操作方法`HomeController`中`Products`将调用区域。

## <a name="link-generation"></a>链接生成

* 从区域中的操作生成链接基于控制器到同一个控制器中的另一个操作。

  假设当前请求的路径就像是`/Products/Home/Create`

  HtmlHelper 语法：`@Html.ActionLink("Go to Product's Home Page", "Index")`

  TagHelper 语法：`<a asp-action="Index">Go to Product's Home Page</a>`

  请注意，我们不需要提供的区域和控制器，值此处因为它们都已在当前请求的上下文中不可用。 这些类型的值被称为`ambient`值。

* 从区域中的操作生成链接基于到另一项操作的不同控制器上的控制器

  假设当前请求的路径就像是`/Products/Home/Create`

  HtmlHelper 语法：`@Html.ActionLink("Go to Manage Products’  Home Page", "Index", "Manage")`

  TagHelper 语法：`<a asp-controller="Manage" asp-action="Index">Go to Manage Products’  Home Page</a>`

  请注意，此处使用的区域的环境值但上面显式指定 controller 值。

* 从区域中的操作生成的链接可基于另一项操作的控制器的其他控制器和的另一区域。

  假设当前请求的路径就像是`/Products/Home/Create`

  HtmlHelper 语法：`@Html.ActionLink("Go to Services’ Home Page", "Index", "Home", new { area = "Services" })`

  TagHelper 语法：`<a asp-area="Services" asp-controller="Home" asp-action="Index">Go to Services’ Home Page</a>`

  请注意，此处使用没有环境的值。

* 从基于区域控制器中的一个操作的链接生成到不同的控制器上的另一个操作和**不**某一区域中。

  HtmlHelper 语法：`@Html.ActionLink("Go to Manage Products’  Home Page", "Index", "Home", new { area = "" })`

  TagHelper 语法：`<a asp-area="" asp-controller="Manage" asp-action="Index">Go to Manage Products’  Home Page</a>`

  由于我们想要生成指向非区域基于控制器操作，我们空区域此处的环境值。

## <a name="publishing-areas"></a>发布的区域

所有`*.cshtml`和`wwwroot/**`文件将发布输出时`<Project Sdk="Microsoft.NET.Sdk.Web">`纳入*.csproj*文件。
