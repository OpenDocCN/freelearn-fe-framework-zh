# 快速回顾简单应用的数据服务

欢迎来到本书的第一章。你之所以选择这本书，可能是因为你在设置 Angular 应用架构时遇到了问题。你的应用在成长过程中，你逐渐感觉到你失去了对应用在某个特定时刻所知道的内容的追踪，这就是我们所说的应用状态。可能还有其他问题，比如应用的一部分可能没有与它们所知道的内容达成一致。一个部分发生的更新可能没有被应用到其他部分，你挠头思考，这是否太难了，是否有更好的解决方案？

很可能你只是因为听说 NgRx 是结构化应用的一种方式而选择这本书，你好奇并想了解更多。

无论是什么动机驱使你阅读这本书，本书都是关于学习如何结构化你的应用，以及如何以所有应用部分都同意的方式设置和传达应用的状态及其变化。NgRx 的底层架构模式是 Redux，它将数据限制在仅一个地方，并确保数据仅向一个方向流动。我们将在本书的专门章节中更深入地探讨 Redux。

要达到掌握 NgRx 的水平，我们首先需要在学习过程中掌握一些范式和模式。我们需要建立一个良好的知识基础。良好的基础包括学习诸如**函数式响应式编程**（**FRP**）、架构模式 Flux 以及一种关于异步概念的新颖且令人兴奋的思考方式，即 Observables。

那么，为什么这些对我们学习掌握 NgRx 的旅程是相关的？Flux 模式与 Redux 有很多共同之处，正是它的不足导致了 Redux 的创建。NgRx 本身是使用 RxJS 实现的，它促进了函数式响应式编程风格。所以你看，我们即将探索的基础知识都有助于我们掌握 NgRx 的理论和动机。

在本章中，我们通过讨论众所周知的**模型-视图-控制器**（**MVC**）模式为本书奠定基础。为了验证我们是否理解 MVC 模式，我们使用 Angular 框架来简化问题。虽然谈论架构很有趣，但如果你不看到它应用于现实事物，可能很难掌握。

我们继续深入探讨 Angular 应用的工作流程及其依赖注入机制。在结束本章之前，我们还将了解如何通过 API 获取数据，因为毕竟数据是从那里来的，应该流向那里。

在本章中，我们将：

+   描述 MVC 模式的构建块

+   描述 Angular 中的 MVC 以及帮助支持它的核心结构

+   复习 HTTP 服务以及如何处理 Ajax

# 模型-视图-控制器 – 我们都熟知的模式

无论你是一名程序员一年还是 20 年，你几乎肯定以某种形式遇到过 MVC 模式。这个模式本身，MVC，由三个相互关联的部分组成：模型、视图和控制器。了解所有部分的重要性不如了解它解决的问题。它通过解耦视图逻辑、数据逻辑和业务逻辑来解决问题。MVC 模式催生了以下内容：

+   **模型-视图-适配器**（**MVA**）

+   **模型-视图-呈现器**（**MVP**）

+   **模型-视图-视图模型**（**MVVM**）

# 内聚和耦合 – 建立共同语言

没有像 MVC 这样的模式，你的代码可能很难维护，因为它可能具有低内聚和高耦合。这些是华丽的词藻，那么我们的意思是什么？内聚是关于关注点和类应该做什么。内聚越低，一个类执行的不同事情就越多，因此它没有明确的意图知道它应该做什么。

以下代码展示了当一个类具有低内聚性时会发生什么；它做的不仅仅是存储发票数据，例如能够将日志记录到文件或与数据库通信：

```js
Invoice
  details
  total
  date
  validate()
  print()
  log()
  saveToDatabase()
```

现在我们已经引入了新的专用类，并将方法从`Invoice`类中移出，以确保每个类现在都具有高内聚，即更专注于做好一件事。因此，我们现在有`Invoice`、`Printer`、`Logger`和`InvoiceRepository`这些类：

```js
Invoice
  details
  total
  date
  validate()

Printer
  print(document)

Logger
  log()

InvoiceRepository
  saveToDatabase(invoice)
```

我在这里试图说明的是，一个类应该只做好一件事。这可以通过将缺乏关注的`Invoice`类拆分为四个不同的类来展示，每个类都只专注于做好一件事。

因此，我们解决了内聚/关注点的问题。那么耦合呢？耦合是关于一个软件元素与另一个软件元素连接的强度。最终，耦合越高，更改就越困难/繁琐。让我们看看以下用 Java 编写的具有高耦合的示例：

```js
// cohesion-and-coupling/invoice-system.java

class Printer {
  print(Invoice invoice) {
    String total ="";
    total += invoice.getTitle();
    total += invoice.getDetails();
    total += invoice.getDate();
    //print 'total'
  }
}

class Invoice {
  String title;
    String details;
    int total;
    Date date;
    public String getTitle() { return this.title; }
    public String getDetails() { return this.details; }
    public String getDate() { return this.date; }
}

public class Program {
  private Printer printer = new Printer();
  public void run(ArrayList list) {
    for(int i=0; i< list.length; i++) {
      Object item = list.getItem(i);
      if(item instanceof Invoice) {
        Invoice invoice = (Invoice) item;
        printer.print(invoice);
      }
    }
  }

  public static void main(String [] args) {
    ArrayList list = new ArrayList();
    list.add(new Invoice());
    Program program = new Program();
    program.run( list );
  }
}
```

这段代码存在多个问题，尤其是如果你打算以任何方式更改代码。假设我们想要打印一封电子邮件。我们可能会想，我们需要一个`Email`类，并需要向`Printer`类添加另一个`print()`方法重写。我们还需要向`Program`类添加分支逻辑。此外，测试`Program`类无法不引起副作用：调用`run()`方法会导致实际调用打印机。我们现在的测试方式是每次代码更改时都运行测试，因为我们正在开发程序，这可能导致代码更改非常频繁。我们可能会在开发代码的过程中打印出数千张纸。因此，我们需要在开发代码和测试时将自己与副作用隔离开。我们最终想要测试的是代码的行为是否正确，而不是物理打印机是否看起来工作正常。

在下面的代码中，我们看到一个高耦合的例子。我们添加了另一个类型 `Email`。这样做是为了看到这样做的影响，即我们需要同时向几个地方添加代码。必须这样做是一个代码坏味的迹象。你需要做的更改越少，通常越好：

