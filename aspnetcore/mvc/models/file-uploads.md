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
# <a name="file-uploads-in-aspnet-core"></a><span data-ttu-id="052a4-104">在 ASP.NET 核心中的文件上载</span><span class="sxs-lookup"><span data-stu-id="052a4-104">File uploads in ASP.NET Core</span></span>

<span data-ttu-id="052a4-105">通过[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="052a4-105">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="052a4-106">ASP.NET MVC 操作支持使用简单模型绑定较小的文件或流的较大的文件的一个或多个文件上载。</span><span class="sxs-lookup"><span data-stu-id="052a4-106">ASP.NET MVC actions support uploading of one or more files using simple model binding for smaller files or streaming for larger files.</span></span>

[<span data-ttu-id="052a4-107">查看或从 GitHub 下载示例</span><span class="sxs-lookup"><span data-stu-id="052a4-107">View or download sample from GitHub</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/file-uploads/sample/FileUploadSample)

## <a name="uploading-small-files-with-model-binding"></a><span data-ttu-id="052a4-108">上载了模型绑定的小文件</span><span class="sxs-lookup"><span data-stu-id="052a4-108">Uploading small files with model binding</span></span>

<span data-ttu-id="052a4-109">若要上载的小文件，可以使用多个部分组成的 HTML 窗体或构造使用 JavaScript 的 POST 请求。</span><span class="sxs-lookup"><span data-stu-id="052a4-109">To upload small files, you can use a multi-part HTML form or construct a POST request using JavaScript.</span></span> <span data-ttu-id="052a4-110">使用 Razor，支持多个已上载的文件，示例窗体如下所示：</span><span class="sxs-lookup"><span data-stu-id="052a4-110">An example form using Razor, which supports multiple uploaded files, is shown below:</span></span>

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

<span data-ttu-id="052a4-111">为了支持文件上载，必须指定 HTML 窗体`enctype`的`multipart/form-data`。</span><span class="sxs-lookup"><span data-stu-id="052a4-111">In order to support file uploads, HTML forms must specify an `enctype` of `multipart/form-data`.</span></span> <span data-ttu-id="052a4-112">`files`上面所示的输入的元素支持将多个文件上载。</span><span class="sxs-lookup"><span data-stu-id="052a4-112">The `files` input element shown above supports uploading multiple files.</span></span> <span data-ttu-id="052a4-113">省略`multiple`此输入的元素，以便只需上载单个文件属性。</span><span class="sxs-lookup"><span data-stu-id="052a4-113">Omit the `multiple` attribute on this input element to allow just a single file to be uploaded.</span></span> <span data-ttu-id="052a4-114">上面的标记将呈现在浏览器中为：</span><span class="sxs-lookup"><span data-stu-id="052a4-114">The above markup renders in a browser as:</span></span>

![文件上载窗体](file-uploads/_static/upload-form.png)

<span data-ttu-id="052a4-116">可以通过访问各个文件上载到服务器[模型绑定](xref:mvc/models/model-binding)使用[IFormFile](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.iformfile)接口。</span><span class="sxs-lookup"><span data-stu-id="052a4-116">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using the [IFormFile](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.iformfile) interface.</span></span> <span data-ttu-id="052a4-117">`IFormFile`具有以下结构：</span><span class="sxs-lookup"><span data-stu-id="052a4-117">`IFormFile` has the following structure:</span></span>

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
> <span data-ttu-id="052a4-118">不依赖于或不信任`FileName`而不进行验证的属性。</span><span class="sxs-lookup"><span data-stu-id="052a4-118">Don't rely on or trust the `FileName` property without validation.</span></span> <span data-ttu-id="052a4-119">`FileName`属性应仅用于显示目的。</span><span class="sxs-lookup"><span data-stu-id="052a4-119">The `FileName` property should only be used for display purposes.</span></span>

<span data-ttu-id="052a4-120">上载文件使用模型绑定时与`IFormFile`接口，操作方法可以接受单个`IFormFile`或`IEnumerable<IFormFile>`(或`List<IFormFile>`) 表示多个文件。</span><span class="sxs-lookup"><span data-stu-id="052a4-120">When uploading files using model binding and the `IFormFile` interface, the action method can accept either a single `IFormFile` or an `IEnumerable<IFormFile>` (or `List<IFormFile>`) representing several files.</span></span> <span data-ttu-id="052a4-121">下面的示例循环访问一个或多个已上载的文件，将它们保存到本地文件系统中，并返回的总数和上载的文件的大小。</span><span class="sxs-lookup"><span data-stu-id="052a4-121">The following example loops through one or more uploaded files, saves them to the local file system, and returns the total number and size of files uploaded.</span></span>

[!INCLUDE [GetTempFileName](../../includes/GetTempFileName.md)]

<span data-ttu-id="052a4-122">[!code-csharp[Main](file-uploads/sample/FileUploadSample/Controllers/UploadFilesController.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="052a4-122">[!code-csharp[Main](file-uploads/sample/FileUploadSample/Controllers/UploadFilesController.cs?name=snippet1)]</span></span>

<span data-ttu-id="052a4-123">使用上载的文件`IFormFile`技术进行处理之前缓冲在内存中或在 web 服务器上的磁盘上。</span><span class="sxs-lookup"><span data-stu-id="052a4-123">Files uploaded using the `IFormFile` technique are buffered in memory or on disk on the web server before being processed.</span></span> <span data-ttu-id="052a4-124">在操作方法中，`IFormFile`内容进行访问以流的形式。</span><span class="sxs-lookup"><span data-stu-id="052a4-124">Inside the action method, the `IFormFile` contents are accessible as a stream.</span></span> <span data-ttu-id="052a4-125">除了本地文件系统中，文件可以流式传输到[Azure Blob 存储](https://azure.microsoft.com/documentation/articles/vs-storage-aspnet5-getting-started-blobs/)或[实体框架](https://docs.microsoft.com/ef/core/index)。</span><span class="sxs-lookup"><span data-stu-id="052a4-125">In addition to the local file system, files can be streamed to [Azure Blob storage](https://azure.microsoft.com/documentation/articles/vs-storage-aspnet5-getting-started-blobs/) or [Entity Framework](https://docs.microsoft.com/ef/core/index).</span></span>

<span data-ttu-id="052a4-126">若要在使用实体框架的数据库中存储二进制文件数据，定义类型的属性`byte[]`实体上：</span><span class="sxs-lookup"><span data-stu-id="052a4-126">To store binary file data in a database using Entity Framework, define a property of type `byte[]` on the entity:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public byte[] AvatarImage { get; set; }
}
```

<span data-ttu-id="052a4-127">指定类型的视图模型属性`IFormFile`:</span><span class="sxs-lookup"><span data-stu-id="052a4-127">Specify a viewmodel property of type `IFormFile`:</span></span>

```csharp
public class RegisterViewModel
{
    // other properties omitted

    public IFormFile AvatarImage { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="052a4-128">`IFormFile`可以在直接作为操作方法参数或作为视图模型属性，如上所示。</span><span class="sxs-lookup"><span data-stu-id="052a4-128">`IFormFile` can be used directly as an action method parameter or as a viewmodel property, as shown above.</span></span>

<span data-ttu-id="052a4-129">复制`IFormFile`流并保存到的字节数组：</span><span class="sxs-lookup"><span data-stu-id="052a4-129">Copy the `IFormFile` to a stream and save it to the byte array:</span></span>

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
> <span data-ttu-id="052a4-130">要格外小心时将二进制数据存储在关系数据库，因为它可能会反过来影响性能。</span><span class="sxs-lookup"><span data-stu-id="052a4-130">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>

## <a name="uploading-large-files-with-streaming"></a><span data-ttu-id="052a4-131">上载大型文件流式处理</span><span class="sxs-lookup"><span data-stu-id="052a4-131">Uploading large files with streaming</span></span>

<span data-ttu-id="052a4-132">如果大小或文件上载频率导致应用程序的资源问题，请考虑流式处理文件上载，而不是在其整个中进行缓冲，如上所示的模型绑定方法一样。</span><span class="sxs-lookup"><span data-stu-id="052a4-132">If the size or frequency of file uploads is causing resource problems for the app, consider streaming the file upload rather than buffering it in its entirety, as the model binding approach shown above does.</span></span> <span data-ttu-id="052a4-133">在使用`IFormFile`和模型绑定正在更简单的解决方案，流式处理需要大量的步骤，若要正确实现。</span><span class="sxs-lookup"><span data-stu-id="052a4-133">While using `IFormFile` and model binding is a much simpler solution, streaming requires a number of steps to implement properly.</span></span>

> [!NOTE]
> <span data-ttu-id="052a4-134">任何超过 64 KB 的单个缓冲的文件将从移动 RAM 到磁盘上的临时文件服务器上。</span><span class="sxs-lookup"><span data-stu-id="052a4-134">Any single buffered file exceeding 64KB will be moved from RAM to a temp file on disk on the server.</span></span> <span data-ttu-id="052a4-135">使用文件上载的资源 （磁盘，RAM） 依赖于的数量和大小的并发文件上载。</span><span class="sxs-lookup"><span data-stu-id="052a4-135">The resources (disk, RAM) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="052a4-136">流式处理不是如此之多关于性能，它是有关伸缩。</span><span class="sxs-lookup"><span data-stu-id="052a4-136">Streaming is not so much about perf, it's about scale.</span></span> <span data-ttu-id="052a4-137">如果你尝试在缓冲区过多上载，你的站点时将崩溃时内存或磁盘空间不足。</span><span class="sxs-lookup"><span data-stu-id="052a4-137">If you try to buffer too many uploads, your site will crash when it runs out of memory or disk space.</span></span>

<span data-ttu-id="052a4-138">下面的示例演示如何使用 JavaScript/角流式处理到的控制器操作。</span><span class="sxs-lookup"><span data-stu-id="052a4-138">The following example demonstrates using JavaScript/Angular to stream to a controller action.</span></span> <span data-ttu-id="052a4-139">使用自定义的筛选器属性，而不是在请求正文中的 HTTP 标头中传递，将生成文件的 antiforgery 令牌。</span><span class="sxs-lookup"><span data-stu-id="052a4-139">The file's antiforgery token is generated using a custom filter attribute and passed in HTTP headers instead of in the request body.</span></span> <span data-ttu-id="052a4-140">由于操作方法直接处理上载的数据，模型绑定被禁用的另一个筛选器。</span><span class="sxs-lookup"><span data-stu-id="052a4-140">Because the action method processes the uploaded data directly, model binding is disabled by another filter.</span></span> <span data-ttu-id="052a4-141">在操作内，使用读取窗体的内容`MultipartReader`，读取每个单独`MultipartSection`、 处理文件，或根据需要存储内容。</span><span class="sxs-lookup"><span data-stu-id="052a4-141">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="052a4-142">已读取所有部分，该操作执行其自己的模型绑定。</span><span class="sxs-lookup"><span data-stu-id="052a4-142">Once all sections have been read, the action performs its own model binding.</span></span>

<span data-ttu-id="052a4-143">初始操作加载窗体并将 antiforgery 令牌保存在 cookie 中 (通过`GenerateAntiforgeryTokenCookieForAjax`属性):</span><span class="sxs-lookup"><span data-stu-id="052a4-143">The initial action loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieForAjax` attribute):</span></span>

```csharp
[HttpGet]
[GenerateAntiforgeryTokenCookieForAjax]
public IActionResult Index()
{
    return View();
}
```

<span data-ttu-id="052a4-144">该属性将使用 ASP.NET Core 内置[Antiforgery](xref:security/anti-request-forgery)设置具有请求令牌的 cookie 的支持：</span><span class="sxs-lookup"><span data-stu-id="052a4-144">The attribute uses ASP.NET Core's built-in [Antiforgery](xref:security/anti-request-forgery) support to set a cookie with a request token:</span></span>

<span data-ttu-id="052a4-145">[!code-csharp[Main](file-uploads/sample/FileUploadSample/Filters/GenerateAntiforgeryTokenCookieForAjaxAttribute.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="052a4-145">[!code-csharp[Main](file-uploads/sample/FileUploadSample/Filters/GenerateAntiforgeryTokenCookieForAjaxAttribute.cs?name=snippet1)]</span></span>

<span data-ttu-id="052a4-146">角自动名为请求标头中传递 antiforgery 令牌`X-XSRF-TOKEN`。</span><span class="sxs-lookup"><span data-stu-id="052a4-146">Angular automatically passes an antiforgery token in a request header named `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="052a4-147">ASP.NET 核心 MVC 应用程序配置为在其配置中在此标头是指*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="052a4-147">The ASP.NET Core MVC app is configured to refer to this header in its configuration in *Startup.cs*:</span></span>

<span data-ttu-id="052a4-148">[!code-csharp[Main](file-uploads/sample/FileUploadSample/Startup.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="052a4-148">[!code-csharp[Main](file-uploads/sample/FileUploadSample/Startup.cs?name=snippet1)]</span></span>

<span data-ttu-id="052a4-149">`DisableFormValueModelBinding`属性，如下所示，用来禁用模型绑定`Upload`操作方法。</span><span class="sxs-lookup"><span data-stu-id="052a4-149">The `DisableFormValueModelBinding` attribute, shown below, is used to disable model binding for the `Upload` action method.</span></span>

<span data-ttu-id="052a4-150">[!code-csharp[Main](file-uploads/sample/FileUploadSample/Filters/DisableFormValueModelBindingAttribute.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="052a4-150">[!code-csharp[Main](file-uploads/sample/FileUploadSample/Filters/DisableFormValueModelBindingAttribute.cs?name=snippet1)]</span></span>

<span data-ttu-id="052a4-151">因为禁用了模型绑定，`Upload`操作方法不接受参数。</span><span class="sxs-lookup"><span data-stu-id="052a4-151">Since model binding is disabled, the `Upload` action method doesn't accept parameters.</span></span> <span data-ttu-id="052a4-152">它可直接与`Request`属性`ControllerBase`。</span><span class="sxs-lookup"><span data-stu-id="052a4-152">It works directly with the `Request` property of `ControllerBase`.</span></span> <span data-ttu-id="052a4-153">A`MultipartReader`用于读取每个部分。</span><span class="sxs-lookup"><span data-stu-id="052a4-153">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="052a4-154">该文件将保存为 GUID 文件名和键/值数据存储在`KeyValueAccumulator`。</span><span class="sxs-lookup"><span data-stu-id="052a4-154">The file is saved with a GUID filename and the key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="052a4-155">已读取所有部分的内容后`KeyValueAccumulator`用于将窗体数据绑定到的模型类型。</span><span class="sxs-lookup"><span data-stu-id="052a4-155">Once all sections have been read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="052a4-156">完整`Upload`方法如下所示：</span><span class="sxs-lookup"><span data-stu-id="052a4-156">The complete `Upload` method is shown below:</span></span>

[!INCLUDE [GetTempFileName](../../includes/GetTempFileName.md)]

<span data-ttu-id="052a4-157">[!code-csharp[Main](file-uploads/sample/FileUploadSample/Controllers/StreamingController.cs?name=snippet1)]</span><span class="sxs-lookup"><span data-stu-id="052a4-157">[!code-csharp[Main](file-uploads/sample/FileUploadSample/Controllers/StreamingController.cs?name=snippet1)]</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="052a4-158">疑难解答</span><span class="sxs-lookup"><span data-stu-id="052a4-158">Troubleshooting</span></span>

<span data-ttu-id="052a4-159">下面是一些使用上载文件和其可能的解决方案时遇到的常见问题。</span><span class="sxs-lookup"><span data-stu-id="052a4-159">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="unexpected-not-found-error-with-iis"></a><span data-ttu-id="052a4-160">使用 IIS 的意外找不到错误</span><span class="sxs-lookup"><span data-stu-id="052a4-160">Unexpected Not Found error with IIS</span></span>

<span data-ttu-id="052a4-161">以下的错误指示您的文件上载超过服务器的配置`maxAllowedContentLength`:</span><span class="sxs-lookup"><span data-stu-id="052a4-161">The following error indicates your file upload exceeds the server's configured `maxAllowedContentLength`:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="052a4-162">默认设置是`30000000`，即大约 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="052a4-162">The default setting is `30000000`, which is approximately 28.6MB.</span></span> <span data-ttu-id="052a4-163">可以通过编辑自定义值*web.config*:</span><span class="sxs-lookup"><span data-stu-id="052a4-163">The value can be customized by editing *web.config*:</span></span>

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

<span data-ttu-id="052a4-164">此设置仅适用于 IIS 中。</span><span class="sxs-lookup"><span data-stu-id="052a4-164">This setting only applies to IIS.</span></span> <span data-ttu-id="052a4-165">Kestrel 上承载时，默认情况下不会出现行为。</span><span class="sxs-lookup"><span data-stu-id="052a4-165">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="052a4-166">有关详细信息，请参阅[请求限制\<requestLimits\>](https://docs.microsoft.com/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)。</span><span class="sxs-lookup"><span data-stu-id="052a4-166">For more information, see [Request Limits \<requestLimits\>](https://docs.microsoft.com/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="052a4-167">与 IFormFile null 引用异常</span><span class="sxs-lookup"><span data-stu-id="052a4-167">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="052a4-168">如果你的控制器是接受上载的文件使用`IFormFile`但您查找的值始终为 null，请确认你的 HTML 窗体指定`enctype`值`multipart/form-data`。</span><span class="sxs-lookup"><span data-stu-id="052a4-168">If your controller is accepting uploaded files using `IFormFile` but you find that the value is always null, confirm that your HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="052a4-169">如果不设置此属性`<form>`元素，不会出现文件上载和任何绑定`IFormFile`自变量将为 null。</span><span class="sxs-lookup"><span data-stu-id="052a4-169">If this attribute is not set on the `<form>` element, the file upload will not occur and any bound `IFormFile` arguments will be null.</span></span>
