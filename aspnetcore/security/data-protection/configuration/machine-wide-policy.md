---
title: "计算机范围的策略"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 285ae47d-e0bf-4b03-b0a8-2b1fb18bc3a1
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/configuration/machine-wide-policy
ms.openlocfilehash: fde8f75422c9dd84311a65b21e1e38b47fbe0306
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2017
---
# <a name="machine-wide-policy"></a><span data-ttu-id="99064-103">计算机范围的策略</span><span class="sxs-lookup"><span data-stu-id="99064-103">Machine Wide Policy</span></span>

<a name="data-protection-configuration-machinewidepolicy"></a>

<span data-ttu-id="99064-104">Windows 上运行时，数据保护系统具有有限的支持用于设置默认计算机范围的所有应用程序的使用数据保护的策略。</span><span class="sxs-lookup"><span data-stu-id="99064-104">When running on Windows, the data protection system has limited support for setting default machine-wide policy for all applications which consume data protection.</span></span> <span data-ttu-id="99064-105">常规的思路是管理员可能希望更改某些默认设置 （如算法或密钥的生存期） 而无需手动更新每个计算机上的应用程序。</span><span class="sxs-lookup"><span data-stu-id="99064-105">The general idea is that an administrator might wish to change some default setting (such as algorithms used or key lifetime) without needing to manually update every application on the machine.</span></span>

>[!WARNING]
> <span data-ttu-id="99064-106">系统管理员可以设置默认策略，但它们无法强制执行它。</span><span class="sxs-lookup"><span data-stu-id="99064-106">The system administrator can set default policy, but they cannot enforce it.</span></span> <span data-ttu-id="99064-107">应用程序开发人员始终可以重写其中一个自己的选择的任何值。</span><span class="sxs-lookup"><span data-stu-id="99064-107">The application developer can always override any value with one of their own choosing.</span></span> <span data-ttu-id="99064-108">默认策略仅影响应用程序开发人员没有指定显式值某些特定的设置中。</span><span class="sxs-lookup"><span data-stu-id="99064-108">The default policy only affects applications where the developer has not specified an explicit value for some particular setting.</span></span>

## <a name="setting-default-policy"></a><span data-ttu-id="99064-109">设置默认策略</span><span class="sxs-lookup"><span data-stu-id="99064-109">Setting default policy</span></span>

<span data-ttu-id="99064-110">若要设置默认策略，管理员可以在以下注册表项在系统注册表中设置已知的值。</span><span class="sxs-lookup"><span data-stu-id="99064-110">To set default policy, an administrator can set known values in the system registry under the following key.</span></span>

<span data-ttu-id="99064-111">注册表项：`HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection`</span><span class="sxs-lookup"><span data-stu-id="99064-111">Reg key: `HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection`</span></span>

<span data-ttu-id="99064-112">如果你是在 64 位操作系统上，并想要影响的 32 位应用程序的行为，请记住还将上面的项的 Wow6432Node 等效项。</span><span class="sxs-lookup"><span data-stu-id="99064-112">If you're on a 64-bit operating system and want to affect the behavior of 32-bit applications, remember also to configure the Wow6432Node equivalent of the above key.</span></span>

<span data-ttu-id="99064-113">支持的值为：</span><span class="sxs-lookup"><span data-stu-id="99064-113">The supported values are:</span></span>

* <span data-ttu-id="99064-114">EncryptionType [字符串]-指定应使用哪些算法来进行数据保护。</span><span class="sxs-lookup"><span data-stu-id="99064-114">EncryptionType [string] - specifies which algorithms should be used for data protection.</span></span> <span data-ttu-id="99064-115">此值必须为"CNG CBC"、"CNG-GCM"托管"和中更详细地介绍了[下面](#data-protection-encryption-types)。</span><span class="sxs-lookup"><span data-stu-id="99064-115">This value must be "CNG-CBC", "CNG-GCM", or "Managed" and is described in more detail [below](#data-protection-encryption-types).</span></span>

* <span data-ttu-id="99064-116">DefaultKeyLifetime [DWORD]-指定新生成的键的生存期。</span><span class="sxs-lookup"><span data-stu-id="99064-116">DefaultKeyLifetime [DWORD] - specifies the lifetime for newly-generated keys.</span></span> <span data-ttu-id="99064-117">此值以天为单位指定，并且必须 ≥ 7。</span><span class="sxs-lookup"><span data-stu-id="99064-117">This value is specified in days and must be ≥ 7.</span></span>

