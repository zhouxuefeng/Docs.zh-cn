---
title: "将模型添加到 ASP.NET MVC Core 应用"
author: rick-anderson
description: "将模型添加到简单的 ASP.NET Core 应用。"
keywords: "ASP.NET Core, MVC, 基架, 搭建基架"
ms.author: riande
manager: wpickett
ms.devlang: csharp
ms.date: 09/15/2017
ms.topic: get-started-article
ms.assetid: 8dc28498-eeee-1638-b903-b593059e9f39
ms.technology: aspnet
ms.prod: .net-core
helpviewer_keywords: aspnet, csharp, mvc
uid: tutorials/first-mvc-app-mac/adding-model
ms.openlocfilehash: 36819284073eb1cb20b19c41512944e34c54c6d3
ms.sourcegitcommit: 3fece4e2869581df72090ff5e82af1a09d927699
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2017
---
[!INCLUDE[adding-model](../../includes/mvc-intro/adding-model1.md)]

* <span data-ttu-id="98820-104">右键单击“Models”文件夹，然后选择“添加” > “新建文件”。</span><span class="sxs-lookup"><span data-stu-id="98820-104">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span> 
* <span data-ttu-id="98820-105">在“新建文件”对话框中：</span><span class="sxs-lookup"><span data-stu-id="98820-105">In the **New File** dialog:</span></span>

  * <span data-ttu-id="98820-106">在左侧窗格中，选择“常规”。</span><span class="sxs-lookup"><span data-stu-id="98820-106">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="98820-107">在中间窗格中，选择“空类”。</span><span class="sxs-lookup"><span data-stu-id="98820-107">Select **Empty Class** in the center pain.</span></span>
  * <span data-ttu-id="98820-108">将此类命名为“Movie”，然后选择“新建”。</span><span class="sxs-lookup"><span data-stu-id="98820-108">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="98820-109">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="98820-109">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieNoEF.cs?name=snippet_1)]

<span data-ttu-id="98820-110">数据库需要 `ID` 字段作为主键。</span><span class="sxs-lookup"><span data-stu-id="98820-110">The `ID` field is required by the database for the primary key.</span></span>

<span data-ttu-id="98820-111">生成项目以验证有没有任何错误存在。</span><span class="sxs-lookup"><span data-stu-id="98820-111">Build the project to verify you don't have any errors.</span></span> <span data-ttu-id="98820-112">现在 MVC 应用中已具有模型。</span><span class="sxs-lookup"><span data-stu-id="98820-112">You now have a **M**odel in your **M**VC app.</span></span>

## <a name="prepare-the-project-for-scaffolding"></a><span data-ttu-id="98820-113">准备项目以搭建基架</span><span class="sxs-lookup"><span data-stu-id="98820-113">Prepare the project for scaffolding</span></span>

- <span data-ttu-id="98820-114">右键单击项目文件，然后选择“工具”>“编辑文件”。</span><span class="sxs-lookup"><span data-stu-id="98820-114">Right click on the project file, and then select **Tools > Edit File**.</span></span>

  ![上述步骤的视图](adding-model/_static/1.png)

- <span data-ttu-id="98820-116">将以下突出显示的 NuGet 包添加到 MvcMovie.csproj 文件：</span><span class="sxs-lookup"><span data-stu-id="98820-116">Add the following highlighted NuGet packages to the *MvcMovie.csproj* file:</span></span>
             
  [!code-csharp[Main](../first-mvc-app-xplat/start-mvc/sample/MvcMovie/MvcMovie.csproj?highlight=7,10)]

- <span data-ttu-id="98820-117">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="98820-117">Save the file.</span></span>

- <span data-ttu-id="98820-118">创建 Models/MvcMovieContext.cs 文件并添加以下 `MvcMovieContext` 类：[!code-csharp[Main](../../tutorials/first-mvc-app-xplat/start-mvc/sample/MvcMovie/Models/MvcMovieContext.cs)]</span><span class="sxs-lookup"><span data-stu-id="98820-118">Create a *Models/MvcMovieContext.cs* file and add the following `MvcMovieContext` class:  [!code-csharp[Main](../../tutorials/first-mvc-app-xplat/start-mvc/sample/MvcMovie/Models/MvcMovieContext.cs)]</span></span>
   
