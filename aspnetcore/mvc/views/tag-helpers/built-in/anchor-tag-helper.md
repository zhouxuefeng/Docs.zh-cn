---
title: "定位点标记帮助器 |Microsoft 文档"
author: pkellner
description: "演示如何使用定位点标记帮助器"
keywords: "ASP.NET Core, 标记帮助程序"
ms.author: riande
manager: wpickett
ms.date: 12/20/2017
ms.topic: article
ms.assetid: c045d485-d1dc-4cea-a675-46be83b7a011
ms.technology: aspnet
ms.prod: aspnet-core
uid: mvc/views/tag-helpers/builtin-th/anchor-tag-helper
ms.openlocfilehash: 86756a1d09e6e55ca79aed6e5b718088b82b782c
ms.sourcegitcommit: 2b263e87217658caa42eedc4f9d2d21ef0ab5d59
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2018
---
# <a name="anchor-tag-helper"></a>定位点标记帮助器

作者：[Peter Kellner](http://peterkellner.net) 

定位点标记帮助程序增强了 HTML 定位点 (`<a ... ></a>`) 通过添加新属性的标记。 生成的链接 (上`href`标记) 创建使用新的属性。 该 URL 可以包括可选的协议 （如 https)。

下面的扬声器控制器是本文档中的示例中使用。

**SpeakerController.cs** 

[!code-csharp[SpeakerController](sample/TagHelpersBuiltInAspNetCore/src/TagHelpersBuiltInAspNetCore/Controllers/SpeakerController.cs)]


## <a name="anchor-tag-helper-attributes"></a>定位点标记帮助器属性

### <a name="asp-controller"></a>asp 控制器

`asp-controller`用于将相关联的控制器将用于生成 URL。 指定的控制器必须存在于当前项目。 下面的代码列出所有发言人： 

```cshtml
<a asp-controller="Speaker" asp-action="Index">All Speakers</a>
```

将生成的标记：

```html
<a href="/Speaker">All Speakers</a>
```

如果`asp-controller`指定和`asp-action`不是默认`asp-action`将当前正在执行的视图的默认控制器方法。 即，在上面的示例中，如果`asp-action`留出，和从生成此定位点标记帮助器*HomeController*的`Index`视图 (**/主页**)，将生成的标记：

```html
<a href="/Home">All Speakers</a>
```

### <a name="asp-action"></a>asp 操作

`asp-action`是将包含控制器中的操作方法的名称在生成`href`。 例如，下面的代码将设置生成`href`以指向扬声器详细信息页：

```html
<a asp-controller="Speaker" asp-action="Detail">Speaker Detail</a>
```

将生成的标记：

```html
<a href="/Speaker/Detail">Speaker Detail</a>
```

如果没有`asp-controller`指定特性时，将使用默认控制器调用执行当前视图的视图。  
 
如果该属性`asp-action`是`Index`，则任何操作不追加到的 URL，从而导致默认`Index`所调用方法。 指定的操作 （或默认），必须存在于中引用的控制器`asp-controller`。

### <a name="asp-page"></a>asp 页面

使用`asp-page`中的定位点标记，若要设置其 URL 为指向特定页属性。 前缀的页名称以正斜杠"/"创建 URL。 下面的示例中的 URL 指向的当前目录中的"发言人"页。

```cshtml
<a asp-page="/Speakers">All Speakers</a>
```

`asp-page`中前面的代码示例属性呈现在类似于下面的代码段的视图中的 HTML 输出：

```html
<a href="/items?page=%2FSpeakers">Speakers</a>
```

`asp-page`属性是与互斥`asp-route`， `asp-controller`，和`asp-action`属性。 但是，`asp-page`可以与使用`asp-route-id`来控制路由，如下面的代码示例所示：

```cshtml
<a asp-page="/Speaker" asp-route-id="@speaker.Id">View Speaker</a>
```

`asp-route-id`生成以下输出：

```html
https://localhost:44399/Speakers/Index/2?page=%2FSpeaker
```

> [!NOTE]
> 若要使用`asp-page`在 Razor 页中，Url 属性必须是相对路径，例如`"./Speaker"`。 中的相对路径`asp-page`属性不可用在 MVC 视图中。 而是针对 MVC 视图使用"/"语法。

### <a name="asp-route-value"></a>asp 的路由的 {value}

`asp-route-`是一个通配符路由前缀。 任何 put 后尾随 dash 将解释为潜在的路由参数值。 如果未找到默认路由，则此路由前缀将追加到为请求参数和值生成的 href。 否则它将在路由模板中进行替换。

假设您有一个控制器方法定义，如下所示：

```csharp
public IActionResult AnchorTagHelper(string id)
{
    var speaker = new SpeakerData()
    {
        SpeakerId = 12
    };
    return View(viewName, speaker);
}
```

并且具有默认路由模板中定义你*Startup.cs* ，如下所示：

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
});

