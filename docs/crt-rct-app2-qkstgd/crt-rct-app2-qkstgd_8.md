# 第八章：为您的应用准备生产环境

在这本书的篇幅内，我们已经在 Create React App 中构建了一个相当不错的入门级应用。虽然它可能不会赢得任何奖项，但你现在有了建立在这个基础之上的能力，并将其转变为下一个世界领先的番茄工作法追踪器，或者可能是为开发者开发的新任务管理应用！在这个过程中，我们涵盖了构建过程和引导过程，但尚未关注构建任何应用的最终部分：将其部署到生产环境！

我们已经花费了很多时间讨论 Create React App 中的不同重要特性，并在其基础上构建了一个应用。我们对应用做了很多扩展；我们将一些从零开始的东西变成了一个功能性的应用，使用了最新的最先进的现代 JavaScript 编程技术，并且使用了在以任何专业环境使用 Create React App 时你会看到的那些技术。

我们需要进一步扩展并介绍我们还可以运行的剩余 Create React App CLI 命令：`build` 和 `eject`。我们在一些地方添加了一些额外的库，主要是作为实用库，但我们也应该花一些时间与一些额外的库一起工作，以便了解这对工作流程的影响。因此，我们将讨论一些常见的库，包括它们的用法、影响以及如何了解更多关于它们的信息。

到本章结束时，你将了解以下主题：

+   将其他库添加到我们的应用中

+   使用 Create React App `eject` 及其对应用的影响

+   使用 Create React App `build` 创建生产构建

# 添加其他库

正如我们之前提到的，我们将添加一些更多的库。如果你对交换这些库中的任何一个不感兴趣，你可以安全地跳过这一部分，但有一些可能代表你在团队环境中编写 JavaScript 时会遇到的情况的项目要讨论。例如，Redux 在某个时刻被认为是构建任何具有一定复杂性的 React 项目的（如果不是必需的）重要部分。最近，这种心态有所减弱，一般来说，人们只有在更必要的时候才会选择使用像 Redux 这样的库，当状态管理是它们应用中更困难的部分时。无论如何，我们应该有在应用中添加它的经验，这样我们就可以准备好为任何可能使用它的项目做出贡献。还有许多其他常见的库与 React 一起使用，我们将在快速介绍它们之后，再转到使用 Redux 在 Create React App 项目中的快速示例！

# 其他流行的 React 库

在使用 React 的时候，还会经常遇到其他库。这些可能是帮助您使用浏览器地址栏来确定要渲染为主应用的组件的库，或者可能是为 React 中的任何表单元素提供更多原生功能并减少更多模板代码的库。

# React Router

React Router 是一个旨在帮助您将请求路由到特定组件的库。其理念是，当有人访问您的项目时，他们可能想要与一个特定的组件进行交互。这有助于您管理这些请求并找出将用户直接带到该组件的正确方式。好消息是，在 Create React App 项目中安装 React Router 并使用它实际上非常简单！要安装它，您只需运行以下命令：

```js
$ yarn add react-router-dom
```