- <span data-ttu-id="98820-119">打开 Startup.cs 文件并添加两个 using：[!code-csharp[Main](../../tutorials/first-mvc-app-xplat/start-mvc/sample/MvcMovie/Startup.cs?name=snippet1&highlight=1,2)]</span><span class="sxs-lookup"><span data-stu-id="98820-119">Open the *Startup.cs* file and add two usings:  [!code-csharp[Main](../../tutorials/first-mvc-app-xplat/start-mvc/sample/MvcMovie/Startup.cs?name=snippet1&highlight=1,2)]</span></span>

- <span data-ttu-id="98820-120">将数据库上下文添加到 Startup.cs 文件：</span><span class="sxs-lookup"><span data-stu-id="98820-120">Add the database context to the *Startup.cs* file:</span></span>

   [!code-csharp[Main](../../tutorials/first-mvc-app-xplat/start-mvc/sample/MvcMovie/Startup.cs?name=snippet2&highlight=6-7)]

  <span data-ttu-id="98820-121">这会告诉实体框架，数据模型中包含哪些模型类。</span><span class="sxs-lookup"><span data-stu-id="98820-121">This tells Entity Framework which model classes are included in the data model.</span></span> <span data-ttu-id="98820-122">现在定义一个 Movie 对象实体集，此实体集会表示为数据库中的一个 Movie 表。</span><span class="sxs-lookup"><span data-stu-id="98820-122">You're defining one *entity set* of Movie objects, which will be represented in the database as a Movie table.</span></span>

- <span data-ttu-id="98820-123">生成项目并确定没有任何错误。</span><span class="sxs-lookup"><span data-stu-id="98820-123">Build the project to verify there are no errors.</span></span>

## <a name="scaffold-the-moviecontroller"></a><span data-ttu-id="98820-124">为 MovieController 搭建基架</span><span class="sxs-lookup"><span data-stu-id="98820-124">Scaffold the MovieController</span></span>

<span data-ttu-id="98820-125">在项目文件夹中打开终端窗口，然后运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="98820-125">Open a terminal window in the project folder and run the following commands:</span></span>

