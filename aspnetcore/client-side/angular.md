---
title: "使用 AngularJS 单页面应用程序 (Spa)"
author: rick-anderson
description: "了解如何构建使用 AngularJS 的 SPA 样式 ASP.NET 应用程序"
keywords: "ASP.NET 核心，AngularJS SPA"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 4b30576b-2718-4c39-9253-a59966747893
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/angular
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d271560476ee4efdffbd457e37eb769a7ae6ca25
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2017
---
# <a name="using-angularjs-for-single-page-applications-spas-with-aspnet-core"></a>使用 AngularJS 适用于 ASP.NET Core 的单页面应用程序 (Spa)


通过[Venkata Koppaka](https://blog.falafel.com/falafel-software-recognized-sitefinity-website-year/)和[Scott Addie](https://scottaddie.com)

在本文中，你将了解如何构建使用 AngularJS 的 SPA 样式 ASP.NET 应用程序。

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/angular/sample)

## <a name="what-is-angularjs"></a>AngularJS 是什么？

[AngularJS](https://angularjs.org/)是一个现代的 JavaScript 框架，将来自 Google 通常用于使用单页面应用程序 (Spa)。 AngularJS 处于打开状态来源依据 MIT 许可和 AngularJS 的开发进度适用于[其 GitHub 存储库](https://github.com/angular/angular.js)。 库称为角，因为 HTML 使用形角度方括号。

AngularJS 不是如 jQuery，一个 DOM 操作库，但它使用 jQuery 调用 jQLite 的子集。 AngularJS 主要取决于你可以将其添加到 HTML 标记的声明性 HTML 特性。 你可以在你浏览器使用尝试 AngularJS[代码学校网站](https://www.codeschool.com/courses/shaping-up-with-angularjs)或[W3Schools 网站](https://www.w3schools.com/angular/)。

本文着重于 AngularJS 与在其中的角发展方向上的一些注意事项。

## <a name="getting-started"></a>入门

若要开始在 ASP.NET 应用程序中使用 AngularJS，你必须安装属于你的项目，或从内容传送网络 (CDN) 中引用它。

### <a name="installation"></a>安装

有多种方法来将 AngularJS 添加到你的应用程序。 如果你在 Visual Studio 中开始新的 ASP.NET 核心 web 应用程序，则可以添加使用内置的 AngularJS [Bower](bower.md)支持。 打开*bower.json*，并添加到一个条目`dependencies`属性：

<a name=angular-bower-json></a>

[!code-json[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/bower.json?highlight=9)]

保存后*bower.json*文件，角将安装在你的项目的*wwwroot/lib*文件夹。 此外中, 会列出`Dependencies/Bower`文件夹。 请参阅下面的屏幕截图。

![使用 AngularJS 项目的解决方案资源管理器](angular/_static/angular-solution-explorer.png)

接下来，添加`<script>`参考底部`<body>`的 HTML 页的部分或*_Layout.cshtml*文件，如下所示：

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Shared/_Layout.cshtml?highlight=4&range=48-52)]

建议生产应用程序，如 AngularJS 的公共库利用 Cdn。 你可以从多个 Cdn，例如这个之一来引用 AngularJS:

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Shared/_Layout.cshtml?highlight=10&range=53-67)]

一旦对引用*angular.js*你准备好开始使用 AngularJS 在网页中的脚本文件。

## <a name="key-components"></a>关键组件

AngularJS 包括大量的主要组件，如*指令*，*模板*，*中继器*，*模块*， *控制器*，*组件*，*组件路由器*和的详细信息。 让我们看一下这些组件如何协同工作以将行为添加到你的网页。

### <a name="directives"></a>指令

使用 AngularJS[指令](https://docs.angularjs.org/guide/directive)来扩展自定义属性和元素的 HTML。 AngularJS 指令定义通过`data-ng-*`或`ng-*`前缀 (`ng`是短的角度)。 有两种类型的 AngularJS 指令：

   1. **基元指令**： 这些预定义的由角度团队并且 AngularJS 框架的一部分。

   2. **自定义指令**： 这些是你可以定义的自定义指令。

在所有 AngularJS 应用程序中使用的基元指令之一是`ng-app`指令，启动 AngularJS 应用程序。 此指令可应用于`<body>`标记或正文的子元素。 我们来看操作部分中的示例。 假设你在 ASP.NET 项目中，可以添加到某一 HTML 文件`wwwroot`文件夹，或添加新的控制器操作和关联的视图。 在这种情况下，我添加了一个新`Directives`到操作方法`HomeController.cs`。 关联的视图如下所示：

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Directives.cshtml?highlight=5,7)]

