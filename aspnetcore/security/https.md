---
title: "设置 ASP.NET Core 中开发的 HTTPS"
author: Rick-Anderson
description: "演示如何为 ASP.NET 核心 2.0 中的开发设置 HTTPS。"
keywords: "ASP.NET 核心，SSL，HTTPS"
ms.author: riande
manager: wpickett
ms.date: 05/10/2017
ms.topic: article
ms.assetid: 94f2f1a4-7d46-45e2-a085-a57916e41724
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/https
ms.openlocfilehash: 7913758ffa045dcf884d73eed9bab223b8d2c3fe
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2017
---
# <a name="setting-up-https-for-development-in-aspnet-core"></a>设置 ASP.NET Core 中开发的 HTTPS

> [!NOTE] 
> 本主题适用于 ASP.NET 核心 2.0 Preview 1

你可以配置你的应用程序在开发过程中使用 HTTPS 来模拟生产环境中的 HTTPS。 启用 HTTPS 可能需要启用与各种标识提供程序的集成 (如[Azure AD](https://azure.microsoft.com/services/active-directory)和[Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/))。

<a name="iisxpress"></a>

在 Windows 上如果你已安装 Visual Studio 或 IIS Express，IIS Express 开发证书将在 LocalMachine 证书存储区。 你可以更新项目属性中使用此证书时 IIS Express 后面运行 Visual Studio 中。

   * 在解决方案资源管理器，右键单击该项目并选择**属性**。
   * 在左窗格中，选择**调试**。
   * 检查**启用 SSL**
   * 复制 SSL URL 并将其粘贴到**应用 URL**

![调试 web 应用程序属性选项卡](enforcing-ssl/_static/ssl.png)

用于开发可以使用 IIS Express 开发证书，如果可用，或创建新证书出于开发目的。 应在配置的开发证书`appsettings.Development.json`文件，以便在生产环境中不使用它：

```json
{
  "Certificates": {
    "HTTPS": {
      "Source": "Store",
      "StoreLocation": "LocalMachine",
      "StoreName": "My",
      "Subject": "CN=localhost",
      "AllowInvalid": true
    }
  }
}
```

具有此配置，在生产环境中运行的应用将引发异常指示"没有名为 HTTPS 在当前环境 （生产） 的配置中找到的证书"。 若要切换[环境](xref:fundamentals/environments)到`Development`，将其设置`ASPNETCORE_ENVIRONMENT`环境变量`Development`。

如果你没有 IIS Express 开发安装的证书，你可以自行创建开发证书。 在 Windows 上，你可以创建开发证书并将其添加到当前用户的受信任的根存储中，通过在提升的提示符中运行以下 PowerShell 命令：

```powershell
$cert = New-SelfSignedCertificate -Subject localhost -DnsName localhost -FriendlyName "ASP.NET Core Development" -KeyUsage DigitalSignature -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1") 
Export-Certificate -Cert $cert -FilePath cert.cer
Import-Certificate -FilePath cert.cer -CertStoreLocation cert:/CurrentUser/Root
```

<a name="OpenSSL"></a>

## <a name="kestrel-on--macos-and-linux"></a>在 macOS 和 Linux 上的 kestrel

你可以配置 Kestrel 以通过配置终结点所需的 IP 地址、 端口和证书通过 HTTPS 侦听。 可以配置的内联，该证书或位于顶级`Certificates`部分，然后按名称引用：

```json
{
  "Kestrel": {
    "Endpoints": {
      "LocalhostHttps": {
        "Address": "127.0.0.1",
        "Port": "43434",
        "Certificate": "HTTPS"
      }
    }
  }
}

```

在 macOS 和 Linux 上，你可以创建使用 HTTPS 的自签名的证书[OpenSSL](https://www.openssl.org/):

```bash
openssl req -new -x509 -newkey rsa:2048 -keyout localhost.key -out localhost.cer -days 365 -subj /CN=localhost
openssl pkcs12 -export -out certificate.pfx -inkey localhost.key -in localhost.cer
```

一次`certificate.pfx`生成文件，配置中的 HTTPS 证书你`appsettings.Development.json`文件：

```json
{
  "Certificates": {
    "HTTPS": {
      "Source": "File",
      "Path": "certificate.pfx"
    }
  }
}
```

你还需要通过设置"的证书： HTTPS:Password"配置属性中指定证书的密码。 不应以明文形式存储密码。 请参阅[安全存储的应用程序机密期间开发](app-secrets.md)为合适的处理操作的证书密码。

在 macOS 上可以[将证书添加到你 keychain](https://support.apple.com/kb/PH20129?locale=en_US)和[更改其信任设置](https://support.apple.com/kb/PH20127?locale=en_US&viewlocale=en_US)，以便在开发过程是为支持 HTTPS 的受信任。 若要将证书添加到你的密钥链 (等效于`CurrentUser/My`存储在 Windows 上) 运行以下命令：

```bash
security import certificate.pfx -k ~/Library/Keychains/login.keychain-db
```

然后可以信任的证书：

```bash
security add-trusted-cert localhost.cer
```

然后，你可以配置你的应用以如下的开发中使用此证书：

```json
{
  "Certificates": {
    "HTTPS": {
      "Source": "Store",
      "StoreLocation": "CurrentUser",
      "StoreName": "My",
      "Subject": "CN=localhost",
      "AllowInvalid": true
    }
  }
}
```
