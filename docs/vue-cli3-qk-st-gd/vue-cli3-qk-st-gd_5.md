# 第五章：Vue CLI 3 和路由

在上一章中，我们看到了如何在 Vue 中使用一些测试套件，比如 Jest 和 Cypress。在本章中，我们将看看如何使用 vue-router，并将其与 Vue CLI 3 一起使用。我们将看一些实际任务，比如懒加载组件。我们将看看使用 Vue add 命令添加 vue-router 的原因以及如何减轻其影响的步骤。如果您想构建更大更复杂的应用程序，了解 Vue 中的路由是有益的。本章涵盖的主题如下：

+   使用 vue-router 和 vuex 添加一个新的 Vue 项目

+   通过 VS Code 的命令行配置预设选项

+   理解 vue-router 路由

+   使用命名路由

+   添加动态路由

+   从 Vue 实例的方法选项导航到一个路由

+   使用嵌套（子）路由

+   懒加载路由

我们将从添加一个新项目开始。

# 使用 vue-router 和 vuex 添加一个新的 Vue 项目

让我们从在文件系统中创建一个新文件夹开始。让我们把这个文件夹叫做`vueclichapter5`。

1.  从 Windows 资源管理器中打开`vueclichapter5`文件夹，在`vueclichapter5`文件夹内的空白处右键单击，然后单击“Git Bash here”命令。

1.  一旦 Git Bash 打开，输入`code .`并按*Enter*键。这将打开一个全新的 VS Code 实例，除了欢迎标签之外，没有其他文件或选项卡。

1.  接下来，我们将直接从 VS Code 的集成终端中添加一个新的 Vue 项目。要访问这个终端，点击 VS Code 窗口（使其获得焦点），然后按以下快捷键：*Ctrl* + *`*。

我们之前已经提到了***Ctrl*反引号快捷键**。作为提醒，*`*字符可以在键盘上按下*Tab*键上方的键时找到。

使用*Ctrl* + *`*键盘快捷键将在 VS Code 中打开终端。

1.  接下来，我们将运行`vue create`命令，后面跟着一个点，如下所示：

```js
vue create .
```

这样做将在现有文件夹中生成一个 Vue 项目，也就是说，它不会为我们的新 Vue 项目创建一个子文件夹，如下所示：

![](img/a8a439f6-4869-42f5-96f3-0939caa5afee.png)

图 5.1：从 VS Code 的终端在当前目录中生成项目

1.  按下*Y*键确认我们将在当前目录中生成我们的项目。

# 通过 VS Code 的命令行配置预设选项

接下来，我们将通过按一次向下箭头键，然后按*Enter*键来选择手动选择功能选项，如下面的屏幕截图所示：

![](img/2bfff656-09c8-4bd2-9eda-27f1c7ade9ff.png)

图 5.2：从 VS Code 的终端在当前目录生成一个项目

# 添加 vue-router 和 vuex

接下来，我们将使用向下箭头键和空格键来选择 Router 和 Vuex 作为我们项目中的附加功能，如下截图所示：

![](img/7be8a39d-e27e-4633-9983-18e2ca398eb6.png)

图 5.3：向我们的项目添加 vue-router 和 vuex 插件

一个提示会询问我们是否要为路由使用历史模式。

我们现在不想使用它，所以我们只需输入`n`并按*Enter*。

在本章的后面，我们将讨论历史模式的作用和工作原理。

# 添加 ESLint 和 Prettier

另一个提示询问我们关于我们的代码检查器或格式化程序的偏好。我们将使用 ESLint 和 Prettier，如下所示：

![](img/29763fc3-ed26-4ecf-893b-d55e05221821.png)

图 5.4：选择 ESLint 和 Prettier 作为我们的代码检查器/格式化程序配置

# 完成配置

最后，我们将接受默认的保存时 Lint 功能，并选择将 Babel、PostCSS、ESLint 等配置放在专门的配置文件中，如下所示：

![](img/93cc85fa-70b7-44ca-916c-597ff4a53ac6.png)

图 5.5：选择将配置保存在专门的配置文件中

最后，Vue CLI 会询问我们是否要将此设置为将来项目的预设。目前我们会选择不保存。

# 为我们的新项目安装所有插件

最后，Vue CLI 会安装所有插件，如下所示：

![](img/24449c9c-85df-4486-a99b-3c20096f7935.png)

图 5.6：Vue CLI 正在安装我们项目的插件

安装完成后，我们将拥有一个已安装 vue-router 和 vuex、ESLint 和 Prettier 设置好并准备使用的 Vue 项目。

# 通过 Vue CLI UI 为我们的项目提供服务

项目安装完成后，我们可以使用`npm run serve`命令来运行它。但是，我们将使用 Vue CLI UI 来运行它，所以让我们按照以下步骤使用`vue ui`：

![](img/01b17519-548d-468e-a5c2-ca0da636f35d.png)

图 5.7：通过 vue UI 命令从 VS Code 命令行中为我们的项目提供服务

正如预期的那样，我们的 Vue CLI UI 将自动在浏览器窗口中打开，网址为`http://localhost:8000/dashboard`。此时，旧项目可能会加载在仪表板中，所以我们需要点击主页图标并导入我们的新`vueclichapter5`项目。

