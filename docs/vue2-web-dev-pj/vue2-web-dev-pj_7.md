# 第七章：项目 5 - 在线商店和扩展

在本章中，我们将快速设置一个“时尚商店”应用程序，以便专注于更高级的主题，例如以下内容：

+   改进我们的 CSS 代码与 PostCSS 和 autoprefixer 的兼容性

+   使用 ESLint 对我们的代码进行 linting 以提高其质量和风格

+   对我们的 Vue 组件进行单元测试

+   本地化应用程序并利用 webpack 的代码拆分功能

+   在 Nodejs 中启用应用程序的服务器端渲染

+   为生产构建应用程序

该应用程序将是一个简单的可穿戴在线商店，看起来像这样：

![](img/87d90bd9-c55e-4bfc-8d62-64d4d9722836.png)

# 高级开发工作流程

在本节中，我们将使用新的工具和包来改进我们的开发工作流程。但是，首先，我们需要设置我们的时尚商店项目。

# 设置项目

1.  使用`vue init`命令生成一个新项目，就像我们在第五章中所做的那样，*项目 3 - 支持中心*，以及第六章中所做的那样，*项目 4 - 地理定位博客*：

```js
 vue init webpack-simple e-shop
      cd e-shop
      npm install
      npm install -S babel-polyfill
```

1.  我们还将安装 stylus：

```js
 npm i -D stylus stylus-loader
```

