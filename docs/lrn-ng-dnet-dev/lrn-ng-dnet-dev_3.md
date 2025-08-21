## 第三章：Angular 构建模块-第二部分

本章将详细介绍 Angular 架构中尚未涵盖的核心构建模块。 在本章中，我们将涵盖以下主题：

+   表单

+   管道

+   路由

+   服务

+   观察者

## 表单

* * *

每个应用程序都有一个数据输入点，它使最终用户能够输入数据。表单旨在向服务器和页面插入或更新输入数据。在提交以进行进一步操作之前，应验证输入数据。应用了两种类型的验证方法：客户端验证和服务器端验证：

+   **服务器端验证**：服务器端验证将由服务器处理。 收到的信息将由服务器处理和验证。 如果提交表单时存在任何错误，则需要使用适当的信息更新 UI。 如果信息无效或不足，则将适当的响应发送回客户端。 这种验证方法更加安全，因为即使浏览器中关闭了 JavaScript，它也可以工作，并且恶意用户无法绕过服务器端验证。 但是，这种方法的缺点是只有在将表单提交到服务器后才会验证表单。 因此，用户必须等到完全提交表单到服务器，才能知道所提供的所有数据是否有效。

+   **客户端验证**：虽然服务器端验证更加安全，但它不会提供更好的用户体验。 使用脚本语言，如 JavaScript，实现客户端验证，并在客户端上进行验证。 用户输入的数据可以在用户输入时验证。 这会通过在屏幕上提供验证错误的即时响应，提供更丰富的体验。 用户无需等待整个表单提交，即可知道输入的数据是否有效。

Angular 具有 FormBuilder、Control 和 Validators 等类来处理表单。 它使您能够使用 Control 和 Validators 轻松设置验证规则。

### 表单工具

Angular 有各种工具可实现应用程序中的表单。 以下是这些工具及其各自的目的：

+   **控件**：这些通过封装表单的输入提供对象

+   **验证器**：这些有助于验证表单中的输入数据

+   **观察者**：这些有助于跟踪表单中的更改并通知用户任何验证错误

### Angular 形式的类型

Angular 提供了两种处理表单的方法：模板驱动表单和模型驱动表单。

#### 模板驱动表单

AngularJS 使用`ng-model`指令处理表单，并利用了使开发人员生活更轻松的双向绑定功能。 Angular 使开发人员能够使用`ngModel`构建模板驱动表单，这类似于 AngularJS 中的`ng-model`。

以下是模板驱动表单的实现：

1.  让我们在 **Visual Studio Code**（**VS Code**）中创建一个名为 First Template Form 的应用程序。

1.  在 `package.json` 中添加所需的包和依赖详情，并使用 `npm` install 命令进行安装。

```ts
      {
      "name":"first-template-form",
      "version":"1.0.0",
      "private":true,
      "description":"First template form",
      "scripts":{
      "test:once":"karma start karma.conf.js --single-
       run",
      "build":"tsc -p src/",
      "serve":"lite-server -c=bs-config.json",
      "prestart":"npm run build",
      "start":"concurrently \"npm run build:watch\" \"npm  
       run serve\"",
      "pretest":"npm run build",
      "test":"concurrently \"npm run build:watch\" \"karma 
       start 
      karma.conf.js\"",
      "pretest:once":"npm run build",
      "build:watch":"tsc -p src/ -w",
      "build:upgrade":"tsc",
      "serve:upgrade":"http-server",
      "build:aot":"ngc -p tsconfig-aot.json && rollup -c  
       rollup-
      config.js",
      "serve:aot":"lite-server -c bs-config.aot.json",
      "build:babel":"babel src -d src --extensions 
      \".es6\" --source-
      maps",
      "copy-dist-files":"node ./copy-dist-files.js",
      "i18n":"ng-xi18n",
      "lint":"tslint ./src/**/*.ts -t verbose"
      },
      "keywords":[
      ],
      "author":"",
      "license":"MIT",
      "dependencies":{
      "@angular/common":"~4.0.0",
      "@angular/compiler":"~4.0.0",
      "@angular/compiler-cli":"~4.0.0",
      "@angular/core":"~4.0.0",
      "@angular/forms":"~4.0.0",
      "@angular/http":"~4.0.0",
      "@angular/platform-browser":"~4.0.0",
      "@angular/platform-browser-dynamic":"~4.0.0",
      "@angular/platform-server":"~4.0.0",
      "@angular/router":"~4.0.0",
      "@angular/tsc-wrapped":"~4.0.0",
      "@angular/upgrade":"~4.0.0",
      "angular-in-memory-web-api":"~0.3.1",
      "core-js":"².4.1",
      "rxjs":"5.0.1",
      "systemjs":"0.19.39",
      "zone.js":"⁰.8.4"
      },
      "devDependencies":{
      "@types/angular":"¹.5.16",
      "@types/angular-animate":"¹.5.5",
      "@types/angular-cookies":"¹.4.2",
      "@types/angular-mocks":"¹.5.5",
      "@types/angular-resource":"¹.5.6",
      "@types/angular-route":"¹.3.2",
      "@types/angular-sanitize":"¹.3.3",
      "@types/jasmine":"2.5.36",
      "@types/node":"⁶.0.45",
      "babel-cli":"⁶.16.0",
      "babel-preset-angular2":"⁰.0.2",
      "babel-preset-es2015":"⁶.16.0",
      "canonical-path":"0.0.2",
      "concurrently":"³.0.0",
      "http-server":"⁰.9.0",
      "jasmine":"~2.4.1",
      "jasmine-core":"~2.4.1",
      "karma":"¹.3.0",
      "karma-chrome-launcher":"².0.0",
      "karma-cli":"¹.0.1",
      "karma-jasmine":"¹.0.2",
      "karma-jasmine-html-reporter":"⁰.2.2",
      "karma-phantomjs-launcher":"¹.0.2",
      "lite-server":"².2.2",
      "lodash":"⁴.16.2",
      "phantomjs-prebuilt":"².1.7",
      "protractor":"~4.0.14",
      "rollup":"⁰.41.6",
      "rollup-plugin-commonjs":"⁸.0.2",
      "rollup-plugin-node-resolve":"2.0.0",
      "rollup-plugin-uglify":"¹.0.1",
      "source-map-explorer":"¹.3.2",
      "tslint":"³.15.1",
      "typescript":"~2.2.0"
      },
      "repository":{
      }
      }
```

