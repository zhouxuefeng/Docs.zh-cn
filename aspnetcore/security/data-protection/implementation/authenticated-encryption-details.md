---
title: "经过身份验证的加密的详细信息。"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 826e6d5d-9620-44e6-ad93-3b1d9969b70b
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/implementation/authenticated-encryption-details
ms.openlocfilehash: 4d0e63d7722071ab8806a217e96ee7ad7bf10286
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2017
---
# <a name="authenticated-encryption-details"></a><span data-ttu-id="da347-103">经过身份验证的加密的详细信息。</span><span class="sxs-lookup"><span data-stu-id="da347-103">Authenticated encryption details.</span></span>

<a name=data-protection-implementation-authenticated-encryption-details></a>

<span data-ttu-id="da347-104">对 IDataProtector.Protect 调用都是经过身份验证的加密操作。</span><span class="sxs-lookup"><span data-stu-id="da347-104">Calls to IDataProtector.Protect are authenticated encryption operations.</span></span> <span data-ttu-id="da347-105">保护方法提供保密性和真实性，并且它会绑定到用于此特定 IDataProtector 实例派生其根 IDataProtectionProvider 目的链。</span><span class="sxs-lookup"><span data-stu-id="da347-105">The Protect method offers both confidentiality and authenticity, and it is tied to the purpose chain that was used to derive this particular IDataProtector instance from its root IDataProtectionProvider.</span></span>

