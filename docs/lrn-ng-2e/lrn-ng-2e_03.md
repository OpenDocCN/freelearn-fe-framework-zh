# 第三章：介绍 TypeScript

在上一章中，我们构建了我们的第一个组件，并使用 TypeScript 来塑造代码脚本，从而赋予其形式。本书中的所有示例都使用其语法。正如我们将在本书中看到的，使用 TypeScript 编写我们的脚本并利用其静态类型将使我们在其他脚本语言上具有显着优势。

本章不是对 TypeScript 语言的全面概述。我们将只关注语言的核心元素，并在我们学习 Angular 的过程中详细研究它们。好消息是，TypeScript 并不那么复杂，我们将设法涵盖它的大部分相关部分。

在本章中，我们将：

+   看看 TypeScript 背后的背景和原理

+   发现在线资源，练习学习

+   回顾类型化值的概念以及如何表示它们

+   构建我们自己的类型，基于类和接口

+   学会更好地组织我们的应用架构与模块

# 理解 TypeScript 的案例

早期 JavaScript 驱动的小型 Web 应用程序的自然演变

将厚重的单片客户端揭示了 ECMAScript 5 JavaScript 规范的缺陷。简而言之，一旦规模和复杂性增加，大规模 JavaScript 应用程序就会遭受严重的可维护性和可扩展性问题。

随着新的库和模块需要无缝集成到我们的应用程序中，这个问题变得更加重要。缺乏良好的互操作机制导致了一些非常繁琐的解决方案，似乎从未符合要求。

作为对这些问题的回应，ECMAScript 6（也称为 ES6 或 ES2015）承诺通过引入更好的模块加载功能、改进的语言架构以更好地处理作用域，并引入各种语法糖来更好地管理类型和对象，来解决这些可维护性和可扩展性问题。基于类的编程的引入成为了在构建大规模应用程序时采用更 OOP 方法的机会。

微软接受了这一挑战，花了近两年的时间构建了一种语言的超集，结合了 ES6 的约定，并借鉴了 ES7 的一些提案。其想法是推出一些有助于通过静态类型检查、更好的工具和代码分析来构建企业应用程序的东西，以际降低错误率。

在由 C#首席架构师 Anders Hejlsberg 领导的两年开发之后，TypeScript 0.8 终于在 2012 年推出，并在两年后达到了 1.0 版本。TypeScript 不仅领先于 ECMAScript 6，而且还实现了相同的功能，并通过类型注释引入了可选的静态类型，从而确保了编译时的类型检查。这有助于在开发过程的早期阶段捕获错误。声明文件的支持也为开发人员提供了描述其模块接口的机会，以便其他开发人员可以更好地将其集成到其代码工作流程和工具中。

# TypeScript 的好处

以下信息图提供了对不同功能的俯视。

区分 ECMAScript 6 和 ECMAScript 5，然后区分 TypeScript 与这两者。

作为 ECMAScript 6 的超集，采用 TypeScript 在下一个项目中的主要优势之一是低入门门槛。如果你了解 ECMAScript 6，那么你几乎已经具备了一切，因为 TypeScript 中的所有附加功能都是可选的。你可以选择并引入在实践中帮助你实现目标的功能。总的来说，有很多有力的论点支持在下一个项目中倡导使用 TypeScript，所有这些显然也适用于 Angular。以下是一些论点的简要概述，仅举几例：

+   用类型注释我们的代码可以确保不同代码单元的一致集成，并提高代码的可读性和理解性。

+   TypeScript 的内置类型检查器将在运行时分析您的代码，并帮助您在执行代码之前防止错误。

+   使用类型可以确保应用程序的一致性。与前两者结合使用，从长远来看，整体代码错误的印记得到最小化。

+   TypeScript 通过类字段、私有成员、枚举等长期需求的功能扩展了类。

+   使用装饰器为我们打开了以前无法企及的方式来扩展我们的类和实现。

+   创建接口和类型定义文件（本书不涉及）确保了我们的库在其他系统和代码库中的平稳无缝集成。

+   TypeScript 在商店中不同 IDE 的支持非常好，我们可以从代码高亮、实时类型检查和自动编译中受益，而且没有任何成本。

+   TypeScript 的语法肯定会让来自其他背景（如 Java、C＃、C ++等）的开发人员感到满意。

# 在野外介绍 TypeScript 资源

现在，我们将看看在哪里可以获得更多支持来学习和测试我们对 TypeScript 的新知识。

# TypeScript 官方网站

