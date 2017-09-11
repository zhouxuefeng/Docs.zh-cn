---
title: "视图概述"
author: ardalis
description: 
keywords: "ASP.NET 核心"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 668c320d-c050-45e3-8161-2f460dc93b2f
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/overview
ms.openlocfilehash: 318d8832dadadd6946c7ffe58f9d89aaf68f54fc
ms.sourcegitcommit: 4693cb02d845adf2efa00e07ad432c81867bfa12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/08/2017
---
# <a name="rendering-html-with-views-in-aspnet-core-mvc"></a>与 ASP.NET 核心 mvc 视图中呈现的 HTML

通过[Steve Smith](http://ardalis.com)

ASP.NET 核心 MVC 控制器可以返回格式化后的结果使用*视图*。

## <a name="what-are-views"></a>视图有哪些？

在模型-视图-控制器 (MVC) 模式下，*视图*封装与应用程序的用户的交互的演示文稿详细信息。 视图是用嵌入的代码生成要发送到客户端内容的 HTML 模板。 视图使用[Razor 语法](razor.md)，这样，代码与 HTML 交互最少的代码或方案。

ASP.NET 核心 MVC 视图是*.cshtml*文件存储在默认情况下*视图*应用程序中的文件夹。 通常情况下，每个控制器将拥有其自己的文件夹，在其中是为特定控制器操作的视图。

![在解决方案资源管理器的视图文件夹](overview/_static/views_solution_explorer.png)

除了操作特定的视图，[分部视图](partial.md)，[布局和其他特殊视图文件](layout.md)可以使用以帮助减少重复并允许在应用程序的视图内重复使用。

## <a name="benefits-of-using-views"></a>使用视图的好处

视图提供[关注点分离](http://deviq.com/separation-of-concerns/)内封装从业务逻辑中单独的用户界面级别标记的 MVC 应用。 ASP.NET MVC 视图使用[Razor 语法](razor.md)以使 HTML 标记和服务器端逻辑之间轻松切换。 使用的视图之间可以方便地重用的应用程序的用户界面的通用、 重复方面[布局和共享的指令](layout.md)或[分部视图](partial.md)。

## <a name="creating-a-view"></a>创建视图

特定于控制器的视图中创建*视图 / [ControllerName]*文件夹。 控制器之间共享的视图都将置于*/视图/共享*文件夹。 其关联的控制器操作，相同命名的视图文件并添加*.cshtml*文件扩展名。 例如，若要创建的视图*有关*操作*主页*控制器，则你将创建*About.cshtml*文件中 * /视图/主页*文件夹。

示例视图文件 (*About.cshtml*):

[!code-html[Main](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

*Razor*由表示代码`@`符号。 在 Razor 代码块设置的大括号内运行 C# 语句 (`{` `}`)，例如"关于"分配到`ViewData["Title"]`上面所示的元素。 Razor 可以用于通过只需使用引用值显示在 HTML 内的值`@`符号，如中所示`<h2>`和`<h3>`上面的元素。

此视图着重于只是输出它所负责的部分。 在其他地方指定的页面的布局，rest 和视图，其他常见方面。 详细了解[布局和共享的视图逻辑](layout.md)。

## <a name="how-do-controllers-specify-views"></a>如何执行操作控制器指定视图？

视图通常从之类的操作返回`ViewResult`。 操作方法可以创建并返回`ViewResult`直接，但更常见如果你的控制器继承自`Controller`，只需将使用`View`作为此示例演示了帮助器方法：

*HomeController.cs*

[!code-csharp[Main](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

`View`帮助器方法具有好几个重载，以返回视图更轻松地进行应用程序开发人员。 你可以选择指定一个视图以返回，以及要传递到视图的模型对象。

在此操作返回时， *About.cshtml*呈现视图上面所示：

![有关页面](overview/_static/about-page.png)

### <a name="view-discovery"></a>视图发现

当操作返回的视图时，捗个称*视图发现*发生。 此过程确定将使用哪个视图文件。 除非指定了特定视图文件，运行时将查找有关特定于控制器的视图首先，然后查找中的视图名称匹配*共享*文件夹。

当操作返回`View`方法，如下所示`return View();`，操作名称用作视图名称。 例如，如果这从名为"Index"的操作方法调用的则可以等效于传入的"索引"视图名称。 视图名称可以显式传递给方法 (`return View("SomeView");`)。 在这两种情况下，查看发现搜索匹配的视图文件中：

   1. 视图 /\<ControllerName > /\<ViewName >.cshtml

   2. 视图/共享/\<ViewName >.cshtml

>[!TIP]
> 我们建议以下只返回的约定`View()`从操作，如果可能，因为它会导致更灵活、 更容易重构代码。

可以提供视图文件路径，而不是视图名称。 如果使用从应用程序根目录开始的绝对路径 (根据需要第一页为"/"或"~ /")，则*.cshtml*扩展必须指定为的文件路径的一部分。 例如：`return View("Views/Home/About.cshtml");`。 或者，可以使用中的特定于控制器的目录中的相对路径*视图*目录以指定不同的目录中的视图。 例如：`return View("../Manage/Index");`内*主页*控制器。 同样，你就可以遍历当前的特定于控制器的目录： `return View("./About");`。 请注意，不使用相对路径*.cshtml*扩展。 如前所述，请按照组织视图以反映控制器、 操作和可维护性和清晰性的视图之间的关系的文件结构的最佳做法。

> [!NOTE]
> [分部视图](partial.md)和[查看组件](view-components.md)使用类似的 （但不是完全相同的） 发现机制。

> [!NOTE]
> 你可以自定义有关视图的位置在应用内使用自定义的默认约定`IViewLocationExpander`。

>[!TIP]
> 视图名称可能是区分大小写，具体取决于基础文件系统。 用于对操作系统的兼容性，始终区分大小写之间控制器和操作名称和关联的视图文件夹和文件名。

## <a name="passing-data-to-views"></a>将数据传递到视图

可以将数据传递给使用几种机制的视图。 最可靠方法是指定*模型*视图中的类型 (通常称为*viewmodel*，以区别于业务域模型类型)，然后将此类型的实例传递到视图从操作。 我们建议你使用模型或视图模型将数据传递给视图。 这允许视图以充分利用强类型检查。 你可以指定视图使用的模型`@model`指令：

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

发送到该视图的实例后为视图指定了一个模型，可以访问在强类型方式使用`@Model`如上所示。 若要提供的视图的模型类型实例，控制器将其传递作为参数：

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

可以提供给作为模型的视图类型没有限制。 我们建议传递具有很少或没有行为的普通旧 CLR 对象 (POCO) 查看模型，以便在应用程序可以在其他位置封装业务逻辑。 此方法的一个示例是*地址*viewmodel 使用在上面的示例：

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
> 执行任何操作会阻止你为你的业务模型类型和你显示模型类型中使用相同的类。 但是，通过将它们分开，允许你从你的域或持久性模型独立变化的视图，可以提供一些的安全优势 (的用户将发送到应用程序使用的模型[模型绑定](../models/model-binding.md))。

### <a name="loosely-typed-data"></a>松散类型化的数据

强类型化视图，除了所有视图还都可以对数据松散类型化集合的访问。 可以通过引用此相同集合`ViewData`或`ViewBag`控制器和视图上的属性。 `ViewBag`属性是周围的包装器`ViewData`，该集合上提供的动态视图。 它不是一个单独的集合。

`ViewData`通过访问的字典对象`string`密钥。 你可以存储和检索对象，并且你将需要将其强制转换为特定类型时将其提取。 你可以使用`ViewData`以将数据从控制器传递到视图，以及在视图 （和分部视图和布局） 内。 字符串数据可以存储，以及可以直接使用，而无需强制转换。

设置的某些值`ViewData`中某项操作：

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

处理在视图中的数据：

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

`ViewBag`对象提供对存储在对象的动态访问`ViewData`。 这可以更便捷地使用，因为它不需要强制转换。 相同的示例，使用上面`ViewBag`而不是强类型化`address`视图中的实例：

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
> 由于都是指同一基础`ViewData`集合，您可以混用和命名空间与匹配`ViewData`和`ViewBag`时读取和写入的值，如果方便。

### <a name="dynamic-views"></a>动态视图

未声明的模型类型，但具有传递给它们的模型实例的视图可以动态引用此实例。 例如，如果实例`Address`传递给不声明视图`@model`，视图将仍将能够动态显示引用该实例的属性：

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "html", "highlight_args": {"hl_lines": [13, 16, 17, 18]}} -->

```html
<address>
       @Model.Street<br />
       @Model.City, @Model.State @Model.PostalCode<br />
       <abbr title="Phone">P:</abbr>
       425.555.0100
   </address>
   ```

此功能可以提供一定的灵活性，但不提供任何编译保护或智能感知。 如果属性不存在，页面将在运行时失败。

## <a name="more-view-features"></a>详细视图功能

[标记帮助程序](tag-helpers/intro.md)方便地将服务器端行为添加到现有的 HTML 标记，避免需要使用自定义代码或在视图内的帮助器。 标记帮助程序将作为属性应用于 HTML 元素，将忽略不熟悉它们，允许以进行编辑和呈现在不同的工具，可查看标记的编辑器。 标记帮助器有许多用途，并且特别可以使[使用窗体](working-with-forms.md)容易得多。

生成自定义的 HTML 标记可实现的许多内置的 HTML 帮助器，并将更复杂的用户界面逻辑 （可能有其自己数据的要求） 可以封装在[视图组件](view-components.md)。 视图组件提供的控制器和视图提供的问题相同分离，可以消除操作和视图使用的常见 UI 元素数据处理的需要。

ASP.NET 核心的很多其他方面，如视图支持[依赖关系注入](../../fundamentals/dependency-injection.md)，允许服务[注入到视图](dependency-injection.md)。
