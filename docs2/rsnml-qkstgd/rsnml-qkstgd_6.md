# CSS-in-JS（在 Reason 中）

React 的一个优点是它允许我们将组件的标记、行为和样式放在一个文件中。这种组合对开发者的体验、版本控制和代码质量产生了连锁反应（无意中用了双关语）。在本章中，我们将简要探讨 CSS-in-JS 是什么以及我们如何在 Reason 中处理 CSS-in-JS。当然，如果你更喜欢，完全可以将组件拆分到单独的文件中，并/或使用更传统的 CSS 解决方案。

在本章中，我们将探讨以下主题：

+   什么是 CSS-in-JS？

+   使用`styled-components`

+   使用`bs-css`

# 什么是 CSS-in-JS？

定义 CSS-in-JS 目前在 JavaScript 社区中是一个有争议的话题。CSS-in-JS 是在组件时代诞生的。现代网络主要使用组件模型构建。几乎所有的 JavaScript 框架都采用了它。随着其采用率的增长，越来越多的团队开始在同一项目的各个组件上工作。想象一下，你在一个分布式团队中工作，正在开发一个大型应用程序，每个团队都在并行地开发一个组件。如果没有团队标准化 CSS 约定，你将遇到 CSS 作用域问题。如果没有某种类型的标准化 CSS 风格指南，多个团队很容易对类名进行样式化，从而影响其他未预期的组件。随着时间的推移，出现了一些解决方案来解决这些问题以及其他与 CSS 相关的规模问题。

# 简要历史

一些流行的 CSS 约定包括 BEM、SMACSS 和 OOCSS。每个解决方案都要求开发者学习该约定并记住正确应用它；否则，仍然可能会遇到令人沮丧的作用域问题。

CSS 模块成为了一个更安全的选项，开发者可以将 CSS 导入 JavaScript 模块，构建步骤会自动将 CSS 局部作用域到该 JavaScript 模块。CSS 本身仍然是在一个正常的 CSS（或 SASS）文件中编写的。

CSS-in-JS 更进一步，允许你直接在 JavaScript 模块中编写 CSS，自动将 CSS 局部作用域到组件。这对许多开发者来说感觉是正确的；而有些人从一开始就不喜欢它。一些 CSS-in-JS 解决方案，如`styled-components`，允许开发者直接将 CSS 与组件耦合。你不必使用`<header className="..." />`，而是可以有`<Header />`，其中`Header`组件是用`styled-components`及其 CSS 定义的，如下面的代码所示：

```js
import React from 'react';
import styled from 'styled-components';

const Header = styled.header`
  font-size: 1.5em;
  text-align: center;
  color: dodgerblue;
`;
```

曾经有一段时间，`styled-components`存在性能问题，因为 JavaScript 包必须下载、编译和执行，然后库才能在 DOM 中动态创建样式表。这些问题的现在已经基本得到解决，多亏了服务器端渲染的支持。那么，我们能在 Reason 中这样做吗？让我们看看吧！

# 使用 styled-components

`styled-components`最受欢迎的功能之一是能够根据组件的属性动态创建 CSS。使用这个功能的一个原因是可以创建组件的替代版本。这些替代版本将被封装在样式化组件本身中。以下是一个示例，其中文本可以是居中对齐或左对齐，并且可选地带有下划线。

```js
const Title = styled.h1`
  text-align: ${props => props.center ? "center" : "left"};
  text-decoration: ${props => props.underline ? "underline" : "none"};
  color: white;
  background-color: coral;
`;

render(
  <div>
    <Title>I'm Left Aligned</Title>
    <Title center>I'm Centered!</Title>
    <Title center underline>I'm Centered & Underlined!</Title>
  </div>
);
```

在 Reason 的上下文中，挑战在于通过`style-components` API 创建一个可以动态处理属性的组件。考虑以下对`styled.h1`函数和我们的`<Title />`组件的绑定。

```js
/* StyledComponents.re */
[@bs.module "styled-components"] [@bs.scope "default"] [@bs.variadic]
external h1: (array(string), array('a)) => ReasonReact.reactClass = "h1";

module Title = {
  let title =
    h1(
      [|
        "text-align: ",
        "; text-decoration: ",
        "; color: white; background-color: coral;",
      |],
      [|
        props => props##center ? "center" : "left",
        props => props##underline ? "underline" : "none",
      |],
    );

  [@bs.deriving abstract]
  type jsProps = {
    center: bool,
    underline: bool,
  };

  let make = (~center=false, ~underline=false, children) =>
    ReasonReact.wrapJsForReason(
      ~reactClass=title,
      ~props=jsProps(~center, ~underline),
      children,
    );
};
```

`h1`函数接受一个字符串数组作为其第一个参数，以及一个表达式数组作为其第二个参数。这是因为这是 ES5 对 ES6 标记模板字面量的表示。在`h1`函数的情况下，表达式数组是传递给 React 组件的属性所调用的函数。

