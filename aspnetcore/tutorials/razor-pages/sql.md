---
title: "使用 SQL Server LocalDB 和 ASP.NET Core"
author: rick-anderson
description: "说明如何使用 SQL Server LocalDB 和 ASP.NET Core"
keywords: "ASP.NET Core, Razor 页面, Razor, MVC, SQL, LocalDB"
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: aspnet-core
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 1e35ce49980bf026de35359cdf413961051e3bee
ms.sourcegitcommit: e3b1726cc04e80dc28464c35259edbd3bc39a438
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2017
---
# <a name="working-with-sql-server-localdb-and-aspnet-core"></a><span data-ttu-id="652e1-104">使用 SQL Server LocalDB 和 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="652e1-104">Working with SQL Server LocalDB and ASP.NET Core</span></span>

<span data-ttu-id="652e1-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="652e1-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span> 

<span data-ttu-id="652e1-106">`MovieContext` 对象处理连接到数据库并将 `Movie` 对象映射到数据库记录的任务。</span><span class="sxs-lookup"><span data-stu-id="652e1-106">The `MovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="652e1-107">在 Startup.cs 文件的 `ConfigureServices` 方法中向[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文：</span><span class="sxs-lookup"><span data-stu-id="652e1-107">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

<span data-ttu-id="652e1-108">ASP.NET Core [配置](xref:fundamentals/configuration)系统会读取 `ConnectionString`。</span><span class="sxs-lookup"><span data-stu-id="652e1-108">The ASP.NET Core [Configuration](xref:fundamentals/configuration) system reads the `ConnectionString`.</span></span> <span data-ttu-id="652e1-109">为了进行本地开发，它会从 appsettings.json 文件获取连接字符串：</span><span class="sxs-lookup"><span data-stu-id="652e1-109">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[Main](razor-pages-start/sample/RazorPagesMovie/appsettings.json?highlight=2&range=8-10)]

<span data-ttu-id="652e1-110">将应用部署到测试或生产服务器时，可使用环境变量或另一种方法将连接字符串设置为实际的 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="652e1-110">When you deploy the app to a test or production server, you can use an environment variable or another approach to set the connection string to a real SQL Server.</span></span> <span data-ttu-id="652e1-111">有关详细信息，请参阅[配置](xref:fundamentals/configuration)。</span><span class="sxs-lookup"><span data-stu-id="652e1-111">See [Configuration](xref:fundamentals/configuration) for more information.</span></span>

## <a name="sql-server-express-localdb"></a><span data-ttu-id="652e1-112">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="652e1-112">SQL Server Express LocalDB</span></span>

<span data-ttu-id="652e1-113">LocalDB 是 SQL Server Express 数据库引擎的轻型版本，专门针对程序开发。</span><span class="sxs-lookup"><span data-stu-id="652e1-113">LocalDB is a lightweight version of the SQL Server Express Database Engine that is targeted for program development.</span></span> <span data-ttu-id="652e1-114">LocalDB 按需启动并在用户模式下运行，因此没有复杂的配置。</span><span class="sxs-lookup"><span data-stu-id="652e1-114">LocalDB starts on demand and runs in user mode, so there is no complex configuration.</span></span> <span data-ttu-id="652e1-115">默认情况下，LocalDB 数据库在 C:/Users/\<user\> 目录中创建“\*.mdf”文件。</span><span class="sxs-lookup"><span data-stu-id="652e1-115">By default, LocalDB database creates "\*.mdf" files in the *C:/Users/\<user\>* directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="652e1-116">从“视图”菜单中，打开“SQL Server 对象资源管理器”(SSOX)。</span><span class="sxs-lookup"><span data-stu-id="652e1-116">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![“视图”菜单](sql/_static/ssox.png)

* <span data-ttu-id="652e1-118">右键单击 `Movie` 表，然后选择“视图设计器”：</span><span class="sxs-lookup"><span data-stu-id="652e1-118">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Movie 表上打开的上下文菜单](sql/_static/design.png)

  ![设计器中打开的 Movie 表](sql/_static/dv.png)

<span data-ttu-id="652e1-121">请注意 `ID` 旁边的密钥图标。</span><span class="sxs-lookup"><span data-stu-id="652e1-121">Note the key icon next to `ID`.</span></span> <span data-ttu-id="652e1-122">默认情况下，EF 为该主键创建一个名为 `ID` 的属性。</span><span class="sxs-lookup"><span data-stu-id="652e1-122">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="652e1-123">右键单击 `Movie` 表，然后选择“查看数据”：</span><span class="sxs-lookup"><span data-stu-id="652e1-123">Right click on the `Movie` table and select **View Data**:</span></span>

  ![显示表数据的打开的 Movie 表](sql/_static/vd22.png)

## <a name="seed-the-database"></a><span data-ttu-id="652e1-125">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="652e1-125">Seed the database</span></span>

<span data-ttu-id="652e1-126">在 Models 文件夹中创建一个名为 `SeedData` 的新类。</span><span class="sxs-lookup"><span data-stu-id="652e1-126">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="652e1-127">将生成的代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="652e1-127">Replace the generated code with the following:</span></span>

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="652e1-128">如果 DB 中没有任何电影，则会返回种子初始值设定项，并且不会添加任何电影。</span><span class="sxs-lookup"><span data-stu-id="652e1-128">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```
<a name="si"></a>
### <a name="add-the-seed-initializer"></a><span data-ttu-id="652e1-129">添加种子初始值设定项</span><span class="sxs-lookup"><span data-stu-id="652e1-129">Add the seed initializer</span></span>

