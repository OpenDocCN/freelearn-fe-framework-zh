# 第四章：高级项目设置

在本章之后，我们将开始构建更复杂的应用程序，我们将需要一些额外的工具和库。我们将涵盖以下主题：

+   设置我们的开发环境

+   使用 vue-cli 搭建 Vue 应用程序

+   编写和使用单文件组件

# 设置我们的开发环境

为了创建更复杂的单页应用程序，建议使用一些工具来简化开发。在本节中，我们将安装它们以准备好良好的开发环境。您需要在计算机上安装 Node.js 和 npm。确保您至少拥有 Node 8.x，但建议使用最新的 Node 版本。

# 安装 vue-cli，官方命令行工具

我们首先需要的包是 vue-cli，这是一个命令行工具，将帮助我们创建 Vue 应用程序：

1.  在终端中输入此命令，它将安装 vue-cli 并将其保存为全局包：

```js
 npm install -g vue-cli
```

您可能需要以管理员身份运行此命令。

1.  要测试 vue-cli 是否正常工作，请使用以下命令打印其版本：

```js
 vue --version
```

# 代码编辑器

任何文本编辑器都可以，但我建议使用 Visual Studio Code（[`code.visualstudio.com/`](https://code.visualstudio.com/)）或 Atom（[`atom.io/`](https://atom.io/)）。对于 Visual Studio Code，您需要来自 octref 的`vetur`扩展（[`github.com/vuejs/vetur`](https://github.com/vuejs/vetur)），对于 Atom，您需要来自 hedefalk 的`language-vue`扩展（[`atom.io/packages/language-vue`](https://atom.io/packages/language-vue)）。

最近版本的 Jetbrains 的 WebStorm IDE 支持 Vue。

您还可以安装添加对预处理器语言（如 Sass、Less 或 Stylus）的支持的扩展。

# 我们的第一个完整的 Vue 应用程序

之前的应用程序都是以相当老式的方式制作的，使用`script`标签和简单的 JavaScript。在本节中，我们将发现使用一些强大功能和工具创建 Vue 应用程序的新方法。在这部分中，我们将创建一个迷你项目来演示我们将在接下来使用的新工具。

# 搭建项目脚手架

vue-cli 工具使我们能够创建即用型的应用程序框架，以帮助我们开始新项目。它使用一个项目模板系统，可以向您询问一些问题，以自定义框架以满足您的需求：

1.  用以下命令列出官方项目模板：

```js
 vue list
```

以下是终端中显示的列表：

![](img/8caaafd7-e612-44c7-b349-3375f93bc7c3.png)

官方模板有三种主要类型：

+   simple：不使用构建工具

+   webpack：使用非常流行的 webpack 打包工具（推荐）

+   browserify：使用 browserify 构建工具

推荐的官方模板是`webpack`模板。它包含了创建一个具有 Vue 的全尺寸 SPA 所需的一切。为了本书的目的，我们将使用`webpack-simple`并逐步介绍功能。

要使用这些模板之一创建一个新的应用程序项目，请使用`npm init`命令：

```js
vue init <template> <dir>
```

我们将在一个新的`demo`文件夹中使用`webpack-simple`官方模板：

1.  运行以下命令：

```js
 vue init webpack-simple demo
```

这个项目模板具有一个准备好使用的最小 webpack 配置。该命令将会询问一些问题。

1.  像这样回答 vue-cli 的问题：

```js
      ? Project name demo
      ? Project description Trying out Vue.js!
      ? Author Your Name <your-mail@mail.com>
      ? License MIT
      ? Use sass? No
```

Vue-cli 现在应该已经创建了一个`demo`文件夹。它有一个`package.json`文件和其他配置文件已经为我们填充。`package.json`文件非常重要；它包含有关项目的主要信息；例如，它列出了项目所依赖的所有软件包。

1.  转到新创建的`demo`文件夹，并安装`webpack-simple`模板已经在`package.json`文件中声明的默认依赖项（如 vue 和 webpack）：

```js
 cd demo
 npm install
```

我们的应用现在已经设置好了！

从现在开始，我们将完全使用 ECMAScript 2015 语法和`import`/`export`关键字来使用或公开模块（这意味着导出 JavaScript 元素的文件）。

# 创建应用程序

任何 Vue 应用程序都需要一个 JavaScript 入口文件，代码将从那里开始：

1.  删除`src`文件夹的内容。

1.  创建一个名为`main.js`的新的 JavaScript 文件，内容如下：

```js
      import Vue from 'vue'

      new Vue({
        el: '#app',
        render: h => h('div', 'hello world'),
      })
```

首先，我们将 Vue 核心库导入文件中。然后，我们创建一个新的根 Vue 实例，将其附加到页面中`id`为 app 的元素上。

vue-cli 提供了一个默认的`index.html`文件，其中包含一个空的`<div id="app"></div>`标签。您可以编辑它以更改页面 HTML 以满足您的喜好。

最后，我们通过`render`选项显示包含`'hello world'`文本的`div`元素，这要归功于我们将在“渲染函数”部分介绍的`render`选项。

# 运行我们的应用程序

运行由 vue-cli 生成的`dev` npm 脚本以在开发模式下启动应用程序：

```js
npm run dev
```

这将在 web 服务器端口上启动一个 web 应用程序。终端应该显示编译成功以及访问应用程序的 URL：

![](img/70045016-b634-4ec2-a1ad-a9262aa61474.png)

在浏览器中打开此 URL 以查看结果：

![](img/cddefb10-81c2-4e72-aa4c-3caf893cdc3d.png)

# 渲染函数

Vue 使用虚拟 DOM 实现，由 JavaScript 对象组成的元素树。然后通过计算两者之间的差异，将虚拟 DOM 应用于真实的浏览器 DOM。这有助于尽量避免 DOM 操作，因为它们通常是主要的性能瓶颈。

实际上，当您使用模板时，Vue 会将其编译为渲染函数。如果您需要 JavaScript 的全部功能和灵活性，可以直接编写渲染函数，或编写稍后将讨论的 JSX。

渲染函数返回树的一小部分，该部分特定于其组件。它使用`createElement`方法作为第一个参数。

按照惯例，`h`是`createElement`的别名，这是非常常见的，也是编写 JSX 所需的。它来自于用 JavaScript 描述 HTML 的技术名称--Hyperscript。

`createElement`（或`h`）方法最多接受三个参数：

1.  第一个是元素的类型。它可以是 HTML 标签名（如`'div'`），在应用程序中注册的组件名称，或直接是组件定义对象。

1.  第二个参数是可选的。它是定义属性、props、事件监听器等的数据对象。

1.  第三个参数也是可选的。它可以是简单的纯文本，也可以是用`h`创建的其他元素数组。

考虑以下`render`函数作为示例：

```js
render (h) {
  return h('ul', { 'class': 'movies' }, [
    h('li', { 'class': 'movie' }, 'Star Wars'),
    h('li', { 'class': 'movie' }, 'Blade Runner'),
  ])
}
```

它将在浏览器中输出以下 DOM：

```js
<ul class="movies">
  <li class="movie">Star Wars</li>
  <li class="movie">Blade Runner</li>
</ul>
```

我们将在第六章中更详细地介绍渲染函数，*项目 4-地理定位博客*。

# 配置 babel

Babel 是一个非常流行的工具，它编译 JavaScript 代码，以便我们可以在较旧和当前的浏览器中使用新功能（如 JSX 或箭头函数）。建议在任何严肃的 JavaScript 项目中使用 babel。

默认情况下，`webpack-simple`模板带有默认的 babel 配置，使用支持 ES2015 中所有稳定 JavaScript 版本的`env` babel 预设。它还包括另一个名为`stage-3`的 babel 预设，支持即将到来的 JavaScript 特性，如`async/await`关键字和 Vue 社区常用的对象扩展运算符。

我们需要添加第三个特定于 Vue 的预设，它将为 JSX 添加支持（我们将在本章后面的“JSX”部分中需要它）。

我们还需要包括 babel 提供的 polyfills，以便在旧的浏览器中使用新功能，比如`Promise`和生成器。

Polyfill 是一种代码，用于检查浏览器中是否有某个功能，如果没有，则实现此功能，使其像本地功能一样工作。

# Babel Vue 预设

我们现在将在应用程序的 Babel 配置中安装和使用`babel-preset-vue`。

1.  首先，我们需要在开发依赖项中安装这个新的预设：

```js
 npm i -D babel-preset-vue
```

主要的 babel 配置已经在项目根目录中的`.babelrc` JSON 文件中完成。

这个文件可能在您的文件资源管理器中被隐藏，这取决于系统（它的名称以点开头）。但是，如果它有文件树视图，它应该在您的代码编辑器中可见。

1.  打开这个`.babelrc`文件，并将`vue`预设添加到相应的列表中：

```js
      {
        "presets": [
          ["env", { "modules": false }],
          "stage-3",
          "vue"
        ]
      }
```

# Polyfills

让我们还添加 Babel polyfills，以在旧的浏览器中使用新的 JavaScript 功能。

1.  在开发依赖项中安装`babel-polyfill`软件包：

```js
 npm i -D babel-polyfill
```

1.  在`src/main.js`文件的开头导入它：

```js
 import  'babel-polyfill'
```

这将为浏览器启用所有必要的 polyfills。

# 更新依赖项

项目脚手架完成后，您可能需要更新它使用的软件包。

# 手动更新

要检查项目中使用的软件包是否有新版本可用，请在根文件夹中运行此命令：

```js
npm outdated
```

如果检测到新版本，将显示一个表格：

![](img/b35444cf-12e8-4295-abf4-2c45b60a5fd5.png)

`Wanted`列是与`package.json`文件中指定的版本范围兼容的版本号。要了解更多信息，请访问 npm 文档[http s://docs.npmjs.com/getting-started/semantic-versioning](https://docs.npmjs.com/getting-started/semantic-versioning)。

要手动更新软件包，请打开`package.json`文件并找到相应的行。更改版本范围并保存文件。然后，运行此命令以应用更改：

```js
npm install
```

不要忘记阅读您更新的软件包的更改日志！可能会有重大变化或改进，您会很乐意了解。

# 自动更新

要自动更新软件包，请在项目的根文件夹中使用此命令：

```js
npm update
```

这个命令只会更新与`package.json`文件中指定版本兼容的版本。如果您想要更新包到其他版本，您需要手动进行。

# 更新 Vue

当您更新包含核心库的`vue`包时，您也应该更新`vue-template-compiler`包。这是一个在使用 webpack（或其他构建工具）时编译所有组件模板的包。

这两个包必须始终处于相同的版本。例如，如果您使用`vue 2.5.3`，那么`vue-template-compiler`也应该是版本`2.5.3`。

# 为生产构建

当您需要将您的应用程序部署到真实服务器上时，您需要运行这个命令来编译您的项目：

```js
npm run build
```

默认情况下，使用`webpack-simple`模板时，它会将 JavaScript 文件输出到项目的`/dist`文件夹中。您只需要上传这个文件夹和根文件夹中存在的`index.html`文件。您的服务器上应该有以下文件树：

```js
- index.html
- favicon.png
- [dist] - build.js
         ∟ build.map.js
```

# 单文件组件

在这一部分，我们将介绍一个在创建真实生产 Vue 应用程序中广泛使用的重要格式。

Vue 有自己的格式称为**单文件组件**（**SFC**）。这个格式是由 Vue 团队创建的，文件扩展名是`.vue`。它允许您在一个地方编写一个文件的模板、逻辑和样式。这个地方的主要优势是每个组件都是清晰自包含的，更易维护和共享。

SFC 使用类似 HTML 的语法描述了一个 Vue 组件。它可以包含三种类型的根块：

+   `<template>`，描述了组件的模板，使用了我们已经使用过的模板语法

+   `<script>`，其中包含组件的 JavaScript 代码

+   `<style>`，其中包含组件使用的样式

以下是一个 SFC 的示例：

```js
<template>
  <div>
    <p>{{ message }}</p>
    <input v-model="message"/>
  </div>
</template>

<script>
export default {
  data () {
    return {
      message: 'Hello world',
    }
  },
}
</script>

<style>
p {
  color: grey;
}
</style>
```

现在让我们尝试这个组件！

1.  将上述组件源代码放入`src`文件夹中的新`Test.vue`文件中。

1.  编辑`main.js`文件，并使用`import`关键字导入 SFC：

```js
      import Test from './Test.vue'
```

1.  删除`render`选项，而是使用对象扩展运算符复制`Test`组件的定义：

```js
      new Vue({
        el: '#app',
        ...Test,
      })
```

在前面的片段中，我演示了将根组件添加到应用程序的另一种方法--使用 JavaScript Spread 运算符--因此`...App`表达式将将属性复制到应用程序定义对象。主要优点是我们将不再在开发工具中有一个无用的顶级组件；它现在将是我们的直接根组件。

1.  继续打开终端中显示的 URL 以查看结果：

![](img/bcd377ab-9146-4ad7-8711-97d392d83d4d.png)

# Template

`<template>`标签包含组件的模板。与之前一样，它是 HTML，具有 Vue 特殊语法（指令，文本插值，快捷方式等）。

以下是 SFC 中`<template>`标签的示例：

```js
<template>
  <ul class="movies">
    <li v-for="movie of movies" class="movie">
      {{ movie.title }}
    </li>
  </ul>
</template>
```

在此示例中，我们组件的模板将包含一个包含显示电影标题的`li`元素列表的`ul`元素。

如果在 SFC 中不放置`<template>`标签，您将需要编写一个渲染函数，否则您的组件将无效。

# 使用 Pug

Pug（以前叫 Jade）是一种编译成 HTML 的语言。我们可以在`<template>`标签中使用它，`lang`属性设置为“pug”：

```js
<template lang="pug">
ul.movies
  li.movie Star Wars
  li.movie Blade Runner
</template>
```

要编译 SFC 中的 Pug 代码，我们需要安装这些包：

```js
npm install --save-dev pug pug-loader
```

开发所需的包称为开发依赖项，并应使用`--save-dev`标志安装。应使用`--save`标志安装应用程序运行所需的直接依赖项（例如，将 markdown 编译为 HTML 的包）。

# Script

`<script>`标签包含与组件关联的 JavaScript 代码。它应该导出组件定义对象。

以下是`<script>`标签的示例：

```js
<script>
export default {
  data () {
    return {
      movies: [
        { title: 'Star Wars' },
        { title: 'Blade Runner' },
      ],
    }
  },
}
</script>
```

在此示例中，组件将具有返回具有`movies`数组的初始状态的`data`钩子。

如果您不需要组件选项中的任何选项，默认为空对象，则`<script>`标签是可选的。

# JSX

JSX 是在 JavaScript 代码中使用的一种特殊表示法，用于表示 HTML 标记。它使代码负责描述视图的方式更接近纯 HTML 语法，同时仍然具有 JavaScript 的全部功能。

以下是使用 JSX 编写的渲染函数示例：

```js
<script>
export default {
  data () {
    return {
      movies: [
        { title: 'Star Wars' },
        { title: 'Blade Runner' },
      ],
    }
  },
  render (h) {
    const itemClass = 'movie'
    return <ul class='movies'>
      {this.movies.map(movie =>
        <li class={ itemClass }>{ movie.title }</li>
      )}
    </ul>
  },
}
</script>
```

您可以在单括号内使用任何 JavaScript 表达式。

正如您在此示例中所看到的，我们可以使用任何 JavaScript 代码来组成我们的视图。我们甚至可以使用`movies`数组的`map`方法为每个项目返回一些 JSX。我们还使用了一个变量来动态设置电影元素的 CSS 类。

在编译过程中，真正发生的是一个名为`babel-plugin-transform-vue-jsx`的特殊模块包含在`babel-preset-vue`中，将 JSX 代码转换为纯 JavaScript 代码。编译后，前面的渲染函数将如下所示：

```js
render (h) {
  const itemClass = 'movie'
  return h('ul', { class: 'movies' },
    this.movies.map(movie =>
      h('li', { class: itemClass }, movie.title)
    )
  )
},
```

如您所见，JSX 是一种帮助编写渲染函数的语法。最终的 JavaScript 代码将与我们手动使用`h`（或`createElement`）编写的代码非常接近。

我们将在第六章中更详细地介绍渲染函数，*项目 4 - 地理定位博客*。

# 样式

单文件组件可以包含多个`<style>`标签，以向应用程序添加与此组件相关的 CSS。

以下是一个非常简单的组件样式应用一些 CSS 规则到`.movies`类：

```js
<style>
.movies {
  list-style: none;
  padding: 12px;
  background: rgba(0, 0, 0, .1);
  border-radius: 3px;
}
</style>
```

# 作用域样式

我们可以使用作用域属性将包含在`<style>`标签中的 CSS 限定为当前组件。这意味着此 CSS 仅应用于此组件模板的元素。

例如，我们可以使用通用的类名，如 movie，并确保它不会与应用程序的其余部分发生冲突：

```js
<style scoped>
.movie:not(:last-child) {
  padding-bottom: 6px;
  margin-bottom: 6px;
  border-bottom: solid 1px rgba(0, 0, 0, .1);
}
</style>
```

结果将如下所示：

![](img/c0c86f93-a28f-4002-8141-78469f0d8495.png)

这是有效的，多亏了应用于模板和 CSS 的特殊属性，使用 PostCSS（一种处理工具）。例如，考虑以下作用域样式组件：

```js
<template>
  <h1 class="title">Hello</h1>
</template>

<style scoped>
.title {
  color: blue;
}
</style>
```

它相当于以下内容：

```js
<template>
  <h1 class="title" data-v-02ad4e58>Hello</h1>
</template>

<style>
.title[data-v-02ad4e58] {
  color: blue;
}
</style>
```

如您所见，为所有模板元素和所有 CSS 选择器添加了一个唯一的属性，以便它只匹配此组件的模板，不会与其他组件冲突。

作用域样式并不能消除对类的需求；由于浏览器呈现 CSS 的方式，当选择一个带有属性的普通元素时，可能会出现性能损失。例如，`li { color: blue; }`在组件范围内将比`.movie { color: blue; }`慢得多。

# 添加预处理器

现在，CSS 很少被直接使用。通常会使用功能更强大、功能更丰富的预处理器语言来编写样式。

在`<style>`标签上，我们可以使用`lang`属性指定其中一种语言。

我们将以此模板作为我们组件的基础：

```js
<template>
  <article class="article">
    <h3 class="title">Title</h3>
  </article>
</template>
```

# Sass

Sass 是许多技术公司使用的知名 CSS 预处理器：

1.  要在组件中启用 Sass，请安装以下软件包：

```js
 npm install --save-dev node-sass sass-loader
```

1.  然后，在您的组件中，添加一个`<style>`标签，其中`lang`属性设置为`"sass"`：

```js
      <style lang="sass" scoped>
      .article
        .title
          border-bottom: solid 3px rgba(red, .2)
      </style>
```

1.  现在，使用`vue build`命令测试您的组件。您应该有一个类似于这样的结果：

![](img/a154cb93-95aa-4ac3-8734-6c1c337db551.png)如果您想使用 Sass 的 SCSS 语法变体，您需要使用`lang="scss"`。

# Less

Less 的语法比其他 CSS 预处理语言更简单：

1.  要使用 Less，您需要安装以下包：

```js
 npm install --save-dev less less-loader
```

1.  然后，在您的组件中，将`lang`属性设置为`"less"`：

```js
      <style lang="less" scoped>
      .article {
        .title {
          border-bottom: solid 3px fade(red, 20%);
        }
      }
      </style>
```

# Stylus

Stylus 比 Less 和 Sass 更新，也非常受欢迎：

1.  最后，对于 Stylus，您需要这些包：

```js
 npm install --save-dev stylus stylus-loader
```

1.  在`<style>`标签上，将`lang`属性设置为`"stylus"`：

```js
      <style lang="stylus" scoped>
      .article
        .title
          border-bottom solid 3px rgba(red, .2)
      </style>
```

# 组件内部的组件

现在我们知道如何编写单文件组件，我们希望在其他组件中使用它们来组成应用程序的界面。

要在另一个组件中使用组件，我们需要导入它并将其公开给模板：

1.  首先，创建一个新的组件。例如，这是一个`Movie.vue`组件：

```js
      <template>
        <li class="movie">
          {{ movie.title }}
        </li>
      </template>

      <script>
      export default {
        props: ['movie'],
      }
      </script>

      <style scoped>
      .movie:not(:last-child) {
        padding-bottom: 6px;
        margin-bottom: 6px;
        border-bottom: solid 1px rgba(0, 0, 0, .1);
      }
      </style>
```

如果您还没有创建`Movies.vue`组件，我们还需要一个。它应该是这样的：

```js
<template>
  <ul class="movies">
    <li v-for="movie of movies" class="movie">
      {{ movie.title }}
    </li>
  </ul>
</template>

<script>
export default {
  data () {
    return {
      movies: [
        { id: 0, title: 'Star Wars' },
        { id: 1, title: 'Blade Runner' },
      ],
    }
  },
}
</script>
```

1.  然后，在`Movies`组件的脚本中导入`Movie` SFC：

```js
      <script>
      import Movie from './Movie.vue'

      export default {
        // ...
      }
      </script>
```

1.  将`components`选项设置为向模板公开一些组件，使用一个对象（键是我们将在模板中使用的名称，值是组件定义）：

```js
      export default {
        components: {
          Movie,
          // Equivalent to `Movie: Movie,`
        },

        // ...
      }
```

1.  我们现在可以在模板中使用`Movie`标签使用组件：

```js
      <template>
        <ul class="movies">
          <Movie v-for="movie of movies"
            :key="movie.id"
            :movie="movie" />
        </ul>
      </template>
```

如果您使用 JSX，则不需要`components`选项，因为如果以大写字母开头，可以直接使用组件定义：

```js
import Movies from './Movies.vue'

export default {
  render (h) {
    return <Movies/>
    // no need to register Movies via components option
  }
}
```

# 总结

在本章中，我们安装了几个工具，这些工具将允许我们使用推荐的方法编写真正的生产就绪应用程序。现在，我们可以搭建整个项目骨架来开始构建出色的新应用程序。我们可以以各种方式编写组件，但是我们可以使用单文件组件以一致和可维护的方式进行编写。我们可以在我们的应用程序内部或在其他组件内部使用这些组件来组成我们的用户界面，其中包含多个可重用组件。

在下一章中，我们将使用我们到目前为止学到的所有知识创建我们的第三个应用程序，还有一些新的主题，比如路由！
