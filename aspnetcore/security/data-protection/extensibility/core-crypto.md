---
title: "核心加密可扩展性"
author: rick-anderson
description: "说明 IAuthenticatedEncryptor、 IAuthenticatedEncryptorDescriptor、 IAuthenticatedEncryptorDescriptorDeserializer 和顶级工厂。"
keywords: "ASP.NET 核心，IAuthenticatedEncryptor，IAuthenticatedEncryptorDescriptor IAuthenticatedEncryptorDescriptorDeserializer"
ms.author: riande
manager: wpickett
ms.date: 8/11/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: 8ee4e380b154db7f1736edc793b56258655ddd52
ms.sourcegitcommit: bd05f7ea8f87ad076ef6e8b704698ebcba5ca80c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2017
---
# <a name="core-cryptography-extensibility"></a><span data-ttu-id="0f13d-104">核心加密可扩展性</span><span class="sxs-lookup"><span data-stu-id="0f13d-104">Core cryptography extensibility</span></span>

<a name=data-protection-extensibility-core-crypto></a>

>[!WARNING]
> <span data-ttu-id="0f13d-105">实现的任意以下接口的类型应该是线程安全的多个调用方。</span><span class="sxs-lookup"><span data-stu-id="0f13d-105">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

<a name=data-protection-extensibility-core-crypto-iauthenticatedencryptor></a>

## <a name="iauthenticatedencryptor"></a><span data-ttu-id="0f13d-106">IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="0f13d-106">IAuthenticatedEncryptor</span></span>

<span data-ttu-id="0f13d-107">**IAuthenticatedEncryptor**接口是加密子系统的基本构建基块。</span><span class="sxs-lookup"><span data-stu-id="0f13d-107">The **IAuthenticatedEncryptor** interface is the basic building block of the cryptographic subsystem.</span></span> <span data-ttu-id="0f13d-108">通常没有一个 IAuthenticatedEncryptor 每个键，并且 IAuthenticatedEncryptor 实例包装所有加密的密钥材料和算法执行加密操作所需的信息。</span><span class="sxs-lookup"><span data-stu-id="0f13d-108">There is generally one IAuthenticatedEncryptor per key, and the IAuthenticatedEncryptor instance wraps all cryptographic key material and algorithmic information necessary to perform cryptographic operations.</span></span>

<span data-ttu-id="0f13d-109">顾名思义，类型是负责提供经过身份验证的加密和解密服务。</span><span class="sxs-lookup"><span data-stu-id="0f13d-109">As its name suggests, the type is responsible for providing authenticated encryption and decryption services.</span></span> <span data-ttu-id="0f13d-110">以下两个 Api，它公开。</span><span class="sxs-lookup"><span data-stu-id="0f13d-110">It exposes the following two APIs.</span></span>

* <span data-ttu-id="0f13d-111">解密 (ArraySegment<byte>已加密文本，ArraySegment<byte> additionalAuthenticatedData): byte]</span><span class="sxs-lookup"><span data-stu-id="0f13d-111">Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]</span></span>

* <span data-ttu-id="0f13d-112">加密 (ArraySegment<byte>纯文本，ArraySegment<byte> additionalAuthenticatedData): byte]</span><span class="sxs-lookup"><span data-stu-id="0f13d-112">Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]</span></span>

<span data-ttu-id="0f13d-113">加密方法返回的 blob，包括 enciphered 纯文本和身份验证标记。</span><span class="sxs-lookup"><span data-stu-id="0f13d-113">The Encrypt method returns a blob that includes the enciphered plaintext and an authentication tag.</span></span> <span data-ttu-id="0f13d-114">身份验证标记必须包含附加经过身份验证的数据 (AAD) 时，尽管 AAD 本身不需要从最终的负载。</span><span class="sxs-lookup"><span data-stu-id="0f13d-114">The authentication tag must encompass the additional authenticated data (AAD), though the AAD itself need not be recoverable from the final payload.</span></span> <span data-ttu-id="0f13d-115">解密方法验证身份验证标记，并返回被解码的负载。</span><span class="sxs-lookup"><span data-stu-id="0f13d-115">The Decrypt method validates the authentication tag and returns the deciphered payload.</span></span> <span data-ttu-id="0f13d-116">应为 CryptographicException homogenized （除 ArgumentNullException 和类似） 的所有失败。</span><span class="sxs-lookup"><span data-stu-id="0f13d-116">All failures (except ArgumentNullException and similar) should be homogenized to CryptographicException.</span></span>

