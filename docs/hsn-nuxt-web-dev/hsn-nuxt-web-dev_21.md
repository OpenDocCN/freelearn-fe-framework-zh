使用 Nuxt 创建 SPA

在之前的章节中，我们创建了各种 Nuxt 应用程序，以`universal`模式。这些是通用服务器端渲染（SSR）应用程序。这意味着它们是在服务器端和客户端上运行的应用程序。Nuxt 为我们提供了另一种开发**单页面应用程序**（**SPA**）的选项，就像我们可以使用 Vue 和其他 SPA 框架（如 Angular 和 React）一样。在本章中，我们将指导您如何在 Nuxt 中开发、构建和部署 SPA，并了解它与现有的传统 SPA 有何不同。

在这一章中，我们将涵盖以下主题：

+   理解经典 SPA 和 Nuxt SPA

+   安装 Nuxt SPA

+   开发 Nuxt SPA

+   部署 Nuxt SPA

让我们开始吧！

# 第十五章：理解经典 SPA 和 Nuxt SPA

SPA，也称为经典 SPA，是一种应用程序，它在浏览器上加载一次，不需要我们在应用程序的整个生命周期内重新加载和重新渲染页面。这与多页面应用程序（MPA）不同，在多页面应用程序中，每次更改和与服务器的每次数据交换都需要我们重新从服务器到浏览器重新渲染整个页面。

在经典/传统的 SPA 中，提供给客户端的 HTML 相对为空。一旦到达客户端，JavaScript 将动态渲染 HTML 和内容。React、Angular 和 Vue 是创建经典 SPA 的流行选择。然而，不要与 spa 模式的 Nuxt 应用程序混淆（让我们称之为**Nuxt SPA**），尽管 Nuxt 为您提供了使用一行配置开发“SPA”的选项，如下所示：

```js
// nuxt.config.js
export default {
  mode: 'spa'
}
```

Nuxt 的 SPA 模式简单地意味着您失去了 Nuxt 和 Node.js 的服务器端特性，就像我们在第十四章中学到的，在将通用 SSR Nuxt 应用程序转换为静态生成（预渲染）Nuxt 应用程序时，使用了代码检查器、格式化程序和部署命令。对于 spa 模式的 Nuxt 应用程序也是一样-当您使用上述配置时，您的 spa 模式 Nuxt 应用程序将成为纯粹的**客户端应用程序**。

但是，spa 模式 Nuxt 应用程序与您从 Vue CLI、React 或 Angular 创建的经典 SPA 有很大不同。这是因为构建应用程序后，您（经典）SPA 的页面和路由将在运行时由 JavaScript 动态呈现。另一方面，spa 模式 Nuxt 应用程序中的页面将在构建时进行预渲染，并且每个页面中的 HTML 与经典 SPA 一样“空”。这就是事情开始变得混乱的地方。让我们看看以下示例。假设您的 Vue 应用程序中有以下页面和路由：

```js
src
├── favicon.ico
├── index.html
├── components
│ ├── about.vue
│ ├── secured.vue
│ └── ...
└── routes
  ├── about.js
  ├── secured.js
  └── ...
```

您的应用程序将构建到以下分发中：

```js
dist
├── favicon.ico
├── index.html
├── css
│ └── ...
└── js
  └── ...
```

在这里，您可以看到只有`index.html`、`/css/`和`/js/`文件夹构建到`/dust/`文件夹中。这意味着您的应用程序的页面和路由将在运行时由 JavaScript 动态呈现。然而，假设您的 spa 模式 Nuxt 应用程序中有以下页面：

```js
pages
├── about.vue
├── secured.vue
├── ...
└── users
  ├── about.js
  ├── index.vu
  └── _id.vue
```

您的应用程序将构建到以下分发中：

```js
dist
├── index.html
├── favicon.ico
├── about
│ └── index.html
├── secured
│ └── index.html
├── users
│ └── index.html
└── ...
```

正如您所看到的，您应用程序的每个页面和路由都是使用`index.html`文件构建并放置在`/dust/`文件夹中 - 就像您为通用 SSR Nuxt 应用程序生成的静态站点一样。因此，在这里，我们可以说您将构建和部署的 spa 模式 Nuxt 应用程序是一个“静态”SPA，而不是经典的“动态”SPA。当然，您仍然可以使用以下部署命令将您的 spa 模式 Nuxt 应用程序部署为通用 SSR Nuxt 应用程序。这将使其在运行时变得“动态”：

