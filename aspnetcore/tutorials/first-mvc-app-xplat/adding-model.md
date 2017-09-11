---
title: "添加模型"
author: rick-anderson
description: "将模型添加到简单 ASP.NET Core 应用。"
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 03/30/2017
ms.topic: get-started-article
ms.assetid: 8dc28498-eeee-4666-b903-b593059e9f39
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app-xplat/adding-model
ms.openlocfilehash: 054ef316461447ab651cca8c4f324e7b4e98f856
ms.sourcegitcommit: d9e2c99c837078fcac0e315025f8fbfbd45ea6e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2017
---
[!INCLUDE[adding-model1](../../includes/mvc-intro/adding-model1.md)]

* <span data-ttu-id="8d3e1-104">将类添加到名为“Movie.cs”的“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="8d3e1-104">Add a class to the *Models* folder named *Movie.cs*.</span></span>
* <span data-ttu-id="8d3e1-105">将以下代码添加到“Models/Movie.cs”文件：</span><span class="sxs-lookup"><span data-stu-id="8d3e1-105">Add the following code to the *Models/Movie.cs* file:</span></span>

<span data-ttu-id="8d3e1-106">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieNoEF.cs?name=snippet_1)]</span><span class="sxs-lookup"><span data-stu-id="8d3e1-106">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieNoEF.cs?name=snippet_1)]</span></span>

<span data-ttu-id="8d3e1-107">数据库需要 `ID` 字段作为主键。</span><span class="sxs-lookup"><span data-stu-id="8d3e1-107">The `ID` field is required by the database for the primary key.</span></span> 

<span data-ttu-id="8d3e1-108">生成应用以确认没有任何错误，最后将 Model 添加到你的 MVC 应用。</span><span class="sxs-lookup"><span data-stu-id="8d3e1-108">Build the app to verify you don't have any errors, and you've finally added a **M**odel to your **M**VC app.</span></span>

## <a name="prepare-the-project-for-scaffolding"></a><span data-ttu-id="8d3e1-109">准备项目以搭建基架</span><span class="sxs-lookup"><span data-stu-id="8d3e1-109">Prepare the project for scaffolding</span></span>

- <span data-ttu-id="8d3e1-110">将以下突出显示的 NuGet 包添加到 MvcMovie.csproj 文件：</span><span class="sxs-lookup"><span data-stu-id="8d3e1-110">Add the following highlighted NuGet packages to the *MvcMovie.csproj* file:</span></span>
             
   <span data-ttu-id="8d3e1-111">[!code-csharp[Main](start-mvc/sample/MvcMovie/MvcMovie.csproj?highlight=7,10)]</span><span class="sxs-lookup"><span data-stu-id="8d3e1-111">[!code-csharp[Main](start-mvc/sample/MvcMovie/MvcMovie.csproj?highlight=7,10)]</span></span>

- <span data-ttu-id="8d3e1-112">保存该文件，并对信息性消息“存在未解析的依赖项”选择“还原”。</span><span class="sxs-lookup"><span data-stu-id="8d3e1-112">Save the file and select **Restore** to the **Info** message "There are unresolved dependencies".</span></span>
- <span data-ttu-id="8d3e1-113">创建 Models/MvcMovieContext.cs 文件并添加以下 `MvcMovieContext` 类：</span><span class="sxs-lookup"><span data-stu-id="8d3e1-113">Create a *Models/MvcMovieContext.cs* file and add the following `MvcMovieContext` class:</span></span>

   <span data-ttu-id="8d3e1-114">[!code-csharp[Main](start-mvc/sample/MvcMovie/Models/MvcMovieContext.cs)]</span><span class="sxs-lookup"><span data-stu-id="8d3e1-114">[!code-csharp[Main](start-mvc/sample/MvcMovie/Models/MvcMovieContext.cs)]</span></span>
   
- <span data-ttu-id="8d3e1-115">打开 Startup.cs 文件并添加两个 using：</span><span class="sxs-lookup"><span data-stu-id="8d3e1-115">Open the *Startup.cs* file and add two usings:</span></span>

   <span data-ttu-id="8d3e1-116">[!code-csharp[Main](start-mvc/sample/MvcMovie/Startup.cs?name=snippet1&highlight=1,2)]</span><span class="sxs-lookup"><span data-stu-id="8d3e1-116">[!code-csharp[Main](start-mvc/sample/MvcMovie/Startup.cs?name=snippet1&highlight=1,2)]</span></span>

