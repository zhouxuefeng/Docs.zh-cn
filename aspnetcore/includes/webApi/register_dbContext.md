## <a name="register-the-database-context"></a>注册数据库上下文

若要将数据库上下文注入到控制器中，需要将它注册到[依赖关系注入](xref:fundamentals/dependency-injection)容器。 使用[依赖关系注入](xref:fundamentals/dependency-injection)的内置支持将数据库上下文注册到服务容器。 使用以下内容替换 Startup.cs 文件的内容：

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Startup.cs?highlight=2,4,12)]

前面的代码：

* 删除未使用的代码。
* 指定将内存数据库注入到服务容器中。
