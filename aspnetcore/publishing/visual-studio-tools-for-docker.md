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
# <a name="visual-studio-tools-for-docker"></a>Visual Studio Tools for Docker

含 [Docker for Windows](https://docs.docker.com/docker-for-windows/install/) 的 [Microsoft Visual Studio 2017](https://www.visualstudio.com/) 支持生成、调试和运行.NET Framework、.NET Core Web 以及使用 Windows 和 Linux 容器的控制台应用程序。

## <a name="prerequisites"></a>先决条件

- 含 .NET Core 工作负载的 [Microsoft Visual Studio 2017](https://www.visualstudio.com/)
- [Docker for Windows](https://docs.docker.com/docker-for-windows/install/)

## <a name="installation-and-setup"></a>安装和设置

使用 .NET Core 工作负载安装 [Microsoft Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio)。

要安装 Docker，请查看 [Docker for Windows：安装须知](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)了解相关信息，并安装 [Docker for Windows](https://docs.docker.com/docker-for-windows/install/)。

所需的配置是在 Docker for Windows 中安装 **[Shared Drives](https://docs.docker.com/docker-for-windows/#shared-drives)**。 卷映射和调试支持需要此设置。

右键单击系统托盘中的 Docker 图标，单击“设置”，再选择“共享驱动器”。 选择 Docker 用来存储文件并应用更改的驱动器。

![Shared Drives](./visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

## <a name="create-an-aspnet-web-application-and-add-docker-support"></a>创建 ASP.NET Web 应用程序并添加 Docker 支持

使用 Visual Studio，创建新的 ASP.NET Core Web 应用程序。 当加载应用程序时，从“项目菜单”中选择“添加 Docker 支持”，或者右键单击“解决方案资源管理器”中的项目，然后选择“添加” > “Docker 支持”。

*项目菜单*

![项目添加 Docker 支持](./visual-studio-tools-for-docker/_static/project-add-docker-support.png)

*项目上下文菜单*

![右键单击“添加 Docker 支持”](./visual-studio-tools-for-docker/_static/right-click-add-docker-support.png)

向项目添加 Docker 支持后，可以选择 Windows 或 Linux 容器。 （Docker 主机必须运行类型相同的容器。 如果需要更改正在运行的 Docker 实例中的容器类型，请右键单击系统托盘中的“Docker”图标，再选择“切换到 Windows 容器”或“切换到 Linux 容器”。） 

下列文件已添加到该项目中：

- **Dockerfile**：适用于 ASP.NET Core 应用程序的 Docker 文件基于 [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore) 映像。 此映像包括 ASP.NET Core NuGet 包，已对此包进行了预实时编译，以提高启动性能。 生成 .NET Core 控制台应用程序时，Dockerfile 将引用最新的 [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet) 映像。   
- **docker-compose.yml**：基本 Docker Compose 文件，用于定义要通过 docker-compose build/run 生成和运行的映像集合。   
- **docker compose.dev.debug.yml**：配置设置为调试时其他具有用于迭代更改的 docker-compose 文件。 Visual Studio 将调用 -f docker-compose.yml -f docker-compose.dev.debug.yml 以将它们合并到一起。 此组成文件可供 Visual Studio 开发工具使用。   
- **docker compose.dev.release.yml**：调试发布定义的其他 Docker Compose 文件。 它将卷装载调试器，使它不会更改生产映像的内容。  

*docker-compose.yml* 文件包含运行项目时创建的映像的名称。 

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

在此示例中，当应用程序以**调试**模式运行以及 `user/hellodockertools:latest` 处于**发布**模式时，`image: user/hellodockertools${TAG}` 将生成映像 `user/hellodockertools:dev`。 

如果计划要将映像推送到注册表，则需要将 `user` 更改为 [Docker Hub](https://hub.docker.com/) 用户名。 例如 `spboyer/hellodockertools`，或更改为专用注册表 URL `privateregistry.domain.com/`，这具体要取决于配置。

### <a name="debugging"></a>调试

在工具栏的调试下拉列表中选择“Docker”，然后使用 F5 启动调试应用程序。 

- 已获取 *Microsoft/aspnetcore* 映像（如果缓存中尚不存在）
- 将 *ASPNETCORE_ENVIRONMENT* 设置为“在容器内开发”
- 已公开端口 80，并已映射为 localhost 动态分配的端口。 该端口由 docker 主机确定，并且可以使用 docker ps 进行查询。 
- 将应用程序复制到容器
- 使用动态分配端口，通过带有附加到容器的调试程序启动默认浏览器。 

生成的结果 Docker 映像是应用程序的开发映像，而 Microsoft/aspnetcore 映像则是基本映像。

注意：因为调试配置使用卷装载提供迭代体验，因此，开发映像中的应用内容为空。 若要推送映像，请使用“发行”配置。

```console
REPOSITORY                  TAG         IMAGE ID            CREATED         SIZE
spboyer/hellodockertools    dev         0b6e2e44b3df        4 minutes ago   268.9 MB
microsoft/aspnetcore        1.0.1       189ad4312ce7        5 days ago      268.9 MB
```

该应用程序正在使用容器运行，可以通过运行 `docker ps` 命令进行查看。

```console
CONTAINER ID        IMAGE                          COMMAND               CREATED             STATUS              PORTS                   NAMES
3f240cf686c9        spboyer/hellodockertools:dev   "tail -f /dev/null"   4 minutes ago       Up 4 minutes        0.0.0.0:32769->80/tcp   hellodockertools_hellodockertools_1
```

### <a name="edit-and-continue"></a>编辑并继续

对静态文件和/或 razor 模板文件 (.cshtml) 所做的更改会自动更新，无需执行编译步骤。 进行更改，保存并在浏览器中点击“刷新”以查看更新。  

对代码文件的修改需要在容器内进行编译和重新启动 Kestrel。 更改后，使用 CTRL+F5 执行该过程并在容器内启动应用程序。 不可以重新生成或停止 Docker 容器；在命令行中使用 `docker ps` 命令，可以看到原始容器仍与 10 分钟前一样正在运行。 

```console
CONTAINER ID        IMAGE                          COMMAND               CREATED             STATUS              PORTS                   NAMES
3f240cf686c9        spboyer/hellodockertools:dev   "tail -f /dev/null"   10 minutes ago      Up 10 minutes       0.0.0.0:32769->80/tcp   hellodockertools_hellodockertools_1
```

### <a name="publishing-docker-images"></a>发布 Docker 映像

完成应用程序的开发和调试循环后，Visual Studio Tools for Docker 将帮助创建应用程序的生产映像。 将调试下拉列表更改为“发行”，然后生成应用程序。 此工具将生成具有 `:latest` 标签的映像，可以将其推送到私有注册表或 Docker Hub。 

使用 `docker images` 命令可以查看映像的列表。

```console
REPOSITORY                 TAG                 IMAGE ID            CREATED             SIZE
spboyer/hellodockertools   latest              8184ae38ba91        5 seconds ago       278.4 MB
spboyer/hellodockertools   dev                 0b6e2e44b3df        About an hour ago   268.9 MB
microsoft/aspnetcore       1.0.1               189ad4312ce7        5 days ago          268.9 MB
```

与开发映像相比，生产或发行映像的大小可能会更小，但是通过使用卷映射，调试程序和应用程序实际会从本地计算机运行，而不是在容器中。 **最新**映像已打包在主机计算机上运行应用程序所需的整个应用程序代码，因此增量为应用程序代码的大小。