- <span data-ttu-id="8d3e1-117">将数据库上下文添加到 Startup.cs 文件：</span><span class="sxs-lookup"><span data-stu-id="8d3e1-117">Add the database context to the *Startup.cs* file:</span></span>

   <span data-ttu-id="8d3e1-118">[!code-csharp[Main](start-mvc/sample/MvcMovie/Startup.cs?name=snippet2&highlight=6-7)]</span><span class="sxs-lookup"><span data-stu-id="8d3e1-118">[!code-csharp[Main](start-mvc/sample/MvcMovie/Startup.cs?name=snippet2&highlight=6-7)]</span></span>

  <span data-ttu-id="8d3e1-119">这会告诉实体框架，数据模型中包含哪些模型类。</span><span class="sxs-lookup"><span data-stu-id="8d3e1-119">This tells Entity Framework which model classes are included in the data model.</span></span> <span data-ttu-id="8d3e1-120">现在定义一个 Movie 对象实体集，此实体集会表示为数据库中的一个 Movie 表。</span><span class="sxs-lookup"><span data-stu-id="8d3e1-120">You're defining one *entity set* of Movie objects, which will be represented in the database as a Movie table.</span></span>

- <span data-ttu-id="8d3e1-121">生成项目并确定没有任何错误。</span><span class="sxs-lookup"><span data-stu-id="8d3e1-121">Build the project to verify there are no errors.</span></span>

## <a name="scaffold-the-moviecontroller"></a><span data-ttu-id="8d3e1-122">为 MovieController 搭建基架</span><span class="sxs-lookup"><span data-stu-id="8d3e1-122">Scaffold the MovieController</span></span>

<span data-ttu-id="8d3e1-123">在项目文件夹中打开终端窗口，然后运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="8d3e1-123">Open a terminal window in the project folder and run the following commands:</span></span>

```
dotnet restore
dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries 
```

> [!NOTE]
> <span data-ttu-id="8d3e1-124">如果在基架命令运行时遇到错误，请参阅[基架存储库中的问题 444](https://github.com/aspnet/scaffolding/issues/444)，以获得解决方案。</span><span class="sxs-lookup"><span data-stu-id="8d3e1-124">If you get an error when the scaffolding command runs, see [issue 444 in the scaffolding repository](https://github.com/aspnet/scaffolding/issues/444) for a workaround.</span></span>

<span data-ttu-id="8d3e1-125">基架引擎创建以下组件：</span><span class="sxs-lookup"><span data-stu-id="8d3e1-125">The scaffolding engine creates the following:</span></span>

* <span data-ttu-id="8d3e1-126">电影控制器 (Controllers/MoviesController.cs)</span><span class="sxs-lookup"><span data-stu-id="8d3e1-126">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="8d3e1-127">“创建”、“删除”、“详细信息”、“编辑”和“索引”页面的 Razor 视图文件 (Views/Movies/\*.cshtml)</span><span class="sxs-lookup"><span data-stu-id="8d3e1-127">Razor view files for Create, Delete, Details, Edit and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="8d3e1-128">自动创建 [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete)（创建、读取、更新和删除）操作方法和视图的过程称为“搭建基架”。</span><span class="sxs-lookup"><span data-stu-id="8d3e1-128">The automatic creation of [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span> <span data-ttu-id="8d3e1-129">你很快就会拥有一个功能完备的 Web 应用程序，并且你可以使用它管理电影数据库。</span><span class="sxs-lookup"><span data-stu-id="8d3e1-129">You'll soon have a fully functional web application that lets you manage a movie database.</span></span>

[!INCLUDE[adding-model 2x](../../includes/mvc-intro/adding-model2xp.md)]

[!INCLUDE[adding-model](../../includes/mvc-intro/adding-model3.md)]

<span data-ttu-id="8d3e1-130">现在你已拥有用于显示、编辑、更新和删除数据的数据库和页面。</span><span class="sxs-lookup"><span data-stu-id="8d3e1-130">You now have a database and pages to display, edit, update and delete data.</span></span> <span data-ttu-id="8d3e1-131">在下一个教程中，我们将使用此数据库。</span><span class="sxs-lookup"><span data-stu-id="8d3e1-131">In the next tutorial, we'll work with the database.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="8d3e1-132">其他资源</span><span class="sxs-lookup"><span data-stu-id="8d3e1-132">Additional resources</span></span>

* [<span data-ttu-id="8d3e1-133">标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="8d3e1-133">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="8d3e1-134">全球化和本地化</span><span class="sxs-lookup"><span data-stu-id="8d3e1-134">Globalization and localization</span></span>](xref:fundamentals/localization)

>[!div class="step-by-step"]
<span data-ttu-id="8d3e1-135">[上一篇 - 添加视图](adding-view.md)
[下一篇 - 使用 SQLite](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="8d3e1-135">[Previous - Add a view](adding-view.md)
[Next - Working with SQLite](working-with-sql.md)</span></span>
