# 第二章：Vue CLI 3 中的 Webpack

在上一章中，我们看到了如何通过命令行和 UI 开始使用 Vue CLI。在本章中，我们将从 Vue CLI 3 的角度介绍 webpack 的基础知识。我们将首先概述 webpack 是什么。我们将研究模块捆绑、摇树、webpack 加载器和输出、webpack 插件、**热模块替换**（**HMR**）、代码覆盖和代码拆分的概念，然后我们将看看这些概念如何与 Vue CLI 3 配合，如下所示：

+   从脚本标签到模块捆绑器的 JavaScript（JS）语言的演变

+   脚本标签

+   **立即调用函数表达式**（**IIFEs**），它们解决了什么问题，以及它们没有解决的问题

+   **Node Package Manager** (**NPM**)如何帮助团队在他们的代码中共享第三方库

+   JS 任务运行器和 NPM 脚本的作用

+   CommonJS 规范是什么，以及它如何在 JavaScript 和 Node.js 中工作

+   模块捆绑器是什么，以及它们如何弥合 Node.js 和浏览器之间的差距

+   webpack 是什么，以及它是如何工作的

+   如何在项目中运行 webpack

+   使用生产和开发模式使用 webpack 捆绑资产

+   通过 NPM 添加 Vue 项目并使用 webpack

准确理解 webpack 的工作原理对于理解 Vue CLI 3 的魔力至关重要。如果您熟悉 webpack，您可能仍然会发现本章的某些部分有用。如果您觉得自己是 webpack 专家，您可能可以直接跳过本章。

在深入了解 webpack 是什么以及正确理解 webpack 解决的问题之前，我们需要回顾一下过去十年中 JS 语言发生的一些变化。

# JS 语言的演变

从 webpack 的角度来看，以下是 JS 生态系统中添加的方法、技术、最佳实践和模式的时间顺序列表，这些方法、技术、最佳实践和模式导致了当前的状态：

+   `script`标签作为向网页添加交互性的答案

+   立即调用函数表达式作为模块化库和避免代码冲突的答案

+   IIFEs 的问题

+   使用 NPM 在团队环境中共享第三方库

+   JS 任务运行器和 NPM 脚本

+   JS 中的模块

让我们更详细地看看这些解决方案中的每一个。

# 脚本标签

最初，将 JS 添加到您的网页意味着您需要直接在 HTML 中添加一些`script`标签。对于快速原型，这仍然是一种有效的做法，甚至到今天。很多时候，第三方库是通过`script`标签内的`src`属性添加的（通常放在我们的 HTML 中关闭`body`标签的正上方）。

不幸的是，您通常需要在 HTML 中添加多个`script`标签。而不管您是直接将 JS 代码添加到页面中，还是从项目中的另一个文件添加，或者从远程位置添加（例如从**内容传送网络**（**CDN**）使用`src`属性），最终，所有这些脚本都被添加到全局 JS 范围内。这意味着一件事，冲突。

为了避免冲突，采取了一个巧妙的方法，即使用 IIFE。

# 立即调用的函数表达式

IIFE 到底是什么？IIFE 简单地利用了 JS 中*括号不能包含语句*的事实。这个事实本身允许 JS 开发人员放入匿名函数，他们可以立即调用，而不会因为简单地将它们包装在括号中而从解析器中得到任何错误。

