---
title: "ASP.NET 核心中的文件提供程序"
author: ardalis
description: "了解如何 ASP.NET Core 提取通过文件提供程序使用的文件系统访问权限。"
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: 1e35d362-0005-4f84-a187-274ca203a787
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/file-providers
ms.openlocfilehash: fd847db992b20ab096b54378418d2b9bccff67be
ms.sourcegitcommit: 2b263e87217658caa42eedc4f9d2d21ef0ab5d59
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2018
---
# <a name="file-providers-in-aspnet-core"></a>ASP.NET 核心中的文件提供程序

通过[Steve Smith](https://ardalis.com/)

ASP.NET 核心抽象化通过文件提供程序使用的文件系统访问权限。

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/file-providers/sample)（[如何下载](xref:tutorials/index#how-to-download-a-sample)）

## <a name="file-provider-abstractions"></a>文件提供程序抽象

文件提供程序通过文件系统是一种抽象。 主界面是`IFileProvider`。 `IFileProvider`公开一些方法以获取文件信息 (`IFileInfo`)，目录信息 (`IDirectoryContents`)，并设置更改通知 (使用`IChangeToken`)。

`IFileInfo`提供方法和属性有关单个文件或目录。 它具有两个布尔属性，`Exists`和`IsDirectory`，描述文件的属性以及`Name`， `Length` （以字节为单位），和`LastModified`日期。 你可以从文件使用读取其`CreateReadStream`方法。

## <a name="file-provider-implementations"></a>文件提供程序实现

三种实现`IFileProvider`可用： 物理、 嵌入和复合。 物理提供程序用于访问实际系统文件。 嵌入的提供程序用于访问嵌入在程序集中的文件。 复合的提供程序用于提供一个或多个其他提供商组合对文件和目录的访问权限。

### <a name="physicalfileprovider"></a>PhysicalFileProvider

`PhysicalFileProvider`提供对物理文件系统的访问。 它所包装`System.IO.File`类型 （用于物理提供程序），作用域到一个目录及其子级的所有路径。 此作用域限制对特定目录及其子级，防止对在此边界之外的文件系统的访问的访问。 时实例化此提供程序，你必须向它提供可用作对此提供程序所做的所有请求的基路径 （限制外部此路径的访问） 的目录路径。 在 ASP.NET Core 应用程序，你可以实例化`PhysicalFileProvider`直接，提供程序，也可以请求`IFileProvider`中的控制器或服务的构造函数通过[依赖关系注入](dependency-injection.md)。 后一种方法通常会产生更加灵活和可测试解决方案。

下面的示例演示如何创建`PhysicalFileProvider`。


```csharp
IFileProvider provider = new PhysicalFileProvider(applicationRoot);
IDirectoryContents contents = provider.GetDirectoryContents(""); // the applicationRoot contents
IFileInfo fileInfo = provider.GetFileInfo("wwwroot/js/site.js"); // a file under applicationRoot
```

可以循环访问其目录内容，也可以通过提供子路径获取特定文件的信息。

若要从控制器请求提供程序，指定控制器的构造函数中，并将其分配给本地字段。 使用你的操作方法中的本地实例：

[!code-csharp[Main](file-providers/sample/src/FileProviderSample/Controllers/HomeController.cs?highlight=5,7,12&range=6-19)]

然后，在应用程序的创建提供程序`Startup`类：

[!code-csharp[Main](file-providers/sample/src/FileProviderSample/Startup.cs?highlight=35,40&range=1-43)]

在*Index.cshtml*视图中，循环访问`IDirectoryContents`提供：

[!code-html[Main](file-providers/sample/src/FileProviderSample/Views/Home/Index.cshtml?highlight=2,7,9,11,15)]

结果：

![文件提供程序示例应用程序列表物理文件和文件夹](file-providers/_static/physical-directory-listing.png)

### <a name="embeddedfileprovider"></a>EmbeddedFileProvider

`EmbeddedFileProvider`用于访问嵌入在程序集中的文件。 在.NET 核心中要将文件嵌入程序集中`<EmbeddedResource>`中的元素*.csproj*文件：

[!code-json[Main](file-providers/sample/src/FileProviderSample/FileProviderSample.csproj?range=13-18)]

你可以使用[组合模式](#globbing-patterns)时指定要嵌入到程序集中的文件。 这些模式可用于以匹配一个或多个文件。

> [!NOTE]
> 不太可能会想要实际上将每个.js 文件嵌入在其程序集; 中的项目上面的示例是仅供演示。

在创建时`EmbeddedFileProvider`，传递给其构造函数将读取它的程序集。

```csharp
var embeddedProvider = new EmbeddedFileProvider(Assembly.GetEntryAssembly());
```

上述代码段演示如何创建`EmbeddedFileProvider`有权访问当前正在执行的程序集。

更新示例应用程序以使用`EmbeddedFileProvider`导致生成以下输出：

![列出嵌入的文件的文件提供程序示例应用程序](file-providers/_static/embedded-directory-listing.png)

> [!NOTE]
> 嵌入的资源不会公开目录。 相反，（通过其命名空间） 的资源路径嵌入在其文件名使用`.`分隔符。

> [!TIP]
> `EmbeddedFileProvider`构造函数接受一个可选`baseNamespace`参数。 指定此将作用域调用`GetDirectoryContents`对这些资源在提供的命名空间。

### <a name="compositefileprovider"></a>CompositeFileProvider

`CompositeFileProvider`将合并`IFileProvider`情况下，公开一个接口用于处理从多个提供程序的文件。 在创建时`CompositeFileProvider`，传递一个或多个`IFileProvider`给其构造函数的实例：

[!code-csharp[Main](file-providers/sample/src/FileProviderSample/Startup.cs?highlight=3&range=35-37)]

更新示例应用程序以使用`CompositeFileProvider`包括这两个物理和嵌入配置提供程序之前，请在下面的输出结果：

![列出的物理文件和文件夹以及嵌入的文件的文件提供程序示例应用程序](file-providers/_static/composite-directory-listing.png)

## <a name="watching-for-changes"></a>监视的更改

`IFileProvider` `Watch`方法使您能够监视一个或多个文件或目录的更改。 此方法接受路径字符串，可以使用该对话框[组合模式](#globbing-patterns)来指定多个文件，并返回`IChangeToken`。 此令牌公开`HasChanged`属性，可以检查和`RegisterChangeCallback`为指定的路径字符串检测到更改时调用的方法。 请注意每个更改令牌仅在响应的单个更改调用其关联的回调。 若要启用持续的监视，你可以使用`TaskCompletionSource`如下所示，或重新创建`IChangeToken`实例以响应更改。

在本文中的示例中，一个控制台应用程序配置为每当修改文本文件时显示一条消息：

[!code-csharp[Main](file-providers/sample/src/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

保存此文件若干次后的结果：

![命令窗口后执行 dotnet 运行显示监视 quotes.txt 文件的更改的应用程序和文件已更改五次。](file-providers/_static/watch-console.png)

> [!NOTE]
> 某些文件系统，例如 Docker 容器和网络共享，可能会不可靠地发送更改通知。 设置`DOTNET_USE_POLLINGFILEWATCHER`环境变量`1`或`true`轮询每 4 秒的文件系统更改。

## <a name="globbing-patterns"></a>组合模式

文件系统路径中使用通配符模式调用*组合模式*。 这些简单的模式可以用于指定的文件组。 两个通配符字符都是`*`和`**`。

**`*`**

   匹配任何内容在当前文件夹级别，或任何文件名或任何文件扩展名。 匹配项即可终止`/`和`.`文件路径中的字符。

<strong><code>**</code></strong>

   跨多个目录级别匹配任何内容。 可用于以递归方式与在目录层次结构中的许多文件匹配。

### <a name="globbing-pattern-examples"></a>组合模式示例

**`directory/file.txt`**

   匹配特定的目录中的特定文件。

**<code>directory/*.txt</code>**

   匹配的所有文件的`.txt`特定目录中的扩展。

**`directory/*/bower.json`**

   匹配所有`bower.json`目录恰好一个级别中的文件`directory`目录。

**<code>directory/&#42;&#42;/&#42;.txt</code>**

   匹配的所有文件的`.txt`下的任何位置找到扩展`directory`目录。

## <a name="file-provider-usage-in-aspnet-core"></a>在 ASP.NET Core 文件提供程序使用情况

ASP.NET 核心的多个部分利用文件提供程序。 `IHostingEnvironment`公开应用程序的内容的根和 web 根与`IFileProvider`类型。 静态文件中间件使用文件提供程序查找静态文件。 Razor 使大量使用`IFileProvider`定位视图。 Dotnet 的发布功能使用文件提供程序和组合模式，以指定应发布哪些文件。

## <a name="recommendations-for-use-in-apps"></a>用在应用程序的建议

ASP.NET Core 应用程序需要文件系统访问权限，你可以请求的实例`IFileProvider`通过依赖关系注入，然后使用其方法来执行权限，此示例中所示。 这可以一次，配置提供程序，当应用程序启动，并减少你的应用程序实例化的实现类型的数目。
