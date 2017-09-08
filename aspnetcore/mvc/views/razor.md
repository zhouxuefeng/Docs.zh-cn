---
title: "有关 ASP.NET 核心的 razor 语法参考"
author: rick-anderson
description: "详细信息 Razor 语法"
keywords: "ASP.NET 核心 Razor"
ms.author: riande
manager: wpickett
ms.date: 07/4/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/razor
ms.openlocfilehash: 7648bc2ac7b9efd1653725cda749d6cd271bae77
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="razor-syntax-for-aspnet-core"></a><span data-ttu-id="e2019-104">ASP.NET 核心的 razor 语法</span><span class="sxs-lookup"><span data-stu-id="e2019-104">Razor syntax for ASP.NET Core</span></span>

<span data-ttu-id="e2019-105">通过[Taylor Mullen](https://twitter.com/ntaylormullen)和[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e2019-105">By [Taylor Mullen](https://twitter.com/ntaylormullen) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="what-is-razor"></a><span data-ttu-id="e2019-106">什么是 Razor？</span><span class="sxs-lookup"><span data-stu-id="e2019-106">What is Razor?</span></span>

<span data-ttu-id="e2019-107">Razor 是才能嵌入到网页的服务器基于代码的标记语法。</span><span class="sxs-lookup"><span data-stu-id="e2019-107">Razor is a markup syntax for embedding server based code into web pages.</span></span> <span data-ttu-id="e2019-108">Razor 语法包含 Razor 标记，C# 和 HTML。</span><span class="sxs-lookup"><span data-stu-id="e2019-108">The Razor syntax consists of Razor markup, C# and HTML.</span></span> <span data-ttu-id="e2019-109">通常包含 Razor 文件具有*.cshtml*文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="e2019-109">Files containing Razor generally have a *.cshtml* file extension.</span></span>

## <a name="rendering-html"></a><span data-ttu-id="e2019-110">呈现 HTML</span><span class="sxs-lookup"><span data-stu-id="e2019-110">Rendering HTML</span></span>

<span data-ttu-id="e2019-111">默认 Razor 语言为 HTML。</span><span class="sxs-lookup"><span data-stu-id="e2019-111">The default Razor language is HTML.</span></span> <span data-ttu-id="e2019-112">从 Razor 呈现 HTML 在某一 HTML 文件中是没有什么不同。</span><span class="sxs-lookup"><span data-stu-id="e2019-112">Rendering HTML from Razor is no different than in an HTML file.</span></span> <span data-ttu-id="e2019-113">使用以下标记 Razor 文件：</span><span class="sxs-lookup"><span data-stu-id="e2019-113">A Razor file with the following markup:</span></span>

```html
<p>Hello World</p>
   ```

<span data-ttu-id="e2019-114">呈现为保持不变`<p>Hello World</p>`服务器。</span><span class="sxs-lookup"><span data-stu-id="e2019-114">Is rendered unchanged as `<p>Hello World</p>` by the server.</span></span>

## <a name="razor-syntax"></a><span data-ttu-id="e2019-115">Razor 语法</span><span class="sxs-lookup"><span data-stu-id="e2019-115">Razor syntax</span></span>

<span data-ttu-id="e2019-116">Razor 支持 C#，并使用`@`转换到 C# 的 HTML 中的符号。</span><span class="sxs-lookup"><span data-stu-id="e2019-116">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="e2019-117">Razor C# 表达式的计算结果，并将它们呈现的 HTML 输出中。</span><span class="sxs-lookup"><span data-stu-id="e2019-117">Razor evaluates C# expressions and renders them in the HTML output.</span></span> <span data-ttu-id="e2019-118">为 C# 或 Razor 特定标记，可以从 HTML 转换 razor。</span><span class="sxs-lookup"><span data-stu-id="e2019-118">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="e2019-119">当`@`符号后跟[Razor 保留关键字](#razor-reserved-keywords)转为 Razor 特定标记，否则它可以转换为纯 C#。</span><span class="sxs-lookup"><span data-stu-id="e2019-119">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords) it transitions into Razor-specific markup, otherwise it transitions into plain C#.</span></span>

<a name=escape-at-label></a>

<span data-ttu-id="e2019-120">HTML 包含`@`符号可能需要使用第二个进行转义`@`符号。</span><span class="sxs-lookup"><span data-stu-id="e2019-120">HTML containing `@` symbols may need to be escaped with a second `@` symbol.</span></span> <span data-ttu-id="e2019-121">例如: </span><span class="sxs-lookup"><span data-stu-id="e2019-121">For example:</span></span>

```html
<p>@@Username</p>
   ```

<span data-ttu-id="e2019-122">将会呈现在以下 HTML:</span><span class="sxs-lookup"><span data-stu-id="e2019-122">would render the following HTML:</span></span>

```html
<p>@Username</p>
   ```

<a name=razor-email-ref></a>

<span data-ttu-id="e2019-123">HTML 特性和内容包含电子邮件地址不处理`@`转换字符的形式的符号。</span><span class="sxs-lookup"><span data-stu-id="e2019-123">HTML attributes and content containing email addresses don’t treat the `@` symbol as a transition character.</span></span>

   `<a href="mailto:Support@contoso.com">Support@contoso.com</a>`

## <a name="implicit-razor-expressions"></a><span data-ttu-id="e2019-124">隐式 Razor 表达式</span><span class="sxs-lookup"><span data-stu-id="e2019-124">Implicit Razor expressions</span></span>

<span data-ttu-id="e2019-125">隐式 Razor 表达式开头`@`跟 C# 代码。</span><span class="sxs-lookup"><span data-stu-id="e2019-125">Implicit Razor expressions start with `@` followed by C# code.</span></span> <span data-ttu-id="e2019-126">例如: </span><span class="sxs-lookup"><span data-stu-id="e2019-126">For example:</span></span>

```html
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="e2019-127">除了 C#`await`关键字隐式表达式不能包含空格。</span><span class="sxs-lookup"><span data-stu-id="e2019-127">With the exception of the C# `await` keyword implicit expressions must not contain spaces.</span></span> <span data-ttu-id="e2019-128">例如，你可以只要 C# 语句已清除结束 intermingle 空格：</span><span class="sxs-lookup"><span data-stu-id="e2019-128">For example, you can intermingle spaces as long as the C# statement has a clear ending:</span></span>

```html
<p>@await DoSomething("hello", "world")</p>
```

## <a name="explicit-razor-expressions"></a><span data-ttu-id="e2019-129">显式 Razor 表达式</span><span class="sxs-lookup"><span data-stu-id="e2019-129">Explicit Razor expressions</span></span>

<span data-ttu-id="e2019-130">显式 Razor 表达式组成 @ 符号与平衡括号。</span><span class="sxs-lookup"><span data-stu-id="e2019-130">Explicit Razor expressions consists of an @ symbol with balanced parenthesis.</span></span> <span data-ttu-id="e2019-131">例如，若要呈现最后一周的时间：</span><span class="sxs-lookup"><span data-stu-id="e2019-131">For example, to render last week's time:</span></span>

```html
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="e2019-132">中的所有内容 @ （） 括号是计算并呈现到输出。</span><span class="sxs-lookup"><span data-stu-id="e2019-132">Any content within the @() parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="e2019-133">隐式表达式通常不能包含空格。</span><span class="sxs-lookup"><span data-stu-id="e2019-133">Implicit expressions generally cannot contain spaces.</span></span> <span data-ttu-id="e2019-134">例如，在下面的代码中，一周不是从当前时间减去：</span><span class="sxs-lookup"><span data-stu-id="e2019-134">For example, in the code below, one week is not subtracted from the current time:</span></span>

<span data-ttu-id="e2019-135">[!code-html[Main](razor/sample/Views/Home/Contact.cshtml?range=20)]</span><span class="sxs-lookup"><span data-stu-id="e2019-135">[!code-html[Main](razor/sample/Views/Home/Contact.cshtml?range=20)]</span></span>

<span data-ttu-id="e2019-136">这将会呈现以下 HTML:</span><span class="sxs-lookup"><span data-stu-id="e2019-136">Which renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
   ```

<span data-ttu-id="e2019-137">可以使用显式表达式将文本与表达式结果串联起来：</span><span class="sxs-lookup"><span data-stu-id="e2019-137">You can use an explicit expression to concatenate text with an expression result:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "none", "highlight_args": {"hl_lines": [5]}} -->

```none
@{
    var joe = new Person("Joe", 33);
 }

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="e2019-138">如果没有显式的表达式，`<p>Age@joe.Age</p>`将被视为电子邮件地址和`<p>Age@joe.Age</p>`将呈现。</span><span class="sxs-lookup"><span data-stu-id="e2019-138">Without the explicit expression, `<p>Age@joe.Age</p>` would be treated as an email address and `<p>Age@joe.Age</p>` would be rendered.</span></span> <span data-ttu-id="e2019-139">在作为显式表达式，写入时`<p>Age33</p>`呈现。</span><span class="sxs-lookup"><span data-stu-id="e2019-139">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<a name=expression-encoding-label></a>

## <a name="expression-encoding"></a><span data-ttu-id="e2019-140">表达式编码</span><span class="sxs-lookup"><span data-stu-id="e2019-140">Expression encoding</span></span>

<span data-ttu-id="e2019-141">C# 表达式，其计算结果为字符串是 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="e2019-141">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="e2019-142">C# 表达式的计算结果为`IHtmlContent`呈现直接通过*IHtmlContent.WriteTo*。</span><span class="sxs-lookup"><span data-stu-id="e2019-142">C# expressions that evaluate to `IHtmlContent` are rendered directly through *IHtmlContent.WriteTo*.</span></span> <span data-ttu-id="e2019-143">C# 表达式不计算结果为*IHtmlContent*转换为字符串 (由*ToString*) 和编码然后将它们呈现。</span><span class="sxs-lookup"><span data-stu-id="e2019-143">C# expressions that don't evaluate to *IHtmlContent* are converted to a string (by *ToString*) and encoded before they are rendered.</span></span> <span data-ttu-id="e2019-144">例如，以下 Razor 标记：</span><span class="sxs-lookup"><span data-stu-id="e2019-144">For example, the following Razor markup:</span></span>

```html
@("<span>Hello World</span>")
   ```

<span data-ttu-id="e2019-145">将此呈现 HTML:</span><span class="sxs-lookup"><span data-stu-id="e2019-145">Renders this HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
   ```

<span data-ttu-id="e2019-146">该浏览器将呈现为：</span><span class="sxs-lookup"><span data-stu-id="e2019-146">Which the browser renders as:</span></span>

`<span>Hello World</span>`

<span data-ttu-id="e2019-147">`HtmlHelper``Raw`输出不是编码而呈现为 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="e2019-147">`HtmlHelper` `Raw` output is not encoded but rendered as HTML markup.</span></span>

>[!WARNING]
> <span data-ttu-id="e2019-148">使用`HtmlHelper.Raw`未净化的用户输入会带来安全风险。</span><span class="sxs-lookup"><span data-stu-id="e2019-148">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="e2019-149">用户输入可能包含恶意 JavaScript 或其他攻击。</span><span class="sxs-lookup"><span data-stu-id="e2019-149">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="e2019-150">对用户输入会很困难，请不要使用`HtmlHelper.Raw`用户输入。</span><span class="sxs-lookup"><span data-stu-id="e2019-150">Sanitizing user input is difficult, avoid using `HtmlHelper.Raw` on user input.</span></span>

<span data-ttu-id="e2019-151">以下 Razor 标记：</span><span class="sxs-lookup"><span data-stu-id="e2019-151">The following Razor markup:</span></span>

```html
@Html.Raw("<span>Hello World</span>")
   ```

<span data-ttu-id="e2019-152">将此呈现 HTML:</span><span class="sxs-lookup"><span data-stu-id="e2019-152">Renders this HTML:</span></span>

```html
<span>Hello World</span>
   ```

<a name=razor-code-blocks-label></a>

## <a name="razor-code-blocks"></a><span data-ttu-id="e2019-153">Razor 代码块</span><span class="sxs-lookup"><span data-stu-id="e2019-153">Razor code blocks</span></span>

<span data-ttu-id="e2019-154">Razor 代码块开头`@`，并且通过包括`{}`。</span><span class="sxs-lookup"><span data-stu-id="e2019-154">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="e2019-155">与不同的是表达式，则不会呈现代码块内的 C# 代码。</span><span class="sxs-lookup"><span data-stu-id="e2019-155">Unlike expressions, C# code inside code blocks is not rendered.</span></span> <span data-ttu-id="e2019-156">代码块中和表达式 Razor 页共享相同的作用域和按顺序定义 （即，在代码块中的声明将更高版本的代码块和表达式的作用域中）。</span><span class="sxs-lookup"><span data-stu-id="e2019-156">Code blocks and expressions in a Razor page share the same scope and are defined in order (that is, declarations in a code block will be in scope for later code blocks and expressions).</span></span>

```none
@{
    var output = "Hello World";
}

<p>The rendered result: @output</p>
```

<span data-ttu-id="e2019-157">将会呈现在：</span><span class="sxs-lookup"><span data-stu-id="e2019-157">Would render:</span></span>

```html
<p>The rendered result: Hello World</p>
   ```

<a name=implicit-transitions-label></a>

### <a name="implicit-transitions"></a><span data-ttu-id="e2019-158">隐式转换</span><span class="sxs-lookup"><span data-stu-id="e2019-158">Implicit transitions</span></span>

<span data-ttu-id="e2019-159">代码块中的默认语言为 C# 中，而是可以转换回 HTML。</span><span class="sxs-lookup"><span data-stu-id="e2019-159">The default language in a code block is C#, but you can transition back to HTML.</span></span> <span data-ttu-id="e2019-160">代码块中的 HTML 将转换回呈现 HTML:</span><span class="sxs-lookup"><span data-stu-id="e2019-160">HTML within a code block will transition back into rendering HTML:</span></span>

```none
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

<a name=explicit-delimited-transition-label></a>

### <a name="explicit-delimited-transition"></a><span data-ttu-id="e2019-161">带分隔符的显式转换</span><span class="sxs-lookup"><span data-stu-id="e2019-161">Explicit delimited transition</span></span>

<span data-ttu-id="e2019-162">若要定义一个小节应呈现的 HTML 代码块，括住的字符要呈现具有 Razor`<text>`标记：</span><span class="sxs-lookup"><span data-stu-id="e2019-162">To define a sub-section of a code block that should render HTML, surround the characters to be rendered with the Razor `<text>` tag:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "none", "highlight_args": {"hl_lines": [4]}} -->