1.  创建一个书籍类，并添加以下代码片段：

```ts
      export class Book {
      constructor(
      public id: number,
      public name: string,
      public author: string,
      public publication?: string
      ) { }
      }
```

1.  创建 `AppComponent`，并添加以下代码：

```ts
      import { Component } from '@angular/core';
      @Component({
      selector: 'first-template-form',
      template: '<book-form></book-form>'
      })
      export class AppComponent { }
```

这里展示的 `AppComponent` 是应用程序的根组件，将托管 `BookFormComponent`。`AppComponent` 被装饰为第一个模板表单选择器，模板中包含带有`<book-form/>`特殊标签的内联 HTML。这个标签在运行时将被更新为实际模板。

1.  现在，让我们使用以下代码片段向 `book-form.component.ts` 中添加代码：

```ts
      import { Component } from '@angular/core';
      import { Book } from './book';
      @Component({selector: 'book-form',
      templateUrl: './book-form.component.html'
      })
      export class BookFormComponent {
      model = new Book(1, 'book name','author 
      name','publication name 
      is optional');
      onSubmit() {
      // code to post the data
      }
      newBook() {
      this.model = new Book(0,'','','');
      }
      }
```

在这里，注意到我们从 `book.ts` 中导入了 Book。Book 是该表单的数据模型。`BookFormComponent` 被装饰为 `@Component` 指令，该指令从 `@angular/core` 中引入。选择器值设置为 `book-form`，templateUrl 被分配为模板 HTML 文件。在 `BookFormCompoent` 中，我们用虚拟数据初始化了 Book 模型。我们有两个方法--`onSubmit()` 和 `newBook()`--一个用于向 API 提交数据，另一个用于清空表单。

1.  现在，让我们向以下 HTML 内容中添加 `book-form.component.html` 模板文件：

```ts
      <div class="container">
      <h1>New Book Form</h1>
      <form (ngSubmit)="onSubmit()" #bookForm="ngForm">
      <div class="form-group">
      <label for="name">Name</label>
      <input type="text" class="form-control" id="name"
      required
      [(ngModel)]="model.name" name="name"
      #name="ngModel">
      <div [hidden]="name.valid || name.pristine"
      class="alert alert-danger">
      Name is required
      </div>
      </div>
      <div class="form-group">
      <label for="author">Author</label>
      <input type="text" class="form-control" id="author"
      required
      [(ngModel)]="model.author" name="author"
      #author="ngModel">
      <div [hidden]="author.valid || author.pristine"
      class="alert alert-danger">
      Author is required
      </div>
      </div>
      <div class="form-group">
      <label for="publication">Publication</label>
      <input type="text" class="form-control" 
      id="publication"
      [(ngModel)]="model.publication" name="publication"
      #publication="ngModel">
      </div>
      <button type="submit" class="btn btn-success"       
      [disabled]="!bookForm.form.valid">Submit</button>
      &nbsp;&nbsp;
      <button type="button" class="btn btn-default"        
      (click)="newBook()">Clear</button>
      </form>
      </div>
      <style>
      .no-style .ng-valid {
      border-left: 1px solid #CCC
      }
      .no-style .ng-invalid {
      border-left: 1px solid #CCC
      }
      </style>
```

这是一个简单的模板表单，包含三个输入控件用于输入书名、作者和出版商名称，一个提交按钮用于提交详情，以及一个清除按钮用于清空表单。Angular 隐式地将 `ngForm` 指令应用于模板中的表单。我们将 `ngForm` 指令分配给了 `#bookForm` 本地变量。

使用 `#bookForm` 本地变量，我们可以跟踪表单的错误，并检查它们是有效还是无效、被触碰还是未触碰以及原始还是脏。在这里，只有当 `ngForm` 的 valid 属性返回 true 时，提交按钮才会被启用，因为它被分配到按钮的 disabled 属性。

`BookFormComponent` 中的 `onSubmit` 函数被分配给了表单的 `ngSubmit` 事件。因此，当单击提交按钮时，它将调用 `BookFormComponent` 中的 `onSubmit` 函数。

请注意，所有输入控件都包含 `ngModel` 事件-属性属性，并且将其分配给它们各自的模型属性，比如 `model.name`、`model.author` 和 `model.publication`。通过这种方式，我们可以实现双向绑定，这样当在对应的输入控件中输入值时，`BookFormComponent` 中的模型属性将被更新为它们各自的值：

1.  我们已经放置了所需的模板和组件。现在，我们需要创建一个 `AppModule` 来引导我们应用程序的根组件 `AppComponent`。创建一个名为 `app.module.ts` 的文件，并添加以下代码片段：

```ts
      import { NgModule } from '@angular/core';
      import { BrowserModule } from '@angular/platform-
      browser';
      import { FormsModule } from '@angular/forms';
      import { AppComponent } from './app.component';
      import { BookFormComponent } from './book-
      form.component';
      @NgModule({
        imports: [
        BrowserModule,
        FormsModule
        ],
        declarations: [
        AppComponent,
        BookFormComponent
        ],
        bootstrap: [ AppComponent ]
      })
      export class AppModule { }
```

正如我们在第二章*Angular 构建块-第一部分*中讨论的，任何 Angular 应用程序都将有一个根模块，该模块将使用`NgModule`指令进行装饰，并包含导入、声明和引导等元数据详细信息。

