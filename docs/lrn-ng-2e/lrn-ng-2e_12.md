# 第十二章：使用 Angular 为组件添加动画

如今，动画是现代用户体验设计的基石之一。远非仅仅是用来美化 UI 的视觉点缀，它们已经成为视觉叙事的重要组成部分。动画为以非侵入方式传达信息铺平了道路，成为了一个廉价但强大的工具，用来告知用户在与我们的应用程序交互时发生的基础过程和事件。一旦一个动画模式变得普遍，并且受众接受它作为现代标准，我们就获得了一个无价的工具，用来增强我们应用程序的用户体验。动画是与语言无关的，不一定绑定在单一设备或环境（Web、桌面或移动），并且当明智地使用时，它们对于观看者来说是令人愉悦的。换句话说，动画是不可或缺的，而 Angular 2 对现代视觉开发的这一方面有着强烈的承诺。

随着所有现代浏览器都支持 CSS3 的新特性来处理动画，Angular 2 提供了支持通过一个简单但强大的 API 来实现命令式动画脚本。本章将涵盖几种实现动画效果的方法，从利用纯粹的 CSS 来应用基于类的动画，到实现脚本例程，其中 Angular 完全负责处理 DOM 转换。

在这一章中，我们涵盖以下主题：

+   使用纯粹的 CSS 创建动画

+   利用`ngClass`指令来更好地使用类命名动画

处理转换

+   查看 Angular 内置的 CSS 钩子，为每个定义样式

转换状态

+   引入动画触发器，并在模板中声明性地将这些动画附加到元素上

+   使用**AnimationBuilder** API 来为组件添加动画

+   设计处理动画的指令

# 使用纯粹的 CSS 创建动画

基于 CSS 的动画的诞生是现代网页设计中的重要里程碑。在那之前，我们过去常常依赖 JavaScript 来通过复杂和繁琐的脚本来操作 DOM 元素，通过间隔、超时和各种循环来实现我们网页应用中的动画。不幸的是，这既不可维护也不可扩展。

然后，现代浏览器采用了最近引入的 CSS 变换、过渡、关键帧和动画属性带来的功能。这在 Web 交互设计的背景下成为了一个改变游戏规则的因素。虽然像**Microsoft Internet Explorer**这样的浏览器对这些技术的支持远非理想，但其他可用的浏览器（包括微软自己的 Edge）对这些 CSS API 提供了全面的支持。

MSIE 仅在版本 10 及以上提供对这些动画技术的支持。

我们假设您对 CSS 动画的工作原理有广泛的了解，因此本书的范围显然不包括这些技术的覆盖。总之，我们可以强调 CSS 动画通常是通过这些方法之一或两者的组合来实现的：

+   过渡属性将作为 DOM 元素应用的所有或部分 CSS 属性的观察者。每当这些 CSS 属性中的任何一个发生变化时，DOM 元素不会立即采用新值，而是会经历一个稳定的过渡到新状态。

+   命名关键帧动画，我们在一个唯一的名称下定义了一个或多个 CSS 属性演变的不同步骤，稍后将在给定选择器的动画属性中填充，能够设置额外的参数，如延迟、动画缓动的持续时间或动画的迭代次数。

正如我们在前面提到的两种情况中所看到的，使用带有动画设置的 CSS 选择器是与动画相关的一切的起点，这就是我们现在要做的。让我们构建一个花哨的脉冲动画，以模拟装饰我们的番茄钟的位图中的心跳样式效果。

这次我们将使用基于关键帧的动画，因此我们将首先在单独的样式表中构建实际的 CSS 例程。整个动画基于一个简单的插值，我们将一个对象放大 10%，然后再缩小到初始状态。然后将这个基于关键帧的缓动命名并包装在一个名为`pulse`的 CSS 类中，它将在一个无限循环中执行动画，每次迭代需要 1 秒完成。

所有用于实现此动画的 CSS 规则将存储在外部样式表中，作为计时器小部件组件的一部分，位于`timer feature`文件夹内：

```ts
// app/timer/timer.widget.component.css

@keyframes pulse {
 0% {
 transform: scale3d(1, 1, 1);
 }
 50% {
 transform: scale3d(1.1, 1.1, 1.1);
 }
 100% {
 transform: scale3d(1, 1, 1);
 }
}

.pulse {
 animation: pulse 1s infinite;
}

.task { background: red;
 width: 30px;
 height: 30px;
 border-radius: 50%; }
```

从这一点开始，任何带有此类名称的 DOM 元素都将像心脏一样跳动。这种视觉效果实际上是一个很好的提示，表明元素正在进行某种操作，因此在倒计时进行时将其应用于计时器小部件中的主图标位图将有助于传达当前正在以生动的方式进行某种活动的感觉。

