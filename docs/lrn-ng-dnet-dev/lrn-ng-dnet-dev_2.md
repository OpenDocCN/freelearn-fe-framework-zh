## 第二章：Angular 构建模块 - 第一部分

本章将详细介绍 Angular 架构的核心构建模块。

在本章中，我们将涵盖以下主题：

+   模块

+   组件

+   装饰器和元数据

+   模板

+   绑定

+   指令

+   依赖注入

## 模块（NgModules）

* * *

模块是实现不同功能的单个实现单元。通过多个模块的集合来实现复杂的应用程序。实现模块模式有助于避免变量和方法的全局冲突。JavaScript 通过实现模块模式将私有方法和公共方法封装在单个对象中。模块模式在 JavaScript 中使用闭包来实现封装。JavaScript 不支持访问修饰符；然而，使用函数作用域可以实现相同的效果。所有的 Angular 应用都是模块化的。我们通过创建许多模块来开发 Angular 应用。我们开发模块来封装独立且具有单一职责的功能。一个模块导出该模块中可用的类。Angular 模块称为`NgModules`。在任何 Angular 应用程序中都会至少存在一个 Angular 模块：根模块，它被表示为`AppModule`。`AppModule`是一个被`@NgModule`装饰的类。

```ts
AppModule class:
```

```ts
import { NgModule }      from '@angular/core'; 
import { BrowserModule } from '@angular/platform-browser'; 
@NgModule({ 
  imports:      [ BrowserModule ], 
  providers:    [ Logger ], 
  declarations: [ AppComponent ], 
  exports:      [ AppComponent ], 
  bootstrap:    [ AppComponent ] 
}) 
export class AppModule { } 
```

在上述代码中，从`@angular/core`导入的`NgModule`被装饰为`AppModule`类。请注意，`NgModule`具有一些重要属性，如 imports、exports、providers、declarations 和 bootstrap。

元数据声明应该分配给视图类，如组件、指令和管道，这些类属于该模块。元数据的 exports 将被分配给在组件模板中可用的组件、指令或管道。元数据的 imports 应该分配给组件模板中使用的导出类。元数据 provider 将分配给在整个应用程序中使用或访问的服务。它创建分配的服务的实例，并将其添加到服务的全局集合中，以便这些服务可以在整个 Angular 应用程序中被消耗。元数据 bootstrap 分配给负责渲染应用程序主视图的根组件。

![](img/image_02_001.png)

Angular 模块

一个示例`AppComponent`类如下所示。该`export`语句公开了组件，并且`AppComponent`类可被应用程序中的其他模块访问：

```ts
export class AppComponent { } 
```

类是包含对象方法和变量定义的模板。对象是类的一个实例，因此它可以保存变量的真实值，并且方法可以针对实际值执行操作。注意，当前版本的 JavaScript 不支持类。它是一种无类语言。在 JavaScript 中，一切都是对象，并且函数被用来模拟类。ECMAScript 6 通过在 JavaScript 中引入类来引入对 JavaScript 基于原型的继承的一种语法糖。

在这里，我们利用了 TypeScript 作为 JavaScript 的超集的能力。语句中的 export 关键字表示我们正在向应用程序的其他模块导出或公开一个`AppComponent`类。

假设我们已经把这个组件保存在一个名为`app.component.ts`的文件中。为了访问或引用被公开的`AppComponent`类，我们需要在我们将要访问的文件中导入它。下面的语句完成了这个操作：

```ts
import {AppComponent} from './app.component';
```

在这里，语句中的 import 关键字表示我们正在导入一个被公开的类：`AppComponent`。from 关键字表示或指向导入组件所在的文件或模块。例如，在我们的情况下，它是`app.component.ts`。一个模块名是组件的文件名去掉扩展名；所以，在这里，模块名是`app.component`。我们用相对文件路径(`./`)开头的模块文件名，并表示相同的文件夹。

