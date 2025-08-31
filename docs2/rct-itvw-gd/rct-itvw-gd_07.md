# 7

# 在 ReactJS 中应用 CSS 的不同方法

在现代 Web 开发中，创建美观且用户友好的界面对于建立引人入胜和有效的应用程序至关重要。ReactJS 是一个流行的前端框架，用于创建用户界面，并且有几种实现**层叠样式表**（**CSS**）的方法，这是负责在线内容样式的语言。本章试图回答面试者可能对 CSS 主题提出的一些重要问题。通过解释将 CSS 纳入 ReactJS 的各种方法，我们将能够从更广泛的知识体系中受益，这将使我们更好地应对有关此主题的面试问题。

我们将探讨实现 CSS 的五种不同方法：`CSS Modules`、`styled-components`和**原子 CSS**（使用 Tailwind CSS 框架）。这些解决方案各有优缺点，具体取决于项目目标和偏好。通过研究这些选项，您将获得在面试环境中应对这些问题的知识和信心，这在您需要创建 ReactJS 应用程序时尤其有用。通过了解编写和维持干净、可管理和可扩展代码的原则，您会发现面试中提出高质量答案要简单得多。

Sass 和 Less 等预处理器也将在本章中介绍，因为我们旨在涵盖将 CSS 实现到我们的 React 项目中所有相关的用例，并准备好回答这些领域的任何面试问题。

在本章中，我们将详细探讨这些重要的 CSS 相关主题：

+   应用 CSS 的不同方式

+   探索处理器和 CSS Modules

+   CSS-in-JS 方法以及`styled-components`及其用法

+   如何在 React 应用程序中使用 styled components

# 技术要求

确保您的计算机上已安装`Node`和`npm`，并且已安装并正常工作用于 Create React App 和 Next.js 的 JavaScript Node 包。使用您喜欢的 IDE 和**命令行界面**（**CLI**）来处理这些项目。