我们使用`[@bs.variadic]`装饰器来允许任意数量的参数。在 Reason 端，我们使用一个数组，而在 JavaScript 端，这个数组被展开为任意数量的参数。

# 使用[@bs.variadic]

让我们快速偏离一下主题，进一步探讨`[@bs.variadic]`。假设你想要绑定到`Math.max()`，它可以接受一个或多个参数：

```js
/* JavaScript */
Math.max(1, 2);
Math.max(1, 2, 3, 4);
```

这是一个完美的`[@bs.variadic]`用例。我们在 Reason 端使用一个数组来保存参数，这个数组将被展开以匹配上述 JavaScript 中的语法。

```js
/* Reason */
[@bs.scope "Math"][@bs.val][@bs.variadic] external max: array('a) => unit = "";
max([|1, 2|]);
max([|1, 2, 3, 4|]);
```

好的，我们回到了`styled-components`的例子。我们可以这样使用`<Title />`组件：

```js
/* Home.re */
let component = ReasonReact.statelessComponent("Home");

let make = _children => {
  ...component,
  render: _self =>
    <StyledComponents.Title center=true underline=true>
 {ReasonReact.string("Page1")}
 </StyledComponents.Title>,
};
```

上一段代码是一个样式化的 ReasonReact 组件，它使用 CSS 渲染了一个`h1`元素。该 CSS 之前在`StyledComponents.Title`模块中定义过。《Title />》组件有两个属性——center 和 underline，它们都默认为`false`。

当然，这并不是编写样式化组件的优雅方式，但它在功能上与 JavaScript 版本相似。另一个选择是回到原始 JavaScript，以利用熟悉的标记模板字面量语法。让我们在`Title.re`中展示这个例子。

```js
/* Title.re */
%bs.raw
{|const styled = require("styled-components").default|};

let title = [%bs.raw
  {|
     styled.h1`
       text-align: ${props => props.center ? "center" : "left"};
       text-decoration: ${props => props.underline ? "underline" : "none"};
       color: white;
       background-color: coral;
     `
   |}
];

[@bs.deriving abstract]
type jsProps = {
  center: bool,
  underline: bool,
};

let make = (~center=false, ~underline=false, children) =>
  ReasonReact.wrapJsForReason(
    ~reactClass=title,
    ~props=jsProps(~center, ~underline),
    children,
  );
```

使用方法类似，但现在`<Title />`组件不再是`StyledComponents`的子模块。

```js
/* Home.re */
let component = ReasonReact.statelessComponent("Home");

let make = _children => {
  ...component,
  render: _self =>
    <Title center=true underline=true> {ReasonReact.string("Page1")} </Title>,
};
```

个人来说，我喜欢使用`[%bs.raw]`版本的开发者体验。我想对 Adam Coll (`@acoll1`)表示衷心的感谢，因为他想出了`styled-components`绑定的两个版本。我也非常期待看到社区会提出什么。

让我们探索社区最受欢迎的 CSS-in-JS 解决方案：`bs-css`。

# 使用 bs-css

虽然 Reason 团队没有对 CSS-in-JS 解决方案提出官方推荐，但许多人目前都在使用一个名为`bs-css`的库，该库封装了 emotion CSS-in-JS 库（版本 9）。`bs-css`库为 Reason 提供了类型安全的 API。使用这种方法，我们可以让编译器检查我们的 CSS。我们将通过将我们的`App.scss`转换为`App.scss`，这是我们在第三章中创建的，*创建 ReasonReact 组件*，来了解这个库。

要跟上，请克隆这本书的 GitHub 仓库，并从`Chapter06/app-start`开始，使用以下代码：

```js
git clone https://github.com/PacktPublishing/ReasonML-Quick-Start-Guide.git
cd ReasonML-Quick-Start-Guide
cd Chapter06/app-start
npm install
```

要开始使用`bs-css`，我们将将其作为依赖项包含在`package.json`和`bsconfig.json`中，如下所示：

```js
/* bsconfig.json */
...
"bs-dependencies": ["reason-react", "bs-css"],
...
```

在通过 npm 安装`bs-css`并配置`bsconfig.json`之后，我们将能够访问库提供的`Css`模块。定义自己的子模块`Styles`是标准做法，我们在其中打开`Css`模块，并将所有的 CSS-in-Reason 写在那里。由于我们将转换`App.scss`，我们将在`App.re`中声明一个`Styles`子模块，如下所示：

```js
/* App.re */

...
let component = ReasonReact.reducerComponent("App");

module Styles = {
  open Css;
};
...
```

现在，让我们将以下 Sass 转换为：

```js
.App {
  min-height: 100vh;

  &:after {
    content: "";
    transition: opacity 450ms cubic-bezier(0.23, 1, 0.32, 1),
      transform 0ms cubic-bezier(0.23, 1, 0.32, 1) 450ms;
    position: fixed;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    background-color: rgba(0, 0, 0, 0.33);
    transform: translateX(-100%);
    opacity: 0;
    z-index: 1;
  }

  &.overlay {
    &:after {
      transition: opacity 450ms cubic-bezier(0.23, 1, 0.32, 1);
      transform: translateX(0%);
      opacity: 1;
    }
  }
}
```

