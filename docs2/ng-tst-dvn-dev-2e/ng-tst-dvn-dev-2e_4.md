# 第四章。使用 Protractor 进行端到端测试

单元测试只是测试的一个方面，它只测试每一块代码的责任。然而，当涉及到测试任何组件、模块或完整应用程序的流程和功能时，那么**端到端**（**e2e**）测试是唯一的解决方案。

在本章中，我们将逐步查看应用程序的所有层级的端到端测试流程。我们将介绍来自 Angular 团队的端到端测试工具 Protractor。我们已经知道了它的原因，为什么它被创建，以及它解决了哪些问题。

在本章中，我们将：

+   安装和配置 Protractor 的过程

+   在我们的现有 Angular 项目中实现 Protractor 端到端测试

+   e2e 测试运行

+   返回测试结果

# Protractor 概述

Protractor 是一个使用 Node.js 运行的端到端测试工具，作为 npm 包提供。在具体讨论 Protractor 之前，我们需要了解什么是端到端测试。

我们已经在第二章*JavaScript 测试的细节*中简要了解了端到端测试。但是，让我们快速回顾一下：

端到端测试是对应用程序的所有相互连接的移动部分和层进行测试。这与单元测试不同，单元测试的重点在于单个组件，例如类、服务和指令。在端到端测试中，重点是整个应用程序或模块如何工作，例如确认按钮的点击会触发 x、y 和 z 动作。

Protractor 允许通过交互应用程序的 DOM 元素来对任何模块或任何规模的 Web 应用程序进行端到端测试。它提供了选择特定 DOM 元素、与该元素共享数据、模拟按钮点击以及以用户的方式与应用程序交互的能力。然后允许根据用户期望设置期望。

## Protractor 的核心

在快速概述中，我们得到了关于 Protractor 的基本概念——它需要如何选择 DOM 元素并与它们交互，就像真实用户在运行任何应用程序的端到端测试时那样。为了进行这些活动，Protractor 提供了一些全局函数；其中一些来自其核心 API，一些来自 WebDriver。我们将在第五章*Protractor，一步领先*中详细讨论它们。

然而，现在让我们快速概述一下：

+   **浏览器**：Protractor 提供了全局函数 `browser`，这是一个来自 WebDriver 的全局对象，主要用于在端到端测试过程中与运行应用程序的应用程序浏览器进行交互。它提供了一些有用的方法来交互，如下所示：

    ```js
            browser.get('http://localhost:3000'); // to navigate the    
            browser to a specific url address  
            browser.getTitle(); // this will return the page title that 
            defined in the projects landing page  

    ```

    还有更多，我们将在下一章中讨论。

+   **元素**：这是 Protractor 提供的一个全局函数；它基本上用于根据定位器查找单个元素，但它也支持通过链式另一个方法 `.all`（即 `element.all`）进行多个元素选择，该方法也接受 `定位器` 并返回 `ElementFinderArray`。让我们看看一个 `element` 的示例：

    ```js
            element(Locator); // return the ElementFinder 
            element.all(Locator); // return the ElementFinderArray 
            element.all(Locator).get(position);  // will return the
            defined  position 
            element from the ElementFinderArray  
            element.all(Locator).count(); // will return the 
            total number in the select element's array   

    ```

    还有更多，我们将在下一章讨论。

+   **动作**：正如我们所见，`element` 方法将返回一个选定的 DOM `element` 对象，但我们需要与 DOM 进行交互，而执行这项工作的动作方法由一些内置方法提供。DOM 不会通过任何动作方法调用与浏览器单元进行联系。让我们看看动作的一些示例：

    ```js
            element(Locator).getText(); // return the ElementFinder 
            based on locator  
            element.(Locator).click(); // Will trigger the click 
            handler for that specific element  
            element.(Locator).clear(); // Clear the field's value 
            (suppose the element is input field)
    ```

    还有更多，我们将在下一章讨论。