<span data-ttu-id="652e1-130">将种子初始值设定项添加 Program.cs 文件中的 `Main` 方法末端：</span><span class="sxs-lookup"><span data-stu-id="652e1-130">Add the seed initializer to the end of the `Main` method in the *Program.cs* file:</span></span>

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Program.cs)]

<span data-ttu-id="652e1-131">测试应用</span><span class="sxs-lookup"><span data-stu-id="652e1-131">Test the app</span></span>

* <span data-ttu-id="652e1-132">删除 DB 中的所有记录。</span><span class="sxs-lookup"><span data-stu-id="652e1-132">Delete all the records in the DB.</span></span> <span data-ttu-id="652e1-133">可以使用浏览器中的删除链接，也可以从 [SSOX](xref:tutorials/razor-pages/new-field#ssox) 执行此操作</span><span class="sxs-lookup"><span data-stu-id="652e1-133">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="652e1-134">强制应用初始化（调用 `Startup` 类中的方法），使种子方法能够正常运行。</span><span class="sxs-lookup"><span data-stu-id="652e1-134">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="652e1-135">若要强制进行初始化，必须先停止 IIS Express，然后再重新启动它。</span><span class="sxs-lookup"><span data-stu-id="652e1-135">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="652e1-136">可以使用以下任一方法来执行此操作：</span><span class="sxs-lookup"><span data-stu-id="652e1-136">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="652e1-137">右键单击通知区域中的 IIS Express 系统任务栏图标，然后点击“退出”或“停止站点”：</span><span class="sxs-lookup"><span data-stu-id="652e1-137">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express 系统任务栏图标](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![上下文菜单](sql/_static/stopIIS.png)

   * <span data-ttu-id="652e1-140">如果是在非调试模式下运行 VS 的，请按 F5 以在调试模式下运行。</span><span class="sxs-lookup"><span data-stu-id="652e1-140">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
   * <span data-ttu-id="652e1-141">如果是在调试模式下运行 VS 的，请停止调试程序并按 F5。</span><span class="sxs-lookup"><span data-stu-id="652e1-141">If you were running VS in debug mode, stop the debugger and press F5.</span></span>
   
<span data-ttu-id="652e1-142">应用将显示设定为种子的数据：</span><span class="sxs-lookup"><span data-stu-id="652e1-142">The app shows the seeded data:</span></span>

![在 Chrome 中打开的显示电影数据的电影应用程序](sql/_static/m55.png)

<span data-ttu-id="652e1-144">在下一教程中将对数据的展示进行整理。</span><span class="sxs-lookup"><span data-stu-id="652e1-144">The next tutorial will clean up the presentation of the data.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="652e1-145">[上一篇：已搭建基架的 Razor 页面](xref:tutorials/razor-pages/page)
[下一篇：更新页面](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="652e1-145">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>
