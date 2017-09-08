---
title: "全球化和本地化"
author: rick-anderson
description: 
keywords: "ASP.NET 核心"
ms.author: riande
manager: wpickett
ms.date: 01/14/2017
ms.topic: article
ms.assetid: 7f275a09-f118-41c9-88d1-8de52d6a5aa1
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/localization
ms.openlocfilehash: 70f11cc9de8e885745e7d08cb98ac68e3cc8ef95
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="globalization-and-localization"></a><span data-ttu-id="ac13f-103">全球化和本地化</span><span class="sxs-lookup"><span data-stu-id="ac13f-103">Globalization and localization</span></span>

<span data-ttu-id="ac13f-104">通过[Rick Anderson](https://twitter.com/RickAndMSFT)， [Damien Bowden](https://twitter.com/damien_bod)，[邓 Calixto](https://twitter.com/bartmax)， [Nadeem Afana](https://twitter.com/NadeemAfana)，和[Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="ac13f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://twitter.com/NadeemAfana), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="ac13f-105">使用 ASP.NET Core 创建多语言的网站将允许你的站点来访问更多的人。</span><span class="sxs-lookup"><span data-stu-id="ac13f-105">Creating a multilingual website with ASP.NET Core will allow your site to reach a wider audience.</span></span> <span data-ttu-id="ac13f-106">ASP.NET Core 提供服务和中间件将本地化为不同的语言和区域性。</span><span class="sxs-lookup"><span data-stu-id="ac13f-106">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="ac13f-107">国际化涉及[全球化](https://msdn.microsoft.com/library/aa292081(v=vs.71).aspx)和[本地化](https://msdn.microsoft.com/library/aa292137(v=vs.71).aspx)。</span><span class="sxs-lookup"><span data-stu-id="ac13f-107">Internationalization involves [Globalization](https://msdn.microsoft.com/library/aa292081(v=vs.71).aspx) and [Localization](https://msdn.microsoft.com/library/aa292137(v=vs.71).aspx).</span></span> <span data-ttu-id="ac13f-108">全球化是设计支持不同的区域性的应用程序的过程。</span><span class="sxs-lookup"><span data-stu-id="ac13f-108">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="ac13f-109">全球化添加输入、 显示和一组定义与特定的地理区域相关的语言脚本的输出的支持。</span><span class="sxs-lookup"><span data-stu-id="ac13f-109">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="ac13f-110">本地化是调整的全球化的应用，你已经处理进行了本地化分析，为特定的区域性/区域设置的过程。</span><span class="sxs-lookup"><span data-stu-id="ac13f-110">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span>  <span data-ttu-id="ac13f-111">有关详细信息请参阅**全球化和本地化条款**接近本文档的结尾。</span><span class="sxs-lookup"><span data-stu-id="ac13f-111">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="ac13f-112">应用程序本地化涉及以下过程：</span><span class="sxs-lookup"><span data-stu-id="ac13f-112">App localization involves the following:</span></span>

1. <span data-ttu-id="ac13f-113">使可本地化的应用程序的内容</span><span class="sxs-lookup"><span data-stu-id="ac13f-113">Make the app's content localizable</span></span>

2. <span data-ttu-id="ac13f-114">提供的语言和你支持的区域性的本地化的资源</span><span class="sxs-lookup"><span data-stu-id="ac13f-114">Provide localized resources for the languages and cultures you support</span></span>

3. <span data-ttu-id="ac13f-115">实现策略来选择用于每个请求的语言/区域性</span><span class="sxs-lookup"><span data-stu-id="ac13f-115">Implement a strategy to select the language/culture for each request</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="ac13f-116">使可本地化的应用程序的内容</span><span class="sxs-lookup"><span data-stu-id="ac13f-116">Make the app's content localizable</span></span>

<span data-ttu-id="ac13f-117">ASP.NET 核心中引入`IStringLocalizer`和`IStringLocalizer<T>`已设计为可提高工作效率，开发本地化应用程序时。</span><span class="sxs-lookup"><span data-stu-id="ac13f-117">Introduced in ASP.NET Core, `IStringLocalizer` and `IStringLocalizer<T>` were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="ac13f-118">`IStringLocalizer`使用[ResourceManager](https://msdn.microsoft.com/library/system.resources.resourcemanager(v=vs.110).aspx)和[ResourceReader](https://msdn.microsoft.com/library/system.resources.resourcereader(v=vs.110).aspx)在运行时提供区域性特定资源。</span><span class="sxs-lookup"><span data-stu-id="ac13f-118">`IStringLocalizer` uses the [ResourceManager](https://msdn.microsoft.com/library/system.resources.resourcemanager(v=vs.110).aspx) and [ResourceReader](https://msdn.microsoft.com/library/system.resources.resourcereader(v=vs.110).aspx) to provide culture-specific resources at run time.</span></span> <span data-ttu-id="ac13f-119">简单接口具有一个索引器和`IEnumerable`用于返回本地化的字符串。</span><span class="sxs-lookup"><span data-stu-id="ac13f-119">The simple interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="ac13f-120">`IStringLocalizer`不要求你在资源文件中存储的默认语言字符串。</span><span class="sxs-lookup"><span data-stu-id="ac13f-120">`IStringLocalizer` doesn't require you to store the default language strings in a resource file.</span></span> <span data-ttu-id="ac13f-121">你可以开发针对本地化应用程序并不需要在开发的早期创建资源文件。</span><span class="sxs-lookup"><span data-stu-id="ac13f-121">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="ac13f-122">下面的代码演示如何包装本地化的字符串"有关 Title"。</span><span class="sxs-lookup"><span data-stu-id="ac13f-122">The code below shows how to wrap the string "About Title" for localization.</span></span>

<span data-ttu-id="ac13f-123">[!code-csharp[Main](localization/sample/Controllers/AboutController.cs)]</span><span class="sxs-lookup"><span data-stu-id="ac13f-123">[!code-csharp[Main](localization/sample/Controllers/AboutController.cs)]</span></span>

<span data-ttu-id="ac13f-124">在上面的代码，`IStringLocalizer<T>`实现来自[依赖关系注入](dependency-injection.md)。</span><span class="sxs-lookup"><span data-stu-id="ac13f-124">In the code above, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="ac13f-125">如果找不到"有关标题"的本地化的值，则索引器返回，即"有关标题"的字符串。</span><span class="sxs-lookup"><span data-stu-id="ac13f-125">If the localized value of "About Title" is not found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="ac13f-126">可以在应用程序保留默认语言字符串并将它们包装在本地化人员，以便您可以集中精力开发应用。</span><span class="sxs-lookup"><span data-stu-id="ac13f-126">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="ac13f-127">使用默认语言开发你的应用程序，并准备好进行本地化步骤无需首先创建默认资源文件。</span><span class="sxs-lookup"><span data-stu-id="ac13f-127">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="ac13f-128">或者，你可以使用传统方法，并提供键以检索的默认语言字符串。</span><span class="sxs-lookup"><span data-stu-id="ac13f-128">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="ac13f-129">许多开发人员的新工作流不具有一种默认语言*.resx*文件和包装的字符串文本可以减少本地化应用程序的开销。</span><span class="sxs-lookup"><span data-stu-id="ac13f-129">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="ac13f-130">其他开发人员将首选的传统工作流，因为它可以使更轻松地使用较长的字符串文本和更加轻松地更新本地化的字符串。</span><span class="sxs-lookup"><span data-stu-id="ac13f-130">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="ac13f-131">使用`IHtmlLocalizer<T>`包含 HTML 资源的实现。</span><span class="sxs-lookup"><span data-stu-id="ac13f-131">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="ac13f-132">`IHtmlLocalizer`HTML 编码格式中的资源字符串，但不是资源字符串的自变量。</span><span class="sxs-lookup"><span data-stu-id="ac13f-132">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but not the resource string.</span></span> <span data-ttu-id="ac13f-133">在此示例中突出显示下面的值`name`参数是 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="ac13f-133">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

<span data-ttu-id="ac13f-134">[!code-csharp[Main](../fundamentals/localization/sample/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]</span><span class="sxs-lookup"><span data-stu-id="ac13f-134">[!code-csharp[Main](../fundamentals/localization/sample/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]</span></span>

<span data-ttu-id="ac13f-135">注意： 你通常想要仅本地化文本和不 HTML。</span><span class="sxs-lookup"><span data-stu-id="ac13f-135">Note: You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="ac13f-136">在最低级别，你可以获取`IStringLocalizerFactory`外[依赖关系注入](dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="ac13f-136">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

<span data-ttu-id="ac13f-137">[!code-csharp[Main](localization/sample/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]</span><span class="sxs-lookup"><span data-stu-id="ac13f-137">[!code-csharp[Main](localization/sample/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]</span></span>

<span data-ttu-id="ac13f-138">上面的代码演示每两个工厂创建方法。</span><span class="sxs-lookup"><span data-stu-id="ac13f-138">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="ac13f-139">可以按控制器，区域中，分区本地化的字符串，也可以具有一个容器。</span><span class="sxs-lookup"><span data-stu-id="ac13f-139">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="ac13f-140">在示例应用中，dummy 类名为`SharedResource`用于共享资源。</span><span class="sxs-lookup"><span data-stu-id="ac13f-140">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

<span data-ttu-id="ac13f-141">[!code-csharp[Main](localization/sample/Resources/SharedResource.cs)]</span><span class="sxs-lookup"><span data-stu-id="ac13f-141">[!code-csharp[Main](localization/sample/Resources/SharedResource.cs)]</span></span>

<span data-ttu-id="ac13f-142">某些开发人员使用`Startup`类，以包含全局或共享的字符串。</span><span class="sxs-lookup"><span data-stu-id="ac13f-142">Some developers use the `Startup` class to contain global or shared strings.</span></span>  <span data-ttu-id="ac13f-143">在下面，示例`InfoController`和`SharedResource`本地化人员使用：</span><span class="sxs-lookup"><span data-stu-id="ac13f-143">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

<span data-ttu-id="ac13f-144">[!code-csharp[Main](localization/sample/Controllers/InfoController.cs?range=9-26)]</span><span class="sxs-lookup"><span data-stu-id="ac13f-144">[!code-csharp[Main](localization/sample/Controllers/InfoController.cs?range=9-26)]</span></span>

## <a name="view-localization"></a><span data-ttu-id="ac13f-145">视图本地化</span><span class="sxs-lookup"><span data-stu-id="ac13f-145">View localization</span></span>

<span data-ttu-id="ac13f-146">`IViewLocalizer`服务提供的本地化的字符串[视图](http://docs.asp.net/projects/mvc/en/latest/views/index.html)。</span><span class="sxs-lookup"><span data-stu-id="ac13f-146">The `IViewLocalizer` service provides localized strings for a [view](http://docs.asp.net/projects/mvc/en/latest/views/index.html).</span></span> <span data-ttu-id="ac13f-147">`ViewLocalizer`类实现此接口，并找到视图文件路径中的资源的位置。</span><span class="sxs-lookup"><span data-stu-id="ac13f-147">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="ac13f-148">下面的代码演示如何使用的默认实现`IViewLocalizer`:</span><span class="sxs-lookup"><span data-stu-id="ac13f-148">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

<span data-ttu-id="ac13f-149">[!code-HTML[Main](localization/sample/Views/Home/About.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="ac13f-149">[!code-HTML[Main](localization/sample/Views/Home/About.cshtml)]</span></span>

<span data-ttu-id="ac13f-150">默认实现`IViewLocalizer`查找基于视图的文件名称的资源文件。</span><span class="sxs-lookup"><span data-stu-id="ac13f-150">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="ac13f-151">没有要使用的全局共享的资源文件的选项。</span><span class="sxs-lookup"><span data-stu-id="ac13f-151">There is no option to use a global shared resource file.</span></span> <span data-ttu-id="ac13f-152">`ViewLocalizer`实现使用在本地化人员`IHtmlLocalizer`，因此 Razor 不 HTML 编码的本地化的字符串。</span><span class="sxs-lookup"><span data-stu-id="ac13f-152">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="ac13f-153">你可以参数化资源字符串和`IViewLocalizer`将 HTML 编码的参数，但不是资源字符串。</span><span class="sxs-lookup"><span data-stu-id="ac13f-153">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="ac13f-154">请考虑以下 Razor 标记：</span><span class="sxs-lookup"><span data-stu-id="ac13f-154">Consider the following Razor markup:</span></span>

```HTML
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
   ```

<span data-ttu-id="ac13f-155">法语资源文件可以包含以下信息：</span><span class="sxs-lookup"><span data-stu-id="ac13f-155">A French resource file could contain the following:</span></span>

| <span data-ttu-id="ac13f-156">键</span><span class="sxs-lookup"><span data-stu-id="ac13f-156">Key</span></span> | <span data-ttu-id="ac13f-157">值</span><span class="sxs-lookup"><span data-stu-id="ac13f-157">Value</span></span> |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b> ` |

<span data-ttu-id="ac13f-158">呈现的视图可能包含的资源文件中的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="ac13f-158">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="ac13f-159">注意：</span><span class="sxs-lookup"><span data-stu-id="ac13f-159">Notes:</span></span>
- <span data-ttu-id="ac13f-160">视图本地化需要"Localization.AspNetCore.TagHelpers"NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="ac13f-160">View localization requires the "Localization.AspNetCore.TagHelpers" NuGet package.</span></span>
- <span data-ttu-id="ac13f-161">你通常想要仅本地化文本和不 HTML。</span><span class="sxs-lookup"><span data-stu-id="ac13f-161">You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="ac13f-162">若要使用在视图中的共享的资源文件，将注入`IHtmlLocalizer<T>`:</span><span class="sxs-lookup"><span data-stu-id="ac13f-162">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

<span data-ttu-id="ac13f-163">[!code-HTML[Main](../fundamentals/localization/sample/Views/Test/About.cshtml?highlight=5,12)]</span><span class="sxs-lookup"><span data-stu-id="ac13f-163">[!code-HTML[Main](../fundamentals/localization/sample/Views/Test/About.cshtml?highlight=5,12)]</span></span>

## <a name="dataannotations-localization"></a><span data-ttu-id="ac13f-164">DataAnnotations 本地化</span><span class="sxs-lookup"><span data-stu-id="ac13f-164">DataAnnotations localization</span></span>

<span data-ttu-id="ac13f-165">DataAnnotations 错误消息已本地化与`IStringLocalizer<T>`。</span><span class="sxs-lookup"><span data-stu-id="ac13f-165">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="ac13f-166">使用选项`ResourcesPath = "Resources"`中的错误消息`RegisterViewModel`可以存储在以下路径之一：</span><span class="sxs-lookup"><span data-stu-id="ac13f-166">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="ac13f-167">Resources/ViewModels.Account.RegisterViewModel.fr.resx</span><span class="sxs-lookup"><span data-stu-id="ac13f-167">Resources/ViewModels.Account.RegisterViewModel.fr.resx</span></span>
* <span data-ttu-id="ac13f-168">Resources/ViewModels/Account/RegisterViewModel.fr.resx</span><span class="sxs-lookup"><span data-stu-id="ac13f-168">Resources/ViewModels/Account/RegisterViewModel.fr.resx</span></span>

<span data-ttu-id="ac13f-169">[!code-csharp[Main](localization/sample/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]</span><span class="sxs-lookup"><span data-stu-id="ac13f-169">[!code-csharp[Main](localization/sample/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]</span></span>

<span data-ttu-id="ac13f-170">在 ASP.NET 核心 MVC 1.1.0 和更高版本、 非验证属性已经进行了本地化。</span><span class="sxs-lookup"><span data-stu-id="ac13f-170">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="ac13f-171">ASP.NET 核心 MVC 1.0 未**不**查找非验证特性的本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="ac13f-171">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name=one-resource-string-multiple-classes></a>
### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="ac13f-172">为多个类使用一个的资源字符串</span><span class="sxs-lookup"><span data-stu-id="ac13f-172">Using one resource string for multiple classes</span></span>

<span data-ttu-id="ac13f-173">下面的代码演示如何使用与多个类别的验证特性的一个资源字符串：</span><span class="sxs-lookup"><span data-stu-id="ac13f-173">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="ac13f-174">在上面的代码，`SharedResource`是对应于 resx 类验证消息的存储位置。</span><span class="sxs-lookup"><span data-stu-id="ac13f-174">In the preceeding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="ac13f-175">使用此方法时，将仅使用 DataAnnotations `SharedResource`，而不是每个类的资源。</span><span class="sxs-lookup"><span data-stu-id="ac13f-175">With this approach,  DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span> 

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="ac13f-176">提供的语言和你支持的区域性的本地化的资源</span><span class="sxs-lookup"><span data-stu-id="ac13f-176">Provide localized resources for the languages and cultures you support</span></span>  

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="ac13f-177">SupportedCultures 和 SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="ac13f-177">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="ac13f-178">ASP.NET 核心，可指定两个区域性值，`SupportedCultures`和`SupportedUICultures`。</span><span class="sxs-lookup"><span data-stu-id="ac13f-178">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="ac13f-179">[CultureInfo](https://msdn.microsoft.com/library/system.globalization.cultureinfo(v=vs.110).aspx)对象`SupportedCultures`确定得到的依赖于区域性的函数，如日期、 时间、 数字和货币格式的结果。</span><span class="sxs-lookup"><span data-stu-id="ac13f-179">The [CultureInfo](https://msdn.microsoft.com/library/system.globalization.cultureinfo(v=vs.110).aspx) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="ac13f-180">`SupportedCultures`此外确定文本、 大小写约定和字符串比较的排序顺序。</span><span class="sxs-lookup"><span data-stu-id="ac13f-180">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="ac13f-181">请参阅[CultureInfo.CurrentCulture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.currentculture%28v=vs.110%29.aspx)有关服务器如何获取区域性的详细信息。</span><span class="sxs-lookup"><span data-stu-id="ac13f-181">See [CultureInfo.CurrentCulture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.currentculture%28v=vs.110%29.aspx) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="ac13f-182">`SupportedUICultures`确定就会转换字符串 (从*.resx*文件) 按查找[ResourceManager](https://msdn.microsoft.com/library/system.resources.resourcemanager(v=vs.110).aspx)。</span><span class="sxs-lookup"><span data-stu-id="ac13f-182">The `SupportedUICultures` determines which translates strings (from *.resx* files) are looked up by the [ResourceManager](https://msdn.microsoft.com/library/system.resources.resourcemanager(v=vs.110).aspx).</span></span> <span data-ttu-id="ac13f-183">`ResourceManager`只需查找区域性特定字符串由决定`CurrentUICulture`。</span><span class="sxs-lookup"><span data-stu-id="ac13f-183">The `ResourceManager` simply looks up culture-specific strings that is determined by `CurrentUICulture`.</span></span> <span data-ttu-id="ac13f-184">.NET 中的每个线程都具有`CurrentCulture`和`CurrentUICulture`对象。</span><span class="sxs-lookup"><span data-stu-id="ac13f-184">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="ac13f-185">ASP.NET 核心呈现依赖于区域性的函数时检查这些值。</span><span class="sxs-lookup"><span data-stu-id="ac13f-185">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="ac13f-186">例如，如果当前线程的区域性设置为"EN-US"（英语，美国），`DateTime.Now.ToLongDateString()`显示"星期四，2016 年 2 月 18，"，但如果`CurrentCulture`设置为"ES-ES"（西班牙语、 西班牙） 则输出将为"jueves，18 de febrero de 2016"。</span><span class="sxs-lookup"><span data-stu-id="ac13f-186">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="working-with-resource-files"></a><span data-ttu-id="ac13f-187">使用资源文件</span><span class="sxs-lookup"><span data-stu-id="ac13f-187">Working with resource files</span></span>

<span data-ttu-id="ac13f-188">资源文件是用于将可本地化的字符串与代码分离的有用机制。</span><span class="sxs-lookup"><span data-stu-id="ac13f-188">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="ac13f-189">非默认语言已翻译的字符串将被隔离*.resx*资源文件。</span><span class="sxs-lookup"><span data-stu-id="ac13f-189">Translated strings for the non-default language are isolated *.resx* resource files.</span></span> <span data-ttu-id="ac13f-190">例如，你可能想要创建名为西班牙语资源文件*Welcome.es.resx*包含转换字符串。</span><span class="sxs-lookup"><span data-stu-id="ac13f-190">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="ac13f-191">"es"是西班牙语的语言代码。</span><span class="sxs-lookup"><span data-stu-id="ac13f-191">"es" is the language code for Spanish.</span></span> <span data-ttu-id="ac13f-192">在 Visual Studio 中创建此资源文件：</span><span class="sxs-lookup"><span data-stu-id="ac13f-192">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="ac13f-193">在**解决方案资源管理器**，右键单击该文件夹将包含资源文件 >**添加** > **新项**。</span><span class="sxs-lookup"><span data-stu-id="ac13f-193">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![嵌套的上下文菜单： 在解决方案资源管理器，上下文菜单可供资源。](localization/_static/newi.png)

2. <span data-ttu-id="ac13f-196">在**搜索已安装的模板**框中，输入"资源"并将该文件。</span><span class="sxs-lookup"><span data-stu-id="ac13f-196">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![添加新项对话框](localization/_static/res.png)

3. <span data-ttu-id="ac13f-198">在输入密钥的值 （本机字符串）**名称**列和中的已翻译的字符串**值**列。</span><span class="sxs-lookup"><span data-stu-id="ac13f-198">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![带有单词名称列中的 Hello 和值列中的单词 Hola (西班牙语 Hello) Welcome.es.resx 文件 （西班牙语的欢迎使用资源文件）](localization/_static/hola.png)

    <span data-ttu-id="ac13f-200">Visual Studio 将显示*Welcome.es.resx*文件。</span><span class="sxs-lookup"><span data-stu-id="ac13f-200">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![显示欢迎使用西班牙语 (es) 资源文件的解决方案资源管理器](localization/_static/se.png)

<a name="error"></a>

<span data-ttu-id="ac13f-202">如果使用 Visual Studio 2017 预览版本 15.3，你将在资源编辑器中获得的错误指示器。</span><span class="sxs-lookup"><span data-stu-id="ac13f-202">If you are using Visual Studio 2017 Preview version 15.3, you'll get an error indicator in the resource editor.</span></span> <span data-ttu-id="ac13f-203">删除*ResXFileCodeGenerator*值从*自定义工具*属性网格，若要避免此错误消息：</span><span class="sxs-lookup"><span data-stu-id="ac13f-203">Remove the *ResXFileCodeGenerator*  value from the *Custom Tool*  properties grid to prevent this error message:</span></span>

![Resx 编辑器](localization/_static/err.png)

<span data-ttu-id="ac13f-205">或者，你可以忽略此错误。</span><span class="sxs-lookup"><span data-stu-id="ac13f-205">Alternatively, you can ignore this error.</span></span> <span data-ttu-id="ac13f-206">我们希望解决此问题在下一个版本。</span><span class="sxs-lookup"><span data-stu-id="ac13f-206">We hope to fix this in the next release.</span></span>

## <a name="resource-file-naming"></a><span data-ttu-id="ac13f-207">资源文件命名</span><span class="sxs-lookup"><span data-stu-id="ac13f-207">Resource file naming</span></span>

<span data-ttu-id="ac13f-208">资源在命名的程序集名称减其类的完整类型名称。</span><span class="sxs-lookup"><span data-stu-id="ac13f-208">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="ac13f-209">例如，其主要的程序集的项目中的法语资源`LocalizationWebsite.Web.dll`类`LocalizationWebsite.Web.Startup`将被命名为*Startup.fr.resx*。</span><span class="sxs-lookup"><span data-stu-id="ac13f-209">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="ac13f-210">类资源`LocalizationWebsite.Web.Controllers.HomeController`将被命名为*Controllers.HomeController.fr.resx*。</span><span class="sxs-lookup"><span data-stu-id="ac13f-210">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="ac13f-211">如果您的目标的类的命名空间不与程序集名称相同你将需要的完整类型名称。</span><span class="sxs-lookup"><span data-stu-id="ac13f-211">If your targeted class's namespace is not the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="ac13f-212">例如，在此示例中项目的类型的资源`ExtraNamespace.Tools`将被命名为*ExtraNamespace.Tools.fr.resx*。</span><span class="sxs-lookup"><span data-stu-id="ac13f-212">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="ac13f-213">在示例项目中，`ConfigureServices`方法设置`ResourcesPath`对"资源"，因此主控制器的法语资源文件的项目相对路径是*Resources/Controllers.HomeController.fr.resx*。</span><span class="sxs-lookup"><span data-stu-id="ac13f-213">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="ac13f-214">或者，可以使用文件夹来组织资源文件。</span><span class="sxs-lookup"><span data-stu-id="ac13f-214">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="ac13f-215">对于主控制器，该路径将为*Resources/Controllers/HomeController.fr.resx*。</span><span class="sxs-lookup"><span data-stu-id="ac13f-215">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="ac13f-216">如果不使用`ResourcesPath`选项， *.resx*文件会在项目的基目录中。</span><span class="sxs-lookup"><span data-stu-id="ac13f-216">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="ac13f-217">资源文件`HomeController`将被命名为*Controllers.HomeController.fr.resx*。</span><span class="sxs-lookup"><span data-stu-id="ac13f-217">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="ac13f-218">使用圆点或路径的命名约定的选择取决于你想要组织资源文件。</span><span class="sxs-lookup"><span data-stu-id="ac13f-218">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="ac13f-219">资源名称</span><span class="sxs-lookup"><span data-stu-id="ac13f-219">Resource name</span></span> | <span data-ttu-id="ac13f-220">圆点或路径命名</span><span class="sxs-lookup"><span data-stu-id="ac13f-220">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="ac13f-221">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="ac13f-221">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="ac13f-222">点</span><span class="sxs-lookup"><span data-stu-id="ac13f-222">Dot</span></span>  |
| <span data-ttu-id="ac13f-223">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="ac13f-223">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="ac13f-224">路径</span><span class="sxs-lookup"><span data-stu-id="ac13f-224">Path</span></span> |
|    |     |

<span data-ttu-id="ac13f-225">使用的资源文件`@inject IViewLocalizer`在 Razor 视图中遵循类似的模式。</span><span class="sxs-lookup"><span data-stu-id="ac13f-225">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="ac13f-226">可以使用点命名或路径命名命名为视图的资源文件。</span><span class="sxs-lookup"><span data-stu-id="ac13f-226">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="ac13f-227">Razor 视图资源文件模拟其关联的视图文件的路径。</span><span class="sxs-lookup"><span data-stu-id="ac13f-227">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="ac13f-228">假设我们设置`ResourcesPath`对"资源"的法语资源文件与*Views/Home/About.cshtml*视图可能是下列操作之一：</span><span class="sxs-lookup"><span data-stu-id="ac13f-228">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="ac13f-229">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="ac13f-229">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="ac13f-230">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="ac13f-230">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="ac13f-231">如果不使用`ResourcesPath`选项， *.resx*文件视图将位于视图所在的文件夹。</span><span class="sxs-lookup"><span data-stu-id="ac13f-231">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

<span data-ttu-id="ac13f-232">如果你删除"法国"区域性指示符，并具有设置为法语 （通过 cookie 或其他机制） 的区域性，请将读取默认资源文件，并已本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="ac13f-232">If you remove the ".fr" culture designator AND you have the culture set to French (via cookie or other mechanism), the default resource file is read and strings are localized.</span></span> <span data-ttu-id="ac13f-233">资源管理器指定的默认资源或回退资源，在执行任何操作符合您请求正在提供 *.resx 文件而不进行区域性指示符的区域性。</span><span class="sxs-lookup"><span data-stu-id="ac13f-233">The Resource manager designates a default or fallback resource, when nothing meets your requested culture you're served the *.resx file without a culture designator.</span></span> <span data-ttu-id="ac13f-234">如果你想要仅返回密钥，如果缺少区域性所请求资源必须具有默认资源文件。</span><span class="sxs-lookup"><span data-stu-id="ac13f-234">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generating-resource-files-with-visual-studio"></a><span data-ttu-id="ac13f-235">使用 Visual Studio 的生成资源文件</span><span class="sxs-lookup"><span data-stu-id="ac13f-235">Generating resource files with Visual Studio</span></span>

<span data-ttu-id="ac13f-236">如果你在而无需在文件名中区域性情况下将在 Visual Studio 中创建资源文件 (例如， *Welcome.resx*)，Visual Studio 将创建一个 C# 类的属性为每个字符串。</span><span class="sxs-lookup"><span data-stu-id="ac13f-236">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="ac13f-237">这通常是你不想使用 ASP.NET Core;通常不需要默认*.resx*资源文件 (一个*.resx*文件而无需区域性名称)。</span><span class="sxs-lookup"><span data-stu-id="ac13f-237">That's usually not what you want with ASP.NET Core; you typically don't have a default *.resx* resource file (A *.resx* file without the culture name).</span></span> <span data-ttu-id="ac13f-238">我们建议你创建*.resx*使用区域性名称的文件 (例如*Welcome.fr.resx*)。</span><span class="sxs-lookup"><span data-stu-id="ac13f-238">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="ac13f-239">当你创建*.resx*使用区域性名称，Visual Studio 的文件将不会生成的类文件。</span><span class="sxs-lookup"><span data-stu-id="ac13f-239">When you create a *.resx* file with a culture name, Visual Studio will not generate the class file.</span></span> <span data-ttu-id="ac13f-240">我们预计许多开发人员将**不**创建默认语言资源文件。</span><span class="sxs-lookup"><span data-stu-id="ac13f-240">We anticipate that many developers will **not** create a default language resource file.</span></span>

### <a name="adding-other-cultures"></a><span data-ttu-id="ac13f-241">添加其他区域性</span><span class="sxs-lookup"><span data-stu-id="ac13f-241">Adding Other Cultures</span></span>

<span data-ttu-id="ac13f-242">每个语言和区域性的组合 （以外的默认语言） 需要唯一的资源文件。</span><span class="sxs-lookup"><span data-stu-id="ac13f-242">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="ac13f-243">通过创建新的资源文件的 ISO 语言代码中的文件名称的一部分创建不同的区域性和区域设置的资源文件 (例如， **en-我们**， **fr ca**，和**en gb**)。</span><span class="sxs-lookup"><span data-stu-id="ac13f-243">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="ac13f-244">这些 ISO 代码位于之间的文件名称和*.resx*文件扩展名，如*Welcome.es MX.resx* （西班牙语/墨西哥）。</span><span class="sxs-lookup"><span data-stu-id="ac13f-244">These ISO codes are placed between the file name and the *.resx* file name extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span> <span data-ttu-id="ac13f-245">若要指定的区域性的、 非特定语言，删除国家/地区代码 (`MX`在前面的示例)。</span><span class="sxs-lookup"><span data-stu-id="ac13f-245">To specify a culturally neutral language, remove the country code (`MX` in the preceding example).</span></span> <span data-ttu-id="ac13f-246">非特定区域性的西班牙语资源文件的名称*Welcome.es.resx*。</span><span class="sxs-lookup"><span data-stu-id="ac13f-246">The culturally neutral Spanish resource file name is *Welcome.es.resx*.</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="ac13f-247">实现策略来选择用于每个请求的语言/区域性</span><span class="sxs-lookup"><span data-stu-id="ac13f-247">Implement a strategy to select the language/culture for each request</span></span>  

### <a name="configuring-localization"></a><span data-ttu-id="ac13f-248">配置本地化</span><span class="sxs-lookup"><span data-stu-id="ac13f-248">Configuring localization</span></span>

<span data-ttu-id="ac13f-249">在配置本地化`ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="ac13f-249">Localization is configured in the `ConfigureServices` method:</span></span>

<span data-ttu-id="ac13f-250">[!code-csharp[Main](localization/sample/Startup.cs?range=45-49)]</span><span class="sxs-lookup"><span data-stu-id="ac13f-250">[!code-csharp[Main](localization/sample/Startup.cs?range=45-49)]</span></span>

* <span data-ttu-id="ac13f-251">`AddLocalization`将本地化服务添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="ac13f-251">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="ac13f-252">上面的代码还将设置"资源"的资源路径。</span><span class="sxs-lookup"><span data-stu-id="ac13f-252">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="ac13f-253">`AddViewLocalization`添加了对本地化的查看文件支持。</span><span class="sxs-lookup"><span data-stu-id="ac13f-253">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="ac13f-254">在此示例视图中本地化基于视图文件后缀。</span><span class="sxs-lookup"><span data-stu-id="ac13f-254">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="ac13f-255">例如"fr"中的*Index.fr.cshtml*文件。</span><span class="sxs-lookup"><span data-stu-id="ac13f-255">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="ac13f-256">`AddDataAnnotationsLocalization`增加了支持对本地化的`DataAnnotations`验证消息通过`IStringLocalizer`抽象。</span><span class="sxs-lookup"><span data-stu-id="ac13f-256">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="ac13f-257">本地化中间件</span><span class="sxs-lookup"><span data-stu-id="ac13f-257">Localization middleware</span></span>

<span data-ttu-id="ac13f-258">在请求的当前区域性设置本地化[中间件](middleware.md)。</span><span class="sxs-lookup"><span data-stu-id="ac13f-258">The current culture on a request is set in the localization [Middleware](middleware.md).</span></span> <span data-ttu-id="ac13f-259">在中启用本地化中间件`Configure`方法*Startup.cs*文件。</span><span class="sxs-lookup"><span data-stu-id="ac13f-259">The localization middleware is enabled in the `Configure` method of *Startup.cs* file.</span></span> <span data-ttu-id="ac13f-260">请注意，必须在其中可以检查请求区域性的任何中间件之前配置本地化中间件 (例如， `app.UseMvc()`)。</span><span class="sxs-lookup"><span data-stu-id="ac13f-260">Note,  the localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvc()`).</span></span>

<span data-ttu-id="ac13f-261">[!code-csharp[Main](localization/sample/Startup.cs?highlight=13-35&range=123-159)]</span><span class="sxs-lookup"><span data-stu-id="ac13f-261">[!code-csharp[Main](localization/sample/Startup.cs?highlight=13-35&range=123-159)]</span></span>

<span data-ttu-id="ac13f-262">`UseRequestLocalization`初始化`RequestLocalizationOptions`对象。</span><span class="sxs-lookup"><span data-stu-id="ac13f-262">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="ac13f-263">对每个请求列表的`RequestCultureProvider`中`RequestLocalizationOptions`枚举和使用的第一个提供程序已成功确定请求区域性。</span><span class="sxs-lookup"><span data-stu-id="ac13f-263">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="ac13f-264">默认的提供程序来自`RequestLocalizationOptions`类：</span><span class="sxs-lookup"><span data-stu-id="ac13f-264">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="ac13f-265">默认值列表将从最具体到最不具体转。</span><span class="sxs-lookup"><span data-stu-id="ac13f-265">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="ac13f-266">在本文的后面，我们将了解如何更改的顺序和甚至添加自定义区域性提供程序。</span><span class="sxs-lookup"><span data-stu-id="ac13f-266">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="ac13f-267">如果没有一个提供程序可以确定请求区域性，`DefaultRequestCulture`使用。</span><span class="sxs-lookup"><span data-stu-id="ac13f-267">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="ac13f-268">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="ac13f-268">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="ac13f-269">某些应用程序，将使用查询字符串来设置[区域性和 UI 区域性](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx#Current)。</span><span class="sxs-lookup"><span data-stu-id="ac13f-269">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx#Current).</span></span> <span data-ttu-id="ac13f-270">对于使用 cookie 或接受语言标头方法的应用程序，向 URL 添加查询字符串可用于调试和测试代码。</span><span class="sxs-lookup"><span data-stu-id="ac13f-270">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="ac13f-271">默认情况下，`QueryStringRequestCultureProvider`注册为第一个本地化提供程序在`RequestCultureProvider`列表。</span><span class="sxs-lookup"><span data-stu-id="ac13f-271">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="ac13f-272">将查询字符串参数`culture`和`ui-culture`。</span><span class="sxs-lookup"><span data-stu-id="ac13f-272">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="ac13f-273">下面的示例设置西班牙语/墨西哥的特定区域性 （语言和区域）：</span><span class="sxs-lookup"><span data-stu-id="ac13f-273">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="ac13f-274">如果您仅以两种状态之一传递 (`culture`或`ui-culture`)，查询字符串提供程序将使用你传入这两个值。</span><span class="sxs-lookup"><span data-stu-id="ac13f-274">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="ac13f-275">例如，设置仅的区域性将同时设置`Culture`和`UICulture`:</span><span class="sxs-lookup"><span data-stu-id="ac13f-275">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="ac13f-276">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="ac13f-276">CookieRequestCultureProvider</span></span>

<span data-ttu-id="ac13f-277">通常，生产应用将提供一种机制来设置使用 ASP.NET Core 区域性 cookie 的区域性。</span><span class="sxs-lookup"><span data-stu-id="ac13f-277">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="ac13f-278">使用`MakeCookieValue`方法来创建一个 cookie。</span><span class="sxs-lookup"><span data-stu-id="ac13f-278">Use the `MakeCookieValue`  method to create a cookie.</span></span>

<span data-ttu-id="ac13f-279">`CookieRequestCultureProvider` `DefaultCookieName`返回用来跟踪用户的默认 cookie 名称的首选区域性信息。</span><span class="sxs-lookup"><span data-stu-id="ac13f-279">The `CookieRequestCultureProvider` `DefaultCookieName`  returns the default cookie name used to track the user’s preferred culture information.</span></span> <span data-ttu-id="ac13f-280">默认的 cookie 名称是"。AspNetCore.Culture"。</span><span class="sxs-lookup"><span data-stu-id="ac13f-280">The default cookie  name is ".AspNetCore.Culture".</span></span>

<span data-ttu-id="ac13f-281">Cookie 格式`c=%LANGCODE%|uic=%LANGCODE%`，其中`c`是`Culture`和`uic`是`UICulture`，例如：</span><span class="sxs-lookup"><span data-stu-id="ac13f-281">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="ac13f-282">如果您仅指定其中一个区域性信息和 UI 区域性，则指定的区域性将使用区域性信息和 UI 区域性。</span><span class="sxs-lookup"><span data-stu-id="ac13f-282">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="ac13f-283">Accept-language HTTP 标头</span><span class="sxs-lookup"><span data-stu-id="ac13f-283">The Accept-Language HTTP header</span></span>

<span data-ttu-id="ac13f-284">[接受语言标头](https://www.w3.org/International/questions/qa-accept-lang-locales)是可在大多数浏览器设置，最初用于指定用户的语言。</span><span class="sxs-lookup"><span data-stu-id="ac13f-284">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="ac13f-285">此设置指示浏览器已设置为发送或从基础操作系统已继承。</span><span class="sxs-lookup"><span data-stu-id="ac13f-285">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="ac13f-286">浏览器请求的 Accept 语言 HTTP 标头不是一种可靠的方法来检测用户的首选的语言 (请参阅[在浏览器中设置语言首选项](https://www.w3.org/International/questions/qa-lang-priorities.en.php))。</span><span class="sxs-lookup"><span data-stu-id="ac13f-286">The Accept-Language HTTP header from a browser request is not an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="ac13f-287">生产应用程序应包括用户可以自定义区域性他们选择一种方法。</span><span class="sxs-lookup"><span data-stu-id="ac13f-287">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="setting-the-accept-language-http-header-in-ie"></a><span data-ttu-id="ac13f-288">在 IE 中设置 Accept 语言 HTTP 标头</span><span class="sxs-lookup"><span data-stu-id="ac13f-288">Setting the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="ac13f-289">从齿轮图标，点击**Internet 选项**。</span><span class="sxs-lookup"><span data-stu-id="ac13f-289">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="ac13f-290">点击**语言**。</span><span class="sxs-lookup"><span data-stu-id="ac13f-290">Tap **Languages**.</span></span>

    ![Internet 选项](localization/_static/lang.png)

3. <span data-ttu-id="ac13f-292">点击**设置语言首选项**。</span><span class="sxs-lookup"><span data-stu-id="ac13f-292">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="ac13f-293">点击**添加一种语言**。</span><span class="sxs-lookup"><span data-stu-id="ac13f-293">Tap **Add a language**.</span></span>

5. <span data-ttu-id="ac13f-294">添加的语言。</span><span class="sxs-lookup"><span data-stu-id="ac13f-294">Add the language.</span></span>

6. <span data-ttu-id="ac13f-295">点击语言，然后点击**移**。</span><span class="sxs-lookup"><span data-stu-id="ac13f-295">Tap the language, then tap **Move Up**.</span></span>

### <a name="using-a-custom-provider"></a><span data-ttu-id="ac13f-296">使用自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="ac13f-296">Using a custom provider</span></span>

<span data-ttu-id="ac13f-297">假设你想要让客户在数据库中存储其语言和区域性。</span><span class="sxs-lookup"><span data-stu-id="ac13f-297">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="ac13f-298">你可以编写一个提供程序来查找用户的这些值。</span><span class="sxs-lookup"><span data-stu-id="ac13f-298">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="ac13f-299">下面的代码演示如何添加自定义提供程序：</span><span class="sxs-lookup"><span data-stu-id="ac13f-299">The following code shows how to add a custom provider:</span></span>

```csharp
services.Configure<RequestLocalizationOptions>(options =>
   {
       var supportedCultures = new[]
       {
           new CultureInfo("en-US"),
           new CultureInfo("fr")
       };

       options.DefaultRequestCulture = new RequestCulture(culture: "en-US", uiCulture: "en-US");
       options.SupportedCultures = supportedCultures;
       options.SupportedUICultures = supportedCultures;

       options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
       {
         // My custom request culture logic
         return new ProviderCultureResult("en");
       }));
   });
   ```

<span data-ttu-id="ac13f-300">使用`RequestLocalizationOptions`添加或删除本地化提供程序。</span><span class="sxs-lookup"><span data-stu-id="ac13f-300">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="setting-the-culture-programmatically"></a><span data-ttu-id="ac13f-301">以编程方式设置区域性</span><span class="sxs-lookup"><span data-stu-id="ac13f-301">Setting the culture programmatically</span></span>

<span data-ttu-id="ac13f-302">此示例**Localization.StarterWeb**项目上[GitHub](https://github.com/aspnet/entropy)包含 UI，以设置`Culture`。</span><span class="sxs-lookup"><span data-stu-id="ac13f-302">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="ac13f-303">*Views/Shared/_SelectLanguagePartial.cshtml*文件可以从支持的区域性的列表中选择区域性：</span><span class="sxs-lookup"><span data-stu-id="ac13f-303">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

<span data-ttu-id="ac13f-304">[!code-HTML[Main](localization/sample/Views/Shared/_SelectLanguagePartial.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="ac13f-304">[!code-HTML[Main](localization/sample/Views/Shared/_SelectLanguagePartial.cshtml)]</span></span>

<span data-ttu-id="ac13f-305">*Views/Shared/_SelectLanguagePartial.cshtml*文件添加到`footer`的布局文件，使它将可供所有视图的部分：</span><span class="sxs-lookup"><span data-stu-id="ac13f-305">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

<span data-ttu-id="ac13f-306">[!code-HTML[Main](localization/sample/Views/Shared/_Layout.cshtml?range=48-61&highlight=10)]</span><span class="sxs-lookup"><span data-stu-id="ac13f-306">[!code-HTML[Main](localization/sample/Views/Shared/_Layout.cshtml?range=48-61&highlight=10)]</span></span>

<span data-ttu-id="ac13f-307">`SetLanguage`方法会设置区域性 cookie。</span><span class="sxs-lookup"><span data-stu-id="ac13f-307">The `SetLanguage` method sets the culture cookie.</span></span>

<span data-ttu-id="ac13f-308">[!code-csharp[Main](localization/sample/Controllers/HomeController.cs?range=57-67)]</span><span class="sxs-lookup"><span data-stu-id="ac13f-308">[!code-csharp[Main](localization/sample/Controllers/HomeController.cs?range=57-67)]</span></span>

<span data-ttu-id="ac13f-309">您不能插入*_SelectLanguagePartial.cshtml*为此项目的代码示例。</span><span class="sxs-lookup"><span data-stu-id="ac13f-309">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="ac13f-310">**Localization.StarterWeb**项目上[GitHub](https://github.com/aspnet/entropy)具有代码流`RequestLocalizationOptions`到 Razor 部分通过[依赖关系注入](dependency-injection.md)容器。</span><span class="sxs-lookup"><span data-stu-id="ac13f-310">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="ac13f-311">全球化和本地化条款</span><span class="sxs-lookup"><span data-stu-id="ac13f-311">Globalization and localization terms</span></span>

<span data-ttu-id="ac13f-312">本地化你的应用程序的过程还要求通常在现代软件开发中使用的字符集相关的一个基本的了解并与之关联的问题的了解。</span><span class="sxs-lookup"><span data-stu-id="ac13f-312">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="ac13f-313">尽管所有计算机将文本都存储为数字 （代码），不同的系统都存储使用不同数量的相同文本。</span><span class="sxs-lookup"><span data-stu-id="ac13f-313">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="ac13f-314">在本地化过程是指转换特定区域性或区域设置的应用程序用户界面 (UI)。</span><span class="sxs-lookup"><span data-stu-id="ac13f-314">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="ac13f-315">[可本地化性](https://msdn.microsoft.com/library/aa292135(v=vs.71).aspx)是中间的过程，用于验证的全球化的应用是否准备好进行本地化。</span><span class="sxs-lookup"><span data-stu-id="ac13f-315">[Localizability](https://msdn.microsoft.com/library/aa292135(v=vs.71).aspx) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="ac13f-316">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt)设置格式的区域性名称为"<languagecode2>-< country/regioncode2 >"，其中<languagecode2>是语言代码，< country/regioncode2 > 是子区域性代码。</span><span class="sxs-lookup"><span data-stu-id="ac13f-316">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is "<languagecode2>-<country/regioncode2>", where <languagecode2> is the language code and <country/regioncode2> is the subculture code.</span></span> <span data-ttu-id="ac13f-317">例如，`es-CL`为西班牙语 （智利）`en-US`为英语 （美国） 和`en-AU`以表示英语 （澳大利亚）。</span><span class="sxs-lookup"><span data-stu-id="ac13f-317">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="ac13f-318">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt)是两个字母小写区域性代码与语言相关 ISO 639 和两个字母大写子区域性代码关联的国家或地区与 ISO 3166 的组合。</span><span class="sxs-lookup"><span data-stu-id="ac13f-318">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span>  <span data-ttu-id="ac13f-319">请参阅[语言区域性名称](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx)。</span><span class="sxs-lookup"><span data-stu-id="ac13f-319">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="ac13f-320">国际化常缩写为"I18N"。</span><span class="sxs-lookup"><span data-stu-id="ac13f-320">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="ac13f-321">缩写采用第一个和最后一个字母并它们，因此 18 之间号的数量代表数量的第一个之间的字母"I"和最后一个"N"。</span><span class="sxs-lookup"><span data-stu-id="ac13f-321">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="ac13f-322">这同样适用于全球化 (G11N) 和本地化 (L10N)。</span><span class="sxs-lookup"><span data-stu-id="ac13f-322">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="ac13f-323">条款：</span><span class="sxs-lookup"><span data-stu-id="ac13f-323">Terms:</span></span>

* <span data-ttu-id="ac13f-324">全球化 (G11N): 使应用程序支持不同的语言和区域的过程。</span><span class="sxs-lookup"><span data-stu-id="ac13f-324">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="ac13f-325">本地化 (L10N): 自定义的过程适用于给定的语言和区域的应用。</span><span class="sxs-lookup"><span data-stu-id="ac13f-325">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="ac13f-326">国际化 (I18N): 描述全球化和本地化。</span><span class="sxs-lookup"><span data-stu-id="ac13f-326">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="ac13f-327">区域性： 它是一种语言和区域，（可选）。</span><span class="sxs-lookup"><span data-stu-id="ac13f-327">Culture: It is a language and, optionally, a region.</span></span>
* <span data-ttu-id="ac13f-328">非特定区域性： 一个具有指定的语言中，但不是区域的区域性。</span><span class="sxs-lookup"><span data-stu-id="ac13f-328">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="ac13f-329">(例如"en"，"es")</span><span class="sxs-lookup"><span data-stu-id="ac13f-329">(for example "en", "es")</span></span>
* <span data-ttu-id="ac13f-330">特定区域性： 一个具有指定的语言和区域的区域性。</span><span class="sxs-lookup"><span data-stu-id="ac13f-330">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="ac13f-331">（有关示例"EN-US"、"EN-GB"、"es CL"）</span><span class="sxs-lookup"><span data-stu-id="ac13f-331">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="ac13f-332">区域设置： 区域设置是相同的区域性。</span><span class="sxs-lookup"><span data-stu-id="ac13f-332">Locale: A locale is the same as a culture.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ac13f-333">其他资源</span><span class="sxs-lookup"><span data-stu-id="ac13f-333">Additional Resources</span></span>

* <span data-ttu-id="ac13f-334">[Localization.StarterWeb 项目](https://github.com/aspnet/entropy)文章中使用。</span><span class="sxs-lookup"><span data-stu-id="ac13f-334">[Localization.StarterWeb project](https://github.com/aspnet/entropy) used in the article.</span></span>
* <span data-ttu-id="ac13f-335">[Visual Studio 中的资源文件](https://msdn.microsoft.com/library/xbx3z216(v=vs.110).aspx#VSResFiles)</span><span class="sxs-lookup"><span data-stu-id="ac13f-335">[Resource Files in Visual Studio](https://msdn.microsoft.com/library/xbx3z216(v=vs.110).aspx#VSResFiles)</span></span>
* <span data-ttu-id="ac13f-336">[.Resx 文件中的资源](https://msdn.microsoft.com/library/xbx3z216(v=vs.110).aspx#ResourcesFiles)</span><span class="sxs-lookup"><span data-stu-id="ac13f-336">[Resources in .resx Files](https://msdn.microsoft.com/library/xbx3z216(v=vs.110).aspx#ResourcesFiles)</span></span>
