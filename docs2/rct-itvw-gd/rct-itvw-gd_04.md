# 4

# 处理路由和国际化

在软件开发的广阔世界中导航并非没有困难。我们的旅程经常需要熟练地导航各种屏幕和语言设置，以确保提供卓越的用户体验。本章深入探讨了路由和国际化以及任何现代 React 应用程序的虚拟环境和视野。

本章将带我们深入探讨屏幕导航，并介绍 React Router。它是网络应用导航中最重要的工具，因为它在用户在不刷新页面的情况下穿越您的应用时刷新浏览器 URL。我们将介绍许多类型的路由，如基本路由和嵌套路由，学习如何将路由添加到我们的应用中，甚至深入到访问 URL 参数的世界。

但我们的冒险并未结束。随着我们进入国际化与本地化的领域，我们将超越功能性的困难，并在全球范围内规划我们的路线。这些发展方面展示了我们对多样性的承诺，确保我们的应用能够使用用户所在地的语言进行交流。

我们将详细介绍如何将翻译和格式化消息添加到我们的程序中，从而提高其面向全球受众的可用性。我们还将学习如何使用占位符并向这些消息传递参数，以实现动态翻译。将本章视为您通往更用户友好和全球化的应用的路线图。我们将利用这些知识来引导我们穿越软件开发领域，避免任何障碍，并提供无瑕疵的用户体验。在接下来的几节中，准备开始这场迷人的冒险。

在接下来的章节中，我们将涵盖以下主题，以了解如何处理路由以及国际化是如何工作的：

+   导航屏幕和 React Router 简介

+   路由、路由类型和链接

+   添加路由

+   访问 URL 参数

+   嵌套路由

+   介绍国际化与本地化

+   添加翻译和格式化消息

+   传递参数和占位符

# 技术要求

