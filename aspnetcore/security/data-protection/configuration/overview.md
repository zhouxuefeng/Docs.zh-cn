---
title: "配置数据保护"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 0e4881a3-a94d-4e35-9c1c-f025d65dcff0
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/configuration/overview
ms.openlocfilehash: d35e0e806999ffd2e0f8f82e0adfc940ea2b503d
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2017
---
# <a name="configuring-data-protection"></a>配置数据保护

<a name="data-protection-configuring"></a>

初始化数据保护系统时它将部分[默认设置](default-settings.md#data-protection-default-settings)基于的操作环境。 这些设置是在一台计算机上运行的应用程序通常完好的。 在某些情况的下，开发人员可能想要更改它们 (可能是因为他们的应用程序分布在多个计算机之间或者出于合规原因)，并为这些方案数据保护系统提供的丰富的配置 API。

<a name="data-protection-configuration-callback"></a>

没有扩展方法 AddDataProtection 它返回 IDataProtectionBuilder 其本身公开，你可以链接在一起以配置各种数据保护选项的扩展方法。 例如，若要存储在 UNC 共享而不是 %LOCALAPPDATA%（默认值） 的密钥，将系统配置，如下所示：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

>[!WARNING]
> 如果更改密钥持久性位置时，系统将不再自动会加密存放的密钥，因为它不知道 DPAPI 是否合适的加密机制。

<a name="configuring-x509-certificate"></a>

你可以将系统配置为通过调用任何 ProtectKeysWith 保护静止的密钥\*配置 Api。 请考虑以下示例中，它可以存储在 UNC 共享的密钥并对这些密钥在使用特定的 X.509 证书的其余部分进行加密。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

请参阅[密钥加密对静止](../implementation/key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest)有关更多示例和有关讨论的内置密钥加密机制。

若要配置系统而不是 90 天使用的默认密钥生存期为 14 天，请考虑下面的示例：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

默认情况下数据保护系统隔离应用程序从另一个，即使它们共享相同的物理密钥存储库。 这样可以防止应用程序从了解对方的受保护的负载。 若要共享两个不同的应用程序之间的受保护的负载，配置系统在两个应用程序中的相同应用程序名称传递下面的示例：

<a name="data-protection-code-sample-application-name"></a>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("my application");
}
```

<a name="data-protection-configuring-disable-automatic-key-generation"></a>

最后，你可能必须在你不希望自动轮转密钥，因为它们接近过期的应用程序的方案。 这一个示例可能是应用程序设置中的主 / 辅助的关系，其中只有主应用程序负责密钥管理问题，而且所有辅助应用程序只需具有密钥环的只读视图。 通过按如下所示配置系统以只读方式处理密钥环，可以配置辅助应用程序：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

<a name="data-protection-configuration-per-app-isolation"></a>

## <a name="per-application-isolation"></a>每个应用程序隔离

数据保护系统提供了 ASP.NET 核心主机，它将自动将应用程序隔离从另一个，即使这些应用程序在相同的工作进程帐户下运行，并且使用相同的主密钥材料。 这是某种程度上类似于 IsolateApps 修饰符从 System.Web 的<machineKey>元素。

隔离机制的工作方式通过考虑作为唯一租户本地计算机上的每个应用程序，因此取得 root 权限的任何给定应用程序自动 IDataProtector 包括应用程序 ID 作为鉴别器。 应用程序的唯一 ID 来自两个位置之一。

1. 如果在 IIS 中托管的应用程序的唯一标识符是应用程序的配置路径。 如果在场环境中部署应用程序，此值应为稳定，前提是在服务器场中的所有计算机同样配置 IIS 环境。

2. 如果应用程序不承载于 IIS 中的唯一标识符是应用程序的物理路径。

唯一标识符旨在得以重置-在站点的单个应用程序和计算机本身。

此隔离机制假设应用程序不恶意。 恶意应用程序始终可以影响在相同的工作进程帐户下运行的任何其他应用程序。 在共享宿主环境中在应用程序很相互不受信任，托管提供商应采取措施来确保应用程序，包括分离应用程序的基础密钥的存储库之间的操作系统级别隔离。

如果数据保护系统不会提供程序 ASP.NET Core 宿主 （例如，如果开发人员将其实例化自己通过 DataProtectionProvider 具体类型），默认情况下，禁用应用程序隔离到由相同的密钥的所有应用程序支持材料可以共享的负载，只要它们提供适当的目的。 若要提供在此环境中的应用程序隔离，在配置对象上调用 SetApplicationName 方法，请参阅[的代码示例](#data-protection-code-sample-application-name)上面。

<a name="data-protection-changing-algorithms"></a>

## <a name="changing-algorithms"></a>更改算法

数据保护堆栈，可以更改默认的算法使用的新生成的键。 执行此操作的最简单方法是从配置回调，调用 UseCryptographicAlgorithms，如下面的示例。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(new AuthenticatedEncryptorConfiguration()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(new AuthenticatedEncryptionSettings()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

---

默认 EncryptionAlgorithm 和 ValidationAlgorithm 分别为 AES 256 CBC 和 HMACSHA256。 默认策略可以设置由系统管理员通过[计算机宽策略](machine-wide-policy.md)，但对 UseCryptographicAlgorithms 的显式调用将替代默认策略。

调用 UseCryptographicAlgorithms 将允许开发人员指定所需的算法 （从预定义内置列表） 和开发人员不需要担心算法的实现。 例如，在上面的数据保护系统的方案将尝试使用 AES 的 CNG 实现，在 Windows 上运行，否则它会回退到托管的 System.Security.Cryptography.Aes 类。

开发人员可以手动指定如果需要通过调用 UseCustomCryptographicAlgorithms，如所示实现下面的示例。

>[!TIP]
> 更改算法不会影响现有的密钥在密钥环。 它只影响新生成的键。

<a name="data-protection-changing-algorithms-custom-managed"></a>

### <a name="specifying-custom-managed-algorithms"></a>指定自定义托管的算法

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

若要指定自定义托管的算法，创建指向的实现类型的 ManagedAuthenticatedEncryptorConfiguration 实例。

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

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

若要指定自定义托管的算法，创建指向的实现类型的 ManagedAuthenticatedEncryptionSettings 实例。

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

通常\*类型属性必须指向具体，可实例化 （通过公共的无参数 ctor) 实现的 SymmetricAlgorithm 和 KeyedHashAlgorithm，尽管等系统特殊情况下某些值为方便起见 typeof(Aes).

> [!NOTE]
> SymmetricAlgorithm 必须 ≥ 128 位的密钥长度和块大小的 ≥ 64 位，并且它必须支持使用 PKCS #7 填充 CBC 模式下的加密。 KeyedHashAlgorithm 的摘要大小必须 > = 128 位，并且它必须支持密钥长度等于 length 哈希算法的摘要。 KeyedHashAlgorithm 不是绝对必需，要 HMAC。

<a name="data-protection-changing-algorithms-cng"></a>

### <a name="specifying-custom-windows-cng-algorithms"></a>指定自定义 Windows CNG 算法

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

若要指定自定义 Windows CNG 算法使用 CBC 模式下加密 + HMAC 验证，请创建包含的算法信息的 CngCbcAuthenticatedEncryptorConfiguration 实例。

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

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

若要指定自定义 Windows CNG 算法使用 CBC 模式下加密 + HMAC 验证，请创建包含的算法信息的 CngCbcAuthenticatedEncryptionSettings 实例。

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
> 对称块加密算法必须 ≥ 128 位的密钥长度和块大小的 ≥ 64 位，并且它必须支持使用 PKCS #7 填充 CBC 模式下的加密。 哈希算法的摘要大小必须 > = 128 位并且必须支持使用 BCRYPT_ALG_HANDLE_HMAC_FLAG 标志打开。 \*提供程序属性可以设置为空，默认的提供程序用于为指定的算法。 请参阅[BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx)文档以了解更多信息。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

若要指定自定义 Windows CNG 算法使用 Galois/计数器模式加密 + 验证，请创建包含的算法信息的 CngGcmAuthenticatedEncryptorConfiguration 实例。

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

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

若要指定自定义 Windows CNG 算法使用 Galois/计数器模式加密 + 验证，请创建包含的算法信息的 CngGcmAuthenticatedEncryptionSettings 实例。

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
> 对称块加密算法必须 ≥ 128 位的密钥长度和块大小的完全 128 位，并且它必须支持 GCM 加密。 EncryptionAlgorithmProvider 属性可以设置为 null，若要使用默认提供程序指定的算法。 请参阅[BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx)文档以了解更多信息。

### <a name="specifying-other-custom-algorithms"></a>指定其他自定义算法

尽管不作为第一类 API 公开，则数据保护系统是算法的可扩展，足以允许指定几乎任何类型。 例如，很可能需要包含在 HSM 中的所有键，并为核心的自定义实现加密和解密的例程。 请参阅 IAuthenticatedEncryptorConfiguration 中核心加密可扩展性部分以了解更多信息。

### <a name="see-also"></a>请参阅

* [非 DI 感知方案](non-di-scenarios.md)
* [计算机范围的策略](machine-wide-policy.md)
