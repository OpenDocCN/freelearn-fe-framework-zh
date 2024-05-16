# 第三章：使用 Vue-CLI，组件，props 和插槽工作

上一章是对 Vue 基本概念的介绍。我们将以更现实的方式开始本章：我们将介绍 Vue-cli。我们将查看组件层次结构，全局和本地组件以及组件之间的通信。我们将介绍插槽，并且我们还将检查插槽和 props 之间的区别。

在本章中，我们将涵盖以下主题：

+   Vue 组件层次结构，全局和本地组件

+   使用 Vue-cli

+   设置代码编辑器以与 Vue 一起使用

+   基于 Vue-cli 的项目结构

+   向子组件添加基本功能

+   向我们的`HelloAgain.vue`添加 props

+   插槽介绍

# Vue 组件层次结构，全局和本地组件

正如我们在第二章中学到的，*Vue 2 的基本概念*，要运行一个新的 Vue 实例，我们使用 new Vue：

```js
new Vue(
  el: "#app",
  // the rest of the Vue instance code here
)
```

我们的`app`组件位于这个 Vue 实例中。

app 组件通常有一个子组件，就像我们在第二章中看到的例子一样，*Vue 2 的基本概念*：[`codepen.io/AjdinImsirovic/pen/xzpOaJ`](https://codepen.io/AjdinImsirovic/pen/xzpOaJ)：

```js
Vue.component('custom-article', {
  template: `
    <article>
      Our own custom article component!
    </article>`
})
new Vue({
    el: '#app'
})
```

在上一章中我们没有提到的是：

+   子组件可以根据需要重复使用

+   子组件也可以有自己的子组件

这个例子可以在以下 pen 中找到：[`codepen.io/AjdinImsirovic/pen/ZjdOdK`](https://codepen.io/AjdinImsirovic/pen/ZjdOdK)。

以下是演示这两个原则的代码：

```js
// JS
Vue.component('custom-article', {
  template: `
    <article>
      Our own custom article component!
    </article>`
})
Vue.component('another-custom-article', {
  template: `
    <article>
      Another custom article component! 
      This one has it's own child component too!
      Here it is:
      <custom-article></custom-article>
    </article>`
})
new Vue({
    el: '#app'
})

/* CSS */
article {
  font-size: 40px;
  padding: 20px;
  color: limegreen;
  font-family: Arial;
  border: 3px solid green;
  border-radius: 10px;
}

<!-- HTML -->
<main id="app">
    <custom-article></custom-article>
    <custom-article></custom-article>
    <another-custom-article></another-custom-article>
</main>
```

如已经看到的，要向我们的 Vue 实例添加一个组件，我们使用以下语法：

```js
Vue.component('another-custom-article', { // etc...
```

在 Vue 术语中，我们使用这段代码来**注册**一个组件。如前所述，它被称为**全局注册**。还有**本地注册**。

本地注册与`Vue.component`语法类似。代码中唯一的区别是我们引入本地组件的方式与全局组件相比。在之前的代码中，我们有以下全局组件：

```js
Vue.component('custom-article', {
  template: `
    <article>
      Our own custom article component!
    </article>`
})
```

将这个全局组件转换为本地组件就像删除这段代码一样简单：

```js
Vue.component('custom-article'
```

与之前的代码不同，我们将简单地创建一个新变量，并给它与我们在全局组件中使用的完全相同的选项对象，就像这样：

```js
var customArticle = {
  template: `
    <article>
      Our own custom article component!
    </article>`
}
```

为了在 Vue 实例中使用这个本地组件，我们将引入`components`选项，就像这样：

```js
new Vue({
    el: '#app',
    components: { 
      'custom-article': customArticle
    }
})
```

这里有一个使用本地组件的示例：[`codepen.io/AjdinImsirovic/pen/ZMzrpr`](https://codepen.io/AjdinImsirovic/pen/ZMzrpr)。

然而，前面的示例是故意不完整的。正如我们所看到的，`customArticle`本地组件只能在主 Vue 实例中使用，而不能在`anotherCustomArticle`组件中使用。

为了使其正常工作并完成示例，我们需要调整这部分代码：

```js
Vue.component('another-custom-article', {
  template: `
    <article>
      Another custom article component! 
      This one has it's own child component too!
      Here it is:
      <custom-article></custom-article>
    </article>`,
    //components: {
    // 'customArticle': customArticle
    //}
})
```

我们只需删除这三行注释：

```js
    components: {
     'customArticle': customArticle
    }
```

通过这样做，我们已经在全局组件`anotherCustomArticle`中注册了本地组件`customArticle`。基本上，我们正在遵循在主 Vue 实例中注册本地组件的相同过程，并且我们正在应用相同的方法在`anotherCustomArticle`全局组件中注册本地组件。

要了解全局和本地注册的细微差别，您可以参考官方 Vue 文档的这一部分：

[`vuejs.org/v2/guide/components-registration.html`](https://vuejs.org/v2/guide/components-registration.html)。

在下一节中，我们将开始使用 Vue-cli。

# 使用 Vue-CLI

为了开始使用 Vue-cli，我们需要在计算机上设置 Node.js，并且我们还需要在我们选择的操作系统上安装一个命令行应用程序。

例如，我的首选工具是 Windows 10 和 Git bash for Windows。

有许多不同的操作系统和命令行应用程序，您可能会使用其中之一。

如果在安装本节中提到的任何工具时遇到问题，值得查看这篇关于在操作系统上安装 Node.js 的深入指南：

[`www.packtpub.com/mapt/book/web_development/9781788626859/2`](https://www.packtpub.com/mapt/book/web_development/9781788626859/2)

# 安装 Git bash

您首先需要访问[`git-scm.com/downloads`](https://git-scm.com/downloads)，该网站可以让您在 macOS X、Windows 和 Linux/Unix 安装之间进行选择。点击 Windows 下载后，您可以按照 Git bash 的安装步骤进行安装。在安装过程中，只需按照默认预设选项进行操作即可。

# 安装 nvm

要下载 Windows 的 Node 版本管理器，请访问此链接：

[`github.com/coreybutler/nvm-windows/releases`](https://github.com/coreybutler/nvm-windows/releases)

一旦进入页面，点击`nvm-setup.zip`文件进行下载，然后运行下载的`nvm-setup.exe`并按照常规安装步骤进行操作。

接下来，以管理员权限启动 Git bash，并运行以下命令：

```js
nvm install 8.11.4
```

以下消息将被记录到控制台：

```js
Downloading node.js version 8.11.4 (64-bit)...
```

# 为什么使用 nvm？

有两个主要原因：

+   安全关键升级

+   在不同项目中更轻松地切换 Node 版本

这里列出的第一个原因与 Node.js 的未来更新有关。假设在本书出版几个月后有一个重大的安全补丁，更新系统上的 Node 将是明智的选择。使用 nvm 可以轻松实现这一点，这也带来了第二点。即使没有可供升级的 Node 的主要版本，您仍然可以根据不同项目的需求运行不同版本的 Node。无论如何，使用 nvm 都是值得的。

下载完成后，在我们的 Git bash 中，我们可以简单地运行此命令：

```js
nvm use 8.11.4
```

现在，我们准备好使用 Vue-cli 了。

# 安装和更新 Vue-cli

值得注意的是，Vue-cli 是对 Webpack 的封装，经过调整和优化，以便在开发和发布 Vue 应用程序时提供最佳体验。这对开发人员来说是一个重大优势，因为这种设置让我们能够专注于编码，而不必长时间地与工具链搏斗。

让我们打开 Git bash 并运行以下命令：

```js
npm install -g vue-cli
```

由于 Vue-cli 是一个`npm`包，您可以在这里阅读更多信息：[`www.npmjs.com/package/vue-cli`](https://www.npmjs.com/package/vue-cli)。

要检查系统上安装的 Vue-cli 当前版本，请运行此命令：

```js
vue -V
```

请注意，Vue-cli 版本 2 和 3 之间有一个重大升级。为了确保您在系统上使用的是最新版本，您可以运行此命令：

```js
npm install @vue/cli
```

此命令将更新您的 Vue-cli 版本为最新版本。更新是本地的，这意味着它将放在您运行上一个命令的文件夹的`node_modules`文件夹中。请注意，由于需要安装的所有依赖项，此操作可能需要一些时间。

在使用 Vue-cli 初始化项目之前，快速列出 Vue-cli 版本 3 带来的改进将是有益的。希望这将强化第一章中关于 Vue 易用性的一些关键点，*介绍 Vue*。

Vue-cli 版本 3 的目标如下：

+   简化和优化工具链，避免前端开发中的工具疲劳

+   遵循最佳实践的工具，并使其成为 Vue 应用程序的默认选择

新版本的 Vue-cli 还有一系列功能和升级：

+   为热模块替换、树摇和其他功能预设 Webpack 配置

+   ES2017 功能

+   Babel 7 支持

+   PostCSS 支持

+   可选集成 Typescript、PWA、Jest、E2E 测试等

简而言之，Vue.js 跟上了时代的步伐，Vue-cli 只是更多的证据。

# 使用 Vue-cli 初始化一个新项目

安装完成后，我们可以使用以下命令初始化一个新项目：

```js
vue create quickstart-vue
```

我们给我们的 Vue 应用程序命名为*quickstart-vue*。我们也可以给它取其他任何名字。

一旦运行了上述命令，我们可以选择使用预设，或者手动选择要使用的功能：

```js
$ vue create quickstart-vue
 ? Please pick a preset: (Use arrow keys)
 > default (babel, eslint)
 Manually select features
```

我们可以选择默认预设，但作为一个小练习，让我们选择手动选择功能选项。然后我们将选择`npm`而不是`yarn`。这将导致屏幕上显示以下输出：

```js
$ vue create quickstart-vue
 ? Please pick a preset: (Use arrow keys)
 ? Please pick a preset: default (babel, eslint)
 ? Pick the package manager to use when installing dependencies: (Use arrow keys)
 ? Pick the package manager to use when installing dependencies: NPM
Installing CLI plugins. This might take a while...
```

当您看到这条消息时，您将知道插件已安装：

```js
...
Successfully created project quickstart-vue.
Get started with the following commands:
$ cd quickstart-vue
 $ npm run serve
```

现在我们可以简单地按照先前的说明，切换到`quickstart-vue`目录：

```js
cd quickstart-vue
```

接下来，我们将运行服务器（实际上是在后台运行 Webpack 开发服务器）：

```js
npm run serve
```

我们的应用程序可在端口`8080`上使用，将在控制台中记录。因此，让我们在`http://localhost:8080`上打开浏览器，查看默认站点：

![](img/7ffa1c92-53e9-4b24-b12b-7e03948fe060.png)

在下一节中，我们将设置两个编辑器以与我们的新 Vue 项目一起使用。这些编辑器是 Sublime Text 和 Visual Studio Code。

# 设置代码编辑器以与 Vue 一起使用

有许多代码编辑器和**IDEs**（**集成开发环境**）可以用来处理 Vue。其中一些比较流行的包括：

+   Sublime Text [`www.sublimetext.com/`](https://www.sublimetext.com/)

+   Visual Studio Code (VS Code), [`code.visualstudio.com/`](https://code.visualstudio.com/)

+   Atom, [`atom.io/`](https://atom.io/)

+   WebStorm, [`www.jetbrains.com/webstorm/`](https://www.jetbrains.com/webstorm/)

+   Visual Studio 2017, [`visualstudio.microsoft.com/downloads/`](https://visualstudio.microsoft.com/downloads/)

在本节中，我们将看看如何在 Sublime Text 和 VS Code 中使用 Vue.js。

# 在 Sublime Text 3 中使用 Vue.js

Sublime Text 是一个成熟且有趣的文本编辑器，因此我们将下载并设置它以用于我们的 Vue.js 项目。

# 下载 Sublime Text 3

我们将从下载页面开始下载 Sublime Text 3：

[`www.sublimetext.com/3`](https://www.sublimetext.com/3)

接下来，访问网站[`packagecontrol.io/`](https://packagecontrol.io/)，这是 Sublime Text 的软件包管理器的主页。

# 安装软件包管理器

在软件包管理器网站上，单击页面右上角的立即安装按钮，然后按照这些安装步骤进行操作：

1.  选择并复制 Sublime Text 3 标签内的所有文本。

1.  打开新安装的 Sublime Text 3。

1.  在 Sublime Text 3 中，按下*Ctrl* + *`*（按住并按下控制键，然后按下反引号键）的键盘快捷键。在大多数键盘上，反引号字符位于键盘的字母数字部分的 1 号键左侧。

1.  将从[`packagecontrol.io`](https://packagecontrol.io)复制的代码粘贴到上一步中打开的底部输入字段中。

完成这些步骤后，重新启动 Sublime Text，您将可以通过此键盘快捷键访问快速启动安装程序：*Ctrl* + *Shift* + *P*。

这个键盘组合将在屏幕中间显示一个小输入框，您可以在其中输入单词`install`。这将显示不同的选项，您可以使用鼠标单击或使用`箭头上`和`箭头下`键进行突出显示，然后使用`Enter`键运行：

![](img/951589bd-b6ae-4cda-985d-1a8a667b29c3.png)

接下来，选择读取“Package control: Install package”的选项。

这是我们将安装的软件包列表：

+   Vue Syntax Highlight，网址为[`packagecontrol.io/packages/Vue%20Syntax%20Highlight`](https://packagecontrol.io/packages/Vue%20Syntax%20Highlight)

+   Vuejs Snippets，网址为[`packagecontrol.io/packages/Vuejs%20Snippets`](https://packagecontrol.io/packages/Vuejs%20Snippets)

+   JavaScript Beautify，网址为[`packagecontrol.io/packages/Javascript%20Beautify`](https://packagecontrol.io/packages/Javascript%20Beautify)

有趣的是，Chrome 浏览器最近*也获得了类似的快速启动功能，可以通过相同的快捷键使用。要查看它的操作，您可以简单地使用*F12*键打开开发者工具实用程序，然后运行*Ctrl* + *Shift* + *P*快捷键。

例如，在打开的启动器中，您可以输入单词`node`，然后单击下拉菜单中的第一个命令`Capture node screenshot`。此命令将捕获您当前在 DOM 树中的元素的屏幕截图。

* 几个月前

在下一节中，我们将学习如何在 VS Code 中设置基于 Vue 的项目。

# 在 VS Code 中使用 Vue.js

尽管 Sublime Text 具有成熟和对系统资源消耗较少的优势，这使得它在较慢的机器上易于使用，但 VS Code 是一个可行的替代方案。

# 安装 VS Code 和扩展

让我们导航到[`code.visualstudio.com/download`](https://code.visualstudio.com/download)，并下载适合我们操作系统的 VS Code 版本。

如果您使用的是 Windows 10，您可以轻松地查看系统是 32 位还是 64 位。只需使用快捷键*Winkey* + *X*，然后在上下文菜单中单击 System。一个新窗口将打开，您将在`Device Specifications | System type`区域看到您的系统是 32 位还是 64 位。

一旦您下载并打开了 VS Code，就可以轻松地向其添加扩展。只需单击屏幕左侧最下方的图标（扩展图标）：

![](img/97362cf0-30ec-4c01-8ea5-b8c44ab19cce.png)

单击该图标将打开扩展窗格，您可以在其中输入 Vue 并获得类似于此的结果：

![](img/bc0a53e8-0fd8-45cf-b294-b43753a24bda.png)

接下来，只需选择 Vue VS Code Extension Packs 中的任一选项，然后单击绿色的 Install 按钮。此包含的扩展包括语法高亮、代码片段、代码检查和错误检查、格式化（如 js-beautify）、自动完成、悬停信息、自动重命名标签、VS Code 的 npm 支持、ES6 代码片段、ESLint 等。

或者，如果您想避免在 VS Code 扩展中出现臃肿，您可以通过安装 Pine Wu 的 Vetur 扩展来减少一些扩展，而不是之前提到的 Vue 扩展包。

安装完成后，我们只需单击标有 Reload 的按钮来重新启动 VS Code 并激活扩展。最后，要返回到项目的树结构，只需单击屏幕左侧 VS Code 主菜单下方的最顶部图标。

# 基于 Vue-cli 的项目结构

在这一部分，我们将看一下使用 Vue-cli 设置的 Vue 项目的文件结构。我们的`quickstart-vue`文件夹结构如下：

![](img/411b4431-89b9-476b-ae41-c3f3b6d522ff.png)

让我们首先检查`main.js`文件的内容：

```js
import Vue from 'vue'
import App from './App.vue'

Vue.config.productionTip = false

new Vue({
  render: h => h(App)
}).$mount('#app')
```

我们首先从`vue`文件夹中导入`Vue`。这个`vue`文件夹位于你的`node_modules`文件夹中。

接下来，我们从`App.vue`中导入`App`。

正如我们已经学到的，`new Vue`创建了一个 Vue 实例，然后我们将选项对象传递给它。

在选项对象中，我们只设置了`render`属性。正如我们所看到的，`render`属性的值是一个箭头函数：

```js
h => h(App)
```

箭头函数接受作为其参数我们在`main.js`文件的第二行导入的`App`组件。

正如你可能已经注意到的，前面的函数是用 ES6 编写的。转译为 ES5 后，它会变成这样：

```js
function(h) {
  return h(App); }
```

前面的函数接收一个要呈现的 Vue 模板。它将在我们的`index.html`页面中呈现，替换我们传递给`$mount()`函数的 DOM 的任何静态部分。

它将在 DOM 中的位置取决于我们将什么作为参数传递给`$mount()`函数。在前面的代码中，我们传递了`#app`参数。

`'#app'`来自哪里？它来自`App`组件，更具体地来说，来自位于我们的`src`文件夹中的`App.vue`文件。

`src`文件夹包含我们 Vue 项目的所有实际应用程序代码。

请注意，`main.js`是我们项目中唯一的实际 JavaScript 文件——`src`文件夹中的所有文件都具有`.vue`扩展名。每个`.vue`文件都有三个部分：模板定义了组件的 HTML，脚本定义了 JS，样式标签定义了 CSS。此外，Vue-cli（在底层使用 Webpack）将所有这些放在一起，因为它知道如何处理`.vue`文件。

让我们修改我们`src`文件夹中的`App.vue`文件，使其看起来像这样：

```js
<template>
  <div id="app">
    <HelloWorld msg="Welcome to Vue Quickstart!"/>
    <HelloAgain />
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue';
import HelloAgain from './components/HelloAgain.vue'

export default {
  name: 'app',
  components: {
    HelloWorld, HelloAgain
  }
}
</script>

<style>
#app {
  font-family: sans-serif;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```

让我们也改变`HelloWorld.vue`的内容，使其看起来像这样：

```js
<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <p>
      This is the beginning of something great.
    </p>
 </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
p {
  font-size: 20px;
  font-weight: 600;
  text-align: center;
}
</style>
```

最后，让我们在`src/components/`文件夹中添加另一个组件。我们将其命名为`HelloAgain.vue`，并给它以下代码：

```js
<template>
 <p class="hello-again">
 This is another component.
 </p>
</template>

<script>
export default {
 name: 'HelloAgain'
}
</script>

<style scoped>
p {
 font-size: 16px;
 text-align: center;
 color: tomato;
}
</style>
```

在这三个文件中，我们主要只是删除了一些额外的代码，以更清楚地展示以下几点：

+   每个`vue`文件都包含一个单文件组件

+   每个单文件组件的结构都遵循相同的模式：顶部是模板，中间是脚本，底部是样式

+   样式可以针对每个单独的文件进行作用域限定

+   `App.vue`文件从`components`文件夹导入组件并导出自身，以便`main.js`可以使用它

+   `HelloWorld`和`HelloAgain`组件只是将自己导出到父组件`App.vue`文件中

+   为了使用新引入的组件（`HelloAgain`组件），`App.vue`文件需要在其`<template>`标签内添加它

+   `App.vue`文件还需要导入和导出`HelloAgain`单文件模板，以便`main.js`可以使用它

`App.vue`，`HelloWorld.vue`和`HelloAgain.vue`是单文件组件的示例。单文件组件是我们 Vue 项目中使用组件的首选方式。

如果您按照前面描述的更改文件，您应该在浏览器中的`http://localhost:8080`看到以下屏幕：

![](img/36843230-2832-419a-8797-b07bdbab04fa.png)

现在我们已经看到了`vue/components/`文件夹的组织方式和基本工作原理，我们将列出我们 Vue 项目中的其他重要文件：

1.  不应该被 Git 源代码版本控制跟踪的文件列表：`.gitignore`

1.  Babel 的配置文件：`.babel.config.js`

1.  列出我们基于 npm 的项目的依赖项和其他信息的文件：`package.json`

1.  我们应用的 markdown 格式手册：`README.md`

当然，还有一个公共文件夹，其中包含我们编译后的应用程序，从`index.html`文件中引用。这个文件最终将在浏览器中呈现和重新呈现，因为我们的 Vue 应用程序不断编译。`index`文件的内容非常简单：

```js
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <link rel="icon" href="<%= BASE_URL %>favicon.ico">
    <title>quickstart-vue</title>
  </head>
  <body>
    <noscript>
      <strong>
        We're sorry but quickstart-vue doesn't work properly 
        without JavaScript enabled. Please enable it to continue.
      </strong>
    </noscript>
    <div id="app"></div>
    <!-- built files will be auto injected -->
  </body>
</html>
```

如前所述，`id`属性设置为`app`的`div`是我们 Vue 应用程序的入口点。

现在我们对项目结构有了更好的理解，我们将继续构建子组件。

在下一节中，我们将向我们的`HelloAgain`组件添加一些基本功能。

# 向子组件添加基本功能

在这一部分，我们将向子组件添加一些非常基本的功能。在我们深入了解如何做到这一点之前，我们还需要安装官方的 Vue Chrome 扩展。

Chrome 的 Vue 开发者工具扩展可以在此 URL 找到：[`bit.ly/2Pkpk2I`](http://bit.ly/2Pkpk2I)。

安装官方的 Vue Chrome 扩展很简单；您只需像安装其他 Chrome 扩展一样安装它。

安装完成后，你将在 Chrome 右上角看到一个 Vue 标志，点击该标志将会给你以下消息：

Vue.js 在此页面上被检测到。打开 DevTools 并查找 Vue 面板。

打开 DevTools 很容易：只需按 F12 键。然后你可以在具有以下标签的区域中找到 Vue 面板：元素、控制台、源等。你应该会得到类似以下屏幕的东西：

![](img/57fb3263-e102-4969-a3d2-8b4dc908bc44.png)

回到 VS Code，让我们打开`HelloAgain.vue`组件，并更新代码的模板部分，使其看起来像这样：

```js
<template>
 <p class="hello-again">
 This is another component.
 <button v-on:click="incrementUp">Add One</button>
 <br>
 <span>Current value of the counter: {{ counter }}</span>
 </p>
</template>
```

让我们也更新`script`标签，就像这样：

```js
<script>
export default {
 name: 'HelloAgain',
 data() {
     return {
         counter: 0
     }
 },
 methods: {
     incrementUp: function() {
         this.counter++
     }
 }
}
</script>
```

最后，我们将更新样式，使我们的按钮看起来更漂亮：

```js
<style scoped>
p {
 font-size: 16px;
 text-align: center;
 color: tomato;
}
button {
    display: block;
    margin: 10px auto;
    background: tomato;
    color: white;
    border: none;
    padding: 5px 10px;
    font-size: 16px;
    border-radius: 4px;
    cursor: pointer;
}
</style>
```

这次更新的最终结果将在我们的浏览器中呈现如下：

![](img/36b70dfd-26c2-402f-a6ae-6657f22cc9bd.png)

现在我们已经看过构建模板和使用一些基本功能，让我们把重点转移到另一个重要的主题：组件之间的通信。我们将从重新审视 props 开始，这是一种在父组件和子组件之间通信的方式。

# 向我们的 HelloAgain.vue 添加 props

在这一部分，我们将简要回顾 props，看一个在 Vue 中如何在父组件和子组件之间通信的实际例子。换句话说，我们想从父组件获取一些数据并将其传递给子组件。我们将要传递的数据只是要包含在我们的`quickstart-vue`应用程序的计数器中的额外数字。

在我们的`App.vue`文件中，我们将添加一个按钮：

```js
<template>
  <div id="app">
    <HelloWorld msg="Welcome to Vue Quickstart!"/>
    <button v-on:click="addTen">Add 10</button>
    <HelloAgain v-bind:counterFromParent="countUp"/>
  </div>
</template>
```

按钮放置在我们已经拥有的两个组件之间。我们已经添加了`v-on`指令，跟踪按钮上的点击事件。点击事件将触发`addTen`方法，因此我们将在`App.vue`文件的`<script>`标签之间指定它：

```js
  methods: {
    addTen() {
      this.countUp += 10
    }
  },
```

`addTen`方法正在使用`countUp`数据片段，所以让我们也将这个新数据添加到我们的`<script>`中：

```js
  data: function() {
    return {
      countUp: 0
    };
  },
```

因此，最初，我们在`App.vue`中的`data`函数返回零的`countUp`。每当用户点击我们的`App.vue`组件中的按钮时，`countUp`的值增加 10。这个值是我们想要传递给子组件的数据，即`HelloAgain.vue`子组件中计数器中存储的值。

这就是`props`语法的用法。为了告诉我们的`HelloAgain.vue`组件它应该期望来自父组件的数据，我们将添加以下代码：

```js
props: ['counterFromParent']
```

`props`键的值是一个数组，我们在其中添加了子组件应该从父组件那里期望的`props`字符串。

请注意，`props`选项也可以是一个对象。使用对象作为我们`props`选项的示例是，例如，如果我们想要验证从父组件传递给子组件的数据。我们将在本书的后面章节中验证`props`。

在`HelloAgain.vue`中，我们将修改其模板内的`<span>`标签，就像这样：

```js
<span>Current value of the counter: {{ counter + counterFromParent }}</span>
```

现在我们已经在父组件和子组件中设置了代码，只需将数据从一个传递到另一个。我们将在`App.vue`模板中通过在`<HelloAgain />`标签上添加`v-bind`指令来实现这一点。以下是更新后的`App.vue`模板：

```js
<template>
  <div id="app">
    <HelloWorld msg="Welcome to Vue Quickstart!"/>
    <button v-on:click="addTen">Add 10</button>
    <HelloAgain v-bind:counterFromParent="countUp"/>
  </div>
</template>
```

请注意，我们将`counterFromParent`绑定到`countUp`的值。`countUp`的值从零开始，每次点击父组件按钮时，将运行`addTen`方法，我们在父组件`<script>`标签的`methods`选项中指定了这一点。

`addTen`方法将 10 添加到`countUp`的当前值。

在子组件`HelloAgain.vue`中，我们只需将`counterFromParent`的当前值添加到我们的`counter`变量中。要获取`counterFromParent`的值，我们将其列在`HelloAgain.vue`组件的`<script>`标签的`props`数组中。

# 从子组件传递数据到父组件

要从子组件传递数据到父组件，我们使用以下语法：

```js
this.$emit();
```

`$`符号用于表示内置的 Vue 函数。这个特定的`$emit`函数用于向父组件发送自定义事件。我们传递给`$emit`函数的第一个参数是自定义事件的名称。例如，我们可以将计数器重置为零，所以我们可以像这样命名自定义事件：

```js
this.$emit('counterHasBeenReset')
```

第二个参数是要传递的数据，所以我们将传递当前的计数器值，就像这样：

```js
this.$emit('counterHasBeenReset', this.countUp);
```

当然，这意味着我们需要更新`countUp`的值，使其返回到零。为了做到这一点，我们需要更新`HelloAgain`子组件的`<script>`标签中的`methods`选项，使其看起来像这样：

```js
 methods: {
     incrementUp: function() {
         this.counter++
     },
     resetTheCounter() {
         this.countUp = 0;
         this.$emit('counterHasBeenReset', this.countUp);
     }
 }
```

基本上，我们在方法选项中说，每当运行`resetTheCounter`方法时，`countUp`的值应该被重置为`0`。接下来，我们通过在`counterHasBeenReset`自定义事件中发出这个更新后的值来跟进。

现在让我们在子组件`template`标签中添加一个重置按钮，也在`HelloAgain.vue`中。我们只需在`template`标签中添加另一行即可：

```js
<button v-on:click="resetTheCounter">Reset parent-added values</button>
```

正如我们在这里看到的，按钮点击将运行`resetTheCounter`方法。

现在我们正在发出事件，我们将使用以下语法在父组件中捕获它：

```js
<HelloAgain v-bind:counterFromParent="countUp" v-on:counterHasBeenReset="countUp = $event" />
```

正如我们在这里看到的，我们在父组件中的`<HelloAgain>`标签中添加了内容。具体来说，我们添加了一个`v-on`指令，如下所示：

```js
v-on:counterHasBeenReset="countUp = $event" />
```

该组件正在监听`counterHasBeenReset`自定义事件，该事件将从子组件中发出。当在父组件中捕获到这样的事件时，`countUp`的值将被设置为事件本身中的值。由于我们将其设置为零，它就是零。

在 Vue 中，有其他的方式来在组件之间进行通信（包括父到子和子到子），我们将在后面的章节中讨论它们，当我们讨论 Vuex 时。

这个练习的最终结果是，我们将重置从父组件中添加的计数器中的值，但该事件不会影响从子组件中添加的值。

现在我们已经了解了自定义事件，我们可以通过查看插槽来继续讨论组件。

# 插槽介绍

插槽是重用组件的一种方式。通过 props，我们将数据传递给组件。但是，如果我们想要将整个组件传递给其他组件怎么办？这就是插槽的用武之地。

插槽只是一种将更复杂的代码传递给我们的组件的方法。它们可以是一些 HTML，甚至是整个组件。

要将 HTML 元素从父组件插入到子组件中，我们在子组件内部使用`slot`元素：

```js
<slot></slot>
```

插槽的实际内容在父组件中指定。

这是插槽使用的一个例子：

```js
<!-- HTML -->
<div id="app"></div>

// JS
Vue.component("basicComponent", {
  template: `
    <div>
      <slot name="firstSlot"></slot>
      <slot name="secondSlot"></slot>
      <slot></slot>
    </div>
  `
});

new Vue({
  el: "#app",
  template: `
    <basicComponent>
      <p slot="firstSlot">
        This content will populate the slot named 'firstSlot' 
        in the 'basicComponent' template
      </p>
      <p slot="secondSlot">
        This content will populate the slot named 'secondSlot' 
        in the 'basicComponent' template
      </p>
    </basicComponent>
  `
});

/* CSS */
div {
  font-size: 30px;
  padding: 20px;
  color: darkgoldenrod;
  font-family: Arial;
  border: 3px solid darkgoldenrod;
  border-radius: 0px;
}
```

这个例子可以在这里实时查看：[`codepen.io/AjdinImsirovic/pen/ERoLQM`](https://codepen.io/AjdinImsirovic/pen/ERoLQM)。

在处理插槽时有几个关键点：

+   插槽是基于 Web 组件规范草案实现的

+   插槽的样式由子组件中的作用域样式标签确定

+   插槽使可组合组件的使用成为可能

+   您可以在插槽中使用任何模板代码

+   如果您有多个插槽，可以为它们命名（使用`name`属性）

+   如果您有多个插槽，可以在其中一个插槽中省略`name`属性，那个插槽将成为默认插槽

+   从 Vue 2.1.0 开始，插槽可以被作用域化

+   插槽作用域可以使用 ES2015 表达式解构进行解构

要向插槽添加默认信息，只需在插槽标记中添加内容。

只需将插槽标记的代码从这个：

```js
<slot></slot>
```

更改为这个：

```js
<slot>This is some default information</slot>
```

如果您通过在提供的示例笔中添加上面引用的默认未命名插槽代码来更新它，您会注意到即使我们没有在 Vue 实例中引用它，插槽也会被填充。

# 总结

在本章中，我们讨论了 Vue 的组件。我们讨论了 Vue 组件层次结构以及全局和本地组件之间的区别。我们启动了 Vue-cli v3 并学习了如何使用它。我们使用`.vue`文件并在几个代码编辑器中设置了开发环境。我们了解了如何向子组件添加功能以及 props 和 slots 的用例。最后，我们看了 Vue 中的组件通信。

在下一章中，我们将讨论过滤器作为改变屏幕上呈现内容的一种方式，而不影响其背后的数据。我们还将看到如何在编程中遵循 DRY 规则，借助混合来实现。
