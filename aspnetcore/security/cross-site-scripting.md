---
title: "防止跨站点脚本"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 95790927-2bfe-445e-b1fd-429c2c7030ce
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/cross-site-scripting
ms.openlocfilehash: d0880fda4ee726bd30a48cce0907a3887f2a4545
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2017
---
# <a name="preventing-cross-site-scripting"></a><span data-ttu-id="8e672-103">防止跨站点脚本</span><span class="sxs-lookup"><span data-stu-id="8e672-103">Preventing Cross-Site Scripting</span></span>

<a name="security-cross-site-scripting"></a>

<span data-ttu-id="8e672-104">跨站点脚本 (XSS) 是一个安全漏洞，这会使攻击者将客户端脚本 (通常是 JavaScript) 放入网页。</span><span class="sxs-lookup"><span data-stu-id="8e672-104">Cross-Site Scripting (XSS) is a security vulnerability which enables an attacker to place client side scripts (usually JavaScript) into web pages.</span></span> <span data-ttu-id="8e672-105">当其他用户加载受影响的页的攻击者脚本将运行时，从而使攻击者窃取 cookie 和会话令牌更改通过 DOM 操作网页的内容或将浏览器重定向到另一页。</span><span class="sxs-lookup"><span data-stu-id="8e672-105">When other users load affected pages the attackers scripts will run, enabling the attacker to steal cookies and session tokens, change the contents of the web page through DOM manipulation or redirect the browser to another page.</span></span> <span data-ttu-id="8e672-106">当应用程序接受用户输入，并输出它在页中而不验证、 编码或转义它时，通常会发生 XSS 漏洞。</span><span class="sxs-lookup"><span data-stu-id="8e672-106">XSS vulnerabilities generally occur when an application takes user input and outputs it in a page without validating, encoding or escaping it.</span></span>

## <a name="protecting-your-application-against-xss"></a><span data-ttu-id="8e672-107">保护 XSS 针对应用程序</span><span class="sxs-lookup"><span data-stu-id="8e672-107">Protecting your application against XSS</span></span>

<span data-ttu-id="8e672-108">在通过诱使你的应用程序插入到工作原理的基本级别 XSS`<script>`标记到呈现页中，或通过插入`On*`插入元素的事件。</span><span class="sxs-lookup"><span data-stu-id="8e672-108">At a basic level XSS works by tricking your application into inserting a `<script>` tag into your rendered page, or by inserting an `On*` event into an element.</span></span> <span data-ttu-id="8e672-109">开发人员应使用以下的预防步骤以避免 XSS 引入其应用程序。</span><span class="sxs-lookup"><span data-stu-id="8e672-109">Developers should use the following prevention steps to avoid introducing XSS into their application.</span></span>

1. <span data-ttu-id="8e672-110">永远不会，将不受信任的数据放入你的 HTML 输入中，除非按照下面的步骤的其余部分。</span><span class="sxs-lookup"><span data-stu-id="8e672-110">Never put untrusted data into your HTML input, unless you follow the rest of the steps below.</span></span> <span data-ttu-id="8e672-111">不受信任的数据是可能由攻击者、 HTML 窗体输入、 查询字符串、 HTTP 标头，甚至数据源从数据库中，攻击者可能能够破坏您的数据库，即使它们不能违反你的应用程序控制任何数据。</span><span class="sxs-lookup"><span data-stu-id="8e672-111">Untrusted data is any data that may be controlled by an attacker, HTML form inputs, query strings, HTTP headers, even data sourced from a database as an attacker may be able to breach your database even if they cannot breach your application.</span></span>

2. <span data-ttu-id="8e672-112">将 HTML 元素中的不受信任的数据之前请确保它为 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="8e672-112">Before putting untrusted data inside an HTML element ensure it is HTML encoded.</span></span> <span data-ttu-id="8e672-113">HTML 编码采取字符如&lt;和会变成安全的形式，例如&amp;lt;</span><span class="sxs-lookup"><span data-stu-id="8e672-113">HTML encoding takes characters such as &lt; and changes them into a safe form like &amp;lt;</span></span>

