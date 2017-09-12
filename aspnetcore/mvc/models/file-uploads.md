---
title: "在 ASP.NET 核心中的文件上载"
author: ardalis
description: "如何使用模型绑定和流式处理以 ASP.NET 核心 MVC 上载文件。"
keywords: "ASP.NET 核心，文件上载模型绑定，IFormFile，流式处理"
ms.author: riande
manager: wpickett
ms.date: 7/5/2017
ms.topic: article
ms.assetid: ebc98159-a028-4a94-b06c-43981c79c6be
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/models/file-uploads
ms.openlocfilehash: 3d42fd0657bcfb4b0fdab699bbcb572e5736688c
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="file-uploads-in-aspnet-core"></a>在 ASP.NET 核心中的文件上载

通过[Steve Smith](https://ardalis.com/)

ASP.NET MVC 操作支持使用简单模型绑定较小的文件或流的较大的文件的一个或多个文件上载。

[查看或从 GitHub 下载示例](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/file-uploads/sample/FileUploadSample)

## <a name="uploading-small-files-with-model-binding"></a>上载了模型绑定的小文件

若要上载的小文件，可以使用多个部分组成的 HTML 窗体或构造使用 JavaScript 的 POST 请求。 使用 Razor，支持多个已上载的文件，示例窗体如下所示：

```html
<form method="post" enctype="multipart/form-data" asp-controller="UploadFiles" asp-action="Index">
    <div class="form-group">
        <div class="col-md-10">
            <p>Upload one or more files using this form:</p>
            <input type="file" name="files" multiple />
        </div>
    </div>
    <div class="form-group">
        <div class="col-md-10">
            <input type="submit" value="Upload" />
        </div>
    </div>
</form>
```

为了支持文件上载，必须指定 HTML 窗体`enctype`的`multipart/form-data`。 `files`上面所示的输入的元素支持将多个文件上载。 省略`multiple`此输入的元素，以便只需上载单个文件属性。 上面的标记将呈现在浏览器中为：

![文件上载窗体](file-uploads/_static/upload-form.png)

可以通过访问各个文件上载到服务器[模型绑定](xref:mvc/models/model-binding)使用[IFormFile](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.iformfile)接口。 `IFormFile`具有以下结构：

```csharp
public interface IFormFile
{
    string ContentType { get; }
    string ContentDisposition { get; }
    IHeaderDictionary Headers { get; }
    long Length { get; }
    string Name { get; }
    string FileName { get; }
    Stream OpenReadStream();
    void CopyTo(Stream target);
    Task CopyToAsync(Stream target, CancellationToken cancellationToken = null);
}
```

> [!WARNING]
> 不依赖于或不信任`FileName`而不进行验证的属性。 `FileName`属性应仅用于显示目的。

上载文件使用模型绑定时与`IFormFile`接口，操作方法可以接受单个`IFormFile`或`IEnumerable<IFormFile>`(或`List<IFormFile>`) 表示多个文件。 下面的示例循环访问一个或多个已上载的文件，将它们保存到本地文件系统中，并返回的总数和上载的文件的大小。

[!INCLUDE [GetTempFileName](../../includes/GetTempFileName.md)]

[!code-csharp[Main](file-uploads/sample/FileUploadSample/Controllers/UploadFilesController.cs?name=snippet1)]

使用上载的文件`IFormFile`技术进行处理之前缓冲在内存中或在 web 服务器上的磁盘上。 在操作方法中，`IFormFile`内容进行访问以流的形式。 除了本地文件系统中，文件可以流式传输到[Azure Blob 存储](https://azure.microsoft.com/documentation/articles/vs-storage-aspnet5-getting-started-blobs/)或[实体框架](https://docs.microsoft.com/ef/core/index)。

若要在使用实体框架的数据库中存储二进制文件数据，定义类型的属性`byte[]`实体上：

```csharp
public class ApplicationUser : IdentityUser
{
    public byte[] AvatarImage { get; set; }
}
```

指定类型的视图模型属性`IFormFile`:

```csharp
public class RegisterViewModel
{
    // other properties omitted

    public IFormFile AvatarImage { get; set; }
}
```

> [!NOTE]
> `IFormFile`可以在直接作为操作方法参数或作为视图模型属性，如上所示。

复制`IFormFile`流并保存到的字节数组：

```csharp
// POST: /Account/Register
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Register(RegisterViewModel model)
{
    ViewData["ReturnUrl"] = returnUrl;
    if  (ModelState.IsValid)
    {
        var user = new ApplicationUser {
          UserName = model.Email,
          Email = model.Email
        };
        using (var memoryStream = new MemoryStream())
        {
            await model.AvatarImage.CopyToAsync(memoryStream);
            user.AvatarImage = memoryStream.ToArray();
        }
    // additional logic omitted
    
    // Don't rely on or trust the model.AvatarImage.FileName property 
    // without validation.
}
```

> [!NOTE]
> 要格外小心时将二进制数据存储在关系数据库，因为它可能会反过来影响性能。

## <a name="uploading-large-files-with-streaming"></a>上载大型文件流式处理

如果大小或文件上载频率导致应用程序的资源问题，请考虑流式处理文件上载，而不是在其整个中进行缓冲，如上所示的模型绑定方法一样。 在使用`IFormFile`和模型绑定正在更简单的解决方案，流式处理需要大量的步骤，若要正确实现。

> [!NOTE]
> 任何超过 64 KB 的单个缓冲的文件将从移动 RAM 到磁盘上的临时文件服务器上。 使用文件上载的资源 （磁盘，RAM） 依赖于的数量和大小的并发文件上载。 流式处理不是如此之多关于性能，它是有关伸缩。 如果你尝试在缓冲区过多上载，你的站点时将崩溃时内存或磁盘空间不足。

下面的示例演示如何使用 JavaScript/角流式处理到的控制器操作。 使用自定义的筛选器属性，而不是在请求正文中的 HTTP 标头中传递，将生成文件的 antiforgery 令牌。 由于操作方法直接处理上载的数据，模型绑定被禁用的另一个筛选器。 在操作内，使用读取窗体的内容`MultipartReader`，读取每个单独`MultipartSection`、 处理文件，或根据需要存储内容。 已读取所有部分，该操作执行其自己的模型绑定。

初始操作加载窗体并将 antiforgery 令牌保存在 cookie 中 (通过`GenerateAntiforgeryTokenCookieForAjax`属性):

```csharp
[HttpGet]
[GenerateAntiforgeryTokenCookieForAjax]
public IActionResult Index()
{
    return View();
}
```

该属性将使用 ASP.NET Core 内置[Antiforgery](xref:security/anti-request-forgery)设置具有请求令牌的 cookie 的支持：

[!code-csharp[Main](file-uploads/sample/FileUploadSample/Filters/GenerateAntiforgeryTokenCookieForAjaxAttribute.cs?name=snippet1)]

角自动名为请求标头中传递 antiforgery 令牌`X-XSRF-TOKEN`。 ASP.NET 核心 MVC 应用程序配置为在其配置中在此标头是指*Startup.cs*:

[!code-csharp[Main](file-uploads/sample/FileUploadSample/Startup.cs?name=snippet1)]

`DisableFormValueModelBinding`属性，如下所示，用来禁用模型绑定`Upload`操作方法。

[!code-csharp[Main](file-uploads/sample/FileUploadSample/Filters/DisableFormValueModelBindingAttribute.cs?name=snippet1)]

因为禁用了模型绑定，`Upload`操作方法不接受参数。 它可直接与`Request`属性`ControllerBase`。 A`MultipartReader`用于读取每个部分。 该文件将保存为 GUID 文件名和键/值数据存储在`KeyValueAccumulator`。 已读取所有部分的内容后`KeyValueAccumulator`用于将窗体数据绑定到的模型类型。

完整`Upload`方法如下所示：

[!INCLUDE [GetTempFileName](../../includes/GetTempFileName.md)]

[!code-csharp[Main](file-uploads/sample/FileUploadSample/Controllers/StreamingController.cs?name=snippet1)]

## <a name="troubleshooting"></a>疑难解答

下面是一些使用上载文件和其可能的解决方案时遇到的常见问题。

### <a name="unexpected-not-found-error-with-iis"></a>使用 IIS 的意外找不到错误

以下的错误指示您的文件上载超过服务器的配置`maxAllowedContentLength`:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

默认设置是`30000000`，即大约 28.6 MB。 可以通过编辑自定义值*web.config*:

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- This will handle requests up to 50MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

此设置仅适用于 IIS 中。 Kestrel 上承载时，默认情况下不会出现行为。 有关详细信息，请参阅[请求限制\<requestLimits\>](https://docs.microsoft.com/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)。

### <a name="null-reference-exception-with-iformfile"></a>与 IFormFile null 引用异常

如果你的控制器是接受上载的文件使用`IFormFile`但您查找的值始终为 null，请确认你的 HTML 窗体指定`enctype`值`multipart/form-data`。 如果不设置此属性`<form>`元素，不会出现文件上载和任何绑定`IFormFile`自变量将为 null。
