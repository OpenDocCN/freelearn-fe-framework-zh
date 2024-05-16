添加服务器端框架

在本章中，您将学习如何配置 Nuxt 与服务器端框架，以及如何使用`asyncData`方法从服务器端框架（如 Koa 或 Express）获取数据。使用 Nuxt 设置服务器端框架相当容易。我们只需要选择一个框架作为一等公民，并将 Nuxt 用作中间件。我们可以使用`npx create-nuxt-app <project-name>`来为我们设置，但我们将手把手地教您如何手动操作，以便更好地理解这两个应用是如何协同工作的。此外，在本章中，我们将使用**Backpack**作为我们应用的构建系统。

本章我们将涵盖以下主题：

+   介绍背包

+   介绍 Koa

+   将 Koa 与 Nuxt 集成

+   理解异步数据

+   在 asyncData 中访问上下文

+   使用 Axios 获取异步数据

# 第八章：介绍背包

Backpack 是一个用于构建现代 Node.js 应用的构建系统，零配置或最小配置。它支持最新的 JavaScript，并处理文件监视、实时重新加载、转译和打包，这些都是我们在前几章中使用 webpack 进行的操作。我们可以将其视为 webpack 的**包装器**，是我们在本书中迄今为止一直在使用的 webpack 配置的简化版本。您可以在[`github.com/jaredpalmer/backpack`](https://github.com/jaredpalmer/backpack)找到有关 Backpack 的更多信息。现在，让我们看看如何在接下来的章节中使用它来加快我们的应用开发。

## 安装和配置 Backpack

使用 Backpack 创建现代 Node.js 应用可以像实现以下步骤一样简单：

1.  通过 npm 安装 Backpack：

```js
$ npm i backpack-core
```

1.  在项目根目录中创建一个`/src/`目录和一个`package.json`文件，并在`dev`脚本中添加`backpack`，如下所示：

```js
{
  "scripts": {
    "dev": "backpack"
  }
}
```

请注意，您必须将`/src/`作为应用的**默认入口目录**。

1.  在项目根目录创建一个 Backpack 配置文件，并配置 webpack 的函数如下：

```js
// backpack.config.js
module.exports = {
  webpack: (config, options, webpack) => {
    // ....
    return config
  }
}
```

这一步是可选的，但如果您想要将应用的默认入口目录（即您在*步骤 2*中创建的`/src/`目录）更改为其他目录，例如`/server/`目录，可以按以下方式进行：

```js
webpack: (config, options, webpack) => {
  config.entry.main = './server/index.js'
  return config
}
```

1.  使用以下命令以开发模式启动您的应用：

```js
$ npm run dev
```

然后你可以在`/server/`目录中开发你的应用程序的源代码，并在浏览器上浏览到你设置的任何端口的应用程序。让我们在下一节中使用 Backpack 创建一个简单的 Express 应用程序。

## 使用 Backpack 创建一个简单的应用程序

使用 Backpack 创建一个 Express 应用程序可以像实现以下步骤一样简单：

1.  通过 npm 安装 Express：

```js
$ npm i express
```

1.  在`package.json`文件的`dev`脚本之后添加`build`和`start`脚本：

```js
// package.json
"scripts": {
  "dev": "backpack",
  "build": "backpack build",
  "start": "cross-env NODE_ENV=production node build/main.js"
}
```

1.  创建 Backpack 配置文件，并将`/server/`作为应用程序的入口文件夹，就像我们在上一节中向你展示的那样：

```js
// backpack.config.js
module.exports = {
  webpack: (config, options, webpack) => {
    config.entry.main = './server/index.js'
    return config
  }
}
```

1.  创建一个带有`'Hello World'`消息的简单路由：

```js
// server/index.js
import express from 'express'
const app = express()
const port = 3000

app.get('/', (req, res) =>
  res.send('Hello World')
)

app.listen(port, () =>
  console.log(Example app listening on port ${port}!)
)
```

1.  在开发模式下运行你的应用程序：

```js
$ npm run dev
```

现在你可以在浏览器上浏览`127.0.0.1:3000`上的应用程序。你应该在屏幕上看到 Hello World。你可以在我们的 GitHub 存储库的`/chapter-8/backpack/`中找到这个例子。接下来，让我们在下一节中使用 Koa 作为服务器端框架，允许我们以比 Express 更少的行数编写 ES2015 代码和异步函数。

# 介绍 Koa

Koa 是由带给你 Express 的同一个团队设计的 Node.js web 框架。该框架的主要目标是成为 Web 应用程序和 API 的更小、更具表现力的基础。如果你曾经在 Express 上工作过，并且在应用程序变得更大时厌倦了回调地狱，Koa 允许你摆脱回调，并通过利用异步函数大大增加错误处理。Koa 中另一个很酷的东西是**级联** - 你添加的中间件将会“下游”运行，然后再“上游”流动，这给你更可预测的控制。我们稍后将在本章中演示这一点。

如果你想了解更多关于 Koa 的信息，请访问[`koajs.com/`](https://koajs.com/)。

## 安装和配置 Koa

现在，让我们创建一个 Koa 应用程序，使用 Backpack 的默认配置（不创建 Backpack 配置文件），如下所示：

1.  通过 npm 安装 Koa：

```js
$ npm i koa
```

1.  使用`/src/`作为 Backpack 的默认入口目录，并在该目录中创建一个以 Koa 风格的最小代码的入口文件，如下所示：

```js
// src/index.js
const Koa = require('koa')
const app = new Koa()

app.use(async ctx => {
  ctx.body = 'Hello World'
})
app.listen(3000)
```

1.  在开发模式下运行 Koa 应用程序：

```js
$ npm run dev
```

当在浏览器上浏览`127.0.0.1:3000`时，你应该在屏幕上看到 Hello World。如果你一直在使用 Express 来创建你的 Node.js 应用程序，你会发现 Koa 是一个可以用来以更整洁的代码做同样事情的替代方案。接下来，让我们在接下来的章节中学习 Koa 上下文是什么，以及 Koa 中级联是如何工作的。

## ctx 是什么？

您可能想知道在我们在上一节中创建的最小代码中，`ctx`是什么，以及`req`和`res`对象在哪里，因为它们在 Express 应用程序中存在。它们在 Koa 中并没有消失。它们只是封装在 Koa 中的一个单一对象中，这就是 Koa 上下文，称为`ctx`。我们可以按如下方式访问`request`和`response`对象：

```js
app.use(async ctx => {
  ctx.request
  ctx.response
})
```

因此，您可以看到我们可以轻松使用`ctx.request`来访问 Node.js 的`request`对象，以及`ctx.response`来访问 Node.js 的`response`对象。这两个重要的 HTTP 对象在 Koa 中并没有消失！它们只是隐藏在 Koa 上下文 - `ctx`中。接下来，让我们在下一节中了解 Koa 中级联的工作原理。

## 了解 Koa 中级联的工作原理

简而言之，Koa 中的级联工作是通过按顺序调用下游中间件，然后控制它们按顺序向上游流动。最好创建一个简单的 Koa 应用程序来演示 Koa 中的这一重要特性：

1.  在`/src/`目录中创建一个`index.js`文件，就像我们在上一节中所做的那样：

```js
// src/index.js
const Koa = require('koa')
const app = new Koa()

app.use(async ctx => {
  console.log('Hello World')
  ctx.body = 'Hello World'
})
app.listen(3000)
```

1.  在`Hello World`中间件之前创建三个中间件，以便我们可以先运行它们：

```js
app.use(async (ctx, next) => {
  console.log('Time started at: ', Date.now())
  await next()
})

app.use(async (ctx, next) => {
  console.log('I am the first')
  await next()
  console.log('I am the last')
})

app.use(async (ctx, next) => {
  console.log('I am the second')
  await next()
  console.log('I am the third')
})
```

1.  在开发模式下运行应用程序，您应该在终端上获得以下输出：

```js
Time started at: 1554647742894
I am the first
I am the second
Hello World
I am the third
I am the last
```

在这个演示中，请求通过`Time started at:`流向`I am the first`，`I am the second`，并到达`Hello World`。当没有更多的中间件需要向下执行（下游）时，每个中间件将按以下顺序向上解开并恢复（上游）：`I am the third`，`I am the last`。

您可以在我们的 GitHub 存储库的`/chapter-8/koa/cascading/`中找到这个示例。

接下来，我们将为您介绍一些依赖项，您应该安装这些依赖项来开发一个全栈 Koa 应用程序，使其可以像 Express 应用程序一样工作。

## 安装 Koa 应用程序的依赖项

Koa 是极简的。它本质上是一个基本框架。因此，它的核心中没有任何中间件。Express 自带路由器，默认情况下，Koa 没有。这在使用 Koa 编写应用程序时可能会有挑战，因为你需要选择一个第三方包或从它们的 GitHub 主页上列出的包中选择一个。你可能会尝试一些包，发现它们不符合你的要求。有一些 Koa 包可用于路由；`koa-router`在本书中被广泛使用，以及其他用于使用 Koa 开发 API 的基本依赖项。让我们通过安装它们并创建一个骨架应用程序来发现它们是什么以及它们的作用，如下所示：

1.  安装`koa-router`模块并使用如下：

```js
$ npm i koa-router
```

在入口文件中导入`koa-router`，并创建一个主页路由`/`，如下所示：

```js
// src/index.js
const Router = require('koa-router')
const router = new Router()

router.get('/', (ctx, next) => {
  ctx.body = 'Hello World'
})

app
  .use(router.routes())
  .use(router.allowedMethods())
```

你可以在 Koa 的 GitHub 存储库中找到有关此中间件的更多信息。此模块是从`ZijianHe/koa-router`（https://github.com/ZijianHe/koa-router）分叉而来。它是 Koa 社区中最广泛使用的路由器模块。它提供了使用`app.get`、`app.put`、`app.post`等的 Express 风格路由。它还支持其他重要功能，如多个路由中间件和多个可嵌套的路由器。

1.  安装`koa-bodyparser`模块并使用如下：

```js
$ npm i koa-bodyparser
```

在入口文件中导入`koa-bodyparser`，注册它，并创建一个主页路由`/post`，如下所示：

```js
// src/index.js
const bodyParser = require('koa-bodyparser')
app.use(bodyParser())

router.post('/post', (ctx, next) => {
  ctx.body = ctx.request.body
})
```

你可以在 Koa 的 GitHub 存储库中找到有关此中间件的更多信息。也许你会想：什么是 body parser？当我们处理 HTML 表单时，我们使用`application/x-www-form-urlencoding`或`multipart/form-data`在客户端和服务器端之间传输数据，例如：

```js
// application/x-www-form-urlencoding
<form action="/update" method="post">
  //...
</form>

// multipart/form-data
<form action="/update" method="post" encrypt="multipart/form-data">
  //...
</form>
```

HTML 表单的默认类型是`application/x-www-urlencoded`，如果我们想要读取 HTTP `POST`、`PATCH`和`PUT`的数据，我们使用一个 body parser，它是一个解析传入请求的中间件，组装包含表单数据的**块**，然后创建一个填充有表单数据的 body 对象，以便我们可以从请求对象中的`ctx`对象中访问它们，如下所示：

```js
ctx.body = ctx.request.body
```

1.  安装`koa-favicon`模块并使用如下：

```js
$ npm i koa-favicon
```

在入口文件中导入`koa-favicon`并注册它，路径为`favicon`，如下所示：

```js
// src/index.js
const favicon = require('koa-favicon')
app.use(favicon('public/favicon.ico'))
```

您可以在 Koa 的 GitHub 存储库中的[`github.com/koajs/favicon`](https://github.com/koajs/favicon)找到有关此中间件的更多信息。这是一个提供`favicon`的中间件，因此让我们创建一个`favicon.ico`文件并将其保存在项目根目录中的`/public`文件夹中。当您刷新主页时，您应该在浏览器标签上看到`favicon`。

1.  安装`koa-static`模块并按以下方式使用它：

```js
$ npm i koa-static
```

在入口文件中导入`koa-static`并按照以下路径进行注册：

```js
const serve = require('koa-static')
app.use(serve('.'))
app.use(serve('static/fixtures'))
```

您可以在 Koa 的 GitHub 存储库中的[`github.com/koajs/static`](https://github.com/koajs/static)找到有关此中间件的更多信息。默认情况下，Koa 不允许您提供静态文件。因此，此中间件将允许您从 API 中提供静态文件。例如，我们刚刚设置的路径将允许我们从项目根目录中的`/static`文件夹访问以下文件：

+   在`127.0.0.1:3000/package.json`处获取`/package.json`。

+   在`127.0.0.1:3000/hello.txt`处获取`/hello.txt`。

在未来的章节中，我们将在创建 Koa API 时使用这个框架。现在，让我们在下一节中发现如何将 Koa 与 Nuxt 集成。

您可以在我们的 GitHub 存储库的`/chapter-8/koa/skeleton/`中找到此框架应用。

# 将 Koa 与 Nuxt 集成

将 Koa 和 Nuxt 集成可以在单域应用程序的单个端口上完成，也可以在跨域应用程序的不同端口上完成。在本章中，我们将进行单域集成，然后我们将指导您完成第十二章中的跨域集成，*创建用户登录和 API 身份验证*。我们将使用在上一节中开发的 Koa 框架来进行这两种类型的集成。单域集成需要在以下步骤中进行一些配置。让我们开始吧：

1.  在 Nuxt 项目的根目录中创建一个`/server/`目录，并在使用`create-nuxt-app`脚手架工具创建项目后，按以下方式构建服务器端目录：

```js
├── package.json
├── nuxt.config.js
├── server
│ ├── config
│ │ └── ...
│ ├── public
│ │ └── ...
│ ├── static
│ │ └── ...
│ └── index.js
└── pages
    └── ...
```

1.  修改默认的`package.json`文件中的默认脚本以使用默认的`Backpack`，该文件与脚手架工具一起提供。

```js
// package.json
"scripts": {
  "dev": "backpack",
  "build": "nuxt build && backpack build",
  "start": "cross-env NODE_ENV=production node build/main.js",
  "generate": "nuxt generate"
}
```

1.  在根目录中创建一个 Backpack 配置文件（我们在其中有 Nuxt 配置文件），将 Backpack 默认的入口目录更改为我们刚刚创建的`/server/`目录：

```js
// backpack.config.js
module.exports = {
  webpack: (config, options, webpack) => {
    config.entry.main = './server/index.js'
    return config
  }
}
```

1.  在`/server/`目录中创建一个`index.js`文件，以以下方式将 Koa（确保您已经安装了 Koa）作为主应用程序导入，并将 Nuxt 作为 Koa 中的中间件：

```js
// server/index.js
import Koa from 'koa'
import consola from 'consola'
import { Nuxt, Builder } from 'nuxt'
const app = new Koa()
const nuxt = new Nuxt(config)

async function start() {
  app.use((ctx) => {
    ctx.status = 200
    ctx.respond = false
    ctx.req.ctx = ctx
    nuxt.render(ctx.req, ctx.res)
  })
}
start()
```

请注意，我们创建了一个异步函数来使用 Nuxt 作为中间件，以便在下一步中可以使用`await`语句来运行 Nuxt 构建过程。

请注意，Consola 是一个控制台记录器，您必须在使用之前通过 npm 安装它。有关此软件包的更多信息，请访问[`github.com/nuxt-contrib/consola`](https://github.com/nuxt-contrib/consola)。

1.  在将 Nuxt 注册为中间件之前，在开发模式下导入 Nuxt 构建过程的配置：

```js
// server/index.js
let config = require('../nuxt.config.js')
config.dev = !(app.env === 'production')

if (config.dev) {
  const builder = new Builder(nuxt)
  await builder.build()
} else {
  await nuxt.ready()
}
```

1.  通过监听其端口和主机来运行应用程序，并使用 Consola 记录服务器状态如下：

```js
app.listen(port, host)
consola.ready({
  message: `Server listening on http://${host}:${port}`,
  badge: true
})
```

1.  在开发模式下启动应用程序：

```js
$ npm run dev
```

我们的 Nuxt 和 Koa 应用现在作为一个单一应用程序运行。您可能已经意识到，Nuxt 现在作为中间件在 Koa 下运行。我们所有的 Nuxt 页面仍然像以前一样在`localhost:3000`上运行，但是我们将在接下来的部分中将`localhost:3000/api`配置为 API 的主要端点。

## 添加路由和其他必要的中间件

在上一节中，我们建立了集成并构建了服务器端目录结构。现在让我们在接下来的步骤中完善一些 API 路由和其他中间件：

1.  通过 npm 安装 Koa Router 和 Koa Static 包：

```js
$ npm i koa-route
$ npm i koa-static
```

1.  创建一个服务器端配置文件：

```js
// server/config/index.js
export default {
  static_dir: {
    root: '../static'
  }
}
```

1.  在`/server/`目录中创建一个`routes.js`文件，用于定义我们将向公众公开的路由，并附带一些虚拟用户数据：

```js
// server/routes.js
import Router from 'koa-router'
const router = new Router({ prefix: '/api' })

const users = [
  { id: 1, name: 'Alexandre' },
  { id: 2, name: 'Pooya' },
  { id: 3, name: 'Sébastien' }
]

router.get('/', async (ctx, next) => {
  ctx.type = 'json'
  ctx.body = {
    message: 'Hello World!'
  }
})

router.get('/users', async (ctx, next) => {
  ctx.type = 'json'
  ctx.body = users
})

router.get('/users/:id', async (ctx, next) => {
  const id = parseInt(ctx.params.id)
  const found = users.find(function (user) {
    return user.id == id
  })
  if (found) {
    ctx.body = found
  } else {
    ctx.throw(404, 'user not found')
  }
})
```

1.  在单独的`middlewares.js`文件中导入其他中间件，并从*步骤 1*和*2*中导入路由和配置文件：

```js
// server/middlewares.js
import serve from 'koa-static'
import bodyParser from 'koa-bodyparser'
import config from './config'
import routes from './routes'

export default (app) => {
  app.use(serve(config.static_dir.root))
  app.use(bodyParser())
  app.use(routes.routes(), routes.allowedMethods())
}
```

我们不会在 API 中使用`koa-favicon`，因为我们以 JSON 格式导出数据，而`favicon.ico`的图像不会显示在浏览器标签上。此外，Nuxt 已经在 Nuxt 配置文件中为我们处理了`favicon.ico`，因此我们可以从骨架中删除`koa-favicon`中间件。相反，我们将创建一个中间件来将我们的 JSON 数据装饰成这两个最终的 JSON 输出

+   200 输出的格式：

```js
{"status":<status code>,"data":<data>}
```

+   所有错误输出的格式（例如 400，500）：

```js
{"status":<status code>,"message":<error message>}
```

1.  在`app.use(serve(config.static_dir.root))`行之前添加以下代码以创建前述格式：

```js
app.use(async (ctx, next) => {
  try {
    await next()
    if (ctx.status === 404) {
      ctx.throw(404)
    }
    if (ctx.status === 200) {
      ctx.body = {
        status: 200,
        data: ctx.body
      }
    }
  } catch (err) {
    ctx.status = err.status || 500
    ctx.type = 'json'
    ctx.body = {
      status: ctx.status,
      message: err.message
    }
    ctx.app.emit('error', err, ctx)
  }
})
```

因此，现在有了这个中间件，我们将不再获得诸如`{"message":"Hello World!"}`的输出，而是会得到以下装饰过的输出：

```js
{"status":200,"data":{"message":"Hello World!"}}
```

1.  在注册 Nuxt 之前，在主`index.js`文件中导入`middlewares.js`文件：

```js
// server/index.js
import middlewares from './middlewares'

middlewares(app)
app.use(ctx => {
  ...
  nuxt.render(ctx.req, ctx.res)
})
```

1.  以开发模式重新运行应用程序：

```js
$ npm run dev
```

1.  然后，如果您访问`localhost:3000/api`上的应用程序，您将在屏幕上获得以下输出：

```js
{"status":200,"data":{"message":"Hello World!"}}
```

如果您访问`localhost:3000/api/users`上的用户索引页面，您将在屏幕上获得以下输出：

```js
{"status":200,"data":[{"id":1,"name":"Alexandre"},{"id":2,"name":"Pooya"},{"id":3,"name":"Sébastien"}]}
```

您还可以使用`localhost:3000/api/users/<id>`来获取特定用户。例如，如果您使用`/api/users/1`，您将在屏幕上获得以下输出：

```js
{"status":200,"data":{"id":1,"name":"Alexandre"}}
```

您可以在我们的 GitHub 存储库的`/chapter-8/nuxt-universal/skeletons/koa/`中找到这个集成示例应用程序。

接下来，我们将看看如何在接下来的部分从 Nuxt 页面上的客户端使用`asyncData`方法请求前面的 API 数据。

# 理解异步数据

`asyncData`方法允许我们在组件初始化之前异步获取数据并在服务器端渲染它。这是一个额外的方法，只在 Nuxt 中可用。这意味着您不能在 Vue 中使用它，因为 Vue 没有这个默认方法。Nuxt 总是在渲染页面组件之前执行这个方法。当通过`<nuxt-link>`组件生成的路由重新访问该页面时，该方法将在服务器端的页面上执行一次，然后在客户端上执行。Nuxt 将从`asyncData`方法中返回的数据与`data`方法或`data`属性中的组件数据合并。该方法将`context`对象作为第一个参数，如下所示：

```js
export default {
  asyncData (context) {
    // ...
  }
}
```

请记住，这个方法总是在页面组件初始化之前执行，所以我们无法通过`this`关键字在这个方法内访问组件实例。有两种不同的使用方法；让我们在接下来的部分中探讨它们。

## 返回一个承诺

我们可以通过返回`Promise`在`asyncData`方法中使用`Promise`对象，例如：

```js
// pages/returning-promise.vue
asyncData (context) {
  const promise = new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve('Hello World by returning a Promise')
    }, 1000)
  })

  return promise.then((value) => {
    return { message: value }
  })
}
```

在前面的代码中，Nuxt 将等待 1 秒钟，直到承诺被解决，然后再使用'通过返回 Promise 来打招呼的 Hello World'渲染页面组件。

## 使用 async/await

我们还可以在`asyncData`方法中使用`async`/`await`语句，例如：

```js
// pages/using-async.vue
async asyncData (context) {
  const promise = new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve('Hello World by using async/await')
    }, 2000)
  })

  const result = await promise
  return { message: result }
}
```

在上述代码中，Nuxt 将等待 2 秒钟，直到承诺被解决，然后使用`'Hello World by using async/await'`消息呈现页面组件。使用`async`/`await`语句是编写异步 JavaScript 代码的新方法。它建立在`Promise`对象之上，并使我们的异步代码更易读。我们将在整本书中经常使用这个语句。

## 合并数据

正如我们之前提到的，`asyncData`方法中的异步数据将与`data`方法或`data`属性中的组件数据合并。这意味着如果您在组件数据中使用了与`asyncData`方法中相同对象键的一些默认数据，它们将被`asyncData`方法覆盖。以下是一个例子：

```js
// pages/merging-data.vue
<p>{{ message }}</p>

