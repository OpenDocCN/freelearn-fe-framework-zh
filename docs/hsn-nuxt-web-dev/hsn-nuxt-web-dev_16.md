创建用户登录和 API 身份验证

在过去的两章中，我们开始在 Nuxt 应用程序中使用会话和 JSON Web Token（JWT）进行身份验证。我们在第十章中使用会话进行身份验证，*添加 Vuex Store*，以练习`nuxtServerInit`。然后我们在第十一章中使用会话和令牌一起进行身份验证，*编写路由中间件和服务器中间件*，以练习按路由中间件，例如：

```js
// store/index.js
nuxtServerInit({ commit }, { req }) {
  if (req.ctx.session && req.ctx.session.authUser) {
    commit('setUser', req.ctx.session.authUser)
  }
}

// middleware/token.js
export default async ({ store, error }) => {
  if (!store.state.auth.token) {
    // handle error
  }
  axios.defaults.headers.common['Authorization'] = Bearer: ${store.state.auth.token}
}
```

如果您是新手，它们可能会让人感到不知所措，但不用担心。简而言之，身份验证是验证您是谁的过程。身份验证系统允许您在您的凭据与数据库或数据身份验证服务器中的凭据匹配时访问资源。有几种身份验证方法。基于会话和基于令牌的身份验证是最常见的，或者这两种的组合。所以，让我们深入了解它们。

本章我们将涵盖以下主题：

+   理解基于会话的身份验证

+   理解基于令牌的身份验证

+   创建后端身份验证

+   创建前端身份验证

+   使用 Google OAuth 进行登录

# 第十二章：理解基于会话的身份验证

超文本传输协议（HTTP）是无状态的。因此，所有 HTTP 请求都是无状态的。这意味着它不记住任何我们已经验证过的东西或任何用户，我们的应用程序也不知道它是否是上一个请求的同一个人。因此，我们将不得不在下一个请求上再次进行身份验证。这并不理想。

因此，基于会话和基于 Cookie 的身份验证（通常仅称为基于会话的身份验证）被引入以在 HTTP 请求之间存储用户数据，以消除 HTTP 请求的无状态性质。它们使身份验证过程“有状态”。这意味着经过身份验证的记录或会话存储在服务器和客户端两侧。服务器可以将活动会话保存在数据库或服务器内存中，因此它被称为基于会话的身份验证。客户端可以创建一个 Cookie 来保存会话标识符（会话 ID），因此它被称为基于 Cookie 的身份验证。

但是会话和 Cookie 到底是什么？让我们在接下来的章节中深入了解它们。

## 什么是会话和 Cookie？

会话是在两个或多个通信设备之间，或者在计算机和用户之间交换的临时信息片段。它在特定时间建立，然后在将来的某个时间到期。当用户关闭浏览器或离开网站时，会话也会到期。建立会话时，在服务器的临时目录（或数据库或服务器内存）中创建一个文件，用于存储注册的会话值。然后在整个访问期间，这些数据都可用，并且浏览器会接收一个会话 ID，该 ID 将通过 cookie 或`GET`变量发送回服务器进行验证。

简而言之，cookie 和会话只是数据。Cookie 仅存储在客户端机器上，而会话既存储在客户端又存储在服务器上。会话被认为比 cookie 更安全，因为数据可以仅保存在服务器上。当会话建立时通常会创建 cookie，并且它们保存在客户端计算机上。它们可以是经过身份验证的用户的名称、年龄或 ID，并且由浏览器发送回服务器以识别用户。让我们在下一节通过示例流程来看看它们是如何工作的。

## 会话身份验证流程

基于会话和基于 cookie 的身份验证可以通过以下示例身份验证流程来理解：

1.  用户从其浏览器上的客户端应用程序发送其凭据，例如用户名和密码，到服务器。

1.  服务器检查凭据并向客户端发送一个唯一的令牌（会话 ID）。此令牌还将保存在服务器端的数据库或内存中。

1.  客户端应用程序将令牌存储在客户端的 cookie 中，并在每个 HTTP 请求中使用它并发送回服务器。

1.  服务器接收令牌并对用户进行身份验证，然后将请求的数据返回给客户端应用程序。

1.  客户端应用程序在用户注销时销毁令牌。在注销之前，客户端还可以向服务器发送请求以删除会话，或者会话将根据设置的到期时间自行结束。

在基于会话的身份验证中，服务器承担了所有繁重的工作。它是有状态的。它将会话标识符与用户账户关联起来（例如，在数据库中）。基于会话的身份验证的缺点是，在大量用户同时使用系统时，可伸缩性会受到影响，因为会话存储在服务器的内存中，因此涉及大量的内存使用。此外，cookie 在单个域或子域上运行良好，但通常在跨域共享（跨域资源共享）时被浏览器禁用。因此，当客户端从不同的域中进行 API 请求时，这会给客户端造成问题。但是，这个问题可以通过基于令牌的身份验证来解决，我们将在下一节中详细介绍。

# 理解基于令牌的身份验证

基于令牌的身份验证更简单。有一些令牌的实现，但是 JSON Web Tokens 是最常见的一种。基于令牌的身份验证是无状态的。这意味着服务器端不会保留任何会话，因为状态存储在客户端的令牌中。服务器的责任只是使用秘钥创建一个 JWT 并将其发送给客户端。客户端将 JWT 存储在本地存储中，或者客户端的 cookie 中，并在发出请求时将其包含在标头中。服务器然后验证 JWT 并发送响应。

但是 JWT 是什么，它是如何工作的？让我们在下一节中找出答案。

## 什么是 JSON Web Tokens？

要理解 JWT 的工作原理，我们首先应该了解它是什么。简而言之，JWT 是一个由标头、有效载荷和签名组成的哈希 JSON 对象的字符串。JWT 的生成格式如下：

```js
header.payload.signature
```

标头通常由两部分组成：类型和算法。类型是 JWT，算法可以是 HMAC、SHA256 或 RSA，这是一种使用秘钥对令牌进行签名的哈希算法，例如：

```js
{
  "typ": "JWT",
  "alg": "HS256"
}
```

有效载荷是 JWT 中存储信息（或声明）的部分，例如：

```js
{
  "userId": "b08f86af-35da-48f2-8fab-cef3904660bd",
  "name": "Jane Doe"
}
```

在这个例子中，我们在有效载荷中只包括了两个声明。您可以放置任意多个声明。您包含的声明越多，JWT 的大小就越大，这可能会影响性能。还有其他可选的声明，比如`iss`（发行者）、`sub`（主题）和`exp`（过期时间）。

