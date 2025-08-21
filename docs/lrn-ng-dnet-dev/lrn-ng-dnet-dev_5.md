## 第五章：在 Visual Studio 中创建 Angular 单页应用程序

本章将指导您通过使用 Visual Studio 创建 Angular **单页应用程序** （**SPA**）的过程。

在本章中，我们将涵盖以下主题：

+   创建一个 ASP.NET Core web 应用程序

+   使用 NPM 软件包管理器添加客户端软件包

+   使用 Gulp 运行任务

+   添加 Angular 组件和模板

## 创建一个 ASP.NET Core web 应用程序

* * *

让我们从创建 ASP.NET Core web 应用程序开始这一章。我假设您在开发环境中已经安装了 Visual Studio 2017 或更新版本。按照以下步骤创建应用程序：

1.  打开 Visual Studio，然后通过导航到 `File` | `New` | `Project` 来点击菜单项。

1.  从安装模板中导航到 **`Visual C#`**，然后选择 **`Web`**。

1.  然后，选择 **`ASP.NET Core Web Application`** 并输入应用程序名称为 `My Todo`，如下图所示：

![](img/image_05_001.png)

创建名为 My Todo 的项目

1.  选择 **`ASP.NET Core Empty`** 模板，然后点击 **`Ok`** 创建项目，如图所示：

![](img/image_05_002.png)

选择一个空的 ASP.NET Core 模板

我们创建的 `My Todo` 应用程序的解决方案结构如下图所示：

![](img/image_05_003.png)

My Todo 的默认解决方案结构

`Startup` 类是 ASP.NET Core web 应用程序的入口点。 `Startup` 类中的 `Configure` 方法用于设置用于处理应用程序中所有请求的请求管道。在这里，`Startup` 类的默认代码被配置为默认返回 `Hello World!` 文本：

![](img/image_05_004.png)

`Startup` 类的默认代码

所以，当您运行应用程序时，您将在浏览器中获得以下输出：

![](img/image_05_005.png)

'My Todo' 项目的默认输出

现在，让我们让应用程序为任何请求提供默认页面。按照以下步骤进行操作：

1.  选择 `My Todo` 项目下的 `wwwroot` 文件夹。右键单击选择项目，转到 **`Add`**，然后点击 **`New Item`**：

![](img/image_05_006.png)

转到添加新项目菜单

1.  在 **`Add New Item`** 窗口中，点击 **`Web`** 下的 **`Content`**，然后从中心窗格中选择 **`HTML Page`**。输入 `index.html` 作为文件名，然后点击 **`Add`**：

![](img/image_05_007.png)

将 HTML 文件命名为 index.html

1.  更新 `index.html` 文件的内容如下：

![](img/image_05_008.png)

index.html 的更新代码

1.  打开 `Startup` 类并删除以下代码片段，该代码片段将 `Hello World` 默认文本写入每个请求的响应中：

```ts
      app.Run(async (context) =>   
      {   
            await   context.Response.WriteAsync("Hello  
            World!");   
      });   
```

1.  将以下代码添加到 `Configure` 方法中，以便管道为请求提供默认和静态文件：

![](img/image_05_009.png)

启用管道为静态和默认文件提供服务的代码

1.  你需要添加 `Microsoft.AspNetCore.StaticFiles` NuGet 软件包，如图所示，以使用这些扩展:

![](img/image_05_010.png)

如有需要，请向命名空间添加引用

1.  现在，在 `wwwroot` 文件夹下添加一个 `index.html` 文件，并通过按下 *F5* 运行应用程序。你会注意到应用程序为请求提供了 `index.html` 文件作为默认文件。在这里，我已经添加了一个内容为 **`My Todo Landing Page`** 的 `h1` 标签:

![](img/image_05_011.png)

在添加了 index.html 后的应用程序输出

## 使用 NPM 软件包管理器添加客户端软件包

* * *

在我们开发应用程序时，我们将很多框架和库添加为依赖项的引用。在 Visual Studio 中，我们有 NuGet 软件包管理工具来管理应用程序中的所有这些软件包。

在前端网络社区中，使用 Bower、Grunt、Gulp 和 NPM 来管理软件包和运行构建任务，开发现代 Web 应用已经变得广泛流行。由于这个生态系统非常丰富并且得到了广泛接受，Visual Studio 2015 已经采用了这些系统来管理客户端框架和库，如图所示。NuGet 是管理服务器端软件包的理想选择:

