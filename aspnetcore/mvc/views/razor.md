---
title: "有关 ASP.NET 核心的 razor 语法参考"
author: rick-anderson
description: "详细信息 Razor 语法"
keywords: "ASP.NET 核心 Razor"
ms.author: riande
manager: wpickett
ms.date: 07/04/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/razor
ms.openlocfilehash: 066fe3b2486c63bd4de2ccb865ad432a67846d77
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2017
---
# <a name="razor-syntax-for-aspnet-core"></a>ASP.NET 核心的 razor 语法

通过[Taylor Mullen](https://twitter.com/ntaylormullen)和[Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="what-is-razor"></a>什么是 Razor？

Razor 是才能嵌入到网页的服务器基于代码的标记语法。 Razor 语法包含 Razor 标记，C# 和 HTML。 通常包含 Razor 文件具有*.cshtml*文件扩展名。

## <a name="rendering-html"></a>呈现 HTML

默认 Razor 语言为 HTML。 从 Razor 呈现 HTML 在某一 HTML 文件中是没有什么不同。 使用以下标记 Razor 文件：

```html
<p>Hello World</p>
   ```

呈现为保持不变`<p>Hello World</p>`服务器。

## <a name="razor-syntax"></a>Razor 语法

Razor 支持 C#，并使用`@`转换到 C# 的 HTML 中的符号。 Razor C# 表达式的计算结果，并将它们呈现的 HTML 输出中。 Razor 可以从 HTML 转换为 C# 或 Razor 特定标记。 当`@`符号后跟[Razor 保留关键字](#razor-reserved-keywords)转为 Razor 特定标记，否则它可以转换为纯 C#。

<a name=escape-at-label></a>

HTML 包含`@`符号可能需要使用第二个进行转义`@`符号。 例如: 

```html
<p>@@Username</p>
   ```

将会呈现在以下 HTML:

```html
<p>@Username</p>
   ```

<a name=razor-email-ref></a>

HTML 特性和内容包含电子邮件地址不处理`@`转换字符的形式的符号。

   `<a href="mailto:Support@contoso.com">Support@contoso.com</a>`

## <a name="implicit-razor-expressions"></a>隐式 Razor 表达式

隐式 Razor 表达式开头`@`跟 C# 代码。 例如: 

```html
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

除了 C#`await`关键字隐式表达式不能包含空格。 例如，你可以只要 C# 语句已清除结束 intermingle 空格：

```html
<p>@await DoSomething("hello", "world")</p>
```

## <a name="explicit-razor-expressions"></a>显式 Razor 表达式

显式 Razor 表达式组成 @ 符号与平衡括号。 例如，若要呈现最后一周的时间：

```html
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

中的所有内容 @ （） 括号是计算并呈现到输出。

隐式表达式通常不能包含空格。 例如，在下面的代码中，一周不是从当前时间减去：

[!code-html[Main](razor/sample/Views/Home/Contact.cshtml?range=20)]

这将会呈现以下 HTML:

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
   ```

可以使用显式表达式将文本与表达式结果串联起来：

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "none", "highlight_args": {"hl_lines": [5]}} -->

```none
@{
    var joe = new Person("Joe", 33);
 }

<p>Age@(joe.Age)</p>
```

如果没有显式的表达式，`<p>Age@joe.Age</p>`将被视为电子邮件地址和`<p>Age@joe.Age</p>`将呈现。 在作为显式表达式，写入时`<p>Age33</p>`呈现。

<a name=expression-encoding-label></a>

## <a name="expression-encoding"></a>表达式编码

C# 表达式，其计算结果为字符串是 HTML 编码。 C# 表达式的计算结果为`IHtmlContent`呈现直接通过*IHtmlContent.WriteTo*。 C# 表达式不计算结果为*IHtmlContent*转换为字符串 (由*ToString*) 和编码然后将它们呈现。 例如，以下 Razor 标记：

```html
@("<span>Hello World</span>")
   ```

将此呈现 HTML:

```html
&lt;span&gt;Hello World&lt;/span&gt;
   ```

该浏览器将呈现为：

`<span>Hello World</span>`

`HtmlHelper``Raw`输出不是编码而呈现为 HTML 标记。

>[!WARNING]
> 使用`HtmlHelper.Raw`未净化的用户输入会带来安全风险。 用户输入可能包含恶意 JavaScript 或其他攻击。 对用户输入会很困难，请不要使用`HtmlHelper.Raw`用户输入。

以下 Razor 标记：

```html
@Html.Raw("<span>Hello World</span>")
   ```

将此呈现 HTML:

```html
<span>Hello World</span>
   ```

<a name=razor-code-blocks-label></a>

## <a name="razor-code-blocks"></a>Razor 代码块

Razor 代码块开头`@`，并且通过包括`{}`。 与不同的是表达式，则不会呈现代码块内的 C# 代码。 代码块中和表达式 Razor 页共享相同的作用域和按顺序定义 （即，在代码块中的声明将更高版本的代码块和表达式的作用域中）。

```none
@{
    var output = "Hello World";
}

<p>The rendered result: @output</p>
```

将会呈现在：

```html
<p>The rendered result: Hello World</p>
   ```

<a name=implicit-transitions-label></a>

### <a name="implicit-transitions"></a>隐式转换

代码块中的默认语言为 C# 中，而是可以转换回 HTML。 代码块中的 HTML 将转换回呈现 HTML:

```none
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

<a name=explicit-delimited-transition-label></a>

### <a name="explicit-delimited-transition"></a>带分隔符的显式转换

若要定义一个小节应呈现的 HTML 代码块，括住的字符要呈现具有 Razor`<text>`标记：

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "none", "highlight_args": {"hl_lines": [4]}} -->

