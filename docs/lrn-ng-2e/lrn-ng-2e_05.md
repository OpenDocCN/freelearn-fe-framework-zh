# 第五章：通过管道和指令增强我们的组件

在之前的章节中，我们构建了几个组件，借助输入和输出属性在屏幕上呈现数据。我们将利用本章的知识，通过使用指令和管道，将我们的组件提升到一个新的水平。简而言之，管道为我们提供了在模板中绑定的信息进行解析和转换的机会，而指令允许我们进行更有野心的功能，我们可以访问宿主元素的属性，并绑定我们自己的自定义事件监听器和数据绑定。

在本章中，我们将：

+   全面了解 Angular 的内置指令

+   探讨如何使用管道来优化我们的数据输出

+   看看如何设计和构建我们自己的自定义管道和指令

+   利用内置对象来操作我们的模板

+   将所有前述主题和更多内容付诸实践，以构建一个完全交互式的待办事项表

# Angular 中的指令

Angular 将指令定义为没有视图的组件。事实上，组件是具有关联模板视图的指令。之所以使用这种区别，是因为指令是 Angular 核心的一个重要部分，每个指令（普通指令和组件指令）都需要另一个存在。指令基本上可以影响 HTML 元素或自定义元素的行为和显示其内容。

# 核心指令

让我们仔细研究一下框架的核心指令，然后您将在本章后面学习如何构建自己的指令。

# NgIf

正如官方文档所述，`ngIf`指令根据表达式删除或重新创建 DOM 树的一部分。如果分配给`ngIf`指令的表达式求值为`false`，则该元素将从 DOM 中移除。否则，元素的克隆将重新插入 DOM 中。我们可以通过利用这个指令来增强我们的倒计时器，就像这样：

```ts
<timer> [seconds]="timeout"></timer>
<p *ngIf="timeout === 0">Time up!</p>
```

当我们的计时器达到 0 时，将在屏幕上呈现显示“时间到！”文本的段落。您可能已经注意到了在指令前面加上的星号。这是因为 Angular 将标有`ngIf`指令的 HTML 控件（以及其所有 HTML 子树，如果有的话）嵌入到`<ng-template>`标记中，稍后将用于在屏幕上呈现内容。涵盖 Angular 如何处理模板显然超出了本书的范围，但让我们指出，这是 Angular 提供的一种语法糖，作为其他更冗长的基于模板标记的语法的快捷方式。

也许您想知道使用`*ngIf="conditional"`在屏幕上呈现一些 HTML 片段与使用`[hidden]="conditional"`有什么区别。前者将克隆并注入模板化的 HTML 片段到标记中，在条件评估为`false`时从 DOM 中删除它，而后者不会从 DOM 中注入或删除任何标记。它只是设置带有该 DOM 属性的已存在的 HTML 片段的可见性。

# NgFor

`ngFor`指令允许我们遍历集合（或任何其他可迭代对象），并将其每个项目绑定到我们选择的模板，我们可以在其中定义方便的占位符来插入项目数据。每个实例化的模板都作用域限定在外部上下文中，我们可以访问其他绑定。假设我们有一个名为`Staff`的组件：它具有一个名为 employees 的字段，表示一个`Employee`对象数组。我们可以这样列出这些员工和职位：

```ts
<ul>
 <li *ngFor="let employee of employees">
 Employee {{ employee.name }}, {{ employee.position }}
 </li>
</ul>
```

正如我们在提供的示例中看到的，我们将从每次循环中获取的可迭代对象中的每个项目转换为本地引用，以便我们可以轻松地在我们的模板中绑定这个项目。需要强调的是，表达式以关键字`let`开头。

该指令观察底层可迭代对象的更改，并将根据项目在集合中添加、删除或重新排序而添加、删除或排序呈现的模板。

# 高级循环

除了只循环列表中的所有项目之外，还可以跟踪其他可用属性。每个属性都可以通过在声明项目后添加另一个语句来使用：

```ts
<div *ngFor="let items of items; let property = property">{{ item }}</div>
```

**First/last**，这是一个布尔值，用于跟踪我们是否在循环中的第一个或最后一个项目上，如果我们想要以不同的方式呈现该项目。可以通过以下方式访问它：

```ts
<div *ngFor="let item of items; let first = first">
 <span [ngClass]="{ 'first-css-class': first, 'item-css-class' : !first }">
 {{ item }}
 </span>
</div>
```

**Index**，是一个数字，告诉我们我们在哪个索引上；它从 0 开始。

**Even/odd**是一个布尔值，指示我们是否在偶数或奇数索引上。

**TrackBy**，要解释`trackBy`做什么，让我们首先谈谈它试图解决的问题。问题是，`*ngFor`指向的数据可能会发生变化，元素可能会被添加或删除，甚至整个列表可能会被替换。对于添加/删除元素的天真方法是对所有这些元素在 DOM 树上进行创建/删除。如果使用相同的天真方法来显示新列表而不是我们用来显示这个旧列表，那将是非常昂贵和缓慢的。Angular 通过将 DOM 元素保存在内存中来处理这个问题，因为创建是昂贵的。在内部，Angular 使用称为对象标识的东西来跟踪列表中的每个项目。然而，`trackBy`允许您从对象标识更改为项目上的特定属性。默认的对象标识在大多数情况下都很好，但是如果您开始遇到性能问题，请考虑更改`*ngFor`应查看的项目的属性，如下所示：

```ts
@Component({
 template : `
 <*ngFor="let item of items; trackBy: trackFunction">{{ item }}</div>
 `
})
export class SomeComponent {
 trackFunction(index, item) {
 return item ? item.id : undefined;
 }
}
```

# Else

Else 是 Angular 4.0 的一个新构造，并且是一个简写，可以帮助您处理条件语句。想象一下，如果您有以下内容：

```ts
<div *ngIf="hero">
 {{ hero.name }}
</div>
<div *ngIf="!hero">
 No hero set
</div>
```

我们在这里的用例非常清楚；如果我们设置了一个人，那么显示它的名字，否则显示默认文本。我们可以使用`else`以另一种方式编写这个：

```ts
<div *ngIf="person; else noperson">{{person.name}}</div>
<div #noperson>No person set</div>
```

这里发生的是我们如何定义我们的条件：

```ts
person; else noperson
```

我们说如果`person`已设置，那么继续，如果没有显示模板`noperson`。 `noperson`也可以应用于普通的 HTML 元素以及`ng-template`。

# 应用样式

在您的标记中应用样式有三种方法：

+   插值

+   NgStyle

+   NgClass

# 插值

这个版本是关于使用花括号并让它们解析应该应用什么类/类。您可以编写一个看起来像这样的表达式：

```ts
<div class="item {{ item.selected ? 'selected' : ''}}"
```

这意味着如果您的项目具有选定的属性，则应用 CSS 类 selected，否则应用空字符串，即没有类。虽然在许多情况下这可能足够，但它也有缺点，特别是如果需要应用多个样式，因为有多个需要检查的条件。

插值表达式在性能方面被认为是昂贵的，通常是不鼓励使用的。

# NgStyle

正如你可能已经猜到的那样，这个指令允许我们通过评估自定义对象或表达式来绑定 CSS 样式。我们可以绑定一个对象，其键和值映射 CSS 属性，或者只定义特定属性并将数据绑定到它们：

```ts
<p [ngStyle]="{ 'color': myColor, 'font-weight': myFontWeight }">
 I am red and bold
</p>
```

如果我们的组件定义了`myColor`和`myFontWeight`属性，分别具有`red`和`bold`的值，那么文本的颜色和粗细将相应地改变。该指令将始终反映组件内所做的更改，我们还可以传递一个对象，而不是按属性基础绑定数据：

```ts
<p [ngStyle]="myCssConfig">I am red and bold</p>
```

# NgClass

与`ngStyle`类似，`ngClass`允许我们以一种方便的声明性语法在 DOM 元素中定义和切换类名。然而，这种语法有其自己的复杂性。让我们看看这个例子中可用的三种情况：

```ts
<p [ngClass]="{{myClassNames}}">Hello Angular!</p>
```

例如，我们可以使用字符串类型，这样如果`myClassNames`包含一个由空格分隔的一个或多个类的字符串，所有这些类都将绑定到段落上。

我们也可以使用数组，这样每个元素都会被添加。

最后但同样重要的是，我们可以使用一个对象，其中每个键对应于由布尔值引用的 CSS 类名。标记为`true`的每个键名将成为一个活动类。否则，它将被移除。这通常是处理类名的首选方式。

