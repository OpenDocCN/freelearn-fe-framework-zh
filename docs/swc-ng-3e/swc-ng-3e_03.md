# 第三章：Angular 应用程序的构建模块

在上一章中，我们探讨了 Angular 设计决策背后的驱动因素。我们描述了导致开发全新框架的主要理由；Angular 利用最新的网络标准，同时吸取过去的教训。尽管我们熟悉主要驱动因素，但我们还没有描述新框架的核心概念。Angular 与 AngularJS 走了不同的道路，并在用于开发单页应用程序的基本构建模块中引入了许多变化。

本章的使命是描述框架的核心，并对其主要概念进行简要介绍。在接下来的几页中，我们还将概述如何将这些概念组合起来，以帮助我们为网络应用程序构建专业的用户界面。随后的章节将概述我们将在本书的后续部分更详细学习的所有内容。

在本章中，我们将探讨以下主题：

+   框架的概念概述，展示了不同概念之间的相互关系。

+   将用户界面构建为组件的组合。

+   Angular 中指令所走的路径，以及与 AngularJS 相比其接口的变化。

+   导致指令分解为两个不同概念并强制分离关注点的理由。为了更好地理解它们，我们将演示它们定义的基本语法。

+   改进后的变更检测概述，以及它如何涉及指令提供的上下文。

+   区域（zones）是什么，以及它们如何使我们的日常开发过程变得更简单。

+   管道（pipes）是什么，以及它们与 AngularJS 过滤器的关联。

+   介绍 Angular 中全新的 **依赖注入**（**DI**）机制及其与服务的关联。

# Angular 的概念概述

在我们深入 Angular 的不同部分之前，让我们先了解一切是如何相互关联的概念概述。让我们看一下以下图表：

![](img/b74832ba-467d-4396-a393-72fdbc2722e1.png)

图 1

*图 1* 至 *图 4* 展示了 Angular 的主要概念及其之间的联系。这些图表的主要目的是说明使用 Angular 构建单页应用程序的核心模块及其关系。

`组件`是我们将使用的主要构建模块，以 Angular 创建应用程序的用户界面。组件模块是指令的直接后继者，它是将行为附加到 DOM 的原始形式。组件通过提供进一步的功能扩展了指令，例如模板，可以用来渲染指令的组合。在模板内部，可以存在不同的表达式：

![](img/b329a433-4d2b-44b5-9443-2b838f300cd4.png)

图 2

上述图表从概念上说明了 Angular 的 **变更检测** 机制。它执行脏检查，评估特定 UI 组件上下文中的注册表达式。由于作用域的概念已被从 Angular 中移除，表达式的执行上下文是与之关联的组件控制器的实例。

可以使用 **Differs** 来增强 **变更检测** 机制；这就是为什么这两个元素在图表中存在直接关系。

**管道**是 Angular 的另一个组件。我们可以将管道视为 AngularJS 中的过滤器。管道可以与组件一起使用。我们可以在任何组件上下文中定义的表达式中包含它们。

现在，让我们看一下以下图表：

![](img/5983c214-5478-452c-a12c-81b7e9812fc7.png)

图 3

指令和组件将业务逻辑委托给服务。这强制执行更好的关注点分离、可维护性和代码重用性。指令通过框架的 **DI** 机制接收声明为依赖项的特定服务实例的引用，并将与业务相关的逻辑执行委托给它们。指令和组件都可以使用 **DI** 机制注入服务，也可以注入 DOM 元素和/或其他组件或指令。请记住，组件扩展指令，因此组件的模板是由组件和指令的组合形成的。

**模块**（也称为 **NgModules**）是将构建块组合成逻辑上相关组的核心概念。NgModules 与 AngularJS 模块非常相似，但提供了更多的语义。请注意，NgModules 与我们在 第四章，“TypeScript 快速入门”中描述的 ES2015 模块不同。Angular 模块是框架功能，而 ES2015 模块是语言结构。

NgModules 承担以下职责：

+   提供 Angular 模板编译器的上下文

+   提供一种封装级别，其中我们可以拥有仅用于给定模块边界内的组件或指令

+   在 NgModules 中，我们可以配置框架的 DI 机制的提供者：

![](img/a73e7650-28b8-43f2-bf67-a80e9be36a78.png)

图 4

最后，我们使用新的路由器来定义应用中的路由。由于指令不拥有模板，因此只有组件可以被路由器渲染，代表应用中的不同视图。路由器还使用一组预定义的指令，这允许我们在不同的视图和它们应该渲染的容器之间定义超链接。