```none
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="e2019-163">通常，当你想要呈现不括在的 HTML 标记的 HTML 时，可使用此方法。</span><span class="sxs-lookup"><span data-stu-id="e2019-163">You generally use this approach when you want to render HTML that is not surrounded by an HTML tag.</span></span> <span data-ttu-id="e2019-164">没有 HTML 或 Razor 标记，则会收到 Razor 运行时错误。</span><span class="sxs-lookup"><span data-stu-id="e2019-164">Without an HTML or Razor tag, you get a Razor runtime error.</span></span>

<a name=explicit-line-transition-with-label></a>

### <a name="explicit-line-transition-with-"></a><span data-ttu-id="e2019-165">使用显式行转换`@:`</span><span class="sxs-lookup"><span data-stu-id="e2019-165">Explicit Line Transition with `@:`</span></span>

<span data-ttu-id="e2019-166">若要在代码块内以 html 格式呈现整个行的其余内容，使用`@:`语法：</span><span class="sxs-lookup"><span data-stu-id="e2019-166">To render the rest of an entire line as HTML inside a code block, use the `@:` syntax:</span></span>

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "none", "highlight_args": {"hl_lines": [4]}} -->

```none
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="e2019-167">而无需`@:`在上面的代码中，你会收到一个运行时错误 Razor。</span><span class="sxs-lookup"><span data-stu-id="e2019-167">Without the `@:` in the code above, you'd get a Razor run time error.</span></span>

