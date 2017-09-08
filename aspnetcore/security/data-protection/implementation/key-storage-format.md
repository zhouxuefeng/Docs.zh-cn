---
title: "密钥存储格式"
author: tdykstra
description: 
keywords: "ASP.NET 核心"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: e8996478-f7bf-4b58-bab4-7fdb5d8556c5
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: e761eaa406a9691e3fa36881d42c1a0c1bd8a206
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="key-storage-format"></a><span data-ttu-id="929e5-103">密钥存储格式</span><span class="sxs-lookup"><span data-stu-id="929e5-103">Key Storage Format</span></span>

<a name=data-protection-implementation-key-storage-format></a>

<span data-ttu-id="929e5-104">对象存储在 XML 表示形式中的其余部分。</span><span class="sxs-lookup"><span data-stu-id="929e5-104">Objects are stored at rest in XML representation.</span></span> <span data-ttu-id="929e5-105">密钥存储的默认目录为 %localappdata%\asp.net\dataprotection-keys\。</span><span class="sxs-lookup"><span data-stu-id="929e5-105">The default directory for key storage is %LOCALAPPDATA%\ASP.NET\DataProtection-Keys\.</span></span>

## <a name="the-key-element"></a><span data-ttu-id="929e5-106">\<密钥 > 元素</span><span class="sxs-lookup"><span data-stu-id="929e5-106">The \<key> element</span></span>

<span data-ttu-id="929e5-107">注册表项存在作为密钥存储库中的顶级对象。</span><span class="sxs-lookup"><span data-stu-id="929e5-107">Keys exist as top-level objects in the key repository.</span></span> <span data-ttu-id="929e5-108">按照约定密钥具有 filename**密钥-{guid}.xml**，其中 {guid} 是密钥的 id。</span><span class="sxs-lookup"><span data-stu-id="929e5-108">By convention keys have the filename **key-{guid}.xml**, where {guid} is the id of the key.</span></span> <span data-ttu-id="929e5-109">每个此类文件包含一个密钥。</span><span class="sxs-lookup"><span data-stu-id="929e5-109">Each such file contains a single key.</span></span> <span data-ttu-id="929e5-110">文件的格式如下所示。</span><span class="sxs-lookup"><span data-stu-id="929e5-110">The format of the file is as follows.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<key id="80732141-ec8f-4b80-af9c-c4d2d1ff8901" version="1">
  <creationDate>2015-03-19T23:32:02.3949887Z</creationDate>
  <activationDate>2015-03-19T23:32:02.3839429Z</activationDate>
  <expirationDate>2015-06-17T23:32:02.3839429Z</expirationDate>
  <descriptor deserializerType="{deserializerType}">
    <descriptor>
      <encryption algorithm="AES_256_CBC" />
      <validation algorithm="HMACSHA256" />
      <enc:encryptedSecret decryptorType="{decryptorType}" xmlns:enc="...">
        <encryptedKey>
          <!-- This key is encrypted with Windows DPAPI. -->
          <value>AQAAANCM...8/zeP8lcwAg==</value>
        </encryptedKey>
      </enc:encryptedSecret>
    </descriptor>
  </descriptor>
