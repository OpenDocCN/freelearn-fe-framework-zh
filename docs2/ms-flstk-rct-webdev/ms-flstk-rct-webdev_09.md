# 与单元测试和行为测试的持续集成

我们做到了；恭喜！我们已经创建了一个全栈应用程序，它运行在特定的域名下（在这本书中是*reactjs.space*）。整个设置中缺少的部分是部署流程。部署应该是零停机时间。我们需要有一个冗余的服务器来运行我们的应用程序。

我们在应用程序中缺少一些步骤，使其能够专业地工作，包括压缩、单元测试和行为测试。

在本章中，我们将向你介绍一些需要掌握全栈开发所需的一些额外概念。剩余的缺失部分留给你作为作业。

# 何时编写单元测试和行为测试

通常，有一些关于何时编写单元测试和/或行为测试的建议。

在 ReactPoland，我们经常有客户运营初创公司。作为对他们的一般治理，我们建议以下内容：

+   如果你的初创公司正在寻找增长动力，你需要你的产品来实现它，那么不要担心测试

+   在你创建了你的**最小可行产品**（**MVP**）之后，在扩展你的应用程序时，你**必须**有这些测试

+   如果你是一家已经建立起来的公司，正在为你的客户开发应用程序，并且你对他们的需求非常了解，那么你肯定需要进行测试

前两点与初创公司和年轻公司相关。第三点主要与已经建立起来的公司相关。

根据你和你产品的位置，你需要自己决定是否值得编写测试。

# React 约定

