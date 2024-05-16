编写端到端测试

编写测试是 Web 开发的一部分。您的应用程序变得越来越复杂和庞大，您就越需要测试应用程序，否则它将在某个时候出现故障，并且您将花费大量时间修复错误和补丁。在本章中，您将使用 AVA 和 jsdom 为 Nuxt 应用编写端到端测试，并且还将亲身体验使用 Nightwatch 进行浏览器自动化测试。您将学习如何安装这些工具并设置测试环境-让我们开始吧。

本章我们将涵盖的主题如下：

+   端到端测试与单元测试

+   端到端测试工具

+   使用`jsdomn`和 AVA 为 Nuxt 应用编写测试

+   介绍 Nightwatch

+   使用 Nightwatch 为 Nuxt 应用编写测试

# 第十三章：端到端测试与单元测试

通常有两种类型的测试用于 Web 应用程序：单元测试和端到端测试。你可能听说过很多关于单元测试，并且在过去做过一些（或者很多）。单元测试用于测试应用程序的小部分和个体部分，而相反，端到端测试用于测试应用程序的整体功能。端到端测试涉及确保应用程序的集成组件按预期运行。换句话说，整个应用程序在类似于真实用户与应用程序交互的真实场景中进行测试。例如，用户登录页面的简化端到端测试可能涉及以下内容：

1.  加载登录页面。

1.  在登录表单中输入有效的详细信息。

1.  点击“提交”按钮。

1.  成功登录到页面并看到问候消息。

1.  退出系统。

那么单元测试呢？单元测试运行速度快，可以精确识别确切的问题和错误。单元测试的主要缺点是为应用程序的每个方面编写测试非常耗时。而且尽管您的应用程序已通过了单元测试，但整体应用程序仍可能出现故障。

端到端测试可以隐式测试许多方面，并确保您拥有一个正常工作的系统。与单元测试相比，端到端测试运行速度较慢，并且无法明确指出应用程序失败的根本原因。应用程序中看似不重要的部分发生微小变化可能会破坏整个测试套件。

将应用程序的单元测试和端到端测试结合在一起可能是理想和令人信服的，因为这样可以更彻底地测试您的应用程序，但这可能会耗费时间和金钱。在本书中，我们专注于**端到端测试**，因为默认情况下，Nuxt 与端到端测试工具无缝配置，您将在下一节中发现。

# 端到端测试工具

Nuxt 通过将 AVA 和 jsdom Node.js 模块一起使用，使端到端测试变得非常简单和有趣。但在 Nuxt 应用程序中实现并结合它们进行测试之前，让我们深入了解这些 Node.js 模块的每一个，看看它们是如何分开工作的，这样您就可以对这些工具有一个扎实的基本理解。让我们从下一节开始学习 jsdom。

## jsdom

简而言之，`jsdom`是基于 JavaScript 的 W3C 文档对象模型（DOM）在 Node.js 中的实现。但是，这意味着什么？我们需要它做什么？想象一下，您需要在 Node.js 应用程序中从原始 HTML 的服务器端操作 DOM，例如 Express 和 Koa 应用程序，但服务器端没有 DOM，因此您无法做太多事情。这就是 jsdom 拯救我们的时候。它将原始 HTML 转换为在 Node.js 中像客户端 DOM 一样工作的 DOM 片段。然后，您可以使用客户端 JavaScript 库，如 jQuery，在 Node.js 上操作 DOM。以下是用于服务器端应用程序的基本用法示例：

1.  在服务器端应用程序上导入 jsdom：

```js
import jsdom from 'jsdom'
const { JSDOM } = jsdom
```

1.  将任何原始 HTML 字符串传递给`JSDOM`构造函数，您将获得一个 DOM 对象：

```js
const dom = new JSDOM(<!DOCTYPE html><p>Hello World</p>)
console.log(dom.window.document.querySelector('p').textContent)
```

