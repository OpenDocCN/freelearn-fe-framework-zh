添加 UI 框架

在本章中，我们将指导您安装一些前端 UI 框架到您的 Nuxt 项目中，这些框架将为您的应用模板添加样式。本书中我们选择的框架有 Foundation 用于设计布局，Motion UI 用于创建动画，Less 作为样式表语言，jQuery UI 用于为 DOM 添加动画，AOS 用于在滚动时为内容添加动画，以及 Swiper 用于创建轮播图像。这些框架可以加快 Nuxt 项目的前端开发速度，使其变得有趣且简单。

本章我们将涵盖的主题如下：

+   添加基础和 Motion UI

+   添加 Less（更轻量的样式表）

+   添加 jQuery UI

+   添加 AOS

+   添加 Swiper

# 第三章：添加基础和 Motion UI

Foundation 是一个用于创建响应式网站的前端框架。它提供了用于网格布局、排版、按钮、表格、导航、表单等的 HTML 和 CSS 模板，以及可选的 JavaScript 插件。它适用于任何设备，移动或桌面，并且是 Bootstrap（https://getbootstrap.com/）的另一种流行的前端框架。我们在本书中专注于 Foundation。因此，就像在上一章中一样，当使用`create-nuxt-app`脚手架安装 Nuxt 项目的骨架时，我们有一系列建议的 UI 框架供您选择。我们应该选择`None`，以便我们可以添加 Foundation 作为 UI 框架：

```js
? Choose UI framework (Use arrow keys)
❯ None
  Ant Design Vue
  Bootstrap Vue
  ...
```

一旦您回答了安装过程中的问题，导航到您的项目目录，然后您可以安装并集成 Foundation 到您的 Nuxt 应用程序中。最简单的方法是使用**内容交付网络**（**CDN**），但不鼓励这样做。最简单的原因是，如果您离线开发，CDN 链接将无法工作。此外，您将失去对源文件的控制，因为它们由大型网络公司（如 Google、Microsoft 和 Amazon）处理。但是，如果您想在 Nuxt 项目中使用 CDN 快速启动，只需将 CDN 源添加到 Nuxt 配置文件中的`head`选项中，如下所示：

```js
// nuxt.config.js
export default {
  head: {
    script: [
      { src: 'https://cdn.jsdelivr.net/.../foundation.min.js' },
    ],
    link: [
      { rel: 'stylesheet', href: 
      'https://cdn.jsdelivr.net/.../foundation.min.css' },
    ],
  },
}
```

您可以在官方 Foundation 网站上找到最新的 CDN 链接：https://get.foundation/sites/docs/installation.html#cdn-links。

这很容易，不是吗？但如果您想要在本地托管源文件，这并不理想。让我们在以下步骤中找出与 Nuxt 集成的正确方法：

1.  通过 npm 在终端上安装 Foundation 及其依赖项（jQuery 和 what-input）：

```js
$ npm i foundation-sites
$ npm i jquery
$ npm i what-input
```

1.  从`/node_modules/`文件夹中的 Foundation CSS 源添加到 Nuxt 配置文件中的`css`选项中，如下所示：

```js
// nuxt.config.js
export default {
  css: [
    'foundation-sites/dist/css/foundation.min.css'
  ],
}
```

1.  在`/plugins/`目录中创建一个`foundation.client.js`文件，并添加以下代码：

```js
// plugins/client-only/foundation.client.js
import 'foundation-sites'
```

这个插件将确保 Foundation 仅在客户端运行。我们将在第六章中更详细地介绍插件和模块。

1.  在 Nuxt 配置文件的`plugins`选项中注册上述 Foundation 插件，如下所示：

```js
// nuxt.config.js
export default {
  plugins: [
    '~/plugins/client-only/foundation.client.js',
  ],
}
```

1.  然后，您可以在需要的任何页面中使用 Foundation 的 JavaScript 插件，例如：

```js
// layouts/form.vue
<script>
import $ from 'jquery'

export default {
  mounted () {
    $(document).foundation()
  }
}
</script>
```

就是这样。您已经成功在您的 Nuxt 项目中安装并成功集成了它。现在，让我们在下一节中探讨如何使用 Foundation 创建网格结构布局和网站导航，以加速前端网页开发。

## 使用 Foundation 创建网格布局和网站导航

我们应该首先看一下 Foundation 的网格系统，它被称为 XY Grid。在网页开发中，网格系统是一种将我们的 HTML 元素结构化为基于网格的布局的系统。Foundation 带有我们可以轻松使用的 CSS 类来结构化我们的 HTML 元素，例如：

```js
<div class="grid-x">
  <div class="cell medium-6">left</div>
  <div class="cell medium-6">right</div>
</div
```

这将在大屏幕上（例如 iPad，Windows Surface）将我们的元素响应地结构化为两列，但在小屏幕上（例如 iPhone）将其结构化为单列。让我们在默认的`index.vue`页面和由`create-nuxt-app`脚手架工具生成的`default.vue`布局中创建一个响应式布局和网站导航：

1.  删除`/components/`目录中的`Logo.vue`组件。

