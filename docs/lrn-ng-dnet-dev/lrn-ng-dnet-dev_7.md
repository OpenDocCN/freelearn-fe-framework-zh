## 第七章：在 Visual Studio 中使用 Angular，ASP.NET MVC 和 Web API 创建应用程序

本章将指导您通过将 Angular 应用程序与 ASP.NET MVC 和 ASP.NET Web API 集成的过程。在上一章中，我们使用 Angular 应用程序消耗了 ASP.NET Web API 服务。所有视图都由 Angular 渲染。在本章中，我们将从 ASP.NET MVC 提供视图。因此，它提供了许多机会，比如使用 Razor 语法，因为 Angular 视图将由 ASP.NET MVC 提供动力。

在本章中，我们将涵盖以下主题：

+   使用 ASP.NET MVC 为 Angular 组件模板提供视图

+   结合 ASP.NET MVC，ASP.NET Web API 和 Angular 的路由

## 使用 ASP.NET MVC

* * *

ASP.NET 包括 Web 堆栈，如 ASP.NET MVC，Razor 和 Web API。ASP.NET MVC 框架是建立在 ASP.NET 之上的。ASP.NET MVC Web 框架实现了**模型-视图-控制器**（MVC）模式以开发 Web 应用程序。

在 MVC 模式中，**模型**代表业务对象的状态。**视图**表示用户界面，**控制器**处理模型和视图之间的通信。所有请求将由控制器处理，并返回响应：

![](img/image_07_001.jpg)

MVC 架构

ASP.NET MVC 有自己的视图引擎，称为 Razor 引擎。

## 结合 ASP.NET MVC，ASP.NET Web API 和 Angular 的路由

* * *

**路由**是将端点分解为可处理请求的模块或控制器和操作的过程。路由使 URL 可读且有意义。它还帮助隐藏用户的数据。

### ASP.NET MVC 中的路由

ASP.NET MVC 路由将请求映射到控制器操作。所有路由将在路由表中定义，并由路由引擎使用来匹配请求的 URL 模式与控制器和操作。

我们可以在`Startup.cs`文件的 configure 方法中向路由表添加路由。以下代码片段显示了在路由表上注册的默认路由：

```ts
public void Configure(IApplicationBuilder app) 
{ 
    app.UseIISPlatformHandler(); 
    app.UseDefaultFiles(); 
    app.UseStaticFiles(); 
    app.UseMvc(config => 
    { 
        config.MapRoute( 
            name: "Default", 
            template: "{controller}/{action}/{id?}", 
            defaults: new { controller = "Home", action = "Index" } 
            ); 
    });             
} 
```

在这里，一个路由被注册为模板和默认值。如果在 URL 中未提供控制器或操作名称，则该请求将映射到`HomeController`类中的`Index`操作；否则，它将映射到相应的控制器操作。

在我们的应用程序中，我们有三个 MVC 控制器，分别是`HomeController`，`UserController`和`TodoController`。

#### 添加 ASP.NET MVC HomeController

```ts
Index action:
```

```ts
public IActionResult Index() 
{ 
    return View(); 
} 
```

当一个请求被路由到`Index`操作时，它将返回`Index`视图。`Index.cshtml`的源代码如下所示：

```ts
 @{ 
    Layout = null; 
} 
<!DOCTYPE html> 
<html> 
<head> 
    <meta name="viewport" content="width=device-width" /> 
    <title>Index</title> 
</head> 
<body> 
    <h1>index view</h1> 
</body> 
</html> 
```

按照给定的步骤，将 ASP.NET MVC 的`HomeController`及其相应视图添加到我们在早期章节中创建的`My Todo`应用程序中：

1.  右键单击`Controllers`文件夹并添加一个新的控制器。

1.  将新添加的控制器命名为`HomeController`。请注意，默认情况下`HomeController`已添加了`Index`操作。

1.  现在，让我们为`Index`动作添加一个视图。右键单击`My Todo`应用程序，并添加一个名为`Views`的新文件夹。

1.  然后，在刚刚创建的`Views`文件夹下添加一个名为`Home`的文件夹。

1.  右键单击`Home`文件夹并添加一个名为`Index.cshtml`的新视图：

![](img/image_07_002.png)

ASP.NET MVC HomeController Index 视图