您从前面的代码片段中获得的 DOM 对象具有许多有用的属性，特别是`window`对象，然后您可以开始像在客户端上一样操作传递的 HTML 字符串。现在让我们在**Koa API**上应用这个工具，您在上一章中了解过，并且可以在我们的 GitHub 存储库中的`/chapter-12/nuxt-universal/cross-domain/jwt/axios-module/backend/`中找到，以打印`Hello world`消息。按照以下步骤进行：

1.  通过 npm 安装`jsdom`和 jQuery：

```js
$ npm i jsdom --save-dev
$ npm i jquery --save-dev
```

1.  导入`jsdom`并传递 HTML 字符串，就像我们在前面的基本用法示例中所做的那样：

```js
// src/modules/public/home/_routes/index.js
import Router from 'koa-router'
import jsdom from 'jsdom'

const { JSDOM } = jsdom
const router = new Router()

const html = '<!DOCTYPE html><p>Hello World</p>'
const dom = new JSDOM(html)
const window = dom.window
const text = window.document.querySelector('p').textContent
```

1.  将`text`输出到端点：

```js
router.get('/', async (ctx, next) => {
  ctx.type = 'json'
  ctx.body = {
    message: text
  }
})
```

当您在终端上运行`npm run dev`时，您应该在`localhost:4000/public`看到以 JSON 格式显示的“Hello world”消息（在下面的代码片段中显示）：

```js
{"status":200,"data":{"message":"Hello world"}}
```

1.  在我们的 API 中创建一个`movie`模块，并使用 Axios 从 IMDb 网站获取 HTML 页面，将 HTML 传递给 JSDOM 构造函数，导入 jQuery，然后将其应用于由 jsdom 创建的 DOM 窗口对象如下：

```js
// src/modules/public/movie/_routes/index.js
const url = 'https://www.imdb.com/movies-in-theaters/'
const { data } = await axios.get(url)

const dom = new JSDOM(data)
const $ = (require('jquery'))(dom.window)
```

请注意，Axios 必须通过 npm 在您的项目目录中安装，您可以使用`npm i axios`进行安装。

1.  将 jQuery 对象应用于所有具有`list_item`类的电影，并提取数据（每部电影的名称和放映时间）如下：

```js
var items = $('.list_item')
var list = []
$.each(items, function( key, item ) {
  var movieName = $('h4 a', item).text()
  var movieShowTime = $('h4 span', item).text()
  var movie = {
    name: movieName,
    showTime: movieShowTime
  }
  list.push(movie)
})
```

1.  将`list`输出到端点：

```js
ctx.type = 'json'
ctx.body = {
  list: list
}
```

您应该在`localhost:4000/public/movies`看到以下 JSON 格式的类似电影列表：

```js
{
  "status": 200,
  "data": {
    "list": [{
      "name": " Onward (2020)",
      "showTime": ""
    }, {
      "name": " Finding the Way Back (2020)",
      "showTime": ""
    },
    ...
    ...
    ]
  }
}
```