3. <span data-ttu-id="8e672-114">将不受信任的数据放入 HTML 特性之前请确保它是 HTML 编码的属性。</span><span class="sxs-lookup"><span data-stu-id="8e672-114">Before putting untrusted data into an HTML attribute ensure it is HTML attribute encoded.</span></span> <span data-ttu-id="8e672-115">HTML 特性编码为 HTML 编码的超集，并将其他字符编码如"和。</span><span class="sxs-lookup"><span data-stu-id="8e672-115">HTML attribute encoding is a superset of HTML encoding and encodes additional characters such as " and '.</span></span>

4. <span data-ttu-id="8e672-116">将不受信任的数据放入 JavaScript 之前将数据放在一个 HTML 元素在运行时检索其内容。</span><span class="sxs-lookup"><span data-stu-id="8e672-116">Before putting untrusted data into JavaScript place the data in an HTML element whose contents you retrieve at runtime.</span></span> <span data-ttu-id="8e672-117">如果这不可能，则确保数据被编码 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="8e672-117">If this is not possible then ensure the data is JavaScript encoded.</span></span> <span data-ttu-id="8e672-118">JavaScript 编码 javascript 采用危险的字符并将其替换为其十六进制，例如&lt;将编码为`\u003C`。</span><span class="sxs-lookup"><span data-stu-id="8e672-118">JavaScript encoding takes dangerous characters for JavaScript and replaces them with their hex, for example &lt; would be encoded as `\u003C`.</span></span>

5. <span data-ttu-id="8e672-119">将不受信任的数据放入一个 URL 查询字符串之前确保它是编码的 URL。</span><span class="sxs-lookup"><span data-stu-id="8e672-119">Before putting untrusted data into a URL query string ensure it is URL encoded.</span></span>

## <a name="html-encoding-using-razor"></a><span data-ttu-id="8e672-120">使用 Razor 的 HTML 编码</span><span class="sxs-lookup"><span data-stu-id="8e672-120">HTML Encoding using Razor</span></span>

<span data-ttu-id="8e672-121">自动使用 MVC Razor 引擎将所有编码输出源自变量，除非您真正努力工作以避免其执行此操作。</span><span class="sxs-lookup"><span data-stu-id="8e672-121">The Razor engine used in MVC automatically encodes all output sourced from variables, unless you work really hard to prevent it doing so.</span></span> <span data-ttu-id="8e672-122">它使用编码规则，每当你使用的 HTML 特性 *@* 指令。</span><span class="sxs-lookup"><span data-stu-id="8e672-122">It uses HTML Attribute encoding rules whenever you use the *@* directive.</span></span> <span data-ttu-id="8e672-123">为 HTML 属性编码为 HTML 编码，这意味着你不必考虑自己是否应使用 HTML 编码或 HTML 特性编码的超集。</span><span class="sxs-lookup"><span data-stu-id="8e672-123">As HTML attribute encoding is a superset of HTML encoding this means you don't have to concern yourself with whether you should use HTML encoding or HTML attribute encoding.</span></span> <span data-ttu-id="8e672-124">你必须确保您仅使用在 HTML 上下文中，不是在尝试将直接插入 JavaScript 不受信任的输入时。</span><span class="sxs-lookup"><span data-stu-id="8e672-124">You must ensure that you only use @ in an HTML context, not when attempting to insert untrusted input directly into JavaScript.</span></span> <span data-ttu-id="8e672-125">标记帮助程序还会编码标记参数中使用的输入。</span><span class="sxs-lookup"><span data-stu-id="8e672-125">Tag helpers will also encode input you use in tag parameters.</span></span>

<span data-ttu-id="8e672-126">考虑以下 Razor 视图;</span><span class="sxs-lookup"><span data-stu-id="8e672-126">Take the following Razor view;</span></span>

