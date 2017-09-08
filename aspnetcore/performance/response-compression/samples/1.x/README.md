# <a name="response-compression-sample-application-aspnet-core-1x"></a>响应压缩示例应用程序 (ASP.NET Core 1.x)

此示例演示如何使用 ASP.NET Core 1.x 响应的压缩中间件来压缩 HTTP 响应中的。 此示例演示 Gzip 和自定义压缩提供程序文本和图像的响应，并演示如何将添加压缩的 MIME 类型。 有关 ASP.NET 核心 2.x 示例，请参阅[响应压缩示例应用程序 (ASP.NET Core 2.x)](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/response-compression/samples/2.x)。

## <a name="examples-in-this-sample"></a>在此示例中的示例
* `GzipCompressionProvider`
  * `text/plain`
    * **/**-在将会压缩为 927 字节的 2,044 字节 Lorem Ipsum 文本文件响应
    * **/testfile1kb.txt** -1,033 将会压缩为 47 字节的字节的文本文件响应
    * **/ 渗透**-作为单个字符以 1 秒为间隔发出的响应 
  * `image/svg+xml`
    * **/banner.svg** -9,707 将会压缩为 4,459 字节的字节的可缩放向量图形 (SVG) 映像响应
* `CustomCompressionProvider`<br>演示如何实现自定义压缩提供程序用于该中间件

当请求中包括`Accept-Encoding`标头，该示例将添加`Vary: Accept-Encoding`到响应的标头。 `Vary`标头指示缓存来维护的响应基于替换值的多个副本`Accept-Encoding`，因此的压缩 (gzip) 和未压缩的版本存储在缓存中的系统可以接受压缩或未压缩的响应。

## <a name="using-the-sample"></a>使用示例
1. 使请求使用[Fiddler](http://www.telerik.com/fiddler)， [Firebug](http://getfirebug.com/)，或[Postman](https://www.getpostman.com/)到应用程序而不`Accept-Encoding`标头并记下响应负载中，响应大小和响应标头。
2. 添加`Accept-Encoding: gzip`标头并记下压缩的响应大小和响应标头。 请参阅 drop，响应大小和`Content-Encoding: gzip`响应标头包含的示例应用程序。 当你查看响应正文以 Lorem Ipsum 或**testfile1kb.txt**响应，你将看到该文本是压缩和不可读。
3. 添加`Accept-Encoding: mycustomcompression`标头，并记下响应标头。 `CustomCompressionProvider`是一个空的实现，不会实际压缩响应，但你可以创建的自定义压缩流包装`CreateStream()`方法。
