---
title: "配置可移植对象本地化"
author: sebastienros
description: "本文介绍可移植的对象文件，并概述了有关使用 ASP.NET Core 应用程序的 Orchard 核心框架中的步骤。"
keywords: "ASP.NET 核心、 本地化、 区域性、 语言、 可移植的对象"
ms.author: scaddie
manager: wpickett
ms.date: 09/26/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/portable-object-localization
ms.openlocfilehash: e563cec0878a86d6c6031cfae4cde61a753486fa
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2017
---
# <a name="configure-portable-object-localization-with-orchard-core"></a><span data-ttu-id="d06a7-104">使用 Orchard 核心配置可移植对象本地化</span><span class="sxs-lookup"><span data-stu-id="d06a7-104">Configure portable object localization with Orchard Core</span></span>

<span data-ttu-id="d06a7-105">通过[Sébastien Ros](https://github.com/sebastienros)和[Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="d06a7-105">By [Sébastien Ros](https://github.com/sebastienros) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="d06a7-106">本文将指导完成使用 ASP.NET Core 应用程序中的可移植对象 (PO) 文件的步骤[Orchard 核心](https://github.com/OrchardCMS/OrchardCore)framework。</span><span class="sxs-lookup"><span data-stu-id="d06a7-106">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="d06a7-107">**注意：** Orchard 核心不是 Microsoft 的产品。</span><span class="sxs-lookup"><span data-stu-id="d06a7-107">**Note:** Orchard Core is not a Microsoft product.</span></span> <span data-ttu-id="d06a7-108">因此，Microsoft 不提供支持此功能。</span><span class="sxs-lookup"><span data-stu-id="d06a7-108">Consequently, Microsoft provides no support for this feature.</span></span>

[<span data-ttu-id="d06a7-109">查看或下载示例代码</span><span class="sxs-lookup"><span data-stu-id="d06a7-109">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization)

## <a name="what-is-a-po-file"></a><span data-ttu-id="d06a7-110">PO 文件是什么文件？</span><span class="sxs-lookup"><span data-stu-id="d06a7-110">What is a PO file?</span></span>

<span data-ttu-id="d06a7-111">PO 文件以包含给定语言的已翻译的字符串的文本文件分发。</span><span class="sxs-lookup"><span data-stu-id="d06a7-111">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="d06a7-112">改为使用 PO 文件的一些优势*.resx*文件包括：</span><span class="sxs-lookup"><span data-stu-id="d06a7-112">Some advantages of using PO files instead *.resx* files include:</span></span>
- <span data-ttu-id="d06a7-113">PO 文件支持复数形式;*.resx*文件不支持复数形式。</span><span class="sxs-lookup"><span data-stu-id="d06a7-113">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="d06a7-114">PO 文件不编译像一样*.resx*文件。</span><span class="sxs-lookup"><span data-stu-id="d06a7-114">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="d06a7-115">在这种情况下，专用的工具和生成步骤不是必需的。</span><span class="sxs-lookup"><span data-stu-id="d06a7-115">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="d06a7-116">PO 文件很好地配合协作联机编辑工具。</span><span class="sxs-lookup"><span data-stu-id="d06a7-116">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="d06a7-117">示例</span><span class="sxs-lookup"><span data-stu-id="d06a7-117">Example</span></span>

<span data-ttu-id="d06a7-118">下面是包含用法语，包括另一个使用其复数形式的两个字符串的转换的示例 PO 文件：</span><span class="sxs-lookup"><span data-stu-id="d06a7-118">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="d06a7-119">*fr.po*</span><span class="sxs-lookup"><span data-stu-id="d06a7-119">*fr.po*</span></span>

```text
#: Services/EmailService.cs:29
msgid "Enter a comma separated list of email addresses."
msgstr "Entrez une liste d'emails séparés par une virgule."

#: Views/Email.cshtml:112
msgid "The email address is \"{0}\"."
msgid_plural "The email addresses are \"{0}\"."
msgstr[0] "L'adresse email est \"{0}\"."
msgstr[1] "Les adresses email sont \"{0}\""
```

<span data-ttu-id="d06a7-120">此示例使用以下语法：</span><span class="sxs-lookup"><span data-stu-id="d06a7-120">This example uses the following syntax:</span></span>

- <span data-ttu-id="d06a7-121">`#:`： 指示要转换的字符串的上下文中的注释。</span><span class="sxs-lookup"><span data-stu-id="d06a7-121">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="d06a7-122">相同的字符串可能产生不同的翻译具体取决于它正在使用的位置。</span><span class="sxs-lookup"><span data-stu-id="d06a7-122">The same string might be translated differently depending on where it is being used.</span></span>
- <span data-ttu-id="d06a7-123">`msgid`： 在未转换的字符串。</span><span class="sxs-lookup"><span data-stu-id="d06a7-123">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="d06a7-124">`msgstr`: 已翻译的字符串。</span><span class="sxs-lookup"><span data-stu-id="d06a7-124">`msgstr`: The translated string.</span></span>

<span data-ttu-id="d06a7-125">对于复数形式的支持，可以定义多个条目。</span><span class="sxs-lookup"><span data-stu-id="d06a7-125">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="d06a7-126">`msgid_plural`： 在未转换复数形式的字符串。</span><span class="sxs-lookup"><span data-stu-id="d06a7-126">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="d06a7-127">`msgstr[0]`: 这种情况 0 转换的字符串。</span><span class="sxs-lookup"><span data-stu-id="d06a7-127">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="d06a7-128">`msgstr[N]`： 在已翻译的字符串的大小写的 n。</span><span class="sxs-lookup"><span data-stu-id="d06a7-128">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="d06a7-129">找不到 PO 文件规范[此处](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html)。</span><span class="sxs-lookup"><span data-stu-id="d06a7-129">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="d06a7-130">在 ASP.NET 核心中配置 PO 文件支持</span><span class="sxs-lookup"><span data-stu-id="d06a7-130">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="d06a7-131">此示例基于 ASP.NET 核心 MVC 应用程序从 Visual Studio 2017 项目模板生成。</span><span class="sxs-lookup"><span data-stu-id="d06a7-131">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="d06a7-132">引用包</span><span class="sxs-lookup"><span data-stu-id="d06a7-132">Referencing the package</span></span>

<span data-ttu-id="d06a7-133">添加对的引用`OrchardCore.Localization.Core`NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="d06a7-133">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span> <span data-ttu-id="d06a7-134">适用于的[MyGet](https://www.myget.org/)在以下的包源： https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span><span class="sxs-lookup"><span data-stu-id="d06a7-134">It is available on [MyGet](https://www.myget.org/) at the following package source: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span></span>

<span data-ttu-id="d06a7-135">*.Csproj*文件现包含类似于以下的行 （版本号可能不同）：</span><span class="sxs-lookup"><span data-stu-id="d06a7-135">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[Main](localization/sample/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a><span data-ttu-id="d06a7-136">注册该服务</span><span class="sxs-lookup"><span data-stu-id="d06a7-136">Registering the service</span></span>

<span data-ttu-id="d06a7-137">添加到所需的服务`ConfigureServices`方法*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d06a7-137">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[Main](localization/sample/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="d06a7-138">添加到所需的中间件`Configure`方法*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d06a7-138">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[Main](localization/sample/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="d06a7-139">将下面的代码添加到选择的 Razor 视图中。</span><span class="sxs-lookup"><span data-stu-id="d06a7-139">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="d06a7-140">*About.cshtml*此示例中使用。</span><span class="sxs-lookup"><span data-stu-id="d06a7-140">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[Main](localization/sample/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="d06a7-141">`IViewLocalizer`注入实例并将其用于翻译文本"Hello world ！"。</span><span class="sxs-lookup"><span data-stu-id="d06a7-141">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="d06a7-142">创建 PO 文件</span><span class="sxs-lookup"><span data-stu-id="d06a7-142">Creating a PO file</span></span>

<span data-ttu-id="d06a7-143">创建名为的文件 *<culture code>.po*应用程序根文件夹中。</span><span class="sxs-lookup"><span data-stu-id="d06a7-143">Create a file named *<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="d06a7-144">在此示例中，文件名是*fr.po*因为使用法语语言：</span><span class="sxs-lookup"><span data-stu-id="d06a7-144">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[Main](localization/sample/POLocalization/fr.po)]

<span data-ttu-id="d06a7-145">要转换的字符串和法语翻译字符串，将存储此文件。</span><span class="sxs-lookup"><span data-stu-id="d06a7-145">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="d06a7-146">如有必要，翻译恢复到其父区域性。</span><span class="sxs-lookup"><span data-stu-id="d06a7-146">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="d06a7-147">在此示例中， *fr.po*如果请求的区域性，则使用文件`fr-FR`或`fr-CA`。</span><span class="sxs-lookup"><span data-stu-id="d06a7-147">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="d06a7-148">测试应用程序</span><span class="sxs-lookup"><span data-stu-id="d06a7-148">Testing the application</span></span>

<span data-ttu-id="d06a7-149">运行你的应用程序，并导航到 URL `/Home/About`。</span><span class="sxs-lookup"><span data-stu-id="d06a7-149">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="d06a7-150">文本**Hello world ！**</span><span class="sxs-lookup"><span data-stu-id="d06a7-150">The text **Hello world!**</span></span> <span data-ttu-id="d06a7-151">将显示。</span><span class="sxs-lookup"><span data-stu-id="d06a7-151">is displayed.</span></span>

<span data-ttu-id="d06a7-152">导航到的 URL `/Home/About?culture=fr-FR`。</span><span class="sxs-lookup"><span data-stu-id="d06a7-152">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="d06a7-153">文本**Bonjour le monde ！**</span><span class="sxs-lookup"><span data-stu-id="d06a7-153">The text **Bonjour le monde!**</span></span> <span data-ttu-id="d06a7-154">将显示。</span><span class="sxs-lookup"><span data-stu-id="d06a7-154">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="d06a7-155">复数形式</span><span class="sxs-lookup"><span data-stu-id="d06a7-155">Pluralization</span></span>

<span data-ttu-id="d06a7-156">PO 文件支持复数形式窗体时相同的字符串会以不同的方式基于基数不需要进行转换，这非常有用。</span><span class="sxs-lookup"><span data-stu-id="d06a7-156">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="d06a7-157">执行此任务较为复杂，因为每种语言定义自定义规则，以选择要使用哪些字符串基于基数。</span><span class="sxs-lookup"><span data-stu-id="d06a7-157">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="d06a7-158">Orchard 本地化包提供了一个 API 来自动调用这些不同的复数形式。</span><span class="sxs-lookup"><span data-stu-id="d06a7-158">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="d06a7-159">创建复数形式 PO 文件</span><span class="sxs-lookup"><span data-stu-id="d06a7-159">Creating pluralization PO files</span></span>

<span data-ttu-id="d06a7-160">将以下内容添加到前面所述*fr.po*文件：</span><span class="sxs-lookup"><span data-stu-id="d06a7-160">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="d06a7-161">请参阅[PO 文件是什么文件？](#what-is-a-po-file)有关在此示例中的每个条目表示的说明。</span><span class="sxs-lookup"><span data-stu-id="d06a7-161">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="d06a7-162">添加使用不同的复数形式窗体的语言</span><span class="sxs-lookup"><span data-stu-id="d06a7-162">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="d06a7-163">在前面的示例使用英语和法语的字符串。</span><span class="sxs-lookup"><span data-stu-id="d06a7-163">English and French strings were used in the previous example.</span></span> <span data-ttu-id="d06a7-164">英语和法语有只有两个复数形式窗体并且共享相同的窗体规则，这是基数为一映射到第一个复数形式。</span><span class="sxs-lookup"><span data-stu-id="d06a7-164">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="d06a7-165">任何其他基数映射到第二个复数形式。</span><span class="sxs-lookup"><span data-stu-id="d06a7-165">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="d06a7-166">并非所有语言都共享相同的规则。</span><span class="sxs-lookup"><span data-stu-id="d06a7-166">Not all languages share the same rules.</span></span> <span data-ttu-id="d06a7-167">这一点与捷克语语言，它具有三个复数形式。</span><span class="sxs-lookup"><span data-stu-id="d06a7-167">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="d06a7-168">创建`cs.po`，如下所示，文件并记下复数形式需要三个不同的翻译的如何：</span><span class="sxs-lookup"><span data-stu-id="d06a7-168">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[Main](localization/sample/POLocalization/cs.po)]

<span data-ttu-id="d06a7-169">若要接受捷克语本地化信息，请添加`"cs"`到中支持的区域性列表`ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="d06a7-169">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

```csharp
var supportedCultures = new List<CultureInfo>
{
    new CultureInfo("en-US"),
    new CultureInfo("en"),
    new CultureInfo("fr-FR"),
    new CultureInfo("fr"),
    new CultureInfo("cs")
};
```

<span data-ttu-id="d06a7-170">编辑*Views/Home/About.cshtml*文件来呈现的几个基数的本地化，复数形式的字符串：</span><span class="sxs-lookup"><span data-stu-id="d06a7-170">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="d06a7-171">**注意：**在实际方案中，变量将用于表示计数。</span><span class="sxs-lookup"><span data-stu-id="d06a7-171">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="d06a7-172">在这里，我们重复和三个不同的值，以公开非常具体的用例相同的代码。</span><span class="sxs-lookup"><span data-stu-id="d06a7-172">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="d06a7-173">在切换区域性，您会看到如下：</span><span class="sxs-lookup"><span data-stu-id="d06a7-173">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="d06a7-174">对于 `/Home/About`：</span><span class="sxs-lookup"><span data-stu-id="d06a7-174">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="d06a7-175">对于 `/Home/About?culture=fr`：</span><span class="sxs-lookup"><span data-stu-id="d06a7-175">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="d06a7-176">对于 `/Home/About?culture=cs`：</span><span class="sxs-lookup"><span data-stu-id="d06a7-176">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="d06a7-177">请注意，对于捷克语的区域性，三个翻译都不同。</span><span class="sxs-lookup"><span data-stu-id="d06a7-177">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="d06a7-178">法语和英语区域性共享同一个构造为两个最后一个已翻译的字符串。</span><span class="sxs-lookup"><span data-stu-id="d06a7-178">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="d06a7-179">高级的任务</span><span class="sxs-lookup"><span data-stu-id="d06a7-179">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="d06a7-180">不一而足字符串</span><span class="sxs-lookup"><span data-stu-id="d06a7-180">Contextualizing strings</span></span>

<span data-ttu-id="d06a7-181">应用程序通常包含在多个位置中要转换的字符串。</span><span class="sxs-lookup"><span data-stu-id="d06a7-181">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="d06a7-182">在应用程序 （Razor 视图或类文件） 中的某些位置，相同的字符串可能具有不同的翻译。</span><span class="sxs-lookup"><span data-stu-id="d06a7-182">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="d06a7-183">PO 文件支持的文件上下文，可用来对其进行分类所表示的字符串的概念。</span><span class="sxs-lookup"><span data-stu-id="d06a7-183">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="d06a7-184">使用的文件上下文，字符串可以产生不同的翻译，具体取决于文件上下文 （或缺乏文件上下文）。</span><span class="sxs-lookup"><span data-stu-id="d06a7-184">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="d06a7-185">PO 本地化服务使用的完整类或转换字符串时使用的视图的名称。</span><span class="sxs-lookup"><span data-stu-id="d06a7-185">The PO localization services use the name of the full class or the view that is used when translating a string.</span></span> <span data-ttu-id="d06a7-186">这通过设置上的值实现`msgctxt`条目。</span><span class="sxs-lookup"><span data-stu-id="d06a7-186">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="d06a7-187">请考虑为上一次添加*fr.po*示例。</span><span class="sxs-lookup"><span data-stu-id="d06a7-187">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="d06a7-188">Razor 视图位于*Views/Home/About.cshtml*可以通过设置保留定义为的文件上下文`msgctxt`条目的值：</span><span class="sxs-lookup"><span data-stu-id="d06a7-188">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="d06a7-189">与`msgctxt`设置这种情况下，文本转换发生时导航到`/Home/About?culture=fr-FR`。</span><span class="sxs-lookup"><span data-stu-id="d06a7-189">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="d06a7-190">导航到时，就不会发生转换`/Home/Contact?culture=fr-FR`。</span><span class="sxs-lookup"><span data-stu-id="d06a7-190">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="d06a7-191">当与给定的文件上下文匹配时没有特定条目时，Orchard 核心回退机制查找没有上下文的适当 PO 文件。</span><span class="sxs-lookup"><span data-stu-id="d06a7-191">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="d06a7-192">假设存在是没有为定义的特定文件上下文*Views/Home/Contact.cshtml*、 导航至`/Home/Contact?culture=fr-FR`如加载 PO 文件：</span><span class="sxs-lookup"><span data-stu-id="d06a7-192">Assuming there is no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[Main](localization/sample/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="d06a7-193">更改 PO 文件的位置</span><span class="sxs-lookup"><span data-stu-id="d06a7-193">Changing the location of PO files</span></span>

<span data-ttu-id="d06a7-194">可以在中更改 PO 文件的默认位置`ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d06a7-194">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="d06a7-195">在此示例中，从加载这些 PO 文件*本地化*文件夹。</span><span class="sxs-lookup"><span data-stu-id="d06a7-195">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="d06a7-196">实现用于查找本地化文件自定义逻辑</span><span class="sxs-lookup"><span data-stu-id="d06a7-196">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="d06a7-197">当定位 PO 文件所需的更复杂的逻辑`OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider`可以实现接口，并将其注册为服务。</span><span class="sxs-lookup"><span data-stu-id="d06a7-197">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="d06a7-198">当 PO 文件可以存储在不同位置或在文件夹层次结构中找到所需的文件时，这非常有用。</span><span class="sxs-lookup"><span data-stu-id="d06a7-198">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="d06a7-199">使用不同变为复数形式的默认语言</span><span class="sxs-lookup"><span data-stu-id="d06a7-199">Using a different default pluralized language</span></span>

<span data-ttu-id="d06a7-200">此程序包包含`Plural`特定于两个复数形式的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="d06a7-200">The package includes a `Plural` extension method that is specific to two plural forms.</span></span> <span data-ttu-id="d06a7-201">对于需要更多的复数形式的语言，创建扩展方法。</span><span class="sxs-lookup"><span data-stu-id="d06a7-201">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="d06a7-202">使用扩展方法，你无需提供的默认语言任何本地化文件&mdash;对原始字符串已在代码中直接使用。</span><span class="sxs-lookup"><span data-stu-id="d06a7-202">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="d06a7-203">你可以使用多个泛型`Plural(int count, string[] pluralForms, params object[] arguments)`接受的翻译的字符串数组的重载。</span><span class="sxs-lookup"><span data-stu-id="d06a7-203">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>