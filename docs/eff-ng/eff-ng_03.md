

# 第三章：使用指令、管道和动画增强您的应用程序

在构建前端应用程序时，我们经常需要在 HTML 模板中增强、转换、添加、删除或替换 DOM 元素和值。Angular 框架通过使用指令、管道和动画来简化这一过程。本章将解释如何在 Angular 中创建和使用指令、管道和动画。到本章结束时，您将了解指令的所有细节，从指令组合到使用强大选择器创建指令。您还将了解如何创建自定义管道并有效地使用内置管道。最后，我们将探讨如何在使用 Angular 构建的应用程序中构建和重用动画。

本章将涵盖以下主要主题：

+   使用和创建 Angular 指令

+   使用 Angular 管道转换值

+   创建和重用惊人的动画

# 使用和创建 Angular 指令

指令分为两种不同类型：**属性指令**和**结构指令**。Angular 有一个内置指令列表，并允许您创建自己的指令以覆盖您的个人用例。自 Angular 15 以来，引入了一个新功能：**指令组合**。指令组合允许您在组件装饰器内部分配指令，而不是在它们的模板中。指令组合还可以用于在其它指令的装饰器内部声明指令，从而实现同时应用多个指令的指令。

当您想在独立组件中使用指令时，需要将指令添加到组件的 `imports` 数组中。如果是内置指令，您还可以导入 `CommonModule`，因为 `CommonModule` 包含所有内置指令。当您使用内置的 Nx 生成器生成组件时，默认会添加 `CommonModule`。本节将向您介绍有关指令的所有内容，从属性指令开始。

## Angular 属性指令

**属性指令**作为修改 DOM 元素属性、行为或外观的工具。指令根据装饰有指令装饰器的类中定义的逻辑进行修改。属性指令通过将属性指令的选择器添加到 HTML 标签中分配，如下所示：

```js
<div appRedBackgroundHover. When you use an attribute directive with an input, you use the square bracket syntax like this:

```

<div [ngClass]="useRedStyles ? 'red' : 'blue'">点击</div>

```js

 In the preceding example, `ngClass` is the attribute directive, and we add it between square brackets because it receives an input. Now that you know how to use attribute directives in HTML templates, let’s explore the most commonly used built-in attribute directives and, after that, create a custom attribute directive.
Common built-in attribute directives
The most commonly used built-in attribute directives are the following:

*   `ngClass` or `class`: These directives are used to conditionally add CSS classes.
*   `ngStyle` or `style`: These directives are used to conditionally add inline styling.
*   `ngModel`: This directive is used for two-way data binding on “from” elements.

More built-in attribute directives exist, but these additional built-in directives belong to specific Angular packages, such as `routerLink` from the `formGroup` from the `routerLink` in *Chapter 2*, and we will learn more about directives from the forms package in *Chapter 4*. Now, let us learn how to create custom attribute directives.
Creating custom attribute directives
We will start by creating a new library with our custom generator. Name the new library `common-directives` and select `libs\shared\ui\common-directives\src\lib folder`, right-click, and select `directive` and select **@nx/angular – directive**. Now, follow these steps:

1.  Enter `highlight` for the **name*** field.
2.  Click on **Show** **all options**.
3.  Check the **standalone** checkbox.
4.  Click on **generate** in the top right of the window.
5.  When the directive is generated, export the directive in the `index.ts` file of the library:

```

export * from './lib/highlight.directive';

```js

 When you look at the `highlight.directive.ts` file, you will see an empty class with a directive decorator above it:

```

@Directive({

selector: ‹[btLibsUiHighlight]›,

standalone: true,

}) export class HighlightDirective {}

```js

 Inside the decorator, you’ll see the standalone flag set to true, indicating that the directive is standalone and doesn’t have to be included in an `NgModule`. Besides that, you’ll find the directive selector. The selector is used to apply the directive.
Now, let’s add some logic in the `HighlightDirective` class so that our directive does something. Start by injecting `ElementRef`. `ElementRef` gives you access to the host DOM element, the HTML elements applying the directive. After injecting `ElementRef`, you need to use `ElementRef` to adjust the host element that applies the directive. Here is an example highlighting the host element with a background color:

```