export default {
  data () {
    return { message: 'Hello World' }
  },
  asyncData (context) {
    return { message: 'Data Merged' }
  }
}
```

在上述代码中，Nuxt 将合并两组数据，并在屏幕上得到以下结果：

```js
<p>Data Merged</p>
```

您可以在我们的 GitHub 存储库的`/chapter-8/nuxt-universal/koa-nuxt/understanding-asyncdata/`中找到这些示例。

接下来，我们将看看如何在接下来的部分中从`asyncData`方法中访问`context`对象。

# 在 asyncData 中访问上下文

我们可以从 Nuxt 上下文中访问大量有用的数据。它们存储在上下文对象中，作为以下键：

|

+   应用

+   路由

+   存储

+   参数

+   查询

|

+   请求

+   res

+   重定向

+   错误

+   env

|

+   isDev

+   isHMR

+   beforeNuxtRender(fn)

+   来自

+   nuxtState

|

它们是额外提供的，特别是在 Nuxt 中，因此我们在 Vue 中找不到它们。我们可以使用`context.<key>`或`{ <key> }`来访问它们。让我们探索一些这些键，并看看我们如何在接下来的部分中利用它们。

有关 Nuxt 上下文的更多信息，请访问[`nuxtjs.org/api/context`](https://nuxtjs.org/api/context)。

## 访问 req/res 对象

当在服务器端执行`asyncData`方法时，我们可以访问`req`和`res`对象。它们包含用户发送的 HTTP 请求的有用信息。但在访问它们之前，我们应该始终检查`if`条件：

```js
// pages/index.vue
<p>{{ host }}</p>

