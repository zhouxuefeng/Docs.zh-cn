## <a name="implement-the-other-crud-operations"></a>实现其他的 CRUD 操作

我们将向控制器添加 `Create`、`Update` 和 `Delete` 方法。 这些是主题的变体，因此在这里只提供代码并突出显示主要的差异。 添加或更改代码后生成项目。

### <a name="create"></a>创建

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

这是 HTTP POST 方法，由 [`[HttpPost]`](/aspnet/core/api/microsoft.aspnetcore.mvc.httppostattribute) 特性指示的。 [`[FromBody]`](/aspnet/core/api/microsoft.aspnetcore.mvc.frombodyattribute) 特性告诉 MVC 从 HTTP 请求正文获取待办事项的值。

`CreatedAtRoute` 方法返回 201 响应，这是在服务器上创建新资源的 HTTP POST 方法的标准响应。 `CreatedAtRoute` 还会向响应添加位置标头。 位置标头指定新建的待办事项的 URI。 请参阅 [10.2.2 201 已创建](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。

### <a name="use-postman-to-send-a-create-request"></a>使用 Postman 发送创建请求

![Postman 控制台](../../tutorials/first-web-api/_static/pmc.png)

* 将 HTTP 方法设置为 `POST`
* 选择“正文”单选按钮
* 选择“原始”单选按钮
* 将类型设置为 JSON
* 在键值编辑器中，输入一个待办事项，例如 

```json
{
    "name":"walk dog",
    "isComplete":true
}
```

* 选择“发送”

* 选择下窗格中的“标头”选项卡，然后复制“位置”标头：

![Postman 控制台的“标头”选项卡](../../tutorials/first-web-api/_static/pmget.png)

可以使用此位置标头 URI 访问刚刚创建的资源。 重新调用创建名为 `"GetTodo"` 路由时使用的 `GetById` 方法：

```csharp
[HttpGet("{id}", Name = "GetTodo")]
public IActionResult GetById(long id)
```

### <a name="update"></a>更新

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

`Update` 与 `Create` 类似，但是使用的是 HTTP PUT。 响应是 [204（无内容）](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。 根据 HTTP 规范，PUT 请求需要客户端发送整个更新的实体，而不仅仅是增量。 若要支持部分更新，请使用 HTTP PATCH。

![显示 204（无内容）响应的 Postman 控制台](../../tutorials/first-web-api/_static/pmcput.png)

### <a name="delete"></a>删除

[!code-csharp[Main](../../tutorials/first-web-api/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

响应是 [204（无内容）](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。

![显示 204（无内容）响应的 Postman 控制台](../../tutorials/first-web-api/_static/pmd.png)
