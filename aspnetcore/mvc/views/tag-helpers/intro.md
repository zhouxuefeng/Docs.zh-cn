---
title: "在 ASP.NET 核心中的标记帮助程序"
author: rick-anderson
description: "了解标记帮助程序是什么以及如何在 ASP.NET 核心中使用它们。"
keywords: "ASP.NET 核心，标记帮助程序"
ms.author: riande
manager: wpickett
ms.date: 7/14/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/tag-helpers/intro
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 78004aa370cac8b297fd7ede534260c83965ae79
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2017
---
# <a name="introduction-to-tag-helpers-in-aspnet-core"></a>在 ASP.NET 核心中的标记帮助器简介 

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="what-are-tag-helpers"></a>标记帮助器有哪些？

标记帮助程序启用服务器端代码，以参与创建和呈现 Razor 文件中的 HTML 元素。 例如，内置`ImageTagHelper`可以追加到映像名称的版本号。 当映像发生更改时，服务器将生成的映像的新唯一版本，因此，保证客户端以获取当前映像 （而不是过时缓存的映像）。 有许多内置的标记帮助器常见任务-例如，创建窗体、 链接、 加载资产和详细的和甚至更多的可用在公共 GitHub 存储库并作为 NuGet 程序包。 在使用 C# 中，创作标记帮助程序和它们目标基于元素名称、 属性名称或父标记的 HTML 元素。 例如，内置`LabelTagHelper`可以面向 HTML`<label>`元素时`LabelTagHelper`属性均适用。 如果你熟悉[HTML 帮助器](http://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers)，标记帮助程序减少之间 HTML 和 C# 在 Razor 视图中的显式转换。 在许多情况下，HTML 帮助器向特定的标记帮助器，提供的备用方法，但务必要识别标记帮助程序不替换 HTML 帮助器并不是每个 HTML 帮助器标记帮助器。 [标记帮助程序与 HTML 帮助器相比](#tag-helpers-compared-to-html-helpers)介绍更多详细信息中的差异。

## <a name="what-tag-helpers-provide"></a>标记帮助程序提供的内容

**HTML 友好开发体验**对于在多数情况下，使用标记帮助程序 Razor 标记看起来像标准 HTML。 与 HTML/CSS/JavaScript conversant 前端设计器可以编辑 Razor，而无需学习 C# Razor 语法。

**用于创建 HTML 和 Razor 标记的丰富智能感知环境**这是到 HTML 帮助器，在 Razor 视图中的标记的服务器端创建与前一方法尖锐相反。 [标记帮助程序与 HTML 帮助器相比](#tag-helpers-compared-to-html-helpers)介绍更多详细信息中的差异。 [标记帮助器的 IntelliSense 支持](#intellisense-support-for-tag-helpers)还是解释了 IntelliSense 环境。 具有 Razor C# 语法的经验的甚至开发人员提高工作效率比编写 C# Razor 标记中使用标记帮助程序。

**方法使你更高效且能够生成更为可靠、 可靠和可维护的代码使用在服务器上的信息只能**例如，从历史上看上更新映像箴言是在更改时更改映像的名称图像。 映像应激进缓存出于性能原因，并更改映像的名称，除非你存在的风险获取陈旧副本的客户端。 从历史上看，映像中进行了编辑后，名称必须更改并且 web 应用中的图像的每次引用所需更新。 这不只是非常人工密集型的它也是 （你可能缺少引用、 意外输入错误字符串，等等），而且容易出错内置`ImageTagHelper`可以自动执行此操作为你。 `ImageTagHelper`可以追加版本数字到映像名称，因此当该图像发生更改时，服务器自动生成的映像的新唯一版本。 保证客户端都获得当前的图像。 通过使用实质上是免费的可靠性和人工节约附送`ImageTagHelper`。

大部分内置标记帮助器目标现有 HTML 元素，而为的元素提供服务器端属性。 例如，`<input>`元素中的视图中的许多使用*视图/帐户*文件夹包含`asp-for`属性，它将指定的模型属性的名称提取到呈现的 HTML。 以下 Razor 标记：

```html
<label asp-for="Email"></label>
```

生成的以下 HTML:

```html
<label for="Email">Email</label>
```

`asp-for`属性可通过`For`中的属性`LabelTagHelper`。 请参阅[创作标记帮助程序](authoring.md)有关详细信息。

## <a name="managing-tag-helper-scope"></a>管理标记帮助器作用域

标记帮助器作用域控制的组合来`@addTagHelper`， `@removeTagHelper`，与"！"选择退出字符。

<a name="add-helper-label"></a>

### <a name="addtaghelper-makes-tag-helpers-available"></a>`@addTagHelper`提供标记帮助程序

如果你创建新的 ASP.NET 核心 web 应用名为*AuthoringTagHelpers* （不带身份验证），以下*Views/_ViewImports.cshtml*文件将添加到你的项目：

[!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=2&range=2-3)]

