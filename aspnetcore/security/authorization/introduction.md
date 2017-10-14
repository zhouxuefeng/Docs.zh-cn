---
title: "介绍"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: a6a556ed-ba59-4107-9358-44cf20e5931b
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/introduction
ms.openlocfilehash: fa6dcbc0627181cd1aca0926fa008f3db907742f
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2017
---
# <a name="introduction"></a><span data-ttu-id="c874e-103">介绍</span><span class="sxs-lookup"><span data-stu-id="c874e-103">Introduction</span></span>

<a name="security-authorization-introduction"></a>

<span data-ttu-id="c874e-104">授权是指确定进程用户是能够执行。</span><span class="sxs-lookup"><span data-stu-id="c874e-104">Authorization refers to the process that determines what a user is able to do.</span></span> <span data-ttu-id="c874e-105">例如，管理用户可以创建文档库、 将文档添加、 编辑文档，以及删除它们。</span><span class="sxs-lookup"><span data-stu-id="c874e-105">For example, an administrative user is allowed to create a document library, add documents, edit documents, and delete them.</span></span> <span data-ttu-id="c874e-106">使用库的非管理用户仅有权读取文档。</span><span class="sxs-lookup"><span data-stu-id="c874e-106">A non-administrative user working with the library is only authorized to read the documents.</span></span>

<span data-ttu-id="c874e-107">授权是正交和独立于身份验证，这是有助于确定用户是谁的过程。</span><span class="sxs-lookup"><span data-stu-id="c874e-107">Authorization is orthogonal and independent from authentication, which is the process of ascertaining who a user is.</span></span> <span data-ttu-id="c874e-108">身份验证可能会创建一个或多个标识为当前用户。</span><span class="sxs-lookup"><span data-stu-id="c874e-108">Authentication may create one or more identities for the current user.</span></span>

## <a name="authorization-types"></a><span data-ttu-id="c874e-109">授权类型</span><span class="sxs-lookup"><span data-stu-id="c874e-109">Authorization Types</span></span>

<span data-ttu-id="c874e-110">ASP.NET 核心授权提供一个简单的声明性[角色](roles.md#security-authorization-role-based)和[基于的丰富策略](policies.md#security-authorization-policies-based)模型。</span><span class="sxs-lookup"><span data-stu-id="c874e-110">ASP.NET Core authorization provides a simple declarative [role](roles.md#security-authorization-role-based) and a [rich policy based](policies.md#security-authorization-policies-based) model.</span></span> <span data-ttu-id="c874e-111">要求，以表示授权，并且处理程序评估针对需求的用户的声明。</span><span class="sxs-lookup"><span data-stu-id="c874e-111">Authorization is expressed in requirements, and handlers evaluate a user's claims against requirements.</span></span> <span data-ttu-id="c874e-112">命令性检查可以基于简单的策略或策略的评估用户标识和该用户尝试访问资源的属性。</span><span class="sxs-lookup"><span data-stu-id="c874e-112">Imperative checks can be based on simple policies or policies which evaluate both the user identity and properties of the resource that the user is attempting to access.</span></span>

## <a name="namespaces"></a><span data-ttu-id="c874e-113">命名空间</span><span class="sxs-lookup"><span data-stu-id="c874e-113">Namespaces</span></span>

<span data-ttu-id="c874e-114">授权组件，包括`AuthorizeAttribute`和`AllowAnonymousAttribute`中找不到属性`Microsoft.AspNetCore.Authorization`命名空间。</span><span class="sxs-lookup"><span data-stu-id="c874e-114">Authorization components, including the `AuthorizeAttribute` and `AllowAnonymousAttribute` attributes are found in the `Microsoft.AspNetCore.Authorization` namespace.</span></span>
