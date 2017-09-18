---
title: "ASP.NET Core 中的配置"
author: rick-anderson
description: "了解如何使用配置 API 将来自多个源 ASP.NET Core 应用配置。"
keywords: "ASP.NET 核心，配置中，JSON 配置"
ms.author: riande
manager: wpickett
ms.date: 6/24/2017
ms.topic: article
ms.assetid: b3a5984d-e172-42eb-8a48-547e4acb6806
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/configuration
ms.openlocfilehash: 7d591259587766a932a14bb030c76274101d16ac
ms.sourcegitcommit: f8f6b5934bd071a349f5bc1e389365c52b1c00fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2017
---
# <a name="configuration-in-aspnet-core"></a>ASP.NET Core 中的配置

[Rick Anderson](https://twitter.com/RickAndMSFT)，[标记 Michaelis](http://intellitect.com/author/mark-michaelis/)， [Steve Smith](https://ardalis.com/)，和[Daniel Roth](https://github.com/danroth27)

配置 API 提供一种方法配置应用程序中基于名称-值对的列表。 在运行时从多个源读取配置。 名称-值对可以分组到多级的层次结构。 有配置提供程序：

* 文件格式 （INI、 JSON 和 XML）
* 命令行自变量
* 环境变量
* 内存中的.NET 对象
* 一个加密的用户存储区
* [Azure 密钥保管库](xref:security/key-vault-configuration)
* 自定义提供程序，你安装或在创建

每个配置值将映射到字符串键。 若要反序列化到自定义设置的内置绑定支持[POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object)对象 （具有属性的简单.NET 类）。

[查看或下载示例代码](https://github.com/aspnet/docs/tree/master/aspnetcore/fundamentals/configuration/sample)

## <a name="simple-configuration"></a>简单的配置

以下控制台应用程序使用 JSON 配置提供程序：

[!code-csharp[Main](configuration/sample/ConfigJson/Program.cs)]

应用程序读取，并显示以下配置设置：

[!code-json[Main](configuration/sample/ConfigJson/appsettings.json)]

配置由在其中节点以冒号分隔的名称-值对的分层列表组成。 若要检索一个值，请访问`Configuration`与相应的项的键的索引器：

```csharp
Console.WriteLine($"option1 = {Configuration["subsection:suboption1"]}");
```

若要使用 JSON 格式配置源中的数组，使用作为一部分的冒号分隔的字符串的数组索引。 下面的示例获取在前面的第一项的名称`wizards`数组：

```csharp
Console.Write($"{Configuration["wizards:0:Name"]}, ");
```

写入到生成的名称-值对中`Configuration`提供程序是**不**保持不变，但是，你可以创建的自定义提供程序将保存值。 请参阅[自定义配置提供程序](xref:fundamentals/configuration#custom-config-providers)。

上面的示例使用配置索引器可以读取值。 外部访问配置`Startup`，使用[选项模式](xref:fundamentals/configuration#options-config-objects)。 *选项模式*本文后面所示。

它是通常具有不同的配置设置的不同的环境，例如，开发、 测试和生产。 `CreateDefaultBuilder` ASP.NET Core 2.x 应用程序中的扩展方法 (或使用`AddJsonFile`和`AddEnvironmentVariables`直接在 ASP.NET Core 1.x 应用程序) 将配置提供程序添加用于读取 JSON 文件和系统配置源：

* *appsettings.json*
* *appsettings。\<EnvironmentName >.json*
* 环境变量

请参阅[AddJsonFile](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.jsonconfigurationextensions)有关参数的说明。 `reloadOnChange`仅支持在 ASP.NET 核心 1.1 和更高版本。 

配置源中指定它们的顺序读取。 在上面的代码中，最后读取环境变量。 任何通过环境设置的配置值会替换在两个以前的提供程序中设置。

环境通常设置为其中一个`Development`， `Staging`，或`Production`。 请参阅[使用多个环境](environments.md)有关详细信息。

配置注意事项：

* `IOptionsSnapshot`当更改时，可以重新加载配置数据。 使用`IOptionsSnapshot`如果需要重新加载配置数据。  请参阅[IOptionsSnapshot](#ioptionssnapshot)有关详细信息。
* 配置密钥是区分大小写。
* 一种最佳做法是上一次，指定环境变量，使本地环境可以重写在已部署的配置文件中设置的任何内容。
* **永远不会**在配置提供程序代码或纯文本配置文件中存储密码或其他敏感数据。 请不要在你的开发中使用生产机密或测试环境。 相反，指定外部项目树中，机密，以便它们不会意外地提交到你的存储库。 详细了解[使用多个环境](environments.md)和管理[安全存储在开发过程中的应用程序机密](../security/app-secrets.md)。
* 如果`:`不能为系统中所用环境变量中，替换`:`与`__`（双下划线）。

<a name=options-config-objects></a>

## <a name="using-options-and-configuration-objects"></a>使用选项和配置对象

选项模式使用自定义选项类来表示一组相关的设置。 我们建议在你的应用程序中创建分离的类为每个功能。 分离的类遵循：

* [接口分隔原则 (ISP)](http://deviq.com/interface-segregation-principle/) ： 类仅取决于它们使用的配置设置。
* [关注点分离](http://deviq.com/separation-of-concerns/)： 你的应用程序的不同部分的设置不是依赖或与另一个耦合。

选项类必须为非抽象具有一个公共的无参数构造函数。 例如: 

[!code-csharp[Main](configuration/sample/UsingOptions/Models/MyOptions.cs)]

<a name=options-example></a>

在下面的代码中，启用 JSON 配置提供程序。 `MyOptions`类是已添加到服务容器并绑定到配置。

[!code-csharp[Main](configuration/sample/UsingOptions/Startup.cs?name=snippet1&highlight=8,20-21)]

以下[控制器](../mvc/controllers/index.md)使用[构造函数依赖关系注入](xref:fundamentals/dependency-injection#what-is-dependency-injection)上[ `IOptions<TOptions>` ](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.options.ioptions-1)访问设置：

[!code-csharp[Main](configuration/sample/UsingOptions/Controllers/HomeController.cs?name=snippet1)]

替换为以下*appsettings.json*文件：

[!code-json[Main](configuration/sample/UsingOptions/appsettings1.json)]

`HomeController.Index`方法返回`option1 = value1_from_json, option2 = 2`。

典型的应用程序不会将整个配置绑定到单个选项文件。 稍后我将介绍如何使用`GetSection`将绑定到一个部分。

在下面的代码中，第二个`IConfigureOptions<TOptions>`服务添加到服务容器。 它使用委托来配置的绑定`MyOptions`。

[!code-csharp[Main](configuration/sample/UsingOptions/Startup2.cs?name=snippet1&highlight=9-13)]

你可以添加多个配置提供程序。 配置提供程序 NuGet 包中。 它们会按它们已注册的顺序应用。

每次调用`Configure<TOptions>`添加`IConfigureOptions<TOptions>`服务到服务容器。 在前面的示例中，值`Option1`和`Option2`同时中指定*appsettings.json* -但的值`Option1`配置委托来重写。 

启用多个配置服务后，最后一个配置源指定"wins"（设置配置值）。 在前面的代码中，`HomeController.Index`方法返回`option1 = value1_from_action, option2 = 2`。

绑定到配置的选项时, 选项类型中的每个属性绑定到窗体的配置键`property[:sub-property:]`。 例如，`MyOptions.Option1`属性绑定到键`Option1`，这从读取`option1`中的属性*appsettings.json*。 在本文后面显示了子属性示例。

在下面的代码中，第三个`IConfigureOptions<TOptions>`服务添加到服务容器。 它将绑定`MySubOptions`的部分`subsection`的*appsettings.json*文件：

[!code-csharp[Main](configuration/sample/UsingOptions/Startup3.cs?name=snippet1&highlight=16-17)]

注意： 此扩展方法要求`Microsoft.Extensions.Options.ConfigurationExtensions`NuGet 包。

使用以下*appsettings.json*文件：

[!code-json[Main](configuration/sample/UsingOptions/appsettings.json)]

`MySubOptions`类：

[!code-csharp[Main](configuration/sample/UsingOptions/Models/MySubOptions.cs?name=snippet1)]

替换为以下`Controller`:

[!code-csharp[Main](configuration/sample/UsingOptions/Controllers/HomeController2.cs?name=snippet1)]

`subOption1 = subvalue1_from_json, subOption2 = 200`返回。

也可以提供视图模型中的选项，或将注入`IOptions<TOptions>`直接到视图：

[!code-html[Main](configuration/sample/UsingOptions/Views/Home/Index.cshtml?highlight=3-4,16-17,20-21)]

<a name=in-memory-provider></a>

## <a name="ioptionssnapshot"></a>IOptionsSnapshot

*需要 ASP.NET Core 1.1 或更高版本。*

`IOptionsSnapshot`当配置文件发生更改时重新加载配置数据的支持。 它具有最小的开销。 使用`IOptionsSnapshot`与`reloadOnChange: true`，选项绑定到`IConfiguration`并更改时重新加载。

下面的示例演示如何新`IOptionsSnapshot`后创建*config.json*更改。 对服务器请求将返回相同时间*config.json*具有**不**更改。 之后的第一个请求*config.json*做的更改将显示新的时间。

[!code-csharp[Main](configuration/sample/IOptionsSnapshot2/Startup.cs?name=snippet1&highlight=1-9,13-18,32,33,52,53)]

下图显示服务器输出：

![浏览器图像显示"上次更新时间： 2016 年 11 月 22 日下午 4:43"](configuration/_static/first.png)

刷新浏览器不会更改消息值或显示的时间 (时*config.json*仍是如此)。

进行更改并保存*config.json*然后刷新浏览器：

![浏览器图像显示"上次更新到 e: 2016 年 11 月 22 日下午 4:53"](configuration/_static/change.png)

## <a name="in-memory-provider-and-binding-to-a-poco-class"></a>内存中提供程序并绑定到 POCO 类

下面的示例演示如何使用内存中提供程序并将绑定到类：

[!code-csharp[Main](configuration/sample/InMemory/Program.cs)]

配置值将返回为字符串，但绑定可使对象的构造。 绑定允许您检索 POCO 对象或甚至是整个对象图。 下面的示例演示如何将绑定到`MyWindow`，并且与 ASP.NET 核心 MVC 应用程序使用选项模式：

[!code-csharp[Main](configuration/sample/WebConfigBind/MyWindow.cs)]

[!code-json[Main](configuration/sample/WebConfigBind/appsettings.json)]

绑定中的自定义类`ConfigureServices`生成主机时：

[!code-csharp[Main](configuration/sample/WebConfigBind/Program.cs?name=snippet1&highlight=3-4)]

显示从设置`HomeController`:

[!code-csharp[Main](configuration/sample/WebConfigBind/Controllers/HomeController.cs)]

### <a name="getvalue"></a>GetValue

下面的示例演示如何[GetValue<T> ](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationbinder#Microsoft_Extensions_Configuration_ConfigurationBinder_GetValue_Microsoft_Extensions_Configuration_IConfiguration_System_Type_System_String_System_Object_)扩展方法：

[!code-csharp[Main](configuration/sample/InMemoryGetValue/Program.cs?highlight=27-29)]

ConfigurationBinder`GetValue<T>`方法允许你指定默认值 (在此示例中为 80)。 `GetValue<T>`用于简单的方案，不会绑定到整个部分。 `GetValue<T>`获取标量值从`GetSection(key).Value`转换为特定类型。

## <a name="binding-to-an-object-graph"></a>绑定到对象图

你可以以递归方式绑定到类中每个对象。 请考虑以下`AppOptions`类：

[!code-csharp[Main](configuration/sample/ObjectGraph/AppOptions.cs)]

下面的示例将绑定到`AppOptions`类：

[!code-csharp[Main](configuration/sample/ObjectGraph/Program.cs?highlight=15-16)]

**ASP.NET 核心 1.1**和更高版本可以使用`Get<T>`，这适用于整个部分。 `Get<T>`可以是比使用多个 convienent `Bind`。 下面的代码演示如何使用`Get<T>`与上面的示例：

```csharp
var appConfig = config.GetSection("App").Get<AppOptions>();
```

使用以下*appsettings.json*文件：

[!code-json[Main](configuration/sample/ObjectGraph/appsettings.json)]

该程序显示`Height 11`。

下面的代码可以用于单元测试配置：

```csharp
[Fact]
public void CanBindObjectTree()
{
    var dict = new Dictionary<string, string>
        {
            {"App:Profile:Machine", "Rick"},
            {"App:Connection:Value", "connectionstring"},
            {"App:Window:Height", "11"},
            {"App:Window:Width", "11"}
        };
    var builder = new ConfigurationBuilder();
    builder.AddInMemoryCollection(dict);
    var config = builder.Build();

    var options = new AppOptions();
    config.GetSection("App").Bind(options);

    Assert.Equal("Rick", options.Profile.Machine);
    Assert.Equal(11, options.Window.Height);
    Assert.Equal(11, options.Window.Width);
    Assert.Equal("connectionstring", options.Connection.Value);
}
```

<a name=custom-config-providers></a>

## <a name="basic-sample-of-entity-framework-custom-provider"></a>实体框架自定义提供程序的基本示例

在本部分中，创建的基本配置提供程序从使用 EF 数据库中读取名称-值对。 

定义`ConfigurationValue`在数据库中存储配置值的实体：

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/ConfigurationValue.cs)]

添加`ConfigurationContext`存储和访问配置的值：

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/ConfigurationContext.cs?name=snippet1)]

创建一个类，实现[IConfigurationSource](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.iconfigurationsource):

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/EntityFrameworkConfigurationSource.cs?highlight=7)]

创建自定义配置提供程序通过继承[ConfigurationProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationprovider)。  为空时，配置提供程序初始化数据库：

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/EntityFrameworkConfigurationProvider.cs?highlight=9,18-31,38-39)]

运行示例时，会显示数据库 （"value_from_ef_1"和"value_from_ef_2"） 中突出显示的值。

你可以添加`EFConfigSource`将该配置源添加的扩展方法：

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/EntityFrameworkExtensions.cs?highlight=12)]

