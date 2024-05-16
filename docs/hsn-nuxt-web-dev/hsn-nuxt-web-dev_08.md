编写插件和模块

还记得自从第三章以来在 Nuxt 应用程序中编写一些简单的插件吗，*添加 UI 框架*？正如我们之前提到的，插件本质上是**JavaScript 函数**。在 web 开发中，您总是需要编写自定义函数以适应您的情况，在本书中我们将创建相当多的函数。在本章中，我们将更详细地了解为您的 Nuxt 应用程序创建自定义插件，以及自定义模块。您将学习在 Vue 应用程序中创建自定义插件并在 Nuxt 应用程序中实现它们。然后，您将学习如何在插件之上创建自定义 Nuxt 模块。您还将学习导入和安装现有的 Vue 插件和 Nuxt 模块，这些插件和模块是来自 Vue 和 Nuxt 社区的贡献，可以在您的 Nuxt 应用程序中使用。无论是自定义的还是外部导入的，学习和理解 Vue 插件和 Nuxt 模块都很重要，因为在接下来的章节中我们将经常使用其中的一些。

本章我们将涵盖以下主题：

+   编写 Vue 插件

+   在 Nuxt 中编写全局函数

+   编写 Nuxt 模块

+   编写异步 Nuxt 模块

+   编写 Nuxt 模块片段

# 第六章：编写 Vue 插件

插件是封装在`.js`文件中的全局 JavaScript 函数，可以通过使用`Vue.use`全局方法在应用程序中安装。我们在第四章的过去示例中使用了一些 Vue 插件，例如`vue-router`和`vue-meta`。这些插件必须在使用`new`语句初始化根 Vue 之前通过`Vue.use`方法安装，如下例所示：

```js
// src/entry.js
import Vue from 'vue'
import Meta from 'vue-meta'

Vue.use(Meta)
new VueRouter({ ... })
```

您可以通过`Vue.use`将选项传递到插件中以配置插件的格式：

```js
Vue.use(<plugin>, <options>)
```

例如，我们可以将以下选项传递到`vue-meta`插件中：

```js
Vue.use(Meta, {
  keyName: metaData, // default => metaInfo
  refreshOnceOnNavigation: true // default => false
})
```

选项是可选的。这意味着您可以在不传递任何选项的情况下使用插件本身。`Vue.use`还可以防止您意外多次注入相同的插件，因此多次调用插件将只安装一次。

您可以查看`awesome-vue`，这是一个庞大的社区贡献的插件和库集合，网址为`https://github.com/vuejs/awesome-vuecomponents--libraries`。

现在让我们在下一节中探讨如何创建 Vue 插件。

## 在 Vue 中编写自定义插件

编写 Vue 插件相当容易。您只需要在插件中使用一个`install`方法来接受`Vue`作为第一个参数和`options`作为第二个参数：

```js
// plugin.js
export default {
  install (Vue, options) {
    // ...
  }
}
```

让我们为标准 Vue 应用程序创建一个简单的自定义问候插件，以不同的语言。可以通过`options`参数配置语言；当没有提供选项时，将使用英语作为默认语言：

1.  在`/src/`目录中创建一个`/plugins/`文件夹，并在其中创建一个`basic.js`文件，其中包含以下代码：

```js
// src/plugins/basic.js
export default {
  install (Vue, options) {
    if (options === undefined) {
      options = {}
    }
    let { language } = options
    let languages = {
      'EN': 'Hello!',
      'ES': 'Hola!'
    }
    if (language === undefined) {
      language = 'EN'
    }
    Vue.prototype.$greet = (name) => {
      return languages[language] + ' ' + name
    }
    Vue.prototype.$message = 'Helló Világ!'
  }
}
```

在这个简单的插件中，我们还添加了一个名为`$message`的实例属性，其默认值为匈牙利语的“Hello World!”（`Helló Világ!`），当此插件在组件中使用时可以进行修改。请注意，`{ language } = options`是使用 ES6 编写`language = options.language`的方式。此外，我们应该使用`$`作为方法和属性的前缀，因为这是一种惯例。

1.  安装和配置此插件如下：

```js
// src/entry.js
import PluginSample from './plugins/basic'
Vue.use(PluginBasic, {
  language: 'ES'
})
```

1.  然后我们可以在任何 Vue 组件中全局使用该插件，就像以下示例中一样：

```js
// src/components/home.vue
<p>{{ $greet('John') }}</p>
<p>{{ $message }}</p>
<p>{{ messages }}</p>

export default {
  data () {
    let helloWorld = []
    helloWorld.push(this.$message)

    this.$message = 'Ciao mondo!'
    helloWorld.push(this.$message)

    return { messages: helloWorld }
  }
}
```

因此，当您在浏览器上运行应用程序时，您应该在屏幕上看到以下输出：

```js
Hola! John
Ciao mondo!
[ "Helló Világ!", "Ciao mondo!" ]
```

您还可以在插件中使用`component`或`directive`，就像以下示例中一样：

```js
// src/plugins/component.js
export default {
  install (Vue, options) {
    Vue.component('custom-component', {
     // ...
    })
  }
}

// src/plugins/directive.js
export default {
  install (Vue, options) {
    Vue.directive('custom-directive', {
      bind (el, binding, vnode, oldVnode) {
        // ...
      }
    })
  }
}
```

我们还可以使用`Vue.mixin()`将插件注入到所有组件中，如下所示：

```js
// src/plugins/plugin-mixin.js
export default {
  install (Vue, options) {
    Vue.mixin({
      // ...
    })
  }
}
```

您可以在我们的 GitHub 存储库的`/chapter-6/vue/webpack/`中找到前面的示例 Vue 应用程序。

就是这样。创建一个可以在 Vue 应用程序中安装和使用的 Vue 插件非常简单，不是吗？那么在 Nuxt 应用程序中呢？我们如何在 Nuxt 应用程序中安装前面的自定义 Vue 插件？让我们在下一节中找出答案。

## 将 Vue 插件导入到 Nuxt 中

在 Nuxt 应用程序中，该过程基本相同。所有插件都必须在初始化根 Vue 之前运行。因此，如果我们想要使用 Vue 插件，就像之前的示例插件一样，我们需要在启动 Nuxt 应用程序之前设置插件。让我们将我们的自定义`basic.js`插件复制到 Nuxt 应用程序的`/plugins/`目录中，然后执行以下步骤来安装它：

1.  创建一个`basic-import.js`文件，以以下方式在`/plugins/`目录中导入`basic.js`：

```js
// plugins/basic-import.js
import Vue from 'vue'
import PluginSample from './basic'

Vue.use(PluginSample)
```

这次在使用`Vue.use`方法安装插件时，我们跳过了选项。

1.  将`basic-import.js`的文件路径添加到 Nuxt 配置文件的`plugins`选项中，如下所示：

```js
export default {
  plugins: [
    '~/plugins/basic-import',
  ]
}
```

