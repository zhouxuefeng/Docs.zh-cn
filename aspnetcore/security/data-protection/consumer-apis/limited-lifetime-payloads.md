---
title: "限制受保护的负载的生存期"
author: rick-anderson
description: 
keywords: "ASP.NET 核心"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 000175e2-10fc-43dd-bfc2-51e004b97b44
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: 4ff13803b328c1e9dd2934c38c88b43f5798de03
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="limiting-the-lifetime-of-protected-payloads"></a>限制受保护的负载的生存期

有一些应用程序开发人员希望创建将在一段时间后过期的受保护的负载的情形。 例如，受保护的负载可能表示只应为有效一小时的密码重置令牌。 当然也可以为开发人员创建他们自己包含的嵌入的到期日期的负载格式和高级开发人员可能想要执行此操作，但对于开发人员的大多数管理这些过期时间可能变得需要很长时间。

若要简化此过程对于我们的开发人员受众，包 Microsoft.AspNetCore.DataProtection.Extensions 用于创建在一段时间后自动过期的负载包含实用工具 Api。 这些 Api 的挂起从 ITimeLimitedDataProtector 类型中移出。

## <a name="api-usage"></a>API 使用情况

ITimeLimitedDataProtector 接口是保护和取消保护的限时 / 自到期的负载的核心接口。 若要创建 ITimeLimitedDataProtector 的实例，将首先需要的正则表达式实例[IDataProtector](overview.md)构造具有特定的用途。 IDataProtector 实例可用后，调用 IDataProtector.ToTimeLimitedDataProtector 扩展方法，能获得重新保护程序内置到期功能。

ITimeLimitedDataProtector 公开以下 API 图面和扩展方法：

* CreateProtector （字符串目的）： 在于它可以用于创建 ITimeLimitedDataProtector 此 API 所显示类似于现有 IDataProtectionProvider.CreateProtector[目的链](purpose-strings.md)从根的限时保护程序。

* 保护 （字节 [] 纯文本，DateTimeOffset 到期）： byte]

* 保护 （字节 [] 纯文本、 TimeSpan 生存期）： byte]

* 保护 （字节 [] 纯文本）： byte]

* 保护 （字符串纯文本，DateTimeOffset 到期）： 字符串

* 保护 (TimeSpan 生存期中的字符串纯文本）： 字符串

* 保护 （字符串纯文本）： 字符串

除了采取仅纯文本的核心保护方法，存在一些新重载允许指定的负载的到期日期。 可以指定过期日期，为的绝对日期 （通过 DateTimeOffset) 或相对时间 （从当前系统时间，通过一个时间跨度）。 如果调用不带过期的重载，则负载假定为永远不会过期。

* 取消保护 (字节 [] protectedData，出 DateTimeOffset 到期): byte]

* 取消保护 (字节 [] protectedData): byte]

* 取消保护 (出 DateTimeOffset 过期字符串 protectedData): 字符串

* 取消保护 (字符串 protectedData): 字符串

取消保护方法返回原始的未受保护的数据。 如果尚未超过负载，绝对过期则返回为可选输出参数以及原始的未受保护数据。 如果负载已过期，则取消保护方法的所有重载将都引发 CryptographicException。

>[!WARNING]
> 不建议使用这些 Api 来保护需要长期或无限期暂留的负载。 "，我可以承受为要在一个月后永久性不可恢复的受保护负载？" 可用作一个很好的经验法则;如果问题的回答是没有然后开发人员应考虑备用 Api。

使用下面的示例[非 DI 代码路径](../configuration/non-di-scenarios.md)用于实例化数据保护系统的。 若要运行此示例，请确保先添加对 Microsoft.AspNetCore.DataProtection.Extensions 包的引用。

[!code-none[Main](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