export default {
  asyncData ({ req, res }) {
    if (process.server) {
     return { host: req.headers.host }
    }
    return { host: '' }
  }
}
```

在上述代码中，我们使用`if`条件来确保在获取请求头信息之前在服务器端调用`asyncData`方法。这两个对象在客户端不可用，因此在客户端访问它们时会得到`undefined`。因此，当页面在浏览器上首次加载时，我们将得到上述代码的结果`localhost:3000`，但是除非刷新该页面，否则当通过`<nuxt-link>`组件生成的路由重新访问此页面时，您将不会再看到该信息。

## 访问动态路由数据

当我们的应用程序中有动态路由时，我们可以通过`params`键访问动态路由数据。例如，如果我们在`/pages/`目录中有一个`_id.vue`文件，那么我们可以通过`context.params.id`来访问路由参数的值。

```js
// pages/users/_id.vue
<p>{{ id }}</p>

export default {
  asyncData ({ params }) {
    return { id: params.id }
  }
}
```

在上述代码中，当在浏览器上调用`users/1`时，`id`将得到`1`。

## 监听查询变化

默认情况下，`asyncData`方法不会在查询字符串更改时执行。例如，如果您在使用`<nuxt-link>`组件的路由上使用诸如`/users?id=<id>`的查询，那么当通过`<nuxt-link>`组件路由从一个查询更改到另一个查询时，`asyncData`将不会被调用。这是因为 Nuxt 默认禁用了查询更改的监听以提高性能。如果您想覆盖此默认行为，可以使用`watchQuery`属性来监听特定参数：

```js
// pages/users/index.vue
<p>{{ id }}</p>
<ul>
  <li>
    <nuxt-link :to="'users?id=1'">1</nuxt-link>
    <nuxt-link :to="'users?id=2'">2</nuxt-link>
  </li>