请注意，我们本来可以从一开始就使用 Vue CLI UI 安装新应用，但有趣的是，您可以在命令行和 UI 之间切换而不会出现问题。

项目加载完成后，我们可以点击插件链接以查看已安装的插件。请注意，缺少`Add vue-router`和`Add vuex`按钮。它们不存在，因为我们已经安装了它们。

# 从 UI 中运行 serve 任务

最后，我们将点击任务图标以打开可用任务列表，然后点击 serve 任务以编译和提供我们的项目。就像以前一样，运行任务面板将出现在仪表板的右侧，如下所示：

![](img/48f3aaff-94ae-4b60-9406-436adceb33a0.png)

图 5.8：运行 npm run serve 脚本的任务

在上面的截图中，我们可以看到 serve 任务屏幕的放大部分。点击运行任务按钮将开始构建应用程序，并且我们将在 Windows 开始栏上收到通知，我们的应用程序已成功构建。查看可用的选项卡，我们可以看到当前视图显示了我们 serve 任务的仪表板。点击当前活动的 serve 任务仪表板按钮左侧的输出按钮将显示日志信息，如下所示：

```js
  App running at:
  - Local: http://localhost:8082/
  - Network: http://192.168.1.70:8082/

  Note that the development build is not optimized.
  To create a production build, run npm run build.
```

当然，`vue-cli-service`将为我们的 Vue 应用提供服务的特定端口将取决于其他已经在使用的端口。现在，要在浏览器中打开我们正在运行的 Vue 应用，只需点击输出日志中列出的`Local`或`Network`URL 之一即可。

# 在 vue-router 中使用路由

在接下来的部分中，我们将研究 vue-router 中的路由工作原理，以及如何使用它们添加页面和路由。我们将从检查现有路由开始，然后添加额外的路由和它们对应的组件。

# 检查两个默认路由

这将打开一个带有 Vue 标志的熟悉的起始项目，稍作添加：顶部有一个链接指向关于页面，如下截图所示：

![](img/d35aaef5-d3a5-41e4-9077-a0c1385cb1fe.png)

图 5.9：安装了 vue-router 的项目的默认导航

如果您点击关于链接，您将看到另一个页面，其中有一个`h1`标签，里面是以下文本：

```js
This is an about page
```

请注意，关于页面的路由在页面名称前面有一个井号。在我们的示例应用程序中，它看起来像这样：`http://localhost:8082/#/about.`我们如何去掉井号？换句话说，我们如何将地址设置为`http://localhost:8082/about`？

