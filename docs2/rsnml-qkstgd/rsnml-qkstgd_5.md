# 第五章：有效 ML

到目前为止，我们已经学习了 Reason 的基础知识。我们看到了拥有一个健全的类型系统如何使重构变得更加安全、压力更小。在更改实现细节时，类型系统会帮助我们及时提醒代码库中需要更新的其他区域。在本章中，我们将学习如何隐藏实现细节，使重构变得更加容易。通过隐藏实现细节，我们保证更改它们不会影响代码库的其他区域。

我们还将学习类型系统如何帮助我们强制执行应用程序中的业务规则。隐藏实现细节也为我们提供了一种很好的方式来强制执行业务规则，通过确保模块不会被用户误用。我们将通过包含在此书 GitHub 仓库中的简单代码示例来阐述这一观点。

要继续学习，请从 `Chapter05/app-start` 开始。这些示例与我们一直在构建的应用程序是隔离的。

您可以使用以下方式访问本书的 GitHub 仓库：

```js
git clone https://github.com/PacktPublishing/ReasonML-Quick-Start-Guide.git
cd ReasonML-Quick-Start-Guide
cd Chapter05/app-start
npm install
```

记住，所有模块都是全局的，并且默认情况下，模块的所有类型和绑定都会被暴露。正如我们很快就会看到的，模块签名可以用来隐藏模块的类型和/或绑定，使其对其他模块不可见。在本章中，我们还将学习高级类型系统特性，包括以下内容：

+   抽象类型

+   幻影类型

+   多态变体

# 模块签名

模块签名以一种类似于接口在面向对象编程中约束类的方式约束模块。模块签名可以要求模块实现某些类型和绑定，并且也可以用来隐藏实现细节。假设我们有一个在 `Foo.re` 中定义的名为 `Foo` 的模块。其签名可以在 `Foo.rei` 中定义。任何列在模块签名中的类型或绑定都会暴露给其他模块。如果存在模块签名并且该类型或绑定不在模块签名中，则列在模块中的任何类型或绑定都会被隐藏。给定 `Foo.re` 中的绑定 `let foo = "foo";`，该绑定可以通过在 `Foo.rei` 中包含 `let foo: string;` 来由其模块签名既要求又暴露：

```js
/* Foo.re */
let foo = "foo";

/* Foo.rei */
let foo: string;

/* Bar.re */
Js.log(Foo.foo);
```

在这里，`Foo.rei` 需要 `Foo.re` 包含一个名为 `foo` 的 `let` 绑定，其类型为 `string`。

如果模块的 `.rei` 文件存在且为空，则模块内的所有内容都将被隐藏，如下面的代码所示：

```js
/* Foo.rei */
/* this is intentionally empty */

/* Bar.re */
Js.log(Foo.foo); /* Compilation error: The value foo can't be found in Foo */
```

模块的签名要求模块包含签名中列出的任何类型和/或绑定，如下面的代码所示：

```js
/* Foo.re */
let foo = "foo";

/* Foo.rei */
let foo: string;
let bar: string;
```

这导致以下编译错误，因为模块签名要求有一个未在模块中定义的 `string` 类型的 `bar` 绑定：

```js
The implementation src/Foo.re does not match the interface src/Foo.rei:
The value `bar' is required but not provided
```

# 模块类型

模块签名也可以使用 `module type` 关键字而不是单独的 `.rei` 文件来定义。模块类型必须以大写字母开头。一旦定义，一个模块可以使用 `module <Name> : <Type>` 语法被模块类型约束，如下所示：

```js
module type FooT {
  let foo: (~a: int, ~b: int) => int;
};

