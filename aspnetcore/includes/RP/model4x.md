<a name="scaffold"></a>
### <a name="scaffold-the-movie-model"></a><span data-ttu-id="cd9d2-101">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="cd9d2-101">Scaffold the Movie model</span></span>

* <span data-ttu-id="cd9d2-102">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="cd9d2-102">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="cd9d2-103">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="cd9d2-103">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc MovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```