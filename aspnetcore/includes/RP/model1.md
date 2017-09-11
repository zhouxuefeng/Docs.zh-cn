作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

在本部分中将添加用于管理数据库中的电影的类。 可以结合使用这些类和 [Entity Framework Core](https://docs.microsoft.com/ef/core) (EF Core) 来处理数据库。 EF Core 是对象关系映射 (ORM) 框架，可以简化必须要编写的数据访问代码。

要创建的模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。 它们定义数据库中存储的数据属性。

在本教程中，首先要编写模型类，然后 EF Core 将创建数据库。 有一种备选方法（此处未介绍）：[从现有数据库生成模型类](https://docs.microsoft.com/ef/core/get-started/aspnetcore/existing-db)。

[查看或下载](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie)示例。