`@addTagHelper`指令使标记帮助程序可供查看。 在此示例中，视图文件仅*Views/_ViewImports.cshtml*，默认情况下由继承中的所有视图文件*视图*文件夹和子目录; 提供标记帮助程序。 上面的代码中使用通配符语法 ("\*") 来指定在指定的程序集中的所有标记帮助程序 (*Microsoft.AspNetCore.Mvc.TagHelpers*) 将可供在每个视图文件*视图*目录或子目录。 之后的第一个参数`@addTagHelper`指定标记帮助程序，以加载 (我们将使用"\*"的所有标记帮助程序)，和第二个参数"Microsoft.AspNetCore.Mvc.TagHelpers"指定包含标记帮助程序的程序集。 *Microsoft.AspNetCore.Mvc.TagHelpers*是内置的 ASP.NET 核心标记帮助程序的程序集。

公开所有在此项目中的标记帮助程序 (这将创建名为程序集*AuthoringTagHelpers*)，则将使用以下命令：

[!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=3)]

如果你的项目包含`EmailTagHelper`具有默认命名空间 (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`)，你可以提供标记帮助器的完全限定的名称 (FQN):

```cshtml
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

若要将标记帮助器添加到使用 FQN 的视图，你首先添加 FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`)，，然后是程序集名称 (*AuthoringTagHelpers*)。 大多数开发人员更愿意使用"\*"通配符语法。 通配符语法，可插入通配符"\*"FQN 中的后缀。 例如，任何以下指令将`EmailTagHelper`:

```csharp
@addTagHelper AuthoringTagHelpers.TagHelpers.E*, AuthoringTagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.Email*, AuthoringTagHelpers
```

如前所述，添加`@addTagHelper`指令至*Views/_ViewImports.cshtml*文件使标记帮助器可用于中的所有视图文件*视图*目录及其子目录。 你可以使用`@addTagHelper`指令特定视图文件，如果你想要参加公开标记帮助器到仅这些视图中。

<a name="remove-razor-directives-label"></a>

### <a name="removetaghelper-removes-tag-helpers"></a>`@removeTagHelper`删除标记帮助程序

`@removeTagHelper`具有相同的两个参数为`@addTagHelper`，它会删除以前添加标记帮助器。 例如，`@removeTagHelper`应用于特定视图中删除指定的标记帮助器从视图。 使用`@removeTagHelper`中*Views/Folder/_ViewImports.cshtml*从视图中的所有文件中都移除的指定的标记帮助器*文件夹*。

### <a name="controlling-tag-helper-scope-with-the-viewimportscshtml-file"></a>控制具有标记帮助器作用域*_ViewImports.cshtml*文件

你可以添加*_ViewImports.cshtml*任何视图文件夹中，和视图引擎应用到指令从这两个文件和*Views/_ViewImports.cshtml*文件。 如果你添加一个空*Views/Home/_ViewImports.cshtml*文件以获取*主页*视图，则不存在任何更改因为*_ViewImports.cshtml*文件是累加性。 任何`@addTagHelper`指令将添加到*Views/Home/_ViewImports.cshtml*文件 (位于不在默认*Views/_ViewImports.cshtml*文件) 将公开到视图这些标记帮助器仅在*主页*文件夹。

<a name="opt-out"></a>

### <a name="opting-out-of-individual-elements"></a>选择退出各个元素

你可以禁用与标记帮助器选择退出字符元素级别的标记帮助器 ("！")。 例如，`Email`中禁用验证`<span>`标记帮助器选择退出字符：

```csharp
<!span asp-validation-for="Email" class="text-danger"></!span>
```

必须将标记帮助器选择退出字符应用于的开始和结束标记。 （Visual Studio 编辑器会自动向退出字符结束标记时你添加到的开始标记）。 添加退出字符后和标记帮助器属性的元素将不再显示在不同的字体。

<a name="prefix-razor-directives-label"></a>

### <a name="using-taghelperprefix-to-make-tag-helper-usage-explicit"></a>使用`@tagHelperPrefix`以便显式标记帮助程序使用情况

`@tagHelperPrefix`指令允许你指定的标记前缀字符串来启用标记帮助器支持和明确标记帮助程序使用情况。 例如，可以添加到以下标记*Views/_ViewImports.cshtml*文件：

```html
@tagHelperPrefix th:
```
在下面的代码图中，标记帮助器前缀设置为`th:`，因此只有这些元素使用前缀`th:`支持标记帮助程序 （已启用标记帮助器的元素具有不同的字体）。 `<label>`和`<input>`元素具有标记帮助器前缀和标记帮助器支持，而是`<span>`元素不一样。

![图像](intro/_static/thp.png)

相同的层次结构规则适用于`@addTagHelper`也适用于`@tagHelperPrefix`。

## <a name="intellisense-support-for-tag-helpers"></a>标记帮助器的 IntelliSense 支持

Visual Studio 中创建新的 ASP.NET web 应用程序，它会添加 NuGet 包"Microsoft.AspNetCore.Razor.Tools"。 这是添加标记帮助程序工具的包。

请考虑编写 HTML`<label>`元素。 你输入时，就会立即`<l`在 Visual Studio 编辑器中，IntelliSense 将显示匹配的元素：

![图像](intro/_static/label.png)

你不仅获取 HTML 帮助，但图标 ("@" symbol with "<>"下)。

![图像](intro/_static/tagSym.png)

为为目标的标记帮助程序，请标识此元素。 纯 HTML 元素 (如`fieldset`) 显示"<>"图标。

纯 HTML`<label>`标记棕色的进度的字体，以红色的属性中显示 （与默认 Visual Studio 颜色主题） 的 HTML 标记，并以蓝色的属性值。

![图像](intro/_static/LableHtmlTag.png)

输入后`<label`，IntelliSense 将列出可用的 HTML/CSS 属性和标记帮助程序目标属性：

![图像](intro/_static/labelattr.png)

IntelliSense 语句结束允许您输入 tab 键完成语句的选定值：

![图像](intro/_static/stmtcomplete.png)

只要输入标记帮助器属性，标记和特性字体更改。 使用默认 Visual Studio"蓝色"或"Light"颜色主题，此字体是粗体紫色。 如果你使用的"深色"主题此字体是粗体蓝绿色。 本文档中的映像已使用默认主题执行。

![图像](intro/_static/labelaspfor2.png)

你可以输入 Visual Studio *CompleteWord*快捷方式 (Ctrl + 空格键是[默认](https://docs.microsoft.com/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio)在双引号 ("")，，您现在在 C# 中，就像你将 C# 类中一样。 IntelliSense 显示页面模型上的所有方法和属性。 方法和属性有该属性类型是因为`ModelExpression`。 在下图中，我将编辑`Register`视图中，因此`RegisterViewModel`可用。

![图像](intro/_static/intellemail.png)

IntelliSense 将列出的属性和方法可用于在页上的模型。 丰富智能感知环境可帮助你选择的 CSS 类：

![图像](intro/_static/iclass.png)

![图像](intro/_static/intel3.png)

## <a name="tag-helpers-compared-to-html-helpers"></a>标记帮助器相比 HTML 帮助器

标记帮助程序将附加到在 Razor 视图中的 HTML 元素时[HTML 帮助器](http://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers)如方法穿插 HTML 在 Razor 视图中调用。 请考虑使用 CSS 类"标题"创建一个 HTML 标签的以下 Razor 标记：

```html
@Html.Label("FirstName", "First Name:", new {@class="caption"})
```

在 (`@`) 符号通知 Razor 这是代码的开始位置。 下面的两个参数 ("FirstName"和"第一个名称:") 都是字符串，因此[IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense)无法提供帮助。 最后一个自变量：

```html
new {@class="caption"}
```

用于表示属性的匿名对象。 因为**类**是保留的关键字在 C# 中，你使用`@`符号以强制 C#，以解释"@class="为符号 （属性名称）。 到前端的设计器 (人熟悉 HTML/CSS/JavaScript 和其他客户端技术，但不是熟悉 C# 和 Razor)，行的大多数是外部角色。 从 IntelliSense 没有帮助，必须编写的整个行。

使用`LabelTagHelper`，相同的标记可以编写为：

![图像](intro/_static/label2.png)

使用标记帮助器版本，你输入时，就会立即`<l`在 Visual Studio 编辑器中，IntelliSense 将显示匹配的元素：

![图像](intro/_static/label.png)

IntelliSense 可帮助您编写的整个行。 `LabelTagHelper`也默认为设置的内容`asp-for`到"名字"; 属性值 ("FirstName")它将转换为句子组成与每个新的大小写字母的出现位置空间的属性名称混合使用大小写的属性。 在下列标记中：

![图像](intro/_static/label2.png)

生成：

```html
<label class="caption" for="FirstName">First Name</label>
```

如果你将内容添加到未使用 camel 大小写形式句子大小写形式内容`<label>`。 例如: 

![图像](intro/_static/1stName.png)

生成：

```html
<label class="caption" for="FirstName">Name First</label>
```

下面的代码图显示的窗体部分*Views/Account/Register.cshtml* Razor 视图从 Visual Studio 2015 中包含旧 ASP.NET 4.5.x MVC 模板生成。

![图像](intro/_static/regCS.png)

Visual Studio 编辑器会显示包含一个灰色背景的 C# 代码。 例如，`AntiForgeryToken`的 HTML 帮助器：

```html
@Html.AntiForgeryToken()
```

将显示灰色背景。 注册视图中的大多数是标记的 C#。 与等效的方法使用标记帮助程序进行的比较：

![图像](intro/_static/regTH.png)

标记是得更简洁且更轻松地读取、 编辑和维护的 HTML 帮助器方法。 C# 代码都会减少到所需要了解的有关服务器的最低要求。 Visual Studio 编辑器会显示不同的字体中标记帮助程序目标的标记。

请考虑*电子邮件*组：

[!code-csharp[Main](intro/sample/Register.cshtml?range=12-18)]

每个"asp-"属性具有值为"电子邮件"，但是"Email"不是字符串。 在此上下文中，"Email"为 C# 模型表达式属性`RegisterViewModel`。

Visual Studio 编辑器可帮助您编写**所有**的寄存器窗体，而 Visual Studio 中的 HTML 帮助器方法的代码的大部分提供没有帮助的标记帮助程序方法中的标记。 [标记帮助器的 IntelliSense 支持](#intellisense-support-for-tag-helpers)进入使用在 Visual Studio 编辑器中的标记帮助器的详细信息。

## <a name="tag-helpers-compared-to-web-server-controls"></a>与 Web 服务器控件的标记帮助程序

* 标记帮助程序不属于他们正在与; 关联的元素他们只需参与的元素和内容的呈现。 ASP.NET[控件 Web 服务器控件](https://msdn.microsoft.com/library/7698y1f0.aspx)是声明并调用在页面上。

* [Web 服务器控件](https://msdn.microsoft.com/library/zsyt68f1.aspx)具有重要的生命周期，难以在开发和调试。

* Web 服务器控件，可以使用的客户端控件将功能添加到客户端文档对象模型 (DOM) 元素。 标记帮助程序有任何 dom。

* Web 服务器控件包括浏览器自动检测。 标记帮助程序具有浏览器没有的知识。

* 多个标记帮助程序可以在同一元素上执行操作 (请参阅[避免标记帮助器冲突](https://docs.microsoft.com/aspnet/core/mvc/views/tag-helpers/authoring#avoiding-tag-helper-conflicts)) 时通常不能撰写 Web 服务器控件。

* 标记帮助程序可以修改的标记和它们在作用于的 HTML 元素的内容，但不要直接修改任何其他页上。 Web 服务器控件具有更具体的作用域，并且可以执行影响你页; 的其他部件的操作启用意外的副作用。

* Web 服务器控件使用的类型转换器将字符串转换为对象。 通过标记帮助程序，您可以以本机方式在 C# 中，因此无需进行类型转换。

* Web 服务器控件使用[System.ComponentModel](https://docs.microsoft.com/dotnet/api/system.componentmodel)实现组件和控件的运行时和设计时行为。 `System.ComponentModel`包括基类和实现属性和类型转换器、 绑定到数据源，以及授权组件的接口。 相比之下，到标记帮助程序，通常派生自`TagHelper`，和`TagHelper`基类公开只有两种方法，`Process`和`ProcessAsync`。

## <a name="customizing-the-tag-helper-element-font"></a>自定义标记帮助器元素字体

你可以自定义的字体和着色从**工具** > **选项** > **环境** > **字体和颜色**:

![图像](intro/_static/fontoptions2.png)

## <a name="additional-resources"></a>其他资源

* [创作标记帮助程序](authoring.md)
* [使用窗体](../working-with-forms.md)
* [在 GitHub 上的 TagHelperSamples](https://github.com/dpaquette/TagHelperSamples)包含用于处理的标记帮助程序示例[Bootstrap](http://getbootstrap.com/)。

<!--
* [Working with Forms ](xref:mvc/views/working-with-forms)
-->
