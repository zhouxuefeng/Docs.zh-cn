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
ms.openlocfilehash: 60797bff85a44dd10caad4aabc109ee12dedfe61
ms.sourcegitcommit: 7efdc4b6025ad70c15c26bf7451c3c0411123794
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2017
---
# <a name="directory-structure-of-published-aspnet-core-apps"></a>已发布的 ASP.NET Core 应用的目录结构

作者：[Luke Latham](https://github.com/guardrex)

在 ASP.NET 核，应用程序目录中，*发布*，组成应用程序文件、 配置文件、 静态资产、 包和的运行时 （独立的应用）。 这是以前版本的 ASP.NET，则整个应用程序时，web 根目录中位于相同的目录结构。

| 应用类型 | 目录结构 |
| --- | --- |
| 依赖于框架的部署 | <ul><li>发布\*<ul><li>日志\*（如果包含在 publishOptions）</li><li>refs\*</li><li>运行时\*</li><li>视图\*（如果包含在 publishOptions）</li><li>wwwroot\* （如果包含在 publishOptions）</li><li>.dll 文件</li><li>myapp.deps.json</li><li>myapp.dll</li><li>myapp.pdb</li><li>myapp。PrecompiledViews.dll （如果预编译 Razor 视图）</li><li>myapp。PrecompiledViews.pdb （如果预编译 Razor 视图）</li><li>myapp.runtimeconfig.json</li><li>（如果包含在 publishOptions） 的 web.config</li></ul></li></ul> |
| 自包含的部署 | <ul><li>发布\*<ul><li>日志\*（如果包含在 publishOptions）</li><li>refs\*</li><li>视图\*（如果包含在 publishOptions）</li><li>wwwroot\* （如果包含在 publishOptions）</li><li>.dll 文件</li><li>myapp.deps.json</li><li>myapp.exe</li><li>myapp.pdb</li><li>myapp。PrecompiledViews.dll （如果预编译 Razor 视图）</li><li>myapp。PrecompiledViews.pdb （如果预编译 Razor 视图）</li><li>myapp.runtimeconfig.json</li><li>（如果包含在 publishOptions） 的 web.config</li></ul></li></ul> |
\*指示目录

内容*发布*目录表示*内容的根路径*，也称为*应用程序基路径*，部署。 任何名称提供给*发布*部署中的目录，其位置用作托管的应用程序服务器的物理路径。 *Wwwroot*目录中，如果存在，仅包含静态资产。 *日志*目录可能包含在部署项目中创建它并添加`<Target>`到下面显示的元素你*.csproj*文件或通过以物理方式上创建目录服务器。

```xml
<Target Name="CreateLogsFolder" AfterTargets="Publish">
  <MakeDir Directories="$(PublishDir)Logs" 
           Condition="!Exists('$(PublishDir)Logs')" />
  <WriteLinesToFile File="$(PublishDir)Logs\.log" 
                    Lines="Generated file" 
                    Overwrite="True" 
                    Condition="!Exists('$(PublishDir)Logs\.log')" />
</Target>
```

`<MakeDir>`元素创建一个空*日志*中已发布的输出文件夹。 元素使用`PublishDir`属性来确定用于创建文件夹的目标位置。 多种部署方法，例如 Web 部署，在部署过程中跳过空文件夹。 `<WriteLinesToFile>`元素生成的文件中*日志*文件夹中，这可确保部署到服务器的文件夹。 请注意，如果工作进程不具有写访问权限的目标文件夹的文件夹创建可能仍会失败。

部署目录中需要读取/Execute 权限，而*日志*目录需要读/写权限。 将在其中写入资产的附加目录需要读/写权限。