> [!NOTE]
> <span data-ttu-id="0f13d-117">实际上，IAuthenticatedEncryptor 实例本身不需要包含密钥材料。</span><span class="sxs-lookup"><span data-stu-id="0f13d-117">The IAuthenticatedEncryptor instance itself doesn't actually need to contain the key material.</span></span> <span data-ttu-id="0f13d-118">例如，实现可以将委托到 HSM 中的所有操作。</span><span class="sxs-lookup"><span data-stu-id="0f13d-118">For example, the implementation could delegate to an HSM for all operations.</span></span>

<a name=data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory></a>
<a name=data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a><span data-ttu-id="0f13d-119">如何创建 IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="0f13d-119">How to create an IAuthenticatedEncryptor</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="0f13d-120">ASP.NET 核心 2.x</span><span class="sxs-lookup"><span data-stu-id="0f13d-120">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="0f13d-121">**IAuthenticatedEncryptorFactory**接口表示一种类型，知道如何创建[IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)实例。</span><span class="sxs-lookup"><span data-stu-id="0f13d-121">The **IAuthenticatedEncryptorFactory** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="0f13d-122">其 API 是，如下所示。</span><span class="sxs-lookup"><span data-stu-id="0f13d-122">Its API is as follows.</span></span>

* <span data-ttu-id="0f13d-123">CreateEncryptorInstance （IKey 密钥）： IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="0f13d-123">CreateEncryptorInstance(IKey key) : IAuthenticatedEncryptor</span></span>

<span data-ttu-id="0f13d-124">对于任何给定的 IKey 实例，其 CreateEncryptorInstance 方法创建的任何经过身份验证的加密程序应被视为等效，如下面的代码示例。</span><span class="sxs-lookup"><span data-stu-id="0f13d-124">For any given IKey instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorFactory instance and an IKey instance
IAuthenticatedEncryptorFactory factory = ...;
IKey key = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = factory.CreateEncryptorInstance(key);
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = factory.CreateEncryptorInstance(key);
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="0f13d-125">ASP.NET 核心 1.x</span><span class="sxs-lookup"><span data-stu-id="0f13d-125">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="0f13d-126">**IAuthenticatedEncryptorDescriptor**接口表示一种类型，知道如何创建[IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor)实例。</span><span class="sxs-lookup"><span data-stu-id="0f13d-126">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="0f13d-127">其 API 是，如下所示。</span><span class="sxs-lookup"><span data-stu-id="0f13d-127">Its API is as follows.</span></span>

* <span data-ttu-id="0f13d-128">CreateEncryptorInstance(): IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="0f13d-128">CreateEncryptorInstance() : IAuthenticatedEncryptor</span></span>

* <span data-ttu-id="0f13d-129">ExportToXml(): XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="0f13d-129">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

<span data-ttu-id="0f13d-130">如 IAuthenticatedEncryptor，假定的 IAuthenticatedEncryptorDescriptor 实例包装一个特定键。</span><span class="sxs-lookup"><span data-stu-id="0f13d-130">Like IAuthenticatedEncryptor, an instance of IAuthenticatedEncryptorDescriptor is assumed to wrap one specific key.</span></span> <span data-ttu-id="0f13d-131">这意味着，对于任何给定的 IAuthenticatedEncryptorDescriptor 实例，其 CreateEncryptorInstance 方法创建的任何经过身份验证的加密程序应被视为等效，如下面的代码示例。</span><span class="sxs-lookup"><span data-stu-id="0f13d-131">This means that for any given IAuthenticatedEncryptorDescriptor instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorDescriptor instance
IAuthenticatedEncryptorDescriptor descriptor = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = descriptor.CreateEncryptorInstance();
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = descriptor.CreateEncryptorInstance();
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

---

<a name=data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor></a>

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a><span data-ttu-id="0f13d-132">IAuthenticatedEncryptorDescriptor （ASP.NET 核心 2.x 仅）</span><span class="sxs-lookup"><span data-stu-id="0f13d-132">IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x only)</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="0f13d-133">ASP.NET 核心 2.x</span><span class="sxs-lookup"><span data-stu-id="0f13d-133">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="0f13d-134">**IAuthenticatedEncryptorDescriptor**接口表示知道如何将自身导出到 XML 的类型。</span><span class="sxs-lookup"><span data-stu-id="0f13d-134">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to export itself to XML.</span></span> <span data-ttu-id="0f13d-135">其 API 是，如下所示。</span><span class="sxs-lookup"><span data-stu-id="0f13d-135">Its API is as follows.</span></span>

* <span data-ttu-id="0f13d-136">ExportToXml(): XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="0f13d-136">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="0f13d-137">ASP.NET 核心 1.x</span><span class="sxs-lookup"><span data-stu-id="0f13d-137">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a><span data-ttu-id="0f13d-138">XML 序列化</span><span class="sxs-lookup"><span data-stu-id="0f13d-138">XML Serialization</span></span>

