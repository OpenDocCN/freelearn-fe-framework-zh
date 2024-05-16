# 第六章：在 Vue CLI 3 中使用 ESLint 和 Prettier

在本章中，我们将学习 ESLint 的用处，并看看如何使用它对我们的代码进行 lint。我们还将讨论不同的风格指南：标准、Airbnb 和 Google。具体来说，我们将看以下主题：

+   ESLint 是什么，以及如何单独配置它

+   本地安装 npm 包和全局安装 npm 包之间的区别

+   本地安装 ESLint 并使用 npx 运行它

+   在 Vue CLI 3 中使用 ESLint

+   使用 Vue CLI 3 UI 配置 ESLint

我们将首先看一下 ESLint 是什么，以及如何将其配置为一个独立的`npm`包。

# ESLint 是什么，以及如何单独配置它

在本章中，我们将讨论 ESLint 的作用。ESLint 与代码质量有关。当你在一个团队中工作时，每个团队成员都会带来自己对于如何编写 JavaScript 的最佳方式的小怪癖和想法。即使你的团队对特定的编码风格和如何编写代码有明确的规则，你如何保证每个团队成员都遵守规则？你如何确保他们提供一致的代码质量？毕竟，我们都是人类，这意味着我们都有那些我们尽力了，但简单地忘记了要注意的小事情的日子。

在足够长的时间内，我们的团队将开始产生风格不一致的代码。接下来会发生的事情是，团队中的每个人开始将 JavaScript 风格指南视为*建议*，而不是你应该*真正遵守*的东西。

幸运的是，有一些工具可以让我们自动改善代码的外观，并在不遵循规定的做事方式时对其进行格式化。ESLint 就是这样一个工具的例子。

基本上，ESLint 是让你把*制表符与空格*等类似辩论外包给一款软件的方式，这款软件将以一种流畅、一致的方式处理这些问题。

在本章中，我们还将简要提到全局和本地 node 模块之间的区别，即全局安装`npm`包和本地安装之间的区别。

# 全局安装 npm 包与本地安装 npm 包的区别

这是一个全局安装的例子：

```js
npm install eslint -g --verbose
```

这是一个本地安装的例子：

```js
npm install eslint --save-dev --verbose
```

那么，有什么区别呢？

全局包安装在计算机的一个特定位置。无论您在安装它们时所在的文件夹是什么，*它们都不会保存在该文件夹内*，并且在命令行中全局可用。因此，如果我们想要从命令行运行一个包，全局安装是一种方法。

您可以从命令行程序中运行`npm install`命令。命令行程序将始终从一个目录内运行。使用诸如`cd ..`来在您的操作系统文件系统中返回上一级目录，或者`cd <directory-name>`来进入一个目录，您可以选择在安装`npm`包时希望您的命令行程序所在的文件夹。

将`npm`包本地安装意味着该包保存在当前命令行程序指向的目录内。当运行`npm install`命令时，您正在安装的新包将保存在您所在的当前目录的`node_modules`目录中。如果`node_modules`目录不存在，当运行`npm install`命令时，它将被添加*在当前目录内*。

使用全局模块的缺点是版本问题。对于本地模块，您计算机上的每个不同目录和每个不同应用程序都有自己的模块版本。然而，如果您有一个全局包，并决定更新它，全局安装的模块的更新版本可能会因为与特定目录中特定应用程序中其余代码的兼容性问题而破坏您的应用程序。

作为一个经验法则，应该将`npm`包安装在本地，因为可以避免版本问题。

要了解更多关于本地安装`npm`包的信息，请访问此网页：

