---
title: "使用 ASP.NET Core 和 Visual Studio for Windows 创建 Web API"
author: rick-anderson
description: "使用 ASP.NET Core MVC 和 Visual Studio for Windows 生成 Web API"
keywords: "ASP.NET Core, WebAPI, Web API, REST, HTTP, 服务, HTTP 服务"
ms.author: riande
manager: wpickett
ms.date: 8/15/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-web-api
ms.openlocfilehash: 4aab61c7ee4498b33a4ea8bbec6033ce9828e2af
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
#<a name="create-a-web-api-with-aspnet-core-and-visual-studio-for-windows"></a><span data-ttu-id="d7864-104">使用 ASP.NET Core 和 Visual Studio for Windows 创建 Web API</span><span class="sxs-lookup"><span data-stu-id="d7864-104">Create a web API with ASP.NET Core and Visual Studio for Windows</span></span>

<span data-ttu-id="d7864-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="d7864-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="d7864-106">在本教程中，将生成用于管理“待办事项”列表的 Web API。</span><span class="sxs-lookup"><span data-stu-id="d7864-106">In this tutorial, you’ll build a web API for managing a list of "to-do" items.</span></span> <span data-ttu-id="d7864-107">不会生成 UI。</span><span class="sxs-lookup"><span data-stu-id="d7864-107">You won’t build a UI.</span></span>

<span data-ttu-id="d7864-108">本教程有三个版本：</span><span class="sxs-lookup"><span data-stu-id="d7864-108">There are 3 versions of this tutorial:</span></span>

* <span data-ttu-id="d7864-109">Windows：使用 Visual Studio for Windows 创建 Web API（本教程）</span><span class="sxs-lookup"><span data-stu-id="d7864-109">Windows: Web API with Visual Studio for Windows (This tutorial)</span></span>
* <span data-ttu-id="d7864-110">macOS：[使用 Visual Studio for Mac 创建 Web API](xref:tutorials/first-web-api-mac)</span><span class="sxs-lookup"><span data-stu-id="d7864-110">macOS: [Web API with Visual Studio for Mac](xref:tutorials/first-web-api-mac)</span></span>
* <span data-ttu-id="d7864-111">macOS、Linux、Windows：[使用 Visual Studio Code 创建 Web API](xref:tutorials/web-api-vsc)</span><span class="sxs-lookup"><span data-stu-id="d7864-111">macOS, Linux, Windows: [Web API with Visual Studio Code](xref:tutorials/web-api-vsc)</span></span>

<!-- WARNING: The code AND images in this doc are used by uid: tutorials/web-api-vsc, tutorials/first-web-api-mac and tutorials/first-web-api. If you change any code/images in this tutorial, update uid: tutorials/web-api-vsc -->

[!INCLUDE[intro to web API](../includes/webApi/intro.md)]

## <a name="prerequisites"></a><span data-ttu-id="d7864-112">先决条件</span><span class="sxs-lookup"><span data-stu-id="d7864-112">Prerequisites</span></span>

[!INCLUDE[install 2.0](../includes/install2.0.md)]

<span data-ttu-id="d7864-113">请参阅[此 PDF](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/first-web-api/_static/_webAPI.pdf) 了解有关 ASP.NET Core 1.1 版本的信息。</span><span class="sxs-lookup"><span data-stu-id="d7864-113">See [this PDF](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/first-web-api/_static/_webAPI.pdf) for the ASP.NET Core 1.1 version.</span></span>

## <a name="create-the-project"></a><span data-ttu-id="d7864-114">创建项目</span><span class="sxs-lookup"><span data-stu-id="d7864-114">Create the project</span></span>

<span data-ttu-id="d7864-115">在 Visual Studio 中，选择“文件”菜单 >“新建” > “项目”。</span><span class="sxs-lookup"><span data-stu-id="d7864-115">From Visual Studio, select **File** menu, > **New** > **Project**.</span></span>

<span data-ttu-id="d7864-116">选择“ASP.NET Core Web 应用程序(.NET Core)”项目模板。</span><span class="sxs-lookup"><span data-stu-id="d7864-116">Select the **ASP.NET Core Web Application (.NET Core)** project template.</span></span> <span data-ttu-id="d7864-117">将此项目命名为 `TodoApi` ，然后选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="d7864-117">Name the project `TodoApi` and select **OK**.</span></span>

![“新建项目”对话框](first-web-api/_static/new-project.png)