```none
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

通常，当你想要呈现不括在的 HTML 标记的 HTML 时，可使用此方法。 没有 HTML 或 Razor 标记，则会收到 Razor 运行时错误。

<a name=explicit-line-transition-with-label></a>

### <a name="explicit-line-transition-with-"></a>使用显式行转换`@:`

若要在代码块内以 html 格式呈现整个行的其余内容，使用`@:`语法：

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "none", "highlight_args": {"hl_lines": [4]}} -->

```none
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

而无需`@:`在上面的代码中，你会收到一个运行时错误 Razor。

<a name=control-structures-razor-label></a>

## <a name="control-structures"></a>控件结构

控制结构是一种扩展的代码块。 所有方面的代码块 （过渡到标记中，内联 C#） 也都适用于以下结构。

### <a name="conditionals-if-else-if-else-and-switch"></a>条件语句`@if`， `else if`，`else`和`@switch`

`@if`系列控制代码的运行时：

```none
@if (value % 2 == 0)
{
    <p>The value was even</p>
}
```

`else`和`else if`不需要`@`符号：

```none
@if (value % 2 == 0)
{
    <p>The value was even</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value was not large and is odd.</p>
}
```

你可以使用 switch 语句如下：

```none
@switch (value)
{
    case 1:
        <p>The value is 1!</p>
        break;
    case 1337:
        <p>Your number is 1337!</p>
        break;
    default:
        <p>Your number was not 1 or 1337.</p>
        break;
}
```

### <a name="looping-for-foreach-while-and-do-while"></a>循环`@for`， `@foreach`， `@while`，和`@do while`

你可以呈现包含循环的控制语句的模板化 HTML。 例如，若要呈现的人员列表：

```none
@{
    var people = new Person[]
    {
          new Person("John", 33),
          new Person("Doe", 41),
    };
}
```

你可以使用任何以下循环语句：

`@for`

```none
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@foreach`

```none
@foreach (var person in people)
{
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@while`

```none
@{ var i = 0; }
@while (i < people.Length)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
}
```

`@do while`

```none
@{ var i = 0; }
@do
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
} while (i < people.Length);
```

### <a name="compound-using"></a>复合`@using`

使用 C# 语句用于确保释放对象。 在 Razor 此相同的机制可以用于创建包含其他内容的 HTML 帮助器。 例如，我们可以利用 HTML 帮助器呈现窗体标记与`@using`语句：

```none
@using (Html.BeginForm())
{
    <div>
        email:
        <input type="email" id="Email" name="Email" value="" />
        <button type="submit"> Register </button>
    </div>
}
```

你还可以执行类似上面使用的作用域级别操作[标记帮助程序](tag-helpers/index.md)。

