---
title: "在 ASP.NET Core MVC 视图"
author: ardalis
description: "了解视图如何处理应用程序的数据表示和 ASP.NET 核心 MVC 中的用户交互。"
keywords: "ASP.NET 核心查看，MVC、 razor、 视图模型、 viewdata、 viewbag"
ms.author: riande
manager: wpickett
ms.date: 09/26/2017
ms.topic: article
ms.assetid: 668c320d-c050-45e3-8161-2f460dc93b2f
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/overview
ms.openlocfilehash: d3fbdecaed87b3432f0532748a0833c833c65129
ms.sourcegitcommit: a60a99104fe7a29e271667cead6a06b6d8258d03
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2017
---
# <a name="views-in-aspnet-core-mvc"></a>在 ASP.NET Core MVC 视图

通过[Steve Smith](https://ardalis.com/)和[Luke Latham](https://github.com/guardrex)

在**M**odel-**V**查看-**C**ontroller (MVC) 模式，*视图*处理应用程序的数据的演示文稿和用户交互。 视图是 HTML 模板与嵌入[Razor 标记](xref:mvc/views/razor)。 Razor 标记是代码与 HTML 标记来生成一个发送到客户端的网页交互。

ASP.NET 核心 mvc 视图是*.cshtml*文件使用[C# 编程语言](/dotnet/csharp/)Razor 标记中。 通常情况下，查看文件会被分成文件夹为每个应用程序的名为[控制器](xref:mvc/controllers/actions)。 文件夹存储在中*视图*根目录下的应用程序的文件夹：

![Visual Studio 解决方案资源管理器中的视图文件夹是使用主文件夹打开状态以显示 About.cshtml、 Contact.cshtml 和 Index.cshtml 文件打开](overview/_static/views_solution_explorer.png)

*主页*控制器由*主页*内的文件夹*视图*文件夹。 *主页*文件夹包含的视图*有关*，*联系人*，和*索引*（主页） 网页。 当用户请求这些三个网页中的控制器操作之一*主页*控制器确定的三个视图用于生成并返回给用户的网页。

使用[布局](xref:mvc/views/layout)提供一致的网页部分和减少代码重复。 布局通常包含标头、 导航和菜单元素和页脚。 页眉和页脚通常包含多个元数据元素和链接到脚本和样式资产的样本标记。 布局帮助你避免在你的视图中此样本标记。

[分部视图](xref:mvc/views/partial)减少由管理视图的可重用部件的代码的重复。 例如，分部视图可用于在多个视图将显示博客网站上作者个人简介。 作者个人简介是普通视图内容，不要求代码以执行以便生成的网页内容。 作者个人简介内容是内容的可用于按模型绑定单独出现时，查看，因此使用了分部视图适用于此类型是内容的理想之选。

[查看组件](xref:mvc/views/view-components)是类似于分部视图，因为它们允许您以减少重复代码，但它们适用于需要的代码来呈现网页以便在服务器上运行的视图内容。 在所呈现的内容需要数据库交互，如购物车网站时，视图组件是有用的。 查看组件不限制为模型绑定，以便生成网页输出。

## <a name="benefits-of-using-views"></a>使用视图的好处

视图可帮助建立[ **S**eparation **o**f **C**oncerns (SoC) 设计](http://deviq.com/separation-of-concerns/)隔开的用户界面标记的 MVC 应用程序中应用程序的其他部分。 以下 SoC 设计可让你的应用程序模块化，提供以下几个好处：

* 应用程序更容易维护，因为它被更好地组织。 视图通常由应用程序功能分组。 这使得更轻松地找到相关的视图，在功能上工作时。
* 松散耦合的应用程序的部分。 你可以生成和更新单独从业务逻辑和数据访问组件的应用程序的视图。 无需具有更新的应用程序其他部分，你可以修改应用程序的视图。
* 很容易地测试应用程序的用户界面部分，因为视图的单独单位。
* 由于更好地组织，它是不太可能将用户界面的意外重复部分。

## <a name="creating-a-view"></a>创建视图

特定于控制器的视图中创建*视图 / [ControllerName]*文件夹。 控制器之间共享的视图都将置于*视图/共享*文件夹。 若要创建一个视图，添加新文件，并为其提供其关联的控制器操作具有相同的名称*.cshtml*文件扩展名。 若要创建与对应的视图*有关*中的操作*主页*控制器，创建*About.cshtml*文件中*视图/主页*文件夹：

[!code-cshtml[Main](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

*Razor*标记开头`@`符号。 运行的 C# 语句通过将 C# 中的代码[Razor 代码块](xref:mvc/views/razor#razor-code-blocks)设置 off，大括号内 (`{ ... }`)。 有关示例，请参阅"关于"分配到`ViewData["Title"]`上面所示。 您可以通过只需使用引用值显示在 HTML 内的值`@`符号。 看到的内容`<h2>`和`<h3>`上面的元素。

上面所示视图内容是仅向用户呈现的整个网页的一部分。 其他视图文件中指定的页面的布局的其余部分和视图的其他常见方面。 若要了解详细信息，请参阅[布局主题](xref:mvc/views/layout)。

## <a name="how-controllers-specify-views"></a>控制器如何指定视图

视图通常从之类的操作返回[ViewResult](/aspnet/core/api/microsoft.aspnetcore.mvc.viewresult)，这是一种[ActionResult](/aspnet/core/api/microsoft.aspnetcore.mvc.actionresult)。 操作方法可以创建并返回`ViewResult`直接，但此通常未完成。 由于大多数控制器继承[控制器](/aspnet/core/api/microsoft.aspnetcore.mvc.controller)，只需使用`View`helper 方法，以返回`ViewResult`:

*HomeController.cs*

[!code-csharp[Main](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

在此操作返回时， *About.cshtml*视图中的最后一节所示呈现为以下网页：

![有关在 Edge 浏览器中呈现的页面](overview/_static/about-page.png)

`View`帮助器方法具有好几个重载。 你可以选择指定：

* 返回一个显式视图：

  ```csharp
  return View("Orders");
  ```
* A[模型](xref:mvc/models/model-binding)要传递给视图：

  ```csharp
  return View(Orders);
  ```
* 视图和模型：

  ```csharp
  return View("Orders", Orders);
  ```

### <a name="view-discovery"></a>视图发现

当操作返回的视图时，捗个称*视图发现*发生。 此过程确定哪些视图文件使用基于视图名称。 

默认行为`View`方法 (`return View();`) 是返回具有与从中调用它的操作方法相同的名称的视图。 例如，*有关*`ActionResult`控制器的方法名称用于搜索名为的视图文件*About.cshtml*。 首先，运行时查看*视图 / [ControllerName]*视图的文件夹。 如果找不到匹配的视图存在，它将搜索*共享*视图的文件夹。

它并不重要如果隐式返回`ViewResult`与`return View();`或显式传递到的视图名称`View`方法替换`return View("<ViewName>");`。 在这两种情况下，查看发现搜索匹配的视图文件顺序如下：

   1. *视图 /\[ControllerName]\[ViewName].cshtml*
   1. *视图/共享/\[ViewName].cshtml*

视图文件路径可以提供而不是视图名称。 如果使用从应用程序根目录开始的绝对路径 (根据需要第一页为"/"或"~ /")，则*.cshtml*必须指定扩展：

```csharp
return View("Views/Home/About.cshtml");
```

你可以使用相对路径以在不同的目录，而无需指定视图*.cshtml*扩展。 内部`HomeController`，你可以返回*索引*视图你*管理*具有相对路径的视图：

```csharp
return View("../Manage/Index");
```

同样，你可以指示与当前的特定于控制器的目录"。 /"前缀：

```csharp
return View("./About");
```

[分部视图](xref:mvc/views/partial)和[查看组件](xref:mvc/views/view-components)使用类似的 （但不是完全相同的） 发现机制。

你可以自定义视图如何使用自定义是位于应用程序的默认约定[IViewLocationExpander](/aspnet/core/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander)。

视图发现依赖于按文件名称查找查看文件。 如果基础的文件系统是区分大小写，视图名称是可能区分大小写。 对于跨操作系统的兼容性，与匹配用例之间控制器和操作名称和关联的视图文件夹和文件名称。 如果你遇到的错误，在使用区分大小写的文件系统时找不到视图文件，确认所请求的视图文件和实际视图文件名称之间与匹配大小写。

遵循组织您的视图以反映控制器、 操作和可维护性和清晰性的视图之间的关系的文件结构的最佳实践。

## <a name="passing-data-to-views"></a>将数据传递到视图

可以将数据传递给使用几种方法的视图。 最可靠方法是指定[模型](xref:mvc/models/model-binding)视图中的类型。 此模型通常称为*viewmodel*。 从操作可将视图模型类型的实例传递到视图中。

视图模型用于将数据传递给视图允许视图以充分利用*强*类型检查。 *强类型化*(或*强类型*) 意味着每个变量和常量具有显式定义的类型 (例如， `string`， `int`，或`DateTime`)。 在编译时被检查的类型视图中使用的有效性。

[Visual Studio](https://www.visualstudio.com/vs/)和[Visual Studio Code](https://code.visualstudio.com/)列表使用一种称为功能的强类型类成员[IntelliSense](/visualstudio/ide/using-intellisense)。 当你想要查看的视图模型属性时，键入后跟句号 viewmodel 的变量名称 (`.`)。 这有助于更快地编写代码错误较少。

指定模型使用`@model`指令。 使用与模型`@Model`:

```cshtml
@model WebApplication1.ViewModels.Address

<h2>Contact</h2>
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

若要提供到视图模型，控制器将其传递作为参数：

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

可以提供到视图的模型类型没有限制。 我们建议使用**P**词条**O**ld **C**LR **O**对象 (POCO) viewmodel 与很少或没有定义的行为 （方法）。 通常，viewmodel 类既存储在*模型*文件夹或单独*Viewmodel*根目录下的应用程序的文件夹。 *地址*viewmodel 使用在上面的示例是存储在名为的文件 POCO viewmodel *Address.cs*:

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
> 你可以随意使用相同的类进行 viewmodel 类型和你的业务模型类型。 但是，使用单独的模型允许您的视图以独立变化的业务逻辑和数据从应用的访问部分。 分隔的模型和 viewmodel 还提供了安全好处时模型使用[模型绑定](xref:mvc/models/model-binding)和[验证](xref:mvc/models/validation)用户发送到应用的数据。

### <a name="weakly-typed-data-viewdata-and-viewbag"></a>弱类型化数据 （ViewData 和 ViewBag）

除了强类型化的视图，视图还可以访问*弱类型*(也称为*松散类型化*) 的数据集合。 与强类型不同*弱类型*(或*松散类型*) 意味着你不显式声明要使用的数据类型。 用于传递少量入和移出控制器和视图的数据，可以使用弱类型化数据的集合。

| 将之间的数据传递...                        | 示例                                                                        |
| ------------------------------------------------- | ------------------------------------------------------------------------------ |
| 控制器和视图                             | 填充数据的下拉列表。                                          |
| 视图和[布局视图](xref:mvc/views/layout)   | 设置**\<标题 >**视图文件中的布局视图中的元素内容。  |
| [分部视图](xref:mvc/views/partial)和视图 | 会根据用户请求的网页显示数据的小组件。      |

此集合可以通过引用`ViewData`或`ViewBag`控制器和视图上的属性。 `ViewData`属性是弱类型化对象的字典。 `ViewBag`属性是周围的包装器`ViewData`为基础提供动态属性`ViewData`集合。

`ViewData`和`ViewBag`动态解析在运行时。 因为它们未提供编译时类型检查，两者都通常更容易出错比使用视图模型。 出于上述原因，一些开发人员更愿意按最小方式或从不使用`ViewData`和`ViewBag`。

**ViewData**

`ViewData`是[ViewDataDictionary](/aspnet/core/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary)通过访问的对象`string`密钥。 字符串数据可以存储和使用直接而无需强制转换，但您必须将其他转换`ViewData`对象时将其提取到特定类型的值。 你可以使用`ViewData`若要将数据从控制器传递到视图并在视图内，包括[分部视图](xref:mvc/views/partial)和[布局](xref:mvc/views/layout)。

以下是设置问候语和地址使用的值的示例`ViewData`中某项操作：

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

```cshtml
@{
    // Since Address isn't a string, it requires a cast.
    var address = ViewData["Address"] as Address;
}

@ViewData["Greeting"] World!

<address>
    @address.Name<br>
    @address.Street<br>
    @address.City, @address.State @address.PostalCode
</address>
```

**ViewBag**

`ViewBag`是[DynamicViewData](/aspnet/core/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata)对象，它提供对存储在对象的动态访问`ViewData`。 `ViewBag`可以更便捷地使用，因为它不需要强制转换。 下面的示例演示如何使用`ViewBag`与使用相同的结果与`ViewData`上面：

```csharp
public IActionResult SomeAction()
{
    ViewBag.Greeting = "Hello";
    ViewBag.Address  = new Address()
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

```cshtml
@ViewBag.Greeting World!

<address>
    @ViewBag.Address.Name<br>
    @ViewBag.Address.Street<br>
    @ViewBag.Address.City, @ViewBag.Address.State @ViewBag.Address.PostalCode
</address>
```

**同时使用 ViewData 和 ViewBag**

由于`ViewData`和`ViewBag`引用相同的基础`ViewData`集合，你可以同时使用`ViewData`和`ViewBag`和混合和匹配它们时读取和写入值之间。

设置标题使用`ViewBag`和说明使用`ViewData`顶部*About.cshtml*视图：

```cshtml
@{
    Layout = "/Views/Shared/_Layout.cshtml";
    ViewBag.Title = "About Contoso";
    ViewData["Description"] = "Let us tell you about Contoso's philosophy and mission.";
}
```

读取的属性，但反向使用`ViewData`和`ViewBag`。 在*_Layout.cshtml*文件中，获取标题使用`ViewData`并获取说明使用`ViewBag`:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"]</title>
    <meta name="description" content="@ViewBag.Description">
    ...
```

请记住字符串不需要强制转换为`ViewData`。 你可以使用`@ViewData["Title"]`而不需要转换。

同时使用`ViewData`和`ViewBag`在同一时间工作原理，作为执行混合和匹配读取和写入属性。 在呈现的以下标记：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>About Contoso</title>
    <meta name="description" content="Let us tell you about Contoso's philosophy and mission.">
    ...
```

**ViewData 和 ViewBag 之间的差异的摘要**

* `ViewData`
  * 派生自[ViewDataDictionary](/aspnet/core/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary)，因此它有可能有用，如字典属性`ContainsKey`， `Add`， `Remove`，和`Clear`。
  * 字典中的键是字符串，因此允许有空白。 示例：`ViewData["Some Key With Whitespace"]`
  * 以外的任何类型`string`必须强制转换在要使用的视图`ViewData`。
* `ViewBag`
  * 派生自[DynamicViewData](/aspnet/core/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata)，因此它允许使用点表示法的动态属性创建 (`@ViewBag.SomeKey = <value or object>`)，并没有强制转换是必需的。 语法`ViewBag`得更加快速地将添加到控制器和视图。
  * 易于检查 null 值。 示例：`@ViewBag.Person?.Name`

**何时使用 ViewData 或 ViewBag**

同时`ViewData`和`ViewBag`有同样传递数据在控制器和视图间的信息量很小的有效方法。 要选择哪一到使用 （或两者） 取决于你的组织的首选项或个人首选项。 尽管可以混合和匹配`ViewData`和`ViewBag`对象，该代码是以易于读取和维护时只能选择一个并一致地使用它。 因为二者都在运行时动态解析并且因此容易导致运行时错误，请谨慎使用它们。 一些开发人员完全避免它们。

### <a name="dynamic-views"></a>动态视图

不要声明模型的视图类型使用`@model`但具有传递给它们的模型实例 (例如， `return View(Address);`) 可以动态引用实例的属性：

```cshtml
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

此功能提供的灵活性，但不提供编译保护或智能感知。 如果属性不存在，网页生成失败，则在运行时。

## <a name="more-view-features"></a>详细视图功能

[标记帮助程序](xref:mvc/views/tag-helpers/intro)方便地将服务器端行为添加到现有的 HTML 标记。 使用标记帮助程序可避免编写自定义代码或在你的视图内的帮助器的需求。 标记帮助程序作为属性应用于 HTML 元素，并将忽略通过无法处理它们的编辑器。 这样可编辑，呈现在各种工具中的查看标记。

生成自定义的 HTML 标记可以通过许多内置的 HTML 帮助器。 可以由更复杂的用户界面逻辑[视图组件](xref:mvc/views/view-components)。 查看组件提供相同 SoC 该控制器和视图提供。 它们可以消除对操作和处理使用常见用户界面元素的数据的视图的需要。

ASP.NET 核心的很多其他方面，如视图支持[依赖关系注入](xref:fundamentals/dependency-injection)，允许服务[注入到视图](xref:mvc/views/dependency-injection)。
