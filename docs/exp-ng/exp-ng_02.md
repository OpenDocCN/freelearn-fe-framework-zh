# 第二章：将 AngularJS 应用程序迁移到 Angular 应用程序

我们都知道 Angular 有很多改进，并且是从头开始设计的。因此，Angular 开发人员中最令人困扰的问题之一是如何将现有的 AngularJS 应用程序迁移到 Angular。在本章中，我们将讨论成功迁移现有 AngularJS 应用程序所推荐的最佳实践、方法和工具。

在本章中，我们将涵盖以下主题：

+   迁移过程

+   语法差异

+   升级到 Angular 的好处

+   升级到 Angular 的规则

+   使用 UpgradeAdapter 进行增量升级

+   组件迁移

+   从 AngularJS 到 Angular 的路线图

# 迁移过程

AngularJS 和 Angular 在语法和概念上有所不同。因此，迁移过程不仅涉及在语法层面上的代码更改，还涉及实现层面的更改。Angular 团队通过在 Angular 中提供内置工具，使开发人员更容易将 AngularJS 应用程序迁移到 Angular。在开始迁移过程之前，我们的现有 AngularJS 应用程序中有一些初步过程要做。

初步过程涉及解耦现有代码并使现有代码可维护。这个初步过程不仅为升级代码做好准备，还将改善现有的 AngularJS 应用程序的性能。

我们可以通过在同一个应用程序中同时运行 AngularJS 和 Angular，并逐个启动迁移过程，从组件开始逐步迁移。这种方法有助于迁移大型应用程序，将业务与任何影响隔离开，并在一段时间内完成升级。这种方法可以使用 Angular 升级模块实现。

# Angular 和 AngularJS 之间的语法差异

Angular 在许多方面与 AngularJS 的语法不同。让我们在这里看一些。

# 模板中的本地变量和绑定

模板是处理应用程序的 UI 部分的视图，使用 HTML 编写。首先，我们将看到单向数据绑定的语法差异。

AngularJS：

```ts
<h1>Book Details:</h1> 
<p>{{vm.bookName}}</p> 
<p>{{vm.authorName}}</p> 

```

Angular：

```ts
<h1>Book Details:</h1> 
<p>{{bookName}}</p> 
<p>{{authorName}}</p> 

```

这两个代码片段都显示了单向数据绑定，将书籍和作者名称绑定到 UI，使用双大括号。然而，AngularJS 在引用控制器的属性以绑定到模板时会加上控制器的别名前缀，而 Angular 不会使用别名前缀，因为视图或模板默认与组件关联。 

# 模板中的过滤器和管道

AngularJS 过滤器现在在 Angular 中被称为管道。在 AngularJS 中，过滤器在管道字符（`|`）之后使用，在 Angular 中没有语法上的变化。然而，Angular 将过滤器称为管道。

AngularJS：

```ts
<h1>Book Details:</h1> 
<p>{{vm.bookName}}</p> 
<p>{{vm.releaseDate | date }}</p> 

```

Angular：

```ts
<h1>Book Details:</h1> 
<p>{{bookName}}</p> 
<p>{{releaseDate | date }}</p> 

```

请注意，我们已经将日期管道或过滤器应用于`releaseDate`，在 AngularJS 和 Angular 之间没有语法上的变化。

# 模板中的本地变量

让我们看看在 AngularJS 和 Angular 中分别使用本地变量在`ng-repeat`和`ngFor`中的示例。

AngularJS：

```ts
<tr ng-repeat="book in vm.books"> 
  <td>{{book.name}}</td> 
</tr> 

```

Angular：

```ts
<tr *ngFor="let book of books"> 
  <td>{{book.name}}</td> 
</tr> 

```

请注意，在 AngularJS 中，本地变量 book 是隐式声明的，在 Angular 中，使用 let 关键字来定义本地变量 book。

# Angular 应用程序指令

AngularJS 允许使用`ng-app`指令声明性地引导应用程序。但是，Angular 不支持声明性引导。它只支持通过调用引导函数并传递应用程序的根组件来显式引导应用程序。

AngularJS：

```ts
<body ng-app="packtPub"> 

```

