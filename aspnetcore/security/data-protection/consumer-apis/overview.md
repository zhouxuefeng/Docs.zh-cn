---
title: "使用者 Api 概述"
author: rick-anderson
description: 
keywords: "ASP.NET 核心"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: f69beb9d-a519-43a8-857c-f6b01886a903
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/consumer-apis/overview
ms.openlocfilehash: d23a6ce50eef71f393124b9420f4ba473904d8b4
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="consumer-apis-overview"></a>使用者 Api 概述

IDataProtectionProvider 和 IDataProtector 接口都是通过该使用者使用数据保护系统的基本接口。 它们位于 Microsoft.AspNetCore.DataProtection.Abstractions 包中。

## <a name="idataprotectionprovider"></a>IDataProtectionProvider

提供程序接口表示数据保护系统的根目录。 它不能直接用于保护或取消保护数据。 相反，使用者必须通过调用的 IDataProtectionProvider.CreateProtector(purpose)，其中，目的是一个字符串，描述预期的使用者用例获得对 IDataProtector 的引用。 请参阅[目的字符串](purpose-strings.md)着眼于此参数以及如何选择适当的值的更多的信息。

## <a name="idataprotector"></a>IDataProtector

保护程序接口将返回通过调用 CreateProtector，并且此接口使用者可以用于执行保护和取消保护操作。

若要保护的数据片段，请将数据传递给保护方法。 基本接口定义的方法的将 byte []-> byte []，但还存在 （提供作为扩展方法） 将字符串转换的重载-> 字符串。 提供两种方法的安全性是相同的;开发人员应选择任何重载是最方便对其用例。 而不考虑的重载选择，则返回保护方法现在受保护 （enciphered 和防考验） 和应用程序可以将其发送到不受信任的客户端。

要取消对以前受保护的数据片段的保护，请将受保护的数据传递给取消保护方法。 (有 byte []-基于和基于字符串的重载，为开发人员方便起见。)如果受保护的负载由以前在此同一 IDataProtector 保护调用生成的取消保护方法将返回原始的未受保护的负载。 如果已被篡改或由不同 IDataProtector 生成受保护的负载的情况下，取消保护方法将引发 CryptographicException。

与不同 IDataProtector 的相同的概念将联系回目的的概念。 如果从同一根 IDataProtectionProvider 但通过不同的用途字符串 IDataProtectionProvider.CreateProtector，对的调用中生成两个 IDataProtector 实例，则它们将被视为[不同保护程序](purpose-strings.md)，和一个将无法取消保护由其他生成的负载。

## <a name="consuming-these-interfaces"></a>使用这些接口

对于 DI 感知的组件，预期的用法是组件在其构造函数采用 IDataProtectionProvider 参数，参数，并在实例化组件时，DI 系统可以自动提供此服务。

> [!NOTE]
> 某些应用程序 （如控制台应用程序或 ASP.NET 4.x 应用程序） 可能不是 DI 感知因此不能使用此处所述的机制。 有关这些方案，请查阅[非 DI 感知的情境](../configuration/non-di-scenarios.md)文档而无需通过 DI 获取 IDataProtection 提供程序实例的详细信息。

下面的示例演示三个概念：

1. [添加数据保护系统](../configuration/overview.md)到服务容器

2. 使用 DI 接收的 IDataProtectionProvider 实例和

3. 从 IDataProtectionProvider 创建 IDataProtector 并使用它来保护和取消保护数据。

[!code-csharp[Main](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

包 Microsoft.AspNetCore.DataProtection.Abstractions 包含扩展方法 IServiceProvider.GetDataProtector 为开发人员方便起见。 它封装作为单个操作同时从服务提供程序检索 IDataProtectionProvider 并调用 IDataProtectionProvider.CreateProtector。 下面的示例演示其用法。

[!code-csharp[Main](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> IDataProtectionProvider 和 IDataProtector 的实例是线程安全的多个调用方。 它旨在后一个组件获取通过 CreateProtector 调用 IDataProtector 的引用，它将该引用用于保护的多个调用和 Unprotect Unprotect.A 调用将引发 CryptographicException，如果受保护的负载不能为验证或中译解出来。 某些组件可能想要忽略错误期间取消保护操作;组件它读取身份验证 cookie 可能处理此错误和请求则将视为根本具有任何 cookie，而不使迫切地请求失败。 需要此行为的组件应专门捕获 CryptographicException，而不是忽略所有异常。