若要使这些示例的相互独立，我现在没有使用共享的布局文件。 你可以看到我们修饰将正文标记与`ng-app`指令来指示在此页是一个 AngularJS 应用程序。 `{{2+2}}`是你将了解有关在稍后的角度的数据绑定表达式。 下面是结果，如果运行此应用程序：

![简单的角度指令](angular/_static/simple-directive.png)

其他基元中 AngularJS 指令包括：

`ng-controller`确定哪个 JavaScript 控制器绑定到的视图。

`ng-model`确定 HTML 元素的属性的值绑定到的模型。

`ng-init`用于初始化当前作用域表达式形式的应用程序数据。

`ng-if`删除或重新创建基于提供的表达式 truthiness DOM 中给定的 HTML 元素。

`ng-repeat`重复访问的数据集的 HTML 给定的块。

`ng-show`显示或隐藏基于提供的表达式的给定的 HTML 元素。

有关在 AngularJS 中受支持的所有基元指令的完整列表，请参阅[AngularJS 文档网站上的指令的文档部分](https://docs.angularjs.org/api/ng/directive)。

### <a name="data-binding"></a>数据绑定

AngularJS 提供[数据绑定](https://docs.angularjs.org/guide/databinding)支持的-现成使用`ng-bind`指令或数据绑定表达式语法，如`{{expression}}`。 AngularJS 支持双向数据绑定模型中的数据与视图模板在所有时间的同步的保存。 对视图的任何更改自动反映在模型中。 同样，在模型中的任何更改都会反映在视图中。

使用名为的随附视图创建某一 HTML 文件或的控制器操作`Databinding`。 该视图中包括以下内容：

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Databinding.cshtml?highlight=8,9,10)]

请注意，你可以显示使用指令或数据绑定模型值 (`ng-bind`)。 则结果页应如下所示：

![简单数据绑定](angular/_static/simple-databinding.png)

### <a name="templates"></a>模板

[模板](https://docs.angularjs.org/guide/templates)AngularJS 中是普通的 HTML 页使用了修饰 AngularJS 指令和项目。 AngularJS 中的模板是指令、 表达式、 筛选和组合使用的 HTML 以窗体视图的控件的混合。

添加另一个视图，以演示模板，并向其中添加以下：

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Templates.cshtml?highlight=8,9,10)]

该模板具有类似的 AngularJS 指令`ng-app`， `ng-init`，`ng-model`和数据绑定表达式语法以绑定`personName`属性。 视图在浏览器中运行，类似于下面的屏幕截图：

![简单的模板示例 1](angular/_static/simple-templates-1.png)

如果通过在输入字段中键入更改名称，你将看到的文本输入字段旁边动态更新，显示角度双向数据绑定操作中。

![简单的模板示例 2](angular/_static/simple-templates-2.png)

### <a name="expressions"></a>表达式

[表达式](https://docs.angularjs.org/guide/expression)AngularJS 中是类似于 JavaScript 的代码段内写入`{{ expression }}`语法。 这些表达式中的数据绑定到 HTML 与相同的方式`ng-bind`指令。 AngularJS 表达式和 JavaScript 的正则表达式之间的主要区别是表达式针对进行了评估该 AngularJS `$scope` AngularJS 中的对象。

绑定下面的示例中的 AngularJS 表达式`personName`和简单 JavaScript 计算表达式：

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Expressions.cshtml?highlight=8,9,10)]

在浏览器将显示运行该示例`personName`数据和计算的结果：

![简单表达式](angular/_static/simple-expressions.png)

### <a name="repeaters"></a>中继器

重复 AngularJS 中是通过调用基元指令`ng-repeat`。 `ng-repeat`指令重复通过重复的数据数组的长度在视图中给定的 HTML 元素。 在 AngularJS 中继器可以重复对字符串或对象的数组。 下面是一个字符串数组通过重复的示例用法：

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Repeaters.cshtml?highlight=8,10,11)]

