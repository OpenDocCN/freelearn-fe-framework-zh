## 第八章：测试 Angular 应用

本章讨论使用**Jasmine**框架测试 Angular 组件和服务。

在本章中，我们将涵盖以下主题：

+   介绍 Jasmine

+   测试 Angular 服务

+   测试 Angular 组件

## 介绍 Jasmine

* * *

Jasmine 是一个开源框架，用于在不依赖于 DOM 的情况下测试 JavaScript 代码。由于 Angular 是松散耦合的，我们可以使用 Jasmine 框架来测试 Angular 组件、服务等。独立于彼此，Jasmine 的清晰语法使您能够轻松编写测试。

一个名为 describe 的全局函数是**Jasmine**函数的起始点。这个全局函数接受一个函数和两个 string 类型的参数。字符串参数描述了测试，函数将有测试的实际实现：

```ts
describe("short description about the test suite", function() { 
}); 
```

实际测试方法由名为`it`函数的全局函数定义，该函数接受两个参数。第一个参数是测试或规范的标题，第二个参数是通过验证代码状态来测试期望的函数。期望与 Microsoft 单元测试框架中的断言类似。如果在规范中任何一个定义的期望失败，这被称为失败的规范。以下代码说明了前述声明：

```ts
describe("short description about the test suite", function() { 
  it("a spec with single expectation", function() { 
    expect(true).toBe(true); 
  }); 
}); 
```

测试方法或规范方法将会有一个或多个 expect 语句，如下所示，通过链式连接到 expect 函数的匹配器函数来比较实际值和期望值；有各种默认的匹配器函数可供使用：

```ts
describe("short description about the test suite", function() { 
it("a spec with single expectation", function() { 
expect(afunction).toThrow(e); 
expect(true).toBe(true); 
expect(variable).toBeDefined(); 
expect(variable).toBeFalsy(); 
expect(number).toBeGreaterThan(number); 
expect(number).toBeLessThan(number); 
expect(variable).toBeNull(); 
expect(variable).toBeTruthy(); 
expect(value).toBeUndefined(); 
expect(array).toContain(member); 
expect(string).toContain(substring); 
expect(variable).toEqual(variable); 
expect(value).toMatch(pattern); 
  }); 
}); 
```

