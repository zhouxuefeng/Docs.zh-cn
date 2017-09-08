---
title: "密钥不可变性和更改设置"
author: rick-anderson
description: 
keywords: "ASP.NET 核心"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: fc911ae3-feca-4ffe-8b43-362bc632fe04
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 3afce8f84ebe3b709ea169c7db27f99829f157ab
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="key-immutability-and-changing-settings"></a>密钥不可变性和更改设置

一旦对象持久保留到后备存储，其表示形式是永久固定的。 新的数据可以添加到后备存储，但可以永远不会更改现有数据。 此行为的主要目的是为了防止数据损坏。

此行为的一个后果是，一旦给后备存储编写了一个键，不可变。 决不能更改其创建、 激活和到期日期，但它可以通过使用 IKeyManager 吊销。 此外，其基础的算法信息、 主密钥材料和加密 rest 属性也是不可变。

如果开发人员更改会影响密钥保持任何设置，这些更改将不会起作用之前都会生成一个密钥，通过显式调用 IKeyManager.CreateNewKey 还是通过数据保护系统的自己的下一步时间[自动密钥生成](key-management.md#data-protection-implementation-key-management)行为。 影响密钥持久性的设置如下所示：

* [默认密钥生存时间](key-management.md#data-protection-implementation-key-management)

* [在 rest 机制密钥加密](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest)

* [在项包含的算法信息](../configuration/overview.md#data-protection-changing-algorithms)

如果你需要这些设置，以便早于下一步的自动密钥滚动时间启动，请考虑进行显式调用 IKeyManager.CreateNewKey 强制创建新的密钥。 请记得提供显式激活日期 （{现在 + 2 天} 是一个很好的经验法则以允许更改传播的时间） 和调用中的到期日期。

>[!TIP]
> 接触存储库中的所有应用程序应使用 IDataProtectionBuilder 扩展方法指定相同的设置，否则保存的密钥的属性将为依赖于特定应用程序调用的密钥生成例程.