模块也可以包含其他模块的集合，这样的模块被称为库模块。Angular 本身有许多库模块。一些库模块是核心，公用，路由等。我们从`@angular/core`库模块中导入`Component`，这是我们大多数情况下使用的主要模块：

```ts
import {Component} from '@angular/core'; 
```

所有的 Angular 库模块都将在 from 子句中以没有相对文件路径的方式提及。

## 组件

* * *

AngularJS 具有控制器，作用域和指令来处理视图，绑定数据，并通过更新数据来响应事件。在 Angular 中，组件取代了 AngularJS 的控制器，作用域和指令。

Angular 引入了支持面向对象组件模型的组件，以编写更干净的代码。一个组件是一个简单的类，它保持管理相关模板或视图的逻辑。一个简单的组件类如下所示：

```ts
Class FirstComponent { 
} 
```

在组件类中，我们将属性和方法暴露给模板或视图。组件属性可以为模板或视图提供数据，并允许用户修改属性值。根据用户在视图上的操作，可以调用组件方法。

![](img/image_02_002.png)

Angular 组件 FirstComponent

正如你所看到的，上述代码创建了一个名为`**`FirstComponent`**`的简单 JavaScript 类。也许你想知道一个 JavaScript 普通类如何被视为组件，模板如何与这个类被连接起来。为了实现这一点，Angular 利用了 TypeScript 语法来按照 2015 年的 ES6 规范对`**`FirstComponent`**`类进行注释，将其声明为组件并将模板与选择器的标识符进行连接。下面的代码展示了带有注释的组件类，声明类为组件并用模板将其与标记标识符连接起来：

```ts
import { Component } from '@angular/core';
@Component({
  selector: 'first-component',
  template: `<h1>{{getGreetingPhrase()}} {{name}}</h1>`,
})
export class FirstComponent {
  name: string;
  constructor() {
  this.name = 'Rajesh Gunasundaram';
}
getGreetingPhrase() {
  return 'Hello Author,';
}
}
getGreetingPhrase() function to fetch and display the phrase to greet, and it will also access the name property to display the name. The @Component()Â preceding the FirstComponent class is the annotation that denotes this class is a Component, and the markup identifier first component for this component is assigned to the metadata of @Component named selector.
```

也许你会惊讶地发现我们没有使用`$scope`来暴露`FirstComponent`的属性和方法。在这里，我们的组件实例化并可在模板或视图中使用。因此，我们可以访问该实例的任何属性；同时，我们可以根据用户在视图或模板中的操作或输入调用实例中的方法。组件实例提供了有关该实例的封装数据，类似于 AngularJS 中的隔离作用域。

当根组件的模板具有另一个组件选择器的特殊标记时，Angular 中的组件可以继承，并且这也使子组件能够访问其父级和同级组件。

![](img/image_02_003.png)

应用程序的组件层次结构

### 组件的生命周期

Angular 管理组件的生命周期。Angular 负责创建和渲染组件及其子组件，并在从 DOM 中删除之前销毁它们。Angular 跟踪组件属性值的变化。以下是 Angular 组件的生命周期事件按调用顺序给出：

+   **OnChanges**: 当绑定值发生变化时会触发此事件。此方法将有权访问旧值和新值。

+   **OnInit**: 这个事件在由于绑定值的更改而执行`OnChanges`事件之后触发。

+   **DoCheck**: 这个事件会在检测到每次变化时被触发，开发人员可以编写自定义逻辑来检查属性的变化。

+   **AfterContentInit**: 当指令的内容完全初始化后将触发此事件。

+   **AfterContentChecked**: 这个事件将在指令内容被检查后触发。

+   **AfterViewInit**: 当组件模板完全初始化后将触发此事件。

+   **AfterViewChecked**: 这个事件将在组件模板被检查后触发。

+   **OnDestroy**: 这个事件将在销毁指令或组件之前触发。

您可以实现所有这些事件，也可以只实现组件所需的特定事件。

