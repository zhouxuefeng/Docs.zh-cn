---
title: "全球化和 ASP.NET Core 的本地化"
author: rick-anderson
description: "了解如何 ASP.NET Core 提供服务和中间件将内容本地化为不同的语言和区域性。"
keywords: "ASP.NET 核心，本地化、 区域性、 语言、 资源文件、 全球化、 国际化、 区域设置"
ms.author: riande
manager: wpickett
ms.date: 01/14/2017
ms.topic: article
ms.assetid: 7f275a09-f118-41c9-88d1-8de52d6a5aa1
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/localization
ms.openlocfilehash: 85a192bf0b2eb245ecdaaa8ffa1c8dd2f43b45b0
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2017
---
# <a name="globalization-and-localization-in-aspnet-core"></a>全球化和 ASP.NET Core 的本地化

通过[Rick Anderson](https://twitter.com/RickAndMSFT)， [Damien Bowden](https://twitter.com/damien_bod)，[邓 Calixto](https://twitter.com/bartmax)， [Nadeem Afana](https://twitter.com/NadeemAfana)，和[Hisham Bin Ateya](https://twitter.com/hishambinateya)

使用 ASP.NET Core 创建多语言的网站将允许你的站点来访问更多的人。 ASP.NET Core 提供服务和中间件将本地化为不同的语言和区域性。

国际化涉及[全球化](https://docs.microsoft.com/dotnet/api/system.globalization)和[本地化](https://docs.microsoft.com/dotnet/standard/globalization-localization/localization)。 全球化是设计支持不同的区域性的应用程序的过程。 全球化添加输入、 显示和一组定义与特定的地理区域相关的语言脚本的输出的支持。

本地化是调整的全球化的应用，你已经处理进行了本地化分析，为特定的区域性/区域设置的过程。  有关详细信息请参阅**全球化和本地化条款**接近本文档的结尾。

应用程序本地化涉及以下过程：

1. 使可本地化的应用程序的内容

2. 提供的语言和你支持的区域性的本地化的资源

3. 实现策略来选择用于每个请求的语言/区域性

## <a name="make-the-apps-content-localizable"></a>使可本地化的应用程序的内容

ASP.NET 核心中引入`IStringLocalizer`和`IStringLocalizer<T>`已设计为可提高工作效率，开发本地化应用程序时。 `IStringLocalizer`使用[ResourceManager](https://docs.microsoft.com/dotnet/api/system.resources.resourcemanager)和[ResourceReader](https://docs.microsoft.com/dotnet/api/system.resources.resourcereader)在运行时提供区域性特定资源。 简单接口具有一个索引器和`IEnumerable`用于返回本地化的字符串。 `IStringLocalizer`不要求你在资源文件中存储的默认语言字符串。 你可以开发针对本地化应用程序并不需要在开发的早期创建资源文件。 下面的代码演示如何包装本地化的字符串"有关 Title"。

[!code-csharp[Main](localization/sample/Localization/Controllers/AboutController.cs)]

在上面的代码，`IStringLocalizer<T>`实现来自[依赖关系注入](dependency-injection.md)。 如果找不到"有关标题"的本地化的值，则索引器返回，即"有关标题"的字符串。 可以在应用程序保留默认语言字符串并将它们包装在本地化人员，以便您可以集中精力开发应用。 使用默认语言开发你的应用程序，并准备好进行本地化步骤无需首先创建默认资源文件。 或者，你可以使用传统方法，并提供键以检索的默认语言字符串。 许多开发人员的新工作流不具有一种默认语言*.resx*文件和包装的字符串文本可以减少本地化应用程序的开销。 其他开发人员将首选的传统工作流，因为它可以使更轻松地使用较长的字符串文本和更加轻松地更新本地化的字符串。

使用`IHtmlLocalizer<T>`包含 HTML 资源的实现。 `IHtmlLocalizer`HTML 编码格式中的资源字符串，但不是资源字符串的自变量。 在此示例中突出显示下面的值`name`参数是 HTML 编码。

[!code-csharp[Main](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

注意： 你通常想要仅本地化文本和不 HTML。

在最低级别，你可以获取`IStringLocalizerFactory`外[依赖关系注入](dependency-injection.md):

[!code-csharp[Main](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

上面的代码演示每两个工厂创建方法。

可以按控制器，区域中，分区本地化的字符串，也可以具有一个容器。 在示例应用中，dummy 类名为`SharedResource`用于共享资源。

[!code-csharp[Main](localization/sample/Localization/Resources/SharedResource.cs)]

某些开发人员使用`Startup`类，以包含全局或共享的字符串。  在下面，示例`InfoController`和`SharedResource`本地化人员使用：

[!code-csharp[Main](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>视图本地化

`IViewLocalizer`服务提供的本地化的字符串[视图](https://docs.microsoft.com/aspnet/core)。 `ViewLocalizer`类实现此接口，并找到视图文件路径中的资源的位置。 下面的代码演示如何使用的默认实现`IViewLocalizer`:

[!code-HTML[Main](localization/sample/Localization/Views/Home/About.cshtml)]

默认实现`IViewLocalizer`查找基于视图的文件名称的资源文件。 没有要使用的全局共享的资源文件的选项。 `ViewLocalizer`实现使用在本地化人员`IHtmlLocalizer`，因此 Razor 不 HTML 编码的本地化的字符串。 你可以参数化资源字符串和`IViewLocalizer`将 HTML 编码的参数，但不是资源字符串。 请考虑以下 Razor 标记：

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

法语资源文件可以包含以下信息：

| 键 | 值 |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b> ` |

呈现的视图可能包含的资源文件中的 HTML 标记。

注意：
- 视图本地化需要"Localization.AspNetCore.TagHelpers"NuGet 包。
- 你通常想要仅本地化文本和不 HTML。

若要使用在视图中的共享的资源文件，将注入`IHtmlLocalizer<T>`:

[!code-HTML[Main](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>DataAnnotations 本地化

DataAnnotations 错误消息已本地化与`IStringLocalizer<T>`。 使用选项`ResourcesPath = "Resources"`中的错误消息`RegisterViewModel`可以存储在以下路径之一：

* Resources/ViewModels.Account.RegisterViewModel.fr.resx
* Resources/ViewModels/Account/RegisterViewModel.fr.resx

[!code-csharp[Main](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

在 ASP.NET 核心 MVC 1.1.0 和更高版本、 非验证属性已经进行了本地化。 ASP.NET 核心 MVC 1.0 未**不**查找非验证特性的本地化字符串。

<a name=one-resource-string-multiple-classes></a>
### <a name="using-one-resource-string-for-multiple-classes"></a>为多个类使用一个的资源字符串

下面的代码演示如何使用与多个类别的验证特性的一个资源字符串：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

在上面的代码，`SharedResource`是对应于 resx 类验证消息的存储位置。 使用此方法时，将仅使用 DataAnnotations `SharedResource`，而不是每个类的资源。 

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>提供的语言和你支持的区域性的本地化的资源  

### <a name="supportedcultures-and-supporteduicultures"></a>SupportedCultures 和 SupportedUICultures

ASP.NET 核心，可指定两个区域性值，`SupportedCultures`和`SupportedUICultures`。 [CultureInfo](https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo)对象`SupportedCultures`确定得到的依赖于区域性的函数，如日期、 时间、 数字和货币格式的结果。 `SupportedCultures`此外确定文本、 大小写约定和字符串比较的排序顺序。 请参阅[CultureInfo.CurrentCulture](https://docs.microsoft.com/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture)有关服务器如何获取区域性的详细信息。 `SupportedUICultures`确定就会转换字符串 (从*.resx*文件) 按查找[ResourceManager](https://docs.microsoft.com/dotnet/api/system.resources.resourcemanager)。 `ResourceManager`只需查找区域性特定字符串由决定`CurrentUICulture`。 .NET 中的每个线程都具有`CurrentCulture`和`CurrentUICulture`对象。 ASP.NET 核心呈现依赖于区域性的函数时检查这些值。 例如，如果当前线程的区域性设置为"EN-US"（英语，美国），`DateTime.Now.ToLongDateString()`显示"星期四，2016 年 2 月 18，"，但如果`CurrentCulture`设置为"ES-ES"（西班牙语、 西班牙） 则输出将为"jueves，18 de febrero de 2016"。

## <a name="working-with-resource-files"></a>使用资源文件

资源文件是用于将可本地化的字符串与代码分离的有用机制。 非默认语言已翻译的字符串将被隔离*.resx*资源文件。 例如，你可能想要创建名为西班牙语资源文件*Welcome.es.resx*包含转换字符串。 "es"是西班牙语的语言代码。 在 Visual Studio 中创建此资源文件：

1. 在**解决方案资源管理器**，右键单击该文件夹将包含资源文件 >**添加** > **新项**。

    ![嵌套的上下文菜单： 在解决方案资源管理器，上下文菜单可供资源。 第二个上下文菜单可供添加显示突出显示的新项命令。](localization/_static/newi.png)

2. 在**搜索已安装的模板**框中，输入"资源"并将该文件。

    ![“添加新项”对话框](localization/_static/res.png)

3. 在输入密钥的值 （本机字符串）**名称**列和中的已翻译的字符串**值**列。

    ![带有单词名称列中的 Hello 和值列中的单词 Hola (西班牙语 Hello) Welcome.es.resx 文件 （西班牙语的欢迎使用资源文件）](localization/_static/hola.png)

    Visual Studio 将显示*Welcome.es.resx*文件。

    ![显示欢迎使用西班牙语 (es) 资源文件的解决方案资源管理器](localization/_static/se.png)

<a name="error"></a>

如果使用 Visual Studio 2017 预览版本 15.3，你将在资源编辑器中获得的错误指示器。 删除*ResXFileCodeGenerator*值从*自定义工具*属性网格，若要避免此错误消息：

![Resx 编辑器](localization/_static/err.png)

或者，你可以忽略此错误。 我们希望解决此问题在下一个版本。

## <a name="resource-file-naming"></a>资源文件命名

资源在命名的程序集名称减其类的完整类型名称。 例如，其主要的程序集的项目中的法语资源`LocalizationWebsite.Web.dll`类`LocalizationWebsite.Web.Startup`将被命名为*Startup.fr.resx*。 类资源`LocalizationWebsite.Web.Controllers.HomeController`将被命名为*Controllers.HomeController.fr.resx*。 如果您的目标的类的命名空间不与程序集名称相同你将需要的完整类型名称。 例如，在此示例中项目的类型的资源`ExtraNamespace.Tools`将被命名为*ExtraNamespace.Tools.fr.resx*。

在示例项目中，`ConfigureServices`方法设置`ResourcesPath`对"资源"，因此主控制器的法语资源文件的项目相对路径是*Resources/Controllers.HomeController.fr.resx*。 或者，可以使用文件夹来组织资源文件。 对于主控制器，该路径将为*Resources/Controllers/HomeController.fr.resx*。 如果不使用`ResourcesPath`选项， *.resx*文件会在项目的基目录中。 资源文件`HomeController`将被命名为*Controllers.HomeController.fr.resx*。 使用圆点或路径的命名约定的选择取决于你想要组织资源文件。

| 资源名称 | 圆点或路径命名 |
| ------------   | ------------- |
| Resources/Controllers.HomeController.fr.resx | 点  |
| Resources/Controllers/HomeController.fr.resx  | 路径 |
|    |     |

使用的资源文件`@inject IViewLocalizer`在 Razor 视图中遵循类似的模式。 可以使用点命名或路径命名命名为视图的资源文件。 Razor 视图资源文件模拟其关联的视图文件的路径。 假设我们设置`ResourcesPath`对"资源"的法语资源文件与*Views/Home/About.cshtml*视图可能是下列操作之一：

* Resources/Views/Home/About.fr.resx

* Resources/Views.Home.About.fr.resx

如果不使用`ResourcesPath`选项， *.resx*文件视图将位于视图所在的文件夹。

如果你删除"法国"区域性指示符，并具有设置为法语 （通过 cookie 或其他机制） 的区域性，请将读取默认资源文件，并已本地化字符串。 资源管理器指定的默认资源或回退资源，在执行任何操作符合您请求正在提供 *.resx 文件而不进行区域性指示符的区域性。 如果你想要仅返回密钥，如果缺少区域性所请求资源必须具有默认资源文件。

### <a name="generating-resource-files-with-visual-studio"></a>使用 Visual Studio 的生成资源文件

如果你在而无需在文件名中区域性情况下将在 Visual Studio 中创建资源文件 (例如， *Welcome.resx*)，Visual Studio 将创建一个 C# 类的属性为每个字符串。 这通常是你不想使用 ASP.NET Core;通常不需要默认*.resx*资源文件 (一个*.resx*文件而无需区域性名称)。 我们建议你创建*.resx*使用区域性名称的文件 (例如*Welcome.fr.resx*)。 当你创建*.resx*使用区域性名称，Visual Studio 的文件将不会生成的类文件。 我们预计许多开发人员将**不**创建默认语言资源文件。

### <a name="adding-other-cultures"></a>添加其他区域性

每个语言和区域性的组合 （以外的默认语言） 需要唯一的资源文件。 通过创建新的资源文件的 ISO 语言代码中的文件名称的一部分创建不同的区域性和区域设置的资源文件 (例如， **en-我们**， **fr ca**，和**en gb**)。 这些 ISO 代码位于之间的文件名称和*.resx*文件扩展名，如*Welcome.es MX.resx* （西班牙语/墨西哥）。 若要指定的区域性的、 非特定语言，删除国家/地区代码 (`MX`在前面的示例)。 非特定区域性的西班牙语资源文件的名称*Welcome.es.resx*。

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>实现策略来选择用于每个请求的语言/区域性  

### <a name="configuring-localization"></a>配置本地化

在配置本地化`ConfigureServices`方法：

[!code-csharp[Main](localization/sample/Localization/Program.cs?name=snippet1)]

* `AddLocalization`将本地化服务添加到服务容器。 上面的代码还将设置"资源"的资源路径。

* `AddViewLocalization`添加了对本地化的查看文件支持。 在此示例视图中本地化基于视图文件后缀。 例如"fr"中的*Index.fr.cshtml*文件。

* `AddDataAnnotationsLocalization`增加了支持对本地化的`DataAnnotations`验证消息通过`IStringLocalizer`抽象。

### <a name="localization-middleware"></a>本地化中间件

在请求的当前区域性设置本地化[中间件](middleware.md)。 在中启用本地化中间件`Configure`方法*Program.cs*文件。 请注意，必须在其中可以检查请求区域性的任何中间件之前配置本地化中间件 (例如， `app.UseMvcWithDefaultRoute()`)。

[!code-csharp[Main](localization/sample/Localization/Program.cs?name=snippet2)]

`UseRequestLocalization`初始化`RequestLocalizationOptions`对象。 对每个请求列表的`RequestCultureProvider`中`RequestLocalizationOptions`枚举和使用的第一个提供程序已成功确定请求区域性。 默认的提供程序来自`RequestLocalizationOptions`类：

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

默认值列表将从最具体到最不具体转。 在本文的后面，我们将了解如何更改的顺序和甚至添加自定义区域性提供程序。 如果没有一个提供程序可以确定请求区域性，`DefaultRequestCulture`使用。

### <a name="querystringrequestcultureprovider"></a>QueryStringRequestCultureProvider

某些应用程序，将使用查询字符串来设置[区域性和 UI 区域性](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)。 对于使用 cookie 或接受语言标头方法的应用程序，向 URL 添加查询字符串可用于调试和测试代码。 默认情况下，`QueryStringRequestCultureProvider`注册为第一个本地化提供程序在`RequestCultureProvider`列表。 将查询字符串参数`culture`和`ui-culture`。 下面的示例设置西班牙语/墨西哥的特定区域性 （语言和区域）：

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

如果您仅以两种状态之一传递 (`culture`或`ui-culture`)，查询字符串提供程序将使用你传入这两个值。 例如，设置仅的区域性将同时设置`Culture`和`UICulture`:

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a>CookieRequestCultureProvider

通常，生产应用将提供一种机制来设置使用 ASP.NET Core 区域性 cookie 的区域性。 使用`MakeCookieValue`方法来创建一个 cookie。

`CookieRequestCultureProvider` `DefaultCookieName`返回用来跟踪用户的默认 cookie 名称的首选区域性信息。 默认的 cookie 名称是"。AspNetCore.Culture"。

Cookie 格式`c=%LANGCODE%|uic=%LANGCODE%`，其中`c`是`Culture`和`uic`是`UICulture`，例如：

    c=en-UK|uic=en-US

如果您仅指定其中一个区域性信息和 UI 区域性，则指定的区域性将使用区域性信息和 UI 区域性。

### <a name="the-accept-language-http-header"></a>Accept-language HTTP 标头

[接受语言标头](https://www.w3.org/International/questions/qa-accept-lang-locales)是可在大多数浏览器设置，最初用于指定用户的语言。 此设置指示浏览器已设置为发送或从基础操作系统已继承。 浏览器请求的 Accept 语言 HTTP 标头不是一种可靠的方法来检测用户的首选的语言 (请参阅[在浏览器中设置语言首选项](https://www.w3.org/International/questions/qa-lang-priorities.en.php))。 生产应用程序应包括用户可以自定义区域性他们选择一种方法。

### <a name="setting-the-accept-language-http-header-in-ie"></a>在 IE 中设置 Accept 语言 HTTP 标头

1. 从齿轮图标，点击**Internet 选项**。

2. 点击**语言**。

    ![Internet 选项](localization/_static/lang.png)

3. 点击**设置语言首选项**。

4. 点击**添加一种语言**。

5. 添加的语言。

6. 点击语言，然后点击**移**。

### <a name="using-a-custom-provider"></a>使用自定义提供程序

假设你想要让客户在数据库中存储其语言和区域性。 你可以编写一个提供程序来查找用户的这些值。 下面的代码演示如何添加自定义提供程序：

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

使用`RequestLocalizationOptions`添加或删除本地化提供程序。

### <a name="setting-the-culture-programmatically"></a>以编程方式设置区域性

此示例**Localization.StarterWeb**项目上[GitHub](https://github.com/aspnet/entropy)包含 UI，以设置`Culture`。 *Views/Shared/_SelectLanguagePartial.cshtml*文件可以从支持的区域性的列表中选择区域性：

[!code-HTML[Main](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

*Views/Shared/_SelectLanguagePartial.cshtml*文件添加到`footer`的布局文件，使它将可供所有视图的部分：

[!code-HTML[Main](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

`SetLanguage`方法会设置区域性 cookie。

[!code-csharp[Main](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

您不能插入*_SelectLanguagePartial.cshtml*为此项目的代码示例。 **Localization.StarterWeb**项目上[GitHub](https://github.com/aspnet/entropy)具有代码流`RequestLocalizationOptions`到 Razor 部分通过[依赖关系注入](dependency-injection.md)容器。

## <a name="globalization-and-localization-terms"></a>全球化和本地化条款

本地化你的应用程序的过程还要求通常在现代软件开发中使用的字符集相关的一个基本的了解并与之关联的问题的了解。 尽管所有计算机将文本都存储为数字 （代码），不同的系统都存储使用不同数量的相同文本。 在本地化过程是指转换特定区域性或区域设置的应用程序用户界面 (UI)。

[可本地化性](https://docs.microsoft.com/dotnet/standard/globalization-localization/localizability-review)是中间的过程，用于验证的全球化的应用是否准备好进行本地化。

[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt)设置格式的区域性名称为"<languagecode2>-< country/regioncode2 >"，其中<languagecode2>是语言代码，< country/regioncode2 > 是子区域性代码。 例如，`es-CL`为西班牙语 （智利）`en-US`为英语 （美国） 和`en-AU`以表示英语 （澳大利亚）。 [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt)是两个字母小写区域性代码与语言相关 ISO 639 和两个字母大写子区域性代码关联的国家或地区与 ISO 3166 的组合。  请参阅[语言区域性名称](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx)。

国际化常缩写为"I18N"。 缩写采用第一个和最后一个字母并它们，因此 18 之间号的数量代表数量的第一个之间的字母"I"和最后一个"N"。 这同样适用于全球化 (G11N) 和本地化 (L10N)。

条款：

* 全球化 (G11N): 使应用程序支持不同的语言和区域的过程。
* 本地化 (L10N): 自定义的过程适用于给定的语言和区域的应用。
* 国际化 (I18N): 描述全球化和本地化。
* 区域性： 它是一种语言和区域，（可选）。
* 非特定区域性： 一个具有指定的语言中，但不是区域的区域性。 (例如"en"，"es")
* 特定区域性： 一个具有指定的语言和区域的区域性。 （有关示例"EN-US"、"EN-GB"、"es CL"）
* 区域设置： 区域设置是相同的区域性。

## <a name="additional-resources"></a>其他资源

* [Localization.StarterWeb 项目](https://github.com/aspnet/entropy)文章中使用。
* [Visual Studio 中的资源文件](https://docs.microsoft.com/cpp/windows/resource-files-visual-studio)
* [.Resx 文件中的资源](https://docs.microsoft.com/dotnet/framework/resources/working-with-resx-files-programmatically)