```none
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

<span data-ttu-id="8e672-127">此视图输出的内容*untrustedInput*变量。</span><span class="sxs-lookup"><span data-stu-id="8e672-127">This view outputs the contents of the *untrustedInput* variable.</span></span> <span data-ttu-id="8e672-128">此变量包含在 XSS 攻击中，即使用某些字符&lt;，"和&gt;。</span><span class="sxs-lookup"><span data-stu-id="8e672-128">This variable includes some characters which are used in XSS attacks, namely &lt;, " and &gt;.</span></span> <span data-ttu-id="8e672-129">查看源显示呈现的输出编码为：</span><span class="sxs-lookup"><span data-stu-id="8e672-129">Examining the source shows the rendered output encoded as:</span></span>

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> <span data-ttu-id="8e672-130">ASP.NET 核心 MVC 提供`HtmlString`不在输出时自动编码的类。</span><span class="sxs-lookup"><span data-stu-id="8e672-130">ASP.NET Core MVC provides an `HtmlString` class which is not automatically encoded upon output.</span></span> <span data-ttu-id="8e672-131">这应永远不会用于与不受信任的输入结合使用这会将公开 XSS 漏洞。</span><span class="sxs-lookup"><span data-stu-id="8e672-131">This should never be used in combination with untrusted input as this will expose an XSS vulnerability.</span></span>

## <a name="javascript-encoding-using-razor"></a><span data-ttu-id="8e672-132">使用 Razor Javascript 编码</span><span class="sxs-lookup"><span data-stu-id="8e672-132">Javascript Encoding using Razor</span></span>

<span data-ttu-id="8e672-133">可能有些时候你想要插入处理在视图中的 JavaScript 值。</span><span class="sxs-lookup"><span data-stu-id="8e672-133">There may be times you want to insert a value into JavaScript to process in your view.</span></span> <span data-ttu-id="8e672-134">有两种方法可以实现此目的。</span><span class="sxs-lookup"><span data-stu-id="8e672-134">There are two ways to do this.</span></span> <span data-ttu-id="8e672-135">插入简单值的最安全方法是将值放在一个标记的数据属性并检索你在 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="8e672-135">The safest way to insert simple values is to place the value in a data attribute of a tag and retrieve it in your JavaScript.</span></span> <span data-ttu-id="8e672-136">例如: </span><span class="sxs-lookup"><span data-stu-id="8e672-136">For example:</span></span>

```none
@{
       var untrustedInput = "<\"123\">";
   }

   <div
       id="injectedData"
       data-untrustedinput="@untrustedInput" />

   <script>
     var injectedData = document.getElementById("injectedData");

     // All clients
     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     // HTML 5 clients only
     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

<span data-ttu-id="8e672-137">这将生成以下 HTML</span><span class="sxs-lookup"><span data-stu-id="8e672-137">This will produce the following HTML</span></span>