现在，我们将更仔细地研究这些概念，看看它们是如何一起工作以创建 Angular 应用程序，以及它们是如何从它们的 AngularJS 前身中变化的。

# 更改指令

AngularJS 在开发单页应用程序时引入了指令的概念。指令的目的是封装与 DOM 相关的逻辑，并允许我们通过组合它们来构建用户界面。这样，我们能够扩展 HTML 的语法和语义。最初，像大多数创新概念一样，指令受到了争议性的看法，因为它们使我们倾向于在不使用`data-`前缀的情况下编写无效的 HTML，当使用自定义元素或属性时。然而，随着时间的推移，这个概念逐渐被接受，并证明它将长期存在。

AngularJS 中指令实现的另一个缺点是我们可以使用它们的不同方式。这需要理解属性值，这些值可以是字面量、表达式、回调或微语法。这使得工具基本上变得不可能。

Angular 保留了指令的概念，但从中吸取了 AngularJS 的精华，并添加了一些新的想法和语法。Angular 指令的主要目的是通过扩展 ES2015 类中定义的自定义逻辑来将行为附加到 DOM 上。我们可以将这些类视为与指令关联的控制器，并将它们的构造函数视为与 AngularJS 指令的链接函数类似。然而，新的指令具有有限的配置性。它们不允许将模板与它们关联，这使得大多数已知用于定义指令的属性变得不必要。API 的简单性并不限制指令的行为，但只强制执行更严格的关注点分离。为了补充这个更简单的 API，Angular 引入了一个更丰富的界面来定义 UI 元素，称为组件。组件通过允许它们拥有模板（通过**组件元数据**）来扩展指令的功能。我们将在本书的后面进一步探讨组件。

用于 Angular 指令的语法涉及 ES2016 装饰器；请记住，**TypeScript 是 ES2016 的超集**，因此它也有装饰器。以下是一个定义简单指令的代码片段：

```js
@Directive({ selector: '[tooltip]' })
export class Tooltip { 
  @Input() tooltip: string; 
  private overlay: Overlay;

  constructor(private el: ElementRef, manager: OverlayManager) { 
    this.overlay = manager.get(); 
  }

  @HostListener('mouseenter') onMouseEnter() { 
    this.overlay.open(this.el.nativeElement, this.tooltip); 
  }

  @HostListener('mouseleave') onMouseLeave() { 
    this.overlay.close(); 
  } 
} 
```

指令可以在我们的模板中使用以下标记：

```js
<div tooltip="42">Tell me the answer!</div> 
```

一旦用户将鼠标悬停在`告诉我答案！`标签上，Angular 将调用指令定义中`@HostListener`装饰器下定义的方法。最终，将执行覆盖管理器的`open`方法。

由于我们可以在单个元素上使用多个指令，最佳实践建议我们应该使用属性作为选择器。

我们可以总结说，Angular 通过保持将行为附加到 DOM 的想法来保留了指令的概念。与 AngularJS 的核心区别在于新的语法，以及通过引入组件带来的关注点分离的进一步深化。在第五章，*开始使用 Angular 组件和指令*中，我们将进一步探讨指令 API。现在，让我们看看 Angular 组件的巨大变化。

# 了解 Angular 组件

**模型-视图-控制器**（**MVC**）是一个最初用于实现用户界面的微架构模式。作为 Angular 开发者，我们每天都在使用这个模式的不同变体，最常见的是 **模型-视图-视图模型**（**MVVM**）。在 MVC 中，我们有模型，它封装了我们的应用程序的业务逻辑，以及视图，它负责渲染用户界面，接受用户输入，并将用户交互逻辑委托给控制器。视图被表示为组件的组合，这正式称为**组合设计模式**。

让我们看一下下面的结构图，它展示了组合设计模式：

![](img/02a6bb04-ce29-4f38-aea4-963dd3509dbb.png)

图 5

这里，我们有三个类：

+   一个名为 `Component` 的抽象类。

+   两个具体的类，称为 `Leaf` 和 `Composite`。`Leaf` 类是我们即将构建的组件树中的简单终端组件。

`Component` 类定义了一个名为 `operation` 的抽象操作。`Leaf` 和 `Composite` 都继承自 `Component` 类，然而，`Composite` 类还拥有对其的引用。我们可以更进一步，允许 `Composite` 拥有一个指向 `Component` 实例列表的引用，如图所示。`Composite` 内部的组件列表可以持有指向不同 `Composite` 或 `Leaf` 实例，或扩展 `Component` 类及其后继类的其他类的实例的引用。我们可以在 `Composite` 的 `operation` 方法实现中调用单个 `Component` 实例的 `operation` 方法，从而实现不同的行为。这是由于面向对象编程语言中实现多态所使用的后期绑定机制。