`ngClass`还有一种替代语法，格式如下：

```ts
[ngClass]="{ 'class' : boolean-condition, 'class2' : boolean-condition-two }"
```

简而言之，这是一个逗号分隔的版本，在条件为`true`时将应用一个类。如果有多个条件为`true`，则可以应用多个类。如果在更现实的场景中使用，它会看起来像这样：

```ts
<span [ngClass] ="{
 'light' : jedi.side === 'Light',
 'dark' : jedi.side === 'Dark'
}">
{{ jedi.name }}
</span>
```

生成的标记可能如下，如果`jedi.side`的值为`light`，则将 CSS 类 light 添加到 span 元素中：

```ts
<span class="light">Luke</span>
```

# NgSwitch、ngSwitchCase 和 ngSwitchDefault

`ngSwitch`指令用于根据显示每个模板所需的条件在特定集合内切换模板。实现遵循几个步骤，因此在本节中解释了三个不同的指令。

`ngSwitch`将评估给定的表达式，然后切换和显示那些带有`ngSwitchCase`属性指令的子元素，其值与父`ngSwitch`元素中定义的表达式抛出的值匹配。需要特别提到带有`ngSwitchDefault`指令属性的子元素。该属性限定了当其`ngSwitchCase`兄弟元素定义的任何其他值都不匹配父条件表达式时将显示的模板。

我们将在一个例子中看到所有这些：

```ts
<div [ngSwitch]="weatherForecaseDay">
 <ng-template ngSwitchCase="today">{{weatherToday}}</ng-template>
 <ng-template ngSwitchCase="tomorrow">{{weatherTomorrow}}</ng-template>
 <ng-template ngSwitchDefault>
 Pick a day to see the weather forecast
 <ng-template>
</div>
```

父`[ngSwitch]`参数评估`weatherForecastDay`上下文变量，每个嵌套的`ngSwitchCase`指令将针对其进行测试。我们可以使用表达式，但我们希望将`ngSwitchCase`包装在括号中，以便 Angular 可以正确地将其内容评估为上下文变量，而不是将其视为文本字符串。

`NgPlural`和`NgPluralCase`的覆盖范围超出了本书的范围，但基本上提供了一种方便的方法来呈现或删除与开关表达式匹配的模板 DOM 块，无论是严格的数字还是字符串，类似于`ngSwitch`和`ngSwitchWhen`指令的方式。

# 使用管道操作模板绑定

因此，我们看到了如何使用指令根据我们的组件类管理的数据来呈现内容，但是还有另一个强大的功能，我们将在日常实践中充分利用 Angular。我们正在谈论管道。

管道允许我们在视图级别过滤和引导我们表达式的结果，以转换或更好地显示我们绑定的数据。它们的语法非常简单，基本上由管道符号分隔的要转换的表达式后面跟着管道名称（因此得名）：

```ts
@Component({
 selector: 'greeting',
 template: 'Hello {{ name | uppercase }}'
})
export class GreetingComponent{ name: string; }
```

在前面的例子中，我们在屏幕上显示了一个大写的问候语。由于我们不知道名字是大写还是小写，所以我们通过在视图级别转换名称的值来确保一致的输出。管道是可链式的，Angular 已经内置了各种管道类型。正如我们将在本章中进一步看到的，我们还可以构建自己的管道，以在内置管道不足以满足需求的情况下对数据输出进行精细调整。

# 大写/小写管道

大写/小写管道的名称就是它的含义。就像之前提供的示例一样，这个管道可以将字符串输出设置为大写或小写。在视图中的任何位置插入以下代码，然后自行检查输出：

```ts
<p>{{ 'hello world' | uppercase}}</p>  // outputs HELLO WORLD
<p>{{ 'wEIrD hElLo' | lowercase}}</p>  // outputs weird hello
```

# 小数、百分比和货币管道

数值数据可以有各种各样的类型，当涉及到更好的格式化和本地化输出时，这个管道特别方便。这些管道使用国际化 API，因此只在 Chrome 和 Opera 浏览器中可靠。

# 小数管道

小数管道将帮助我们使用浏览器中的活动区域设置定义数字的分组和大小。其格式如下：

```ts
number_expression | number[:digitInfo[:locale]]
```

在这里，`number_expression`是一个数字，`digitInfo`的格式如下：

```ts
{minIntegerDigits}.{minFractionDigits}-{maxFractionDigits}
```

每个绑定对应以下内容：

+   `minIntegerDigits`：要使用的整数位数的最小数字。默认为 1。

+   `minFractionDigits`：分数后的最小数字位数。默认为 0。

+   `maxFractionDigits`：分数后的最大数字位数。默认为 3。

请记住，每个数字和其他细节的可接受范围将取决于您的本地国际化实现。让我们尝试通过创建以下组件来解释这是如何工作的：

```ts
import { Component, OnInit } from  '@angular/core'; @Component({ selector:  'pipe-demo', template: ` <div>{{ no  |  number }}</div>   <!-- 3.141 --> <div>{{ no  |  number:'2.1-5' }}</div> <! -- 03.14114 --> <div>{{ no  |  number:'7.1-5' }}</div> <!-- 0,000,003.14114 -->
 <div>{{ no  |  number:'7.1-5':'sv' }}</div> <!-- 0 000 003,14114 -->
 ` }) export  class  PipeDemoComponent { no:  number  =  3.1411434344; constructor() { } }
```

这里有一个四种不同表达式的示例，展示了我们如何操作数字、分数以及区域设置。在第一种情况下，我们除了使用`number`管道之外没有给出任何指令。在第二个示例中，我们指定了要显示的小数位数和数字，通过输入`number: '2.1-5'`。这意味着我们在分数标记的左侧显示两个数字，右侧显示 5 个数字。因为左侧只有 3 个数字，我们需要用零来填充。右侧我们只显示 5 位小数。在第三个示例中，我们指示它显示 7 个数字在分数标记的左侧，右侧显示 5 个数字。这意味着我们需要在左侧填充 6 个零。这也意味着千位分隔符被添加了。我们的第四个示例演示了区域设置功能。我们看到显示的结果是千位分隔符的空格字符，小数点的逗号。

不过有一件事要记住；要使区域设置起作用，我们需要在根模块中安装正确的区域设置。原因是 Angular 只有从一开始就设置了 en-US 区域设置。不过添加更多区域设置非常容易。我们需要将以下代码添加到`app.module.ts`中：

```ts
import { BrowserModule } from  '@angular/platform-browser'; import { NgModule } from  '@angular/core'; import { AppComponent } from  './app.component'; import { PipeDemoComponent } from  "./pipe.demo.component"; 
import { registerLocaleData } from  '@angular/common'; import localeSV from '@angular/common/locales/sv'; 
registerLocaleData(localeSV**);** 
@NgModule({
  declarations: [ AppComponent, PipeDemoComponent ],
 imports: [ BrowserModule
 ],
 providers: [], bootstrap: [AppComponent] })
export  class  AppModule { }
```

# 百分比管道

百分比管道将数字格式化为本地百分比。除此之外，它继承自数字管道，以便我们可以进一步格式化输出，以提供更好的整数和小数大小和分组。它的语法如下：

```ts
number_expression | percent[:digitInfo[:locale]]
```

# 货币管道

这个管道将数字格式化为本地货币，支持选择货币代码，如美元的 USD 或欧元的 EUR，并设置我们希望货币信息显示的方式。它的语法如下：

```ts
number_expression | currency[:currencyCode[:display[:digitInfo[:locale]]]]
```

在前面的语句中，`currencyCode`显然是 ISO 4217 货币代码，而`display`是一个字符串

可以是`code`，假设值为`symbol`或`symbol-narrow`。值`symbol-narrow`指示是否使用货币符号（例如，$）。值`symbol`指示在输出中使用货币代码（例如 USD）。与小数和百分比管道类似，我们可以通过`digitInfo`值格式化输出，还可以根据区域设置格式化。

在下面的示例中，我们演示了所有三种形式：

```ts
import { Component, OnInit } from  '@angular/core'; 
@Component({ selector:  'currency-demo', template: ` <p>{{ 11256.569  |  currency:"SEK":'symbol-narrow':'4.1-2' }}</p> <!--kr11,256.57 --> <p>{{ 11256.569  |  currency:"SEK":'symbol':'4.1-3' }}</p> <!--SEK11,256.569 --> <p>{{ 11256.569  |  currency:"SEK":'code' }}</p> <!--SEK11,256.57 --> `
})
export  class  CurrencyDemoComponent { constructor() { } }  
```

