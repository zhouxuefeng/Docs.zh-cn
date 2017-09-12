---
title: "视图概述"
author: ardalis
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 668c320d-c050-45e3-8161-2f460dc93b2f
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/overview
ms.openlocfilehash: 3b33c13f2385d3b07ba9b6f0bc0fd560abc3735c
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="rendering-html-with-views-in-aspnet-core-mvc"></a><span data-ttu-id="7892a-103">与 ASP.NET 核心 mvc 视图中呈现的 HTML</span><span class="sxs-lookup"><span data-stu-id="7892a-103">Rendering HTML with views in ASP.NET Core MVC</span></span>

<span data-ttu-id="7892a-104">通过[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="7892a-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="7892a-105">ASP.NET 核心 MVC 控制器可以返回格式化后的结果使用*视图*。</span><span class="sxs-lookup"><span data-stu-id="7892a-105">ASP.NET Core MVC controllers can return formatted results using *views*.</span></span>

## <a name="what-are-views"></a><span data-ttu-id="7892a-106">视图有哪些？</span><span class="sxs-lookup"><span data-stu-id="7892a-106">What are Views?</span></span>

<span data-ttu-id="7892a-107">在模型-视图-控制器 (MVC) 模式下，*视图*封装与应用程序的用户的交互的演示文稿详细信息。</span><span class="sxs-lookup"><span data-stu-id="7892a-107">In the Model-View-Controller (MVC) pattern, the *view* encapsulates the presentation details of the user's interaction with the app.</span></span> <span data-ttu-id="7892a-108">视图是用嵌入的代码生成要发送到客户端内容的 HTML 模板。</span><span class="sxs-lookup"><span data-stu-id="7892a-108">Views are HTML templates with embedded code that generate content to send to the client.</span></span> <span data-ttu-id="7892a-109">视图使用[Razor 语法](razor.md)，这样，代码与 HTML 交互最少的代码或方案。</span><span class="sxs-lookup"><span data-stu-id="7892a-109">Views use [Razor syntax](razor.md), which allows code to interact with HTML with minimal code or ceremony.</span></span>

<span data-ttu-id="7892a-110">ASP.NET 核心 MVC 视图是*.cshtml*文件存储在默认情况下*视图*应用程序中的文件夹。</span><span class="sxs-lookup"><span data-stu-id="7892a-110">ASP.NET Core MVC views are *.cshtml* files stored by default in a *Views* folder within the application.</span></span> <span data-ttu-id="7892a-111">通常情况下，每个控制器将拥有其自己的文件夹，在其中是为特定控制器操作的视图。</span><span class="sxs-lookup"><span data-stu-id="7892a-111">Typically, each controller will have its own folder, in which are views for specific controller actions.</span></span>

![在解决方案资源管理器的视图文件夹](overview/_static/views_solution_explorer.png)

<span data-ttu-id="7892a-113">除了操作特定的视图，[分部视图](partial.md)，[布局和其他特殊视图文件](layout.md)可以使用以帮助减少重复并允许在应用程序的视图内重复使用。</span><span class="sxs-lookup"><span data-stu-id="7892a-113">In addition to action-specific views, [partial views](partial.md), [layouts, and other special view files](layout.md) can be used to help reduce repetition and allow for reuse within the app's views.</span></span>

## <a name="benefits-of-using-views"></a><span data-ttu-id="7892a-114">使用视图的好处</span><span class="sxs-lookup"><span data-stu-id="7892a-114">Benefits of Using Views</span></span>

<span data-ttu-id="7892a-115">视图提供[关注点分离](http://deviq.com/separation-of-concerns/)内封装从业务逻辑中单独的用户界面级别标记的 MVC 应用。</span><span class="sxs-lookup"><span data-stu-id="7892a-115">Views provide [separation of concerns](http://deviq.com/separation-of-concerns/) within an MVC app, encapsulating user interface level markup separately from business logic.</span></span> <span data-ttu-id="7892a-116">ASP.NET MVC 视图使用[Razor 语法](razor.md)以使 HTML 标记和服务器端逻辑之间轻松切换。</span><span class="sxs-lookup"><span data-stu-id="7892a-116">ASP.NET MVC views use [Razor syntax](razor.md) to make switching between HTML markup and server side logic painless.</span></span> <span data-ttu-id="7892a-117">使用的视图之间可以方便地重用的应用程序的用户界面的通用、 重复方面[布局和共享的指令](layout.md)或[分部视图](partial.md)。</span><span class="sxs-lookup"><span data-stu-id="7892a-117">Common, repetitive aspects of the app's user interface can easily be reused between views using [layout and shared directives](layout.md) or [partial views](partial.md).</span></span>

## <a name="creating-a-view"></a><span data-ttu-id="7892a-118">创建视图</span><span class="sxs-lookup"><span data-stu-id="7892a-118">Creating a View</span></span>

<span data-ttu-id="7892a-119">特定于控制器的视图中创建*视图 / [ControllerName]*文件夹。</span><span class="sxs-lookup"><span data-stu-id="7892a-119">Views that are specific to a controller are created in the *Views/[ControllerName]* folder.</span></span> <span data-ttu-id="7892a-120">控制器之间共享的视图都将置于*/视图/共享*文件夹。</span><span class="sxs-lookup"><span data-stu-id="7892a-120">Views that are shared among controllers are placed in the */Views/Shared* folder.</span></span> <span data-ttu-id="7892a-121">其关联的控制器操作，相同命名的视图文件并添加*.cshtml*文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="7892a-121">Name the view file the same as its associated controller action, and add the *.cshtml* file extension.</span></span> <span data-ttu-id="7892a-122">例如，若要创建的视图*有关*操作*主页*控制器，则你将创建*About.cshtml*文件中 * /视图/主页*文件夹。</span><span class="sxs-lookup"><span data-stu-id="7892a-122">For example, to create a view for the *About* action on the *Home* controller, you would create the *About.cshtml* file in the */Views/Home* folder.</span></span>

<span data-ttu-id="7892a-123">示例视图文件 (*About.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="7892a-123">A sample view file (*About.cshtml*):</span></span>

<span data-ttu-id="7892a-124">[!code-html[Main](../../common/samples/WebApplication1/Views/Home/About.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="7892a-124">[!code-html[Main](../../common/samples/WebApplication1/Views/Home/About.cshtml)]</span></span>

<span data-ttu-id="7892a-125">*Razor*由表示代码`@`符号。</span><span class="sxs-lookup"><span data-stu-id="7892a-125">*Razor* code is denoted by the `@` symbol.</span></span> <span data-ttu-id="7892a-126">在 Razor 代码块设置的大括号内运行 C# 语句 (`{` `}`)，例如"关于"分配到`ViewData["Title"]`上面所示的元素。</span><span class="sxs-lookup"><span data-stu-id="7892a-126">C# statements are run within Razor code blocks set off by curly braces (`{` `}`), such as the assignment of "About" to the `ViewData["Title"]` element shown above.</span></span> <span data-ttu-id="7892a-127">Razor 可以用于通过只需使用引用值显示在 HTML 内的值`@`符号，如中所示`<h2>`和`<h3>`上面的元素。</span><span class="sxs-lookup"><span data-stu-id="7892a-127">Razor can be used to display values within HTML by simply referencing the value with the `@` symbol, as shown within the `<h2>` and `<h3>` elements above.</span></span>

<span data-ttu-id="7892a-128">此视图着重于只是输出它所负责的部分。</span><span class="sxs-lookup"><span data-stu-id="7892a-128">This view focuses on just the portion of the output for which it is responsible.</span></span> <span data-ttu-id="7892a-129">在其他地方指定的页面的布局，rest 和视图，其他常见方面。</span><span class="sxs-lookup"><span data-stu-id="7892a-129">The rest of the page's layout, and other common aspects of the view, are specified elsewhere.</span></span> <span data-ttu-id="7892a-130">详细了解[布局和共享的视图逻辑](layout.md)。</span><span class="sxs-lookup"><span data-stu-id="7892a-130">Learn more about [layout and shared view logic](layout.md).</span></span>

## <a name="how-do-controllers-specify-views"></a><span data-ttu-id="7892a-131">如何执行操作控制器指定视图？</span><span class="sxs-lookup"><span data-stu-id="7892a-131">How do Controllers Specify Views?</span></span>

<span data-ttu-id="7892a-132">视图通常从之类的操作返回`ViewResult`。</span><span class="sxs-lookup"><span data-stu-id="7892a-132">Views are typically returned from actions as a `ViewResult`.</span></span> <span data-ttu-id="7892a-133">操作方法可以创建并返回`ViewResult`直接，但更常见如果你的控制器继承自`Controller`，只需将使用`View`作为此示例演示了帮助器方法：</span><span class="sxs-lookup"><span data-stu-id="7892a-133">Your action method can create and return a `ViewResult` directly, but more commonly if your controller inherits from `Controller`, you'll simply use the `View` helper method, as this example demonstrates:</span></span>

<span data-ttu-id="7892a-134">*HomeController.cs*</span><span class="sxs-lookup"><span data-stu-id="7892a-134">*HomeController.cs*</span></span>

<span data-ttu-id="7892a-135">[!code-csharp[Main](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]</span><span class="sxs-lookup"><span data-stu-id="7892a-135">[!code-csharp[Main](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]</span></span>

<span data-ttu-id="7892a-136">`View`帮助器方法具有好几个重载，以返回视图更轻松地进行应用程序开发人员。</span><span class="sxs-lookup"><span data-stu-id="7892a-136">The `View` helper method has several overloads to make returning views easier for app developers.</span></span> <span data-ttu-id="7892a-137">你可以选择指定一个视图以返回，以及要传递到视图的模型对象。</span><span class="sxs-lookup"><span data-stu-id="7892a-137">You can optionally specify a view to return, as well as a model object to pass to the view.</span></span>

<span data-ttu-id="7892a-138">在此操作返回时， *About.cshtml*呈现视图上面所示：</span><span class="sxs-lookup"><span data-stu-id="7892a-138">When this action returns, the *About.cshtml* view shown above is rendered:</span></span>

![有关页面](overview/_static/about-page.png)

### <a name="view-discovery"></a><span data-ttu-id="7892a-140">视图发现</span><span class="sxs-lookup"><span data-stu-id="7892a-140">View Discovery</span></span>

<span data-ttu-id="7892a-141">当操作返回的视图时，捗个称*视图发现*发生。</span><span class="sxs-lookup"><span data-stu-id="7892a-141">When an action returns a view, a process called *view discovery* takes place.</span></span> <span data-ttu-id="7892a-142">此过程确定将使用哪个视图文件。</span><span class="sxs-lookup"><span data-stu-id="7892a-142">This process determines which view file will be used.</span></span> <span data-ttu-id="7892a-143">除非指定了特定视图文件，运行时将查找有关特定于控制器的视图首先，然后查找中的视图名称匹配*共享*文件夹。</span><span class="sxs-lookup"><span data-stu-id="7892a-143">Unless a specific view file is specified, the runtime looks for a controller-specific view first, then looks for matching view name in the *Shared* folder.</span></span>

<span data-ttu-id="7892a-144">当操作返回`View`方法，如下所示`return View();`，操作名称用作视图名称。</span><span class="sxs-lookup"><span data-stu-id="7892a-144">When an action returns the `View` method, like so `return View();`, the action name is used as the view name.</span></span> <span data-ttu-id="7892a-145">例如，如果这从名为"Index"的操作方法调用的则可以等效于传入的"索引"视图名称。</span><span class="sxs-lookup"><span data-stu-id="7892a-145">For example, if this were called from an action method named "Index", it would be equivalent to passing in a view name of "Index".</span></span> <span data-ttu-id="7892a-146">视图名称可以显式传递给方法 (`return View("SomeView");`)。</span><span class="sxs-lookup"><span data-stu-id="7892a-146">A view name can be explicitly passed to the method (`return View("SomeView");`).</span></span> <span data-ttu-id="7892a-147">在这两种情况下，查看发现搜索匹配的视图文件中：</span><span class="sxs-lookup"><span data-stu-id="7892a-147">In both of these cases, view discovery searches for a matching view file in:</span></span>

   1. <span data-ttu-id="7892a-148">视图 /\<ControllerName > /\<ViewName >.cshtml</span><span class="sxs-lookup"><span data-stu-id="7892a-148">Views/\<ControllerName>/\<ViewName>.cshtml</span></span>

   2. <span data-ttu-id="7892a-149">视图/共享/\<ViewName >.cshtml</span><span class="sxs-lookup"><span data-stu-id="7892a-149">Views/Shared/\<ViewName>.cshtml</span></span>

>[!TIP]
> <span data-ttu-id="7892a-150">我们建议以下只返回的约定`View()`从操作，如果可能，因为它会导致更灵活、 更容易重构代码。</span><span class="sxs-lookup"><span data-stu-id="7892a-150">We recommend following the convention of simply returning `View()` from actions when possible, as it results in more flexible, easier to refactor code.</span></span>

<span data-ttu-id="7892a-151">视图文件路径可以提供而不是视图名称。</span><span class="sxs-lookup"><span data-stu-id="7892a-151">A view file path can be provided instead of a view name.</span></span> <span data-ttu-id="7892a-152">如果使用从应用程序根目录开始的绝对路径 (根据需要第一页为"/"或"~ /")，则*.cshtml*扩展必须指定为的文件路径的一部分 (例如， `return View("Views/Home/About.cshtml");`)。</span><span class="sxs-lookup"><span data-stu-id="7892a-152">If using an absolute path starting at the application root (optionally starting with "/" or "~/"), the *.cshtml* extension must be specified as part of the file path (for example, `return View("Views/Home/About.cshtml");`).</span></span> <span data-ttu-id="7892a-153">或者，可以使用中的特定于控制器的目录中的相对路径*视图*目录以指定不同的目录中的视图 (例如，`return View("../Manage/Index");`内`HomeController`)。</span><span class="sxs-lookup"><span data-stu-id="7892a-153">Alternatively, you can use a relative path from the controller-specific directory within the *Views* directory to specify views in different directories (for example, `return View("../Manage/Index");` inside the `HomeController`).</span></span> <span data-ttu-id="7892a-154">同样，你就可以遍历当前的特定于控制器的目录 (例如， `return View("./About");`)。</span><span class="sxs-lookup"><span data-stu-id="7892a-154">Similarly, you can traverse the current controller-specific directory (for example, `return View("./About");`).</span></span> <span data-ttu-id="7892a-155">请注意，不使用相对路径*.cshtml*扩展。</span><span class="sxs-lookup"><span data-stu-id="7892a-155">Note that relative paths don't use the *.cshtml* extension.</span></span> <span data-ttu-id="7892a-156">如前所述，请按照组织视图以反映控制器、 操作和可维护性和清晰性的视图之间的关系的文件结构的最佳做法。</span><span class="sxs-lookup"><span data-stu-id="7892a-156">As previously mentioned, follow the best practice of organizing the file structure for views to reflect the relationships among controllers, actions, and views for maintainability and clarity.</span></span>

> [!NOTE]
> <span data-ttu-id="7892a-157">[分部视图](partial.md)和[查看组件](view-components.md)使用类似的 （但不是完全相同的） 发现机制。</span><span class="sxs-lookup"><span data-stu-id="7892a-157">[Partial views](partial.md) and [view components](view-components.md) use similar (but not identical) discovery mechanisms.</span></span>

> [!NOTE]
> <span data-ttu-id="7892a-158">你可以自定义有关视图的位置在应用内使用自定义的默认约定`IViewLocationExpander`。</span><span class="sxs-lookup"><span data-stu-id="7892a-158">You can customize the default convention regarding where views are located within the app by using a custom `IViewLocationExpander`.</span></span>

>[!TIP]
> <span data-ttu-id="7892a-159">视图名称可能是区分大小写，具体取决于基础文件系统。</span><span class="sxs-lookup"><span data-stu-id="7892a-159">View names may be case sensitive depending on the underlying file system.</span></span> <span data-ttu-id="7892a-160">用于对操作系统的兼容性，始终区分大小写之间控制器和操作名称和关联的视图文件夹和文件名。</span><span class="sxs-lookup"><span data-stu-id="7892a-160">For compatibility across operating systems, always match case between controller and action names and associated view folders and filenames.</span></span>

## <a name="passing-data-to-views"></a><span data-ttu-id="7892a-161">将数据传递到视图</span><span class="sxs-lookup"><span data-stu-id="7892a-161">Passing Data to Views</span></span>

<span data-ttu-id="7892a-162">可以将数据传递给使用几种机制的视图。</span><span class="sxs-lookup"><span data-stu-id="7892a-162">You can pass data to views using several mechanisms.</span></span> <span data-ttu-id="7892a-163">最可靠方法是指定*模型*视图中的类型 (通常称为*viewmodel*，以区别于业务域模型类型)，然后将此类型的实例传递到视图从操作。</span><span class="sxs-lookup"><span data-stu-id="7892a-163">The most robust approach is to specify a *model* type in the view (commonly referred to as a *viewmodel*, to distinguish it from business domain model types), and then pass an instance of this type to the view from the action.</span></span> <span data-ttu-id="7892a-164">我们建议你使用模型或视图模型将数据传递给视图。</span><span class="sxs-lookup"><span data-stu-id="7892a-164">We recommend you use a model or view model to pass data to a view.</span></span> <span data-ttu-id="7892a-165">这允许视图以充分利用强类型检查。</span><span class="sxs-lookup"><span data-stu-id="7892a-165">This allows the view to take advantage of strong type checking.</span></span> <span data-ttu-id="7892a-166">你可以指定视图使用的模型`@model`指令：</span><span class="sxs-lookup"><span data-stu-id="7892a-166">You can specify a model for a view using the `@model` directive:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [1]}} -->

```html
@model WebApplication1.ViewModels.Address
   <h2>Contact</h2>
   <address>
       @Model.Street<br />
       @Model.City, @Model.State @Model.PostalCode<br />
       <abbr title="Phone">P:</abbr>
       425.555.0100
   </address>
   ```

<span data-ttu-id="7892a-167">发送到该视图的实例后为视图指定了一个模型，可以访问在强类型方式使用`@Model`如上所示。</span><span class="sxs-lookup"><span data-stu-id="7892a-167">Once a model has been specified for a view, the instance sent to the view can be accessed in a strongly-typed manner using `@Model` as shown above.</span></span> <span data-ttu-id="7892a-168">若要提供的视图的模型类型实例，控制器将其传递作为参数：</span><span class="sxs-lookup"><span data-stu-id="7892a-168">To provide an instance of the model type to the view, the controller passes it as a parameter:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [13]}} -->

```csharp
public IActionResult Contact()
   {
       ViewData["Message"] = "Your contact page.";

       var viewModel = new Address()
       {
           Name = "Microsoft",
           Street = "One Microsoft Way",
           City = "Redmond",
           State = "WA",
           PostalCode = "98052-6399"
       };
       return View(viewModel);
   }
   ```

<span data-ttu-id="7892a-169">可以提供给作为模型的视图类型没有限制。</span><span class="sxs-lookup"><span data-stu-id="7892a-169">There are no restrictions on the types that can be provided to a view as a model.</span></span> <span data-ttu-id="7892a-170">我们建议传递具有很少或没有行为的普通旧 CLR 对象 (POCO) 查看模型，以便在应用程序可以在其他位置封装业务逻辑。</span><span class="sxs-lookup"><span data-stu-id="7892a-170">We recommend passing Plain Old CLR Object (POCO) view models with little or no behavior, so that business logic can be encapsulated elsewhere in the app.</span></span> <span data-ttu-id="7892a-171">此方法的一个示例是*地址*viewmodel 使用在上面的示例：</span><span class="sxs-lookup"><span data-stu-id="7892a-171">An example of this approach is the *Address* viewmodel used in the example above:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [13]}} -->

```csharp
namespace WebApplication1.ViewModels
   {
       public class Address
       {
           public string Name { get; set; }
           public string Street { get; set; }
           public string City { get; set; }
           public string State { get; set; }
           public string PostalCode { get; set; }
       }
   }
   ```

> [!NOTE]
> <span data-ttu-id="7892a-172">执行任何操作会阻止你为你的业务模型类型和你显示模型类型中使用相同的类。</span><span class="sxs-lookup"><span data-stu-id="7892a-172">Nothing prevents you from using the same classes as your business model types and your display model types.</span></span> <span data-ttu-id="7892a-173">但是，通过将它们分开，允许你从你的域或持久性模型独立变化的视图，可以提供一些的安全优势 (的用户将发送到应用程序使用的模型[模型绑定](../models/model-binding.md))。</span><span class="sxs-lookup"><span data-stu-id="7892a-173">However, keeping them separate allows your views to vary independently from your domain or persistence model, and can offer some security benefits as well (for models that users will send to the app using [model binding](../models/model-binding.md)).</span></span>

### <a name="loosely-typed-data"></a><span data-ttu-id="7892a-174">松散类型化的数据</span><span class="sxs-lookup"><span data-stu-id="7892a-174">Loosely Typed Data</span></span>

<span data-ttu-id="7892a-175">强类型化视图，除了所有视图还都可以对数据松散类型化集合的访问。</span><span class="sxs-lookup"><span data-stu-id="7892a-175">In addition to strongly typed views, all views have access to a loosely typed collection of data.</span></span> <span data-ttu-id="7892a-176">可以通过引用此相同集合`ViewData`或`ViewBag`控制器和视图上的属性。</span><span class="sxs-lookup"><span data-stu-id="7892a-176">This same collection can be referenced through either the `ViewData` or `ViewBag` properties on controllers and views.</span></span> <span data-ttu-id="7892a-177">`ViewBag`属性是周围的包装器`ViewData`，该集合上提供的动态视图。</span><span class="sxs-lookup"><span data-stu-id="7892a-177">The `ViewBag` property is a wrapper around `ViewData` that provides a dynamic view over that collection.</span></span> <span data-ttu-id="7892a-178">它不是一个单独的集合。</span><span class="sxs-lookup"><span data-stu-id="7892a-178">It is not a separate collection.</span></span>

<span data-ttu-id="7892a-179">`ViewData`通过访问的字典对象`string`密钥。</span><span class="sxs-lookup"><span data-stu-id="7892a-179">`ViewData` is a dictionary object accessed through `string` keys.</span></span> <span data-ttu-id="7892a-180">你可以存储和检索对象，并且你将需要将其强制转换为特定类型时将其提取。</span><span class="sxs-lookup"><span data-stu-id="7892a-180">You can store and retrieve objects in it, and you'll need to cast them to a specific type when you extract them.</span></span> <span data-ttu-id="7892a-181">你可以使用`ViewData`以将数据从控制器传递到视图，以及在视图 （和分部视图和布局） 内。</span><span class="sxs-lookup"><span data-stu-id="7892a-181">You can use `ViewData` to pass data from a controller to views, as well as within views (and partial views and layouts).</span></span> <span data-ttu-id="7892a-182">字符串数据可以存储，以及可以直接使用，而无需强制转换。</span><span class="sxs-lookup"><span data-stu-id="7892a-182">String data can be stored and used directly, without the need for a cast.</span></span>

<span data-ttu-id="7892a-183">设置的某些值`ViewData`中某项操作：</span><span class="sxs-lookup"><span data-stu-id="7892a-183">Set some values for `ViewData` in an action:</span></span>

```csharp
public IActionResult SomeAction()
   {
       ViewData["Greeting"] = "Hello";
       ViewData["Address"]  = new Address()
       {
           Name = "Steve",
           Street = "123 Main St",
           City = "Hudson",
           State = "OH",
           PostalCode = "44236"
       };

       return View();
   }
   ```

<span data-ttu-id="7892a-184">处理在视图中的数据：</span><span class="sxs-lookup"><span data-stu-id="7892a-184">Work with the data in a view:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [3, 6]}} -->