### <a name="try-catch-finally"></a>`@try`, `catch`, `finally`

异常处理是类似于 C#:

[!code-html[Main](razor/sample/Views/Home/Contact7.cshtml)]

### `@lock`

Razor 具有的功能来保护与 lock 语句的关键部分：

```none
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a>注释

Razor 支持 C# 和 HTML 注释。 以下标记：

```none
@{
    /* C# comment. */
    // Another C# comment.
}
<!-- HTML comment -->
```

作为由服务器呈现：

```none
<!-- HTML comment -->
```

在呈现页之前，将服务器中删除 razor 注释。 使用 razor`@*  *@`来分隔注释。 下面的代码是加上注释，以便服务器将不会呈现任何标记：

```none
 @*
 @{
     /* C# comment. */
     // Another C# comment.
 }
 <!-- HTML comment -->
*@
```

<a name=razor-directives-label></a>

## <a name="directives"></a>指令

保留的关键字以下的隐式表达式由表示 razor 指令`@`符号。 通常，一个指令将更改分析页面的方式，或者启用 Razor 页内的不同功能。

了解如何 Razor 生成视图的代码将更加轻松地了解指令的工作原理。 Razor 页用于生成 C# 文件中。 例如，此 Razor 页：

[!code-html[Main](razor/sample/Views/Home/Contact8.cshtml)]

生成类似于下面的类：

```csharp
public class _Views_Something_cshtml : RazorPage<dynamic>
{
    public override async Task ExecuteAsync()
    {
        var output = "Hello World";

        WriteLiteral("/r/n<div>Output: ");
        Write(output);
        WriteLiteral("</div>");
    }
}
```

[查看生成的视图的 Razor C# 类](#razor-customcompilationservice-label)说明如何查看此生成的类。

### `@using`

`@using`指令将添加 c#`using`指令至生成的 razor 页：

[!code-html[Main](razor/sample/Views/Home/Contact9.cshtml)]

### `@model`

`@model`指令指定的模型传递给 Razor 页的类型。 它使用以下语法：

```none
@model TypeNameOfModel
   ```

例如，如果使用单个用户帐户创建的 ASP.NET 核心 MVC 应用*Views/Account/Login.cshtml* Razor 视图包含以下模型声明：

```csharp
@model LoginViewModel
   ```

在前面的类示例中，生成的类继承自`RazorPage<dynamic>`。 通过添加`@model`控制什么继承。 例如

```csharp
@model LoginViewModel
   ```

生成下面的类

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
   ```

Razor 页公开`Model`属性访问的模型传递到页。

```html
<div>The Login Email: @Model.Email</div>
   ```

