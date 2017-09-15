---
title: "安全性"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: a8fb7eb7-e0e5-4394-84f3-1f1dbe012345
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/index
ms.openlocfilehash: e8045b8804d1e7915cd81d697d43a173e33a7119
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="security"></a><span data-ttu-id="40787-103">安全性</span><span class="sxs-lookup"><span data-stu-id="40787-103">Security</span></span>

*   [<span data-ttu-id="40787-104">身份验证</span><span class="sxs-lookup"><span data-stu-id="40787-104">Authentication</span></span>](authentication/index.md)
    *   [<span data-ttu-id="40787-105">标识简介</span><span class="sxs-lookup"><span data-stu-id="40787-105">Introduction to Identity</span></span>](authentication/identity.md)
    *   [<span data-ttu-id="40787-106">启用使用 Facebook、Google 和其他外部提供程序的身份验证</span><span class="sxs-lookup"><span data-stu-id="40787-106">Enabling authentication using Facebook, Google and other external providers</span></span>](authentication/social/index.md)
    * [<span data-ttu-id="40787-107">配置 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="40787-107">Configure Windows Authentication</span></span>](authentication/windowsauth.md)
    *   [<span data-ttu-id="40787-108">帐户确认和密码恢复</span><span class="sxs-lookup"><span data-stu-id="40787-108">Account Confirmation and Password Recovery</span></span>](authentication/accconfirm.md)
    *   [<span data-ttu-id="40787-109">使用 SMS 设置双因素身份验证</span><span class="sxs-lookup"><span data-stu-id="40787-109">Two-factor authentication with SMS</span></span>](authentication/2fa.md) 
    *   [<span data-ttu-id="40787-110">在没有 ASP.NET Core 标识的情况下使用 Cookie 身份验证</span><span class="sxs-lookup"><span data-stu-id="40787-110">Using Cookie Authentication without ASP.NET Core Identity</span></span>](authentication/cookie.md)
    *   [<span data-ttu-id="40787-111">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="40787-111">Azure Active Directory</span></span>](authentication/azure-active-directory/index.md)
        *   <span data-ttu-id="40787-112">[Integrating Azure AD Into an ASP.NET Core Web App](https://azure.microsoft.com/documentation/samples/active-directory-dotnet-webapp-openidconnect-aspnetcore/)（将 Azure AD 集成到 ASP.NET Core Web 应用中）</span><span class="sxs-lookup"><span data-stu-id="40787-112">[Integrating Azure AD Into an ASP.NET Core Web App](https://azure.microsoft.com/documentation/samples/active-directory-dotnet-webapp-openidconnect-aspnetcore/)</span></span>
        *   <span data-ttu-id="40787-113">[Calling a ASP.NET Core Web API From a WPF Application Using Azure AD](https://azure.microsoft.com/documentation/samples/active-directory-dotnet-native-aspnetcore/)（从使用 Azure AD 的 WPF 应用程序调用 ASP.NET Core Web API）</span><span class="sxs-lookup"><span data-stu-id="40787-113">[Calling a ASP.NET Core Web API From a WPF Application Using Azure AD](https://azure.microsoft.com/documentation/samples/active-directory-dotnet-native-aspnetcore/)</span></span>
        *   <span data-ttu-id="40787-114">[Calling a Web API in an ASP.NET Core Web Application Using Azure AD](https://azure.microsoft.com/documentation/samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore/)（在使用 Azure AD 的 ASP.NET Core Web 应用程序中调用 Web API）</span><span class="sxs-lookup"><span data-stu-id="40787-114">[Calling a Web API in an ASP.NET Core Web Application Using Azure AD](https://azure.microsoft.com/documentation/samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore/)</span></span>
        *   [<span data-ttu-id="40787-115">带有 Azure AD B2C 的 ASP.NET Core Web 应用</span><span class="sxs-lookup"><span data-stu-id="40787-115">An ASP.NET Core web app with Azure AD B2C</span></span>](https://azure.microsoft.com/resources/samples/active-directory-b2c-dotnetcore-webapp/)
    *   [<span data-ttu-id="40787-116">使用 IdentityServer4 保护 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="40787-116">Securing ASP.NET Core apps with IdentityServer4</span></span>](https://identityserver4.readthedocs.io)
*   [<span data-ttu-id="40787-117">授权</span><span class="sxs-lookup"><span data-stu-id="40787-117">Authorization</span></span>](authorization/index.md)
    *   [<span data-ttu-id="40787-118">介绍</span><span class="sxs-lookup"><span data-stu-id="40787-118">Introduction</span></span>](authorization/introduction.md)
    *   [<span data-ttu-id="40787-119">通过授权保护的用户数据创建应用</span><span class="sxs-lookup"><span data-stu-id="40787-119">Create an app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)
    *   [<span data-ttu-id="40787-120">简单授权</span><span class="sxs-lookup"><span data-stu-id="40787-120">Simple Authorization</span></span>](authorization/simple.md)
    *   [<span data-ttu-id="40787-121">基于角色的授权</span><span class="sxs-lookup"><span data-stu-id="40787-121">Role based Authorization</span></span>](authorization/roles.md)
    *   [<span data-ttu-id="40787-122">基于声明的授权</span><span class="sxs-lookup"><span data-stu-id="40787-122">Claims-Based Authorization</span></span>](authorization/claims.md)
    *   [<span data-ttu-id="40787-123">基于自定义策略的授权</span><span class="sxs-lookup"><span data-stu-id="40787-123">Custom Policy-Based Authorization</span></span>](authorization/policies.md)
    *   [<span data-ttu-id="40787-124">要求处理程序中的依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="40787-124">Dependency Injection in requirement handlers</span></span>](authorization/dependencyinjection.md)
    *   [<span data-ttu-id="40787-125">基于资源的授权</span><span class="sxs-lookup"><span data-stu-id="40787-125">Resource Based Authorization</span></span>](authorization/resourcebased.md)
    *   [<span data-ttu-id="40787-126">基于视图的授权</span><span class="sxs-lookup"><span data-stu-id="40787-126">View Based Authorization</span></span>](authorization/views.md)
    *   [<span data-ttu-id="40787-127">使用方案限制标识</span><span class="sxs-lookup"><span data-stu-id="40787-127">Limiting identity by scheme</span></span>](authorization/limitingidentitybyscheme.md)
*   [<span data-ttu-id="40787-128">数据保护</span><span class="sxs-lookup"><span data-stu-id="40787-128">Data Protection</span></span>](data-protection/index.md)
    *   [<span data-ttu-id="40787-129">数据保护简介</span><span class="sxs-lookup"><span data-stu-id="40787-129">Introduction to Data Protection</span></span>](data-protection/introduction.md)
    *   [<span data-ttu-id="40787-130">数据保护 API 入门</span><span class="sxs-lookup"><span data-stu-id="40787-130">Getting Started with the Data Protection APIs</span></span>](data-protection/using-data-protection.md)
    *   [<span data-ttu-id="40787-131">使用者 API</span><span class="sxs-lookup"><span data-stu-id="40787-131">Consumer APIs</span></span>](data-protection/consumer-apis/index.md)
        *   [<span data-ttu-id="40787-132">使用者 API 概述</span><span class="sxs-lookup"><span data-stu-id="40787-132">Consumer APIs Overview</span></span>](data-protection/consumer-apis/overview.md)
        *   [<span data-ttu-id="40787-133">目标字符串</span><span class="sxs-lookup"><span data-stu-id="40787-133">Purpose Strings</span></span>](data-protection/consumer-apis/purpose-strings.md)
        *   [<span data-ttu-id="40787-134">目标层次结构和多租户</span><span class="sxs-lookup"><span data-stu-id="40787-134">Purpose hierarchy and multi-tenancy</span></span>](data-protection/consumer-apis/purpose-strings-multitenancy.md)
        *   [<span data-ttu-id="40787-135">密码哈希</span><span class="sxs-lookup"><span data-stu-id="40787-135">Password Hashing</span></span>](data-protection/consumer-apis/password-hashing.md)
        *   [<span data-ttu-id="40787-136">限制受保护负载的生存期</span><span class="sxs-lookup"><span data-stu-id="40787-136">Limiting the lifetime of protected payloads</span></span>](data-protection/consumer-apis/limited-lifetime-payloads.md)
        *   [<span data-ttu-id="40787-137">取消保护已撤消其密钥的负载</span><span class="sxs-lookup"><span data-stu-id="40787-137">Unprotecting payloads whose keys have been revoked</span></span>](data-protection/consumer-apis/dangerous-unprotect.md)
    *   [<span data-ttu-id="40787-138">配置</span><span class="sxs-lookup"><span data-stu-id="40787-138">Configuration</span></span>](data-protection/configuration/index.md)
        *   [<span data-ttu-id="40787-139">配置数据保护</span><span class="sxs-lookup"><span data-stu-id="40787-139">Configuring Data Protection</span></span>](data-protection/configuration/overview.md)
        *   [<span data-ttu-id="40787-140">默认设置</span><span class="sxs-lookup"><span data-stu-id="40787-140">Default Settings</span></span>](data-protection/configuration/default-settings.md)
        *   [<span data-ttu-id="40787-141">计算机范围的策略</span><span class="sxs-lookup"><span data-stu-id="40787-141">Machine Wide Policy</span></span>](data-protection/configuration/machine-wide-policy.md)
        *   [<span data-ttu-id="40787-142">非 DI 感知方案</span><span class="sxs-lookup"><span data-stu-id="40787-142">Non DI Aware Scenarios</span></span>](data-protection/configuration/non-di-scenarios.md)
    *   [<span data-ttu-id="40787-143">扩展性 API</span><span class="sxs-lookup"><span data-stu-id="40787-143">Extensibility APIs</span></span>](data-protection/extensibility/index.md)
        *   [<span data-ttu-id="40787-144">核心加密扩展性</span><span class="sxs-lookup"><span data-stu-id="40787-144">Core cryptography extensibility</span></span>](data-protection/extensibility/core-crypto.md)
        *   [<span data-ttu-id="40787-145">密钥管理扩展性</span><span class="sxs-lookup"><span data-stu-id="40787-145">Key management extensibility</span></span>](data-protection/extensibility/key-management.md)
        *   [<span data-ttu-id="40787-146">其他 API</span><span class="sxs-lookup"><span data-stu-id="40787-146">Miscellaneous APIs</span></span>](data-protection/extensibility/misc-apis.md)
    *   [<span data-ttu-id="40787-147">实现</span><span class="sxs-lookup"><span data-stu-id="40787-147">Implementation</span></span>](data-protection/implementation/index.md)
        *   [<span data-ttu-id="40787-148">已验证的加密详细信息。</span><span class="sxs-lookup"><span data-stu-id="40787-148">Authenticated encryption details.</span></span>](data-protection/implementation/authenticated-encryption-details.md)
        *   [<span data-ttu-id="40787-149">子项派生和已验证的加密</span><span class="sxs-lookup"><span data-stu-id="40787-149">Subkey Derivation and Authenticated Encryption</span></span>](data-protection/implementation/subkeyderivation.md)
        *   [<span data-ttu-id="40787-150">上下文标头</span><span class="sxs-lookup"><span data-stu-id="40787-150">Context headers</span></span>](data-protection/implementation/context-headers.md)
        *   [<span data-ttu-id="40787-151">密钥管理</span><span class="sxs-lookup"><span data-stu-id="40787-151">Key Management</span></span>](data-protection/implementation/key-management.md)
        *   [<span data-ttu-id="40787-152">密钥存储提供程序</span><span class="sxs-lookup"><span data-stu-id="40787-152">Key Storage Providers</span></span>](data-protection/implementation/key-storage-providers.md)
        *   [<span data-ttu-id="40787-153">静态密钥加密</span><span class="sxs-lookup"><span data-stu-id="40787-153">Key Encryption At Rest</span></span>](data-protection/implementation/key-encryption-at-rest.md)
        *   [<span data-ttu-id="40787-154">密钥永久性和更改设置</span><span class="sxs-lookup"><span data-stu-id="40787-154">Key Immutability and Changing Settings</span></span>](data-protection/implementation/key-immutability.md)
        *   [<span data-ttu-id="40787-155">密钥存储格式</span><span class="sxs-lookup"><span data-stu-id="40787-155">Key Storage Format</span></span>](data-protection/implementation/key-storage-format.md)
        *   [<span data-ttu-id="40787-156">短数据保护提供程序</span><span class="sxs-lookup"><span data-stu-id="40787-156">Ephemeral data protection providers</span></span>](data-protection/implementation/key-storage-ephemeral.md)
    *   [<span data-ttu-id="40787-157">兼容性</span><span class="sxs-lookup"><span data-stu-id="40787-157">Compatibility</span></span>](data-protection/compatibility/index.md)
        *   [<span data-ttu-id="40787-158">在应用程序之间共享 cookie</span><span class="sxs-lookup"><span data-stu-id="40787-158">Sharing cookies between applications</span></span>](data-protection/compatibility/cookie-sharing.md)
        *   [<span data-ttu-id="40787-159">在 ASP.NET 中替换 <machineKey></span><span class="sxs-lookup"><span data-stu-id="40787-159">Replacing <machineKey> in ASP.NET</span></span>](data-protection/compatibility/replacing-machinekey.md)
*   [<span data-ttu-id="40787-160">通过授权保护的用户数据创建应用</span><span class="sxs-lookup"><span data-stu-id="40787-160">Create an app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)
*   [<span data-ttu-id="40787-161">在开发期间安全存储应用密钥</span><span class="sxs-lookup"><span data-stu-id="40787-161">Safe storage of app secrets during development</span></span>](app-secrets.md)
*   [<span data-ttu-id="40787-162">Azure Key Vault 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="40787-162">Azure Key Vault configuration provider</span></span>](key-vault-configuration.md)
*   [<span data-ttu-id="40787-163">强制实施 SSL</span><span class="sxs-lookup"><span data-stu-id="40787-163">Enforcing SSL</span></span>](enforcing-ssl.md)
*   [<span data-ttu-id="40787-164">设置 HTTPS 进行开发</span><span class="sxs-lookup"><span data-stu-id="40787-164">Setting up HTTPS for development</span></span>](https.md)
*   [<span data-ttu-id="40787-165">反请求伪造</span><span class="sxs-lookup"><span data-stu-id="40787-165">Anti-Request Forgery</span></span>](anti-request-forgery.md)
*   [<span data-ttu-id="40787-166">阻止打开重定向攻击</span><span class="sxs-lookup"><span data-stu-id="40787-166">Preventing Open Redirect Attacks</span></span>](preventing-open-redirects.md)
*   [<span data-ttu-id="40787-167">阻止跨网站脚本编写</span><span class="sxs-lookup"><span data-stu-id="40787-167">Preventing Cross-Site Scripting</span></span>](cross-site-scripting.md)
*   [<span data-ttu-id="40787-168">启用跨域请求 (CORS)</span><span class="sxs-lookup"><span data-stu-id="40787-168">Enabling Cross-Origin Requests (CORS)</span></span>](cors.md)