<span data-ttu-id="d7864-119">在“新建 ASP.NET Core Web 应用程序 - TodoApi”对话框中，选择“Web API”模板。</span><span class="sxs-lookup"><span data-stu-id="d7864-119">In the **New ASP.NET Core Web Application - TodoApi** dialog, select the **Web API** template.</span></span> <span data-ttu-id="d7864-120">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="d7864-120">Select **OK**.</span></span> <span data-ttu-id="d7864-121">请不要选择“启用 Docker 支持”。</span><span class="sxs-lookup"><span data-stu-id="d7864-121">Do **not** select **Enable Docker Support**.</span></span>

![从 ASP.NET Core 模板选择的 Web API 项目模板的“新建 ASP.NET Web 应用程序”对话框](first-web-api/_static/web-api-project.png)

### <a name="launch-the-app"></a><span data-ttu-id="d7864-123">启动应用</span><span class="sxs-lookup"><span data-stu-id="d7864-123">Launch the app</span></span>

<span data-ttu-id="d7864-124">在 Visual Studio 中，按 CTRL+F5 启动应用。</span><span class="sxs-lookup"><span data-stu-id="d7864-124">In Visual Studio, press CTRL+F5 to launch the app.</span></span> <span data-ttu-id="d7864-125">Visual Studio 启动浏览器并导航到 `http://localhost:port/api/values`，其中“端口”是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="d7864-125">Visual Studio launches a browser and navigates to `http://localhost:port/api/values`, where *port* is a randomly-chosen port number.</span></span> <span data-ttu-id="d7864-126">Chrome、Edge 和 Firefox 将显示以下内容：</span><span class="sxs-lookup"><span data-stu-id="d7864-126">Chrome, Edge, and Firefox display the following:</span></span>

```
["value1","value2"]
``` 

### <a name="add-a-model-class"></a><span data-ttu-id="d7864-127">添加模型类</span><span class="sxs-lookup"><span data-stu-id="d7864-127">Add a model class</span></span>

<span data-ttu-id="d7864-128">模型是表示应用程序中的数据的对象。</span><span class="sxs-lookup"><span data-stu-id="d7864-128">A model is an object that represents the data in your application.</span></span> <span data-ttu-id="d7864-129">在此示例中，唯一的一个模型是待办事项。</span><span class="sxs-lookup"><span data-stu-id="d7864-129">In this case, the only model is a to-do item.</span></span>

<span data-ttu-id="d7864-130">添加名为“Models”的文件夹。</span><span class="sxs-lookup"><span data-stu-id="d7864-130">Add a folder named "Models".</span></span> <span data-ttu-id="d7864-131">在解决方案资源管理器中，右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="d7864-131">In Solution Explorer, right-click the project.</span></span> <span data-ttu-id="d7864-132">选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="d7864-132">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="d7864-133">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="d7864-133">Name the folder *Models*.</span></span>

<span data-ttu-id="d7864-134">注意：模型类可位于项目的任意位置，但按照惯例会使用“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="d7864-134">Note: The model classes go anywhere in your project, but the *Models* folder is used by convention.</span></span>

<span data-ttu-id="d7864-135">添加 `TodoItem` 类。</span><span class="sxs-lookup"><span data-stu-id="d7864-135">Add a `TodoItem` class.</span></span> <span data-ttu-id="d7864-136">右键单击“Models”文件夹，然后选择“添加” > “类”。</span><span class="sxs-lookup"><span data-stu-id="d7864-136">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="d7864-137">将此类命名为 `TodoItem`，然后选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="d7864-137">Name the class `TodoItem` and select **Add**.</span></span>

<span data-ttu-id="d7864-138">将生成的代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="d7864-138">Replace the generated code with the following:</span></span>

<span data-ttu-id="d7864-139">[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoItem.cs)]</span><span class="sxs-lookup"><span data-stu-id="d7864-139">[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoItem.cs)]</span></span>

<span data-ttu-id="d7864-140">创建 `TodoItem` 时，数据库将生成 `Id`。</span><span class="sxs-lookup"><span data-stu-id="d7864-140">The database generates the `Id` when a `TodoItem` is created.</span></span>

### <a name="create-the-database-context"></a><span data-ttu-id="d7864-141">创建数据库上下文</span><span class="sxs-lookup"><span data-stu-id="d7864-141">Create the database context</span></span>

