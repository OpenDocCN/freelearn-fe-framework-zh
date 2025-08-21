# 第三章：TypeScript 入门

Angular 2 是用 TypeScript 编写的，但这并不意味着我们必须用 TypeScript 编写我们的应用程序。Angular 2 应用程序可以用 ES6（JavaScript 2015）甚至 ES5（JavaScript 1.5）编写。在本书中，我们将主要使用 TypeScript，主要是因为装饰器的实现可以使我们的 Angular 2 代码比 ES6 和 ES5 更清晰。

我假设你已经知道如何编写 JavaScript 2015（ES6）代码。在本章中，我们将只涵盖我们需要了解的 TypeScript 知识；大部分代码与 JavaScript 2015 兼容。如果你完全不熟悉 ES6，强烈建议你赶上新的语法和特性。

我们将涵盖以下主题：

+   TypeScript 语言简介

+   使用模块管理依赖关系

+   类的声明和使用

+   系统、内置和自定义类型

+   如何使用装饰器

# TypeScript 简介

你应该知道的最重要的一点是，TypeScript 并不是一个全新的语言。它是 ES6 的超集。这意味着 ES6 代码可以通过将文件扩展名从`.js`改为`.ts`来*转换*为 TypeScript。

例如，以下代码是有效的 ES6 或 TypeScript：

```ts
class User {
  constructor(id){
    this.id = id;
  }

  getUserInfo(){
    return this.userInfo;
  }
}
```

另一方面，TypeScript 编译器可以将代码目标定为各种 JavaScript 版本，包括 ES6。编译器将剥离所有*额外*的代码，并输出干净可读的 JavaScript 代码，几乎与源代码相同。

这是一个简单的 TypeScript 类：

```ts
class Product {
  private id: number;
  private color: string;

  constructor(id:number, color:string) {
    this.id = id;
    this.color = color;
  }
}
```

目标为 ES6 将输出这段代码：

```ts
class Product {
  constructor(id, color) {
    this.id = id;
    this.color = color;
  }
}
```

当目标为 ES5 时，这是完成的结果：

```ts
var Product = (function () {
  function Product(id, color) {
    this.id = id;
    this.color = color;
  }
  return Product;
})();
```

正如你所看到的，编译结果是干净可读的代码，几乎与源代码相同（在 ES6 的情况下）。

接下来，我们将探索语言特性。请注意，我们将要介绍的大部分特性都是 ES6 的一部分，而不是 TypeScript。我会提到哪些特性属于 TypeScript，哪些不属于。

# 使用模块管理依赖关系

JavaScript 引入的最重要的变化之一是模块。模块是以特殊方式加载的 JavaScript 文件。所有变量和声明都作用域限定在模块内。如果我们想要向外部公开一些代码，就需要显式地导出它。如果你尝试在模块的顶层记录`this`的值，你会得到 undefined。

## 导出和导入语句

`export` 和 `import` 关键字用于定义代码中应该暴露给其他模块的部分，以及我们想要从另一个模块导入的代码。以下模块暴露了一个函数、一个类和一个变量：

```ts
[user.ts]
export function getRandomNumber() {
  return Math.random();
}

export class User {
  constructor(name) {
    this.name = name;
  }
}

export const id = 12345;
```

要使用这个导出的代码，我们需要在另一个模块中导入它。我们可以以各种方式导入这段代码：

```ts
// import only the function from the module
import { getRandomNumber } from './user';

// import both the function and the class from the module
import { getRandomNumber, Person } from './user';

// import the function and bind it to a random variable
import { getRandomNumber as random } from './user';

// import everything from the module and
// bind it to a userModule variable
import * as UserModule from './user';
```

## 默认导出

我们可以从模块中导入我们需要的内容，导入多个代码和导入模块导出的所有内容。还有另一种从模块中导出代码的选项，称为 `default` 导出：

```ts
[user.ts]
export default class User {
  constructor(name) {
    this.name = name;
  }
}
```

