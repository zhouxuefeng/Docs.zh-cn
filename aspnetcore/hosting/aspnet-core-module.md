---
title: "ASP.NET 核心模块配置参考"
author: guardrex
description: "如何配置适用于托管 ASP.NET Core 应用程序的 ASP.NET 核心模块。"
keywords: "ASP.NET 核心，ancm、 核心模块、 iis、 stdout 日志记录、 环境变量、 env var、 subapplication、 subapp、 appoffline、 app_offline，502，架构"
ms.author: riande
manager: wpickett
ms.date: 03/07/2017
ms.topic: article
ms.assetid: 5de0c8f7-50ce-4e2c-b3d4-a1bd9fdfcff5
ms.technology: aspnet
ms.prod: asp.net-core
uid: hosting/aspnet-core-module
ms.openlocfilehash: 277e63a5663aca622e8252d6c6be1671e57cbf68
ms.sourcegitcommit: 44a62f59d4db39d685c4487a0345a486be18d7c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="aspnet-core-module-configuration-reference"></a>ASP.NET 核心模块配置参考

通过[Luke Latham](https://github.com/guardrex)， [Rick Anderson](https://twitter.com/RickAndMSFT)，和[Sourabh Shirhatti](https://twitter.com/sshirhatti)

本文档提供有关如何配置 ASP.NET 核心模块用于托管 ASP.NET Core 应用程序的详细信息。 有关 ASP.NET 核心模块和安装说明简介，请参阅[ASP.NET 核心模块概述](xref:fundamentals/servers/aspnet-core-module)。

## <a name="configuration-via-webconfig"></a>通过 web.config 配置

通过站点或应用程序配置 ASP.NET 核心模块*web.config*文件，并都具有其自己`aspNetCore`内的配置部分`system.webServer`。 下面是一个示例*web.config*文件`Microsoft.NET.Sdk.Web`SDK 将提供的发布项目时[framework 相关部署](https://docs.microsoft.com/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)带有占位符`processPath`和`arguments`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="%LAUNCHER_PATH%" 
        arguments="%LAUNCHER_ARGS%" 
        stdoutLogEnabled="false" 
        stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

*Web.config*下面的示例适用于[独立的部署](https://docs.microsoft.com/dotnet/articles/core/deploying/#self-contained-deployments-scd)到[Azure App Service](https://azure.microsoft.com/services/app-service/)。 有关详细信息，请参阅[发布到 IIS](xref:publishing/iis)。 请参阅[的子应用程序配置](xref:publishing/iis#configuration-of-sub-applications)的与配置相关的重要说明*web.config*子应用程序中的文件。

```xml
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe" 
        stdoutLogEnabled="false" 
        stdoutLogFile="\\?\%home%\LogFiles\stdout" />
  </system.webServer>
</configuration>
```

### <a name="attributes-of-the-aspnetcore-element"></a>AspNetCore 元素的特性

| 特性 | 描述 |
| --- | --- |
| processPath | <p>必需的字符串属性。</p><p>将启动侦听 HTTP 请求的进程的可执行文件的路径。 支持相对路径。 如果路径以。，考虑使用该路径是相对站点根。</p><p>没有默认值。</p> |
| 自变量 | <p>可选的字符串属性。</p><p>可执行文件中指定的自变量**processPath**。</p><p>默认值为一个空字符串。</p> |
| startupTimeLimit | <p>可选的整数属性。</p><p>以秒为单位，模块将等待启动侦听端口的进程的可执行文件的持续时间。 如果超过此时间限制，该模块将终止的进程。 该模块将尝试重新启动该过程，在它接收新请求，并将继续尝试重新启动在后续的传入请求的过程，除非应用程序启动失败时**rapidFailsPerMinute**数最后一个滚动分钟内的时间。</p><p>默认值为 120。</p> |
| shutdownTimeLimit | <p>可选的整数属性。</p><p>以秒为单位为其模块将等待正常关闭的可执行文件的持续时间时*app_offline.htm*检测到文件。</p><p>默认值为 10。</p> |
| rapidFailsPerMinute | <p>可选的整数属性。</p><p>指定在指定的进程的次数**processPath**允许每分钟崩溃。 如果超出此限制，该模块将停止启动剩余秒数的进程。</p><p>默认值为 10。</p> |
| requestTimeout | <p>可选的 timespan 属性。</p><p>指定 ASP.NET 核心模块将等待侦听 %aspnetcore_port%的进程的响应的持续时间。</p><p>默认值为“00:02:00”。</p><p>`requestTimeout`必须指定整分钟数，否则它将默认为 2 分钟。</p> |
| stdoutLogEnabled | <p>可选布尔属性。</p><p>如果为 true， **stdout**和**stderr**中指定的进程的**processPath**将重定向到中指定的文件**stdoutLogFile**.</p><p>默认值为 False。</p> |
| stdoutLogFile | <p>可选的字符串属性。</p><p>为其指定的相对或绝对文件路径**stdout**和**stderr**中指定的进程从**processPath**将记录。 相对路径是相对于站点的根目录。 从任何路径。 将相对站点根和所有其他路径将被视为绝对路径。 路径中提供的任何文件夹必须存在于要创建的日志文件的模块的顺序。 进程 ID，时间戳 (*yyyyMdhms*)，和文件扩展名 (*.log*) 以下划线分隔符将添加到最后一个段**stdoutLogFile**提供。</p><p>默认值为 `aspnetcore-stdout`。</p> |
| forwardWindowsAuthToken | “真”或“假”。</p><p>如果为 true，则令牌将转发到侦听作为每个请求的标头 MS ASPNETCORE WINAUTHTOKEN 的 %aspnetcore_port%的子进程。 它是该进程可以在每个请求此令牌上调用 CloseHandle 责任。</p><p>默认值为 true。</p> |
| disableStartUpErrorPage | “真”或“假”。</p><p>如果为 true， **502.5-进程失败**页将禁止显示，并且 502 状态代码页中配置你*web.config*将优先。</p><p>默认值为 False。</p> |

### <a name="setting-environment-variables"></a>设置环境变量

ASP.NET 核心模块允许你指定中指定的进程的环境变量`processPath`通过一个或多中指定它们的属性`environmentVariable`的子元素`environmentVariables`集合元素下的`aspNetCore`元素。 在本部分中设置的环境变量优先于系统进程的环境变量。

下面的示例设置两个环境变量。 `ASPNETCORE_ENVIRONMENT`将配置应用程序的环境`Development`。 开发人员可能将此值暂时设置*web.config*文件以便强制[开发人员异常页](xref:fundamentals/error-handling)以便在调试应用程序异常时加载。 `CONFIG_DIR`是一种用户定义的环境变量中，开发人员曾将读取在启动时，才能加载应用程序的配置文件组合成路径的值的代码。

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

## <a name="appofflinehtm"></a>app_offline.htm

如果名称的文件放*app_offline.htm* ASP.NET 核心模块将在 web 应用程序目录的根目录，尝试正常关闭应用程序并停止处理传入请求。 如果应用程序仍在运行`shutdownTimeLimit`数 ASP.NET 核心模块将为秒，终止正在运行的进程。

虽然*app_offline.htm*文件不存在，如果通过发回的内容，ASP.NET 核心模块将将响应的请求*app_offline.htm*文件。 一次*app_offline.htm*删除文件下, 一个请求加载应用程序，然后将响应请求。

## <a name="start-up-error-page"></a>启动错误页

如果 ASP.NET 核心模块无法启动后端进程或后端进程启动但不能在配置的端口上侦听时，你将看到了 HTTP 502.5 状态的代码页。 若要禁止显示此页并还原为默认 IIS 502 状态代码页，请使用`disableStartUpErrorPage`属性。 有关配置自定义错误消息的详细信息，请参阅[HTTP 错误`<httpErrors>` ](https://docs.microsoft.com/iis/configuration/system.webServer/httpErrors/)。

![502 状态页](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a>日志创建和重定向

ASP.NET 核心模块将重定向`stdout`和`stderr`日志磁盘如果你设置`stdoutLogEnabled`和`stdoutLogFile`属性`aspNetCore`元素。 中的任何文件夹`stdoutLogFile`路径必须存在于要创建的日志文件的模块的顺序。 创建日志文件时，将自动添加时间戳和文件扩展名。 不旋转日志，除非进程回收/重新启动时发生。 它负责的托管商来限制日志使用的磁盘空间。 使用`stdout`日志仅建议用于故障排除应用程序启动问题并不用于常规应用程序日志记录。

日志文件名称由追加的进程 ID (PID)，时间戳 (*yyyyMdhms*)，和文件扩展名 (*.log*) 到的最后一段`stdoutLogFile`路径 (通常*stdout*) 由下划线分隔。 例如如果`stdoutLogFile`路径以结束*stdout*，pid 为 10652 在 12:05:02 8/10/2017年上创建的应用程序日志中的文件名称*stdout_10652_20178101252.log*。

下面是一个示例`aspNetCore`配置元素`stdout`日志记录。 `stdoutLogFile`示例所示的路径是否适用于 Azure App Service。 本地路径或网络共享路径是可接受的本地日志记录。 确认应用程序池用户标识有权写入提供的路径。

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout">
</aspNetCore>
```
请参阅[通过 web.config 配置](#configuration-via-webconfig)有关的示例`aspNetCore`中的元素*web.config*文件。

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>ASP.NET 核心模块与 IIS 共享配置

ASP.NET 核心模块安装程序使用的特权运行**系统**帐户。 本地系统帐户不具有修改权限由 IIS 共享配置的共享路径，因为安装程序将命中拒绝访问错误时尝试进行配置中的模块设置*applicationHost.config*共享上。

不受支持的解决方法是禁用 IIS 共享配置、 运行安装程序、 导出更新*applicationHost.config*文件到共享，并重新启用 IIS 共享配置。

## <a name="module-schema-and-configuration-file-locations"></a>模块、 架构和配置文件位置

### <a name="module"></a>模块

**IIS (x86/amd64):**

   * %windir%\System32\inetsrv\aspnetcore.dll

   * %windir%\SysWOW64\inetsrv\aspnetcore.dll

**IIS Express (x86/amd64):**

   * %ProgramFiles%\IIS Express\aspnetcore.dll

   * %Programfiles (x86) %\IIS Express\aspnetcore.dll

### <a name="schema"></a>架构

**IIS**

   * %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

**IIS Express**

   * %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

### <a name="configuration"></a>配置

**IIS**

   * %windir%\System32\inetsrv\config\applicationHost.config

**IIS Express**

   * .vs\config\applicationHost.config

你可以搜索*aspnetcore.dll*中*applicationHost.config*文件。 IIS express， *applicationHost.config*文件不存在默认情况下。 在创建文件*{应用程序根目录}\.vs\config*时启动 Visual Studio 解决方案中的任何 web 应用程序项目。