```js
$ npm run build
$ npm run start
```

但是在 Node.js 主机上部署 Nuxt SPA 应用可能会过度，因为您选择 spa 模式 Nuxt 应用程序并且不想为您的 SPA 使用 Node.js 主机，必须有一些充分的理由。因此，将 Nuxt SPA**预渲染**为静态生成的应用程序（让我们称之为**静态生成的 Nuxt SPA**）可能更合理。您可以像通用 SSR Nuxt 应用程序一样轻松地使用`nuxt export`命令预渲染您的 Nuxt SPA。

这就是本章的全部内容：在 spa 模式下开发 Nuxt 应用程序，并在部署到静态托管服务器（如 GitHub Pages）之前生成所需的静态 HTML 文件。因此，让我们开始安装和设置环境。

# 安装 Nuxt SPA

安装 Nuxt SPA 与使用`create-nuxt-app`脚手架工具安装 Nuxt 通用 SSR 相同。让我们开始吧：

1.  通过终端使用 Nuxt 脚手架工具安装 Nuxt 项目：

```js
$ npx create-nuxt-app <project-name>
```

1.  回答出现的问题，并在要求**渲染模式**时选择**单页面应用**选项：

```js
? Project name
? Project description
//...
? Rendering mode:  
  Universal (SSR / SSG)  
> Single Page App 
```

安装完成后，如果您检查项目根目录中的 Nuxt 配置文件，您应该会看到在安装过程中已经为您配置了`mode`选项为 SPA：

```js
// nuxt.config.js
export default {
  mode: 'spa'
}
```

1.  在您的终端中启动 Nuxt 开发模式：

```js
$ npm run dev
```

您应该看到您的终端上**只有**客户端端的代码被编译：

```js
✓ Client
  Compiled successfully in 1.76s
```

您将不再看到在服务器端编译的代码，这是您通常在`universal`模式下看到的 Nuxt 应用程序的代码：

```js
✓ Client
  Compiled successfully in 2.75s

✓ Server
  Compiled successfully in 2.56s
```

如您所见，在 Nuxt 中很容易启动 spa 模式环境。您也可以通过在 Nuxt 配置文件中的`mode`选项中添加`spa`值来**手动**设置 spa 模式。现在，让我们开发一个 Nuxt SPA。

# 开发 Nuxt SPA

在开发 Nuxt SPA 时需要牢记的一个重要事项是，给予`asyncData`和`fetch`方法的 Nuxt 上下文将失去它们的`req`和`res`对象，因为这些对象是 Node.js HTTP 对象。在本节中，我们将创建一个简单的用户登录身份验证，您应该已经熟悉。但是，这一次，我们将在 Nuxt SPA 中进行。我们还将创建一个用于使用动态路由列出用户的页面，就像我们在第四章中学到的那样，*添加视图、路由和过渡*。让我们开始吧：

1.  准备以下`.vue`文件，或者只需从上一章中复制，如下所示：

```js
-| pages/
---| index.vue
---| about.vue
---| login.vue
---| secret.vue
---| users/
-----| index.vue
-----| _id.vue
```

1.  准备具有存储状态、突变、动作和处理用户登录身份验证的索引文件的 Vuex 存储，如下所示：

```js
-| store/
---| index.js
---| state.js
---| mutations.js
---| actions.js
```

在前一章中提到，当我们**静态**生成 Nuxt 通用 SSR 应用程序时，存储中的`nuxtServerInit`动作将会丢失，因此在 Nuxt SPA 中也是一样的-我们在客户端不会有这个服务器动作。因此，我们需要一个客户端`nuxtServerInit`动作来模拟服务器端的`nuxtServerInit`动作。我们接下来将学习如何做到这一点。

## 创建客户端 nuxtServerInit 动作

这些文件中的方法和属性与我们在过去的练习中所拥有的方法和属性相同，除了`nuxtServerInit`动作：