[`docs.npmjs.com/downloading-and-installing-packages-locally.`](https://docs.npmjs.com/downloading-and-installing-packages-locally)

使用本地安装的包的一个缺点是，特定命令，如`prettier`、`webpack`或`eslint`，不会在命令行上可用。相反，您需要将本地安装的节点模块作为 npm 脚本运行。

或者，这个本地安装的模块实际上可以直接在命令行上运行，使用`npx`命令，我们将在下一节中讨论。

# 什么是 Prettier？

Prettier 是一个代码格式化工具。它会在你保存文件时格式化你的代码。该项目网站可以在[`prettier.io/`](https://prettier.io/)找到。在接下来的示例中，我们将运行一个本地安装的 Prettier npm 模块。

# 使用 npx 在命令行上运行本地安装的 npm 模块

要开始使用 Prettier，我们将按照以下步骤启动一个新项目：

```js
mkdir Chapter6 && cd $_
```

接下来，在新的`Chapter6`文件夹中，我们将添加另一个文件夹`prettier-practice`，如下所示：

```js
mkdir prettier-practice && cd $_
```

接下来，我们将使用默认值初始化`npm`如下：

```js
npm init -y
```

接下来，让我们添加一个单独的文件`index.html`，如下所示：

```js
touch index.html && code .
```

上面的命令创建并打开了一个新的`index.html`文件在 VS Code 中。

我们将添加一个没有任何构建过程的小型 Vue 应用程序。让我们将以下代码添加到我们的`index.html`文件中：

```js
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>101 - using watchers in Vue</title>
    <style>
        body,
        input {
            font-family: Arial, sans-serif;
            font-size: 20px;
        }
    </style>
</head>

<body>
    <div id="example">
        <p>Enter owner name and the thing that is owned:
            <input v-model="ownerName" placeholder="enter owner">
            <input v-model="thing" placeholder="enter thing">
        </p>
        <span>{{ ownerName }}</span>
        <span> has a </span>
        <span>{{ thing }}</span>
    </div>
    <script src='https://cdnjs.cloudflare.com/ajax/libs/vue/2.6.10/vue.min.js'></script>
    <script>
        var example = new Vue({
            el: '#example',
            data: {
                ownerName: 'e.g Old McDonald',
                thing: 'e.g cow'
            },
            watch: {
                ownerName(currentValue, previousValue) {
console.log(`The value in the first input has changed from: ${previousValue} to: ${currentValue}`);
                }
            },
            computed: {
                // a computed getter
                ownerHasThing: function () {
                    // `this` points to the Vue instance's data option
                    return this.ownerName + " " + this.thing
                }
            }
        })
    </script>
</body>

</html>
```

你可以在 CodePen 上看到这个简单的 Vue 应用程序运行，网址是：[`codepen.io/AjdinImsirovic/pen/jprwKe.`](https://codepen.io/AjdinImsirovic/pen/jprwKe)

接下来，让我们添加 Prettier 来看它如何格式化我们的代码。

# 全局安装 Prettier 并在 index.html 上运行

要全局安装 Prettier，我们可以使用以下命令：

```js
npm install prettier -g --loglevel verbose
```

然后，要开始使用它，你可以运行`--help`标志来查看所有可用的选项，比如以下内容：

```js
prettier --help
```

现在，你可以运行`prettier index.html`命令来在控制台中查看格式化后的代码。或者，你可以运行命令`prettier index.html --write`，这样格式化后的输出实际上会保存在你指定的文件中。

接下来，我们将看看如何在本地安装 Prettier 并使用 npx 运行它。

# 使用 npx 运行 Prettier

npx 实用程序是一个**NPM 包运行器**。它在 2017 年的 5.2.0 版本中被添加到 NPM 中。它使得从`node_modules`目录中运行包变得更容易，因为我们不必像之前在`package.json`中那样依赖于`npm run <someScriptName>`。

在这个示例中，我们将看到如何将 Prettier 安装为开发依赖项，并如何使用 npx 运行它。首先，我们需要在我们的`prettier-practice`文件夹中使用`--save-dev`标志安装它，如下所示：

```js
npm install prettier --save-dev --verbose
```

完成后，我们的`package.json`将会被更新，然后我们可以开始使用 Prettier。

最简单的方法就是在 VS Code 的终端中简单地运行 Prettier 命令。

要查看可用的选项，我们可以运行以下命令：

```js
npx prettier
```

要实际运行带有一些选项的命令，我们可以运行这段代码：

```js
npx prettier --single-quote --trailing-coma es5 --write "index.html" --verbose
```

npx 还有许多其他用法，这是一个值得熟悉的好工具。

另一个有趣的练习是比较 Prettier 的格式化和 VS Code 自带的格式化工具。使用*Shift* + *Alt* + *F*组合键可以触发 VS Code 的格式化工具。我们可以在 VS Code 的控制台中按上箭头键，快速重新运行刚刚运行的`npx prettier`命令。

接下来，我们将创建另一个小项目来演示如何使用 ESLint。

# 使用 ESLint

ESLint 是一个 JavaScript 的代码检查工具。你可以在[`eslint.org/`](https://eslint.org/)找到它。代码检查工具会对你的代码进行分析，将其与特定的标准进行比较。你可以说它是一个检查代码质量的工具。代码检查工具非常适合团队环境，因为它们可以让我们的代码遵循特定的代码风格，并且确保在代码审查过程中少了一件需要讨论的事情。

代码风格的示例可以在这里找到：[`github.com/airbnb/javascript`](https://github.com/airbnb/javascript)。这是 Airbnb 的 JavaScript 代码风格指南。如果你在页面上滚动，你会发现有很多需要阅读的信息：如何访问原始类型，如何重新分配引用，如何在对象声明中分组简写属性等等。

重要的是要理解 ESLint 只是分析你的代码；它不运行它。但是，它可以找到错误，并且也可以修复它们。实际上，如果设置正确，它可以改变你的代码。

还有其他一些 JavaScript 代码检查工具，比如 JSLint 或 JSHint。ESLint 被宣传为更加可定制的工具。

# 全局安装 ESLint 并在命令行中运行失败

要全局安装 ESLint 节点模块，请运行以下命令：

```js
npm install eslint -g --loglevel verbose
```

现在，我们的`eslint`命令是全局可用的。

现在，我们将创建一个新的文件夹，`eslint-practice`。在其中，我们将创建一个新文件，`eslint-test.js`。

在 VS Code 中打开文件并添加以下代码：

```js
function (a) 
{
console.log(a, b);

}
```

保存文件。

接下来，在 VS Code 中使用*Ctrl* + *`*打开命令行，并运行以下命令：

```js
eslint eslint-test.js
```

你将收到的输出是：

```js
Oops! Something went wrong! :(

ESLint: 5.15.3.
ESLint couldn't find a configuration file. To set up a configuration file for this project, please run:

 eslint --init

ESLint looked for configuration files in C:\Users\Warrior Gaming\Desktop\Chapter6 and its ancestors. If it found none, it then looked in your home directory.
```

为什么会发生这种情况？没有 ESLint 配置文件，但为什么 ESLint 需要配置文件才能运行？

这是因为 ESLint 用来检查我们的代码的规则是在**配置**文件中指定的。如果没有配置文件，ESLint 就不知道在检查我们的代码时应用哪些规则。

这个问题的解决方案很有趣。没有`package.json`，我们就无法初始化 ESLint。因为我们必须安装一个新的 NPM 项目，我们可能会选择在本地安装 ESLint 节点模块。这不是必须的，但对于简化的工作流程来说，这是一个首选选项。

# 运行`eslint --init`命令

显然，我们不能在没有 ESLint 配置文件的情况下运行`eslint`命令。然而，我们也不能在没有`package.json`文件的情况下初始化 ESLint 配置文件。这意味着我们首先需要运行`npm init`，所以让我们现在这样做：

```js
npm init -y
```

现在，我们可以初始化`eslint`。当我们运行`eslint --init`命令时，我们将在命令行提示中被问到几个问题。我们选择的答案将取决于我们的 ESLint 配置。以下是我们需要为这个示例选择的选项：

+   选择`To check syntax, find problems, and enforce code style`

+   选择`None of these`

+   选择`None of these`（对于这个简单的演示，我们不会使用`Vue.js`，因为那将需要安装额外的依赖项，而在这一点上我们只是在测试 ESLint）

+   选择`Browser`

+   选择`Answer questions about your style`

+   选择`JSON`

+   选择`Spaces`

+   选择`Single`

+   选择`Windows`

+   输入*Y*（是的，我们确实需要分号）

+   选择*''*`JavaScript`*''*（作为你想要你的配置文件的格式是什么的答案）

一旦你回答了所有的问题，你将会看到以下输出：

```js
Local ESLint installation not found. 
The config that you've selected requires the following dependencies:
eslint@latest
Successfully created .eslintrc.js file in C:\Users\W\Desktop\Chapter6\eslint-practice
ESLint was installed locally. We recommend using this local copy instead of your globally-installed copy.
```

阅读这条消息，我们现在可以欣赏到 ESLint 的维护者正在引导我们朝着在计算机上限制全局安装的`npm`包的最佳实践，并选择在我们的项目中本地安装我们的 node 模块。前面的消息还提到我们需要`eslint@latest`依赖项才能运行我们的 ESLint 配置，所以让我们立即添加它，如下所示：

```js
npm install eslint@latest --save-dev --verbose
```

在本书中已经提到过，但现在是一个很好的复习时间：`--save-dev`标志意味着我们安装的包只用于开发，而不用于生产。`--save-dev`选项不会设置本地模块。你可以通过简单运行`npm install estlint@latest`来本地安装 ESLint。然而，`--save-dev`和`--verbose`标志都有它们的用处，但需要明确的是它们与本地安装`npm`包没有任何关系。

现在，我们准备在项目的 JavaScript 文件上运行 ESLint。在这样做之前，让我们检查一下`.eslintrc.js`的内容。

# 理解 .eslintrc.js 配置文件的结构

根据我们选择的规则，我们生成的 `.eslintrc.js` 文件如下所示：

```js
module.exports = {
    'env': {
        'browser': true,
        'es6': true
    },
    'extends': 'eslint:recommended',
    'globals': {
        'Atomics': 'readonly',
        'SharedArrayBuffer': 'readonly'
    },
    'parserOptions': {
        'ecmaVersion': 2018
    },
    'rules': {
        'indent': [
            'error',
            4
        ],
        'linebreak-style': [
            'error',
            'windows'
        ],
        'quotes': [
            'error',
            'single'
        ],
        'semi': [
            'error',
            'always'
        ]
    }
};
```

在配置文件中导出的对象内部，我们可以看到环境设置为 `browser`，`es6` 属性设置为 `true`，这意味着 ES6 语法会自动启用。

通过 `'extends': 'eslint:recommended'`，我们打开了推荐的核心规则，报告常见问题。这些规则列在以下页面上：[`eslint.org/docs/rules/`](https://eslint.org/docs/rules/)。

`'globals'` 条目列出了在使用指定配置运行 ESLint 时将访问的其他全局变量。

通过 `parserOptions` 属性，我们将 ECMA Script 版本设置为 `2018`，这当然是 ECMA Script 9。最后，`'rules'` 属性指定要应用的规则，以及**错误级别**。

# 在 JavaScript 文件上运行 ESLint

现在，我们可以在 `eslint-test.js` 文件上运行 `eslint` 命令。由于它是全局安装的，我们可以简单地运行 `eslint eslint-test.js`。

然而，由于这是被模块的维护者们不赞成的，让我们改为使用以下命令在本地运行它：

```js
npx eslint eslint-test.js
```

运行上述命令将产生以下输出：

```js
  1:10 error Parsing error: Unexpected token (

✖ 1 problem (1 error, 0 warnings)
```

我们还可以在 VS Code 中看到错误，作为一个单一字符，即 `(` 字符，在我们的 `eslint-test.js` 文件的第一行下划线标记为波浪红色。当然，这个错误意味着我们缺少函数的名称。所以让我们更新代码为：

```js
function  aFunction(a)  { console.log(a,  b)  }
```

注意，我们还删除了调用 `console.log` 方法后的分号。让我们再次运行 `eslint`。

这一次我们得到了五个新的错误，如下所示：

```js
  1:10 error 'aFunction' is defined but never used no-unused-vars
  3:1 error Expected indentation of 4 spaces but found 0 indent
  3:1 error Unexpected console statement no-console
  3:16 error 'b' is not defined no-undef
  3:18 error Missing semicolon semi

✖ 5 problems (5 errors, 0 warnings)
  2 errors and 0 warnings potentially fixable with the `--fix` option.
```

查看前述输出的每行末尾，我们可以看到我们的代码违反的具体**ESLint 规则**。在每行开头，我们可以看到行号，后跟一个冒号，再跟着违反 ESLint 规则的第一个字符的确切位置。因此，1:10 可以读作 *第 1 行，第 10 个位置的字符*。

让我们再次运行 `eslint`，并使用建议的 `--fix` 标志，如下所示：

```js
npx eslint eslint-test.js --fix
```

现在我们的代码被格式化，如下所示：

```js
function aFunction(a) 
{
    console.log(a, b);

}
```

然而，我们仍然会得到一些错误，如下所示：

```js
  1:10 error 'aFunction' is defined but never used no-unused-vars
  3:5 error Unexpected console statement no-console
  3:20 error 'b' is not defined no-undef

✖ 3 problems (3 errors, 0 warnings)
```

从这个小练习中我们可以得出结论，ESLint 将会执行以下操作：

+   指出我们代码中的规则违反。

+   允许我们传递 `--fix` 标志来纠正那些工具本身可以修复的错误。

接下来，我们将更新错误级别规则。

# 更新 ESLint 中的错误级别规则

默认情况下，所有规则的错误级别都设置为`'error'`。要手动设置不同的错误级别，比如`'warn'`，我们可以添加一个`errorLevel`常量，并将我们的规则更新为以下代码：

```js
const errorLevel1 = 'warn';
const errorLevel2 = 'error';
module.exports = {
    // ...
    // omitted this section to save space
    // ...
    'rules': {
        'indent': [
            'error',
            4
        ],
        'linebreak-style': [
            'error',
            'windows'
        ],
        'quotes': [
            'warn',
            'single'
        ],
        'semi': [
            'warn',
            'always'
        ]
    }
};
```

现在，通过这个更新，我们的`linebreak-style`和`indent`规则的错误级别将是`error`，而`quotes`和`semi`规则的错误级别将是`warn`。

接下来，我们将在 Vue CLI 3 中使用 ESLint。

# 在 Vue CLI 3 中配置 ESLint

现在我们已经熟悉了 Prettier 和 ESLint，我们将在 Vue CLI 3 中将它们安装到一个新项目中。让我们将 Git Bash 指向`Chapter6`文件夹的根目录，并运行以下命令：

```js
code .
```

一旦 VS Code 打开，我们将切换到命令行，并运行以下命令：

```js
vue create vc3-prettier-eslint
```

然后，我们将接受使用 Babel 和 ESLint 的默认安装，并等待应用程序安装完成。

接下来，类似于之前的操作，我们将运行`vue ui`，然后将`vc3-eslint`应用程序导入到我们的 Vue CLI 3 GUI 仪表板中。

正如我们所看到的，ESLint 作为 Vue CLI 3 应用的默认安装。但是，我们如何配置 ESLint，就像我们在本章的前一节中所做的那样呢？

# 在 Vue CLI 3 GUI 中设置 ESLint 配置

在 Vue CLI 3 UI 中加载我们的`vc3-eslint`应用程序后，让我们单击配置图标。

我们将单击 ESLint 配置，这将更新项目配置窗口右侧的面板，如下所示：

![](img/245a02c9-f576-489f-85a7-92884e8507f7.png)

图 6.1：单击配置图标显示应用程序的可能配置

在这个视图中，我们可以看到两个主要条目：保存时进行 Lint 和选择配置。

保存时进行 Lint 选项目前已经切换打开，但我们可以通过单击 Lint on save 选项右侧的绿色开关来切换关闭。

我们还可以在“选择配置”条目中选择不同的配置。默认设置可以更改为“强烈推荐”或“推荐”设置。我们在本章前面看到了如何在`eslintrc.js`文件中配置此设置。

您还可以选择点击页面右上角的“打开 eslintrc”按钮，打开我们项目的 ESLint 配置文件，这样您就可以直接编辑它。

最后，我们可以通过单击配置面板顶部的“规则”选项卡，查看项目中所有 ESLint 规则的列表，如下所示：

![](img/ef290a9f-6456-4c95-86dc-8a8eeca3c6e7.png)

图 6.2：在 Vue CLI 3 UI 中选择 ESLint 配置屏幕中的单个规则

屏幕将显示默认应用于我们 ESLint 配置的基本规则，而在“常规”选项卡上，我们可以通过单击“选择配置条目”中的下拉菜单来更改整个规则集；在规则选项卡中，我们可以混合和匹配单个规则，然后保存我们的更新。这样就可以更新规则并根据自己的喜好进行自定义更改。

# 在 Vue CLI 3 UI 项目中同时使用 ESLint 和 Prettier

如果您在 Vue CLI 3 UI 中从头开始一个项目，您也可以让 ESLint 和 Prettier 一起工作。方法如下：

1.  我们首先要将 Vue UI 指向根文件夹，如下所示。重要的是要验证您不在现有的 Vue 应用程序内：

![](img/91292f7b-75fc-4cc6-97a6-c72f82a515ab.png)

图 6.3：在 Vue CLI 3 UI 中创建新项目

1.  接下来，我们将点击“选择此文件夹”按钮继续，然后按照以下方式选择我们项目的名称：

![](img/201b603d-6613-4c65-a305-b32d971b8361.png)

图 6.4：添加项目名称

1.  接下来，我们将点击“选择此文件夹”按钮继续，然后选择我们项目的名称。点击“下一步”继续，您将被带到预设选项卡。

1.  一旦您在预设选项卡上，选择手动预设以手动选择功能，然后再次点击“下一步”。在功能选项卡上，Babel 和代码检查/格式化功能应该已经被预先选择，所以只需再次点击“下一步”进入配置屏幕，如下所示：

![](img/c963b523-75b9-4c53-add7-f5b575ef7274.png)

图 6.5：从配置屏幕中选择 ESLint + Prettier

1.  “选择一个代码检查/格式化配置”给了我们几个选项。在底部选择*ESLint + Prettier*。一旦我们点击它，就会出现一个选项来保存预设，所以让我们按照以下图片保存它：

![](img/82ace185-cda8-4325-93a5-1bd033cf705f.png)

图 6.6：在 Vue CLI 3 UI 中保存新预设

1.  一旦我们点击“创建新预设”按钮，我们就完成了对项目的自定义，然后我们可以等待所有依赖项安装完成。

1.  完成后，您将看到“欢迎使用新项目”的消息。现在，您可以点击左侧菜单上的“项目依赖”链接。除了预期的依赖项（`babel-eslint`，`eslint`，`eslint-plugin-vue`和`vue-template-compiler`）之外，您还会看到`@vue/eslint-config-prettier`开发依赖项。正如我们所看到的，这是一个官方的`vue npm`模块，因为它有`@vue`命名空间。

# 总结

在本章中，我们已经看到了`npm`和`npx`、全局和本地安装节点模块之间的区别。我们进一步讨论了 ESLint 和 Prettier。我们看到了如何在独立项目中安装它们，以及如何在 Vue CLI 3 GUI 中配置 ESLint。我们还看到了如何在 Vue CLI 3 中设置 ESLint 和 Prettier。

在下一章中，我们将探讨在 Vue CLI 3 中使用 CSS、SCSS 和 PostCSS。