答案很简单，我们只需要设置我们的服务器始终返回`index.html`页面，然后我们将添加另一个设置，即 vue-router 的`mode`设置，如下所示：`mode: 'history'`

您需要在`router.js`中的`export default new Router({`行下面添加上述代码。这样做将去掉井号。

接下来，我们将检查预安装了 vue-router 和 vuex 的项目的内容。

# 检查项目文件

返回 VS Code 并查看项目结构。您会看到与以前不同的一些差异。以下是我们`src`文件夹的内容：

![](img/9a64b06c-5bcf-4935-8626-096f4a5c7c4b.png)

图 5.10：带有预安装的 vue-router 和 vuex 的 src 文件夹的内容

在`src`文件夹中，我们可以看到一个以前没有见过的文件夹：`views`文件夹，其中包含两个视图文件：`Home.vue`和`About.vue`。在我们项目的根目录中，我们还可以看到一些额外的文件：`router.js`、`store.js`、`.browserslistrc`、`eslintrc.js`、`postcss.config.js`和`README.md`。

`router.js`文件是 vue-router 用来设置应用程序中路径的文件。`routes`数组是一组对象：一个对象对应一个路由。由于默认安装中有两个路由，所以`routes`数组中有两个对象。`store.js`文件是 vuex 用来跟踪我们应用程序的状态、mutations 和 actions 的文件；这些都被称为**vuex store**。`README.md`文件列出了与我们的应用程序一起使用的常见 npm 命令，我们项目根目录中的其他文件都是配置文件；在我们通过 Vue CLI 3 的旅程中的这一点上，这应该是可以预期的。

# main.js 的内容

就像我们以前看到的那样，`src`文件夹中的`main.js`文件导入了所有以下依赖项：

+   来自`node_modules`的 Vue 库

+   根组件`App.vue`

+   设置 vue-router 路由的路由器文件

+   设置 vuex store 的 store 文件

# App.vue 文件和 router-link 导航

通过检查根组件`App.vue`的内容，我们可以看到该文件与以前不同：没有`script`部分！

此外，`App.vue`内的模板标签包含所谓的*导航组件*。每个导航项都包含在`router-link`标签内。

为什么不只使用锚标签而不是`router-link`标签？因为锚标签会继续发送服务器请求。使用`router-link`标签可以避免这种行为。

如果我们检查`dist`文件夹中编译的 HTML 代码，我们将看到捆绑的 HTML 确实最终成为一个锚标签，如下所示在编译的生产代码中所见：

```js
<div id="nav">
    <a href="#/" class="router-link-active">Home</a> |
    <a href="#/about" class="router-link-exact-active router-link-active">About</a>
</div>
```

回到`view`文件夹内的`Home.vue`和`About.vue`文件，我们还可以看到`router-view`标签（在具有`id`为`nav`的`div`下方）。这实际上是渲染`Home view`或`About view`的地方。将要渲染的组件将由与`to`属性映射的内容确定。

# 渲染 Vue 实例并将其挂载到`#app`

最后，调用一个 Vue 的新实例，并传递一个选项对象作为参数。该对象接收以下键值对：`router: router`、`store: store`和`render: h => h(App)`。

用 ES5 代码编写，该代码看起来如下：

```js
new Vue({
    router: router,
    store: store,
    render: function render(h) {
        return h(App);
    }
}).$mount("#app");
```

我们的`main.js`文件幸运地采用了更现代的语法，因此当所有这些东西放在一起时，它看起来如下：

```js
import Vue from "vue";
import App from "./App.vue";
import router from "./router";
import store from "./store";

Vue.config.productionTip = false;

new Vue({
  router,
  store,
  render: h => h(App)
}).$mount("#app");
```

渲染方法将获取我们应用程序的所有不同部分，将它们组合在一起，并准备好挂载。最后，我们的 Vue 应用程序将被挂载到`./public/index.html`中具有`id`为 app 的`div`内。

