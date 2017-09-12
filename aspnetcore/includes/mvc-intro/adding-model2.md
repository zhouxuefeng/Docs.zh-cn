## <a name="add-initial-migration-and-update-the-database"></a><span data-ttu-id="1fd50-101">添加初始迁移并更新数据库</span><span class="sxs-lookup"><span data-stu-id="1fd50-101">Add initial migration and update the database</span></span>

* <span data-ttu-id="1fd50-102">打开命令提示符并导航到项目目录。</span><span class="sxs-lookup"><span data-stu-id="1fd50-102">Open a command prompt and navigate to the project directory.</span></span> <span data-ttu-id="1fd50-103">(目录包含*Startup.cs*文件)。</span><span class="sxs-lookup"><span data-stu-id="1fd50-103">(The directory containing the *Startup.cs* file).</span></span>

* <span data-ttu-id="1fd50-104">在命令提示符中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="1fd50-104">Run the following commands in the command prompt:</span></span>

  ```console
  dotnet restore
  dotnet ef migrations add Initial
  dotnet ef database update
  ```
  
  <span data-ttu-id="1fd50-105">[.NET 核心](https://docs.microsoft.com/dotnet/core/tools/index)是.NET 的跨平台实现。</span><span class="sxs-lookup"><span data-stu-id="1fd50-105">[.NET Core](https://docs.microsoft.com/dotnet/core/tools/index) is a cross-platform implementation of .NET.</span></span> <span data-ttu-id="1fd50-106">下面是这些命令执行的操作：</span><span class="sxs-lookup"><span data-stu-id="1fd50-106">Here is what these commands do:</span></span>

  * <span data-ttu-id="1fd50-107">`dotnet restore`： 将下载 NuGet 包中指定*.csproj*文件。</span><span class="sxs-lookup"><span data-stu-id="1fd50-107">`dotnet restore`: Downloads the NuGet packages specified in the *.csproj* file.</span></span>
  * <span data-ttu-id="1fd50-108">`dotnet ef migrations add Initial`运行实体框架.NET 核心 CLI 迁移命令并创建初始迁移。</span><span class="sxs-lookup"><span data-stu-id="1fd50-108">`dotnet ef migrations add Initial` Runs the Entity Framework .NET Core CLI migrations command and creates the initial migration.</span></span> <span data-ttu-id="1fd50-109">在"添加"后的参数是你将分配给迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="1fd50-109">The parameter after "add" is a name that you assign to the migration.</span></span> <span data-ttu-id="1fd50-110">此处你正在命名迁移"初始"因为它是初始数据库迁移。</span><span class="sxs-lookup"><span data-stu-id="1fd50-110">Here you're naming the migration "Initial" because it's the initial database migration.</span></span> <span data-ttu-id="1fd50-111">此操作将创建*数据/迁移/\<日期时间 > _Initial.cs*文件包含要添加的迁移命令*电影*到数据库的表。</span><span class="sxs-lookup"><span data-stu-id="1fd50-111">This operation creates the *Data/Migrations/\<date-time>_Initial.cs* file containing the migration commands to add the *Movie* table to the database.</span></span>
  * <span data-ttu-id="1fd50-112">`dotnet ef database update`使用我们刚刚创建的迁移更新数据库。</span><span class="sxs-lookup"><span data-stu-id="1fd50-112">`dotnet ef database update`  Updates the database with the migration we just created.</span></span>

<span data-ttu-id="1fd50-113">你将在下一教程中了解有关数据库和连接字符串。</span><span class="sxs-lookup"><span data-stu-id="1fd50-113">You'll learn about the database and connection string in the next tutorial.</span></span> <span data-ttu-id="1fd50-114">你将了解有关中的数据模型更改[添加字段](xref:tutorials/first-mvc-app/new-field)教程。</span><span class="sxs-lookup"><span data-stu-id="1fd50-114">You'll learn about data model changes in the [Add a field](xref:tutorials/first-mvc-app/new-field) tutorial.</span></span>