</ul>

export default {
  asyncData ({ query }) {
    return { id: query.id }
  },
  watchQuery: ['id']
}
```

在上述代码中，我们正在监听`id`参数，因此当导航到`/users?id=1`时，您将得到`1`，而当导航到`/users?id=2`时，您将得到`2`。如果您想为所有查询字符串设置一个观察器，只需将`watchQuery`设置为`true`。

## 处理错误

我们可以使用`context`对象中的`error`方法来调用 Nuxt 默认的错误页面并显示错误。您可以通过默认的`params.statusCode`和`params.message`属性传递错误代码和消息：

```js
// pages/users/error.vue
export default {
  asyncData ({ error }) {
    return error({
      statusCode: 404,
      message: 'User not found'
    })
  }
}
```

如果您想要更改传递给`error`方法的默认属性，您可以创建一个自定义错误页面，您在第四章中学到了如何创建自定义错误属性和布局。让我们按照以下步骤创建这些自定义错误属性和布局：

1.  创建一个要抛出自定义属性的页面：

```js
// pages/users/error-custom.vue
export default {
  asyncData ({ error }) {
    return error({
      status: 404,
      text: 'User not found'
    })
  }
}
```

1.  在`/layouts/`目录中创建一个自定义错误页面：

```js
// layouts/error.vue
<template>
  <div>
    <h1>Custom Error Page</h1>
    <h2>{{ error.status }} Error</h2>
    <p>{{ error.text }}</p>
    <nuxt-link to="/">Home page</nuxt-link>
  </div>
