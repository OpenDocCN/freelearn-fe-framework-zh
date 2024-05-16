编写路由中间件和服务器中间件

还记得在第八章中使用 Koa 在服务器端创建中间件吗？中间件非常有用且强大，正如你在 Koa 应用程序中注意到的那样，你可以预测和控制整个应用程序的流程。那么在 Nuxt 中呢？嗯，在 Nuxt 中有两种类型的中间件：路由中间件和服务器中间件。在本章中，您将学习如何区分它们，并在进入下一章关于身份验证的章节之前创建一些基本的中间件，那里中间件是非常需要的。我们还将在接下来的章节中使用中间件。因此，在本章中，就像在许多以前的章节中一样，您将在 Vue 应用程序中创建一些带有导航守卫的中间件，以便在创建 Nuxt 应用程序中的路由中间件和服务器中间件之前掌握 Vue/Nuxt 系统中的中间件机制。

在这一章中，我们将涵盖以下主题：

+   使用 Vue Router 编写中间件

+   介绍 Vue CLI

+   在 Nuxt 中编写路由中间件

+   编写 Nuxt 服务器中间件

# 第十一章：使用 Vue Router 编写中间件

在学习 Nuxt 应用程序中的中间件如何工作之前，我们应该了解它在标准 Vue 应用程序中是如何工作的。此外，在 Vue 应用程序中创建中间件之前，让我们先了解它们是什么。

## 什么是中间件？

简而言之，中间件是位于两个或多个软件之间的软件层。这是软件开发中的一个古老概念。中间件是一个自 1968 年以来就一直在使用的术语。它在 1980 年代作为将新应用程序链接到旧的遗留系统的解决方案而变得流行。对于它有许多定义，例如（来自*Google 字典*）“[中间件是]在操作系统或数据库与应用程序之间充当桥梁的软件，尤其是在网络上。”

在 Web 开发世界中，服务器端软件或应用程序（如 Koa 和 Express）接收请求并输出响应。中间件是在接收请求后执行的程序或函数，它们产生的输出可以是最终输出，也可以被下一个中间件使用，直到循环完成。这也意味着我们可以有**多个**中间件，并且它们将按照声明的顺序执行：

![](img/cda0dfde-bb0a-4fe4-9a36-ebc285e49a1b.png)

此外，中间件不仅限于服务器端技术。当您的应用程序中有路由时，在客户端中也非常常见。Vue.js 的 Vue Router 就是使用这种中间件概念的一个很好的例子。我们已经在第四章 *添加视图、路由和过渡*中学习和使用了 Vue Router，为我们的 Vue 应用程序创建了路由器。现在，让我们深入了解 Vue Router 的高级用法 - 导航守卫。

## 安装 Vue Router

如果您从本书的开头开始就已经跟着章节走了，那么您应该已经知道如何从第四章 *添加视图、路由和过渡*中安装 Vue Router。然而，这里是一个快速回顾。

按照以下步骤直接下载 Vue Router：

1.  单击以下链接并下载源代码：

```js
https://unpkg.com/vue-router/dist/vue-router.js
```

1.  在 Vue 之后包含路由器，这样它就可以自动安装：

```js
<script src="/path/to/vue.js"></script>
<script src="/path/to/vue-router.js"></script>
```

或者，您可以通过 npm 安装 Vue Router：

1.  使用 npm 将路由器安装到您的项目中：

```js
$ npm i vue-router
```

1.  使用`use`方法显式注册路由器：

```js
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.use(VueRouter)
```

1.  一旦你安装好了路由器，你就可以开始使用 Vue Router 提供的导航守卫来创建中间件：

```js
const router = new VueRouter({ ... })
router.beforeEach((to, from, next) => {
  // ...
})
```

在前面的示例中，`beforeEach`导航守卫是一个全局导航守卫，当导航到任何路由时都会被调用。除了全局守卫，还有特定路由的导航守卫，我们将在下一节中更详细地探讨这一点。所以，让我们开始吧！