导出类 HighlightDirective 实现 OnInit 接口 {

private el = inject(ElementRef).nativeElement;

ngOnInit() { ElementRef 通过使用构造函数注入代替 inject 函数，然后在构造函数的功能括号内添加背景颜色和文字颜色。或者，如果您只想在宿主元素上设置样式、CSS 类或属性，可以使用 @HostBinding() 装饰器：

```js
@HostBinding('style.backgroundColor') get color() { return 'red'; }
```

现在，要应用这个指令，你需要在独立组件或 NgModule 中导入它，并在 HTML 元素上使用选择器，如下所示：

```js
<div @Input() decorators. The most common way to pass a value to a directive is to add an @Input() with the same name as the directive selector. In our case, this would look as follows:

```

@Input() btLibsUiHighlight!: string;

```js

 Now, you can use the `btLibsUiHighlight` property to assign the background color:

```

this.el.style.backgroundColor = this.btLibsUiHighlight;

```js

 To give the directive input a value, you need to add square brackets around the directive when you declare it on an HTML element and give it the value you want:

```

`<div [btLibsUiHighlight]="'orange'">我被高亮显示</div>

```js

 If you don’t like using the selector name in your TypeScript file but instead use a more descriptive property name, you can alias the input like this:

```

@Input('btLibsUiHighlight') background!: string;

```js

 This will probably give you a lint error because aliasing inputs is not generally recommended. Still, you can disable the lint error in this scenario if you prefer a more descriptive property name.
If you also want to customize the text color, add another `@Input()` property to the directive class. We will name the input property `textColor` and assign it a default value of `white`:

```

@Input() textColor = 'white';

```js

 This is how you assign additional inputs and their values in the HTML template:

```

`<div [btLibsUiHighlight]="'orange'" btLibsUiHighlight 输入是必需的；额外的输入是可选的，但请记住为它们提供一个默认值；否则，你可能会遇到错误或不受欢迎的 UI 行为。你可以将两个输入都设置为可选的，并带有默认值；你必须重命名输入，以便没有输入与指令选择器同名：

```js
@Input() background = 'black';
@Input() textColor = 'white';
```

接下来，你通过移除方括号并添加额外的输入来更改 HTML 元素上的指令，仅当你想要覆盖默认值时：

```js
<div @HostListner() directive. Inside the function brackets of the @HostListner() directive, you need to add the browser event you want to listen for. This is how we can adjust our directive so it will apply the highlight for mouseenter and restore the original text and background color for mouseleave:

```

private el = inject(ElementRef).nativeElement;

private originalColor = 'black';

private originalBackground = 'white';

@HostListener('mouseenter') onMouseEnter() {

this.originalColor = this.el.style.color;

this.originalBackground = this.el.style.backgroundColor;

this.el.style.backgroundColor = this.background;

this.el.style.color = this.textColor;

}

@HostListener('mouseleave') onMouseLeave() {

this.el.style.backgroundColor = this.originalBackground;

this.el.style.color = this.originalColor;

}

```js

 We’ve added a property to save the original background and text color to restore that for `mouseleave`. Lastly, we’ve added the host listeners so we can react to the `mouseenter` and leave the events of the host element. We’ve added the logic to adjust the background and text color inside the host listeners.
Now that you know how to create custom attribute directives and enhance them with inputs and host listeners, let’s explore structural directives.
Angular structural directives
**Structural directives** represent a tool for adding and removing DOM elements based on logic. The logic of when to add or remove DOM elements is defined in the directive class. Defining structural directives in your HTML templates is achieved like so:

```

`<div *ngFor>`指令；正如你所见，它以一个星号开头。这个星号是结构指令的典型前缀，有助于你在 HTML 模板中区分它们和属性指令。结构和属性指令之间的另一个区别是，你可以在 DOM 元素上声明多个属性指令，但只能有一个结构指令。如果你想应用多个结构指令，你需要用 ng-container 标签包裹 DOM 元素，并将额外的指令添加到 ng-container 标签中：

```js
<ng-container *ngIf="expression">
  <div *ngFor=»let item of list»>{{item}}</div>
</ng-container>
```

Angular 提供了内置指令，并允许你创建自己的。首先，让我们看看一些常见的内置结构指令

常见的内置结构指令

最常用的内置结构指令如下：

+   `*ngIf`：这个指令用于有条件地显示或隐藏 DOM 元素（或者（相对于指令），你可以使用新的`@if`控制流语法，如第二章所示）。

+   `*ngFor`：这个指令用于在 HTML 模板中创建 for 循环，并为数组中的每个项目输出一个 DOM 元素（或者（相对于指令），你可以使用新的`@for`控制流语法，如第二章所示）。

+   `*ngSwitch`：这个指令用于在 HTML 模板中创建 switch case，并显示匹配的 switch case 的 DOM 元素（或者（相对于指令），你可以使用新的`@switch`控制流语法，如第二章所示）。

如果你曾经开发过 Angular 应用程序，你很可能已经见过并使用过所有这些结构指令，因为它们真的很常见。尽管如此，`*ngIf` 和 `*ngFor` 指令还有一些额外的、不太为人所知的属性，我想解释并展示一下。之后，我们将创建一个自定义结构指令。

充分利用 *ngIf

`*ngIf` 指令用于有条件地显示 DOM 元素。DOM 元素不会渲染，除非属性或语句评估为 `true`。通常，当条件为 `true` 时，你需要显示一个 HTML 块，而当条件为 `false` 时，则显示另一个。一个常见的解决方案是使用两个 `*ngIf` 指令和一个相反的语句，如下所示：

```js
<div *ngIf="showContent">Show if true</div>
<div *ngIf="!showContent">Show if false</div>
```

这是一个完全有效且非常易读的语法，但 `*ngIf` 也允许你创建一个 `if-else` 语句。你可以使用以下语法来做这件事：

```js
<div *ngIf="showContent; else elseBlock">Show if true</div>
<ng-template #elseBlock>
  <div>Show if false</div>
</ng-template>
```

如你所见，这在 HTML 模板中略显笨重，因为你需要使用 `ng-template` 标签来显示当 `else` 语句被触发时的内容。你想要使用什么语法完全取决于你；没有更好的或更差的方法；这更多的是关于个人喜好。

有效使用 *ngFor

当使用 `*ngFor` 时，你可以添加许多属性来增强指令的使用。`*ngFor` 指令为列表中的每个项目输出 DOM 元素。通常，当你为列表输出 DOM 元素时，你想要知道当前索引，如果某个元素是第一个或最后一个元素，或者它是一个奇数或偶数索引。基于这些值，你可能想要添加一些样式类或在使用模板时使用特定的属性。`*ngFor` 指令允许你检测这些值，如下所示：

```js
<div *ngFor="let item of list;
    let i = index; let isFirst = first;
    let isLast = last; let isEven = even; let isOdd = odd»
>
  Item at index {{ i }}: {{ item }}
  Is first: {{ isFirst }} Is last: {{ isLast }}
  Is even: {{ isEven }} Is odd: {{ isOdd }}
</div>
```

如你所见，你可以在定义 `*ngFor` 指令的 `HTML` 标签中添加变量来访问 `index`、`first`、`last`、`odd` 和 `even` 等值。如果我们 `list` 是以下数组：`[0, 1, 2, 3]`，那么前面的代码片段将在浏览器中输出以下结果：

```js
Item at index 0: 0 Is first: true Is last: false Is even: true Is odd: false
Item at index 1: 1 Is first: false Is last: false Is even: false Is odd: true
Item at index 2: 2 Is first: false Is last: false Is even: true Is odd: false
Item at index 3: 3 Is first: false Is last: true Is even: false Is odd: true
```

除了 `index`、`first`、`last`、`odd` 和 `even` 属性外，`*ngFor` 还有一些其他属性可以用来提高其性能。

默认情况下，当你使用 `*ngFor` 渲染某些内容并且列表中发生变化时，Angular 会重新渲染整个列表。正如你可以想象的那样，这会对你性能产生负面影响。你可以添加 `trackBy` 函数来改善这一点。当你使用 `trackBy` 函数时，Angular 会通过 `index` 或 `ID` 来识别每个项目。通过这样做，它只会重新渲染发生变化的内容。建议你尽可能多地使用 `trackBy` 函数。在模板中，你可以这样定义 `trackBy` 函数：

```js
<div *ngFor="let item of users; trackBy: trackByFunction">
{{ item }} </div>
```

在你的组件类中，你可以这样定义 `trackBy` 函数：

```js
trackByFunction(index, user) { return user.id; }
```

现在你已经了解了 `*ngIf` 和 `*ngFor` 的隐藏功能，让我们看看你如何可以创建自定义结构指令。

创建自定义结构指令

创建自己的结构化指令与创建属性指令类似，但指令类有一些关键的区别。此外，使用场景也不同。自定义属性指令对于诸如自动聚焦元素、将不同主题应用于特定元素、突出显示、文本缩放、工具提示和弹出窗口，以及添加 CSS 类、aria 属性或 ID 等很有用。自定义结构化指令用于删除或添加 DOM 元素；一些好的用例包括`if false`指令、`"重复 x 次"`函数，以及根据权限或特定窗口大小显示或隐藏元素。

我们将创建一个自定义指令，当条件为假时显示一个元素，这基本上是`*ngIf`指令的相反。首先，使用与生成自定义属性指令相同的步骤生成该指令（参见*创建自定义属性指令*部分）。唯一的区别将是名称；这次，将指令命名为`ifFalse`。

当你的指令类生成后，你可以开始添加你的结构化指令的逻辑。在属性指令中，你注入了`ElementRef`；对于结构化指令，你必须注入`TemplateRef`和`ViewContainerRef`。当你将结构化指令添加到 HTML 元素时，Angular 会将其转换为嵌入式模板，使用类似于这样的`ng-template`标签：

```js
<ng-template [ ngIf ]="condition">
  <div>Shown when condition is true</div>
</ng-template>
```

Angular 创建的嵌入式模板是使用`TemplateRef`访问的。嵌入式模板在没有结构化指令使用`ViewContainerRef`将其添加到视图容器之前不会渲染。`ViewContainerRef`为你提供了访问定义指令宿主元素的视图。

让我们从向你的指令类添加`TemplateRef`和`ViewContainerRef`开始。你可以通过构造函数注入或使用`inject`函数来注入`TemplateRef`和`ViewContainerRef`；我将像这样使用`inject`函数：

```js
private templateRef = inject(TemplateRef);
private viewContainer = inject(ViewContainerRef);
```

我们还必须跟踪是否已经将嵌入式视图添加到视图容器中。为此，在指令类中添加另一个私有属性：

```js
private embeddedTemplateAdded = false;
```

接下来，我们需要为我们的`if false`指令提供一个`@Input()`，这样我们就可以给它一个条件来评估。我们将使用`@Input()`设置器，因此每次它都会接收到一个新的值；当条件评估时，我们可以执行添加或删除嵌入式模板到视图的逻辑。`@Input()`设置器需要与指令选择器具有相同的名称：

```js
@Input() set btLibsUiIfFalse(condition: boolean) {}
```

在`@Input()`设置器内部，如果条件评估为`false`并且我们尚未将其添加到嵌入式模板中，我们将添加嵌入式模板作为视图容器的嵌入式视图。在`if false`指令的情况下，当条件评估为真，并且考虑到我们已经添加了嵌入式模板，我们希望清除视图容器，以便移除之前添加的嵌入式视图，并且 Angular 再次渲染原始 HTML，而不添加额外的嵌入式模板。为了实现这一点，你可以按如下方式更改`@Input()`：

```js
@Input() set btLibsUiIfFalse(condition: boolean) {
 if (!condition && !this.embeddedTemplateAdded) {
   this.viewContainer.createEmbeddedView(this.templateRef);
   this.embeddedTemplateAdded = true;
 } else if (condition && this.embeddedTemplateAdded) {
   this.viewContainer.clear();
   this.embeddedTemplateAdded = false;
 }}
```

正如你所见，当我们的检查通过时，我们会将`TemplateRef`作为一个嵌入式视图添加；否则，我们会清除视图容器，因为我们已经添加了`TemplateRef`。

我们现在可以使用我们的自定义结构指令，就像使用其他任何指令一样，通过使用带有前缀星号的指令选择器：

```js
<div *btLibsUiIfFalse="condition">shown when false</div>
```

现在你已经了解了如何使用内置指令、创建自定义属性和结构指令，让我们学习一下你可以使用指令选择器做什么。

指令选择器

`btLibsUiHighlight`指令并更改选择器，以便它默认应用于所有`span`元素：

```js
selector: 'span, [btLibsUiHighlight]',
```

当你使用前面的示例作为选择器时，指令将自动应用于所有`span`元素，并且你可以使用`btLibsUiHighlight`选择器将其添加到其他元素。现在，假设你需要一个选项来排除一些`span`元素，那么默认情况下，所有`span`元素都会收到高亮指令，但当你想要退出时，你可以这样做。为了实现这一点，你可以在选择器中添加`:not`语法，如下所示：

```js
selector: 'span:not([noHighlight]), [btLibsUiHighlight]',
```

现在，所有`span`元素都将应用高亮指令，除非你将`noHighlight`添加到`span`元素中。对于所有其他元素，你仍然需要添加`btLibsUiHighlight`以应用指令：

```js
<span noHighlight>Test</span>
```

如果你想要排除 HTML 元素，你可以使用`:not`语法来实现，如下所示：

```js
selector: '[btLibsUiHighlight]:not(label)',
```

当你使用前面的选择器时，你可以将`btLibsUiHighlight`指令添加到所有元素，但不能添加到`label`元素。当你尝试将指令添加到`label`元素时，编译器将抛出错误。

你也可以创建选择器，将指令应用于具有特定 ID、数据属性或应用于 HTML 元素的 CSS 类的 HTML 元素。以下是这三个选项的示例：

```js
selector: '#someId, .someCssClass, [data-highlight="true"]'
```

现在你已经了解了关于内置指令、自定义指令和指令选择器的所有内容，让我们继续学习指令组合。

Angular 指令组合

**指令组合**是一个相对较新的概念，它在 Angular 15 版本中被引入。正如其词义所示，指令组合允许你在组件和指令上组合不同的指令。它允许你在组件和指令类装饰器中声明指令，而不是使用 HTML 模板添加它们。你可以使用指令组合来自动将指令应用于组件，就像指令选择器一样。指令组合还可以用来创建使用单个选择器应用多个指令的指令。

假设我们有一个标签和按钮组件以及一个类型和大小指令，允许你设置类型（主要或次要）和大小（小、中或大），这将为宿主元素应用特定的 CSS 类。如果你想自动将这两个指令应用于所有的按钮和标签，你可以使用指令组合来实现这一点。在组件装饰器内部添加一个 `hostDirectives` 数组以将指令添加到组件中。在 `hostDirectives` 数组中，你可以添加包含指令和装饰器输入的对象。如果装饰器没有输入，你可以将装饰器类添加到 `hostDirectives` 数组中。如果你总是想使用输入的默认值（假设输入有默认值），你不需要在 `hostDirectives` 数组中声明输入：

```js
@Component({ ………, hostDirectives: [
{directive: TypeDirective, inputs: ['btLibsUiType']},
{directive: SizeDirective, inputs: ['btLibsUiSizeType']}]})
export class ButtonComponent { }
```

在定义了 `hostDirectives` 数组中的对象之后，当你在一个模板中声明按钮或标签组件时，这两个指令将自动应用。当使用指令组合时，你还可以为指令的输入值设置别名：

```js
{directive: TypeDirective, inputs: ['btLibsUiType: style']}
```

现在，在你的 HTML 模板中，如果你想为 `TypeDirective` 的 `btLibsUiType` 输入提供一个值，你可以使用以下语法：

```js
<bt-libs-button [style]="'secondary'">XX</bt-libs-button>
```

在指令内部使用指令组合与在组件中相同。假设我们有一个 `backgroundColorDirective` 和 `textColorDirective`；我们可以在 `backgroundColorDirective` 的 `hostDirectives` 数组中声明 `textColorDirective`。现在，当你使用 `backgroundColorDirective` 时，这两个指令都将应用，并且两个指令的输入都将暴露出来，前提是你已经在 `backgroundColorDirective` 的 `hostDirectives` 数组中定义了 `textColorDirective` 的输入。

当使用指令组合时，你需要使用独立的指令。否则，它将不会工作。此外，每次创建一个组件时，`hostDirectives` 数组中声明的所有指令都会创建一个新的实例。因为每个指令实例都是为宿主组件的每个实例创建的，所以在使用指令组合时必须小心。当你将太多指令放入常用组件内部时，你的内存使用量将会激增，并会负面影响你应用程序的性能。

在本节中，你学习了属性指令、结构指令、指令选择器和指令组合。现在我们将继续学习本章的下一部分，开始了解如何使用 Angular 管道来转换值。

使用 Angular 管道转换值

在 Angular 中，**管道**用于转换值。Angular 提供了许多有用的内置管道，并允许你创建自己的管道。让我们首先列出最强大和最常用的内置管道，并简要说明它们的使用目的：

+   `AsyncPipe`：`AsyncPipe`用于处理模板中的异步值。它自动订阅并自动取消订阅，以防止内存泄漏。建议尽可能多地使用`AsyncPipe`。

+   `UpperCasePipe`：此管道用于将文本值转换为全部大写字母。

+   `LowerCasePipe`：此管道用于将文本值转换为全部小写字母。

+   `TitleCasePipe`：此管道用于将每个单词的首字母大写。

+   `CurrencyPipe`：此管道用于将数值转换为带有货币符号的货币值。你还可以控制小数格式。

+   `DatePipe`：此管道用于根据你指定的格式格式化日期值。

如果你想要探索所有内置管道，你可以在这个网址找到完整的列表：[`angular.io/guide/pipes`](https://angular.io/guide/pipes)。

现在你已经了解了最常用的内置管道，让我们看看如何使用管道。

在 HTML 模板和 TypeScript 文件中使用管道

管道通常用于 HTML 模板，但你也可以在 TypeScript 文件中使用它们。要在 HTML 模板中使用管道，你可以使用以下语法：

```js
<div>{{currentDate | date}}</div>
```

在左侧，你有一个属性或值；然后，你通过使用垂直线（管道符号：`|`）来指示你将使用一个管道；在管道符号的右侧，你声明你想要使用的管道的名称；在我们的例子中，它是`date`。如果你的管道需要参数，你可以通过添加冒号并提供值来提供参数：

```js
<div>{{currentDate | date: 'YYYY-MM-dd'}}</div>
```

当管道需要多个参数时，你可以通过添加另一个冒号并将值附加到冒号之后来将它们链接在一起，如下所示：

```js
<div>{{currentDate | date: 'YYYY-MM-dd':'GMT'}}</div>
```

如果你需要应用多个管道，你可以将它们链接到一个值上。当你链式调用管道时，它们将按从左到右的顺序依次执行。链式调用管道使用以下语法：

```js
<div>{{currentDate | date: 'YYYY-MM-dd' | uppercase}}</div>
```

如前所述，你还可以在 TypeScript 文件中使用管道。尽管管道主要用于 HTML 模板，但它们在 TypeScript 文件中也非常有用。你可以在组件的提供者数组中添加管道，然后通过依赖注入注入管道。注入管道后，你可以在组件类中通过调用`transform`方法来使用它：

```js
const formattedDate = this.datePipe.transform(this.currentDate, 'dd/MM/yyyy');
```

当使用独立组件（正如我们正在做的那样）时，在可以使用管道之前，你需要将管道导入到组件中。你可以导入包含所有管道的`CommonModule`，或者如果你是一个简单的组件并且不需要`CommonModule`用于其他目的，你可以导入特定的管道。

现在你已经知道了如何在 HTML 模板和 TypeScript 文件中使用管道，让我们来学习纯管道和不纯管道。

它是纯的还是不纯的？

Angular 管道有两种类型：带有`pure false`标志。纯管道和不纯管道之间的区别在于它们的更新行为以及 Angular 如何对它们运行变更检测。

在创建纯管道时，你需要使用纯转换函数。**纯函数**是一个在给定相同输入时总是返回相同输出的函数。

Angular 仅在检测到输入值的纯变化时才运行纯管道。纯变化是指对原始值（数字、字符串、布尔值、bigint、symbol、undefined 和 null）的更改，或者当提供一个新引用对象（日期、数组、函数或对象）时。对引用对象的更改不被视为纯变化。因此，如果你有一个接受数组作为值的纯管道，更新数组将不会触发管道，因为这是一种不纯的变化。当你使用新数组分配属性时，管道将运行，因为它接收到了一个新的引用对象，这被视为纯变化。

当运行纯管道时，Angular 会跳过对引用对象的更新，因为检测纯变化比在对象上执行深度检查要快得多；正因为如此，Angular 可以快速确定你的管道是否需要再次执行，或者管道是否可以跳过。如果 Angular 必须在每个变更检测周期中执行深度检查或运行你的管道，这将极大地影响应用程序的性能。

因此，记住当你使用带有引用对象的纯管道时，除非你知道自己在做什么，否则你并不总是能得到你期望的结果。例如，假设你有一个仪表盘数组和一个管道，它只过滤数组以包括像这样的活动仪表盘：

```js
<div *ngFor="let dashboard of dashboards | active">
    {{dashboard.name}}
</div>
```

现在，当使用`push`更新仪表盘数组时，管道将不会运行，因为仪表盘数组的引用没有改变。如果你使用新数组分配仪表盘的属性，引用将改变，Angular 的变更检测将触发活动管道并按预期过滤结果。

当使用不纯的管道时，Angular 会每次检测到变化时执行管道。这意味着 Angular 会每次按键或鼠标移动时运行管道。不纯的管道可能很有用，并且会按预期更新引用对象，但使用不纯管道时要小心，因为它们可能会显著减慢您的应用程序。当您使用不纯的管道时，您希望将组件的变更检测策略设置为 `OnPush`，这样您的管道就不会被频繁执行。当组件的变更检测设置为 `OnPush` 时，变更检测仅在组件接收到新的输入值或您手动触发时才会运行。尽可能地将变更检测策略设置为 `OnPush` 是一个好习惯，因为它将有助于提高您应用程序的性能。

现在您已经了解了纯管道和不纯管道之间的区别，让我们更多地了解 `AsyncPipe`，因为它是 Angular 提供给我们最重要的内置管道。在了解 `AsyncPipe` 之后，我们将学习如何创建自定义管道。

使用 AsyncPipe

最强大的内置管道是 `AsyncPipe`。尽管 `AsyncPipe` 是一个不纯的管道，但建议尽可能多地使用它来处理在模板中使用的可观察对象和承诺结果。使用 `AsyncPipe` 相比在组件类中使用订阅处理可观察对象具有优势。

首先，`AsyncPipe` 会自动订阅和取消订阅可观察对象，更重要的是，它会自动取消订阅。这一点非常重要，因为它可以防止内存泄漏。如果您没有正确清理订阅，最终会导致内存泄漏，您的应用程序将开始变慢，并显示出意外的行为，甚至可能崩溃。

为了演示 `AsyncPipe`，我们将在组件类中使用 RxJS 的 `interval` 操作符创建一个可观察对象，如下所示：

```js
timer: Observable<number> = interval(2000);
```

这个 `interval` 可观察对象将每 2 秒发出下一个索引，从 `0` 开始。因此，2 秒后，可观察对象发出 `0`，再过 2 秒，可观察对象发出 `1`，依此类推。

我们可以在组件类内部订阅 `interval` 可观察对象，并将结果分配给我们在模板中显示的组件属性：

```js
this.timer.subscribe((n) => { this.count = n; });
```

接下来，您可以在模板中使用 `count` 属性：

```js
<div>{{count}}</div>
```

如果您为组件使用 `OnPush` 变更检测，您需要每次可观察对象接收到新值时手动调用变更检测。否则，模板中的 `count` 属性将不会更新。在上述方法中，您还必须添加逻辑，在组件销毁或可观察属性分配给另一个可观察对象时取消订阅您的可观察对象。现在，让我们看看如何使用 `AsyncPipe` 在我们的 HTML 模板中利用间隔可观察对象：

```js
<div>{{timer | async}}</div>
```

如你所见，使用`AsyncPipe`很简单。你声明使用可观察者分配的属性（在我们的例子中，它被命名为`timer`），并在其旁边添加`AsyncPipe`。每当间隔可观察者发射新的值时，它都会反映在我们的模板中。不需要额外的属性来保存可观察者的结果，不需要取消订阅，也没有内存泄漏的风险！即使你使用新的可观察者分配`timer`属性，异步管道也会自动取消订阅旧的可观察者并订阅新的可观察者。

当使用异步管道时，建议使用`OnPush`变更检测，因为`AsyncPipe`是非纯的。`AsyncPipe`的另一个优点是，当管道接收到新值时，它会自动标记需要检查更改的组件模板。当你将变更检测策略设置为`OnPush`时，这很有用。当使用常规的可观察者订阅时，如果你使用`OnPush`策略，HTML 模板不会被标记为检查更改，这意味着你必须在订阅接收到新值后手动触发变更检测。

现在你对`AsyncPipe`以及为什么它是一个如此强大的工具有了更多的了解，让我们来探索如何创建你自己的管道。

构建你自己的管道

在我们的 Nx 单仓库中创建管道时，我们将在`util`库中这样做。在我们的例子中，我们将创建一个简单的管道，该管道将使用指定的因子乘以一个数字。创建此管道的正确位置是在共享域下的一个库中，并且是`util`类型。使用我们的自定义生成器创建一个名为`common-pipes`的新库，并选择**shared**作为其域，**util**作为其类型。

当你的新库生成后，按照以下步骤生成自定义管道：

1.  关闭并重新打开 VSCode，以确保你的新库包含在 Nx 图示中。

1.  右键单击此位置的文件夹：`libs\shared\util\common-pipes\src\lib`，并选择**Nx 生成**。

1.  输入`pipe`并点击**@nx/angular –** **pipe**。

1.  在**名称**字段中输入`multiply`。

1.  点击**显示** **所有选项**。

1.  选择**独立**复选框。

1.  在右上角点击**生成**。

1.  当组件生成后，将以下内容添加到库中的`index.ts`文件：

    ```js
    export * from './lib/multiply.pipe';
    ```

之后，你可以使用你的管道，但在使用之前，让我们添加一些逻辑。

当你打开`multiply.pipe.ts`文件时，你会看到 Nx 生成了一个`MultiplyPipe`类，该类实现了`PipeTransform`接口。该类还装饰了`@Pipe()`装饰器，其中独立标志设置为`true`，管道名称`multiply`被定义。在这个装饰器内部，你可以添加纯标志；你的管道默认是纯的。只有当你想创建一个非纯管道时，你才需要添加纯标志并设置为`false`。在我们的例子中，我们将创建一个简单、纯的管道，因此不需要将纯标志添加到装饰器中。

Nx 还向类中添加了一个转换函数，以符合`PipeTransform`接口。转换函数是管道的“难点”，在这里你添加你的转换逻辑。你可以调整转换函数如下：

```js
transform(value: number, multiplier = 2): number {
  return value * multiplier;
}
```

如你所见，我们有一个值和一个乘数函数参数。值参数是我们在我们 HTML 模板的管道左侧声明的。`multiplier`是我们冒号后面的参数。我们给`multiplier`赋予了一个默认值`2`，所以在模板中声明管道时它是可选的。

当你想使用管道时，首先需要导入它；如果你使用 NgModules，管道需要在 NgModules 实例中导入；如果你使用独立组件，就像我们在这里做的那样，你必须将管道导入到你想要使用它的组件中。在你将管道导入到独立组件后，你可以在模板中使用它，如下所示：

```js
<div>{{10 | multiply}}</div>
```

如果你想为管道提供一个自定义的乘数值，你可以使用以下语法：

```js
<div>{{10 | multiply: 5}}</div>
```

如果你想在管道中添加更多参数，你可以在你的转换函数中添加更多参数来实现。假设你想在`multiply`管道中添加另一个乘数。你可以按照以下方式添加：

```js
transform(value: number, multiplier = 2, additional = 1): number {
  return value * multiplier * additional;
}
```

现在，你可以在你的 HTML 模板中使用以下语法：

```js
<div>{{10 | multiply: 5: 10}}</div>
```

我们的示例管道很简单，但你可以在你的转换函数中添加任何你想要的逻辑。只需确保在创建纯管道时使用纯函数，即当给定相同的输入时返回相同值且不影响任何其他代码的函数。当你创建不纯的管道时，确保不要添加耗时或资源密集型的代码，因为这会负面影响你的应用程序性能。以下是如何创建不纯管道的示例：

```js
@Pipe({
  name: 'multiply',
  standalone: true,
  pure: false,
})
```

现在你已经知道管道用于转换值。Angular 提供了内置的管道用于常见的转换和处理异步值。你知道纯管道和不纯管道之间的区别，并且可以创建你自己的自定义管道。为了完成本章，我们将学习关于 Angular 动画的内容。

创建和重用令人惊叹的动画

在前面的章节中，你已经看到了如何使用指令操作 DOM 元素，以及如何使用管道转换模板值；在本节中，你将学习如何使用内置的动画模块为你的 HTML 元素和组件创建动画。

首先，你必须启用动画模块。为此，请转到你的 Nx 单仓库中`expenses-registration application`应用程序下的`app.config.ts`。在`app.config.ts`中，你会在`bootstrapApplication`函数内的`main.ts`中找到`appConfig`对象。要启用动画模块，请在`appConfig`对象的提供者数组中添加`provideAnimations()`函数，如下所示：

```js
provideAnimations(),
```

如果你正在使用基于 NgModule 的应用程序，你需要在想要使用动画的 NgModule 中导入 `BrowserAnimationsModule`。在添加了 `provideAnimations` 函数或 `BrowserAnimationsModule` 之后，你可以在组件内部开始添加动画。为了演示动画，让我们在 `common-components` 库中创建一个可选择的标签组件。使用 Nx 生成器创建一个组件，命名为 `selectable-label`，为项目选择 `common-components` 库，勾选 `OnPush`。当组件生成后，在 `common-components` 库的 `index.ts` 中添加以下导出：

```js
export * from './lib/selectable-label/selectable-label.component';
```

现在，将以下代码添加到 `component` 类中：

```js
@Input() labelText!: string;
@Input() get selected() {
  return this._selected;
}
set selected(selected) {
  this._selected = selected;
  this.animationState = selected ? ‹selected› : ‹deselected›;
}
@Output() selectedChange = new EventEmitter<boolean>();
private _selected = false;
animationState = 'deselected';
onSelectionChanged() {
  this.selected = !this.selected;
  this.selectedChange.emit(this.selected);
}
```

将以下 CSS 添加到 SCSS 文件中：

```js
span {
  color: white; background-color: #455b66;
  padding: 5px 15px; border-radius: 15px; cursor: pointer;
}
```

将以下 HTML 添加到 HTML 文件中：

```js
<span (click)="onSelectionChanged()">{{labelText}}</span>
```

现在我们已经创建了一个简单的标签组件，让我们创建我们的动画。首先，在组件装饰器内部添加 `animations` 数组，如下所示：

```js
@Component({ ………, animations: []})
```

在这个 `animations` 数组内部添加组件的动画。在数组中添加以下动画：

```js
trigger('selectedState', [
  state('selected', style({ backgroundColor: '#382632' })),
  state('deselected', style({ backgroundColor: '#455b66'})),
  transition('selected <=> deselected', [animate('2s')])
])
```

这是一个简单的动画，将背景颜色从十六进制颜色 `#382632` 变换到 `#455b66`，并需要 2 秒来完成过渡。现在让我们逐行检查我们添加的内容。

动画触发器

我们动画的起始触发器：

```js
selectedState. The animation metadata array contains state and transition functions that define the behavior of our animation. Let’s explore these functions in more detail.
Animation state
Inside our animation metadata array, by using our `selectedState` trigger, you’ll find the state functions:

```

state('selected', style({ backgroundColor: '#382632' })),

state('deselected', style({ backgroundColor: '#455b66'})),

```js

 Your animation can have as many state functions as are needed. You can define animations without a state or with many state functions. Each state defines a state your animation can transition to. If you have an animation without any state functions, you can define the style changes inside the transition functions, and the animation will still run, but after it finishes, the HTML element will be as it was before the animation started. When you have a state, you can transition an element from one state to another. When the animation transitions to a specific animation state, the HTML element will stay styled as the state defined it until the animation transitions the HTML element to another animation state.
Each animation state receives a name to indicate the state and a style function to define the style properties with which to transition to for the specific animation state. It is important to note that the styles are indicated with camel case, so no hyphens are used. The background color CSS property becomes `backgroundColor`.
In our example, we have two states: `selected` and `deselected`. Inside the component class, we also have a property called `animationState`, which holds the current state of our animation. By default, it’s set to `deselected`. When we click on our label, we will set the `selected` property, and inside the setter of our `selected` property, we will set `animationState` to its proper value.
Animation transition
After our state functions, we define a transition function inside the animation metadata array:

```

transition('selected <=> deselected', [animate('2s')])

```js

 Transition functions specify how to transition from one animation state to another and they can take three parameters: the transition statement, an animation metadata array, and an object that can define a delay for your transition.
Transition expression
The `transition` expression indicates what state transition to cover when using a specific `transition` function. The syntax of the `transition` expression reads from left to right and uses arrows to indicate the state transition. For example, `selected => deselected` would target state transitions from `selected` to `deselected`, `deselected => selected` would target state transitions from `deselected` to `selected`, and `selected <=> deselected` would target state transitions from `selected` to `deselected` and from `deselected` to `selected`. You can also use an asterisk inside your selection expression. The asterisk symbol is a wildcard and stands for every state. For example, `* => deselected` would trigger the transition if any state transfers to `deselected`, and `* => *` would trigger if any state transfers to another.
The `transition` expression has a few more special selectors that are similar to the asterisk. For example, you can use `:enter` and `:leave` as transition expressions. The `:enter` expression will be applied to an element entering the DOM, and `:leave` targets elements that are removed from the DOM. The `:enter` and `:leave` expressions do not care about the animation state an HTML element currently has. These two expressions are useful when combined with the `*ngIf` or `*``ngFor` directives.
You can also use `:increment` and `:decrement` as expressions. The `:increment` and `:decrement` expressions will trigger the animation when the value inside the HTML element is a number, and it gets incremented or decremented.
Animation metadata array
After the transition expression, the `transition` function also takes an animation metadata array as input. In our example, we only declared an `animate` function inside, which indicates how long the transition takes, which is 2 seconds in our case. The animate function can also take a `style` function like this:

```

animate('2s', style({ color: 'red' })),

```js

 The `style` function inside the `animate` function is useful if you have no states for your animation or if you want to perform additional animations during the transition that will not last once the state transition is finished.
You can also define keyframes for your `animate` function. With keyframes, you can indicate different stages of the animation; the offset defines how far into the animation you are, with `0` defining the start and `1` defining the end of the animation:

```

animate('2s', keyframes([

style({ backgroundColor: ‹blue›, offset: 0}),

style({ backgroundColor: ‹red›, offset: 0.8}),

style({ backgroundColor: ‹#754600›, offset: 1.0})])),

```js

 Besides the `animate` function, the animation metadata array inside the transition function can take more configurations. The most commonly used are `group` and `sequence`.
The `sequence` function is used to trigger multiple animate steps one after another. These can be steps that lead to the result of the state you are transitioning to or just additional animation steps that are not included in your animation state:

```

sequence([

animate(‹2s›, style({ backgroundColor: ‹#382632› })),

animate(‹2s›, style({ color: ‹orange› }))

])

```js

 The `group` function is used to group different `animate` functions. When you group `animate` functions, they will be executed simultaneously during the transition. Each group is executed one after another:

```

group([

animate(‹2s›, style({ color: ‹white› })),

animate(‹2s›, style({ backgroundColor: ‹#455b66› })),

])

group([

animate(‹2s›, style({ fontSize: ‹24px› })),

animate(‹2s›, style({ opacity: ‹0.5› })),

])

```js

 Now that you know how to define animations inside your component class, let’s examine how you can add those animations to HTML elements inside your template.
Adding animations to your template
Adding animations inside your HTML template is pretty straightforward. To add our animation to the selectable label, we need to add the following code line on the span tag:

```

[@selectedState]="animationState"

```js

 On the left-hand side, you define the animation trigger, which is preceded by an `@` sign and enclosed by square brackets. On the right-hand side, you declare the animation state; in our case, we used the `animationState` property from our component class for this, but you can also add ternary operations.
You can also trigger events when the animation starts or finishes by adding the following code to your HTML tag with the animation defined on it:

```

(@selectedState.start)="onAnimationEvent($event)"

(@selectedState.done)="onAnimationEvent($event)"

```js

 Lastly, you can disable an animation within HTML child elements based on a Boolean value and the `@.disabled` animation control binding like this:

```

<div [@.disabled]="isDisabled"> ……… </div>

```js

 In the preceding example, all animations inside `div` with `@.disabled` are disabled if the `isDisabled` property is `true`.
Now that you know how to create and use animations, let’s explore how you can reuse animations.
Reusing animations
Creating animations, especially complex ones, can be a lot of work. If you want to apply them within multiple components, you don’t want to add duplicated code and create the same animation multiple times. To reuse animations, you can create an `animations.ts` file in your application or a `utils` library, depending on your use case. You can create exported functions in this file. Here is an example of our animation as a reusable animation:

```

export function selectedAnimation(): AnimationTriggerMetadata {

return trigger('selectedState', [

state(‹selected›, style({ backgroundColor: ‹#382632› })),

state(‹deselected›, style({ backgroundColor: ‹#455b66›})),

transition(‹selected <=> deselected›,[animate(‹2s›)]),])

}

```js

 Now, inside the component class, you can define the animation as follows:

```

@Component({

………

animations: [selectedAnimation()],

})

```js

 This maintains readability in your component and allows you to use the animation inside multiple components without creating it again.
Summary
In this chapter, you learned the difference between structural and attribute directives. You learned how to add and remove DOM elements using directives. You’ve learned to change the styling and behavior of DOM elements using directives, and you now know how to listen out for the events of host elements. You can use built-in directives and create your own. Besides directives, you learned how to transform values using Angular pipes. You learned about pure and impure pipes and how they can be made and impact your performance. Lastly, you made your own Angular animation and learned about animation triggers, states, and transform functions. Now, you know how to declare animations in your templates and how to reuse them throughout your application.
In the next chapter, you will learn about reactive and template-driven forms.

```

```js

```

```js

```

```js

```