Angular：

```ts
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic'; 
import { AppModule } from './app/app.module'; 
platformBrowserDynamic().bootstrapModule(AppModule); 

```

请注意，在 AngularJS 中，Angular 模块名称`packtPub`已分配给`ng-app`指令。然而，在 Angular 中，根据执行环境，我们将`AppModule`传递给引导模块。请注意，`AppModule`是`NgModule`类，它是我们刚刚根据执行环境引导的应用程序的根模块。

# 处理 CSS 类

AngularJS 提供了`ng-class`指令来包含或排除 CSS 类。同样，Angular 有`ngClass`指令根据表达式应用或移除 CSS 类。类绑定是 Angular 提供的另一个更好的选项，用于添加或移除 CSS 类。

AngularJS：

```ts
<div ng-class="{active: isActive}"> 

```

Angular：

```ts
<div [ngClass]="{active: isActive}"> 
<div [class.active]="isActive"> 

```

请注意，在 Angular 中将类绑定应用于第二个`div`。

# 绑定点击事件

AngularJS 提供了基于事件的指令`ng-click`，可以将`click`事件绑定到关联控制器中的方法。Angular 通过使用可以使用`( )`语法来定位本机 DOM 元素来实现相同的功能，并通过将单向数据绑定与`event`绑定相结合来实现这一点。

AngularJS：

```ts
<button ng-click="vm.showBook()"> 
<button ng-click="vm.showBook($event)"> 

```

Angular：

```ts
<button (click)="showBook()"> 
<button (click)="showBook($event)"> 

```

请注意，在 Angular 中，目标事件 click 是在括号内定义的，并且组件中的方法在引号中指定。

# 模板中的控制器和组件

AngularJS 提供`ng-controller`指令来将控制器附加到视图，并将视图与该视图相关的控制器绑定。Angular 不支持控制器和`ng-controller`指令将控制器与视图关联。组件同意其关联的视图或模板，而不是反过来。

AngularJS：

```ts
<div ng-controller="PacktBooksCtrl as vm"> 

```

Angular：

```ts
@Component({ 
  selector: 'packt-books', 
  templateUrl:'app/packtbooks.component.html' 
})

```

在 AngularJS 中，我们使用**立即调用的函数表达式**（**IIFE**）来定义控制器。在 Angular 中，我们使用装饰有`@Component`的 TypeScript 类来定义组件，提供元数据，如`selector`、`templateUrl`等。

AngularJS：

```ts
(function () { 
  ... 
}()); 

```

Angular：

```ts
@Component({ 
  selector: 'packt-books', 
  templateUrl:'app/packtbooks.component.html' 
}) 
export class PacktBooks { 
} 

```

# 升级到 Angular 的好处

让我们看看升级到 Angular 的一些好处：

+   更好的性能：Angular 支持更快的变更检测、更快的引导时间、视图缓存、模板预编译等。

+   服务器端渲染：Angular 已经分成了一个应用层和一个渲染层。这使我们能够在 Web 工作者或者除了浏览器之外的服务器上运行 Angular。

+   更强大的模板：Angular 引入了新的模板语法，去除了许多指令，并与 Web 组件和其他元素更好地集成。

+   更好的生态系统：Angular 生态系统将来会变得更好，更有趣。

# 升级到 Angular 的策略

有不同的升级策略可用于迁移到 Angular。它们如下：

+   一次性：替换整个 AngularJS 应用程序，从一个点开始重写代码为 Angular。

+   增量：逐个服务或组件升级现有应用程序，同时运行 AngularJS 和 Angular。

如果 AngularJS 应用程序很小，那么一次性重写可能是升级的最简单和最快的方式。如果 AngularJS 应用程序较大，无法一次性重写整个代码，我们需要逐步重写，逐个组件，逐个服务。这被称为增量升级。然而，同时运行`ng1`和`ng2`会对性能产生影响。

# 增量升级到 Angular 的规则

如果我们遵循以下一套规则，逐步升级将会更容易：

+   每个文件实现一个组件；这有助于隔离组件并逐个迁移它们。

