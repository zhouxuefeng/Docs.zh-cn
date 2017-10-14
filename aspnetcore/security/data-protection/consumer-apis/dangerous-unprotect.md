---
title: "正在取消保护已吊销其密钥的有效负载"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 6c4e6591-45d2-4d25-855e-062ad352d648
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 082fd69769dd0ef000b39ec148c12719d66f7aac
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2017
---
# <a name="unprotecting-payloads-whose-keys-have-been-revoked"></a>正在取消保护已吊销其密钥的有效负载

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

ASP.NET 核心数据保护 Api 主要不用于机密负载的无限期持久性。 其他技术喜欢[Windows CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx)和[Azure Rights Management](https://docs.microsoft.com/rights-management/)更适合于以下场景： 无限期存储，并且它们的相应强密钥管理功能。 也就是说，无需进行任何开发人员禁止使用 ASP.NET Core 数据保护 Api 进行长期保护的机密数据。 因此，只要键为可用，有效，IDataProtector.Unprotect 始终可以恢复现有负载从密钥链中，永远不会删除项。

但是，当开发人员尝试取消保护已吊销的密钥，保护的数据，如 IDataProtector.Unprotect 将在此情况下引发异常时，会出现问题。 这些类型的负载可以轻松地重新创建这些系统，以及在坏的情况下站点访问者可能需要重新登录，这可能是特别适用于短期或临时负载 （如身份验证令牌） 的。 但为持久化的负载，具有 Unprotect 引发可能导致不可接受数据丢失。

## <a name="ipersisteddataprotector"></a>IPersistedDataProtector

若要支持允许负载为即使在遇到吊销密钥时未受保护的方案，数据保护系统包含 IPersistedDataProtector 类型。 若要获取的 IPersistedDataProtector 实例，只需让以正常方式 IDataProtector 的实例，然后重强制转换到 IPersistedDataProtector IDataProtector。

> [!NOTE]
> 并非所有 IDataProtector 实例可以都转换为 IPersistedDataProtector。 开发人员应使用 C# 作为运算符或类似以避免运行时异常导致通过无效强制转换，并且应在准备好正确地处理失败案例。

IPersistedDataProtector 公开以下 API 图面：

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
   ```

此 API 采用 （作为字节数组） 的受保护的负载，并返回未受保护的负载。 没有任何基于字符串的重载。 Out 参数的两个如下所示。

* requiresMigration： 将设置为 true 时用于保护此负载密钥不再是活动默认密钥，例如，用于保护此负载的关键是旧，密钥滚动操作包含以来执行的位置。 调用方可能想要考虑重新保护具体取决于其业务需求的负载。

* wasRevoked： 将设置为 true 时用来保护此负载的密钥已被吊销。

>[!WARNING]
> 传递 ignoreRevocationErrors 时应格外谨慎： 设置为 true 将 DangerousUnprotect 方法。 如果调用此方法之后 wasRevoked 值为 true，然后用来保护此负载的密钥已被吊销，并且负载的真实性应被视为可疑。 在这种情况下仅继续工作时未受保护的负载，如果你有一定程度上单独保证它是可信，例如它的来自安全的数据库，而不是由不受信任的 web 客户端发送。

[!code-none[Main](dangerous-unprotect/samples/dangerous-unprotect.cs)]
