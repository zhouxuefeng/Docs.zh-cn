---
title: "密钥管理和生存期"
author: rick-anderson
description: "描述密钥管理和生存期。"
keywords: "ASP.NET 核心，密钥管理，DPAPI，数据保护"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: ef7dad2a-7029-4ae5-8f06-1fbebedccaa4
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/configuration/default-settings
ms.openlocfilehash: 5ac2d80e7d1cebcbc792e1247608e67991ec36f4
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2017
---
# <a name="key-management-and-lifetime"></a><span data-ttu-id="c8299-104">密钥管理和生存期</span><span class="sxs-lookup"><span data-stu-id="c8299-104">Key management and lifetime</span></span>

<a name="data-protection-default-settings"></a>

## <a name="key-management"></a><span data-ttu-id="c8299-105">密钥管理</span><span class="sxs-lookup"><span data-stu-id="c8299-105">Key Management</span></span>

<span data-ttu-id="c8299-106">系统将尝试检测其操作环境并提供良好的零配置行为默认值。</span><span class="sxs-lookup"><span data-stu-id="c8299-106">The system tries to detect its operational environment and provide good zero-configuration behavioral defaults.</span></span> <span data-ttu-id="c8299-107">使用启发式方法是，如下所示。</span><span class="sxs-lookup"><span data-stu-id="c8299-107">The heuristic used is as follows.</span></span>

1. <span data-ttu-id="c8299-108">如果系统正在承载在 Azure 网站，密钥会保留到"%home%\asp.net\dataprotection-keys"文件夹中。</span><span class="sxs-lookup"><span data-stu-id="c8299-108">If the system is being hosted in Azure Web Sites, keys are persisted to the "%HOME%\ASP.NET\DataProtection-Keys" folder.</span></span> <span data-ttu-id="c8299-109">此文件夹由网络存储提供支持，并且在托管应用程序的所有计算机同步。</span><span class="sxs-lookup"><span data-stu-id="c8299-109">This folder is backed by network storage and is synchronized across all machines hosting the application.</span></span> <span data-ttu-id="c8299-110">在 rest 不受保护密钥。</span><span class="sxs-lookup"><span data-stu-id="c8299-110">Keys are not protected at rest.</span></span> <span data-ttu-id="c8299-111">此文件夹提供密钥环到单个部署槽位中的应用程序的所有实例。</span><span class="sxs-lookup"><span data-stu-id="c8299-111">This folder supplies the key ring to all instances of an application in a single deployment slot.</span></span> <span data-ttu-id="c8299-112">单独的部署槽，如过渡和生产时，不会共享密钥链。</span><span class="sxs-lookup"><span data-stu-id="c8299-112">Separate deployment slots, such as Staging and Production, will not share a key ring.</span></span> <span data-ttu-id="c8299-113">更换之间部署槽，如交换过渡到生产环境或使用 A / B 测试，使用数据保护任何系统不能在前一槽内使用密钥链存储的数据进行解密。</span><span class="sxs-lookup"><span data-stu-id="c8299-113">When you swap between deployment slots, for example swapping Staging to Production or using A/B testing, any system using data protection will not be able to decrypt stored data using the key ring inside the previous slot.</span></span> <span data-ttu-id="c8299-114">这将导致用户正在注销的 ASP.NET 应用程序使用标准的 ASP.NET cookie 中间件，因为它使用数据保护来保护其 cookie。</span><span class="sxs-lookup"><span data-stu-id="c8299-114">This will lead to users being logged out of an ASP.NET application that uses the standard ASP.NET cookie middleware, as it uses data protection to protect its cookies.</span></span> <span data-ttu-id="c8299-115">如果你需要独立于槽的密钥环，使用 Azure Blob 存储，Azure 密钥保管库，SQL 存储区中，之类的外部密钥环提供程序，或 Redis 缓存。</span><span class="sxs-lookup"><span data-stu-id="c8299-115">If you desire slot-independent key rings, use an external key ring provider, such as Azure Blob Storage, Azure Key Vault, a SQL store, or Redis cache.</span></span>

2. <span data-ttu-id="c8299-116">如果用户配置文件可用，密钥会保留到"%localappdata%\asp.net\dataprotection-keys"文件夹中。</span><span class="sxs-lookup"><span data-stu-id="c8299-116">If the user profile is available, keys are persisted to the "%LOCALAPPDATA%\ASP.NET\DataProtection-Keys" folder.</span></span> <span data-ttu-id="c8299-117">此外，如果操作系统是 Windows，它们将在 rest 使用 DPAPI 加密。</span><span class="sxs-lookup"><span data-stu-id="c8299-117">Additionally, if the operating system is Windows, they'll be encrypted at rest using DPAPI.</span></span>