# 切片管道

这个管道的目的相当于`Array.prototype.slice()`和`String.prototype.slice()`在减去集合列表、数组或字符串的子集（切片）时所起的作用。它的语法非常简单，遵循与前述`slice()`方法相同的约定：

```ts
expression | slice: start[:end]
```

基本上，我们配置一个起始索引，我们将从中开始切片项目数组或字符串的可选结束索引，当省略时，它将回退到输入的最后索引。

开始和结束参数都可以取正值和负值，就像 JavaScript 的`slice()`方法一样。请参考 JavaScript API 文档，了解所有可用场景的详细情况。

最后但并非最不重要的是，请注意，在操作集合时，返回的列表始终是副本，即使所有元素都被返回。

# 日期管道

你一定已经猜到了，日期管道根据请求的格式将日期值格式化为字符串。格式化输出的时区将是最终用户机器的本地系统时区。它的语法非常简单：

```ts
date_expression | date[:format[:timezone[:locale]]]
```

表达式输入必须是一个日期对象或一个数字（自 UTC 纪元以来的毫秒数）。格式参数是高度可定制的，并接受基于日期时间符号的各种变化。为了我们的方便，一些别名已经被提供为最常见的日期格式的快捷方式：

+   '中等'：这相当于'yMMMdjms'（例如，对于 en-US，Sep 3, 2010, 12:05:08 PM）

+   '短'：这相当于'yMdjm'（例如，9/3/2010, 12:05 PM

对于 en-US）

+   'fullDate'：这相当于'yMMMMEEEEd'（例如，对于 en-US，Friday, September 3, 2010）

+   '长日期'：这相当于'yMMMMd'（例如，September 3, 2010）

+   '中等日期'：这相当于'yMMMd'（例如，对于 en-US，Sep 3, 2010）

+   '短日期'：这相当于'yMd'（例如，对于 en-US，9/3/2010）

+   '中等时间'：这相当于'jms'（例如，对于 en-US，12:05:08 PM）

+   '短时间'：这相当于'jm'（例如，对于 en-US，12:05 PM）

+   json 管道

# JSON 管道

JSON 可能是定义中最直接的管道；它基本上以对象作为输入，并以 JSON 格式输出它：

```ts
import { Component } from  '@angular/core'; 
@Component({
  selector:  'json-demo', template: ` {{ person | json **}}** 
 **<!--{ "name": "chris", "age": 38, "address": { "street": "Oxford Street", "city": "London" }** } --> `
})
export  class  JsonDemoComponent { person  = { name:  'chris', age:  38, address: { street:  'Oxford Street', city:  'London' }
 }

 constructor() { } }  
```

使用 Json 管道的输出如下：{ "name": "chris", "age": 38, "address": { "street": "Oxford Street", "city": "London" } }。这表明管道已将单引号转换为双引号，从而生成有效的 JSON。那么，我们为什么需要这个？一个原因是调试；这是一个很好的方式来查看复杂对象包含什么，并将其漂亮地打印到屏幕上。正如您从前面的字段'person'中看到的，它包含一些简单的属性，但也包含复杂的'address'属性。对象越深，json 管道就越好。

# i18n 管道

作为 Angular 对提供强大国际化工具集的坚定承诺的一部分，已经提供了一组针对常见 i18n 用例的管道。本书将只涵盖两个主要的管道，但很可能在将来会发布更多的管道。请在完成本章后参考官方文档以获取更多信息。

# i18nPlural 管道

`i18nPlural`管道有一个简单的用法，我们只需评估一个数字值与一个对象映射不同的字符串值，根据评估的结果返回不同的字符串。这样，我们可以根据数字值是零、一、二、大于*N*等不同的情况在我们的模板上呈现不同的字符串。语法如下：

```ts
expression | i18nPlural:mapping[:locale]
```

让我们看看这在你的组件类上的一个数字字段`jedis`上是什么样子的：

```ts
<h1> {{ jedis | i18nPlural:jediWarningMapping }} </h1>
```

然后，我们可以将这个映射作为我们组件控制器类的一个字段：

```ts
export class i18DemoComponent {
 jedis: number = 11;
 jediWarningMapping: any = {
 '=0': 'No jedis',
 '=1' : 'One jedi present',
 'other' : '# jedis in sight'
 }
}
```

我们甚至通过在字符串映射中引入`'#'`占位符来绑定表达式中评估的数字值。当找不到匹配的值时，管道将回退到使用键`'other'`设置的映射。

# i18nSelect 管道

`i18nSelect`管道类似于`i18nPlural`管道，但它评估的是一个字符串值。这个管道非常适合本地化文本插值或根据状态变化提供不同的标签，例如。例如，我们可以回顾一下我们的计时器，并以不同的语言提供 UI：

```ts
<button (click)="togglePause()">
 {{ languageCode | i18nSelect:localizedLabelsMap }}
</button>
```

在我们的控制器类中，我们可以填充`localizedLabelsMap`，如下所示：

```ts
export class TimerComponent {
 languageCode: string ='fr';
 localizedLabelsMap: any = {
 'en' : 'Start timer',
 'es' : 'Comenzar temporizador',
 'fr' : 'Demarrer une sequence',
 'other' : 'Start timer' 
 }
}
```

重要的是要注意，我们可以在除了本地化组件之外的用例中使用这个方便的管道，而是根据映射键和类似的东西提供字符串绑定。与`i18nPlural`管道一样，当找不到匹配的值时，管道将回退到使用`'other'`键设置的映射。

# 异步管道

有时，我们管理可观察数据或仅由组件类异步处理的数据，并且我们需要确保我们的视图及时反映信息的变化，一旦可观察字段发生变化或异步加载在视图渲染后完成。异步管道订阅一个可观察对象或承诺，并返回它发出的最新值。当发出新值时，异步管道标记组件以检查更改。我们将在第七章中返回这个概念，*使用 Angular 进行异步数据服务*。

# 将所有内容放在任务列表中

现在你已经学会了所有的元素，可以让你构建完整的组件，是时候把所有这些新知识付诸实践了。在接下来的页面中，我们将构建一个简单的任务列表管理器。在其中，我们将看到一个包含我们需要构建的待办事项的任务表。

我们还将直接从可用任务的积压队列中排队任务。这将有助于显示完成所有排队任务所需的时间，并查看我们工作议程中定义了多少任务。

# 设置我们的主 HTML 容器

在构建实际组件之前，我们需要先设置好我们的工作环境，为此，我们将重用在上一个组件中使用的相同的 HTML 样板文件。请将您迄今为止所做的工作放在一边，并保留我们在以前的示例中使用的`package.json`、`tsconfig.json`、`typings.json`和`index.html`文件。如果需要的话，随时重新安装所需的模块，并替换我们`index.html`模板中的 body 标签的内容：

```ts
<nav class="navbar navbar-default navbar-static-top">
 <div class="container">
 <div class="navbar-header">
 <strong class="navbar-brand">My Tasks</strong>
 </div>
 </div>
</nav>
<tasks></tasks>
```

简而言之，我们刚刚更新了位于我们新的`<tasks>`自定义元素上方的标题布局的标题，该元素替换了以前的`<timer>`。您可能希望更新`app.module.ts`文件，并确保将任务作为一个可以在我们模块之外可见的组件，输入到`exports`关键数组中：

```ts
@NgModule({
  declarations : [ TasksComponent ],
 imports : [ ],
 providers : [],
  exports : [ TasksComponent ]
})
export class TaskModule{}
```

让我们在这里强调一下，到目前为止，应用程序有两个模块：我们的根模块称为`AppModule`和我们的`TaskModule`。我们的根模块应该像这样导入我们的`TaskModule`：

```ts
@NgModule({
 imports : [
 BrowserModule,
    TaskModule
 ]
})
export class AppModule {}
```

# 使用 Angular 指令构建我们的任务列表表格

创建一个空的 `tasks.ts` 文件。您可能希望使用这个新创建的文件从头开始构建我们的新组件，并在其中嵌入我们将在本章后面看到的所有伴随管道、指令和组件的定义。

