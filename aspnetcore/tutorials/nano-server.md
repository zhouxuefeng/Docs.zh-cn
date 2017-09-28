---
title: "Nano Server 上的 ASP.NET Core"
author: shirhatti
description: "了解如何采用现有的 ASP.NET Core 应用并将其部署到运行 IIS 的 Nano Server 实例。"
keywords: ASP.NET Core, nano server
ms.author: riande
manager: wpickett
ms.date: 11/04/2016
ms.topic: article
ms.assetid: 50922cf1-ca58-4006-9236-99b7ff2dd0cf
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/nano-server
ms.openlocfilehash: 337cc69ef522452c17cdd6ea4a5e71cd122035dc
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2017
---
# <a name="aspnet-core-with-iis-on-nano-server"></a><span data-ttu-id="277c3-104">ASP.NET Core 与 Nano Server 上运行的 IIS</span><span class="sxs-lookup"><span data-stu-id="277c3-104">ASP.NET Core with IIS on Nano Server</span></span>

<span data-ttu-id="277c3-105">作者：[Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="277c3-105">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

<span data-ttu-id="277c3-106">在本文中，将采用现有的 ASP.NET Core 应用并将其部署到运行 IIS 的 Nano Server 实例。</span><span class="sxs-lookup"><span data-stu-id="277c3-106">In this tutorial, you'll take an existing ASP.NET Core app and deploy it to a Nano Server instance running IIS.</span></span>

## <a name="introduction"></a><span data-ttu-id="277c3-107">介绍</span><span class="sxs-lookup"><span data-stu-id="277c3-107">Introduction</span></span>

<span data-ttu-id="277c3-108">Nano Server 是 Windows Server 2016 中的一个安装选项，它占用内存较小、提供更强的安全性，并提供比服务器核心或完全服务器更出色的服务。</span><span class="sxs-lookup"><span data-stu-id="277c3-108">Nano Server is an installation option in Windows Server 2016, offering a tiny footprint, better security, and better servicing than Server Core or full Server.</span></span> <span data-ttu-id="277c3-109">请参阅官方 [Nano Server 文档](https://docs.microsoft.com/windows-server/get-started/getting-started-with-nano-server)获取更多详细信息，并获取 180 天评估版本的下载链接。</span><span class="sxs-lookup"><span data-stu-id="277c3-109">Please consult the official [Nano Server documentation](https://docs.microsoft.com/windows-server/get-started/getting-started-with-nano-server) for more details and download links for 180 Days evaluation versions.</span></span> 

<span data-ttu-id="277c3-110">可通过三个简单的方法来试用 Nano Server。</span><span class="sxs-lookup"><span data-stu-id="277c3-110">There are three easy ways for you to try out Nano Server.</span></span> <span data-ttu-id="277c3-111">使用 MS 帐户登录时：</span><span class="sxs-lookup"><span data-stu-id="277c3-111">When you sign in with your MS account:</span></span>

1. <span data-ttu-id="277c3-112">可以下载 Windows Server 2016 ISO 文件，并生成 Nano Server 映像。</span><span class="sxs-lookup"><span data-stu-id="277c3-112">You can download the Windows Server 2016 ISO file and build a Nano Server image.</span></span>

2. <span data-ttu-id="277c3-113">下载 Nano Server VHD。</span><span class="sxs-lookup"><span data-stu-id="277c3-113">Download the Nano Server VHD.</span></span>

3. <span data-ttu-id="277c3-114">使用 Azure 库中的 Nano Server 映像在 Azure 中创建 VM。</span><span class="sxs-lookup"><span data-stu-id="277c3-114">Create a VM in Azure using the Nano Server image in the Azure Gallery.</span></span> <span data-ttu-id="277c3-115">如果没有 Azure 帐户，可以获取 30 天免费试用版。</span><span class="sxs-lookup"><span data-stu-id="277c3-115">If you don’t have an Azure account, you can get a free 30-day trial.</span></span>

<span data-ttu-id="277c3-116">在本教程中，我们将使用第二个选项，即 Windows Server 2016 中的预生成 Nano Server VHD。</span><span class="sxs-lookup"><span data-stu-id="277c3-116">In this tutorial, we will be using the 2nd option, the pre-built Nano Server VHD from Windows Server 2016.</span></span>

<span data-ttu-id="277c3-117">在继续阅读本教程之前，需要现有 ASP.NET Core 应用程序的[发布输出](xref:hosting/directory-structure)。</span><span class="sxs-lookup"><span data-stu-id="277c3-117">Before proceeding with this tutorial, you will need the [published output](xref:hosting/directory-structure) of an existing ASP.NET Core application.</span></span> <span data-ttu-id="277c3-118">确保生成的应用程序在 64 位进程中运行。</span><span class="sxs-lookup"><span data-stu-id="277c3-118">Ensure your application is built to run in a **64-bit** process.</span></span>

## <a name="setting-up-the-nano-server-instance"></a><span data-ttu-id="277c3-119">设置 Nano Server 实例</span><span class="sxs-lookup"><span data-stu-id="277c3-119">Setting up the Nano Server instance</span></span>

<span data-ttu-id="277c3-120">在开发计算机上，借助之前下载的 VHD [使用 HYPER-V 创建新虚拟机](https://technet.microsoft.com/library/hh846766.aspx)。</span><span class="sxs-lookup"><span data-stu-id="277c3-120">[Create a new Virtual Machine using Hyper-V](https://technet.microsoft.com/library/hh846766.aspx) on your development machine using the previously downloaded VHD.</span></span> <span data-ttu-id="277c3-121">在登录之前，计算机会要求你设置管理员密码。</span><span class="sxs-lookup"><span data-stu-id="277c3-121">The machine will require you to set an administrator password before logging on.</span></span> <span data-ttu-id="277c3-122">在 VM 控制台中，在首次登录前按 F11 设置密码。</span><span class="sxs-lookup"><span data-stu-id="277c3-122">At the VM console, press F11 to set the password before the first log in.</span></span> <span data-ttu-id="277c3-123">还需要检查新 VM 的 IP 地址，可以通过检查在预配 VM 时所提供的 DHCP 服务器的固定 IP，也可以在 Nano Server 恢复控制台的网络设置中执行此操作。</span><span class="sxs-lookup"><span data-stu-id="277c3-123">You also need to check your new VM's IP address either my checking your DHCP server's fixed IP supplied while provisioning your VM or in Nano Server recovery console's networking settings.</span></span>

> [!NOTE]
> <span data-ttu-id="277c3-124">假设你的新 VM 使用本地 V4 IP 地址 192.168.1.10 运行。</span><span class="sxs-lookup"><span data-stu-id="277c3-124">Let's assume your new VM runs with the local V4 IP address 192.168.1.10.</span></span>

<span data-ttu-id="277c3-125">现在能够使用 PowerShell 远程处理来管理它，这是完全管理 Nano Server 的唯一方法。</span><span class="sxs-lookup"><span data-stu-id="277c3-125">Now you're able to manage it using PowerShell remoting, which is the only way to fully administer your Nano Server.</span></span>

## <a name="connecting-to-your-nano-server-instance-using-powershell-remoting"></a><span data-ttu-id="277c3-126">使用 PowerShell 远程处理连接到 Nano Server 实例</span><span class="sxs-lookup"><span data-stu-id="277c3-126">Connecting to your Nano Server instance using PowerShell Remoting</span></span>

<span data-ttu-id="277c3-127">打开提升的 PowerShell 窗口，向 `TrustedHosts` 列表添加你的远程 Nano Server 实例。</span><span class="sxs-lookup"><span data-stu-id="277c3-127">Open an elevated PowerShell window to add your remote Nano Server instance to your `TrustedHosts` list.</span></span>

```PowerShell
$nanoServerIpAddress = "192.168.1.10"
Set-Item WSMan:\localhost\Client\TrustedHosts "$nanoServerIpAddress" -Concatenate -Force
```

> [!NOTE]
> <span data-ttu-id="277c3-128">使用正确的 IP 地址替换变量 `$nanoServerIpAddress`。</span><span class="sxs-lookup"><span data-stu-id="277c3-128">Replace the variable `$nanoServerIpAddress` with the correct IP address.</span></span>

<span data-ttu-id="277c3-129">将 Nano Server 实例添加到 `TrustedHosts` 后，可以使用 PowerShell 远程处理连接到它。</span><span class="sxs-lookup"><span data-stu-id="277c3-129">Once you have added your Nano Server instance to your `TrustedHosts`, you can connect to it using PowerShell remoting.</span></span>

```PowerShell
$nanoServerSession = New-PSSession -ComputerName $nanoServerIpAddress -Credential ~\Administrator
Enter-PSSession $nanoServerSession
```

<span data-ttu-id="277c3-130">成功连接后，会出现一个提示，格式如下：`[192.168.1.10]: PS C:\Users\Administrator\Documents>`</span><span class="sxs-lookup"><span data-stu-id="277c3-130">A successful connection results in a prompt with a format looking like: `[192.168.1.10]: PS C:\Users\Administrator\Documents>`</span></span>

## <a name="creating-a-file-share"></a><span data-ttu-id="277c3-131">创建文件共享</span><span class="sxs-lookup"><span data-stu-id="277c3-131">Creating a file share</span></span>

<span data-ttu-id="277c3-132">在 Nano Server 上创建文件共享，以便可以将发布的应用程序复制到该文件。</span><span class="sxs-lookup"><span data-stu-id="277c3-132">Create a file share on the Nano server so that the published application can be copied to it.</span></span> <span data-ttu-id="277c3-133">在远程会话中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="277c3-133">Run the following commands in the remote session:</span></span>

```PowerShell
New-Item C:\PublishedApps\AspNetCoreSampleForNano -type directory
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=yes
net share AspNetCoreSampleForNano=c:\PublishedApps\AspNetCoreSampleForNano /GRANT:EVERYONE`,FULL
```

<span data-ttu-id="277c3-134">运行上述命令后，应能够通过访问主计算机的 Windows 资源管理器中的 `\\192.168.1.10\AspNetCoreSampleForNano` 来访问此共享。</span><span class="sxs-lookup"><span data-stu-id="277c3-134">After running the above commands, you should be able to access this share by visiting `\\192.168.1.10\AspNetCoreSampleForNano` in the host machine's Windows Explorer.</span></span>

## <a name="open-port-in-the-firewall"></a><span data-ttu-id="277c3-135">在防火墙中打开端口</span><span class="sxs-lookup"><span data-stu-id="277c3-135">Open port in the firewall</span></span>

<span data-ttu-id="277c3-136">在远程会话中运行以下命令，打开防火墙中的端口以允许 IIS 侦听端口 TCP/8000 上的 TCP 流量。</span><span class="sxs-lookup"><span data-stu-id="277c3-136">Run the following commands in the remote session to open up a port in the firewall to let IIS listen for TCP traffic on port TCP/8000.</span></span>

```PowerShell
New-NetFirewallRule -Name "AspNet5 IIS" -DisplayName "Allow HTTP on TCP/8000" -Protocol TCP -LocalPort 8000 -Action Allow -Enabled True
```

## <a name="installing-iis"></a><span data-ttu-id="277c3-137">安装 IIS</span><span class="sxs-lookup"><span data-stu-id="277c3-137">Installing IIS</span></span>

<span data-ttu-id="277c3-138">从 PowerShell 库添加 `NanoServerPackage` 提供程序。</span><span class="sxs-lookup"><span data-stu-id="277c3-138">Add the `NanoServerPackage` provider from the PowerShell Gallery.</span></span> <span data-ttu-id="277c3-139">安装和导入提供程序后，便可以安装 Windows 程序包。</span><span class="sxs-lookup"><span data-stu-id="277c3-139">Once the provider is installed and imported, you can install Windows packages.</span></span>

<span data-ttu-id="277c3-140">在先前创建的 PowerShell 会话中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="277c3-140">Run the following commands in the PowerShell session that was created earlier:</span></span>

```PowerShell
Install-PackageProvider NanoServerPackage
Import-PackageProvider NanoServerPackage
Install-NanoServerPackage -Name Microsoft-NanoServer-Storage-Package
Install-NanoServerPackage -Name Microsoft-NanoServer-IIS-Package
```

<span data-ttu-id="277c3-141">若要快速验证是否已正确设置 IIS，可以访问 URL `http://192.168.1.10/`，应该会看到欢迎页。</span><span class="sxs-lookup"><span data-stu-id="277c3-141">To quickly verify if IIS is setup correctly, you can visit the URL `http://192.168.1.10/` and should see a welcome page.</span></span> <span data-ttu-id="277c3-142">安装 IIS 时，会默认创建用于侦听端口 80 的名为 `Default Web Site` 的网站。</span><span class="sxs-lookup"><span data-stu-id="277c3-142">When IIS is installed, a website called `Default Web Site` listening on port 80 is created by default.</span></span>

## <a name="installing-the-aspnet-core-module-ancm"></a><span data-ttu-id="277c3-143">安装 ASP.NET Core 模块 (ANCM)</span><span class="sxs-lookup"><span data-stu-id="277c3-143">Installing the ASP.NET Core Module (ANCM)</span></span>

<span data-ttu-id="277c3-144">ASP.NET Core 模块是一个 IIS 7.5+ 模块，它负责 ASP.NET Core HTTP 侦听器的进程管理，并将请求代理到它所管理的进程。</span><span class="sxs-lookup"><span data-stu-id="277c3-144">The ASP.NET Core Module is an IIS 7.5+ module which is responsible for process management of ASP.NET Core HTTP listeners and to proxy requests to processes that it manages.</span></span> <span data-ttu-id="277c3-145">目前，为 IIS 安装 ASP.NET Core 模块的过程为手动操作。</span><span class="sxs-lookup"><span data-stu-id="277c3-145">At the moment, the process to install the ASP.NET Core Module for IIS is manual.</span></span> <span data-ttu-id="277c3-146">需要在常规（而不是 Nano）计算机上安装 [.NET Core Windows Server 托管捆绑包](https://download.microsoft.com/download/B/1/D/B1D7D5BF-3920-47AA-94BD-7A6E48822F18/DotNetCore.2.0.0-WindowsHosting.exe)。</span><span class="sxs-lookup"><span data-stu-id="277c3-146">You will need to install the [.NET Core Windows Server Hosting bundle](https://download.microsoft.com/download/B/1/D/B1D7D5BF-3920-47AA-94BD-7A6E48822F18/DotNetCore.2.0.0-WindowsHosting.exe) on a regular (not Nano) machine.</span></span> <span data-ttu-id="277c3-147">在常规计算机上安装捆绑包之后，需要将以下文件复制到之前创建的文件共享。</span><span class="sxs-lookup"><span data-stu-id="277c3-147">After installing the bundle on a regular machine, you will need to copy the following files to the file share that we created earlier.</span></span>

<span data-ttu-id="277c3-148">在使用 IIS 的常规（而不是 Nano）服务器上，运行以下复制命令：</span><span class="sxs-lookup"><span data-stu-id="277c3-148">On a regular (not Nano) server with IIS, run the following copy commands:</span></span>

```PowerShell
Copy-Item -Path  C:\windows\system32\inetsrv\aspnetcore.dll -Destination `\\<nanoserver-ip-address>\AspNetCoreSampleForNano`
Copy-Item -Path  C:\windows\system32\inetsrv\config\schema\aspnetcore_schema.xml -Destination `\\<nanoserver-ip-address>\AspNetCoreSampleForNano`
```

<span data-ttu-id="277c3-149">在 Windows 10 计算机上，将 `C:\windows\system32\inetsrv` 替换为 `C:\Program Files\IIS Express`。</span><span class="sxs-lookup"><span data-stu-id="277c3-149">Replace `C:\windows\system32\inetsrv` with `C:\Program Files\IIS Express` on a Windows 10 machine.</span></span>

<span data-ttu-id="277c3-150">在 Nano 端，需要将以下文件从我们先前创建的文件共享复制到有效位置。</span><span class="sxs-lookup"><span data-stu-id="277c3-150">On the Nano side, you will need to copy the following files from the file share that we created earlier to the valid locations.</span></span> <span data-ttu-id="277c3-151">因此，运行以下复制命令：</span><span class="sxs-lookup"><span data-stu-id="277c3-151">So, run the following copy commands:</span></span>

```PowerShell
Copy-Item -Path C:\PublishedApps\AspNetCoreSampleForNano\aspnetcore.dll -Destination C:\windows\system32\inetsrv\
Copy-Item -Path C:\PublishedApps\AspNetCoreSampleForNano\aspnetcore_schema.xml -Destination C:\windows\system32\inetsrv\config\schema\
```

<span data-ttu-id="277c3-152">在远程会话中运行以下脚本：</span><span class="sxs-lookup"><span data-stu-id="277c3-152">Run the following script in the remote session:</span></span>

```PowerShell
# Backup existing applicationHost.config
Copy-Item -Path C:\Windows\System32\inetsrv\config\applicationHost.config -Destination  C:\Windows\System32\inetsrv\config\applicationHost_BeforeInstallingANCM.config

Import-Module IISAdministration

# Initialize variables
$aspNetCoreHandlerFilePath="C:\windows\system32\inetsrv\aspnetcore.dll"
Reset-IISServerManager -confirm:$false
$sm = Get-IISServerManager

# Add AppSettings section 
$sm.GetApplicationHostConfiguration().RootSectionGroup.Sections.Add("appSettings")

# Set Allow for handlers section
$appHostconfig = $sm.GetApplicationHostConfiguration()
$section = $appHostconfig.GetSection("system.webServer/handlers")
$section.OverrideMode="Allow"

# Add aspNetCore section to system.webServer
$sectionaspNetCore = $appHostConfig.RootSectionGroup.SectionGroups["system.webServer"].Sections.Add("aspNetCore")
$sectionaspNetCore.OverrideModeDefault = "Allow"
$sm.CommitChanges()

# Configure globalModule
Reset-IISServerManager -confirm:$false
$globalModules = Get-IISConfigSection "system.webServer/globalModules" | Get-IISConfigCollection
New-IISConfigCollectionElement $globalModules -ConfigAttribute @{"name"="AspNetCoreModule";"image"=$aspNetCoreHandlerFilePath}

# Configure module
$modules = Get-IISConfigSection "system.webServer/modules" | Get-IISConfigCollection
New-IISConfigCollectionElement $modules -ConfigAttribute @{"name"="AspNetCoreModule"}
```

> [!NOTE]
> <span data-ttu-id="277c3-153">完成上述步骤后，从共享中删除文件 aspnetcore.dll 和 aspnetcore_schema.xml。</span><span class="sxs-lookup"><span data-stu-id="277c3-153">Delete the files *aspnetcore.dll* and *aspnetcore_schema.xml* from the share after the above step.</span></span>

## <a name="installing-net-core-framework"></a><span data-ttu-id="277c3-154">安装 .NET Core Framework</span><span class="sxs-lookup"><span data-stu-id="277c3-154">Installing .NET Core Framework</span></span>

<span data-ttu-id="277c3-155">如果应用作为[依赖框架的部署 (FDD)](/dotnet/core/deploying/#framework-dependent-deployments-fdd) 发布，则服务器上必须安装 .NET Core。</span><span class="sxs-lookup"><span data-stu-id="277c3-155">If your app is published as a [framework-dependent deployment (FDD)](/dotnet/core/deploying/#framework-dependent-deployments-fdd), .NET Core must be installed on the server.</span></span> <span data-ttu-id="277c3-156">若要在 Nano Server 上安装 .NET Core，请在远程 PowerShell 会话中使用 [dotnet-install.ps1 PowerShell 脚本](https://dot.net/v1/dotnet-install.ps1)。</span><span class="sxs-lookup"><span data-stu-id="277c3-156">Use the [dotnet-install.ps1 PowerShell script](https://dot.net/v1/dotnet-install.ps1) in a remote PowerShell session to install .NET Core on your Nano Server.</span></span> <span data-ttu-id="277c3-157">通过 `-Version` 开关传递 CLI 版本：</span><span class="sxs-lookup"><span data-stu-id="277c3-157">Pass the CLI version with the `-Version` switch:</span></span>

```console
dotnet-install.ps1 -Version 2.0.0
```

## <a name="publishing-the-application"></a><span data-ttu-id="277c3-158">发布应用程序</span><span class="sxs-lookup"><span data-stu-id="277c3-158">Publishing the application</span></span>

<span data-ttu-id="277c3-159">将现有应用程序的已发布输出复制到文件共享的根目录。</span><span class="sxs-lookup"><span data-stu-id="277c3-159">Copy the published output of your existing application to the file share's root.</span></span>

<span data-ttu-id="277c3-160">可能需要更改 web.config 才能指向在其中提取 dotnet.exe 的位置。</span><span class="sxs-lookup"><span data-stu-id="277c3-160">You may need to make changes to your *web.config* to point to where you extracted *dotnet.exe*.</span></span> <span data-ttu-id="277c3-161">或者，可以将 dotnet.exe 添加到你的路径。</span><span class="sxs-lookup"><span data-stu-id="277c3-161">Alternatively, you can add *dotnet.exe* to your PATH.</span></span>

<span data-ttu-id="277c3-162">以下示例演示 dotnet.exe 不在路径中的情况下，web.config 的可能显示方式：</span><span class="sxs-lookup"><span data-stu-id="277c3-162">Example of how a *web.config* might look if *dotnet.exe* is **not** on the PATH:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="C:\dotnet\dotnet.exe" arguments=".\AspNetCoreSampleForNano.dll" stdoutLogEnabled="false" stdoutLogFile=".\logs\aspnetcore-stdout" forwardWindowsAuthToken="true" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="277c3-163">在远程会话中运行以下命令，在不同的端口（而不是默认网站）上为已发布的应用创建一个新的站点。</span><span class="sxs-lookup"><span data-stu-id="277c3-163">Run the following commands in the remote session to create a new site in IIS for the published app on a different port than the default website.</span></span> <span data-ttu-id="277c3-164">此外，还需要打开该端口来访问 Web。</span><span class="sxs-lookup"><span data-stu-id="277c3-164">You also need to open that port to access the web.</span></span> <span data-ttu-id="277c3-165">为简单起见，此脚本使用 `DefaultAppPool`。</span><span class="sxs-lookup"><span data-stu-id="277c3-165">This script uses the `DefaultAppPool` for simplicity.</span></span> <span data-ttu-id="277c3-166">关于在应用程序池下运行的更多注意事项，请参阅[应用程序池](xref:publishing/iis#application-pools)。</span><span class="sxs-lookup"><span data-stu-id="277c3-166">For more considerations on running under an application pool, see [Application Pools](xref:publishing/iis#application-pools).</span></span>

```PowerShell
Import-module IISAdministration
New-IISSite -Name "AspNetCore" -PhysicalPath c:\PublishedApps\AspNetCoreSampleForNano -BindingInformation "*:8000:"
```

## <a name="known-issue-running-net-core-cli-on-nano-server-and-workaround"></a><span data-ttu-id="277c3-167">在 Nano Server 上运行 .NET Core CLI 的已知问题和解决方法</span><span class="sxs-lookup"><span data-stu-id="277c3-167">Known issue running .NET Core CLI on Nano Server and workaround</span></span>
```PowerShell
New-NetFirewallRule -Name "AspNetCore Port 81 IIS" -DisplayName "Allow HTTP on TCP/81" -Protocol TCP -LocalPort 81 -Action Allow -Enabled True
```

## <a name="running-the-application"></a><span data-ttu-id="277c3-168">运行应用程序</span><span class="sxs-lookup"><span data-stu-id="277c3-168">Running the application</span></span>

<span data-ttu-id="277c3-169">可以在浏览器中转到 `http://192.168.1.10:8000` 访问已发布的 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="277c3-169">The published web app is accessible in a browser at `http://192.168.1.10:8000`.</span></span> <span data-ttu-id="277c3-170">如果你已按照[日志创建和重定向](xref:hosting/aspnet-core-module#log-creation-and-redirection)中所述设置了日志记录，则可以在 C:\PublishedApps\AspNetCoreSampleForNano\logs 中查看日志。</span><span class="sxs-lookup"><span data-stu-id="277c3-170">If you've set up logging as described in [Log creation and redirection](xref:hosting/aspnet-core-module#log-creation-and-redirection), you can view your logs at *C:\PublishedApps\AspNetCoreSampleForNano\logs*.</span></span>