您可能会注意到我们当前应用程序的目录中没有`dist`文件夹。正如我们在第二章*中学到的，Vue CLI 3 中的 Webpack*，`dist`文件夹是 webpack 构建我们网站的产物。因此，让我们接下来在 Vue CLI 3 UI 中运行构建任务，以查看`dist`文件夹被创建和提供。

# 从 UI 中运行构建任务

要从 Vue CLI 3 UI 中运行构建任务，我们只需要转到`http://localhost:8000/tasks/`，然后点击`构建任务`，然后点击`运行任务`按钮。

我们运行的构建任务的输出选项卡将记录以下信息：

```js
  File Size Gzipped

  dist\js\chunk-vendors.1030118d.js 116.48 KiB 40.51 KiB
  dist\js\app.51b1d496.js 5.97 KiB 2.23 KiB
  dist\js\about.d288b4f1.js 0.44 KiB 0.31 KiB
  dist\css\app.08e7a232.css 0.42 KiB 0.26 KiB

  Images and other types of assets omitted.

 DONE Build complete. The dist directory is ready to be deployed.
 INFO Check out deployment instructions at https://cli.vuejs.org/guide/deployment.html
```

`./dist/js/`文件夹中的这些不同的 JavaScript 文件是什么？它们是 webpack 将我们的 Vue 应用程序的单文件组件、路由和存储打包成部署准备的捆绑包的结果。这些捆绑包现在已添加到编译和缩小的`index.html`页面中，位于我们的`dist`文件夹内，因此这就是它们最终出现在我们的生产就绪网页上的方式。

最后，让我们看看我们更新后的应用程序。为此，我们将单击 serve 任务，并通过单击停止任务按钮来停止任务。

# 从 UI 中以生产模式提供应用程序

要以生产模式提供应用程序，我们需要单击 serve 任务选定的 Run 任务面板内的参数按钮。

一旦单击参数按钮，我们将看到以下对话框：

![](img/03355705-9e7e-4f7f-b307-7b7ac114a4a5.png)

图 5.11：在 Vue CLI 3 UI 中 serve 任务的参数对话框中指定 env 模式

在指定 env 模式设置中，单击下拉菜单，然后选择生产模式。保存更改，然后您将再次看到 serve 任务仪表板。现在，单击运行任务按钮。最后，为了确保一切仍然正常工作，请将浏览器指向 Vue 应用程序正在提供的任何端口。在本例中，正确的地址是`http://localhost:8082/`。接下来，单击关于链接。

回到 VS Code，将`About.vue`页面的`h1`标签更改为以下内容：

```js
<h1>This is an about page. HMR rocks!</h1>
```

保存文件并查看您的关于页面是否获得了 HMR 更新。

# 一些处理路由的基础知识

尽管本书是关于 Vue CLI 3 的，但我们将利用这个机会快速列出一些 vue-router 的特性。这绝不是一个详尽的列表：它只是对您需要了解的某些特性的快速概述：

+   使用命名路由

+   添加动态路由

+   从 Vue 实例中的方法选项导航到路由

+   使用嵌套（子）路由

+   延迟加载路由

我们将从理解`router-view`标签需要嵌套开始。

# router-view 标签需要嵌套

在我们开始之前，让我们看一下`App.vue`模板标签，如下所示：

```js
<template>
  <div id="app">
    <div id="nav">
      <router-link to="/">Home</router-link> |
      <router-link :to="{ name: 'about' }">About</router-link> | 
      <router-link :to="{ name: 'cars' }">Cars</router-link>
    </div>
    <router-view />
  </div>
</template>
```

请注意，`router-view`元素（从底部数第三行）被包裹在父`div`标签内，然后再包裹在`template`标签内。这很重要，因为您不能将`router-view`标签直接放在`template`标签的子级。换句话说，以下是不可能的：

```js
<template>
    <router-view />
</template>
```

现在我们知道这种方法行不通，我们可以继续讨论命名路由。

# 使用命名路由

