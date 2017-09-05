---
title: "密钥管理扩展性"
author: rick-anderson
description: 
keywords: "ASP.NET 核心"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 3606b251-8324-4485-8d52-582a2cd5cffb
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: fb74905660015b9a83503e1f74b25c66ae9df9e3
ms.sourcegitcommit: bd05f7ea8f87ad076ef6e8b704698ebcba5ca80c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2017
---
# <a name="key-management-extensibility"></a>密钥管理扩展性

<a name=data-protection-extensibility-key-management></a>

>[!TIP]
> 读取[密钥管理](../implementation/key-management.md#data-protection-implementation-key-management)之前阅读此部分中，因为它介绍了这些 Api 的基本概念的某些部分。

>[!WARNING]
> 实现的任意以下接口的类型应该是线程安全的多个调用方。

## <a name="key"></a>键

IKey 接口是键的加密系统中的基本表示。 在抽象的意义上，不在"加密密钥材料"字面意义上此处使用的术语密钥。 密钥具有以下属性：

* 激活、 创建和到期日期

* 吊销状态

* 密钥标识符 (GUID)

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET 核心 2.x](#tab/aspnetcore2x)

此外，IKey 公开用于创建的 CreateEncryptor 方法[IAuthenticatedEncryptor](core-crypto.md#data-protection-extensibility-core-crypto-iauthenticatedencryptor)实例绑定到此密钥。

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET 核心 1.x](#tab/aspnetcore1x)

此外，IKey 公开用于创建的 CreateEncryptorInstance 方法[IAuthenticatedEncryptor](core-crypto.md#data-protection-extensibility-core-crypto-iauthenticatedencryptor)实例绑定到此密钥。

---

> [!NOTE]
> 没有 API 从 IKey 实例中检索原始加密材料。

## <a name="ikeymanager"></a>IKeyManager

IKeyManager 接口表示负责常规的密钥存储、 检索和操作的对象。 它公开三个高级操作：

* 创建新密钥并将其保存在存储。

* 从存储中获取所有键。

* 撤消一个或多个密钥，而且将保留到存储的吊销信息。

>[!WARNING]
> 编写 IKeyManager 是一个非常高级的任务，和大多数开发人员不应尝试它。 相反，大多数开发人员应充分利用所提供的功能[XmlKeyManager](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-xmlkeymanager)类。

<a name=data-protection-extensibility-key-management-xmlkeymanager></a>

## <a name="xmlkeymanager"></a>XmlKeyManager

XmlKeyManager 类型是 IKeyManager 的现成具体实现。 它提供了几个有用的功能，包括密钥证书和加密对静止的密钥。 在此系统的密钥将呈现为 XML 元素 (具体而言， [XElement](https://msdn.microsoft.com/library/system.xml.linq.xelement(v=vs.110).aspx))。

XmlKeyManager 取决于在完成其任务的过程中的其他几个组件：

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET 核心 2.x](#tab/aspnetcore2x)

* AlgorithmConfiguration，也就是使用新密钥的算法要求。

* IXmlRepository，其中键将保留在存储中的控件。

* 这允许加密对静止的密钥 IXmlEncryptor [可选]。

* 提供密钥托管服务 IKeyEscrowSink [可选]。

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET 核心 1.x](#tab/aspnetcore1x)

* IXmlRepository，其中键将保留在存储中的控件。

* 这允许加密对静止的密钥 IXmlEncryptor [可选]。

* 提供密钥托管服务 IKeyEscrowSink [可选]。

---

以下是高级关系图，表明如何这些组件连接在一起在 XmlKeyManager 内。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET 核心 2.x](#tab/aspnetcore2x)

   ![密钥创建](key-management/_static/keycreation2.png)

   *密钥创建 / CreateNewKey*

在 CreateNewKey 实现中，AlgorithmConfiguration 组件用于创建唯一 IAuthenticatedEncryptorDescriptor，然后序列化为 XML。 如果存在密钥托管接收器，则原始 （未加密） 的 XML 到接收器提供适用于长期存储。 未加密的 XML 然后通过运行 IXmlEncryptor （如果需要） 来生成加密的 XML 文档。 此加密的文档保存到通过 IXmlRepository 长期存储。 （如果配置没有 IXmlEncryptor，未加密的文档被保留在 IXmlRepository。）

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET 核心 1.x](#tab/aspnetcore1x)

   ![密钥创建](key-management/_static/keycreation1.png)

   *密钥创建 / CreateNewKey*

在 CreateNewKey 实现中，IAuthenticatedEncryptorConfiguration 组件用于创建唯一 IAuthenticatedEncryptorDescriptor，然后序列化为 XML。 如果存在密钥托管接收器，则原始 （未加密） 的 XML 到接收器提供适用于长期存储。 未加密的 XML 然后通过运行 IXmlEncryptor （如果需要） 来生成加密的 XML 文档。 此加密的文档保存到通过 IXmlRepository 长期存储。 （如果配置没有 IXmlEncryptor，未加密的文档被保留在 IXmlRepository。）

---

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET 核心 2.x](#tab/aspnetcore2x)

   ![密钥检索](key-management/_static/keyretrieval2.png)
   
# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET 核心 1.x](#tab/aspnetcore1x)

   ![密钥检索](key-management/_static/keyretrieval1.png)

---

   *密钥检索 / GetAllKeys*

在 GetAllKeys 实现中，从基础 IXmlRepository 读取表示密钥和吊销的 XML 文档。 如果这些文档进行加密，系统将自动解密。 XmlKeyManager 中创建相应的 IAuthenticatedEncryptorDescriptorDeserializer 实例，重新插入 IAuthenticatedEncryptorDescriptor 实例，稍后被包装在单个 IKey 实例中反序列化文档。 此集合 IKey 实例返回到调用方。

在找不到在特定的 XML 元素上的进一步信息[密钥存储格式文档](../implementation/key-storage-format.md#data-protection-implementation-key-storage-format)。

## <a name="ixmlrepository"></a>IXmlRepository

IXmlRepository 接口表示可保留 XML 到和从一个后备存储检索 XML 的类型。 它公开两个 Api:

* GetAllElements(): IReadOnlyCollection<XElement>

* StoreElement （XElement 元素，字符串 friendlyName）

IXmlRepository 的实现不需要通过其传递对 XML 进行分析。 它们应视为不透明 XML 文档，让较高层担心生成和分析文档。

有两个内置的具体类型实现 IXmlRepository: FileSystemXmlRepository 和 RegistryXmlRepository。 请参阅[密钥存储提供程序文档](../implementation/key-storage-providers.md#data-protection-implementation-key-storage-providers)有关详细信息。 注册自定义 IXmlRepository 将对它们进行适当的方式，以使用其他备份应用商店，例如，Azure Blob 存储。 若要更改默认存储库应用程序范围，请在服务提供程序中注册的自定义单独 IXmlRepository。

<a name=data-protection-extensibility-key-management-ixmlencryptor></a>

## <a name="ixmlencryptor"></a>IXmlEncryptor

IXmlEncryptor 接口表示可以加密纯文本 XML 元素的类型。 它公开单个 API:

* 加密 (XElement plaintextElement): EncryptedXmlInfo

如果序列化的 IAuthenticatedEncryptorDescriptor 包含标记为"需要加密"的任何元素，然后 XmlKeyManager 将配置的 IXmlEncryptor 加密方法，通过运行这些元素，它会继续而不是保留 enciphered 的元素比到 IXmlRepository 的纯文本元素。 加密方法的输出是一个 EncryptedXmlInfo 对象。 此对象是包装器，其中包含生成 enciphered 的 XElement 和表示 IXmlDecryptor 用于解密的相应元素的类型。

有四个内置的具体类型实现 IXmlEncryptor: CertificateXmlEncryptor、 DpapiNGXmlEncryptor、 DpapiXmlEncryptor 和 NullXmlEncryptor。 请参阅[rest 文档的密钥加密](../implementation/key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest)有关详细信息。 若要更改默认密钥加密在 rest 机制整个应用程序范围，请在服务提供程序中注册的自定义单独 IXmlEncryptor。

## <a name="ixmldecryptor"></a>IXmlDecryptor

IXmlDecryptor 接口表示一种类型，知道如何解密已通过 IXmlEncryptor enciphered 的 XElement。 它公开单个 API:

* 解密 (XElement encryptedElement): XElement

解密方法撤消由 IXmlEncryptor.Encrypt 执行加密。 一般每个具体的 IXmlEncryptor 实现会提供相应的具体 IXmlDecryptor 实现。

实现 IXmlDecryptor 该类型应具有以下两个公共构造函数之一：

* .ctor(IServiceProvider)

* .ctor()

> [!NOTE]
> IServiceProvider 传递到构造函数可能为 null。

## <a name="ikeyescrowsink"></a>IKeyEscrowSink

IKeyEscrowSink 接口表示可以执行托管的敏感信息的类型。 回想一下，序列化的描述符可能包含敏感信息 （如加密材料），这是什么导致了引入[IXmlEncryptor](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-ixmlencryptor)键入第一个位置。 但是，意外的发生，并且 keyrings 可以删除或损坏。

托管接口提供了允许访问原始序列化的 XML 转换由任何配置前紧急转义阴影[IXmlEncryptor](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-ixmlencryptor)。 接口公开单个 API:

* 应用商店 （Guid keyId、 XElement 元素）

负责 IKeyEscrowSink 实现以处理与业务策略一致的安全方式提供的元素。 一个可能的实现可能是为托管接收器使用已知的企业 X.509 证书的 XML 元素进行加密其中已托管证书的私钥;CertificateXmlEncryptor 类型可以对此有帮助。 IKeyEscrowSink 实现负责，也为适当地保留提供的元素。

默认情况下没有托管机制启用，但服务器管理员可以[全局配置此](../configuration/machine-wide-policy.md#data-protection-configuration-machinewidepolicy)。 它还可以配置以编程方式通过*IDataProtectionBuilder.AddKeyEscrowSink*方法，如下面的示例中所示。 *AddKeyEscrowSink*方法重载镜像*IServiceCollection.AddSingleton*和*IServiceCollection.AddInstance* IKeyEscrowSink 的重载，实例旨在能单一实例。 如果注册了多个 IKeyEscrowSink 实例，每个过程中将调用密钥生成，因此密钥可以托管多个机制同时。

没有 API 从 IKeyEscrowSink 实例读取材料。 这是与托管机制的设计理论一致： 它具有用于受信任的颁发机构，方便密钥材料，并且应用程序本身不是受信任颁发机构，因为它不应具有访问自己保管材料。

下面的示例代码演示如何创建和注册 IKeyEscrowSink 其中托管密钥，以便只有"CONTOSODomain 管理员"的成员可以恢复它们。

> [!NOTE]
> 若要运行此示例，你必须是在已加入域的 Windows 8 / Windows Server 2012 计算机和域控制器必须是 Windows Server 2012 或更高版本。

[!code-none[Main](key-management/samples/key-management-extensibility.cs)]
