---
title: "目的字符串"
author: rick-anderson
description: 
keywords: "ASP.NET 核心"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: c96ed361-c382-4980-8933-800e740cfc38
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: cc33bcfab4945e6d6f9ca7e61edeff4d1837661a
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="purpose-strings"></a>目的字符串

<a name=data-protection-consumer-apis-purposes></a>

使用 IDataProtectionProvider 组件都必须通过唯一*目的*CreateProtector 方法参数。 目的*参数*是固有的数据保护系统中，安全的因为它提供了加密的使用者之间的隔离，即使根加密密钥是相同的。

当使用者指定目的时，目的字符串用于以及根加密密钥派生加密子项唯一到该使用者。 这将隔离应用程序中其他加密使用者的使用者： 其他组件不能读取其有效负载，并且它无法读取任何其他组件的负载。 这种隔离还会呈现不可行的整个类别的针对组件的攻击。

![目的图示例](purpose-strings/_static/purposes.png)

在上面 IDataProtector 实例 A 和 B 的图表**无法**读取对方的负载，仅自己。

目的字符串不一定是机密。 它只应是唯一其他任何功能良好组件不断将提供相同的目的字符串。

>[!TIP]
> 使用该组件使用的数据保护 Api 的命名空间和类型名称是一个很好的经验法则，如下所示此信息将不会发生冲突的做法。
>
>Contoso 编写的组件，它负责 minting 持有者令牌可能 Contoso.Security.BearerToken 用作其用途字符串。 或者-甚至更好地-它可能使用 Contoso.Security.BearerToken.v1 作为其用途字符串。 追加版本号，为将来的版本，以 Contoso.Security.BearerToken.v2 用作其用途，并且负载到位会完全独立于另一个不同的版本。

由于 CreateProtector 的目的参数是一个字符串数组，上述无法已改为指定为 ["Contoso.Security.BearerToken"，"v1"]。 这允许建立目的的层次结构，并打开与数据保护系统的多租户方案的可能性。

<a name=data-protection-contoso-purpose></a>

>[!WARNING]
> 组件不应允许不受信任的用户输入要用于目的链的输入的唯一来源。
>
>例如，考虑 Contoso.Messaging.SecureMessage 负责存储安全消息的组件。 如果安全消息的组件是为了调用 CreateProtector ([username])，则恶意用户可能创建的帐户用户名"Contoso.Security.BearerToken"在尝试获取要调用的组件 CreateProtector(["Contoso.Security.BearerToken"]) 下，因此无意中导致安全消息传送系统 mint 的负载，可以预见到身份验证令牌。
>
>消息组件的更好目的链将 CreateProtector (["Contoso.Messaging.SecureMessage"，"用户： 用户名"])，该属性提供正确的隔离。

通过提供隔离和 IDataProtectionProvider、 IDataProtector，和目的的行为如下所示：

* 对于给定的 IDataProtectionProvider 对象，CreateProtector 方法将创建唯一依赖于创建它的 IDataProtectionProvider 对象和已传递到方法的目的参数 IDataProtector 对象。

* 目的参数不得为空。 （如果目的指定为一个数组，这意味着数组不能为零长度和数组的所有元素必须都为非 null。）非空字符串目的从技术上讲允许但不建议这样做。

* 两个用途自变量是等效的当且仅当它们包含相同的字符串 （使用序号比较器） 中的顺序相同。 单一用途参数等效于相应的单个元素目的数组。

* 两个 IDataProtector 对象是等效的当且仅当从具有等效目的参数的等效 IDataProtectionProvider 对象创建。

* 对于给定的 IDataProtector 对象，对 Unprotect(protectedData) 的调用将返回原始 unprotectedData 当且仅当 protectedData: = Protect(unprotectedData) 等效 IDataProtector 对象。

> [!NOTE]
> 我们不会考虑的如果某些组件有意选择已知与另一个组件发生冲突的目的字符串。 此类组件将考虑恶意内容，实质上是和此系统不应以中，恶意代码已在运行的工作进程内提供的安全保证。
