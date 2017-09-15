## <a name="register-the-database-context"></a><span data-ttu-id="8c0b6-101">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="8c0b6-101">Register the database context</span></span>

<span data-ttu-id="8c0b6-102">若要将数据库上下文注入到控制器中，需要将它注册到[依赖关系注入](xref:fundamentals/dependency-injection)容器。</span><span class="sxs-lookup"><span data-stu-id="8c0b6-102">In order to inject the database context into the controller, we need to register it with the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="8c0b6-103">使用[依赖关系注入](xref:fundamentals/dependency-injection)的内置支持将数据库上下文注册到服务容器。</span><span class="sxs-lookup"><span data-stu-id="8c0b6-103">Register the database context with the service container using the built-in support for [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="8c0b6-104">使用以下内容替换 Startup.cs 文件的内容：</span><span class="sxs-lookup"><span data-stu-id="8c0b6-104">Replace the contents of the *Startup.cs* file with the following:</span></span>

<span data-ttu-id="8c0b6-105">[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Startup.cs?highlight=2,4,12)]</span><span class="sxs-lookup"><span data-stu-id="8c0b6-105">[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Startup.cs?highlight=2,4,12)]</span></span>

<span data-ttu-id="8c0b6-106">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="8c0b6-106">The preceding code:</span></span>

* <span data-ttu-id="8c0b6-107">删除未使用的代码。</span><span class="sxs-lookup"><span data-stu-id="8c0b6-107">Removes the code we're not using.</span></span>
* <span data-ttu-id="8c0b6-108">指定将内存数据库注入到服务容器中。</span><span class="sxs-lookup"><span data-stu-id="8c0b6-108">Specifies an in-memory database is injected into the service container.</span></span>
