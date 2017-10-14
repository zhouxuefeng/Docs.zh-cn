---
title: "密钥管理扩展性"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 3606b251-8324-4485-8d52-582a2cd5cffb
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: ce23931e72404347ebc17c69ae90e70cd15328bc
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2017
---
# <a name="key-management-extensibility"></a><span data-ttu-id="11a21-103">密钥管理扩展性</span><span class="sxs-lookup"><span data-stu-id="11a21-103">Key management extensibility</span></span>

<a name="data-protection-extensibility-key-management"></a>

>[!TIP]
> <span data-ttu-id="11a21-104">读取[密钥管理](../implementation/key-management.md#data-protection-implementation-key-management)之前阅读此部分中，因为它介绍了这些 Api 的基本概念的某些部分。</span><span class="sxs-lookup"><span data-stu-id="11a21-104">Read the [key management](../implementation/key-management.md#data-protection-implementation-key-management) section before reading this section, as it explains some of the fundamental concepts behind these APIs.</span></span>

>[!WARNING]
> <span data-ttu-id="11a21-105">实现的任意以下接口的类型应该是线程安全的多个调用方。</span><span class="sxs-lookup"><span data-stu-id="11a21-105">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="key"></a><span data-ttu-id="11a21-106">键</span><span class="sxs-lookup"><span data-stu-id="11a21-106">Key</span></span>

<span data-ttu-id="11a21-107">IKey 接口是键的加密系统中的基本表示。</span><span class="sxs-lookup"><span data-stu-id="11a21-107">The IKey interface is the basic representation of a key in cryptosystem.</span></span> <span data-ttu-id="11a21-108">在抽象的意义上，不在"加密密钥材料"字面意义上此处使用的术语密钥。</span><span class="sxs-lookup"><span data-stu-id="11a21-108">The term key is used here in the abstract sense, not in the literal sense of "cryptographic key material".</span></span> <span data-ttu-id="11a21-109">密钥具有以下属性：</span><span class="sxs-lookup"><span data-stu-id="11a21-109">A key has the following properties:</span></span>

* <span data-ttu-id="11a21-110">激活、 创建和到期日期</span><span class="sxs-lookup"><span data-stu-id="11a21-110">Activation, creation, and expiration dates</span></span>

* <span data-ttu-id="11a21-111">吊销状态</span><span class="sxs-lookup"><span data-stu-id="11a21-111">Revocation status</span></span>

* <span data-ttu-id="11a21-112">密钥标识符 (GUID)</span><span class="sxs-lookup"><span data-stu-id="11a21-112">Key identifier (a GUID)</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="11a21-113">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="11a21-113">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="11a21-114">此外，IKey 公开用于创建的 CreateEncryptor 方法[IAuthenticatedEncryptor](core-crypto.md#data-protection-extensibility-core-crypto-iauthenticatedencryptor)实例绑定到此密钥。</span><span class="sxs-lookup"><span data-stu-id="11a21-114">Additionally, IKey exposes a CreateEncryptor method which can be used to create an [IAuthenticatedEncryptor](core-crypto.md#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="11a21-115">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="11a21-115">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="11a21-116">此外，IKey 公开用于创建的 CreateEncryptorInstance 方法[IAuthenticatedEncryptor](core-crypto.md#data-protection-extensibility-core-crypto-iauthenticatedencryptor)实例绑定到此密钥。</span><span class="sxs-lookup"><span data-stu-id="11a21-116">Additionally, IKey exposes a CreateEncryptorInstance method which can be used to create an [IAuthenticatedEncryptor](core-crypto.md#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

---

> [!NOTE]
> <span data-ttu-id="11a21-117">没有 API 从 IKey 实例中检索原始加密材料。</span><span class="sxs-lookup"><span data-stu-id="11a21-117">There is no API to retrieve the raw cryptographic material from an IKey instance.</span></span>

## <a name="ikeymanager"></a><span data-ttu-id="11a21-118">IKeyManager</span><span class="sxs-lookup"><span data-stu-id="11a21-118">IKeyManager</span></span>

<span data-ttu-id="11a21-119">IKeyManager 接口表示负责常规的密钥存储、 检索和操作的对象。</span><span class="sxs-lookup"><span data-stu-id="11a21-119">The IKeyManager interface represents an object responsible for general key storage, retrieval, and manipulation.</span></span> <span data-ttu-id="11a21-120">它公开三个高级操作：</span><span class="sxs-lookup"><span data-stu-id="11a21-120">It exposes three high-level operations:</span></span>

* <span data-ttu-id="11a21-121">创建新密钥并将其保存在存储。</span><span class="sxs-lookup"><span data-stu-id="11a21-121">Create a new key and persist it to storage.</span></span>

* <span data-ttu-id="11a21-122">从存储中获取所有键。</span><span class="sxs-lookup"><span data-stu-id="11a21-122">Get all keys from storage.</span></span>

* <span data-ttu-id="11a21-123">撤消一个或多个密钥，而且将保留到存储的吊销信息。</span><span class="sxs-lookup"><span data-stu-id="11a21-123">Revoke one or more keys and persist the revocation information to storage.</span></span>

>[!WARNING]
> <span data-ttu-id="11a21-124">编写 IKeyManager 是一个非常高级的任务，和大多数开发人员不应尝试它。</span><span class="sxs-lookup"><span data-stu-id="11a21-124">Writing an IKeyManager is a very advanced task, and the majority of developers should not attempt it.</span></span> <span data-ttu-id="11a21-125">相反，大多数开发人员应充分利用所提供的功能[XmlKeyManager](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-xmlkeymanager)类。</span><span class="sxs-lookup"><span data-stu-id="11a21-125">Instead, most developers should take advantage of the facilities offered by the [XmlKeyManager](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-xmlkeymanager) class.</span></span>

<a name="data-protection-extensibility-key-management-xmlkeymanager"></a>

## <a name="xmlkeymanager"></a><span data-ttu-id="11a21-126">XmlKeyManager</span><span class="sxs-lookup"><span data-stu-id="11a21-126">XmlKeyManager</span></span>

<span data-ttu-id="11a21-127">XmlKeyManager 类型是 IKeyManager 的现成具体实现。</span><span class="sxs-lookup"><span data-stu-id="11a21-127">The XmlKeyManager type is the in-box concrete implementation of IKeyManager.</span></span> <span data-ttu-id="11a21-128">它提供了几个有用的功能，包括密钥证书和加密对静止的密钥。</span><span class="sxs-lookup"><span data-stu-id="11a21-128">It provides several useful facilities, including key escrow and encryption of keys at rest.</span></span> <span data-ttu-id="11a21-129">在此系统的密钥将呈现为 XML 元素 (具体而言， [XElement](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)。</span><span class="sxs-lookup"><span data-stu-id="11a21-129">Keys in this system are represented as XML elements (specifically, [XElement](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview).</span></span>

<span data-ttu-id="11a21-130">XmlKeyManager 取决于在完成其任务的过程中的其他几个组件：</span><span class="sxs-lookup"><span data-stu-id="11a21-130">XmlKeyManager depends on several other components in the course of fulfilling its tasks:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="11a21-131">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="11a21-131">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

* <span data-ttu-id="11a21-132">AlgorithmConfiguration，也就是使用新密钥的算法要求。</span><span class="sxs-lookup"><span data-stu-id="11a21-132">AlgorithmConfiguration, which dictates the algorithms used by new keys.</span></span>

* <span data-ttu-id="11a21-133">IXmlRepository，其中键将保留在存储中的控件。</span><span class="sxs-lookup"><span data-stu-id="11a21-133">IXmlRepository, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="11a21-134">这允许加密对静止的密钥 IXmlEncryptor [可选]。</span><span class="sxs-lookup"><span data-stu-id="11a21-134">IXmlEncryptor [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="11a21-135">提供密钥托管服务 IKeyEscrowSink [可选]。</span><span class="sxs-lookup"><span data-stu-id="11a21-135">IKeyEscrowSink [optional], which provides key escrow services.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="11a21-136">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="11a21-136">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

* <span data-ttu-id="11a21-137">IXmlRepository，其中键将保留在存储中的控件。</span><span class="sxs-lookup"><span data-stu-id="11a21-137">IXmlRepository, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="11a21-138">这允许加密对静止的密钥 IXmlEncryptor [可选]。</span><span class="sxs-lookup"><span data-stu-id="11a21-138">IXmlEncryptor [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="11a21-139">提供密钥托管服务 IKeyEscrowSink [可选]。</span><span class="sxs-lookup"><span data-stu-id="11a21-139">IKeyEscrowSink [optional], which provides key escrow services.</span></span>

---

<span data-ttu-id="11a21-140">以下是高级关系图，表明如何这些组件连接在一起在 XmlKeyManager 内。</span><span class="sxs-lookup"><span data-stu-id="11a21-140">Below are high-level diagrams which indicate how these components are wired together within XmlKeyManager.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="11a21-141">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="11a21-141">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

   ![密钥创建](key-management/_static/keycreation2.png)

   <span data-ttu-id="11a21-143">*密钥创建 / CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="11a21-143">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="11a21-144">在 CreateNewKey 实现中，AlgorithmConfiguration 组件用于创建唯一 IAuthenticatedEncryptorDescriptor，然后序列化为 XML。</span><span class="sxs-lookup"><span data-stu-id="11a21-144">In the implementation of CreateNewKey, the AlgorithmConfiguration component is used to create a unique IAuthenticatedEncryptorDescriptor, which is then serialized as XML.</span></span> <span data-ttu-id="11a21-145">如果存在密钥托管接收器，则原始 （未加密） 的 XML 到接收器提供适用于长期存储。</span><span class="sxs-lookup"><span data-stu-id="11a21-145">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="11a21-146">未加密的 XML 然后通过运行 IXmlEncryptor （如果需要） 来生成加密的 XML 文档。</span><span class="sxs-lookup"><span data-stu-id="11a21-146">The unencrypted XML is then run through an IXmlEncryptor (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="11a21-147">此加密的文档保存到通过 IXmlRepository 长期存储。</span><span class="sxs-lookup"><span data-stu-id="11a21-147">This encrypted document is persisted to long-term storage via the IXmlRepository.</span></span> <span data-ttu-id="11a21-148">（如果配置没有 IXmlEncryptor，未加密的文档被保留在 IXmlRepository。）</span><span class="sxs-lookup"><span data-stu-id="11a21-148">(If no IXmlEncryptor is configured, the unencrypted document is persisted in the IXmlRepository.)</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="11a21-149">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="11a21-149">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

   ![密钥创建](key-management/_static/keycreation1.png)

   <span data-ttu-id="11a21-151">*密钥创建 / CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="11a21-151">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="11a21-152">在 CreateNewKey 实现中，IAuthenticatedEncryptorConfiguration 组件用于创建唯一 IAuthenticatedEncryptorDescriptor，然后序列化为 XML。</span><span class="sxs-lookup"><span data-stu-id="11a21-152">In the implementation of CreateNewKey, the IAuthenticatedEncryptorConfiguration component is used to create a unique IAuthenticatedEncryptorDescriptor, which is then serialized as XML.</span></span> <span data-ttu-id="11a21-153">如果存在密钥托管接收器，则原始 （未加密） 的 XML 到接收器提供适用于长期存储。</span><span class="sxs-lookup"><span data-stu-id="11a21-153">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="11a21-154">未加密的 XML 然后通过运行 IXmlEncryptor （如果需要） 来生成加密的 XML 文档。</span><span class="sxs-lookup"><span data-stu-id="11a21-154">The unencrypted XML is then run through an IXmlEncryptor (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="11a21-155">此加密的文档保存到通过 IXmlRepository 长期存储。</span><span class="sxs-lookup"><span data-stu-id="11a21-155">This encrypted document is persisted to long-term storage via the IXmlRepository.</span></span> <span data-ttu-id="11a21-156">（如果配置没有 IXmlEncryptor，未加密的文档被保留在 IXmlRepository。）</span><span class="sxs-lookup"><span data-stu-id="11a21-156">(If no IXmlEncryptor is configured, the unencrypted document is persisted in the IXmlRepository.)</span></span>

---

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="11a21-157">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="11a21-157">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

   ![密钥检索](key-management/_static/keyretrieval2.png)
   
# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="11a21-159">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="11a21-159">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

   ![密钥检索](key-management/_static/keyretrieval1.png)

---

   <span data-ttu-id="11a21-161">*密钥检索 / GetAllKeys*</span><span class="sxs-lookup"><span data-stu-id="11a21-161">*Key Retrieval / GetAllKeys*</span></span>

<span data-ttu-id="11a21-162">在 GetAllKeys 实现中，从基础 IXmlRepository 读取表示密钥和吊销的 XML 文档。</span><span class="sxs-lookup"><span data-stu-id="11a21-162">In the implementation of GetAllKeys, the XML documents representing keys and revocations are read from the underlying IXmlRepository.</span></span> <span data-ttu-id="11a21-163">如果这些文档进行加密，系统将自动解密。</span><span class="sxs-lookup"><span data-stu-id="11a21-163">If these documents are encrypted, the system will automatically decrypt them.</span></span> <span data-ttu-id="11a21-164">XmlKeyManager 中创建相应的 IAuthenticatedEncryptorDescriptorDeserializer 实例，重新插入 IAuthenticatedEncryptorDescriptor 实例，稍后被包装在单个 IKey 实例中反序列化文档。</span><span class="sxs-lookup"><span data-stu-id="11a21-164">XmlKeyManager creates the appropriate IAuthenticatedEncryptorDescriptorDeserializer instances to deserialize the documents back into IAuthenticatedEncryptorDescriptor instances, which are then wrapped in individual IKey instances.</span></span> <span data-ttu-id="11a21-165">此集合 IKey 实例返回到调用方。</span><span class="sxs-lookup"><span data-stu-id="11a21-165">This collection of IKey instances is returned to the caller.</span></span>

<span data-ttu-id="11a21-166">在找不到在特定的 XML 元素上的进一步信息[密钥存储格式文档](../implementation/key-storage-format.md#data-protection-implementation-key-storage-format)。</span><span class="sxs-lookup"><span data-stu-id="11a21-166">Further information on the particular XML elements can be found in the [key storage format document](../implementation/key-storage-format.md#data-protection-implementation-key-storage-format).</span></span>

## <a name="ixmlrepository"></a><span data-ttu-id="11a21-167">IXmlRepository</span><span class="sxs-lookup"><span data-stu-id="11a21-167">IXmlRepository</span></span>

<span data-ttu-id="11a21-168">IXmlRepository 接口表示可保留 XML 到和从一个后备存储检索 XML 的类型。</span><span class="sxs-lookup"><span data-stu-id="11a21-168">The IXmlRepository interface represents a type that can persist XML to and retrieve XML from a backing store.</span></span> <span data-ttu-id="11a21-169">它公开两个 Api:</span><span class="sxs-lookup"><span data-stu-id="11a21-169">It exposes two APIs:</span></span>

* <span data-ttu-id="11a21-170">GetAllElements(): IReadOnlyCollection<XElement></span><span class="sxs-lookup"><span data-stu-id="11a21-170">GetAllElements() : IReadOnlyCollection<XElement></span></span>

* <span data-ttu-id="11a21-171">StoreElement （XElement 元素，字符串 friendlyName）</span><span class="sxs-lookup"><span data-stu-id="11a21-171">StoreElement(XElement element, string friendlyName)</span></span>

<span data-ttu-id="11a21-172">IXmlRepository 的实现不需要通过其传递对 XML 进行分析。</span><span class="sxs-lookup"><span data-stu-id="11a21-172">Implementations of IXmlRepository don't need to parse the XML passing through them.</span></span> <span data-ttu-id="11a21-173">它们应视为不透明 XML 文档，让较高层担心生成和分析文档。</span><span class="sxs-lookup"><span data-stu-id="11a21-173">They should treat the XML documents as opaque and let higher layers worry about generating and parsing the documents.</span></span>

<span data-ttu-id="11a21-174">有两个内置的具体类型实现 IXmlRepository: FileSystemXmlRepository 和 RegistryXmlRepository。</span><span class="sxs-lookup"><span data-stu-id="11a21-174">There are two built-in concrete types which implement IXmlRepository: FileSystemXmlRepository and RegistryXmlRepository.</span></span> <span data-ttu-id="11a21-175">请参阅[密钥存储提供程序文档](../implementation/key-storage-providers.md#data-protection-implementation-key-storage-providers)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="11a21-175">See the [key storage providers document](../implementation/key-storage-providers.md#data-protection-implementation-key-storage-providers) for more information.</span></span> <span data-ttu-id="11a21-176">注册自定义 IXmlRepository 将对它们进行适当的方式，以使用其他备份应用商店，例如，Azure Blob 存储。</span><span class="sxs-lookup"><span data-stu-id="11a21-176">Registering a custom IXmlRepository would be the appropriate manner to use a different backing store, e.g., Azure Blob Storage.</span></span> <span data-ttu-id="11a21-177">若要更改默认存储库应用程序范围，请在服务提供程序中注册的自定义单独 IXmlRepository。</span><span class="sxs-lookup"><span data-stu-id="11a21-177">To change the default repository application-wide, register a custom singleton IXmlRepository in the service provider.</span></span>

<a name="data-protection-extensibility-key-management-ixmlencryptor"></a>

## <a name="ixmlencryptor"></a><span data-ttu-id="11a21-178">IXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="11a21-178">IXmlEncryptor</span></span>

<span data-ttu-id="11a21-179">IXmlEncryptor 接口表示可以加密纯文本 XML 元素的类型。</span><span class="sxs-lookup"><span data-stu-id="11a21-179">The IXmlEncryptor interface represents a type that can encrypt a plaintext XML element.</span></span> <span data-ttu-id="11a21-180">它公开单个 API:</span><span class="sxs-lookup"><span data-stu-id="11a21-180">It exposes a single API:</span></span>

* <span data-ttu-id="11a21-181">加密 (XElement plaintextElement): EncryptedXmlInfo</span><span class="sxs-lookup"><span data-stu-id="11a21-181">Encrypt(XElement plaintextElement) : EncryptedXmlInfo</span></span>

<span data-ttu-id="11a21-182">如果序列化的 IAuthenticatedEncryptorDescriptor 包含标记为"需要加密"的任何元素，然后 XmlKeyManager 将配置的 IXmlEncryptor 加密方法，通过运行这些元素，它会继续而不是保留 enciphered 的元素比到 IXmlRepository 的纯文本元素。</span><span class="sxs-lookup"><span data-stu-id="11a21-182">If a serialized IAuthenticatedEncryptorDescriptor contains any elements marked as "requires encryption", then XmlKeyManager will run those elements through the configured IXmlEncryptor's Encrypt method, and it will persist the enciphered element rather than the plaintext element to the IXmlRepository.</span></span> <span data-ttu-id="11a21-183">加密方法的输出是一个 EncryptedXmlInfo 对象。</span><span class="sxs-lookup"><span data-stu-id="11a21-183">The output of the Encrypt method is an EncryptedXmlInfo object.</span></span> <span data-ttu-id="11a21-184">此对象是包装器，其中包含生成 enciphered 的 XElement 和表示 IXmlDecryptor 用于解密的相应元素的类型。</span><span class="sxs-lookup"><span data-stu-id="11a21-184">This object is a wrapper which contains both the resultant enciphered XElement and the Type which represents an IXmlDecryptor which can be used to decipher the corresponding element.</span></span>

<span data-ttu-id="11a21-185">有四个内置的具体类型实现 IXmlEncryptor: CertificateXmlEncryptor、 DpapiNGXmlEncryptor、 DpapiXmlEncryptor 和 NullXmlEncryptor。</span><span class="sxs-lookup"><span data-stu-id="11a21-185">There are four built-in concrete types which implement IXmlEncryptor: CertificateXmlEncryptor, DpapiNGXmlEncryptor, DpapiXmlEncryptor, and NullXmlEncryptor.</span></span> <span data-ttu-id="11a21-186">请参阅[rest 文档的密钥加密](../implementation/key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="11a21-186">See the [key encryption at rest document](../implementation/key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) for more information.</span></span> <span data-ttu-id="11a21-187">若要更改默认密钥加密在 rest 机制整个应用程序范围，请在服务提供程序中注册的自定义单独 IXmlEncryptor。</span><span class="sxs-lookup"><span data-stu-id="11a21-187">To change the default key-encryption-at-rest mechanism application-wide, register a custom singleton IXmlEncryptor in the service provider.</span></span>

## <a name="ixmldecryptor"></a><span data-ttu-id="11a21-188">IXmlDecryptor</span><span class="sxs-lookup"><span data-stu-id="11a21-188">IXmlDecryptor</span></span>

<span data-ttu-id="11a21-189">IXmlDecryptor 接口表示一种类型，知道如何解密已通过 IXmlEncryptor enciphered 的 XElement。</span><span class="sxs-lookup"><span data-stu-id="11a21-189">The IXmlDecryptor interface represents a type that knows how to decrypt an XElement that was enciphered via an IXmlEncryptor.</span></span> <span data-ttu-id="11a21-190">它公开单个 API:</span><span class="sxs-lookup"><span data-stu-id="11a21-190">It exposes a single API:</span></span>

* <span data-ttu-id="11a21-191">解密 (XElement encryptedElement): XElement</span><span class="sxs-lookup"><span data-stu-id="11a21-191">Decrypt(XElement encryptedElement) : XElement</span></span>

<span data-ttu-id="11a21-192">解密方法撤消由 IXmlEncryptor.Encrypt 执行加密。</span><span class="sxs-lookup"><span data-stu-id="11a21-192">The Decrypt method undoes the encryption performed by IXmlEncryptor.Encrypt.</span></span> <span data-ttu-id="11a21-193">一般每个具体的 IXmlEncryptor 实现会提供相应的具体 IXmlDecryptor 实现。</span><span class="sxs-lookup"><span data-stu-id="11a21-193">Generally each concrete IXmlEncryptor implementation will have a corresponding concrete IXmlDecryptor implementation.</span></span>

<span data-ttu-id="11a21-194">实现 IXmlDecryptor 该类型应具有以下两个公共构造函数之一：</span><span class="sxs-lookup"><span data-stu-id="11a21-194">Types which implement IXmlDecryptor should have one of the following two public constructors:</span></span>

* <span data-ttu-id="11a21-195">.ctor(IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="11a21-195">.ctor(IServiceProvider)</span></span>

* <span data-ttu-id="11a21-196">.ctor()</span><span class="sxs-lookup"><span data-stu-id="11a21-196">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="11a21-197">IServiceProvider 传递到构造函数可能为 null。</span><span class="sxs-lookup"><span data-stu-id="11a21-197">The IServiceProvider passed to the constructor may be null.</span></span>

## <a name="ikeyescrowsink"></a><span data-ttu-id="11a21-198">IKeyEscrowSink</span><span class="sxs-lookup"><span data-stu-id="11a21-198">IKeyEscrowSink</span></span>

<span data-ttu-id="11a21-199">IKeyEscrowSink 接口表示可以执行托管的敏感信息的类型。</span><span class="sxs-lookup"><span data-stu-id="11a21-199">The IKeyEscrowSink interface represents a type that can perform escrow of sensitive information.</span></span> <span data-ttu-id="11a21-200">回想一下，序列化的描述符可能包含敏感信息 （如加密材料），这是什么导致了引入[IXmlEncryptor](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-ixmlencryptor)键入第一个位置。</span><span class="sxs-lookup"><span data-stu-id="11a21-200">Recall that serialized descriptors might contain sensitive information (such as cryptographic material), and this is what led to the introduction of the [IXmlEncryptor](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-ixmlencryptor) type in the first place.</span></span> <span data-ttu-id="11a21-201">但是，意外的发生，并且 keyrings 可以删除或损坏。</span><span class="sxs-lookup"><span data-stu-id="11a21-201">However, accidents happen, and keyrings can be deleted or become corrupted.</span></span>

<span data-ttu-id="11a21-202">托管接口提供了允许访问原始序列化的 XML 转换由任何配置前紧急转义阴影[IXmlEncryptor](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-ixmlencryptor)。</span><span class="sxs-lookup"><span data-stu-id="11a21-202">The escrow interface provides an emergency escape hatch, allowing access to the raw serialized XML before it is transformed by any configured [IXmlEncryptor](xref:security/data-protection/extensibility/key-management#data-protection-extensibility-key-management-ixmlencryptor).</span></span> <span data-ttu-id="11a21-203">接口公开单个 API:</span><span class="sxs-lookup"><span data-stu-id="11a21-203">The interface exposes a single API:</span></span>

* <span data-ttu-id="11a21-204">应用商店 （Guid keyId、 XElement 元素）</span><span class="sxs-lookup"><span data-stu-id="11a21-204">Store(Guid keyId, XElement element)</span></span>

<span data-ttu-id="11a21-205">负责 IKeyEscrowSink 实现以处理与业务策略一致的安全方式提供的元素。</span><span class="sxs-lookup"><span data-stu-id="11a21-205">It is up to the IKeyEscrowSink implementation to handle the provided element in a secure manner consistent with business policy.</span></span> <span data-ttu-id="11a21-206">一个可能的实现可能是为托管接收器使用已知的企业 X.509 证书的 XML 元素进行加密其中已托管证书的私钥;CertificateXmlEncryptor 类型可以对此有帮助。</span><span class="sxs-lookup"><span data-stu-id="11a21-206">One possible implementation could be for the escrow sink to encrypt the XML element using a known corporate X.509 certificate where the certificate's private key has been escrowed; the CertificateXmlEncryptor type can assist with this.</span></span> <span data-ttu-id="11a21-207">IKeyEscrowSink 实现负责，也为适当地保留提供的元素。</span><span class="sxs-lookup"><span data-stu-id="11a21-207">The IKeyEscrowSink implementation is also responsible for persisting the provided element appropriately.</span></span>

<span data-ttu-id="11a21-208">默认情况下没有托管机制启用，但服务器管理员可以[全局配置此](../configuration/machine-wide-policy.md#data-protection-configuration-machinewidepolicy)。</span><span class="sxs-lookup"><span data-stu-id="11a21-208">By default no escrow mechanism is enabled, though server administrators can [configure this globally](../configuration/machine-wide-policy.md#data-protection-configuration-machinewidepolicy).</span></span> <span data-ttu-id="11a21-209">它还可以配置以编程方式通过*IDataProtectionBuilder.AddKeyEscrowSink*方法，如下面的示例中所示。</span><span class="sxs-lookup"><span data-stu-id="11a21-209">It can also be configured programmatically via the *IDataProtectionBuilder.AddKeyEscrowSink* method as shown in the sample below.</span></span> <span data-ttu-id="11a21-210">*AddKeyEscrowSink*方法重载镜像*IServiceCollection.AddSingleton*和*IServiceCollection.AddInstance* IKeyEscrowSink 的重载，实例旨在能单一实例。</span><span class="sxs-lookup"><span data-stu-id="11a21-210">The *AddKeyEscrowSink* method overloads mirror the *IServiceCollection.AddSingleton* and *IServiceCollection.AddInstance* overloads, as IKeyEscrowSink instances are intended to be singletons.</span></span> <span data-ttu-id="11a21-211">如果注册了多个 IKeyEscrowSink 实例，每个过程中将调用密钥生成，因此密钥可以托管多个机制同时。</span><span class="sxs-lookup"><span data-stu-id="11a21-211">If multiple IKeyEscrowSink instances are registered, each one will be called during key generation, so keys can be escrowed to multiple mechanisms simultaneously.</span></span>

<span data-ttu-id="11a21-212">没有 API 从 IKeyEscrowSink 实例读取材料。</span><span class="sxs-lookup"><span data-stu-id="11a21-212">There is no API to read material from an IKeyEscrowSink instance.</span></span> <span data-ttu-id="11a21-213">这是与托管机制的设计理论一致： 它具有用于受信任的颁发机构，方便密钥材料，并且应用程序本身不是受信任颁发机构，因为它不应具有访问自己保管材料。</span><span class="sxs-lookup"><span data-stu-id="11a21-213">This is consistent with the design theory of the escrow mechanism: it's intended to make the key material accessible to a trusted authority, and since the application is itself not a trusted authority, it shouldn't have access to its own escrowed material.</span></span>

<span data-ttu-id="11a21-214">下面的示例代码演示如何创建和注册 IKeyEscrowSink 其中托管密钥，以便只有"CONTOSODomain 管理员"的成员可以恢复它们。</span><span class="sxs-lookup"><span data-stu-id="11a21-214">The following sample code demonstrates creating and registering an IKeyEscrowSink where keys are escrowed such that only members of "CONTOSODomain Admins" can recover them.</span></span>

> [!NOTE]
> <span data-ttu-id="11a21-215">若要运行此示例，你必须是在已加入域的 Windows 8 / Windows Server 2012 计算机和域控制器必须是 Windows Server 2012 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="11a21-215">To run this sample, you must be on a domain-joined Windows 8 / Windows Server 2012 machine, and the domain controller must be Windows Server 2012 or later.</span></span>

[!code-none[Main](key-management/samples/key-management-extensibility.cs)]
