# 7

# 在 Angular 中精通响应式编程

响应式编程有助于提高您应用程序的性能，并允许 Angular 更好地利用变更检测机制，减少应用程序需要重新渲染的次数。

在本章中，您将了解响应式编程。您将学习响应式编程是什么以及如何使用它来改进您的 Angular 应用程序。您还将了解 RxJS 库以及它如何反应性地管理异步数据流。本章将教会您如何使用不同的 RxJS 操作符，创建可重用的 RxJS 操作符，重用一组 RxJS 操作符，以及使用 RxJS 将其他 Observables 映射到视图模型中。

您还将了解 Angular Signals 以及如何使用 Signals 来反应性地管理同步数据流。最后，您将学习如何结合 RxJS 和 Signals，何时使用 RxJS，以及何时 Signals 占据主导地位。

到本章结束时，您将能够反应性地管理数据流，并了解有关 Angular Signals 的一切。

本章将涵盖以下主题：

+   什么是响应式编程？

+   使用 RxJS 进行响应式编程

+   使用 Angular Signals 进行响应式编程

+   结合 Signals 和 RxJS

# 什么是响应式编程？

**响应式编程**是一种像函数式、模块化、过程式或**面向对象编程**（**OOP**）一样的声明式编程范式。**编程范式**是一组规则和原则，它指定了您编写代码的方式。它与建筑和设计模式类似，但它们在抽象的不同层面上运作。

编程范式是指导编写代码的整体风格、结构和方法的宏观概念，而架构和设计模式则提供了可重用的模板或蓝图，用于结构化代码、处理组件之间的通信、管理关系以及解决代码库中其他常见的设计挑战。

响应式编程处理数据流和变化的传播。简单来说，响应式编程决定了您如何处理可能在任何给定时间发生的事件和数据变化，也称为**异步变化**。正如其名称所暗示的，您使用响应式编程来对变化做出反应。使用响应式编程，代码的依赖部分将自动通知事件和数据变化，以便这些部分可以自动对变化做出反应。您可以将响应式编程视为一个系统，其中变化被推送到需要根据变化做出反应的代码部分，而不是您拉取当前状态，检查是否已更改，然后相应地更新依赖代码。

## 响应式编程的亮点

反应式系统通过仅在数据发生变化时订阅和处理数据来优化资源分配，减少了不必要的处理，提高了整体系统性能。这种方法允许系统更加响应和可扩展，同时节省计算资源，使其在高效处理异步数据流方面特别有利。你可以使用反应式编程来处理数据流和事件的常见示例包括 HTTP 请求、表单更改以及浏览器事件，如点击、鼠标移动和键盘事件。

反应式编程通过允许开发者轻松地将更简单的行为组合成更复杂的组合，从而促进了可组合性。通过`map`、`filter`、`merge`等运算符，反应式系统允许你转换、组合和操作数据流。

这种固有的模块化使开发者能够构建更模块化和灵活的应用程序，其中不同的数据流可以无缝集成、转换和适应，以创建复杂且易于维护的系统。这种对可组合性的强调促进了代码的可重用性，并促进了高度可扩展和适应性强应用的创建。

另一个反应式编程的重要部分是处理非阻塞的事件和数据变化；这主要是在你开始进行反应式编程时性能提升的地方。**非阻塞代码**确保多个任务、事件和数据变化可以并行执行。换句话说，非阻塞代码在任务开始后直接运行代码，而不需要等待任务完成，因此你的代码在任务开始后直接继续到下一行代码。相比之下，**阻塞代码**会在代码完成之前等待，然后才移动到下一行代码。

## 反应式编程的缺点

反应式编程很棒，但它不仅仅是阳光和玫瑰；反应式编程也有一些缺点。

反应式系统可能会变得复杂，学习曲线，尤其是对于初级开发者来说，可能会很陡峭。除了一些难以理解的概念外，反应式编程可能难以调试，并且使用自动化测试进行测试也更困难。特别是当你为你的应用程序编写单元测试时，高度反应式系统可能会给你带来一些头疼。

现在，你已经了解了什么是反应式编程以及它如何提高你的应用程序性能并有效地处理事件和数据流。反应式编程提供了数据流和事件的简单可组合性，并以非阻塞的方式运行你的代码。你还知道反应式编程可能给你的代码库带来哪些挑战，以及对于初级开发者来说，理解一些反应式模式可能具有挑战性。现在，让我们更多地了解反应式编程在 Angular 应用程序中的应用。

# 反应式编程在 Angular 中的应用

响应式编程是 Angular 框架的核心。Angular 强烈依赖于 *观察者*（我们将在下一节中详细解释观察者）并且内置了 **RxJS** 库，以强大和可组合的方式管理观察者数据流。**观察者**是一种响应式设计模式，因为它包含了观察者，即数据发布者，以及订阅者或数据流的接收者。当观察者发出新的值时，订阅者会自动收到通知并相应地采取行动。

观察者（Observables）在 Angular 框架中被广泛应用于多个方面。以下是一些例子，展示了在 Angular 框架中你可以找到观察者的地方：

+   **HTTP 请求**：在 Angular 框架中，HTTP 请求默认返回观察者。在纯 JavaScript 中，HTTP 请求是通过 Promise 处理的。

+   `events` 观察者。使用路由 `events` 观察者，你可以监听诸如 `NavigationStart`、`NavigationEnd`、`GuardCheckStart` 和 `GuardCheckEnd` 等事件。

+   Angular 框架暴露了 `valueChanges` 观察者。使用这个观察者，你可以对表单或表单字段中的变化做出响应。

+   `ViewChildren` 装饰器，它返回的 `QueryList` 对象有一个 `changes` 事件。这个 `changes` 事件是一个观察者，当 `ViewChildren` 选择的项发生变化时，它会通知你。

这些例子只是 Angular 框架依赖观察者的几个实例。在用 Angular 制作的应用程序代码中，以及与 Angular 常常一起使用的库中，你会在许多地方遇到观察者。

除了观察者之外，Angular 还以其他方式使用响应式编程，例如在处理浏览器事件时使用 `@Hostlistener()` 装饰器：

```js
@HostListener('document:keydown', ['$event'])
handleTheKeyboardEvent(event: KeyboardEvent) { …… }
```

在前面的代码片段中，我们使用了 `@Hostlistener()` 装饰器来监听 `keydown` 事件并对其进行响应式处理。Angular 使用响应式编程范式的另一个地方是 **Angular Signals**，这是在 Angular 16 中引入的。Angular Signals 是一个跟踪值变化并相应地通知感兴趣消费者的系统。Signals API 包含了计算属性和效果，这些属性和效果会在 Signal 值变化时自动更新或运行。Signals 适用于处理同步值的响应式，而 RxJS 在处理异步数据流方面表现出色。我们将在本章的 *使用 Angular Signals 进行响应式编程* 部分更深入地探讨 Signals。 

既然你已经了解到 Angular 框架内置了响应式编程，从观察者和 RxJS 到事件处理和新的 Angular Signals，让我们继续下一节，学习如何在 Angular 应用程序中充分利用 RxJS。

# 使用 RxJS 进行响应式编程

在 Angular 应用程序的上下文中，关于响应式编程，RxJS 处于核心地位。**RxJS** 代表 **Reactive Extensions Library for JavaScript**。正如其名所示，它是一个用于处理 JavaScript 中响应性的库，并且它是内置的，默认情况下在 Angular 框架中使用。

RxJS 用于创建、消费、修改和组合异步和基于事件的流数据。在其核心，RxJS 围绕四个主要概念：可观测量、观察者、主题和操作符。让我们逐一深入探讨这些概念，从可观测量开始。

## 什么是可观测量？

**可观测量**是 RxJS 的基石。你可以将可观测量视为一个流或管道，它以异步方式在一段时间内发出不同的值。要接收可观测量数据流发出的值，你需要订阅可观测量。

现在，想象一个可观测量数据流就像一个水管。当你打开水龙头（订阅）时，水（数据）会通过水管（可观测量）流动，你在你的末端接收水滴（值）。水（数据）可能在你打开水龙头（订阅）之前就已经流动了，除非你安装了特殊的系统；你将只从你打开水龙头（订阅）的那一刻起接收水（数据），直到你关闭水龙头（取消订阅）。如果其他水龙头（订阅者）是打开的，水（数据）可能还会流向其他水龙头。简而言之，要接收数据流中的值，你需要订阅，而你之前订阅之前发出的所有值都将丢失，除非你有特殊的逻辑来存储这些值。要停止接收值，你需要取消订阅，并且发出值的流就像一条河流一样。

可观测量有两种类型：*热可观测量和冷可观测量*。**冷可观测量**是单播的，意味着每个订阅者都会从头开始。就像 Netflix 上的电影一样；当有人开始播放电影时，电影将从开头开始。如果有人在另一个账户或电视上开始播放同一部电影，电影也将从开头开始。每个人从开始观看的那一刻起都会获得独特的观看体验。

相反，**热可观测量**是多播的，意味着有一个数据流被广播给每个订阅者。热可观测量可以与现场电视相比较。不同的人可以收听现场节目（数据流），但你已经错过的内容将不会为你重播。即使他们没有从开始观看，观看的人也会同时体验到相同的内容。