在上述代码中，请注意我们将`AppComponent`类分配为引导元数据，以通知 Angular`AppComponent`是应用程序的根组件。

1.  现在我们已经准备好了所有所需的模板和类，我们需要引导模块。让我们创建一个名为`main.ts`的文件，其中包含以下代码片段，用于引导模块：

```ts
      import { platformBrowserDynamic } from 
      '@angular/platform-
      browser-dynamic';
      import { AppModule } from './app/app.module';
      platformBrowserDynamic().bootstrapModule(AppModule)
```

1.  最后，添加以下内容的 index.html 文件：

```ts
      <!DOCTYPE html>
      <html>
      <head>
      <title>Book Form</title>
      <base href="/">
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, 
      initial-
      scale=1">
      <link rel="stylesheet"
      href="https://unpkg.com/bootstrap@3.3.7/
      dist/css/bootstra p.min.cs
      s">
      <link rel="stylesheet" href="styles.css">
      <link rel="stylesheet" href="forms.css">
      <!-- Polyfills -->
      <script src="node_modules/core-
      js/client/shim.min.js"></script>
      <script src="img/zone.js">
      </script>
      <script 
      src="img/system.src.js">
      </script>
      <script src="img/systemjs.config.js"></script>
      <script>
      System.import('main.js').catch(function(err){   
      console.error(err); 
      });
      </script>
      </head>
      <body>
      <first-template-form>Loading...</first-template-
      form>
      </body>
      </html>
```

注意在正文中添加了`<first-template-form/>`特殊标记。该标记将在运行时使用实际模板进行更新。另外，请注意，在运行时使用`System.js`模块加载器加载必需的库。`systemjs.config.js`文件应包含有关映射 npm 包和我们应用程序起始点的指令。在这里，我们的应用程序在`main.ts`中引导，这将在构建应用程序后被转译为`main.js`。`systemjs.config.js`的内容如下所示：

```ts
/**
* System configuration for Angular samples
* Adjust as necessary for your application needs.
*/
(function (global) {
System.config({
paths: {
  // paths serve as alias
  'npm:': 'node_modules/'
},
// map tells the System loader where to look for things
map: {// our app is within the app folder
'app': 'app',
// angular bundles
'@angular/animations': 'npm:@angular/animations/bundles/animations.umd.js',
'@angular/animations/browser': 'npm:@angular/animations/bundles/animations-browser.umd.js',
'@angular/core': 'npm:@angular/core/bundles/core.umd.js',
'@angular/common': 'npm:@angular/common/bundles/common.umd.js',
'@angular/compiler': 'npm:@angular/compiler/bundles/compiler.umd.js',
'@angular/platform-browser': 'npm:@angular/platform-browser/bundles/platform-browser.umd.js',
'@angular/platform-browser/animations': 'npm:@angular/platform-browser/bundles/platform-browser-animations.umd.js',
'@angular/platform-browser-dynamic': 'npm:@angular/platform-browser-dynamic/bundles/platform-browser-dynamic.umd.js',
'@angular/http': 'npm:@angular/http/bundles/http.umd.js',
'@angular/router': 'npm:@angular/router/bundles/router.umd.js',
'@angular/router/upgrade': 'npm:@angular/router/bundles/router-upgrade.umd.js',
'@angular/forms': 'npm:@angular/forms/bundles/forms.umd.js',
'@angular/upgrade': 'npm:@angular/upgrade/bundles/upgrade.umd.js',
'@angular/upgrade/static': 'npm:@angular/upgrade/bundles/upgrade-static.umd.js',
// other libraries
'rxjs': 'npm:rxjs',
'angular-in-memory-web-api': 'npm:angular-in-memory-web-api/bundles/in-memory-web-api.umd.js'
},
// packages tells the System loader how to load when no filename and/or no extension
packages: {
app: {
  main: './main.js',
  defaultExtension: 'js',
meta: {
'./*.js': {
  loader: 'systemjs-angular-loader.js'
}
}
},
rxjs: {
  defaultExtension: 'js'
}
}
});
})(this);
```

1.  现在，我们已经准备好了所有所需的内容。通过按下*F5*来运行应用程序，索引页面将以由`BookFormComponent`提供模板的方式呈现，如下所示：

![](img/image_03_001.png)

`FirstTemplateForm`应用程序的输出

1.  现在移除分配给输入控件的虚拟文本，并注意表单验证已触发，显示验证错误消息，保持**`Submit`**按钮处于禁用状态：

![](img/image_03_002.png)

检查控制台日志以进行表单提交

在这个模板驱动表单中，您可能已经注意到我们已经将`required`属性应用于输入控件。类似于这样，我们还可以应用最小长度和最大长度验证。然而，这样应用验证会将验证逻辑紧密耦合到模板中，并且我们只能通过编写基于浏览器的端到端测试来测试这些验证。

#### 模型驱动表单

Angular 提供了`FormGroup`和`FormControl`属性来实现模型驱动表单。

##### 模型驱动表单的基本对象

`FormControl`和`FormGroup`是模型驱动表单中的两个基本对象。`FormControl`是 Angular 表单中的输入字段，它封装了输入字段的值，其状态（有效性），是否已更改（脏），或是否有任何错误。

当我们构建一个表单时，我们需要创建控件并附加元数据到这些控件。我们必须通过添加`formControlName`属性将 Control 类附加到 DOM 输入元素，如下所示：

```ts
<input type="text" formControlName="name" />
```

`FormGroup`可以由 FormBuilder 进行实例化。我们还可以用默认值在组件中手动构建`FormGroup`，如下所示：

```ts
this.bookForm = new FormGroup({
  name: new FormControl('book name', Validators.required),
  author: new FormControl('author name', Validators.required),
  publication: new FormControl('publication name is optional')
});
```

让我们在**Visual Studio Code**（**VS Code**）中创建一个名为`ModelDrivenForm`的应用程序。以下是模型驱动表单的实现：