[Repeat 指令](https://docs.angularjs.org/api/ng/directive/ngRepeat)输出一系列的列表项，在未排序的列表中，在此屏幕截图所示的开发人员工具中可以看到：

![转发器示例](angular/_static/repeater.png)

下面是示例重复通过对象的数组。 `ng-init`指令建立`names`数组，其中每个元素是一个包含第一次的对象的姓氏。 `ng-repeat`分配， `name in names`，输出每个数组元素的一个列表项。

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Repeaters2.cshtml?highlight=8,9,10,11,13,14)]

在这种情况下，输出是与前面的示例中的相同。

角提供了一些可以帮助提供基于其中循环是在其执行中的行为的其他指令。

`$index`

使用`$index`中`ng-repeat`循环，以确定哪个索引定位你循环当前位于。

`$even` 和 `$odd`

使用`$even`中`ng-repeat`循环，以确定你循环中的当前索引是否即使索引的行。 同样，使用`$odd`以确定当前的索引是否为奇数的索引的行。

`$first` 和 `$last`

使用`$first`中`ng-repeat`循环，以判定在循环中的当前索引是否为第一个行。 同样，使用`$last`若要确定是否当前的索引的最后一行。

下面是一个示例，演示`$index`， `$even`， `$odd`， `$first`，和`$last`中操作：

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Repeaters3.cshtml?highlight=14,15,16,17,18)]

下面是生成的输出：

![转发器示例 2](angular/_static/repeaters2.png)

### <a name="scope"></a>$scope

`$scope`是充当粘附视图 （模板） 和 （如下所述） 的控制器之间的 JavaScript 对象。 有关附加到的值仅知道 AngularJS 中的视图模板`$scope`控制器中的对象。

> [!NOTE]
> MVVM 世界`$scope`中 AngularJS 对象通常定义为视图模型。 AngularJS 团队指`$scope`对象作为数据模型。 [了解有关 AngularJS 中的作用域](https://docs.angularjs.org/guide/scope)。

下面是一个简单的示例演示如何设置属性`$scope`内一个单独的 JavaScript 文件， *scope.js*:

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/scope.js?highlight=2,3)]

观察`$scope`参数传递到控制器在第 2 行上。 此对象是视图的了解。 在一行 3，我们设置一个名为"name"到"Mary Jane"属性。

由视图找不到的特定属性时，会发生什么情况？ 下面定义的视图引用的是"name"和"age"属性：

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Scope.cshtml?highlight=9,10,14)]

请注意，在第 9 我们会要求角显示"name"属性使用表达式语法行。 此行 10 然后引用"时间"，不存在的属性。 运行示例显示期限设置为"Mary Jane"和执行任何操作的名称。 将忽略缺少的属性。

![作用域示例](angular/_static/scope.png)

### <a name="modules"></a>模块

A[模块](https://docs.angularjs.org/guide/module)AngularJS 中是控制器、 服务、 指令、 等的集合。`angular.module()`函数调用用于创建、 注册和检索 AngularJS 中的模块。 应使用注册所有模块，包括那些由 AngularJS 团队和第三方库提供`angular.module()`函数。

下面是代码的演示如何在 AngularJS 中创建新的模块段。 第一个参数是模块的名称。 第二个参数定义上其他模块依赖项。 更高版本在本文中，我们将演示如何将传递到这些依赖关系`angular.module()`方法调用。

```javascript
var personApp = angular.module('personApp', []);
```

使用`ng-app`指令来表示在页上的 AngularJS 模块。 若要使用模块，请将该模块，该名称分配`personApp`在此示例中，到`ng-app`指令在我们的模板。

```html
<body ng-app="personApp">
```

### <a name="controllers"></a>控制器

[控制器](https://docs.angularjs.org/guide/controller)AngularJS 中是为你的代码的条目的第一个点。 `<module name>.controller()`函数调用用于创建和注册在 AngularJS 控制器。 `ng-controller`指令用于表示 HTML 页上的 AngularJS 控制器。 角中的控制器角色是将设置状态和数据模型的行为 (`$scope`)。 控制器不应该用于直接处理的 DOM。

下面是代码的注册新的控制器段。 `personApp`代码段中的变量引用一个角度模块，请在第 2 行定义。

[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/controllers.js?highlight=2,5)]

视图使用`ng-controller`指令将分配的控制器名称：

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Controllers.cshtml?highlight=8,14)]

该页面显示"Mary"和"Jane"对应于`firstName`和`lastName`属性附加到`$scope`对象：

![控制器示例](angular/_static/controllers.png)

### <a name="components"></a>组件数

