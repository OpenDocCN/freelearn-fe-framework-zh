# 准备开发环境

在使用 MERN 堆栈构建应用程序之前，我们首先需要为每种技术以及辅助开发和调试的工具准备开发环境。使用此堆栈需要您使不同的技术和工具协同工作，鉴于有关此主题的许多选项和资源，弄清楚这一切如何结合在一起可能是一项艰巨的任务。本章将指导您了解工作空间选项、基本开发工具、如何在您的工空间中设置 MERN 技术，以及如何通过实际代码检查此设置。

我们将涵盖以下主题：

+   选择开发工具

+   设置 MERN 堆栈技术

+   检查您的开发设置

# 选择开发工具

在选择基本开发工具时，如文本编辑器或 IDE、版本控制软件，甚至开发工作空间本身时，有很多选项可供选择。在本节中，我们将讨论与 MERN 堆栈相关的选项和建议，以便您可以根据个人偏好做出明智的选择。

# 工作空间选项

在本地机器上进行开发是程序员中的一种常见做法，但随着良好的云端和远程开发服务的出现，例如 AWS Cloud9 ([`aws.amazon.com/cloud9/?origin=c9io`](https://aws.amazon.com/cloud9/?origin=c9io)) 和 Visual Studio Code 的远程开发扩展 ([`code.visualstudio.com/docs/remote`](https://code.visualstudio.com/docs/remote))，您可以使用 MERN 技术设置您的本地工作空间（本书余下部分将假设这种情况），但您也可以选择在为 Node 开发配备的云端服务中运行和开发代码。

# 本地与云端开发

您可以选择同时使用这两种类型的工作空间，以享受在本地工作的好处，无需担心带宽/互联网问题，并在您没有物理上拥有您最喜欢的本地机器时远程工作。为此，您可以使用 Git 进行版本控制您的代码，将最新的代码存储在远程 Git 托管服务（如 GitHub 或 BitBucket）上，然后在所有工作空间中共享相同的代码。在您的工空间中，您可以从许多可用的选项中选择您喜欢的 IDE 来编写代码，其中一些将在下文中讨论。

# IDE 或文本编辑器

大多数云端开发环境都会集成源代码编辑器，但针对您的本地工作空间，您可以根据自己的编程偏好选择任何一种，然后对其进行定制以适应 MERN 开发。例如，以下流行的选项可以根据需要定制：

+   **Visual Studio Code** ([`code.visualstudio.com/`](https://code.visualstudio.com/))：Microsoft 开发的特性丰富的源代码编辑器，广泛支持现代网络应用程序开发工作流程，包括对 MERN 堆栈技术的支持

+   **Atom** ([`atom.io/`](https://atom.io/))：一个免费的、开源的文本编辑器，由 GitHub 提供，有许多其他开发者提供的与 MERN 堆栈相关的包

+   **SublimeText** ([`www.sublimetext.com/`](https://www.sublimetext.com/))：一个专有、跨平台的文本编辑器，也提供了许多与 MERN 堆栈相关的包，以及 JavaScript 开发支持

+   **WebStorm** ([`www.jetbrains.com/webstorm/`](https://www.jetbrains.com/webstorm/))：JetBrains 开发的完整 JavaScript IDE，支持 MERN 堆栈开发

您还有其他可用的编辑器，但除了关注每个编辑器能提供什么之外，选择一个适合您、能够实现高效工作流程并且与其他必要的网络应用程序开发工具良好集成的编辑器同样重要。

# Chrome 开发者工具

加载、查看和调试前端是网络开发过程中的一个非常重要的部分。Chrome DevTools（[`developers.google.com/web/tools/chrome-devtools`](https://developers.google.com/web/tools/chrome-devtools)），它是 Chrome 浏览器的一部分，拥有许多出色的功能，允许调试、测试和实验前端代码以及 UI 的外观、感觉、响应性和性能。此外，React Developer Tools 扩展作为 Chrome 插件提供，它为 Chrome DevTools 添加了 React 调试工具。

在您的开发工作流程中利用此类工具可以帮助您更好地理解代码，并有效地构建应用程序。同样，将代码版本控制与 Git 等工具集成可以提高您作为开发者的生产力和效率。

# Git

任何开发工作流程如果没有一个能够跟踪代码更改、代码共享和协作的版本控制系统都是不完整的。多年来，Git 已成为许多开发者的首选版本控制系统，并且是最广泛使用的分布式源代码管理工具。在本书的代码开发中，Git 将主要帮助我们跟踪在构建每个应用程序的步骤中的进度。

# 安装

要开始使用 Git，首先根据您的系统规格在本地机器或云开发环境中安装它。有关下载和安装 Git 最新版本的说明以及使用 Git 命令的文档，可以在[`git-scm.com/downloads`](https://git-scm.com/downloads)找到。

# 远程 Git 托管服务

基于云的 Git 仓库托管服务，如 GitHub 和 BitBucket，有助于在工作和部署环境中共享您的最新代码，并备份您的代码。这些服务包含了许多有用的功能，以帮助进行代码管理和开发工作流程。要开始使用，您可以创建一个账户并为您的代码库设置远程仓库。

所有这些基本工具都将帮助丰富您的 Web 开发工作流程并提高生产力。一旦您根据下一节中的讨论在您的空间中完成必要的设置，我们将继续前进并开始构建 MERN 应用程序。

# 设置 MERN 技术栈

随着本书的编写，MERN 技术栈正在开发和升级，因此对于本书中展示的工作，我们使用撰写本文时的最新稳定版本。这些技术的大多数安装指南取决于您的工作空间系统环境，因此本节指向所有相关的安装资源，并作为设置一个完全运行的 MERN 技术栈的指南。

# MongoDB

在将任何数据库功能添加到 MERN 应用程序之前，MongoDB 必须设置好、运行良好，并且对您的开发环境可访问。在撰写本文时，MongoDB 的当前稳定版本是 4.2.0，本书中的应用程序开发使用的是这个版本的 MongoDB 社区版。本节其余部分提供了有关如何安装和运行 MongoDB 的资源。

# 安装

在您可以使用 MongoDB 进行开发之前，您需要在您的空间中安装并启动 MongoDB。MongoDB 的安装和启动过程取决于您的工作空间规格：

+   云开发服务将有自己的安装和设置 MongoDB 的说明。例如，AWS Cloud9 的如何操作步骤可以在[`docs.c9.io/docs/setup-a-database#mongodb`](https://docs.c9.io/docs/setup-a-database#mongodb)找到。

+   您可以在[`docs.mongodb.com/manual/administration/install-community`](https://docs.mongodb.com/manual/administration/install-community)找到 MongoDB 在本地机器上的安装指南。

在您成功安装 MongoDB 到您的空间并使其运行后，您可以使用**mongo shell**与之交互。

# 运行 mongo shell

MongoDB shell 是 MongoDB 安装的一部分，是一个交互式工具，用于开始熟悉 MongoDB 操作。一旦 MongoDB 安装并运行，你就可以在命令行上运行 mongo shell。在 mongo shell 中，你可以使用命令查询和更新数据，并执行管理操作。

你也可以跳过 MongoDB 的本地安装，而是使用 MongoDB Atlas 在云中部署 MongoDB 数据库（[`www.mongodb.com/cloud/atlas`](https://www.mongodb.com/cloud/atlas)）。它是一个全球云数据库服务，可以用于向现代应用程序添加完全管理的 MongoDB 数据库。

MERN 开发的下一个核心组件是 Node.js，这是完成剩余的 MERN 设置所必需的。

# Node.js

MERN 应用程序的后端服务器实现依赖于 Node.js。在撰写本文时，13.12.0 是可用的最新稳定版 Node.js，书中代码也已在最新夜间构建的 14.0.0 版本上进行了测试。你选择的 Node.js 版本将捆绑 npm 作为包管理器。根据你选择 npm 或 Yarn 作为 MERN 项目的包管理器，你可以带或不带 npm 安装 Node.js。

# 安装

Node.js 可以通过直接下载、安装程序或 Node 版本管理器进行安装：

+   你可以通过直接下载源代码或针对你的工作空间平台预构建的安装程序来安装 Node.js。下载可在[nodejs.org/en/download](https://nodejs.org/en/download/)找到。

+   云开发服务可能预装了 Node.js，例如 AWS Cloud9 就是这样，或者会有添加和更新它的具体说明。

要测试安装是否成功，你可以打开命令行并运行`node -v`以查看它是否正确返回版本号。

# 使用 nvm 进行 Node 版本管理

如果你需要为不同的项目维护多个版本的 Node.js 和 npm，nvm 是一个有用的命令行工具，可以在同一工作空间上安装和管理不同版本。你必须单独安装 nvm。设置说明可以在[github.com/creationix/nvm](https://github.com/creationix/nvm)找到。

在你的系统上设置 Node.js 后，你可以使用 npm 或 Yarn 等包管理器开始集成 MERN 堆栈的其余部分。

# Yarn 包管理器

Yarn 是一个相对较新的 JavaScript 依赖项包管理器，可以用作 npm 的替代品。它是一个快速、可靠且安全的依赖项管理器，提供了一系列额外的功能，包括离线模式，可以在没有互联网连接的情况下重新安装包，并支持多个包注册表，如 npmjs.com 和 Bower。

我们将使用 Yarn（v1.22.4）来管理本书中项目的 Node 模块和包。在使用 Yarn 之前，您需要在您的开发空间中安装它。根据您的操作系统及其版本，安装 Yarn 有多种方法。

要了解更多关于在您的开发空间中安装 Yarn 的选项，请访问 [`yarnpkg.com/lang/en/docs/install`](https://yarnpkg.com/lang/en/docs/install)。

安装 Yarn 后，它可以用来添加其他必要的模块，包括 Express 和 React。

# MERN 模块

剩余的 MERN 堆栈技术都可作为 Node.js 包模块提供，并可以使用 Yarn 添加到每个项目中。这包括运行每个 MERN 应用程序所需的关键模块，如 React 和 Express，以及开发期间必要的模块。在本节中，我们列出并讨论这些模块，然后在下一节中查看如何在实际项目中使用这些模块。

# 关键模块

要集成 MERN 堆栈技术并运行您的应用程序，我们需要以下模块：

+   **React**：要开始使用 React，我们需要两个模块：

    +   `react`

    +   `react-dom`

+   **Express**：要在您的代码中使用 Express，您需要 `express` 模块。

+   **MongoDB**：要直接在 Node 应用程序中使用 MongoDB，您需要添加驱动程序，该驱动程序作为名为 `mongodb` 的 Node 模块提供。

这些关键模块将生成全栈 Web 应用程序，但我们需要一些额外的模块来帮助开发和应用代码的生成。

# 开发依赖模块

为了在整个 MERN 应用程序开发过程中保持一致性，我们将在客户端和服务器端实现中使用 ES6 和更高版本的新的 JavaScript 语法。因此，为了帮助开发过程，我们将使用以下附加模块来编译和打包代码，并在开发过程中代码更新时自动重新加载服务器和浏览器应用程序：

+   Babel 模块需要将 ES6 和 JSX 转换为适用于所有浏览器的合适 JavaScript。要使 Babel 工作所需的模块如下：

    +   `@babel/core`

    +   `babel-loader`

    +   用于使用 Webpack 转换 JavaScript 文件

    +   `@babel/preset-env` 和 `@babel/preset-react` 以提供对 React 和最新 JavaScript 特性的支持

+   Webpack 模块将帮助打包编译后的 JavaScript，包括客户端和服务器端代码。要使 Webpack 工作所需的模块如下：

+   +   `webpack`。

    +   `webpack-cli` 用于运行 Webpack 命令。

    +   `webpack-node-externals` 用于在 Webpack 打包时忽略外部 Node.js 模块文件。

    +   `webpack-dev-middleware` 用于在代码开发期间通过连接的服务器提供 Webpack 输出的文件。

    +   `webpack-hot-middleware` 通过将浏览器客户端连接到 Webpack 服务器并在开发过程中接收代码更改时的更新，将热模块重载添加到现有服务器中。

    +   `nodemon` 用于在开发期间监视服务器端更改，以便服务器可以重新加载以使更改生效。

    +   `react-hot-loader` 用于客户端上的快速开发。每当 React 前端中的文件发生变化时，`react-hot-loader` 都会启用浏览器应用程序更新，而无需重新捆绑整个前端代码。

    +   `@hot-loader/react-dom` 用于启用对 React hooks 的热重载支持。它本质上替换了相同版本的 `react-dom` 包，但增加了额外的补丁以支持热重载。

虽然 `react-hot-loader` 旨在帮助开发流程，但将其作为常规依赖项而不是开发依赖项安装是安全的。它将自动确保在生产环境中禁用热重载，并且占用空间最小。

在安装并准备好使用必要的 MERN 堆栈技术和相关工具后，在下一节中，我们将使用此工具集编写代码以确认您的开发空间是否正确设置以开始开发基于 MERN 的 Web 应用程序。

# 检查您的开发设置

在本节中，我们将通过开发工作流程，逐步编写代码以确保环境正确设置以开始开发和运行 MERN 应用程序。

我们将在以下文件夹结构中生成这些项目文件以运行一个简单的设置项目：

```js
| mern-simplesetup/
 | -- client/
  | --- HelloWorld.js
  | --- main.js
 | -- server/
  | --- devBundle.js
  | --- server.js
 | -- .babelrc
 | -- nodemon.json
 | -- package.json
 | -- template.js
 | -- webpack.config.client.js
 | -- webpack.config.client.production.js
 | -- webpack.config.server.js
```

本节中讨论的代码可在 GitHub 上的以下存储库中找到：[`github.com/PacktPublishing/Full-Stack-React-Projects-Second-Edition/tree/master/Chapter02/mern-simplesetup`](https://github.com/PacktPublishing/Full-Stack-React-Projects-Second-Edition/tree/master/Chapter02/mern-simplesetup)。您可以将此代码克隆并运行，同时您将通读本章其余部分的代码解释。

我们将配置文件留在根目录中，并将应用程序代码组织到客户端和服务器端文件夹中。`client` 文件夹将包含前端代码，而 `server` 文件夹将包含后端代码。在本节的其余部分，我们将生成这些文件，并实现前端和后端代码以构建一个可工作的全栈 Web 应用程序。

# 初始化 package.json 和安装 Node.js 模块

我们将首先使用 Yarn 安装所有必需的模块。在项目文件夹中添加 `package.json` 文件是一种最佳实践，以维护、记录和共享在 MERN 应用程序中使用的 Node.js 模块。`package.json` 文件将包含有关应用程序的元信息，并列出模块依赖项。

执行以下要点中概述的步骤以生成 `package.json` 文件，修改它，并使用它来安装模块：

+   `yarn init`：从命令行进入您的项目文件夹，并运行 `yarn init`。您将回答一系列问题以收集有关项目的元信息，例如名称、许可证和作者。然后，将自动生成一个包含您答案的 `package.json` 文件。

+   `dependencies`：在编辑器中打开 `package.json`，并修改 `JSON` 对象，添加键模块和 `react-hot-loader` 作为常规依赖项：

在代码块之前提到的文件路径表示代码在项目目录中的位置。本书中一直保持此约定，以便在跟随代码时提供更好的上下文和指导。

`mern-simplesetup/package.json`：

```js
"dependencies": {
    "@hot-loader/react-dom": "16.13.0",
    "express": "4.17.1",
    "mongodb": "3.5.5",
    "react": "16.13.1",
    "react-dom": "16.13.1",
    "react-hot-loader": "4.12.20"
  }
```

+   `devDependencies`：进一步修改 `package.json`，将开发期间所需的以下 Node 模块作为 `devDependencies` 添加：

`mern-simplesetup/package.json`：

```js
"devDependencies": {
    "@babel/core": "7.9.0",
    "@babel/preset-env": "7.9.0",
    "@babel/preset-react": "7.9.4",
    "babel-loader": "8.1.0",
    "nodemon": "2.0.2",
    "webpack": "4.42.1",
    "webpack-cli": "3.3.11",
    "webpack-dev-middleware": "3.7.2",
    "webpack-hot-middleware": "2.25.0",
    "webpack-node-externals": "1.7.2"
  }
```

+   yarn：保存 `package.json`，然后从命令行运行 `yarn` 命令以获取并添加所有这些模块到你的项目中。

在安装并添加所有必要的模块后，接下来我们将添加配置以编译和运行应用程序代码。

# 配置 Babel、Webpack 和 Nodemon

在我们开始编写网络应用程序代码之前，我们需要配置 Babel、Webpack 和 Nodemon 以在开发期间编译、捆绑和自动重新加载代码更改。

# Babel

在你的项目文件夹中创建一个 `.babelrc` 文件，并添加以下带有 `presets` 和 `plugins` 的 JSON，指定如下：

`mern-simplesetup/.babelrc`：

```js
{
  "presets": [
   ["@babel/preset-env",
    {
     "targets": {
      "node": "current"
     }
    }
   ],
   "@babel/preset-react"
  ],
  "plugins": [
   "react-hot-loader/babel"
  ]
}
```

在此配置中，我们指定需要 Babel 将最新的 JavaScript 语法进行转译，支持 Node.js 环境中的代码以及 React/JSX 代码。`react-hot-loader` 模块需要 `react-hot-loader/babel` 插件来编译 `React` 组件。

# Webpack

我们将不得不配置 Webpack 以捆绑客户端和服务器代码，并为生产环境单独捆绑客户端代码。在你的项目文件夹中创建 `webpack.config.client.js`、`webpack.config.server.js` 和 `webpack.config.client.production.js` 文件。所有三个文件都将具有以下代码结构，从导入开始，然后是 `config` 对象的定义，最后是定义的 `config` 对象的导出：

```js
const path = require('path')
const webpack = require('webpack')
const CURRENT_WORKING_DIR = process.cwd()

const config = { ... }

module.exports = config
```

`config` JSON 对象将根据客户端或服务器端代码以及开发与生产代码的特定值而有所不同。在以下各节中，我们将突出显示每个配置实例中的相关属性。

或者，你也可以使用交互式门户 *Generate Custom Webpack Configuration* 在 [`generatewebpackconfig.netlify.com/`](https://generatewebpackconfig.netlify.com/) 或使用命令行的 Webpack-cli 的 `init` 命令生成 Webpack 配置。

# 开发时的客户端 Webpack 配置

在你的 `webpack.config.client.js` 文件中更新 `config` 对象，以便配置 Webpack 在开发期间捆绑和热加载 React 代码：

`mern-simplesetup/webpack.config.client.js`：

```js
const config = {
   name: "browser",
   mode: "development",
   devtool: 'eval-source-map',
   entry: [
     'webpack-hot-middleware/client?reload=true',
     path.join(CURRENT_WORKING_DIR, 'client/main.js')
   ],
   output: {
     path: path.join(CURRENT_WORKING_DIR , '/dist'),
     filename: 'bundle.js',
     publicPath: '/dist/'
   },
   module: {
     rules: [
       {
         test: /\.jsx?$/,
         exclude: /node_modules/,
         use: ['babel-loader']
       }
     ]
   }, 
   plugins: [
     new webpack.HotModuleReplacementPlugin(),
     new webpack.NoEmitOnErrorsPlugin()
   ],
   resolve: {
     alias: {
       'react-dom': '@hot-loader/react-dom'
     }
   }
}
```

在 `config` 对象中突出显示的键和值将确定 Webpack 如何捆绑代码以及捆绑代码将放置的位置：

+   `mode`将`process.env.NODE_ENV`设置为给定的值，并告诉 Webpack 相应地使用其内置优化。如果没有明确设置，则默认为"`production`"值。也可以通过命令行传递 CLI 参数来设置。

+   `devtool`指定了源映射的生成方式，如果有的话。通常，源映射提供了一种将压缩文件内的代码映射回源文件原始位置的方法，以帮助调试。

+   `entry`指定了 Webpack 开始捆绑的入口文件，在本例中是`client`文件夹中的`main.js`文件。

+   `output`指定了捆绑代码的输出路径，在本例中设置为`dist/bundle.js`。

+   `publicPath`允许指定应用程序中所有资产的基准路径。

+   `module`设置用于转译的文件扩展名的正则表达式规则，以及要排除的文件夹。这里要使用的转译工具是`babel-loader`。

+   `HotModuleReplacementPlugin`启用了`react-hot-loader`的热模块替换功能。

+   `NoEmitOnErrorsPlugin`允许在编译错误时跳过输出。

+   我们还添加了一个 Webpack 别名，将`react-dom`引用指向`@hot-loader/react-dom`版本。

应用程序的客户端代码将从`bundle.js`中的捆绑代码在浏览器中加载。

Webpack 还提供了其他配置选项，可以根据你的代码和捆绑规范按需使用，正如我们将在探索服务器端特定捆绑时看到的。

# 服务器端 Webpack 配置

修改代码以要求`nodeExternals`，并在你的`webpack.config.server.js`文件中使用以下代码更新`config`对象，以配置 Webpack 进行服务器端代码的捆绑：

`mern-simplesetup/webpack.config.server.js`:

```js
const nodeExternals = require('webpack-node-externals')
const config = {
  name: "server",
  entry: [ path.join(CURRENT_WORKING_DIR , './server/server.js') ],
  target: "node",
  output: {
    path: path.join(CURRENT_WORKING_DIR , '/dist/'),
    filename: "server.generated.js",
    publicPath: '/dist/',
    libraryTarget: "commonjs2"
  },
  externals: [nodeExternals()],
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: [ 'babel-loader' ]
      }
    ]
  }
}
```

`mode`选项在此处未明确设置，但在运行 Webpack 命令时，根据是开发运行还是生产构建，将按需传递。

Webpack 从`server.js`所在的`server`文件夹开始捆绑，然后在`dist`文件夹中的`server.generated.js`中输出捆绑代码。在捆绑过程中，将假设 CommonJS 环境，因为我们已在`libraryTarget`中指定了`commonjs2`，因此输出将被分配给`module.exports`。

我们将使用生成的`server.generated.js`捆绑来运行服务器端代码。

# 生产环境下的客户端 Webpack 配置

为了准备客户端代码以用于生产，在你的`webpack.config.client.production.js`文件中使用以下代码更新`config`对象：

`mern-simplesetup/webpack.config.client.production.js`:

```js
const config = {
  mode: "production",
  entry: [
    path.join(CURRENT_WORKING_DIR, 'client/main.js')
  ],
  output: {
    path: path.join(CURRENT_WORKING_DIR , '/dist'),
    filename: 'bundle.js',
    publicPath: "/dist/"
  },
  module: {
    rules: [
      {
        test: /\.jsx?$/,
        exclude: /node_modules/,
        use: [
          'babel-loader'
        ]
      }
    ]
  }
}
```

这将配置 Webpack 捆绑用于生产模式的 React 代码。这里的配置与开发模式的客户端配置类似，但没有热重载插件和调试配置，因为在生产中不需要这些。

在设置好打包配置后，我们可以添加配置，以便在开发期间使用 Nodemon 自动运行这些生成的包，以响应代码更新。

# Nodemon

在你的项目文件夹中创建一个 `nodemon.json` 文件，并添加以下配置：

`mern-simplesetup/nodemon.json`:

```js
{
  "verbose": false,
  "watch": [ "./server" ],
  "exec": "webpack --mode=development --config 
      webpack.config.server.js 
        && node ./dist/server.generated.js"
}
```

此配置将设置 `nodemon` 在开发期间监视服务器文件中的更改，然后根据需要执行编译和构建命令。我们可以开始编写一个简单的全栈 Web 应用程序的代码，以查看这些配置的实际效果。

# 使用 React 的前端视图

为了开始开发前端，首先在项目文件夹中创建一个名为 `template.js` 的根模板文件，该文件将渲染带有 React 组件的 HTML：

`mern-simplesetup/template.js`:

```js
export default () => {
  return `<!doctype html>
   <html lang="en">
    <head>
     <meta charset="utf-8">
     <title>MERN Kickstart</title>
    </head>
    <body>
     <div id="root"></div>
     <script type="text/javascript" src="img/bundle.js"> 
    </script>
    </body>
   </html>` 
}
```

当服务器接收到对根 URL 的请求时，这个 HTML 模板将在浏览器中渲染，并且具有 ID `"root"` 的 `div` 元素将包含我们的 React 组件。

接下来，创建一个 `client` 文件夹，我们将在此文件夹中添加两个 React 文件，`main.js` 和 `HelloWorld.js`。

`main.js` 文件简单地渲染 HTML 文档中的 `div` 元素顶层的入口 React 组件：

`mern-simplesetup/client/main.js`:

```js
import React from 'react'
import { render } from 'react-dom'
import HelloWorld from './HelloWorld'

render(<HelloWorld/>, document.getElementById('root'))
```

在这种情况下，入口 React 组件是从 `HelloWorld.js` 导入的 `HelloWorld` 组件：

`mern-simplesetup/client/HelloWorld.js`:

```js
import React from 'react'
import { hot } from 'react-hot-loader'

const HelloWorld = () => {
  return (
    <div>
     <h1>Hello World!</h1>
    </div>
   )
}

export default hot(module)(HelloWorld) 
```

`HelloWorld.js` 包含一个基本的 `HelloWorld` React 组件，它通过 `react-hot-loader` 在开发期间启用热重载进行热导出。

要在服务器接收到对根 URL 的请求时在浏览器中看到渲染的 React 组件，我们需要使用 Webpack 和 Babel 设置来编译和打包此代码，并添加响应根路由请求的打包代码的服务器端代码。我们将在下一节中实现此服务器端代码。

# 使用 Express 和 Node 的服务器

在项目文件夹中，创建一个名为 `server` 的文件夹，并添加一个名为 `server.js` 的文件，该文件将设置服务器。然后，添加另一个名为 `devBundle.js` 的文件，该文件将帮助在开发模式下使用 Webpack 配置编译 React 代码。在接下来的章节中，我们将实现 Node-Express 应用程序，该应用程序启动客户端代码打包，启动服务器，设置路径以向客户端提供静态资产，并在对根路由发起 `GET` 请求时在模板中渲染 React 视图。

# Express 应用程序

在 `server.js` 中，我们首先将添加代码以导入 `express` 模块，以便初始化一个 Express 应用程序：

`mern-simplesetup/server/server.js`:

```js
import express from 'express'

const app = express()
```

然后，我们将使用这个 Express 应用程序来构建 Node 服务器应用程序的其余部分。

# 在开发期间打包 React 应用程序

为了保持开发流程简单，我们将初始化 Webpack，在服务器运行时编译客户端代码。在`devBundle.js`中，我们将设置一个`compile`方法，该方法接受 Express 应用并配置它使用 Webpack 中间件来编译、打包和提供代码，以及在开发模式下启用热重载：

`mern-simplesetup/server/devBundle.js`:

```js
import webpack from 'webpack'
import webpackMiddleware from 'webpack-dev-middleware'
import webpackHotMiddleware from 'webpack-hot-middleware'
import webpackConfig from './../webpack.config.client.js'

const compile = (app) => {
 if(process.env.NODE_ENV == "development"){
  const compiler = webpack(webpackConfig)
  const middleware = webpackMiddleware(compiler, {
   publicPath: webpackConfig.output.publicPath
  })
  app.use(middleware)
  app.use(webpackHotMiddleware(compiler))
 }
}

export default {
 compile
}
```

我们将在开发模式下通过添加以下行在`server.js`中调用此编译方法：

`mern-simplesetup/server/server.js`:

```js
import devBundle from './devBundle'
const app = express()
devBundle.compile(app)

```

这两条高亮的行仅用于开发模式，当构建用于生产的应用程序代码时应将其注释掉。在开发模式下，当这些行执行时，Webpack 将编译并打包 React 代码，将其放置在`dist/bundle.js`中。

# 从 dist 文件夹提供静态文件

Webpack 将在开发和生产模式下编译客户端代码，然后将打包的文件放置在`dist`文件夹中。为了使这些静态文件对客户端请求可用，我们将在`server.js`中添加以下代码，以从`dist`文件夹中提供静态文件：

`mern-simplesetup/server/server.js`:

```js
import path from 'path'
const CURRENT_WORKING_DIR = process.cwd()
app.use('/dist', express.static(path.join(CURRENT_WORKING_DIR, 'dist')))
```

这将配置 Express 应用，当请求的路由以`/dist`开头时，从`dist`文件夹返回静态文件。

# 在根目录渲染模板

当服务器在根 URL `/` 接收到请求时，我们将在浏览器中渲染`template.js`。在`server.js`中，向 Express 应用添加以下路由处理代码以接收`/`的`GET`请求：

`mern-simplesetup/server/server.js`:

```js
import template from './../template'
app.get('/', (req, res) => {
   res.status(200).send(template())
})
```

最后，配置 Express 应用以启动一个监听指定端口的请求的服务器：

`mern-simplesetup/server/server.js`:

```js
let port = process.env.PORT || 3000
app.listen(port, function onStart(err) {
 if (err) {
  console.log(err) 
 }
 console.info('Server started on port %s.', port)
})
```

使用此代码，当服务器运行时，它将能够接受根路由的请求并在浏览器中渲染带有“Hello World”文本的 React 视图。这个全栈实现中唯一缺少的部分是与数据库的连接，我们将在下一节中添加。

# 将服务器连接到 MongoDB

为了将你的 Node 服务器连接到 MongoDB，请将以下代码添加到`server.js`中，并确保你在工作区中运行 MongoDB 或者你有云 MongoDB 数据库实例的 URL：

`mern-simplesetup/server/server.js`:

```js
import { MongoClient } from 'mongodb'
const url = process.env.MONGODB_URI ||
  'mongodb://localhost:27017/mernSimpleSetup'
MongoClient.connect(url, (err, db)=>{
  console.log("Connected successfully to mongodb server")
 db.close()
})
```

在此代码示例中，`MongoClient`是使用其 URL 连接到运行中的 MongoDB 实例的驱动程序。它允许我们在后端实现数据库相关代码。这完成了使用 MERN 设置的这个简单 Web 应用程序的全栈集成，最终我们可以运行此代码以查看应用程序实时工作。

# 运行脚本

为了运行应用程序，我们将更新`package.json`文件，为开发和生产添加以下运行脚本：

`mern-simplesetup/package.json`:

```js
"scripts": {
  "development": "nodemon",
  "build": "webpack --config webpack.config.client.production.js 
    && webpack --mode=production --config 
      webpack.config.server.js",
  "start": "NODE_ENV=production node ./dist/server.generated.js"
} 
```

让我们看看代码：

+   `yarn development`：此命令将启动 Nodemon、Webpack 和服务器以进行开发。

+   `yarn build`：这将生成生产模式下的客户端和服务器代码包（在运行此脚本之前，请确保从`server.js`中删除`devBundle.compile`代码）。

+   `yarn start`：此命令将在生产中运行打包后的代码。

您可以使用以下命令来运行应用程序，无论是您在开发应用程序时进行调试，还是当应用程序准备在生产环境中上线时。

# 实时开发和调试

要运行到目前为止开发的代码，并确保一切正常工作，您可以按照以下步骤进行：

1.  **从命令行运行应用程序**：`yarn development`。

1.  **在浏览器中加载**：在浏览器中打开根 URL，如果您使用的是本地机器，则为`http://localhost:3000`。您应该看到一个标题为 MERN Kickstart 的页面，该页面仅显示 Hello World!。

1.  **实时开发代码和调试**：将`HelloWorld.js`组件的文本从`"Hello World!"`更改为仅`"hello"`。保存更改后，您可以在浏览器中看到即时更新，并且还可以检查命令行输出以确认`bundle.js`没有被重新创建。同样，当您更改服务器端代码时，您也可以看到即时更新，这可以在开发过程中提高生产力。

如果您已经走到这一步，恭喜您！您已经准备好开始开发令人兴奋的 MERN 应用程序了。

# 摘要

在本章中，我们讨论了开发工具选项以及如何安装 MERN 技术，然后我们编写了代码来检查开发环境是否正确设置。

我们首先查看适合 Web 开发的推荐工作区、IDE、版本控制软件和浏览器选项。您可以根据作为开发者的个人喜好从中选择。

接下来，我们首先安装 MongoDB、Node 和 Yarn，然后使用 Yarn 添加剩余所需库，从而设置 MERN 堆栈技术。

在编写代码以检查此设置之前，我们已配置 Webpack 和 Babel 在开发期间编译和打包代码，并构建生产就绪代码。我们了解到，在将应用程序打开到浏览器之前，有必要编译用于开发 MERN 应用程序的 ES6 和 JSX 代码。

此外，我们还通过包括 React Hot Loader 用于前端开发、配置 Nodemon 用于后端开发，以及在开发期间运行服务器时使用一条命令编译客户端和服务器代码，使开发流程更高效。

在下一章中，我们将使用这个设置开始构建一个作为全功能应用程序基础的骨架 MERN 应用程序。