然后，您可以将 `src/App/App.js` 文件修改为开始向组件添加新路由！React Router 与 Redux 类似，它本身是一个非常复杂的工程；因此，如果您想了解更多，可以访问 [`reacttraining.com/react-router/`](https://reacttraining.com/react-router/)。

# React Final Form

如果您正在处理任何形式的交互式网络应用，您可能需要能够以一致和高效的方式处理网页表单。好消息是，由于这是一个非常普遍的问题，因此有多个流行的库可以高效地处理 React 中的表单处理。其中最受欢迎的是 React Final Form，您可以通过运行以下命令将其添加到项目中：

```js
$ yarn add react-final-form final-form
```

从那里，您可以使用 React Final Form 的任何功能来替换应用程序中的表单！有关如何充分利用此库的更多信息，您可以访问 [`github.com/final-form/react-final-form`](https://github.com/final-form/react-final-form)。

# 添加 Redux 进行状态管理

Redux 是一个用于管理应用程序状态的常见库，尤其是在应用程序稍微复杂一些，并且您想要控制前端项目中事件流的时候！Redux 提供了一种方式，让您可以紧密控制应用程序的整个状态如何受到不同类型事件的影响，例如按钮点击或表单更改。如果您的应用程序变得非常大且复杂，管理影响应用程序的众多不同事件流变得过于困难，那么在项目中引入类似 Redux 这样的工具将是一个很好的时机！

好消息是，与之前的库类似，您也可以轻松地将 Redux 添加到您的 Create React App 项目中！您需要添加 `redux` 和 `react-redux`，以确保您拥有连接一切所需的所有绑定：

```js
$ yarn add redux react-redux
```

这应该会安装你开始使用 Redux 所需的所有内容。类似于我们提到的其他库，Redux 是一个极其复杂的话题，单独一本书都足以涵盖。如果你想了解更多关于 Redux 的信息，请访问[`redux.js.org/`](https://redux.js.org/)。

# 创建一个生产构建

最后，我们准备好将所有这些打包起来，为生产部署做好准备！我们的代码完成了，代码运行正常，一切都很完美；那么，我们现在该做什么呢？

这很简单；我们只需运行`yarn build`，然后就算完成了！嗯，那基本上就是整个过程。你看，当你运行`yarn build`时，Create React App 会尝试找出最佳、最高效的方式来捆绑你在 Create React App 项目中所有的工作，并最小化/生产化你所写的每一行代码——你添加的每一张图片和资产，需要包含的每一个库——所有这些。

# 如何创建一个生产构建

要创建一个生产构建，我们只需要运行`yarn build`。就这么简单！请看以下内容：

```js
$ yarn build
yarn run v1.12.3
$ react-scripts build
Creating an optimized production build...
Compiled successfully.

File sizes after gzip:

69.67 KB (+4.53 KB) build/static/js/1.877dc59d.chunk.js
21.85 KB build/static/css/1.4cb1c3c1.chunk.css
2.32 KB (+46 B) build/static/js/main.1f5d1459.chunk.js
763 B build/static/js/runtime~main.229c360f.js
507 B build/static/css/main.af121e46.chunk.css

The project was built assuming it is hosted at the server root.
You can control this with the homepage field in your package.json.
For example, add this to build it for GitHub Pages:

"homepage" : "http://myname.github.io/myapp",

The build folder is ready to be deployed.
You may serve it with a static server:

yarn global add serve
serve -s build

Find out more about deployment here:

http://bit.ly/CRA-deploy

Done in 26.70s.
```

现在，你应该在你的应用程序的`build/`目录中拥有一个生产就绪的文件构建！这是一个高度优化的构建，准备好复制和部署，无论你在哪里可以部署你的应用程序！

需要注意的重要一点是，这个过程输出的结果是静态文件（完全编译的 HTML/JS/CSS），可以从任何可以服务静态文件回传给用户的地方部署和运行——所以，本质上，任何你已有的**内容分发网络**（**CDN**）或网页服务器都可以。

这个过程高度依赖于 Webpack 的构建过程和多个不同的构建和优化插件，它们优化了构建过程的几乎每一个部分。文件被压缩，根据它们的用途和范围进行分块（以减少导入我们不需要的文件），并设置为允许浏览器缓存，以尽可能减少每次有人重新访问你的网站时的努力！

**压缩**意味着文件通过重命名代码、最小化空白或以其他方式减少多余代码，尽可能减小文件大小，从而使需要部署的 JavaScript 量尽可能小。

# 关于部署过程的思考

实际的部署过程要复杂一些。有无数种部署方法，但鉴于我们是在一个代理后端服务器上构建的应用程序，其中一种方法是我们将项目集成到一个现有的后端服务器中，使用某种东西来服务我们的前端代码，例如 Rails/node/Phoenix 服务器。这允许我们运行这段代码作为我们的前端，并在幕后有一个后端服务器支持这个应用程序。没有那个，我们的应用程序将无法工作；如你所回忆，这是一个由`Todo`后端支持的应用程序。

# 退出我们的项目

我们可用的另一个选项是能够在 Create React App 中 `eject` 我们的项目。**Ejecting** 一个应用意味着它移除了 Create React App CLI 的所有脚手架和限制，以及你可能从这个操作中预期到的所有好处和注意事项。首先，我们获得了更大的能力和控制权，可以按需调整事物，这是非常好的；但这也让你进入了一个需要理解你的 Babel 配置、Webpack 配置以及你之前能够忽略的每一个幕后配置选项的世界。

`eject` 是一把双刃剑；它是一个强大的工具，它让你能够超越 Create React App 带入你世界中的规则。然而，你现在将负责未来配置修改带来的任何头痛问题。

话虽如此，`eject` 在 Create React App 的世界中也是一个重要的命令。我见过许多项目最初是以 Create React App 构建的项目开始的，但随着应用变得足够复杂，它们发生了变化。在那个阶段，进行 `eject` 是一个好主意，这样你就可以根据需要相应地调整事物。再次强调，本书的一个主要重点是理解如何成为 Create React App 及其最新版本的专家；因此，理解和使用所有命令是这一过程的重要组成部分。

# 如何进行 `eject`

让我们先来谈谈如何使用 Create React App 进行 `eject`。这个过程本身其实很简单：你只需运行 `npm run eject` 或 `yarn eject`，然后过程就开始了。这也不是足够的信息来做出关于何时何地使用 Create React App 进行 `eject` 的明智决定，所以我们将实际探索运行该命令的结果。我们将首先将其移动到我们选择的源控制中的新分支（或者，如果你没有使用源控制，将目录复制到某个地方，这样你就可以在没有失去项目的风险下进行实验）。

这是一个永久性的操作。如果你不希望被这些更改所困扰，确保你已经复制了你的文件夹，或者以某种方式创建了分支，这样你就不需要在这一部分被锁定在 `eject` 中了！

我们得到以下输出：

```js
$ yarn eject

yarn run v1.12.3
$ react-scripts eject
? Are you sure you want to eject? This action is permanent. (y/N)
If you answer yes, you'll see a ton of output:

Ejecting...

Copying files into /Users/brandon/Documents/dev/create-react-app-book/code/todoifier
Adding /config/env.js to the project
Adding /config/paths.js to the project
Adding /config/webpack.config.dev.js to the project
Adding /config/webpack.config.prod.js to the project
Adding /config/webpackDevServer.config.js to the project
Adding /config/jest/cssTransform.js to the project
Adding /config/jest/fileTransform.js to the project
Adding /scripts/build.js to the project
Adding /scripts/start.js to the project
Adding /scripts/test.js to the project

Updating the dependencies
Removing react-scripts from dependencies
Adding ...lots of packages... to dependencies

Updating the scripts
Replacing "react-scripts start" with "node scripts/start.js"
Replacing "react-scripts build" with "node scripts/build.js"
Replacing "react-scripts test" with "node scripts/test.js"

Configuring package.json
Adding Jest configuration
Adding Babel preset
Adding ESLint configuration

Running yarn...
[1/4] Resolving packages...
[2/4] Fetching packages...
[3/4] Linking dependencies...
[4/4] Building fresh packages...

success Saved lockfile.
Ejected successfully!

Please consider sharing why you ejected in this survey:
http://goo.gl/forms/Bi6CZjk1EqsdelXk1

Done in 14.39s.
```

哇！当你 `eject` 时会发生很多事情！它大约创建了 13 个新的脚本，其中大多数是辅助/实用 JavaScript 文件或 Webpack、Babel 或 Jest 等事物的配置文件。这其中的许多是为了让你能够继续使用你的项目，就像它已经是一个主要的 Create React App 项目一样，尽管你刚刚进行了 `eject`。例如，我们的大多数命令应该工作得完全一样。例如，如果我运行 `yarn test`，我应该仍然得到一个完整的 `Test Suite` 运行并通过：

```js
$ yarn test
yarn run v1.12.3
node scripts/test.js

PASS src/NewTodo/NewTodo.test.js
PASS src/TodoList/TodoList.test.js
PASS src/Todo/Todo.test.js
PASS src/App/App.test.js

Test Suites: 4 passed, 4 total
Tests: 21 passed, 21 total
Snapshots: 3 passed, 3 total
Time: 5.939s
Ran all test suites.

Watch Usage: Press w to show more.
```

同样，如果我运行 `yarn start`，我应该能够期待以与我之前相同的方式使用我的 React 项目：

```js
$ yarn run v1.12.3
node scripts/start.js
```

我们的浏览器应该仍然会启动并自动打开到`http://localhost:3000/`。我们还可以继续运行我们自己的后端模拟服务器，请求会被适当代理！正如您所看到的，Create React App 团队已经尽其所能使`eject`过程尽可能无痛和无缝。我们甚至还可以使用`yarn build`来构建生产环境。

我们还可以看到为我们创建的配置文件，基于 Create React App 如何构建其项目。例如，我们可以看到 Webpack 配置在这些文件中：

```js
Adding /config/webpack.config.dev.js to the project
Adding /config/webpack.config.prod.js to the project
```

如果您想以某种自定义方式调整您的`config`，这就是您需要操作的地方，您可以根据他们在这里填充的极其详尽的文件来构建您的配置。

您也可以通过查看`scripts`文件夹中的`scripts/test.js`以及位于`config/`目录下的 Jest 特定配置文件，来了解他们是如何无缝设置 Jest 的。

# 使用 eject 的缺点

记住，这并非免费提供，也没有立即将项目 eject 的理由。首先，当 Create React App 团队更新 Create React App 的脚本时，您将不会获得任何潜在的时间节省更改或生产力提升器。如果出现问题，如果某些东西工作不正常，或者在其他情况下，您实际上将无处寻求支持。

`eject`命令就像购买现成的东西。它可能不再有任何附加条件，但它也没有任何支持。买家需谨慎！

# 摘要

就这些了！在这个阶段，您应该已经对 Create React App 2 的变更和好处有了稳固的理解。我们已经探索了新旧功能，以确保我们能够构建尽可能最新、最现代的 JavaScript 实现！我们正在使用我们在可能构建的大多数前端项目中已经会使用的库和技术。

我们还深入研究了如何使用 Create React App 设置健康的软件开发生命周期，使我们的应用程序在发生更改时运行顺畅且具有弹性。我们的应用程序经过良好的彻底测试，并且不易随机崩溃。这一切都是我们在没有任何配置的情况下完成的；在我看来，这是在 Create React App 中与前端开发项目合作的最大优势之一！

我们还把许多其他非特定的 Create React App 解决方案整合到了我们的代码中，无论是通过 CSS 模块或 SASS 提供更好的 CSS 支持，还是通过服务库提供额外的库和巧妙的抽象。我们努力以智能的方式编写项目代码，以便于轻松重用和扩展，同时不会使我们的项目变得晦涩或让其他开发者难以贡献。我们还代理了一个后端 API，这样我们就可以与后端开发者合作，向他们展示前端使用的接口和数据语言，减少多个开发团队和理念之间的摩擦和来回沟通！

最后，你已经看到了当我们超越 Create React App 的安全边界，将其应用推出到标准的 Webpack 配置时，我们的选项会变成什么样子！我们的应用依然存在，并且我们可以配置新的和令人兴奋的项目功能，而不会束缚我们自己或我们的开发团队！

我希望你已经学到了很多关于如何真正开始使用 Create React App（以及通过代理，React 本身）的知识。非常感谢你陪我走过了这段旅程，我迫不及待地想看看你在那里能创造出什么！