```js
// store/index.js
const cookie = process.server ? require('cookie') : undefined

export const actions = {
  nuxtServerInit ({ commit }, { req }) {
    if (
      req 
      && req.headers 
      && req.headers.cookie 
      && req.headers.cookie.indexOf('auth') > -1
    ) {
      let auth = cookie.parse(req.headers.cookie)['auth']
      commit('setAuth', JSON.parse(auth))
    }
  }
}
```

在 Nuxt SPA 中，没有涉及服务器，因为`nuxtServerInit`只能由 Nuxt 从服务器端调用。因此，我们需要一个解决方案。我们可以使用 Node.js 的`js-cookie`模块在用户登录时在客户端存储经过身份验证的数据，这使其成为替代服务器端 cookie 的最佳选择。让我们学习如何实现这一点：

1.  通过 npm 安装 Node.js 的`js-cookie`模块：

```js
$ npm i js-cookie
```

1.  在存储操作中创建一个名为`nuxtClientInit`（如果愿意，也可以选择其他名称）的自定义方法，以从 cookie 中检索用户数据。然后，在用户刷新浏览器时将其设置回所需的状态。

```js
// store/index.js
import cookies from 'js-cookie'

export const actions = {
  nuxtClientInit ({ commit }, ctx) {
    let auth = cookies.get('auth')
    if (auth) {
      commit('setAuth', JSON.parse(auth))
    }
  }
}
```

正如您可能记得的那样，在刷新页面时，商店的`nuxtServerInit`动作总是在服务器端调用。`nuxtClientInit`方法也是如此；每次在客户端刷新页面时都应该被调用。然而，它不会被**自动**调用，因此我们可以使用插件在 Vue 根实例初始化之前每次调用它。

1.  在`/plugins/`目录中创建一个名为`nuxt-client-init.js`的插件，该插件将通过存储中的`dispatch`方法调用`nuxtClientInit`方法：

```js
// plugins/nuxt-client-init.js
export default async (ctx) => {
  await ctx.store.dispatch('nuxtClientInit', ctx)
}
```

请记住，在 Vue 根实例初始化之前，我们可以在插件中访问 Nuxt 上下文。存储被添加到 Nuxt 上下文中，因此我们可以访问存储操作，而这里我们感兴趣的是`nuxtClientInit`方法。

1.  现在，将此插件添加到 Nuxt 配置文件中以安装该插件：

```js
// nuxt.config.js
export default {
  plugins: [
    { src: '~/plugins/nuxt-client-init.js', mode: 'client' }
  ]
}
```

现在，每次刷新浏览器时，`nuxtClientInit`方法都将被调用，并且在 Vue 根实例初始化之前，状态将被此方法重新填充。正如您所看到的，当我们失去 Nuxt 作为通用 JavaScript 应用程序的全部功能时，模仿`nuxtClientInit`动作并不是一件简单的事情。但是，如果您必须选择 Nuxt SPA，那么我们刚刚创建的`nuxtClientInit`方法可以解决这个问题。

接下来，我们将使用 Nuxt 插件创建一些自定义的 Axios 实例。这应该是您已经非常熟悉的内容。然而，能够创建自定义的 Axios 实例是有用的，因为当需要时，您总是可以回退到**原始**版本的 Axios，即使我们也有**Nuxt Axios 模块**。所以，让我们继续！

## 使用插件创建多个自定义的 Axios 实例

在这个 spa 模式的练习中，我们将需要两个 Axios 实例来对以下地址进行 API 调用：

+   `localhost:4000`用于用户认证

+   `jsonplaceholder.typicode.com`用于获取用户

