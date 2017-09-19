---
title: "在 ASP.NET 核心中的创作标记帮助程序"
author: rick-anderson
description: "了解如何创作 ASP.NET Core 中的标记帮助程序。"
keywords: "ASP.NET 核心，标记帮助程序"
ms.author: riande
manager: wpickett
ms.date: 06/14/2017
ms.topic: article
ms.assetid: 4f16d978-5695-4abf-a785-fdaabf3bbcb9
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/tag-helpers/authoring
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1a5222da1380c2fe768b287bfa1a49b300c02f2b
ms.sourcegitcommit: 67f54fabbfa4e3942f5bfe1f8a7fdfe4a7a75358
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2017
---
# <a name="authoring-tag-helpers-in-aspnet-core-a-walkthrough-with-samples"></a>在 ASP.NET 核心，使用示例演练中的创作标记帮助程序

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/tag-helpers/authoring/sample)

## <a name="getting-started-with-tag-helpers"></a>开始使用标记帮助程序

本教程介绍编程标记帮助程序。 [标记帮助器简介](intro.md)介绍标记帮助程序提供的优势。

标记帮助器是实现任何类`ITagHelper`接口。 但是，在创作标记帮助器时，你通常派生自`TagHelper`，执行这样的可以访问`Process`方法。

1. 创建一个名为的新 ASP.NET Core 项目**AuthoringTagHelpers**。 为此项目，你不需要身份验证。

2. 创建一个文件夹来保存调用标记帮助程序*TagHelpers*。 *TagHelpers*文件夹是*不*必需的但它是合理的约定。 现在让我们开始吧编写一些简单标记帮助程序。

## <a name="a-minimal-tag-helper"></a>最小的标记帮助器

在本部分中，你编写的标记帮助程序更新的电子邮件标记。 例如: 

```html
<email>Support</email>
   ```

该服务器将使用我们的电子邮件标记帮助器以将该标记转换为以下：

```html
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
   ```

即的定位点标记，使此电子邮件链接。 你可能想要执行此操作，如果你在编写博客引擎并且需要使用它来发送电子邮件发送的市场营销、 支持和其他联系人，所有内容都在同一个域。

1.  添加以下`EmailTagHelper`类到*TagHelpers*文件夹。

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1EmailTagHelperCopy.cs)]
    
    **注意：**
    
    * 标记帮助器使用面向根类名称的元素的命名约定 (减*TagHelper*类名称的一部分)。 在此示例中的根名称**电子邮件**TagHelper 是*电子邮件*，因此`<email>`标记将被加载目标。 此命名约定也适用于大多数标记帮助程序，稍后我将介绍如何重写它。
    
    * `EmailTagHelper` 类派生自 `TagHelper`。 `TagHelper`类提供用于编写标记帮助器方法和属性。
    
    * 重写`Process`方法控制执行时的标记帮助程序的用途。 `TagHelper`类还提供的异步版本 (`ProcessAsync`) 使用相同的参数。
    
    * 上下文参数`Process`(和`ProcessAsync`) 包含与当前的 HTML 标记的执行关联信息。
    
    * 输出参数`Process`(和`ProcessAsync`) 包含用于生成的 HTML 标记和内容的原始源具有代表性的有状态 HTML 元素。
    
    * 我们类名称具有是由后缀为**TagHelper**，即*不*必需的但它被视为最佳做法约定。 你无法将类声明为：
    
    ```csharp
    public class Email : TagHelper
    ```

