---
title: "ASP.NET Core 中的数据保护"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 1f402da8-1052-4970-9835-9f9f16a02dbc
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/index
ms.openlocfilehash: 39f2ca96f8542de033274ea957b5c7736948c981
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="data-protection-in-aspnet-core-consumer-apis-configuration-extensibility-apis-and-implementation"></a><span data-ttu-id="731a8-103">ASP.NET Core 中的数据保护：使用者 API、配置、扩展性 API 和实现</span><span class="sxs-lookup"><span data-stu-id="731a8-103">Data Protection in ASP.NET Core: Consumer APIs, configuration, extensibility APIs and implementation</span></span>

* [<span data-ttu-id="731a8-104">数据保护简介</span><span class="sxs-lookup"><span data-stu-id="731a8-104">Introduction to Data Protection</span></span>](introduction.md)

* [<span data-ttu-id="731a8-105">数据保护 API 入门</span><span class="sxs-lookup"><span data-stu-id="731a8-105">Getting Started with the Data Protection APIs</span></span>](using-data-protection.md)

* [<span data-ttu-id="731a8-106">使用者 API</span><span class="sxs-lookup"><span data-stu-id="731a8-106">Consumer APIs</span></span>](consumer-apis/index.md)

  * [<span data-ttu-id="731a8-107">使用者 API 概述</span><span class="sxs-lookup"><span data-stu-id="731a8-107">Consumer APIs Overview</span></span>](consumer-apis/overview.md)

  * [<span data-ttu-id="731a8-108">目标字符串</span><span class="sxs-lookup"><span data-stu-id="731a8-108">Purpose Strings</span></span>](consumer-apis/purpose-strings.md)

  * [<span data-ttu-id="731a8-109">目标层次结构和多租户</span><span class="sxs-lookup"><span data-stu-id="731a8-109">Purpose hierarchy and multi-tenancy</span></span>](consumer-apis/purpose-strings-multitenancy.md)

  * [<span data-ttu-id="731a8-110">密码哈希</span><span class="sxs-lookup"><span data-stu-id="731a8-110">Password Hashing</span></span>](consumer-apis/password-hashing.md)

  * [<span data-ttu-id="731a8-111">限制受保护负载的生存期</span><span class="sxs-lookup"><span data-stu-id="731a8-111">Limiting the lifetime of protected payloads</span></span>](consumer-apis/limited-lifetime-payloads.md)

  * [<span data-ttu-id="731a8-112">取消保护已撤消其密钥的负载</span><span class="sxs-lookup"><span data-stu-id="731a8-112">Unprotecting payloads whose keys have been revoked</span></span>](consumer-apis/dangerous-unprotect.md)

* [<span data-ttu-id="731a8-113">配置</span><span class="sxs-lookup"><span data-stu-id="731a8-113">Configuration</span></span>](configuration/index.md)

  * [<span data-ttu-id="731a8-114">配置数据保护</span><span class="sxs-lookup"><span data-stu-id="731a8-114">Configuring Data Protection</span></span>](configuration/overview.md)

  * [<span data-ttu-id="731a8-115">默认设置</span><span class="sxs-lookup"><span data-stu-id="731a8-115">Default Settings</span></span>](configuration/default-settings.md)

  * [<span data-ttu-id="731a8-116">计算机范围的策略</span><span class="sxs-lookup"><span data-stu-id="731a8-116">Machine Wide Policy</span></span>](configuration/machine-wide-policy.md)

  * [<span data-ttu-id="731a8-117">非 DI 感知方案</span><span class="sxs-lookup"><span data-stu-id="731a8-117">Non DI Aware Scenarios</span></span>](configuration/non-di-scenarios.md)

* [<span data-ttu-id="731a8-118">扩展性 API</span><span class="sxs-lookup"><span data-stu-id="731a8-118">Extensibility APIs</span></span>](extensibility/index.md)

  * [<span data-ttu-id="731a8-119">Core 加密扩展性</span><span class="sxs-lookup"><span data-stu-id="731a8-119">Core cryptography extensibility</span></span>](extensibility/core-crypto.md)

  * [<span data-ttu-id="731a8-120">密钥管理扩展性</span><span class="sxs-lookup"><span data-stu-id="731a8-120">Key management extensibility</span></span>](extensibility/key-management.md)

  * [<span data-ttu-id="731a8-121">其他 API</span><span class="sxs-lookup"><span data-stu-id="731a8-121">Miscellaneous APIs</span></span>](extensibility/misc-apis.md)

* [<span data-ttu-id="731a8-122">实现</span><span class="sxs-lookup"><span data-stu-id="731a8-122">Implementation</span></span>](implementation/index.md)

  * [<span data-ttu-id="731a8-123">已验证的加密详细信息。</span><span class="sxs-lookup"><span data-stu-id="731a8-123">Authenticated encryption details.</span></span>](implementation/authenticated-encryption-details.md)

  * [<span data-ttu-id="731a8-124">子项派生和已验证的加密</span><span class="sxs-lookup"><span data-stu-id="731a8-124">Subkey Derivation and Authenticated Encryption</span></span>](implementation/subkeyderivation.md)

  * [<span data-ttu-id="731a8-125">上下文标头</span><span class="sxs-lookup"><span data-stu-id="731a8-125">Context headers</span></span>](implementation/context-headers.md)

  * [<span data-ttu-id="731a8-126">密钥管理</span><span class="sxs-lookup"><span data-stu-id="731a8-126">Key Management</span></span>](implementation/key-management.md)

  * [<span data-ttu-id="731a8-127">密钥存储提供程序</span><span class="sxs-lookup"><span data-stu-id="731a8-127">Key Storage Providers</span></span>](implementation/key-storage-providers.md)

  * [<span data-ttu-id="731a8-128">静态密钥加密</span><span class="sxs-lookup"><span data-stu-id="731a8-128">Key Encryption At Rest</span></span>](implementation/key-encryption-at-rest.md)

  * [<span data-ttu-id="731a8-129">密钥永久性和更改设置</span><span class="sxs-lookup"><span data-stu-id="731a8-129">Key Immutability and Changing Settings</span></span>](implementation/key-immutability.md)

  * [<span data-ttu-id="731a8-130">密钥存储格式</span><span class="sxs-lookup"><span data-stu-id="731a8-130">Key Storage Format</span></span>](implementation/key-storage-format.md)

  * [<span data-ttu-id="731a8-131">短数据保护提供程序</span><span class="sxs-lookup"><span data-stu-id="731a8-131">Ephemeral data protection providers</span></span>](implementation/key-storage-ephemeral.md)

* [<span data-ttu-id="731a8-132">兼容性</span><span class="sxs-lookup"><span data-stu-id="731a8-132">Compatibility</span></span>](compatibility/index.md)

  * [<span data-ttu-id="731a8-133">在应用程序之间共享 cookie</span><span class="sxs-lookup"><span data-stu-id="731a8-133">Sharing cookies between applications</span></span>](compatibility/cookie-sharing.md)

  * [<span data-ttu-id="731a8-134">在 ASP.NET 中替换 <machineKey></span><span class="sxs-lookup"><span data-stu-id="731a8-134">Replacing <machineKey> in ASP.NET</span></span>](compatibility/replacing-machinekey.md)