# 组件在行动

理论就到这里！让我们基于前面图中展示的类层次结构构建一个组件树。这样，我们将展示如何利用组合模式，通过简化的语法构建用户界面。我们将在第五章，*开始使用 Angular 组件和指令*的上下文中查看一个类似的例子：

```js
Composite c1 = new Composite(); 
Composite c2 = new Composite(); 
Composite c3 = new Composite(); 

c1.components.push(c2); 
c1.components.push(c3); 

Leaf l1 = new Leaf(); 
Leaf l2 = new Leaf(); 
Leaf l3 = new Leaf(); 

c2.components.push(l1); 
c2.components.push(l2);  
c3.components.push(l3); 
```

前面的伪代码创建了三个`Composite`类的实例和三个`Leaf`类的实例。`c1`实例在`components`列表中持有对`c2`和`c3`的引用。`c2`实例持有对`l1`和`l2`的引用，而`c3`持有对`l3`的引用：

![图片](img/d2630023-6b07-402b-abbd-6b2d2b787a3d.png)

图 6

前面的图是我们在代码片段中构建的组件树的图形表示。这是现代 JavaScript 框架中视图的简化版本。然而，它说明了我们可以如何组合指令和组件的基本原理。例如，在 Angular 的上下文中，我们可以将指令视为`Leaf`类的实例（因为它们不拥有视图，因此不能组合其他指令和组件），而将组件视为`Composite`类的实例。

如果我们更抽象地考虑 AngularJS 的用户界面，我们可以注意到我们使用了相当类似的方法。我们视图的模板将不同的指令组合在一起，以便向我们的应用程序最终用户提供一个完全功能化的用户界面。

# Angular 中的组件

Angular 通过引入称为组件的新构建块采取了这种方法。组件扩展了我们之前章节中描述的指令概念，并提供了更广泛的功能。以下是基本`Hello world`组件的定义：

```js
@Component({ 
  selector: 'hello-world', 
  template: '<h1>Hello, {{target}}!</h1>' 
}) 
class HelloWorld { 
  target: string; 
  constructor() { 
    this.target = 'world'; 
  } 
} 
```

我们可以通过在我们的视图中插入以下标记来使用它：

```js
<hello-world></hello-world> 
```

根据最佳实践，我们应该为组件使用类型为元素的选择器，因为我们可能每个 DOM 元素只有一个组件。

我们将在本书的后续部分更详细地查看前面的语法。现在让我们简要描述该组件提供的功能。一旦 Angular 应用程序启动，它将查看 DOM 树中的所有元素并处理它们。当它找到一个名为`hello-world`的元素时，它将调用与其定义相关的逻辑，这意味着组件的模板将被渲染，花括号之间的表达式将被评估。这将导致生成`<h1>Hello, world!</h1>`标记。

因此，总结一下，Angular 核心团队将 AngularJS 中的指令分离成两个不同的部分：组件和指令。指令提供了一种简单的方法来将行为附加到 DOM 元素上，而无需定义视图。Angular 组件提供了一个强大且易于学习的 API，这使得定义我们应用程序的用户界面更加容易。Angular 组件允许我们像 AngularJS 指令一样做同样惊人的事情，但需要更少的输入和更少的学习内容。组件通过添加视图来扩展 Angular 指令的概念。我们可以将 Angular 组件和指令之间的关系视为与我们在*图 5*中看到的`Composite`和`Leaf`之间的关系相同。

从概念上讲，我们可以将指令和组件之间的关系表示为继承。第五章，*开始使用 Angular 组件和指令*，更详细地描述了这两个概念。

# Angular 模块

在 AngularJS 中，我们有模块的概念。那里的模块负责将相关功能分组并在引导过程中内部注册。不幸的是，它们没有提供封装和懒加载等特性。

Angular 在框架的第五个候选版本中引入了 NgModules。新模块的主要目的是为 Angular 编译器提供一个上下文，并实现良好的封装级别。例如，如果我们使用 NgModules 构建一个库，我们可以拥有多个声明，这些声明在内部使用但不会作为公共接口的一部分导出。让我们看看以下示例：

```js
import {NgModule} from '@angular/core';
import {CommonModule} from '@angular/common';
import {TabComponent} from './tab.component';
import {TabItemComponent} from './tab-item.component';

@NgModule({
  imports: [CommonModule],
  declarations: [TabComponent, TabItemComponent],
  exports: [TabComponent]
})
class TabModule { }
```