2.  若要使`EmailTagHelper`类可用于所有我们 Razor 视图中，添加`addTagHelper`指令至*Views/_ViewImports.cshtml*文件：[!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopyEmail.cshtml?highlight=2,3)]
    
    上面的代码中使用通配符语法来指定在我们的程序集中的所有标记帮助程序都将提供。 之后的第一个字符串`@addTagHelper`指定要加载的标记帮助程序 (使用"*"的所有标记帮助程序)，和第二个字符串"AuthoringTagHelpers"指定标记帮助程序是中的程序集。 另请注意第二行使中使用通配符的语法的 ASP.NET 核心 MVC 标记帮助器 (中讨论了这些帮助器[简介标记帮助程序](intro.md)。)它是`@addTagHelper`Razor 视图提供的标记帮助程序的指令。 或者，你可以提供完全限定的名称 (FQN) 的标记帮助程序，如下所示：
    
    [!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImports.cshtml?highlight=3&range=1-3)]
    
    若要将标记帮助器添加到使用 FQN 的视图，你首先添加 FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`)，，然后是程序集名称 (*AuthoringTagHelpers*)。 大多数开发人员会更喜欢使用通配符语法。 [标记帮助器简介](intro.md)进入标记帮助器添加、 删除、 层次结构中，和通配符语法中的详细信息。
    
3.  更新中的标记*Views/Home/Contact.cshtml*使用这些更改的文件：

    [!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

4.  运行应用并使用你最喜欢的浏览器来查看 HTML 源，以便验证电子邮件标记将替换定位点标记 (例如， `<a>Support</a>`)。 *支持*和*市场营销*呈现为链接，但它们不具有`href`属性以使其正常工作。 我们将在下一部分中修复此问题。

注意： 如 HTML 标记和属性、 标记、 类名和 Razor，和 C# 中的属性不区分大小写。

## <a name="setattribute-and-setcontent"></a>SetAttribute 和 SetContent

在本部分中，我们将更新`EmailTagHelper`，以便它将创建电子邮件的有效定位点标记。 我们将更新，使其带有 Razor 视图中的信息 (形式`mail-to`特性) 并使用它在生成定位点。

更新`EmailTagHelper`替换为以下类：

[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?range=6-22)]

**注意：**

* 有关标记帮助程序 Pascal 大小写形式类和属性的名称转换为其[降低 kebab 用例](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101)。 因此，若要使用`MailTo`属性中，你将使用`<email mail-to="value"/>`等效。

* 最后一行将已完成的内容设置我们按最小方式功能标记帮助器。

* 突出显示的行将显示用于将属性添加的语法：

[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?highlight=6&range=14-21)]

只要当前不存在属性集合中，该方法将适用于"href"的属性。 你还可以使用`output.Attributes.Add`方法将标记帮助器属性添加到标记特性的集合的末尾。

1.  更新中的标记*Views/Home/Contact.cshtml*使用这些更改的文件：[!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/ContactCopy.cshtml?highlight=15,16)]

2.  运行应用程序并验证它会生成正确的链接。
    
    > [!NOTE]
    >如果你打算编写电子邮件标记自关闭 (`<email mail-to="Rick" />`)，则最终输出还为自结束。 若要启用用于编写将标记与开始标记的功能 (`<email mail-to="Rick">`) 必须修饰替换为以下类：
    >
    > [!code-csharp[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailVoid.cs?highlight=1&range=6-10)]
    
    具有自结束电子邮件标记帮助程序，则输出为`<a href="mailto:Rick@contoso.com" />`。 自结束的定位点标记不是有效的 HTML，因此你不希望创建一个，但你可能想要创建的标记帮助程序为自结束。 标记帮助程序设置的一种`TagMode`在读取标记后的属性。
    
### <a name="processasync"></a>ProcessAsync

在本部分中，我们将编写的异步电子邮件帮助器。

1.  替换`EmailTagHelper`类替换为以下代码：

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelper.cs?range=6-17)]

    **注意：**

    * 此版本使用异步`ProcessAsync`方法。 异步`GetChildContentAsync`返回`Task`包含`TagHelperContent`。

    * 使用`output`参数，以获取内容的 HTML 元素。

2.  进行以下更改到*Views/Home/Contact.cshtml*文件以便标记帮助程序可以获取目标电子邮件。

    [!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

3.  运行应用程序并验证它会生成有效的电子邮件链接。

### <a name="removeall-precontentsethtmlcontent-and-postcontentsethtmlcontent"></a>RemoveAll、 PreContent.SetHtmlContent 和 PostContent.SetHtmlContent

1.  添加以下`BoldTagHelper`类到*TagHelpers*文件夹。

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/BoldTagHelper.cs)]

    **注意：**
    
    * `[HtmlTargetElement]`属性指定任何包含 HTML 特性的 HTML 元素名为"bold"的属性参数将匹配，传递和`Process`类中的重写方法将运行。 在我们示例中，`Process`方法中删除"粗体"的属性，并在包含标记与`<strong></strong>`。
    
    * 由于你不想替换内容的现有标记，你必须编写打开`<strong>`使用标记`PreContent.SetHtmlContent`方法和结束`</strong>`使用标记`PostContent.SetHtmlContent`方法。
    
2.  修改*About.cshtml*视图，以包含`bold`属性值。 已完成的代码所示。

    [!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutBoldOnly.cshtml?highlight=7)]

3.  运行应用。 你最喜欢的浏览器可用于检查源和验证标记。

    `[HtmlTargetElement]`上面属性只针对提供的"加粗"的属性名称的 HTML 标记。 `<bold>`元素均未修改的标记帮助程序。

4. 注释掉`[HtmlTargetElement]`属性行和它将默认为目标设定`<bold>`标记，也就是说，HTML 标记的窗体`<bold>`。 请记住，默认命名约定将匹配的类名称**加粗**到 TagHelper`<bold>`标记。

5. 运行应用并确认`<bold>`标记处理的标记帮助程序。

修饰的类具有多个`[HtmlTargetElement]`特性导致的目标的逻辑 OR。 例如，使用下面的代码，以粗体显示标记或以粗体显示的属性将匹配。

[!code-csharp[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zBoldTagHelperCopy.cs?highlight=1,2&range=5-15)]

当多个属性添加到同一个语句时，运行时将它们视为逻辑 and。 例如，在下面的代码中，HTML 元素必须命名为"bold"具有一个名为的属性"bold"(`<bold bold />`) 以匹配。

```csharp
[HtmlTargetElement("bold", Attributes = "bold")]
   ```

你还可以使用`[HtmlTargetElement]`若要更改目标元素的名称。 例如，如果你想`BoldTagHelper`到目标`<MyBold>`标记，则将使用以下属性：

```csharp
[HtmlTargetElement("MyBold")]
   ```

## <a name="passing-a-model-to-a-tag-helper"></a>将模型传递到标记帮助器

1.  添加*模型*文件夹。

2.  将以下 `WebsiteContext` 类添加到“模型”文件夹：

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Models/WebsiteContext.cs)]

3.  添加以下`WebsiteInformationTagHelper`类到*TagHelpers*文件夹。

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/WebsiteInformationTagHelper.cs)]
    
    **注意：**
    
    * 如前所述，标记帮助程序会将转换 Pascal 大小写形式 C# 类名称和属性的标记帮助程序到[降低 kebab 用例](http://wiki.c2.com/?KebabCase)。 因此，若要使用`WebsiteInformationTagHelper`在 Razor，你将编写`<website-information />`。
    
    * 显式未识别具有的目标元素`[HtmlTargetElement]`特性，因此默认的`website-information`将加载目标。 如果您应用以下属性 （请注意它不 kebab 这种情况，但与类名称匹配）：
    
    ```csharp
    [HtmlTargetElement("WebsiteInformation")]
    ```
    
    较低的 kebab 用例标记`<website-information />`将不匹配。 如果你想要`[HtmlTargetElement]`属性中，你将使用 kebab 用例，如下所示：
    
    ```csharp
    [HtmlTargetElement("Website-Information")]
    ```
    
    * 自结束的元素没有任何内容。 对于此示例中，Razor 标记将使用自结束标记，但将创建的标记帮助程序[部分](http://www.w3.org/TR/html5/sections.html#the-section-element)元素 (它不是自结束，并且你正在编写中的内容`section`元素)。 因此，你需要设置`TagMode`到`StartTagAndEndTag`写入输出。 或者，你可以注释行设置`TagMode`和写入与结束标记的标记。 （示例标记提供更高版本在本教程中）。
    
    * `$` （美元符号） 在下面的一行使用[内插字符串](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/interpolated-strings):
    
    ```cshtml
    $@"<ul><li><strong>Version:</strong> {Info.Version}</li>
    ```

4.  添加到以下标记*About.cshtml*视图。 突出显示的标记显示 web 站点的信息。
    
    [!code-html[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?highlight=1,12-)]
    
    >[!NOTE]
    > 在 Razor 标记如下所示：
    >
    >[!code-html[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?range=13-17)]
    > 
    >Razor 知道`info`属性是一个类，不是字符串，并且你想要编写 C# 代码。 应而无需编写任何非字符串标记帮助器属性`@`字符。
    
5.  运行应用，并导航到关于视图以查看 web 站点的信息。

    >[!NOTE]
    >你可以使用以下标记与结束标记，并删除在行的`TagMode.StartTagAndEndTag`标记帮助程序中：
    >
    >[!code-html[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutNotSelfClosing.cshtml?range=13-18)]

## <a name="condition-tag-helper"></a>条件标记帮助器

条件标记帮助器呈现输出时传递 true 值。

1.  添加以下`ConditionTagHelper`类到*TagHelpers*文件夹。

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/ConditionTagHelper.cs)]

2.  内容替换*Views/Home/Index.cshtml*文件替换为以下标记：

    <!-- literal_block {"xml:space": "preserve", "source": "mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Index.cshtml", "ids": [], "linenos": false, "highlight_args": {"linenostart": 1}} -->
    
    ```cshtml
    @using AuthoringTagHelpers.Models
    @model WebsiteContext
    
    @{
        ViewData["Title"] = "Home Page";
    }
    
    <div>
        <h3>Information about our website (outdated):</h3>
        <website-information info=Model />
        <div condition="Model.Approved">
            <p>
                This website has <strong surround="em"> @Model.Approved </strong> been approved yet.
                Visit www.contoso.com for more information.
            </p>
        </div>
    </div>
    ```
    
3.  替换`Index`中的方法`Home`控制器替换为以下代码：

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Controllers/HomeController.cs?range=9-18)]

4.  运行应用程序，并浏览到主页。 在条件中的标记`div`将不会呈现。 将查询的字符串追加`?approved=true`到的 URL (例如， `http://localhost:1235/Home/Index?approved=true`)。 `approved`设置为 true，条件将显示标记。