现实生活中的项目从未以这种方式实现，因为我们的代码必须符合“一个类，一个文件”的原则，利用 ECMAScript 模块将事物粘合在一起。第六章，*使用 Angular 组件构建应用程序*，将向您介绍构建 Angular 应用程序的一套常见最佳实践，包括组织目录树和不同元素（组件、指令、管道、服务等）的可持续方式。相反，本章将利用`tasks.ts`将所有代码包含在一个中心位置，然后提供我们现在将涵盖的所有主题的鸟瞰视图，而无需在文件之间切换。请记住，这实际上是一种反模式，但出于教学目的，我们将在本章中最后一次采用这种方法。文件中声明元素的顺序很重要。如果出现异常，请参考 GitHub 中的代码存储库。

在继续我们的组件之前，我们需要导入所需的依赖项，规范我们将用于填充表格的数据模型，然后搭建一些数据，这些数据将由一个方便的服务类提供。

让我们首先在我们的`tasks.ts`文件中添加以下代码块，导入我们在本章中将需要的所有标记。特别注意我们从 Angular 库中导入的标记。我们已经介绍了组件和输入，但其余的内容将在本章后面进行解释：

```ts
import { 
 Component,
 Input,
 Pipe,
 PipeTransform,
 Directive,
 OnInit,
 HostListener
 } from '@angular/core';
```

已经导入了依赖标记，让我们在导入的代码块旁边定义我们任务的数据模型：

```ts
/// Model interface
interface Task {
 name: string;
 deadline: Date;
 queued: boolean;
 hoursLeft: number;
}
```

`Task`模型接口的架构非常容易理解。每个任务都有一个名称，一个截止日期，一个字段用于通知需要运送多少单位，以及一个名为`queued`的布尔字段，用于定义该任务是否已被标记为在下一个会话中完成。

您可能会惊讶我们使用接口而不是类来定义模型实体，但当实体模型不需要实现方法或在构造函数或 setter/getter 函数中进行数据转换时，这是完全可以的。当后者不需要时，接口就足够了，因为它以简单且更轻量的方式提供了我们需要的静态类型。

现在，我们需要一些数据和一个服务包装类，以集合`Task`对象的形式提供这样的数据。在这里定义的`TaskService`类将起到作用，因此请在`Task`接口之后立即将其附加到您的代码中：

```ts
/// Local Data Service
class TaskService {
 public taskStore: Array<Task> = [];
 constructor() {
 const tasks = [
 {
 name : 'Code and HTML table',
 deadline : 'Jun 23 2015',
 hoursLeft : 1
 }, 
 {
 name : 'Sketch a wireframe for the new homepage',
 deadline : 'Jun 24 2016',
 hoursLeft : 2
 }, 
 {
 name : 'Style table with bootstrap styles',
 deadline : 'Jun 25 2016',
 hoursLeft : 1
 }
 ];

 this.taskStore = tasks.map( task => {
 return {
 name : task.name,
 deadline : new Date(task.deadline),
 queued : false,
 hoursLeft : task.hoursLeft 
 };
 })
 }
}
```

这个数据存储相当简单明了：它公开了一个`taskStore`属性，返回一个符合`Task`接口的对象数组（因此受益于静态类型），其中包含有关名称、截止日期和时间估计的信息。

现在我们有了一个数据存储和一个模型类，我们可以开始构建一个 Angular 组件，该组件将使用这个数据源来呈现我们模板视图中的任务。在您之前编写的代码之后插入以下组件实现：

```ts
/// Component classes
// - Main Parent Component
@Component({
 selector : 'tasks',
 styleUrls : ['tasks.css'],
 templateUrl : 'tasks.html'
})
export class TaskComponent {
 today: Date;
 tasks: Task[];
 constructor() {
 const TasksService: TaskService = new TasksService();
 this.tasks = tasksService.taskStore;
 this.today = new Date();
 }
}
```

正如您所见，我们通过引导函数定义并实例化了一个名为`TasksComponent`的新组件，选择器为`<tasks>`（我们在填充主`index.html`文件时已经包含了它，记得吗？）。这个类公开了两个属性：今天的日期和一个任务集合，它将在组件视图中的表中呈现，我们很快就会看到。为此，在其构造函数中实例化了我们之前创建的数据源，并将其映射到作为`Task`对象类型的模型数组，由任务字段表示。我们还使用 JavaScript 内置的`Date`对象的实例初始化了 today 属性，其中包含当前日期。

正如您所见，组件选择器与其控制器类命名不匹配。我们将在本章末深入探讨命名约定，作为第六章《使用 Angular 组件构建应用程序》的准备工作。

现在让我们创建样式表文件，其实现将非常简单明了。在我们的组件文件所在的位置创建一个名为`tasks.css`的新文件。然后，您可以使用以下样式规则填充它：

```ts
h3, p {
 text-align : center;
}

table {
 margin: auto;
 max-width: 760px;
}
```

这个新创建的样式表非常简单，以至于它可能看起来有点多余作为一个独立的文件。然而，在我们的示例中，这是展示组件元数据的`styleUrls`属性功能的好机会。

关于我们的 HTML 模板，情况大不相同。这一次，我们也不会在组件中硬编码我们的 HTML 模板，而是将其指向外部 HTML 文件，以更好地管理我们的呈现代码。请在与我们的主要组件控制器类相同的位置创建一个 HTML 文件，并将其保存为`tasks.html`。创建完成后，使用以下 HTML 片段填充它：

```ts
<div class="container text-center">
 <img src="assets/img/task.png" alt="Task" />
 <div class="container">
 <h4>Tasks backlog</h4>
 <table class="table">
 <thead>
 <tr>
 <th> Task ID</th>
 <th>Task name</th>
 <th>Deliver by</th>
 <th></th>
 <th>Actions</th>
 </tr>
 </thead>
 <tbody>
 <tr *ngFor="let task of tasks; let i = index">
 <th scope="row">{{i}}</th>
 <td>{{ task.name | slice:0:35 }}</td>
 <span [hidden]="task.name.length < 35">...</span>
 </td>
 <td>
 {{ task.deadline | date:'fullDate' }}
 <span *ngIf="task.deadline < today" 
 class="label label-danger">
 Due
 </span>
 </td>
 <td class="text-center">
 {{ task.hoursLeft }}
 </td>
 <td>[Future options...]</td>
 </tbody>
 </table>
</div> 

```

基本上，我们正在创建一个基于 Bootstrap 框架的具有整洁样式的表格。然后，我们使用始终方便的`ngFor`指令渲染所有任务，提取并显示我们在本章早些时候概述`ngFor`指令时解释的集合中每个项目的索引。

请看我们如何通过管道格式化任务名称和截止日期的输出，以及如何方便地显示（或不显示）省略号来指示文本是否超过了我们为名称分配的最大字符数，方法是将 HTML 隐藏属性转换为绑定到 Angular 表达式的属性。所有这些呈现逻辑都标有红色标签，指示给定任务是否在截止日期之前到期。

您可能已经注意到，这些操作按钮在我们当前的实现中不存在。我们将在下一节中修复这个问题，在我们的组件中玩转状态。回到第一章，*在 Angular 中创建我们的第一个组件*，我们提到了点击事件处理程序来停止和恢复倒计时，然后在第四章，*在我们的组件中实现属性和事件*中更深入地讨论了这个主题，我们涵盖了输出属性。让我们继续研究，看看我们如何将 DOM 事件处理程序与我们组件的公共方法连接起来，为我们的组件添加丰富的交互层。

# 在我们的任务列表中切换任务

将以下方法添加到您的`TasksComponent`控制器类中。它的功能非常基本；我们只是简单地切换给定`Task`对象实例的 queued 属性的值：

```ts
toggleTask(task: Task): void {
 task.queued = !task.queued;
}
```

现在，我们只需要将其与我们的视图按钮连接起来。更新我们的视图，包括在`ngFor`循环中创建的按钮中的点击属性（用大括号括起来，以便它充当输出属性）。现在，我们的`Task`对象将具有不同的状态，让我们通过一起实现`ngSwitch`结构来反映这一点：