![](img/image_05_012.png)

各种软件包管理工具

我们看到如何在 Visual Studio Code 中使用 NPM 管理客户端软件包。类似地，我们在 Visual Studio 2015 或更高版本中使用 NPM 来管理项目中的前端框架和库。让我们通过以下步骤使用 NPM 将 Angular 框架和其他所需的 JavaScript 库作为项目的依赖项添加到我们的项目中：

1.  首先，让我们向我们的项目添加 **`NPM 配置文件`**。右键单击项目节点，导航到 `Add` | `New Item`。从左侧窗格中选择 **`Web`** 下的 **`General`**，并且从中间窗格选择 **`NPM 配置文件`**。

然后，点击 **`Add`**，将默认名称保留为 `package.json`:

![](img/image_05_013.png)

名为 package.json 的 NPM 配置文件

`package.json` 文件将被添加到您的项目中，其默认 JSON 代码如下:

![](img/image_05_014.png)

package.json 的代码片段

1.  将 `name` 字段更新为 `my-todo` 并将所需的依赖项添加到 `package.json` 文件中，如图所示:

```ts
        "version": "1.0.0",   
        "name": "my-todo",   
        "private": true,   
        "dependencies":
        {   
          "@angular/common": "~4.0.0",   
          "@angular/compiler": "~4.0.0",   
          "@angular/core": "~4.0.0",   
          "@angular/forms": "~4.0.0",   
          "@angular/platform-browser": "~4.0.0",   
          "@angular/platform-browser-dynamic":   "~4.0.0",   

          "systemjs": "0.19.40",   
          "core-js": "².4.1",   
          "rxjs": "5.0.1",   
          "zone.js": "⁰.8.4"   
        },   
          "devDependencies": 
        {   
          "@types/node": "⁶.0.46",   
          "typescript": "~2.1.0"   
        }   
      }   
```

1.  当我们保存了带有所有依赖信息的 `package.json` 文件时，Visual Studio 将所需的软件包添加到我们的项目下的 `node_modules` 隐藏文件夹中，你可以通过导航到 `Dependencies` 节点下的 `npm` 文件夹来查看加载的依赖项列表，如下图所示：

![](img/image_05_015.png)

具有依赖库的 NPM 文件夹

我们的项目依赖节点中已经有了我们需要的所有客户端框架和库。但是，我们需要将依赖库添加到我们的 `wwwroot` 文件夹中，以便我们的应用程序引用和消耗。我们将在下一节中讨论这一点。

## 使用 Gulp 运行任务

* * *

