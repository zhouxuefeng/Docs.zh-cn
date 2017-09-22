---
title: "ASP.NET 核心目录结构"
author: guardrex
description: "已发布 ASP.NET Core 应用程序目录结构。"
keywords: "ASP.NET 核心，目录结构"
ms.author: riande
manager: wpickett
ms.date: 03/15/2017
ms.topic: article
ms.assetid: e55eb131-d42e-4bf6-b130-fd626082243c
ms.technology: aspnet
ms.prod: asp.net-core
uid: hosting/directory-structure
ms.openlocfilehash: 9ec635b596520e3d19f4040758dd59c1cbca97f4
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2017
---
# <a name="directory-structure-of-published-aspnet-core-apps"></a>已发布的 ASP.NET Core 应用的目录结构

通过[Luke Latham](https://github.com/GuardRex)

在 ASP.NET 核，应用程序目录中，*发布*，组成应用程序文件、 配置文件、 静态资产、 包和的运行时 （独立的应用）。 这是以前版本的 ASP.NET，则整个应用程序时，web 根目录中位于相同的目录结构。

| 应用类型 | 目录结构 |
| --- | --- |
| 依赖于框架的部署 | <ul><li>发布\*<ul><li>日志\*（如果包含在 publishOptions）</li><li>refs\*</li><li>运行时\*</li><li>视图\*（如果包含在 publishOptions）</li><li>wwwroot\* （如果包含在 publishOptions）</li><li>.dll 文件</li><li>myapp.deps.json</li><li>myapp.dll</li><li>myapp.pdb</li><li>myapp。PrecompiledViews.dll （如果预编译 Razor 视图）</li><li>myapp。PrecompiledViews.pdb （如果预编译 Razor 视图）</li><li>myapp.runtimeconfig.json</li><li>（如果包含在 publishOptions） 的 web.config</li></ul></li></ul> |
| 自包含的部署 | <ul><li>发布\*<ul><li>日志\*（如果包含在 publishOptions）</li><li>refs\*</li><li>视图\*（如果包含在 publishOptions）</li><li>wwwroot\* （如果包含在 publishOptions）</li><li>.dll 文件</li><li>myapp.deps.json</li><li>myapp.exe</li><li>myapp.pdb</li><li>myapp。PrecompiledViews.dll （如果预编译 Razor 视图）</li><li>myapp。PrecompiledViews.pdb （如果预编译 Razor 视图）</li><li>myapp.runtimeconfig.json</li><li>（如果包含在 publishOptions） 的 web.config</li></ul></li></ul> |
\*指示目录

内容*发布*目录表示*内容的根路径*，也称为*应用程序基路径*，部署。 任何名称提供给*发布*部署中的目录，其位置用作托管的应用程序服务器的物理路径。 *Wwwroot*目录中，如果存在，仅包含静态资产。 *日志*目录可能包含在部署项目中创建它并添加`<Target>`到下面显示的元素你*.csproj*文件或通过以物理方式上创建目录服务器。

```xml
<Target Name="CreateLogsFolder" AfterTargets="AfterPublish">
  <MakeDir Directories="$(PublishDir)logs" Condition="!Exists('$(PublishDir)logs')" />
  <MakeDir Directories="$(PublishUrl)Logs" Condition="!Exists('$(PublishUrl)Logs')" />
</Target>
```

第一个`<MakeDir>`元素，它使用`PublishDir`.NET 核心 CLI 使用属性，以确定发布操作的目标位置。 第二个`<MakeDir>`元素，它使用`PublishUrl`Visual Studio 使用属性，以确定目标位置。 Visual Studio 将使用`PublishUrl`与非.NET 核心项目的兼容性的属性。

部署目录中需要读取/Execute 权限，而*日志*目录需要读/写权限。 将在其中写入资产的附加目录需要读/写权限。