Create React App 的包可以在以下位置找到：[`create-react-app.dev/`](https://create-react-app.dev/)。

Next.js 的包可以在以下位置找到：[`nextjs.org/`](https://nextjs.org/)。

# 应用 CSS 的不同方式

在本节中，我们将探讨在 React 项目中应用 CSS 的不同方式。获得的知识将为我们提供应对这些常见问题的关键面试答案，并且示例可以帮助我们详细解释它们之间的差异以及它们是如何工作的。让我们继续学习，更深入地了解这些 CSS 解决方案。

虽然 ReactJS 是一个用于创建用户界面的 JavaScript 库，CSS 是一种用于描述 HTML 或 XML 文档外观和格式的样式表语言。将 CSS 与 ReactJS 结合使用可以帮助开发者高效地设置组件样式，从而实现美观且一致的界面。使用 CSS 与 ReactJS 有几种方法，我们将在本章中学习。

在接下来的章节中，我们将学习导入 CSS、CSS Modules、CSS 预处理器、Atomic CSS 和内联样式。后者涉及使用 JavaScript 对象直接将样式添加到 React 组件中。虽然内联样式对于小型组件或动态样式很有用，但它们可能导致代码重复和维护性问题，我们将在后面讨论。首先，让我们从导入样式表开始。

## 我们如何导入外部样式表？

在 React 中利用 CSS 的标准技术涉及创建单独的 CSS 文件，并使用类名来设置组件样式。这种解决方案将样式和逻辑关注点分开，使代码更加结构化和易于管理。官方 React 文档建议开发者在开始新的 React 项目时使用生产级别的 React 框架。这包括 Next.js、Remix、Gatsby 和 Expo（用于原生应用）。现在，这被认为是开发 React 应用程序最现代的方式，你可以在这里了解更多：[`react.dev/learn/start-a-new-react-project`](https://react.dev/learn/start-a-new-react-project)。

我们将查看两个代码示例，一个使用 Next.js，另一个使用 Create React App，以展示两种（旧与新的）构建 React 应用程序的过程之间的对比。Next.js 被认为是构建 ReactJS 应用程序最现代的推荐方式，而 Create React App 现在被视为一个遗留工具。这是因为 Next.js 被视为一个更适用于生产级别的 ReactJS 框架。

### 我们如何使用 Create React App 构建 React 应用程序？

这是如何在 Create React App 中实现传统方法的示例。

首先，创建一个 React 项目，然后创建一个 CSS 文件。使用 CSS 规则和类名，在名为 `App.css` 的单独 CSS 文件中指定你的样式，如下所示：

```js
/* App.css */
.container {
  text-align: center;
  margin: 0 auto;
  background-color: #bada55;
  padding: 1rem;
}
.title {
  font-size: 2rem;
  font-weight: bold;
}
```

现在，将创建的 CSS 文件导入到你的 React 组件文件中，该文件应该是 `App.js`：

```js
// App.js
import './App.css';
export default function App() {
  return (
      <div className="container">
        <h1 className="title">Hello, World!</h1>
      </div>
  );
}
```

注意

`className` 属性用于 JSX 元素中应用导入样式表中的相应 CSS 类。我们使用 `className` 属性而不是 `class`，因为 `class` 是 JavaScript 中的一个保留字。在编写 CSS 文件时这并不是问题，但在 JavaScript 文件中就是了。此外，JSX 是一种命名约定，这意味着它需要使用 camelCase 命名约定来使用元素属性，如类名。

在你的控制台中启动 `npm run start` 命令，你的应用程序应该已经启动并运行。

### 我们如何使用 Next.js 构建 React 应用程序？

Next.js 是一个基于 ReactJS 的知名开源 Web 开发框架。它的目的是让开发者更容易创建服务器端渲染的 React 应用程序，使他们能够创建针对搜索引擎优化（SEO）和提供卓越用户体验的高性能 Web 应用程序。

Next.js 的项目结构略有不同，尽管导入 CSS 样式表的方式仍然相同。幸运的是，当使用 Next.js 中最新的 App Router 功能时，这个过程实际上非常相似。这就是我们在 Next.js 中导入 CSS 样式表的方法。

首先，使用 Next.js 创建一个 React 项目，然后在 `app` 文件夹内创建一个 `Home.css` 文件。使用以下所示的 CSS：

```js
/* Home.css */
.container {
  margin: 0 auto;
  display: flex;
  flex-flow: column nowrap;
  background-color: #0384c8;
  padding: 2rem;
}
.main-content {
  display: flex;
  flex-flow: row nowrap;
  padding: 2rem 0;
}
```

现在，只需将 `app` 文件夹中的 `page.js` 文件内的所有代码替换为这里提供的代码即可：

```js
import './Home.css';
export default function Home() {
  return (
      <div className="container">
        <h1>Heading 1</h1>
        <h2>Heading 2</h2>
        <h3>Heading 3</h3>
        <h4>Heading 4</h4>
        <h5>Heading 5</h5>
        <section className="main-content">
          <p>
            Lorem ipsum dolor sit amet, consectetur
            adipiscing elit. Nullam eu
            mi sit amet velit convallis tincidunt.
          </p>
          <p>
            Lorem ipsum dolor sit amet, consectetur
            adipiscing elit. Nullam eu mi sit
            amet velit convallis tincidunt.
          </p>
        </section>
      </div>
  );
}
```

使用 `npm run dev` 命令运行您的应用程序，它应该和之前一样工作。

这是我们从 Create React App 时代起就使用的默认导入样式表的方法。然而，它并不能实现组件级别的隔离，全局类名可能会导致命名冲突和不需要的样式覆盖。例如，CSS Modules 和 CSS-in-JS 框架解决了这些问题，并为装饰 React 组件提供了更广泛的功能。一些流行的 CSS 框架包括 Tailwind CSS、MUI、Chakra UI、Semantic UI、NextUI、React Bootstrap、Ant Design 和 Emotion。实际上，Tailwind CSS 是在您第一次配置 Next.js 应用程序时可以选择的一个选项。

还值得一提的是，在 CSS 网页布局模型中，构建网站结构最流行的两种方式是使用 Flexbox 或 CSS Grid。Flexbox 无疑更受欢迎，尽管根据网站设计和复杂度，通常会选择使用其中一种或两种。我们可以单独使用这些网页布局模型，或者与 CSS 框架一起使用。我们可能还希望研究的一个领域是动画。除了使用常规的 CSS 库创建动画外，我们还可以利用各种第三方库。一些流行的库包括 React Spring、Green Sock、Framer Motion、React Move 以及许多其他库。

我们现在将讨论另一种为我们的 ReactJS 应用程序添加样式的的方法，称为内联样式，这是在常规 HTML 和 ReactJS 应用程序中做样式的一种常见方式。它已经是一段时间内 HTML 的默认样式方法，并且使用 JSX 在 React 中也是可行的。

内联样式也提供了许多优势，这使得它成为为我们的 React 应用程序添加样式的一个非常有吸引力的选项。我们能够根据组件状态或属性使用动态样式，并且有组件隔离，这降低了意外样式覆盖或其他组件不兼容的可能性。更快的开发速度、易用性，以及我们甚至不需要 CSS 类名的事实，都增加了使用这种方法的好处。

## 我们如何使用内联样式？

在 ReactJS 应用程序中，内联样式允许开发者使用 JavaScript 对象直接对特定元素或组件应用样式，而不是在单独的样式表或类中指定 CSS 样式。它们被指定为包含键值对的字面量对象。基本上，它们是位于 JSX 花括号内的对象，看起来像这样`{{ backgroundColor: blue }}`。在 JSX 花括号内，我们将使用 CSS 属性及其值。对象的键是 CSS 属性名称，值是相关的属性值。

让我们来看一个例子，以便我们可以看到在实际代码中它看起来是什么样子。只需将`page.js`文件中的所有代码替换，将其转换为一个现在使用内联样式而不是外部样式的应用程序：

```js
const container = {
  display: 'flex',
  flexFlow: 'column nowrap',
  backgroundColor: '#7e7dd6',
  padding: '2rem',
};
const mainContent = {
  display: 'flex',
  flexFlow: 'row nowrap',
  padding: '2rem 0',
};
export default function Home() {
  return (
    <>
      <div style={container}>
        <h1>Heading 1</h1>
        <h2>Heading 2</h2>
        <h3>Heading 3</h3>
        <h4>Heading 4</h4>
        <h5>Heading 5</h5>
        <section style={mainContent}>
          <p>
            Lorem ipsum dolor sit amet, consectetur
            adipiscing elit. Nullam eu
            mi sit amet velit convallis tincidunt.
          </p>
          <p>
            Lorem ipsum dolor sit amet, consectetur
            adipiscing elit. Nullam eu
            mi sit amet velit convallis tincidunt.
          </p>
        </section>
      </div>
    </>
  );
}
```

当我们需要根据组件的状态或属性应用动态样式时，React 内联样式可能会有所帮助。例如，基于用户交互或其他事件，我们可能将样式对象声明为组件状态的属性，并动态地更改它。

我们还可以通过将其分配给状态来在内联样式中使用变量，这看起来是这样的：

```js
'use client';
import { useState } from 'react';
export default function Home() {
  const [h1color, setH1Color] = useState('blue');
    return (
  <div>
    <h1 style={{ color: h1color }}>Hello World</h1>
  </div>
);
}
```

然而，内联样式有一些缺点，包括无法在组件之间重用样式，对于大型系统来说，它们不如外部样式表有效，并且如果不小心使用，可能会影响可读性。

解决这些问题的方法之一是使用 CSS 模块。CSS 模块是一种为组件编写模块化、作用域化的 CSS 的方法。它有助于解决典型的 CSS 问题，如全局作用域和名称冲突。

在 React 中实现 CSS 的另一个好策略是使用`styled-components`。`styled-components`是一个知名的 CSS-in-JS 包，用于为 React 组件添加样式。它允许你使用标签模板字面量直接在你的 JavaScript 代码中编写 CSS。`styled-components`生成唯一的类名并将样式注入 DOM 中，将它们限制为单个组件。这种方法提高了开发者的体验和组件分离。

我们在项目中使用 CSS 的另一种方法是使用原子 CSS。原子 CSS，也称为函数式 CSS，是一种关注开发小型、单一用途 CSS 类的样式技术。每个类提供一种类型的规则或一组紧密相关的规则，并且它们通常以定义其目的或它们应用的属性的方式标记。 

这里的优势是开发速度快，因为通过简单地混合现有的原子类，你可以快速原型化和构建组件。遵循一个通用主题或模板，以确保每个开发者都使用相同的文档和类集。这使得调试非常简单，并且入职过程很快，因为每个人都使用相同的过程。

## 我们如何使用原子 CSS？

原子 CSS 是一种构建 CSS 代码的策略，强调使用简短、专业的类，这些类可以组合起来产生复杂的样式。目标是把设计分解成可管理的、可重用的部分，能够以多种方式合并以产生所需的设计。

原子 CSS 技术由许多知名的 CSS 库实现，包括 Tailwind CSS、Bootstrap CSS 和 Bulma 等。这些库提供了预定义的原子类集合，可以快速生成复杂的样式。我们现在将使用 Tailwind CSS 库在我们的 Next.js 应用中进行一些基本的样式设计，因为其在社区中的流行度以及 Tailwind CSS 集成到 Create Next App 的事实，而 Create Next App 是构建 Next.js 应用的官方框架。当你理解了基础知识时，你可以使用任何 CSS 库。

安装 Tailwind CSS 相对简单；你只需遵循这里的设置指南：[`tailwindcss.com/docs/guides/nextjs`](https://tailwindcss.com/docs/guides/nextjs)。

完成这些后，我们可以看到这个例子中的语法是什么样的：

```js
export default function Home() {
  return (
    <>
      <div class="flex flex-row">
        <div class="basis-1/4 bg-teal-600">01</div>
        <div class="basis-1/4 bg-teal-700">02</div>
        <div class="basis-1/2 bg-teal-800">03</div>
      </div>
    </>
  );
}
```

接下来，让我们了解预处理器和 CSS 模块。CSS 预处理器是一个程序，允许我们使用预处理器的语法来构建 CSS。在 CSS 模块中，每个类名和动画名现在，根据定义，都是本地分配的。为了提高效率和安全性，CSS 模块允许你在 CSS 文件中创建样式；然而，你需要将样式作为 JavaScript 对象来使用。

# 探索处理器和 CSS 模块

我们可以用两种不同的方式用 CSS 构建网站——通过使用 CSS 处理器和 CSS 模块。CSS 处理器已经存在很长时间了，并且被设计成是传统 CSS 的改进。它们让我们能够嵌套 CSS 代码，并将代码编译成常规 CSS。另一方面，CSS 模块为我们提供了文件中的作用域 CSS 代码，这有助于避免名称冲突。现在让我们了解它们，从 CSS 处理器开始。

## CSS 处理器是什么？

CSS 处理器，通常被称为 CSS 预处理器，是添加额外功能到 CSS 的工具，例如变量、混合和嵌套规则。它们使你能够以更少重复和模块化的方式编写，更容易维护。Sass（也称为 SCSS）、Less 和 Stylus 是最广泛使用的三种 CSS 预处理器。为了将改进的 CSS 语法转换为网络浏览器可以理解的常规 CSS，这些预处理器需要一个构建步骤。当使用 Webpack 等构建工具时，你可以将这个构建阶段纳入你的开发流程中。

## 我们如何使用 CSS 处理器？

Next.js 原生支持 Sass，利用 `.scss` 和 `.sass` 扩展名。通过 CSS 模块和 `.module.scss` 或 `.module.sass` 扩展名，你可以应用组件级别的 Sass。首先，使用 `npm install –save-dev sass` 命令安装 Sass。然后，在一个新的 `.scss` 文件中用 Sass 语法编写你的样式。通过在 React 组件文件中引用 `.scss` 文件来导入生成的 CSS，如下所示：

```js
import './styles.scss';
const MyComponent = () => {
  return <div className="myComponent">Hello, World!</div>;
};
export default MyComponent;
```

导入 `.scss` 文件与导入正常的 `.css` 文件完全相同。

## CSS 模块是什么？

在模块化方法中局部作用域 CSS 的方法是 CSS 模块。通过为每个组件自动创建独特的类名，它通过确保样式不会扩散到程序的其他区域来帮助防止全局样式之间的冲突。将 CSS 样式写入单独的文件，通常带有 `module.css` 扩展名，并将它们导入到 JavaScript 文件中是 CSS 模块的工作方式。导入的样式被处理为一个对象，其中产生的唯一类名作为值对，键作为主要类名。

## 我们如何使用 CSS 模块？

我们可以通过使用 CSS 模块在我们的组件中利用局部作用域的 CSS。使用 CSS 模块时，类名默认是局部作用域的，这可以防止任何命名冲突。这也是 Next.js 应用程序中使用的默认样式方法。我们可以在下面的代码片段中看到它的样子。

这是 `Home.module.css` 文件的 CSS：

```js
/* Home.module.css */
.main {
  display: flex;
  padding: 2rem;
  color: #ffffff;
}
.box {
  background-color: rgb(241, 255, 240);
  color: #000;
  padding: 1rem;
  margin: 1rem;
}
```

这是 `page.js` 文件的 JavaScript：

```js
// page.js
import styles from './Home.module.css';
export default function Home() {
  return (
    <>
      <div className={styles.main}>
        <h1>Hello World!</h1>
      </div>
      <div className={styles.box}>
        <p>
          Lorem ipsum dolor sit amet, consectetur
          adipiscing elit. Etiam convallis, nulla non
          laoreet condimentum, turpis felis finibus
          metus,ut molestie risus enim id neque. Integer
          tristique purus non gravida sodales. Maecenas
          ultricies feugiat dolor lobortis commodo. Sed
          maximus vitae neque quis mollis.
        </p>
      </div>
      <div className={styles.box}>
        <p>
          Lorem ipsum dolor sit amet, consectetur
          adipiscing elit. Etiam convallis, nulla non
          laoreet condimentum, turpis felis finibus
          metus,ut molestie risus enim id neque. Integer
          tristique purus non gravida sodales. Maecenas
          ultricies feugiat dolor lobortis commodo. Sed
          maximus vitae neque quis mollis.
        </p>
      </div>
    </>
  );
}
```

如你所见，它类似于使用内联样式；然而，我们仍然有一个外部样式表，所以这是两者的最佳结合。还有另一种实现 CSS 的方法，即使用 `styled-components` 和 CSS-in-JS 方法。这为我们提供了另一种设置项目的方式，并且与其他方法相比可以提供许多优势。现在让我们更深入地了解这种实现方式。

# CSS-in-JS 方法、样式组件及其用法

这是我们学习的一个基本领域，因为 CSS-in-JS 方法在整个 React 框架中适用。我们将了解这种方法，以及我们如何使用第三方库，如 `styled-components`，作为我们之前学到的其他 CSS 技术的替代方案。

## CSS-in-JS 是什么？

CSS-in-JS 是一种创新的 Web 开发样式解决方案，它将 CSS 集成到 JavaScript 代码中。这种方法不是使用单独的 CSS 文件，而是允许开发者在他们的 JavaScript 或 TypeScript 脚本中直接定义和监督组件的样式。CSS-in-JS 允许改进组件封装、作用域样式和更简单的动态样式。它还允许你在样式中使用 JavaScript 的全部功能，包括根据组件的状态动态应用样式或使用 JavaScript 变量计算样式值。

## 样式组件是什么，以及它们如何在 React 项目中使用？

在 React 的一个流行第三方工具 `styled-components` 的帮助下，程序员可以在 JavaScript 中指定组件样式，而不是外部 CSS 文件。它提供了一种针对特定组件的 CSS 代码的编写方法，简化了在整个应用程序中管理样式和重用样式的过程。

样式组件采用 CSS-in-JS 方法，这意味着 JavaScript 函数和变量被用来定义组件的 CSS 样式。这使得程序员能够通过利用 JavaScript 的所有功能，如函数、变量和其他语言结构，来创建动态样式。当使用 `styled-components` 时，我们有 **服务器端渲染**（**SSR**），这保证了我们的样式在服务器上得到适当的渲染。与内联样式等其他 CSS 相比，它有一个优势，因为它不需要额外的努力来确保良好的 SSR 支持。由于样式组件在大多数代码编辑器中包含语法高亮、代码检查和自动完成支持，开发者的体验也得到了进一步的提升。

这使得开发体验更加积极，并提高了生产力，因为它们还允许你将样式从组件的 JSX 中分离出来，从而产生更干净、更易于维护的代码。我们还可以使用样式组件为每个组件生成唯一的类名，确保样式被限制在适当的组件中，避免意外的样式泄漏或冲突。

我们获得的一个额外好处是通过使用 `styled-components` 的 React 上下文 API 来内置主题支持。这使得我们在整个应用程序中构建和管理统一主题变得简单，这是其他 CSS 技术所无法实现的。支持所有 CSS 功能，如伪选择器、媒体查询和关键帧，是一个巨大的优势。

# 如何在 React 应用程序中使用样式组件

为了加强这一学习，让我们通过一个示例来看看语法是什么样的。我们将快速查看一个简单、易于理解的简单设置，应该会使这一点非常清楚。

## 我们如何使用样式组件？

基本上，创建样式组件可以通过四个简单的步骤完成。首先，我们必须安装 `styled-components` 库的包，该包可以在以下链接找到：[`styled-components.com/`](https://styled-components.com/)。接下来，我们将包导入到文件的顶部。然后，我们为我们的 HTML 创建一个具有 CSS 样式的 JavaScript 类型的对象。我们使用 `styled` 方法后跟我们要使用的 HTML 元素，例如 `div`、`section` 或 `p` 标签等。

最后，我们在代码中返回对象以在屏幕上渲染它。以下代码片段展示了工作示例：

```js
import styled from 'styled-components';
const ContainerDiv = styled.div`
  color: blue;
  font-size: 30px;
`;
export default function Home() {
  return <ContainerDiv>Hello World!</ContainerDiv>;
}
```

我们成功地完成了这一部分，并学习了关于许多不同的 CSS 相关面试问题，这将使我们在这个主题领域在面试中处于有利位置。

# 摘要

我们探讨了在 ReactJS 应用程序中利用 CSS 的几种方法，强调了设计和样式在创建美观用户界面中的重要性。导入外部样式表、内联 CSS 样式、CSS 模块、`styled-components` 和像 Tailwind CSS 这样的原子 CSS 框架是探索的五种主要选项。

我们讨论了如何将外部 CSS 文件连接和导入到 React 组件中，从而实现集中管理和分离样式逻辑与组件逻辑的关注点。这种方法非常适合在 React 应用程序中使用经典 CSS。

我们还研究了原子 CSS 和其以实用工具为首要的方法，特别关注了流行的 Tailwind CSS 框架。通过提供一大套可用于构建定制设计的实用类，这种技术减少了自定义 CSS 的需求。

在 CSS 模块的主题上，我们探讨了 CSS 模块如何以模块化的方式处理特定组件的样式。CSS 模块通过使用本地作用域的类名来消除全局样式冲突，并鼓励组件的可重用性。我们还讨论了流行的 `styled-components` 包，它允许你使用标签模板字面量创建样式化组件。这种方法鼓励组件封装、主题支持和基于属性的动态样式。

通过了解并利用这些不同的 CSS 方法在你的 ReactJS 应用程序中，你可以轻松地设计和样式化你的应用程序组件，同时保持代码库的整洁、可管理和可扩展。

在下一章中，我们将学习如何测试和调试我们的 ReactJS 应用程序。