现在你已经清楚地理解了可观测量，并且知道了热可观测量和冷可观测量之间的区别，让我们来学习观察者。

### 使用观察者订阅可观测量

**观察者**是订阅可观察对象并接收数据流中可观察对象值的实体。你可以将观察者视为观看现场表演或播放 Netflix 电影的人（或订阅者）。订阅者有两个任务：订阅他们想要接收的流，并取消订阅他们不再想要或需要的流。

其中最关键的部分是成功取消不再需要的订阅。不取消可观察对象可能是使用代码中的可观察对象时最大的风险，也是最常见的错误来源。如果你没有正确清理你的订阅，你会遇到内存泄漏。内存泄漏会导致应用程序出现奇怪的行为，运行速度变慢，并最终在内存耗尽时崩溃你的应用程序。

让我们假设你有一个订阅的杂志，每周都会送到你家。如果你搬到了一个新的地址，你需要从订阅中*取消订阅*，并在新的地址上创建一个新的*订阅*。如果你不取消旧地址的订阅，而只是为新地址开始一个新的订阅，你将开始支付双倍费用，杂志将同时送到两个地址。如果你继续重复这个过程，你最终会耗尽资金，你的生活将崩溃。在你的应用程序中，情况相同，只是你不用金钱支付，而是用内存。

当你在组件内部创建一个订阅时，必须在组件销毁时取消订阅。否则，订阅将继续运行。下次你打开相同的组件时，将启动第二个订阅，因为旧的订阅仍在运行。结果，所有值都将被两个观察者接收。如果你继续重复这个过程，最终会有许多观察者接收相同的值，而你只需要一个观察者来接收这些值。当观察者太多时，应用程序将耗尽处理所有值的内存，应用程序将崩溃。

## 取消可观察对象的订阅

取消可观察对象的订阅可以以许多不同的方式完成。在 Angular 应用程序中，你通常可以在`ngOnDestroy`生命周期钩子内部取消订阅。你可以手动取消订阅，如下所示：

```js
ngOnInit() {
  this.observable$.subscribe(…)
}
ngOnDestroy() {
  this.observable$.unsubscribe();
  this.observable$.complete();
}
```

在前面的代码中，我们在`ngOnInit`生命周期钩子内部订阅了一个可观察对象，并在`ngOnDestroy`生命周期钩子中通过在可观察对象上调用`unsubscribe()`和`complete()`方法手动取消订阅。

当你的组件内部有多个可观察对象时，这种方法可能不是最佳选择。如果你有五个可观察对象，你必须手动取消和完成所有五个可观察对象的订阅。手动完成所有订阅会增加遗漏一个可观察对象的风险，并导致`ngOnDestroy`方法中包含大量重复代码的大方法。

你还需要为所有 Observables 创建一个本地属性，这会进一步污染你的文件，增加样板代码。在这种情况下，你需要将 Observable 订阅保存在一个属性中，以便你可以在`ngOnDestroy`方法中取消订阅。

另一个更好的选择是创建一个`Subscription`对象，并通过在`Subscription`对象上使用`add()`方法将所有订阅添加到这个对象中。在这种情况下，你只需要从`Subscription`对象中取消订阅，它将自动取消订阅并完成添加到`Subscription`对象中的所有订阅。

这里有一个如何使用`Subscription`对象方法的示例：

```js
subscriptions = new Subscription()
ngOnInit() {
  this.subscriptions.add(this.observableA$.subscribe(…));
  this.subscriptions.add(this.observableB$.subscribe(…));
}
ngOnDestroy() {
  this.subscriptions.unsubscribe();
}
```

在前面的代码片段中，我们创建了一个`subscriptions`属性并将其分配给`Subscription`对象。接下来，我们使用`subscriptions`属性，并通过`Subscription`类的`add`方法将所有订阅添加到`subscriptions`属性中。最后，在`ngOnDestroy`方法内部，我们调用`subscriptions`对象的`unsubscribe()`方法，这将取消订阅并完成所有内部订阅。

使用`Subscription`类是一个不错的选择，但将活动订阅添加到`Subscription`类的语法看起来很杂乱。当你开始使用 RxJS 可连接操作符时，有一种方法更符合你代码的其他部分。我们将在本节稍后更详细地讨论可连接操作符，但现在，我想向你展示如何使用它们来自动取消订阅。

### 使用`takeUntil()`操作符取消订阅

首先，我们将看看`takeUntil()`操作符。你可以在 RxJS 管道方法内部添加`takeUntil()`操作符，该操作符用于你的 Observables。`takeUntil()`操作符会在触发器触发时自动取消你的订阅。这个触发器通常是 RxJS 的`Subject`（我们将在本节稍后更详细地讨论`Subject`）。当我们调用这个`Subject`的下一个方法时，`takeUntil()`操作符将被触发并取消订阅：

```js
private destroy$ = new Subject<void>();
ngOnInit() {
  this.observable$.pipe(takeUntil(this.destroy$))
  .subscribe(……)
}
ngOnDestroy() {
  this.destroy$.next();
  this.destroy$.complete();
}
```

在前面的示例中，我们创建了一个名为`destroy$`的`Subject` Observable。然后，我们在我们的 Observable 上使用`pipe()`函数，并在`pipe()`函数内部添加了`takeUntil()`操作符。`takeUntil()`操作符接收`destroy$`属性作为参数，一旦我们调用`destroy$`属性的`next()`方法，它就会被触发。最后，在`ngOnDestroy`生命周期方法内部，我们调用`destroy$`属性的`next()`方法，并通过调用`complete()`方法完成对`destroy$` Observable 本身的完成。

使用`takeUntil()`操作符是许多 Angular 开发者取消订阅的首选解决方案。这是因为它与 RxJS 的`pipe`函数和其他可连接操作符配合得非常好。我想展示的最后一种取消订阅的选项是`takeUntilDestroyed()`操作符。

### 使用 takeUntilDestroyed() 操作符取消订阅

`takeUntilDestroyed()` 操作符是在 Angular 16 版本中添加的。它可以用来在组件销毁时自动取消订阅。当你将你的订阅声明在注入上下文（在构造函数中或声明属性的地方）内时，你只需要添加 `takeUntilDestroyed()` 操作符，它将为你管理一切：

```js
data = observable$.pipe(ngOnInit, you must provide the takeUntilDestroyed() operator with a reference, DestroyRef. Here, DestroyRef is the ngOnDestroy life cycle in the form of an injectable:

```

protected readonly destroy = inject(DestroyRef);

ngOnInit() {

this.observable$.pipe(takeUntilDestroyed(this.destroy)).subscribe(…);

}

```js

 As you can see, we created a property and assigned it to `DestroyRef`. We add this property as a function parameter to the `takeUntilDestroyed()` operator. That is all you need to do, and it will unsubscribe and complete your subscriptions automatically.
You now know why it’s essential to unsubscribe from Observables and how to do so using different approaches. Now, we will move on to the next major concept of RxJS: `Subject`.
Using special Observables – RxJS Subjects
The `Subject` Observables are `Subject` Observables are like `EventEmitter` Observables, which maintain a registry of all listeners.
Every `Subject` is an Observable, meaning you can subscribe to `Subject` to receive the values it emits. Each `Subject` is also an internal Observer object with the `next()`, `error()`, and `complete()` methods. The `next()` method is called to emit the next value in the data stream, `error()` is called automatically when an error occurs, and `complete()` can be called to complete the data stream.
Within RxJS, there are four different `Subjects`. Let’s take a look.
Subject
This is the basic RxJS `Subject` type. The `Subject` class allows you to create a hot Observable data stream. You can emit a new value using the `next()` method; the `Subject` class has no initial value or memory of the values that have already been emitted. Subscribers will only receive values that are emitted after they are subscribed; anything emitted before that point will not be received:

```

const subject = new Subject<number>();

subject.subscribe({next: (v) => console.log(`A: ${v}`)});

subject.next(1);

subject.subscribe({next: (v) => console.log(`B: ${v}`)});

subject.next(2);

// 日志输出：

// A:1, A:2, B:2

```js

 As shown in the preceding example, we use a `Subject` class to emit two values (`1` and `2`). The first value is emitted after the first subscriber (`A`), and the second value is emitted after both subscribers have subscribed. Because of this, subscriber `A` receives both values, while subscriber `B` only receives the second value.
A good use case for the `Subject` class is when multiple Observers must respond to a specific event, such as a selection or a changing toggle. The `Subject` class can be used if components only have to react to the change if the components are active during the event. Now that you know how `Subject` works, let’s examine `BehaviorSubject`.
BehaviorSubject
The `BehaviorSubject` class extends the `Subject` class and has two main differences from the regular `Subject`. The `BehaviorSubject` class receives an initial value and stores the last emitted value. When a subscriber subscribes to `BehaviorSubject`, the subscriber will immediately receive the last emitted value. When no value is emitted, the subscriber will receive the initial value instead.
The `Subject` class is good for emitting values that have to notify subscribers when an event happens, such as when multiple Observers need to react when something is added. The `BehaviorSubject` class, on the other hand, is well suited for values with state, such as `lastAddedItem`, where subscribers receive the last added item. Here, `lastAddedItem` will always emit the last item that has been added. In contrast, an `itemAdded` event using a `Subject` class will only notify subscribed Observers the moment the item is added and not after the fact:

```