<span data-ttu-id="0f13d-139">IAuthenticatedEncryptor 和 IAuthenticatedEncryptorDescriptor 的主要区别是描述符知道如何创建加密程序和其提供有效的参数。</span><span class="sxs-lookup"><span data-stu-id="0f13d-139">The primary difference between IAuthenticatedEncryptor and IAuthenticatedEncryptorDescriptor is that the descriptor knows how to create the encryptor and supply it with valid arguments.</span></span> <span data-ttu-id="0f13d-140">请考虑其实现依赖于 SymmetricAlgorithm 和 KeyedHashAlgorithm IAuthenticatedEncryptor。</span><span class="sxs-lookup"><span data-stu-id="0f13d-140">Consider an IAuthenticatedEncryptor whose implementation relies on SymmetricAlgorithm and KeyedHashAlgorithm.</span></span> <span data-ttu-id="0f13d-141">加密器的作业是使用这些类型，但它不一定知道这些类型的来源，因此它不能真正写出如何重新创建本身应用程序重启时的正确说明。</span><span class="sxs-lookup"><span data-stu-id="0f13d-141">The encryptor's job is to consume these types, but it doesn't necessarily know where these types came from, so it can't really write out a proper description of how to recreate itself if the application restarts.</span></span> <span data-ttu-id="0f13d-142">描述符充当基于此较高级别。</span><span class="sxs-lookup"><span data-stu-id="0f13d-142">The descriptor acts as a higher level on top of this.</span></span> <span data-ttu-id="0f13d-143">由于描述符知道如何创建加密程序实例 （例如，它知道如何创建所需的算法），以便可以重新创建加密器实例，应用程序重置后，它可以序列化该知识库中的 XML 格式。</span><span class="sxs-lookup"><span data-stu-id="0f13d-143">Since the descriptor knows how to create the encryptor instance (e.g., it knows how to create the required algorithms), it can serialize that knowledge in XML form so that the encryptor instance can be recreated after an application reset.</span></span>

<a name=data-protection-extensibility-core-crypto-exporttoxml></a>

<span data-ttu-id="0f13d-144">通过其 ExportToXml 例程，描述符可序列化。</span><span class="sxs-lookup"><span data-stu-id="0f13d-144">The descriptor can be serialized via its ExportToXml routine.</span></span> <span data-ttu-id="0f13d-145">此例程返回 XmlSerializedDescriptorInfo 其中包含两个属性： XElement 表示形式的说明符和表示的类型[IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer)可以是用于使重新起用给定相应 XElement 此描述符。</span><span class="sxs-lookup"><span data-stu-id="0f13d-145">This routine returns an XmlSerializedDescriptorInfo which contains two properties: the XElement representation of the descriptor and the Type which represents an [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) which can be used to resurrect this descriptor given the corresponding XElement.</span></span>

<span data-ttu-id="0f13d-146">序列化的描述符可能包含敏感信息，例如加密的密钥材料。</span><span class="sxs-lookup"><span data-stu-id="0f13d-146">The serialized descriptor may contain sensitive information such as cryptographic key material.</span></span> <span data-ttu-id="0f13d-147">数据保护系统具有已持久化到存储空间之前加密信息的内置支持。</span><span class="sxs-lookup"><span data-stu-id="0f13d-147">The data protection system has built-in support for encrypting information before it's persisted to storage.</span></span> <span data-ttu-id="0f13d-148">若要充分利用此功能，描述符应标记的元素，其中包含敏感信息的属性名称"requiresEncryption"(xmlns"http://schemas.asp.net/2015/03/dataProtection")，值"true"。</span><span class="sxs-lookup"><span data-stu-id="0f13d-148">To take advantage of this, the descriptor should mark the element which contains sensitive information with the attribute name "requiresEncryption" (xmlns "http://schemas.asp.net/2015/03/dataProtection"), value "true".</span></span>

>[!TIP]
> <span data-ttu-id="0f13d-149">没有用于将此属性设置的帮助器 API。</span><span class="sxs-lookup"><span data-stu-id="0f13d-149">There's a helper API for setting this attribute.</span></span> <span data-ttu-id="0f13d-150">调用 XElement.MarkAsRequiresEncryption() 位于命名空间 Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="0f13d-150">Call the extension method XElement.MarkAsRequiresEncryption() located in namespace Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.</span></span>

