---
title: "使用 Apache 在 Linux 上托管 ASP.NET Core"
description: "了解如何在 CentOS 上将 Apache 设置为反向代理服务器，以将 HTTP 流量重定向到在 Kestrel 上运行的 ASP.NET Core Web 应用程序。"
keywords: "ASP.NET Core,Apache,CentOS,反向代理,Linux,mod_proxy,httpd,托管"
author: spboyer
ms.author: spboyer
manager: wpickett
ms.date: 10/19/2016
ms.topic: article
ms.assetid: fa9b0cb7-afb3-4361-9e7e-33afffeaca0c
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/apache-proxy
ms.openlocfilehash: 34ede2fdebe0e9516f62e39618e7adba3c8c89db
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2017
---
# <a name="set-up-a-hosting-environment-for-aspnet-core-on-linux-with-apache-and-deploy-to-it"></a><span data-ttu-id="d5f13-104">使用 Apache 在 Linux 上为 ASP.NET Core 设置托管环境，并对其进行部署</span><span class="sxs-lookup"><span data-stu-id="d5f13-104">Set up a hosting environment for ASP.NET Core on Linux with Apache, and deploy to it</span></span>

<span data-ttu-id="d5f13-105">作者：[Shayne Boyer](https://github.com/spboyer)</span><span class="sxs-lookup"><span data-stu-id="d5f13-105">By [Shayne Boyer](https://github.com/spboyer)</span></span>

<span data-ttu-id="d5f13-106">Apache 是非常热门的 HTTP 服务器，并且与 nginx 类似，可以将它配置为代理以重定向 HTTP 流量。</span><span class="sxs-lookup"><span data-stu-id="d5f13-106">Apache is a very popular HTTP server and can be configured as a proxy to redirect HTTP traffic similar to nginx.</span></span> <span data-ttu-id="d5f13-107">在本指南中，我们将了解如何在 CentOS 7 上设置 Apache 并使用它作为反向代理来接收传入连接，并将这些连接重定向到 Kestrel 上运行的 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="d5f13-107">In this guide, we will learn how to set up Apache on CentOS 7 and use it as a reverse proxy to welcome incoming connections and redirect them to the ASP.NET Core application running on Kestrel.</span></span> <span data-ttu-id="d5f13-108">出于此目的，我们将使用“mod_proxy”扩展和其他相关的 Apache 模块。</span><span class="sxs-lookup"><span data-stu-id="d5f13-108">For this purpose, we will use the *mod_proxy* extension and other related Apache modules.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d5f13-109">先决条件</span><span class="sxs-lookup"><span data-stu-id="d5f13-109">Prerequisites</span></span>

1. <span data-ttu-id="d5f13-110">运行 CentOS 7 的服务器，使用具有 sudo 特权的标准用户帐户。</span><span class="sxs-lookup"><span data-stu-id="d5f13-110">A server running CentOS 7, with a standard user account with sudo privilege.</span></span>
2. <span data-ttu-id="d5f13-111">现有 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="d5f13-111">An existing ASP.NET Core application.</span></span> 

## <a name="publish-your-application"></a><span data-ttu-id="d5f13-112">发布应用程序</span><span class="sxs-lookup"><span data-stu-id="d5f13-112">Publish your application</span></span>

<span data-ttu-id="d5f13-113">从开发环境运行 `dotnet publish -c Release` 将应用程序打包到可在服务器上运行的自包含目录中。</span><span class="sxs-lookup"><span data-stu-id="d5f13-113">Run `dotnet publish -c Release` from your development environment to package your application into a self-contained directory that can run on your server.</span></span> <span data-ttu-id="d5f13-114">然后，必须使用 SCP、FTP 或其他文件传输方法将发布的应用程序复制到服务器。</span><span class="sxs-lookup"><span data-stu-id="d5f13-114">The published application must then be copied to the server using SCP, FTP or other file transfer method.</span></span> 

> [!NOTE]
> <span data-ttu-id="d5f13-115">在生产部署方案中，持续集成工作流会执行发布应用程序并将资产复制到服务器的工作。</span><span class="sxs-lookup"><span data-stu-id="d5f13-115">Under a production deployment scenario, a continuous integration workflow does the work of publishing the application and copying the assets to the server.</span></span> 

## <a name="configure-a-proxy-server"></a><span data-ttu-id="d5f13-116">配置代理服务器</span><span class="sxs-lookup"><span data-stu-id="d5f13-116">Configure a proxy server</span></span>

<span data-ttu-id="d5f13-117">反向代理是为动态 Web 应用程序提供服务的常见设置。</span><span class="sxs-lookup"><span data-stu-id="d5f13-117">A reverse proxy is a common setup for serving dynamic web applications.</span></span> <span data-ttu-id="d5f13-118">反向代理终止 HTTP 请求，并将其转发到 ASP.NET 应用程序。</span><span class="sxs-lookup"><span data-stu-id="d5f13-118">The reverse proxy terminates the HTTP request and forwards it to the ASP.NET application.</span></span>

<span data-ttu-id="d5f13-119">代理服务器将客户端请求转发到另一个服务器，而不是自身实现这些请求。</span><span class="sxs-lookup"><span data-stu-id="d5f13-119">A proxy server is one which forwards client requests to another server instead of fulfilling them itself.</span></span> <span data-ttu-id="d5f13-120">反向代理转发到固定的目标，通常代表任意客户端。</span><span class="sxs-lookup"><span data-stu-id="d5f13-120">A reverse proxy forwards to a fixed destination, typically on behalf of arbitrary clients.</span></span> <span data-ttu-id="d5f13-121">在本指南中，Apache 被配置为反向代理，在 Kestrel 为 ASP.NET Core 应用程序提供服务的同一服务器上运行。</span><span class="sxs-lookup"><span data-stu-id="d5f13-121">In this guide, Apache is being configured as the reverse proxy running on the same server that Kestrel is serving the ASP.NET Core application.</span></span> 

<span data-ttu-id="d5f13-122">应用程序的每个部分都可位于单独的物理计算机、Docker 容器或配置组合中，具体取决于体系结构需求或限制。</span><span class="sxs-lookup"><span data-stu-id="d5f13-122">Each piece of the application can exist on separate physical machines, Docker containers, or a combination of configurations depending on your architectural needs or restrictions.</span></span>

### <a name="install-apache"></a><span data-ttu-id="d5f13-123">安装 Apache</span><span class="sxs-lookup"><span data-stu-id="d5f13-123">Install Apache</span></span>

<span data-ttu-id="d5f13-124">在 CentOS 上安装 Apache Web 服务器是单个命令，但我们先来更新包。</span><span class="sxs-lookup"><span data-stu-id="d5f13-124">Installing the Apache web server on CentOS is a single command, but first let's update our packages.</span></span>

```bash
    sudo yum update -y
```

<span data-ttu-id="d5f13-125">这可确保所有安装的包已更新到最新版本。</span><span class="sxs-lookup"><span data-stu-id="d5f13-125">This ensures that all of the installed packages are updated to their latest version.</span></span> <span data-ttu-id="d5f13-126">使用 `yum` 安装 Apache</span><span class="sxs-lookup"><span data-stu-id="d5f13-126">Install Apache using `yum`</span></span>

```bash
    sudo yum -y install httpd mod_ssl
```

<span data-ttu-id="d5f13-127">输出应反映与以下类似的内容。</span><span class="sxs-lookup"><span data-stu-id="d5f13-127">The output should reflect something similar to the following.</span></span>

```bash
    Downloading packages:
    httpd-2.4.6-40.el7.centos.4.x86_64.rpm               | 2.7 MB  00:00:01     
    Running transaction check
    Running transaction test
    Transaction test succeeded
    Running transaction
    Installing : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 
    Verifying  : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 

    Installed:
    httpd.x86_64 0:2.4.6-40.el7.centos.4                                                                           

    Complete!
```

> [!NOTE]
> <span data-ttu-id="d5f13-128">在此示例中，输出反映了 httpd.86_64，因为 CentOS 7 版本是 64 位的。</span><span class="sxs-lookup"><span data-stu-id="d5f13-128">In this example the output reflects httpd.86_64 since the CentOS 7 version is 64 bit.</span></span> <span data-ttu-id="d5f13-129">输出可能因具体的服务器而有所不同。</span><span class="sxs-lookup"><span data-stu-id="d5f13-129">The output may be different for your server.</span></span> <span data-ttu-id="d5f13-130">若要验证 Apache 的安装位置，请从命令提示符运行 `whereis httpd`。</span><span class="sxs-lookup"><span data-stu-id="d5f13-130">To verify where Apache is installed, run `whereis httpd` from a command prompt.</span></span> 

### <a name="configure-apache-for-reverse-proxy"></a><span data-ttu-id="d5f13-131">配置 Apache 用于反向代理</span><span class="sxs-lookup"><span data-stu-id="d5f13-131">Configure Apache for reverse proxy</span></span>

<span data-ttu-id="d5f13-132">Apache 的配置文件位于 `/etc/httpd/conf.d/` 目录内。</span><span class="sxs-lookup"><span data-stu-id="d5f13-132">Configuration files for Apache are located within the `/etc/httpd/conf.d/` directory.</span></span> <span data-ttu-id="d5f13-133">除了 `/etc/httpd/conf.modules.d/` 中的模块配置文件外（其中包含加载模块所需的任何配置文件），将对任何带 .conf 扩展名的文件按字母顺序进行处理。</span><span class="sxs-lookup"><span data-stu-id="d5f13-133">Any file with the **.conf** extension will be processed in alphabetical order in addition to the module configuration files in `/etc/httpd/conf.modules.d/`, which contains any configuration files necessary to load modules.</span></span>

<span data-ttu-id="d5f13-134">创建应用的配置文件，在此示例中我们称之为 `hellomvc.conf`</span><span class="sxs-lookup"><span data-stu-id="d5f13-134">Create a configuration file for your app, for this example we'll call it `hellomvc.conf`</span></span>

```text
    <VirtualHost *:80>
        ProxyPreserveHost On
        ProxyPass / http://127.0.0.1:5000/
        ProxyPassReverse / http://127.0.0.1:5000/
        ErrorLog /var/log/httpd/hellomvc-error.log
        CustomLog /var/log/httpd/hellomvc-access.log common
    </VirtualHost>
```

<span data-ttu-id="d5f13-135">VirtualHost 节点（一个文件中可能有多个或在多个文件中的一个服务器上可能有多个）已设置为侦听使用端口 80 的任何 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="d5f13-135">The *VirtualHost* node, of which there can be multiple in a file or on a server in many files, is set to listen on any IP address using port 80.</span></span> <span data-ttu-id="d5f13-136">接下来的两行都设置为将在根处接收的所有请求传递到计算机 127.0.0.1 端口 5000，并按相反的顺序传递。</span><span class="sxs-lookup"><span data-stu-id="d5f13-136">The next two lines are set to pass all requests received at the root to the machine 127.0.0.1 port 5000 and in reverse.</span></span> <span data-ttu-id="d5f13-137">如果需要双向通信，则同时需要“ProxyPass”和“ProxyPassReverse”这两个设置。</span><span class="sxs-lookup"><span data-stu-id="d5f13-137">For there to be bi-directional communication, both settings *ProxyPass* and *ProxyPassReverse* are required.</span></span>

<span data-ttu-id="d5f13-138">可使用“ErrorLog”和“CustomLog”指令配置每个 VirtualHost 的日志记录。</span><span class="sxs-lookup"><span data-stu-id="d5f13-138">Logging can be configured per VirtualHost using *ErrorLog* and *CustomLog* directives.</span></span> <span data-ttu-id="d5f13-139">“ErrorLog”是服务器记录错误的位置，“CustomLog”则设置文件名和日志文件的格式。</span><span class="sxs-lookup"><span data-stu-id="d5f13-139">*ErrorLog* is the location where the server will log errors and *CustomLog* sets the filename and format of log file.</span></span> <span data-ttu-id="d5f13-140">在我们的示例中，这是记录请求信息的位置。</span><span class="sxs-lookup"><span data-stu-id="d5f13-140">In our case this is where request information will be logged.</span></span> <span data-ttu-id="d5f13-141">每个请求将各占一行。</span><span class="sxs-lookup"><span data-stu-id="d5f13-141">There will be one line for each request.</span></span>

<span data-ttu-id="d5f13-142">保存文件，并测试配置。</span><span class="sxs-lookup"><span data-stu-id="d5f13-142">Save the file, and test the configuration.</span></span> <span data-ttu-id="d5f13-143">如果一切正常，响应应为 `Syntax [OK]`。</span><span class="sxs-lookup"><span data-stu-id="d5f13-143">If everything passes, the response should be `Syntax [OK]`.</span></span>

```bash
    sudo service httpd configtest
```

<span data-ttu-id="d5f13-144">重启 Apache。</span><span class="sxs-lookup"><span data-stu-id="d5f13-144">Restart Apache.</span></span>

```text
    sudo systemctl restart httpd
    sudo systemctl enable httpd
```

## <a name="monitoring-our-application"></a><span data-ttu-id="d5f13-145">监视应用程序</span><span class="sxs-lookup"><span data-stu-id="d5f13-145">Monitoring our application</span></span>

<span data-ttu-id="d5f13-146">Apache 现在已设置为将对 `http://localhost:80` 发起的请求转发到运行在 `http://127.0.0.1:5000` 处的 Kestrel 上的 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="d5f13-146">Apache is now setup to forward requests made to `http://localhost:80` on to the ASP.NET Core application running on Kestrel at `http://127.0.0.1:5000`.</span></span>  <span data-ttu-id="d5f13-147">但是，未将 Apache 设置为管理 Kestrel 进程。</span><span class="sxs-lookup"><span data-stu-id="d5f13-147">However, Apache is not set up to manage the Kestrel process.</span></span> <span data-ttu-id="d5f13-148">我们将使用 systemd，并创建服务文件以启动和监视基础 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="d5f13-148">We will use *systemd* and create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="d5f13-149">systemd 是一个初始系统，可以提供启动、停止和管理进程的许多强大的功能。</span><span class="sxs-lookup"><span data-stu-id="d5f13-149">*systemd* is an init system that provides many powerful features for starting, stopping and managing processes.</span></span> 


### <a name="create-the-service-file"></a><span data-ttu-id="d5f13-150">创建服务文件</span><span class="sxs-lookup"><span data-stu-id="d5f13-150">Create the service file</span></span>

<span data-ttu-id="d5f13-151">创建服务定义文件</span><span class="sxs-lookup"><span data-stu-id="d5f13-151">Create the service definition file</span></span> 

```bash
    sudo nano /etc/systemd/system/kestrel-hellomvc.service
```

<span data-ttu-id="d5f13-152">应用程序的一个示例服务文件：</span><span class="sxs-lookup"><span data-stu-id="d5f13-152">An example service file for our application.</span></span>

```text
[Unit]
    Description=Example .NET Web API Application running on CentOS 7

    [Service]
    WorkingDirectory=/var/aspnetcore/hellomvc
    ExecStart=/usr/local/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
    Restart=always
    # Restart service after 10 seconds if dotnet service crashes
    RestartSec=10
    SyslogIdentifier=dotnet-example
    User=apache
    Environment=ASPNETCORE_ENVIRONMENT=Production 

    [Install]
    WantedBy=multi-user.target
```

> [!NOTE]
> <span data-ttu-id="d5f13-153">**用户** - 如果用户 apache 未被你的配置所使用，则必须先在此处创建定义的用户，并为该用户提供适当的文件所有权</span><span class="sxs-lookup"><span data-stu-id="d5f13-153">**User** -- If the user *apache* is not used by your configuration, the user defined here must be created first and given proper ownership for files</span></span>

<span data-ttu-id="d5f13-154">保存该文件并启用该服务。</span><span class="sxs-lookup"><span data-stu-id="d5f13-154">Save the file and enable the service.</span></span>

```bash
    systemctl enable kestrel-hellomvc.service
```

<span data-ttu-id="d5f13-155">启用该服务，并确认它正在运行。</span><span class="sxs-lookup"><span data-stu-id="d5f13-155">Start the service and verify that it is running.</span></span>

```
    systemctl start kestrel-hellomvc.service
    systemctl status kestrel-hellomvc.service

    ● kestrel-hellomvc.service - Example .NET Web API Application running on CentOS 7
        Loaded: loaded (/etc/systemd/system/kestrel-hellomvc.service; enabled)
        Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
    Main PID: 9021 (dotnet)
        CGroup: /system.slice/kestrel-hellomvc.service
                └─9021 /usr/local/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
```

<span data-ttu-id="d5f13-156">在配置了反向代理并通过 systemd 管理 Kestrel 后，Web 应用程序现已完全配置，并能在本地计算机上的浏览器中从 `http://localhost` 进行访问。</span><span class="sxs-lookup"><span data-stu-id="d5f13-156">With the reverse proxy configured and Kestrel managed through systemd, the web application is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="d5f13-157">检查响应标头，服务器仍会显示由 Kestrel 所提供服务的 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="d5f13-157">Inspecting the response headers, the **Server** still shows the ASP.NET Core application being served by Kestrel.</span></span>

```text
    HTTP/1.1 200 OK
    Date: Tue, 11 Oct 2016 16:22:23 GMT
    Server: Kestrel
    Keep-Alive: timeout=5, max=98
    Connection: Keep-Alive
    Transfer-Encoding: chunked
```

### <a name="viewing-logs"></a><span data-ttu-id="d5f13-158">查看日志</span><span class="sxs-lookup"><span data-stu-id="d5f13-158">Viewing logs</span></span>

<span data-ttu-id="d5f13-159">由于使用 Kestrel 的 Web 应用程序是使用 systemd 进行管理的，因此所有事件和进程都被记录到集中日志。</span><span class="sxs-lookup"><span data-stu-id="d5f13-159">Since the web application using Kestrel is managed using systemd, all events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="d5f13-160">但是，此日志包含由 systemd 管理的所有服务和进程的全部条目。</span><span class="sxs-lookup"><span data-stu-id="d5f13-160">However, this journal includes all entries for all services and processes managed by systemd.</span></span> <span data-ttu-id="d5f13-161">若要查看特定于 `kestrel-hellomvc.service` 的项，请使用以下命令。</span><span class="sxs-lookup"><span data-stu-id="d5f13-161">To view the `kestrel-hellomvc.service` specific items, use the following command.</span></span>

```bash
    sudo journalctl -fu kestrel-hellomvc.service
```

<span data-ttu-id="d5f13-162">有关进一步筛选，使用时间选项（如 `--since today`、`--until 1 hour ago`）或这些选项的组合可以减少返回的条目数。</span><span class="sxs-lookup"><span data-stu-id="d5f13-162">For further filtering, time options such as `--since today`, `--until 1 hour ago` or a combination of these can reduce the amount of entries returned.</span></span>

```bash
    sudo journalctl -fu kestrel-hellomvc.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="securing-our-application"></a><span data-ttu-id="d5f13-163">保护应用程序</span><span class="sxs-lookup"><span data-stu-id="d5f13-163">Securing our application</span></span>

### <a name="configure-firewall"></a><span data-ttu-id="d5f13-164">配置防火墙</span><span class="sxs-lookup"><span data-stu-id="d5f13-164">Configure firewall</span></span>

<span data-ttu-id="d5f13-165">Firewalld 是管理防火墙的动态守护程序，支持网络区域，但是你仍可以使用 iptable 管理端口和数据包筛选。</span><span class="sxs-lookup"><span data-stu-id="d5f13-165">*Firewalld* is a dynamic daemon to manage firewall with support for network zones, although you can still use iptables to manage ports and packet filtering.</span></span> <span data-ttu-id="d5f13-166">默认情况下应安装 Firewalld，可以使用 `yum` 安装包或进行验证。</span><span class="sxs-lookup"><span data-stu-id="d5f13-166">Firewalld should be installed by default, `yum` can be used to install the package or verify.</span></span>

```bash
    sudo yum install firewalld -y
```

<span data-ttu-id="d5f13-167">通过使用 `firewalld`，可以仅打开应用程序所需的端口。</span><span class="sxs-lookup"><span data-stu-id="d5f13-167">Using `firewalld` you can open only the ports needed for the application.</span></span> <span data-ttu-id="d5f13-168">在此示例中，使用的是端口 80 和 443。</span><span class="sxs-lookup"><span data-stu-id="d5f13-168">In this case, port 80 and 443 are used.</span></span> <span data-ttu-id="d5f13-169">以下命令将这些端口永久设置为打开。</span><span class="sxs-lookup"><span data-stu-id="d5f13-169">The following commands permanently sets these to open.</span></span>

```bash
    sudo firewall-cmd --add-port=80/tcp --permanent
    sudo firewall-cmd --add-port=443/tcp --permanent
```

<span data-ttu-id="d5f13-170">重新加载防火墙设置，并检查默认区域中可用的服务和端口。</span><span class="sxs-lookup"><span data-stu-id="d5f13-170">Reload the firewall settings, and check the available services and ports in the default zone.</span></span> <span data-ttu-id="d5f13-171">通过检查 `firewall-cmd -h` 获取可用选项</span><span class="sxs-lookup"><span data-stu-id="d5f13-171">Options are available by inspecting `firewall-cmd -h`</span></span>

```bash 
    sudo firewall-cmd --reload
    sudo firewall-cmd --list-all
```

```bash
    public (default, active)
    interfaces: eth0
    sources: 
    services: dhcpv6-client
    ports: 443/tcp 80/tcp
    masquerade: no
    forward-ports: 
    icmp-blocks: 
    rich rules: 
```

### <a name="ssl-configuration"></a><span data-ttu-id="d5f13-172">SSL 配置</span><span class="sxs-lookup"><span data-stu-id="d5f13-172">SSL configuration</span></span>

<span data-ttu-id="d5f13-173">若要配置 Apache 用于 SSL，需使用 mod_ssl 模块。</span><span class="sxs-lookup"><span data-stu-id="d5f13-173">To configure Apache for SSL, the mod_ssl module is used.</span></span>  <span data-ttu-id="d5f13-174">这在最初安装 `httpd` 模块时已经安装。</span><span class="sxs-lookup"><span data-stu-id="d5f13-174">This was installed initially when we installed the `httpd` module.</span></span> <span data-ttu-id="d5f13-175">如果它已丢失或未安装，请使用 yum 将其添加到配置中。</span><span class="sxs-lookup"><span data-stu-id="d5f13-175">If it was missed or not installed, use yum to add it to your configuration.</span></span>

```bash
    sudo yum install mod_ssl
```
<span data-ttu-id="d5f13-176">若要强制实施 SSL，请安装 `mod_rewrite`</span><span class="sxs-lookup"><span data-stu-id="d5f13-176">To enforce SSL, install `mod_rewrite`</span></span>

```bash
    sudo yum install mod_rewrite
```

<span data-ttu-id="d5f13-177">需要对为此示例创建的 `hellomvc.conf` 文件进行修改才能启用重写，以及为 HTTPS 添加新的“VirtualHost”部分。</span><span class="sxs-lookup"><span data-stu-id="d5f13-177">The `hellomvc.conf` file that was created for this example needs to be modified to enable the rewrite as well as adding the new **VirtualHost** section for HTTPS.</span></span>

```text
    <VirtualHost *:80>
        RewriteEngine On
        RewriteCond %{HTTPS} !=on
        RewriteRule ^/?(.*) https://%{SERVER_NAME}/ [R,L]
    </VirtualHost>

    <VirtualHost *:443>
        ProxyPreserveHost On
        ProxyPass / http://127.0.0.1:5000/
        ProxyPassReverse / http://127.0.0.1:5000/
        ErrorLog /var/log/httpd/hellomvc-error.log
        CustomLog /var/log/httpd/hellomvc-access.log common
        SSLEngine on
        SSLProtocol all -SSLv2
        SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
        SSLCertificateFile /etc/pki/tls/certs/localhost.crt
        SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
    </VirtualHost>    
```

> [!NOTE]
> <span data-ttu-id="d5f13-178">此示例中使用了本地生成的证书。</span><span class="sxs-lookup"><span data-stu-id="d5f13-178">This example is using a locally generated certificate.</span></span> <span data-ttu-id="d5f13-179">SSLCertificateFile 应为域名的主证书文件。</span><span class="sxs-lookup"><span data-stu-id="d5f13-179">**SSLCertificateFile** should be your primary certificate file for your domain name.</span></span> <span data-ttu-id="d5f13-180">SSLCertificateKeyFile 应为创建 CSR 时生成的密钥文件。</span><span class="sxs-lookup"><span data-stu-id="d5f13-180">**SSLCertificateKeyFile** should be the key file generated when you created the CSR.</span></span> <span data-ttu-id="d5f13-181">SSLCertificateChainFile 应为证书颁发机构提供的中间证书文件（如有）</span><span class="sxs-lookup"><span data-stu-id="d5f13-181">**SSLCertificateChainFile** should be the intermediate certificate file (if any) that was supplied by your certificate authority</span></span>

<span data-ttu-id="d5f13-182">保存文件，并测试配置。</span><span class="sxs-lookup"><span data-stu-id="d5f13-182">Save the file, and test the configuration.</span></span>

```bash
    sudo service httpd configtest
```

<span data-ttu-id="d5f13-183">重启 Apache。</span><span class="sxs-lookup"><span data-stu-id="d5f13-183">Restart Apache.</span></span>

```bash
    sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a><span data-ttu-id="d5f13-184">其他 Apache 建议</span><span class="sxs-lookup"><span data-stu-id="d5f13-184">Additional Apache suggestions</span></span>

### <a name="additional-headers"></a><span data-ttu-id="d5f13-185">其他标头</span><span class="sxs-lookup"><span data-stu-id="d5f13-185">Additional Headers</span></span> 
<span data-ttu-id="d5f13-186">为了防止恶意攻击，应对一些标头进行修改或添加一些标头。</span><span class="sxs-lookup"><span data-stu-id="d5f13-186">In order to secure against malicious attacks there are a few headers that should either be modified or added.</span></span> <span data-ttu-id="d5f13-187">确保已安装 `mod_headers` 模块。</span><span class="sxs-lookup"><span data-stu-id="d5f13-187">Ensure that the `mod_headers` module is installed.</span></span>

```bash
    sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking"></a><span data-ttu-id="d5f13-188">保护 Apache 免受点击劫持的侵害</span><span class="sxs-lookup"><span data-stu-id="d5f13-188">Secure Apache from clickjacking</span></span>
<span data-ttu-id="d5f13-189">点击劫持是一种可收集受感染用户的点击量的恶意技术。</span><span class="sxs-lookup"><span data-stu-id="d5f13-189">Clickjacking is a malicious technique to collect an infected user's clicks.</span></span> <span data-ttu-id="d5f13-190">点击劫持诱使受害者（访问者）点击受感染的网站。</span><span class="sxs-lookup"><span data-stu-id="d5f13-190">Clickjacking tricks the victim (visitor) into clicking on an infected site.</span></span> <span data-ttu-id="d5f13-191">使用 X-FRAME-OPTIONS 保护站点。</span><span class="sxs-lookup"><span data-stu-id="d5f13-191">Use X-FRAME-OPTIONS to secure your site.</span></span>

<span data-ttu-id="d5f13-192">编辑 httpd.conf 文件。</span><span class="sxs-lookup"><span data-stu-id="d5f13-192">Edit the httpd.conf file.</span></span>

```bash
    sudo nano /etc/httpd/conf/httpd.conf
```

<span data-ttu-id="d5f13-193">添加行 `Header append X-FRAME-OPTIONS "SAMEORIGIN"` 并保存文件，然后重启 Apache。</span><span class="sxs-lookup"><span data-stu-id="d5f13-193">Add the line `Header append X-FRAME-OPTIONS "SAMEORIGIN"` and save the file, then restart Apache.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="d5f13-194">MIME 类型探查</span><span class="sxs-lookup"><span data-stu-id="d5f13-194">MIME-type sniffing</span></span>

<span data-ttu-id="d5f13-195">此标头阻止 Internet Explorer 避开声明的内容类型对响应进行 MIME 探查，因为标头会指示浏览器不要替代响应内容类型。</span><span class="sxs-lookup"><span data-stu-id="d5f13-195">This header prevents Internet Explorer from MIME-sniffing a response away from the declared content-type as the header instructs the browser not to override the response content type.</span></span> <span data-ttu-id="d5f13-196">凭借 nosniff 选项，如果服务器认为内容为文本/html，浏览器则将其呈现为文本/html。</span><span class="sxs-lookup"><span data-stu-id="d5f13-196">With the nosniff option, if the server says the content is text/html, the browser will render it as text/html.</span></span>

<span data-ttu-id="d5f13-197">编辑 httpd.conf 文件。</span><span class="sxs-lookup"><span data-stu-id="d5f13-197">Edit the httpd.conf file.</span></span>

```bash
    sudo nano /etc/httpd/conf/httpd.conf
```

<span data-ttu-id="d5f13-198">添加行 `Header set X-Content-Type-Options "nosniff"` 并保存文件，然后重启 Apache。</span><span class="sxs-lookup"><span data-stu-id="d5f13-198">Add the line `Header set X-Content-Type-Options "nosniff"` and save the file, then restart Apache.</span></span>

### <a name="load-balancing"></a><span data-ttu-id="d5f13-199">负载平衡</span><span class="sxs-lookup"><span data-stu-id="d5f13-199">Load Balancing</span></span> 

<span data-ttu-id="d5f13-200">此示例演示如何在同一实例计算机上的 CentOS 7 和 Kestrel 上设置和配置 Apache。</span><span class="sxs-lookup"><span data-stu-id="d5f13-200">This example shows how to setup and configure Apache on CentOS 7 and Kestrel on the same instance machine.</span></span>  <span data-ttu-id="d5f13-201">然而，为了不出现单一故障点；使用 mod_proxy_balancer 并修改 VirtualHost 可实现在 Apache 代理服务器后面管理 Web 应用的多个实例。</span><span class="sxs-lookup"><span data-stu-id="d5f13-201">However, in order to not have a single point of failure; using *mod_proxy_balancer* and modifying the VirtualHost would allow for managing mutliple instances of the web applications behind the Apache proxy server.</span></span>

```bash
    sudo yum install mod_proxy_balancer
```

<span data-ttu-id="d5f13-202">在配置文件中，`hellomvc` 应用的其他实例已设置为在端口 5001 上运行，并且使用具有两个成员的均衡器配置对 Proxy 部分进行了设置以负载均衡 byrequests。</span><span class="sxs-lookup"><span data-stu-id="d5f13-202">In the configuration file, an additional instance of the `hellomvc` app has been setup to run on port 5001 and the *Proxy* section has been set with a balancer configuration with two members to load balance *byrequests*.</span></span>

```text
    <VirtualHost *:80>
        RewriteEngine On
        RewriteCond %{HTTPS} !=on
        RewriteRule ^/?(.*) https://%{SERVER_NAME}/ [R,L]
    </VirtualHost>

    <VirtualHost *:443>
            ProxyPass / balancer://mycluster/ 

            ProxyPassReverse / http://127.0.0.1:5000/
            ProxyPassReverse / http://127.0.0.1:5001/

            <Proxy balancer://mycluster>
                BalancerMember http://127.0.0.1:5000
                BalancerMember http://127.0.0.1:5001 
                ProxySet lbmethod=byrequests
            </Proxy>

            <Location />
                SetHandler balancer
            </Location>
            ErrorLog /var/log/httpd/hellomvc-error.log
            CustomLog /var/log/httpd/hellomvc-access.log common
            SSLEngine on
            SSLProtocol all -SSLv2
            SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
            SSLCertificateFile /etc/pki/tls/certs/localhost.crt
            SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
    </VirtualHost>
```

### <a name="rate-limits"></a><span data-ttu-id="d5f13-203">速率限制</span><span class="sxs-lookup"><span data-stu-id="d5f13-203">Rate Limits</span></span>
<span data-ttu-id="d5f13-204">使用包括在 `htttpd` 模块中的 `mod_ratelimit` 可限制客户端的带宽量。</span><span class="sxs-lookup"><span data-stu-id="d5f13-204">Using `mod_ratelimit`, which is included in the `htttpd` module you can limit the amount of bandwidth of clients.</span></span> 

```bash
    sudo nano /etc/httpd/conf.d/ratelimit.conf
```
<span data-ttu-id="d5f13-205">示例文件将根位置下的带宽限制为 600 KB/秒。</span><span class="sxs-lookup"><span data-stu-id="d5f13-205">The example file limits bandwidth as 600 KB/sec under the root location.</span></span>

```text
    <IfModule mod_ratelimit.c>
        <Location />
            SetOutputFilter RATE_LIMIT
            SetEnv rate-limit 600
        </Location>
    </IfModule>
```