[组件](https://docs.angularjs.org/guide/component)在角 1.5.x 允许封装和创建单独的 HTML 元素的功能。 角度 1.4.x 中无法实现使用.directive() 方法的相同功能。

通过使用.component() 方法，开发简化获得指令和控制器的功能。 其他好处包括：作用域隔离，最佳做法是固有的并迁移到角度 2 变得更轻松的任务。 `<module name>.component()`函数调用用于创建和在 AngularJS 中注册组件。

下面是代码的注册新的组件段。 `personApp`代码段中的变量引用一个角度模块，请在第 2 行定义。

[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/components.js?highlight=2,5,13)]

我们将在其中显示的自定义的 HTML 元素的视图。

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/Home/Components.cshtml?highlight=8)]

使用组件的关联的模板：

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/partials/personcomponent.html?highlight=2,3)]

该页面显示"Aftab"和"Ansari"对应于`firstName`和`lastName`属性附加到`vm`对象：

![组件的示例](angular/_static/components.png)

### <a name="services"></a>服务

[服务](https://docs.angularjs.org/guide/services)中 AngularJS 通常用于为一个可在整个角度的应用程序的生存期的文件抽象出来的共享代码。 服务惰式实例化，这意味着，不服务的实例，除非获取使用依赖于服务的组件。 工厂是服务的使用 AngularJS 应用程序中的一个示例。 使用创建工厂`myApp.factory()`函数调用，其中`myApp`是模块。

下面是一个示例，演示如何使用中 AngularJS 工厂：

[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/simpleFactory.js?highlight=1)]

若要从控制器中调用此工厂，传入`personFactory`作为参数传递给`controller`函数：

```javascript
personApp.controller('personController', function($scope,personFactory) {
  $scope.name = personFactory.getName();
});
```

### <a name="using-services-to-talk-to-a-rest-endpoint"></a>使用服务进行对话的 REST 终结点

下面是一个端到端示例使用服务在 AngularJS 与 ASP.NET 核心 Web API 终结点进行交互。 该示例从 Web API 获取数据，并显示一个视图模板中的数据。 让我们首先启动与视图：

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/People/Index.cshtml?highlight=5,8,10,17,18,19)]

在此视图中，我们具有调用的角度模块`PersonsApp`和控制器调用`personController`。 我们将使用`ng-repeat`以循环访问的人员列表。 我们正在引用三个行 17-19 的自定义 JavaScript 文件。

*PersonApp.js*文件用于注册`PersonsApp`模块中; 以及的语法是类似于前面的示例。 我们将使用`angular.module`函数来创建我们将使用的模块的新实例。

[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personApp.js?highlight=3)]

让我们看一看*personFactory.js*下面。 我们正在呼叫模块的`factory`方法来创建一个工厂。 第 12 行显示内置角`$http`服务从 web 服务检索用户信息。

[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personFactory.js?highlight=6,7,12)]

在*personController.js*，我们正在呼叫模块的`controller`方法可创建控制器。 `$scope`对象的`people`属性分配从 personFactory （行 13） 返回的数据。

[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personController.js?highlight=6,7,13)]

让我们快速了解一下 Web API 和其后面的模型。 `Person`模型是使用 POCO （普通旧 CLR 对象） `Id`， `FirstName`，和`LastName`属性：

[!code-csharp[Main](angular/sample/AngularJSSample/src/AngularJSSample/Models/Person.cs)]

`Person`控制器返回 JSON 格式的列表`Person`对象：

[!code-csharp[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Controllers/Api/PersonController.cs?highlight=9,10,19)]

让我们查看在操作中的应用程序：

![控制器显示 REST 结果](angular/_static/rest-bound.png)

你可以[在 GitHub 上查看应用程序的结构](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/angular/sample)。