<a name=control-structures-razor-label></a>

## <a name="control-structures"></a><span data-ttu-id="e2019-168">控件结构</span><span class="sxs-lookup"><span data-stu-id="e2019-168">Control Structures</span></span>

<span data-ttu-id="e2019-169">控制结构是一种扩展的代码块。</span><span class="sxs-lookup"><span data-stu-id="e2019-169">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="e2019-170">所有方面的代码块 （过渡到标记中，内联 C#） 也都适用于以下结构。</span><span class="sxs-lookup"><span data-stu-id="e2019-170">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures.</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="e2019-171">条件语句`@if`， `else if`，`else`和`@switch`</span><span class="sxs-lookup"><span data-stu-id="e2019-171">Conditionals `@if`, `else if`, `else` and `@switch`</span></span>

<span data-ttu-id="e2019-172">`@if`系列控制代码的运行时：</span><span class="sxs-lookup"><span data-stu-id="e2019-172">The `@if` family controls when code runs:</span></span>

```none
@if (value % 2 == 0)
{
    <p>The value was even</p>
}
```

<span data-ttu-id="e2019-173">`else`和`else if`不需要`@`符号：</span><span class="sxs-lookup"><span data-stu-id="e2019-173">`else` and `else if` don't require the `@` symbol:</span></span>

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

