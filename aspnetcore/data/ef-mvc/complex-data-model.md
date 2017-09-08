---
title: "ASP.NET 核心 MVC 与 EF 核心-数据模型的 10 5"
author: tdykstra
description: "在本教程中添加更多实体和关系，并通过指定格式设置、 验证和数据库的映射规则自定义数据模型。"
keywords: "ASP.NET 核心，实体框架核心数据批注"
ms.author: tdykstra
manager: wpickett
ms.date: 03/15/2017
ms.topic: get-started-article
ms.assetid: 0dd63913-a041-48b6-96a4-3aeaedbdf5d0
ms.technology: aspnet
ms.prod: asp.net-core
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: 7d216bc07d0a8d739f0cecbc5b571b6144c13e61
ms.sourcegitcommit: 5355c96a1768e5a1d5698a98c190e7addcc4ded5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2017
---
# <a name="creating-a-complex-data-model---ef-core-with-aspnet-core-mvc-tutorial-5-of-10"></a><span data-ttu-id="6f0b4-104">创建复杂的数据模型的 EF 内核，它们有 ASP.NET 核心 MVC 教程 (5 的 10)</span><span class="sxs-lookup"><span data-stu-id="6f0b4-104">Creating a complex data model - EF Core with ASP.NET Core MVC tutorial (5 of 10)</span></span>

