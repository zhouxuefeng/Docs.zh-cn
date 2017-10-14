---
title: "开始使用数据保护 Api"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 39b7a73c-29d4-4137-b311-49529adcf3cb
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 9489b55b1de626b77bcbe21cb9678e561b559099
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2017
---
# <a name="getting-started-with-the-data-protection-apis"></a>开始使用数据保护 Api

<a name="security-data-protection-getting-started"></a>

在其最简单的保护数据包括以下步骤：

1. 从数据保护提供程序创建一个数据保护程序。

2. 调用具有你想要保护的数据的保护方法。

3. 调用具有你想要将返回转换为纯文本数据的取消保护方法。

大多数框架，例如 ASP.NET 或 SignalR 已配置数据保护系统，并将其添加到通过依赖关系注入访问的服务容器。 下面的示例演示配置依赖关系注入的服务容器和注册数据保护堆栈、 接收通过 DI 数据保护提供程序、 创建保护程序和保护然后正在取消保护数据

[!code-csharp[Main](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

创建一个保护程序时必须提供一个或多个[目的字符串](consumer-apis/purpose-strings.md)。 目的字符串都提供了使用者之间的隔离，例如"green"的目的字符串创建一个保护程序将不能取消保护数据的目的为"紫色"提供的保护程序。

>[!TIP]
> IDataProtectionProvider 和 IDataProtector 的实例是线程安全的多个调用方。 其目的是，在组件获取通过 CreateProtector 调用 IDataProtector 的引用，它将使用该引用，以便保护和取消保护多个调用。
>
>如果无法验证或中译解出来的受保护的负载，撤消对的调用将引发 CryptographicException。 某些组件可能想要忽略错误期间取消保护操作;组件它读取身份验证 cookie 可能处理此错误和请求则将视为根本具有任何 cookie，而不使迫切地请求失败。 需要此行为的组件应专门捕获 CryptographicException，而不是忽略所有异常。
