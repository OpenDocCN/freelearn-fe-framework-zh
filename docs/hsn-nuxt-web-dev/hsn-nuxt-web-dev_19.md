使用 Linter、Formatter 和部署命令

除了编写测试（无论是端到端测试还是单元测试），代码检查和格式化也是 Web 开发的一部分。所有开发人员，无论您是 Java、Python、PHP 还是 JavaScript 开发人员，都应该了解其领域的编码标准，并遵守这些标准，以保持您的代码清洁、可读，并为将来更好地维护格式化。我们通常用于 JavaScript、Vue 和 Nuxt 应用的工具是 ESLint、Prettier 和 StandardJS。在本章中，您将学习如何安装、配置和使用它们。最后，在构建、测试和检查您的应用程序之后，您将学习 Nuxt 部署命令，以将您的应用程序部署到主机。

本章我们将涵盖以下主题：

+   介绍 linter - Prettier、ESLint 和 StandardJS

+   集成 ESLint 和 Prettier

+   为 Vue 和 Nuxt 应用程序使用 ESLint 和 Prettier

+   部署 Nuxt 应用程序

# 第十四章：介绍 linter - Prettier、ESLint 和 StandardJS

简而言之，linter 是一种分析源代码并标记代码和样式中的错误和错误的工具。这个术语起源于 1978 年的一个名为`lint`的 Unix 实用程序，它评估了用 C 编写的源代码，并由贝尔实验室的计算机科学家 Stephen C. Johnson 开发，用于调试他正在编写的 Yacc 语法。今天，我们在本书中关注的工具是 Prettier、ESLint 和 StandardJS。让我们来看看它们各自的情况。

## Prettier

Prettier 是一个支持许多语言的代码格式化程序，如 JavaScript、Vue、JSX、CSS、HTML、JSON、GraphQL 等。它提高了代码的可读性，并确保您的代码符合它为您设置的规则。它为您的代码行设置了长度限制；例如，看一下以下单行代码：

```js
hello(reallyLongArg(), omgSoManyParameters(), IShouldRefactorThis(), isThereSeriouslyAnotherOne())
```

上面的代码被认为是单行代码过长且难以阅读，因此 Prettier 将为您重新打印成多行，如下所示：

```js
hello(
  reallyLongArg(),
  omgSoManyParameters(),
  IShouldRefactorThis(),
  isThereSeriouslyAnotherOne()
);
```

此外，任何自定义或混乱的样式也会被解析和重新打印，如下例所示：

```js
fruits({ type: 'citrus' },
  'orange', 'kiwi')

fruits(
  { type: 'citrus' },
  'orange',
  'kiwi'
)
```

Prettier 会将其打印并重新格式化为以下更整洁的格式：

```js
fruits({ type: 'citrus' }, 'orange', 'kiwi');

fruits({ type: 'citrus' }, 'orange', 'kiwi');
```

但是，如果 Prettier 在您的代码中找不到分号，它将为您插入分号，就像前面的示例代码一样。如果您喜欢代码中没有分号，您可以关闭此功能，就像本书中使用的所有代码一样。让我们通过以下步骤关闭这个规则：

1.  通过 npm 将 Prettier 安装到您的项目中：

```js
$ npm i prettier --save-dev --save-exact
```

1.  解析特定的 JavaScript 文件：

```js
$ npx prettier --write src/index.js
```

或者，解析递归文件夹中的所有文件：

```js
$ npx prettier --write "src/**/*"
```

甚至尝试解析并行文件夹中的文件：

```js
$ npx prettier --write "{scripts,config,bin}/**/*"
```

在提交任何**原地**更改（注意！）之前，您可以使用其他输出选项，例如以下选项：

+   使用`-c`或`--check`来检查给定的文件是否格式化，并在之后打印一个人性化的摘要消息，其中包含未格式化文件的路径。

+   使用`-l`或`--list-different`来打印与 Prettier 格式不同的文件的名称。

