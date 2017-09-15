---
title: "在 ASP.NET Core 中使用数据"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: a8fb7eb7-e0e5-4394-84f3-1f1dbe0ba2ef
ms.technology: aspnet
ms.prod: asp.net-core
ms.openlocfilehash: 3566127476289ae085a9161132b103638bc9b068
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="working-with-data-in-aspnet-core"></a><span data-ttu-id="23433-103">在 ASP.NET Core 中使用数据</span><span class="sxs-lookup"><span data-stu-id="23433-103">Working with Data in ASP.NET Core</span></span> 

*   [<span data-ttu-id="23433-104">借助 Visual Studio 使用 ASP.NET Core 和 Entity Framework Core 入门</span><span class="sxs-lookup"><span data-stu-id="23433-104">Getting started with ASP.NET Core and Entity Framework Core using Visual Studio</span></span>](ef-mvc/index.md)
    *   [<span data-ttu-id="23433-105">入门</span><span class="sxs-lookup"><span data-stu-id="23433-105">Getting started</span></span>](ef-mvc/intro.md)
    *   [<span data-ttu-id="23433-106">创建、读取、更新和删除操作</span><span class="sxs-lookup"><span data-stu-id="23433-106">Create, Read, Update, and Delete operations</span></span>](ef-mvc/crud.md)
    *   [<span data-ttu-id="23433-107">排序、筛选、分页和分组</span><span class="sxs-lookup"><span data-stu-id="23433-107">Sorting, filtering, paging, and grouping</span></span>](ef-mvc/sort-filter-page.md)
    *   [<span data-ttu-id="23433-108">迁移</span><span class="sxs-lookup"><span data-stu-id="23433-108">Migrations</span></span>](ef-mvc/migrations.md)
    *   [<span data-ttu-id="23433-109">创建复杂数据模型</span><span class="sxs-lookup"><span data-stu-id="23433-109">Creating a complex data model</span></span>](ef-mvc/complex-data-model.md)
    *   [<span data-ttu-id="23433-110">读取相关数据</span><span class="sxs-lookup"><span data-stu-id="23433-110">Reading related data</span></span>](ef-mvc/read-related-data.md)
    *   [<span data-ttu-id="23433-111">更新相关数据</span><span class="sxs-lookup"><span data-stu-id="23433-111">Updating related data</span></span>](ef-mvc/update-related-data.md)
    *   [<span data-ttu-id="23433-112">处理并发冲突</span><span class="sxs-lookup"><span data-stu-id="23433-112">Handling concurrency conflicts</span></span>](ef-mvc/concurrency.md)
    *   [<span data-ttu-id="23433-113">继承</span><span class="sxs-lookup"><span data-stu-id="23433-113">Inheritance</span></span>](ef-mvc/inheritance.md)
    *   [<span data-ttu-id="23433-114">高级主题</span><span class="sxs-lookup"><span data-stu-id="23433-114">Advanced topics</span></span>](ef-mvc/advanced.md)
* <span data-ttu-id="23433-115">[ASP.NET Core 和 EF Core - 新数据库](https://docs.microsoft.com/ef/core/get-started/aspnetcore/new-db)（Entity Framework Core 文档站点）</span><span class="sxs-lookup"><span data-stu-id="23433-115">[ASP.NET Core with EF Core - new database](https://docs.microsoft.com/ef/core/get-started/aspnetcore/new-db) (Entity Framework Core documentation site)</span></span>
* <span data-ttu-id="23433-116">[ASP.NET Core 和 EF Core - 现有数据库](https://docs.microsoft.com/ef/core/get-started/aspnetcore/existing-db)（Entity Framework Core 文档站点）</span><span class="sxs-lookup"><span data-stu-id="23433-116">[ASP.NET Core with EF Core - existing database](https://docs.microsoft.com/ef/core/get-started/aspnetcore/existing-db) (Entity Framework Core documentation site)</span></span>
*   [<span data-ttu-id="23433-117">开始使用 ASP.NET Core 和 Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="23433-117">Getting Started with ASP.NET Core and Entity Framework 6</span></span>](entity-framework-6.md)
*   [<span data-ttu-id="23433-118">Azure 存储</span><span class="sxs-lookup"><span data-stu-id="23433-118">Azure Storage</span></span>](azure-storage/index.md)
    *   [<span data-ttu-id="23433-119">使用 Visual Studio 连接服务添加 Azure 存储</span><span class="sxs-lookup"><span data-stu-id="23433-119">Adding Azure Storage by Using Visual Studio Connected Services</span></span>](https://azure.microsoft.com/documentation/articles/vs-azure-tools-connected-services-storage/)
    *   [<span data-ttu-id="23433-120">开始使用 Azure Blob 存储和 Visual Studio 连接服务</span><span class="sxs-lookup"><span data-stu-id="23433-120">Get Started with Azure Blob storage and Visual Studio Connected Services</span></span>](https://azure.microsoft.com/documentation/articles/vs-storage-aspnet5-getting-started-blobs/)
    *   [<span data-ttu-id="23433-121">开始使用队列存储和 Visual Studio 连接服务</span><span class="sxs-lookup"><span data-stu-id="23433-121">Get Started with Queue Storage and Visual Studio Connected Services</span></span>](https://azure.microsoft.com/documentation/articles/vs-storage-aspnet5-getting-started-queues/)
    *   [<span data-ttu-id="23433-122">如何开始使用 Azure 表服务和 Visual Studio 连接服务</span><span class="sxs-lookup"><span data-stu-id="23433-122">How to Get Started with Azure Table Storage and Visual Studio Connected Services</span></span>](https://azure.microsoft.com/documentation/articles/vs-storage-aspnet5-getting-started-tables/)