下面的代码演示如何使用自定义`EFConfigProvider`:

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/Program.cs?highlight=21-26)]

请注意此示例将添加自定义`EFConfigProvider`后 JSON 提供程序，因此数据库中的任何设置将替代设置从*appsettings.json*文件。

使用以下*appsettings.json*文件：

[!code-json[Main](configuration/sample/CustomConfigurationProvider/appsettings.json)]

将显示以下内容：

```console
key1=value_from_ef_1
key2=value_from_ef_2
key3=value_from_json_3
```

## <a name="commandline-configuration-provider"></a>命令行配置提供程序

下面的示例使最后一个命令行配置提供程序：

[!code-csharp[Main](configuration/sample/CommandLine/Program.cs)]

使用以下方法来配置设置中传递：

```console
dotnet run /Profile:MachineName=Bob /App:MainWindow:Left=1234
```

这将显示：

```console
Hello Bob
Left 1234
```

`GetSwitchMappings`方法允许你使用`-`而非`/`和它中抽出前导子项前缀。 例如: 

```console
dotnet run -MachineName=Bob -Left=7734
```

显示：

```console
Hello Bob
Left 7734
```

命令行自变量必须包含 （它可以为 null） 的值。 例如: 

```console
dotnet run /Profile:MachineName=
```

