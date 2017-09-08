---
title: "临时数据保护提供程序"
author: rick-anderson
description: 
keywords: "ASP.NET 核心"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: af6ea1d0-0d9d-41df-a870-5dda24978e2f
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: 6b564f082eefad993ac938407e0f3b657d83fb52
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="ephemeral-data-protection-providers"></a><span data-ttu-id="1c70a-103">临时数据保护提供程序</span><span class="sxs-lookup"><span data-stu-id="1c70a-103">Ephemeral data protection providers</span></span>

<a name=data-protection-implementation-key-storage-ephemeral></a>

<span data-ttu-id="1c70a-104">有一些的情形，其中应用程序所需 throwaway IDataProtectionProvider。</span><span class="sxs-lookup"><span data-stu-id="1c70a-104">There are scenarios where an application needs a throwaway IDataProtectionProvider.</span></span> <span data-ttu-id="1c70a-105">例如，开发人员可能只需在一次性的控制台应用程序中，进行试验或应用程序本身是暂时性 （已编写脚本或单元测试项目）。</span><span class="sxs-lookup"><span data-stu-id="1c70a-105">For example, the developer might just be experimenting in a one-off console application, or the application itself is transient (it's scripted or a unit test project).</span></span> <span data-ttu-id="1c70a-106">若要支持这些方案包 Microsoft.AspNetCore.DataProtection 包括 EphemeralDataProtectionProvider 的类型。</span><span class="sxs-lookup"><span data-stu-id="1c70a-106">To support these scenarios the package Microsoft.AspNetCore.DataProtection includes a type EphemeralDataProtectionProvider.</span></span> <span data-ttu-id="1c70a-107">此类型提供的 IDataProtectionProvider 其密钥的存储库保留仅在内存中和不写出到任何后备存储的基本实现。</span><span class="sxs-lookup"><span data-stu-id="1c70a-107">This type provides a basic implementation of IDataProtectionProvider whose key repository is held solely in-memory and isn't written out to any backing store.</span></span>

<span data-ttu-id="1c70a-108">每个 EphemeralDataProtectionProvider 实例使用其自身唯一的主密钥。</span><span class="sxs-lookup"><span data-stu-id="1c70a-108">Each instance of EphemeralDataProtectionProvider uses its own unique master key.</span></span> <span data-ttu-id="1c70a-109">因此，如果位于 EphemeralDataProtectionProvider IDataProtector 生成受保护的负载，该负载可以仅不受等效 IDataProtector (给定相同[目的](../consumer-apis/purpose-strings.md#data-protection-consumer-apis-purposes)链) 位于同一个取得 root 权限EphemeralDataProtectionProvider 实例。</span><span class="sxs-lookup"><span data-stu-id="1c70a-109">Therefore, if an IDataProtector rooted at an EphemeralDataProtectionProvider generates a protected payload, that payload can only be unprotected by an equivalent IDataProtector (given the same [purpose](../consumer-apis/purpose-strings.md#data-protection-consumer-apis-purposes) chain) rooted at the same EphemeralDataProtectionProvider instance.</span></span>

<span data-ttu-id="1c70a-110">下面的示例演示如何实例化 EphemeralDataProtectionProvider 并使用它来保护和取消保护数据。</span><span class="sxs-lookup"><span data-stu-id="1c70a-110">The following sample demonstrates instantiating an EphemeralDataProtectionProvider and using it to protect and unprotect data.</span></span>

```none
using System;
   using Microsoft.AspNetCore.DataProtection;

   public class Program
   {
       public static void Main(string[] args)
       {
           const string purpose = "Ephemeral.App.v1";

           // create an ephemeral provider and demonstrate that it can round-trip a payload
           var provider = new EphemeralDataProtectionProvider();
           var protector = provider.CreateProtector(purpose);
           Console.Write("Enter input: ");
           string input = Console.ReadLine();

           // protect the payload
           string protectedPayload = protector.Protect(input);
           Console.WriteLine($"Protect returned: {protectedPayload}");

           // unprotect the payload
           string unprotectedPayload = protector.Unprotect(protectedPayload);
           Console.WriteLine($"Unprotect returned: {unprotectedPayload}");

           // if I create a new ephemeral provider, it won't be able to unprotect existing
           // payloads, even if I specify the same purpose
           provider = new EphemeralDataProtectionProvider();
           protector = provider.CreateProtector(purpose);
           unprotectedPayload = protector.Unprotect(protectedPayload); // THROWS
       }
   }

   /*
    * SAMPLE OUTPUT
    *
    * Enter input: Hello!
    * Protect returned: CfDJ8AAAAAAAAAAAAAAAAAAAAA...uGoxWLjGKtm1SkNACQ
    * Unprotect returned: Hello!
    * << throws CryptographicException >>
    */
   ```
