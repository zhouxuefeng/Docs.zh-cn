---
title: "在 ASP.NET 核心中使用 Grunt"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 471112e9-2c33-454b-96fc-32916102ce73
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/using-grunt
ms.openlocfilehash: 8ae50514ce24c7f9e3bb1e347d5d860e1de43c5f
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2017
---
# <a name="using-grunt-in-aspnet-core"></a><span data-ttu-id="ed0de-103">在 ASP.NET 核心中使用 Grunt</span><span class="sxs-lookup"><span data-stu-id="ed0de-103">Using Grunt in ASP.NET Core</span></span> 

<span data-ttu-id="ed0de-104">通过[了米](https://blog.falafel.com/falafel-software-recognized-sitefinity-website-year/)</span><span class="sxs-lookup"><span data-stu-id="ed0de-104">By [Noel Rice](https://blog.falafel.com/falafel-software-recognized-sitefinity-website-year/)</span></span>

<span data-ttu-id="ed0de-105">Grunt 是自动化脚本缩减、 TypeScript 编译、 代码质量"链接形式"工具、 CSS 预处理器和几乎任何重复繁琐的工作，需要采取措施来支持客户端开发的 JavaScript 任务运行程序。</span><span class="sxs-lookup"><span data-stu-id="ed0de-105">Grunt is a JavaScript task runner that automates script minification, TypeScript compilation, code quality "lint" tools, CSS pre-processors, and just about any repetitive chore that needs doing to support client development.</span></span> <span data-ttu-id="ed0de-106">尽管 ASP.NET 项目模板默认情况下使用 Gulp Visual Studio 中，完全支持 grunt (请参阅[使用 Gulp](using-gulp.md))。</span><span class="sxs-lookup"><span data-stu-id="ed0de-106">Grunt is fully supported in Visual Studio, though the ASP.NET project templates use Gulp by default (see [Using Gulp](using-gulp.md)).</span></span>

<span data-ttu-id="ed0de-107">此示例使用一个空的 ASP.NET Core 项目作为起始点，以显示如何从零开始的客户端生成过程中自动运行。</span><span class="sxs-lookup"><span data-stu-id="ed0de-107">This example uses an empty ASP.NET Core project as its starting point, to show how to automate the client build process from scratch.</span></span>

<span data-ttu-id="ed0de-108">完成的示例清除目标部署目录、 将合并 JavaScript 文件，检查代码质量，会将 JavaScript 文件内容和将部署到 web 应用程序的根目录。</span><span class="sxs-lookup"><span data-stu-id="ed0de-108">The finished example cleans the target deployment directory, combines JavaScript files, checks code quality, condenses JavaScript file content and deploys to the root of your web application.</span></span> <span data-ttu-id="ed0de-109">我们将使用下列包：</span><span class="sxs-lookup"><span data-stu-id="ed0de-109">We will use the following packages:</span></span>

* <span data-ttu-id="ed0de-110">**grunt**: Grunt 任务运行程序包。</span><span class="sxs-lookup"><span data-stu-id="ed0de-110">**grunt**: The Grunt task runner package.</span></span>

* <span data-ttu-id="ed0de-111">**grunt contrib 清理**： 删除文件或目录的插件。</span><span class="sxs-lookup"><span data-stu-id="ed0de-111">**grunt-contrib-clean**: A plugin that removes files or directories.</span></span>

* <span data-ttu-id="ed0de-112">**grunt contrib jshint**： 查看 JavaScript 代码质量的插件。</span><span class="sxs-lookup"><span data-stu-id="ed0de-112">**grunt-contrib-jshint**: A plugin that reviews JavaScript code quality.</span></span>

* <span data-ttu-id="ed0de-113">**grunt contrib concat**： 将文件联接成单个文件的插件。</span><span class="sxs-lookup"><span data-stu-id="ed0de-113">**grunt-contrib-concat**: A plugin that joins files into a single file.</span></span>

* <span data-ttu-id="ed0de-114">**grunt contrib uglify**: minifies JavaScript 以减少大小的插件。</span><span class="sxs-lookup"><span data-stu-id="ed0de-114">**grunt-contrib-uglify**: A plugin that minifies JavaScript to reduce size.</span></span>

* <span data-ttu-id="ed0de-115">**grunt contrib 监视**： 监视文件活动的插件。</span><span class="sxs-lookup"><span data-stu-id="ed0de-115">**grunt-contrib-watch**: A plugin that watches file activity.</span></span>

## <a name="preparing-the-application"></a><span data-ttu-id="ed0de-116">准备好应用程序</span><span class="sxs-lookup"><span data-stu-id="ed0de-116">Preparing the application</span></span>

<span data-ttu-id="ed0de-117">若要开始，设置新的空 web 应用程序，并添加 TypeScript 示例文件。</span><span class="sxs-lookup"><span data-stu-id="ed0de-117">To begin, set up a new empty web application and add TypeScript example files.</span></span> <span data-ttu-id="ed0de-118">TypeScript 文件将会自动编译到 JavaScript 使用默认 Visual Studio 设置，并且将是我们原材料处理使用 Grunt。</span><span class="sxs-lookup"><span data-stu-id="ed0de-118">TypeScript files are automatically compiled into JavaScript using default Visual Studio settings and will be our raw material to process using Grunt.</span></span>

1.  <span data-ttu-id="ed0de-119">在 Visual Studio 中，创建一个新`ASP.NET Web Application`。</span><span class="sxs-lookup"><span data-stu-id="ed0de-119">In Visual Studio, create a new `ASP.NET Web Application`.</span></span>

2.  <span data-ttu-id="ed0de-120">在**新建 ASP.NET 项目**对话框中，选择 ASP.NET Core**空**模板，然后单击确定按钮。</span><span class="sxs-lookup"><span data-stu-id="ed0de-120">In the **New ASP.NET Project** dialog, select the ASP.NET Core **Empty** template and click the OK button.</span></span>

3.  <span data-ttu-id="ed0de-121">在解决方案资源管理器，查看的项目结构。</span><span class="sxs-lookup"><span data-stu-id="ed0de-121">In the Solution Explorer, review the project structure.</span></span> <span data-ttu-id="ed0de-122">`\src`文件夹包含空`wwwroot`和`Dependencies`节点。</span><span class="sxs-lookup"><span data-stu-id="ed0de-122">The `\src` folder includes empty `wwwroot` and `Dependencies` nodes.</span></span>

    ![空 web 解决方案](using-grunt/_static/grunt-solution-explorer.png)

4.  <span data-ttu-id="ed0de-124">添加一个名为的新文件夹`TypeScript`到你的项目目录。</span><span class="sxs-lookup"><span data-stu-id="ed0de-124">Add a new folder named `TypeScript` to your project directory.</span></span>

5.  <span data-ttu-id="ed0de-125">在添加之前的任何文件，我们必须确保 Visual Studio 具有选项编译保存 TypeScript 文件检查。</span><span class="sxs-lookup"><span data-stu-id="ed0de-125">Before adding any files, let’s make sure that Visual Studio has the option 'compile on save' for TypeScript files checked.</span></span> <span data-ttu-id="ed0de-126">*工具 > 选项 > 文本编辑器 > Typescript > 项目*</span><span class="sxs-lookup"><span data-stu-id="ed0de-126">*Tools > Options > Text Editor > Typescript > Project*</span></span>

    ![设置自动 compliation TypeScript 文件的选项](using-grunt/_static/typescript-options.png)

6.  <span data-ttu-id="ed0de-128">右键单击`TypeScript`目录，然后选择**添加 > 新项**从上下文菜单。</span><span class="sxs-lookup"><span data-stu-id="ed0de-128">Right-click the `TypeScript` directory and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="ed0de-129">选择**JavaScript 文件**项并将该文件命名*Tastes.ts* (请注意\*.ts 扩展)。</span><span class="sxs-lookup"><span data-stu-id="ed0de-129">Select the **JavaScript file** item and name the file *Tastes.ts* (note the \*.ts extension).</span></span> <span data-ttu-id="ed0de-130">将以下 TypeScript 代码的行复制到文件 (在您保存，新*Tastes.js*文件将出现与 JavaScript 源)。</span><span class="sxs-lookup"><span data-stu-id="ed0de-130">Copy the line of TypeScript code below into the file (when you save, a new *Tastes.js* file will appear with the JavaScript source).</span></span>
    
    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7.  <span data-ttu-id="ed0de-131">添加的第二个文件，以便**TypeScript**目录并将其命名`Food.ts`。</span><span class="sxs-lookup"><span data-stu-id="ed0de-131">Add a second file to the **TypeScript** directory and name it `Food.ts`.</span></span> <span data-ttu-id="ed0de-132">将下面的代码复制到文件。</span><span class="sxs-lookup"><span data-stu-id="ed0de-132">Copy the code below into the file.</span></span>

    ```typescript
    class Food {
      constructor(name: string, calories: number) {
        this._name = name;
        this._calories = calories;
      }
    
      private _name: string;
      get Name() {
        return this._name;
      }
    
      private _calories: number;
      get Calories() {
        return this._calories;
      }
    
      private _taste: Tastes;
      get Taste(): Tastes { return this._taste }
      set Taste(value: Tastes) {
        this._taste = value;
      }
    }
    ```

## <a name="configuring-npm"></a><span data-ttu-id="ed0de-133">配置 NPM</span><span class="sxs-lookup"><span data-stu-id="ed0de-133">Configuring NPM</span></span>

<span data-ttu-id="ed0de-134">接下来，配置 NPM 来下载 grunt 和 grunt 任务。</span><span class="sxs-lookup"><span data-stu-id="ed0de-134">Next, configure NPM to download grunt and grunt-tasks.</span></span>

1. <span data-ttu-id="ed0de-135">在解决方案资源管理器，右键单击该项目并选择**添加 > 新项**从上下文菜单。</span><span class="sxs-lookup"><span data-stu-id="ed0de-135">In the Solution Explorer, right-click the project and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="ed0de-136">选择**NPM 配置文件**项，保留默认名称， *package.json*，然后单击**添加**按钮。</span><span class="sxs-lookup"><span data-stu-id="ed0de-136">Select the **NPM configuration file** item, leave the default name, *package.json*, and click the **Add** button.</span></span>

2. <span data-ttu-id="ed0de-137">在*package.json*文件内,`devDependencies`对象大括号中，输入"grunt"。</span><span class="sxs-lookup"><span data-stu-id="ed0de-137">In the *package.json* file, inside the `devDependencies` object braces, enter "grunt".</span></span> <span data-ttu-id="ed0de-138">选择`grunt`从智能感知列表并按 Enter 键。</span><span class="sxs-lookup"><span data-stu-id="ed0de-138">Select `grunt` from the Intellisense list and press the Enter key.</span></span> <span data-ttu-id="ed0de-139">Visual Studio 将 quote grunt 包名称，并添加一个冒号。</span><span class="sxs-lookup"><span data-stu-id="ed0de-139">Visual Studio will quote the grunt package name, and add a colon.</span></span> <span data-ttu-id="ed0de-140">从 Intellisense 列表的顶部中的冒号右侧，选择包的最新稳定版本 (按`Ctrl-Space`如果未显示 Intellisense)。</span><span class="sxs-lookup"><span data-stu-id="ed0de-140">To the right of the colon, select the latest stable version of the package from the top of the Intellisense list (press `Ctrl-Space` if Intellisense does not appear).</span></span>

    ![grun Intellisense](using-grunt/_static/devdependencies-grunt.png)
    
    > [!NOTE]
    > <span data-ttu-id="ed0de-142">使用 NPM[语义版本控制](http://semver.org/)来组织依赖关系。</span><span class="sxs-lookup"><span data-stu-id="ed0de-142">NPM uses [semantic versioning](http://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="ed0de-143">语义版本控制，也称为 SemVer，标识包的编号方案<major>。<minor>。<patch>.Intellisense 显示仅几个常用的选项，从而简化了语义版本控制。</span><span class="sxs-lookup"><span data-stu-id="ed0de-143">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme <major>.<minor>.<patch>. Intellisense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="ed0de-144">Intellisense 列表 (在上面的示例 0.4.5) 中的顶级项被视为包的最新稳定版本。</span><span class="sxs-lookup"><span data-stu-id="ed0de-144">The top item in the Intellisense list (0.4.5 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="ed0de-145">脱字号 (^) 符号匹配的最新的主版本和波形符 （~） 与最新次要版本匹配。</span><span class="sxs-lookup"><span data-stu-id="ed0de-145">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span> <span data-ttu-id="ed0de-146">请参阅[NPM semver 版本分析器参考](https://www.npmjs.com/package/semver)作为 SemVer 提供其完整表达的指南。</span><span class="sxs-lookup"><span data-stu-id="ed0de-146">See the [NPM semver version parser reference](https://www.npmjs.com/package/semver) as a guide to the full expressivity that SemVer provides.</span></span>

3. <span data-ttu-id="ed0de-147">添加更多的依赖关系，以加载 grunt-contrib-\*打包以*干净*， *jshint*， *concat*， *uglify*，和*监视*下面的示例中所示。</span><span class="sxs-lookup"><span data-stu-id="ed0de-147">Add more dependencies to load grunt-contrib-\* packages for *clean*, *jshint*, *concat*, *uglify*, and *watch* as shown in the example below.</span></span> <span data-ttu-id="ed0de-148">版本不需要与示例匹配。</span><span class="sxs-lookup"><span data-stu-id="ed0de-148">The versions do not need to match the example.</span></span>

    ```json
    "devDependencies": {
      "grunt": "0.4.5",
      "grunt-contrib-clean": "0.6.0",
      "grunt-contrib-jshint": "0.11.0",
      "grunt-contrib-concat": "0.5.1",
      "grunt-contrib-uglify": "0.8.0",
      "grunt-contrib-watch": "0.6.1"
    }
    ```

4. <span data-ttu-id="ed0de-149">保存*package.json*文件。</span><span class="sxs-lookup"><span data-stu-id="ed0de-149">Save the *package.json* file.</span></span>

<span data-ttu-id="ed0de-150">将下载的每个 devDependencies 项包，以及每个包需要的任何文件。</span><span class="sxs-lookup"><span data-stu-id="ed0de-150">The packages for each devDependencies item will download, along with any files that each package requires.</span></span> <span data-ttu-id="ed0de-151">你可以查找中的包文件`node_modules`通过启用目录**显示所有文件**在解决方案资源管理器的按钮。</span><span class="sxs-lookup"><span data-stu-id="ed0de-151">You can find the package files in the `node_modules` directory by enabling the **Show All Files** button in the Solution Explorer.</span></span>

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> <span data-ttu-id="ed0de-153">如果需要可以通过右键单击来手动还原解决方案资源管理器中的依赖关系`Dependencies\NPM`并选择**还原包**菜单选项。</span><span class="sxs-lookup"><span data-stu-id="ed0de-153">If you need to, you can manually restore dependencies in Solution Explorer by right-clicking on `Dependencies\NPM` and selecting the **Restore Packages** menu option.</span></span>

![还原包](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a><span data-ttu-id="ed0de-155">配置 Grunt</span><span class="sxs-lookup"><span data-stu-id="ed0de-155">Configuring Grunt</span></span>

<span data-ttu-id="ed0de-156">使用名为的清单配置 grunt *Gruntfile.js*的定义、 加载和注册任务可以手动运行或配置以自动基于运行 Visual Studio 中的事件。</span><span class="sxs-lookup"><span data-stu-id="ed0de-156">Grunt is configured using a manifest named *Gruntfile.js* that defines, loads and registers tasks that can be run manually or configured to run automatically based on events in Visual Studio.</span></span>

1.  <span data-ttu-id="ed0de-157">右键单击项目并选择**添加 > 新项**。</span><span class="sxs-lookup"><span data-stu-id="ed0de-157">Right-click the project and select **Add > New Item**.</span></span> <span data-ttu-id="ed0de-158">选择**Grunt 配置文件**选项，请保留默认名称， *Gruntfile.js*，然后单击**添加**按钮。</span><span class="sxs-lookup"><span data-stu-id="ed0de-158">Select the **Grunt Configuration file** option, leave the default name, *Gruntfile.js*, and click the **Add** button.</span></span>

    <span data-ttu-id="ed0de-159">初始代码包含了一个模块定义和`grunt.initConfig()`方法。</span><span class="sxs-lookup"><span data-stu-id="ed0de-159">The initial code includes a module definition and the `grunt.initConfig()` method.</span></span> <span data-ttu-id="ed0de-160">`initConfig()`用于为每个包中，设置选项和模块的剩余部分将会加载和注册任务。</span><span class="sxs-lookup"><span data-stu-id="ed0de-160">The `initConfig()` is used to set options for each package, and the remainder of the module will load and register tasks.</span></span>
    
    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
      });
    };
    ```

2. <span data-ttu-id="ed0de-161">内部`initConfig()`方法，添加用于`clean`任务示例中所示*Gruntfile.js*下面。</span><span class="sxs-lookup"><span data-stu-id="ed0de-161">Inside the `initConfig()` method, add options for the `clean` task as shown in the example *Gruntfile.js* below.</span></span> <span data-ttu-id="ed0de-162">Clean 任务，接受目录字符串的数组。</span><span class="sxs-lookup"><span data-stu-id="ed0de-162">The clean task accepts an array of directory strings.</span></span> <span data-ttu-id="ed0de-163">此任务从 wwwroot/lib 中删除文件，并将移除整个/临时目录。</span><span class="sxs-lookup"><span data-stu-id="ed0de-163">This task removes files from wwwroot/lib and removes the entire /temp directory.</span></span>

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

3. <span data-ttu-id="ed0de-164">下面 initConfig() 方法中，添加对的调用`grunt.loadNpmTasks()`。</span><span class="sxs-lookup"><span data-stu-id="ed0de-164">Below the initConfig() method, add a call to `grunt.loadNpmTasks()`.</span></span> <span data-ttu-id="ed0de-165">这将从 Visual Studio 来使该任务可运行。</span><span class="sxs-lookup"><span data-stu-id="ed0de-165">This will make the task runnable from Visual Studio.</span></span>

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

4. <span data-ttu-id="ed0de-166">保存*Gruntfile.js*。</span><span class="sxs-lookup"><span data-stu-id="ed0de-166">Save *Gruntfile.js*.</span></span> <span data-ttu-id="ed0de-167">该文件应类似于下面的屏幕截图。</span><span class="sxs-lookup"><span data-stu-id="ed0de-167">The file should look something like the screenshot below.</span></span>

    ![初始 gruntfile](using-grunt/_static/gruntfile-js-initial.png)

5. <span data-ttu-id="ed0de-169">右键单击*Gruntfile.js*和选择**任务运行程序资源管理器**从上下文菜单。</span><span class="sxs-lookup"><span data-stu-id="ed0de-169">Right-click *Gruntfile.js* and select **Task Runner Explorer** from the context menu.</span></span> <span data-ttu-id="ed0de-170">任务运行程序资源管理器窗口将打开。</span><span class="sxs-lookup"><span data-stu-id="ed0de-170">The Task Runner Explorer window will open.</span></span>

    ![任务运行程序资源管理器菜单](using-grunt/_static/task-runner-explorer-menu.png)

6. <span data-ttu-id="ed0de-172">验证`clean`下显示**任务**在任务运行程序资源管理器。</span><span class="sxs-lookup"><span data-stu-id="ed0de-172">Verify that `clean` shows under **Tasks** in the Task Runner Explorer.</span></span>

    ![任务运行程序资源管理器任务列表](using-grunt/_static/task-runner-explorer-tasks.png)

7. <span data-ttu-id="ed0de-174">右键单击 clean 任务，然后选择**运行**从上下文菜单。</span><span class="sxs-lookup"><span data-stu-id="ed0de-174">Right-click the clean task and select **Run** from the context menu.</span></span> <span data-ttu-id="ed0de-175">命令窗口将显示任务的进度。</span><span class="sxs-lookup"><span data-stu-id="ed0de-175">A command window displays progress of the task.</span></span>

    ![任务运行程序资源管理器运行 clean 任务](using-grunt/_static/task-runner-explorer-run-clean.png)
    
    > [!NOTE]
    > <span data-ttu-id="ed0de-177">没有任何文件或目录尚未清理。</span><span class="sxs-lookup"><span data-stu-id="ed0de-177">There are no files or directories to clean yet.</span></span> <span data-ttu-id="ed0de-178">如果您愿意，你可以在解决方案资源管理器中手动创建它们，然后运行为测试的 clean 任务。</span><span class="sxs-lookup"><span data-stu-id="ed0de-178">If you like, you can manually create them in the Solution Explorer and then run the clean task as a test.</span></span>
    
8. <span data-ttu-id="ed0de-179">在 initConfig() 方法中，添加一个条目`concat`使用下面的代码。</span><span class="sxs-lookup"><span data-stu-id="ed0de-179">In the initConfig() method, add an entry for `concat` using the code below.</span></span>

    <span data-ttu-id="ed0de-180">`src`属性数组列出文件合并，它们应合并的顺序。</span><span class="sxs-lookup"><span data-stu-id="ed0de-180">The `src` property array lists files to combine, in the order that they should be combined.</span></span> <span data-ttu-id="ed0de-181">`dest`属性将路径分配给组合生成的文件。</span><span class="sxs-lookup"><span data-stu-id="ed0de-181">The `dest` property assigns the path to the combined file that is produced.</span></span>

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```
    
    > [!NOTE]
    > <span data-ttu-id="ed0de-182">`all`在上面的代码的属性为目标的名称。</span><span class="sxs-lookup"><span data-stu-id="ed0de-182">The `all` property in the code above is the name of a target.</span></span> <span data-ttu-id="ed0de-183">某些 Grunt 任务中使用的目标用于允许多个生成环境。</span><span class="sxs-lookup"><span data-stu-id="ed0de-183">Targets are used in some Grunt tasks to allow multiple build environments.</span></span> <span data-ttu-id="ed0de-184">你可以查看内置的目标使用 Intellisense，或指定你自己。</span><span class="sxs-lookup"><span data-stu-id="ed0de-184">You can view the built-in targets using Intellisense or assign your own.</span></span>
    