如果你对前面示例中的 TypeScript 语法不熟悉，请不要担心；我们将在下一章深入探讨这门语言。

在前面的代码片段中，使用`@NgModule`装饰器，我们声明了`TabModule`。请注意，在`declarations`列表中，我们包括了`TabComponent`和`TabItemComponent`，但在`exports`列表中，我们只有`TabComponent`。这样，我们可以为我们的库实现一定程度的封装。模块的使用者只能使用`TabComponent`，因此我们不必担心`TabItemComponent` API 的向后兼容性，因为它只能在模块内部访问，即我们模块的边界内。最后，通过设置传递给`@NgModule`的对象字面量的`imports`属性，我们可以列出我们想要在当前模块内部使用的模块。这样，我们将能够利用它们声明的所有`exports`和提供者（我们将在第六章，*Angular 中的依赖注入*)。

# 引导 Angular 应用程序

与 AngularJS 类似，在我们应用程序渲染之前，它将经历引导过程。在 Angular 中，我们可以根据所使用的平台（例如，Web、NativeScript，以及启用 JIT 或 AOT 编译）以不同的方式引导应用程序。让我们看看一个简单的示例，了解我们如何引导 Web 应用程序，以便更好地理解 Angular 模块在过程中的使用：

```js
import {NgModule} from '@angular/core';
import {platformBrowserDynamic} from '@angular/platform-browser-dynamic';
import {BrowserModule} from '@angular/platform-browser';

import {AppComponent} from './app.component';

@NgModule({
  imports: [BrowserModule],
  bootstrap: [AppComponent],
  declarations: [AppComponent],
})
export class AppModule {}

platformBrowserDynamic().bootstrapModule(AppModule);
```

在前面的示例中，使用`@NgModule`装饰器，我们声明了`AppModule`，并且还导入了`BrowserModule`。请注意，这次，我们向`bootstrap`属性提供了一个值，其中我们明确声明我们希望使用`AppComponent`来引导我们的应用程序。

在文件的最后一行，我们调用`platformBrowserDynamic`调用的返回对象上的`bootstrapModule`方法，并传递`AppModule`参数。

总结来说，Angular 中的模块扮演着重要的角色：它们不仅逻辑上组织了我们的应用程序的构建块，还提供了一种实现封装的方法。最后但同样重要的是，NgModules 在应用程序的引导过程中被大量使用。

# 管道

在业务应用程序中，我们经常需要具有相同数据的不同视觉表示。例如，如果我们有一个数字 100,000，我们希望将其格式化为货币，我们很可能不想以纯数据的形式显示它；更有可能的是，我们希望显示为$100,000。

在 AngularJS 中，格式化数据的责任被分配给了过滤器。另一个数据格式化需求示例是当我们使用项目集合时。例如，如果我们有一个项目列表，我们可能希望根据谓词（一个布尔函数）来过滤它；在一个数字列表中，我们可能只想显示素数。AngularJS 有一个名为`filter`的过滤器，允许我们这样做。然而，名称的重复经常导致混淆。这也是核心团队将过滤器组件重命名为**pipe**的原因之一。此外，单词*filter*使我们倾向于认为过滤器只负责过滤数据，而它们实际上有更通用的格式化数据的责任。

新名称背后的动机是管道和过滤器使用的语法：

```js
{{expression | decimal | currency}} 
```

在前面的示例中，我们将`decimal`和`currency`管道应用于`expression`返回的值。大括号之间的整个表达式看起来像 Unix 管道语法。

# 定义管道

定义管道的语法与用于定义模块、指令和组件的语法类似。为了创建一个新的管道，我们可以使用 ES2015 装饰器`@Pipe`。它允许我们向类添加元数据，将其声明为管道。我们所需做的就是为管道提供一个名称并定义数据格式化逻辑。

在运行时，一旦 Angular 表达式解释器发现给定的表达式包含对管道的调用，它将从中检索出分配在组件内的管道集合，并使用适当的参数调用它。

以下示例说明了我们如何定义一个简单的名为`lowercase1`的管道，它将作为参数传递的给定字符串转换为小写表示：

```js
@Pipe({ name: 'lowercase1' }) 
class LowerCasePipe1 implements PipeTransform { 
  transform(value: string): string { 
    if (!value) return value; 
    if (typeof value !== 'string') { 
      throw new Error('Invalid pipe value', value); 
    } 
    return value.toLowerCase(); 
  } 
} 
```

