---
title: "配置数据保护"
author: rick-anderson
description: 
keywords: "ASP.NET 核心"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 0e4881a3-a94d-4e35-9c1c-f025d65dcff0
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/configuration/overview
ms.openlocfilehash: 39fab796c24456d61a6a103c4a3f7a8722b4718c
ms.sourcegitcommit: bd05f7ea8f87ad076ef6e8b704698ebcba5ca80c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2017
---
# <a name="configuring-data-protection"></a><span data-ttu-id="1fdfe-103">配置数据保护</span><span class="sxs-lookup"><span data-stu-id="1fdfe-103">Configuring data protection</span></span>

<a name=data-protection-configuring></a>

<span data-ttu-id="1fdfe-104">初始化数据保护系统时它将部分[默认设置](default-settings.md#data-protection-default-settings)基于的操作环境。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-104">When the data protection system is initialized it applies some [default settings](default-settings.md#data-protection-default-settings) based on the operational environment.</span></span> <span data-ttu-id="1fdfe-105">这些设置是在一台计算机上运行的应用程序通常完好的。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-105">These settings are generally good for applications running on a single machine.</span></span> <span data-ttu-id="1fdfe-106">在某些情况的下，开发人员可能想要更改它们 (可能是因为他们的应用程序分布在多个计算机之间或者出于合规原因)，并为这些方案数据保护系统提供的丰富的配置 API。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-106">There are some cases where a developer may want to change these (perhaps because their application is spread across multiple machines or for compliance reasons), and for these scenarios the data protection system offers a rich configuration API.</span></span>

<a name=data-protection-configuration-callback></a>

<span data-ttu-id="1fdfe-107">没有扩展方法 AddDataProtection 它返回 IDataProtectionBuilder 其本身公开，你可以链接在一起以配置各种数据保护选项的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-107">There is an extension method AddDataProtection which returns an IDataProtectionBuilder which itself exposes extension methods that you can chain together to configure various data protection options.</span></span> <span data-ttu-id="1fdfe-108">例如，若要存储在 UNC 共享而不是 %LOCALAPPDATA%（默认值） 的密钥，将系统配置，如下所示：</span><span class="sxs-lookup"><span data-stu-id="1fdfe-108">For instance, to store keys at a UNC share instead of %LOCALAPPDATA% (the default), configure the system as follows:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

>[!WARNING]
> <span data-ttu-id="1fdfe-109">如果更改密钥持久性位置时，系统将不再自动会加密存放的密钥，因为它不知道 DPAPI 是否合适的加密机制。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-109">If you change the key persistence location, the system will no longer automatically encrypt keys at rest since it doesn't know whether DPAPI is an appropriate encryption mechanism.</span></span>

<a name=configuring-x509-certificate></a>

<span data-ttu-id="1fdfe-110">你可以将系统配置为通过调用任何 ProtectKeysWith 保护静止的密钥\*配置 Api。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-110">You can configure the system to protect keys at rest by calling any of the ProtectKeysWith\* configuration APIs.</span></span> <span data-ttu-id="1fdfe-111">请考虑以下示例中，它可以存储在 UNC 共享的密钥并对这些密钥在使用特定的 X.509 证书的其余部分进行加密。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-111">Consider the example below, which stores keys at a UNC share and encrypts those keys at rest with a specific X.509 certificate.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

<span data-ttu-id="1fdfe-112">请参阅[密钥加密对静止](../implementation/key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest)有关更多示例和有关讨论的内置密钥加密机制。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-112">See [key encryption at rest](../implementation/key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) for more examples and for discussion on the built-in key encryption mechanisms.</span></span>

<span data-ttu-id="1fdfe-113">若要配置系统而不是 90 天使用的默认密钥生存期为 14 天，请考虑下面的示例：</span><span class="sxs-lookup"><span data-stu-id="1fdfe-113">To configure the system to use a default key lifetime of 14 days instead of 90 days, consider the following example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

<span data-ttu-id="1fdfe-114">默认情况下数据保护系统隔离应用程序从另一个，即使它们共享相同的物理密钥存储库。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-114">By default the data protection system isolates applications from one another, even if they're sharing the same physical key repository.</span></span> <span data-ttu-id="1fdfe-115">这样可以防止应用程序从了解对方的受保护的负载。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-115">This prevents the applications from understanding each other's protected payloads.</span></span> <span data-ttu-id="1fdfe-116">若要共享两个不同的应用程序之间的受保护的负载，配置系统在两个应用程序中的相同应用程序名称传递下面的示例：</span><span class="sxs-lookup"><span data-stu-id="1fdfe-116">To share protected payloads between two different applications, configure the system passing in the same application name for both applications as in the below example:</span></span>

<a name=data-protection-code-sample-application-name></a>

<!-- literal_block {"ids": ["data-protection-code-sample-application-name"], "linenos": false, "names": ["data-protection-code-sample-application-name"], "xml:space": "preserve", "language": "csharp"} -->

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("my application");
}
```

<a name=data-protection-configuring-disable-automatic-key-generation></a>

<span data-ttu-id="1fdfe-117">最后，你可能必须在你不希望自动轮转密钥，因为它们接近过期的应用程序的方案。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-117">Finally, you may have a scenario where you do not want an application to automatically roll keys as they approach expiration.</span></span> <span data-ttu-id="1fdfe-118">这一个示例可能是应用程序设置中的主 / 辅助的关系，其中只有主应用程序负责密钥管理问题，而且所有辅助应用程序只需具有密钥环的只读视图。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-118">One example of this might be applications set up in a primary / secondary relationship, where only the primary application is responsible for key management concerns, and all secondary applications simply have a read-only view of the key ring.</span></span> <span data-ttu-id="1fdfe-119">通过按如下所示配置系统以只读方式处理密钥环，可以配置辅助应用程序：</span><span class="sxs-lookup"><span data-stu-id="1fdfe-119">The secondary applications can be configured to treat the key ring as read-only by configuring the system as below:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

<a name=data-protection-configuration-per-app-isolation></a>

## <a name="per-application-isolation"></a><span data-ttu-id="1fdfe-120">每个应用程序隔离</span><span class="sxs-lookup"><span data-stu-id="1fdfe-120">Per-application isolation</span></span>

<span data-ttu-id="1fdfe-121">数据保护系统提供了 ASP.NET 核心主机，它将自动将应用程序隔离从另一个，即使这些应用程序在相同的工作进程帐户下运行，并且使用相同的主密钥材料。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-121">When the data protection system is provided by an ASP.NET Core host, it will automatically isolate applications from one another, even if those applications are running under the same worker process account and are using the same master keying material.</span></span> <span data-ttu-id="1fdfe-122">这是某种程度上类似于 IsolateApps 修饰符从 System.Web 的<machineKey>元素。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-122">This is somewhat similar to the IsolateApps modifier from System.Web's <machineKey> element.</span></span>

<span data-ttu-id="1fdfe-123">隔离机制的工作方式通过考虑作为唯一租户本地计算机上的每个应用程序，因此取得 root 权限的任何给定应用程序自动 IDataProtector 包括应用程序 ID 作为鉴别器。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-123">The isolation mechanism works by considering each application on the local machine as a unique tenant, thus the IDataProtector rooted for any given application automatically includes the application ID as a discriminator.</span></span> <span data-ttu-id="1fdfe-124">应用程序的唯一 ID 来自两个位置之一。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-124">The application's unique ID comes from one of two places.</span></span>

1. <span data-ttu-id="1fdfe-125">如果在 IIS 中托管的应用程序的唯一标识符是应用程序的配置路径。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-125">If the application is hosted in IIS, the unique identifier is the application's configuration path.</span></span> <span data-ttu-id="1fdfe-126">如果在场环境中部署应用程序，此值应为稳定，前提是在服务器场中的所有计算机同样配置 IIS 环境。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-126">If an application is deployed in a farm environment, this value should be stable assuming that the IIS environments are configured similarly across all machines in the farm.</span></span>

2. <span data-ttu-id="1fdfe-127">如果应用程序不承载于 IIS 中的唯一标识符是应用程序的物理路径。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-127">If the application is not hosted in IIS, the unique identifier is the physical path of the application.</span></span>

<span data-ttu-id="1fdfe-128">唯一标识符旨在得以重置-在站点的单个应用程序和计算机本身。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-128">The unique identifier is designed to survive resets - both of the individual application and of the machine itself.</span></span>

<span data-ttu-id="1fdfe-129">此隔离机制假设应用程序不恶意。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-129">This isolation mechanism assumes that the applications are not malicious.</span></span> <span data-ttu-id="1fdfe-130">恶意应用程序始终可以影响在相同的工作进程帐户下运行的任何其他应用程序。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-130">A malicious application can always impact any other application running under the same worker process account.</span></span> <span data-ttu-id="1fdfe-131">在共享宿主环境中在应用程序很相互不受信任，托管提供商应采取措施来确保应用程序，包括分离应用程序的基础密钥的存储库之间的操作系统级别隔离。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-131">In a shared hosting environment where applications are mutually untrusted, the hosting provider should take steps to ensure OS-level isolation between applications, including separating the applications' underlying key repositories.</span></span>

<span data-ttu-id="1fdfe-132">如果数据保护系统不会提供程序 ASP.NET Core 宿主 （例如，如果开发人员将其实例化自己通过 DataProtectionProvider 具体类型），默认情况下，禁用应用程序隔离到由相同的密钥的所有应用程序支持材料可以共享的负载，只要它们提供适当的目的。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-132">If the data protection system is not provided by an ASP.NET Core host (e.g., if the developer instantiates it himself via the DataProtectionProvider concrete type), application isolation is disabled by default, and all applications backed by the same keying material can share payloads as long as they provide the appropriate purposes.</span></span> <span data-ttu-id="1fdfe-133">若要提供在此环境中的应用程序隔离，在配置对象上调用 SetApplicationName 方法，请参阅[的代码示例](#data-protection-code-sample-application-name)上面。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-133">To provide application isolation in this environment, call the SetApplicationName method on the configuration object, see the [code sample](#data-protection-code-sample-application-name) above.</span></span>

<a name=data-protection-changing-algorithms></a>

## <a name="changing-algorithms"></a><span data-ttu-id="1fdfe-134">更改算法</span><span class="sxs-lookup"><span data-stu-id="1fdfe-134">Changing algorithms</span></span>

<span data-ttu-id="1fdfe-135">数据保护堆栈，可以更改默认的算法使用的新生成的键。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-135">The data protection stack allows changing the default algorithm used by newly-generated keys.</span></span> <span data-ttu-id="1fdfe-136">执行此操作的最简单方法是从配置回调，调用 UseCryptographicAlgorithms，如下面的示例。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-136">The simplest way to do this is to call UseCryptographicAlgorithms from the configuration callback, as in the below example.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="1fdfe-137">ASP.NET 核心 2.x</span><span class="sxs-lookup"><span data-stu-id="1fdfe-137">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(new AuthenticatedEncryptorConfiguration()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="1fdfe-138">ASP.NET 核心 1.x</span><span class="sxs-lookup"><span data-stu-id="1fdfe-138">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(new AuthenticatedEncryptionSettings()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

---

<span data-ttu-id="1fdfe-139">默认 EncryptionAlgorithm 和 ValidationAlgorithm 分别为 AES 256 CBC 和 HMACSHA256。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-139">The default EncryptionAlgorithm and ValidationAlgorithm are AES-256-CBC and HMACSHA256, respectively.</span></span> <span data-ttu-id="1fdfe-140">默认策略可以设置由系统管理员通过[计算机宽策略](machine-wide-policy.md)，但对 UseCryptographicAlgorithms 的显式调用将替代默认策略。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-140">The default policy can be set by a system administrator via [Machine Wide Policy](machine-wide-policy.md), but an explicit call to UseCryptographicAlgorithms will override the default policy.</span></span>

<span data-ttu-id="1fdfe-141">调用 UseCryptographicAlgorithms 将允许开发人员指定所需的算法 （从预定义内置列表） 和开发人员不需要担心算法的实现。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-141">Calling UseCryptographicAlgorithms will allow the developer to specify the desired algorithm (from a predefined built-in list), and the developer does not need to worry about the implementation of the algorithm.</span></span> <span data-ttu-id="1fdfe-142">例如，在上面的数据保护系统的方案将尝试使用 AES 的 CNG 实现，在 Windows 上运行，否则它会回退到托管的 System.Security.Cryptography.Aes 类。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-142">For instance, in the scenario above the data protection system will attempt to use the CNG implementation of AES if running on Windows, otherwise it will fall back to the managed System.Security.Cryptography.Aes class.</span></span>

<span data-ttu-id="1fdfe-143">开发人员可以手动指定如果需要通过调用 UseCustomCryptographicAlgorithms，如所示实现下面的示例。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-143">The developer can manually specify an implementation if desired via a call to UseCustomCryptographicAlgorithms, as show in the below examples.</span></span>

>[!TIP]
> <span data-ttu-id="1fdfe-144">更改算法不会影响现有的密钥在密钥环。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-144">Changing algorithms does not affect existing keys in the key ring.</span></span> <span data-ttu-id="1fdfe-145">它只影响新生成的键。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-145">It only affects newly-generated keys.</span></span>

<a name=data-protection-changing-algorithms-custom-managed></a>

### <a name="specifying-custom-managed-algorithms"></a><span data-ttu-id="1fdfe-146">指定自定义托管的算法</span><span class="sxs-lookup"><span data-stu-id="1fdfe-146">Specifying custom managed algorithms</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="1fdfe-147">ASP.NET 核心 2.x</span><span class="sxs-lookup"><span data-stu-id="1fdfe-147">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="1fdfe-148">若要指定自定义托管的算法，创建指向的实现类型的 ManagedAuthenticatedEncryptorConfiguration 实例。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-148">To specify custom managed algorithms, create a ManagedAuthenticatedEncryptorConfiguration instance that points to the implementation types.</span></span>

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(new ManagedAuthenticatedEncryptorConfiguration()
    {
        // a type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // specified in bits
        EncryptionAlgorithmKeySize = 256,

        // a type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="1fdfe-149">ASP.NET 核心 1.x</span><span class="sxs-lookup"><span data-stu-id="1fdfe-149">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="1fdfe-150">若要指定自定义托管的算法，创建指向的实现类型的 ManagedAuthenticatedEncryptionSettings 实例。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-150">To specify custom managed algorithms, create a ManagedAuthenticatedEncryptionSettings instance that points to the implementation types.</span></span>

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(new ManagedAuthenticatedEncryptionSettings()
    {
        // a type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // specified in bits
        EncryptionAlgorithmKeySize = 256,

        // a type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

---

<span data-ttu-id="1fdfe-151">通常\*类型属性必须指向具体，可实例化 （通过公共的无参数 ctor) 实现的 SymmetricAlgorithm 和 KeyedHashAlgorithm，尽管等系统特殊情况下某些值为方便起见 typeof(Aes).</span><span class="sxs-lookup"><span data-stu-id="1fdfe-151">Generally the \*Type properties must point to concrete, instantiable (via a public parameterless ctor) implementations of SymmetricAlgorithm and KeyedHashAlgorithm, though the system special-cases some values like typeof(Aes) for convenience.</span></span>

> [!NOTE]
> <span data-ttu-id="1fdfe-152">SymmetricAlgorithm 必须 ≥ 128 位的密钥长度和块大小的 ≥ 64 位，并且它必须支持使用 PKCS #7 填充 CBC 模式下的加密。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-152">The SymmetricAlgorithm must have a key length of ≥ 128 bits and a block size of ≥ 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="1fdfe-153">KeyedHashAlgorithm 的摘要大小必须 > = 128 位，并且它必须支持密钥长度等于 length 哈希算法的摘要。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-153">The KeyedHashAlgorithm must have a digest size of >= 128 bits, and it must support keys of length equal to the hash algorithm's digest length.</span></span> <span data-ttu-id="1fdfe-154">KeyedHashAlgorithm 不是绝对必需，要 HMAC。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-154">The KeyedHashAlgorithm is not strictly required to be HMAC.</span></span>

<a name=data-protection-changing-algorithms-cng></a>

### <a name="specifying-custom-windows-cng-algorithms"></a><span data-ttu-id="1fdfe-155">指定自定义 Windows CNG 算法</span><span class="sxs-lookup"><span data-stu-id="1fdfe-155">Specifying custom Windows CNG algorithms</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="1fdfe-156">ASP.NET 核心 2.x</span><span class="sxs-lookup"><span data-stu-id="1fdfe-156">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="1fdfe-157">若要指定自定义 Windows CNG 算法使用 CBC 模式下加密 + HMAC 验证，请创建包含的算法信息的 CngCbcAuthenticatedEncryptorConfiguration 实例。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-157">To specify a custom Windows CNG algorithm using CBC-mode encryption + HMAC validation, create a CngCbcAuthenticatedEncryptorConfiguration instance that contains the algorithmic information.</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(new CngCbcAuthenticatedEncryptorConfiguration()
    {
        // passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // specified in bits
        EncryptionAlgorithmKeySize = 256,

        // passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="1fdfe-158">ASP.NET 核心 1.x</span><span class="sxs-lookup"><span data-stu-id="1fdfe-158">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="1fdfe-159">若要指定自定义 Windows CNG 算法使用 CBC 模式下加密 + HMAC 验证，请创建包含的算法信息的 CngCbcAuthenticatedEncryptionSettings 实例。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-159">To specify a custom Windows CNG algorithm using CBC-mode encryption + HMAC validation, create a CngCbcAuthenticatedEncryptionSettings instance that contains the algorithmic information.</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(new CngCbcAuthenticatedEncryptionSettings()
    {
        // passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // specified in bits
        EncryptionAlgorithmKeySize = 256,

        // passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

---

> [!NOTE]
> <span data-ttu-id="1fdfe-160">对称块加密算法必须 ≥ 128 位的密钥长度和块大小的 ≥ 64 位，并且它必须支持使用 PKCS #7 填充 CBC 模式下的加密。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-160">The symmetric block cipher algorithm must have a key length of ≥ 128 bits and a block size of ≥ 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="1fdfe-161">哈希算法的摘要大小必须 > = 128 位并且必须支持使用 BCRYPT_ALG_HANDLE_HMAC_FLAG 标志打开。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-161">The hash algorithm must have a digest size of >= 128 bits and must support being opened with the BCRYPT_ALG_HANDLE_HMAC_FLAG flag.</span></span> <span data-ttu-id="1fdfe-162">\*提供程序属性可以设置为空，默认的提供程序用于为指定的算法。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-162">The \*Provider properties can be set to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="1fdfe-163">请参阅[BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx)文档以了解更多信息。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-163">See the [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation for more information.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="1fdfe-164">ASP.NET 核心 2.x</span><span class="sxs-lookup"><span data-stu-id="1fdfe-164">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="1fdfe-165">若要指定自定义 Windows CNG 算法使用 Galois/计数器模式加密 + 验证，请创建包含的算法信息的 CngGcmAuthenticatedEncryptorConfiguration 实例。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-165">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption + validation, create a CngGcmAuthenticatedEncryptorConfiguration instance that contains the algorithmic information.</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(new CngGcmAuthenticatedEncryptorConfiguration()
    {
        // passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="1fdfe-166">ASP.NET 核心 1.x</span><span class="sxs-lookup"><span data-stu-id="1fdfe-166">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="1fdfe-167">若要指定自定义 Windows CNG 算法使用 Galois/计数器模式加密 + 验证，请创建包含的算法信息的 CngGcmAuthenticatedEncryptionSettings 实例。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-167">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption + validation, create a CngGcmAuthenticatedEncryptionSettings instance that contains the algorithmic information.</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(new CngGcmAuthenticatedEncryptionSettings()
    {
        // passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

---

> [!NOTE]
> <span data-ttu-id="1fdfe-168">对称块加密算法必须 ≥ 128 位的密钥长度和块大小的完全 128 位，并且它必须支持 GCM 加密。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-168">The symmetric block cipher algorithm must have a key length of ≥ 128 bits and a block size of exactly 128 bits, and it must support GCM encryption.</span></span> <span data-ttu-id="1fdfe-169">EncryptionAlgorithmProvider 属性可以设置为 null，若要使用默认提供程序指定的算法。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-169">The EncryptionAlgorithmProvider property can be set to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="1fdfe-170">请参阅[BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx)文档以了解更多信息。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-170">See the [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation for more information.</span></span>

### <a name="specifying-other-custom-algorithms"></a><span data-ttu-id="1fdfe-171">指定其他自定义算法</span><span class="sxs-lookup"><span data-stu-id="1fdfe-171">Specifying other custom algorithms</span></span>

<span data-ttu-id="1fdfe-172">尽管不作为第一类 API 公开，则数据保护系统是算法的可扩展，足以允许指定几乎任何类型。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-172">Though not exposed as a first-class API, the data protection system is extensible enough to allow specifying almost any kind of algorithm.</span></span> <span data-ttu-id="1fdfe-173">例如，很可能需要包含在 HSM 中的所有键，并为核心的自定义实现加密和解密的例程。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-173">For example, it is possible to keep all keys contained within an HSM and to provide a custom implementation of the core encryption and decryption routines.</span></span> <span data-ttu-id="1fdfe-174">请参阅 IAuthenticatedEncryptorConfiguration 中核心加密可扩展性部分以了解更多信息。</span><span class="sxs-lookup"><span data-stu-id="1fdfe-174">See IAuthenticatedEncryptorConfiguration in the core cryptography extensibility section for more information.</span></span>

### <a name="see-also"></a><span data-ttu-id="1fdfe-175">请参阅</span><span class="sxs-lookup"><span data-stu-id="1fdfe-175">See also</span></span>

* [<span data-ttu-id="1fdfe-176">非 DI 感知的情境</span><span class="sxs-lookup"><span data-stu-id="1fdfe-176">Non DI Aware Scenarios</span></span>](non-di-scenarios.md)
* [<span data-ttu-id="1fdfe-177">计算机范围的策略</span><span class="sxs-lookup"><span data-stu-id="1fdfe-177">Machine Wide Policy</span></span>](machine-wide-policy.md)
