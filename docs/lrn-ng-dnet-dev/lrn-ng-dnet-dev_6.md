## 第六章：创建 ASP.NET Core Web API 服务用于 Angular

本章将指引您创建 ASP.NET Web API 服务用于上一章中创建的 Angular 应用程序。

在本章中，我们将涵盖以下主题：

+   RESTful Web Services

+   ASP.NET Web API 概述

+   创建 ASP.NET Web API 服务

+   将 ASP.NET Web API 与 Angular 应用程序集成

## RESTful Web Services

*** 

**表征状态转移** (**REST**) 是一种可以应用于实现 RESTful 服务的架构风格或设计原则。REST 确保了客户端和服务之间的通信通过拥有有限数量的操作而改善。REST 帮助您以一种简单的方式组织这些独立系统之间的通信。

在 REST 中，每个资源都由自己的 **通用资源标识符** (**URI**) 标识。它在 HTTP 的基础上使用，并利用 HTTP 动词，如 `GET`、`POST`、`PUT` 和 `DELETE`，来控制或访问资源。

**表征状态转移** (**REST**)是一种无状态的 Web 服务，易于扩展，工作在 HTTP 协议下，可以从任何支持 HTTP 的设备上访问。客户端不需要担心除数据格式之外的任何内容：

![图片](img/image_06_001.png)

一个 RESTful 服务

## ASP.NET Web API 概述

*** 

ASP.NET Web API 是一个可以在 .NET 框架上构建 RESTful 服务的框架。ASP.NET Web API 基于 HTTP 协议，以 URI 形式公开 HTTP 动词/操作，允许客户端应用程序使用 HTTP 动词与数据交互。任何支持 HTTP 通信的客户端应用程序或设备都可以轻松访问 Web API 服务。