```ts
<table class="table">
 <thead>
 <tr>
 <th>Task ID</th>
 <th>Task name</th>
 <th>Deliver by</th>
 <th>Units to ship</th>
 <th>Actions</th>
 </tr>
 </thead>
 <tbody>
 <tr *ngFor="let task of tasks; let i = index">
 <th scope="row">{{i}}
 <span *ngIf="task.queued" class="label label-info">Queued</span>
 </th>
 <td>{{task.name | slice:0:35}}
 <span [hidden]="task.name.length < 35">...</span>
 </td>
 <td>{{ task.deadline | date:'fullDate'}}
 <span *ngIf="task.deadline < today" class="label label-danger">Due</span>
 </td>
 <td class="text-center">{{task.hoursLeft}}</td>
 <td>
 <button type="button" 
 class="btn btn-default btn-xs"
 (click)="toggleTask(task)"
 [ngSwitch]="task.queued">
 <ng-template ngSwitchCase="false">
 <i class="glyphicon glyphicon-plus-sign"></i>
 Add
 </ng-template>
 <ng-template ngSwitchCase="true">
 <i class="glyphicon glyphicon-minus-sign"></i>
 Remove
 <ng-template>
 <ng-template ngSwitchDefault>
 <i class="glyphicon glyphicon-plus-sign"></i>
 Add
 </ng-template>
 </button>
 </td>
 </tbody>
</table>
```

我们全新的按钮可以在我们的组件类中执行“toggleTask（）”方法，将`Task`对象作为参数传递给`ngFor`迭代对应的对象。另一方面，先前的`ngSwitch`实现允许我们根据`Task`对象在任何给定时间的状态来显示不同的按钮标签和图标。

我们正在用从 Glyphicons 字体系列中获取的字体图标装饰新创建的按钮。这些图标是我们之前安装的 Bootstrap CSS 捆绑包的一部分，与 Angular 无关。请随意跳过使用它或用另一个图标字体系列替换它。

现在执行代码并自行检查结果。整洁，不是吗？但是，也许我们可以通过向任务列表添加更多功能来从 Angular 中获得更多的效果。

# 在我们的模板中显示状态变化

现在我们可以从表中选择要完成的任务，很好地显示出我们需要运送多少个单位的一些视觉提示将是很好的。逻辑如下：

+   用户审查表上的任务，并通过点击每个任务来选择要完成的任务

+   每次点击一行时，底层的`Task`对象状态都会发生变化，并且其布尔排队属性会被切换

+   状态变化立即通过在相关任务项上显示`queued`标签来反映在表面上

+   用户得到了需要运送的单位数量的提示信息和交付所有这些单位的时间估计

+   我们看到在表格上方显示了一排图标，显示了所有要完成的任务中所有单位的总和

这个功能将不得不对我们处理的`Task`对象集的状态变化做出反应。好消息是，由于 Angular 自己的变化检测系统，使组件完全意识到状态变化变得非常容易。

因此，我们的第一个任务将是调整我们的`TasksComponent`类，以包括一种计算和显示排队任务数量的方法。我们将使用这些信息来在我们的组件中渲染或不渲染一块标记，其中我们将通知我们排队了多少任务，以及完成所有任务需要多少累计时间。

我们类的新`queuedTasks`字段将提供这样的信息，我们将希望在我们的类中插入一个名为`updateQueuedTasks()`的新方法，该方法将在实例化组件或排队任务时更新其数值。除此之外，我们将创建一个键/值映射，以便稍后根据排队任务的数量使用`I18nPlural`管道来呈现更具表现力的标题头：

```ts
class TasksComponent {
 today: Date;
 tasks: Task[];
 queuedTasks: number;
 queuedHeaderMapping: any = {
 '=0': 'No tasks',
 '=1': 'One task',
 'other' : '# tasks'
 };

 constructor() {
 const TasksService: TasksService = new TasksService();
 this.tasks = tasksService.tasksStore;
 this.today = new Date();
 this.updateQueuedTasks();
 }

 toggleTask(task: Task) {
 task.queued = !task.queued;
 this.updateQueuedTasks();
 }

 private updateQueuedTasks() {
 this.queuedTasks = this.tasks
 .filter( task:Task => task.queued )
 .reduce((hoursLeft: number, queuedTask: Task) => {
 return hoursLeft + queuedTask.hoursLeft;
 }, 0)
 }
}
```

`updateQueuedTasks()`方法利用 JavaScript 的原生`Array.filter()`和`Array.reduce()`方法从原始任务集合属性中构建一个排队任务列表。应用于结果数组的`reduce`方法给出了要运送的单位总数。现在有了一个有状态的计算排队单位数量的方法，是时候相应地更新我们的模板了。转到`tasks.html`并在`<h4>Tasks backlog</h4>`元素之前注入以下 HTML 代码块。代码如下：

```ts
<div>
 <h3>
 {{queuedTasks | i18nPlural:queueHeaderMapping}}
 for today
 <span class="small" *ngIf="queuedTasks > 0">
 (Estimated time: {{ queuedTasks > 0 }})
 </span>
 </h3>
</div>
<h4>Tasks backlog</h4>
<!-- rest of the template remains the same -->
```

前面的代码块始终呈现一个信息性的标题，即使没有任务排队。我们还将该值绑定在模板中，并使用它通过表达式绑定来估算通过每个会话所需的分钟数。

我们正在在模板中硬编码每个任务的持续时间。理想情况下，这样的常量值应该从应用程序变量或集中设置中绑定。别担心，我们将在接下来的章节中看到如何改进这个实现。

保存更改并重新加载页面，然后尝试在表格上切换一些任务项目，看看信息如何实时变化。令人兴奋，不是吗？

# 嵌入子组件

现在，让我们开始构建一个微小的图标组件，它将嵌套在`TasksComponent`组件内部。这个新组件将显示我们大图标的一个较小版本，我们将用它来在模板上显示排队等待完成的任务数量，就像我们在本章前面描述的那样。让我们为组件树铺平道路，我们将在第六章中详细分析，*使用 Angular 组件构建应用程序*。现在，只需在之前构建的`TasksComponent`类之前包含以下组件类。

我们的组件将公开一个名为 task 的公共属性，我们可以在其中注入一个`Task`对象。组件将使用这个`Task`对象绑定，根据该任务的`hoursLeft`属性所需的会话次数，在模板中复制渲染的图像，这都是通过`ngFor`指令实现的。

在我们的`tasks.ts`文件中，在`TasksComponent`之前注入以下代码块：

```ts
@Component({
 selector : 'task-icons',
 template : `
 <img *ngFor="let icon of icons"
 src="/assets/img/task.png"
 width="50">`
})
export class TaskIconsComponent implements OnInit {
 @Input() task: Task;
 icons: Object[] = [];
 ngOnInit() {
 this.icons.length = this.task.hoursLeft;
 this.icons.fill({ name : this.task.name });
 }
}
```

在我们继续迭代我们的组件之前，重要的是要确保我们将组件注册到一个模块中，这样其他构造体就可以知道它的存在，这样它们就可以在它们的模板中使用该组件。我们通过将它添加到其模块对象的`declarations`属性中来注册它：

```ts
@NgModule({
 imports : [ /* add needed imports here */ ]
 declarations : [ 
 TasksComponent,
   TaskIconsComponent  
 ]
})
export class TaskModule {}
```

现在`TaskModule`知道了我们的组件，我们可以继续改进它。

我们的新`TaskIconsComponent`具有一个非常简单的实现，具有一个非常直观的选择器，与其驼峰命名的类名匹配，以及一个模板，在模板中，我们根据控制器类的 icons 数组属性中填充的对象的数量，多次复制给定的`<img>`标签，这是通过 JavaScript API 中的`Array`对象的 fill 方法填充的（fill 方法用静态值填充数组的所有元素作为参数传递），在`ngOnInit()`中。等等，这是什么？我们不应该在构造函数中实现填充图标数组成员的循环吗？

这种方法是我们将在下一章概述的生命周期钩子之一，可能是最重要的一个。我们之所以在这里填充图标数组字段，而不是在构造方法中，是因为我们需要在继续运行 for 循环之前，每个数据绑定属性都得到适当的初始化。否则，太早访问输入值任务将会返回一个未定义的值。

`OnInit`接口要求在实现此接口的控制器类中集成一个`ngOnInit()`方法，并且一旦所有已定义绑定的输入属性都已检查，它将被执行。我们将在第六章中对组件生命周期钩子进行概述，*使用 Angular 组件构建应用程序*。

我们的新组件仍然需要找到其父组件。因此，让我们在`TasksComponent`的装饰器设置的 directives 属性中插入对组件类的引用：

```ts
@Component({
 selector : 'tasks',
 styleUrls : ['tasks.css'],
 templateUrl : 'tasks.html'
})
```

我们的下一步将是在`TasksComponent`模板中注入`<task-icons>`元素。回到`tasks.html`，并更新条件块内的代码，以便在`hoursLeft`大于零时显示。代码如下：

