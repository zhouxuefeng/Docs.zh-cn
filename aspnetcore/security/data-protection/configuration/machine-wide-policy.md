---
title: "计算机范围的策略"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 285ae47d-e0bf-4b03-b0a8-2b1fb18bc3a1
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/configuration/machine-wide-policy
ms.openlocfilehash: 7ada940acfbb7fb0887fd7c0cd722bf62f211248
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="machine-wide-policy"></a>计算机范围的策略

<a name=data-protection-configuration-machinewidepolicy></a>

Windows 上运行时，数据保护系统具有有限的支持用于设置默认计算机范围的所有应用程序的使用数据保护的策略。 常规的思路是管理员可能希望更改某些默认设置 （如算法或密钥的生存期） 而无需手动更新每个计算机上的应用程序。

>[!WARNING]
> 系统管理员可以设置默认策略，但它们无法强制执行它。 应用程序开发人员始终可以重写其中一个自己的选择的任何值。 默认策略仅影响应用程序开发人员没有指定显式值某些特定的设置中。

## <a name="setting-default-policy"></a>设置默认策略

若要设置默认策略，管理员可以在以下注册表项在系统注册表中设置已知的值。

注册表项：`HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection`

如果你是在 64 位操作系统上，并想要影响的 32 位应用程序的行为，请记住还将上面的项的 Wow6432Node 等效项。

支持的值为：

* EncryptionType [字符串]-指定应使用哪些算法来进行数据保护。 此值必须为"CNG CBC"、"CNG-GCM"托管"和中更详细地介绍了[下面](#data-protection-encryption-types)。

* DefaultKeyLifetime [DWORD]-指定新生成的键的生存期。 此值以天为单位指定，并且必须 ≥ 7。

* KeyEscrowSinks [字符串]-指定将用于密钥证书的类型。 此值为密钥托管接收器，其中在列表中的每个元素都实现 IKeyEscrowSink 的类型的程序集限定的名称的以分号分隔列表。

<a name=data-protection-encryption-types></a>

### <a name="encryption-types"></a>加密类型

如果 EncryptionType 为"CNG CBC"，系统将配置为使用 CBC 模式对称的块密码来进行保密性和 HMAC 真实性有通过 Windows CNG 提供服务 (请参阅[指定自定义 Windows CNG 算法](overview.md#data-protection-changing-algorithms-cng)有关详细信息)。 支持以下的其他值，其中每个对应于 CngCbcAuthenticatedEncryptionSettings 类型上的属性：

* EncryptionAlgorithm [字符串]-理解的 CNG 对称的块密码算法的名称。 此算法将在 CBC 模式下打开。

* EncryptionAlgorithmProvider [字符串]-这可能生成 EncryptionAlgorithm 的算法的 CNG 提供程序实现的名称。

* EncryptionAlgorithmKeySize [DWORD]-要派生的对称块加密算法的密钥的长度 （以位为单位）。

* HashAlgorithm [字符串]-理解的 CNG 哈希算法的名称。 此算法将在 HMAC 模式下打开。

* HashAlgorithmProvider [字符串]-这可能生成算法的 HashAlgorithm CNG 提供程序实现的名称。

如果 EncryptionType 为"CNG GCM"，将配置系统要用于 Galois/计数器模式对称的块密码保密性和身份验证与服务提供的 Windows CNG (请参阅[指定自定义 Windows CNG 算法](overview.md#data-protection-changing-algorithms-cng)有关详细信息)。 支持以下的其他值，其中每个对应于 CngGcmAuthenticatedEncryptionSettings 类型上的属性：

* EncryptionAlgorithm [字符串]-理解的 CNG 对称的块密码算法的名称。 此算法将在 Galois/计数器模式中打开。

* EncryptionAlgorithmProvider [字符串]-这可能生成 EncryptionAlgorithm 的算法的 CNG 提供程序实现的名称。

* EncryptionAlgorithmKeySize [DWORD]-要派生的对称块加密算法的密钥的长度 （以位为单位）。

如果 EncryptionType"管理"，将配置系统要用于托管的 SymmetricAlgorithm 保密性和 KeyedHashAlgorithm 真实性 (请参阅[指定自定义管理算法](overview.md#data-protection-changing-algorithms-custom-managed)有关详细信息)。 支持以下的其他值，其中每个对应于 ManagedAuthenticatedEncryptionSettings 类型上的属性：

* EncryptionAlgorithmType [字符串]-实现 SymmetricAlgorithm 的类型的程序集限定名称。

* EncryptionAlgorithmKeySize [DWORD]-要派生的对称加密算法的密钥的长度 （以位为单位）。

* ValidationAlgorithmType [字符串]-实现 KeyedHashAlgorithm 的类型的程序集限定名称。

如果 EncryptionType 具有任何其他值 （null / 空），则数据保护系统将在启动时引发异常。

>[!WARNING]
> 在配置涉及类型名称 （EncryptionAlgorithmType、 ValidationAlgorithmType、 KeyEscrowSinks） 的默认策略设置时，类型必须是应用程序。 在实践中，这意味着，对于在桌面 CLR 上运行的应用程序，其中包含这些类型的程序集应 GACed。 有关 ASP.NET 核心上运行的应用程序[.NET 核心](https://www.microsoft.com/net/core)，应安装包含这些类型的包。
