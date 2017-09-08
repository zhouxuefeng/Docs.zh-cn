---
title: "非 DI 感知的情境"
author: rick-anderson
description: 
keywords: "ASP.NET 核心"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: a7d8a962-80ff-48e3-96f6-8472b7ba2df9
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/configuration/non-di-scenarios
ms.openlocfilehash: 54a930c26f9f48ea0e6f7865e2927bcde0f4d6c0
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="non-di-aware-scenarios"></a>非 DI 感知的情境

数据保护系统通常设计[要添加到服务容器](../consumer-apis/overview.md)和为通过 DI 机制的从属组件提供。 但是，可能有某些情况下，其中这不可行，尤其是在导入现有的应用程序的系统。

为了支持这些方案包 Microsoft.AspNetCore.DataProtection.Extensions 提供具体类型 DataProtectionProvider 它提供一种简单的方法，而无需通过 DI 特定代码路径中使用数据保护系统。 该类型本身实现 IDataProtectionProvider，并构造它十分轻松，提供 DirectoryInfo 应在其中存储此提供程序的加密密钥。

例如: 

[!code-none[Main](non-di-scenarios/_static/nodisample1.cs)]

>[!WARNING]
> 默认情况下 DataProtectionProvider 具体的类型不加密原始密钥材料之前将其保存到文件系统。 这是为了支持的方案： 开发人员指向网络共享，在这种情况下数据保护系统无法自动推导出相应的在 rest 密钥加密机制。
>
>此外，DataProtectionProvider 具体类型却不[隔离应用程序](overview.md#data-protection-configuration-per-app-isolation)默认情况下，因此所有应用程序指向相同的密钥目录可以共享的负载，只要其用途的参数匹配。

如果需要，应用程序开发人员可以满足这两种。 DataProtectionProvider 构造函数将接受[可选配置回调](overview.md#data-protection-configuration-callback)可用于调整的系统行为。 下面的示例演示通过显式调用 SetApplicationName，还原隔离，并且它还演示将系统配置为自动加密持久化的密钥使用 Windows DPAPI。 如果目录指向 UNC 共享，你可能想要在所有相关计算机间分发共享的证书还可将系统配置为通过调用改为使用基于证书的加密[ProtectKeysWithCertificate](overview.md#configuring-x509-certificate)。

[!code-none[Main](non-di-scenarios/_static/nodisample2.cs)]

>[!TIP]
> DataProtectionProvider 具体类型的实例都创建开销很大。 如果应用程序将保留此类型的多个实例，并且它们正在所有指向相同的密钥存储目录，则可能会降低应用程序性能。 预期的用法是应用程序开发人员，一次实例化此类型，则保留重用此单个引用尽可能多地。 DataProtectionProvider 类型和从其创建的所有 IDataProtector 实例都是线程安全的多个调用方。