```html
@{
       // Requires cast
       var address = ViewData["Address"] as Address;
   }

   @ViewData["Greeting"] World!

   <address>
       @address.Name<br />
       @address.Street<br />
       @address.City, @address.State @address.PostalCode
   </address>
   ```

<span data-ttu-id="7892a-185">`ViewBag`对象提供对存储在对象的动态访问`ViewData`。</span><span class="sxs-lookup"><span data-stu-id="7892a-185">The `ViewBag` objects provides dynamic access to the objects stored in `ViewData`.</span></span> <span data-ttu-id="7892a-186">这可以更便捷地使用，因为它不需要强制转换。</span><span class="sxs-lookup"><span data-stu-id="7892a-186">This can be more convenient to work with, since it doesn't require casting.</span></span> <span data-ttu-id="7892a-187">相同的示例，使用上面`ViewBag`而不是强类型化`address`视图中的实例：</span><span class="sxs-lookup"><span data-stu-id="7892a-187">The same example as above, using `ViewBag` instead of a strongly typed `address` instance in the view:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [1, 4, 5, 6]}} -->

```html
@ViewBag.Greeting World!

   <address>
       @ViewBag.Address.Name<br />
       @ViewBag.Address.Street<br />
       @ViewBag.Address.City, @ViewBag.Address.State @ViewBag.Address.PostalCode
   </address>
   ```