## 装饰器和元数据

* * *

如您在上一节中所看到的，我们为组件定义了 JavaScript 普通类，并对其进行了一些信息注释，以通知 Angular 框架该类是一个组件。

我们利用了 Typescript 语法，并使用装饰符功能将类附加元数据。为了使一个类成为组件，我们添加`@Component`装饰符，如下所示：

```ts
@Component({...})
export class FirstComponent {...}
FirstComponent class has been decorated as a component.
```

现在，让我们使用装饰符语法为`FirstComponent`类附加元数据：

```ts
@Component({ 
   selector: 'first-component', 
   templateUrl: 'app/first.component.html' 
}) 
export class FirstComponent {...} 
```

在这里，我们已经添加了诸如选择器和`templateUrl`之类的元数据。组件中配置的选择器元数据告诉 Angular 在遇到`<first-controller>`标记时创建该组件的实例：

```ts
<first-controller></first-controller> 
```

`templateUrl`提供了组件渲染的模板文件的 URL。当您运行应用程序时，`<first-controller>`标记将被`templateUrl`中引用的模板内容替换。此元数据实际上是`@Component`装饰符的一个参数，而装饰符是一个函数。

通过装饰符添加元数据，我们实际上告诉 Angular 如何处理定义的类。组件、模板和元数据一起构成一个视图。

## 模板

* * *

当对组件进行注解时，您可能已经注意到我们为视图或模板添加了内联标记。我们还可以添加一个模板 URL，将视图或模板标记隔离在一个单独的 HTML 文件中，而不是将其作为内联视图或模板。

模板由 HTML 标记组成，向 Angular 提供有关呈现组件的信息。以下代码行中给出了一个简单的模板内容。它呈现了书名和出版商：

```ts
<div> 
  The Name of the book is {{bookName}} and is published by {{pubName}}. 
</div> 
```

### 内联模板

内联模板在需要呈现非常简单内容（例如一行）时使用。在这种情况下，内联视图或模板将直接在注释中定义：

```ts
@Component({ 
  selector: 'first-component', 
  template: "<div>{{getGreetingPhrase()}} {{name}}</div>" 
}) 
```

### 隔离模板

隔离模板主要用于模板包含更多内容的情况。在这种情况下，内容将被移到一个单独的文件中，并将 HTML 文件的 URL 分配给`templateUrl`，如下所示：

```ts
@Component({ 
  selector: 'first-component', 
  templateUrl: FirstPage.html' 
}) 
```

### 本地模板变量

Angular 允许创建模板作用域变量，在模板中移动数据：

```ts
<div *ngFor="let todo of todos"> 
  <todo-item [todo]="todo"></todo-item> 
</div> 
```

在前面的模板标记中，我们使用 let 关键字声明了一个本地变量 todo。然后，我们遍历 todos 集合变量；每个 todo 项目都被分配给 todo，并且可以在`<todo-item>`中使用。

也可以使用本地模板变量来保存 DOM 元素。以下代码显示了作者将保存输入元素本身，并且可以使用 author.value 访问元素的值：

```ts
<!-- author refers to input element and passes its `value`to the event handler --> 
<input #author placeholder="Author Name"> 
<button (click)="updateAuthor(author.value)">Update</button> 
```

## 绑定

* * *

绑定技术将使您能够将数据绑定到模板，并允许用户与绑定的数据进行交互。Angular 绑定框架负责将数据呈现到视图，并根据用户在视图上的操作进行更新。

以下截图让您快速了解了 Angular 中各种绑定技术。我们将逐个详细介绍每种绑定技术：

![](img/image_02_004.png)

各种绑定语法

### 单向绑定

诸如`插值`、`属性`、`属性`、`类`和`样式`等绑定类型支持从数据源（从组件公开）到视图或模板的单向数据流。让数据从组件属性或方法流向模板的模板标记在下表中给出（单向绑定）：