```js
// cohesion-and-coupling/invoice-systemII.java

class Email {
  String from;
  String to;
  String subject;
  String body;
  String getSubject() { return this.subject; }
  String getFrom() { return this.from; }
  String getTo() { return this.to; }
  String getBody() { return this.body; }
}

class Invoice {
  String title;
  String details;
  int total;
  Date date;
  String getTitle(){ return this.title; }
  String getDetails() { return this.details; }
  Date getDate() { return this.date; }
}

class Printer {
  print(Invoice invoice) {
    String total ="";
    total += invoice.getTitle();
    total += invoice.getDetails();
    total += invoice.getDate();
    //print 'total'
  }

  print(Email email) {
    String total ="";
    total += email.getSubject();
    total += email.getFrom();
    total += email.getTo();
    total += email.getBody();
  }
}

class Program {
  private Printer printer = new Printer();
  run(ArrayList list) {
    for(int i=0; i< list.length; i++) {
      Object item = list.getItem(i);
      if(item instanceof Invoice) {
        Invoice invoice = (Invoice) item;
        printer.print( invoice );
      } else if( item instanceof Email ) {
        Email email = (Email) item;
        printer.print( email );
      }
    }
  }

  public static void main(String [] args) {
    ArrayList list = new ArrayList();
    list.add( new Invoice() );
    list.add( new Email() );
    Program program = new Program();
    program.run( list );
  }
}
```

因此，让我们稍微调整一下代码：

```js
// cohesion-and-coupling/invoice-systemIII.java

class Email implements IPrintable {
  String from;
  String to;
  String subject;
  String body;
  String getSubject() { return this.subject; }
  String getFrom() { return this.from; }
  String getTo() { return this.to; }
  String getBody() { return this.body; }
  public String getContent() {
    String total = "";
    total += email.getSubject();
    total += email.getFrom();
    total += email.getFrom();
    total += email.getBody();
    return total;
  }
}

class Invoice implements IPrintable {
  String title;
  String details;
  int total;
  Date date;
  String getTitle() { return this.title; }
  String getDetails() { return this.details; }
  String getDate() { return this.date; }
  public String getContent() {
    String total = "";
    total += invoice.getTitle();
    total += invoice.getDetails();
    total += invoice.getDate();
    return total;
  }
}

interface IPrintable {
  String getContent();
}

interface IPrinter {
  print(IPrintable printable);
}

class Printer implements IPrinter {
  print( IPrintable printable ) {
    String content = printable.getContent();
    // print content
  }
}

class Program {
  private IPrinter printer;
  public Program(IPrinter printer) {
    this.printer = printer;
  }

  run(ArrayList<IPrintable> list) {
    for(int i=0; i< list.length; i++) {
      IPrintable item = list.getItem(i);
      printer.print(item);
    }
  }

  public static void main(String [] args) {
    ArrayList<IPrintable> list = new ArrayList<IPrintable>();
    Printer printer = new Printer();
    list.add(new Invoice());
    list.add(new Email());
    Program program = new Program(printer);
  }
}
```

到目前为止，我们已经使我们的程序可以扩展。你怎么说呢？显然，我们已经从 `printer` 中移除了 `printer` 方法。我们还从 `Program` 类的方法 `run` 中移除了切换逻辑。我们还添加了抽象 `IPrintable`，这使得任何可打印的内容都负责告诉打印机其可打印的内容是什么。

你可以清楚地看到，当我们引入 `Document` 和 `Note` 类型时，我们是如何从高耦合转变为低耦合的。它们引起的唯一变化是它们自己被添加并实现了 `IPrintable` 接口。其他什么都不需要改变。成功！

```js
// invoice-systemIV.java

class Document implements IPrintable {
  String title;
  String body; 

  String getContent() {
    return this.title + this.body;
  }
}

class Note implements IPrintable {
  String message;

  String getContent() {
    return this.message;
  }
}

// everything else stays the same

// adding the new types to the list
class Program {
  public static void main(String[] args) {
    list.add(new Note());
    list.add(new Document());
  }
}
```

好吧，所以总结一下我们的更改：

+   我们添加了 `IPrintable` 接口

+   我们简化/移除了 `Program.run()` 方法中的分支逻辑

+   我们让每个可打印类实现 `IPrintable`

+   我们在之前的代码片段末尾添加了一些代码，以展示添加新类型是多么容易

+   我们通过 `Program` 类构造函数注入了 `IPrinter`，以确保我们可以轻松测试 `Program` 类

特别注意，当我们添加 `Document` 和 `Note` 类型时，我们不需要在 `Printer` 或 `Program` 中更改任何逻辑。我们唯一需要做的是添加 `Document` 和 `Notes` 作为类，并确保它们实现了 `IPrintable` 接口。为了强调这一点，*任何对程序的添加都不应该导致代码的整体系统变化*。

让我们重申一下添加 `IPrinter` 的最后一条。可测试性是一个非常好的衡量标准，可以用来判断你的代码是否具有低耦合。如果你依赖于抽象而不是实际类，你就可以轻松地用另一个具体类替换一个具体类，同时保持高级行为。

将 `Printer` 切换到 `IPrinter` 的另一个原因是，当我们测试代码时，可以消除程序中的副作用。副作用是指我们与文件交互、改变状态或通过网络进行通信等情况。测试 `Program` 类意味着我们希望消除实际打印等副作用，而是调用一些虚假的函数，或者每次运行测试时都会有一大堆纸张。因此，为了测试目的实例化我们的 `Program` 类，我们会写一些像这样的事情：

```js
// cohesion-and-coupling/invoice-systemV.java

class FakePrinter implements IPrinter {
  print(IPrintable printable) { System.out.println("printing"); }
}

class Program {
  FakePrinter fakePrinter;
  Program(FakePrinter fakePrinter) {
    this.fakePrinter = fakePrinter;
  }

  public static void main(String[] args) {
    ArrayList<IPrintable> list = new ArrayList<IPrintable>();
    Printer printer = new FakePrinter();
    list.add(new Invoice());
    list.add(new Email());
    Program program = new Program(printer);
  }
}
```

从这段代码中，我们可以看到我们是如何从实例化打印到真实打印机的 `Printer` 类，转变为使用 `FakePrinter` 实例的 `Program` 类。在测试场景中，这正是你想要测试 `Program` 类时你会做的事情。你最关心的可能是 `print()` 方法被正确地调用。

好吧，这已经是一种相当长的表达低耦合的方式了。然而，在谈论模式时，建立诸如耦合和内聚等关键术语的重要性是至关重要的。

# 解释 MVC 的组成部分

