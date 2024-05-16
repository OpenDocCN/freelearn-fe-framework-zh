组件，混合和功能组件

构建 Vue 应用程序就像拼图一样。拼图的每一块都是一个组件，每一块都有一个插槽要填充。

组件在 Vue 开发中扮演着重要的角色。在 Vue 中，您的代码的每一部分都将是一个组件——它可以是布局，页面，容器或按钮，但最终，它都是一个组件。学习如何与它们交互并重用它们是清理代码和提高 Vue 应用性能的关键。组件是最终会在屏幕上呈现出某些东西的代码，无论大小如何。

在本章中，我们将学习如何制作一个可视化组件，可以在许多地方重复使用。我们将使用插槽将数据放入我们的组件中，为了严格快速的渲染，创建功能性组件，实现父子组件之间的直接通信，最后，看看如何异步加载您的组件。

让我们把所有这些部分放在一起，创建一个美丽的拼图，即 Vue 应用程序。

在本章中，我们将涵盖以下配方：

+   创建一个可视化模板组件

+   使用插槽和命名插槽将数据放入您的组件中

+   将数据传递给您的组件并验证数据

+   创建功能性组件

+   访问您的子组件数据

+   创建一个动态注入的组件

+   创建一个依赖注入组件

+   创建一个组件`mixin`

+   延迟加载您的组件

# 第五章：技术要求

在本章中，我们将使用**Node.js**和**Vue-CLI**。

注意 Windows 用户：您需要安装一个名为`windows-build-tools`的 NPM 包，以便能够安装以下所需的包。为此，请以管理员身份打开 PowerShell 并执行以下命令：

> npm install -g windows-build-tools

要安装**Vue-CLI**，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> npm install -g @vue/cli @vue/cli-service-global
```

# 创建一个可视化模板组件

组件可以是数据驱动的，无状态的，有状态的，或者是一个简单的可视化组件。但是什么是可视化组件？可视化组件是一个只有一个目的的组件：可视化操作。

一个可视化组件可以有一个简单的带有一些`div` HTML 元素的作用域 CSS，或者它可以是一个更复杂的组件，可以实时计算元素在屏幕上的位置。

我们将创建一个遵循 Material Design 指南的卡片包装组件。

## 准备工作

此食谱的先决条件如下：

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   `@vue/cli`

+   `@vue/cli-service-global`

## 如何做...

要启动我们的组件，我们可以使用带有 Vue-CLI 的 Vue 项目，就像我们在第二章中的“使用 Vue CLI 创建您的第一个项目”食谱中所做的那样，*介绍 TypeScript 和 Vue 生态系统***，或者我们可以开始一个新的项目。

要启动一个新项目，打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows），并执行以下命令：

```js
> vue create visual-component
```

CLI 将询问一些问题，这些问题将有助于创建项目。您可以使用箭头键导航，*Enter*键继续，*空格键*选择选项。选择`default`选项：

```js
?  Please pick a preset: **(Use arrow keys)** ❯ default (babel, eslint) 
  Manually select features  ‌
```

现在，让我们按照这些步骤创建一个可视化模板组件：

1.  让我们在`src/components`文件夹中创建一个名为`MaterialCardBox.vue`的新文件。

1.  在这个文件中，我们将从组件的模板开始。我们需要为卡片创建一个框。通过使用 Material Design 指南，这个框将有阴影和圆角：

```js
<template>
 <div class="cardBox elevation_2">
 <div class="section">
 This is a Material Card Box
 </div>
 </div>
</template>
```

1.  在我们组件的`<script>`部分中，我们将只添加我们的基本名称：

```js
<script>
  export default {
   name: 'MaterialCardBox',
  };
</script>
```

1.  我们需要创建我们的高程 CSS 样式表规则。为此，请在`style`文件夹中创建一个名为`elevation.css`的文件。在那里，我们将创建从`0`到`24`的高程，以遵循 Material Design 指南上的所有高程：

```js
.elevation_0 {
    border: 1px solid rgba(0, 0, 0, 0.12);
}

.elevation_1 {
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.2),
        0 1px 1px rgba(0, 0, 0, 0.14),
        0 2px 1px -1px rgba(0, 0, 0, 0.12);
}

.elevation_2 {
    box-shadow: 0 1px 5px rgba(0, 0, 0, 0.2),
        0 2px 2px rgba(0, 0, 0, 0.14),
        0 3px 1px -2px rgba(0, 0, 0, 0.12);
}

.elevation_3 {
    box-shadow: 0 1px 8px rgba(0, 0, 0, 0.2),
        0 3px 4px rgba(0, 0, 0, 0.14),
        0 3px 3px -2px rgba(0, 0, 0, 0.12);
}

.elevation_4 {
    box-shadow: 0 2px 4px -1px rgba(0, 0, 0, 0.2),
        0 4px 5px rgba(0, 0, 0, 0.14),
        0 1px 10px rgba(0, 0, 0, 0.12);
}

.elevation_5 {
    box-shadow: 0 3px 5px -1px rgba(0, 0, 0, 0.2),
        0 5px 8px rgba(0, 0, 0, 0.14),
        0 1px 14px rgba(0, 0, 0, 0.12);
}

.elevation_6 {
    box-shadow: 0 3px 5px -1px rgba(0, 0, 0, 0.2),
        0 6px 10px rgba(0, 0, 0, 0.14),
        0 1px 18px rgba(0, 0, 0, 0.12);
}

.elevation_7 {
    box-shadow: 0 4px 5px -2px rgba(0, 0, 0, 0.2),
        0 7px 10px 1px rgba(0, 0, 0, 0.14),
        0 2px 16px 1px rgba(0, 0, 0, 0.12);
}

.elevation_8 {
    box-shadow: 0 5px 5px -3px rgba(0, 0, 0, 0.2),
        0 8px 10px 1px rgba(0, 0, 0, 0.14),
        0 3px 14px 2px rgba(0, 0, 0, 0.12);
}

.elevation_9 {
    box-shadow: 0 5px 6px -3px rgba(0, 0, 0, 0.2),
        0 9px 12px 1px rgba(0, 0, 0, 0.14),
        0 3px 16px 2px rgba(0, 0, 0, 0.12);
}

.elevation_10 {
    box-shadow: 0 6px 6px -3px rgba(0, 0, 0, 0.2),
        0 10px 14px 1px rgba(0, 0, 0, 0.14),
        0 4px 18px 3px rgba(0, 0, 0, 0.12);
}

.elevation_11 {
    box-shadow: 0 6px 7px -4px rgba(0, 0, 0, 0.2),
        0 11px 15px 1px rgba(0, 0, 0, 0.14),
        0 4px 20px 3px rgba(0, 0, 0, 0.12);
}

.elevation_12 {
    box-shadow: 0 7px 8px -4px rgba(0, 0, 0, 0.2),
        0 12px 17px 2px rgba(0, 0, 0, 0.14),
        0 5px 22px 4px rgba(0, 0, 0, 0.12);
}

.elevation_13 {
    box-shadow: 0 7px 8px -4px rgba(0, 0, 0, 0.2),
        0 13px 19px 2px rgba(0, 0, 0, 0.14),
        0 5px 24px 4px rgba(0, 0, 0, 0.12);
}

.elevation_14 {
    box-shadow: 0 7px 9px -4px rgba(0, 0, 0, 0.2),
        0 14px 21px 2px rgba(0, 0, 0, 0.14),
        0 5px 26px 4px rgba(0, 0, 0, 0.12);
}

.elevation_15 {
    box-shadow: 0 8px 9px -5px rgba(0, 0, 0, 0.2),
        0 15px 22px 2px rgba(0, 0, 0, 0.14),
        0 6px 28px 5px rgba(0, 0, 0, 0.12);
}