你可以在我们的 GitHub 存储库的`/chapter-13/jsdom/`中找到这些示例。有关此 npm 包的更多信息，请访问[`github.com/jsdom/jsdom`](https://github.com/jsdom/jsdom)。

您可以看到此工具在服务器端有多有用。它使我们能够像在客户端一样操纵原始 HTML。现在让我们在下一节中继续学习 AVA 的一些基本用法，然后在我们的 Nuxt 应用程序中与**jsdom**一起使用。

## AVA

简而言之，AVA（不是 Ava 或 ava，发音为`/ˈeɪvə/`）是一个用于 Node.js 的 JavaScript 测试运行器。有很多测试运行器：Mocha、Jasmine 和 tape 等。AVA 是现有列表的另一种选择。首先，AVA 很简单。它真的很容易设置。此外，默认情况下它并行运行测试，这意味着您的测试将运行得很快。它适用于前端和后端的 JavaScript 应用程序。总而言之，它绝对值得一试。让我们通过以下步骤开始创建一个简单的基本 Node.js 应用程序：

1.  通过 npm 安装 AVA 并将其保存到`package.json`文件的`devDependencies`选项中：

```js
$ npm i ava --save-dev
```

1.  安装 Babel 核心和其他 Babel 包，以便我们可以在应用程序的测试中编写 ES6 代码：

```js
$ npm i @babel/polyfill
$ npm i @babel/core --save-dev
$ npm i @babel/preset-env --save-dev
$ npm i @babel/register --save-dev
```

1.  在`package.json`文件中配置`test`脚本如下：

```js
// package.json
{
  "scripts": {
    "test": "ava --verbose",
    "test:watch": "ava --watch"
  },
  "ava": {
    "require": [
      "./setup.js",
      "@babel/polyfill"
    ],
    "files": [
      "test/**/*"
    ]
  }
}
```

1.  在根目录中创建一个`setup.js`文件，其中包含以下代码：

```js
// setup.js
require('@babel/register')({
  babelrc: false,
  presets: ['@babel/preset-env']
})
```

1.  在我们的应用程序中的两个单独文件中创建以下类和函数，以便稍后进行测试：

```js
// src/hello.js
export default class Greeter {
  static greet () {
    return 'hello world'
  }
}

// src/add.js
export default function (num1, num2) {
  return num1 + num2
}
```

1.  在`/test/`目录中为测试`/src/hello.js`创建一个`hello.js`测试：

```js
// test/hello.js
import test from 'ava'
import hello from '../src/hello'

test('should say hello world', t => {
  t.is('hello world', hello.greet())
})
```

1.  在`/test/`目录中的另一个文件中创建另一个测试，用于测试`/src/add.js`：

```js
// test/add.js
import test from 'ava'
import add from '../src/add'

test('amount should be 50', t => {
  t.is(add(10, 50), 60)
})
```

1.  在终端上运行所有测试：

```js
$ npm run test
```

您还可以使用`--watch`标志运行测试，以启用 AVA 的观察模式：

```js
$ npm run test:watch
```

如果测试通过，您应该会得到以下结果：

```js
✓ add › amount should be 50
✓ hello › should say hello world

2 tests passed
```

您可以在我们的 GitHub 存储库的`/chapter-13/ava/`中找到前面的示例。有关此 npm 包的更多信息，请访问[`github.com/avajs/ava`](https://github.com/avajs/ava)。

这很容易也很有趣，不是吗？看到我们的代码通过测试总是令人满意的。现在您已经对这个工具有了基本的了解，所以现在是时候在 Nuxt 应用程序中使用 jsdom 来实现它了。让我们在下一节中开始吧。

# 使用 jsdomn 和 AVA 为 Nuxt 应用程序编写测试

您已经独立学习了`jsdom`和`AVA`，并进行了一些简单的测试。现在，我们可以将这两个包合并到我们的 Nuxt 应用程序中。让我们在我们在上一章中创建的 Nuxt 应用程序中安装它们，路径为`/chapter-12/nuxt-universal/cross-domain/jwt/axios-module/frontend/`，使用以下步骤：

1.  通过 npm 安装这两个工具，并将它们保存到`package.json`文件中的`devDependencies`选项中：

```js
$ npm i ava --save-dev
$ npm i jsdom --save-dev
```

1.  安装`Babel`核心和其他 Babel 包，以便我们可以在应用程序中编写 ES6 代码：

```js
$ npm i @babel/polyfill
$ npm i @babel/core --save-dev
$ npm i @babel/preset-env --save-dev
$ npm i @babel/register --save-dev
```

1.  将 AVA 配置添加到`package.json`文件中，如下所示：

```js
// package.json
{
  "scripts": {
    "test": "ava --verbose",
    "test:watch": "ava --watch"
  },
  "ava": {
    "require": [
      "./setup.js",
      "@babel/polyfill"
    ],
    "files": [
      "test/**/*"
    ]
  }
}
```

1.  在根目录中创建一个`setup.js`文件，就像您在上一节中所做的那样，但使用以下代码：

```js
// setup.js
require('@babel/register')({
  babelrc: false,
  presets: ['@babel/preset-env']
})
```

1.  准备以下测试模板，以便在`/test/`目录中编写测试：

```js
// test/tests.js
import test from 'ava'
import { Nuxt, Builder } from 'nuxt'
import { resolve } from 'path'

let nuxt = null

test.before('Init Nuxt.js', async t => {
  const rootDir = resolve(__dirname, '..')
  let config = {}
  try { config = require(resolve(rootDir, 'nuxt.config.js')) } 
   catch (e) {}
  config.rootDir = rootDir
  config.dev = false
  config.mode = 'universal'
  nuxt = new Nuxt(config)
  await new Builder(nuxt).build()
  nuxt.listen(5000, 'localhost')
})

// write your tests here...

test.after('Closing server', t => {
  nuxt.close()
})
```

测试将在`localhost:5000`上运行（或者您喜欢的任何端口）。您应该在生产构建上进行测试，因此在`config.dev`键中关闭开发模式，并在`config.mode`键中使用`universal`，如果您的应用程序同时为服务器端和客户端开发。然后，在测试过程完成后，请确保关闭 Nuxt 服务器。

1.  编写第一个测试，测试我们的主页，以确保在此页面上呈现了正确的 HTML：

```js
// test/tests.js
test('Route / exits and renders correct HTML', async (t) => {
  let context = {}
  const { html } = await nuxt.renderRoute('/', context)
  t.true(html.includes('<p class="blue">My marvelous Nuxt.js 
   project</p>'))
})
```

1.  为`/about`路由编写第二个测试，以确保在此页面上呈现了正确的 HTML。

```js
// test/tests.js
test('Route /about exits and renders correct HTML', async (t) => {
  let context = {}
  const { html } = await nuxt.renderRoute('/about', context)
  t.true(html.includes('<h1>About page</h1>'))
  t.true(html.includes('<p class="blue">Something awesome!</p>'))
})
```

1.  为`/about`页面编写第三个测试，以确保通过服务器端的`jsdom`进行 DOM 操作，文本内容、类名和样式符合预期。

```js
// test/tests.js
test('Route /about exists and renders correct HTML and style', 
async (t) => {

  function hexify (number) {
    const hexChars = 
     ['0','1','2','3','4','5','6','7','8','9','a','b',
      'c','d','e','f']
    if (isNaN(number)) {
      return '00'
    }
    return hexChars[(number - number % 16) / 16] + 
     hexChars[number % 16]
  }

  const window = await nuxt.renderAndGetWindow(
   'http://localhost:5000/about')
  const element = window.document.querySelector('.blue')
  const rgb = window.getComputedStyle(element).color.match(/\d+/g)
  const hex = '' + hexify(rgb[0]) + hexify(rgb[1]) + hexify(rgb[2])

  t.not(element, null)
  t.is(element.textContent, 'Something awesome!')
  t.is(element.className, 'blue')
  t.is(hex, '0000ff')
})
```

如果测试通过`npm run test`，您应该会得到以下结果：

```js
✓ Route / exits and renders correct HTML (369ms)
✓ Route /about exits and renders correct HTML (369ms)
✓ Route /about exists and renders correct HTML and style (543ms)

3 tests passed
```

您可以看到，在我们的第三个测试中，我们创建了一个`hexify`函数，用于将由`Window.getComputedStyle`方法计算的十进制代码（R、G、B）转换为十六进制代码。例如，您将得到`rgb(255, 255, 255)`，对应于您在 CSS 样式中设置为`color: white`的颜色。因此，对于`0000ff`，您将得到`rgb(0, 0, 255)`，应用程序必须将其转换以通过测试。

您可以在我们的 GitHub 存储库的`/chapter-13/nuxt-universal/ava/`中找到这些测试。

干得好。您已经成功为 Nuxt 应用程序编写了简单的测试。我们希望您发现在 Nuxt 中编写测试很容易且有趣。您的测试的复杂性取决于您想要测试什么。因此，首先了解您想要测试的内容非常重要。然后，您可以开始编写一个合理、有意义和相关的测试。

然而，使用 jsdom 与 AVA 测试 Nuxt 应用程序存在一些限制，因为它不涉及浏览器。请记住，jsdom 用于在服务器端将原始 HTML 转换为 DOM，因此我们在前面的练习中使用了 async/await 语句来异步请求页面进行测试。如果您想要使用浏览器来测试您的 Nuxt 应用程序，Nightwatch 可能是一个很好的解决方案，因此我们将在下一节中介绍它。让我们继续。

# 介绍 Nightwatch

Nightwatch 是一个自动化测试框架，为基于 Web 的应用程序提供端到端的测试解决方案。它在幕后使用 W3C WebDriver API（以前称为 Selenium WebDriver）来打开**web 浏览器**，对 DOM 元素执行操作和断言。如果您想要使用浏览器来测试您的 Nuxt 应用程序，这是一个很好的工具。但在 Nuxt 应用程序中使用它之前，让我们按照以下步骤单独使用它来编写一些简单的测试，以便您对其工作原理有一个基本的了解：

1.  通过 npm 安装 Nightwatch，并将其保存到`package.json`文件的`devDependencies`选项中：

```js
$ npm i nightwatch --save-dev
```

1.  通过 npm 安装 GeckoDriver，并将其保存到`package.json`文件的`devDependencies`选项中：

```js
$ npm install geckodriver --save-dev
```

Nightwatch 依赖于 WebDriver，因此我们需要根据您的**目标浏览器**安装特定的 WebDriver 服务器-例如，如果您只想针对 Firefox 编写测试，则需要安装 GeckoDriver。

在本书中，我们专注于针对单个浏览器编写测试。但是，如果您想要并行地针对多个浏览器（如 Chrome、Edge、Safari 和 Firefox）进行测试，那么您需要安装**Selenium Standalone Server**（也称为 Selenium Grid），如下所示：

```js
$ npm i selenium-server --save-dev
```

请注意，在本书中我们将在 Firefox 和 Chrome 上进行测试，因此不会使用`selenium-server`包。

1.  在`package.json`文件的`test`脚本中添加`nightwatch`：

```js
// package.json
{
  "scripts": {
    "test": "nightwatch"
  }
}
```

1.  创建一个`nightwatch.json`文件来配置 Nightwatch 如下：

```js
// nightwatch.json
{
  "src_folders" : ["tests"],

  "webdriver" : {
    "start_process": true,
    "server_path": "node_modules/.bin/geckodriver",
    "port": 4444
  },

  "test_settings" : {
    "default" : {
      "desiredCapabilities": {
        "browserName": "firefox"
      }
    }
  },

  "launch_url": "https://github.com/lautiamkok"
}
```

在这个简单的练习中，我们想要测试 github.com 上特定贡献者**Lau Tiam Kok**的仓库搜索功能，所以我们在这个配置中的`launch_url`选项中设置了`https://github.com/lautiamkok`。

我们将在`/tests/`目录中编写测试，所以我们在`src_folders`选项中指定了目录位置。我们将仅针对 Firefox 进行测试，端口为`4444`，所以我们在`webdriver`和`test_settings`选项中设置了这些信息。

你可以在[`nightwatchjs.org/gettingstarted/configuration/`](https://nightwatchjs.org/gettingstarted/configuration/)找到其余测试设置的选项，比如`output_folder`。如果你想找出 Selenium 服务器的测试设置，请访问[`nightwatchjs.org/gettingstarted/configuration/selenium-server-settings`](https://nightwatchjs.org/gettingstarted/configuration/#selenium-server-settings)。

1.  在项目根目录中创建一个`nightwatch.conf.js`文件，用于将驱动程序路径**动态**设置为服务器路径：

```js
// nightwatch.conf.js
const geckodriver = require("geckodriver")
module.exports = (function (settings) {
  settings.test_workers = false
  settings.webdriver.server_path = geckodriver.path
  return settings
})(require("./nightwatch.json"))
```

1.  在`/tests/`目录中的一个`.js`文件（例如`demo.js`）中准备以下 Nightwatch 测试模板，如下所示：

```js
// tests/demo.js
module.exports = {
  'Demo test' : function (browser) {
    browser
      .url(browser.launchUrl)
      // write your tests here...
      .end()
  }
}
```

1.  在`/tests/`目录中创建一个`github.js`文件，其中包含以下代码：

```js
// tests/github.js
module.exports = {
  'Demo test GitHub' : function (browser) {
    browser
      .url(browser.launchUrl)
      .waitForElementVisible('body', 1000)
      .assert.title('lautiamkok (LAU TIAM KOK) · GitHub')
      .assert.visible('input[type=text][placeholder=Search]')
      .setValue('input[type=text][placeholder=Search]', 'nuxt')
      .waitForElementVisible('li[id=jump-to-suggestion-
        search-scoped]', 1000)
      .click('li[id=jump-to-suggestion-search-scoped]')
      .pause(1000)
      .assert.visible('ul[class=repo-list]')
      .assert.containsText('em:first-child', 'nuxt')
      .end()
  }
}
```

在这个测试中，我们想要断言仓库搜索功能是否按预期工作，所以我们需要确保某些元素和文本内容存在并可见，比如`<body>`和`<input>`元素，以及`nuxt`和`lautiamkok (LAU TIAM KOK) · GitHub`的文本。当你用`npm run test`运行它时，你应该得到以下结果（假设测试通过）：

```js
[Github] Test Suite
===================
Running: Demo test GitHub

✓ Element <body> was visible after 34 milliseconds.
✓ Testing if the page title equals "lautiamkok (LAU TIAM KOK) · 
   GitHub" - 4 ms.
✓ Testing if element <input[type=text][placeholder=Search]> is 
   visible - 18 ms.
✓ Element <li[id=jump-to-suggestion-search-scoped]> was visible 
   after 533 milliseconds.
✓ Testing if element <ul[class=repo-list]> is visible - 25 ms.
✓ Testing if element <em:first-child> contains text: "nuxt"
  - 28 ms.

OK. 6 assertions passed. (5.809s)
```

你可以在我们的 GitHub 仓库的`/chapter-13/nightwatch/`中找到上述测试。有关 Nightwatch 的更多信息，请访问[`nightwatchjs.org/`](https://nightwatchjs.org/)。

与 AVA 相比，Nightwatch 并不那么简洁，因为它需要一些可能会很冗长和复杂的配置，但如果你遵循最简单的`nightwatch.json`文件，它应该能让你很快地开始使用 Nightwatch。所以，让我们在下一节将你刚学到的内容应用到 Nuxt 应用中。

# 使用 Nightwatch 为 Nuxt 应用编写测试。

在这个练习中，我们希望针对**Chrome 浏览器**测试我们在上一章第十二章中创建的用户登录验证和 API 身份验证。我们希望确保用户可以使用他们的凭据登录并按预期获取他们的用户数据。我们将在存放 Nuxt 应用程序的`/frontend/`目录中编写测试，因此我们需要相应地修改`package.json`文件，并按以下步骤编写测试：

1.  通过 npm 安装 ChromeDriver 并将其保存到`package.json`文件中的`devDependencies`选项中：

```js
$ npm install chromedriver --save-dev
```

1.  在`nightwatch.json`文件中将启动 URL 更改为`localhost:3000`，并根据以下代码块中显示的其他设置修改 Nightwatch 配置文件，以便针对 Chrome 进行测试：

```js
// nightwatch.json
{
  "src_folders" : ["tests"],

  "webdriver" : {
    "start_process": true,
    "server_path": "node_modules/.bin/chromedriver",
    "port": 9515
  },

  "test_settings" : {
    "default" : {
      "desiredCapabilities": {
        "browserName": "chrome"
      }
    }
  },

  "launch_url": "http://localhost:3000"
}
```

1.  在项目根目录中创建一个`nightwatch.conf.js`文件，用于将驱动程序路径**动态**设置为服务器路径：

```js
// nightwatch.conf.js
const chromedriver = require("chromedriver")
module.exports = (function (settings) {
  settings.test_workers = false
  settings.webdriver.server_path = chromedriver.path
  return settings
})(require("./nightwatch.json"))
```

1.  在`/tests/`目录中创建一个`login.js`文件，其中包含以下代码：

```js
// tests/login.js
module.exports = {
  'Local login test' : function (browser) {
    browser
      .url(browser.launchUrl + '/login')
      .waitForElementVisible('body', 1000)
      .assert.title('nuxt-e2e-tests')
      .assert.containsText('h1', 'Please login to see the 
       secret content')
      .assert.visible('input[type=text][name=username]')
      .assert.visible('input[type=password][name=password]')
      .setValue('input[type=text][name=username]', 'demo')
      .setValue('input[type=password][name=password]', 
       '123123')
      .click('button[type=submit]')
      .pause(1000)
      .assert.containsText('h2', 'Hello Alexandre!')
      .end()
  }
}
```

这个测试的逻辑与上一节的测试相同。我们希望在登录前后确保登录页面上存在某些元素和文本。

1.  在运行测试之前，在终端上运行 Nuxt 和 API 应用程序，分别在`localhost:3000`和`localhost:4000`上运行，然后在`/frontend/`目录中打开另一个终端并运行`npm run test`。如果测试通过，您应该会得到以下结果：

```js
[Login] Test Suite
==================
Running: Local login test

✓ Element <body> was visible after 28 milliseconds.
✓ Testing if the page title equals "nuxt-e2e-tests" - 4 ms.
✓ Testing if element <h1> contains text: "Please login to see the 
   secret content" - 27 ms.
✓ Testing if element <input[type=text][name=username]> is 
   visible - 25 ms.
✓ Testing if element <input[type=password][name=password]> is 
   visible - 25 ms.
✓ Testing if element <h2> contains text: "Hello Alexandre!" 
  - 75 ms.

OK. 6 assertions passed. (1.613s)
```

请注意，在运行测试之前，您必须同时运行 Nuxt 应用程序和 API。您可以在我们的 GitHub 存储库的`/chapter-13/nuxt-universal/nightwatch/`中找到前面的测试。

做得好。您已经完成了关于为 Nuxt 应用程序编写测试的简短章节。本章中的步骤和练习为您提供了扩展测试的基本基础，使您的应用程序变得更大更复杂。让我们在最后一节总结一下您在本章学到的内容。

# 总结

在本章中，您已经学会了使用 jsdom 进行服务器端 DOM 操作，并分别使用 AVA 和 Nightwatch 编写简单的测试，然后尝试使用这些工具一起在我们的 Nuxt 应用程序上运行端到端测试。您还学会了端到端测试和单元测试之间的区别以及它们各自的优缺点。最后但同样重要的是，您从本章的练习中学到，Nuxt 默认配置完美，可以让您使用 jsdom 和 AVA 轻松编写端到端测试。

在接下来的章节中，我们将介绍如何使用诸如 ESLint、Prettier 和 StandardJS 等代码检查工具来保持我们的代码整洁，以及如何将它们集成和混合到 Vue 和 Nuxt 应用程序中。最后，您将学习 Nuxt 部署命令，并使用它们将您的应用程序部署到实时服务器上。所以，请继续关注。