| **模板代码** | **描述** |
| --- | --- |
| `{{表达式}}` | 这显示了从数据源构建的表达式 |
| `[目标]` = "表达式" | 这将数据源的表达式分配给目标属性 |
| `bind-target` = "表达式" | 这将数据源的表达式分配给绑定目标属性 |

让数据从模板流向组件属性或方法的模板标记在下表中给出（单向绑定）：

| **模板代码** | **描述** |
| --- | --- |
| `(目标)` = "语句" | 这将数据源的表达式分配给目标属性 |
| `on-target` = "语句" | 这将数据源的表达式分配给绑定目标属性 |

### 内插绑定

内插是Â Angular 的主要特性之一。您可以将任何属性值或表达式插值到任何 HTML 元素的内容中，例如`div`和`li`。您可以通过双大括号`{{和}}`来实现此目的，如下行代码所示：

```ts
<div>Hello, {{authorName}}!</div>
```

在这里，我们将`authorName`插值到`div`标签的内容中。这是一种单向绑定，其中数据从组件属性或方法流向模板。

### 属性绑定

属性绑定用于将组件属性绑定到 HTML 元素属性：

```ts
<div [hidden]="hidePubName>Packt Publishing</div> 
hidePubName component property to the div property hidden. This is also a one-way binding where the data flows from a component property to a template.
```

### 事件绑定

HTML 元素具有各种 DOM 事件，当事件触发时将触发。例如，单击按钮时将触发点击事件。我们挂钩事件监听器以便在事件触发时得到通知：

```ts
<button (click)="doSomething()">Do Something</button>
```

前面的 Angular 代码片段将事件名称放在括号中，需要挂接事件监听器，以便在触发单击事件时调用它。

### 双向绑定

Angular 已经从其框架中移除了一个核心功能，这是 AngularJS 诞生的一个主要原因，即双向绑定。因此，默认情况下不支持双向绑定。现在，让我们看看如何在 Angular 中实现双向绑定。

Angular 结合属性和事件绑定，使我们能够实现双向绑定，如下面的代码所示：

```ts
<input [(ngModel)]="authorName">
ngModel is wrapped with parentheses and then with square brackets. The parentheses indicate that the component property is tied up with the ngChange event, and the square brackets indicate that the component property is assigned to a value property of the input element. So, when the value of the input element changes, it fires up the change event that eventually updates authorName with the new value from the event object. ngModel in the markup is the built-in directive in Angular that unifies property and event binding.
```

可以帮助数据双向流动的模板标记，从模板到组件，从组件到模板，如下表所示（双向绑定）：

| **模板代码** | **描述** |
| --- | --- |
| `[(目标)]` = "表达式" | 这将数据源的表达式分配给目标属性 |
| `bindon-target` = "表达式" | 这将数据源的表达式分配给绑定目标属性 |

## 指令

* * *

我们详细介绍了 Angular 组件及其装饰方式。`@Component` 本身是一个带有在元数据中配置的模板的指令。因此，一个没有模板的指令是一个组件，而 `@directive` 在 Typescript 中用于附加元数据。

### 结构指令

结构指令处理通过添加新元素、删除现有元素和用新元素替换现有元素来修改 DOM 中的元素。下面的标记显示了两个结构指令：`*ngFor` 和 `*ngIf`：

```ts
<div *ngFor="#todo of todos"></div> 
<todo-item *ngIf="selectedTodo"></todo-item> 
```

`*ngFor` 遍历 todos 集合中的每个项目，并为每个项目添加一个 `div` 标签。而 `*ngIf` 仅在 selectedTodo 可用时呈现 `<todo-item>`。

### 属性指令

属性指令将像属性一样添加到现有的 HTML 元素中，并且可以修改或扩展 HTML 元素的行为。例如，如果将 ngModel 指令添加到输入元素中，它将通过更新其 value 属性和响应更改事件来扩展它：

