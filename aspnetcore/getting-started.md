---
title: "ASP.NET Core 2.0 入门"
author: rick-anderson
description: "介绍如何使用 ASP.NET Core 创建并运行简单的 Hello World 应用的快速教程。"
keywords: "ASP.NET Core, 教程, 入门"
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: get-started-article
ms.assetid: 73543e9d-d9d5-47d6-9664-17a9beea6cd3
ms.technology: aspnet
ms.prod: asp.net-core
uid: getting-started
ms.openlocfilehash: 3399df3958093da9117b013736b1cb370fd6beb2
ms.sourcegitcommit: 297ee5d2f3b3b24eb8a2c4a25195c9e2973cb91b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2017
---
# <a name="getting-started-with-aspnet-core"></a>ASP.NET Core 入门

> [!NOTE]
> 这些说明适用于 ASP.NET Core 的最新版本。 想要从早期版本开始？ 请参阅[本教程的 1.1 版本](xref:getting-started-1.1)。

1. 安装 [.NET Core](https://microsoft.com/net/core/)。

2. 创建新的 .NET Core 项目。

   在 macOS 和 Linux 上，打开终端窗口。 在 Windows 上，打开命令提示符。

   ```terminal
   mkdir aspnetcoreapp
   cd aspnetcoreapp
   dotnet new web
   ```
    
4. 运行应用。

   如有需要，`dotnet run` 命令会首先生成应用。

   ```terminal
   dotnet run
   ```

5. 浏览到 `http://localhost:5000`

### <a name="next-steps"></a>后续步骤

有关入门教程，请参阅 [ASP.NET Core 教程](tutorials/index.md)

有关 ASP.NET Core 概念和体系结构的简介，请参阅 [ASP.NET Core 简介](index.md)和 [ASP.NET Core 基础知识](fundamentals/index.md)。

ASP.NET Core 应用可使用 .NET Core 或.NET Framework 基类库和运行时。 有关详细信息，请参阅[在 .NET Core 和 .NET Framework 之间进行选择](https://docs.microsoft.com/dotnet/articles/standard/choosing-core-framework-server)。
