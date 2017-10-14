---
title: "临时数据保护提供程序"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: af6ea1d0-0d9d-41df-a870-5dda24978e2f
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: ee8dccac3ba990b110758042192779426b01fc53
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2017
---
# <a name="ephemeral-data-protection-providers"></a>临时数据保护提供程序

<a name="data-protection-implementation-key-storage-ephemeral"></a>

有一些的情形，其中应用程序所需 throwaway IDataProtectionProvider。 例如，开发人员可能只需在一次性的控制台应用程序中，进行试验或应用程序本身是暂时性 （已编写脚本或单元测试项目）。 若要支持这些方案包 Microsoft.AspNetCore.DataProtection 包括 EphemeralDataProtectionProvider 的类型。 此类型提供的 IDataProtectionProvider 其密钥的存储库保留仅在内存中和不写出到任何后备存储的基本实现。

每个 EphemeralDataProtectionProvider 实例使用其自身唯一的主密钥。 因此，如果位于 EphemeralDataProtectionProvider IDataProtector 生成受保护的负载，该负载可以仅不受等效 IDataProtector (给定相同[目的](../consumer-apis/purpose-strings.md#data-protection-consumer-apis-purposes)链) 位于同一个取得 root 权限EphemeralDataProtectionProvider 实例。

下面的示例演示如何实例化 EphemeralDataProtectionProvider 并使用它来保护和取消保护数据。

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