9. <span data-ttu-id="ed0de-185">添加`jshint`任务使用下面的代码。</span><span class="sxs-lookup"><span data-stu-id="ed0de-185">Add the `jshint` task using the code below.</span></span>

    <span data-ttu-id="ed0de-186">Jshint 代码质量实用工具运行的临时目录中找到的每个 JavaScript 文件。</span><span class="sxs-lookup"><span data-stu-id="ed0de-186">The jshint code-quality utility is run against every JavaScript file found in the temp directory.</span></span>
    
    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="ed0de-187">选项"-W069"一个错误时产生的 jshint JavaScript 使用方括号语法，即分配而不是点表示法，属性`Tastes["Sweet"]`而不是`Tastes.Sweet`。</span><span class="sxs-lookup"><span data-stu-id="ed0de-187">The option "-W069" is an error produced by jshint when JavaScript uses bracket syntax to assign a property instead of dot notation, i.e. `Tastes["Sweet"]` instead of `Tastes.Sweet`.</span></span> <span data-ttu-id="ed0de-188">选项关闭了警告，以允许进程继续的其余部分。</span><span class="sxs-lookup"><span data-stu-id="ed0de-188">The option turns off the warning to allow the rest of the process to continue.</span></span>

10.  <span data-ttu-id="ed0de-189">添加`uglify`任务使用下面的代码。</span><span class="sxs-lookup"><span data-stu-id="ed0de-189">Add the `uglify` task using the code below.</span></span>

    <span data-ttu-id="ed0de-190">任务 minifies *combined.js*文件的临时目录中找到并在 wwwroot/lib 以下标准命名约定中创建结果文件*\<文件名\>。 min.js*.</span><span class="sxs-lookup"><span data-stu-id="ed0de-190">The task minifies the *combined.js* file found in the temp directory and creates the result file in wwwroot/lib following the standard naming convention *\<file name\>.min.js*.</span></span>
    
    ```javascript
    uglify: {
      all: {
        src: ['temp/combined.js'],
        dest: 'wwwroot/lib/combined.min.js'
      }
    },
    ```