1.  添加所需的包和依赖项详细信息，并使用`npm install`命令来安装它们：

```ts
      {
      "name":"model-driven-form",
      "version":"1.0.0",
      "private":true,
      "description":"Model driven form",
      "scripts":{
      "test:once":"karma start karma.conf.js --single-
       run",
      "build":"tsc -p src/",
      "serve":"lite-server -c=bs-config.json",
      "prestart":"npm run build",
      "start":"concurrently \"npm run build:watch\" \"npm 
      run serve\"",
      "pretest":"npm run build",
      "test":"concurrently \"npm run build:watch\" \"karma 
       start 
      karma.conf.js\"",
      "pretest:once":"npm run build",
      "build:watch":"tsc -p src/ -w",
      "build:upgrade":"tsc",
      "serve:upgrade":"http-server",
      "build:aot":"ngc -p tsconfig-aot.json && rollup -c 
       rollup-
      config.js",
      "serve:aot":"lite-server -c bs-config.aot.json",
      "build:babel":"babel src -d src --extensions 
      \".es6\" --source-
      maps",
      "copy-dist-files":"node ./copy-dist-files.js",
      "i18n":"ng-xi18n",
      "lint":"tslint ./src/**/*.ts -t verbose"
      },
      "keywords":[
      ],
      "author":"",
      "license":"MIT",
      "dependencies":{
      "@angular/common":"~4.0.0",
      "@angular/compiler":"~4.0.0",
      "@angular/compiler-cli":"~4.0.0",
      "@angular/core":"~4.0.0",
      "@angular/forms":"~4.0.0","@angular/http":"~4.0.0",
      "@angular/platform-browser":"~4.0.0",
      "@angular/platform-browser-dynamic":"~4.0.0",
      "@angular/platform-server":"~4.0.0",
      "@angular/router":"~4.0.0",
      "@angular/tsc-wrapped":"~4.0.0",
      "@angular/upgrade":"~4.0.0
      ",
      "angular-in-memory-web-api":"~0.3.1",
      "core-js":"².4.1",
      "rxjs":"5.0.1",
      "systemjs":"0.19.39",
      "zone.js":"⁰.8.4"
      },
      "devDependencies":{
      "@types/angular":"¹.5.16",
      "@types/angular-animate":"¹.5.5",
      "@types/angular-cookies":"¹.4.2",
      "@types/angular-mocks":"¹.5.5",
      "@types/angular-resource":"¹.5.6",
      "@types/angular-route":"¹.3.2",
      "@types/angular-sanitize":"¹.3.3",
      "@types/jasmine":"2.5.36",
      "@types/node":"⁶.0.45",
      "babel-cli":"⁶.16.0",
      "babel-preset-angular2":"⁰.0.2",
      "babel-preset-es2015":"⁶.16.0",
      "canonical-path":"0.0.2",
      "concurrently":"³.0.0",
      "http-server":"⁰.9.0",
      "jasmine":"~2.4.1",
      "jasmine-core":"~2.4.1",
      "karma":"¹.3.0",
      "karma-chrome-launcher":"².0.0",
      "karma-cli":"¹.0.1",
      "karma-jasmine":"¹.0.2",
      "karma-jasmine-html-reporter":"⁰.2.2",
      "karma-phantomjs-launcher":"¹.0.2",
      "lite-server":"².2.2",
      "lodash":"⁴.16.2",
      "phantomjs-prebuilt":"².1.7",
      "protractor":"~4.0.14",
      "rollup":"⁰.41.6",
      "rollup-plugin-commonjs":"⁸.0.2",
      "rollup-plugin-node-resolve":"2.0.0",
      "rollup-plugin-uglify":"¹.0.1",
      "source-map-explorer":"¹.3.2",
      "tslint":"³.15.1",
      "typescript":"~2.2.0"
      },
      "repository":{
      }
      }
```

1.  创建一个`Book`类，并添加以下代码片段：

```ts
      export class Book {
      constructor(
      public id: number,
      public name: string,
      public author: string,
      public publication?: string
      ) { }
      }
```

1.  创建`AppComponent`并添加以下代码：

```ts
      import { Component } from '@angular/core';
      @Component({
      selector: 'first-model-form',
      template: '<book-form></book-form>'
      })
      export class AppComponent { }
```

此前展示的`AppComponent`是应用程序的根组件，将托管`BookFormComponent`。`AppComponent`带有第一个模型表单选择器和模板，其中包含带有特殊标签`<book-form/>`的内联 HTML。这个标签将在运行时更新为实际模板。

1.  现在，让我们添加`book-form.component.ts`，使用以下代码片段：

```ts
      import { Component, OnInit } from '@angular/core';
      import { FormControl, FormGroup, Validators } from 
      '@angular/forms';
      import { Book } from './book';
      @Component({
      selector: 'book-form',
      templateUrl: './book-form.component.html'
      })
      export class BookFormComponent implements OnInit {
      bookForm: FormGroup;
      public submitted: boolean;
      constructor() { }
      ngOnInit() {
      this.bookForm = new FormGroup({
      name: new FormControl('book name', 
      Validators.required),
      author: new FormControl('author name', 
      Validators.required),
      publication: new FormControl('publication name is 
      optional')
      });
      }
      onSubmit(model: Book, isValid: boolean) {
      this.submitted = true;
      console.log(model, isValid);
      // code to post the data
      }
      }
```

在这里，注意我们从`@angular/forms`中导入了`FormControl`、`FormGroup`和`Validators`。这些是实现模型驱动表单的基本类。我们还从`@angular/core`中导入了`Component`和`OnInit`，用于组件类的实现，然后我们从`book.ts`中导入了 Book。Book 是该表单的数据模型。

`BookFormComponent`带有从`@angular/core`导入的`@Component`指令。选择器值设置为`book-form`，`templateUrl`分配了模板 HTML 文件。

