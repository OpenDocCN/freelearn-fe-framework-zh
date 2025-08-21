# 第二章：ECMAScript 和 TypeScript 速成课程

第一章，*快速入门*，是一个杂乱的主题混合，可能看起来有点松散，但是以这种方式呈现是为了铺设与前端 Web 应用程序开发相关的一大片材料和主题，显然是为了开始你成为 Angular 大师的冒险。从这一点开始，每一章都将尽可能保持专注，并且专门致力于特定的覆盖领域。随着你逐渐深入各章，你会发现它们变得越来越技术化。这是一个自然的进步，不必害怕，因为你可能还记得，我在第一章，*快速入门*中对你做出的承诺之一是不要陷入技术细节以至于达到收益递减的地步。换句话说，不会有任何对我们目的没有任何价值的深奥技术的废话。相反，我们会变得尽可能技术化——不多，也不少。此外，材料将以一种引人入胜的方式呈现，你将以最小的努力获得最大的保留可能性。

不过，请不要认为这意味着你不必努力。和生活中的任何其他事情一样，你想要变得更优秀，就需要付出更多的努力。种瓜得瓜，种豆得豆。话虽如此，本章和下一章将是逐渐升级到接下来的技术深入探讨的过渡阶段——有点像在我们开始在整本书中运用 Angular 技术之前的热身。

本章将涵盖以下主题：

+   JavaScript 和 TypeScript 之间的关系。

+   TypeScript 的速成课程

# （快速）路线图

本章是关于 TypeScript 的速成课程，旨在为已经熟悉 JavaScript 的开发人员快速过渡到 TypeScript 提供帮助。

正如在第一章中提到的，TypeScript 是我们在本书中处理 Angular 特定事务时将使用的语言，因此本章将作为您准备好使用 Angular 进行 Web 开发的程序化部分。您可以将第三章《Bootstrap-网格布局和组件》视为本章的表兄弟，因为它的目标类似，但是针对的是呈现方面（即网页布局），而不是程序化方面。第二章《ECMAScript 和 TypeScript 速成课程》和第三章《Bootstrap-网格布局和组件》将一起完成构建客户端 Web 应用程序的先决条件-无论客户端 Web 应用程序框架如何，但也特别适用于基于 Angular 的应用程序。从第五章《Flex-layout-Angular 的强大响应式布局引擎》开始，几乎都将是以 Angular 为中心。简而言之，这是本章和下一章的路线图。让我们开始热身我们的技术肌肉！

# JavaScript 和 TypeScript 之间的关系

JavaScript 和 TypeScript 是密不可分的。因此，虽然本章涵盖了 ECMAScript 和 TypeScript 两种技术，但它们足够相似，以至于本章可以同时涵盖两者。它们彼此有多相似？嗯，大部分时间，您可以将 TypeScript 视为 JavaScript 的超集。它们之间关系最有用的描述是：TypeScript 是一种带有许多强大功能和可选类型的严格类型语言，通过其转译器，它变成了普通的 JavaScript。这对开发人员来说非常重要，并带来了几个优势；这足够引人注目，以至于谷歌的 Angular 团队决定从 JavaScript 转换到 TypeScript 来开发 Angular 本身。我们将很快介绍转译器是什么，以及使用 TypeScript 的优势是什么。

# JavaScript 的一系列幸运事件

在我们深入技术部分和代码之前，值得快速看一下 JavaScript 的演变以及导致需要像 TypeScript 这样的语言的一些驱动因素。此外，就像 Angular 的命名混乱在开发社区中引起了一些困惑，自从 20 多年前诞生以来，JavaScript 的版本过去更加混乱，因此我想尝试澄清一些关于 JavaScript 版本命名的混乱。更重要的是，我想讨论一下我所说的 JavaScript 的一系列幸运事件。这将有助于为我们在本书的其余部分一起涵盖的大部分内容设定节奏。

我必须承认，我喜欢使用 JavaScript。我一直喜欢这种语言，不是因为语言本身，而是因为它让我们能够使网络生动起来，而无需其他插件，如 Flash 或 Shockwave。然而，近年来，我喜欢使用这种语言的原因还有一些额外的原因，而我喜欢 JavaScript 的确切原因正是我即将介绍的一系列幸运事件。话虽如此，我在行业中有一些朋友持相反的观点，他们认为 JavaScript 是一种“玩具语言”，并且更倾向于使用 Java 和 C#等语言，尽量避免使用 JavaScript，直到他们不得不勉强为客户端编写一些代码。这些老手对 JavaScript 的使用通常不会超出使用 jQuery 库将点击事件绑定到函数调用（以提交表单数据到他们的 Java 或 C# API）。当然，大约十年前，由于 JavaScript 只能在客户端（即浏览器）上运行，没有那么多的库，也没有真正高性能的运行时，因此 JavaScript 并不像 Java 或 C#那样强大。所有这些都将因为一系列幸运事件而发生改变，具体来说，有三个。让我们快速回顾一下。

# Chromium 项目

第一个是谷歌的 Chromium 项目。2008 年 9 月，谷歌发布了 Chrome V8，这是一个高性能的 JavaScript 引擎。谷歌的 Chrome V8 极大地提高了 JavaScript 代码的执行方式。这是一个非常成功的项目，它使其他技术得以实现，包括真正立即和永远改变了 JavaScript 未来的技术：Node.js（简称 Node）。

# JavaScript 框架

JavaScript 的统治地位作为最重要的 Web 应用程序编程语言，甚至可能是移动应用程序和甚至桌面应用程序的事件系列中的第二个事件，是 JavaScript 框架的爆炸式增长。自 2010 年以来，开发世界对创建基于 JavaScript 的框架的渴望已经变得非常疯狂，不仅用于客户端 Web 应用程序开发（如 Ember，Knockout 和 React），还用于服务器端库（再次感谢 Node），用于创建原生移动应用程序的框架（如 Ionic，React Native 和 Native Script），以及用于开发桌面应用程序的框架（如 Meteor 和 Electron）。我在这段话中没有提到 Angular，因为我们已经知道 Angular 可以用于构建跨平台应用程序，涵盖了浏览器、桌面和原生移动应用程序的所有三个领域。

# ECMAScript 2015

