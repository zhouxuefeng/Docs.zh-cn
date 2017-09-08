---
title: "有关 ASP.NET 核心 Microsoft.AspNetCore.All metapackage 2.x 及更高版本"
author: Rick-Anderson
description: "Microsoft.AspNetCore.All metapackage 包括所有受支持的包。"
keywords: "ASP.NET 核心，NuGet 包，Microsoft.AspNetCore.All、 metapackage"
ms.author: riande
manager: wpickett
ms.date: 07/16/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/metapackage
ms.openlocfilehash: 255438a4ce36ce4978f8c8ee298388a25ac00d17
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
#<a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-2x"></a>有关 ASP.NET 核心 Microsoft.AspNetCore.All metapackage 2.x

此功能需要 ASP.NET Core 2.x。

[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) ASP.NET Core 的 metapackage 包括：

* 所有受 ASP.NET Core 团队支持包。
* 所有支持由实体框架核心的包。 
* 内部和第三方依赖关系，使用 ASP.NET Core 和实体框架核心。 

ASP.NET 核心的所有功能 2.x 并且实体框架核心 2.x 包含在`Microsoft.AspNetCore.All`包。 默认的项目模板使用此程序包。

版本数`Microsoft.AspNetCore.All`metapackage 表示 ASP.NET Core 版本和实体框架 Core 版本 （.NET Core 版本与对齐）。

应用程序使用`Microsoft.AspNetCore.All`metapackage 自动充分利用.NET 核心运行时存储。 运行时存储包含运行 ASP.NET Core 2.x 应用程序所需的所有运行时资产。 当你使用`Microsoft.AspNetCore.All`metapackage，**没有**资产从引用的 ASP.NET Core NuGet 包与应用程序部署&mdash;.NET 核心运行时存储区包含这些资产。 <!-- todo add link to Runtime store -->预编译运行时存储中的资产以提高应用程序启动时间。

你可以使用包修整过程删除不使用的包。 裁剪后的包不在发布的应用程序输出中。

以下*.csproj*文件引用`Microsoft.AspNetCore.All`metapackage 有关 ASP.NET 核心：

[!code-xml[Main](..\mvc\views\view-compilation\sample\MvcRazorCompileOnPublish2.csproj?highlight=9)]