11. <span data-ttu-id="ed0de-191">在加载 grunt contrib 清理调用 grunt.loadNpmTasks()，包括相同的调用，用于 jshint，concat 并 uglify 使用下面的代码使用。</span><span class="sxs-lookup"><span data-stu-id="ed0de-191">Under the call grunt.loadNpmTasks() that loads grunt-contrib-clean, include the same call for jshint, concat and uglify using the code below.</span></span>
    
    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

12. <span data-ttu-id="ed0de-192">保存*Gruntfile.js*。</span><span class="sxs-lookup"><span data-stu-id="ed0de-192">Save *Gruntfile.js*.</span></span> <span data-ttu-id="ed0de-193">该文件应类似于下面的示例。</span><span class="sxs-lookup"><span data-stu-id="ed0de-193">The file should look something like the example below.</span></span>

    ![完成 grunt 文件示例](using-grunt/_static/gruntfile-js-complete.png)

13. <span data-ttu-id="ed0de-195">请注意，该任务运行程序资源管理器任务列表包括`clean`， `concat`，`jshint`和`uglify`任务。</span><span class="sxs-lookup"><span data-stu-id="ed0de-195">Notice that the Task Runner Explorer Tasks list includes `clean`, `concat`, `jshint` and `uglify` tasks.</span></span> <span data-ttu-id="ed0de-196">按顺序运行每个任务，并观察解决方案资源管理器中的结果。</span><span class="sxs-lookup"><span data-stu-id="ed0de-196">Run each task in order and observe the results in Solution Explorer.</span></span> <span data-ttu-id="ed0de-197">每个任务应运行且未发生错误。</span><span class="sxs-lookup"><span data-stu-id="ed0de-197">Each task should run without errors.</span></span>
    
    ![任务运行程序资源管理器运行每个任务](using-grunt/_static/task-runner-explorer-run-each-task.png)
    
    <span data-ttu-id="ed0de-199">Concat 任务创建一个新*combined.js*文件并将其放到临时目录。</span><span class="sxs-lookup"><span data-stu-id="ed0de-199">The concat task creates a new *combined.js* file and places it into the temp directory.</span></span> <span data-ttu-id="ed0de-200">Jshint 任务只需运行，但不生成输出。</span><span class="sxs-lookup"><span data-stu-id="ed0de-200">The jshint task simply runs and doesn’t produce output.</span></span> <span data-ttu-id="ed0de-201">Uglify 任务创建一个新*combined.min.js*文件并将其放到 wwwroot/lib。</span><span class="sxs-lookup"><span data-stu-id="ed0de-201">The uglify task creates a new *combined.min.js* file and places it into wwwroot/lib.</span></span> <span data-ttu-id="ed0de-202">完成时，解决方案应类似下面的屏幕截图：</span><span class="sxs-lookup"><span data-stu-id="ed0de-202">On completion, the solution should look something like the screenshot below:</span></span>
    
    ![解决方案资源管理器在所有任务](using-grunt/_static/solution-explorer-after-all-tasks.png)
    
    > [!NOTE]
    > <span data-ttu-id="ed0de-204">每个包的选项的详细信息，请访问[https://www.npmjs.com/](https://www.npmjs.com/)和查找在主页上的搜索框中的包名称。</span><span class="sxs-lookup"><span data-stu-id="ed0de-204">For more information on the options for each package, visit [https://www.npmjs.com/](https://www.npmjs.com/) and lookup the package name in the search box on the main page.</span></span> <span data-ttu-id="ed0de-205">例如，你可以查找 grunt contrib 清理包以获取说明它的所有参数的文档链接。</span><span class="sxs-lookup"><span data-stu-id="ed0de-205">For example, you can look up the grunt-contrib-clean package to get a documentation link that explains all of its parameters.</span></span>

### <a name="all-together-now"></a><span data-ttu-id="ed0de-206">总的来说</span><span class="sxs-lookup"><span data-stu-id="ed0de-206">All together now</span></span>

<span data-ttu-id="ed0de-207">使用 Grunt`registerTask()`方法以按特定顺序运行一系列任务。</span><span class="sxs-lookup"><span data-stu-id="ed0de-207">Use the Grunt `registerTask()` method to run a series of tasks in a particular sequence.</span></span> <span data-ttu-id="ed0de-208">例如，若要运行示例上述顺序干净的步骤-> concat-> jshint-> uglify，将下面的代码添加到该模块。</span><span class="sxs-lookup"><span data-stu-id="ed0de-208">For example, to run the example steps above in the order clean -> concat -> jshint -> uglify, add the code below to the module.</span></span> <span data-ttu-id="ed0de-209">应将代码添加到与外部 initConfig loadNpmTasks() 调用相同的级别。</span><span class="sxs-lookup"><span data-stu-id="ed0de-209">The code should be added to the same level as the loadNpmTasks() calls, outside initConfig.</span></span>

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

<span data-ttu-id="ed0de-210">新任务将显示在任务运行程序资源管理器的别名任务中。</span><span class="sxs-lookup"><span data-stu-id="ed0de-210">The new task shows up in Task Runner Explorer under Alias Tasks.</span></span> <span data-ttu-id="ed0de-211">您可以右键单击，并运行它，就像其他任务。</span><span class="sxs-lookup"><span data-stu-id="ed0de-211">You can right-click and run it just as you would other tasks.</span></span> <span data-ttu-id="ed0de-212">`all`任务将运行`clean`， `concat`，`jshint`和`uglify`，按顺序。</span><span class="sxs-lookup"><span data-stu-id="ed0de-212">The `all` task will run `clean`, `concat`, `jshint` and `uglify`, in order.</span></span>

![别名 grunt 任务](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a><span data-ttu-id="ed0de-214">监视的更改</span><span class="sxs-lookup"><span data-stu-id="ed0de-214">Watching for changes</span></span>

<span data-ttu-id="ed0de-215">A`watch`任务保留文件和目录。</span><span class="sxs-lookup"><span data-stu-id="ed0de-215">A `watch` task keeps an eye on files and directories.</span></span> <span data-ttu-id="ed0de-216">如果它检测到更改，监视将自动触发任务。</span><span class="sxs-lookup"><span data-stu-id="ed0de-216">The watch triggers tasks automatically if it detects changes.</span></span> <span data-ttu-id="ed0de-217">将下面的代码添加到 initConfig，若要监视的更改\*TypeScript 目录中的.js 文件。</span><span class="sxs-lookup"><span data-stu-id="ed0de-217">Add the code below to initConfig to watch for changes to \*.js files in the TypeScript directory.</span></span> <span data-ttu-id="ed0de-218">如果更改 JavaScript 文件，`watch`将运行`all`任务。</span><span class="sxs-lookup"><span data-stu-id="ed0de-218">If a JavaScript file is changed, `watch` will run the `all` task.</span></span>

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

<span data-ttu-id="ed0de-219">添加对的调用`loadNpmTasks()`以显示`watch`任务运行程序资源管理器中的任务。</span><span class="sxs-lookup"><span data-stu-id="ed0de-219">Add a call to `loadNpmTasks()` to show the `watch` task in Task Runner Explorer.</span></span>

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

<span data-ttu-id="ed0de-220">右键单击任务运行程序资源管理器中的监视任务，并从上下文菜单中选择运行。</span><span class="sxs-lookup"><span data-stu-id="ed0de-220">Right-click the watch task in Task Runner Explorer and select Run from the context menu.</span></span> <span data-ttu-id="ed0de-221">显示运行的监视任务的命令窗口将显示"等待..."</span><span class="sxs-lookup"><span data-stu-id="ed0de-221">The command window that shows the watch task running will display a "Waiting…"</span></span> <span data-ttu-id="ed0de-222">消息。</span><span class="sxs-lookup"><span data-stu-id="ed0de-222">message.</span></span> <span data-ttu-id="ed0de-223">打开一个 TypeScript 文件，添加一个空格，然后保存该文件。</span><span class="sxs-lookup"><span data-stu-id="ed0de-223">Open one of the TypeScript files, add a space, and then save the file.</span></span> <span data-ttu-id="ed0de-224">这将触发监视任务并触发其他任务以按顺序运行。</span><span class="sxs-lookup"><span data-stu-id="ed0de-224">This will trigger the watch task and trigger the other tasks to run in order.</span></span> <span data-ttu-id="ed0de-225">下面的屏幕截图显示了示例运行。</span><span class="sxs-lookup"><span data-stu-id="ed0de-225">The screenshot below shows a sample run.</span></span>

![运行任务输出](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a><span data-ttu-id="ed0de-227">绑定到 Visual Studio 事件</span><span class="sxs-lookup"><span data-stu-id="ed0de-227">Binding to Visual Studio events</span></span>

<span data-ttu-id="ed0de-228">除非你想要手动启动你的任务，每次在 Visual Studio 中工作时，你可以将绑定到的任务**之前生成**，**后生成**，**清理**，和**项目打开**事件。</span><span class="sxs-lookup"><span data-stu-id="ed0de-228">Unless you want to manually start your tasks every time you work in Visual Studio, you can bind tasks to **Before Build**, **After Build**, **Clean**, and **Project Open** events.</span></span>

<span data-ttu-id="ed0de-229">接下来将绑定`watch`以便使其运行每次 Visual Studio 将打开。</span><span class="sxs-lookup"><span data-stu-id="ed0de-229">Let’s bind `watch` so that it runs every time Visual Studio opens.</span></span> <span data-ttu-id="ed0de-230">在任务运行程序资源管理器，右键单击监视任务并选择**绑定 > 项目打开**从上下文菜单。</span><span class="sxs-lookup"><span data-stu-id="ed0de-230">In Task Runner Explorer, right-click the watch task and select **Bindings > Project Open** from the context menu.</span></span>

![将任务绑定到在项目打开](using-grunt/_static/bindings-project-open.png)

<span data-ttu-id="ed0de-232">卸载并重新加载项目。</span><span class="sxs-lookup"><span data-stu-id="ed0de-232">Unload and reload the project.</span></span> <span data-ttu-id="ed0de-233">在项目加载后再次，监视任务将开始自动运行。</span><span class="sxs-lookup"><span data-stu-id="ed0de-233">When the project loads again, the watch task will start running automatically.</span></span>

## <a name="summary"></a><span data-ttu-id="ed0de-234">摘要</span><span class="sxs-lookup"><span data-stu-id="ed0de-234">Summary</span></span>

<span data-ttu-id="ed0de-235">Grunt 是可以用于自动执行大多数客户端生成任务的功能强大的任务运行程序。</span><span class="sxs-lookup"><span data-stu-id="ed0de-235">Grunt is a powerful task runner that can be used to automate most client-build tasks.</span></span> <span data-ttu-id="ed0de-236">Grunt 利用 NPM 来提供其包和工具与 Visual Studio 的集成的功能。</span><span class="sxs-lookup"><span data-stu-id="ed0de-236">Grunt leverages NPM to deliver its packages, and features tooling integration with Visual Studio.</span></span> <span data-ttu-id="ed0de-237">Visual Studio 的任务运行程序资源管理器检测到配置文件的更改，并提供方便的界面以运行任务，查看正在运行的任务，并将任务绑定到 Visual Studio 事件。</span><span class="sxs-lookup"><span data-stu-id="ed0de-237">Visual Studio's Task Runner Explorer detects changes to configuration files and provides a convenient interface to run tasks, view running tasks, and bind tasks to Visual Studio events.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ed0de-238">其他资源</span><span class="sxs-lookup"><span data-stu-id="ed0de-238">Additional resources</span></span>

   * [<span data-ttu-id="ed0de-239">使用 Gulp</span><span class="sxs-lookup"><span data-stu-id="ed0de-239">Using Gulp</span></span>](using-gulp.md)