IIFE 本质上是 JS 语言的一个怪癖，但是非常有用；通过 IIFE，所有的代码都被限定在函数范围内，因此您的代码不会受到外部任何其他东西的影响。换句话说，使用 IIFE 是避免冲突的一种简单方法，即意外覆盖变量或函数。因此，有一段时间，许多流行的库开始将它们自己的代码包装成 IIFE。例如，如果您打开 jQuery 库的代码（[`code.jquery.com`](https://code.jquery.com)），或 Chart.js 库的代码（[`cdnjs.cloudflare.com/ajax/libs/Chart.js/2.7.3/Chart.bundle.js`](https://cdnjs.cloudflare.com/ajax/libs/Chart.js/2.7.3/Chart.bundle.js)），或许多其他流行的 JS 库的代码，您会发现它们使用了 IIFE 模式。

因此，通过 IIFE，我们可以向页面添加不同的脚本，而不必担心代码冲突可能发生。

# IIFE 的问题

不幸的是，仅仅使用 IIFE 并不能解决我们所有的问题。为了说明手头的问题，让我们引用 Erlang 的创始人 Joe Armstrong 的话：

“你想要香蕉，但你得到的是拿着香蕉的大猩猩，整个丛林。”

请记住，在这段引用中，阿姆斯特朗先生讨论的是面向对象语言的问题，但根本问题在 JS 代码模块化中也适用。

基本上，我们对 IIFEs 的问题在于我们无法从 JS 库中精选出我们想要使用的特定功能。使用 IIFE 模式，我们*必须*使用 IIFE 中包含的所有内容，即使我们只是使用特定库代码库的一小部分。当然，老实说，IIFEs 并不是这个问题的罪魁祸首。长期以来，JS 语言根本没有能力精选任何类型的代码功能，因为在 JS 中，将代码拆分成模块是不可能的。

JS 的另一个主要痛点是在团队之间重复使用第三方代码的问题。

# 使用 NPM 在团队环境中共享第三方库

IIFEs 解决了代码冲突的问题，但并没有解决代码重用的问题。如果我的团队中的开发人员有一个不同的、更新的库版本，其中有破坏性的更改，该怎么办？如果我决定在我的计算机上更新依赖关系，我的其他团队成员将如何处理？除了使用源代码版本控制，还有其他更快的协作选项吗？

**Node Package Manager**（**NPM**）是这些问题的答案。Node 只是一个可以在服务器上运行的 Google V8 JS 引擎。NPM 允许开发人员将新库安装到项目中，无论是用于应用程序的前端还是后端。因此，NPM 实际上是 JS 包管理器，类似于 Ruby（gems ([`rubygems.org/`](https://rubygems.org/)））、C#（NuGet ([`www.nuget.org/`](https://www.nuget.org/)））或 Linux 中的`apt-get`、`yum`。

例如，假设我们想通过 NPM 安装 Vue。如果我们的计算机上安装了 Node，那么我们也会有 NPM，因为 NPM 随 Node 一起捆绑安装。

接下来，我们需要创建一个新目录。让我们将此目录的名称更改为`vue-from-npm`，并将命令行控制台指向它。然后我们可以跟随这个命令：

```js
npm init -y
```

运行上述命令将创建一个`package.json`文件。`-y`标志接受控制台中提示的所有默认答案。

如果我们查看项目目录中新创建的`package.json`文件，我们会看到以下内容：

```js
{
  "name": "vue-from-npm",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

请注意，`npm init`命令只会将`package.json`文件添加到空目录中。就是这样！

然后，添加 Vue 就像运行这个命令一样简单：

```js
npm install vue --save --verbose
```

上述命令将执行一些操作，即：

+   它将添加`node_modules`目录。

+   它将整个 Vue 库放在`node_modules`目录中。

+   它将在我们项目的根目录中创建`package-lock.json`文件。

+   它将更新我们项目的根目录中的`package.json`文件。

更新后的`package.json`文件现在看起来是这样的：

```js
{
  "name": "vue-from-npm",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "vue": "².6.7"
  }
}
```

如果不明显的话，文件已经更新了一个新条目：`dependencies`。这个条目列出了项目中包含的所有依赖项。具体来说，我们已经将 Vue（版本 2.6.7 或以上）添加到了我们的项目中。

NPM 的一个很棒的地方是，我们可以像添加 Vue 一样轻松地向我们的项目添加任何其他库。例如，要使用 accounting.js 更新我们的项目，我们只需运行这个命令：

```js
npm install accounting-js --save --verbose
```

安装完成后，让我们再次检查`node_modules`目录：

```js
vue-npm/node_modules/
 ├── accounting-js/
 │ ├── dist/
 │ ├── lib/
 │ ├── CHANGELOG.md
 │ ├── package.json
 │ └── README.md
 ├── is-string/
 ├── object-assign/
 └── vue/
```

请注意，为了简洁起见，我们只显示了`accounting-js`文件夹内的第二级文件夹和文件。`is-string`，`object-assign`和`vue`文件夹都被折叠显示。

这向我们展示了有时其他 NPM 模块会捆绑实际安装的库。在`accounting-js`的情况下，我们还得到了`is-string`和`object-assign` NPM 模块。让我们也检查一下我们目录根目录中更新的`package.json`文件：

```js
{
  "name": "vue-from-npm",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "accounting-js": "¹.1.1",
    "vue": "².6.7"
  }
}
```

正如我们所看到的，根`package.json`文件已经更新为正确的`accounting-js`版本。让我们找到另一个`package.json`文件，这次是在`node_modules/accounting-js`文件夹中。如果你打开了那个文件，它包含了更多信息，仅仅超过 100 行代码。这些信息是特定于实际的 NPM 模块`accounting-js`。

好的，现在我们的项目已经准备好进行协作了。怎么做呢？让我们看看我们的一个同事，让我们称他为“约翰”，如何在他自己的电脑上添加我们刚刚创建的项目和所有项目依赖项。

为此，我们将创建一个新文件夹，让我们称之为`johns-computer`，然后我们只需将`vue-from-npm`文件夹中的根级`package.json`复制到我们的`johns-computer`文件夹中。

接下来，让我们简单地运行这个命令：

```js
npm install --verbose
```

运行上述命令将安装我们在`vue-from-npm`文件夹中的所有项目和依赖项。

# JavaScript 任务运行器和 NPM 脚本

大约在 NPM 变得流行的同时，另一种前端技术也在崛起：任务运行器。任务运行器是简单的工具；它们运行重复的任务。有时，任务运行器被称为构建工具，因为它们充当开发人员对代码库进行更新和最终生成的生产就绪代码之间的中介。这就是所谓的*构建步骤*，这是软件开发过程中的一部分，在这个过程中，你的代码在你编写完之后会发生一些事情。

例如，CSS3 中添加的新功能通常以*供应商前缀*（也称为*浏览器前缀*）的形式开始。换句话说，在新的 CSS 功能在所有浏览器中可用之前，它会在各个浏览器中以实验阶段实现，使用浏览器特定的前缀，如下所示：

```js
-ms-
-moz-
-o-
-webkit-
```

在这个按字母顺序排列的浏览器前缀列表中，我们可以看到微软浏览器、Mozilla、旧版本的 Opera，最后是所有基于 webkit 的浏览器（Chrome、Safari、新版 Opera 等）的浏览器前缀。

跟踪浏览器前缀的更新是有点困难的。开发人员的时间可能不是最好的用法，去监视 CSS 实现的变化，然后相应地更新他们的代码。例如，在过去的某个时间点，有必要在 CSS 的`transition`属性上使用以下浏览器前缀：

```js
-webkit-transition: background-color 1s;
-moz-transition: background-color 1s;
-o-transition: background-color 1s;
-ms-transition: background-color 1s;
```

显然，今天我们在 CSS 声明中简单地使用`transition`属性，而不需要任何浏览器前缀，因为`transition`属性在所有现代浏览器中得到了广泛支持。

不得不应对不断变化的 CSS 规范和各种浏览器中的实现带来的不断变化的情况，导致了任务运行器这种解决方案的出现。前端开发人员现在不再需要手动向他们的 CSS 代码中添加供应商前缀，而是可以简单地向他们的任务运行器添加一个插件，它会为他们做这些繁重的工作：在需要时添加供应商前缀。

当然，我们之前看到的只是任务运行器用于的一个例子。其他一些例子包括：压缩 CSS 和 JS 文件，从 ES6 转译为 ES5，从 SASS 编译 CSS，删除未使用的 CSS，在项目中保存文件时重新加载浏览器，等等。

今天，有许多不同的工具帮助我们有效地自动化开发过程中的一些任务。三个工具脱颖而出：Grunt、Gulp 和 NPM 脚本。

虽然 Grunt 和 Gulp 是独立的任务运行器，可以通过 NPM 安装，但基于 NPM 的脚本是一个有趣的替代方案，原因如下：

+   您已经在使用 NPM，为什么不更熟悉一下您已经在使用的工具呢？

+   使用 NPM 脚本而不是前面提到的任务运行器将进一步简化您的开发流程。

+   通过使用 NPM，您可以避免使用任务运行器插件来自动化 NPM 中可以自动化的任务的复杂性。

直到这一点，我们已经回顾了 JS 生态系统的历史和演变。我们已经看到了 IIFE 如何用来处理意外的作用域泄漏。我们还看到了 NPM 如何处理代码共享。我们进一步看到了如何使用任务运行器自动化一些重复的任务，以及如何使用 NPM 来通过将任务保留在 NPM 脚本中来消除不必要的抽象层。

然而，我们还没有看到解决 JS 中代码模块化问题的方法。所以，让我们接着看看。

# JavaScript 中的模块

在任何编程语言中，模块都是一个独立的功能块。您可以将它们视为电视节目的不同集数。它们可以独立查看。它们可以独立存在，尽管它们是整体的一部分。

就像电视节目中的一集有一个季节和一个编号，这样我们就知道*它在*更大情节中的位置一样，一个模块也包含了告诉我们它依赖的其他模块（*模块依赖*）以及它为整个应用程序添加了什么功能的信息；这就是所谓的*模块接口*，对其他模块公开的 API。

我们已经看到，在开始时，JS 根本没有模块。这在 Node.js 的引入后发生了变化。Node.js 实际上是 CommonJS 的一种实现，这是由 Mozilla 的 Kevin Dangoor 在 2009 年发起的一个项目。

CommonJS 项目的目的是定义一个标准库，提供供在浏览器之外使用的 JS API。这包括一个模块规范，这导致开发人员能够在 Node.js 中使用这样的代码：

```js
var bootstrap = require('bootstrap');
```

# 在 Node.js 中使用模块

让我们在 Node.js 中要求并使用一些模块：

1.  首先，我们将创建一个新目录。让我们称之为`module-practice`。让我们将 Git Bash 指向这个文件夹。

1.  一旦进入其中，让我们创建两个新文件。让我们将这些文件命名为`main.js`和`whatever.js`，如下所示：

```js
touch main.js whatever.js
```

1.  接下来，让我们按照以下步骤在 VS Code 中打开整个文件夹：

```js
code .
```

1.  现在，让我们向`whatever.js`添加一些代码如下：

```js
console.log('whatever');
```

这就是 JS 中代码的简单形式。

1.  现在让我们看看如何使它在我们的`main.js`文件中可用。我们只需要像下面这样要求`whatever.js`：

```js
let whatever = require('./whatever');
```

1.  现在它被要求了，我们可以使用它，所以让我们将`main.js`更新为这样：

```js
let whatever = require('./whatever');

whatever.returnWhatever();
```

1.  现在让我们用以下方式运行这段代码：

```js
node main.js
```

现在会发生的是，我们将在 Git Bash 中看到单词`whatever`被打印出来。

让我们进一步进行我们的实验。这是我们更新后的`whatever.js`：

```js
module.exports = {
    returnWhatever: function() {
        console.log('whatever');
    }
}
```

因此，我们需要更新`main.js`如下：

```js

whatever.returnWhatever();
```

正如我们已经看到的，`require`关键字导入了一个模块的代码，并使其在另一个文件中可用；在我们的例子中，就是`main.js`文件。

`exports`关键字让我们可以将代码提供给其他文件，但有一个注意事项。它还允许我们选择我们想要向其他文件提供的模块的哪些部分。正如我们所看到的，`module.exports`是一个对象。这个对象的内容在我们的`main.js`要求`whatever`模块时将被返回。这使我们能够仅暴露代码的某些部分，并且使模块接口的设置成为可能。换句话说，`module.exports`是使我们能够保持代码的部分私有的东西。考虑对`whatever.js`的这个更新：

```js
module.exports = {
    returnWhatever: function() {
        returnSomething();
    }
}

let returnSomething = () => {
    console.log('whatever');
}
```

我们不需要对`main.js`进行任何更改。如果我们从 Git Bash 运行它，仍然会在控制台输出单词`whatever`。但是我们已经使`whatever.js`的部分内容不直接可访问。

作为一个旁注，注意在前面的代码中，ES3 和 ES5 的函数语法一起使用。定义`returnSomething`函数的代码部分使用了更新的语法，这使我们能够在不使用`function`关键字的情况下编写函数定义。

# 模块捆绑器，一种在浏览器中使用模块的方法

不幸的是，你不能直接在浏览器中使用`require`关键字，正如我们刚才看到的那样。`require`关键字不是 JS 浏览器 API 的一部分。这里需要注意的是，Node.js 有能力读取和写入计算机文件系统。因此，如果你在项目中使用 Node.js 安装了任何 NPM 包，你就可以像之前解释的那样要求这样一个模块。

然而，浏览器中的 JS 无法访问你的操作系统文件系统，因此这给我们留下了一个难题：我们如何在浏览器中使用 JS 模块语法？

答案是：我们有一个工具可以做到这一点，它被称为**模块捆绑器**。

今天，在 2019 年，有许多不同的模块打包工具可用，比如 webpack（[`webpack.github.io/`](http://webpack.github.io/)）、FuseBox（[`fuse-box.org/`](https://fuse-box.org/)）、Parcel（[`parceljs.org/`](https://parceljs.org/)）、rollup.js（[`rollupjs.org/guide/en`](https://rollupjs.org/guide/en)）或 Browserify（[`browserify.org/`](http://browserify.org/)）。

什么是模块打包工具？以下是 Browserify 主页上的一句话，简洁地表达了它：

“Browserify 让你可以在浏览器中使用 require('modules')来捆绑所有你的依赖项。”

除了打包项目中通过模块所需的所有依赖项，模块打包工具还解决了循环依赖等问题；也就是说，它们使用算法来解析项目中所有依赖项应该在项目中捆绑的顺序。

我们几乎完成了对 JS 生态系统的概述。接下来，我们将看一种特定的模块打包工具，那就是 webpack。

一旦我们知道 webpack 究竟是什么，以及它在幕后是如何工作的，我们就能完全理解它在 Vue CLI 中的位置。

# 什么是 webpack？

Webpack 是 Web 的模块打包工具。有些人也把它称为 Web 应用程序的资产编译器。

根据 webpack 的 GitHub 页面：

“它将许多模块打包成少量的捆绑资产等等。模块可以是 CommonJs、AMD、ES6 模块、CSS、图片、JSON、CoffeeScript、LESS 等等，还有你自定义的东西。”

在本章的前面，标题为*在 Node.js 中使用模块*的部分中，我们只是浅尝辄止地介绍了模块在 Node 应用程序中的导出和引入。我们没有提到的是，我们可以使用各种不同的模块语法。正如前面提到的，Node.js 使用 CommonJS 模块语法。除了 CommonJS，还有**异步模块定义**（**AMD**）。除了 AMD，你还可以使用 ESM 模块。使用 ESM 模块时，语法与我们之前看到的有些不同。

让我们按照以下步骤使用 ESM 语法重写`whatever`模块，并在`main.js`中使用它：

1.  为了简化事情，让我们创建一个新的文件夹如下：

```js
mkdir es6-module-practice;
```

1.  让我们使用`cd`命令（*更改目录*命令）指向我们的 Git Bash 如下：

```js
cd es6-module-practice
```

1.  让我们按照以下方式添加我们的两个文件：

```js
touch whatever2.mjs main2.mjs
```

1.  现在，让我们按照以下方式打开我们的文件夹与 VS Code：

```js
code .
```

1.  接下来，让我们添加`main2.mjs`的代码如下：

```js
import returnWhatever from './whatever2';

returnWhatever();
```

1.  最后，让我们按照以下方式编写`whatever2.mjs`的代码：

```js
let returnWhatever = () => {
    returnSomething();
}

let returnSomething = () => {
    console.log('whatever');
}

export default returnWhatever;
```

1.  正如我们所看到的，我们需要将文件保存为 ESM 模块，使用`mjs`文件扩展名。Node.js 实验性地支持 ESM 模块，因此您需要在 Git Bash 中运行以下命令：

```js
node --experimental-modules main2.mjs
```

1.  运行上述命令后，您将在控制台中看到以下输出：

```js
(node:12528) ExperimentalWarning: The ESM module loader is experimental.
whatever
```

正如我们所看到的，除了在控制台中收到预期的输出之外，我们还收到了`ExperimentalWarning`消息。希望这个演示两种不同模块语法的示例能帮助我们理解 webpack 将为我们做什么。除其他事项外，它将*平衡竞争环境*，这样我们就可以在项目中使用各种标准和非标准的模块工作方式。

基本上，webpack 所做的是，它接受具有依赖项的模块（包括我们项目的资产，如`.png`、`.jpeg`和`.scss`文件），并输出静态资产（`.js`、`.css`和`.image`文件）。

# webpack 的工作原理

我们已经看到了如何使用 CommonJS 和 ESM 模块语法。再次强调，CommonJS 是 Node.js 模块使用的语法。这意味着 Node.js 模块中的所有依赖项都是使用`require`命令描述的。与此相反，webpack 模块的依赖项可以用各种语法描述。例如，如果您的模块依赖于一个 SCSS 部分，您将使用`@import`语句。如果您正在导入 AMD 模块的依赖项，您将使用其自己的`require`和`define`语法。

这意味着，基本上*webpack 模块接受导入各种依赖项的不同语法*。甚至`src`属性（用于`img` HTML 元素）也被视为 webpack 模块的依赖项。

# 构建一个新项目并在其上运行 webpack

现在，让我们通过以下步骤构建一个项目并将 webpack 捆绑到我们的工作流程中：

1.  让我们添加一个新目录。让我们运行一个不存在的命令，如下所示：

```js
new-project-with-webpack
```

控制台将返回以下内容：

```js
bash: new-project-with-webpack: command not found
```

1.  太棒了！现在，让我们使用*重复上一条命令*的快捷方式，双感叹号，如下所示：

```js
mkdir !! && cd !!
```

运行上述命令将创建我们的`new-project-with-webpack`文件夹，并将`cd`进入这个新目录。双和符号命令(`&&`)只是一种运行多个命令的方式，而不是一个接一个地输入它们。双感叹号命令(`!!`)表示*重复上一行*，所以上述命令实际上意味着以下内容：

```js
mkdir new-project-with-webpack && cd new-project-with-webpack
```

1.  接下来，让我们添加我们的`package.json`，并接受所有默认值（使用`-y`标志）如下：

```js
npm init -y
```

1.  让我们按以下步骤检查我们在 VS Code 中的文件夹内容：

```js
code .
```

1.  一旦 VS Code 在我们的屏幕上运行，我们可以双击`package.json`文件并验证其内容如下：

```js
{
  "name": "new-project-with-webpack",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}

```

1.  现在，让我们按照以下步骤将 webpack 添加到我们的项目中：

```js
npm install --save-dev webpack webpack-cli --verbose
```

1.  完成后，让我们回到 VS Code 并再次审查我们的`package.json`如下：

```js
{
  "name": "new-project-with-webpack",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "webpack": "⁴.29.5",
    "webpack-cli": "³.2.3"
  }
}
```

正如我们所看到的，一个新的键已经被添加：`devDependencies`。在其中，我们有`webpack`和`webpack-cli`开发依赖。这些`devDependencies`是你在构建项目时才会使用的依赖项，而 webpack 就是这样一个依赖的完美例子：你在生产环境中不需要 webpack。这就是为什么我们在通过 NPM 安装 webpack 时使用了`--save-dev`标志。

查看我们项目的文件结构，我们现在可以看到以下内容：

```js
node_modules/
package.json
package-lock.json
```

如果你打开`node_modules`文件夹，你会看到里面有 300 多个文件夹。这个庞大的依赖列表以一个`.bin`文件夹开始。与我们之前的一个例子`vue-from-npm`相比，其中`node_modules`文件夹内只有四个子文件夹，尽管我们安装了`vue`和`accounting-js`两个 NPM 包。还要注意的是，在`vue-from-npm`文件夹内，没有`.bin`文件夹。无论你在运行`npm install`时使用`--save`还是`--save-dev`标志，情况都是如此。虽然这对于更有经验的开发人员可能是显而易见的，但对于那些在 Node.js 和 NPM 生态系统方面经验不足的开发人员来说，更好地理解这一点可能是很重要的。

那么，这个`.bin`文件夹是什么？它只是存储了你使用`npm install`安装的 Node 模块的编译后的本地二进制文件（即可执行文件）。并非所有的 NPM 模块都有这些编译后的本地二进制文件，这就是为什么你不会总是在`node_modules`文件夹内看到`.bin`文件夹的原因。在这个`.bin`文件夹内，有许多不同的 Node 模块。这些对于 webpack 正常工作都是必要的。

回到我们的项目，现在让我们向其中添加两个文件：`index.js`和`whatever.js`，如下所示：

```js
touch index.js whatever.js
```

目前，我们不会向这些文件中添加任何代码。现在，我们将专注于在我们的项目中运行 webpack。

# 在一个项目上运行 webpack

回到我们的`new-project-with-webpack`文件夹，再次检查`package.json`的内容，重点关注`scripts`键如下：

```js
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
},
```

在 Git Bash 中使用以下命令运行`test`脚本：

```js
npm run test
```

这将抛出一个带有`exit code 1`的错误。

让我们对它做一些更改，如下所示：

```js
"scripts": {
    "test": "echo \"You haven't specified any tests\""
},
```

让我们再次用`npm run test`来运行测试。这次控制台的输出不会那么可怕，因为我们删除了`exit 1`命令，并且改变了运行`test`命令时会被回显的内容。

让我们尝试完全不同的东西，如下所示：

```js
"scripts": {
    "test": "node index.js"
},
```

现在，我们不会得到错误，因为我们的`index.js`是空的。让我们添加一些内容，如下所示：

```js
// add up 2 numbers
console.log(2+2)
```

保存对`index.js`的更改，再次运行`npm run test`，这次在 Git Bash 中的输出将会打印出数字`4`。

这告诉我们什么？它告诉我们我们完全控制我们的脚本将要做什么！所以，最初我们有一个名为 test 的脚本。这个脚本会回显一条消息，并抛出一个带有`exit code 1`的错误。

就像我们可以给我们的脚本任意的键名，比如`test`，我们也可以给它们任意的值。当然，`console.log(2+2)`是一个愚蠢的值给一个脚本键。我们可以给我们的脚本键更好的值，例如：

```js
  "scripts": {
    "webpack": "webpack"
  },