1.  删除`/pages/`目录中`index.vue`页面中的`<style>`和`<script>`块，但用以下元素和网格类替换`<template>`块：

```js
// pages/index.vue
<template>
  <div class="grid-x">
    <div class="medium-6 cell">
      <img src="~/assets/images/sample-01.jpg">
    </div>
    <div class="medium-6 cell">
      <img src="~/assets/images/sample-02.jpg">
    </div>
  </div>
</template>
```

在这个模板中，当页面在大屏幕上加载时，图像会并排结构。但当页面调整大小或在小屏幕上加载时，它们会自适应地堆叠在一起。

1.  删除`/layouts/`目录中`default.vue`布局中的`<style>`和`<script>`块，但用以下导航替换`<template>`块：

```js
// layouts/default.vue
<template>
  <div>
    <ul class="menu align-center">
      <li><nuxt-link to="/">Home</nuxt-link></li>
      <li><nuxt-link to="/form">Form</nuxt-link></li>
      <li><nuxt-link to="/motion-ui">Motion UI</nuxt-link></li>
    </ul>
    <nuxt />
  </div>
</template>
```

在这个新布局中，我们只是创建了一个基本的网站水平菜单，其中包含一个填充有三个`<li>`元素和`<nuxt-link>`组件的`<ul>`元素，并通过在`<ul>`元素后添加`.align-center`类将菜单项对齐到中心。

就是这样。现在您拥有一个可以在任何设备上完美运行的具有响应式布局和导航的网站。您可以看到，您可以在不编写任何 CSS 样式的情况下快速完成它。很棒，不是吗？但 JavaScript 呢？Foundation 还附带了一些 JavaScript 实用程序和插件，我们也可以利用它们。让我们在下一节中找出。

