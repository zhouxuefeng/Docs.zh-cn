---
title: "各种 API"
author: rick-anderson
description: "本文档概述了 ASP.NET 核心数据保护 ISecret 接口。"
keywords: "ASP.NET 核心，数据保护，ISecret"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 512c6ba7-88ec-47e4-a656-6b30350b34e6
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: f5d6920f9f229bd480a76c952dab30efb7d9eff5
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
# <a name="miscellaneous-apis"></a>各种 API

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> 实现的任意以下接口的类型应该是线程安全的多个调用方。

## <a name="isecret"></a>ISecret

`ISecret`接口表示一个机密的值，如加密的密钥材料。 它包含以下 API 图面：

* `Length`: `int`

* `Dispose()`: `void`

* `WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`

`WriteSecretIntoBuffer`方法填充原始机密值与提供的缓冲区。 此 API 将作为参数的缓冲区的原因而不返回`byte[]`直接是，这使调用方机会固定限制对托管的垃圾回收器的机密暴露该缓冲区对象。

`Secret`类型是的具体实现`ISecret`机密的值在进程中内存中的存储位置。 在 Windows 平台上的机密的值加密通过[CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx)。