> [!NOTE]
> <span data-ttu-id="7892a-188">由于都是指同一基础`ViewData`集合，您可以混用和命名空间与匹配`ViewData`和`ViewBag`时读取和写入的值，如果方便。</span><span class="sxs-lookup"><span data-stu-id="7892a-188">Since both refer to the same underlying `ViewData` collection, you can mix and match between `ViewData` and `ViewBag` when reading and writing values, if convenient.</span></span>

### <a name="dynamic-views"></a><span data-ttu-id="7892a-189">动态视图</span><span class="sxs-lookup"><span data-stu-id="7892a-189">Dynamic Views</span></span>

<span data-ttu-id="7892a-190">未声明的模型类型，但具有传递给它们的模型实例的视图可以动态引用此实例。</span><span class="sxs-lookup"><span data-stu-id="7892a-190">Views that do not declare a model type but have a model instance passed to them can reference this instance dynamically.</span></span> <span data-ttu-id="7892a-191">例如，如果实例`Address`传递给不声明视图`@model`，视图将仍将能够动态显示引用该实例的属性：</span><span class="sxs-lookup"><span data-stu-id="7892a-191">For example, if an instance of `Address` is passed to a view that doesn't declare an `@model`, the view would still be able to refer to the instance's properties dynamically as shown:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [13, 16, 17, 18]}} -->