回到 MVC 模式。使用该模式意味着我们获得高内聚和低耦合；这是由于代码被分割成具有不同责任的不同层。视图逻辑属于视图，控制器逻辑属于控制器，模型逻辑属于模型。

# 模型

这是应用程序的关键部分。这并不依赖于任何特定的用户界面，而更多地定义了你在其中操作的范围。规则、逻辑和数据都生活在这里。

# 视图

这可以是任何从原生应用视图到柱状图，甚至是网页。关键是它最终显示模型中的数据。可能有不同的视图显示相同的内容，但根据它们的设计对象，它们可能看起来不同。管理员可能看到的视图与用户看到的相同信息完全不同。

# 控制器

这实际上就是网中的蜘蛛。它能够从视图或数据中获取输入并将其转换为命令。

# 交互 – 组件之间的行为

所有这三个提到的组件在相互交谈时表现不同。模型根据命令存储从控制器接收到的数据。视图根据模型中的变化改变其外观。控制器可以根据用户交互向模型发送命令。一个这样的例子是用户决定在基于页面的记录之间浏览。需要根据新的视觉位置检索一组新的数据。

这两个基本流程是大多数基于 MVC 的应用程序中发生的事情：

+   用户交互：控制器向模型发送命令 => 模型发生变化 => 视图被更新

+   视图请求数据：控制器向模型发送命令 => 模型被创建/更改 => 视图被更新

# MVC 概述

关于 MVC 及其许多变体有很多可以说的，但让我们现在就满足于总结我们已识别的模式属性：

+   低耦合

+   高内聚，将表示关注点从模型中分离出来

+   同时开发是可能的；由于存在许多层，人们可以并行地完成任务

+   易于更改；由于事物是分离的，添加未来的概念或进行更改变得更容易

# Angular 中的 MVC 流程

让我们看看以下问题以及我们如何在 Angular 中解决这些问题：

+   创建和渲染模型数据到屏幕

+   学习 MVC 模式如何映射到 Angular 框架

+   学习如何以不同的构建块结构化 Angular 应用程序

+   获取数据/持久化数据

# 模型

Angular 中的模型是一个普通的类，因为我们使用 TypeScript。它可能看起来像以下代码：

```js
// mvc/MvcExample/src/app/product.model.ts

export class Product {
  constructor(
    private id: number,
    private title: string,
    private description: string,
    private created: Date
  ) {}

  method() {}

  anotherMethod() {}
}
```

它是一个普通的 TypeScript 文件，或者更确切地说是一个 ES2015 模块，不要与 Angular 模块混淆。我们将在下一主要部分讨论 Angular 模块是什么，以及它的设置和消费方式。现在，记住模型是一个简单的东西。

# 组件 – 控制器和构建块

在 MVC 的上下文中，组件是 V 和 C，即视图和控制。组件允许你定义一个单独的模板文件或内联模板。模板是视图部分。

在这个上下文中，控制器是一个处理用户交互并从模板显示所需数据的组件类文件。

组件已经成为许多当今流行的框架的核心概念，例如 React、Vue.js 和 Polymer。组件可以接受输入，这些输入可以是数据或方法。它由一段代码和一个 HTML 模板组成，这些模板渲染有趣的数据，并存在于组件中。Angular 中的组件由三个主要部分组成：

+   一个装饰器函数

+   一个类

+   一个模板

一个组件由一个控制器类和一个模板组成。在 Angular 应用程序中，它可以扮演两个不同的角色：要么作为路由的响应者，要么作为构建块。在前一种情况下，当发生新的路由时，Angular 将实例化它，并使用该组件进行响应。在后一种情况下，组件直接由另一个组件作为子组件创建。

我们将在下一部分解释前一段话的含义。

# 路由的第一个响应者

如前所述，组件可以用作路由的响应者。所以，假设应用程序由于用户交互或程序性地路由到 `/products` 路由，Angular 处理这种情况的方式是将 `/products` 路由与一个组件关联。借助组件的类和 HTML 标记，我们能够生成包含我们的标记和数据一起渲染的 HTML 片段。将组件指定为路由的响应者是在定义所谓的路由映射时完成的，如下所示：

```js
// example of what routing might look like

export const appRoutes: Routes = [
  { 
    path: '', 
    component: HomeComponent
  },
  {
    path: 'payments',
    component: ProductsComponent,
    data: { title: 'Products' }
  }
]
```

实质上，路由被定义为具有 `path` 属性的对象，指明我们的路由，以及一个指向响应组件的 `component` 属性。我们可以将其他属性附加到路由上，例如 `data`，以给响应组件提供一些初始数据以进行渲染。

# 作为构建块使用

将组件作为构建块使用意味着它将成为另一个组件模板的一部分。本质上，它将被视为该组件的子组件。这种思维方式相当自然，意味着我们可以将我们的应用程序视为组件的分层树。如前所述，Angular 中的组件由一个控制器类和一个模板组成。一个典型的组件看起来是这样的：

```js
// an example component

@Component({
  selector: 'example-component'
})
export class ExampleComponent {}
```

`@Component`装饰器函数为类添加元数据。这指导 Angular 如何创建组件，以便 Angular 可以将组件放置在 DOM 中。这使得你可以将其用作对路由的响应者，或者作为你自己的自定义元素。属性`selector`决定了如果你的组件用作自定义元素，它应该被称为什么。以下是一个示例用法：

```js
// an example container component
@Component({
  selector: `
  {{ title }}
  <example-component>
  `
})
export class ContainerComponent {
  title ="container component";
}
```

组件可以以这种方式使用，这使得将应用视为由组件组成的分层树变得容易。因此，一个待办事项应用可能看起来如下所示：

```js
AppComponent
  TodoList
    TodoItem
    TodoItem
    TodoItem
    ...
```

让我们开始创建这个应用，从`AppComponent`开始。由于这是最顶层的组件，它也被称作根组件。`AppComponent`应该在它的模板中渲染`TodoListComponent`，如下所示：

```js
// mvc/MvcExample/src/app/app.component.ts

import { Component } from "@angular/core";

@Component({
  selector: "app-root",
  template: `
  <todo-list></todo-list>
`,
  styleUrls: ["./app.component.css"]
})
export class AppComponent {
  title = "app";
}

```

下一步是定义`TodoListComponent`，并知道它应该能够在其模板中渲染多个`TodoItemComponent`实例。列表的大小通常是未知的。这正是结构指令`*ngFor`的作用。因此，在定义`TodoListComponent`时，我们将利用以下代码：

