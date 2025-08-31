# 第二章：*第二章*：探索不同的渲染策略

当谈到渲染策略时，我们指的是我们如何向网络浏览器提供网页（或网络应用程序）。有一些框架，如 Gatsby（如前一章所示），在提供静态生成的页面方面非常好。其他框架将使创建服务器端渲染的页面变得容易。

但 Next.js 将这些概念提升到了全新的水平，让你决定哪些页面应该在构建时渲染，哪些应该在运行时动态提供，为每个请求重新生成整个页面，使应用程序的某些部分变得极其动态。该框架还允许你决定哪些组件应该仅客户端渲染，使你的开发体验非常满意。

在本章中，我们将更深入地了解：

+   如何使用服务器端渲染为每个请求动态渲染页面

+   在客户端仅渲染某些组件的不同方式

+   在构建时生成静态页面

+   如何使用增量静态重新生成在生产中重新生成静态页面

# 技术要求

要运行本章中的代码示例，请确保你的机器上已安装 Node.js 和`npm`。作为替代方案，你可以使用在线 IDE，如[`repl.it`](https://repl.it)或[`codesandbox.io`](https://codesandbox.io)。

你可以在 GitHub 仓库中找到本章的代码：[`github.com/PacktPublishing/Real-World-Next.js`](https://github.com/PacktPublishing/Real-World-Next.js)。

# 服务器端渲染（SSR）

即使**服务器端渲染**（**SSR**）在开发者的词汇中听起来像是一个新术语，但实际上它是提供网页最常见的方式。如果你想到 PHP、Ruby 或 Python 这样的语言，它们都会在发送到浏览器之前在服务器上渲染 HTML，一旦所有 JavaScript 内容都加载完毕，这将使标记动态化。

嗯，Next.js 通过在每个请求上动态在服务器上渲染一个 HTML 页面，然后将其发送到网络浏览器来做同样的事情。该框架还会注入它自己的脚本，通过一个称为**激活**的过程使服务器端渲染的页面动态化。

想象你正在构建一个博客，你希望在单个页面上显示特定作者撰写的所有文章。这可以是一个很好的 SSR 用例：用户想要访问这个页面，所以服务器渲染它，并将生成的 HTML 发送到客户端。在这个时候，浏览器将下载页面请求的所有脚本，并使 DOM 活跃，使其在没有页面刷新或故障的情况下交互（你可以在[`reactjs.org/docs/react-dom.html#hydrate`](https://reactjs.org/docs/react-dom.html#hydrate)上了解更多关于 React hydration 的信息）。从这一点开始，多亏了 React hydration，Web 应用也可以成为一个**单页应用**（**SPA**），同时拥有**客户端渲染**（**CSR**）（我们将在下一节中看到）和 SSR 的所有优势。

谈到采用特定渲染策略的优势，SSR 相对于标准的 React CSR 提供了多个好处：

+   **更安全的 Web 应用**：在服务器端渲染页面意味着诸如管理 cookies、调用私有 API 和数据验证等活动都在服务器上发生，因此我们永远不会将私有数据暴露给客户端。

+   **更兼容的网站**：即使用户禁用了 JavaScript 或使用较旧的浏览器，网站也将可用。

+   **增强搜索引擎优化**：由于客户端将在服务器渲染并发送 HTML 内容后立即接收，搜索引擎蜘蛛（爬取网页的机器人）将不需要等待页面在客户端渲染。这将提高你的 Web 应用的 SEO 评分。

尽管有这些巨大的优势，但在某些情况下，SSR 可能不是你网站的最佳解决方案。事实上，使用 SSR，你需要将你的 Web 应用部署到服务器，该服务器将在需要时重新渲染页面。正如我们稍后看到的，使用 CSR 和**静态站点生成**（**SSG**），你可以免费（或以微小的成本）将静态 HTML 文件部署到任何云服务提供商，如 Vercel 或 Netlify；如果你已经使用自定义服务器部署你的 Web 应用，你必须记住，SSR 应用将始终导致更大的服务器工作负载和维护成本。

当你想要在服务器端渲染你的页面时，还有一点需要记住，那就是你为每个请求增加了一些延迟；你的页面可能需要调用一些外部 API 或数据源，并且它们会在每次页面渲染时调用。在服务器端渲染的页面之间导航总是会比在客户端渲染或静态服务页面之间导航慢一些。

当然，Next.js 提供了一些很棒的功能来提高导航性能，正如我们将在*第三章*，“Next.js 基础和内置组件”中看到的。

另一件需要考虑的事情是，默认情况下，Next.js 页面在构建时是静态生成的。如果我们想通过调用外部 API、数据库或其他数据源来使其更具动态性，我们需要从我们的页面导出特定的函数：

```js
function IndexPage() {
  return <div>This is the index page.</div>;
}
export default IndexPage;
```

如你所见，页面只打印了 `div`。它不需要调用外部 API 或其他任何数据源来工作，并且其内容对于每个请求都是相同的。但现在，让我们假设我们想在每个请求上问候用户；我们需要在服务器上调用一个 REST API 来获取一些特定的用户信息，并使用 Next.js 流将其结果传递给客户端。我们将通过使用保留的 `getServerSideProps` 函数来完成这项工作：

```js
export async function getServerSideProps() {
  const userRequest =     await fetch('https://example.com/api/user');
  const userData = await userRequest.json();
  return {
    props: {
      user: userData
    }
  };
}
function IndexPage(props) {
  return <div>Welcome, {props.user.name}!</div>;
}
export default IndexPage;
```

在前面的示例中，我们使用了 Next.js 保留的 `getServerSideProps` 函数，在每个请求的服务器端进行 REST API 调用。让我们将其分解成小步骤，以便我们更好地理解我们在做什么：

1.  我们首先导出一个名为 `getServerSideProps` 的异步函数。在构建阶段，Next.js 会查找每个导出此函数的页面，并使它们针对每个请求进行动态服务器端渲染。在这个函数作用域内编写的所有代码都将始终在服务器端执行。

1.  在 `getServerSideProps` 函数内部，我们返回一个包含名为 `props` 的属性的对象。这是必需的，因为 Next.js 会将这些属性注入到我们的 `page` 组件中，使它们在客户端和服务器端都可用。如果你想知道，当我们使用它时，我们不需要 polyfill fetch API，因为 Next.js 已经为我们做了这件事。

1.  然后，我们重构了 `IndexPage` 函数，现在它接受一个 `props` 参数，包含从 `getServerSideProps` 函数传递的所有属性。

就这样！在我们发布这段代码后，Next.js 将始终在服务器上动态渲染我们的 `IndexPage`，调用外部 API，并在我们更改数据源时立即显示不同的结果。

如本节开头所见，SSR 提供了一些显著的优势，但也存在一些注意事项。如果你想使用任何依赖于浏览器特定 API 的组件，你将需要显式地在浏览器上渲染它，因为默认情况下，Next.js 在服务器上渲染整个页面内容，这不会暴露某些 API，如 `window` 或 `document`。因此，出现了 CSR 的概念。

# 客户端渲染（CSR）

如前一章所见，一个标准的 React 应用在 JavaScript 包从服务器传输到客户端后才会渲染。

如果你熟悉 **create-react-app**（**CRA**），你可能已经注意到在网页渲染之前，整个网页都是完全白色的。这是因为服务器只提供了一个非常基本的 HTML 标记，其中包含所有必要的脚本和样式，使我们的网页动态化。让我们更仔细地看看 CRA 生成的那个 HTML：

```js
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <link rel="icon" href="%PUBLIC_URL%/favicon.ico" />
    <meta
      name="viewport"
      content="width=device-width, initial-scale=1"
    />
    <meta name="theme-color" content="#000000" />
    <meta
      name="description"
      content="Web site created using create-react-app"
    />
    <link rel="apple-touch-icon" 
      href="%PUBLIC_URL%/logo192.png" />
    <link rel="manifest" href="%PUBLIC_URL%/manifest.json" />
    <title>React App</title>
  </head>
  <body>
    <noscript>
      You need to enable JavaScript to run this app.
    </noscript>
    <div id="root"></div>
  </body>
</html>
```

正如你所见，我们只能在`body`标签内找到一个`div`：`<div id="root"></div>`。

在构建阶段，`create-react-app`会将编译后的 JavaScript 和 CSS 文件注入到这个 HTML 页面中，并使用`root` div 作为渲染整个应用的目标容器。

这意味着一旦我们将这个页面发布到任何托管提供商（Vercel、Netlify、Google Cloud、AWS 等），第一次调用所需的 URL 时，我们的浏览器将首先渲染前面的 HTML。然后，根据前面标记（在构建时由 CRA 注入）中的`script`和`link`标签，浏览器将渲染整个应用，使其可用于任何类型的交互。

CSR 的主要优势是：

+   **让你的应用感觉像原生应用**：下载整个 JavaScript 包意味着你已经在浏览器中下载了你的 Web 应用的每一页。如果你想导航到不同的页面，它会交换页面内容而不是从服务器下载新内容。你不需要刷新页面来更新其内容。

+   **页面过渡变得简单**：客户端导航允许我们在不重新加载浏览器窗口的情况下从一个页面切换到另一个页面。当你想轻松地展示页面间的酷炫过渡效果时，这非常有用，因为你没有任何重新加载可能会打断你的动画。

+   **懒加载和性能**：使用 CSR，浏览器将只渲染 Web 应用工作所需的最低 HTML 标记。如果你有一个在用户点击按钮后出现的模态框，其 HTML 标记不会出现在 HTML 页面上。它将在按钮点击事件发生时由 React 动态创建。

+   **减少服务器端工作量**：由于整个渲染阶段都委托给了浏览器，服务器只需要向客户端发送一个非常基本的 HTML 页面。因此，你不需要一个非常强大的服务器；实际上，有些情况下你可以在无服务器环境中托管你的 Web 应用，例如 AWS Lambda、Firebase 等。

但所有这些好处都是要付出代价的。正如我们之前看到的，服务器只发送一个空的 HTML 页面。如果用户的互联网连接速度慢，JavaScript 和 CSS 文件的下载将需要几秒钟才能完成，让用户在空屏幕上等待几秒钟。

这也会影响你的 Web 应用 SEO 评分；搜索引擎蜘蛛会到达你的页面，并发现它是空的。例如，谷歌机器人将等待 JavaScript 包被传输，但由于他们的等待时间，将给你的网站分配一个低性能评分。

默认情况下，Next.js 会将所有 React 组件在给定页面上在服务器端（如前文所述）或构建时渲染。在第一章的“从 React 迁移到 *Next.js*”部分，我们看到了 Node.js 运行时不暴露一些浏览器特定的 API，例如 `window` 或 `document`，或者 HTML 元素，例如 `canvas`，因此如果您尝试渲染需要访问这些 API 的任何组件，渲染过程将会崩溃。

有许多不同的方法可以避免 Next.js 中这类问题，要求将特定组件渲染到浏览器上。

## 使用 React.useEffect 钩子

如果您来自 React 16.8.0 之前的版本，您可能已经习惯了 `React.Component` 类的 `componentDidMount` 方法。在更现代的 React 版本中，它强调使用 `React.useEffect` 钩子。它将允许您在函数组件内部执行副作用（如数据获取和手动 DOM 更改），并且它将在组件挂载后执行。这意味着在 Next.js 中，`useEffect` 回调将在 React 活化后运行在浏览器上，让您能够仅在客户端执行某些操作。

例如，假设我们想在网页上使用 Highlight.js 库显示一个代码片段，使其易于高亮显示并使代码更易读。我们可以创建一个名为 `Highlight` 的组件，其外观如下：

```js
import Head from 'next/head';
import hljs from 'highlight.js';
import javascript from 'highlight.js/lib/languages/javascript';
function Highlight({ code }) {
   hljs.registerLanguage('javascript', javascript);
   hljs.initHighlighting();
  return (
    <>
      <Head>
        <link rel='stylesheet' href='/highlight.css' />
      </Head>
      <pre>
        <code className='js'>{code}</code>
      </pre>
    </>
  );
}
export default Highlight;
```

虽然这段代码在客户端 React 应用中可以完美运行，但在 Next.js 的渲染或构建阶段将会崩溃，因为 Highlight.js 需要全局变量 `document`，而 Node.js 中不存在这个变量，因为它是浏览器暴露的。

您可以通过将所有的 `hljs` 调用包裹在 `useEffect` 钩子中来轻松解决这个问题：

```js
import { useEffect } from 'react';
import Head from 'next/head';
import hljs from 'highlight.js';
import javascript from
 'highlight.js/lib/languages/javascript';
function Highlight({ code }) {
  useEffect(() => {
    hljs.registerLanguage('javascript', javascript);
    hljs.initHighlighting();
  }, []);
  return (
    <>
      <Head>
        <link rel='stylesheet' href='/highlight.css' />
      </Head>
      <pre>
        <code className='js'>{code}</code>
      </pre>
    </>
  );
}
export default Highlight;
```

这样，Next.js 将渲染我们组件返回的 HTML 标记，将 Highlight.js 脚本注入到我们的页面中，一旦组件在浏览器上挂载，它将在客户端调用库函数。

您也可以通过同时使用 `React.useEffect` 和 `React.useState` 来实现仅在客户端渲染组件的相同方法：

```js
import {useEffect, useState} from 'react';
import Highlight from '../components/Highlight';
function UseEffectPage() {
  const [isClient, setIsClient] = useState(false);
  useEffect(() => {
    setIsClient(true);
  }, []);
  return (
    <div>
      {isClient &&
        (<Highlight
          code={"console.log('Hello, world!')"}
          language='js'
        />)
      }
    </div>
  );
}
export default UseEffectPage;
```

这样，`Highlight` 组件将仅在浏览器上渲染。

## 使用 process.browser 变量

避免在服务器端使用浏览器特定 API 时崩溃的另一种方法是条件性地执行脚本和组件，这取决于全局变量 `process.browser`。实际上，Next.js 将这个极其有用的属性附加到 Node.js 的 `process` 对象上。它是一个布尔值，当代码在客户端运行时设置为 `true`，在服务器上运行时设置为 `false`。让我们看看它是如何工作的：

```js
function IndexPage() {
  const side = process.browser ? 'client' : 'server';
  return <div>You're currently on the {side}-side.</div>;
}
export default IndexPage;
```

如果您尝试运行前面的示例，您会注意到浏览器会短暂地显示以下文本：**您当前正在服务器端运行**；一旦 React 活化发生，它将被 **您当前正在客户端运行** 的文本所替换。

## 使用动态组件加载

正如我们在第一章中看到的，Next.js 通过添加一些优秀的内置组件和实用函数来扩展 React 的功能。其中之一被称为`dynamic`，这是框架提供的最有趣的模块之一。

记得我们构建的 Highlight.js 组件，以了解如何使用`React.useEffect`钩子在浏览器上渲染组件吗？这里还有另一种使用 Next.js 的`dynamic`函数来渲染它的方法：

```js
import dynamic from 'next/dynamic';
const Highlight = dynamic(
  () => import('../components/Highlight'),
  { ssr: false }
);
import styles from '../styles/Home.module.css';
function DynamicPage() {
  return (
    <div className={styles.main}>
      <Highlight
        code={"console.log('Hello, world!')"}
        language='js'
      />
    </div>
  );
}
export default DynamicPage;
```

在前面的代码中，我们通过`ssr: false`选项导入我们的`Highlight`组件。这样，Next.js 就不会尝试在服务器上渲染该组件，我们则需要等待 React 激活过程使其在浏览器上可用。

CSR（客户端渲染）可以是构建非常动态的 Web 页面的 SSR（服务器端渲染）的一个绝佳替代方案。如果你正在处理一个不需要被搜索引擎索引的页面，那么首先加载应用程序的 JavaScript，然后从客户端获取任何必要的数据，这可能是有意义的；这种方法可以减轻服务器端的工作负载，因为这种方法不涉及 SSR，并且你的应用程序可以更好地扩展。

那么，这里有一个问题——如果我们需要构建一个动态页面，而 SEO（搜索引擎优化）并不是特别重要（例如管理页面、私有个人资料页面等），为什么我们不直接向客户端发送一个静态页面，并在页面传输到浏览器后一次性加载所有数据呢？我们将在下一节探讨这种可能性。

# 静态站点生成

到目前为止，我们已经看到了两种渲染我们的 Web 应用的不同方式：客户端和服务器端。Next.js 为我们提供了一个名为**静态站点生成**（**SSG**）的第三种选择。

使用 SSG（静态站点生成），我们将在构建时能够预渲染一些特定的页面（如果需要，甚至整个网站）；这意味着在我们构建我们的 Web 应用时，可能会有一些页面内容不会经常改变，因此将它们作为静态资源提供服务是有意义的。Next.js 将在构建阶段渲染这些页面，并且始终提供那个特定的 HTML，就像 SSR 一样，将得益于 React 的激活过程而变得交互式。

与 CSR（客户端渲染）和 SSR（服务器端渲染）相比，SSG 带来了许多优势：

+   **易于扩展**：静态页面只是可以被任何内容分发网络（以下简称**CDN**）轻松提供和缓存的 HTML 文件。但即使你想使用自己的 Web 服务器来提供服务，由于不需要为提供静态资源进行复杂计算，这也会导致非常低的工作负载。

+   **卓越的性能**：正如之前所说，HTML 是在构建时预渲染的，因此客户端和服务器都可以绕过每个请求的运行时渲染阶段。Web 服务器将发送静态文件，浏览器只需显示它，就这么简单。服务器端不需要进行数据获取；我们需要的所有内容都已经预渲染在静态 HTML 标记中，这减少了每个请求的潜在延迟。

+   **更安全的请求**：我们不需要向 Web 服务器发送任何敏感数据来渲染页面，这使得恶意用户的生活变得有些困难。不需要访问 API、数据库或其他私人信息，因为所需的所有信息都已经包含在预渲染的页面中。

SSG 可能是构建高性能和高度可扩展的前端应用的最佳解决方案之一。关于这种渲染技术的最大担忧是，一旦页面构建完成，内容将保持不变，直到下一次部署。

例如，假设我们正在撰写一篇博客文章，但在标题中拼写了一个单词。使用其他静态站点生成器，如 Gatsby 或 Jekyll，我们需要重建整个网站来更改博客文章标题中的一个单词，因为我们需要在构建时重复数据获取和渲染阶段。记住我们在本节开头所说的话：静态生成的页面是在构建时创建的，并且作为每个请求的静态资源提供。

虽然这对于其他静态站点生成器也是正确的，但 Next.js 提供了一种独特的解决方案来解决这个问题：**增量静态再生**（**ISR**）。得益于 ISR，我们可以在页面级别指定 Next.js 在重新渲染更新内容的静态页面之前应该等待多长时间。

例如，假设我们想要构建一个显示一些动态内容的页面，但由于某种原因，数据获取阶段需要太长时间才能成功。这将导致性能不佳，给我们的用户带来糟糕的用户体验。SSG 和 ISR 的组合通过采用 SSR 和 SSG 之间的混合方法来解决这个问题。

让我们假设我们已经构建了一个非常复杂的仪表板，它可以处理大量数据……但获取这些数据的 REST API 请求可能需要几秒钟才能成功。在这种情况下，我们很幸运，因为在这段时间内，这些数据不会变化太多，所以我们可以使用 SSG 和 ISR 将其**缓存**长达 10 分钟（600 秒）：

```js
import fetch from 'isomorphic-unfetch';
import Dashboard from './components/Dashboard';
export async function getStaticProps() {
  const userReq = await fetch('/api/user');
  const userData = await userReq.json();
  const dashboardReq = await fetch('/api/dashboard');
  const dashboardData = await dashboardReq.json();
  return {
    props: {
      user: userData,
      data: dashboardData,
    },
    revalidate: 600 // time in seconds (10 minutes)
  };
}
function IndexPage(props) {
  return (
    <div>
      <Dashboard
        user={props.user}
        data={props.data}
      />
    </div>
  );
}
export default IndexPage;
```

我们现在使用一个名为`getStaticProps`的函数，它看起来与我们在上一节中看到的`getServerSideProps`函数相似。正如你可能已经猜到的，`getStaticProps`由 Next.js 在构建时用于获取数据和渲染页面，并且直到下一次构建之前不会再次调用。正如之前所说，虽然这可以非常强大，但它也有代价：如果我们想更新页面内容，我们必须重建整个网站。

为了避免整个网站重建，Next.js 最近引入了一个名为`revalidate`的选项，它可以在我们的`getStaticProps`函数的返回对象中设置。它表示在收到新请求后，我们应该过多少秒重建页面。

在前面的代码中，我们将我们的`revalidate`选项设置为`600`秒，因此 Next.js 将如下行为：

1.  Next.js 在构建时使用`getStaticProps`的结果填充页面，在构建过程中静态生成页面。

1.  在最初的 10 分钟内，每位用户都将访问完全相同的静态页面。

1.  10 分钟后，如果发生新的请求，Next.js 将在服务器端渲染该页面，重新执行`getStaticProps`函数，保存并缓存新渲染的页面作为静态资产，覆盖在构建时创建的先前的页面。

1.  在接下来的 10 分钟内，每个新的请求都将使用那个新静态生成的页面进行服务。

*请记住，ISR 过程是懒惰的，所以如果 10 分钟后没有发生任何请求，Next.js 不会重建其页面。*

如果你有所疑问，目前还没有通过 API 强制 ISR 重新验证的方法；一旦你的网站已经部署，你必须等待在`revalidate`选项中设置的过期时间长度，页面才能重建。

静态站点生成是一种创建快速和安全的网页的绝佳方式，但有时我们可能想要有更多动态的内容。多亏了 Next.js，我们总是可以决定在构建时间（SSG）或请求时间（SSR）渲染哪个页面。通过使用 SSG + ISR，我们可以取两者之长，使我们的页面成为 SSR 和 SSG 之间的“混合”体，这对现代 Web 开发来说是一个变革性的突破。

# 摘要

在本章中，我们看到了三种不同的渲染策略，以及 Next.js 如何通过其混合渲染方法将它们提升到全新的水平。我们还看到了这些策略的好处，当我们想要使用它们时，以及它们如何影响用户体验或服务器负载。在接下来的章节中，我们将始终关注这些渲染方法，为每个方法添加越来越多的示例和用例。它们是选择使用 Next.js 作为框架背后的核心概念。

在下一章中，我们将更详细地探讨一些最有用的内置 Next.js 组件，其路由系统，以及如何动态管理元数据以改善 SEO 和用户体验。