```
dotnet restore
dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries 
```
<span data-ttu-id="98820-126">如果收到错误 `No executable found matching command "dotnet-aspnet-codegenerator", verify`：</span><span class="sxs-lookup"><span data-stu-id="98820-126">If you get the error `No executable found matching command "dotnet-aspnet-codegenerator", verify`:</span></span>

 * <span data-ttu-id="98820-127">你现在位于项目目录中。</span><span class="sxs-lookup"><span data-stu-id="98820-127">You are in the project directory.</span></span> <span data-ttu-id="98820-128">项目目录中包含 Program.cs、Startup.cs、和 .csproj 文件。</span><span class="sxs-lookup"><span data-stu-id="98820-128">The project directory has the *Program.cs*, *Startup.cs* and *.csproj* files.</span></span>
 * <span data-ttu-id="98820-129">你的 dotnet 版本是 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="98820-129">Your dotnet version is 1.1 or higher.</span></span> <span data-ttu-id="98820-130">运行 `dotnet` 获取版本。</span><span class="sxs-lookup"><span data-stu-id="98820-130">Run `dotnet` to get the version.</span></span>
 * <span data-ttu-id="98820-131">你已将 `<DotNetCliToolReference>` 元素添加到 [MvcMovie.csproj 文件](#prepare-the-project-for-scaffolding)。</span><span class="sxs-lookup"><span data-stu-id="98820-131">You have added the `<DotNetCliToolReference>` elment to the [MvcMovie.csproj file](#prepare-the-project-for-scaffolding).</span></span>
 
<!--
> [!NOTE]
> If you get an error when the scaffolding command runs, see [issue 444 in the scaffolding repository](https://github.com/aspnet/scaffolding/issues/444) for a workaround.
-->

<span data-ttu-id="98820-132">基架引擎创建以下组件：</span><span class="sxs-lookup"><span data-stu-id="98820-132">The scaffolding engine creates the following:</span></span>

* <span data-ttu-id="98820-133">电影控制器 (Controllers/MoviesController.cs)</span><span class="sxs-lookup"><span data-stu-id="98820-133">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="98820-134">“创建”、“删除”、“详细信息”、“编辑”和“索引”页面的 Razor 视图文件 (Views/Movies/\*.cshtml)</span><span class="sxs-lookup"><span data-stu-id="98820-134">Razor view files for Create, Delete, Details, Edit and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="98820-135">自动创建 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)（创建、读取、更新和删除）操作方法和视图的过程称为“搭建基架”。</span><span class="sxs-lookup"><span data-stu-id="98820-135">The automatic creation of [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span> <span data-ttu-id="98820-136">你很快就会拥有一个功能完备的 Web 应用程序，并且你可以使用它管理电影数据库。</span><span class="sxs-lookup"><span data-stu-id="98820-136">You'll soon have a fully functional web application that lets you manage a movie database.</span></span>

### <a name="add-the-files-to-visual-studio"></a><span data-ttu-id="98820-137">将文件添加到 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98820-137">Add the files to Visual Studio</span></span>

* <span data-ttu-id="98820-138">将 MovieController.cs 文件添加到 Visual Studio 项目：</span><span class="sxs-lookup"><span data-stu-id="98820-138">Add the *MovieController.cs* file to the Visual Studio project:</span></span>

  * <span data-ttu-id="98820-139">右键单击“控制器”文件夹，然后选择“添加”>“添加文件”。</span><span class="sxs-lookup"><span data-stu-id="98820-139">Right-click on the *Controllers* folder and select **Add > Add Files**.</span></span>
  * <span data-ttu-id="98820-140">选择 MovieController.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="98820-140">Select the *MovieController.cs* file.</span></span>

* <span data-ttu-id="98820-141">添加“电影”文件夹和视图：</span><span class="sxs-lookup"><span data-stu-id="98820-141">Add the *Movies* folder and views:</span></span>

  * <span data-ttu-id="98820-142">右键单击“视图”文件夹，然后选择“添加”>“添加现有文件夹”。</span><span class="sxs-lookup"><span data-stu-id="98820-142">Right-click on the *Views* folder and select **Add > Add Existing Folder**.</span></span>
  * <span data-ttu-id="98820-143">导航到“视图”文件夹，选择“视图\电影”，然后选择“打开”。</span><span class="sxs-lookup"><span data-stu-id="98820-143">Navigate to the *Views* folder, select *Views\Movies*, and then select **Open**.</span></span>
  * <span data-ttu-id="98820-144">在“从‘电影’选择要添加的文件”对话框中，选择“包括全部”，然后选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="98820-144">In the **Select files to add from Movies** dialog, select **Include All**, and then **OK**.</span></span>

[!INCLUDE[adding-model 2x](../../includes/mvc-intro/adding-model2xp.md)]

[!INCLUDE[adding-model](../../includes/mvc-intro/adding-model3.md)]

<span data-ttu-id="98820-145">现在你已拥有用于显示、编辑、更新和删除数据的数据库和页面。</span><span class="sxs-lookup"><span data-stu-id="98820-145">You now have a database and pages to display, edit, update and delete data.</span></span> <span data-ttu-id="98820-146">在下一个教程中，我们将使用此数据库。</span><span class="sxs-lookup"><span data-stu-id="98820-146">In the next tutorial, we'll work with the database.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="98820-147">其他资源</span><span class="sxs-lookup"><span data-stu-id="98820-147">Additional resources</span></span>

* [<span data-ttu-id="98820-148">标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="98820-148">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="98820-149">全球化和本地化</span><span class="sxs-lookup"><span data-stu-id="98820-149">Globalization and localization</span></span>](xref:fundamentals/localization)

>[!div class="step-by-step"]
<span data-ttu-id="98820-150">[上一篇：添加视图](adding-view.md)
[下一篇：使用 SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="98820-150">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>  