3. <span data-ttu-id="c8299-118">如果在 IIS 中托管的应用程序，密钥会保留到 HKLM 注册表仅对辅助进程帐户是 ACLed 某个特殊的注册表项中。</span><span class="sxs-lookup"><span data-stu-id="c8299-118">If the application is hosted in IIS, keys are persisted to the HKLM registry in a special registry key that is ACLed only to the worker process account.</span></span> <span data-ttu-id="c8299-119">使用 DPAPI 对密钥静态加密。</span><span class="sxs-lookup"><span data-stu-id="c8299-119">Keys are encrypted at rest using DPAPI.</span></span>

4. <span data-ttu-id="c8299-120">如果没有这些条件与之匹配，则密钥不会保留在当前进程之外。</span><span class="sxs-lookup"><span data-stu-id="c8299-120">If none of these conditions matches, keys are not persisted outside of the current process.</span></span> <span data-ttu-id="c8299-121">关闭进程，所有生成密钥将会丢失。</span><span class="sxs-lookup"><span data-stu-id="c8299-121">When the process shuts down, all generated keys will be lost.</span></span>

<span data-ttu-id="c8299-122">开发人员可始终完全控制，并且可重写如何和密钥的存储位置。</span><span class="sxs-lookup"><span data-stu-id="c8299-122">The developer is always in full control and can override how and where keys are stored.</span></span> <span data-ttu-id="c8299-123">上面的前三个选项应很好的默认值对于大多数应用程序类似于如何 ASP.NET<machineKey>过去可以正常运行自动生成例程。</span><span class="sxs-lookup"><span data-stu-id="c8299-123">The first three options above should good defaults for most applications similar to how the ASP.NET <machineKey> auto-generation routines worked in the past.</span></span> <span data-ttu-id="c8299-124">最终，故障回复选项是真正需要开发人员指定的唯一情形[配置](overview.md)前部如果他们想要密钥持久性，但此回退才会出现在极少数情况下。</span><span class="sxs-lookup"><span data-stu-id="c8299-124">The final, fall back option is the only scenario that truly requires the developer to specify [configuration](overview.md) upfront if they want key persistence, but this fall-back would only occur in rare situations.</span></span>

>[!WARNING]
> <span data-ttu-id="c8299-125">如果开发人员重写此启发式方法，并指向特定的密钥存储库的数据保护系统，则将禁用自动加密对静止的密钥。</span><span class="sxs-lookup"><span data-stu-id="c8299-125">If the developer overrides this heuristic and points the data protection system at a specific key repository, automatic encryption of keys at rest will be disabled.</span></span> <span data-ttu-id="c8299-126">在 rest 保护可能会通过重新启用[配置](overview.md)。</span><span class="sxs-lookup"><span data-stu-id="c8299-126">At rest protection can be re-enabled via [configuration](overview.md).</span></span>

## <a name="key-lifetime"></a><span data-ttu-id="c8299-127">密钥的生存期</span><span class="sxs-lookup"><span data-stu-id="c8299-127">Key Lifetime</span></span>

<span data-ttu-id="c8299-128">默认情况下的密钥具有 90 天的生存期。</span><span class="sxs-lookup"><span data-stu-id="c8299-128">Keys by default have a 90-day lifetime.</span></span> <span data-ttu-id="c8299-129">密钥过期时，系统将自动生成新密钥，并将新的密钥设置为活动密钥。</span><span class="sxs-lookup"><span data-stu-id="c8299-129">When a key expires, the system will automatically generate a new key and set the new key as the active key.</span></span> <span data-ttu-id="c8299-130">只要已停用的密钥保留在系统上仍将能够解密使用它们保护的任何数据。</span><span class="sxs-lookup"><span data-stu-id="c8299-130">As long as retired keys remain on the system you will still be able to decrypt any data protected with them.</span></span> <span data-ttu-id="c8299-131">请参阅[密钥管理](../implementation/key-management.md#data-protection-implementation-key-management-expiration)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="c8299-131">See [key management](../implementation/key-management.md#data-protection-implementation-key-management-expiration) for more information.</span></span>

## <a name="default-algorithms"></a><span data-ttu-id="c8299-132">默认的算法</span><span class="sxs-lookup"><span data-stu-id="c8299-132">Default Algorithms</span></span>

<span data-ttu-id="c8299-133">使用的默认负载保护算法是 AES 256 CBC 机密性和 HMACSHA256 真实性。</span><span class="sxs-lookup"><span data-stu-id="c8299-133">The default payload protection algorithm used is AES-256-CBC for confidentiality and HMACSHA256 for authenticity.</span></span> <span data-ttu-id="c8299-134">512 位主密钥，回滚每隔 90 天，用于派生用于每个负载基于这些算法的两个子键。</span><span class="sxs-lookup"><span data-stu-id="c8299-134">A 512-bit master key, rolled every 90 days, is used to derive the two sub-keys used for these algorithms on a per-payload basis.</span></span> <span data-ttu-id="c8299-135">请参阅[子项派生](../implementation/subkeyderivation.md#data-protection-implementation-subkey-derivation-aad)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="c8299-135">See [subkey derivation](../implementation/subkeyderivation.md#data-protection-implementation-subkey-derivation-aad) for more information.</span></span>