module Foo: FooT {
  let foo = (~a, ~b) => a + b;
};
```

同一个模块类型可以用于多个模块，如下所示：

```js
module Bar: FooT {
  let bar = (~a, ~b) => a - b;
};
```

我们可以将模块签名视为面向对象意义上的接口。接口定义了模块必须定义的属性和方法。然而，在 Reason 中，模块签名还隐藏了绑定和类型。但也许模块签名最有用的特性是能够公开抽象类型。

# 抽象类型

抽象类型是没有定义的类型声明。让我们探讨一下这为什么会很有用。除了绑定之外，模块签名还可以包括类型。在下面的代码中，你会注意到`Foo`的模块签名包括一个`person`类型，现在`Foo`必须包含这个类型声明：

```js
/* Foo.re */
type person = {
  firstName: string,
  lastName: string
};

/* Foo.rei */
type person = {
  firstName: string,
  lastName: string
};
```

`person`类型的公开方式与没有定义模块签名时相同。正如你所期望的，如果定义了签名而类型未列出，则类型不会公开给其他模块。还有选择将类型保留为抽象。我们只保留等号后面的部分。让我们看看下面的代码：

```js
/* Foo.rei */
type person;
```

现在，`person`类型对其他模块是公开的，但没有任何其他模块可以直接创建或操作`person`类型的值。`person`类型必须在`Foo`中定义，但它可以有任何定义。这意味着`person`类型可以随时间变化，并且`Foo`之外的任何模块都不会知道这一点。

让我们在下一节进一步探讨抽象类型。

# 使用模块签名

让我们假设我们正在构建一个发票管理系统，并且我们有一个`Invoice`模块，它定义了一个`invoice`类型以及一个其他模块可以使用来创建该类型值的函数。这种安排如下面的代码所示：

```js
/* Invoice.re */
type t = {
  name: string,
  email: string,
  date: Js.Date.t,
  total: float
};

let make = (~name, ~email, ~date, ~total) => {
  name,
  email,
  date,
  total
};
```

假设我们还有一个负责向客户发送电子邮件的模块，如下面的代码所示：

```js
/* Email.re */
let send = invoice: Invoice.t => ...
let invoice =
  Invoice.make(
    ~name="Raphael",
    ~email="persianturtle@gmail.com",
    ~date=Js.Date.make(),
    ~total=15.0,
  );
send(invoice);
```

由于`Invoice.t`类型是公开的，发票可以通过`Email`进行操作，如下面的代码所示：

```js
/* Email.re */
let invoice =
  Invoice.make(
    ~name="Raphael",
    ~email="persianturtle@gmail.com",
    ~date=Js.Date.make(),
    ~total=15.0,
  );
let invoice = {...invoice, total: invoice.total *. 0.8};
Js.log(invoice);
```

尽管`Invoice.t`类型是不可变的，但没有任何东西阻止`Email`通过一些修改的字段来覆盖发票绑定。然而，如果我们使`Invoice.t`类型抽象化，这就不可能了，因为`Email`无法操作抽象类型。`Email`模块可以访问的所有函数都无法与`Invoice.t`类型一起工作。

```js
/* Invoice.rei */
type t;
let make:
 (~name: string, ~email: string, ~date: Js.Date.t, ~total: float) => t;
```

现在，编译会给我们以下错误：

```js
8 │ let invoice = {...invoice, total: invoice.total *. 0.8};
9 │ Js.log(invoice);

The record field total can't be found.
```

如果我们决定允许其他模块向发票添加折扣，我们需要创建一个函数并将其包含在`Invoice`模块的模块签名中。假设我们只想允许每个发票只有一个折扣，并且限制折扣金额为十、十五或二十个百分点。我们可以这样实现：

```js
/* Invoice.re */
type t = {
 name: string,
 email: string,
 date: Js.Date.t,
 total: float,
 isDiscounted: bool,
};

type discount =
 | Ten
 | Fifteen
 | Twenty;

let make = (~name, ~email, ~date, ~total) => {
 name,
 email,
 date,
 total,
 isDiscounted: false,
};

let discount = (~invoice, ~discount) =>
 if (invoice.isDiscounted) {
 invoice;
 } else {
 {
 ...invoice,
 isDiscounted: true,
 total:
 invoice.total
 *. (
 switch (discount) {
 | Ten => 0.9
 | Fifteen => 0.85
 | Twenty => 0.8
 }
 ),
 };
 };