要使用命名路由，只需将对象传递给`router-link`标签中的`to`属性，如下所示：

```js
<router-link :to="{ name: 'about' }">About</router-link>
```

注意`to`属性前面的`:`。每当我们想要添加命名路由时，都会添加这个`:`。

在传递给`to`属性的对象中使用`name`键，vue-router 将检查`router.js`中的`routes`数组。它将查找具有指定值的 name 键的对象，如果找到，它将重新路由到特定的视图组件。

# 通过触发 Vue 实例的方法导航到路由

Vue 中的每个组件都是一个独立的 Vue 实例。我们将在`HelloWorld.vue`组件中工作。要从`HelloWorld.vue`的`methods`选项中导航到路由，可以在组件的`scripts`部分中使用以下代码：

```js
methods: {
 goToAboutPage(){ 
 this.$router.push({path: '/About'}) 
 },
}
```

为了使其工作，我们需要添加一个`v-*`指令来确定何时触发具有上述代码的方法。因此，在`HelloWorld.vue`中，仍然在`template`标签中更新为以下内容：

```js
<template>
 <!-- code skipped for brevity -->
 <p v-on:mouseover="goToAboutPage" 
    style="color: red; font-size: 50px; background: gray; max-width: 500px; margin: 0 auto">
    Hover to see the About Page
 </p>
 <!-- code skipped for brevity -->
</template>
```

显然，上述`template`标签是不完整的。我们关注重要部分：文本颜色为红色，字体大小为`50`像素的`p`标签。你不能在屏幕上错过它！你可以在`vuecli3chapter5/HelloWorld.vue`中的第 4 行找到这行代码。

当你悬停在上面的段落时，Vue 会立即带你到关于页面。

现在让我们将主页上的相同功能添加到关于页面。因此，一旦你在关于页面上，你可以悬停在显眼的悬停上看到主页链接，它会带你回到主页。

为了使事情不那么跳跃，我们还可以使用老式的纯浏览器外观函数：`setTimeout`。以下是`About.vue`文件的更新代码：

```js
<template>
  <div class="about">
    <h1>This is an about page. HMR rocks!</h1>
    <p v-on:mouseover="goToHomePageSlowly" class="go">Hover to see the Home Page</p>
  </div>
</template>
<script>
export default {
  name: "About",
  props: {
    msg: String
  },
  methods: {
      goToHomePage(){ 
        this.$router.push({path: '/'}) 
      },
      goToHomePageSlowly(){
        setTimeout(() => this.goToHomePage(), 1000);
      }
  }
};
</script>
<style>
.go {
  color: purple; 
  font-size: 50px; 
  cursor: pointer;
  max-width: 500px;
  margin: 0 auto;
  background: #fabdab;
}
</style>
```

在上述代码中我们在`methods`选项中简单地添加了另一个方法。我们给这个新方法命名为`goToHomePageSlowly`。然后，在我们的 about 组件的模板标签中调用这个方法。

`goToHomePagesSlowly`使用`setTimeout`外观函数来实现浏览器的`setTimeout`功能。`setTimeout`函数以`1000`毫秒的延迟调用我们的`goToHomePage`函数，一旦运行，它将使用`$router`返回到主页。

接下来，我们将讨论嵌套路由。

# 使用嵌套路由

在开始使用嵌套路由之前，我们将在`src/views`文件夹中添加 Cars 组件，如下所示：

```js
<template>
  <div class="home">
    <HelloCars msg="Welcome to Your Cars" />
    <router-view></router-view>
  </div>
</template>

<script>
// @ is an alias to /src
import HelloCars from "@/components/HelloCars.vue";

export default {
  name: "cars",
  components: {
    HelloCars
  }
};
</script>
```

要使用嵌套路由，您需要在`router.js`中的`path`对象中添加一个`children`数组，如下例所示：