当导入使用默认关键字导出的代码时，我们不必使用导出的函数、类或变量的确切名称：

```ts
import UserModule from './user.ts';
```

每个模块只能声明一个 `default` 导出。我们可以在同一个模块中混合使用默认导出和命名导出。请注意，当导入默认导出的代码时，我们不必使用大括号。

# 类

JavaScript 语言的面向对象能力是围绕原型的概念构建的。原型模型定义了对象之间的链接，而不是继承树。原型模型虽然强大，但对于普通的 JavaScript 程序员来说并不友好。TypeScript 让我们能够使用熟悉的语法创建类，它与 JavaScript 1.5 类完全相同（如果我们选择不使用 TypeScript 的独有功能）。要在 TypeScript 中定义一个类，我们使用 `class` 关键字：

```ts
class Product {}
```

在 TypeScript 中，类可能有构造函数和方法，就像 JavaScript 2015 一样。TypeScript 还添加了定义类属性的能力。以下示例展示了我们的 `Product` 类，其中包含构造函数、属性和方法：

```ts
class Product {

  color;
  price;

  constructor(color, price) {
    this.color = color;
    this.price = price;
  }

  getProductDetails() {
    return this.color + this.price;
  }
}
```

在 TypeScript 中，就像 JavaScript 2015 一样，通过 `extends` 关键字实现继承，当需要调用父类时使用 `super` 关键字。以下示例说明了如何使用它：

```ts
class Product {
  color;
  price;

  constructor(color, price) {
    this.color = color;
    this.price = price;
  }

  getProductDetails() {
    return `${this.color}, ${this.price}`;
  }
}

class Ebook extends Product {
  size;

  constructor(color, price, size) {
    super(color, price);
    this.size = size;
  }

  getProductDetails(){
    return `${this.color}, ${this.price}, ${this.size}`;
  }
}
```

重要的是要意识到，类只是原型的一种“糖”，这意味着 JavaScript 处理对象实例化和继承的方式在幕后并没有改变。它只是有一个友好的语法。

在 Angular 2 中，包含所有组件行为的组件被定义为一个类。其余部分只是元数据装饰器，我们将在未来的章节中学习。

# 类型系统

TypeScript 最著名的特性是类型系统，它使我们能够在编译时利用静态类型检查。我们已经在之前的代码示例中看到了类型的使用。重要的是要理解，在 TypeScript 中，类型的使用是可选的，但强烈建议使用。正如我们在本章开头看到的，TypeScript 编译器会将所有类型声明都分解，因此编译结果将是普通的 JavaScript。

## 基本类型

TypeScript 支持您期望的所有基本 JavaScript 类型：布尔值、数字、字符串和数组。以下示例显示了如何在代码中使用它：

```ts
// strings
let name: string = "bob";

// boolean
let isLoggedIn: boolean = true;

// number
let height: number = 24;
let width: number = 12;

// arrays
let colors: string[] = ['red', 'green', 'blue'];
let colors: Array<string> = ['red', 'green', 'blue'];
```

TypeScript 还将额外的三种类型添加到混合中，即`enum`、`any`和`void`。类型`any`顾名思义，用于处理动态数据，我们无法确定期望的数据类型。如果根本不指定类型，TypeScript 默认为`any`类型：

```ts
// value can be any type, init with a number
let value: any = 10;

// different types can assigned
value = false;
value = "this value is a string";
```

`void`类型就像`any`的相反。它表示*没有类型*。大多数情况下，它被用作不返回任何值的函数的返回类型：

```ts
// this function doesn't returns
function setId(id:string): void {
  this.id = id;
}
```

`enum`只是一种为一组数字值提供更友好名称的方式。没有其他。默认编号从`0`开始，可以手动设置为任何其他数字值：

```ts
// default behavior, value of color will be 2;
enum Color {Red, Green, Blue}
let color: Color = Color.Blue;

// manual initialize, value of color will be 6;
enum Color {Red = 2, Green = 4, Blue = 6}
let color: Color = Color.Blue;
```