幸运事件系列中的第三个事件是 ECMAScript 2015 的发布。ECMAScript 是 JavaScript 标准的官方名称。尽管主要版本发布数量增加，但 JavaScript 语言多年来基本上没有发生变化。这是由于影响力玩家之间的分歧（最好不要提），导致语言的发展分裂和停滞不前。

总之，这是对 JavaScript 当前状态及其生态系统的一个快速概述。JavaScript 的生态系统非常庞大，需要写好几本书来覆盖它。例如，我们甚至没有提到可视化 JavaScript 库。JavaScript 有数百甚至数千个可用于项目的库，我们甚至无法开始覆盖。但是，JavaScript 生态系统中有一部分我们一定会涵盖：单元测试。您可能知道单元测试的重要性，并且可能已经使用诸如 JUnit、NUnit、RSpec 等框架为服务器端代码编写了单元测试，具体取决于您使用的编程语言。但是，客户端上的单元测试同样重要，大多数开发人员并没有进行单元测试，即使他们可能已经为服务器端编写了单元测试脚本。在第十三章中，*单元测试*，您将学习如何为客户端编写单元测试，特别是如何编写它们来测试您的 Angular 应用程序。我们将一起介绍的两个框架是 Jasmine（一种流行的单元测试框架）和 Karma（一个具有用于测试框架的插件的测试运行器，如 Jasmine）。

有了本章的技术前言，让我们戴上潜水装备，潜入 TypeScript 的海洋吧！

# TypeScript 速成课程

TypeScript 对开发人员比 JavaScript 具有许多优势，包括以下内容：

+   纯粹的面向对象

+   可选的静态类型

+   类型推断

+   访问 ECMAScript 功能

+   转译

+   IntelliSense 的强大工具支持

+   您可以构建 Angular 应用程序！

# 转译与编译

开发人员通常可以在编程的上下文中定义编译是什么。定义可能是这样的：编译是将源代码通过另一个称为编译器的程序转换为机器可读代码的过程。这个结果代码通常被称为汇编代码，它是一组本机于机器 CPU 的机器指令，代码是要在其上运行的机器。

另一方面，转译是将用一种语言编写的源代码转换为另一种（或目标）语言的等效代码的过程。虽然这个定义对于讨论来说已经足够好了，但为了完全准确，我们还必须注意到源语言和目标语言实际上可能是同一语言的不同版本（或发布）。对于我们的转译需求，我们将使用 TypeScript 的转译器 tsc，它与 TypeScript 捆绑在一起。我们关心转译的原因是因为我们将在构建 Angular 应用时使用 TypeScript 编写代码。然而，Web 浏览器只有 JavaScript 引擎/解释器，因此我们需要一种将其转译为 JavaScript 的方法。

# 让

`let`和`const`关键字是在 ES6 中引入的。为了讨论它们是什么以及它们如何工作，让我们回顾一下`var`关键字的作用。在 ES6 之前，初始化变量的方式是使用`var`关键字。关于`var`需要记住的两件事是：

+   当您使用`var`在函数体外定义变量时，它将成为全局作用域。这意味着 JavaScript 文件中的所有其他函数都可以访问它。虽然这有时可能很方便，但也可能很危险，因为值可能会被意图之外的函数无意中更改。当多个函数引用相同的变量名时，这种情况是可能的。

+   当您使用`var`在函数内部定义变量时，它将成为局部作用域。与全局作用域变量相反，局部作用域变量只能在创建它们的函数内部访问。这是真实的，无论块作用域如何，因为使用`var`关键字声明的 JavaScript 变量作用域限定在最近的父函数内。

当然，您仍然可以使用`var`来声明和定义变量，因为该关键字尚未被弃用。只是现在您对初始化代码的行为有了更明确的控制，并且使用`let`和`const`后代码的可读性得到了改善，因为在查看 JavaScript 代码时意图是清晰的。

`let`关键字创建了块作用域的局部变量，并且其名称来源于其他具有类似构造的语言，比如 Lisp、Clojure、Scala 和 F#。在这些语言中，使用`let`声明的变量可以被赋值，但不能被改变。然而，在 ES6 中，使用`let`赋值的变量可以被改变；即使如此，无论它们是否被改变，该变量都是一个本地块作用域变量。

如果你觉得这有点令人困惑，你并不孤单。要牢固理解变量作用域的微妙之处并不是仅仅通过阅读就能学会的事情。编程就像学数学（或者学大多数事情一样）：你做得越多，就会变得越好。话虽如此，将所有这些都归纳到你的脑海中的一种方法是看待`var`和`let`之间的这个主要区别：由于在一个函数内可以有多个块，甚至是嵌套块（或子块），使用`let`关键字定义的变量只能在其定义的块内访问，以及从该块的嵌套块中访问。相比之下，使用`var`定义的变量的作用域是整个封闭函数。记住这个主要区别，你就掌握了。

让我们看一些代码来理解`let`关键字的影响，然后我们可以继续讨论`const`关键字：

```ts
let x = 5;
 if (x === 5) {
     let x = 10;
     console.log(x); // this line will output 10 to your console
     // note, x was allowed to be changed
 }
 console.log(x);
 // this line will output 5 to your console
 // because the change to x was made within a block
```

# 常量

`const`关键字创建一个常量。你会很高兴知道，因为你已经经历了理解`let`关键字的痛苦，理解`const`关键字的作用将会非常简单。准备好了吗？在这里……`const`和`let`在它们的作用域工作方式上是相同的。`let`和`const`之间唯一的区别是你不能重新声明一个常量，它的值也不能被改变。就是这样。让我们继续讨论一些数据类型。

# 数据类型

每种编程语言都有数据类型。它们只在可用类型的数量以及类型变量可以保存的值（以及数字类型的值范围）方面有所不同。我不会在本书中探讨强类型与静态类型与弱类型语言之间的哲学辩论（通常称为静态与动态类型）—但由于本章专门讨论 JavaScript 和 TypeScript，我需要简要谈一下它们的类型。

JavaScript 是一种弱类型语言——也就是说它是一种动态语言，而不是静态语言。这意味着 JavaScript 中的变量不绑定到任何特定类型，而是它们的值与类型相关联。变量可以被分配和重新分配给所有可用类型的值。虽然方便，但由于没有编译器检查值是否符合类型引用，因此很难找到错误。这是因为当你使用`var`、`let`或`const`声明变量时，你没有指定关联的类型。

