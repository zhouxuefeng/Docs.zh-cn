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
# <a name="set-up-a-hosting-environment-for-aspnet-core-on-linux-with-apache-and-deploy-to-it"></a>使用 Apache 在 Linux 上为 ASP.NET Core 设置托管环境，并对其进行部署

作者：[Shayne Boyer](https://github.com/spboyer)

Apache 是非常热门的 HTTP 服务器，并且与 nginx 类似，可以将它配置为代理以重定向 HTTP 流量。 在本指南中，我们将了解如何在 CentOS 7 上设置 Apache 并使用它作为反向代理来接收传入连接，并将这些连接重定向到 Kestrel 上运行的 ASP.NET Core 应用程序。 出于此目的，我们将使用“mod_proxy”扩展和其他相关的 Apache 模块。

## <a name="prerequisites"></a>先决条件

1. 运行 CentOS 7 的服务器，使用具有 sudo 特权的标准用户帐户。
2. 现有 ASP.NET Core 应用程序。 

## <a name="publish-your-application"></a>发布应用程序

从开发环境运行 `dotnet publish -c Release` 将应用程序打包到可在服务器上运行的自包含目录中。 然后，必须使用 SCP、FTP 或其他文件传输方法将发布的应用程序复制到服务器。 

> [!NOTE]
> 在生产部署方案中，持续集成工作流会执行发布应用程序并将资产复制到服务器的工作。 

## <a name="configure-a-proxy-server"></a>配置代理服务器

反向代理是为动态 Web 应用程序提供服务的常见设置。 反向代理终止 HTTP 请求，并将其转发到 ASP.NET 应用程序。

代理服务器将客户端请求转发到另一个服务器，而不是自身实现这些请求。 反向代理转发到固定的目标，通常代表任意客户端。 在本指南中，Apache 被配置为反向代理，在 Kestrel 为 ASP.NET Core 应用程序提供服务的同一服务器上运行。 

应用程序的每个部分都可位于单独的物理计算机、Docker 容器或配置组合中，具体取决于体系结构需求或限制。

### <a name="install-apache"></a>安装 Apache

在 CentOS 上安装 Apache Web 服务器是单个命令，但我们先来更新包。

```bash
    sudo yum update -y
```

这可确保所有安装的包已更新到最新版本。 使用 `yum` 安装 Apache

```bash
    sudo yum -y install httpd mod_ssl
```

输出应反映与以下类似的内容。

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
> 在此示例中，输出反映了 httpd.86_64，因为 CentOS 7 版本是 64 位的。 输出可能因具体的服务器而有所不同。 若要验证 Apache 的安装位置，请从命令提示符运行 `whereis httpd`。 

### <a name="configure-apache-for-reverse-proxy"></a>配置 Apache 用于反向代理

Apache 的配置文件位于 `/etc/httpd/conf.d/` 目录内。 除了 `/etc/httpd/conf.modules.d/` 中的模块配置文件外（其中包含加载模块所需的任何配置文件），将对任何带 .conf 扩展名的文件按字母顺序进行处理。

创建应用的配置文件，在此示例中我们称之为 `hellomvc.conf`

```text
    <VirtualHost *:80>
        ProxyPreserveHost On
        ProxyPass / http://127.0.0.1:5000/
        ProxyPassReverse / http://127.0.0.1:5000/
        ErrorLog /var/log/httpd/hellomvc-error.log
        CustomLog /var/log/httpd/hellomvc-access.log common
    </VirtualHost>
```

VirtualHost 节点（一个文件中可能有多个或在多个文件中的一个服务器上可能有多个）已设置为侦听使用端口 80 的任何 IP 地址。 接下来的两行都设置为将在根处接收的所有请求传递到计算机 127.0.0.1 端口 5000，并按相反的顺序传递。 如果需要双向通信，则同时需要“ProxyPass”和“ProxyPassReverse”这两个设置。

可使用“ErrorLog”和“CustomLog”指令配置每个 VirtualHost 的日志记录。 “ErrorLog”是服务器记录错误的位置，“CustomLog”则设置文件名和日志文件的格式。 在我们的示例中，这是记录请求信息的位置。 每个请求将各占一行。

保存文件，并测试配置。 如果一切正常，响应应为 `Syntax [OK]`。

```bash
    sudo service httpd configtest
```

重启 Apache。

```text
    sudo systemctl restart httpd
    sudo systemctl enable httpd
```

## <a name="monitoring-our-application"></a>监视应用程序

Apache 现在已设置为将对 `http://localhost:80` 发起的请求转发到运行在 `http://127.0.0.1:5000` 处的 Kestrel 上的 ASP.NET Core 应用程序。  但是，未将 Apache 设置为管理 Kestrel 进程。 我们将使用 systemd，并创建服务文件以启动和监视基础 Web 应用。 systemd 是一个初始系统，可以提供启动、停止和管理进程的许多强大的功能。 


### <a name="create-the-service-file"></a>创建服务文件

创建服务定义文件 

```bash
    sudo nano /etc/systemd/system/kestrel-hellomvc.service
```

应用程序的一个示例服务文件：

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
> **用户** - 如果用户 apache 未被你的配置所使用，则必须先在此处创建定义的用户，并为该用户提供适当的文件所有权

保存该文件并启用该服务。

```bash
    systemctl enable kestrel-hellomvc.service
```

启用该服务，并确认它正在运行。

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

在配置了反向代理并通过 systemd 管理 Kestrel 后，Web 应用程序现已完全配置，并能在本地计算机上的浏览器中从 `http://localhost` 进行访问。 检查响应标头，服务器仍会显示由 Kestrel 所提供服务的 ASP.NET Core 应用程序。

```text
    HTTP/1.1 200 OK
    Date: Tue, 11 Oct 2016 16:22:23 GMT
    Server: Kestrel
    Keep-Alive: timeout=5, max=98
    Connection: Keep-Alive
    Transfer-Encoding: chunked
```

### <a name="viewing-logs"></a>查看日志

由于使用 Kestrel 的 Web 应用程序是使用 systemd 进行管理的，因此所有事件和进程都被记录到集中日志。 但是，此日志包含由 systemd 管理的所有服务和进程的全部条目。 若要查看特定于 `kestrel-hellomvc.service` 的项，请使用以下命令。

```bash
    sudo journalctl -fu kestrel-hellomvc.service
```

有关进一步筛选，使用时间选项（如 `--since today`、`--until 1 hour ago`）或这些选项的组合可以减少返回的条目数。

```bash
    sudo journalctl -fu kestrel-hellomvc.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="securing-our-application"></a>保护应用程序

### <a name="configure-firewall"></a>配置防火墙

Firewalld 是管理防火墙的动态守护程序，支持网络区域，但是你仍可以使用 iptable 管理端口和数据包筛选。 默认情况下应安装 Firewalld，可以使用 `yum` 安装包或进行验证。

```bash
    sudo yum install firewalld -y
```

通过使用 `firewalld`，可以仅打开应用程序所需的端口。 在此示例中，使用的是端口 80 和 443。 以下命令将这些端口永久设置为打开。

```bash
    sudo firewall-cmd --add-port=80/tcp --permanent
    sudo firewall-cmd --add-port=443/tcp --permanent
```

重新加载防火墙设置，并检查默认区域中可用的服务和端口。 通过检查 `firewall-cmd -h` 获取可用选项

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

### <a name="ssl-configuration"></a>SSL 配置

若要配置 Apache 用于 SSL，需使用 mod_ssl 模块。  这在最初安装 `httpd` 模块时已经安装。 如果它已丢失或未安装，请使用 yum 将其添加到配置中。

```bash
    sudo yum install mod_ssl
```
若要强制实施 SSL，请安装 `mod_rewrite`

```bash
    sudo yum install mod_rewrite
```

需要对为此示例创建的 `hellomvc.conf` 文件进行修改才能启用重写，以及为 HTTPS 添加新的“VirtualHost”部分。

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
> 此示例中使用了本地生成的证书。 SSLCertificateFile 应为域名的主证书文件。 SSLCertificateKeyFile 应为创建 CSR 时生成的密钥文件。 SSLCertificateChainFile 应为证书颁发机构提供的中间证书文件（如有）

保存文件，并测试配置。

```bash
    sudo service httpd configtest
```

重启 Apache。

```bash
    sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a>其他 Apache 建议

### <a name="additional-headers"></a>其他标头 
为了防止恶意攻击，应对一些标头进行修改或添加一些标头。 确保已安装 `mod_headers` 模块。

```bash
    sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking"></a>保护 Apache 免受点击劫持的侵害
点击劫持是一种可收集受感染用户的点击量的恶意技术。 点击劫持诱使受害者（访问者）点击受感染的网站。 使用 X-FRAME-OPTIONS 保护站点。

编辑 httpd.conf 文件。

```bash
    sudo nano /etc/httpd/conf/httpd.conf
```

添加行 `Header append X-FRAME-OPTIONS "SAMEORIGIN"` 并保存文件，然后重启 Apache。

#### <a name="mime-type-sniffing"></a>MIME 类型探查

此标头阻止 Internet Explorer 避开声明的内容类型对响应进行 MIME 探查，因为标头会指示浏览器不要替代响应内容类型。 凭借 nosniff 选项，如果服务器认为内容为文本/html，浏览器则将其呈现为文本/html。

编辑 httpd.conf 文件。

```bash
    sudo nano /etc/httpd/conf/httpd.conf
```

添加行 `Header set X-Content-Type-Options "nosniff"` 并保存文件，然后重启 Apache。

### <a name="load-balancing"></a>负载平衡 

此示例演示如何在同一实例计算机上的 CentOS 7 和 Kestrel 上设置和配置 Apache。  然而，为了不出现单一故障点；使用 mod_proxy_balancer 并修改 VirtualHost 可实现在 Apache 代理服务器后面管理 Web 应用的多个实例。

```bash
    sudo yum install mod_proxy_balancer
```

在配置文件中，`hellomvc` 应用的其他实例已设置为在端口 5001 上运行，并且使用具有两个成员的均衡器配置对 Proxy 部分进行了设置以负载均衡 byrequests。

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

### <a name="rate-limits"></a>速率限制
使用包括在 `htttpd` 模块中的 `mod_ratelimit` 可限制客户端的带宽量。 

```bash
    sudo nano /etc/httpd/conf.d/ratelimit.conf
```
示例文件将根位置下的带宽限制为 600 KB/秒。

```text
    <IfModule mod_ratelimit.c>
        <Location />
            SetOutputFilter RATE_LIMIT
            SetEnv rate-limit 600
        </Location>
    </IfModule>
```
