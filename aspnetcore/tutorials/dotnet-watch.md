---
title: "使用 dotnet watch 开发 ASP.NET Core 应用"
author: rick-anderson
description: "演示如何使用 dotnet watch。"
keywords: "ASP.NET Core, 使用 dotnet watch"
ms.author: riande
manager: wpickett
ms.date: 03/09/2017
ms.topic: article
ms.assetid: 563ffb3f-d369-4aa5-bf0a-7300b4e7832c
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/dotnet-watch
ms.openlocfilehash: 6a8943619e6174dbcd3d901b7bb736ba5d3af95d
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2017
---
# <a name="developing-aspnet-core-apps-using-dotnet-watch"></a>使用 dotnet watch 开发 ASP.NET Core 应用


作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Victor Hurdugaci](https://twitter.com/victorhurdugaci)

`dotnet watch` 是一种在源文件更改时运行 `dotnet` 命令的工具。 例如，文件更改可能触发编译、测试或部署。

本教程中使用现有 Web API 应用，它具有两个终结点：分别返回总计和产品。 产品方法中有一个 bug，我们将在本教程中修复它。

下载[示例应用](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample)。 它包含两个项目：`WebApp`（Web 应用）和 `WebAppTests`（Web 应用的单元测试）。

在控制台中，导航到“WebApp”文件夹并运行以下命令：

- `dotnet restore`
- `dotnet run`

控制台输出将显示类似如下的消息（表示应用正在运行且正在等待请求）：

```console
$ dotnet run
Hosting environment: Production
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

在 Web 浏览器中，导航到 `http://localhost:5000/api/math/sum?a=4&b=5`，应该会看到结果 `9`。

导航到产品 API (`http://localhost:5000/api/math/product?a=4&b=5`)，它将如你所料返回 `9` 而非 `20`。 本教程稍后将对其进行修复。

## <a name="add-dotnet-watch-to-a-project"></a>将 `dotnet watch` 添加到项目

- 将 `Microsoft.DotNet.Watcher.Tools` 添加到“.csproj”文件：
 ```xml
 <ItemGroup>
   <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
 </ItemGroup> 
 ```

- 运行 `dotnet restore`。

## <a name="running-dotnet-commands-using-dotnet-watch"></a>使用 `dotnet watch` 运行 `dotnet` 命令

可使用 `dotnet watch` 运行任何 `dotnet` 命令，例如：

| 命令 | 带 watch 的命令 |
| ---- | ----- |
| dotnet 运行 | dotnet watch run |
| dotnet run -f net451 | dotnet watch run -f net451 |
| dotnet run -f net451 -- --arg1 | dotnet watch run -f net451 -- --arg1 |
| dotnet test | dotnet watch test |

在 `WebApp` 文件夹中运行 `dotnet watch run`。 控制台输出将指示 `watch` 已启动。

## <a name="making-changes-with-dotnet-watch"></a>通过 `dotnet watch` 进行更改

确保 `dotnet watch` 正在运行。

在 `MathController` 的 `Product` 方法中修复该 bug，使其返回产品而非总和。

```csharp
public static int Product(int a, int b)
{
  return a * b;
} 
```

保存该文件。 控制台输出将显示消息，表示 `dotnet watch` 已检测到文件更改并已重启应用。

验证 `http://localhost:5000/api/math/product?a=4&b=5` 是否返回正确结果。

## <a name="running-tests-using-dotnet-watch"></a>使用 `dotnet watch` 运行测试

- 将 `MathController` 的 `Product` 方法改回返回总和并保存文件。
- 在命令窗口中，导航到 `WebAppTests` 文件夹。
- 运行 `dotnet restore`
- 运行 `dotnet watch test`。 将看到输出，它表示测试失败且观察程序正在等待文件更改：

 ```console
 Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
 Test Run Failed.
  ```
- 修复 `Product` 方法代码，使其返回产品。 保存该文件。

`dotnet watch` 检测到文件更改并重新运行测试。 控制台输出将显示通过的测试。

## <a name="dotnet-watch-in-github"></a>GitHub 中的 dotnet-watch

dotnet-watch 是 GitHub [DotNetTools 存储库](https://github.com/aspnet/DotNetTools/tree/dev/src/Microsoft.DotNet.Watcher.Tools)的一部分。

[dotnet-watch 自述文件](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md)的 [MSBuild 部分](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md#msbuild)阐释了如何通过被监视的 MSBuild 项目文件配置 dotnet-watch。 [dotnet-watch 自述文件](https://github.com/aspnet/DotNetTools/blob/dev/src/Microsoft.DotNet.Watcher.Tools/README.md)介绍了本教程中没有的 dotnet-watch 相关信息。