```js
// mvc/MvcExample/src/app/todo-list.component.ts

import { Component } from "@angular/core";

@Component({
selector: "todo-list",
template: `
  <h1>{{title}}</h1> <custom></custom>
  <div *ngFor="let todo of todos">
    <todo-item [todo]="todo" ></todo-item>
  </div>
` . // the view
})
export class TodoListComponent { // the controller class
  title: string;
  todos = [{
    title: "todo1"
  },{
    title: "todo1"
  }]
}
```

在这里，我们可以看到我们通过在模板中循环`todos`数组来渲染一系列`todo`项，如下所示：

```js
<div *ngFor="let todo of todos">
    <todo-item [todo]="todo" ></todo-item>
</div>
```

在前面的代码中，我们可以看到我们渲染了`todo-item`选择器，它指向一个我们尚未定义的`TodoItemComponent`。值得注意的是，我们如何传递一个`todo`对象并将其分配给`TodoItemComponent`上的一个输入属性。该组件的定义如下：

```js
// mvc/MvcExample/src/app/todo-item.component.ts

import { Component, Input } from "@angular/core";
@Component({
  selector: "todo-item",
  template: `<h1>{{todo.title}}</h1>` 
})
export class TodoItemComponent {
  @Input() todo;
}
```

思考哪些组件应该作为其他组件的一部分存在，这是你需要投入大量时间去做的事情。

# 从架构角度来看的组件

鼓励你在你的 Angular 应用中创建大量的组件。在前面的示例中，创建了一个`todo`列表应用，很容易想到创建一个只包含一个组件`AppComponent`的应用。这意味着一个组件将负责很多事情，比如显示`todo`项、保存这些项、删除它们等等。组件旨在用于解决一个问题。这就是为什么我们创建了`TodoItemComponent`，它的唯一任务是显示一个`todo`项。对于`TodoListComponent`也是如此。它应该只关心显示列表，其他什么都不管。你将应用拆分成更小、更专注的区域越多，效果越好。

# NgModule – 我们的新门面（以及一些其他部分）

到目前为止，我们讨论组件时，是按照它们专注于解决一个任务来进行的。然而，Angular 中还有其他可以使用的结构，例如管道、指令和服务。我们的大部分组件都会发现自己属于一个共同的主题，比如产品或用户管理等。当我们意识到哪些结构属于同一主题时，我们也会意识到其中一些结构是我们想在应用程序的其他地方使用的。相反，有些结构只意味着在提到的主题上下文中使用。为了保护这些结构免受意外使用，我们希望以门面方式将它们分组，并在结构与其他应用程序的其余部分之间放置一个保护层。在纯 ES2015 模块中这样做的方法是创建一个门面文件，在其中公开结构，而其他结构则不公开，如下所示：

```js
// an old facade file, index.ts

import { MyComponent } from 'my.component';
import { MyService } from 'my.service';

export MyComponent;
export MyService;
```

假设我们有一个包含以下文件的目录：

```js
/my
  MyComponent.ts
  MyService.ts
  MyOtherService.ts
  index.ts
```

创建门面文件的目的是确保只有一个地方可以从中导入所有需要的结构。在这种情况下，那就是 `index.ts` 文件。前一个目录的消费者会这样做：

```js
// consumer.ts

import * as my from './my';
let component = new my.MyComponent();
let service = new MyService();
```

`MyOtherService` 并没有在 `index.ts` 文件中公开，所以尝试像在 `consumer.ts` 中那样访问它会导致错误。理论上，您可以指定结构的完整路径，但您应该使用桶。桶通常用于轻松访问您的结构，而无需编写长达五英里的导入语句，如下所示：

```js
// index.ts
import { Service } from '../../../path-to-service';
import { AnotherService } from '../../path-to-other-service';
export Service;
export AnotherService;

// consumer.ts

// the long and tedious way
import { Service } from '../../../path-to-service';
import { AnotherService } from '../../path-to-other-service';

// the easier way using a barrel
import * as barrel from './index';
let service = new barrel.Service();
let anotherService = new barrel.AnotherService();

```

如您所见，该桶，`index.ts` 是负责知道所有结构所在位置的那个。这也意味着，如果您移动文件，更改某些结构的目录，那么更新这些结构路径的唯一文件就是桶文件。

Angular 处理这个问题的方式是使用 Angular 模块。一个 Angular 模块看起来如下所示：

```js
// mvc/MvcExample/src/app/my/my.module.ts

import { NgModule } from "@angular/core";
import { MyComponent } from "./my.component";
import { MyPipe } from "./my.pipe";

@NgModule({
  imports: [],
  exports: [MyComponent],
  declarations: [MyComponent, MyPipe],
  providers: []
})
export class MyModule {}
```

将 `MyComponent` 和 `MyPipe` 放入模块的声明属性中的效果是，这些组件可以在 `MyModule` 中自由使用。例如，您可以在 `MyComponent` 模板中使用 `MyPipe`。然而，如果您想在模块外部使用 `MyComponent`，在一个属于另一个模块的组件中，您需要将其导出。我们通过将其放置在属于 `exports` 属性的数组中来做到这一点：

```js
exports: [MyComponent]
```

Angular 将模块的概念远远超出了分组。我们 `NgModule` 中的某些指令是为了让编译器知道如何组装组件。我们给出的其他指令是为了依赖注入树。将 Angular 模块视为一个配置点，同时也是您在逻辑上将应用程序划分为代码块的地方。

在发送给 `@NgModule` 装饰器的对象上，你可以设置具有不同含义的属性。最重要的属性包括：

+   `declarations` 属性是一个数组，指定了属于我们的模块的内容

+   `imports` 属性是一个数组，指定了我们依赖的其他 Angular 模块；它可能是基本的 Angular 指令或我们想在模块内部使用的通用功能

+   `exports` 属性是一个数组，指定了应该对任何导入此模块的模块可用的内容；`MyComponent` 被公开，而 `MyPipe` 只对此模块是私有的

+   `providers` 属性是一个数组，指定了哪些服务应该注入到属于此模块的构造函数中，也就是说，注入到在声明数组中列出的构造函数中。

# 使用 ES2015 模块

到目前为止，我们已经提到模型只是普通的类。ES2015 模块只是一个文件。在这个文件中，既有公共构造函数也有私有构造函数。私有的事物只在该文件内部可见。公共的事物可以在文件外部使用。在 Angular 中，ES2015 模块不仅用于模型，还用于所有可想象的构造函数，如组件、指令、管道、服务等等。这是因为 ES2015 模块是我们将项目拆分成更小部分的一种回答，这为我们提供了以下好处：