</key>
```

<span data-ttu-id="929e5-111">\<密钥 > 元素包含以下属性和子元素：</span><span class="sxs-lookup"><span data-stu-id="929e5-111">The \<key> element contains the following attributes and child elements:</span></span>

* <span data-ttu-id="929e5-112">密钥 id。</span><span class="sxs-lookup"><span data-stu-id="929e5-112">The key id.</span></span> <span data-ttu-id="929e5-113">此值是被看作是授权;文件名是只需以方便人们阅读过去。</span><span class="sxs-lookup"><span data-stu-id="929e5-113">This value is treated as authoritative; the filename is simply a nicety for human readability.</span></span>

* <span data-ttu-id="929e5-114">版本\<密钥 > 元素，当前固定为 1。</span><span class="sxs-lookup"><span data-stu-id="929e5-114">The version of the \<key> element, currently fixed at 1.</span></span>

* <span data-ttu-id="929e5-115">密钥的创建、 激活和到期日期。</span><span class="sxs-lookup"><span data-stu-id="929e5-115">The key's creation, activation, and expiration dates.</span></span>

* <span data-ttu-id="929e5-116">A\<描述符 > 元素，它包含有关该注册表项中包含的经过身份验证的加密实现的信息。</span><span class="sxs-lookup"><span data-stu-id="929e5-116">A \<descriptor> element, which contains information on the authenticated encryption implementation contained within this key.</span></span>

<span data-ttu-id="929e5-117">在上面的示例中，密钥的 id 是 {80732141-ec8f-4b80-af9c-c4d2d1ff8901}，它已创建并将在 2015 年 3 月 19 日激活且它具有 90 天的生存期。</span><span class="sxs-lookup"><span data-stu-id="929e5-117">In the above example, the key's id is {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, it was created and activated on March 19, 2015, and it has a lifetime of 90 days.</span></span> <span data-ttu-id="929e5-118">（有时激活日期可能略有如此示例所示的创建日期之前。</span><span class="sxs-lookup"><span data-stu-id="929e5-118">(Occasionally the activation date might be slightly before the creation date as in this example.</span></span> <span data-ttu-id="929e5-119">这是由于 n 它在 Api 工作和无碍在实践中的方式。）</span><span class="sxs-lookup"><span data-stu-id="929e5-119">This is due to a nit in how the APIs work and is harmless in practice.)</span></span>

## <a name="the-descriptor-element"></a><span data-ttu-id="929e5-120">\<描述符 > 元素</span><span class="sxs-lookup"><span data-stu-id="929e5-120">The \<descriptor> element</span></span>

<span data-ttu-id="929e5-121">外部\<描述符 > 元素包含属性 deserializerType，这是实现 IAuthenticatedEncryptorDescriptorDeserializer 的类型的程序集限定名称。</span><span class="sxs-lookup"><span data-stu-id="929e5-121">The outer \<descriptor> element contains an attribute deserializerType, which is the assembly-qualified name of a type which implements IAuthenticatedEncryptorDescriptorDeserializer.</span></span> <span data-ttu-id="929e5-122">此类型是负责读取内部\<描述符 > 元素和分析中包含的信息。</span><span class="sxs-lookup"><span data-stu-id="929e5-122">This type is responsible for reading the inner \<descriptor> element and for parsing the information contained within.</span></span>

<span data-ttu-id="929e5-123">特定的格式\<描述符 > 元素取决于经过身份验证的加密程序实现的键，封装和每个反序列化程序类型所预期的此略有不同的格式。</span><span class="sxs-lookup"><span data-stu-id="929e5-123">The particular format of the \<descriptor> element depends on the authenticated encryptor implementation encapsulated by the key, and each deserializer type expects a slightly different format for this.</span></span> <span data-ttu-id="929e5-124">一般情况下，不过，此元素将包含算法的信息 (名称、 类型，Oid，或类似) 和机密密钥材料。</span><span class="sxs-lookup"><span data-stu-id="929e5-124">In general, though, this element will contain algorithmic information (names, types, OIDs, or similar) and secret key material.</span></span> <span data-ttu-id="929e5-125">在上面的示例中，该描述符指定此密钥包装 AES 256 CBC 加密 + HMACSHA256 验证。</span><span class="sxs-lookup"><span data-stu-id="929e5-125">In the above example, the descriptor specifies that this key wraps AES-256-CBC encryption + HMACSHA256 validation.</span></span>

## <a name="the-encryptedsecret-element"></a><span data-ttu-id="929e5-126">\<EncryptedSecret > 元素</span><span class="sxs-lookup"><span data-stu-id="929e5-126">The \<encryptedSecret> element</span></span>

<span data-ttu-id="929e5-127"><encryptedSecret>元素它包含机密的密钥材料的加密的表单可能出现如果[启用了加密对静止的机密](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest)。</span><span class="sxs-lookup"><span data-stu-id="929e5-127">An <encryptedSecret> element which contains the encrypted form of the secret key material may be present if [encryption of secrets at rest is enabled](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest).</span></span> <span data-ttu-id="929e5-128">属性 decryptorType 将实现 IXmlDecryptor 的类型的程序集限定名称。</span><span class="sxs-lookup"><span data-stu-id="929e5-128">The attribute decryptorType will be the assembly-qualified name of a type which implements IXmlDecryptor.</span></span> <span data-ttu-id="929e5-129">此类型是负责读取内部<encryptedKey>元素和解密以恢复原始的纯文本。</span><span class="sxs-lookup"><span data-stu-id="929e5-129">This type is responsible for reading the inner <encryptedKey> element and decrypting it to recover the original plaintext.</span></span>

<span data-ttu-id="929e5-130">与\<描述符 >，特定的格式<encryptedSecret>元素取决于正在使用的静态加密机制。</span><span class="sxs-lookup"><span data-stu-id="929e5-130">As with \<descriptor>, the particular format of the <encryptedSecret> element depends on the at-rest encryption mechanism in use.</span></span> <span data-ttu-id="929e5-131">在上面的示例中，每个注释中使用 Windows DPAPI 对主密钥进行加密。</span><span class="sxs-lookup"><span data-stu-id="929e5-131">In the above example, the master key is encrypted using Windows DPAPI per the comment.</span></span>

## <a name="the-revocation-element"></a><span data-ttu-id="929e5-132">\<吊销 > 元素</span><span class="sxs-lookup"><span data-stu-id="929e5-132">The \<revocation> element</span></span>

<span data-ttu-id="929e5-133">吊销存在作为密钥存储库中的顶级对象。</span><span class="sxs-lookup"><span data-stu-id="929e5-133">Revocations exist as top-level objects in the key repository.</span></span> <span data-ttu-id="929e5-134">按照约定吊销具有 filename**吊销-{时间戳}.xml** （适用于特定日期之前撤消所有键） 或**吊销-{guid}.xml** （对于都撤消特定键）。</span><span class="sxs-lookup"><span data-stu-id="929e5-134">By convention revocations have the filename **revocation-{timestamp}.xml** (for revoking all keys before a specific date) or **revocation-{guid}.xml** (for revoking a specific key).</span></span> <span data-ttu-id="929e5-135">每个文件包含单个\<吊销 > 元素。</span><span class="sxs-lookup"><span data-stu-id="929e5-135">Each file contains a single \<revocation> element.</span></span>

<span data-ttu-id="929e5-136">对于各个密钥吊销，该文件的内容将如下。</span><span class="sxs-lookup"><span data-stu-id="929e5-136">For revocations of individual keys, the file contents will be as below.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="929e5-137">在这种情况下，只有指定的密钥已吊销。</span><span class="sxs-lookup"><span data-stu-id="929e5-137">In this case, only the specified key is revoked.</span></span> <span data-ttu-id="929e5-138">如果密钥 id 为"*"，但是，如以下示例中，其创建日期是在指定的吊销日期之前的所有键都被都吊销。</span><span class="sxs-lookup"><span data-stu-id="929e5-138">If the key id is "*", however, as in the below example, all keys whose creation date is prior to the specified revocation date are revoked.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="929e5-139">\<原因 > 由系统永远不会读取元素。</span><span class="sxs-lookup"><span data-stu-id="929e5-139">The \<reason> element is never read by the system.</span></span> <span data-ttu-id="929e5-140">它是只需存储吊销的用户可读的原因方便位置。</span><span class="sxs-lookup"><span data-stu-id="929e5-140">It is simply a convenient place to store a human-readable reason for revocation.</span></span>