谢天谢地，我们有一个很好的方法，只在倒计时活动时应用这样的效果。我们在`TimerWidgetComponent`模板中使用`isPaused`绑定。将其值绑定到`NgClass`指令，以便仅在组件暂停时渲染类名，这样就可以打开计时器小部件代码单元文件，并添加对我们刚刚创建的样式表的引用，并按照之前描述的方式应用指令：

```ts
// app/timer/timer.widget.component.ts

import { Component } from  "@angular/core"; 
@Component({
 selector:  'timer-widget',
 styleUrls: ['timer.widget.component.css'],
 template: `
 <div  class="text-center">
 <div  class="task"  [ngClass]="{ pulse: !isPaused }"></div>
 <h3><small>{{ taskName }}</small></h3>
 <h1> {{ minutes }}:{{ seconds  |  number: '2.0' }} </h1>
 <p>
 <button  (click)="togglePause()"  class="btn btn-danger">
 Toggle
 </button>
 </p>
 </div>` })
export  class  TimerWidgetComponent { taskName:  string  =  'task';
 minutes  =  10;
 seconds  =  20;
 isPaused  =  true; 
 togglePause() {
 this.isPaused  =  !this.isPaused;
 }
}
```

就是这样！运行我们的番茄钟应用程序，点击顶部的`Timer`链接，进入计时器组件页面，并在启动倒计时后实时检查视觉效果。停止并再次恢复，以查看效果仅在倒计时活动时应用。

# 介绍 Angular 动画

动画触发器的想法是，当某个属性从一个状态变化到另一个状态时，您可以显示动画。要定义触发器，我们首先需要安装和导入我们需要的库，具体来说是`BrowserAnimationsModule`，所以让我们这样做。

我们通过输入以下命令来安装库：

```ts
npm install @angular/animations --save
```

现在让我们导入并设置带有`BrowsersAnimationsModule`的模块：

```ts
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';

@NgModule({
 imports: [BrowserAnimationsModule]
})
```

之后，是时候导入一堆我们需要设置触发器本身的结构：

```ts
import  { trigger, state, style, animate, transition }  from  '@angular/animations';
```

导入的结构具有以下功能：

+   `trigger`：这定义了组件中动画目标的属性；它需要一个名称作为第一个参数，以及作为第二个参数的状态和转换数组

+   `state`：这定义了属性值以及它应该具有的 CSS 属性；您需要为属性可以假定的每个值定义一个这样的属性

+   `transition`：这定义了当您从一个属性值转到另一个属性值时动画应该如何播放

+   `animate`：当我们从一个状态值转移到下一个状态时，执行定义的动画

# 我们的第一个触发器

让我们快速看一下动画触发器可能是什么样子，然后解释各个部分：

```ts
animations: [
 trigger('sizeAnimation', [
 state('small', style({
 transform:'scale(1)', 
 backgroundColor: 'green'
 })),
 state('large', style({
 transform: '(1.4)', 
 backgroundColor: 'red'
 })),
 transition('small => large', animate('100ms ease-in')),
 transition('large => small', animate('100ms ease-out'))
 ])
]
```

`animations`数组是我们添加到组件对象中的内容，比如模板或`styleUrls`。在`animations`数组中有许多触发器定义。`trigger`需要一个名称和一个项目数组，就像这样：

```ts
trigger('name', [ ... items ]) 
```

这些项目要么是状态定义，要么是过渡。有了这个知识，更容易理解我们正在看的是什么。目前，我们选择将触发器称为`animationName`。它定义了两个状态和两个过渡。状态表示值已更改为此状态，我们相应地通过执行样式来做出反应，这就是为什么代码应该被理解为以下内容：

```ts
state(
 'when I change to this value', 
 style({ /*apply these style changes*/ }))
```

请注意，样式属性是驼峰式命名，而不是短横线命名，例如，写`backgroundColor`而不是`background-color`，就像你在 CSS 中习惯的那样。

看看我们的例子，我们是在说以下内容：

+   如果有人触发`sizeAnimation`并且值设置为`small`，那么应用这个变换：`scale(1)`和`backgroundColor: 'green'`

+   如果有人触发`sizeAnimation`并且值设置为`large`，那么应用这个变换：`scale(1.4)`和`backgroundColor: 'red'`

剩下的两个项目是两个`transition`调用。这指示动画如何以平滑的方式应用动画。您可以这样阅读过渡定义：

```ts
transition(' when I go from this state > to this state ', animate( 100ms ease-in))
```

因此，当我们从一个状态转换到另一个状态时，我们应用一个缓动函数，并且还定义了动画应该执行多长时间。让我们回顾一下我们的代码：

```ts
transition('small => large', animate('100ms ease-in')),
transition('large => small',animate('100ms ease-out'))
```

