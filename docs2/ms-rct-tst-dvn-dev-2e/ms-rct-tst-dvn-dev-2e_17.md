# 17

# 编写您的第一个 Cucumber 测试

测试驱动开发主要是一个面向开发者的过程。有时，客户和产品所有者也想看到自动化测试的结果。不幸的是，作为 TDD 基础的谦逊的单元测试太低级，对非开发者没有帮助。这就是**行为驱动开发（BDD）**理念出现的地方。

BDD 测试有一些特性，使它们与您迄今为止看到的单元测试有所不同：

+   它们是**端到端测试**，在整个系统中运行。

+   它们是用自然语言而不是代码编写的，既可被非编码者理解，也可被编码者理解。

+   他们避免提及内部机制，而是专注于系统的外部行为。

+   测试定义描述了自身（与单元测试不同，您需要编写一个与代码匹配的测试描述）。

+   语法设计是为了确保您的测试被编写为示例，并且作为行为的离散规范。

BDD 工具与 TDD 和单元测试的比较

您在这本书中迄今为止看到的 TDD 风格将测试（在大多数情况下）视为指定行为的示例。此外，我们的测试始终遵循**安排-行动-断言（AAA）**模式。然而，请注意，单元测试工具如 Jest 并不强迫您以这种方式编写测试。

这就是为什么存在 BDD 工具的原因之一：在您指定系统行为时，迫使您非常明确。

本章介绍了两个新的软件包：Cucumber 和 Puppeteer。

我们将使用 Cucumber 来构建我们的 BDD 测试。Cucumber 是一个存在于许多不同编程环境中的系统，包括 Node.js。它由一个测试运行器组成，该运行器运行包含在**特性文件**中的测试。特性是用一种称为**Gherkin**的普通英语编写的。当 Cucumber 运行您的测试时，它将这些特性文件转换为函数调用；这些函数调用是用 JavaScript **支持脚本**编写的。

由于 Cucumber 有自己的测试运行器，因此它不使用 Jest。然而，我们将在一些测试中利用 Jest 的`expect`包。

Cucumber 不是编写系统测试的唯一方式

另一个流行的测试库是 Cypress，它可能更适合您和/或您的团队。Cypress 强调结果的视觉呈现。我倾向于避免使用它，因为它的 API 与行业标准测试模式非常不同，这增加了开发者需要具备的知识量。Cucumber 是跨平台的，测试看起来与您在这本书中看到的标准单元测试非常相似。

**Puppeteer**与 JSDOM 库执行类似的功能。然而，虽然 JSDOM 在 Node.js 环境中实现了一个假的 DOM API，Puppeteer 则使用真实的网络浏览器 Chromium。在这本书中，我们将以*无头*模式使用它，这意味着你不会在屏幕上看到应用程序的运行；但如果你愿意，你也可以关闭无头模式。Puppeteer 附带了许多附加功能，例如截图功能。

跨浏览器测试

如果你想测试你应用程序的跨浏览器支持，你可能更倾向于查看像 Selenium 这样的替代方案，这本书中没有涵盖 Selenium。然而，当为 Selenium 编写测试时，相同的测试原则同样适用。

本章涵盖了以下主题：

+   将 Cucumber 和 Puppeteer 集成到你的代码库中

+   编写你的第一个 Cucumber 测试

+   使用数据表进行设置

到本章结束时，你将很好地了解 Cucumber 测试是如何构建和运行的。

# 技术要求

本章的代码文件可以在以下位置找到：