在`BookFormCompoent`中，我们通过实例化`FormGroup`并将其分配给属性，如名称、作者和出版物，来初始化表单模型。我们有`onSubmit()`方法来将提交的数据提交到 API。

1.  现在，让我们添加`book-form.component.html`模板文件，并添加以下 HTML 内容：

```ts
      <div class="container">
      <h1>New Book Form</h1>
      <form [formGroup]="bookForm" novalidate       
      (ngSubmit)="onSubmit(bookForm.value, 
       bookForm.valid)">
      <div class="form-group">
      <label for="name">Name</label>
      <input type="text" class="form-control" 
       formControlName="name">
      <small [hidden]="bookForm.controls.name.valid ||       
      (bookForm.controls.name.pristine && !submitted)" 
      class="text-
      danger">
      Name is required.
      </small>
      </div>
      <div class="form-group">
      <label for="author">Author</label>
      <input type="text" class="form-control" 
      formControlName="author">
      <small [hidden]="bookForm.controls.author.valid ||       
      (bookForm.controls.author.pristine && !submitted)" 
      class="text-
      danger">
      Author is required.
      </small>
      </div>
      <div class="form-group">
      <label for="publication">Publication</label>
      <input type="text" class="form-control" 
      formControlName="publication">
      </div>
      <button type="submit" class="btn btn-
      success">Submit</button>
      </form>
      </div>
      <style>
      .no-style .ng-valid {
      border-left: 1px solid #CCC
      }
      .no-style .ng-invalid {
      border-left: 1px solid #CCC
      }
      </style>
```

与模板驱动表单类似，这是一个简单的模型驱动表单，其中包含三个输入控件用于输入图书、作者和出版商名称，以及一个提交按钮来提交详细信息。在表单标签中，我们添加了`formGroup`指令来分配给表单，并将其分配给了`bookForm`。每个输入控件都有一个特殊的属性`formControlName`，分别分配有各自的`formControl`，比如名称、作者和出版物。

`BookFormComponent`中的`onSubmit`函数分配给了表单的`ngSubmit`事件。因此，当单击提交按钮时，它将调用`BookFormComponent`中的`onSubmit`函数，传递`bookForm`的值和有效属性。

注意，所有的输入控件都没有任何事件兼属性属性，就像模板驱动表单中一样。在这里，我们可以通过将模型值从`bookForm.value`属性传递到`onSubmit`函数，并从组件中访问模型来实现双向绑定。

我们已经准备好所需的模板和组件。现在我们需要创建一个`AppModule`来引导我们应用程序的根组件`AppComponent`。创建一个名为`app.module.ts`的文件，并添加以下代码片段：

```ts
      import { NgModule } from '@angular/core';
      import { BrowserModule } from '@angular/platform-
      browser';
      import { FormsModule, ReactiveFormsModule } from 
      '@angular/forms';
      import { AppComponent } from './app.component';
      import { BookFormComponent } from './book-
      form.component';
      @NgModule({
      imports: [
      BrowserModule,
      ReactiveFormsModule
      ],
      declarations: [
      AppComponent,
      BookFormComponent
      ],
      bootstrap: [ AppComponent ]
      })
      export class AppModule { }
```

在上述代码中，请注意，我们已将 `AppComponent` 类分配为引导元数据，以通知 Angular `AppComponent` 是应用程序的根组件。还要注意，我们已从 `@angular/forms` 导入了 `FormsModule` 和 `ReactiveFormsModule`。

1.  现在，我们已经准备好所有所需的模板和类，我们需要引导模块。让我们创建一个名为 `main.ts` 的文件，其中包含如下代码段来引导模块：

```ts
      import { platformBrowserDynamic } from 
      '@angular/platform-
      browser-dynamic';
      import { AppModule } from './app/app.module';
      platformBrowserDynamic().bootstrapModule(AppModule)
```

1.  最后，使用以下内容添加 `index.html` 文件：

```ts
      <!DOCTYPE html>
      <html>
      <head>
      <title>Hero Form</title>
      <base href="/">
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, 
      initial-
      scale=1">
      <link rel="stylesheet"
      href="https://unpkg.com/bootstrap@3.3.7
      /dist/css/bootstra   p.min.css">
      <link rel="stylesheet" href="styles.css">
      <link rel="stylesheet" href="forms.css">
      <!-- Polyfills -->
      <script src="node_modules/core-   
      js/client/shim.min.js"></script>
      <script src="img/zone.js">
      </script>
      <script 
      src="img/system.src.js">
      </script>
      <script src="img/systemjs.config.js"></script>
      <script>
      System.import('main.js').catch(function(err){ 
      console.error(err); 
      });
      </script>
      </head>
      <body>
      <first-model-form>Loading...</first-model-form>
      </body>
      </html>
```

请注意，`<first-model-form/>` 特殊标记被添加到正文中。此标记将在运行时更新为实际模板。还要注意，使用 `System.js` 模块加载器在运行时加载所需的库。`systemjs.config.js` 文件应该包含有关如何映射 `npm` 包和我们应用程序的起始点的指令。在这里，我们的应用程序在 `main.ts` 中启动，在构建应用程序后，它将被转译为 `main.js`。`systemjs.config.js` 的内容如下：

