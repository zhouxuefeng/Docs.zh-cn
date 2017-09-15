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
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="controller-methods-and-views"></a><span data-ttu-id="6fdff-104">控制器方法和视图</span><span class="sxs-lookup"><span data-stu-id="6fdff-104">Controller methods and views</span></span>

<span data-ttu-id="6fdff-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6fdff-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6fdff-106">我们的电影应用有个不错的开始，但是展示效果还不够理想。</span><span class="sxs-lookup"><span data-stu-id="6fdff-106">We have a good start to the movie app, but the presentation is not ideal.</span></span> <span data-ttu-id="6fdff-107">我们不希望看到时间（如下图所示的 12:00:00 AM），并且“ReleaseDate”应为两个词。</span><span class="sxs-lookup"><span data-stu-id="6fdff-107">We don't want to see the time (12:00:00 AM in the image below) and **ReleaseDate** should be two words.</span></span>

![索引视图：Release Date 为一个词（没有空格），每个电影发行日期都显示时间中午 12 点](working-with-sql/_static/m55.png)

<span data-ttu-id="6fdff-109">打开 Models/Movie.cs 文件，并添加以下代码中突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="6fdff-109">Open the *Models/Movie.cs* file and add the highlighted lines shown below:</span></span>

<span data-ttu-id="6fdff-110">[!code-csharp[Main](start-mvc/sample/MvcMovie/Models/MovieDateWithExtraUsings.cs?name=snippet_1&highlight=13-14)]</span><span class="sxs-lookup"><span data-stu-id="6fdff-110">[!code-csharp[Main](start-mvc/sample/MvcMovie/Models/MovieDateWithExtraUsings.cs?name=snippet_1&highlight=13-14)]</span></span>

<span data-ttu-id="6fdff-111">右键单击红色的波浪线，然后选择“快速操作和重构”。</span><span class="sxs-lookup"><span data-stu-id="6fdff-111">Right click on a red squiggly line **> Quick Actions and Refactorings**.</span></span>

  ![上下文菜单中显示“快速操作和重构”。](controller-methods-views/_static/qa.png)


<span data-ttu-id="6fdff-113">点击 `using System.ComponentModel.DataAnnotations;`</span><span class="sxs-lookup"><span data-stu-id="6fdff-113">Tap `using System.ComponentModel.DataAnnotations;`</span></span>

  ![使用列表顶部的 System.ComponentModel.DataAnnotations](controller-methods-views/_static/da.png)

  <span data-ttu-id="6fdff-115">Visual studio 添加 `using System.ComponentModel.DataAnnotations;`。</span><span class="sxs-lookup"><span data-stu-id="6fdff-115">Visual studio adds `using System.ComponentModel.DataAnnotations;`.</span></span>

<span data-ttu-id="6fdff-116">删除不需要的 `using` 语句。</span><span class="sxs-lookup"><span data-stu-id="6fdff-116">Let's remove the `using` statements that are not needed.</span></span> <span data-ttu-id="6fdff-117">默认情况下，它们显示为浅灰色字体。</span><span class="sxs-lookup"><span data-stu-id="6fdff-117">They show up by default in a light grey font.</span></span> <span data-ttu-id="6fdff-118">右键单击 Movie.cs 文件中的任意位置，然后选择“对 Using 进行删除和排序”。</span><span class="sxs-lookup"><span data-stu-id="6fdff-118">Right click anywhere in the *Movie.cs* file **> Remove and Sort Usings**.</span></span>

![对 Using 进行删除和排序](controller-methods-views/_static/rm.png)

<span data-ttu-id="6fdff-120">更新的代码：</span><span class="sxs-lookup"><span data-stu-id="6fdff-120">The updated code:</span></span>

<span data-ttu-id="6fdff-121">[!code-csharp[Main](./start-mvc/sample/MvcMovie/Models/MovieDate.cs?name=snippet_1)]</span><span class="sxs-lookup"><span data-stu-id="6fdff-121">[!code-csharp[Main](./start-mvc/sample/MvcMovie/Models/MovieDate.cs?name=snippet_1)]</span></span>

<!-- include start -->

[!INCLUDE[adding-model](../../includes/mvc-intro/controller-methods-views.md)]

>[!div class="step-by-step"]
<span data-ttu-id="6fdff-122">[上一页](working-with-sql.md)
[下一页](search.md)</span><span class="sxs-lookup"><span data-stu-id="6fdff-122">[Previous](working-with-sql.md)
[Next](search.md)</span></span>  
