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
ms.openlocfilehash: f173d03f55a1ce52222a75c023f9e8a20d5c60dc
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2017
---
# <a name="security"></a>安全性

*   [身份验证](authentication/index.md)
    *   [标识简介](authentication/identity.md)
    *   [启用使用 Facebook、Google 和其他外部提供程序的身份验证](authentication/social/index.md)
    * [配置 Windows 身份验证](authentication/windowsauth.md)
    *   [帐户确认和密码恢复](authentication/accconfirm.md)
    *   [使用 SMS 设置双因素身份验证](authentication/2fa.md) 
    *   [在没有 ASP.NET Core 标识的情况下使用 Cookie 身份验证](authentication/cookie.md)
    *   [Azure Active Directory](authentication/azure-active-directory/index.md)
        *   [Integrating Azure AD Into an ASP.NET Core Web App](https://azure.microsoft.com/documentation/samples/active-directory-dotnet-webapp-openidconnect-aspnetcore/)（将 Azure AD 集成到 ASP.NET Core Web 应用中）
        *   [Calling a ASP.NET Core Web API From a WPF Application Using Azure AD](https://azure.microsoft.com/documentation/samples/active-directory-dotnet-native-aspnetcore/)（从使用 Azure AD 的 WPF 应用程序调用 ASP.NET Core Web API）
        *   [Calling a Web API in an ASP.NET Core Web Application Using Azure AD](https://azure.microsoft.com/documentation/samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore/)（在使用 Azure AD 的 ASP.NET Core Web 应用程序中调用 Web API）
        *   [带有 Azure AD B2C 的 ASP.NET Core Web 应用](https://azure.microsoft.com/resources/samples/active-directory-b2c-dotnetcore-webapp/)
    *   [使用 IdentityServer4 保护 ASP.NET Core 应用](https://identityserver4.readthedocs.io)
*   [授权](authorization/index.md)
    *   [介绍](authorization/introduction.md)
    *   [通过授权保护的用户数据创建应用](xref:security/authorization/secure-data)
    *   [简单授权](authorization/simple.md)
    *   [基于角色的授权](authorization/roles.md)
    *   [基于声明的授权](authorization/claims.md)
    *   [基于自定义策略的授权](authorization/policies.md)
    *   [要求处理程序中的依赖关系注入](authorization/dependencyinjection.md)
    *   [基于资源的授权](authorization/resourcebased.md)
    *   [基于视图的授权](authorization/views.md)
    *   [使用方案限制标识](authorization/limitingidentitybyscheme.md)
*   [数据保护](data-protection/index.md)
    *   [数据保护简介](data-protection/introduction.md)
    *   [数据保护 API 入门](data-protection/using-data-protection.md)
    *   [使用者 API](data-protection/consumer-apis/index.md)
        *   [使用者 API 概述](data-protection/consumer-apis/overview.md)
        *   [目标字符串](data-protection/consumer-apis/purpose-strings.md)
        *   [目标层次结构和多租户](data-protection/consumer-apis/purpose-strings-multitenancy.md)
        *   [密码哈希](data-protection/consumer-apis/password-hashing.md)
        *   [限制受保护负载的生存期](data-protection/consumer-apis/limited-lifetime-payloads.md)
        *   [取消保护已撤消其密钥的负载](data-protection/consumer-apis/dangerous-unprotect.md)
    *   [配置](data-protection/configuration/index.md)
        *   [配置数据保护](data-protection/configuration/overview.md)
        *   [默认设置](data-protection/configuration/default-settings.md)
        *   [计算机范围的策略](data-protection/configuration/machine-wide-policy.md)
        *   [非 DI 感知方案](data-protection/configuration/non-di-scenarios.md)
    *   [扩展性 API](data-protection/extensibility/index.md)
        *   [核心加密扩展性](data-protection/extensibility/core-crypto.md)
        *   [密钥管理扩展性](data-protection/extensibility/key-management.md)
        *   [其他 API](data-protection/extensibility/misc-apis.md)
    *   [实现](data-protection/implementation/index.md)
        *   [已验证的加密详细信息。](data-protection/implementation/authenticated-encryption-details.md)
        *   [子项派生和已验证的加密](data-protection/implementation/subkeyderivation.md)
        *   [上下文标头](data-protection/implementation/context-headers.md)
        *   [密钥管理](data-protection/implementation/key-management.md)
        *   [密钥存储提供程序](data-protection/implementation/key-storage-providers.md)
        *   [静态密钥加密](data-protection/implementation/key-encryption-at-rest.md)
        *   [密钥永久性和更改设置](data-protection/implementation/key-immutability.md)
        *   [密钥存储格式](data-protection/implementation/key-storage-format.md)
        *   [短数据保护提供程序](data-protection/implementation/key-storage-ephemeral.md)
    *   [兼容性](data-protection/compatibility/index.md)
        *   [在应用程序之间共享 cookie](data-protection/compatibility/cookie-sharing.md)
        *   [在 ASP.NET 中替换 <machineKey>](data-protection/compatibility/replacing-machinekey.md)
*   [通过授权保护的用户数据创建应用](xref:security/authorization/secure-data)
*   [在开发期间安全存储应用密钥](app-secrets.md)
*   [Azure Key Vault 配置提供程序](key-vault-configuration.md)
*   [强制实施 SSL](enforcing-ssl.md)
*   [反请求伪造](anti-request-forgery.md)
*   [阻止打开重定向攻击](preventing-open-redirects.md)
*   [阻止跨网站脚本编写](cross-site-scripting.md)
*   [启用跨域请求 (CORS)](cors.md)