```ts
<input [(ngModel)]="author.name">
```

除了使用现有的指令，我们还可以编写自己的指令，比如 `ngSwitch`、`ngStyles` 和 `ngClass`。

## 依赖注入

* * *

依赖注入是一种处理依赖关系并解决它们的设计模式。依赖项的实例将传递给依赖项，以便使用它。如果客户端模块或类依赖于一个服务，它需要在使用之前创建该服务的一个实例。我们可以使用依赖注入模式注入或传递服务的实例给客户端，而不是客户端模块构建服务。

应用依赖注入使我们能够创建一个不知道要构建的服务和实际消费的服务的客户端。客户端只会知道服务的接口，因为它需要知道如何使用服务。

### 为什么依赖注入？

假设我们正在创建一个 `Mobile` 类，并且它依赖于 `camera` 和 `internet` 连接。

![](img/image_02_005.png)

Mobile 类的代码片段

```ts
Camera and Internet areÂ created in the constructor of the MobileÂ class. These are the features of Mobile. Instead of requesting for the feature, the Mobile class created the feature by itself. This means that the Mobile class is bound to a certain version of features, such as a 2 MP camera and 2G Internet. Later, if we want to upgrade the camera to 20 MP and Internet to 3G or 4G, we need to rewrite the code of the Mobile class.
```

`Mobile` 类依赖于 `Camera` 和 `Internet`，这增加了测试的难度。我们只能用 2G 互联网和 2 MP 相机来测试 Mobile，因为我们无法控制依赖，因为 `Mobile` 类通过自身负责依赖的实例。

现在，让我们修改构造函数，接收 `Camera` 和 `Internet` 的实例作为参数，如下面的代码行所示：

```ts
constructor(public camera: Camera, public internet: Internet) { } 
```

现在，`Mobile` 类将不再创建 `Camera` 或 `Internet` 的实例。它只消耗从构造函数参数中收到的 `Camera` 或 `Internet` 的实例。这意味着我们将依赖项移到了构造函数中。客户端可以通过向构造函数传递 `Camera` 和 `Internet` 的实例来创建一个 `Mobile` 类，如下面的代码片段所示：

```ts
// Simple mobile with 2MP camera and 2G internet. 
var mobile = new Mobile(new Camera2MP(), new Internet2G()); 
```

您可以看到`Camera`和`Internet`的定义已经与`Mobile`类解耦。只要客户端传递的`Camera`和`Internet`类型符合`Camera`和`Internet`的接口，我们就可以传递任何类型的具有不同百万像素的摄像头和不同带宽的互联网，比如 2G，3G 和 4G。

```ts
// an advanced mobile with 20MP camera and 4G internet. 
var mobile = new Mobile(new Camera20MP(), new Internet4G()); 
```

在`Mobile`类中没有改变，以适应 20 MP 摄像头和 4G 互联网的依赖性。`Mobile`类更容易通过各种组合的`Camera`和`Internet`进行测试，因为我们对依赖性有完全的控制。我们还可以在测试中使用模拟技术，并将`Camera`和`Internet`的模拟传递给构造函数，以便所有必要的操作都将针对`Camera`和`Internet`的模拟进行。

### 注入器的作用

我们刚刚了解了什么是依赖注入，以及它如何从外部客户端接收依赖性而不是自己创建它们。然而，客户端需要更新其代码，以传递 20 MP 摄像头和 4G 互联网依赖的实例。任何想要使用`Mobile`类的客户端都必须创建`Camera`和`Internet`的实例，因为`Mobile`类依赖于它们。我们从`Mobile`类中消除了创建依赖实例的责任，并将其移动到将使用`Mobile`类的客户端。

现在，成为可怜的客户端的问题，要创建`Camera`和`Internet`的实例。因此，为了减少客户端创建依赖实例的额外工作，我们需要注入器来负责为客户端组装所需的`Camera`和`Internet`的实例。依赖注入框架有一个叫做注入器的东西，我们在其中注册我们的类，比如`Mobile`。然后我们可以请求注入器为我们创建`Mobile`的实例。注入器将负责解析依赖关系并创建`mobile`，如下面的代码行所示：