在`Styles`内部，我们声明了一个名为`app`的绑定，它将在`<App />`组件的`className`属性中使用。我们将绑定到名为`style`的`bs-css`函数的结果。`style`函数接受一个 CSS 规则列表。让我们通过以下代码来探索其语法：

```js
module Styles = {
  open Css;

  let app = style([
    minHeight(vh(100.)),
  ]);
};
```

起初可能有点奇怪，但用得越多，感觉越好。所有 CSS 属性和所有单位都是函数。函数有类型。如果类型不匹配，编译器会报错。考虑以下无效的 CSS：

```js
min-height: red;
```

这在 CSS、Sass 甚至`styled-components`中都是静默失败的。使用`bs-css`，我们至少可以防止很多无效的 CSS。编译器还会通知我们任何未使用的绑定，这可以帮助我们维护 CSS 样式表，并且，像往常一样，我们有完整的 IntelliSense，这有助于我们在使用过程中学习 API。

个人来说，我是 Sass 嵌套 CSS 的大粉丝，我很高兴我们也可以用`bs-css`做到这一点。为了嵌套`:after`伪选择器，我们使用`after`函数。为了嵌套`.overlay`选择器，我们使用`selector`函数。就像在 Sass 中一样，我们使用`&`符号来引用父元素，如下所示：

```js
module Styles = {
  open Css;

  let app =
    style([
      minHeight(vh(100.)),

      after([
 contentRule(""),
 transitions([
 `transition("opacity 450ms cubic-bezier(0.23, 1, 0.32, 1)"),
 `transition("transform 0ms cubic-bezier(0.23, 1, 0.32, 1) 450ms"),
 ]),
        position(fixed),
        top(zero),
        right(zero),
        bottom(zero),
        left(zero),
        backgroundColor(rgba(0, 0, 0, 0.33)),
        transform(translateX(pct(-100.))),
        opacity(0.),
        zIndex(1),
      ]),

      selector(
        "&.overlay",
        [ 
          after([
            `transition("opacity 450ms cubic-bezier(0.23, 1, 0.32, 1)"),
            transform(translateX(zero))),
            opacity(1.),
          ]),
        ],
      )
    ]);
};
```

注意我们是如何使用多态变体`transition`来表示过渡字符串的。否则，过渡是不有效的。

你可以在 GitHub 仓库的`Chapter06/app-end/src/App.re`文件中找到其余的转换。现在我们只剩下将样式应用到`<App />`组件的`className`属性上了，如下所示：

```js
/* App.re */
...
render: self =>
  <div
    className={"App " ++ Styles.app ++ (self.state.isOpen ? " overlay" : "")}
...
```

删除`App.scss`后，一切看起来几乎都一样。太棒了！例外的是`nav > ul > li:after`选择器。在之前的章节中，我们使用内容属性来渲染图像，如下所示：

```js
content: url(./img/icon/chevron.svg);
```

根据 `Css.rei`，`contentRule` 函数接受一个字符串。因此，使用 `url` 函数不会进行类型检查，如下面的代码所示：

```js
contentRule(url("./img/icon/chevron.svg")) /* type error */
```

作为一条逃生路线，`bs-css` 提供了 `unsafe` 函数（如下面的代码所示），这将绕过这个问题：

```js
unsafe("content", "url('./img/icon/chevron.svg')")
```

然而，尽管我们之前的 webpack 配置会将前面的图像作为依赖项拉入，但在使用 `bs-css` 时，它不再这样做。

# 权衡

在 Reason 中使用 CSS-in-JS 显然是一个权衡。一方面，我们可以获得类型安全的、局部作用域的 CSS，并且可以将我们的 CSS 与组件一起定位。另一方面，语法稍微有点冗长，可能会有一些奇怪的边缘情况。选择 Sass 而不是 CSS-in-JS 解决方案是完全合理的，因为在这里没有明显的胜者。

# 其他库

我鼓励您尝试其他 CSS-in-JS Reason 库。并且无论您在寻找 Reason 库时，您的第一个目的地应该是 Redex（**Re**ason **P**ackage **I**ndex**）。

您可以在以下位置找到 Redex（**Re**ason **P**ackage **I**ndex**）：

[`redex.github.io/`](https://redex.github.io/)

另一个有用的资源是 Reason Discord 频道。这是一个询问各种 CSS-in-JS 解决方案及其权衡的好地方。

您可以在以下位置找到 Reason Discord 频道：

[`discord.gg/reasonml`](https://discord.gg/reasonml)

# 摘要

CSS-in-JS 仍然相对较新，在 Reason 社区中未来将会有很多关于它的实验。在本章中，我们了解了一些 CSS-in-JS（在 Reason 中）的好处和挑战。您站在哪一边？

在 第七章，“Reason 中的 JSON”，我们将学习如何在 Reason 中处理 JSON，并看看 GraphQL 如何帮助减少样板代码同时实现一些相当吸引人的保证。