>[!NOTE]
>使用[nameof](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/nameof)运算符来指定为目标，而不是指定的字符串，就像处理以粗体显示标记帮助器属性：
>
>[!code-csharp[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zConditionTagHelperCopy.cs?highlight=1,2,5&range=5-18)]
>
>[Nameof](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/nameof)运算符将保护该代码应它曾重构 (我们可能想要将名称更改为`RedCondition`)。

### <a name="avoiding-tag-helper-conflicts"></a>避免标记帮助器冲突

在本部分中，你编写的成对的自动链接标记帮助程序。 第一个将包含到 HTML 定位点标记包含相同的 URL （这将会产生一个链接到的 URL） 从 HTTP URL 的标记。 第二个将执行相同操作的 URL 开头 WWW。

由于这些两个帮助器密切相关，并且你可能在将来重构，我们将使它们保持在相同的文件。

1.  添加以下`AutoLinkerHttpTagHelper`类到*TagHelpers*文件夹。

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=7-19)]

    >[!NOTE]
    >`AutoLinkerHttpTagHelper`类目标`p`元素，并使用[正则表达式](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference)创建定位点。

2.  将以下标记添加到末尾*Views/Home/Contact.cshtml*文件：

    [!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=19)]

3.  运行应用程序并验证标记帮助程序正常呈现定位点。