正如前一节所讨论的，RESTful 服务通过 URI 标识资源。例如，我们有 [`www.programmerguide.net/api/todos/101`](http://www.programmerguide.net/api/todos/101)，并且 Angular 应用程序应用一个 GET 请求。响应这个 GET 请求的 C# 方法将在 Web API 控制器类中。路由技术将根据配置或在相应的类和方法中注释的路由来映射请求 URI 与相应的控制器和方法。

在这里，按照默认配置，请求将由 `TodosController` 中的 `Get` 方法处理。`Get` 方法将根据 ID 值 101 从数据库中检索 `Todo` 项，并将其作为 `Todo` 对象返回。返回的 `Todo` 对象将被序列化为 JSON 或 XML。

对于 `Post` 方法，新发布的 `Todo` 对象将以 JSON 形式从请求体中接收，并且将被反序列化成 `Todo` 对象以在 `TodosController` 的 `Post` 方法中使用。

我们可以通过强大的 ASP.NET **Model-View-Controller** (**MVC**) 编程模型在 ASP.NET Web API 中创建基于 HTTP 的服务。路由、模型绑定和验证等功能提供了在使用 ASP.NET Web API 开发 RESTful Web 服务时更大的灵活性。

### 为什么 ASP.NET Web API 很适合 Angular

ASP.NET Web API 是一个用于构建 HTTP 服务的框架。它采用非常轻量级的架构，可以通过 RESTful 方式在 Angular 中以异步方式访问 HTTP 服务。使用 ASP.NET Web API，我们可以轻松地在 Angular 应用程序中同步数据。

## 创建 ASP.NET Web API 服务

* * *

让我们为上一章创建的我的待办应用添加 ASP.NET Web API 服务。我们的我的待办应用是在 Visual Studio 2015 中使用空的 ASP.NET 5 模板创建的。创建空项目时，会生成一个精简的 Web 应用程序。它不包括与 MVC 或 Web API 相关的程序集。因此，我们需要明确添加所需的程序集或模块来实现应用程序中的 Web API。

### 向 ASP.NET 项目添加和配置 MVC 服务

由于 ASP.NET Core 将 Web API 与 MVC 合并，我们需要添加一个 MVC 服务来实现应用程序中的 Web API：

1.  安装 `NuGet` 包 `Microsoft.AspNetCore.MVC`。

1.  在 Visual Studio 中从项目的根文件夹中打开 `Startup.cs` 文件。

1.  在 `ConfigureServices` 方法下添加以下语句以向项目添加 MVC 服务

```ts
    public void   ConfigureServices(IServiceCollection   
    services)   
        {   
            services.AddMvc();   
        }   
```

1.  我们刚刚在项目中启用了 MVC。接下来，我们将通过在 `Configure` 方法中添加以下语句来将 MVC 与我们的请求管道连接起来：

```ts
    app.UseMvc();
```

### 向 ASP.NET 应用程序添加 Web API 控制器

我们刚刚启用并连接了 MVC 服务到我们的应用程序。现在，让我们按照以下步骤添加一个 Web API 控制器：

1.  在 `我的待办` 项目上右键单击，导航到 `Add` | `New Folder`，并命名文件夹为 `Controllers`：

![](img/image_06_002.png)

在我的待办项目下创建一个用于控制器的新文件夹

1.  现在，右键单击我们刚刚创建的 `Controllers` 文件夹，转到 `Add` | `New Item`：

![](img/image_06_003.png)

将 Web API 控制器添加到控制器文件夹中

1.  选择 **`Minimal Dependencies`** 并单击 **`Add`** 如果您收到一个添加 MVC 依赖项的弹出窗口：

![](img/image_06_004.png)

添加最小的 MVC 依赖项

Visual Studio 2017 添加了一个 `ScaffoldingReadMe.txt` 自述文件，其中包含以下启用脚手架的说明;遵循并相应地更新您的项目代码。

ASP.NET MVC 核心依赖项已添加到项目中。但是，您可能仍然需要对项目进行以下更改：

1.  向项目添加 `Scaffolding``CLI` 工具：

```ts
    <ItemGroup>   
     <DotNetCliToolReference    
     Include="Microsoft.VisualStudio.Web.CodeGeneration.  
     Tools"  Version="1.0.0" />   
    </ItemGroup>   
```

1.  下面是对 `Startup` 类的建议更改：

```ts
    2.1 Add a constructor:   
        public IConfigurationRoot   Configuration { get; }   
        public Startup(IHostingEnvironment   env)   
        {   
            var builder = new   ConfigurationBuilder()   
                .SetBasePath(env.ContentRootPath)   
                .AddJsonFile("appsettings.json",     
                 optional: true, 
                    reloadOnChange: true)   
                .AddJsonFile($"appsettings.
                 {env.EnvironmentName}.json",   optional: 
                  true)   
                .AddEnvironmentVariables();   
            Configuration =   builder.Build();   
        }   
    2.2 Add MVC services:   
        public void   ConfigureServices(IServiceCollection  
        services)   
        {   
            // Add framework   services.   
            services.AddMvc();   
       }   
    2.3 Configure web app to use   use Configuration and 
        use MVC routing:  
        public void   Configure(IApplicationBuilder app, 
        IHostingEnvironment env, ILoggerFactory   
        loggerFactory)   
        {      
        loggerFactory.AddConsole(Configuration.GetSection       
        ("Logging"));   
              loggerFactory.AddDebug();  
            if (env.IsDevelopment())   
            {   
                  app.UseDeveloperExceptionPage();   
            }   
            else   
            {   
                  app.UseExceptionHandler("/Home/Error");   
            }   
            app.UseStaticFiles();   

            app.UseMvc(routes   =>   
            {   
                routes.MapRoute(   
                    name: "default",   
                    template: " 
            {controller=Home}/{action=Index}
                     /{id?}");   
            });   
        }
```

1.  再次右键单击 `Controllers` 文件夹，转到 **`Add`** | **`Controllers`**，选择 **`API Controller with read/write actions`**，并将其命名为 `TodosController`：

![](img/image_06_005.png)

将控制器命名为 TodosController

### 注意

如果你在下面的截图中看到了错误，你需要通过编辑你的`csproj`文件添加给定的 XML 标签，然后再次添加控制器。

这是错误：

![](img/image_06_006.png)

以下是 XML 标签的代码：

```ts
<ItemGroup>   
          <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools"   Version="1.0.1" />   
</ItemGroup>   
```

这将为我们创建`TodosController` Web API 控制器，并提供以下模板代码，供我们根据需求进行修改：

```ts
[Produces("application/json")]   
    [Route("api/Todos")]   
    public class TodosController   : Controller   
    {   
        // GET: api/Todos   
        [HttpGet]   
        public   IEnumerable<string> Get()   
        {   
            return new string[] {   "value1", "value2" };   
        }  
        // GET: api/Todos/5   
        [HttpGet("{id}", Name = "Get")]   
        public string Get(int id)   
        {   
            return "value";   
        }    
        // POST: api/Todos   
        [HttpPost]   
        public void   Post([FromBody]string value)   
        {   
        }   
        // PUT: api/Todos/5   
        [HttpPut("{id}")]   
        public void Put(int id,   [FromBody]string value)   
        {   
        }   
        // DELETE:   api/ApiWithActions/5   
        [HttpDelete("{id}")]   
        public void Delete(int   id)   
        {   
        }   
    }   
```

1.  按下*F5*运行应用程序，并从浏览器导航到`http://localhost:2524/api/todos`。

### 注意

你的系统可能有不同的端口。

你将会在`TodosController`中看到以下输出，默认代码中的`Get`方法。如您在下面的截图中所见，它只返回了一个字符串数组：

![](img/image_06_007.png)

在 TodoController 中默认的 Get 操作的输出

### 添加模型到 ASP.NET 应用程序

我们配置了我们的应用程序以使用 MVC 服务，并添加了 Web API 控制器。现在，让我们为我们的 My Todo 应用程序添加所需的模型。按照这些步骤添加一个名为`Todo`的模型：

1.  在`My``Todo`项目上右键点击，转到**`Add`** | **`New Folder`**，并将文件夹命名为`Models`：

![](img/image_06_008.png)

在 My Todo 项目下为 Models 添加一个新文件夹

1.  现在，右键点击刚刚创建的`Models`文件夹，然后转到**`Add`** | **`Class`**....：

![](img/image_06_009.png)

在 Models 文件夹下为 Todo 对象添加一个类

1.  将类命名为`Todo`，并将以下代码片段添加到其中：

```ts
   namespace My_Todo.Models
   {
   public class Todo
   {
   public int Id { get; set;
    }
   public string Title { get; set;
    }
   public bool Completed { get; set;
    }
   }  
  }
```

`Todo`是一个 C# POCO 类，代表一个`Todo`项目。它具有属性，例如`Id`保存着`Todo`项目的主键值，`Title`属性保存着`Todo`项目的标题，`Completed`属性保存着布尔标志，指示该项目是否已完成。

### 将 DBContext 添加到 ASP.NET 应用程序

我们刚刚添加了`Todo`模型。现在，让我们添加`DBContext`来管理和持久化数据库中的`Todo`。`DBContext`充当您的类和数据库之间的桥梁。要添加它，请按照以下步骤操作：

1.  右键点击`Models`文件夹，转到**`Add`** | **`Class`**：

![](img/image_06_010.png)

在 Models 文件夹下添加一个 DBContext 类

1.  将类命名为`TodoContext`，并将以下代码片段添加到其中：

```ts
   public class TodoContext : DbContext
   {
     public TodoContext(DbContextOptions<TodoContext>       
     options)
     : base(options)
    {
    }
    public DbSet<Todo> Todos { get; set; }
  }
```

`TodoContext`帮助你与数据库交互，并将更改提交为一个单独的工作单元。`TodoContext`被配置为使用 SQL Server，并且连接字符串是从我们将在下一步添加的`config.json`文件中读取的。

1.  在`Startup.cs`中添加使用语句以导入`Microsoft.EntityFrameworkCore`。

1.  通过将以下代码片段添加到`ConfigureServices`方法中来配置 SQL 服务：

```ts
    services.AddEntityFrameworkSqlServer()   
    .AddDbContext<TodoContext>(options =>   
    options.UseSqlServer(Configuration.GetConnectionString
    ("DefaultConnection")));   
    services.AddMvc();   
```

1.  添加一个`appsettings.json`文件来保存连接字符串的值，并更新它的内容如下：

```ts
 {   
   "ConnectionStrings": 
    {   
     "DefaultConnection": "Server=(localdb)\\mssqllocaldb;
         Database=aspnet-CloudInsights-f2d509d5-468f-4bc9-  
         9c47-
         0593d0907063;Trusted_Connection=True;
         MultipleActiveResultSets=true"   
    },   
   "Logging": 
    {
     "IncludeScopes": false,   
     "LogLevel": {   
      "Default": "Warning"   
     }   
   }   
 }   
```

在这个`JSON`文件中，我们在`data`项下添加了连接字符串。

```ts
Startup.cs file is as follows:
```

```ts
public class Startup   
    {   
        public Startup(IHostingEnvironment   env)   
        {   
            var builder = new   ConfigurationBuilder()   
                  .SetBasePath(env.ContentRootPath)   
                .AddJsonFile("appsettings.json",   optional: true, reloadOnChange: true)   
                .AddJsonFile($"appsettings.{env.EnvironmentName}.json",   optional: true)   
                  .AddEnvironmentVariables();   
            Configuration =   builder.Build();   
        }   
        public IConfigurationRoot   Configuration { get; }   

        // This method gets   called by the runtime. Use this method to add services to the container.   
        // For more information   on how to configure your application, visit   https://go.microsoft.com/fwlink/?LinkID=398940   
        public void ConfigureServices(IServiceCollection   services)   
        {   
              services.AddEntityFrameworkSqlServer()   
              .AddDbContext<TodoContext>(options =>   
                  options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));   
            // Add framework   services.   
            services.AddMvc();   
        }   
        // This method gets   called by the runtime. Use this method to configure the HTTP request   pipeline.   
        public void   Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory   loggerFactory)   
        {   
              loggerFactory.AddConsole();   

            if   (env.IsDevelopment())   
            {   
                  app.UseDeveloperExceptionPage();   
            }   
              app.UseDefaultFiles();   
            app.UseStaticFiles();   
              app.UseStaticFiles(new StaticFileOptions   
            {   
                FileProvider =   new PhysicalFileProvider(   
                      Path.Combine(env.ContentRootPath, "node_modules")   
                ),   
                RequestPath = "/"   + "node_modules"   
            });   
            app.UseMvc();   
        }   
    }   
```

在`Startup.cs`构造函数中，我们创建了用于从`config.json`文件中读取的配置。在`ConfigureServices`方法中，我们添加了 Entity Framework 并将 SQL Server 和`TodoContext`连接到了它。

## 使用数据库迁移

* * *

Entity Framework 中的数据库迁移帮助您在应用程序开发阶段创建数据库或更新数据库模式。现在我们已经准备好了所需的模型和数据库上下文。现在需要创建数据库。让我们使用 Entity Framework 中的数据库迁移功能在 SQL Server Compact 中创建数据库。按照以下步骤操作：

1.  首先通过编辑将以下 XML 标签添加到`csproj`文件中：

```ts
  <ItemGroup>   
    <DotNetCliToolReference    
    Include="Microsoft.EntityFrameworkCore.Tools.DotNet"  
    Version="1.0.0" />   
  </ItemGroup>   
```

1.  打开命令提示符并导航到项目文件夹。

1.  执行以下命令以初始化迁移的数据库：

![](img/image_06_011.png)

执行命令以添加迁移

此命令在**`My`****`Todo`**项目下创建`Migration`文件夹，并添加两个类以创建表和更新模式。

![](img/image_06_012.png)

与数据库迁移相关的文件

1.  执行以下命令以更新数据库：

![](img/image_06_013.png)

执行命令以更新数据库

此命令根据上下文和模型为我们的应用程序创建`database`。

### 在 Web API 控制器中使用数据库上下文

现在我们已经准备就绪，迁移也已设置好，让我们更新`TodosController` Web API 控制器以使用之前创建的`TodoContext`。按照以下步骤进行：

1.  打开`TodosController.cs`。

1.  声明`_db`私有变量类型为`TodoContext`：

```ts
private TodoContext _db; 
```

1.  定义接受`TodoContext`类型的`context`参数并将`context`值赋给`_db`的`constructor`：

```ts
        public TodosController(TodoContext context) 
        { 
            _db = context; 
        } 
```

1.  引入一个`GET`动作方法，该方法使用`_db`数据库上下文从数据库中返回所有`Todo`项的集合：

```ts
        // GET: api/todos 
        [HttpGet] 
        public IEnumerable<Todo> Get() 
        { 
            return _db.Todos.ToList(); 
        } 
```

1.  引入另一个`GET`动作方法，该方法从数据库中移除已完成的`Todo`项，并使用`_db`数据库上下文返回所有待处理的`Todo`项：

```ts
        // GET: api/todos/pending-only 
        [HttpGet] 
        [Route("pending-only")] 
        public IEnumerable<Todo> GetPendingOnly() 
        { 
            _db.Todos.RemoveRange(_db.Todos.Where(x =>   
            x.Completed == true)); 
            _db.SaveChanges(); 
            return _db.Todos.ToList(); 
        }
```

1.  引入一个`POST`动作方法，该方法在`TodoContext``_db`数据库中插入新的`Todo`项：

```ts
        // POST api/todos 
        [HttpPost] 
        public Todo Post([FromBody]Todo value) 
        { 
            _db.Todos.Add(value); 
            _db.SaveChanges(); 
            return value; 
        } 
```

1.  引入`PUT`动作方法，使用`TodoContext``_db`更新具有匹配 ID 的现有`Todo`项：

```ts
        // PUT api/todos/id 
        [HttpPut("{id}")] 
        public Todo Put(int id, [FromBody]Todo value) 
        { 
            var todo = _db.Todos.FirstOrDefault(x => x.Id  
            == id); 
            todo.Title = value.Title; 
            todo.Completed = value.Completed; 
            _db.Entry(todo).State = 
            Microsoft.Data.Entity.EntityState.Modified; 
            _db.SaveChanges(); 
            return value; 
        } 
```

1.  引入一个`DELETE`动作方法，使用`TodoContext``_db`删除具有匹配 ID 的现有`Todo`项：

```ts
        // DELETE api/todos/id 
        [HttpDelete("{id}")] 
        public void Delete(int id) 
        { 
            var todo = _db.Todos.FirstOrDefault(x => x.Id 
            == id); 
            _db.Entry(todo).State = 
            Microsoft.Data.Entity.EntityState.Deleted; 
            _db.SaveChanges(); 
        }     
TodosController is this:
```

```ts
[Produces("application/json")]   
    [Route("api/Todos")]   
    public class TodosController   : Controller   
    {   
        private TodoContext _db;   
        public   TodosController(TodoContext context)   
        {   
            _db = context;   
        }   
        // GET: api/todos   
        [HttpGet]   
        public   IEnumerable<Todo> Get()   
        {   
            return   
            _db.Todos.ToList();   
        }   
        // GET: api/todos/pending-only   
        [HttpGet]   
        [Route("pending-only")]   
        public   IEnumerable<Todo> GetPendingOnly()   
        {   
            _db.Todos.RemoveRange(_db.Todos.Where(x => 
            x.Completed == true));   
            _db.SaveChanges();   
            return   _db.Todos.ToList();   
        }   
        // POST api/todos   
        [HttpPost]   
        public Todo   Post([FromBody]Todo value)   
        {   
            _db.Todos.Add(value);   
            _db.SaveChanges();   
            return value;   
        }   
        // PUT api/todos/id   
        [HttpPut("{id}")]   
        public Todo Put(int id,   [FromBody]Todo value)   
        {   
            var todo =   _db.Todos.FirstOrDefault(x => 
            x.Id == id);   
            todo.Title =   value.Title;   
            todo.Completed =   value.Completed;   
            _db.Entry(todo).State   = 
            EntityState.Modified;   
            _db.SaveChanges();   
            return value;   
        }   
        // DELETE api/todos/id   
        [HttpDelete("{id}")]   
        public void Delete(int   id)   
        {   
            var todo =   _db.Todos.FirstOrDefault(x => 
            x.Id == id);   
            _db.Entry(todo).State   = EntityState.Deleted;   
            _db.SaveChanges();   
        }   
    }   
```

## 将 ASP.NET Core Web API 集成到 Angular 应用程序中

* * *

在上一节中，我们添加和修改了 Web API 控制器，并介绍了处理`Todo`项的 HTTP 动词方法。现在，让我们修改我们的 Angular 代码，以调用所有 Web API 方法来管理`Todo`项。

### 在 Angular 应用程序中更新模型

首先，我们需要在 Angular 应用程序中的`Todo.ts`中添加`id`属性来保存从 API 接收的`Todo`项的 ID。因此，更新后的`Todo.ts`如下所示：

```ts
export class Todo { 
    id: number; 
    title: string; 
    completed: boolean; 
    constructor(id: number, title: string, completed: 
    boolean) { 
        this.id = id; 
        this.title = title; 
        this.completed = completed; 
    } 
    set isCompleted(value: boolean) { 
        this.completed = value; 
    }  
}  
```

`constructor`接受三个参数：`id`、`title`和`completed`，并将它们分配给`id`、`title`和`completed`属性，分别使用`this`关键字访问它们。`Todo`类还为`completed`属性设置了访问器。

### 准备 Angular 应用程序

准备 Angular 应用程序的步骤如下：

1.  在`package.json`中将`@angular/http`模块添加到依赖项中。需要使用 HTTP 模块来消费 Web API 服务。更新后的`package.json`如下所示：

```ts
 {   
   "version": "1.0.0",   
   "name": "my-todo",   
   "private": true,   
   "dependencies": {   
     "@angular/common": "~4.0.0",   
     "@angular/compiler": "~4.0.0",   
     "@angular/core": "~4.0.0",   
     "@angular/forms": "~4.0.0",   
     "@angular/http": "~4.0.0",   
     "@angular/platform-browser": "~4.0.0",   
     "@angular/platform-browser-dynamic":   "~4.0.0",   
     "systemjs": "0.19.40",   
     "core-js": "².4.1",   
     "rxjs": "5.0.1",   
     "zone.js": "⁰.8.4"   
   },   
   "devDependencies": {   
     "@types/node": "⁶.0.46",   
     "gulp": "³.9.1",   
     "typescript": "~2.1.0"   
   }   
 }
```

1.  使用`@angular/http`在`systemjs.config.js`中进行映射更新。更新后的`systemjs.config.js`如下所示：

```ts
  (function (global) {   
      System.config({           
          paths: {               
             'npm:': 'node_modules/'   
        },   
        map: {   
            'app': 'app',   
            '@angular/common':   
   'npm:@angular/common/bundles/common.umd.js',   
            '@angular/compiler': 
   'npm:@angular/compiler/bundles/compiler.umd.js',   
            '@angular/core': 
   'npm:@angular/core/bundles/core.umd.js',   
            '@angular/forms': 
   'npm:@angular/forms/bundles/forms.umd.js',   
            '@angular/http': 
   'npm:@angular/http/bundles/http.umd.js',   
            '@angular/platform-browser':   
   'npm:@angular/platform-browser/bundles/platform-
    browser.umd.js',   
            '@angular/platform-browser-dynamic':   
   'npm:@angular/platform-browser-
    dynamic/bundles/platform-browser-dynamic.umd.js',   
            'rxjs': 'npm:rxjs'   
          },   
           packages: {   
              app: {   
                  main: './main.js',   
                  defaultExtension:   'js'   
              },   
              rxjs: {   
                  defaultExtension:   'js'   
              }   
          }   
      });   
   })(this);   
```

1.  在`AppModule`中导入`HttpModule`，如下所示：

```ts
   import { NgModule } from '@angular/core';   
   import { BrowserModule } from '@angular/platform-   
   browser';   
   import { FormsModule } from '@angular/forms';   
   import { HttpModule } from '@angular/http';   
   import { AppComponent } from './app.component';   
   import { TodoService } from './todo.service'   
   @NgModule({   
   imports: [   
        BrowserModule,   
        FormsModule,   
        HttpModule   
    ],   
       declarations: [AppComponent],   
       providers: [TodoService],   
       bootstrap: [AppComponent]   
   })   
   export class AppModule { }   
```

1.  如下所示更新模型`Todo`：

```ts
export class Todo {   
    id: number;   
    title: string;   
    completed: boolean;   
    constructor(id: number,   title: string, completed: boolean) {   
        this.id = id;   
        this.title = title;   
        this.completed =   completed;   
    }  
    set isCompleted(value:   boolean) {   
        this.completed = value;   
    }   
}   
```

### 在 TodoService 中消耗 Web API GET 操作

首先，让我们更新`TodoService`，以使用`Http`服务与 Web API 服务通信，从而获取`Todo`项目列表：

1.  打开 app 文件夹中的`todoService.ts`文件。

1.  添加以下`import`语句以导入模块，例如`Injectable`、`Http`、`headers`、`Response`、`Observable`、`map`和`Todo`：

```ts
   import { Injectable } from '@angular/core'; 
   import { Http, Headers } from '@angular/http'; 
   import 'rxjs/add/operator/toPromise';
   import { Todo } from './todo'
```

1.  修改`constructor`以注入`Http`服务，添加`Http`服务的参数：

```ts
  constructor (private http: Http) { ... } 
```

1.  添加`getTodos`方法以使用`Http`标签消费 Web API 服务以获取`Todo`项目列表：

```ts
     getTodos(): Promise<Array<Todo>> { 
        return this.http.get('/api/todos') 
            .toPromise() 
            .then(response => response.json() as   
             Array<Todo>) 
            .catch(this.handleError); 
     }
```

在这里，`toPromise`方法将`http`的`Get`方法返回的`Observable`序列转换为 Promise。然后，我们在返回的 promise 上调用`then`方法或`catch`方法。我们将响应中收到的`JSON`转换为`Todo`数组。

1.  我们刚刚添加了`getTodos`方法。接下来，让我们添加`getPendingTodos`方法来调用配置了 Web API 中`pending-only`路由的`GET`方法，从数据库中删除已完成的`Todo`项目，并只返回待办的`Todo`项目。`GetPendingTodos`的代码片段如下所示：

```ts
    getPendingTodos() { 
    this.http.get('http://localhost:2524/api/todos/    
    pending-only') 
         .subscribe( 
         err => console.log(err), 
         () => console.log('getTodos Complete') 
         ); 
    } 
app.component.ts:
```

```ts
       getPending() { 
       return this.todos.filter((todo: Todo) =>   
       todo.completed === false); 
   } 
```

更新后的`todo.service.ts`用于调用 Web API 的`GET`方法的代码如下：

```ts
import { Injectable } from '@angular/core'; 
import { Http, Headers } from '@angular/http'; 
import 'rxjs/add/operator/toPromise';
import { Todo } from './todo' 
@Injectable() 
export class TodoService { 
    constructor(private http: Http) {    } 
    getTodos(): Promise<Array<Todo>> { 
        return this.http.get('/api/todos') 
            .toPromise() 
            .then(response => response.json() as Array<Todo>) 
            .catch(this.handleError); 
    } 
    getPendingTodos() { 
        this.http.get('/api/todos/pending-only') 
            .subscribe( 
            err => console.log(err), 
            () => console.log('getTodos Complete') 
            ); 
    }    
    removeCompleted() { 
        this.getPendingTodos();         
    } 
    private handleError(error: any): Promise<any> { 
        console.error('An error occurred', error);  
        return Promise.reject(error.message || error); 
    } 
} 
```

### 从 TodoService 向 Web API 发布

我们刚刚更新了`todo.Services.ts`以调用 Web API 的`GET`操作并获取`Todo`项目。现在，让我们添加代码来将新的`Todo`项目发布到 Web API。按照给定的步骤进行操作：

1.  打开`todo.service.ts`。

1.  添加`postTodo`函数以将新的`Todo`项目发布到 Web API 控制器：

```ts
     postTodo(todo: Todo): Promise<Todo> { 
             var headers = new Headers(); 
             headers.append('Content-Type',  
     'application/json'); 
        return this.http.post('/api/todos',  
     JSON.stringify(todo), { headers: headers }) 
            .toPromise() 
            .then(response => response.json() as Todo) 
            .catch(this.handleError); 
     } 
```

此函数接受`Todo`项目作为参数。它定义了带有`JSON`内容类型的`header`部分，并使用`http`服务将`Todo`项目异步发布到 Web API。响应被转换为`Promise`，`then`方法返回`Promise<Todo>`。

### 调用 Web API 的 PUT 操作以更新 Todo 项目

我们刚刚添加了消费 Web API GET 操作的代码，并添加了代码将新的`Todo`项目发布到 Web API 的 POST。现在，让我们使用 Web API 的 PUT 操作来更新现有的 Todo 项目。按照以下步骤进行操作：

1.  打开`todo.service.ts`。

1.  使用以下代码段添加 `putTodo` 函数来调用 Web API 中的 `PUT` 操作来更新现有的 `Todo` 项目：

```ts
     putTodo(todo: Todo) {
       var headers = new Headers(); 
       headers.append('Content-Type', 'application/json'); 
       this.http.put('/api/todos/' + todo.id,  
     JSON.stringify(todo), { headers: headers }) 
            .toPromise() 
            .then(() => todo) 
            .catch(this.handleError); 
     } 
```

此代码定义了具有 `JSON` 内容类型的标头，并调用了 `PUT` 操作方法以及 `JSON` 字符串化的 `Todo` 项目和该 `Todo` 项目的 `id`。 Web API 中的 `PUT` 操作方法将更新数据库中的匹配 `Todo` 项目。

### 调用 Web API 的 DELETE 操作来删除一个 Todo 项目

我们添加了一些代码，通过调用各种 Web API 操作，如`GET`，`POST` 和 `PUT`，来获取、添加和编辑 `Todo` 项目。现在，让我们使用 Web API 中的 `DELETE` 操作来删除匹配的 `Todo` 项目。请按照以下步骤进行：

1.  打开 `todo.service.ts`。

1.  使用以下代码段添加 `deleteTodo` 函数，通过调用 `Web API` 中的 `DELETE` 操作来删除匹配的 `Todo` 项目：

```ts
      deleteTodo(todo: Todo) { 
        this.http.delete('/api/todos/' + todo.id) 
            .subscribe(err => console.log(err), 
            () => console.log('getTodos Complete') 
            ); 
         } 
```

此代码调用 `DELETE` 操作，以及被删除的 `Todo` 项目的 `id`。 Web API 中的 `DELETE` 操作方法将从数据库中检索匹配的 `Todo` 项目并删除它。

### 更新 TodoService 中的包装函数

我们有一些函数，如 `getTodos`，`getPendingTodos`，`postTodos`，`putTodo` 和 `deleteTodo`，这些函数与 `GET`，`POST`，`PUT` 和 `DELETE` Web API 操作交互。 现在，让我们更新或替换作为从 `app.component.ts` 中使用的包装器的 `remove`，`add`，`toggleCompletion` 和 `removeCompleted` 函数的代码。 更新或替换函数的代码，如下所示：

```ts
    remove(todo: Todo) { 
        this.deleteTodo(todo);         
    } 
    add(title: string): Promise<Todo> { 
        var todo = new Todo(0, title, false); 
        return this.postTodo(todo); 
    } 
    toggleCompletion(todo: Todo) { 
        todo.completed = !todo.completed; 
        this.putTodo(todo); 
    } 
    removeCompleted() { 
        this.getPendingTodos();         
    } 
todo.service.ts after all the updates is this:
```

```ts
import { Injectable } from '@angular/core'; 
import { Http, Headers } from '@angular/http'; 
import 'rxjs/add/operator/toPromise'; 
import { Todo } from './todo' 
@Injectable() 
export class TodoService { 
    constructor(private http: Http) {    } 
    getTodos(): Promise<Array<Todo>> { 
        return this.http.get('/api/todos') 
            .toPromise() 
            .then(response => response.json() as Array<Todo>) 
            .catch(this.handleError); 
    } 
    getPendingTodos() { 
        this.http.get('/api/todos/pending-only') 
            .subscribe( 
            err => console.log(err), 
            () => console.log('getTodos Complete') 
            ); 
    }    
    postTodo(todo: Todo): Promise<Todo> { 
        var headers = new Headers(); 
        headers.append('Content-Type', 'application/json'); 
        return this.http.post('/api/todos', JSON.stringify(todo), { headers: headers }) 
            .toPromise() 
            .then(response => response.json() as Todo) 
            .catch(this.handleError); 
    } 
    putTodo(todo: Todo) { 
        var headers = new Headers(); 
        headers.append('Content-Type', 'application/json'); 
        this.http.put('/api/todos/' + todo.id, JSON.stringify(todo), { headers: headers }) 
            .toPromise() 
            .then(() => todo) 
            .catch(this.handleError); 
    } 
    deleteTodo(todo: Todo) { 
        this.http.delete('/api/todos/' + todo.id) 
            .subscribe(err => console.log(err), 
            () => console.log('getTodos Complete') 
            ); 
    }     
    remove(todo: Todo) { 
        this.deleteTodo(todo);         
    } 
    add(title: string): Promise<Todo> { 
        var todo = new Todo(0, title, false); 
        return this.postTodo(todo); 
    } 
    toggleCompletion(todo: Todo) { 
        todo.completed = !todo.completed; 
        this.putTodo(todo); 
    } 
    removeCompleted() { 
        this.getPendingTodos();         
    } 
    private handleError(error: any): Promise<any> { 
        console.error('An error occurred', error);  
        return Promise.reject(error.message || error); 
    } 
} 
```

### 更新 AppComponent

```ts
app.component.ts is as shown:
```

```ts
import { Component, OnInit } from '@angular/core'; 
import { Todo } from './todo' 
import { TodoService } from './todo.service' 
@Component({ 
    selector: 'my-app', 
    templateUrl: './app/app.component.html', 
    providers: [TodoService] 
}) 
export class AppComponent implements OnInit { 
    todos: Array<Todo>; 
    newTodoText = ''; 
    constructor(private todoService: TodoService) { 
        this.todos = new Array(); 
    } 
    getTodos(): void { 
        this.todoService 
            .getTodos() 
            .then(todos => this.todos = todos); 
    } 
    ngOnInit(): void { 
        this.getTodos(); 
    } 
    removeCompleted() { 
        this.todoService.removeCompleted(); 
        this.todos = this.getPending(); 
    } 
    toggleCompletion(todo: Todo) { 
        this.todoService.toggleCompletion(todo); 
    } 
    remove(todo: Todo) { 
        this.todoService.remove(todo); 
        this.todos.splice(this.todos.indexOf(todo), 1); 
    } 
    addTodo() { 
        if (this.newTodoText.trim().length) { 
        this.todoService.add(this.newTodoText).then(res =>    
   { 
            this.getTodos(); 
            }); 
            this.newTodoText = ''; 
            this.getTodos(); 
        } 
    } 
    getPending() { 
        return this.todos.filter((todo: Todo) => todo.completed === false); 
    } 
    getCompleted() { 
        return this.todos.filter((todo: Todo) => todo.completed === true); 
    } 
} 
```

### 更新 AppComponent 模板

`app.component.html` 的更新内容如下所示：

```ts
<section> 
    <header> 
        <h1>todos</h1> 
        <input placeholder="Add new todo" autofocus="" [(ngModel)]="newTodoText"> 
        <button type="button" (click)="addTodo()">Add</button> 
    </header> 
    <section> 
        <ul> 
            <li *ngFor="let todo of todos"> 
                <input type="checkbox" (click)="toggleCompletion(todo)" [checked]="todo.completed"> 
                <label>{{todo.title}}</label> 
                <button (click)="remove(todo)">X</button> 
            </li> 
        </ul> 
    </section> 
    <footer *ngIf="todos.length > 0"> 
        <span><strong>{{getPending().length}}</strong> {{getPending().length == 1 ? 'item' : 'items'}} left</span> 
        <button *ngIf="getCompleted().length > 0" (click)="removeCompleted()">Clear completed</button> 
    </footer> 
</section> 
```

`TexBox` 输入应用了双向绑定，使用 `ngModel` 来绑定新的 `Todo` 项目 `title`。 `Add` 按钮的点击事件与 `AppComponent` 中的 `addTodo` 方法绑定。可用的 `Todo` 项目将在 `<li>` 标签中列出，使用 `ngFor` 迭代 `TodoService` 中的每个 `Todo` 项目。 渲染每个 `Todo` 项目的复选框分别具有 `click` 事件和 `checked` 属性，与 `toggleCompletion` 方法和 `Todo` 项目的 `completed` 属性映射。 接下来，移除按钮的 `click` 事件与 `AppComponent` 中的 `remove` 方法映射。

`footer` 标签中有一个 span，显示待办 `Todo` 项目的计数以及一个按钮，用于从列表中移除已完成的 `Todo` 项目。这个按钮有一个点击事件，映射到 `AppComponent` 中的 `removeCompleted` 方法。

### 更新索引页面

```ts
index.html:
```

```ts
<!DOCTYPE html> 
<html> 
<head> 
    <title>My Todo</title> 
    <script>document.write('<base href="' +   
    document.location + '" />');</script> 
    <meta charset="UTF-8"> 
    <!-- Polyfills --> 
    <script src="img/shim.min.js"></script> 
    <script src="img/zone.min.js"></script> 
    <script src="img/system.src.js"></script> 
    <script src="img/systemjs.config.js"></script> 
    <script> 
      System.import('main.js').catch(function(err){ console.error(err); }); 
    </script> 
</head> 
<body> 
    <my-app>Loading My Todo App...</my-app> 
</body> 
</html> 
```

注意 `body` 标签中有一个特殊的 `<my-app/>` 标签, 这是 `AppComponent` 中的元数据。这是 `AppComponent` 将被实例化并使用模板渲染的地方。

## 运行应用程序

* * *

通过按下 *F5* 运行应用程序，之后，您将能够执行添加、编辑、删除和列出 `Todo` 项目等操作：

![](img/image_06_014.png)

我的 Todo 应用程序具有所有操作

## 总结

* * *

我们从介绍 RESTful 服务开始本章，并为您概述了 ASP.NET Web API。我们还讨论了为什么 ASP.NET Web API 是 Angular 应用程序的最佳选择。然后，您了解了如何在 ASP.NET 5 项目中添加和配置 Entity Framework 以及使用数据库迁移来创建数据库所需的步骤。接下来，我们讲解了创建 Web API 服务和使用 Entity Framework 管理数据的过程。最后，您学会了如何从 Angular 应用程序中调用 Web API。

在本章中，我们讨论了如何从 Angular 应用程序中使用 Web API 服务来添加、更新、删除和检索数据库中的 Todo 项目，使用 Entity Framework。

在下一章中，我们将讨论如何将 Angular 应用程序与 ASP.NET MVC 和 Web API 集成。