相比之下，TypeScript 是可选的静态类型。这里的关键词是*可选*。TypeScript 是一种静态类型语言，但它不强制你明确注释你的变量所需的类型。这是因为 TypeScript 具有所谓的类型推断，也就是说 TypeScript 运行时将在运行时推断变量的数据类型。这是 TypeScript 的默认行为。现在，这就是可选部分的地方……如果你想严格地给变量加上类型，从而将数据类型绑定到变量而不是与变量的值相关，你必须在变量声明中明确添加类型注释。

这是代码中的情况：

```ts
var num: number = 12345; // this is a statically typed variable
var num = 12345; // this is a dynamically typed variable
```

前两行都是有效的 TypeScript 语法，但它们之间有所不同：

+   第一行是静态类型变量，使用`num`关键字进行注释，由 TypeScript 转译器检查，任何问题都将由它报告

+   第二行，变量声明以 JavaScript 的方式进行（也就是说，没有静态类型的注释），不受检查，任何问题只能在运行时发现。

ES6 有七种数据类型，其中六种被称为原始数据类型，一种被称为引用数据类型（只是称为`Object`）。JavaScript 标准库中还有几种内置数据类型，但由于这不是 JavaScript 的全面覆盖，我们只会在这里涵盖其中一些：你可能会在你的 Angular 开发中使用的那些。

以下是提供的原始数据类型列表：

+   空

+   未定义

+   布尔值

+   数字

+   字符串

+   符号

以下是提供的内置数据类型：

+   日期

+   数组

+   地图

+   集合

# 对象

仅具有原始数据类型和内置复杂数据类型并不足以编写表达性软件，试图模拟现实世界或虚构世界（在游戏中）。解决方案是拥有具有创建自定义对象构造的编程语言。幸运的是，JavaScript，因此 TypeScript，是一种允许创建自定义对象的编程语言。JavaScript 中的对象是一组映射键和值的集合，其中键可以是字符串或符号。这类似于许多其他编程语言的情况，例如 Python 的字典和 Ruby 的哈希。不要仅仅为了技术而变得太技术化（这是我讨厌的事情，也可能是你讨厌的事情），但 JavaScript 不是*经典*面向对象的语言。相反，JavaScript 使用原型继承来创建其他对象，而不是从类定义创建对象的实例。换句话说，JavaScript 没有类的概念。JavaScript 有原型。JavaScript 中的对象直接从其他对象继承。实际上，当您在 JavaScript 中使用大括号创建一个空对象时，这实际上是使用内置对象的`create`方法的语法糖。在 JavaScript 中有几种可用的方法可以创建一个空对象。我们不会在这里涵盖它们所有，但我们将涵盖两种多年来在 JavaScript 中可用的方法，并在 ES6 中提供给我们的方法：

+   使用`Object`构造函数：`var myObject = new Object();`

+   使用大括号语法：`var myObject = {};`

+   使用 ES6 类语法（我们将在接下来的*类*部分介绍语法）

前两种方法创建一个空对象。如果您想在 JavaScript 中轻松创建一个空对象，第二种方法显然是最简单的。然而，第三种方法，ES6 类语法，是我们将在本书中使用的方法。

# JSON

JSON 是 JavaScript 对象表示法的缩写，不是一种数据类型，而是结构化数据。JSON 被用作轻量级数据交换格式，并被许多编程语言使用。我们不仅将在稍后更详细地介绍这一点，而且我们将广泛使用这种格式在我们的 Angular 应用程序和我们为其构建的后端 Web 服务之间传递数据。就像编程语言有数据类型一样，数据交换格式通常也有。以下是 JSON 允许表示的数据类型：

+   字符串

+   数字

+   对象

+   数组

+   布尔

+   空

您可能已经注意到 JavaScript 和 JSON 数据类型之间有很大的重叠。这并非偶然，因为 JSON 是 JavaScript 对象表示法，因此是模仿 JavaScript 的数据类型而建模的。以下是一个包含三个人的姓名和年龄的 JSON 数据的示例（每个都是 JavaScript 对象）：

```ts
{
   “people”: [
     {“name”:”Peter”, “age”:40},
     {“name”:”Paul”, “age”:42},
     {“name”:”Mary”, “age”:38}
   ]
 }
```

在前面的 JSON 示例中，我有`people`作为键，其值是一个包含三个`people`对象的数组。并没有硬性规定说你必须给嵌套结构命名，但这样做会更易读。在这个简单的例子中，你可以省略键而不会丢失数据，就像下一个 JSON 示例所示的那样：

```ts
[
   {“name”:”Peter”, “age”:40},
   {“name”:”Paul”, “age”:42},
   {“name”:”Mary”, “age”:38}
 ]
```

然而，在第一个例子中，我们有`people`键，不仅更容易阅读，而且在代码中更容易处理。当我们在第十二章中为我们的应用编写 RESTful web 服务 API 时，我们将采取第一种方法，为我们的数据集提供键。

关于数据交换格式，这里有一个有趣的注释。虽然有一些可供选择的格式，比如 XML 和 SOAP（简单对象访问协议），但在开发 Web 服务时，JSON 是最受欢迎的格式，它受到 JavaScript 的启发。

如果没有 JavaScript，我们会在哪里呢？我不敢想象。

# JavaScript 运行环境

本章的其余部分有许多代码片段，所以如果您想在阅读本章的过程中尝试材料，最好启动 JavaScript 运行环境。除非您使用 JavaScript IDE，比如 JetBrains 的 WebStorm，否则您有几种选择可以用来测试 JavaScript 代码。以下是其中的三种选择：

