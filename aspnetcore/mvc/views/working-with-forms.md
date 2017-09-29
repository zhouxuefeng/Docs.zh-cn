---
title: "在 ASP.NET 核心中的窗体中的标记帮助程序"
author: rick-anderson
description: "描述的内置标记帮助程序用于窗体。"
keywords: "ASP.NET 核心，标记帮助器，TagHelper，HTML 窗体中，窗体"
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: 25595059-4fac-4785-8152-f88590e3169b
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/working-with-forms
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ff6fee6eee539fc77b6c6180a816daa760202848
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2017
---
# <a name="introduction-to-using-tag-helpers-in-forms-in-aspnet-core"></a>在 ASP.NET Core 中的窗体中使用标记帮助器简介

通过[Rick Anderson](https://twitter.com/RickAndMSFT)， [Dave Paquette](https://twitter.com/Dave_Paquette)，和[Jerrie Pelser](https://github.com/jerriep)

本文档演示如何使用窗体和窗体上的常用的 HTML 元素。 HTML[窗体](https://www.w3.org/TR/html401/interact/forms.html)元素提供的主要机制 web 应用使用发布到服务器的备份数据。 大部分本文档介绍[标记帮助程序](tag-helpers/intro.md)以及它们可以帮助你高效创建可靠的 HTML 窗体。 我们建议你阅读[简介标记帮助程序](tag-helpers/intro.md)在阅读本文档之前。

在许多情况下，HTML 帮助器向特定的标记帮助器，提供的备用方法，但务必要识别标记帮助程序不替换 HTML 帮助器并不是每个 HTML 帮助器标记帮助器。 HTML 帮助器或者当存在时，被提到而变。

<a name=my-asp-route-param-ref-label></a>

## <a name="the-form-tag-helper"></a>窗体标记帮助器

[窗体](https://www.w3.org/TR/html401/interact/forms.html)标记帮助器：

* 生成 HTML [\<窗体 >](https://www.w3.org/TR/html401/interact/forms.html) `action`的 MVC 控制器操作或命名的路由的属性值

* 生成隐藏[请求验证令牌](https://docs.microsoft.com/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)以防止跨站点请求伪造 (一起使用时`[ValidateAntiForgeryToken]`HTTP Post 操作方法中的属性)

* 提供`asp-route-<Parameter Name>`属性，其中`<Parameter Name>`添加到路由值。 `routeValues`参数`Html.BeginForm`和`Html.BeginRouteForm`提供类似的功能。

* 具有备用的 HTML 帮助器`Html.BeginForm`和`Html.BeginRouteForm`

示例:

[!code-HTML[Main](working-with-forms/sample/final/Views/Demo/RegisterFormOnly.cshtml)]

上述窗体标记帮助程序生成以下 HTML:

```HTML
<form method="post" action="/Demo/Register">
     <!-- Input and Submit elements -->
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
    </form>
```

MVC 运行时生成`action`属性从窗体标记帮助器属性的值`asp-controller`和`asp-action`。 窗体标记帮助器还会生成隐藏[请求验证令牌](https://docs.microsoft.com/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)以防止跨站点请求伪造 (一起使用时`[ValidateAntiForgeryToken]`HTTP Post 操作方法中的属性)。 一个纯 HTML 窗体防止跨站点请求伪造会很困难，窗体标记帮助器提供此服务为你。

### <a name="using-a-named-route"></a>使用命名的路由

`asp-route`标记帮助器属性还可以为 HTML 生成标记`action`属性。 使用应用程序[路由](../../fundamentals/routing.md)名为`register`可以使用注册页上的以下标记：

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterRoute.cshtml)]

中的视图的许多*视图/帐户*文件夹 (在创建与新的 web 应用程序时生成*单个用户帐户*) 包含[asp 路由 returnurl](https://docs.microsoft.com/aspnet/core/mvc/views/working-with-forms)属性：

```cshtml
<form asp-controller="Account" asp-action="Login"
     asp-route-returnurl="@ViewData["ReturnUrl"]"
     method="post" class="form-horizontal" role="form">
```

>[!NOTE]
>利用内置的模板，`returnUrl`仅将在您尝试访问的授权的资源但不是进行身份验证或授权时自动填充。 当试图未经授权的访问时，安全中间件将你重定向至登录页与`returnUrl`设置。

## <a name="the-input-tag-helper"></a>输入的标记帮助器

输入标记帮助器绑定 HTML [\<输入 >](https://www.w3.org/wiki/HTML/Elements/input)为模型表达式在 razor 视图中的元素。

语法：

```HTML
<input asp-for="<Expression Name>" />
```

输入的标记帮助器：

* 生成`id`和`name`中指定的表达式名称的 HTML 特性`asp-for`属性。 `asp-for="Property1.Property2"` 与 `m => m.Property1.Property2` 相等。 该表达式的名称是用途`asp-for`属性值。 请参阅[表达式名称](#expression-names)部分以了解更多信息。

* 设置 HTML`type`属性值根据模型类型和[数据注释](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter)特性应用于模型属性

* 将不会覆盖 HTML`type`时指定了一个属性值

* 生成[HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)验证属性从[数据注释](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter)特性应用于模型属性

* 具有与重叠的 HTML 帮助器功能`Html.TextBoxFor`和`Html.EditorFor`。 请参阅**输入标记帮助器的 HTML 帮助器替代**部分了解详细信息。

* 提供强类型化。 如果属性更改和你的名称未更新标记帮助程序将收到错误类似于以下：

```HTML
An error occurred during the compilation of a resource required to process
this request. Please review the following specific error details and modify
your source code appropriately.

Type expected
 'RegisterViewModel' does not contain a definition for 'Email' and no
 extension method 'Email' accepting a first argument of type 'RegisterViewModel'
 could be found (are you missing a using directive or an assembly reference?)
```

`Input`标记帮助器设置的 HTML`type`属性基于.NET 类型。 下表列出了一些常见的.NET 类型和生成的 HTML 类型 （不是每个.NET 类型已经列出）。

|.NET 类型|输入的类型|
|---|---|
|Bool|类型 ="复选框"|
|字符串|类型 ="text"|
|DateTime|类型 ="datetime"|
|Byte|类型 ="number"|
|Int|类型 ="number"|
|Single、Double|类型 ="number"|


下表显示某些常见[数据注释](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter)输入的标记帮助器将映射到特定的输入类型 （列出不是每个验证属性） 的属性：


|特性|输入的类型|
|---|---|
|[EmailAddress]|类型 ="email"|
|[Url]|类型 ="url"|
|[HiddenInput]|类型 ="隐藏"|
|[Phone]|类型 ="电话"|
|[DataType(DataType.Password)]| 类型 ="密码"|
|[DataType(DataType.Date)]| 类型 ="日期"|
|[DataType(DataType.Time)]| 类型 ="时间"|


示例:

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[Main](working-with-forms/sample/final/Views/Demo/RegisterInput.cshtml)]

上面的代码生成以下 HTML:

```HTML
  <form method="post" action="/Demo/RegisterInput">
       Email:
       <input type="email" data-val="true"
              data-val-email="The Email Address field is not a valid e-mail address."
              data-val-required="The Email Address field is required."
              id="Email" name="Email" value="" /> <br>
       Password:
       <input type="password" data-val="true"
              data-val-required="The Password field is required."
              id="Password" name="Password" /><br>
       <button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
   </form>
```

应用于数据注释`Email`和`Password`属性生成的模型的元数据。 输入标记帮助器使用的模型元数据并生成[HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*`属性 (请参阅[模型验证](../models/validation.md))。 这些属性描述验证程序可以将附加到的输入字段。 这提供了非介入式 HTML5 和[jQuery](https://jquery.com/)验证。 非介入式属性具有格式`data-val-rule="Error Message"`，其中规则是验证规则的名称 (如`data-val-required`， `data-val-email`，`data-val-maxlength`等。)如果此属性中提供了一条错误消息，它显示的值为`data-val-rule`属性。 也有属性的窗体`data-val-ruleName-argumentName="argumentValue"`，例如，提供有关的规则的其他详细信息`data-val-maxlength-max="1024"`。

### <a name="html-helper-alternatives-to-input-tag-helper"></a>输入标记帮助器的 HTML 帮助器的替代方法

`Html.TextBox``Html.TextBoxFor`，`Html.Editor`和`Html.EditorFor`有重叠的功能与输入标记帮助器。 输入标记帮助程序将自动设置`type`属性;`Html.TextBox`和`Html.TextBoxFor`将不会。 `Html.Editor`和`Html.EditorFor`处理集合、 复杂的对象和模板; 输入标记帮助器却没有。 输入标记帮助器，`Html.EditorFor`和`Html.TextBoxFor`强类型 （它们使用 lambda 表达式）;`Html.TextBox`和`Html.Editor`不 （它们使用表达式名称）。

### <a name="htmlattributes"></a>HtmlAttributes

`@Html.Editor()`和`@Html.EditorFor()`使用特殊`ViewDataDictionary`条目名为`htmlAttributes`时执行其默认模板。 （可选） 使用扩充此行为`additionalViewData`参数。 键"htmlAttributes"是不区分大小写。 键"htmlAttributes"的方式类似于处理`htmlAttributes`输入类似的帮助程序对象传递给`@Html.TextBox()`。

```HTML
@Html.EditorFor(model => model.YourProperty, 
  new { htmlAttributes = new { @class="myCssClass", style="Width:100px" } })
```

### <a name="expression-names"></a>表达式名称

`asp-for`属性值是`ModelExpression`和 lambda 表达式的右侧。 因此，`asp-for="Property1"`变得`m => m.Property1`在生成的代码，这正是你无需前缀`Model`。 你可以使用"@"字符，以启动内联表达式并移动之前`m.`:

```HTML
@{
       var joe = "Joe";
   }
   <input asp-for="@joe" />
```

生成以下：

```HTML
<input type="text" id="joe" name="joe" value="Joe" />
```

集合属性时，与`asp-for="CollectionProperty[23].Member"`生成相同的名称`asp-for="CollectionProperty[i].Member"`时`i`具有值`23`。

### <a name="navigating-child-properties"></a>导航子属性

你还可以导航到使用视图模型的属性路径的子属性。 请考虑一个包含子级的更复杂的模型类`Address`属性。

[!code-csharp[Main](../../mvc/views/working-with-forms/sample/final/ViewModels/AddressViewModel.cs?highlight=1,2,3,4&range=5-8)]

[!code-csharp[Main](../../mvc/views/working-with-forms/sample/final/ViewModels/RegisterAddressViewModel.cs?highlight=8&range=5-13)]

在视图中，我们将绑定到`Address.AddressLine1`:

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterAddress.cshtml?highlight=6)]

为生成的以下 HTML `Address.AddressLine1`:

```HTML
<input type="text" id="Address_AddressLine1" name="Address.AddressLine1" value="" />
```

### <a name="expression-names-and-collections"></a>表达式名称和集合

示例模型包含一个数组`Colors`:

[!code-csharp[Main](../../mvc/views/working-with-forms/sample/final/ViewModels/Person.cs?highlight=3&range=5-10)]

操作方法中：

```csharp
public IActionResult Edit(int id, int colorIndex)
   {
       ViewData["Index"] = colorIndex;
       return View(GetPerson(id));
   }
```

以下 Razor 显示如何访问特定`Color`元素：

[!code-HTML[Main](working-with-forms/sample/final/Views/Demo/EditColor.cshtml)]

*Views/Shared/EditorTemplates/String.cshtml*模板：

[!code-HTML[Main](working-with-forms/sample/final/Views/Shared/EditorTemplates/String.cshtml)]

示例使用`List<T>`:

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/ToDoItem.cs?range=3-8)]

以下 Razor 演示如何循环访问集合：

[!code-HTML[Main](working-with-forms/sample/final/Views/Demo/Edit.cshtml)]

*Views/Shared/EditorTemplates/ToDoItem.cshtml*模板：

[!code-HTML[Main](working-with-forms/sample/final/Views/Shared/EditorTemplates/ToDoItem.cshtml)]


>[!NOTE]
>始终使用`for`(和*不* `foreach`) 以循环访问列表。 计算在 LINQ 中的索引器表达式可能很昂贵，并应将最小化。

&nbsp;

>[!NOTE]
>上面的注释的示例代码演示如何将替换放在 lambda 表达式的`@`运算符来访问每个`ToDoItem`列表中。

## <a name="the-textarea-tag-helper"></a>Textarea 标记帮助器

`Textarea Tag Helper`标记帮助程序是类似于输入标记帮助器。

* 生成`id`和`name`特性和的模型中的数据验证属性[ \<textarea >](https://www.w3.org/wiki/HTML/Elements/textarea)元素。

* 提供强类型化。

* HTML 帮助器的替代项：`Html.TextAreaFor`

示例:

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/DescriptionViewModel.cs)]

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterTextArea.cshtml?highlight=4)]

生成的以下 HTML:

```HTML
<form method="post" action="/Demo/RegisterTextArea">
  <textarea data-val="true"
   data-val-maxlength="The field Description must be a string or array type with a maximum length of &#x27;1024&#x27;."
   data-val-maxlength-max="1024"
   data-val-minlength="The field Description must be a string or array type with a minimum length of &#x27;5&#x27;."
   data-val-minlength-min="5"
   id="Description" name="Description">
  </textarea>
  <button type="submit">Test</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
</form>
```

## <a name="the-label-tag-helper"></a>标签标记帮助器

* 生成标签标题和`for`属性[ <label> ](https://www.w3.org/wiki/HTML/Elements/label)表达式名称的元素

* HTML 帮助器的替代项： `Html.LabelFor`。

`Label Tag Helper`在纯 HTML 标签元素提供了以下好处：

* 自动获取描述性标签值`Display`属性。 预期的显示名称可能会更改通过时间和的组合`Display`属性和标签标记帮助程序将应用`Display`无处不在使用它。

* 在源代码中较少标记

* 强类型化与模型属性。

示例:

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/SimpleViewModel.cs)]

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterLabel.cshtml?highlight=4)]

为生成的以下 HTML`<label>`元素：

```HTML
<label for="Email">Email Address</label>
```

标签标记帮助器生成`for`"Email"，是的 ID 属性值与关联`<input>`元素。 标记帮助程序生成一致`id`和`for`元素以便能够正确关联。 在此示例中的标题来自`Display`属性。 如果该模型未包含`Display`属性标题将该表达式的属性名称。

## <a name="the-validation-tag-helpers"></a>验证标记帮助程序

有两个验证标记帮助程序。 `Validation Message Tag Helper` （这会显示一条验证消息对单个属性模型上）、 和`Validation Summary Tag Helper`（它会显示验证错误的摘要）。 `Input Tag Helper`将 HTML5 客户端端验证属性输入元素基于数据模型类上的批注属性添加。 此外在服务器上执行验证。 发生验证错误时，验证标记帮助器将显示这些错误消息。

### <a name="the-validation-message-tag-helper"></a>验证消息标记帮助器

* 将添加[HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-valmsg-for="property"`属性设为[跨越](https://developer.mozilla.org/docs/Web/HTML/Element/span)元素，它将附加在指定的模型属性的输入字段上的验证错误消息。 客户端验证错误时， [jQuery](https://jquery.com/)显示中的错误消息`<span>`元素。

* 验证还会在服务器。 客户端可能已禁用 JavaScript，仅可以在服务器端执行一些验证。

* HTML 帮助器的替代项：`Html.ValidationMessageFor`

`Validation Message Tag Helper`用于`asp-validation-for`上对 HTML 特性[跨越](https://developer.mozilla.org/docs/Web/HTML/Element/span)元素。

```HTML
<span asp-validation-for="Email"></span>
```

验证消息标记帮助器将生成以下 HTML:

```HTML
<span class="field-validation-valid"
  data-valmsg-for="Email"
  data-valmsg-replace="true"></span>
```

通常使用`Validation Message Tag Helper`后`Input`为相同属性的标记帮助器。 这样显示附近的输入将导致错误的任何验证错误消息。

> [!NOTE]
> 你必须具有正确的 JavaScript 的视图和[jQuery](https://jquery.com/)脚本中的客户端验证准备好的引用。 请参阅[模型验证](../models/validation.md)有关详细信息。

（例如当你已自定义服务器端验证或客户端验证已禁用） 服务器端验证错误时，MVC 中放置该错误消息的正文`<span>`元素。

```HTML
<span class="field-validation-error" data-valmsg-for="Email"
            data-valmsg-replace="true">
   The Email Address field is required.
</span>
```

### <a name="the-validation-summary-tag-helper"></a>验证摘要标记帮助器

* 目标`<div>`元素`asp-validation-summary`属性

* HTML 帮助器的替代项：`@Html.ValidationSummary`

`Validation Summary Tag Helper`用于显示的验证消息的摘要。 `asp-validation-summary`属性值可以是任何以下：

|asp 验证摘要|显示的验证消息|
|--- |--- |
|ValidationSummary.All|属性和模型级别|
|ValidationSummary.ModelOnly|模型|
|ValidationSummary.None|无|

### <a name="sample"></a>示例

在下面的示例中，数据模型用修饰`DataAnnotation`生成验证错误消息的属性`<input>`元素。  出现验证错误时，验证标记帮助器显示的错误消息：

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterValidation.cshtml?highlight=4,6,8&range=1-10)]

生成的 HTML （如果该模型为有效）：

```HTML
<form action="/DemoReg/Register" method="post">
  <div class="validation-summary-valid" data-valmsg-summary="true">
  <ul><li style="display:none"></li></ul></div>
  Email:  <input name="Email" id="Email" type="email" value=""
   data-val-required="The Email field is required."
   data-val-email="The Email field is not a valid e-mail address."
   data-val="true"> <br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Email"></span><br>
  Password: <input name="Password" id="Password" type="password"
   data-val-required="The Password field is required." data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Password"></span><br>
  <button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
</form>
```

## <a name="the-select-tag-helper"></a>选择标记帮助器

* 生成[选择](https://www.w3.org/wiki/HTML/Elements/select)和关联[选项](https://www.w3.org/wiki/HTML/Elements/option)模型属性的元素。

* 具有备用的 HTML 帮助器`Html.DropDownListFor`和`Html.ListBoxFor`

`Select Tag Helper` `asp-for`指定的模型属性名称[选择](https://www.w3.org/wiki/HTML/Elements/select)元素和`asp-items`指定[选项](https://www.w3.org/wiki/HTML/Elements/option)元素。  例如: 

[!code-HTML[Main](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

示例:

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/CountryViewModel.cs)]

`Index`方法初始化`CountryViewModel`、 设置所选国家/地区和将其传递给`Index`视图。

[!code-csharp[Main](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

HTTP POST`Index`方法会显示所选内容：

[!code-csharp[Main](working-with-forms/sample/final/Controllers/HomeController.cs?range=15-27)]

`Index`视图：

[!code-cshtml[Main](working-with-forms/sample/final/Views/Home/Index.cshtml?highlight=4)]

这将生成以下 html 代码虽然 （使用"CA"选择):

```html
<form method="post" action="/">
     <select id="Country" name="Country">
       <option value="MX">Mexico</option>
       <option selected="selected" value="CA">Canada</option>
       <option value="US">USA</option>
     </select>
       <br /><button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
   </form>
```

> [!NOTE]
> 我们不建议使用`ViewBag`或`ViewData`与选择标记帮助器。 视图模型是在提供 MVC 元数据更加可靠和通常更不容易出错。

`asp-for`属性值是一种特殊情况，不要求`Model`前缀，其他标记帮助器属性那样 (如`asp-items`)

[!code-HTML[Main](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

### <a name="enum-binding"></a>枚举绑定

通常可以方便地使用`<select>`与`enum`属性并生成`SelectListItem`元素从`enum`值。

示例:

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/CountryEnumViewModel.cs?range=3-7)]

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/CountryEnum.cs)]

`GetEnumSelectList`方法生成`SelectList`枚举的对象。

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEnum.cshtml?highlight=5)]

你可以按照声明你使用的枚举器列表`Display`属性以获取更丰富的 UI:

[!code-csharp[Main](working-with-forms/sample/final/ViewModels/CountryEnum.cs?highlight=5,7)]

生成的以下 HTML:

```HTML
  <form method="post" action="/Home/IndexEnum">
         <select data-val="true" data-val-required="The EnumCountry field is required."
                 id="EnumCountry" name="EnumCountry">
             <option value="0">United Mexican States</option>
             <option value="1">United States of America</option>
             <option value="2">Canada</option>
             <option value="3">France</option>
             <option value="4">Germany</option>
             <option selected="selected" value="5">Spain</option>
         </select>
         <br /><button type="submit">Register</button>
         <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
    </form>
```

### <a name="option-group"></a>选项组

HTML [ \<optgroup >](https://www.w3.org/wiki/HTML/Elements/optgroup)视图模型包含一个或多时，会生成元素`SelectListGroup`对象。

`CountryViewModelGroup`组`SelectListItem`"北美"和"Europe"分组的元素：

[!code-csharp[Main](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelGroup.cs?highlight=5,6,14,20,26,32,38,44&range=6-56)]

下面显示了两个组：

![选项组示例](working-with-forms/_static/grp.png)

生成的 HTML 中：

```HTML
 <form method="post" action="/Home/IndexGroup">
      <select id="Country" name="Country">
          <optgroup label="North America">
              <option value="MEX">Mexico</option>
              <option value="CAN">Canada</option>
              <option value="US">USA</option>
          </optgroup>
          <optgroup label="Europe">
              <option value="FR">France</option>
              <option value="ES">Spain</option>
              <option value="DE">Germany</option>
          </optgroup>
      </select>
      <br /><button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
 </form>
```

### <a name="multiple-select"></a>多个选择

选择标记帮助程序将自动生成[多个 ="多个"](http://w3c.github.io/html-reference/select.html)属性中指定的属性如果`asp-for`属性是`IEnumerable`。 例如，给定以下模型：

[!code-csharp[Main](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelIEnumerable.cs?highlight=6)]

使用以下视图：

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexMultiSelect.cshtml?highlight=4)]

生成的以下 HTML:

```HTML
<form method="post" action="/Home/IndexMultiSelect">
    <select id="CountryCodes"
    multiple="multiple"
    name="CountryCodes"><option value="MX">Mexico</option>
<option value="CA">Canada</option>
<option value="US">USA</option>
<option value="FR">France</option>
<option value="ES">Spain</option>
<option value="DE">Germany</option>
</select>
    <br /><button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
</form>
```

### <a name="no-selection"></a>没有选定内容

如果你发现自己使用多个页中的"未指定"选项，你可以创建一个模板以消除重复 HTML:

[!code-HTML[Main](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEmptyTemplate.cshtml?highlight=5)]

*Views/Shared/EditorTemplates/CountryViewModel.cshtml*模板：

[!code-HTML[Main](working-with-forms/sample/final/Views/Shared/EditorTemplates/CountryViewModel.cshtml)]

添加 HTML [\<选项 >](https://www.w3.org/wiki/HTML/Elements/option)元素并不局限于*没有选定内容*用例。 例如，以下视图和操作方法将生成 HTML 类似于上面的代码：

[!code-csharp[Main](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

[!code-HTML[Main](working-with-forms/sample/final/Views/Home/IndexOption.cshtml)]

正确`<option>`将选择的元素 (包含`selected="selected"`属性) 具体取决于当前`Country`值。

```HTML
 <form method="post" action="/Home/IndexEmpty">
      <select id="Country" name="Country">
          <option value="">&lt;none&gt;</option>
          <option value="MX">Mexico</option>
          <option value="CA" selected="selected">Canada</option>
          <option value="US">USA</option>
      </select>
      <br /><button type="submit">Register</button>
   <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>" />
 </form>
 ```

## <a name="additional-resources"></a>其他资源

* [标记帮助程序](tag-helpers/intro.md)

* [HTML 窗体元素](https://www.w3.org/TR/html401/interact/forms.html)

* [请求验证令牌](https://docs.microsoft.com/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)

* [模型绑定](../models/model-binding.md)

* [模型验证](../models/validation.md)

* [数据注释](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter)

* [代码片段此文档](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/forms/sample)。