```ts
<div>
 <h3>
 {{ hoursLeft | i18nPlural:queueHeaderMapping }}
 for today
 <span class="small" *ngIf="hoursLeft > 0">
 (Estimated time : {{ hoursLeft * 25 }})
 </span>
 </h3> 
 <p>
 <span *ngFor="let queuedTask of tasks">
      <task-icons
 [task]="queuedTask"
 (mouseover)="tooltip.innerText = queuedTask.name"
 (mouseout)="tooltip.innerText = 'Mouseover for details'">
 </task-icons>
 </span>
 </p>
 <p #tooltip *ngIf="hoursLeft > 0">Mouseover for details</p>
</div>
<h4>Tasks backlog</h4>
<!-- rest of the template remains the same -->
```

然而，仍然有一些改进的空间。不幸的是，图标大小在`TaskIconsComponent`模板中是硬编码的，这使得在其他需要不同大小的上下文中重用该组件变得更加困难。显然，我们可以重构`TaskIconsComponent`类，以公开一个`size`输入属性，然后将接收到的值直接绑定到组件模板中，以便根据需要调整图像的大小。

```ts
@Component({
 selector : 'task-icon',
 template : `
 <img *ngfor="let icon of icons" 
 src="/assets/img/task.png" 
 width="{{size}}">`
})
export class TaskIconsComponent implements OnInit {
 @Input() task: Task;
 icons : Object[] = [];
  @Input() size: number;
 ngOnInit() {
 // initialise component here
 }
}
```

然后，我们只需要更新`tasks.html`的实现，以声明我们需要的大小值：

```ts
<span *ngFor="let queuedTask of tasks">
 <task-icons 
 [task]="queuedTask" 
    size="50" 
 (mouseover)="tooltip.innerText = queuedTask.name">
 </task-icons>
</span>
```

请注意，`size`属性没有用括号括起来，因为我们绑定了一个硬编码的值。如果我们想要绑定一个组件变量，那么该属性应该被正确声明为`[size]="{{mySizeVariable}}"`。

我们插入了一个新的 DOM 元素，只有在剩余小时数时才会显示出来。我们通过在 H3 DOM 元素中绑定`hoursLeft`属性，显示了一个实际的标题告诉我们剩余多少小时，再加上一个总估计时间，这些都包含在`{{ hoursLeft * 25 }}`表达式中。

`ngFor`指令允许我们遍历 tasks 数组。在每次迭代中，我们渲染一个新的`<task-icons>`元素。

我们在循环模板中将每次迭代的`Task`模型对象，由`queuedTask`引用表示，绑定到了`<task-icons>`的 task 输入属性中。

我们利用了`<task-icons>`元素来包含额外的鼠标事件处理程序，这些处理程序指向以下段落，该段落已标记为`#tooltip`本地引用。因此，每当用户将鼠标悬停在任务图标上时，图标行下方的文本将显示相应的任务名称。

我们额外努力，将由`<task-icons>`渲染的图标大小作为组件 API 的可配置属性。我们现在有了实时更新的图标，当我们切换表格上的信息时。然而，新的问题已经出现。首先，我们正在显示与每个任务剩余时间匹配的图标组件，而没有过滤掉那些未排队的图标。另一方面，为了实现所有任务所需的总估计时间，显示的是总分钟数，随着我们添加更多任务，这个信息将毫无意义。

也许，现在是时候修改一下了。自定义管道来拯救真是太好了！

# 构建我们自己的自定义管道

我们已经看到了管道是什么，以及它们在整个 Angular 生态系统中的目的是什么，但现在我们将更深入地了解如何构建我们自己的一组管道，以提供对数据绑定的自定义转换。

# 自定义管道的解剖

定义管道非常容易。我们基本上需要做以下事情：

+   导入`Pipe`和`PipeTransform`

+   实现`PipeTransform`接口

+   将`Pipe`组件添加到模块中

实现`Pipe`的完整代码看起来像这样：

```ts
import { Pipe, PipeTransform, Component } from '@angular/core';

@Pipe({
 name : 'myPipeName'
})
export class MyPipe implements PipeTransform {
 transform( value: any, ...args: any[]): any {
 // We apply transformations to the input value here
 return something;
 }
}
@Component({
 selector : 'my-selector',
 template : '<p>{{ myVariable | myPipeName: "bar"}}</p>'
})
export class MyComponent {
 myVariable: string = 'Foo';
}
```

让我们逐步分解即将到来的小节中的代码。

# 导入

我们导入了以下结构：

```ts
import { Pipe, PipeTransform, Component }
```

# 定义我们的管道

`Pipe`是一个装饰器，它接受一个对象文字；我们至少需要给它一个名称属性：

```ts
@Pipe({ name : 'myPipeName' })
```

这意味着一旦使用，我们将像这样引用它的名称属性：

```ts
{{ value | myPipeName }}
```

`PipeTransform`是我们需要实现的接口。我们可以通过将其添加到我们的类中轻松实现：

```ts
@Pipe({ name : 'myPipeName' })
export class MyPipeClass {
 transform( value: any, args: any[]) {
 // apply transformation here
 return 'add banana ' + value; 
 }
}
```

在这里，我们可以看到我们有一个 transform 方法，但第一个参数是值本身，其余是`args`，一个包含您提供的任意数量参数的数组。我们已经展示了如何使用这个`Pipe`，但是如果提供参数，它看起来有点不同，就像这样：

```ts
{{ value | myPipeName:arg1:arg2 }}
```

值得注意的是，对于我们提供的每个参数，它最终都会出现在`args`数组中，并且我们用冒号分隔它。

# 注册它

要使一个构造可用，比如一个管道，你需要告诉模块它的存在。就像组件一样，我们需要像这样添加到 declarations 属性中：

```ts
@NgModule({
 declarations : [ MyPipe ]
})
export ModuleClass {}
```

# 纯属性

我们可以向我们的`@Pipe`装饰器添加一个属性，`pure`，如下所示：

```ts
@Pipe({ name : 'myPipe', pure : false })
export class MyPipe implements PipeTransform {
 transform(value: any, ...args: any[]) {}
}
```

“为什么我们要这样做？”你问。嗯，有些情况下可能是必要的。如果你有一个像这样处理原始数据的管道：

```ts
{{ "decorate me" |  myPipe }}
```

我们没有问题。但是，如果它看起来像这样：

```ts
{{ object | myPipe }}
```

我们可能会遇到问题。考虑组件中的以下代码：

```ts
export class Component {
 object = { name : 'chris', age : 37 }

 constructor() {
 setTimeout(() => this.object.age = 38 , 3000)
 }
}
```

假设我们有以下`Pipe`实现来配合它：

```ts
@Pipe({ name : 'pipe' })
export class MyPipe implements PipeTransform {
 transform(value:any, ...args: any[]) {
 return `Person: ${value.name} ${value.age}` 
 }
}
```

这起初会是输出：

```ts
Chris 37
```

然而，你期望输出在 3 秒后改变为`Chris 38`，但它没有。管道只关注引用是否已更改。在这种情况下，它没有，因为对象仍然是相同的，但对象上的属性已更改。告诉它对更改做出反应的方法是指定`pure`属性，就像我们在开始时所做的那样。因此，我们更新我们的`Pipe`实现如下：

```ts
@Pipe({ name : 'pipe', pure: false })
export class MyPipe implements PipeTransform {
 transform(value: any, ...args:any[]) {
 return `Person: ${value.name} ${value.age}`
 }
}
```

现在，我们突然看到了变化发生。不过，需要注意的是，这实际上意味着`transform`方法在每次变更检测周期被触发时都会被调用。因此，这对性能可能会造成损害。如果设置`pure`属性，你可以尝试缓存该值，但也可以尝试使用 reducer 和不可变数据以更好地解决这个问题：

```ts
// instead of altering the data like so
this.jedi.side = 'Dark'

// instead do
this.jedi = Object.assign({}, this.jedi, { side : 'Dark' });
```

前面的代码将更改引用，我们的 Pipe 不会影响性能。总的来说，了解 pure 属性的作用是很好的，但要小心。

# 更好地格式化时间输出的自定义管道

当排列要完成的任务时，观察总分钟数的增加并不直观，因此我们需要一种方法将这个值分解为小时和分钟。我们的管道将被命名为`formattedTime`，并由`formattedTimePipe`类实现，其唯一的 transform 方法接收一个表示总分钟数的数字，并返回一个可读的时间格式的字符串（证明管道不需要返回与载荷中接收到的相同类型）。：