```

现在，当我们用 webpack 的值运行一个 NPM 脚本时，这个脚本将运行 webpack 可执行文件。让我们试一下，如下所示：

```js
npm run webpack
```

这会返回一个错误，但在所有被记录下来的信息中，以下两行是最重要的：

```js
Insufficient number of arguments or no entry found.
Alternatively, run 'webpack(-cli) --help' for usage info.
```

我们得到这个错误的原因是因为 webpack 在寻找入口点来启动。默认情况下，这个入口点被设置为`./src/index.js`。所以，让我们添加这个`src`文件夹，并将我们的`index.js`移动到其中，如下所示：

```js
mkdir src && mv index.js $_
```

现在，让我们再次从命令行运行 webpack，如下所示：

```js
npm run webpack
```

这次我们会得到一个更好的输出。然而，默认情况下 Git Bash 没有语法高亮。这可以很快解决。因为我们已经在使用 VS Code，只需键入*Ctrl* + *~*的快捷键。如果你对这个符号不熟悉，它叫做*tilde*，位于*Esc*键下方，*Tab*键上方。按下这个快捷键将在 VS Code 中打开一个终端窗口，如果你再次执行`npm run webpack`命令，你会得到格式良好且带有颜色高亮的输出，就像这样：

![](img/3d6cb640-cf6b-4d45-8781-a79d20bdb165.png)

图 2.1：在 VS Code 中将信息记录到控制台的 webpack 作为一个侧面说明，你的屏幕颜色可能会有所不同，这取决于你在 VS Code 中使用的颜色方案。要访问颜色主题，请使用以下键盘快捷键：*Ctrl + K* *Ctrl + T*。

查看控制台输出的消息，我们可以看到它可以分为两部分：实际信息（哈希、版本、时间、构建时间、入口点等）和警告。警告显示我们没有设置`mode`选项。

如果未设置，`mode`选项默认为生产模式。但是，我们也可以将其设置为`development`，这对于更快的构建进行了优化。这意味着我们可以在`package.json`的`scripts`部分中添加另一个脚本，然后可以用于项目的开发构建。这是更新后的`scripts`部分：

```js
"scripts": {
    "webpack": "webpack",
    "dev": "webpack --mode=development"
},
```

现在，我们可以使用以下命令在 webpack 中运行开发模式：

```js
npm run dev
```

这是控制台中的完整输出：

```js
Hash: 86c0da41f48381d9bd70
Version: webpack 4.29.5
Time: 108ms
Built at: 2019-02-27 12:23:30
 Asset Size Chunks Chunk Names
