# *第九章*：实现服务器端渲染

在上一章的进展基础上，我们现在使用我们的 **React** 应用在不同的路径下服务多个页面。目前，所有路由都在客户端直接进行。在本章中，我们将探讨 **服务器端渲染**（**SSR**）的优缺点。到本章结束时，你将配置 **Graphbook** 以从服务器而不是客户端作为预渲染的 HTML 来服务所有页面。

本章涵盖了以下主题：

+   介绍 SSR

+   在 **Express.js** 中设置 SSR 以在服务器上渲染 React

+   在 SSR 中启用 **JSON Web Token**（**JWT**）认证

+   在 React 树中运行所有我们的 **GraphQL** 查询

# 技术要求

本章的源代码可在以下 **GitHub** 仓库中找到：

[`github.com/PacktPublishing/Full-Stack-Web-Development-with-GraphQL-and-React-Second-Edition/tree/main/Chapter09`](https://github.com/PacktPublishing/Full-Stack-Web-Development-with-GraphQL-and-React-Second-Edition/tree/main/Chapter09)

# 介绍 SSR

首先，你必须理解使用服务器端渲染和客户端渲染应用之间的区别。在将纯客户端渲染应用转换为支持服务器端渲染（SSR）时，有许多事情需要考虑。在我们的应用中，当前的用户流程从客户端请求一个标准的 `index.html` 文件开始。这个文件只包含少量内容，例如一个包含一个 `div` 元素的少量 `body` 对象，一个带有一些非常基本的 `meta` 标签的 `head` 标签，以及一个至关重要的 `script` 标签，该标签下载捆绑的 `index.html` 和 `bundle.js` 文件。然后，客户端的浏览器开始处理我们编写的 React 标记。当 React 完成代码评估后，我们看到的就是我们想要看到的应用的 HTML。所有 CSS 文件或图像都是从我们的服务器下载的，但只有在 React 将 HTML 插入浏览器 **文档对象模型**（**DOM**）之后才会下载。在 React 的渲染过程中，**Apollo** 组件被执行，所有查询都被发送。当然，这些查询由我们的后端和数据库处理。

与 SSR 相比，客户端方法更为直接。在**Angular**、**Ember**、**React**和其他 JavaScript 框架开发之前，传统的方法是拥有一个后端，它实现了所有的业务逻辑，并且有大量的模板或函数返回有效的 HTML。后端查询数据库，处理数据，并将数据插入到 HTML 中。HTML 直接在客户端请求时提供。然后浏览器根据 HTML 下载 JavaScript、CSS 和图像文件。大多数情况下，JavaScript 只负责允许动态内容或布局变化，而不是渲染整个应用程序。这可能包括下拉菜单、手风琴或只是通过**Ajax**从后端拉取新数据。然而，应用程序的主要 HTML 直接从后端返回，这导致了一个单体应用程序。这个解决方案的一个显著优点是客户端不需要处理所有的业务逻辑，因为这一切已经在服务器上完成了。

然而，当我们谈论 React 应用程序中的 SSR 时，我们指的是不同的事情。在本书的这一部分，我们已经编写了一个在客户端渲染的 React 应用程序。我们不想以稍微不同的方式重新实现后端的渲染。我们也不希望失去在浏览器中动态更改数据、页面或布局的能力，因为我们已经有一个功能完善的应用程序，它为用户提供了许多交互可能性。

一种允许我们利用预渲染的 HTML 以及 React 提供的动态功能的方法被称为**通用渲染**。在通用渲染中，客户端的第一个请求包括一个预渲染的 HTML 页面。HTML 应该是客户端在自行处理时生成的确切 HTML。如果是这样，React 可以重用服务器提供的 HTML。由于 SSR 不仅涉及重用 HTML，还涉及节省 Apollo 发出的请求，因此客户端也需要一个 React 可以依赖的起始缓存。服务器在发送渲染的 HTML 之前发出所有请求，并将 Apollo 和 React 的状态变量插入到 HTML 中。结果是，在客户端的第一个请求中，我们的前端不应该需要重新渲染或刷新服务器返回的任何 HTML 或数据。对于所有后续操作，如导航到其他页面或发送消息，之前使用的相同客户端 React 代码仍然适用。换句话说，SSR 仅在第一次页面加载时使用。之后，这些功能不需要 SSR，因为客户端代码将继续像之前一样动态工作。

让我们开始编写一些代码。

# 在 Express.js 中设置 SSR 以在服务器上渲染 React

在这个例子中，第一步是在后端实现基本的 SSR。我们将在稍后扩展这个功能以验证用户的身份验证。经过身份验证的用户允许我们执行 Apollo 或 GraphQL 请求，而不仅仅是渲染纯 React 标记。首先，我们需要一些新的包。因为我们将使用通用渲染的 React 代码，我们需要一个高级的 webpack 配置。因此，我们将安装以下包：

```js
npm install --save-dev webpack-dev-middleware webpack-hot-middleware @babel/cli
```

让我们快速浏览一下我们要安装的包。我们只需要这些包进行开发：

+   第一个 webpack 模块，称为`webpack-dev-middleware`，允许后端服务由 webpack 生成的包，但仅从内存中生成，而不创建文件。这对于需要直接运行 JavaScript 而不想使用单独文件的情况非常有用。

+   第二个包，称为`webpack-hot-middleware`，仅处理客户端更新。如果创建了新的包版本，客户端会收到通知，并交换包。

+   最后一个包，称为`@babel/cli`，允许我们引入**Babel**为我们后端提供的出色功能。我们将使用需要转译的 React 代码。

在生产环境中，不建议使用这些包。相反，在部署应用程序之前，一次性构建包。当应用程序上线时，客户端下载包。

在启用 SSR 的开发中，后端使用这些包在 SSR 完成后将打包的 React 代码分发到客户端。服务器本身依赖于普通的`src`文件，而不是客户端接收的 webpack 包。

我们还依赖于一个额外的关键包，如下所示：

```js
npm install --save node-fetch
```

为了设置`window.fetch`方法。Apollo Client 使用它来发送 GraphQL 请求，这就是为什么我们要安装`node-fetch`作为 polyfill。我们将在本章后面设置 Apollo Client 以用于后端。

在开始主要工作之前，请确保您的`NODE_ENV`环境变量设置为`development`。

然后，转到服务器的`index.js`文件，所有 Express.js 的魔法都在这里发生。我们之前没有涵盖这个文件，因为我们现在要调整它以支持 SSR，包括直接的路由。

首先，我们将为 SSR 设置开发环境，因为这对我们接下来的任务至关重要。按照以下步骤准备您的开发环境以支持 SSR：

1.  第一步是导入两个新的 webpack 模块：`webpack-dev-middleware`和`webpack-hot-middleware`。这些模块应该只在开发环境中使用，因此我们应该通过检查环境变量有条件地引入它们。在生产环境中，我们提前生成 webpack 包。为了只在开发中使用新包，请将以下代码放在 Express.js helmet 设置下方：

    ```js
    if(process.env.NODE_ENV === 'development') {
      const devMiddleware = 
        require('webpack-dev-middleware');
      const hotMiddleware = 
        require('webpack-hot-middleware');
      const webpack = require('webpack');
      const config = 
        require('../../webpack.server.config');
      const compiler = webpack(config);
      app.use(devMiddleware(compiler));
      app.use(hotMiddleware(compiler));
    }
    ```

1.  在加载这些包之后，我们还将需要 webpack，因为我们将解析一个新的 webpack 配置文件。新的配置文件仅用于 SSR。

1.  在加载 webpack 和配置文件之后，我们将使用`webpack(config)`命令解析配置并创建一个新的 webpack 实例。

1.  接下来，我们将创建 webpack 配置文件。为此，我们将创建的 webpack 实例传递给我们的两个新模块。当一个请求到达服务器时，这两个包将根据配置文件采取行动。

与原始配置文件相比，新的配置文件只有几个小的差异，但这些差异影响很大。创建新的`webpack.server.config.js`文件，并输入以下配置：

```js
const path = require('path');
const webpack = require('webpack');
const buildDirectory = 'dist';
module.exports = {
  mode: 'development',
  entry: [
    'webpack-hot-middleware/client',
    './src/client/index.js'
  ],
  output: {
    path: path.join(__dirname, buildDirectory),
    filename: 'bundle.js',
    publicPath: '/'
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
        },
      },
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader'],
      },
      {
        test: /\.(png|woff|woff2|eot|ttf|svg)$/,
        loader: 'url-loader?limit=100000',

      },
    ],
  },
  plugins: [
    new webpack.HotModuleReplacementPlugin(),
    new webpack.NamedModulesPlugin(),
  ],
};
```

与原始的`webpack.client.config.js`文件相比，我们在前面的配置中做了三项更改，具体如下：

+   在`entry`属性中，我们现在有多个入口点。前端代码的`index`文件，如之前一样，是一个入口点。第二个入口点是新的`webpack-hot-middleware`模块，它启动客户端和服务器之间的连接。这个连接用于发送客户端通知，以更新到新版本的 bundle。

+   我已经移除了`devServer`字段，因为这个配置不需要 webpack 启动自己的服务器。Express.js 是我们要使用的 web 服务器，当加载配置时我们已经在使用它了。

+   插件与客户端的 webpack 配置中的插件完全不同。我们不需要`CleanWebpackPlugin`，因为它会清理`dist`文件夹，也不需要`HtmlWebpackPlugin`插件，该插件会将 webpack 打包文件插入到`index.html`文件中；这由服务器以不同的方式处理。这些插件仅适用于客户端开发。现在，我们有`HotModuleReplacementPlugin`，它启用了`NamedModulesPlugin`，显示由 HMR 注入的模块的相对路径。这两个插件仅推荐在开发中使用。

webpack 的准备工作现在已完成。

现在，我们必须关注如何渲染 React 代码以及如何服务生成的 HTML。然而，我们不能使用我们已编写的现有 React 代码。首先，我们必须对主文件进行特定的调整：`index.js`、`App.js`、`router.js`和`apollo/index.js`。我们使用的许多包，如**React Router**或 Apollo Client，都有默认设置或模块，当它们在服务器上执行时，我们必须进行不同的配置。

我们将从 React 应用程序的根目录开始，即`index.js`文件。我们将实现一个单独的 SSR `index`文件，因为需要进行特定的服务器调整。

在`server`文件夹内创建一个名为`ssr`的新文件夹。然后，将以下代码插入到`ssr`文件夹内的`index.js`文件中：

```js
import React from 'react';
import { ApolloProvider } from '@apollo/client';
import App from './app';
const ServerClient = ({ client, location, context }) => {
  return(
    <ApolloProvider client={client}>
      <App location={location} context={context}/>
    </ApolloProvider>
  );
}
export default ServerClient
```

上述代码是我们客户端 `index.js` 根文件的修改版本。该文件所经历的变化如下列所示：

+   我们现在不再使用 `ReactDOM.render` 函数将 HTML 插入具有 `root` ID 的 DOMNode 中，而是导出一个 React 组件。返回的组件被称为 `ServerClient`。我们没有可以访问的 DOM 来让 `ReactDOM` 渲染任何内容，所以在服务器端渲染时我们跳过这一步。

+   `ApolloProvider` 组件现在直接从 `ServerClient` 属性接收 Apollo Client，而之前我们是直接在这个文件内部通过从 `apollo` 文件夹导入 `index.js` 文件并将其传递给提供者来设置 Apollo Client。你很快就会看到我们为什么要这样做。

+   我们所做的最后一个变化是提取了一个 `location` 和一个 `context` 属性。我们将这些属性传递给 `App` 组件。在原始版本中，没有向 `App` 组件传递任何属性。这两个属性都是配置 React Router 以与 SSR 一起工作所必需的。我们将在本章后面实现这些属性。

在更详细地查看我们为什么要进行这些更改之前，让我们为后端创建一个新的 `App` 组件。在 `ssr` 文件夹中 `index.js` 文件旁边创建一个 `app.js` 文件，并插入以下代码：

```js
import React, { useState } from 'react';
import { Helmet } from 'react-helmet';
import { withApollo } from '@apollo/client/react/hoc';
import Router from '../../client/router';
import { useCurrentUserQuery } from '../../client/apollo/queries/currentUserQuery';
import '../../client/components/fontawesome';
const App = ({ location, context }) => {
  const { data, loading, error } = useCurrentUserQuery();
  const [loggedIn, setLoggedIn] = useState(false);
  return (
    <div className="container">
      <Helmet>
        <title>Graphbook - Feed</title>
        <meta name="description" content="Newsfeed of all 
          your friends on Graphbook" />
      </Helmet>
      <Router loggedIn={loggedIn}
        changeLoginState={setLoggedIn} location={location}
          context={context} />
    </div>
  )
}
export default withApollo(App)
```

以下是我们所做的几个更改：

+   与原始的客户端 `App` 组件相比，第一个变化是调整了 `import` 语句，以便从 `client` 文件夹中加载路由器和 `fontawesome` 组件，因为它们不存在于 `server` 文件夹中。

+   第二个变化是移除了 `useEffect` 钩子和 `localStorage` 访问。我们这样做是因为我们构建的认证使用了 `localStorage` 访问。这对于客户端认证来说是可行的。这两个 `useEffect` 钩子仅在客户端调用。这就是为什么我们在将我们的应用程序移动到 SSR 时移除认证。我们将在稍后的步骤中将 `localStorage` 实现替换为 cookies。目前，用户保持从服务器端注销状态。

+   最后一个变化是将两个新属性 `context` 和 `location` 传递给前面代码中的 `Router` 组件。

React Router 提供了对 SSR 的即时支持。尽管如此，我们仍需要进行一些调整。最好的方式是我们在后端和前端使用相同的路由器，这样我们就不需要定义两次路由，这既低效又可能导致问题。打开 `client` 文件夹内的 `router.js` 文件，按照以下步骤操作：

1.  将 `react-router-dom` 包的 `import` 语句更改为以下形式：

    ```js
    import { BrowserRouter, StaticRouter, Route, Redirect, Switch } from 'react-router-dom';
    ```

1.  插入以下代码以提取正确的路由：

    ```js
    let Router;
    if(typeof window !== typeof undefined) {
      Router = BrowserRouter;
    }
    else {
      Router = StaticRouter;
    }
    ```

    在导入 React Router 包后，我们通过查找`window`对象来检查文件是在服务器端还是客户端执行。由于 Node.js 中没有`window`对象，这是一个足够的检查。另一种方法是在一个单独的文件中设置`Switch`组件，包括路由。这种方法允许我们在为客户端和服务器端渲染创建两个单独的路由器文件时，直接将路由导入到正确的路由器中。

    如果我们在客户端，我们使用`BrowserRouter`，如果不是，我们使用`StaticRouter`。在这里，逻辑是，使用`StaticRouter`时，我们处于一个无状态的环境，其中我们使用固定的位置渲染所有路由。`StaticRouter`组件不允许通过重定向更改位置，因为在使用 SSR 时无法发生用户交互。其他组件`Route`、`Redirect`和`Switch`可以像以前一样使用。

    无论提取哪个路由器，我们都会将它们保存在`Router`变量中。然后我们在`routing`组件的返回语句中使用它们。

1.  我们准备了`context`和`location`属性，这些属性从顶层的`ServerClient`组件传递给`Router`变量。如果我们处于服务器端，这些属性应该被填充，因为`StaticRouter`对象需要它们。你可以在底部的`Routing`组件中替换`Router`标签，如下所示：

    ```js
    <Router context={this.props.context} location={this.props.location}>
    ```

    `location`对象包含路由应该渲染的路径。`context`变量存储`Router`组件处理的所有信息，例如重定向。我们可以在渲染`Router`组件后检查这个变量，以手动触发重定向。这是`BrowserRouter`和`StaticRouter`之间的一大区别。在前一种情况下，`BrowserRouter`会自动重定向用户，但`StaticRouter`不会。

成功渲染我们的 React 代码的关键组件现在已经准备好了。然而，还有一些模块在我们使用 React 渲染任何内容之前必须初始化。再次打开`index.js`服务器文件。目前，我们正在为`http://localhost:8000`根路径上的`dist`路径提供静态服务。当我们转向 SSR 时，我们必须在`/`路径上提供由我们的 React 应用程序生成的 HTML。

此外，任何其他路径，如`/app`，也应该使用 SSR 在服务器上渲染这些路径。删除文件底部的当前`app.get`方法，该方法位于`app.listen`方法之前。然后，插入以下代码作为替代：

```js
app.use('/', express.static(path.join(root, 'dist/client'), { index: false }));
app.get('*', (req, res) => {
  res.status(200);
  res.send('<!doctype html>');
  res.end();
});
```

代码的第一行应该替换旧的静态路由。它引入了一个名为`index`的新选项，这将禁用在根路径上提供`index.html`文件。

我们在先前的代码中使用星号 (`*`) 可以覆盖 Express.js 路由中定义的任何路径。始终记住，我们在 Express.js 中使用的 `services` 例程可以实现新的路径，例如 `/graphql`，我们不希望覆盖。为了避免这种情况，将代码放在文件的底部，在 `services` 设置下方。该路由捕获发送到后端的任何请求。

您可以通过运行 `npm run server` 命令来尝试此路由。只需访问 `http://localhost:8000` 即可。

目前，前面的通配符路由仅返回一个空的站点，状态为 `200`。让我们改变这一点。逻辑步骤将是加载并渲染 `ssr` 文件夹中的 `index.js` 文件中的 `ServerClient` 组件，因为它是 React SSR 代码的起点。然而，`ServerClient` 组件需要一个初始化的 Apollo Client 实例，正如我们之前解释的那样。我们将为 SSR 创建一个特殊的 Apollo Client 实例。

创建一个 `ssr/apollo.js` 文件，因为它还不存在。我们将在该文件中设置 Apollo Client。内容几乎与客户端原始设置相同：

```js
import { ApolloClient } from 'apollo-client';
import { InMemoryCache } from 'apollo-cache-inmemory';
import { onError } from 'apollo-link-error';
import { ApolloLink } from 'apollo-link';
import { HttpLink } from 'apollo-link-http';
import fetch from 'node-fetch';
export default (req) => {
  const AuthLink = (operation, next) => {
    return next(operation);
  };
  const client = new ApolloClient({
    ssrMode: true,
    link: ApolloLink.from([
      onError(({ graphQLErrors, networkError }) => {
        if (graphQLErrors) {
          graphQLErrors.map(({ message, locations, path,
            extensions }) => {
            console.log('[GraphQL error]: Message:
              ${message}, 
                Location: ${locations}, Path: ${path}');
          });
          if (networkError) {
            console.log('[Network error]:
              ${networkError}');
          }
        }
      }), 
      AuthLink,
      new HttpLink({
        uri: 'http://localhost:8000/graphql',
        credentials: 'same-origin',
        fetch
      })
    ]),
    cache: new InMemoryCache(),
  });
  return client;
};
```

然而，我们进行了一些更改，以便在服务器上使客户端工作。这些更改相当大，因此我们为服务器端 Apollo Client 设置创建了一个单独的文件。查看以下更改（如下）以了解前端和 SSR 设置之间的差异：

+   我们不再使用我们之前引入的 `createUploadLink` 函数来允许用户上传图片或其他文件，而是再次使用标准的 `HttpLink` 类。您可以使用 `UploadClient` 函数，但它在服务器上提供的功能将不会使用，因为服务器不会上传文件。

+   `AuthLink` 函数跳转到下一个链接，因为我们尚未实现服务器端身份验证。

+   `HttpLink` 对象接收 `fetch` 属性，该属性由我们在本章开头安装的 `node-fetch` 包填充。这用于替代在 Node.js 中不可用的 `window.fetch` 方法。

+   我们不是直接导出 `client` 对象，而是导出一个包装函数，它接受一个 `request` 对象。我们将其作为参数传递给 Express.js 路由。如您在先前的代码示例中看到的，我们尚未使用该对象，但很快就会改变。

在服务器 `index.js` 文件顶部导入 `ApolloClient` 组件，如下所示：

```js
import ApolloClient from './ssr/apollo';
```

导入的 `ApolloClient` 函数接受我们的 Express.js 服务器的 `request` 对象。

在新的 Express.js 通配符路由顶部添加以下行：

```js
const client = ApolloClient(req);
```

这样，我们就设置了一个新的 `client` 实例，我们可以将其传递给我们的 `ServerClient` 组件。

我们可以继续并实现 `ServerClient` 组件的渲染。为了使未来的代码工作，我们必须加载 React 和当然，`ServerClient` 组件本身：

```js
import React from 'react';
import Graphbook from './ssr/';
```

`ServerClient` 组件以 `Graphbook` 的名称导入。我们导入 React 是因为我们使用标准的 **JSX** 语法来渲染我们的 React 代码。

现在我们已经可以访问 Apollo Client 和 `ServerClient` 组件，在 Express.js 路由中的 `ApolloClient` 设置下方插入以下两行代码：

```js
const context= {};
const App = (<Graphbook client={client} location={req.url}
  context= {context}/>);
```

我们将初始化的 `client` 变量传递给 `Graphbook` 组件。我们使用常规的 React 语法传递所有属性。此外，我们将 `location` 属性设置为请求对象的 `url` 对象，以告诉路由器要渲染哪个路径。`context` 属性被传递为一个空对象。

然而，为什么我们在最后将一个空对象作为 `context` 传递给路由器呢？

原因是，在将 `Graphbook` 组件渲染成 HTML 之后，我们可以访问 `context` 对象并查看是否通常会触发重定向（或其他操作）。正如我们之前提到的，重定向必须由后端代码实现。React Router 的 `StaticRouter` 组件不会对您使用的 Node.js 网络服务器做出假设。这就是为什么 `StaticRouter` 不会自动执行它们。使用 `context` 变量跟踪和后处理这些事件是可能的。

生成的 React 对象被保存到一个新的变量中，该变量被命名为 `App`。现在，如果你使用 `npm run server` 启动服务器并访问 `http://localhost:8000`，应该不会出现任何错误。然而，我们仍然看到一个空页面。这是因为我们只返回了一个空的 HTML 页面；我们还没有将 React 的 `App` 对象渲染成 HTML。要将对象渲染成 HTML，请在服务器 `index.js` 文件的顶部导入以下包：

```js
import ReactDOM from 'react-dom/server';
```

`react-dom` 包不仅为浏览器提供了绑定，还提供了一个专门用于服务器的模块，这就是为什么我们在导入它时使用 `/server` 后缀。返回的模块提供了一系列仅适用于服务器的函数。

注意

要了解一些更高级的 SSR 特性和其背后的动态，你应该阅读 `react-dom` 服务器包的官方文档，请参阅 [`reactjs.org/docs/react-dom-server.html`](https://reactjs.org/docs/react-dom-server.html)。

我们可以通过使用 `ReactDOM.rendertoString` 函数将 React 的 `App` 对象转换成 HTML。在 `App` 对象下方插入以下代码行：

```js
const content = ReactDOM.renderToString(App);
```

此函数生成 HTML 并将其存储在 `content` 变量中。现在可以将 HTML 返回给客户端。如果您从服务器返回预渲染的 HTML，客户端会遍历它并检查其当前状态是否与返回的 HTML 匹配。比较是通过识别 HTML 中的某些点来进行的，例如 `data-reactroot` 属性。

如果在任何时候，服务器渲染的 HTML 和客户端将生成的 HTML 之间的标记不匹配，则会抛出错误。应用程序仍然可以工作，但客户端将无法使用 SSR；客户端将用重新渲染的一切替换从服务器返回的完整标记。在这种情况下，服务器的 HTML 响应被丢弃。这当然是非常低效的，并不是我们想要的结果。

我们必须将渲染的 HTML 返回给客户端。我们渲染的 HTML 以根`div`标签开始，而不是以`html`标签开始。我们必须将`content`变量包裹在一个包含周围 HTML 标签的模板中。因此，在`ssr`文件夹内创建一个`template.js`文件，并输入以下代码以实现我们渲染的 HTML 的模板：

```js
import React from 'react';
import ReactDOM from 'react-dom/server';
const htmlTemplate = (content) => {
  return '
    <html lang="en">
      <head>
        <meta charSet="UTF-8"/>
        <meta name="viewport" content="width=device-width, 
          initial-scale=1.0"/>
        <meta httpEquiv="X-UA-Compatible"
          content="ie=edge"/>
        <link rel="shortcut icon" 
          href="data:image/x-icon;," type="image/x-icon"> 
        ${(process.env.NODE_ENV === 'development')? "":
          "<link rel='stylesheet' href='/bundle.css'/>"}
      </head>
      <body>
        ${ReactDOM.renderToStaticMarkup(<div id="root" 
          dangerouslySetInnerHTML={{ __html: content 
            }}></div>)}
        <script src="img/bundle.js"></script>
      </body>
    </html>
  ';
};
export default htmlTemplate;
```

上述代码基本上与通常提供给客户端的`index.html`文件中的 HTML 标记相同。区别在于这里我们使用了 React 和`ReactDOM`。

首先，我们导出一个函数，该函数接受带有渲染后的 HTML 的`content`变量。

然后，我们在`head`标签内渲染一个`link`标签，如果我们在生产环境中，这个标签会下载 CSS 包。对于我们的当前开发场景，没有打包的 CSS。

重要的是，我们在`body`标签内使用了一个新的`ReactDOM`函数，名为`rendertoStaticMarkup`。这个函数将 React 的`root`标签插入到我们的 HTML 模板的 body 中。之前，我们使用的是`renderToString`方法，它包含了特殊的 React 标签，例如`data-reactroot`属性。现在，我们使用`rendertoStaticMarkup`函数生成没有特殊 React 标签的标准 HTML。我们传递给函数的唯一参数是带有`root` ID 的`div`标签和一个新属性`dangerouslySetInnerHTML`。这个属性是常规`innerHTML`属性的替代品，但用于 React。它允许 React 在根`div`标签内插入 HTML。正如其名所示，这样做是有风险的，但只有在客户端这样做时才有风险，因为`ReactDOM.renderToStaticMarkup`函数无法使用这个属性。插入的 HTML 最初是用`renderToString`函数渲染的，以便包含所有的关键 React HTML 属性和带有`root` ID 的包装`div`标签。然后，它可以在浏览器中被前端代码无问题地重用。

我们需要在服务器`index`文件中引入这个`template.js`文件，文件顶部如下：

```js
import template from './ssr/template';
```

模板函数现在可以直接在`res.send`方法中使用，如下所示：

```js
res.send('<!doctype html>\n${template(content)}');
```

我们现在不仅返回一个`doctype`对象，还响应`template`函数的返回值。正如您应该看到的，`template`函数接受作为参数的渲染后的`content`变量并将其组合成一个有效的 HTML 文档。

到目前为止，我们已经成功使我们的第一个服务器端渲染的 React 应用程序版本工作。你可以通过在浏览器窗口中右键单击并选择查看源代码来证明这一点。窗口显示服务器返回的原始 HTML。输出等于`template`函数的 HTML，包括登录和注册表单。

尽管如此，我们面临两个问题：

+   服务器响应中没有包含描述性 meta `head`标签。React `Helmet`肯定出了些问题。

+   当在客户端登录，例如在`/app`路径下查看新闻源时，服务器响应没有渲染新闻源或登录表单。通常，React Router 会重定向我们到登录表单，因为我们没有在服务器端登录。然而，由于我们使用了`StaticRouter`，我们必须单独发起重定向，正如我们之前解释的那样。我们将分步骤实现身份验证。

我们将从第一个问题开始。要修复 React `Helmet`的问题，在服务器`index.js`文件的顶部导入它，如下所示：

```js
import { Helmet } from 'react-helmet';
```

现在，在设置响应状态`res.status`之前，你可以提取 React `Helmet`的状态，如下所示：

```js
const head = Helmet.renderStatic();
```

`renderStatic`方法专门用于 SSR。我们可以在使用`renderToString`函数渲染 React 应用程序后使用它。它给我们提供了在整个代码中插入的所有`head`标签。将这个`head`变量作为第二个参数传递给`template`函数，如下所示：

```js
res.send('<!doctype html>\n${template(content, head)}');
```

返回到`ssr`文件夹中的`template.js`文件。将`head`参数添加到导出函数的签名中。然后，在 HTML 的`head`标签中添加以下两行新代码：

```js
${head.title.toString()}
${head.meta.toString()}
```

从 React `Helmet`提取的`head`变量为每个`meta`标签都持有属性。这些标签提供了一个`toString`函数，它返回一个有效的 HTML 标签，你可以直接将其输入到文档的`head`对象中。第一个问题应该得到修复：现在所有的`head`标签都包含在服务器的 HTML 响应中。

让我们专注于第二个问题。当访问`PrivateRoute`组件时，服务器响应返回一个空的 React `root`标签。正如我们之前解释的那样，这是因为自然发起的重定向没有到达我们这里，因为我们使用了`StaticRouter`。由于服务器端渲染的代码没有实现身份验证，所以我们被重定向离开了`PrivateRoute`组件。首先需要修复的是处理重定向，我们至少应该响应登录表单，而不是一个空的 React `root`标签。稍后，我们需要修复身份验证问题。

如果不查看服务器响应的源代码，你不会注意到这个问题。前端下载`bundle.js`文件并自行触发渲染，因为它知道用户的身份验证状态。用户不会注意到这一点。然而，如果服务器直接发送正确的 HTML，这将更加高效。如果用户已登录，HTML 将是错误的，但在未认证用户的情况下，登录表单是由服务器预先渲染的，因为它启动了重定向。

为了解决这个问题，我们可以在 React Router 使用`renderToString`函数后访问已经被填充的`context`对象。最终的 Express.js 路由应该看起来像以下代码示例：

```js
app.get('*', (req, res) => {
  const client = ApolloClient(req);
  const context= {};
  const App = (<Graphbook client={client} 
    location={req.url} context= {context}/>);
  const content = ReactDOM.renderToString(App);
  if (context.url) {
    res.redirect(301, context.url);
  } else {
    const head = Helmet.renderStatic();
    res.status(200);
    res.send('<!doctype html>\n${template(content, 
      head)}');
    res.end();
  }
});
```

在服务器上渲染正确路由的条件是检查`context.url`属性。如果它被填充，我们可以使用 Express.js 启动重定向。这将导航浏览器到正确的路径。如果属性没有被填充，我们可以返回 React 生成的 HTML。

此路由正确渲染了 React 代码，直到需要身份验证的点。SSR 路由正确渲染了所有公共路由，但没有渲染任何安全路由。这意味着我们现在只响应登录表单，因为它是唯一不需要身份验证的路由。

下一步是在与 SSR 结合的情况下实现身份验证，以解决这个问题。

# 使用 SSR 进行身份验证

你应该已经注意到，我们已经从服务器端的 React 代码中移除了大部分身份验证逻辑。这样做的原因是`localStorage`不能在页面初始加载时传输到服务器，这是 SSR 可以使用的唯一情况。这导致的问题是我们不能渲染正确的路由，因为我们不能验证用户是否已登录。身份验证必须转移到与每个请求一起发送的 cookies 上。

重要的是要理解，cookies 也会引入一些安全问题。我们将继续使用我们编写的 GraphQL API 的常规 HTTP 授权头。如果我们为 GraphQL API 使用 cookies，我们将使我们的应用程序容易受到潜在的**跨站请求伪造**（**CSRF**）攻击。前端代码继续使用 HTTP 授权头发送所有 GraphQL 请求。

我们将只使用 cookies 来验证用户的身份验证状态，并初始化对我们的 GraphQL API 的 SSR 请求。SSR GraphQL 请求将在 HTTP 授权头中包含授权 cookie 的值。我们的 GraphQL API 只读取和验证这个头，不接受 cookies。只要你在加载页面时没有修改数据，并且只查询要渲染的数据，就不会存在安全问题。

小贴士

由于 CSRF 和 XSS 是一个重要的主题，我建议你阅读相关内容，以便全面了解如何保护自己和用户。你可以在[`www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)`](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF))找到一篇优秀的文章。

因此，只需按照以下说明来在 SSR 上实现身份验证：

1.  首件事是使用`npm`安装一个新的包，如下所示：

    ```js
    cookies package allows us to easily interact through the Express.js request object with the cookies sent by the browser. Instead of manually parsing and reading through the cookie string (which is just a comma-separated list), you can access the cookies with simple get and set methods. To get this package working, you have to initialize it inside Express.js.
    ```

1.  导入`cookies`和`jwt`包，并从服务器`index.js`文件顶部的环境变量中提取`JWT_SECRET`字符串：

    ```js
    import Cookies from 'cookies';
    import JWT from 'jsonwebtoken';
    const { JWT_SECRET } = process.env;
    ```

    要使用`cookies`包，我们需要设置一个新的中间件路由。

1.  在初始化 webpack 模块和服务流程之前插入以下代码：

    ```js
    app.use(
      (req, res, next) => {
        const options = { keys: ['Some random keys'] }; 
        req.cookies = new Cookies(req, res, options); 
        next();
      }
    );
    ```

    这个新的 Express.js 中间件为它处理的每个请求在`req.cookies`属性下初始化`cookies`包。`Cookies`构造函数的第一个参数是请求，第二个是响应对象，最后一个是一个`options`参数。这个参数接受一个`keys`数组，用于对 cookies 进行签名。如果你出于安全原因想要对 cookies 进行签名，则这些键是必需的。在生产环境中，你应该注意这一点。你也可以指定一个`secure`属性，这确保了 cookies 只会在安全的 HTTPS 连接上传输。

1.  我们现在可以提取`authorization`cookie 并验证用户的身份验证。为此，将服务器`index.js`文件中 SSR 路由的开始部分替换为以下代码：

    ```js
    app.get('*', async (req, res) => {
      const token = req.cookies.get('authorization', 
        { signed: true });
      var loggedIn;
      try {
        await JWT.verify(token, JWT_SECRET);
        loggedIn = true;
      } catch(e) {
        loggedIn = false;
      }
    ```

    在这里，我已将`async`声明添加到回调函数中，因为我们在这个函数内部使用了`await`语句。第二步是从请求对象中提取`authorization`cookie，使用`req.cookies.get`。重要的是，我们在`options`参数中指定了`signed`字段，因为只有这样它才能成功返回签名的 cookies。

    提取的值代表我们在用户登录时生成的 JWT。我们可以通过我们在*第六章*中实现的典型方法来验证它，即*使用 Apollo 和 React 进行身份验证*。也就是说，我们在验证 JWT 时使用`await`语句。如果抛出错误，则用户未登录。状态保存在`loggedIn`变量中。

1.  将`loggedIn`变量传递给`Graphbook`组件，如下所示：

    ```js
    const App = (<Graphbook client={client} loggedIn={loggedIn} location={req.url} context={context}/>);
    ```

    现在，我们可以从`ssr`文件夹中的`index.js`文件访问`loggedIn`属性。

1.  从属性中提取`loggedIn`状态，并将其传递给`ssr`文件夹的`index.js`文件中的`App`组件，如下所示：

    ```js
    <App location={location} context={context} loggedIn={loggedIn}/>
    ```

    在`App`组件内部，我们不需要直接将`loggedIn`状态设置为`false`，但我们可以获取属性值，因为它在`App`组件渲染之前就已经确定了。这种流程与客户端流程不同，在客户端流程中，`loggedIn`状态是在`App`组件内部确定的。

1.  修改`app.js`文件中的`App`组件，以匹配以下代码：

    ```js
    const App = ({ location, context, loggedIn: loggedInProp }) => {
      const { data, loading, error } = 
        useCurrentUserQuery();
      const [loggedIn, setLoggedIn] = 
        useState(loggedInProp);
    ```

    在这里，结果是我们将`loggedIn`值从我们的 Express.js 路由传递到`Graphbook`和`App`组件，再到我们的`Router`组件。这已经接受`loggedIn`属性以渲染用户的正确路径。目前，我们还没有在用户成功登录时在后端设置 cookie。

1.  打开我们的 GraphQL 服务器的`resolvers.js`文件以修复此问题。我们将为`login`和`signup`函数更改几行。由于两个解析函数在登录或注册后都需要设置认证令牌，因此它们需要相同的更改。所以，直接在返回语句上方插入以下代码：

    ```js
    context.cookies.set(
      'authorization',
      token, { signed: true, expires: expirationDate, 
        httpOnly: true, secure: false, sameSite: 'strict'
          }
    );
    ```

    上述函数为用户的浏览器设置 cookie。上下文对象仅是 Express.js 的`request`对象，其中我们初始化了 cookies 包。`cookies.set`函数的属性相当直观，但让我们如下描述它们：

    a. `signed`字段指定在初始化`cookies`对象时输入的密钥是否应该用于签名 cookie 的值。

    b. `expires`属性接受一个`date`对象。它表示 cookie 有效的截止时间。您可以设置属性为任何您想要的日期，但我建议设置一个较短的时间，例如一天。在`context.cookies.set`语句上方插入以下代码，以正确初始化`expirationDate`变量：

    ```js
    const cookieExpiration = 1;
    const expirationDate = new Date(); 
    expirationDate.setDate(
      expirationDate.getDate() + cookieExpiration
    );
    ```

    c. `httpOnly`字段确保 cookie 不会被客户端 JavaScript 访问。

    d. `secure`属性与初始化`Cookie`包时的含义相同。它将 cookie 限制为仅 SSL 连接。在上线时这是必须的，但在开发时不能使用，因为大多数开发者都是在本地开发，没有 SSL 证书。

    e. `sameSite`字段可以取`strict`或`lax`作为值。我建议将其设置为`strict`，因为您希望您的 GraphQL API 或服务器在每次请求时都接收 cookie，但您也希望排除所有跨站请求，因为这可能是危险的。

1.  现在，我们应该清理我们的代码。由于我们正在使用 cookies，我们可以从前端代码中移除`localStorage`认证流程。在`client`文件夹中打开`App.js`文件。移除`componentWillMount`方法，因为我们从`localStorage`中读取数据。

    cookies 会自动与任何请求一起发送，并且不需要像`localStorage`那样的单独绑定。这也意味着我们需要一个特殊的`logout`突变来从浏览器中删除 cookie。JavaScript 无法访问或删除 cookie，因为我们将其指定为`httpOnly`。只有服务器才能从客户端删除它。

1.  在`mutations`文件夹内创建一个新的`logout.js`文件，以便创建一个`logout`突变钩子。内容应如下所示：

    ```js
    import { gql, useMutation } from '@apollo/client';
    export const LOGOUT = gql'
      mutation logout {
        logout {
          success
        }
      }
    ';
    export const useLogoutMutation = () => useMutation(LOGOUT);
    ```

    之前的功能钩子仅发送一个简单的 `logout` 变异，没有任何参数或进一步逻辑。

1.  我们应该使用 `bar` 文件夹中 `logout.js` 文件内的函数来发送 GraphQL 请求。在文件顶部导入组件，如下所示：

    ```js
    import { useLogoutMutation } from '../../apollo/mutations/logout';
    ```

1.  将 `logout` 方法替换为以下代码，以便在点击 `logoutMutation` 函数时发送变异。这会将 GraphQL 请求发送到我们的服务器。

1.  要在 `schema.js` 中的 GraphQL `RootMutation` 类型上实现变异，请向后台添加一行代码：

    ```js
    logout: Response @auth
    ```

    需要确保尝试注销的用户已被授权，因此我们使用 `@auth` 指令。

1.  相应的解析函数如下。将其添加到 `resolvers.js` 文件中的 `RootMutation` 属性：

    ```js
    logout(root, params, context) {
      context.cookies.set(
        'authorization',
        '', { signed: true, expires: new Date(), httpOnly:
          true, secure: false, sameSite: 'strict' }
      );
      return {
        message: true
      };
    },
    ```

    解析函数是最小的。它通过将过期日期设置为当前时间来删除 cookie。当浏览器收到响应时，由于此时已过期，它会在客户端删除 cookie。与 `localStorage` 相比，这种行为是一个优点。

我们已经完成了所有工作，使授权与 SSR 一起工作。这是一个非常复杂的任务，因为授权、SSR 和 CSR 对整个应用程序都有影响。每个框架都有自己的方法来实现这个功能，所以请也查看它们。

如果您查看渲染后从我们的服务器返回的源代码，您应该看到登录表单被正确返回，就像之前一样。此外，服务器现在能够识别用户是否已登录。然而，服务器尚未返回渲染的新闻源、应用栏或聊天。返回的 HTML 中只包含一个加载消息。客户端代码也没有识别出用户已登录。我们将在下一节中查看这些问题。

# 使用 SSR 运行 Apollo 查询

通过本质，GraphQL 查询通过 `HttpLink` 是异步的。我们已经实现了一个 `loading` 组件，在数据正在获取时向用户显示加载消息。

这与我们在服务器上渲染 React 代码时发生的情况相同。所有路由都被评估，包括我们是否已登录。如果找到正确的路由，所有 GraphQL 请求都会发送。问题是第一次渲染 React 返回加载状态，由我们的服务器发送到客户端。服务器不会等待 GraphQL 查询完成并收到所有响应，然后渲染我们的 React 代码。

我们现在将解决这个问题。以下是我们必须做的事情列表：

+   我们需要实现 SSR Apollo 客户端实例的认证。我们已经在路由中做到了这一点，但现在，我们需要将 cookie 传递到服务器端的 GraphQL 请求中。

+   我们需要使用 React Apollo 特定的方法来异步渲染 React 代码，以便等待所有 GraphQL 请求的响应。

+   重要的是，我们需要将 Apollo 缓存状态返回给客户端。否则，客户端将重新获取所有内容，因为它的状态在页面首次加载时是空的。

让我们开始吧，如下所示：

1.  第一步是将 Express.js SSR 路由中的`loggedIn`变量传递给`ApolloClient`函数作为第二个参数。将服务器`index.js`文件中的`ApolloClient`调用修改为以下代码：

    ```js
    const client = ApolloClient(req, loggedIn);
    ```

    将从`apollo.js`文件导出的函数的签名修改为也包括这个第二个参数。

1.  用以下代码替换 Apollo Client 设置中的`AuthLink`函数：

    ```js
    const AuthLink = (operation, next) => {
      if(loggedIn) {
        operation.setContext(context => ({
          ...context,
          headers: {
            ...context.headers,
            Authorization: 
              req.cookies.get('authorization')
          },
        }));
      }
      return next(operation)
    };
    ```

    这个`AuthLink`通过使用 Express.js 提供的`request`对象将 cookie 添加到 GraphQL 请求中。`request`对象已经包含了初始化的 cookie 包，我们使用它来提取授权 cookie。这只有在用户之前已经验证为登录状态时才需要执行。

1.  在服务器的`index.js`文件中导入 Apollo 包中的一个新函数。用以下代码替换对`ReactDOM`包的导入：

    ```js
    import { renderToStringWithData } from "@apollo/client/react/ssr";
    ```

1.  最初，我们使用`ReactDOM`服务器方法将 React 代码渲染为 HTML。这些函数是同步的；这就是为什么 GraphQL 请求没有完成。为了等待所有 GraphQL 请求，替换服务器`index.js`文件中从`rendertoString`函数开始直到 SSR 路由结束的所有行。结果应该如下所示：

    ```js
    renderToStringWithData(App).then((content) => {
      if (context.url) {
        res.redirect(301, context.url);
      } else {
        const head = Helmet.renderStatic();
        res.status(200);
        res.send('<!doctype html>\n${template(content,
          head)}');
        res.end();
      }
    });
    ```

    `renderToStringWithData`函数渲染 React 代码，包括通过 Apollo 请求接收到的数据。由于该方法异步，我们将其余代码包裹在一个回调函数中。

    现在，如果你查看服务器返回的 HTML，你应该看到正确的标记，包括聊天、图片以及其他所有内容。问题是客户端不知道所有的 HTML 已经存在并且可以被重用。客户端将重新渲染一切。

1.  为了让客户端能够重用我们服务器发送的 HTML，我们必须将 Apollo Client 的状态包含在我们的响应中。在先前的回调函数内部，通过插入以下代码来访问 Apollo Client 的状态：

    ```js
    const initialState = client.extract();
    ```

    `client.extract`方法返回一个包含客户端使用`renderToStringWithData`函数后存储的所有缓存信息的大对象。

1.  状态必须作为第三个参数传递给`template`函数。因此，将`res.send`调用修改为以下代码：

    ```js
    res.send('<!doctype html>\n${template(content, head, initialState)}');
    ```

1.  在`template.js`文件中，扩展函数声明并在`head`变量之后追加`state`变量作为第三个参数。

1.  在 HTML body 中`bundle.js`文件上方插入`state`变量，使用以下代码。如果你将它添加到`bundle.js`文件下方，它将无法正确工作：

    ```js
    ${ReactDOM.renderToStaticMarkup(<script dangerouslySetInnerHTML=
    {{__html: 'window.__APOLLO_STATE__=${JSON.stringify(state).replace
    (/</g, '\\u003c')}'}}/>)}
    ```

    我们使用`renderToStaticMarkup`函数插入另一个`script`标签。它将一个大型、字符串化的 JSON 对象设置为 Apollo Client 的起始缓存值。该 JSON 对象包含在渲染我们的服务器端 React 应用程序时返回的所有 GraphQL 请求的结果。我们直接将 JSON 对象作为字符串存储在`window`对象的新字段中。`window`对象很有用，因为您可以直接全局访问该字段。

1.  Apollo 必须了解状态变量。它可以被 Apollo Client 用来用指定数据初始化其缓存，而不是必须再次发送所有 GraphQL 请求。打开客户端`apollo`文件夹中的`index.js`文件。初始化过程的最后一个属性是缓存。我们需要将我们的`__APOLLO_STATE__`实例设置为缓存的起始值。将`cache`属性替换为以下代码：

    ```js
    cache: new InMemoryCache().restore(window.__APOLLO_STATE__)
    ```

    我们创建了`InMemoryCache`实例并运行其`restore`方法，其中我们插入来自窗口对象的价值。Apollo Client 应该从该变量重新创建其缓存。

1.  我们现在已经为 Apollo 设置了缓存。它将不再运行已经存在结果的不必要请求。现在，我们最终可以重用 HTML，只需进行最后一次更改。我们必须在客户端的`index.js`文件中将`ReactDOM.render`更改为`ReactDOM.hydrate`。这两个函数之间的区别在于，如果我们的服务器正确渲染了 HTML，React 会重用该 HTML。在这种情况下，React 仅附加一些必要的事件监听器。如果您使用`ReactDOM.render`方法，它将显著减慢初始渲染过程，因为它会将初始 DOM 与当前 DOM 进行比较，并根据需要进行更改。

我们遇到最后一个问题是客户端代码在刷新页面后不显示应用程序的登录状态。服务器返回了包含所有数据的正确标记，但前端将我们重定向到登录表单。这是因为我们在客户端代码的`App.js`文件中静态地将`loggedIn`状态变量设置为`false`。

检查用户是否认证的最佳方式是验证窗口对象上的`__APOLLO_STATE__`字段是否被填充并且附加了一个`currentUser`对象。如果是这样，我们可以假设用户能够检索到自己的数据记录，所以他们必须已经登录。为了相应地更改我们的`App.js`文件，向`loggedIn`状态变量添加以下条件：

```js
(typeof window.__APOLLO_STATE__ !== typeof undefined && typeof window.__APOLLO_STATE__.ROOT_QUERY !== typeof undefined && typeof window.__APOLLO_STATE__.ROOT_QUERY.currentUser !== typeof undefined)
```

如您在前面的代码中所见，我们验证 Apollo 启动缓存变量是否包含一个带有`currentUser`子字段的`ROOT_QUERY`属性。如果任何查询可以成功检索，则`ROOT_QUERY`属性会被填充。只有当认证用户成功请求时，`currentUser`字段才会被填充。

如果你执行`npm run server`，你会看到现在一切运行得非常完美。看看返回的标记；你会看到登录表单，或者当你登录时，你访问的页面的全部内容。你可以在客户端登录，新闻源会动态获取，你可以刷新页面，所有的帖子都会直接显示，无需进行单个 GraphQL 请求，因为服务器已经将数据与 HTML 一起返回。这不仅适用于`/app`路径，也适用于你实现的任何路径。

我们现在已经完成了 SSR 的设置。

到目前为止，我们只看了 SSR 的开发部分。当我们到达想要制作生产构建并发布我们的应用的时候，我们还需要考虑一些其他的事情，这些内容我们将在*第十二章*，*使用 CircleCI 和 AWS 进行持续部署*中探讨。

# 摘要

在本章中，我们修改了我们迄今为止编写的大量代码。你学习了提供 SSR 的优势和劣势。React Router、Apollo 以及使用 SSR 进行 cookie 认证的主要原则现在应该已经很清晰了。要让 SSR 运行起来需要做很多工作，并且需要管理你应用中每一次的更改。尽管如此，它为你的用户提供了卓越的性能和用户体验优势。

在下一章中，我们将探讨如何通过**Apollo Subscriptions**提供实时更新，而不是使用旧的和低效的轮询方法。
