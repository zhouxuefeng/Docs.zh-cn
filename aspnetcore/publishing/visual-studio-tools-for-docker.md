---
title: "使用 ASP.NET Core 的 Visual Studio Tools for Docker"
description: "本文演示如何使用 Visual Studio 2017 工具和 Docker for Windows 来容器化 ASP.NET Core 应用程序。"
keywords: "Docker, ASP.NET Core, Visual Studio, 容器"
author: spboyer
ms.author: scaddie
manager: wpickett
ms.date: 09/26/2017
ms.topic: article
ms.prod: asp.net-core
ms.technology: aspnet
ms.assetid: 1f3b9a68-4dea-4b60-8cb3-f46164eedbbf
uid: publishing/vs-tools-for-docker
ms.openlocfilehash: bc436e2c02b05475b84cf9f8bdedf9463a673c4a
ms.sourcegitcommit: b861bab71ea6945f673c62223ae2cba3aa74cb6b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2017
---
# <a name="visual-studio-tools-for-docker"></a><span data-ttu-id="cd232-104">Visual Studio Tools for Docker</span><span class="sxs-lookup"><span data-stu-id="cd232-104">Visual Studio Tools for Docker</span></span>

<span data-ttu-id="cd232-105">含 [Docker for Windows](https://docs.docker.com/docker-for-windows/install/) 的 [Microsoft Visual Studio 2017](https://www.visualstudio.com/) 支持生成、调试和运行.NET Framework、.NET Core Web 以及使用 Windows 和 Linux 容器的控制台应用程序。</span><span class="sxs-lookup"><span data-stu-id="cd232-105">[Microsoft Visual Studio 2017](https://www.visualstudio.com/) with [Docker for Windows](https://docs.docker.com/docker-for-windows/install/) supports building, debugging, and running .NET Framework and .NET Core web and console applications using Windows and Linux containers.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cd232-106">先决条件</span><span class="sxs-lookup"><span data-stu-id="cd232-106">Prerequisites</span></span>

- <span data-ttu-id="cd232-107">含 .NET Core 工作负载的 [Microsoft Visual Studio 2017](https://www.visualstudio.com/)</span><span class="sxs-lookup"><span data-stu-id="cd232-107">[Microsoft Visual Studio 2017](https://www.visualstudio.com/) with .NET Core workload</span></span>
- [<span data-ttu-id="cd232-108">Docker for Windows</span><span class="sxs-lookup"><span data-stu-id="cd232-108">Docker for Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

## <a name="installation-and-setup"></a><span data-ttu-id="cd232-109">安装和设置</span><span class="sxs-lookup"><span data-stu-id="cd232-109">Installation and setup</span></span>

<span data-ttu-id="cd232-110">使用 .NET Core 工作负载安装 [Microsoft Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio)。</span><span class="sxs-lookup"><span data-stu-id="cd232-110">Install [Microsoft Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) with the .NET Core workload.</span></span>

<span data-ttu-id="cd232-111">要安装 Docker，请查看 [Docker for Windows：安装须知](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)了解相关信息，并安装 [Docker for Windows](https://docs.docker.com/docker-for-windows/install/)。</span><span class="sxs-lookup"><span data-stu-id="cd232-111">For Docker installation, review the information at [Docker for Windows: What to know before you install](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install) and install [Docker For Windows](https://docs.docker.com/docker-for-windows/install/).</span></span>

<span data-ttu-id="cd232-112">所需的配置是在 Docker for Windows 中安装 **[Shared Drives](https://docs.docker.com/docker-for-windows/#shared-drives)**。</span><span class="sxs-lookup"><span data-stu-id="cd232-112">A required configuration is to setup **[Shared Drives](https://docs.docker.com/docker-for-windows/#shared-drives)** in Docker for Windows.</span></span> <span data-ttu-id="cd232-113">卷映射和调试支持需要此设置。</span><span class="sxs-lookup"><span data-stu-id="cd232-113">The setting is required for the volume mapping and debugging support.</span></span>

<span data-ttu-id="cd232-114">右键单击系统托盘中的 Docker 图标，单击“设置”，再选择“共享驱动器”。</span><span class="sxs-lookup"><span data-stu-id="cd232-114">Right-click the Docker icon in the System Tray, click **Settings**, and select **Shared Drives**.</span></span> <span data-ttu-id="cd232-115">选择 Docker 用来存储文件并应用更改的驱动器。</span><span class="sxs-lookup"><span data-stu-id="cd232-115">Select the drive where Docker will store your files and apply changes.</span></span>

![Shared Drives](./visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

## <a name="create-an-aspnet-web-application-and-add-docker-support"></a><span data-ttu-id="cd232-117">创建 ASP.NET Web 应用程序并添加 Docker 支持</span><span class="sxs-lookup"><span data-stu-id="cd232-117">Create an ASP.NET Web Application and add Docker Support</span></span>

<span data-ttu-id="cd232-118">使用 Visual Studio，创建新的 ASP.NET Core Web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="cd232-118">Using Visual Studio, create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="cd232-119">当加载应用程序时，从“项目菜单”中选择“添加 Docker 支持”，或者右键单击“解决方案资源管理器”中的项目，然后选择“添加” > “Docker 支持”。</span><span class="sxs-lookup"><span data-stu-id="cd232-119">When the application is loaded, either select **Add Docker Support** from the **Project Menu** or right-click the project from the Solution Explorer and select **Add** > **Docker Support**.</span></span>

<span data-ttu-id="cd232-120">*项目菜单*</span><span class="sxs-lookup"><span data-stu-id="cd232-120">*Project Menu*</span></span>

![项目添加 Docker 支持](./visual-studio-tools-for-docker/_static/project-add-docker-support.png)

<span data-ttu-id="cd232-122">*项目上下文菜单*</span><span class="sxs-lookup"><span data-stu-id="cd232-122">*Project Context Menu*</span></span>

![右键单击“添加 Docker 支持”](./visual-studio-tools-for-docker/_static/right-click-add-docker-support.png)

<span data-ttu-id="cd232-124">向项目添加 Docker 支持后，可以选择 Windows 或 Linux 容器。</span><span class="sxs-lookup"><span data-stu-id="cd232-124">When you add Docker support to your project, you can choose either Windows or Linux containers.</span></span> <span data-ttu-id="cd232-125">（Docker 主机必须运行类型相同的容器。</span><span class="sxs-lookup"><span data-stu-id="cd232-125">(The Docker host must be running the same container type.</span></span> <span data-ttu-id="cd232-126">如果需要更改正在运行的 Docker 实例中的容器类型，请右键单击系统托盘中的“Docker”图标，再选择“切换到 Windows 容器”或“切换到 Linux 容器”。）</span><span class="sxs-lookup"><span data-stu-id="cd232-126">If you need change the container type in the running Docker instance, right-click the **Docker** icon in the System Tray, and choose **Switch to Windows containers** or **Switch to Linux containers**.)</span></span> 

<span data-ttu-id="cd232-127">下列文件已添加到该项目中：</span><span class="sxs-lookup"><span data-stu-id="cd232-127">The following files are added to the project:</span></span>

- <span data-ttu-id="cd232-128">**Dockerfile**：适用于 ASP.NET Core 应用程序的 Docker 文件基于 [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore) 映像。</span><span class="sxs-lookup"><span data-stu-id="cd232-128">**Dockerfile**: the Docker file for ASP.NET Core applications is based on the [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore) image.</span></span> <span data-ttu-id="cd232-129">此映像包括 ASP.NET Core NuGet 包，已对此包进行了预实时编译，以提高启动性能。</span><span class="sxs-lookup"><span data-stu-id="cd232-129">This image includes the ASP.NET Core NuGet packages, which have been pre-jitted improving startup performance.</span></span> <span data-ttu-id="cd232-130">生成 .NET Core 控制台应用程序时，Dockerfile 将引用最新的 [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet) 映像。</span><span class="sxs-lookup"><span data-stu-id="cd232-130">When building .NET Core Console Applications, the Dockerfile FROM will reference the most recent [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet) image.</span></span>   
- <span data-ttu-id="cd232-131">**docker-compose.yml**：基本 Docker Compose 文件，用于定义要通过 docker-compose build/run 生成和运行的映像集合。</span><span class="sxs-lookup"><span data-stu-id="cd232-131">**docker-compose.yml**: base Docker Compose file used to define the collection of images to be built and run with docker-compose build/run.</span></span>   
- <span data-ttu-id="cd232-132">**docker compose.dev.debug.yml**：配置设置为调试时其他具有用于迭代更改的 docker-compose 文件。</span><span class="sxs-lookup"><span data-stu-id="cd232-132">**docker-compose.dev.debug.yml**: additional docker-compose file with for iterative changes when your configuration is set to debug.</span></span> <span data-ttu-id="cd232-133">Visual Studio 将调用 -f docker-compose.yml -f docker-compose.dev.debug.yml 以将它们合并到一起。</span><span class="sxs-lookup"><span data-stu-id="cd232-133">Visual Studio will call -f docker-compose.yml -f docker-compose.dev.debug.yml to merge these together.</span></span> <span data-ttu-id="cd232-134">此组成文件可供 Visual Studio 开发工具使用。</span><span class="sxs-lookup"><span data-stu-id="cd232-134">This compose file is used by Visual Studio development tools.</span></span>   
- <span data-ttu-id="cd232-135">**docker compose.dev.release.yml**：调试发布定义的其他 Docker Compose 文件。</span><span class="sxs-lookup"><span data-stu-id="cd232-135">**docker-compose.dev.release.yml**: additional Docker Compose file to debug your release definition.</span></span> <span data-ttu-id="cd232-136">它将卷装载调试器，使它不会更改生产映像的内容。</span><span class="sxs-lookup"><span data-stu-id="cd232-136">It will volume mount the debugger so it doesn't change the contents of the production image.</span></span>  

<span data-ttu-id="cd232-137">*docker-compose.yml* 文件包含运行项目时创建的映像的名称。</span><span class="sxs-lookup"><span data-stu-id="cd232-137">The *docker-compose.yml* file contains the name of the image that is created when project is run.</span></span> 

```
version '2'

services:
  hellodockertools:
    image:  user/hellodockertools${TAG}
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "80"
``` 

<span data-ttu-id="cd232-138">在此示例中，当应用程序以**调试**模式运行以及 `user/hellodockertools:latest` 处于**发布**模式时，`image: user/hellodockertools${TAG}` 将生成映像 `user/hellodockertools:dev`。</span><span class="sxs-lookup"><span data-stu-id="cd232-138">In this example, `image: user/hellodockertools${TAG}` generates the image `user/hellodockertools:dev` when the application is run in **Debug** mode and `user/hellodockertools:latest` in **Release** mode respectively.</span></span> 

<span data-ttu-id="cd232-139">如果计划要将映像推送到注册表，则需要将 `user` 更改为 [Docker Hub](https://hub.docker.com/) 用户名。</span><span class="sxs-lookup"><span data-stu-id="cd232-139">You will want to change the `user` to your [Docker Hub](https://hub.docker.com/) username if you plan to push the image to the registry.</span></span> <span data-ttu-id="cd232-140">例如 `spboyer/hellodockertools`，或更改为专用注册表 URL `privateregistry.domain.com/`，这具体要取决于配置。</span><span class="sxs-lookup"><span data-stu-id="cd232-140">For example, `spboyer/hellodockertools`, or change to your private registry URL `privateregistry.domain.com/` depending on your configuration.</span></span>

### <a name="debugging"></a><span data-ttu-id="cd232-141">调试</span><span class="sxs-lookup"><span data-stu-id="cd232-141">Debugging</span></span>

<span data-ttu-id="cd232-142">在工具栏的调试下拉列表中选择“Docker”，然后使用 F5 启动调试应用程序。</span><span class="sxs-lookup"><span data-stu-id="cd232-142">Select **Docker** from the debug drop-down in the toolbar and use F5 to start debugging the application.</span></span> 

- <span data-ttu-id="cd232-143">已获取 *Microsoft/aspnetcore* 映像（如果缓存中尚不存在）</span><span class="sxs-lookup"><span data-stu-id="cd232-143">The *microsoft/aspnetcore* image is acquired (if not already in your cache)</span></span>
- <span data-ttu-id="cd232-144">将 *ASPNETCORE_ENVIRONMENT* 设置为“在容器内开发”</span><span class="sxs-lookup"><span data-stu-id="cd232-144">*ASPNETCORE_ENVIRONMENT* is set to Development within the container</span></span>
- <span data-ttu-id="cd232-145">已公开端口 80，并已映射为 localhost 动态分配的端口。</span><span class="sxs-lookup"><span data-stu-id="cd232-145">PORT 80 is EXPOSED and mapped to a dynamically assigned port for localhost.</span></span> <span data-ttu-id="cd232-146">该端口由 docker 主机确定，并且可以使用 docker ps 进行查询。</span><span class="sxs-lookup"><span data-stu-id="cd232-146">The port is determined by the docker host and can be queried with docker ps.</span></span> 
- <span data-ttu-id="cd232-147">将应用程序复制到容器</span><span class="sxs-lookup"><span data-stu-id="cd232-147">Your application is copied to the container</span></span>
- <span data-ttu-id="cd232-148">使用动态分配端口，通过带有附加到容器的调试程序启动默认浏览器。</span><span class="sxs-lookup"><span data-stu-id="cd232-148">Default browser is launched with the debugger attached to the container, using the dynamically assigned port.</span></span> 

<span data-ttu-id="cd232-149">生成的结果 Docker 映像是应用程序的开发映像，而 Microsoft/aspnetcore 映像则是基本映像。</span><span class="sxs-lookup"><span data-stu-id="cd232-149">The resulting Docker image built is the *dev* image of your application with the *microsoft/aspnetcore* images as the base image.</span></span>

<span data-ttu-id="cd232-150">注意：因为调试配置使用卷装载提供迭代体验，因此，开发映像中的应用内容为空。</span><span class="sxs-lookup"><span data-stu-id="cd232-150">**Note:** The dev image is empty of your app contents as Debug configurations use volume mounting to provide the iterative experience.</span></span> <span data-ttu-id="cd232-151">若要推送映像，请使用“发行”配置。</span><span class="sxs-lookup"><span data-stu-id="cd232-151">To push an image, use the Release configuration.</span></span>

```console
REPOSITORY                  TAG         IMAGE ID            CREATED         SIZE
spboyer/hellodockertools    dev         0b6e2e44b3df        4 minutes ago   268.9 MB
microsoft/aspnetcore        1.0.1       189ad4312ce7        5 days ago      268.9 MB
```

<span data-ttu-id="cd232-152">该应用程序正在使用容器运行，可以通过运行 `docker ps` 命令进行查看。</span><span class="sxs-lookup"><span data-stu-id="cd232-152">The application is running using the container, which you can see by running the `docker ps` command.</span></span>

```console
CONTAINER ID        IMAGE                          COMMAND               CREATED             STATUS              PORTS                   NAMES
3f240cf686c9        spboyer/hellodockertools:dev   "tail -f /dev/null"   4 minutes ago       Up 4 minutes        0.0.0.0:32769->80/tcp   hellodockertools_hellodockertools_1
```

### <a name="edit-and-continue"></a><span data-ttu-id="cd232-153">编辑并继续</span><span class="sxs-lookup"><span data-stu-id="cd232-153">Edit and Continue</span></span>

<span data-ttu-id="cd232-154">对静态文件和/或 razor 模板文件 (.cshtml) 所做的更改会自动更新，无需执行编译步骤。</span><span class="sxs-lookup"><span data-stu-id="cd232-154">Changes to static files and/or razor template files (*.cshtml*) are automatically updated without the need of a compilation step.</span></span> <span data-ttu-id="cd232-155">进行更改，保存并在浏览器中点击“刷新”以查看更新。</span><span class="sxs-lookup"><span data-stu-id="cd232-155">Make the change, save, and tap refresh in the browser to view the update.</span></span>  

<span data-ttu-id="cd232-156">对代码文件的修改需要在容器内进行编译和重新启动 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="cd232-156">Modifications to code files require compiling and a restart of Kestrel within the container.</span></span> <span data-ttu-id="cd232-157">更改后，使用 CTRL+F5 执行该过程并在容器内启动应用程序。</span><span class="sxs-lookup"><span data-stu-id="cd232-157">After making the change, use CTRL + F5 to perform the process and start the application within the container.</span></span> <span data-ttu-id="cd232-158">不可以重新生成或停止 Docker 容器；在命令行中使用 `docker ps` 命令，可以看到原始容器仍与 10 分钟前一样正在运行。</span><span class="sxs-lookup"><span data-stu-id="cd232-158">The Docker container is not rebuilt or stopped; using `docker ps` in the command line, you can see that the original container is still running as of 10 minutes ago.</span></span> 

```console
CONTAINER ID        IMAGE                          COMMAND               CREATED             STATUS              PORTS                   NAMES
3f240cf686c9        spboyer/hellodockertools:dev   "tail -f /dev/null"   10 minutes ago      Up 10 minutes       0.0.0.0:32769->80/tcp   hellodockertools_hellodockertools_1
```

### <a name="publishing-docker-images"></a><span data-ttu-id="cd232-159">发布 Docker 映像</span><span class="sxs-lookup"><span data-stu-id="cd232-159">Publishing Docker images</span></span>

<span data-ttu-id="cd232-160">完成应用程序的开发和调试循环后，Visual Studio Tools for Docker 将帮助创建应用程序的生产映像。</span><span class="sxs-lookup"><span data-stu-id="cd232-160">Once you have completed the develop and debug cycle of your application, the Visual Studio Tools for Docker will help you create the production image of your application.</span></span> <span data-ttu-id="cd232-161">将调试下拉列表更改为“发行”，然后生成应用程序。</span><span class="sxs-lookup"><span data-stu-id="cd232-161">Change the debug dropdown to **Release** and build the application.</span></span> <span data-ttu-id="cd232-162">此工具将生成具有 `:latest` 标签的映像，可以将其推送到私有注册表或 Docker Hub。</span><span class="sxs-lookup"><span data-stu-id="cd232-162">The tooling will produce the image with the `:latest` tag which you can push to your private registry or Docker Hub.</span></span> 

<span data-ttu-id="cd232-163">使用 `docker images` 命令可以查看映像的列表。</span><span class="sxs-lookup"><span data-stu-id="cd232-163">Using the `docker images` command, you can see the list of images.</span></span>

```console
REPOSITORY                 TAG                 IMAGE ID            CREATED             SIZE
spboyer/hellodockertools   latest              8184ae38ba91        5 seconds ago       278.4 MB
spboyer/hellodockertools   dev                 0b6e2e44b3df        About an hour ago   268.9 MB
microsoft/aspnetcore       1.0.1               189ad4312ce7        5 days ago          268.9 MB
```

<span data-ttu-id="cd232-164">与开发映像相比，生产或发行映像的大小可能会更小，但是通过使用卷映射，调试程序和应用程序实际会从本地计算机运行，而不是在容器中。</span><span class="sxs-lookup"><span data-stu-id="cd232-164">There may be an expectation for the production or release image to be smaller in size by comparison to the **dev** image; however, through the use of the volume mapping, the debugger and application were actually being run from your local machine and not within the container.</span></span> <span data-ttu-id="cd232-165">**最新**映像已打包在主机计算机上运行应用程序所需的整个应用程序代码，因此增量为应用程序代码的大小。</span><span class="sxs-lookup"><span data-stu-id="cd232-165">The **latest** image has packaged the entire application code needed to run the application on a host machine, therefore the delta is the size of your application code.</span></span>