```ts
var mobile = injector.get(Mobile); 
```

### 在 Angular 中处理依赖注入

Angular 有自己的依赖注入框架，并且我们将通过一个示例看到它如何处理依赖注入。

首先，我们将在`app/todos/todo.ts`下创建一个`Todo`类，该类具有诸如`id`，`description`和`isCompleted`等属性，如下截图所示：

![](img/image_02_006.png)

Todo 类的代码片段

然后，创建一个`TodoListComponent`组件，并添加一个属性来保存从注入的`TodoService`检索到的待办事项集合。当依赖注入框架实例化`TodoListComponent`时，服务将被注入到构造函数中。您将在第三章*Angular 构建块-第二部分*中了解更多关于服务的内容。

![](img/image_02_007.png)

TodoListComponent 类的代码片段

代码是使用 Typescript 编写的，当它将代码编译为 JavaScript 时，会包含有关类元数据的信息，因为类被装饰为 `@component`。这个类元数据包含了关联`todoService`参数和`TodoService`类的信息。这使得 Angular 注入器在创建新的 `TodoListComponent` 时能够注入 `TodoService` 的实例。

在我们的代码中，我们不需要显式调用注入器来注入服务。相反，Angular 的自动依赖注入会处理它。当 Angular 遇到通过 HTML 标记或通过路由导航到组件时遇到`<todo-list>`选择器时，注入器会在实例化组件的同时被隐式调用。

现在，我们将创建 `TodosComponent`，在 `@Component` 指令的 providers 参数中注册 `TodoService`。`TodoService` 的实例在`TodosComponent`中和它的所有子项中都可以被注入使用。

```ts
import { Component } from '@angular/core';
import { TodoListComponent } from './todo-list.component';
@Component({
  selector: 'my-todos',
  template: '<h2>Todolist</h2><todo-list></todo-list>',
  providers: [TodoService],
  directives: [TodoListComponent]
})
export class TodosComponent { }
```

现在，让我们创建返回待办事项集合的 `TodoService` 服务。

![](img/image_02_008.png)

TodoService 的代码片段

在生产环境的 `TodoList` 应用程序中，`TodoService` 中的 `getTodos` 方法将进行一个 HTTP 请求来获取待办事项列表。在基本情况下，我们从`mock-todos`中返回待办事项的集合。

最后，我们需要创建 `mock-todos`，其中包含待办事项的集合，如下面的屏幕截图所示：

![](img/image_02_009.png)

`mock-todos` 的代码片段

该文件用作内存中的集合，以保存待办事项，并且可以在导入该文件的组件中使用。这种方法适用于开发阶段，但在生产阶段需要从远程服务器获取待办事项。

在 VS Code 中按下*F5*运行应用程序，您将得到 Angular TodoList 应用程序的输出，如下面的屏幕截图所示：

![](img/todoapp.png)

在浏览器中运行的 TodoList 应用程序

## 总结

* * *

哇！到现在为止，您一定已经学到了很多关于 Angular 架构的核心构建块。我们从 e 开始，讨论了它如何封装了独立且具有单一职责的功能。然后，您学习了组件的概念，以及它们如何取代了 AngularJS 中的控制器、作用域和指令。您还了解了装饰器和元数据，它们利用了 Typescript 语法将普通的 JavaScript 类转换为 Angular 组件。然后，我们讨论了模板以及内联模板和独立模板之间的区别。您还学习了如何在模板中实现各种绑定技术。稍后，我们通过指令讨论了指令以及指令与组件的区别。最后，您学习了一个最受欢迎的设计模式之一，依赖注入，以及它如何被 Angular 处理。

在下一章中，我们将讨论 Angular 架构中剩下的部分。