有关 Foundation 中 XY 网格和导航的更多信息，请访问[`get.foundation/sites/docs/xy-grid.html`](https://get.foundation/sites/docs/xy-grid.html)和[`get.foundation/sites/docs/menu.html`](https://get.foundation/sites/docs/menu.html)。

## 使用 Foundation 的 JavaScript 实用程序和插件

Foundation 附带许多有用的 JavaScript 实用程序，例如 MediaQuery。此 MediaQuery 实用程序可用于获取应用程序中创建响应式布局所需的屏幕大小断点（小，中，大，超大）。让我们在以下步骤中找出如何使用它：

1.  创建一个`utils.js`文件，将您的自定义全局实用程序保存在`/plugins/`目录中，并添加以下代码：

```js
// plugins/utils.js
import Vue from 'vue'
Vue.prototype.$getCurrentScreenSize = () => {
  window.addEventListener('resize', () => {
    console.log('Current screen size: ' +
     Foundation.MediaQuery.current)
  })
}
```

在这段代码中，我们创建了一个全局插件（即 JavaScript 函数），它将从 MediaQuery 实用程序的`current`属性中获取当前屏幕大小，并在浏览器的屏幕大小更改时记录输出。通过使用 JavaScript 的`EventTarget`方法`addEventListener`，将调整大小事件监听器添加到 window 对象中。然后通过将其命名为`$getCurrentScreenSize`将此插件注入到 Vue 实例中。

1.  在默认布局中调用`$getCurrentScreenSize`函数如下：

```js
// layouts/default.vue
<script>
export default {
  mounted () {
    this.$getCurrentScreenSize()
  }
}
</script>
```

因此，如果您在 Chrome 浏览器上打开控制台选项卡，当您调整屏幕大小时，您应该会看到当前屏幕大小的日志，例如`当前屏幕大小：中等`。

有关 Foundation MediaQuery 和其他实用程序的更多信息，请访问[`get.foundation/sites/docs/javascript-utilities.html#mediaquery`](https://get.foundation/sites/docs/javascript-utilities.html#mediaquery)和[`get.foundation/sites/docs/javascript-utilities.html`](https://get.foundation/sites/docs/javascript-utilities.html)。

有关 JavaScript EventTarget 和 addEventListener 的更多信息，请访问[`developer.mozilla.org/en-US/docs/Web/API/EventTarget`](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget)和[`developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener`](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener)。

除了 JavaScript 实用程序之外，Foundation 还提供了许多 JavaScript 插件，例如 Dropdown Menu 用于创建下拉导航，Abide 用于表单验证，Tooltip 用于在 HTML 页面中显示元素的扩展信息。这些插件可以通过简单地将它们的类名添加到您的元素中来激活。此外，您可以通过编写 JavaScript 来修改和与它们交互，就像我们在本节中向您展示的那样。让我们在以下步骤中看一下 Abide 插件：

1.  创建包含提交按钮和重置按钮的最后一个`<div>`块，如下所示：

```js
// pages/form.vue
<template>
  <form data-abide novalidate>
    <div class="grid-container">
      <div class="grid-x">
        <div class="cell small-12">
          <div data-abide-error class="alert callout" 
           style="display: none;">
            <p><i class="fi-alert"></i> There are errors in your 
             form.</p>
          </div>
        </div>
      </div>
    </div>
    <div class="grid-container">
      <div class="grid-x">
        //...
      </div>
    </div>
  </form>
</template>
```

在这个表单中，第一个网格容器包含一般错误消息，而第二个容器将包含表单输入字段。我们通过向表单元素添加`data-abide`来激活 Abide 插件。我们还向表单元素添加了一个`novalidate`属性，以防止浏览器的本机验证，这样我们就可以将工作交给 Abide 插件。

1.  创建一个包含`.cell`和`.small-12`类的`<div>`块，其中包含一个电子邮件`<input>`元素和两个默认错误消息`<span>`元素，如下所示：

```js
// pages/form.vue
<div class="cell small-12">
  <label>Email (Required)
    <input type="text" placeholder="hello@example.com" required
      pattern="email">
    <span class="form-error" data-form-error-on="required">
      Sorry, this field is required.
    </span>
    <span class="form-error" data-form-error-on="pattern">
      Sorry, invalid Email
    </span>
  </label>
</div>
```

创建两个包含两个`<input>`元素的`<div>`块，用于收集密码，其中第二个密码用于通过在第二个密码`<input>`元素中添加`data-equalto`属性来匹配第一个密码，如下所示：

1.  在`/pages/`目录中创建一个`form.vue`页面，其中包含以下 HTML 元素，以创建包含两个`.grid-container`元素的表单：

```js
// pages/form.vue
<div class="cell small-12">
  <label>Password Required
    <input type="password" placeholder="chewieR2D2" required >
    <span class="form-error">
      Sorry, this field is required.
    </span>
  </label>
</div>
<div class="cell small-12">
  <label>Re-enter Password
    <input type="password" placeholder="chewieR2D2" required
      pattern="alpha_numeric"
      data-equalto="password">
    <span class="form-error">
      Sorry, passwords are supposed to match!
    </span>
  </label>
</div>
```

1.  在这个单元格块中，有三个来自 Foundation 的自定义属性：`pattern`属性用于验证电子邮件字符串，`data-form-error-on`属性用于显示与`required`和`pattern`属性相应的输入错误，`placeholder`属性用于在输入字段中显示输入提示。请注意，`required`属性是 HTML5 的默认属性。

```js
// pages/form.vue
<div class="cell small-12">
  <button class="button" type="submit" value="Submit">Submit</button>
  <button class="button" type="reset" value="Reset">Reset</button>
</div>
```

1.  在 Vue 组件挂载时，在`<script>`块中初始化 Foundation JavaScript 插件：

```js
// pages/form.vue
<script>
import $ from 'jquery'

export default {
  mounted () {
    $(document).foundation()
  }
}
</script>
```

就是这样。不需要编写任何 JavaScript，只需添加带有类和属性的 HTML 元素，就可以创建一个漂亮的前端表单验证。这非常有用！

有关 Foundation 中 Abide 插件的更多信息，请访问[`get.foundation/sites/docs/abide.html`](https://get.foundation/sites/docs/abide.html)。

除了 JavaScript 实用程序和插件外，Zurb Foundation 还有一些有用的库，我们可以从中受益：Motion UI 用于创建 Sass/CSS 动画，Panini 用于使用可重用部分创建页面和布局，Style Sherpa 用于为代码库创建样式指南。我们将在下一节中探讨如何使用 Motion UI 创建 CSS 动画和过渡。让我们找出！

## 使用 Motion UI 创建 CSS 动画和过渡

Motion UI 是 Zurb Foundation 的一个方便的 Sass 库，用于快速创建 CSS 过渡和动画。您可以从 Motion UI 网站下载 Starter Kit 并进行调试，但这缺乏自己的控制，因为它带有许多内置的默认值和效果，您必须遵循。因此，如果您想要更多的控制并充分利用 Motion UI，您必须知道如何自定义和编译 Sass 代码。让我们在以下步骤中找出如何编写您的 Sass 动画：

1.  通过 npm 在终端上安装 Motion UI 及其依赖项（Sass 和 Sass loader）：

```js
$ npm i motion-ui --save-dev
$ npm i node-sass --save-dev
$ npm i sass-loader --save-dev
```

1.  在`/assets/`目录中的`/css/`文件夹中创建一个`main.scss`文件，并按以下方式导入 Motion UI：

```js
// assets/scss/main.scss
@import 'motion-ui/src/motion-ui';
@include motion-ui-transitions;
@include motion-ui-animations;
```

1.  随后是自定义 CSS 动画如下：

```js
// assets/scss/main.scss
.welcome {
  @include mui-animation(fade);
  animation-duration: 2s;
}
```

1.  在 Nuxt 配置文件的`css`选项中注册自定义 Motion UI CSS 资源：

```js
// nuxt.config.js
export default {
  css: [
    'assets/scss/main.scss'
  ]
}
```

1.  通过使用其类名将动画应用于任何元素，例如：

```js
// pages/index.vue
<img class="welcome" src="~/assets/images/sample-01.jpg">
```

然后，您应该看到上述图像在页面加载时逐渐淡入需要 2 秒钟的时间。

Motion UI 还提供了两个公共函数，我们可以与其交互以触发其内置动画和过渡：`animationIn`和`animateOut`。让我们在以下步骤中找出如何使用它们：

1.  在`/plugins/`目录中创建一个`motion-ui.client.js`文件，其中包含以下代码：

```js
// plugins/client-only/motion-ui.client.js
import Vue from 'vue'
import MotionUi from 'motion-ui'
Vue.prototype.$motionUi = MotionUi
```

此插件将确保 Motion UI 仅在客户端运行。我们将在第六章中更详细地介绍插件和模块的内容。

1.  在 Nuxt 配置文件的`plugins`选项中注册上述 Motion UI 插件如下：

```js
// nuxt.config.js
export default {
  plugins: [
    '~/plugins/client-only/motion-ui.client.js',
  ],
}
```

1.  在模板中随意使用 Motion UI 函数，例如：

```js
// pages/motion-ui.vue
<template>
  <h1 data-animation="spin-in">Hello Motion UI</h1>
</template>

<script>
import $ from 'jquery'

export default {
  mounted () {
    $('h1').click(function() {
      var $animation = $('h1').data('animation')
      this.$motionUi.animateIn($('h1'), $animation)
    })
  }
}
</script>
```

在此页面中，我们将过渡名称`spin-in`存储在元素的`data`属性中，然后将其传递给 Motion UI 的`animateIn`函数，在元素被点击时应用动画。请注意，我们使用 jQuery 从`data`属性中获取数据。

如果您想了解其余内置过渡名称，请访问[`get.foundation/sites/docs/motion-ui.html#built-in-transitions`](https://get.foundation/sites/docs/motion-ui.html#built-in-transitions)。

这很酷，不是吗？如果您需要在元素上使用 CSS 动画或过渡，而又不想自己编写大量的 CSS 代码，这将非常方便。这可以使您的 CSS 样式保持简洁，并专注于模板的主要和自定义呈现。说到节省时间和不必亲自编写通用代码，还值得一提的是 Zurb Foundation 提供的常用图标字体——Foundation Icon Font 3。让我们在下一节中了解一下您可以从中受益的方式。

有关 Motion UI 的更多信息，请访问[`get.foundation/sites/docs/motion-ui.html`](https://get.foundation/sites/docs/motion-ui.html)。至于 Panini 和 Style Sherpa，请访问[`get.foundation/sites/docs/panini.html`](https://get.foundation/sites/docs/panini.html)和[`get.foundation/sites/docs/style-sherpa.html`](https://get.foundation/sites/docs/style-sherpa.html)。

## 使用 Foundation Icon Fonts 3 添加图标

Foundation Icon Fonts 3 是我们可以在前端开发项目中使用的有用图标字体集之一。它可以帮助您避免自己创建常见的图标，例如社交媒体图标（Facebook、Twitter、YouTube）、箭头图标（向上箭头、向下箭头等）、辅助功能图标（轮椅、电梯等）、电子商务图标（购物车、信用卡等）和文本编辑器图标（加粗、斜体等）。让我们在以下步骤中了解如何在您的 Nuxt 项目中安装它：

1.  通过 npm 安装 Foundation Icon Fonts 3：

```js
$ npm i foundation-icon-fonts
```

1.  在 Nuxt 配置文件中全局添加 Foundation Icon Fonts 的路径：

```js
// nuxt.config.js
export default {
  css: [
    'foundation-icon-fonts/foundation-icons.css',
  ]
}
```

1.  使用图标名称前缀为`fi`的任何`<i>`元素应用图标，例如：

```js
<i class="fi-heart"></i>
```

您可以在[`zurb.com/playground/foundation-icon-fonts-3`](https://zurb.com/playground/foundation-icon-fonts-3)找到其余图标名称。

干得好！在本节和之前关于将 Foundation 添加到您的 Nuxt 项目的章节中，您已经成功地使用了网格系统来构建您的布局，并使用 Sass 创建了 CSS 动画。但是，添加网格系统和编写 CSS 动画还不足以构建一个应用程序；我们需要特定的 CSS 来描述 Nuxt 应用程序中 HTML 文档和 Vue 页面的呈现。我们可以在整个项目中使用 Sass 来创建无法仅通过使用 Foundation 完成的自定义样式，但让我们尝试另一种流行的样式预处理器并将其添加到您的 Nuxt 项目中——**Less**。让我们在下一节中找出。

您可以在我们的 GitHub 存储库的`/chapter-3/nuxt-universal/adding-foundation/`中找到到目前为止学到的所有示例代码。

# 添加 Less（**Leaner Style Sheets**）

Less 代表 Leaner Style Sheets，是 CSS 的语言扩展。它看起来就像 CSS，因此非常容易学习。Less 只对 CSS 语言进行了一些方便的添加，这也是它可以被迅速学习的原因之一。您可以在使用 Less 编写 CSS 时使用变量、mixin、嵌套、嵌套 at-rules 和冒泡、操作、函数等等；例如，以下是变量的样子：

```js
@width: 10px;
@height: @width + 10px;
```

这些变量可以像其他编程语言中的变量一样使用；例如，您可以在普通的 CSS 中以以下方式使用前面的变量：

```js
#header {
  width: @width;
  height: @height;
}
```

上述代码将转换为以下 CSS，我们的浏览器将理解：

```js
#header {
  width: 10px;
  height: 20px;
}
```

这非常简单和整洁，不是吗？在 Nuxt 中，您可以通过在`<style>`块中使用`lang`属性来将 Less 作为您的 CSS 预处理器使用：

```js
<style lang="less">
</style>
```

如果您想要将本地样式应用于特定页面或布局，这种方式是很好和可管理的。您应该在`lang`属性之前添加一个`scoped`属性，以便本地样式仅在特定页面上本地应用，并且不会干扰其他页面的样式。但是，如果您有多个页面和布局共享一个公共样式，那么您应该在项目的`/assets/`目录中全局创建样式。因此，让我们看看您如何在以下步骤中使用 Less 创建全局样式：

1.  通过终端在 npm 上安装 Less 及其 webpack 加载器：

```js
$ npm i less --save-dev
$ npm i less-loader --save-dev
```

1.  在`/assets/`目录中创建一个`main.less`文件，并添加以下样式：

```js
// assets/less/main.less @borderWidth: 1px;
@borderStyle: solid;

.cell {
  border: @borderWidth @borderStyle blue;
}

.row {
  border: @borderWidth @borderStyle red;
}

```

1.  在 Nuxt 配置文件中安装上述全局样式如下：

```js
// nuxt.config.js
export default {
  css: [
    'assets/less/main.less'
  ]
}
```

1.  例如，在项目的任何地方应用上述样式：

```js
// pages/index.vue
<template>
  <div class="row">
    <div class="grid-x">
      <div class="medium-6 cell">
        <img class="welcome" src="~/assets/images/sample-01.jpg">
      </div>
      <div class="medium-6 cell">
        <img class="welcome" src="~/assets/images/sample-02.jpg">
      </div>
    </div>
  </div>
</template>
```

当你在浏览器上启动应用程序时，你应该看到刚刚添加到 CSS 类的边框。这些边框在开发布局时可以作为指南，因为网格系统下面的网格线是“不可见的”，没有可见的线可能很难将它们可视化。

你可以在我们的 GitHub 存储库的`/chapter-3/nuxt-universal/adding-less/`中找到上述代码。

由于我们在本节中涵盖了 CSS 预处理器，值得一提的是我们可以在`<style>`块、`<template>`块或`<script>`块中使用任何预处理器，例如：

+   如果你想用 CoffeeScript 编写 JavaScript，可以按照以下步骤进行：

```js
<script lang="coffee">
export default data: ->
  { message: 'hello World' }
</script>
```

有关 CoffeeScript 的更多信息，请访问[`coffeescript.org/`](https://coffeescript.org/)。

+   如果你想在 Nuxt 中使用 Pug 编写 HTML 标签，可以按照以下步骤进行：

```js
<template lang="pug">
  h1.blue Greet {{ message }}!
</template>
```

有关 Pug 的更多信息，请访问[`pugjs.org/`](https://pugjs.org/)。

+   如果你想使用 Sass（Syntactically Awesome Style Sheets）或 Scss（Sassy Cascaded Style Sheets）来编写 CSS 样式，可以按照以下步骤进行：

```js
<style lang="sass">
.blue
  color: blue
</style>

<style lang="scss">
.blue {
  color: blue;
}
</style>
```

有关 Sass 和 Scss 的更多信息，请访问[`sass-lang.com/`](https://sass-lang.com/)。

在本书中，我们在各章节中主要使用 Less、原生 HTML 和 JavaScript（主要是 ECMAScript 6 或 ECMAScript 2015）。但你可以自由选择任何我们提到的预处理器。现在让我们来看看在 Nuxt 项目中为 HTML 元素添加效果和动画的另一种方法——jQuery UI。

# 添加 jQuery UI

jQuery UI 是建立在 jQuery 之上的一组用户界面（UI）交互、效果、小部件和实用工具。它对设计师和开发人员都是一个有用的工具。与 Motion UI 和 Foundation 一样，jQuery UI 可以帮助你用更少的代码在项目中做更多事情。它可以通过使用 CDN 资源和以 jQuery 为依赖项轻松地添加到普通 HTML 页面中，例如：

```js
<script src="https://code.jquery.com/jquery-3.5.1.min.js"></script>
<script src="https://code.jquery.com/ui/1.12.1/jquery-ui.js"></script>
<link rel="stylesheet" href="https://code.jquery.com/ui/1.12.1/themes/base/jquery-ui.css">

<div id="accordion">...</div>

<script>
  $('#accordion').accordion()
</script>
```

再次强调，与 Foundation 一样。当你想要将 jQuery UI 与 Nuxt 集成时会有一些复杂。我们可以使用上述 CDN 资源，并将它们添加到 Nuxt 配置文件中的`head`选项中，如下所示：

```js
// nuxt.config.js
export default {
  head: {
    script: [
      { src: 'https://cdnjs.cloudflare.com/.../jquery.min.js' },
      { src: 'https://code.jquery.com/.../jquery-ui.js' },
    ],
    link: [
      { rel: 'stylesheet', href: 
       'https://code.jquery.com/.../jquery-ui.css' },
    ]
  }
}
```

但是，就像与 Foundation 集成一样，不鼓励这样做。以下是正确的做法：

1.  在终端上通过 npm 安装 jQuery UI：

```js
$ npm i jquery-ui-bundle
```

1.  将 jQuery UI 的 CSS 源文件从`/node_modules/`文件夹添加到 Nuxt 配置文件的`css`选项中：

```js
// nuxt.config.js
module.exports = {
  css: [
    'jquery-ui-bundle/jquery-ui.min.css'
  ]
}
```

1.  在`/plugins/`目录中创建一个名为`jquery-ui-bundle.js`的文件，并按以下方式导入 jQuery UI：

```js
// plugins/client-only/jquery-ui-bundle.client.js
import 'jquery-ui-bundle'
```

再次强调，此插件将确保 jQuery UI 仅在客户端上运行，并且我们将在第六章中更详细地介绍插件和模块的内容。

1.  在 Nuxt 配置文件的`plugins`选项中注册前面的 jQuery UI 插件，如下所示：

```js
// nuxt.config.js
export default {
  plugins: [
    '~/plugins/client-only/jquery-ui-bundle.client.js',
  ],
}
```

1.  现在您可以在任何地方使用 jQuery UI，例如：

```js
// pages/index.vue
<template>
  <div id="accordion">...</div>
</template>

<script>
import $ from 'jquery'

export default {
  mounted () {
    $('#accordion').accordion()
  }
}
</script>
```

在此示例中，我们使用了 jQuery UI 的一个小部件 Accordion 来显示可折叠的内容面板。您可以在[`jqueryui.com/accordion/`](https://jqueryui.com/accordion/)找到 HTML 代码的详细信息。

除了小部件，jQuery UI 还带有动画缓动效果等效果。让我们看看如何在以下步骤中使用缓动效果创建动画：

1.  在`/pages/`目录中创建一个名为`animate.vue`的新页面，并在`<template>`块中添加以下元素：

```js
// pages/animate.vue
<h1>Hello jQuery UI</h1>
```

1.  在`<template>`块中使用 jQuery 的`animate`函数和 jQuery UI 的缓动效果创建动画，如下所示：

```js
// pages/animate.vue
import $ from 'jquery'

export default {
  mounted () {
    var state = true
    $('h1').on('click', function() {
      if (state) {
        $(this).animate({
          color: 'red', fontSize: '10em'
        }, 1000, 'easeInQuint', () => {
          console.log('easing in done')
        })
      } else {
        $(this).animate({
          color: 'black', fontSize: '2em'
        }, 1000, 'easeOutExpo', () => {
          console.log('easing out done')
        })
      }
      state = !state
    })
  }
}
```

在此代码中，当单击元素时，我们使用`easeInQuint`缓动效果，再次单击时使用`easeOutExpo`缓动效果。单击时，元素的字体大小从`2em`变化到`10em`，再次单击时从`10em`变化到`2em`。对于文本颜色也是一样，当单击元素时，它在`red`和`black`之间进行动画变化。

1.  刷新您的浏览器，您应该会看到我们已经将动画和缓动效果应用到`H1`上。

有关更多缓动效果，请访问[`api.jqueryui.com/easings/`](https://api.jqueryui.com/easings/)，有关 jQuery 动画函数的更多信息，请访问[`api.jquery.com/animate/`](https://api.jquery.com/animate/)。

如果您想了解 jQuery UI 的其他效果、小部件和实用工具，请访问[`jqueryui.com/`](https://jqueryui.com/)。

尽管您可以使用 CSS 使用 Motion UI 创建动画和过渡效果，但是 jQuery UI 是另一种选项，可以使用 JavaScript 对 HTML 元素应用动画。除了 jQuery 和 jQuery UI 之外，还有其他 JavaScript 库，我们可以从中受益，以特定方式交互地和有趣地呈现我们的内容，例如在向上或向下滚动页面时对内容进行动画处理，以及从左侧或右侧滑入内容。我们将在接下来的部分中了解这两个动画工具，即 AOS 和 Swiper。让我们在下一节中进行。

您可以在我们的 GitHub 存储库的`/chapter-3/nuxt-universal/adding-jquery-ui/`中找到本节中使用的所有代码。

# 添加 AOS

AOS 是一个 JavaScript 动画库，可以在您向下（或向上）滚动页面时将 DOM 元素美观地动画显示出来。这是一个小型库，非常容易使用，可以在滚动页面时触发动画，而无需自己编写代码。要对元素进行动画处理，只需使用`data-aos`属性：

```js
<div data-aos="fade-in">...</div>
```

就像这样简单，当您滚动页面时，元素将逐渐淡入。您甚至可以设置动画完成的持续时间。因此，让我们找出如何在以下步骤中将此库添加到您的 Nuxt 项目中：

1.  在终端上通过 npm 安装 AOS：

```js
$ npm i aos
```

1.  将以下元素添加到`index.vue`中：

```js
// pages/index.vue
<template>
  <div class="grid-x">
    <div class="medium-6 medium-offset-3 cell" data-aos="fade-up">
      <img src="~/assets/images/sample-01.jpg">
    </div>
    <div class="medium-6 medium-offset-3 cell" data-aos="fade-up">
      <img src="~/assets/images/sample-02.jpg">
    </div>
    <div class="medium-6 medium-offset-3 cell" data-aos="fade-up">
      <img src="~/assets/images/sample-03.jpg">
    </div>
  </div>
</template>
```

在此模板中，我们使用 Foundation 为元素添加网格结构，并通过使用`data-aos`属性在每个元素上应用 AOS `fade-up`动画。

1.  在`<script>`块中导入 AOS JavaScript 和 CSS 资源，并在 Vue 组件挂载时初始化 AOS：

```js
// pages/index.vue
<script>
import 'aos/dist/aos.css'
import aos from 'aos'

export default {
  mounted () {
    aos.init()
  }
}
</script>
```

当您刷新屏幕时，您应该看到元素逐个向上淡入，按顺序出现，就像您向下滚动页面一样。这样可以让您如此轻松地美观地呈现您的内容，是不是很棒？

然而，我们刚刚应用 AOS 的方式并不适合如果您还有其他页面需要进行动画处理。您需要将前面的脚本复制到需要 AOS 动画的每个页面上。因此，如果您有多个页面需要使用 AOS 进行动画处理，那么您应该进行全局注册和初始化。让我们在以下步骤中找出如何做到这一点：

1.  在`/plugins/`目录中创建一个`aos.client.js`插件，导入 AOS 资源，并初始化 AOS 如下：

```js
// plugins/client-only/aos.client.js
import 'aos/dist/aos.css'
import aos from 'aos'

aos.init({
  duration: 2000,
})
```

在这个插件中，我们指示 AOS 全局地花费 2 秒来动画化我们的元素。您可以在 https://github.com/michalsnik/aos#1-initialize-aos 找到其余的设置选项。

1.  在 Nuxt 配置文件的`plugins`选项中注册前面的 AOS 插件如下：

```js
// nuxt.config.js
module.exports = {
  plugins: [
    '~/plugins/client-only/aos.client.js',
  ],
}
```

就是这样。现在您可以将 AOS 动画应用于多个页面，而无需复制脚本。

请注意，我们在 AOS 插件中直接导入 CSS 资源，而不是通过 Nuxt 配置文件中的`css`选项全局导入，与我们在以前的部分中为 Foundation 和 Motion UI 所做的相反。因此，如果您想为 Foundation 做同样的事情，可以直接将其 CSS 资源导入到插件文件中，如下所示：

```js
// plugins/client-only/foundation-site.client.js
import 'foundation-sites/dist/css/foundation.min.css'
import 'foundation-sites'
```

然后，您无需在 Nuxt 配置文件中使用全局的`css`选项。如果您希望保持配置文件“轻量”并将 UI 框架的 CSS 和 JavaScript 资源保留在其插件文件中，这种方式是首选。

您可以在我们的 GitHub 存储库的`/chapter-3/nuxt-universal/adding-aos/`中找到此示例 Nuxt 应用程序的源代码。

如果您想了解有关 AOS 和其余动画名称的更多信息，请访问 https://michalsnik.github.io/aos/。

现在让我们探索最后一个 JavaScript 助手，可以帮助加速您的前端开发 - **Swiper**。

# 添加 Swiper

Swiper 是一个 JavaScript 触摸滑块，可用于现代 Web 应用程序（桌面或移动）和移动本机或混合应用程序。它是 Framework7（https://framework7.io/）和 Ionic Framework（https://ionicframework.com/）的一部分，用于构建移动混合应用程序。我们可以像在以前的部分中使用 CDN 资源一样轻松地为 Web 应用程序设置 Swiper。但让我们看看您如何在以下步骤中以正确的方式在 Nuxt 中安装和使用它：

1.  在您的 Nuxt 项目中通过终端使用 npm 安装 Swiper：

```js
$ npm i swiper
```

1.  添加以下 HTML 元素以在`<template>`块中创建图像滑块：

```js
// pages/index.vue
<template>
  <div class="swiper-container">
    <div class="swiper-wrapper">
      <div class="swiper-slide"><img 
       src="~/assets/images/sample-01.jpg">
      </div>
      <div class="swiper-slide"><img 
       src="~/assets/images/sample-02.jpg">
      </div>
      <div class="swiper-slide"><img 
       src="~/assets/images/sample-03.jpg">
      </div>
    </div>
    <div class="swiper-button-next"></div>
    <div class="swiper-button-prev"></div>
  </div>
</template>
```

从这些元素中，我们希望创建一个图像滑块，其中包含三个图像，可以从左侧或右侧滑入视图，以及两个按钮 - 下一个按钮和上一个按钮。

1.  在`<script>`块中导入 Swiper 资源并在页面挂载时创建一个新的 Swiper 实例：

```js
// pages/index.vue
<script>
import 'swiper/swiper-bundle.css'
import Swiper from 'swiper/bundle'

export default {
  mounted () {
    var swiper = new Swiper('.swiper-container', {
      navigation: {
        nextEl: '.swiper-button-next',
        prevEl: '.swiper-button-prev',
      },
    })
  }
}
</script>
```

在这个脚本中，我们向 Swiper 提供了我们图像滑块的类名，以便可以初始化一个新实例。此外，我们通过 Swiper 的`pagination`选项将下一个和上一个按钮注册到新实例。

您可以在[`swiperjs.com/api/`](https://swiperjs.com/api/)找到用于初始化 Swiper 和与实例交互的 API 的其余设置选项。

1.  在`<style>`块中添加以下 CSS 样式来自定义图像滑块：

```js
// pages/index.vue
<style>
  .swiper-container {
    width: 100%;
    height: 100%;
  }
  .swiper-slide {
    display: flex;
    justify-content: center;
    align-items: center;
  }
</style>
```

在这个样式中，我们只想通过在 CSS 的`width`和`height`属性上使用 100%，并通过使用 CSS 的`flex`属性将图像置于滑块容器中央，使幻灯片占据整个屏幕。

1.  现在，您可以运行 Nuxt 并在浏览器中加载页面，您应该会看到一个交互式图像滑块很好地工作。

您可以在 Swiper 官方网站[`swiperjs.com/demos/`](https://swiperjs.com/demos/)找到一些很棒的示例幻灯片。

请注意，我们刚刚使用的 Swiper 方式仅适用于单个页面。如果您想在多个页面上创建滑块，则可以通过插件全局注册 Swiper。因此，让我们在以下步骤中了解如何做到这一点：

1.  在`/plugins/`目录中创建一个名为`swiper.client.js`的插件，导入 Swiper 资源，并创建一个名为`$swiper`的属性。将 Swiper 附加到此属性，并将其注入到 Vue 实例中，如下所示：

```js
// plugins/client-only/swiper.client.js
import 'swiper/swiper-bundle.css'
import Vue from 'vue'
import Swiper from 'swiper/bundle'

Vue.prototype.$swiper = Swiper
```

1.  在 Nuxt 配置文件的`plugins`选项中注册此 Swiper 插件：

```js
// nuxt.config.js
export default {
  plugins: [
    '~/plugins/client-only/swiper.client.js',
  ],
}
```

1.  现在，您可以通过使用`this`关键字调用`$swiper`属性，在应用的多个页面中创建 Swiper 的新实例，例如：

```js
// pages/global.vue
<script>
export default {
  mounted () {
    var swiper = new this.$swiper('.swiper-container', { ... })
  }
}
</script>
```

同样，我们将 CSS 资源组织在插件文件中，而不是通过 Nuxt 配置文件中的`css`选项全局注册它。但是，如果您想要全局覆盖这些 UI 框架和库中的一些样式，那么通过在`css`选项中全局注册它们的 CSS 资源，然后在`/assets/`目录中存储的 CSS 文件中添加自定义样式，更容易覆盖它们。

您可以从我们的 GitHub 存储库的`/chapter-3/nuxt-universal/adding-swiper/`中下载本章的源代码。如果您想了解更多关于 Swiper 的信息，请访问[`swiperjs.com/`](https://swiperjs.com/)。

干得好！您已经成功掌握了我们为您选择的一些流行的 UI 框架和库，以加速您的前端开发。我们希望它们将对您未来创建的 Nuxt 项目有所帮助。在接下来的章节中，我们将偶尔使用这些框架和库，特别是在最后一章 - [第十八章]《使用 CMS 和 GraphQL 创建 Nuxt 应用》中。现在，让我们总结一下您在本章学到的内容。

# 总结

在本章中，您已经将 Foundation 安装为 Nuxt 项目中的主要 UI 框架，并使用 Foundation 的网格系统、JavaScript 实用程序和插件来创建简单的网格布局、表单和导航。您已经使用 Foundation 的 Motion UI 来创建 Sass 动画和过渡，还使用了 Foundation Icon Fonts 3 来向 HTML 页面添加常见和有用的图标。您已经安装了 Less 作为样式预处理器，并在 Less 样式表中创建了一些变量。

您已经安装了 jQuery UI，并将其手风琴小部件添加到您的应用程序中，并使用其缓动效果创建了动画。您已经安装了 AOS，并在向下或向上滚动页面时使用它来使元素动画进入视口。最后，您已经安装了 Swiper 来创建一个简单的图像幻灯片。最后但同样重要的是，您已经学会了如何通过 Nuxt 配置文件全局安装这些框架和库，或者仅在特定页面上局部使用它们。

在接下来的章节中，我们将介绍 Nuxt 中的视图、路由和过渡。您将创建自定义页面、路由和 CSS 过渡，并学习如何使用`/assets/`目录来提供图像和字体等资源。此外，您还将学习如何自定义默认布局并在`/layouts/`目录中添加新的布局。我们将提供一个简单的网站示例，该示例使用了所有这些 Nuxt 功能，以便您可以从本书中学到的内容中获得具体用途的感觉。因此，让我们在下一章中进一步探索 Nuxt！
