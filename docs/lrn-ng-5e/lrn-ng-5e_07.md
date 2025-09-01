

# 第七章：使用信号跟踪应用程序状态

Angular 通过**信号**赋予开发者使用其应用程序内内置反应性的能力。Angular 信号是反应式编程的同步方法，它有效地提高了应用程序性能并管理应用程序状态。

我们在之前的章节中遇到了信号，当时我们使用`input`方法在组件之间交换数据，使用`viewChild`方法查询子组件。信号 API 可以在 Angular 应用程序的不同部分使用，因此，其使用分散在这本书的各个章节中。

在本章中，我们将涵盖以下主题：

+   理解信号

+   读取和写入信号

+   计算信号

+   与 RxJS 协作

# 技术要求

本章包含各种代码示例，以向您介绍 Angular 信号的概念。您可以在以下 GitHub 仓库的`ch07`文件夹中找到相关源代码：

[`www.github.com/PacktPublishing/Learning-Angular-Fifth-Edition`](https://www.github.com/PacktPublishing/Learning-Angular-Fifth-Edition)

# 理解信号

如我们在*第三章*，*使用组件构建用户界面*中学习的那样，**Zone.js**在 Angular 应用程序的性能中发挥着重要作用。当应用程序内部发生特定事件时，它会触发 Angular 的变更检测机制。框架在每次检测周期中检查每个应用程序组件，并评估其绑定，从而降低应用程序的性能。

基于 Zone.js 的变更检测的原理是基于 Angular 无法知道应用程序内部何时或何地发生了变化。不可避免地，Angular 开发者试图通过以下技术来限制变更检测周期：

+   使用`OnPush`变更检测策略配置组件

+   使用`ChangeDetectorRef`服务手动与变更检测机制交互

信号通过根据应用程序需求简化并增强先前的技术来改善开发者与 Angular 变更检测机制的交互。

Angular 信号提供了基于反应性的更稳健和更人性化的变更检测周期管理。它们监视应用程序状态的变化，并允许框架通过仅在受变化影响的区域触发变更检测来做出反应。

信号是 Angular 框架的一个创新特性，它将通过引入未来的**无 Zone 应用程序**和**基于信号的组件**来进一步提高应用程序的性能。

信号还充当值的容器，变更检测机制必须检查这些值。当值发生变化时，信号会通知框架该变化。框架负责触发变更检测并更新任何信号消费者。信号值可以通过**可写**信号直接更改，或者通过只读或**计算**信号间接更改。

在下一节中，我们将学习可写信号的工作方式。

# 读取和写入信号

可写信号由`@angular/core` npm 包中的`signal`类型指示。

您需要我们在*第六章*，*Angular 中的响应式模式*中创建的 Angular 应用程序的源代码，以便跟随本章的其余部分。在您获取代码后，我们建议您为了简化，删除`key-logger`文件夹。

让我们开始学习如何在信号中写入一个值：

1.  打开`app.component.ts`文件，并从`@angular/core` npm 包中导入`signal`实体：

    ```js
    import { Component, inject, **signal** } from '@angular/core'; 
    ```

1.  在`AppComponent`类中声明以下属性为`signal`并初始化它：

    ```js
    currentDate = signal(new Date()); 
    ```

1.  将`setTitle`属性中的`timestamp`变量替换为以下代码片段：

    ```js
    this.currentDate.set(new Date()); 
    ```

在上述代码片段中，我们使用`set`方法在信号中写入新值。该方法通知 Angular 框架值已更改，并且它必须运行变更检测机制。

1.  将`title`属性修改为使用`currentDate`信号的值：

    ```js
    this.title = `${this.settings.title} (${**this.currentDate()**})`; 
    ```

在上述代码片段中，我们调用`currentDate`获取器方法来读取信号的值。

当应用程序的速度和性能很重要时，信号是一个很好的选择，例如：

+   一个包含小部件和实时数据的仪表板页面，必须定期更新，例如股票交易应用程序。

+   需要显示来自大型或复杂对象的属性组件，例如以下内容：

    ```js
    const order = {
      no: '1',
      date: new Date(),
      products: [
        { 
          id: 1,
          title: 'Keyboard',
          price: 100
        },
        {
          id: 2,
          title: 'Microphone',
          price: 35
        }
      ],
      customerCode: '0002',
      isCompleted: false
    }; 
    ```

在这种情况下，我们可以在不涉及整个对象进入变更检测周期的情况下，从信号中提取我们想要的属性，例如：

```js
const orderDetails = signal({
  no: '1',
  customerCode: '0002',
  isCompleted: false
}); 
```

与信号类似的方法，也可以触发变更检测的是`update`方法。当我们要根据信号当前值设置新的信号值时使用它：

```js
this.currentDate.update(d => {
  return new Date(d.getFullYear(), d.getMonth(), d.getDate(), 0, 0);
}); 
```

上述代码片段将获取`currentDate`信号在`d`变量中的值，并使用它来返回一个新的`Date`对象。

在下一节中，我们将探讨计算信号在 Angular 应用程序中的行为。

# 计算信号

计算信号或只读信号依赖于其他信号，无论是可写还是计算。计算信号的值不能直接使用`set`或`update`方法更改，它只能在其他信号的值更改时间接更改。

让我们看看它是如何工作的：

1.  打开`app.component.ts`文件，并从`@angular/core` npm 包中导入`computed`和`Signal`实体：

    ```js
    import {
      Component,
      inject,
      **Signal**,
      **computed**,
      signal 
    } from '@angular/core'; 
    ```

1.  将`title`组件属性的类型更改为`Signal`：

    ```js
    title: **Signal<string> = signal('');** 
    ```

`Signal`类型表示该信号是一个计算信号。

1.  从`setTitle`方法中删除`title`赋值，并将其添加到`constructor`中，如下所示：

    ```js
    constructor() {
      **this****.title$.****subscribe****(****this****.setTitle);**
      **this****.****title =** **computed****(() => {**
        **return****`****${****this.settings.title}****(****${****this****.currentDate()}****)`****;**
      **});**
    } 
    ```

在上述代码片段中，我们使用`computed`函数设置`title`信号的值。`title`信号的值取决于`currentDate`信号。当`currentDate`信号的值更改时，它每 2 秒更新一次。

1.  打开 `app.component.html` 文件并按以下方式修改 `<header>` HTML 元素：

    ```js
    <header>{{ **title()** }}</header> 
    ```

1.  使用 `ng serve` 运行应用程序并验证标题是否正确更新。

相比之前的计算，计算信号在更复杂的计算方面具有优异的性能，原因如下：

+   计算函数在模板中首次读取信号值时执行

+   只有当派生信号发生变化时，才会计算新的信号值

+   计算信号使用缓存机制来记忆值并在不重新计算的情况下返回它们

尽管信号是 Angular 的现代反应式方法，但与 RxJS 相比，它们在 Angular 生态系统中相对较新。在下一节中，我们将学习它们如何在 Angular 应用程序中与 RxJS 协作。

# 与 RxJS 协作

信号和 RxJS 为 Angular 应用程序赋予了反应式能力。这些库可以相互补充，在利用 Angular 框架优势的同时提供反应性。信号并非旨在取代 RxJS，而是为开发者提供了一种具有以下额外特性的替代反应式方法：

+   精细粒度反应性

+   强制性编程

+   改进了变更检测机制的用法

然而，Angular 框架的核心部分仍然使用 RxJS 和可观察者，例如 HTTP 客户端和路由器。此外，许多开发者更喜欢 RxJS 库提供的开箱即用的声明式方法。

在撰写本文时，Angular 团队目前正在调查和实验，以便在可预见的未来将 RxJS 作为 Angular 应用程序的**可选**组件。他们还在努力将内置 API，如 HTTP 客户端和路由器，转换为信号。

Angular 信号提供了与 RxJS 和可观察者协作的内置 API。信号 API 提供了一个可以将可观察者转换为信号的功能：

1.  打开 `product-list.component.ts` 文件并导入 `inject` 和 `toSignal` 依赖项：

    ```js
    import { Component, **inject** } from '@angular/core';
    **import** **{ toSignal }** **from****'@angular/core/rxjs-interop'****;** 
    ```

`@angular/core/rxjs-interop` npm 包包括处理信号和可观察者协作的所有实用方法。`toSignal` 函数可以将可观察者转换为信号。

`rxjs-interop` 包还包含将信号转换为可观察者的实用方法。您可以在 Lamis Chebbi（Packt Publishing）的《使用 RxJS 和 Angular 信号的反应式模式》中了解更多信息。

1.  在 `ProductListComponent` 类中创建以下信号：

    ```js
    products = toSignal(inject(ProductsService).getProducts(), {
      initialValue: []
    }); 
    ```

在 `toSignal` 函数中，我们传递两个参数：我们想要转换的可观察者和可选的初始值。在这种情况下，我们传递 `ProductService` 类的 `getProducts` 方法，该方法返回一个可观察者，我们还设置了信号的初始值为空数组。

1.  打开 `product-list.component.html` 文件并按以下方式修改其内容：

    ```js
    @if (products().length > 0) {
      <h1>Products ({{products().length}})</h1>
    }
    <ul class="pill-group">
      @for (product of products() | sort; track product.id) {
        <li class="pill" (click)="selectedProduct = product">
          @switch (product.title) {
            @case ('Keyboard') { ![img](img/Icon-041.png) }
            @case ('Microphone') { ![img](img/Icon-05.png) }
            @default { ![img](img/Icon-09.png) }
          }
          {{product.title}}
        </li>
      } @empty {
        <p>No products found!</p>
      }
    </ul>
    <app-product-detail
      [product]="selectedProduct"
      (added)="onAdded()"
    ></app-product-detail> 
    ```

在前面的模板中，我们移除了顶部的`@if`块，并将`products`属性转换成了一个信号。我们不需要`async`管道，因为信号会自动订阅到一个可观察对象。

1.  为了进一步清理我们的组件，我们可以移除任何与`async`管道和可观察对象相关的代码，因为它们不再需要。结果`product-list.component.ts`文件应该是以下内容：

    ```js
    import { Component, inject } from '@angular/core';
    import { toSignal } from '@angular/core/rxjs-interop';
    import { Product } from '../product';
    import { ProductDetailComponent } from '../product-detail/product-detail.component';
    import { SortPipe } from '../sort.pipe';
    import { ProductsService } from '../products.service';
    @Component({
      selector: 'app-product-list',
      imports: [ProductDetailComponent, SortPipe],
      templateUrl: './product-list.component.html',
      styleUrl: './product-list.component.css'
    })
    export class ProductListComponent {
      selectedProduct: Product | undefined;
      products = toSignal(inject(ProductsService).getProducts(), {
        initialValue: []
      });  

      onAdded() {
        alert(`${this.selectedProduct?.title} added to the cart!`);
      }
    } 
    ```

1.  使用`ng serve`运行应用程序，并观察应用程序输出显示产品列表。

前面的代码片段看起来简单多了。Angular 信号不仅提高了我们应用程序的性能，还改善了开发者的体验和人体工程学。

# 摘要

在本章中，我们探讨了信号，这是 Angular 中一个新的响应式模式，用于管理应用程序状态。我们学习了其原理以及它们与 Zone.js 的比较。我们还探讨了如何将值读入和写入信号。我们还学习了如何创建依赖于其他信号值的计算信号。

在下一章中，我们将学习如何使用 Angular HTTP 客户端并从远程端点获取数据。