我们将使用原始的 Axios ([`github.com/axios/axios`](https://github.com/axios/axios))，因为它给了我们灵活性来创建带有一些自定义配置的多个 Axios 实例。让我们开始吧：

1.  通过 npm 安装原始的`axios`：

```js
$ npm i axios
```

1.  在需要的页面上创建一个`axios`实例：

```js
// pages/users/index.vue
const instance = axios.create({
  baseURL: '<api-address>',
  timeout: <value>,
  headers: { '<x-custom-header>': '<value>' }
})
```

但直接在页面上创建`axios`实例并不理想。理想情况下，我们应该能够提取这个实例并在任何地方重用它。通过 Nuxt 插件，我们可以创建提取的 Axios 实例。我们可以遵循两种方法来创建它们。我们将在下一节中看一下第一种方法。

### 在 Nuxt 配置文件中安装自定义的 Axios 插件

在之前的章节中，你学会了我们可以使用`inject`方法创建一个插件，并通过 Nuxt 的`config`文件安装插件。除了使用`inject`方法，值得知道的是我们也可以**直接**将插件注入到 Nuxt 上下文中。让我们看看如何做到这一点：

1.  在`/plugins/`目录下创建一个`axios-typicode.js`文件，导入原始的`axios`，并创建实例，如下所示：

```js
// plugins/axios-typicode.js
import axios from 'axios'

const instance = axios.create({
  baseURL: 'https://jsonplaceholder.typicode.com'
})

export default (ctx, inject) => {
  ctx.$axiosTypicode = instance
  inject('axiosTypicode', instance)
}
```

如你所见，在创建了`axios`实例之后，我们通过 Nuxt 上下文(`ctx`)注入了插件，使用了`inject`方法，然后导出它。

1.  在 Nuxt 配置文件中安装这个插件：

```js
// nuxt.config.js
export default {
  plugins: [
    { src: '~/plugins/axios-typicode.js', mode: 'client' }
  ]
}
```

你必须将`mode`选项设置为`client`，因为我们**只**需要它在客户端。

1.  你可以在任何你喜欢的地方访问这个插件。在这个例子中，我们想在用户索引页面上使用这个插件来获取用户列表：

```js
// pages/users/index.vue
export default {
  async asyncData({ $axiosTypicode }) {
    let { data } = await $axiosTypicode.get('/users')
    return { users: data }
  }
}
```

在这个插件中，我们将自定义的`axios`实例直接注入到 Nuxt 上下文(`ctx`)中，命名为`$axiosTypicode`，这样我们可以使用 JavaScript 解构赋值语法直接调用它作为`$axiosTypicode`。我们还使用`inject`方法注入了插件，所以我们也可以通过`ctx.app`来调用这个插件，如下所示：

```js
// pages/users/index.vue
export default {
  async asyncData({ app }) {
    let { data } = await app.$axiosTypicode.get('/users')
    return { users: data }
  }
}
```

创建自定义的 Axios 插件并不太难，是吗？如果你通过 Nuxt 配置文件安装插件，这意味着它是一个全局的 JavaScript 函数，你可以从任何地方访问它。但如果你不想将它安装为全局插件，你可以跳过在 Nuxt 配置文件中安装它。这就引出了创建 Nuxt 插件的第二种方法。

### 手动导入自定义的 Axios 插件

创建自定义 Axios 实例的另一种方法根本不涉及 Nuxt 配置。我们可以将自定义实例导出为常规的 JavaScript 函数，然后直接在需要它的页面中导入。让我们看看如何做到这一点：

1.  在`/plugins/`目录中创建一个`axios-api.js`文件，导入原始的`axios`，并创建实例，如下所示：

```js
// plugins/axios-api.js
import axios from 'axios'

export default axios.create({
  baseURL: 'http://localhost:4000',
  withCredentials: true
})
```

如您所见，我们不再使用`inject`方法；相反，我们直接导出实例。

1.  现在，我们可以在需要时手动导入它。在这个例子中，我们需要在`login`动作方法中使用它，如下所示：

```js
// store/actions.js
import axios from '~/plugins/axios-api'

async login({ commit }, { username, password }) {
  const { data } = await axios.post('/public/users/login', { 
   username, password })
  //...
}
```

如您所见，我们必须**手动**导入此插件，因为它没有插入到 Nuxt 生命周期中。

1.  导入它并在`token`中间件中设置`Authorization`头部，如下所示：

```js
// middleware/token.js
import axios from '~/plugins/axios-api'

export default async ({ store, error }) => {
  //...
  axios.defaults.headers.common['Authorization'] = Bearer: 
  ${store.state.auth.token}
}
```

尽管在遵循这种方法时我们必须手动导入插件，但至少我们已经将以下设置提取到了一个可以在需要时重用的插件中：

```js
{
  baseURL: 'http://localhost:4000',
  withCredentials: true
}
```

您可以在本书的 GitHub 存储库的`/chapter-15/frontend/`中找到 Nuxt SPA 的代码以及这两种方法。

一旦您创建、测试和 lint 所有代码和文件，您就可以准备部署 Nuxt SPA 了。所以，让我们开始吧！

# 部署 Nuxt SPA

如果我们有一个 Node.js 运行时服务器，我们可以像部署通用 SSR Nuxt 应用程序一样部署 Nuxt SPA。如果没有，那么我们只能将 SPA 部署为静态站点到静态托管服务器，比如 GitHub Pages。您可以按照以下步骤部署静态生成的 Nuxt SPA：

1.  确保在 Nuxt 配置文件的`mode`选项中将值设置为`spa`：

```js
// nuxt.config.js
export default {
  mode: 'spa'
}
```

1.  确保`package.json`文件中有以下运行脚本：

```js
{
  "scripts": {
    "generate": "nuxt generate"
  }
}
```

1.  运行`npm run generate`，就像您为通用 SSR Nuxt 应用程序一样。您应该在终端中看到以下输出：

```js
ℹ Generating output directory: dist/
ℹ Generating pages 
✓ Generated /about
✓ Generated /login
✓ Generated /secret
✓ Generated /users
✓ Generated /
```

在上述输出中，如果您导航到项目内的`/dist/`文件夹，您将在根目录找到一个`index.html`文件，以及在每个子文件夹中找到带有路由名称的`index.html`文件。但是，您将在生成的动态路由中找不到任何页面，比如`/users/1`。这是因为与通用模式相反，在 spa 模式下不会生成动态路由。

此外，如果您在`/dist/`文件夹中打开`index.html`文件，您会发现所有的`index.html`文件都是完全相同的-只是一些“空”的 HTML 元素，类似于经典的 SPA。此外，每个`index.html`文件都不包含自己的元信息，只包含来自`nuxt.config.js`的公共元信息。这些页面的元信息将在运行时进行填充和更新。由于这个原因，对于“静态”SPA 来说，这可能看起来有些违反直觉和“半成品”。除此之外，没有生成静态有效负载。这意味着，如果您在浏览器中导航到`localhost:3000/users`，您会注意到该页面仍然从[`jsonplaceholder.typicode.com/users`](https://jsonplaceholder.typicode.com/users)请求其数据，而不是像通用 SSR Nuxt 应用程序那样从有效负载中获取数据。这是因为 Nuxt 在 spa 模式下不生成静态内容，即使您已经在 Nuxt 配置文件中为目标属性设置了`static`。为了解决这些问题，我们可以从通用模式生成我们需要的静态内容。

1.  在 Nuxt 配置文件中将`mode`选项的`spa`更改为`universal`：

```js
// nuxt.config.js
export default {
  mode: 'universal'
}
```

1.  运行`npm run generate`，这样 Nuxt 将对 API 进行 REST API 调用，以检索用户并将其内容导出到本地静态有效负载。您将看到以下输出：

```js
ℹ Generating output directory: dist/
ℹ Generating pages with full static mode 
✓ Generated /about
✓ Generated /secret
✓ Generated /login
✓ Generated /users
✓ Generated /users/1
✓ Generated /users/2
...
...
✓ Generated /users/10
✓ Generated /
```

请注意，前面的输出中没有生成动态路由。如果您再次导航到`/dist/`文件夹，您会看到`/users/`文件夹现在包含多个文件夹，每个文件夹都有自己的用户 ID。每个文件夹都包含一个包含该特定用户内容的`index.html`文件。现在，每个`index.html`文件都包含自己的独立元信息和在`/dist/_nuxt/static/`中生成的有效负载。

1.  在 Nuxt 配置文件中将`mode`选项的`universal`改回`spa`：

```js
// nuxt.config.js
export default {
  mode: 'spa'
}
```

1.  现在，在终端上运行`npm run build`。您应该会看到以下输出：

```js
Hash: c36ee9714ee9427ac1ff 
Version: webpack 4.43.0 
Time: 5540ms 
Built at: 11/07/2020 07:58:09 
                         Asset       Size  Chunks                         Chunk Names 
../server/client.manifest.json   9.31 KiB          [emitted]               
                      LICENSES  617 bytes          [emitted]               
                app.922dbd1.js     57 KiB       0  [emitted] 
                [immutable] app 
        commons/app.7236c86.js    182 KiB       1  [emitted] 
        [immutable] commons/app 
        pages/about.75fcd06.js  667 bytes       2  [emitted] 
        [immutable] pages/about 
        pages/index.76b5c20.js  784 bytes       3  [emitted] 
        [immutable] pages/index 
        pages/login.09e509e.js   3.14 KiB       4  [emitted]
        [immutable] pages/login 
      pages/secured.f086299.js   1.36 KiB       5  [emitted] 
       [immutable] pages/secured 
    pages/users/_id.e1c568c.js   1.69 KiB       6  [emitted] 
      [immutable] pages/users/_id 
  pages/users/index.b3e7aa8.js    1.5 KiB       7  [emitted]
    [immutable] pages/users/index 
            runtime.266b4bf.js   2.47 KiB       8  [emitted] 
            [immutable] runtime 
+ 1 hidden asset 
Entrypoint app = runtime.266b4bf.js commons/app.7236c86.js app.922dbd1.js 
ℹ Ready to run nuxt generate 
```

1.  忽略“准备运行 nuxt generate”消息。相反，首先使用终端上的`nuxt start`命令从`/dist/`目录中测试您的生产静态 SPA：

```js
$ npm run start
```

您应该会得到以下输出：

```js
Nuxt.js @ v2.14.0

> Environment: production
> Rendering: client-side
> Target: static
Listening: http://localhost:3000/

ℹ Serving static application from dist/ 
```

现在，诸如`localhost:3000/users`之类的路由将不再从[`jsonplaceholder.typicode.com`](https://jsonplaceholder.typicode.com)请求其数据。相反，它们将从`/dist/`文件夹中的有效负载中获取数据，该文件夹位于`/static/`文件夹内。

1.  最后，只需将`/dist/`目录部署到您的静态托管服务器。

如果您正在寻找免费的静态托管服务器，请考虑使用 GitHub Pages。使用此功能，您可以为您的站点获得以下格式的域名：

```js
<username>.github.io/<app-name>
```

GitHub 还允许您使用自定义域名而不是使用他们的域名来提供站点。有关更多信息，请参阅 GitHub 帮助网站的指南：[`help.github.com/en/github/working-with-github-pages/configuring-a-custom-domain-for-your-github-pages-site`](https://help.github.com/en/github/working-with-github-pages/configuring-a-custom-domain-for-your-github-pages-site)。但是，在本书中，我们将向您展示如何在 GitHub 的`github.io`域名上提供站点。我们将在下一节中学习如何做到这一点。

您可以在本书的 GitHub 存储库中的`/chapter-15/frontend/`中找到此部分的代码。

## 部署到 GitHub Pages

GitHub Pages 是 GitHub 提供的静态站点托管服务，用于托管和发布 GitHub 存储库中的静态文件（仅限 HTML、CSS 和 JavaScript）。只要您在 GitHub 上拥有用户帐户并为您的站点创建了 GitHub 存储库，就可以在 GitHub Pages 上托管您的静态站点。

请访问[`guides.github.com/features/pages/`](https://guides.github.com/features/pages/)，了解如何开始使用 GitHub Pages。

您只需要转到 GitHub 存储库的**设置**部分，然后向下滚动到**GitHub Pages**部分。然后，您需要单击**选择主题**按钮，以开始创建静态站点的过程。

将 Nuxt SPA 的静态版本部署到 GitHub Pages 非常简单-您只需要对 Nuxt 配置文件进行一些微小的配置更改，然后使用`git push`命令将其上传到 GitHub 存储库。当您创建 GitHub 存储库并创建 GitHub Pages 时，默认情况下，静态页面的 URL 将以以下格式提供：

```js
<username>.github.io/<repository-name>
```

因此，您需要将此`<repository-name>`添加到 Nuxt 配置文件中`router`基本选项，如下所示：

```js
export default {
  router: {
    base: '/<repository-name>/'
  }
}
```

但是更改基本名称将干扰 Nuxt 应用程序的开发时的`localhost:3000`。让我们学习如何解决这个问题：

1.  在 Nuxt 配置文件中为开发和生产 GitHub Pages 创建一个`if`条件，如下所示：

```js
// nuxt.config.js
const routerBase = process.env.DEPLOY_ENV === 'GH_PAGES' ? {
  router: {
    base: '/<repository-name>/'
  }
} : {}
```

如果在进程环境中`DEPLOY_ENV`选项具有`GH_PAGES`，则此条件只是将`/<repository-name>/`添加到`router`选项的`base`键。

1.  使用`spread`操作符在配置文件中的 Nuxt 配置中添加`routerBase`常量：

```js
// nuxt.config.js
export default {
  ...routerBase
}
```

1.  在`package.json`文件中设置`DEPLOY_ENV='GH_PAGES'`脚本：

```js
// package.json
"scripts": {
  "build:gh-pages": "DEPLOY_ENV=GH_PAGES nuxt build",   
  "generate:gh-pages": "DEPLOY_ENV=GH_PAGES nuxt generate"
}
```

使用这两个 npm 脚本中的一个，`/<repository-name>/`的值不会被注入到你的 Nuxt 配置中，并且在运行`npm run dev`进行开发时不会干扰开发过程。

1.  在 Nuxt 配置文件中，将`mode`选项更改为`universal`，就像在上一节的*步骤 4*中一样，使用`nuxt generate`命令生成静态负载和页面：

```js
$ npm run generate:gh-pages
```

1.  将 Nuxt 配置文件中的`mode`选项从`universal`改回`spa`，就像在上一节的*步骤 6*中一样，使用`nuxt build`命令构建 SPA：

```js
$ npm run build:gh-pages
```

1.  通过你的 GitHub 仓库将 Nuxt 生成的`/dist/`文件夹中的文件推送到 GitHub Pages。

部署 Nuxt SPA 到 GitHub Pages 就是这样。但是，在将静态站点推送到 GitHub Pages 时，请确保在`/dist/`文件夹中包含一个`empty .nojekyll`文件。

Jekyll 是一个简单的、博客感知的静态站点生成器。它将纯文本转换为静态网站和博客。GitHub Pages 在幕后由 Jekyll 提供支持，默认情况下不会构建任何以点“.”、下划线“_”开头或以波浪符“~”结尾的文件或目录。这在为 GitHub Pages 提供静态站点时会成为问题，因为在构建 Nuxt SPA 时，`/_nuxt/`文件夹也会在`/dist/`文件夹内生成；Jekyll 会忽略这个`/_nuxt/`文件夹。为了解决这个问题，我们需要在`/dist/`文件夹中包含一个空的`.nojekyll`文件来关闭 Jekyll。当我们为 Nuxt SPA 构建静态页面时，会生成这个文件，所以确保将它推送到你的 GitHub 仓库中。

干得好 - 你已经完成了本书的另一短章节！如果你想在 Nuxt 中构建 SPA 而不是使用 Vue 或其他框架（如 Angular 和 React），Nuxt SPA 是一个很好的选择。但是，如果你提供需要立即或实时发布的社交媒体等网络服务，静态生成的 Nuxt SPA 可能不是一个好选择。这完全取决于你的业务性质，以及你是想要充分利用 Nuxt 的全能 SSR，还是只想使用 Nuxt 的客户端版本 - Nuxt SPA。接下来，我们将总结本章学到的内容。

# 总结

在本章中，我们学习了如何在 Nuxt 中开发、构建和部署 SPA，并了解了它与经典 SPA 的区别。我们还了解到，Nuxt SPA 可以是开发应用程序的一个很好选择，但是开发 Nuxt SPA 意味着我们将失去`nuxtServerInit`动作和`req`和`res` HTTP 对象。然而，我们可以使用客户端的`js-cookies`（或`localStorage`）和 Nuxt 插件来模拟`nuxtServerInit`动作。最后但并非最不重要的是，我们学习了如何在 GitHub Pages 上发布和提供静态生成的 Nuxt SPA。

到目前为止，在本书中，我们一直在为所有 Nuxt 应用程序和 API 使用 JavaScript。然而，在接下来的章节中，我们将探讨如何进一步使用 Nuxt，以便我们可以使用另一种语言**PHP**。我们将带领您了解 HTTP 消息和 PHP 标准，使用 PHP 数据库框架编写 CRUD 操作，并为 Nuxt 应用程序提供 PHP API。敬请期待！
