使用 Nuxt 创建实时应用程序

在本章中，我们将进一步探讨 Nuxt，看看如何使用它与其他框架一起制作实时应用程序。我们将继续使用 Koa 作为后端 API，但是通过 RethinkDB 和 Socket.IO 来“增强”它。换句话说，我们将使用这两个令人敬畏的框架和工具将我们的后端 API 转换为**实时 API**。同时，我们还将借助它们将我们的前端 Nuxt 应用程序转换为**实时 Nuxt 应用程序**。如果您愿意，您可以在单域方法上开发这两个实时应用程序。但是，本书更倾向于跨域方法，以便我们不会混淆前端和后端的依赖关系，并随着时间的推移而感到困惑。因此，这将是另一个您可以从中学习的有趣而令人兴奋的章节！

在本章中，我们将涵盖以下主题：

+   介绍 RethinkDB

+   将 RethinkDB 与 Koa 集成

+   介绍 Socket.IO

+   将 Socket.IO 与 Nuxt 集成

让我们开始吧！

# 第十七章：介绍 RethinkDB

RethinkDB 是用于实时应用程序的开源 JSON 数据库。每当数据库表中发生更改时，它会从数据库实时推送 JSON 数据到您的应用程序，您可以订阅这些实时订阅 - changefeeds。尽管 changefeeds 是 RethinkDB 实时功能的核心，但如果您愿意，您可以跳过此功能。您可以像使用 MongoDB 一样使用 RethinkDB 来存储和查询您的 NoSQL 数据库。

尽管您可以使用 MongoDB 中的更改流来访问实时数据更改，但这需要一些配置才能启动，而实时订阅在 RethinkDB 中默认情况下已准备就绪，您可以立即开始使用，无需任何配置。让我们首先在您的系统中安装**RethinkDB 服务器**，然后看看您如何在下一节中使用它。

## 安装 RethinkDB 服务器