我们这样解释它：

+   当我们从值`small`到`large`时，执行`100ms`的动画并使用`ease-in`函数

+   当我们从值`large`到`small`时，执行`100ms`的动画并使用`ease-out`函数

# 连接部分

现在我们已经完全解析了我们的`trigger`语句，我们还有最后一件事要做，那就是将触发器连接到它需要查看的属性。所以，让我们在模板中添加一些代码：

```ts
@Component({
 selector:  'example', template:  `
 <button  (click)="makeBigger()">Make bigger</button>
 <button  (click)="makeSmaller()">Make smaller</button>
 <p  class="animate"  [@sizeAnimation]="state">some text</p>
 `
 ,
 animations:  [
 trigger('sizeAnimation', [
 state('small',  style({
 transform:'scale(1)',
 backgroundColor:  'green'})),
 state('large',  style({
 transform:  'scale(1.4)',
 backgroundColor :  'red'
 })),
 transition('small => large',  animate('100ms ease-in')),
 transition('large => small',animate('100ms ease-out'))
 ])
 ],
 styles: [`
 .animate  {
 background:  green;
 width:  100px;
 }
 `] })
export  class  ExampleComponent  {
 state:  string;

 makeBigger() {
 this.state  =  'large';
 }

 makeSmaller() {
 this.state  =  'small';
 }
}
```

现在，要注意的关键是`[@animationName]='state'`；这是我们说触发器应该查看组件`state`属性，我们已经知道`state`应该具有哪些值才能触发动画。

# 通配符状态

我们为触发器定义的状态不仅仅是两个。在某些情况下，无论我们来自什么状态值，应用转换都更有意义。对于这些情况，有通配符状态。使用通配符状态很容易。您只需转到转换定义并用`*`替换状态值，如下所示：

```ts
transition('* => larger') 
```

这意味着无论我们之前处于什么状态，当我们的`state`属性假定一个`larger`值时，转换都会发生。

# 空状态

`void`状态不同于通配符状态。Void 与说如果一个元素之前不存在，那么它就有`void`值是一样的。在退出时，它假定一个值。因此，转换调用的定义如下：

```ts
transition(' void => *') 
```

通过向我们的模板添加一些代码，让我们使其更真实：

```ts
<button  (click)="abraCadabra()">Abracadabra</button> <button  (click)="poof()">Poof</button>   <p  class="elem"  [@flyInOut]="state"  *ngIf="showMe">
 Show me
</p>
```

在这里，我们添加了一个按钮，设置为调用`abraCadabra()`来显示元素，以及一个调用`poof()`的按钮，它将隐藏元素。现在让我们向组件添加一些代码：

```ts
trigger('flyInOut', [
 state('in', style({transform:  'translateX(0)'})), transition('void => *', [ style({transform:  'translateX(-100%)'}), animate(500) ]),
 transition('* => void', [ animate(500, style({transform:  'translateX(200%)'})) ])
])
```

这个触发器定义如下，如果一个元素从不存在到存在，`void => *`，那么从`-100%`到`x`位置`0`进行动画。当从存在到不存在时，将其移出画面，将其移动到`x`位置`200%`。

现在是最后一部分，我们的组件代码：

```ts
abraCadabra() { this.state  =  'in'; this.showMe  =  true; }

poof() {
 this.showMe  =  false; }  
```

在这里，我们可以看到调用`abraCadabra()`方法将触发状态`'in'`，并将布尔值`showMe`设置为`true`将触发转换`void => *`。这解释了`void`状态的主要目的，即在先前元素不存在时使用。

# 动画回调

有时候，您可能想要知道何时启动动画以及动画何时完成。在这里有好消息；我们可以找出这一点，并执行我们需要的任何代码。

我们需要做的是监听触发器的`start`和`done`属性，如下所示：

```ts
[@sizeAnimation.start]=animationStarted($event)
[@sizeAnimation.done]="animationDone($event)"
[@sizeAnimation]="state"
```

当然，我们需要向我们的组件添加代码，使其看起来像这样：

```ts
animationStarted() {
 // animation started, execute code
}

animationDone() {
 // animation ended, execute code
}
```

# 使用 AnimationBuilder 对组件进行动画处理

到目前为止，我们已经介绍了如何使用纯 CSS 进行动画处理，或者通过定义一个触发器来连接到我们的标记。还有另一种更程序化的动画处理方法。这种方法使用一个名为`AnimationBuilder`的服务。使这种方法起作用涉及一些关键因素，即：

+   `AnimationBuilder`：这是一个我们注入的服务；它有一个名为`build`的方法，当调用时创建一个``AnimationFactory``的实例