main.js 3.81 KiB main [emitted] main
Entrypoint main = main.js
[./src/index.js] 38 bytes {main} [built]
```

我们可以看到，webpack 在开发模式下花了`108ms`来打包我的项目。当我在生产模式下运行它（在我的设置中默认的`npm run webpack`命令），它花了`447ms`。

运行这个命令时实际上发生了什么？webpack 在后台做了什么？它构建了所有模块依赖的依赖图。回到本章前面的比喻，就好像我们给了它一堆电视剧的剧集，录在一堆蓝光光盘上，它把它们全部拿来并正确地排列起来。Webpack 找出了每个模块的正确位置，然后将它们捆绑起来并提供给`dist`文件夹。如果再次查看项目的文件结构，你会发现有一个新的添加：`dist`文件夹。如果我们检查`dist`文件夹的内容，我们会看到：

```js
./dist/
   |- main.js
```

如果我们检查`main.js`文件，我们会看到 webpack 添加了很多东西。即使在像我们这样的小项目上，输出也会有大约 100 行长。

我们的`main.js`文件的前几行如下：

```js
/******/ (function(modules) { // webpackBootstrap
/******/ // The module cache
/******/ var installedModules = {};
...
```

让我们再次运行`npm run webpack`命令，看看它如何影响`main.js`中的输出。

如果我们检查`main.js`，我们会看到现在只有一行代码，以以下内容开头：

```js
!function(e){var t={};function n(r){if(t[r])return t[r].exports;var ...
```

这意味着当以生产模式运行时，webpack 会对我们的代码进行混淆和缩小。

显然，这也会影响文件大小。在开发模式下，打包的`main.js`文件大小为 3.81 KB，而在生产模式下，它只有 944 字节。

最后，为了避免看到警告消息，我们可以将`package.json`中的脚本条目更新为：

```js
"scripts": {
    "webpack": "webpack --mode=production",
    "dev": "webpack --mode=development"
},
```

在这一点上，我们可以开始使用 webpack 与 Vue。但是，我们不会使用 Vue CLI。相反，我们将看到如何手动设置所有内容。这不是做事情的最佳方式，但它将帮助我们更好地理解为什么在 Vue 生态系统中会这样做。

# 通过 NPM 添加一个 Vue 项目并使用 webpack

在这一部分，我们将使用 NPM 构建一个新项目，然后将 webpack 添加到其中，并最终添加一个 Vue 单文件组件。

首先，让我们按照以下步骤新建一个目录。我们将我们的项目命名为`npm-vue-webpack`：

1.  打开 Git Bash 并按照以下方式添加一个新文件夹：

```js
mkdir npm-vue-webpack && cd $_
```

1.  按照以下方式初始化`npm`：

```js
npm init -y
```

1.  接下来，按照以下步骤将 Vue 和 webpack 安装到我们的新项目中：

```js
npm install vue webpack webpack-cli --save-dev --verbose
```

一旦 NPM 安装完成，我们可以像在本章前面那样验证`package.json`的文件夹和内容。

1.  接下来，按照以下方式添加我们的项目将使用的源文件夹和输出文件夹：

```js
mkdir dist src
```

1.  按照以下步骤打开我们的新项目在 VS Code 中：

```js
code .
```

现在，我们可以直接从 VS Code 编辑器中添加两个新文件。我们将第一个文件命名为`source.js`，第二个文件命名为`output.js`。确保在此阶段将这两个空文件添加并保存到您的项目中：`source.js`在`src`文件夹中，`output.js`在`dist`文件夹中。

# 将我们的 Vue 组件添加为 JavaScript 模块

现在让我们添加我们的 Vue 组件：

1.  接下来，让我们按照以下方式将这段代码添加到`source.js`中：

```js
import CustomArticle from './CustomArticle.js';

new Vue({
    el: '#app',
    render: h => h(CustomArticle),
})
```

1.  在第一行，我们正在导入一个名为`CustomArticle.js`的文件。

1.  让我们在`src`文件夹内新建一个文件。我们将这个文件命名为`CustomArticle.js`。

1.  并将以下代码添加到其中：

```js
let CustomArticle = Vue.component('custom-article', {
    template: `
      <article>
        Our own custom article component!
      </article>`
  })

export default CustomArticle;
```

我们可以看到，我们正在使用 ESM 语法来导出和导入 JS 模块。

# 使用 webpack 编译 JavaScript 模块

现在我们几乎可以准备好使用 webpack 将我们的`source.js`编译为`output.js`了。但在这之前，我们仍然需要按照以下方式更新我们的`package.json`中的`scripts`部分：

```js
"scripts": {
    "webpack": "webpack"
},
```

现在，我们可以在 Git Bash 中运行以下命令：

```js
npm run webpack ./src/source.js ./dist/output.js
```

正如预期的那样，我们在控制台中看到了输出，以及关于设置模式选项的警告。我们现在知道这意味着什么，所以在这个时候处理它并不重要。

如果我们检查`output.js`的内容，我们会发现它是空的，并且默认情况下，webpack 会将我们的输出代码压缩和混淆到默认的`main.js`文件中，具体如下：

```js
!function(e){var t={};function n(r){if(t[r])return t[r].exports;var o=t[r]={i ...
```

那么，我们如何让 webpack 输出到一个不同的文件，而不是默认的`main.js`？我们使用 webpack 配置文件！

# 通过 webpack 配置文件添加选项

使用 webpack 配置文件，我们可以添加各种选项来打包我们的应用程序。具体如下：

1.  在项目的根目录添加一个新文件。我们将这个文件命名为`webpack.config.js`。代码如下：

```js
module.exports = {
  output: {
    filename: 'output.js',
  }
};
```

1.  现在，再次运行我们的命令如下：

```js
npm run webpack ./src/source.js ./dist/output.js
```

这次它输出到了正确的文件。

1.  就像我们可以指定输出文件一样，我们也可以指定输入文件如下：

```js
module.exports = {
 entry: './src/source.js',
 output: {
 filename: 'output.js',
 }
};
```

我们仍然需要在屏幕上的某个位置渲染我们的 Vue 组件。我们需要一个 HTML 文件来实现这一点。

# 添加一个 HTML 文件，以便渲染我们的 Vue 组件

让我们在`dist`文件夹中添加一个新的 HTML 文件，我们将其命名为`index.html`，具体如下：

```js
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Compiled HTML file</title>
</head>
<body>
    <div id="entryPoint"></div>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/vue/2.5.13/vue.min.js"></script>
    <script src="output.js"></script>
</body>
</html>
```

就像我们在本章开头讨论的那样，我们以*老派*的方式直接向 HTML 中添加脚本，只需在 HTML 文件底部堆叠`script`标签。我们使用的第一个`script`标签是从 CDN 获取 Vue，第二个`script`标签从`dist`文件夹中获取我们的`output.js`文件。

如果你使用 VS Code，现在可以右键单击新的`dist/index.html`文件，然后单击“在默认浏览器中打开”命令。

在打开的网页上会看到以下句子：

我们自己的自定义文章组件！

现在，我们需要让 webpack 能够输出 HTML 文件。为此，我们需要使用`html-webpack-plugin`。

# 赋予 webpack 输出 HTML 文件的能力

在本节中，我们将看到如何使用 webpack 插件输出 HTML 文件，具体步骤如下：

1.  通过 NPM 安装`html-webpack-plugin`如下：

```js
npm install html-webpack-plugin --save-dev --verbose
```

1.  我们的`package.json`的`devDependencies`已相应更新如下：

```js
"devDependencies": {
    "html-webpack-plugin": "³.2.0",
    "vue": "².6.7",
    "webpack": "⁴.29.5",
    "webpack-cli": "³.2.3"
}
```

1.  现在，按照以下步骤更新我们的`webpack.config.js`：

```js
let HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    entry: './src/source.js',
    output: {
        filename: 'output.js',
    },
    plugins: [new HtmlWebpackPlugin()]
};
```

1.  在继续之前，删除`dist`文件夹中的`index.html`文件。不过不用担心删除它，因为 webpack 很快就会重新创建它。

1.  接下来，再次运行 webpack 脚本如下：

```js
npm run webpack
```

Webpack 刚刚为我们创建了一个新的`index.html`文件！文件内容如下：

```js
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Webpack App</title>
  </head>
  <body>
  <script type="text/javascript" src="output.js"></script></body>
</html>
```

这都很好，但显然，我们的文件不一样了。我们丢失了 Vue 组件的入口点。此外，我们需要更新我们的 Vue 代码，使其作为单文件组件工作。

# 将.vue 文件作为 JavaScript 模块添加

首先，让我们更新`source.js`文件，如下所示：

```js
import Vue from 'vue';
import CustomArticle from './CustomArticle.vue';

new Vue({
    el: '#entryPoint',
    render: h => h(CustomArticle),
})
```

现在我们还可以将`CustomArticle.js`重命名为`CustomArticle.vue`，并向其中添加以下代码：

```js
<template>
  <div id="entryPoint">
      <article>
        Our own custom article component!
      </article>
  </div>
</template>
```

不幸的是，webpack 不能直接处理`.vue`文件。为了解决目前的问题，我们需要使用**webpack 加载器**。webpack 加载器帮助 webpack 理解它正在处理的文件。有许多加载器，但现在我们需要使用 Vue。

# 添加 webpack 加载器以处理.vue 文件

要处理`.vue`文件，请按以下步骤进行：

1.  通过 NPM 安装名为`vue-loader`的 webpack 加载器，如下所示：

```js
npm install vue-loader --save-dev --verbose
```

1.  现在我们已经保存了它，我们需要使用它，我们将通过更新 webpack 配置来做到这一点：

```js
let HtmlWebpackPlugin = require('html-webpack-plugin');
let VueLoaderPlugin = require('vue-loader/lib/plugin');

module.exports = {
    entry: './src/source.js',
    output: {
        filename: 'output.js',
    },
    plugins: [
        new HtmlWebpackPlugin(),
        new VueLoaderPlugin(), 
    ]
};
```

1.  现在尝试运行 webpack，如下所示。剧透警告：它会失败：

```js
npm run webpack
```

我们得到的错误消息如下：

```js
Error: [VueLoaderPlugin Error] No matching rule for .vue files found.
```

1.  要修复此错误，我们需要为我们的 Vue 加载器添加一个规则，通过更新我们的`webpack.config.js`文件如下：

```js
let HtmlWebpackPlugin = require('html-webpack-plugin');
let VueLoaderPlugin = require('vue-loader/lib/plugin');

module.exports = {
    entry: './src/source.js',
    output: {
        filename: 'output.js',
    },
    module: {
        rules: [
            { test: /\.vue$/, use: 'vue-loader' }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin(),
        new VueLoaderPlugin(), 
    ]
};
```

`rules`选项中数组内的`test`键接收一个正则表达式作为值。这个正则表达式检查是否存在一个带有`vue`文件扩展名的文件。如果匹配，也就是说，如果找到了一个`vue`文件，它将在其上使用`vue-loader`模块。

1.  让我们再次运行我们的 webpack 脚本，如下所示：

```js
npm run webpack
```

这将抛出另一个错误，如下所示：

```js
ERROR in ./src/CustomArticle.vue
Module Error (from ./node_modules/vue-loader/lib/index.js):
[vue-loader] vue-template-compiler must be installed as a peer dependency, or a compatible compiler implementation must be passed via options
```

1.  控制台中记录了更多错误，但我们需要通过添加另一个 NPM 包来解决这个。

```js
npm install vue-template-compiler --save-dev --verbose
```

`package.json`中的`devDependencies`条目刚刚又更新了，如下所示：

```js
  "devDependencies": {
    "html-webpack-plugin": "³.2.0",
    "vue": "².6.7",
    "vue-loader": "¹⁵.6.4",
    "vue-template-compiler": "².6.7",
    "webpack": "⁴.29.5",
    "webpack-cli": "³.2.3"
  }
```

所以，现在我们可以再次运行 webpack，如下所示：

```js
npm run webpack
```

在 webpack 运行后，如果此时打开`output.js`，你会看到它里面有完整的 Vue 库，以及我们的`CustomArticle`在最后。所有这些都没有任何错误编译。

# 修复我们的 index.html 文件的问题

我们仍然有`dist`文件夹中的`index.html`文件的问题。这很容易解决！我们只需在`src`文件夹中添加我们自己的`index.html`文件，内容如下：

```js
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Compiled HTML file</title>
</head>
<body>
    <div id="entryPoint"></div>
</body>
</html>
```

请注意，我们现在已经删除了自己的`script`标签，因为 webpack 将添加它们。另外，请确保删除`dist`文件夹中的`index.html`文件。现在，再次运行`npm run webpack`命令，你将在`dist/index.html`中得到以下输出：

```js
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Webpack App</title>
  </head>
  <body>
  <script type="text/javascript" src="output.js"></script></body>
</html>
```

为什么这不起作用？

它不起作用，因为我们需要更新要输出的 JS 文件以及 HTML 文件。目前，我们只更新 JS 文件，但我们仍然需要为我们的`index.html`文件做同样的操作。幸运的是，我们已经有`html-webpack-plugin`来帮忙。

# 通过 html-webpack-plugin 使用 webpack 传递 HTML 文件

我们将首先更新`webpack.config.js`文件中的`html-webpack-plugin`如下：

```js
plugins: [
    new HtmlWebpackPlugin({
        template: './src/index.html',
    }),
        new VueLoaderPlugin(), 
]
```

在插件部分所做的是，我们向`HtmlWebpackPlugin()`调用传递了一个`options`对象。在这个`options`对象内部，我们指定了我们的模板：`./src/index.html`。

在我们再次运行 webpack 脚本之前，我们需要确保添加`meta`标签，并将`charset`设置为`utf-8`。否则，当我们在浏览器中打开`dist/index.html`时，我们将在控制台中收到错误。

现在让我们再次运行`npm run webpack`。这次，一切都正常！我们在屏幕上得到了我们看起来很简单的句子：

我们自己的自定义文章组件！

恭喜！虽然它看起来很简单，但您已成功将一个 Vue 应用程序添加到了运行在 webpack 上的 NPM 项目中。

接下来，我们将学习 HMR 以及它如何在 Vue 开发中帮助我们。

# 了解 Vue 中的热模块替换

HMR 在过去几年已经成为了一个热门词汇。这有什么了不起的？在本节中，我们将讨论 HMR 的工作原理。

为了做到这一点，我们将构建另一个默认的简单应用程序，就像我们在第一章中所做的那样，*介绍 Vue CLI 3*：

```js
vue create -d second-default-app
```

过一会儿，一旦完成，我们将按以下方式进入我们应用程序的目录：

```js
cd second-default-app
```

让我们按以下方式在 VS Code 中打开项目文件夹：

```js
code .
```

现在，我们可以看到整个`second-default-project`的内容。

现在，我们可以按以下方式提供应用程序：

```js
npm run serve
```

当然，我们的应用程序现在正在浏览器中提供。

要查看您的应用程序，请在浏览器中访问`localhost:8080`。

让我们实时查看 HMR 更新。

# 观察 HMR 更新

在浏览器窗口处于活动状态时，让我们按下*F12*键打开开发者工具。还要确保我们的元素面板是开发工具内的活动选项卡，这样我们就可以看到**文档对象模型**（**DOM**）结构，就像在浏览器中放大的截图中一样：

![](img/555e8cdc-a628-43eb-870d-45c51239a104.png)

图 2.2：打开开发工具中元素面板的 second-default-app 的欢迎屏幕

现在，让我们看看 HMR 的实际效果。理想情况下，要看到这一点，您需要使用两个监视器。因此，您可以将 VS Code 窗口移动到左侧监视器，将为应用程序提供的浏览器移动到右侧监视器。或者，您可以使用单个监视器并将两个应用程序并排查看（每个应用程序占据屏幕宽度的一半）。这个练习的重点是能够同时看到您的 VS Code 窗口、Vue 应用程序浏览器窗口和浏览器开发工具中的 Elements 面板。

接下来，在 VS Code 中打开项目的`src`文件夹中的`App.vue`文件。查看第 4 行，目前的内容如下：

```js
<HelloWorld msg="Welcome to Your Vue.js App"/>
```

我们很快就会将该行更改为其他内容。在更改该行之前，请注意这些更改如何在为您提供应用程序的浏览器中反映。浏览器会刷新吗？

现在，您专注于跟踪浏览器中的更改，让我们按照以下方式更新`App.vue`中的第 4 行：

```js
<HelloWorld msg="HMR is cool"/>
```

在保存`App.vue`中的更改时，请注意浏览器。最好的方法是让 VS Code 处于焦点状态，但要观察浏览器窗口，特别是 Elements 面板。将 VS Code 置于焦点状态，您可以使用快捷键*Ctrl* + *S*保存更改。

如果您仔细观察，并且正在使用 Chrome 浏览器，您会注意到 Elements 面板内出现了一道紫色的闪光。这是 Chrome 浏览器通知我们 Vue 应用程序的 DOM 发生了变化。如果您仔细观察，您会注意到`head`元素上有一道闪光，以及`h1`元素和其子文本节点`HMR is cool`上也有一道闪光。

您可能会注意到浏览器没有刷新。无论是 webpack、我们的应用代码还是我们自己都没有强制浏览器刷新。这里的结论是什么？Webpack 实际上并没有使用 HMR 强制刷新页面！相反，它只是注入了 HMR。

虽然`h1`元素的更改是显而易见的（因为它是我们更改`App.vue`文件中文本的直接可见结果），但在`head`元素中发生的更新既更加隐晦又更有帮助。为了看到发生了什么，让我们点击 Elements 面板中`head`标签左侧的小黑三角形展开 head 标签，如下面的截图所示：

![](img/495be8e0-9fbb-415a-ac5e-e73a101235f3.png)

图 2.3：在开发工具中的 Elements 面板中展开 head 标签

接下来，我们需要滚动到最后的`</head>`标签。在它的上方，会有一个类似于这样的`script`标签：

```js
<script charset="utf-8" src="/app.c7e7b2f6599f49948328.hot-update.js"></script>
```

当我们对`App.vue`进行另一个更改时，让我们密切关注元素面板中 DOM 树的这一部分。让我们将第 4 行的`msg`属性更新为这样：

```js
<HelloWorld msg="HMR is cool indeed"/>
```

如果你观察了`script`标签，你会注意到它的变化如下：

```js
<script charset="utf-8" src="/app.417e697f270d544a21b3.hot-update.js"></script>
```

你有没有注意到 Vue 在闭合的`</head>`标签上面注入了另一个脚本？那个注入的脚本就是 HMR 在那里发挥作用。

让我们检查附加的脚本文件的第一行，如下所示：

```js
webpackHotUpdate("app",{
```

整个过程是如何工作的呢？Webpack 简单地运行我们的更改，捆绑了我们应用程序的更新，并且，由于它已经在运行，使用 Vue 加载器注入了新的代码。

所以，正如我们刚才看到的，HMR 只是 webpack 的一个功能，帮助我们更顺畅地进行操作，而不需要担心刷新我们的应用程序。

# 摘要

在本章中，我们讨论了 JS 语言及其生态系统的演变，以及这种演变如何导致模块捆绑器的出现。我们还看了 webpack，这是 Vue CLI 3 的首选模块捆绑器。

我们看了如何插入一个非常基本的 Vue 应用程序，运行在单文件 Vue 模板上。除了这个小项目，我们还看了一些重要的 webpack 概念。我们通过观察 Vue 项目上的 HMR 来结束了本章。

现在我们知道了 webpack 的基本工作原理，在接下来的章节中，我们将讨论一些其他相关技术，并且在 webpack 和 Vue CLI 3 的知识基础上进行深入。我们将密切关注的下一个主题是 Babel。
