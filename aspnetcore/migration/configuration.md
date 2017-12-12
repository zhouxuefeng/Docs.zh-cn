---
title: "迁移配置"
author: ardalis
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 8468d859-ff32-4a92-9e62-08c4a9e36594
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/configuration
ms.openlocfilehash: d20235feec9d66c371b8ce0b7c66fb424fb261d5
ms.sourcegitcommit: 8f42ab93402c1b8044815e1e48d0bb84c81f8b59
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2017
---
# <a name="migrating-configuration"></a>迁移配置

通过[Steve Smith](https://ardalis.com/)和[Scott Addie](https://scottaddie.com)

在以前的文章中，我们就已着手[将 ASP.NET MVC 项目迁移到 ASP.NET 核心 MVC](mvc.md)。 在本文中，我们将迁移配置。

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/migration/configuration/samples)（[如何下载](xref:tutorials/index#how-to-download-a-sample)）

## <a name="setup-configuration"></a>安装程序配置

ASP.NET 核心不再使用*Global.asax*和*web.config* ASP.NET 的早期版本使用的文件。 在 ASP.NET 的早期版本中，应用程序的启动逻辑放入`Application_StartUp`方法内的*Global.asax*。 更高版本，在 ASP.NET MVC *Startup.cs*项目; 的根目录中包含文件和应用程序启动时调用它。 ASP.NET 核心已完全采用这种方法，通过将中的所有启动逻辑*Startup.cs*文件。

*Web.config*还在 ASP.NET Core 替换文件。 配置本身现在可以配置，作为应用程序启动过程中所述的一部分*Startup.cs*。 配置仍然可以利用 XML 文件，但通常 ASP.NET 核心项目将置于配置值的 JSON 格式文件，如*appsettings.json*。 ASP.NET 核心配置系统可以方便地访问环境变量，可以提供特定于环境的值的更安全且更可靠位置。 这是针对如连接字符串和不应签入源代码管理的 API 密钥的机密尤其如此。 请参阅[配置](xref:fundamentals/configuration/index)若要了解有关 ASP.NET 核心中配置的详细信息。

有关本文中，我们开始使用中的部分迁移 ASP.NET Core 项目[上一篇文章](mvc.md)。 要设置配置中添加以下构造函数和属性*Startup.cs*文件位于项目根目录中：

[!code-csharp[Main](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-21)]

请注意，此时， *Startup.cs*文件将不进行编译，因为我们仍需要将以下内容添加`using`语句：

```csharp
using Microsoft.Extensions.Configuration;
```

添加*appsettings.json*使用合适的项目模板项目的根目录的文件：

![添加 AppSettings JSON](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a>将配置设置迁移从 web.config

我们的 ASP.NET MVC 项目包含中的所需的数据库连接字符串*web.config*中`<connectionStrings>`元素。 在我们 ASP.NET Core 项目中，我们将存储此信息在*appsettings.json*文件。 打开*appsettings.json*，并记下它已包含以下：

[!code-json[Main](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]


在突出显示的行将上面所示，将更改从数据库的名称**_CHANGE_ME**为你的数据库的名称。

## <a name="summary"></a>摘要

ASP.NET 核心将置于单个文件，在其中的必要的服务和依赖项可以定义和配置应用程序的所有启动逻辑。 它将替换*web.config*与一种灵活的配置功能，可以利用多种文件格式，例如 JSON，以及环境变量的文件。