</template>

<script>
export default {
  props: ['error'],
  layout: 'layout-error'
}
</script>
```

1.  为这个错误页面创建一个自定义布局页面：

```js
// layouts/layout-error.vue
<template>
  <nuxt />
</template>
```

当访问`/users/error-custom`时，您应该看到自定义属性和布局。

您可以在我们的 GitHub 存储库的`/chapter-8/nuxt-universal/koa-nuxt/accessing-context/`中看到所有示例。

接下来，我们将看看如何在接下来的部分中使用 Axios，一个 HTTP 客户端，与`asyncData`方法一起请求 API 数据。

# 使用 Axios 获取异步数据

我们创建了一个简单的 API，使用 Koa 暴露了一些公共路由，用于访问其数据，比如`/api/users`和`/api/users/1`。我们还将这个 API 与 Nuxt 集成到一个单一的应用程序中，其中 Nuxt 充当中间件。您还学会了`asyncData`方法的工作原理以及如何利用 Nuxt 上下文。现在，让我们通过在请求 API 数据时使用 Axios 和`asyncData`方法将这三个部分整合在一起。

## 安装和配置 Axios

Axios 是一个基于 Promise 的 Node.js 应用程序的 HTTP 客户端。在上一节中，我们使用了`asyncData`方法与原始的 Promise 一起工作。我们可以使用 Axios 进一步简化我们的代码，并节省一些行数，它是由异步 JavaScript 和 XML（AJAX）支持的，用于进行异步 HTTP 请求。让我们在接下来的步骤中开始吧：

1.  通过 npm 安装 Axios：

```js
$ npm i axios
```

在使用 Axios 进行 HTTP 请求时，我们应该始终使用完整路径。

```js
axios.get('https://jsonplaceholder.typicode.com/posts')
```

但是在每个请求的路径中包含`https://jsonplaceholder.typicode.com/`可能会重复。此外，这个基本 URL 可能会随时间改变。因此，我们应该将其抽象出来并简化请求：

