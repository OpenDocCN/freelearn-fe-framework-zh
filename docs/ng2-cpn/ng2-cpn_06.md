# 第六章：组件通信

到目前为止，我们已经构建了一个单一的组件，但是 Angular 组件的真正力量在于构建它们之间的交互。在本章中，我们将学习组件如何以不同的方式进行通信：

+   从父组件通过属性向子组件传递数据

+   在子组件上定义自定义事件供父组件监听

+   通过本地变量进行通信

+   使用父组件查询子组件

# 通过属性传递数据

父组件可以通过属性将数据传递给子组件。有两种方式可以为组件定义输入属性：

+   通过在组件装饰器上创建一个输入数组

+   使用`@Input`装饰器装饰一个类属性

使用组件输入数组非常简单明了。只需声明一个输入数组，并用表示您期望的属性名称的字符串填充它：

```ts
[app.component.ts]
import { Component } from '@angular/core';

@Component({
  selector: 'child-component',
  inputs:   ['title'],
  template: `<h2>{{ title }}</h2>`
})

export class ChildComponent {}

@Component({
  selector: 'app-root',
  template: ` 
    <h1>Component Interactions</h1>
    <child-component [title]="title" ></child-component>
  `
})
export class AppComponent {
  private title: string = "Sub title for child";
}
```

在这个例子中，我们创建了一个子组件，它定义了一个名为`title`的单个字符串输入数组，表示父组件可以绑定并通过其传递数据的属性。

不要忘记将`ChildComponent`类添加到`AppModule`的 declarations 属性中。否则，该组件无法在`AppComponent`的模板中使用。每次需要在另一个组件和同一模块中使用组件或指令时，都需要进行此配置：

```ts
[app.module.ts]
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { HttpModule } from '@angular/http';
import { AppComponent, ChildComponent } from './app.component';

@NgModule({
  declarations: [
    AppComponent,
    ChildComponent
  ],
  imports: [
    BrowserModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

当我们不需要在`Component`类中访问输入，并且不关心输入的类型时，输入数组的方法是合适的。

或者，我们可以使用`@Input()`装饰器将输入绑定到类属性：

```ts
[app.component.ts]
import { Component, Input } from '@angular/core';

@Component({
  selector: 'child-component',
  template: `<h2>{{ title }}</h2>`
})
export class ChildComponent {
  @Input() private title: string;
}

@Component({
  selector: 'app-root',
  template: ` 
    <h1>Component Interactions</h1>
    <child-component [title]="title"></child-component>
  `
})
export class AppComponent {
  private title: string = 'Sub title for child';
}
```

绑定到类属性（第二个例子）被认为是处理输入时的最佳实践。

输入可以是原始类型或对象。

# 发出自定义事件

当子组件需要与其父组件通信时，它可以触发一个事件。这种技术使子组件与其父组件解耦（解耦：不需要知道其父组件）。

在 Angular 中，如果我们想要触发事件，我们需要使用一个名为`EventEmitter`的类。

您需要实例化`EventEmitter`类，将其分配给一个类属性，并调用`emit`方法。

在下面的例子中，当用户点击标题时，子组件将触发一个名为`TitleClicked`的自定义事件：

```ts
[app.component.ts]
import { Component, Input, EventEmitter, Output } from '@angular/core';

@Component({
  selector: 'child-component',
  template: `<h2 (click)="titleClicked.emit()">{{ title }}</h2>`
})
export class ChildComponent {
  @Input() private title: string;
  @Output() private titleClicked = new EventEmitter<any>();
}

