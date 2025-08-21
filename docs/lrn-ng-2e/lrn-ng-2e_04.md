# 第四章：在我们的组件中实现属性和事件

到目前为止，我们有机会俯瞰新的 Angular 生态系统中组件的概述，它们的角色是什么，它们的行为如何，以及开始构建我们自己的组件来表示小部件和功能块所需的工具是什么。此外，TypeScript 证明是这项努力的完美伴侣，因此我们似乎拥有了进一步探索 Angular 为创建公开属性和发出事件所带来的可能性的一切所需的一切。

在本章中，我们将：

+   发现我们可以使用的所有语法可能性来绑定内容

我们的模板

+   为我们的组件创建公共 API，以便我们可以从它们的属性和事件处理程序中受益

+   看看如何在 Angular 中实现数据绑定

+   通过视图封装来减少 CSS 管理的复杂性

# 更好的模板语法

在第一章 *在 Angular 中创建我们的第一个组件*中，我们看到了如何在我们的组件中嵌入 HTML 模板，但我们甚至没有触及 Angular 模板开发的表面。正如我们将在本书中看到的，模板实现与 Shadow DOM 设计原则紧密耦合，并且它为我们在视图中以声明方式绑定属性和事件带来了大量的语法糖，以简化任务。

简而言之，Angular 组件可以公开一个公共 API，允许它们与其他组件或容器进行通信。这个 API 可能包括输入属性，我们用它来向组件提供数据。它还可以公开输出属性，我们可以将事件监听器绑定到它，从而及时获取有关组件状态变化的信息。

让我们看看 Angular 是如何通过快速简单的示例来解决将数据注入和注出我们的组件的问题的。请关注这些属性背后的哲学。我们将有机会在稍后看到它们的实际应用。

# 使用输入属性进行数据绑定

让我们重新审视定时器组件的功能，这是我们在第一章中已经看到的

*在 Angular 中创建我们的第一个组件*，让我们假设我们希望我们的组件具有可配置的属性，以便我们可以增加或减少倒计时时间：

```ts
<timer [seconds]="25"></timer>
```

请注意大括号之间的属性。这告诉 Angular 这是一个输入属性。模拟`timer`组件的类将包含一个`seconds`属性的 setter 函数，该函数将根据该值的变化来更新自己的倒计时持续时间。我们可以注入一个数据变量或一个实际的硬编码值，如果这样的值是文本字符串，则必须在双引号内用单引号括起来。

有时我们会看到这种语法，用于将数据注入到组件的自定义属性中，而在其他时候，我们将使用这种括号语法使原生 HTML 属性对组件字段具有响应性，就像这样：

```ts
<h1 [hidden]="hideMe">
 This text will not be visible if 'hideMe' is true
</h1>
```

# 在绑定表达式时的一些额外语法糖

Angular 团队已经为我们的组件指令和 DOM 元素提供了一些快捷方式，用于执行常见的转换，比如调整属性和类名或应用样式。在这里，我们有一些在属性中声明性地定义绑定时的时间节省示例：

```ts
<div [attr.hidden]="isHidden">...</div>
<input [class.is-valid]="isValid">
<div [style.width.px]="myWidth"></div>
```

