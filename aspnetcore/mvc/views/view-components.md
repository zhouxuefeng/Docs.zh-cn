---
title: "查看组件"
author: rick-anderson
description: "查看组件旨在任意位置必须可重用呈现逻辑。"
keywords: "ASP.NET 核心，视图组件分部视图"
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: ab4705b7-59d7-4f31-bc97-ea7f292fe926
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/view-components
ms.openlocfilehash: 3bc6d3f85d8ea7fb9b72b18cfd9c5ec2d07293b0
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2017
---
# <a name="view-components"></a><span data-ttu-id="03f58-104">查看组件</span><span class="sxs-lookup"><span data-stu-id="03f58-104">View components</span></span>

<span data-ttu-id="03f58-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="03f58-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="03f58-106">[查看或下载的示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/view-components/sample)([如何下载](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="03f58-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

## <a name="introducing-view-components"></a><span data-ttu-id="03f58-107">查看组件简介</span><span class="sxs-lookup"><span data-stu-id="03f58-107">Introducing view components</span></span>

<span data-ttu-id="03f58-108">新到 ASP.NET 核心 MVC 视图组件是类似于分部视图，但它们可以强大得多。</span><span class="sxs-lookup"><span data-stu-id="03f58-108">New to ASP.NET Core MVC, view components are similar to partial views, but they are much more powerful.</span></span> <span data-ttu-id="03f58-109">查看组件不使用模型绑定，并仅依赖于在调用到它时提供的数据。</span><span class="sxs-lookup"><span data-stu-id="03f58-109">View components don’t use model binding, and only depend on the data you provide when calling into it.</span></span> <span data-ttu-id="03f58-110">视图组件：</span><span class="sxs-lookup"><span data-stu-id="03f58-110">A view component:</span></span>

* <span data-ttu-id="03f58-111">呈现一个块，而不是整个响应</span><span class="sxs-lookup"><span data-stu-id="03f58-111">Renders a chunk rather than a whole response</span></span>
* <span data-ttu-id="03f58-112">包括的相同问题分离和控制器和视图之间找到的可测试性优势</span><span class="sxs-lookup"><span data-stu-id="03f58-112">Includes the same separation-of-concerns and testability benefits found between a controller and view</span></span>
* <span data-ttu-id="03f58-113">可以具有参数和业务逻辑</span><span class="sxs-lookup"><span data-stu-id="03f58-113">Can have parameters and business logic</span></span>
* <span data-ttu-id="03f58-114">从布局页通常会对其进行调用</span><span class="sxs-lookup"><span data-stu-id="03f58-114">Is typically invoked from a layout page</span></span>

<span data-ttu-id="03f58-115">查看组件旨在任意位置具有太过复杂，分部视图，如的可重用呈现逻辑：</span><span class="sxs-lookup"><span data-stu-id="03f58-115">View components are intended anywhere you have reusable rendering logic that is too complex for a partial view, such as:</span></span>

* <span data-ttu-id="03f58-116">动态导航菜单</span><span class="sxs-lookup"><span data-stu-id="03f58-116">Dynamic navigation menus</span></span>
* <span data-ttu-id="03f58-117">标记云 （其中查询数据库）</span><span class="sxs-lookup"><span data-stu-id="03f58-117">Tag cloud (where it queries the database)</span></span>
* <span data-ttu-id="03f58-118">登录面板</span><span class="sxs-lookup"><span data-stu-id="03f58-118">Login panel</span></span>
* <span data-ttu-id="03f58-119">购物车</span><span class="sxs-lookup"><span data-stu-id="03f58-119">Shopping cart</span></span>
* <span data-ttu-id="03f58-120">最近发布的文章</span><span class="sxs-lookup"><span data-stu-id="03f58-120">Recently published articles</span></span>
* <span data-ttu-id="03f58-121">典型的博客上的侧栏内容</span><span class="sxs-lookup"><span data-stu-id="03f58-121">Sidebar content on a typical blog</span></span>
* <span data-ttu-id="03f58-122">一个登录名面板，用于将每一页上呈现和显示在任一链接注销或登录，具体取决于日志中的用户状态</span><span class="sxs-lookup"><span data-stu-id="03f58-122">A login panel that would be rendered on every page and show either the links to log out or log in, depending on the log in state of the user</span></span>

<span data-ttu-id="03f58-123">视图组件由两部分组成： 类 (通常派生自[ViewComponent](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.viewcomponent)) 并在结果返回 （通常视图）。</span><span class="sxs-lookup"><span data-stu-id="03f58-123">A view component consists of two parts: the class (typically derived from [ViewComponent](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.viewcomponent)) and the result it returns (typically a view).</span></span> <span data-ttu-id="03f58-124">与控制器，一样视图组件可以是 POCO，但大多数开发人员将想要的方法和属性可用来利用派生自`ViewComponent`。</span><span class="sxs-lookup"><span data-stu-id="03f58-124">Like controllers, a view component can be a POCO, but most developers will want to take advantage of the methods and properties available by deriving from `ViewComponent`.</span></span>

## <a name="creating-a-view-component"></a><span data-ttu-id="03f58-125">创建视图组件</span><span class="sxs-lookup"><span data-stu-id="03f58-125">Creating a view component</span></span>

<span data-ttu-id="03f58-126">本部分包含创建视图组件的高级别要求。</span><span class="sxs-lookup"><span data-stu-id="03f58-126">This section contains the high-level requirements to create a view component.</span></span> <span data-ttu-id="03f58-127">本文后面的部分，我们将检查每个步骤中详细信息，并创建视图组件。</span><span class="sxs-lookup"><span data-stu-id="03f58-127">Later in the article, we'll examine each step in detail and create a view component.</span></span>

### <a name="the-view-component-class"></a><span data-ttu-id="03f58-128">视图组件类</span><span class="sxs-lookup"><span data-stu-id="03f58-128">The view component class</span></span>

<span data-ttu-id="03f58-129">可以通过以下任一创建视图组件类：</span><span class="sxs-lookup"><span data-stu-id="03f58-129">A view component class can be created by any of the following:</span></span>

* <span data-ttu-id="03f58-130">派生自*ViewComponent*</span><span class="sxs-lookup"><span data-stu-id="03f58-130">Deriving from *ViewComponent*</span></span>
* <span data-ttu-id="03f58-131">修饰具有的类`[ViewComponent]`属性，或从具有的类派生`[ViewComponent]`属性</span><span class="sxs-lookup"><span data-stu-id="03f58-131">Decorating a class with the `[ViewComponent]` attribute, or deriving from a class with the `[ViewComponent]` attribute</span></span>
* <span data-ttu-id="03f58-132">创建的类名称与后缀的结束位置*ViewComponent*</span><span class="sxs-lookup"><span data-stu-id="03f58-132">Creating a class where the name ends with the suffix *ViewComponent*</span></span>

<span data-ttu-id="03f58-133">与控制器，一样查看组件必须是公共的、 有非嵌套的和非抽象类。</span><span class="sxs-lookup"><span data-stu-id="03f58-133">Like controllers, view components must be public, non-nested, and non-abstract classes.</span></span> <span data-ttu-id="03f58-134">视图组件名称是删除了"ViewComponent"后缀的类名称。</span><span class="sxs-lookup"><span data-stu-id="03f58-134">The view component name is the class name with the "ViewComponent" suffix removed.</span></span> <span data-ttu-id="03f58-135">它还可以显式指定使用`ViewComponentAttribute.Name`属性。</span><span class="sxs-lookup"><span data-stu-id="03f58-135">It can also be explicitly specified using the `ViewComponentAttribute.Name` property.</span></span>

<span data-ttu-id="03f58-136">视图组件类：</span><span class="sxs-lookup"><span data-stu-id="03f58-136">A view component class:</span></span>

* <span data-ttu-id="03f58-137">完全支持构造函数[依赖关系注入](../../fundamentals/dependency-injection.md)</span><span class="sxs-lookup"><span data-stu-id="03f58-137">Fully supports constructor [dependency injection](../../fundamentals/dependency-injection.md)</span></span>

* <span data-ttu-id="03f58-138">不在控制器生命周期，这意味着你无法使用采用一部分[筛选器](../controllers/filters.md)视图组件中</span><span class="sxs-lookup"><span data-stu-id="03f58-138">Does not take part in the controller lifecycle, which means you can't use [filters](../controllers/filters.md) in a view component</span></span>

### <a name="view-component-methods"></a><span data-ttu-id="03f58-139">查看组件方法</span><span class="sxs-lookup"><span data-stu-id="03f58-139">View component methods</span></span>

<span data-ttu-id="03f58-140">视图组件来定义其逻辑中的`InvokeAsync`返回方法`IViewComponentResult`。</span><span class="sxs-lookup"><span data-stu-id="03f58-140">A view component defines its logic in an `InvokeAsync` method that returns an `IViewComponentResult`.</span></span> <span data-ttu-id="03f58-141">参数直接来自视图组件，不是从模型绑定的调用。</span><span class="sxs-lookup"><span data-stu-id="03f58-141">Parameters come directly from invocation of the view component, not from model binding.</span></span> <span data-ttu-id="03f58-142">视图组件永远不会直接处理的请求。</span><span class="sxs-lookup"><span data-stu-id="03f58-142">A view component never directly handles a request.</span></span> <span data-ttu-id="03f58-143">通常情况下，视图组件初始化模型，并将其传递到视图，通过调用`View`方法。</span><span class="sxs-lookup"><span data-stu-id="03f58-143">Typically, a view component initializes a model and passes it to a view by calling the `View` method.</span></span> <span data-ttu-id="03f58-144">总之，查看组件方法：</span><span class="sxs-lookup"><span data-stu-id="03f58-144">In summary, view component methods:</span></span>

* <span data-ttu-id="03f58-145">定义`InvokeAsync`返回的方法`IViewComponentResult`</span><span class="sxs-lookup"><span data-stu-id="03f58-145">Define an `InvokeAsync` method that returns an `IViewComponentResult`</span></span>
* <span data-ttu-id="03f58-146">通常初始化模型并将其传递到视图，通过调用`ViewComponent``View`方法</span><span class="sxs-lookup"><span data-stu-id="03f58-146">Typically initializes a model and passes it to a view by calling the `ViewComponent` `View` method</span></span>
* <span data-ttu-id="03f58-147">参数来自调用的方法中，不是 HTTP，没有任何模型绑定</span><span class="sxs-lookup"><span data-stu-id="03f58-147">Parameters come from the calling method, not HTTP, there is no model binding</span></span>
* <span data-ttu-id="03f58-148">是直接为 HTTP 终结点不可访问，调用它们 （通常在视图中） 在代码中。</span><span class="sxs-lookup"><span data-stu-id="03f58-148">Are not reachable directly as an HTTP endpoint, they are invoked from your code (usually in a view).</span></span> <span data-ttu-id="03f58-149">视图组件永远不会处理请求</span><span class="sxs-lookup"><span data-stu-id="03f58-149">A view component never handles a request</span></span>
* <span data-ttu-id="03f58-150">在签名而不是当前 HTTP 请求中的任何详细上重载</span><span class="sxs-lookup"><span data-stu-id="03f58-150">Are overloaded on the signature rather than any details from the current HTTP request</span></span>

### <a name="view-search-path"></a><span data-ttu-id="03f58-151">视图搜索路径</span><span class="sxs-lookup"><span data-stu-id="03f58-151">View search path</span></span>

<span data-ttu-id="03f58-152">运行时中搜索以下路径中的视图：</span><span class="sxs-lookup"><span data-stu-id="03f58-152">The runtime searches for the view in the following paths:</span></span>

   * <span data-ttu-id="03f58-153">视图 /\<controller_name > /Components/\<view_component_name > /\<view_name ></span><span class="sxs-lookup"><span data-stu-id="03f58-153">Views/\<controller_name>/Components/\<view_component_name>/\<view_name></span></span>
   * <span data-ttu-id="03f58-154">视图/共享/组件/\<view_component_name > /\<view_name ></span><span class="sxs-lookup"><span data-stu-id="03f58-154">Views/Shared/Components/\<view_component_name>/\<view_name></span></span>

<span data-ttu-id="03f58-155">查看组件的默认视图名称是*默认*，这意味着你视图文件将通常命名为*Default.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="03f58-155">The default view name for a view component is *Default*, which means your view file will typically be named *Default.cshtml*.</span></span> <span data-ttu-id="03f58-156">创建视图组件结果时或在调用时，可以指定不同的视图名称`View`方法。</span><span class="sxs-lookup"><span data-stu-id="03f58-156">You can specify a different view name when creating the view component result or when calling the `View` method.</span></span>

<span data-ttu-id="03f58-157">我们建议您命名该视图文件*Default.cshtml*并用*视图/共享/组件/\<view_component_name > /\<view_name >*路径。</span><span class="sxs-lookup"><span data-stu-id="03f58-157">We recommend you name the view file *Default.cshtml* and use the *Views/Shared/Components/\<view_component_name>/\<view_name>* path.</span></span> <span data-ttu-id="03f58-158">`PriorityList`此示例中使用的视图组件使用*Views/Shared/Components/PriorityList/Default.cshtml*视图组件视图。</span><span class="sxs-lookup"><span data-stu-id="03f58-158">The `PriorityList` view component used in this sample uses *Views/Shared/Components/PriorityList/Default.cshtml* for the view component view.</span></span>

## <a name="invoking-a-view-component"></a><span data-ttu-id="03f58-159">调用视图组件</span><span class="sxs-lookup"><span data-stu-id="03f58-159">Invoking a view component</span></span>

<span data-ttu-id="03f58-160">若要使用视图组件，请调用以下命令，在视图内：</span><span class="sxs-lookup"><span data-stu-id="03f58-160">To use the view component, call the following inside a view:</span></span>

```html
@Component.InvokeAsync("Name of view component", <anonymous type containing parameters>)
```

<span data-ttu-id="03f58-161">参数将传递给`InvokeAsync`方法。</span><span class="sxs-lookup"><span data-stu-id="03f58-161">The parameters will be passed to the `InvokeAsync` method.</span></span> <span data-ttu-id="03f58-162">`PriorityList`视图在文章中开发的组件调用从*Views/Todo/Index.cshtml*视图文件。</span><span class="sxs-lookup"><span data-stu-id="03f58-162">The `PriorityList` view component developed in the article is invoked from the *Views/Todo/Index.cshtml* view file.</span></span> <span data-ttu-id="03f58-163">在下面的示例，`InvokeAsync`方法调用包含两个参数：</span><span class="sxs-lookup"><span data-stu-id="03f58-163">In the following, the `InvokeAsync` method is called with two parameters:</span></span>

[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]

## <a name="invoking-a-view-component-as-a-tag-helper"></a><span data-ttu-id="03f58-164">调用视图组件作为标记帮助器</span><span class="sxs-lookup"><span data-stu-id="03f58-164">Invoking a view component as a Tag Helper</span></span>

<span data-ttu-id="03f58-165">有关 ASP.NET 核心 1.1 和更高版本，你可以调用将视图组件作为[标记帮助器](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="03f58-165">For ASP.NET Core 1.1 and higher, you can invoke a view component as a [Tag Helper](xref:mvc/views/tag-helpers/intro):</span></span>

[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="03f58-166">有关标记帮助程序 Pascal 大小写形式类和方法的参数转换为其[降低 kebab 用例](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101)。</span><span class="sxs-lookup"><span data-stu-id="03f58-166">Pascal-cased class and method parameters for Tag Helpers are translated into their [lower kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="03f58-167">用于调用视图组件标记帮助程序使用`<vc></vc>`元素。</span><span class="sxs-lookup"><span data-stu-id="03f58-167">The Tag Helper to invoke a view component uses the `<vc></vc>` element.</span></span> <span data-ttu-id="03f58-168">视图组件按如下方式指定：</span><span class="sxs-lookup"><span data-stu-id="03f58-168">The view component is specified as follows:</span></span>

```html
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

<span data-ttu-id="03f58-169">注意： 若要将视图组件用作标记帮助程序，你必须注册包含视图组件使用的程序集`@addTagHelper`指令。</span><span class="sxs-lookup"><span data-stu-id="03f58-169">Note: In order to use a View Component as a Tag Helper, you must register the assembly containing the View Component using the `@addTagHelper` directive.</span></span> <span data-ttu-id="03f58-170">例如，如果你视图组件是一个程序集称为"MyWebApp"中，添加以下指令到`_ViewImports.cshtml`文件：</span><span class="sxs-lookup"><span data-stu-id="03f58-170">For example, if your View Component is in an assembly called "MyWebApp", add the following directive to the `_ViewImports.cshtml` file:</span></span>

```csharp
@addTagHelper *, MyWebApp
```

<span data-ttu-id="03f58-171">可以将视图组件注册为引用视图组件任何文件标记帮助器。</span><span class="sxs-lookup"><span data-stu-id="03f58-171">You can register a View Component as a Tag Helper to any file that references the View Component.</span></span> <span data-ttu-id="03f58-172">请参阅[管理标记帮助器作用域](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope)有关如何注册标记帮助器的详细信息。</span><span class="sxs-lookup"><span data-stu-id="03f58-172">See [Managing Tag Helper Scope](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) for more information on how to register Tag Helpers.</span></span>

<span data-ttu-id="03f58-173">`InvokeAsync`本教程中使用的方法：</span><span class="sxs-lookup"><span data-stu-id="03f58-173">The `InvokeAsync` method used in this tutorial:</span></span>

[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="03f58-174">在标记帮助器标记：</span><span class="sxs-lookup"><span data-stu-id="03f58-174">In Tag Helper markup:</span></span>

[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="03f58-175">在此示例中更高版本，`PriorityList`视图组件将成为`priority-list`。</span><span class="sxs-lookup"><span data-stu-id="03f58-175">In the sample above, the `PriorityList` view component becomes `priority-list`.</span></span> <span data-ttu-id="03f58-176">查看组件的参数作为特性中 kebab 小写进行传递。</span><span class="sxs-lookup"><span data-stu-id="03f58-176">The parameters to the view component are passed as attributes in lower kebab case.</span></span>

### <a name="invoking-a-view-component-directly-from-a-controller"></a><span data-ttu-id="03f58-177">调用视图组件直接从控制器</span><span class="sxs-lookup"><span data-stu-id="03f58-177">Invoking a view component directly from a controller</span></span>

<span data-ttu-id="03f58-178">查看组件通常调用从视图中，但你可以直接从控制器方法中调用它们。</span><span class="sxs-lookup"><span data-stu-id="03f58-178">View components are typically invoked from a view, but you can invoke them directly from a controller method.</span></span> <span data-ttu-id="03f58-179">查看组件未定义终结点类似控制器，你可以轻松地实现返回的内容的控制器操作`ViewComponentResult`。</span><span class="sxs-lookup"><span data-stu-id="03f58-179">While view components do not define endpoints like controllers, you can easily implement a controller action that returns the content of a `ViewComponentResult`.</span></span>

<span data-ttu-id="03f58-180">在此示例中，直接从控制器中调用视图组件：</span><span class="sxs-lookup"><span data-stu-id="03f58-180">In this example, the view component is called directly from the controller:</span></span>

[!code-csharp[Main](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

## <a name="walkthrough-creating-a-simple-view-component"></a><span data-ttu-id="03f58-181">演练： 创建简单视图组件</span><span class="sxs-lookup"><span data-stu-id="03f58-181">Walkthrough: Creating a simple view component</span></span>

<span data-ttu-id="03f58-182">[下载](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/view-components/sample)、 生成和测试起始代码。</span><span class="sxs-lookup"><span data-stu-id="03f58-182">[Download](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/view-components/sample), build and test the starter code.</span></span> <span data-ttu-id="03f58-183">它是使用一个简单项目`Todo`显示的列表的控制器*Todo*项。</span><span class="sxs-lookup"><span data-stu-id="03f58-183">It's a simple project with a `Todo` controller that displays a list of *Todo* items.</span></span>

![ToDos 的列表](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a><span data-ttu-id="03f58-185">添加 ViewComponent 类</span><span class="sxs-lookup"><span data-stu-id="03f58-185">Add a ViewComponent class</span></span>

<span data-ttu-id="03f58-186">创建*ViewComponents*文件夹并添加以下`PriorityListViewComponent`类：</span><span class="sxs-lookup"><span data-stu-id="03f58-186">Create a *ViewComponents* folder and add the following `PriorityListViewComponent` class:</span></span>

[!code-csharp[Main](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]

<span data-ttu-id="03f58-187">代码的说明：</span><span class="sxs-lookup"><span data-stu-id="03f58-187">Notes on the code:</span></span>

* <span data-ttu-id="03f58-188">视图组件类可包含在**任何**项目中的文件夹。</span><span class="sxs-lookup"><span data-stu-id="03f58-188">View component classes can be contained in **any** folder in the project.</span></span>
* <span data-ttu-id="03f58-189">因为的类名称 PriorityList**ViewComponent**以后缀结尾**ViewComponent**，从视图中引用类组件时，运行时将使用字符串"PriorityList"。</span><span class="sxs-lookup"><span data-stu-id="03f58-189">Because the class name PriorityList**ViewComponent** ends with the suffix **ViewComponent**, the runtime will use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="03f58-190">我将介绍的更详细地更高版本。</span><span class="sxs-lookup"><span data-stu-id="03f58-190">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="03f58-191">`[ViewComponent]`属性可以更改用来引用视图组件的名称。</span><span class="sxs-lookup"><span data-stu-id="03f58-191">The `[ViewComponent]` attribute can change the name used to reference a view component.</span></span> <span data-ttu-id="03f58-192">例如，我们无法具有命名类`XYZ`和应用`ViewComponent`属性：</span><span class="sxs-lookup"><span data-stu-id="03f58-192">For example, we could have named the class `XYZ` and applied the `ViewComponent` attribute:</span></span>

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* <span data-ttu-id="03f58-193">`[ViewComponent]`上面属性告知视图组件选择器，以使用名称`PriorityList`查找关联与该组件，并从视图中引用类组件时使用字符串"PriorityList"的视图时。</span><span class="sxs-lookup"><span data-stu-id="03f58-193">The `[ViewComponent]` attribute above tells the view component selector to use the name `PriorityList` when looking for the views associated with the component, and to use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="03f58-194">我将介绍的更详细地更高版本。</span><span class="sxs-lookup"><span data-stu-id="03f58-194">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="03f58-195">该组件使用[依赖关系注入](../../fundamentals/dependency-injection.md)要提供的数据上下文。</span><span class="sxs-lookup"><span data-stu-id="03f58-195">The component uses [dependency injection](../../fundamentals/dependency-injection.md) to make the data context available.</span></span>
* <span data-ttu-id="03f58-196">`InvokeAsync`公开可以从视图中，和它调用的方法可以采用任意数量的自变量。</span><span class="sxs-lookup"><span data-stu-id="03f58-196">`InvokeAsync` exposes a method which can be called from a view, and it can take an arbitrary number of arguments.</span></span>
* <span data-ttu-id="03f58-197">`InvokeAsync`方法返回的一套`ToDo`符合项`isDone`和`maxPriority`参数。</span><span class="sxs-lookup"><span data-stu-id="03f58-197">The `InvokeAsync` method returns the set of `ToDo` items that satisfy the `isDone` and `maxPriority` parameters.</span></span>

### <a name="create-the-view-component-razor-view"></a><span data-ttu-id="03f58-198">创建视图组件 Razor 视图</span><span class="sxs-lookup"><span data-stu-id="03f58-198">Create the view component Razor view</span></span>

* <span data-ttu-id="03f58-199">创建*视图/共享/组件*文件夹。</span><span class="sxs-lookup"><span data-stu-id="03f58-199">Create the *Views/Shared/Components* folder.</span></span> <span data-ttu-id="03f58-200">此文件夹**必须**命名为*组件*。</span><span class="sxs-lookup"><span data-stu-id="03f58-200">This folder **must** be named *Components*.</span></span>

* <span data-ttu-id="03f58-201">创建*视图/共享/组件/PriorityList*文件夹。</span><span class="sxs-lookup"><span data-stu-id="03f58-201">Create the *Views/Shared/Components/PriorityList* folder.</span></span> <span data-ttu-id="03f58-202">此文件夹名称必须与视图组件类的名称或减后缀类的名称匹配 (如果我们遵循约定，并且使用*ViewComponent*中的类名称后缀)。</span><span class="sxs-lookup"><span data-stu-id="03f58-202">This folder name must match the name of the view component class, or the name of the class minus the suffix (if we followed convention and used the *ViewComponent* suffix in the class name).</span></span> <span data-ttu-id="03f58-203">如果你使用`ViewComponent`属性，类名称需要匹配所指定的属性。</span><span class="sxs-lookup"><span data-stu-id="03f58-203">If you used the `ViewComponent` attribute, the class name would need to match the attribute designation.</span></span>

* <span data-ttu-id="03f58-204">创建*Views/Shared/Components/PriorityList/Default.cshtml* Razor 视图：[!code-html[Main](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="03f58-204">Create a *Views/Shared/Components/PriorityList/Default.cshtml* Razor view: [!code-html[Main](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]</span></span>
    
   <span data-ttu-id="03f58-205">Razor 查看所花费的列表`TodoItem`并显示它们。</span><span class="sxs-lookup"><span data-stu-id="03f58-205">The Razor view takes a list of `TodoItem` and displays them.</span></span> <span data-ttu-id="03f58-206">如果视图组件`InvokeAsync`方法不满足 （如我们的示例），视图的名称*默认*由用于约定的视图名称。</span><span class="sxs-lookup"><span data-stu-id="03f58-206">If the view component `InvokeAsync` method doesn't pass the name of the view (as in our sample), *Default* is used for the view name by convention.</span></span> <span data-ttu-id="03f58-207">更高版本在教程中，我将向你演示如何将传递视图的名称。</span><span class="sxs-lookup"><span data-stu-id="03f58-207">Later in the tutorial, I'll show you how to pass the name of the view.</span></span> <span data-ttu-id="03f58-208">若要覆盖特定控制器的默认样式，请将视图添加到特定于控制器的视图文件夹 (例如*Views/Todo/Components/PriorityList/Default.cshtml)*。</span><span class="sxs-lookup"><span data-stu-id="03f58-208">To override the default styling for a specific controller, add a view to the controller-specific view folder (for example *Views/Todo/Components/PriorityList/Default.cshtml)*.</span></span>
    
    <span data-ttu-id="03f58-209">如果控制器特定视图组件，则你可以将其添加到特定于控制器的文件夹 (*Views/Todo/Components/PriorityList/Default.cshtml*)。</span><span class="sxs-lookup"><span data-stu-id="03f58-209">If the view component is controller-specific, you can add it to the controller-specific folder (*Views/Todo/Components/PriorityList/Default.cshtml*).</span></span>

* <span data-ttu-id="03f58-210">添加`div`包含到底部的优先级列表组件调用*Views/Todo/index.cshtml*文件：</span><span class="sxs-lookup"><span data-stu-id="03f58-210">Add a `div` containing a call to the priority list component to the bottom of the *Views/Todo/index.cshtml* file:</span></span>

    [!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexFirst.cshtml?range=34-38)]

<span data-ttu-id="03f58-211">标记`@await Component.InvokeAsync`显示调用查看组件的语法。</span><span class="sxs-lookup"><span data-stu-id="03f58-211">The markup `@await Component.InvokeAsync` shows the syntax for calling view components.</span></span> <span data-ttu-id="03f58-212">第一个参数是我们想要调用或调用组件的名称。</span><span class="sxs-lookup"><span data-stu-id="03f58-212">The first argument is the name of the component we want to invoke or call.</span></span> <span data-ttu-id="03f58-213">后续参数传递给该组件。</span><span class="sxs-lookup"><span data-stu-id="03f58-213">Subsequent parameters are passed to the component.</span></span> <span data-ttu-id="03f58-214">`InvokeAsync`可以采用任意数量的自变量。</span><span class="sxs-lookup"><span data-stu-id="03f58-214">`InvokeAsync` can take an arbitrary number of arguments.</span></span>

<span data-ttu-id="03f58-215">测试应用程序。</span><span class="sxs-lookup"><span data-stu-id="03f58-215">Test the app.</span></span> <span data-ttu-id="03f58-216">下图显示 ToDo 列表和优先级的项：</span><span class="sxs-lookup"><span data-stu-id="03f58-216">The following image shows the ToDo list and the priority items:</span></span>

![todo 列表和优先级项](view-components/_static/pi.png)

<span data-ttu-id="03f58-218">你还可以直接从控制器调用视图组件：</span><span class="sxs-lookup"><span data-stu-id="03f58-218">You can also call the view component directly from the controller:</span></span>

[!code-csharp[Main](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

![优先级的项从 IndexVC 操作](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a><span data-ttu-id="03f58-220">指定的视图名称</span><span class="sxs-lookup"><span data-stu-id="03f58-220">Specifying a view name</span></span>

<span data-ttu-id="03f58-221">复杂视图组件可能需要指定在某些情况下的非默认视图。</span><span class="sxs-lookup"><span data-stu-id="03f58-221">A complex view component might need to specify a non-default view under some conditions.</span></span> <span data-ttu-id="03f58-222">下面的代码演示如何指定"PVC"视图从`InvokeAsync`方法。</span><span class="sxs-lookup"><span data-stu-id="03f58-222">The following code shows how to specify the "PVC" view  from the `InvokeAsync` method.</span></span> <span data-ttu-id="03f58-223">更新`InvokeAsync`中的方法`PriorityListViewComponent`类。</span><span class="sxs-lookup"><span data-stu-id="03f58-223">Update the `InvokeAsync` method in the `PriorityListViewComponent` class.</span></span>

[!code-csharp[Main](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]

<span data-ttu-id="03f58-224">复制*Views/Shared/Components/PriorityList/Default.cshtml*文件到名为视图*Views/Shared/Components/PriorityList/PVC.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="03f58-224">Copy the *Views/Shared/Components/PriorityList/Default.cshtml* file to a view named *Views/Shared/Components/PriorityList/PVC.cshtml*.</span></span> <span data-ttu-id="03f58-225">添加标题以指示正在使用 PVC 视图。</span><span class="sxs-lookup"><span data-stu-id="03f58-225">Add a heading to indicate the PVC view is being used.</span></span>

[!code-html[Main](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]

<span data-ttu-id="03f58-226">更新*Views/TodoList/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="03f58-226">Update *Views/TodoList/Index.cshtml*:</span></span>

<!-- Views/TodoList/Index.cshtml is never imported, so change to test tutorial -->

[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="03f58-227">运行应用程序并验证 PVC 视图。</span><span class="sxs-lookup"><span data-stu-id="03f58-227">Run the app and verify PVC view.</span></span>

![优先级视图组件](view-components/_static/pvc.png)

<span data-ttu-id="03f58-229">如果不呈现 PVC 视图，请验证正在调用的优先级为 4 或更高版本的视图组件。</span><span class="sxs-lookup"><span data-stu-id="03f58-229">If the PVC view is not rendered, verify you are calling the view component with a priority of 4 or higher.</span></span>

### <a name="examine-the-view-path"></a><span data-ttu-id="03f58-230">检查视图路径</span><span class="sxs-lookup"><span data-stu-id="03f58-230">Examine the view path</span></span>

* <span data-ttu-id="03f58-231">将优先级参数更改为三个或更少因此 priority 视图不返回。</span><span class="sxs-lookup"><span data-stu-id="03f58-231">Change the priority parameter to three or less so the priority view is not returned.</span></span>
* <span data-ttu-id="03f58-232">暂时重命名*Views/Todo/Components/PriorityList/Default.cshtml*到*1Default.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="03f58-232">Temporarily rename the *Views/Todo/Components/PriorityList/Default.cshtml* to *1Default.cshtml*.</span></span>
* <span data-ttu-id="03f58-233">测试应用程序，您将收到以下错误：</span><span class="sxs-lookup"><span data-stu-id="03f58-233">Test the app, you'll get the following error:</span></span>

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' was not found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* <span data-ttu-id="03f58-234">复制*Views/Todo/Components/PriorityList/1Default.cshtml*到*Views/Shared/Components/PriorityList/Default.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="03f58-234">Copy *Views/Todo/Components/PriorityList/1Default.cshtml* to *Views/Shared/Components/PriorityList/Default.cshtml*.</span></span>
* <span data-ttu-id="03f58-235">添加到一些标记*共享*Todo 视图组件视图，以指示该视图是从*共享*文件夹。</span><span class="sxs-lookup"><span data-stu-id="03f58-235">Add some markup to the *Shared* Todo view component view to indicate the view is from the *Shared* folder.</span></span>
* <span data-ttu-id="03f58-236">测试**共享**组件视图。</span><span class="sxs-lookup"><span data-stu-id="03f58-236">Test the **Shared** component view.</span></span>

![与共享组件视图 ToDo 输出](view-components/_static/shared.png)

### <a name="avoiding-magic-strings"></a><span data-ttu-id="03f58-238">避免神奇的字符串</span><span class="sxs-lookup"><span data-stu-id="03f58-238">Avoiding magic strings</span></span>

<span data-ttu-id="03f58-239">如果你想编译时间安全性，可以将硬编码视图组件名称替换类名称。</span><span class="sxs-lookup"><span data-stu-id="03f58-239">If you want compile time safety, you can replace the hard-coded view component name with the class name.</span></span> <span data-ttu-id="03f58-240">创建不带"ViewComponent"后缀的视图组件：</span><span class="sxs-lookup"><span data-stu-id="03f58-240">Create the view component without the "ViewComponent" suffix:</span></span>

[!code-csharp[Main](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]

<span data-ttu-id="03f58-241">添加`using`语句与你 Razor 查看文件，并使用`nameof`运算符：</span><span class="sxs-lookup"><span data-stu-id="03f58-241">Add a `using` statement to your Razor view file, and use the `nameof` operator:</span></span>

[!code-html[Main](view-components/sample/ViewCompFinal/Views/Todo/IndexNameof.cshtml?range=1-6,33-)]

## <a name="additional-resources"></a><span data-ttu-id="03f58-242">其他资源</span><span class="sxs-lookup"><span data-stu-id="03f58-242">Additional Resources</span></span>

* [<span data-ttu-id="03f58-243">视图中的依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="03f58-243">Dependency injection into views</span></span>](dependency-injection.md)