+   应用模块化编程并按功能排列文件夹；这将使开发人员能够集中精力逐步迁移一个功能。

+   使用模块加载器；遵循前面的规则，您将在项目中得到大量的文件。这会带来组织文件和在 HTML 页面中正确引用它们的麻烦。当您使用诸如`SystemJS`、`Webpack`或`Browserify`之类的模块加载器时，它使我们能够使用 TypeScript 内置的模块系统。这使开发人员能够明确地导入或导出功能，并在应用程序的各个部分之间共享它们的代码中使用。

+   首先安装 TypeScript；在开始实际升级过程之前，最好先引入 TypeScript 编译器。这可以通过简单的安装 TypeScript 编译器来实现。

+   使用组件指令；最好使用组件指令而不是在 AngularJS 应用程序中使用`ng-controller`和`ng-include`，这样在 Angular 中迁移组件指令将比迁移控制器更容易。

# 使用 UpgradeAdapter 进行增量升级

可以使用`UpgradeAdapter`无缝进行增量升级。`UpgradeAdapter`是一个可以引导和管理同时支持 Angular 和 AngularJS 代码的混合应用程序的服务。`UpgradeAdapter`使您能够同时运行 AngularJS 和 Angular 代码。`UpgradeAdapter`促进了从一个框架到另一个框架的组件和服务之间的互操作性。`UpgradeAdapter`将负责依赖注入、DOM 和变更检测的互操作性。

# 将 AngularJS 依赖注入到 Angular 中

我们可能会遇到这样的情况，即将 AngularJS 服务上的业务逻辑或任何内置服务（如`$location`或`$timeout`）注入到 Angular 代码中。这可以通过将 AngularJS 提供者升级到 Angular 并在需要的地方将其注入到 Angular 代码中来处理。

# 将 Angular 依赖注入到 AngularJS 中

有时可能需要将 Angular 依赖项降级，以便在 AngularJS 代码中使用它们。当我们需要将现有服务迁移到 Angular 或在 Angular 中创建新服务时，这是必要的，因为这些服务在 AngularJS 中编写的组件依赖于它们。

# 组件迁移

将 AngularJS 应用程序设计为以组件为中心的做法比以控制器为中心的设计更好。如果您按照这种做法开发了应用程序，那么迁移将会更容易。AngularJS 中的组件指令将具有与 Angular 组件类似的模板、控制器和绑定。但请确保您的 AngularJS 应用程序组件指令没有使用 compile、replace、priority 和 terminal 等属性。如果您的应用程序实现了具有这些属性的组件指令，那么它就不符合 Angular 架构。如果您的 AngularJS 应用程序是使用 AngularJS 1.5 开发的，并且组件是使用组件 API 实现的，那么您可能已经注意到了与 Angular 组件的相似之处。

# 从 AngularJS 到 Angular 的路线图

在将 AngularJS 迁移到 Angular 的过程中，遵循这个路线图是很好的：

+   JavaScript 转换为 TypeScript

+   安装 Angular 包

+   创建 AppModule

+   引导您的应用程序

+   升级您的应用程序服务

+   升级您的应用程序组件

+   添加 Angular 路由器

让我们在以下部分详细讨论它们。

# JavaScript 转换为 TypeScript

通过引入 TypeScript 开始迁移过程，因为您将在 Angular 中使用 TypeScript 编写代码。将 TypeScript 安装到您的 Angular 应用程序中非常容易。运行以下命令，从`npm`安装 TypeScript 到您的应用程序，并将包信息保存到`package.json`中：

```ts
npm i typescript --save-dev

```

注意：由于 Angular 包仅在 npm 上可用，我们将从 npm 安装任何新包，并逐渐淘汰 Bower 包管理器

我们还需要配置 TypeScript，指示它将 TypeScript 代码转译为`tsconfig.json`文件中的 ES5 代码。

最后，我们需要在`package.json`的 scripts 部分下添加以下命令，以在后台以监视模式运行 TypeScript 编译器，这样当您进行更改时，代码将被重新编译：

```ts
"script": {   
  "tsc": "tsc",   
  "tsc:w": "tsc -w",   
}   

```

# 安装 Angular 包

