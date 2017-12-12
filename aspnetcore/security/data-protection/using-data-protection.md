---
title: "开始使用数据保护 Api"
author: rick-anderson
description: "本文档说明如何使用 ASP.NET Core 数据保护 Api 用于保护和取消保护应用中的数据。"
keywords: "ASP.NET 核心，数据保护"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 39b7a73c-29d4-4137-b311-49529adcf3cb
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 535bfaf2077cda91c27e7d0d68b9804e8596070e
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
# <a name="getting-started-with-the-data-protection-apis"></a>开始使用数据保护 Api

<a name="security-data-protection-getting-started"></a>

在其最简单、 保护数据包括以下步骤：

1. 从数据保护提供程序创建一个数据保护程序。

2. 调用`Protect`与你想要保护的数据的方法。

3. 调用`Unprotect`想要将返回转换为纯文本的数据的方法。

大多数框架和应用模型，如 ASP.NET 或 SignalR，已配置数据保护系统，并将其添加到通过依赖关系注入访问的服务容器。 下面的示例演示配置依赖关系注入的服务容器和注册数据保护堆栈、 接收通过 DI 数据保护提供程序、 创建保护程序和保护然后正在取消保护数据

[!code-csharp[Main](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

创建一个保护程序时必须提供一个或多个[目的字符串](consumer-apis/purpose-strings.md)。 目的字符串都提供了使用者之间的隔离。 例如，使用"green"的目的字符串创建一个保护程序将无法取消保护数据的目的为"紫色"提供的保护程序。

>[!TIP]
> 实例`IDataProtectionProvider`和`IDataProtector`是线程安全的多个调用方。 它是预期，后一个组件获取的引用`IDataProtector`通过调用`CreateProtector`，它将使用该引用，以便多个调用`Protect`和`Unprotect`。
>
>调用`Unprotect`将引发 CryptographicException，如果无法验证或中译解出来的受保护的负载。 某些组件可能想要忽略错误期间取消保护操作;组件它读取身份验证 cookie 可能处理此错误和请求则将视为根本具有任何 cookie，而不使迫切地请求失败。 需要此行为的组件应专门捕获 CryptographicException，而不是忽略所有异常。