+   许多小文件使得并行处理你的工作变得更加容易，并且可以同时让许多开发者工作

+   通过使应用程序的一些部分公开而其他部分私有来隐藏数据的能力

+   代码重用

+   更好的可维护性

为了理解这些陈述，我们必须记住过去网页开发的样子。当网络还很年轻时，我们的 JavaScript 代码通常只有一个文件。这很快变成了一个巨大的混乱。多年来，我们采用了不同的技术来找到一种方法将我们的应用程序拆分成许多小文件。许多小文件使得维护变得更加容易，同时也更容易获得对正在发生的事情的良好概述，以及其他许多好处。然而，也存在其他问题。由于所有这些小文件在与应用程序一起发布之前都必须重新拼接在一起，这个过程称为打包，我们突然有一个巨大的文件，其中函数和变量可能会因为命名冲突而意外地相互影响。解决这个问题的方法之一是处理称为信息隐藏的东西。这是为了确保我们创建的变量和函数只能对某些其他构造函数可见。当然，解决这个问题有多种方法。ES2015 提供了一种默认为私有的方式。在 ES2015 中声明的所有内容默认都是私有的，除非你明确导出它，从而使它对导入上述模块的其他模块公开可访问。

那么，这与前面的陈述有什么联系呢？任何模块系统实际上都允许我们在项目随着我们成长而增长时保持可见性。另一种选择是只有一个文件，那将是一团糟。对于同时工作的多个开发者来说，任何逻辑上划分应用的方法都会使开发者之间的工作流划分更容易。

# 消费模块

在 ES2015 中，我们使用`import`和`from`关键字导入一个或多个结构，如下所示：

```js
import { SomeConstruct } from './module';
```

导入的文件看起来是这样的：

```js
export let SomeConstruct = 5;
```

涉及的基本操作，使用 ES2015 模块，可以总结如下：

+   定义一个模块并编写模块的业务逻辑

+   导出你想要公开的结构

+   使用`import`关键字从消费者文件中导入该模块

当然，这不仅仅是那样，所以让我们在下一小节中看看你还能做什么。

# 一个 Angular 示例

我们已经在本章中广泛使用了 ES2015 导入，但让我们强调一下那是何时。如前所述，所有结构都使用了 ES2015 模块、模型、服务、组件和模块。对于模块，它看起来是这样的：

```js
import { NgModule } from '@angular/core';

@NgModule({
  declarations: [],
  imports: [],
  exports: [],
  providers: []
})
export class FeatureModule {}
```

在这里，我们看到我们导入了我们需要的功能，并最终导出这个类，使其可供其他结构消费。模块也是如此：

```js
import { Component } from '@angular/core';

@Component({
  selector: 'example'
})
export class ExampleComponent {}
```

管道、指令和过滤器都遵循相同的模式，导入它们需要的部分，并将自己导出以作为`NgModule`的一部分：

# 多重导出

到目前为止，我们只展示了如何导出一个结构。通过在所有希望导出的结构旁边添加`export`关键字，从一个模块中导出多个东西是可能的，如下所示：

```js
export class Math {
  add() {}
  subtract() {}
}

export const PI = 3.14 
```

实际上，对于你想要公开的每一件事，你都需要在它前面添加一个`export`关键字。还有一个替代语法，我们可以在大括号内定义应该导出哪些结构，而不是在每个结构前添加`export`关键字。它看起来像这样：

```js
class Math {
  add() {}
  subtract() {}
}

const PI = 3.14 

export {
  Math, PI
}
```

无论你是将`export`放在每个结构前面，还是将它们全部放在`export {}`中，最终结果都是相同的，只是使用哪种方式取决于个人喜好。要从这个模块中消费结构，我们就会输入：

```js
import { Math, PI } from './module';
```

在这里，我们有选择指定我们想要`import`的内容。在先前的例子中，我们选择了导出`Math`和`PI`，但我们可以只满足于导出`Math`，例如；这取决于我们。

# 默认导入/导出

到目前为止，我们已经非常明确地说明了我们导入和导出了什么。然而，我们可以创建一个所谓的默认导出，它的消费方式略有不同：

```js
export default class Player {
  attack() {}
  move() {}
}

export const PI = 3.13;
```

要消费这个，我们可以编写以下代码：

```js
import Player from './module';
import { PI } from './module'
```

特别注意第一行，我们不再使用花括号`{}`来导入特定的构造函数。我们只需使用我们自己设定的名称。在第二行，我们必须正确地命名为`PI`，但在第一行我们可以选择名称。玩家指向的是我们默认导出的内容，即`Player`类。正如你所见，如果我们想的话，我们仍然可以使用正常的花括号`{}`来导入特定的构造函数。

# 重命名导入

有时我们可能会遇到冲突，构造函数被赋予相同的名称。这种情况可能发生：

```js
import { productService } from './module1/service'
import { productService } from './module2/service'; // name collision
```

这是我们需要解决的问题。我们可以使用`as`关键字来解决这个问题，如下所示：

```js
import { productService as m1_productService }
import { productService as m2_productService }
```

多亏了`as`关键字，编译器现在可以没有问题地区分不同的事物。

# 该服务

我们在主部分开始时讨论了 ES2015 模块在 Angular 中适用于所有构造函数。本节是关于服务的，当使用 ES2015 模块时，服务并没有什么不同。我们使用的服务应该在单独的文件中声明。如果我们打算使用服务，我们需要导入它。不过，导入的原因取决于服务的类型。服务可以分为两种类型：

+   无依赖的服务

+   带有依赖的服务

# 无依赖的服务

无依赖的服务是一个构造函数为空的服务：

```js
export Service {
  constructor(){}
  getData() {}
}
```

要使用它，你只需输入：

```js
import { Service } from './service'
let service = new Service();
service.getData();
```

任何消费此服务的模块都将获得自己的代码副本，这种代码。然而，如果你想让消费者共享一个公共实例，你只需稍微修改`service`模块的定义，如下所示：

```js
class Service {
  constructor() {}
  getData() {}
}
const service = new Service();
export default service;
```

在这里，我们导出服务的一个实例而不是服务声明。

# 带有依赖的服务

带有依赖的服务在其构造函数中有依赖项，我们需要帮助解决这些依赖项。如果没有这个解决过程，我们无法创建服务。这样的服务可能看起来像这样：

```js
export class Service {
  constructor(
    Logger logger: Logger, 
    repository:Repository
  ) {}
}
```

