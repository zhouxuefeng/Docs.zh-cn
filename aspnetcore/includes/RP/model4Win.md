<a name="scaffold"></a>
### <a name="scaffold-the-movie-model"></a>搭建“电影”模型的基架

* 打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。
* 运行下面的命令：

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc MovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

如果收到错误：
  ```
  The process cannot access the file 
 'RazorPagesMovie/bin/Debug/netcoreapp2.0/RazorPagesMovie.dll' 
  because it is being used by another process.
  ```

退出 Visual Studio，然后重新运行命令。