<span data-ttu-id="0f13d-151">也可以是其中的序列化的描述符不包含敏感信息的情况。</span><span class="sxs-lookup"><span data-stu-id="0f13d-151">There can also be cases where the serialized descriptor doesn't contain sensitive information.</span></span> <span data-ttu-id="0f13d-152">再次考虑存储在 HSM 中的加密密钥的大小写。</span><span class="sxs-lookup"><span data-stu-id="0f13d-152">Consider again the case of a cryptographic key stored in an HSM.</span></span> <span data-ttu-id="0f13d-153">在序列化本身由于 HSM 并不会以明文形式材料时可描述符无法写出密钥材料。</span><span class="sxs-lookup"><span data-stu-id="0f13d-153">The descriptor cannot write out the key material when serializing itself since the HSM will not expose the material in plaintext form.</span></span> <span data-ttu-id="0f13d-154">相反，描述符可以编写出密钥包装的密钥 （如果 HSM 允许以这种方式导出） 或密钥的 HSM 自己唯一标识符版本。</span><span class="sxs-lookup"><span data-stu-id="0f13d-154">Instead, the descriptor might write out the key-wrapped version of the key (if the HSM allows export in this fashion) or the HSM's own unique identifier for the key.</span></span>

<a name=data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a><span data-ttu-id="0f13d-155">IAuthenticatedEncryptorDescriptorDeserializer</span><span class="sxs-lookup"><span data-stu-id="0f13d-155">IAuthenticatedEncryptorDescriptorDeserializer</span></span>

<span data-ttu-id="0f13d-156">**IAuthenticatedEncryptorDescriptorDeserializer**接口表示知道如何反序列化从 XElement IAuthenticatedEncryptorDescriptor 实例的类型。</span><span class="sxs-lookup"><span data-stu-id="0f13d-156">The **IAuthenticatedEncryptorDescriptorDeserializer** interface represents a type that knows how to deserialize an IAuthenticatedEncryptorDescriptor instance from an XElement.</span></span> <span data-ttu-id="0f13d-157">它公开一种方法：</span><span class="sxs-lookup"><span data-stu-id="0f13d-157">It exposes a single method:</span></span>

* <span data-ttu-id="0f13d-158">ImportFromXml （XElement 元素）： IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="0f13d-158">ImportFromXml(XElement element) : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="0f13d-159">ImportFromXml 方法采用返回 XElement [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml)和创建原始 IAuthenticatedEncryptorDescriptor 等效项。</span><span class="sxs-lookup"><span data-stu-id="0f13d-159">The ImportFromXml method takes the XElement that was returned by [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) and creates an equivalent of the original IAuthenticatedEncryptorDescriptor.</span></span>

<span data-ttu-id="0f13d-160">实现 IAuthenticatedEncryptorDescriptorDeserializer 该类型应具有以下两个公共构造函数之一：</span><span class="sxs-lookup"><span data-stu-id="0f13d-160">Types which implement IAuthenticatedEncryptorDescriptorDeserializer should have one of the following two public constructors:</span></span>

