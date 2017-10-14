---
title: "密钥管理和生存期"
author: rick-anderson
description: "描述密钥管理和生存期。"
keywords: "ASP.NET 核心，密钥管理，DPAPI，数据保护"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: ef7dad2a-7029-4ae5-8f06-1fbebedccaa4
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/configuration/default-settings
ms.openlocfilehash: 5ac2d80e7d1cebcbc792e1247608e67991ec36f4
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2017
---
# <a name="key-management-and-lifetime"></a>密钥管理和生存期

<a name="data-protection-default-settings"></a>

## <a name="key-management"></a>密钥管理

系统将尝试检测其操作环境并提供良好的零配置行为默认值。 使用启发式方法是，如下所示。

1. 如果系统正在承载在 Azure 网站，密钥会保留到"%home%\asp.net\dataprotection-keys"文件夹中。 此文件夹由网络存储提供支持，并且在托管应用程序的所有计算机同步。 在 rest 不受保护密钥。 此文件夹提供密钥环到单个部署槽位中的应用程序的所有实例。 单独的部署槽，如过渡和生产时，不会共享密钥链。 更换之间部署槽，如交换过渡到生产环境或使用 A / B 测试，使用数据保护任何系统不能在前一槽内使用密钥链存储的数据进行解密。 这将导致用户正在注销的 ASP.NET 应用程序使用标准的 ASP.NET cookie 中间件，因为它使用数据保护来保护其 cookie。 如果你需要独立于槽的密钥环，使用 Azure Blob 存储，Azure 密钥保管库，SQL 存储区中，之类的外部密钥环提供程序，或 Redis 缓存。

2. 如果用户配置文件可用，密钥会保留到"%localappdata%\asp.net\dataprotection-keys"文件夹中。 此外，如果操作系统是 Windows，它们将在 rest 使用 DPAPI 加密。

3. 如果在 IIS 中托管的应用程序，密钥会保留到 HKLM 注册表仅对辅助进程帐户是 ACLed 某个特殊的注册表项中。 使用 DPAPI 对密钥静态加密。

4. 如果没有这些条件与之匹配，则密钥不会保留在当前进程之外。 关闭进程，所有生成密钥将会丢失。

开发人员可始终完全控制，并且可重写如何和密钥的存储位置。 上面的前三个选项应很好的默认值对于大多数应用程序类似于如何 ASP.NET<machineKey>过去可以正常运行自动生成例程。 最终，故障回复选项是真正需要开发人员指定的唯一情形[配置](overview.md)前部如果他们想要密钥持久性，但此回退才会出现在极少数情况下。

>[!WARNING]
> 如果开发人员重写此启发式方法，并指向特定的密钥存储库的数据保护系统，则将禁用自动加密对静止的密钥。 在 rest 保护可能会通过重新启用[配置](overview.md)。

## <a name="key-lifetime"></a>密钥的生存期

默认情况下的密钥具有 90 天的生存期。 密钥过期时，系统将自动生成新密钥，并将新的密钥设置为活动密钥。 只要已停用的密钥保留在系统上仍将能够解密使用它们保护的任何数据。 请参阅[密钥管理](../implementation/key-management.md#data-protection-implementation-key-management-expiration)有关详细信息。

## <a name="default-algorithms"></a>默认的算法

使用的默认负载保护算法是 AES 256 CBC 机密性和 HMACSHA256 真实性。 512 位主密钥，回滚每隔 90 天，用于派生用于每个负载基于这些算法的两个子键。 请参阅[子项派生](../implementation/subkeyderivation.md#data-protection-implementation-subkey-derivation-aad)有关详细信息。