<span data-ttu-id="e2019-174">你可以使用 switch 语句如下：</span><span class="sxs-lookup"><span data-stu-id="e2019-174">You can use a switch statement like this:</span></span>

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

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="e2019-175">循环`@for`， `@foreach`， `@while`，和`@do while`</span><span class="sxs-lookup"><span data-stu-id="e2019-175">Looping `@for`, `@foreach`, `@while`, and `@do while`</span></span>

<span data-ttu-id="e2019-176">你可以呈现包含循环的控制语句的模板化 HTML。</span><span class="sxs-lookup"><span data-stu-id="e2019-176">You can render templated HTML with looping control statements.</span></span> <span data-ttu-id="e2019-177">例如，若要呈现的人员列表：</span><span class="sxs-lookup"><span data-stu-id="e2019-177">For example, to render a list of people:</span></span>

```none
@{
    var people = new Person[]
    {
          new Person("John", 33),
          new Person("Doe", 41),
    };
}
```

<span data-ttu-id="e2019-178">你可以使用任何以下循环语句：</span><span class="sxs-lookup"><span data-stu-id="e2019-178">You can use any of the following looping statements:</span></span>

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

### <a name="compound-using"></a><span data-ttu-id="e2019-179">复合`@using`</span><span class="sxs-lookup"><span data-stu-id="e2019-179">Compound `@using`</span></span>

<span data-ttu-id="e2019-180">使用 C# 语句用于确保释放对象。</span><span class="sxs-lookup"><span data-stu-id="e2019-180">In C# a using statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="e2019-181">在 Razor 此相同的机制可以用于创建包含其他内容的 HTML 帮助器。</span><span class="sxs-lookup"><span data-stu-id="e2019-181">In Razor this same mechanism can be used to create HTML helpers that contain additional content.</span></span> <span data-ttu-id="e2019-182">例如，我们可以利用 HTML 帮助器呈现窗体标记与`@using`语句：</span><span class="sxs-lookup"><span data-stu-id="e2019-182">For instance, we can utilize HTML Helpers to render a form tag with the `@using` statement:</span></span>

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

<span data-ttu-id="e2019-183">你还可以执行类似上面使用的作用域级别操作[标记帮助程序](tag-helpers/index.md)。</span><span class="sxs-lookup"><span data-stu-id="e2019-183">You can also perform scope level actions like the above with [Tag Helpers](tag-helpers/index.md).</span></span>

### <a name="try-catch-finally"></a><span data-ttu-id="e2019-184">`@try`, `catch`, `finally`</span><span class="sxs-lookup"><span data-stu-id="e2019-184">`@try`, `catch`, `finally`</span></span>

<span data-ttu-id="e2019-185">异常处理是类似于 C#:</span><span class="sxs-lookup"><span data-stu-id="e2019-185">Exception handling is similar to  C#:</span></span>