在第一种情况下，如果`isHidden`表达式评估为`true`，`div`将启用隐藏属性。除了布尔值之外，我们还可以绑定任何其他数据类型，比如字符串值。在第二种情况下，如果`isValid`表达式评估为`true`，`is-valid`类名将被注入到 class 属性中。在我们的第三个例子中，`div`将具有一个样式属性，显示出一个以像素为单位设置的`width`属性的值，该值由`myWidth`表达式设置。您可以在 Angular 速查表（[`angular.io/guide/cheatsheet`](https://angular.io/cheatsheet)）中找到更多这种语法糖的例子，该速查表可在官方 Angular 网站上找到。

# 使用输出属性进行事件绑定

假设我们希望我们的计时器组件在倒计时结束时通知我们，以便我们可以执行组件范围之外的其他操作。我们可以通过输出属性实现这样的功能：

```ts
<timer (countdownComplete)="onCountdownCompleted()"></timer>
```

注意大括号之间的属性。这告诉 Angular，这样的属性实际上是一个输出属性，将触发我们绑定到它的事件处理程序。在这种情况下，我们将希望在包装此组件的容器对象上创建一个`onCountownCompleted`事件处理程序。

顺便说一句，驼峰命名不是巧合。这是 Angular 中应用于所有输出和输入属性名称的命名约定。

我们将找到与我们已知的交互事件映射的输出属性，例如`click`，`mouseover`，`mouseout`，`focus`等等：

```ts
<button (click)="doSomething()">click me</button>
```

# 输入和输出属性的作用

掌握前面章节中详细介绍的概念的最佳方法是实践。在第一章中，我们学习了如何使用 Webpack 或 Angular-CLI 从头开始构建应用程序。由于 Angular-CLI 被认为是设置项目的标准方式，让我们只使用它，并通过输入以下内容来创建一个新项目：

```ts
ng new InputOutputDemo
```

此时，我们有一个完全可用的项目，可以通过输入`ng serve`轻松启动。

让我们快速回顾一下 Angular 项目的结构，这样我们就知道如何处理我们即将创建的所有新构造。以下文件特别值得关注：

+   `main.ts`：这个文件引导我们的应用程序。

+   `app/app.module.ts`：这个文件声明了我们的根模块，任何新的构造都必须添加到这个模块的 declarations 属性中，或者您需要为这些未来的构造添加一个专门的模块。通常建议为我们的新构造拥有一个专门的模块。

在前面的项目列表中，我们提到了*根模块*的概念。我们提到这个概念是为了提醒自己关于 Angular 模块的一般情况。Angular 模块包含一堆彼此相关的构造。您可以通过使用`@NgModule`装饰器来识别 Angular 模块；模块本身只是一个普通的类。`@NgModule`装饰器以对象字面量作为输入，并且在这个对象字面量中注册属于模块的一切。

如前面的项目列表中所述，为我们的新构造添加一个专门的模块被认为是一个良好的做法，所以让我们这样做：

```ts
@NgModule({
 declarations: []
})
export class InputModule {}
```

此时，我们将`declarations`属性数组留空。一旦声明了我们的组件，我们将把它添加到该数组中。

这个模块目前还不属于应用程序，但它需要在根模块中注册。打开`app.module.ts`文件，并将新创建的模块添加到`import`数组中，就像这样：

```ts
@NgModule({
 declarations: [AppComponent],
 imports: [ BrowserModule,
      InputModule
 ],
 providers: [], bootstrap: [AppComponent] })
export  class  AppModule { }
```

让我们剥离我们在第一章中看到的定时器示例，*在 Angular 中创建我们的第一个组件*，并讨论一个更简单的例子。让我们看一下`TimerComponent`文件，并用以下组件类替换其内容：

```ts
import { Component } from '@angular/core';

@Component({
 selector : 'countdown-timer',
 template : '<h1>Time left: {{seconds}}</h1>'
})
export class CountdownTimerComponent {
 seconds: number = 25;
 intervalId: any;

 constructor() {
 this.intervalId = setInterval(() => this.tick(), 1000);
 }

 private tick(): void {
 if(--this.seconds < 1) {
 clearInterval(this.intervalId);
 }
 }
} 
```

太棒了！我们刚刚定义了一个简单但非常有效的倒计时组件，它将从 25 秒倒数到 0（你看到上面的`seconds`字段了吗？TypeScript 支持在声明时初始化成员）。一个简单的`setInterval()`循环执行一个名为`tick()`的自定义私有函数，它减少秒数的值直到达到零，此时我们只需清除间隔。

然而，现在我们只需要在某个地方嵌入这个组件，所以让我们创建另一个组件，除了作为前一个组件的 HTML 包装主机之外，没有其他功能。在同一个文件中，在`CountdownTimerComponent`类之后创建这个新组件：

```ts
@Component({
 selector: 'timer',
 template: '<countdown-timer></countdown-timer>'
})
export class TimerComponent {}
```

按照之前的承诺，我们还将把我们新创建的组件添加到它所属的模块的`declarations`数组中，就像这样：

```ts
@NgModule({
 declarations: [CountdownTimerComponent, TimerComponent]
})
export class InputModule {}
```

首先这样做的原因是确保这些组件可以相互使用，就像`CountdownTimerComponent`在`TimerComponent`的模板中使用的情况一样。

在 Angular 中，组件基本上是带有视图模板的指令。我们还可以找到没有视图的指令，它们基本上为宿主元素添加新功能，或者它们只是作为不带 UI 的自定义元素包装其他元素。或者，它们通过 API 为其他组件提供更多功能。

我们将在下一章和整本书中详细探讨指令。你一定想知道为什么我们创建了这个没有实现的主机或父`TimerComponent`组件。很快，我们将为它增加一些更多的功能，但现在让我们将其用作初始化组件树的概念验证。

# 声明式设置自定义值

你可能会同意，设置自定义倒计时器的功能会很好，对吧？输入属性证明是实现这一点的一个很好的方式。为了利用这个功能，我们将不得不调整文件顶部的`import`语句。

```ts
import { Component, Input } from '@angular/core';

@Component({
 selector: 'countdown-timer',
 template: '<h1>Time left: {{ seconds }}</h1>'
})
export class CountdownTimerComponent {
  @Input() seconds : number;
 intervalId;
 // rest of the implementation remains the same
}
```

你可能已经注意到，我们不再初始化`seconds`字段了，现在它被一个属性装饰器修饰（就像我们在第三章中看到的那样，*介绍 TypeScript*）。我们刚刚开始定义我们组件的 API。

属性命名区分大小写。Angular 强制执行的约定是对组件输入和输出属性都应用驼峰命名法，正如我们很快将看到的那样。

接下来，我们只需要在容器组件的模板中添加所需的属性：

```ts
@Component({
 selector: 'timer',
 template: `
 <div class="container text-center">
 <countdown-timer [seconds]="25"></countdown-timer>
 </div>`
})
```

请注意，我们根本没有更新`TimerComponent`。我们只更新了它的`CountdownComponent`子组件。然而，它全新的 API 可以在任何最终将其包含在自己模板中作为子组件的组件中使用，因此我们可以从模板中声明性地设置其属性，或者甚至可以从`TimerComponent`控制器类中的属性中以命令方式绑定值。

当使用`@Input()`标记类属性时，我们可以配置在 HTML 中实例化组件时希望该属性具有的名称。为此，我们只需要在装饰器签名中引入我们选择的名称，就像这样：`@Input('name_of_the_property')`。无论如何，这种做法是不鼓励的，因为在组件 API 中公开与其控制器类中定义的属性名称不同的属性名称只会导致混淆。

# 通过自定义事件在组件之间进行通信

现在我们的子组件正在被其父组件配置，如何

我们可以实现从子组件到父组件的通信吗？这就是自定义事件发挥作用的地方！为了创建适当的事件绑定，我们只需要在组件中配置一个输出属性，并将事件处理程序函数附加到它上面。

为了触发自定义事件，我们需要引入`EventEmitter`，以及`@Output`装饰器，其功能与我们学到的关于`@Input`装饰器完全相反：

```ts
import { Component, Input, Output, EventEmitter } from '@angular/core';
```

`EventEmitter`是 Angular 的内置事件总线。简而言之，`EventEmitter`类支持发出`Observable`数据并订阅`Observer`消费者对数据更改。它的简单接口基本上包括两种方法，`emit()`和`subscribe()`，因此可以用于触发自定义事件以及同步和异步地监听事件。我们将在第七章中更详细地讨论 Observables，*使用 Angular 进行异步数据服务*。目前，我们可以通过`EventEmitter`API 来生成事件，组件中托管我们发出事件的组件可以观察并附加事件处理程序。这些事件通过使用`@Input()`装饰器注释的任何属性在组件范围之外获得可见性。

以下代码显示了一个实际的实现，从前面的例子中跟进：

```ts
@Component({
 selector : 'countdown-timer',
 template : '<h1>Time left: {{ seconds }}</h1>'
})
export class CountdownTimerComponent {
 @Input() seconds : number;
 intervalId: any;
  @Output() complete: EventEmitter<any> = new EventEmitter();
 constructor() {
 this.intervalId = setInterval( () => this.tick(), 1000 );
 }

 private tick(): void {
 if(--this.seconds < 1) {
 clearTimeout(this.intervalId);
 // an event is emitted upon finishing the countdown
      this.complete.emit(null);
 }
 }
}
```

一个名为`complete`的新属性被方便地注释为`EventEmitter`类型，并立即初始化。稍后，我们将访问它的`emit`方法，以便在倒计时结束时生成一个自定义事件。`emit()`方法需要一个任意类型的必需参数，因此我们可以向事件订阅者发送数据值（如果不需要，则为 null）。

现在，我们只需要设置我们的宿主组件，以便它将监听此完成事件或输出属性，并订阅一个事件处理程序：

```ts
@Component({
 selector : 'timer',
 template : `
 <div class="container text-center">
 <img src="assets/img/timer.png" />
 <countdown-timer [seconds]="25"
                 (complete)="onCountdownCompleted()">
 </countdown-timer>`
})
export class TimerComponent {
 onCountdownCompleted(): void {
 alert('Time up !')
 }
}
```

为什么是`complete`而不是`onComplete`？Angular 支持另一种语法形式，称为规范形式，用于输入和输出属性。在输入属性的情况下，一个属性表示为`[seconds]`可以表示为`bind-seconds`，无需使用括号。关于输出属性，这些可以表示为`on-complete`而不是`(complete)`。这就是为什么我们从不在输出属性名称前加上`on`前缀，因为这将出现在输出属性上，比如`on-complete`，如果我们最终决定在我们的项目中更喜欢规范语法形式。

我们已经学会了如何使用组件的输入数据。数据将驻留在容器中，组件将在容器模板内呈现。这意味着组件可以通过我们输入的方式突然访问容器的数据：

```ts
<component [property]="propertyOnContainer">
```

在组件方面，代码如下所示：

```ts
@Component({
 selector : 'component'
})
export class Component {
  @Input() property;
}
```

我们还学习了输出，也就是如何从组件向容器进行通信。为了实现这一点，我们在组件上添加了另一个属性，如下所示：

```ts
<component (event)="methodOnContainer()" [property]="propertyOnContainer">
```

在组件方面，我们将使用一个名为`Output`的装饰器，如下所示：

```ts
@Component({
 selector : 'component'
})
export class Component {
  @Output() event = new EventEmitter<any>();
}
```

并积极调用绑定的方法，我们会输入：

```ts
event.emit();
```

接下来要学习的是如何从组件传递数据到容器。

# 通过自定义事件发出数据

既然我们知道如何从组件 API 发出自定义事件，为什么不再进一步，将数据信号发送到组件范围之外呢？我们已经讨论过`EventEmitter<T>`类的`emit()`事件在其签名中接受由`T`注释表示的任何给定数据。让我们扩展我们的示例以通知倒计时的进度。为什么我们要这样做呢？基本上，我们的组件在屏幕上显示一个可视倒计时，但我们可能希望以编程方式观察倒计时的进度，以便在倒计时结束或达到某一点时采取行动。

让我们用另一个输出属性更新我们的计时器组件，与之匹配

原始的并在每次迭代`seconds`属性时发出自定义事件，

如下所示：

```ts
class CountdownTimerComponent {
 @Input() seconds: number;
  @Output() complete: EventEmitter<any> = new EventEmitter();
 @Output() progress: EventEmitter<number> = new EventEmitter();
 intervalId;

 constructor() {
 this.intervalId = setInterval(() => this.tick(), 1000);
 }

 private tick(): void {
 if(--this.seconds < 1) {
 clearTimeout(this.intervalId);
      this.complete.emit(null);
 }
    this.progress.emit(this.seconds);
 }
}
```

现在，让我们重建主机组件的模板，以反映倒计时的实际进度。我们已经通过显示倒计时来做到这一点，但这是由`CountdownTimerComponent`在内部处理的功能。现在，我们将在该组件之外跟踪倒计时：

```ts
@Component({
 selector: 'timer',
 template: `
 <div class="container text-center">
 <countdown-timer [seconds]="25"
                 (progress)="timeout = $event"
                 (complete)="onCountdownCompleted()" >
 </countdown-timer>
 <p *ngIf="timeout < 10">
 Beware! Only
 <strong>{{ timeout }} seconds</strong>
 </p>
 </div>` 
})
export class TimerComponent {
 timeout: number;
 onCountdownCompleted(): void {
 alert('Time up')
 }
}
```

我们利用这一轮更改来将超时值正式化为主机组件的属性。这使我们能够在我们的自定义事件处理程序中将新值绑定到该属性，就像我们在前面的示例中所做的那样。我们不是将事件处理程序方法绑定到(`progress`)处理程序，而是引用`$event`保留变量。它是指向`progress output`属性的有效负载的指针，反映了我们在执行`this.progress.emit(this.seconds)`时传递给`emit()`函数的值。简而言之，`$event`是`CountdownTimerComponent`内`this.seconds`所假定的值。通过将这样的值分配给模板中的`timeout`类属性，我们还更新了模板中插入的段落中表达的绑定。当`timeout`小于`10`时，此段落将变为可见。

```ts
<countdown-timer [seconds]="25"
           (progress)="timeout = $event"
           (complete)="onCountdownCompleted()">
</countdown-timer>
```

在本节中，我们看到了如何从组件发送数据到容器。基本上有两种方法：

+   将`$event`分配给容器属性

+   使用`$event`作为函数参数调用容器方法

第一个版本就是我们所演示的：

```ts
<countdown [seconds]="25" (progress)="timeout = $event" >
</countdown>
```

组件调用它如下：

```ts
progress.emit(data);
```

第二个版本是对前面示例的小改写：

```ts
<countdown [seconds]="25" (progress)="onProgress($event)">
</countdown>
```

我们会以与组件相同的方式调用它，但不同之处在于我们需要声明一个容器方法`onProgress`，这样`timeout`属性就会以这种方式设置：

```ts
onProgress(data) {
 this.timeout = data;
}
```

# 模板中的本地引用

我们之前已经看到了如何使用双大括号语法通过数据插值将数据绑定到我们的模板。除此之外，我们经常会在属于我们组件或甚至常规 HTML 控件的元素中看到以井号（`#`）为前缀的命名标识符。这些引用标识符，即本地名称，用于在我们的模板视图中引用标记为它们的组件，然后以编程方式访问它们。它们也可以被组件用来引用虚拟 DOM 中的其他元素并访问其属性。

在前一节中，我们看到了如何通过`progress`事件订阅倒计时的进度。但是，如果我们能深入检查组件，或者至少是它的公共属性和方法，并在不必监听`progress`事件的情况下读取`seconds`属性在每个滴答间隔中的值，那该多好啊？好吧，给组件本身设置一个本地引用将打开其公共外观的大门。

让我们在`TimerComponent`模板中标记我们的`CountdownTimerComponent`实例，使用一个名为`#counter`的本地引用。从那一刻起，我们将能够直接访问组件的公共属性，比如`seconds`，甚至在模板的其他位置绑定它。这样，我们甚至不需要依赖`progress`事件发射器或`timeout`类字段，甚至可以操纵这些属性的值。这在下面的代码中显示：

```ts
@Component({
 selector: 'timer',
 template: `
 <div class="container text-center">
 <countdown-timer [seconds]="25"
 (complete)="onCountdownCompleted()"
                 #counter >
 </countdown-timer>
 <p>
 <button class="btn btn-default"
 (click)="counter.seconds = 25">
 reset
 </button>
 </p>
 <p *ngIf="counter.seconds < 10">
 Beware, only !
 <strong>{{ counter.seconds }} seconds</strong>
 </p>
 </div>`
})
export class TimerComponent {
 // timeout: any /* No longer required */
 onCountdownCompleted(): void {
 alert('Time up'); 
 }
}
```

# 输入和输出属性的替代语法

除了`@Input()`和`@Output()`装饰器之外，还有一种替代语法，我们可以通过`@Component`装饰器来定义组件的`input`和`output`属性。它的元数据实现通过`inputs`和`outputs`属性名称分别提供对这两个功能的支持。

因此，`CountdownTimerComponent`的 API 可以这样实现：

```ts
@Component({
 selector : 'countdown-timer',
 template : '<h1>Time left: {{seconds}}</h1>',
  inputs : ['seconds'],
  outputs : ['complete','progress']
})
export class CountdownTimerComponent {
  seconds: number;
 intervalId;
  complete: EventEmitter<any> = new EventEmitter();
 progress: EventEmitter<any> = new EventEmitter();
 // And so on..
}
```

总的来说，这种语法是不鼓励的，仅出于参考目的而包含在这里。首先，我们通过在两个地方定义 API 端点的名称来重复代码，增加了重构代码时出错的风险。另外，通常惯例是尽量保持装饰器的实现尽可能简洁，以提高可读性。

我强烈建议您坚持使用`@Input`和`@Output`装饰器。

# 从组件类配置我们的模板

组件元数据还支持一些设置，有助于简化模板管理和配置。另一方面，Angular 利用了 Web 组件的 CSS 封装功能。

# 内部和外部模板

随着应用程序的规模和复杂性的增长，我们的模板也可能会增长，承载其他组件和更大的 HTML 代码块。将所有这些代码嵌入到我们的组件类定义中将变得繁琐和不愉快，而且也很容易出错。为了防止这种情况发生，我们可以利用`templateUrl`属性，指向一个包含我们组件 HTML 标记的独立 HTML 文件。

回到我们之前的例子，我们可以重构`TimerComponent`类的`@Component`装饰器，指向一个包含我们模板的外部 HTML 文件。在我们的`timer.component.ts`文件所在的工作区中创建一个名为`timer.component.html`的新文件，并用我们在`TimerComponent`类中配置的相同 HTML 填充它：

```ts
<div class="container text-center">
 <countdown [seconds]="25"
 (complete)="onCountdownCompleted()"
 #counter >
 </countdown>
 <p>
 <button class="btn btn-default"
 (click)="counter.seconds = 25">
 Reset countdown to 25 seconds
 </button>
 </p>
 <p *ngIf="counter.seconds < 10">
 Beware only !
 <strong>{{ seconds }} seconds</strong> left
 </p>
</div>
```

现在，我们可以修改`@Component`装饰器，指向该文件，而不是在装饰器元数据中定义 HTML：

```ts
@Component({
 selector: 'timer',
 templateUrl: './timer.component.html'
})
export class TimerComponent {
 // Class follows below
}
```

外部模板遵循 Angular 中的某种约定，由最流行的 Angular 编码风格指南强制执行，即与它们所属的组件共享相同的文件名，包括我们可能附加到组件文件名的任何前缀或后缀。在第六章中探索组件命名约定时，我们将看到这一点，*使用 Angular 组件构建应用程序*。这样，更容易识别，甚至可以使用 IDE 的内置模糊查找工具搜索，哪个 HTML 文件实际上是特定组件的模板。

在哪种情况下创建独立模板而不是将模板标记保留在组件内？这取决于模板的复杂性和大小。在这种情况下，常识将是您最好的顾问。

# 封装 CSS 样式

为了更好地封装我们的代码并使其更具重用性，我们可以在组件内定义 CSS 样式。这些内部样式表是使我们的组件更具共享性和可维护性的好方法。有三种不同的方法来定义我们组件的 CSS 样式。

# styles 属性

我们可以通过组件装饰器中的`styles`属性为我们的 HTML 元素和类名定义样式，如下所示：

```ts
@Component({
 selector : 'my-component',
 styles : [`
 p {
 text-align: center;
 }
 table {
 margin: auto;
 }
 `]
})
export class ExampleComponent {}
```

此属性将接受一个字符串数组，每个字符串包含 CSS 规则，并在我们启动应用程序时将这些规则嵌入到文档的头部以应用于模板标记。我们可以将样式规则内联为一行，也可以利用 ES2015 模板字符串来缩进代码并使其更可读，就像前面的示例中所示。

# styleUrls 属性

就像`styles`一样，`styleUrls`也会接受一个字符串数组，尽管每个字符串都代表一个外部样式表的链接。这个属性也可以与`styles`属性一起使用，根据需要定义不同的规则集：

```ts
@Component({
 selector: 'my-component',
 styleUrls: ['path/to/my-stylesheet.css'], // use this
 styles : [
 `
 p { text-align : center; }
 table { margin: auto; }
 `
 ]  // and this at the same time
})
export class MyComponent {}
```

# 内联样式表

我们还可以将样式规则附加到模板本身，无论是内联模板还是通过`templateUrl`参数提供的模板：

```ts
@Component({
 selector: 'app',
 template: `
 <style> p { color : red; } </style>
 <p>I am a red paragraph </p>
 `
})
export class AppComponent {}
```

# 管理视图封装

所有前面的部分（`styles`，`styleUrls`和内联样式表）都将受到 CSS 特异性的通常规则的约束（[`developer.mozilla.org/en/docs/Web/CSS/Specificity`](https://developer.mozilla.org/en/docs/Web/CSS/Specificity)）。在支持 Shadow DOM 的浏览器上，由于作用域样式，CSS 管理和特异性变得轻而易举。CSS 样式适用于组件中包含的元素，但不会超出其边界。

此外，Angular 将嵌入这些样式表到文档的头部，因此它们可能会影响我们应用程序的其他元素。为了防止这种情况发生，我们可以设置不同级别的视图封装。

简而言之，封装是 Angular 需要在组件内管理 CSS 作用域的方式，适用于支持阴影 DOM 的浏览器和不支持它的浏览器。为此，我们利用`ViewEncapsulation enum`，它可以采用以下任何值：

+   模拟：这是默认选项，基本上是通过在特定选择器下沙盒化 CSS 规则来模拟阴影 DOM 中的本地作用域。推荐使用此选项，以确保我们的组件样式不会受到站点上其他现有库的影响。

+   本地：使用渲染器的本地阴影 DOM 封装机制，仅适用于支持阴影 DOM 的浏览器。

+   无：不提供模板或样式封装。样式将按原样注入到文档的头部。

让我们看一个实际的例子。首先，将`ViewEncapsulation enum`导入脚本，然后创建一个模拟值的封装属性。然后，让我们为倒计时文本创建一个样式规则，以便任何`<h1> (!)`标签都呈现为深红色：

```ts
import {
 Component,
 EventEmitter, 
 Input,
 Output, 
 ViewEncapsulation
} from '@angular/core';
@Component({
 selector: 'countdown-timer',
 template: '<h1>Time left: {{seconds}}</h1>',
 styles: ['h1 { color: #900}'],
 encapsulation: ViewEncapsulation.Emulated 
})
export class CountdownTimerCoponent { 
 // Etc
}
```

现在，点击浏览器的开发工具检查器，并检查生成的 HTML，以发现 Angular 如何将 CSS 注入到页面的`<head>`块中。刚刚注入的样式表已经被沙盒化，以确保我们在组件设置中以非常不具体的方式定义的全局 CSS 规则仅适用于由`CountdownTimerComponent`组件专门作用域的匹配元素。

我们建议您尝试不同的值，并查看 CSS 代码如何注入到文档中。您将立即注意到每种变化提供的隔离等级不同。

# 总结

本章引导我们了解了 Angular 中为组件创建强大 API 的选项，这样我们就可以在组件之间提供高水平的互操作性，通过分配静态值或管理绑定来配置其属性。我们还看到了一个组件如何可以作为另一个子组件的宿主组件，实例化前者的自定义元素在其自己的模板中，为我们的应用程序中更大的组件树奠定了基础。输出参数为我们提供了所需的交互层，通过将我们的组件转换为事件发射器，使它们可以以一种不可知的方式与任何可能最终托管它们的父组件进行通信。模板引用为我们的自定义元素创建了引用的途径，我们可以以声明性的方式从模板内部使用它们的属性和方法。我们还讨论了如何将组件的 HTML 模板隔离在外部文件中，以便于将来的维护，以及如何对我们想要绑定到组件的任何样式表执行相同的操作，以防我们不想将组件样式内联绑定。对 Angular 中处理视图封装的内置功能的概述为我们提供了一些额外的见解，让我们了解了如何可以从每个组件的角度受益于 Shadow DOM 的 CSS 封装，以及在不支持时如何进行 polyfill。

在 Angular 中，我们仍然有很多关于模板管理的东西要学习，主要是关于你在使用 Angular 过程中会广泛使用的两个概念。我指的是指令和管道，在第五章中我们将对其进行详细介绍，《使用管道和指令增强我们的组件》。
