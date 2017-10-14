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
# <a name="getting-started-with-the-data-protection-apis"></a><span data-ttu-id="afb54-103">开始使用数据保护 Api</span><span class="sxs-lookup"><span data-stu-id="afb54-103">Getting Started with the Data Protection APIs</span></span>

<a name="security-data-protection-getting-started"></a>

<span data-ttu-id="afb54-104">在其最简单的保护数据包括以下步骤：</span><span class="sxs-lookup"><span data-stu-id="afb54-104">At its simplest protecting data consists of the following steps:</span></span>

1. <span data-ttu-id="afb54-105">从数据保护提供程序创建一个数据保护程序。</span><span class="sxs-lookup"><span data-stu-id="afb54-105">Create a data protector from a data protection provider.</span></span>

2. <span data-ttu-id="afb54-106">调用具有你想要保护的数据的保护方法。</span><span class="sxs-lookup"><span data-stu-id="afb54-106">Call the Protect method with the data you want to protect.</span></span>

3. <span data-ttu-id="afb54-107">调用具有你想要将返回转换为纯文本数据的取消保护方法。</span><span class="sxs-lookup"><span data-stu-id="afb54-107">Call the Unprotect method with the data you want to turn back into plain text.</span></span>

<span data-ttu-id="afb54-108">大多数框架，例如 ASP.NET 或 SignalR 已配置数据保护系统，并将其添加到通过依赖关系注入访问的服务容器。</span><span class="sxs-lookup"><span data-stu-id="afb54-108">Most frameworks such as ASP.NET or SignalR already configure the data protection system and add it to a service container you access via dependency injection.</span></span> <span data-ttu-id="afb54-109">下面的示例演示配置依赖关系注入的服务容器和注册数据保护堆栈、 接收通过 DI 数据保护提供程序、 创建保护程序和保护然后正在取消保护数据</span><span class="sxs-lookup"><span data-stu-id="afb54-109">The following sample demonstrates configuring a service container for dependency injection and registering the data protection stack, receiving the data protection provider via DI, creating a protector and protecting then unprotecting data</span></span>

[!code-csharp[Main](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

<span data-ttu-id="afb54-110">创建一个保护程序时必须提供一个或多个[目的字符串](consumer-apis/purpose-strings.md)。</span><span class="sxs-lookup"><span data-stu-id="afb54-110">When you create a protector you must provide one or more [Purpose Strings](consumer-apis/purpose-strings.md).</span></span> <span data-ttu-id="afb54-111">目的字符串都提供了使用者之间的隔离，例如"green"的目的字符串创建一个保护程序将不能取消保护数据的目的为"紫色"提供的保护程序。</span><span class="sxs-lookup"><span data-stu-id="afb54-111">A purpose string provides isolation between consumers, for example a protector created with a purpose string of "green" would not be able to unprotect data provided by a protector with a purpose of "purple".</span></span>

>[!TIP]
> <span data-ttu-id="afb54-112">IDataProtectionProvider 和 IDataProtector 的实例是线程安全的多个调用方。</span><span class="sxs-lookup"><span data-stu-id="afb54-112">Instances of IDataProtectionProvider and IDataProtector are thread-safe for multiple callers.</span></span> <span data-ttu-id="afb54-113">其目的是，在组件获取通过 CreateProtector 调用 IDataProtector 的引用，它将使用该引用，以便保护和取消保护多个调用。</span><span class="sxs-lookup"><span data-stu-id="afb54-113">It is intended that once a component gets a reference to an IDataProtector via a call to CreateProtector, it will use that reference for multiple calls to Protect and Unprotect.</span></span>
>
><span data-ttu-id="afb54-114">如果无法验证或中译解出来的受保护的负载，撤消对的调用将引发 CryptographicException。</span><span class="sxs-lookup"><span data-stu-id="afb54-114">A call to Unprotect will throw CryptographicException if the protected payload cannot be verified or deciphered.</span></span> <span data-ttu-id="afb54-115">某些组件可能想要忽略错误期间取消保护操作;组件它读取身份验证 cookie 可能处理此错误和请求则将视为根本具有任何 cookie，而不使迫切地请求失败。</span><span class="sxs-lookup"><span data-stu-id="afb54-115">Some components may wish to ignore errors during unprotect operations; a component which reads authentication cookies might handle this error and treat the request as if it had no cookie at all rather than fail the request outright.</span></span> <span data-ttu-id="afb54-116">需要此行为的组件应专门捕获 CryptographicException，而不是忽略所有异常。</span><span class="sxs-lookup"><span data-stu-id="afb54-116">Components which want this behavior should specifically catch CryptographicException instead of swallowing all exceptions.</span></span>
