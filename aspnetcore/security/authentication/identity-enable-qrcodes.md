---
title: "启用 ASP.NET Core 中的身份验证器应用的 QR 代码生成"
author: rick-anderson
description: "启用 ASP.NET Core 中的身份验证器应用的 QR 代码生成"
keywords: "ASP.NET 核心、 MVC，QR 代码生成、 身份验证器、 2FA"
ms.author: riande
manager: wpickett
ms.date: 7/24/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: 4eb260ecb3d1f1797bed5ba82ef3fc0628f05b6f
ms.sourcegitcommit: bb615e57e80ae496a63a1bf53a3a9cffab2fce9b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2017
---
# <a name="enabling-qr-code-generation-for-authenticator-apps-in-aspnet-core"></a>启用 ASP.NET Core 中的身份验证器应用的 QR 代码生成

注意： 本主题适用于 ASP.NET Core 2.x 与 Razor 页。

ASP.NET 核心附带的单个身份验证的身份验证器应用程序的支持。 两个因素身份验证 (2FA) 身份验证器应用，使用基于时间的一次性密码算法 (TOTP)，是建议 2FA 的 approch 行业。 2FA 使用 TOTP 优于 SMS 2FA。 验证器应用提供哪些用户确认其用户名和密码后，必须输入一个 6 到 8 位代码。 通常在智能手机上安装验证器应用。

ASP.NET 核心 web 应用程序模板支持身份验证器，但不是提供对 QRCode 生成的支持。 QRCode 生成器轻松地 2FA 的安装程序。 本文档将指导你完成添加[QR 代码](https://wikipedia.org/wiki/QR_code)生成到 2FA 配置页。

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a>将 QR 代码添加到 2FA 配置页

这些说明使用*qrcode.js*从 https://davidshimjs.github.io/qrcodejs/ 存储库。

* 下载[qrcode.js javascript 库](https://davidshimjs.github.io/qrcodejs/)到`wwwroot\lib`项目文件夹中的。

* 在*Pages\Account\Manage\EnableAuthenticator.cshtml*文件中，找到`Scripts`文件末尾的部分：

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* 更新`Scripts`部分添加到引用`qrcodejs`你添加的库和生成的 QR 代码的调用。 其外观应，如下所示：

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")

    <script type="text/javascript" src="/lib/qrcode.js"></script>
    <script type="text/javascript">
        new QRCode(document.getElementById("qrCode"),
            {
                text: "@Html.Raw(Model.AuthenticatorUri)",
                width: 150,
                height: 150
            });
    </script>
}
```

* 删除的段落，您链接到这些说明。

运行你的应用，并确保你可以扫描 QR 代码和验证身份验证器证明的代码。

## <a name="change-the-site-name-in-the-qr-code"></a>更改 QR 代码中的站点名称

最初创建你的项目时选择的项目名称中获取 QR 代码中的站点名称。 你可以通过查找对其进行更改`GenerateQrCodeUri(string email, string unformattedKey)`中的方法*EnableAuthenticator.cshtml.cs*文件。 

从模板的默认代码将如下所示：

```c#
private string GenerateQrCodeUri(string email, string unformattedKey)
{
    return string.Format(
        AuthenicatorUriFormat,
        _urlEncoder.Encode("Razor Pages"),
        _urlEncoder.Encode(email),
        unformattedKey);
}
```

第二个参数的调用中`string.Format`是你的站点名称，取自解决方案名称。 它可以更改为任何值，但它必须始终为 URL 编码。

## <a name="using-a-different-qr-code-library"></a>使用不同的 QR 代码库

QR 代码库可以替换你首选的库。 HTML 包含`qrCode`元素在其中可以通过任何机制将 QR 代码你的库提供。

QR 代码的格式正确 URL 可用于:

* `AuthenticatorUri`模型的属性。
* `data-url`中的属性`qrCodeData`元素。 

使用`@Html.Raw`访问视图中的模型属性 （否则为 & 符的 url 中将双编码和 QR 代码的标签参数将被忽略）。