<span data-ttu-id="e2019-186">[!code-html[Main](razor/sample/Views/Home/Contact7.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="e2019-186">[!code-html[Main](razor/sample/Views/Home/Contact7.cshtml)]</span></span>

### `@lock`

<span data-ttu-id="e2019-187">Razor 具有的功能来保护与 lock 语句的关键部分：</span><span class="sxs-lookup"><span data-stu-id="e2019-187">Razor has the capability to protect critical sections with lock statements:</span></span>

```none
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="e2019-188">注释</span><span class="sxs-lookup"><span data-stu-id="e2019-188">Comments</span></span>

<span data-ttu-id="e2019-189">Razor 支持 C# 和 HTML 注释。</span><span class="sxs-lookup"><span data-stu-id="e2019-189">Razor supports C# and HTML comments.</span></span> <span data-ttu-id="e2019-190">以下标记：</span><span class="sxs-lookup"><span data-stu-id="e2019-190">The following markup:</span></span>

```none
@{
    /* C# comment. */
    // Another C# comment.
}
<!-- HTML comment -->
```

<span data-ttu-id="e2019-191">作为由服务器呈现：</span><span class="sxs-lookup"><span data-stu-id="e2019-191">Is rendered by the server as:</span></span>

```none
<!-- HTML comment -->
```

<span data-ttu-id="e2019-192">在呈现页之前，将服务器中删除 razor 注释。</span><span class="sxs-lookup"><span data-stu-id="e2019-192">Razor comments are removed by the server before the page is rendered.</span></span> <span data-ttu-id="e2019-193">使用 razor`@*  *@`来分隔注释。</span><span class="sxs-lookup"><span data-stu-id="e2019-193">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="e2019-194">下面的代码是加上注释，以便服务器将不会呈现任何标记：</span><span class="sxs-lookup"><span data-stu-id="e2019-194">The following code is commented out, so the server will not render any markup:</span></span>

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

## <a name="directives"></a><span data-ttu-id="e2019-195">指令</span><span class="sxs-lookup"><span data-stu-id="e2019-195">Directives</span></span>

<span data-ttu-id="e2019-196">保留的关键字以下的隐式表达式由表示 razor 指令`@`符号。</span><span class="sxs-lookup"><span data-stu-id="e2019-196">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="e2019-197">通常，一个指令将更改分析页面的方式，或者启用 Razor 页内的不同功能。</span><span class="sxs-lookup"><span data-stu-id="e2019-197">A directive will typically change the way a page is parsed or enable different functionality within your Razor page.</span></span>

<span data-ttu-id="e2019-198">了解如何 Razor 生成视图的代码将更加轻松地了解指令的工作原理。</span><span class="sxs-lookup"><span data-stu-id="e2019-198">Understanding how Razor generates code for a view will make it easier to understand how directives work.</span></span> <span data-ttu-id="e2019-199">Razor 页用于生成 C# 文件中。</span><span class="sxs-lookup"><span data-stu-id="e2019-199">A Razor page is used to generate a C# file.</span></span> <span data-ttu-id="e2019-200">例如，此 Razor 页：</span><span class="sxs-lookup"><span data-stu-id="e2019-200">For example, this Razor page:</span></span>

<span data-ttu-id="e2019-201">[!code-html[Main](razor/sample/Views/Home/Contact8.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="e2019-201">[!code-html[Main](razor/sample/Views/Home/Contact8.cshtml)]</span></span>

<span data-ttu-id="e2019-202">生成类似于下面的类：</span><span class="sxs-lookup"><span data-stu-id="e2019-202">Generates a class similar to the following:</span></span>

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

<span data-ttu-id="e2019-203">[查看生成的视图的 Razor C# 类](#razor-customcompilationservice-label)说明如何查看此生成的类。</span><span class="sxs-lookup"><span data-stu-id="e2019-203">[Viewing the Razor C# class generated for a view](#razor-customcompilationservice-label) explains how to view this generated class.</span></span>

### `@using`

<span data-ttu-id="e2019-204">`@using`指令将添加 c#`using`指令至生成的 razor 页：</span><span class="sxs-lookup"><span data-stu-id="e2019-204">The `@using` directive will add the c# `using` directive to the generated razor page:</span></span>

<span data-ttu-id="e2019-205">[!code-html[Main](razor/sample/Views/Home/Contact9.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="e2019-205">[!code-html[Main](razor/sample/Views/Home/Contact9.cshtml)]</span></span>

### `@model`

<span data-ttu-id="e2019-206">`@model`指令指定的模型传递给 Razor 页的类型。</span><span class="sxs-lookup"><span data-stu-id="e2019-206">The `@model` directive specifies the type of the model passed to the Razor page.</span></span> <span data-ttu-id="e2019-207">它使用以下语法：</span><span class="sxs-lookup"><span data-stu-id="e2019-207">It uses the following syntax:</span></span>

```none
@model TypeNameOfModel
   ```

<span data-ttu-id="e2019-208">例如，如果使用单个用户帐户创建的 ASP.NET 核心 MVC 应用*Views/Account/Login.cshtml* Razor 视图包含以下模型声明：</span><span class="sxs-lookup"><span data-stu-id="e2019-208">For example, if you create an ASP.NET Core MVC app with individual user accounts, the *Views/Account/Login.cshtml* Razor view contains the following model declaration:</span></span>

```csharp
@model LoginViewModel
   ```

<span data-ttu-id="e2019-209">在前面的类示例中，生成的类继承自`RazorPage<dynamic>`。</span><span class="sxs-lookup"><span data-stu-id="e2019-209">In the preceding class example, the class generated inherits from `RazorPage<dynamic>`.</span></span> <span data-ttu-id="e2019-210">通过添加`@model`控制什么继承。</span><span class="sxs-lookup"><span data-stu-id="e2019-210">By adding an `@model` you control what’s inherited.</span></span> <span data-ttu-id="e2019-211">例如</span><span class="sxs-lookup"><span data-stu-id="e2019-211">For example</span></span>

```csharp
@model LoginViewModel
   ```

<span data-ttu-id="e2019-212">生成下面的类</span><span class="sxs-lookup"><span data-stu-id="e2019-212">Generates the following class</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
   ```

<span data-ttu-id="e2019-213">Razor 页公开`Model`属性访问的模型传递到页。</span><span class="sxs-lookup"><span data-stu-id="e2019-213">Razor pages expose a `Model` property for accessing the model passed to the page.</span></span>

```html
<div>The Login Email: @Model.Email</div>
   ```

<span data-ttu-id="e2019-214">`@model`指令指定此属性的类型 (通过指定`T`中`RazorPage<T>`页生成的类派生自)。</span><span class="sxs-lookup"><span data-stu-id="e2019-214">The `@model` directive specified the type of this property (by specifying the `T` in `RazorPage<T>` that the generated class for your page derives from).</span></span> <span data-ttu-id="e2019-215">如果没有指定`@model`指令`Model`属性的类型将为`dynamic`。</span><span class="sxs-lookup"><span data-stu-id="e2019-215">If you don't specify the `@model` directive the `Model` property will be of type `dynamic`.</span></span> <span data-ttu-id="e2019-216">模型的值从控制器传递到该视图。</span><span class="sxs-lookup"><span data-stu-id="e2019-216">The value of the model is passed from the controller to the view.</span></span> <span data-ttu-id="e2019-217">请参阅[强类型模型和@model关键字](../../tutorials/first-mvc-app/adding-model.md#strongly-typed-models-keyword-label)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="e2019-217">See [Strongly typed models and the @model keyword](../../tutorials/first-mvc-app/adding-model.md#strongly-typed-models-keyword-label) for more information.</span></span>

### `@inherits`

<span data-ttu-id="e2019-218">`@inherits`指令可完全控制 Razor 页继承的类：</span><span class="sxs-lookup"><span data-stu-id="e2019-218">The `@inherits` directive gives you full control of the class your Razor page inherits:</span></span>

```none
@inherits TypeNameOfClassToInheritFrom
   ```

<span data-ttu-id="e2019-219">例如，假设我们有以下自定义 Razor 页类型：</span><span class="sxs-lookup"><span data-stu-id="e2019-219">For instance, let’s say we had the following custom Razor page type:</span></span>

<span data-ttu-id="e2019-220">[!code-csharp[Main](razor/sample/Classes/CustomRazorPage.cs)]</span><span class="sxs-lookup"><span data-stu-id="e2019-220">[!code-csharp[Main](razor/sample/Classes/CustomRazorPage.cs)]</span></span>

<span data-ttu-id="e2019-221">将生成以下 Razor `<div>Custom text: Hello World</div>`。</span><span class="sxs-lookup"><span data-stu-id="e2019-221">The following Razor would generate `<div>Custom text: Hello World</div>`.</span></span>

<span data-ttu-id="e2019-222">[!code-html[Main](razor/sample/Views/Home/Contact10.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="e2019-222">[!code-html[Main](razor/sample/Views/Home/Contact10.cshtml)]</span></span>

<span data-ttu-id="e2019-223">不能使用`@model`和`@inherits`同一页面上。</span><span class="sxs-lookup"><span data-stu-id="e2019-223">You can't use `@model` and `@inherits` on the same page.</span></span> <span data-ttu-id="e2019-224">你可以`@inherits`中*_ViewImports.cshtml* Razor 页导入的文件。</span><span class="sxs-lookup"><span data-stu-id="e2019-224">You can have `@inherits` in a *_ViewImports.cshtml* file that the Razor page imports.</span></span> <span data-ttu-id="e2019-225">例如，如果在 Razor 视图导入以下*_ViewImports.cshtml*文件：</span><span class="sxs-lookup"><span data-stu-id="e2019-225">For example, if your Razor view imported the following *_ViewImports.cshtml* file:</span></span>

<span data-ttu-id="e2019-226">[!code-html[Main](razor/sample/Views/_ViewImportsModel.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="e2019-226">[!code-html[Main](razor/sample/Views/_ViewImportsModel.cshtml)]</span></span>

<span data-ttu-id="e2019-227">以下的强类型化的 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="e2019-227">The following strongly typed Razor page</span></span>

<span data-ttu-id="e2019-228">[!code-html[Main](razor/sample/Views/Home/Login1.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="e2019-228">[!code-html[Main](razor/sample/Views/Home/Login1.cshtml)]</span></span>

<span data-ttu-id="e2019-229">生成此 HTML 标记：</span><span class="sxs-lookup"><span data-stu-id="e2019-229">Generates this HTML markup:</span></span>

```none
<div>The Login Email: Rick@contoso.com</div>
<div>Custom text: Hello World</div>
```

<span data-ttu-id="e2019-230">当传递"[Rick@contoso.com](mailto:Rick@contoso.com)"模型中：</span><span class="sxs-lookup"><span data-stu-id="e2019-230">When passed "[Rick@contoso.com](mailto:Rick@contoso.com)" in the model:</span></span>

   <span data-ttu-id="e2019-231">请参阅[布局](layout.md)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="e2019-231">See [Layout](layout.md) for more information.</span></span>

### `@inject`

<span data-ttu-id="e2019-232">`@inject`指令使你能够注入从服务你[服务容器](../../fundamentals/dependency-injection.md)到使用你 Razor 页。</span><span class="sxs-lookup"><span data-stu-id="e2019-232">The `@inject` directive enables you to inject a service from your [service container](../../fundamentals/dependency-injection.md)  into your Razor page for use.</span></span> <span data-ttu-id="e2019-233">请参阅[到视图的依赖关系注入](dependency-injection.md)。</span><span class="sxs-lookup"><span data-stu-id="e2019-233">See [Dependency injection into views](dependency-injection.md).</span></span>

<a name="functions"></a>

### `@functions`

<span data-ttu-id="e2019-234">`@functions`指令，可将函数级的内容添加到你 Razor 页。</span><span class="sxs-lookup"><span data-stu-id="e2019-234">The `@functions` directive enables you to add function level content to your Razor page.</span></span> <span data-ttu-id="e2019-235">语法为：</span><span class="sxs-lookup"><span data-stu-id="e2019-235">The syntax is:</span></span>

```none
@functions { // C# Code }
   ```

<span data-ttu-id="e2019-236">例如: </span><span class="sxs-lookup"><span data-stu-id="e2019-236">For example:</span></span>

<span data-ttu-id="e2019-237">[!code-html[Main](razor/sample/Views/Home/Contact6.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="e2019-237">[!code-html[Main](razor/sample/Views/Home/Contact6.cshtml)]</span></span>

<span data-ttu-id="e2019-238">生成以下的 HTML 标记：</span><span class="sxs-lookup"><span data-stu-id="e2019-238">Generates the following HTML markup:</span></span>

```none
<div>From method: Hello</div>
   ```

<span data-ttu-id="e2019-239">生成 Razor 的 C# 如下所示：</span><span class="sxs-lookup"><span data-stu-id="e2019-239">The generated Razor C# looks like:</span></span>

<span data-ttu-id="e2019-240">[!code-csharp[Main](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]</span><span class="sxs-lookup"><span data-stu-id="e2019-240">[!code-csharp[Main](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]</span></span>

### `@section`

<span data-ttu-id="e2019-241">`@section`结合使用指令[布局页](layout.md)启用视图呈现中呈现的 HTML 页面的不同部分的内容。</span><span class="sxs-lookup"><span data-stu-id="e2019-241">The `@section` directive is used in conjunction with the [layout page](layout.md) to enable views to render content in different parts of the rendered HTML page.</span></span> <span data-ttu-id="e2019-242">请参阅[部分](layout.md#layout-sections-label)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="e2019-242">See [Sections](layout.md#layout-sections-label) for more information.</span></span>

## <a name="tag-helpers"></a><span data-ttu-id="e2019-243">标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="e2019-243">Tag Helpers</span></span>

<span data-ttu-id="e2019-244">以下[标记帮助程序](tag-helpers/index.md)指令中提供的链接详细介绍。</span><span class="sxs-lookup"><span data-stu-id="e2019-244">The following [Tag Helpers](tag-helpers/index.md) directives are detailed in the links provided.</span></span>

* [@addTagHelper](tag-helpers/intro.md#add-helper-label)
* [@removeTagHelper](tag-helpers/intro.md#remove-razor-directives-label)
* [@tagHelperPrefix](tag-helpers/intro.md#prefix-razor-directives-label)

<a name=razor-reserved-keywords-label></a>

## <a name="razor-reserved-keywords"></a><span data-ttu-id="e2019-245">Razor 保留关键字</span><span class="sxs-lookup"><span data-stu-id="e2019-245">Razor reserved keywords</span></span>

### <a name="razor-keywords"></a><span data-ttu-id="e2019-246">Razor 关键字</span><span class="sxs-lookup"><span data-stu-id="e2019-246">Razor keywords</span></span>

* <span data-ttu-id="e2019-247">页 （需要 ASP.NET Core 2.0 及更高版本）</span><span class="sxs-lookup"><span data-stu-id="e2019-247">page (Requires ASP.NET Core 2.0 and later)</span></span>
* <span data-ttu-id="e2019-248">函数</span><span class="sxs-lookup"><span data-stu-id="e2019-248">functions</span></span>
* <span data-ttu-id="e2019-249">继承</span><span class="sxs-lookup"><span data-stu-id="e2019-249">inherits</span></span>
* <span data-ttu-id="e2019-250">模型</span><span class="sxs-lookup"><span data-stu-id="e2019-250">model</span></span>
* <span data-ttu-id="e2019-251">section</span><span class="sxs-lookup"><span data-stu-id="e2019-251">section</span></span>
* <span data-ttu-id="e2019-252">帮助器 （不支持 ASP.NET Core。）</span><span class="sxs-lookup"><span data-stu-id="e2019-252">helper   (Not supported by ASP.NET Core.)</span></span>

<span data-ttu-id="e2019-253">可以使用转义 razor 关键字`@(Razor Keyword)`，例如`@(functions)`。</span><span class="sxs-lookup"><span data-stu-id="e2019-253">Razor keywords can be escaped with `@(Razor Keyword)`, for example `@(functions)`.</span></span> <span data-ttu-id="e2019-254">请参阅下面的完整示例。</span><span class="sxs-lookup"><span data-stu-id="e2019-254">See the complete sample below.</span></span>

### <a name="c-razor-keywords"></a><span data-ttu-id="e2019-255">C# Razor 关键字</span><span class="sxs-lookup"><span data-stu-id="e2019-255">C# Razor keywords</span></span>

* <span data-ttu-id="e2019-256">case</span><span class="sxs-lookup"><span data-stu-id="e2019-256">case</span></span>
* <span data-ttu-id="e2019-257">do</span><span class="sxs-lookup"><span data-stu-id="e2019-257">do</span></span>
* <span data-ttu-id="e2019-258">default</span><span class="sxs-lookup"><span data-stu-id="e2019-258">default</span></span>
* <span data-ttu-id="e2019-259">for</span><span class="sxs-lookup"><span data-stu-id="e2019-259">for</span></span>
* <span data-ttu-id="e2019-260">foreach</span><span class="sxs-lookup"><span data-stu-id="e2019-260">foreach</span></span>
* <span data-ttu-id="e2019-261">if</span><span class="sxs-lookup"><span data-stu-id="e2019-261">if</span></span>
* <span data-ttu-id="e2019-262">else</span><span class="sxs-lookup"><span data-stu-id="e2019-262">else</span></span>
* <span data-ttu-id="e2019-263">锁定</span><span class="sxs-lookup"><span data-stu-id="e2019-263">lock</span></span>
* <span data-ttu-id="e2019-264">switch</span><span class="sxs-lookup"><span data-stu-id="e2019-264">switch</span></span>
* <span data-ttu-id="e2019-265">try</span><span class="sxs-lookup"><span data-stu-id="e2019-265">try</span></span>
* <span data-ttu-id="e2019-266">catch</span><span class="sxs-lookup"><span data-stu-id="e2019-266">catch</span></span>
* <span data-ttu-id="e2019-267">finally</span><span class="sxs-lookup"><span data-stu-id="e2019-267">finally</span></span>
* <span data-ttu-id="e2019-268">using</span><span class="sxs-lookup"><span data-stu-id="e2019-268">using</span></span>
* <span data-ttu-id="e2019-269">while</span><span class="sxs-lookup"><span data-stu-id="e2019-269">while</span></span>

<span data-ttu-id="e2019-270">C# Razor 关键字需要双使用转义`@(@C# Razor Keyword)`，例如`@(@case)`。</span><span class="sxs-lookup"><span data-stu-id="e2019-270">C# Razor keywords need to be double escaped with `@(@C# Razor Keyword)`, for example `@(@case)`.</span></span> <span data-ttu-id="e2019-271">第一个`@`转义 Razor 分析器中，第二个`@`转义 C# 分析器。</span><span class="sxs-lookup"><span data-stu-id="e2019-271">The first `@` escapes the Razor parser, the second `@` escapes the C# parser.</span></span> <span data-ttu-id="e2019-272">请参阅下面的完整示例。</span><span class="sxs-lookup"><span data-stu-id="e2019-272">See the complete sample below.</span></span>

### <a name="reserved-keywords-not-used-by-razor"></a><span data-ttu-id="e2019-273">不使用 Razor 的保留的关键字</span><span class="sxs-lookup"><span data-stu-id="e2019-273">Reserved keywords not used by Razor</span></span>

* <span data-ttu-id="e2019-274">namespace</span><span class="sxs-lookup"><span data-stu-id="e2019-274">namespace</span></span>
* <span data-ttu-id="e2019-275">类</span><span class="sxs-lookup"><span data-stu-id="e2019-275">class</span></span>

<a name=razor-customcompilationservice-label></a>

## <a name="viewing-the-razor-c-class-generated-for-a-view"></a><span data-ttu-id="e2019-276">查看生成的视图的 Razor C# 类</span><span class="sxs-lookup"><span data-stu-id="e2019-276">Viewing the Razor C# class generated for a view</span></span>

<span data-ttu-id="e2019-277">将下面的类添加到 ASP.NET 核心 MVC 项目：</span><span class="sxs-lookup"><span data-stu-id="e2019-277">Add the following class to your ASP.NET Core MVC project:</span></span>

<span data-ttu-id="e2019-278">[!code-csharp[Main](razor/sample/Services/CustomCompilationService.cs)]</span><span class="sxs-lookup"><span data-stu-id="e2019-278">[!code-csharp[Main](razor/sample/Services/CustomCompilationService.cs)]</span></span>

<span data-ttu-id="e2019-279">重写`ICompilationService`由 MVC 具有上述类; 添加</span><span class="sxs-lookup"><span data-stu-id="e2019-279">Override the `ICompilationService` added by MVC with the above class;</span></span>

<span data-ttu-id="e2019-280">[!code-csharp[Main](razor/sample/Startup.cs?highlight=4&range=29-33)]</span><span class="sxs-lookup"><span data-stu-id="e2019-280">[!code-csharp[Main](razor/sample/Startup.cs?highlight=4&range=29-33)]</span></span>

<span data-ttu-id="e2019-281">在上设置断点`Compile`方法`CustomCompilationService`和视图，以及`compilationContent`。</span><span class="sxs-lookup"><span data-stu-id="e2019-281">Set a break point on the `Compile` method of `CustomCompilationService` and view `compilationContent`.</span></span>

![CompilationContent 文本可视化工具视图](razor/_static/tvr.png)

<a name="case"></a>
## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="e2019-283">视图的查找，并区分大小写</span><span class="sxs-lookup"><span data-stu-id="e2019-283">View lookups and case sensitivity</span></span>

<span data-ttu-id="e2019-284">Razor 视图引擎视图执行区分大小写的查找。</span><span class="sxs-lookup"><span data-stu-id="e2019-284">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="e2019-285">但是，由基础源确定实际查找：</span><span class="sxs-lookup"><span data-stu-id="e2019-285">However, the actual lookup is determined by the underlying source:</span></span>

* <span data-ttu-id="e2019-286">文件基于的源：</span><span class="sxs-lookup"><span data-stu-id="e2019-286">File based source:</span></span> 

    * <span data-ttu-id="e2019-287">在操作系统上使用区分大小写的文件系统 （如 Windows)，物理文件提供程序查找是区分大小写的。</span><span class="sxs-lookup"><span data-stu-id="e2019-287">On operating systems with case insensitive file systems (like Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="e2019-288">例如`return View("Test")`将导致`/Views/Home/Test.cshtml`，`/Views/home/test.cshtml`和所有其他大小写变体可能会发现。</span><span class="sxs-lookup"><span data-stu-id="e2019-288">For example `return View("Test")` would result in `/Views/Home/Test.cshtml`, `/Views/home/test.cshtml` and all other casing variants would be discovered.</span></span>
    * <span data-ttu-id="e2019-289">在区分大小写的文件系统，其中包括 Linux，OSX 和`EmbeddedFileProvider`，查找是区分大小写。</span><span class="sxs-lookup"><span data-stu-id="e2019-289">On case sensitive file systems, which includes Linux, OSX and `EmbeddedFileProvider`, lookups are case sensitive.</span></span> <span data-ttu-id="e2019-290">例如，`return View("Test")`将专门查找`/Views/Home/Test.cshtml`。</span><span class="sxs-lookup"><span data-stu-id="e2019-290">For example, `return View("Test")` would specifically look for `/Views/Home/Test.cshtml`.</span></span>
        
* <span data-ttu-id="e2019-291">预编译的视图：</span><span class="sxs-lookup"><span data-stu-id="e2019-291">Precompiled views:</span></span>

   * <span data-ttu-id="e2019-292">ASP.Net 核心 2.0 和更高版本，查找预编译视图是在所有操作系统上不区分。</span><span class="sxs-lookup"><span data-stu-id="e2019-292">With ASP.Net Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="e2019-293">行为是在 Windows 上的物理文件提供程序的行为相同。</span><span class="sxs-lookup"><span data-stu-id="e2019-293">The behavior is identical to physical file provider's behavior on Windows.</span></span> 
   <span data-ttu-id="e2019-294">注意： 如果两个预编译的视图仅大小写不同，查找的结果是不确定的。</span><span class="sxs-lookup"><span data-stu-id="e2019-294">Note: If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="e2019-295">开发人员建议以匹配到区域，控制器和操作的名称的大小写的文件和目录名称的大小写。</span><span class="sxs-lookup"><span data-stu-id="e2019-295">Developers are encouraged to match the casing of file and directory names to the casing of area, controller and action names.</span></span> <span data-ttu-id="e2019-296">这将确保你的部署保持不可知的基础的文件系统。</span><span class="sxs-lookup"><span data-stu-id="e2019-296">This would ensure your deployments remain agnostic of the underlying file system.</span></span>
