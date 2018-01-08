---
title: "使用 Nginx 在 Linux 上托管 ASP.NET Core"
description: "介绍如何在 Ubuntu 16.04 上将 Nginx 设置为反向代理，从而将 HTTP 流量转发到在 Kestrel 上运行的 ASP.NET Core Web 应用程序。"
keywords: "ASP.NET Core, Linux, nginx, Ubuntu, 反向代理"
author: rick-anderson
ms.author: riande
manager: wpickett
ms.date: 08/21/2017
ms.topic: article
ms.assetid: 1c33e576-33de-481a-8ad3-896b94fde0e3
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/linuxproduction
ms.openlocfilehash: 7c7b949fc922c605aa4554c158200a4123c4eb1c
ms.sourcegitcommit: fc98e93464ccf37d9904e89a71cdddbd4bbdb86a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2018
---
# <a name="set-up-a-hosting-environment-for-aspnet-core-on-linux-with-nginx-and-deploy-to-it"></a>使用 Nginx 在 Linux 上为 ASP.NET Core 设置托管环境，并对其进行部署

作者：[Sourabh Shirhatti](https://twitter.com/sshirhatti)

本指南介绍如何在 Ubuntu 16.04 服务器上设置生产就绪 ASP.NET Core 环境。

注意：对于 Ubuntu 14.04，建议进行监控，以此作为监视 Kestrel 进程的解决方案。 在 Ubuntu 14.04 上不提供 systemd。 [请参阅本文档的早期版本](https://github.com/aspnet/Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md)

本指南：

* 将现有 ASP.NET Core 应用程序置于反向代理服务器后面
* 设置反向代理服务器，以便将请求转发到 Kestrel Web 服务器
* 确保 Web 应用程序在启动时作为守护程序运行
* 配置进程管理工具以帮助重新启动 Web 应用程序

## <a name="prerequisites"></a>系统必备

1. 使用具有 sudo 特权的标准用户帐户访问 Ubuntu 16.04 服务器
2. 现有 ASP.NET Core 应用程序

## <a name="copy-over-your-app"></a>复制应用

从开发环境中运行`dotnet publish` 以将应用打包到可在服务器上运行的独立目录。

使用集成到你的工作流的任何工具（SCP 和 FTP 等）将 ASP.NET Core 应用复制到服务器。 测试应用，例如：
 - 从命令行中，运行 `dotnet yourapp.dll`
 - 在浏览器中，导航到 `http://<serveraddress>:<port>` 以确认应用在 Linux 上正常运行。 
 
## <a name="configure-a-reverse-proxy-server"></a>配置反向代理服务器

反向代理是为动态 Web 应用程序提供服务的常见设置。 反向代理终止 HTTP 请求，并将其转发到 ASP.NET Core 应用程序。

### <a name="why-use-a-reverse-proxy-server"></a>为何使用反向代理服务器？

Kestrel 非常适合从 ASP.NET Core 提供动态内容，但是，Web 服务部件的功能不像 IIS、Apache 或 Nginx 等服务器那样强大。 反向代理服务器可以从 HTTP 服务器卸载服务静态内容、缓存请求、压缩请求和 SSL 终端等工作。 反向代理服务器可能驻留在专用计算机上，也可能与 HTTP 服务器一起部署。

鉴于此指南的目的，使用 Nginx 的单个实例。 它与 HTTP 服务器一起运行在同一服务器上。 根据你的要求，可以选择不同的设置。

由于请求通过反向代理转发，因此使用 `Microsoft.AspNetCore.HttpOverrides` 包中的 `ForwardedHeaders` 中间件。 此中间件使用 `X-Forwarded-Proto` 标头来更新 `Request.Scheme`，使重定向 URI 和其他安全策略能够正常工作。

设置反向代理服务器时，身份验证中间件需要首先运行 `UseForwardedHeaders`。 此顺序可确保身份验证中间件可以使用受影响的值，并生成正确的重定向 URI。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

在调用 `UseAuthentication` 或类似的身份验证方案中间件之前，调用 `UseForwardedHeaders` 方法（在 *Startup.cs* 的 `Configure` 方法中）：

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

在调用 `UseIdentity`、`UseFacebookAuthentication` 或类似的身份验证方案中间件之前，调用 `UseForwardedHeaders` 方法（在 *Startup.cs* 的 `Configure` 方法中）：

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseIdentity();
app.UseFacebookAuthentication(new FacebookOptions()
{
    AppId = Configuration["Authentication:Facebook:AppId"],
    AppSecret = Configuration["Authentication:Facebook:AppSecret"]
});
```

---

### <a name="install-nginx"></a>安装 Nginx

```bash
sudo apt-get install nginx
```

> [!NOTE]
> 如果计划安装可选 Nginx 模块，可能需要从源生成 Nginx。

使用 `apt-get` 安装 Nginx。 安装程序创建一个 System V init 脚本，该脚本运行 Nginx 作为系统启动时的守护程序。 因为是首次安装 Nginx，通过运行以下命令显式启动：

```bash
sudo service nginx start
```

确认浏览器显示 Nginx 的默认登陆页。

### <a name="configure-nginx"></a>配置 Nginx

若要将 Nginx 配置为反向代理以将请求转发到 ASP.NET Core 应用程序，请修改 `/etc/nginx/sites-available/default`。 在文本编辑器中打开它，并将内容替换为以下内容：

```nginx
server {
    listen 80;
    location / {
        proxy_pass http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection keep-alive;
        proxy_set_header Host $http_host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

此 Nginx 配置文件将传入的公共流量从端口 `80` 转发到端口 `5000`。

完成对 Nginx 配置的修改后，可运行 `sudo nginx -t` 以验证配置文件的语法。 如果配置文件测试成功，可以通过运行 `sudo nginx -s reload` 让 Nginx 选取更改。

## <a name="monitoring-our-application"></a>监视应用程序

Nginx 现在已设置为将对 `http://yourhost:80` 发起的请求转发到运行在 `http://127.0.0.1:5000` 中的 Kestrel 上的 ASP.NET Core 应用程序。 但是，未将 Nginx 设置为管理 Kestrel 进程。 可以使用 systemd 并创建服务文件以启动和监视基础 Web 应用。 systemd 是一个 init 系统，可以提供用于启动、停止和管理进程的许多强大的功能。 

### <a name="create-the-service-file"></a>创建服务文件

创建服务定义文件：

```bash
sudo nano /etc/systemd/system/kestrel-hellomvc.service
```

以下是我们的应用程序的一个示例服务文件：

```ini
[Unit]
Description=Example .NET Web API Application running on Ubuntu

[Service]
WorkingDirectory=/var/aspnetcore/hellomvc
ExecStart=/usr/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
Restart=always
RestartSec=10  # Restart service after 10 seconds if dotnet service crashes
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

请注意：如果配置未使用用户 www-data，则必须先创建此处定义的用户，并为该用户提供适当的文件所有权。

保存该文件，并启用该服务。

```bash
systemctl enable kestrel-hellomvc.service
```

启用该服务，并确认它正在运行。

```
systemctl start kestrel-hellomvc.service
systemctl status kestrel-hellomvc.service

● kestrel-hellomvc.service - Example .NET Web API Application running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-hellomvc.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-hellomvc.service
            └─9021 /usr/local/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
```

配置反向代理并通过 systemd 管理 Kestrel 后，Web 应用程序即已完全配置并能从 `http://localhost` 中的本地计算机上的浏览器访问。 也可以从远程计算机进行访问，同时限制可能进行阻止的任何防火墙。 检查响应标头，`Server` 标头显示由 Kestrel 所提供的 ASP.NET Core 应用程序。

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="viewing-logs"></a>查看日志

使用 Kestrel 的 Web 应用程序是通过 `systemd` 进行管理的，因此所有事件和进程都被记录到集中日志。 但是，此日志包含由 `systemd` 管理的所有服务和进程的全部条目。 若要查看特定于 `kestrel-hellomvc.service` 的项，请使用以下命令：

```bash
sudo journalctl -fu kestrel-hellomvc.service
```

有关进一步筛选，时间选项（如 `--since today`、`--until 1 hour ago` 或这些选项的组合）可以减少返回的条目数。

```bash
sudo journalctl -fu kestrel-hellomvc.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="securing-our-application"></a>保护应用程序

### <a name="enable-apparmor"></a>启用 AppArmor

Linux 安全模块 (LSM) 是一个框架，它是自 Linux 2.6 后的 Linux kernel 的一部分。 LSM 支持安全模块的不同实现。 [AppArmor](https://wiki.ubuntu.com/AppArmor) 是实现强制访问控制系统的 LSM，它允许将程序限制在一组有限的资源内。 确保已启用并成功配置 AppArmor。

### <a name="configuring-our-firewall"></a>配置防火墙

关闭所有未使用的外部端口。 通过为配置防火墙提供命令行接口，不复杂的防火墙 (ufw) 为 `iptables` 提供了前端。 确认已配置 `ufw` 以允许所需的任何端口上的流量。

```bash
sudo apt-get install ufw
sudo ufw enable

sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
```

### <a name="securing-nginx"></a>保护 Nginx

Nginx 的默认分配不启用 SSL。 若要启用其他安全功能，请从源生成。

#### <a name="download-the-source-and-install-the-build-dependencies"></a>下载源并安装生成依赖项

```bash
# Install the build dependencies
sudo apt-get update
sudo apt-get install build-essential zlib1g-dev libpcre3-dev libssl-dev libxslt1-dev libxml2-dev libgd2-xpm-dev libgeoip-dev libgoogle-perftools-dev libperl-dev

# Download nginx 1.10.0 or latest
wget http://www.nginx.org/download/nginx-1.10.0.tar.gz
tar zxf nginx-1.10.0.tar.gz
```

#### <a name="change-the-nginx-response-name"></a>更改 Nginx 响应名称

编辑 src/http/ngx_http_header_filter_module.c：

```c
static char ngx_http_server_string[] = "Server: Your Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Your Web Server" CRLF;
```

#### <a name="configure-the-options-and-build"></a>配置选项和生成

正则表达式需要 PCRE 库。 正则表达式用于 ngx_http_rewrite_module 的位置指令。 http_ssl_module adds HTTPS 协议支持。

请考虑使用诸如“ModSecurity”的 Web 应用程序防火墙来加强对应用程序的保护。

```bash
./configure
--with-pcre=../pcre-8.38
--with-zlib=../zlib-1.2.8
--with-http_ssl_module
--with-stream
--with-mail=dynamic
```

#### <a name="configure-ssl"></a>配置 SSL

* 通过指定由受信任的证书颁发机构 (CA) 颁发的有效证书来配置服务器，以侦听端口 `443` 上的 HTTPS 流量。

* 通过采用以下“/etc/nginx/nginx.conf”文件中所示的某些做法来增强安全保护。 示例包括选择更强的密码并将通过 HTTP 的所有流量重定向到 HTTPS。

* 添加 `HTTP Strict-Transport-Security` (HSTS) 标头可确保由客户端发起的所有后续请求都仅通过 HTTPS。

* 如果计划以后禁用 SSL，则不要添加 Strict-Transport-Security 标头或选择相应的 `max-age`。

添加 /etc/nginx/proxy.conf 配置文件：

[!code-nginx[Main](linuxproduction/proxy.conf)]

编辑 /etc/nginx/nginx.conf 配置文件。 示例包含一个配置文件中的 `http` 和 `server` 部分。

[!code-nginx[Main](../publishing/linuxproduction/nginx.conf?highlight=2)]

#### <a name="secure-nginx-from-clickjacking"></a>保护 Nginx 免受点击劫持的侵害
点击劫持是收集受感染用户的点击数的恶意技术。 点击劫持诱使受害者（访问者）点击受感染的网站。 使用 X-FRAME-OPTIONS 保护你的网站。

编辑 nginx.conf 文件：

```bash
sudo nano /etc/nginx/nginx.conf
```

添加行 `add_header X-Frame-Options "SAMEORIGIN";` 并保存文件，然后重新启动 Nginx。

#### <a name="mime-type-sniffing"></a>MIME 类型探查

此标头可阻止大部分浏览器通过 MIME 方式探查来自已声明内容类型的响应，因为标头会指示浏览器不要替代响应内容类型。 使用 `nosniff` 选项后，如果服务器认为内容是“文本/html”，则浏览器将其显示为“文本/html”。

编辑 nginx.conf 文件：

```bash
sudo nano /etc/nginx/nginx.conf
```

添加行 `add_header X-Content-Type-Options "nosniff";` 并保存文件，然后重新启动 Nginx。