我们需要安装 Angular 以及`SystemJS`模块加载器。最快的方法是从 GitHub 克隆`quickstart`应用程序到您的开发系统。然后将与 Angular 相关的依赖项从`package.json`复制到您的应用程序`package.json`中，并将`SystemJS`配置文件`systemjs.config.js`复制到您的应用程序根目录。完成所有这些后，然后运行以下命令来安装我们刚刚在`package.json`中添加的软件包：

```ts
npm install

```

将以下语句添加到`index.html`文件中。这将帮助相对 URL 从`app`文件夹中提供服务。这很重要，因为我们需要将`index.html`文件从`app`文件夹移动到应用程序的`root`文件夹中：

```ts
<base href="/app/">   

```

现在，让我们添加 JavaScript 文件引用并通过`SystemJS`加载 Angular。最后，使用`System.import`语句加载实际应用程序：

```ts
<script src="/node_modules/core-js/client/shim.min.js"></script>   
<script src="/node_modules/zone.js/dist/zone.js"></script>   
<script src="/node_modules/systemjs/dist/system.src.js"></script>   
<script src="/systemjs.config.js"></script>   
<script>   
  System.import('/app');   
</script>   

```

# 创建 AppModule

我们需要为您的应用程序创建一个`AppModule`。以下`AppModule`类定义了最小的`NgModule`：

```ts
import { NgModule } from '@angular/core';   
import { BrowserModule } from '@angular/platform-browser';   

@NgModule({   
  imports: [   
    BrowserModule,   
  ],   
})   
export class AppModule {   
}

```

在这里，我们只是从`@angular/core`导入了一个`NgModule`和从`@angular/platform-browser`导入了`BrowserModule`。任何简单的基于浏览器的 Angular 应用程序都会有这样一个简单的`AppModule`。

# 引导您的应用程序

通过将`ng-app`指令附加到`<html>`元素来引导 AngularJS 应用程序。这在 Angular 中将不再起作用，因为引导 Angular 应用程序是不同的。

通过运行以下命令安装 Angular 升级包，并将映射添加到`system.config.js`：

```ts
npm install @angular/upgrade --save

```

该语句还会更新`package.json`，引用了`@angular/upgrade`。更新后的`systemjs.config.js`如下所示：

```ts
System.config({   
    paths: {   
      'npm:': '/node_modules/'   
    },   
    map: {   
      'ng-loader': '../src/systemjs-angular-loader.js',   
      app: '/app',   
      '@angular/upgrade/static': 'npm:@angular/upgrade/bundles/upgrade-static.umd.js'   
    }   
})   

```

现在从`index.html`文件中的`<html>`元素中删除`ng-app`属性。然后我们需要将`UpgradeModule`导入到`AppModule`中。为了以 Angular 方式引导我们的 AngularJS 应用程序，我们需要在`AppModule`中重写`ngDoBootstrap`函数如下：

```ts
import { UpgradeModule } from '@angular/upgrade/static';   

@NgModule({   
  imports: [   
    BrowserModule,   
    UpgradeModule,   
  ],   
})   
export class AppModule {   
  constructor(private upgrade:   UpgradeModule) { }   
  ngDoBootstrap() {   
      this.upgrade.bootstrap(document.documentElement, [yourApp']);   
  }   
}   

```

最后，我们需要在`main.ts`中引导`AppModule`，该文件在`system.config.js`中配置为应用程序的入口点。`main.ts`的代码片段如下所示：

```ts
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';   
import { AppModule } from './app.module';      
platformBrowserDynamic().bootstrapModule(AppModule);   

```

# 升级应用程序服务

在 Angular 应用程序中，服务主要用于在整个应用程序中提供数据，并且这些数据将从任何服务中获取。在 AngularJS 中，我们一直在使用`ngResource`和`%http`来与服务通信和处理数据。

作为迁移的一部分，我们需要在我们使用`ngResource`和`$http`的地方使用 Angular HTTP 模块。要使用 Angular HTTP 模块，我们首先需要导入`HttpModule`并将其添加到`AppModule`的`NgModule`指令的导入数组中，如下所示：