使用 TypeScript 语法，我们实现`PipeTransform`接口并定义其中声明的`transform`方法。我们将在下一章解释 TypeScript 接口。

现在，让我们演示如何在一个组件中使用`lowercase1`管道：

```js
@Component({ 
  selector: 'app', 
  template: '<h1>{{"SAMPLE" | lowercase1}}</h1>' 
}) 
class App {} 

@NgModule({
  declarations: [App, LowerCasePipe1],
  bootstrap: [App],
  imports: [BrowserModule]
})
class AppModule {}

platformBrowserDynamic().bootstrapModule(AppModule);
```

我们可以使用以下标记使用`App`组件：

```js
<app></app> 
```

我们将在屏幕上看到的`sample`文本是在`h1`元素内的。请注意，我们在`@NgModule`装饰器的`declarations`属性中包含了对`LowerCasePipe1`的引用。

通过将数据格式化逻辑作为一个单独的组件来保持，Angular 保持了一直以来可以看到的强关注点分离。我们将在 第八章 中查看如何为我们的应用程序定义有状态和无状态的管道，*解释管道和与 RESTful 服务通信*。

# 改进变更检测

如我们之前所看到的，MVC 中的视图根据从模型接收到的变更事件来更新自己。许多 **Model-View-Whatever** (**MVW**) 框架采取了这种方法，并在其变更检测机制的核心中嵌入观察者模式。

# 经典的变更检测

让我们看看一个简单的例子，它不使用任何框架。假设，我们有一个名为 `User` 的模型，它有一个名为 `name` 的属性：

```js
class User extends EventEmitter { 
  private name: string;

  setName(name: string) { 
    this.name = name; 
    this.emit('change');
  }

  getName(): string { 
    return this.name;
  } 
} 
```

前面的代码片段再次使用了 TypeScript。如果你对语法不熟悉，请不要担心，我们将在下一章介绍这门语言。

`user` 类扩展了 `EventEmitter` 类。这为发出和订阅事件提供了原语。

现在，让我们定义一个视图，它显示 `User` 类实例的 `name`，该实例作为其 `constructor` 的参数传递：

```js
class View { 
  constructor(user: User, el: Element /* a DOM element */) { 
    el.innerHTML = user.getName();
  } 
} 
```

我们可以像下面这样初始化 `view` 元素：

```js
let user = new User(); 
user.setName('foo'); 
let view = new View(user, document.getElementById('label')); 
```

最终的结果是，用户将看到一个包含内容 `foo` 的标签。然而，`user` 的变化不会在视图中反映出来。为了在用户 `name` 变化时更新视图，我们需要订阅 `change` 事件，然后更新 DOM 元素的 内容。我们需要以下方式更新 `View` 定义：

```js
class View { 
  constructor(user:User, el:any /* a DOM element */) { 
    el.innerHTML = user.getName(); 
    user.on('change', () => { 
      el.innerHTML = user.getName();
    }); 
  } 
} 
```

这就是大多数框架在 AngularJS 时代之前实现变更检测的方式。

# AngularJS 的变更检测

大多数初学者都对 AngularJS 中的数据绑定机制感到着迷。基本的 `Hello world!` 示例看起来像这样：

```js
function MainCtrl($scope) { 
  $scope.label = 'Hello world!'; 
}
<body ng-app ng-controller="MainCtrl"> 
  {{label}} 
</body> 
```

如果你运行这个程序，`Hello world!` 就会神奇地出现在屏幕上。然而，这并不是唯一令人印象深刻的事情！如果我们添加一个文本输入并将其绑定到作用域的 `label` 属性，每次更改都会反映在插值指令显示的内容中：

```js
<body ng-controller="MainCtrl"> 
  <input ng-model="label"> 
  {{label}} 
</body> 
```

这有多么酷！这是 AngularJS 的主要卖点之一：实现数据绑定的极端简单性。我们在标记中添加了一些属性，插值指令，将 `label` 属性绑定到一个神秘的对象 `$scope`，这个对象神奇地传递给我们定义的自定义函数，然后一切就简单地工作了！

经验更丰富的 Angular 开发者对幕后实际发生的事情有更好的理解。在前面的例子中，在`ng-model`和`ng-bind`指令（在我们的案例中，是插值指令`{{}}`）内部，Angular 添加了具有与同一表达式相关联的不同行为的 watchers：`label`。这些 watchers 与经典 MVC 模式中的观察者相当相似。在特定事件（在我们的案例中，是文本输入内容的变化）上，AngularJS 将遍历所有这样的 watchers，在给定作用域的上下文中评估与它们相关联的表达式，并存储它们的结果。这个循环被称为**digest 循环**。