const subject = new BehaviorSubject(0); // 初始值 0

subject.subscribe({next: (v) => console.log(`A: ${v}`)});

subject.next(1);

subject.subscribe({next: (v) => console.log(`B: ${v}`)});

subject.next(2);

// 日志输出：

// A:0, A:1, B:1, A:2, B:2

```js

 As you can see, the `BehaviorSubject` class receives an initial value; in our case, the initial value is `0`. When subscriber `A` subscribes to `BehaviorSubject`, the subscriber immediately gets the initial value, `0`, and logs the value. After subscriber `A` has subscribed, we emit a new value: `1`. This new value is received and logged by subscriber `A`.
Next, subscriber `B` subscribes to `BehaviorSubject`. Because `1` is the last emitted value, subscriber `B` gets and logs it. Lastly, we emit a new value, `2`, which is received and logged by subscribers `A` and `B`.
Now that you know how `BehaviorSubject` works and how it differs from the regular `Subject`, let’s learn about `ReplaySubject`.
ReplaySubject
The `ReplaySubject` class is also an extension of the regular `Subject` class and behaves a bit like `BehaviorSubject` with some differences. The `ReplaySubject` class also stores values, just like `BehaviorSubject`, but unlike `BehaviorSubject`, `ReplaySubject` can store more than one value and doesn’t have an initial value.
Instead of an initial value, the `ReplaySubject` class receives a buffer size as a parameter. The buffer size determines how many values the `ReplaySubject` class stores and shares with a new subscriber upon subscription. Besides the buffer size, the `ReplaySubject` class can take a second parameter to determine how long the `ReplaySubject` class will store the emitted values in the buffer of `ReplaySubject`:

```

const subject = new ReplaySubject(100, 500);

subject.subscribe({

next: (v) => console.log(`A: ${v}`),

});

let i = 1;

setInterval(() => subject.next(i++), 200);

setTimeout(() => {

subject.subscribe({

next: (v) => console.log(`B: ${v}`),

});

}, 1000);

// 日志输出

// A:1, A:2, A:3, A:4, A:5, B:3, B:4, B:5, A:6, B:6

// ...

```js

 In the preceding example, we have a `ReplaySubject` class with a buffer of `100` and a time window of `500` milliseconds. Subscriber `A` subscribes before we emit the first value. Next, we create an interval that emits a new number every 200 milliseconds. As a result, subscriber `A` will receive and log a new value every 200 milliseconds.
Lastly, we create a timeout of `1` second and add the second subscriber. Because we have a time window of `500` milliseconds, subscriber `B` will immediately receive all values that are emitted after the first `500` milliseconds – that is, the timeout of `1` second that has passed minus the time window of the `ReplaySubject` class.
As a result, subscriber `A` logs `1` to `5`; after 1 second, subscriber `B` joins and immediately receives values `3`, `4`, and `5`. After subscriber `B` receives the replay values, both subscribers receive all values emitted after that point.
AsyncSubject
The `AsyncSubject` class is also an extension of the regular `Subject` class. The `AsyncSubject` class only emits the last value to all its subscribers and only when the Observable data stream is completed:

```

const subject = new AsyncSubject();

subject.subscribe({

next: (v) => console.log(`A: ${v}`),

});

subject.next(1);

subject.next(2);

subject.subscribe({

next: (v) => console.log(`B: ${v}`),

});

subject.next(3);

subject.complete();

// 日志输出：

// A:3, B3

```js

 In the preceding example, you can see that `AsyncSubject` only emitted the last value that was emitted before we completed the data stream. First, subscriber `A` subscribed to `AsyncSubject`. Next, we emitted two values, and then subscriber `B` subscribed to `AsyncSubject`. Lastly, we emitted the third value and completed the Observable stream. After we complete the stream, the last value is emitted to and logged by subscribers `A` and `B`.
Now, you know about Observables, Observers, and `Subjects`. You know that there are hot and cold Observables and the difference between the two. You also learned that Observers subscribe to Observables and how to unsubscribe from Observable data streams. You discovered that `Subjects` is a special kind of Observable that’s used to multicast values and that they can emit values using the `next()` method. Lastly, you learned about the four different `Subject` types and saw how you can visualize their differences. Next, we will learn about the last major concept in RxJS: operators.
Using and creating RxJS operators
In this section, you will learn about **RxJS operators**. You will learn what operators are, what types of operators there are, and how to use some of the most commonly used operators in the RxJS library. You will also learn how to create your own RxJS operators and combine multiple operators into a single operator.
While Observables are the foundation of the RxJS library, operators are what make the library so useful and powerful for handling Observable data streams. Operators allow you to easily compose and handle complex asynchronous code declaratively.
Types of operators
RxJS operators come in two different types: creational and pipeable operators.
In short, creational operators can be used to create new Observables with a standalone function, whereas pipeable  operators can be used to modify the Observable stream. Let’s explore both in more detail, starting with creational operators.
Creational operators
`of()` operator. The `of()` operator takes in one or more comma-separated values and turns these values into an Observable stream that emits one value after the other:

```

of() 操作符带有三个值：1、2 和 3。我们订阅了使用 of() 操作符创建的 Observable 流，并记录了这些值。这个订阅将导致三个独立的日志输出：值：1、值：2 和值：3。正如你所见，of() 操作符是一个相当简单直接的方式来创建 Observable 流。

另一个常用的创建操作符是 `from()` 操作符。`from()` 操作符创建一个数组、可迭代对象、Promise 或字符串的 Observable 流。如果你使用 `from()` 操作符将字符串转换成 Observable 流，字符串将被逐字符发射。以下是一个使用 `from()` 操作符的例子：

```js
from([1, 2, 3, 4, 5]).subscribe(val => console.log(val));
//output: 1,2,3,4,5
from(new Promise(resolve => resolve('Promise to Observbale!'))).subscribe(val => console.log(val));
//output: Promise to Observbale!
```

在前面的例子中，我们使用了 `from()` 操作符从一个数组和一个 Promise 创建了一个 Observable 流。

另一个有用的创建操作符是 `fromEvent()` 操作符。`fromEvent()` 操作符可以从事件目标（如 *click* 或 *hover*）创建一个 Observable：

```js
fromEvent() operator takes two arguments. The first is the target element – in our case, we took the document. Then, you declared the event you wanted to listen for; in our example, this is a click event.
With that, you’ve learned how to create a new Observable stream from scratch using creational operators. Next, you will learn how to create a new Observable stream by combining multiple existing Observable streams.
Creating an Observable from multiple Observable streams
As your Angular applications grow and the state of these applications becomes more complex, you often find yourself in a situation where you need the result of multiple Observable streams simultaneously. When you need the result from various Observable streams, you may be tempted to create nested subscriptions, but this isn’t a good solution since nested subscriptions can lead to strange behavior and hard-to-debug bugs.
In scenarios where you need the result of multiple Observables, you can use creational RxJS operators that focus on combining various Observables into a new single Observable stream. When using these operators, the combined Observables are referred to as `combineLatest()` operator.
The `combineLatest()` operator is best used when you have multiple long-lived Observables and need the values of all these Observables to construct the object or perform the logic you want. The `combineLatest()` operator will only output its first value when all of its inner Observables output at least one value; after that, `combineLatest()` outputs another value each time one of the inner Observables emits a new value. The `combineLatest()` operator will always use the last emitted value of all its inner Observables:

```

const amountExclVat = of(100);

const vatPercentage = of(20);

combineLatest([amountExclVat, vatPercentage]).subscribe({

next: ([amount, percentage]) => {

console.log('总计:', amount * (percentage / 100 + 1));

}

});

```js

 As you can see, we provided `combineLatest()` with an array containing two Observables: one Observable with the amount excluding VAT and another Observable containing the VAT percentage. We need both Observable values to log the amount, including VAT. Instead of creating a nested subscription, we handled this with `combineLatest()`.
Inside the `combineLatest()` subscription, we also declare an array for the value of the Observable stream. We used `amount` and `percentage` as values, but you can name these properties however you like. Alternatively, you can use a different syntax and provide an object to `combineLatest()` instead of an array:

```

combineLatest({ amount: amountExclVat, percentage: vatPercentage }).subscribe({

next: (data) => { console.log('总计:', data.amount * (data.percentage / 100 + 1)) }

});

```js

 Now, let’s consider another example where we emit different values over time so that you get a better understanding of how `combineLatest()` works and when and what values it will emit:

```

const a = new Subject();

const b = new Subject();

combineLatest([a, b]).subscribe({

next: ([a, b]) => { console.log(‹data›, a, b) }

});

a.next(1);

setTimeout(() => { b.next(2) }, 5000);

setTimeout(() => { a.next(10) }, 10000);