1.  在任何喜欢的页面中使用此插件-就像我们在 Vue 应用程序中所做的那样：

```js
// pages/index.vue
<p>{{ $greet('Jane') }}</p>
<p>{{ $message }}</p>
<p>{{ messages }}</p>

export default {
  data () {
    let helloWorld = []
    helloWorld.push(this.$message)

    this.$message = 'Olá Mundo!'
    helloWorld.push(this.$message)

    return { messages: helloWorld }
  }
}
```

1.  在浏览器上运行 Nuxt 应用程序，您应该在屏幕上看到以下输出：

```js
Hello! Jane
Olá Mundo!
[ "Helló Világ!", "Olá Mundo!" ]
```

这次我们使用`$greet`方法得到了英文版的“Hello!”，因为在安装插件时没有设置任何语言选项。此外，在这个索引页面的`<template>`块中，你将得到“Olá Mundo!”的`$message`，而在其他页面（例如`/about`、`/contact`）上，你将得到“Helló Világ!”，因为我们只在索引页面上设置了这个葡萄牙语版本的“Hello World!”，即`this.$message = 'Olá Mundo!'`。

正如我们在本章开头提到的，有一个庞大的社区贡献的 Vue 插件集合，可能对你的 Nuxt 应用程序有用，但是一些插件可能只在浏览器中工作，因为它们缺乏 SSR（服务器端渲染）支持。因此，在接下来的部分，我们将看看如何解决这种类型的插件。

## 导入没有 SSR 支持的外部 Vue 插件

在 Nuxt 中，有一些 Vue 插件已经预先安装好了，比如`vue-router`、`vue-meta`、`vuex`和`vue-server-renderer`。未安装的插件可以按照我们在上一节中安装自定义 Vue 插件的步骤轻松排序。以下是我们如何在 Nuxt 应用程序中使用`vue-notifications`的示例：

1.  使用 npm 安装插件：

```js
$ npm i vue-notification
```

1.  导入并注入插件，就像我们使用自定义插件一样：

```js
// plugins/vue-notifications.js
import Vue from 'vue'
import VueNotifications from 'vue-notifications'

Vue.use(VueNotifications)
```

1.  将文件路径包含到 Nuxt 配置文件中：

```js
// nuxt.config.js:
export default {
  plugins: ['~/plugins/vue-notifications']
}
```

对于没有 SSR 支持的插件，或者当你只想在客户端上使用这个插件时，你可以在`plugins`选项中使用`mode: 'client'`选项，以确保这个插件不会在服务器端执行，就像下面的例子一样：

```js
// nuxt.config.js
export default {
  plugins: [
    { src: '~/plugins/vue-notifications', mode: 'client' }
  ]
}
```

如你所见，安装 Vue 插件只需要三个步骤，无论是外部插件还是自定义插件。总之，Vue 插件是通过使用`Vue.use`方法将全局 JavaScript 函数注入到 Vue 实例中，并通过在插件内部暴露`install`方法来实现的。但在 Nuxt 本身中，还有其他创建全局函数的方法，可以将它们注入到 Nuxt 上下文（`context`）和 Vue 实例（`$root`）中，而无需使用`install`方法。我们将在接下来的部分中探讨这些方法。

有关`vue-notifications`的更多信息，请访问`https://github.com/euvl/vue-notification`。

# 在 Nuxt 中编写全局函数

在 Nuxt 中，我们可以通过将它们注入到以下三个项目中来创建“插件”或全局函数：

+   Vue 实例（客户端）：

```js
// plugins/<function-name>.js
import Vue from 'vue'
Vue.prototype.$<function-name> = () => {
  //...
}
```

+   Nuxt 上下文（服务器端）：

```js
// plugins/<function-name>.js
export default (context, inject) => {
  context.app.$<function-name> = () => {
    //...
  }
}
```

+   Vue 实例和 Nuxt 上下文：

```js
// plugins/<function-name>.js
export default (context, inject) => {
  inject('<function-name>', () => {
    //...
  })
}
```

使用上述格式，你可以轻松地为你的应用编写全局函数。在接下来的章节中，我们将指导你通过一些示例函数。所以让我们开始吧。

## 将函数注入到 Vue 实例中

在这个例子中，我们将创建一个用于计算两个数字之和的函数，例如，1 + 2 = 3。我们将通过以下步骤将这个函数注入到 Vue 实例中：

1.  创建一个`.js`文件，导入`vue`，并将函数附加到`vue.prototype`中的`/plugins/`目录中：

```js
// plugins/vue-injections/sum.js
import Vue from 'vue'
Vue.prototype.$sum = (x, y) => x + y
```

1.  将函数文件路径添加到 Nuxt 配置文件的`plugins`属性中：

```js
// nuxt.config.js
export default {
  plugins: ['~/plugins/vue-injections/sum']
}
```

1.  在任何你喜欢的地方使用这个函数，例如：

```js
// pages/vue-injections.vue
<p>{{ this.$sum(1, 2) }}</p>
<p>{{ sum }}</p>

export default {
  data () {
    return {
      sum: this.$sum(2, 3)
    }
  }
}
```

1.  在浏览器上运行页面，你应该在屏幕上得到以下输出（即使刷新页面）：

```js
3
5
```

## 将函数注入到 Nuxt 上下文中

在这个例子中，我们将创建一个用于计算一个数字的平方的函数，例如，5 * 5 = 25。我们将通过以下步骤将这个函数注入到 Nuxt 上下文中：

1.  创建一个`.js`文件，并将函数附加到`context.app`中：

```js
// plugins/ctx-injections/square.js
export default ({ app }, inject) => {
  app.$square = (x) => x * x
}
```

1.  将函数文件路径添加到 Nuxt 配置文件的`plugins`选项中：

```js
// nuxt.config.js
export default {
  plugins: ['~/plugins/ctx-injections/square']
}
```

1.  在任何你喜欢的页面上使用这个函数，只要你可以访问到上下文，例如在`asyncData`方法中：

```js
// pages/ctx-injections.vue
<p>{{ square }}</p>

export default {
  asyncData (context) {
    return {
      square: context.app.$square(5)
    }
  }
}
```

1.  在浏览器上运行页面，你应该在屏幕上得到以下输出（即使刷新页面）：

```js
25
```

请注意，`asyncData`总是在页面组件初始化之前调用，你不能在这个方法中访问`this`。因此，你不能在`asyncData`方法中使用你注入到 Vue 实例（`$root`）中的函数，比如我们在前面例子中创建的`$sum`函数（我们将在第八章中更详细地了解`asyncData`）。同样，我们也不能在 Vue 的生命周期钩子/方法（例如`mounted`、`updated`等）中调用上下文注入的函数，比如这个例子中的`$square`函数。但是，如果你想要一个可以从`this`和`context`中使用的函数，让我们看看如何通过在下一节中将这种函数注入到 Vue 实例和 Nuxt 上下文中来实现。