在前面的例子中，在作用域的上下文中评估`label`表达式将返回文本，Hello world！。在每次迭代中，AngularJS 将比较评估的当前结果与之前的结果，并在值不同的情况下调用相关的回调。例如，由插值指令添加的回调将设置元素的 内容为新表达式评估的结果。这是两个指令的 watchers 回调之间依赖关系的一个例子。由`ng-model`添加的 watcher 的回调修改了与插值指令添加的 watcher 相关联的表达式的结果。

这种方法有其自身的缺点。我们说 digest 循环将在某些特定事件上被调用，但如果我们使用`setTimeout`，并在回调中（作为第一个参数传递），改变我们正在监视的作用域附加的属性，这些事件会发生在框架之外；例如，会发生什么？AngularJS 将不会意识到这种变化，并且不会调用 digest 循环，因此我们需要显式使用`$scope.$apply`来做这件事。但是，如果框架知道浏览器中发生的所有异步事件，例如用户事件、`XMLHttpRequest`事件和与`WebSocket`相关的事件，会怎样呢？在这种情况下，Angular 将能够拦截事件处理并调用 digest 循环，而无需强迫我们这样做！

# 在 zone.js 中

这正是 Angular 的情况。这个功能是通过使用`zone.js`中的 zones 实现的。

在 2014 年的 ng-conf 会议上，布赖恩·福特（Brian Ford）做了一场关于 zones 的演讲。布赖恩将 zones 描述为浏览器 API 的元猴子补丁（meta-monkey patching）。`zone.js`是由 Angular 团队开发的一个库，它在 JavaScript 中实现了 zones。zones 代表一个执行上下文，它允许我们拦截异步浏览器调用。基本上，使用 zones，我们能够在给定的`XMLHttpRequest`对象完成或当我们接收到新的`WebSocket`事件时，调用一段逻辑。Angular 通过拦截异步浏览器事件并在正确的时间调用 digest 循环来利用`zone.js`。这完全消除了使用 Angular 的开发者显式调用 digest 循环的需求。

# 简化的数据流

跨观察者依赖关系可能会在我们的应用程序中创建复杂的数据流，难以追踪。这可能导致不可预测的行为和错误，难以找到。尽管 Angular 保留了脏检查作为实现更改检测的方式，但框架强制执行单向数据流。这是通过禁止不同观察者之间的依赖关系实现的，这允许消化循环只运行一次。这种策略显著提高了我们应用程序的性能，并降低了数据流的复杂性。Angular 还对内存效率和消化循环的性能进行了改进。有关 Angular 的更改检测及其实现的不同策略的更多详细信息，请参阅第五章，《开始使用 Angular 组件和指令》。

# 提升 AngularJS 的更改检测

现在，让我们退一步，再次思考框架的更改检测机制。

我们说在消化循环中，Angular 评估注册的表达式，并将评估的值与循环前一次迭代中相同表达式的值进行比较。

用于比较的最优算法可能因表达式评估返回的值类型而异。例如，如果我们得到一个可变列表项，我们需要遍历整个集合，逐个比较集合中的项，以验证是否有变化。然而，如果我们有一个不可变列表，我们只需通过比较引用即可执行检查，其复杂度保持不变。这是因为不可变数据结构的实例不能改变：而不是修改实例，我们将获得一个应用了修改的新引用。

在 AngularJS 中，我们可以使用几种方法添加观察者。其中两种是`$watch(exp, fn, deep)`和`$watchCollection(exp, fn)`。这些方法让我们在一定程度上控制更改检测将如何执行等性检查。例如，使用`$watch`添加观察者并将`false`值作为第三个参数传递将使 AngularJS 执行引用检查（即使用`===`比较当前值和前一个值）。然而，如果我们传递一个真值（任何`true`值），检查将是深层次的（即使用`angular.equals`）。这样，根据表达式返回的预期类型，我们可以以最合适的方式添加监听器，以便框架使用最优化算法执行等性检查。此 API 有两个限制：

+   它不允许你在运行时选择最合适的等性检查算法

+   它不允许你将更改检测扩展到第三方特定的数据结构

Angular 核心团队将这一责任分配给了比对器，允许它们根据我们在应用程序中使用的数据进行扩展，优化变更检测机制。Angular 定义了两个基类，我们可以扩展它们来定义自定义算法：