```js

 In the preceding example, it takes 5 seconds before the `combineLatest()` operator emits the first value; this is because, after 5 seconds, both Observable `a` and `b` have emitted a value. Even though Observable A directly emits a value, `combineLatest()` will only emit a value after both A and B have emitted at least one value.
After 5 seconds have passed and both Observables have emitted a value, `combineLatest()` will emit a value, and we log `data: 1, 2`. After both Observables emit a value, `combineLatest()` will emit a new value whenever one of its Observables emits a new value. So, when Observable A emits a new value after another 5 seconds have passed, we log `data: 10, 2` inside the subscription of `combineLatest()`.
If, for example, you first emitted two values with Observable A (`1`, `10`) and then emitted a value with Observable B (`2`), `combineLatest()` will only emit one value, `data: 10, 2`. This is the case because both A and B need to emit a value before `combineLatest()` starts emitting values.
Now that you have a good idea of how `combineLatest()` works and how to use RxJS to create a new Observable based on multiple Observables, let’s explore other operators that create an Observable from multiple Observables:

*   `forkJoin()` operator is best used when you have multiple Observables and are only interested in the final value of each of these Observables. This means that each Observable has to be completed before `forkJoin()` emits a value. A good example of when `forkJoin()` is useful is when you must make multiple HTTP requests and only want to do something when all requests return a result. The `forkJoin()` operator can be compared with `Promise.all()`. It’s important to note that if one or more of the inner Observables has an error (and you don’t catch that error correctly), `forkJoin()` will not emit a value:

    ```

    forkJoin({ posts: this.http.get('…'), post2: this.http.get('…)}).subscribe(console.log);

    concat()操作符用于当你有多个内部 Observables 时，并且这些内部 Observables 的发射和完成顺序是重要的。所以，如果你有两个内部 Observables，concat()将发射第一个内部 Observables 的所有值，直到该 Observables 完成。在第一个 Observables 完成之前，第二个 Observables 已经发射的所有值将不会由 concat()发射。当第一个 Observables 完成时，`concat()`操作符将订阅第二个 Observables 并开始发射第二个 Observables 发射的值。如果你有更多的内部 Observables，这个过程将重复进行，并且`concat()`将在前一个 Observables 完成时订阅下一个 Observables：

    ```js
    const a = new Subject();
    const b = new Subject();
    concat(a, b).subscribe(console.log);
    a.next(1);
    a.next(2);
    b.next(3);
    a.complete();
    b.next(4);
    b.complete();
    merge() operator combines all inner Observables and emits the values as they come in. The merge() operator doesn’t wait for all Observables to emit a value, nor does it care about the order. When one of the Observables emits a value, the merge() operator will process it:

    ```

    const a = new Subject();

    const b = new Subject();

    merge(a, b).subscribe(console.log);

    b.next('B:1');

    a.next('A:1');

    a.next('A:2');

    b.next('B:2');

    // Logs: B:1, A:1, A:2, B:2

    ```js

    ```

    ```js

You now know how to create Observables with creational operators. You know there are creational operators such as `of()` and `from()` to create simple Observables and creational operators such as `combineLatest()` to create a new Observable based on multiple inner Observables.
Next, we will learn about pipeable operators and how they can be used to filter, modify, and transform Observable streams.
Pipeable operators
**Pipeable operators** take in an Observable as input and return a new and modified Observable without modifying the original Observable. When you subscribe or unsubscribe to the piped Observable, you also subscribe or unsubscribe to the original Observable. Pipeable operators can filter, map, transform, flatten, or modify the Observable stream. For example, pipeable operators can be used to unsubscribe upon a trigger automatically, take the first or last emission of an Observable steam, only emit an Observable value if specific conditions are met, or map the output of the Observable stream into a new object.
Using pipeable operators starts with using the `pipe()` function on an Observable. The `pipe()` function acts like a path for your Observable data, guiding it through different tools called operators. It’s like how materials in a factory move through various stations before becoming a finished product. Here, your data can go through these operators, where you can change it, pick out specific parts, or make it fit your needs. It’s a common scenario that developers use four, five, or even more operators inside a single `pipe()` function.
Let’s examine an example and learn about some commonly used pipeable operators:

```

const observable = of(1, 1, 2, 3, 4, 4, 5);

observable.pipe(

distinctUntilChanged(),

filter(value => value < 5),

map(value => value as number * 10)

).subscribe(results => {console.log('results:', results)});

// Logs: 10, 20, 30, 40

```js

 In the preceding code, we created an Observable using the `of()` operator. On the Observable, we use the `pipe()` function with three different pipeable operators declared inside the pipe function: `distinctUntilChanged()`, `filter()`, and `map()`. At the end of the pipe function, we subscribe to the Observable stream. The values of the Observable stream move through the pipe and perform the operators on them one by one before ending up in the subscribe block of our code.
The first `distinctUntilChanged()` operator checks if the Observable value differs from the previous and filters it out if the value is the same as the last emitted value. Next, the `filter()` operator works similarly to the filter function on an array; in our case, we filter out all values that aren’t smaller than `5`. Lastly, we use the `map()` operator; this is also similar to the `map` function on an array and lets you map the value to a new value; in our case, we multiply by a factor of `10`. After applying all our operators, the Observable values that are logged in the subscription are `10`, `20`, `30`, and `40`; all other values of our Observable are filtered out.
As you can see, the pipeable operators are performed one after another, guiding the Observable value through a pipe where changes are applied to the value until it reaches the subscription or is filtered out. The `distinctUntilChanged()`, `filter()`, and `map()` operators are some of the most commonly used operators. In this chapter, you also learned about the `takeUntil()` and `takeUntilDestroyed()` operators, which are also commonly used.
Now, let’s continue by exploring some other powerful and commonly used operators and scenarios when pipeable operators are helpful, starting with flattening operators.
Flattening multiple Observable streams using flattening operators
As we’ve seen earlier in this section, sometimes, you need the value of multiple Observables. In some cases, you need all these values at once; in these scenarios, you can use the creational operators that create a new Observable based on multiple inner Observables.
But in other scenarios, you first need the value of one Observable to pass as an argument to another Observable. These scenarios where you have an outer Observable and an inner Observable, where the inner Observable relies on the value of the outer Observable, are commonly referred to as `concatAll()`, `mergeAll()`, `swtichAll()`, and `exhaustAll()`. To get a better understanding of this concept, let’s look at some examples.
Let’s say you have an Observable yielding an API URL. Next, you want to use this URL to make an HTTP request. In actuality, you’re only interested in the result of the API request and not so much in the result of the Observable yielding the URL. The URL is only needed to make the API request, and the API response is required to render your page or perform some logic. One approach would be to nest the two subscriptions, but as you’ve learned, this isn’t a good approach. The correct solution is to use a flattening operator to flatten the Observable stream into a single stream:

```

ngOnInit() {

this.urlObservable.pipe(

map((url) => this.http.get(url)),

concatAll()

).subscribe((data) => { console.log('data ==>', data) })

}

```js

 As you can see, we have an outer Observable receiving an API URL and using two pipeable operators on this Observable. First, we use the `map()` operator to take the result of the URL Observable and use it to make the API request, which results in our second Observable. Next, we use the `concatAll()` operator to flatten the two Observables into a single Observable, only returning the result of the API request.
Inside the subscription, we log the result, which will be the data that’s returned by the API call. You can simplify this code even more by using the combined operator, `concatMap()`, which combines the `map()` and `concatAll()` operators into a single operator:

```

this.urlObservable.pipe(

concatMap((url) => this.http.get(url)),

).subscribe(……)

```js

 These combined operators exist for all four flattening operators, so you have the following operators:

*   `concatMap()`
*   `mergeMap()`
*   `switchMap()`
*   `exhaustMap()`

Now that you’ve seen how you can use a flattening operator and that there are map operators that combine the map and flattening operators, let’s learn about the difference between them.
The concatMap() operator
The `concatAll()` operator is used when you want the first value of the outer Observable and all its inner concatenated Observables to complete before the second value of the outer Observable and its inner Observables are processed. Let’s consider the following example:

```

const clicks = fromEvent(document, 'click');

clicks.pipe(

concatMap(() => interval(1000).pipe(take(4)));

).subscribe(number => console.log(number));

```js

 In the preceding code, we use the `fromEvent()` creational operator to create an Observable whenever we click the browser document (that would be any place in our app). Next, we use `concatMap()` to map the result of the click Observable into a new Observable using the RxJS `interval()` creational operator. The `interval()` operator will emit sequential numbers starting at zero; in our case, it will emit the following number every `1000` milliseconds.
We also used the `take()` pipeable operator on the interval Observable. This limits the number of emissions we take to `4`, so the interval Observable will emit `0`, `1`, `2`, and `3` as values and be unsubscribed and completed by the `take()` operator afterward.
Because we use the `concatMap()` flattening operator, when we click twice on the screen, both the outer and inner Observables will be triggered two times, but the first click Observable and its inner Observables will be processed first and only when that is completed the second sequence will start. So, our subscription part will log `0, 1, 2, 3, 0, 1,` `2, 3`.
The mergeMap() operator
Now, let’s consider the same scenario with the other flattening operators, starting with the `mergeMap()` operator:

```

const clicks = fromEvent(document, 'click');

clicks.pipe(

mergeMap(() => interval(1000).pipe(take(4))),

).subscribe(x => console.log(x));

```js

 In the preceding example, we only changed `concatMap()` for the `mergeMap()` operator, yet the result will be completely different. The `mergeMap()` operator will not wait for the first inner and outer Observables to complete but will process the values as they come in.
So, if you click on the screen, wait for 2 seconds, and then click on the screen again, the values of the second click and its inner interval Observable will start to come in before the first stream has completed. If you click the first time on the screen, the first log will come in after one second and another one for every second.
Then, when you click again after a second, the first log of the second stream will come in and log another value for every second after that. In this case, the result of all logs would be `0, 1, 2, 0, 3, 1, 2, 3`. As you can see, the result is entirely different from the result we had with `concatMap()`. Now, let’s see what happens when we change `mergeMap()` to `switchMap()`.
The switchMap() operator
The `switchMap()` operator will switch the Observable stream from the first to the second stream when the second stream starts to emit values. The `switchMap()` operator will unsubscribe and complete the first stream so that the first stream will stop emitting values; the next stream will keep emitting until that stream is completed or until another stream comes:

```

const clicks = fromEvent(document, 'click');

clicks.pipe(

switchMap(() => interval(1000).pipe(take(4)));

).subscribe(x => console.log(x));

```js

 So, with the preceding code, if we click on the screen now, wait for 2 seconds, and then click another time, our log will look like `0, 1, 0, 1, 2, 3`. As you can see in the logs, the first stream is completed the moment the second stream starts to emit values. Lastly, we have the `exhaustMap()` operator.
The exhaustMap() operator
The `exhaustMap()` operator will start to emit the values of the first Observable stream as soon as it starts to emit values. The `exhaustMap()` operator will not process any other Observable streams that come in while the first stream is still running. Only when the stream has been completed will new values be processed, so if you click while the first stream is still running, it will never be processed:

```

const clicks = fromEvent(document, 'click');

clicks.pipe(

exhaustMap(() => interval(1000).pipe(take(4))),

]).subscribe(x => console.log(x));

```js

 In the preceding example, where we used the `exhaustMap()` operator and clicked and waited for 2 seconds before we made another click, only the first click will be processed because the first stream takes 4 seconds to complete. So, when we make the second click, the first stream is not completed yet, so `exhaustMap()` doesn’t process the second stream. The log of the preceding code will look like `0, 1,` `3, 4`.
Lastly, it’s important to note that when you use the `take()`, `takeUntil()`, or `takeUntilDestroyed()` operators inside the pipe of the outer Observable and also use flattening operators in the same `pipe()` function, the `take()`, `takeUntil()`, and `takeUntilDestroyed()` operators need to be declared after the flattening operators. The flattening operators will create their own Observables, and if you declare `take()`, `takeUntil()`, or `takeUntilDestroyed()` before the flattening operator, the Observables created by the flattening operators will not be unsubscribed and closed by `take()`, `takeUntil()`, or `takeUntilDestroyed()`.
You now know what higher-order Observables are and how you can handle them using flattening operators. You learned about combined operators that combine the `map()` and flattening operators, and you learned about using `take()`, `takeUntil()`, or `takeUntilDestroyed()` in combination with the flattening operators. Lastly, you learned about the `interval()` and `take()` operators.
Now, let’s start exploring other useful pipeable operators that serve a few more straightforward use cases and scenarios.
Powerful and useful RxJS operators
You have already learned much about RxJS and seen how you can handle some complex Observable scenarios by combining or flattening Observables. You’ve also seen how to unsubscribe Observables or filter values using pipeable operators. We will now walk through some commonly used pipeable operators that are useful in more straightforward scenarios:

*   `debounceTime()`: The `debounceTime()` operator takes a pause and waits for another value to come in within the defined timeframe. A good real-world example of this is a search or filter input field. Instead of bombarding your system with an update for every keystroke, a more efficient solution would be waiting until the user stops typing for a specific interval. This waiting can be done by using `debounceTime()`. You provide `debounceTime()` with a parameter indicating the milliseconds it should wait (`debounceTime(300)`) before processing the value; only when no new value is received within the specified timeframe will the value be passed on to the next operator or the subscription block.
*   `Skip()`: The `skip()` operator can skip a fixed number of emissions. Let’s say you have `ReplaySubject`, and for one of your subscriptions on `ReplaySubject`, you aren’t interested in the replayed emissions, only in the new emission. In this scenario, you can use the `skip()` operator and define the number of emissions you want to skip inside the operator: `skip(5)`.
*   `skipUntil()`: The `skipUntil()` operator works a bit like the `takeUntil()` operator; only it will skip the emissions until the inner Observable of `skipUntil()` receives a value. You could provide `skipUntil()` with a `Subject` class or something like an RxJS timer so that you only take values after a predefined interval has passed: `skipUntil(timer(5000))`.
*   `find()`: The `find()` operator works similarly to the `find` method on an array. It will only emit the first value it finds that matches the condition you provide the `find()` operator with. So, `find((item: any) => item.size === 'large')` will only pass on the first item through the pipe where the size property is equal to `large`.
*   `scan()`: The `scan()` operator is comparable to the `reduce` function on an array. It gives you access to the previous and current value and allows you to emit a new value based on the previous and current value. For example, you can combine the results or take the lowest or highest result of the two: ``scan((prev, curr) => `${prev} ${curr}`, '')``. Here, we combined the previous and current values using the `scan()` operator.

With that, we have covered some of the more commonly used operators and learned how to filter, map, limit, or transform the value stream with pipeable operators. You can find them in the official documentation if you want to learn more about operators and check out a complete list: [`rxjs.dev/guide/operators`](https://rxjs.dev/guide/operators).
Before we move on to the next section and start to learn about Angular Signals, let’s finish this section on RxJS by creating combined and reusable operators.
Creating combined and reusable RxJS operators
Creating a reusable operator or combining multiple operators can easily be done by creating a function that returns an RxJS `pipe()` function. Let’s say you find yourself making a filter pipe that filters odd numbers multiple times. Creating a function that does this would be easier so that you don’t have to repeat the logic numerous times. You can do this by creating a function that returns an RxJS pipe implementing the filter operator with the filter logic predefined in the operator:

```

export const discardOdd = () => pipe(

filter((v: number) => !(v % 2)),

);

```js

 You can now use this pipeable operator like any other operator:

```

of(1, 2, 4, 5, 6, 7).pipe(discardOddDoubleEven()).subscribe(console.log);

```js

 If you want to combine multiple operators, it works the same way: you must create a function that returns an RxJS pipe and declares all the operators you want to use inside the pipe function:

```

const discardOddDoubleEven = () => pipe(

filter((v: number) => !(v % 2)),

map((v: number) => v * 2)

);

```js

 With that, you’ve learned all about operators and how to use pipeable and creational operators. You know how to combine and flatten multiple Observable streams and create reusable and combined operators using the `pipe()` function. You’ve seen how to create Observable streams and handle code reactively and asynchronously with Observables and RxJS.
Since the introduction of Angular Signals, you can also handle reactivity more synchronously, allowing you to do almost everything in your Angular applications in a reactive manner, both with synchronous and asynchronous code.
In the next section, you will learn everything about Angular Signals. You will learn what Signals are and how and when to use them.
Reactive programming using Angular Signals
We briefly discussed **Angular Signals** in *Chapter 2*, but let’s reiterate that and dive a bit deeper so that you can get a good grasp of Angular Signals and how they can help you handle code more reactively.
Angular Signals was introduced in Angular 16, and it’s one of the most significant changes for the framework since it went from AngularJS to Angular. With Signals, the Angular framework now has a reactive primitive in the Angular framework that allows you to declare, compute, mutate, and consume synchronous values reactively. A **reactive primitive** is an immutable value that alerts consumers when the primitive is set with a new value. Because all consumers are notified, the consumers can automatically track and react to changes in this reactive primitive.
Because Signals are reactive primitives, the Angular framework can better detect changes and optimize rendering, resulting in better performance. Signals are the first step to an Angular version with fully fine-grained and local change detection that doesn’t need Zone.js to detect changes based on browser events.
At the time of writing, Angular assumes that any triggered browser event handler can change any data bound to an HTML template. Because of that, each time a browser event is triggered, Angular checks the entire component tree for changes because it can’t detect changes in a fine-grained manner. This is a significant drain on resources and impacts performance negatively.
Because Signals notify interested parties of changes, Angular doesn’t have to check the entire component tree and can perform change detection more efficiently. While we aren’t at a stage yet where Angular can perform fully local change detection and only update components or properties with changes, by using Signals combined with OnPush change detection, you reduce the number of components Angular has to check for changes. Eventually, Signals will allow the framework to perform local change detection, where the framework only has to check and update components and properties that have changed values.
Besides change detection, Signals bring more advantages. Signal allows for a more reactive approach within your Angular code. While RxJS already does a fantastic job facilitating reactive programming within your Angular applications, RxJS focuses on handling asynchronous Observable data streams and isn’t suited to handle synchronous code.
On the other hand, Signals shine where RxJS falls short; Signals reactively handle synchronous code by automatically notifying all consumers when the synchronous value changes. All dependent code can then react and update accordingly when the Signal pushes a new value. Especially when you start to utilize Signal effects and computed Signals, you can take your reactivity to the next level! Signal effects and computed Signals will automatically compute new values or run side effects when the Signal value changes, making it easy to automatically update and run logic as a reaction to the changed value of synchronous code.
Another problem that Signals solves is the infamous and dreaded `ExpressionChanged` **AfterItHasBeenCheckedError** error. If you’ve worked with Angular, changes are pretty significant you’ve seen this error before. This error occurs because of how Angular currently detects changes. Because the change detection on Signals is different, as Angular knows when they change and doesn’t have to check for changes, the dreaded `ExpressionChangedAfterItHasBeenCheckedError` error will not occur when working with Signal values.
Signals wrap around values such as strings, numbers, arrays, and objects. The Signal then exposes the value through a getter, which allows the Angular framework to track who is consuming the Signal and notify the consumers when the value changes. Signals can wrap around simple values or complex data structures such as objects or arrays with nested structures. Signals can be read-only and writable. As you might expect, writeable Signals can be modified, whereas read-only Signals can only be read.
Now that you understand the theory behind Signals, let’s dive into some examples and learn how and when to use Angular Signals within Angular applications.
Using Signals, computed Signals, and Signals effects
The best way to better understand something is to use it. So, without further ado, let’s start learning about Signals by writing some code. Start by cleaning up your `expenses-overview` component, clear the entire HTML template, and remove any logic you still have in the component class. Your component class and the corresponding HTML template should be empty when you’re done.
To explain Signals step by step, we will initially use hardcoded expenses inside the `expenses-overview` component. We’ll start by creating an `expenses` Signals with an initial value containing an array with some expenses inside:

```

expenses = signal<ExpenseModel[]>([ …… ]);

```js

 As you can see, we created a property and assigned it a `signal()` function. This function receives a parameter that sets the initial value of the Signal. In our example, we have added an array with some expenses for the initial value (you can create the mocked expenses based on `ExpenseModel`). You can manually add a type for your Signal using the arrow syntax, `<ExpenseModel[]>`, but the Signal also infers the type from the initial value. Let’s use this Signal inside our HTML template to output the expenses.
You can access a Signal like any other function – you use the property name of the Signal and add function brackets after it. In general, I don’t recommend using functions inside your HTML template, but Signals are an exception as they are non-computational functions; they return a value without computing anything. So, let’s output our Signal inside the HTML template:

```

<div class="container">

<h1>Expenses Overview</h1>

<table>

<tr>

<th>Description</th>

……

</tr>

@for (expense of expenses(); track expense.id){

<tr>

<td>{{ expense.description }}</td>

……

</tr>

}

</table>

</div>

```js

 Here, we’ve created an HTML table and used the control flow syntax to output a table row for each expense within our `expenses` Signal. We accessed the expenses by calling our Signal with `expenses()`. You can compose your own table headers and data rows or copy the HTML and CSS from this book’s GitHub repository. Now that you know how to create and use Signal values, next, you will learn how to update your Signals.
Updating Signals
A Signal can be updated by using the `set()` or `update()` method on it. The `set()` method sets an entirely new value, whereas the `update()` method allows you to use the current Signal value and construct a new value based on the current value of the Signal.
To demonstrate this, let’s add a modal with `AddExpenseComponent` inside the modal. Before you add the form inside the template, let’s update `AddExpenseComponent` so that it uses our new `ExpenseModel` instead of the `AddExpenseReactive` model we used in *Chapter 4*. Replace all instances of `AddExpenseReactive` with `ExpenseModel`. Now, change the `date` and `tags` fields in the `addExpenseForm` property to this:

```

date: new FormControl<string | null>(null, [Validators.required]),

tags: new FormArray<FormControl<string | null>>([

new FormControl('');

])

```js

 Now that we’ve updated the form so that it uses `ExpenseModel`, let’s import `AddExpenseComponent` and `ModalComponent` into `ExpensesOverviewComponent` so that we can use them inside the HTML template. Next, create a new Signal in `ExpensesOverviewComponent` to control the state of the modal component:

```

showAddExpenseModal = signal(false);

```js

 After adding the Signal to control the modal state, you can add both the modal and expense components to the HTML template, like this:

```

<bt-libs-modal [shown]=" showAddExpenseModal()" (shownChange)=" showAddExpenseModal.set(false)" [title]="'添加费用'">

<bt-libs-ui-add-expense-form #form (addExpense)="onAddExpense($event)" />

</bt-libs-modal>

```js

 As you can see, when the modal outputs the `shownChange` event, we use the `set()` method on the `showAddExpenseModal` Signal to set a new value for the Signal. In this scenario, we don’t care about the previous signal value because we know we want to close the modal when this event is fired. Because we don’t care about the previous value, we can use the `set()` method on the Signal to set a new value. Inside the component class, we need to add the `onAddExpense` method so that we can the expense we submit in the add expense form:

```

onAddExpense(expenseToAdd: ExpenseModel) {

this.expenses.update(expenses => [...expenses, expenseToAdd]);

this. showAddExpenseModal.set(false);

}

```js

 In the preceding code, we used the `update()` method to change the `expenses` Signal and the `set()` method for the `showAddExpenseModal` Signal. We use the `update()` method for the `expenses` Signal to access the current state of `expenses` and add the new expense to the existing expenses. When we submit the form, we also want to close the modal. For this, we can use the `set()` method because we just wish to change the Signal to a `false` value and are not interested in the current value of the Signal. Lastly, we need a button to open the modal:

```

<button (click)="showAddExpenseModal.set(true)">添加费用</button>

```js

 After adding the button, you can open the modal and create a new expense using `addExpenseForm`.
Lastly, it’s good to know that when you update a Signal using `set()` or `update()` in a component with `OnPush` change detection, the component will automatically be marked by Angular to be checked for changes. As a result, Angular will automatically update the component on the next change detection cycle.
Now that you know how to create and update Signals, let’s learn about computed Signals.
Computed Signals
`set()` or `update()` method on them. Instead, computed Signals automatically update when one or more Signals they derive their value from changes.
Let’s start with a basic example to better understand computed Signals and how they work. You don’t have to add this example inside `ExpensesOverviewComponent`; it’s just for demonstration purposes:

```

const count: WritableSignal<number> = signal(0);

const double: Signal<number> = count Signal and a computed Signal named double. The computed Signal uses the computed function, and the count Signal is used inside the callback of the computed function. When the value of the count Signal changes to 1, the value of the computed Signal is automatically updated to 2.

重要的是要知道，计算信号仅在它所依赖的信号有新的稳定值时才会更新。注意我说的是*稳定值*；这是因为信号异步提供更新，并且有点像 RxJS 的`switchMap`操作符，如果在旧数据流完成之前新数据流到来，它会取消前一个数据流。所以，如果您连续多次更新信号而不暂停，信号将不会稳定其值，因此计算信号将仅在信号的最后一次值变化上运行。

计算信号非常强大且效率很高。计算信号将不会在首次读取计算值之前计算任何值。接下来，计算信号将缓存其值，当您再次读取计算信号时，它将简单地返回缓存的值而无需运行任何计算。如果计算信号使用的信号值发生变化，计算信号将运行新的计算并更新其值。

由于计算信号缓存其结果，您可以在计算信号的回调函数内部安全地使用计算密集型操作，如过滤和映射数组。就像常规信号一样，计算信号在值变化时通知所有消费者。因此，所有计算信号的消费者都将显示最新的计算值。

现在，让我们向`ExpensesOverviewComponent`添加一个计算信号以显示总金额，包括增值税：

```js
totalInclVat = computed(() => this.expenses().reduce((total, { amount: { amountExclVat, vatPercentage } }) => amountExclVat / 100 * (100 + vatPercentage) + total, 0));
```

正如您所看到的，我们使用了`computed`函数，并在`computed`函数的回调中使用了`expenses`信号来检索当前的费用列表。我们可以使用`Array.reduce`函数在`expenses`数组上检索包括增值税在内的总成本。您可以像访问常规信号一样访问计算信号：

```js
this.totalInclVat()
```

让我们在 HTML 模板中创建一个新的表格行以显示总价值。您可以在 HTML 模板中的`for`循环下方添加表格行：

```js
<tr class="summary">
  <td>Total: {{totalInclVat()}}</td>
</tr>
```

假设你使用添加支出表单添加了一笔新支出。在这种情况下，你会注意到总金额会自动更新，因为计算信号使用`expenses`信号来评估总金额。当`expenses`信号发生变化时，计算信号也会根据`expenses`信号进行更新。

关于计算信号，还有一点值得了解的是，只有用于计算的信号才会被跟踪。例如，假设你添加了一个信号来控制是否显示或隐藏表格行摘要，以及另一个用于相应按钮文本的信号：

```js
showSummary = signal(false);
summaryBtnText = computed(() => this.showSummary() ? 'Hide summary' : 'Show summary');
```

你现在可以在计算信号内部使用以下`showSummary`信号，如下所示：

```js
totalInclVat = computed(() => this.showSummary() ? this.expenses().reduce(
    (total, { amount: { amountExclVat, vatPercentage } }) => amountExclVat / 100 * (100 + vatPercentage) + total,
    0
  ) : null);
```

在这个场景中，计算信号只会跟踪`expenses`信号，如果`showSummary`信号设置为`true`。如果`showSummary`信号设置为`false`，`expenses`信号在`computed`函数内部永远不会被访问，因此它不会因为变化而被跟踪。所以，如果你在`showSummary`信号设置为`false`时更新`expenses`信号，计算信号将不会计算新的值。

现在你已经了解了计算信号是什么，如何在代码中使用它们以及计算信号如何更新它们的值，让我们来探索信号效果。

信号效果

**信号效果**是每次信号变化时都会运行的副作用。你可以在信号效果内部执行任何你想要的逻辑。信号效果的用例可能包括记录日志、更新本地存储、显示通知或执行无法从 HTML 模板内部处理的 DOM 操作。

你可以通过使用`effect`函数并为其提供一个回调来创建信号效果：

```js
effect function is initialized. Furthermore, when you use a Signal inside the callback of the effect function, the effect function becomes dependent on that Signal, and the effect function will run each time one of the Signals it depends on has a new stable value.
It is also good to know that just as with computed Signals, a Signal effect only runs if the Signal within the `effect` function can be reached:

```

effect(() => {

if (this.showSummary()) {

console.log('更新后的支出:', this.expenses());

}

});

```js

 In the preceding example, the `effect` function will not run if the `expenses` signal updates while the `showSummary` signal is evaluated to be `false`. Besides unreached Signals, you can also prevent the Signal’s `effect` function from reacting to a Signal by wrapping that Signal in the `untracked` function:

```

effect = effect(() => {

console.log('摘要:', this.showSummary());

console.log('支出:', untracked(this.expenses()));

});

```js

 Another good thing to know about Signal effects is that they need access to the injection context. This means you need to declare the Signal inside the constructor or directly assign it to a property where you declare your component properties. An error will be thrown when you create a Signal effect outside the injection context. If you need to declare a Signal effect outside the injection context, you can provide the effect with the injection context like so:

```

injector = inject(Injector);

effect(() => {

console.log('更新后的支出:', this.expenses());

}, { injector: this.injector });

```js

 By default, effects clean up when the injection context where the effect is declared is destroyed. If you don’t want this to happen and you need manual control over the destruction of the signal effect, you can configure the effect so that it uses manual cleanup:

```

expenseEffect = effect(() => {

console.log('更新后的支出:', this.expenses());

}, { manualCleanup to true, you have to call the destroy() function on the effect:

```js
expenseEffect.destroy();
```

你还可以通过使用回调函数来挂钩信号效果的清理。这在你想在信号效果清理时执行一些逻辑时非常有用。以下是一个`onCleanup`回调的示例：

```js
effect((onCleanup) => {
  onCleanup(() => { console.log('Cleanup logic')})
})
```

除了`manualCleanup`和`onCleanup`回调之外，信号效果还有一个最后的配置选项。默认情况下，你不允许在信号效果内部更新信号；这是因为这很容易导致信号效果的无限执行。然而，你可以通过在信号效果上设置`allowSignalWrites`属性来规避这一点：

```js
expenseEffect = effect(() => {
  console.log(‹Updated expenses:›, this.expenses());
}, { allowSignalWrites: true });
```

现在你已经了解了关于信号效果的所有内容，包括如何使用它们以及如何触发或配置它们，让我们来学习信号组件的输入。

Signal 组件输入

自 Angular 17.1 以来，您也可以使用 Signal 作为组件输入，而不是使用`@Input()`装饰器和`ngOnChanges`生命周期钩子。

让我们看看 Signal 输入的一个示例，并将其与`@Input()`装饰器进行比较（您不需要在 monorepo 中添加示例，只是为了说明目的）：

```js
@Input() data!: DataModel; // The old way of doing things
data = input<DataModel>(); // The signal input
```

如您所见，Signal 输入在声明输入属性方面有一个更直接的方法。您声明一个属性并使用`input()`函数为其赋值；可选地，您还可以添加箭头语法来为 Signal 输入添加类型。如果您想为 Signal 输入提供一个初始值，您可以将其作为函数参数提供，如下所示：

```js
data = input({ values: [……], id: 1 });
```

就像输入装饰器一样，您也可以使输入成为必需的，使用输入别名，或创建输入上的转换函数：

```js
data = input.required<DataModel>();
data = input({ values: [……], id: 1 }, { alias: 'product'});
data = input({ values: [……], id: 1 }, transform: sort<DataModel>);
export function sort<T>(data: T[]): T[] {
  return data.sort((a, b) => a.id - b.id)
}
```

如您所见，您可以使输入成为必需的，并使用配置对象为 Signal 输入提供一个别名和转换函数。Signal 输入使用更简单的语法，有助于改进变更检测机制，并允许您移除`ngOnChanges`生命周期钩子。

当您将 Signal 输入与计算 Signal 结合使用时，可以移除`ngOnChanges`生命周期钩子，因为所有需要在特定属性输入时更新的属性现在都可以通过基于输入 Signal 的计算 Signal 自动更新。您想要运行的任何附加逻辑都可以在响应 Signal 输入的 Signal 效果内部声明。

既然您已经了解了 Signal 输入，让我们来学习 Signal 查询，它用于以响应式的方式与 HTML 元素交互。

Signal 查询

通常，您需要从模板中选择 HTML 元素并在组件类内部与之交互。在 Angular 中，这通常是通过使用`@ContentChild`、`@ContentChildren`、`@ViewChild`或`@ViewChildren`装饰器来实现的。在 Angular 17.2 中，引入了一种基于 Signal 的新方法，允许您以响应式的方式与 HTML 元素交互，并将它们与计算 Signal 和 Signal 效果相结合。使用基于 Signal 的方法而不是装饰器提供了一些额外的优势：

+   您可以使查询结果更加可预测。

+   所有基于 Signal 的查询都返回一个 Signal，当有多个值时，Signal 返回一个常规数组。装饰器返回多种返回类型，当您的查询返回多个值时，它们返回一个查询列表而不是常规数组。

+   基于 Signal 的查询可以用于随时间变化的 HTML 元素，因为它们是条件渲染的或通过`for`循环输出的。指令方法在这两种情况下都有问题，并且不会在模板更新时自动通知您。

+   TypeScript 可以自动推断查询 HTML 元素或组件的类型。

现在您已经了解了基于查询的信号与指令方法相比的优势，让我们来探索基于查询的信号的语法。与定义指令不同，基于信号的方法与简单的函数一起工作。有四个不同的函数：`viewChild()`、`contentChild()`、`viewChildren()`和`contentChildren()`。您向这些函数提供查询选择器，类似于您可以使用装饰器组合的查询选择器。以下是如何使用信号查询的示例：

```js
@Component({
  template: `
      <div #el></div>
      <my-component />
  `
})
export class TestComponent {
  divEl = viewChild<ElementRef>('el');
  cmp = viewChild(MyComponent);
}
```

在前面的示例中，我们使用了`viewChild()`查询信号来检索具有`#el` ID 的`<div>`元素和`<my-component>`元素。或者，如果您想检索具有相同选择器的多个元素，您可以使用`viewChildren()`函数。

通过这些，您知道了如何使用基于信号的新方法查询模板元素。您还了解了新的基于信号的方法相对于装饰器的优势。如果您更喜欢装饰器或者代码库中有装饰器，仍然可以使用装饰器。

在本节中，您学习了关于信号的内容。您学习了如何使用`set()`和`update()`方法创建、读取和更新信号。然后，我们在`ExpensesOverviewComponent`中添加了一些信号并学习了计算信号。最后，您学习了信号效果、信号组件输入和查询信号。

在上一节中，您学习了关于 RxJS 的内容；在下一节中，您将学习如何结合 RxJS 和信号。

结合信号和 RxJS

在本章中，您已经看到了信号和 RxJS 如何帮助您以响应式的方式管理数据变化。信号和 RxJS 都允许您在值发生变化时做出反应，并通过组合多个数据流或根据数据变化执行副作用来创建新值。因此，可能会出现以下问题：信号是否取代了 RxJS？我何时使用信号，何时使用 RxJS？

RxJS 有时可能感觉令人畏惧且复杂，因此一些开发者可能会倾向于完全用信号取代 RxJS。虽然这可能适用于某些应用程序，但 RxJS 和信号都在您的应用程序中有其位置，并解决不同的问题和需求。在许多情况下，您可以使用信号或 RxJS 为您的解决问题，但其中之一将更好地解决问题，并且用更少的代码行处理它。信号并不是要取代 RxJS，但信号将与之互补，并且在许多情况下，与您的 RxJS 代码一起工作。

由于信号和 RxJS 应该共存，Angular 创建了两个 RxJS 互操作性函数：`toSignal`函数。

使用 toSignal

`toSignal`函数用于将可观察对象转换为信号。`toSignal`函数与`ASYNC`管道非常相似，但具有更多的灵活性和配置选项，并且可以在应用程序的任何位置使用。语法相当简单；您使用`toSignal`函数并给它提供一个可观察对象：

```js
counter = counterObservable$ into a counter Signal. The toSignal function will immediately subscribe to counterObservable$, receiving any values the Observable emits from that point. As with regular Signals, you can use the Signals that were created with the toSignal function inside computed Signals and Signal effects. When toSignal changes its value because the Observable emits a new value, any Signal effect or computed Signal depending on that Signal will be triggered.
The `toSignal` function will also automatically unsubscribe from the Observable, given that the `toSignal` function is used within the injection context. When you use the `toSignal` function outside the injection context or want to make it dependent on a different injection context, you can provide the `toSignal` function with an injection context, like so:

```

injector = inject(Injector);

counter = toSignal(this.countObs$，toSignal 函数中包含一个额外的配置对象，其中我们提供了 injector 属性。也可能存在您不希望 toSignal 函数在组件或注入上下文被销毁时自动取消订阅 Observable 的场景。

您可能需要根据系统的需求在更早或更晚的时候停止 Observable。对于这种情况，您可以为 `toSignal` 函数提供一个 `manualCleanup` 配置，类似于 Signal 效应：

```js
counter = toSignal(this.countObs$, {manualCleanup to true, the toSignal function will receive values up to the point the Observable it depends on is completed. When the inner Observable has been completed, the Signal will keep returning the last emitted value; this is also the case if you don’t use the manualCleanup configuration. Besides having control over the unsubscribe process of the Observable used by the toSignal function, you can also provide an initialValue configuration.
The Observable you convert into a Signal might not immediately and synchronously emit a value upon subscription. Yet Signals always require an initial value, and if one isn’t provided or the value comes in asynchronously, the initial value of the Signal will be `undefined`. Because the initial value of the Signal is `undefined`, the type of the Signal will also be `undefined`. To prevent `undefined` being the initial, you can provide the `toSignal` function with an initial value using this syntax:

```

counter = toSignal(this.countObs$，{undefined 作为您的初始值可能在计算信号或使用 undefined 值的 Signal 效应中引起问题。

另一方面，一些 Observable 在订阅时会发出同步值；例如，考虑 `BehaviorSubject`。如果您使用在订阅时发出同步值的 Observable，您还需要在 `toSignal` 函数内部使用 `requireSync` 配置来配置这一点：

```js
counter = toSignal(this.countObs$, {requireSync: true});
```

通过将 `requireSync` 选项设置为 `true`，`toSignal` 函数强制在订阅时接收初始值是同步的，跳过初始的 `undefined` 值并将 Signal 类型化为 `undefined`。最后，您可以配置 `toSignal` 函数应该如何处理 Observable 中发生的错误。

默认情况下，如果 Observable 抛出错误，Signal 将在读取 Signal 时抛出错误。您还可以将 `rejectErrors` 选项设置为 `true`；在这种情况下，`toSignal` 函数将忽略错误并继续返回 Observable 发射的最后一个良好值：

```js
counter = toSignal(this.countObs$, {rejectErrors option to true, errors are handled in the same way the ASYNC pipe handles errors within Observables.
Using toObservable
The `toObservable` function is used to convert a Signal into an Observable. Here’s an example of how you can use the `toObservable` function:

```

counter = toSignal(this.countObs$);

countObs$ = toObservable 函数背后的场景，Angular 将使用 Signal 效应来跟踪在 `toObservable` 函数内部使用的 Signal 的值，并将更新的值发射到 Observable。因为 Angular 使用 Signal 效应来更新 Observable 的值，所以它只会发射 Signal 中的稳定变化。因此，如果您连续多次设置 Signal 而没有间隔，使用 `toObservable` 创建的 Observable 将只在 Signal 稳定时发射最后一个值。

默认情况下，`toObservable` 函数使用当前的注入上下文来创建 Signal 效应；如果您在注入上下文外部声明 `toObservable` 函数或想在另一个注入上下文中创建 Signal 效应，您可以向 `toObservable` 函数提供一个注入上下文：

```js
injector = inject(Injector);
countObs$ = toObservable(this.counter, {toObservable function – there are no other configuration options; you use the toObservable function and provide the function with your Observable to convert the Signal into an Observable. You can also combine both the toSignal and toObservable functions in one go.
Here’s an example of how you could use a Signal input and the `toObservable` and `toSignal` functions to fetch a new product and convert it into a Signal each time the component receives a new ID input:

```

id = input(0);

product = toSignal(

toObservable(this.id).pipe(

switchMap((id) => this.service.getProduct(id as number)),

),

{ initialValue: null }

);

```js

 Now that you know how to combine RxJS and Signals by using the `toSignal` and `toObservable` functions, let’s finish this chapter by providing a bit more clarity about when to use Signals and when to use RxJS.
Choosing between Signals and RxJS
As mentioned previously, neither Signals nor RxJS are one-size-fits-all solutions. When you’re building an application, chances are you’ll need both Signals and RxJS to create the most optimal code. The most straightforward distinction is that Signals handle synchronous code, and RxJS is used to handle synchronous code, but things aren’t always as simple. You could also convert synchronous code using the `toSignal` function. For clarity, we’ll go through some examples at face value and determine if using Signals or RxJS would be better. In the real world, there are always nuances, and you should take whatever best fits your scenario, the team, and the existing code of the application.
Let’s start with an HTML template. In an HTML template, you can use an RxJS Observable by using the `ASYNC` pipe, or you can use a Signal. I would try to use Signals in the HTML template as this simplifies the HTML template by maintaining a synchronous approach. Using Signals will also help improve the change detection mechanism Angular uses, which can improve your application’s performance.
There are more gray areas in the component classes where it might be more complex to determine whether to use a Signal or RxJS Observable. If we look at the local component state, I would use Signals and computed Signals to define the component state; this also allows you to consume the component state as signals inside the HTML template.
When it comes to handling user events, it depends a bit on how you need to process the values of the event. If it’s a simple event such as handling a form submission or a button click, a Signal will work perfectly fine to update the correlating values. If you need more control over the delivery of the value stream, combine multiple events, or map, filter, and transform the data stream before it reaches your application logic, RxJS will be a better fit.
A typical example is when you have a search input field that makes API requests. You don’t want to make too many API requests by firing an API call on each key-up event. Instead, you want to check if the user stopped typing for a specified interval. Using RxJS, this can be done using the `debounceTime` operator. You can handle the same functionality using a Signal, but this requires a lot more code, and it becomes more complex and less readable. Depending on your architecture, most other scenarios that are handled inside your components are connected with your facade services or state management.
Now, let’s discuss some different scenarios and compare Signals with RxJS. Events that have to be distributed throughout the application and where different parts of your application have to react differently are also best handled using RxJS, more specifically an RxJS `Subject`. Using a `Subject` class, each part of your application can listen for the Observable and react how it needs to react.
Defining simple synchronous global application states can be done using Signals and computed Signals. The current value of the state can be retrieved by using Signals. Additionally, you can define change events using RxJS subjects if different application parts need to perform different logic when the values change. You can trigger the RxJS subjects inside your state management using a signal effect. Using the Signal effect might only work if reacting on stabilized value changes is enough; if you need to react to multiple changes that follow on from each other, this approach will not work for you.
When you have more complex state or asynchronous sources that need to be modified, combined, filtered, or mapped before you can provide the values to the rest of your application, RxJS is the best solution to handle the data streams. Especially when you need to handle multiple nested Observables or if you want to combine various streams and need control over when and how the values of these different streams are processed, RxJS offers many more tools to handle this gracefully.
Inside your facade services, you can combine RxJS and Signals. Depending on the complexity and setup of your state, a good approach is to use the `toObservable` function and RxJS to create the models you need to expose to the view layer. Once you’ve mapped all the data streams into the models and values you need, you can use `toSignal`, Signals, and computed Signals to expose the values to the view layer. Then, inside the view layer, you can consume the Signals synchronously while the facade service updates them asynchronously.
Now that you have a better idea of when to use Signals and when to use RxJS, let’s move on to the next chapter and start learning about state management.
Summary
In this chapter, you learned about reactive programming. You learned what reactive programming is, how the Angular framework uses it, and how it can be utilized to make your code efficient, event-driven, and performant.
Next, we did a deep dive into RxJS and saw how it can be used to create and handle Observable streams. You learned about different types of Observables and how to combine, flatten, and modify Observable streams using RxJS operators. We also explored some of the most used RxJS operators and learned how to create operators using the pipe function.
After understanding RxJS, we moved on to Angular Signals. You learned why Angular introduced Signals into the framework and how they help simplify your Angular code and improve the performance of your applications. You learned about Signals, computed Signals, the Signal effect, and interoperability functions for Signals and RxJS. We finished this chapter by exploring when you should use Signals and when to use RxJS within your applications.
In the next chapter, we will take a deep dive into state management.

```

```js

```

```js

```

```js

```

```js

```

```js

```

```js

```