```js
axios.get('/posts')
```

1.  在`/plugins/`目录中创建一个 Axios 实例：

```js
// plugins/axios-api.js
import axios from 'axios'

export default axios.create({
  baseURL: 'http://localhost:3000'
})
```

1.  在组件中需要时导入这个插件：

```js
import axios from '~/plugins/axios-api'
```

安装和配置完成后，我们准备在下一节中获取异步数据。

## 使用 Axios 和 asyncData 获取数据

让我们在接下来的步骤中创建需要呈现数据的页面：

1.  创建一个用于列出所有用户的索引用户页面：

```js
// pages/users/index.vue
<li v-for="user in users" v-bind:key="user.id">
  <nuxt-link :to="'users/' + user.id">
    {{ user.name }}
  </nuxt-link>
</li>

<script>
import axios from '~/plugins/axios-api'
export default {
  async asyncData({error}) {
    try {
      let { data } = await axios.get('/api/users')
      return { users: data.data }
    } catch (e) {
      // handle error
    }
  }
}
</script>
```

在这个页面上，我们使用 Axios 的`get`方法来调用`/api/users`的 API 端点，它将被转换为`localhost:3000/api/users`，用户列表可以如下输出：

```js
{"status":200,"data":[{"id":1,"name":"Alexandre"},{"id":2,"name":"Pooya"},{"id":3,"name":"Sébastien"}]}
```