+   `KeyValueDiffer`：这允许我们对基于键值的数据结构进行高级的比对操作

+   `IterableDiffer`：这允许我们对类似列表的数据结构进行高级的比对操作

Angular 允许我们通过扩展自定义算法或适当配置来完全控制变更检测机制，这在 AngularJS 中是不可能的。我们将在第五章，*开始使用 Angular 组件和指令*中进一步探讨变更检测及其配置方法。

# 服务

服务是 Angular 为定义我们应用程序的业务逻辑提供的构建块。在 AngularJS 中，我们有三种不同的方式来定义服务：

```js
// The Factory method 
module.factory('ServiceName', function (dep1, dep2, ...) { 
  return { 
    // public API 
  }; 
}); 

// The Service method 
module.service('ServiceName', function (dep1, dep2, ...) { 
  // public API 
  this.publicProp = val; 
}); 

// The Provider method 
module.provider('ServiceName', function () { 
  return { 
    $get: function (dep1, dep2, ...) { 
      return { 
        // public API 
      }; 
    } 
  }; 
}); 
```

尽管前两种语法变体提供了类似的功能，但它们在注册服务实例化的方式上有所不同。第三种语法允许在配置期间进一步配置注册的提供者。

有三种不同的方法来定义服务对于 AngularJS 初学者来说相当混乱。让我们思考一下，是什么促使引入这些方法来注册服务。为什么我们不能简单地使用 JavaScript 构造函数、对象字面量或 ES2015 类，Angular 将不会意识到这些？我们可以像这样将我们的业务逻辑封装在一个自定义 JavaScript 构造函数中：

```js
function UserTransactions(id) { 
  this.userId = id; 
}

UserTransactions.prototype.makeTransaction = function (amount) { 
  // method logic 
}; 

module.controller('MainCtrl', function () { 
  this.submitClick = function () { 
    new UserTransactions(this.userId).makeTransaction(this.amount); 
  }; 
}); 
```

这段代码是完全有效的。然而，它没有利用 AngularJS 提供的关键特性之一：依赖注入（DI）机制。《MainCtrl》函数使用了可见于其体内的 `UserTransaction` 构造函数。前面的代码有两个主要缺陷：

+   我们与服务实例化所使用的逻辑耦合在一起。

+   代码不可测试。为了模拟 `UserTransactions`，我们需要对其进行猴子补丁。

AngularJS 如何处理这两件事呢？当需要某个服务时，通过框架的依赖注入（DI）机制，AngularJS 解决其所有依赖关系，并通过传递给一个 `factory` 函数来实例化它，该函数封装了其创建的逻辑。`factory` 函数作为 `factory` 和 `service` 方法的第二个参数传递。`provider` 方法允许在较低级别上定义服务；对于 `factory` 方法，有一个位于 `$get` 属性下的方法。

就像 AngularJS 一样，Angular 的新版本也容忍这种关注点的分离，因此核心团队保留了服务。与 AngularJS 相比，Angular 通过允许我们使用纯 ES2015 类来定义它们的接口，提供了一个更简单的接口。我们无法回避这样一个事实，即我们需要明确声明应可注入的服务，并指定它们的实例化指令。与 AngularJS 相比，现在框架使用 ES2016 装饰器的语法和提供者来实现这一目的，而不是我们从 AngularJS 中熟悉的那些方法。这使得我们可以在应用程序中将服务定义为简单的 ES2015 类，并使用装饰器来配置 DI：

```js
import {Injectable} from '@angular/core'; 

@Injectable() 
class HttpService { 
  constructor() { /* ... */ } 
} 

@Injectable() 
class User { 
  constructor(private service: HttpService) {}

  save() { 
    return this.service.post('/users') 
      .then(res => { 
        this.id = res.id; 
        return this; 
      }); 
  } 
} 
```

服务与前面章节中描述的组件和指令相关。为了开发高度一致和可重用的 UI 组件，我们需要将所有与业务相关的逻辑移动到我们的服务内部。此外，为了开发可测试的组件，我们需要利用 DI 机制来解决它们的所有依赖。

AngularJS 和 Angular 中服务之间的另一个关键区别是框架内部表示它们的方式。AngularJS 使用字符串来标识不同的服务及其用于实例化的相关工厂。另一方面，现在 Angular 使用键。通常，键是不同服务的类型。在实例化方面的另一个核心区别是注入器的层次结构，它们封装了具有不同可见性的不同依赖提供者。