```ts
/**
* System configuration for Angular samples
* Adjust as necessary for your application needs.
*/
(function (global) {
System.config({
paths: {
// paths serve as alias
'npm:': 'node_modules/'
},
// map tells the System loader where to look for things
map: {
// our app is within the app folder
'app': 'app',
// angular bundles
'@angular/animations': 'npm:@angular/animations/bundles/animations.umd.js',
'@angular/animations/browser': 'npm:@angular/animations/bundles/animations-browser.umd.js',
'@angular/core': 'npm:@angular/core/bundles/core.umd.js',
'@angular/common': 'npm:@angular/common/bundles/common.umd.js',
'@angular/compiler': 'npm:@angular/compiler/bundles/compiler.umd.js',
'@angular/platform-browser': 'npm:@angular/platform-browser/bundles/platform-browser.umd.js',
'@angular/platform-browser/animations': 'npm:@angular/platform-browser/bundles/platform-browser-animations.umd.js',
'@angular/platform-browser-dynamic': 'npm:@angular/platform-browser-dynamic/bundles/platform-browser-dynamic.umd.js',
'@angular/http': 'npm:@angular/http/bundles/http.umd.js',
'@angular/router': 'npm:@angular/router/bundles/router.umd.js',
'@angular/router/upgrade': 'npm:@angular/router/bundles/router-upgrade.umd.js',
'@angular/forms': 'npm:@angular/forms/bundles/forms.umd.js',
'@angular/upgrade': 'npm:@angular/upgrade/bundles/upgrade.umd.js',
'@angular/upgrade/static': 'npm:@angular/upgrade/bundles/upgrade-static.umd.js',
// other libraries
'rxjs': 'npm:rxjs',
'angular-in-memory-web-api': 'npm:angular-in-memory-web-api/bundles/in-memory-web-api.umd.js'
},
// packages tells the System loader how to load when no filename and/or no extension
packages: {
app: {
main: './main.js',
defaultExtension: 'js',
meta: {
'./*.js': {
loader: 'systemjs-angular-loader.js'
}
},
rxjs: {
defaultExtension: 'js'
}
}
});
})(this);
```

1.  现在，我们已经拥有了所需的一切。按下 *F5* 运行应用程序，索引页面将以`BookFormComponent`为模板进行渲染，如下所示:

![](img/image_03_003.png)

模型驱动表单的输出

在 Chrome 浏览器的开发者工具中保持控制台窗口打开的情况下，单击 **`提交`** 按钮。请注意，日志记录模型对象是表单有效值为 false，因为作者属性缺少值。

现在，让我们在作者属性中输入一些值，并在 Chrome 浏览器的开发者工具中保持控制台窗口打开的情况下，单击**`提交`** 按钮。请注意，模型对象与填充了值的所有必需属性的表单有效值都被记录如下：

![](img/image_03_004.png)

检查模型驱动表单提交

当我们使用 `FormGroup` 在组件中配置验证时，我们将验证逻辑从模板松散耦合移动到了组件中。所以，我们可以使用任何测试框架编写测试方法来通过断言组件来验证验证逻辑。参考第八章，*测试 Angular 应用* 来了解如何测试 Angular 应用。

## 管道

* * *

在 Angular 中，管道是 AngularJS 1.x 中过滤器的替代品。管道是过滤器的改良版本，可以转换常见数据。大多数应用程序都会从服务器获取数据，并在在前端显示数据之前对其进行转换。在这种情况下，管道在渲染模板时非常有用。Angular 为此提供了这些强大的管道 API。管道将数据作为输入，并根据需要输出转换后的数据。

### 常用的管道

以下是 `@angular/core` 中提供的内置管道，并将看到一些带有示例的管道：

+   AsyncPipe

+   CurrencyPipe

+   DatePipe

+   DecimalPipe

+   I18nPluralPipe

+   I18nSelectPipe

+   JsonPipe

+   LowerCasePipe

+   PercentPipe

+   SlicePipe

+   TitleCasePipe

+   UpperCasePipe

#### 带参数的管道

我们可以通过冒号(:)符号向管道传递参数，如下所示：

```ts
<p>Price of the book is {{ price | currency:'USD' }} </p>
```

通过(:)分隔的方式可以将多个输入传递给管道，如下所示：

```ts
<li *ngFor="let book of books | slice:1:3">{{i}}</li>
```

### 管道链

在某些情况下，可能需要使用多个管道。例如，考虑一种情况，需要以大写形式和长日期格式显示数据。以下代码以大写形式和长日期格式显示书籍的出版日期：

```ts
Publishing Date: {{ pubDate | date | uppercase}}
```

### 货币管道

货币管道将数字格式化为所需的货币格式。这是货币管道的语法：

```ts
expression | currency[:currencyCode[:symbolDisplay[:digitInfo]]] 
```

`expression`是管道的输入数据；`currency`是管道的关键词，它接受三个参数，分别为`currencyCode`，取值为 USD、INR、GBP 和 EUR，`symbolDisplay`，接受 true 或 false 来显示/隐藏货币符号，以及`digitInfo`，用于货币的小数格式。以下模板演示了如何使用货币管道：

![](img/image_03_005.png)

实现货币管道的模板

对于各种货币格式，模板的输出如下：

![](img/image_03_006.png)

使用货币管道的输出

### 日期管道

日期管道将输入数据转换为日期管道支持的各种日期格式。日期管道的语法如下：

```ts
expression | date[:format] 
```

假设组件中的`dateData`被赋予了`Date.now()`。在模板中实现日期管道的方式如下截图所示：

![](img/image_03_007.png)

实现日期管道的模板

应用各种日期格式后的模板输出如下：

![](img/image_03_008.png)

使用日期管道的输出

日期管道支持各种格式，如`medium`(`yMMMdjms`)、`short`(`yMdjm`)、`mediumDate`(`yMMMd`)、`shortDate`(`yMd`)、`fullDate`(`yMMMMEEEEd`)、`longDate`(`yMMMMd`)、`mediumTime`(`jms`)和`shortTime`(`jm`)。

### 大写和小写管道

大写和小写管道将输入数据分别转换为大写和小写。以下模板同时显示作者姓名的大写和小写形式：

![](img/image_03_009.png)

实现大写和小写管道的模板

此模板的输出如下：

![](img/image_03_010.png)

实现大写和小写管道的输出

### JSON 管道

JSON 管道类似于在 JavaScript 中应用 `JSON.Stringify` 对持有 JSON 值的对象进行操作。模板中使用 JSON 管道的用法如下截图所示：

![](img/image_03_011.png)