如果您想了解有关 JWT 标准字段的更多详细信息，请访问[`tools.ietf.org/html/rfc7519`](https://tools.ietf.org/html/rfc7519)。

签名是使用编码的标头、编码的有效负载、一个密钥和标头中指定的算法计算的。无论您在标头部分选择了什么算法，您必须使用该算法来加密 JWT 的前两部分：`base64(header) + '.' + base64(payload)`，例如，在这个伪代码中：

```js
// signature algorithm
data = base64urlEncode(header) + '.' + base64urlEncode(payload)
hashedData = hash(data, secret)
signature = base64urlEncode(hashedData)
```

签名是 JWT 中唯一不公开可读的部分，因为它是用一个秘钥加密的。除非有人有秘钥，否则他们无法解密这些信息。因此，前面伪代码的示例输出是由三个由点分隔的 Base64-URL 字符串，可以在 HTTP 请求中轻松传递。

```js
// JWT Token
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VySWQiOiJiMDhmODZhZi0zNWRhLTQ4ZjItOGZhYi1jZWYzOTA0NjYwYmQifQ.-xN_h82PHVTCMA9vdoHrcZxH-x5mb11y1537t3rGzcM
```

让我们在下一节看看这个令牌认证是如何工作的，附带一个示例流程。

## 令牌认证流程

基于令牌的认证可以通过以下示例认证流程来理解：

1.  用户从他们的浏览器上的客户端应用发送他们的凭据，例如用户名和密码，到服务器。

1.  服务器检查用户名和密码，如果凭据正确，则返回一个签名令牌（JWT）。

1.  这个令牌存储在客户端。它可以存储在本地存储、会话存储或者 cookie 中。

1.  客户端应用通常会在任何后续请求到服务器时将该令牌作为附加标头包含进去。

1.  服务器接收并解码 JWT，然后如果令牌有效就允许请求访问。

1.  当用户注销并且不再需要与服务器进行进一步交互时，令牌将在客户端销毁。

在基于令牌的认证中，通常不应在有效负载中包含任何敏感信息，并且令牌不应保留太长时间。您用于包含令牌的附加标头应该是这种格式：

```js
Authorization: Bearer <token>
```

基于令牌的认证中的可扩展性不是一个问题，因为令牌存储在客户端。跨域共享也不是一个问题，因为 JWT 是一个包含所有必要信息的字符串，包含在请求标头中，由服务器检查每个客户端发出的请求。在 Node.js 应用中，我们可以使用 Node.js 模块之一，比如`jsonwebtoken`，来为我们生成令牌。让我们在下一节看看我们如何使用这个 Node.js 模块。

## 使用 Node.js 模块进行 JWT

正如我们之前提到的，`jsonwebtoken`可以用于在服务器端生成 JWT。您可以在以下简化的步骤中同步或异步地使用这个模块：

1.  通过 npm 安装`jsonwebtoken`：

```js
$ npm i jsonwebtoken
```

1.  在服务器端导入并签署令牌：

```js
import jwt from 'jsonwebtoken'
var token = jwt.sign({ name: 'john' }, 'secret', { expiresIn: '1h' })
```

1.  在服务器端异步验证来自客户端的令牌：

```js
try {
  var verified = jwt.verify(token, 'secret')
} catch(err) {
  // handle error
}
```

如果您想了解有关此模块的更多信息，请访问[`github.com/brianloveswords/node-jws`](https://github.com/brianloveswords/node-jws)。

所以，现在您对基于会话和基于令牌的身份验证有了基本的了解，我们将指导您如何在使用 Koa 和 Nuxt 的服务器端和客户端应用程序中应用它们。在本章中，我们将使用基于令牌的身份验证在我们的应用程序中创建两种身份验证选项：本地身份验证和 Google OAuth 身份验证。本地身份验证是我们在应用程序内部和本地验证用户的选项，而 Google OAuth 身份验证是我们使用 Google OAuth 验证用户的选项。所以，让我们在接下来的章节中找出来！

# 创建后端身份验证

在第十章和第十一章中的先前练习，*添加 Vuex 存储*和*编写路由中间件和服务器中间件*，我们在后端身份验证中使用了一个虚拟用户，特别是在`/chapter-11/nuxt-universal/route-middleware/per-route/`中用于每个路由中间件的虚拟用户，例如：

```js
// server/modules/public/user/_routes/login.js
router.post('/login', async (ctx, next) => {
  let request = ctx.request.body || {}

  if (request.username === 'demo' && request.password === 'demo') {
    let payload = { id: 1, name: 'Alexandre', username: 'demo' }
    let token = jwt.sign(payload, config.JWT_SECRET, { expiresIn: 1 * 60 })
    //...
  }
})
```

但在本章中，我们将使用一个带有一些用户数据的数据库进行身份验证。此外，在第九章中，*添加服务器端数据库*，我们使用 MongoDB 作为我们的数据库服务器。但这一次，让我们尝试一种不同的数据库系统，以增加多样性 – **MySQL**。所以，让我们开始吧。

## 使用 MySQL 作为服务器数据库

确保您的本地计算机上安装了 MySQL 服务器。在撰写本书时，最新的 MySQL 版本是 5.7。根据您使用的操作系统，您可以在[`dev.mysql.com/doc/mysql-installation-excerpt/5.7/en/installing.html`](https://dev.mysql.com/doc/mysql-installation-excerpt/5.7/en/installing.html)找到系统的具体指南。如果您使用的是 Linux，您可以在[`dev.mysql.com/doc/mysql-installation-excerpt/5.7/en/linux-installation.html`](https://dev.mysql.com/doc/mysql-installation-excerpt/5.7/en/linux-installation.html)找到 Linux 发行版的安装指南。如果您使用的是 Linux Ubuntu 并且使用 APT 存储库，您可以按照[`dev.mysql.com/doc/mysql-apt-repo-quick-guide/en/apt-repo-fresh-install`](https://dev.mysql.com/doc/mysql-apt-repo-quick-guide/en/#apt-repo-fresh-install)中的指南操作。

或者，您可以安装 MariaDB 服务器，而不是 MySQL 服务器，以在项目中使用关系数据库管理系统（DBMS）。同样，根据您使用的操作系统，您可以在[`mariadb.com/downloads/`](https://mariadb.com/downloads/)找到系统的具体指南。如果您使用的是 Linux，您可以在[`downloads.mariadb.org/mariadb/repositories/`](https://downloads.mariadb.org/mariadb/repositories/)找到特定 Linux 发行版的指南。如果您使用的是 Linux Ubuntu 19.10，您可以按照[`downloads.mariadb.org/mariadb/repositories/#distro=Ubuntu&distro_release=eoan--ubuntu_eoan&mirror=bme&version=10.4`](https://downloads.mariadb.org/mariadb/repositories/#distro=Ubuntu&distro_release=eoan--ubuntu_eoan&mirror=bme&version=10.4)中的指南操作。

无论您选择哪种方式，都很方便在浏览器中使用管理工具来管理您的 MySQL 数据库。您可以使用 phpMyAdmin 或 Adminer（[`www.adminer.org/latest.php`](https://www.adminer.org/latest.php)）；两者都需要在您的计算机上安装 PHP。如果您对 PHP 不熟悉，可以在第十六章中使用安装指南，*为 Nuxt 创建一个与框架无关的 PHP API*。本书中更倾向于使用 Adminer。您可以在[`www.phpmyadmin.net/downloads/`](https://www.phpmyadmin.net/downloads/)下载该程序。如果您想使用 phpMyAdmin，请访问[`www.phpmyadmin.net/`](https://www.phpmyadmin.net/)了解更多信息。一旦您有了管理工具，请按照以下步骤设置我们在本章中将需要的数据库：

1.  使用 Adminer 创建一个名为“nuxt-auth”的数据库。

1.  在数据库中插入以下表格和示例数据：

```js
DROP TABLE IF EXISTS users;
CREATE TABLE users (
  id int(11) NOT NULL AUTO_INCREMENT,
  name varchar(255) NOT NULL,
  email varchar(255) NOT NULL,
  username varchar(255) NOT NULL,
  password varchar(255) NOT NULL,
  created_on datetime NOT NULL,
  last_on datetime NOT NULL,
  PRIMARY KEY (id),
  UNIQUE KEY email (email),
  UNIQUE KEY username (username)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

INSERT INTO users (id, name, email, username, password, created_on, last_on) VALUES
(1, 'Alexandre', 'demo@gmail.com', 'demo', '$2a$10$pyMYtPfIvE.PAboF3cIx9.IsyW73voMIRxFINohzgeV0I2BxwnrEu', '2019-06-17 00:00:00', '2019-01-21 23:32:58');
```

前面示例数据中的用户密码是`123123`，并且以`$2a$10$pyMYtPfIvE.PAboF3cIx9.IsyW73voMIRxFINohzgeV0I2BxwnrEu`的形式进行了 bcrypt 加密。我们将安装并使用`bcryptjs` Node.js 模块来在服务器端对此密码进行哈希和验证。但在跳转到`bcryptjs`之前，让我们先看一下我们将在下一节中创建的应用程序的结构。

您可以在我们的 GitHub 存储库的`/chapter-12/`中找到我们导出的数据库副本`nuxt-auth.sql`。

## 跨域应用目录结构

我们一直在为单个域制作 Nuxt 应用程序。自从第八章以来，我们的服务器端 API 与 Nuxt 紧密耦合，*添加服务器端框架*，在这一章中，我们使用 Koa 作为处理和为 Nuxt 应用程序提供数据的服务器端框架和 API。如果你回顾一下我们在 GitHub 存储库中的`/chapter-8/nuxt-universal/koa-nuxt/`，你应该记得我们将服务器端程序和文件保存在`/server/`目录中。我们还将我们的包/模块依赖项保存在一个`package.json`文件中，并在同一个`/node_modules/`目录中安装它们。当我们的应用程序变得更大时，混合两个框架（Nuxt 和 Koa）的模块依赖项在同一个`package.json`文件中可能会令人困惑。这也可能使调试过程变得更加困难。因此，将由 Nuxt 和 Koa（或其他服务器端框架，如 Express）制作的单个应用程序分开可能更有利于可扩展性和维护。现在，是时候制作一个跨域 Nuxt 应用程序了。我们将重用并重组我们在第八章中制作的 Nuxt 应用程序，*添加服务器端框架*。让我们称我们的 Nuxt 应用程序为前端应用程序，Koa 应用程序为后端应用程序。随着我们的进展，我们将分别在这两个应用程序中添加新的模块。

后端应用程序将进行后端身份验证，而前端应用程序将分别进行前端身份验证，但最终它们将作为一个整体。为了使您更容易学习和重组这个过程，我们将仅使用 JWT 进行身份验证。因此，让我们按照以下步骤创建我们的新工作目录：

1.  创建一个项目目录，并以您喜欢的任何名称命名，其中包含两个子目录。一个称为`frontend`，另一个称为`backend`，如下所示：

```js
<project-name>
├── frontend
└── backend
```

1.  使用脚手架工具`create-nuxt-app`在`/frontend/`目录中安装 Nuxt 应用程序，以便获得您已经熟悉的 Nuxt 目录，如下所示：

```js
frontend
├── package.json
├── nuxt.config.js
├── store
│ ├── index.js
│ └── ...
└── pages
    ├── index.vue
    └── ...
```

1.  在`/backend/`目录中创建一个`package.json`文件，一个`backpack.config.js`文件，一个`/static/`文件夹和一个`/src/`文件夹，然后在`/src/`文件夹中按照以下方式添加其他文件和子文件夹（我们将在接下来的部分中更详细地介绍它们）：

```js
backend
├── package.json
├── backpack.config.js
├── assets
│ └── ...
├── static
│ └── ...
└── src
    ├── index.js
    ├── ...
    ├── modules
    │ └── ...
    └── core
        └── ...
```

后端目录是我们的 API 所在的地方，可以使用 Express 或 Koa 来创建。我们仍然会使用 Koa，这是您已经熟悉的。我们将在这个目录中安装服务器端的依赖，比如`mysql`、`bcryptjs`和`jsonwebtoken`，这样它们就不会与 Nuxt 应用的前端模块混在一起。

正如您所看到的，在这种新的结构中，我们成功地完全分离和解耦了我们的 API 和 Nuxt 应用。这对于调试和开发有好处。从技术上讲，我们现在将一次开发和测试一个应用。在单个环境中开发两个应用可能会令人困惑，当应用变得更大时，协作可能会变得困难，就像我们之前提到的那样。

在深入研究如何在服务器端使用 JWT 之前，让我们首先在下一节深入研究如何在`/src/`目录中结构化 API 路由和模块。

## 创建 API 公共/私有路由及其模块

请注意，在本书中，不是强制遵循此处建议的目录结构。关于如何使用 Koa 来构建应用程序的官方或任意规则是没有的。Koa 社区提供了一些骨架、样板和框架，您可以访问[`github.com/koajs/koa/wiki`](https://github.com/koajs/koa/wiki)了解更多信息。现在让我们更仔细地看一下`/src/`目录中的目录结构，在接下来的步骤中，我们将在这里开发我们的 API 源代码。

1.  按照以下方式在`/src/`目录中创建以下文件夹和空的`.js`文件：

```js
└── src
    ├── index.js
    ├── middlewares.js
    ├── routes-private.js
    ├── routes-public.js
    ├── config
    │ └── index.js
    ├── core
    │ └── database
    ├── middlewares
    │ ├── authenticate.js
    │ ├── errorHandler.js
```

```js
    │ └── ...
    └── modules
        └── ...
```

在`/src/`目录中，`/middlewares/`目录是存放所有中间件的地方，比如`authenticate.js`，我们希望将其注册到 Kao 的`app.use`方法中，而`/modules/`目录是存放所有 API 端点组的地方，比如`home`、`user`和`login`。

1.  创建两个主要目录，`private`和`public`，每个目录中都有子目录，如下所示：

```js
└── modules
    ├── private
    │ └── home
    └── public
        ├── home
        ├── user
        └── login
```

`/public/`目录用于无需 JWT 的公共访问，例如登录路由，而`/private/`目录用于需要 JWT 保护模块的访问。正如你所看到的，我们已将 API 路由分为两个主要组，因此`/private/`组将在`routes-private.js`中处理，而`/public/`组将在`routes-public.js`中处理。我们有`/config/`目录来保存所有配置文件，以及`/core/`目录来保存可以在整个应用程序中共享和使用的抽象程序或模块，例如你将在本章后面发现的 mysql 连接池。因此，从前面的目录树中，我们将在我们的 API 中使用这些公共模块：`home`，`user`，`login`，以及一个私有模块：`home`。

1.  在每个模块中，例如`user`模块，创建一个`/_routes/`目录来配置属于该特定模块（或组）的所有路由（或端点）：

```js
└── user
    ├── index.js
    └── _routes
        ├── index.js
        └── fetch-user.js
```

在`user`模块中，`/user/index.js`文件是该模块的所有路由被组装和分组的地方，例如：

```js
// src/modules/public/user/index.js
import Router from 'koa-router'
import fetchUsers from './_routes'
import fetchUser from './_routes/fetch-user'

const router = new Router({
  prefix: '/users'
})
const routes = [fetchUsers, fetchUser]

for (var route of routes) {
  router.use(route.routes(), route.allowedMethods())
}
```

`prefix`键设置为`/users`是该用户模块的模块路由。在每个导入的子路由内部是我们开发代码的地方，例如登录路由的代码。

1.  在每个模块的每个`.js`文件中，例如`user`模块，添加以下用于在后期构建我们的代码的基本代码结构：

```js
// src/modules/public/user/_routes/index.js
import Router from 'koa-router'
import pool from 'core/database/mysql'

const router = new Router()

router.get('/', async (ctx, next) => {
  // code goes here....
})
export default router
```

1.  让我们创建`home`模块，它将返回一个包含`'Hello World!'`消息的响应。

```js
// src/modules/public/home/_routes/index.js
import Router from 'koa-router'
const router = new Router()

router.get('/', async (ctx, next) => {
  ctx.type = 'json'
  ctx.body = {
    message: 'Hello World!'
  }
})
export default router
```

1.  `home`模块只有一个路由，但我们仍然需要在该模块的`index.js`文件中组装此路由，以便我们的代码与其他模块保持一致，如下所示：

```js
// src/modules/public/home/index.js
import Router from 'koa-router'
import index from './_routes'

const router = new Router() // no prefix
const routes = [index]

for (var route of routes) {
  router.use(route.routes(), route.allowedMethods())
}
export default router
```

请注意，此`home`模块未添加前缀，因此我们可以直接在`localhost:4000/public`上访问其唯一路由。

1.  在`/src/`目录中创建`routes-public.js`文件，并从`/modules/`目录中的公共模块导入所有公共路由，如下所示：

```js
// src/routes-public.js
import Router from 'koa-router'

import home from './modules/public/home'
import user from './modules/public/user'
import login from './modules/public/login'

const router = new Router({ prefix: '/public' })
const modules = [home, user, login]

for (var module of modules) {
  router.use(module.routes(), module.allowedMethods())
}
export default router
```

正如你所看到的，我们导入了刚刚创建的`home`模块。我们将在接下来的部分中创建`user`和`login`模块。导入这些模块后，我们应该将它们的路由注册到路由器，然后导出路由器。请注意，这些路由都添加了前缀`/public`。还要注意，每个路由都使用纯 JavaScript 的`for`循环函数进行循环注册到路由器。

1.  在`/src/`目录中创建`routes-private.js`文件，并从`/modules/`目录中导入所有私有模块中的私有路由，如下所示：

```js
// src/routes-private.js
import Router from 'koa-router'

import home from './modules/private/home'
import authenticate from './middlewares/authenticate'

const router = new Router({ prefix: '/private' })
const modules = [home]

for (var module of modules) {
  router.use(authenticate, module.routes(), module.allowedMethods())
}
export default router
```

在这个文件中，你可以看到我们将在接下来的章节中只创建一个私有`home`模块。此外，这个文件中导入了一个`authenticate`中间件，并将其添加到私有路由中，以便保护私有模块。之后，我们应该导出带有路由的私有路由，并用`/private`前缀。我们也将在接下来的章节中创建这个`authenticate`中间件。现在，让我们用 Backpack 配置我们的模块文件路径，并安装我们的 API 基本依赖的 Node.js 模块。

1.  通过 Backpack 配置文件向 webpack 配置中添加以下额外的文件路径(`./src`, `./src/core`, 和 `./src/modules`)：

```js
// backpack.config.js
module.exports = {
  webpack: (config, options, webpack) => {
    config.resolve.modules = ['./src', './src/core',
      './src/modules']
    return config
  }
}
```

有了这些额外的文件路径，我们可以简单地用`import pool from 'core/database/mysql'`导入我们的模块，而不是以下方式：

```js
import pool from '../../../../core/database/mysql'
```

有关使用 webpack 中的`modules`选项解析模块的更多信息，请访问[`webpack.js.org/configuration/resolve/#resolvemodules`](https://webpack.js.org/configuration/resolve/#resolvemodules)。

1.  现在我们应该在我们的项目中安装 Backpack，以及其他基本和必要的 Node.js 模块，以便开发这个后端应用程序：

```js
$ npm i backpack-core
$ npm i cross-env
$ npm i koa
$ npm i koa-bodyparser
$ npm i koa-favicon
$ npm i koa-router
$ npm i koa-static
```

你应该熟悉这些模块，因为你已经在第八章中学习过它们并安装了它们，*添加服务器端框架*，你可以在我们的 GitHub 存储库的`/chapter-8/nuxt-universal/koa-nuxt/`中重新访问它，还有第十章，*添加 Vuex Store*，在`/chapter-10/nuxt-universal/nuxtServerInit/`，以及第十一章，*编写路由中间件和服务器中间件*，在`/chapter-11/nuxt-universal/route-middleware/per-route/`。

1.  在`/backend/`目录中的`package.json`中添加以下运行脚本：

```js
// package.json 
{
  "scripts": {
    "dev": "backpack",
    "build": "backpack build",
    "start": "cross-env NODE_ENV=production node build/main.js"
  }
}
```

因此，`"dev"`运行脚本用于开发我们的 API，`"build"`运行脚本用于在完成时构建我们的 API，`"start"`脚本用于构建后为 API 提供服务。

1.  在`/config/`目录中的`index.js`文件中添加以下服务器配置：

```js
// src/config/index.js
export default {
  server: {
    port: 4000
  },
}
```

这个配置文件只有一个非常简单的配置，即服务器配置为在端口`4000`上运行。

1.  导入您刚刚安装的以下模块，并在`/src/`目录中的`middlewares.js`文件中注册它们如下：

```js
// src/middlewares.js
import serve from 'koa-static'
import favicon from 'koa-favicon'
import bodyParser from 'koa-bodyparser'

export default (app) => {
  app.use(serve('assets'))
  app.use(favicon('static/favicon.ico'))
  app.use(bodyParser())
}
```

1.  在`/middlewares/`目录中创建一个处理具有`200` HTTP 状态的 HTTP 响应的中间件：

```js
// src/middlewares/okOutput.js
export default async (ctx, next) => {
  await next()
  if (ctx.status === 200) {
    ctx.body = {
      status: 200,
      data: ctx.body
    }
  }
}
```

如果响应正常，我们将获得以下 JSON 输出：

```js
{"status":200,"data":{"message":"Hello World!"}}
```

1.  创建一个处理 HTTP 错误状态（例如`400`、`404`和`500`）的中间件：

```js
export default async (ctx, next) => {
  try {
    await next()
  } catch (err) {
    ctx.status = err.status || 500

    ctx.type = 'json'
    ctx.body = {
      status: ctx.status,
      message: err.message
    }

    ctx.app.emit('error', err, ctx)
  }
}
```

对于`400`错误响应，您将获得以下 JSON 响应：

```js
{"status":400,"message":"username param is required."}
```

1.  创建一个专门处理 HTTP 404 响应的中间件，通过抛出一个'Not found'消息：

```js
// src/middlewares/notFound.js
export default async (ctx, next) => {
  await next()
  if (ctx.status === 404) {
    ctx.throw(404, 'Not found')
  }
}
```

对于未知路由，我们将获得以下 JSON 输出：

```js
{"status":404,"message":"Not found"}
```

1.  将这三个中间件导入`middlewares.js`并像其他中间件一样注册到 Koa 实例中：

```js
// src/middlewares.js
import errorHandler from './middlewares/errorHandler'
import notFound from './middlewares/notFound'
import okOutput from './middlewares/okOutput'

export default (app) => {
  app.use(errorHandler)
  app.use(notFound)
  app.use(okOutput)
}
```

请注意我们如何按顺序安排这些中间件 - 即使`errorHandler`中间件首先注册，但如果 HTTP 响应中出现错误，它将是最后一个重新执行的中间件。如果 HTTP 响应状态为`200`，上游级联将在`okOutput`中间件处停止。还要注意，这些中间件必须在`static`、`favicon`和`bodyparser`中间件之后注册，这些中间件必须首先在下游级联中调用和公开服务。

1.  从`routes-public.js`和`routes-private.js`导入公共和私有路由，并在前述中间件之后注册它们如下：

```js
// Import custom local middlewares.
import routesPublic from './routes-public'
import routesPrivate from './routes-private'

export default (app) => {
  app.use(routesPublic.routes(), routesPublic.allowedMethods())
  app.use(routesPrivate.routes(), routesPrivate.allowedMethods())
}
```

1.  在`/config/`目录中的`index.js`文件中导入 Koa、`middlewares.js`文件中的所有中间件和服务器配置，实例化一个 Koa 实例并将其传递给`middlewares.js`文件，然后使用这个 Koa 实例启动服务器：

```js
// index.js
import Koa from 'koa'
import config from './config'
import middlewares from './middlewares'

const app = new Koa()
const host = process.env.HOST || '127.0.0.1'
const port = process.env.PORT || config.server.port

middlewares(app)
app.listen(port, host)
```

1.  使用`npm run dev`运行此 API，您应该在`localhost:4000`上在浏览器中看到应用程序正在运行。当您在`localhost:4000`上时，您应该在浏览器中获得以下输出：

```js
{"status":404,"message":"Not found"}
```

这是因为在`/`上不再设置路由 - 我们已经将所有路由前缀设置为`/public`或`/private`。但是，如果您导航到`localhost:4000/public`，您将获得以下 JSON 输出：

```js
{"status":200,"data":{"message":"Hello World!"}}
```

这是我们刚刚在前面步骤中创建的`home`模块的响应。此外，您应该看到您的网站图标和资源在`localhost:4000`上正确提供 - 如果您将它们放在`/static/`和`/assets/`目录中的任何一个，例如：

```js
localhost:4000/sample-asset.jpg
localhost:4000/favicon.ico
```

您可以在`localhost:4000`这两个目录中看到您的文件。这是因为`static`和`favicon`中间件已安装并注册为在 Koa 中进行下游级联时首先执行的中间件堆栈。

干得好！现在您已经准备好了新的工作目录，并且基本的 API 正在运行，就像第八章中一样，*添加服务器端框架*。接下来，您需要在`/backend/`目录中安装其他服务器端依赖项，并开始向公共`user`和`login`模块以及私有`home`模块的路由添加代码。让我们从下一节开始使用`bcryptjs`。

您可以在我们的 GitHub 存储库中的`/chapter-12/nuxt-universal/cross-domain/jwt/axios-module/backend/`中找到具有前述结构的示例应用程序。

## 使用 Node.js 的 bcryptjs 模块

如前所述，`bcryptjs`用于对密码进行哈希和验证。请查看有关如何在我们的应用程序中使用此模块的进一步建议的简化步骤：

1.  通过 npm 安装`bcryptjs`模块：

```js
$ npm i bcryptjs
```

1.  通过在请求体（请求）中添加`salt`与来自客户端的密码一起对密码进行哈希处理，例如，在`user`模块中进行新用户创建时：

```js
// src/modules/public/user/_routes/create-user.js
import bcrypt from 'bcryptjs'

const saltRounds = 10
const salt = bcrypt.genSaltSync(saltRounds)
const hashed = bcrypt.hashSync(request.password, salt)
```

请注意，在本章中为了加快我们的身份验证课程，我们跳过了创建新用户的过程。但在更完整的 CRUD 中，您可以使用此步骤来对用户提供的密码进行哈希处理。

1.  通过将来自客户端的密码（请求）与数据库中存储的密码进行比较来验证密码，例如，在`login`模块中进行登录验证过程如下：

```js
// src/modules/public/login/_routes/local.js
import bcrypt from 'bcryptjs'

const isMatched = bcrypt.compareSync(request.password,
  user.password)
if (isMatched === false) { ... }
```

请注意，您可以在我们的 GitHub 存储库中的`/chapter-12/nuxt-universal/cross-domain/jwt/axios-module/backend/src/modules/public/login/_routes/local.js`中找到此步骤在我们后端应用程序中的应用方式。

我们将向您展示如何在接下来的部分中使用`bcryptjs`来验证来自客户端的密码。但在对客户端的密码进行哈希和验证之前，首先，我们需要连接到我们的 MySQL 数据库，以确定是要注入新用户还是查询现有用户。为此，我们将需要在我们的应用程序中使用下一个 Node.js 模块：mysql - 一个 MySQL 客户端。所以让我们继续前进到下一部分，看看您如何安装和使用它。

如果您想找到关于这个模块和一些异步示例的更多信息，请访问[`github.com/dcodeIO/bcrypt.js`](https://github.com/dcodeIO/bcrypt.js)。

## 使用 Node.js 的 mysql 模块

我们有在上一节中安装的 MySQL 服务器。现在我们需要一个 MySQL 客户端，我们可以连接到 MySQL 服务器并从服务器端程序执行 SQL 查询。mysql 是标准的 MySQL Node.js 模块，实现了 MySQL 协议，因此我们可以使用这个模块来处理 MySQL 连接和 SQL 查询，无论你是在 MySQL 服务器还是 MariaDB 服务器上。所以，让我们按照以下步骤开始：

1.  通过 npm 安装`mysql`模块：

```js
$ npm i mysql
```

1.  在`/src/`目录的子目录中，使用你的 MySQL 连接详细信息在`mysql.js`文件中创建 MySQL 连接实例，如下所示：

```js
// src/core/database/mysql.js
import util from 'util'
import mysql from 'mysql'

const pool = mysql.createPool({
  connectionLimit: 10,
  host : 'localhost',
  user : '<username>',
  password : '<password>',
  database : '<database>'
})

pool.getConnection((err, connection) => {
  if (error) {
    // Handle errors ...
  }
  // Release the connection to the pool if no error.
  if (connection) {
    connection.release()
  }
  return
})
pool.query = util.promisify(pool.query)
export default pool
```

让我们在以下笔记中浏览我们刚刚创建的代码：

+   mysql 不支持`async/await`，所以我们使用了 Node.js 的`promisify`实用程序来包装 MySQL 的`pool.query`。`pool.query`是 mysql 中处理我们的 SQL 查询的函数，它通过回调返回结果，例如：

```js
connection.query('SELECT ...', function (error, results, fields) {
  if (error) {
    throw error
  }
  // Do something ...
})

```

通过 promisify 实用程序，我们已经消除了回调，现在我们可以使用`async/await`，如下所示：

```js
let result = null
try {
  result = await pool.query('SELECT ...')
} catch (error) {
  // Handle errors ...
}
```

+   `pool.query`是这三个函数的快捷方式，`pool.getConnection`、`connection.query`和`connection.release`，我们应该一起使用它们在 mysql 模块的连接池中执行 SQL 查询。通过使用`pool.query`，当你完成时，连接会自动释放回连接池。这是`pool.query`函数的基本底层结构：

```js
import mysql from 'mysql'
const pool = mysql.createPool(...)

pool.getConnection(function(error, connection) {
  if (error) { throw error }

  connection.query('SELECT ...', function (error, results,
   fields) {
    connection.release()
    if (error) { throw error }
  })
})
```

+   在这个 mysql 模块中，我们可以使用`mysql.createPool`进行连接池，而不是通过`mysql.createConnection`逐个创建和管理 MySQL 连接，这可能是一个昂贵的操作。连接池是一个可重用的数据库连接缓存，用于减少每次连接到数据库时建立新连接的成本。有关连接池的更多信息，请访问[`github.com/mysqljs/mysqlpooling-connections`](https://github.com/mysqljs/mysql#pooling-connections)。

1.  所以，我们已经将 MySQL 连接抽象成了`/core/`目录中的前述文件。现在我们可以使用它来获取`user`模块中用户列表，如下所示：

```js
// backend/src/modules/public/user/_routes/index.js
import Router from 'koa-router'
import pool from 'core/database/mysql'
const router = new Router()

router.get('/', async (ctx, next) => {
  try {
    var users = await pool.query(
     'SELECT `id`, `name`, `created_on`
      FROM `users`'
    )
  } catch (err) { ... }

  ctx.type = 'json'
  ctx.body = users
})

export default router
```

您可以看到，我们使用了与前一节中所述的相同代码结构，通过 MySQL 连接池将我们的请求发送到 MySQL 服务器。在我们发送的查询中，我们告诉 MySQL 服务器仅为我们从`users`表中返回`id`、`name`和`created_on`字段的结果。

1.  如果您访问`localhost:4000/public/users`上的用户路由，您应该在屏幕上看到以下输出：

```js
{"status":200,"data":[{"id":1,"name":"Alexandre","created_on":"2019-06-16T22:00:00.000Z"}]}
```

现在我们有了用于连接到 MySQL 服务器和数据库的 mysql 模块，以及用于对客户端密码进行哈希和验证的 bcryptjs 模块，因此我们可以重构和改进我们在上一章中粗略创建的登录代码。让我们在下一节中找出如何做。

如果您想了解更多关于 mysql 模块的信息，请访问[`github.com/mysqljs/mysql`](https://github.com/mysqljs/mysql)。

## 在服务器端重构登录代码

我们已经在前几节中收集了所有必要的要素，一旦我们创建了 MySQL 连接池，我们就可以重构和改进我们的登录代码，从第十章 *添加一个 Vuex Store* 和第十一章 *编写路由中间件和服务器中间件*，按照以下步骤进行：

1.  导入所有依赖项，如`koa-router`、`jsonwebtoken`、`bcryptjs`和 MySQL 连接池，用于登录路由如下：

```js
// src/modules/public/login/_routes/local.js
import Router from 'koa-router'
import jwt from 'jsonwebtoken'
import bcrypt from 'bcrypt'
import pool from 'core/database/mysql'
import config from 'config'

const router = new Router()

router.post('/login', async (ctx, next) => {
  let request = ctx.request.body || {}
  //...
})

export default router
```

我们在这里导入了配置文件，用于 API 的配置选项，其中包含了 MySQL 数据库连接详细信息、服务器和静态目录的选项，以及我们稍后需要用于签署令牌的 JWT 的秘密代码。

1.  在登录路由的`post`方法中验证用户输入，以确保它们已定义且不为空：

```js
if (request.username === undefined) {
  ctx.throw(400, 'username param is required.')
}
if (request.password === undefined) {
  ctx.throw(400, 'password param is required.')
}
if (request.username === '') {
  ctx.throw(400, 'username is required.')
}
if (request.password === '') {
  ctx.throw(400, 'password is required.')
}
```

1.  当它们通过验证时，将用户名和密码分配给变量以查询数据库：

```js
let username = request.username
let password = request.password

let users = []
try {
  users = await pool.query('SELECT  FROM users WHERE 
   username = ?', [username])
} catch(err) {
  ctx.throw(400, err.sqlMessage)
}

if (users.length === 0) {
  ctx.throw(404, 'no user found')
}
```

1.  如果从 MySQL 查询中有结果，就使用 bcryptjs 比较存储的密码和用户输入的密码：

```js
let user = users[0]
let match = false

try {
  match = await bcrypt.compare(password, user.password)
} catch(err) {
  ctx.throw(401, err)
}
if (match === false) {
  ctx.throw(401, 'invalid password')
}
```

1.  如果用户通过了所有先前的步骤和验证，就对 JWT 进行签名并将其发送给客户端：

```js
let payload = { name: user.name, email: user.email }
let token = jwt.sign(payload, config.JWT_SECRET, { expiresIn:
  1 * 60 })

ctx.body = {
  user: payload,
  message: 'logged in ok',
  token: token
}
```

1.  使用`npm run dev`运行 API，并在终端上手动使用`curl`测试上一个路由，如下所示：

```js
$ curl -X POST -d "username=demo&password=123123" -H "Content-Type: application/x-www-form-urlencoded" http://localhost:4000/public/login/local
```

如果您成功登录，您应该得到以下结果：

```js
{"status":200,"data":{"user":{"name":"Alexandre","email":"thiamkok.lau@gmail.com"},"message":"logged in ok","token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJuYW1lIjoiQWxleGFuZHJlIiwiZW1haWwiOiJ0aGlhbWtvay5sYXVAZ21haWwuY29tIiwiaWF0IjoxNTgwMDExNzAwLCJleHAiOjE1ODAwMTE3NjB9.Lhd78jokSGALup6DUYAqWAjl7C-8dLhXjEba-KAxy4k"}}
```

当然，每当成功签署时，您将在前面的响应中获得不同的令牌。现在，您已经成功地重构和改进了登录代码。接下来，我们将看一下如何在下一节中验证前面的令牌，该令牌将从客户端以请求头的形式发送回来。所以，请继续阅读！

## 在服务器端验证传入的令牌

我们成功地签署了一个令牌，并在凭据与我们在数据库中存储的内容匹配时将其返回给客户端。但这只是故事的一半。每次客户端使用令牌进行请求时，我们都应该验证这个令牌，以便访问服务器端中间件保护的所有受保护路由。

因此，让我们按照以下步骤创建中间件和受保护的路由：

1.  在`/src/`目录内的`/middlewares/`目录中创建一个中间件文件，并使用以下代码：

```js
// src/middlewares/authenticate.js
import jwt from 'jsonwebtoken'
import config from 'config'

export default async (ctx, next) => {
  if (!ctx.headers.authorization) {
    ctx.throw(401, 'Protected resource, use Authorization header 
    to get access')
  }
  const token = ctx.headers.authorization.split(' ')[1]

  try {
    ctx.state.jwtPayload = jwt.verify(token, config.JWT_SECRET)
  } catch (err) {
    // handle error.
  }
  await next()
}
```

`if`条件`!ctx.headers.authorization`用于确保客户端已在请求头中包含了令牌。由于`authorization`以`Bearer: [token]`的格式带有值，其中有一个单个空格，我们通过该空格拆分值，并仅在`try`和`catch`块中获取`[token]`进行验证。如果令牌有效，则我们允许请求通过到下一个路由，使用`await next()`。

1.  导入并注入此中间件到我们想要用 JWT 保护的路由组中：

```js
// src/routes-private.js
import Router from 'koa-router'
import home from './modules/private/home'
import authenticate from './middlewares/authenticate'

const router = new Router({ prefix: '/private' })
const modules = [home]

for (var module of modules) {
  router.use(authenticate, module.routes(), module.allowedMethods())
}
```

在这个 API 中，我们希望保护所有属于`/private`路由的路由。因此，我们将在这个文件中导入我们想要保护的任何路由，例如前面的`/home`路由。因此，当您使用`/private/home`请求此路由时，您必须在请求头中包含令牌以访问此路由。

就是这样。你已经成功地在服务器端创建并验证了 JWT。接下来，我们应该看一下如何在下一节中使用 Nuxt 在客户端完成 JWT 认证。让我们开始吧！

# 创建前端身份验证

你会发现这一部分很容易和熟悉，因为在前两章中你已经用虚拟后端身份验证构建了一些认证 Nuxt 应用。本章的不同之处在于我们正在制作跨域应用，而不是像前两章那样的单域应用。你可以在`/chapter-10/nuxt-universal/nuxtServerInit/`和`/chapter-11/nuxt-universal/route-middleware/per-route/`中重新访问这些单域 Nuxt 应用。

此外，我们将再次使用我们在第六章中已经介绍过的 Nuxt 模块：`@nuxtjs/axios`和`@nuxtjs/proxy`。你可以在`/chapter-6/nuxt-universal/module-snippets/top-level/`中查看采用这两个模块的 Nuxt 应用。但现在，让我们安装并配置它们用于这个 Nuxt 应用，我们将在接下来的步骤中重构它，以创建客户端身份验证：

1.  通过 npm 安装`@nuxtjs/axios`和`@nuxtjs/proxy`：

```js
$ npm i @nuxtjs/axios
$ npm i @nuxtjs/proxy
```

1.  在 Nuxt 配置文件中配置这两个模块如下：

```js
// nuxt.config.js
module.exports = {
  modules: [
    '@nuxtjs/axios',
  ],

  axios: {
    proxy: true
  },

  proxy: {
    '/api/': { target: 'http://localhost:4000/', pathRewrite:
     {'^/api/': ''} },
  }
}
```

由于我们知道我们在之前章节中创建的远程 API 服务器运行在`localhost:4000`，在这个配置中，我们将这个 API 地址分配给`proxy`选项中的`/api/`键。

1.  移除我们之前用来导入 axios Node.js 模块的任何`import`语句；例如，在安全页面上：

```js
// pages/secured.vue
import axios from '~/plugins/axios'
```

这是因为我们现在使用`@nuxtjs/axios`（Nuxt Axios 模块）了，我们将不再需要直接在我们的代码中导入原始的 axios Node.js 模块。

1.  通过使用`$axios`调用 Nuxt Axios 模块，并替换我们之前在我们的代码中用于 HTTP 请求的原始 axios Node.js 模块中的`axios`；例如，在安全页面上：

```js
// pages/secured.vue
async asyncData ({ $axios, redirect }) {
  const { data } = await $axios.$get('/api/private')
}
```

Nuxt Axios 模块通过*Nuxt 配置文件*中的步骤 2 加载到我们的 Nuxt 应用中，所以我们可以通过 Nuxt 上下文或`this`来使用`$axios`访问它。

我们还应该使用这两个 Nuxt 模块`@nuxtjs/axios`和`@nuxtjs/proxy`以及 cookies、Node.js 模块（客户端和服务器端）来重构这个应用中存储和中间件的其余代码。所以让我们在以下部分开始吧。

## 在（Nuxt）客户端使用 cookies

在这个应用中，我们不再使用会话来“记住”认证数据。相反，我们将使用`js-cookie` Node.js 模块来创建 cookies 来存储来自远程服务器的数据。

使用这个 Node.js 模块非常容易创建一个在整个站点上都存在的 cookie；例如：

1.  使用以下格式设置 cookie：

```js
Cookies.set(<name>, <value>)
```

以下是如果你想创建一个 30 天后过期的 cookie 的代码：

```js
Cookies.set(<name>, <value>, { expires: 30 })
```

1.  使用以下格式读取 cookie：

```js
Cookies.get(<name>)
```

使用这个 Node.js 模块是多么容易 - 你只需要使用`set`和`get`方法在客户端设置和检索你的 cookies。所以，让我们按照以下步骤重构我们存储中的代码：

1.  只有在 Nuxt 应用程序在客户端处理时，才使用`if`三元条件来导入 js-cookie Node.js 模块：

```js
// store/actions.js
const cookies = process.client ? require('js-cookie') : undefined
```

1.  使用 js-cookie 的`set`函数将服务器端的数据存储为`auth`，在`login`操作中如下所示：

```js
// store/actions.js
export default {
  async login(context, { username, password }) {
    const { data } = await 
     this.$axios.$post('/api/public/login/local', 
     { username, password })
    cookies.set('auth', data)
    context.commit('setAuth', data)
  }
}
```

1.  使用 js-cookie 的`remove`函数在`logout`操作中删除`auth` cookie，如下所示：

```js
// store/actions.js
export default {
  logout({ commit }) {
    cookies.remove('auth')
    commit('setAuth', null)
  }
}
```

这很简单，不是吗？但是，你可能会问：我们用这个`auth` cookie 做什么，以及如何使用？让我们在下一节中了解如何在 Nuxt 服务器端使用 cookie。

有关 Node.js 模块的更多信息和代码示例，请访问[`github.com/js-cookie/js-cookie`](https://github.com/js-cookie/js-cookie)。

## 在（Nuxt）服务器端使用 cookie

由于我们使用 JWT 进行身份验证的数据已经被`js-cookie`以`auth`的形式哈希并存储在 cookie 中，因此我们需要在需要时读取和解析此 cookie。这就是 Node.js 模块`cookie`的用武之地。同样，我们在过去的章节中使用了这个 Node.js 模块，但我们还没有讨论过它。

cookie Node.js 模块是用于 HTTP 服务器的 HTTP cookie 解析器和序列化程序。它用于在服务器端解析 cookie 标头。让我们看看如何在以下步骤中在`auth` cookie 上使用它：

1.  只有在 Nuxt 应用程序在服务器端处理时，才使用`if`三元条件来导入 cookie Node.js 模块：

```js
// store/index.js
const cookie = process.server ? require('cookie') : undefined
```

1.  使用 cookie Node.js 模块的`parse`函数来解析`nuxtServerInit`操作中 HTTP 请求头中的`auth` cookie，如下所示：

```js
// store/index.js
export const actions = {
  nuxtServerInit({ commit }, { req }) {
    if (req.headers.cookie && req.headers.cookie.indexOf('auth') >
      -1) {
      let auth = cookie.parse(req.headers.cookie)['auth']
      commit('setAuth', JSON.parse(auth))
    }
  }
}
```

1.  通过`$axios`使用 Nuxt Axios 模块的`setHeader`函数将令牌（JWT）包含在远程服务器上的令牌中间件的 HTTP 标头中，以访问私有 API 路由，如下所示：

```js
// middleware/token.js
export default async ({ store, error, $axios }) => {
  if (!store.state.auth.token) {
    // handle error
  }
  $axios.setHeader('Authorization', Bearer: ${store.state.auth.token})
}
```

1.  使用`npm run dev`运行 Nuxt 应用程序。您应该在`localhost:3000`上的浏览器中运行该应用程序。您可以使用登录页面上的凭据登录，然后访问受 JWT 保护的受限安全页面。

干得好！您已经完成了基于令牌的本地身份验证。您已经重构了存储和中间件中的代码，使得`js-cookie`和`cookie` Node.js 模块可以在 Nuxt 应用程序的前端身份验证中完美地在客户端和服务器端协同工作并相互补充。此外，您已成功将 Nuxt 应用程序与跨域方法解耦 API。

正如您所看到的，使用`js-cookie`和`cookie` Node.js 模块进行前端身份验证非常简单且非常好。但是也可以通过 Google OAuth 实现，我们将在下一节中进行讨论。将 Google OAuth 添加到前端身份验证可以为用户提供额外的登录选项。所以，让我们开始吧。

您可以在我们的 GitHub 存储库的`/chapter-12/nuxt-universal/cross-domain/jwt/axios-module/frontend/`中找到此 Nuxt 应用程序的源代码。

有关`cookie` Node.js 模块的更多信息和代码示例，请访问[`github.com/jshttp/cookie`](https://github.com/jshttp/cookie)。

有关助手的更多信息，例如 Nuxt Axios 模块中的`setHeader`助手，请访问[`axios.nuxtjs.org/helpers`](https://axios.nuxtjs.org/helpers)。

# 使用 Google OAuth 登录

OAuth 是一种开放的委托授权协议，允许网站或应用程序之间进行访问，而不会将用户密码暴露给已被授予访问权限的各方。它是许多公司和网站用来识别用户的常见访问委托。让我们让我们的用户使用 Google OAuth 登录我们的应用程序。此选项需要来自 Google 开发者控制台的客户端 ID 和客户端密钥。可以通过以下步骤获得它们：

1.  在[`console.developers.google.com/`](https://console.developers.google.com/)的谷歌开发者控制台中创建一个新项目。

1.  在 OAuth 同意屏幕选项卡上选择 External。

1.  在凭据选项卡上的“创建凭据”下拉选项中选择 OAuth 客户端 ID，然后选择 Web 应用程序作为应用程序类型。

1.  在“名称”字段中提供您的 OAuth 客户端 ID 的名称，在“授权重定向 URI”字段中提供重定向 URI，以便谷歌在用户在谷歌同意页面上进行身份验证后重定向用户。

1.  在库选项卡中启用 Google People API，该 API 提供对 API 库中有关配置文件和联系人的信息的访问权限。

一旦您设置了开发者帐户并按照上述步骤创建了**客户端 ID**和**客户端密钥**，您就可以准备在下一节中将 Google OAuth 添加到后端身份验证中。让我们开始吧。

## 将 Google OAuth 添加到后端身份验证

为了让某人登录谷歌，我们需要将他们发送到谷歌登录页面。从那里，他们将登录他们的账户，并将被重定向到我们的应用程序，并携带他们的谷歌登录详细信息，我们将提取谷歌代码并将其发送回谷歌以获取我们可以在应用程序中使用的用户数据。这个过程需要`googleapis` Node.js 模块，这是一个用于使用谷歌 API 的客户端库。

让我们按照以下步骤在我们的代码中安装并采用它：

1.  通过 npm 安装`googleapis` Node.js 模块：

```js
$ npm i googleapis
```

1.  创建一个文件，包含你的凭证，这样谷歌就知道是谁在发出请求。

```js
// backend/src/config/google.js
export default {
  clientId: '<client ID>',
  clientSecret: '<client secret>',
  redirect: 'http://localhost:3000/login'
}
```

请注意，您必须用从谷歌开发者控制台获得的 ID 和密钥替换上述的`<client ID>`和`<client secret>`值。另外，请注意`redirect`选项中的 URL 必须与您的谷歌应用 API 设置中的授权重定向 URI 中的重定向 URI 匹配。

1.  使用 Google OAuth 生成 Google 身份验证 URL，将用户发送到谷歌同意页面，以获取用户检索访问令牌的权限，如下所示：

```js
// backend/src/modules/public/login/_routes/google/url.js
import Router from 'koa-router'
import { google } from 'googleapis'
import googleConfig from 'config/google'

const router = new Router()

router.get('/google/url', async (ctx, next) => {

  const oauth = new google.auth.OAuth2(
    googleConfig.clientId,
    googleConfig.clientSecret,
    googleConfig.redirect
  )

  const scopes = [
    'https://www.googleapis.com/auth/userinfo.email',
    'https://www.googleapis.com/auth/userinfo.profile',
  ]

  const url = oauth.generateAuthUrl({
    access_type: 'offline',
    prompt: 'consent',
    scope: scopes
  })

  ctx.body = url
})
```

当用户登录并生成 URL 时，范围决定了我们在用户登录时需要什么信息和权限。在我们的情况下，我们希望获得检索用户电子邮件和个人资料信息的权限：`userinfo.email`和`userinfo.profile`。用户在谷歌同意页面上进行了身份验证后，谷歌将用户重定向回我们的应用程序，并携带了一堆经过身份验证的数据和用于访问用户数据的授权代码。

1.  从谷歌在上一步返回的 URL 中附加的经过身份验证的数据中提取`code`参数中的值。我们将在下一节中回到 Node.js 模块，它可以帮助我们从 URL 查询中提取`code`参数。现在，让我们假设我们已经提取了`code`值，并将其发送到服务器端，以请求使用 Google OAuth2 实例的令牌，如下所示：

```js
// backend/src/modules/public/login/_routes/google/me.js
import Router from 'koa-router'
import { google } from 'googleapis'
import jwt from 'jsonwebtoken'
import pool from 'core/database/mysql'
import config from 'config'
import googleConfig from 'config/google'

const router = new Router()

router.get('/google/me', async (ctx, next) => {

  // Get the code from url query.
  const code = ctx.query.code

  // Create a new google oauth2 client instance.
  const oauth2 = new google.auth.OAuth2(
    googleConfig.clientId,
    googleConfig.clientSecret,
    googleConfig.redirect
  )
  //...
})
```

1.  使用我们刚刚提取的代码从谷歌获取令牌，并将它们传递给 Google People，`google.people`，使用`get`方法获取用户数据，并指定在`personFields`查询参数中需要返回的与人相关的字段。

```js
// backend/src/modules/public/login/_routes/google/me.js
...
const {tokens} = await oauth2.getToken(code)
oauth.setCredentials(tokens)

const people = google.people({
  version: 'v1',
  auth: oauth2,
})

const me = await people.people.get({
  resourceName: 'people/me',
  personFields: 'names,emailAddresses'
})
```

您可以看到我们在前面的代码中只想要与 Google 中的人相关的两个字段，即`names`和`emailAddresses`。您可以在[`developers.google.com/people/api/rest/v1/people/get`](https://developers.google.com/people/api/rest/v1/people/get)上找到您想要从 Google 获取的与人相关的其他字段。如果访问成功，我们应该从 Google 以 JSON 格式获取用户数据，然后我们可以从该数据中提取电子邮件，以确保它将在下一步中与我们数据库中的用户匹配。

1.  仅从 Google 人员数据中检索第一个电子邮件，并查询我们的数据库，以查看是否已经有任何使用该电子邮件的用户：

```js
// backend/src/modules/public/login/_routes/google/me.js
...
let email = me.data.emailAddresses[0].value
let users = []

try {
  users = await pool.query('SELECT  FROM users WHERE email = ?',
   [email])
} catch(err) {
  ctx.throw(400, err.sqlMessage)
}
```

1.  如果没有该电子邮件的用户，请向客户端发送来自 Google 的用户数据的`'signup required'`消息，并要求用户在我们的应用程序中注册帐户：

```js
// backend/src/modules/public/login/_routes/google/me.js
...
if (users.length === 0) {
  ctx.body = {
    user: me.data,
    message: 'signup required'
  }
  return
}
let user = users[0]
```

1.  如果匹配，则使用有效载荷和 JWT 密钥签署 JWT，然后将令牌（JWT）发送到客户端：

```js
// backend/src/modules/public/login/_routes/google/me.js
...
let payload = { name: user.name, email: user.email }
let token = jwt.sign(payload, config.JWT_SECRET, { expiresIn: 1 * 60 })

ctx.body = {
  user: payload,
  message: 'logged in ok',
  token: token
}
```

就是这样。在前面的几个步骤中，您已经成功在服务器端添加了 Google OAuth。接下来，我们应该看看如何在下一节中使用 Nuxt 完成 Google OAuth 的客户端身份验证。让我们开始吧。

有关 googleapis Node.js 模块的更多信息，请访问[`github.com/googleapis/google-api-nodejs-client`](https://github.com/googleapis/google-api-nodejs-client)。

## 为 Google OAuth 创建前端身份验证

当 Google 将用户重定向回我们的应用程序时，我们将在重定向 URL 上获得大量数据，例如：

```js
http://localhost:3000/login?code=4%2F1QGpS37E21TcgQhhIvJZlK1cG4M1jpPJ0I_XPQgrFjvKUFUJQ3aYuO1zYsqPmKgNb4Wfd8ito88yDjUTD6CKD3E&scope=email%20profile%20https%3A%2F%2Fwww.googleapis.com%2Fauth%2Fuserinfo.email%20https%3A%2F%2Fwww.googleapis.com%2Fauth%2Fuserinfo.profile%20openid&authuser=1&prompt=consent
```

当您第一次看到它时，它很难阅读和解密，但它只是一个带有参数附加到我们重定向 URL 的查询字符串：

```js
<redirect URL>?
code=4/1QFvWYDSrW...
&scope=email profile...
&authuser=1
&prompt=consent
```

我们可以使用 Node.js 模块`query-string`来解析 URL 中的查询字符串，例如：

```js
const queryString = require('query-string')
const parsed = queryString.parse(location.search)
console.log(parsed)
```

然后您将在浏览器控制台中获得以下 JavaScript 对象：

```js
{authuser: "1", code: "4/1QFvWYDSrWLklhIgRfVR0LJy6Pk0gn5TkjTKWKlRr9pdZveGAHV_pMrxBhicy7Zd6d9nfz0IQrcLl-VGS-Gu9Xk", prompt: "consent", scope: "email profile https://www.googleapis.com/auth/user…//www.googleapis.com/auth/userinfo.profile openid"}
```

在前面的重定向 URL 中，`code`参数是我们最感兴趣的，因为我们需要将其发送到服务器端，以便通过 googleapis Node.js 模块获取 Google 用户数据，正如您在上一节中学到的。因此，让我们安装`query-string`并在接下来的步骤中在我们的 Nuxt 应用程序中创建前端身份验证：

1.  通过 npm 安装`query-string` Node.js 模块：

```js
$ npm i query-string
```

1.  在登录页面上创建一个按钮，并绑定一个名为`loginWithGoogle`的方法，以调度存储中的`getGoogleUrl`方法，如下所示：

```js
// frontend/pages/login.vue
<button v-on:click="loginWithGoogle">Google Login</button>

export default {
  methods: {
    async loginWithGoogle() {
      try {
        await this.$store.dispatch('getGoogleUrl')
      } catch (error) {
        let errorData = error.response.data
        this.formError = errorData.message
      }
    }
  }
}
```

1.  在 API 中调用`/api/public/login/google/url`路由，在`getGoogleUrl`方法中如下所示：

```js
// frontend/store/actions.js
export default {
  async getGoogleUrl(context) {
    const { data } = await this.$axios.$get('/api/public/login/
     google/url')
    window.location.replace(data)
  }
}
```

`/api/public/login/google/url`路由将返回一个 Google URL，然后我们可以使用它将用户重定向到 Google 登录页面。从那里，用户将决定要登录到哪个 Google 帐户（如果有多个）。

1.  从返回的 URL 中提取查询部分，并在 Google 将用户重定向回登录页面时将其发送到 store 中的`loginWithGoogle`方法中，如下所示：

```js
// frontend/pages/login.vue
export default {
  async mounted () {
    let query = window.location.search

    if (query) {
      try {
        await this.$store.dispatch('loginWithGoogle', query)
      } catch (error) {
        // handle error
      }
    }
  }
}
```

1.  使用`query-string`从前面的查询部分中提取`code`参数的代码，并使用`$axios`将其发送到我们的 API`/api/public/login/google/me`，如下所示：

```js
// frontend/store/actions.js
import queryString from 'query-string'

export default {
  async loginWithGoogle (context, query) {
    const parsed = queryString.parse(query)
    const { data } = await this.$axios.$get('/api/public/login/
     google/me', {
      params: {
        code: parsed.code
      }
    })

    if (data.message === 'signup required') {
      localStorage.setItem('user', JSON.stringify(data.user))
      this.$router.push({ name: 'signup'})
    } else {
      cookies.set('auth', data)
      context.commit('setAuth', data)
    }
  }
}
```

当我们从服务器收到`'signup required'`消息时，我们将用户重定向到注册页面。但是，如果我们收到带有 JWT 的消息，那么我们可以将 cookie 和经过身份验证的数据设置到 store 状态中。我们将留下注册页面让您自己想象和努力，因为这是一个用于收集用户数据以存储在数据库中的表单。

1.  最后，使用`npm run dev`运行 Nuxt 应用程序。您应该可以在`localhost:3000`上在浏览器中运行该应用程序。您可以使用 Google 登录，然后访问受 JWT 保护的受限页面，就像本地认证一样。

所以，这就是你使用 Google OAuth API 登录用户的基本步骤。这一点并不难，是吗？我们还可以使用 Nuxt Auth 模块来实现几乎与我们在这里完成的相同的功能。使用此模块，您可以使用 Auth0、Facebook、GitHub、Laravel Passport 和 Google 登录用户。如果您正在寻找 Nuxt 的快速、简单和零样板认证支持，这可能是您项目的一个不错的选择。有关此 Nuxt 模块的更多信息，请访问[`auth.nuxtjs.org/`](https://auth.nuxtjs.org/)。现在让我们在下一节总结一下您在本章中学到的内容。

您可以在我们的 GitHub 存储库中的`/chapter-12/nuxt-universal/cross-domain/jwt/axios-module/`中找到前面使用 Google OAuth 的登录选项。

有关`query-string` Node.js 模块的使用信息，请访问[`www.npmjs.com/package/query-string`](https://www.npmjs.com/package/query-string)。

# 摘要

干得好！您已经走了这么远。毕竟，在网页身份验证上工作并不难。在本章中，您已经了解了基于会话的身份验证和基于令牌的身份验证，特别是关于 JSON Web Token（JWT）。您现在应该知道它们之间的区别以及 JWT 的组成部分，以及如何使用`jsonwebtoken` Node.js 模块生成 JWT。我们还介绍了 MySQL Node.js 模块，并将其用作我们身份验证系统的一部分。您还集成了 Google OAuth 以便用户登录，然后使用 Nuxt 创建了前端身份验证。

在下一章中，您将学习如何在您的 Nuxt 应用程序中编写端到端测试。您将了解可以安装和使用的用于编写端到端测试的工具，特别是 AVA 和 Nightwatch。除此之外，您还将学习如何使用一个 Node.js 模块，即`jsdom`，使您的端到端测试在服务器端成为可能。这是因为 Nuxt 在技术上是一种服务器端技术，并在服务器端呈现我们的 HTML 页面，但在服务器端没有 DOM，因此我们可以利用`jsdom`来实现。但请放心，我们将引导您完成设置所有这些工具并编写您的测试的步骤。所以，请继续关注！
