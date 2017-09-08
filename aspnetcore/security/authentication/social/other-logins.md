---
title: "其他身份验证提供程序的简短的调查。"
author: rick-anderson
ms.author: riande
manager: wpickett
ms.date: 11/3/2016
ms.topic: article
ms.assetid: BC36CA84-3DE8-496E-9AA2-2F1B74AE8309
ms.prod: asp.net-core
uid: security/authentication/otherlogins
ms.openlocfilehash: 421db8c89e01cebba0c1f98cc9286288a75777f2
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2017
---
# <a name="short-survey-of-other-authentication-providers"></a><span data-ttu-id="78b4b-102">简短的调查的其他身份验证提供程序</span><span class="sxs-lookup"><span data-stu-id="78b4b-102">Short survey of other authentication providers</span></span>

<a name=security-authentication-other-logins></a>

<span data-ttu-id="78b4b-103">通过[Rick Anderson](https://twitter.com/RickAndMSFT)， [Pranav Rastogi](https://github.com/rustd)，和[Valeriy Novytskyy](https://github.com/01binary)</span><span class="sxs-lookup"><span data-stu-id="78b4b-103">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Pranav Rastogi](https://github.com/rustd), and [Valeriy Novytskyy](https://github.com/01binary)</span></span>

<span data-ttu-id="78b4b-104">此处会设置对于某些其他常见 OAuth 提供程序的说明。</span><span class="sxs-lookup"><span data-stu-id="78b4b-104">Here are set up instructions for some other common OAuth providers.</span></span> <span data-ttu-id="78b4b-105">第三方 NuGet 包，例如由维护的[aspnet contrib](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth)可以用于补充由 ASP.NET Core 团队实现的身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="78b4b-105">Third-party NuGet packages such as the ones maintained by [aspnet-contrib](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth) can be used to complement authentication providers implemented by the ASP.NET Core team.</span></span>

* <span data-ttu-id="78b4b-106">设置**LinkedIn**登录： [https://developer.linkedin.com/my-apps](https://developer.linkedin.com/my-apps)。</span><span class="sxs-lookup"><span data-stu-id="78b4b-106">Set up **LinkedIn** sign in: [https://developer.linkedin.com/my-apps](https://developer.linkedin.com/my-apps).</span></span> <span data-ttu-id="78b4b-107">请参阅[官方步骤](https://developer.linkedin.com/docs/oauth2)。</span><span class="sxs-lookup"><span data-stu-id="78b4b-107">See [official steps](https://developer.linkedin.com/docs/oauth2).</span></span>

* <span data-ttu-id="78b4b-108">设置**Instagram**登录： [https://www.instagram.com/developer/clients/manage](https://www.instagram.com/developer/clients/manage)。</span><span class="sxs-lookup"><span data-stu-id="78b4b-108">Set up **Instagram** sign in: [https://www.instagram.com/developer/clients/manage](https://www.instagram.com/developer/clients/manage).</span></span> <span data-ttu-id="78b4b-109">请参阅[官方步骤](https://www.instagram.com/developer/authentication/)。</span><span class="sxs-lookup"><span data-stu-id="78b4b-109">See [official steps](https://www.instagram.com/developer/authentication/).</span></span>

* <span data-ttu-id="78b4b-110">设置**Reddit**登录： [https://www.reddit.com/prefs/apps](https://www.reddit.com/prefs/apps)。</span><span class="sxs-lookup"><span data-stu-id="78b4b-110">Set up **Reddit** sign in: [https://www.reddit.com/prefs/apps](https://www.reddit.com/prefs/apps).</span></span> <span data-ttu-id="78b4b-111">请参阅[官方步骤](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example)。</span><span class="sxs-lookup"><span data-stu-id="78b4b-111">See [official steps](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example).</span></span>

* <span data-ttu-id="78b4b-112">设置**Github**登录： [https://github.com/settings/applications/new](https://github.com/settings/applications/new)。</span><span class="sxs-lookup"><span data-stu-id="78b4b-112">Set up **Github** sign in: [https://github.com/settings/applications/new](https://github.com/settings/applications/new).</span></span> <span data-ttu-id="78b4b-113">请参阅[官方步骤](https://developer.github.com/v3/oauth/)。</span><span class="sxs-lookup"><span data-stu-id="78b4b-113">See [official steps](https://developer.github.com/v3/oauth/).</span></span>

* <span data-ttu-id="78b4b-114">设置**Yahoo**登录： [https://developer.yahoo.com/apps/create/](https://developer.yahoo.com/apps/create/)。</span><span class="sxs-lookup"><span data-stu-id="78b4b-114">Set up **Yahoo** sign in: [https://developer.yahoo.com/apps/create/](https://developer.yahoo.com/apps/create/).</span></span> <span data-ttu-id="78b4b-115">请参阅[官方步骤](https://developer.yahoo.com/bbauth/user.html)。</span><span class="sxs-lookup"><span data-stu-id="78b4b-115">See [official steps](https://developer.yahoo.com/bbauth/user.html).</span></span>

* <span data-ttu-id="78b4b-116">设置**Tumblr**登录： [https://www.tumblr.com/oauth/apps](https://www.tumblr.com/oauth/apps)。</span><span class="sxs-lookup"><span data-stu-id="78b4b-116">Set up **Tumblr** sign in: [https://www.tumblr.com/oauth/apps](https://www.tumblr.com/oauth/apps).</span></span> <span data-ttu-id="78b4b-117">请参阅[官方步骤](https://www.tumblr.com/docs/en/api/v2#auth)。</span><span class="sxs-lookup"><span data-stu-id="78b4b-117">See [official steps](https://www.tumblr.com/docs/en/api/v2#auth).</span></span>

* <span data-ttu-id="78b4b-118">设置**Pinterest**登录： [https://developers.pinterest.com/apps](https://developers.pinterest.com/apps)。</span><span class="sxs-lookup"><span data-stu-id="78b4b-118">Set up **Pinterest** sign in: [https://developers.pinterest.com/apps](https://developers.pinterest.com/apps).</span></span> <span data-ttu-id="78b4b-119">请参阅[官方步骤](https://developers.pinterest.com/docs/api/overview/?)。</span><span class="sxs-lookup"><span data-stu-id="78b4b-119">See [official steps](https://developers.pinterest.com/docs/api/overview/?).</span></span>

* <span data-ttu-id="78b4b-120">设置**Pocket**登录： [http://getpocket.com/developer/apps/new](http://getpocket.com/developer/apps/new)。</span><span class="sxs-lookup"><span data-stu-id="78b4b-120">Set up **Pocket** sign in: [http://getpocket.com/developer/apps/new](http://getpocket.com/developer/apps/new).</span></span> <span data-ttu-id="78b4b-121">请参阅[官方步骤](https://getpocket.com/developer/docs/authentication)。</span><span class="sxs-lookup"><span data-stu-id="78b4b-121">See [official steps](https://getpocket.com/developer/docs/authentication).</span></span>

* <span data-ttu-id="78b4b-122">设置**Flickr**登录： [https://www.flickr.com/services/apps/create](https://www.flickr.com/services/apps/create)。</span><span class="sxs-lookup"><span data-stu-id="78b4b-122">Set up **Flickr** sign in: [https://www.flickr.com/services/apps/create](https://www.flickr.com/services/apps/create).</span></span> <span data-ttu-id="78b4b-123">请参阅[官方步骤](https://www.flickr.com/services/api/auth.oauth.html)。</span><span class="sxs-lookup"><span data-stu-id="78b4b-123">See [official steps](https://www.flickr.com/services/api/auth.oauth.html).</span></span>

* <span data-ttu-id="78b4b-124">设置**Dribble**登录： [https://dribbble.com/signup](https://dribbble.com/signup)。</span><span class="sxs-lookup"><span data-stu-id="78b4b-124">Set up **Dribble** sign in: [https://dribbble.com/signup](https://dribbble.com/signup).</span></span> <span data-ttu-id="78b4b-125">请参阅[官方步骤](http://developer.dribbble.com/v1/oauth/)。</span><span class="sxs-lookup"><span data-stu-id="78b4b-125">See [official steps](http://developer.dribbble.com/v1/oauth/).</span></span>

* <span data-ttu-id="78b4b-126">设置**Vimeo**登录： [https://developer.vimeo.com/apps](https://developer.vimeo.com/apps)。</span><span class="sxs-lookup"><span data-stu-id="78b4b-126">Set up **Vimeo** sign in: [https://developer.vimeo.com/apps](https://developer.vimeo.com/apps).</span></span> <span data-ttu-id="78b4b-127">请参阅[官方步骤](https://developer.vimeo.com/api/authentication)。</span><span class="sxs-lookup"><span data-stu-id="78b4b-127">See [official steps](https://developer.vimeo.com/api/authentication).</span></span>

* <span data-ttu-id="78b4b-128">设置**SoundCloud**登录： [http://soundcloud.com/you/apps/new](http://soundcloud.com/you/apps/new)。</span><span class="sxs-lookup"><span data-stu-id="78b4b-128">Set up **SoundCloud** sign in: [http://soundcloud.com/you/apps/new](http://soundcloud.com/you/apps/new).</span></span> <span data-ttu-id="78b4b-129">请参阅[官方步骤](https://developers.soundcloud.com/blog/we-love-oauth-2)。</span><span class="sxs-lookup"><span data-stu-id="78b4b-129">See [official steps](https://developers.soundcloud.com/blog/we-love-oauth-2).</span></span>

* <span data-ttu-id="78b4b-130">设置**VK**登录： [https://vk.com/apps?act=manage](https://vk.com/apps?act=manage)。</span><span class="sxs-lookup"><span data-stu-id="78b4b-130">Set up **VK** sign in: [https://vk.com/apps?act=manage](https://vk.com/apps?act=manage).</span></span> <span data-ttu-id="78b4b-131">请参阅[官方步骤](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites)。</span><span class="sxs-lookup"><span data-stu-id="78b4b-131">See [official steps](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites).</span></span>