在撰写本书时，RethinkDB 的当前稳定版本是**2.4.0**（**活死人之夜**），于 2019 年 12 月 19 日发布。根据平台（Ubuntu 或 OS），有几种安装 RethinkDB 服务器的方法。您可以在[`rethinkdb.com/docs/install/`](https://rethinkdb.com/docs/install/)上查看您平台的指南。请注意，Windows 在 2.4.0 中尚不受支持。有关 Windows 的更多信息，请访问[`rethinkdb.com/docs/install/windows`](https://rethinkdb.com/docs/install/windows)。

在本书中，我们将在**Ubuntu 20.04 LTS**（Focal Fossa）上安装 RethinkDB 2.4.0。如果您使用的是 Ubuntu 19.10（Eoan Ermine）、Ubuntu 19.04（Disco Dingo）或较旧版本的 Ubuntu，如 18.04 LTS（Bionic Beaver），操作方式是相同的。让我们开始吧：

1.  将 RethinkDB 存储库添加到 Ubuntu 存储库列表中，如下所示：

```js
$ source /etc/lsb-release && echo "deb https://download.rethinkdb.com/apt $DISTRIB_CODENAME main" | sudo tee /etc/apt/sources.list.d/rethinkdb.list
```

1.  使用`wget`获取 RethinkDB 的公钥：

```js
$ wget -qO- https://download.rethinkdb.com/apt/pubkey.gpg | sudo apt-key add -
```

对于上述命令行，您应该在终端上收到一个 OK 消息。

1.  更新您的 Ubuntu 版本并安装 RethinkDB：

```js
$ sudo apt update
$ sudo apt install rethinkdb
```

1.  验证 RethinkDB：

```js
$ rethinkdb -v
```

您应该在终端上获得以下输出：

```js
rethinkdb 2.4.0~0eoan (CLANG 9.0.0 (tags/RELEASE_900/final))
```

RethinkDB 附带了一个管理 UI，供您在浏览器上管理数据库，地址为`localhost:8080`。这在项目开发过程中非常方便和有用。如果您想要卸载 RethinkDB 并删除所有数据库，可以使用以下命令进行操作：

```js
$ sudo apt purge rethinkdb.
$ sudo rm -r /var/lib/rethinkdb
```

安装时附带的管理 UI 类似于您在上一章中用于管理 PHP API 的 MySQL 数据库的 PHP Adminer。您可以使用 RethinkDB 管理 UI 通过 UI 上的图形按钮或使用 JavaScript 中的 RethinkDB 查询语言（ReQL）添加数据库和表。我们将在下一节中探索管理 UI 和 ReQL。

## 介绍 ReQL

ReQL 是 RethinkDB 的查询语言，用于操作 RethinDB 数据库中的 JSON 文档。查询是通过在服务器端调用 RethinkDB 的内置可链接函数自动构建的。这些函数嵌入在各种编程语言的驱动程序中，包括 JavaScript、Python、Ruby 和 Java。您可以在以下链接中查看 ReQL 命令/函数：

+   JavaScript 在[`rethinkdb.com/api/javascript/`](https://rethinkdb.com/api/javascript/)上。

+   Python 在[`rethinkdb.com/api/python/`](https://rethinkdb.com/api/python/)上。

+   Ruby 在[`rethinkdb.com/api/ruby/`](https://rethinkdb.com/api/ruby/)上。

+   Java 在[`rethinkdb.com/api/java/`](https://rethinkdb.com/api/java/)上。

本书将使用 JavaScript。让我们在管理 UI 上使用数据资源管理器，通过使用相应的 ReQL 命令执行一些 CRUD 操作。您可以导航到数据资源管理器所在的页面，或者将浏览器指向`localhost:8080/#dataexplorer`并开始使用查询，如下所示。数据资源管理器上的默认顶级命名空间是`r`，因此 ReQL 命令必须链接到此命名空间。

但是，在我们的应用程序中使用驱动程序时，我们可以更改这个`r`命名空间，并在下一节中使用任何我们喜欢的名称。现在，让我们在这个练习中坚持使用默认命名空间`r`。

1.  创建数据库：

```js
r.dbCreate('nuxtdb')
```

点击运行按钮。您应该在屏幕上看到类似以下的结果，显示已创建一个数据库，数据库名称由您选择，并且 RethinkDB 生成了一个 ID：

```js
{
  "config_changes": [
    {
      "new_val": {
      "id": "353d11a4-adc8-4958-a4ae-a82c996dcb9f" ,
      "name": "nuxtdb"
    } ,
      "old_val": null
    }
  ] ,
  "dbs_created": 1
}
```

如果您想了解有关`dbCreate` ReQL 命令的更多信息，请访问[`rethinkdb.com/api/javascript/db_create/`](https://rethinkdb.com/api/javascript/db_create/)。

1.  在现有数据库中创建表；例如，在`nuxtdb`数据库中创建一个`user`表：

```js
r.db('nuxtdb').tableCreate('user')
```

点击运行按钮。您应该在屏幕上看到类似以下的结果，显示 RethinkDB 为您生成的一个带有 ID 的表已被创建，并显示您创建的表的其他信息：

```js
{
  "config_changes": [{
    "new_val": {
      "db": "nuxtdb",
      "durability": "hard",
      "id": "259e0066-1ffe-4064-8b24-d1c82e515a4a",
      "indexes": [],
      "name": "user",
      "primary_key": "id",
      "shards": [{
        "nonvoting_replicas": [],
        "primary_replica": "lau_desktop_opw",
        "replicas": ["lau_desktop_opw"]
      }],
      "write_acks": "majority",
      "write_hook": null
    },
    "old_val": null
  }],
  "tables_created": 1
}
```

如果您想了解有关`tableCreate` ReQL 命令的更多信息，请访问[`rethinkdb.com/api/javascript/table_create/`](https://rethinkdb.com/api/javascript/table_create/)。

1.  将新文档插入`user`表中：

```js
r.db('nuxtdb').table('user').insert([
 { name: "Jane Doe", slug: "jane" },
 { name: "John Doe", slug: "john" }
])
```

点击运行按钮。您应该在屏幕上看到类似以下的结果，显示 RethinkDB 为您生成的两个带有键的文档已被插入：

```js
{
  "deleted": 0,
  "errors": 0,
  "generated_keys": [
    "7f7d768d-0efd-447d-8605-2d460a381944",
    "a144001c-d47e-4e20-a570-a29968980d0f"
  ],
  "inserted": 2,
  "replaced": 0,
  "skipped": 0,
  "unchanged": 0
}
```

如果您想了解有关`table`和`insert` ReQL 命令的更多信息，请分别访问[`rethinkdb.com/api/javascript/table/`](https://rethinkdb.com/api/javascript/table/)和[`rethinkdb.com/api/javascript/insert/`](https://rethinkdb.com/api/javascript/insert/)。

1.  从`user`表中检索文档：

```js
r.db('nuxtdb').table('user')
```

点击运行按钮。您应该在屏幕上看到类似以下的结果，显示`user`表中的两个文档：

```js
[{
  "id": "7f7d768d-0efd-447d-8605-2d460a381944",
  "name": "Jane Doe",
  "slug": "jane"
}, {
  "id": "a144001c-d47e-4e20-a570-a29968980d0f",
  "name": "John Doe",
  "slug": "john"
}]
```

如果要计算表中的总文档数，可以将`count`方法链接到查询中，如下所示：

```js
r.db('nuxtdb').table('user').count()
```

在注入新文档后，`user`表中应该有`2`个文档。

如果您想了解有关`count` ReQL 命令的更多信息，请访问[`rethinkdb.com/api/javascript/count/`](https://rethinkdb.com/api/javascript/count/)。

1.  更新`user`表中的文档，通过使用`slug`键过滤表：

```js
r.db('nuxtdb').table('user')
.filter(
  r.row("slug").eq("john")
)
.update({
  name: "John Wick"
})
```

点击运行按钮。您应该在屏幕上看到以下结果，显示已替换一个文档：

```js
{
  "deleted": 0,
  "errors": 0,
  "inserted": 0,
  "replaced": 1,
  "skipped": 0,
  "unchanged": 0
}
```

如果您想了解有关`filter`和`update` ReQL 命令的更多信息，请分别访问[`rethinkdb.com/api/javascript/filter/`](https://rethinkdb.com/api/javascript/filter/)和[`rethinkdb.com/api/javascript/update/`](https://rethinkdb.com/api/javascript/update/)。

另外，如果您想了解有关`row`和`eq` ReQL 命令的更多信息，请分别访问[`rethinkdb.com/api/javascript/row/`](https://rethinkdb.com/api/javascript/row/)和[`rethinkdb.com/api/javascript/eq/`](https://rethinkdb.com/api/javascript/eq/)。

1.  通过使用`slug`键过滤表格来从`user`表中删除文档：

```js
r.db('nuxtdb').table('user')
.filter(
  r.row("slug").eq("john")
)
.delete()
```

点击运行按钮。您应该在屏幕上看到以下结果，显示已删除一个文档：

```js
{
  "deleted": 1,
  "errors": 0,
  "inserted": 0,
  "replaced": 0,
  "skipped": 0,
  "unchanged": 0
}
```

如果您想删除表中的所有文档，那么只需将`delete`方法链接到表而不进行过滤，如下所示：

```js
r.db('nuxtdb').table('user').delete()
```

如果您想了解有关`delete` ReQL 命令的更多信息，请访问[`rethinkdb.com/api/javascript/delete/`](https://rethinkdb.com/api/javascript/delete/)。

在使用 ReQL 命令时，这很有趣也很容易，不是吗？您不必阅读所有 ReQL 命令并详细研究每个命令以提高生产力。您只需要知道您想要做什么，并根据您已经了解的编程语言从 ReQL 命令参考/API 页面中找到您需要的命令。接下来，您将找出如何将**RethinkDB 客户端**或驱动程序添加到您的应用程序中。让我们开始吧！

# 将 RethinkDB 与 Koa 集成

在本节中，我们将构建一个简单的 API，按照我们在上一章中创建的 PHP API 的方式列出、添加、更新和删除用户。在之前的 API 中，我们使用了 PHP 和 MySQL，而在本章中，我们将使用 JavaScript 和 RethinkDB。我们仍将使用 Koa 作为 API 的框架。但这一次，我们将重新构建 API 目录，使其结构与您已经熟悉的 Nuxt 应用程序和 PHP API 的目录结构保持一致（尽可能）。所以，让我们开始吧！

## 重构 API 目录

请记住，当使用 Vue CLI 时，您在项目中获得的默认目录结构，您在第十一章中了解过，*编写路由中间件和服务器中间件*？使用 Vue CLI 安装项目后，如果您查看项目目录，您将看到一个基本的项目结构，其中包含`/src/`目录，用于开发组件、页面和路由，如下所示：

```js
├── package.json
├── babel.config.js
├── README.md
├── public
│ ├── index.html
│ └── favicon.ico
└── src
    ├── App.vue
    ├── main.js
    ├── router.js
    ├── components
    │ └── HelloWorld.vue
    └── assets
        └── logo.png
```

自第十二章以来，我们一直在为跨域应用程序使用这种标准结构，*创建用户登录和 API 身份验证*。例如，以下是您之前创建的 Koa API 的目录结构：

```js
backend
├── package.json
├── backpack.config.js
├── static
│ └── ...
└── src
    ├── index.vue
    ├── ...
    ├── modules
    │ └── ...
    └── core
        └── ...
```

但是这一次，我们将在本章中要创建的 API 中消除`/src/`目录。因此，让我们将`/src/`目录中的所有内容移动到顶层，并重新配置应用程序的引导方式，如下所示：

1.  在项目的根目录中创建以下文件和文件夹：

```js
backend
├── package.json
├── backpack.config.js
├── middlewares.js
├── routes.js
├── configs
│ ├── index.js
│ └── rethinkdb.js
├── core
│ └── ...
├── middlewares
│ └── ...
├── modules
│ └── ...
└── public
    └── index.js
```

再次强调，这里的目录结构仅仅是一个建议；您可以根据自己的需求设计自己的目录结构，使其最适合您。但让我们来看看这个建议的目录，并研究这些文件和文件夹的用途：

+   `/configs/`目录用于存储应用程序的基本信息和 RethinkDB 数据库连接的详细信息。

+   `/public/`目录用于存储启动应用程序的文件。

+   `/modules/`目录用于存储应用程序的模块，例如我们将在接下来的章节中创建的`'user'`模块。

+   `/core/`目录用于存储可以在整个应用程序中使用的常用函数或类。

+   `middlewares.js`文件是从`/middlewares/`和`/node_modules/`目录导入中间件的核心位置。

+   `routes.js`文件是从`/modules`目录导入路由的核心位置。

+   `backpack.config.js`文件用于自定义我们应用程序的 webpack 配置。

+   `package.json`文件包含我们应用程序的脚本和依赖项，并始终位于根目录。

1.  将入口文件指向`/public/`目录中的`index.js`文件：

```js
// backpack.config.js
module.exports = {
  webpack: (config, options, webpack) => {
    config.entry.main = './public/index.js'
    return config
  }
}
```

请记住，Backpack 中的默认入口文件是`/src/`目录中的`index.js`文件。由于我们已将此索引文件移动到`/public/`目录，因此必须通过 Backpack 配置文件配置此入口点。

如果您想了解 webpack 中的入口点，请访问[`webpack.js.org/concepts/entry-points/`](https://webpack.js.org/concepts/entry-points/)。

1.  在返回 Backpack 配置文件中的`config`对象之前，在 webpack 配置中的`resolve`选项中为`/configs`、`/core`、`/modules`和`/middlewares`路径添加别名：

```js
// backpack.config.js
const path = require('path')

config.resolve = {
  alias: {
    Configs: path.resolve(__dirname, 'configs/'),
    Core: path.resolve(__dirname, 'core/'),
    Modules: path.resolve(__dirname, 'modules/'),
    Middlewares: path.resolve(__dirname, 'middlewares/')
  }
}
```

在我们的应用程序中使用别名来解析文件路径非常有用和方便。通常，我们使用相对路径导入文件，就像这样：

```js
import notFound from '../../Middlewares/notFound'
```

现在，我们可以使用别名从任何地方导入文件，这样可以隐藏相对路径，从而使我们的代码更整洁：

```js
import notFound from 'Middlewares/notFound'
```

如果您想了解 webpack 中的别名和解析选项，请访问[`webpack.js.org/configuration/resolve/resolvealias`](https://webpack.js.org/configuration/resolve/#resolvealias)。

一旦您准备好了上述结构并且入口文件已经排序好，您就可以开始将 CRUD 操作应用到此 API 中。但首先，您需要将**RethinkDB JavaScript 客户端**安装到您的项目中。所以，让我们开始吧！

## 添加并使用 RethinkDB JavaScript 客户端

根据您拥有的编程知识，您可以选择几个官方客户端驱动程序，包括 JavaScript、Ruby、Python 和 Java。还有许多社区支持的驱动程序，如 PHP、Perl 和 R。您可以在[`rethinkdb.com/docs/install-drivers/`](https://rethinkdb.com/docs/install-drivers/)上查看它们。

在本书中，我们将使用 RethinkDB JavaScript 客户端驱动程序。我们将指导您通过以下步骤安装并使用此驱动程序进行 CRUD 操作：

1.  通过 npm 安装 RethinkDB JavaScript 客户端驱动程序：

```js
$ npm i rethinkdb
```

1.  创建一个`rethinkdb.js`文件，其中包含 RethinkDB 服务器连接详细信息，放在`/configs/`目录中，如下所示：

```js
// configs/rethinkdb.js
export default {
  host: 'localhost',
  port: 28015,
  dbname: 'nuxtdb'
}
```

1.  创建一个名为`connection.js`的文件，其中包含在`/core/`目录中的上述连接详细信息，用于打开 RethinkDB 服务器连接，如下所示：

```js
// core/database/rethinkdb/connection.js
import config from 'Configs/rethinkdb'
import rethink from'rethinkdb'

const c = async() => {
  const connection = await rethink.connect({
    host: config.host,
    port: config.port,
    db: config.dbname
  })
  return connection
}
export default c
```

1.  此外，还可以创建一个名为`open.js`的开放连接中间件文件，并将其绑定到 Koa 上下文中作为连接到 RethinkDB 的另一种选择，放在`/middlewares/`目录中。

```js
// middlewares/database/rdb/connection/open.js
import config from 'Configs/rethinkdb'
import rdb from'rethinkdb'

export default async (ctx, next) => {
  ctx._rdbConn = await rdb.connect({
    host: config.host,
    port: config.port,
    db: config.dbname
  })
  await next()
}
```

使用目录路径来描述你的中间件（或 CRUD 操作）是一个很好的做法，我们从 PHP 的 PSR-4 中学到了这一点，这样你就不必使用一个很长的名称来描述你的文件。例如，如果你没有使用描述性的目录路径，你可能想将这个中间件命名为`rdb-connection-open.js`，以尽可能清楚地描述它。但如果你使用目录路径来描述中间件，那么你可以简单地将文件命名为`open.js`。

1.  在`/middlewares/`目录中创建一个`close.js`文件，创建一个关闭连接中间件，并将其绑定到 Koa 上下文作为最后一个中间件，如下所示：

```js
// middlewares/database/rdb/connection/close.js
import config from 'Configs/rethinkdb'
import rdb from'rethinkdb'

export default async (ctx, next) => {
  ctx._rdbConn.close()
  await next()
}
```

1.  在根目录的`middlewares.js`文件中导入`open`和`close`连接中间件，并将它们注册到应用程序中，如下所示：

```js
// middlewares.js
import routes from './routes'
import rdbOpenConnection from 'Middlewares/database/rdb/connection/open'
import rdbCloseConnection from 'Middlewares/database/rdb/connection/close'

export default (app) => {
  //...
  app.use(rdbOpenConnection)
  app.use(routes.routes(), routes.allowedMethods())
  app.use(rdbCloseConnection)
}
```

在这里，你可以看到`open`连接中间件在所有模块路由之前注册，而`close`连接中间件则在最后注册，这样它们分别被首先和最后调用。

1.  在接下来的步骤中，我们将使用以下模板代码与 Koa 路由器和 RethinkDB 客户端驱动进行 CRUD 操作。例如，以下代码显示了我们如何将模板代码应用于从`user`模块中的`user`表中获取所有用户的操作：

```js
// modules/user/_routes/index.js
import Router from 'koa-router'
import rdb from 'rethinkdb'

const router = new Router()
router.get('/', async (ctx, next) => {
  try {
    // perform verification on the incoming parameters...
    // perform a CRUD operation:
    let result = await rdb.table('user')
      .run(ctx._rdbConn)

    ctx.type = 'json'
    ctx.body = result
    await next()

  } catch (err) {
    ctx.throw(500, err)
  }
})
export default router
```

让我们来看看这段代码，了解它的作用。在这里，你可以看到我们在应用中使用了自定义的顶级命名空间`rdb`，用于 RethinkDB 客户端驱动，而不是你在`localhost:8080`上练习过的`r`命名空间。此外，在我们的应用中使用 RethinkDB 客户端驱动时，我们必须始终在 ReQL 命令的末尾调用`run`方法，以及 RethinkDB 服务器连接，以构造查询并将其传递到服务器执行。

此外，我们必须在代码的末尾调用`next`方法，以便将应用的执行传递给下一个中间件，特别是用于关闭 RethinkDB 连接的`close`连接中间件。在执行任何 CRUD 操作之前，我们应该对来自客户端的传入参数和数据进行检查。然后，我们应该将我们的代码包装在`try-catch`块中，以捕获和抛出任何潜在的错误。

请注意，在接下来的步骤中，我们将跳过编写参数验证和 try-catch 语句的代码，以避免冗长和重复的代码行和代码块，但你应该在实际代码中包含它们。

1.  在`user`模块的`/_routes/`文件夹中创建一个名为`create-user.js`的文件，其中包含以下代码，用于将新用户注入到数据库中的`user`表中：

```js
// modules/user/_routes/create-user.js
router.post('/user', async (ctx, next) => {
  let result = await rdb.table('user')
    .insert(document, {returnChanges: true})
    .run(ctx._rdbConn)

  if (result.inserted !== 1) {
    ctx.throw(404, 'insert user failed')
  }

  ctx.type = 'json'
  ctx.body = result
  await next()
})
```

如果插入失败，我们应该抛出错误，并将错误消息传递给 Koa 的`throw`方法，以便我们可以在前端使用`try-catch`块捕获它们并显示出来。

1.  在`user`模块的`/_routes/`文件夹中创建一个名为`fetch-user.js`的文件，用于通过使用`slug`键从`user`表中获取特定用户，如下所示：

```js
// modules/user/_routes/fetch-user.js
router.get('/:slug', async (ctx, next) => {
  const slug = ctx.params.slug
  let user = await rdb.table('user')
    .filter(searchQuery)
    .nth(0)
    .default(null)
    .run(ctx._rdbConn)

  if (!user) {
    ctx.throw(404, 'user not found')
  }

  ctx.type = 'json'
  ctx.body = user
  await next()
})
```

我们在查询中添加了`nth`命令，以显示文档的位置。在我们的情况下，我们只想获取第一个文档，因此我们将一个`0`整数传递给此方法。我们还添加了`default`命令，以便在`user`表中找不到用户时返回一个`null`异常。

1.  在`user`模块的`/_routes/`文件夹中创建一个名为`update-user.js`的文件，用于通过使用文档 ID 更新`user`表中的现有用户，如下所示：

```js
// modules/user/_routes/update-user.js
router.put('/user', async (ctx, next) => {
  let body = ctx.request.body || {}
  let objectId = body.id

  let timestamp = Date.now()
  let updateQuery = {
    name: body.name,
    slug: body.slug,
    updatedAt: timestamp
  }

  let result = await rdb.table('user')
    .get(objectId)
    .update(updateQuery, {returnChanges: true})
    .run(ctx._rdbConn)

  if (result.replaced !== 1) {
    ctx.throw(404, 'update user failed')
  }

  ctx.type = 'json'
  ctx.body = result
  await next()
})
```

我们在查询中添加了`get`命令，首先通过其 ID 获取特定文档，然后再运行更新。

1.  在`user`模块的`/_routes/`文件夹中创建一个名为`delete-user.js`的文件，用于通过使用文档 ID 从`user`表中删除现有用户，如下所示：

```js
// modules/user/_routes/delete-user.js
router.del('/user', async (ctx, next) => {
  let body = ctx.request.body || {}
  let objectId = body.id

  let result = await rdb.table('user')
    .get(objectId)
    .delete()
    .run(ctx._rdbConn)

  if (result.deleted !== 1) {
    ctx.throw(404, 'delete user failed')
  }

  ctx.type = 'json'
  ctx.body = result
  await next()
})
```

1.  最后，在`index.js`文件中对刚刚在*步骤 7*中创建的用于列出`user`表中所有用户的 CRUD 操作进行重构，通过在查询中添加`orderBy`命令，该文件位于`/_routes/`文件夹中，如下所示：

```js
// modules/user/_routes/index.js
router.get('/', async (ctx, next) => {
  let cursor = await rdb.table('user')
    .orderBy(rdb.desc('createdAt'))
    .run(ctx._rdbConn)

  let users = await cursor.toArray()

  ctx.type = 'json'
  ctx.body = users
  await next()
})
```

我们在查询中添加了`orderBy`命令，以便我们可以按创建日期降序（最新的在前）对文档进行排序。此外，RethinkDB 数据库返回的文档始终作为 CRUD 操作的回调包含在一个**游标对象**中，因此我们必须使用`toArray`命令来遍历游标并将对象转换为数组。

如果您想了解更多关于`orderBy`和`toArray`命令，请访问[`rethinkdb.com/api/javascript/order_by/`](https://rethinkdb.com/api/javascript/order_by/)和[`rethinkdb.com/api/javascript/to_array/`](https://rethinkdb.com/api/javascript/to_array/)。

通过这样，您已成功在 API 中使用 RethinkDB 实现了 CRUD 操作。再次强调，这很容易且有趣，不是吗？但是我们仍然可以通过在 RethinkDB 数据库中强制执行模式来提高我们存储的文档的“质量”。我们将在下一部分学习如何做到这一点。

## 在 RethinkDB 中强制执行模式

就像 MongoDB 中的 BSON 数据库一样，RethinkDB 中的 JSON 数据库也是**无模式**的。这意味着数据库上没有蓝图，也没有强加在数据库上的公式或完整性约束。数据库的构造方式没有组织规则可能会引发数据库完整性的问题。同一张表（或 MongoDB 中的“集合”）中的某些文档可能包含不同和不需要的键，以及具有正确键的文档。您可能会错误地注入一些键，或者忘记注入所需的键和值。因此，如果您希望保持文档中的数据有组织，强制执行 JSON 或 BSON 数据库中的某种模式可能是一个好主意。RethinkDB（或 MongoDB）没有内部功能来强制执行模式，但我们可以使用 Node.js Lodash 模块创建自定义函数来强制执行一些基本模式。让我们探讨如何做到这一点：

1.  通过 npm 安装 Lodash 模块：

```js
$ npm i lodash
```

1.  在`/core/`目录中创建一个`utils.js`文件，并导入`lodash`以创建一个名为`sanitise`的函数，如下所示：

```js
// core/utils.js
import lodash from 'lodash'

function sanitise (options, schema) {
  let data = options || {}

  if (schema === undefined) {
    const err = new Error('Schema is required.')
    err.status = 400
    err.expose = true
    throw err
  }

  let keys = lodash.keys(schema)
  let defaults = lodash.defaults(data, schema)
  let picked = lodash.pick(defaults, keys)

  return picked
}
export { sanitise }
```

这个函数简单地选择您设置的默认键，并忽略任何不在“模式”中的额外键。

我们正在使用 Lodash 中的以下方法。有关每种方法的更多信息，请访问以下链接：[`lodash.com/docs/4.17.15#keys`](https://lodash.com/docs/4.17.15#keys) 获取`keys`方法的信息 [`lodash.com/docs/4.17.15#defaults`](https://lodash.com/docs/4.17.15#defaults) 获取`defaults`方法的信息 [`lodash.com/docs/4.17.15#pick`](https://lodash.com/docs/4.17.15#pick) 获取`pick`方法的信息

1.  在`user`模块中创建一个`user`模式，只接受以下键：

```js
// modules/user/schema.js
export default {
  slug: null,
  name: null,
  createdAt: null,
  updatedAt: null
}
```

1.  在要强制执行模式的路由中导入`sanitise`方法和前面的模式；例如，在`create-user.js`文件中：

```js
// modules/user/_routes/create-user.js
let timestamp = Date.now()
let options = {
  name: body.name,
  slug: body.slug,
  createdAt: timestamp,
  username: 'marymoe',
  password: '123123'
}

let document = sanitise(options, schema)
let result = await rdb.table('user')
  .insert(document, {returnChanges: true})
  .run(ctx._rdbConn)
```

在上述代码中，示例字段`username`和`password`在插入数据之前对数据进行清理时不会被注入到`user`表中的文档中。

您可以看到这个`sanitise`函数只执行简单的验证。如果您需要更复杂和高级的数据验证，可以使用 hapi web 框架的 Node.js joi 模块。

如果您想了解更多关于这个模块的信息，请访问[`hapi.dev/module/joi/`](https://hapi.dev/module/joi/)。

您接下来必须探索 RethinkDB 中的**changefeeds**。这是本章的主要目的 - 展示如何利用 RethinkDB 的实时功能创建实时应用程序。因此，让我们探索并玩转 RethinkDB 中的 changefeeds！

## 介绍 RethinkDB 中的 changefeeds

在使用 RethinkDB 客户端驱动程序在我们的应用程序中应用 changefeeds 之前，让我们再次在`localhost:8080/#dataexplorer`的管理 UI 中使用数据浏览器，实时在屏幕上查看实时 feeds：

1.  粘贴以下的 ReQL 查询，并单击“运行”按钮：

```js
r.db('nuxtdb').table('user').changes()
```

您应该在浏览器屏幕上看到以下信息：

```js
Listening for events...
Waiting for more results
```

1.  在浏览器上打开另一个标签，并将其指向`localhost:8080/#dataexplorer`。现在，您有两个数据浏览器。将其中一个从浏览器标签中拖出来，以便您可以将它们并排放置。然后，从其中一个数据浏览器中将新文档插入`user`表中：

```js
r.db('nuxtdb').table('user').insert([
  { name: "Richard Roe", slug: "richard" },
  { name: "Marry Moe", slug: "marry" }
])
```

您应该得到以下结果：

```js
{
  "deleted": 0,
  "errors": 0,
  "generated_keys": [
    "f7305c97-2bc9-4694-81ec-c5acaed1e757",
    "5862e1fa-e51c-4878-a16b-cb8c1f1d91de"
  ],
  "inserted": 2,
  "replaced": 0,
  "skipped": 0,
  "unchanged": 0
}
```

与此同时，您应该立即在另一个数据浏览器中看到以下 feeds 的实时显示：

```js
{
  "new_val": {
    "id": "f7305c97-2bc9-4694-81ec-c5acaed1e757",
    "name": "Richard Roe",
    "slug": "richard"
  },
  "old_val": null
}

{
  "new_val": {
    "id": "5862e1fa-e51c-4878-a16b-cb8c1f1d91de",
    "name": "Marry Moe",
    "slug": "marry"
  },
  "old_val": null
}
```

万岁！您刚刚轻松地使用 RethinkDB 创建了实时 feeds！请注意，您将始终在每个实时 feed 中获得这两个键，`new_val`和`old_val`。它们具有以下含义：

+   如果您在`new_val`中获取数据，但在`old_val`中获取的是`null`，这意味着新文档被注入到数据库中。

+   如果您在`new_val`和`old_val`中都获取到数据，这意味着现有文档在数据库中已更新。

+   如果您在`old_val`中获取数据，但在`new_val`中获取的是`null`，这意味着现有文档已从数据库中删除。

当我们在本章的最后一节中在 Nuxt 应用程序中使用它们时，您将可以使用这些键。因此，现在不要太担心它们。相反，下一个挑战是在 API 和 Nuxt 应用程序中实现它。为此，我们将需要另一个 Node.js 模块 - **Socket.IO**。因此，让我们探索一下这个模块如何帮助您实现这一目标。

# 介绍 Socket.IO

就像 HTTP 一样，WebSocket 是一种通信协议，但它提供了客户端和服务器之间的全双工（双向）通信。与 HTTP 不同，WebSocket 连接始终保持开放状态，用于实时数据传输。因此，在 WebSocket 应用程序中，服务器可以在没有客户端发起请求的情况下向客户端发送数据。

另外，与以 HTTP 或 HTTPS 开头的 HTTP 模式不同，WebSocket 协议模式以`ws`或`wss`开头，例如：

```js
ws://example.com:4000
```

Socket.IO 是一个使用 WebSocket 协议和轮询作为创建实时 Web 应用的备用选项的 JavaScript 库。它支持任何平台、浏览器或设备，并处理服务器和客户端的所有降级，以实现实时的全双工通信。大多数浏览器现在都支持 WebSocket 协议，包括 Google Chrome、Microsoft Edge、Firefox、Safari 和 Opera。但是在使用 Socket.IO 时，我们必须同时使用其客户端和服务器端库。客户端库在浏览器内运行，而服务器端库在服务器端的 Node.js 应用程序上运行。因此，让我们在我们的应用程序中让这两个库一起工作。

如果您想了解更多关于 Socket.IO 的信息，请访问[`socket.io/`](https://socket.io/)。

## 添加和使用 Socket.IO 服务器和客户端

我们将把 Socket.IO 服务器添加到我们在最近几节中构建的 API 中，然后最终将 Socket.IO 客户端添加到 Nuxt 应用程序中。但在将其添加到 Nuxt 应用程序之前，我们将其添加到一个简单的 HTML 页面中，以便我们可以全面了解 Socket.IO 服务器和 Socket.IO 客户端是如何一起工作的。让我们学习如何做到这一点：

1.  通过 npm 安装 Socket.IO 服务器：

```js
$ npm i socket.io
```

1.  如果您还没有这样做，可以在`/configs/`目录中创建一个`index.js`文件来存储服务器设置：

```js
// configs/index.js
export default {
  server: {
    port: 4000
  },
}
```

从这个简单的设置中，我们将在端口 4000 上提供我们的 API。

1.  导入`socket.io`并将其绑定到 Node.js HTTP 对象，使用 Koa 的新实例创建一个新的 Socket.IO 实例，如下所示：

```js
// backend/koa/public/index.js
import Koa from 'koa'
import socket from 'socket.io'
import http from 'http'
import config from 'Configs'
import middlewares from '../middlewares'

const app = new Koa()
const host = process.env.HOST || '127.0.0.1'
const port = process.env.PORT || config.server.port
middlewares(app)

const server = http.createServer(app.callback())
const io = socket(server)

io.sockets.on('connection', socket => {
  console.log('a user connected: ' + socket.id)
  socket.on('disconnect', () => {
    console.log('user disconnected: ' + socket.id)
  })
})
server.listen(port, host)
```

创建 Socket.IO 的新实例后，我们可以开始监听 Socket.IO 的`connection`事件，以接收来自`socket`回调的传入 socket。我们将传入的 socket 记录到控制台并附带其 ID。当 socket 断开连接时，我们还会记录传入 socket 的`disconnect`事件。最后，请注意，我们使用原生 Node.js HTTP 来启动和提供应用程序在`localhost:4000`上，而不是使用我们以前使用的 Koa 内部的 HTTP：

```js
app.listen(4000)
```

1.  创建一个`socket-client.html`页面，并通过 CDN 导入 Socket.IO 客户端。通过将`localhost:4000`作为特定 URL 传递来创建一个新的实例，如下所示：

```js
// frontend/html/socket-client.html
<script src="https://cdn.jsdelivr.net/npm/socket.io-
 client@2/dist/socket.io.js"></script>

<script>
  var socket = io('http://localhost:4000/')
</script>
```

现在，如果你在浏览器上浏览这个 HTML 页面，或者当你刷新页面时，你应该看到控制台打印出带有 socket ID 的日志，如下所示：

```js
a user connected: abeGnarBnELo33vQAAAB
```

当你关闭 HTML 页面时，你也应该看到控制台打印出带有 socket ID 的日志，如下所示：

```js
user disconnected: abeGnarBnELo33vQAAAB
```

这就是连接 Socket.IO 服务器和客户端的全部操作。这非常简单易懂，不是吗？但我们在这里所做的只是连接和断开服务器和客户端。我们需要更多的功能——我们希望能够同时传输数据。为了做到这一点，我们只需要相互发射和接收事件，这将在接下来的步骤中完成。

如果你想使用 Socket.IO 客户端的本地版本，你可以将脚本标签的 URL 源指向`/node_modules/socket.io-client/dist/socket.io.js`。

1.  通过使用 Socket.IO 服务器的`emit`方法，从服务器创建一个发射事件，如下所示：

```js
// backend/koa/public/index.js
io.sockets.on('connection', socket => {
  io.emit('emit.onserver', 'Hi client, what you up to?')
  console.log('Message to client: ' + socket.id)
})
```

在这里，你可以看到我们通过名为`emit.onserver`的自定义事件发射了一个简单的消息，并将活动记录到控制台中。请注意，我们只能在连接建立时发射事件。然后，我们可以在客户端监听这个自定义事件，并记录来自服务器的消息，如下所示：

```js
// frontend/html/socket-client.html
socket.on('emit.onserver', function (message) {
  console.log('Message from server: ' + message)
})
```

1.  所以，现在，如果你再次在浏览器上刷新页面，你应该看到控制台打印出带有 socket ID 的日志，如下所示：

```js
Message to client: abeGnarBnELo33vQAAAB // server side
Message from server: Hi client, what you up to? // client side
```

1.  通过使用 Socket.IO 客户端的`emit`方法，从客户端创建一个发射事件，如下所示：

```js
// frontend/html/socket-client.html
<script
  src="https://code.jquery.com/jquery-3.4.1.slim.min.js"
  integrity="sha256-pasqAKBDmFT4eHoN2ndd6lN370kFiGUFyTiUHWhU7k8="
  crossorigin="anonymous"></script>

<button class="button-sent">Send</button>

$('.button-sent').click(function(e){
  e.preventDefault()

  var message = 'Hi server, how are you holding up?'
  socket.emit('emit.onclient', message)
  console.log('Message sent to server.')

  return false
})
```

在这里，你可以看到，首先，我们通过 CDN 安装 jQuery，并创建一个带有 jQuery `click`事件的`<button>`。其次，当按钮被点击时，我们发射名为`emit.onclient`的 Socket.IO 自定义事件，并附带一个简单的消息。最后，我们将活动记录到控制台中。

1.  之后，我们可以在服务器端监听 Socket.IO 自定义事件，并记录来自客户端的消息，如下所示：

```js
// backend/koa/public/index.js
socket.on('emit.onclient', (message) => {
  console.log('Message from client, '+ socket.id + ' :' + message);
})
```

1.  如果你再次在浏览器上刷新页面，你应该看到控制台打印出日志，以及 socket ID，如下所示：

```js
Message sent to server. // client side
Message from client, abeGnarBnELo33vQAAAB: Hi server, 
how are you holding up? // server side
```

现在你知道如何通过 Socket.IO 实时传输数据——只需发射自定义事件并监听它们。你接下来应该了解的是如何将 Socket.IO 与 RethinkDB 的 changefeeds 集成，以便将实时数据从数据库传输到客户端。所以，请继续阅读！

## 集成 Socket.IO 服务器和 RethinkDB changefeeds

请记住，您之前曾在`localhost:8080/#dataexplorer`的管理 UI 中再次使用 Data Explorer 调整 RethinkDB changefeeds。要订阅 changefeed，您只需将 ReQL 的`changes`命令链接到查询，如下所示：

```js
r.db('nuxtdb').table('user').changes()
```

RethinkDB changefeeds 包含从 RethinkDB 数据库发出的实时数据，这意味着我们需要在服务器端使用 Socket.IO 服务器捕获这些 feed，并将它们发出到客户端。因此，让我们学习如何通过重构我们在本章中一直在开发的 API 来捕获它们：

1.  通过 npm 将 Socket.IO 服务器安装到您的 API 中：

```js
$ npm i socket.io
```

1.  在`/core/`目录中的`changefeeds.js`文件中创建一个异步匿名箭头函数，代码如下：

```js
// core/database/rethinkdb/changefeeds.js
import rdb from 'rethinkdb'
import rdbConnection from './connection'

export default async (io, tableName, eventName) => {
  try {
    const connection = await rdbConnection()
    var cursor = await rdb.table(tableName)
      .changes()
      .run(connection)

    cursor.each(function (err, row) {
      if (err) {
        throw err
      }
      io.emit(eventName, row)
    })
  } catch( err ) {
    console.error(err);
  }
}

```

在此函数中，我们将`rethinkdb`导入为`rdb`，将我们的 RethinkDB 数据库连接导入为`rdbConnection`，然后将以下项目用作此函数的参数：

+   Socket.IO 服务器的实例

+   您将要使用的 Socket.IO 发出的自定义事件名称

+   您要订阅其 changefeed 的 RethinkDB 表名

changefeed 将以回调的形式将文档返回为游标对象，因此我们通过游标对象进行迭代，并使用自定义事件名称发出每个文档的行。

1.  在应用程序根目录的`/public/`目录中将`changefeeds`函数导入为`rdbChangeFeeds`，并将其与`index.js`文件中的其余现有代码集成，如下所示：

```js
// public/index.js
import Koa from 'koa'
import socket from 'socket.io'
import http from 'http'
import config from 'Configs'
import middlewares from '../middlewares'
import rdbChangeFeeds from 'Core/database/rethinkdb/changefeeds'

const app = new Koa()
const host = process.env.HOST || '127.0.0.1'
const port = process.env.PORT || config.server.port
middlewares(app)

const server = http.createServer(app.callback())
const io = socket(server)
io.sockets.on('connection', socket => {
  //...
})

rdbChangeFeeds(io, 'user', 'user.changefeeds')
server.listen(port, host)
```

在上述代码中，我们要订阅的表名是`user`，我们要调用的发出事件名称是`user.changefeeds`。因此，我们将它们传递给`rdbChangeFeeds`函数，并使用`socket.io`实例。这就是您一次性全局集成 Socket.IO 和 RethinkDB 所需做的一切。

干得好！您已成功在服务器端集成了 Koa、RethinkDB 和 Socket.IO，并创建了一个实时 API。但是客户端怎么样，我们如何监听从 API 发出的事件？我们将在下一节中找出答案。

# 将 Socket.IO 与 Nuxt 集成

我们要构建的 Nuxt 应用程序与上一章中的应用程序非常相似，在那里我们有一个包含以下 CRUD 页面的`/users/`目录，该目录位于`/pages/`目录中，用于添加、更新、列出和删除用户：

```js
users
├── index.vue
├── _slug.vue
├── add
│ └── index.vue
├── update
│ └── _slug.vue
└── delete
    └── _slug.vue
```

您可以从上一章复制这些文件。这个应用程序中唯一的主要变化和不同之处在于`<script>`块，我们将通过监听来自 Socket.IO 服务器的 emit 事件实时列出用户。为此，我们需要使用 Socket.IO 客户端，这是您在*添加和使用 Socket.IO 服务器和客户端*部分学到的，该部分使用简单的 HTML 页面。因此，让我们看看如何将我们已经知道的内容实现到 Nuxt 应用中：

1.  通过 npm 将 Socket.IO 客户端安装到您的 Nuxt 项目中：

```js
$ npm i socket.io-client
```

1.  在 Nuxt 配置文件中创建以下变量，以便稍后重用应用的协议、主机名和跨域端口：

```js
// nuxt.config.js
const protocol = 'http'
const host = process.env.NODE_ENV === 'production' ? 'a-cool-domain-name.com' : 'localhost'

const ports = {
  local: '8000',
  remote: '4000'
}

const remoteUrl = protocol + '://' + host + ':' + ports.remote + '/'
```

这些变量适用于以下情况：

+   `host`变量用于在 Nuxt 应用处于生产环境时获取`a-cool-domain-name.com`的值；也就是说，当您使用`npm run start`运行应用时。否则，它只会将`localhost`作为默认值。

+   `ports`变量中的`local`键用于为 Nuxt 应用设置服务器端口，设置为`8000`。请记住，Nuxt 提供应用的默认端口是`3000`。

+   `ports`变量中的`remote`键用于告诉 Nuxt 应用 API 所在的服务器端口，即`4000`。

+   `remoteUrl`变量用于将 API 与前面的变量连接起来。

1.  将前述变量应用于 Nuxt 配置文件中的`env`和`server`选项，如下所示：

```js
// nuxt.config.js
export default {
  env: {
    remoteUrl
  },
  server: {
    port: ports.local,
    host: host
  }
}
```

因此，通过这种配置，我们可以通过以下方法再次访问`remoteUrl`变量：

+   `process.env.remoteUrl`

+   `context.env.remoteUrl`

此外，在这个配置中，我们已将 Nuxt 应用的默认服务器端口更改为`8000`，在`server`选项中。默认端口是`3000`，默认主机是`localhost`。但是您可能出于某种原因想要使用不同的端口。这就是为什么我们在这里看如何更改它们。

如果您想了解更多关于`server`配置和其他选项（如`timing`和`https`）的信息，请访问[`nuxtjs.org/api/configuration-server`](https://nuxtjs.org/api/configuration-server)。

如果您想了解更多关于`env`配置的信息，请访问[`nuxtjs.org/api/configuration-envthe-env-property`](https://nuxtjs.org/api/configuration-env#the-env-property)。

1.  安装 Nuxt Axios 和 Proxy 模块，并在 Nuxt 配置文件中进行配置，如下所示：

```js
// nuxt.config.js
export default {
  modules: [
    '@nuxtjs/axios'
  ],

  axios: {
    proxy: true
  },

  proxy: {
    '/api/': {
      target: remoteUrl,
      pathRewrite: {'^/api/': ''}
    }
  }
}
```

请注意，我们在`proxy`选项中重用了`remoteUrl`变量。因此，我们发出的每个以`/api/`开头的 API 请求都将转换为`http://localhost:4000/api/`。但由于我们在 API 的路由中没有`/api/`，在将其发送到 API 之前，我们使用`pathRewrite`选项从请求 URL 中删除这个`/api/`部分。

1.  在`/plugin/`目录中创建一个插件，用于抽象 Socket.IO 客户端的实例，以便我们可以在任何地方重用它：

```js
// plugins/socket.io.js
import io from 'socket.io-client'

const remoteUrl = process.env.remoteUrl
const socket = io(remoteUrl)

export default socket
```

请注意，我们通过`process.env.remoteUrl`重用了`remoteUrl`变量在 Socket.IO 客户端实例中。这意味着 Socket.IO 客户端将在`localhost:4000`调用 Socket.IO 服务器。

1.  将`socket.io`客户端插件导入到`<script>`块中，并使用`@nuxtjs/axios`模块在`index`文件中获取用户列表。此索引文件位于`/users/`目录下的`pages`中：

```js
// pages/users/index.vue
import socket from '~/plugins/socket.io'

export default {
  async asyncData ({ error, $axios }) {
    try {
      let { data } = await $axios.get('/api/users')
      return { users: data.data }
    } catch (err) {
      // Handle the error.
    }
  }
}
```

1.  使用`asyncData`方法获取并设置用户后，使用 Socket.IO 插件在`mounted`方法中监听`user.changefeeds`事件，以获取来自服务器的任何新的实时反馈，如下所示：

```js
// pages/users/index.vue
export default {
  async asyncData ({ error, $axios }) {
    //...
  },
  mounted () {
    socket.on('user.changefeeds', data => {
      if (data.new_val === undefined && data.old_val === undefined) {
        return
      }
      //...
    })
  }
}
```

在这里，您可以看到我们始终检查`data`回调，以确保传入的反馈中`new_val`和`old_val`被定义。换句话说，在继续下一行之前，我们希望确保这两个键始终出现在反馈中。

1.  检查后，如果我们在`new_val`键中收到数据，但`old_val`键为空，这意味着新用户已添加到服务器。如果我们从服务器端获取新的反馈，我们将使用 JavaScript 的`unshift`函数将新用户数据添加到`user`数组的顶部，如下所示：

```js
// pages/users/index.vue
mounted () {
  //...
  if(data.old_val === null && data.new_val !== null) {
    this.users.unshift(data.new_val)
  }
}
```

然后，如果我们在`old_val`键中收到数据，但`new_val`键为空，这意味着现有用户已从服务器中删除。因此，要从数组中弹出现有用户，我们可以使用 JavaScript 的`splice`函数，通过其索引（在数组中的位置/位置）来删除。但首先，我们必须使用 JavaScript 的`map`函数按其 ID 找到用户的索引，如下所示：

```js
// pages/users/index.vue
mounted () {
  //...
  if(data.new_val === null && data.old_val !== null) {
    var id = data.old_val.id
    var index = this.users.map(el => {
      return el.id
    }).indexOf(id)
    this.users.splice(index, 1)
  }
}
```

最后，如果我们在`new_val`和`old_val`键中都收到数据，这意味着当前用户已更新。因此，如果用户已更新，我们必须首先找到数组中用户的索引，然后使用 JavaScript 的`splice`函数替换它，如下所示：

```js
// pages/users/index.vue
mounted () {
  //...
  if(data.new_val !== null && data.old_val !== null) {
    var id = data.new_val.id
    var index = this.users.findIndex(item => item.id === id)
    this.users.splice(index, 1, data.new_val)
  }
}
```

请注意，我们使用 JavaScript 的`findIndex`函数作为`map`函数的另一种替代方法。

如果您想了解更多关于我们在这里使用的用于操作 JavaScript 数组的 JavaScript 标准内置函数的信息，请访问以下链接：

+   [`developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/unshift`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/unshift) 用于`unshift` 函数

+   [`developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice) 用于`splice` 函数

+   [`developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 用于`map` 函数

+   [`developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/findIndex`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/findIndex) 用于`findIndex` 函数

1.  将以下模板添加到`<template>`块中以显示用户，如下所示：

```js
// pages/users/index.vue
<div>
  <h1>Users</h1>
  <ul>
    <li v-for="user in users" v-bind:key="user.uuid">
      <nuxt-link :to="'/users/' + user.slug">
        {{ user.name }}
      </nuxt-link>
    </li>
  </ul>
  <nuxt-link to="/users/add">
    Add New
  </nuxt-link>
</div>
```

在此模板中，您可以看到我们只是简单地使用`v-for`从`asyncData`方法获取的用户数据，并将用户`uuid`绑定到每个循环的元素上。之后，发生在`mounted`方法中的任何实时反馈都将以响应式方式更新用户数据和模板。

1.  使用`npm run dev`运行 Nuxt 应用程序。您应该在终端上看到以下信息：

```js
Listening on: http://localhost:8000/
```

1.  在浏览器中打开两个标签并排，或者在两个不同的浏览器中并排，并将它们指向`localhost:8000/users`。从其中一个标签（或浏览器）在`localhost:8000/users/add`添加一个新用户。您应该看到新添加的用户立即并同时显示在所有标签（或浏览器）上，而无需刷新它们。

您可以在本书的 GitHub 存储库中的`/chapter-17/frontend/`和`/chapter-17/backend/`中找到本章中的所有代码和应用程序。

干得好 - 您成功了！我们希望您觉得这个应用程序有趣且易于操作，并且它能激励您进一步探索您迄今为止所学到的知识。让我们总结一下本章学到的内容。

# 摘要

在本章中，您成功安装并使用 RethinkDB 和 Socket.IO，将普通的后端 API 和前端 Nuxt 应用程序转变为实时应用程序。您学会了如何通过 RethinkDB 管理 UI 在服务器端创建、读取、更新和删除 JSON 数据，然后使用 Koa 与 RethinkDB 客户端驱动程序。最重要的是，您学会了如何通过 RethinkDB 管理 UI 操作 RethinkDB 中的实时订阅，称为 changefeeds，然后在服务器端将其与 Socket.IO 服务器和 Koa 集成。此外，您使用 Socket.IO 服务器发出自定义事件的数据，并使用 Socket.IO 客户端在 Nuxt 应用程序的客户端端实时监听事件并捕获数据。这难道不是一次有趣的旅程吗？

在下一章中，我们将通过第三方 API、内容管理系统（CMS）和 GraphQL 进一步了解 Nuxt。您将了解 WordPress API、Keystone 和 GraphQL。然后，您将学习如何创建自定义内容类型和自定义路由，以扩展 WordPress API，以便将其与 Nuxt 集成，并从 WordPress 项目中流式传输远程图像。您将使用 Keystone 开发自定义 CMS，安装和保护用于 Keystone 应用程序开发的 PostgreSQL，以及保护 MongoDB，您将学习如何在第九章中安装的内容，即*添加服务器端数据库*。最重要且令人兴奋的是，您将学习 REST API 和 GraphQL API 之间的区别；使用 GraphQL.js、Express 和 Apollo Server 构建 GraphQL API；了解 GraphQL 模式及其解析器；使用 Keystone GraphQL API；然后将它们与 Nuxt 集成。这绝对会是另一次有趣的旅程，所以系好安全带，准备好！