4.  更新`AutoLinker`类，以包含`AutoLinkerWwwTagHelper`这会将 www 文本转换为还包含原始 www 文本的定位点标记。 在下面突出显示更新的代码：

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?highlight=15-34&range=7-34)]

5.  运行应用。 请注意 www 文本呈现为链接，但不是 HTTP 文本。 如果将中断点放在这两个类中，你可以看到首先运行 HTTP 标记帮助器类。 问题是，标记帮助器输出缓存的并且当 WWW 标记帮助程序运行时，它将覆盖从 HTTP 标记帮助程序缓存的输出。 在教程后面部分中，我们将了解如何控制标记帮助程序中运行的顺序。 我们将替换为以下修复代码：

    [!code-csharp[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10,21,22,26&range=8-37)]

    >[!NOTE]
    >中的自动链接标记帮助器第一版，你的内容替换为以下代码目标：
    >
    >[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=12)]
    >
    >也就是说，调用`GetChildContentAsync`使用`TagHelperOutput`传入`ProcessAsync`方法。 如提到的那样以前，因为缓存输出，最后一个标记帮助器以运行 wins。 修复该问题替换为以下代码：
    >
    >[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?range=34-35)]
    >
    >上面的代码检查已修改内容，但如果它具有，它获取该内容从输出缓冲区。

6.  运行应用程序并验证两个链接按预期方式工作。 虽然它可能显示我们自动链接器标记帮助程序是正确和完整，但其细微问题。 如果首先运行 WWW 标记帮助器，www 链接不会正确。 请更新代码，通过添加`Order`重载以控制标记运行中的顺序。 `Order`属性确定相对于其他面向同一元素的标记帮助程序的执行顺序。 默认顺序值为 0，第一次执行具有较低的值的实例。

    [!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?highlight=5,6,7,8&range=8-15)]
    
    上面的代码可以保证 HTTP 标记帮助程序运行之前 WWW 标记帮助器。 更改`Order`到`MaxValue`并验证是否为 WWW 标记生成的标记不正确。

## <a name="inspecting-and-retrieving-child-content"></a>检查和检索子内容

标记帮助程序提供多个属性以检索内容。

-  结果`GetChildContentAsync`可以追加到`output.Content`。
-  你可以检查的结果`GetChildContentAsync`与`GetContent`。
-  如果你修改`output.Content`，不会执行或呈现除非调用 TagHelper 正文`GetChildContentAsync`如我们自动链接器示例所示：

[!code-csharp[Main](../../views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10&range=8-21)]

-  多次调用`GetChildContentAsync`将返回相同的值而不会重新执行`TagHelper`正文除非你传入一个 false 的参数，指示不使用缓存的结果。
