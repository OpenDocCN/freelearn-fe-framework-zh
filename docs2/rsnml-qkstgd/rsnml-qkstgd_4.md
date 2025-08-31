# BuckleScript、Belt 和互操作性

在本章中，我们将更深入地了解 BuckleScript 特有的功能，这些功能对我们可用。我们还将学习递归和递归数据结构。在本章结束时，我们将完成 Reason 及其生态系统的介绍。在这个过程中，我们将完成以下工作：

+   更深入地了解了 Reason 的模块系统

+   探索了 Reason 的更多原始数据结构（数组和列表）

+   看到了各种管道操作符如何使代码更易于阅读

+   熟悉 Reason 和 Belt 标准库

+   为在 Reason 中使用创建了绑定到 JavaScript 模块

+   通过绑定到 React Transition Group 组件为我们的应用程序添加了路由转换

为了跟上进度，请使用您想要的任何环境。我们将要做的大部分内容都不是 ReasonReact 特有的。在本章的结尾，我们将继续构建我们的 ReasonReact 应用程序。

# 模块作用域

如您现在所知，所有`.re`文件都是模块，所有模块都是全局可用的——包括嵌套的模块。默认情况下，所有类型和绑定都可以通过提供命名空间在任何地方访问。然而，这样做会很快变得繁琐。幸运的是，我们有几种方法可以使这更加愉快：

```js
/* Foo.re */
type fromFoo =
  | Add(int, int)
  | Multiply(int, int);

let a = 1;
let b = 2;
```

接下来，我们将以不同的方式使用`Foo`模块的`fromFoo`类型及其在另一个模块内的绑定：

+   **选项 1**：没有任何糖：

```js
/* Bar.re */
let fromFoo = Foo.Add(Foo.a, Foo.b);
```

+   **选项 2**：将模块别名到更短的名字。例如，我们可以声明一个新的模块`F`并将其绑定到现有的模块`Foo`：

```js
/* Bar.re */
module F = Foo;
let fromFoo = F.Add(F.a, F.b);
```

+   **选项 3**：使用`Module.()`语法在本地打开模块。这种语法只适用于单个表达式：

```js
/* Bar.re */
let fromFoo = Foo.(Add(a, b));
```

+   **选项 4**：在面向对象的意义上，使用`include`让`Bar`扩展`Foo`：

```js
/* Bar.re */
include Foo;
let a = 4; /* override Foo.a */
let fromFoo = Add(a, b);
```

+   **选项 5**：全局`打开`模块。在大范围内，`open`应谨慎使用，因为它会变得难以知道哪些类型和绑定属于哪个模块：

```js
/* Bar.re */
open Foo;
let fromFoo = Add(a, b);
```

建议在局部作用域中使用`open`：

```js
/* Bar.re */
let fromFoo = {
  open Foo;
  Add(a, b);
};
```

上述语法将通过`refmt`重新格式化为选项 3 的语法，但请记住，选项 3 的语法只适用于单个表达式。例如，以下内容无法转换为选项 3 的语法：

```js
/* Bar.re */
let fromFoo = {
  open Foo;
  Js.log("foo");
  let result = Add(a, b);
};
```

Reason 标准库位于我们已可用的各种模块中。例如，Reason 的标准库包括一个`Array`模块，我们可以使用点符号（即`Array.length`）来访问其函数。

在第五章《有效的 ML》中，我们将学习如何隐藏模块的类型和绑定，以便在不需要它们全局可用时不可用。

# 数据结构

我们已经看到了 Reason 的几个原始数据结构，包括字符串、整数、浮点数、元组、记录和变体。让我们再探索一些。

# 数组

Reason 数组编译为常规 JavaScript 数组。Reason 数组如下所示：

+   同质（所有元素必须是同一类型）

+   可变

+   快速的随机访问和更新

它们看起来像这样：

```js
let array = [|"first", "second", "third"|];
```

访问和更新数组元素的方式与 JavaScript 中的相同：

```js
array[0] = "updated";
```

在 JavaScript 中，我们按照以下方式遍历数组：

```js
/* JavaScript */
array.map(e => e + "-mapped")
```

在 Reason 中做同样的事情，我们有几种不同的选择。

# 使用 Reason 标准库

Reason 标准库的 `Array` 模块包含几个函数，但并非所有你从 JavaScript 中期望的函数都有。然而，它确实有一个 `map` 函数：

```js
/* Reason standard library */
let array = [|"first", "second", "third"|];
Array.map(e => e ++ "-mapped", array);
```

