---
title: "在 ASP.NET 核心目的字符串"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 9d18c287-e0e6-4541-b09c-7fed45c902d9
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/consumer-apis/purpose-strings-multitenancy
ms.openlocfilehash: b25af7c1f4dd3c63734290e6ac82e2e30a030c61
ms.sourcegitcommit: e3b1726cc04e80dc28464c35259edbd3bc39a438
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2017
---
# <a name="purpose-hierarchy-and-multi-tenancy-in-aspnet-core"></a><span data-ttu-id="2ff65-103">目的层次结构和 ASP.NET Core 中的多租户</span><span class="sxs-lookup"><span data-stu-id="2ff65-103">Purpose hierarchy and multi-tenancy in ASP.NET Core</span></span>

<span data-ttu-id="2ff65-104">由于 IDataProtector 还隐式 IDataProtectionProvider，目的可以链接在一起。</span><span class="sxs-lookup"><span data-stu-id="2ff65-104">Since an IDataProtector is also implicitly an IDataProtectionProvider, purposes can be chained together.</span></span> <span data-ttu-id="2ff65-105">在此意义上提供程序。CreateProtector （["purpose1"，"purpose2"]） 相当于提供程序。CreateProtector("purpose1")。CreateProtector("purpose2")。</span><span class="sxs-lookup"><span data-stu-id="2ff65-105">In this sense provider.CreateProtector([ "purpose1", "purpose2" ]) is equivalent to provider.CreateProtector("purpose1").CreateProtector("purpose2").</span></span>

<span data-ttu-id="2ff65-106">这允许通过数据保护系统的一些有趣的层次结构关系。</span><span class="sxs-lookup"><span data-stu-id="2ff65-106">This allows for some interesting hierarchical relationships through the data protection system.</span></span> <span data-ttu-id="2ff65-107">前面的示例中[Contoso.Messaging.SecureMessage](purpose-strings.md#data-protection-contoso-purpose)，SecureMessage 组件可以调用提供程序。CreateProtector("Contoso.Messaging.SecureMessage") 后前部和缓存到私有结果`_myProvide`字段。</span><span class="sxs-lookup"><span data-stu-id="2ff65-107">In the earlier example of [Contoso.Messaging.SecureMessage](purpose-strings.md#data-protection-contoso-purpose), the SecureMessage component can call provider.CreateProtector("Contoso.Messaging.SecureMessage") once upfront and cache the result into a private `_myProvide` field.</span></span> <span data-ttu-id="2ff65-108">然后可以通过调用创建将来的保护程序`_myProvider.CreateProtector("User: username")`，并且这些保护程序会使用用于保护单个消息。</span><span class="sxs-lookup"><span data-stu-id="2ff65-108">Future protectors can then be created via calls to `_myProvider.CreateProtector("User: username")`, and these protectors would be used for securing the individual messages.</span></span>

<span data-ttu-id="2ff65-109">这可以还翻转。</span><span class="sxs-lookup"><span data-stu-id="2ff65-109">This can also be flipped.</span></span> <span data-ttu-id="2ff65-110">考虑单个逻辑应用程序可以使用其自己的身份验证和状态管理系统配置多个租户 （CMS 看起来合理） 和每个租户的主机。</span><span class="sxs-lookup"><span data-stu-id="2ff65-110">Consider a single logical application which hosts multiple tenants (a CMS seems reasonable), and each tenant can be configured with its own authentication and state management system.</span></span> <span data-ttu-id="2ff65-111">涵盖应用程序具有单一的主提供程序，并且它会调用提供程序。CreateProtector ("Tenant 1") 和提供程序。CreateProtector （"租户 2"） 为每个租户提供其自己的数据保护系统的独立的切片。</span><span class="sxs-lookup"><span data-stu-id="2ff65-111">The umbrella application has a single master provider, and it calls provider.CreateProtector("Tenant 1") and provider.CreateProtector("Tenant 2") to give each tenant its own isolated slice of the data protection system.</span></span> <span data-ttu-id="2ff65-112">租户然后无法派生自己单独的保护程序，基于其自己的需求，但无论硬他们将在尝试在不能创建其中发生冲突的保护程序与其他任何租户系统中。</span><span class="sxs-lookup"><span data-stu-id="2ff65-112">The tenants could then derive their own individual protectors based on their own needs, but no matter how hard they try they cannot create protectors which collide with any other tenant in the system.</span></span> <span data-ttu-id="2ff65-113">这以图形方式表示如下所示。</span><span class="sxs-lookup"><span data-stu-id="2ff65-113">Graphically this is represented as below.</span></span>

![多租户目的](purpose-strings-multitenancy/_static/purposes-multi-tenancy.png)

>[!WARNING]
> <span data-ttu-id="2ff65-115">这假定涵盖应用程序的控制哪些 Api 可供各个租户和租户不能在服务器上执行任意代码。</span><span class="sxs-lookup"><span data-stu-id="2ff65-115">This assumes the umbrella application controls which APIs are available to individual tenants and that tenants cannot execute arbitrary code on the server.</span></span> <span data-ttu-id="2ff65-116">如果租户可以执行任意代码，用户无法执行私有反射来中断隔离保证，或它们只是无法直接读取主密钥材料和派生任何子项他们所需。</span><span class="sxs-lookup"><span data-stu-id="2ff65-116">If a tenant can execute arbitrary code, they could perform private reflection to break the isolation guarantees, or they could just read the master keying material directly and derive whatever subkeys they desire.</span></span>

<span data-ttu-id="2ff65-117">数据保护系统实际上使用一种在其默认现成可用配置中的多租户。</span><span class="sxs-lookup"><span data-stu-id="2ff65-117">The data protection system actually uses a sort of multi-tenancy in its default out-of-the-box configuration.</span></span> <span data-ttu-id="2ff65-118">默认情况下主密钥材料存储在辅助进程帐户的用户配置文件文件夹中 （或注册表中的，为 IIS 应用程序池标识）。</span><span class="sxs-lookup"><span data-stu-id="2ff65-118">By default master keying material is stored in the worker process account's user profile folder (or the registry, for IIS application pool identities).</span></span> <span data-ttu-id="2ff65-119">但它实际上相当通常使用单个帐户来运行多个应用程序，并且因此这些应用程序将最终共享主密钥材料。</span><span class="sxs-lookup"><span data-stu-id="2ff65-119">But it is actually fairly common to use a single account to run multiple applications, and thus all these applications would end up sharing the master keying material.</span></span> <span data-ttu-id="2ff65-120">若要解决此问题，数据保护系统自动将唯一的按应用程序标识符作为整体的目的链中的第一个元素。</span><span class="sxs-lookup"><span data-stu-id="2ff65-120">To solve this, the data protection system automatically inserts a unique-per-application identifier as the first element in the overall purpose chain.</span></span> <span data-ttu-id="2ff65-121">此隐式目的提供给[将单个应用程序隔离](../configuration/overview.md#data-protection-configuration-per-app-isolation)从另一个通过有效地将每个应用程序，像在系统和保护程序创建过程中的唯一租户上去与上面的图像相同。</span><span class="sxs-lookup"><span data-stu-id="2ff65-121">This implicit purpose serves to [isolate individual applications](../configuration/overview.md#data-protection-configuration-per-app-isolation) from one another by effectively treating each application as a unique tenant within the system, and the protector creation process looks identical to the image above.</span></span>