[`github.com/PacktPublishing/Mastering-React-Test-Driven-Development-Second-Edition/tree/main/Chapter17`](https://github.com/PacktPublishing/Mastering-React-Test-Driven-Development-Second-Edition/tree/main/Chapter17)

# 将 Cucumber 和 Puppeteer 集成到你的代码库中

让我们向我们的项目添加必要的包：

1.  首先，安装我们需要的包。除了 Cucumber 和 Puppeteer，我们还将引入`@babel/register`，这将使我们能够在支持文件中使用 ES6 功能：

    ```js
    $ npm install --save-dev @cucumber/cucumber puppeteer 
    $ npm install --save-dev @babel/register
    ```

1.  接下来，创建一个名为`cucumber.json`的新文件，并包含以下内容。这有两个设置；`publishQuiet`关闭了在运行测试时出现的许多噪音，而`requireModule`在运行测试之前连接`@babel/register`：

    ```js
    {
      "default": {
        "publishQuiet": true,
        "requireModule": [
          "@babel/register"
        ]
      }
    }
    ```

1.  创建一个名为`features`的新文件夹。这个文件夹应该与`src`和`test`处于同一级别。

1.  在其中创建一个名为`features/support`的文件夹。

你现在可以使用以下命令运行测试：

```js
$ npx cucumber-js
```

你会看到如下输出：

```js
0 scenarios
0 steps
0m00.000s
```

在本章和下一章中，缩小你正在运行的测试范围可能会有所帮助。你可以通过提供测试运行器的场景文件名和起始行号来运行单个场景：

```js
$ npx cucumber-js features/drawing.feature:5
```

这就是使用 Cucumber 和 Puppeteer 设置的全部内容——现在是我们编写测试的时候了。

# 编写你的第一个 Cucumber 测试

在本节中，你将为我们已经构建的 Spec Logo 应用程序的一部分构建一个 Cucumber 功能文件。

关于 Gherkin 代码样本的警告

如果你正在阅读这本书的电子版，在复制粘贴功能定义时要小心。你可能会发现代码中插入了 Cucumber 无法识别的额外换行符。在运行测试之前，请检查你粘贴的代码片段，并删除任何不应该存在的换行符。

让我们开始吧！

1.  在运行任何 Cucumber 测试之前，确保通过运行 `npm run build` 来更新你的构建输出是很重要的。你的 Cucumber 规范将针对 `dist` 目录中构建的代码运行，而不是 `src` 目录中的源代码。

利用 package.json 脚本的优势

你也可以修改 `package.json` 脚本来在运行 Cucumber 规范之前调用构建，或者以监视模式运行 webpack。

1.  创建一个名为 `features/sharing.feature` 的新文件，并输入以下文本。一个功能有一个名称和简短描述，以及一系列按顺序列出的场景。我们现在只从一个场景开始：

    ```js
    Feature: Sharing
      A user can choose to present their session to any 
      number of other users, who observe what the 
      presenter is doing via their own browser.
      Scenario: Observer joins a session
        Given the presenter navigated to the application page
        And the presenter clicked the button 'startSharing'
        When the observer navigates to the presenter's sharing link
        Then the observer should see a message saying 'You are now watching the session'
    ```

Gherkin 语法

**Given**, **When**, 和 **Then** 与你的 Jest 测试的 **Arrange**, **Act**, 和 **Assert** 阶段类似：*given* 所有这些条件都为真，*when* 我执行这个操作，*then* 我期望所有这些事情发生。

理想情况下，你每个场景中只有一个 **When** 子句。

你会注意到，我已经将 **Given** 子句写成过去时，**When** 子句写成现在时，而 **Then** 子句中有一个“should”。

1.  在命令行中键入 `npx cucumber-js` 来运行该功能。你会看到一条警告信息，如下面的代码块所示。Cucumber 在第一个 `Given...` 语句处停止处理，因为它找不到与之对应的 JavaScript 支持函数。在警告信息中，Cucumber 有助于为你提供了定义的起点：

    ```js
    ? Given the presenter navigated to the application page
       Undefined. Implement with the following snippet:
         Given('the presenter navigated to the application page', function () {
           // Write code here that turns the phrase above
           // into concrete actions
           return 'pending';
         });
    ```

1.  让我们按照它建议的去做。创建一个名为 `features/support/sharing.steps.js` 的文件，并添加以下代码。它定义了一个步骤定义，该定义调用 Puppeteer 的 API 来启动一个新的浏览器，然后打开一个新页面，然后导航到提供的 URL。步骤定义描述与我们的测试场景中的 **Given** 子句相匹配。

1.  `async` 关键字的第二个参数。这是对 Cucumber 在其建议函数定义中告诉我们的内容的补充。我们需要 `async`，因为 Puppeteer 的 API 调用都返回 promises，我们需要对它们进行 `await`：

    ```js
    import {
      Given, When, Then
    } from "@cucumber/cucumber";
    import puppeteer from "puppeteer";
    const port = process.env.PORT || 3000;
    const appPage = `http://localhost:${port}/index.xhtml`;
    Given(
      "the presenter navigated to the application page",
      async function () {
        const browser = await puppeteer.launch();
        const page = await browser.newPage();
        await page.goto(appPage);
      }
    );
    ```

匿名函数，而非 lambda 表达式

你可能想知道为什么我们定义匿名函数（`async function (...) { ... }`）而不是 lambda 表达式（`async (...) => { ... }`）。这使我们能够利用匿名函数发生的隐式上下文绑定。如果我们使用 lambda 表达式，我们就需要在它们上调用 `.bind(this)`。

1.  再次运行你的测试。Cucumber 现在指定了下一个需要工作的子句。对于这个子句，`并且演示者点击了按钮 'startSharing'`，我们需要访问我们在上一个步骤中刚刚创建的`page`对象。要做到这一点，我们需要访问所谓的`World`对象，它是当前场景中所有子句的上下文。我们必须现在构建它。创建`features/support/world.js`文件并添加以下内容。它定义了两个方法，`setPage`和`getPage`，允许我们在世界中保存多个页面。对于这个测试来说，能够保存多个页面是非常重要的，因为我们至少有两个页面——演示者页面和观察者页面：

    ```js
    import {
      setWorldConstructor
    } from "@cucumber/cucumber";
    class World {
      constructor() {
        this.pages = {};
      }
      setPage(name, page) {
        this.pages[name] = page;
      }
      getPage(name) {
        return this.pages[name];
      }
    };
    setWorldConstructor(World);
    ```

1.  我们现在可以在我们的步骤定义中使用`setPage`和`getPage`函数。我们的方法将是首先从第一个步骤定义——我们在*步骤 3*中编写的——调用`setPage`，然后使用`getPage`在后续步骤中检索它。现在修改第一个步骤定义，包括对`setPage`的调用，如下面的代码块所示：

    ```js
    Given(
      "the presenter navigated to the application page",
      async function () {
        const browser = await puppeteer.launch();
        const page = await browser.newPage();
        await page.goto(appPage);
        this.setPage("presenter", page);
      }
    );
    ```

1.  接下来进行下一步，`演示者点击了按钮 'startSharing'`，我们将通过使用`Page.click` Puppeteer 函数来查找一个 ID 为`startSharing`的按钮来解决此问题。就像上一个测试一样，我们使用`buttonId`参数，这样这个步骤定义就可以在未来场景中用于其他按钮：

    ```js
    Given(
      "the presenter clicked the button {string}",
      async function (buttonId) {
        await this.getPage(
          "presenter"
        ).click(`button#${buttonId}`);
      }
    );
    ```

1.  下一步，`观察者导航到演示者的分享链接`，就像第一步那样，我们想要打开一个新的浏览器。不同的是，这是为观察者准备的，我们首先需要查找要遵循的路径。路径是通过演示者在开始搜索时显示的 URL 给出的。我们可以使用`Page.$eval`函数来查找：

    ```js
    When(
      "the observer navigates to the presenter's sharing link",
      async function () {
        await this.getPage(
          "presenter"
        ).waitForSelector("a");
        const link = await this.getPage(
          "presenter"
        ).$eval("a", a => a.getAttribute("href"));
        const url = new URL(link);
        const browser = await puppeteer.launch();
        const page = await browser.newPage();
        await page.goto(url);
        this.setPage("observer", page);
      }
    );
    ```

步骤定义重复

在我们的步骤定义之间正在积累一些重复。稍后，我们将把这个共性提取到它自己的函数中。

1.  最后一个步骤定义再次使用`Page.$eval` Puppeteer 函数，这次是为了找到一个 HTML 节点并将其转换为一个普通的 JavaScript 对象。然后我们使用`expect`函数以正常方式测试该对象。确保将列出的`import`语句放置在文件顶部：

    ```js
    import expect from "expect";
    ...
    Then(
      "the observer should see a message saying {string}",
      async function (message) {
        const pageText = await this.getPage(
          "observer"
        ).$eval("body", e => e.outerHTML);
        expect(pageText).toContain(message);
      }
    );
    ```

1.  使用`npx cucumber-js`运行你的测试。你的测试运行输出将如下所示。虽然我们的步骤定义是完整的，但似乎有些不对劲：

    ```js
    1) Scenario: Observer joins a session
       ✖ Given the presenter navigated to the application page
            Error: net::ERR_CONNECTION_REFUSED at http://localhost:3000/index.xhtml
    ```

1.  虽然我们的应用已经加载，但我们仍然需要启动服务器来处理我们的请求。为此，将以下两个函数添加到 `features/support/world.js` 中的 `World` 类，包括在文件顶部添加对应用的 `import` 语句。`startServer` 函数相当于我们在 `server/src/server.js` 中启动服务器的方式。`closeServer` 函数停止服务器，但在这样做之前，它会关闭所有 Puppeteer 浏览器实例。这样做很重要，因为当调用 `close` 方法时，服务器不会杀死任何活跃的 websocket 连接。我们需要确保它们首先关闭；否则，服务器将无法停止：

在同一项目中启动服务器

我们很幸运，所有代码都位于同一个项目中，因此可以在同一个进程中启动。如果你的代码库分布在多个项目中，你可能会发现自己需要处理多个进程。

```js
import { app } from "../../server/src/app";
class World {
  ...
  startServer() {
    const port = process.env.PORT || 3000;
    this.server = app.listen(port);
  }
  closeServer() {
    Object.keys(this.pages).forEach(name =>
      this.pages[name].browser().close()
    );
    this.server.close();
  }
}
```

1.  利用这些新函数通过 `Before` 和 `After` 钩子。创建一个新文件 `features/support/hooks.js` 并添加以下代码：

    ```js
    import { Before, After } from "@cucumber/cucumber";
    Before(function() {
      this.startServer();
    });
    After(function() {
      this.closeServer();
    });
    ```

1.  运行 `npx cucumber-js` 命令并观察输出。你的场景现在应该通过（如果没有通过，请再次检查你是否已经运行了 `npm run build`）：

    ```js
    > npx cucumber-js
    ......
    1 scenario (1 passed)
    4 steps (4 passed)
    0m00.848s
    ```

1.  让我们回到代码中，整理一下重复的部分。我们将提取一个名为 `browseToPageFor` 的函数，并将其放置在我们的 `World` 类中。打开 `features/support/world.js` 并在类底部添加以下方法：

    ```js
    async browseToPageFor(role, url) {
      const browser = await puppeteer.launch();
      const page = await browser.newPage();
      await page.goto(url);
      this.setPage(role, page);
    }
    ```

1.  此外，将 Puppeteer 的 `import` 语句从 `features/support/sharing.steps.js` 移动到 `features/support/world.js`：

    ```js
    import puppeteer from "puppeteer";
    ```

1.  最后，用 `browseToPageFor` 重新编写两个导航步骤：

    ```js
    Given(
      "the presenter navigated to the application page",
      async function () {
        await this.browseToPageFor("presenter", appPage);
      }
    );
    When(
      "the observer navigates to the presenter's sharing link",
      async function () {
        await this.getPage(
          "presenter"
        ).waitForSelector("a");
        const link = await this.getPage(
          "presenter"
        ).$eval("a", a => a.getAttribute("href"));
        const url = new URL(link);
        await this.browseToPageFor("observer", url);
      }
    );
    ```

在浏览器内和通过控制台日志进行观察

我们编写的测试以无头模式运行 Puppeteer，这意味着不会启动实际的 Chrome 浏览器窗口。如果你想看到这种情况发生，可以通过修改启动命令（记住在之前的步骤定义中有两个）来关闭无头模式，如下所示：

`const browser = await puppeteer.launch(`

`{ headless: false }`

`);`

如果你正在使用控制台日志来协助调试，你需要提供另一个参数来将控制台输出重定向到 `stdout`：

`const browser = await puppeteer.launch(`

`{ dumpio: true }`

`);`

你现在已经使用 Cucumber 和 Puppeteer 编写了一个 BDD 测试。接下来，让我们看看一个更高级的 Cucumber 场景。

# 使用数据表进行设置

在本节中，我们将探讨 Cucumber 的一个有用的节省时间特性：数据表。我们将编写第二个场景，就像之前的场景一样，将已经通过 Spec Logo 的现有实现：

1.  创建一个名为 `features/drawing.feature` 的新功能文件，内容如下。它包含一系列使用 Logo 函数绘制正方形的指令。使用小的边长 `10`，以确保动画快速完成：

    ```js
    Feature: Drawing
      A user can draw shapes by entering commands
      at the prompt.
      Scenario: Drawing functions
        Given the user navigated to the application page
        When the user enters the following instructions at the prompt:
          | to drawsquare |
          |   repeat 4 [ forward 10 right 90 ] |
          | end |
          | drawsquare |
        Then these lines should have been drawn:
          | x1 | y1 | x2 | y2 |
          | 0  | 0  | 10 | 0  |
          | 10 | 0  | 10 | 10 |
          | 10 | 10 | 0  | 10 |
          | 0  | 10 | 0  | 0  |
    ```

1.  第一个短语与我们的上一个步骤定义做的是同样的事情，只是我们将 `presenter` 重命名为 `user`。在这种情况下，使用更通用的名称是有意义的，因为演示者的角色对这个测试不再相关。我们可以使用 `World` 函数 `browseToPageFor` 来完成这个第一步。在共享功能中，我们使用了这个函数与一个包含要导航到的 URL 的 `appPage` 常量。现在让我们将这个常量拉入 `World`。在 `features/support/world.js` 文件中，在 `World` 类之上添加以下常量：

    ```js
    const port = process.env.PORT || 3000;
    ```

1.  将以下方法添加到 `World` 类中：

    ```js
    appPage() {
      return `http://localhost:${port}/index.xhtml`;
    }
    ```

1.  在 `features/support/sharing.steps.js` 文件中，删除 `port` 和 `appPage` 的定义，并更新第一个步骤定义，如下所示：

    ```js
    Given(
      "the presenter navigated to the application page",
      async function () {
        await this.browseToPageFor(
          "presenter",
          this.appPage()
        );
      }
    );
    ```

1.  是时候为用户页面创建一个新的步骤定义了。打开 `features/support/drawing.steps.js` 文件并添加以下代码：

    ```js
    import {
      Given,
      When,
      Then
    } from "@cucumber/cucumber";
    import expect from "expect";
    Given("the user navigated to the application page",
      async function () {
        await this.browseToPageFor(
          "user",
          this.appPage()
        );
      }
    );
    ```

1.  那么，关于带有数据表的第二行，我们的步骤定义应该是什么样子呢？让我们问问 Cucumber。运行 `npx cucumber-js` 命令并查看输出。它给出了我们定义的起点：

    ```js
    1) Scenario: Drawing functions
      ✔ Before # features/support/sharing.steps.js:5
      ✔ Given the user navigated to the application page
      ? When the user enters the following instructions at the prompt:
        | to drawsquare |
        |   repeat 4 [ forward 10 right 90 ] |
        | end |
        | drawsquare |
      Undefined. Implement with the following snippet:
      When('the user enters the following instructions at the prompt:',
        function (dataTable) {
          // Write code here that turns the phrase above
          // into concrete actions
          return 'pending';
        }
      );
    ```

1.  现在将建议的代码添加到 `features/supports/drawing.steps.js` 文件中。如果你现在运行 `npx cucumber-js`，你会注意到 Cucumber 成功地注意到步骤定义是挂起的：

    ```js
    When(
      "the user enters the following instructions at the prompt:",
      function (dataTable) {
        // Write code here that turns the phrase above
        //into concrete actions
        return "pending";
      }
    );
    ```

1.  `dataTable` 变量是一个具有 `raw()` 函数的 `DataTable` 对象，该函数返回一个数组的数组。外层数组代表每一行，内层数组代表每一行的列。在下一步定义中，我们希望将每一行都插入到编辑提示中。每一行后面应该跟着一个按下 *Enter* 键的操作。现在就创建它：

    ```js
    When(
      "the user enters the following instructions at the prompt:",
      async function (dataTable) {
        for (let instruction of dataTable.raw()) {
          await this.getPage("user").type(
            "textarea",
            `${instruction}\n`
          );
        }
      }
    );
    ```

1.  最后一步需要我们查找具有正确属性值的行元素，并将它们与我们的第二个数据表中的值进行比较。以下代码正是这样做的。现在复制这段代码并运行你的测试，以确保它工作并且测试能够通过。所有详细点的解释将会随后提供：

    ```js
    Then("these lines should have been drawn:",
      async function(dataTable) {
        await this.getPage("user").waitForTimeout(2000);
        const lines = await this.getPage("user").$$eval(
          "line",
          lines => lines.map(line => {
            return {
              x1: parseFloat(line.getAttribute("x1")),
              y1: parseFloat(line.getAttribute("y1")),
              x2: parseFloat(line.getAttribute("x2")),
              y2: parseFloat(line.getAttribute("y2"))
            };
          })
        );
        for (let i = 0; i < lines.length; ++i) {
          expect(lines[i].x1).toBeCloseTo(
            parseInt(dataTable.hashes()[i].x1)
          );
          expect(lines[i].y1).toBeCloseTo(
            parseInt(dataTable.hashes()[i].y1)
          );
          expect(lines[i].x2).toBeCloseTo(
            parseInt(dataTable.hashes()[i].x2)
          );
          expect(lines[i].y2).toBeCloseTo(
            parseInt(dataTable.hashes()[i].y2)
          );
        }
      }
    });
    ```

最后一个测试包含了一些值得深入研究的复杂性：

+   我们使用了 `Page.waitForTimeout` 来等待 2 秒，这给了系统完成动画的时间。包含这样的超时并不是一个好的做法，但暂时它是可行的。我们将在下一章中探讨使其更具体的方法。

+   `Page.$$eval` 函数类似于 `Page.$eval`，但在底层返回一个数组，并且调用 `document.querySelector` 而不是 `document.querySelectorAll`。

+   重要的是，我们所有的属性转换逻辑——从 HTML 行元素和属性移动到“纯”整数 `x1`、`y1` 等值——都应该在 `Page.$$eval` 的 `transform` 函数内完成。这是因为 Puppeteer 会在 `$$eval` 调用完成后回收任何 DOM 节点对象。

+   我们的行值需要用 `parseFloat` 来解析，因为我们所编写的 `requestAnimationFrame` 逻辑与整数端点不完全对齐——它们有非常微小的分数差异。

+   这也意味着我们需要使用 `toBeCloseTo` Jest 匹配器而不是 `toBe`，这是因为我们之前描述的分数值差异所必需的。

+   最后，我们在这里使用 `DataTable` 的 `hashes()` 函数来提取一个对象数组，该数组具有数据表中每列的一个键，基于我们在功能定义中提供的标题行。例如，我们可以调用 `hashes()[0].x1` 来提取第一行 `x1` 列的值。

继续使用 `npx cucumber-js` 运行你的测试。一切都应该通过。

你现在已经很好地理解了如何使用 Cucumber 数据表来制作更具说服力的 BDD 测试。

# 摘要

Cucumber 测试（以及一般的 BDD 测试）与我们在这本书的其余部分所编写的单元测试类似。它们专注于指定行为的 *示例*。它们应该使用真实的数据和数字作为测试一般概念的手段，就像我们在本章的两个例子中所做的那样。

BDD 测试与单元测试的不同之处在于，它们是系统测试（具有更广泛的测试范围）并且使用自然语言编写。

就像单元测试一样，在编写 BDD 测试时，找到简化代码的方法很重要。首要规则是尝试编写通用的 `World` 类或其它模块。我们已经在本章中看到了如何做到这一点的例子。

在下一章中，我们将使用 BDD 测试来驱动 Spec Logo 中新功能的实现。