<span data-ttu-id="d7864-142">数据库上下文是为给定数据模型协调实体框架功能的主类。</span><span class="sxs-lookup"><span data-stu-id="d7864-142">The *database context* is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="d7864-143">此类由 `Microsoft.EntityFrameworkCore.DbContext` 类派生而来。</span><span class="sxs-lookup"><span data-stu-id="d7864-143">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

<span data-ttu-id="d7864-144">添加 `TodoContext` 类。</span><span class="sxs-lookup"><span data-stu-id="d7864-144">Add a `TodoContext` class.</span></span> <span data-ttu-id="d7864-145">右键单击“Models”文件夹，然后选择“添加” > “类”。</span><span class="sxs-lookup"><span data-stu-id="d7864-145">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="d7864-146">将此类命名为 `TodoContext`，然后选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="d7864-146">Name the class `TodoContext` and select **Add**.</span></span>

<span data-ttu-id="d7864-147">将生成的代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="d7864-147">Replace the generated code with the following:</span></span>

<span data-ttu-id="d7864-148">[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoContext.cs)]</span><span class="sxs-lookup"><span data-stu-id="d7864-148">[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoContext.cs)]</span></span>

[!INCLUDE[Register the database context](../includes/webApi/register_dbContext.md)]

### <a name="add-a-controller"></a><span data-ttu-id="d7864-149">添加控制器</span><span class="sxs-lookup"><span data-stu-id="d7864-149">Add a controller</span></span>

<span data-ttu-id="d7864-150">在解决方案资源管理器中，右键单击“控制器”文件夹。</span><span class="sxs-lookup"><span data-stu-id="d7864-150">In Solution Explorer, right-click the *Controllers* folder.</span></span> <span data-ttu-id="d7864-151">选择“添加” > “新建项”。</span><span class="sxs-lookup"><span data-stu-id="d7864-151">Select **Add** > **New Item**.</span></span> <span data-ttu-id="d7864-152">在“添加新项”对话框中，选择“Web API 控制器类”模板。</span><span class="sxs-lookup"><span data-stu-id="d7864-152">In the **Add New Item** dialog, select the **Web  API Controller Class** template.</span></span> <span data-ttu-id="d7864-153">将此类命名为 `TodoController`。</span><span class="sxs-lookup"><span data-stu-id="d7864-153">Name the class `TodoController`.</span></span>

![“添加新项”对话框，“控制器”显示在搜索框中，并且“Web API 控制器”已选中](first-web-api/_static/new_controller.png)

<span data-ttu-id="d7864-155">将生成的代码替换为以下内容：</span><span class="sxs-lookup"><span data-stu-id="d7864-155">Replace the generated code with the following:</span></span>

[!INCLUDE[code and get todo items](../includes/webApi/getTodoItems.md)]
  
### <a name="launch-the-app"></a><span data-ttu-id="d7864-156">启动应用</span><span class="sxs-lookup"><span data-stu-id="d7864-156">Launch the app</span></span>

<span data-ttu-id="d7864-157">在 Visual Studio 中，按 CTRL+F5 启动应用。</span><span class="sxs-lookup"><span data-stu-id="d7864-157">In Visual Studio, press CTRL+F5 to launch the app.</span></span> <span data-ttu-id="d7864-158">Visual Studio 启动浏览器并导航到 `http://localhost:port/api/values`，其中“端口”是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="d7864-158">Visual Studio launches a browser and navigates to `http://localhost:port/api/values`, where *port* is a randomly chosen port number.</span></span> <span data-ttu-id="d7864-159">如果使用的是 Chrome、Edge 或 Firefox，则会显示数据。</span><span class="sxs-lookup"><span data-stu-id="d7864-159">If you're using Chrome, Edge or Firefox, the data will be displayed.</span></span> <span data-ttu-id="d7864-160">如果使用的是 IE，IE 将提示你打开或保存 values.json 文件。</span><span class="sxs-lookup"><span data-stu-id="d7864-160">If you're using IE, IE will prompt to you open or save the *values.json* file.</span></span> <span data-ttu-id="d7864-161">导航到刚刚创建的 `Todo` 控制器 `http://localhost:port/api/todo`。</span><span class="sxs-lookup"><span data-stu-id="d7864-161">Navigate to the `Todo` controller we just created `http://localhost:port/api/todo`.</span></span>

[!INCLUDE[last part of web API](../includes/webApi/end.md)]

[!INCLUDE[next steps](../includes/webApi/next.md)]