## 将函数注入到 Vue 实例和 Nuxt 上下文中

在这个例子中，我们将创建一个用于计算两个数字之积的函数，例如，2 * 3 = 6。我们将通过以下步骤将这个函数注入到 Vue 实例和 Nuxt 上下文中：

1.  创建一个`.js`文件，并使用`inject`函数封装您的函数：

```js
// plugins/combined-injections/multiply.js
export default ({ app }, inject) => {
  inject('multiply', (x, y) => x  y)
}
```

请注意，`$`会自动添加到您的函数前缀，因此您不必担心将其添加到您的函数中。

1.  将函数文件路径添加到 Nuxt 配置文件的`plugins`属性中：

```js
// nuxt.config.js
export default {
  plugins: ['~/plugins/combined-injections/multiply']
}
```

1.  在任何您可以访问`context`和`this`（Vue 实例）的页面上使用该函数，例如以下示例：

```js
// pages/combined-injections.vue
<p>{{ this.$multiply(4, 3) }}</p>
<p>{{ multiply }}</p>

export default {
  asyncData (context) {
    return { multiply: context.app.$multiply(2, 3) }
  }
}
```

1.  在浏览器上运行页面，您应该在屏幕上得到以下输出（即使在刷新页面时也是如此）：

```js
12
6
```

您可以在任何 Vue 生命周期钩子中使用该函数，例如以下示例：

```js
mounted () {
  console.log(this.$multiply(5, 3))
}
```

您应该在浏览器控制台上得到`15`的输出。此外，您还可以从`Vuex store`的`actions`和`mutations`对象/属性中访问该函数，我们将在第十章中介绍*添加一个 Vuex Store*。

1.  创建一个`.js`文件，并将以下函数封装在`actions`和`mutations`对象中：

```js
// store/index.js
export const state = () => ({
  xNumber: 1,
  yNumber: 3
})

export const mutations = {
  changeNumbers (state, newValue) {
    state.xNumber = this.$multiply(3, 8)
    state.yNumber = newValue
  }
}

export const actions = {
  setNumbers ({ commit }) {
    const newValue = this.$multiply(9, 6)
    commit('changeNumbers', newValue)
  }
}
```

1.  在任何您喜欢的页面上使用前面的存储`action`方法，例如以下示例：

```js
// pages/combined-injections.vue
<p>{{ $store.state }}</p>
<button class="button" v-on:click="updateStore">Update Store</button>

export default {
  methods: {
    updateStore () {
      this.$store.dispatch('setNumbers')
    }
  }
}
```

1.  在浏览器上运行页面，您应该在屏幕上得到以下输出（即使在刷新页面时也是如此）：

```js
{ "xNumber": 1, "yNumber": 3 }
```

1.  单击“更新存储”按钮，前面的数字将更改为存储默认状态如下：

```js
{ "xNumber": 24, "yNumber": 54 }
```

这很棒。通过这种方式，我们可以编写一个在客户端和服务器端都能工作的插件。但有时，我们需要能够在服务器端或客户端独占地使用的函数。为了做到这一点，我们必须指示 Nuxt 如何专门运行我们的函数。让我们在下一节中找出如何做到这一点。

## 注入仅客户端或仅服务器端的插件

在这个例子中，我们将创建一个用于除法的函数，例如，8 / 2 = 4，以及另一个用于减法的函数，例如，8 - 2 = 6。我们将将第一个函数注入到 Vue 实例中，并使其专门用于客户端使用，而将第二个函数注入到 Nuxt 上下文中，并使其专门用于服务器端使用。

1.  创建两个函数，并将它们分别附加`.client.js`和`.server.js`：

```js
// plugins/name-conventions/divide.client.js
import Vue from 'vue'
Vue.prototype.$divide = (x, y) => x / y

// plugins/name-conventions/subtract.server.js
export default ({ app }, inject) => {
  inject('subtract', (x, y) => x - y)
}
```

附加`.client.js`的函数文件将仅在客户端运行，而附加`.server.js`的函数文件将仅在服务器端运行。

1.  将函数文件路径添加到 Nuxt 配置文件的`plugins`属性中：

```js
// nuxt.config.js:
export default {
  plugins: [
    '~/plugins/name-conventions/divide.client.js',
    '~/plugins/name-conventions/subtract.server.js'
  ]
}
```

1.  在任何你喜欢的页面上使用这些插件，比如以下示例：

```js
// pages/name-conventions.vue
<p>{{ divide }}</p>
<p>{{ subtract }}</p>

export default {
  data () {
    let divide = ''
    if (process.client) {
      divide = this.$divide(8, 2)
    }
    return { divide }
  },
  asyncData (context) {
    let subtract = ''
    if (process.server) {
      subtract = context.app.$subtract(10, 4)
    }
    return { subtract }
  }
}
```

1.  在浏览器上运行页面，你应该在屏幕上得到以下输出：

```js
4
6
```

请注意，当你在浏览器上首次运行页面时，你将得到前面的结果，即使在刷新页面时也是如此。但是在第一次加载后，如果你通过`<nuxt-link>`导航到这个页面，你将在屏幕上得到以下输出：

```js
4
```

另外，请注意我们必须将`$divide`方法包裹在`process.client`的 if 条件中，因为它是一个只在客户端执行的函数。如果你移除`process.client`的 if 条件，你将在浏览器中得到一个服务器端错误：

```js
this.$divide is not a function
```

对于`$subtract`方法也是一样的：我们必须将其包裹在`process.server`的 if 条件中，因为它是一个只在服务器端执行的函数。如果你移除`process.server`的 if 条件，你将在浏览器上得到一个客户端错误：

```js
this.$subtract is not a function
```

将函数包裹在`process.server`中可能不是理想的做法

`process.client`的 if 条件每次使用时都会被阻塞。但是在仅在客户端被调用的 Vue 生命周期钩子/方法上，比如`mounted`钩子，你不需要使用`process.client`的 if 条件。因此，你可以在不使用 if 条件的情况下安全地使用你的仅客户端函数，就像以下示例中一样：

```js
mounted () {
  console.log(this.$divide(8, 2))
}
```

你将在浏览器控制台中得到`4`的输出。下表显示了八个 Vue 生命周期钩子/方法，值得知道的是在 Nuxt 应用中只有其中两个会在两端被调用：

| **服务器和客户端** | **仅客户端** |
| --- | --- |

|

+   beforeCreate ()

+   created ()

|

+   beforeMount ()

+   mounted ()

+   beforeUpdate ()

+   updated ()

+   beforeDestroy ()

+   destroyed ()

|

请注意，我们在 Vue 和 Nuxt 应用中一直在使用的`data`方法会在两端被调用，就像`asyncData`方法一样。因此，你可以在**仅客户端**列表下的钩子中使用`$divide`方法，它是专门为客户端使用而制作的，而不需要 if 条件。而对于`$subtract`方法，它是专门为仅在服务器端使用而制作的，你可以在`nuxtServerInit`动作中安全地使用它，就像以下示例中一样：