`@model`指令指定此属性的类型 (通过指定`T`中`RazorPage<T>`页生成的类派生自)。 如果没有指定`@model`指令`Model`属性的类型将为`dynamic`。 模型的值从控制器传递到该视图。 请参阅[强类型模型和@model关键字](../../tutorials/first-mvc-app/adding-model.md#strongly-typed-models-keyword-label)有关详细信息。

### `@inherits`

`@inherits`指令可完全控制 Razor 页继承的类：

```none
@inherits TypeNameOfClassToInheritFrom
   ```

例如，假设我们有以下自定义 Razor 页类型：

[!code-csharp[Main](razor/sample/Classes/CustomRazorPage.cs)]

将生成以下 Razor `<div>Custom text: Hello World</div>`。

[!code-html[Main](razor/sample/Views/Home/Contact10.cshtml)]

不能使用`@model`和`@inherits`同一页面上。 你可以`@inherits`中*_ViewImports.cshtml* Razor 页导入的文件。 例如，如果在 Razor 视图导入以下*_ViewImports.cshtml*文件：

[!code-html[Main](razor/sample/Views/_ViewImportsModel.cshtml)]

以下的强类型化的 Razor 页面

[!code-html[Main](razor/sample/Views/Home/Login1.cshtml)]

生成此 HTML 标记：

```none
<div>The Login Email: Rick@contoso.com</div>
<div>Custom text: Hello World</div>
```

当传递"[Rick@contoso.com](mailto:Rick@contoso.com)"模型中：

   请参阅[布局](layout.md)了解详细信息。

### `@inject`

`@inject`指令使你能够注入从服务你[服务容器](../../fundamentals/dependency-injection.md)到使用你 Razor 页。 请参阅[到视图的依赖关系注入](dependency-injection.md)。

<a name="functions"></a>

### `@functions`

`@functions`指令，可将函数级的内容添加到你 Razor 页。 语法为：

```none
@functions { // C# Code }
   ```

例如: 

[!code-html[Main](razor/sample/Views/Home/Contact6.cshtml)]

生成以下的 HTML 标记：

```none
<div>From method: Hello</div>
   ```

生成 Razor 的 C# 如下所示：

[!code-csharp[Main](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

### `@section`

`@section`结合使用指令[布局页](layout.md)启用视图呈现中呈现的 HTML 页面的不同部分的内容。 请参阅[部分](layout.md#layout-sections-label)有关详细信息。

## <a name="tag-helpers"></a>标记帮助程序

以下[标记帮助程序](tag-helpers/index.md)指令中提供的链接详细介绍。

* [@addTagHelper](tag-helpers/intro.md#add-helper-label)
* [@removeTagHelper](tag-helpers/intro.md#remove-razor-directives-label)
* [@tagHelperPrefix](tag-helpers/intro.md#prefix-razor-directives-label)

<a name=razor-reserved-keywords-label></a>

## <a name="razor-reserved-keywords"></a>Razor 保留关键字

### <a name="razor-keywords"></a>Razor 关键字

* 页 （需要 ASP.NET Core 2.0 及更高版本）
* 函数
* 继承
* 模型
* section
* 帮助器 （不支持 ASP.NET Core。）

可以使用转义 razor 关键字`@(Razor Keyword)`，例如`@(functions)`。 请参阅下面的完整示例。

### <a name="c-razor-keywords"></a>C# Razor 关键字

* case
* do
* default
* for
* foreach
* if
* else
* 锁定
* switch
* try
* catch
* finally
* using
* while

C# Razor 关键字需要双使用转义`@(@C# Razor Keyword)`，例如`@(@case)`。 第一个`@`转义 Razor 分析器中，第二个`@`转义 C# 分析器。 请参阅下面的完整示例。

### <a name="reserved-keywords-not-used-by-razor"></a>不使用 Razor 的保留的关键字

* namespace
* 类

<a name=razor-customcompilationservice-label></a>

## <a name="viewing-the-razor-c-class-generated-for-a-view"></a>查看生成的视图的 Razor C# 类

将下面的类添加到 ASP.NET 核心 MVC 项目：

[!code-csharp[Main](razor/sample/Services/CustomCompilationService.cs)]

重写`ICompilationService`由 MVC 具有上述类; 添加

[!code-csharp[Main](razor/sample/Startup.cs?highlight=4&range=29-33)]

在上设置断点`Compile`方法`CustomCompilationService`和视图，以及`compilationContent`。

![CompilationContent 文本可视化工具视图](razor/_static/tvr.png)

<a name="case"></a>
## <a name="view-lookups-and-case-sensitivity"></a>视图的查找，并区分大小写

Razor 视图引擎视图执行区分大小写的查找。 但是，由基础源确定实际查找：

* 文件基于的源： 

    * 在操作系统上使用区分大小写的文件系统 （如 Windows)，物理文件提供程序查找是区分大小写的。 例如`return View("Test")`将导致`/Views/Home/Test.cshtml`，`/Views/home/test.cshtml`和所有其他大小写变体可能会发现。
    * 在区分大小写的文件系统，其中包括 Linux，OSX 和`EmbeddedFileProvider`，查找是区分大小写。 例如，`return View("Test")`将专门查找`/Views/Home/Test.cshtml`。
        
* 预编译的视图：

   * ASP.Net 核心 2.0 和更高版本，查找预编译视图是在所有操作系统上不区分。 行为是在 Windows 上的物理文件提供程序的行为相同。 
   注意： 如果两个预编译的视图仅大小写不同，查找的结果是不确定的。

开发人员建议以匹配到区域，控制器和操作的名称的大小写的文件和目录名称的大小写。 这将确保你的部署保持不可知的基础的文件系统。