### ASP.NET MVC 的路由

我们刚刚创建了一个 ASP.NET MVC 控制器，并为控制器的`Index`动作添加了一个视图。现在我们需要为 ASP.NET MVC 应用配置路由，以便任何对`Index`动作的请求都由 ASP.NET MVC 路由处理。请按照以下步骤配置 ASP.NET MVC 路由：

1.  打开`Startup.cs`。

1.  请注释或删除`Configure`方法中的`app.UseDefaultFiles()`语句，因为我们将使用 ASP.NET MVC 来提供视图。

1.  用这个声明替换`Configure`方法中的`app.UseMvc()`语句：

```ts
     app.UseMvc(config =>   
            {   
              config.MapRoute(   
              name: "Default",   
              template: "{controller}/{action}/{id?}",   
     defaults: new   { controller = "Home", action =    
     "Index" }   
               );   
            });
```

在这里，我们已经添加了 ASP.NET MVC 的默认路由。对于 Web API 的任何请求都将与控制器中的 HTTP 谓词或动作进行映射。

通过按下*F5*键来运行应用程序，您将在浏览器中看到呈现的图形界面：

![](img/image_07_003.png)

ASP.NET MVC HomeController Index 视图在浏览器中呈现

### 将内容从默认页面移动到 ASP.NET MVC 视图

在前面的部分中，我们能够运行应用程序并注意到默认视图是由 HomeController 提供的 ASP.NET MVC Index 视图。现在，让我们使用`wwwroot`文件夹下的`Index.html`文件的内容更新`Index.cshtml`视图。更新后的`Index.cshtml`如下所示：

```ts
@{   
    Layout = null;   
}   
<!DOCTYPE html>   
<html>   
<head>   
    <title>My   Todo</title>   
    <script>document.write('<base   href="' + 
    document.location + '" />');</script>   
    <meta charset="UTF-8">   
    <!-- Polyfills -->   
    <script src="img/shim.min.js"></script>   
    <script src="img/zone.min.js"></script>   
    <script src="img/system.src.js"></script>   
    <script src="img/systemjs.config.js"></script>   
    <script>   
    System.import('main.js').catch(function(err){   
    console.error(err); });   
    </script>   
</head>   
<body>   
    <h1>My Todo   App</h1>   
    <my-app>Loading My Todo   App...</my-app>   
</body>   
</html>   
```

现在通过按下*F5*来运行应用程序，并注意`my-app`特殊标签已被 Angular 解析为`app.component.html`的模板，如下所示：

![](img/image_07_004.png)

Angular 应用加载到 ASP.NET MVC Index 视图

### ASP.NET Web API 中的路由

任何对 Web API 的请求都将使用路由将其映射到控制器中的 HTTP 谓词或动作。Web API 2 引入了一种基于属性的路由技术，称为**属性路由**。可以在控制器级别和操作级别添加属性路由。可以通过传递 URL 模板来修饰 Web API 控制器的`Route`属性，如下所示：

```ts
[Route("api/[controller]")] 
public class TodosController : Controller 
{    
    // GET: api/todos/pending-only 
    [HttpGet] 
    [Route("pending-only")] 
    public IEnumerable<Todo> GetPendingOnly() 
    { 
    } 
} 
```