在此代码中，我们的服务有两个依赖项。在构建服务时，我们需要一个`Logger`实例和一个`Repository`实例。我们完全可以通过输入类似以下内容来找到`Logger`实例和`Repository`实例：

```js
import { Service } from './service'
import logger from './logger';
import { Repository } from './repository';

// create the service
let service = new Service( logger, new Repository() )
```

这绝对是可以做到的。然而，每次我想获取服务实例时，编写代码都有些繁琐。当你开始有 100 多个具有深层对象依赖的类时，DI 系统很快就会显示出其价值。

这就是依赖注入库帮助你解决的一个问题，即使它不是其存在背后的主要动机。DI 系统的主要动机是在系统的不同部分之间创建松散耦合，并依赖于契约而不是具体实现。以我们的服务为例。DI 可以帮助我们做两件事：

+   用另一个具体实现替换一个

+   轻松测试我们的构造函数

为了说明我的意思，让我们首先假设`Logger`和`Repository`是接口。不同的具体类可能会以不同的方式实现接口，如下所示：

```js
import { Service } from './service'
import logger from './logger';
import { Repository } from './repository';

class FileLogger implements Logger {
  log(message: string) {
    // write to a file
  }
}

class ConsoleLogger implements Logger {
  log(message: string) {
    console.log('message', message);
  }
}

// create the service
let service = new Service( new FileLogger(), new Repository() )
```

这段代码展示了如何通过选择`FileLogger`而不是`ConsoleLogger`或反之，轻松切换`Logger`的实现。如果只依赖外部提供的依赖项，测试用例也会变得简单得多，因此可以轻松地进行模拟。

# 依赖注入

实质上，当我们请求一个结构实例时，我们希望得到帮助来构建它。当请求解析一个实例时，DI 系统可以以两种方式之一行事：

+   **瞬态模式**：依赖关系始终被重新创建

+   **单例模式**：依赖关系被重用

Angular 只创建单例，这意味着每次我们请求一个依赖项时，它只会被创建一次，如果我们不是第一个请求该依赖项的结构，我们将得到一个已经存在的依赖项。

任何 DI 框架的默认行为都是使用类的默认构造函数，并从类中创建一个实例。如果该类有依赖项，那么它必须首先解决这些依赖项。想象一下以下情况：

```js
export class Logger { }

export class Service {
  constructor(logger: Logger) { }
}

```

DI 框架会遍历依赖链，找到没有任何依赖的结构，并首先实例化它。然后它会向上遍历，最终解决你请求的结构。所以用这段代码：

```js
import { Service } from './service';

export class ExampleComponent {
  constructor(srv: Service) { }
}

```

DI 框架会：

+   首先实例化日志记录器

+   第二次实例化服务

+   第三次实例化组件

# 使用提供者进行 Angular 依赖注入

到目前为止，我们只讨论了依赖注入的一般概念，但 Angular 有一些结构或装饰器来确保依赖注入能够完成其工作。首先想象一个简单的场景，一个没有依赖的服务：

```js
export class SimpleService {}
```

如果存在一个需要服务实例的组件，如下所示：

```js
@Component({
  selector: 'component'
})
export class ExampleComponent {
  constructor(srv: Service) {}
}
```

Angular 依赖注入系统介入并尝试解析它。因为服务没有依赖项，解决方案就是简单地实例化`Service`，Angular 为我们做这件事。然而，我们需要告诉 Angular 这个结构，以便 DI 机制能够工作。需要知道这个结构的东西被称为提供者。Angular 模块和组件都可以访问一个提供者数组，我们可以将`Service`结构添加到其中。不过，关于这一点，自从 Angular 模块出现以来，建议不要为组件使用提供者数组。下面的段落只是为了告诉你组件的提供者是如何工作的。

这将确保当请求`Service`实例时，它会在正确的位置创建和注入。让我们告诉 Angular 模块一个服务结构：

```js
import { Service } from "./Service";

@NgModule({
  providers: [Service]
})
export class FeatureModule{}
```

这通常就足够让它工作了。然而，你可以将`Service`结构注册到`component`类中，它看起来是一样的：

```js
@Component({
  providers: [Service]
})
export ExampleComponent {}
```

这会有不同的效果。你会告诉 DI 机制这个构造函数，并且它将能够解析它。然而，有一个限制。它只能为这个组件及其所有视图子组件解析它。有些人可能会认为这是一种限制组件可以看到哪些服务的方法，因此将其视为一个特性。让我通过展示 DI 机制何时能够确定我们提供的服务来解释这一点：

**每个人的父母——它起作用了**：在这里，我们可以看到，只要最高层的组件将`Service`声明为提供者，所有后续的组件都能够注入`Service`：

```js
AppComponent // Service added here, Can resolve Service
  TodosComponent // Can resolve Service
    TodoComponent // Can resolve Service
```

让我们通过一些代码来举例说明：

```js
// example code on how DI for works for Component providers, there is no file for it
// app.component.ts
@Component({
  providers: [Service] // < - provided,
  template : `<todos></todos>`
})
export class AppComponent {}

// todos.component.ts
@Component({
  template : `<todo></todo>`,
  selector: 'todos'
})
export class TodosComponent {
  // this works
  constructor(private service: Service) {}
}

// todo.component.ts
@Component({
  selector: 'todo',
  template: `todo component ` 
})
export class TodoComponent {
  // this works
  constructor(private service: Service) {}
}
```

**TodosComponent——对其子组件有效但对其上级无效**：在这里，我们向下提供`Service`到`TodosComponent`。这使得`Service`对`TodosComponent`的子组件可用，但它的父组件`AppComponent`则无法访问：

```js
AppComponent // Does not know about Service
  TodosComponent // Service added here, Can resolve Service
    TodoComponent // Can resolve Service
```

让我们尝试用代码来展示这一点：

```js
// this is example code on how it works, there is no file for it
// app.component.ts
@Component({
  selector: 'app',
  template: `<todos></todos>`
})
export class AppComponent {
  // does NOT work, only TodosComponent and below knows about Service
  constructor(private service: Service) {}
}

// todos.component.ts
@Component({
  selector: 'todos',
  template: `<todo></todo>`
  providers: [Service]
})
export class TodosComponent {
  // this works
  constructor(private service: Service) {}
}

// todo.component.ts
@Component({
  selector: 'todo',
  template: `a todo`
})
export class TodoComponent {
  // this works
  constructor(private service: Service) {}
}
```