* <span data-ttu-id="0f13d-161">.ctor(IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="0f13d-161">.ctor(IServiceProvider)</span></span>

* <span data-ttu-id="0f13d-162">.ctor()</span><span class="sxs-lookup"><span data-stu-id="0f13d-162">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="0f13d-163">IServiceProvider 传递到构造函数可能为 null。</span><span class="sxs-lookup"><span data-stu-id="0f13d-163">The IServiceProvider passed to the constructor may be null.</span></span>

## <a name="the-top-level-factory"></a><span data-ttu-id="0f13d-164">顶级工厂</span><span class="sxs-lookup"><span data-stu-id="0f13d-164">The top-level factory</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="0f13d-165">ASP.NET 核心 2.x</span><span class="sxs-lookup"><span data-stu-id="0f13d-165">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="0f13d-166">**AlgorithmConfiguration**类表示知道如何创建该类型[IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor)实例。</span><span class="sxs-lookup"><span data-stu-id="0f13d-166">The **AlgorithmConfiguration** class represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="0f13d-167">它公开一个单一的 API。</span><span class="sxs-lookup"><span data-stu-id="0f13d-167">It exposes a single API.</span></span>

* <span data-ttu-id="0f13d-168">CreateNewDescriptor(): IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="0f13d-168">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="0f13d-169">将 AlgorithmConfiguration 视为顶级工厂。</span><span class="sxs-lookup"><span data-stu-id="0f13d-169">Think of AlgorithmConfiguration as the top-level factory.</span></span> <span data-ttu-id="0f13d-170">配置用作模板。</span><span class="sxs-lookup"><span data-stu-id="0f13d-170">The configuration serves as a template.</span></span> <span data-ttu-id="0f13d-171">它所包装算法信息 （例如，此配置生成使用 AES 128 GCM 主密钥描述符），但它不能与特定键相关联。</span><span class="sxs-lookup"><span data-stu-id="0f13d-171">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it is not yet associated with a specific key.</span></span>

<span data-ttu-id="0f13d-172">当调用 CreateNewDescriptor、 仅用于此调用中，创建新的密钥材料，并且生成新 IAuthenticatedEncryptorDescriptor 其包装此密钥材料以及所需使用材料算法信息。</span><span class="sxs-lookup"><span data-stu-id="0f13d-172">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="0f13d-173">无法在软件中创建 （和保存在内存中） 的密钥材料，它无法创建并保存在 HSM 中，依次类推。</span><span class="sxs-lookup"><span data-stu-id="0f13d-173">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="0f13d-174">关键点是 CreateNewDescriptor 任何两个调用应永远不会创建等效 IAuthenticatedEncryptorDescriptor 实例。</span><span class="sxs-lookup"><span data-stu-id="0f13d-174">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="0f13d-175">AlgorithmConfiguration 类型用作密钥创建例程的入口点如[自动密钥滚动](../implementation/key-management.md#data-protection-implementation-key-management)。</span><span class="sxs-lookup"><span data-stu-id="0f13d-175">The AlgorithmConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](../implementation/key-management.md#data-protection-implementation-key-management).</span></span> <span data-ttu-id="0f13d-176">若要更改的所有将来的键的实现，请在 KeyManagementOptions 中设置 AuthenticatedEncryptorConfiguration 属性。</span><span class="sxs-lookup"><span data-stu-id="0f13d-176">To change the implementation for all future keys, set the AuthenticatedEncryptorConfiguration property in KeyManagementOptions.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="0f13d-177">ASP.NET 核心 1.x</span><span class="sxs-lookup"><span data-stu-id="0f13d-177">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="0f13d-178">**IAuthenticatedEncryptorConfiguration**接口表示一种类型它知道如何创建[IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor)实例。</span><span class="sxs-lookup"><span data-stu-id="0f13d-178">The **IAuthenticatedEncryptorConfiguration** interface represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="0f13d-179">它公开一个单一的 API。</span><span class="sxs-lookup"><span data-stu-id="0f13d-179">It exposes a single API.</span></span>

* <span data-ttu-id="0f13d-180">CreateNewDescriptor(): IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="0f13d-180">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="0f13d-181">将 IAuthenticatedEncryptorConfiguration 视为顶级工厂。</span><span class="sxs-lookup"><span data-stu-id="0f13d-181">Think of IAuthenticatedEncryptorConfiguration as the top-level factory.</span></span> <span data-ttu-id="0f13d-182">配置用作模板。</span><span class="sxs-lookup"><span data-stu-id="0f13d-182">The configuration serves as a template.</span></span> <span data-ttu-id="0f13d-183">它所包装算法信息 （例如，此配置生成使用 AES 128 GCM 主密钥描述符），但它不能与特定键相关联。</span><span class="sxs-lookup"><span data-stu-id="0f13d-183">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it is not yet associated with a specific key.</span></span>

<span data-ttu-id="0f13d-184">当调用 CreateNewDescriptor、 仅用于此调用中，创建新的密钥材料，并且生成新 IAuthenticatedEncryptorDescriptor 其包装此密钥材料以及所需使用材料算法信息。</span><span class="sxs-lookup"><span data-stu-id="0f13d-184">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="0f13d-185">无法在软件中创建 （和保存在内存中） 的密钥材料，它无法创建并保存在 HSM 中，依次类推。</span><span class="sxs-lookup"><span data-stu-id="0f13d-185">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="0f13d-186">关键点是 CreateNewDescriptor 任何两个调用应永远不会创建等效 IAuthenticatedEncryptorDescriptor 实例。</span><span class="sxs-lookup"><span data-stu-id="0f13d-186">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="0f13d-187">IAuthenticatedEncryptorConfiguration 类型用作密钥创建例程的入口点如[自动密钥滚动](../implementation/key-management.md#data-protection-implementation-key-management)。</span><span class="sxs-lookup"><span data-stu-id="0f13d-187">The IAuthenticatedEncryptorConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](../implementation/key-management.md#data-protection-implementation-key-management).</span></span> <span data-ttu-id="0f13d-188">若要更改的所有将来的键的实现，请在服务容器中注册的单独 IAuthenticatedEncryptorConfiguration。</span><span class="sxs-lookup"><span data-stu-id="0f13d-188">To change the implementation for all future keys, register a singleton IAuthenticatedEncryptorConfiguration in the service container.</span></span>

---
