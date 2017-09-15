# <a name="adding-a-model-to-an-aspnet-core-mvc-app"></a>将模型添加到 ASP.NET Core MVC 应用

作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Tom Dykstra](https://github.com/tdykstra)

在本部分中，将添加用于管理数据库中电影的一些类。 这些类将是 MVC 应用的“Model”部分。

可以结合 [Entity Framework Core](https://docs.microsoft.com/ef/core) (EF Core) 使用这些类来处理数据库。 EF Core 是对象关系映射 (ORM) 框架，可以简化需要编写的数据访问代码。 [EF Core 支持许多数据库引擎](https://docs.microsoft.com/ef/core/providers/)。

要创建的模型类称为 POCO 类（源自“普通旧 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。 它们只定义将存储在数据库中的数据的属性。

在本教程中，首先将编写模型类，然后 EF Core 将创建数据库。 有一种备选方法（此处未介绍）是从已存在的数据库生成模型类。 有关此方法的信息，请参阅 [ASP.NET Core - Existing Database](https://docs.microsoft.com/ef/core/get-started/aspnetcore/existing-db)（ASP.NET Core - 现有数据库）。

## <a name="add-a-data-model-class"></a>添加数据模型类
