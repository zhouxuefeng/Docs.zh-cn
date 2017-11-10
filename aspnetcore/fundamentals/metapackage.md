---
title: "有关 ASP.NET 核心 Microsoft.AspNetCore.All metapackage 2.x 及更高版本"
author: Rick-Anderson
description: "Microsoft.AspNetCore.All metapackage 包括所有受支持的 ASP.NET Core 和实体框架核心包，以及其依赖项。"
keywords: ASP.NET Core,NuGet,package,Microsoft.AspNetCore.All,metapackage
ms.author: riande
manager: wpickett
ms.date: 09/20/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/metapackage
ms.openlocfilehash: ff25d80be907994f7ac3d64a8ffa39ae53278ba6
ms.sourcegitcommit: 73bf6b222474d9f1f6aba3feaca4e191069d2121
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2017
---
#<a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-2x"></a>有关 ASP.NET 核心 Microsoft.AspNetCore.All metapackage 2.x

此功能需要 ASP.NET Core 2.x 目标.NET 核心 2.x。

ASP.NET Core 的 [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) 元包包括：

* ASP.NET Core 团队支持的所有包。
* Entity Framework Core 支持的所有包。 
* ASP.NET Core 和 Entity Framework Core 使用的内部和第三方依赖关系。 

ASP.NET 核心的所有功能 2.x 并且实体框架核心 2.x 包含在`Microsoft.AspNetCore.All`包。 默认的项目模板使用此程序包。

版本数`Microsoft.AspNetCore.All`metapackage 表示 ASP.NET Core 版本和实体框架 Core 版本 （.NET Core 版本与对齐）。

应用程序使用`Microsoft.AspNetCore.All`metapackage 自动充分利用[.NET 核心运行时存储](https://docs.microsoft.com/dotnet/core/deploying/runtime-store)。 运行时存储包含运行 ASP.NET Core 2.x 应用程序所需的所有运行时资产。 当你使用`Microsoft.AspNetCore.All`metapackage，**没有**资产从引用的 ASP.NET Core NuGet 包与应用程序部署&mdash;.NET 核心运行时存储区包含这些资产。 预编译运行时存储中的资产以提高应用程序启动时间。

你可以使用包修整过程删除不使用的包。 裁剪后的包不在发布的应用程序输出中。

以下*.csproj*文件引用`Microsoft.AspNetCore.All`metapackage 有关 ASP.NET 核心：

[!code-xml[Main](..\mvc\views\view-compilation\sample\MvcRazorCompileOnPublish2.csproj?highlight=9)]