```ts
@Pipe({
 name : 'formattedTime'
})
export class FormattedTimePipe implements PipeTransform {
 transform(totalMinutes : number) {
 let minutes : number = totalMinutes % 60;
 let hours : numbers = Math.floor(totalMinutes / 60);
 return `${hours}h:{minutes}m`;
 }
}
```

我们不应该错过强调管道的命名约定，与我们在组件中看到的一样，管道类的名称加上`Pipe`后缀，再加上一个与该名称匹配但不带后缀的选择器。为什么管道控制器的类名和选择器之间存在这种不匹配？这是常见的做法，为了防止与第三方管道和指令定义的其他选择器发生冲突，我们通常会给我们自定义管道和指令的选择器字符串添加一个自定义前缀。

```ts
@Component({
 selector : 'tasks',
 styleUrls : [ 'tasks.css' ],
 templateUrl : 'tasks.html'
})
export class TasksComponent {}
```

最后，我们只需要调整`tasks.html`模板文件中的 HTML，以确保我们的 EDT 表达式格式正确：

```ts
<span class="small">
 (Estimated time: {{ queued * 25 | formattedTime }})
</span>
```

现在，重新加载页面并切换一些任务。预计时间将以小时和分钟正确呈现。

最后，我们不要忘记将我们的`Pipe`构造添加到其模块`tasks.module.ts`中：

```ts
@NgModule({
 declarations: [TasksComponent, FormattedTimePipe]
})
export class TasksModule {}
```

# 使用自定义过滤器过滤数据

正如我们已经注意到的，我们目前为每个任务在从任务服务提供的集合中显示一个图标组件，而没有过滤出哪些任务标记为排队，哪些不是。管道提供了一种方便的方式来映射、转换和消化数据绑定，因此我们可以利用其功能来过滤我们`ngFor`循环中的任务绑定，只返回那些标记为排队的任务。

逻辑将非常简单：由于任务绑定是一个`Task`对象数组，我们只需要利用`Array.filter()`方法来获取那些`queued`属性设置为`true`的`Task`对象。我们可能会额外配置我们的管道以接受一个布尔参数，指示我们是否要过滤出排队或未排队的任务。这些要求的实现如下，您可以再次看到选择器和类名的惯例：

```ts
@Pipe({
 name : 'queuedOnly'
})
export class QueuedOnlyPipe implements PipeTransform {
 transform(tasks: Task[]), ...args:any[]): Task[] {
 return tasks.filter( task:Task => task.queued === args[0])
 }
}
```

实现非常简单，所以我们不会在这里详细介绍。然而，在这个阶段有一件值得强调的事情：这是一个不纯的管道。请记住，任务绑定是一个有状态对象的集合，随着用户在表格上切换任务，其长度和内容将发生变化。因此，我们需要指示管道利用 Angular 的变更检测系统，以便其输出在每个周期都被后者检查，无论其输入是否发生变化。然后，将管道装饰器的`pure`属性配置为`false`就可以解决问题。

现在，我们只需要更新使用此管道的组件的 pipes 属性：

```ts
@Component({
 selector : 'tasks',
 styleUrls : ['tasks.css'],
 templateUrl : 'tasks.html'
})
export class TasksComponent {
 // Class implementation remains the same
}
```

然后，在`tasks.html`中更新`ngFor`块，以正确过滤出未排队的任务：

```ts
<span *ngFor="queuedTask of tasks | queuedOnly:true">
 <task-icons
 [task]="queuedTask"
 (mouseover)="tooltip.innerText = queuedTask.name"
 (mouseout)="tooltip.innerText = 'Mouseover for details'">
 </task-icons>
</span>
```

请检查我们如何将管道配置为`queuedOnly: true`。将布尔参数值替换为`false`将使我们有机会列出与我们未选择的队列相关的任务。

保存所有工作并重新加载页面，然后切换一些任务。您将看到我们的整体 UI 如何根据最新更改做出相应的反应，我们只列出与排队任务的剩余小时数相关的图标。

# 构建我们自己的自定义指令

自定义指令涵盖了广泛的可能性和用例，我们需要一整本书来展示它们提供的所有复杂性和可能性。

简而言之，指令允许您将高级行为附加到 DOM 中的元素上。如果指令附有模板，则它将成为一个组件。换句话说，组件是具有视图的 Angular 指令，但我们可以构建没有附加视图的指令，这些指令将应用于已经存在的 DOM 元素，使其 HTML 内容和标准行为立即对指令可用。这也适用于 Angular 组件，其中指令将在必要时访问其模板和自定义属性和事件。

# 自定义指令的解剖

声明和实现自定义指令非常容易。我们只需要导入`Directive`类，以为其附属的控制器类提供装饰器功能：

```ts
import { Directive } from '@angular/core';
```

然后，我们定义一个由`@Directive`装饰器注释的控制器类，在其中我们将定义指令选择器、输入和输出属性（如果需要）、应用于宿主元素的可选事件，以及可注入的提供者令牌，如果我们的指令构造函数在实例化时需要特定类型由 Angular 注入器实例化自己（我们将在第六章中详细介绍这一点，*使用 Angular 组件构建应用程序*）：

让我们先创建一个非常简单的指令来热身：

```ts
import { Directive, ElementRef } from '@angular/core';

@Directive({
 selector : '[highlight]'
})
export class HighLightDirective {
 constructor( private elementRef: ElementRef, private renderer : Renderer2 ) {
 var nativeElement = elementRef.nativeElement;
 this.renderer.setProperty( nativeElement,'backgroundColor', 'yellow');
 }
}
```

要使用它就像输入一样简单：

```ts
<h1 highlight></h1>
```

我们在这里使用了两个 actor，`ElementRef`和`Renderer2`，来操作底层元素。我们可以直接使用`elementRef.nativeElement`，但这是不鼓励的，因为这可能会破坏服务器端渲染或与服务工作者交互时。相反，我们使用`Renderer2`的实例进行所有操作。

注意我们不输入方括号，而只输入选择器名称。

我们在这里快速回顾了一下，注入了`ElementRef`并访问了`nativeElement`属性，这是实际元素。我们还像在组件和管道上一样，在类上放置了一个`@Directive`装饰器。创建指令时要有的主要思维方式是考虑可重用的功能，不一定与某个特定功能相关。之前选择的主题是高亮，但我们也可以相对容易地构建其他功能，比如工具提示、可折叠或无限滚动功能。

属性和装饰器，比如选择器、`@Input()`或`@Output()`（与输入和输出相同），可能会让您回想起我们概述组件装饰器规范时的时间。尽管我们尚未详细提到所有可能性，但选择器可以声明为以下之一：

+   `element-name`: 通过元素名称选择

+   `.class`: 通过类名选择

+   `[attribute]`: 通过属性名称选择

+   `[attribute=value]`: 通过属性名称和值选择

+   `not(sub_selector)`: 仅在元素不匹配时选择

`sub_selector`

+   `selector1`, `selector2`: 如果`selector1`或`selector2`匹配，则选择

除此之外，我们还会找到主机参数，该参数指定了与主机元素（即我们指令执行的元素）相关的事件、动作、属性和属性，我们希望从指令内部访问。因此，我们可以利用这个参数来绑定与容器组件或任何其他目标元素（如窗口、文档或主体）的交互处理程序。这样，当编写指令事件绑定时，我们可以引用两个非常方便的本地变量：

+   `$event`: 这是触发事件的当前事件对象。

+   `$target`: 这是事件的来源。这将是一个 DOM 元素或一个 Angular 指令。

除了事件，我们还可以更新属于主机组件的特定 DOM 属性。我们只需要将任何特定属性用大括号括起来，并在我们指令的主机定义中将其作为键值对与指令处理的表达式链接起来。

可选的主机参数还可以指定应传播到主机元素的静态属性，如果尚未存在。这是一种方便的方式，可以使用计算值注入 HTML 属性。

Angular 团队还提供了一些方便的装饰器，这样我们就可以更加直观地在代码中声明我们的主机绑定和监听器，就像这样：

```ts
@HostBinding('[class.valid]')
isValid: boolean; // The host element will feature class="valid"
// is the value of 'isValid' is true.
@HostListener('click', ['$event'])
onClick(e) {
 // This function will be executed when the host 
  // component triggers a 'click' event.
}
```