AngularJS 和 Angular 中服务之间的另一个区别是简化的语法。Angular 中的 DI 具有完全不同的语法，并通过提供一种一致的方式来注入依赖项而改进了行为。你还可以在第六章中找到关于 Angular 服务和 DI 的更详细解释，*Angular 中的依赖注入*。

# 新的路由

在传统的 Web 应用程序中，所有页面更改都与整个页面的重新加载相关联，这会获取所有引用的资源和数据，并将整个页面渲染到屏幕上。然而，Web 应用程序的需求随着时间的推移而演变。

我们用 Angular 构建的**单页应用程序**（**SPAs**）模拟桌面用户体验。这通常涉及逐步加载应用程序所需的资源和数据，并且在初始页面加载后不会进行整个页面的重新加载。通常，SPAs 中的不同页面或视图由不同的模板表示，这些模板异步加载并在屏幕上的特定位置渲染。稍后，当包含所有所需资源的模板加载完毕且路由发生变化时，将调用与所选页面关联的逻辑，并用数据填充模板。如果用户在 SPA 中加载给定页面后点击刷新按钮，则在视图刷新完成后，需要重新渲染相同的页面。这涉及到类似的行为：找到请求的视图，获取包含所有引用资源的所需模板，并调用与该视图关联的逻辑。

需要获取的模板以及页面刷新成功后应调用的逻辑，取决于用户点击刷新按钮之前选中的视图。框架通过解析包含当前选中页面标识符的页面 URL，该标识符以分层结构表示，来确定这一点。

所有与导航、更改 URL、加载适当的模板以及在视图加载时调用特定逻辑相关的责任都分配给了路由器组件。这些都是一些相当具有挑战性的任务，并且为了实现跨浏览器的兼容性，需要支持不同的导航 API，这使得在现代 SPAs 中实现路由成为一个非平凡的问题。

AngularJS 在其核心中引入了路由器，后来将其外部化为`ngRoute`模块。它通过为每个页面提供一个模板以及当页面被选中时需要调用的逻辑，以声明性方式定义我们 SPA 中的不同视图。然而，路由器的功能有限。它不支持诸如嵌套视图路由等基本功能。这是大多数开发者更喜欢使用社区开发的`ui-router`的原因之一。AngularJS 的路由器和`ui-router`的路由定义都包括一个路由配置对象，它定义了一个与页面关联的模板和一个控制器。

如前几节所述，Angular 改变了它为开发 SPAs 提供的构建块。Angular 移除了浮动控制器，而是将视图表示为组件的组合。这需要开发一个全新的路由器，它赋予了这些新概念能力。

AngularJS 路由器和新的 Angular 路由器之间的核心差异如下：

+   新的路由器是基于组件的，而`ngRoute`不是。新的 Angular 路由器将一个组件与单个路由或懒加载路由的情况下的模块关联起来。

+   现在支持嵌套视图。

# Angular 路由定义语法

让我们简要了解一下 Angular 路由器为我们应用定义路由提供的新语法：

```js
import {Component, NgModule} from '@angular/core';
import {BrowserModule} from '@angular/platform-browser';
import {RouterModule, Routes} from '@angular/router';

import {HomeComponent} from './home/home.component';
import {AboutComponent} from './about/about.component';
import {AppComponent} from './app.component';

const routes: Routes = [
  { path: 'home', component: HomeComponent },
  { path: 'about', component: AboutComponent }
];

@NgModule({ 
  imports: [BrowserModule, RouterModule.forRoot(routes)],
  declarations: [AppComponent, HomeComponent, AboutComponent],
  bootstrap: [AppComponent]
})
export class AppModule {} 
```

由于第七章，*使用 Angular 路由器和表单*和第八章，*解释管道和与 RESTful 服务通信*都专门介绍了新的路由器，但让我们提及前面代码片段的主要要点。

路由器位于 `@angular/router`。由于 `AppModule` 是我们应用的根模块，我们使用 `RouterModule` 的 `forRoot` 方法来导入路由器导出的所有必需指令和服务。

传递给 `RouterModule.forRoot` 装饰器的参数显示了我们在应用中定义路由的方式。我们使用一个对象数组，它定义了路由与它们关联的组件之间的映射。

# 摘要

在本章中，我们快速概述了 Angular 提供的用于开发单页应用（SPAs）的主要构建块。我们指出了与 AngularJS 核心概念的主要区别。

在下一章中，我们将探讨 TypeScript 以及我们如何在下一个应用中开始使用它。我们还将解释如何通过环境类型定义，在编写纯 JavaScript 的库和框架中使用静态类型。
