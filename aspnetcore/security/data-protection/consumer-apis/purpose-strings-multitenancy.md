---
title: "在 ASP.NET 核心目的字符串"
author: rick-anderson
description: 
keywords: "ASP.NET 核心"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 9d18c287-e0e6-4541-b09c-7fed45c902d9
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/consumer-apis/purpose-strings-multitenancy
ms.openlocfilehash: dd87d8bcaf0056b322908e9a3ef75678f603e1e6
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="purpose-hierarchy-and-multi-tenancy-in-aspnet-core"></a>目的层次结构和 ASP.NET Core 中的多租户

由于 IDataProtector 还隐式 IDataProtectionProvider，目的可以链接在一起。 在此意义上提供程序。CreateProtector （["purpose1"，"purpose2"]） 相当于提供程序。CreateProtector("purpose1")。CreateProtector("purpose2")。

这允许通过数据保护系统的一些有趣的层次结构关系。 前面的示例中[Contoso.Messaging.SecureMessage](purpose-strings.md#data-protection-contoso-purpose)，SecureMessage 组件可以调用提供程序。CreateProtector("Contoso.Messaging.SecureMessage") 后前部和缓存到专用 _myProvider 字段的结果。 然后可以通过调用 _myProvider.CreateProtector 创建将来的保护程序 ("用户： 用户名")，并且这些保护程序会使用用于保护单个消息。

这可以还翻转。 考虑单个逻辑应用程序可以使用其自己的身份验证和状态管理系统配置多个租户 （CMS 看起来合理） 和每个租户的主机。 涵盖应用程序具有单一的主提供程序，并且它会调用提供程序。CreateProtector ("Tenant 1") 和提供程序。CreateProtector （"租户 2"） 为每个租户提供其自己的数据保护系统的独立的切片。 租户然后无法派生自己单独的保护程序，基于其自己的需求，但无论硬他们将在尝试在不能创建其中发生冲突的保护程序与其他任何租户系统中。 这以图形方式表示如下所示。

![多租户目的](purpose-strings-multitenancy/_static/purposes-multi-tenancy.png)

>[!WARNING]
> 这假定涵盖应用程序的控制哪些 Api 可供各个租户和租户不能在服务器上执行任意代码。 如果租户可以执行任意代码，用户无法执行私有反射来中断隔离保证，或它们只是无法直接读取主密钥材料和派生任何子项他们所需。

数据保护系统实际上使用一种在其默认现成可用配置中的多租户。 默认情况下主密钥材料存储在辅助进程帐户的用户配置文件文件夹中 （或注册表中的，为 IIS 应用程序池标识）。 但它实际上相当通常使用单个帐户来运行多个应用程序，并且因此这些应用程序将最终共享主密钥材料。 若要解决此问题，数据保护系统自动将唯一的按应用程序标识符作为整体的目的链中的第一个元素。 此隐式目的提供给[将单个应用程序隔离](../configuration/overview.md#data-protection-configuration-per-app-isolation)从另一个通过有效地将每个应用程序，像在系统和保护程序创建过程中的唯一租户上去与上面的图像相同。