```js
export const actions = {
  nuxtServerInit ({ commit }, context) {
    console.log(context.app.$subtract(10, 4))
  }
}
```

当您的应用在服务器端运行时，即使刷新页面（任何页面），您将得到`6`的输出。值得知道的是，只能通过这些方法访问 Nuxt 上下文：`nuxtServerInit`和`asyncData`。`nuxtServerInit`操作可以作为第二个参数访问上下文，而`asyncData`方法可以作为第一个参数访问上下文。我们将在第十章中介绍`nuxtServerInit`操作，*添加一个 Vuex Store*，但是，现在在下一节中，我们将看一下在`nuxtServerInit`操作之后，但在 Vue 实例和插件之前以及在`$root`和 Nuxt 上下文注入的函数之前注入到 Nuxt 上下文中的 JavaScript 函数。这种类型的函数称为 Nuxt 模块，通过本章末尾，您将知道如何编写这些模块。让我们开始吧。

# 编写 Nuxt 模块

模块只是一个顶级的 JavaScript 函数，在 Nuxt 启动时执行。Nuxt 会按顺序调用每个模块，并在继续调用 Vue 实例、Vue 插件和要注入到`$root`和 Nuxt 上下文中的全局函数之前等待所有模块完成。因为模块在它们之前被调用（即 Vue 实例等），我们可以使用模块来覆盖模板、配置 webpack 加载器、添加 CSS 库以及执行其他应用所需的任务。此外，模块也可以打包成 npm 包并与 Nuxt 社区共享。您可以查看以下链接，了解由 Nuxt 社区制作的生产就绪模块：