```

**Cshtml**文件，其中包含有必要将使用定位点标记帮助器**扬声器**从控制器中传递到视图的模型参数是，如下所示：

```cshtml
@model SpeakerData
<!DOCTYPE html>
<html><body>
<a asp-controller='Speaker' asp-action='Detail' asp-route-id=@Model.SpeakerId>SpeakerId: @Model.SpeakerId</a>
<body></html>
```

生成的 HTML 将然后是，如下所示，因为**id**位于默认路由。

```html
<a href='/Speaker/Detail/12'>SpeakerId: 12</a>
```

如果路由前缀不找到路由模板的一部分，这是替换为以下这种情况**cshtml**文件：

```cshtml
@model SpeakerData
<!DOCTYPE html>
<html><body>
<a asp-controller='Speaker' asp-action='Detail' asp-route-speakerid=@Model.SpeakerId>SpeakerId: @Model.SpeakerId</a>
<body></html>
```

生成的 HTML 将然后是，如下所示，因为**speakerid**匹配的路由中未找到：

```html
<a href='/Speaker/Detail?speakerid=12'>SpeakerId: 12</a>
```

如果任一`asp-controller`或`asp-action`未指定，则相同的默认处理后面，如在`asp-route`属性。

### <a name="asp-route"></a>asp 路由

`asp-route`使您能够创建链接直接到命名路由的 URL。 使用路由特性，路由可以命名为中所示`SpeakerController`和中使用其`Evaluations`方法。

`Name = "speakerevals"`告知定位点标记帮助器以生成直接指向该控制器方法使用的 URL 的路由`/Speaker/Evaluations`。 如果`asp-controller`或`asp-action`指定除了`asp-route`，生成的路由可能不是你的预期。 `asp-route`不应与属性任一使用`asp-controller`或`asp-action`以避免路由冲突。

### <a name="asp-all-route-data"></a>asp 所有路线数据

`asp-all-route-data`可以创建的字典的键/值对，其中键是参数名称，值是与该项关联的值。

如以下示例所示，创建一个内联字典和数据传递到 razor 视图。 作为替代方法，还可以使用您的模型传递数据。

```cshtml
@{
    var dict =
        new Dictionary<string, string>
        {
            {"speakerId", "11"},
            {"currentYear", "true"}
        };
}
<a asp-route="speakerevalscurrent"
asp-all-route-data="dict">SpeakerEvals</a>
```

上面的代码生成以下 URL: http://localhost/Speaker/EvaluationsCurrent?speakerId=11&currentYear=true

单击该链接后，控制器方法`EvaluationsCurrent`调用。 因为该控制器具有匹配什么从已创建的两个字符串参数调用`asp-all-route-data`字典。

如果字典匹配项中的任何密钥传送参数，这些值将替换为相应的路由中和其他的不匹配的值会转换为请求参数。

### <a name="asp-fragment"></a>asp 片段

`asp-fragment`定义要追加到 URL 的 URL 片段。 定位点标记帮助程序将添加的哈希字符 （#）。 如果要创建一个标记：

```cshtml
<a asp-action="Evaluations" asp-controller="Speaker"  
   asp-fragment="SpeakerEvaluations">About Speaker Evals</a>
```

生成的 URL 将是： http://localhost/Speaker/Evaluations#SpeakerEvaluations

构建客户端应用程序时，哈希标记很有用。 它们可用于轻松的标记和在 JavaScript 中，例如搜索。

### <a name="asp-area"></a>asp 区域

`asp-area`设置 ASP.NET Core 使用设置适当的路由的区域名称。 下面是如何区域属性将导致重新路由映射的示例。 设置`asp-area`博客前缀目录`Areas/Blogs`到关联的控制器和视图此定位点标记的路由。

* 项目名称
  * wwwroot
  * 区域
    * 博客
      * 控制器
        * HomeController.cs
      * 视图
        * 主页
          * Index.cshtml
          * AboutBlog.cshtml
  * 控制器

指定一个区域标记，它是有效的如```area="Blogs"```引用时```AboutBlog.cshtml```文件将如下所示使用定位点标记帮助器。

```cshtml
<a asp-action="AboutBlog" asp-controller="Home" asp-area="Blogs">Blogs About</a>
```

生成的 HTML 将包括区域段，并将如下所示：

```html
<a href="/Blogs/Home/AboutBlog">Blogs About</a>
```

> [!TIP]
> Web 应用程序中的工作的 MVC 区域，路由模板必须包括如果它存在到区域的引用。 该模板，这是第二个参数的`routes.MapRoute`方法调用中，将显示为：`template: '"{area:exists}/{controller=Home}/{action=Index}"'`

### <a name="asp-protocol"></a>asp 协议

`asp-protocol`可用于指定一种协议 (如`https`) 在你的 URL。 示例包括协议的定位点标记帮助器将如下所示：

```<a asp-protocol="https" asp-action="About" asp-controller="Home">About</a>```

并将生成 HTML，如下所示：

```<a href="https://localhost/Home/About">About</a>```

在示例中的域是 localhost，但定位点标记帮助程序生成 URL 时使用网站的公共域。

## <a name="additional-resources"></a>其他资源

* [区域](xref:mvc/controllers/areas)