实现 JSON 管道的模板

在模板中使用 JSON 管道的输出如下所示：

![](img/image_03_012.png)

使用 JSON 管道的输出

## AppComponent

* * *

`AppComponent`是一个应用程序的组件，其配置为根组件，并且处理`app.component.html`Â模板的渲染。在前面的章节中，我们看到了实现各种管道及其各自输出的模板代码。以下代码片段显示了模板的组件：

```ts
import { Component } from '@angular/core'; 

@Component({ 
  selector: 'pipe-page', 
  templateUrl: 'app/app.component.html' 
}) 
export class AppComponent { 
    numberData : number; 
    currencyData : number; 
    dateData : number; 
    authorName : string; 
    object: Object = {autherName: 'Rajesh Gunasundaram',   
    pubName: 'Packt Publishing'} 
    constructor() { 
        this.numberData = 123.456789; 
        this.currencyData = 50; 
        this.dateData = Date.now(); 
        this.authorName = 'rAjEsH gUnAsUnDaRaM'; 
    } 
} 
```

管道，这是 Angular 提供的非常强大且易于使用的 API，能够在显示在屏幕上之前格式化数据，这极大地简化了我们的流程。

## 路由器

* * *

AngularJS 使用`ngRoute`模块来运行具有基本功能的简单路由器。它通过将路径映射到使用`$routeProvider`服务配置的路由来使 URL 与组件和视图进行深度链接。AngularJS 1.x 需要安装`ngRoute`模块才能在应用中实现路由。

Angular 引入了一个组件路由器，用于深度链接 URL 请求并导航到模板或视图。如果有任何参数，它会将其传递给标注为该路线的相应组件。

### 组件路由的核心概念

Angular 使用一个组件路由器作为视图系统。它还适用于 AngularJS 1.x。它支持拦截路由并为加载的组件提供特定路由值，自动深度链接，嵌套和同级路由。让我们来看一下组件路由器的一些核心功能。

### 设置组件路由器

组件路由器不是核心 Angular 框架的一部分。它作为单独库`@angular/router`的一部分出现在 Angular NPM 包中。我们需要将`@angular/router`添加到`packages.json`中的依赖项部分。然后，在`app.routing.ts`中，我们需要从`@angular/router`中导入`Routes`和`RouterModule`。路由器包括诸如`RouterOutlet`，`RouterLink`和`RouterLinkActive`这样的指令，一个`RouterModule`服务和`Routes`的配置。

```ts
<base> tag with the href attribute that is to be added to the head tag in the index file, considering that the app folder is the root of the application. This is required when you run your application in HTML5 mode. It helps resolve all the relative URLs in the application:
```

```ts
<base href="/"> 
```

### 配置路由

```ts
app.module.ts:
```

```ts
import { RouterModule } from '@angular/router';
RouterModule.forRoot([
{
  path: 'about',
  component: AboutComponent
},
{
  path: 'contact',
  component: ContactComponent
}
])
```

在这里，我们配置了两个路由，帮助用户在单击时转到`about`和`contact`视图。路由基本上是路由定义的集合。所定义的路径值标识出匹配路径的 URL 时要实例化的组件。然后，实例化的组件将负责渲染视图。

现在，我们需要将配置的路由添加到`AppModule`中，从`@angular/router`中导入`RouterModule`，并将其添加到`@NgModule`的 imports 部分中，如下所示：

```ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { RouterModule } from '@angular/router';
import { AppComponent } from './app.component';
import { AboutComponent } from './heroes.component';
@NgModule({
  imports: [
  BrowserModule,
  FormsModule,
  RouterModule.forRoot([
{
  path: 'about',
  component: AboutComponent
}
])
],
declarations: [
AppComponent,
AboutComponent
],
bootstrap: [ AppComponent ]
})
export class AppModule { }
```

在这里，`forRoot()`方法提供了路由器服务提供程序和指令来执行导航。

### 路由出口和路由链接

当用户将`'/about'`添加到应用程序 URL 的末尾后，将其传递到浏览器地址栏中时，路由将使用`'about'`匹配该请求，并启动`AboutComponent`来处理`about`视图的渲染。我们需要以某种方式告知路由器在哪里显示此`about`视图。可以通过指定`<router-outlet/>`来实现这一点，这类似于 AngularJS 1.x 中的`<ng-view/>`标记，用于加载与路由相应路径相关的模板。

路由链接可通过单击锚标记中指定的链接来导航到路由 URL。以下是一个示例路由链接标记：

```ts
<a [routerLink]="['/about']">About</a>
```

## 服务

* * *

我们创建的应用程序处理大量的数据。大多数数据将从服务中检索，并且将在应用程序的各个部分重用。让我们创建一个可以使用`http`检索数据的服务。服务应该与组件松散耦合，因为组件的主要重点应该是支持视图。因此，可以使用依赖注入将服务注入到组件中。这种方法将使我们能够模拟服务以进行单元测试组件。

```ts
TodoService is shown here. TodoService has a property named todos of the type array that can hold a collection of Todo items and is hardcoded with the Todo items in the constructor:
```

```ts
import {Injectable} from '@angular/core'; 
import { Todo } from './todo'; 

@Injectable()  
export class TodoService { 
    todos: Array<Todo>; 
    constructor() { 
        this.todos = [ 
    {"title": "First Todo", "completed":  false}, 
    {"title": "Second Todo", "completed": false}, 
    {"title": "Third Todo", "completed": false} 
            ] 
    } 

    getTodos() { 
        return this.todos; 
    } 
} 
```

请注意，用`@Injectable`装饰的服务是为了让 Angular 知道这个服务是可注入的。

我们可以将可注入的`TodoService`注入到`AppComponent`的构造函数中，如下所示：

