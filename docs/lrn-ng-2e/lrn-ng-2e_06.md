# 第六章：使用 Angular 组件构建应用程序

我们已经达到了一个阶段，在这个阶段，我们可以通过在其他组件中嵌套组件来成功开发更复杂的应用程序，形成一种组件树。然而，将所有组件逻辑捆绑在一个唯一的文件中绝对不是正确的方法。我们的应用程序很快可能变得难以维护，并且正如我们将在本章后面看到的那样，我们将错过 Angular 的依赖管理机制可以为游戏带来的优势。

在本章中，我们将看到如何基于组件树构建应用程序架构，以及新的 Angular 依赖注入机制如何帮助我们以最小的工作量和最佳结果声明和使用应用程序中的依赖项。

在本章中，我们将涵盖以下主题：

+   目录结构和命名约定的最佳实践

+   依赖注入的不同方法

+   将依赖项注入到我们的自定义类型中

+   在整个组件树中覆盖全局依赖项

+   与宿主组件交互

+   概述指令生命周期

+   概述组件生命周期

# 介绍组件树

基于 Web 组件架构的现代 Web 应用程序通常符合一种树形层次结构，其中顶层主要组件（通常放置在主 HTML 索引文件的某个位置）充当全局占位符，子组件成为其他嵌套子组件的宿主，依此类推。

这种方法有明显的优势。一方面，可重用性不会受到损害，我们可以轻松地在组件树中重用组件。其次，由此产生的细粒度减少了构想、设计和维护更大型应用程序所需的负担。我们可以简单地专注于单个 UI 部分，然后将其功能包装在新的抽象层周围，直到我们从头开始包装一个完整的应用程序。

或者，我们可以从另一个角度来处理我们的 Web 应用程序，从更通用的功能开始，最终将应用程序拆分为更小的 UI 和功能部分，这些部分成为我们的 Web 组件。后者已成为构建基于组件的架构时最常见的方法。我们将在本书的其余部分坚持这一方法，将架构视为下图所示的架构：

```ts
Application bootstrap
Root module
 Root component that is Application component
 Component A
 Component B
 Component B-I
 Component B-II
 Component C
 Component D
Feature module
 Component E
 Component F
Common module
 Component G
 Component H
```

为了清晰起见，本章将借用我们在前几章中编写的代码，并将其拆分为组件层次结构。我们还将为最终应用程序中所有支持类和模型分配一些空间，以塑造我们的番茄工具。这将成为学习 Angular 中内置的依赖注入机制的绝佳机会，我们将在本章后面看到。

# 可扩展应用程序的通用约定

公平地说，我们已经解决了现代网页开发人员在构建应用程序时所面临的许多常见问题，无论是小型还是大型应用程序。因此，定义一个架构来将上述问题分离成单独的领域文件夹，满足媒体资产和共享代码单元的需求是有意义的。

Angular 将代码和资产分离的方法是通过将它们组织到不同的文件夹中，同时引入 Angular 模块的概念。在这些模块中注册构造。通过引入模块，我们的组件中的许多噪音已经消失，我们的组件可以自由地使用同一模块中的其他构造，有时甚至可以使用其他模块中的构造，前提是导入其所在的模块。

值得强调的是，当我们谈论 Angular 模块时，我们指的是`@NgModule`装饰器，当我们谈论模块时，我们指的是 ES2015 构造。

有时，两个上下文可能需要共享相同的实体，这是可以接受的（只要在我们的项目中不成为常见情况，这将表示严重的设计问题）。还值得强调的是，我们使用“上下文”一词来描述构造的逻辑边界。上下文最好保留在一个 Angular 模块中。因此，每当使用“上下文”一词时，都要考虑在代码中将其转换为一个 Angular 模块。

以下示例应用于我们之前在番茄工作法组件上的工作，基本上构成了我们整个应用程序的上下文和不同构造。

+   任务上下文：

+   任务模块

+   任务模型

+   任务服务

+   任务表组件

+   任务番茄钟组件

+   任务工具提示指令

+   计时器上下文：

+   计时器模块

+   计时器功能

+   计时器组件

+   管理员上下文：

+   管理员模块

+   认证服务

+   登录组件

+   编辑器组件

+   共享上下文：

+   共享模块

+   跨功能共享的组件

+   跨功能共享的管道

+   跨功能共享的指令

+   全局模型和服务

+   共享媒体资产

正如我们所看到的，第一步是定义应用程序需要的不同功能，要记住的是，每个功能在与其他功能隔离时应该是有意义的。一旦我们定义了所需的功能集，我们将为每个功能创建一个模块。然后，每个模块将填充代表其特征的组件、指令、管道、模型和服务。在定义功能集时，请始终记住封装和可重用性的原则。

最初，在启动项目时，您应该根据它们的名称命名您的构造，所以说我们有`Admin`上下文，它应该看起来像这样：

```ts
//admin/

admin.module.ts
authentication.service.ts
login.component.ts
editor.component.ts
```

通过快速浏览，您应该能够看到构造包含的内容，因此使用类似于以下的命名标准：

```ts
<name>.<type>.ts // example login.service.ts
```

当然，这不是唯一的方法。还有另一种完全可以接受的方法，即为每种类型创建子目录，因此您之前的`admin`目录可能看起来像这样：

```ts
//admin/

admin.module.ts
services/
 authentication.service.ts
components/
 login.component.ts
 login.component.html
 editor.component.ts
 create-user.component.ts
pipes/
 user.pipe.ts
```

值得注意的是，为了便于调试，您应该在文件名中保留类型。否则，当在浏览器中寻找特定文件以设置断点时，比如登录服务，如果您开始输入`login.ts`，然后出现以下情况可能会相当令人困惑：

+   `components/login.ts`

+   `services/login.ts`

+   `pipes/login.ts`