显然，我们首先要去官方网站了解这门语言：[`www.typescriptlang.org`](http://www.typescriptlang.org)。在那里，我们可以找到更详尽的语言介绍以及 IDE 和企业支持者的链接。然而，我们肯定会经常回顾的最重要部分是学习部分和 play 沙盒。

学习部分为我们提供了快速教程，让我们迅速掌握这门语言。这可能是对我们在上一章讨论的内容的一个回顾，但我们建议您跳过它，转而查看示例页面和语言规范，后者是指向 GitHub 上语言完整广泛文档的直接链接。这对新用户和有经验的用户都是无价的资源。

play 部分提供了一个方便的沙盒，包括一些现成的代码示例，涵盖了语言的一些最常见特性。我们鼓励您利用这个工具来测试我们在本章中将看到的代码示例。

# TypeScript 官方 wiki

在上一章中，当我们谈到使用 TypeScript 编译器 API 执行命令时，我们提到了 TypeScript 的 wiki 中最基本的参数。

TypeScript 的代码完全开源在 GitHub 上，微软团队在存储库网站上提供了对代码不同方面的良好文档。我们鼓励您随时查看，如果您有问题或想深入了解语言特性或语法方面的任何内容。

wiki 位于：[`github.com/Microsoft/TypeScript/wiki`](https://github.com/Microsoft/TypeScript/wiki)。

# TypeScript 中的类型

使用 TypeScript 或任何其他编程语言基本上意味着使用数据，这些数据可以表示不同类型的内容。这就是我们所知的类型，一个用来表示这样的数据可以是文本字符串、整数值或这些值类型的数组等的名词。这对 JavaScript 来说并不新鲜，因为我们一直在隐式地使用类型，但是以一种灵活的方式。这意味着任何给定的变量都可以假定（或返回，在函数的情况下）任何类型的值。有时，这会导致我们的代码出现错误和异常，因为我们的代码返回的类型与我们期望的类型发生了冲突。虽然这种灵活性仍然可以通过我们将在本章后面看到的任何类型来强制执行，但是静态地为我们的变量标注类型可以给我们和我们的 IDE 提供一个很好的图片，说明我们应该在每个代码实例中找到什么样的数据。这成为在编译时帮助我们调试应用程序的无价方式，而不至于为时已晚。要调查语言特性的工作原理，我建议您使用游乐场，有两个原因。第一个原因是学习该功能的工作原理。第二个原因是了解它产生的相应的 ES5 代码。我建议使用以下游乐场进行此操作：[`www.typescriptlang.org/play/.`](https://www.typescriptlang.org/play/)

# 字符串

我们代码中可能最广泛使用的原始类型之一将是字符串类型，我们用一段文本填充一个变量。

```ts
var brand: string = 'Chevrolet';
```

检查变量名称旁边的类型赋值，用冒号符号分隔。这就是我们在 TypeScript 中注释类型的方式，就像我们在上一章中看到的那样。

回到字符串类型，我们可以使用单引号或双引号，与 ECMAScript6 相同。我们可以使用相同类型定义支持文本插值的多行文本字符串，使用占位变量：

```ts
var brand: string = 'Chevrolet';
var message: string = `Today it's a happy day! I just bought a new ${brand} car`;
```

# 声明我们的变量 - ECMAScript 6 的方式

TypeScript 作为 ECMAScript 6 的超集，支持表达性声明名词，比如`let`，它告诉我们变量的作用域是最近的封闭块（函数`for`循环或任何封闭语句）。另一方面，`const`是一个指示，这种方式声明的值一旦被填充就应该始终具有相同的类型或值。在本章的其余部分，我们将强制使用传统的`var`符号来声明变量，但请记住在适当的地方使用`let`和`const`。

# let 关键字

在代码中的许多情况下，我一直在使用`var`来声明对象、变量和其他构造。但是在 ES6 或 TypeScript 中开始时，这是不被鼓励的。这是有原因的，因为 ES5 只有方法作用域。对于大多数从其他语言转到 JavaScript 的开发人员来说，这可能有点震惊。首先，我们所说的函数作用域是什么意思？我们的意思是变量在函数的上下文中是唯一的，就像这样：

```ts
function test() {
 var a;
}
```

在该函数中不能有其他变量`a`。如果你声明了更多的变量，那么你将有效地重新定义它。好的，但是什么时候作用域不起作用呢？例如，在`for`-循环中就没有作用域。在 Java 中，你会这样写：

```ts
for (int i = 0; i < arr.length; i++) {
}
```

在 Java 中，你会知道变量`i`永远不会泄漏到`for`-循环之外，你可以这样写：

```ts
int i = 3;
for (int i = 0; i < arr.length; i++) {
}
```

并且要知道`for`-循环之外的变量`i`不会影响`for`-循环内的变量`i`，它们会被分隔或作用域化，就像它被称为的那样。好的，所以 ES5 JavaScript 的用户已经有了这个语言缺陷很长时间了，最近 ES6 和 Typescript 分别添加了一个修复这个问题的方法，即`let`关键字。像这样使用它：

```ts
let i = 3;
for (let i = 0; i < arr.length; i++) {
}
```

这样运行的原因是 TypeScript 编译器将其转换为以下 ES5 代码：

```ts
var i = 3;
for (var i_1 = 0; i_1 < arr.length; i_1++) {
}
```

编译器基本上会在`for`-循环中重新命名变量，以防发生名称冲突。所以记住，不再使用`var`，当有疑问时只需使用`let`关键字。

# Const

`const`关键字是一种方式，让你传达这些数据永远不应该被改变。随着代码库的增长，很容易发生错误的更改；这样的错误可能是代价高昂的。为了在编译时支持这一点，`const`关键字可以帮助你。以以下方式使用它：

```ts
const PI = 3.14;
PI = 3 // not allowed
```

编译器甚至会指出不允许这样做，并显示以下消息：

```ts
Cannot assign to PI because it is a constant or a read-only property
```

这里需要注意一点：这仅适用于顶层。如果您将对象声明为`const`，则需要注意这一点：

```ts
const obj = {
 a : 3
}
obj.a = 4; // actually allowed
```

声明`obj`为`const`并不会冻结整个对象，而是`obj`指向的内容。因此，以下内容将不被允许：

```ts
obj = {}
```

在这里，我们积极改变了`obj`指向的内容，而不是它的一个子属性，因此这是不允许的，你会得到与之前相同的编译错误。

# 数字

数字可能是除了字符串和布尔值之外最常见的原始数据类型。与 JavaScript 一样，数字定义了浮点数。数字类型还定义了十六进制、十进制、二进制和八进制文字：

```ts
var age: number = 7;
var height: number = 5.6;
```

# 布尔值

布尔类型定义了可以是`True`或`False`的数据，表示条件的满足：

```ts
var isZeroGreaterThanOne: boolean = false;
```

# 数组

将错误的成员类型分配给数组，并处理由此引起的异常，现在可以通过`Array`类型轻松避免，我们在其中描述了仅包含某些类型的数组。语法只需要在类型注释中使用`后缀[]`，如下所示：

```ts
var brand: string[] = ['Chevrolet', 'Ford', 'General Motors'];
var childrenAges: number[] = [8, 5, 12, 3, 1];
```

如果我们尝试向`childrenAges`数组添加一个类型不是数字的新成员，运行时类型检查器将抱怨，确保我们的类型成员保持一致，我们的代码是无错误的。

# 使用 any 类型的动态类型

有时，很难根据我们在某一时刻拥有的信息推断数据类型，特别是当我们将遗留代码移植到 TypeScript 或集成松散类型的第三方库和模块时。不用担心，TypeScript 为我们提供了一个方便的类型来处理这些情况。`any`类型与所有其他现有类型兼容，因此我们可以使用它对任何数据值进行类型标注，并在以后分配任何值给它。然而，这种强大的功能也伴随着巨大的责任。如果我们绕过静态类型检查的便利，我们就会在通过我们的模块传递数据时打开类型错误的大门，我们将需要确保整个应用程序的类型安全：

```ts
var distance: any;
// Assigning different value types is perfectly fine
distance = '1000km':
distance = '1000'
// Allows us to seamlessly combine different types
var distance: any[] = ['1000km', '1000'];
```

空值和未定义的 JavaScript 文字需要特别提到。简而言之，它们在`any`类型下进行了类型化。这样以后就可以将这些文字分配给任何其他变量，而不管其原始类型如何。

# 自定义类型

在 Typescript 中，如果需要，您可以使用以下方式使用`type`关键字自定义类型：

```ts
type Animal = 'Cheetah' | 'Lion';
```

现在我们创建的是一个具有*x*个允许值的类型。让我们从这种类型创建一个变量：

```ts
var animal: Animal = 'Cheetah';
```

这是完全允许的，因为 `Cheetah` 是允许的值之一，并且按预期工作。有趣的部分发生在我们给变量赋予它不期望的值时：

```ts
var animal: Animal = 'Turtle';
```

这导致了以下编译器错误：

```ts
error TS2322: Type '"Turtle"' is not assignable to type 'Animal'.
```

# Enum

Enum 基本上是一组唯一的数值，我们可以通过为每个数值分配友好的名称来表示它们。枚举的用途不仅限于为数字分配别名。我们可以将它们用作以方便和可识别的方式列出特定类型可以假定的不同变化的方法。

枚举使用 `enum` 关键字声明，不使用 `var` 或任何其他变量声明名词，并且它们从 0 开始编号成员，除非为它们分配了显式的数值：

```ts
enum Brands { Chevrolet, Cadillac, Ford, Buick, Chrysler, Dodge };
var myCar: Brands = Brands.Cadillac;
```

检查 `myCar` 的值将返回 `1`（这是 `enum` 中 `Cadillac` 所持有的索引）。正如我们已经提到的，我们可以在 `enum` 中分配自定义数值：

```ts
enum BrandsReduced { Tesla = 1, GMC, Jeep };
var myTruck: BrandsReduced = BrandsReduced.GMC;
```

检查 `myTruck` 将产生 `2`，因为第一个枚举值已经设置为 `1`。只要这些值是整数，我们就可以将值分配给所有的 `enum` 成员：

```ts
enum StackingIndex {
 None = 0,
 Dropdown = 1000,
 Overlay = 2000,
 Modal = 3000
};
var mySelectBoxStacking: StackingIndex = LayerStackingIndex.Dropdown;
```

最后值得一提的一个技巧是查找与给定数值映射的枚举成员的可能性：

```ts
enum Brands { Chevrolet, Cadillac, Ford, Buick, Chrysler, Dodge };
var MyCarBrandName: string = Brands[1];
```

应该提到的是，从 TypeScript 2.4 开始，可以将字符串值分配给枚举。

# Void

`void` 类型确实表示任何类型的缺失，其使用受限于注释不返回实际值的函数。因此，也没有返回类型。我们已经有机会在上一章中通过一个实际例子看到这一点：

```ts
resetPomodoro(): void {
 this.minutes = 24;
 this.seconds = 59;
}
```

# 类型推断

对我们的数据进行类型标注是可选的，因为 TypeScript 足够聪明，可以在上下文中推断出变量和函数返回值的数据类型，并且具有一定的准确性。当无法进行类型推断时，TypeScript 将以动态的 any 类型分配给松散类型的数据，以减少类型检查的成本。

推断工作的一个例子可以在以下代码中看到：

```ts
var brand = 'Chevrolet';
```

这具有相同的效果，也就是说，如果您尝试将不兼容的数据类型分配给它，它将导致编译错误，就像这样：

```ts
var brand: string = 'Chevrolet';
var brand2 = 'Chevrolet';
brand = false; // compilation error
brand = 114; // compilation error
```

# 函数、lambda 和执行流

与 JavaScript 一样，函数是处理机器，我们在其中分析输入，消化信息，并对提供的数据应用必要的转换，以便转换我们应用程序的状态或返回一个输出，该输出将用于塑造我们应用程序的业务逻辑或用户交互。

TypeScript 中的函数与普通 JavaScript 并没有太大的区别，除了函数本身以及 TypeScript 中的其他所有内容一样，可以用静态类型进行注释，因此，它们更好地通知编译器它们在签名中期望的信息以及它们的返回数据类型（如果有的话）。

# 在我们的函数中注释类型

以下示例展示了在 TypeScript 中如何注释常规函数：

```ts
function sayHello(name: string): string {
 return 'Hello, ' + name;
}
```

我们可以清楚地看到与普通 JavaScript 中的常规函数语法有两个主要区别。首先，在函数签名中注释了参数的类型信息。这是有道理的，因为编译器将希望检查在执行函数时提供的数据是否具有正确的类型。除此之外，我们还通过在函数声明中添加后缀字符串来注释返回值的类型。在这些情况下，给定的函数不返回任何值，类型注释 void 将为编译器提供所需的信息，以进行适当的类型检查。

正如我们在前一节中提到的，TypeScript 编译器足够聪明，可以在没有提供注释时推断类型。在这种情况下，编译器将查看提供的参数和返回语句，以推断返回类型。

TypeScript 中的函数也可以表示为匿名函数的表达式，我们将函数声明绑定到一个变量上：

```ts
var sayHello = function(name: string): string {
 return 'Hello, ' + name;
}
```

然而，这种语法也有一个缺点。虽然允许以这种方式对函数表达式进行类型化，但由于类型推断，编译器在声明的变量中缺少类型定义。我们可能会假设指向类型为字符串的函数的变量的推断类型显然是字符串。但事实并非如此。指向匿名函数的变量应该用函数类型进行注释。基本上，函数类型通知了函数负载中期望的类型以及函数执行返回的类型（如果有的话）。这整个块，以`(arguments: type) =>`返回类型的形式，成为我们的编译器期望的类型注释：

```ts
var sayHello: (name: string) => string = function(name: string): string {
 return 'Hello, ' + name;
}
```

你可能会问为什么会有这样繁琐的语法？有时，我们会声明可能依赖于工厂或函数绑定的变量。然后，尽可能向编译器提供尽可能多的信息总是一个好习惯。这个简单的例子可能会帮助你更好地理解：

```ts
// Two functions with the same typing but different logic.
function sayHello(input: string): string {
 return 'Hello, ' + input;
}

function sayHi(input: string): string{
 return 'Hi, ' + input;
}

// Here we declare the variable with is own function type
var greetMe: (name: string) => string;
greetMe = sayHello; 
```

这样，我们也确保以后的函数赋值符合在声明变量时设置的类型注解。

# TypeScript 中的函数参数

由于编译器执行的类型检查，TypeScript 中的函数参数需要特别注意。

# 可选参数

参数是 TypeScript 编译器应用的类型检查的核心部分。TypeScript 通过在参数名称后面添加`?`符号来提供可选功能，这允许我们在函数调用中省略第二个参数。

```ts
function greetMe(name: string, greeting?: string): string {
 console.log(greeting);
 if(!greeting) { greeting = 'Hello'; }
 return greeting + ', ' + name;
}

console.log( greetMe('Chris') );
```

这段代码将尝试打印出问候变量，并产生一个合适的问候。像这样运行这段代码：

```ts
greetMe('Chris');
```

将给我们以下结果：

```ts
undefined
Hello Chris
```

因此，可选参数实际上不会被设置，除非你明确地这样做。这更多是一种构造，让你可以帮助决定哪些参数是必需的，哪些是可选的。让我们举个例子：

```ts
function add(mandatory: string, optional?: number) {}
```

你可以以以下方式调用这个函数：

```ts
add('some string');
add('some string', 3.14);
```

两个版本都是允许的。在函数签名中使用可选参数会强制你将它们放在最后，就像前面的例子一样。以下例子说明了什么不应该做：

```ts
function add(optional?: number, mandatory: string) {}
```

这将创建这样一种情况，其中两个参数都是必需的：

```ts
add(11); // error. mandatory parameter missing
```

即使编译器会抱怨并说以下内容：

```ts
A required parameter cannot follow an optional parameter
```

记住，可选参数很好，但要放在最后。

# 默认参数

TypeScript 给了我们另一个功能来应对前面描述的情况，即默认参数，我们可以在执行函数时设置参数的默认值，当没有明确赋值时参数将采用默认值。语法非常简单，我们可以在重构前面的例子时看到：

```ts
function greetMe(name: string, greeting: string = 'Hello'): string {
 return `${greeting}, ${name}`;
}
```

与可选参数一样，默认参数必须放在函数签名中非默认参数的后面。有一个非常重要的区别，就是默认参数总是安全的。为什么它们是安全的，可以从下面的 ES5 代码中看出。下面的 ES5 代码是将上面的 TypeScript 编译为 ES5 得到的结果代码。下面的代码表明编译器添加了一个 IF 子句，检查变量`greeting`是否为 undefined，如果是，则给它一个起始值：

```ts
function greetMe(name, greeting){
 if (greeting === void 0) { greeting = 'Hello'; }

 return greeting + ', ' + name;
}
```

正如你所看到的，编译器添加了一个 if 子句来检查你的值，如果没有设置，它会添加你之前提供的值。

当你处理默认参数时，类型会被推断出来，因为你给它们赋了一个值。在前面的代码片段中，greeting 被赋予字符串值'Hello'，因此被推断为字符串类型。

# 剩余参数

在定义函数时，JavaScript 的灵活性之一是接受以 arguments 对象形式的无限数量的未声明的参数。在 TypeScript 这样的静态类型上下文中，这可能是不可能的，但通过 REST 参数对象实际上是可能的。在这里，我们可以在参数列表的末尾定义一个额外的参数，前面加上省略号(`...`)并且类型为数组：

```ts
function greetPeople(greeting: string, ...names: string[]): string{
 return greeting + ', ' + names.join(' and ') + '!';
}

alert(greetPeople('Hello', 'John', 'Ann', 'Fred'));
```

需要注意的是，剩余参数必须放在参数列表的末尾，不需要时可以省略。让我们看一下生成的 ES5 代码，以了解 TypeScript 编译器生成了什么：

```ts
function greetPeople(greeting) {
 var names = [];
 for (var _i = 1; _i < arguments.length; _i++) {
 names[_i - 1] = arguments[_i];
 }
 return greeting + ', ' + names.join(' and ') + '!';
}

alert(greetPeople('Hello', 'John', 'Ann', 'Fred'));
```

我们可以看到这里使用了内置的 arguments 数组。而且，它的内容被复制到`names`数组中：

```ts
for (var _i = 1; _i < arguments.length; _i++) {
 names[_i -1] = arguments[_i];
}
```

当你想一想的时候，这真的是非常合理的。所以，当你不知道参数的数量时，剩余参数就是你的朋友。

# 函数签名的重载

方法和函数的重载在其他语言中是一种常见模式，比如 C#。然而，在 TypeScript 中实现这种功能与 JavaScript 相冲突，因为 JavaScript 并没有提供一种优雅的方式来直接集成这种功能。因此，唯一的解决方法可能是为每个重载编写函数声明，然后编写一个通用函数，它将包装实际的实现，并且其类型参数和返回类型与所有其他函数兼容：

```ts
function hello(name: string): string {}
function hello(name: string[]): string {}
function hello(name: any, greeting?: string): string {
 var namesArray: string[];
 if (Array.isArray(names)) {
 namesArray = names;
 } else {
 namesArray = [names];
 }
 if (!greeting) {
 greeting = 'Hello';
 }
 return greeting + ', ' + namesArray.join(' and ') + '!';
}
```

在上面的例子中，我们暴露了三种不同的函数签名，每个函数签名都具有不同的类型注释。如果有必要，我们甚至可以定义不同的返回类型。为此，我们只需使用任何返回类型注释包裹函数即可。

# 更好的函数语法和 lambda 的范围处理

ECMAScript 6 引入了箭头函数的概念（在其他语言中也称为 lambda 函数，如 Python、C＃、Java 或 C++），旨在简化一般函数语法，并提供一种处理函数范围的可靠方法，传统上由于处理`this`关键字的范围问题而处理。

第一印象是它的极简语法，大多数情况下，我们会看到箭头函数作为单行匿名表达式：

```ts
var double = x => x * 2;
```

该函数计算给定数字`x`的两倍，并返回结果，尽管我们在表达式中没有看到任何函数或返回语句。如果函数签名包含多个参数，我们只需要将它们都包裹在大括号中：

```ts
var add = (x, y) => x + y;
```

这使得这种语法在开发`map`、`reduce`等功能操作时非常方便：

```ts
var reducedArray = [23, 5, 62, 16].reduce((a, b) => a + b, 0);
```

箭头函数也可以包含语句。在这种情况下，我们希望将整个实现包裹在大括号中：

```ts
var addAndDouble = (x, y) => {
 var sum = x + y;
 return sum * 2;
}
```

但是，这与范围处理有什么关系呢？基本上，this 的值取决于我们执行函数的上下文。对于一种以出色的功能编程灵活性自豪的语言来说，这是一件大事，其中回调等模式至关重要。在回调函数中引用`this`时，我们失去了上下文的追踪，这通常迫使我们使用约定，例如将`this`的值分配给一个名为 self 或 that 的变量，稍后在回调中使用。包含间隔或超时函数的语句是这一点的完美例子：

```ts
function delayedGreeting(name): void {
 this.name = name;
 this.greet = function(){
 setTimeout(function() {
 alert('Hello ' + this.name);
 }, 0);
 }
}

var greeting = new delayedGreeting('Peter');
greeting.greet(); // alert 'Hello undefined'
```

在执行上述脚本时，我们不会得到预期的`Hello Peter`警报，而是一个不完整的字符串，突出显示对`Mr. Undefined!`的讨厌的问候。基本上，这种构造在评估超时调用内部的函数时会破坏 this 的词法作用域。将此脚本转换为箭头函数将解决问题：

```ts
function delayedGreeting(name): void {
 this.name = name;
 this.greet = function() {
 setTimeout(() => alert('Hello ' + this.name), 0);
 }
}
```

即使我们将箭头函数中的语句拆分为由花括号包裹的几行代码，this 的词法作用域仍将指向 setTimeout 调用外部的适当上下文，从而实现更加优雅和清晰的语法。

# 一般特性

在 TypeScript 中有一些一般特性，它们并不特别适用于类、函数或参数，而是使编码更加高效和有趣。这个想法是，你写的代码行数越少，就越好。这不仅仅是关于行数更少，还关乎让事情更清晰。在 ES6 中有许多这样的特性，TypeScript 也实现了这些特性，但在这里，我只会列出一些可能会出现在你的 Angular 项目中的特性。

# 展开参数

展开参数使用与 REST 参数相同的语法`...`省略号，但用法不同。它不是作为函数内部的参数使用，而是在函数体内使用。

让我们来说明一下这意味着什么：

```ts
var newItem = 3;
var oldArray = [ 1, 2 ];
var newArray = [
 ...oldArray,
 newItem
];
console.log( newArray )
```

这将输出：

```ts
1,2,3
```

我们在这里做的是向现有数组添加一个项目，而不改变旧数组。oldArray 变量仍然包含 1,2，但 newArray 包含 1,2,3。这个一般原则被称为*不可变性*，它基本上意味着不要改变，而是从旧状态创建一个新状态。这是函数式编程中使用的原则，既作为一种范式，也是出于性能原因。

你也可以在对象上使用 REST 参数；是的，真的。你可以这样写：

```ts
var oldPerson = { name : 'Chris' };
var newPerson = { ...oldPerson, age : 37 }; 
console.log( newPerson );
```

运行此代码的结果是：

```ts
{ name: 'Chris', age: 37 }
```

两个对象之间的合并。就像列表的例子一样，我们不会改变先前的变量 oldPerson。一个 newPerson 变量将从 oldPerson 获取信息，但同时将其新值添加到其中。看看 ES5 代码，你就会明白为什么：

```ts
var __assign = ( this && this.__assign ) || Object.assign || function(t) {
 for (var s, i = n, n = arguments.length; i < n; i++) {
 s = arguments[i];
 for (var p in s) if (Object.prototype.hasOwnProperty.call( s, p )) {
 t[ p ] = s[ p ];
 }
 return t;
 };
 var oldPerson = { name : 'Chris' };
 var newPerson = __assign({}, oldPerson, { age: 37 });
 console.log( newPerson );
}
```

这里发生的是定义了一个`assign`函数。该函数循环遍历`oldPerson`变量的键，并将其分配给一个新对象，最后添加`newPerson`变量的内容。如果你看一下前面的函数，它要么定义一个执行此操作的函数，要么使用 ES6 标准中的`Object.assign`（如果可用）。

# 模板字符串

模板字符串的目的是让你的代码更清晰。想象一下以下情景：

```ts
var url = 'http://path_to_domain' + 
'path_to_resource' + 
'?param=' + parameter + 
'=' + 'param2=' + 
parameter2;
```

那么，这有什么问题吗？答案是可读性。很难想象结果字符串会是什么样子，但你也很容易错误地编辑以前的代码，突然间，结果将不是你想要的。大多数语言都使用格式化函数来解决这个问题，这正是模板字符串的作用，一个格式化函数。它的使用方式如下：

```ts
var url = `${baseUrl}/${path_to_resource}?param=
 ${parameter}&param2={parameter2}`;
```

这是一个更简洁的表达方式，因此更容易阅读，所以一定要使用它。

# 泛型

泛型是一个表达式，表示我们有一个通用的代码行为，无论数据类型如何，我们都可以使用它。泛型经常用于操作集合，因为集合通常具有类似的行为，无论类型如何。但泛型也可以用于方法等结构。其想法也是，泛型应该指示你是否要以不允许的方式混合类型。

```ts
function method<T>(arg: T): T {
 return arg;
}
console.log(method<number>(1)); // works
console.log(method<string>(1)); // doesn't work
```

在前面的例子中，`T` 直到你实际使用该方法时才确定。正如你所看到的，`T` 的类型根据你调用它的方式从数字变化到 `String`。它还确保你输入了正确类型的数据。这可以在以下行中看到：

```ts
console.log(method<string>(1)); // doesn't work
```

在这里，我们明确指定 `T` 应该是一个字符串，但我们坚持要输入一个数字类型的值。编译器明确指出这是不允许的。

然而，你可以更具体地指定 `T` 应该是什么类型。通过输入以下内容，你确保 `T` 是 `Array` 类型，因此你输入的任何类型的值都必须遵循这一规定：

```ts
function method<T>(arg: T[]): T[] {
 console.log(arg.length); // Array has a .length, so no more error
 return arg;
}

class A extends Array {
}

class Person {
}

var p = new Array<Person>();
var person = new Person();
var a = new A();

method<Person>(p);
method<A>(a);
method<Person>(person);
```

在这种情况下，我们决定 `T` 应该是 `Person` 或 `A` 类型，并且我们还看到输入需要是数组类型：

```ts
function method<T>(arg: T[]) {}
```

因此，输入单个对象是不允许的。那么我们为什么要这样做呢？在这种情况下，我们希望确保某些方法是可用的，比如 `.length`，并且在某一时刻，我们不在乎我们是在操作 `A` 类型还是 `Person` 类型的东西。

你还可以决定你的类型 `T` 应该遵循这样一个接口：

```ts
interface Shape {
 area(): number;
}

class Square implements Shape {
 area() { return 1; }
}

class Circle implements Shape {
 area() { return 2; }
}

function allAreas<T extends Shape>(...args: T[]): number {
 let total = 0;
 args.forEach (x => {
 total += x.area();
 });
 return total;
}

allAreas(new Square(), new Circle());
```

以下行限制了 `T` 可以是什么：

```ts
T extends Shape
```

正如你所看到的，如果你有许多不同数据类型可以关联的共同行为，泛型是非常强大的。你可能最初不会编写自己的泛型代码，但了解正在发生的事情是很好的。

# 类、接口和类继承

现在我们已经概述了 TypeScript 最相关的部分，是时候看看如何将所有内容组合起来构建 TypeScript 类了。这些类是 TypeScript 和 Angular 应用程序的构建模块。

尽管名词类在 JavaScript 中是一个保留字，但语言本身从未对传统的面向对象的类有过实际的实现，就像 Java 或 C#等其他语言那样。JavaScript 开发人员过去常常模仿这种功能，利用函数对象作为构造函数类型，然后使用 new 运算符对其进行实例化。其他常见的做法，比如扩展我们的函数对象，是通过应用原型继承或使用组合来实现的。

现在，我们有了一个实际的类功能，足够灵活和强大，可以实现我们应用程序所需的功能。我们已经有机会在上一章中了解类。现在让我们更详细地看一下它们。

# 类的解剖-构造函数、属性、方法、getter 和 setter

以下代码片段说明了一个类的结构。请注意，类的属性成员首先出现，然后我们包括一个构造函数和几个方法和属性访问器。它们中没有一个使用保留字 function，并且所有成员和方法都正确地用类型进行了注释，除了构造函数：

```ts
class Car {
 private distanceRun: number = 0;
 color: string;

 constructor(public isHybrid: boolean, color: string = 'red') {
 this.color = color;
 }

 getCasConsumsption(): string {
 return this.ishybrid ? 'Very low' : 'Too high!';
 }

 drive(distance: number): void {
 this.distanceRun += distance;
 }

 static honk(): string {
 return 'HOOONK!';
 }

 get distance(): number {
 return this.distanceRun;
 }
}
```

这个类的布局可能会让我们想起我们在第一章中构建的组件类，*在 Angular 中创建我们的第一个组件*。基本上，类语句包含了我们可以分解为的几个元素。

+   **成员**：`Car`类的任何实例都将具有两个属性-color 类型为字符串，`distanceRun`类型为数字，它们只能从类内部访问。如果我们实例化这个类，`distanceRun`或任何其他标记为私有的成员或方法，它们将不会作为对象 API 的一部分公开。

+   **构造函数**：构造函数在创建类的实例时立即执行。通常，我们希望在这里使用构造函数签名中提供的数据初始化类成员。我们还可以利用构造函数签名本身来声明类成员，就像我们在`isHybrid`属性中所做的那样。为此，我们只需要使用 private 或 public 等访问修饰符作为构造函数参数的前缀。与我们在前面的部分中分析函数时看到的一样，我们可以定义剩余参数、可选参数或默认参数，就像在前面的示例中使用颜色参数时一样，当它没有明确定义时会回退到红色。

+   **方法**：方法是一种特殊类型的成员，表示一个函数，因此可以返回或不返回一个类型化的值。基本上，它是对象 API 的一部分的函数。方法也可以是私有的。在这种情况下，它们基本上用作类的内部范围内的辅助函数，以实现其他类成员所需的功能。

+   **静态成员**：标记为静态的成员与类相关联，而不是与该类的对象实例相关联。这意味着我们可以直接使用静态成员，而不必首先实例化对象。事实上，静态成员无法从对象实例中访问，因此它们无法使用 this 访问其他类成员。这些成员通常作为辅助或工厂方法包含在类定义中，以提供与任何特定对象实例无关的通用功能。

+   **属性访问器**：在 ES5 中，我们可以使用`Object.defineProperty`以非常冗长的方式定义自定义 setter/getter。现在，事情变得更简单了。为了创建属性访问器（通常指向内部私有字段，如所提供的示例），我们只需要使用以 set（使其可写）和 get（使其可读）命名的类型化方法前缀作为我们要公开的属性。

作为个人练习，为什么不将前面的代码片段复制到游乐场页面（[`www.typescriptlang.org/Playground`](http://www.typescriptlang.org/Playground)）并执行它呢？我们甚至可以在类定义之后直接附加此片段，运行代码并在浏览器的开发者工具控制台中检查输出，看`Car`类的实例对象如何运行。

```ts
var myCar = new Car(false);
console.log(myCar.color);  // 'red'
// Public accessor returns distanceRun:
console.log(myCar.distance)  // 0
myCar.drive(15);
console.log(myCar.distance);  // 15 (0 + 15)
myCar.drive(21);
console.log(myCar.distance);  // 36 (15 + 21)
// What's my carbon footprint according to my car type?
myCar.getGasConsumption();  // 'Too high!'
Car.honk();  // 'HOOONK!' no object instance required
```

我们甚至可以执行一个额外的测试，并在我们的代码中添加以下非法语句，尝试访问私有属性`distanceRun`，甚至通过 distance 成员应用一个值，而该成员没有 getter。

```ts
console.log(myCar.distanceRun);
myCar.distance = 100;
```

在将这些代码语句插入到代码编辑器中后，红色的下划线会提示我们正在尝试做一些不正确的事情。尽管如此，我们可以继续转译和运行代码，因为 ES5 将遵守这些做法。总的来说，如果我们尝试在这个文件上运行`tsc`编译器，运行时将退出并显示以下错误跟踪：

```ts
example_26.ts(21,7): error TS1056: Accessors are only available when targeting ECMAScript 5 and higher example_26.ts(29,13): error TS2341: Property 'distanceRun' is private and only accessible within class 'Car'
```

# 带有访问器的构造函数参数

通常，在创建一个类时，你需要给它命名，定义一个构造函数，并创建一个或多个后备字段，就像这样：

```ts
class Car {
 make: string;
 model: string;
 constructor(make: string, model: string) {
 this.make = make;
 this.model = model;
 }
}
```

对于每个你需要添加到类中的字段，通常需要做以下操作：

+   在构造函数中添加一个条目

+   在构造函数中添加一个赋值

+   创建后备字段

这真的很无聊，也不太高效。TypeScript 已经做到了，所以我们不需要通过在构造函数参数上使用访问器来输入后备字段。我们现在可以输入：

```ts
constuctor( public make: string, private model: string ) {}
```

给参数添加一个公共访问器意味着它将创建一个公共字段，给它一个私有访问器意味着它将为我们创建一个私有字段，就像这样：

```ts
class Car {
 public make: string;  // creating backing field
 private model: string;

 constructor(make: string, model: string) {
 this.make = make;  //doing assignment
 this.model = model;
 }
}
```

尝试访问这些字段会像这样：

```ts
var car = new Car('Ferrari', 'F40');
car.make  // Ferrari
car.model  // not accessible as it is private
```

在 ES5 中，我们没有字段的概念，所以它消失了，但是构造函数中的赋值仍然存在：

```ts
function Car(make) {
 this.make = make;
 this.model = model;
}
```

但是，在 TypeScript 中，你再也不需要做任何这些事情了。

```ts
class Car {
 constructor(public make: string, public model: string) {}
}
```

正如你所看到的，超过一半的代码消失了；这确实是 TypeScript 的一个卖点，因为它可以帮你省去输入大量乏味的代码。

# TypeScript 中的接口

随着应用程序规模的扩大，创建更多的类和结构，我们需要找到方法来确保代码的一致性和规则的遵从。解决一致性和类型验证问题的最佳方法之一就是创建接口。

简而言之，接口是一个定义特定字段模式和任何类型（无论是类、函数签名）的代码蓝图，实现这些接口的类型都应该符合这个模式。当我们想要强制对由工厂生成的类进行严格类型检查时，当我们定义函数签名以确保有效载荷中存在某个类型的属性，或者其他情况时，这就变得非常有用。

让我们开始吧！在这里，我们定义了`Vehicle`接口。`Vehicle`不是一个类，而是任何实现它的类必须遵守的合同模式：

```ts
interface Vehicle {
 make: string;
}
```

任何实现`Vehicle`接口的类必须具有名为`make`的成员，根据此示例，它必须被定义为字符串类型。否则，TypeScript 编译器会抱怨：

```ts
class Car implements Vehicle {
 // Compiler will raise a warning if 'make' is not defined
 make: string;
}
```

因此，接口非常有用，可以定义任何类型必须满足的最小成员集，成为确保代码库一致性的宝贵方法。

重要的是要注意，接口不仅用于定义最小的类模式，还用于定义任何类型。这样，我们可以利用接口的力量来强制存在于类中的某些字段和方法以及后来用作函数参数、函数类型、特定数组中包含的类型以及甚至变量的对象属性。接口也可以包含可选成员，甚至成员。

让我们创建一个例子。为此，我们将所有接口类型的前缀都加上`I`（大写）。这样，在引用它们时，使用我们的 IDE 代码自动完成功能会更容易找到它们的类型。

首先，我们定义了一个`Exception`接口，该接口模拟了一个具有强制消息属性成员和可选`id`成员的类型：

```ts
interface Exception {
 message: string;
 id?: number;
}
```

我们也可以为数组元素定义接口。为此，我们必须定义一个仅有一个成员的接口，定义索引为数字或字符串（用于字典集合），然后定义我们希望该数组包含的类型。在这种情况下，我们希望创建一个包含`Exception`类型的数组的接口。这是一个包含字符串消息属性和可选 ID 号成员的类型，就像我们在前面的例子中说的那样：

```ts
interface ExceptionArrayItem {
 [index: number]: IException;
}
```

现在，我们定义了未来类的蓝图，其中包括一个带有类型数组和一个返回类型定义的方法：

```ts
interface ErrorHandler {
 exception: ExceptionArrayItem[];
 logException(message: string; id?: number: void;)
}
```

我们还可以为独立的对象类型定义接口。当定义模板构造函数或方法签名时，这是非常有用的，我们稍后将在本例中看到：

```ts
interface ExceptionHandlerSettings {
 logAllExceptions: boolean;
}
```

最后但并非最不重要的是，在接下来的课程中，我们将实现所有这些接口类型：

```ts
class ErrorHandler implements ErrorHandler {
 exceptions: ExceptionArrayItem[];
 logAllExceptions: boolean;
 constructor(settings: ExceptionHandlerSettings) {
 this.logAllExceptions = settings.logAllExceptions;
 }

 logException(message: string, id?: number): void {
 this.exception.push({ message, id });
 }
}
```

基本上，我们在这里定义了一个错误处理程序类，它将管理一组异常并公开一个方法，通过将它们保存到前述数组中来记录新的异常。这两个元素由`ErrorHandler`接口定义，并且是强制性的。类构造函数期望由`ExceptionHandlerSettings`接口定义的参数，并使用它们来填充异常成员，其类型为`Exception`。在不带有有效载荷中的`logAllExceptions`参数的情况下实例化`ErrorHandler`类将触发错误。

到目前为止，我一直在解释接口，就像我们在其他高级语言中习惯看到的那样，但是 TypeScript 中的接口是经过增强的；让我通过以下代码来举例说明：

```ts
interface A {
 a
}

var instance = <A>{ a: 3 };
instance.a = 5;
```

在这里，我们声明了一个接口，但同时也在这里从接口创建了一个实例：

```ts
var instance = <A>{ a: 3 };
```

这很有趣，因为这里没有涉及到类。这意味着编写一个模拟库是小菜一碟。让我们稍微解释一下我们所说的模拟库。当你在开发代码时，你可能会先考虑接口，然后再考虑具体的类。这是因为你知道需要存在哪些方法，但可能还没有确定这些方法应该如何执行任务。想象一下，你正在构建一个订单模块。你的订单模块中有逻辑，你知道在某个时候需要与一个数据库服务进行通信，这将帮助你保存订单。你为所述数据库服务制定了一个合同，一个接口。你推迟了对该接口的实现。在这一点上，一个模拟库可以创建一个从接口生成的模拟实例。你的代码此时可能看起来像这样：

```ts
class OrderProcessor {
 constructor(private databaseService: DatabaseService) {}

 process(order) {
 this.databaseService.save(order);
 }
}

interface DatabaseService {
} 

let orderProcessor = new OrderProcessor(mockLibrary.mock<DatabaseService>());
orderProcessor.process(new Order());
```

因此，此时的模拟使我们能够推迟对`DatabaseService`的实现，直到我们完成了`OrderProcessor`的编写。它还使`OrderProcessor`的测试体验变得更好。在其他语言中，我们需要引入第三方依赖的模拟库，而现在我们可以利用 TypeScript 中的内置构造来实现以下类型：

```ts
var databaseServiceInstance = <DatabaseService>{};
```

这将给我们一个`DatabaseService`的实例。不过，需要警告一下，你需要为你的实例添加一个`process()`方法。你的实例最初是一个空对象。

这不会引起编译器的任何问题；这意味着这是一个强大的功能，但它留给你来验证你创建的东西是否正确。

让我们强调一下 TypeScript 功能的强大之处，通过查看一些更多的代码案例，这样能够模拟掉一些东西就会很值得。让我们重申，在代码中模拟任何东西的原因是为了更容易地进行测试。

假设您的代码看起来像这样：

```ts
class Stuff {
 srv:AuthService = new AuthService();
 execute() {
 if (srv.isAuthenticated())  // do x
 else  // do y
 }
}
```

测试这个的更好方法是确保`Stuff`类依赖于抽象，这意味着`AuthService`应该在其他地方创建，并且我们与`AuthService`的接口而不是具体实现进行交流。因此，我们将修改我们的代码看起来像这样：

```ts
interface AuthService {
 isAuthenticated(): boolean;
}

class Stuff {
 constructor(srv:AuthService) {}
 execute() {
 if (srv.isAuthenticated()) { /* do x */ }
 else { /* do y */ }
 }
}
```

要测试这个类，我们通常需要创建`AuthService`的具体实现，并将其作为`Stuff`实例的参数使用，就像这样：

```ts
class MockAuthService implements AuthService {
 isAuthenticated() { return true; }
}
var srv = new AuthService();
var stuff = new Stuff(srv);
```

然而，如果您想要模拟掉每个想要模拟掉的依赖项的话，这将变得相当乏味。因此，大多数语言中都存在模拟框架。其想法是给模拟框架一个接口，它将从中创建一个具体的对象。您永远不需要创建一个模拟类，就像我们之前所做的那样，但这将是模拟框架内部要做的事情。使用所述的模拟框架，它看起来会像这样：

```ts
var instance = mock<Type>();
```

到目前为止，我们已经说过从接口创建实例是多么容易，就像这样：

```ts
var instance = <A>{ a: 3 };
```

这意味着创建一个模拟框架就像输入以下内容一样容易：

```ts
function mock<T>(startData) {
 return <T>Object.assign({}, startData);
}
```

并且以以下方式使用它：

```ts
interface IPoint {
 x;
 y;
}

class Point implements IPoint {
 x;
 y;
}
var point = mock<IPoint>({ x: 3 });
console.log(point);
```

让我们通过强调类可以实现多个接口，但也可以让接口变得更加强大并且大大简化测试来总结一下关于接口的这一部分。

# 通过类继承扩展类

就像类可以由接口定义一样，它也可以扩展其他类的成员和功能，就好像它们是自己的一样。我们可以通过在类名后添加关键字`extends`，包括我们想要继承其成员的类的名称，使一个类继承自另一个类。

```ts
class Sedan extends Car {
 model: string;
 constructor(make: string, model: string) {
 super(maker);
 this.model = model;
 }
}
```

在这里，我们从一个父类`Car`扩展，该类已经公开了一个 make 成员。我们可以填充父类已定义的成员，甚至通过执行`super()`方法执行它们自己的构造函数，该方法指向父构造函数。我们还可以通过附加具有相同名称的方法来覆盖父类的方法。尽管如此，我们仍然能够执行原始父类的方法，因为它仍然可以从 super 对象中访问。回到接口，它们也可以从其他接口继承定义。简而言之，一个接口可以从另一个接口继承。

作为一种谨慎的提醒，ES6 和 TypeScript 不支持多重继承。因此，如果您想从不同的来源借用功能，您可能希望改用组合或中间类。

# TypeScript 中的装饰器

装饰器是一种非常酷的功能，最初由 Google 在 AtScript（TypeScript 的超集，最终于 2015 年初合并到 TypeScript 中）中提出，并且也是 ECMAScript 7 当前标准提案的一部分。简而言之，装饰器是一种向类声明添加元数据的方式，供依赖注入或编译指令使用（[`blogs.msdn.com/b/somasegar/archive/2015/03/05/typescript-lt-3-angular.aspx`](http://blogs.msdn.com/b/somasegar/archive/2015/03/05/typescript-lt-3-angular.aspx)）。通过创建装饰器，我们正在定义可能对我们的类、方法或函数的行为产生影响，或者仅仅改变我们在字段或参数中定义的数据的特殊注释。在这个意义上，装饰器是一种强大的方式，可以增强我们类型的本机功能，而不需要创建子类或从其他类型继承。

这是 TypeScript 最有趣的功能之一。事实上，在 Angular 中设计指令和组件或管理依赖注入时，它被广泛使用，我们将从第五章 *使用管道和指令增强我们的组件*开始看到。

装饰器可以很容易地通过其名称的`@`前缀来识别，它们通常位于它们装饰的元素的上方，包括方法负载或不包括方法负载。

我们可以定义最多四种不同类型的装饰器，具体取决于每种类型所要装饰的元素：

+   类装饰器

+   属性装饰器

+   方法装饰器

+   参数装饰器

让我们逐个看一下！

# 类装饰器

类装饰器允许我们增强一个类或对其任何成员执行操作，并且装饰器语句在类被实例化之前执行。

创建一个类装饰器只需要定义一个普通函数，其签名是指向我们想要装饰的类的构造函数的指针，类型为函数（或任何其他继承自函数的类型）。正式声明定义了一个`ClassDecorator`，如下所示：

```ts
declare type ClassDecorator = <TFunction extends Function>(Target: TFunction) => TFunction | void;
```

是的，很难理解这些胡言乱语的含义，对吧？让我们通过一个简单的例子来把一切放在上下文中，就像这样：

```ts
function Banana(target: Function): void {
 target.prototype.banana = function(): void {
 console.log('We have bananas!');
 }
}

@Banana
class FruitBasket {
 constructor() {
 // Implementation goes here...
 }
}
var basket = new FruitBasket();
basket.banana();  // console will output 'We have bananas!'
```

正如我们所看到的，我们通过正确地使用`Banana`装饰器，获得了一个在`FruitBasket`类中原本未定义的`banana()`方法。不过值得一提的是，这实际上不会编译通过。编译器会抱怨`FruitBasket`没有`banana()`方法，这是理所当然的。TypeScript 是有类型的。在 ES5 中，我们可以做任何我们想做的事情，任何错误都会在运行时被发现。所以在这一点上，我们需要告诉编译器这是可以的。那么，我们该如何做呢？一种方法是在创建篮子实例时，像这样给它赋予任意类型：

```ts
var basket: any = new FruitBasket();
basket.banana();
```

我们在这里所做的是将变量 basket 主动赋予`any`类型，从而抵制 TypeScript 编译器将类型推断为`FruitBasket`的冲动。通过使用 any 类型，TypeScript 无法知道我们对它所做的是否正确。另一种实现相同效果的方法是这样类型：

```ts
var basket = new FruitBasket();
(basket as any).banana();
```

在这里，我们使用`as`运算符进行了即时转换，从而告诉编译器这是可以的。

# 扩展类装饰器函数签名

有时，我们可能需要在实例化时自定义装饰器的操作方式。别担心！我们可以设计带有自定义签名的装饰器，然后让它们返回一个与我们在设计不带参数的类装饰器时定义的相同签名的函数。作为一个经验法则，带参数的装饰器只需要一个函数，其签名与我们想要配置的参数匹配。这样的函数必须返回另一个函数，其签名与我们想要定义的装饰器的签名匹配。

下面的代码片段展示了与前面例子相同的功能，但它允许开发人员自定义问候消息：

```ts
function Banana(message: string) {
 return function(target: Function) {
 target.prototype.banana = function(): void {
 console.log(message);
 }
 }
}

@Greeter('Bananas are yellow!')
class FruitBasket {
 constructor() {
 // Implementation goes here...
 }
}
var basket = new FruitBasket();
basket.banana();  // console will output 'Bananas are yellow'
```

# 属性装饰器

属性装饰器是用于应用于类字段的，并且可以通过创建一个`PropertyDecorator`函数来轻松定义，其签名接受两个参数：

+   **Target**：这是我们想要装饰的类的原型

+   **Key**：这是我们想要装饰的属性的名称

特定类型的装饰器的可能用例可能包括日志记录

在实例化此类的对象的类字段分配的值，甚至对这些字段的数据更改做出反应。让我们看一个实际的例子，涵盖了这两种行为：

```ts
function Jedi(target: Object, key: string) {
 var propertyValue: string = this[key];
 if (delete this[key]) {
 Object.defineProperty(target, key, {
 get: function() {
 return propertyValue;
 }, 
 set: function(newValue){
 propertyValue = newValue;
 console.log(`${propertyValue} is a Jedi`);
 }
 });
 }
}

class Character {
 @Jedi
 name: string;
}

var character = new Character();
character.name = 'Luke';  // console outputs 'Luke is a Jedi'
character.name = 'Yoda';  // console outputs 'Yoda is a Jedi'
```

这里适用于带参数的类装饰器的相同逻辑，尽管返回函数的签名略有不同，以匹配我们已经看到的无参数装饰器声明的签名。

以下示例描述了我们如何记录给定类属性的更改，并在发生这种情况时触发自定义函数：

```ts
function NameChanger(callbackObject: any): Function {
 return function(target: Object, key: string): void {
 var propertyValue: string = this[key];
 if (delete this[key]) {
 Object.defineProperty(target, key, {
 get: function() {
 return propertyValue;
 }, 
 set: function(newValue) {
 propertyValue = newValue;
 callbackObject.changeName.call(this, propertyValue);
 }
 });
 }
 }
}

class Fruit {
 @NameChanger ({
 changeName: function(string,newValue: string): void {
 console.log(`You are now known as ${newValue}`);
 }
 })
 name: string;
}

var character = new Character();
character.name 'Anakin';  // console: 'You are now known as Anakin'
character.name = 'Lord Vader';  //console: 'You are now known as Lord Vader'
```

# 方法装饰器

这些特殊的装饰器可以检测、记录并干预方法的执行方式。为此，我们只需要定义一个`MethodDecorator`函数，其有效负载接受以下参数：

+   **Target**：这被定义为一个对象，代表被装饰的方法。

+   **Key**：这是给定方法的实际名称的字符串。

+   **Value**：这是给定方法的属性描述符。实际上，它是一个哈希对象，其中包含了一个名为 value 的属性，其中包含对方法本身的引用。

让我们看看如何在实际示例中利用`MethodDecorator`函数。在后来的 TypeScript 版本中，这种语法已经改变。然而，想法是在方法执行之前和之后拦截。那么，为什么你想这样做呢？嗯，有一些有趣的情况：

+   您想了解有关方法如何被调用的更多信息，例如`args`，结果等

+   您想知道某个方法运行了多长时间

让我们为每种情况创建一个装饰器：

```ts
function Log(){
 return function(target, propertyKey: string, 
 descriptor: PropertyDescriptor) {
 var oldMethod = descriptor.value;
 descriptor.value = function newFunc( ...args:any[]){
 let result = oldMethod.apply(this, args);
 console.log(`${propertyKey} is called with ${args.join(',') and
 result ${result}`);
 return result;
 }
 }
}

class Hero {
 @Log()
 attack(...args:[]) { return args.join(); }
}

var hero = new Hero();
hero.attack();
```

在这里，我们正在讨论`descriptor.value`，其中包含我们实际的函数，正如你所看到的，我们：

+   保存对旧方法的引用

+   我们通过替换`descriptor.value`指向的内容来重新定义方法

+   在我们的新函数内部执行旧方法

+   我们记录使用了什么参数以及结果如何变化

到目前为止，我们已经解释了如何向方法添加日志信息，但还有另一种情况我们也想描述一下，即测量执行时间。我们可以使用与之前类似的方法，但有一些细微的差别：

```ts
function Timer(){
 return function(target, propertyKey: string, descriptor: PropertyDescriptor) {
 var oldMethod = descriptor.value;
 descriptor.value = function() {
 var start = new Date();
 let result = oldMethod.apply(this, args);
 var stop = new Date();
 console.log(`Method took ${stop.getMilliseconds() - 
 start.getMilliseconds()}ms to run`);
 return result;
 }
 }
}
```

我们仍然做了很多相同的事情，但让我们用几个要点来总结一下：

+   保存对旧方法的引用

+   重新定义`descriptor.value`

+   在方法执行前启动计时器

+   执行方法

+   在方法执行后停止计时器

请记住，装饰器函数的作用域限定在目标参数中表示的类中，因此我们可以利用这一点来为类增加我们自己的自定义成员。在这样做时要小心，因为这可能会覆盖已经存在的成员。在本例中，我们不会对此进行任何尽职调查，但在将来的代码中要小心处理。方法装饰器是非常强大的，但不要总是使用它们，而是在像前面那样它们发挥作用的情况下使用。

# 参数装饰器

我们最后一轮的装饰器将涵盖`ParameterDecorator`函数，该函数可以访问位于函数签名中的参数。这种装饰器并不意图改变参数信息或函数行为，而是查看参数值，然后在其他地方执行操作，例如，记录日志或复制数据。`ParameterDecorator`函数接受以下参数：

+   **Target**：这是包含被装饰参数的函数的对象原型，通常属于一个类

+   **Key**：这是包含装饰参数的函数签名的函数的名称

+   **参数索引**：这是装饰器应用的参数数组中的索引

以下示例显示了参数装饰器的工作示例：

```ts
function Log(target: Function, key: string, parameterIndex: number) {
 var functionLogged = key || target.prototype.constructor.name;
 console.log(`
 The parameter in position 
 ${parameterIndex} at ${functionLogged} has been decorated`
 );
}

class Greeter {
 greeting: string;
 constructor (@Log phrase: string) {
 this.greeting = phrase;
 }
}
// The console will output right after the class above is defined:
// 'The parameter in position 0 at Greeter has been decorated'
```

您可能已经注意到`functionLogged`变量的奇怪赋值。这是因为目标参数的值将根据被装饰参数的函数而变化。因此，如果我们装饰构造函数参数或方法参数，它是不同的。前者将返回对类原型的引用，后者将只返回构造函数。当装饰构造函数参数时，key 参数也将是未定义的。

正如我们在本节开头提到的，参数装饰器并不意味着修改装饰的参数的值或更改这些参数所在的方法或构造函数的行为。它们的目的通常是记录或准备容器对象，以通过更高级别的装饰器（如方法或类装饰器）实现额外的抽象层或功能。这种情况的典型案例包括记录组件行为或管理依赖注入，正如我们将在第五章中看到的，“通过管道和指令增强我们的组件”。

# 使用模块组织我们的应用程序

随着我们的应用规模和规模的增长，总会有一个时候，我们需要更好地组织我们的代码，使其可持续且更具重用性。模块是对这种需求的响应，所以让我们看看它们是如何工作的，以及我们如何在应用程序中实现它们。模块可以是内部的或外部的。在本书中，我们将主要关注外部模块，但现在概述这两种类型是一个好主意。

# 内部模块

简而言之，内部模块是包含一系列类、函数、对象或变量的单例包装器，其范围在内部，远离全局或外部范围。我们可以通过在我们希望从外部访问的元素前加上关键字`export`来公开模块的内容，就像这样：

```ts
module Greetings {
 export class Greeting {
 constructor(public name: string) {
 console.log(`Hello ${name}`);
 }
 }

 export class XmasGreeting {
 constructor(public name: string){
 console.log(`Merry Xmas ${name}`);
 }
 }
}
```

我们的“问候”模块包含两个类，可以通过导入模块并通过其名称访问要使用的类来从模块外部访问：

```ts
import XmasGreeting = Greeting.XmasGreeting;
var xmasGreeting = XmasGreeting('Joe');
// console outputs 'Merry Xmas Joe'
```

在查看前面的代码之后，我们可以得出结论，内部模块是将元素分组和封装在命名空间上下文中的一种好方法。我们甚至可以将我们的模块拆分成几个文件，只要模块声明在这些文件中保持相同的名称。为了做到这一点，我们将希望使用引用标签引用我们散布在这个模块中的不同文件中的对象：

```ts
/// <reference path="greetings/XmasGreeting.ts" />
```

然而，内部模块的主要缺点是，为了使它们在我们的 IDE 领域之外工作，我们需要将它们全部放在同一个文件或应用程序范围内。我们可以将所有生成的 JavaScript 文件作为脚本插入到我们的网页中，利用诸如 Grunt 或 Gulp 的任务运行器，或者甚至使用 TypeScript 编译器中的`--outFile`标志，将工作区中找到的所有`.ts`文件编译成一个单独的捆绑包，使用引用标签到所有其他模块作为我们编译的起点的引导文件：

```ts
tsc --outFile app.js module.ts
```

这将编译所有的 TypeScript 文件，遵循引用标签引用的依赖文件的路径。如果我们忘记以这种方式引用任何文件，它将不会包含在最终的构建文件中，所以另一个选项是在编译命令中列出包含独立模块的所有文件，或者只需添加一个包含模块综合列表的`.txt`文件来捆绑。或者，我们可以只使用外部模块。

# 外部模块

外部模块基本上是我们在构建旨在增长的应用程序时所需要的解决方案。基本上，每个外部模块都在文件级别上工作，其中每个文件都是模块本身，模块名称将与没有`.js`扩展名的文件名匹配。我们不再使用模块关键字，每个标有导出前缀的成员将成为外部模块 API 的一部分。在上一个示例中描述的内部模块一旦方便地保存在`Greetings.ts`文件中，将变成这样：

```ts
export class Greeting {
 constructor(public name: string) {
 console.log(`Hello ${name}`);
 }
}

export class XmasGreeting {
 constructor(public name: string) {
 console.log(`Merry Xmas ${name}`);
 }
}
```

导入此模块并使用其导出的类需要以下代码：

```ts
import greetings = require('Greetings');
var XmasGreetings = greeting.XmasGreetings();
var xmasGreetings = new XmasGreetings('Pete');
// console outputs 'Merry Xmas Pete'
```

显然，传统 JavaScript 不支持 require 函数，因此我们需要告诉编译器我们希望在目标 JavaScript 文件中实现该功能。幸运的是，TypeScript 编译器在其 API 中包含了`--module`参数，因此我们可以为我们的项目配置所选择的依赖加载器：`commonjs`用于基于 node 的导入，`amd`用于基于 RequireJS 的导入，`umd`用于实现通用模块定义规范的加载器，或者 system 用于基于 SystemJS 的导入。我们将在本书中重点介绍 SystemJS 模块加载器：

```ts
tsc --outFile app.js --module commonjs
```

生成的文件将被适当地填充，因此模块可以使用我们选择的模块加载器跨文件加载依赖项。

# TypeScript > 1.5 的 ES6 模块

在你的 Angular 项目中使用模块的方式是使用 ES6 语法的外部模块，所以让我们了解一下这意味着什么的基础知识。如本节前面提到的，每个模块一个文件，我们可以使用`export`关键字导出它。然而，你如何消费依赖在语法上有所不同；让我们通过创建一个 ES6 模块`service.ts`和另一个模块`consumer.ts`来说明这一点，后者旨在消费前者：

```ts
//service.ts
export class Service {
 getData() {} 
}

//consumer.ts import {} from './service';
```

这里有两件事要注意，在`consumer.ts`文件中：

+   使用大括号`{}`导入

+   使用 from 关键字来找到我们的文件

大括号`{}`给了我们选择想要导入的构造的机会。想象一下如果`service.ts`更复杂，像这样：

```ts
//service-v2.ts
export class Service {
 getData(){}
}

export const PI = 3.14
```

作为消费者，我们现在可以选择这样导入`Service`和/或`PI`：

```ts
//consumer-v2.ts
import { Service, PI } from './service-v2'
```

然而，也可以使用另一种语法来导出你的构造。到目前为止，我们一直在为每个想要导出的东西输入`export`；在我们的`service.ts`的第三部分`service-v3.ts`中，我们可以这样输入它：

```ts
//service-v3.ts
class Service {}

const PI = 3.14;

export { Service, PI }
```

进行导出的第三种方式是默认的`export`。有一个`default`关键字，这意味着我们在导入时不必使用大括号`{}`：

```ts
//service-v4.ts
export default function(a, b) {
 return a + b;
}

//consumer-v3.ts import service from './service-v4';

```

# 总结

这绝对是一篇长篇大论，但这篇关于 TypeScript 的介绍绝对是必要的，以便理解 Angular 许多最精彩部分背后的逻辑。它让我们有机会不仅介绍语言语法，还解释了它作为构建 Angular 框架的首选语法成功背后的原因。我们审查了它的类型架构，以及如何使用各种参数化签名的高级业务逻辑设计函数，甚至发现了如何通过使用强大的新箭头函数来绕过与作用域相关的问题。这一章最相关的部分可能是类、方法、属性和访问器的概述，以及我们如何通过接口处理继承和更好的应用程序设计。模块和装饰器是本章探讨的其他重要特性，正如我们很快将看到的那样，对这些机制的充分了解对于理解 Angular 中的依赖注入是至关重要的。

有了这些知识，我们现在可以恢复对 Angular 的调查，并自信地面对组件创建的相关部分，比如样式封装、输出格式化等等。第四章，*在我们的组件中实现属性和事件*，将使我们接触到高级模板创建技术、数据绑定技术、指令和管道。所有这些特性将使我们能够将新获得的 TypeScript 知识付诸实践。
