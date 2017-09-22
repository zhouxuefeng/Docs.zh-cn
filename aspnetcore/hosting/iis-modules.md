---
title: "使用 ASP.NET Core 的 IIS 模块"
author: guardrex
description: "参考文档，其中描述 ASP.NET Core 应用程序的活动和非活动 IIS 模块。"
keywords: "ASP.NET 核心，iis、 模块、 反向代理"
ms.author: riande
manager: wpickett
ms.date: 03/08/2017
ms.topic: article
ms.assetid: 492b3a7e-04c5-461b-b96a-38ecee5c64bc
ms.technology: aspnet
ms.prod: aspnet-core
uid: hosting/iis-modules
ms.openlocfilehash: 97c5fb6db6fe2a1dbae5529c11479413fd4814fb
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2017
---
# <a name="using-iis-modules-with-aspnet-core"></a>使用 ASP.NET Core 的 IIS 模块

通过[Luke Latham](https://github.com/GuardRex)

反向代理配置中，由 IIS 承载 ASP.NET Core 应用程序。 一些本机 IIS 模块和所有托管的 IIS 模块不可用来处理 ASP.NET Core 应用的请求。 在许多情况下，ASP.NET Core 提供 IIS 本机和托管模块的功能的替代方法。

## <a name="native-modules"></a>本机模块
模块 | .NET 核心活动 | ASP.NET 核心选项
--- | :---: | ---
**匿名身份验证**<br>`AnonymousAuthenticationModule` | 是 | 
**基本身份验证**<br>`BasicAuthenticationModule` | 是 | 
**客户端证书映射身份验证**<br>`CertificateMappingAuthenticationModule` | 是 | 
**CGI**<br>`CgiModule` | No | 
**配置验证**<br>`ConfigurationValidationModule` | 是 | 
**HTTP 错误**<br>`CustomErrorModule` | No | [状态代码页中间件](xref:fundamentals/error-handling#configuring-status-code-pages)
**自定义日志记录**<br>`CustomLoggingModule` | 是 | 
**默认文档**<br>`DefaultDocumentModule` | No | [默认文件中间件](xref:fundamentals/static-files#serving-a-default-document)
**摘要式身份验证**<br>`DigestAuthenticationModule` | 是 | 
**目录浏览**<br>`DirectoryListingModule` | No | [目录浏览中间件](xref:fundamentals/static-files#enabling-directory-browsing)
**动态压缩**<br>`DynamicCompressionModule` | 是 | [响应压缩中间件](xref:performance/response-compression)
**跟踪**<br>`FailedRequestsTracingModule` | 是 | [ASP.NET 核心日志记录](xref:fundamentals/logging#the-tracesource-provider)
**文件缓存**<br>`FileCacheModule` | No | [响应缓存中间件](xref:performance/caching/middleware)
**HTTP 缓存功能**<br>`HttpCacheModule` | No | [响应缓存中间件](xref:performance/caching/middleware)
**HTTP 日志记录**<br>`HttpLoggingModule` | 是 | [ASP.NET 核心日志记录](xref:fundamentals/logging)<br>实现： [elmah.io](https://github.com/elmahio/Elmah.Io.Extensions.Logging)， [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging)， [NLog](https://github.com/NLog/NLog.Extensions.Logging)， [Serilog](https://github.com/serilog/serilog-extensions-logging)
**HTTP 重定向**<br>`HttpRedirectionModule` | 是 | [URL 重写中间件](xref:fundamentals/url-rewriting)
**IIS 客户端证书映射身份验证**<br>`IISCertificateMappingAuthenticationModule` | 是 | 
**IP 和域限制**<br>`IpRestrictionModule` | 是 | 
**ISAPI 筛选器**<br>`IsapiFilterModule` | 是 | [中间件](xref:fundamentals/middleware)
**ISAPI**<br>`IsapiModule` | 是 | [中间件](xref:fundamentals/middleware)
**协议支持**<br>`ProtocolSupportModule` | 是 | 
**请求筛选**<br>`RequestFilteringModule` | 是 | [URL 重写中间件`IRule`](xref:fundamentals/url-rewriting#irule-based-rule)
**请求监视器**<br>`RequestMonitorModule` | 是 | 
**URL 重写**<br>`RewriteModule` | Yes† | [URL 重写中间件](xref:fundamentals/url-rewriting)
**服务器端包括**<br>`ServerSideIncludeModule` | No | 
**静态压缩**<br>`StaticCompressionModule` | No | [响应压缩中间件](xref:performance/response-compression)
**静态内容**<br>`StaticFileModule` | No | [静态文件中间件](xref:fundamentals/static-files)
**令牌缓存**<br>`TokenCacheModule` | 是 | 
**URI 缓存**<br>`UriCacheModule` | 是 | 
**URL 授权**<br>`UrlAuthorizationModule` | 是 | [ASP.NET 核心标识](xref:security/authentication/identity)
**Windows 身份验证**<br>`WindowsAuthenticationModule` | 是 | 

†The URL 重写模块`isFile`和`isDirectory`不能使用 ASP.NET Core 应用程序中的更改由于[目录结构](xref:hosting/directory-structure)。

## <a name="managed-modules"></a>托管的模块
模块 | .NET 核心活动 | ASP.NET 核心选项
--- | :---: | ---
AnonymousIdentification | No | 
DefaultAuthentication | No | 
FileAuthorization | No | 
FormsAuthentication | No | [Cookie 身份验证中间件](xref:security/authentication/cookie)
OutputCache | No | [响应缓存中间件](xref:performance/caching/middleware)
配置文件 | No | 
RoleManager | No | 
ScriptModule 4.0 | No | 
会话 | No | [会话中间件](xref:fundamentals/app-state)
Url 授权 | No | 
UrlMappingsModule | No | [URL 重写中间件](xref:fundamentals/url-rewriting)
UrlRoutingModule 4.0 | No | [ASP.NET 核心标识](xref:security/authentication/identity)
WindowsAuthentication | No | 

## <a name="iis-manager-application-changes"></a>IIS 管理器应用程序更改
当你使用 IIS 管理器配置设置时，将直接更改*web.config*应用程序文件。 如果你部署您的应用程序，包括*web.config*，使用 IIS 管理器进行的任何更改将被覆盖的部署*web.config 文件*。 因此如果你更改了服务器的*web.config*文件中，复制已更新*web.config*立即将本地项目的文件。

## <a name="disabling-iis-modules"></a>禁用 IIS 模块
如果你有一个你想要禁用为应用程序的服务器级别配置的 IIS 模块，可以进行此操作而添加到你*web.config*文件。 将模块保留原位和停用它 （如果可用） 使用配置设置，或者从应用程序中删除模块。

### <a name="module-deactivation"></a>模块停用
多个模块提供配置设置，您可以禁用它们，而不从应用程序中删除它们。 这是最简单且最快速方式停用模块。 例如，如果你想要禁用 IIS URL 重写模块，请使用`<httpRedirect>`元素如下所示。 禁用使用配置设置的模块的详细信息，请按照中的链接*子元素*部分[IIS `<system.webServer>` ](https://docs.microsoft.com/iis/configuration/system.webServer/)。

```xml
<configuration>
  <system.webServer>
     <httpRedirect enabled="false" />
  </system.webServer>
</configuration>
```

### <a name="module-removal"></a>模块删除
如果你选择删除模块中的设置与*web.config*，你必须解锁模块和解锁`<modules>`部分*web.config*第一个。 步骤如下所述：

1. 解锁的服务器级别的模块。 在 IIS 管理器在 IIS 服务器上单击**连接**侧栏。 打开**模块**中**IIS**区域。 单击列表中的模块。 在**操作**侧栏右侧，单击**解锁**。 解锁任意多个模块，因为你打算删除，但*web.config*更高版本。

2. 部署应用程序，而`<modules>`主题中*web.config*。如果你部署应用时采用*web.config*包含`<modules>`时你尝试解锁部分，而无需具有解锁部分首先在 IIS 管理器，Configuration Manager 的部分将引发异常。 因此，部署应用程序，而`<modules>`部分。

3. 解锁`<modules>`部分*web.config*。在**连接**栏中，单击在网站**站点**。 在**管理**区域中，打开**配置编辑器**。 使用导航控件选择`system.webServer/modules`部分。 在**操作**侧栏右侧，单击到**解锁**部分。

4. 此时，你将能够添加`<modules>`部分为你*web.config*文件`<remove>`元素从应用程序中删除该模块。 你可以添加多个`<remove>`要移除多个模块元素。 不要忘记，如果你进行*web.config*服务器以便它们可以立即在本地项目上的更改。 删除这种方式的模块不会影响您的服务器上的其他应用的模块使用。

  ```xml
  <configuration> 
    <system.webServer> 
      <modules> 
        <remove name="MODULE_NAME" /> 
      </modules> 
    </system.webServer> 
  </configuration>
  ```

对于与默认模块安装 IIS 安装，你可以使用以下`<module>`部分，以移除默认模块。

```xml
<modules>
  <remove name="CustomErrorModule" />
  <remove name="DefaultDocumentModule" />
  <remove name="DirectoryListingModule" />
  <remove name="HttpCacheModule" />
  <remove name="HttpLoggingModule" />
  <remove name="ProtocolSupportModule" />
  <remove name="RequestFilteringModule" />
  <remove name="StaticCompressionModule" /> 
  <remove name="StaticFileModule" /> 
</modules>
```

您还可以删除某个 IIS 模块具有*Appcmd.exe*。 提供`MODULE_NAME`和`APPLICATION_NAME`中下面显示的命令：

```console
Appcmd.exe delete module MODULE_NAME /app.name:APPLICATION_NAME
```

下面介绍了如何删除`DynamicCompressionModule`从默认网站：

```console
%windir%\system32\inetsrv\appcmd.exe delete module DynamicCompressionModule /app.name:"Default Web Site"
```

## <a name="minimal-module-configuration"></a>最少模块配置
若要运行 ASP.NET Core 应用程序所需的唯一模块是匿名身份验证模块和 ASP.NET 核心模块。

![显示最少模块配置到模块打开 IIS 管理器](iis-modules/_static/modules.png)

## <a name="resources"></a>资源
* [发布到 IIS](xref:publishing/iis)
* [IIS 模块概述](https://docs.microsoft.com/iis/get-started/introduction-to-iis/iis-modules-overview)
* [自定义 IIS 7.0 角色和模块](https://technet.microsoft.com/library/cc627313.aspx)
* [IIS`<system.webServer>`](https://docs.microsoft.com/iis/configuration/system.webServer/)
