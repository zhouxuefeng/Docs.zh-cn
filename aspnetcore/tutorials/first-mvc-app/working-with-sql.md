---
title: "使用 SQL Server LocalDB"
author: rick-anderson
description: "结合简单的 MVC 应用使用 SQL Server LocalDB"
keywords: ASP.NET Core, SQL Server LocalDB, SQL Server, LocalDB
ms.author: riande
manager: wpickett
ms.date: 03/07/2017
ms.topic: get-started-article
ms.assetid: ff8fd9b8-7c98-424d-8641-7524e23bf541
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: dd8b8603d8444c95f086fd593aabe86d60f93ad4
ms.sourcegitcommit: eb025f2166023e1c394a0213c7ed8a9ca7190da5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2017
---
# <a name="working-with-sql-server-localdb"></a><span data-ttu-id="59d4b-104">使用 SQL Server LocalDB</span><span class="sxs-lookup"><span data-stu-id="59d4b-104">Working with SQL Server LocalDB</span></span>

<span data-ttu-id="59d4b-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="59d4b-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="59d4b-106">`MvcMovieContext` 对象处理连接到数据库并将 `Movie` 对象映射到数据库记录的任务。</span><span class="sxs-lookup"><span data-stu-id="59d4b-106">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="59d4b-107">在 Startup.cs 文件的 `ConfigureServices` 方法中向[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文：</span><span class="sxs-lookup"><span data-stu-id="59d4b-107">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

<span data-ttu-id="59d4b-108">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=ConfigureServices&highlight=6-7)]</span><span class="sxs-lookup"><span data-stu-id="59d4b-108">[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=ConfigureServices&highlight=6-7)]</span></span>

<span data-ttu-id="59d4b-109">ASP.NET Core [配置](xref:fundamentals/configuration)系统会读取 `ConnectionString`。</span><span class="sxs-lookup"><span data-stu-id="59d4b-109">The ASP.NET Core [Configuration](xref:fundamentals/configuration) system reads the `ConnectionString`.</span></span> <span data-ttu-id="59d4b-110">为了进行本地开发，它会从 appsettings.json 文件获取连接字符串：</span><span class="sxs-lookup"><span data-stu-id="59d4b-110">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