/* Invoice.rei */
type t;

type discount =
 | Ten
 | Fifteen
 | Twenty;

let make:
 (~name: string, ~email: string, ~date: Js.Date.t, ~total: float) => t;

let discount: (~invoice: t, ~discount: discount) => t;

/* Email.re */
let invoice =
 Invoice.make(
 ~name="Raphael",
 ~email="persianturtle@gmail.com",
 ~date=Js.Date.make(),
 ~total=15.0,
 );
Js.log(invoice);
```

现在，只要`Invoice`模块的公共 API（或模块签名）不改变，我们就可以自由地按照我们的意愿重构`Invoice`模块，而无需担心破坏其他模块的代码。为了证明这一点，让我们将`Invoice.t`重构为元组而不是记录，如下所示代码。只要我们不改变模块签名，`Email`模块就不需要做任何改变：

```js
/* Invoice.re */
type t = (string, string, Js.Date.t, float, bool);

type discount =
  | Ten
  | Fifteen
  | Twenty;

let make = (~name, ~email, ~date, ~total) => (
  name,
  email,
  date,
  total,
  false,
);

let discount = (~invoice, ~discount) => {
  let (name, email, date, total, isDiscounted) = invoice;
  if (isDiscounted) {
    invoice;
  } else {
    (
      name,
      email,
      date,
      total
      *. (
        switch (discount) {
        | Ten => 0.9
        | Fifteen => 0.85
        | Twenty => 0.8
        }
      ),
      true,
    );
  };
};

/* Invoice.rei */
type t;

type discount =
  | Ten
  | Fifteen
  | Twenty;

let make:
  (~name: string, ~email: string, ~date: Js.Date.t, ~total: float) => t;

let discount: (~invoice: t, ~discount: discount) => t;

/* Email.re */
let invoice =
  Invoice.make(
    ~name="Raphael",
    ~email="persianturtle@gmail.com",
    ~date=Js.Date.make(),
    ~total=15.0,
  );
