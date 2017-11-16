---
title: "控制器方法和视图"
author: rick-anderson
description: "使用控制器方法、视图和 DataAnnotations"
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 03/07/2017
ms.topic: get-started-article
ms.assetid: c7313211-b271-4adf-bab8-8e72603cc0ce
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/controller-methods-views
ms.openlocfilehash: 8960853438d3227de3ef7c50936626149d8d5997
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
# <a name="controller-methods-and-views"></a>控制器方法和视图

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

我们的电影应用有个不错的开始，但是展示效果还不够理想。 我们不希望看到时间（如下图所示的 12:00:00 AM），并且“ReleaseDate”应为两个词。

![索引视图：Release Date 为一个词（没有空格），每个电影发行日期都显示时间中午 12 点](working-with-sql/_static/m55.png)

打开 Models/Movie.cs 文件，并添加以下代码中突出显示的行：

[!code-csharp[Main](start-mvc/sample/MvcMovie/Models/MovieDateWithExtraUsings.cs?name=snippet_1&highlight=13-14)]

右键单击红色的波浪线，然后选择“快速操作和重构”。

  ![上下文菜单中显示“快速操作和重构”。](controller-methods-views/_static/qa.png)


点击 `using System.ComponentModel.DataAnnotations;`

  ![使用列表顶部的 System.ComponentModel.DataAnnotations](controller-methods-views/_static/da.png)

  Visual studio 添加 `using System.ComponentModel.DataAnnotations;`。

删除不需要的 `using` 语句。 默认情况下，它们显示为浅灰色字体。 右键单击 Movie.cs 文件中的任意位置，然后选择“对 Using 进行删除和排序”。

![对 Using 进行删除和排序](controller-methods-views/_static/rm.png)

更新的代码：

[!code-csharp[Main](./start-mvc/sample/MvcMovie/Models/MovieDate.cs?name=snippet_1)]

<!-- include start -->

[!INCLUDE[adding-model](../../includes/mvc-intro/controller-methods-views.md)]

>[!div class="step-by-step"]
[上一页](working-with-sql.md)
[下一页](search.md)  