* <span data-ttu-id="99064-118">KeyEscrowSinks [字符串]-指定将用于密钥证书的类型。</span><span class="sxs-lookup"><span data-stu-id="99064-118">KeyEscrowSinks [string] - specifies the types which will be used for key escrow.</span></span> <span data-ttu-id="99064-119">此值为密钥托管接收器，其中在列表中的每个元素都实现 IKeyEscrowSink 的类型的程序集限定的名称的以分号分隔列表。</span><span class="sxs-lookup"><span data-stu-id="99064-119">This value is a semicolon-delimited list of key escrow sinks, where each element in the list is the assembly qualified name of a type which implements IKeyEscrowSink.</span></span>

<a name="data-protection-encryption-types"></a>

### <a name="encryption-types"></a><span data-ttu-id="99064-120">加密类型</span><span class="sxs-lookup"><span data-stu-id="99064-120">Encryption types</span></span>

<span data-ttu-id="99064-121">如果 EncryptionType 为"CNG CBC"，系统将配置为使用 CBC 模式对称的块密码来进行保密性和 HMAC 真实性有通过 Windows CNG 提供服务 (请参阅[指定自定义 Windows CNG 算法](overview.md#data-protection-changing-algorithms-cng)有关详细信息)。</span><span class="sxs-lookup"><span data-stu-id="99064-121">If EncryptionType is "CNG-CBC", the system will be configured to use a CBC-mode symmetric block cipher for confidentiality and HMAC for authenticity with services provided by Windows CNG (see [Specifying custom Windows CNG algorithms](overview.md#data-protection-changing-algorithms-cng) for more details).</span></span> <span data-ttu-id="99064-122">支持以下的其他值，其中每个对应于 CngCbcAuthenticatedEncryptionSettings 类型上的属性：</span><span class="sxs-lookup"><span data-stu-id="99064-122">The following additional values are supported, each of which corresponds to a property on the CngCbcAuthenticatedEncryptionSettings type:</span></span>

* <span data-ttu-id="99064-123">EncryptionAlgorithm [字符串]-理解的 CNG 对称的块密码算法的名称。</span><span class="sxs-lookup"><span data-stu-id="99064-123">EncryptionAlgorithm [string] - the name of a symmetric block cipher algorithm understood by CNG.</span></span> <span data-ttu-id="99064-124">此算法将在 CBC 模式下打开。</span><span class="sxs-lookup"><span data-stu-id="99064-124">This algorithm will be opened in CBC mode.</span></span>

* <span data-ttu-id="99064-125">EncryptionAlgorithmProvider [字符串]-这可能生成 EncryptionAlgorithm 的算法的 CNG 提供程序实现的名称。</span><span class="sxs-lookup"><span data-stu-id="99064-125">EncryptionAlgorithmProvider [string] - the name of the CNG provider implementation which can produce the algorithm EncryptionAlgorithm.</span></span>

* <span data-ttu-id="99064-126">EncryptionAlgorithmKeySize [DWORD]-要派生的对称块加密算法的密钥的长度 （以位为单位）。</span><span class="sxs-lookup"><span data-stu-id="99064-126">EncryptionAlgorithmKeySize [DWORD] - the length (in bits) of the key to derive for the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="99064-127">HashAlgorithm [字符串]-理解的 CNG 哈希算法的名称。</span><span class="sxs-lookup"><span data-stu-id="99064-127">HashAlgorithm [string] - the name of a hash algorithm understood by CNG.</span></span> <span data-ttu-id="99064-128">此算法将在 HMAC 模式下打开。</span><span class="sxs-lookup"><span data-stu-id="99064-128">This algorithm will be opened in HMAC mode.</span></span>

* <span data-ttu-id="99064-129">HashAlgorithmProvider [字符串]-这可能生成算法的 HashAlgorithm CNG 提供程序实现的名称。</span><span class="sxs-lookup"><span data-stu-id="99064-129">HashAlgorithmProvider [string] - the name of the CNG provider implementation which can produce the algorithm HashAlgorithm.</span></span>

<span data-ttu-id="99064-130">如果 EncryptionType 为"CNG GCM"，将配置系统要用于 Galois/计数器模式对称的块密码保密性和身份验证与服务提供的 Windows CNG (请参阅[指定自定义 Windows CNG 算法](overview.md#data-protection-changing-algorithms-cng)有关详细信息)。</span><span class="sxs-lookup"><span data-stu-id="99064-130">If EncryptionType is "CNG-GCM", the system will be configured to use a Galois/Counter Mode symmetric block cipher for confidentiality and authenticity with services provided by Windows CNG (see [Specifying custom Windows CNG algorithms](overview.md#data-protection-changing-algorithms-cng) for more details).</span></span> <span data-ttu-id="99064-131">支持以下的其他值，其中每个对应于 CngGcmAuthenticatedEncryptionSettings 类型上的属性：</span><span class="sxs-lookup"><span data-stu-id="99064-131">The following additional values are supported, each of which corresponds to a property on the CngGcmAuthenticatedEncryptionSettings type:</span></span>

* <span data-ttu-id="99064-132">EncryptionAlgorithm [字符串]-理解的 CNG 对称的块密码算法的名称。</span><span class="sxs-lookup"><span data-stu-id="99064-132">EncryptionAlgorithm [string] - the name of a symmetric block cipher algorithm understood by CNG.</span></span> <span data-ttu-id="99064-133">此算法将在 Galois/计数器模式中打开。</span><span class="sxs-lookup"><span data-stu-id="99064-133">This algorithm will be opened in Galois/Counter Mode.</span></span>

* <span data-ttu-id="99064-134">EncryptionAlgorithmProvider [字符串]-这可能生成 EncryptionAlgorithm 的算法的 CNG 提供程序实现的名称。</span><span class="sxs-lookup"><span data-stu-id="99064-134">EncryptionAlgorithmProvider [string] - the name of the CNG provider implementation which can produce the algorithm EncryptionAlgorithm.</span></span>

* <span data-ttu-id="99064-135">EncryptionAlgorithmKeySize [DWORD]-要派生的对称块加密算法的密钥的长度 （以位为单位）。</span><span class="sxs-lookup"><span data-stu-id="99064-135">EncryptionAlgorithmKeySize [DWORD] - the length (in bits) of the key to derive for the symmetric block cipher algorithm.</span></span>

<span data-ttu-id="99064-136">如果 EncryptionType"管理"，将配置系统要用于托管的 SymmetricAlgorithm 保密性和 KeyedHashAlgorithm 真实性 (请参阅[指定自定义管理算法](overview.md#data-protection-changing-algorithms-custom-managed)有关详细信息)。</span><span class="sxs-lookup"><span data-stu-id="99064-136">If EncryptionType is "Managed", the system will be configured to use a managed SymmetricAlgorithm for confidentiality and KeyedHashAlgorithm for authenticity (see [Specifying custom managed algorithms](overview.md#data-protection-changing-algorithms-custom-managed) for more details).</span></span> <span data-ttu-id="99064-137">支持以下的其他值，其中每个对应于 ManagedAuthenticatedEncryptionSettings 类型上的属性：</span><span class="sxs-lookup"><span data-stu-id="99064-137">The following additional values are supported, each of which corresponds to a property on the ManagedAuthenticatedEncryptionSettings type:</span></span>

* <span data-ttu-id="99064-138">EncryptionAlgorithmType [字符串]-实现 SymmetricAlgorithm 的类型的程序集限定名称。</span><span class="sxs-lookup"><span data-stu-id="99064-138">EncryptionAlgorithmType [string] - the assembly-qualified name of a type which implements SymmetricAlgorithm.</span></span>

* <span data-ttu-id="99064-139">EncryptionAlgorithmKeySize [DWORD]-要派生的对称加密算法的密钥的长度 （以位为单位）。</span><span class="sxs-lookup"><span data-stu-id="99064-139">EncryptionAlgorithmKeySize [DWORD] - the length (in bits) of the key to derive for the symmetric encryption algorithm.</span></span>

* <span data-ttu-id="99064-140">ValidationAlgorithmType [字符串]-实现 KeyedHashAlgorithm 的类型的程序集限定名称。</span><span class="sxs-lookup"><span data-stu-id="99064-140">ValidationAlgorithmType [string] - the assembly-qualified name of a type which implements KeyedHashAlgorithm.</span></span>

<span data-ttu-id="99064-141">如果 EncryptionType 具有任何其他值 （null / 空），则数据保护系统将在启动时引发异常。</span><span class="sxs-lookup"><span data-stu-id="99064-141">If EncryptionType has any other value (other than null / empty), the data protection system will throw an exception at startup.</span></span>

>[!WARNING]
> <span data-ttu-id="99064-142">在配置涉及类型名称 （EncryptionAlgorithmType、 ValidationAlgorithmType、 KeyEscrowSinks） 的默认策略设置时，类型必须是应用程序。</span><span class="sxs-lookup"><span data-stu-id="99064-142">When configuring a default policy setting that involves type names (EncryptionAlgorithmType, ValidationAlgorithmType, KeyEscrowSinks), the types must be available to the application.</span></span> <span data-ttu-id="99064-143">在实践中，这意味着，对于在桌面 CLR 上运行的应用程序，其中包含这些类型的程序集应 GACed。</span><span class="sxs-lookup"><span data-stu-id="99064-143">In practice, this means that for applications running on Desktop CLR, the assemblies which contain these types should be GACed.</span></span> <span data-ttu-id="99064-144">有关 ASP.NET 核心上运行的应用程序[.NET 核心](https://www.microsoft.com/net/core)，应安装包含这些类型的包。</span><span class="sxs-lookup"><span data-stu-id="99064-144">For ASP.NET Core applications running on [.NET Core](https://www.microsoft.com/net/core), the packages which contain these types should be installed.</span></span>