我们在这里可以看到，将我们的`Service`添加到组件的`providers`数组中存在限制。将其添加到 Angular 模块中是确保它能够被该数组内所有构造函数解析的可靠方法。但这并非全部。将我们的`Service`添加到 Angular 模块的`providers`数组中确保它在整个应用程序中都是可访问的。你可能会问，这是如何实现的？这与模块系统本身有关。想象一下，在我们的应用程序中我们有以下 Angular 模块：

```js
AppModule
SharedModule
```

为了能够使用我们的`SharedModule`，我们需要通过将其添加到`AppModule`的`imports`数组中来将其导入到`AppModule`中，如下所示：

```js
//app.module.ts

@NgModule({
  imports: [ SharedModule ],
  providers: [ AppService ]
})
export class AppModule{}
```

我们知道这会从`SharedModule`的`exports`数组中拉取所有构造函数，但这也会将`SharedModule`的提供者数组连接到`AppModule`的提供者数组上。想象一下`SharedModule`看起来可能像这样：

```js
//shared.module.ts

@NgModule({
  providers : [ SharedService ]
})
export class SharedModule {} 
```

在导入完成后，合并后的提供者数组现在包含：

+   `AppService`

+   `SharedService`

因此，这里的经验法则是，如果你想将服务暴露给应用程序，那么请将其放入 Angular 模块的`providers`数组中。如果你想限制对服务的访问，那么请将其放入组件的`providers`数组中。然后，你将确保它只能被该组件及其视图子组件访问。

接下来，让我们谈谈当你想要覆盖注入时的情况。

# 覆盖现有构造函数

有时候，你想要覆盖构造函数的默认解析。你可以在模块级别做这件事，也可以在组件级别做。你所做的就是简单地表达你正在覆盖哪个构造函数以及用哪个其他构造函数来覆盖。它看起来像这样：

```js
@Component({
  providers: [
    { provide: Service, useClass : FakeService }
  ]
})
```

`provide`是我们已知的构造函数，而`useClass`是它应该指向的对象。让我们假设我们这样实现了我们的`Service`：

```js
export class Service {
  no: number = 0;
  constructor() {}
}
```

我们还向一个组件添加了以下覆盖：

```js
@Component({
  providers: [{ provide : Service, useClass: FakeService }]
})
```

`FakeService`类具有以下实现：

```js
export class FakeService {
  set no(value) {
    // do nothing
  }

  get no() {
    return 99;
  }
}
```

现在组件及其所有视图子组件在请求服务构造函数时都将始终得到`FakeService`。

# 运行时覆盖

有一种方法可以在运行时决定为构造函数注入什么。到目前为止，我们一直非常明确地说明了何时覆盖，但我们可以通过添加一些逻辑并使用`useFactory`关键字来实现这一点。它的工作方式如下：

```js
let factory = () => {
  if(condition) {
    return new FakeService();
  } else {
    return new Service();
  }
}

@Component({
 providers : [
   { provide : Service, useFactory : factory }
 ]
})
```

这个工厂本身可能存在依赖；我们使用`deps`关键字来指定这些依赖，如下所示：

```js
let factory = (auth:AuthService, logger: Logger) => {
  if(condition) {
    return new FakeService();
  } else {
    return new Service();
  }  
}

@Component({
  providers : [
   { provide : Service, useFactory : factory, 
 deps: [AuthService, Logger] }
  ]
})
```

在这里，我们突出了`condition`变量，它是一个布尔值。我们可能有无数的理由想要能够切换实现。一个很好的例子是当端点还不存在时，我们想要确保它调用我们的`FakeService`。另一个原因可能是我们处于测试模式，只需更改这个变量，我们就可以让所有服务都依赖于它们自己的假版本。

# 覆盖常量

并非所有需要解析的东西都是类；有时它是一个常量。对于这些情况，我们不是使用`useClass`，而是可以使用`useValue`，如下所示：

```js
providers: [ { provide: 'a-string-token', useValue: 12345678 } ]
```

这实际上不是一个类类型，所以您不能在构造函数中这样写：

```js
constructor(a-string-token) . // will not compile
```

这样是无法编译的。我们可以做的是使用以下方式使用`@Inject`装饰器：

```js
constructor( @Inject('a-string-token') token) // token will have value 12345678
```

`useValue`在如何覆盖方面与`useClass`没有区别。当然，区别在于我们需要在我们的指令中键入`useValue`而不是`useClass`。

# 使用@Injectable 解析依赖

在上一节中，我们对依赖注入（DI）进行了一些深入探讨，但几乎忘记了一个非常重要的装饰器`@Injectable`。`@Injectable`对于一般的服务来说并不是强制使用的。然而，如果该服务有依赖项，那么它就需要使用。未能用`@Injectable`装饰具有依赖项的服务会导致编译错误，编译器会抱怨它不知道如何构造提到的服务。让我们看看我们需要使用`@Injectable`装饰器的一个案例：

```js
import { Injectable } from '@angular/core';

@Injectable() 
export class Service {
  constructor(logger:Logger) {}
}
```

在这种情况下，Angular 的 DI 机制将查找`Logger`并将其注入到`Service`构造函数中。所以，只要我们做了以下操作：

```js
providers: [Service, Logger]
```

在组件或模块中，它应该可以工作。记住，当不确定时，如果服务在构造函数中有依赖项或将在不久的将来有依赖项，请将`@Injectable`添加到您的服务中。如果您的服务缺少`@Injectable`关键字，并且您尝试将其注入到组件的构造函数中，那么它将抛出错误，并且您的组件将不会被创建。

本节旨在从一般角度解释 DI（依赖注入）的工作原理，以及在 Angular 中的具体应用。对于后者，它涵盖了如何将构造函数注册到 Angular 的 DI 机制中，以及如何覆盖它。很明显，DI 机制相当复杂。它可以通过向 Angular 模块的提供者数组添加构造函数来应用于应用级别，也可以应用于组件级别及其视图子组件。描述 DI 机制的主要原因是为了向您展示其可能性，这样您在定义应用程序架构时就能知道如何最好地利用它。

# 使用 HTTP 获取和持久化数据——介绍使用 Observables 的服务

到目前为止，我们已经经历了一个数据流，其中组件是我们的对外视图，同时也是控制器。组件使用服务来获取数据，也用来持久化数据。然而，直到这一点，数据一直生活在服务中，这并不是一个很合适的数据存放位置。几乎可以肯定，这些数据应该被获取并持久化到端点上。这个端点是一个暴露在互联网上某个地方的后端系统的 URL。我们可以使用 HTTP 来访问这个端点。Angular 在原始通过 HTTP 获取数据的方式之上创建了一个包装器。这个包装器是一个类，它封装了一个名为`XmlHttpRequest`的对象的功能。Angular 的包装器类被称为`HttpClient`服务。

