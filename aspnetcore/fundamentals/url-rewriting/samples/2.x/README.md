# <a name="aspnet-core-url-rewriting-sample-aspnet-core-2x"></a>ASP.NET 核心 URL 重写示例 （ASP.NET 核心 2.x）

本示例演示使用 ASP.NET Core 2.x URL 重写中间件。 应用程序演示 URL 重定向和 URL 重写选项。 有关 ASP.NET 核心 1.x 示例，请参阅[ASP.NET 核心 URL 重写示例 (ASP.NET Core 1.x)](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/1.x)。

运行示例时，响应将提供显示规则之一到请求的 URL 的应用时的重写或重定向 URL。

## <a name="examples-in-this-sample"></a>在此示例中的示例

* `AddRedirect("redirect-rule/(.*)", "$1")`
  - 成功状态代码： 302 （发现）
  - 示例 （重定向）： **/redirect-rule / {capture_group}**到**/redirected/ {capture_group}**
* `AddRewrite(@"^rewrite-rule/(\d+)/(\d+)", "rewritten?var1=$1&var2=$2", skipRemainingRules: true)`
  - 成功状态代码： 200 （正常）
  - 示例 （重写）： **/rewrite-rule / {capture_group_1} / {capture_group_2}**到**/ 重写？ var1 = {capture_group_1} & var2 = {capture_group_2}**
* `AddApacheModRewrite(env.ContentRootFileProvider, "ApacheModRewrite.txt")`
  - 成功状态代码： 302 （发现）
  - 示例 （重定向）： **/apache-mod-rules-redirect / {capture_group}**到**/ 重定向？ id = {capture_group}**
* `AddIISUrlRewrite(env.ContentRootFileProvider, "IISUrlRewrite.xml")`
  - 成功状态代码： 200 （正常）
  - 示例 （重写）： **/iis-rules-rewrite / {capture_group}**到**/ 重写？ id = {capture_group}**
* `Add(RedirectXMLRequests)`
  - 成功状态代码： 301 （永久移动）
  - 示例 （重定向）： **/file.xml**到**/xmlfiles/file.xml**
* `Add(new RedirectPNGRequests(".png", "/png-images")))`<br>`Add(new RedirectPNGRequests(".jpg", "/jpg-images")))`
  - 成功状态代码： 301 （永久移动）
  - 示例 （重定向）： **/image.png**到**/png-images/image.png**
  - 示例 （重定向）： **/image.jpg**到**/jpg-images/image.jpg**

## <a name="using-a-physicalfileprovider"></a>使用`PhysicalFileProvider`
你还可以获取`IFileProvider`通过创建`PhysicalFileProvider`将传递到`AddApacheModRewrite()`和`AddIISUrlRewrite()`方法：
```csharp
using Microsoft.Extensions.FileProviders;
PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
```
## <a name="secure-redirection-extensions"></a>安全重定向扩展
此示例包括`WebHostBuilder`配置应用程序以使用 Url (**https://localhost:5001**， **https://localhost**) 和测试证书 (**testCert.pfx**) 以帮助你浏览这些重定向方法。 添加到其中任何`RewriteOptions()`中**Startup.cs**来研究它们的行为。

方法 | 状态代码 | 端口
--- | :---: | :---:
`.AddRedirectToHttpsPermanent()` | 301 | null (465)
`.AddRedirectToHttps()` | 302 | null (465)
`.AddRedirectToHttps(301)` | 301 | null (465)
`.AddRedirectToHttps(301, 5001)` | 301 | 5001