有一个项目展示了全栈开发设置应该如何看起来，请访问[`React JS.co`](http://reactjs.co/)。

访问这个网站，了解如何将你的应用程序与单元测试和行为测试集成，并了解最新的最佳实践，以制作 React Redux 应用程序。

# Karma 测试

我们不会在本章中指导你设置测试，因为这超出了本书的范围。本章的目的是向你提供在线资源，帮助你理解更大的图景。

Karma 是最受欢迎的单元测试和行为测试工具之一。主要目标是提供一个高效测试环境，以便在开发任何应用程序时使用。

使用这个测试运行器，你将获得许多功能。有一个很好的视频解释了 Karma 的整体情况，请访问[`karma-runner.github.io`](https://karma-runner.github.io)。

一些主要功能如下：

+   **在真实设备上的测试**：你可以使用真实的浏览器和真实设备，如手机、平板电脑或 PhantomJS 来运行测试（PhantomJS 是一个可以通过 JavaScript API 脚本化的无头 WebKit，它对各种网络标准有快速和本地的支持：DOM 处理、CSS 选择器、JSON、Canvas 和 SVG）。有不同环境，但有一个在所有这些环境中运行的工具。

+   **远程控制**：你可以远程运行测试，例如，在 IDE 中每次保存时，这样你就不必手动操作。

+   **测试框架无关**：你可以使用 Jasmine、Mocha、QUnit 和其他框架编写测试。这完全取决于你。

+   **持续集成**：Karma 与 CI 工具（如 Jenkins、Travis 或 CircleCI）配合得很好。

# 如何编写单元和行为测试

让我们提供一个示例，说明如何正确设置项目以便能够编写测试。

访问非常受欢迎的 Redux 入门套件的 GitHub 仓库 [`github.com/davezuko/react-redux-starter-kit`](https://github.com/davezuko/react-redux-starter-kit)。

然后访问这个仓库的 `package.json` 文件。我们可以从中找到可能的命令/脚本：

```js
 "scripts": { 
    "clean": "rimraf dist", 
    "compile": "better-npm-run compile", 
    "lint": "eslint src tests server", 
    "lint:fix": "npm run lint -- --fix", 
    "start": "better-npm-run start", 
    "dev": "better-npm-run dev", 
    "dev:no-debug": "npm run dev -- --no_debug", 
    "test": "better-npm-run test", 
    "test:dev": "npm run test -- --watch", 
    "deploy": "better-npm-run deploy", 
    "deploy:dev": "better-npm-run deploy:dev", 
    "deploy:prod": "better-npm-run deploy:prod", 
    "codecov": "cat coverage/*/lcov.info | codecov" 
  },

```

如你所见，在 NPM 测试之后，它运行以下命令：

```js
   "test": { 
      "command": "babel-node ./node_modules/karma/bin/ 
       karma start build/karma.conf", 
      "env": { 
        "NODE_ENV": "test", 
        "DEBUG": "app:*" 
      } 
    }

```

你可以在 `build/karma.conf` 中找到 Karma 的配置文件，位于 [`github.com/davezuko/react-redux-starter-kit/blob/master/build/karma.conf.js`](https://github.com/davezuko/react-redux-starter-kit/blob/master/build/karma.conf.js)。

内容（2016 年 7 月）如下：

```js
import { argv } from 'yargs' 
import config from '../config' 
import webpackConfig from './webpack.config' 
import _debug from 'debug' 

const debug = _debug('app:karma') 
debug('Create configuration.') 

const karmaConfig = { 
  basePath: '../', // project root in relation to bin/karma.js 
  files: [ 
    { 
      pattern: &grave;./${config.dir_test}/test-bundler.js&grave;, 
      watched: false, 
      served: true, 
      included: true 
    } 
  ], 
  singleRun: !argv.watch, 
  frameworks: ['mocha'], 
  reporters: ['mocha'], 
  preprocessors: { 
    [&grave;${config.dir_test}/test-bundler.js&grave;]: ['webpack'] 
  }, 
  browsers: ['PhantomJS'], 
  webpack: { 
    devtool: 'cheap-module-source-map', 
    resolve: { 
      ...webpackConfig.resolve, 
      alias: { 
        ...webpackConfig.resolve.alias, 
        sinon: 'sinon/pkg/sinon.js' 
      } 
    }, 
    plugins: webpackConfig.plugins, 
    module: { 
      noParse: [ 
        //sinon.js/ 
      ], 
      loaders: webpackConfig.module.loaders.concat([ 
        { 
          test: /sinon(|/)pkg(|/)sinon.js/, 
          loader: 'imports?define=>false,require=>false' 
        } 
      ]) 
    }, 
    // Enzyme fix, see: 
    // https://github.com/airbnb/enzyme/issues/47 
    externals: { 
      ...webpackConfig.externals, 
      'react/addons': true, 
      'react/lib/ExecutionEnvironment': true, 
      'react/lib/ReactContext': 'window' 
    }, 
    sassLoader: webpackConfig.sassLoader 
  }, 
  webpackMiddleware: { 
    noInfo: true 
  }, 
  coverageReporter: { 
    reporters: config.coverage_reporters 
  } 
} 

if (config.globals.__COVERAGE__) { 
  karmaConfig.reporters.push('coverage') 
  karmaConfig.webpack.module.preLoaders = [{ 
    test: /.(js|jsx)$/, 
    include: new RegExp(config.dir_client), 
    loader: 'isparta', 
    exclude: /node_modules/ 
  }] 
} 

// cannot use &grave;export default&grave; because of Karma. 
module.exports = (cfg) => cfg.set(karmaConfig)

```

如你在 `karma.conf.js` 中所见，他们使用 Mocha（检查带有 `"frameworks: ['mocha']"` 的行）。配置文件中使用的其余选项在可用的文档中有描述，文档位于 [`karma-runner.github.io/1.0/config/configuration-file.html`](http://karma-runner.github.io/1.0/config/configuration-file.html)。如果你对学习 Karma 配置感兴趣，那么 `karma.conf.js` 应该是你的起点文件。

# Mocha 是什么，为什么你需要它？

在 Karma 配置文件中，我们发现它使用 Mocha 作为 JS 测试框架 ([`mochajs.org/`](https://mochajs.org/))。让我们分析代码库。

我们可以在 `config/index.js` 文件中找到 `dir_test : 'tests'`，因此基于这个变量，Karma 的 `config` 知道 Mocha 的测试位于 `tests/test-bundler.js` 文件中。

让我们看看在 https://github.com/davezuko/react-redux-starter-kit/tree/master/tests 的 `tests` 目录里有什么。正如你在 `test-bundler.js` 文件中看到的，这里有很多依赖项：

```js
// --------------------------------------- 
// Test Environment Setup 
// --------------------------------------- 
import 'babel-polyfill' 
import sinon from 'sinon' 
import chai from 'chai' 
import sinonChai from 'sinon-chai' 
import chaiAsPromised from 'chai-as-promised' 
import chaiEnzyme from 'chai-enzyme' 

chai.use(sinonChai) 
chai.use(chaiAsPromised) 
chai.use(chaiEnzyme()) 

global.chai = chai 
global.sinon = sinon 
global.expect = chai.expect 
global.should = chai.should()

```

让我们大致描述一下在那里使用的内容：

+   Babel-polyfill 模拟完整的 ES6 环境

+   Sinon 是一个独立的、与测试框架无关的 JavaScript 测试库，用于间谍、模拟和存根。

间谍在测试代码中调用其他外部服务时很有用。你可以检查它是否被调用，它有哪些参数，它是否返回了某些内容，甚至它被调用了多少次！

模拟概念与间谍概念非常相似。最大的区别是模拟会替换目标函数。它们也会用自定义行为（如抛出异常或返回值）替换被调用的代码。它们还能调用作为参数提供的回调函数。模拟代码返回指定结果。

模拟器是一种更智能的存根。模拟器用于断言数据，并且当使用存根仅用于返回数据且不应断言时，不应返回数据。模拟器可以在断言时填充您的测试，而存根则不能。

Chai 是 Node.js 和浏览器的 BDD/TDD 断言框架。在上一个示例中，它已经与 Mocha 测试框架配对。

# 逐步测试 CoreLayout

让我们分析 `CoreLayout.spec.js` 测试。此组件在发布应用中的角色类似于 CoreLayout，因此它是描述您如何开始编写应用程序测试的好方法。

核心布局测试文件位置（2016 年 7 月）可在[`github.com/davezuko/react-redux-starter-kit/blob/master/tests/layouts/CoreLayout.spec.js`](https://github.com/davezuko/react-redux-starter-kit/blob/master/tests/layouts/CoreLayout.spec.js)找到。

内容如下：

```js
import React from 'react' 
import TestUtils from 'react-addons-test-utils' 
import CoreLayout from 'layouts/CoreLayout/CoreLayout' 

function shallowRender (component) { 
  const renderer = TestUtils.createRenderer() 

  renderer.render(component) 
  return renderer.getRenderOutput() 
} 

function shallowRenderWithProps (props = {}) { 
  return shallowRender(<CoreLayout {...props} />) 
} 

describe('(Layout) Core', function () { 
  let _component 
  let _props 
  let _child 

  beforeEach(function () { 
    _child = <h1 className='child'>Child</h1> 
    _props = { 
      children: _child 
    } 

    _component = shallowRenderWithProps(_props) 
  }) 

  it('Should render as a <div>.', function () { 
    expect(_component.type).to.equal('div') 
  }) 
})

```

`react-addons-test-utils` 库使得使用 Mocha 测试 React 组件变得容易。我们之前示例中使用的方法是 **浅渲染**，它可在[`facebook.github.io/react/docs/test-utils.html#shallow-rendering`](https://facebook.github.io/react/docs/test-utils.html#shallow-rendering)找到。

此功能帮助我们测试 `render` 函数，并且是我们组件中渲染一层深度的结果。然后我们可以断言关于其 `render` 方法返回的内容的事实，如下所示：

```js
function shallowRender (component) { 
  const renderer = TestUtils.createRenderer() 

  renderer.render(component) 
  return renderer.getRenderOutput() 
} 

function shallowRenderWithProps (props = {}) { 
  return shallowRender(<CoreLayout {...props} />) 
}

```

首先，我们在 `shallowRender` 方法中提供一个组件（在这个例子中，它将是 CoreLayout）。然后，我们使用 `method.render`，然后使用 `renderer.getRenderOutput` 返回输出。

在我们的案例中，该函数在这里被调用（注意以下示例中缺少分号，因为我们描述的入门项目与我们的 linting 选项不同）：

```js
describe('(Layout) Core', function () { 
  let _component 
  let _props 
  let _child 

  beforeEach(function () { 
    _child = <h1 className='child'>Child</h1> 
    _props = { 
      children: _child 
    } 

    _component = shallowRenderWithProps(_props) 
  }) 

  it('Should render as a <div>.', function () { 
    expect(_component.type).to.equal('div') 
  }) 
})

```

您会发现 `_component` 变量包含 `renderer.getRenderOutput` 的结果。此值如下断言：

```js
expect(_component.type).to.equal('div')

```

在那个测试中，我们测试我们的代码是否返回`div`。但如果您访问文档，您会发现如下代码示例：

```js
<div> 
  <span className="heading">Title</span> 
  <Subcomponent foo="bar" /> 
</div>

```

您也可以找到如下断言示例：

```js
var renderer = ReactTestUtils.createRenderer(); 
result = renderer.getRenderOutput(); 
expect(result.type).toBe('div'); 
expect(result.props.children).toEqual([ 
  <span className="heading">Title</span>, 
  <Subcomponent foo="bar" /> 
]);

```

如前两个示例所示，您可以期望类型为 `div`，或者根据您的需求，可以期望更多关于 CoreLayout 返回的具体信息。

第一个测试断言组件的类型（如果它是 `div`），第二个示例测试断言 CoreLayout 是否返回正确的组件，如下所示：

```js
[ 
  <span className="heading">Title</span>, 
  <Subcomponent foo="bar" /> 
]

```

第一个是单元测试，因为这并不是测试用户是否看到了正确的东西。第二个是行为测试。

通常，Packt 有许多关于 **行为驱动开发**（**BDD**）和 **测试驱动开发**（**TDD**）的书籍。

# 与 Travis 的持续集成

在给定的示例中，你可以在[`github.com/davezuko/react-redux-starter-kit/blob/master/.travis.yml`](https://github.com/davezuko/react-redux-starter-kit/blob/master/.travis.yml)找到`.yml`文件。

这是一个 Travis 的配置文件。这是什么？这是一个用于构建和测试软件的托管 CI 服务。通常，它是开源项目可以免费使用的工具。如果你想为私有项目使用托管的 Travis CI，那么将适用他们的费用。

如前所述，通过添加`.travis.yml`文件来配置 Travis。YAML 格式是一个放置在你项目根目录的文本文件。该文件的内容描述了必须执行的所有步骤以测试、安装和构建项目。

Travis CI 的目标是使你 GitHub 账户的每个提交都运行测试，当测试通过时，你可以将应用部署到 Amazon AWS 上的一个预发布服务器。持续集成不在此书的范围之内，所以如果你有兴趣将此步骤添加到整个发布应用项目中，也有相关的书籍。

# 摘要

我们的发布应用正在运行。就像任何数字项目一样，我们还有很多可以改进的地方，以便得到更好的最终产品。例如，以下作业是给你的：

+   在前端添加一个 minifaction，这样在通过互联网加载时会更轻量。

+   如前所述，你需要开始使用 Karma 和 Mocha 进行单元和行为测试。本章详细描述了一个示例设置。

+   你需要选择一个 CI 工具，如 Travis，创建你的 YML 文件，并在 AWS 上准备环境。

这就是除了书中 350 多页内容所涵盖的所有内容外，你还可以额外做的事情，其中你构建了一个全栈 React + Redux + Falcor + Node + Express + Mongo 应用。我希望与你保持联系；在 Twitter/GitHub 上关注我，以便保持联系，或者如果你有任何额外的问题，请发送电子邮件给我。

祝你在下一个商业全栈应用中动手实践时好运，再次见到你。