## 自定义类型

除了内置的基本类型，您可以（而且可能会）为自己编写的代码使用自己的类型。在 TypeScript 中有三种定义类型的方式，即创建类、定义接口和使用声明现有库类型的特殊文件。

在 TypeScript 中，接口可以描述对象的*形状*，通常包括没有实现的类成员和方法。接口仅在设计时存在；例如，在定义提供程序时，您不能将其用作类型。

以下示例说明了如何将自己的类用作类型：

```ts
class Model {}
class Account extends Model {}
class Controller {
  model:Model;
  constructor(model:Model) {
    this.model = Model;
  }
}
new Controller(Account);
```

以下示例说明了如何创建用于定义类型的接口：

```ts
interface Model {
  get(query:string): any[];
}

class Account implements Model {
  get(query:string):any[] {
    return [];
  }
}

class Controller {
 model:Model;
 constructor(model:Model) {
  this.model = Model;
 }
}
```

第三种选择是创建一个带有`.d.ts`扩展名的文件，将现有代码（第三方）映射到类型。创建此文件的过程超出了本书的范围，您可以访问[`www.typescriptlang.org/Handbook#writing-dts-files`](http://www.typescriptlang.org/Handbook#writing-dts-files)了解更多信息。

好消息是，你几乎可以在任何库中找到定义映射（包括 Angular）。访问[`github.com/typings/typings`](https://github.com/typings/typings)，在那里你可以浏览定义映射的存储库，并了解更多关于 typings 的信息，它是一个用于管理这些映射的命令行工具。

## 关于泛型

还有另一个与类型相关的特性，应该提到的是*泛型*。这个特性使我们能够创建一个可以处理多种类型而不是单一类型的组件。

泛型 API 超出了本书的范围，我们不会在代码示例中使用这个特性。你可以通过访问[`www.typescriptlang.org/Handbook#generics`](http://www.typescriptlang.org/Handbook#generics)来了解更多关于泛型的信息。

# 使用装饰器

装饰器是修改类、属性、方法或方法参数的函数。下面的例子说明了如何定义和使用一个简单的装饰器，它给类添加了一个静态参数：

```ts
// decorator function
function AddMetadata (...args) {
  return function (target){
    target.metadata = [...args];
  }
}

// decorator applied
@AddMetadata({ metadata: 'some values'})
class Model {
}
```

三个点的语法（`...`）是*展开运算符*，这是 JavaScript 2015 的一个特性，它可以解构给定数组的项目。

## 装饰器与注解

你可能听说过注解这个术语；它们只是与 Angular 2 相关的元数据。在 Angular 团队决定使用 TypeScript 之前，他们向我们介绍了一种他们称为 AtScript 的新语言。这种语言包括一个叫做注解的特性，看起来与装饰器完全相同。那么有什么区别呢？装饰器是创建这些 Angular 注解的接口。装饰器被执行，在 Angular 2 中，它们负责使用 Reflect Metadata 库设置元数据。此外，装饰器是 ES7 的一个提案——JavaScript 的下一个版本。因此，我们可以专注于装饰器。

# 总结

TypeScript 是 JavaScript 的超集。这意味着你可以在`.ts`文件中编写纯粹的 JavaScript。TypeScript 编译器将去除所有额外的 TypeScript 代码，并生成纯净、可读的代码，几乎与源代码相同。Angular 2 团队使用 TypeScript 开发 Angular 平台（源代码是用 TypeScript 编写的，但也有编译后的 JavaScript 版本）。作为开发者，我们可以选择使用 TypeScript、JavaScript 2015（ES6）或 JavaScript 1.5。

如果您选择使用 TypeScript，强烈建议访问[`www.typescriptlang.org/`](http://www.typescriptlang.org/)，了解更多关于这种语言能力的信息，这超出了本书的范围。