let invoice = Invoice.(discount(~invoice, ~discount=Ten));
Js.log(invoice);
```

此外，多亏了`Invoice.t`抽象类型，我们保证发票只能折扣一次，并且只能按指定的百分比折扣。我们可以进一步扩展这个例子，要求对发票的所有更改都要进行记录。传统上，这类要求会在数据库事务之后添加副作用来解决，因为在 JavaScript 中，我们否则无法确定我们会记录所有对发票的更改。有了模块签名，我们可以在应用层解决这类要求。

# 幻影类型

看看我们之前的实现，如果我们不需要在运行时检查发票是否已经折扣，那会很好。我们能否在编译时检查发票是否已经折扣呢？使用幻影类型，我们可以做到。

幻影类型是具有类型变量的类型，但这种类型变量在其定义中没有被使用。为了更好地理解，让我们再次看看`option`类型，如下所示代码：

```js
type option('a) =
  | None
  | Some('a);
```

`option`类型有一个类型变量`'a`，并且这个类型变量在其定义中被使用。正如我们已经学到的，`option`是一个多态类型，因为它有一个类型变量。另一方面，幻影类型在其定义中不使用类型变量。让我们看看这在我们的发票管理示例中是如何有用的。

让我们改变`Invoice`模块的签名，使用幻影类型，如下所示：

```js
/* Invoice.rei */
type t('a);

type discounted;
type undiscounted;

type discount =
  | Ten
  | Fifteen
  | Twenty;

let make:
  (~name: string, ~email: string, ~date: Js.Date.t, ~total: float) =>
  t(undiscounted);

let discount:
  (~invoice: t(undiscounted), ~discount: discount) => t(discounted);
```

抽象类型`t`现在是`t('a)`。我们还有两个更多的抽象类型，如下所示代码：

```js
type discounted;
type undiscounted;
```

此外，请注意，`make`函数现在返回`t(undiscounted)`（而不是仅仅`t`），而`discount`函数现在接受`t(undiscounted)`并返回`t(discounted)`。记住，抽象`t('a)`接受一个类型变量，而这个类型变量恰好是`discounted`类型或`undiscounted`类型。

在实现中，我们现在可以去掉之前已有的运行时检查，如下所示代码：

```js
if (isDiscounted) {
  ...
} else {
  ...
}
```

现在，这个检查是在编译时完成的，因为`discount`函数只接受`undiscounted`类型的发票，如下所示代码：

```js
/* Invoice.re */
type t('a) = {
  name: string,
  email: string,
  date: Js.Date.t,
  total: float,
};

type discount =
  | Ten
  | Fifteen
  | Twenty;

let make = (~name, ~email, ~date, ~total) => {name, email, date, total};

let discount = (~invoice, ~discount) => {
  ...invoice,
  total:
    invoice.total
    *. (
      switch (discount) {
      | Ten => 0.9
      | Fifteen => 0.85
      | Twenty => 0.8
      }
    ),
};
```

这只是类型系统帮助我们更多地关注逻辑而不是错误处理的一种方式。以前，试图对发票进行两次折扣只会返回未更改的原始发票。现在，让我们在`Email.re`中尝试对发票进行两次折扣，使用以下代码：

```js
/* Email.re */
let invoice =
  Invoice.make(
    ~name="Raphael",
    ~email="persianturtle@gmail.com",
    ~date=Js.Date.make(),
    ~total=15.0,
  );
let invoice = Invoice.(discount(~invoice, ~discount=Ten));
let invoice = Invoice.(discount(~invoice, ~discount=Ten)); /* discounted twice */
Js.log(invoice);
```

现在，试图对发票进行两次折扣将导致编译时错误，如下所示：

```js
We've found a bug for you!

   7 │ );
   8 │ let invoice = Invoice.(discount(~invoice, ~discount=Ten));
   9 │ let invoice = Invoice.(discount(~invoice, ~discount=Ten));
  10 │ Js.log(invoice);

  This has type:
    Invoice.t(Invoice.discounted)
  But somewhere wanted:
    Invoice.t(Invoice.undiscounted)
```

这绝对是美丽的。然而，假设你想要能够发送任何发票——无论是打折的还是不打折的。我们使用幻影类型会导致问题吗？我们该如何编写一个接受任何发票类型的函数？记住，我们的发票类型是 `Invoice.t('a)`，如果我们想接受任何发票，我们保持类型参数，如下面的代码所示：

```js
/* Email.re */
let invoice =
  Invoice.make(
    ~name="Raphael",
    ~email="persianturtle@gmail.com",
    ~date=Js.Date.make(),
    ~total=15.0,
  );

let send: Invoice.t('a) => unit = invoice => {
 /* send invoice email */
 Js.log(invoice);
};

send(invoice);
```

因此，我们可以同时拥有我们的蛋糕并享用它。

# 多态变体

我们已经在上一章中简要地了解了多态变体。为了回顾，我们是在使用 `[@bs.unwrap]` 装饰器绑定到一些现有的 JavaScript 时了解到它们的。想法是 `[@bs.unwrap]` 可以用来绑定到一个现有的 JavaScript 函数，其中其参数可以是不同类型的。例如，假设我们想要绑定到以下函数：

```js
function dynamic(a) {
  switch (typeof a) {
    case "string":
      return "String: " + a;
    case "number":
      return "Number: " + a;
  }
}
```

假设这个函数应该只接受 `string` 类型或 `int` 类型的参数，不接受其他类型的参数。我们可以这样绑定到这个示例函数：

```js
[@bs.val] external dynamic : 'a => string = "";
```

然而，我们的绑定将允许无效的参数类型（例如 `bool`）。如果我们的编译器能够通过防止无效参数类型来帮助我们，那就更好了。一种方法是在多态变体上使用 `[@bs.unwrap]`。我们的绑定将如下所示：

```js
[@bs.val] external dynamic : ([@bs.unwrap] [
  | `Str(string)
  | `Int(int)
]) => string = "";
```

我们会这样使用绑定：

```js
dynamic(`Int(42));
dynamic(`Str("foo"));
```

现在，如果我们尝试传递一个无效的参数类型，编译器会告诉我们，如下面的代码所示：

```js
dynamic(42);

/*
We've found a bug for you!

This has type:
  int
But somewhere wanted:
  [ `Int of int | `Str of string ]
*/
```

这里的权衡是我们需要通过将参数包裹在多态变体构造函数中来传递参数，而不是直接传递。

一开始，你就会注意到正常变体和多态变体之间的以下两个区别：

1.  我们不需要显式声明多态变体的类型

1.  多态变体以反引号字符（`` ` ``）开头

无论何时你看到以反引号字符为前缀的构造函数，你就知道它是一个多态变体构造函数。多态变体构造函数可能或可能没有与之关联的类型声明。

# 这会与普通变体一起工作吗？

让我们尝试用普通变体来做这件事，看看会发生什么：

```js
type validArgs = 
  | Int(int)
  | Str(string);

[@bs.val] external dynamic : validArgs => string = "";

dynamic(Int(1));
```

前面实现的问题在于 `Int(1)` 不会编译成 JavaScript 数字。普通变体被编译成 `array`，我们的 `dynamic` 函数返回 `undefined` 而不是 `"Number: 42"`。函数返回 `undefined` 因为在 switch 语句中没有匹配到任何情况。

使用多态变体，BuckleScript 将 ``dynamic(`Int(42))`` 编译为 `dynamic(42)`，并且函数按预期工作。

# 高级类型系统特性

Reason 的类型系统功能非常全面，并且在过去的几十年里得到了精炼。我们迄今为止所看到的内容只是 Reason 类型系统的一个简介。在我看来，你应该在继续学习更高级的类型系统功能之前，先熟悉基础知识。如果没有经历过一个健全的类型系统本可以防止的错误，就很难欣赏类型安全等特性。如果没有对这本书中迄今为止所学的内容感到些许挫败，就很难欣赏高级类型系统功能。本书的范围并不包括过多地详细讨论高级类型系统功能，但我想要确保那些正在评估 Reason 作为选项的人知道，它的类型系统还有更多内容。

除了幻影类型和多态变体之外，Reason 还有**泛化代数数据类型**（**GADTs**）。模块可以使用函子（即在编译时间和运行时之间操作的模块函数）动态创建。Reason 还有类和对象——OCaml 中的 O 代表 objective。OCaml 的前身是一种名为 Caml 的语言，它首次出现在 20 世纪 80 年代中期。我们在本书中学到的内容在典型 React 应用程序的上下文中特别有用。我个人喜欢 Reason 是一种我可以不断成长并保持高效的语言。

如果你发现自己在类型系统上感到挫败，那么请通过 Discord 频道联系专家，他们很可能会帮助你解决你的问题。我总是对社区的帮助感到惊讶。而且别忘了，如果你只是想继续前进，你总是可以在需要时直接使用原始 JavaScript，并在准备好时再回来解决这个问题。

你可以在这里找到 Reason 的 Discord 频道：

[`discord.gg/reasonml`](https://discord.gg/reasonml)

也不必使用 Reason 类型系统的更高级功能。我们迄今为止所学的知识在为我们的 React 应用程序添加类型安全方面提供了很多价值。

# 摘要

到目前为止，我们已经看到了 Reason 如何通过其类型系统帮助我们构建更安全、更易于维护的代码库。变体允许我们使无效状态无法表示。类型系统有助于使重构过程不那么可怕、不那么痛苦。模块签名可以帮助我们在应用程序中强制执行业务规则。模块签名还充当基本文档，列出模块公开的内容，并根据公开的函数名称及其参数类型以及公开的类型，给出模块应该如何使用的基本概念。

在第六章“CSS-in-JS（在 Reason 中）”，我们将探讨如何使用 Reason 的类型系统通过一个包装 Emotion（[`emotion.sh`](https://emotion.sh)）的 CSS-in-Reason 库`bs-css`来强制执行有效的 CSS。