在接下来的章节中，我们将更详细地介绍指令和组件的配置接口，特别关注它的生命周期管理以及我们如何轻松地将依赖项注入到我们的指令中。现在，让我们只是构建一个简单但强大的指令，它将对我们的 UI 的显示和维护产生巨大的影响。

# 监听事件

到目前为止，我们已经能够创建我们的第一个指令，但这并不是很有趣。然而，添加监听事件的能力会使它变得更有趣，所以让我们来做吧。我们需要使用一个叫做`HostListener`的辅助工具来监听事件，所以我们首先要导入它：

```ts
import { HostListener } from '@angular/core';
```

我们需要做的下一件事是将它用作装饰器并装饰一个方法；是的，一个方法，而不是一个类。它看起来像下面这样：

```ts
@Directive({
 selector : '[highlight]'
})
export class HighlightDirective {
 @HostListener('click')
 clicked() {
 alert('clicked') 
 }
}

```

使用这个指令点击一个元素将会导致一个警告窗口弹出。添加事件非常简单，所以让我们尝试添加`mouseover`和`mouseleave`事件：

```ts
@Directive({
 selector : '[highlight]'
})
export class HighlightDirective {
 private nativeElement;

 constructor(elementRef: ElementRef, renderer: Renderer2) {
 this.nativeElement = elementRef.nativeElement;
 }

 @HostListener('mousenter')
 onMouseEnter() {
 this.background('red');
 }

 onMouseLeave('mouseleave') {
 this.background('yellow');
 }

 private background(bg:string) {
 this.renderer.setAttribute(nativeElement,'backgroundColor', bg);
 }
}
```

这给了我们一个指令，当鼠标悬停在组件上时，背景会变成`红色`，当鼠标离开时会恢复为`黄色`。

# 添加输入数据

我们的指令对于使用什么颜色是相当静态的，所以让我们确保它们可以从外部设置。要添加第一个输入，我们需要使用我们的老朋友`@Input`装饰器，但是不像我们习惯的那样不给它任何参数作为输入，我们需要提供指令本身的名称，如下所示：

```ts
<div highlight="orange"></div>

@Directive({ selector : '[highlight]' })
export class HighlightDirective 
 private nativeElement;

 constructor(elementRef: ElementRef, renderer: Renderer2) {
 this.nativeElement = elementRef.nativeElement;
 }

 @Input('highlight') color:string;

 @HostListener('mousenter')
 onMouseEnter(){
 this.background(this.color);
 }

 onMouseLeave() {
 this.background('yellow'); 
 }

 private background(bg: string) {
 this.renderer( nativeElement, 'background', bg );
 }
}
```

在这一点上，我们已经处理了第一个输入；我们用以下方法做到了这一点：

```ts
@Input('highlight') color: string;
```

但是，我们如何向我们的指令添加更多的输入？我们将在下一小节中介绍这个问题。

# 添加多个输入属性

所以你想要添加另一个输入，这也相对容易。我们只需要在我们的 HTML 元素中添加一个属性，如下所示：

```ts
<div [highlight]="orange" defaultColor="yellow">
```

在代码中我们输入：

```ts
@Directive({})
export class HighlightDirective {
 @Input() defaultColor
 constructor() {
 this.background(this.defaultColor);
 }
 // the rest omitted for brevity
}
```

然而，我们注意到在我们进行第一次`mousenter` + `mouseleave`之前，我们没有颜色，原因是构造函数在我们的`defaultColor`属性被设置之前运行。为了解决这个问题，我们需要稍微不同地设置输入。我们需要像这样使用一个属性：

```ts
private defaultColor: string;

@Input()
set defaultColor(value) { 
 this.defaultColor = value;
 this.background(value); 
}

get defaultColor(){ return this.defaultColor; }
```

总结一下关于使用输入的部分，很明显我们可以使用`@Input`装饰器来处理一个或多个输入。然而，第一个输入应该是指令的选择器名称，第二个输入是你给它的属性的名称。

# 第二个例子 - 错误验证

让我们利用对指令的这些新知识，构建一个指示字段错误的指令。我们认为错误是指我们着色元素并显示错误文本：

```ts
import { Directive, ElementRef, Input } from '@angular/core';
@Directive({
 selector: '[error]'
})
export class ErrorDirective {
 error:boolean;
 private nativeElement;
 @Input errorText: string;
 @Input()
 set error(value: string) {
 let val = value === 'true' ? true : false;
 if(val){ this.setError(); }
 else { this.reset(); }
 }

 constructor(
 private elementRef: ElementRef, 
 private renderer: Renderer2
 ) {
 this.nativeElement = elementRef.nativeElement;
 }

 private reset() { 
 this.renderer.setProperty(nativeElement, 'innerHTML', '');
 this.renderer.setProperty(nativeElement, 'background', '') 
 }

 private setError(){
 this.renderer.setProperty(nativeElement, 'innerHTML', this.errorText);
 this.renderer.setProperty(nativeElement, 'background', 'red');
 }
}
```

而要使用它，我们只需输入：

```ts
<div error="{{hasError}}" errorText="display this error">
```

# 构建一个任务提示自定义指令

到目前为止，我们已经构建了一个高亮指令以及一个错误显示指令。我们已经学会了如何处理事件以及多个输入。

关于提示信息的简短说明。当我们悬停在一个元素上时，会出现提示信息。通常你要做的是在元素上设置 title 属性，就像这样：

```ts
<div title="a tooltip"></div>
```

通常有几种方法可以在这样的组件上构建提示信息。一种方法是绑定到`title`属性，就像这样：

```ts
<task-icons [title]="task.name"></task-icons>
```

然而，如果你有更多的逻辑想法，将所有内容都添加到标记中可能不太好，所以在这一点上，我们可以创建一个指令来隐藏提示信息，就像这样：

```ts
@Directive({ selector : '[task]' })
export class TooltipDirective {
 private nativeElement;
 @Input() task:Task;
 @Input() defaultTooltip: string;

 constructor(private elementRef: ElementRef, private renderer : Renderer2) {
 this.nativeElement = elementRef.nativeElement;
 }

 @HostListener('mouseover')
 onMouseOver() {
 let tooltip = this.task ? this.task.name : this.defaultTooltip;
 this.renderer.setProperty( this.nativeElement, 'title', tooltip );
 }
}
```

使用它将是：

```ts
<div [task]="task">
```

然而，我们还可以采取另一种方法。如果我们想在悬停在一个元素上时改变另一个元素的 innerText 呢？这是很容易做到的，我们只需要将我们的指令传递给另一个元素，并更新它的 innerText 属性，就像这样：

```ts
<div [task]="task" [elem]="otherElement" defaultTooltip="default text" >
<div #otherElement>
```

当然，这意味着我们需要稍微更新我们的指令到这样：

```ts
@Directive({ selector : '[task]' })
export class TooltipDirective {
 private nativeElement;
 @Input() task:Task;
 @Input() defaultTooltip: string;

 constructor(private elementRef: ElementRef, private renderer : Renderer2) {
 this.nativeElement = elementRef.nativeElement;
 }

 @HostListener('mouseover')
 onMouseOver() {
 let tooltip = this.task ? this.task.name : this.defaultTooltip;
    this.renderer.setProperty( this.nativeElement, 'innerText', tooltip );
 }
}
```

# 关于自定义指令和管道的命名约定

谈到可重用性，通常的约定是在选择器前面添加一个自定义前缀。这可以防止与其他库定义的选择器发生冲突，这些库可能在我们的项目中使用。同样的规则也适用于管道，正如我们在介绍我们的第一个自定义管道时已经强调的那样。

最终，这取决于你和你采用的命名约定，但建立一个可以防止这种情况发生的命名约定通常是一个好主意。自定义前缀绝对是更容易的方法。

# 总结

现在我们已经达到这一点，可以说你几乎知道构建 Angular 组件所需的一切，这些组件确实是所有 Angular 2 应用程序的核心和引擎。在接下来的章节中，我们将看到如何更好地设计我们的应用程序架构，因此在整个组件树中管理依赖注入，使用数据服务，利用新的 Angular 路由器在需要时显示和隐藏组件，并管理用户输入和身份验证。

然而，这一章是 Angular 开发的支柱，我们希望您和我们一样喜欢它，当我们写关于模板语法、基于属性和事件的组件 API、视图封装、管道和指令时。现在，准备好迎接新的挑战——我们将从学习如何编写组件转向发现如何使用它们来构建更大的应用程序，同时强调良好的实践和合理的架构。我们将在下一章中看到所有这些。