请确保您已在您的机器上设置并安装了 JavaScript 构建工具 **Vite.js**。您可以在以下链接找到它：[`vitejs.dev/`](https://vitejs.dev/)。我们将使用它来完成我们的下一个 React 项目。

此外，熟悉一下 React Router 库：[`reactrouter.com/en/main`](https://reactrouter.com/en/main)。

我们现在准备开始。下一节将向我们介绍 React Router。让我们行动起来吧！

# 导航屏幕和 React Router 简介

理解导航和 React Router 库对于任何程序员来说都是至关重要的。在本节中，我们将探讨使用 React Router 遍历屏幕的基本原理以及为什么它至关重要。本节的目标是概述 React Router，并简单描述通过各种 Web 应用程序进行导航的方式，而不使内容过于技术化。我们将通过提供有用的建议来缩小雇主期望与你的现有技能水平之间的差距，这些建议有助于在利用 React Router 的同时提高工作流程效率。

现在，让我们探讨为什么我们应该首先使用 React Router 库，以及它能帮助我们什么。值得一提的是，Next.js 已经内置了路由功能，但这仍然是一项值得学习的知识。这是因为在不同 React 框架中，路由的方式不同，其核心工作原理仍然有效，并且可以在任何地方使用。

## React Router 库的目的是什么？

创建路由逻辑可能会很耗时且困难，这正是 React Router 发挥作用的地方。由于库的广泛功能，它可以极大地减轻我们的路由挑战。React Router 是一个开源的 Web 应用程序路由模块，允许你在不同的页面和组件之间进行导航。它提供了一个易于使用的界面，用于在 Web 项目中实现动态路由。它支持众多 URL，并让你完全控制你应用的路由，从而实现无缝且引人入胜的用户体验。

现在我们已经了解了使用这个库的目的，我们将在下一节学习导航是如何工作的。

## 在 React Router 中，屏幕之间的导航是如何工作的？

在屏幕之间导航一开始可能看起来很令人畏惧，但使用 React Router 可以使这个过程变得相当容易和实用。使用 React Router，你可以高效地管理你应用中的所有路由，使得在不同屏幕之间切换变得轻而易举。此外，它允许你保持你的 UI 与 URL 保持同步。所以，即使你不是专家，React Router 也能让你轻松地添加所需的功能，使你的应用运行顺畅。

React Router 启用了客户端路由，这基本上是路由启动的方式。本质上，计算机的浏览器从网站的服务器请求一个页面，服务器获取并确定 CSS 和 JavaScript 文件，并在网站上渲染从服务器本身提供的 HTML。当用户点击网站上的链接时，这个过程会为全新的页面重新启动。

Next.js 已经内置了路由解决方案，因此我们将使用另一个流行的 JavaScript 构建工具 Vite.js 来查看本章中的代码。以下是工具的链接：[`vitejs.dev/`](https://vitejs.dev/)。

第一步是构建一个`BrowserRouter`组件并配置主路由。这为我们的 Web 应用启用了客户端路由。我们的`main.jsx`文件作为起点，如下所示：

```js
import * as React from 'react';
import * as ReactDOM from 'react-dom/client';
import { createBrowserRouter, RouterProvider } from 'react-router-dom';
import './index.css';
const router = createBrowserRouter([
  {
    path: '/',
    element: <div>Hello world!</div>,
  },
]);
ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>
);
```

这段代码定义了我们的初始路由。在这种情况下，它将是我们的根路由。根路由是加载并显示在网站上的第一页，通常称为我们的主页。

在我们进入下一节之前，让我们快速了解一下`BrowserRouter`及其相关的子主题。首先，`BrowserRouter`是一个通过使用 HTML5 历史 API 来保持 UI 与 URL 同步的路由解决方案。这个 API 利用了诸如`popstate`、`replacestate`和`pushstate`等事件。我们可以使用`BrowserRouter`通过使用干净的 URL 和历史记录来存储地址栏中的当前位置。我们还可以用它来跟踪 iframe 中的 URL 变化。

React Router 有许多功能，你可以在其文档中找到。以下是提供的概述：

| **功能** | **描述** |
| --- | --- |
| 路由器 | 虽然你的应用可能只会使用一个路由器，但根据其运行设置，可以访问多个路由器。其中包含的有些是`createBrowserRouter`、`createMemoryRouter`、`createHashRouter`和`createStaticRouter`。 |
| 路由器组件 | 你将在你的应用中用于页面路由的路由器组件类型。 |
| 路由 | 用于创建和管理路由的方法。这些可以包括操作、懒加载和加载器。 |
| 组件 | 使用此功能，我们可以使用自定义组件来管理我们的数据。例如，我们可以使用`Form`组件，该组件模拟浏览器进行客户端路由和数据变更，或者使用`Await`组件进行自动错误处理。还有用于导航到其他页面的重要`Link`组件。这些都是我们可用的组件之一。 |
| 钩子 | 这些自定义钩子就像任何 React 钩子一样工作，并为我们提供新的功能。`useNavigation`、`useSearchParams`、`useOutlet`和`useLocation`都是具有不同目的的钩子。 |
| 获取工具 | 这些用于管理我们从 API 接收到的数据。我们可以获取数据并执行重定向。 |
| 工具 | 我们可以使用工具执行不同的操作。例如，`matchPath`可以用来匹配路由路径模式并将其与 URL 路径名称进行比较，并返回匹配信息。 |

表 4.1：React Router 功能

这涵盖了 React Router 的大部分主要功能。要了解更多信息，请阅读官方文档：[`reactrouter.com/en/main`](https://reactrouter.com/en/main)。

在下一节中，我们将进一步探讨并学习更多关于路由和链接的内容。

# 路由、路由类型和链接

路由是 React Router 应用程序中最关键的组件。它们将 URL 段与组件链接，以及执行数据加载和数据修改。通过路由嵌套，复杂的项目布局和信息依赖变得简单，因为路由是提供给路由构建操作的对象，这使得整个过程变得更加容易。

## 我们可以使用哪些类型的路由？

React Router 给我们提供了访问各种路由形式的能力。以下表格解释了这一点：

| **路由类型** | **描述** |
| --- | --- |
| `path` | 路径模式将与 URL 进行比较，以查看此路由是否与 URL、链接 href 或表单操作匹配。 |
| `index` | 这决定了路由是否是索引路由。索引路由，如默认子路由，将在其父级的 URL 中渲染到其父级的 `Outlet`。 |
| `children` | 嵌套路由允许你在单个页面上渲染多个组件，同时保持路由完整性。 |
| `caseSensitive` | 这指定了路由是否应该匹配大小写。 |
| `loader` | 在路由渲染之前，路由加载器被调用，并通过 `useLoaderData` 为元素提供数据。 |
| `action` | 当一个表单、fetcher 或提交将提交发送到路由时，路由动作会被调用。 |

表 4.2：路由类型

现在我们已经了解了不同类型的路由，是时候学习创建路由和链接的代码了。

## 如何创建路由和链接？

我们可以通过使用 React `Element` 和 React `Component` 来创建路由。

使用 `element` 的典型语法如下：

```js
<Route path="/about" element={<About />} />
```

如果你想使用 `Component` 代替，那么代码将如下所示：

```js
<Route path="/about" Component={About} />
```

链接的工作方式略有不同。客户端路由允许我们的应用程序在点击链接后调整 URL，而不是从服务器请求另一个文档。相反，应用程序可以迅速显示新的 UI，并使用 `fetch` 执行数据调用以更新页面内容。由于网络浏览器不需要请求全新的页面或重新访问下一页的 CSS 和 JavaScript 内容，这导致加载时间更快。它还允许增强用户交互，如滚动。

以下示例展示了如何使用链接进行页面导航：

```js
import { createRoot } from 'react-dom/client';
import { createBrowserRouter, RouterProvider, Link } from
  'react-router-dom';
const router = createBrowserRouter([
  {
    path: '/',
    element: (
      <div>
        <h1>Hello World</h1>
        <Link to="about">About Us</Link>
      </div>
    ),
  },
  {
    path: 'about',
    element: <div>About</div>,
  },
]);
createRoot(document.getElementById('root')).render(
  <RouterProvider router={router} />
);
```

这段代码块展示了如何创建一个包含链接的首页，该链接可以导航到关于我们页面。

现在我们已经了解了路由和链接，让我们学习如何添加路由。

# 添加路由

如果我们使用 `<Routes>` 组件，路由可以在我们的应用程序中渲染，该组件与我们的文件中的其他子路由相匹配。路由搜索其所有子路由以找到最佳匹配，如果位置发生变化，则渲染该 UI 分支。为了表示嵌套 UI，这也对应于嵌套 URL 路径，`<Route>` 组件也可以嵌套。通过渲染 `<Outlet>`，父路由渲染其子路由。

以下代码示例说明了如何向文件中添加路由：

```js
<Routes>
  <Route path="/" element={<Menu />}>
    <Route
      path="messages"
      element={<MenuItems />}
    />
    <Route path="actions" element={<MenuActions />} />
  </Route>
  <Route path="about" element={<About />} />
</Routes>
```

在此文件中，有四个路由：

+   `"/"`

+   `"/messages"`

+   `"/actions"`

+   `"/about"`

消息和操作的路线是主路线`"/"`下的嵌套路线。`"/about"`路线是一个顶级路线，因为它独立且不像前两个那样嵌套。当页面位于其定义的路由时，属性元素内的组件将加载。路线也可以使用 JSX 和`createRoutesFromElements`声明。

基本知识已经介绍完毕。现在，让我们继续下一个主题，即访问 URL 参数。这将教会我们如何导航到由其 ID 确定的页面。这为我们进行`GET`请求提供了更多的定制选项，这是在我们学会使用基本路由导航到页面之后的下一步。

# 访问 URL 参数

我们可以使用 React 中的`useParams`钩子，它提供了一个与指定路由匹配的当前 URL 动态参数的关键/值对象。所有参数都从父路由继承到子路由。一个工作示例如下：

```js
import * as React from 'react';
import { Routes, Route, useParams } from 'react-router-dom';
function ProfilePage() {
  // Get the userId param from the URL.
  let { userId } = useParams();
  // ...
}
function App() {
  return (
    <Routes>
      <Route path="users">
        <Route path=":userId" element={<ProfilePage />} />
        <Route path="me" element={...} />
      </Route>
    </Routes>
  );
}
```

因此，使用这种路由配置，应用程序可以根据 URL 的结构渲染各种组件。首先，`users`/`userId`的页面路由渲染`ProfilePage`组件，并将`userId`部分作为`userId`提供给组件。`users`/`me`的路由是渲染元素属性中提供的组件的路由。

如您所见，URL 参数功能强大，为我们提供了对路由的另一个层次的定制。在下一节中，我们将探讨嵌套路由，这是我们现在已经学会了如何创建基本路由后的自然发展。有了嵌套路由，我们将在同一页面上渲染多个组件。

# 嵌套路由

在 2014 年，React Router 中的嵌套路由受到了 Ember.js 路由机制的影响。Ember.js 团队发现，URL 的某些部分通常决定了渲染页面布局的方法以及数据如何与渲染的布局连接。在我们的示例中可以看到创建具有嵌套元素的页面的一个方法：

```js
createBrowserRouter(
    createRoutesFromElements(
      <Route path="/" element={<Root />}>
        <Route path="connect" element={<ConnectPage />} />
        <Route
          path="admin"
          element={<Admin />}
          loader={({ request }) =>
            fetch("/data/api/admnin.json", {
              signal: request.signal,
            })
          }
        />
        <Route element={<AuthLayout />}>
          <Route
            path="login"
            element={<Login />}
            loader={redirectIfUser}
          />
          <Route path="logout" action={logoutUser} />
        </Route>
      </Route>
    )
  );
```

此代码块用于用户认证的登录流程。如果用户已登录，则加载管理页面。还有一个登录和登出路由。现在，让我们看看动态路由，这是另一个有用的功能。

# 动态路由

当开发具有多个页面或视图的应用程序时，这些页面或视图具有基本结构但信息或行为不同，动态路由变得方便。与在应用程序中建立预定的路由数量相反，动态路由允许您根据应用程序的整体当前状态现场构建路由。

我们可以在这里看到它的样子：

```js
import { BrowserRouter, Route } from 'react-router-dom';
function App() {
  return (
    <BrowserRouter>
      <Route path="/users/:id" component={Profile} />
    </BrowserRouter>
  );
}
```

路由结构中的`:id`属性表示一个动态值，它可能根据用户提供的输入而变化。当 URL 符合此模式时，React Router 会从 URL 中获取 `id` 参数并将其提供给 `Profile` 组件。接下来，我们将查看错误页面，因为当用户遇到损坏的页面时，这是我们需要了解的情况。

# 错误页面

为了解决用户访问不存在路由或在使用我们的应用程序时遇到困难的情况，我们可以在 React Router 中创建错误页面或*找不到*页面。当出现问题时，这有助于提供更好的用户体验，防止用户遇到空白页面或不一致的布局。

以下代码示例展示了如何创建错误页面。

首先，我们必须创建必要的组件：

```js
import React from 'react';
const NotFound = () => {
  return <div>404 – The page was not found</div>;
};
const ErrorPage = () => {
  return <div>An error occurred. :(</div>;
};
export { NotFound, ErrorPage };
```

此代码创建了两个组件 - 一个用于`404`错误页面，另一个用于通用错误页面。

现在，让我们设置路由：

```js
import React from 'react';
import { BrowserRouter as Router, Route, Switch } from
  'react-router-dom';
import { NotFound, ErrorPage } from './ErrorComponents';
import Home from './Home';
function App() {
  return (
    <Router>
      <Switch>
        <Route exact path="/" component={Home} />
        <Route path="/error" component={ErrorPage} />
        <Route component={NotFound} />
      </Switch>
    </Router>
  );
}
export default App;
```

此代码创建了一个包含我们应用程序所有页面路由的组件。

就这样，我们已经考虑了错误页面并学习了如何设置一些路由。

接下来，我们将介绍国际化和本地化。了解如何根据不同地区调整您的应用程序非常重要，因为我们所有人生活在不同国家。所以，让我们直接进入正题。

# 国际化和本地化

国际化和本地化是软件开发中的基本实践，使您能够设计和部署可以针对多种语言和地区定制的系统。让我们学习它们之间的区别。

## 国际化是什么？

国际化是将您的应用程序创建和准备成可以在多种语言中使用的进程。这通常涉及将应用程序的所有字符串提取到可以翻译成多种语言的单独文件中。它还需要对您的软件进行结构化，以确保它可以正确管理和显示这些翻译。

## 本地化是什么？

这涉及到将您的本地优化应用程序翻译成特定的本地语言。翻译应用程序的文本只是本地化的一部分。它可能还包括其他区域特有的元素，例如文本方向、数字形式以及日期和时间格式等。

React Router 允许您构建本地化路由。例如，为了管理语言选择，您可能为各种语言（如`"/en/about"`和`"/fr/about"`）设置多个路由，或者您可以使用上下文或状态。

我们已经学到了很多，我们的知识已经大大增加。接下来，我们将进入本章的倒数第二节，我们将学习如何在我们的 React 应用程序中添加翻译和格式化消息。我们刚刚学习了国际化本地化，这是我们为不同语言准备应用程序的地方。现在，让我们学习如何在编写的代码中实现不同的语言。

# 添加翻译和格式化消息

将应用程序中的文本内容从一种语言翻译成另一种语言，并按照区域习俗和标准进行翻译的过程被称为翻译和格式化消息。我们可以利用像**FormatJS**这样的库将翻译和格式化消息添加到 React 应用程序中。React Router 默认不允许翻译或本地化；然而，它可以很容易地与 FormatJS（或类似包）一起使用来构建国际化路由系统。

让我们看看一个使用该库的代码示例，看看它可能是什么样子：

```js
import { IntlProvider, FormattedMessage } from
  'react-intl';
import English from './translations/en.json';
import French from './translations/fr.json';
const Home = () => (
  <div>
    <h2>
      <FormattedMessage id="home.title" />
    </h2>
    <p>
      <FormattedMessage id="home.welcome" />
    </p>
  </div>
);
// We can assume that we are able to get the user's preferred language from somewhere like in user or browser settings...
const userLanguage = 'fr';
// This value can be dynamically created.
const messages = {
  en: English,
  fr: French,
};
const App = () => (
  <IntlProvider locale={userLanguage} messages=
    {messages[userLanguage]}>
    <Home />
  </IntlProvider>
);
export default App;
```

在这个例子中，应用程序已经设置了英语和法语翻译。默认语言是硬编码为法语；然而，在实际应用中，它可以根据用户在浏览器中设置的某种语言设置动态生成。`en`和`fr`语言代码映射到由`messages`对象导入的翻译文件。

翻译和格式化消息——它们究竟是什么？我们马上就会找到答案。所以，继续阅读。

## 翻译是什么？

这通常指的是在程序中将文本从一种语言翻译成另一种语言。在软件中，我们通常保留许多包含每种支持语言翻译文本的语言文件（通常在 JSON 或类似格式中）。这使得应用程序能够根据用户的偏好或区域设置动态显示适当的语言。

## 格式化消息是什么？

许多应用程序需要在翻译的字符串中显示动态内容，而不仅仅是直接的翻译。格式化通信在这方面发挥作用。使用格式化消息，您可以管理复数规则，向您的翻译字符串添加变量，使用区域标准格式化日期和数字，等等。

通过结合翻译和格式化消息，我们可以开发出易于适应各种语言和位置的应用程序，从而提高可访问性和用户体验。

我们的进展非常出色——只剩下最后一部分，我们就会完成这一章。最后一部分将介绍传递参数和占位符。到目前为止，我们已经学会了如何向单页应用添加数据；然而，在实际应用中，我们在单页应用中有多条路由。因此，在下一节中，我们将学习如何传递参数和占位符，以便在我们的应用程序中实现动态路由。

# 传递参数和占位符

React 和 JavaScript（一般而言），以及 React Router（具体而言），支持传递占位符和参数。然而，对于动态路由和数据在路由之间的传输，当与 React Router 结合使用时，这些方法工作得很好。

## 我们如何传递参数？

通过使用多种技术，例如 URL 参数、查询参数或 history prop 的状态对象，我们可以将数据发送到由 React Router 显示的组件。通常，这是为了将精确的数据从一个路由传递到另一个路由。

这个示例展示了当我们使用 history prop 的状态对象时传递参数的过程：

```js
import { Link } from "react-router-dom";
<Link
  to={{
    pathname: "/route",
    state: { myData: "Hello, World!" }
  }}
>
  My Link
</Link>
```

接收组件揭示了我们可以如何访问我们传递的数据：

```js
import { useLocation } from "react-router-dom";
function MyComponent() {
  const location = useLocation();
  console.log(location.state.myData);
  // Outputs: "Hello, World!"
  // ...
}
```

最后，我们将学习如何使用占位符（URL 参数）。

## 我们如何使用占位符？

URL 参数，这些参数是 URL 的一部分，可以根据您希望显示的内容而变化，但仍然渲染相同的核心组件，由 React Router 支持。这些通常用于开发动态路由。

这里是一个如何在组件中创建路由并利用 URL 参数的示例：

```js
import { Route, useParams } from "react-router-dom";
function MyComponent() {
  let { id } = useParams();
  return <h2>My id is: {id}</h2>;
}
function App() {
  return (
    <Route path="/post/:id">
      <MyComponent />
    </Route>
  );
}
```

在这个示例中，`id`占位符可以依赖于任何值。当你访问`"/post/123"`时，`MyComponent`组件会渲染，并且`useParams()`返回一个包含`{ id: "123"}`的对象。

使用 React Router 构建动态和响应式应用需要参数和占位符。

# 概述

随着本章的结束，很明显，在 React 应用的路由和国际化环境中进行探索之旅既困难又令人满意。我们考察、探究并揭开了几个关键主题的复杂性，每个主题都对开发全面、交互式和国际化的应用做出了贡献。

我们从学习 React Router 开始，它是我们应用显示的可靠导航器。我们研究了路由，了解了它们的许多类型，并学习了如何在我们的应用中有效地使用它们。我们对获取 URL 参数和分层路由的研究扩大了我们的专业知识，使我们能够在应用内部设计复杂和精细的路径。

然后，我们将焦点转向国际化本地化，拓宽视野以确保世界各地的人们都能与我们的应用互动。我们认识到打破语言障碍的重要性及其对用户体验的巨大影响。学习如何添加翻译和格式化消息使我们能够用用户的母语与他们建立联系，使我们的应用成为全球性的实体。我们还通过学习如何使用占位符和将参数传递给消息来发现创建动态和响应式翻译的强大功能。

这些能力帮助我们成为高效的开发者，能够创建不仅有用而且无处不在的程序。这条道路为我们提供了设计和构建不仅响应性强、弹性好，而且全球化和包容性的在线应用程序所需的工具。随着我们完成这一章，思考一下你学到了什么，但请记住，这仅仅是你的更大旅程中的一个站点。继续探索、学习和，最重要的是，继续你的成长，追随与你共鸣最多的道路。

在下一章中，我们将学习更多高级的 ReactJS 概念，例如错误边框、端口、高阶组件、并发渲染和转发引用。因此，让我们为新的冒险做好准备，随着我们知识的增长。
