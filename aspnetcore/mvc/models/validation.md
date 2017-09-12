---
title: "ASP.NET 核心 mvc 模型验证"
author: rick-anderson
description: "引入了 ASP.NET 核心 mvc 模型验证。"
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
ms.openlocfilehash: be130c24f5baf643a4c9493a33ec45bdd4cc66ed
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="introduction-to-model-validation-in-aspnet-core-mvc"></a><span data-ttu-id="0f356-104">ASP.NET 核心 mvc 模型验证简介</span><span class="sxs-lookup"><span data-stu-id="0f356-104">Introduction to model validation in ASP.NET Core MVC</span></span>

<span data-ttu-id="0f356-105">通过[Rachel Appel](https://github.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="0f356-105">By [Rachel Appel](https://github.com/rachelappel)</span></span>

## <a name="introduction-to-model-validation"></a><span data-ttu-id="0f356-106">模型验证简介</span><span class="sxs-lookup"><span data-stu-id="0f356-106">Introduction to model validation</span></span>

<span data-ttu-id="0f356-107">应用程序在数据库中存储数据之前，应用程序必须验证数据。</span><span class="sxs-lookup"><span data-stu-id="0f356-107">Before an app stores data in a database, the app must validate the data.</span></span> <span data-ttu-id="0f356-108">必须检查潜在的安全威胁，验证相应地设置格式的类型和大小，并且它必须符合规则的数据。</span><span class="sxs-lookup"><span data-stu-id="0f356-108">Data must be checked for potential security threats, verified that it is appropriately formatted by type and size, and it must conform to your rules.</span></span> <span data-ttu-id="0f356-109">验证是必需的但也可以是冗余且单调乏味实现。</span><span class="sxs-lookup"><span data-stu-id="0f356-109">Validation is necessary although it can be redundant and tedious to implement.</span></span> <span data-ttu-id="0f356-110">在 MVC 中，验证在客户端和服务器上发生。</span><span class="sxs-lookup"><span data-stu-id="0f356-110">In MVC, validation happens on both the client and server.</span></span>

<span data-ttu-id="0f356-111">幸运的是，.NET 已提取到验证特性的验证。</span><span class="sxs-lookup"><span data-stu-id="0f356-111">Fortunately, .NET has abstracted validation into validation attributes.</span></span> <span data-ttu-id="0f356-112">这些属性包含验证代码，从而减少了必须编写的代码量。</span><span class="sxs-lookup"><span data-stu-id="0f356-112">These attributes contain validation code, thereby reducing the amount of code you must write.</span></span>

<span data-ttu-id="0f356-113">[查看或从 GitHub 下载示例](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/validation/sample)。</span><span class="sxs-lookup"><span data-stu-id="0f356-113">[View or download sample from GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/validation/sample).</span></span>

## <a name="validation-attributes"></a><span data-ttu-id="0f356-114">验证特性</span><span class="sxs-lookup"><span data-stu-id="0f356-114">Validation Attributes</span></span>

<span data-ttu-id="0f356-115">验证特性是一种方法配置模型验证，因此从概念上讲类似验证对数据库表中的字段。</span><span class="sxs-lookup"><span data-stu-id="0f356-115">Validation attributes are a way to configure model validation so it's similar conceptually to validation on fields in database tables.</span></span> <span data-ttu-id="0f356-116">这包括如将数据类型或必填的字段分配的约束。</span><span class="sxs-lookup"><span data-stu-id="0f356-116">This includes constraints such as assigning data types or required fields.</span></span> <span data-ttu-id="0f356-117">其他类型的验证包括将模式应用于数据以强制实施业务规则，如信用卡号，电话号码或电子邮件地址。</span><span class="sxs-lookup"><span data-stu-id="0f356-117">Other types of validation include applying patterns to data to enforce business rules, such as a credit card, phone number, or email address.</span></span> <span data-ttu-id="0f356-118">验证特性使强制实施更简单、 更轻松地使用这些要求。</span><span class="sxs-lookup"><span data-stu-id="0f356-118">Validation attributes make enforcing these requirements much simpler and easier to use.</span></span>

<span data-ttu-id="0f356-119">下面是带批注`Movie`模型从的应用程序存储有关电影和电视节目的信息。</span><span class="sxs-lookup"><span data-stu-id="0f356-119">Below is an annotated `Movie` model from an app that stores information about movies and TV shows.</span></span> <span data-ttu-id="0f356-120">大多数属性都需要和多个字符串属性具有长度要求。</span><span class="sxs-lookup"><span data-stu-id="0f356-120">Most of the properties are required and several string properties have length requirements.</span></span> <span data-ttu-id="0f356-121">此外，没有在此处的数值范围限制`Price`从 0 到 $999.99，以及自定义验证特性的属性。</span><span class="sxs-lookup"><span data-stu-id="0f356-121">Additionally, there is a numeric range restriction in place for the `Price` property from 0 to $999.99, along with a custom validation attribute.</span></span>

<span data-ttu-id="0f356-122">[!code-csharp[Main](validation/sample/Movie.cs?range=6-31)]</span><span class="sxs-lookup"><span data-stu-id="0f356-122">[!code-csharp[Main](validation/sample/Movie.cs?range=6-31)]</span></span>

<span data-ttu-id="0f356-123">只需读取通过模型将显示有关此应用程序，从而更便于维护代码的数据的规则。</span><span class="sxs-lookup"><span data-stu-id="0f356-123">Simply reading through the model reveals the rules about data for this app, making it easier to maintain the code.</span></span> <span data-ttu-id="0f356-124">下面是几个常用的内置验证特性：</span><span class="sxs-lookup"><span data-stu-id="0f356-124">Below are several popular built-in validation attributes:</span></span>

* <span data-ttu-id="0f356-125">`[CreditCard]`： 验证该属性具有信用卡格式。</span><span class="sxs-lookup"><span data-stu-id="0f356-125">`[CreditCard]`: Validates the property has a credit card format.</span></span>

* <span data-ttu-id="0f356-126">`[Compare]`： 验证在模型匹配的两个属性。</span><span class="sxs-lookup"><span data-stu-id="0f356-126">`[Compare]`: Validates two properties in a model match.</span></span>

* <span data-ttu-id="0f356-127">`[EmailAddress]`： 验证该属性具有电子邮件格式。</span><span class="sxs-lookup"><span data-stu-id="0f356-127">`[EmailAddress]`: Validates the property has an email format.</span></span>

* <span data-ttu-id="0f356-128">`[Phone]`： 验证该属性具有电话格式。</span><span class="sxs-lookup"><span data-stu-id="0f356-128">`[Phone]`: Validates the property has a telephone format.</span></span>

* <span data-ttu-id="0f356-129">`[Range]`： 验证该属性值降到给定范围内。</span><span class="sxs-lookup"><span data-stu-id="0f356-129">`[Range]`: Validates the property value falls within the given range.</span></span>

* <span data-ttu-id="0f356-130">`[RegularExpression]`： 验证的数据与指定的正则表达式匹配。</span><span class="sxs-lookup"><span data-stu-id="0f356-130">`[RegularExpression]`: Validates that the data matches the specified regular expression.</span></span>

* <span data-ttu-id="0f356-131">`[Required]`： 使要求的属性。</span><span class="sxs-lookup"><span data-stu-id="0f356-131">`[Required]`: Makes a property required.</span></span>

* <span data-ttu-id="0f356-132">`[StringLength]`： 验证字符串属性最多包含给定的最大长度。</span><span class="sxs-lookup"><span data-stu-id="0f356-132">`[StringLength]`: Validates that a string property has at most the given maximum length.</span></span>

* <span data-ttu-id="0f356-133">`[Url]`： 验证该属性具有的 URL 格式。</span><span class="sxs-lookup"><span data-stu-id="0f356-133">`[Url]`: Validates the property has a URL format.</span></span>

<span data-ttu-id="0f356-134">MVC 支持任何特性的派生自`ValidationAttribute`来进行验证。</span><span class="sxs-lookup"><span data-stu-id="0f356-134">MVC supports any attribute that derives from `ValidationAttribute` for validation purposes.</span></span> <span data-ttu-id="0f356-135">在找不到许多有用的验证属性[System.ComponentModel.DataAnnotations](https://docs.microsoft.com/dotnet/api/system.componentmodel.dataannotations)命名空间。</span><span class="sxs-lookup"><span data-stu-id="0f356-135">Many useful validation attributes can be found in the [System.ComponentModel.DataAnnotations](https://docs.microsoft.com/dotnet/api/system.componentmodel.dataannotations) namespace.</span></span>

<span data-ttu-id="0f356-136">可能需要更多的功能比内置属性提供的实例。</span><span class="sxs-lookup"><span data-stu-id="0f356-136">There may be instances where you need more features than built-in attributes provide.</span></span> <span data-ttu-id="0f356-137">对于这些时间中，你可以创建自定义验证特性的派生自`ValidationAttribute`或更改您的模型来实现`IValidatableObject`。</span><span class="sxs-lookup"><span data-stu-id="0f356-137">For those times, you can create custom validation attributes by deriving from `ValidationAttribute` or changing your model to implement `IValidatableObject`.</span></span>

## <a name="model-state"></a><span data-ttu-id="0f356-138">模型状态</span><span class="sxs-lookup"><span data-stu-id="0f356-138">Model State</span></span>

<span data-ttu-id="0f356-139">模型状态表示提交 HTML 窗体值中的验证错误。</span><span class="sxs-lookup"><span data-stu-id="0f356-139">Model state represents validation errors in submitted HTML form values.</span></span>

<span data-ttu-id="0f356-140">MVC 将继续验证域，直到达到最大错误 (默认情况下为 200) 数。</span><span class="sxs-lookup"><span data-stu-id="0f356-140">MVC will continue validating fields until reaches the maximum number of errors (200 by default).</span></span> <span data-ttu-id="0f356-141">你可以配置该数字的方法是插入下面的代码插入`ConfigureServices`中的方法*Startup.cs*文件：</span><span class="sxs-lookup"><span data-stu-id="0f356-141">You can configure this number by inserting the following code into the `ConfigureServices` method in the *Startup.cs* file:</span></span>

<span data-ttu-id="0f356-142">[!code-csharp[Main](validation/sample/Startup.cs?range=27)]</span><span class="sxs-lookup"><span data-stu-id="0f356-142">[!code-csharp[Main](validation/sample/Startup.cs?range=27)]</span></span>

## <a name="handling-model-state-errors"></a><span data-ttu-id="0f356-143">处理模型状态错误</span><span class="sxs-lookup"><span data-stu-id="0f356-143">Handling Model State Errors</span></span>

<span data-ttu-id="0f356-144">在正在调用的每个控制器操作之前，将执行模型验证和操作方法负责检查`ModelState.IsValid`并相应地作出反应。</span><span class="sxs-lookup"><span data-stu-id="0f356-144">Model validation occurs prior to each controller action being invoked, and it is the action method’s responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="0f356-145">在许多情况下，正确的反应是返回某种类型的错误响应，理想情况下详细说明模型验证失败的原因。</span><span class="sxs-lookup"><span data-stu-id="0f356-145">In many cases, the appropriate reaction is to return some kind of error response, ideally detailing the reason why model validation failed.</span></span>

<span data-ttu-id="0f356-146">某些应用程序会选择遵守标准的约定来处理模型验证错误，在该情况下筛选器可能会为适当的位置，若要实现此类策略。</span><span class="sxs-lookup"><span data-stu-id="0f356-146">Some apps will choose to follow a standard convention for dealing with model validation errors, in which case a filter may be an appropriate place to implement such a policy.</span></span> <span data-ttu-id="0f356-147">你应测试你的操作与有效和无效的模型状态的行为方式。</span><span class="sxs-lookup"><span data-stu-id="0f356-147">You should test how your actions behave with valid and invalid model states.</span></span>

## <a name="manual-validation"></a><span data-ttu-id="0f356-148">手动验证</span><span class="sxs-lookup"><span data-stu-id="0f356-148">Manual validation</span></span>

<span data-ttu-id="0f356-149">模型绑定和验证完成后，你可能需要重复它的部分。</span><span class="sxs-lookup"><span data-stu-id="0f356-149">After model binding and validation are complete, you may want to repeat parts of it.</span></span> <span data-ttu-id="0f356-150">例如，用户可能具有文本在字段中输入应为整数，或可能需要计算模型的属性的值。</span><span class="sxs-lookup"><span data-stu-id="0f356-150">For example, a user may have entered text in a field expecting an integer, or you may need to compute a value for a model's property.</span></span>

<span data-ttu-id="0f356-151">你可能需要手动运行验证。</span><span class="sxs-lookup"><span data-stu-id="0f356-151">You may need to run validation manually.</span></span> <span data-ttu-id="0f356-152">为此，请调用`TryValidateModel`方法，如下所示：</span><span class="sxs-lookup"><span data-stu-id="0f356-152">To do so, call the `TryValidateModel` method, as shown here:</span></span>

<span data-ttu-id="0f356-153">[!code-csharp[Main](validation/sample/MoviesController.cs?range=52)]</span><span class="sxs-lookup"><span data-stu-id="0f356-153">[!code-csharp[Main](validation/sample/MoviesController.cs?range=52)]</span></span>

## <a name="custom-validation"></a><span data-ttu-id="0f356-154">自定义验证</span><span class="sxs-lookup"><span data-stu-id="0f356-154">Custom validation</span></span>

<span data-ttu-id="0f356-155">验证特性适用于大多数验证需求。</span><span class="sxs-lookup"><span data-stu-id="0f356-155">Validation attributes work for most validation needs.</span></span> <span data-ttu-id="0f356-156">但是，一些的验证规则是特定于你的业务，它们不只是一般数据验证如确保字段是必需的或符合到一系列的值。</span><span class="sxs-lookup"><span data-stu-id="0f356-156">However, some validation rules are specific to your business, as they're not just generic data validation such as ensuring a field is required or that it conforms to a range of values.</span></span> <span data-ttu-id="0f356-157">对于这些情况下，自定义验证的特性是不错的解决方案。</span><span class="sxs-lookup"><span data-stu-id="0f356-157">For these scenarios, custom validation attributes are a great solution.</span></span> <span data-ttu-id="0f356-158">在 MVC 创建你自己的自定义验证特性很容易。</span><span class="sxs-lookup"><span data-stu-id="0f356-158">Creating your own custom validation attributes in MVC is easy.</span></span> <span data-ttu-id="0f356-159">只需从继承`ValidationAttribute`，并重写`IsValid`方法。</span><span class="sxs-lookup"><span data-stu-id="0f356-159">Just inherit from the `ValidationAttribute`, and override the `IsValid` method.</span></span> <span data-ttu-id="0f356-160">`IsValid`方法接受两个参数，第一种是已命名的对象*值*第二项是`ValidationContext`对象名为*validationContext*。</span><span class="sxs-lookup"><span data-stu-id="0f356-160">The `IsValid` method accepts two parameters, the first is an object named *value* and the second is a `ValidationContext` object named *validationContext*.</span></span> <span data-ttu-id="0f356-161">*值*从验证你的自定义验证程序的字段引用的实际值。</span><span class="sxs-lookup"><span data-stu-id="0f356-161">*Value* refers to the actual value from the field that your custom validator is validating.</span></span>

<span data-ttu-id="0f356-162">在下面的示例中，业务规则规定，用户不可能设置流派为*经典*对于影片之后 1960年发布。</span><span class="sxs-lookup"><span data-stu-id="0f356-162">In the following sample, a business rule states that users may not set the genre to *Classic* for a movie released after 1960.</span></span> <span data-ttu-id="0f356-163">`[ClassicMovie]`属性会首先，检查流派，如果它是标准，然后它检查发布日期是否晚于 1960年。</span><span class="sxs-lookup"><span data-stu-id="0f356-163">The `[ClassicMovie]` attribute checks the genre first, and if it is a classic, then it checks the release date to see that it is later than 1960.</span></span> <span data-ttu-id="0f356-164">如果它将被释放后 1960年，验证将失败。</span><span class="sxs-lookup"><span data-stu-id="0f356-164">If it is released after 1960, validation fails.</span></span> <span data-ttu-id="0f356-165">此属性接受一个表示可用于验证数据的年份的整数参数。</span><span class="sxs-lookup"><span data-stu-id="0f356-165">The attribute accepts an integer parameter representing the year that you can use to validate data.</span></span> <span data-ttu-id="0f356-166">你可以捕获该特性的构造函数中参数的值，如下所示：</span><span class="sxs-lookup"><span data-stu-id="0f356-166">You can capture the value of the parameter in the attribute's constructor, as shown here:</span></span>

<span data-ttu-id="0f356-167">[!code-csharp[Main](validation/sample/ClassicMovieAttribute.cs?range=9-28)]</span><span class="sxs-lookup"><span data-stu-id="0f356-167">[!code-csharp[Main](validation/sample/ClassicMovieAttribute.cs?range=9-28)]</span></span>

<span data-ttu-id="0f356-168">`movie`变量表示上面`Movie`包含窗体提交以验证中的数据的对象。</span><span class="sxs-lookup"><span data-stu-id="0f356-168">The `movie` variable above represents a `Movie` object that contains the data from the form submission to validate.</span></span> <span data-ttu-id="0f356-169">在这种情况下，验证代码检查的日期和中的流派`IsValid`方法`ClassicMovieAttribute`根据规则类。</span><span class="sxs-lookup"><span data-stu-id="0f356-169">In this case, the validation code checks the date and genre in the `IsValid` method of the `ClassicMovieAttribute` class as per the rules.</span></span> <span data-ttu-id="0f356-170">在成功地验证`IsValid`返回`ValidationResult.Success`代码，并在验证失败时，`ValidationResult`并显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="0f356-170">Upon successful validation `IsValid` returns a `ValidationResult.Success` code, and when validation fails, a `ValidationResult` with an error message.</span></span> <span data-ttu-id="0f356-171">当用户修改`Genre`字段并提交该表单，`IsValid`方法`ClassicMovieAttribute`将验证电影是否为标准。</span><span class="sxs-lookup"><span data-stu-id="0f356-171">When a user modifies the `Genre` field and submits the form, the `IsValid` method of the `ClassicMovieAttribute` will verify whether the movie is a classic.</span></span> <span data-ttu-id="0f356-172">与任何内置属性，类似应用`ClassicMovieAttribute`到属性，如`ReleaseDate`以确保验证执行，如前面的代码示例中所示。</span><span class="sxs-lookup"><span data-stu-id="0f356-172">Like any built-in attribute, apply the `ClassicMovieAttribute` to a property such as `ReleaseDate` to ensure validation happens, as shown in the previous code sample.</span></span> <span data-ttu-id="0f356-173">由于此示例仅处理`Movie`类型，更好的选择是使用`IValidatableObject`下面这一段中所示。</span><span class="sxs-lookup"><span data-stu-id="0f356-173">Since the example works only with `Movie` types, a better option is to use `IValidatableObject` as shown in the following paragraph.</span></span>

<span data-ttu-id="0f356-174">或者，这一相同代码无法放置在模型中通过实现`Validate`方法`IValidatableObject`接口。</span><span class="sxs-lookup"><span data-stu-id="0f356-174">Alternatively, this same code could be placed in the model by implementing the `Validate` method on the `IValidatableObject` interface.</span></span> <span data-ttu-id="0f356-175">当自定义验证特性适用于验证单独的属性时，实现`IValidatableObject`可以用来实现类级别验证，如下所示。</span><span class="sxs-lookup"><span data-stu-id="0f356-175">While custom validation attributes work well for validating individual properties, implementing `IValidatableObject` can be used to implement class-level validation as seen here.</span></span>

<span data-ttu-id="0f356-176">[!code-csharp[Main](validation/sample/MovieIValidatable.cs?range=33-41)]</span><span class="sxs-lookup"><span data-stu-id="0f356-176">[!code-csharp[Main](validation/sample/MovieIValidatable.cs?range=33-41)]</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="0f356-177">客户端验证</span><span class="sxs-lookup"><span data-stu-id="0f356-177">Client side validation</span></span>

<span data-ttu-id="0f356-178">客户端验证是一个重大便利的用户。</span><span class="sxs-lookup"><span data-stu-id="0f356-178">Client side validation is a great convenience for users.</span></span> <span data-ttu-id="0f356-179">它将保存它们需要花费时间的时间等待往返行程到服务器。</span><span class="sxs-lookup"><span data-stu-id="0f356-179">It saves time they would otherwise spend waiting for a round trip to the server.</span></span> <span data-ttu-id="0f356-180">在业务术语中，甚至几秒的乘积数百次每一天的秒的小数部分将添加最多会导致大量时间，费用，并减少失败。</span><span class="sxs-lookup"><span data-stu-id="0f356-180">In business terms, even a few fractions of seconds multiplied hundreds of times each day adds up to be a lot of time, expense, and frustration.</span></span> <span data-ttu-id="0f356-181">简单和快速验证使用户能够更有效地工作，并生成更好的质量输入和输出。</span><span class="sxs-lookup"><span data-stu-id="0f356-181">Straightforward and immediate validation enables users to work more efficiently and produce better quality input and output.</span></span>

<span data-ttu-id="0f356-182">你必须具有正确的 JavaScript 脚本引用的视图中的客户端验证工作如下所示准备好。</span><span class="sxs-lookup"><span data-stu-id="0f356-182">You must have a view with the proper JavaScript script references in place for client side validation to work as you see here.</span></span>

<span data-ttu-id="0f356-183">[!code-html[Main](validation/sample/Views/Shared/_Layout.cshtml?range=37)]</span><span class="sxs-lookup"><span data-stu-id="0f356-183">[!code-html[Main](validation/sample/Views/Shared/_Layout.cshtml?range=37)]</span></span>

<span data-ttu-id="0f356-184">[!code-html[Main](validation/sample/Views/Shared/_ValidationScriptsPartial.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="0f356-184">[!code-html[Main](validation/sample/Views/Shared/_ValidationScriptsPartial.cshtml)]</span></span>

<span data-ttu-id="0f356-185">MVC 使用除了从模型属性的类型元数据的验证属性以验证数据并显示使用 JavaScript 的任何错误消息。</span><span class="sxs-lookup"><span data-stu-id="0f356-185">MVC uses validation attributes in addition to type metadata from model properties to validate data and display any error messages using JavaScript.</span></span> <span data-ttu-id="0f356-186">当你使用 MVC 来呈现从模型使用的窗体元素[标记帮助程序](xref:mvc/views/tag-helpers/intro)或[HTML 帮助器](xref:mvc/views/overview)它会将添加 HTML 5[数据特性](http://w3c.github.io/html/dom.html#embedding-custom-non-visible-data-with-the-data-attributes)中验证，需要为窗体元素下面所示。</span><span class="sxs-lookup"><span data-stu-id="0f356-186">When you use MVC to render form elements from a model using [Tag Helpers](xref:mvc/views/tag-helpers/intro) or [HTML helpers](xref:mvc/views/overview) it will add HTML 5 [data- attributes](http://w3c.github.io/html/dom.html#embedding-custom-non-visible-data-with-the-data-attributes) in the form elements that need validation, as shown below.</span></span> <span data-ttu-id="0f356-187">MVC 生成`data-`内置和自定义属性的属性。</span><span class="sxs-lookup"><span data-stu-id="0f356-187">MVC generates the `data-` attributes for both built-in and custom attributes.</span></span> <span data-ttu-id="0f356-188">你可以使用如下所示的相关标记帮助器客户端上显示验证错误：</span><span class="sxs-lookup"><span data-stu-id="0f356-188">You can display validation errors on the client using the relevant tag helpers as shown here:</span></span>

<span data-ttu-id="0f356-189">[!code-html[Main](validation/sample/Views/Movies/Create.cshtml?highlight=4,5&range=19-25)]</span><span class="sxs-lookup"><span data-stu-id="0f356-189">[!code-html[Main](validation/sample/Views/Movies/Create.cshtml?highlight=4,5&range=19-25)]</span></span>

<span data-ttu-id="0f356-190">上面的标记帮助器呈现的 HTML 下面。</span><span class="sxs-lookup"><span data-stu-id="0f356-190">The tag helpers above render the HTML below.</span></span> <span data-ttu-id="0f356-191">请注意， `data-` HTML 中的属性输出对应的验证属性`ReleaseDate`属性。</span><span class="sxs-lookup"><span data-stu-id="0f356-191">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `ReleaseDate` property.</span></span> <span data-ttu-id="0f356-192">`data-val-required`属性下面包含的错误消息，如果用户在发行日期字段中，未填满，该消息显示在随附的说明显示`<span>`元素。</span><span class="sxs-lookup"><span data-stu-id="0f356-192">The `data-val-required` attribute below contains an error message to display if the user doesn't fill in the release date field, and that message displays in the accompanying `<span>` element.</span></span>

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

<span data-ttu-id="0f356-193">客户端验证阻止提交，直到该窗体是有效。</span><span class="sxs-lookup"><span data-stu-id="0f356-193">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="0f356-194">提交按钮运行 JavaScript 提交窗体或显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="0f356-194">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="0f356-195">MVC 确定基于.NET 数据类型的属性，可能使用重写的类型属性值`[DataType]`属性。</span><span class="sxs-lookup"><span data-stu-id="0f356-195">MVC determines type attribute values based on the .NET data type of a property, possibly overridden using `[DataType]` attributes.</span></span> <span data-ttu-id="0f356-196">基`[DataType]`属性不会实际的服务器端验证。</span><span class="sxs-lookup"><span data-stu-id="0f356-196">The base `[DataType]` attribute does no real server-side validation.</span></span> <span data-ttu-id="0f356-197">浏览器选择自己的错误消息，并显示这些错误，但他们希望，但是 jQuery 验证非介入式包可以替代的消息，并与其他一致地显示它们。</span><span class="sxs-lookup"><span data-stu-id="0f356-197">Browsers choose their own error messages and display those errors however they wish, however the jQuery Validation Unobtrusive package can override the messages and display them consistently with others.</span></span> <span data-ttu-id="0f356-198">发生这种情况最明显时用户应用`[DataType]`如子类`[EmailAddress]`。</span><span class="sxs-lookup"><span data-stu-id="0f356-198">This happens most obviously when users apply `[DataType]` subclasses such as `[EmailAddress]`.</span></span>

## <a name="iclientmodelvalidator"></a><span data-ttu-id="0f356-199">IClientModelValidator</span><span class="sxs-lookup"><span data-stu-id="0f356-199">IClientModelValidator</span></span>

<span data-ttu-id="0f356-200">可能你的自定义特性，创建客户端逻辑和[非介入式验证](http://jqueryvalidation.org/documentation/)将为你自动作为一部分验证客户端上执行。</span><span class="sxs-lookup"><span data-stu-id="0f356-200">You may create client side logic for your custom attribute, and [unobtrusive validation](http://jqueryvalidation.org/documentation/) will execute it on the client for you automatically as part of validation.</span></span> <span data-ttu-id="0f356-201">第一步是控制哪些数据特性添加通过实现`IClientModelValidator`接口如下所示：</span><span class="sxs-lookup"><span data-stu-id="0f356-201">The first step is to control what data- attributes are added by implementing the `IClientModelValidator` interface as shown here:</span></span>

<span data-ttu-id="0f356-202">[!code-csharp[Main](validation/sample/ClassicMovieAttribute.cs?range=30-42)]</span><span class="sxs-lookup"><span data-stu-id="0f356-202">[!code-csharp[Main](validation/sample/ClassicMovieAttribute.cs?range=30-42)]</span></span>

<span data-ttu-id="0f356-203">实现此接口的属性可以将 HTML 特性添加到生成的字段。</span><span class="sxs-lookup"><span data-stu-id="0f356-203">Attributes that implement this interface can add HTML attributes to generated fields.</span></span> <span data-ttu-id="0f356-204">检查的输出`ReleaseDate`元素显示非常相似与前面的示例中，但现在没有的 HTML`data-val-classicmovie`属性中定义`AddValidation`方法`IClientModelValidator`。</span><span class="sxs-lookup"><span data-stu-id="0f356-204">Examining the output for the `ReleaseDate` element reveals HTML that is similar to the previous example, except now there is a `data-val-classicmovie` attribute that was defined in the `AddValidation` method of `IClientModelValidator`.</span></span>

```html
<input class="form-control" type="datetime"
data-val="true"
data-val-classicmovie="Classic movies must have a release year earlier than 1960."
data-val-classicmovie-year="1960"
data-val-required="The ReleaseDate field is required."
id="ReleaseDate" name="ReleaseDate" value="" />
```

<span data-ttu-id="0f356-205">非介入式验证将使用中的数据`data-`属性来显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="0f356-205">Unobtrusive validation uses the data in the `data-` attributes to display error messages.</span></span> <span data-ttu-id="0f356-206">但是，jQuery 不知道有关规则或消息之前将它们添加到 jQuery 的`validator`对象。</span><span class="sxs-lookup"><span data-stu-id="0f356-206">However, jQuery doesn't know about rules or messages until you add them to jQuery's `validator` object.</span></span> <span data-ttu-id="0f356-207">这下面添加一个名为方法的示例中所示`classicmovie`包含自定义客户端验证代码，以便 jQuery`validator`对象。</span><span class="sxs-lookup"><span data-stu-id="0f356-207">This is shown in the example below that adds a method named `classicmovie` containing custom client validation code to the jQuery `validator` object.</span></span>

<span data-ttu-id="0f356-208">[!code-javascript[Main](validation/sample/Views/Movies/Create.cshtml?range=71-93)]</span><span class="sxs-lookup"><span data-stu-id="0f356-208">[!code-javascript[Main](validation/sample/Views/Movies/Create.cshtml?range=71-93)]</span></span>

<span data-ttu-id="0f356-209">现在 jQuery 具有的信息执行自定义 JavaScript 验证，以及要显示如果该验证代码返回的错误消息。</span><span class="sxs-lookup"><span data-stu-id="0f356-209">Now jQuery has the information to execute the custom JavaScript validation as well as the error message to display if that validation code returns false.</span></span>

## <a name="remote-validation"></a><span data-ttu-id="0f356-210">远程验证</span><span class="sxs-lookup"><span data-stu-id="0f356-210">Remote validation</span></span>

<span data-ttu-id="0f356-211">远程验证是很有用的功能，你需要验证服务器上的数据对客户端上的数据时要使用。</span><span class="sxs-lookup"><span data-stu-id="0f356-211">Remote validation is a great feature to use when you need to validate data on the client against data on the server.</span></span> <span data-ttu-id="0f356-212">例如，你的应用程序可能需要验证是否电子邮件或用户的名称已在使用，并且它必须查询大量的数据进行管理。</span><span class="sxs-lookup"><span data-stu-id="0f356-212">For example, your app may need to verify whether an email or user name is already in use, and it must query a large amount of data to do so.</span></span> <span data-ttu-id="0f356-213">下载大型数据集的用于验证一个或几个字段会占用过多资源。</span><span class="sxs-lookup"><span data-stu-id="0f356-213">Downloading large sets of data for validating one or a few fields consumes too many resources.</span></span> <span data-ttu-id="0f356-214">它还可以公开敏感信息。</span><span class="sxs-lookup"><span data-stu-id="0f356-214">It may also expose sensitive information.</span></span> <span data-ttu-id="0f356-215">一种替代方法是发出往返的请求，以验证字段。</span><span class="sxs-lookup"><span data-stu-id="0f356-215">An alternative is to make a round-trip request to validate a field.</span></span>

<span data-ttu-id="0f356-216">你可以在一个双步骤过程中实现远程验证。</span><span class="sxs-lookup"><span data-stu-id="0f356-216">You can implement remote validation in a two step process.</span></span> <span data-ttu-id="0f356-217">首先，必须批注与模型`[Remote]`属性。</span><span class="sxs-lookup"><span data-stu-id="0f356-217">First, you must annotate your model with the `[Remote]` attribute.</span></span> <span data-ttu-id="0f356-218">`[Remote]`属性接受多个重载可用于将定向到相应的代码以调用的客户端 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="0f356-218">The `[Remote]` attribute accepts multiple overloads you can use to direct client side JavaScript to the appropriate code to call.</span></span> <span data-ttu-id="0f356-219">示例指向`VerifyEmail`操作方法`Users`控制器。</span><span class="sxs-lookup"><span data-stu-id="0f356-219">The example points to the `VerifyEmail` action method of the `Users` controller.</span></span>

<span data-ttu-id="0f356-220">[!code-csharp[Main](validation/sample/User.cs?range=5-9)]</span><span class="sxs-lookup"><span data-stu-id="0f356-220">[!code-csharp[Main](validation/sample/User.cs?range=5-9)]</span></span>

<span data-ttu-id="0f356-221">中定义的第二步将验证代码放在相应的操作方法`[Remote]`属性。</span><span class="sxs-lookup"><span data-stu-id="0f356-221">The second step is putting the validation code in the corresponding action method as defined in the `[Remote]` attribute.</span></span> <span data-ttu-id="0f356-222">它将返回`JsonResult`，客户端可用来继续操作或暂停，如果需要显示错误。</span><span class="sxs-lookup"><span data-stu-id="0f356-222">It returns a `JsonResult` that the client side can use to proceed or pause and display an error if needed.</span></span>

<span data-ttu-id="0f356-223">[!code-none[Main](validation/sample/UsersController.cs?range=19-28)]</span><span class="sxs-lookup"><span data-stu-id="0f356-223">[!code-none[Main](validation/sample/UsersController.cs?range=19-28)]</span></span>

<span data-ttu-id="0f356-224">现在，当用户输入的电子邮件，在视图中的 JavaScript 进行远程调用以查看是否有已采用该电子邮件，以及如果是这样，然后显示的错误消息。</span><span class="sxs-lookup"><span data-stu-id="0f356-224">Now when users enter an email, JavaScript in the view makes a remote call to see if that email has been taken, and if so, then displays the error message.</span></span> <span data-ttu-id="0f356-225">否则，用户可以像往常一样提交窗体。</span><span class="sxs-lookup"><span data-stu-id="0f356-225">Otherwise, the user can submit the form as usual.</span></span>
