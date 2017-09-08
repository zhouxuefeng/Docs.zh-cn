---
title: "密钥加密对静止"
author: rick-anderson
description: 
keywords: "ASP.NET 核心"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: f2bbbf4e-0945-43ce-be59-8bf19e448798
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/implementation/key-encryption-at-rest
ms.openlocfilehash: cef7644d29168e9560d1175885ea85a525fec435
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="key-encryption-at-rest"></a><span data-ttu-id="27404-103">密钥加密对静止</span><span class="sxs-lookup"><span data-stu-id="27404-103">Key Encryption At Rest</span></span>

<a name=data-protection-implementation-key-encryption-at-rest></a>

<span data-ttu-id="27404-104">默认情况下，数据保护系统[使用启发式方法](../configuration/default-settings.md#data-protection-default-settings)来确定如何加密的密钥材料应加密对静止。</span><span class="sxs-lookup"><span data-stu-id="27404-104">By default the data protection system [employs a heuristic](../configuration/default-settings.md#data-protection-default-settings) to determine how cryptographic key material should be encrypted at rest.</span></span> <span data-ttu-id="27404-105">开发人员可以重写启发式方法，并手动指定应如何静态加密密钥。</span><span class="sxs-lookup"><span data-stu-id="27404-105">The developer can override the heuristic and manually specify how keys should be encrypted at rest.</span></span>

> [!NOTE]
> <span data-ttu-id="27404-106">如果指定在 rest 机制显式密钥加密时，数据保护系统将取消注册启发式方法提供的默认密钥存储机制。</span><span class="sxs-lookup"><span data-stu-id="27404-106">If you specify an explicit key encryption at rest mechanism, the data protection system will deregister the default key storage mechanism that the heuristic provided.</span></span> <span data-ttu-id="27404-107">你必须[指定显式的密钥存储机制](key-storage-providers.md#data-protection-implementation-key-storage-providers)，否则数据保护系统将无法启动。</span><span class="sxs-lookup"><span data-stu-id="27404-107">You must [specify an explicit key storage mechanism](key-storage-providers.md#data-protection-implementation-key-storage-providers), otherwise the data protection system will fail to start.</span></span>

<a name=data-protection-implementation-key-encryption-at-rest-providers></a>

<span data-ttu-id="27404-108">数据保护系统都附带有三个框中的密钥加密机制。</span><span class="sxs-lookup"><span data-stu-id="27404-108">The data protection system ships with three in-box key encryption mechanisms.</span></span>

## <a name="windows-dpapi"></a><span data-ttu-id="27404-109">Windows DPAPI</span><span class="sxs-lookup"><span data-stu-id="27404-109">Windows DPAPI</span></span>

<span data-ttu-id="27404-110">*仅在 Windows 上，此机制是可用。*</span><span class="sxs-lookup"><span data-stu-id="27404-110">*This mechanism is available only on Windows.*</span></span>

<span data-ttu-id="27404-111">当使用 Windows DPAPI 时，将通过加密密钥材料[CryptProtectData](https://msdn.microsoft.com/library/windows/desktop/aa380261(v=vs.85).aspx)之前保存到存储。</span><span class="sxs-lookup"><span data-stu-id="27404-111">When Windows DPAPI is used, key material will be encrypted via [CryptProtectData](https://msdn.microsoft.com/library/windows/desktop/aa380261(v=vs.85).aspx) before being persisted to storage.</span></span> <span data-ttu-id="27404-112">DPAPI 是一种用于将永远不会读取当前计算机之外的数据的合适的加密机制 (尽管它可以备份到 Active Directory 这些密钥，请参阅[DPAPI 和漫游配置文件](https://support.microsoft.com/kb/309408/#6))。</span><span class="sxs-lookup"><span data-stu-id="27404-112">DPAPI is an appropriate encryption mechanism for data that will never be read outside of the current machine (though it is possible to back these keys up to Active Directory; see [DPAPI and Roaming Profiles](https://support.microsoft.com/kb/309408/#6)).</span></span> <span data-ttu-id="27404-113">例如，若要配置 DPAPI 密钥在 rest 加密。</span><span class="sxs-lookup"><span data-stu-id="27404-113">For example to configure DPAPI key-at-rest encryption.</span></span>

```csharp
sc.AddDataProtection()
       // only the local user account can decrypt the keys
       .ProtectKeysWithDpapi();
   ```

<span data-ttu-id="27404-114">如果不带任何参数调用 ProtectKeysWithDpapi，则只有当前 Windows 用户帐户才能解密持久化的密钥材料。</span><span class="sxs-lookup"><span data-stu-id="27404-114">If ProtectKeysWithDpapi is called with no parameters, only the current Windows user account can decipher the persisted key material.</span></span> <span data-ttu-id="27404-115">你可以选择指定中所示，应导致计算机 （而不仅仅是当前用户帐户） 上的任何用户帐户能够解密的密钥材料，下面的示例。</span><span class="sxs-lookup"><span data-stu-id="27404-115">You can optionally specify that any user account on the machine (not just the current user account) should be able to decipher the key material, as shown in the below example.</span></span>

```csharp
sc.AddDataProtection()
       // all user accounts on the machine can decrypt the keys
       .ProtectKeysWithDpapi(protectToLocalMachine: true);
   ```

## <a name="x509-certificate"></a><span data-ttu-id="27404-116">X.509 证书</span><span class="sxs-lookup"><span data-stu-id="27404-116">X.509 certificate</span></span>

<span data-ttu-id="27404-117">*此机制不可用上`.NET Core 1.0`或`1.1`。*</span><span class="sxs-lookup"><span data-stu-id="27404-117">*This mechanism is not available on `.NET Core 1.0` or `1.1`.*</span></span>

<span data-ttu-id="27404-118">如果你的应用程序分布在多台计算机，则可能很方便多台计算机之间分发共享的 X.509 证书并配置应用程序，此证书用于加密存放的密钥。</span><span class="sxs-lookup"><span data-stu-id="27404-118">If your application is spread across multiple machines, it may be convenient to distribute a shared X.509 certificate across the machines and to configure applications to use this certificate for encryption of keys at rest.</span></span> <span data-ttu-id="27404-119">有关示例，请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="27404-119">See below for an example.</span></span>

```csharp
sc.AddDataProtection()
       // searches the cert store for the cert with this thumbprint
       .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
   ```

<span data-ttu-id="27404-120">由于.NET Framework 限制支持仅使用 CAPI 私钥的证书。</span><span class="sxs-lookup"><span data-stu-id="27404-120">Due to .NET Framework limitations only certificates with CAPI private keys are supported.</span></span> <span data-ttu-id="27404-121">请参阅[基于证书的加密使用 Windows DPAPI NG](#data-protection-implementation-key-encryption-at-rest-dpapi-ng)下面这些限制的可能解决方法。</span><span class="sxs-lookup"><span data-stu-id="27404-121">See [Certificate-based encryption with Windows DPAPI-NG](#data-protection-implementation-key-encryption-at-rest-dpapi-ng) below for possible workarounds to these limitations.</span></span>

<a name=data-protection-implementation-key-encryption-at-rest-dpapi-ng></a>

## <a name="windows-dpapi-ng"></a><span data-ttu-id="27404-122">Windows DPAPI NG</span><span class="sxs-lookup"><span data-stu-id="27404-122">Windows DPAPI-NG</span></span>

<span data-ttu-id="27404-123">*此机制是仅适用于 Windows 8 / Windows Server 2012 和更高版本。*</span><span class="sxs-lookup"><span data-stu-id="27404-123">*This mechanism is available only on Windows 8 / Windows Server 2012 and later.*</span></span>

<span data-ttu-id="27404-124">操作系统从 Windows 8 开始，支持 DPAPI NG （也称为 CNG DPAPI）。</span><span class="sxs-lookup"><span data-stu-id="27404-124">Beginning with Windows 8, the operating system supports DPAPI-NG (also called CNG DPAPI).</span></span> <span data-ttu-id="27404-125">Microsoft 是布局其使用方案，如下所示。</span><span class="sxs-lookup"><span data-stu-id="27404-125">Microsoft lays out its usage scenario as follows.</span></span>

   <span data-ttu-id="27404-126">但是，云计算，通常需要该内容的加密在一台计算机进行在另一台解密。</span><span class="sxs-lookup"><span data-stu-id="27404-126">Cloud computing, however, often requires that content encrypted on one computer be decrypted on another.</span></span> <span data-ttu-id="27404-127">因此，从开始 Windows 8，Microsoft 扩展的使用相对比较简单的 API 以覆盖云方案的想法。</span><span class="sxs-lookup"><span data-stu-id="27404-127">Therefore, beginning with Windows 8, Microsoft extended the idea of using a relatively straightforward API to encompass cloud scenarios.</span></span> <span data-ttu-id="27404-128">称为 DPAPI NG，此新 API，可安全地通过保护它们的一组可用于取消这些不同的计算机上保护后正确的身份验证和授权的主体到共享机密 （密钥、 密码、 密钥材料） 和消息。</span><span class="sxs-lookup"><span data-stu-id="27404-128">This new API, called DPAPI-NG, enables you to securely share secrets (keys, passwords, key material) and messages by protecting them to a set of principals that can be used to unprotect them on different computers after proper authentication and authorization.</span></span>

   <span data-ttu-id="27404-129">从[https://msdn.microsoft.com/library/windows/desktop/hh706794 (v=vs.85).aspx](https://msdn.microsoft.com/library/windows/desktop/hh706794(v=vs.85).aspx)</span><span class="sxs-lookup"><span data-stu-id="27404-129">From [https://msdn.microsoft.com/library/windows/desktop/hh706794(v=vs.85).aspx](https://msdn.microsoft.com/library/windows/desktop/hh706794(v=vs.85).aspx)</span></span>

<span data-ttu-id="27404-130">主体编码为保护描述符规则。</span><span class="sxs-lookup"><span data-stu-id="27404-130">The principal is encoded as a protection descriptor rule.</span></span> <span data-ttu-id="27404-131">请考虑以下示例中，该加密密钥材料，以便仅已加入域的用户具有指定 SID 都可以解密密钥材料。</span><span class="sxs-lookup"><span data-stu-id="27404-131">Consider the below example, which encrypts key material such that only the domain-joined user with the specified SID can decrypt the key material.</span></span>

```csharp
sc.AddDataProtection()
     // uses the descriptor rule "SID=S-1-5-21-..."
     .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
       flags: DpapiNGProtectionDescriptorFlags.None);
   ```

<span data-ttu-id="27404-132">此外，还存在 ProtectKeysWithDpapiNG 无参数重载。</span><span class="sxs-lookup"><span data-stu-id="27404-132">There is also a parameterless overload of ProtectKeysWithDpapiNG.</span></span> <span data-ttu-id="27404-133">这是用于指定规则的便捷方法"SID = 挖掘"，其中挖掘是当前 Windows 用户帐户的 SID。</span><span class="sxs-lookup"><span data-stu-id="27404-133">This is a convenience method for specifying the rule "SID=mine", where mine is the SID of the current Windows user account.</span></span>

```csharp
sc.AddDataProtection()
     // uses the descriptor rule "SID={current account SID}"
     .ProtectKeysWithDpapiNG();
   ```

<span data-ttu-id="27404-134">在此方案中，AD 域控制器负责分发 DPAPI NG 操作所使用的加密密钥。</span><span class="sxs-lookup"><span data-stu-id="27404-134">In this scenario, the AD domain controller is responsible for distributing the encryption keys used by the DPAPI-NG operations.</span></span> <span data-ttu-id="27404-135">目标用户将能够解密从任何加入域的计算机的加密的负载 （前提是进程正在其标识下运行时）。</span><span class="sxs-lookup"><span data-stu-id="27404-135">The target user will be able to decipher the encrypted payload from any domain-joined machine (provided that the process is running under their identity).</span></span>

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a><span data-ttu-id="27404-136">基于证书的加密使用 Windows DPAPI NG</span><span class="sxs-lookup"><span data-stu-id="27404-136">Certificate-based encryption with Windows DPAPI-NG</span></span>

<span data-ttu-id="27404-137">如果在 Windows 8.1 上运行 / Windows Server 2012 R2 或更高版本，你可以使用 Windows DPAPI NG 执行基于证书的加密，即使在上运行应用程序[.NET 核心](https://microsoft.com/net/core)。</span><span class="sxs-lookup"><span data-stu-id="27404-137">If you're running on Windows 8.1 / Windows Server 2012 R2 or later, you can use Windows DPAPI-NG to perform certificate-based encryption, even if the application is running on [.NET Core](https://microsoft.com/net/core).</span></span> <span data-ttu-id="27404-138">若要充分利用此功能，使用规则描述符字符串"证书 = HashId:thumbprint"，其中指纹是要使用的证书的十六进制编码 SHA1 指纹。</span><span class="sxs-lookup"><span data-stu-id="27404-138">To take advantage of this, use the rule descriptor string "CERTIFICATE=HashId:thumbprint", where thumbprint is the hex-encoded SHA1 thumbprint of the certificate to use.</span></span> <span data-ttu-id="27404-139">有关示例，请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="27404-139">See below for an example.</span></span>

```csharp
sc.AddDataProtection()
       // searches the cert store for the cert with this thumbprint
       .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0",
           flags: DpapiNGProtectionDescriptorFlags.None);
   ```

<span data-ttu-id="27404-140">在此存储库指向任何应用程序必须在 Windows 8.1 上运行 / Windows Server 2012 R2 或更高版本能够解密此密钥。</span><span class="sxs-lookup"><span data-stu-id="27404-140">Any application which is pointed at this repository must be running on Windows 8.1 / Windows Server 2012 R2 or later to be able to decipher this key.</span></span>

## <a name="custom-key-encryption"></a><span data-ttu-id="27404-141">自定义密钥加密</span><span class="sxs-lookup"><span data-stu-id="27404-141">Custom key encryption</span></span>

<span data-ttu-id="27404-142">如果不适合的内置机制，开发人员可以通过提供自定义 IXmlEncryptor 指定自己的密钥加密机制。</span><span class="sxs-lookup"><span data-stu-id="27404-142">If the in-box mechanisms are not appropriate, the developer can specify their own key encryption mechanism by providing a custom IXmlEncryptor.</span></span>