Gulp 是一个在`node.js`上运行的任务运行器。使用 Gulp，我们可以自动化活动，如移动或复制文件，以及捆绑和最小化。在 ASP.NET Core 中，微软还将 Gulp 与 Visual Studio 集成在一起，因为它已被 Web 社区广泛接受，可以非常轻松地运行和管理复杂的任务。您可以访问官方网站了解更多信息：[`gulpjs.com/`](http://gulpjs.com/)

让我们使用 Gulp 将解决方案中隐藏的`node_modules`文件夹中所需的 JavaScript 框架和库推送到项目`wwwroot`下的`libs`文件夹中。在 Visual Studio 中安装 Gulp 很容易。执行以下步骤来安装和运行 Gulp 任务：

1.  在`package.json`的 NPM 配置文件中添加 Gulp 作为开发依赖项，如图所示，并保存文件：

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
            "@angular/platform-browser": "~4.0.0",   
            "@angular/platform-browser-dynamic":   
            "~4.0.0",   
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

当我们在`package.json`文件中添加了 Gulp 作为开发依赖项并保存时，Visual Studio 会将该包安装到我们的应用程序中的`node` Dependencies` | `npm`文件夹下，如下截图所示：

![](img/image_05_016.png)

添加的 npm 文件夹下的 Gulp 依赖项

我们的应用程序中有 Gulp 包。现在，我们需要在 JavaScript 中编写一个任务，从隐藏在解决方案中的`node_modules`文件夹中复制所需的 JavaScript 库，如下所示：

![](img/image_05_017.png)

某个`node_modules`隐藏文件夹

1.  现在，让我们将**`Gulp 配置文件`**添加到我们的项目中。右键单击项目，导航到`添加` | `新建项`。在左侧窗格中选择**`Web`**下的**`General`**，然后在中间窗格中选择**`Gulp 配置文件`**。然后，单击**`添加`**，保持默认名称为`gulpfile.js`：

![](img/image_05_018.png)

添加 Gulp 配置文件

以下是 Gulp 配置文件`gulpfile.js`的默认内容：

![](img/image_05_019.png)

Gulp 配置文件的默认代码片段

1.  让我们再写一个任务，将隐藏在解决方案中的`node_modules`文件夹中所需的 JavaScript 库复制到项目`wwwroot`节点下的`libs`文件夹中。将以下代码片段添加到新任务的`gulpfile.js`中：

```ts
      var paths = {   
          sourcePath: "./node_modules",   
          targetPath: "./wwwroot/libs"   
      }   
          var librariesToMove = [   
          paths.sourcePath + '/core-
          js/client/shim.min.js',   
          paths.sourcePath + '/zone.js/dist/zone.min.js',   
          paths.sourcePath +   
         '/systemjs/dist/system.src.js',   
      ];   
          var gulp = require('gulp');   
          gulp.task('librariesToMove',   function () {   
          return           
          gulp.src(librariesToMove).pipe      
          (gulp.dest(paths.targetPath));   
      });
```

`paths`变量保存要移动的库的源和目标文件夹，`librariesToMove`变量保存要移动到`libs`文件夹的库列表。文件中的最后一条语句是在运行时将所需的 JavaScript 库复制到`libs`文件夹的新任务。

1.  我们已经准备好了 Gulp 任务的代码，现在，我们需要运行 Gulp 任务来复制这些库。所以，要运行任务，右键单击`gulpfile.js`并打开**`任务运行器资源管理器`**，如下截图中所示：

![](img/image_05_020.png)

打开任务运行器资源管理器

任务运行器资源管理器将在**`Tasks`**下列出在`gulpfile.js`中编写的可用任务，如下截图所示：

![](img/image_05_021.png)

gulpfile.js 中可用的任务列表

1.  在 ****`Task Runner Explorer`**** 中右键点击列表中的 `librariesToMove` 任务，然后从菜单中选择 **`Run`**，如下所示：

![](img/image_05_022.png)

在 gulpfile.js 中运行 librariesToMove 任务

您可以在 **`Task Runner Explorer`** 的右侧窗格中看到执行该任务的命令：

![](img/image_05_023.png)

任务完成时没有错误

注意，库会被复制到 `wwwroot` 下的 `libs` 文件夹，如下截图所示：

![](img/image_05_024.png)

创建了包含所需 JavaScript 库的 libs 文件夹

1.  现在，我们已经在 `wwwroot` 节点下的 `libs` 文件夹中拥有了所需的库，请按照以下示例更新 `index.html`，向其中添加对 `libs` 文件夹中库的脚本引用以及配置 `SystemJS` 的代码：

```ts
      <!DOCTYPE html>   
      <html>   
      <head>   
          <title>My   Todo</title>   
          <script>document.write('<base   href="' + 
          document.location + 
          '" />');</script>   
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
          <my-app>Loading My Todo   App...</my-app>   
      </body>   
      </html>
```

1.  添加 `system.js` 配置文件 `systemjs.config.js`，并更新其中的以下内容。这包含了运行应用程序时加载 Angular 库的映射信息：

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
      '@angular/platform-browser': 'npm:@angular/platform-
      browser/bundles/platform-browser.umd.js',
      '@angular/platform-browser-dynamic': 
      'npm:@angular/platform-
      browser-dynamic/bundles/platform-browser-
      dynamic.umd.js',
      'rxjs': 'npm:rxjs'
      },
      packages: 
      {app: {
      main: './main.js',
      defaultExtension: 'js'
      },
      rxjs: {
      defaultExtension: 'js'
      }
      }
      });
      })(this);
```

我们创建了一个项目来开发`My Todo` 应用程序，并使用 NPM 包管理器管理所有客户端依赖项。我们还使用了 Gulp 运行一个任务，将 JavaScript 库复制到 `wwwroot` 节点。在下一节中，让我们为我们的应用程序创建所需的 Angular 组件。

## 添加 Angular 组件和模板

* * *

我们将使用 TypeScript 为我们的应用程序编写 Angular 组件。TypeScript 文件应该编译为 ECMAScript 5 目标的 JavaScript。

### 配置 TypeScript 编译器选项

我们需要告知 Visual Studio 编译 TypeScript 所需的编译器选项，以便在运行时消耗我们的应用程序。通过 TypeScript 配置文件，我们可以使用以下步骤配置编译器选项和其他详细信息：

1.  在项目上右键点击，然后导航到 `Add | New Item`，保持文件名默认，添加 **`TypeScript Configuration File`**，如下截图所示：

![](img/image_05_025.png)

添加 TypeScript 配置文件

将一个名为 `tsconfig.json` 的文件添加到项目根目录。

1.  用以下配置替换 TypeScript 配置文件的内容：

```ts
      {   
            "compilerOptions": 
            {   
            "diagnostics": true,   
            "emitDecoratorMetadata":   true,   
            "experimentalDecorators":   true,   
            "lib": ["es2015", "dom"],   
            "listFiles": true,   
            "module": "commonjs",   
            "moduleResolution": "node",   
            "noImplicitAny": true,   
            "outDir": "wwwroot",   
            "removeComments": false,   
            "rootDir": "wwwroot",   
            "sourceMap": true,   
            "suppressImplicitAnyIndexErrors":   true,   
            "target": "es5"   
            },   
            "exclude": [   
            "node_modules"   
          ]   
      }
```

### 添加 Angular 组件

我们已经配置了 TypeScript 编译器选项。现在，让我们为我们的应用程序添加一个 Angular 根组件。按照以下步骤操作：

1.  首先，通过右键点击 `wwwroot`，然后导航到 `Add | New Folder`，在 `wwwroot` 下创建一个 `app` 文件夹，如下截图所示：

![](img/image_05_026.png)

为 Angular 应用程序文件夹添加一个名为 app 的新文件夹

1.  我们已经准备好了`app`文件夹。让我们通过右键单击`app`文件夹并导航到`Add` | `New Item`来添加 TypeScript 文件，以创建一个根组件。从左侧面板下选择**`Web`**下的**`Scripts`**，并在中间面板选择**`TypeScript File`**。将文件命名为`app.component.ts`，然后单击**`Add`**：

![](img/image_05_027.png)

添加名为 app.component.ts 的根组件

1.  将以下代码片段添加到`app.component.ts`：

```ts
      import { Component } from '@angular/core';   
      @Component({   
          selector: 'my-app',   
          template: `<h1>Hello   {{name}}</h1>`   
      })   
      export class AppComponent { name   = 'My Todo App';  
      }
```

创建了一个名为`AppComponent`的根组件，并用组件元数据`selector`和`templateUrl`进行修饰。

### 添加应用程序模块

在前面的部分中，我们创建了一个名为`AppComponent`的 Angular 组件。现在我们需要引导这个`AppComponent`，这样 Angular 才会将其视为应用程序的根组件。我们可以通过在`AppModule`类上添加`NgModule`元数据并将其分配给`AppComponent`来引导一个组件。按照以下步骤创建`AppModule`：

1.  通过右键单击`app`文件夹并导航到`Add` | `New Item`来创建一个`TypeScript`文件。在左侧面板下选择**`Web`**下的**`Scripts`**，并在中间面板选择**`TypeScript File`**。添加一个名为`app.module.ts`的文件，然后单击**`Add`**：

![](img/image_05_028.png)

添加名为`app.module.ts`的 TypeScript 文件

1.  将以下代码片段添加到`app.module.ts`：

```ts
      import { NgModule } from '@angular/core';
      import { BrowserModule } from '@angular/platform-
      browser';
      import { FormsModule } from '@angular/forms';
      import { AppComponent } from './app.component';
      @NgModule({
      imports: [
      BrowserModule,
      FormsModule
      ],
      declarations: [AppComponent],
      bootstrap: [AppComponent]
      })
      export class AppModule { }
```

在这里，我们将`AppComponent`添加为根组件，并导入`BrowserModule`，因为我们的应用将通过浏览器消耗，还有`FormsModule`两个绑定。

### 添加一个 Angular 组件

现在我们需要引导前面部分中创建的`AppModule`。执行以下步骤：

1.  让我们创建一个`TypeScript`文件，`main.ts`，用于引导`AppModule`。在`wwwroot`文件夹上右键单击并导航到`Add` | `New Item`。从左侧面板下选择**`Web`**下的**`Scripts`**，并在中间面板选择**`TypeScript File`**。将文件命名为`main.ts`，然后单击**`Add`**：

![](img/image_05_029.png)

添加名为 main.ts 的 TypeScript 文件

1.  使用这段代码更新`main.ts`文件：

```ts
      import { platformBrowserDynamic }   from 
      '@angular/platform-
      browser-dynamic';   
      import { AppModule } from './app/app.module';   
      platformBrowserDynamic().bootstrapModule(AppModule);
```

在这里，平台浏览器动态包含使应用在浏览器中运行的 Angular 功能。如果我们的应用程序不是针对在浏览器上运行的话，可以忽略这一点。

我们已经准备好验证我们的 Angular 应用程序是否正常运行。请注意 Visual Studio 如何在解决方案资源管理器中整齐地组织了模板文件、TypeScript 文件以及它们各自的已编译 JavaScript 文件，如下面的屏幕截图所示：

![](img/image_05_030.png)

编译 TypeScript 文件到 JavaScript 文件

*请注意，Visual Studio 在对 app 文件夹中的 TypeScript 文件进行编译并进行更改并保存文件时，将自动生成 JavaScript 文件。*

1.  通过按下*F5*键来运行应用程序，如果成功构建，您将看到浏览器中的输出，如下面的截图所示：

![](img/image_05_031.png)

应用的输出

### 注意

请注意`<my-app>`标签的内部文本是使用`app.component.html`中的内容插入的。

### 添加 Todo 类

我们的应用处理`Todo`项目。因此，让我们创建一个名为`Todo`的类，并向其添加`title`和`completed`属性，如下所示：

```ts
export class Todo {   
    title: string;   
    completed: boolean;   
    constructor(title: string) {   
        this.title = title;   
        this.completed = false;   
    }   
    set isCompleted(value:   boolean) {   
        this.completed = value;   
    }   
}   
```

这个`Todo`类还有一个以`title`为参数的构造函数和一个设置`todo`项目为`completed`状态的方法。

### 添加一个 TodoService 类

```ts
todo.service.ts file:
```

```ts
import { Todo } from './todo'    
export class TodoService {   
    todos: Array<Todo>   
    constructor() {   
        this.todos = [new Todo('First   item'),   
        new Todo('Second item'),   
        new Todo('Third item')];   
    }   
    getPending() {   
        return   this.todos.filter((todo: Todo) => todo.completed === 
        false);   
    }   
    getCompleted() {   
        return   this.todos.filter((todo: Todo) => todo.completed === 
        true);   
    }   
    remove(todo: Todo) {   
          this.todos.splice(this.todos.indexOf(todo), 1);   
    }   

    add(title: string) {   
        this.todos.push(new   Todo(title));   
    }   
    toggleCompletion(todo: Todo)   {   
        todo.completed =   !todo.completed;   
    }   
    removeCompleted() {   
        this.todos =   this.getPending();   
    }   
}
```

我们创建了`TodoService`类，其中包含各种方法来添加、删除和返回`todo`项目的集合。

### 更新 AppComponent 类

现在我们已经拥有了`TodoService`类，让我们更新`AppComponent`类，如下所示，来消费`TodoService`类：

```ts
import { Component } from '@angular/core';   
import { Todo } from './todo'   
import { TodoService } from './todo.service'     
@Component({   
    selector: 'my-app',   
    templateUrl: './app/app.component.html'   
})   
export class AppComponent {   
    todos: Array<Todo>;   
    todoService: TodoService;   
    newTodoText = '';   
    constructor(todoService:   TodoService) {   
        this.todoService =   todoService;   
        this.todos =   todoService.todos;   
    }   
    removeCompleted() {   
        this.todoService.removeCompleted();   
    }   
    toggleCompletion(todo: Todo)   {   
          this.todoService.toggleCompletion(todo);   
    }   
    remove(todo: Todo) {   
          this.todoService.remove(todo);   
    }  
    addTodo() {   
        if   (this.newTodoText.trim().length) {   
              this.todoService.add(this.newTodoText);   
            this.newTodoText = '';   
        }   
    }   
}   
```

请注意`@Component`里的 metadata `template`已被替换为`templateUrl`，并且指定了一个`AppComponent`模板文件`app.component.html`。由于模板内容现在比较复杂，我们需要为`AppComponent`视图引入一个 HTML 文件。

### 更新 AppModule

```ts
app.module.ts file:
```

```ts
import { NgModule } from '@angular/core';   
import { BrowserModule } from '@angular/platform-browser';   
import { FormsModule } from '@angular/forms';   
import { AppComponent } from './app.component';   
import { TodoService } from './todo.service'   
@NgModule({   
    imports: [   
        BrowserModule,   
        FormsModule   
    ],   
    declarations: [AppComponent],   
    providers: [TodoService],   
    bootstrap: [AppComponent]   
})   
export class AppModule { }
```

### 添加 AppComponent 模板

```ts
AppComponent with all the mentioned features:
```

```ts
<section>   
    <header>   
          <h1>todos</h1>   
        <input placeholder="Add   new todo" autofocus="" [(ngModel)]="newTodoText">   
        <button type="button"   (click)="addTodo()">Add</button>   
    </header>   
    <section *ngIf="todoService.todos.length   > 0">   
        <ul>   
            <li *ngFor="let   todo of todoService.todos">   
                <input type="checkbox"   (click)="toggleCompletion(todo)" [checked]="todo.completed">   
                  <label>{{todo.title}}</label>   
                <button   (click)="remove(todo)">X</button>   
            </li>   
        </ul>   
    </section>   
    <footer *ngIf="todoService.todos.length   > 0">   
          <span><strong>{{todoService.getPending().length}}</strong>   {{todoService.getPending().length == 1 ? 'item' : 'items'}} left</span>   
        <button *ngIf="todoService.getCompleted().length   > 0" (click)="removeCompleted()">Clear completed</button>   
    </footer>   
</section>   
```

如你所见，我们使用`ngModel`在输入控件上应用了双向绑定来绑定新的`todo`项目`title`。我们将`addTodo`方法分配给`Add`按钮的点击事件，以将新的`todo`项目添加到`todoService`中的内存中的`Todo`项目集合。我们对`<li>`标签应用了`ngFor`来迭代`todoService`中的每个`Todo`项目。为每个`Todo`项目呈现的复选框有其自己的`click`事件，`checked`属性与`toggleCompletion`方法，以及`Todo`项目的`completed`属性分别映射。接下来，删除按钮将其`click`事件映射为`AppComponent`中的`remove`方法。

footer 标签有一个显示待办`todo`项目数量的 span，以及一个按钮来从列表中删除已完成的`todo`项目。该按钮的`click`事件与`AppComponent`中的`removeCompleted`方法相映射。

通过按下*F5*键来运行应用程序，您将能够执行所有操作，如添加，删除和列举`todo`项目：

![](img/image_05_032.png)

我的 Todo 应用操作

## 总结

* * *

哇！你实际上在本章中学到了这本书非常重要且核心的目标。是的！我在谈论将 Angular 与.NET 应用程序集成。

我们通过创建一个新的 ASP.NET Core 空应用程序开始了这一章,并更新了`Startup`类以服务静态页面和默认页面。然后,您学习了如何在 Visual Studio 中使用 NPM 管理客户端包,我们还设法使用 Visual Studio 中的 Gulp 自动化和运行任务。接下来,您学习了如何添加应用程序所需的组件并将其引导。后来,我们设计了一个模型类和一个服务类来处理应用程序的核心业务逻辑。最后,我们设计了一个模板来列出`Todo`项目,并添加了一些控件并将它们挂钩到`TodoService`和`AppComponent`中的某些方法。

这个应用程序只处理内存中的待办事项。然而，在实时应用程序中,我们会使用一个服务来添加、删除或获取`todo`项目。在下一章中,我们将讨论如何创建一个 ASP.NET Core Web API 服务来处理检索、保存和删除`todo`项目,并从我们刚刚构建的 Angular 应用程序中使用它。