+   `AnimationFactory`：这是在`AnimationBuilder`实例上调用`build()`的结果；它已经被赋予了许多样式转换和一个或多个动画

+   `AnimationPlayer`：播放器需要一个元素来应用动画指令

让我们解释这些要点，这样我们就能理解发生了什么，什么时候发生，以及对哪个元素发生了什么。首先，我们需要将`AnimationBuilder`注入到组件的构造函数中，并且还需要注入一个`elementRef`实例，这样我们就有了动画的目标，就像这样：

```ts
import { AnimationBuilder } from '@angular/animations';

@Component({})
export class Component {
 constructor(
 private animationBuilder:AnimationBuilder,
 private elementRef: ElementRef
 ) {
 }
}
```

在这一点上，我们可以访问`animationBuilder`的一个实例，并准备好设置我们的样式转换和动画，所以让我们接着做：

```ts
ngOnInit() {
 const animationFactory = this.animationBuilder.build([
 style({ width : '0px' }), // set starter value
 animate(1000, style({ width:  '100px' }))  // animate to this new value ])
}
```

在这里，我们定义了一个将宽度初始设置为`0px`的转换，以及一个将宽度在`1`秒内设置为`100px`的动画。我们还将调用`animationBuilder.build()`的结果分配给了一个名为 animation 的变量，它的类型是`AnimationFactory`。下一步是创建一个动画播放器的实例，并决定要将此动画应用到哪个元素：

```ts
const  elem  =  this.elementRef.nativeElement.querySelector('.text'); const animationPlayer  =  animationFactory.create(elem);
```

我们在这里做了两件事；首先，我们指出了模板中我们想要应用动画的元素。接下来，我们通过调用`animation.create(elem)`并将我们的元素作为输入来创建一个动画播放器的实例。现在缺少的是在 UI 中创建元素，这样我们的`querySelector()`才能找到它。我们需要创建一个带有 CSS 类文本的元素，这正是我们在下面的代码中所做的：

```ts
@Component({
 template : `
 <p class="text">Animate this text</p> 
 ` 
})
export class ExampleComponent {}
```

最后一步是在我们的动画播放器实例上调用`play()`方法：

```ts
animationPlayer.play();
```

在浏览器中播放动画。您可以通过向我们的`style({})`方法调用添加更多属性来轻松扩展动画，就像这样：

```ts
ngOnInit() {
 const animation = this.builder.build([
 style({ 
 width : '0px', 
 height : '0px' 
 }),   // set starter values
 animate(1000, style({ 
 width:  '100px', 
 height:  '40px' })) ])
}
```

总之，`AnimationBuilder`是一种强大的方式，可以创建可重用的动画，您可以轻松地将其应用到您选择的元素上。

# 创建一个可重用的动画指令

到目前为止，我们已经看到了如何创建`AnimationBuilder`以及如何使用它来随意地以编程方式创建和应用动画。使其可重用的一种方法是将其包装在一个指令中。创建指令是一件相当简单的事情，我们已经做过几次了；我们需要记住的是，我们的指令将被应用到一个元素上，而这个元素就是我们的动画将要被应用到的东西。让我们总结一下我们需要在列表中做的事情：

1.  创建一个指令。

1.  注入`AnimationBuilder`。

1.  创建我们的动画。

1.  创建一个动画播放器。

1.  播放动画。

这个事情清单与我们解释`AnimationBuilder`的工作原理非常相似，而且应该是这样的；毕竟，指令是这里唯一的新东西。让我们定义我们的指令和动画；实际上并没有太多要做的。

```ts
@Directive({
 selector : '[highlight]'
})
export class HighlightDirective implements OnInit {
 constructor( 
 private elementRef: ElementRef,
 private animationBuilder: AnimationBuilder 
 ) {}

 ngOnInit() {
 const animation = this.animationBuilder.build([
 style({ width: '0px' }),
 animate(1000, style({ width : '100px' }))
 ]);
 const player = animation.create( this.elementRef.nativeElement );
 player.play();
 }
}
```

这就是我们需要的一切。现在我们可以将我们的指令应用到任何元素上，就像这样：

```ts
<p highlight>animate me</p>
```

# 总结

我们只是触及了处理动画的表面。要了解你可以做的一切，请阅读官方文档[`angular.io/guide/animations`](https://angular.io/guide/animations)。

在本章中，我们开始学习如何定义原始的 CSS 动画。然后，我们解释了动画触发器以及如何以声明方式将定义的动画附加到元素上。然后，我们看了如何以编程方式定义动画并随意将其附加到元素上。我们最后做的事情就是将我们的程序化动画打包到一个指令中。关于动画还有很多要学习的，但现在你应该对存在的 API 有基本的了解以及何时使用它们。走出去，让你的应用充满生机，但记住，少即是多。