```html
<div
     id="injectedData"
     data-untrustedinput="&lt;&quot;123&quot;&gt;" />

   <script>
     var injectedData = document.getElementById("injectedData");

     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

<span data-ttu-id="8e672-138">其中，运行时，将呈现以下操作;</span><span class="sxs-lookup"><span data-stu-id="8e672-138">Which, when it runs, will render the following;</span></span>

```none
<"123">
   <"123">
   ```

<span data-ttu-id="8e672-139">你还可以直接调用 JavaScript 编码器</span><span class="sxs-lookup"><span data-stu-id="8e672-139">You can also call the JavaScript encoder directly,</span></span>

```none
@using System.Text.Encodings.Web;
   @inject JavaScriptEncoder encoder;

   @{
       var untrustedInput = "<\"123\">";
   }

   <script>
       document.write("@encoder.Encode(untrustedInput)");
   </script>
   ```

<span data-ttu-id="8e672-140">这将呈现在浏览器中，如下所示;</span><span class="sxs-lookup"><span data-stu-id="8e672-140">This will render in the browser as follows;</span></span>

```html
<script>
       document.write("\u003C\u0022123\u0022\u003E");
   </script>
   ```

>[!WARNING]
> <span data-ttu-id="8e672-141">请勿连接 JavaScript 创建 DOM 元素中不受信任的输入。</span><span class="sxs-lookup"><span data-stu-id="8e672-141">Do not concatenate untrusted input in JavaScript to create DOM elements.</span></span> <span data-ttu-id="8e672-142">应使用`createElement()`并将属性值分配适当如`node.TextContent=`，或使用`element.SetAttribute()` / `element[attribute]=`否则向基于 DOM 的 XSS 公开自己。</span><span class="sxs-lookup"><span data-stu-id="8e672-142">You should use `createElement()` and assign property values appropriately such as `node.TextContent=`, or use `element.SetAttribute()`/`element[attribute]=` otherwise you expose yourself to DOM-based XSS.</span></span>

## <a name="accessing-encoders-in-code"></a><span data-ttu-id="8e672-143">访问代码中的编码器</span><span class="sxs-lookup"><span data-stu-id="8e672-143">Accessing encoders in code</span></span>

<span data-ttu-id="8e672-144">HTML、 JavaScript 和 URL 编码器可供代码使用两种方式，可以将它们通过注入[依赖关系注入](../fundamentals/dependency-injection.md#fundamentals-dependency-injection)或者你可以使用中包含的默认编码器`System.Text.Encodings.Web`命名空间。</span><span class="sxs-lookup"><span data-stu-id="8e672-144">The HTML, JavaScript and URL encoders are available to your code in two ways, you can inject them via [dependency injection](../fundamentals/dependency-injection.md#fundamentals-dependency-injection) or you can use the default encoders contained in the `System.Text.Encodings.Web` namespace.</span></span> <span data-ttu-id="8e672-145">如果你使用的默认编码器，则有你应用于字符范围为安全处理将不会生效-默认编码器使用的可能的安全编码规则。</span><span class="sxs-lookup"><span data-stu-id="8e672-145">If you use the default encoders then any  you applied to character ranges to be treated as safe will not take effect - the default encoders use the safest encoding rules possible.</span></span>

<span data-ttu-id="8e672-146">若要使用可配置编码器通过你的构造函数应采用的 DI *HtmlEncoder*， *JavaScriptEncoder*和*UrlEncoder*作为适当的参数。</span><span class="sxs-lookup"><span data-stu-id="8e672-146">To use the configurable encoders via DI your constructors should take an *HtmlEncoder*, *JavaScriptEncoder* and *UrlEncoder* parameter as appropriate.</span></span> <span data-ttu-id="8e672-147">例如，</span><span class="sxs-lookup"><span data-stu-id="8e672-147">For example;</span></span>

```csharp
public class HomeController : Controller
   {
       HtmlEncoder _htmlEncoder;
       JavaScriptEncoder _javaScriptEncoder;
       UrlEncoder _urlEncoder;

       public HomeController(HtmlEncoder htmlEncoder,
                             JavaScriptEncoder javascriptEncoder,
                             UrlEncoder urlEncoder)
       {
           _htmlEncoder = htmlEncoder;
           _javaScriptEncoder = javascriptEncoder;
           _urlEncoder = urlEncoder;
       }
   }
   ```

## <a name="encoding-url-parameters"></a><span data-ttu-id="8e672-148">编码的 URL 参数</span><span class="sxs-lookup"><span data-stu-id="8e672-148">Encoding URL Parameters</span></span>

<span data-ttu-id="8e672-149">如果你想要构建 URL 查询字符串以不受信任的输入作为值使用`UrlEncoder`值进行编码。</span><span class="sxs-lookup"><span data-stu-id="8e672-149">If you want to build a URL query string with untrusted input as a value use the `UrlEncoder` to encode the value.</span></span> <span data-ttu-id="8e672-150">例如，</span><span class="sxs-lookup"><span data-stu-id="8e672-150">For example,</span></span>

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

<span data-ttu-id="8e672-151">编码 encodedValue 后变量将包含`%22Quoted%20Value%20with%20spaces%20and%20%26%22`。</span><span class="sxs-lookup"><span data-stu-id="8e672-151">After encoding the encodedValue variable will contain `%22Quoted%20Value%20with%20spaces%20and%20%26%22`.</span></span> <span data-ttu-id="8e672-152">空格、 引号、 标点和其他不安全字符百分比编码为其十六进制值，例如空格字符将成为 %20。</span><span class="sxs-lookup"><span data-stu-id="8e672-152">Spaces, quotes, punctuation and other unsafe characters will be percent encoded to their hexadecimal value, for example a space character will become %20.</span></span>

>[!WARNING]
> <span data-ttu-id="8e672-153">不要使用不受信任的输入的 URL 路径的一部分。</span><span class="sxs-lookup"><span data-stu-id="8e672-153">Do not use untrusted input as part of a URL path.</span></span> <span data-ttu-id="8e672-154">始终将不受信任的输入传递作为查询字符串值。</span><span class="sxs-lookup"><span data-stu-id="8e672-154">Always pass untrusted input as a query string value.</span></span>

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a><span data-ttu-id="8e672-155">自定义编码器</span><span class="sxs-lookup"><span data-stu-id="8e672-155">Customizing the Encoders</span></span>

<span data-ttu-id="8e672-156">默认情况下编码器使用安全列表限制为基本拉丁 Unicode 范围并为其等效的字符代码在该范围之外的所有字符进行都编码。</span><span class="sxs-lookup"><span data-stu-id="8e672-156">By default encoders use a safe list limited to the Basic Latin Unicode range and encode all characters outside of that range as their character code equivalents.</span></span> <span data-ttu-id="8e672-157">此行为还会影响 Razor TagHelper 和 HtmlHelper 呈现，因为它将使用编码器输出字符串。</span><span class="sxs-lookup"><span data-stu-id="8e672-157">This behavior also affects Razor TagHelper and HtmlHelper rendering as it will use the encoders to output your strings.</span></span>

<span data-ttu-id="8e672-158">这背后的原因是为了防止未知或将来的浏览器 bug （以前的浏览器 bug 具有触发向上分析基于非英语字符的处理）。</span><span class="sxs-lookup"><span data-stu-id="8e672-158">The reasoning behind this is to protect against unknown or future browser bugs (previous browser bugs have tripped up parsing based on the processing of non-English characters).</span></span> <span data-ttu-id="8e672-159">如果您的网站进行大量使用非拉丁字符，例如中文、 西里尔文或其他人这是可能不希望的行为。</span><span class="sxs-lookup"><span data-stu-id="8e672-159">If your web site makes heavy use of non-Latin characters, such as Chinese, Cyrillic or others this is probably not the behavior you want.</span></span>

<span data-ttu-id="8e672-160">你可以自定义编码器安全列表，以包括范围使用适合于在启动期间，你的应用程序中的 Unicode `ConfigureServices()`。</span><span class="sxs-lookup"><span data-stu-id="8e672-160">You can customize the encoder safe lists to include Unicode ranges appropriate to your application during startup, in `ConfigureServices()`.</span></span>

<span data-ttu-id="8e672-161">例如，使用默认配置可能会使用 Razor HtmlHelper 如下所示;</span><span class="sxs-lookup"><span data-stu-id="8e672-161">For example, using the default configuration you might use a Razor HtmlHelper like so;</span></span>

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

<span data-ttu-id="8e672-162">当您查看的网页的源时，你将看到已呈现，如下所示，使用中文文本编码;</span><span class="sxs-lookup"><span data-stu-id="8e672-162">When you view the source of the web page you will see it has been rendered as follows, with the Chinese text encoded;</span></span>

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

<span data-ttu-id="8e672-163">若要扩大范围的字符视为安全编码器您将插入以下行到`ConfigureServices()`中的方法`startup.cs`;</span><span class="sxs-lookup"><span data-stu-id="8e672-163">To widen the characters treated as safe by the encoder you would insert the following line into the `ConfigureServices()` method in `startup.cs`;</span></span>

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

<span data-ttu-id="8e672-164">此示例扩大安全的列表，使其包含 Unicode 范围 CjkUnifiedIdeographs。</span><span class="sxs-lookup"><span data-stu-id="8e672-164">This example widens the safe list to include the Unicode Range CjkUnifiedIdeographs.</span></span> <span data-ttu-id="8e672-165">呈现的输出将现在变为</span><span class="sxs-lookup"><span data-stu-id="8e672-165">The rendered output would now become</span></span>

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

<span data-ttu-id="8e672-166">安全列表范围被指定为 Unicode 代码图表、 不语言。</span><span class="sxs-lookup"><span data-stu-id="8e672-166">Safe list ranges are specified as Unicode code charts, not languages.</span></span> <span data-ttu-id="8e672-167">[Unicode 标准](http://unicode.org/)具有的列表[代码图表](http://www.unicode.org/charts/index.html)可用来查找包含你字符图表。</span><span class="sxs-lookup"><span data-stu-id="8e672-167">The [Unicode standard](http://unicode.org/) has a list of [code charts](http://www.unicode.org/charts/index.html) you can use to find the chart containing your characters.</span></span> <span data-ttu-id="8e672-168">每个编码器，Html、 JavaScript 和 Url，必须单独配置。</span><span class="sxs-lookup"><span data-stu-id="8e672-168">Each encoder, Html, JavaScript and Url, must be configured separately.</span></span>

> [!NOTE]
> <span data-ttu-id="8e672-169">自定义安全列表仅影响通过 DI 来源的编码器。</span><span class="sxs-lookup"><span data-stu-id="8e672-169">Customization of the safe list only affects encoders sourced via DI.</span></span> <span data-ttu-id="8e672-170">如果你直接访问通过编码器`System.Text.Encodings.Web.*Encoder.Default`然后默认情况下，基本拉丁将使用仅安全列表。</span><span class="sxs-lookup"><span data-stu-id="8e672-170">If you directly access an encoder via `System.Text.Encodings.Web.*Encoder.Default` then the default, Basic Latin only safelist will be used.</span></span>

## <a name="where-should-encoding-take-place"></a><span data-ttu-id="8e672-171">编码的 take 应放置位置？</span><span class="sxs-lookup"><span data-stu-id="8e672-171">Where should encoding take place?</span></span>

<span data-ttu-id="8e672-172">常规接受的做法是编码发生在输出和编码的值应永远不会存储在数据库中。</span><span class="sxs-lookup"><span data-stu-id="8e672-172">The general accepted practice is that encoding takes place at the point of output and encoded values should never be stored in a database.</span></span> <span data-ttu-id="8e672-173">在输出编码，可将使用数据，例如，从 HTML 为查询字符串值更改。</span><span class="sxs-lookup"><span data-stu-id="8e672-173">Encoding at the point of output allows you to change the use of data, for example, from HTML to a query string value.</span></span> <span data-ttu-id="8e672-174">它还使您可以轻松地搜索你的数据，而无需搜索前对值进行编码，并使您可以充分利用任何更改或对编码器所做的 bug 修复。</span><span class="sxs-lookup"><span data-stu-id="8e672-174">It also enables you to easily search your data without having to encode values before searching and allows you to take advantage of any changes or bug fixes made to encoders.</span></span>

## <a name="validation-as-an-xss-prevention-technique"></a><span data-ttu-id="8e672-175">验证在 XSS 预防方法</span><span class="sxs-lookup"><span data-stu-id="8e672-175">Validation as an XSS prevention technique</span></span>

<span data-ttu-id="8e672-176">验证可以在限制 XSS 攻击的有用工具。</span><span class="sxs-lookup"><span data-stu-id="8e672-176">Validation can be a useful tool in limiting XSS attacks.</span></span> <span data-ttu-id="8e672-177">例如，一个简单的数值字符串只能包含字符 0-9 不会触发 XSS 受到攻击。</span><span class="sxs-lookup"><span data-stu-id="8e672-177">For example, a simple numeric string containing only the characters 0-9 will not trigger an XSS attack.</span></span> <span data-ttu-id="8e672-178">验证变得更复杂应想要接受用户输入的中的 HTML HTML 输入内容分析为难以进行，即使不是不可能。</span><span class="sxs-lookup"><span data-stu-id="8e672-178">Validation becomes more complicated should you wish to accept HTML in user input - parsing HTML input is difficult, if not impossible.</span></span> <span data-ttu-id="8e672-179">MarkDown 以及其他文本的格式将丰富的输入的更安全选项。</span><span class="sxs-lookup"><span data-stu-id="8e672-179">MarkDown and other text formats would be a safer option for rich input.</span></span> <span data-ttu-id="8e672-180">你应永远不会依赖于单独的验证。</span><span class="sxs-lookup"><span data-stu-id="8e672-180">You should never rely on validation alone.</span></span> <span data-ttu-id="8e672-181">始终对不受信任的输入，输出之前进行编码，无论何种验证已执行。</span><span class="sxs-lookup"><span data-stu-id="8e672-181">Always encode untrusted input before output, no matter what validation you have performed.</span></span>
