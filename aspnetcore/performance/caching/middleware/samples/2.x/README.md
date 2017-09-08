# <a name="aspnet-core-response-caching-sample-aspnet-core-2x"></a>ASP.NET 核心响应缓存示例 （ASP.NET 核心 2.x）

本示例演示使用 ASP.NET Core[响应缓存中间件](xref:performance/caching/middleware)与 ASP.NET 核心 2.x。 有关 ASP.NET 核心 1.x 示例，请参阅[ASP.NET 核心响应缓存示例 (ASP.NET Core 1.x)](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/middleware/samples/1.x)。

应用程序发送`Hello World!`消息和当前时间以及`Cache-Control`标头来配置缓存行为。 应用程序还将发送`Vary`标头来配置缓存，以提供响应才`Accept-Encoding`的后续请求的标头中的匹配的原始请求。

运行示例时，响应是从缓存提供时可能和存储最多 10 秒钟。