```html
<address>
       @Model.Street<br />
       @Model.City, @Model.State @Model.PostalCode<br />
       <abbr title="Phone">P:</abbr>
       425.555.0100
   </address>
   ```

<span data-ttu-id="7892a-192">此功能可以提供一定的灵活性，但不提供任何编译保护或智能感知。</span><span class="sxs-lookup"><span data-stu-id="7892a-192">This feature can offer some flexibility, but does not offer any compilation protection or IntelliSense.</span></span> <span data-ttu-id="7892a-193">如果属性不存在，页面将在运行时失败。</span><span class="sxs-lookup"><span data-stu-id="7892a-193">If the property doesn't exist, the page will fail at runtime.</span></span>

## <a name="more-view-features"></a><span data-ttu-id="7892a-194">详细视图功能</span><span class="sxs-lookup"><span data-stu-id="7892a-194">More View Features</span></span>

<span data-ttu-id="7892a-195">[标记帮助程序](tag-helpers/intro.md)方便地将服务器端行为添加到现有的 HTML 标记，避免需要使用自定义代码或在视图内的帮助器。</span><span class="sxs-lookup"><span data-stu-id="7892a-195">[Tag helpers](tag-helpers/intro.md) make it easy to add server-side behavior to existing HTML tags, avoiding the need to use custom code or helpers within views.</span></span> <span data-ttu-id="7892a-196">标记帮助程序将作为属性应用于 HTML 元素，将忽略不熟悉它们，允许以进行编辑和呈现在不同的工具，可查看标记的编辑器。</span><span class="sxs-lookup"><span data-stu-id="7892a-196">Tag helpers are applied as attributes to HTML elements, which are ignored by editors that aren't familiar with them, allowing view markup to be edited and rendered in a variety of tools.</span></span> <span data-ttu-id="7892a-197">标记帮助器有许多用途，并且特别可以使[使用窗体](working-with-forms.md)容易得多。</span><span class="sxs-lookup"><span data-stu-id="7892a-197">Tag helpers have many uses, and in particular can make [working with forms](working-with-forms.md) much easier.</span></span>