```js
routes: [
    {...},
    {...},
    {
      path: "/cars",
      name: "cars",
      component: Cars, 
      children: [
          { path: '/cars', component: CarsHome },
          { path: '/cars/cars-new', component: CarsNew },
          { path: '/cars/cars-used', component: CarsOld }
      ]
    }
]
```

每个子组件也需要路由。由于它们是嵌套路由，它们需要从各自的父组件中调用。在我们的情况下，这是`HelloCars`组件。我们将使用以下代码将`HelloCars.vue`组件添加到`components`文件夹中：

```js
<template>
 <div class="hello">
 <h1>{{ msg }}</h1>
 </div>
</template>

<script>
export default {
 name: "HelloCars",
 props: {
 msg: String
 }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
* {
 margin: 0 auto;
}
</style>
```

现在我们只需要添加额外的子组件，我们的父组件`HelloCars.vue`将调用。

从`/cars`路由可用的默认子组件是`CarsHome.vue`组件，如下所示：

```js
<template>
  <div class="home">
    <div>
        This is Cars home
        <ul>
            <li><router-link to="/cars/cars-new">See new cars</router-link></li>
            <li><router-link to="/cars/cars-used">See old cars</router-link></li>
        </ul>
    </div>
  </div>
</template>

<style>
ul li { list-style-type: none }
</style>
```

如前所述，`HelloCars.vue`组件的一个子组件是`CarsNew.vue`组件，如下所示：

```js
<template>
  <div>
    <div>
        This is Cars New
        <ul>
            <li><router-link to="/cars">Back to cars home</router-link></li>
        </ul> 
    </div>
  </div>
</template>
```

`HelloCars.vue`组件的另一个子组件是`CarsOld.vue`，如下所示：

```js
<template>
  <div>
    <div>
        This is Cars Old
        <ul>
            <li><router-link to="/cars">Back to cars home</router-link></li>
        </ul> 
    </div>
  </div>
</template>
```

现在我们了解了嵌套路由的工作原理，我们将转移焦点，简要讨论懒加载路由。

# 懒加载路由

有时，webpack 生成的 JavaScript 包太大。这会减慢我们的 Web 应用程序加载时间，这当然是不可取的。

为了避免这种情况，我们可以回想一下 vue-router 的工作原理：每个路由都是一个单独的 Vue 组件。

我们已经在前面看到，webpack 在捆绑我们的 Vue 应用程序时会生成**块**。这种行为可以用于我们的优势，使每个组件都捆绑到一个单独的块中。这是通过 Vue 的**异步组件**和 webpack 中的**代码拆分**实现的。

# 使用动态路由

什么是动态路由？让我们想象一个购物平台，在其中任何待售物品的 URL 结构都列在一个链接中，看起来像这样：

```js
https://example.com/items/:id
```

`:id`部分是所谓的**动态段**。要使用动态段，您首先需要像任何其他路由一样在`router.js`中的路由对象数组中列出它们，如下所示：

```js
routes: [
    {...},
    {...},
    {
        path: "/item/:id",
        name: "item",
        component: Item
    }
]
```

显然，在上述代码中，我们使用三个点来节省空间。

回到`Item.vue`视图组件的模板标签中，我们需要添加以下语法：

```js
<template>
    <article>
        <h1>Shopping item: {{ $route.params.id }}</h1>
    </article>
</template>
```

我们活跃路由的状态存储在`$route`中。

至少可以单独撰写一整章关于动态路由，所以此时，我们需要继续讨论如何从 Vue 实例的`methods`选项中触发路由。

通过这个，我们结束了这一章。

# 总结

在这一章中，我们看了如何使用 vue-router 和 vuex 添加一个新的 Vue 项目。我们还通过 VS Code 的命令行配置了预设选项。我们讨论了 vue-router 的路由，并且学习了如何使用命名路由，添加动态路由，从 Vue 实例的方法选项导航到一个路由，并且处理嵌套（子）路由。

在下一章中，我们将看到如何在 Vue CLI 3 中使用 ESLint 和 Prettier。