+   你可以使用在线的 JavaScript 控制台，比如[`es6console.com/`](https://es6console.com/)。

+   你可以在终端中使用 Node（附录 A 向你展示了如何安装 Node）。

+   你可以在浏览器的开发者工具中使用控制台。例如，我主要使用 Chrome 进行开发，而 Google 有出色的开发者工具。

任何这些选择都可以很好地工作。我更喜欢在 Node 终端中快速地做一些小事情，这也是我用来测试我为本章编写的代码的工具。

# 数组

数组是对象集合的一部分，被称为索引集合。如果你写过一定量的 JavaScript，你一定使用过数组。数组对象可以保存任何有效的 JavaScript 数据类型，并且可以通过调用它们的内置方法（如`push`和`splice`）来增长和缩小。你可以使用`indexOf`方法来搜索数组中值的存在，使用`length`属性来获取数组的长度，等等。创建一个空数组的 JavaScript 语法如下：

```ts
var myDreamCars = [];
```

然后你可以使用数组的内置`push`方法来向数组中添加一个项，就像这样：

```ts
myDreamCars.push("Porsche");
```

或者，你可以一次性地创建数组，就像这样：

```ts
var myDreamCars = ["Porsche", "Mercedes", "Ferrari", "Lamborghini"];
```

`indexOf`方法非常方便，我们肯定会在后面用到它。在继续讲解`TypedArrays`之前，让我们快速介绍一下这个方法。当你需要找到数组中特定项的位置，或者判断它是否存在于数组中时，你可以使用`indexOf`方法。假设我们想要查找 Mercedes 在数组中的位置。我们可以这样搜索：

```ts
var indexOfMercedes = myDreamCars.indexOf("Mercedes");
```

给定我们的`myDreamCars`数组，`indexOf`函数会返回 1。这是因为 JavaScript 中的数组从 0 开始索引，而 Mercedes 在我们的数组中是第二个位置。如果我们要查找的东西不在数组中会怎样呢？让我们看看当我们查找 Corvette 时会发生什么：

```ts
var indexOfMercedes = myDreamCars.indexOf("Corvette");
```

当执行上述行时，`indexOf`函数会返回-1，这表示我们搜索的项没有找到。

# TypedArray

`TypedArray`在 ES6 中使用，尽管它具有与普通 JavaScript 数组对象相同的一些方法，但它与您可能期望的有很大不同。事实上，`TypedArray`根本不是数组。如果您尝试将`TypedArray`传递给`Array.isArray()`，您会发现返回的值是`false`。那么它们是什么呢？`TypedArray`为我们提供了对底层二进制数据缓冲区的视图，并使我们能够访问和操作数据。我们不会在本书中涵盖`TypedArray`，因为我们不会使用它们，它是一种高级数据类型和机制，但我提到它的原因是让您知道它的存在。在我们继续之前，让我至少解释一下它的创建动机和您可能希望考虑可能使用它的用例。`TypedArray`随着 ES6 的出现而出现，因为 Web 应用程序变得越来越先进，客户端机器现在有如此多的可用性能，编写一个处理和操作音频和视频的客户端应用程序是一个好主意。为了做到这一点，您需要一种机制来使您的 JavaScript 代码能够读取和写入这些原始二进制流的数据。

您可能希望构建的两个立即可以使用`TypedArray`的示例是：

+   视频编辑（您希望删除不需要的镜头段）

+   对音频进行采样（更改声音字节的频率，也许创建原始样本的 11 个版本以创建一个音阶，以便能够从原始单个样本中演奏旋律）

再次，这是 JavaScript 发展的一个例子。

# 地图

地图是一种数据结构，它在 ES6 中引入了 JavaScript。地图用于将值映射到值。此外，它们允许使用任意值作为键，这意味着您可以使用整数作为键，或字符串，甚至对象；但是，不允许使用符号作为键。还有一些方便的方法可以在地图上执行操作，并且您还可以对地图进行迭代。让我们来看一些创建地图的代码，并探索一些常见的内置函数。首先让我们创建我们的地图。我们将创建一个用于将学习曲线映射到编程语言的地图，使用`new`关键字：

```ts
var mapLangCurve = new Map();
```

现在让我们使用地图的设置功能向其添加一些条目：

```ts
mapLangCurve.set('Python', 'low');
mapLangCurve.set('Java', 'medium');
mapLangCurve.set('C++', 'high');
```

虽然你可以一次在`Map`中添加键值对，就像我们刚刚做的那样，但`set`方法是可链接的，所以我们可以使用这种语法来完成完全相同的事情，这样可以节省一些输入：

```ts
var mapLangCurve = new Map().
set('Python', 'low').
set('Java', 'medium').
set('C++', 'high');
```

或者，作为声明和初始化我们的语言学习曲线`Map`的第三种方法，我们可以将一个包含两个元素数组的数组传递给 Map 的构造函数。假设我们的两个元素数组设置如下：

```ts
var arrLangCurve = [['Python', 'low'], 
['Java', 'medium'], 
['C++', 'high']]; 
```

然后我们可以像这样将其传递给构造函数：

```ts
var mapLangCurve = new Map(arrLangCurve);
```

这三种创建`Map`的方法都会产生完全相同的结果。好了，让我们继续快速看一下可以在`Map`上执行的一些常见操作。我们可以使用`size`属性获取 Map 的大小：

```ts
var langCurveSize = mapLangCurve.size;
```

我们可以使用`get`函数检索键的值：

```ts
var javaLearningCurve = mapLangCurve.get('Java');
```

我们可以使用`has`函数检查`Map`中键的存在：

```ts
var blnCurveExists = mapLangCurve.has('C++'); 
```

我们可以使用`delete`函数删除一个键及其值：

```ts
mapLangCurve.delete('Python');
```

我们可以使用`clear`函数一次性清除一个集合中的所有项。

如果你在 JavaScript 环境中跟着做，现在不要尝试这样做，因为我们需要一些数据来迭代：

```ts
mapLangCurve.clear();
```

在 JavaScript 中，你可以很容易地使用`for`构造迭代`Map`，但我们需要知道我们想要迭代什么。我们想要获取我们地图的键吗？还是它的值？或者我们想要获取两者。这是如何迭代我们地图的键：

```ts
 for (let key of mapLangCurve.keys()) {
    console.log(key);
} 
```

这是如何迭代我们地图的值：

```ts
 for (let value of mapLangCurve.values()) {
    console.log(value);
} 
```

这是如何迭代我们地图的键和值：

```ts
for (let item of mapLangCurve.entries()) {
    console.log(item[0], item[1]);
} 
```

大多数情况下，你可能希望访问你的地图的键和值，所以你应该使用地图的`entries`函数。

在本章的稍后部分，我们将看一下 ES6 给我们的一个构造，叫做解构，它使我们能够直接访问键和值。

# WeakMap

`WeakMap`是一个有趣的生物，它不是从`Map`继承而来，尽管它们都是键值对的集合，并且共享一些相同的功能。

`Map`和`WeakMap`之间最重要的区别是可以用作它们的键的数据类型。通过`Map`，我们已经看到可以使用各种数据类型作为其键，包括对象。但是，`WeakMap`只能将对象作为其键。这是有意设计的，并且使`WeakMap`特别有用，如果只关心在键尚未被垃圾回收时访问键的值。让这一点沉淀片刻。我知道这听起来像一个奇怪的用例，但是如果考虑到`WeakMap`可以帮助减轻 JavaScript 程序中的内存泄漏，可能足够考虑如何在代码中使用`WeakMap`。

数据结构名称中的*Weak*部分来自于`WeakMap`弱引用其键对象。这意味着它们有可能被垃圾回收。这一事实导致了我们的下一个观点。由于我们的键可能在任何时候被垃圾回收而无需我们的参与，因此将它们列举出来是没有意义的，因此它们不是，这意味着我们无法遍历集合。

如果需要遍历集合中键或值的列表，则应使用`Map`。相反，如果不需要遍历`Map`，只打算将其用作查找表，则可以考虑使用`WeakMap`。

我们将在下一节学习`Set`。

# Set

`Set`是一个唯一值的集合，并且可以按照其元素添加到其中的顺序进行迭代。`Set`可以包含同类数据，但是每个数据（即元素）都需要是唯一的。如果尝试将现有元素添加到集合中，对集合不会产生影响。

集合具有许多与映射相同的功能。让我们创建一个`Set`对象，并快速浏览一些常用的函数。

创建一个集合，我们使用`new`关键字调用它的构造函数：

```ts
var setCelestialObjects = new Set();
```

让我们向我们的`Set`添加一些元素（即天体）：

```ts
setCelestialObjects.add('Earth');
setCelestialObjects.add('Moon');
setCelestialObjects.add('Solar System');setCelestialObjects.add('Milky Way');
setCelestialObjects.add('Andromeda');
setCelestialObjects.add(['Aries', 'Cassiopeia', 'Orion']);
setCelestialObjects.add(7);
```

好吧，数字`7`并不完全是天体，但我想向您展示您可以将不同类型的元素添加到同一个`Set`中。与我们的星座数组一样：我们可以向我们的`Set`添加数组和任何类型的对象。

我们可以使用`size`属性获取我们的`Set`的大小：

```ts
var sizeCelestialObjects = setCelestialObjects.size;
```

现在不要这样做，但是您可以使用其`clear`函数清除`Set`：

```ts
setCelestialObjects.clear();
```

我们可以通过将其值传递到集合的`delete`函数中来从我们的`Set`中删除一个元素：

```ts
setCelestialObjects.delete('Andromeda');
```

您可以使用`for`循环来迭代`Set`，就像我们用于`Map`一样：

```ts
for (let element of setCelestialObjects) {
    console.log(element);
}
```

如果您想对`Set`中的每个元素执行操作，可以使用 set 的`forEach`函数，该函数以回调作为其参数。例如，如果您的`Set`中有一组整数，并且想要对它们进行平方运算，可以这样实现：

```ts
var setIntegers = new Set();
setIntegers.add(1);
setIntegers.add(7);
setIntegers.add(11);
setIntegers.forEach(function(value) {
    console.log(Math.pow(value, 2));
});
```

上述代码不会更改我们`Set`中的元素，而是将平方值打印到控制台。我们无法轻松地就地更改我们的元素，但是我们可以创建一个新的`Set`并将我们的平方元素存储在其中，就像这样：

```ts
var setSquaredIntegers = new Set();
setIntegers.forEach(function(value) {
    setSquaredIntegers.add(Math.pow(value, 2));
});
```

我们可以使用`has`函数来检查我们的`Set`中是否存在元素：

```ts
var blnElementExists = setCelestialObjects.has('Moon');
```

如果您还记得我们讨论过的`Map`，`Map`对象有以下三个函数：keys，values 和 entries。`Set`也有这三个函数，但它们的返回值是非常不同的。当您在`Set`上调用这些内置函数时，将会得到一个`SetIterator`对象。

在本书中，我们不会使用`SetIterator`，但是就像我给您介绍`TypedArray`的用例一样，我想给您介绍`SetIterator`的用例。`Map`对象和`Set`对象是不同的数据结构，您可以以不同的方式迭代这些结构。如果使用迭代器，可以构建一个可以以相同方式迭代这两种类型数据结构的函数。换句话说，您可以将对象传递到迭代集合的函数中，而不必担心它们的类型。

# WeakSet

`WeakSet`是一组弱引用对象，每个对象必须是唯一的；不允许添加重复的对象。回想一下我们在`WeakMap`上的讨论，由于它们的键只能是对象，因此它们的键可能会在我们脚下被垃圾回收。因此，就像`WeakMap`一样，对于迭代，`WeakSet`也是如此：我们无法对集合进行迭代。

`WeakSet`有非常少量的内置函数，即 add，delete 和 has。`WeakSet`还有一个`length`属性，类似于数组，而不是`Map`的`size`属性。

让我们快速看一下创建`WeakSet`对象的语法，以及它的属性和函数。

我们可以使用其构造函数创建一个空的`WeakSet`对象：

```ts
var myWeakSet = new WeakSet();
```

让我们创建三个空对象添加到我们的`WeakSet`中，然后使用`WeakSet`对象的`add`函数将它们添加到其中，然后使用其`length`属性获取它包含的对象数量：

```ts
var objA = {};
var objB = {};
var objC = {};
myWeakSet.add(objA); 
myWeakSet.add(objB); 
myWeakSet.add(objC);  
var lengthMyWeakSet = myWeakSet.length; // lengthMyWeakSet will be set to 3
```

您可能会问，*等一下。您说对象必须都是唯一的，任何重复的对象都不会被插入到 WeakSet*对象中。*所有对象都是空的；它们不是相同的吗？*是的，当尝试插入操作时，重复的对象将被拒绝。然而，虽然我们的三个对象都具有相同的值（即它们都是空的），但它们实际上是三个独立且不同的对象。

在 JavaScript 中，与大多数其他面向对象的语言一样，决定对象是否与另一个对象相同的是对象引用（即底层内存地址），而不是其内容。

以下是您可以比较两个引用同一对象的对象变量的方法：

```ts
var blnSameObject = Object.is(objA, objB);
```

`objA`和`objB`各自引用空对象，但这是两个不同的对象；因此，`blnSameObject`将被设置为`false`。

如果我们做了以下操作，由于`objB`和`objC`变量指向内存中的同一个对象，试图将`objC`添加到`myWeakSet`的行将不会对`myWeakSet`产生影响，因为底层对象已经包含在`WeakSet`对象中：

```ts
var objA = {};
var objB = {};
var objC = objB; // objB and objC now both point to the same object in memory
myWeakSet.add(objA); 
myWeakSet.add(objB); 
myWeakSet.add(objC);  
var lengthMyWeakSet = myWeakSet.length; // lengthMyWeakSet will be set to 2
```

# 类

几页前，我们介绍了在 JavaScript 中创建对象的三种不同方法。我还提到我们将在后面介绍如何使用 ES6 类语法创建对象。此外，我还提到 JavaScript 没有类的概念，但我们在本节中涵盖了类。让我们澄清这一切，看看如何在 JavaScript 中创建类，以及如何从这些类创建对象。

在 ES6 之前的 JavaScript 版本中，不存在类的概念。相反，每当创建对象时，JavaScript 运行时会直接从其他对象继承，而不是从类继承（请记住，JavaScript 不是经典的面向对象语言；它使用原型继承）。这并不是说 JavaScript*不好*，但它确实与众不同。为了将经典面向对象的风格和语义带入，ES6 给我们带来了类的概念。类是对象的蓝图，当我们从这个蓝图或模板创建一个对象时，它被称为实例化。我们使用类来实例化（使之存在）一个或多个对象。让我们来看一下语法。

让我们创建一个`Car`类，并为其提供一个构造函数、三个属性和三个方法：

```ts
class Car {
  constructor(make, model) {
    this.make = make;
    this.model = model;
    this.speed = 0;
  }
  get speed() {
    return this._speed;
  }
  set speed(value) {
    this._speed = value;
  }
  speedUp() {
    this.speed += 10;
  }
  slowDown() {
    this.speed -= 10;
  }
}
```

我故意在这里使用了术语*方法*，而以前我总是把它们称为*函数*。因为我们现在讨论的是类和对象，在经典的面向对象术语中，*方法*比*函数*更好。

你需要记住任何面向对象语言的两种关系，如下：

+   对象是它们类的实例

+   对象封装了操作数据的数据和方法

数据代表对象在任何时刻的状态，方法代表对象具有的行为。就是这样。

好了，回到我们的类示例。我们的`Car`类有一个构造函数，它接受两个参数：汽车的制造商和型号。它还有三个实例变量：`make`，`model`和`speed`。此外，它有两个方法，`speedUp`和`slowDown`。最后，`speed`实例变量实际上是一个属性；这是因为它有一个关联的 getter 和 setter。

需要注意的是，我们类中的 setter 和 getter 在属性名称前面有一个下划线，而关联的实例变量没有。这很重要，因为如果没有下划线，JavaScript 运行时在实例化您的`Car`对象时会抛出异常（即`RangeError: Maximum call stack size exceeded`）。

太好了！那么，我们如何创建它的实例（即`Car`对象），以及如何调用它的方法和读取它的属性？以下是代码来帮助回答这些问题。

我们创建我们的`Car`对象就像创建任何其他对象一样，通过调用它的构造函数：

```ts
var myG6 = new Car('Pontiac', 'G6');
```

让我们读一下我们车的当前速度：

```ts
myG6.speed; // this returns 0, which is the value it was initialized to
```

哦，天啊！零英里每小时？这是不可接受的。让我们踩油门！看这个：

```ts
myG6.speedUp(); // this increases our speed by 10 mph
myG6.speedUp(); // this increases our speed by another 10 mph
myG6.speedUp(); // this increases our speed yet again, by 10 mph
myG6.speedUp(); // this increases our speed, one last time, by ... you guessed it, 10 mph
```

我们的速度有多快？我们最好检查一下：

```ts
myG6.speed; // this now returns 40 
```

该死！我们刚刚进入了一个学校区，必须将最高速度降到 20 英里/小时：

```ts
myG6.slowDown(); // this decreases our speed by 10 mph
myG6.slowDown(); // this decreases our speed by another 10 mph
```

让我们再次检查我们的速度：

```ts
myG6.speed; // this now returns 20
```

呼！好了，目前我们的速度还可以。

总结一下这一部分，在 JavaScript 中有几件事情要记住：

+   与 Java 或 Python 不同，JavaScript 中的类只能有一个构造函数。不支持构造函数重载。

+   你可以在你的类中使用 super 调用（用于调用层次结构中更高级别的类的构造函数），但必须在使用`this`引用之前调用它，就像我们将`make`和`model`参数分配给它们各自的实例变量时一样。

# 接口

到目前为止，我们一直在看一些 JavaScript 的新添加，这些对我们来说是可用的。对于我们来说，接口部分是 TypeScript 特有的东西，因为 JavaScript 没有接口的概念。

接口就像是类的合同，并提供了类必须遵循的一组规则。让我们从构建`Car`类转换到构建`Animal`类，并且在此过程中，让我们的类实现一个我们称之为`Species`的接口：

```ts
class Animal implements Species {
}  
interface Species {
  name: string;
  isExtinct: boolean;
} 
```

我们的`Animal`类是空的。它甚至没有构造函数或任何实例变量，但对我们来说这并不是问题，因为它仍然可以满足我们演示如何使用接口的目的。

稍微看一下`Species`接口。你会注意到一些事情：

+   它有两个公共属性。TypeScript 有访问修饰符，就像 Java 和 C#一样，我们将在后面的章节中使用它们时介绍。现在，你只需要知道属性上缺少访问修饰符意味着属性是公共的。这很重要，因为由于接口描述了实现它的类的公共接口，其属性必须是公共的。

+   第二件你会注意到的事情是我们在输入属性。我们声明`name`属性的类型为字符串，`isExtinct`属性的类型为布尔值。这是 TypeScript 的一个主要优势，正如我们之前学到的那样，也是 TypeScript 得名的原因（即，一个有类型的 JavaScript）。

我们将在本书的后面看到访问修饰符的作用。有三种：

+   **公共的：** 这是默认的修饰符，意味着属性或函数对所有其他代码可见

+   **私有：** 类的属性和函数标记为私有的可供其声明的类的成员函数使用

+   **受保护的：** 这与私有相同，但类成员也对从其声明的类继承的任何类可见

我们将类与接口结合的方式是在类定义中使用`implements`关键字，就像我们在这个例子中所做的那样。一旦我们这样做，类必须遵守接口的合同。

那么现在呢？如果`Animal`类没有实现`Species`接口规定的两个属性，那么 TypeScript 在转译过程中会抛出错误。

我们还可以通过在属性或函数的末尾添加问号来描述一个可选的接口。我们的接口中没有列出函数，但我们也绝对可以有函数。

如果我们的接口是一个可选的合同，它会是这样的：

```ts
interface Species {
 name?: string;
  isExtinct?: boolean;
} 
```

# 继承

我们提到，标有受保护访问修饰符的类成员也对任何从它们声明的类继承的类可见，因此我们最好快速讨论一下继承。

继承并不意味着我们创建的类会变得独立富裕；那是一种完全不同的继承。我们谈论的继承类型可能不那么令人兴奋，但对于我们的 JavaScript 代码来说，它更有用。

一个类可以从另一个类继承。为了做到这一点，我们在类定义中使用`extends`关键字。让我们再次转换一下，这次是从`Animal`到`Employee`（尽管我在一些客户地点的一些浴室和厨房看到过一些动物，我可以告诉你一些员工也可以是动物）。让我们来看代码：

```ts
class Employee {
  constructor(name) {
    this.name = name;
    this.title = "an employee";
  }
  annouceThyself() {
    return `Hi. My name is ${this.name} and I am ${this.title}.`;
  }
}
class Manager extends Employee {
  constructor(name) {
    super(name);
    this.title = "a manager";
  }
}
```

让我们创建一个`Employee`对象，并让员工宣布自己：

```ts
var emp = new Employee("Goofy");
console.log(emp.annouceThyself()); 
//  Hi. My name is Goofy and I am an employee.
```

让我们创建一个`Manager`对象，并让经理宣布自己：

```ts
var mgr = new Manager("Mickey");
console.log(mgr.annouceThyself()); 
//  Hi. My name is Mickey and I am a manager.
```

这里发生了什么：

1.  我们创建了一个`Employee`类。

1.  我们创建了一个从`Employee`继承的`Manager`类。

1.  `Manager`类除了构造函数之外，没有任何属性或函数。但是，它从`Employee`类继承了属性（`name`和`title`）和方法（`annouceThyself`）。

1.  `Manager`类中的构造函数调用`Employee`类中的构造函数，传递经理的名字。

1.  经理的构造函数重新分配了`title`属性的值。

这是相当简单的，但这里有两个要点要记住：

+   继承类从它继承的类中获取所有类成员。

+   一个构造函数可以调用它的父类的构造函数，并且如果父类有父类，等等，这种情况可以一直延续下去。

# 解构

解构是一个非常酷和非常有用的构造，我们将在本书中多次使用它，而且在你完成本书后，你在你的 Angular 项目中也无法离开它。简而言之，解构是一个 JavaScript 表达式，它使我们能够轻松地从对象和集合中提取数据。

当我们看`Map`对象时，我提到我们会看一个解构的例子。在这里。

假设我们有以下对象：

```ts
const author = {
  firstName: 'Aki',
  lastName: 'Iskandar',
  topics: ['Angular', 'JavaScript', 'TypeScript', 'Bootstrap', 'Node'],
  cities: ['Calgary', 'Cleveland'],
  publisher: 'Packt'
}
```

如果我们想提取`firstName`，`lastName`和`publisher`，我们知道如何以传统的方式做到这一点（即在 ES6 之前）：

```ts
const firstName = author.firstName;
const lastName = author.lastName;
const publisher = author.publisher;
```

好吧，解构（尽管它的语法看起来有点奇怪）通过以下语法节省了我们大量的按键次数，给我们提供了相同的结果（提取数据的新变量）：

```ts
const {firstName, lastName, publisher} = author;
```

我们可以很容易地看到，它通过将一个变量写入控制台来完成了它的工作：

```ts
console.log(publisher);  //  Packt
```

这非常方便，当我们一起编写应用程序时，我们将充分利用它。

# 模板字符串

模板字符串是用反引号括起来的字符串（即`）。

注意：反引号字符通常与波浪号（即~）在键盘上的同一个键上，并且紧挨着数字 1 键的左边。

JavaScript 总是允许我们使用双引号和单引号来创建字符串，那么为什么要使用第三种类型的字符串创建字符呢？嗯，事实证明，鉴于前端框架的大量使用，有一个共同的需求需要做三件事：

+   字符串插值

+   多行字符串

+   标记模板

# for-of 循环

JavaScript 为我们带来了`forEach`构造，用于循环遍历集合。这是一个很好的内置方法，但你无法跳出这个循环。我们还有`for-in`循环，对于具有字符串键的对象来说非常好，但在迭代数组时有一些缺点。

进入新的`for-of`循环。它适用于对象、数组和映射，并且你可以跳出它。以下是语法，与`for-in`循环的语法相同，只是将`in`更改为`of`：

```ts
let myArray = 5, 10, 15, 20, 25;
  for (var item of myArray) {
  console.log(item);
}
```

# 装饰器

装饰器也是 TypeScript 的一部分。TypeScript 中的装饰器装饰函数和类，就像它们在其他一些语言中一样，比如 Python 和 Java。

我们不会花太多时间在这里，因为我们不会为我们要一起构建的应用程序编写自己的装饰器，但由于 Angular 大量使用装饰器，我想至少让你了解它们的用途。我们还将看一个快速的例子，如何创建一个装饰器以及如何使用它，但我们会快速地飞过它。

装饰器是一种通过用装饰器注释类来为函数或类（通常是类）添加功能的方法。装饰器只是一个函数，尽管乍一看它的语法看起来有些奇怪。让我们看一些代码：

```ts
function iq(target) {
  Object.defineProperty(target.prototype, 'iq', {value: () => "Genius"})
}
@iq
class Person {
}
let readerOfMyBook = new Person();
console.log(readerOfMyBook.iq());  // prints out "Genius" to the console
```

这是中级到高级水平的 TypeScript，整个章节都可以写关于装饰器的内容。我们没有时间在这里详细介绍它们，但要记住的是，它们只是简单地为函数或类添加功能的函数，你只需要用装饰器的名称来注释函数或类（即`@NameOfDecorator`）。

# Promises

当我们在[第十二章 *集成后端数据服务*中使用它们时，我们将更详细地介绍 promises，所以我会推迟代码。原因是展示一个真正好的实际例子需要相当多的代码，因为需要调用异步代码。所以，我承诺在书中稍后会有真实世界的 promises。但是，我们至少可以看一下定义，这样你就知道它们是什么。

当你调用一个可能需要很长时间才能返回结果或完成任务的函数时，而且你不想延迟程序的执行，你可以异步调用该函数。这意味着你的代码在异步调用前一行后会继续执行到下一行。如果你不异步调用它，你的程序的执行将停止并等待你最后调用的函数从它正在做的事情中返回，比如从数据库中读取一堆记录。

有几种不同的方法可以异步调用函数。异步调用函数最常见的方式是使用回调函数。回调函数是你传递给异步调用的函数的函数，这样它就可以在完成工作后调用该函数。这就是回调函数得名的原因；你调用的函数在完成时会回调你。

Promises 是我们可以用来异步编程的另一种机制。虽然 Promises 使事情变得更加可控，但在 JavaScript 中编写良好的异步代码通常仍然非常困难。因为这个事实，人们开始编写 JavaScript 库来尝试使异步代码更容易编写。有几种库可供选择。一个拯救了我的理智的库叫做 Async。

话虽如此，我仍然没有给你一个 Promise 的定义，所以在这里：Promise 是一个代理尚未知的值；它就像一个值的占位符，最终将从异步调用的函数中返回。这种构造允许异步函数立即返回一个值，就像它是一个同步方法一样。返回的初始值是一个 Promise，一旦调用的函数完成了它的工作，Promise 最终将被返回的值替换。

我知道这可能需要花费一些时间来理解，但当我们在第十二章中编写我们的代码，*集成后端数据服务*，你将理解 Promise。这是一个承诺，双关语。

# 模块

在 ES6 之前，JavaScript 没有模块的概念。模块是简单的代码文件，可以加载到其他代码中，以便加载的模块中的函数对导入模块的代码可用。模块可以加载模块。模块导致模块化的代码，这是一件好事。与其在一个文件中编写庞大的代码块，不如将其分割成逻辑单元，并使该代码存在于多个文件中。这导致了代码重用，命名空间和可维护性。

虽然 JavaScript 没有模块，但我们仍然能够在一定程度上实现相同的功能。我们可以在网页中调用函数之前使用脚本标签加载脚本文件。但是，对于在服务器端或网页之外的其他环境中运行的 JavaScript，没有模块，编写非单片应用程序变得困难。

让我们继续进行代码。

假设我们有一个名为`alphafunctions.js`的文件，其中包含以下代码：

```ts
function alpha1() {
  console.log("Alpha 1 was called");
}
function alpha2() {
  console.log("Alpha 2 was called");
}
export {alpha1, alpha2};
```

`export`关键字用于标记哪些函数可以被导出，因此可以被其他模块导入。

现在假设我们有这个文件`main.js`，其中包含以下代码：

```ts
import {alpha1, alpha2} from ./alphafunctions;  
alpha1();  //  "Alpha 1 was called" is written to the console
```

# 默认导出

假设我们总是希望将我们的`alpha1`函数导入到其他模块中，或者至少更频繁地这样做。我们可以在`keyword`函数之前添加关键字`export default`。因此，当我们导入它时，我们不再需要在函数名称周围使用大括号。让我们在代码中看到这一点。

查看`alphafunctions.js`：

```ts
export default function alpha1() {
  console.log("Alpha 1 was called");
}
function alpha2() {
  console.log("Alpha 2 was called");
}
export {alpha1, alpha2};
```

查看`main.js`：

```ts
import alpha1, {alpha2} from ./alphafunctions;  
```

虽然这并不是一个惊人的差异，但“默认导出”这个术语在对话和博客文章的代码片段中经常出现，因此我想确保我们至少快速看一下，这样你就明白为什么有时会有大括号，而有时没有。当你使用 JavaScript 库时，你也会在文档和代码示例中看到这一点。所以，现在你知道了。

# 总结

在本章中，我们涵盖了 JavaScript 的一些历史，特别是围绕 JavaScript 生态系统的一系列幸运事件，这些事件巩固了该语言作为近代最重要的编程语言。现在我们不仅可以编写在浏览器中执行的客户端代码，还可以编写在服务器上运行的 JavaScript 代码。如果这还不足以成为使用更多 JavaScript 的有力理由，你还可以将 JavaScript 用于原生移动开发，以及创建桌面应用程序。这真是一个令人兴奋的故事！

然后，我们简要地查看了 ES6 发布时添加到 JavaScript 中的一些内容。这些新增内容相当重要，特别是因为 JavaScript 在十多年来基本保持不变，因此这些新增内容真正加强了语言。我们还列举了 TypeScript 带来的一些好处。请记住，你可以将 TypeScript 视为 JavaScript 的超集，并且可以将其定义为 ES6 加上可选类型。

微软将 TypeScript 贡献给 JavaScript 开发人员是该公司长期以来对开源世界做出的最重要的贡献之一。Angular 本身就是用 TypeScript 编写的，因为 TypeScript 相对于纯 JavaScript 具有优势，所以在构建 Angular 应用程序时，最好使用 TypeScript 进行编写。我们记得 JavaScript 是唯一可以在浏览器中执行的语言，但幸运的是，TypeScript 附带了一个转译器，可以将我们的 TypeScript 代码转换为纯 JavaScript 代码。

如路线图中所述，第三章《Bootstrap - 响应式网格布局和组件》，我们有一个类似的目标。在本章中，我们将对 SASS 进行快速介绍，这是我们将用来为我们的 Angular 组件设计样式的工具，而不是使用 CSS。我们还将涵盖足够的 Bootstrap 知识，让您能够舒适地使用这个古老的 CSS 框架来布局我们将一起构建的网络应用程序 ListingCarousel 的网页。您将获得足够的知识，立即将这些技能应用于您目前可能拥有或将来可能开始的几乎任何网络应用项目中。