@Component({
  selector: 'app-root',
  template: ` 
    <h1>Component Interactions</h1>
    <child-component [title]="title" 
    (titleClicked)="clickHandler()"></child-component>
  `
})
export class AppComponent {
  private title: string = 'Sub title for child';
  clickHandler() {
    console.log('Clicked!');
  }
}
```

首先，我们从 Angular 核心中导入了`EventEmitter`类和`Output`装饰器。然后，我们创建了一个名为`titleClicked`的类属性，并将其分配给`EventEmitter`类的一个新实例。

然后，我们绑定了`<h2>`元素的原生点击事件，并调用了`titleClicked`对象的`emit()`方法。

父组件现在可以绑定到这个事件。

## 使用本地变量引用

一个组件可以使用本地变量访问另一个组件的属性和方法。在下面的例子中，我们为子组件创建了一个本地变量，该变量在模板内部可访问：

```ts
[app.component.ts]
import { Component } from '@angular/core';

@Component({
  selector: 'child-component',
  template: `
    <h2>Content Header</h2>
    <p *ngIf="flag">Toggleable Content</p>
  `
})
export class ChildComponent {
  private flag: boolean = false;
  toggle() {
    this.flag = !this.flag;
  }
}

@Component({
  selector  : 'app-root',
  template  : ` 
    <h1>Component Interactions</h1>
    <button (click)="child.toggle()">Toggle Child</button>
    <child-component #child></child-component>
  `
})
export class AppComponent {}
```

我们使用`#`符号创建一个本地变量。

子组件中的方法必须是公共的，否则 Angular 会抛出异常。

这种技术在某些情况下非常有用，因为它不需要在组件类内部编写任何代码。另一方面，引用上下文仅在模板内部。

如果您需要在父组件内部访问子组件，您需要使用`@ViewChild`装饰器注入对子组件的引用。

考虑以下例子：

```ts
[app.component.ts]
import { Component, ViewChild } from '@angular/core';

@Component({
  selector: 'child-component',
  template: `
    <h2>Content Header</h2>
    <p *ngIf="flag">Toggleable Content</p>
  `
})
export class ChildComponent {
  private flag: boolean = false;
  toggle(){
    this.flag = !this.flag;
  }
}

@Component({
  selector: 'app-root',
  template: ` 
    <h1>Component Interactions</h1>
    <button (click)="toggle()">Toggle Child</button>
    <child-component></child-component>
  `
})
export class AppComponent {
  @ViewChild(ChildComponent)
  private childComponent: ChildComponent;
  toggle(){
    this.childComponent.toggle();
  }
}
```

父组件正在使用`@ViewChild`装饰器（从 angular 核心导入），传递组件的名称，并将其分配给一个名为`childComponent`的本地类成员。

如果我们有多个子组件的实例，我们可以使用`@ViewChildren`装饰器。

# 使用父组件查询子组件

`@ViewChildren`组件将提供对给定类型的所有子组件的引用，作为`QueryList`，其中包含子实例的数组。

考虑以下例子：

```ts
[app.component.ts]
import { Component, ViewChildren, QueryList } from '@angular/core';

@Component({
  selector: 'child-component',
  template: `
    <h2>Content Header</h2>
    <p *ngIf="flag">Toggleable Content</p>
  `
})
export class ChildComponent {
  private flag: boolean = false;

  toggle(){
    this.flag = !this.flag;
  }
}

@Component({
  selector: 'app-root',
  template: ` 
    <h1>Component Interactions</h1>
    <button (click)="toggle()">Toggle Child</button>
    <child-component></child-component>
    <child-component></child-component>
    <child-component></child-component>
  `
})
export class AppComponent {
  @ViewChildren(ChildComponent)
  private children: QueryList<ChildComponent>;
  toggle(){
    this.children.forEach(child => child.toggle())
  }
}
```

`ViewChildren`和`QueryList`都是从 Angular 核心中导入的。

# 总结

组件可以以多种方式进行交互和通信。每种技术都适用于特定情况。主要区别与通信范围有关：模板上下文或组件类上下文。

这种灵活性使我们能够创建复杂的组件组合，轻松共享数据和交互，其中包括 API。

在下一章中，我们将构建有用的组件，还将学习关于 Angular 2 变化检测和组件生命周期。
