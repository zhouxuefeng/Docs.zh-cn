---
title: "ASP.NET Core 中的数据保护"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 1f402da8-1052-4970-9835-9f9f16a02dbc
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/index
ms.openlocfilehash: 39f2ca96f8542de033274ea957b5c7736948c981
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="data-protection-in-aspnet-core-consumer-apis-configuration-extensibility-apis-and-implementation"></a>ASP.NET Core 中的数据保护：使用者 API、配置、扩展性 API 和实现

* [数据保护简介](introduction.md)

* [数据保护 API 入门](using-data-protection.md)

* [使用者 API](consumer-apis/index.md)

  * [使用者 API 概述](consumer-apis/overview.md)

  * [目标字符串](consumer-apis/purpose-strings.md)

  * [目标层次结构和多租户](consumer-apis/purpose-strings-multitenancy.md)

  * [密码哈希](consumer-apis/password-hashing.md)

  * [限制受保护负载的生存期](consumer-apis/limited-lifetime-payloads.md)

  * [取消保护已撤消其密钥的负载](consumer-apis/dangerous-unprotect.md)

* [配置](configuration/index.md)

  * [配置数据保护](configuration/overview.md)

  * [默认设置](configuration/default-settings.md)

  * [计算机范围的策略](configuration/machine-wide-policy.md)

  * [非 DI 感知方案](configuration/non-di-scenarios.md)

* [扩展性 API](extensibility/index.md)

  * [Core 加密扩展性](extensibility/core-crypto.md)

  * [密钥管理扩展性](extensibility/key-management.md)

  * [其他 API](extensibility/misc-apis.md)

* [实现](implementation/index.md)

  * [已验证的加密详细信息。](implementation/authenticated-encryption-details.md)

  * [子项派生和已验证的加密](implementation/subkeyderivation.md)

  * [上下文标头](implementation/context-headers.md)

  * [密钥管理](implementation/key-management.md)

  * [密钥存储提供程序](implementation/key-storage-providers.md)

  * [静态密钥加密](implementation/key-encryption-at-rest.md)

  * [密钥永久性和更改设置](implementation/key-immutability.md)

  * [密钥存储格式](implementation/key-storage-format.md)

  * [短数据保护提供程序](implementation/key-storage-ephemeral.md)

* [兼容性](compatibility/index.md)

  * [在应用程序之间共享 cookie](compatibility/cookie-sharing.md)

  * [在 ASP.NET 中替换 <machineKey>](compatibility/replacing-machinekey.md)