<span data-ttu-id="7892a-198">生成自定义的 HTML 标记可实现的许多内置的 HTML 帮助器，并将更复杂的用户界面逻辑 （可能有其自己数据的要求） 可以封装在[视图组件](view-components.md)。</span><span class="sxs-lookup"><span data-stu-id="7892a-198">Generating custom HTML markup can be achieved with many built-in HTML Helpers, and more complex UI logic (potentially with its own data requirements) can be encapsulated in [View Components](view-components.md).</span></span> <span data-ttu-id="7892a-199">视图组件提供的控制器和视图提供的问题相同分离，可以消除操作和视图使用的常见 UI 元素数据处理的需要。</span><span class="sxs-lookup"><span data-stu-id="7892a-199">View components provide the same separation of concerns that controllers and views offer, and can eliminate the need for actions and views to deal with data used by common UI elements.</span></span>

<span data-ttu-id="7892a-200">ASP.NET 核心的很多其他方面，如视图支持[依赖关系注入](../../fundamentals/dependency-injection.md)，允许服务[注入到视图](dependency-injection.md)。</span><span class="sxs-lookup"><span data-stu-id="7892a-200">Like many other aspects of ASP.NET Core, views support [dependency injection](../../fundamentals/dependency-injection.md), allowing services to be [injected into views](dependency-injection.md).</span></span>