```ts
import { HttpModule } from '@angular/http';   

@NgModule({   
  imports: [   
    BrowserModule,   
    UpgradeModule,   
    HttpModule,   
  ],   
})   
export class AppModule {   
  constructor(private upgrade:   UpgradeModule) { }   
  ngDoBootstrap() {   
      this.upgrade.bootstrap(document.documentElement, ['yourApp']);   
  }   
}   

```

接下来，用装饰有`@Injectable`指令的新 TypeScript 类替换应用程序中基于`ngResource`或`$http`的服务的代码片段，如下所示：

```ts
@Injectable()   
export class BookService {   
/* . . . */   
}   

```

装饰器`@Injectable`将向`BookService`类添加特定于依赖注入的元数据，以便 Angular 知道哪些类已准备好进行依赖注入。我们需要将 HTTP 服务注入到`BookService`的构造函数中，并且注入的 HTTP 服务将用于访问`books.json`中的数据以获取书籍列表，如下所示：

```ts
@Injectable()   
export class BookService {   
  constructor(private http: Http) { }   
  books(): Observable<Book[]> {   
    return   this.http.get(`data/books.json`)   
      .map((res: Response) =>   res.json());   
  }   
}   

```

以下是可以作为书籍模型类型的`Book`接口：

```ts
export interface PhoneData {   
  title: string;   
  author: string;   
  publication: string;   
}   

```

这个 Angular 服务与 AngularJS 不兼容，不能直接注入。因此，我们需要将`injectable`方法降级以将我们的`BookService`插入到 AngularJS 代码中。为此，我们需要在`@angular/upgrade/static`中使用一个名为`downgradeInjectable`的方法：

```ts
declare var angular:   angular.IAngularStatic;   
import { downgradeInjectable } from '@angular/upgrade/static';   

@Injectable()   
export class BookService {   
}   

angular.module('core.lib')   
  .factory('core.lib',   downgradeInjectable(BookService));   

```

`BookService`的完整代码片段如下所示：

```ts
import { Injectable } from '@angular/core';   
import { Http, Response } from '@angular/http';   
import { Observable } from 'rxjs/Rx';   

declare var angular:   angular.IAngularStatic;   
import { downgradeInjectable } from '@angular/upgrade/static';   

import 'rxjs/add/operator/map';   

export interface Book {   
  title: string;   
  author: string;   
  publication: string;   
}   

@Injectable()   
export class BookService {   
  constructor(private http: Http) { }   
  books(): Observable<Book[]> {   
    return   this.http.get(`data/books.json`)   
      .map((res: Response) =>   res.json());   
  }   
}   

angular.module('core.lib')   
  .factory('phone',   downgradeInjectable(BookService));   

```

最后，我们需要在`NgModule`下注册`BookService`作为提供者，以便 Angular 将`BookService`的实例保持准备好在整个应用程序中注入。`app.module.ts`的更新代码片段如下所示：

```ts
import { BookService } from './book.service';   

@NgModule({   
  imports: [   
    BrowserModule,   
    UpgradeModule,   
    HttpModule,   
  ],   
  providers: [   
    BookService,   
  ]   
})   
export class AppModule {   
  constructor(private upgrade:   UpgradeModule) { }   
  ngDoBootstrap() {   
    this.upgrade.bootstrap(document.documentElement,   [yourApp']);   
  }   
}   

```

# 升级你的应用程序组件

作为升级组件的一部分，我们需要创建一个降级的 Angular 组件，以便它可以被 AngularJS 代码消耗。以下是降级的 Angular 组件的代码片段：

```ts
declare var angular:   angular.IAngularStatic;   
import { downgradeComponent } from '@angular/upgrade/static';   

@Component({   
  selector: 'book-list',   
  templateUrl: './book-list.template.html'   
})   
export class BookListComponent {   
}   

angular.module('bookList')   
  .directive(   
    'bookList',   
    downgradeComponent({component:   BookListComponent}) as 
           angular.IDirectiveFactory   
);   

```

在这里，我们向 TypeScript 编译器指示`directive`工厂是从`downgradeComponent`返回的。现在我们需要通过将其添加到`AppModule`的`entryComponents`来注册`downgradeComponent`，如下所示：

