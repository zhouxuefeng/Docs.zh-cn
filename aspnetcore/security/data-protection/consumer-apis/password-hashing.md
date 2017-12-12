---
title: "密码哈希"
author: rick-anderson
description: "本文档说明如何使用 ASP.NET Core 数据保护 Api 的密码执行哈希。"
keywords: "ASP.NET 核心，数据保护、 密码哈希"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 982a1eb2-1e6f-4909-896f-82784364472d
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: da9f505f58f18f7ab3b93753bae079eb976b3939
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
# <a name="password-hashing"></a>密码哈希

基本数据保护代码包括包*Microsoft.AspNetCore.Cryptography.KeyDerivation*其中包含加密密钥派生函数。 此包是一个独立组件，并不依赖于数据保护系统的其余部分。 可以完全单独使用它。 源共存于基本为方便起见数据保护代码。

包当前提供一种方法`KeyDerivation.Pbkdf2`这样，哈希密码使用[PBKDF2 算法](https://tools.ietf.org/html/rfc2898#section-5.2)。 此 API 已非常类似于.NET Framework 的现有[Rfc2898DeriveBytes 类型](https://docs.microsoft.com/dotnet/api/system.security.cryptography.rfc2898derivebytes)，但有三个重要区别：

1. `KeyDerivation.Pbkdf2`方法支持使用多个 PRFs (当前`HMACSHA1`， `HMACSHA256`，和`HMACSHA512`)，而`Rfc2898DeriveBytes`类型仅支持`HMACSHA1`。

2. `KeyDerivation.Pbkdf2`方法检测到当前操作系统，然后尝试选择最优化的实现的例程，提供更好的性能，在某些情况下，选择。 (在 Windows 8 中，它提供约 10 倍的吞吐量`Rfc2898DeriveBytes`。)

3. `KeyDerivation.Pbkdf2`方法要求调用方指定的所有参数 (salt，PRF 和迭代计数)。 `Rfc2898DeriveBytes`类型提供的这些默认值。

[!code-csharp[Main](password-hashing/samples/passwordhasher.cs)]

ASP.NET 核心标识，请参阅源代码`PasswordHasher`现实世界的类型用例。