+   **定位器**：这实际上告诉 Protractor 如何在 DOM 元素中找到某个元素。Protractor 将 `定位器` 导出为一个全局工厂函数，该函数将与全局 `by` 对象一起使用。让我们看看 `定位器` 的几个示例：

    ```js
            element(by.css(cssSelector)); // select element by css 
            selector  
            element(by.id(id)); //  select element by element ID 
            element.(by.model); // select element by ng-model 

    ```

    还有更多，我们将在下一章讨论。

## 一个快速示例

现在我们可以通过以下用户规范快速进行一个示例。

假设我在搜索框中输入 `abc`，以下应该发生：

+   应该点击搜索按钮

+   应至少收到一个结果

前面的规范描述了一个基本搜索功能。前面的规范中没有描述控制器、指令或服务；它只描述了预期的应用程序行为。如果用户要测试该规范，他们可能执行以下步骤：

1.  将浏览器指向网站。

1.  选择输入字段。

1.  在输入字段中输入 `abc`。

1.  点击 **搜索** 按钮。

1.  确认搜索输出至少显示一个结果。

Protractor 的结构和语法与 Jasmine 和我们已在第三章中编写的测试相似，*Karma 方式*。我们可以将 Protractor 视为 Jasmine 的一个包装器，增加了支持端到端测试的功能。要使用 Protractor 编写端到端测试，我们可以遵循我们刚才看到的相同步骤，但使用代码。

这里是带有代码的步骤：

1.  将浏览器指向网站：

    ```js
            browser.get('/'); 

    ```

1.  选择输入字段：

    ```js
            var inputField = element.all(by.css('input')); 

    ```

1.  在输入字段中输入 `abc`：

    ```js
            inputField.setText('abc'); 

    ```

1.  点击 **搜索** 按钮：

    ```js
            var searchButton = element.all(by.css('#searchButton'); 
            searchButton.click(); 

    ```

1.  在页面上找到搜索结果详情：

    ```js
            var searchResults = element.all(by.css('#searchResult'); 

    ```

1.  最后，需要断言屏幕上至少有一个或多个搜索结果可用：

    ```js
            expect(searchResults).count() >= 1); 

    ```

作为完整的测试，代码将如下所示：

```js
    describe('Given I input 'abc' into the search box',function(){ 
        //1 - Point browser to website 
        browser.get('/'); 
        //2 - Select input field 
        var inputField = element.all(by.css('input')); 
        //3 - Type abc into input field 
        inputField.setText('abc'); 
        //4 - Push search button 
        var searchButton = element.all(by.css('#searchButton'); 
        searchButton.click(); 
        it('should display search results',function(){ 
        // 5 - Find the search result details 
        var searchResults = element.all(by.css('#searchResult'); 
        //6 - Assert 
        expect(searchResults).count() >= 1); 
        }); 
    }); 

```

就这样！当 Protractor 运行时，它将打开浏览器，访问网站，遵循指示，并最终检查期望。端到端测试的技巧是有一个清晰的用户规范愿景，然后将该规范转换为代码。

之前的示例是本章将描述的内容的高级概述。现在我们已经介绍了 Protractor，本章的其余部分将展示 Protractor 在幕后是如何工作的，如何安装它，最后，通过一个使用 TDD 的完整示例来引导我们。

# Protractor 的起源

Protractor 不是 Angular 团队构建的第一个端到端测试工具。第一个工具被称为**Scenario Runner**。为了理解为什么构建了 Protractor，我们首先需要看看它的前身--Scenario Runner。

Scenario Runner 处于维护模式，并已到达其生命的尽头。它已被 Protractor 取代。在本节中，我们将探讨 Scenario Runner 是什么以及该工具存在的空白。

## Protractor 的诞生