# 使用 HTTP 服务获取数据

通过 HTTP 进行通信的方式不止一种。一种方式是使用`XmlHttpRequest`对象，但这是一种相当繁琐且低级的方法。另一种方式是使用新的 fetch API，您可以在以下链接中了解更多信息：[`developer.mozilla.org/en-US/docs/Web/API/Fetch_API`](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)。

Angular 有自己的抽象层，即 HTTP 服务，它可以在`HTTPModule`中找到。要使用它，只需导入`HttpModule`：

```js
import { HttpClientModule } from '@angular/common/http';

@NgModule({
  imports: [HttpClientModule]
})
```

然后，将`HttpClient`服务注入到您想要使用它的地方，如下所示：

```js
import { HttpClient } from '@angular/common/http';

@Component({
  selector: 'consumer',
  template: ``
})
export class ConsumerComponent {
  constructor(private http:HttpClient) {}
}
```

到目前为止，我们已经准备好使用它了。让我们快速了解一下这个 HTTP 服务有哪些方法：

+   `get('url', <可选的 options 参数>)`为我们获取数据

+   `post('url', payload,<可选的 options 参数>)`创建资源

+   `put('url', payload,<可选的 options 参数>)`更新资源

+   `delete('url',<可选的 options 参数>)`删除资源

+   `request`是一个原始请求，您可以根据需要配置要进行的调用、要添加的标头等。

当我们使用`http.get()`时，我们得到一个称为 Observable 的结构。Observable 就像`Promise`一样，是一个异步概念，它使我们能够将回调附加到未来某个时间数据到达时，以及将回调附加到发生错误时。RxJS 对 Observable 的实现包含了许多操作符，帮助我们转换数据并与其他 Observable 交互。其中一个操作符叫做`toPromise()`，它使我们能够将 Observable 转换为 Promise。有了这个，我们可以以两种不同的方式，或者说两种风味进行 HTTP 调用。第一种方式是我们使用`toPromise()`操作符将我们的`Observable`转换为`Promise`，另一种方式是使用我们的 Observable 并以此方式处理数据。

一个典型的调用有两种不同的风味：

+   **使用 Promise**

```js
// converting an Observable to a Promise using toPromise()
http
  .get('url')
  .toPromise()
  .then(x => x.data)
  .then(data => console.log('our data'))
  .catch(error => console.error('some error happened', error));
```

这个版本感觉熟悉。如果你需要复习一下 Promise，在继续之前，请查看以下链接：[`developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)。我们认识到`.then()`方法是在数据到达时被调用的方法，而`.catch()`方法是在我们的请求出现问题时被调用的方法。这就是我们在处理 Promise 时的期望。

+   **使用 RxJS**

```js
// calling http.get() and gets an Observable back
http
  .get('url')
  .map( x => x.data ) 
  .subscribe( data => console.log('our data', data))
  .catch( error => console.error('some error happened', error))
```

第二个版本看起来不同。在这里，我们使用`.map()`方法的方式与使用`.then()`方法的方式非常相似。这个声明需要一些解释。让我们再次查看 Promise 风格的代码，并突出我们所说的话：

```js
http
  .get('url')
  .toPromise()
  .then(x => x.data)
  .then(data => console.log('our data'))
  .catch(error => console.error('some error happened', error));
```

突出的部分是在数据首次从服务到达时被调用的方法。我们在这次调用中做的事情是创建数据的投影，如下所示：

```js
.then(x => x.data)
```

后续的`then()`调用仅处理将数据打印到控制台：

```js
.then(data => console.log('our data'))
```

现在我们来看看 RxJS 版本的不同之处，突出显示投影部分和打印结果的部分：

```js
http
  .get('url')
  .map( x => x.data ) 
  .subscribe( data => console.log('our data', data) )
  .catch( error => console.error('some error happened', error) )
```

我们突出显示的代码的第一行表明了我们的投影：

```js
.map( x => x.data )
```

订阅的调用是我们打印数据的地方，如下所示：

```js
.subscribe( data => console.log('our data', data) )
```

当我们使用`http.get()`时，我们得到一个称为 Observable 的结构。Observable 就像 Promise 一样，是一个异步概念，它使我们能够将回调附加到未来某个时间数据到达时，以及将回调附加到发生错误时。

Observable 是 RxJS 库的一部分，这是在`HttpClient`服务中提供动力的。这是一个强大的库，不仅用于简单的请求/响应模式。我们将在未来的章节中进一步探索 RxJS 库，并发现 Observable 实际上是一个多么强大的范式，它带来了哪些其他重要概念，以及它不仅仅关于 HTTP 工作的事实，而是所有异步概念。

# 摘要

我们以尝试解释在应用架构方面打下良好基础的重要性开始本章，因此我们研究了 MVC 模式。然后我们继续描述 MVC 模式在 Angular 中的应用，尽管它被称为 MVW，模型视图随意。我们这样做是为了理解 Angular 框架由许多帮助我们以易于扩展、维护和并行化工作的方式组织应用程序的结构组成。

尽管 Angular 带来了许多新事物，例如 ES2015 模块，它试图以可管理的方式解决如何分割代码的问题。之后，我们争论尽管 ES2015 模块很棒，但在创建复杂对象时，它们附带了很多仪式。为了帮助我们摆脱这种仪式，我们描述了 Angular 依赖注入如何成为解决该问题的方案。实际上，您将使用 ES2015 导入您的结构。Angular DI 帮助我们的是创建我们结构所需的依赖项。

最后，我们通过简单地说数据实际上并不永久地存在于模型、控制器或视图中，而是可以通过与一个可通过 HTTP 访问的端点进行交互来检索和持久化，从而结束了 MVC 模式的解释。我们通过描述 Angular 4.x HTTP 服务如何帮助我们做到这一点来结束本章。

所有这些从教育角度来看都很有趣。它没有描述房间里的大象，当事情变得复杂时我们如何管理我们的数据？我们必须处理的担忧是：

+   双向数据流

+   缺乏可预测性（一个变化可能导致级联变化）

+   状态分散（没有单一的真实来源，并且我们的组件可以位于部分更新的状态上）

在我们继续进入第二章，*1.21 吉瓦特 – 流量模式解释*时，让我们牢记这些担忧。
