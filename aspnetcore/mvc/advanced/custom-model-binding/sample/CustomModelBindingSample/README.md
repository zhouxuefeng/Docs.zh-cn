# <a name="custom-model-binding-demo"></a>自定义模型绑定演示

你可以测试`ByteArrayModelBinder`通过运行应用程序并发布 ImageController 终结点的 base64 编码字符串 (/ api/图像 /)。 你应在请求正文作为窗体数据中指定文件和文件名 proparties （使用 Postman 或类似的工具）。 你可以使用[此示例字符串](Base64String.txt)。 结果将保存在具有所指定的文件名的 wwwroot/图像/上载文件夹中。

若要测试自定义绑定示例，请尝试以下终结点： /api/authors/1 /api/authors/2 （未找到） /api/boundauthors/1 /api/boundauthors/2 （未找到） 此操作不会检查 /api/boundauthors/get/1 /api/boundauthors/get/2 （无内容） 的null，并且返回未找到