[`github.com/nuxt-community/awesome-nuxt#official`](https://github.com/nuxt-community/awesome-nuxt#official)

让我们试试 Axios 模块，这是一个与 Axios 集成的模块（[`github.com/axios/axios`](https://github.com/axios/axios)）用于 Nuxt。它具有一些功能，例如自动设置客户端和服务器端的基本 URL。我们将在接下来的章节中发现它的一些特性。如果您想了解更多关于这个模块的信息，请访问[`axios.nuxtjs.org/`](https://axios.nuxtjs.org/)。现在，让我们看看如何在以下步骤中使用这个模块：

1.  使用 npm 安装它：

```js
$ npm install @nuxtjs/axios
```

1.  在 Nuxt 配置文件中进行配置：

```js
// nuxt.config.js
module.exports = {
  modules: [
    '@nuxtjs/axios'
  ]
}
```

1.  在任何地方使用，例如在页面的`asyncData`方法中：

```js
// pages/index.vue
async asyncData ({ $axios }) {
  const ip = await $axios.$get('http://icanhazip.com')
  console.log(ip)
}
```

您还可以在`mounted`方法（或`created`，`updated`等）中使用它，如下所示：

```js
// pages/index.vue
async mounted () {
  const ip = await this.$axios.$get('http://icanhazip.com')
  console.log(ip)
}
```

每次导航到`/about`页面时，您应该在浏览器控制台上看到您的 IP 地址。您应该注意到现在您可以像使用原始 Axios 一样发送 HTTP 请求，而无需在需要时导入它，因为它现在通过模块全局注入。很棒，不是吗？接下来，我们将通过从基本模块开始编写您的模块来指导您。

## 编写基本模块

正如我们已经提到的，模块是函数，它们可以选择地打包为 npm 模块。这是您创建模块所需的非常基本的结构：

```js
// modules/basic.js
export default function (moduleOptions) {
  // ....
}
```

您只需在项目根目录中创建一个`/modules/`目录，然后开始编写您的模块代码。请注意，如果您想将模块发布为 npm 包，必须包含以下行：

```js
module.exports.meta = require('./package.json')
```

如果您想创建模块并将其发布为 npm 包，请按照 Nuxt 社区的此模板：

[`github.com/nuxt-community/module-template/tree/master/template`](https://github.com/nuxt-community/module-template/tree/master/template)

无论您是为 Nuxt 社区还是仅为自己的项目创建模块，每个模块都可以访问以下内容：

+   **模块选项：**

我们可以从配置文件中向模块传递 JavaScript 对象中的一些选项，例如：

```js
// nuxt.config.js
export default {
  modules: [
    ['~/modules/basic/module', { language: 'ES' }],
  ]
}
```

然后，您可以在模块函数的第一个参数中将前述选项作为`moduleOptions`访问，如下所示：

```js
// modules/basic/module.js
export default function (moduleOptions) {
  console.log(moduleOptions)
}
```

您将获得从配置文件中传递的以下选项：

```js
{
  language: 'ES'
}
```

+   **配置选项：**

我们还可以创建一个自定义选项（例如`token`，`proxy`或`basic`），并将一些特定选项传递给模块（这个自定义选项可以在模块之间共享使用），如下例所示：

```js
// nuxt.config.js
export default {
  modules: [
    ['~/modules/basic/module'],
  ],
  basic: { // custom option
    option1: false,
    option2: true,
  }
}
```

然后，您可以使用`this.options`访问前述自定义选项，如下所示：

```js
// modules/basic/module.js
export default function (moduleOptions) {
  console.log(this.options['basic'])
}
```

您将获得从配置文件中传递的以下选项：

```js
{
  option1: false,
  option2: true
}
```

然后我们可以将`moduleOptions`和`this.options`组合如下：

```js
// modules/basic/module.js
export default function (moduleOptions) {
  const options = {
    ...this.options['basic'],
    ...moduleOptions
  }
  console.log(options)
}
```

您将获得从配置文件中传递的以下组合选项：

```js
{
  option1: false,
  option2: true
}
```

+   **Nuxt 实例：**

您可以使用`this.nuxt`来访问 Nuxt 实例。请访问以下链接以获取可用方法（例如`hook`方法，我们可以使用它在 Nuxt 启动时创建特定事件的某些任务）：

[`nuxtjs.org/api/internals-nuxt`](https://nuxtjs.org/api/internals-nuxt)

+   **`ModuleContainer`实例：**

您可以使用 `this` 来访问 `ModuleContainer` 实例。请访问以下链接以获取可用方法（例如，`addPlugin` 方法，我们在模块中经常使用它来注册插件）：

[`nuxtjs.org/api/internals-module-container`](https://nuxtjs.org/api/internals-module-container)

+   **`module.exports.meta` 代码行：**

如果您将您的模块发布为 npm 包，则此行是必需的，正如我们之前提到的。但在本书中，我们将指导您完成为您的项目创建模块的步骤。让我们通过以下步骤开始创建一个非常基本的模块：

1.  创建一个带有以下代码的 `module` 文件：

```js
// modules/basic/module.js
const { resolve } = require('path')

export default function (moduleOptions) {
  const options = {
    ...this.options['basic'],
    ...moduleOptions
  }

  // Add plugin.
  this.addPlugin({
    src: resolve(__dirname, 'plugin.js'),
    fileName: 'basic.js',
    options
  })
}
```

1.  创建一个带有以下代码的 `plugin` 文件：

```js
// modules/basic/plugin.js
var options = []

<% if (options.option1 === true) { %>
  options.push('option 1')
<% } %>

<% if (options.option2 === true) { %>
  options.push('option 2')
<% } %>

<% if (options.language === 'ES') { %>
  options.push('language ES')
<% } %>

const basic = function () {
  return options
}

export default ({ app }, inject) => {
  inject('basic', basic)
}
```

请注意，`<%= %>` 符号是 Lodash 用于在 `template` 函数中插入数据属性的插值分隔符。我们稍后将在本章再次介绍它们。有关 Lodash `template` 函数的更多信息，请访问 [`lodash.com/docs/4.17.15#template`](https://lodash.com/docs/4.17.15#template)。

1.  仅在 Nuxt 配置文件中包含模块文件路径（`/modules/basic/module.js`），并提供一些选项，如下所示使用 `basic` 自定义选项：

```js
// nuxt.config.js
export default {
  modules: [
    ['~/modules/basic/module', { language: 'ES' }],
  ],

  basic: {
    option1: false,
    option2: true,
  }
}
```

1.  您可以在任何地方使用它，例如：

```js
// pages/index.vue
mounted () {
  const basic = this.$basic()
  console.log(basic)
}
```

1.  每次访问主页时，您应该在浏览器控制台上看到以下输出：

```js
["option 2", "language ES"]
```

请注意 `module.js` 如何处理高级配置细节，例如语言和选项。它还负责注册 `plugin.js` 文件，该文件执行实际工作。正如您所看到的，该模块是围绕插件的一个包装器。我们将在接下来的章节中更详细地学习这一点。

请注意，如果您只为构建时间和开发编写模块，则应在 Nuxt 配置文件中使用 `buildModules` 选项来注册您的模块，而不是在 Node.js 运行时使用 `modules` 选项。有关此选项的更多信息，请访问 [`nuxtjs.org/guide/modules#build-only-modules`](https://nuxtjs.org/guide/modules#build-only-modules) 和 [`nuxtjs.org/api/configuration-modules`](https://nuxtjs.org/api/configuration-modules)。

# 编写异步 Nuxt 模块

如果您需要在模块中使用 `Promise` 对象，例如，使用 HTTP 客户端从远程 API 获取一些异步数据，那么 Nuxt 可以完美支持。以下是一些选项，您可以使用这些选项编写您的 async 模块。

## 使用 async/await

您可以在您的模块中使用 ES6 的 async/await 与 Axios，这是我们自第四章以来一直在使用的 HTTP 客户端，例如以下示例中：

```js
// modules/async-await/module.js
import axios from 'axios'

export default async function () {
  let { data } = await axios.get(
   'https://jsonplaceholder.typicode.com/posts')
  let routes = data.map(post => '/posts/' + post.id)
  console.log(routes)
}

// nuxt.config.js
modules: [
  ['~/modules/async-await/module']
]
```

在前面的例子中，我们使用 Axios 的`get`方法从远程 API JSONPlaceholder（[`jsonplaceholder.typicode.com/`](https://jsonplaceholder.typicode.com/)）获取所有帖子。当您第一次启动 Nuxt 应用程序时，您应该在终端上看到以下输出：

```js
[
  '/posts/1',
  '/posts/2',
  '/posts/3',
  ...
]
```

## 返回一个 Promise

您可以在您的模块中使用 promise 链并返回`Promise`对象，就像以下示例中一样：

```js
// modules/promise-sample/module.js
import axios from 'axios'

export default function () {
  return axios.get('https://jsonplaceholder.typicode.com/comments')
    .then(res => res.data.map(comment => '/comments/' + comment.id))
    .then(routes => {
      console.log(routes)
    })
}

// nuxt.config.js
modules: [
  ['~/modules/promise-sample/module']
]
```

在这个例子中，我们使用 Axios 的`get`方法从远程 API 获取所有评论。然后我们使用`then`方法来`链`Promise 并打印结果。当您第一次启动 Nuxt 应用程序时，您应该在终端上看到以下输出：

```js
[
  '/comments/1',
  '/comments/2',
  '/comments/3',
  ...
]
```

您可以在我们的 GitHub 存储库的`/chapter-6/nuxt-universal/modules/async/`中找到这两个示例。

有了这两个异步选项和您从前面部分学到的基本模块编写技能，您可以轻松开始创建您的 Nuxt 模块。我们将在下一节中通过编写模块的小片段来查看更多示例 - **片段**。

# 编写 Nuxt 模块片段

在这个主题中，我们将把我们创建的自定义模块分解成小片段。

您可以在我们的 GitHub 存储库的`/chapter-6/nuxt-universal/module-snippets/`中找到所有以下代码。

## 使用顶级选项

记住我们在*编写基本模块*部分中说过的可以传递到模块中的配置选项吗？模块选项是在 Nuxt 配置文件中注册我们的模块的顶级选项。我们甚至可以结合来自不同模块的多个选项，并且它们的选项可以共享。让我们尝试在以下步骤中一起使用`@nuxtjs/axios`和`@nuxtjs/proxy`的示例：

1.  使用 npm 一起安装这两个模块：

```js
$ npm i @nuxtjs/axios
$ npm i @nuxtjs/proxy
```

这两个模块被很好地集成在一起，以防止 CORS 问题，我们将在本书的后面看到并讨论跨域应用程序的开发。不需要手动注册`@nuxtjs/proxy`模块，但它确实需要在您的`package.json`文件的依赖项中。

1.  在 Nuxt 配置文件中注册`@nuxtjs/axios`模块并设置这两个模块的顶级选项：

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
    '/api/': { target: 'https://jsonplaceholder.typicode.com/', 
     pathRewrite: {'^/api/': ''} },
  }
}
```

`axios` 自定义选项中的 `proxy: true` 选项告诉 Nuxt 使用 `@nuxtjs/proxy` 模块作为代理。`proxy` 自定义选项中的 `/api/: {...}` 选项告诉 `@nuxtjs/axios` 模块将 [`jsonplaceholder.typicode.com/`](https://jsonplaceholder.typicode.com/) 作为 API 服务器的目标地址，而 `pathRewrite` 选项告诉 `@nuxtjs/axios` 模块在 HTTP 请求期间从地址中删除 `/api/`，因为目标 API 中没有带有 `/api` 的路由。

1.  接下来，在任何组件中无缝使用它们，就像以下示例中一样：

```js
// pages/index.vue
<template>
  <ul>
    <li v-for="user in users">
      {{ user.name }}
    </li>
  </ul>
</template>

<script>
export default {
  async asyncData({ $axios }) {
    const users = await $axios.$get('/api/users')
    return { users }
  }
}
</script>
```

现在，使用这两个模块，我们可以在请求方法（例如 `get`、`post` 和 `put`）中只写更短的 API 地址，比如 `/api/users` 而不是 `https://jsonplaceholder.typicode.com/users`。这样可以使我们的代码更整洁，因为我们不必在每次调用时都写完整的 URL。请注意，我们在 Nuxt 配置文件中配置的 `/api/` 地址将被添加到对 API 端点的所有请求中。因此，我们使用 `pathRewrite`，如我们已经解释的那样，在发送请求时删除它。

你可以在以下链接中找到这两个模块提供的更多信息和顶层选项：

+   [`axios.nuxtjs.org/options`](https://axios.nuxtjs.org/options) 用于 `@nuxtjs/axios`

+   [`github.com/nuxt-community/proxy-module`](https://github.com/nuxt-community/proxy-module) 用于 `@nuxtjs/proxy`

你可以在我们的 GitHub 仓库的 `/chapter-6/nuxt-universal/module-snippets/top-level/` 中找到我们刚创建的示例模块片段。

## 使用 addPlugin 辅助函数

还记得我们在 *编写基本模块* 部分介绍过的 `ModuleContainer` 实例和 `this.addPlugin` 辅助方法吗？在这个示例中，我们将使用这个辅助函数创建一个模块，该模块通过这个辅助函数提供了一个插件，这个插件就是 `bootstrap-vue`，它将被注册到 Vue 实例中。让我们按照以下步骤创建这个模块片段：

1.  安装 Bootstrap 和 BootstrapVue：

```js
$ npm i bootstrap-vue
$ npm i bootstrap
```

1.  创建一个插件文件来导入 `vue` 和 `bootstrap-vue`，然后使用 `use` 方法注册 `bootstrap-vue`：

```js
// modules/bootstrap/plugin.js
import Vue from 'vue'
import BootstrapVue from 'bootstrap-vue/dist/bootstrap-vue.esm'

Vue.use(BootstrapVue)
```

1.  创建一个模块文件，使用 `addPlugin` 方法添加我们刚创建的插件文件：

```js
// modules/bootstrap/module.js
import path from 'path'

export default function (moduleOptions) {
  this.addPlugin(path.resolve(__dirname, 'plugin.js'))
}
```

1.  在 Nuxt 配置文件中添加这个 `bootstrap` 模块的文件路径：

```js
// nuxt.config.js
export default {
  modules: [
    ['~/modules/bootstrap/module']
  ]
}
```

1.  在任何喜欢的组件上开始使用 `bootstrap-vue`；例如，让我们创建一个按钮来切换 Bootstrap 中的警报文本，如下所示：

```js
// pages/index.vue
<b-button @click="toggle">
  {{ show ? 'Hide' : 'Show' }} Alert
</b-button>
<b-alert v-model="show">
  Hello {{ name }}!
</b-alert>

import 'bootstrap/dist/css/bootstrap.css'
import 'bootstrap-vue/dist/bootstrap-vue.css'

export default {
  data () {
    return {
      name: 'BootstrapVue',
      show: true
    }
  }
}
```

有了这个模块片段，我们不必每次在组件上需要时导入`bootstrap-vue`，因为它已经通过前面的片段模块全局添加了。我们只需要导入它的 CSS 文件。在使用示例中，我们使用 Bootstrap 的自定义`<b-button>`组件来切换 Bootstrap 的自定义`<b-alert>`组件。然后，`<b-button>`组件将在该按钮上切换文本“隐藏”或“显示”。

有关 BootstrapVue 的更多信息，请访问[`bootstrap-vue.js.org/`](https://bootstrap-vue.js.org/)。您可以在我们的 GitHub 存储库中的`/chapter-6/nuxt-universal/module-snippets/provide-plugin/`中找到我们刚刚创建的示例模块片段。

## 使用 Lodash 模板

再次，这是我们在*编写基本模块*部分创建的自定义模块中熟悉的内容-利用 Lodash 模板通过使用 if 条件块来改变注册插件的输出。再次，Lodash 模板是一段代码，我们可以用`<%= %>`插值分隔符插入数据属性。让我们在以下步骤中尝试另一个简单的例子：

1.  创建一个插件文件来导入`axios`并添加 if 条件块，以确保为`axios`提供请求 URL，并在您的 Nuxt 应用程序以`dev`模式（`npm run dev`）运行时在终端上打印请求结果以进行调试：

```js
// modules/users/plugin.js
import axios from 'axios'

let users = []
<% if (options.url) { %>
  users = axios.get('<%= options.url %>')
<% } %>

<% if (options.debug) { %>
  // Dev only code
  users.then((response) => {
    console.log(response);
  })
  .catch((error) => {
    console.log(error);
  })
<% } %>

export default ({ app }, inject) => {
  inject('getUsers', async () => {
    return users
  })
}
```

1.  创建一个`module`文件，使用`addPlugin`方法添加我们刚刚创建的插件文件，使用`options`选项传递请求 URL 和`this.options.dev`的布尔值给这个插件：

```js
// modules/users/module.js
import path from 'path'

export default function (moduleOptions) {
  this.addPlugin({
    src: path.resolve(__dirname, 'plugin.js'),
    options: {
      url: 'https://jsonplaceholder.typicode.com/users',
      debug: this.options.dev
    }
  })
}
```

1.  将此模块的文件路径添加到 Nuxt 配置文件中：

```js
// nuxt.config.js
export default {
  modules: [
      ['~/modules/users/module']
    ]
}
```

1.  在任何您喜欢的组件上开始使用`$getUsers`方法，就像以下示例中一样：

```js
// pages/index.vue
<li v-for="user in users">
  {{ user.name }}
</li>

export default {
  async asyncData({ app }) {
    const { data: users } = await app.$getUsers()
    return { users }
  }
}
```

在上面的示例中，Nuxt 将在将插件复制到项目时将`options.url`替换为`https://jsonplaceholder.typicode.com/users`。`options.debug`的 if 条件块将在生产构建时从插件代码中剥离，因此您在生产模式（`npm run build`和`npm run start`）中将看不到终端上的`console.log`输出。

您可以在我们的 GitHub 存储库中的`/chapter-6/nuxt-universal/module-snippets/template-plugin/`中找到我们刚刚创建的示例模块片段。

## 添加 CSS 库

在*使用 addPlugin 助手*部分的模块片段示例中，我们创建了一个模块，允许我们在应用程序中全局使用`bootstrap-vue`插件，而无需使用`import`语句来引入此插件，如下例所示：

```js
// pages/index.vue
<b-button size="sm" @click="toggle">
  {{ show ? 'Hide' : 'Show' }} Alert
</b-button>

import 'bootstrap/dist/css/bootstrap.css'
import 'bootstrap-vue/dist/bootstrap-vue.css'
export default {
  //...
}
```

这看起来非常整洁，因为我们不必每次都导入`bootstrap-vue`，而只需导入 CSS 样式即可。但是，通过模块，我们仍然可以节省几行代码，将样式添加到应用程序的全局 CSS 堆栈中。让我们创建一个新的示例，并看看我们如何在以下步骤中执行该操作：

1.  创建一个模块文件，其中包含一个名为`options`的`const`变量，用于将模块和顶层选项传递给插件文件，以及一个 if 条件块，用于确定是否使用原始 JavaScript 的`push`方法将 CSS 文件推送到 Nuxt 配置文件中的`css`选项中：

```js
// modules/bootstrap/module.js
import path from 'path'
export default function (moduleOptions) {
  const options = Object.assign({}, this.options.bootstrap, 
    moduleOptions)

  if (options.styles !== false) {
    this.options.css.push('bootstrap/dist/css/bootstrap.css')
    this.options.css.push('bootstrap-vue/dist/bootstrap-vue.css')
  }

  this.addPlugin({
    src: path.resolve(__dirname, 'plugin.js'),
    options
  })
}
```

1.  创建一个插件文件，其中注册了`bootstrap-vue`插件，并使用 if 条件 Lodash-template 块打印从模块文件处理的选项：

```js
// modules/bootstrap/plugin.js
import Vue from 'vue'
import BootstrapVue from 'bootstrap-vue/dist/bootstrap-vue.esm'

Vue.use(BootstrapVue)

<% if (options.debug) { %>
  <% console.log (options) %>
<% } %>
```

1.  将模块文件的文件路径添加到 Nuxt 配置文件中，模块选项指定是否在模块文件中禁用 CSS 文件。还要添加顶层选项`bootstrap`，以将布尔值传递给`debug`选项：

```js
// nuxt.config.js
export default {
  modules: [
    ['~/modules/bootstrap/module', { styles: true }]
  ],

  bootstrap: {
    debug: process.env.NODE_ENV === 'development' ? true : false
  }
}
```

1.  从我们的组件中删除 CSS 文件：

```js
// pages/index.vue
<script>
- import 'bootstrap/dist/css/bootstrap.css'
- import 'bootstrap-vue/dist/bootstrap-vue.css'
export default {
  //...
}
</script>
```

因此，最终，我们可以在组件中使用`bootstrap-vue`插件及其 CSS 文件，而无需全部导入它们。以下是将 Font Awesome 的`css`选项快速推送到 Nuxt 配置文件的模块片段的另一个示例：

```js
// modules/bootstrap/module.js
export default function (moduleOptions) {
  if (moduleOptions.fontAwesome !== false) {
    this.options.css.push('font-awesome/css/font-awesome.css')
  }
}
```

如果您想了解有关 Font Awesome 的更多信息，请访问[`fontawesome.com/`](https://fontawesome.com/)。

您可以在我们的 GitHub 存储库的`/chapter-6/nuxt-universal/module-snippets/css-lib/`中找到我们刚刚创建的示例模块片段。

## 注册自定义 webpack 加载器

当我们想要在 Nuxt 中扩展 webpack 配置时，通常会在`nuxt.config.js`中使用`build.extend`来完成。但是，我们也可以通过使用`this.extendBuild`和以下模块/加载器模板来通过模块执行相同的操作：

```js
export default function (moduleOptions) {
  this.extendBuild((config, { isClient, isServer }) => {
    //...
  })
}
```

例如，假设我们想要使用`svg-transform-loader`扩展我们的 webpack 配置，这是一个用于添加或修改 SVG 图像中标记和属性的 webpack 加载器。该加载器的主要目的是允许我们在 SVG 图像上使用`fill`、`stroke`和其他操作。我们还可以在 CSS、Sass、Less、Stylus 或 PostCSS 中使用它；例如，如果您想要用白色填充 SVG 图像，可以使用`fill`将`fff`（CSS 颜色白色代码）添加到图像中，如下所示：

```js
.img {
  background-image: url('./img.svg?fill=fff');
}
```

如果您想要在 Sass 中使用变量`stroke` SVG 图像，可以这样做：

```js
$stroke-color: fff;

.img {
  background-image: url('./img.svg?stroke={$stroke-color}');
}
```

让我们创建一个示例模块，将此加载器注册到 Nuxt webpack 默认配置中，以便我们可以通过以下步骤在 Nuxt 应用程序中操作 SVG 图像：

1.  使用 npm 安装加载器：

```js
$ npm i svg-transform-loader
```

1.  使用我们之前提供的模块/加载器模板创建一个模块文件，如下所示：

```js
// modules/svg-transform-loader/module.js
export default function (moduleOptions) {
  this.extendBuild((config, { isClient, isServer }) => {
    //...
  })
}
```

1.  在`this.extendBuild`的回调函数中，添加以下行以查找文件加载器并从其现有规则测试中删除`svg`：

```js
const rule = config.module.rules.find(
  r => r.test.toString() === '/\\.(png|jpe?g|gif|svg|webp)$/i'
)
rule.test = /\.(png|jpe?g|gif|webp)$/i
```

1.  在前面的代码块之后添加以下代码块，将`svg-transform-loader`加载器推入默认 webpack 配置的模块规则：

```js
config.module.rules.push({
  test: /\.svg(\?.)?$/, // match img.svg and img.svg?param=value
  use: [
    'url-loader',
    'svg-transform-loader'
  ]
})
```

模块现在已经完成，我们可以继续*步骤 5*。

1.  将此模块的文件路径添加到 Nuxt 配置文件中：

```js
// nuxt.config.js
export default {
  modules: [
    ['~/modules/svg-transform-loader/module']
  ]
}
```

1.  开始转换我们组件中的任何 SVG 图像，例如以下内容：

```js
// pages/index.vue
<template>
  <div>
    <div class="background"></div>
    <img src="~/assets/bug.svg?stroke=red&stroke-
     width=4&fill=blue">
  </div>
</template>

<style lang="less">
.background {
   height: 100px;
   width: 100px;
   border: 4px solid red;
   background-image: url('~assets/bug.svg?stroke=red&stroke-
    width=2');
}
</style>
```

您可以在[`www.npmjs.com/package/svg-transform-loader`](https://www.npmjs.com/package/svg-transform-loader)找到有关`svg-transform-loader`的更多信息。如果您想了解有关规则测试的更多信息，并查看 Nuxt 默认 webpack 配置的完整内容，请访问以下链接：

+   [`webpack.js.org/configuration/module/ruletest`](https://webpack.js.org/configuration/module/#ruletest) webpack 规则测试

+   [`github.com/nuxt/nuxt.js/blob/dev/packages/webpack/src/config/base.js`](https://github.com/nuxt/nuxt.js/blob/dev/packages/webpack/src/config/base.js) Nuxt 默认 webpack 配置

您可以在我们的 GitHub 存储库中的`/chapter-6/nuxt-universal/module-snippets/webpack-loader/`中找到我们刚刚创建的示例模块片段。

## 注册自定义 webpack 插件

Nuxt 模块不仅允许我们注册 webpack 加载器，还允许我们使用以下模块/插件架构注册 webpack 插件：`this.options.build.plugins.push`。

```js
export default function (moduleOptions) {
  this.options.build.plugins.push({
    apply(compiler) {
      compiler.hooks.<hookType>.<tap>('<PluginName>', (param) => {
        //...
      })
    }
  })
}
```

`<tap>`取决于挂钩类型；它可以是`tapAsync`，`tapPromise`或`tap`。让我们按照以下步骤通过 Nuxt 模块创建一个非常简单的“Hello World”webpack 插件：

1.  使用我们提供的模块/插件架构创建一个模块文件，以打印“Hello World!”，如下所示：

```js
// modules/hello-world/module.js
export default function (moduleOptions) {
  this.options.build.plugins.push({
    apply(compiler) {
      compiler.hooks.done.tap('HelloWordPlugin', (stats) => {
        console.log('Hello World!')
      })
    }
  })
}

```

请注意，在`done`挂钩被触发时，`stats`（统计信息）被传递为参数。

1.  将此模块的文件路径添加到 Nuxt 配置文件中：

```js
// nuxt.config.js
export default {
 modules: [
 ['~/modules/hello-world/module']
}
```

1.  使用`$ npm run dev`运行你的 Nuxt 应用程序，你应该在终端上看到“Hello World!”。

请注意，`apply`方法，`compiler`，`hooks`和`tap`都是构建 webpack 插件的关键部分。

如果你是新手 webpack 插件开发者，并想了解更多关于如何为 webpack 开发插件，请访问[`webpack.js.org/contribute/writing-a-plugin/`](https://webpack.js.org/contribute/writing-a-plugin/)。

你可以在我们的 GitHub 存储库中的`/chapter-6/nuxt-universal/module-snippets/webpack-plugin/`中找到我们刚刚创建的示例模块片段。

## 在特定挂钩上创建任务

如果你需要在 Nuxt 启动时对特定生命周期事件（例如，当所有模块加载完成时）执行某些任务，你可以创建一个模块，并使用`hook`方法来监听该事件，然后执行任务。请考虑以下示例：

+   如果你想在所有模块加载完成后做一些事情，请尝试以下操作：

```js
export default function (moduleOptions) {
  this.nuxt.hook('modules:done', moduleContainer => {
    //...
  })
}
```

+   如果你想在渲染器创建后做一些事情，请尝试以下操作：

```js
export default function (moduleOptions) {
  this.nuxt.hook('render:before', renderer => {
    //...
  })
}
```

+   如果你想在编译器（webpack 是默认值）启动之前做一些事情，请尝试以下操作：

```js
export default function (moduleOptions) {
  this.nuxt.hook('build:compile', async ({ name, compiler }) => {
    //...
  })
}
```

+   如果你想在 Nuxt 生成页面之前做一些事情，请尝试以下操作：

```js
export default function (moduleOptions) {
  this.nuxt.hook('generate:before', async generator => {
    //...
  })
}
```

+   如果你想在 Nuxt 准备就绪时做一些事情，请尝试以下操作：

```js
export default function (moduleOptions) {
  this.nuxt.hook('ready', async nuxt => {
    //...
  })
}
```

让我们按照以下步骤创建一个简单的模块来监听`modules:done`挂钩/事件：

1.  创建一个模块文件，在所有模块加载完成时打印`'All modules are loaded'`：

```js
// modules/tasks/module.js
export default function (moduleOptions) {
  this.nuxt.hook('modules:done', moduleContainer => {
    console.log('All modules are loaded')
  })
}
```

1.  创建几个模块来打印`'Module 1'`，`'Module 2'`，`'Module 3'`等，如下所示：

```js
// modules/module1.js
export default function (moduleOptions) {
  console.log('Module 1')
}
```

1.  将挂钩模块的文件路径和其他模块添加到 Nuxt 配置文件中：

```js
// nuxt.config.js
export default {
  modules: [
    ['~/modules/tasks/module'],
    ['~/modules/module3'],
    ['~/modules/module1'],
    ['~/modules/module2']
  ]
}
```

1.  使用`$ npm run dev`运行你的 Nuxt 应用程序，你应该在终端上看到以下输出：

```js
Module 3
Module 1
Module 2
All modules are loaded
```

你可以看到挂钩模块总是最后打印，而其余的根据它们在`modules`选项中的顺序打印。

挂钩模块可以是异步的，无论你是使用`async/await`函数还是返回`Promise`。

有关上述钩子和 Nuxt 生命周期事件中的其他钩子的更多信息，请访问以下链接：

+   [模块容器内部钩子](https://nuxtjs.org/api/internals-module-container#hooks) 用于 Nuxt 的模块生命周期事件（`ModuleContainer`类）

+   [构建器内部钩子](https://nuxtjs.org/api/internals-builder#hooks) 用于 Nuxt 的构建生命周期事件（`Builder`类）

+   [生成器内部钩子](https://nuxtjs.org/api/internals-generator#hooks) 用于 Nuxt 的生成生命周期事件（`Generator`类）

+   [渲染器内部钩子](https://nuxtjs.org/api/internals-renderer#hooks) 用于 Nuxt 的渲染生命周期事件（`Renderer`类）

+   [Nuxt 内部钩子](https://nuxtjs.org/api/internals-nuxt#hooks) 用于 Nuxt 本身的生命周期事件（`Nuxt`类）

您可以在我们的 GitHub 存储库的`/chapter-6/nuxt-universal/module-snippets/hooks/`中找到我们刚刚创建的示例模块片段。

# 总结

在本章中，我们已成功涵盖了 Nuxt 中的插件和模块。您已经了解到它们在技术上是您可以为项目创建的 JavaScript 函数，或者从外部来源导入它们。此外，您已经学会了通过将它们注入到 Vue 实例或 Nuxt 上下文中（或两者都有）来为您的 Nuxt 应用创建全局函数，以及创建仅客户端和仅服务器端的函数。最后，您已经学会了通过使用`addPlugin`助手添加 JavaScript 库的模块片段，全局添加 CSS 库，使用 Lodash 模板有条件地更改已注册插件的输出，向 Nuxt 默认 webpack 配置添加 webpack 加载器和插件，以及使用 Nuxt 生命周期事件钩子创建任务，例如`modules:done`。

在接下来的章节中，我们将探索 Vue 表单并将其添加到 Nuxt 应用程序中。您将了解`v-model`在 HTML 元素（如`text`、`textarea`、`checkbox`、`radio`和`select`）中的工作原理。您将学会如何在 Vue 应用程序中验证这些元素，绑定默认和动态数据，并使用`.lazy`和`.trim`等修饰符来修改或强制输入值。您还将学会使用 Vue 插件`vee-validate`对它们进行验证，然后将其应用到 Nuxt 应用程序中。我们将引导您顺利地完成所有这些领域。敬请关注。
