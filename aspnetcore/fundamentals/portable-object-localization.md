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
ms.openlocfilehash: 4fa73ae08b10217de657681a27f6097fc3443737
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2017
---
# <a name="configure-portable-object-localization-with-orchard-core"></a>使用 Orchard 核心配置可移植对象本地化

通过[Sébastien Ros](https://github.com/sebastienros)和[Scott Addie](https://twitter.com/Scott_Addie)

本文将指导完成使用 ASP.NET Core 应用程序中的可移植对象 (PO) 文件的步骤[Orchard 核心](https://github.com/OrchardCMS/OrchardCore)framework。

**注意：** Orchard 核心不是 Microsoft 的产品。 因此，Microsoft 不提供支持此功能。

[查看或下载的示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization)([如何下载](xref:tutorials/index#how-to-download-a-sample))

## <a name="what-is-a-po-file"></a>PO 文件是什么文件？

PO 文件以包含给定语言的已翻译的字符串的文本文件分发。 改为使用 PO 文件的一些优势*.resx*文件包括：
- PO 文件支持复数形式;*.resx*文件不支持复数形式。
- PO 文件不编译像一样*.resx*文件。 在这种情况下，专用的工具和生成步骤不是必需的。
- PO 文件很好地配合协作联机编辑工具。

### <a name="example"></a>示例

下面是包含用法语，包括另一个使用其复数形式的两个字符串的转换的示例 PO 文件：

*fr.po*

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

此示例使用以下语法：

- `#:`： 指示要转换的字符串的上下文中的注释。 相同的字符串可能产生不同的翻译具体取决于它正在使用的位置。
- `msgid`： 在未转换的字符串。
- `msgstr`: 已翻译的字符串。

对于复数形式的支持，可以定义多个条目。

- `msgid_plural`： 在未转换复数形式的字符串。
- `msgstr[0]`: 这种情况 0 转换的字符串。
- `msgstr[N]`： 在已翻译的字符串的大小写的 n。

找不到 PO 文件规范[此处](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html)。

## <a name="configuring-po-file-support-in-aspnet-core"></a>在 ASP.NET 核心中配置 PO 文件支持

此示例基于 ASP.NET 核心 MVC 应用程序从 Visual Studio 2017 项目模板生成。

### <a name="referencing-the-package"></a>引用包

添加对的引用`OrchardCore.Localization.Core`NuGet 包。 适用于的[MyGet](https://www.myget.org/)在以下的包源： https://www.myget.org/F/orchardcore-preview/api/v3/index.json

*.Csproj*文件现包含类似于以下的行 （版本号可能不同）：

[!code-xml[Main](localization/sample/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a>注册该服务

添加到所需的服务`ConfigureServices`方法*Startup.cs*:

[!code-csharp[Main](localization/sample/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

添加到所需的中间件`Configure`方法*Startup.cs*:

[!code-csharp[Main](localization/sample/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

将下面的代码添加到选择的 Razor 视图中。 *About.cshtml*此示例中使用。

[!code-cshtml[Main](localization/sample/POLocalization/Views/Home/About.cshtml)]

`IViewLocalizer`注入实例并将其用于翻译文本"Hello world ！"。

### <a name="creating-a-po-file"></a>创建 PO 文件

创建名为的文件 *<culture code>.po*应用程序根文件夹中。 在此示例中，文件名是*fr.po*因为使用法语语言：

[!code-text[Main](localization/sample/POLocalization/fr.po)]

要转换的字符串和法语翻译字符串，将存储此文件。 如有必要，翻译恢复到其父区域性。 在此示例中， *fr.po*如果请求的区域性，则使用文件`fr-FR`或`fr-CA`。

### <a name="testing-the-application"></a>测试应用程序

运行你的应用程序，并导航到 URL `/Home/About`。 文本**Hello world ！** 将显示。

导航到的 URL `/Home/About?culture=fr-FR`。 文本**Bonjour le monde ！** 将显示。

## <a name="pluralization"></a>复数形式

PO 文件支持复数形式窗体时相同的字符串会以不同的方式基于基数不需要进行转换，这非常有用。 执行此任务较为复杂，因为每种语言定义自定义规则，以选择要使用哪些字符串基于基数。

Orchard 本地化包提供了一个 API 来自动调用这些不同的复数形式。

### <a name="creating-pluralization-po-files"></a>创建复数形式 PO 文件

将以下内容添加到前面所述*fr.po*文件：

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

请参阅[PO 文件是什么文件？](#what-is-a-po-file)有关在此示例中的每个条目表示的说明。

### <a name="adding-a-language-using-different-pluralization-forms"></a>添加使用不同的复数形式窗体的语言

在前面的示例使用英语和法语的字符串。 英语和法语有只有两个复数形式窗体并且共享相同的窗体规则，这是基数为一映射到第一个复数形式。 任何其他基数映射到第二个复数形式。

并非所有语言都共享相同的规则。 这一点与捷克语语言，它具有三个复数形式。

创建`cs.po`，如下所示，文件并记下复数形式需要三个不同的翻译的如何：

[!code-text[Main](localization/sample/POLocalization/cs.po)]

若要接受捷克语本地化信息，请添加`"cs"`到中支持的区域性列表`ConfigureServices`方法：

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

编辑*Views/Home/About.cshtml*文件来呈现的几个基数的本地化，复数形式的字符串：

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

**注意：**在实际方案中，变量将用于表示计数。 在这里，我们重复和三个不同的值，以公开非常具体的用例相同的代码。

在切换区域性，您会看到如下：

对于 `/Home/About`：

```html
There is one item.
There are 2 items.
There are 5 items.
```

对于 `/Home/About?culture=fr`：

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

对于 `/Home/About?culture=cs`：

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

请注意，对于捷克语的区域性，三个翻译都不同。 法语和英语区域性共享同一个构造为两个最后一个已翻译的字符串。

## <a name="advanced-tasks"></a>高级的任务

### <a name="contextualizing-strings"></a>不一而足字符串

应用程序通常包含在多个位置中要转换的字符串。 在应用程序 （Razor 视图或类文件） 中的某些位置，相同的字符串可能具有不同的翻译。 PO 文件支持的文件上下文，可用来对其进行分类所表示的字符串的概念。 使用的文件上下文，字符串可以产生不同的翻译，具体取决于文件上下文 （或缺乏文件上下文）。

PO 本地化服务使用的完整类或转换字符串时使用的视图的名称。 这通过设置上的值实现`msgctxt`条目。

请考虑为上一次添加*fr.po*示例。 Razor 视图位于*Views/Home/About.cshtml*可以通过设置保留定义为的文件上下文`msgctxt`条目的值：

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

与`msgctxt`设置这种情况下，文本转换发生时导航到`/Home/About?culture=fr-FR`。 导航到时，就不会发生转换`/Home/Contact?culture=fr-FR`。

当与给定的文件上下文匹配时没有特定条目时，Orchard 核心回退机制查找没有上下文的适当 PO 文件。 假设存在是没有为定义的特定文件上下文*Views/Home/Contact.cshtml*、 导航至`/Home/Contact?culture=fr-FR`如加载 PO 文件：

[!code-text[Main](localization/sample/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a>更改 PO 文件的位置

可以在中更改 PO 文件的默认位置`ConfigureServices`:

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

在此示例中，从加载这些 PO 文件*本地化*文件夹。

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a>实现用于查找本地化文件自定义逻辑

当定位 PO 文件所需的更复杂的逻辑`OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider`可以实现接口，并将其注册为服务。 当 PO 文件可以存储在不同位置或在文件夹层次结构中找到所需的文件时，这非常有用。

### <a name="using-a-different-default-pluralized-language"></a>使用不同变为复数形式的默认语言

此程序包包含`Plural`特定于两个复数形式的扩展方法。 对于需要更多的复数形式的语言，创建扩展方法。 使用扩展方法，你无需提供的默认语言任何本地化文件&mdash;对原始字符串已在代码中直接使用。

你可以使用多个泛型`Plural(int count, string[] pluralForms, params object[] arguments)`接受的翻译的字符串数组的重载。