<span data-ttu-id="6f0b4-105">通过[Tom Dykstra](https://github.com/tdykstra)和[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6f0b4-105">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6f0b4-106">Contoso 大学示例 web 应用程序演示如何创建使用实体框架核心和 Visual Studio 的 ASP.NET 核心 MVC web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-106">The Contoso University sample web application demonstrates how to create ASP.NET Core MVC web applications using Entity Framework Core and Visual Studio.</span></span> <span data-ttu-id="6f0b4-107">有关教程系列的信息，请参阅[序列中的第一个教程](intro.md)。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-107">For information about the tutorial series, see [the first tutorial in the series](intro.md).</span></span>

<span data-ttu-id="6f0b4-108">在前面的教程，你使用过简单的数据模型的三个实体组成。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-108">In the previous tutorials, you worked with a simple data model that was composed of three entities.</span></span> <span data-ttu-id="6f0b4-109">在本教程中，你将添加多个实体和关系并将通过指定格式设置、 验证和数据库的映射规则自定义数据模型。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-109">In this tutorial, you'll add more entities and relationships and you'll customize the data model by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="6f0b4-110">当完成时，实体类将组成以下图所示的已完成的数据模型：</span><span class="sxs-lookup"><span data-stu-id="6f0b4-110">When you're finished, the entity classes will make up the completed data model that's shown in the following illustration:</span></span>

![实体关系图](complex-data-model/_static/diagram.png)

## <a name="customize-the-data-model-by-using-attributes"></a><span data-ttu-id="6f0b4-112">通过使用属性自定义数据模型</span><span class="sxs-lookup"><span data-stu-id="6f0b4-112">Customize the Data Model by Using Attributes</span></span>

<span data-ttu-id="6f0b4-113">在本部分中，你将了解如何通过使用指定格式设置，验证和数据库的映射规则的属性来自定义数据模型。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-113">In this section you'll see how to customize the data model by using attributes that specify formatting, validation, and database mapping rules.</span></span> <span data-ttu-id="6f0b4-114">然后在其中你将创建以下各节的几加上完成的 School 数据模型属性类到你已创建，并在模型中创建的剩余的实体类型的新类。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-114">Then in several of the following sections you'll create the complete School data model by adding attributes to the classes you already created and creating new classes for the remaining entity types in the model.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="6f0b4-115">数据类型属性</span><span class="sxs-lookup"><span data-stu-id="6f0b4-115">The DataType attribute</span></span>

<span data-ttu-id="6f0b4-116">对于学生注册日期，网页的所有当前显示时间和日期，虽然所有关心为此字段是日期。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-116">For student enrollment dates, all of the web pages currently display the time along with the date, although all you care about for this field is the date.</span></span> <span data-ttu-id="6f0b4-117">通过使用数据 annotation 特性，你可以使一个代码将在每个视图中显示的数据中修复的显示格式的更改。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-117">By using data annotation attributes, you can make one code change that will fix the display format in every view that shows the data.</span></span> <span data-ttu-id="6f0b4-118">若要查看如何执行操作，你将添加到属性的示例`EnrollmentDate`中的属性`Student`类。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-118">To see an example of how to do that, you'll add an attribute to the `EnrollmentDate` property in the `Student` class.</span></span>

<span data-ttu-id="6f0b4-119">在*Models/Student.cs*，添加`using`语句`System.ComponentModel.DataAnnotations`命名空间并添加`DataType`和`DisplayFormat`特性以`EnrollmentDate`属性，如下面的示例中所示：</span><span class="sxs-lookup"><span data-stu-id="6f0b4-119">In *Models/Student.cs*, add a `using` statement for the `System.ComponentModel.DataAnnotations` namespace and add `DataType` and `DisplayFormat` attributes to the `EnrollmentDate` property, as shown in the following example:</span></span>

<span data-ttu-id="6f0b4-120">[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]</span><span class="sxs-lookup"><span data-stu-id="6f0b4-120">[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]</span></span>

<span data-ttu-id="6f0b4-121">`DataType`属性用于指定比数据库内部类型更具体的数据类型。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-121">The `DataType` attribute is used to specify a data type that is more specific than the database intrinsic type.</span></span> <span data-ttu-id="6f0b4-122">在这种情况下，我们只想跟踪的日期，不的日期和时间。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-122">In this case we only want to keep track of the date, not the date and time.</span></span> <span data-ttu-id="6f0b4-123">`DataType`枚举提供了许多数据类型，例如日期、 时间、 电话号码、 货币、 电子邮件地址，和的详细信息。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-123">The  `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="6f0b4-124">`DataType`属性还可以启用应用程序以自动提供特定类型的功能。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-124">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="6f0b4-125">例如，`mailto:`链接可以为创建`DataType.EmailAddress`，和日期选择器可提供用于`DataType.Date`在支持 HTML5 的浏览器中。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-125">For example, a `mailto:` link can be created for `DataType.EmailAddress`, and a date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="6f0b4-126">`DataType`属性发出 HTML 5 `data-` HTML 5 浏览器可以理解的 (读作的数据 dash) 属性。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-126">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers can understand.</span></span> <span data-ttu-id="6f0b4-127">`DataType`属性不提供任何验证。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-127">The `DataType` attributes do not provide any validation.</span></span>

<span data-ttu-id="6f0b4-128">`DataType.Date`未指定的日期的显示格式。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-128">`DataType.Date` does not specify the format of the date that is displayed.</span></span> <span data-ttu-id="6f0b4-129">默认情况下，根据基于服务器的 CultureInfo 的默认格式显示数据字段。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-129">By default, the data field is displayed according to the default formats based on the server's CultureInfo.</span></span>

<span data-ttu-id="6f0b4-130">`DisplayFormat`特性用于显式指定的日期格式：</span><span class="sxs-lookup"><span data-stu-id="6f0b4-130">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="6f0b4-131">`ApplyFormatInEditMode`设置指定的格式设置也应该将应用时的值显示在文本框中以进行编辑。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-131">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied when the value is displayed in a text box for editing.</span></span> <span data-ttu-id="6f0b4-132">（你可能不想，对于某些域-例如，货币值，你可能不想在文本框中的货币符号以进行编辑。）</span><span class="sxs-lookup"><span data-stu-id="6f0b4-132">(You might not want that for some fields -- for example, for currency values, you might not want the currency symbol in the text box for editing.)</span></span>

<span data-ttu-id="6f0b4-133">你可以使用`DisplayFormat`属性通过本身，但它通常是使用一个好办法`DataType`还属性。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-133">You can use the `DisplayFormat` attribute by itself, but it's generally a good idea to use the `DataType` attribute also.</span></span> <span data-ttu-id="6f0b4-134">`DataType`属性传达数据而不是如何在屏幕上呈现其的语义，并提供就不会使用了以下好处`DisplayFormat`:</span><span class="sxs-lookup"><span data-stu-id="6f0b4-134">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with `DisplayFormat`:</span></span>

* <span data-ttu-id="6f0b4-135">浏览器可以启用 HTML5 功能 （例如以显示一个日历控件、 区域设置相对应的货币符号、 电子邮件链接，某些客户端输入验证，等等。）。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-135">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, some client-side input validation, etc.).</span></span>

* <span data-ttu-id="6f0b4-136">默认情况下，浏览器将呈现使用基于你的区域设置的正确格式的数据。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-136">By default, the browser will render data using the correct format based on your locale.</span></span>

<span data-ttu-id="6f0b4-137">有关详细信息，请参阅[\<输入 > 标记帮助器文档](../../mvc/views/working-with-forms.md#the-input-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-137">For more information, see the [\<input> tag helper documentation](../../mvc/views/working-with-forms.md#the-input-tag-helper).</span></span>

<span data-ttu-id="6f0b4-138">再次运行学生索引页，并请注意，注册日期不再显示时间。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-138">Run the Students Index page again and notice that times are no longer displayed for the enrollment dates.</span></span> <span data-ttu-id="6f0b4-139">相同将为真，使用学生模型的任何视图。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-139">The same will be true for any view that uses the Student model.</span></span>

![显示日期而无需时间的学生索引页](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="6f0b4-141">StringLength 属性</span><span class="sxs-lookup"><span data-stu-id="6f0b4-141">The StringLength attribute</span></span>

<span data-ttu-id="6f0b4-142">你还可以指定数据验证规则和使用属性的验证错误消息。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-142">You can also specify data validation rules and validation error messages using attributes.</span></span> <span data-ttu-id="6f0b4-143">`StringLength`属性数据库中设置的最大长度，并提供客户端和服务器端 ASP.NET MVC 的验证。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-143">The `StringLength` attribute sets the maximum length  in the database and provides client side and server side validation for ASP.NET MVC.</span></span> <span data-ttu-id="6f0b4-144">你还可以通过以下方式指定最小字符串长度中此特性，但最小值对数据库架构没有任何影响。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-144">You can also specify the minimum string length in this attribute, but the minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="6f0b4-145">假设你想要确保，用户不超过 50 个字符输入名称。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-145">Suppose you want to ensure that users don't enter more than 50 characters for a name.</span></span> <span data-ttu-id="6f0b4-146">若要添加此限制，添加`StringLength`特性以`LastName`和`FirstMidName`属性，如下面的示例中所示：</span><span class="sxs-lookup"><span data-stu-id="6f0b4-146">To add this limitation, add `StringLength` attributes to the `LastName` and `FirstMidName` properties, as shown in the following example:</span></span>

<span data-ttu-id="6f0b4-147">[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]</span><span class="sxs-lookup"><span data-stu-id="6f0b4-147">[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]</span></span>

<span data-ttu-id="6f0b4-148">`StringLength`属性不会阻止的用户的空白区域输入一个名称。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-148">The `StringLength` attribute won't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="6f0b4-149">你可以使用`RegularExpression`要将限制应用到的输入属性。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-149">You can use the `RegularExpression` attribute to apply restrictions to the input.</span></span> <span data-ttu-id="6f0b4-150">例如，下面的代码要求的第一个字符是大写且其余的字符是按字母顺序排列：</span><span class="sxs-lookup"><span data-stu-id="6f0b4-150">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z''-'\s]*$")]
```

<span data-ttu-id="6f0b4-151">`MaxLength`属性提供的功能类似于`StringLength`属性但不提供客户端验证。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-151">The `MaxLength` attribute provides functionality similar to the `StringLength` attribute but doesn't provide client side validation.</span></span>

<span data-ttu-id="6f0b4-152">现在，数据库模型已更改需要在数据库架构更改的方式。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-152">The database model has now changed in a way that requires a change in the database schema.</span></span> <span data-ttu-id="6f0b4-153">你将使用迁移来更新的架构，而不会丢失你在使用应用程序 UI 已添加到数据库的任何数据。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-153">You'll use migrations to update the schema without losing any data that you may have added to the database by using the application UI.</span></span>

<span data-ttu-id="6f0b4-154">保存所做的更改并生成项目。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-154">Save your changes and build the project.</span></span> <span data-ttu-id="6f0b4-155">然后在项目文件夹中打开命令窗口并输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="6f0b4-155">Then open the command window in the project folder and enter the following commands:</span></span>

```console
dotnet ef migrations add MaxLengthOnNames
```

```console
dotnet ef database update
```

<span data-ttu-id="6f0b4-156">`migrations add`命令会发出警告，可能会发生数据丢失，因为更改使短为两个列的最大长度。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-156">The `migrations add` command warns that data loss may occur, because the change makes the maximum length shorter for two columns.</span></span>  <span data-ttu-id="6f0b4-157">迁移创建名为的文件*\<时间戳 > _MaxLengthOnNames.cs*。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-157">Migrations creates a file named *\<timeStamp>_MaxLengthOnNames.cs*.</span></span> <span data-ttu-id="6f0b4-158">此文件包含中的代码`Up`将更新数据库，以匹配当前数据模型的方法。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-158">This file contains code in the `Up` method that will update the database to match the current data model.</span></span> <span data-ttu-id="6f0b4-159">`database update`命令运行该代码。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-159">The `database update` command ran that code.</span></span>

<span data-ttu-id="6f0b4-160">实体框架使用的迁移文件名称的前缀的时间戳以整理迁移。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-160">The timestamp prefixed to the migrations file name is used by Entity Framework to order the migrations.</span></span> <span data-ttu-id="6f0b4-161">你可以在运行 update-database 命令中之前, 创建多个迁移，然后所有迁移应用中已创建的顺序。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-161">You can create multiple migrations before running the update-database command, and then all of the migrations are applied in the order in which they were created.</span></span>

<span data-ttu-id="6f0b4-162">运行创建页中，并输入任一名称超过 50 个字符。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-162">Run the Create page, and enter either name longer than 50 characters.</span></span> <span data-ttu-id="6f0b4-163">单击创建时，客户端验证将显示一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-163">When you click Create, client side validation shows an error message.</span></span>

![学生索引页显示的字符串长度错误](complex-data-model/_static/string-length-errors.png)

### <a name="the-column-attribute"></a><span data-ttu-id="6f0b4-165">列属性</span><span class="sxs-lookup"><span data-stu-id="6f0b4-165">The Column attribute</span></span>

<span data-ttu-id="6f0b4-166">特性还可用于控制如何类和属性映射到数据库。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-166">You can also use attributes to control how your classes and properties are mapped to the database.</span></span> <span data-ttu-id="6f0b4-167">假设你已使用名称`FirstMidName`的第一个名称字段，因为该字段还可能包含中间名。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-167">Suppose you had used the name `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span> <span data-ttu-id="6f0b4-168">但你想要将名为的数据库列`FirstName`，因为将编写针对数据库的即席查询的用户习惯于该名称。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-168">But you want the database column to be named `FirstName`, because users who will be writing ad-hoc queries against the database are accustomed to that name.</span></span> <span data-ttu-id="6f0b4-169">若要使此映射，你可以使用`Column`属性。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-169">To make this mapping, you can use the `Column` attribute.</span></span>

<span data-ttu-id="6f0b4-170">`Column`属性指定当创建数据库时，列`Student`映射到表`FirstMidName`属性将被命名为`FirstName`。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-170">The `Column` attribute specifies that when the database is created, the column of the `Student` table that maps to the `FirstMidName` property will be named `FirstName`.</span></span> <span data-ttu-id="6f0b4-171">换而言之，你的代码引用到`Student.FirstMidName`，数据将来自或在中更新`FirstName`列`Student`表。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-171">In other words, when your code refers to `Student.FirstMidName`, the data will come from or be updated in the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="6f0b4-172">如果未指定列名称，系统会提供与属性名相同的名称。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-172">If you don't specify column names, they are given the same name as the property name.</span></span>

<span data-ttu-id="6f0b4-173">在*Student.cs*文件中，添加`using`语句`System.ComponentModel.DataAnnotations.Schema`并添加到的列名称属性`FirstMidName`属性，如以下突出显示的代码中所示：</span><span class="sxs-lookup"><span data-stu-id="6f0b4-173">In the *Student.cs* file, add a `using` statement for `System.ComponentModel.DataAnnotations.Schema` and add the column name attribute to the `FirstMidName` property, as shown in the following highlighted code:</span></span>

<span data-ttu-id="6f0b4-174">[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]</span><span class="sxs-lookup"><span data-stu-id="6f0b4-174">[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]</span></span>

<span data-ttu-id="6f0b4-175">添加`Column`属性将更改模型后备`SchoolContext`，因此它不会与数据库相匹配。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-175">The addition of the `Column` attribute changes the model backing the `SchoolContext`, so it won't match the database.</span></span>

<span data-ttu-id="6f0b4-176">保存所做的更改并生成项目。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-176">Save your changes and build the project.</span></span> <span data-ttu-id="6f0b4-177">然后在项目文件夹中打开命令窗口并输入以下命令以创建另一个迁移：</span><span class="sxs-lookup"><span data-stu-id="6f0b4-177">Then open the command window in the project folder and enter the following commands to create another migration:</span></span>

```console
dotnet ef migrations add ColumnFirstName
```

```console
dotnet ef database update
```

<span data-ttu-id="6f0b4-178">在**SQL Server 对象资源管理器**，通过双击打开学生表设计器**学生**表。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-178">In **SQL Server Object Explorer**, open the Student table designer by double-clicking the **Student** table.</span></span>

![在 SSOX 中后迁移的学生表](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="6f0b4-180">应用前两个迁移之前，名称列中的 nvarchar (max) 类型。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-180">Before you applied the first two migrations, the name columns were of type nvarchar(MAX).</span></span> <span data-ttu-id="6f0b4-181">它们现在是 nvarchar(50) 和列名称已从 FirstMidName 更改为名字。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-181">They are now nvarchar(50) and the column name has changed from FirstMidName to FirstName.</span></span>

> [!Note]
> <span data-ttu-id="6f0b4-182">如果您尝试编译完成下列部分中创建的所有实体类之前，你可能会收到编译器错误。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-182">If you try to compile before you finish creating all of the entity classes in the following sections, you might get compiler errors.</span></span>

## <a name="final-changes-to-the-student-entity"></a><span data-ttu-id="6f0b4-183">最终学生实体更改</span><span class="sxs-lookup"><span data-stu-id="6f0b4-183">Final changes to the Student entity</span></span>

![学生实体](complex-data-model/_static/student-entity.png)

<span data-ttu-id="6f0b4-185">在*Models/Student.cs*，更早版本将你添加的代码替换为下面的代码。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-185">In *Models/Student.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="6f0b4-186">突出显示所做的更改。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-186">The changes are highlighted.</span></span>

<span data-ttu-id="6f0b4-187">[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]</span><span class="sxs-lookup"><span data-stu-id="6f0b4-187">[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]</span></span>

### <a name="the-required-attribute"></a><span data-ttu-id="6f0b4-188">必需的特性</span><span class="sxs-lookup"><span data-stu-id="6f0b4-188">The Required attribute</span></span>

<span data-ttu-id="6f0b4-189">`Required`特性使名称属性必填的字段。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-189">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="6f0b4-190">`Required`属性不需要不可为 null 的类型，如值类型 (DateTime、 int、 double、 float 等。)。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-190">The `Required` attribute is not needed for non-nullable types such as value types (DateTime, int, double, float, etc.).</span></span> <span data-ttu-id="6f0b4-191">不能为 null 的类型是自动被视为必填字段。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-191">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="6f0b4-192">无法删除`Required`属性并将其替换的最小长度参数`StringLength`属性：</span><span class="sxs-lookup"><span data-stu-id="6f0b4-192">You could remove the `Required` attribute and replace it with a minimum length parameter for the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="6f0b4-193">显示属性</span><span class="sxs-lookup"><span data-stu-id="6f0b4-193">The Display attribute</span></span>

<span data-ttu-id="6f0b4-194">`Display`属性指定文本框的标题应在每个实例 （其除以单词没有空间） 中为"名字"、"姓氏"、"全名"和"注册日期"而不是属性名称。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-194">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date" instead of the property name in each instance (which has no space dividing the words).</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="6f0b4-195">计算的 FullName 属性</span><span class="sxs-lookup"><span data-stu-id="6f0b4-195">The FullName calculated property</span></span>

<span data-ttu-id="6f0b4-196">`FullName`是返回一个值，通过串联两个其他属性创建一个计算的属性。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-196">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="6f0b4-197">因此它具有仅一个 get 访问器，但没有`FullName`列将生成数据库中。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-197">Therefore it has only a get accessor, and no `FullName` column will be generated in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="6f0b4-198">创建 Instructor 实体</span><span class="sxs-lookup"><span data-stu-id="6f0b4-198">Create the Instructor Entity</span></span>

![Instructor 实体](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="6f0b4-200">创建*Models/Instructor.cs*，模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="6f0b4-200">Create *Models/Instructor.cs*, replacing the template code with the following code:</span></span>

<span data-ttu-id="6f0b4-201">[!code-csharp[Main](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]</span><span class="sxs-lookup"><span data-stu-id="6f0b4-201">[!code-csharp[Main](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]</span></span>

<span data-ttu-id="6f0b4-202">请注意的几个属性都是相同的学生和 Instructor 实体中。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-202">Notice that several properties are the same in the Student and Instructor entities.</span></span> <span data-ttu-id="6f0b4-203">在[实现继承](inheritance.md)本系列教程更高版本，你将重构此代码以消除冗余。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-203">In the [Implementing Inheritance](inheritance.md) tutorial later in this series, you'll refactor this code to eliminate the redundancy.</span></span>

<span data-ttu-id="6f0b4-204">可以将多个属性放在一行上，这样你可以编写`HireDate`属性，如下所示：</span><span class="sxs-lookup"><span data-stu-id="6f0b4-204">You can put multiple attributes on one line, so you could also write the `HireDate` attributes as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="6f0b4-205">CourseAssignments 和 OfficeAssignment 导航属性</span><span class="sxs-lookup"><span data-stu-id="6f0b4-205">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="6f0b4-206">`CourseAssignments`和`OfficeAssignment`属性是导航属性。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-206">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="6f0b4-207">一个教师可以教授任意数量的课程，因此`CourseAssignments`定义为集合。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-207">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="6f0b4-208">如果导航属性可以具有多个实体，其类型必须是在其中条目可以添加、 删除和更新的列表。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-208">If a navigation property can hold multiple entities, its type must be a list in which entries can be added, deleted, and updated.</span></span>  <span data-ttu-id="6f0b4-209">你可以指定`ICollection<T>`或类型，如`List<T>`或`HashSet<T>`。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-209">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="6f0b4-210">如果指定`ICollection<T>`，EF 创建`HashSet<T>`默认情况下的集合。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-210">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="6f0b4-211">为何它们仅的原因`CourseAssignment`实体进行了解释下面有关多对多关系的部分。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-211">The reason why these are `CourseAssignment` entities is explained below in the section about many-to-many relationships.</span></span>

<span data-ttu-id="6f0b4-212">Contoso 大学业务规则状态教师只能有一个 office 最多，因此`OfficeAssignment`属性包含单个 OfficeAssignment 实体 （它可能是不分配任何 office 的情况下为 null）。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-212">Contoso University business rules state that an instructor can only have at most one office, so the `OfficeAssignment` property holds a single OfficeAssignment entity (which may be null if no office is assigned).</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="6f0b4-213">创建 OfficeAssignment 实体</span><span class="sxs-lookup"><span data-stu-id="6f0b4-213">Create the OfficeAssignment entity</span></span>

![OfficeAssignment 实体](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="6f0b4-215">创建*Models/OfficeAssignment.cs*替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="6f0b4-215">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

<span data-ttu-id="6f0b4-216">[!code-csharp[Main](intro/samples/cu/Models/OfficeAssignment.cs)]</span><span class="sxs-lookup"><span data-stu-id="6f0b4-216">[!code-csharp[Main](intro/samples/cu/Models/OfficeAssignment.cs)]</span></span>

### <a name="the-key-attribute"></a><span data-ttu-id="6f0b4-217">键属性</span><span class="sxs-lookup"><span data-stu-id="6f0b4-217">The Key attribute</span></span>

<span data-ttu-id="6f0b4-218">没有教师和 OfficeAssignment 实体之间的对零或一一个关系。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-218">There's a one-to-zero-or-one relationship  between the Instructor and the OfficeAssignment entities.</span></span> <span data-ttu-id="6f0b4-219">相对于其分配给，教师仅存在一个办公室分配，因此其主键也是其外的键与 Instructor 实体。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-219">An office assignment only exists in relation to the instructor it's assigned to, and therefore its primary key is also its foreign key to the Instructor entity.</span></span> <span data-ttu-id="6f0b4-220">但是，实体框架无法自动识别 InstructorID 作为此实体的主键因为其名称未遵循 ID 或 classnameID 命名约定。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-220">But the Entity Framework can't automatically recognize InstructorID as the primary key of this entity because its name doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="6f0b4-221">因此，`Key`属性用于标识作为键：</span><span class="sxs-lookup"><span data-stu-id="6f0b4-221">Therefore, the `Key` attribute is used to identify it as the key:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="6f0b4-222">你还可以使用`Key`属性如果实体具有其自己的主键，但你想要提供的名称属性是除 classnameID 或 id。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-222">You can also use the `Key` attribute if the entity does have its own primary key but you want to name the property something other than classnameID or ID.</span></span>

<span data-ttu-id="6f0b4-223">默认情况下，EF 将密钥视为非数据库生成因为列是标识关系。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-223">By default, EF treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="6f0b4-224">教师导航属性</span><span class="sxs-lookup"><span data-stu-id="6f0b4-224">The Instructor navigation property</span></span>

<span data-ttu-id="6f0b4-225">Instructor 实体具有为 null`OfficeAssignment`导航属性 （因为教师可能没有一个办公室分配），并且 OfficeAssignment 实体具有非 null`Instructor`导航属性 （因为一个办公室分配无法存在，而一个教师-`InstructorID`是不可为 null)。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-225">The Instructor entity has a nullable `OfficeAssignment` navigation property (because an instructor might not have an office assignment), and the OfficeAssignment entity has a non-nullable `Instructor` navigation property (because an office assignment can't exist without an instructor -- `InstructorID` is non-nullable).</span></span> <span data-ttu-id="6f0b4-226">当 Instructor 实体具有相关的 OfficeAssignment 实体时，每个实体将具有对另一个在其导航属性的引用。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-226">When an Instructor entity has a related OfficeAssignment entity, each entity will have a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="6f0b4-227">您可以将`[Required]`属性教师导航属性以指定必须有相关的教师，但不需要这样做是因为`InstructorID`外键 （它也是此表的关键） 是不可为 null。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-227">You could put a `[Required]` attribute on the Instructor navigation property to specify that there must be a related instructor, but you don't have to do that because the `InstructorID` foreign key (which is also the key to this table) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="6f0b4-228">修改过程实体</span><span class="sxs-lookup"><span data-stu-id="6f0b4-228">Modify the Course Entity</span></span>

![课程实体](complex-data-model/_static/course-entity.png)

<span data-ttu-id="6f0b4-230">在*Models/Course.cs*，更早版本将你添加的代码替换为下面的代码。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-230">In *Models/Course.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="6f0b4-231">突出显示所做的更改。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-231">The changes are highlighted.</span></span>

<span data-ttu-id="6f0b4-232">[!code-csharp[Main](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]</span><span class="sxs-lookup"><span data-stu-id="6f0b4-232">[!code-csharp[Main](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]</span></span>

<span data-ttu-id="6f0b4-233">课程实体具有外键属性`DepartmentID`它指向相关的部门实体和它具有`Department`导航属性。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-233">The course entity has a foreign key property `DepartmentID` which points to the related Department entity and it has a `Department` navigation property.</span></span>

<span data-ttu-id="6f0b4-234">实体框架不要求你将外键属性添加到你的数据模型，当你具有相关实体的导航属性时。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-234">The Entity Framework doesn't require you to add a foreign key property to your data model when you have a navigation property for a related entity.</span></span>  <span data-ttu-id="6f0b4-235">EF 自动在数据库中创建外键，在需要的位置和创建[隐藏属性](https://docs.microsoft.com/ef/core/modeling/shadow-properties)它们。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-235">EF automatically creates foreign keys in the database wherever they are needed and creates [shadow properties](https://docs.microsoft.com/ef/core/modeling/shadow-properties) for them.</span></span> <span data-ttu-id="6f0b4-236">但是，数据模型中具有外键可以使更新更简单、 更高效。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-236">But having the foreign key in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="6f0b4-237">例如，当你提取要编辑的课程实体，部门实体为 null 时，如果你不加载它，当你更新课程实体中，你将需要来首次读取部门实体。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-237">For example, when you fetch a course entity to edit, the  Department entity is null if you don't load it, so when you update the course entity, you would have to first fetch the Department entity.</span></span> <span data-ttu-id="6f0b4-238">当外键属性`DepartmentID`包含在数据模型中，你无需更新之前提取部门实体。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-238">When the foreign key property `DepartmentID` is included in the data model, you don't need to fetch the Department entity before you update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="6f0b4-239">DatabaseGenerated 属性</span><span class="sxs-lookup"><span data-stu-id="6f0b4-239">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="6f0b4-240">`DatabaseGenerated`特性与`None`参数`CourseID`属性指定主键值是由用户而不是由数据库生成。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-240">The `DatabaseGenerated` attribute with the `None` parameter on the `CourseID` property specifies that primary key values are provided by the user rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="6f0b4-241">默认情况下，实体框架将假定主键值都由数据库。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-241">By default, Entity Framework assumes that primary key values are generated by the database.</span></span> <span data-ttu-id="6f0b4-242">在大多数情况下要执行的操作。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-242">That's what you want in most scenarios.</span></span> <span data-ttu-id="6f0b4-243">但是，对于课程实体，将一个部门，另一个部门，一 2000年系列使用用户指定过程号例如 1000年系列，依此类推。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-243">However, for Course entities, you'll use a user-specified course number such as a 1000 series for one department, a 2000 series for another department, and so on.</span></span>

<span data-ttu-id="6f0b4-244">`DatabaseGenerated`属性还可以用于生成默认值，如在数据库列用于记录的日期的情况下创建或更新行。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-244">The `DatabaseGenerated` attribute can also be used to generate default values, as in the case of database columns used to record the date a row was created or updated.</span></span>  <span data-ttu-id="6f0b4-245">有关详细信息，请参阅[生成属性](https://docs.microsoft.com/ef/core/modeling/generated-properties)。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-245">For more information, see [Generated Properties](https://docs.microsoft.com/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="6f0b4-246">外键和导航属性</span><span class="sxs-lookup"><span data-stu-id="6f0b4-246">Foreign key and navigation properties</span></span>

<span data-ttu-id="6f0b4-247">外键属性和课程实体中的导航属性反映了以下关系：</span><span class="sxs-lookup"><span data-stu-id="6f0b4-247">The foreign key properties and navigation properties in the Course entity reflect the following relationships:</span></span>

<span data-ttu-id="6f0b4-248">课程都将分配到一个部门，因此没有`DepartmentID`外键和一个`Department`上面提到的原因的导航属性。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-248">A course is assigned to one department, so there's a `DepartmentID` foreign key and a `Department` navigation property for the reasons mentioned above.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="6f0b4-249">课程可以有任意数量的学生注册，因此`Enrollments`导航属性是集合：</span><span class="sxs-lookup"><span data-stu-id="6f0b4-249">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="6f0b4-250">可能由多个教师讲授课程因此`CourseAssignments`导航属性是集合 (类型`CourseAssignment`解释了[更高版本](#many-to-many-relationships)):</span><span class="sxs-lookup"><span data-stu-id="6f0b4-250">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection (the type `CourseAssignment` is explained [later](#many-to-many-relationships)):</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-the-department-entity"></a><span data-ttu-id="6f0b4-251">创建部门实体</span><span class="sxs-lookup"><span data-stu-id="6f0b4-251">Create the Department entity</span></span>

![部门实体](complex-data-model/_static/department-entity.png)


<span data-ttu-id="6f0b4-253">创建*Models/Department.cs*替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="6f0b4-253">Create *Models/Department.cs* with the following code:</span></span>

<span data-ttu-id="6f0b4-254">[!code-csharp[Main](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]</span><span class="sxs-lookup"><span data-stu-id="6f0b4-254">[!code-csharp[Main](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="6f0b4-255">列属性</span><span class="sxs-lookup"><span data-stu-id="6f0b4-255">The Column attribute</span></span>

<span data-ttu-id="6f0b4-256">前面使用`Column`特性更改列名称映射。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-256">Earlier you used the `Column` attribute to change column name mapping.</span></span> <span data-ttu-id="6f0b4-257">在代码中的部门实体，`Column`属性用于更改 SQL 数据类型映射，以便将将列定义的数据库中使用 SQL Server money 类型：</span><span class="sxs-lookup"><span data-stu-id="6f0b4-257">In the code for the Department entity, the `Column` attribute is being used to change SQL data type mapping so that the column will be defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="6f0b4-258">列映射通常不是必需的因为实体框架选择基于你定义的属性的 CLR 类型的相应 SQL Server 数据类型。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-258">Column mapping is generally not required, because the Entity Framework chooses the appropriate SQL Server data type based on the CLR type that you define for the property.</span></span> <span data-ttu-id="6f0b4-259">CLR`decimal`类型映射到 SQL Server`decimal`类型。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-259">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="6f0b4-260">但在这种情况下你知道保存在列中的货币金额和 money 数据类型是更适合。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-260">But in this case you know that the column will be holding currency amounts, and the money data type is more appropriate for that.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="6f0b4-261">外键和导航属性</span><span class="sxs-lookup"><span data-stu-id="6f0b4-261">Foreign key and navigation properties</span></span>

<span data-ttu-id="6f0b4-262">外键和导航属性反映了以下关系：</span><span class="sxs-lookup"><span data-stu-id="6f0b4-262">The foreign key and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="6f0b4-263">部门可能或可能没有管理员，并且管理员始终是一个教师。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-263">A department may or may not have an administrator, and an administrator is always an instructor.</span></span> <span data-ttu-id="6f0b4-264">因此`InstructorID`作为外键与 Instructor 实体中，还包括的属性和之后添加一个问号`int`键入标记用于将标记为可为 null 的属性。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-264">Therefore the `InstructorID` property is included as the foreign key to the Instructor entity, and a question mark is added after the `int` type designation to mark the property as nullable.</span></span> <span data-ttu-id="6f0b4-265">导航属性名为`Administrator`但保留 Instructor 实体：</span><span class="sxs-lookup"><span data-stu-id="6f0b4-265">The navigation property is named `Administrator` but holds an Instructor entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="6f0b4-266">部门可能会产生许多课程，因此课程导航属性：</span><span class="sxs-lookup"><span data-stu-id="6f0b4-266">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> <span data-ttu-id="6f0b4-267">按照约定，实体框架使级联删除对于不可为 null 的外键以及多对多关系。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-267">By convention, the Entity Framework enables cascade delete for non-nullable foreign keys and for many-to-many relationships.</span></span> <span data-ttu-id="6f0b4-268">这可能导致循环的级联删除规则，当你尝试添加迁移时，将导致异常。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-268">This can result in circular cascade delete rules, which will cause an exception when you try to add a migration.</span></span> <span data-ttu-id="6f0b4-269">例如，如果你未定义 Department.InstructorID 属性为可为 null，EF 会配置一个级联删除规则，以删除部门，这不是你希望能够发生时删除教师。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-269">For example, if you didn't define the Department.InstructorID property as nullable, EF would configure a cascade delete rule to delete the instructor when you delete the department, which is not what you want to have happen.</span></span> <span data-ttu-id="6f0b4-270">如果您的业务规则需要`InstructorID`属性不可为 null，你必须使用以下 fluent API 语句若要禁用的关系上的级联删除：</span><span class="sxs-lookup"><span data-stu-id="6f0b4-270">If your business rules required the `InstructorID` property to be non-nullable, you would have to use the following fluent API statement to disable cascade delete on the relationship:</span></span>
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-the-enrollment-entity"></a><span data-ttu-id="6f0b4-271">修改注册实体</span><span class="sxs-lookup"><span data-stu-id="6f0b4-271">Modify the Enrollment entity</span></span>

![注册实体](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="6f0b4-273">在*Models/Enrollment.cs*，你添加的代码更早版本替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="6f0b4-273">In *Models/Enrollment.cs*, replace the code you added earlier with the following code:</span></span>

<span data-ttu-id="6f0b4-274">[!code-csharp[Main](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]</span><span class="sxs-lookup"><span data-stu-id="6f0b4-274">[!code-csharp[Main](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="6f0b4-275">外键和导航属性</span><span class="sxs-lookup"><span data-stu-id="6f0b4-275">Foreign key and navigation properties</span></span>

<span data-ttu-id="6f0b4-276">外键属性和导航属性反映了以下关系：</span><span class="sxs-lookup"><span data-stu-id="6f0b4-276">The foreign key properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="6f0b4-277">注册记录已经是单个的课程，因此`CourseID`外键属性和`Course`导航属性：</span><span class="sxs-lookup"><span data-stu-id="6f0b4-277">An enrollment record is for a single course, so there's a `CourseID` foreign key property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="6f0b4-278">因此，注册记录将适用于单个学生，`StudentID`外键属性和`Student`导航属性：</span><span class="sxs-lookup"><span data-stu-id="6f0b4-278">An enrollment record is for a single student, so there's a `StudentID` foreign key property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="6f0b4-279">多对多关系</span><span class="sxs-lookup"><span data-stu-id="6f0b4-279">Many-to-Many Relationships</span></span>

<span data-ttu-id="6f0b4-280">学生和课程，则为实体之间没有多对多关系和注册实体函数为多对多联接表*具有负载*数据库中。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-280">There's a many-to-many relationship between the Student and Course entities, and the Enrollment entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="6f0b4-281">"负载"意味着注册表包含除 （在这种情况下，主键和年级属性） 的联接的表的外键以外的其他数据。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-281">"With payload" means that the Enrollment table contains additional data besides foreign keys for the joined tables (in this case, a primary key and a Grade property).</span></span>

<span data-ttu-id="6f0b4-282">下图显示这些关系中的实体关系图的外观。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-282">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="6f0b4-283">(此关系图生成使用实体框架 Power Tools for EF 6.x; 创建关系图不属于本教程，只需使用此处为说明。)</span><span class="sxs-lookup"><span data-stu-id="6f0b4-283">(This diagram was generated using the Entity Framework Power Tools for EF 6.x; creating the diagram isn't part of the tutorial, it's just being used here as an illustration.)</span></span>

![学生课程多对多关系](complex-data-model/_static/student-course.png)

<span data-ttu-id="6f0b4-285">每个关系行已在其他，，指示一个对多关系一端和星号 （*） 1。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-285">Each relationship line has a 1 at one end and an asterisk (*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="6f0b4-286">如果注册表没有包括年级信息，它将只需包含两个的外键 CourseID 和 StudentID。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-286">If the Enrollment table didn't include grade information, it would only need to contain the two foreign keys CourseID and StudentID.</span></span> <span data-ttu-id="6f0b4-287">在这种情况下，它将在数据库中是没有负载的多对多联接表 （或纯联接表）。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-287">In that case, it would be a many-to-many join table without payload (or a pure join table) in the database.</span></span> <span data-ttu-id="6f0b4-288">教师和课程实体具有这种多对多关系，并且你下一步是创建一个实体类能够充当没有负载的联接表。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-288">The Instructor and Course entities have that kind of many-to-many relationship, and your next step is to create an entity class to function as a join table without payload.</span></span>

<span data-ttu-id="6f0b4-289">（隐式联接表的多对多关系，但 EF 核心不 EF 6.x 支持。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-289">(EF 6.x supports implicit join tables for many-to-many relationships, but EF Core does not.</span></span> <span data-ttu-id="6f0b4-290">有关详细信息，请参阅[EF 核心 GitHub 存储库中的讨论](https://github.com/aspnet/EntityFramework/issues/1368)。)</span><span class="sxs-lookup"><span data-stu-id="6f0b4-290">For more information, see the [discussion in the EF Core GitHub repository](https://github.com/aspnet/EntityFramework/issues/1368).)</span></span> 

## <a name="the-courseassignment-entity"></a><span data-ttu-id="6f0b4-291">CourseAssignment 实体</span><span class="sxs-lookup"><span data-stu-id="6f0b4-291">The CourseAssignment entity</span></span>

![CourseAssignment 实体](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="6f0b4-293">创建*Models/CourseAssignment.cs*替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="6f0b4-293">Create *Models/CourseAssignment.cs* with the following code:</span></span>

<span data-ttu-id="6f0b4-294">[!code-csharp[Main](intro/samples/cu/Models/CourseAssignment.cs)]</span><span class="sxs-lookup"><span data-stu-id="6f0b4-294">[!code-csharp[Main](intro/samples/cu/Models/CourseAssignment.cs)]</span></span>

### <a name="join-entity-names"></a><span data-ttu-id="6f0b4-295">加入实体名称</span><span class="sxs-lookup"><span data-stu-id="6f0b4-295">Join entity names</span></span>

<span data-ttu-id="6f0b4-296">联接表需要在数据库中为教师课程多对多关系，并且它必须由一个实体集。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-296">A join table is required in the database for the Instructor-to-Courses many-to-many relationship, and it has to be represented by an entity set.</span></span> <span data-ttu-id="6f0b4-297">很普遍命名联接实体`EntityName1EntityName2`，这样在这种情况下具有`CourseInstructor`。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-297">It's common to name a join entity `EntityName1EntityName2`, which in this case would be `CourseInstructor`.</span></span> <span data-ttu-id="6f0b4-298">但是，我们建议你选择的名称。 描述的关系。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-298">However, we recommend that you choose a name that describes the relationship.</span></span> <span data-ttu-id="6f0b4-299">数据模型按简单启动和扩展，有无负载联接频繁收到的负载。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-299">Data models start out simple and grow, with no-payload joins frequently getting payloads later.</span></span> <span data-ttu-id="6f0b4-300">如果启动具有描述性的实体名称时，不需要以后更改此名称。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-300">If you start with a descriptive entity name, you won't have to change the name later.</span></span> <span data-ttu-id="6f0b4-301">理想情况下，联接实体将业务域中具有其自己自然的 （可能是单个单词） 名称。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-301">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="6f0b4-302">例如，丛书客户无法链接通过分级。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-302">For example, Books and Customers could be linked through Ratings.</span></span> <span data-ttu-id="6f0b4-303">为此关系，`CourseAssignment`是更好的选择比`CourseInstructor`。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-303">For this relationship, `CourseAssignment` is a better choice than `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="6f0b4-304">复合密钥</span><span class="sxs-lookup"><span data-stu-id="6f0b4-304">Composite key</span></span>

<span data-ttu-id="6f0b4-305">由于外键不是可以为 null 并且它们一起唯一地标识表的每一行，则无需单独的主键。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-305">Since the foreign keys are not nullable and together uniquely identify each row of the table, there is no need for a separate primary key.</span></span> <span data-ttu-id="6f0b4-306">*InstructorID*和*CourseID*属性应充当复合主键。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-306">The *InstructorID* and *CourseID* properties should function as a composite primary key.</span></span> <span data-ttu-id="6f0b4-307">标识到 EF 的复合主键的唯一方法是使用*fluent API* （它不能通过使用属性来完成）。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-307">The only way to identify composite primary keys to EF is by using the *fluent API* (it can't be done by using attributes).</span></span> <span data-ttu-id="6f0b4-308">你将看到如何在下一节中配置的复合主键。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-308">You'll see how to configure the composite primary key in the next section.</span></span>

<span data-ttu-id="6f0b4-309">复合密钥可确保该虽然都有一个过程中，以及一个教师的多个行的多个行，不能具有多个行的同一教师和过程。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-309">The composite key ensures that while you can have multiple rows for one course, and multiple rows for one instructor, you can't have multiple rows for the same instructor and course.</span></span> <span data-ttu-id="6f0b4-310">`Enrollment`联接实体定义其自己的主键，因此可能会出现这种重复项。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-310">The `Enrollment` join entity defines its own primary key, so duplicates of this sort are possible.</span></span> <span data-ttu-id="6f0b4-311">若要防止此类重复项，你无法上的外键字段，添加一个唯一索引，或配置`Enrollment`带有复合主键类似于`CourseAssignment`。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-311">To prevent such duplicates, you could add a unique index on the foreign key fields, or configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="6f0b4-312">有关详细信息，请参阅[索引](https://docs.efproject.net/en/latest/modeling/indexes.html)。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-312">For more information, see [Indexes](https://docs.efproject.net/en/latest/modeling/indexes.html).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="6f0b4-313">更新数据库上下文</span><span class="sxs-lookup"><span data-stu-id="6f0b4-313">Update the database context</span></span>

<span data-ttu-id="6f0b4-314">添加以下突出显示的代码*Data/SchoolContext.cs*文件：</span><span class="sxs-lookup"><span data-stu-id="6f0b4-314">Add the following highlighted code to the *Data/SchoolContext.cs* file:</span></span>

<span data-ttu-id="6f0b4-315">[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]</span><span class="sxs-lookup"><span data-stu-id="6f0b4-315">[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]</span></span>

<span data-ttu-id="6f0b4-316">此代码将添加新的实体，并配置 CourseAssignment 实体的复合主键。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-316">This code adds the new entities and configures the CourseAssignment entity's composite primary key.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="6f0b4-317">属性 fluent API 替代方法</span><span class="sxs-lookup"><span data-stu-id="6f0b4-317">Fluent API alternative to attributes</span></span>

<span data-ttu-id="6f0b4-318">中的代码`OnModelCreating`方法`DbContext`类使用*fluent API*配置 EF 行为。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-318">The code in the `OnModelCreating` method of the `DbContext` class uses the *fluent API* to configure EF behavior.</span></span> <span data-ttu-id="6f0b4-319">API 称为"fluent"，因为它通常用于通过连接到单个语句中，从如此示例所示的一系列方法调用一起[EF 核心文档](https://docs.microsoft.com/ef/core/modeling/#methods-of-configuration):</span><span class="sxs-lookup"><span data-stu-id="6f0b4-319">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement, as in this example from the [EF Core documentation](https://docs.microsoft.com/ef/core/modeling/#methods-of-configuration):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="6f0b4-320">在本教程中，仅为不能具有属性的数据库映射使用 fluent API。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-320">In this tutorial, you're using the fluent API only for database mapping that you can't do with attributes.</span></span> <span data-ttu-id="6f0b4-321">但是，你可以使用 fluent API 指定的格式、 验证和映射规则，你可以通过使用特性执行大多数。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-321">However, you can also use the fluent API to specify most of the formatting, validation, and mapping rules that you can do by using attributes.</span></span> <span data-ttu-id="6f0b4-322">某些属性，如`MinimumLength`不能使用 fluent API 应用。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-322">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="6f0b4-323">如前所述，`MinimumLength`不会更改架构，它仅适用于客户端和服务器端验证规则。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-323">As mentioned previously, `MinimumLength` doesn't change the schema, it only applies a client and server side validation rule.</span></span>

<span data-ttu-id="6f0b4-324">一些开发人员更愿意使用 fluent API 以独占方式，以便它们能够获得它们的实体类"清理"。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-324">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="6f0b4-325">如果你想，并且有几个仅可由使用 fluent API 中完成的自定义项，可以混合属性和 fluent API，但建议的做法通常是选择这两种方法之一，并使用该一致地尽可能多地。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-325">You can mix attributes and fluent API if you want, and there are a few customizations that can only be done by using fluent API, but in general the recommended practice is to choose one of these two approaches and use that consistently as much as possible.</span></span> <span data-ttu-id="6f0b4-326">不要使用这两者时，请注意，每当存在冲突，则 Fluent API 将覆盖属性。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-326">If you do use both, note that wherever there is a conflict, Fluent API overrides attributes.</span></span>

<span data-ttu-id="6f0b4-327">有关与 fluent API 的特性的详细信息，请参阅[方法配置](https://docs.microsoft.com/ef/core/modeling/#methods-of-configuration)。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-327">For more information about attributes vs. fluent API, see [Methods of configuration](https://docs.microsoft.com/ef/core/modeling/#methods-of-configuration).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="6f0b4-328">关系图显示关系的实体</span><span class="sxs-lookup"><span data-stu-id="6f0b4-328">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="6f0b4-329">下图显示为已完成的 School 模型的实体框架 Power 工具创建的关系图。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-329">The following illustration shows the diagram that the Entity Framework Power Tools create for the completed School model.</span></span>

![实体关系图](complex-data-model/_static/diagram.png)

<span data-ttu-id="6f0b4-331">除了一个对多关系线 (1 到\*)，你可以在此处看到教师和 OfficeAssignment 实体和零-或--一对多关系行之间的对零或一一个关系行 (1 对 0..1) (0..1 对 *) 之间教师和部门实体。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-331">Besides the one-to-many relationship lines (1 to \*), you can see here the one-to-zero-or-one relationship line (1 to 0..1) between the Instructor and OfficeAssignment entities and the zero-or-one-to-many relationship line (0..1 to *) between the Instructor and Department entities.</span></span>

## <a name="seed-the-database-with-test-data"></a><span data-ttu-id="6f0b4-332">种子使用测试数据的数据库</span><span class="sxs-lookup"><span data-stu-id="6f0b4-332">Seed the Database with Test Data</span></span>

<span data-ttu-id="6f0b4-333">中的代码替换*Data/DbInitializer.cs*文件替换为以下代码，以便为你已创建的新实体提供种子数据。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-333">Replace the code in the *Data/DbInitializer.cs* file with the following code in order to provide seed data for the new entities you've created.</span></span>

<span data-ttu-id="6f0b4-334">[!code-csharp[Main](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]</span><span class="sxs-lookup"><span data-stu-id="6f0b4-334">[!code-csharp[Main](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]</span></span>

<span data-ttu-id="6f0b4-335">正如你看到的第一个教程中，大多数的此代码将只需创建新的实体对象，并将示例数据加载到所需的测试的属性。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-335">As you saw in the first tutorial, most of this code simply creates new entity objects and loads sample data into properties as required for testing.</span></span> <span data-ttu-id="6f0b4-336">请注意如何处理的多对多关系： 该代码通过创建中的实体创建关系`Enrollments`和`CourseAssignment`加入实体集。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-336">Notice how the many-to-many relationships are handled: the code creates relationships by creating entities in the `Enrollments` and `CourseAssignment` join entity sets.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="6f0b4-337">添加迁移</span><span class="sxs-lookup"><span data-stu-id="6f0b4-337">Add a migration</span></span>

<span data-ttu-id="6f0b4-338">保存所做的更改并生成项目。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-338">Save your changes and build the project.</span></span> <span data-ttu-id="6f0b4-339">然后在项目文件夹中打开命令窗口并输入`migrations add`命令 （不执行操作的更新 database 命令尚未）：</span><span class="sxs-lookup"><span data-stu-id="6f0b4-339">Then open the command window in the project folder and enter the `migrations add` command (don't do the update-database command yet):</span></span>

```console
dotnet ef migrations add ComplexDataModel
```

<span data-ttu-id="6f0b4-340">获取有关可能造成数据丢失的警告。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-340">You get a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="6f0b4-341">如果你尝试运行`database update`此时命令 （不执行此操作尚未），则会遇到以下错误：</span><span class="sxs-lookup"><span data-stu-id="6f0b4-341">If you tried to run the `database update` command at this point (don't do it yet), you would get the following error:</span></span>

> <span data-ttu-id="6f0b4-342">ALTER TABLE 语句与外键约束"FK_dbo 冲突。Course_dbo。Department_DepartmentID"。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-342">The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID".</span></span> <span data-ttu-id="6f0b4-343">冲突发生于数据库"ContosoUniversity"表"dbo。部门"，列 DepartmentID。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-343">The conflict occurred in database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.</span></span>

<span data-ttu-id="6f0b4-344">有时时执行现有数据的迁移，你需要将存根 （stub） 数据插入数据库以满足外键约束。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-344">Sometimes when you execute migrations with existing data, you need to insert stub data into the database to satisfy foreign key constraints.</span></span> <span data-ttu-id="6f0b4-345">生成的代码`Up`方法将不可为 null 的 DepartmentID 外键添加到过程表。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-345">The generated code in the `Up` method adds a non-nullable DepartmentID foreign key to the Course table.</span></span> <span data-ttu-id="6f0b4-346">如果已有行课程表中的代码运行时，`AddColumn`操作失败，因为 SQL Server 并不知道要放入中不能为 null 的列的值。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-346">If there are already rows in the Course table when the code runs, the `AddColumn` operation fails because SQL Server doesn't know what value to put in the column that can't be null.</span></span> <span data-ttu-id="6f0b4-347">对于本教程将运行迁移，在新的数据库，但在生产应用程序将需要进行迁移处理现有数据，因此下面的说明展示一个如何执行该操作的示例。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-347">For this tutorial you'll run the migration on a new database, but in a production application you'd have to make the migration handle existing data, so the following directions show an example of how to do that.</span></span>

<span data-ttu-id="6f0b4-348">若要使这种迁移处理你需要更改代码以提供默认值的新列的现有数据并创建一个存根 （stub） 部门名为"Temp"使其作为默认部门。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-348">To make this migration work with existing data you have to change the code to give the new column a default value, and create a stub department named "Temp" to act as the default department.</span></span> <span data-ttu-id="6f0b4-349">因此，现有的课程行将所有与"Temp"部门后`Up`方法运行。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-349">As a result, existing Course rows will all be related to the "Temp" department after the `Up` method runs.</span></span>

* <span data-ttu-id="6f0b4-350">打开*{timestamp}_ComplexDataModel.cs*文件。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-350">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span> 

* <span data-ttu-id="6f0b4-351">注释掉的将 DepartmentID 列添加到过程表的代码行。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-351">Comment out the line of code that adds the DepartmentID column to the Course table.</span></span>

  <span data-ttu-id="6f0b4-352">[!code-csharp[Main](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]</span><span class="sxs-lookup"><span data-stu-id="6f0b4-352">[!code-csharp[Main](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]</span></span>

* <span data-ttu-id="6f0b4-353">创建将 Department 表的代码后面添加以下突出显示的代码：</span><span class="sxs-lookup"><span data-stu-id="6f0b4-353">Add the following highlighted code after the code that creates the Department table:</span></span>

  <span data-ttu-id="6f0b4-354">[!code-csharp[Main](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]</span><span class="sxs-lookup"><span data-stu-id="6f0b4-354">[!code-csharp[Main](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]</span></span>

<span data-ttu-id="6f0b4-355">在生产应用程序，你将编写代码或脚本添加部门行并与新的部门行相关课程行。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-355">In a production application, you would write code or scripts to add Department rows and relate Course rows to the new Department rows.</span></span> <span data-ttu-id="6f0b4-356">您然后不再需要"Temp"部门或 Course.DepartmentID 列上的默认值。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-356">You would then no longer need the "Temp" department or the default value on the Course.DepartmentID column.</span></span>

<span data-ttu-id="6f0b4-357">保存所做的更改并生成项目。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-357">Save your changes and build the project.</span></span>

## <a name="change-the-connection-string-and-update-the-database"></a><span data-ttu-id="6f0b4-358">更改连接字符串，并更新数据库</span><span class="sxs-lookup"><span data-stu-id="6f0b4-358">Change the connection string and update the database</span></span>

<span data-ttu-id="6f0b4-359">你现在有新的代码`DbInitializer`将新实体的种子数据添加到空数据库的类。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-359">You now have new code in the `DbInitializer` class that adds seed data for the new entities to an empty database.</span></span> <span data-ttu-id="6f0b4-360">若要使 EF 创建新的空数据库，更改中的连接字符串中的数据库的名称*appsettings.json*到 ContosoUniversity3 或尚未在所使用的计算机使用的一些其他名称。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-360">To make EF create a new empty database, change the name of the database in the connection string in *appsettings.json* to ContosoUniversity3 or some other name that you haven't used on the computer you're using.</span></span>

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

<span data-ttu-id="6f0b4-361">保存到你更改*appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-361">Save your change to *appsettings.json*.</span></span>

> [!NOTE]
> <span data-ttu-id="6f0b4-362">作为对不断变化的数据库名称的替代方法，您可以删除数据库。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-362">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="6f0b4-363">使用**SQL Server 对象资源管理器**(SSOX) 或`database drop`CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="6f0b4-363">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
> ```console
> dotnet ef database drop
> ```

<span data-ttu-id="6f0b4-364">已更改的数据库名称或删除了数据库后，运行`database update`命令在命令窗口中执行迁移。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-364">After you have changed the database name or deleted the database, run the `database update` command in the command window to execute the migrations.</span></span>

```console
dotnet ef database update
```

<span data-ttu-id="6f0b4-365">运行应用，以导致`DbInitializer.Initialize`方法运行并填充新数据库。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-365">Run the app to cause the `DbInitializer.Initialize` method to run and populate the new database.</span></span>

<span data-ttu-id="6f0b4-366">更早版本，就像在 SSOX 中打开数据库并展开**表**节点以查看是否已创建的所有表。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-366">Open the database in SSOX as you did earlier, and expand the **Tables** node to see that all of the tables have been created.</span></span> <span data-ttu-id="6f0b4-367">（如果您还有 SSOX 打开从较早的时间，请单击刷新按钮。）</span><span class="sxs-lookup"><span data-stu-id="6f0b4-367">(If you still have SSOX open from the earlier time, click the Refresh button.)</span></span>

![在 SSOX 中的表](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="6f0b4-369">运行应用程序来触发设定种子数据库初始值设定项代码。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-369">Run the application to trigger the initializer code that seeds the database.</span></span>

<span data-ttu-id="6f0b4-370">右键单击**CourseAssignment**表，然后选择**查看数据**以验证它中有数据。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-370">Right-click the **CourseAssignment** table and select **View Data** to verify that it has data in it.</span></span>

![在 SSOX 中 CourseAssignment 数据](complex-data-model/_static/ssox-ci-data.png)

## <a name="summary"></a><span data-ttu-id="6f0b4-372">摘要</span><span class="sxs-lookup"><span data-stu-id="6f0b4-372">Summary</span></span>

<span data-ttu-id="6f0b4-373">现在将具有更复杂的数据模型和相应的数据库。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-373">You now have a more complex data model and corresponding database.</span></span> <span data-ttu-id="6f0b4-374">在以下教程中，你将了解有关如何访问相关的数据的详细信息。</span><span class="sxs-lookup"><span data-stu-id="6f0b4-374">In the following tutorial, you'll learn more about how to access related data.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="6f0b4-375">[上一页](migrations.md)
[下一页](read-related-data.md)</span><span class="sxs-lookup"><span data-stu-id="6f0b4-375">[Previous](migrations.md)
[Next](read-related-data.md)</span></span>  
