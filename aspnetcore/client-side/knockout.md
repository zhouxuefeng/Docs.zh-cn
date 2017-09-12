---
title: "在 ASP.NET Core Knockout.js MVVM Framework"
author: ardalis
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: b20e3b23-1c51-47bf-adac-91b5048567e0
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/knockout
ms.openlocfilehash: d1c5cbd430587b757bb550f8f04355e67f04eb54
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="knockoutjs-mvvm-framework-in-aspnet-core"></a>在 ASP.NET Core Knockout.js MVVM Framework

通过[Steve Smith](https://ardalis.com/)

Knockout 是一种常用的 JavaScript 库，简化复杂的基于数据的用户界面的创建。 它可单独使用或与其他库，例如 jQuery。 其主要目的是要绑定到基础数据模型为 JavaScript 对象，定义的 UI 元素，以便时对 UI 进行更改，将更新模型，反之亦然。 Knockout 便于在 web 应用程序的客户端行为模型-视图-视图模型 (MVVM) 模式的使用。 其中一个必须了解在使用 Knockout 的 MVVM 实现的两个主要概念是可观察对象和绑定。

## <a name="getting-started"></a>入门

Knockout 部署为一个 JavaScript 文件中，因此安装和使用它是非常简单使用[bower](bower.md)。 假设您已有[bower](bower.md)和[gulp](using-gulp.md)配置，打开*bower.json*中 ASP.NET Core 项目，并添加 knockout 依赖项，如下所示：

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

与此就地，随后可以手动运行 bower 方法是打开任务运行程序资源管理器 （在下视图 ‣ 其他窗口 ‣ 任务运行程序资源管理器），然后在任务，右键单击在 bower 上并选择运行。 结果应显示类似如下：

![bower 运行 knockout 在任务运行程序资源管理器](knockout/_static/bower-knockout.png)

现在，如果你的项目中查找`wwwroot`文件夹中，你应看到 knockout 安装的 lib 文件夹下。

![安装在 lib 文件夹中的 knockout](knockout/_static/wwwroot-knockout.png)

建议在生产环境中引用 knockout 通过内容传送网络或 CDN，因为这会增加你的用户已有该文件的缓存的副本，因此根本下载不需要的可能性。 Knockout 位于多个 Cdn，包括 Microsoft Ajax CDN，此处：

[http://ajax.aspnetcdn.com/ajax/knockout/knockout-3.3.0.js](http://ajax.aspnetcdn.com/ajax/knockout/knockout-3.3.0.js)

若要将使用它的页面上包含 Knockout，只需添加`<script>`从任何位置你将托管它 （使用你的应用程序，或通过 CDN） 中引用该文件的元素：

```html
<script type="text/javascript" src="knockout-3.3.0.js"></script>
```

## <a name="observables-viewmodels-and-simple-binding"></a>可观察对象、 Viewmodel 和简单绑定

你可能已经熟悉使用 JavaScript 来操作在网页上，是指在通过直接访问 DOM 元素或使用 jQuery 等的库。 通常这种行为被通过编写代码以直接在对某些用户操作的响应中设置的元素值。 使用 Knockout，声明性方法则改为采用，通过该页面上的元素绑定到的属性对象。 而不是编写代码来操作 DOM 元素，与 ViewModel 对象，只需交互用户操作和 Knockout 负责确保同步页元素。

作为一个简单的示例，请考虑下面的页列表。 它包括`<span>`具有元素`data-bind`属性，该值指示文本内容，应绑定到作者姓名。 接下来，在使用单个属性，定义变量 viewModel JavaScript 块`authorName`，设置为某个值。 最后，调用`ko.applyBindings`由传入此 viewModel 变量。

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

在浏览器的内容中查看时<span>元素替换为 viewModel 变量中的值：

![knockout 简单绑定](knockout/_static/simple-binding-screenshot.png)

我们现在有了简单的单向绑定工作。 请注意，无代码中未我们编写 JavaScript 将值分配给范围的内容。 如果我们想要操作 ViewModel，我们可以进一步这和添加 HTML 输入文本框中，并如绑定到它的值，以便：

```html
<p>
    Author Name: <input type="text" data-bind="value: authorName" />
</p>
```

重新加载该页面，我们看到此值确实绑定到输入框：

![knockout 输入的绑定](knockout/_static/input-binding-screenshot.png)

但是，如果我们更改在文本框中的值，对应的值中`<span>`元素不会更改。 为什么？

问题在于执行任何操作通知`<span>`需要进行更新。 只需更新 ViewModel 不足够，本身，除非视图模型的属性将封装在一种特殊类型。 我们需要使用**可观察对象**在任何需要自动更新在发生的更改的属性视图模型。 通过更改用于 ViewModel `ko.observable("value")` ViewModel 将而不是只是"value"，更新绑定到其值发生更改时某任何 HTML 元素。 请注意，直到他们失去焦点，因此不会更改绑定元素，键入时，输入的框不更新它们的值。

> [!NOTE]
> 添加对实时更新每个 keypress 后只需添加支持`valueUpdate: "afterkeydown"`到`data-bind`属性的内容。 你还可以通过以下方式获取此行为通过使用`data-bind="textInput: authorName"`若要获取的值的即时更新。 

在更新它以使用 ko.observable 后我们 viewModel:

```javascript
var viewModel = {
  authorName: ko.observable('Steve Smith')
};
ko.applyBindings(viewModel);
```

Knockout 支持多种不同类型的绑定。 到目前为止，我们已了解如何将绑定到`text`并对其`value`。 此外可以绑定到任何给定的属性。 例如，若要使用的定位点标记，创建一个超链接`src`属性可以绑定到视图模型。 Knockout 还支持绑定到函数。 若要说明这点，让我们更新 viewModel 包括作者 twitter 句柄，并显示 twitter 句柄为作者的 twitter 页面的链接。 我们将在三个阶段来执行此操作。

首先，添加 HTML 中以显示超链接，我们后作者的名称将显示在括号中：

```html
<h1>Some Article</h1>
<p>
    By <span data-bind="text: authorName"></span>
    (<a data-bind="attr: { href: twitterUrl}, text: twitterAlias" ></a>)
</p>
```

接下来，更新 viewModel 包括的 twitterUrl 和 twitterAlias 属性：

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

请注意，此时我们尚未尚未更新 twitterUrl 转到此 twitter 别名的正确 URL – 只需指向 twitter.com。另请注意，我们将使用新的 Knockout 函数`computed`，为 twitterUrl。 这是一个可观察到的函数，如果它更改，则将通知任何 UI 元素。 但是，对于其在视图模型具有可访问其他属性，我们需要更改，我们将如何创建视图模型，以便每个属性是其自己的语句。

修改后的 viewModel 声明如下所示。 现在，它被声明为函数。 请注意，每个属性是其自己语句现在，以分号结尾。 另请注意，若要访问 twitterAlias 属性值，我们需要执行它，使其引用包含 （）。

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

在浏览器中按预期的工作原理结果：

![knockout 超链接](knockout/_static/hyperlink-screenshot.png)

Knockout 还支持绑定到特定的 UI 元素事件，如 click 事件。 这允许您轻松地以声明方式将用户界面元素绑定到应用程序的视图模型中的函数。 举一个简单的例子中，我们可以添加一个按钮，单击时，修改作者 twitterAlias 为全部大写。

首先，我们添加按钮，则绑定到按钮的单击事件，并引用我们要将添加到视图模型的函数名称：

```html
<p>
    <button data-bind="click: capitalizeTwitterAlias">Capitalize</button>
</p>
```

然后，将函数添加到视图模型，并将它捆绑起来要修改 viewModel 的状态。 请注意，若要设置到 twitterAlias 属性的新值，我们调用它作为一种方法并传入新值。

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

运行代码，然后单击按钮将会按预期方式修改所显示的链接：

![超链接大写](knockout/_static/hyperlink-caps-screenshot.png)

## <a name="control-flow"></a>控制流

Knockout 包括可以执行条件和循环操作的绑定。 循环操作是非常适合绑定到 UI 列表、 菜单和网格或表的数据的列表。 Foreach 绑定将循环访问数组。 如果使用的可观察到的数组，它将添加或从数组，而无需重新创建 UI 树中的每个元素中删除项时，自动更新 UI 元素。 下面的示例使用新的视图模型，其中包括游戏结果的可观察到数组。 将其绑定到一个简单表具有两个列使用`foreach`绑定`<tbody>`元素。 每个`<tr>`中的元素`<tbody>`将绑定到 gameResults 集合的某个元素。

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

请注意，这次我们正在使用 ViewModel 带有大写字母"V"因为我们希望构造它使用"new"（在 applyBindings 调用中）。 在执行时，页面将生成以下输出：

![knockout 记录视图模型](knockout/_static/record-screenshot.png)

若要演示可查看的集合正常工作，让我们添加更多的功能。 我们可以包括能够记录到视图模型，另一个游戏的结果，然后再添加一个按钮以及一些 UI，以使用此新函数。  首先，让我们创建 addResult 方法：

```javascript
// add this to ViewModel()
self.addResult = function() {
  self.gameResults.push(new GameResult("", self.resultChoices[0]));
}
```

将此方法绑定到按钮使用`click`绑定：

```html
<button data-bind="click: addResult">Add New Result</button>
```

在浏览器中打开的页面并单击按钮几次，从而导致每次单击新的表行：

![添加了结果](knockout/_static/record-addresult-screenshot.png)

有几种方法可以支持在 UI 中，通常为内联或在单独的窗体中添加新记录。 我们可轻松修改以使用文本框和 dropdownlists 以便整个字符串都是可编辑的表。 只需更改`<tr>`元素如下所示：

```html
<tbody data-bind="foreach: gameResults">
    <tr>
      <td><input data-bind="value:opponent" /></td>
      <td><select data-bind="options: $root.resultChoices, value:result, optionsText: $data"></select></td>
    </tr>
</tbody>
```

请注意，`$root`指根视图模型，它是公开的可能选择的位置。 `$data`是指任何当前模型是在给定上下文-在此情况下，它是指 resultChoices 数组，其中每个是一个简单的字符串的单个元素中。

进行此更改后，整个网格变得可编辑:

![可编辑的网格](knockout/_static/editable-grid-screenshot.png)

如果不使用 Knockout，我们无法实现上述所有操作均使用 jQuery，但它很可能不会将几乎一样有效。 Knockout 跟踪对哪些用户界面元素，在视图模型中的项相对应的绑定的数据，而且仅更新需要添加、 删除或更新这些元素。 它将花费大量精力，来实现此目的自行使用 jQuery 或直接 DOM 操作，并甚至然后如果我们然后想要显示基于表的数据的聚合结果 （如 win 丢失记录），我们将需要一次循环访问它和分析HTML 元素。  Knockout，显示 win 丢失记录很简单。 我们可以执行 ViewModel 本身中的计算，然后将其显示与简单文本绑定和一个`<span>`。

若要生成的 win 丢失记录字符串，我们可以使用计算可观测对象。 请注意，引用向视图模型内的可观察属性必须是函数调用，否则它们不会检索该可观测对象的值 (即`gameResults()`不`gameResults`中所示的代码):

```javascript
self.displayRecord = ko.computed(function () {
  var wins = self.gameResults().filter(function (value) { return value.result() == "Win"; }).length;
  var losses = self.gameResults().filter(function (value) { return value.result() == "Loss"; }).length;
  var ties = self.gameResults().filter(function (value) { return value.result() == "Tie"; }).length;
  return wins + " - " + losses + " - " + ties;
}, this);
```

将此函数绑定到某个范围内`<h1>`页顶部的元素：

```html
<h1>Record <span data-bind="text: displayRecord"></span></h1>
```

结果：

![Win 丢失](knockout/_static/record-winloss-screenshot.png)

添加行或修改任何行的结果列中的选定的元素将更新显示在窗口顶部的记录。

除了为值的绑定，还可以使用绑定内的几乎任何合法 JavaScript 表达式。 例如，如果 UI 元素仅会显示在某些情况下，例如当一个值超过特定阈值，则可以指定此逻辑上绑定表达式中：

```html
<div data-bind="visible: customerValue > 100"></div>
```

这`<div>`只能将是可见的 customerValue 时超过 100。

## <a name="templates"></a>模板

Knockout 具有支持模板，以便可以很容易地从您的行为，将你的 UI，也可以以增量方式加载到按需大型应用程序的 UI 元素。 我们可以更新我们前面的示例通过简单地拖 HTML 缩小到模板，并在数据绑定调用中按名称指定模板使每个行其自己的模板`<tbody>`。

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

Knockout 还支持其他模板化引擎，如 jQuery.tmpl 库和 Underscore.js 的模板化引擎。

## <a name="components"></a>组件数

组件允许您组织和重用 UI 代码，通常以及 UI 代码所依赖的 ViewModel 数据。 若要创建一个组件，只需指定其模板和其视图模型，并为其提供一个名称。 可以通过调用 `ko.components.register()` 来完成此操作。 除了定义的模板和 viewmodel 内联，可以加载它们从外部文件使用如库*require.js*，这会导致非常干净且高效的代码中。

## <a name="communicating-with-apis"></a>与 Api 进行通信

Knockout 可以使用 JSON 格式中的任何数据。 检索和保存数据使用 Knockout 常见方法是使用 jQuery，支持`$.getJSON()`函数以检索数据，与`$.post()`方法从浏览器将数据发送到 API 终结点。 当然，如果你愿意发送和接收 JSON 数据的不同方式，Knockout 将与其也工作。

## <a name="summary"></a>摘要

Knockout 提供一种简单、 简洁的方法，将用户界面元素绑定到客户端应用程序，在视图模型中定义的当前状态。 Knockout 的绑定语法使用应用于 HTML 元素的要处理的数据绑定属性。 Knockout 能够有效地呈现和更新通过跟踪 UI 元素的大型数据集，仅处理更改受影响的元素。 大型应用程序可以分解使用模板和组件，可以按需从外部文件加载的用户界面逻辑。 当前版本 3，Knockout 是一个稳定的 JavaScript 库，可以提高 web 应用程序需要丰富的客户端交互。