是可以的但

```console
dotnet run /Profile:MachineName
```

引发异常的结果。 如果你指定为其中没有相应的交换机映射的-或--命令行开关前缀，则将引发异常。

## <a name="the-webconfig-file"></a>Web.config 文件

A *web.config*文件是必需的当你在 IIS 或 IIS Express 应用程序承载。 *web.config*开启 AspNetCoreModule IIS 启动你的应用中。 中的设置*web.config*启用 IIS 来启动你的应用和配置其他 IIS 设置和模块中 AspNetCoreModule。 如果你使用的 Visual Studio 将删除*web.config*，Visual Studio 将创建一个新。

### <a name="additional-notes"></a>附加说明

* 依赖关系注入 (DI) 未设置截止日期之后`ConfigureServices`调用。
* 配置系统不是 DI 感知。
* `IConfiguration`具有两个专用化：
  * `IConfigurationRoot`用于根节点。 可以触发重新加载。
  * `IConfigurationSection`表示一个节的配置值。 `GetSection`和`GetChildren`方法返回`IConfigurationSection`。

### <a name="additional-resources"></a>其他资源

* [使用多个环境](environments.md)
* [在开发期间安全存储应用密钥](../security/app-secrets.md)
* [依赖关系注入](dependency-injection.md)
* [Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)