Julie Ralph 是 Protractor 的主要贡献者。据 Julie Ralph 所说，Protractor 的动机基于以下在 Google 内部另一个项目中对 Angular Scenario Runner 的经验([`javascriptjabber.com/106-jsj-protractor-with-julie-ralph/`](http://javascriptjabber.com/106-jsj-protractor-with-julie-ralph/))：

> *"我们尝试使用 Scenario Runner。我们发现它真的无法完成我们需要测试的事情。我们需要测试登录等功能。您的登录页面不是 Angular 页面，Scenario Runner 无法处理这种情况。它也无法处理弹出窗口、多个窗口、浏览历史记录导航等问题。"*

基于她对 Scenario Runner 的使用经验，Julie Ralph 决定创建 Protractor 来填补空白。

Protractor 利用了 Selenium 项目的成熟度，并封装了其方法，以便它可以轻松用于 Angular 项目。记住，Protractor 是通过用户的角度进行测试的。它被设计来测试应用程序的所有层：Web UI、后端服务、持久化层等。

## 没有 Protractor 的生活

单元测试不是唯一需要编写和维护的测试。单元测试关注应用程序的小型单个组件。通过测试小型组件，代码和逻辑的信心增强。单元测试不关注当相互连接时整个系统是如何工作的。

使用 Protractor 进行端到端测试允许开发者专注于一个功能或模块的完整行为。回到搜索示例，测试应该只在整个用户规范通过时才通过；在搜索框中输入数据，点击**搜索**按钮，并查看结果。Protractor 不是唯一的端到端测试框架，但它是 Angular 应用程序的最佳选择。以下是您应该选择 Protractor 的几个原因：

+   它在 Angular 教程和示例中都有文档记录

+   它可以使用多个 JavaScript 测试框架编写，包括 Jasmine 和 Mocha

+   它为 Angular 组件提供便利方法，包括等待页面加载、对承诺的期望等

+   它封装了 Selenium 方法，这些方法会自动等待承诺得到满足

+   它由 **SaaS**（**软件即服务**）提供商支持，例如 Sauce Labs，可在 [`saucelabs.com/`](https://saucelabs.com/) 找到

+   它由维护 Angular 和 Google 的同一家公司支持和维护。

# 准备使用 Protractor

是时候开始动手安装和配置 Protractor 了。安装和应用程序不断变化。主要关注的是本书中使用的特定配置，而不是深入的安装指南。有几种不同的配置，因此请查阅 Protractor 网站，以获取更多详细信息。要找到最新的安装和配置指南，请访问 [`angular.github.io/protractor/`](http://angular.github.io/protractor/)。

## 安装 WebDriver 的先决条件

Protractor 有以下先决条件：

+   **Node.js**：Protractor 是一个使用 npm 可用的 Node.js 模块。安装 Node.js 的最佳方式是遵循官方站点 [`nodejs.org/download/`](http://nodejs.org/download/) 上的说明。

+   **Chrome**：这是由 Google 构建的 Web 浏览器。它将在 Protractor 中运行端到端测试，而无需 Selenium 服务器。请遵循官方站点 [`www.google.com/chrome/browser/`](http://www.google.com/chrome/browser/) 上的安装说明。

+   **Chrome 的 Selenium WebDriver**：这是一个允许您与 Web 应用程序交互的工具。Selenium WebDriver 与 Protractor 的 `npm` 模块一起提供。我们将按照安装 Protractor 的说明进行操作。

## 安装 Protractor

安装 Protractor 的步骤如下：

1.  一旦 Node.js 安装并可在命令提示符中使用，请输入以下命令以在当前目录中安装 Protractor：

    ```js
    $ npm install protractor

    ```

1.  前面的命令使用 Node 的 `npm` 命令在当前本地目录中安装 Protractor。

1.  要在命令提示符中使用 Protractor，请使用 Protractor bin 目录的相对路径。

1.  按以下方式测试 Protractor 版本是否可以确定：

    ```js
    $ ./node_modules/protractor/bin/protractor --version

    ```

## 安装 Chrome WebDriver

安装 Chrome WebDriver 的步骤如下：

1.  要安装 Chrome 的 Selenium WebDriver，请转到 Protractor 的 `bin` 目录中的 `webdriver-manager` 可执行文件，该文件位于 `./node_modules/protractor/bin/`，然后输入以下内容：

    ```js
    $ ./node_modules/protractor/bin/webdriver-manager update

    ```

1.  确认目录结构。

1.  前面的命令将在项目中创建一个包含所需 Chrome 驱动程序的 Selenium 目录。

安装现已完成。Protractor 和 Chrome 的 Selenium WebDriver 都已安装。我们现在可以继续进行配置。

## 自定义配置

在本节中，我们将按照以下步骤配置 Protractor：

1.  从标准模板配置开始。

1.  幸运的是，Protractor 的安装目录中包含了一些基本的配置。

1.  我们将使用的是位于`protractor/example`部分的名为`conf.js`的文件。

1.  查看示例配置文件：

    `capabilities`参数应仅指定浏览器的名称：

    ```js
              exports.config = {  
              //...  
              capabilities: { 
                'browserName': 'chrome' 
              },   
              //... 
              }; 

    ```

    `framework`参数应指定测试框架的名称，我们将在这里使用 Jasmine：

    ```js
              exports.config = {  
              //...  
              framework: 'jasmine'   
              //... 
              };
    ```

    最后一个重要的配置是源文件声明：

    ```js
              exports.config = { 
                //... 
                specs: ['example_spec.js'], 
                //... 
              }; 

    ```

太棒了！现在我们已经安装并配置了 Protractor。

## 确认安装和配置

要确认安装，Protractor 至少需要一个在`specs`配置部分中定义的文件。在添加真实测试并使事情复杂化之前，请在根目录中创建一个名为`confirmConfigTest.js`的空文件。然后，编辑位于项目根目录中的`conf.js`文件，并将测试文件添加到`specs`部分，使其看起来如下：

```js
specs: ['confirmConfigTest.js'],

```

要确认 Protractor 已经安装，请进入项目目录的根目录，并输入以下命令：

```js
 $ ./node_modules/protractor/bin/protractor conf.js

```

如果一切设置正确并且安装成功，我们将在命令提示符中看到类似以下的内容：

```js
Finished in 0.0002 seconds
0 tests, 0 assertions, 0 failures

```

## 常见的安装和配置问题

以下是一些在安装 Chrome WebDriver 时可能会遇到的一些常见问题：

| **问题** | **解决方案** |
| --- | --- |
| Selenium 未正确安装 | 如果测试出现与 Selenium WebDriver 位置相关的错误，您需要确保您已遵循更新 WebDriver 的步骤。更新步骤会将 WebDriver 组件下载到本地的 Protractor 安装文件夹中。在 WebDriver 更新之前，您无法在 Protractor 配置中引用它。一个简单的方法是查看 Protractor 目录，并确保存在一个 Selenium 文件夹。 |
| 找不到测试 | 当 Protractor 没有执行任何测试时，可能会让人感到沮丧。最好的开始地方是配置文件。请确保相对路径以及任何文件名或扩展名都是正确的。 |

有关完整列表，请参阅官方 Protractor 网站[`angular.github.io/protractor/`](http://angular.github.io/protractor/)。

# 将 Protractor 集成到 Angular 中

到目前为止，我们已经看到了如何安装和配置 Protractor，我们还对 Protractor 的工作原理有一个基本的概述。在本节中，我们将介绍将 Protractor 集成到现有 Angular 项目中的过程，其中我们只有单元测试，并了解 Protractor 在实际的端到端测试中的应用。

## 获取现有项目

此测试中的代码将利用来自第三章，“Karma 方式”的单元测试代码。我们将把代码复制到一个名为`angular-protractor`的新目录中。

作为提醒，该应用程序是一个待办事项应用程序，其中包含一些待办事项列表中的项目；让我们再添加一些项目到列表中。它有一个单独的组件类，`AppComponent`，其中包含一个项目列表和一个`add`方法。当前的代码目录应该按照以下结构组织：

![获取现有项目](img/image_04_001.jpg)

在获得这种结构后，第一项任务是运行以下命令，在本地获取所需的依赖项`node_modules`：

```js
$ npm install

```

这将安装所有必需的模块；接下来，让我们使用`npm`命令构建和运行项目：

```js
$ npm start

```

一切都应该正常；项目应该在`http://localhost:3000`上运行，输出应该如下所示：

![获取现有项目](img/image_04_002.jpg)

是的，我们已经准备好进入下一步，在 Angular 项目中实现 Protractor。

## Protractor 设置流程

设置将与我们在本章前面看到的安装和配置步骤相匹配：

1.  安装 Protractor。

1.  更新 Selenium WebDriver。

1.  根据示例配置配置 Protractor。

我们将在新的项目目录中遵循我们在上一节中涵盖的 Protractor 安装和配置步骤。唯一的区别是 Protractor 测试可以带有 e2e 前缀，例如`**.e2e.js`。这将使我们能够轻松地在项目结构中识别 Protractor 测试。

### 小贴士

这完全取决于开发者的选择；有些人只是将 Protractor 测试放在一个新的目录中，并有一个子目录，`spec/e2e`。这只是项目结构的一部分。

## 安装 Protractor

我们可能已经全局设置了 Protractor，也可能没有，所以总是很好在项目中安装 Protractor。因此，我们将本地安装 Protractor 并将其添加到`package.json`中作为`devDependency`。

要在我们的项目中安装 Protractor，请从项目目录运行此命令：

```js
$ npm install protractor -save-dev

```

我们可以如下检查 Protractor：

```js
$ ./node_modules/protractor/bin/protractor --version

```

这应该提供最新版本，4.0.10，如下所示：

```js
Version 4.0.10

```

### 小贴士

**我们将遵循的良好实践**

我们展示了如何在目录中设置 Protractor，但最好使用以下命令全局安装 Protractor：

```js
$ npm install -g protractor

```

这样我们可以轻松地从命令行调用 Protractor，就像使用`protractor`一样；要了解 Protractor 版本，我们可以如下调用它：

```js
$ protractor -version

```

## 更新 WebDriver

要更新 Selenium WebDriver，请转到 Protractor 的`bin`目录中的`webdriver-manager`可执行文件，该文件位于`./node_modules/protractor/bin/`，并输入以下内容：

```js
$ ./node_modules/protractor/bin/webdriver-manager update

```

根据通知，我们将全局安装 Protractor，如果这样做，我们也将全局安装`webdriver-manager`命令，这样我们就可以轻松地运行它进行`update`，如下所示：

```js
$ webdriver-manager update

```

这将更新 WebDriver 并支持最新浏览器。

## 准备工作

我们已经克隆了快速入门项目样本，该项目已经集成了并配置了 Protractor。为了学习目的，我们希望在现有项目中集成 Protractor。

要做到这一点，我们必须从项目根目录中删除现有的`protractor.config.js`文件。

## 设置核心配置

如我们之前所见，Protractor 配置将存储在一个 JS 文件中。我们需要在项目根目录中创建一个配置文件；让我们将其命名为`protractor.config.js`。

目前，请保持可变字段为空，因为这些字段取决于项目结构和配置。因此，初始外观可能如下所示，并且这些配置选项是我们所熟知的：

```js
exports.config = { 

    baseUrl: ' ', 

    framework: 'jasmine', 

    specs: [], 

    capabilities: { 
        'browserName': 'chrome' 
    } 

}; 

```

只要我们的项目在本地端口`3000`上运行，我们的`baseUrl`变量将如下所示：

```js
exports.config = { 
    // ....  
    baseUrl: ' http://localhost:3000', 
    // ....  
}; 

```

我们计划将我们的端到端测试 spec 放在与单元测试文件相同的文件夹中，即`app/app.component.spec.ts`。这次它将有一个新的 e2e 前缀，看起来像`app/app.component.e2e.ts`。基于这一点，我们的 spec 和配置将更新：

```js
exports.config = { 
    // ....  
    specs: [ 
        'app/**/*.e2e.js' 
    ], 
    // ..... 
}; 

```

只要是一个 Angular 项目，我们需要传递一个额外的配置，`useAllAngular2AppRoots: true`，因为它将告诉 Protractor 等待页面上所有 Angular 应用的根元素，而不仅仅是匹配的一个根元素：

```js
exports.config = { 
    // ....  
    useAllAngular2AppRoots: true, 
    // ..... 
}; 

```

我们通过 node 服务器运行我们的项目；因此，我们需要传递一个额外的配置选项，以便 Jasmine 本身支持 node。这个配置在 Jasmine 2.x 版本中是必须的，但如果我们使用 Jasmine 1.x，可能不需要它。在这里，我们在`jasmineNodeOpts`中添加了两个最常用的选项；有一些是基于需求使用的：

```js
exports.config = { 
    // ....  
    jasmineNodeOpts: { 
        showColors: true, 
        defaultTimeoutInterval: 30000 
    }, 
    // ..... 
}; 

```

## 深入测试细节

要运行 Protractor 测试，我们需要两个文件：一个是我们在项目根目录中已经创建的配置文件，名为`protractor.conf.js`，另一个是 spec 文件，我们将在这里定义端到端测试 spec，它将位于 app 文件夹中，名为`app/app.component.e2e.ts`。

因此，让我们看看我们应该在那里定义的文件：

```js
describe('Title for test suite', () => { 

    beforeEach(() => { 
        // ...  
    }); 

    it('Title for test spec', () => { 
        // ... 
    }); 

});; 

```

这些语法应该是我们所熟知的，因为我们已经在单元测试套件中使用了 Jasmine 语法。

让我们快速回顾一下

+   `describe`：这个包含要运行的测试套件的代码块。

+   `beforeEach`：这个用于包含设置代码，它被用于每个测试 spec 中。

+   `it`：这个用于定义测试 spec 并包含运行该测试 spec 的特定代码。

运行任何网站的端到端测试的主要部分是获取该网站的 DOM 元素，然后通过测试过程与这些元素交互。因此，我们需要获取我们正在运行的项目中的 DOM 元素。

只要当前项目在浏览器中运行，我们首先需要获取浏览器本身的实例；有趣的是，Protractor 通过全局浏览器对象提供这一点。有了这个浏览器对象，我们可以获取所有浏览器级别的命令，例如 `browser.get`，并且我们可以导航到我们的项目 URL：

```js
beforeEach(() => { 
    browser.get(''); 
});; 

```

使用这个 `browser.get('')` 方法，我们将导航到我们项目的根目录。

我们有全局浏览器对象，我们可以用它来获取正在运行的页面的标题，这基本上是我们在这里项目 `index.html` 文件中定义的标题。`browser.getTitle` 将提供标题，然后我们可以按预期匹配它。所以，我们的测试规范将看起来像这样：

```js
it('Browser should have a defined title', () => { 
       expect(browser.getTitle()).toEqual('Angular Protractor'); 
}); 

```

如果我们快速浏览一下，我们的简短端到端测试规范将看起来如下：

```js
describe('AppComponent Tests', () => { 
    beforeEach(() => { 
        browser.get(''); 
    }); 

    it('Browser should have a defined title', () => { 
        expect(browser.getTitle()).toEqual('Angular Protractor'); 
    }); 
}); 

```

是时候使用 Protractor 运行端到端测试了。命令看起来如下所示：

```js
$ protractor protractor.conf.js

```

结果正如预期--没有失败，因为我们已经将 `index.html` 页面标题设置为 **Angular Protractor**。结果将如下所示：

```js
1 spec, 0 failures 
Finished in 1.95 seconds 

```

现在是时候继续并添加一个新的测试规范来测试页面上剩余的 DOM 元素了，其中我们在页面上列出了列表项；因此，我们将通过 Protractor 自动测试它们。

首先，我们将检查我们是否列出了所有三个项目。我们已经在本章的早期部分学习了关于一些 Protractor 常见 API 的内容，但快速回顾一下，我们将使用 `element.all` 方法通过传递一些定位器（`by.css`、`by.id` 和 `by.model`）来获取元素数组对象。然后，我们可以使用 Jasmine 匹配器与预期值匹配，如下所示：

```js
it('Should get the number of items as defined in item object', () => { 
        var todoListItems = element.all(by.css('li')); 
        expect(todoListItems.count()).toBe(3); 
    }); 

```

我们应该得到通过的结果，因为我们已经在 UI 中列出了三个项目。

我们可以添加一些额外的测试规范来测试 UI 元素。例如，为了检查列出的项目是否按正确顺序排列，我们可以检查它们的标签，如下所示：

```js
it('Should get the first item text as defined', () => { 
        expect(todoListItems.first().getText()).toEqual('test'); 
    }); 

    it('Should get the last item text as defined', () => { 
        expect(todoListItems.last().getText()).toEqual('refactor'); 
    }); 

```

我们已经将第一个和最后一个项目的标签/文本与预期值匹配，它也应该通过。

让我们将 e2e 文件中的所有测试规范合并起来。它将看起来像这样：

```js
describe('AppComponent Tests', () => { 
    var todoListItems = element.all(by.css('li')); 

    beforeEach(() => { 
        browser.get('/'); 
    }); 

    it('Browser should have a defined title', () => { 
        expect(browser.getTitle()).toEqual('Angular Protractor'); 
    }); 

    it('Should get the number of items as defined in item object', () 
    => { 
        expect(todoListItems.count()).toBe(3); 
    }); 

    it('Should get the first item text as defined', () => { 
        expect(todoListItems.first().getText()).toEqual('test'); 
    }); 

    it('Should get the last item text as defined', () => { 
        expect(todoListItems.last().getText()).toEqual('refactor'); 
    }); 
}); 

```

让我们一次性运行所有规范：

```js
$ protractor protractor.conf.js

```

如预期，所有测试都应该通过，结果将如下所示：

```js
4 specs, 0 failures
Finished in 2.991 seconds

```

### 小贴士

只要我们命名我们的 Protractor 配置文件为 `protractor.conf.js`，在运行时就不需要提及配置文件名；Protractor 会自己获取其配置文件。如果使用其他名称，我们应该在 Protractor 中提及配置文件名。

因此，在这种情况下，我们只需按照以下方式运行测试：

```js
$ protractor

```

结果将与之前相同。

## 通过 NPM 运行测试

在这个项目中，我们将通过 npm 构建和运行项目。在 第三章，*Karma 方法*中，我们通过 `npm` 运行了 `karma` 测试；同样，我们也将通过 `npm` 运行 `protractor` 测试。为此，我们必须在我们的项目 `package.json` 的 `scripts` 部分添加 `protractor`：

```js
"scripts": { 
    // ...  
    "e2e": "protractor" 
    // .... 
  }; 

```

要在我们的项目中安装 `protractor`，从项目目录运行：

```js
$ npm e2e

```

在某些操作系统上，此命令可能会产生一些`npm`错误。这实际上是针对`webdriver-manager`的，可能没有更新。为了解决这个问题，我们必须将`webdriver-manager`更新脚本添加到`npm`中，并在第一次运行时只运行一次，如下所示：

```js
"scripts": { 
    // ...  
    "webdriver-update": "webdriver-manager update" 
    // .... 
  }; 

```

我们还必须以以下方式运行它：

```js
$ npm webdriver-update

```

就这样，我们准备好再次运行 e2e 测试，这应该与`protractor`命令完全相同。

让我们确认这一点：

```js
$ npm run e2e

```

预期的结果将如下所示：

```js
4 specs, 0 failures
Finished in 2.991 seconds

```

# 提高测试质量

本章中讨论的一些内容需要进一步澄清。这些包括以下内容：

+   异步逻辑在哪里？

+   我们究竟如何真正地通过端到端测试实现 TDD？

## 异步魔法

在前面的测试中，我们看到了一些你可能质疑的魔法。以下是我们简要了解的一些魔法组件：

+   在测试执行前加载页面

+   在承诺中加载的元素上的断言

### 在测试执行前加载页面

在之前的测试中，我们使用了以下代码来指定浏览器应指向主页：

```js
browser.get(''); 

```

之前的命令将启动浏览器并导航到`baseUrl`位置。一旦浏览器到达页面，它将必须加载 Angular 并实现 Angular 特定的功能。我们的测试没有任何等待逻辑，这正是 Protractor 与 Angular 结合的美丽之处。页面加载的等待已经为我们内置到框架中。然后我们可以非常干净地编写测试。

### 在承诺中加载的元素上的断言

断言和期望已经包含了承诺满足的代码。在我们的测试中，我们编写断言，使其期望计数为`3`：

```js
expect(todoListItems.count()).toBe(3); 

```

然而，在现实中，我们可能认为我们需要将异步测试添加到断言中，以便等待承诺得到满足，涉及一些更复杂的东西，如下所示：

```js
it('Should get the number of items as defined in item object', (done) => { 
    var todoListItems = element.all(by.css('li')); 
    todoListItems.count().then(function(count){ 
        expect(count).toBe(3); 
        done(); 
    }); 
}); 

```

之前的代码更长，更细粒度，也更难以阅读。Protractor 具有使测试对某些内置期望的元素更加简洁的能力。

## 使用 Protractor 进行 TDD

在我们的第一个测试中，端到端测试和单元测试之间有一个清晰的区分。在单元测试中，我们关注将测试与代码紧密耦合。例如，我们的单元测试监视了特定组件类`AppComponent`的作用域。我们必须初始化组件以获取组件的实例，如下所示：

```js
import {AppComponent} from "./app.component"; 

beforeEach(() => { 
    app = new AppComponent(); 
}); 

```

在 Protractor 测试中，我们不关心我们正在测试哪个组件类，我们的重点是测试的用户视角。我们从选择 DOM 中的特定元素开始；在我们的例子中，该元素与 Angular 相关联。断言是特定重复器的元素数量等于预期的计数。

通过端到端测试的松散耦合，我们可以编写一个专注于用户需求的测试，最初显示三个元素，然后有自由选择在页面、类、组件等地方以我们想要的方式编写。

# 自我测试问题

使用 Protractor 进行 TDD 开发第三个开发待办事项。

Q1\. Protractor 使用以下哪个框架？

+   Selenium

+   Unobtanium

+   Karma

Q2\. 你可以使用任何现有的 Angular 项目安装 Protractor。

+   正确

+   错误

Q3\. Karma 和 Protractor 可以在单个项目中一起运行。

+   正确

+   错误

Q4\. 哪个团队开发了 Protractor？

+   ReactJS 团队

+   Angular 团队

+   NodeJS 团队

# 摘要

本章为我们提供了使用 Protractor 进行端到端测试的概述，并提供了安装、配置和使用现有 Angular 项目进行端到端测试的必要思路。Protractor 是测试任何 Angular 应用程序的重要组成部分。它架起了桥梁，确保用户的规格工作如预期。当端到端测试根据用户规格编写时，应用程序的信心和重构能力都会增强。在接下来的章节中，我们将通过简单直接示例深入了解如何应用 Karma 和 Protractor。

下一章将带我们深入了解 Protractor，包括一些高级配置、一些 API 的细节，并且还会调试测试。