```ts
import { Component } from '@angular/core'; 
import { Todo } from './Todo'; 
import { TodoService } from './TodoService'; 
@Component({ 
  selector: 'my-service', 
  templateUrl: 'app/app.component.html' 
}) 
export class AppComponent { 
    todos: Array<Todo>; 
    constructor(todoService: TodoService) { 
        this.todos = todoService.getTodos(); 
    } 
} 
```

在引导过程中，我们还需要传递`TodoService`，这样 Angular 将创建服务的实例，并在其被注入的任何地方保持可用。因此，让我们在`main.ts`文件中如所示地向`bootstrap`函数传递`TodoService`： 

```ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { AppComponent } from './app.component';
import { TodoService } from './TodoService';
@NgModule({
imports: [
BrowserModule,
],
declarations: [
AppComponent,
],
providers: [ TodoService ],
bootstrap: [ AppComponent ]
})
export class AppModule { }
```

注意，可注入服务用方括号括起来。这是一种应用依赖注入的方法。有关 Angular 依赖注入更多信息，请参考第二章, *Angular Building Blocks - Part 1*。Angular 已经改进了依赖注入，可以创建`TodoService`的实例并将其注入到组件中。

在`app.component.html`模板中，我们遍历`AppComponent`中`todos`属性的每个项目并列出它们：

```ts
<h2>My Todo List</h2> 
<ul> 
    <li *ngFor="let todo of todos"> 
        {{ todo.title }} - {{ todo.completed }} 
    </li> 
</ul> 
```

此模板的内容将在`index.html`文件的`<my-service>`特殊标签下呈现：

```ts
 <body> 
        <my-service>Loading...</my-service> 
 </body> 
```

运行时，应用程序将呈现如下的`todo`项目清单：

![](img/image_03_013.png)

我的待办事项应用程序的输出

## 可观察对象

* * *

在 AngularJS 中，我们使用服务以异步方式使用 `$http` 中的 promise 获取数据。在 Angular 中，我们有了 `Http` 服务取代了 `$http`，它返回一个可观察对象而不是 promise，因为它应用了类似模式。 Angular 利用了从 ReactiveX 库采用的 Observable 类。 ReactiveX 是一个用于应用观察者、迭代器模式和函数式编程完成异步编程的 API。你可以在 [`reactivex.io/`](http://reactivex.io/) 找到有关反应式编程的更多信息。

Observer 模式将在依赖对象更改时通知依赖者。迭代器模式将方便地访问集合，无需了解集合中元素的结构。在 ReactiveX 中结合这些模式使观察者能够订阅可观察的集合对象。观察者不需要等到可观察的集合对象可用时才能做出反应，而是在获得可观察对象更改通知时做出反应。

Angular 使用名为 RxJS 的 JavaScript 实现，它是一组库而不是一个特定的 API。它在 HTTP 服务和事件系统中使用 Observables。promise 总是返回一个值。

`http.get()` 方法将返回 Observables，并且客户端可以订阅以获取从服务返回的数据。 Observables 可以处理多个值。因此，我们还可以调用多个 `http.get()` 方法，并将它们包装在 Observables 提供的 `forkJoin` 方法下。

我们还可以控制服务调用并通过 Observable 延迟调用，通过应用一个规则，只有在上次对服务的调用是 500 毫秒前才调用服务。

Observables 是可取消的。所以，我们也可以通过取消订阅来取消之前的请求，并发起新的请求。我们随时可以取消任何之前未完成的调用。

让我们修改 `TodoService` 以使用 Observable，并将硬编码的 JSON 值替换为对 `todos.json` 文件的 `http.get()` 调用。更新后的 `TodoService` 如下所示：

```ts
import {Injectable} from '@angular/core';
import {Http} from '@angular/http';
import 'rxjs/add/operator/toPromise';
@Injectable()
export class TodoService {
constructor(private http: Http) {
this.http = http;
}
getTodos() {
  return this.http.get('/app/todos.json')
  .toPromise()
  .then(response => response.json().data)
  .catch(this.handleError);
}
}
```

请注意，我们从 `@angular/http` 中导入了 HTTP 模块、`rsjs/Rx` 中的响应，以及基于 ReactiveX 的 Observable 模块。`getTodos` 方法通过调用 `todos.json` 查询并返回一组待办事项来更新。

`AppComponent` 和 `TodoService` 在 `app.module.ts` 文件中进行了引导，如下所示：

```ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { HttpModule } from '@angular/http';
import { AppComponent } from './app.component';
import { TodoComponent } from './todo.component';
import { TodoService } from './hero.service';
@NgModule({
  imports: [
  BrowserModule,
  HttpModule,
  AppRoutingModule
  ],
  declarations: [
  AppComponent,
  TodoComponent
  ],
  providers: [ TodoService ],
  bootstrap: [ AppComponent ]
})
export class AppModule { }
```

从 `'@angular/platform-browser-dynamic'` 中导入 `{bootstrap}`；模板被更新以渲染待办事项列表，如下所示：

```ts
import {HTTP_PROVIDERS} from '@angular/http'; 
import 'rxjs/add/operator/map'; 
import {AppComponent} from './app.component'; 
import {TodoService} from './TodoService';
bootstrap(AppComponent, [HTTP_PROVIDERS, TodoService]); 
```

运行应用将呈现从 `TodoService` 中返回的从 Observables 订阅的数据：

![](img/image_03_014.png)

从 Observables 订阅的渲染数据的 index.html 输出

## 总结

* * *

哇呜！你已经学习完了 Angular 架构的其余构建模块。我们从表单开始介绍本章，并讨论了 Angular 中可用的表单类型以及如何实现它们。然后，您了解了管道，这是 AngularJS 1.x 中筛选器的替代方案。接下来，我们讨论了路由器，并学习了如何在 Angular 中配置路由器到组件是多么容易。最后，您学会了如何在 Angular 中创建服务以及如何使用 HTTP 模块访问外部服务。您还了解了使用 Observables 的优势以及在服务调用中如何实现它。

在下一章中，我们将讨论 TypeScript 的基础知识。