1.  删除`src`文件夹的内容。然后，下载源文件（[`github.com/Akryum/packt-vue-project-guide/tree/master/chapter7-download/src`](https://github.com/Akryum/packt-vue-project-guide/tree/master/chapter7-download/src)）并将其解压缩到`src`文件夹中。这些包含了已经完成的所有应用程序源代码，以便我们可以更快地前进。

1.  我们需要在依赖项中安装一些更多的包：

```js
 npm i -S axios vue-router vuex vuex-router-sync
```

axios 是一个很棒的库，用于向服务器发出请求，并且被 Vue.js 团队推荐使用。

# 生成一个快速开发 API

以前，我们有一个完整的用于后端的 node 服务器，但这次我们不会专注于应用程序功能。因此，我们将使用`json-server`包为本章的目的生成一个非常简单的本地 API：

1.  安装`json-server`作为开发依赖：

```js
 npm i -D json-server
```

1.  当我们运行这个包时，它将在本地公开一个简单的 REST API，并使用`db.json`文件来存储数据。您可以下载它（[`github.com/Akryum/packt-vue-project-guide/blob/master/chapter7-download/db.json`](https://github.com/Akryum/packt-vue-project-guide/blob/master/chapter7-download/db.json)）并将其放在项目根目录中。如果您打开它，您将看到一些待售物品和评论。

1.  然后，我们需要添加一个脚本来启动 json 服务器。在`package.json`文件中添加一个新的`db`脚本：

```js
 "db": "json-server --watch db.json"
```

上述命令将运行`json-server`包的命令行工具，并监视您刚刚下载的`db.json`文件以进行更改，以便您可以轻松编辑它。要尝试它，请使用`npm run`：

```js
npm run db
```

默认情况下，它将监听端口`3000`。您可以通过在浏览器中打开`http://localhost:3000/items` REST 地址来尝试它：

![](img/13a1e3e2-fbe1-426a-a1be-553418aa754d.png)

# 启动应用程序

我们现在准备启动应用程序。打开一个新的终端，像往常一样使用`npm run`：

```js
npm run dev
```

它应该打开一个新的浏览器窗口，显示正确的地址，您应该能够使用该应用程序：

![](img/98d88834-6f0f-41c2-aaca-6b8d287b8682.png)

# 使用 PostCSS 自动添加 CSS 前缀

在编写 CSS（或 Stylus）代码时，我们希望它与大多数浏览器兼容。幸运的是，有一些工具可以自动为我们完成这项工作，例如，通过添加 CSS 属性的供应商前缀版本（例如`-webkit-user-select`和`-moz-user-select`）。

PostCSS 是一个专门用于 CSS 后处理的库。它具有非常模块化的架构；它通过向其中添加处理 CSS 的插件来工作。

我们不必安装它。`vue-loader`已经包含了 PostCSS。我们只需要安装我们想要的插件。在我们的情况下，我们需要`autoprefixer`包来使我们的 CSS 代码与更多浏览器兼容。

1.  安装`autoprefixer`包：

```js
 npm i -D autoprefixer
```

1.  为了使 PostCSS 生效，我们需要在项目根目录下添加一个名为`postcss.config.js`的配置文件。让我们在这个文件中告诉 PostCSS 我们想要使用`autoprefixer`：

```js
      module.exports = {
        plugins: [
          require('autoprefixer'),
        ],
      }
```

就是这样！我们的代码现在将由`autoprefixer`处理。例如，考虑这段 Stylus 代码：

```js
.store-cart-item
  user-select none
```

最终的 CSS 将如下所示：

```js
.store-item[data-v-1af8c5dc] {
  -webkit-user-select: none;
 -moz-user-select: none;
 -ms-user-select: none;
  user-select: none;
}
```

# 使用 browserslist 来定位特定的浏览器

我们可以使用`browserslist`配置更改`autoprefixer`所定位的浏览器。它包括一系列规则，用于确定要支持哪些浏览器。打开`package.json`文件，查找`browserslist`字段。它应该已经具有`webpack-simple`模板的默认值，如下所示：

```js
"> 1%",
"last 2 versions",
"not ie <= 8"
```

第一个规则获取在互联网上使用份额超过 1%的浏览器。第二个规则另外选择每个浏览器的最后两个版本。最后，我们声明不支持 Internet Explorer 8 或更早版本。

使用的数据由专门从事浏览器兼容性数据的网站（[`caniuse.com/`](https://caniuse.com/)）提供。

您现在可以通过自定义此字段来针对甚至更旧的浏览器。例如，要针对 Firefox 20 及更高版本进行定位，您将添加以下规则：

```js
"Firefox >= 20"
```

您可以在其存储库中找到有关`browserslist`的更多信息（[`github.com/ai/browserslist`](https://github.com/ai/browserslist)）。

# 使用 ESLint 改进代码质量和风格

在与其他开发人员一起开发项目时，强制执行良好的编码实践和质量至关重要。它确保不会出现语法或基本错误（例如忘记声明变量），并有助于保持源代码清洁和一致。这个过程称为**linting**。

ESLint 是 Vue.js 团队推荐的 linting 工具。它提供了一组可以打开和关闭以检查代码质量的 linting 规则。通过插件可以添加更多规则，并且一些软件包定义了启用规则的预设。

1.  我们将使用 StandardJS 预设和`eslint-plugin-vue`软件包，该软件包添加了更多规则，有助于遵循官方 Vue 风格指南（[`vuejs.org/v2/style-guide/`](https://vuejs.org/v2/style-guide/)）。

```js
 npm i -D eslint eslint-config-standard eslint-plugin-vue@beta
```

1.  `eslint-config-standard`软件包有四个需要安装的对等依赖项：

```js
 npm i -D eslint-plugin-import eslint-plugin-node eslint-plugin- 
       promise eslint-plugin-standard
```

1.  为了在 ESLint 解析文件时对 JavaScript 代码使用 babel，我们需要安装额外的软件包：

```js
 npm i -D babel-eslint
```

# 配置 ESLint

在项目根目录中创建一个新的`.eslintrc.js`文件，并编写以下配置：

```js
module.exports = {
  // Use only this configuration
  root: true,
  // File parser
  parser: 'vue-eslint-parser',
  parserOptions: {
    // Use babel-eslint for JavaScript
    'parser': 'babel-eslint',
    'ecmaVersion': 2017,
    // With import/export syntax
    'sourceType': 'module'
  },
  // Environment global objects
  env: {
    browser: true,
    es6: true,
  },
  extends: [
    // https://github.com/feross/standard/blob/master/RULES.md#javascript-standard-style
    'standard',
    // https://github.com/vuejs/eslint-plugin-vue#bulb-rules
    'plugin:vue/recommended',
  ],
}
```

首先，我们使用`vue-eslint-parser`来读取文件（包括`.vue`文件）。在解析 JavaScript 代码时，它使用`babel-eslint`。我们还指定了 JavaScript 的 EcmaScript 版本，以及我们使用`import/export`语法进行模块化。

然后，我们告诉 ESLint 我们期望在浏览器和 ES6（或 ES2015）JavaScript 环境中，这意味着我们应该能够访问全局变量，如`window`或 Promise，而不会引发 ESLint 未定义变量错误。

我们还指定了我们想要使用的配置（或预设）--`standard`和`vue/recommended`。

# 自定义规则

我们可以使用`rules`对象更改启用的规则以及修改它们的选项。将以下内容添加到 ESLint 配置中：

```js
rules: {
  // https://github.com/babel/babel-eslint/issues/517
  'no-use-before-define': 'off',
  'comma-dangle': ['error', 'always-multiline'],
},
```

第一行禁用了`no-use-before-define`规则，在使用`...`解构运算符时会出现 bug。第二行将`commad-dangle`规则更改为强制在所有数组和对象行的末尾放置逗号`,`。

规则有一个状态，可以取这三个值--`'off'`（或`0`），`'warn'`（或`1`），和`'error'`（或`2`）。

# 运行 ESLint

要在`src`文件夹上运行 eslint，我们需要在`package.json`中添加一个新的脚本：

```js
"eslint": "eslint --ext .js,.jsx,.vue src"
```

你应该在控制台中注意到一些错误：

![](img/405cf69d-f21a-4c90-b2cd-f1c943e713ed.png)

通过在前面的`eslint`命令中添加`--fix`参数，可以修复其中一些问题：

```js
"eslint": "eslint --ext .js,.jsx,.vue src --fix"
```

再次运行它，你应该只看到一个错误剩下：

![](img/9fa11065-ae01-4017-bf71-b27f2df6a266.png)

ESLint 告诉我们不应该创建新对象而不保留它们的引用变量。如果我们查看相应的代码，我们会看到我们确实在`main.js`文件中创建了 Vue 的新实例：

```js
new Vue({
  el: '#app',
  router,
  store,
  ...App,
})
```

如果你查看 ESLint 错误，你可以看到规则的代码--`no-new`。你可以打开[`eslint.org/`](https://eslint.org/)网站并在搜索字段中输入它以获取规则定义。如果它是由插件添加的规则，它应该有插件名称后跟一个斜杠，例如`vue/require-v-for-key`。

这段代码是按预期编写的，因为这是声明 Vue 应用程序的标准方式。因此，我们需要通过在代码的特定行之前添加一个特殊注释来禁用此规则：

```js
// **eslint-disable-next-line no-new**
new Vue({
  ...
}) 
```

# Webpack 中的 ESLint

目前，我们必须手动运行`eslint`脚本来检查我们的代码。如果我们能够在 Webpack 处理代码时检查我们的代码，那将更好，这样它将是完全自动的。幸运的是，这是可能的，这要归功于`eslint-loader`。

1.  将其安装在`friendly-errors-webpack-plugin`包的开发依赖项中，这将改善控制台消息：

```js
 npm i -D eslint-loader friendly-errors-webpack-plugin
```

现在我们必须更改 webpack 配置以添加新的 ESLint 加载器规则。

1.  编辑`webpack.config.js`文件并在`module.rules`选项的顶部添加这个新规则：

```js
      module: {
        rules: [
          {
 test: /\.(jsx?|vue)$/,
 loader: 'eslint-loader',
 enforce: 'pre',
 },
          // ...
```

1.  此外，我们可以启用`friendly-errors-webpack-plugin`包。在文件顶部导入它：

```js
      const FriendlyErrors = require('friendly-errors-webpack-plugin')
```

我们不能在这里使用`import/export`语法，因为它将在 nodejs 中执行。

1.  然后，在配置文件的末尾添加一个`else`条件时，当我们处于开发模式时添加这个插件：

```js
      } else {
        module.exports.plugins = (module.exports.plugins ||            
        []).concat([
          new FriendlyErrors(),
        ])
      }
```

通过重新运行`dev`脚本重新启动 webpack 并删除代码中的逗号。你应该在 webpack 输出中看到 ESLint 错误显示：

![](img/2a3f551a-d859-475b-a371-c77241e6fa29.png)

在浏览器中，现在你应该看到错误叠加：

![](img/3f43e45a-33e9-4dee-8eec-e0324b169115.png)

如果您通过重新添加逗号来修复错误，覆盖层将关闭，并且控制台将显示友好的消息：

![](img/30a0902a-1b0d-4313-85f2-9172ffcb67a4.png)

# 使用 Jest 进行单元测试

重要的代码和组件应该进行单元测试，以确保它们按预期工作，并在代码演变时防止大多数回归。Vue 组件的推荐测试运行器是来自 Facebook 的 Jest。它具有缓存系统，速度相当快，并且具有一个方便的快照功能，可以帮助检测更多的回归。

1.  首先，安装 Jest 和官方的 Vue 单元测试工具：

```js
 npm i -D jest vue-test-utils
```

1.  我们还需要一些与 Vue 相关的实用程序，以使用`jest-vue`编译`.vue`文件并对组件进行快照：

```js
 npm i -D vue-jest jest-serializer-vue vue-server-renderer
```

在节点中获取组件的 HTML 渲染的推荐方式是使用`vue-server-renderer`包，用于进行服务器端渲染，我们将在本章后面看到。

1.  最后，我们将需要一些 babel 包来支持 Jest 内部的 babel 编译和 webpack 动态导入：

```js
 npm i -D babel-jest babel-plugin-dynamic-import-node
```

# 配置 Jest

要配置 Jest，让我们在项目根目录中创建一个新的`jest.config.js`文件：

```js
module.exports = {
  transform: {
    '.+\\.jsx?$': '<rootDir>/node_modules/babel-jest',
    '.+\\.vue$': '<rootDir>/node_modules/vue-jest',
  },
  snapshotSerializers: [
    '<rootDir>/node_modules/jest-serializer-vue',
  ],
  mapCoverage: true,
}
```

`transform`选项定义了 JavaScript 和 Vue 文件的处理器。然后，我们告诉 Jest 使用`jest-serializer-vue`来序列化组件的快照。我们还将使用`mapCoverage`选项启用源映射。

您可以在 Jest 网站([`facebook.github.io/jest/`](https://facebook.github.io/jest/))上找到更多配置选项。

# Jest 的 Babel 配置

为了支持 Jest 内部的 JavaScript `import/export`模块和动态导入，我们需要在测试运行时更改我们的 babel 配置。

在使用 Jest 时，我们不使用 webpack 和我们用来构建真实应用程序的加载器。

当`NODE_ENV`环境变量设置为`"test"`时，我们需要向配置中添加两个 babel 插件：

```js
{
  "presets": [
    ["env", { "modules": false }],
    "stage-3"
  ],
 "env": {
 "test": {
 "plugins": [
 "transform-es2015-modules-commonjs",
 "dynamic-import-node"
 ]
 }
 }
}
```

`transform-es2015-modules-commonjs`插件为 Jest 添加了对`import/export`语法的支持，`dynamic-import-node`为动态导入添加了支持。

当运行时，Jest 会自动将`NODE_ENV`环境变量设置为`'test'`。

# 我们的第一个单元测试

为了让 Jest 默认识别任何地方，我们需要将我们的测试文件命名为`.test.js`或`.spec.js`。我们将测试`BaseButton.vue`组件；继续在`src/components`文件夹中创建一个新的`BaseButton.spec.js`文件。

1.  首先，我们将从`vue-test-utils`中导入组件和`shallow`方法：

```js
      import BaseButton from './BaseButton.vue'
      import { shallow } from 'vue-test-utils'
```

1.  接下来，我们将使用`describe`函数创建一个测试套件：

```js
 describe('BaseButton', () => {
        // Tests here
      })
```

1.  在测试套件内部，我们可以使用`test`函数添加我们的第一个单元测试：

```js
      describe('BaseButton', () => {
        test('click event', () => {
          // Test code
        })
      })
```

1.  我们将测试在点击组件时是否会触发`click`事件。我们需要在组件周围创建一个包装对象，该对象将提供有用的函数来测试组件：

```js
 const  wrapper  =  shallow(BaseButton)
```

1.  然后，我们将模拟点击组件：

```js
 wrapper.trigger('click')
```

1.  最后，我们将使用 Jest 的`expect`方法检查`click`事件是否被触发：

```js
 expect(wrapper.emitted().click).toBeTruthy()
```

1.  现在，让我们在`package.json`文件中添加一个脚本来运行 Jest：

```js
 "jest": "jest"
```

1.  然后，使用通常的`npm run`命令：

```js
 npm run jest
```

测试已启动并应该通过如下：

![](img/8ba5e5b4-61b3-472e-a5d8-7061e349368d.png)

要了解有关单元测试 Vue 组件的更多信息，您可以访问官方指南[`vue-test-utils.vuejs.org/`](https://vue-test-utils.vuejs.org/)

# ESLint 和 Jest 全局变量

如果现在运行 ESLint，我们将会收到与 Jest 关键字（如`describe`，`test`和`expect`）相关的错误：

![](img/cbe5bb34-3fa0-4ec2-845e-665b0927d6f4.png)

我们需要对 ESLint 配置进行微小更改--我们必须指定`jest`环境；编辑`.eslintrc.js`文件：

```js
// Environment global objects
env: {
  browser: true,
  es6: true,
  jest: true,
},
```

现在，ESLint 将了解 Jest 关键字，并将停止抱怨。

# Jest 快照

快照是保存并比较每次运行测试时的字符串，以检测潜在的回归。它们主要用于保存组件的 HTML 渲染，但只要在测试之间存储它并进行比较就可以用于任何值。

对于我们的 Vue 组件，我们将使用名为`vue-server-renderer`的服务器端渲染工具对其进行 HTML 渲染快照。我们将需要来自此软件包的`createRenderer`方法：

```js
import { createRenderer } from  'vue-server-renderer'
```

在测试开始时，我们实例化一个渲染器实例，然后用`shallow`包装组件并开始将组件渲染为字符串。最后，我们将结果与先前的结果进行比较。以下是对`BaseButton`组件进行快照测试的示例，传递一些 props 值和默认插槽内容：

```js
test('snapshot', () => {
  const renderer = createRenderer()
  const wrapper = shallow(BaseButton, {
    // Props values
    propsData: {
      icon: 'add',
      disabled: true,
      badge: '3',
    },
    // Slots content
    slots: {
      default: '<span>Add Item</span>',
    },
  })
  renderer.renderToString(wrapper.vm, (err, str) => {
    if (err) throw new Error(err)
    expect(str).toMatchSnapshot()
  })
})
```

如果首次运行快照测试，它将创建并保存快照到其旁边的`__snapshots__`文件夹中。如果您正在使用 git 等版本控制系统，则需要将这些快照文件添加到其中。

# 更新快照

如果您修改了一个组件，那么它的 HTML 渲染也有可能会发生变化。这意味着它的快照将不再有效，Jest 测试将失败。幸运的是，`jest`命令有一个`--updateSnapshots`参数。当使用时，所有失败的快照将被重新保存并通过测试。

1.  让我们在`package.json`文件中添加一个新的脚本：

```js
 "jest:update": "jest **--updateSnapshot**"
```

1.  通过更改 CSS 类来修改`BaseButton`组件，例如。如果再次运行 Jest 测试，您应该会收到一个错误，指出快照不再匹配。

![](img/697b17b2-cdb2-499a-a113-e8e3ea3c7301.png)

1.  现在，使用新的脚本更新快照：

```js
 npm run jest:update
```

所有的测试现在应该都通过了，`BaseButton`的快照应该已经更新了：

![](img/d00b9be6-1df2-480a-96e8-50533f37de7d.png)您应该仅在确定其他地方没有回归时运行此命令。一个好主意是在更新快照之前正常运行测试，以确保只有修改的组件快照失败，这是预期的。更新快照后，使用正常的测试命令。

# 补充主题

在本节中，我们将涵盖一些对于更大型应用程序可能有用的主题。

# 国际化和代码拆分

如果应用程序将被不同国家的人使用，应该进行翻译以使其更加用户友好和吸引人。为了本地化应用程序的文本，您可以使用推荐的`vue-i18n`包：

```js
npm i -S vue-i18n
```

使用`vue-i18n`，我们将在`AppFooter`组件中添加一个链接到一个新页面，用户可以在该页面中选择语言。只有链接和此页面将被翻译，但如果您愿意，您可以翻译应用程序的更多部分。`vue-i18n`通过使用翻译消息创建一个`i18n`对象，并将其注入到 Vue 应用程序中。

1.  在`src/plugins.js`文件中，将新的插件安装到 Vue 中：

```js
      import VueI18n from 'vue-i18n'

      // ...

      Vue.use(VueI18n)
```

1.  让我们在项目目录中创建一个名为`i18n`的新文件夹。下载包含翻译文件的`locales`文件夹（[`github.com/Akryum/packt-vue-project-guide/tree/master/chapter7-download/locales`](https://github.com/Akryum/packt-vue-project-guide/tree/master/chapter7-download/locales)）并将其放入其中。例如，您应该在`i18n/locales/en.js`文件中有`en`的翻译。

1.  创建一个新的`index.js`文件，导出可用语言的列表：

```js
      export default [
        'en',
        'fr',
        'es',
        'de',
      ]
```

我们将需要两个新的实用函数：

+   `createI18n`：创建`i18n`对象，带有`locale`参数。

+   `getAutoLang`：返回用户在浏览器中设置的两字母语言代码，例如`en`或`fr`。大多数情况下，这将是操作系统的语言设置。

1.  在`src/utils`文件夹中，创建一个新的`i18n.js`文件，并导入之前定义的`VueI18n`和可用区域设置列表：

```js
      import VueI18n from 'vue-i18n'
      import langs from '../../i18n'
```

1.  在撰写本文时，我们需要`babel-preset-stage-2`（或更低版本）来允许 Babel 解析动态导入。在`package.json`文件中，更改`babel-preset-stage-3`包：

```js
      "babel-preset-stage-2": "⁶.24.1",
```

1.  运行`npm install`来更新您的包。

1.  编辑根文件夹中的`.babelrc`文件，并将`stage-3`更改为`stage-2`。

1.  为了切换到阶段 2，进行以下安装：

```js
npm install --save-dev babel-preset-stage-2
```

# 使用动态导入进行代码拆分

当我们创建`i18n`对象时，我们希望仅通过`locale`参数加载所选语言环境的翻译。为此，我们将使用`import`函数对文件进行动态导入。它以路径作为参数，并返回一个 Promise，一旦从服务器加载，它将最终解析为相应的 JavaScript 模块。

在 webpack 中，这种动态导入功能有时被称为'代码拆分'，因为 webpack 将将异步模块移动到另一个编译后的 JavaScript 文件中，称为块。

以下是使用动态导入加载的异步模块的示例：

```js
async function loadAsyncModule () {
  await module = await import('./path/to/module')
  console.log('default export', module.default)
  console.log('named export', module.myExportedFunction)
}
```

您可以在导入路径中使用变量，只要它包含有关 webpack 可以找到文件的一些信息。例如，这段代码将无法工作：

```js
import(myModulePath)
```

然而，只要变量路径简单（没有`../`），以下内容将正常工作：

```js
import(`./data/${myFileName}.json`)
```

在这个例子中，`data`文件夹中所有带有`json`扩展名的文件将被添加到构建中作为异步块，因为 webpack 无法猜测您在运行时真正会使用哪些文件。

使用动态导入异步加载大型 JavaScript 模块可以减少在打开页面时发送到浏览器的初始 JavaScript 代码的大小。在我们的应用程序中，它允许我们仅加载所选语言环境的相关翻译文件，而不是在初始 JavaScript 文件中包含它们。

如果一个模块已经在主代码（初始块）中使用普通的`import`导入，它将已经被加载，不会被拆分成另一个块。在这种情况下，你将无法享受代码拆分功能的好处，初始文件大小也不会减小。请注意，你可以在动态加载的模块中同步使用其他模块，使用普通的`import`关键字：它们将被放在同一个块中（如果它们尚未包含在初始块中）。

`i18n`对象是使用`vue-i18n`包中的`VueI18n`构造函数创建的。我们将传递`locale`参数。

`createI18n`函数应该是这样的：

```js
export async function createI18n (locale) {
  const { default: localeMessages } = await import(`../../i18n/locales/${locale}`)
  const messages = {
    [locale]: localeMessages,
  }

  const i18n = new VueI18n({
    locale,
    messages,
  })

  return i18n
}
```

如你所见，我们需要取模块的`default`值，因为我们使用`export default`导出了消息。

上面使用`async/await`的代码可以使用 Promises 来编写：

```js
export function createI18n (locale) {
  return import(`../../i18n/locales/${locale}`)
    .then(module => {
      const localeMessages = module.default
      // ...
    })
}
```

# 自动加载用户语言环境

接下来，我们可以使用`navigator.language`（或`userLanguage`用于兼容 Internet Explorer）来检索语言环境代码。然后，我们将检查它是否在`langs`列表中可用，或者我们是否必须使用默认的`en`语言环境。

1.  `getAutoLang`函数应该是这样的：

```js
      export function getAutoLang () {
        let result = window.navigator.userLanguage || 
        window.navigator.language
        if (result) {
          result = result.substr(0, 2)
        }
        if (langs.indexOf(result) === -1) {
          return 'en'
        } else {
          return result
        }
      }
```

一些浏览器可能以`en-US`格式返回代码，但我们只需要前两个字符。

1.  在`src/main.js`文件中，导入这两个新的实用函数：

```js
      import { createI18n, getAutoLang } from './utils/i18n'
```

1.  然后，修改`main`函数：

1.  使用`getAutoLang`检索首选语言环境。

1.  使用`createI18n`函数创建并等待`i18n`对象。

1.  将`i18n`对象注入到根 Vue 实例中。

现在它应该是这样的：

```js
async function main () {
  const locale = getAutoLang()
 const i18n = await createI18n(locale)
  await store.dispatch('init')

  // eslint-disable-next-line no-new
  new Vue({
   el: '#app',
    router,
    store,
    i18n, // Inject i18n into the app
    ...App,
  })
}
```

不要忘记在`createI18n`前面加上`await`关键字，否则你将得到一个 Promise。

现在你可以在浏览器开发工具的网络面板中打开，并刷新页面。webpack 将会通过单独的请求加载对应于所选语言环境的翻译模块。在这个示例截图中，这是异步加载的`2.build.js`文件：

![](img/7fc40741-5fb8-4dac-b4fe-6b1108ed649e.png)

# 更改语言页面

目前，应用程序并没有真正改变，所以让我们添加一个页面，让我们可以选择语言。

1.  在`src/router.js`文件中，导入`PageLocale`组件：

```js
      import PageLocale from './components/PageLocale.vue'
```

1.  然后，在`routes`数组中添加`locale`路由，就在最后一个路由（带有`*`路径）之前：

```js
      { path: '/locale', name: 'locale', component: PageLocale },
```

1.  在`AppFooter.vue`组件中，将这个路由链接添加到模板中：

```js
      <div v-if="$route.name !== 'locale'">
        <router-link :to="{ name: 'locale' }">{{ $t('change-lang') }}
        </router-link>
      </div>
```

正如您在前面的代码中所看到的，我们使用了`vue-i18n`提供的`$t`来显示翻译文本。参数对应于区域文件中的键。现在您应该在应用程序页脚中看到该链接：

![](img/16f6aa12-ab37-4402-b2ad-68b0ba446978.png)

链接将我们带到语言选择页面，该页面已经完全使用`vue-i18n`进行了翻译：

![](img/ebe4ef49-49c9-4427-b65a-090b56bd23f0.png)

您可以在`components/PageLocale.vue`文件中查看其源代码。

当您单击区域按钮时，如果尚未加载，将加载相应的翻译。在浏览器开发工具的网络面板中，每次都应该看到对其他块的请求：

![](img/be04ca4d-f80a-4f51-94fd-f3a2659730a8.png)

# 服务器端渲染

**服务器端渲染**（**SSR**）包括在服务器上运行和渲染应用程序，然后将 HTML 发送回浏览器。这有两个主要优点：

+   更好的**搜索引擎优化**（**SEO**），因为应用程序的初始内容将在页面 HTML 中呈现。这很重要，因为没有搜索引擎正在索引异步 JavaScript 应用程序（例如，当您有一个旋转器时）。

+   较慢的网络或设备将更快地显示内容——渲染的 HTML 不需要 JavaScript 才能显示给用户。

然而，使用 SSR 也带来了一些权衡：

+   代码需要能够在服务器上运行（除非它是在客户端专用的挂钩，比如`mounted`）。此外，一些库可能在浏览器上表现不佳，可能需要特殊处理。

+   服务器的负载将增加，因为它要做更多的工作。

+   开发设置有点复杂。

因此，使用 SSR 并不总是一个好主意，特别是如果第一次显示内容的时间不是关键的话（例如，管理仪表板）。

# 通用应用程序结构

编写一个可以在客户端和服务器上运行的通用应用程序需要改变源代码的架构。

在客户端运行时，每次加载页面时我们都处于一个新的上下文。这就是为什么我们到目前为止都使用根实例、路由器和存储的单例实例。然而，现在我们也需要在服务器上有一个新的上下文——问题是，Node.js 是有状态的。解决方案是为服务器处理的每个请求创建一个全新的根实例、路由器和存储。

1.  让我们从路由器开始。在`src/router.js`文件中，将路由器创建包装成一个新的导出的`createRouter`函数：

```js
 export function createRouter () {
        const router = new VueRouter({
          routes,
          mode: 'history',
          scrollBehavior (to, from, savedPosition) {
            // ...
          },
        })

        return router
 }
```

1.  我们将对 Vuex 存储执行相同的操作。在`src/store/index.js`文件中，将代码包装到一个新的导出的`createStore`函数中：

```js
 export function createStore () {
        const store = new Vuex.Store({
          strict: process.env.NODE_ENV !== 'production',

          // ...

          modules: {
            cart,
            item,
            items,
            ui,
          },
        })

        return store
 }
```

1.  让我们也将`src/main.js`文件重命名为`src/app.js`。这将是我们的通用文件，用于创建路由器、存储器和 Vue 根实例。将`main`函数改为导出的`createApp`函数，该函数接受一个`context`参数并返回应用程序、路由器和存储器：

```js
 export async function createApp (context) {
        const router = createRouter()
        const store = createStore()

        sync(store, router)

        const i18n = await createI18n(context.locale)
        await store.dispatch('init')

        const app = new Vue({
          router,
          store,
          i18n,
          ...App,
        })

        return {
 app,
 router,
 store,
 }
      }
```

不要忘记更改`createRouter`和`createStore`的导入。

在服务器上，我们不会像在客户端那样选择初始区域设置，因为我们无法访问`window.navigator`。这就是为什么我们在`context`参数中传递区域设置的原因：

```js
const i18n = await createI18n(context.locale)
```

我们还从根实例定义中删除了`el`选项，因为在服务器上没有意义。

# 客户端入口

在浏览器上，代码将在我们现在将编写的客户端入口文件中启动。

1.  创建一个新的`src/entry-client.js`文件，它将成为客户端包的入口点。它将获取用户语言，调用`createApp`函数，然后将应用程序挂载到页面上：

```js
      import { createApp } from './app'
      import { getAutoLang } from './utils/i18n'

      const locale = getAutoLang()
      createApp({
        locale,
      }).then(({ app }) => {
        app.$mount('#app')
      })
```

1.  现在您可以更改`webpack.config.js`文件中的入口路径：

```js
 entry: './src/entry-client.js',
```

您可以重新启动`dev`脚本，并检查应用程序是否仍然在浏览器中运行。

# 服务器入口

创建一个新的`src/entry-server.js`文件，它将成为服务器包的入口点。它将导出一个从我们稍后将构建的 HTTP 服务器获取`context`对象的函数。它应该返回一个 Promise，在 Vue 应用程序准备就绪时解析该 Promise。

我们将在`context`中传递一个`url`属性，以便我们可以设置当前路由，就像这样： 

```js
router.push(context.url)
```

与客户端入口类似，我们还使用`createApp`函数来创建根应用程序实例、路由器和存储器。`entry-server.js`应该是这样的：

```js
import { createApp } from './app'

export default context => {
  return new Promise(async (resolve, reject) => {
    const { app, router, store } = await createApp(context)
    // Set the current route
    router.push(context.url)
    // TODO get matched components to preload data
    // TODO resolve(app)
  })
}
```

我们返回一个 Promise，因为当我们完成所有操作时，我们将发送应用程序`app`。

`app`根实例将通过`resolve(app)`发送回我们称之为渲染器的地方（有点像我们做 Jest 快照时）。首先，我们需要处理预加载 Vuex 存储。

# 状态管理

在处理请求时，我们需要在渲染应用程序之前在相关组件上获取数据。这样，当浏览器加载 HTML 时，数据已经显示出来。例如，`PageHome.vue`获取存储项，`PageStoreItem.vue`检索项目详细信息和评论。

我们将为这些组件添加一个新的`asyncData`自定义选项，这样我们可以在进行 SSR 时在服务器上调用它。

1.  通过添加此函数来编辑`PageHome.vue`组件，该函数会调度`items`存储模块的`fetchItems`操作：

```js
 asyncData ({ store }) {
        return store.dispatch('items/fetchItems')
      },
```

1.  在`PageStoreItem.vue`组件中，我们需要调用服务器传递的路由的`id`参数，调用`item`存储模块的`fetchStoreItemDetails`操作：

```js
 asyncData ({ store, route }) {
        return store.dispatch('item/fetchStoreItemDetails', {
          id: route.params.id,
        })
      },
```

1.  现在我们的组件已经准备好了，我们将回到`entry-server.js`。我们可以使用`router.getMatchedComponents()`方法获取与当前路由匹配的组件列表：

```js
      export default context => {
        return new Promise(async (resolve, reject) => {
          const { app, router, store } = await createApp(context)
          router.push(context.url)
          // Wait for the component resolution
          router.onReady(() => {
 const matchedComponents = router.getMatchedComponents()
            // TODO pre-load data
            // TODO resolve(app)
          }, reject)
        })
      }
```

1.  然后我们可以调用这些组件的所有`asyncData`选项并等待它们完成。我们将 store 和当前路由传递给它们，当它们全部完成时，我们使用`context.state = store.state`将 Vuex 存储状态发送回渲染器。使用`Promise.all(array)`等待所有`asyncData`调用：

```js
      router.onReady(() => {
        const matchedComponents = router.getMatchedComponents()

        Promise.all(matchedComponents.map(Component => {
 if (Component.asyncData) {
 return Component.asyncData({
 store,
 route: router.currentRoute,
 })
 }
 })).then(() => {
          // Send back the store state
          context.state = store.state

          // Send the app to the renderer
          resolve(app)
 }).catch(reject)
      }, reject)
```

如果发生错误，它将拒绝我们返回给渲染器的 Promise。

# 在客户端恢复 Vuex 状态

服务器将 store 状态序列化为 HTML 页面中的`__INITIAL_STATE__`变量。我们可以使用这个来在应用挂载之前设置状态，这样组件将可以访问它。

编辑`entry-client.js`文件，并在挂载应用之前使用`store.replaceState`方法：

```js
createApp({
  locale,
}).then(({ app, store }) => {
  if (window.__INITIAL_STATE__) {
 store.replaceState(window.__INITIAL_STATE__)
 }

  app.$mount('#app')
})
```

现在，存储将拥有服务器发送的数据。

# Webpack 配置

我们的应用代码现在已经准备好了。在继续之前，我们需要重构我们的 webpack 配置。

我们需要为客户端和服务器准备稍有不同的 webpack 配置。最好有一个通用的配置文件，然后为客户端和服务器进行扩展。我们可以使用`webpack-merge`包轻松实现这一点，该包将多个 webpack 配置对象合并为一个。

对于服务器配置，我们还需要`webpack-node-externals`包来防止 webpack 打包`node_modules`中的包--这是不必要的，因为我们将在 nodejs 中运行而不是在浏览器中。所有相应的导入将保留为`require`语句，以便 node 自己加载它们。

1.  在开发依赖项中安装这些包：

```js
 npm i -D webpack-merge webpack-node-externals
```

1.  在项目根目录中创建一个新的`webpack`文件夹，然后将`webpack.config.js`文件移动并重命名为`webpack/common.js`。需要一些更改。

1.  从配置中删除`entry`选项。这将在特定的扩展配置中指定。

1.  更新 `output` 选项以定位到正确的文件夹并生成更好的 chunk 名称：

```js
      output: {
        path: path.resolve(__dirname, '../dist'),
        publicPath: '/dist/',
        filename: '[name].[chunkhash].js',
      },
```

# 客户端配置

在 `webpack/common.js` 旁边，创建一个新的 `client.js` 文件，扩展基本配置：

```js
const webpack = require('webpack')
const merge = require('webpack-merge')
const common = require('./common')
const VueSSRClientPlugin = require('vue-server-renderer/client-plugin')

module.exports = merge(common, {
  entry: './src/entry-client',
  plugins: [
    new webpack.optimize.CommonsChunkPlugin({
      name: 'manifest',
      minChunks: Infinity,
    }),
    // Generates the client manifest file
    new VueSSRClientPlugin(),
  ],
})
```

`VueSSRClientPlugin` 将生成一个 `vue-ssr-client-manifest.json` 文件，我们将其提供给渲染器。这样，它将更多地了解客户端。此外，它将自动将脚本标签和关键 CSS 注入到 HTML 中。

关键 CSS 是服务器渲染的组件的样式。这些样式将直接注入到页面 HTML 中，这样浏览器就不必等待 CSS 加载，可以更早地显示这些组件。

`CommonsChunkPlugin` 将 webpack 运行时代码放入一个主要的 chunk 中，这样异步 chunk 就可以在它之后被注入。它还改善了应用程序和供应商代码的缓存。

# 服务器配置

在 `webpack/common.js` 旁边，创建一个新的 `server.js` 文件，扩展基本配置：

```js
const merge = require('webpack-merge')
const common = require('./common')
const nodeExternals = require('webpack-node-externals')
const VueSSRServerPlugin = require('vue-server-renderer/server-plugin')

module.exports = merge(common, {
  entry: './src/entry-server',
  target: 'node',
  devtool: 'source-map',
  output: {
    libraryTarget: 'commonjs2',
  },
  // Skip webpack processing on node_modules
  externals: nodeExternals({
    // Force css files imported from no_modules
    // to be processed by webpack
    whitelist: /\.css$/,
  }),
  plugins: [
    // Generates the server bundle file
    new VueSSRServerPlugin(),
  ],
})
```

在这里，我们更改多个选项，例如 `target` 和 `output.libraryTarget`，以适应 node.js 环境。

使用 `webpack-node-externals` 包，我们告诉 webpack 忽略位于 `node_modules` 文件夹中的模块（这意味着依赖项）。由于我们在 nodejs 中而不是在浏览器中，我们不必将所有依赖项捆绑到包中，因此这将改善构建时间。

最后，我们使用 `VueSSRServerPlugin` 生成将被渲染器使用的服务器包文件。它包含编译后的服务器端代码和许多其他信息，以便渲染器可以支持源映射（使用 `devtool` 的 `source-map` 值）、热重新加载、关键 CSS 注入以及与客户端清单数据一起的其他注入。

# 服务器端设置

在开发中，我们不能再直接使用 `webpack-dev-server` 来进行 SSR。相反，我们将使用 webpack 设置 express 服务器。下载 `server.dev.js` 文件（[`github.com/Akryum/packt-vue-project-guide/blob/master/chapter7-download/server.dev.js`](https://github.com/Akryum/packt-vue-project-guide/blob/master/chapter7-download/server.dev.js)）并将其放在项目根目录中。该文件导出一个 `setupDevServer` 函数，我们将使用它来运行 webpack 并更新服务器。

我们还需要一些用于开发设置的包：

```js
npm i -D memory-fs chokidar webpack-dev-middleware webpack-hot-middleware
```

我们可以使用`memory-fs`创建虚拟文件系统，使用`chokidar`监视文件，并在 express 服务器中使用最后两个中间件启用 webpack 热模块替换。

# 页面模板

在`index.html`旁边创建一个新的`index.template.html`文件，并复制其内容。然后，用特殊的`<!--vue-ssr-outlet-->`注释替换 body 内容：

```js
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Fashion Store</title>
  </head>
  <body>
    <!--vue-ssr-outlet-->
  </body>
</html&gt;
```

这个特殊的注释将被服务器上的渲染标记替换。

# Express 服务器

在 nodejs 端，我们将使用`express`包来创建我们的 HTTP 服务器。我们还需要`reify`包，以便我们可以在 nodejs 中要求使用`import/export`语法的文件（它不支持原生支持）。

1.  安装新的包：

```js
 npm i -S express reify
```

1.  下载这个不完整的`server.js`文件（[`github.com/Akryum/packt-vue-project-guide/blob/master/chapter7-download/server.dev.js`](https://github.com/Akryum/packt-vue-project-guide/blob/master/chapter7-download/server.dev.js)）并将其放在项目根目录中。它已经创建了一个 express 服务器并配置了必要的 express 路由。

现在，我们将专注于开发部分。

# 创建和更新渲染器

要渲染我们的应用程序，我们将需要使用`vue-server-renderer`包中的`createBundleRenderer`函数创建的渲染器。

捆绑渲染器与普通渲染器有很大不同。它使用一个服务器捆绑文件（这将由我们的新 webpack 配置生成），还有一个可选的客户端清单，允许渲染器对代码有更多的信息。这使得更多功能成为可能，比如源映射和热重新加载。

在`server.js`文件中，用这段代码替换`// TODO development`注释：

```js
const setupDevServer = require('./server.dev')
readyPromise = setupDevServer({
  server,
  templatePath,
  onUpdate: (bundle, options) => {
    // Re-create the bundle renderer
    renderer = createBundleRenderer(bundle, {
      runInNewContext: false,
      ...options,
    })
  },
})
```

由于`server.dev.js`文件，我们可以为我们的 express 服务器添加 webpack 热重新加载支持。我们还指定了 HTML 页面模板的路径，因此当更改时我们也可以重新加载它。

当设置触发更新时，我们将创建或重新创建捆绑渲染器。

# 渲染 Vue 应用程序

接下来，我们需要实现渲染应用程序的代码，并将 HTML 结果发送回客户端。

用这个替换`// TODO render`注释：

```js
const context = {
  url: req.url,
  // Languages sent by the browser
  locale: req.acceptsLanguages(langs) || 'en',
}
renderer.renderToString(context, (err, html) => {
  if (err) {
    // Render Error Page or Redirect
    res.status(500).send('500 | Internal Server Error')
    console.error(`error during render : ${req.url}`)
    console.error(err.stack)
  }
  res.send(html)
})
```

由于 express 的`req.acceptsLanguages`方法，我们可以轻松地选择用户的首选语言。

在执行请求时，Web 浏览器将发送用户的“接受的语言”列表。这通常是他们的浏览器或操作系统设置的语言。

然后我们使用 `renderToString` 方法，该方法将调用我们在 `entry-server.js` 文件中导出的函数，等待返回的 Promise 完成，然后将应用程序渲染为 HTML 字符串。最后，我们将结果发送给客户端（除非在渲染过程中出现错误）。

# 运行我们的 SSR 应用程序

现在是运行应用程序的时候了。将 `dev` 脚本更改为运行我们的 express 服务器，而不是 `webpack-dev-server`：

```js
"dev": "node server",
```

重新启动脚本并刷新应用程序。为了确保 SSR 正常工作，请查看页面的源代码：

![](img/e6fa9edd-fa17-4311-ad62-3f77f09f2fcb.png)

应用程序已经由服务器呈现为 HTML。

# 不必要的获取

不幸的是，我们的应用出了问题。服务器将 Vuex 存储数据与页面的 HTML 一起发送，这意味着应用程序在第一次运行时已经具有了所有需要的数据，只是仍在进行检索项目详细信息和评论的请求。您可以通过加载动画来看到这一点，该动画在首次加载或刷新相应页面时出现。

解决此问题的方法是防止组件在不必要时获取数据：

1.  在 `PageHome.vue` 组件中，我们只需要在没有这些数据时才获取项目：

```js
      mounted () {
        if (!this.items.length) {
          this.fetchItems()
        }
      },
```

1.  在 `PageStoreItem.vue` 组件中，只有在没有数据时才应获取详细信息和评论：

```js
      fetchData () {
        if (!this.details || this.details.id !== this.id) {
          this.fetchStoreItemDetails({
            id: this.id,
          })
        }
      },
```

我们现在不再有这个问题了。

要继续了解 SSR，您可以访问官方文档 [`ssr.vuejs.org/`](https://ssr.vuejs.org/)，或者使用一个易于使用的框架 nuxtjs ([`nuxtjs.org/`](https://nuxtjs.org/))，该框架为您抽象了许多样板代码。

# 生产构建

我们的应用在开发中运行得很好。假设我们已经完成了它，并且想要将其部署到真实服务器上。

# 额外配置

我们需要为应用程序的生产构建添加一些配置，以确保其优化。

# 将样式提取到 CSS 文件中

到目前为止，样式是通过 JavaScript 代码添加到页面中的。这在开发中非常好，因为它允许使用 webpack 进行热重载。然而，在生产中，建议将其提取到单独的 CSS 文件中。

1.  在开发依赖中安装 `extract-text-webpack-plugin` 包：

```js
 npm i -D extract-text-webpack-plugin
```

1.  在 `webpack/common.js` 配置文件中，添加一个新的 `isProd` 变量：

```js
      const isProd = process.env.NODE_ENV === 'production'
```

1.  修改`vue-loader`规则，以在生产环境下启用 CSS 提取，并忽略 HTML 标签之间的空白：

```js
      {
        test: /\.vue$/,
        loader: 'vue-loader',
        options: {
          extractCSS: isProd,
          preserveWhitespace: false,
        },
      },
```

1.  在文件底部的仅限生产的插件列表中添加`ExtractTextPlugin`和`ModuleConcatenationPlugin`：

```js
      if (isProd) {
        module.exports.devtool = '#source-map'
        module.exports.plugins = (module.exports.plugins || 
        []).concat([
          // ...
          new webpack.optimize.ModuleConcatenationPlugin(),
 new ExtractTextPlugin({
 filename: 'common.[chunkhash].css',
 }),
        ])
      } else {
       // ...
      }
```

`ExtractTextPlugin`将样式放入 CSS 文件中，而`ModuleConcatenationPlugin`将优化编译后的 JavaScript 代码以提高速度。

# 生产环境 express 服务器

我们需要对我们的代码进行的最后更改是在 express 服务器中创建包渲染器。

在`server.js`文件中，用以下内容替换`// TODO production`注释：

```js
const template = fs.readFileSync(templatePath, 'utf-8')
const bundle = require('./dist/vue-ssr-server-bundle.json')
const clientManifest = require('./dist/vue-ssr-client-manifest.json')
renderer = createBundleRenderer(bundle, {
  runInNewContext: false,
  template,
  clientManifest,
})
```

我们将读取 HTML 页面模板、服务器包和客户端清单。然后，我们创建一个新的包渲染器，因为在生产环境中我们不会有热重载。

# 新的 npm 脚本

编译后的代码将输出到项目根目录中的`dist`目录。在每次构建之间，我们需要将其删除，以便处于干净的状态。为了以跨平台的方式做到这一点，我们将使用可以递归删除文件和文件夹的`rimraf`包。

1.  安装`rimraf`包到开发依赖项中：

```js
 npm i -D rimraf
```

1.  为客户端和服务器包添加一个`build`脚本：

```js
      "build:client": "cross-env NODE_ENV=production webpack --progress 
       --hide-modules --config webpack/client.js",
      "build:server": "cross-env NODE_ENV=production webpack --progress 
       --hide-modules --config webpack/server.js",
```

我们将`NODE_ENV`环境变量设置为`'production'`，并使用相应的 webpack 配置文件运行`webpack`命令。

1.  创建一个新的`build`脚本，清空`dist`文件夹，并运行另外两个`build:client`和`build:server`脚本：

```js
      "build": "rimraf dist && npm run build:client && npm run 
        build:server",
```

1.  添加一个名为`start`的最后一个脚本，以在生产模式下运行 express 服务器：

```js
      "start": "cross-env NODE_ENV=production node server",
```

1.  现在可以运行构建；使用通常的`npm run`命令：

```js
 npm run build
```

`dist`文件夹现在应该包含 webpack 生成的所有块，以及服务器包和客户端清单 json 文件：

![](img/b512c29d-758b-4e57-8c09-bee33bb1f435.png)这些是需要上传到真实 nodejs 服务器的文件。

1.  现在可以启动 express 服务器：

```js
 npm start
```

您还应该上传`server.js`、`package.json`和`package-lock.json`文件到真实服务器。不要忘记使用`npm install`安装依赖项。

# 总结

在这一章中，我们通过学习如何使用 PostCSS 自动添加 CSS 前缀，使用 ESLint 对代码进行质量检查，以及使用 Jest 对组件进行单元测试，改进了我们的开发工作流程。我们甚至进一步添加了`vue-i18n`包和动态导入的本地化，并通过重构项目实现了服务器端渲染，同时仍然利用了 webpack 的热重载、代码分割和优化等强大功能。

在最后一章中，我们将使用 Meteor 全栈框架和 Vue 创建一个简单的实时应用程序。