<span data-ttu-id="da347-106">IDataProtector.Protect 采用 byte [] 纯文本参数，并且生成的字节 [] 受保护的负载，其格式为下面所述。</span><span class="sxs-lookup"><span data-stu-id="da347-106">IDataProtector.Protect takes a byte[] plaintext parameter and produces a byte[] protected payload, whose format is described below.</span></span> <span data-ttu-id="da347-107">（此外还有一个扩展方法重载以采用字符串的纯文本参数和返回的字符串受保护的负载。</span><span class="sxs-lookup"><span data-stu-id="da347-107">(There is also an extension method overload which takes a string plaintext parameter and returns a string protected payload.</span></span> <span data-ttu-id="da347-108">如果使用此 API 的受保护的负载格式仍将下面结构，但它会[base64url 编码](https://tools.ietf.org/html/rfc4648#section-5)。)</span><span class="sxs-lookup"><span data-stu-id="da347-108">If this API is used the protected payload format will still have the below structure, but it will be [base64url-encoded](https://tools.ietf.org/html/rfc4648#section-5).)</span></span>

## <a name="protected-payload-format"></a><span data-ttu-id="da347-109">受保护的负载格式</span><span class="sxs-lookup"><span data-stu-id="da347-109">Protected payload format</span></span>

<span data-ttu-id="da347-110">受保护的负载格式包含三个主要组件：</span><span class="sxs-lookup"><span data-stu-id="da347-110">The protected payload format consists of three primary components:</span></span>

* <span data-ttu-id="da347-111">32 位神奇标头，它标识数据保护系统的版本。</span><span class="sxs-lookup"><span data-stu-id="da347-111">A 32-bit magic header that identifies the version of the data protection system.</span></span>

* <span data-ttu-id="da347-112">128 位密钥 id 标识该密钥用于保护此特定的负载。</span><span class="sxs-lookup"><span data-stu-id="da347-112">A 128-bit key id that identifies the key used to protect this particular payload.</span></span>

* <span data-ttu-id="da347-113">受保护的负载的剩余部分[特定于加密程序按此键封装](subkeyderivation.md#data-protection-implementation-subkey-derivation)。</span><span class="sxs-lookup"><span data-stu-id="da347-113">The remainder of the protected payload is [specific to the encryptor encapsulated by this key](subkeyderivation.md#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="da347-114">在下面的示例密钥表示 AES 256 CBC + HMACSHA256 加密器和负载进一步细分，如下所示: * 一个 128 位密钥修饰符。</span><span class="sxs-lookup"><span data-stu-id="da347-114">In the example below the key represents an AES-256-CBC + HMACSHA256 encryptor, and the payload is further subdivided as follows: * A 128-bit key modifier.</span></span> <span data-ttu-id="da347-115">* 一个 128 位初始化向量。</span><span class="sxs-lookup"><span data-stu-id="da347-115">* A 128-bit initialization vector.</span></span> <span data-ttu-id="da347-116">* AES 256 CBC 输出 48 个字节。</span><span class="sxs-lookup"><span data-stu-id="da347-116">* 48 bytes of AES-256-CBC output.</span></span> <span data-ttu-id="da347-117">* 一个 HMACSHA256 身份验证标记。</span><span class="sxs-lookup"><span data-stu-id="da347-117">* An HMACSHA256 authentication tag.</span></span>

<span data-ttu-id="da347-118">示例受保护的负载如下所示。</span><span class="sxs-lookup"><span data-stu-id="da347-118">A sample protected payload is illustrated below.</span></span>

```
09 F0 C9 F0 80 9C 81 0C 19 66 19 40 95 36 53 F8
AA FF EE 57 57 2F 40 4C 3F 7F CC 9D CC D9 32 3E
84 17 99 16 EC BA 1F 4A A1 18 45 1F 2D 13 7A 28
79 6B 86 9C F8 B7 84 F9 26 31 FC B1 86 0A F1 56
61 CF 14 58 D3 51 6F CF 36 50 85 82 08 2D 3F 73
5F B0 AD 9E 1A B2 AE 13 57 90 C8 F5 7C 95 4E 6A
8A AA 06 EF 43 CA 19 62 84 7C 11 B2 C8 71 9D AA
52 19 2E 5B 4C 1E 54 F0 55 BE 88 92 12 C1 4B 5E
52 C9 74 A0
```

<span data-ttu-id="da347-119">从上面的第一个 32 位或 4 个字节的负载格式是标识版本 (09 F0 C9 F0) 的幻标头</span><span class="sxs-lookup"><span data-stu-id="da347-119">From the payload format above the first 32 bits, or 4 bytes are the magic header identifying the version (09 F0 C9 F0)</span></span>

<span data-ttu-id="da347-120">下一步的 128 位或 16 个字节是密钥标识符 (80 9 C 81 0 C 19 66 19 40 95 36 53 F8 AA FF EE 57)</span><span class="sxs-lookup"><span data-stu-id="da347-120">The next 128 bits, or 16 bytes is the key identifier (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)</span></span>

<span data-ttu-id="da347-121">其余部分包含负载，并特定于所使用的格式。</span><span class="sxs-lookup"><span data-stu-id="da347-121">The remainder contains the payload and is specific to the format used.</span></span>

>[!WARNING]
> <span data-ttu-id="da347-122">给定的键对受保护的所有负载将都开始与同一个 20 字节 （神奇的值，密钥 id） 标头。</span><span class="sxs-lookup"><span data-stu-id="da347-122">All payloads protected to a given key will begin with the same 20-byte (magic value, key id) header.</span></span> <span data-ttu-id="da347-123">管理员可以使用这一事实以进行诊断来估计生成负载时间。</span><span class="sxs-lookup"><span data-stu-id="da347-123">Administrators can use this fact for diagnostic purposes to approximate when a payload was generated.</span></span> <span data-ttu-id="da347-124">例如，上面的负载对应于键 {0c819c80-6619-4019-9536-53f8aaffee57}。</span><span class="sxs-lookup"><span data-stu-id="da347-124">For example, the payload above corresponds to key {0c819c80-6619-4019-9536-53f8aaffee57}.</span></span> <span data-ttu-id="da347-125">如果检查密钥的存储库后找到验证此特定密钥激活日期为 2015年-01-01 和其到期日期为 2015年-03-01，然后是合乎情理假定负载 （如果未被篡改） 在该窗口中，给予生成或采用一个较小在任意一侧奶油因子。</span><span class="sxs-lookup"><span data-stu-id="da347-125">If after checking the key repository you find that this specific key's activation date was 2015-01-01 and its expiration date was 2015-03-01, then it is reasonable to assume that the payload (if not tampered with) was generated within that window, give or take a small fudge factor on either side.</span></span>