.elevation_16 {
    box-shadow: 0 8px 10px -5px rgba(0, 0, 0, 0.2),
        0 16px 24px 2px rgba(0, 0, 0, 0.14),
        0 6px 30px 5px rgba(0, 0, 0, 0.12);
}

.elevation_17 {
    box-shadow: 0 8px 11px -5px rgba(0, 0, 0, 0.2),
        0 17px 26px 2px rgba(0, 0, 0, 0.14),
        0 6px 32px 5px rgba(0, 0, 0, 0.12);
}

.elevation_18 {
    box-shadow: 0 9px 11px -5px rgba(0, 0, 0, 0.2),
        0 18px 28px 2px rgba(0, 0, 0, 0.14),
        0 7px 34px 6px rgba(0, 0, 0, 0.12);
}

.elevation_19 {
    box-shadow: 0 9px 12px -6px rgba(0, 0, 0, 0.2),
        0 19px 29px 2px rgba(0, 0, 0, 0.14),
        0 7px 36px 6px rgba(0, 0, 0, 0.12);
}

.elevation_20 {
    box-shadow: 0 10px 13px -6px rgba(0, 0, 0, 0.2),
        0 20px 31px 3px rgba(0, 0, 0, 0.14),
        0 8px 38px 7px rgba(0, 0, 0, 0.12);
}

.elevation_21 {
    box-shadow: 0 10px 13px -6px rgba(0, 0, 0, 0.2),
        0 21px 33px 3px rgba(0, 0, 0, 0.14),
        0 8px 40px 7px rgba(0, 0, 0, 0.12);
}

.elevation_22 {
    box-shadow: 0 10px 14px -6px rgba(0, 0, 0, 0.2),
        0 22px 35px 3px rgba(0, 0, 0, 0.14),
        0 8px 42px 7px rgba(0, 0, 0, 0.12);
}

.elevation_23 {
    box-shadow: 0 11px 14px -7px rgba(0, 0, 0, 0.2),
        0 23px 36px 3px rgba(0, 0, 0, 0.14),
        0 9px 44px 8px rgba(0, 0, 0, 0.12);
}

.elevation_24 {
    box-shadow: 0 11px 15px -7px rgba(0, 0, 0, 0.2),
        0 24px 38px 3px rgba(0, 0, 0, 0.14),
        0 9px 46px 8px rgba(0, 0, 0, 0.12);
}
```

1.  为了在组件的`<style>`部分中设置样式，我们需要在`<style>`标签内设置`scoped`属性，以确保视觉样式不会干扰应用程序中的任何其他组件。我们将使这张卡遵循 Material Design 指南。我们需要导入`Roboto`字体系列并将其应用于将包装在此组件内的所有元素：

```js
<style scoped>
  @import url('https://fonts.googleapis.com/css?family=Roboto:400,500,700&display=swap');
  @import '../style/elevation.css';

  *{
    font-family: 'Roboto', sans-serif;
  }
  .cardBox{
      width: 100%;
  max-width: 300px;
    background-color: #fff;
    position: relative;
    display: inline-block;
    border-radius: 0.25rem;
  }
  .cardBox > .section {
    padding: 1rem;
    position: relative;
  }
</style>
```

1.  要运行服务器并查看您的组件，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> npm run serve
```

这是您的组件呈现并运行的方式：

![](img/5f9be750-4b22-4898-a28a-c02bba7912d9.png)

## 它是如何工作的...

可视化组件是一个将包装任何组件并使用自定义样式放置包装数据的组件。由于此组件与其他组件混合，它可以形成一个新的组件，而无需在代码中重新应用或重写任何样式。

## 参见