如果您想了解更多关于 Vue Router 的信息，请访问[`router.vuejs.org/`](https://router.vuejs.org/)。

## 使用导航守卫

导航守卫用于保护应用程序中的导航。这些守卫允许我们在进入、更新和离开路由之前调用函数。当某些条件不满足时，它们可以重定向或取消路由。有几种方式可以连接到路由导航过程中：全局、每个路由或在组件中。让我们在下一节中探索全局守卫。

请注意，您可以在我们的 GitHub 存储库的`/chapter-11/vue/non-sfc/`中找到以下所有示例。

### 创建全局守卫

Vue Router 提供了两种全局守卫 - 全局前置守卫和全局后置守卫。让我们学习如何在应用程序中应用它们之前先了解如何使用它们：

+   **全局前置守卫**：全局前置守卫在路由进入时调用。它们按特定顺序调用，并且可以是异步的。导航总是等待直到所有守卫都被解析。我们可以使用 Vue Router 的`beforeEach`方法注册这些守卫，如下所示：

```js
const router = new VueRouter({ ... })
router.beforeEach((to, from, next) => { ... })
```

+   **全局后置守卫**：全局后置守卫在路由进入后调用。与全局前置守卫不同，全局后置守卫没有`next`函数，因此它们不会影响导航。我们可以使用 Vue Router 的`afterEach`方法注册这些守卫，如下所示：

```js
const router = new VueRouter({ ... })
router.afterEach((to, from) => { ... })
```

让我们创建一个 Vue 应用程序，使用一个简单的 HTML 页面，并在以下步骤中使用这些守卫：

1.  使用`<router-link>`元素创建两个路由，如下所示：

```js
<div id="app">
  <p>
    <router-link to="/page1">Page 1</router-link>
    <router-link to="/page2">Page 2</router-link>
  </p>
  <router-view></router-view>
</div>
```

1.  为路由定义组件（`Page1`和`Page2`），并将它们传递给`<script>`块中的路由实例：

```js
const Page1 = { template: '<div>Page 1</div>' }
const Page2 = { template: '<div>Page 2</div>' }

const routes = [
  { path: '/page1', component: Page1 },
  { path: '/page2', component: Page2 }
]

const router = new VueRouter({
  routes
})
```

1.  在路由实例之后声明全局前置守卫和全局后置守卫，如下所示：

```js
router.beforeEach((to, from, next) => {
  console.log('global before hook')
  next()
})

router.afterEach((to, from,) => {
  console.log('global after hook')
})
```

1.  在守卫之后挂载根实例并运行我们的应用程序：

```js
const app = new Vue({
  router
}).$mount('#app')
```

1.  在浏览器中运行应用程序，当您在路由之间切换时，您应该在浏览器控制台中获得以下日志：

```js
global before hook
global after hook
```

全局守卫在你想要应用到所有路由的共同内容时非常有用。然而，有时我们只需要特定路由的特定内容。为此，您应该使用每个路由的守卫。让我们在下一节中学习如何部署它们。

### 创建每个路由的守卫

我们可以通过在路由的配置对象上直接使用`beforeEnter`方法或属性来创建每个路由的守卫。例如，看一下以下示例：

```js
beforeEnter: (to, from, next) => { ... }
// or:
beforeEnter (to, from, next) { ... }
```

让我们复制我们之前的 Vue 应用程序，并更改路由配置以使用这些每个路由的守卫，如下所示：

```js
const routes = [
  {
    path: '/page1',
    component: Page1,
    beforeEnter: (to, from, next) => {
      console.log('before entering page 1')
      next()
    }
  },
  {
    path: '/page2',
    component: Page2,
    beforeEnter (to, from, next) {
      console.log('before entering page 2')
      next()
    }
  }
]
```

当您导航到`/page1`时，您应该在浏览器控制台上获得“进入页面 1 之前”的日志，当您在`/page2`上时，您应该获得“进入页面 2 之前”的日志。因此，我们可以将守卫应用于页面的路由，那么将守卫应用于路由组件本身呢？答案是肯定的，我们可以。让我们继续下一节，学习如何使用组件内守卫来保护特定组件。

### 创建组件内守卫

我们可以在路由组件内部单独或一起使用以下方法来创建特定组件的导航守卫。

**beforeRouteEnter 守卫**：

就像在全局前置守卫和`beforeEnter`每个路由守卫中一样，`beforeRouteEnter`守卫在路由渲染组件之前调用，但它适用于组件本身。我们可以使用`beforeRouteEnter`方法注册这种类型的守卫，如下所示：

```js
beforeRouteEnter (to, from, next) { ... }
```

因为它在组件实例之前被调用，所以无法通过`this`关键字访问 Vue 组件。但可以通过将 Vue 组件的回调传递给`next`参数来解决这个问题：

```js
beforeRouteEnter (to, from, next) {
  next(vueComponent => { ... })
}
```

**beforeRouteLeave 守卫**：

相比之下，当由路由渲染的组件即将从中导航离开时，将调用`beforeRouteLeave`守卫。由于它在 Vue 组件渲染时被调用，因此可以通过`this`关键字访问 Vue 组件。我们可以使用`beforeRouteLeave`方法注册这种类型的守卫，如下所示：

```js
beforeRouteLeave (to, from, next) { ... }
```

通常，这种类型的守卫最适合用于防止用户意外离开路由。因此，可以通过调用`next(false)`来取消导航：

```js
beforeRouteLeave (to, from, next) {
  const confirmed = window.confirm('Are you sure you want to leave?')
  if (confirmed) {
    next()
  } else {
    next(false)
  }
}
```

**beforeRouteUpdate 守卫**：

当由路由渲染的组件已更改但组件在新路由中被重用时，将调用`beforeRouteUpdate`守卫；例如，如果您有使用相同路由组件的子路由组件：`/page1/foo`和`/page1/bar`。因此，从`/page1/foo`导航到`/page1/bar`将触发此方法。由于它在组件渲染时被调用，因此可以通过`this`关键字访问 Vue 组件。我们可以使用`beforeRouteUpdate`方法注册这种类型的守卫：

```js
beforeRouteUpdate (to, from, next) { ... }
```

请注意，`beforeRouteEnter`方法是唯一支持在`next`方法中使用回调的守卫。在调用`beforeRouteUpdate`和`beforeRouteLeave`方法之前，Vue 组件已经可用。因此，在这两种情况下在`next`方法中使用回调是不受支持的，因为这是不必要的。因此，如果要访问 Vue 组件，只需使用`this`关键字：

```js
beforeRouteUpdate (to, from, next) {
  this.name = to.params.name
  next()
}
```

现在，让我们使用以下守卫创建一个带有简单 HTML 页面的 Vue 应用：

1.  创建一个页面组件，其中包含`beforeRouteEnter`、`beforeRouteUpdate`和`beforeRouteLeave`方法，如下所示：

```js
const Page1 = {
  template: '<div>Page 1 {{ $route.params.slug }}</div>',
  beforeRouteEnter (to, from, next) {
    console.log('before entering page 1')
    next(vueComponent => {
      console.log('before entering page 1: ', 
       vueComponent.$route.path)
    })
  },
  beforeRouteUpdate (to, from, next) {
    console.log('before updating page 1: ', this.$route.path)
    next()
  },
  beforeRouteLeave (to, from, next) {
    console.log('before leaving page 1: ', this.$route.path)
    next()
  }
}
```

1.  创建另一个页面组件，只包含`beforeRouteEnter`和`beforeRouteLeave`方法，如下所示：

```js
const Page2 = {
  template: '<div>Page 2</div>',
  beforeRouteEnter (to, from, next) {
    console.log('before entering page 2')
    next(vueComponent => {
      console.log('before entering page 2: ', 
       vueComponent.$route.path)
    })
  },
  beforeRouteLeave (to, from, next) {
    console.log('before leaving page 2: ', this.$route.path)
    next()
  }
}
```

1.  在初始化路由器实例之前定义主路由和子路由，如下所示：

```js
const routes = [
  {
    path: '/page1',
    component: Page1,
    children: [
      {
        path: ':slug'
      }
    ]
  },
  {
    path: '/page2',
    component: Page2
  }
]
```

1.  使用`<router-link>` Vue 组件创建导航链接，如下所示：

```js
<div id="app">
  <ul>
    <li><router-link to="/">Home</router-link></li>
    <li><router-link to="/page1">Page 1</router-link></li>
    <li><router-link to="/page1/foo">Page 1: foo</router-link></li>
    <li><router-link to="/page1/bar">Page 1: bar</router-link></li>
    <li><router-link to="/page2">Page 2</router-link></li>
  </ul>
  <router-view></router-view>
</div>
```

1.  在浏览器中运行应用程序，当在路由之间切换时，你应该在浏览器控制台中得到以下日志：

+   当从`/`导航到`/page1`时，你应该看到以下内容：

```js
before entering page 1
before entering page 1: /page1
```

+   当从`/page1`导航到`/page2`时，你应该看到以下内容：

```js
before leaving page 1: /page1
before entering page 2
before entering page 2: /page2
```

+   当从`/page2`导航到`/page1/foo`时，你应该看到以下内容：

```js
before leaving page 2: /page2
before entering page 1
before entering page 1: /page1/foo
```

+   当从`/page1/foo`导航到`/page1/bar`时，你应该看到以下内容：

```js
before updating page 1: /page1/foo
```

+   当从`/page1/bar`导航到`/`时，你应该看到以下内容：

```js
before leaving page 1: /page1/bar
```

正如你所看到的，Vue 中的导航守卫只是允许我们创建中间件的 JavaScript 函数，带有一些默认参数。现在，让我们在下一节更仔细地看看每个守卫方法得到的参数（`to`、`from`和`next`）。

## 理解导航守卫的参数：to、from 和 next

你已经在前面的部分中看到了这些参数在导航守卫中的使用，但我们还没有向你详细介绍它们。所有守卫，除了`afterEach`全局守卫，都使用这三个参数：`to`、`from`和`next`。

**`to`参数**：

这个参数是你要导航到的路由对象（因此被称为*to*参数）。这个对象保存了 URL 和路由的解析信息：

| namemetapathhash | queryparamsfullPathmatched |
| --- | --- |

如果你想了解每个这些对象属性的更多信息，请访问[`router.vuejs.org/api/the-route-object`](https://router.vuejs.org/api/#the-route-object)。

**`from`参数**：

这个参数是你从中导航的当前路由对象。同样，这个对象保存了 URL 和路由的解析信息：

| namemetapathhash | queryparamsfullPathmatched |
| --- | --- |

**`next`参数**：

这个参数是一个函数，你必须调用它才能继续到队列中的下一个守卫（中间件）。如果你想中止当前的导航，你可以向这个函数传递一个`false`布尔值：

```js
next(false)
```

如果你想重定向到不同的位置，你可以使用以下代码：

```js
next('/')
// or
next({ path: '/' })
```

如果你想用`Error`的实例中止导航，你可以使用以下代码：

```js
const error = new Error('An error occurred!')
next(error)
```

然后，你可以从根目录捕获错误：

```js
router.onError(err
 => { ... })
```

现在，让我们创建一个带有简单 HTML 页面的 Vue 应用程序，并在以下步骤中尝试使用 next 函数：

1.  按照以下方式创建带有`beforeRouteEnter`方法的页面组件：

```js
const Page1 = {
  template: '<div>Page 1</div>',
  beforeRouteEnter (to, from, next) {
    const error = new Error('An error occurred!')
    error.statusCode = 500
    console.log('before entering page 1')
    next(error)
  }
}

 const Page2 = {
  template: '<div>Page 2</div>',
  beforeRouteEnter (to, from, next) {
    console.log('before entering page 2')
    next({ path: '/' })
  }
}
```

在上述代码中，我们将`Error`实例传递给`Page1`的下一个函数，同时将路由重定向到`Page2`的主页。

1.  在初始化路由实例之前定义路由，如下所示：

```js
const routes = [
  {
    path: '/page1',
    component: Page1
  },
  {
    path: '/page2',
    component: Page2
  }
]
```

1.  创建路由实例并使用`onError`方法*监听*错误：

```js
const router = new VueRouter({
  routes
})

router.onError(err => {
  console.error('Handling this error: ', err.message)
  console.log(err.statusCode)
})
```

1.  使用`<router-link>` Vue 组件创建以下导航链接：

```js
<div id="app">
  <ul>
    <li><router-link to="/">Home</router-link></li>
    <li><router-link to="/page1">Page 1</router-link></li>
    <li><router-link to="/page2">Page 2</router-link></li>
  </ul>
  <router-view></router-view>
</div>
```

1.  在浏览器中运行应用程序，当在路由之间切换时，你应该在浏览器控制台中看到以下日志：

+   当从`/`导航到`/page1`时，你应该看到以下内容：

```js
before entering page 1
Handling this error: An error occurred!
500
```

+   从`/page1`导航到`/page2`时，你应该看到以下内容：

```js
before entering page 2
```

当从`/page1`导航到`/page2`时，你也会注意到被重定向到`/`，因为有这行代码：`next({ path: '/' })`。

到目前为止，我们在单个 HTML 页面中创建了中间件。然而，在实际项目中，我们应该尝试使用你在之前章节中学到的 Vue 单文件组件（SFC）来创建它们。因此，在下一节中，你将学习如何使用 Vue CLI 在 Vue SFC 中创建中间件，而不是你到目前为止学到的自定义 webpack 构建过程。所以，让我们开始吧。

# 介绍 Vue CLI

我们在第五章中使用 webpack 创建了我们的自定义 Vue SFC 应用程序，*添加 Vue 组件*。作为开发人员，了解如何查看复杂事物的机制非常有用，我们还必须了解如何使用常见和标准模式与他人合作。因此，这些天，我们倾向于使用框架。Vue CLI 是 Vue 应用程序开发的标准工具。它可以执行我们的 webpack 自定义工具以及更多操作。如果你不想创建自己的 Vue SFC 开发工具，Vue CLI 是一个很好的选择。它支持 Babel、ESLint、TypeScript、PostCSS、PWA、单元测试和端到端测试。要了解更多关于 Vue CLI 的信息，请访问[`cli.vuejs.org/`](https://cli.vuejs.org/)。

## 安装 Vue CLI

使用 Vue CLI 非常容易入门。执行以下步骤：

1.  使用 npm 全局安装它：

```js
$ npm i -g @vue/cli
```

1.  在你想要的时候创建一个项目：

```js
$ vue create my-project
```

1.  您将被提示选择预设 - `default`或`手动选择功能`，如下所示：

```js
Vue CLI v4.4.6
? Please pick a preset: (Use arrow keys)
> default (babel, eslint) 
  Manually select features 
```

1.  选择`default`预设，因为我们可以随后手动安装所需的内容。当安装完成时，你应该在终端中看到类似以下输出的最后部分：

```js
Successfully created project my-project. 
Get started with the following commands: 

 **$ cd my-project**
 **$ npm run serve** 
```

1.  将目录更改为`my-project`并开始开发过程：

```js
$ npm run serve
```

你应该得到类似于这样的东西：

```js
 DONE Compiled successfully in 3469ms

  App running at:
  - Local: http://localhost:8080/
  - Network: http://199.188.0.44:8080/

  Note that the development build is not optimized.
  To create a production build, run npm run build.
```

在接下来的几节中，我们将把你在前几节中学到的导航守卫转换成使用 Vue CLI 的适当中间件。这意味着我们将把所有的钩子和守卫分开成单独的`.js`文件，并将它们保存在一个名为`middlewares`的常见文件夹中。然而，在我们这样做之前，我们应该先了解 Vue CLI 为我们生成的项目目录结构，然后添加我们自己需要的目录。让我们开始吧。

## 理解 Vue CLI 的项目结构

使用 Vue CLI 创建项目后，如果你查看项目目录，你会发现它为我们提供了一个基本的结构，如下所示：

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

从这个基本结构开始，我们可以构建和发展我们的应用程序。因此，让我们在`/src/`目录中开发我们的应用程序，并使用路由文件添加以下目录：

```js
└── src
    ├── middlewares/
    ├── store/
    ├── routes/
    └── router.js
```

我们将创建两个路由组件，登录和安全，作为 SFC 页面，并将安全页面设置为 403 受保护页面，这将要求用户登录以提供其姓名和年龄以访问页面。以下是我们这个简单的 Vue 应用程序所需的`/src/`目录中的文件和结构：

```js
└── src
    ├── App.vue
    ├── main.js
    ├── router.js
    ├── components
    │ ├── secured.vue
    │ └── login.vue
    ├── assets
    │ └── ...
    ├── middlewares
    │ ├── isLoggedIn.js
    │ └── isAdult.js
    ├── store
    │ ├── index.js
    │ ├── mutations.js
    │ └── actions.js
    └── routes
        ├── index.js
        ├── secured.js
        └── login.js
```

现在我们知道了我们的应用程序需要哪些目录和文件。接下来，我们将继续编写这些文件的代码。

## 使用 Vue CLI 编写中间件和 Vuex 存储

如果你看一下`package.json`，你会发现 Vue CLI 默认的依赖项非常基本和最小：

```js
// package.json
"dependencies": {
  "core-js": "².6.5",
  "vue": "².6.10"
}
```

因此，我们将安装我们项目的依赖项，并按以下步骤编写我们需要的代码：

1.  通过 npm 安装以下软件包：

```js
$ npm i vuex
$ npm i vue-router
$ npm i vue-router-multiguard
```

请注意，Vue 不支持每个路由多个守卫。因此，如果您想为一个路由创建多个守卫，Vue Router Multiguard 允许您这样做。有关此软件包的更多信息，请访问[`github.com/atanas-dev/vue-router-multiguard`](https://github.com/atanas-dev/vue-router-multiguard)。

1.  创建状态、操作和变异以在 Vuex 存储中存储经过身份验证的用户详细信息，以便任何组件都可以访问这些详细信息：

```js
// src/store/index.js
import Vue from 'vue'
import Vuex from 'vuex'

import actions from './actions'
import mutations from './mutations'

Vue.use(Vuex)

export default new Vuex.Store({
  state: { user: null },
  actions,
  mutations
})
```

为了可读性和简单性，我们将把存储的操作分成一个单独的文件，如下所示：

```js
// src/store/actions.js
const actions = {
  async login({ commit }, { name, age }) {
    if (!name || !age) {
      throw new Error('Bad credentials')
    }
    const data = {
      name: name,
      age: age
    }
    commit('setUser', data)
  },

  async logout({ commit }) {
    commit('setUser', null)
  }
}
export default actions
```

我们还将把存储的变异分成一个单独的文件，如下所示：

```js
// src/store/mutations.js
const mutations = {
  setUser (state, user) {
    state.user = user
  }
}
export default mutations
```

1.  创建一个中间件来确保用户已登录：

```js
// src/middlewares/isLoggedIn.js
import store from '../store'

export default (to, from, next) => {
  if (!store.state.user) {
    const err = new Error('You are not connected')
    err.statusCode = 403
    next(err)
  } else {
    next()
  }
}
```

1.  创建另一个中间件来确保用户年满 18 岁：

```js
// src/middlewares/isAdult.js
import store from '../store'

export default (to, from, next) => {
  if (store.state.user.age < 18) {
    const err = new Error('You must be over 18')
    err.statusCode = 403
    next(err)
  } else {
    next()
  }
}
```

1.  通过使用`vue-router-multiguard`在`beforeEnter`中插入多个中间件，将这两个中间件导入到 secured 路由中：

```js
// src/routes/secured.js
import multiguard from 'vue-router-multiguard'
import secured from '../components/secured.vue'
import isLoggedIn from '../middlewares/isLoggedIn'
import isAdult from '../middlewares/isAdult'

export default {
  name: 'secured',
  path: '/secured',
  component: secured,
  beforeEnter: multiguard([isLoggedIn, isAdult])
}
```

1.  创建一个简单的登录页面进行客户端身份验证。以下是我们需要的`login`和`logout`方法的基本输入字段：

```js
// src/components/login.vue
<form @submit.prevent="login">
  <p>Name: <input v-model="name" type="text" name="name"></p>
  <p>Age: <input v-model="age" type="number" name="age"></p>
  <button type="submit">Submit</button>
</form>

export default {
  data() {
    return {
      error: null,
      name: '',
      age: ''
    }
  },
  methods: {
    async login() { ... },
    async logout() { ... }
  }
}
```

1.  通过在`try`和`catch`块中分派`login`和`logout`动作方法来完成上述`login`和`logout`方法，如下所示：

```js
async login() {
  try {
    await this.$store.dispatch('login', {
      name: this.name,
      age: this.age
    })
    this.name = ''
    this.age = ''
    this.error = null
  } catch (e) {
    this.error = e.message
  }
},
async logout() {
  try {
    await this.$store.dispatch('logout')
  } catch (e) {
    this.error = e.message
  }
}
```

1.  将完成的`login`组件导入到登录路由中，如下所示：

```js
// src/routes/login.js
import Login from '../components/login.vue'

export default {
  name: 'login',
  path: '/',
  component: Login
}
```

请注意，我们将此路由命名为`login`，因为我们稍后需要此名称来在前面的中间件中从导航路由重定向时使用。

1.  将`login`和`secured`路由导入到索引路由中，如下所示：

```js
// src/routes/index.js
import login from './login'
import secured from './secured'

const routes = [
  login,
  secured
]

export default routes
```

1.  将前面的索引路由导入到 Vue Router 实例中，并使用`router.onError`捕获路由错误，如下所示：

```js
// src/router.js
import Vue from 'vue'
import VueRouter from 'vue-router'
import Routes from './routes'

Vue.use(VueRouter)

const router = new VueRouter({
  routes: Routes
})

router.onError(err => {
  alert(err.message)
  router.push({ name: 'login' })
})

export default router
```

在这一步中，我们使用`router.onError`来处理从中间件传递的`Error`对象，并使用`router.push`在不满足身份验证条件时将导航路由重定向到登录页面。对象的名称必须与*步骤 7*中的登录路由名称相同，即*login*。

1.  在`main`文件中导入路由并存储：

```js
// src/main.js
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import store from './store'

new Vue({
  router,
  store,
  render: h => h(App),
}).$mount('#app')
```

1.  使用`npm run serve`运行项目，您应该看到该应用程序加载在`localhost:8080`上。如果您在主页的输入字段中输入一个名称和小于 18 的数字，然后点击登录按钮，您应该会收到一个警告，指出“您必须年满 18 岁”当尝试访问 secured 页面时。另一方面，如果您输入一个大于 18 的数字，您应该会在 secured 页面上看到名称和数字。

```js
Name: John
Age: 20
```

您可以在我们的 GitHub 存储库的`/chapter-11/vue/vue-cli/basic/`中找到此应用程序的完整代码。您还可以在`/chapter-11/vue/webpack/`中找到具有自定义 webpack 的应用程序。

干得好！您已经成功完成了关于 Vue 项目中间件的所有章节。现在，让我们在接下来的章节中应用您刚刚学到的关于 Nuxt 项目的知识。

# 在 Nuxt 中编写路由中间件

理解了 Vue 中间件的工作原理后，就更容易在 Nuxt 中使用它，因为 Nuxt 已经为我们处理了 Vue Router。在接下来的章节中，我们将学习如何在 Nuxt 应用程序中使用全局和每个路由的中间件。

在 Nuxt 中，所有中间件都应该保存在`/middleware/`目录中，中间件的文件名将是中间件的名称。例如，`/middleware/user.js`是用户中间件。中间件将 Nuxt 上下文作为其第一个参数：

```js
export default (context) => { ... }
```

此外，中间件可以是异步的。

```js
export default async (context) => {
   const { data } = await axios.get('/api/path')
}
```

在通用模式中，中间件在服务器端只调用一次（例如，当首次请求 Nuxt 应用程序或刷新页面时），然后在客户端导航到其他路由时再次调用。另一方面，无论您是首次请求应用程序还是在首次请求后导航到更多路由时，中间件始终在客户端调用。中间件首先在 Nuxt 配置文件中执行，然后在布局中执行，最后在页面中执行。我们现在将在下一节开始编写一些全局中间件。

## 编写全局中间件

添加全局中间件非常简单；您只需在`config`文件的“路由器”选项中的“中间件”键中声明它们。例如，看一下以下内容：

```js
// nuxt.config.js
export default {
  router: {
    middleware: 'auth'
  }
}
```

现在，让我们按照以下步骤创建一些全局中间件。在这个练习中，我们想要从 HTTP 请求头中获取用户代理的信息，并跟踪用户正在导航到的路由：

1.  在`/middleware/`目录中创建两个中间件，一个用于获取用户代理信息，另一个用于获取用户正在导航到的路由路径信息：

```js
// middleware/user-agent.js
export default (context) => {
  context.userAgent = process.server ? context.req.headers[
    'user-agent'] : navigator.userAgent
}

// middleware/visits.js
export default ({ store, route, redirect }) => {
  store.commit('addVisit', route.path)
}
```

1.  在“路由器”选项中的“中间件”键中声明前面的中间件，如下所示：

```js
// nuxt.config.js
module.exports = {
  router: {
    middleware: ['visits', 'user-agent']
  }
}
```

请注意，在 Nuxt 中，我们不需要像在 Vue 应用程序中那样调用多个守卫的第三方包。

1.  创建存储访问路由的存储器状态和变化：

```js
// store/state.js
export default () => ({
  visits: []
})

// store/mutations.js
export default {
  addVisit (state, path) {
    state.visits.push({
      path,
      date: new Date().toJSON()
    })
  }
}
```

1.  在`about`页面中使用`user-agent`中间件：

```js
// pages/about.vue
<p>{{ userAgent }}</p>

export default {
  asyncData ({ userAgent }) {
    return {
      userAgent
    }
  }
}
```

1.  至于`visits`中间件，我们希望在组件上使用它，然后将该组件注入到我们的布局中，即`default.vue`布局。首先，在`/components/`目录中创建`visits`组件：

```js
// components/visits.vue
<li v-for="(visit, index) in visits" :key="index">
  <i>{{ visit.date | dates }} | {{ visit.date | times }}</i> - {{ 
    visit.path }}
</li>

export default {
  filters: {
    dates(date) {
      return date.split('T')[0]
    },
    times(date) {
      return date.split('T')[1].split('.')[0]
    }
  },
  computed: {
    visits() {
      return this.$store.state.visits.slice().reverse()
    }
  }
}
```

因此，我们在此组件中创建了两个过滤器。`date`过滤器用于从字符串中获取日期。例如，我们将从`2019-05-24T21:55:44.673Z`中获得`2019-05-24`。相比之下，`time`过滤器用于从字符串中获取时间。例如，我们将从`2019-05-24T21:55:44.673Z`中获得`21:55:44`。

1.  将`visits`组件导入到我们的布局中：

```js
// layouts/default.vue
<template>
  <Visits />
</template>

import Visits from '~/components/visits.vue'
export default {
  components: {
    Visits
  }
}
```

当我们在路由之间导航时，我们应该在浏览器中获得以下结果：

```js
2019-06-06 | 01:55:44 - /contact
2019-06-06 | 01:55:37 - /about
2019-06-06 | 01:55:30 - /
```

此外，当您在关于页面时，应该从请求头中获取用户代理的信息：

```js
Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.75 Safari/537.36
```

您可以在我们的 GitHub 存储库中的`/chapter-11/nuxt-universal/route-middleware/global/`中找到上述源代码。

全局中间件就介绍到这里。现在，让我们继续下一节的路由中间件。

## 编写路由中间件

添加路由中间件也非常简单；您只需在特定布局或页面的`middleware`键中声明它们。例如，看一下以下内容：

```js
// pages/index.vue or layouts/default.vue
export default {
  middleware: 'auth'
}
```

因此，在接下来的步骤中，让我们创建一些路由中间件。在这个练习中，我们将使用会话和 JSON Web Tokens（JWT）来访问受限页面或受保护的 API。虽然在现实生活中，我们可以只使用会话或令牌进行身份验证系统，但我们将在练习中同时使用两者，以便了解如何将它们一起用于潜在更复杂的生产系统。在我们的练习中，我们希望用户登录并从服务器获取令牌。当令牌过期或无效时，用户将无法访问受保护的路由。

此外，当会话时间结束时，用户将被注销：

1.  创建一个`auth`中间件来检查我们存储中是否有任何数据的状态。如果没有经过身份验证的数据，则我们使用 Nuxt 上下文中的`error`函数将错误发送到前端：

```js
// middleware/auth.js
export default function ({ store, error }) {
  if (!store.state.auth) {
    error({
      message: 'You are not connected',
      statusCode: 403
    })
  }
}
```

1.  创建一个`token`中间件来确保令牌在存储中；否则，它将错误发送到前端。如果存储中存在令牌，我们将使用令牌将`Authorization`设置为默认的`axios`标头：

```js
// middleware/token.js
export default async ({ store, error }) => {
  if (!store.state.auth.token) {
    error({
      message: 'No token',
      statusCode: 403
    })
  }
  axios.defaults.headers.common['Authorization'] = `Bearer: ${store.state.auth.token}`
}
```

1.  将这两个前置中间件添加到受保护页面的`middleware`键上：

```js
// pages/secured.vue
<p>{{ greeting }}</p>

export default {
  async asyncData ({ redirect }) {
    try {
      const { data } = await axios.get('/api/private')
      return {
        greeting: data.data.message
      }
    } catch (error) {
      if(process.browser){
        alert(error.response.data.message)
      }
      return redirect('/login')
    }
  },
  middleware: ['auth', 'token']
}
```

在请求头中设置带有 JWT 的`Authorization`标头后，我们可以访问受保护的 API 路由，这些路由由服务器端中间件保护（我们将在第十二章中了解更多，*创建用户登录和 API 身份验证*）。我们将从受保护的 API 路由获取我们想要访问的数据，并且如果令牌不正确或已过期，将收到错误消息提示。

1.  在`/store/`目录中创建存储的状态、mutations 和 actions 以存储经过身份验证的数据：

```js
// store/state.js
export default () => ({
  auth: null
})

// store/mutations.js
export default {
  setAuth (state, data) {
    state.auth = data
  }
}

// store/actions.js
export default {
  async login({ commit }, { username, password }) {
    try {
      const { data } = await axios.post('/api/public/users/login', 
      { username, password })
      commit('setAuth', data.data)
    } catch (error) {
      // handle error
    }
  },

  async logout({ commit }) {
    await axios.post('/api/public/users/logout')
    commit('setAuth', null)
  }
}
```

已知并且预期的行为是，当页面刷新时，存储的状态会重置为默认值。如果我们想要保持状态，有一些解决方案可以使用：

1.  localStorage

1.  sessionStorage

1.  vuex-persistedstate（一个 Vuex 插件）

然而，在我们的情况下，由于我们使用会话来存储认证信息，我们实际上可以通过以下方式从会话中重新获取我们的数据：

1.  req.ctx.session（Koa）或 req.session（Express）

1.  req.headers.cookie

一旦我们决定要选择哪种解决方案或选项（比如 `req.headers.cookie`），然后我们可以按照以下方式重新填充状态：

```js
// store/index.js
const cookie = process.server ? require('cookie') : undefined

export const actions = {
  nuxtServerInit({ commit }, { req }) {
    var session = null
    var auth = null
    if (req.headers.cookie && req.headers.cookie.indexOf('koa:sess') > -1) {
      session = cookie.parse(req.headers.cookie)['koa:sess']
    }
    if (session) {
      auth = JSON.parse(Buffer.from(session, 'base64'))
      commit('setAuth', auth)
    }
  }
}
```

您可以在我们的 GitHub 存储库中的 `/chapter-11/nuxt-universal/route-middleware/per-route/` 中找到前面的源代码。

当所有前面的步骤都遵循并且中间件已经创建好后，我们可以通过 `npm run dev` 来运行这个简单的认证应用程序，看看它是如何工作的。我们将在下一章中介绍服务器端认证。现在，我们只需要专注于中间件并理解它的工作原理，这将有助于我们在下一章中。现在，让我们继续本章的最后一部分 - 服务器中间件。

# 编写 Nuxt 服务器中间件

简而言之，服务器中间件是在 Nuxt 中用作中间件的服务器端应用程序。自从第八章以来，我们一直在使用像 Koa 这样的服务器端框架来运行我们的 Nuxt 应用程序，*添加服务器端框架*。如果您使用 Express，这是您 `package.json` 文件中的 `scripts` 对象：

```js
// package.json
"scripts": {
  "dev": "cross-env NODE_ENV=development nodemon server/index.js --watch 
   server",
  "build": "nuxt build",
  "start": "cross-env NODE_ENV=production node server/index.js",
  "generate": "nuxt generate"
}
```

在这个 npm 脚本中，`dev` 和 `start` 脚本指示服务器从 `/server/index.js` 运行您的应用程序。这可能不是理想的，因为我们将 Nuxt 和服务器端框架紧密耦合在一起，这会导致在配置中额外的工作。但是，我们可以告诉 Nuxt 不要附加到 `/server/index.js` 中的服务器端框架配置，并保持我们原始的 Nuxt 运行脚本如下所示：

```js
// package.json
"scripts": {
  "dev": "nuxt",
  "build": "nuxt build",
  "start": "nuxt start",
  "generate": "nuxt generate"
}
```

相反，我们可以在 Nuxt 配置文件中使用 `serverMiddleware` 属性，使服务器端框架在 Nuxt 下运行。例如，看一下以下内容：

```js
// nuxt.config.js
export default {
  serverMiddleware: [
    '~/api'
  ]
}
```

与路由中间件不同，路由中间件在客户端每个路由之前调用，而服务器中间件总是在 `vue-server-renderer` 之前在服务器端调用。因此，服务器中间件可以用于服务器特定的任务，就像我们在之前的章节中使用 Koa 或 Express 一样。因此，让我们在接下来的章节中探讨如何在 Express 和 Koa 中使用作为我们的服务器中间件。

## 使用 Express 作为 Nuxt 的服务器中间件

让我们使用 Express 作为 Nuxt 的服务器中间件来创建一个简单的身份验证应用程序。我们将继续使用身份验证练习中的客户端代码，以及你在前一节中学到的每个路由中间件，其中用户需要提供用户名和密码才能访问受保护的页面。此外，我们将使用 Vuex 存储来集中存储认证用户数据，就像以前一样。这个练习的主要区别在于，我们的 Nuxt 应用程序将作为中间件*移出*服务器端应用程序，而服务器端应用程序将作为中间件*移入*Nuxt 应用程序。所以，让我们按照以下步骤开始：

1.  安装`cookie-session`和`body-parser`作为服务器中间件，并在 Nuxt 的`config`文件中添加它们之后的 API 路径，如下所示：

```js
// nuxt.config.js
import bodyParser from 'body-parser'
import cookieSession from 'cookie-session'

export default {
  serverMiddleware: [
    bodyParser.json(),
    cookieSession({
      name: 'express:sess',
      secret: 'super-secret-key',
      maxAge: 60000
    }),
    '~/api'
  ]
}
```

请注意，cookie-session 是 Express 的基于 cookie 的会话中间件，它将会话存储在客户端的 cookie 中。相比之下，body-parser 是 Express 的一个用于解析请求体的中间件，就像你在第八章中学到的 Koa 的`koa-bodyparser`一样。

有关 Express 的`cookie-session`和`body-parser`的更多信息，请访问[`github.com/expressjs/cookie-session`](https://github.com/expressjs/cookie-session)和[`github.com/expressjs/body-parser`](https://github.com/expressjs/body-parser)。

1.  使用`index.js`文件创建一个`/api/`目录，在其中导入 Express 并将其导出为另一个服务器中间件：

```js
// api/index.js
import express from 'express'
const app = express()

app.get('/', (req, res) => res.send('Hello World!'))

// Export the server middleware
export default {
  path: '/api',
  handler: app
}
```

1.  使用`npm run dev`运行应用程序，你应该在`localhost:3000/api`中收到“Hello World!”消息。

1.  按照以下步骤在`/api/index.js`中添加`login`和`logout`的 post 方法：

```js
// api/index.js
app.post('/login', (req, res) => {
  if (req.body.username === 'demo' && req.body.password === 'demo') {
    req.session.auth = { username: 'demo' }
    return res.json({ username: 'demo' })
  }
  res.status(401).json({ message: 'Bad credentials' })
})

app.post('/logout', (req, res) => {
  delete req.session.auth
  res.json({ ok: true })
})
```

在上述代码中，当用户成功登录时，我们将认证有效载荷存储到 Express 会话中作为 HTTP 请求对象中的`auth`。然后，当用户注销时，我们将通过删除它来清除`auth`会话。

1.  创建一个包含`state.js`和`mutations.js`的存储，就像你为编写每个路由中间件所做的那样，如下所示：

```js
// store/state.js
export default () => ({
  auth: null,
})

// store/mutations.js
export default {
  setAuth (state, data) {
    state.auth = data
  }
}
```

1.  就像编写每个路由中间件一样，在存储中的`actions.js`文件中创建`login`和`logout`动作方法，如下所示：

```js
// store/actions.js
import axios from 'axios'

export default {
  async login({ commit }, { username, password }) {
    try {
      const { data } = await axios.post('/api/login', { username,
        password })
      commit('setAuth', data)
    } catch (error) {
      // handle error...
    }
  },

  async logout({ commit }) {
    await axios.post('/api/logout')
    commit('setAuth', null)
  }
}
```

1.  在存储的`index.js`中添加一个`nuxtServerInit`动作，以便在刷新页面时从 Express 会话中重新填充状态到 HTTP 请求对象中：

```js
// store/index.js
export const actions = {
  nuxtServerInit({ commit }, { req }) {
    if (req.session && req.session.auth) {
      commit('setAuth', req.session.auth)
    }
  }
}
```

1.  最后，就像在逐路由中间件身份验证中一样，在`/pages/`目录中创建一个登录页面，并使用相同的`login`和`logout`方法来调度存储中的`login`和`logout`操作方法：

```js
// pages/index.vue
<form v-if="!$store.state.auth" @submit.prevent="login">
  <p v-if="error" class="error">{{ error }}</p>
  <p>Username: <input v-model="username" type="text"
     name="username"></p>
  <p>Password: <input v-model="password" type="password" 
     name="password"></p>
  <button type="submit">Login</button>
</form>

export default {
  data () {
    return {
      error: null,
      username: '',
      password: ''
    }
  },
  methods: {
    async login () { ... },
    async logout () { ... }
  }
}
```

1.  使用`npm run dev`运行应用程序。您应该有一个与以前一样工作的身份验证应用程序，但它不再是从`/server/index.js`运行的。

你可以在我们的 GitHub 存储库的`/chapter-11/nuxt-universal/server-middleware/express/`中找到前面的源代码。

使用`serverMiddleware`属性使我们的 Nuxt 应用程序看起来整洁，感觉轻盈，不是吗？通过这种方法，我们也可以使其更加灵活，因为我们可以使用任何服务器端框架或应用程序。例如，我们可以使用 Koa，而不是使用 Express，我们将在下一节中讨论。

## 使用 Koa 作为 Nuxt 的服务器中间件

就像 Koa 和 Express 一样，Connect 是一个简单的框架，用于粘合各种中间件来处理 HTTP 请求。Nuxt 在内部使用 Connect 作为服务器，因此大多数 Express 中间件都可以与 Nuxt 的服务器中间件一起使用。相比之下，Koa 中间件要作为 Nuxt 的服务器中间件工作要困难一些，因为在 Koa 中，`req`和`res`对象被*隐藏*并保存在`ctx`中。我们可以通过一个简单的“Hello World”消息来比较这三个框架，如下所示：

```js
// Connect
const connect = require('connect')
const app = connect()
app.use((req, res, next) => res.end('Hello World'))

// Express
const express = require('express')
const app = express()
app.get('/', (req, res, next) => res.send('Hello World'))

// Koa
const Koa = require('koa')
const app = new Koa()
app.use(async (ctx, next) => ctx.body = 'Hello World')
```

请注意，`req`是一个 Node.js HTTP 请求对象，而`res`是一个 Node.js HTTP 响应对象。它们可以被命名为任何你喜欢的东西，例如，*request*而不是*req*和*response*而不是*res*。从前面的比较中，你可以看到 Koa 如何与其他框架不同地处理这两个对象。因此，我们不能像在 Express 中那样将 Koa 用作 Nuxt 的服务器中间件，也不能在`serverMiddleware`属性中定义任何 Koa 中间件，而只能添加 Koa API 所在目录的路径。请放心，让它们作为 Nuxt 应用程序中的中间件工作并不困难。让我们继续以下步骤：

1.  添加我们想要使用 Koa 创建 API 的路径，如下所示：

```js
// nuxt.config.js
export default {
  serverMiddleware: [
    '~/api'
  ]
}
```

1.  导入`koa`和`koa-router`，使用路由创建一个`Hello World!`消息，然后将它们导出到`/api/`目录中的`index.js`文件中：

```js
// api/index.js
import Koa from 'koa'
import Router from 'koa-router'

router.get('/', async (ctx, next) => {
  ctx.type = 'json'
  ctx.body = {
    message: 'Hello World!'
  }
})

app.use(router.routes())
app.use(router.allowedMethods())

// Export the server middleware
export default {
  path: '/api',
  handler: app.listen()
}
```

1.  导入`koa-bodyparser`和`koa-session`，并在`/api/index.js`文件中将它们注册为中间件，如下所示：

```js
// api/index.js
import bodyParser from 'koa-bodyparser'
import session from 'koa-session'

const CONFIG = {
  key: 'koa:sess',
  maxAge: 60000,
}

app.use(session(CONFIG, app))
app.use(bodyParser())
```

1.  使用 Koa 路由创建`login`和`logout`路由，如下所示：

```js
// api/index.js
router.post('/login', async (ctx, next) => {
  let request = ctx.request.body || {}
  if (request.username === 'demo' && request.password === 'demo') {
    ctx.session.auth = { username: 'demo' }
    ctx.body = {
      username: 'demo'
    }
  } else {
    ctx.throw(401, 'Bad credentials')
  }
})

router.post('/logout', async (ctx, next) => {
  ctx.session = null
  ctx.body = { ok: true }
})
```

在上述代码中，就像在上一节中的 Express 示例中一样，当用户成功登录时，我们将经过身份验证的有效负载存储到 Koa 会话中的`auth`中。然后，当用户注销时，我们将通过将会话设置为`null`来清除`auth`会话。

1.  创建一个带有状态、变异和操作的存储，就像您在 Express 示例中所做的那样。此外，在存储中的`index.js`文件中创建`nuxtServerInit`，就像您在编写每个路由中间件时所做的那样：

```js
// store/index.js
export const actions = {
  nuxtServerInit({ commit }, { req }) {
    // ...
  }
}
```

1.  就像以前一样，在`/pages/`目录中创建`login`和`logout`方法来调度存储中的操作方法：

```js
// pages/index.vue
<form v-if="!$store.state.auth" @submit.prevent="login">
  //...
</form>

export default {
  methods: {
    async login () { ... },
    async logout () { ... }
  }
}
```

1.  使用`npm run dev`运行应用程序。您应该有一个身份验证应用程序，其工作方式与上一节中 Express 中的应用程序相同，但它不再是从`/server/index.js`运行的。

您可以在我们的 GitHub 存储库的`/chapter-11/nuxt-universal/server-middleware/koa/`中找到此示例的整个源代码。

根据您的喜好，您可以在下一个项目中使用 Express 或 Koa 作为 Nuxt 的服务器中间件。在本书中，我们主要使用 Koa 因为它简单易用。您甚至可以创建自定义服务器中间件，而无需使用它们中的任何一个。让我们在下一节中看看如何创建自定义服务器中间件。

## 创建自定义服务器中间件

由于 Nuxt 在内部使用 Connect 作为服务器，因此我们可以添加自定义中间件，而无需外部服务器，如 Koa 或 Express。您可以开发一个复杂的 Nuxt 服务器中间件，就像我们在前几节中使用 Koa 和 Express 一样。但是，让我们不要无休止地重复我们已经做过的事情。让我们创建一个非常基本的自定义中间件，以打印“Hello World”消息来确认从基本中间件构建复杂中间件的可行性：

1.  添加我们想要创建自定义中间件的路径：

```js
// nuxt.config.js
serverMiddleware: [
  { path: '/api', handler: '~/api/index.js' }
]
```

1.  将 API 路由添加到`/api/`目录中的`index.js`文件中：

```js
// api/index.js
export default function (req, res, next) {
  res.end('Hello world!')
}
```

1.  使用`npm run dev`运行应用程序，并导航到`localhost:3000/api`。您应该在屏幕上看到打印的“Hello World!”消息。

您可以在[`github.com/senchalabs/connect`](https://github.com/senchalabs/connect)上查找 Connect 文档以获取更多信息。此外，您可以在我们的 GitHub 存储库的`/chapter-11/nuxt-universal/server-middleware/custom/`中找到此示例的源代码。

干得好！ 你已经成功完成了 Nuxt 的另一个重要章节。在继续下一章之前，让我们总结一下你到目前为止学到的东西。

# 总结

在本章中，你学到了路由中间件和服务器中间件之间的区别。你使用了 Vue Router 的导航守卫来为 Vue 应用程序创建中间件。你还使用了 Vue CLI 来开发一个简单的 Vue 身份验证应用程序。根据你对 Vue 应用程序的学习，你在 Nuxt 应用程序中使用了全局和每个路由的中间件来实现相同的概念（路由中间件）。之后，你学习了 Nuxt 的服务器中间件以及如何使用 Express 和 Koa 作为服务器中间件。中间件对于身份验证和安全非常重要和有用。我们已经制作了一些身份验证应用程序，并将在下一章中更详细地研究和理解它们。

在下一章中，你将详细学习有关开发用户登录和身份验证 API 的内容，以改进你在本章中创建的身份验证应用程序。我们将为你介绍基于会话的身份验证和基于令牌的身份验证。虽然你已经使用这两种技术创建了身份验证应用程序，但我们还没有解释它们是什么。但请放心，你将在下一章更好地理解它们。除此之外，你还将学习如何为你的 Nuxt 应用程序创建后端和前端身份验证，并使用 Google OAuth 进行登录。所以，请继续关注！
