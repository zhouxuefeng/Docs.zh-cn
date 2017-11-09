---
title: "使用 ASP.NET Core 和 Visual Studio for Windows 创建 Web API"
author: rick-anderson
description: "使用 ASP.NET Core MVC 和 Visual Studio for Windows 生成 Web API"
keywords: "ASP.NET Core, WebAPI, Web API, REST, HTTP, 服务, HTTP 服务"
ms.author: riande
manager: wpickett
ms.date: 08/15/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-web-api
ms.openlocfilehash: 617b11cd7652e393c06446c62138802e4a4e90df
ms.sourcegitcommit: 67f54fabbfa4e3942f5bfe1f8a7fdfe4a7a75358
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2017
---
#<a name="create-a-web-api-with-aspnet-core-and-visual-studio-for-windows"></a>使用 ASP.NET Core 和 Visual Studio for Windows 创建 Web API

作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Mike Wasson](https://github.com/mikewasson)

在本教程中，将生成用于管理“待办事项”列表的 Web API。 不会生成 UI。

本教程有三个版本：

* Windows：使用 Visual Studio for Windows 创建 Web API（本教程）
* macOS：[使用 Visual Studio for Mac 创建 Web API](xref:tutorials/first-web-api-mac)
* macOS、Linux、Windows：[使用 Visual Studio Code 创建 Web API](xref:tutorials/web-api-vsc)

<!-- WARNING: The code AND images in this doc are used by uid: tutorials/web-api-vsc, tutorials/first-web-api-mac and tutorials/first-web-api. If you change any code/images in this tutorial, update uid: tutorials/web-api-vsc -->

[!INCLUDE[intro to web API](../includes/webApi/intro.md)]

## <a name="prerequisites"></a>先决条件

[!INCLUDE[install 2.0](../includes/install2.0.md)]

请参阅[此 PDF](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/first-web-api/_static/_webAPI.pdf) 了解有关 ASP.NET Core 1.1 版本的信息。

## <a name="create-the-project"></a>创建项目

在 Visual Studio 中，选择“文件”菜单 >“新建” > “项目”。

选择“ASP.NET Core Web 应用程序(.NET Core)”项目模板。 将此项目命名为 `TodoApi` ，然后选择“确定”。

![“新建项目”对话框](first-web-api/_static/new-project.png)

在“新建 ASP.NET Core Web 应用程序 - TodoApi”对话框中，选择“Web API”模板。 选择“确定”。 请不要选择“启用 Docker 支持”。

![从 ASP.NET Core 模板选择的 Web API 项目模板的“新建 ASP.NET Web 应用程序”对话框](first-web-api/_static/web-api-project.png)

### <a name="launch-the-app"></a>启动应用

在 Visual Studio 中，按 CTRL+F5 启动应用。 Visual Studio 启动浏览器并导航到 `http://localhost:port/api/values`，其中“端口”是随机选择的端口号。 Chrome、Edge 和 Firefox 将显示以下内容：

```
["value1","value2"]
``` 

### <a name="add-a-model-class"></a>添加模型类

模型是表示应用程序中的数据的对象。 在此示例中，唯一的一个模型是待办事项。

添加名为“Models”的文件夹。 在解决方案资源管理器中，右键单击项目。 选择“添加” > “新建文件夹”。 将文件夹命名为“Models”。

注意：模型类可位于项目的任意位置，但按照惯例会使用“Models”文件夹。

添加 `TodoItem` 类。 右键单击“Models”文件夹，然后选择“添加” > “类”。 将此类命名为 `TodoItem`，然后选择“添加”。

将生成的代码替换为以下内容：

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoItem.cs)]

创建 `TodoItem` 时，数据库将生成 `Id`。

### <a name="create-the-database-context"></a>创建数据库上下文

数据库上下文是为给定数据模型协调实体框架功能的主类。 此类由 `Microsoft.EntityFrameworkCore.DbContext` 类派生而来。

添加 `TodoContext` 类。 右键单击“Models”文件夹，然后选择“添加” > “类”。 将此类命名为 `TodoContext`，然后选择“添加”。

将生成的代码替换为以下内容：

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoContext.cs)]

[!INCLUDE[Register the database context](../includes/webApi/register_dbContext.md)]

### <a name="add-a-controller"></a>添加控制器

在解决方案资源管理器中，右键单击“控制器”文件夹。 选择“添加” > “新建项”。 在“添加新项”对话框中，选择“Web API 控制器类”模板。 将此类命名为 `TodoController`。

![“添加新项”对话框，“控制器”显示在搜索框中，并且“Web API 控制器”已选中](first-web-api/_static/new_controller.png)

将生成的代码替换为以下内容：

[!INCLUDE[code and get todo items](../includes/webApi/getTodoItems.md)]
  
### <a name="launch-the-app"></a>启动应用

在 Visual Studio 中，按 CTRL+F5 启动应用。 Visual Studio 启动浏览器并导航到 `http://localhost:port/api/values`，其中“端口”是随机选择的端口号。 如果使用的是 Chrome、Edge 或 Firefox，则会显示数据。 如果使用的是 IE，IE 将提示你打开或保存 values.json 文件。 导航到刚刚创建的 `Todo` 控制器 `http://localhost:port/api/todo`。

[!INCLUDE[last part of web API](../includes/webApi/end.md)]

[!INCLUDE[next steps](../includes/webApi/next.md)]

<!-- test -->
