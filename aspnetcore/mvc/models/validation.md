---
title: "ASP.NET 核心 mvc 模型验证"
author: rachelappel
description: "了解有关 ASP.NET 核心 mvc 模型验证。"
keywords: "ASP.NET 核心，MVC，验证"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 3a8676dd-7ed8-4a05-bca2-44e288ab99ee
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/models/validation
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a3f3f7010d7744d59ce2dd88b323418423b3ae08
ms.sourcegitcommit: 9ecd4e9fb0c40c3693dab079eab1ff94b461c922
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2017
---
# <a name="introduction-to-model-validation-in-aspnet-core-mvc"></a>ASP.NET 核心 mvc 模型验证简介

通过[Rachel Appel](https://github.com/rachelappel)

## <a name="introduction-to-model-validation"></a>模型验证简介

应用程序在数据库中存储数据之前，应用程序必须验证数据。 必须检查潜在的安全威胁，验证相应地设置格式的类型和大小，并且它必须符合规则的数据。 验证是必需的但也可以是冗余且单调乏味实现。 在 MVC 中，验证在客户端和服务器上发生。

幸运的是，.NET 已提取到验证特性的验证。 这些属性包含验证代码，从而减少了必须编写的代码量。

[查看或从 GitHub 下载示例](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/validation/sample)。

## <a name="validation-attributes"></a>验证特性

验证特性是一种方法配置模型验证，因此从概念上讲类似验证对数据库表中的字段。 这包括如将数据类型或必填的字段分配的约束。 其他类型的验证包括将模式应用于数据以强制实施业务规则，如信用卡号，电话号码或电子邮件地址。 验证特性使强制实施更简单、 更轻松地使用这些要求。

下面是带批注`Movie`模型从的应用程序存储有关电影和电视节目的信息。 大多数属性都需要和多个字符串属性具有长度要求。 此外，没有在此处的数值范围限制`Price`从 0 到 $999.99，以及自定义验证特性的属性。

[!code-csharp[Main](validation/sample/Movie.cs?range=6-29)]

只需读取通过模型将显示有关此应用程序，从而更便于维护代码的数据的规则。 下面是几个常用的内置验证特性：

* `[CreditCard]`： 验证该属性具有信用卡格式。

* `[Compare]`： 验证在模型匹配的两个属性。

* `[EmailAddress]`： 验证该属性具有电子邮件格式。

* `[Phone]`： 验证该属性具有电话格式。

* `[Range]`： 验证该属性值降到给定范围内。

* `[RegularExpression]`： 验证的数据与指定的正则表达式匹配。

* `[Required]`： 使要求的属性。

* `[StringLength]`： 验证字符串属性最多包含给定的最大长度。

* `[Url]`： 验证该属性具有的 URL 格式。

MVC 支持任何特性的派生自`ValidationAttribute`来进行验证。 在找不到许多有用的验证属性[System.ComponentModel.DataAnnotations](https://docs.microsoft.com/dotnet/api/system.componentmodel.dataannotations)命名空间。

可能需要更多的功能比内置属性提供的实例。 对于这些时间中，你可以创建自定义验证特性的派生自`ValidationAttribute`或更改您的模型来实现`IValidatableObject`。

## <a name="notes-on-the-use-of-the-required-attribute"></a>必需的特性的使用说明

从本质上来说，需要不可以为 null 的[值类型](/dotnet/csharp/language-reference/keywords/value-types)（如 `decimal`、`int`、`float` 和 `DateTime`），但不需要 `Required` 特性。 该应用程序执行的不可以为 null 的类型的标记的任何服务器端验证检查`Required`。

MVC 模型绑定，并不关心的验证和验证特性，拒绝包含缺少的值或非 null 的类型的空白窗体字段提交。 在缺少`BindRequired`属性上的目标属性中，模型绑定忽略缺少数据不可为 null 的类型，其中的表单域不存在从传入的窗体数据。

[BindRequired 属性](/aspnet/core/api/microsoft.aspnetcore.mvc.modelbinding.bindrequiredattribute)(另请参阅[自定义模型具有属性的绑定行为](xref:mvc/models/model-binding#customize-model-binding-behavior-with-attributes)) 有助于确保窗体数据是否完整。 当应用于一个属性，以及模型绑定系统将需要该属性的值。 应用于类型时，模型绑定系统将要求提供的所有该类型的属性的值。

当你使用[可以为 Null\<T > 类型](/dotnet/csharp/programming-guide/nullable-types/)(例如，`decimal?`或`System.Nullable<decimal>`) 并将其标记`Required`，就像该属性是标准的可以为 null 类型 （对于执行服务器端验证检查示例中， `string`)。

客户端验证对应于一个模型属性，已标记为窗体字段需要值`Required`和尚未标记为不可为 null 的类型属性`Required`。 `Required`可以用于控制客户端验证错误消息。

## <a name="model-state"></a>模型状态

模型状态表示提交 HTML 窗体值中的验证错误。

MVC 将继续验证域，直到达到最大错误 (默认情况下为 200) 数。 你可以配置该数字的方法是插入下面的代码插入`ConfigureServices`中的方法*Startup.cs*文件：

[!code-csharp[Main](validation/sample/Startup.cs?range=27)]

## <a name="handling-model-state-errors"></a>处理模型状态错误

在正在调用的每个控制器操作之前，将执行模型验证和操作方法负责检查`ModelState.IsValid`并相应地作出反应。 在许多情况下，正确的反应是返回某种类型的错误响应，理想情况下详细说明模型验证失败的原因。

某些应用程序会选择遵守标准的约定来处理模型验证错误，在该情况下筛选器可能会为适当的位置，若要实现此类策略。 你应测试你的操作与有效和无效的模型状态的行为方式。

## <a name="manual-validation"></a>手动验证

模型绑定和验证完成后，你可能需要重复它的部分。 例如，用户可能具有文本在字段中输入应为整数，或可能需要计算模型的属性的值。

你可能需要手动运行验证。 为此，请调用`TryValidateModel`方法，如下所示：

[!code-csharp[Main](validation/sample/MoviesController.cs?range=52)]

## <a name="custom-validation"></a>自定义验证

验证特性适用于大多数验证需求。 但是，一些的验证规则是特定于你的业务，它们不只是一般数据验证如确保字段是必需的或符合到一系列的值。 对于这些情况下，自定义验证的特性是不错的解决方案。 在 MVC 创建你自己的自定义验证特性很容易。 只需从继承`ValidationAttribute`，并重写`IsValid`方法。 `IsValid`方法接受两个参数，第一种是已命名的对象*值*第二项是`ValidationContext`对象名为*validationContext*。 *值*从验证你的自定义验证程序的字段引用的实际值。

在下面的示例中，业务规则规定，用户不可能设置流派为*经典*对于影片之后 1960年发布。 `[ClassicMovie]`属性会首先，检查流派，如果它是标准，然后它检查发布日期是否晚于 1960年。 如果它将被释放后 1960年，验证将失败。 此属性接受一个表示可用于验证数据的年份的整数参数。 你可以捕获该特性的构造函数中参数的值，如下所示：

[!code-csharp[Main](validation/sample/ClassicMovieAttribute.cs?range=9-29)]

`movie`变量表示上面`Movie`包含窗体提交以验证中的数据的对象。 在这种情况下，验证代码检查的日期和中的流派`IsValid`方法`ClassicMovieAttribute`根据规则类。 在成功地验证`IsValid`返回`ValidationResult.Success`代码，并在验证失败时，`ValidationResult`并显示错误消息。 当用户修改`Genre`字段并提交该表单，`IsValid`方法`ClassicMovieAttribute`将验证电影是否为标准。 与任何内置属性，类似应用`ClassicMovieAttribute`到属性，如`ReleaseDate`以确保验证执行，如前面的代码示例中所示。 由于此示例仅处理`Movie`类型，更好的选择是使用`IValidatableObject`下面这一段中所示。

或者，这一相同代码无法放置在模型中通过实现`Validate`方法`IValidatableObject`接口。 当自定义验证特性适用于验证单独的属性时，实现`IValidatableObject`可以用来实现类级别验证，如下所示。

[!code-csharp[Main](validation/sample/MovieIValidatable.cs?range=32-40)]

## <a name="client-side-validation"></a>客户端验证

客户端验证是一个重大便利的用户。 它将保存它们需要花费时间的时间等待往返行程到服务器。 在业务术语中，甚至几秒的乘积数百次每一天的秒的小数部分将添加最多会导致大量时间，费用，并减少失败。 简单和快速验证使用户能够更有效地工作，并生成更好的质量输入和输出。

你必须具有正确的 JavaScript 脚本引用的视图中的客户端验证工作如下所示准备好。

[!code-cshtml[Main](validation/sample/Views/Shared/_Layout.cshtml?range=37)]

[!code-cshtml[Main](validation/sample/Views/Shared/_ValidationScriptsPartial.cshtml)]

[JQuery 非介入式验证](https://github.com/aspnet/jquery-validation-unobtrusive)脚本是基于流行的自定义 Microsoft 前端库[jQuery 验证](https://jqueryvalidation.org/)插件。 如果没有 jQuery 非介入式验证，你将必须在两个位置相同的验证逻辑的代码： 一次是在服务器端验证属性对模型属性，然后再次在客户端脚本 (jQuery 验证的示例[ `validate()`](https://jqueryvalidation.org/validate/)方法演示如何复杂可能会很大)。 相反，MVC 的[标记帮助程序](xref:mvc/views/tag-helpers/intro)和[HTML 帮助器](xref:mvc/views/overview)能够使用验证属性和类型元数据的模型属性呈现 HTML 5[数据特性](http://w3c.github.io/html/dom.html#embedding-custom-non-visible-data-with-the-data-attributes)中需要验证窗体元素。 MVC 生成`data-`内置和自定义属性的属性。 jQuery 非介入式验证然后分析这些注册表`data-`属性并将逻辑传递给 jQuery 验证，有效地"复制"服务器端验证逻辑到客户端。 你可以使用如下所示的相关标记帮助器客户端上显示验证错误：

[!code-cshtml[Main](validation/sample/Views/Movies/Create.cshtml?highlight=4,5&range=19-25)]

上面的标记帮助器呈现的 HTML 下面。 请注意， `data-` HTML 中的属性输出对应的验证属性`ReleaseDate`属性。 `data-val-required`下面的属性包含要显示如果用户未填满发行日期字段中的错误消息。 jQuery 非介入式验证将此值传递给 jQuery 验证[ `required()` ](https://jqueryvalidation.org/required-method/)方法，然后在随附的说明将显示该消息**\<跨越 >**元素。

```html
<form action="/Movies/Create" method="post">
    <div class="form-horizontal">
        <h4>Movie</h4>
        <div class="text-danger"></div>
        <div class="form-group">
            <label class="col-md-2 control-label" for="ReleaseDate">ReleaseDate</label>
            <div class="col-md-10">
                <input class="form-control" type="datetime"
                data-val="true" data-val-required="The ReleaseDate field is required."
                id="ReleaseDate" name="ReleaseDate" value="" />
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

因此，客户端验证防止提交，直到该窗体是有效。 提交按钮运行 JavaScript 提交窗体或显示错误消息。

MVC 确定基于.NET 数据类型的属性，可能使用重写的类型属性值`[DataType]`属性。 基`[DataType]`属性不会实际的服务器端验证。 浏览器选择自己的错误消息，并显示这些错误，但他们希望，但是 jQuery 验证非介入式包可以替代的消息，并与其他一致地显示它们。 发生这种情况最明显时用户应用`[DataType]`如子类`[EmailAddress]`。

### <a name="adding-validation-to-dynamic-forms"></a>将验证添加到动态窗体：

因为第一次加载页面时项目，jQuery 非介入式验证都会将验证逻辑和参数传递到 jQuery 验证中，则动态生成的窗体将不会自动出现验证。 相反，你必须告知 jQuery 非介入式验证要在创建后立即分析动态窗体。 例如，下面的代码演示可能设置通过 AJAX 添加窗体上的客户端验证的方式。

```js
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Could not add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

`$.validator.unobtrusive.parse()`方法接受它的一个参数的 jQuery 选择器。 此方法指示 jQuery 非介入式验证分析`data-`该选择器中的窗体的属性。 这些特性的值然后传递给 jQuery 验证插件中，这样窗体表现出所需的客户端验证规则。

### <a name="adding-validation-to-dynamic-controls"></a>将验证添加到动态控件：

你还可以更新窗体上的验证规则，当单个控件，如`<input/>`s 和`<select/>`s，动态生成。 不能将传递到这些元素选择器`parse()`方法直接因为周围的窗体已分析并不会更新。  相反，你首先删除现有的验证数据，然后重新分析整个窗体，如下所示：

```js
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Could not add form. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        form.removeData("validator")    // Added by the raw jQuery Validate
            .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="iclientmodelvalidator"></a>IClientModelValidator

可能你的自定义特性，创建客户端逻辑和[非介入式验证](http://jqueryvalidation.org/documentation/)将为你自动作为一部分验证客户端上执行。 第一步是控制哪些数据特性添加通过实现`IClientModelValidator`接口如下所示：

[!code-csharp[Main](validation/sample/ClassicMovieAttribute.cs?range=30-42)]

实现此接口的属性可以将 HTML 特性添加到生成的字段。 检查的输出`ReleaseDate`元素显示非常相似与前面的示例中，但现在没有的 HTML`data-val-classicmovie`属性中定义`AddValidation`方法`IClientModelValidator`。

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="" />
```

非介入式验证将使用中的数据`data-`属性来显示错误消息。 但是，jQuery 不知道有关规则或消息之前将它们添加到 jQuery 的`validator`对象。 这下面添加一个名为方法的示例中所示`classicmovie`包含自定义客户端验证代码，以便 jQuery`validator`对象。

[!code-javascript[Main](validation/sample/Views/Movies/Create.cshtml?range=71-93)]

现在 jQuery 具有的信息执行自定义 JavaScript 验证，以及要显示如果该验证代码返回的错误消息。

## <a name="remote-validation"></a>远程验证

远程验证是很有用的功能，你需要验证服务器上的数据对客户端上的数据时要使用。 例如，你的应用程序可能需要验证是否电子邮件或用户的名称已在使用，并且它必须查询大量的数据进行管理。 下载大型数据集的用于验证一个或几个字段会占用过多资源。 它还可以公开敏感信息。 一种替代方法是发出往返的请求，以验证字段。

你可以在一个双步骤过程中实现远程验证。 首先，必须批注与模型`[Remote]`属性。 `[Remote]`属性接受多个重载可用于将定向到相应的代码以调用的客户端 JavaScript。 下面的示例指向`VerifyEmail`操作方法`Users`控制器。

[!code-csharp[Main](validation/sample/User.cs?range=7-8)]

中定义的第二步将验证代码放在相应的操作方法`[Remote]`属性。 根据 jQuery 验证[ `remote()` ](https://jqueryvalidation.org/remote-method/)方法文档：

> Serverside 响应必须是必须的 JSON 字符串`"true"`对于有效的元素，并且可以`"false"`， `undefined`，或`null`无效元素，使用默认的错误消息。 如果 serverside 响应是一个字符串，例如。 `"That name is already taken, try peter123 instead"`此字符串将显示为以取代默认的自定义错误消息。

定义`VerifyEmail()`方法遵循这些规则，如下所示。 它将返回验证错误消息如果采用电子邮件，或`true`如果电子邮件是免费的并将包装中的结果`JsonResult`对象。 然后，客户端可以使用返回的值以继续，或如果需要显示错误。

[!code-csharp[Main](validation/sample/UsersController.cs?range=19-28)]

现在当用户输入的电子邮件，在视图中的 JavaScript 进行远程调用以查看该电子邮件已并且，如果是这样，显示的错误消息。 否则，用户可以像往常一样提交窗体。

`AdditionalFields`属性`[Remote]`属性可用于验证对服务器上的数据的字段的组合。  例如，如果`User`上面提供的模型有两个调用的其他属性`FirstName`和`LastName`，你可能想要验证是否没有现有的用户已有的名称配对。  下面的代码中所示，你可以定义新属性：

[!code-csharp[Main](validation/sample/User.cs?range=10-13)]

`AdditionalFields`无法显式设置为字符串`"FirstName"`和`"LastName"`，但使用[ `nameof` ](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/nameof)如下运算符简化了更高版本重构。  要执行验证的操作方法然后必须接受两个自变量，其中一个的值的`FirstName`，另一个的值用于`LastName`。


[!code-csharp[Main](validation/sample/UsersController.cs?range=30-39)]

现在当用户输入的第一个和最后一个名称，JavaScript:

* 进行远程调用以查看是否已采用了该对的名称。
* 如果已对，将显示一条错误消息。 
* 如果不采取，用户可以提交窗体。

如果你需要验证具有两个或多个其他字段`[Remote]`属性，您为他们提供以逗号分隔的列表。  例如，若要添加`MiddleName`属性设置为模型中，设`[Remote]`特性，如以下代码所示：

```cs
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

`AdditionalFields`类似于将所有属性变量，必须是常量表达式。  因此，你必须使用[内插字符串](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/interpolated-strings)或调用[ `string.Join()` ](https://msdn.microsoft.com/en-us/library/system.string.join(v=vs.110).aspx)初始化`AdditionalFields`。 你将添加到每个其他字段`[Remote]`属性，必须将另一个自变量添加到相应的控制器操作方法。
