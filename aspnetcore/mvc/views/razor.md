---
title: "有关 ASP.NET 核心的 razor 语法参考"
author: rick-anderson
description: "了解如何将基于服务器的代码嵌入到网页的 Razor 标记语法。"
keywords: "ASP.NET 核心，Razor，Razor 指令"
ms.author: riande
manager: wpickett
ms.date: 10/18/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/razor
ms.openlocfilehash: e3c3149254d602db1fcc6d42360690be026189a5
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
# <a name="razor-syntax-for-aspnet-core"></a>ASP.NET 核心的 razor 语法

通过[Rick Anderson](https://twitter.com/RickAndMSFT)， [Luke Latham](https://github.com/guardrex)， [Taylor Mullen](https://twitter.com/ntaylormullen)，和[Dan Vicarel](https://github.com/Rabadash8820)

Razor 是用于将基于服务器的代码嵌入到网页的标记的语法。 Razor 语法组成 Razor 标记、 C# 和 HTML。 通常包含 Razor 文件具有*.cshtml*文件扩展名。

## <a name="rendering-html"></a>呈现 HTML

默认 Razor 语言为 HTML。 呈现 HTML Razor 标记是呈现 HTML 中，某一 HTML 文件没有什么不同。  HTML 标记中的*.cshtml* Razor 文件呈现服务器保持不变。

## <a name="razor-syntax"></a>Razor 语法

Razor 支持 C#，并使用`@`转换到 C# 的 HTML 中的符号。 Razor C# 表达式的计算结果，并将它们呈现的 HTML 输出中。

当`@`符号后跟[Razor 保留关键字](#razor-reserved-keywords)，它可以转换为 Razor 特定于标记。 否则，它将转换为纯 C#。

要转义`@`符号在 Razor 标记中，请使用第二个`@`符号：

```cshtml
<p>@@Username</p>
```

代码以 HTML 格式呈现与单个`@`符号：

```html
<p>@Username</p>
```

HTML 特性和内容包含电子邮件地址不处理`@`转换字符的形式的符号。 下面的示例中的电子邮件地址是不触及 Razor 分析：

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a>隐式 Razor 表达式

隐式 Razor 表达式开头`@`跟 C# 代码：

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

除了 C#`await`关键字，隐式表达式不能包含空格。 如果 C# 语句已清除结束，则可以混合空格：

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

隐式表达式**无法**包含 C# 泛型，括号内的字符作为 (`<>`) 都会被解释为 HTML 标记。 下面的代码是**不**有效：

```cshtml
<p>@GenericMethod<int>()</p>
```

前面的代码生成编译器错误类似于以下项之一：

 * 未关闭的"int"元素。  所有元素都必须为自结束或具有匹配的结束标记。
 *  无法将方法组 GenericMethod 为非委托 object 类型的转换。 是否希望调用的方法？ 
 
泛型方法调用必须包装在[显式 Razor 表达式](#explicit-razor-expressions)或[Razor 代码块](#razor-code-blocks)。 此限制不适用于*.vbhtml* Razor 文件，因为 Visual Basic 语法将泛型类型参数，而不是括号周围的括号。

## <a name="explicit-razor-expressions"></a>显式 Razor 表达式

显式 Razor 表达式组成`@`与平衡括号的符号。 若要呈现最后一周的时间，请使用以下 Razor 标记：

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

中的任何内容`@()`括号是计算并呈现到输出。

在上一节中所述的隐式表达式通常不能包含空格。 在下面的代码中，从当前时间中减去不一周：

[!code-cshtml[Main](razor/sample/Views/Home/Contact.cshtml?range=17)]

代码将以下 HTML 呈现：

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

显式表达式可以用于将文本与表达式结果串联起来：

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

如果没有显式的表达式，`<p>Age@joe.Age</p>`视为电子邮件地址，和`<p>Age@joe.Age</p>`呈现。 在作为显式表达式，写入时`<p>Age33</p>`呈现。


显式表达式可以用于呈现输出中的泛型方法*.cshtml*文件。 在隐式表达式中，括号内的字符 (`<>`) 都会被解释为 HTML 标记。 以下标记是**不**有效 Razor:

```cshtml
<p>@GenericMethod<int>()</p>
```

前面的代码生成编译器错误类似于以下项之一：

 * 未关闭的"int"元素。  所有元素都必须为自结束或具有匹配的结束标记。
 *  无法将方法组 GenericMethod 为非委托 object 类型的转换。 是否希望调用的方法？ 
 
 以下标记显示正确的方式写入此代码。  作为显式表达式编写的代码：

```cshtml
<p>@(GenericMethod<int>())</p>
```

注意： 此限制不适用于*.vbhtml* Razor 文件。  与*.vbhtml* Razor 文件，Visual Basic 语法将泛型类型参数，而不是括号周围的括号。

## <a name="expression-encoding"></a>表达式编码

C# 表达式，其计算结果为字符串是 HTML 编码。 C# 表达式的计算结果为`IHtmlContent`呈现直接通过`IHtmlContent.WriteTo`。 C# 表达式不计算结果为`IHtmlContent`转换为字符串`ToString`它们在呈现之前进行编码。

```cshtml
@("<span>Hello World</span>")
```

代码将以下 HTML 呈现：

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

HTML 的浏览器所示：

```
<span>Hello World</span>
```

`HtmlHelper.Raw`输出没有编码，但呈现为 HTML 标记。

> [!WARNING]
> 使用`HtmlHelper.Raw`未净化的用户输入会带来安全风险。 用户输入可能包含恶意 JavaScript 或其他攻击。 对用户输入会很困难。 避免使用`HtmlHelper.Raw`需要用户输入。

```cshtml
@Html.Raw("<span>Hello World</span>")
```

代码将以下 HTML 呈现：

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a>Razor 代码块

Razor 代码块开头`@`，并且通过包括`{}`。 与不同的是表达式，C# 代码块内的代码不呈现。 代码块和在视图中的表达式共享相同的作用域和按顺序定义：

```cshtml
@{
    var quote = "The future depends on what you do today. - Mahatma Gandhi";
}

<p>@quote</p>

@{
    quote = "Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.";
}

<p>@quote</p>
```

代码将以下 HTML 呈现：

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

### <a name="implicit-transitions"></a>隐式转换

代码块中的默认语言为 C# 中，而是 Razor 页可以转换回 HTML:

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a>带分隔符的显式转换

若要定义的子部分的应呈现的 HTML 代码块，周围呈现的字符与 Razor **\<文本 >**标记：

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

使用此方法来呈现不包围的 HTML 标记的 HTML。 没有 HTML 或 Razor 标记，Razor 运行时错误时发生。

**\<文本 >**标记可用于在呈现内容时控制空白：

* 仅之间的内容**\<文本 >**标记的呈现。 
* 之前或之后有空格**\<文本 >**的 HTML 输出中将显示标记。

### <a name="explicit-line-transition-with-"></a>显式行转换与 @:

若要在代码块内以 html 格式呈现整个行的其余内容，使用`@:`语法：

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

而无需`@:`在代码中，生成 Razor 运行时错误。

警告： 额外`@`Razor 文件中的字符会导致编译器错误语句在块中更高版本。 这些编译器错误可能很难了解因为实际错误发生之前所报告的错误。  组合到单个代码块的多个隐式/显式表达式后，此错误很常见。

## <a name="control-structures"></a>控件结构

控制结构是一种扩展的代码块。 所有方面的代码块 （过渡到标记中，内联 C#） 也都适用于以下结构：

### <a name="conditionals-if-else-if-else-and-switch"></a>条件语句@if，否则; 否则，和@switch

`@if`当代码运行时的控件：

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

`else`和`else if`不需要`@`符号：

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value is odd and small.</p>
}
```

以下标记显示如何使用交换语句：

```cshtml
@switch (value)
{
    case 1:
        <p>The value is 1!</p>
        break;
    case 1337:
        <p>Your number is 1337!</p>
        break;
    default:
        <p>Your number wasn't 1 or 1337.</p>
        break;
}
```

### <a name="looping-for-foreach-while-and-do-while"></a>循环@for， @foreach， @while，和@do时

模板化 HTML 可以呈现包含循环的控制语句。  若要呈现的人员列表：

```cshtml
@{
    var people = new Person[]
    {
          new Person("Weston", 33),
          new Person("Johnathon", 41),
          ...
    };
}
```

支持以下循环语句：

`@for`

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@foreach`

```cshtml
@foreach (var person in people)
{
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@while`

```cshtml
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

```cshtml
@{ var i = 0; }
@do
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
} while (i < people.Length);
```

### <a name="compound-using"></a>复合@using

在 C# 中，`using`语句用于确保释放对象。 在 Razor，相同的机制用于创建包含其他内容的 HTML 帮助器。 在下面的代码中，HTML 帮助器呈现窗体标记与`@using`语句：


```cshtml
@using (Html.BeginForm())
{
    <div>
        email:
        <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

可以使用执行作用域级操作[标记帮助程序](xref:mvc/views/tag-helpers/intro)。

### <a name="try-catch-finally"></a>@trycatch，finally

异常处理是类似于 C#:

[!code-cshtml[Main](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a>@lock

Razor 具有的功能来保护与 lock 语句的关键部分：

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a>注释

Razor 支持 C# 和 HTML 注释：

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

代码将以下 HTML 呈现：

```html
<!-- HTML comment -->
```

在呈现网页之前，将服务器中删除 razor 注释。 使用 razor`@*  *@`来分隔注释。 下面的代码是加上注释，因此服务器不呈现任何标记：

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a>指令

保留的关键字以下的隐式表达式由表示 razor 指令`@`符号。 指令通常会更改的方式视图分析或启用不同的功能。

了解如何 Razor 生成视图的代码，使更易于理解指令的工作方式。

[!code-html[Main](razor/sample/Views/Home/Contact8.cshtml)]

在代码中生成类似于下面的类：

```csharp
public class _Views_Something_cshtml : RazorPage<dynamic>
{
    public override async Task ExecuteAsync()
    {
        var output = "Getting old ain't for wimps! - Anonymous";

        WriteLiteral("/r/n<div>Quote of the Day: ");
        Write(output);
        WriteLiteral("</div>");
    }
}
```

在本文中，部分中更高版本[查看生成的视图的 Razor C# 类](#viewing-the-razor-c-class-generated-for-a-view)说明如何查看此生成的类。

### <a name="using"></a>@using

`@using`指令添加 C#`using`指令到生成的视图：

[!code-cshtml[Main](razor/sample/Views/Home/Contact9.cshtml)]

### <a name="model"></a>@model

`@model`指令指定的模型传递到视图类型：

```cshtml
@model TypeNameOfModel
```

在 ASP.NET 核心 MVC 应用程序使用单个用户帐户创建*Views/Account/Login.cshtml*视图包含以下模型声明：

```cshtml
@model LoginViewModel
```

生成的类继承自`RazorPage<dynamic>`:

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

Razor 公开`Model`属性访问的模型传递给视图：

```cshtml
<div>The Login Email: @Model.Email</div>
```

`@model`指令指定此属性的类型。 指令指定`T`中`RazorPage<T>`，生成类，派生自的视图。 如果`@model`指令不会指定，`Model`属性属于类型`dynamic`。 模型的值从控制器传递到该视图。 有关详细信息，请参阅 [强类型模型和@model关键字。

### <a name="inherits"></a>@inherits

`@inherits`指令提供的视图继承的类的完全控制：

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

下面的代码是自定义的 Razor 页类型：

[!code-csharp[Main](razor/sample/Classes/CustomRazorPage.cs)]

`CustomText`视图中显示：

[!code-cshtml[Main](razor/sample/Views/Home/Contact10.cshtml)]

代码将以下 HTML 呈现：

```html
<div>Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping a slop bucket on the street below.</div>
```

 `@model`和`@inherits`可以在同一个视图中使用。  `@inherits`可以采用*_ViewImports.cshtml*视图导入的文件：

[!code-cshtml[Main](razor/sample/Views/_ViewImportsModel.cshtml)]

下面的代码是强类型化视图的一个示例：

[!code-cshtml[Main](razor/sample/Views/Home/Login1.cshtml)]

如果"rick@contoso.com"传递在模型中，视图将生成以下的 HTML 标记：

```html
<div>The Login Email: rick@contoso.com</div>
<div>Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping a slop bucket on the street below.</div>
```

### <a name="inject"></a>@inject


`@inject`指令，Razor 页后，可以将从服务注入[服务容器](xref:fundamentals/dependency-injection)到视图。 有关详细信息，请参阅[到视图的依赖关系注入](xref:mvc/views/dependency-injection)。

### <a name="functions"></a>@functions

`@functions`指令，Razor 页后，可以将函数级内容添加到视图：

```cshtml
@functions { // C# Code }
```

例如: 

[!code-cshtml[Main](razor/sample/Views/Home/Contact6.cshtml)]

代码生成以下的 HTML 标记：

```html
<div>From method: Hello</div>
```

下面的代码是生成的 Razor C# 类：

[!code-csharp[Main](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

### <a name="section"></a>@section

`@section`结合使用指令[布局](xref:mvc/views/layout)以启用要呈现的 HTML 页面的不同部分中的内容视图。 有关详细信息，请参阅[部分](xref:mvc/views/layout#layout-sections-label)。

## <a name="tag-helpers"></a>标记帮助程序

有三个指令属于[标记帮助程序](xref:mvc/views/tag-helpers/intro)。

| 指令 | 函数 |
| --------- | -------- |
| [@addTagHelper](xref:mvc/views/tag-helpers/intro#add-helper-label) | 使得标记帮助程序适用于一个视图。 |
| [@removeTagHelper](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | 删除之前从视图添加标记帮助程序。 |
| [@tagHelperPrefix](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | 指定要启用标记帮助器支持，并若要使标记帮助器使用显式标记前缀。 |

## <a name="razor-reserved-keywords"></a>Razor 保留关键字

### <a name="razor-keywords"></a>Razor 关键字

* 页 （需要 ASP.NET Core 2.0 及更高版本）
* 函数
* 继承
* 模型
* section
* （当前不支持 ASP.NET Core） 的帮助器

与转义 razor 关键字`@(Razor Keyword)`(例如， `@(functions)`)。

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

C# Razor 关键字必须使用双转义`@(@C# Razor Keyword)`(例如， `@(@case)`)。 第一个`@`转义 Razor 分析器。 第二个`@`转义 C# 分析器。

### <a name="reserved-keywords-not-used-by-razor"></a>不使用 Razor 的保留的关键字

* namespace
* 类

## <a name="viewing-the-razor-c-class-generated-for-a-view"></a>查看生成的视图的 Razor C# 类

将下面的类添加到 ASP.NET 核心 MVC 项目：

[!code-csharp[Main](razor/sample/Utilities/CustomTemplateEngine.cs)]

重写`RazorTemplateEngine`添加通过与 MVC`CustomTemplateEngine`类：

[!code-csharp[Main](razor/sample/Startup.cs?highlight=4&range=10-14)]

在上设置断点`return csharpDocument`的语句`CustomTemplateEngine`。 当在断点处停止程序执行时，查看值`generatedCode`。

![GeneratedCode 文本可视化工具视图](razor/_static/tvr.png)

## <a name="view-lookups-and-case-sensitivity"></a>视图的查找，并区分大小写

Razor 视图引擎视图执行区分大小写的查找。 但是，由基础文件系统确定实际查找：

* 文件基于的源： 
  * 在操作系统上使用区分大小写的文件系统 (例如，Windows)，物理文件提供程序查找是区分大小写的。 例如，`return View("Test")`与匹配的结果将导致*/Views/Home/Test.cshtml*， */Views/home/test.cshtml*，和任何其他大小写变体。
  * 在区分大小写的文件系统 (例如，Linux，OSX，且`EmbeddedFileProvider`)，查找是区分大小写。 例如，`return View("Test")`专门匹配*/Views/Home/Test.cshtml*。
* 预编译视图： 使用 ASP.NET Core 2.0 及更高版本，查找预编译视图是在所有操作系统上不区分。 行为是在 Windows 上的物理文件提供程序的行为相同。 如果两个预编译的视图仅大小写不同，查找的结果是不确定的。

开发人员建议以匹配的文件和目录名称的大小写的大小写：

    * 区域、 控制器和操作名称。 
    * Razor 的页数。
    
匹配用例以确保部署找到其视图而不考虑基础文件系统。