在这里，`TodosController`被`Route`修饰，并且使用`api/[controller]` URL 模板。这意味着如果收到的请求为[`www.programmerguide.net/api/todos`](http://www.programmerguide.net/api/todos)，它将被路由到`TodosController`，且动作将根据应用的 HTTP 动作进行选择。

注意`GetPendingOnly`动作被`Route`修饰，并且使用`pending-only`URL 模板。这意味着如果控制器中有更多的`GET`动作可用，且请求 URL 为[`www.programmerguide.net/api/todos/pending-only`](http://www.programmerguide.net/api/todos/pending-only)，它将被映射到`GetPendingOnly`动作。

`TodosController` Web API 控制器的完整源代码如下：

```ts
[Produces("application/json")] 
    [Route("api/Todos")] 
    public class TodosController : Controller 
    { 
        private TodoContext _db; 
        public TodosController(TodoContext context) 
        { 
            _db = context; 
        } 
        // GET: api/todos 
        [HttpGet] 
        public IEnumerable<Todo> Get() 
        { 
            return _db.Todos.ToList(); 
        } 
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
        // POST api/todos 
        [HttpPost] 
        public Todo Post([FromBody]Todo value) 
        { 
            _db.Todos.Add(value); 
            _db.SaveChanges(); 
            return value; 
        } 
        // PUT api/todos/id 
        [HttpPut("{id}")] 
        public Todo Put(int id, [FromBody]Todo value) 
        { 
            var todo = _db.Todos.FirstOrDefault(x => x.Id  
            == id); 
            todo.Title = value.Title; 
            todo.Completed = value.Completed; 
            _db.Entry(todo).State = EntityState.Modified; 
            _db.SaveChanges(); 
            return value; 
        } 
        // DELETE api/todos/id 
        [HttpDelete("{id}")] 
        public void Delete(int id) 
        { 
            var todo = _db.Todos.FirstOrDefault(x => x.Id 
            == id); 
            _db.Entry(todo).State = EntityState.Deleted; 
            _db.SaveChanges(); 
        } 
  } 
```

### Angular 中的路由

正如我们在第三章，*Angular 构建模块 - 第二部分*中看到的那样，Angular 引入了一个组件路由器，它深度链接 URL 请求，映射为此路由注释的组件，并渲染与该组件关联的模板或视图。Angular 路由器不是核心 Angular 框架的一部分，它作为 Angular 路由器模块的一部分。我们需要在`package.json`中的依赖项部分添加对此库的引用，如下所示：

```ts
"dependencies": {
"@angular/router": "~4.0.0",
}
<base> tag with the href attribute that should be added to the head tag in the index file, considering that the app folder is the root of the application:
```

```ts
<base href="/">
```

路由器通过查看浏览器请求的 URL 的`RouteDefinition`决定组件和模板。因此，我们需要配置路由定义。

我们的首页将有三个超链接，分别是`todo`、`about`和`contact`。点击`todo`将导航用户到`todo`应用，点击`about`将导航到`about`视图，最后，点击`contact`将导航用户到`contact`视图。因此，我们需要添加另外两个组件，分别是`AboutComponent`和`ContactComponent`，以及它们各自的模板文件，分别是`about.component.html`和`contact.component.html`。按照下面的步骤创建这些组件和它们的模板：

1.  右键单击`app`文件夹，并添加两个 HTML 模板：`about.component.html`和`contact.component.html`。

1.  将以下 HTML 片段添加为`about.component.html`的内容：

```ts
      <h1>This is the About   View</h1>   
```

1.  将以下 HTML 片段添加为`contact.component.html`的内容：

```ts
      <h1>This is the Contact   View</h1>   
```

1.  右键单击`app`文件夹，添加两个 Angular 组件：`about.component.ts`和`contact.component.ts`。

1.  将以下代码片段添加到`about.component.ts`：

```ts
    import { Component } from '@angular/core';   
    @Component({   
        selector: 'about-me',   
        templateUrl: './app/about.component.html',   
    })   
    export class AboutComponent { }   
```

1.  将以下代码片段添加到`contact.component.ts`：

```ts
    import { Component } from '@angular/core';    
    @Component({   
        selector: 'contact-us',   
        templateUrl: './app/contact.component.html',   
    })     
      export class ContactComponent { }
```

1.  还要创建一个 Angular 组件，`todo.component.ts`，并将`app.component.ts`中的属性和方法移动到`todo.component.ts`。同时，更新`TodoComponent`的导入和注解。`TodoComponent`的完整代码片段如下所示：

```ts
     import { Component, OnInit } from   '@angular/core';   
     import { Todo } from './todo'   
     import { TodoService } from './todo.service'     
     @Component({   
         selector: 'my-app',   
         templateUrl: './app/todo.component.html',   
         providers: [TodoService]   
     })   
       export class TodoComponent   implements OnInit {   
         todos: Array<Todo>;   
         newTodoText = '';   
       constructor(private   todoService: TodoService) {   
          this.todos = new Array();   
       }   
           getTodos(): void {   
           this.todoService   
           .getTodos()   
           .then(todos =>   this.todos = todos);   
       }   
       ngOnInit(): void {   
        this.getTodos();   
       }   
        removeCompleted() {   
        this.todoService.removeCompleted();   
        this.todos =   this.getPending();   
       }   
       toggleCompletion(todo: Todo)   {   
          this.todoService.toggleCompletion(todo);   
       }   
       remove(todo: Todo) {   
          this.todoService.remove(todo);   
          this.todos.splice(this.todos.indexOf(todo), 1);   
       }   
       addTodo() {   
           if (this.newTodoText.trim().length)   {   
           this.todoService.add(this.newTodoText).then(res      
           => {   
           this.getTodos();   
           });   
           this.newTodoText = '';   
           this.getTodos();   
           }   
       }   
       getPending() {   
           return this.todos.filter((todo:   Todo) =>   
     todo.completed === false);   
       }   

        getCompleted() {   
        return   this.todos.filter((todo: Todo) =>   
        todo.completed === true);   
       }   
     }   
```

1.  现在，创建`todo.component.html`模板，并将`app.component.html`的内容移动过去。更新后的`todo.component.html`如下所示：

```ts
    <section>   
       <header>   
          <h1>todos</h1>   
        <input placeholder="Add   new todo" autofocus=""   
        [(ngModel)]="newTodoText">   
        <button type="button"   
        (click)="addTodo()">Add</button>   
     </header>   
    <section>   
        <ul>   
            <li *ngFor="let   todo of todos">   
            <input type="checkbox"    
            (click)="toggleCompletion(todo)"  
            [checked]="todo.completed">   
            <label>{{todo.title}}</label>   
            <button   (click)="remove(todo)">X</button>   
            </li>   
        </ul>   
    </section>   
          <footer *ngIf="todos.length   > 0">   
          <span><strong>{{getPending().length}}</strong>     
          {{getPending().length == 1 ? 'item' : 'items'}}   
          left</span>   
          <button *ngIf="getCompleted().length   > 0"    
          (click)="removeCompleted()">Clear     
          completed</button>   
          </footer>   
    </section>
```

1.  接下来，添加一个`app.routing.ts`文件，并使用下面的代码片段更新它。在这里，我们为`todo`、`about`和`contact`配置了三个路由。此外，我们分配了三个组件--`TodoComponent`、`AboutComponent`和`ContactComponent`--来导出`NgModule`属性的元数据：

```ts
    import { NgModule } from '@angular/core';   
    import { Routes, RouterModule }   from  
    '@angular/router';   
    import { TodoComponent } from './todo.component';   
    import { AboutComponent } from './about.component';   
    import { ContactComponent } from   
    './contact.component';   
    export const appRoutes: Routes =   [   
        {   
            path: '',   
            redirectTo: 'todo',   
            pathMatch: 'full',   
        },       
        { path: 'todo', component:   TodoComponent, data:    
          { title: 'Todo' } },   
        { path: 'about', component:  AboutComponent, data:   
          { title: 'About' } },   
        { path: 'contact', component: ContactComponent,   
           data: { title: 'Contact' } }   
    ];     
    export const routedComponents = [   
        TodoComponent,   
        AboutComponent,   
        ContactComponent   
    ];   
    @NgModule({   
        imports:   [RouterModule.forRoot(appRoutes)],   
        exports: [RouterModule]   
    })   
     export class AppRoutingModule { }   
```

1.  更新`app.module.ts`如下以导入我们在上一步创建的`AppRoutingModule`：

```ts
    import { NgModule } from '@angular/core';   
    import { BrowserModule } from '@angular/platform-  
    browser';   
    import { FormsModule } from '@angular/forms';   
    import { HttpModule } from '@angular/http';   
    import { AppComponent } from './app.component';   
    import { TodoComponent } from './todo.component';   
    import { AboutComponent } from './about.component';   
    import { ContactComponent } from   
    './contact.component';   
    import { AppRoutingModule } from './app.routing';   
    import { TodoService } from './todo.service'     
    @NgModule({   
        imports: [   
            BrowserModule,   
            FormsModule,   
            HttpModule,   
            AppRoutingModule   
        ],   
        declarations: [   
            AppComponent,    
            TodoComponent,   
            AboutComponent,   
            ContactComponent   
        ],   
        providers: [TodoService],   
        bootstrap: [AppComponent]   
    })   
    export class AppModule { }   
```

1.  最后，如下更新`app.component.html`：

```ts
    <a routerLinkActive="active"   [routerLink]="   
    ['/todo']">Todo</a>   
    <a routerLinkActive="active"   [routerLink]="
    ['/about']">About</a>   
    <a routerLinkActive="active"   [routerLink]="
    ['/contact']">Contact</a>   
    <router-outlet></router-outlet>   
```

注意每个超链接都有`routerLink`属性，并分配了路由路径。这里，`routerLinkActive`属性分配了`active` CSS 类，当该路由变为活动状态时，将添加到该元素上。换句话说，当用户点击`Todo`链接时，该链接将被分配`active` CSS 类。

`routerLink`属性使应用程序能够链接到应用程序的特定部分或组件。下一条语句是组件路由的`<router-outlet/>`特殊标记，类似于 AngularJS 1.x 中的`<ng-view/>`标记，用于加载与相应路由路径相关联的模板。

1.  按下*F5*运行应用程序，浏览器将通过导航到**`Todo`**路径来加载应用程序，因为我们已经设置了如果是根路径，就重定向到`todo`：

![](img/image_07_005.png)

加载 todo 模板，URL 为\todo 路径

1.  点击**`About`**链接将导航到\about 路径，并加载`about`的解析模板视图：

![](img/image_07_006.png)

加载 about 模板，URL 为\about 路径

1.  点击**`Contact`**链接将导航到\contact 路径，并加载 about 的解析模板视图：

![](img/image_07_007.png)

加载 contact 模板，URL 为\contact 路径

注意在 URL 中路由路径的变化。

### 将 Angular 模板移到 ASP.NET MVC 模板

我们几乎完成了应用程序。但是，我们只使用了 Angular 视图作为 Angular 组件的模板。我们需要通过 ASP.NET MVC 提供模板。这将使我们能够根据需要添加 Razor 代码，因为这些视图是由 ASP.NET MVC 提供支持的。按照以下步骤添加 Razor 视图并更新每个 Angular 组件中的`templateUrl`：

1.  首先，在`HomeController`中添加三个动作，分别为`About`、`Contact`和`Todo`，如下所示：

```ts
        public IActionResult   About()   
        {   
            return View();   
        }   
        public IActionResult   Contact()   
        {   
            return View();
        }      
        public IActionResult   Todo()   
        {   
            return View();   
        }   
```

1.  在`Views -> Home`文件夹下添加三个视图，分别是`About`、`Contact`和`Todo`，如下所示：

![](img/image_07_008.png)

在 Home 下添加 Razor 视图

1.  将以下 HTML 内容添加到`About.cshtml`：

```ts
    <h1>This is the About Razor   View</h1>   
```

1.  添加以下 HTML 内容到`Contact.cshtml`：

```ts
    <h1>This is the Contact Razor View</h1>
```

1.  然后，将`todo.component.html`的内容移动到`Todo.cshtml`：

1.  现在需要将`AboutComponent`、`ContactComponent`和`TodoComponent`的`templateUrl`的元数据更新为 HomeController 中相应操作的 URL：

```ts
     TodoComponent:   
           templateUrl: '/Home/Todo'   
     AboutComponent:   
           templateUrl: '/Home/About'   
     ContactComponent:   
           templateUrl: '/Home/Contact',   
```

1.  现在，按下*F5*运行应用程序，并注意视图是从 ASP.NET MVC 提供的。现在你可以在视图中添加 Razor 语法，因为它们现在由 ASP.NET MVC 提供支持。

1.  点击`About`链接将导航到\about 路径，并实例化相应的组件。这里，它是`AboutComponent`，并且会呈现适当的`about` Razor 视图：

![](img/image_07_009.png)

关于 Razor 模板呈现

1.  点击**`联系`**链接将导航到 `\contact` 路径，并启动负责呈现`联系`Razor 视图的`ContactComponent`：

![](img/image_07_010.png)

联系 Razor 模板呈现

点击注销将重定向到`登录`视图。

## 摘要

* * *

哇！我们刚刚创建了一个由 ASP.NET MVC 提供支持的 Angular 应用程序，具有后端 Web API。我们结合了 Angular 和 ASP.NET MVC 的路由，并演示了这些路由是如何连接在一起的。

在下一章，我们将讨论测试 Angular 应用程序。