```ts
import { BookListComponent } from './components/book-list.component';   

@NgModule({   
  imports: [   
    BrowserModule,   
    UpgradeModule,   
    HttpModule   
  ],   
  declarations: [   
    BookListComponent,   
  ],   
  entryComponents: [   
    BookListComponent,   
})   
export class AppModule {   
  constructor(private upgrade:   UpgradeModule) { }   
  ngDoBootstrap() {   
      this.upgrade.bootstrap(document.documentElement, ['yourApp']);   
  }   
}   

```

`phone-list.template.html`的更新模板如下所示：

```ts
<ul>   
    <li *ngFor="let book of books">   
      {{book.title}}   
    </li>   
  </ul>   

```

这里`ng-repeats`已被替换为`*ngFor`。

# 添加 Angular 路由器

Angular 已经完全重新定义了路由器。逐模块升级路由器模块是一个好的做法。Angular 有一个特殊的标签`<router-outlet>`，用于显示或加载路由视图。这应该在根组件的模板中。所以对于你的应用程序，我们需要创建一个名为`AppComponent`的根组件：

```ts
import { Component } from '@angular/core';   

@Component({   
  selector: 'your-app',   
  template: '<router-outlet></router-outlet>'   
})   
export class AppComponent { }   

```

这是一个指令，如果在网页中找到`<your-app>`，就将根组件加载到其中。因此，让我们用应用程序元素`<your-app>`替换`index.html`中的`ng-view`指令：

```ts
<body>   
  <your-app></your-app>   
</body>   

```

我们需要为路由创建另一个`NgModule`，代码片段如下所示：

```ts
import { NgModule } from '@angular/core';   
import { Routes, RouterModule } from '@angular/router';   
import { HashLocationStrategy,   LocationStrategy } from '@angular/common';   

import { BookListComponent }   from './components/book-list.component';   

const routes: Routes = [   
  { path: '', redirectTo: 'books',   pathMatch: 'full' },   
  { path: 'books',          component:   BookListComponent }   
];   

@NgModule({   
  imports: [ RouterModule.forRoot(routes)   ],   
  exports: [ RouterModule ],   
  providers: [   
    { provide: LocationStrategy,   useClass: HashLocationStrategy },   
  ]   
})   
export class AppRoutingModule { }   

```

在路由对象中定义了单个路由，还为应用程序的空路径或根路径设置了默认路由。然后，我们将路由对象传递给`RouterModule.forRoot`，以便`RouterModule`来处理它。我们使用`HashLocationStrategy`来指示`RouterModule`在 URL 的片段中使用一个哈希（`#`）。

最后，让我们更新`AppModule`来导入`AppRoutingModule`，并且我们已经到了一个阶段，可以移除`ngDoBootstrap`，因为现在一切都是 Angular。以下是`AppModule`的更新代码片段：

```ts
import { NgModule } from '@angular/core';   
import { BrowserModule } from '@angular/platform-browser';   
import { HttpModule } from '@angular/http';   
import { AppRoutingModule } from './app-routing.module';   
import { AppComponent }  from './app.component';   
import { BookService }   from './services/book.service';   
import { BookListComponent }  from './components/book-list.component';   

@NgModule({   
  imports: [   
    BrowserModule,   
    HttpModule,   
    AppRoutingModule   
  ],   
  declarations: [   
    AppComponent,   
    BookListComponent   
  ],   
  providers: [   
    BookService   
  ],   
  bootstrap: [ AppComponent ]   
})   
export class AppModule {}   

```

请注意，我们将`AppRoutingModule`添加到`NgModule`属性的导入集合中，以便应用程序路由将在`AppModule`中注册。

# 总结

干得好！好多东西，不是吗？！我们开始学习在 Angular 中进行迁移。

然后，我们看到了将 AngularJS 迁移到 Angular 应用程序的各种方法和最佳实践。

接下来，我们讨论了使用升级适配器进行增量升级。

最后，我们详细了解了从 AngularJS 迁移到 Angular 的路线图。

在下一章中，我们将讨论 Angular CLI，这是 Angular 的命令行界面。