然后我们使用 JavaScript 的解构赋值`{ data }`来解开输出中的`data`键。在使用`async`/`await`语句时，将代码放在`try`/`catch`块中是一个好习惯。接下来，我们需要请求单个用户的数据。

1.  创建一个用于呈现单个用户数据的单个用户页面：

```js
// pages/users/_id.vue
<h2>
  {{ user.name }}
</h2>

<script>
import axios from '~/plugins/axios-api'
export default {
  name: 'id',
  async asyncData ({ params, error }) {
    try {
      let { data } = await axios.get('/api/users/' + params.id)
      return { user: data.data }
    } catch (e) {
      // handle error
    }
  }
}
</script>
```

在这个页面上，我们再次使用 Axios 的`get`方法来调用`/api/users/<id>`的 API 端点，这将被转换为`localhost:3000/api/users/<id>`，以获取单个用户的数据：

```js
{"status":200,"data":{"id":1,"name":"Alexandre"}}
```

再次使用 JavaScript 的解构赋值`{ data }`来解包输出中的`data`键，并将`async`/`await`代码包装在`try`/`catch`块中。

在下一节中，我们希望实现与本节相同的结果，即获取用户列表和特定用户的数据。但是我们将在单个页面上使用`watchQuery`属性，这是您在上一节中学到的。

