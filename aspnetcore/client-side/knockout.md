---
title: "在 ASP.NET Core Knockout.js MVVM Framework"
author: ardalis
description: 
keywords: "ASP.NET 核心"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: b20e3b23-1c51-47bf-adac-91b5048567e0
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/knockout
ms.openlocfilehash: 87b4fdc86f6bb870ae0a8cc85688a549fd0740ac
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="knockoutjs-mvvm-framework-in-aspnet-core"></a><span data-ttu-id="df477-103">在 ASP.NET Core Knockout.js MVVM Framework</span><span class="sxs-lookup"><span data-stu-id="df477-103">Knockout.js MVVM Framework in ASP.NET Core</span></span>

<span data-ttu-id="df477-104">通过[Steve Smith](http://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="df477-104">By [Steve Smith](http://ardalis.com)</span></span>

<span data-ttu-id="df477-105">Knockout 是一种常用的 JavaScript 库，简化复杂的基于数据的用户界面的创建。</span><span class="sxs-lookup"><span data-stu-id="df477-105">Knockout is a popular JavaScript library that simplifies the creation of complex data-based user interfaces.</span></span> <span data-ttu-id="df477-106">它可单独使用或与其他库，例如 jQuery。</span><span class="sxs-lookup"><span data-stu-id="df477-106">It can be used alone or with other libraries, such as jQuery.</span></span> <span data-ttu-id="df477-107">其主要目的是要绑定到基础数据模型为 JavaScript 对象，定义的 UI 元素，以便时对 UI 进行更改，将更新模型，反之亦然。</span><span class="sxs-lookup"><span data-stu-id="df477-107">Its primary purpose is to bind UI elements to an underlying data model defined as a JavaScript object, such that when changes are made to the UI, the model is updated, and vice versa.</span></span> <span data-ttu-id="df477-108">Knockout 便于在 web 应用程序的客户端行为模型-视图-视图模型 (MVVM) 模式的使用。</span><span class="sxs-lookup"><span data-stu-id="df477-108">Knockout facilitates the use of a Model-View-ViewModel (MVVM) pattern in a web application's client-side behavior.</span></span> <span data-ttu-id="df477-109">其中一个必须了解在使用 Knockout 的 MVVM 实现的两个主要概念是可观察对象和绑定。</span><span class="sxs-lookup"><span data-stu-id="df477-109">The two main concepts one must learn when working with Knockout's MVVM implementation are Observables and Bindings.</span></span>

## <a name="getting-started"></a><span data-ttu-id="df477-110">入门</span><span class="sxs-lookup"><span data-stu-id="df477-110">Getting started</span></span>

<span data-ttu-id="df477-111">Knockout 部署为一个 JavaScript 文件中，因此安装和使用它是非常简单使用[bower](bower.md)。</span><span class="sxs-lookup"><span data-stu-id="df477-111">Knockout is deployed as a single JavaScript file, so installing and using it is very straightforward using [bower](bower.md).</span></span> <span data-ttu-id="df477-112">假设您已有[bower](bower.md)和[gulp](using-gulp.md)配置，打开*bower.json*中 ASP.NET Core 项目，并添加 knockout 依赖项，如下所示：</span><span class="sxs-lookup"><span data-stu-id="df477-112">Assuming you already have [bower](bower.md) and [gulp](using-gulp.md) configured, open *bower.json* in your ASP.NET Core project and add the knockout dependency as shown here:</span></span>

```json
{
  "name": "KnockoutDemo",
  "private": true,
  "dependencies": {
    "knockout" : "^3.3.0"
  },
  "exportsOverride": {
  }
}
```

<span data-ttu-id="df477-113">与此就地，随后可以手动运行 bower 方法是打开任务运行程序资源管理器 （在下视图 ‣ 其他窗口 ‣ 任务运行程序资源管理器），然后在任务，右键单击在 bower 上并选择运行。</span><span class="sxs-lookup"><span data-stu-id="df477-113">With this in place, you can then manually run bower by opening the Task Runner Explorer (under View ‣ Other Windows ‣ Task Runner Explorer) and then under Tasks, right-click on bower and select Run.</span></span> <span data-ttu-id="df477-114">结果应显示类似如下：</span><span class="sxs-lookup"><span data-stu-id="df477-114">The result should appear similar to this:</span></span>

![bower 运行 knockout 在任务运行程序资源管理器](knockout/_static/bower-knockout.png)

<span data-ttu-id="df477-116">现在，如果你的项目中查找`wwwroot`文件夹中，你应看到 knockout 安装的 lib 文件夹下。</span><span class="sxs-lookup"><span data-stu-id="df477-116">Now if you look in your project's `wwwroot` folder, you should see knockout installed under the lib folder.</span></span>

![安装在 lib 文件夹中的 knockout](knockout/_static/wwwroot-knockout.png)

<span data-ttu-id="df477-118">建议在生产环境中引用 knockout 通过内容传送网络或 CDN，因为这会增加你的用户已有该文件的缓存的副本，因此根本下载不需要的可能性。</span><span class="sxs-lookup"><span data-stu-id="df477-118">It's recommended that in your production environment you reference knockout via a Content Delivery Network, or CDN, as this increases the likelihood that your users will already have a cached copy of the file and thus will not need to download it at all.</span></span> <span data-ttu-id="df477-119">Knockout 位于多个 Cdn，包括 Microsoft Ajax CDN，此处：</span><span class="sxs-lookup"><span data-stu-id="df477-119">Knockout is available on several CDNs, including the Microsoft Ajax CDN, here:</span></span>

[<span data-ttu-id="df477-120">http://ajax.aspnetcdn.com/ajax/knockout/knockout-3.3.0.js</span><span class="sxs-lookup"><span data-stu-id="df477-120">http://ajax.aspnetcdn.com/ajax/knockout/knockout-3.3.0.js</span></span>](http://ajax.aspnetcdn.com/ajax/knockout/knockout-3.3.0.js)

<span data-ttu-id="df477-121">若要将使用它的页面上包含 Knockout，只需添加`<script>`从任何位置你将托管它 （使用你的应用程序，或通过 CDN） 中引用该文件的元素：</span><span class="sxs-lookup"><span data-stu-id="df477-121">To include Knockout on a page that will use it, simply add a `<script>` element referencing the file from wherever you will be hosting it (with your application, or via a CDN):</span></span>

```html
<script type="text/javascript" src="knockout-3.3.0.js"></script>
```

## <a name="observables-viewmodels-and-simple-binding"></a><span data-ttu-id="df477-122">可观察对象、 Viewmodel 和简单绑定</span><span class="sxs-lookup"><span data-stu-id="df477-122">Observables, ViewModels, and simple binding</span></span>

<span data-ttu-id="df477-123">你可能已经熟悉使用 JavaScript 来操作在网页上，是指在通过直接访问 DOM 元素或使用 jQuery 等的库。</span><span class="sxs-lookup"><span data-stu-id="df477-123">You may already be familiar with using JavaScript to manipulate elements on a web page, either via direct access to the DOM or using a library like jQuery.</span></span> <span data-ttu-id="df477-124">通常这种行为被通过编写代码以直接在对某些用户操作的响应中设置的元素值。</span><span class="sxs-lookup"><span data-stu-id="df477-124">Typically this kind of behavior is achieved by writing code to directly set element values in response to certain user actions.</span></span> <span data-ttu-id="df477-125">使用 Knockout，声明性方法则改为采用，通过该页面上的元素绑定到的属性对象。</span><span class="sxs-lookup"><span data-stu-id="df477-125">With Knockout, a declarative approach is taken instead, through which elements on the page are bound to properties on an object.</span></span> <span data-ttu-id="df477-126">而不是编写代码来操作 DOM 元素，与 ViewModel 对象，只需交互用户操作和 Knockout 负责确保同步页元素。</span><span class="sxs-lookup"><span data-stu-id="df477-126">Instead of writing code to manipulate DOM elements, user actions simply interact with the ViewModel object, and Knockout takes care of ensuring the page elements are synchronized.</span></span>

<span data-ttu-id="df477-127">作为一个简单的示例，请考虑下面的页列表。</span><span class="sxs-lookup"><span data-stu-id="df477-127">As a simple example, consider the page list below.</span></span> <span data-ttu-id="df477-128">它包括`<span>`具有元素`data-bind`属性，该值指示文本内容，应绑定到作者姓名。</span><span class="sxs-lookup"><span data-stu-id="df477-128">It includes a `<span>` element with a `data-bind` attribute indicating that the text content should be bound to authorName.</span></span> <span data-ttu-id="df477-129">接下来，在使用单个属性，定义变量 viewModel JavaScript 块`authorName`，设置为某个值。</span><span class="sxs-lookup"><span data-stu-id="df477-129">Next, in a JavaScript block a variable viewModel is defined with a single property, `authorName`, set to some value.</span></span> <span data-ttu-id="df477-130">最后，调用`ko.applyBindings`由传入此 viewModel 变量。</span><span class="sxs-lookup"><span data-stu-id="df477-130">Finally, a call to `ko.applyBindings` is made, passing in this viewModel variable.</span></span>

```html
<html>
<head>
    <script type="text/javascript" src="lib/knockout/knockout.js"></script>
</head>
<body>
    <h1>Some Article</h1>
    <p>
        By <span data-bind="text: authorName"></span>
    </p>
    <script type="text/javascript">
      var viewModel = {
        authorName: 'Steve Smith'
      };
      ko.applyBindings(viewModel);
    </script>
</body>
</html>
```

<span data-ttu-id="df477-131">在浏览器的内容中查看时<span>元素替换为 viewModel 变量中的值：</span><span class="sxs-lookup"><span data-stu-id="df477-131">When viewed in the browser, the content of the <span> element is replaced with the value in the viewModel variable:</span></span>

![knockout 简单绑定](knockout/_static/simple-binding-screenshot.png)

<span data-ttu-id="df477-133">我们现在有了简单的单向绑定工作。</span><span class="sxs-lookup"><span data-stu-id="df477-133">We now have simple one-way binding working.</span></span> <span data-ttu-id="df477-134">请注意，无代码中未我们编写 JavaScript 将值分配给范围的内容。</span><span class="sxs-lookup"><span data-stu-id="df477-134">Notice that nowhere in the code did we write JavaScript to assign a value to the span's contents.</span></span> <span data-ttu-id="df477-135">如果我们想要操作 ViewModel，我们可以进一步这和添加 HTML 输入文本框中，并如绑定到它的值，以便：</span><span class="sxs-lookup"><span data-stu-id="df477-135">If we want to manipulate the ViewModel, we can take this a step further and add an HTML input textbox, and bind to its value, like so:</span></span>

```html
<p>
    Author Name: <input type="text" data-bind="value: authorName" />
</p>
```

<span data-ttu-id="df477-136">重新加载该页面，我们看到此值确实绑定到输入框：</span><span class="sxs-lookup"><span data-stu-id="df477-136">Reloading the page, we see that this value is indeed bound to the input box:</span></span>

![knockout 输入的绑定](knockout/_static/input-binding-screenshot.png)

<span data-ttu-id="df477-138">但是，如果我们更改在文本框中的值，对应的值中`<span>`元素不会更改。</span><span class="sxs-lookup"><span data-stu-id="df477-138">However, if we change the value in the textbox, the corresponding value in the `<span>` element doesn't change.</span></span> <span data-ttu-id="df477-139">为什么？</span><span class="sxs-lookup"><span data-stu-id="df477-139">Why not?</span></span>

<span data-ttu-id="df477-140">问题在于执行任何操作通知`<span>`需要进行更新。</span><span class="sxs-lookup"><span data-stu-id="df477-140">The issue is that nothing notified the `<span>` that it needed to be updated.</span></span> <span data-ttu-id="df477-141">只需更新 ViewModel 不足够，本身，除非视图模型的属性将封装在一种特殊类型。</span><span class="sxs-lookup"><span data-stu-id="df477-141">Simply updating the ViewModel isn't by itself sufficient, unless the ViewModel's properties are wrapped in a special type.</span></span> <span data-ttu-id="df477-142">我们需要使用**可观察对象**在任何需要自动更新在发生的更改的属性视图模型。</span><span class="sxs-lookup"><span data-stu-id="df477-142">We need to use **observables** in the ViewModel for any properties that need to have changes automatically updated as they occur.</span></span> <span data-ttu-id="df477-143">通过更改用于 ViewModel `ko.observable("value")` ViewModel 将而不是只是"value"，更新绑定到其值发生更改时某任何 HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="df477-143">By changing the ViewModel to use `ko.observable("value")` instead of just "value", the ViewModel will update any HTML elements that are bound to its value whenever a change occurs.</span></span> <span data-ttu-id="df477-144">请注意，直到他们失去焦点，因此不会更改绑定元素，键入时，输入的框不更新它们的值。</span><span class="sxs-lookup"><span data-stu-id="df477-144">Note that input boxes don't update their value until they lose focus, so you won't see changes to bound elements as you type.</span></span>

> [!NOTE]
> <span data-ttu-id="df477-145">添加对实时更新每个 keypress 后只需添加支持`valueUpdate: "afterkeydown"`到`data-bind`属性的内容。</span><span class="sxs-lookup"><span data-stu-id="df477-145">Adding support for live updating after each keypress is simply a matter of adding `valueUpdate: "afterkeydown"` to the `data-bind` attribute's contents.</span></span> <span data-ttu-id="df477-146">你还可以通过以下方式获取此行为通过使用`data-bind="textInput: authorName"`若要获取的值的即时更新。</span><span class="sxs-lookup"><span data-stu-id="df477-146">You can also get this behavior by using `data-bind="textInput: authorName"` to get instant updates of values.</span></span> 

<span data-ttu-id="df477-147">在更新它以使用 ko.observable 后我们 viewModel:</span><span class="sxs-lookup"><span data-stu-id="df477-147">Our viewModel, after updating it to use ko.observable:</span></span>

```javascript
var viewModel = {
  authorName: ko.observable('Steve Smith')
};
ko.applyBindings(viewModel);
```

<span data-ttu-id="df477-148">Knockout 支持多种不同类型的绑定。</span><span class="sxs-lookup"><span data-stu-id="df477-148">Knockout supports a number of different kinds of bindings.</span></span> <span data-ttu-id="df477-149">到目前为止，我们已了解如何将绑定到`text`并对其`value`。</span><span class="sxs-lookup"><span data-stu-id="df477-149">So far we've seen how to bind to `text` and to `value`.</span></span> <span data-ttu-id="df477-150">此外可以绑定到任何给定的属性。</span><span class="sxs-lookup"><span data-stu-id="df477-150">You can also bind to any given attribute.</span></span> <span data-ttu-id="df477-151">例如，若要使用的定位点标记，创建一个超链接`src`属性可以绑定到视图模型。</span><span class="sxs-lookup"><span data-stu-id="df477-151">For instance, to create a hyperlink with an anchor tag, the `src` attribute can be bound to the viewModel.</span></span> <span data-ttu-id="df477-152">Knockout 还支持绑定到函数。</span><span class="sxs-lookup"><span data-stu-id="df477-152">Knockout also supports binding to functions.</span></span> <span data-ttu-id="df477-153">若要说明这点，让我们更新 viewModel 包括作者 twitter 句柄，并显示 twitter 句柄为作者的 twitter 页面的链接。</span><span class="sxs-lookup"><span data-stu-id="df477-153">To demonstrate this, let's update the viewModel to include the author's twitter handle, and display the twitter handle as a link to the author's twitter page.</span></span> <span data-ttu-id="df477-154">我们将在三个阶段来执行此操作。</span><span class="sxs-lookup"><span data-stu-id="df477-154">We'll do this in three stages.</span></span>

<span data-ttu-id="df477-155">首先，添加 HTML 中以显示超链接，我们后作者的名称将显示在括号中：</span><span class="sxs-lookup"><span data-stu-id="df477-155">First, add the HTML to display the hyperlink, which we'll show in parentheses after the author's name:</span></span>

```html
<h1>Some Article</h1>
<p>
    By <span data-bind="text: authorName"></span>
    (<a data-bind="attr: { href: twitterUrl}, text: twitterAlias" ></a>)
</p>
```

<span data-ttu-id="df477-156">接下来，更新 viewModel 包括的 twitterUrl 和 twitterAlias 属性：</span><span class="sxs-lookup"><span data-stu-id="df477-156">Next, update the viewModel to include the twitterUrl and twitterAlias properties:</span></span>

```javascript
var viewModel = {
  authorName: ko.observable('Steve Smith'),
  twitterAlias: ko.observable('@ardalis'),
  twitterUrl: ko.computed(function() {
    return "https://twitter.com/";
  }, this)
};
ko.applyBindings(viewModel);
```

<span data-ttu-id="df477-157">请注意，此时我们尚未尚未更新 twitterUrl 转到此 twitter 别名的正确 URL – 只需指向 twitter.com。</span><span class="sxs-lookup"><span data-stu-id="df477-157">Notice that at this point we haven't yet updated the twitterUrl to go to the correct URL for this twitter alias – it's just pointing at twitter.com.</span></span> <span data-ttu-id="df477-158">另请注意，我们将使用新的 Knockout 函数`computed`，为 twitterUrl。</span><span class="sxs-lookup"><span data-stu-id="df477-158">Also notice that we're using a new Knockout function, `computed`, for twitterUrl.</span></span> <span data-ttu-id="df477-159">这是一个可观察到的函数，如果它更改，则将通知任何 UI 元素。</span><span class="sxs-lookup"><span data-stu-id="df477-159">This is an observable function that will notify any UI elements if it changes.</span></span> <span data-ttu-id="df477-160">但是，对于其在视图模型具有可访问其他属性，我们需要更改，我们将如何创建视图模型，以便每个属性是其自己的语句。</span><span class="sxs-lookup"><span data-stu-id="df477-160">However, for it to have access to other properties in the viewModel, we need to change how we are creating the viewModel, so that each property is its own statement.</span></span>

<span data-ttu-id="df477-161">修改后的 viewModel 声明如下所示。</span><span class="sxs-lookup"><span data-stu-id="df477-161">The revised viewModel declaration is shown below.</span></span> <span data-ttu-id="df477-162">现在，它被声明为函数。</span><span class="sxs-lookup"><span data-stu-id="df477-162">It is now declared as a function.</span></span> <span data-ttu-id="df477-163">请注意，每个属性是其自己语句现在，以分号结尾。</span><span class="sxs-lookup"><span data-stu-id="df477-163">Notice that each property is its own statement now, ending with a semicolon.</span></span> <span data-ttu-id="df477-164">另请注意，若要访问 twitterAlias 属性值，我们需要执行它，使其引用包含 （）。</span><span class="sxs-lookup"><span data-stu-id="df477-164">Also notice that to access the twitterAlias property value, we need to execute it, so its reference includes ().</span></span>

```javascript
function viewModel() {
  this.authorName = ko.observable('Steve Smith');
  this.twitterAlias = ko.observable('@ardalis');

  this.twitterUrl = ko.computed(function() {
    return "https://twitter.com/" + this.twitterAlias().replace('@','');
  }, this)
};
ko.applyBindings(viewModel);
```

<span data-ttu-id="df477-165">在浏览器中按预期的工作原理结果：</span><span class="sxs-lookup"><span data-stu-id="df477-165">The result works as expected in the browser:</span></span>

![knockout 超链接](knockout/_static/hyperlink-screenshot.png)

<span data-ttu-id="df477-167">Knockout 还支持绑定到特定的 UI 元素事件，如 click 事件。</span><span class="sxs-lookup"><span data-stu-id="df477-167">Knockout also supports binding to certain UI element events, such as the click event.</span></span> <span data-ttu-id="df477-168">这允许您轻松地以声明方式将用户界面元素绑定到应用程序的视图模型中的函数。</span><span class="sxs-lookup"><span data-stu-id="df477-168">This allows you to easily and declaratively bind UI elements to functions within the application's viewModel.</span></span> <span data-ttu-id="df477-169">举一个简单的例子中，我们可以添加一个按钮，单击时，修改作者 twitterAlias 为全部大写。</span><span class="sxs-lookup"><span data-stu-id="df477-169">As a simple example, we can add a button that, when clicked, modifies the author's twitterAlias to be all caps.</span></span>

<span data-ttu-id="df477-170">首先，我们添加按钮，则绑定到按钮的单击事件，并引用我们要将添加到视图模型的函数名称：</span><span class="sxs-lookup"><span data-stu-id="df477-170">First, we add the button, binding to the button's click event, and referencing the function name we're going to add to the viewModel:</span></span>

```html
<p>
    <button data-bind="click: capitalizeTwitterAlias">Capitalize</button>
</p>
```

<span data-ttu-id="df477-171">然后，将函数添加到视图模型，并将它捆绑起来要修改 viewModel 的状态。</span><span class="sxs-lookup"><span data-stu-id="df477-171">Then, add the function to the viewModel, and wire it up to modify the viewModel's state.</span></span> <span data-ttu-id="df477-172">请注意，若要设置到 twitterAlias 属性的新值，我们调用它作为一种方法并传入新值。</span><span class="sxs-lookup"><span data-stu-id="df477-172">Notice that to set a new value to the twitterAlias property, we call it as a method and pass in the new value.</span></span>

```javascript
function viewModel() {
  this.authorName = ko.observable('Steve Smith');
  this.twitterAlias = ko.observable('@ardalis');

  this.twitterUrl = ko.computed(function() {
    return "https://twitter.com/" + this.twitterAlias().replace('@','');
  }, this);

  this.capitalizeTwitterAlias = function() {
    var currentValue = this.twitterAlias();
    this.twitterAlias(currentValue.toUpperCase());
  }
};
ko.applyBindings(viewModel);
```

<span data-ttu-id="df477-173">运行代码，然后单击按钮将会按预期方式修改所显示的链接：</span><span class="sxs-lookup"><span data-stu-id="df477-173">Running the code and clicking the button modifies the displayed link as expected:</span></span>

![超链接大写](knockout/_static/hyperlink-caps-screenshot.png)

## <a name="control-flow"></a><span data-ttu-id="df477-175">控制流</span><span class="sxs-lookup"><span data-stu-id="df477-175">Control flow</span></span>

<span data-ttu-id="df477-176">Knockout 包括可以执行条件和循环操作的绑定。</span><span class="sxs-lookup"><span data-stu-id="df477-176">Knockout includes bindings that can perform conditional and looping operations.</span></span> <span data-ttu-id="df477-177">循环操作是非常适合绑定到 UI 列表、 菜单和网格或表的数据的列表。</span><span class="sxs-lookup"><span data-stu-id="df477-177">Looping operations are especially useful for binding lists of data to UI lists, menus, and grids or tables.</span></span> <span data-ttu-id="df477-178">Foreach 绑定将循环访问数组。</span><span class="sxs-lookup"><span data-stu-id="df477-178">The foreach binding will iterate over an array.</span></span> <span data-ttu-id="df477-179">如果使用的可观察到的数组，它将添加或从数组，而无需重新创建 UI 树中的每个元素中删除项时，自动更新 UI 元素。</span><span class="sxs-lookup"><span data-stu-id="df477-179">When used with an observable array, it will automatically update the UI elements when items are added or removed from the array, without re-creating every element in the UI tree.</span></span> <span data-ttu-id="df477-180">下面的示例使用新的视图模型，其中包括游戏结果的可观察到数组。</span><span class="sxs-lookup"><span data-stu-id="df477-180">The following example uses a new viewModel which includes an observable array of game results.</span></span> <span data-ttu-id="df477-181">将其绑定到一个简单表具有两个列使用`foreach`绑定`<tbody>`元素。</span><span class="sxs-lookup"><span data-stu-id="df477-181">It is bound to a simple table with two columns using a `foreach` binding on the `<tbody>` element.</span></span> <span data-ttu-id="df477-182">每个`<tr>`中的元素`<tbody>`将绑定到 gameResults 集合的某个元素。</span><span class="sxs-lookup"><span data-stu-id="df477-182">Each `<tr>` element within `<tbody>` will be bound to an element of the gameResults collection.</span></span>

```html
<h1>Record</h1>
<table>
    <thead>
        <tr>
            <th>Opponent</th>
            <th>Result</th>
        </tr>
    </thead>
    <tbody data-bind="foreach: gameResults">
        <tr>
            <td data-bind="text:opponent"></td>
            <td data-bind="text:result"></td>
        </tr>
    </tbody>
</table>
<script type="text/javascript">
  function GameResult(opponent, result) {
    var self = this;
    self.opponent = opponent;
    self.result = ko.observable(result);
  }

  function ViewModel() {
    var self = this;

    self.resultChoices = ["Win", "Loss", "Tie"];

    self.gameResults = ko.observableArray([
      new GameResult("Brendan", self.resultChoices[0]),
      new GameResult("Brendan", self.resultChoices[0]),
      new GameResult("Michelle", self.resultChoices[1])
    ]);
  };
  ko.applyBindings(new ViewModel);
</script>
```

<span data-ttu-id="df477-183">请注意，这次我们正在使用 ViewModel 带有大写字母"V"因为我们希望构造它使用"new"（在 applyBindings 调用中）。</span><span class="sxs-lookup"><span data-stu-id="df477-183">Notice that this time we're using ViewModel with a capital “V" because we expect to construct it using “new" (in the applyBindings call).</span></span> <span data-ttu-id="df477-184">在执行时，页面将生成以下输出：</span><span class="sxs-lookup"><span data-stu-id="df477-184">When executed, the page results in the following output:</span></span>

![knockout 记录视图模型](knockout/_static/record-screenshot.png)

<span data-ttu-id="df477-186">若要演示可查看的集合正常工作，让我们添加更多的功能。</span><span class="sxs-lookup"><span data-stu-id="df477-186">To demonstrate that the observable collection is working, let's add a bit more functionality.</span></span> <span data-ttu-id="df477-187">我们可以包括能够记录到视图模型，另一个游戏的结果，然后再添加一个按钮以及一些 UI，以使用此新函数。</span><span class="sxs-lookup"><span data-stu-id="df477-187">We can include the ability to record the results of another game to the ViewModel, and then add a button and some UI to work with this new function.</span></span>  <span data-ttu-id="df477-188">首先，让我们创建 addResult 方法：</span><span class="sxs-lookup"><span data-stu-id="df477-188">First, let's create the addResult method:</span></span>

```javascript
// add this to ViewModel()
self.addResult = function() {
  self.gameResults.push(new GameResult("", self.resultChoices[0]));
}
```

<span data-ttu-id="df477-189">将此方法绑定到按钮使用`click`绑定：</span><span class="sxs-lookup"><span data-stu-id="df477-189">Bind this method to a button using the `click` binding:</span></span>

```html
<button data-bind="click: addResult">Add New Result</button>
```

<span data-ttu-id="df477-190">在浏览器中打开的页面并单击按钮几次，从而导致每次单击新的表行：</span><span class="sxs-lookup"><span data-stu-id="df477-190">Open the page in the browser and click the button a couple of times, resulting in a new table row with each click:</span></span>

![添加了结果](knockout/_static/record-addresult-screenshot.png)

<span data-ttu-id="df477-192">有几种方法可以支持在 UI 中，通常为内联或在单独的窗体中添加新记录。</span><span class="sxs-lookup"><span data-stu-id="df477-192">There are a few ways to support adding new records in the UI, typically either inline or in a separate form.</span></span> <span data-ttu-id="df477-193">我们可轻松修改以使用文本框和 dropdownlists 以便整个字符串都是可编辑的表。</span><span class="sxs-lookup"><span data-stu-id="df477-193">We can easily modify the table to use textboxes and dropdownlists so that the whole thing is editable.</span></span> <span data-ttu-id="df477-194">只需更改`<tr>`元素如下所示：</span><span class="sxs-lookup"><span data-stu-id="df477-194">Just change the `<tr>` element as shown:</span></span>

```html
<tbody data-bind="foreach: gameResults">
    <tr>
      <td><input data-bind="value:opponent" /></td>
      <td><select data-bind="options: $root.resultChoices, value:result, optionsText: $data"></select></td>
    </tr>
</tbody>
```

<span data-ttu-id="df477-195">请注意，`$root`指根视图模型，它是公开的可能选择的位置。</span><span class="sxs-lookup"><span data-stu-id="df477-195">Note that `$root` refers to the root ViewModel, which is where the possible choices are exposed.</span></span> <span data-ttu-id="df477-196">`$data`是指任何当前模型是在给定上下文-在此情况下，它是指 resultChoices 数组，其中每个是一个简单的字符串的单个元素中。</span><span class="sxs-lookup"><span data-stu-id="df477-196">`$data` refers to whatever the current model is within a given context - in this case it refers to an individual element of the resultChoices array, each of which is a simple string.</span></span>

<span data-ttu-id="df477-197">进行此更改后，整个网格变得可编辑:</span><span class="sxs-lookup"><span data-stu-id="df477-197">With this change, the entire grid becomes editable:</span></span>

![可编辑的网格](knockout/_static/editable-grid-screenshot.png)

<span data-ttu-id="df477-199">如果不使用 Knockout，我们无法实现上述所有操作均使用 jQuery，但它很可能不会将几乎一样有效。</span><span class="sxs-lookup"><span data-stu-id="df477-199">If we weren't using Knockout, we could achieve all of this using jQuery, but most likely it would not be nearly as efficient.</span></span> <span data-ttu-id="df477-200">Knockout 跟踪对哪些用户界面元素，在视图模型中的项相对应的绑定的数据，而且仅更新需要添加、 删除或更新这些元素。</span><span class="sxs-lookup"><span data-stu-id="df477-200">Knockout tracks which bound data items in the ViewModel correspond to which UI elements, and only updates those elements that need to be added, removed, or updated.</span></span> <span data-ttu-id="df477-201">它将花费大量精力，来实现此目的自行使用 jQuery 或直接 DOM 操作，并甚至然后如果我们然后想要显示基于表的数据的聚合结果 （如 win 丢失记录），我们将需要一次循环访问它和分析HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="df477-201">It would take significant effort to achieve this ourselves using jQuery or direct DOM manipulation, and even then if we then wanted to display aggregate results (such as a win-loss record) based on the table's data, we would need to once more loop through it and parse the HTML elements.</span></span>  <span data-ttu-id="df477-202">Knockout，显示 win 丢失记录很简单。</span><span class="sxs-lookup"><span data-stu-id="df477-202">With Knockout, displaying the win-loss record is trivial.</span></span> <span data-ttu-id="df477-203">我们可以执行 ViewModel 本身中的计算，然后将其显示与简单文本绑定和一个`<span>`。</span><span class="sxs-lookup"><span data-stu-id="df477-203">We can perform the calculations within the ViewModel itself, and then display it with a simple text binding and a `<span>`.</span></span>

<span data-ttu-id="df477-204">若要生成的 win 丢失记录字符串，我们可以使用计算可观测对象。</span><span class="sxs-lookup"><span data-stu-id="df477-204">To build the win-loss record string, we can use a computed observable.</span></span> <span data-ttu-id="df477-205">请注意，引用向视图模型内的可观察属性必须是函数调用，否则它们不会检索该可观测对象的值 (即`gameResults()`不`gameResults`中所示的代码):</span><span class="sxs-lookup"><span data-stu-id="df477-205">Note that references to observable properties within the ViewModel must be function calls, otherwise they will not retrieve the value of the observable (i.e. `gameResults()` not `gameResults` in the code shown):</span></span>

```javascript
self.displayRecord = ko.computed(function () {
  var wins = self.gameResults().filter(function (value) { return value.result() == "Win"; }).length;
  var losses = self.gameResults().filter(function (value) { return value.result() == "Loss"; }).length;
  var ties = self.gameResults().filter(function (value) { return value.result() == "Tie"; }).length;
  return wins + " - " + losses + " - " + ties;
}, this);
```

<span data-ttu-id="df477-206">将此函数绑定到某个范围内`<h1>`页顶部的元素：</span><span class="sxs-lookup"><span data-stu-id="df477-206">Bind this function to a span within the `<h1>` element at the top of the page:</span></span>

```html
<h1>Record <span data-bind="text: displayRecord"></span></h1>
```

<span data-ttu-id="df477-207">结果：</span><span class="sxs-lookup"><span data-stu-id="df477-207">The result:</span></span>

![Win 丢失](knockout/_static/record-winloss-screenshot.png)

<span data-ttu-id="df477-209">添加行或修改任何行的结果列中的选定的元素将更新显示在窗口顶部的记录。</span><span class="sxs-lookup"><span data-stu-id="df477-209">Adding rows or modifying the selected element in any row's Result column will update the record shown at the top of the window.</span></span>

<span data-ttu-id="df477-210">除了为值的绑定，还可以使用绑定内的几乎任何合法 JavaScript 表达式。</span><span class="sxs-lookup"><span data-stu-id="df477-210">In addition to binding to values, you can also use almost any legal JavaScript expression within a binding.</span></span> <span data-ttu-id="df477-211">例如，如果 UI 元素仅会显示在某些情况下，例如当一个值超过特定阈值，则可以指定此逻辑上绑定表达式中：</span><span class="sxs-lookup"><span data-stu-id="df477-211">For example, if a UI element should only appear under certain conditions, such as when a value exceeds a certain threshold, you can specify this logically within the binding expression:</span></span>

```html
<div data-bind="visible: customerValue > 100"></div>
```

<span data-ttu-id="df477-212">这`<div>`只能将是可见的 customerValue 时超过 100。</span><span class="sxs-lookup"><span data-stu-id="df477-212">This `<div>` will only be visible when the customerValue is over 100.</span></span>

## <a name="templates"></a><span data-ttu-id="df477-213">模板</span><span class="sxs-lookup"><span data-stu-id="df477-213">Templates</span></span>

<span data-ttu-id="df477-214">Knockout 具有支持模板，以便可以很容易地从您的行为，将你的 UI，也可以以增量方式加载到按需大型应用程序的 UI 元素。</span><span class="sxs-lookup"><span data-stu-id="df477-214">Knockout has support for templates, so that you can easily separate your UI from your behavior, or incrementally load UI elements into a large application on demand.</span></span> <span data-ttu-id="df477-215">我们可以更新我们前面的示例通过简单地拖 HTML 缩小到模板，并在数据绑定调用中按名称指定模板使每个行其自己的模板`<tbody>`。</span><span class="sxs-lookup"><span data-stu-id="df477-215">We can update our previous example to make each row its own template by simply pulling the HTML out into a template and specifying the template by name in the data-bind call on `<tbody>`.</span></span>

```html
<tbody data-bind="template: { name: 'rowTemplate', foreach: gameResults }">
</tbody>
<script type="text/html" id="rowTemplate">
  <tr>
      <td><input data-bind="value:opponent" /></td>
      <td><select data-bind="options: $root.resultChoices, value:result, optionsText: $data"></select></td>
  </tr>
</script>
```

<span data-ttu-id="df477-216">Knockout 还支持其他模板化引擎，如 jQuery.tmpl 库和 Underscore.js 的模板化引擎。</span><span class="sxs-lookup"><span data-stu-id="df477-216">Knockout also supports other templating engines, such as the jQuery.tmpl library and Underscore.js's templating engine.</span></span>

## <a name="components"></a><span data-ttu-id="df477-217">组件数</span><span class="sxs-lookup"><span data-stu-id="df477-217">Components</span></span>

<span data-ttu-id="df477-218">组件允许您组织和重用 UI 代码，通常以及 UI 代码所依赖的 ViewModel 数据。</span><span class="sxs-lookup"><span data-stu-id="df477-218">Components allow you to organize and reuse UI code, usually along with the ViewModel data on which the UI code depends.</span></span> <span data-ttu-id="df477-219">若要创建一个组件，只需指定其模板和其视图模型，并为其提供一个名称。</span><span class="sxs-lookup"><span data-stu-id="df477-219">To create a component, you simply need to specify its template and its viewModel, and give it a name.</span></span> <span data-ttu-id="df477-220">可以通过调用 `ko.components.register()` 来完成此操作。</span><span class="sxs-lookup"><span data-stu-id="df477-220">This is done by calling `ko.components.register()`.</span></span> <span data-ttu-id="df477-221">除了定义的模板和 viewmodel 内联，可以加载它们从外部文件使用如库*require.js*，这会导致非常干净且高效的代码中。</span><span class="sxs-lookup"><span data-stu-id="df477-221">In addition to defining the templates and viewmodel inline, they can be loaded from external files using a library like *require.js*, resulting in very clean and efficient code.</span></span>

## <a name="communicating-with-apis"></a><span data-ttu-id="df477-222">与 Api 进行通信</span><span class="sxs-lookup"><span data-stu-id="df477-222">Communicating with APIs</span></span>

<span data-ttu-id="df477-223">Knockout 可以使用 JSON 格式中的任何数据。</span><span class="sxs-lookup"><span data-stu-id="df477-223">Knockout can work with any data in JSON format.</span></span> <span data-ttu-id="df477-224">检索和保存数据使用 Knockout 常见方法是使用 jQuery，支持`$.getJSON()`函数以检索数据，与`$.post()`方法从浏览器将数据发送到 API 终结点。</span><span class="sxs-lookup"><span data-stu-id="df477-224">A common way to retrieve and save data using Knockout is with jQuery, which supports the `$.getJSON()` function to retrieve data, and the `$.post()` method to send data from the browser to an API endpoint.</span></span> <span data-ttu-id="df477-225">当然，如果你愿意发送和接收 JSON 数据的不同方式，Knockout 将与其也工作。</span><span class="sxs-lookup"><span data-stu-id="df477-225">Of course, if you prefer a different way to send and receive JSON data, Knockout will work with it as well.</span></span>

## <a name="summary"></a><span data-ttu-id="df477-226">摘要</span><span class="sxs-lookup"><span data-stu-id="df477-226">Summary</span></span>

<span data-ttu-id="df477-227">Knockout 提供一种简单、 简洁的方法，将用户界面元素绑定到客户端应用程序，在视图模型中定义的当前状态。</span><span class="sxs-lookup"><span data-stu-id="df477-227">Knockout provides a simple, elegant way to bind UI elements to the current state of the client application, defined in a ViewModel.</span></span> <span data-ttu-id="df477-228">Knockout 的绑定语法使用应用于 HTML 元素的要处理的数据绑定属性。</span><span class="sxs-lookup"><span data-stu-id="df477-228">Knockout's binding syntax uses the data-bind attribute, applied to HTML elements that are to be processed.</span></span> <span data-ttu-id="df477-229">Knockout 能够有效地呈现和更新通过跟踪 UI 元素的大型数据集，仅处理更改受影响的元素。</span><span class="sxs-lookup"><span data-stu-id="df477-229">Knockout is able to efficiently render and update large data sets by tracking UI elements and only processing changes to affected elements.</span></span> <span data-ttu-id="df477-230">大型应用程序可以分解使用模板和组件，可以按需从外部文件加载的用户界面逻辑。</span><span class="sxs-lookup"><span data-stu-id="df477-230">Large applications can break up UI logic using templates and components, which can be loaded on demand from external files.</span></span> <span data-ttu-id="df477-231">当前版本 3，Knockout 是一个稳定的 JavaScript 库，可以提高 web 应用程序需要丰富的客户端交互。</span><span class="sxs-lookup"><span data-stu-id="df477-231">Currently version 3, Knockout is a stable JavaScript library that can improve web applications that require rich client interactivity.</span></span>