`Array.map` 的类型如下：

```js
('a => 'b, array('a)) => array('b);
```

类型签名表明 `map` 接受一个类型为 `'a => 'b` 的函数，一个类型为 `'a` 的数组，并返回一个类型为 `'b` 的数组。注意，`'a` 和 `'b` 是 **类型变量**。类型变量就像普通变量一样，只是它们是类型。在上面的例子中，`map` 的类型是：

```js
(string => string, array(string)) => array(string);
```

这是因为类型变量 `'a` 和 `'b` 都被一致地替换成了具体的 `string` 类型。

注意，当使用 `Array.map` 时，编译后的输出不会编译成 JavaScript 的 `Array.prototype.map`——它有自己的实现：

```js
/* in the compiled output */
...
require("./stdlib/array.js");
...
```

Reason 标准库的文档可以在这里找到：

[ReasonML API](https://reasonml.github.io/api)

# 使用 Belt 标准库

Reason 标准库实际上是 OCaml 标准库。它不是针对 JavaScript 创建的。Belt 标准库是由创建 BuckleScript 的同一人 Hongbo Zhang 创建的，并随 BuckleScript 一起提供。Belt 是针对 JavaScript 创建的，并且以其性能而闻名。Belt 标准库通过 `Belt` 模块访问：

```js
/* Belt standard library */
let array = [|"first", "second", "third"|];
Belt.Array.map(array, e => e ++ "-mapped");
```

Belt 标准库的文档可以在这里找到：

[BuckleScript API Belt](https://bucklescript.github.io/bucklescript/api/Belt.html)

# 使用 BuckleScript 内置的 JavaScript 绑定

另一个很好的选择是使用 BuckleScript 内置的 JavaScript 绑定，这些绑定可以在 `Js` 模块中找到：

```js
/* BuckleScript's JavaScript bindings */
let array = [|"first", "second", "third"|];
Js.Array.map(e => e ++ "-mapped", array);
```

这种选项的优点是不需要在编译后的输出中依赖任何依赖。它还有一个非常熟悉的 API。然而，由于并非所有的 Reason 数据结构都存在于 JavaScript 中，你可能会使用标准库。如果是这样，请优先选择 Belt。

BuckleScript 绑定文档可以在这里找到：

[BuckleScript API Js](https://bucklescript.github.io/bucklescript/api/Js.html)

# 使用自定义绑定

没有任何阻止你编写自己的自定义绑定的：

```js
[@bs.send] external map: (array('a), 'a => 'b) => array('b) = "";
let array = [|"first", "second", "third"|];
map(array, e => e ++ "-mapped")
```

当然，你应该优先使用 `Js` 模块中的内置绑定。我们将在本章后面探索更多自定义绑定。

# 使用原生 JavaScript

最后一个选择是在 Reason 中使用实际的 JavaScript：

```js
let array = [|"first", "second", "third"|];
let map = [%raw {|
  function(f, array) {
    return array.map(f)
  }
|}];
map(e => e ++ "-mapped", array)
```

BuckleScript 让我们能够直接进入原生 JavaScript，这样我们可以在学习的同时保持高效。当然，当我们这样做的时候，我们放弃了 Reason 为我们提供的安全性。所以，一旦你准备好了，将任何原生 JavaScript 代码转换回更符合 Reason 习惯的代码。

当涉及到使用原生 JavaScript 时，使用 `%` 表示表达式，`%%` 表示语句。记住，`{| |}` 是 Reason 的多行字符串语法：

```js
let array = [%raw "['first', 'second', 'third']"];
[%%raw {|
  array = array.map(e => e + "-mapped");
|}];
```

使用原始表达式语法，我们也能够注释类型：

```js
let array: array(string) = [%raw "['first', 'second', 'third']"];
```

我们甚至可以注释函数类型：

```js
let random: unit => float = [%raw
  {|
    function() {
     return Math.random();
    }
  |}
];
```

虽然数组在从 JavaScript 过来时很熟悉，但你可能会发现自己更倾向于使用列表，因为它们在函数式编程中无处不在。列表既是不可变的也是递归的。现在让我们看看如何使用这种递归数据结构。

# 列表

Reason 列表如下：

+   同质

+   不可变

+   快速在列表前添加和访问头部

它们看起来像这样：

```js
let list = ["first", "second", "third"];
```

在这种情况下，列表的头部是 `"first"`。到目前为止，我们已经看到处理不可变数据结构并不困难。我们不是进行修改，而是创建更新的副本。

在处理列表时，我们不能直接使用 JavaScript 绑定，因为列表在 JavaScript 中不是一个原始数据结构。然而，我们可以将列表转换为数组，反之亦然：

```js
/* Belt standard library */
let list = ["first", "second", "third"];
let array = Belt.List.toArray(list);

let array = [|"first", "second", "third"|];
let list = Belt.List.fromArray(array);

/* Reason standard library */
let list = ["first", "second", "third"];
let array = Array.of_list(list);

let array = [|"first", "second", "third"|];
let list = Array.to_list(array);
```

但我们也可以直接在列表上使用 `map`：

```js
/* Belt standard library */
let list = ["first", "second", "third"];
Belt.List.map(list, e => e ++ "-mapped");

/* Reason standard library */
let list = ["first", "second", "third"];
List.map(e => e ++ "-mapped", list);
```

在控制台中记录 `list` 显示，列表在 JavaScript 中表示为嵌套数组，其中每个数组总是有两个元素：

```js
["first", ["second", ["third", 0]]]
```

在理解列表是一个递归数据结构之后，这就有意义了。Reason 列表是**单链表**。列表中的每个元素要么是**空**（在 JavaScript 中表示为 `0`），要么是值和另一个列表的组合。

`list` 的一个示例类型定义揭示了 `list` 是一个变体：

```js
type list('a) = Empty | Head('a, list('a));
```

注意：类型定义可以是递归的。

Reason 提供了一些语法糖，简化了其更冗长的版本：

```js
Head("first", Head("second", Head("third", Empty)));
```

# 递归

由于列表是一个递归数据结构，我们在处理它时通常使用递归。

为了热身，让我们编写一个（初级的）求整数列表总和的函数：

```js
let rec sum = list => switch(list) {
  | [] => 0
  | [hd, ...tl] => hd + sum(tl)
};
```

+   这是一个递归函数，因此需要 `rec` 关键字（即，`let rec` 而不是 `let`）

+   我们可以在列表上使用模式匹配（就像任何其他变体和许多其他数据结构一样）

+   从示例类型定义中，`Empty` 表示为 `[]`，`Head` 表示为 `[hd, ...tl]`，其中 `hd` 是列表的**头部**，`tl` 是列表的其余部分（即，**尾部**）

+   `tl` 可以是 `[]`（即，`Empty`），当它是这样的时候，递归停止

将列表 `[1, 2, 3]` 传递给 `sum`，会产生以下步骤：

```js
sum([1, 2, 3])
1 + sum([2, 3])
1 + 2 + sum([3])
1 + 2 + 3
6
```

让我们通过分析另一个（初级的）反转列表的函数来更熟悉列表和递归：

```js
let rec reverse = list => switch(list) {
  | [] => []
  | [hd, ...tl] => reverse(tl) @ [hd]
};
```

+   再次，我们使用 `rec` 来定义一个递归函数

+   再次，我们在列表上使用模式匹配——如果它是空的，停止递归；否则，继续使用更小的列表

+   `@` 操作符将第二个列表追加到第一个列表的末尾

将先前定义的列表（`["first", "second", "third"]`）传递进去，会产生以下步骤：

```js
reverse(["first", "second", "third"])
reverse(["second", "third"]) @ ["first"]
reverse(["third"]) @ ["second"] @ ["first"]
reverse([]) @ ["third"] @ ["second"] @ ["first"]
[] @ ["third"] @ ["second"] @ ["first"]
["third", "second", "first"]
```

这种 reverse 的实现是初级的，原因有两个：

+   它不是尾调用优化（我们的 `sum` 函数也不是）

+   它使用 `append` (`@`)，这比 `prepend` 慢

一个更好的实现是使用带有累加器的局部辅助函数：

```js
let reverse = list => {
  let rec aux = (list, acc) => switch(list) {
    | [] => acc
    | [hd, ...tl] => aux(tl, [hd, ...acc])
  };
  aux(list, []);
};
```

现在，它的尾调用已优化，并使用 `prepend` 代替 `append`。在 Reason 中，您可以使用 `...` 语法向列表中添加元素：

```js
let list = ["first", "second", "third"];
let list = ["prepended", ...list];
```

将列表（`["first", "second", "third"]`）传入大致相当于以下步骤：

```js
reverse(["first", "second", "third"])
aux(["first", "second", "third"], [])
aux(["second", "third"], ["first"])
aux(["third"], ["second", "first"])
aux([], ["third", "second", "first"])
["third", "second", "first"]
```

注意，在非尾递归版本中，Reason 无法在递归完成之前创建列表。在尾递归版本中，累加器（即 `aux` 的第二个参数）在每次迭代后更新。

尾递归（即尾调用优化）函数的好处是能够重用当前的栈帧。因此，尾递归函数永远不会发生栈溢出，但非尾递归函数在迭代足够多的情况下可能会发生栈溢出。

# 管道运算符

Reason 有两个管道运算符：

```js
|> (pipe)
-> (fast pipe)
```

两个管道运算符都将参数传递给函数。`|>` 管道运算符将参数传递给函数的最后一个参数，而 `->` 快速管道运算符将参数传递给函数的第一个参数。

看看这些：

```js
three |> f(one, two)
one -> f(two, three)
```

它们等同于以下内容：

```js
f(one, two, three)
```

如果函数只接受一个参数，那么两个管道的工作方式相同，因为函数的第一个参数也是函数的最后一个参数。

使用这些管道运算符非常流行，因为一旦您掌握了它们，代码的可读性就会大大提高。

我们不需要使用这个：

```js
Belt.List.(reduce(map([1, 2, 3], e => e + 1), 0, (+)))
```

我们可以以不需要读者从内向外阅读的方式编写它：

```js
Belt.List.(
 [1, 2, 3]
 ->map(e => e + 1)
 ->reduce(0, (+))
);
```

如您所见，使用快速管道看起来类似于 JavaScript 中的链式调用。与 JavaScript 不同，我们可以传递 `+` 函数进行缩减，因为它只是一个接受两个参数并相加的正常函数。括号是必要的，以便让 Reason 将中缀运算符 `(+)` 视为一个标识符。

# 使用 Belt

让我们利用本章到目前为止所学的知识来编写一个小程序，该程序创建一副牌，将其洗牌，并从牌顶抽取五张牌。为此，我们将使用 Belt 的 `Option` 和 `List` 模块，以及快速管道运算符。

# 选项模块

Belt 的 `Option` 模块是一组用于处理 `option` 类型的实用函数。例如，要解包选项并抛出运行时异常，如果选项的值为 `None`，我们可以使用 `getExn`：

```js
let foo = Some(3)->Belt.Option.getExn;
Js.log(foo); /* 3 */

let foo = None->Belt.Option.getExn;
Js.log(foo); /* raises getExn exception */
```

能够抛出运行时异常的 Belt 函数总是带有 `Exn` 后缀。

解包无法抛出运行时异常的选项的替代函数是 `getWithDefault`：

```js
let foo = None->Belt.Option.getWithDefault(0);
Js.log(foo); /* 0 */
```

`Option` 模块提供了其他几个函数，如 `isSome`、`isNone`、`map`、`mapWithDefault` 等。有关详细信息，请参阅文档。

Belt 选项模块文档可以在这里找到：

[`bucklescript.github.io/bucklescript/api/Belt.Option.html`](https://bucklescript.github.io/bucklescript/api/Belt.Option.html)

# 列表模块

列表模块是用于列表数据类型的实用工具。要查看 Belt 为处理列表提供的函数，请检查 Belt 的 `List` 模块文档。

Belt 列表模块文档可以在这里找到：

[`bucklescript.github.io/bucklescript/api/Belt.List.html`](https://bucklescript.github.io/bucklescript/api/Belt.List.html)

让我们关注其中的一些。

# make

`make` 函数用于创建一个已填充的列表。它接受一个整数作为列表的长度，以及列表中每个项目的值。其类型如下：

```js
(int, 'a) => Belt.List.t('a)
```

`Belt.List.t` 被公开作为 `list` 类型的别名，因此我们可以说 `Belt.List.make` 的类型如下：

```js
(int, 'a) => list('a)
```

我们可以用它来创建一个包含十个字符串的列表，如下所示：

```js
let list = Belt.List.make(10, "string");
```

在 第五章，*Effective ML*，我们将学习如何显式地从一个模块中公开或隐藏类型和绑定。

# makeBy

`makeBy` 函数类似于 `make` 函数，但它接受一个函数，用于根据项目的索引确定每个项目的值。

`makeBy` 函数的类型如下：

```js
(int, int => 'a) => Belt.List.t('a)
```

我们可以用它来创建一个包含十个项目的列表，其中每个项目等于其索引：

```js
let list = Belt.List.makeBy(10, i => i);
```

# shuffle

`shuffle` 函数随机洗牌。它的类型如下：

```js
Belt.List.t('a) => Belt.List.t('a)
```

它接受一个列表并返回一个新的列表。让我们用它来洗我们的整数列表：

```js
let list = Belt.List.(makeBy(10, i => i)->shuffle);
```

# take

`take` 函数接受一个列表和一个长度，并返回从列表头部开始的长度等于请求长度的子集。由于子集的请求长度可能超过原始列表的长度，因此结果被包裹在选项中。其类型如下：

```js
(Belt.List.t('a), int) => option(Belt.List.t('a))
```

我们可以从洗好的列表中取出前两个项目，如下所示：

```js
let list = Belt.List.(makeBy(10, i => i)->shuffle->take(2));
```

# 堆叠牌的示例

现在，我们已经准备好将之前章节中学到的知识结合起来。你会如何编写一个程序来创建一副牌，洗牌，并抽取前五张牌？在查看以下示例之前，先自己试一试。

```js
type suit =
  | Hearts
  | Diamonds
  | Spades
  | Clubs;

type card = {
  suit,
  rank: int,
};

Belt.List.(
  makeBy(52, i =>
    switch (i / 13, i mod 13) {
    | (0, rank) => {suit: Hearts, rank: rank + 1}
    | (1, rank) => {suit: Diamonds, rank: rank + 1}
    | (2, rank) => {suit: Spades, rank: rank + 1}
    | (3, rank) => {suit: Clubs, rank: rank + 1}
    | _ => assert(false)
    }
  )
  ->shuffle
  ->take(5)
  ->Belt.Option.getExn
  ->(
      cards => {
        let rankToString = rank =>
          switch (rank) {
          | 1 => "Ace"
          | 13 => "King"
          | 12 => "Queen"
          | 11 => "Jack"
          | rank => string_of_int(rank)
          };

        let suitToString = suit =>
          switch (suit) {
          | Hearts => "Hearts"
          | Diamonds => "Diamonds"
          | Spades => "Spades"
          | Clubs => "Clubs"
          };

        map(cards, ({rank, suit}) =>
          rankToString(rank) ++ " of " ++ suitToString(suit)
        );
      }
    )
  ->toArray
  ->Js.log
);
```

这会产生一个包含五张随机字符串格式的牌的数组：

```js
[
  "Queen of Clubs",
  "4 of Clubs",
  "King of Spades",
  "Ace of Hearts",
  "9 of Spades"
]
```

# Currying

Belt 标准库的一些函数有 *U* 后缀，例如这个：

```js
Belt.List.makeBy
```

你可以看到这里的后缀：

```js
Belt.List.makeByU
```

*U* 后缀代表 *uncurried*。在继续之前，让我们定义 currying。

在 Reason 中，每个函数恰好接受一个参数。这似乎与我们之前的许多例子相矛盾：

```js
let add = (a, b) => a + b;
```

前面的 `add` 函数看起来像接受两个参数，但实际上它只是以下内容的语法糖：

```js
let add = a => b => a + b;
```

`add` 函数接受一个单一参数 `a`，它返回一个接受单一参数 `b` 的函数，然后返回 `a + b` 的结果。

在 Reason 中，两种版本都是有效的，并且有相同的编译输出。在 JavaScript 中，前两种版本都是有效的，但它们并不相同；它们需要以不同的方式使用才能得到相同的结果。第二个需要像这样调用：

```js
add(2)(3);
```

这是因为 `add` 返回一个函数，然后需要再次调用，因此有两个括号组。理由接受两种用法：

```js
add(2, 3);
add(2)(3);
```

Currying 的好处是它使函数组合更容易。你可以轻松创建一个部分应用的函数，`addOne`：

```js
let addOne = add(1);
```

这个 `addOne` 函数可以被传递给其他函数，例如 `map`。也许你想使用这个特性将一个函数传递给 ReasonReact 子组件，并使用父组件的 `self` 部分应用。

令人困惑的是，`add` 的任何版本的编译输出如下：

```js
function add(a, b) {
  return a + b | 0;
}
```

中间函数在哪里？尽可能的情况下，BuckleScript 会优化编译输出以避免不必要的函数分配，从而提高性能。

记住，由于 Reason 的中缀运算符只是普通函数，我们本可以这样做：

```js
let addOne = (+)(1);
```

# 未应用柯里化的函数

由于 JavaScript 的动态特性，BuckleScript 无法总是优化编译输出以删除中间函数。然而，你可以使用以下语法告诉 BuckleScript 未应用柯里化一个函数：

```js
let add = (. a, b) => a + b;
```

未应用柯里化的语法是参数列表中的点。它需要在声明和调用位置都存在：

```js
let result = add(. 2, 3); /* 5 */
```

如果调用位置没有使用未应用柯里化的语法，BuckleScript 将抛出编译时错误：

```js
let result = add(2, 3);

We've found a bug for you!

This is an uncurried BuckleScript function. It must be applied with a dot.

Like this: foo(. a, b)
Not like this: foo(a, b)
```

此外，如果在调用位置缺少函数的一些参数，则会抛出编译时错误：

```js
let result = add(. 2);

We've found a bug for you!

Found uncurried application [@bs] with arity 2, where arity 1 was expected.
```

术语 `arity` 指的是函数接受的参数数量。

# makeByU

如果我们未应用其第二个参数，我们可以用 `makeByU` 替换 `makeBy`。这将提高性能（在我们的例子中是微不足道的）：

```js
...
makeByU(52, (. i) =>
  switch (i / 13, i mod 13) {
  | (0, rank) => {suit: Hearts, rank: rank + 1}
  | (1, rank) => {suit: Diamonds, rank: rank + 1}
  | (2, rank) => {suit: Spades, rank: rank + 1}
  | (3, rank) => {suit: Clubs, rank: rank + 1}
  | _ => assert(false)
  }
)
...
```

点语法需要在 `i` 的周围使用括号。

# JavaScript 互操作性

术语 **互操作性** 指的是 Reason 程序在 Reason 中使用现有 JavaScript 的能力。BuckleScript 提供了一个出色的系统，用于在 Reason 中使用现有的 JavaScript 代码，并且也使得在 JavaScript 中使用 Reason 代码变得容易。

# 在 Reason 中使用 JavaScript

我们已经看到了如何在 Reason 中使用原始 JavaScript。现在让我们关注如何绑定到现有的 JavaScript。要将值绑定到命名引用，我们通常使用 `let`。然后，该绑定可以在后续代码中使用。当我们要绑定的值位于 JavaScript 中时，我们使用 `external`。`external` 绑定就像 `let` 一样，因为它可以在后续代码中使用。与 `let` 不同的是，`external` 通常伴随着 BuckleScript 装饰器，如 `[@bs.val]`。

# 理解 `[@bs.val]` 装饰器

我们可以使用 `[@bs.val]` 来绑定到全局值和函数。通常，语法如下：

```js
[@bs.val] external alert: string => unit = "alert";
```

+   一个或多个 BuckleScript 装饰器（即 `[@bs.val]`）

+   `external` 关键字

+   绑定的命名引用

+   类型声明

+   一个等号

+   一个字符串

`external` 关键字将 `alert` 绑定到类型为 `string => unit` 的值，并绑定到字符串 `alert`。字符串 `alert` 是上述外部声明的值，也是将在编译输出中使用的值。当外部绑定的名称与其字符串值相等时，字符串可以留空：

```js
[@bs.val] external alert: string => unit = "";
```

使用绑定就像使用任何其他绑定一样：

```js
alert("hi!");
```

# 理解 `[@bs.scope]` 装饰器

要绑定到 `window.location.pathname`，我们使用 `[@bs.scope]` 添加一个作用域。这定义了 `[@bs.val]` 的作用域。例如，如果你想绑定到 `window.location` 的 `pathname` 属性，你可以指定作用域为 `[@bs.scope ("window", "location")]`：

```js
[@bs.val] [@bs.scope ("window", "location")] external pathname: string = "";
```

或者，我们可以使用 `[@bs.val]` 仅在字符串中包含作用域：

```js
[@bs.val] external pathname: string = "window.location.pathname";
```

# 理解 `[@bs.send]` 装饰器

`[@bs.send]` 装饰器用于绑定到对象的函数和方法。当使用 `[@bs.send]` 时，第一个参数总是对象。如果有剩余的参数，它们将被应用到对象的方法上：

```js
[@bs.val] external document: Dom.document = "";
[@bs.send] external getElementById: (Dom.document, string) => Dom.element = "";
let element = getElementById(document, "root");
```

`Dom` 模块也由 BuckleScript 提供，并为 DOM 提供类型声明。

Dom 模块文档可以在这里找到：

[`bucklescript.github.io/bucklescript/api/Dom.html`](https://bucklescript.github.io/bucklescript/api/Dom.html)

此外，还有一个用于 Node.js 的 Node 模块：

[`bucklescript.github.io/bucklescript/api/Node.html`](https://bucklescript.github.io/bucklescript/api/Node.html)

在编写外部声明时要小心，因为你可能会无意中向类型系统撒谎，这可能导致运行时类型错误。例如，我们告诉 Reason 我们的 `getElementById` 绑定总是返回一个 `Dom.element`，但当 DOM 找不到具有提供的 ID 的元素时，它返回 `undefined`。一个更正确的绑定方式如下：

```js
[@bs.send] external getElementById: (Dom.document, string) => option(Dom.element) = "";
```

# 理解 `[@bs.module]` 装饰器

要导入一个节点模块，使用 `[@bs.module]`。编译输出取决于在 `bsconfig.json` 中使用的 `package-specs` 配置。我们使用 `es6` 作为模块格式。

```js
[@bs.module] external leftPad: (string, int) => string = "left-pad";
let result = leftPad("foo", 6);
```

这编译成以下内容：

```js
import * as LeftPad from "left-pad";

var result = LeftPad("foo", 6);

export {
  result ,
}
```

将模块格式设置为 `commonjs` 导致以下编译输出：

```js
var LeftPad = require("left-pad");

var result = LeftPad("foo", 6);

exports.result = result;
```

当没有字符串参数传递给 `[@bs.module]` 时，默认值将被导入。

# 合理的 API

当绑定到现有的 JavaScript API 时，考虑你如何在 Reason 中使用该 API。即使那些严重依赖 JavaScript 动态类型的现有 JavaScript API 也可以在 Reason 中使用。BuckleScript 利用高级类型系统技术，让我们可以利用 Reason 的类型系统来使用这些 API。

从 BuckleScript 文档中，查看以下 JavaScript 函数：

```js
function padLeft(value, padding) {
  if (typeof padding === "number") {
    return Array(padding + 1).join(" ") + value;
  }
  if (typeof padding === "string") {
    return padding + value;
  }
  throw new Error(`Expected string or number, got '${padding}'.`);
}
```

如果我们要在 Reason 中绑定到这个函数，使用 `padding` 作为变体会很方便。以下是实现方式：

```js
[@bs.val]
external padLeft: (
  string,
  [@bs.unwrap] [
    | `Str(string)
    | `Int(int)
  ])
  => string = "";

padLeft("Hello World", `Int(4));
padLeft("Hello World", `Str("Message: "));
```

这编译成以下内容：

```js
padLeft("Hello World", 4);
padLeft("Hello World", "Message: ");
```

`padLeft` 的类型是 `(string, some_variant) => string`，其中 `some_variant` 使用一个名为 **多态变体** 的高级类型系统特性，它使用 `[@bs.unwrap]` 将其转换为 JavaScript 可以理解的内容。我们将在 第五章，*有效的 ML* 中了解更多关于多态变体的内容。

# BuckleScript 文档

虽然这只是一个简要的介绍，但你可以看出 BuckleScript 有很多工具可以帮助我们与惯用 JavaScript 进行通信。我强烈建议你阅读 BuckleScript 文档，以了解更多关于 JavaScript 互操作性的信息。

BuckleScript 文档可以在以下位置找到：

[`bucklescript.github.io/docs/interop-overview`](https://bucklescript.github.io/docs/interop-overview)

# 绑定到现有的 ReactJS 组件

ReactJS 组件不是 Reason 组件。要使用现有的 ReactJS 组件，我们使用 `[@bs.module]` 来导入节点模块，然后使用 `ReasonReact.wrapJsForReason` 辅助函数将 ReactJS 组件转换为 Reason 组件。还有一个 `ReasonReact.wrapReasonForJs` 辅助函数用于在 ReactJS 中使用 Reason。

让我们从 第三章 中我们停止的地方继续构建我们的应用程序：*创建 ReasonReact 组件*：

```js
git clone https://github.com/PacktPublishing/ReasonML-Quick-Start-Guide.git
cd ReasonML-Quick-Start-Guide
cd Chapter03/app-end
npm install
```

在这里，我们将通过绑定现有的 React Transition Group 组件来添加路由转换：

React Transition Group 文档可以在以下位置找到：

[`reactcommunity.org/react-transition-group/`](https://reactcommunity.org/react-transition-group/)

# 导入依赖

运行 `npm install --save react-transition-group` 来安装依赖。

让我们创建一个名为 `ReactTransitionGroup.re` 的新文件来存放这些绑定。在这个文件中，我们将绑定到 `TransitionGroup` 和 `CSSTransition` 组件：

```js
[@bs.module "react-transition-group"]
external transitionGroup: ReasonReact.reactClass = "TransitionGroup";

[@bs.module "react-transition-group"]
external cssTransition: ReasonReact.reactClass = "CSSTransition";
```

# 创建 make 函数

接下来，我们创建组件所需的 `make` 函数。这是我们使用 `ReasonReact.wrapJsForReason` 辅助函数的地方。

对于 `TransitionGroup`，我们不需要任何属性。由于需要 `~props` 参数，我们传递 `Js.Obj.empty()`。`~reactClass` 参数传递了我们之前步骤中创建的外部绑定：

```js
module TransitionGroup = {
  let make = children =>
    ReasonReact.wrapJsForReason(
      ~reactClass=transitionGroup,
      ~props=Js.Obj.empty(),
      children,
    );
};
```

现在，`ReactTransitionGroup.TransitionGroup` 是一个 ReasonReact 组件，我们可以在我们的应用程序中使用它。

# 使用 [@bs.deriving abstract]

`CSSTransitionGroup` 需要以下属性：

+   `_in`

+   `timeout`

+   `classNames`

由于 `in` 是 Reason 的保留字，因此约定在 Reason 中使用 `_in`，并由 BuckleScript 编译为 JavaScript 中的 `in` 使用 `[@bs.as "in"]`。

BuckleScript 提供 `[@bs.deriving abstract]` 以便于处理某些类型的 JavaScript 对象。我们不需要在 JavaScript 中创建对象并绑定到该对象，可以直接使用 BuckleScript 创建该对象：

```js
[@bs.deriving abstract]
type cssTransitionProps = {
  [@bs.as "in"] _in: bool,
  timeout: int,
  classNames: string,
};
```

注意：`cssTransitionProps` 不是一个记录类型，它看起来像是一个。

当使用 `[@bs.deriving abstract]` 时，会自动提供一个辅助函数来创建该形状的 JavaScript 对象。这个辅助函数也命名为 `cssTransitionProps`。我们在组件的 `make` 函数中使用这个辅助函数来创建组件的属性：

```js
module CSSTransition = {
  let make = (~_in: bool, ~timeout: int, ~classNames: string, children) =>
    ReasonReact.wrapJsForReason(
      ~reactClass=cssTransition,
      ~props=cssTransitionProps(~_in, ~timeout, ~classNames),
      children,
    );
};
```

# 使用组件

现在，在 `App.re` 中，我们可以更改渲染函数以使用这些组件。我们将更改如下：

```js
<main> {currentRoute.component} </main>
```

现在它看起来如下所示：

```js
<main>
  ReactTransitionGroup.(
    <TransitionGroup>
      <CSSTransition
        key={currentRoute.title} _in=true timeout=900 classNames="routeTransition">
        {currentRoute.component}
      </CSSTransition>
    </TransitionGroup>
  )
</main>
```

注意：键（key）属性是一个特殊的 ReactJS 属性，不应成为 `ReasonReact.wrapJsForReason` 组件的 props 参数的一部分。对于特殊的 ReactJS ref 属性也是如此。

为了完整性，以下是相应的 CSS，可以在 `ReactTransitionGroup.scss` 中找到：

```js
@keyframes enter {
  from {
    opacity: 0;
    transform: translateY(50px);
  }
}

@keyframes exit {
  to {
    opacity: 0;
    transform: translateY(50px);
  }
}

.routeTransition-enter.routeTransition-enter-active {
  animation: enter 500ms ease 400ms both;
}

.routeTransition-exit.routeTransition-exit-active {
  animation: exit 400ms ease both;
}
```

一定要在 `ReactTransitionGroup.re` 中要求前面的内容：

```js
/* ReactTransitionGroup.re */
[@bs.val] external require: string => string = "";
require("../../../src/ReactTransitionGroup.scss");
```

现在，当切换路由时，旧路由的内容会先向下动画并淡出，然后新路由的内容才会向上动画并淡入。

# 摘要

BuckleScript 非常强大，因为它让我们以一种非常愉快的方式与惯用的 JavaScript 进行交互。它还提供了 Belt 标准库，该库是考虑到 JavaScript 而创建的。我们学习了数组和列表，并看到了如何在 Reason 中使用现有的 ReactJS 组件是多么容易。

在 第五章，《有效的机器学习》中，我们将学习如何使用模块签名在构建自动完成输入组件时隐藏组件的实现细节。我们最初会使用硬编码的数据，然后在 第六章，《CSS-in-JS（在 Reason 中）》中，我们将把数据移动到 `localStorage`（客户端 Web 存储）。