有一个官方的样式指南，告诉您应该如何组织代码以及如何命名您的构造。遵循指南肯定有好处；对新手来说很容易，代码看起来更一致等等。您可以在这里阅读更多信息；[`angular.io/guide/styleguide`](https://angular.io/guide/styleguide)。请记住，无论您选择是否完全遵循此样式指南，一致性都很重要，因为这将使维护代码变得更容易。

# 文件和 ES6 模块命名约定

我们的每个功能文件夹将托管各种文件，因此我们需要一致的命名约定，以防止文件名冲突，同时确保不同的代码单元易于定位。

以下列表总结了社区强制执行的当前约定：

+   每个文件应包含一个代码单元。简而言之，每个组件、指令、服务、管道等都应该存在于自己的文件中。这样，我们有助于更好地组织代码。

+   文件和目录以小写 kebab-case 命名。

+   表示组件、指令、管道和服务的文件应该在它们的名称后面添加一个类型后缀：`video-player.ts`将变成`video-player.component.ts`。

+   任何组件的外部 HTML 模板或 CSS 样式表文件名都将与组件文件名匹配，包括后缀。例如，我们的`video-player.component.ts`可能会有`video-player.component.css`和`video-player.component.html`。

+   指令选择器和管道名称采用驼峰式命名，而组件选择器采用小写 kebab-case 命名。此外，强烈建议添加我们选择的自定义前缀，以防止与其他组件库发生名称冲突。例如，跟随我们的视频播放器组件，它可以表示为`<vp-video-player>`，其中`vp-`（代表 video-player）是我们的自定义前缀。

+   模块的命名遵循 PascalCased 规则

自描述名称，以及它所代表的类型。例如，如果我们看到一个名为`VideoPlayerComponent`的模块，我们可以轻松地知道它是一个组件。在选择器中使用的自定义前缀（在我们的示例中为`vp-`）不应该成为模块名称的一部分。

+   模型和接口需要特别注意。根据您的应用程序架构，模型类型的相关性会更多或更少。诸如 MVC、MVVM、Flux 或 Redux 的架构从不同的角度和重要性等级处理模型。最终，您和您选择的架构设计模式将决定以一种方式或另一种方式处理模型和它们的命名约定。本书在这方面不会表达观点，尽管我们在示例应用程序中强制执行接口模型，并将为它们创建模块。

+   我们应用程序中的每个业务逻辑组件和共享上下文都旨在以简单直接的方式与其他部分集成。每个子域的客户端都不关心子域本身的内部结构。例如，如果我们的定时器功能发展到需要重新组织成不同的文件夹层次结构，其功能的外部消费者应该保持不受影响。

# 从 facade/barrel 到 NgModule

随着应用程序的增长，有必要将构造分组为逻辑组。随着应用程序的增长，您还意识到并非所有构造都应该能够相互通信，因此您还需要考虑限制这一点。在框架中添加`@NgModule`之前，自然的做法是考虑外观模块，这基本上意味着我们创建了一个具有决定将被导出到外部世界的唯一目的的特定文件。这可能看起来像下面这样：

```ts
import TaskComponent from './task.component';
import TaskDetailsComponent from './task-details.component';
// and so on
export {
 TaskComponent,
 TaskDetailsComponent,
 // other constructs to expose
}
```

一切未明确导出的内容都将被视为私有或内部特性。使用其中一个导出的构造将像输入一样简单：

```ts
import { TaskComponent } from './task.component.ts';
// do something with the component above
```

这是一种处理分组和限制访问的有效方式。当我们深入研究下一小节中的`@NgModule`时，我们将牢记这两个特性。

# 使用 NgModule

随着`@NgModule`的到来，我们突然有了一种更合乎逻辑的方式来分组我们的构造，并且也有了一种自然的方式来决定什么可以被导出或不导出。以下代码对应于前面的外观代码，但它使用了`@NgModule`：

```ts
import { NgModule } from  '@angular/core'; import { TaskDetailComponent } from  './task.detail.component'; import { TaskDetailsComponent } from  './task.details.component'; import { TaskComponent } from  './task.component';   @NgModule({
  declarations: [TaskComponent, TaskDetailsComponent], exports: [TaskComponent, TaskDetailComponent] })
export  class  TaskModule { }
```

这将创建相同的效果，该构造称为特性模块。`exports`关键字表示了什么是公开访问的或不是。然而，获取公开访问的内容看起来有点不同。而不是输入：

```ts
import { TaskDetailComponent } from 'app/tasks/tasks';
```

我们需要将我们的特性模块导入到我们的根模块中。这意味着我们的根模块将如下所示：

```ts
import { TaskModule } from './task.module';

@NgModule({
  imports: [ TasksModule ]
 // the rest is omitted for brevity
}) 
```

这将使我们能够在模板标记中访问导出的组件。因此，在您即将构建的应用程序中，请考虑什么属于根模块，什么是特性的一部分，以及什么是更常见的并且在整个应用程序中都使用。这是您需要拆分应用程序的方式，首先是模块，然后是适当的构造，如组件、指令、管道等。

# 在 Angular 中依赖注入是如何工作的

随着我们的应用程序的增长和发展，我们的每一个代码实体在内部都需要其他对象的实例，这在软件工程领域更为常见的称为依赖关系。将这些依赖关系传递给依赖客户端的行为称为注入，它还涉及另一个名为注入器的代码实体的参与。注入器将负责实例化和引导所需的依赖关系，以便在成功注入客户端后立即可以使用。这非常重要，因为客户端对如何实例化自己的依赖关系一无所知，只知道它们实现的接口以便使用它们。

Angular 具有一流的依赖注入机制，可以轻松地将所需的依赖关系暴露给 Angular 应用程序中可能存在的任何实体，无论是组件、指令、管道还是任何其他自定义服务或提供者对象。事实上，正如我们将在本章后面看到的，任何实体都可以利用 Angular 应用程序中的依赖注入（通常称为 DI）。在深入讨论这个主题之前，让我们先看看 Angular 的 DI 试图解决的问题。

让我们看看我们是否有一个音乐播放器组件，它依赖于一个“播放列表”对象来向用户播放音乐：

```ts
import { Component } from  '@angular/core'; import { Playlist } from  './playlist.model'; @Component({
  selector:  'music-player', templateUrl:  './music-player.component.html' })
export  class  MusicPlayerComponent { playlist:  Playlist; constructor() { this.playlist  =  new  Playlist();
 }}
}
```

“播放列表”类型可能是一个通用类，在其 API 中返回一个随机的歌曲列表或其他内容。现在这并不重要，因为唯一重要的是我们的`MusicPlayerComponent`实体确实需要它来提供功能。不幸的是，先前的实现意味着这两种类型紧密耦合，因为组件在自己的构造函数中实例化了播放列表。这意味着如果需要，我们无法以整洁的方式更改、覆盖或模拟“播放列表”类。这也意味着每次我们实例化一个`MusicPlayerComponent`时都会创建一个新的“播放列表”对象。在某些情况下，这可能是不希望的，特别是如果我们希望在整个应用程序中使用单例并因此跟踪播放列表的状态。

依赖注入系统试图通过提出几种模式来解决这些问题，而构造函数注入模式是 Angular 强制执行的模式。前面的代码片段可以重新思考如下：

```ts
import { Component } from  '@angular/core'; import { Playlist } from  './playlist.model'; @Component({
 selector: 'music-player',
 templateUrl: './music-player.component.html'
})
export class MusicPlayerComponent {
 constructor(private playlist: Playlist) {}
}
```

现在，`Playlist`是在我们的组件外部实例化的。另一方面，`MusicPlayerComponent`期望在组件实例化之前已经有这样一个对象可用，以便通过其构造函数注入。这种方法使我们有机会覆盖它或者模拟它。

基本上，这就是依赖注入的工作原理，更具体地说是构造函数注入模式。但是，这与 Angular 有什么关系呢？Angular 的依赖注入机制是通过手动实例化类型并通过构造函数注入它们吗？显然不是，主要是因为我们也不会手动实例化组件（除非编写单元测试时）。Angular 具有自己的依赖注入框架，顺便说一句，这个框架可以作为其他应用程序的独立框架使用。

该框架提供了一个实际的注入器，可以审视构造函数中用于注释参数的标记，并返回每个依赖类型的单例实例，因此我们可以立即在类的实现中使用它，就像前面的例子一样。注入器不知道如何创建每个依赖项的实例，因此它依赖于在应用程序引导时注册的提供者列表。这些提供者实际上提供了对标记为应用程序依赖项的类型的映射。每当一个实体（比如一个组件、一个指令或一个服务）在其构造函数中定义一个标记时，注入器会在该组件的已注册提供者池中搜索与该标记匹配的类型。如果找不到匹配项，它将委托给父组件的提供者进行搜索，并将继续向上进行提供者的查找，直到找到与匹配类型的提供者或者达到顶层组件。如果提供者查找完成后没有找到匹配项，Angular 将抛出异常。

后者并不完全正确，因为我们可以使用`@Optional`参数装饰器在构造函数中标记依赖项，这种情况下，如果找不到提供者，Angular 将不会抛出任何异常，并且依赖参数将被注入为 null。

每当提供程序解析为与该令牌匹配的类型时，它将返回此类型作为单例，因此将被注入器作为依赖项注入。公平地说，提供程序不仅仅是将令牌与先前注册的类型进行配对的键/值对集合，而且还是一个工厂，它实例化这些类型，并且也实例化每个依赖项自己的依赖项，以一种递归依赖项实例化的方式。

因此，我们可以这样做，而不是手动实例化`Playlist`对象：

```ts
import { Component } from  '@angular/core'; import { Playlist } from  './playlist'; @Component({
  selector:  'music-player', templateUrl:  './music-player.component.html', providers: [Playlist**]** })
export  class  MusicPlayerComponent { constructor(private  playlist:  Playlist) {} }
```

`@Component`装饰器的`providers`属性是我们可以在组件级别注册依赖项的地方。从那时起，这些类型将立即可用于该组件的构造函数注入，并且，正如我们将在接下来看到的，也可用于其子组件。

# 关于提供程序的说明

在引入`@NgModule`之前，Angular 应用程序，特别是组件，被认为是负责其所需内容的。因此，组件通常会要求其需要的依赖项以正确实例化。在上一节的示例中，`MusicPlayerComponent`请求一个`Playlist`依赖项。虽然这在技术上仍然是可能的，但我们应该使用我们的新`@NgModule`概念，而不是在模块级别提供构造。这意味着先前提到的示例将在模块中注册其依赖项，如下所示：

```ts
@NgModule({
 declarations: [MusicComponent, MusicPlayerComponent]
 providers: [Playlist, SomeOtherService]
})
```

在这里，我们可以看到`Playlist`和`SomeOtherService`将可用于注入，对于在 declarations 属性中声明的所有构造。正如你所看到的，提供服务的责任在某种程度上已经转移。正如之前提到的，这并不意味着我们不能在每个组件级别上提供构造，存在这样做有意义的用例。然而，我们想强调的是，通常情况是将需要注入的服务或其他构造放在模块的`providers`属性中，而不是组件中。

# 跨组件树注入依赖项

我们已经看到，provider 查找是向上执行的，直到找到匹配项。一个更直观的例子可能会有所帮助，所以让我们假设我们有一个音乐应用程序组件，在其指令属性（因此也在其模板中）中托管着一个音乐库组件，其中包含我们下载的所有曲目的集合，还托管着一个音乐播放器组件，因此我们可以在我们的库中播放任何曲目：

```ts
MusicAppComponent
 MusicLibraryComponent
 MusicPlayerComponent
```

我们的音乐播放器组件需要我们之前提到的`Playlist`对象的一个实例，因此我们将其声明为构造函数参数，并方便地用`Playlist`标记进行注释：

```ts
MusicAppComponent
 MusicLibraryComponent
 MusicPlayerComponent(playlist: Playlist)
```

当`MusicPlayerComponent`实体被实例化时，Angular DI 机制将会遍历组件构造函数中的参数，并特别关注它们的类型注解。然后，它将检查该类型是否已在组件装饰器配置的 provider 属性中注册。代码如下：

```ts
@Component({
 selector: 'music-player',
 providers: [Playlist]
})
export class MusicPlayerComponent {
 constructor(private playlist: Playlist) {}
}
```

但是，如果我们想在同一组件树中的其他组件中重用`Playlist`类型呢？也许`Playlist`类型在其 API 中包含了一些不同组件在应用程序中同时需要的功能。我们需要为每个组件在 provider 属性中声明令牌吗？幸运的是不需要，因为 Angular 预见到了这种必要性，并通过组件树带来了横向依赖注入。

在前面的部分中，我们提到组件向上进行 provider 查找。这是因为每个组件都有自己的内置注入器，它是特定于它的。然而，该注入器实际上是父组件注入器的子实例（依此类推），因此可以说 Angular 应用程序不是一个单一的注入器，而是同一个注入器的许多实例。

我们需要以一种快速且可重用的方式扩展`Playlist`对象在组件树中的注入。事先知道组件从自身开始执行提供者查找，然后将请求传递给其父组件的注入器，我们可以通过在父组件中注册提供者，甚至是顶级父组件中注册提供者来解决这个问题，这样依赖项将可用于每个子组件的注入。在这种情况下，我们可以直接在`MusicAppComponent`中注册`Playlist`对象，而不管它是否需要它进行自己的实现：

```ts
@Component({
 selector: 'music-app',
 providers: [Playlist],
 template: '<music-library></music-library>'
})
export class MusicAppComponent {}
```

即使直接子组件可能也不需要依赖项进行自己的实现。由于它已经在其父`MusicAppComponent`组件中注册，因此无需再次在那里注册：

```ts
@Component({
 selector: 'music-library',
 template: '<music-player></music-player>'
})
export class MusicLibraryComponent {}
```

最后，我们到达了我们的音乐播放器组件，但现在它的`providers`属性中不再包含`Playlist`类型作为注册令牌。实际上，我们的组件根本没有`providers`属性。它不再需要这个，因为该类型已经在组件层次结构的某个地方注册，立即可用于所有子组件，无论它们在哪里：

```ts
@Component({
 selector: 'music-player'
})
export class MusicPlayerComponent {
 constructor(private playlist: playlist) {}
}
```

现在，我们看到依赖项如何向下注入组件层次结构，以及组件如何执行提供者查找，只需检查其自己注册的提供者并将请求向上冒泡到组件树中。但是，如果我们想限制这种注入或查找操作呢？

# 限制依赖项向下注入组件树

在我们之前的例子中，我们看到音乐应用组件在其提供者集合中注册了播放列表令牌，使其立即可用于所有子组件。有时，我们可能需要限制依赖项的注入，仅限于层次结构中特定组件旁边的那些指令（和组件）。我们可以通过在组件装饰器的`viewProviders`属性中注册类型令牌来实现这一点，而不是使用我们已经看到的 providers 属性。在我们之前的例子中，我们可以仅限制`Playlist`的向下注入一级：

```ts
@Component({
 selector: 'music-app',
 viewProviders : [Playlist],
 template: '<music-library></music-library>'
})
export class MusicAppComponent {}
```

我们正在告知 Angular，`Playlist`提供程序只能被位于`MusicAppComponent`视图中的指令和组件的注入器访问，而不是这些组件的子级。这种技术的使用是组件的专属，因为只有它们具有视图。

# 限制提供程序查找

就像我们可以限制依赖注入一样，我们可以将依赖查找限制在仅限于直接上一级。为此，我们只需要将`@Host()`装饰器应用于那些我们想要限制提供程序查找的依赖参数：

```ts
import {Component, Host} from '@angular/core';

@Component {
 selector: 'music-player'
}
export class MusicPlayerComponent {
 constructor(@Host() playlist:Playlist) {}
}
```

根据前面的例子，`MusicPlayerComponent`注入器将在其父组件的提供程序集合（在我们的例子中是`MusicLibraryComponent`）中查找`Playlist`类型，并在那里停止，抛出异常，因为`Playlist`没有被父级注入器返回（除非我们还用`@Optional()`参数装饰器装饰它）。

为了澄清这个功能，让我们做另一个例子：

```ts
@Component({
 selector: 'granddad',
 template: 'granddad <father>'
 providers: [Service]
})
export class GranddadComponent {
 constructor(srv:Service){}
}

@Component({
 selector: 'father',
 template: 'father <child>'
})
export class FatherComponent {
 constructor(srv:Service) {} // this is fine, as GranddadComponent provides Service
}

@Component({
 selector: 'child',
 template: 'child'
})
export class ChildComponent {
  constructor(@Host() srv:Service) {} // will cause an error
}
```

在这种情况下，我们会得到一个错误，因为`Child`组件只会向上查找一级，尝试找到服务。由于它向上两级，所以找不到。

# 在注入器层次结构中覆盖提供程序

到目前为止，我们已经看到了 Angular 的 DI 框架如何使用依赖标记来内省所需的类型，并从组件层次结构中可用的任何提供程序集中返回它。然而，在某些情况下，我们可能需要覆盖与该标记对应的类实例，以便需要更专业的类型来完成工作。Angular 提供了特殊工具来覆盖提供程序，甚至实现工厂，该工厂将返回给定标记的类实例，不一定匹配原始类型。

我们在这里不会详细涵盖所有用例，但让我们看一个简单的例子。在我们的例子中，我们假设`Playlist`对象应该在组件树中的不同实体中可用。如果我们的`MusicAppComponent`指令托管另一个组件，其子指令需要`Playlist`对象的更专业版本，该怎么办？让我们重新思考我们的例子：

```ts
MusicAppComponent
 MusicChartsComponent
 MusicPlayerComponent
 MusicLibraryComponent
 MusicPlayerComponent
```

这是一个有点牵强的例子，但它肯定会帮助我们理解覆盖依赖项的要点。 `Playlist`实例对象从顶部组件向下都是可用的。 `MusicChartsComponent`指令是一个专门为畅销榜中的音乐提供服务的组件，因此其播放器必须仅播放热门歌曲，而不管它是否使用与`MusicLibraryComponent`相同的组件。我们需要确保每个播放器组件都获得适当的播放列表对象，这可以在`MusicChartsComponent`级别通过覆盖与`Playlist`标记对应的对象实例来完成。以下示例描述了这种情况，利用了`provide`函数的使用：

```ts
import { Component } from '@angular/core';
import { Playlist } from './playlist';

import { TopHitsPlaylist } from './top-hits/playlist';

@Component({
 selector: 'music-charts',
 template: '<music-player></music-player>',
 providers: [{ provide : Playlist, useClass : TopHitsPlaylist }]
})
export class MusicChartsComponent {}
```

`provide`关键字创建了一个与第一个参数中指定的标记（在本例中为`Playlist`）映射的提供程序，而`useClass`属性本质上是用来从该组件和下游重写播放列表为`TopHitsPlaylist`。

我们可以重构代码块以使用`viewProviders`，以确保（如果需要）子实体仍然接收`Playlist`的实例，而不是`TopHitsPlaylist`。或者，我们可以走额外的路线，并使用工厂根据其他要求返回我们需要的特定对象实例。以下示例将根据布尔条件变量的评估返回`Playlist`标记的不同对象实例：

```ts
function playlistFactory() {
 if(condition) { 
 return new Playlist(); 
 }
 else { 
 return new TopHitsPlaylist(); 
 }
}

@Component({
 selector: 'music-charts',
 template: '<music-player></music-player>',
 providers: [{ provide : Playlist, useFactory : playlistFactory }]
})
export class MusicChartsComponent {}
```

所以，你可以看到这有多强大。例如，我们可以确保在测试时，我们的数据服务突然被模拟数据服务替换。关键是很容易告诉 DI 机制根据条件改变其行为。

# 扩展注入器支持到自定义实体

指令和组件需要依赖项进行内省、解析和注入。其他实体，如服务类，通常也需要这样的功能。在我们的示例中，我们的`Playlist`类可能依赖于与第三方通信的 HTTP 客户端的依赖项，以获取歌曲。注入这种依赖的操作应该像在类构造函数中声明带注释的依赖项一样简单，并且有一个注入器准备好通过检查类提供程序或任何其他提供程序来获取对象实例。

只有当我们认真思考后者时，我们才意识到这个想法存在一个漏洞：自定义类和服务不属于组件树。因此，它们不会从任何内置的注入器或父注入器中受益。我们甚至无法声明提供者属性，因为我们没有用`@Component`或`@Directive`装饰器修饰这些类型的类。让我们看一个例子：

```ts
class Playlist {
 songs: Song[];
 constructor(songsService: SongsService) {
 this.songs = songsService.fetch();
 }
}
```

我们可能会尝试这样做，希望当实例化这个类以将其注入到`MusicPlayerComponent`中时，Angular 的 DI 机制会内省`Playlist`类构造函数的`songsService`参数。不幸的是，我们最终得到的只是这样的异常：

```ts
It cannot resolve all parameters for Playlist (?). Make sure they all have valid type or annotations.
```

这有点误导，因为`Playlist`中的所有构造函数参数都已经被正确注释了，对吧？正如我们之前所说，Angular DI 机制通过内省构造函数参数的类型来解析依赖关系。为了做到这一点，需要预先创建一些元数据。每个被装饰器修饰的 Angular 实体类都具有这些元数据，这是 TypeScript 编译装饰器配置细节的副产品。然而，还需要其他依赖项的依赖项没有装饰器，因此也没有为它们创建元数据。这可以通过`@Injectable()`装饰器轻松解决，它将为这些服务类提供 DI 机制的可见性。

```ts
import { Injectable } from '@angular/core';

@Injectable()
class Playlist {
 songs: string[];

 constructor(private songsService: SongsService) {
 this.songs = this.songsService.fetch();
 }
}
```

你会习惯在你的服务类中引入装饰器，因为它们经常依赖于与组件树无关的其他依赖项，以便提供功能。

实际上，无论构造函数是否具有依赖关系，都将所有服务类装饰为`@Injectable()`是一个很好的做法。这样，我们可以避免因为忽略这一要求而导致的错误和异常，一旦服务类增长，并且在将来需要更多的依赖关系。

# 使用`bootstrapModule()`初始化应用程序

正如我们在本章中所看到的，依赖查找一直冒泡直到顶部的第一个组件。这并不完全正确，因为 DI 机制还会检查`bootstrapModule()`函数的额外步骤。

据我们所知，我们使用 `bootstrapModule()` 函数来通过在其第一个参数中声明根模块来启动我们的应用程序，然后指出根组件，从而启动应用程序的组件树。

在文件 `main.ts` 中，典型的引导看起来像下面这样：

```ts
import { enableProdMode } from  '@angular/core'; import { platformBrowserDynamic } from  '@angular/platform-browser-dynamic'; import { AppModule } from  './app/app.module'; import { environment } from  './environments/environment'; if (environment.production) {
  enableProdMode(); }

platformBrowserDynamic().bootstrapModule(AppModule);
```

从上述代码中可以得出的结论是，Angular 已经改变了引导的方式。通过添加 `@NgModule`，我们现在引导一个根模块而不是一个根组件。然而，根模块仍然需要指向一个应用程序启动的入口点。让我们来看看根模块是如何做到这一点的：

```ts
import { NgModule } from '@angular/core';
import { AppComponent } from './app.component';

@NgModule({
 bootstrap: [AppComponent]
 // the rest omitted for brevity
})
```

注意 `bootstrap` 键的存在，我们如何指出根组件 `AppComponent`。还要注意 `bootstrap` 属性是一个数组。这意味着我们可以有多个根组件。每个根组件都将具有自己的注入器和服务单例集，彼此之间没有任何关系。接下来，让我们谈谈我们可以在其中进行修改的不同模式。

# 在开发和生产模式之间切换

Angular 应用程序默认在开发模式下引导和初始化。在开发模式下，Angular 运行时会向浏览器控制台抛出警告消息和断言。虽然这对于调试我们的应用程序非常有用，但当应用程序处于生产状态时，我们不希望显示这些消息。好消息是，可以禁用开发模式，转而使用更为安静的生产模式。这个操作通常是在引导我们的应用程序之前执行的：

```ts
import { environment } from './environments/environment';
// other imports omitted for brevity
if(environment.production) {
 enableProdMode();
}

//bootstrap
platformBrowserDynamic().bootstrapModule(AppModule);
```

我们可以看到，调用 `enableProdMode()` 是启用生产模式的方法。

# Angular CLI 中的不同模式

值得注意的是，将不同的环境配置保存在不同的文件中是一个好主意，如下所示：

```ts
import { environment } from './environments/environment';
```

environments 目录包括两个不同的文件：

+   `environment.ts`

+   `environment.prod.ts`

第一个文件看起来像这样：

```ts
export const environment = {
 production: false
}
```

第二个文件看起来像这样：

```ts
export const environment = {
 production: true
}
```

根据我们调用 `ng build` 命令的方式，将使用其中的一个文件：

```ts
ng build --env=prod // uses environment.prod.ts
ng build // by default uses environment.ts 
```

要找出哪些文件映射到哪个环境，您应该查看 `angular-cli.json` 文件：

```ts
// config omitted for brevity
"environments" : {
 "dev": "environments/environment.ts",
 "prod": "environments/environment.prod.ts"
}
```

# 介绍应用程序目录结构

在前几章和本章的各个部分中，我们已经看到了布局 Angular 应用程序的不同方法和良好实践。这些准则涵盖了从命名约定到如何组织文件和文件夹的指针。从现在开始，我们将通过重构所有不同的接口、组件、指令、管道和服务，将所有这些知识付诸实践，使其符合最常见的社区约定。

到本章结束时，我们将拥有一个最终的应用程序布局，将我们迄今所见的一切都包含在以下站点架构中：

```ts
app/
 assets/ // global CSS or image files are stored here
 core/
 (application wide services end up here)
 core.module.ts
 shared/
 shared.module.ts // Angular module for shared context
 timer/
 ( timer-related components and directives )
 timer.module.ts // Angular module for timer context
 tasks/
 ( task-related components and directive )
 task.module.ts // Angular module for task context
 app
 app.component.ts
 app.module.ts // Angular module for app context
 main.ts // here we bootstrap the application
 index.html
 package.json
 tsconfig.json
 typings.json

```

很容易理解项目的整体原理。现在，我们将组合一个应用程序，其中包含两个主要上下文：计时器功能和任务列表功能。每个功能可以包含不同范围的组件、管道、指令或服务。每个功能的内部实现对其他功能或上下文是不透明的。每个功能上下文都公开了一个 Angular 模块，该模块导出了每个上下文提供给上层上下文或应用程序的功能部分（即组件，一个或多个）。所有其他功能部分（内部指令和组件）对应用程序的其余部分是隐藏的。

可以说很难划清界限，区分哪些属于特定上下文，哪些属于另一个上下文。有时，我们构建功能部分，比如某些指令或管道，可以在整个应用程序中重用。因此，将它们锁定到特定上下文并没有太多意义。对于这些情况，我们确实有共享上下文，其中存储着任何旨在在应用程序级别可重用的代码单元，而不是与组件无关的媒体文件，如样式表或位图图像。

主`app.component.ts`文件包含并导出应用程序根组件，该组件声明并在其自己的注入器中注册其子组件所需的依赖项。正如您已经知道的，所有 Angular 应用程序必须至少有一个根模块和一个根组件，由`bootstrapModule()`函数初始化。这个操作实际上是在`main.ts`文件中执行的，该文件由`index.html`文件触发。

在这样的上下文中定义一个组件或一组相关组件可以提高可重用性和封装性。唯一与应用程序紧密耦合的组件是顶级根组件，其功能通常非常有限，基本上是在其模板视图中呈现其他子组件或作为路由器组件，正如我们将在后续章节中看到的那样。

最后一部分是包含 TypeScript 编译器、类型和`npm`配置的 JSON 文件。由于 Angular 框架的版本不断发展，我们不会在这里查看这些文件的实际内容。你应该知道它们的目的，但一些具体内容，比如对等依赖版本，经常会发生变化，所以最好参考本书的 GitHub 仓库获取每个文件的最新版本。不过，`package.json`文件需要特别提及。有一些常见的行业惯例和流行的种子项目，比如 Angular 官方网站提供的项目。我们提供了几个`npm`命令来简化整个安装过程和开发工作。

# 按照 Angular 的方式重构我们的应用程序

在本节中，我们将把我们在前几章中创建的代码分割成代码单元，遵循单一职责原则。因此，除了将每个模块分配到其自己的专用文件中之外，不要期望代码有太多变化。这就是为什么我们将更多地关注如何分割事物，而不是解释每个模块的目的，你应该已经知道了。无论如何，如果需要，我们将花一分钟讨论变化。

让我们从在你的工作文件夹中创建与前一节中看到的相同的目录结构开始。我们将在路上为每个文件夹填充文件。

# 共享上下文或将所有内容存储在一个公共模块中

共享上下文是我们存储任何构造的地方，其功能旨在一次被多个上下文使用，因为它对这些上下文也是不可知的。一个很好的例子是我们一直在用来装饰我们组件的番茄钟位图，它应该存储在`app/shared/assets/img`路径下（顺便说一句，请确实将它保存在那里）。

另一个很好的例子是对模型数据建模的接口，特别是当它们的模式可以在不同功能上下文中重复使用时。例如，当我们在第四章中定义了`QueuedOnlyPipe`时，我们只对记录集中项目的排队属性进行了操作。然后，我们可以认真考虑实现一个`Queued`接口，以便以后在具有该属性的模块中提供类型检查。这将使我们的管道更具重用性和模型无关性。代码如下：

```ts
//app/shared/queueable.model.ts

export interface Queueable {
 queued: boolean;
}
```

请注意这个工作流程：首先，我们定义与这个代码单元对应的模块，然后导出它，并将其标记为默认，这样我们就可以从其他地方按名称导入它。接口需要以这种方式导出，但在本书的其余部分，我们通常会在同一语句中声明并导出模块。

有了这个接口，我们现在可以安全地重构`QueuedOnlyPipe`，使其完全不依赖于`Task`接口，以便在任何需要过滤记录集的上下文中完全重用，无论它们代表什么。代码如下：

```ts
// app/shared/queued.only.pipe.ts
import { Pipe, PipeTransform } from '@angular/core';
import { Queueable } from '../interfaces/queuable';

@Pipe({ name : 'queuedOnly' })
export class QueuedOnlyPipe implements PipeTransform {
 transform(queueableItems: Queueable[], ...args) :Queueable[] {
 return queuableItems.filter( 
 queueableItem:Queueable => queueableItem.queued === args[0]
 )
 }
}
```

正如您所看到的，每个代码单元都包含一个单一的模块。这个代码单元符合 Angular 文件名的命名约定，清楚地以驼峰命名法陈述了模块名称，再加上类型后缀（在这种情况下是`.pipe`）。实现也没有改变，除了我们用`Queuable`类型注释了所有可排队的项目，而不是之前的任务注释。现在，我们的管道可以在任何实现`Queueable`接口的模型存在的地方重复使用。

然而，有一件事情需要引起您的注意：我们不是从源位置导入`Queuable`接口，而是从一个名为`shared.ts`的文件中导入，该文件位于上一级目录。这是共享上下文的门面文件，我们将从该文件公开所有公共共享模块，不仅供消费共享上下文模块的客户端使用，还供共享上下文内部的模块使用。这是一个情况：如果共享上下文内的任何模块更改其位置，我们需要更新门面，以便任何其他引用该模块的元素在同一上下文中保持不受影响，因为它通过门面来消费它。现在是一个很好的时机来介绍我们的共享模块，以前它将是一个门面文件：

```ts
//app/shared/shared.module.ts

import { QueuedOnlyPipe } from './pipes/queued-only.pipe';

@NgModule({
 declarations: [QueuedOnlyPipe],
 exports: [QueuedOnlyPipe]
})
export class SharedModule {}
```

与门面文件的主要区别在于，我们可以通过向`SharedModule`添加方法和注入服务等方式向其添加各种业务逻辑。

到目前为止，我们只通过`SharedModule`的 exports 属性公开了管道、指令和组件，但是其他东西如类和接口呢？嗯，我们可以在需要时直接要求它们，就像这样：

```ts
import { Queueable } from '../shared/queueable';

export class ProductionService {
 queueable: Queueable;
}
```

现在我们有一个可工作的`Queuable`接口和一个`SharedModule`，我们可以创建其他接口，这些接口将在整本书中使用，对应于`Task`实体，以及我们需要的其他管道：

```ts
//app/task/task.model.ts

import { Queueable } from './queueable';

export interface Task extends Queueable {
 name: string;
 deadline: Date;
 pomodorosRequired: number;
}
```

我们通过使用 extends（而不是 implements）在 TypeScript 中将一个接口实现到另一个接口上。现在，对于`FormattedTimePipe`：

```ts
//app/shared/formatted.time.pipe.ts

import { Pipe, PipeTransform } from '@angular/core';

@Pipe({ name : 'formattedTime' })
export class FormattedTimePipe {
 transform(totalMinutes: number) {
 let minutes: number = totalMinutes % 60;
 let hours: number = Math.floor( totalMinutes / 60 );
 return `${hours}h:${minutes}m`;
 }
}
```

最后，我们需要更新我们的`SharedModule`，以包含这个`Pipe`：

```ts
//app/shared/shared.module.ts

import { QueuedOnlyPipe } from './pipes/queued-only.pipe';
import { FormattedTimePipe } from './pipes/formatted-time.pipe';

@NgModule({
 declarations: [QueuedOnlyPipe, FormattedTimePipe],
 exports: [QueuedOnlyPipe, FormattedTimePipe]
})
export class SharedModule {}
```

总结一下我们在这里做的事情，我们创建了两个接口，`Task`和`Queueable`。我们还创建了两个管道，`QueuedOnlyPipe`和`FormattedTimePipe`。我们将后者添加到我们的`@NgModule`的 declarations 关键字中，至于接口，我们将使用`import`关键字根据需要将它们引入应用程序。不再需要通过门面文件公开它们。

# 共享上下文中的服务

让我们谈谈在共享上下文中拥有服务的影响，以及`@NgModule`的添加带来了什么。我们需要关心两种类型的服务：

+   一个瞬态服务；这个服务创建自己的新副本，可能包含内部状态，对于每个创建的副本，它都有自己的状态

+   一个单例，只能有一个此服务，如果它有状态，我们需要确保在整个应用程序中只有一个此服务的副本

在 Angular 中使用依赖注入，将服务放在模块的提供者中将确保它们最终出现在根注入器上，因此如果我们有这种情况，它们将只创建一个副本：

```ts
// app/task/task.module.ts

@NgModule({
 declarations: [TaskComponent],
 providers: [TaskService]
})
export class TaskModule {} 
```

早些时候，我们在`TaskModule`中声明了一个`TaskService`。让我们来定义另一个模块：

```ts
@NgModule({
 declarations: [ProductsComponent]
 providers: [ProductsService] 
})
export class ProductsModule {}
```

只要我们在根模块中导入这两个模块，就像这样：

```ts
//app/app.module.ts

@NgModule({
 imports: [TaskModule, ProductsModule]
})
export class AppModule {}
```

我们现在已经创建了一个情况，`ProductsService`和`TaskService`可以被注入到`ProductsComponent`或`TaskComponent`的构造函数中，这要归功于`ProductsModule`和`TaskModule`都被导入到`AppModule`中。到目前为止，我们还没有问题。然而，如果我们开始使用延迟加载，我们就会遇到问题。在延迟加载中，用户导航到某个路由，我们的模块与其构造一起被加载到包中。如果延迟加载的模块或其构造之一实际上注入了，比如`ProductsService`，那么它将不是`TaskModule`或`ProductsModule`正在使用的相同`ProductsService`实例，这可能会成为一个问题，特别是如果状态是共享的。解决这个问题的方法是创建一个核心模块，一个被`AppModule`导入的模块；这将确保服务永远不会因错误而被再次实例化。因此，如果`ProductsService`在多个模块中使用，特别是在延迟加载的模块中使用，建议将其移动到核心模块。因此，我们从这样做：

```ts
@NgModule({
 providers: [ProductsService],
})
export class ProductsModule {}
```

将我们的`ProductService`移动到核心模块：

```ts
@NgModule({
 providers: [ProductsService]
})
export class CoreModule {}
```

当然，我们需要将新创建的`CoreModule`添加到我们的根模块中，就像这样：

```ts
@NgModule({
 providers: [],
 imports: [CoreModule, ProductsModule, TasksModule]
})
export class AppModule {}
```

有人可能会认为，如果我们的应用程序足够小，早期创建一个核心模块可能被视为有点过度。反对这一观点的是，Angular 框架采用移动优先的方法，作为开发人员，你应该延迟加载大部分模块，除非有充分的理由不这样做。这意味着当你处理可能被共享的服务时，你应该将它们移动到一个核心模块中。

在上一章中，我们构建了一个数据服务来为我们的数据表填充任务数据集。正如我们将在本书后面看到的那样，数据服务将被应用程序的其他上下文所使用。因此，我们将其分配到共享上下文中，并通过我们的共享模块进行暴露：

```ts
//app/task/task.service.ts

import { Injectable } from '@angular/core';
import { Task } from '../interfaces/task';

@Injectable()
export class TaskService {
 taskStore: Task[] = [];
 constructor() {
 const tasks = [
 {
 name : 'task 1',
 deadline : 'Jun 20 2017 ',
 pomodorosRequired : 2
 },
 {
 name : 'task 2',
 deadline : 'Jun 22 2017',
 pomodorosRequired : 3
 }
 ];

 this.taskStore = tasks.map( task => {
 return {
 name : task.name,
 deadline : new Date(task.deadline),
 queued : false,
 pomodorosRequired : task.pomodorosRequired
 }
 });
 }
}
```

请注意我们如何导入`Injectable()`装饰器并在我们的服务上实现它。它在构造函数中不需要任何依赖项，因此依赖于此服务的其他模块在声明构造函数时不会有任何问题。原因很简单：在我们的服务中默认应用`@Injectable()`装饰器实际上是一个很好的做法，以确保它们在开始依赖其他提供者时仍然能够无缝注入，以防我们忘记对它们进行装饰。

# 从中央服务配置应用程序设置

在之前的章节中，我们在我们的组件中硬编码了很多东西：标签、持续时间、复数映射等等。有时，我们的上下文意味着具有高度的特定性，并且在那里拥有这些信息是可以接受的。但是，有时我们可能需要更灵活和更方便的方式来全局更新这些设置。

对于这个例子，我们将使所有`l18n`管道映射和设置都可以从共享上下文中的一个中央服务中获得，并像往常一样从`shared.ts`门面暴露出来。

以下代码描述了一个将保存应用程序所有配置的`SettingsService`：

```ts
// app/core/settings.service.ts
import { Injectable } from '@angular/core';

@Injectable()
export class SettingsService {
 timerMinutes: number;
 labelsMap: any;
 pluralsMap: any;

 contructor() {
 this.timerMinutes = 25;
 this.labelsMap = {
 timer : {
 start : 'Start Timer',
 pause : 'Pause Timer',
 resume : 'Resume Countdown',
 other : 'Unknown'
 }
 };

 this.pluralsMap = {
 tasks : {
 '=0' : 'No pomodoros',
 '=1' : 'One pomodoro',
 'other' : '# pomodoros'
 }
 }
 }
}
```

请注意我们如何将与上下文无关的映射属性暴露出来，这些属性实际上是有命名空间的，以更好地按上下文分组不同的映射。

将此服务分成两个特定的服务并将它们放置在各自的上下文文件夹中，至少就`l18n`映射而言，这是完全可以的。请记住，诸如时间持续等数据将在不同的上下文中使用，正如我们将在本章后面看到的那样。

# 在我们的共享模块中将所有内容整合在一起

通过所有最新的更改，我们的`shared.module.ts`应该是这样的：

```ts
// app/shared/shared.module.ts

import { NgModule } from '@angular/core';
import { FormattedTimePipe } from './pipes/formatted-time-pipe';
import { QueuedOnlyPipe } from './pipes/queued-only-pipe';

import { SettingsService } from './services/settings.service';
import { TaskService } from './services/task.service';

@NgModule({
 declarations: [FormattedTimePipe, QueuedOnlyPipe],
  providers: [SettingsService, TaskService],
  exports: [FormattedTimePipe, QueuedOnlyPipe]
})
export class SharedModule {}
```

我们的`SharedModule`从前面暴露了`FormattedTimePipe`和`QueuedOnlyPipe`，但是有一些新的添加；即，我们添加了`provider`关键字的内容。我们添加了我们的服务，`SettingsService`和`TaskService`。

现在，当这个模块被另一个模块消耗时，会发生一件有趣的事情；所以，让我们在下面的代码中看看这样的情景：

```ts
// app/app.module.ts

import { NgModule } from '@angular/core';
import { SharedModule } from './shared/shared.module';

@NgModule({
  imports: [SharedModule]
 // the rest is omitted for brevity
})
export class AppModule {}
```

从前面部分部分知道了导入另一个模块的影响。我们知道`SharedModule`中包含的所有内容现在都可以在`AppModule`中使用，但还有更多。`SharedModule`中`provider`关键字中提到的任何内容都可以被注入。所以，假设我们有以下`app.component.ts`文件：

```ts
// app/app.component.ts

import { AppComponent } from './app.component';

@Component({
 selector: 'app',
 template: 'app'
})
export class AppComponent {
 constructor(
    private settingsService:SettingsService, 
 private taskService: TaskService
 ) {}
}
```

正如你所看到的，现在我们可以自由地注入来自其他模块的服务，只要它们是：

+   在其模块的`provider`关键字中提到

+   它们所在的模块被另一个模块导入

总之，到目前为止，我们已经学会了如何将组件和服务添加到共享模块中，还学会了我们需要在声明和`export`关键字中注册组件，对于服务，我们需要将它们放在`provider`关键字中。最后，我们需要`import`它们所在的模块，你的共享构件就可以在应用程序中使用了。

# 创建我们的组件

有了我们共享的上下文，现在是时候满足我们的另外两个上下文了：定时器和任务。它们的名称足够描述它们的功能范围。每个上下文文件夹将分配组件、HTML 视图模板、CSS 和指令文件，以提供它们的功能，还有一个外观文件，导出此功能的公共组件。

# 生命周期钩子简介

生命周期钩子是你在指令或组件的生命周期中监视阶段的能力。这些钩子本身是完全可选的，但如果你了解如何使用它们，它们可能会有很大的帮助。有些钩子被认为是最佳实践，而其他钩子则有助于调试和理解应用程序中发生的情况。一个钩子带有一个定义你需要实现的方法的接口。Angular 框架确保调用钩子，只要你将接口添加到组件或指令中，并通过实现接口指定的方法来履行合同。因为我们刚刚开始学习如何构建你的应用程序，现在可能还没有理由使用某些钩子。所以，我们将有理由在后面的章节中返回这个主题。

你可以使用的钩子如下：

+   `OnInit`

+   `OnDestroy`

+   `OnChanges`

+   `DoCheck`

+   `AfterContentInit`

+   `AfterContentChecked`

+   `AfterViewInit`

+   `AfterViewChecked`

在本节中，我们将涵盖本章中的前三个钩子，因为其余的涉及到更复杂的主题。我们将在本书的后续章节中重新讨论剩下的五个钩子。

# OnInit - 一切开始的地方

使用这个钩子就像添加`OnInit`接口并实现`ngOnInit()`方法一样简单：

```ts
export class ExampleComponent implements OnInit {
 ngOnInit() {}
}
```

不过，让我们谈谈为什么存在这个钩子。构造函数应该相对空，并且除了设置初始变量之外不应包含逻辑。在构造对象时不应该有任何意外，因为有时您构造的是用于业务使用的对象，有时它是在单元测试场景中创建的。

以下是在类的构造函数中执行的适当操作的示例。在这里，我们展示了对类成员变量的赋值：

```ts
export class Component {
 field: string;
 constructor(field: string) {
 this.field = field;
 }
}
```

以下示例显示了不应该做的事情。在代码中，我们在构造函数中订阅了一个 Observable。在某些情况下，这是可以接受的，但通常更好的做法是将这种代码放在`ngOnInit()`方法中：

```ts
export class Component {
 data:Entity;
 constructor(private http:Http) {
 this.http.get('url')
 .map(mapEntity)
 .subscribe( x => this.data = x);
 }
}
```

最好建立订阅，如之前使用`OnInit`接口提供的`ngOnInit()`方法所示。

当然，这是一个建议，而不是一项法律。如果您没有使用这个钩子，那么显然您需要使用构造函数或类似的方法来执行前面的 HTTP 调用。除了仅仅说构造函数应该为空以美观和处理测试时，还有另一个方面，即输入值的绑定。输入变量不会立即设置，因此依赖于构造函数中的输入值会导致运行时错误。让我们举例说明上述情景：

```ts
@Component({
 selector: 'father',
 template: '<child [prop]='title'></child>'
})
export class FatherComponent {
 title: string = 'value';
}

@Component({
 selector: 'child',
 template: 'child'
})
export class ExampleComponent implements OnInit {
 @Input prop;

 constructor(private http:Http) {
    // prop NOT set, accessing it might lead to an error
 console.log('prop constructor',prop) 
 }

 ngOnInit() {
    console.log('prop on init', prop) // prop is set and is safe to use
 }
}
```

在这个阶段，您可以确保所有绑定已经正确设置，并且可以安全地使用 prop 的值。如果您熟悉 jQuery，那么`ngOnInit`的作用很像`$(document).ready()`的构造，总的来说，当组件设置完成时发生的仪式在这一点上已经发生。

# OnDestroy - 当组件从 DOM 树中移除时调用

这种典型用例是在组件即将离开 DOM 树时进行一些自定义清理。它由`OnDestroy`接口和`ngOnDestroy()`方法组成。

为了演示其用法，让我们看一下下面的代码片段，我们在其中实现了`OnDestroy`接口：

```ts
@Component({
 selector: 'todos',
 template: `
 <div *ngFor="let todo of todos">
 <todo [item]="todo" (remove)="remove($event)">
 </div>
 `
})
export class TodosComponent {
 todos;

 constructor() {
 this.todos = [{
 id : 1,
 name : 'clean'
 }, {
 id : 2,
 name : 'code' 
 }]
 }

 remove(todo) {
    this.todos = this.todos.filter( t => t.id !== todo.id );
 }
}

@Component({
 selector: 'todo',
 template: `
 <div *ngIf="item">{{item.name}} <button (click)="remove.emit(item)">Remove</button></div>
 `
})
export class TodoComponent implements OnDestroy {
 @Output() remove = new EventEmitter<any>();
 @Input() item;
  ngOnDestroy() { console.log('todo item removed from DOM'); }
}
```

我们之前的片段试图突出显示当`TodoComponent`的一个实例从 DOM 树中移除时。`TodosComponent`渲染了一个`TodoComponents`列表，当调用`remove()`方法时，目标`TodoComponent`被移除，从而触发`TodoComponent`上的`ngOnDestroy()`方法。

好的，很好，所以我们有一种方法来捕获组件被销毁的确切时刻...那又怎样呢？

这是我们清理资源的地方；通过清理，我们的意思是：

+   超时，间隔应该在这里被取消订阅

+   可观察流应该被取消订阅

+   其他清理

基本上，任何导致印记的东西都应该在这里清理。

# OnChanges - 发生了变化

这个钩子的使用方式如下：

```ts
export class ExampleComponent implements OnChanges {
 ngOnChanges(changes:  SimpleChanges) { }
}
```

注意我们的方法如何接受一个名为`changes`的输入参数。这是一个对象，其中所有已更改的属性作为`changes`对象的键。每个键指向一个对象，其中包含先前值和当前值，如下所示：

```ts
{
 'prop' : { currentValue : 11, previousValue : 10 }
 // below is the remaining changed properties
}
```

上述代码假设我们有一个带有`prop`字段的类，如下所示：

```ts
export class ExampleComponent {
 prop: string;
}
```

那么，是什么导致事物发生变化？嗯，这是绑定的变化，也就是说，我们设置了`@Input`属性，如下所示：

```ts
export  class  TodoComponent  implements  OnChanges { @Input() item; ngOnChanges(changes:  SimpleChanges) { for (let  change  in  changes) { console.log(` '${change}' changed from
 '${changes[change].previousValue}' to
 '${changes[change].currentValue}' `
 ) }
 }
}
```

这里值得注意的一点是，我们跟踪的是引用的变化，而不是对象的属性变化。例如，如果我们有以下代码：

```ts
<todo [item]="todoItem">
```

如果`todoItem`上的 name 属性发生了变化，使得`todoItem.name`变为`code`而不是`coding`，这不会导致报告变化。然而，如果整个项目被替换，就像下面的代码一样：

```ts
this.todoItem = { ...this.todoItem, { name : 'coding' });
```

那么这将导致一个变化事件被发出，因为`todoItem`现在指向一个全新的引用。希望这能稍微澄清一点。

# 计时器功能

我们的第一个功能是属于计时器功能的，这也是最简单的功能。它包括一个独特的组件，其中包含我们在前几章中构建的倒计时计时器：

```ts
import { Component } from  '@angular/core'; import { SettingsService } from  "../core/settings.service"; @Component({
  selector:  'timer-widget', template: ` <div  class="text-center"> <h1> {{ minutes }}:{{ seconds  |  number }}</h1> <p>
 <button  (click)="togglePause()"  class="btn btn-danger"> {{ buttonLabelKey  |  i18nSelect: buttonLabelsMap }} </button>
 </p>
 </div>
 `
})
export  class  TimerWidgetComponent  {
 minutes:  number; seconds:  number; isPaused:  boolean; buttonLabelKey:  string; buttonLabelsMap:  any; constructor(private  settingsService:  SettingsService) { this.buttonLabelsMap  =  this.settingsService.labelsMap.timer; }

 ngOnInit() { this.reset(); setInterval(()  =>  this.tick(),  1000); }

 reset() { this.isPaused  =  true; this.minutes  =  this.settingsService.timerMinutes  -  1; this.seconds  =  59; this.buttonLabelKey  =  'start'; }

 private  tick():  void  { if  (!this.isPaused) { this.buttonLabelKey  =  'pause'; if  (--this.seconds  <  0) {
 this.seconds  =  59;
 if  (--this.minutes  <  0) {
 this.reset();
 }
 }
 }
 }

 togglePause():  void  {
 this.isPaused  =  !this.isPaused;
 if  (this.minutes  <  this.settingsService.timerMinutes  ||
 this.seconds  <  59
 ) {
 this.buttonLabelKey  =  this.isPaused  ?  'resume'  :  'pause';
 }
 }
}
```

正如你所看到的，实现方式与我们在第一章中已经看到的*在 Angular 中创建我们的第一个组件*基本相同，唯一的区别是通过`OnInit`接口钩子在 init 生命周期阶段初始化组件。我们利用`l18nSelect`管道更好地处理定时器每个状态所需的不同标签，从`SettingsService`中消耗标签信息，该服务在构造函数中注入。在本章的后面部分，我们将看到在哪里注册该提供程序。分钟数也是从服务中获取的，一旦后者绑定到类字段。

通过我们将其添加到`declarations`关键字以及`exported`关键字，后者用于启用外部访问，该组件通过`TimerModule`文件`timer.module.ts`公开导出：

```ts
import { NgModule } from '@angular/core';

@NgModule({
 // tell other constructs in this module about it
 declarations: [TimerWidgetComponent], 
 // usable outside of this module
 exports: [TimerWidgetComponent] 
})
export class TimerModule() {}
```

我们还需要记住将我们新创建的模块导入到`app.module.ts`中的根模块中：

```ts
import { NgModule } from '@angular/core';
import { TimerModule } from './timer/timer.module';

@NgModule({
  imports: [TimerModule]
 // the rest is omitted for brevity
})
```

在这一点上，我们已经创建了一个很好的结构，然后我们将为定时器功能创建更多构造。

# 任务功能

任务功能包含了一些更多的逻辑，因为它涉及两个组件和一个指令。让我们从创建`TaskTooltipDirective`所需的核心单元开始：

```ts
import { Task } from  './task.model'; import { Input, Directive, HostListener } from  '@angular/core'; @Directive({
  selector:  '[task]' })
export  class  TaskTooltipDirective { private  defaultTooltipText:  string;
 @Input() task:  Task;
 @Input() taskTooltip:  any;

 @HostListener('mouseover')
 onMouseOver() {
 if (!this.defaultTooltipText  &&  this.taskTooltip) {
 this.defaultTooltipText  =  this.taskTooltip.innerText;
 }
 this.taskTooltip.innerText  =  this.defaultTooltipText;
 }
}
```

指令保留了所有原始功能，并只导入了 Angular 核心类型和所需的任务类型。现在让我们来看一下`TaskIconsComponent`：

```ts
import { Component, Input, OnInit } from '@angular/core';
import { Task } from './task.model';

@Component({
 selector: 'task-icons',
 template: `
 <img *ngFor="let icon of icons"
 src="/app/shared/assets/img/pomodoro.png"
 width="{{size}}">`
})
export class TaskIconsComponent implements OnInit {
 @Input() task: Task;
 @Input() size: number;
 icons: Object[] = [];

 ngOnInit() {
 this.icons.length = this.task.noRequired;
 this.icons.fill({ name : this.task.name });
 }
}
```

到目前为止一切顺利。现在，让我们转到`TasksComponent`。这将包括：

+   组件文件`tasks.component.ts`，其中用 TypeScript 描述了逻辑

+   CSS 文件`tasks.component.css`，其中定义了样式

+   模板文件`tasks.component.html`，其中定义了标记

从 CSS 文件开始，它将如下所示：

```ts
// app/task/tasks.component.css

h3, p {
 text-align: center;
}

.table {
 margin: auto;
 max-width: 860px;
}
```

继续 HTML 标记：

```ts
// app/task/tasks.component.html

<div  class="container text-center"> <h3>
 One point = 25 min, {{ queued | i18nPlural: queueHeaderMapping }} 
 for today
 <span  class="small" *ngIf="queued > 0">
 (Estimated time : {{ queued * timerMinutes | formattedTime }})
 </span>
 </h3>
 <p>
 <span  *ngFor="let queuedTask of tasks | queuedOnly: true"> <task-icons
 [task]="queuedTask" [taskTooltip]="tooltip"
 size="50">
 </task-icons>
 </span>
 </p>
 <p  #tooltip  [hidden]="queued === 0">
 Mouseover for details
 </p>
 <h4>Tasks backlog</h4>
 <table  class="table">
 <thead>
 <tr>
 <th>Task ID</th>
 <th>Task name</th>
 <th>Deliver by</th>
 <th>Points required</th>
 <th>Actions</th>
 </tr>
 </thead>
 <tbody>
 <tr  *ngFor="let task of tasks; let i = index">
 <th  scope="row">{{ (i+1) }}
 <span  *ngIf="task.queued"  class="label label-info">
 Queued</span>
 </th>
 <td>{{ task.name | slice:0:35 }}
 <span  [hidden]="task.name.length < 35">...</span>
 </td>
 <td>{{ task.deadline | date: 'fullDate' }}
 <span  *ngIf="task.deadline < today"  class="label label-danger">
 Due</span>
 </td>
 <td  class="text-center">{{ task.noRequired }}</td>
 <td>
 <button  type="button"  class="btn btn-default btn-xs"  [ngSwitch]="task.queued"  (click)="toggleTask(task)">
 <ng-template  [ngSwitchCase]="false">
 <i  class="glyphicon glyphicon-plus-sign"></i>
 Add
 </ng-template>
 <ng-template  [ngSwitchCase]="true">
 <i  class="glyphicon glyphicon-minus-sign"></i>
 Remove
 </ng-template>
 <ng-template  ngSwitchDefault>
 <i  class="glyphicon glyphicon-plus-sign"></i>
 Add
 </ng-template>
 </button>
 </td>
 </tr>
 </tbody>
 </table>
</div>
```

请花一点时间查看应用于外部组件文件的命名约定，文件名与组件自身匹配，以便在上下文文件夹内的扁平结构中识别哪个文件属于什么。还请注意我们如何从模板中移除了主位图，并用名为`timerMinutes`的变量替换了硬编码的时间持续。这个变量在绑定表达式中计算完成所有排队任务的时间估计。我们将看到这个变量是如何在以下组件类中填充的：

```ts
// app/task/tasks.component.ts

import { Component, OnInit } from  '@angular/core'; import { TaskService } from  './task.service'; import { Task } from  "./task.model"; import { SettingsService } from  "../core/settings.service"; @Component({
  selector:  'tasks', styleUrls: ['tasks.component.css'], templateUrl:  'tasks.component.html' })
export  class  TasksComponent  implements  OnInit { today:  Date;
 tasks:  Task[];
 queued:  number;
 queueHeaderMapping:  any;
 timerMinutes:  number; constructor( private  taskService:  TaskService,
 private  settingsService:  SettingsService) {
 this.tasks  =  this.taskService.taskStore;
 this.today  =  new  Date();
 this.queueHeaderMapping  =  this.settingsService.pluralsMap.tasks;
 this.timerMinutes  =  this.settingsService.timerMinutes;
 }

 ngOnInit():  void  { this.updateQueued(); }

 toggleTask(task:  Task):  void  { task.queued  =  !task.queued;
 this.updateQueued();
 }

 private  updateQueued():  void  { this.queued  =  this.tasks
 .filter((Task:  Task)  =>  Task.queued)
 .reduce((no:  number,  queuedTask:  Task)  =>  {
 return  no  +  queuedTask.noRequired;
 },  0);
 }
}
```

`TasksComponent`的实现有几个值得强调的方面。首先，我们可以在组件中注入`TaskService`和`SettingsService`，利用 Angular 的 DI 系统。这些依赖项可以直接从构造函数中注入访问器，立即成为私有类成员。然后从绑定的服务中填充任务数据集和时间持续时间。

现在让我们将所有这些构造添加到`TaskModule`中，也就是文件`task.module.ts`，并导出所有指令或组件。然而，值得注意的是，我们这样做是因为我们认为所有这些构造可能需要在应用的其他地方引用。我强烈建议您认真考虑在`exports`关键字中放什么，不要放什么。您的默认立场应该是尽量少地进行导出：

```ts
import { NgModule } from '@angular/core';
@NgModule({
  declarations: [TasksComponent, TaskIconsComponent, TasksTooltipDirective],
  exports: [TasksComponent],
 providers: [TaskService]
 // the rest omitted for brevity
})
```

我们现在已经将构造添加到`declarations`关键字中，以便模块知道它们，还有`exports`关键字，以便导入我们的`TaskModule`的其他模块能够使用它们。下一个任务是设置我们的`AppComponent`，或者也称为根组件。

# 定义顶级根组件

准备好所有功能上下文后，现在是时候定义顶级根组件了，它将作为整个应用程序的启动组件，以树形层次结构的一簇组件展开。根组件通常具有最少的实现。主要子组件最终会演变成子组件的分支。

以下是根组件模板的示例。这是您的应用程序将驻留在其中的主要可视组件。在这里，定义应用程序标题、菜单或用于路由的视口是有意义的。

```ts
//app/app.component.ts

import { Component } from '@angular/core';

@Component({
 selector: 'app',
 template: `
 <nav class="navbar navbar-default navbar-static-top">
 <div class="container">
 <div class="navbar-header">
 <strong class="navbar-brand">My App</strong>
 </div>
 </div>
 </nav>
 <tasks></tasks>
 `
})
export class AppComponent {}
```

之前已经提到过，但值得重复。我们在`app.component.ts`文件中使用的任何构造都不属于`AppModule`，都需要被导入。从技术上讲，被导入的是这些构造所属的模块。您还需要确保这些构造通过在所述模块的`exports`关键字中提到而得到适当的暴露。通过前面的根组件，我们可以看到在`app.component.ts`的模板中使用了两个不同的组件，即`<timer-widget>`和`<pomodoro-tasks>`。这两个组件属于不同的模块，第一个组件属于`TimerModule`，第二个组件属于`TaskModule`。这意味着`AppModule`需要导入这两个模块才能编译。因此，`app.module.ts`应该如下所示：

```ts
import { NgModule } from '@angular/core';
import { TimerModule } from './timer/timer.module';
import { TasksModule } from './tasks/tasks.module';

@NgModule({
 imports: [ TimerModule, TasksModule ]
 // omitted for brevity
})
export class AppModule {}
```

# 总结

本章确实为您从现在开始将在 Angular 上构建的所有优秀应用奠定了基础。实际上，Angular 依赖管理的实现是这个框架的一大亮点，也是一个节省时间的工具。基于组件树的应用架构不再是什么高深的技术，我们在构建其他框架（如 AngularJS 和 React）中的 Web 软件时在某种程度上也遵循了这种模式。

本章结束了我们对 Angular 核心及其应用架构的探索，建立了我们在这个新的令人兴奋的框架上构建应用时将遵循的标准。

在接下来的章节中，我们将专注于非常具体的工具和模块，这些工具和模块可以帮助我们解决日常问题，从而打造我们的 Web 项目。我们将看到如何使用 Angular 开发更好的 HTTP 网络客户端。