我们只看到了**Jasmine**框架的基础知识，还有更多的功能可以使用。你可以通过访问官方网站[`jasmine.github.io/`](http://jasmine.github.io/)了解更多信息。这个简介已经足够让我们学会如何测试 Angular 服务和组件了。

## 测试 Angular 服务

* * *

```ts
Todo class:
```

```ts
export class Todo { 
    title: string; 
    completed: boolean;
    constructor(title: string) { 
        this.title = title; 
        this.completed = false; 
    } 
    set isCompleted(value: boolean) { 
        this.completed = value; 
    } 
} 
```

接下来，创建一个名为`todo.service.ts`的服务，它在构造函数中构建`Todo`项目列表。`todo.service.ts`的完整代码如下所示：

```ts
import { Todo } from './todo' 
export class TodoService { 
    todos: Array<Todo> 
    constructor() { 
        this.todos = [new Todo('First item'), 
        new Todo('Second item'), 
        new Todo('Third item')]; 
    } 
    getPending() { 
        return this.todos.filter((todo: Todo) => todo.completed === 
        false); 
    } 
    getCompleted() { 
        return this.todos.filter((todo: Todo) => todo.completed === 
        true); 
    } 
    remove(todo: Todo) { 
        this.todos.splice(this.todos.indexOf(todo), 1); 
    } 
    add(title: string) { 
        this.todos.push(new Todo(title)); 
    } 
    toggleCompletion(todo: Todo) { 
        todo.completed = !todo.completed; 
    } 
    removeCompleted() { 
        this.todos = this.getPending(); 
    } 
} 
```

我们已经建立了与数据源交互的服务。现在，让我们使用 Jasmine 框架编写测试来测试`TodoService`。我们将测试`getPending()`和`getCompleted()`两种方法。创建一个名为`todo.service.spec.ts`的文件。

导入`TodoService`应用特定的服务，如下所示：

```ts
import { TodoService } from "./todo.service"; 
```

定义`describe`方法：是 Jasmine 函数的起始点的全局函数，接受两个参数，一个描述测试的字符串和一个具有测试实际实现的函数：

```ts
describe("TodoService Testing",() => { 
}); 
describe function with beforeEach is given here:
```

```ts
describe('TodoService Testing', () => { 
  let service: TodoService; 
  beforeEach(() => { service = new TodoService(); });   
}); 
```

`beforeEach`函数将在运行每个测试方法之前执行，并且为每个测试提供一个`TodoService`的实例。

现在，让我们定义`it`测试方法，示例如下：

```ts
it('getPending length should return 3', () => { 
    expect(service.getPending().length).toBe(3); 
}); 
it('getCompleted length should return 0', () => { 
    expect(service.getCompleted().length).toBe(0); 
}); 
```

在这里，我们验证`getPending()`和`getCompleted()`返回值的长度期望。

```ts
todo.service.spec.ts is this:
```

```ts
import { TodoService } from "./todo.service";  
describe('TodoService Testing', () => { 
  let service: TodoService; 
  beforeEach(() => { service = new TodoService(); }); 
  it('getPending length should return 3', () => { 
    expect(service.getPending().length).toBe(3); 
  }); 
  it('getCompleted length should return 0', () => { 
    expect(service.getCompleted().length).toBe(0); 
  }); 
}); 
```

我们已准备好要运行的测试用例或测试; 通过执行以下命令来运行它们：

```ts
npm run build:watchkarma start karma.conf.js
```

`npm run build:watch`命令将构建您的应用程序，并将 TypeScript 文件转译为 JavaScript。然后，执行`karma start karma.config`命令启动我们应用程序的测试运行器。

Karma 是一个测试运行器，可用于对任何 JavaScript 应用程序运行测试。`karma.config.js`文件是 karma 的配置文件，提供有关我们应用程序的信息，以便它能够了解并测试应用程序。karma 配置文件包含应用程序消耗的 JavaScript 库和框架的路径详细信息，还提供 karma 所使用的插件的详细信息。

Karma 配置文件包含了应用程序中`basePath`，`frameworks`，`plugins`，`client`和自定义启动器的配置详细信息。我们已在 karma 中配置了 Jasmine 作为我们的测试框架，我们在运行测试时加载了所需模块的插件列表。我们还配置了具有`buildPaths`和`clearContext`的客户端。`buildPaths`将包含查找转译后应用程序 JS 和映射文件的路径详细信息。以下是供您参考的完整 karma 配置文件：

```ts
module.exports = function(config)   {    
  var appBase    = 'src/';         // transpiled app JS and map files   
  var appAssets  = '/base/app/';   // component assets fetched by  
  Angular's compiler   
  // Testing helpers (optional)   are conventionally in a folder called 
     `testing`   
  var testingBase    = 'src/testing/';   // transpiled test JS and map  
  files   
  var testingSrcBase = 'src/testing/';   // test source TS    
   files   
  config.set({   
    basePath: '',   
    frameworks: ['jasmine'],   
    plugins: [   
      require('karma-jasmine'),   
      require('karma-chrome-launcher'),   
      require('karma-jasmine-html-reporter')   
    ],   
    client: 
  {   
      builtPaths: [appBase,   testingBase], // add more 
      spec base paths 
      as needed   
      clearContext: false //   leave Jasmine Spec Runner 
      output visible  
      in browser   
    },   
    customLaunchers: {   
      // From the CLI. Not used   here but interesting   
      // chrome setup for travis   CI using chromium   
      Chrome_travis_ci: {   
        base: 'Chrome',   
        flags: ['--no-sandbox']   
      }   
    },   
    files: [   
      // System.js for module   loading   
      'node_modules/systemjs/dist/system.src.js',   
      // Polyfills   
      'node_modules/core-js/client/shim.js',   
      // zone.js   
      'node_modules/zone.js/dist/zone.js',   
      'node_modules/zone.js/dist/long-stack-trace-
       zone.js',   
      'node_modules/zone.js/dist/proxy.js',   
      'node_modules/zone.js/dist/sync-test.js',   
      'node_modules/zone.js/dist/jasmine-patch.js',   
      'node_modules/zone.js/dist/async-test.js',   
      'node_modules/zone.js/dist/fake-async-test.js',   
      // RxJs   
      { pattern: 'node_modules/rxjs/**/*.js',   included: 
        false, 
        watched: false },   
      { pattern: 'node_modules/rxjs/**/*.js.map',   
        included: false, 
        watched: false },   
      // Paths loaded via module   imports:   
      // Angular itself   
      { pattern: 'node_modules/@angular/**/*.js',   
        included: false, 
        watched: false },   
      { pattern: 'node_modules/@angular/**/*.js.map',   
        included: 
        false, watched: false },   
      { pattern: appBase + '/systemjs.config.js',   
        included: false, 
         watched: false },   
      { pattern: appBase + '/systemjs.config.extras.js',   
        included: 
        false, watched: false },   
      'karma-test-shim.js', //   optionally extend 
       SystemJS mapping 
       e.g., with barrels   
      // transpiled application   & spec code paths loaded 
         via module 
         imports   
      { pattern: appBase + '**/*.js',   included: false, 
        watched: true   
  },   
      { pattern: testingBase + '**/*.js',   included: 
        false, watched:  
        true 
  },   
      // Asset (HTML & CSS)   paths loaded via Angular's 
         component    
         compiler   
      // (these paths need to be   rewritten, see proxies 
          section)   
      { pattern: appBase + '**/*.html',   included: false, 
        watched: 
        true 
  },   
      { pattern: appBase + '**/*.css',   included: false, 
        watched: true        
  },    
      // Paths for debugging with   source maps in dev    
         tools   
      { pattern: appBase + '**/*.ts',   included: false, 
        watched: false   
  },   
      { pattern: appBase + '**/*.js.map',   included: 
        false, watched: 
        false 
  },   
      { pattern: testingSrcBase +   '**/*.ts', included: 
        false, 
        watched: false },   
      { pattern: testingBase + '**/*.js.map',   included: 
        false, 
        watched: false}   
    ],   
    // Proxied base paths for   loading assets   
        proxies: 
  {   
      // required for modules   fetched by SystemJS   
      '/base/src/node_modules/': '/base/node_modules/'   
  },   
    exclude: [],   
    preprocessors: {},   
    reporters: ['progress', 'kjhtml'],   
    port: 9876,   
    colors: true,   
    logLevel: config.LOG_INFO,   
    autoWatch: true,   
    browsers: ['Chrome'],   
    singleRun: false   
   })   
 }   
```

命令`karma start`会以 karma 配置文件路径为参数，并启动 karma 测试运行器。`npm run build`命令配置在`pretest`中，这样它将在运行测试之前执行。它执行`tsc -p src`命令，这是一个 TypeScript 编译器，用于转译`src`文件夹中的代码。以下屏幕截图说明了根据`package.json`中`scripts`项中的配置，在命令窗口中执行这些命令：

```ts
C:\Users\rajesh.g\Packt\Chapter8\mytodos>npm     
            test> my-todo@1.0.0 pretest   
          C:\Users\rajesh.g\Packt\Chapter8\mytodos> npm run build> my-todo@1.0.0 build   
          C:\Users\rajesh.g\Packt\Chapter8\mytodos> tsc -p src/> my-todo@1.0.0 test   
          C:\Users\rajesh.g\Packt\Chapter8\mytodos> concurrently "npm run   build:watch" "karma 
            start 
            karma.conf.js"
```

Karma 在浏览器中启动应用程序，并运行 specs 中的所有测试。`http-server`命令将启动开发服务器，以托管`mytodo` Angular 应用程序。测试执行结果如下所示：

![](img/image_08_001.png)

TodoService 的测试结果

## Angular 组件测试

* * *

我们刚刚学习了如何在 Angular 应用程序中测试服务。现在，让我们讨论如何测试 Angular 组件。执行以下步骤来为应用程序创建`AppComponent`：

1.  创建名为`app.component.ts`的文件。

1.  导入必要的`Component`、`TodoService`和`Todo`等模块，用于`AppComponent`，如下所示：

```ts
        import { Component } from '@angular/core'; 
        import { Todo } from './todo'; 
        import { TodoService } from './todo.service'; 
```

1.  如下所示，定义`AppComponent`类：

```ts
        export class AppComponent {} 
```

1.  通过`@Component`属性装饰`AppComponent`类，具有`selector`，`providers`和`templateUrl`元数据：

```ts
        @Component({ 
            selector: 'my-app', 
            templateUrl: './app.component.html', 
            providers: [TodoService] 
        }) 
        export class AppComponent {     
        } 
```

1.  声明`todos`，`todoService`，`newTodoText`和`title`变量：

```ts
        todos: Array<Todo>; 
        todoService: TodoService; 
        newTodoText = ''; 
        title = 'Test My Todo App'; 
```

1.  定义构造函数，并注入`todoService`，如下所示。请注意，构造函数使用`todoService`返回的`todos`更新`todos`：

```ts
        constructor(todoService: TodoService) 
        { 
              this.todoService = todoService; 
              this.todos = todoService.todos; 
        } 
```

1.  引入`addTodo()`函数，调用`TodoService`的`add()`方法，并传递新`todo`的描述，如下图所示：

```ts
        addTodo() 
        { 
              if (this.newTodoText.trim().length) 
              { 
                  this.todoService.add(this.newTodoText); 
                  this.newTodoText = ''; 
              } 
        }
```

1.  引入调用`TodoService`的`remove()`方法通过传递要移除的`todo`对象来移除该对象的`remove()`函数，如下所示：

```ts
       remove(todo: Todo) 
       { 
              this.todoService.remove(todo); 
       } 
```

1.  引入调用`TodoService`的`removeCompleted()`方法来删除所有已完成的待办事项的`removeCompleted()`函数：

```ts
      removeCompleted() 
      { 
            this.todoService.removeCompleted(); 
      } 
```

1.  引入调用`TodoService`的`toggleCompletion()`方法来切换`todo`项的完成状态值的`toggleCompletion()`函数：

```ts
      toggleCompletion(todo: Todo) 
      { 
             todo.completed = !todo.completed; 
      } 
AppComponent is this:
```

```ts
import { Component } from '@angular/core'; 
import { Todo } from './todo'; 
import { TodoService } from './todo.service'; 
@Component({ 
    selector: 'my-app', 
    templateUrl: './app.component.html', 
    providers: [TodoService] 
}) 
export class AppComponent { 
    todos: Array<Todo>; 
    todoService: TodoService; 
    newTodoText = ''; 
    title = 'Test My Todo App'; 
    constructor(todoService: TodoService) { 
        this.todoService = todoService; 
        this.todos = todoService.todos; 
    } 
    removeCompleted() { 
        this.todoService.removeCompleted(); 
    } 
    toggleCompletion(todo: Todo) { 
        this.todoService.toggleCompletion(todo); 
    } 
    remove(todo: Todo) { 
        this.todoService.remove(todo); 
    } 
    addTodo() { 
        if (this.newTodoText.trim().length) { 
            this.todoService.add(this.newTodoText); 
            this.newTodoText = ''; 
        } 
    } 
} 
```

现在我们已经准备好了`AppComponent`。此`AppComponent`的模板定义在一个模板文件`app.component.html`中。

### 编写 AppComponent 的规范

让我们使用 Jasmine 来编写测试 `AppComponent` 的规范：

1.  创建一个`app.component.spec.ts`文件来为`AppComponent`编写规范或测试。

1.  从 Angular 核心中导入模块，例如 `async`、`ComponentFixture`、`TestBed`、`FormsModule`、`By`、`DebugElement` 和 `AppComponent`。

1.  写以下的`describe`全局函数并声明必要的变量：

```ts
     describe('AppComponent (templateUrl)', () => {
       let comp:    AppComponent; 
       let fixture: ComponentFixture<AppComponent>; 
       let de:      DebugElement; 
       let el:      HTMLElement; 
     });
```

1.  然后，创建两个`beforeEach`函数：一个用于编译模板和 CSS，另一个用于获取组件的实例。代码段如下所示：

```ts
    // async beforeEach 
    beforeEach(async(() => { 
        TestBed.configureTestingModule({ 
          imports: [FormsModule], 
          declarations: [ AppComponent ], // declare the     
    test component 
        }) 
        .compileComponents();  // compile template and css 
     })); 
     // synchronous beforeEach 
     beforeEach(() => { 
        fixture = TestBed.createComponent(AppComponent); 
        comp = fixture.componentInstance; // AppComponent     
    test instance 
        // query for the title <h1> by CSS element    
           selector 
        de = fixture.debugElement.query(By.css('h1')); 
        el = de.nativeElement; 
     }); 
```

对于每个测试，我们可能会重复相同的代码来初始化或清除一些对象。为了简化开发者的工作，Jasmine 提供了在执行每个测试方法之前和之后运行的Â `beforeEach` 和 `afterEach` 全局功能。

1.  最后，添加`it`测试或规范函数来验证期望，如下所示：

```ts
    it('no title in the DOM until manually call     
    `detectChanges`', () => { 
    expect(el.textContent).toEqual(''); 
    }); 
    it('should display original title', () => { 
        fixture.detectChanges(); 
        expect(el.textContent).toContain(comp.title); 
    });
    it('should display a different test title', () => { 
        comp.title = 'Test My Todo'; 
        fixture.detectChanges(); 
        expect(el.textContent).toContain('Test My Todo'); 
    }); 
app.component.spec.ts is as follows:
```

```ts
import { async, ComponentFixture, TestBed } from '@angular/core/testing'; 
import { FormsModule } from '@angular/forms'; 
import { By }              from '@angular/platform-browser'; 
import { DebugElement }    from '@angular/core'; 
import { AppComponent } from './app.component'; 
describe('AppComponent (templateUrl)', () => { 
  let comp:    AppComponent; 
  let fixture: ComponentFixture<AppComponent>; 
  let de:      DebugElement; 
  let el:      HTMLElement; 
  // async beforeEach 
  beforeEach(async(() => { 
    TestBed.configureTestingModule({ 
      imports: [FormsModule], 
      declarations: [ AppComponent ], // declare the test component 
    }) 
    .compileComponents();  // compile template and css 
  })); 
  // synchronous beforeEach 
  beforeEach(() => { 
    fixture = TestBed.createComponent(AppComponent); 
    comp = fixture.componentInstance; // AppComponent test instance 
    // query for the title <h1> by CSS element selector 
    de = fixture.debugElement.query(By.css('h1')); 
    el = de.nativeElement; 
  });
  it('no title in the DOM until manually call `detectChanges`', () => { 
    expect(el.textContent).toEqual(''); 
  });
  it('should display original title', () => { 
    fixture.detectChanges(); 
    expect(el.textContent).toContain(comp.title); 
  });
  it('should display a different test title', () => { 
    comp.title = 'Test My Todo'; 
    fixture.detectChanges(); 
    expect(el.textContent).toContain('Test My Todo'); 
  }); 
}); 
```

### 运行 AppComponent 的规范或测试

在命令行窗口中执行`npm test`命令，应用程序将启动，并为您运行测试。以下截图显示了针对`AppComponent`运行的测试或规范的结果：

![](img/image_08_002.png)

AppComponent 的测试结果

## 单元测试一个模拟服务

* * *

通过将服务注入到组件中保持组件的精简，使我们能够使用模拟服务编写单元测试。我们可以通过模仿服务行为使用其接口来模拟注入的服务：

```ts
class MockTodoService extends TodoService   {   
    getPending() {   
        return [];   
    }   
}      
```

在这里，我们通过扩展和覆盖`getPending`方法创建了一个现有待办服务的模拟，以返回一个空数组。

我们可以使用 `testBed` 来测试这一点，指示如何使用模拟服务 `MockTodoService` 而不是实际服务 `TodoService`，如下所示：

```ts
beforeEach(async(() => {   
      TestBed.configureTestingModule({   
        providers: [   
        {   
            provide: TodoService,   
            useClass:   MockTodoService   
        }   
    ]})   
    .compileComponents();   
}));   
```

在这里，我们指示如何使用 `MockTodoService` 而不是 `TodoService`，并且可以跳过测试的结果，如下所示：

```ts
it('should return empty array   when getPending method is fired', () => {   
   let service =   fixture.debugElement.injector.get(TodoService);   
   spyOn(service, 'getPending').and.returnValue([]);        
});
```

这里，它从 fixture 中获取模拟服务Â `MockTodoService`，并添加一个 spyOn 覆盖，假装列表中没有待办事项。

## 总结

* * *

万岁！我们学会了为 Angular 应用程序编写自动化测试的基础知识。

我们从介绍 Jasmine 框架开始这一章，学习如何编写有效的测试并自动运行它们。然后，我们学习了如何使用 Jasmine 框架测试组件和服务，并断言应用程序的行为。最后，我们讨论了模拟服务并使用 spyOn 进行测试。

在下一章中，我们将讨论一些关于 Angular 和 .NET Core 的新主题。