> [!NOTE]
> 有关构建 AngularJS 应用程序的详细信息，请参阅[John 爸爸角度样式指南](https://github.com/johnpapa/angular-styleguide)

&nbsp;

> [!NOTE]
> 若要创建 AngularJS 模块、 控制器、 工厂，指令和视图文件轻松，一定要签出 Sayed Hashimi [SideWaffle 模板适用于 Visual Studio 的包](http://sidewaffle.com/)。 Sayed Hashimi 是在 Microsoft 的 Visual Studio Web 团队高级项目经理和 SideWaffle 模板均视为黄金标准。 在撰写本文时，SideWaffle 是可用于 Visual Studio 2012、 2013年和 2015年。

### <a name="routing-and-multiple-views"></a>路由和多个视图

AngularJS 有内置的路由提供程序来处理基于 SPA （单页面应用程序） 导航。 若要使用 AngularJS 中的路由，必须添加`angular-route`使用 Bower 库。 你可在看到[bower.json](#angular-bower-json)在开始时，我们已在引用它在我们的项目这篇文章中引用的文件。

安装包后，添加脚本引用 (*角度 route.js*) 向你的视图。

现在让我们举个人员应用我们已生成并向其中添加导航。 首先，我们将使通过创建新的应用程序一份`PeopleController`操作调用`Spa`和相应`Spa.cshtml`通过复制中的 Index.cshtml 视图的视图`People`文件夹。 添加对的脚本引用`angular-route`（请参阅第 11 行）。 此外添加`div`标有`ng-view`指令 （请参阅第 6 行） 作为一个占位符，以将视图中的。 我们将使用其他几个*.js*行 13-16 引用的文件。

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/People/Spa.cshtml?highlight=6,11,12,13,14,15,16)]

让我们看一看*personModule.js*文件以查看如何我们实例化具有路由的模块。 我们传递`ngRoute`为到该模块库。 此模块处理我们的应用程序中的路由。

[!code-javascript[Main](angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personModule.js)]

*PersonRoutes.js*文件，下面，定义基于路由提供程序的路由。 行 4-7 通过有效地说，当与 URL 定义导航`/persons`是请求，使用一个称为模板`partials/personlist`通过一起`personListController`。 第 8 11 行指示的路由参数的详细信息页`personId`。 如果 URL 不匹配的模式，角默认为`/persons`视图。

[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personRoutes.js?highlight=4,5,6,7,8,9,10,11,13)]

`personlist.html`文件是包含仅显示用户列表所需的 HTML 了分部视图。

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/partials/personlist.html?highlight=3)]

通过使用模块的定义控制器`controller`函数中*personListController.js*。

[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/personListController.js?highlight=1)]

如果我们运行此应用程序并导航到`people/spa#/persons`URL，我们将看到：

![人员列表视图](angular/_static/spa-persons.png)

如果我们导航到详细信息页中，例如`people/spa#/persons/2`，我们将要看到的详细信息部分视图：

![人员详细信息视图](angular/_static/spa-persons-2.png)

你可以查看完整的源和上不显示这篇文章中的任何文件[GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/client-side/angular/sample)。

### <a name="event-handlers"></a>事件处理程序

有大量中将事件处理功能添加到你的 HTML dom。 中的输入元素的 AngularJS 指令 下面是内置于 AngularJS 的事件的列表。

   * `ng-click`

   * `ng-dbl-click`

   * `ng-mousedown`

   * `ng-mouseup`

   * `ng-mouseenter`

   * `ng-mouseleave`

   * `ng-mousemove`

   * `ng-keydown`

   * `ng-keyup`

   * `ng-keypress`

   * `ng-change`

> [!NOTE]
> 你可以添加自己的事件处理程序使用[AngularJS 中功能的自定义指令](https://docs.angularjs.org/guide/directive)。

让我们看一下如何`ng-click`向上有线事件。 创建一个名为的新 JavaScript 文件*eventHandlerController.js*，并向其中添加以下：

[!code-javascript[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/wwwroot/app/eventHandlerController.js?highlight=5,6,7)]

请注意新`sayName`函数中`eventHandlerController`在行 5 上面。 所有方法执行的都操作，为现在通过一条欢迎消息向用户显示 JavaScript 警报。

下面的视图将控制器函数绑定到 AngularJS 事件。 第 9 行在其上具有按钮`ng-click`已应用角度指令。 它调用我们`sayName`函数，附加到`$scope`对象传递给此视图。

[!code-html[Main](../client-side/angular/sample/AngularJSSample/src/AngularJSSample/Views/People/Events.cshtml?highlight=9)]

运行示例演示控制器的`sayName`时单击该按钮时将自动调用函数。

![单击事件](angular/_static/events.png)

有关 AngularJS 内置的事件处理程序指令的详细信息，请务必开头到[文档网站](https://docs.angularjs.org/api/ng/directive/ngClick)AngularJS。

## <a name="additional-resources"></a>其他资源

* [角度文档](https://docs.angularjs.org)

* [角度 2 信息](https://angular.io/)
