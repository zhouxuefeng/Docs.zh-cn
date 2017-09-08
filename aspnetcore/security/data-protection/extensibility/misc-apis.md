---
title: "各种 API"
author: rick-anderson
description: 
keywords: "ASP.NET 核心"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 512c6ba7-88ec-47e4-a656-6b30350b34e6
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 541dd721a00495632f0d633577b55933c9be03fa
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="miscellaneous-apis"></a>各种 API

<a name=data-protection-extensibility-mics-apis></a>

>[!WARNING]
> 实现的任意以下接口的类型应该是线程安全的多个调用方。

## <a name="isecret"></a>ISecret

ISecret 接口表示一个机密的值，如加密的密钥材料。 它包含以下 API 图面。

* 长度： int

* Dispose （): void

* WriteSecretIntoBuffer (ArraySegment<byte>缓冲区): void

WriteSecretIntoBuffer 方法填充原始机密值与提供的缓冲区。 此 API 将缓冲区作为参数，而不是返回 byte [] 直接，这使调用方机会固定的缓冲区对象，限制对托管的垃圾回收器的机密暴露原因。

密钥类型是的 ISecret 机密的值在进程中内存中的存储位置的具体实现。 在 Windows 平台上的机密的值加密通过[CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx)。