<span data-ttu-id="59d4b-111">[!code-javascript[Main](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]</span><span class="sxs-lookup"><span data-stu-id="59d4b-111">[!code-javascript[Main](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]</span></span>

<span data-ttu-id="59d4b-112">将应用部署到测试或生产服务器时，可使用环境变量或另一种方法将连接字符串设置为实际的 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="59d4b-112">When you deploy the app to a test or production server, you can use an environment variable or another approach to set the connection string to a real SQL Server.</span></span> <span data-ttu-id="59d4b-113">有关详细信息，请参阅[配置](xref:fundamentals/configuration)。</span><span class="sxs-lookup"><span data-stu-id="59d4b-113">See [Configuration](xref:fundamentals/configuration) for more information.</span></span>

## <a name="sql-server-express-localdb"></a><span data-ttu-id="59d4b-114">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="59d4b-114">SQL Server Express LocalDB</span></span>

<span data-ttu-id="59d4b-115">LocalDB 是 SQL Server Express 数据库引擎的轻型版本，专门针对程序开发。</span><span class="sxs-lookup"><span data-stu-id="59d4b-115">LocalDB is a lightweight version of the SQL Server Express Database Engine that is targeted for program development.</span></span> <span data-ttu-id="59d4b-116">LocalDB 按需启动并在用户模式下运行，因此没有复杂的配置。</span><span class="sxs-lookup"><span data-stu-id="59d4b-116">LocalDB starts on demand and runs in user mode, so there is no complex configuration.</span></span> <span data-ttu-id="59d4b-117">默认情况下，LocalDB 数据库在 C:/Users/\<user\> 目录中创建“\*.mdf”文件。</span><span class="sxs-lookup"><span data-stu-id="59d4b-117">By default, LocalDB database creates "\*.mdf" files in the *C:/Users/\<user\>* directory.</span></span>

* <span data-ttu-id="59d4b-118">从“视图”菜单中，打开“SQL Server 对象资源管理器”(SSOX)。</span><span class="sxs-lookup"><span data-stu-id="59d4b-118">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![“视图”菜单](working-with-sql/_static/ssox.png)

* <span data-ttu-id="59d4b-120">右键单击 `Movie` 表，然后单击“视图设计器”</span><span class="sxs-lookup"><span data-stu-id="59d4b-120">Right click on the `Movie` table **> View Designer**</span></span>

  ![Movie 表上打开的上下文菜单](working-with-sql/_static/design.png)

  ![设计器中打开的 Movie 表](working-with-sql/_static/dv.png)

<span data-ttu-id="59d4b-123">请注意 `ID` 旁边的密钥图标。</span><span class="sxs-lookup"><span data-stu-id="59d4b-123">Note the key icon next to `ID`.</span></span> <span data-ttu-id="59d4b-124">默认情况下，EF 将名为 `ID` 的属性设置为主键。</span><span class="sxs-lookup"><span data-stu-id="59d4b-124">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="59d4b-125">右键单击 `Movie` 表，然后单击“查看数据”</span><span class="sxs-lookup"><span data-stu-id="59d4b-125">Right click on the `Movie` table **> View Data**</span></span>

  ![Movie 表上打开的上下文菜单](working-with-sql/_static/ssox2.png)

  ![显示表数据的打开的 Movie 表](working-with-sql/_static/vd22.png)

## <a name="seed-the-database"></a><span data-ttu-id="59d4b-128">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="59d4b-128">Seed the database</span></span>

<span data-ttu-id="59d4b-129">在 Models 文件夹中创建一个名为 `SeedData` 的新类。</span><span class="sxs-lookup"><span data-stu-id="59d4b-129">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="59d4b-130">将生成的代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="59d4b-130">Replace the generated code with the following:</span></span>

<span data-ttu-id="59d4b-131">[!code-csharp[Main](start-mvc/sample/MvcMovie/Models/SeedData.cs?name=snippet_1)]</span><span class="sxs-lookup"><span data-stu-id="59d4b-131">[!code-csharp[Main](start-mvc/sample/MvcMovie/Models/SeedData.cs?name=snippet_1)]</span></span>

<span data-ttu-id="59d4b-132">如果 DB 中没有任何电影，则会返回种子初始值设定项，并且不会添加任何电影。</span><span class="sxs-lookup"><span data-stu-id="59d4b-132">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>
### <a name="add-the-seed-initializer"></a><span data-ttu-id="59d4b-133">添加种子初始值设定项</span><span class="sxs-lookup"><span data-stu-id="59d4b-133">Add the seed initializer</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="59d4b-134">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="59d4b-134">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="59d4b-135">将种子初始值设定项添加 Program.cs 文件中的 `Main` 方法：</span><span class="sxs-lookup"><span data-stu-id="59d4b-135">Add the seed initializer to the `Main` method in the *Program.cs* file:</span></span>

<span data-ttu-id="59d4b-136">[!code-csharp[Main](start-mvc/sample/MvcMovie/Program.cs?highlight=6,16-32)]</span><span class="sxs-lookup"><span data-stu-id="59d4b-136">[!code-csharp[Main](start-mvc/sample/MvcMovie/Program.cs?highlight=6,16-32)]</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="59d4b-137">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="59d4b-137">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="59d4b-138">将种子初始值设定项添加到 Startup.cs 文件中的 `Configure` 方法末尾：</span><span class="sxs-lookup"><span data-stu-id="59d4b-138">Add the seed initializer to the end of the `Configure` method in the *Startup.cs* file.</span></span>

<span data-ttu-id="59d4b-139">[!code-csharp[Main](start-mvc/sample/MvcMovie/Startup.cs?highlight=9&name=snippet_seed)]</span><span class="sxs-lookup"><span data-stu-id="59d4b-139">[!code-csharp[Main](start-mvc/sample/MvcMovie/Startup.cs?highlight=9&name=snippet_seed)]</span></span>

---

<span data-ttu-id="59d4b-140">测试应用</span><span class="sxs-lookup"><span data-stu-id="59d4b-140">Test the app</span></span>

* <span data-ttu-id="59d4b-141">删除 DB 中的所有记录。</span><span class="sxs-lookup"><span data-stu-id="59d4b-141">Delete all the records in the DB.</span></span> <span data-ttu-id="59d4b-142">可以使用浏览器中的删除链接，也可从 SSOX 执行此操作。</span><span class="sxs-lookup"><span data-stu-id="59d4b-142">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="59d4b-143">强制应用初始化（调用 `Startup` 类中的方法），使种子方法能够正常运行。</span><span class="sxs-lookup"><span data-stu-id="59d4b-143">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="59d4b-144">若要强制进行初始化，必须先停止 IIS Express，然后再重新启动它。</span><span class="sxs-lookup"><span data-stu-id="59d4b-144">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="59d4b-145">可以使用以下任一方法来执行此操作：</span><span class="sxs-lookup"><span data-stu-id="59d4b-145">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="59d4b-146">右键单击通知区域中的 IIS Express 系统任务栏图标，然后点击“退出”或“停止站点”</span><span class="sxs-lookup"><span data-stu-id="59d4b-146">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![IIS Express 系统任务栏图标](working-with-sql/_static/iisExIcon.png)

    ![上下文菜单](working-with-sql/_static/stopIIS.png)

   * <span data-ttu-id="59d4b-149">如果是在非调试模式下运行 VS 的，请按 F5 以在调试模式下运行</span><span class="sxs-lookup"><span data-stu-id="59d4b-149">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
   * <span data-ttu-id="59d4b-150">如果是在调试模式下运行 VS 的，请停止调试程序并按 F5</span><span class="sxs-lookup"><span data-stu-id="59d4b-150">If you were running VS in debug mode, stop the debugger and press F5</span></span>
   
<span data-ttu-id="59d4b-151">应用将显示设定为种子的数据。</span><span class="sxs-lookup"><span data-stu-id="59d4b-151">The app shows the seeded data.</span></span>

![在 Microsoft Edge 中打开的显示电影数据的 MVC 电影应用程序](working-with-sql/_static/m55.png)

>[!div class="step-by-step"]
<span data-ttu-id="59d4b-153">[上一页](adding-model.md)
[下一页](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="59d4b-153">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>  