有关此工具的更多信息，请访问[`prettier.io/`](https://prettier.io/)。

现在让我们看看如何在下一节中配置这个工具。

### 配置 Prettier

Prettier 具有许多自定义选项。您可以通过以下选项配置 Prettier：

+   一个 JavaScript 对象中的`prettier.config.js`或`.prettierrc.js`脚本

+   使用`prettier`键的`package.json`文件

+   一个 YAML 或 JSON 中的`.prettierrc`文件，可选扩展名：`.json`，`.yaml`或`.yml`

+   一个 TOML 中的`.prettierrc.toml`文件

即使您可以选择不这样做，但定制 Prettier 是一个好主意。例如，Prettier 默认强制使用双引号，并在语句末尾打印分号。如果我们不想要这些默认设置，我们可以在项目根目录中创建一个`prettier.config.js`文件。让我们在以下步骤中使用 Prettier 在我们创建的 API 中（我们在 GitHub 存储库的`/chapter-14/apps-to-fix/koa-api/`中制作了一份副本）使用此配置：

1.  在我们的项目根目录中创建一个`prettier.config.js`文件，其中包含以下代码：

```js
// prettier.config.js
module.exports = {
  semi: false,
  singleQuote: true
}
```

1.  使用以下命令解析`/src/`目录中的所有 JavaScript 代码：

```js
$ npx prettier --write "src/**/*"
```

如您所见，当您运行`npx prettier --write "src/**/*"`时，所有我们的文件都会在终端上列出：

```js
src/config/google.js 40ms
src/config/index.js 11ms
src/core/database/mysql.js 18ms
src/index.js 8ms
...
```

Prettier 将突出显示已重新打印和格式化的文件。

有关更多格式选项，请查看[`prettier.io/docs/en/options.html`](https://prettier.io/docs/en/options.html)。您可以在我们的 GitHub 存储库的`/chapter-14/prettier/`中找到此示例。

当您如此轻松地看到您的代码被“美化”时，这是相当不错的，不是吗？让我们继续下一个 linter，ESLint，看看它如何在下一节中帮助整理我们的代码。

## ESLint

ESLint 是一个用于 JavaScript 的可插拔代码检查工具。它被设计成所有规则都是完全可插拔的，并允许开发人员自定义代码检查规则。ESLint 附带了一些内置规则，使其从一开始就很有用，但你可以在任何时候动态加载规则。例如，ESLint 禁止对象字面量中的重复键（`no-dupe-keys`），你将会得到以下代码的错误：

```js
var message = {
  text: "Hello World",
  text: "qux"
}
```

根据这个规则的正确代码将如下所示：

```js
var message = {
  text: "Hello World",
  words: "Hello World"
}
```

ESLint 将标记前面的错误，我们将不得不手动修复它。但是，可以在命令行上使用`--fix`选项来自动修复一些更容易在没有人为干预的情况下修复的问题。让我们看看如何在以下步骤中做到这一点：

1.  在你的项目中通过 npm 安装 ESLint：

```js
$ npm i eslint --save-dev
```

1.  设置一个配置文件：

```js
$ ./node_modules/.bin/eslint --init
```

你将被要求回答类似以下的问题列表：

```js
? How would you like to use ESLint? To check syntax, find problems,
  and enforce code style
? What type of modules does your project use? JavaScript modules (import/export)
? Which framework does your project use? None of these
? Where does your code run? (Press <space> to select, <a> to 
  toggle all, <i> to invert selection)Browser
? How would you like to define a style for your project? Use 
  a popular style guide
? Which style guide do you want to follow? Standard (https://github.com/standard/standard)
? What format do you want your config file to be in? JavaScript
...

Successfully created .eslintrc.js file in /path/to/your/project
```

这些问题可能会根据你为每个问题选择的选项/答案而有所不同。

1.  将`lint`和`lint-fix`脚本添加到`package.json`文件中：

```js
"scripts": {
  "lint": "eslint --ignore-path .gitignore .",
  "lint-fix": "eslint --fix --ignore-path .gitignore ."
}
```

1.  创建一个`.gitignore`文件，包含我们希望 ESLint 忽略的路径和文件：

```js
// .gitignore
node_modules
build
backpack.config.js
```

1.  启动 ESLint 进行错误扫描：

```js
$ npm run lint
```

1.  使用`lint-fix`来修复这些错误：

```js
$ npm run lint-fix
```

你可以在[`eslint.org/docs/rules/`](https://eslint.org/docs/rules/)查看 ESLint 规则列表。ESLint 的规则按类别分组：可能的错误、最佳实践、变量、风格问题、ECMAScript 6 等等。默认情况下没有启用任何规则。你可以在配置文件中使用`"extends": "eslint:recommended"`属性来启用报告常见问题的规则，这些规则在列表中有一个勾号（✓）。

有关此工具的更多信息，请访问[`eslint.org/`](https://eslint.org/)。

现在让我们看看如何在下一节中配置这个工具。

### 配置 ESLint

正如我们之前提到的，ESLint 是一个可插拔的代码检查工具。这意味着它是完全可配置的，你可以关闭每个规则，或其中一些规则，或混合自定义规则，使 ESLint 特别适用于你的项目。让我们在我们创建的 API 中使用 ESLint，并选择以下配置之一。有两种方法来配置 ESLint：

+   在文件中直接使用 JavaScript 注释与 ESLint 配置信息，就像下面的例子一样：

```js
// eslint-disable-next-line no-unused-vars
import authenticate from 'middlewares/authenticate'
```

+   使用 JavaScript、JSON 或 YAML 文件来指定整个目录及其所有子目录的配置信息。

使用第一种方法可能会耗费时间，因为您可能需要在每个`.js`文件中提供 ESLint 配置信息，而在第二种方法中，您只需要在`.json`文件中**一次**配置它。因此，在以下步骤中，让我们使用第二种方法来为我们的 API 进行配置：

1.  创建一个`.eslintrc.js`文件，或者在根目录中使用`--init`生成它，其中包含以下规则：

```js
// .eslintrc.js
module.exports = {
  'rules': {
    'no-undef': ['off'],
    'no-console': ['error']
    'quotes': ['error', 'double']
  }
}
```

在这些规则中，我们希望确保执行以下操作：

+   通过将`no-undef`选项设置为`off`来允许未声明的变量(`no-undef`)

+   通过将`no-console`选项设置为`error`来禁止使用控制台(`no-console`)

+   强制使用反引号、双引号或单引号(`quotes`)，将`quotes`选项设置为`error`和`double`

1.  将`lint`和`lint-fix`脚本添加到`package.json`文件中：

```js
// package.json
"scripts": {
  "lint": "eslint --ignore-path .gitignore .",
  "lint-fix": "eslint --fix --ignore-path .gitignore ."
}
```

1.  启动 ESLint 进行错误扫描：

```js
$ npm run lint
```

如果有任何错误，您将收到以下类似的报告：

```js
/src/modules/public/login/_routes/google/me.js 
   36:11  error  A space is required after '{'  object-
          curly-spacing 
   36:18  error  A space is required before '}' object-
          curly-spacing 
```

尽管 ESLint 可以使用`--fix`选项自动修复您的代码，但您仍然需要手动修复一些，就像以下示例中一样：

```js
/src/modules/public/user/_routes/fetch-user.js 
  9:9  error  'id' is assigned a value but never used  
       no-unused-vars 
```

有关配置的更多信息，请查看[`eslint.org/docs/user-guide/configuring`](https://eslint.org/docs/user-guide/configuring)。您可以在我们的 GitHub 存储库的`/chapter-14/eslint/`中找到此示例。

它用户友好，不是吗？它确实是另一个像 Prettier 一样令人敬畏的工具。让我们继续介绍最后一个代码检查器 StandardJS，看看它如何整理我们的代码。

## StandardJS

StandardJS 或 JavaScript 标准样式是 JavaScript 样式指南、代码检查器和格式化程序。它完全是主观的，这意味着它是完全不可定制的 - 不需要配置，因此没有`.eslintrc`、`.jshintrc`或`.jscsrc`文件来管理。它是不可定制和不可配置的。使用 StandardJS 的最简单方法是将其作为 Node 命令行程序全局安装。让我们看看您可以如何在以下步骤中使用此工具：

1.  通过 npm 全局安装 StandardJS：

```js
$ npm i standard --global
```

您还可以为单个项目在本地安装它：

```js
$ npm i standard --save-dev
```

1.  导航到要检查的目录，并在终端中输入以下命令：

```js
$ standard
```

1.  如果您在本地安装了 StandardJS，则使用`npx`来运行它：

```js
$ npx standard
```

您还可以将其添加到`package.json`文件中，如下所示：

```js
// package.json
{
  scripts": {
    "jss": "standard",
    "jss-fix": "standard --fix"
  },
  "devDependencies": {
    "standard": "¹².0.1"
  },
  "standard": {
    "ignore": [
      "/node_modules/",
      "/build/",
      "backpack.config.js"
    ]
  }
}
```

1.  然后，当您使用 npm 运行 JavaScript 项目的代码时，代码将被自动检查：

```js
$ npm run jss
```

要修复任何混乱或不一致的代码，请尝试以下命令：

```js
$ npm run jss-fix
```

尽管 StandardJS 是不可定制的，但它依赖于 ESLint。StandardJS 使用的 ESLint 包如下：

+   `eslint`

+   `standard-engine`

+   `eslint-config-standard`

+   `eslint-config-standard-jsx`

+   `eslint-plugin-standard`

虽然 Prettier 是一个格式化工具，StandardJS 大多是一个类似 ESLint 的 linter。如果你在你的代码上使用`--fix`来修复 StandardJS 或 ESLint，然后再用 Prettier 运行它，你会发现任何长行（这些行被 StandardJS 和 ESLint 忽略）将被 Prettier 格式化。

有关此工具的更多信息，请访问[`standardjs.com/`](https://standardjs.com/)。你还应该查看标准 JavaScript 规则的摘要，网址为[`standardjs.com/rules.html`](https://standardjs.com/rules.html)。你可以在我们的 GitHub 存储库的`/chapter-14/standard/`中找到一个使用 StandardJS 的示例。

然而，如果你正在寻找一个更灵活和可定制的解决方案，介于这些工具之间，你可以为你的项目结合使用 Prettier 和 ESLint。让我们在下一节看看你如何实现这一点。

# 集成 ESLint 和 Prettier

Prettier 和 ESLint 相辅相成。我们可以将 Prettier 集成到 ESLint 的工作流中。这样你就可以使用 Prettier 来格式化你的代码，同时让 ESLint 专注于 linting 你的代码。因此，为了集成它们，首先我们需要从 ESLint 中使用`eslint-plugin-prettier`插件来使用 Prettier。然后我们可以像往常一样使用 Prettier 来添加格式化代码的规则。

然而，ESLint 包含与 Prettier 冲突的格式相关的规则，比如`arrow-parens`和`space-before-function-paren`，在一起使用时可能会引起一些问题。为了解决这些冲突问题，我们需要使用`eslint-config-prettier`配置来关闭与 Prettier 冲突的 ESLint 规则。让我们在以下步骤中看看你如何实现这一点：

1.  通过 npm 安装`eslint-plugin-prettier`和`eslint-config-prettier`：

```js
$ npm i eslint-plugin-prettier --save-dev
$ npm i eslint-config-prettier --save-dev
```

1.  在`.eslintrc.json`文件中启用`eslint-plugin-prettier`的插件和规则：

```js
{
  "plugins": ["prettier"],
  "rules": {
    "prettier/prettier": "error"
  }
}
```

1.  使用`eslint-config-prettier`在`.eslintrc.json`文件中通过扩展 Prettier 的规则来覆盖 ESLint 的规则：

```js
{
  "extends": ["prettier"]
}
```

请注意，值"`prettier`"应该放在`extends`数组的最后，以便 Prettier 的配置可以覆盖 ESLint 的配置。此外，我们可以使用`.eslintrc.js`文件而不是 JSON 文件来进行上述配置，因为我们可以在 JavaScript 文件中添加有用的注释。因此，以下是我们在 ESLint 下使用 Prettier 的配置：

```js
// .eslintrc.js
module.exports = {
  //...
  'extends': ['prettier']
  'plugins': ['prettier'],
  'rules': {
    'prettier/prettier': 'error'
  }
}
```

1.  在`package.json`文件（或`prettier.config.js`文件）中配置 Prettier，以便 Prettier 不会在我们的代码中打印分号，并始终使用单引号：

```js
{
  "scripts": {
    "lint": "eslint --ignore-path .gitignore .",
    "lint-fix": "eslint --fix --ignore-path .gitignore ."
  },
  "prettier": {
    "semi": false,
    "singleQuote": true
  }
}
```

1.  在终端上运行`npm run lint-fix`以一次性修复和格式化我们的代码。之后，您可以使用`npx prettier`命令再次仅使用 Prettier 检查代码：

```js
$ npx prettier --c "src/**/*"
```

然后您应该在终端上获得以下结果：

```js
Checking formatting...
All matched files use Prettier code style!
```

这意味着我们的代码没有格式问题，并且在 Prettier 代码样式中成功编译。将这两个工具结合起来以满足我们的需求和偏好是非常酷的，不是吗？但是您仍然只完成了一半-让我们在下一节中为 Vue 和 Nuxt 应用程序应用这些配置。

您可以在我们的 GitHub 存储库的`/chapter-14/eslint+prettier/`中找到此集成示例。

# 在 Vue 和 Nuxt 应用程序中使用 ESLint 和 Prettier

eslint-plugin-vue 插件是 Vue 和 Nuxt 应用程序的官方 ESLint 插件。它允许我们使用 ESLint 检查`.vue`文件中`<template>`和`<script>`块中的代码，以查找任何语法错误，错误使用 Vue 指令以及违反 Vue 风格指南的 Vue 样式。此外，我们正在使用 Prettier 来强制执行代码格式，因此像我们在上一节中所做的那样安装`eslint-plugin-prettier`和`eslint-config-prettier`以获取我们喜欢的基本特定配置。让我们在以下步骤中解决所有这些问题：

1.  使用 npm 安装`eslint-plugin-vue`插件：

```js
$ npm i eslint-plugin-vue --save-dev
```

您可能会收到一些警告：

```js
npm WARN eslint-plugin-vue@5.2.3 requires a peer of eslint@⁵.0.0
 but none is installed. You must install peer dependencies
  yourself.
npm WARN vue-eslint-parser@5.0.0 requires a peer of eslint@⁵.0.0 
 but none is installed. You must install peer dependencies 
  yourself.
```

忽略它们，因为`eslint-plugin-vue`的最低要求是 ESLint v5.0.0 或更高版本和 Node.js v6.5.0 或更高版本，而您应该已经拥有最新版本。

您可以在[`eslint.vuejs.org/user-guide/installation`](https://eslint.vuejs.org/user-guide/#installation)查看最低要求。除了 Vue 风格指南，您还应该查看[`eslint.vuejs.org/rules/`](https://eslint.vuejs.org/rules/)上的 Vue 规则。

1.  在 ESLint 配置文件中添加`eslint-plugin-vue`插件及其通用规则集：

```js
// .eslintrc.js
module.exports = {
  extends: [
    'plugin:vue/recommended'
  ]
}
```

1.  安装 `eslint-plugin-prettier` 和 `eslint-config-prettier` 并将它们添加到 ESLint 配置文件中：

```js
// .eslintrc.js
module.exports = {
  'extends': [
    'plugin:vue/recommended',
    'plugin:prettier/recommended'
  ],
  'plugins': [
    'prettier'
  ]
}
```

但这些还不够。您可能希望配置一些 Vue 规则以适应您的偏好。让我们在下一节中找出一些默认的 Vue 关键规则，我们可能希望配置。

有关此 `eslint-plugin-vue` 插件的更多信息，请访问 [`eslint.vuejs.org/`](https://eslint.vuejs.org/)。有关 Vue 指令，请访问 [`vuejs.org/v2/api/Directives`](https://vuejs.org/v2/api/#Directives)，有关 Vue 风格指南，请访问 [`vuejs.org/v2/style-guide/`](https://vuejs.org/v2/style-guide/)。

## 配置 Vue 规则

在本书中，我们只想覆盖四个默认的 Vue 规则。您只需要在 `.eslintrc.js` 文件的 `'rules'` 选项中添加首选规则，就像我们在上一节中为 `eslint-plugin-prettier` 插件所做的那样。让我们按照以下步骤进行：

1.  将 `vue/v-on-style` 规则配置为 "`longform`" 如下：

```js
// .eslintrc.js
'rules': {
  'vue/v-on-style': ['error', 'longform']
}
```

`vue/v-on-style` 规则强制在 `v-on` 指令样式上使用 `shorthand` 或 `longform`。默认设置为 `shorthand`，例如：

```js
<template>
  <!-- ✓ GOOD -->
  <div @click="foo"/>

  <!-- ✗ BAD -->
  <div v-on:click="foo"/>
</template>
```

但在本书中，首选 `longform`，如下例所示：

```js
<template>
  <!-- ✓ GOOD -->
  <div v-on:click="foo"/>

  <!-- ✗ BAD -->
  <div @click="foo"/>
</template>
```

有关此规则的更多信息，请访问 [`eslint.vuejs.org/rules/v-on-style.htmlvue-v-on-style`](https://eslint.vuejs.org/rules/v-on-style.html#vue-v-on-style)。

1.  将 `vue/html-self-closing` 规则配置为允许在空元素上使用自闭合符号如下：

```js
// .eslintrc.js
'rules': {
  'vue/html-self-closing': ['error', {
    'html': {
      'void': 'always'
    }
  }]
}
```

空元素是 HTML 元素，在任何情况下都不允许有内容，例如 `<br>`、`<hr>`、`<img>`、`<input>`、`<link>` 和 `<meta>`。在编写 XHTML 时，必须自闭这些元素，例如 `<br/>` 和 `<img src="..." />`。在本书中，即使在 HTML5 中，`/` 字符被认为是可选的，我们也希望允许这样做。

根据 `vue/html-self-closing` 规则，自闭合这些空元素将导致错误，尽管它旨在强制 HTML 元素中的自闭合符号。这相当令人困惑，对吧？在 Vue.js 模板中，我们可以使用以下两种样式来表示没有内容的元素：

+   +   `<YourComponent></YourComponent>`

+   `<YourComponent/>`（自闭合）

根据此规则，第一个选项将被拒绝，如下例所示：

```js
<template>
  <!-- ✓ GOOD -->
  <MyComponent/>

  <!-- ✗ BAD -->
  <MyComponent></MyComponent>
</template>
```

然而，它也拒绝了自闭合的空元素：

```js
<template>
  <!-- ✓ GOOD -->
  <img src="...">

  <!-- ✗ BAD -->
  <img src="..." />
</template>
```

换句话说，在 Vue 规则中，不允许空元素具有自闭合标记。因此，默认情况下，`html.void`选项的值设置为`'never'`。因此，如果您想要允许这些空元素上的自闭合标记，就像本书中一样，那么将值设置为`'always'`。

有关此规则的更多信息，请访问[`eslint.vuejs.org/rules/html-self-closing.htmlvue-html-self-closing`](https://eslint.vuejs.org/rules/html-self-closing.html#vue-html-self-closing)。

1.  将`vue/max-attributes-per-line`规则配置为关闭此规则如下：

```js
// .eslintrc.js
'rules': {
  'vue/max-attributes-per-line': 'off'
}
```

`vue/max-attributes-per-line`规则旨在强制每行一个属性。默认情况下，当两个属性之间有换行时，认为属性在新行中。以下是在此规则下的示例：

```js
<template>
  <!-- ✓ GOOD -->
  <MyComponent lorem="1"/>
  <MyComponent
    lorem="1"
    ipsum="2"
  />
  <MyComponent
    lorem="1"
    ipsum="2"
    dolor="3"
  />

  <!-- ✗ BAD -->
  <MyComponent lorem="1" ipsum="2"/>
  <MyComponent
    lorem="1" ipsum="2"
  />
  <MyComponent
    lorem="1" ipsum="2"
    dolor="3"
  />
</template>
```

然而，此规则与 Prettier 冲突。我们应该让 Prettier 处理这样的情况，这就是为什么我们会关闭这个规则。

有关此规则的更多信息，请访问[`eslint.vuejs.org/rules/max-attributes-per-line.htmlvue-max-attributes-per-line`](https://eslint.vuejs.org/rules/max-attributes-per-line.html#vue-max-attributes-per-line)。

1.  配置`eslint/space-before-function-paren`规则：

```js
// .eslintrc.js
'rules': {
  'space-before-function-paren': ['error', 'always']
}
```

`eslint/space-before-function-paren`规则旨在强制在函数声明的括号前添加一个空格。ESLint 默认行为是添加空格，这也是 StandardJS 中定义的规则。请参阅以下示例：

```js
function message (text) { ... } // ✓ ok
function message(text) { ... } // ✗ avoid

message(function (text) { ... }) // ✓ ok
message(function(text) { ... }) // ✗ avoid
```

然而，在前述规则下，当您使用 Prettier 时，您将会收到以下错误：

```js
/middleware/auth.js
  1:24 error Delete · prettier/prettier
```

我们将忽略 Prettier 的错误，因为我们想要遵循 Vue 中的规则。但是目前，Prettier 还没有选项来禁用这个规则，可以从[`prettier.io/docs/en/options.html`](https://prettier.io/docs/en/options.html)查看。如果因为 Prettier 而删除了空格，您可以通过在 Vue 规则下将值设置为`'always'`来添加回来。

有关此规则的更多信息，请访问[`eslint.org/docs/rules/space-before-function-paren`](https://eslint.org/docs/rules/space-before-function-paren)和[`standardjs.com/rules.html`](https://standardjs.com/rules.html)。

1.  因为 ESLint 默认只针对`.js`文件，所以在 ESLint 命令中使用`--ext`选项（或者 glob 模式）包含`.vue`扩展名，以在终端上运行 ESLint 并使用前述配置。

```js
$ eslint --ext .js,.vue src
$ eslint "src/**/*.{js,vue}"
```

您还可以在`package.json`文件中的`.gitignore`选项中使用自定义命令来运行它，如下所示：

```js
// package.json
"scripts": {
  "lint": "eslint --ext .js,.vue --ignore-path .gitignore .",
  "lint-fix": "eslint --fix --ext .js,.vue --ignore-path 
   .gitignore ."
}

// .gitignore
node_modules
build
nuxt.config.js
prettier.config.js
```

ESLint 将忽略在前面的`.gitignore`片段中定义的文件，同时对所有 JavaScript 和 Vue 文件进行 lint。通过 webpack 进行热重载时对文件进行 lint 是一个好主意。只需将以下片段添加到 Nuxt 配置文件中，以便在保存代码时运行 ESLint：

```js
// nuxt.config.js
...
build: {
 extend(config, ctx) {
    if (ctx.isDev && ctx.isClient) {
      config.module.rules.push({
        enforce: "pre",
        test: /\.(js|vue)$/,
        loader: "eslint-loader",
        exclude: /(node_modules)/
      })
    }
  }
}
```

您可以在我们的 GitHub 存储库的`/chapter-14/eslint-plugin-vue/integrate/`中找到一个使用此插件与 ESLint 的示例。

正如您在本节和前几节中所看到的，将 ESLint 和 Prettier 混合在单个配置文件中可能会有问题。您可能得到的麻烦可能不值得让它们“作为一个团队”一起工作。为什么不尝试在不耦合它们的情况下分别运行它们呢？让我们在下一节中找出如何为 Nuxt 应用程序做到这一点。

## 在 Nuxt 应用程序中分别运行 ESLint 和 Prettier

另一个解决 ESLint 和 Prettier 之间冲突的可能解决方案，特别是在`space-before-function-paren`上，是根本不集成它们，而是分别运行它们来格式化和检查我们的代码。所以让我们在以下步骤中让它们正常工作：

1.  在`package.json`文件中分别为 Prettier 和 ESLint 创建脚本，如下所示：

```js
// package.json
"scripts": {
"prettier": "prettier --check \"
 {components,layouts,pages,store,middleware,plugins}/**/*.{vue,js}
   \"", "prettier-fix": "prettier --write 
   {components,layouts,pages,store,middleware,plugins}
    /**/*.{vue,js}\"", "lint": "eslint --ext .js,.vue 
    --ignore-path .gitignore .",
   "lint-fix": "eslint --fix --ext .js,.vue --ignore-path
     .gitignore ."
}
```

现在我们可以完全忘记`eslint-plugin-prettier`和我们工作流程中的`eslint-config-prettier`配置。我们仍然保留`eslint-plugin-vue`和在本章中已经配置的规则，但是完全从`.eslintrc.js`文件中删除 Prettier：

```js
// .eslintrc.js
module.exports = {
  //...
  'extends': [
    'standard',
    'plugin:vue/recommended',
    // 'prettier' // <- removed this.
  ]
}
```

1.  当我们想要分析我们的代码时，先运行 Prettier，然后运行 ESLint：

```js
$ npm run prettier
$ npm run lint
```

1.  再次，当我们想要修复格式并对我们的代码进行 lint 时，先运行 Prettier，然后运行 ESLint：

```js
$ npm run prettier-fix
$ npm run lint-fix
```

您可以看到，这个解决方案以这种方式使我们的工作流程更清晰和更干净。不再有冲突-一切都很顺利。很好。

您可以在我们的 GitHub 存储库的`/chapter-14/eslint-plugin-vue/separate/`中找到一个分别运行 ESLint 和 Prettier 的示例。

干得好。您已经完成了本章的第一个重要部分。我们希望您将开始或已经开始为您的 Vue 和 Nuxt 应用程序编写美观且易读的代码，并利用这些令人惊叹的格式化程序和代码检查工具。随着本书中关于 Nuxt 的学习接近尾声，我们将在下一节中向您介绍如何部署 Nuxt 应用程序。所以请继续阅读。

# 部署 Nuxt 应用程序

除了代码检查和格式化之外，应用程序部署也是 Web 开发工作流的一部分。我们需要将应用程序部署到远程服务器或主机上，以便公众可以公开访问应用程序。Nuxt 带有内置命令，我们可以使用这些命令来部署我们的应用程序。它们如下：

+   `nuxt`

+   `nuxt build`

+   `nuxt start`

+   `nuxt generate`

`nuxt`命令是您现在在终端上熟悉使用的命令：

```js
$ npm run dev
```

如果您打开使用`create-nuxt-app`脚手架工具安装项目时 Nuxt 生成的`package.json`文件，您会看到这些命令预先配置在`"scripts"`片段中，如下所示：

```js
// package.json
"scripts": {
  "dev": "nuxt",
  "build": "nuxt build",
  "start": "nuxt start",
  "generate": "nuxt generate"
}
```

您可以使用以下 Node.js 命令行在终端上启动命令：

```js
$ npm run <command>
```

`nuxt`命令用于在开发服务器上进行开发，并具有热重新加载功能，而其他命令用于生产部署。让我们看看如何在下一节中使用它们来部署您的 Nuxt 应用。

您还可以在任何这些命令中使用常见参数，例如`--help`。如果您想了解更多信息，请访问[`nuxtjs.org/guide/commandslist-of-commands`](https://nuxtjs.org/guide/commands#list-of-commands)。

## 部署 Nuxt 通用服务器端渲染应用程序

希望通过学习之前的所有章节，您知道自己一直在开发 Nuxt 通用**服务器端渲染**（**SSR**）应用程序。SSR 应用程序是在服务器端呈现应用程序内容的应用程序。这种应用程序需要特定的服务器来运行您的应用程序，例如 Node.js 和 Apache 服务器，而像您使用 Nuxt 创建的通用 SSR 应用程序在服务器端和客户端上都可以运行。这种应用程序也需要特定的服务器。使用 Nuxt 通用 SSR 应用程序可以在终端上使用两个命令进行部署。让我们看看您可以在以下步骤中如何执行此操作：

1.  通过 npm 启动`nuxt build`命令来使用 webpack 构建应用程序并压缩 JavaScript 和 CSS：

```js
$ npm run build
```

您应该获得以下构建结果：

```js
> [your-app-name]@[your-app-name] start /var/path/to/your/app
> nuxt build
ℹ Production build
ℹ Bundling for server and client side
ℹ Target: server 
✓ Builder initialized
✓ Nuxt files generated
...
...
```

1.  通过 npm 启动`nuxt start`命令以生产模式启动服务器：

```js
$ npm run start
```

您应该获得以下启动状态：

```js
> [your-app-name]@[your-app-name] start /var/path/to/your/app
> nuxt start

Nuxt.js @ v2.14.0

> Environment: production
> Rendering: server-side
> Target: server

Memory usage: 28.8 MB (RSS: 88.6 MB)
```

部署 Nuxt 通用 SSR 应用程序只需要两条命令行。这很容易，不是吗？但是，如果您没有 Node.js 服务器来托管您的应用程序，或者出于任何原因，您只想将应用程序部署为静态站点，您可以从 Nuxt 通用 SSR 应用程序生成它。让我们在下一节中了解如何实现这一点。

## 部署 Nuxt 静态生成（预渲染）应用程序

要从 Nuxt 通用 SSR 应用程序生成 Nuxt 静态生成的应用程序，我们将使用我们在前几章中为此练习创建的示例网站。您可以在我们的 GitHub 存储库的`/chapter-14/deployment/sample-website/`中找到此示例。因此，让我们按照以下步骤开始：

1.  确保您的`package.json`文件中有以下`"generate"`运行脚本：

```js
"scripts": {
  "generate": "nuxt generate"
} 
```

1.  将 Nuxt 配置文件中的`target`项目更改为`static`：

```js
// nuxt.config.js
export default {
  target: 'static'
}
```

1.  通过在 Nuxt 配置文件中配置`generate`选项来生成 404 页面：

```js
// nuxt.config.js
export default {
  generate: {
    fallback: true
  }
}
```

Nuxt 不会生成您的自定义 404 页面，也不会生成默认页面。如果您想在静态应用程序中包含此页面，可以在配置文件中的`generate`选项中设置`fallback: true`。

1.  通过 npm 启动`nuxt generate`命令来构建应用程序并为每个路由生成 HTML 文件：

```js
$ npm run generate
```

Nuxt 具有一个爬虫，它会扫描链接并为您自动生成动态路由及其异步内容（使用`asyncData`和`fetch`方法呈现的数据）。因此，您应该按以下方式获取应用程序的每个路由：

```js
ℹ Generating output directory: dist/
ℹ Generating pages with full static mode
✓ Generated route "/contact"
✓ Generated route "/work-nested"
✓ Generated route "/about"
✓ Generated route "/work"
✓ Generated route "/"
✓ Generated route "/work-nested/work-sample-4"
✓ Generated route "/work-nested/work-sample-1"
✓ Generated route "/work-nested/work-sample-3"
✓ Generated route "/work-nested/work-sample-2"
✓ Generated route "/work/work-sample-1"
✓ Generated route "/work/work-sample-4"
✓ Generated route "/work/work-sample-2"
✓ Generated route "/work/work-sample-3"
✓ Client-side fallback created: 404.html
i Ready to run nuxt serve or deploy dist/ directory 
```

请注意，您仍然需要使用`generate.routes`来生成爬虫无法检测到的路由。

1.  如果您查看项目根目录，您应该会发现 Nuxt 生成的`/dist/`文件夹，其中包含部署应用程序到静态托管服务器所需的一切。但在此之前，您可以使用终端上的`nuxt serve`命令从`/dist/`目录测试生产静态应用程序：

```js
$ npm run start
```

您应该在终端上获得以下输出：

```js
Nuxt.js @ v2.14.0 

> Environment: production
> Rendering: server-side
> Target: static
Listening: http://localhost:3000/

ℹ Serving static application from dist/ 
```

1.  现在，您可以将浏览器指向`localhost:3000`，并查看应用程序是否像 SSR 一样运行，但实际上，它是一个静态生成的应用程序。

我们将在下一章回到这个配置，用于部署 Nuxt **单页面应用程序**（**SPA**）应用。您可以看到，选择这种部署方式只需要做一点工作，但完全值得，因为以“静态”方式部署您的应用程序有好处，比如您可以将静态文件托管在静态托管服务器上，这相对便宜于 Node.js 服务器。我们将在下一章向您展示如何在这种服务器上为您的静态站点提供服务，就像**GitHub Pages**一样。尽管以“静态”方式部署 Nuxt 通用 SSR 应用程序有好处，但您必须考虑以下注意事项：

+   给`asyncData`和`fetch`方法的 Nuxt 上下文将失去来自 Node.js 的 HTTP `req`和`res`对象。

+   `nuxtServerInit`操作将不可用于存储。

因此，如果您的 Nuxt 应用程序在上述列表中严重依赖这些项目，那么将 Nuxt 通用 SSR 应用程序生成为静态文件可能不是一个好主意，因为它们是服务器端功能。但是，我们可以在客户端使用客户端 cookie 模仿`nuxtServerInit`操作，我们也将在下一章向您展示。但现在，让我们继续前进到下一节，找出您可以选择的托管服务器类型来托管您的 Nuxt 应用程序。

如果您想了解有关`generate`属性/选项和其他选项的更多信息，例如您可以使用此属性进行配置的`fallback`和`routes`选项，请访问[`nuxtjs.org/api/configuration-generate`](https://nuxtjs.org/api/configuration-generate)。

## 在虚拟专用服务器上托管 Nuxt 通用 SSR 应用程序。

在托管 Node.js 应用程序时，**虚拟专用服务器**（**VPS**）和专用服务器是更好的选择，因为您将完全自由地为您的应用程序设置 Node.js 环境。每当 Node.js 发布新版本时，您也应该更新您的环境。只有使用 VPS 服务器，您才能随时升级和调整您的环境。

如果您正在寻找 Linux 服务器并且希望从头开始安装您需要的基础设施，VPS 提供商如 Linode 或 Vultr 提供了实惠的 VPS 主机定价。这些 VPS 提供商提供给您的是一个空的虚拟机，您可以选择您喜欢的 Linux 发行版，例如 Ubuntu。构建您所需基础设施的过程与在本地机器上刚刚安装 Linux 发行版时安装 Node.js、MongoDB、MySQL 等的过程是一样的。有关这些 VPS 提供商的更多信息，请访问以下链接：

+   [`welcome.linode.com/`](https://welcome.linode.com/) for Linode

+   [`www.vultr.com/`](https://www.vultr.com/) for Vultr

在满足您的要求的 Node.js 环境和基础设施设置好之后，您可以将 Nuxt 应用程序上传到这种类型的主机，然后通过这些主机提供的**安全外壳**（**SSH**）功能在终端上轻松构建和启动应用程序：

```js
$ npm run build
$ npm run start
```

共享主机服务器怎么样？让我们看看下一节中你可以选择的内容。

## 在共享主机服务器上托管 Nuxt 通用 SSR 应用程序

记住，并非所有主机都支持 Node.js，并且与支持 PHP 的共享主机服务器相比，支持 Node.js 的共享主机服务器相对较少。但所有共享主机服务器都是一样的-通常你所能做的事情受到严格限制，你必须遵循提供者制定的严格规则。您可以查看以下共享主机服务器提供商：

+   **Reclaim Hosting**，网址为[`reclaimhosting.com/shared-hosting/`](https://reclaimhosting.com/shared-hosting/)

+   **A2 Hosting**，网址为[`www.a2hosting.com/nodejs-hosting`](https://www.a2hosting.com/nodejs-hosting)

例如，在 Reclaim Hosting 的共享主机服务器上，您很可能无法运行 Nuxt 命令来启动您的应用程序。相反，您需要向服务器提供一个应用程序启动文件，这个文件必须被称为`app.js`并放在您的项目根目录中。

如果您想选择 Reclaim Hosting，您可以使用他们的测试环境[`stateu.org/`](https://stateu.org/)来看看它对您的工作方式。但请记住，高级设置是不可能的。好消息是，Nuxt 提供了一个 Node.js 模块`nuxt-start`，可以在这样的共享主机服务器上以生产模式启动 Nuxt 应用程序。所以让我们在以下步骤中找出如何做：

1.  通过 npm 在本地安装`nuxt-start`：

```js
$ npm i nuxt-start
```

1.  在你的项目根目录中创建一个`app.js`文件，并使用以下代码启动 Nuxt 应用程序：

```js
// app.js
const { Nuxt } = require('nuxt-start')
const config = require('./nuxt.config.js')

const nuxt = new Nuxt(config)
const { host, port } = nuxt.options.server

nuxt.listen(port, host)
```

或者，你可以使用 Express 或 Koa 来启动你的 Nuxt 应用。以下示例假设你正在使用 Express：

```js
// app.js
const express = require('express')
const { Nuxt } = require('nuxt')
const app = express()

let config = require('./nuxt.config.js')
const nuxt = new Nuxt(config)
const { host, port } = nuxt.options.server

app.use(nuxt.render)
app.listen(port, host)
```

在这段代码中，我们导入了`express`和`nuxt`模块以及`nuxt.config.js`文件，然后将 Nuxt 应用程序用作中间件。如果你使用 Koa，情况也是一样的 - 你只需要将 Nuxt 用作中间件。

1.  使用`app.js`文件将 Nuxt 应用程序上传到服务器，并按照主机的说明通过 npm 安装应用程序依赖项，然后运行`app.js`启动你的应用程序。

这就是你需要做的全部。这些共享主机服务器存在一些限制。在这些服务器中，你对 Node.js 环境的控制较少。但是，如果你遵循服务器提供商设定的严格规则，你可以让你的通用 SSR Nuxt 应用程序快速运行起来。

你可以在我们的 GitHub 存储库中的`/chapter-14/deployment/shared-hosting/reclaimhosting.com/`中找到上述示例代码和其他示例代码，用于在 Reclaim Hosting 上托管 Nuxt 通用 SSR 应用程序。

有关`nuxt-start`的更多信息，请访问[`www.npmjs.com/package/nuxt-start`](https://www.npmjs.com/package/nuxt-start)。

你可以看到它并不完美，并且有其局限性，但如果你正在寻找共享主机，这是合理的。如果这对你来说不理想，那么最后的选择是选择静态站点主机，我们将在下一节中看到。

## 在静态站点主机上托管 Nuxt 静态生成的应用程序

通过这种方式，你将失去 Nuxt 的服务器端。但好消息是，有许多流行的主机可以托管静态生成的 Nuxt 应用程序，并且你可以快速在几乎任何在线主机上提供服务。让我们在以下步骤中看看如何做到这一点：

1.  在 Nuxt 配置文件中将`server`更改为`static`作为目标。

```js
// nuxt.config.js
export default {
  target: 'static'
}
```

1.  通过 npm 在本地启动`nuxt generate`命令来生成 Nuxt 应用程序的静态文件：

```js
$ npm run generate
```

1.  将 Nuxt 生成的`/dist/`文件夹中的所有内容上传到主机。

以下列表详细介绍了你可以选择的主机。所有这些主机的部署过程都在 Nuxt 网站上有详细说明。你应该查看 Nuxt FAQ [`nuxtjs.org/faq`](https://nuxtjs.org/faq) 来查看部署示例，并了解如何将静态生成的 Nuxt 应用程序部署到这些特定主机中的任何一个：

+   AWS w/ S3 (Amazon Web Services) at [`nuxtjs.org/faq/deployment-aws-s3-cloudfront`](https://nuxtjs.org/faq/deployment-aws-s3-cloudfront)

+   GitHub Pages at [`nuxtjs.org/faq/github-pages`](https://nuxtjs.org/faq/github-pages)

+   Netlify at [`nuxtjs.org/faq/netlify-deployment`](https://nuxtjs.org/faq/netlify-deployment)

+   Surge at [`nuxtjs.org/faq/surge-deployment`](https://nuxtjs.org/faq/surge-deployment)

在下一章中，我们将指导您在 GitHub Pages 上部署 Nuxt SPA 应用程序。但现在，这是本章关于格式化、检查和部署 Nuxt 通用 SSR 应用程序的结束。让我们总结一下您在本章中学到的内容。

# 摘要

干得好。你已经走了这么远。这是一段相当漫长的旅程。在本章中，我们涵盖了 JavaScript 的检查器和格式化程序，特别是 ESLint，Prettier 和 StandardJS 用于 Nuxt 应用程序以及一般的 JavaScript 应用程序。您已经学会了如何安装和配置它们以满足您的需求和偏好。我们还介绍了部署 Nuxt 应用程序的 Nuxt 命令以及可用于托管 Nuxt 应用程序的选项，无论是通用 SSR 应用程序还是静态生成的站点。

在接下来的章节中，我们将学习如何使用 Nuxt 创建 SPA，并将其部署到 GitHub Pages。您将看到传统 SPA 和 Nuxt 中 SPA（让我们称之为**Nuxt SPA**）之间的细微差别。我们将指导您完成在 Nuxt 中设置 SPA 开发环境的过程，重构您在前几章中创建的通用 SSR Nuxt 身份验证应用程序，并将其转换为 Nuxt SPA 和静态生成的 Nuxt SPA。最后，您将学会将静态生成的 SPA 部署到 GitHub Pages。所以请继续阅读。