您可以在[`vue-loader.vuejs.org/guide/scoped-css.html#child-component-root-elements`](https://vue-loader.vuejs.org/guide/scoped-css.html#child-component-root-elements)找到有关作用域 CSS 的更多信息。

您可以在[`material.io/components/cards/`](https://material.io/components/cards/)找到有关 Material Design 卡片的更多信息。

在[`fonts.google.com/specimen/Roboto`](https://fonts.google.com/specimen/Roboto)上查看 Roboto 字体系列。

# 使用插槽和命名插槽在组件中放置数据

有时候，拼图的一些部分会丢失，你会发现自己有一个空白的地方。想象一下，你可以用自己制作的一块填充那个空白的地方，而不是原来随拼图盒子一起的那块。这是 Vue 插槽的一个粗略类比。

Vue 插槽就像是组件中的开放空间，其他组件可以用文本、HTML 元素或其他 Vue 组件填充。您可以在组件中声明插槽的位置和行为方式。

使用这种技术，您可以创建一个组件，并在需要时轻松自定义它。

## 准备工作

这个配方的先决条件如下：

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   `@vue/cli`

+   `@vue/cli-service-global`

## 如何做...

要开始我们的组件，我们可以像在第二章的*使用 Vue CLI 创建您的第一个项目*中那样使用 Vue-CLI 创建我们的 Vue 项目，或者使用*创建可视化模板组件*中的项目。

按照以下说明在组件中创建插槽和命名插槽：

1.  让我们打开组件文件夹中的名为`MaterialCardBox.vue`的文件。

1.  在组件的`<template>`部分，我们需要在卡片上添加四个主要部分。这些部分基于 Material Design 卡片解剖学，分别是`header`、`media`、`main section`和`action`区域。我们将使用默认插槽来放置`main section`，其余部分都将是命名作用域。对于一些命名插槽，我们将添加一个备用配置，如果用户没有在插槽上选择任何设置，将显示该配置：

```js
<template>
  <div class="cardBox elevation_2">
    <div class="header">
      <slot
        v-if="$slots.header"
        name="header"
      />
      <div v-else>
        <h1 class="cardHeader cardText">
          Card Header
        </h1>
        <h2 class="cardSubHeader cardText">
          Card Sub Header
        </h2>
      </div>
    </div>
    <div class="media">
      <slot
        v-if="$slots.media"
        name="media"
      />
      <img
        v-else
        src="https://via.placeholder.com/350x250"
      >
    </div>
    <div
      v-if="$slots.default"
      class="section cardText"
      :class="{
        noBottomPadding: $slots.action,
        halfPaddingTop: $slots.media,
      }"
    >
      <slot />
    </div>
    <div
      v-if="$slots.action"
      class="action"
    >
      <slot name="action" />
    </div>
  </div>
</template>
```

1.  现在，我们需要为组件创建文本 CSS 样式表规则。在`style`文件夹中，创建一个名为`cardStyles.css`的新文件，在那里我们将添加卡片文本和标题的规则：

```js
h1, h2, h3, h4, h5, h6{
    margin: 0;
}
.cardText{
    -moz-osx-font-smoothing: grayscale;
    -webkit-font-smoothing: antialiased;
    text-decoration: inherit;
    text-transform: inherit;
    font-size: 0.875rem;
    line-height: 1.375rem;
    letter-spacing: 0.0071428571em;
}
h1.cardHeader{
    font-size: 1.25rem;
    line-height: 2rem;
    font-weight: 500;
    letter-spacing: .0125em;
}
h2.cardSubHeader{
    font-size: .875rem;
    line-height: 1.25rem;
    font-weight: 400;
    letter-spacing: .0178571429em;
    opacity: .6;
}
```

1.  在组件的`<style>`部分，我们需要创建一些 CSS 样式表来遵循我们的设计指南的规则：

```js
<style scoped>
@import url("https://fonts.googleapis.com/css?family=Roboto:400,500,700&display=swap");
@import "../style/elevation.css";
@import "../style/cardStyles.css";

* {
  font-family: "Roboto", sans-serif;
}

.cardBox {
  width: 100%;
  max-width: 300px;
  border-radius: 0.25rem;
  background-color: #fff;
  position: relative;
  display: inline-block;
  box-shadow: 0 1px 5px rgba(0, 0, 0, 0.2), 0 2px 2px rgba(0, 0, 0, 0.14),
    0 3px 1px -2px rgba(0, 0, 0, 0.12);
}
.cardBox > .header {
  padding: 1rem;
  position: relative;
  display: block;
}
.cardBox > .media {
  overflow: hidden;
  position: relative;
  display: block;
  max-width: 100%;
}
.cardBox > .section {
  padding: 1rem;
  position: relative;
  margin-bottom: 1.5rem;
  display: block;
}
.cardBox > .action {
  padding: 0.5rem;
  position: relative;
  display: block;
}
.cardBox > .action > *:not(:first-child) {
  margin-left: 0.4rem;
}
.noBottomPadding {
  padding-bottom: 0 !important;
}
.halfPaddingTop {
  padding-top: 0.5rem !important;
}
</style>
```

1.  在`src`文件夹中的`App.vue`文件中，我们需要向这些插槽添加元素。这些元素将被添加到每个命名插槽和默认插槽中。我们将更改文件的`<template>`部分中的组件。要添加命名插槽，我们需要使用一个名为`v-slot:`的指令，然后是我们想要使用的插槽的名称：

```js
<template>
  <div id="app">
    <MaterialCardBox>
      <template v-slot:header>
        <strong>Card Title</strong><br>
        <span>Card Sub-Title</span>
      </template>
      <template v-slot:media>
        <img src="https://via.placeholder.com/350x150">
      </template>
      <p>Main Section</p>
      <template v-slot:action>
        <button>Action Button</button>
        <button>Action Button</button>
      </template>
    </MaterialCardBox>
  </div>
</template>
```

对于默认插槽，我们不需要使用指令；它只需要包装在组件中，以放置在组件的`<slot />`部分中。

1.  要运行服务器并查看您的组件，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> npm run serve
```

这是您的组件渲染并运行的方式：

![](img/82443046-abca-438a-9a1a-0be3390fecda.png)

## 它是如何工作的...

插槽是可以放置任何可以呈现到 DOM 中的地方。我们选择插槽的位置，并告诉组件在接收到任何信息时在何处呈现。

在这个教程中，我们使用了命名插槽，它们旨在与需要多个插槽的组件一起使用。要在 Vue 单文件（`.vue`）的`<template>`部分中向该组件放置任何信息，您需要添加`v-slot:`指令，以便 Vue 能够知道在何处放置传递下来的信息。

## 另请参阅

您可以在[`vuejs.org/v2/guide/components-slots.html`](https://vuejs.org/v2/guide/components-slots.html)找到有关 Vue 插槽的更多信息。

您可以在[`material.io/components/cards/#anatomy`](https://material.io/components/cards/#anatomy)找到有关 Material Design 卡片解剖的更多信息。

# 向您的组件传递数据并验证数据

您现在知道如何通过插槽将数据放入组件中，但这些插槽是为 HTML DOM 元素或 Vue 组件而设计的。有时，您需要传递诸如字符串、数组、布尔值甚至对象之类的数据。

整个应用程序就像一个拼图，其中每个部分都是一个组件。组件之间的通信是其中的重要部分。向组件传递数据的可能性是连接拼图的第一步，然后验证数据是连接这些部分的最后一步。

在这个教程中，我们将学习如何向组件传递数据并验证传递给组件的数据。

## 准备工作

先决条件如下：

+   Node.js 12+

Node.js 所需的全局对象如下：

+   `@vue/cli`

+   `@vue/cli-service-global`

## 如何做...

要启动我们的组件，我们可以像在第二章“介绍 TypeScript 和 Vue 生态系统”中的*使用 Vue CLI 创建您的第一个项目*食谱中那样，使用 Vue-CLI 创建我们的 Vue 项目，或者使用*使用插槽和命名插槽将数据放入组件*食谱中的项目。

按照这些说明将数据传递给组件并进行验证：

1.  让我们在`src/components`文件夹中打开名为`MaterialCardBox.vue`的文件。

1.  在组件的`<script>`部分，我们创建一个名为`props`的新属性。该属性接收组件数据，该数据可以用于视觉操作、代码内的变量或需要执行的函数。在此属性中，我们需要声明属性的名称、类型、是否必需以及验证函数。此函数将在运行时执行，以验证传递的属性是否有效：

```js
<script>
export default {
  name: 'MaterialCardBox',
  inheritAttrs: false,
  props: {
    header: {
      type: String,
      required: false,
      default: '',
      validator: v => typeof v === 'string',
    },
    subHeader: {
      type: String,
      required: false,
      default: '',
      validator: v => typeof v === 'string',
    },
    mainText: {
      type: String,
      required: false,
      default: '',
      validator: v => typeof v === 'string',
    },
    showMedia: {
      type: Boolean,
      required: false,
      default: false,
      validator: v => typeof v === 'boolean',
    },
    imgSrc: {
      type: String,
      required: false,
      default: '',
      validator: v => typeof v === 'string',
    },
    showActions: {
      type: Boolean,
      required: false,
      default: false,
      validator: v => typeof v === 'boolean',
    },
    elevation: {
      type: Number,
      required: false,
      default: 2,
      validator: v => typeof v === 'number',
    },
  },
  computed: {},
};
</script>
```

1.  在组件的`<script>`部分的`computed`属性中，我们需要创建一组用于呈现卡片的视觉操作规则。这些规则将是`showMediaContent`、`showActionsButtons`、`showHeader`和`cardElevation`。每个规则将检查接收到的`props`和`$slots`对象，以查看是否需要呈现相关的卡片部分：

```js
  computed: {
    showMediaContent() {
      return (this.$slots.media || this.imgSrc) && this.showMedia;
    },
    showActionsButtons() {
      return this.showActions && this.$slots.action;
    },
    showHeader() {
      return this.$slots.header || (this.header || this.subHeader);
    },
    showMainContent() {
      return this.$slots.default || this.mainText;
    },
    cardElevation() {
      return `elevation_${parseInt(this.elevation, 10)}`;
    },
  },
```

1.  在添加了视觉操作规则之后，我们需要将创建的规则添加到组件的`<template>`部分。它们将影响我们卡片的外观和行为。例如，如果没有定义头部插槽，并且定义了头部属性，我们将显示备用头部。该头部是通过`props`传递下来的数据：

```js
<template>
  <div
    class="cardBox"
    :class="cardElevation"
  >
    <div
      v-if="showHeader"
      class="header"
    >
      <slot
        v-if="$slots.header"
        name="header"
      />
      <div v-else>
        <h1 class="cardHeader cardText">
          {{ header }}
        </h1>
        <h2 class="cardSubHeader cardText">
          {{ subHeader }}
        </h2>
      </div>
    </div>
    <div
      v-if="showMediaContent"
      class="media"
    >
      <slot
        v-if="$slots.media"
        name="media"
      />
      <img
        v-else
        :src="imgSrc"
      >
    </div>
    <div
      v-if="showMainContent"
      class="section cardText"
      :class="{
        noBottomPadding: $slots.action,
        halfPaddingTop: $slots.media,
      }"
    >
      <slot v-if="$slots.default" />
      <p
        v-else
        class="cardText"
      >
        {{ mainText }}
      </p>
    </div>
    <div
      v-if="showActionsButtons"
      class="action"
    >
      <slot
        v-if="$slots.action"
        name="action"
      />
    </div>
  </div>
</template>
```

1.  要运行服务器并查看您的组件，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> npm run serve
```

这是您的组件呈现并运行：

![](img/ab464f57-6b57-4caa-b845-afec0d64684c.png)

## 工作原理...

每个 Vue 组件都是一个具有渲染函数的 JavaScript 对象。当需要在 HTML DOM 中呈现它时，将调用此渲染函数。单文件组件是该对象的抽象。

当我们声明我们的组件具有可以传递的唯一 props 时，它为其他组件或 JavaScript 打开了一个小门，以便将信息放入我们的组件中。然后，我们可以在组件内使用这些值来渲染数据，进行一些计算或制定可视规则。

在我们的情况下，使用单文件组件，我们将这些规则作为 HTML 属性传递，因为 `vue-template-compiler` 将获取这些属性并将它们转换为 JavaScript 对象。

当这些值传递给我们的组件时，Vue 首先检查传递的属性是否与正确的类型匹配，然后我们在每个值上执行我们的验证函数，以查看它是否与我们期望的匹配。

完成所有这些后，组件的生命周期将继续，我们可以渲染我们的组件。

## 另请参阅

您可以在 [`vuejs.org/v2/guide/components-props.html`](https://vuejs.org/v2/guide/components-props.html) 找到有关 `props` 的更多信息。

您可以在 [`vue-loader.vuejs.org/guide/`](https://vue-loader.vuejs.org/guide/) 找到有关 `vue-template-compiler` 的更多信息。

# 创建功能组件

功能组件的美妙之处在于它们的简单性。它们是无状态组件，没有任何数据、计算属性，甚至没有生命周期。它们只是在传递的数据发生变化时调用的渲染函数。

您可能想知道这有什么用。嗯，功能组件是 UI 组件的完美伴侣，这些组件不需要在内部保留任何数据，或者只是渲染组件，不需要任何数据操作的可视组件。

顾名思义，它们是简单的函数组件，除了渲染函数外没有其他内容。它们是组件的精简版本，专门用于性能渲染和可视元素。

## 准备工作

先决条件如下：

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   `@vue/cli`

+   `@vue/cli-service-global`

## 如何做...

要启动我们的组件，请使用 Vue-CLI 创建您的 Vue 项目，就像我们在第二章“引入 TypeScript 和 Vue 生态系统”中的食谱“*使用 Vue CLI 创建您的第一个项目*”中所做的那样，或者使用“将数据传递给您的组件并验证数据”的食谱中的项目。

现在，按照以下说明创建一个 Vue 功能组件：

1.  在`src/components`文件夹中创建一个名为`MaterialButton.vue`的新文件。

1.  在这个组件中，我们需要验证我们将接收的 prop 是否是有效的颜色。为此，在项目中安装`is-color`模块。您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> npm install --save is-color
```

1.  在我们组件的`<script>`部分，我们需要创建功能组件将接收的`props`对象。由于功能组件只是一个没有状态的渲染函数，`<script>`部分被简化为`props`、`injections`和`slots`。将有四个`props`对象：`backgroundColor`、`textColor`、`isRound`和`isFlat`。在安装组件时，这些不是必需的，因为我们在`props`中定义了默认值：

```js
<script>
  import isColor from 'is-color';

  export default {
    name: 'MaterialButton',
    props: {
      backgroundColor: {
        type: String,
        required: false,
        default: '#fff',
        validator: v => typeof v === 'string' && isColor(v),
      },
      textColor: {
        type: String,
        required: false,
        default: '#000',
        validator: v => typeof v === 'string' && isColor(v),
      },
      isRound: {
        type: Boolean,
        required: false,
        default: false,
      },
      isFlat: {
        type: Boolean,
        required: false,
        default: false,
      },
    },
  };
</script>
```

1.  在我们组件的`<template>`部分，我们首先需要向`<template>`标签添加`functional`属性，以指示`vue-template-compiler`这个组件是一个功能组件。我们需要创建一个按钮 HTML 元素，带有基本的`class`属性按钮和一个基于`props`对象接收的动态`class`属性。与普通组件不同，我们需要指定`props`属性以使用功能组件。对于按钮的样式，我们需要创建一个基于`props`的动态`style`属性。为了直接将所有事件监听器传递给父组件，我们可以调用`v-on`指令并传递`listeners`属性。这将绑定所有事件监听器，而无需声明每一个。在按钮内部，我们将添加一个用于视觉增强的`div` HTML 元素，并添加`<slot>`，文本将放置在其中：

```js
<template functional>
  <button
    tabindex="0"
    class="button"
    :class="{
      round: props.isRound,
      isFlat: props.isFlat,
    }"
    :style="{
      background: props.backgroundColor,
      color: props.textColor
    }"
    v-on="listeners"
  >
    <div
      tabindex="-1"
      class="button_focus_helper"
    />
    <slot/>
  </button>
</template>
```

1.  现在，让我们把它弄得漂亮一点。在组件的`<style>`部分，我们需要为这个按钮创建所有的 CSS 样式表规则。我们需要向`<style>`添加`scoped`属性，以便所有的 CSS 样式表规则不会影响我们应用程序中的任何其他元素：

```js
<style scoped>
  .button {
    user-select: none;
    position: relative;
    outline: 0;
    border: 0;
    border-radius: 0.25rem;
    vertical-align: middle;
    cursor: pointer;
    padding: 4px 16px;
    font-size: 14px;
    line-height: 1.718em;
    text-decoration: none;
    color: inherit;
    background: transparent;
    transition: 0.3s cubic-bezier(0.25, 0.8, 0.5, 1);
    min-height: 2.572em;
    font-weight: 500;
    text-transform: uppercase;
  }
  .button:not(.isFlat){
    box-shadow: 0 1px 5px rgba(0, 0, 0, 0.2),
    0 2px 2px rgba(0, 0, 0, 0.14),
    0 3px 1px -2px rgba(0, 0, 0, 0.12);
  }

  .button:not(.isFlat):focus:before,
  .button:not(.isFlat):active:before,
  .button:not(.isFlat):hover:before {
    content: '';
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    border-radius: inherit;
    transition: 0.3s cubic-bezier(0.25, 0.8, 0.5, 1);
  }

  .button:not(.isFlat):focus:before,
  .button:not(.isFlat):active:before,
  .button:not(.isFlat):hover:before {
    box-shadow: 0 3px 5px -1px rgba(0, 0, 0, 0.2),
    0 5px 8px rgba(0, 0, 0, 0.14),
    0 1px 14px rgba(0, 0, 0, 0.12);
  }

  .button_focus_helper {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    pointer-events: none;
    border-radius: inherit;
    outline: 0;
    opacity: 0;
    transition: background-color 0.3s cubic-bezier(0.25, 0.8, 0.5, 1),
    opacity 0.4s cubic-bezier(0.25, 0.8, 0.5, 1);
  }

  .button_focus_helper:after, .button_focus_helper:before {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    opacity: 0;
    border-radius: inherit;
    transition: background-color 0.3s cubic-bezier(0.25, 0.8, 0.5, 1),
    opacity 0.6s cubic-bezier(0.25, 0.8, 0.5, 1);
  }

  .button_focus_helper:before {
    background: #000;
  }

  .button_focus_helper:after {
    background: #fff;
  }

  .button:focus .button_focus_helper:before,
  .button:hover .button_focus_helper:before {
    opacity: .1;
  }

  .button:focus .button_focus_helper:after,
  .button:hover .button_focus_helper:after {
    opacity: .6;
  }

  .button:focus .button_focus_helper,
  .button:hover .button_focus_helper {
    opacity: 0.2;
  }

  .round {
    border-radius: 50%;
  }
</style>
```

1.  运行服务器并查看您的组件，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> npm run serve
```

这是您的组件渲染并运行的地方：

![](img/da07bacd-c897-42be-9dc2-536e8ae144c6.png)

## 它是如何工作的...

功能组件就像一个渲染函数一样简单。它们没有任何类型的数据、函数或者对外部世界的访问。

它们最初作为 JavaScript 对象`render()`函数在 Vue 中引入；后来，它们被添加到了`vue-template-compiler`中，用于 Vue 单文件应用程序。

功能组件通过接收两个参数来工作：`createElement`和`context`。正如我们在单文件中看到的，我们只能访问元素，因为它们不在 JavaScript 对象的`this`属性中。这是因为当上下文传递给渲染函数时，就没有`this`属性。

功能组件在 Vue 上提供了最快的渲染速度，因为它不依赖于组件的生命周期来检查渲染；它只是在数据更改时每次渲染。

## 另请参阅

您可以在[`vuejs.org/v2/guide/render-function.html#Functional-Components`](https://vuejs.org/v2/guide/render-function.html#Functional-Components)找到有关功能组件的更多信息。

您可以在[`www.npmjs.com/package/is-color`](https://www.npmjs.com/package/is-color)找到有关`is-color`模块的更多信息。

# 访问您的子组件数据

通常，父子通信是通过事件或 props 来完成的。但有时，您需要访问存在于子组件或父组件函数中的数据、函数或计算属性。

Vue 提供了一种双向交互的方式，打开了通信和事件的大门，例如 props 和事件监听器。

还有另一种访问组件之间数据的方式：通过直接访问。这可以通过在单文件组件中使用模板时使用特殊属性，或者在 JavaScript 中直接调用对象来完成。这种方法被一些人认为有点懒惰，但有时确实没有其他方法可以做到这一点。

## 准备工作

先决条件如下：

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   `@vue/cli`

+   `@vue/cli-service-global`

## 如何做...

要启动您的组件，请使用 Vue-CLI 创建您的 Vue 项目，就像我们在第二章的'*使用 Vue CLI 创建您的第一个项目*'食谱中所做的那样，*介绍 TypeScript 和 Vue 生态系统*，或者使用'*创建功能组件*'食谱中的项目。

我们将把这个教程分成四个部分。前三部分将涵盖新组件的创建——`StarRatingInput`、`StarRatingDisplay`和`StarRating`——最后一部分将涵盖数据和函数访问的父子直接操作。

### 创建星级评分输入

我们将创建一个基于五星级评分系统的星级评分输入。

按照以下步骤创建自定义星级评分输入：

1.  在`src/components`文件夹中创建一个名为`StarRatingInput.vue`的新文件。

1.  在组件的`<script>`部分，在`props`属性中创建一个`maxRating`属性，它是一个数字，非必需，并且默认值为`5`。在`data`属性中，我们需要创建我们的`rating`属性，其默认值为`0`。在`methods`属性中，我们需要创建三个方法：`updateRating`、`emitFinalVoting`和`getStarName`。`updateRating`方法将保存评分到数据中，`emitFinalVoting`将调用`updateRating`并通过`final-vote`事件将评分传递给父组件，`getStarName`将接收一个值并返回星级的图标名称。

```js
<script>
export default {
  name: 'StarRatingInput',
  props: {
    maxRating: {
      type: Number,
      required: false,
      default: 5,
    },
  },
  data: () => ({
    rating: 0,
  }),
  methods: {
    updateRating(value) {
      this.rating = value;
    },
    emitFinalVote(value) {
      this.updateRating(value);
      this.$emit('final-vote', this.rating);
    },
    getStarName(rate) {
      if (rate <= this.rating) {
        return 'star';
      }
      if (Math.fround((rate - this.rating)) < 1) {
        return 'star_half';
      }
      return 'star_border';
    },
  },
};
</script>
```

1.  在组件的`<template>`部分，我们需要创建一个`<slot>`组件来放置星级评分之前的文本。我们将根据通过`props`属性接收到的`maxRating`值创建一个动态星级列表。创建的每个星级都将在`mouseenter`、`focus`和`click`事件上附加一个监听器。当触发`mouseenter`和`focus`时，将调用`updateRating`方法，而`click`将调用`emitFinalVote`方法。

```js
<template>
  <div class="starRating">
    <span class="rateThis">
      <slot />
    </span>
    <ul>
      <li
        v-for="rate in maxRating"
        :key="rate"
        @mouseenter="updateRating(rate)"
        @click="emitFinalVote(rate)"
        @focus="updateRating(rate)"
      >
        <i class="material-icons">
          {{ getStarName(rate) }}
        </i>
      </li>
    </ul>
  </div>
</template>
```

1.  我们需要将 Material Design 图标导入我们的应用程序。在`styles`文件夹中创建一个名为`materialIcons.css`的新样式文件，并添加`font-family`的 CSS 样式规则。

```js
@font-face {
  font-family: 'Material Icons';
  font-style: normal;
  font-weight: 400;
  src: url(https://fonts.gstatic.com/s/materialicons/v48/flUhRq6tzZclQEJ-
      Vdg-IuiaDsNcIhQ8tQ.woff2) format('woff2');
}

.material-icons {
  font-family: 'Material Icons' !important;
  font-weight: normal;
  font-style: normal;
  font-size: 24px;
  line-height: 1;
  letter-spacing: normal;
  text-transform: none;
  display: inline-block;
  white-space: nowrap;
  word-wrap: normal;
  direction: ltr;
  -webkit-font-feature-settings: 'liga';
  -webkit-font-smoothing: antialiased;
}
```

1.  打开`main.js`文件，并将创建的样式表导入其中。`css-loader`将处理 JavaScript 文件中导入的`.css`文件的处理。这将有助于开发，因为您不需要在其他地方重新导入文件。

```js
import Vue from 'vue';
import App from './App.vue';
import './style/materialIcons.css';

Vue.config.productionTip = false;

new Vue({
  render: h => h(App),
}).$mount('#app');
```

1.  为了给我们的组件添加样式，我们将在`src/style`文件夹中创建一个名为`starRating.css`的通用样式文件。在那里，我们将添加`StarRatingDisplay`和`StarRatingInput`组件之间共享的通用样式。

```js
.starRating {
  user-select: none;
  display: flex;
  flex-direction: row;
}
.starRating * {
  line-height: 0.9rem;
}
.starRating .material-icons {
  font-size: .9rem !important;
  color: orange;
}

ul {
  display: inline-block;
  padding: 0;
  margin: 0;
}

ul > li {
  list-style: none;
  float: left;
}
```

1.  在组件的`<style>`部分，我们需要创建所有的 CSS 样式表规则。然后，在位于`src/components`文件夹中的`StarRatingInput.vue`组件文件上，我们需要向`<style>`添加`scoped`属性，以便所有的 CSS 样式表规则不会影响应用程序中的任何其他元素。在这里，我们将导入我们创建的通用样式，并为输入添加新样式：

```js
<style scoped>
  @import '../style/starRating.css';

  .starRating {
    justify-content: space-between;
  }

  .starRating * {
    line-height: 1.7rem;
  }

  .starRating .material-icons {
    font-size: 1.6rem !important;
  }

  .rateThis {
    display: inline-block;
    color: rgba(0, 0, 0, .65);
    font-size: 1rem;
  }
</style>
```

1.  要运行服务器并查看您的组件，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> npm run serve
```

这是您的组件呈现并运行的样子：

![](img/18715858-a5b0-4fa7-97db-d68268fd190d.png)

### 创建 StarRatingDisplay 组件

现在我们有了输入，我们需要一种方法来向用户显示所选的选择。按照以下步骤创建`StarRatingDisplay`组件：

1.  在`src/components`文件夹中创建一个名为`StarRatingDisplay.vue`的新组件。

1.  在组件的`<script>`部分，在`props`属性中，我们需要创建三个新属性：`maxRating`，`rating`和`votes`。它们三个都将是数字，非必需的，并且有默认值。在`methods`属性中，我们需要创建一个名为`getStarName`的新方法，它将接收一个值并返回星星的图标名称：

```js
<script>
export default {
  name: 'StarRatingDisplay',
  props: {
    maxRating: {
      type: Number,
      required: false,
      default: 5,
    },
    rating: {
      type: Number,
      required: false,
      default: 0,
    },
    votes: {
      type: Number,
      required: false,
      default: 0,
    },
  },
  methods: {
    getStarName(rate) {
      if (rate <= this.rating) {
        return 'star';
      }
      if (Math.fround((rate - this.rating)) < 1) {
        return 'star_half';
      }
      return 'star_border';
    },
  },
};
</script>
```

1.  在`<template>`中，我们需要根据通过`props`属性接收到的`maxRating`值创建一个动态星星列表。在列表之后，我们需要显示我们收到的投票数，如果我们收到任何投票，我们也会显示它们：

```js
<template>
  <div class="starRating">
    <ul>
      <li
        v-for="rate in maxRating"
        :key="rate"
      >
        <i class="material-icons">
          {{ getStarName(rate) }}
        </i>
      </li>
    </ul>
    <span class="rating">
      {{ rating }}
    </span>
    <span
      v-if="votes"
      class="votes"
    >
      ({{ votes }})
    </span>
  </div>
</template>
```

1.  在组件的`<style>`部分，我们需要创建所有的 CSS 样式表规则。我们需要向`<style>`添加`scoped`属性，以便所有的 CSS 样式表规则不会影响应用程序中的任何其他元素。在这里，我们将导入我们创建的通用样式，并为显示添加新样式：

```js
<style scoped>
  @import '../style/starRating.css';

  .rating, .votes {
    display: inline-block;
    color: rgba(0,0,0, .65);
    font-size: .75rem;
    margin-left: .4rem;
  }
</style>
```

1.  要运行服务器并查看您的组件，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> npm run serve
```

这是您的组件呈现并运行的样子：

![](img/b65b034f-a304-4975-8858-2bd46e378bce.png)

### 创建 StarRating 组件

创建输入和显示后，我们需要将两者合并到一个单独的组件中。这个组件将是我们在应用程序中使用的最终组件。

按照以下步骤创建最终的`StarRating`组件：

1.  在`src/components`文件夹中创建一个名为`StarRating.vue`的新文件。

1.  在组件的`<script>`部分，我们需要导入`StarRatingDisplay`和`StarRatingInput`组件。在`props`属性中，我们需要创建三个新属性：`maxRating`，`rating`和`votes`。它们三个都将是数字，非必需的，并且有一个默认值。在`data`属性中，我们需要创建我们的`rating`属性，其默认值为`0`，并且一个名为`voted`的属性，其默认值为`false`。在`methods`属性中，我们需要添加一个名为`vote`的新方法，它将接收`rank`作为参数。它将把`rating`定义为接收到的值，并将`voted`组件的内部变量定义为`true`：

```js
<script>
import StarRatingInput from './StarRatingInput.vue';
import StarRatingDisplay from './StarRatingDisplay.vue';

export default {
  name: 'StarRating',
  components: { StarRatingDisplay, StarRatingInput },
  props: {
    maxRating: {
      type: Number,
      required: false,
      default: 5,
    },
    rating: {
      type: Number,
      required: false,
      default: 0,
    },
    votes: {
      type: Number,
      required: false,
      default: 0,
    },
  },
  data: () => ({
    rank: 0,
    voted: false,
  }),
  methods: {
    vote(rank) {
      this.rank = rank;
      this.voted = true;
    },
  },
};
</script>
```

1.  在`<template>`部分，我们将放置两个组件，显示评分的输入：

```js
<template>
  <div>
    <StarRatingInput
      v-if="!voted"
      :max-rating="maxRating"
      @final-vote="vote"
    >
      Rate this Place
    </StarRatingInput>
    <StarRatingDisplay
      v-else
      :max-rating="maxRating"
      :rating="rating || rank"
      :votes="votes"
    />
  </div>
</template>
```

### 子组件上的数据操作

现在我们所有的组件都准备好了，我们需要将它们添加到我们的应用程序中。基本应用程序将访问子组件，并将评分设置为 5 星。

现在，按照以下步骤来理解和操作子组件中的数据：

1.  在`App.vue`文件中，在组件的`<template>`部分，删除`MaterialCardBox`组件的`main-text`属性，并将其放置为组件的默认插槽。

1.  在放置的文本之前，我们将添加`StarRating`组件。我们将为其添加一个`ref`属性。此属性将指示 Vue 将此组件直接链接到组件的`this`对象中的一个特殊属性。在操作按钮中，我们将为点击事件添加监听器——一个用于`resetVote`，另一个用于`forceVote`。

```js
<template>
  <div id="app">
    <MaterialCardBox
      header="Material Card Header"
      sub-header="Card Sub Header"
      show-media
      show-actions
      img-src="https://picsum.photos/300/200"
    >
      <p>
        <StarRating
          ref="starRating"
        />
      </p>
      <p>
        The path of the righteous man is beset on all sides by the 
           iniquities of the selfish and the tyranny of evil men.
      </p>
      <template v-slot:action>
        <MaterialButton
          background-color="#027be3"
          text-color="#fff"
          @click="resetVote"
        >
          Reset
        </MaterialButton>
        <MaterialButton
          background-color="#26a69a"
          text-color="#fff"
          is-flat
          @click="forceVote"
        >
          Rate 5 Stars
        </MaterialButton>
      </template>
    </MaterialCardBox>
  </div>
</template>
```

1.  在组件的`<script>`部分，我们将创建一个`methods`属性，并添加两个新方法：`resetVote`和`forceVote`。这些方法将访问`StarRating`组件并重置数据或将数据设置为 5 星投票：

```js
<script>
import MaterialCardBox from './components/MaterialCardBox.vue';
import MaterialButton from './components/MaterialButton.vue';
import StarRating from './components/StarRating.vue';

export default {
  name: 'App',
  components: {
    StarRating,
    MaterialButton,
    MaterialCardBox,
  },
  methods: {
    resetVote() {
      this.$refs.starRating.rank = 0;
      this.$refs.starRating.voted = false;
    },
    forceVote() {
      this.$refs.starRating.rank = 5;
      this.$refs.starRating.voted = true;
    },
  },
};
</script>
```

## 它是如何工作的...

当`ref`属性添加到组件时，Vue 会将对所引用元素的链接添加到 JavaScript 的`this`属性对象内的`$refs`属性中。从那里，您可以完全访问组件。

这种方法通常用于操作 HTML DOM 元素，而无需调用文档查询选择器函数。

然而，此属性的主要功能是直接访问 Vue 组件，使您能够执行函数并查看组件的计算属性、变量和更改的变量，就像从外部完全访问组件一样。

## 还有更多...

与父组件可以访问子组件的方式相同，子组件可以通过在`this`对象上调用`$parent`来访问父组件。事件可以通过调用`$root`属性来访问 Vue 应用程序的根元素。

## 另请参阅

您可以在[`vuejs.org/v2/guide/components-edge-cases.html#Accessing-the-Parent-Component-Instance`](https://vuejs.org/v2/guide/components-edge-cases.html#Accessing-the-Parent-Component-Instance)找到有关父子通信的更多信息。

# 创建动态注入组件

有些情况下，您的组件可以由您收到的变量类型或数据类型来定义；然后，您需要在不需要设置大量 Vue `v-if`、`v-else-if`和`v-else`指令的情况下即时更改组件。

在这些情况下，最好的做法是使用动态组件，当计算属性或函数可以定义要呈现的组件时，并且决定是实时进行的。

如果有两个响应，这些决策有时可能很简单，但在长的 switch case 中可能会更复杂，其中可能有一长串可能要使用的组件。

## 准备工作

先决条件如下：

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   `@vue/cli`

+   ``@vue/cli-service-global``

## 如何做...

要启动我们的组件，我们可以使用 Vue-CLI 创建我们的 Vue 项目，就像我们在第二章中的'*使用 Vue CLI 创建您的第一个项目*'配方中所做的那样，*介绍 TypeScript 和 Vue 生态系统*，或者使用'*访问您的子组件数据*'配方中的项目。

按照以下步骤创建动态注入组件：

1.  打开`StarRating.vue`组件。

1.  在组件的`<script>`部分，我们需要创建一个带有名为`starComponent`的新计算值的`computed`属性。此值将检查用户是否已投票。如果他们没有，它将返回`StarRatingInput`组件；否则，它将返回`StarRatingDisplay`组件：

```js
<script>
import StarRatingInput from './StarRatingInput.vue';
import StarRatingDisplay from './StarRatingDisplay.vue';

export default {
  name: 'StarRating',
  components: { StarRatingDisplay, StarRatingInput },
  props: {
    maxRating: {
      type: Number,
      required: false,
      default: 5,
    },
    rating: {
      type: Number,
      required: false,
      default: 0,
    },
    votes: {
      type: Number,
      required: false,
      default: 0,
    },
  },
  data: () => ({
    rank: 0,
    voted: false,
  }),
  computed: {
    starComponent() {
      if (!this.voted) return StarRatingInput;
      return StarRatingDisplay;
    },
  },
  methods: {
    vote(rank) {
      this.rank = rank;
      this.voted = true;
    },
  },
};
</script>
```

1.  在组件的`<template>`部分，我们将删除现有组件，并用一个名为`<component>`的特殊组件替换它们。这个特殊组件有一个命名属性，您可以指向任何返回有效 Vue 组件的地方。在我们的例子中，我们将指向计算属性`starComponent`。我们将把从这两个组件中定义的所有绑定 props 放在这个新组件中，包括放在`<slot>`中的文本：

```js
<template>
  <component
    :is="starComponent"
    :max-rating="maxRating"
    :rating="rating || rank"
    :votes="votes"
    @final-vote="vote"
  >
    Rate this Place
  </component>
</template>
```

## 工作原理...

使用 Vue 特殊的`<component>`组件，我们声明了根据计算属性设置的规则应该呈现什么组件。

作为通用组件，您总是需要确保每个可以呈现的组件都存在。最好的方法是使用`v-bind`指令与需要定义的 props 和规则，但也可以直接在组件上定义，因为它将作为 prop 传递下去。

## 另请参阅

您可以在[`vuejs.org/v2/guide/components.html#Dynamic-Components`](https://vuejs.org/v2/guide/components.html#Dynamic-Components)找到有关动态组件的更多信息。

# 创建依赖注入组件

直接从子组件或父组件访问数据而不知道它们是否存在可能非常危险。

在 Vue 中，可以使您的组件的行为像一个接口，并拥有一个在开发过程中不会改变的常见和抽象函数。依赖注入的过程是开发世界中的一个常见范例，并且也已经在 Vue 中实现。

使用内部 Vue 依赖注入有一些利弊，但在开发时，确保子组件知道父组件的期望总是一个好方法。

## 准备工作

先决条件如下：

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   `@vue/cli`

+   `@vue/cli-service-global`

## 如何做...

要启动我们的组件，我们可以像在第二章中的“使用 Vue CLI 创建您的第一个项目”中那样使用 Vue-CLI 创建我们的 Vue 项目，或者使用“创建动态注入组件”中的项目。

现在，按照以下步骤创建一个依赖注入组件：

1.  打开`StarRating.vue`组件。

1.  在组件的`<script>`部分，添加一个名为`provide`的新属性。在我们的情况下，我们将只添加一个键值来检查组件是否是特定组件的子级。在属性中创建一个对象，其中包含`starRating`键和`true`值：

```js
<script>
import StarRatingInput from './StarRatingInput.vue';
import StarRatingDisplay from './StarRatingDisplay.vue';

export default {
  name: 'StarRating',
  components: { StarRatingDisplay, StarRatingInput },
  provide: {
    starRating: true,
  },
  props: {
    maxRating: {
      type: Number,
      required: false,
      default: 5,
    },
    rating: {
      type: Number,
      required: false,
      default: 0,
    },
    votes: {
      type: Number,
      required: false,
      default: 0,
    },
  },
  data: () => ({
    rank: 0,
    voted: false,
  }),
  computed: {
    starComponent() {
      if (!this.voted) return StarRatingInput;
      return StarRatingDisplay;
    },
  },
  methods: {
    vote(rank) {
      this.rank = rank;
      this.voted = true;
    },
  },
};
</script>
```

1.  打开`StarRatingDisplay.vue`文件。

1.  在组件的`<script>`部分，我们将添加一个名为`inject`的新属性。此属性将接收一个名为`starRating`的键的对象，值将是一个具有`default()`函数的对象。如果此组件不是`StarRating`组件的子级，则此函数将记录错误：

```js
<script>
export default {
  name: 'StarRatingDisplay',
  props: {
    maxRating: {
      type: Number,
      required: false,
      default: 5,
    },
    rating: {
      type: Number,
      required: false,
      default: 0,
    },
    votes: {
      type: Number,
      required: false,
      default: 0,
    },
  },
  inject: {
    starRating: {
      default() {
        console.error('StarRatingDisplay need to be a child of 
          StarRating');
      },
    },
  },
  methods: {
    getStarName(rate) {
      if (rate <= this.rating) {
        return 'star';
      }
      if (Math.fround((rate - this.rating)) < 1) {
        return 'star_half';
      }
      return 'star_border';
    },
  },
};
</script>
```

1.  打开`StarRatingInput.vue`文件。

1.  在组件的`<script>`部分，我们将添加一个名为`inject`的新属性。此属性将接收一个名为`starRating`的键的对象，值将是一个具有`default()`函数的对象。如果此组件不是`StarRating`组件的子级，则此函数将记录错误：

```js
<script>
export default {
  name: 'StarRatingInput',
  props: {
    maxRating: {
      type: Number,
      required: false,
      default: 5,
    },
  },
  inject: {
    starRating: {
      default() {
        console.error('StarRatingInput need to be a child of 
          StarRating');
      },
    },
  },
  data: () => ({
    rating: 0,
  }),
  methods: {
    updateRating(value) {
      this.rating = value;
    },
    emitFinalVote(value) {
      this.updateRating(value);
      this.$emit('final-vote', this.rating);
    },
    getStarName(rate) {
      if (rate <= this.rating) {
        return 'star';
      }
      if (Math.fround((rate - this.rating)) < 1) {
        return 'star_half';
      }
      return 'star_border';
    },
  },
};
</script>
```

## 它是如何工作的...

在运行时，Vue 将检查`StarRatingDisplay`和`StarRatingInput`组件中的`starRating`的注入属性，如果父组件未提供此值，则将在控制台上记录错误。

使用组件注入通常用于在绑定组件之间保持共同接口的方式，例如菜单和项目。项目可能需要存储在菜单中的某些功能或数据，或者我们可能需要检查它是否是菜单的子级。

依赖注入的主要缺点是共享元素上不再具有响应性。因此，它主要用于共享功能或检查组件链接。

## 另请参阅

您可以在[`vuejs.org/v2/guide/components-edge-cases.html#Dependency-Injection`](https://vuejs.org/v2/guide/components-edge-cases.html#Dependency-Injection)找到有关组件依赖注入的更多信息。

# 创建一个组件混合

有时您会发现自己一遍又一遍地重写相同的代码。但是，有一种方法可以防止这种情况，并使自己更加高效。

您可以使用所谓的`mixin`，这是 Vue 中的一个特殊代码导入，它将外部代码部分连接到当前组件。

## 准备工作

先决条件如下：

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   `@vue/cli`

+   `@vue/cli-service-global`

## 如何做...

要启动我们的组件，我们可以使用 Vue-CLI 创建我们的 Vue 项目，就像我们在第二章中的'*使用 Vue CLI 创建您的第一个项目*'中所做的那样，或者使用'*创建依赖注入组件*'食谱中的项目。

让我们按照以下步骤创建一个组件`mixin`：

1.  打开`StarRating.vue`组件。

1.  在`<script>`部分，我们需要将`props`属性提取到一个名为`starRatingDisplay.js`的新文件中，我们需要在`mixins`文件夹中创建这个新文件。这个新文件将是我们的第一个`mixin`，并且看起来像这样：

```js
export default {
  props: {
    maxRating: {
      type: Number,
      required: false,
      default: 5,
    },
    rating: {
      type: Number,
      required: false,
      default: 0,
    },
    votes: {
      type: Number,
      required: false,
      default: 0,
    },
  },
};
```

1.  回到`StarRating.vue`组件，我们需要导入这个新创建的文件，并将其添加到一个名为`mixin`的新属性中：

```js
<script>
import StarRatingInput from './StarRatingInput.vue';
import StarRatingDisplay from './StarRatingDisplay.vue';
import StarRatingDisplayMixin from '../mixins/starRatingDisplay';

export default {
  name: 'StarRating',
  components: { StarRatingDisplay, StarRatingInput },
  mixins: [StarRatingDisplayMixin],
  provide: {
    starRating: true,
  },
  data: () => ({
    rank: 0,
    voted: false,
  }),
  computed: {
    starComponent() {
      if (!this.voted) return StarRatingInput;
      return StarRatingDisplay;
    },
  },
  methods: {
    vote(rank) {
      this.rank = rank;
      this.voted = true;
    },
  },
};
</script>
```

1.  现在，我们将打开`StarRatingDisplay.vue`文件。

1.  在`<script>`部分，我们将`inject`属性提取到一个名为`starRatingChild.js`的新文件中，该文件将被创建在`mixins`文件夹中。这将是我们`inject`属性的`mixin`：

```js
export default {
  inject: {
    starRating: {
      default() {
        console.error('StarRatingDisplay need to be a child of 
           StarRating');
      },
    },
  },
};
```

1.  在`StarRatingDisplay.vue`文件中，在`<script>`部分，我们将提取`methods`属性到一个名为`starRatingName.js`的新文件中，该文件将被创建在`mixins`文件夹中。这将是我们`getStarName`方法的`mixin`：

```js
export default {
  methods: {
    getStarName(rate) {
      if (rate <= this.rating) {
        return 'star';
      }
      if (Math.fround((rate - this.rating)) < 1) {
        return 'star_half';
      }
      return 'star_border';
    },
  },
};
```

1.  回到`StarRatingDisplay.vue`文件，我们需要导入这些新创建的文件，并将它们添加到一个名为`mixin`的新属性中：

```js
<script>
import StarRatingDisplayMixin from '../mixins/starRatingDisplay';
import StarRatingNameMixin from '../mixins/starRatingName';
import StarRatingChildMixin from '../mixins/starRatingChild';

export default {
  name: 'StarRatingDisplay',
  mixins: [
    StarRatingDisplayMixin,
    StarRatingNameMixin,
    StarRatingChildMixin,
  ],
};
</script>
```

1.  打开`StarRatingInput.vue`文件。

1.  在`<script>`部分，我们移除`inject`属性，并将`props`属性提取到一个名为`starRatingBase.js`的新文件中，该文件将被创建在`mixins`文件夹中。这将是我们`props`属性的`mixin`：

```js
export default {
  props: {
    maxRating: {
      type: Number,
      required: false,
      default: 5,
    },
    rating: {
      type: Number,
      required: false,
      default: 0,
    },
  },
};
```

1.  回到`StarRatingInput.vue`文件，我们需要将`rating`数据属性重命名为`rank`，并且在`getStarName`方法中，我们需要添加一个新的常量，该常量将接收`rating`属性或`rank`数据。最后，我们需要导入`starRatingChild` `mixin`和`starRatingBase` `mixin`：

```js
<script>
import StarRatingBaseMixin from '../mixins/starRatingBase';
import StarRatingChildMixin from '../mixins/starRatingChild';

export default {
  name: 'StarRatingInput',
  mixins: [
    StarRatingBaseMixin,
    StarRatingChildMixin,
  ],
  data: () => ({
    rank: 0,
  }),
  methods: {
    updateRating(value) {
      this.rank = value;
    },
    emitFinalVote(value) {
      this.updateRating(value);
      this.$emit('final-vote', this.rank);
    },
    getStarName(rate) {
      const rating = (this.rating || this.rank);
      if (rate <= rating) {
        return 'star';
      }
      if (Math.fround((rate - rating)) < 1) {
        return 'star_half';
      }
      return 'star_border';
    },
  },
};
</script>
```

## 它是如何工作的...

`mixins`的工作原理就像对象合并一样，但确保不要用导入的属性替换组件中已经存在的属性。

`mixins`属性的顺序也很重要，因为它们将被检查并作为`for`循环导入，所以最后一个`mixin`不会改变任何祖先的属性。

在这里，我们将我们的代码中的许多重复部分拆分成了四个不同的小 JavaScript 文件，这样更容易维护并提高了生产力，而无需重写代码。

## 另请参阅

您可以在[`vuejs.org/v2/guide/mixins.html`](https://vuejs.org/v2/guide/mixins.html)找到有关 mixins 的更多信息。

# 惰性加载您的组件

`webpack`和 Vue 天生就是一对。当使用`webpack`作为 Vue 项目的打包工具时，可以使组件在需要时或异步加载。这通常被称为惰性加载。

## 准备工作

先决条件如下：

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   `@vue/cli`

+   `@vue/cli-service-global`

## 如何做...

要启动我们的组件，我们可以像在第二章中的'*使用 Vue CLI 创建您的第一个项目*'配方中那样使用 Vue-CLI 创建我们的 Vue 项目，或者使用'*创建组件 mixin*'配方中的项目。

现在，按照以下步骤使用惰性加载技术导入您的组件：

1.  打开`App.vue`文件。

1.  在组件的`<script>`部分，我们将在脚本顶部获取导入并将它们转换为每个组件的惰性加载函数：

```js
<script>
export default {
  name: 'App',
  components: {
    StarRating: () => import('./components/StarRating.vue'),
    MaterialButton: () => import('./components/MaterialButton.vue'),
    MaterialCardBox: () => 
      import('./components/MaterialCardBox.vue'),
  },
  methods: {
    resetVote() {
      this.$refs.starRating.rank = 0;
      this.$refs.starRating.voted = false;
    },
    forceVote() {
      this.$refs.starRating.rank = 5;
      this.$refs.starRating.voted = true;
    },
  },
};
</script>
```

## 它是如何工作的...

当我们声明一个为每个组件返回`import()`函数的函数时，`webpack`知道这个导入函数将进行代码拆分，并且它将使组件成为捆绑包中的一个新文件。

`import()`函数是由 TC39 提出的一个模块加载语法的建议。这个函数的基本功能是异步加载任何声明为模块的文件，避免了在第一次加载时放置所有文件的需要。

## 另请参阅

您可以在[`vuejs.org/v2/guide/components-dynamic-async.html#Async-Components`](https://vuejs.org/v2/guide/components-dynamic-async.html#Async-Components)找到有关异步组件的更多信息。

您可以在[`github.com/tc39/proposal-dynamic-import`](https://github.com/tc39/proposal-dynamic-import)找到有关 TC39 动态导入的更多信息。
