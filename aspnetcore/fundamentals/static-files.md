---
title: "使用 ASP.NET Core 中的静态文件"
author: rick-anderson
description: "使用 ASP.NET Core 上的静态文件"
keywords: "ASP.NET 核心，静态文件、 静态资产，HTML、 CSS、 JavaScript"
ms.author: riande
manager: wpickett
ms.date: 4/07/2017
ms.topic: article
ms.assetid: e32245c7-4eee-4831-bd2e-915dbf9f5f70
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/static-files
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 69a4542c9b2a0d7091d05d42029e68384b760dd7
ms.sourcegitcommit: 6e83c55eb0450a3073ef2b95fa5f5bcb20dbbf89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2017
---
# <a name="working-with-static-files-in-aspnet-core"></a>使用 ASP.NET Core 中的静态文件

<a name=fundamentals-static-files></a>

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

静态文件，如 HTML、 CSS、 映像和 JavaScript，作为 ASP.NET Core 应用程序可以提供给客户端直接的资产。

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/static-files/sample)

## <a name="serving-static-files"></a>为静态文件提供服务

静态文件通常位于`web root`(*\<内容根 > / wwwroot*) 文件夹。 请参阅[内容根](xref:fundamentals/index#content-root)和[Web 根](xref:fundamentals/index#web-root)有关详细信息。 通常设置为当前目录的内容的根，以便你的项目的`web root`将开发中找到。

[!code-csharp[Main](../common/samples/WebApplication1/Program.cs?highlight=5&start=12&end=22)]

可以下任何文件夹中存储静态文件`web root`和访问与该根的相对路径。 例如，当创建默认 Web 应用程序项目中使用 Visual Studio，还有一些文件夹中创建*wwwroot*文件夹- *css*，*映像*，和*js*。 用于访问中的图像的 URI*映像*子文件夹：

* `http://<app>/images/<imageFileName>`
* `http://localhost:9189/images/banner3.svg`

在静态文件提供的顺序，你必须配置[中间件](middleware.md)将静态文件添加到管道。 可以通过上添加一个依赖项配置静态文件中间件*Microsoft.AspNetCore.StaticFiles*包到你的项目并调用`UseStaticFiles`扩展方法从`Startup.Configure`:

[!code-csharp[Main](../fundamentals/static-files/sample/StartupStaticFiles.cs?highlight=3&name=snippet1)]

`app.UseStaticFiles();`使中的文件`web root`(*wwwroot*默认情况下) servable。 稍后我将介绍如何使其他目录内容与 servable `UseStaticFiles`。

必须包括 NuGet 包"Microsoft.AspNetCore.StaticFiles"。

> [!NOTE]
> `web root`默认为*wwwroot*目录中，但你可以设置`web root`目录`UseWebRoot`。

假设你有想要提供的静态文件位于项目层次结构`web root`。 例如: 

* wwwroot
  * css
  * 图像
  * ...
* MyStaticFiles
  * test.png

请求访问*test.png*，配置静态文件中间件，如下所示：

[!code-csharp[Main](../fundamentals/static-files/sample/StartupTwoStaticFiles.cs?highlight=5,6,7,8,9,10&name=snippet1)]

对请求`http://<app>/StaticFiles/test.png`将提供*test.png*文件。

`StaticFileOptions()`可以设置响应标头。 例如，下面的代码将设置从提供的静态文件*wwwroot*文件夹和集`Cache-Control`标头以使它们为 10 分钟 （600 秒） 公开一个可缓存：

[!code-csharp[Main](../fundamentals/static-files/sample/StartupAddHeader.cs?name=snippet1)]

![已添加显示的缓存控制标头的响应标头](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>静态文件授权

静态文件模块提供**没有**授权检查。 由它提供任何文件包括正在*wwwroot*可公开访问。 若要为文件服务基于授权：

* 将其外部存储*wwwroot*和访问静态文件中间件任何目录**和**

* 通过返回的控制器操作提供它们`FileResult`应用授权的位置

## <a name="enabling-directory-browsing"></a>启用目录浏览

目录浏览可让你的 web 应用的用户查看的目录和指定的目录中的文件列表。 默认情况下，出于安全原因禁用目录浏览 (请参阅[注意事项](#considerations))。 若要启用目录浏览，请调用`UseDirectoryBrowser`扩展方法从`Startup.Configure`:

[!code-csharp[Main](static-files/sample/StartupBrowse.cs?name=snippet1)]

并添加所需的服务通过调用`AddDirectoryBrowser`扩展方法从`Startup.ConfigureServices`:

[!code-csharp[Main](static-files/sample/StartupBrowse.cs?name=snippet2)]

上面的代码中允许目录浏览的*wwwroot/images*文件夹使用 URL http://\<应用 > / MyImages，以链接到每个文件和文件夹：

![目录浏览](static-files/_static/dir-browse.png)

请参阅[注意事项](#considerations)上启用浏览时安全风险。

请注意两个`app.UseStaticFiles`调用。 服务的 CSS、 映像和中的 JavaScript 所需的第一个*wwwroot*文件夹，并为目录浏览的第二个调用*wwwroot/images*文件夹使用 URL http://\<应用> / MyImages:

[!code-csharp[Main](static-files/sample/StartupBrowse.cs?highlight=3,5&name=snippet1)]

## <a name="serving-a-default-document"></a>为提供服务的默认文档

设置默认主页提供站点访问者在访问你的站点时的开端。 为了使你的 Web 应用，以便为默认页上，而无需完全限定 URI 用户提供服务，在调用`UseDefaultFiles`扩展方法从`Startup.Configure`，如下所示。

[!code-csharp[Main](../fundamentals/static-files/sample/StartupEmpty.cs?highlight=3&name=snippet1)]

> [!NOTE]
> `UseDefaultFiles`前必须调用`UseStaticFiles`用于默认文件。 `UseDefaultFiles`是不实际处理该文件的 URL 重新编写器。 你必须启用静态文件中间件 (`UseStaticFiles`) 来处理该文件。

与`UseDefaultFiles`，将搜索到的文件夹的请求：

* default.htm
* default.html
* index.htm
* index.html

从列表中找到的第一个文件将提供服务，就像该请求是完全限定的 URI （尽管浏览器 URL 将继续显示所请求的 URI）。

下面的代码演示如何更改默认的文件名为*mydefault.html*。

[!code-csharp[Main](static-files/sample/StartupDefault.cs?name=snippet1)]

## <a name="usefileserver"></a>UseFileServer

`UseFileServer`将功能组合`UseStaticFiles`， `UseDefaultFiles`，和`UseDirectoryBrowser`。

下面的代码使静态文件和默认的文件以提供服务，但不允许目录浏览：

```csharp
app.UseFileServer();
   ```

以下代码启用静态文件，默认文件和目录浏览：

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
   ```

请参阅[注意事项](#considerations)上启用浏览时安全风险。 与`UseStaticFiles`， `UseDefaultFiles`，和`UseDirectoryBrowser`，如果你想要提供文件存在于外部`web root`，实例化和配置`FileServerOptions`将作为参数传递的对象`UseFileServer`。 例如，给定以下目录层次结构中你的 Web 应用：

* wwwroot

  * css

  * 图像

  * ...

* MyStaticFiles

  * test.png

  * default.html

使用上面的层次结构示例中，你可能想要启用静态文件、 默认文件和浏览`MyStaticFiles`目录。 在下面的代码段中，这来实现通过单个调用`FileServerOptions`。

[!code-csharp[Main](static-files/sample/StartupUseFileServer.cs?highlight=5,6,7,8,9,10,11&name=snippet1)]

如果`enableDirectoryBrowsing`设置为`true`需要调用`AddDirectoryBrowser`扩展方法从`Startup.ConfigureServices`:

[!code-csharp[Main](static-files/sample/StartupUseFileServer.cs?name=snippet2)]

使用的文件层次结构和上面的代码：

| URI            |                             响应  |
| ------- | ------|
| `http://<app>/StaticFiles/test.png`    |      MyStaticFiles/test.png |
| `http://<app>/StaticFiles`              |     MyStaticFiles/default.html |

如果没有默认命名文件位于*MyStaticFiles*目录、 http://\<应用 > / StaticFiles 返回具有可单击链接列出的目录：

![静态文件列表](static-files/_static/db2.PNG)

> [!NOTE]
> `UseDefaultFiles`和`UseDirectoryBrowser`需要 url http://\<应用 > / 不包含尾随斜杠和原因 StaticFiles 客户端将重定向到 http://\<应用 > /StaticFiles/ （添加尾部反斜杠）。 如果没有在文档中的尾随斜杠相对 Url 将是不正确的。

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

`FileExtensionContentTypeProvider`类包含将文件扩展名映射到 MIME 内容类型的集合。 在下面的示例中，于已知的 MIME 类型注册多个文件扩展名、".rtf"替换为，并删除".mp4"。

[!code-csharp[Main](../fundamentals/static-files/sample/StartupFileExtensionContentTypeProvider.cs?highlight=3,4,5,6,7,8,9,10,11,12,19&name=snippet1)]

请参阅[MIME 内容类型](http://www.iana.org/assignments/media-types/media-types.xhtml)。

## <a name="non-standard-content-types"></a>非标准的内容类型

ASP.NET 静态文件中间件理解几乎 400 已知的文件内容类型。 如果用户请求了未知的文件类型的文件，静态文件中间件将返回 HTTP 404 （未找到） 响应。 如果启用了目录浏览，将显示文件的链接，但的 URI 将返回 HTTP 404 错误。

以下代码启用为未知的类型，并将呈现为图像未知的文件。

[!code-csharp[Main](static-files/sample/StartupServeUnknownFileTypes.cs?name=snippet1)]

使用上面的代码中，具有未知的内容类型的文件的请求将返回为映像。

>[!WARNING]
> 启用`ServeUnknownFileTypes`存在安全风险，并使用它阻止这么做。  `FileExtensionContentTypeProvider`（上文所述） 提供一个更安全的替代方法为文件使用非标准扩展提供服务。

### <a name="considerations"></a>注意事项

>[!WARNING]
> `UseDirectoryBrowser`和`UseStaticFiles`可能会泄漏机密。 我们建议你**不**启用目录浏览在生产环境中。 请注意你使用有关哪些目录的启用`UseStaticFiles`或`UseDirectoryBrowser`因为整个目录及其所有子目录将可访问。 我们建议你如保留其自己的目录中的公共内容*\<内容的根 > / wwwroot*、 离开应用程序视图、 配置文件，等等。

* 通过公开的内容的 Url`UseDirectoryBrowser`和`UseStaticFiles`受到的区分大小写和其基础文件系统的字符限制。 例如，Windows 不区分大小写，但不是 Mac 和 Linux。

* 在 IIS 中承载的 ASP.NET Core 应用程序使用 ASP.NET 核心模块将所有请求都转发到包括静态文件的请求的应用程序。 因为它不会获取一个机会处理请求，它们均由 ASP.NET 核心模块处理之前未使用 IIS 静态文件处理程序。

* 若要删除 IIS 静态文件处理程序 （在服务器或网站级别）：

     * 导航到**模块**功能

     * 选择**StaticFileModule**列表中

     * 点击**删除**中**操作**侧栏

>[!WARNING]
> 如果启用了 IIS 静态文件处理程序**和**ASP.NET 核心模块 (ANCM) 未正确配置 (例如如果*web.config*未部署)，将提供静态文件。

* 代码文件 （包括 c# 和 Razor） 应放置在应用程序项目之外`web root`(*wwwroot*默认情况下)。 这将创建应用程序的客户端内容和服务器端源代码，以防止服务器端代码将泄漏之间完全分离。

## <a name="additional-resources"></a>其他资源

* [中间件](middleware.md)

* [ASP.NET Core 简介](../index.md)
