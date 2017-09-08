---
title: "依赖关系注入到视图"
author: ardalis
description: 
keywords: "ASP.NET 核心"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 80fb9e43-e4db-4af2-b2a8-e1364a712f69
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/dependency-injection
ms.openlocfilehash: 05d64858dd70b45a1e2bb90a86ab3cbdc85264b1
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="dependency-injection-into-views"></a>依赖关系注入到视图

通过[Steve Smith](http://ardalis.com)

ASP.NET 核心支持[依赖关系注入](xref:fundamentals/dependency-injection)到视图。 这可用于查看特定服务，例如本地化或仅对填充视图元素是必需的数据。 你应尝试维护[关注点分离](http://deviq.com/separation-of-concerns)之间控制器和视图。 您的视图显示的数据的大多数应传递在中，从控制器中。

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample)

## <a name="a-simple-example"></a>一个简单的示例

你可以将服务注入到视图使用`@inject`指令。 你可以将`@inject`作为将属性添加到你的视图，并填充使用 DI 的属性。

语法`@inject`:`@inject <type> <name>`

一个示例`@inject`中操作：

[!code-csharp[Main](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/ToDo/Index.cshtml?highlight=4,5,15,16,17)]

此视图显示的列表`ToDoItem`实例，以及显示总体统计信息的摘要。 填充摘要从插入`StatisticsService`。 此服务注册中的依赖关系注入`ConfigureServices`中*Startup.cs*:

[!code-csharp[Main](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Startup.cs?highlight=6,7&range=15-22)]

`StatisticsService`的一套中执行某些计算`ToDoItem`实例，它通过存储库访问：

[!code-csharp[Main](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/StatisticsService.cs?highlight=15,20,26)]

示例存储库使用的内存中集合。 上面所示的实现 （运行所有内存中的数据上） 不建议用于大、 远程访问数据集。

该示例显示绑定到此视图的模型和视图中注入服务中的数据：

![若要查看列出总的项，完成项、 平均优先级别和使用其优先级别和布尔值，用于指示完成任务的列表。](dependency-injection/_static/screenshot.png)

## <a name="populating-lookup-data"></a>填充查找数据

视图注入可用于填充用户界面元素，如下拉列表中的选项。 请考虑用户配置文件窗体，其中包含用于指定性别、 状态和其他首选项的选项。 呈现表单使用标准的 MVC 方法需要控制器来请求每个选项，这些组的数据访问服务，然后填充模型，或`ViewBag`与每个组的选项来绑定。

另一种方法直接将服务注入视图以获取选项。 这将减少所需的控制器上，将此视图元素构造逻辑移入视图本身的代码量。 要显示的配置文件编辑窗体的控制器操作只需将配置文件实例传递窗体：

[!code-csharp[Main](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Controllers/ProfileController.cs?highlight=9,19)]

用于更新这些首选项的 HTML 窗体包括有关三个属性的下拉列表中：

![更新配置文件视图与窗体允许的名称、 性别、 状态和喜爱的颜色的条目。](dependency-injection/_static/updateprofile.png)

这些列表是通过注入视图的服务来填充：

[!code-csharp[Main](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Profile/Index.cshtml?highlight=4,16,17,21,22,26,27)]

`ProfileOptionsService`是 UI 级服务，旨在提供所需的此窗体数据：

[!code-csharp[Main](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/ProfileOptionsService.cs?highlight=7,13,24)]

>[!TIP]
> 不要忘记将注册的类型将请求中的依赖关系注入通过`ConfigureServices`中的方法*Startup.cs*。

## <a name="overriding-services"></a>重写服务

除了将注入新服务，此方法还可以使用重写以前插入的页面上的服务。 下图显示的所有可用字段在第一个示例中使用的页上：

![在类型化 Intellisense 上下文菜单中的 @ 符号列出 Html，组件、 StatsService，和 Url 字段](dependency-injection/_static/razor-fields.png)

如你所见，包括默认字段`Html`， `Component`，和`Url`(以及`StatsService`我们插入)。 如果实例，你想要将替换为你自己的默认 HTML 帮助器，则可以轻松地执行因此使用`@inject`:

[!code-html[Main](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Helper/Index.cshtml?highlight=3,11)]

如果你想要扩展现有的服务，可以在继承自或包装的现有实现替换为你自己时只需使用此方法。

## <a name="see-also"></a>另请参阅

* 人 Simon Timms 博客：[使查找数据进入你的视图](http://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)