## 监听查询变化

在本节中，我们将创建一个页面来监听查询字符串的变化并获取单个用户的数据。为此，我们只需要一个`.vue`页面来列出所有用户并监视查询，如果查询有任何变化，我们将从查询中获取`id`并使用`asyncData`方法中的 Axios 获取具有该`id`的用户。让我们开始吧：

1.  在`/pages/`目录中创建一个`users-query.vue`页面，并将以下模板添加到`<template>`块中：

```js
// pages/users-query.vue
<ul>
  <li v-for="user in users" v-bind:key="user.id">
    <nuxt-link :to="'users-query?id=' + user.id">
      {{ user.name }}
    </nuxt-link>
  </li>
</ul>
<p>{{ user }}</p>
```

在这个模板中，我们使用`v-for`指令来循环遍历每个`users`中的`user`，并将每个用户的查询添加到`<nuxt-link>`组件中。单个用户的数据将在`<ul>`标签之后的`<p>`标签内呈现。

1.  将以下代码添加到`<script>`块中：

```js
// pages/users-query.vue
import axios from '~/plugins/axios-api'

export default {
  async asyncData ({ query, error }) {
    var user = null
    if (Object.keys(query).length > 0) {
      try {
        let { data } = await axios.get('/api/users/' + query.id)
        user = data.data
      } catch (e) {
        // handle error
      }
    }

    try {
      let { data } = await axios.get('/api/users')
      return {
        users: data.data,
        user: user
      }
    } catch (e) {
      // handle error
    }
  },
  watchQuery: true
}
```

这段代码与`/pages/users/index.vue`相同；我们只是在`asyncData`中添加了一个`query`对象，并根据查询中的信息获取用户数据。当然，我们还添加了`watchQuery: true`或`watchQuery: ['id']`来监视查询的变化。因此，在浏览器中，当您从列表中点击一个用户，比如`users-query?id=1`，该用户的数据将呈现在`<p>`标签内，如下所示：

```js
{ "id": 1, "name": "Alexandre" }
```

干得好！您已经到达了本章的结尾。我们希望这对您来说是一个简单而容易的章节。除了使用 Axios 向 API 后端发出 HTTP 请求，我们还可以使用这些 Nuxt 模块之一：Axios 和 HTTP。在本书中，我们专注于原始的 Axios 和 Axios 模块。您还记得我们在第六章中介绍过 Axios 模块吗，*编写插件和模块*？我们将在接下来的章节中经常使用这个模块。现在，让我们总结一下您在本章学到的内容。

你可以在我们的 GitHub 存储库中的`/chapter-8/nuxt-universal/koa-nuxt/using-axios/axios-vanilla/`找到上述代码。如果您想了解更多关于 Nuxt HTTP 模块的信息，请访问[`http.nuxtjs.org/`](https://http.nuxtjs.org/)。

# 总结

在本章中，您已经学会了如何配置 Nuxt 与服务器端框架，本书中使用的是 Koa。您已经安装了 Koa 及其依赖项，以便创建 API。然后，您使用`asyncData`和 Axios 从 API 查询和获取数据。此外，您还了解了 Nuxt 上下文中的属性，可以从`asyncData`方法中解构和访问，例如`params`，`query`，`req`，`res`和`error`。最后，您开始在应用程序中使用 Backpack 作为一个极简的构建工具。

在下一章中，您将学习如何设置 MongoDB 并编写一些基本的 MongoDB 查询，如何向 MongoDB 数据库添加数据，如何将其与刚刚在本章中学习的服务器端框架 Koa 集成，最后，如何将其与 Nuxt 页面集成。我们将指导您学习一切，以便创建一个更完整的 API。所以，请继续关注。
