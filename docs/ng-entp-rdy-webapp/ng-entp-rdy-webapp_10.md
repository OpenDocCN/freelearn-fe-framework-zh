

# 第十章：RESTful API 和全栈实现

在 *第一章*，*Angular 简介及其概念* 中，我向你介绍了网络应用程序存在的更广泛的架构。在全栈架构中做出的选择可能会深刻影响你网络应用程序的成功。你绝对不能忽视你交互的 API 是如何设计的。在本章中，我们将介绍如何使用 Node、Express 和 Mongo 为你的前端实现后端。结合 Angular，这个软件堆栈被称为 MEAN 堆栈。

我对 MEAN 堆栈的看法是“最小化 MEAN”，它优先考虑易用性、幸福感和效率，这是优秀 **开发者体验**（**DX**）的主要成分。为了保持主题一致，我们将实现 LemonMart 服务器。这个服务器将完善来自 *第八章*，*设计认证和授权* 的 JWT 认证。此外，服务器还将支持我在 *第十一章*，*食谱 – 可重用性、路由和缓存* 和 *第十二章*，*食谱 – 主/详细、数据表和 NgRx* 中将要介绍的功能。

本章涵盖了大量的内容。它旨在作为 GitHub 仓库 ([`github.com/duluca/lemon-mart-server`](https://github.com/duluca/lemon-mart-server)) 的路线图。我涵盖了实现架构、设计和主要组件。我强调了一些重要的代码片段来解释解决方案是如何组合在一起的。然而，与前面的章节不同，你不能仅仅依靠文本中提供的代码示例来完成你的实现。对于本书的目的，理解我们为什么要实现各种功能比掌握实现细节更为重要。因此，对于这一章，我建议你阅读并理解服务器代码，而不是试图自己重新创建它。

你需要在章节的末尾采取行动，在你的 Angular 应用中实现一个自定义认证提供者，以对 **lemon-mart-server** 进行认证，并利用 Postman 生成测试数据，这在后面的章节中将会很有用。

我们首先介绍全栈架构，涵盖 lemon-mart-server 的单仓库设计以及如何使用 Docker Compose 运行一个包含 Web 应用、服务器和数据库的三层应用程序。然后，我们介绍 RESTful API 设计和文档，利用 OpenAPI 规范通过 [Swagger.io](http://Swagger.io) 和使用 Express.js 的实现。然后，我们介绍使用我的 DocumentTS 库实现 MongoDB 的 **对象文档映射器**（**ODM**），用于存储带有登录凭证的用户。我们实现了一个基于令牌的认证功能，并使用它来保护我们的 API。最后，我们利用 Postman 使用我们开发的 API 在数据库中生成测试数据。

在本章中，你将学习以下内容：

+   全栈架构

+   Docker Compose

+   RESTful API

+   MongoDB ODM with DocumentTS

+   实现 JWT 认证

+   使用 Postman 生成用户

书籍示例代码的最新版本可以在以下链接的 GitHub 仓库中找到。该仓库包含代码的最终和完成状态。本章需要 Docker 和 Postman 应用程序。

在您的开发环境中运行**lemon-mart-server**并使其与**lemon-mart**通信是至关重要的。请参阅此处或 GitHub 上的`README`中的说明以启动您的服务器。

对于本章的情况：

1.  使用`--recurse-submodules`选项克隆**lemon-mart-server**仓库：`git clone --recurse-submodules` [`github.com/duluca/lemon-mart-server`](https://github.com/duluca/lemon-mart-server)

1.  在 VS Code 终端中，执行`cd web-app; git checkout master`以确保来自[`github.com/duluca/lemon-mart`](https://github.com/duluca/lemon-mart)的子模块位于 master 分支。

    在后面的**Git 子模块**部分，您可以配置`web-app`文件夹以从您的 lemon-mart 服务器拉取。

1.  在根目录下执行`npm install`以安装依赖项。

    注意，在根目录下运行`npm install`命令会触发一个脚本，该脚本还会在`server`和`web-app`文件夹下安装依赖项。

1.  在根目录下执行`npm run init:env`以配置`.env`文件中的环境变量。

    此命令将在根目录和`server`文件夹下创建两个`.env`文件，用于包含您的私有配置信息。初始文件基于`example.env`文件生成。您可以在以后修改这些文件并设置自己的安全密钥。

1.  在根目录下执行`npm run build`以构建服务器和 Web 应用。

    注意，该 Web 应用是使用名为`--configuration=lemon-mart-server`的新配置构建的，它使用`src/environments/environment.lemon-mart-server.ts`。

1.  执行`docker-compose up --build`以运行服务器、Web 应用和 MongoDB 数据库的容器化版本。

    注意，Web 应用使用名为`nginx.Dockerfile`的新文件进行容器化。

1.  导航到`http://localhost:8080`以查看 Web 应用。

    登录时，单击**填写**按钮以使用默认的演示凭据填充电子邮件和密码字段。

1.  导航到`http://localhost:3000`以查看服务器登录页面。

1.  导航到`http://localhost:3000/api-docs`以查看交互式 API 文档。

1.  您可以使用`npm run start:database`仅启动数据库，并在`server`文件夹下使用`npm start`进行调试。

1.  您可以使用`npm run start:backend`仅启动数据库和服务器，并在`web-app`文件夹下使用`npm start`进行调试。

对于本章中基于**lemon-mart**的示例：

1.  克隆仓库：[`github.com/duluca/lemon-mart`](https://github.com/duluca/lemon-mart)

1.  在根目录下执行`npm install`以安装依赖项。

1.  此章节的代码示例位于子文件夹下：

    ```js
    projects/ch10 
    ```

1.  要为此章节运行 Angular 应用程序，请执行以下命令：

    ```js
    npx ng serve ch10 
    ```

1.  要为此章节运行 Angular 单元测试，请执行以下命令：

    ```js
    npx ng test ch10 --watch=false 
    ```

1.  要为此章节运行 Angular e2e 测试，请执行以下命令：

    ```js
    npx ng e2e ch10 
    ```

1.  要为此章节构建一个生产就绪的 Angular 应用程序，请执行以下命令：

    ```js
    npx ng build ch10 --prod 
    ```

    注意，存储库根目录下的`dist/ch10`文件夹将包含编译结果。

注意，书中或 GitHub 上的源代码可能并不总是与 Angular CLI 生成的代码匹配。由于生态系统不断演变，书中代码与 GitHub 上代码之间的实现也可能存在细微差异。随着时间的推移，示例代码发生变化是自然的。此外，在 GitHub 上，你可能会找到更正、修复以支持库的新版本，或者为读者观察而并排实现多种技术的示例。读者只需实现书中推荐的理想解决方案即可。如果你发现错误或有疑问，请为所有读者创建一个 GitHub 问题或提交一个 pull request。

你可以在附录 C“保持 Angular 和工具常青”中了解更多关于更新 Angular 的信息。你可以在网上从[`static.packt-cdn.com/downloads/9781838648800_Appendix_C_Keeping_Angular_and_Tools_Evergreen.pdf`](https://static.packt-cdn.com/downloads/9781838648800_Appendix_C_Keeping_Angular_and_Tools_Evergreen.)或[`expertlysimple.io/stay-evergreen`](https://expertlysimple.io/stay-evergreen)找到这个附录。

当你的 LemonMart 服务器运行起来后，我们就可以探索 MEAN 栈的架构了。到本节结束时，你应该有自己的 LemonMart 版本与服务器通信。

# 全栈架构

全栈指的是使应用程序工作的整个软件栈，从数据库到服务器、API 以及利用它们的 Web 和/或移动应用程序。传说中的全栈开发者无所不知，可以轻松地在职业的各个垂直领域操作。在所有与软件相关的事物中专业化，并被认为是每个给定主题的专家，几乎是不可能的。然而，要被认为是某个主题的专家，你也必须对相关主题有深入的了解。在学习新主题时，保持你的工具和语言一致非常有帮助，这样你就可以在没有额外噪音的情况下吸收新信息。因此，我选择用 Java 或 C#使用 ASP.NET 介绍你使用 MEAN 栈而不是 Spring Boot。通过坚持使用熟悉的工具和语言，如 TypeScript、VS Code、npm、GitHub、Jasmine、Docker 和 CircleCI，你可以更好地理解全栈实现是如何结合在一起的，并因此成为一个更好的 Web 开发者。

## 最小化 MEAN

为你的项目选择正确的-Stack™ 是困难的。首先，你的技术架构应该足够满足业务需求。例如，如果你试图使用 Node.js 实现人工智能项目，你很可能会选择错误的堆栈。我们的重点是交付网络应用，但除此之外，我们还有其他参数要考虑，包括以下内容：

+   易用性

+   幸福

+   效率

如果你的开发团队将长时间在你的应用程序上工作，那么考虑兼容性以外的因素非常重要。你的堆栈、工具选择和编码风格如果使代码库易于使用，让你的开发者感到快乐，或者让他们觉得自己是项目的有效贡献者，那么这些都会产生重大影响。

一个配置良好的堆栈对于优秀的开发体验至关重要。这可能是干燥的煎饼堆和带有适量黄油和糖浆的精美小堆之间的区别。

通过引入过多的库和依赖项，你可以减慢你的进度，使你的代码难以维护，并发现自己陷入引入更多库以解决其他库问题的反馈循环。赢得这场游戏的唯一方法就是简单地不玩。

如果你花时间学习如何使用几个基本的库，你可以成为一个更有效的开发者。本质上，你可以用更少的资源做更多的事情。我的建议是：

+   **在编写代码之前先思考**，并应用 80-20 规则。

+   **等待库和工具成熟，跳过测试版**。

+   **通过减少对新包和工具的贪婪，掌握基础知识来实现快速**。

    在 YouTube 上观看我 2017 年 Ng 会议的演讲，标题为 *用更少的 JavaScript 做更多的事情*，链接为 [`www.youtube.com/watch?v=Sd1aM8181kc`](https://www.youtube.com/watch?v=Sd1aM8181kc)。

这种简约思维是 minimal MEAN 的设计哲学。你可以在 GitHub 上查看参考实现 [`github.com/duluca/minimal-mean`](https://github.com/duluca/minimal-mean)。参考以下图表了解整体架构：

![](img/B14094_10_01.png)

图 10.1：Minimal MEAN 软件堆栈和工具

让我们逐个介绍架构的组件。

### Angular

Angular 是表现层。Angular 是一个能够和可靠的开发平台。它被广泛理解，拥有一个伟大的社区。在考虑其他选项之前，你绝对应该花时间掌握 Angular 的基础知识。

如 Angular Material、Angular Evergreen 和 `angular-unit-test-helper` 这样的库可以帮助你以最小的努力提供最佳和外观出色的解决方案。

你可以使用最小的 Docker 容器 `duluca/minimal-nginx-web-server` 或 `duluca/minimal-node-web-server` 来容器化你的 Angular（或任何其他网络应用）。

### Express

Express.js 将成为我们的 API 层。Express 是一个快速、无偏见且极简的 Node.js 网络框架。Express 拥有庞大的插件生态系统，几乎可以满足所有需求。在 Minimal MEAN 中，我们只利用了两个包：

+   `cors`: 配置跨源资源共享设置

+   `morgan`: 用于记录 HTTP 请求

此外，我们使用 express 解析器解析传入的 HTTP 请求中的`req.body`，并使用`express.static`函数来提供`public`文件夹的内容。

你可以在[`expressjs.com/`](https://expressjs.com/)了解更多关于 Express.js 的信息。

### Node

Express.js 运行在 Node.js 上。我们将使用 Node 实现业务层。Node 是一个轻量级且高效的 JavaScript 运行时，使用事件驱动、非阻塞 I/O 模型，使其适用于高性能和实时应用。Node 可以在任何地方运行，从冰箱到智能手表。你可以通过使用 TypeScript 开发应用程序来提高 Node 应用程序的可靠性。

有关非阻塞 I/O 的更深入解释，请参阅 Frank Rosner 的博客文章[`blog.codecentric.de/en/2019/04/explain-non-blocking-i-o-like-im-five/`](https://blog.codecentric.de/en/2019/04/explain-non-blocking-i-o-like-im-five/)。

在本章的后面部分，你将学习如何使用 TypeScript 配置 Node 项目。

### Mongo

MongoDB 代表持久层。MongoDB 是一个面向文档的数据库，具有动态的类似 JSON 的架构。使用基于 JSON 的数据库的主要好处是你不需要将数据从一种格式转换为另一种格式。你可以仅使用 JSON 检索、显示、编辑和更新数据。

此外，Node 的 MongoDB 原生驱动程序成熟、性能良好且功能强大。我开发了一个名为`document-ts`的库，旨在通过引入易于编码的丰富文档对象来简化与 MongoDB 的交互。DocumentTS 是一个非常薄的基于 TypeScript 的 MongoDB 辅助工具，具有可选的丰富 ODM 便利功能。

你可以在[`www.mongodb.com/`](https://www.mongodb.com/)了解更多关于 MongoDB 的信息，以及 DocumentTS 库的[`github.com/duluca/document-ts`](https://github.com/duluca/document-ts)。

### 工具

支持你开发的技术工具与你的软件栈选择一样重要。Minimal MEAN 利用以下工具：

+   **VS Code**: 优秀的扩展支持，轻量级、快速且跨平台

+   **TypeScript**: 快速且易于使用的转译器，具有使用 tslint 的出色 lint 支持

+   **Npm**: 多平台脚本和依赖管理，拥有丰富的包生态系统

+   **GitHub**: 灵活、免费且支持良好的 Git 托管服务。GitHub 流程与 CI 服务器协同，实现门控代码检查

+   **Docker**: 一种轻量级虚拟化技术，封装了你的环境配置和设置

+   **持续集成（CI）**: 确保代码交付质量的关键

+   **Jasmine**：包含电池的单元测试框架，与 nyc/istanbul.js 一起工作以提供代码覆盖率指标

注意，我们使用的工具和选择的语言与用于 Angular 开发的工具和语言相同。这使得开发者能够在前端和后端开发之间进行最小化的上下文切换。

现在我们已经涵盖了交付最小 MEAN 堆栈应用的所有主要组件和工具，让我们首先创建一个 Git 仓库，用于存放我们的前端和后端代码。

## 配置 monorepo

您可以通过创建包含您前端和后端代码的 monorepo 来优化您的开发体验。monorepo 允许开发者能够在同一 IDE 窗口内跳转项目。开发者可以更容易地在项目之间引用代码，例如在前端和后端之间共享 TypeScript 接口，从而确保数据对象每次都保持一致。CI 服务器可以一次性构建所有项目，以确保全栈应用的所有组件都处于正常工作状态。

注意，monorepo 与 VS Code 中的多根工作区不同，在多根工作区中，您可以将多个项目添加到同一 IDE 窗口中。monorepo 在源代码控制级别组合项目。有关多根工作区的更多信息，请参阅[`code.visualstudio.com/docs/editor/multi-root-workspaces`](https://code.visualstudio.com/docs/editor/multi-root-workspaces)。

让我们快速浏览一下代码库。

### Monorepo 结构

在`lemon-mart-server`项目中，您将拥有三个主要文件夹，如下所示：

```js
lemon-mart-server
├───bin
├───web-app (default Angular setup)
├───server
│   ├───src
│   │   ├───models
│   │   ├───public
│   │   ├───services
│   │   ├───v1
│   │   │   └───routes
│   │   └───v2
│   │       └───routes
│   └───tests
|   package.json
|   README.md 
```

`bin`文件夹包含辅助脚本或工具，`web-app`文件夹代表您的前端，而`server`包含后端源代码。在我们的案例中，`web-app`文件夹是`lemon-mart`项目。我们不是复制粘贴现有项目的代码，而是利用 Git 子模块将两个仓库链接在一起。

### Git 子模块

Git 子模块帮助您在多个仓库之间共享代码，同时保持提交的分离。前端开发者可能选择仅使用前端仓库进行工作，而全栈开发者将更喜欢访问所有代码。Git 子模块还为现有项目的组合提供了一个方便的方法。

首先，让我们看看您如何将您自己的`lemon-mart`项目作为`lemon-mart-server`的子模块添加，利用位于我们 monorepo 根目录`package.json`文件中的脚本：

我建议您在从 GitHub 克隆的**lemon-mart-server**版本上执行此操作。否则，您将需要创建一个新的项目并执行`npm init -y`以开始操作。

1.  观察以下`package.json`脚本，这些脚本有助于初始化、更新和清理 Git 子模块：

    ```js
    **package.json**
      "config": {
      ...
        "webAppGitUrl": "https://github.com/duluca/lemon-mart.git"
      },
      "scripts": {
        "webapp:clean": "cross-conf-env rimraf web-app && git rm -r --cached web-app",
        "webapp:init": "cross-conf-env git submodule add $npm_package_config_webAppGitUrl web-app",
        "postwebapp:init": "git submodule status web-app",
        "modules:init": "git submodule update --init --recursive",
        "modules:update": "git submodule update --recursive --remote"
      }, 
    ```

1.  将`webAppGitUrl`更新为您自己的项目的 URL。

1.  执行`webapp:clean`以删除现有的`web-app`文件夹。

1.  最后，执行 `webapp:init` 命令以在 `web-app` 文件夹中初始化你的项目：

    ```js
    $ npm run webapp:init 
    ```

接下来，执行 `modules:update` 命令以更新子模块中的代码。在另一个环境中克隆仓库后，要拉取子模块，请执行 `npm modules:init`。如果你需要重置环境并重新启动，请执行 `webapp:clean` 以清理 Git 缓存并删除文件夹。

注意，你的仓库中可以有多个子模块。`modules:update` 命令将更新所有子模块。

你的 Web 应用程序代码现在位于名为 `web-app` 的文件夹中。此外，你应该能够在 VS Code 的 **源代码管理** 面板中看到这两个项目，如图所示：

![](img/B14094_10_02.png)

图 10.2：VS Code 源代码管理提供者

使用 VS Code 的源代码管理，你可以独立地对任一仓库执行 Git 操作。

如果你的子模块出现混乱，只需在子模块目录中执行 `cd` 并运行 `git pull`，然后执行 `git checkout master` 以恢复主分支。使用此技术，你可以从项目中的任何分支检出并提交 PR。

现在我们已经准备好了子模块，让我们看看服务器项目是如何配置的。

### 使用 TypeScript 配置 Node 项目

要使用 TypeScript 创建新的 Node.js 应用程序，请执行以下步骤：

以下步骤仅适用于你正在创建新的服务器项目。我建议你使用从 GitHub 克隆的 `lemon-mart-server` 项目中提供的现有一个。

1.  创建子文件夹 `server`：

    ```js
    $ mkdir server 
    ```

1.  将当前目录更改为 `server` 文件夹：

    ```js
    $ cd server 
    ```

1.  初始化 npm 以在 `server` 文件夹中设置 `package.json`：

    ```js
    $ npm init -y 
    ```

    注意，顶级 `package.json` 将用于与全栈项目相关的脚本。`server/package.json` 将包含后端项目的脚本和依赖项。

1.  使用 `mrm-task-typescript-vscode` 配置你的仓库：

    ```js
    $ npm i -g mrm-task-typescript-vscode
    $ npx mrm typescript-vscode 
    ```

`mrm` 任务配置 VS Code 以获得优化的 TypeScript 开发体验，类似于我们在 *第二章*，*设置开发环境* 中使用 `mrm-task-angular-vscode` 所做的那样。

命令执行完毕后，`project` 文件夹将如所示出现：

```js
server
│   .gitignore
│   .nycrc
│   .prettierignore
│   .prettierrc
│   example.env
│   jasmine.json
│   package-lock.json
│   package.json
│   pull_request_template.md
│   tsconfig.json
│   tsconfig.src.json
│   tslint.json
│
├───.vscode
│       extensions.json
│       launch.json
│       settings.json
│
├───src
│       index.ts
│
└───tests
│       index.spec.ts
│       tsconfig.spec.json 
```

此任务配置以下内容：

+   常用 npm 脚本包：cross-conf-env ([`www.npmjs.com/package/cross-conf-env`](https://www.npmjs.com/package/cross-conf-env))、npm-run-all ([`www.npmjs.com/package/npm-run-all`](https://www.npmjs.com/package/npm-run-all))、dev-norms ([`www.npmjs.com/package/dev-norms`](https://www.npmjs.com/package/dev-norms)) 和 rimraf ([`www.npmjs.com/package/rimraf`](https://www.npmjs.com/package/rimraf))

+   Npm 脚本用于样式、代码检查、构建和测试：

    +   `style` 和 `lint`：检查代码样式和代码检查错误是否符合规范。它们主要用于 CI 服务器使用。

    +   `style:fix` 和 `lint:fix`: 将代码样式和检查规则应用到代码中。并非所有检查错误都可以自动修复。您需要手动解决每个错误。

    +   `build`: 将代码转换为 `dist` 文件夹。

    +   `start`: 在 Node.js 中运行转换后的代码。

    `prepublishOnly` 和 `prepare` 脚本仅在您开发 npm 包时相关。在这种情况下，您还应该实现一个 `.npmignore` 文件，该文件排除了 `src` 和 `tests` 文件夹。

+   `ImportSort`: 维护 `import` 语句的顺序：

    +   将设置添加到 `package.json`

    +   支持的 npm 包已安装：import-sort、import-sort-cli、import-sort-parser-typescript 和 import-sort-style-module

+   使用 TypeScript 和 tslint：

    +   `tsconfig.json`: 常见的 TypeScript 设置

    +   `tsconfig.src.json`: 适用于 `src` 文件夹下源代码的特定设置

    +   `tslint.json`: 检查规则

+   自动格式化我们代码样式的 Prettier 插件：

    +   `.prettierrc`: Prettier 设置

    +   `.prettierignore`: 忽略的文件

+   Jasmine 和 nyc 用于单元测试和代码覆盖率：

    +   `jasmine.json`: 测试设置。

    +   `.nycrc`: 代码覆盖率设置。

    +   `tests` 文件夹：包含 `spec.ts` 文件，其中包含您的测试和 `tsconfig.spec.json`，它配置了更宽松的设置，使快速编写测试变得更容易。

    +   在 `package.json` 中：创建测试脚本以使用 `build:test` 构建测试并使用 `npm test` 执行它们。`test:ci` 命令旨在用于 CI 服务器，而 `test:nyc` 提供代码覆盖率报告。

+   `example.env`：用于记录在您的私有 `.env` 文件中存在的必需环境变量

    +   `.env` 已添加到 `.gitignore`

+   PR 模板：一个请求开发者提供额外信息的拉取请求模板

+   VS Code 扩展、设置和调试配置分别在三份文件中：

    +   `.vscode/extensions.json`

    +   `.vscode/settings.json`

    +   `.vscode/launch.json`

一旦您熟悉了项目引入的更改，请验证您的项目是否处于正常工作状态。

通过执行测试来验证项目：

```js
$ npm test 
```

在运行 `test` 命令之前，执行 `npm run build && npm run build:test` 以将我们的 TypeScript 代码转换为 JavaScript。输出放置在 `dist` 文件夹中，如下所示：

```js
server
│
├───dist
│       index.js
│       index.js.map 
```

注意，在您的文件系统中，`.js` 和 `.js.map` 文件与每个 `.ts` 文件一起创建。在 `.vscode/settings.json` 中，我们配置了 `files.exclude` 属性，以在 IDE 中隐藏这些文件，这样它们就不会在开发期间分散开发者的注意力。此外，在 `.gitignore` 中，我们也忽略了 `.js` 和 `.js.map` 文件，这样它们就不会被提交到我们的仓库中。

现在我们有了基本的单仓库，我们可以配置我们的持续集成服务器。

### CircleCI 配置

使用 Git 子模块的一个好处是我们可以验证我们的前端和后端是否在相同的管道中工作。我们将实现两个任务：

1.  `build_server`

1.  `build_webapp`

这些任务将遵循此处显示的工作流程：

```js
**.circleci/config.yml**
...
workflows:
  version: 2
  build-and-test-compose:
    jobs:
      - build_server
      - build_webapp 
```

CI 管道将同时构建服务器和 Web 应用程序，如果主分支上的作业成功，可以选择运行`deploy`作业。有关如何在 GitHub 上的`config.yml`文件中实现`build_webapp`作业的说明，请参阅*第九章*，*使用 Docker 的 DevOps*，该作业与您在第九章中实现的类似，但包括一些细微的差异，以处理与子模块和文件夹结构变化的工作。构建服务器的管道与 Web 应用程序的管道不太相似，如下所示：

```js
**.circleci/config.yml**
version: 2.1
orbs:
  coveralls: coveralls/coveralls@1.0.4
jobs:
  build_server:
    docker:
      - image: circleci/node:lts
    working_directory: ~/repo/server
    steps:
      - checkout:
          path: ~/repo
      - restore_cache:
          keys:
            - web-modules-{{ checksum "package-lock.json" }}
      # check npm dependencies for security risks - 'npm audit' to fix
      - run: npx audit-ci --high --report-type full
      - run: npm ci
      - save_cache:
          key: web-modules-{{ checksum "package-lock.json" }}
          paths:
            - ~/.npm
      - run: npm run style
      - run: npm run lint
      # run tests and store test results
      - run: npm run pretest
      - run: npm run test:ci
      - store_test_results:
          path: ./test_results
      # run code coverage and store coverage report
      - run: npm run test:nyc
      - store_artifacts:
          path: ./coverage
      - coveralls/upload
      - run:
          name: Move compiled app to workspace
          command: |
            set -exu
            mkdir -p /tmp/workspace/server
            mv dist /tmp/workspace/server
      - persist_to_workspace:
          root: /tmp/workspace
          paths:
            - server 
```

管道会检出代码，使用`audit-ci`验证我们使用的软件包的安全性，安装依赖项，检查样式和 linting 错误，运行测试，并检查代码覆盖率水平。

测试命令隐式构建服务器代码，该代码存储在`dist`文件夹下。在最后一步，我们将`dist`文件夹移动到工作区，以便我们可以在以后使用它。

接下来，让我们看看我们如何将应用程序的所有层组合在一起，并使用 Docker Compose 运行它。

## Docker Compose

由于我们有一个三层架构，我们需要一种方便的方式来设置全栈应用程序的基础设施。您可以创建脚本来单独启动各种 Docker 容器，但有一个专门用于运行多容器应用程序的工具，称为 Docker Compose。Compose 使用名为`docker-compose.yml`的 YAML 文件格式，因此您可以声明性地定义应用程序的配置。Compose 允许您遵循基础设施即代码的原则。Compose 还将使我们能够方便地启动数据库实例，而无需在我们的开发环境中安装永久性和始终开启的数据库解决方案。

您可以使用 Compose 在云服务上部署应用程序，调整您正在运行的容器实例数量，甚至可以在您的 CI 服务器上运行应用程序的集成测试。在本节后面的内容中，我们将介绍如何在 CircleCI 上运行 Docker Compose。

考虑以下应用程序的架构以及每一层的通信端口：

![](img/B14094_10_03.png)

图 10.3：Lemon Mart 三层架构

使用 Docker Compose，我们能够精确地描述这里显示的架构。您可以在[`docs.docker.com/compose/`](https://docs.docker.com/compose/)了解更多关于 Compose 的信息。

接下来，让我们为 Lemon Mart 实现一个更高效的 Web 服务器。

### 使用 Nginx 作为 Web 服务器

我们的 Web 应用程序已经在*第九章*，*使用 Docker 的 DevOps*中进行了容器化。对于这个练习，我们将使用基于 nginx 的容器。

在`web-app`的根目录下添加一个名为`nginx.Dockerfile`的新 Dockerfile。这个镜像将比我们已有的基于 Node 的镜像小，因为我们使用 nginx 作为 Web 服务器：

```js
**web-app/nginx.Dockerfile**
FROM duluca/minimal-nginx-web-server:1-alpine
COPY dist/lemon-mart /var/www
CMD 'nginx' 
```

现在，让我们将我们的服务器容器化。

### 容器化服务器

到目前为止，我们主要使用预配置的 Docker 镜像来部署我们的 Web 应用程序。以下是基于 Node.js 的服务器更详细实现的示例：

如果你需要，可以参考*第九章*，*使用 Docker 的 DevOps*中的*使用 Docker 容器化应用程序*部分，作为 Docker 的复习。

1.  让我们先定义`Dockerfile`：

    ```js
    **server/Dockerfile**
    FROM node:lts-alpine
    RUN apk add --update --no-progress make python bash
    ENV NPM_CONFIG_LOGLEVEL error
    ADD https://github.com/Yelp/dumb-init/releases/download/v1.2.2/dumb-init_1.2.2_amd64 /usr/local/bin/dumb-init
    RUN chmod +x /usr/local/bin/dumb-init
    RUN mkdir -p /usr/src/app
    RUN chown node: /usr/src/app
    USER node
    WORKDIR /usr/src/app
    COPY package*.json ./
    RUN NODE_ENV=production
    RUN npm install --only=production
    ENV HOST "0.0.0.0"
    ENV PORT 3000
    EXPOSE 3000
    ADD dist dist
    ENTRYPOINT ["dumb-init", "--"]
    CMD ["node", "dist/src/index"] 
    ```

    注意，我们将`dist`文件夹添加到我们的服务器中，然后使用 nodes 运行它。

    你可以通过查看[`github.com/duluca/minimal-node-web-server`](https://github.com/duluca/minimal-node-web-server)上类似配置的`minimal-node-web-server`仓库中的`README.md`来了解更多关于我们服务器容器配置的信息。

    现在，设置跨环境的**npm 脚本用于 Docker**，它适用于 Windows 10 和 macOS 上的我们的服务器。

1.  安装 Docker 任务的 npm 脚本：

    ```js
    $ npm i -g mrm-task-npm-docker 
    ```

1.  应用 Docker 配置的 npm 脚本，确保在`server`文件夹中执行命令：

    ```js
    $ npx mrm npm-docker 
    ```

1.  使用配置参数配置你的`package.json`：

    ```js
    **server/package.json**
      "config": {
        "imageRepo": "duluca/lemon-mart-server",
        "imageName": "lemon-mart-server",
        "imagePort": "3000",
        "internalContainerPort": "3000"
      } 
    ```

在构建 Docker 容器之前，请确保构建你的应用程序。

### 使用 DotEnv 配置环境变量

DotEnv 文件广泛支持，方便地将密钥存储在未提交到代码仓库的`.env`文件中。Docker 和 Compose 原生支持`.env`文件。

首先，让我们了解单一代码库核心的环境变量：

1.  参考项目根目录下的`example.env`文件：

    ```js
    **example.env**
    # Root database admin credentials
    MONGO_INITDB_ROOT_USERNAME=admin
    MONGO_INITDB_ROOT_PASSWORD=anAdminPasswordThatIsNotThis
    # Your application's database connection information. 
    # Corresponds to MONGO_URI on server-example.env
    MONGODB_APPLICATION_DATABASE=lemon-mart
    MONGODB_APPLICATION_USER=john.smith
    MONGODB_APPLICATION_PASS=g00fy
    # Needed for AWS deployments
    AWS_ACCESS_KEY_ID=xxxxxx
    AWS_SECRET_ACCESS_KEY=xxxxxx
    # See server-example.env for server environment variables 
    ```

    不要在`example.env`中存储任何真实密钥。将它们存储在`.env`文件中。`example.env`文件用于记录项目所需的环境变量。在这种情况下，我已经在我的`example.env`文件中填充了示例值，以便读者可以在不配置所有这些参数的情况下运行示例。

1.  通过执行以下命令确保`init-dev-env`已安装在项目根目录中：

    ```js
    $ npm i -D init-dev-env 
    ```

1.  `npm run init:env`脚本使用`init-dev-env`包根据`example.env`文件生成`.env`文件：

    在**lemon-mart-server**中，服务器的`example.env`文件存在于两个地方。首先在项目根目录下作为`server-example.env`，其次在`server/example.env`下。这样做是为了增加示例配置设置的可见性。

    ```js
    $ npx init-dev-env generate-dot-env example.env -f && 
    init-dev-env generate-dot-env server-example.env --source=. --target=server -f 
    ```

1.  第二个`.env`文件是为服务器生成的，如下所示：

    ```js
    **server/.env**
    # MongoDB connection string as defined in example.env
    MONGO_URI=mongodb://john.smith:g00fy@localhost:27017/lemon-mart
    # Secret used to generate a secure JWT
    JWT_SECRET=aSecureStringThatIsNotThis
    # DEMO User Login Credentials
    DEMO_EMAIL=duluca@gmail.com
    DEMO_PASSWORD=l0l1pop!!
    DEMO_USERID=5da01751da27cc462d265913 
    ```

注意，此文件包含连接到 MongoDB 的连接字符串、我们将用于加密 JWT 的密钥以及一个种子用户，以便我们可以登录到应用程序。通常，你不会为你的种子用户配置密码或用户 ID。这些只在这里以支持可重复的演示代码。

现在，我们准备好定义 Compose 的 YAML 文件。

### 定义 Docker-Compose YAML

让我们在单一代码库的根目录中定义一个`docker-compose.yml`文件，以反映我们的架构：

```js
**docker-compose.yml**
version: '3.7'
services:
  web-app:
    container_name: web
    build:
      context: ./web-app
      dockerfile: nginx.Dockerfile
    ports:
      - '8080:80'
    links:
      - server
    depends_on:
      - server
  server:
    container_name: lemon-mart-server
    build: server
    env_file: ./server/.env
    environment:
      - MONGO_URI=mongodb://john.smith:g00fy@lemondb:27017/lemon-mart
    ports:
      - '3000:3000'
    links:
      - database
    depends_on:
      - database
  database:
    container_name: lemondb
    image: duluca/minimal-mongo:4.2.2
    restart: always
    env_file: .env
    ports:
      - '27017:27017'
    volumes:
      - 'dbdata:/data/db'
volumes:
  dbdata: 
```

在顶部，我们使用基于 nginx 的容器构建 `web-app` 服务。`build` 属性会自动为我们构建 `Dockerfile`。我们在端口 `8080` 上公开 `web-app` 并将其链接到 `server` 服务。`links` 属性创建了一个隔离的 Docker 网络，以确保我们的容器可以相互通信。通过使用 `depends_on` 属性，我们确保在启动 `web-app` 之前启动服务器。

`server` 也使用 `build` 属性来自动构建 `Dockerfile`。它还使用 `env_file` 属性从 `server` 文件夹下的 `.env` 文件加载环境变量。使用 `environment` 属性，我们覆盖了 `MONGO_URI` 变量，以便使用数据库容器的内部 Docker 网络名称。服务器既 `links` 也 `depends_on` 数据库，该数据库被命名为 `lemondb`。

`database` 服务从 Docker Hub 拉取 `duluca/minimal-mongo` 镜像。使用 `restart` 属性，我们确保数据库在崩溃时将自动重启。我们使用 `.env` 文件中的设置参数来配置和密码保护数据库。使用 `volumes` 属性，我们将数据库的存储目录挂载到本地目录，以便你的数据可以在容器重启后持续存在。

在云环境中，你可以将你的数据库卷挂载到云提供商的持久化解决方案上，包括 AWS **弹性文件系统**（**EFS**）或 Azure 文件存储。

此外，我们定义了一个名为 `dbdata` 的 Docker 卷用于数据存储。

有时，你的数据库可能无法正常工作。这可能发生在你升级容器、使用不同的容器或在不同项目中使用相同的卷时。在这种情况下，你可以通过执行以下命令来重置你的 Docker 设置的状态：

```js
 $ docker image prune
  $ docker container prune
  $ docker volume prune 
```

或者

```js
 $ docker system prune --volumes **(this will delete everything)** 
```

要运行你的基础设施，你将执行 `docker-compose up` 命令。你也可以使用 `-d` 选项以分离模式运行你的基础设施。你可以使用 `down` 命令停止它，并通过 `rm` 命令删除它创建的容器。

在你能够运行你的基础设施之前，你需要构建你的应用程序，这将在下一节中介绍。

### 编排 Compose 启动

运行 `docker-compose up` 是启动你的基础设施的一种方便简单的方式。然而，在构建容器之前，你需要先构建你的代码。这是一个容易忽视的简单步骤。请参考以下 npm 脚本，你可以使用它们来编排你的基础设施启动：

```js
**package.json**
scripts: {
  "build": "npm run build --prefix ./server && npm run build --prefix ./web-app -- --configuration=lemon-mart-server",
  "test": "npm test --prefix ./server && npm test --prefix ./web-app -- --watch=false",
  "prestart": "npm run build && docker-compose build",
  "start": "docker-compose up",
  "stop": "docker-compose down",
  "clean": "docker-compose rm",
  "clean:all": "docker system prune --volumes",
  "start:backend": "docker-compose -f docker-compose.backend.yml up --build",
  "start:database": "docker-compose -f docker-compose.database.yml up --build", 
```

我们实现了一个`build`脚本，该脚本运行服务器和 web 应用的`build`命令。一个`test`脚本可以执行相同的操作来执行测试。我们还实现了一个`npm start`命令，它可以自动运行`build`命令并运行`compose up`。作为额外的好处，我们还实现了`start:backend`和`start:database`脚本，可以运行不同的`docker-compose`文件来仅启动服务器或数据库。你可以通过删除主`docker-compose.yml`文件中的不必要的部分来创建这些文件。有关示例，请参阅 GitHub 仓库。

在服务器上编码时，我通常执行`npm run start:database`来启动数据库，并在单独的终端窗口中，从`server`文件夹使用`npm start`启动服务器。这样，我可以并排看到两个系统生成的日志。

执行`npm start`以验证你的`docker-compose`配置是否正常工作。按*Ctrl* + *C*停止基础设施。

### 在 CircleCI 上组合

你可以在 CircleCI 上执行你的 Compose 基础设施以验证配置的正确性并运行快速集成测试。请参阅以下更新的工作流程：

```js
**.circleci/config.yml**
workflows:
  version: 2
  build-and-test-compose:
    jobs:
      - build_server
      - build_webapp
      - test_compose:
          requires:
            - build_server
            - build_webapp 
```

我们确保在运行名为`test_compose`的新任务之前，`server`和`web-app`都已构建，该任务检查代码、初始化子模块并复制两个构建的`dist`文件夹，如下所示：

```js
**.circleci/config.yml**
  test_compose:
    docker:
      - image: circleci/node:lts-browsers
    working_directory: ~/repo
    steps:
      - setup_remote_docker
      - attach_workspace:
          at: /tmp/workspace
      - checkout:
          path: ~/repo
      - run: npm run modules:init
      - run:
          name: Copy built server to server/dist folder
          command: cp -avR /tmp/workspace/server/dist/ ./server
      - run:
          name: Copy built web-app to web-app/dist folder
          command: cp -avR /tmp/workspace/dist/ ./web-app
      - run:
          name: Restore .env files
          command: |
            set +H
            echo -e $PROJECT_DOT_ENV > .env
            echo -e $SERVER_DOT_ENV > server/.env
      - run:
          name: Compose up
          command: |
            set -x
            docker-compose up -d
      - run:
          name: Verify web app
          command: |
            set -x
            docker run --network container:web jwilder/dockerize -wait http://localhost:80
            docker run --network container:web appropriate/curl http://localhost:80
      - run:
          name: Verify db login with api
          command: |
            set -x
            docker run --network container:lemon-mart-server jwilder/dockerize -wait http://localhost:3000
            docker run --network container:lemon-mart-server appropriate/curl \
              -H "accept: application/json" -H "Content-Type: application/json" \
              -d "$LOGIN_JSON" http://localhost:3000/v1/auth/login 
```

在复制`dist`文件后，该任务随后放置来自 CircleCI 环境变量的`.env`文件。然后，我们运行`docker-compose up`来启动我们的服务器。接下来，我们通过运行一个`curl`命令来检索其`index.html`文件来测试`web-app`。在等待服务器通过`dockerize -wait`变得可用后，我们运行`curl`。同样，我们通过使用我们的演示用户登录来测试我们的 API 服务器和数据库的集成。

恭喜！现在，你对我们的全栈架构在高级别是如何拼接的有了相当好的理解。在本章的后半部分，我们将介绍 API 是如何实现的，它是如何与数据库集成的，以及我们将看到 JWT 身份验证是如何与 API 和数据库协同工作的。

让我们继续深入探讨 API 设计。

# RESTful API

在全栈开发中，尽早确定 API 设计非常重要。API 设计本身与你的数据合约的外观密切相关。你可以创建 RESTful 端点或使用下一代 GraphQL 技术。在设计你的 API 时，前端和后端开发者应紧密合作以实现共同的设计目标。以下是一些高级目标：

+   最小化客户端和服务器之间传输的数据

+   遵循已建立的设计模式（换句话说，数据分页）

+   设计以减少客户端中的业务逻辑

+   扁平化数据结构

+   不要暴露数据库键或关系

+   从一开始就对端点进行版本控制

+   围绕主要数据实体进行设计

你应该旨在在你的 RESTful API 中实现业务逻辑。理想情况下，你的前端不应该包含比展示逻辑更多的内容。任何由前端实现的 `if` 语句也应该在你的后端得到验证。

如同在 *第一章*，*Angular 简介及其概念* 中讨论的那样，在后台和前端实现无状态设计至关重要。每个请求都应使用非阻塞 I/O 方法，并且不应依赖于任何现有会话。这是使用云托管提供商无限扩展你的 Web 应用程序的关键。

无论你在实施项目时，都应限制，如果可能的话，消除实验。这在全栈项目中尤其如此。一旦你的应用程序上线，API 设计中的失误的下游影响可能是深远的，并且无法纠正。

接下来，让我们看看如何围绕主要数据实体设计 API。在这种情况下，我们将回顾围绕用户（包括身份验证）的 API 实现。首先，我们将探索如何使用 Swagger 定义一个端点，这样我们就可以具体地向团队成员传达我们设计的意图。

记住，本章只涵盖了概念上重要的代码片段。虽然你可以选择从头开始实现这段代码，但理解其工作原理并不需要这样做。如果你选择从头开始实现，请参考 [`github.com/duluca/lemon-mart-server`](https://github.com/duluca/lemon-mart-server) 上的完整源代码，以跟进并填补你实现中的空白。

在以后，Swagger 将成为文档工具，反映我们 API 的能力。

## 使用 Swagger 进行 API 设计

Swagger 将允许你设计和记录你的 Web API。对于团队来说，它可以作为前端和后端开发者之间出色的沟通工具，从而减少很多摩擦。此外，尽早定义你的 API 表面，可以让实现开始而不必担心后期集成挑战。

随着我们继续前进，我们将实现一个用户 API，以展示 Swagger 的工作原理。

我强烈推荐安装 Swagger Viewer VS Code 扩展，它允许我们在不运行任何额外工具的情况下预览 YAML 文件。

让我们从探索单一代码库根目录下的 `swagger.yaml` 文件开始：

1.  在 VS Code 中打开 `swagger.yaml`。

1.  安装名为 Swagger Preview 的 VS Code 扩展。

1.  按 *Ctrl* + *Shift* + *P*，或者点击 ![](img/B14094_10_15.png)+![](img/B14094_10_16.png)+`P`，以打开命令面板并运行 **预览 Swagger**。

1.  看看预览，如图所示：![](img/B14094_10_04.png)

    图 10.4：Swagger.yaml 预览

使用 Swagger UI 视图，你将能够尝试命令并在你的服务器环境中执行它们。

### 定义 Swagger YAML 文件

我们将使用 Swagger 规范版本 `openapi: 3.0.1`，它实现了 OpenAPI 标准。让我们在这里回顾 `swagger.yaml` 文件的主要组件：

有关 Swagger 文件定义的更多信息，请参阅[`swagger.io/specification/`](https://swagger.io/specification/)。

1.  YAML 文件以一般信息和目标服务器开始：

    ```js
    **swagger.yaml**
    openapi: 3.0.1
    **info**:
      title: LemonMart
      description: LemonMart API
      version: "2.0.0"
    **servers**:
      - url: http://localhost:3000
        description: Local environment
      - url: https://mystagingserver.com
        description: Staging environment
      - url: https://myprodserver.com
        description: Production environment 
    ```

1.  在`components`下，我们定义常见的`securitySchemes`和响应，这些定义了我们打算实施的认证方案以及我们的错误消息响应的形状：

    ```js
    **swagger.yaml**
    ...
    **components:**
     **securitySchemes:**
        bearerAuth:
          type: http
          scheme: bearer
          bearerFormat: JWT
      **responses:**
        UnauthorizedError:
          description: Unauthorized
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/ServerMessage"
              type: string 
    ```

    注意到`$ref`的使用，以重复使用重复元素。您可以看到在这里定义了`ServerMessage`。

1.  在`components`下，我们定义共享数据`schemas`，它声明了要么作为输入接收要么返回给客户端的数据实体：

    ```js
    **swagger.yaml**
    ...
     **schemas:**
        ServerMessage:
          type: object
          properties:
            message:
              type: string
        Role:
          type: string
          enum: [none, clerk, cashier, manager]
        ... 
    ```

1.  在`components`下，我们添加共享`parameters`，这使得重用常见的模式，如分页端点变得容易：

    ```js
    **swagger.yaml**
    ...
      **parameters:**
        filterParam:
          in: query
          name: filter
          required: false
          schema:
            type: string
          description: Search text to filter the result set by
    ... 
    ```

1.  在`paths`下，我们开始定义 REST 端点，例如`/login`路径的`post`端点：

    ```js
    **swagger.yaml**
    ...
    **paths:**
      /v1/login:
        post:
          description: |
            Generates a JWT, given correct credentials.
          requestBody:
            required: true
            content:
              application/json:
                schema:
                  type: object
                  properties:
                    email:
                      type: string
                    password:
                      type: string
                  required:
                    - email
                    - password
          responses:
            '200': # Response
              description: OK
              content:
                application/json:
                  schema:
                    type: object
                    properties:
                      accessToken:
                        type: string
                    description: JWT token that contains userId as subject, email and role as data payload.
            '401':
              $ref: '#/components/responses/UnauthorizedError' 
    ```

    注意，`requestBody`定义了类型为`string`的必需输入变量。在`responses`下，我们可以定义对请求成功返回的`200`响应和失败返回的`401`响应的外观。在前者的情况下，我们返回一个`accessToken`，而在后者的情况下，我们返回一个`UnauthorizedError`，如第二步中定义的那样。

1.  在`paths`下，我们继续添加以下路径：

    ```js
    **swagger.yaml**
    ...
    **paths:**
      /v1/auth/me:
      get: ...
     /v2/users:
        get: ...
        post: ...
     /v2/users/{id}:
        get: ...
        put: ... 
    ```

OpenAPI 规范功能强大，允许您定义复杂的用户如何与您的 API 交互的要求。在[`swagger.io/docs/specification`](https://swagger.io/docs/specification)上的规范文档在开发您自己的 API 定义时是一个无价资源。

### 预览 Swagger 文件

您可以免费在[`swaggerhub.com`](https://swaggerhub.com)验证您的 Swagger 文件。在您注册免费账户后，创建一个新的项目并定义您的 YAML 文件。SwaggerHub 将突出显示您所犯的错误。它还会为您提供网页预览，这与您在 Swagger Preview VS Code 扩展中获得的预览相同。

参考以下截图，查看有效的 Swagger YAML 定义在 SwaggerHub 上的样子：

![图片](img/B14094_10_05.png)

图 10.5：SwaggerHub 上的有效 Swagger YAML 定义

我们的目的是将这种交互式文档与我们的 Express.js API 集成。

现在，让我们看看您如何实现这样的 API。

## 使用 Express.js 实现 API

在我们开始实现我们的 API 之前，让我们分节回顾我们后端的目标文件结构，以便我们了解服务器是如何启动的，API 端点的路由是如何配置的，公共资源是如何提供的，以及服务是如何配置的。Minimal MEAN 故意坚持基本原理，这样您就可以更多地了解底层技术。虽然我已经使用 Minimal MEAN 实现了生产系统，但您可能不会像我一样享受这种骨架式开发体验。在这种情况下，您可以考虑 Nest.js，这是一个用于实现全栈 Node.js 应用程序的热门框架。Nest.js 具有丰富的功能集，其架构和编码风格与 Angular 非常相似。我建议在您掌握了 MEAN 栈的基础之后使用此类库。

向 Kamil Mysliwiec 和 Mark Pieszak 表示祝贺，他们创建了一个出色的工具，并在 Nest.js 周围营造了一个充满活力的社区。您可以在 [`nestjs.com/`](https://nestjs.com/) 上了解更多关于 Nest.js 的信息，并在 [`trilon.io/`](https://trilon.io/) 获取咨询服务。

现在，让我们回顾一下我们的 Express 服务器的文件结构：

```js
**server/src**
│   api.ts
│   app.ts
│   config.ts
│   docs-config.ts
│   index.ts
│   
├───models
│       enums.ts
│       phone.ts
│       user.ts
│       
├───public
│       favicon.ico
│       index.html
│       
├───services
│       authService.ts
│       userService.ts
│       
├───v1
│   │   index.ts
│   │   
│   └───routes
│           authRouter.ts
│           
└───v2
    │   index.ts
    │   
    └───routes
            userRouter.ts 
```

通过查看组件图，我们可以回顾这些文件的目的和它们之间的交互，从而获得架构和依赖树的概览：

![](img/B14094_10_06.png)

图 10.6：Express 服务器架构

`index.ts` 包含一个 `start` 函数，该函数利用三个主要助手启动应用程序：

1.  `config.ts`：管理环境变量和设置。

1.  `app.ts`：配置 Express.js，定义所有 API 路径，然后路由实现路径并利用包含业务逻辑的服务。服务使用模型，如 `user.ts`，来访问数据库。

1.  `document-ts`：建立与数据库的连接并对其进行配置，并在启动时利用 `user.ts` 配置种子用户。

您可以看到，图中顶部的组件负责启动和配置任务，包括配置 API 路径，这代表了 **API** 层。**服务**层应该包含应用程序的大部分业务逻辑，而持久性则在 **模型** 层处理。

参考以下 `index.ts` 的实现，其中不包含任何数据库功能：

```js
**server/src/index.ts**
import * as http from 'http'
import app from './app'
import * as config from './config'
export let Instance: http.Server
async function start() {
  console.log('Starting server: ')
  console.log(`isProd: ${config.IsProd}`)
  console.log(`port: ${config.Port}`)
  Instance = http.createServer(app)
  Instance.listen(config.Port, async () => {
    console.log(`Server listening on port ${config.Port}...`)
  })
}
start() 
```

注意，显示的最后一行代码 `start()` 是触发服务器初始化的函数调用。

现在，让我们看看 Express 服务器是如何设置的。

### 服务器启动

`App.ts` 配置 Express.js，同时提供静态资源服务、路由和版本控制。Express.js 通过中间件函数与库或您自己的代码集成，例如一个认证方法：

```js
**server/src/app.ts**
import * as path from 'path'
import * as cors from 'cors'
import * as express from 'express'
import * as logger from 'morgan'
import api from './api'
const app = express()
app.use(cors())
app.use(express.json())
app.use(express.urlencoded({ extended: true }))
app.use(logger('dev'))
app.use('/', express.static(path.join(__dirname, '../public'), { redirect: false }))
app.use(api)
export default app 
```

在前面的代码中，请注意配置 Express 使用 `use()` 方法非常简单。首先，我们配置 `cors`，然后是 express 解析器和 `logger`。

接下来，使用`express.static`函数，我们在根路由`/`上提供`public`文件夹，这样我们就可以显示有关我们服务器的一些有用信息，如下所示：

![](img/B14094_10_07.png)

图 10.7：LemonMart 服务器登录页面

我们将在下一节中介绍如何配置上面提到的`/api-docs`端点。

最后，我们配置路由器，它在`api.ts`中定义。

### 路由和版本控制

`Api.ts`配置了 Express 路由器。请参考以下实现：

```js
**server/src/api.ts**
import { Router } from 'express'
import api_v1 from './v1'
import api_v2 from './v2'
const api = Router()
// Configure all routes here
api.use('/v1', api_v1)
api.use('/v2', api_v2)
export default api 
```

在这种情况下，我们有`v1`和`v2`的两个子路由。始终对您实现的 API 进行版本控制是至关重要的。一旦 API 公开，简单地逐步淘汰 API 以适应新版本可能会非常棘手，有时甚至不可能。即使是微小的代码更改或 API 的细微差异都可能导致客户端崩溃。您必须仔细注意，只为您的 API 做出向后兼容的更改。

在某个时候，您可能需要完全重写端点以满足新的需求、性能和业务需求，此时您可以简单地实现端点的`v2`版本，同时保持`v1`实现不变。这允许您以您需要的速度进行创新，同时保持您的应用程序的旧版消费者功能。

简而言之，您应该为创建的每个 API 进行版本控制。通过这样做，您迫使您的消费者对您的 API 的 HTTP 调用进行版本控制。随着时间的推移，您可以在不同的版本下过渡、复制和淘汰 API。消费者然后可以选择调用对他们有用的 API 版本。

配置路由非常简单。让我们看看`v2`的配置，如下所示：

```js
**server/src/v2/index.ts**
import { Router } from 'express'
import userRouter from './routes/userRouter'
const router = Router()
// Configure all v2 routers here
router.use('/users?', userRouter)
export default router 
```

`/users?`结尾的问号意味着`/user`和`/users`都将针对`userRouter`中实现的操作工作。这是一种避免拼写错误的好方法，同时允许开发者选择对操作有意义的复数形式。

在`userRouter`中，您可以实现 GET、POST、PUT 和 DELETE 操作。请参考以下实现：

```js
**server/src/v2/routes/userRouter.ts**
const router = Router()
router.get('/', async (req: Request, res: Response) => {
})
router.post('/', async (req: Request, res: Response) => {
})
router.get('/:userId', async (req: Request, res: Response) => {
})
router.put('/:userId', async (req: Request, res: Response) => {
})
export default router 
```

在前面的代码中，您可以观察到路由参数的使用。您可以通过请求对象，如`req.params.userId`，来消费路由参数。

注意，示例代码中的所有路由都被标记为`async`，因为它们都将进行数据库调用，我们将使用`await`。如果您的路由是同步的，那么您不需要`async`关键字。

接下来，让我们看看服务。

### 服务

我们不希望在表示我们的 API 层的路由文件中实现业务逻辑。API 层应该主要包含转换数据和调用业务逻辑层。

您可以使用 Node.js 和 TypeScript 功能来实现服务。不需要复杂的依赖注入。示例应用程序实现了两个服务 - `authService`和`userService`。

例如，在`userService.ts`中，您可以实现一个名为`createNewUser`的函数：

```js
**server/src/services/userService.ts**
import { IUser, User } from '../models/user'
export async function createNewUser(userData: IUser): Promise<User | boolean> {
  // create user
} 
```

`createNewUser` 接受 `userData`，其形状为 `IUser`，当它完成用户创建后，返回一个 `User` 实例。然后我们可以将此函数用于我们的路由器，如下所示：

```js
**server/src/v2/routes/userRouter.ts**
import { createNewUser } from '../../services/userService'
router.post('/', async (req: Request, res: Response) => {
  const userData = req.body as IUser
  const success = await createNewUser(userData)
  if (success instanceof User) {
    res.send(success)
  } else {
    res.status(400).send({ message: 'Failed to create user.' })
  }
}) 
```

我们可以等待 `createNewUser` 的结果，如果成功，将创建的对象作为对 POST 请求的响应返回。

注意，尽管我们将 `req.body` 转换为 `IUser` 类型，但这仅是一个开发时的便利功能。在运行时，消费者可以向主体传递任意数量的属性。粗心处理请求参数是您的代码可能被恶意利用的主要方式之一。

现在我们已经了解了 Express 服务器的骨架结构，让我们看看如何配置 Swagger，以便您可以用它作为实现的指南并为您 API 创建活页文档。

## 配置 Swagger 与 Express

配置 Swagger 与 Express 是一个手动过程。强迫自己手动记录端点有一个很好的副作用。通过放慢速度，您将有机会从消费者和实现者的角度考虑您的实现。这种视角将帮助您在开发过程中解决端点可能存在的潜在问题，从而避免昂贵的返工。

将 Swagger 集成到您的服务器中的主要好处是，您将获得本章前面提到的相同的交互式 Swagger UI，因此您的测试人员和开发者可以直接从网络界面发现或测试您的 API。

我们将使用两个辅助库来帮助我们集成 Swagger 到我们的服务器中：

+   `swagger-jsdoc`：它允许您通过在 `JSDoc` 注释块中使用 `@swagger` 标识符在相关代码上实现 OpenAPI 规范，生成 `swagger.json` 文件作为输出。

+   `swagger-ui-express`：它消费 `swagger.json` 文件以显示交互式 Swagger UI 网络界面。

让我们来看看 Swagger 如何配置与 Express.js 一起工作：

1.  TypeScript 的依赖项和类型信息如下所示：

    ```js
    $ npm i swagger-jsdoc swagger-ui-express
    $ npm i -D @types/swagger-jsdoc @types/swagger-ui-express 
    ```

1.  让我们来看看 `docs-config.ts` 文件，它配置了基本的 OpenAPI 定义：

    ```js
    **server/src/docs-config.ts**
    import * as swaggerJsdoc from 'swagger-jsdoc'
    import { Options } from 'swagger-jsdoc'
    import * as packageJson from '../package.json'
    const options: Options = {
      swaggerDefinition: {
        openapi: '3.0.1',
        components: {},
        info: {
          title: packageJson.name,
          version: packageJson.version,
          description: packageJson.description,
        },
        servers: [
          {
            url: 'http://localhost:3000',
            description: 'Local environment',
          },
          {
            url: 'https://mystagingserver.com',
            description: 'Staging environment',
          },
          {
            url: 'https://myprodserver.com',
            description: 'Production environment',
          },
        ],
      },
      apis: [
        '**/models/*.js', 
        '**/v1/routes/*.js', 
        '**/v2/routes/*. js'
      ],
    }
    export const specs = swaggerJsdoc(options) 
    ```

    修改 `servers` 属性以包含您的测试、预发布或生产环境的位置。这允许您的 API 消费者使用网络界面测试 API，而无需额外的工具。请注意，`apis` 属性通知 `swaggerJsdoc` 在构建 `swagger.json` 文件时应解析的代码文件。此过程在服务器启动时运行，这就是为什么我们引用了转译的 `.js` 文件而不是 `.ts` 文件。

1.  在 `app.ts` 中启动 Swagger 配置：

    ```js
    **server/src/app.ts**
    import * as swaggerUi from 'swagger-ui-express'
    import { specs } from './docs-config'
    const app = express()
    app.use(cors())
    ...
    **app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(specs))**
    ...
    export default app 
    ```

规范包含 `swagger.json` 文件的内容，然后传递给 `swaggerUi`。然后，使用服务器中间件，我们可以配置 `swaggerUi` 在 `/api-docs` 上托管网络界面。

您已经拥有了从本章开始就需要用于完成应用程序实现的 OpenAPI 定义。有关更多信息，请参考完整的源代码 [`github.com/duluca/lemon-mart-server`](https://github.com/duluca/lemon-mart-server)。

恭喜！现在您已经很好地理解了我们的 Express 服务器是如何工作的。接下来，让我们看看如何连接到 MongoDB。

# MongoDB ODM 与 DocumentTS

DocumentTS 作为 ODM，通过实现模型层来启用与数据库对象的丰富和可定制的交互。ODM 是文档数据库中与关系数据库中的 **对象关系映射器**（**ORM**）相对应的。想想 Hibernate 或 Entity Framework。如果您不熟悉这些概念，我建议在继续之前进行进一步的研究。

在其核心，DocumentTS 利用 MongoDB 的 Node.js 驱动程序。该驱动程序由 MongoDB 的制作者实现。它保证提供最佳性能并与新 MongoDB 版本保持功能一致性，而第三方库通常在支持新功能方面落后。使用 `database.getDbInstance` 方法，您可以直接访问原生驱动程序。否则，您将通过您实现的模型访问 Mongo。请参考以下图表以获取概述：

![](img/B14094_10_08.png)

图 10.8：DocumentTS 概述

您可以在 [`mongodb.github.io/node-mongodb-native/`](https://mongodb.github.io/node-mongodb-native/) 上了解更多关于 MongoDB Node.js 驱动程序的信息。

## 关于 DocumentTS

DocumentTS 提供了三个主要功能：

+   `connect()`：一个 MongoDB 异步连接工具

+   `Document` 和 `IDocument`：一个基类和接口，帮助您定义自己的模型

+   `CollectionFactory`：定义集合，组织索引，并在集合实现中聚合查询

以下是 DocumentTS 集合提供的便利功能：

+   `get collection` 返回原生 MongoDB 集合，因此您可以直接操作它：

    ```js
    get collection(): ICollectionProvider<TDocument> 
    ```

+   `aggregate` 允许您运行 MongoDB 聚合管道：

    ```js
    aggregate(pipeline: object[]): AggregationCursor<TDocument> 
    ```

+   `findOne` 和 `findOneAndUpdate` 简化了常用数据库功能的操作，自动填充返回的模型：

    ```js
    async findOne(
      filter: FilterQuery<TDocument>, 
      options?: FindOneOptions
    ): Promise<TDocument | null> 
    async findOneAndUpdate(
      filter: FilterQuery<TDocument>,
      update: TDocument | UpdateQuery<TDocument>,
      options?: FindOneAndReplaceOption
     ): Promise<TDocument | null> 
    ```

+   `findWithPagination` 是 DocumentTS 中迄今为止最好的功能，允许您过滤、排序和分页大量数据。此功能旨在与数据表一起使用，因此您指定可搜索属性，关闭填充，并使用调试功能来微调您的查询：

    ```js
    async findWithPagination<TReturnType extends IDbRecord>(
      queryParams: Partial<IQueryParameters> & object,
      aggregationCursorFunc?: Func<AggregationCursor<TReturnType>>,
      query?: string | object,
      searchableProperties?: string[],
      hydrate = true,
      debugQuery = false
    ): Promise<IPaginationResult<TReturnType>> 
    ```

DocumentTS 致力于成为可靠、可选且易于使用的工具。DocumentTS 直接将开发者暴露于原生 Node.js 驱动程序，因此您学习如何与 MongoDB 交互，而不是使用某个库。开发者可以选择利用库的便利功能，包括以下内容：

+   通过简单的接口定义您自己的模型。

+   选择您想要自动填充的字段，例如子对象或相关对象。

+   每次请求时序列化计算字段。

+   保护某些字段（如密码）免于序列化，以防止它们意外地通过网络发送。

由于 DocumentTS 是可选的，因此开发人员可以按自己的时间表过渡到新功能。如果性能成为关注点，您可以轻松切换到原生 MongoDB 调用以获得最佳性能。使用 DocumentTS，您将花费更多时间阅读 MongoDB 文档，而不是 DocumentTS 文档。

Mongoose 是一个用于与 MongoDB 交互的流行库。然而，它是一个围绕 MongoDB 的包装器，需要全面采用。此外，该库抽象了原生驱动程序，因此它对生态系统中的更改和更新非常敏感。您可以在[`mongoosejs.com/`](https://mongoosejs.com/)上了解更多关于 Mongoose 的信息。

使用以下命令安装 MongoDB 依赖项和 TypeScript 类型信息：

```js
$ npm i mongodb document-ts
$ npm i -D @types/mongodb 
```

接下来，让我们看看如何连接到数据库。

## 连接到数据库

在编写完全异步的 Web 应用程序时，确保数据库连接存在可能是一个挑战。`connect()`函数使得连接到 MongoDB 实例变得简单，并且可以从多个同时启动的线程中安全地调用。

让我们先配置一下环境变量：

1.  记住`MONGO_URI`连接字符串位于`server/.env`中：

    ```js
    **server/.env**
    MONGO_URI=mongodb://john.smith:g00fy@localhost:27017/lemon-mart 
    ```

    为了更新用户名、密码和数据库名称，您需要编辑顶级`.env`文件中的以下变量：

    ```js
    **.env**
    MONGODB_APPLICATION_DATABASE=lemon-mart
    MONGODB_APPLICATION_USER=john.smith
    MONGODB_APPLICATION_PASS=g00fy 
    ```

    记住，`.env`更改只有在您重新启动服务器后才会生效。

1.  让我们看看`document-ts`如何与`index.ts`集成：

    ```js
    **server/src/index.ts**
    ...
    import * as document from 'document-ts'
    import { UserCollection } from './models/user'
    ...
    async function start() {
      ...
      console.log(`mongoUri: ${config.MongoUri}`)
      try {
        **await document.connect(config.MongoUri, config.IsProd)**
        console.log('Connected to database!')
      } catch (ex) {
        console.log(`Couldn't connect to a database: ${ex}`)
      }
    ...
      Instance.listen(config.Port, async () => {
        console.log(`Server listening on port ${config.Port}...`)
        **await createIndexes()**
        console.log('Done.')
      })
    }
    async function createIndexes() {
      console.log('Create indexes...')
      **await UserCollection.createIndexes()**
    }
    start() 
    ```

我们尝试使用`try/catch`块连接到数据库。一旦 Express 服务器启动并运行，我们调用`createIndexes`，它反过来调用`UserCollection`上具有相同名称的函数。除了性能考虑之外，MongoDB 索引对于使字段可搜索是必要的。

## 具有 IDocument 的模型

您可以实施一个类似于 LemonMart 中的`IUser`接口。然而，这个接口将扩展 DocumentTS 中定义的`IDocument`：

1.  这是`IUser`接口：

    ```js
    **server/src/models/user.ts**
    export interface IUser extends IDocument {
      email: string
      name: IName
      picture: string
      role: Role
      userStatus: boolean
      dateOfBirth: Date
      level: number
      address: {
        line1: string
        line2?: string
        city: string
        state: string
        zip: string
      }
      phones?: IPhone[]
    } 
    ```

    DocumentTS 提供的接口和基类旨在帮助您以一致的方式开发业务逻辑和数据库查询。我鼓励您通过`Ctrl` + 点击它们来探索基类和接口，以便您可以看到它们背后的源代码。

1.  现在，这里是一个扩展`Document<T>`并实现 Swagger 文档的`User`类：

    ```js
    **server/src/models/user.ts**
    import { v4 as uuid } from 'uuid'
    /**
     * @swagger
     * components:
     *   schemas:
     *     Name:
     *       type: object
     *       …
     *     User:
     *       type: object 
     *       …
     */
    export class User extends Document<IUser> implements IUser {
      static collectionName = 'users'
      private password: string
      public email: string
      public name: IName
      public picture: string
      public role: Role
      public dateOfBirth: Date
      public userStatus: boolean
      public level: number
      public address: {
        line1: string
        city: string
        state: string
        zip: string
      }
      public phones?: IPhone[]
      constructor(user?: Partial<IUser>) {
        super(User.collectionName, user)
      }
      fillData(data?: Partial<IUser>) {
        if (data) {
          Object.assign(this, data)
        }
        if (this.phones) {
          this.phones = this.hydrateInterfaceArray(
            Phone, Phone.Build, this.phones
          )
        }
      }
      getCalculatedPropertiesToInclude(): string[] {
        return ['fullName']
      }
      getPropertiesToExclude(): string[] {
        return ['password']
      }
      public get fullName(): string {
        if (this.name.middle) {
          return `${this.name.first} ${this.name.middle} ${this.name.last}`
        }
        return `${this.name.first} ${this.name.last}`
      }
      async create(id?: string, password?: string, upsert = false) {
        if (id) {
          this._id = new ObjectID(id)
        }
        if (!password) {
          password = uuid()
        }
        this.password = await this.setPassword(password)
        await this.save({ upsert })
      }
      hasSameId(id: ObjectID): boolean {
        return this._id.toHexString() === id.toHexString()
      }
    } 
    ```

    注意属性`getCalculatedPropertiesToInclude`和`getPropertiesToExclude`。这些属性定义了字段是否应该由客户端序列化或允许写入数据库。

    数据的序列化和反序列化是将数据转换为可以存储或传输的格式的概念。请参阅*进一步阅读*部分，以获取有关序列化和 JSON 数据格式的文章链接。

    `fullName`是一个计算属性，因此我们不希望将此值写入数据库。然而，`fullName`对客户端很有用。另一方面，`password`属性永远不应该传回客户端，但显然我们需要能够将其保存到数据库中，以便进行密码比较和更改。在保存时，我们传递一个`{ upsert }`对象来指示数据库即使在提供部分信息的情况下也要更新记录。

    记得提供完整的 Swagger 定义。

1.  最后，让我们回顾一下实现`CollectionFactory<T>`的`UserCollectionFactory`：

    ```js
    **server/src/models/user.ts**
    class UserCollectionFactory extends CollectionFactory<User> {
      constructor(docType: typeof User) {
        super(User.collectionName, docType, ['name.first', 'name.last', 'email'])
      }
      async createIndexes() {
        await this.collection().createIndexes([
          {
            key: {
              email: 1,
            },
            unique: true,
          },
          {
            key: {
              'name.first': 'text',
              'name.last': 'text',
              email: 'text',
            },
            weights: {
              'name.last': 4,
              'name.first': 2,
              email: 1,
            },
            name: 'TextIndex',
          },
        ])
      }
    userSearchQuery(
        searchText: string
      ): AggregationCursor<{ _id: ObjectID; email: string }> {
        const aggregateQuery = [
          {
            $match: {
              $text: { $search: searchText },
            },
          },
          {
            $project: {
              email: 1,
            },
          },
        ]
        if (searchText === undefined || searchText === '') {
          delete (aggregateQuery[0] as any).$match.$text
        }
        return this.collection().aggregate(aggregateQuery)
      }
    }
    export let UserCollection = new UserCollectionFactory(User) 
    ```

在这里，我们创建一个唯一索引，这样具有相同电子邮件地址的另一个用户将无法注册。我们还创建了一个加权索引，这有助于编写过滤查询。我们在连接到数据库后立即在`index.ts`中应用这些索引。

`userSearchQuery`是一个有点牵强的例子，用于演示 MongoDB 中的聚合查询。使用 MongoDB 的聚合功能可以执行更复杂和高效的查询。你可以在[`docs.mongodb.com/manual/aggregation`](https://docs.mongodb.com/manual/aggregation)上了解更多关于聚合的信息。

在文件底部，我们实例化一个`UserCollection`并将其导出，以便在应用程序的任何地方引用：

```js
**server/src/models/user.ts**
**export** let UserCollection = new UserCollectionFactory(User) 
```

注意，`UserCollectionFactory`没有被导出，因为它只在`user.ts`文件中需要。

让我们看看如何使用新的用户模型来获取数据。

# 实现 JWT 身份验证

在第八章“设计身份验证和授权”中，我们讨论了如何实现基于 JWT 的身份验证机制。在 LemonMart 中，你实现了一个基础认证服务，它可以扩展为自定义认证服务。

我们将利用三个包来实现我们的功能：

+   `jsonwebtoken`：用于创建和编码 JWT

+   `bcryptjs`：用于在将用户的密码保存到数据库之前对其进行散列和加盐，所以我们永远不会以明文形式存储用户的密码

+   `uuid`：生成一个全局唯一标识符，当需要将用户的密码重置为随机值时很有用

哈希函数是一个一致可重复的单向加密方法，这意味着每次提供相同的输入时都会得到相同的输出，即使你有访问散列值的能力，也无法轻易地找出它存储的信息。然而，我们可以通过散列用户的输入并将其与存储的密码散列值进行比较，来比较用户是否输入了正确的密码。

1.  让我们看看 JWT 身份验证相关的依赖和 TypeScript 类型信息：

    ```js
    $ npm i bcryptjs jsonwebtoken uuid
    $ npm i -D @types/bcryptjs @types/jsonwebtoken @types/uuid 
    ```

1.  观察具有密码散列功能的`User`模型：

    ```js
    **server/src/models/user.ts**
    import * as bcrypt from 'bcryptjs'
      async create(id?: string, password?: string, upsert = false) {
          ...
          this.password = await this.setPassword(password)
          await this.save({ upsert })
        }
      async resetPassword(newPassword: string) {
        this.password = await this.setPassword(newPassword)
        await this.save()
      }
      private setPassword(newPassword: string): Promise<string> {
        return new Promise<string>((resolve, reject) => {
          bcrypt.genSalt(10, (err, salt) => {
            if (err) {
              return reject(err)
            }
            bcrypt.hash(newPassword, salt, (hashError, hash) => {
              if (hashError) {
                return reject(hashError)
              }
              resolve(hash)
            })
          })
        })
      }
      comparePassword(password: string): Promise<boolean> {
        const user = this
        return new Promise((resolve, reject) => {
          bcrypt.compare(password, user.password, (err, isMatch) => {
            if (err) {
              return reject(err)
            }
            resolve(isMatch)
          })
        })
      } 
    ```

使用 `setPassword` 方法，你可以对用户提供的密码进行散列，并将其安全地保存到数据库中。稍后，我们将使用 `comparePassword` 函数将用户提供的值与散列密码进行比较。我们从不存储用户提供的值，因此系统永远不会重新生成用户的密码，这使得它是一个安全的实现。

## 登录 API

以下是在 `lemon-mart-server` 的 `authService` 中 `login` 方法的实现：

```js
**server/src/services/authService.ts**
import * as jwt from 'jsonwebtoken'
import { JwtSecret } from '../config'
export const IncorrectEmailPasswordMessage = 'Incorrect email and/or password'
export const AuthenticationRequiredMessage = 'Request has not been authenticated'
export function createJwt(user: IUser): Promise<string> {
  return new Promise<string>((resolve, reject) => {
    const payload = {
      email: user.email,
      role: user.role,
      picture: user.picture,
    }
    jwt.sign(
      payload,
      JwtSecret(),
      {
        subject: user._id.toHexString(),
        expiresIn: '1d',
      },
      (err: Error, encoded: string) => {
        if (err) {
          reject(err.message)
        }
        resolve(encoded)
      }
    )
  })
} 
```

以下代码示例实现了一个 `createJwt` 函数来为每个用户创建 JWT。我们还为身份验证失败定义了预定义的响应。注意不正确的电子邮件/密码消息的模糊性，这意味着恶意行为者不能利用系统来利用身份验证系统。

让我们在 `/v1/auth/login` 上实现登录 API：

```js
**server/src/v1/routes/authRouter.ts**
import { Request, Response, Router } from 'express'
import { UserCollection } from '../../models/user'
import {
  AuthenticationRequiredMessage,
  IncorrectEmailPasswordMessage,
  authenticate,
  createJwt,
} from '../../services/authService'
const router = Router()
/**
 * @swagger
 * /v1/auth/login:
 *   post:
 * …
 */
router.post('/login', async (req: Request, res: Response) => {
  const userEmail = req.body.email?.toLowerCase()
  const user = await UserCollection.findOne({ email: userEmail })
  if (user && (await user.comparePassword(req.body.password))) {
    return res.send({ accessToken: await createJwt(user) })
  }
  return res.status(401).send({
    message: IncorrectEmailPasswordMessage
  })
}) 
```

注意，当通过电子邮件检索用户时，请记住电子邮件是不区分大小写的。因此，你应该始终将输入转换为小写。你可以通过验证电子邮件、删除任何空白字符、脚本标签或甚至恶意 Unicode 字符来进一步改进此实现。考虑使用 `express-validator` 或 `express-sanitizer` 等库。

`login` 方法利用 `user.comparePassword` 函数来确认提供的密码的正确性。然后 `createJwt` 函数创建要返回给客户端的 `accessToken`。

## 身份验证中间件

`authenticate` 函数是一个中间件，我们可以在我们的 API 实现中使用它来确保只有经过身份验证且具有适当权限的用户才能访问端点。请记住，真正的安全性是在你的后端实现中实现的，而这个 `authenticate` 函数是你的守门人。

`authenticate` 接收一个可选的 `options` 对象，用于使用 `requiredRole` 属性验证当前用户的角色，因此如果 API 配置如下所示，则只有经理可以访问该 API：

```js
authenticate(**{ requiredRole: Role.Manager }**) 
```

在某些情况下，我们希望用户能够更新自己的记录，同时也允许经理更新其他人的记录。在这种情况下，我们利用 `permitIfSelf` 属性，如下所示：

```js
authenticate({
    requiredRole: Role.Manager,
    **permitIfSelf: {**
 **idGetter: (req: Request) => req.body._id,**
 **requiredRoleCanOverride: true,**
 **},**
  }), 
```

在这种情况下，如果正在更新的记录的 `_id` 与当前用户的有效 `_id` 匹配，则用户可以更新自己的记录。由于 `requiredRoleCanOverride` 设置为 `true`，经理可以更新任何记录。如果设置为 `false`，则不允许这样做。通过混合和匹配这些属性，你可以覆盖你大部分的守门人需求。

注意，`idGetter` 是一个函数委托，这样你就可以指定在 `authenticate` 中间件执行时如何访问 `_id` 属性。

请参阅以下 `authenticate` 和 `authenticateHelper` 的实现：

```js
**server/src/services/authService.ts**
import { NextFunction, Request, Response } from 'express'
import { ObjectID } from 'mongodb'
import { IUser, UserCollection } from '../models/user'
interface IJwtPayload {
  email: string
  role: string
  picture: string
  iat: number
  exp: number
  sub: string
}
export function authenticate(options?: {
  requiredRole?: Role
  permitIfSelf?: {
    idGetter: (req: Request) => string
    requiredRoleCanOverride: boolean
  }
}) {
  return async (req: Request, res: Response, next: NextFunction) => {
    try {
      res.locals.currentUser =
        await authenticateHelper(
          req.headers.authorization, {
            requiredRole: options?.requiredRole,
            permitIfSelf: options?.permitIfSelf
              ? {
                  id: options?.permitIfSelf.idGetter(req),
                  requiredRoleCanOverride: 
                    options?.permitIfSelf.requiredRoleCanOverride,
                }
             : undefined,
          }
        )
      return next()
    } catch (ex) {
      return res.status(401).send({ message: ex.message })
    }
  }
}
export async function authenticateHelper(
  authorizationHeader?: string,
  options?: {
    requiredRole?: Role
    permitIfSelf?: {
      id: string
      requiredRoleCanOverride: boolean
    }
  }
): Promise<User> {
  if (!authorizationHeader) {
    throw new Error('Request is missing authorization header')
  }
  const payload = jwt.verify(
    sanitizeToken(authorizationHeader),
    JwtSecret()
  ) as IJwtPayload
  const currentUser = await UserCollection.findOne({
    _id: new ObjectID(payload?.sub),
  })
  if (!currentUser) {
    throw new Error("User doesn't exist")
  }
  if (
    options?.permitIfSelf &&
    !currentUser._id.equals(options.permitIfSelf.id) &&
    !options.permitIfSelf.requiredRoleCanOverride
  ) {
    throw new Error(`You can only edit your own records`)
  }
  if (
    options?.requiredRole && 
    currentUser.role !== options.requiredRole
  ) {
    throw new Error(`You must have role: ${options.requiredRole}`)
  }
  return currentUser
} 
function sanitizeToken(authorization: string | undefined) {
  const authString = authorization || ''
  const authParts = authString.split(' ')
  return authParts.length === 2 ? authParts[1] : authParts[0]
} 
```

`authenticate` 方法作为 Express.js 中间件实现。它可以读取请求头中的授权令牌，验证提供的 JWT 的有效性，加载当前用户，并将其注入到响应流中，以便认证的 API 端点可以方便地访问当前用户的信息。这将通过 `me` API 进行演示。如果成功，中间件调用 `next()` 函数将控制权交还给 Express。如果失败，则无法调用 API。

注意，`authenticateHelper` 返回有用的错误消息，所以如果用户尝试执行他们无权执行的操作，他们不会感到困惑。

考虑 me API 的实现，它通过 `/v1/auth/me` 将当前登录用户返回给客户端，如下所示：

```js
**server/src/v1/routes/authRouter.ts**
/**
 * @swagger
 * /v1/auth/me:
 *   get:
 *     ...
 */
// tslint:disable-next-line: variable-name
router.get('/me', **authenticate()**,
  async (_req: Request, res: Response) => {
    if (res.locals.currentUser) {
      return res.send(res.locals.currentUser)
    }
    return res.status(401)
      .send({ message: AuthenticationRequiredMessage })
  }
) 
```

注意，`/v1/auth/me` 方法使用 `authenticate` 中间件，并简单地返回加载到响应流中的用户。

## 自定义服务器身份验证提供者

现在我们已经在服务器中实现了功能性的身份验证实现，我们可以在 LemonMart 中实现自定义身份验证提供者，如第八章 *设计身份验证和授权* 中所述：

你必须在你的 Angular 应用程序中实现这个自定义身份验证提供者。

本节代码示例位于 **lemon-mart** 仓库的 `projects/ch10` 文件夹中。请注意，该示例也位于 `web-app` 文件夹下。

1.  在 `environment.ts` 和 `environment.prod.ts` 中实现一个 `baseUrl` 变量。

1.  还选择 `authMode` 为 `AuthMode.CustomServer`：

    ```js
    **web-app/src/environments/environment.ts**
    **web-app/src/environments/environment.prod.ts**
    export const environment = {
      ...
      baseUrl: 'http://localhost:3000',
      authMode: AuthMode.CustomServer, 
    ```

1.  安装一个辅助库以编程方式访问 TypeScript 枚举值：

    ```js
    $ npm i ts-enum-util 
    ```

1.  按照如下所示实现自定义身份验证提供者：

    ```js
    **web-app/src/app/auth/auth.custom.service.ts**
    import { $enum } from 'ts-enum-util'
    interface IJwtToken {
      email: string
      role: string
      picture: string
      iat: number
      exp: number
      sub: string
    }
    @Injectable()
    export class CustomAuthService extends AuthService {
      constructor(private httpClient: HttpClient) {
        super()
      }
      protected authProvider(
        email: string,
        password: string
      ): Observable<IServerAuthResponse> {
        return this.httpClient.post<IServerAuthResponse>(
          `${environment.baseUrl}/v1/auth/login`,
          {
            email,
            password,
          }
        )
      }
      protected transformJwtToken(token: IJwtToken): IAuthStatus {
        return {
          isAuthenticated: token.email ? true : false,
          userId: token.sub,
          userRole: $enum(Role)
            .asValueOrDefault(token.role, Role.None),
          userEmail: token.email,
          userPicture: token.picture,
        } as IAuthStatus
      }
      protected getCurrentUser(): Observable<User> {
        return this.httpClient
          .get<IUser>(`${environment.baseUrl}/v1/auth/me`)
          .pipe(map(User.Build, catchError(transformError)))
      }
    } 
    ```

    `authProvider` 方法调用我们的 `/v1/auth/login` 方法，`getCurrentUser` 调用 `/v1/auth/me` 以检索当前用户。

    确保对 `login` 方法的调用始终发生在 HTTPS 上，否则你将在开放的互联网上发送用户凭据。这对于在公共 Wi-Fi 网络上监听者窃取用户凭据来说是一个很好的机会。

1.  更新 `authFactory` 以返回 `AuthMode.CustomServer` 选项的新提供者：

    ```js
    **web-app/src/app/auth/auth.factory.ts**
    export function authFactory(
      afAuth: **AngularFireAuth,**
      **httpClient**: HttpClient
    ) {
      ...
      case AuthMode.CustomServer:
        return new CustomAuthService(**httpClient**)
    } 
    ```

1.  在 `app.modules.ts` 中，更新 `AuthService` 提供者的 `deps` 属性以将 `HttpClient` 注入到 `authFactory`：

    ```js
    **web-app/src/app/app.module.ts**
    ...
      {
        provide: AuthService,
        useFactory: authFactory,
        deps: [AngularFireAuth, **HttpClient**],
      },
    ... 
    ```

1.  启动你的 Web 应用程序以确保一切正常工作。

接下来，让我们实现获取用户端点，以便我们的身份验证提供者可以获取当前用户。

## 通过 ID 获取用户

让我们在 `userRouter` 中实现通过 ID 获取用户的 GET API 端点，在 `/v2/users/{id}`：

```js
**server/src/v2/routes/userRouter.ts**
import { ObjectID } from 'mongodb'
import { authenticate } from '../../services/authService'
import { IUser, User, UserCollection } from '../../models/user'
/**
 * @swagger
 * /v2/users/{id}:
 *   get: …
 */
router.get(
  '/:userId',
  authenticate({
    requiredRole: Role.Manager,
    permitIfSelf: {
      idGetter: (req: Request) => req.body._id,
      requiredRoleCanOverride: true,
    },
  }),
  async (req: Request, res: Response) => {
    const user = await UserCollection
      .findOne({ _id: new ObjectID(req.params.userId) })
    if (!user) {
      res.status(404).send({ message: 'User not found.' })
    } else {
      res.send(user)
    }
  }
) 
```

在前面的代码示例中，我们通过用户 ID 查询数据库以找到我们正在寻找的记录。我们导入 `UserCollection` 并调用 `findOne` 方法以获取一个 `User` 对象。请注意，我们没有利用 `userService`。由于我们只检索单个记录并立即发送结果，额外的抽象层是繁琐的。然而，如果你开始向检索用户的操作中添加任何业务逻辑，那么请重构代码以利用 `userService`。

我们使用`authenticate`中间件来保护端点，允许用户检索他们的记录，管理员可以检索任何记录。

# 使用 Postman 生成用户

在本章前面，我们介绍了如何在*Express.js*部分的*实现 API*子部分的*服务*子部分中创建一个 POST 方法来创建新用户。使用这个 POST 端点和 Postman API 客户端，我们可以快速为测试目的生成用户记录。

你必须按照以下说明在**lemon-mart-server**中生成测试数据，这在后面的章节中将是必需的。

让我们安装和配置 Postman。

前往[`www.getpostman.com`](https://www.getpostman.com)下载并安装 Postman。

## 配置 Postman 进行认证调用

首先，我们需要配置 Postman，以便我们可以访问我们的认证端点：

使用`docker-compose up`或`npm run start:backend`启动你的服务器和数据库。记住，首先确保你能够执行 GitHub 上提供的示例服务器[`github.com/duluca/lemon-mart-server`](https://github.com/duluca/lemon-mart-server)。启动你自己的服务器版本是次要目标。

1.  创建一个名为`LemonMart`的新集合。

1.  添加一个 URL 为`http://localhost:3000/v1/auth/login`的 POST 请求。

1.  在头部设置键值对，Content-Type: `application/json`。

1.  在正文部分，提供我们定义在顶级`.env`文件中的演示用户登录的电子邮件和密码：

    ```js
    http://localhost:3000/v1/auth/login - Body
    {
        "email": "duluca@gmail.com",
        "password": "l0l1pop!!"
    } 
    ```

1.  点击**发送**以登录。

1.  复制`accessToken`，如下所示![图 B14094_10_09](img/B14094_10_09.png)

    图 10.9：设置 Postman

1.  点击右上角的设置图标来管理环境。

1.  添加一个名为 LemonMart Server 的新环境。

1.  创建一个名为`token`的变量。

1.  将`accessToken`值粘贴为当前值（不带括号）。

1.  点击**添加**/**更新**。

从现在起，当你添加 Postman 中的新请求时，你必须提供令牌变量作为授权头，如下所示：

![图 B14094_10_10](img/B14094_10_10.png)

![图 10.10：在 Postman 中提供令牌](img/B14094_10_10.png)

当使用 Postman 时，始终确保在右上角的下拉菜单中选择了正确的环境。

1.  切换到**授权**选项卡。

1.  选择**Bearer Token**作为类型。

1.  将令牌变量作为`{{token}}`提供。

当你**发送**你的请求时，你应该看到结果。请注意，当你的令牌过期时，你需要重复此过程。

## Postman 自动化

使用 Postman，我们可以自动化请求的执行。为了在我们的系统中创建示例用户，我们可以利用这个功能：

1.  为`http://localhost:3000/v2/user`创建一个新的名为**创建用户**的 POST 请求。

1.  在**授权**选项卡中设置`token`。

1.  在**正文**选项卡中，提供一个模板化的 JSON 对象，如下所示：

    ```js
    {
      "email": "{{email}}",
      "name": {
        "first": "{{first}}",
        "last": "{{last}}"
      },
      "picture": "https://en.wikipedia.org/wiki/Bugs_Bunny#/media/File:Bugs_Bunny.svg",
      "role": "clerk",
      "userStatus": true,
      "dateOfBirth": "1940-07-27",
      "address": {
        "line1": "123 Acme St",
        "city": "LooneyVille",
        "state": "Virginia",
        "zip": "22201"
      },
      "phones": [
        {
          "type": "mobile",
          "digits": "5551234567"
        }
      ]
    } 
    ```

    在本例中，我仅对电子邮件和姓名字段进行模板化。你可以对所有属性进行模板化。

1.  实现一个 Postman **Pre-request Script**，它在发送请求之前执行任意逻辑。该脚本将定义一个人员数组，并在请求执行时逐个设置当前环境变量：

    关于预请求脚本的更多信息，请查看 [`learning.postman.com/docs/postman/scripts/pre-request-scripts/`](https://learning.postman.com/docs/postman/scripts/pre-request-scripts/)。

1.  切换到 **Pre-request Script** 选项卡并实现脚本：

    ```js
    var people = pm.environment.get('people')
    if (!people) {
      people = [
        {email: 'efg@gmail.com', first: 'Ali', last: 'Smith'},
        {email: 'veli@gmail.com', first: 'Veli', last: 'Tepeli'},
        {email: 'thunderdome@hotmail.com', first: 'Justin', last: 'Thunderclaps'},
        {email: 'jt23@hotmail.com', first: 'Tim', last: 'John'},
        {email: 'apple@smith.com', first: 'Obladi', last: 'Oblada'},
        {email: 'jones.smith@icloud.com', first: 'Smith', last: 'Jones'},
        {email: 'bugs@bunnylove.com', first: 'Bugs', last: 'Bunny'},
      ]
    }
    var person = people.shift()
    pm.environment.set('email', person.email)
    pm.environment.set('first', person.first)
    pm.environment.set('last', person.last)
    pm.environment.set('people', people) 
    ```

    `pm` 是一个全局变量，代表 **P**ost**M**an。

    在第一行，我们从环境中获取 `people` 数组。在第一次请求期间，它将不存在，这允许我们使用测试数据初始化数组。接下来，我们移动到下一个记录，并设置我们在模板请求体中使用的单个变量。然后，我们将当前数组的当前状态保存回环境，这样，在下次执行时，我们可以移动到下一个记录，直到我们用完记录。

1.  在 **Tests** 选项卡中实现一个 `test` 脚本：

    ```js
    var people = pm.environment.get('people')
    if (people && people.length > 0) {
      postman.setNextRequest('Create Users')
    } else {
      postman.setNextRequest(null)
    } 
    ```

1.  确保保存您的请求。

    在这里，我们定义一个 `test` 脚本，该脚本将一直执行，直到 `people.length` 达到零。在每次迭代中，我们调用 **Create Users** 请求。当没有剩下的人时，我们调用 `null` 来终止测试。

    如您所想象，您可以将多个请求和多个环境变量组合起来执行复杂的测试。

1.  现在，使用屏幕左上角的 **Runner** 执行脚本：![图片](img/B14094_10_11.png)

    图 10.11：Postman UI 左上角的运行器按钮

1.  在继续操作之前，请更新您的 `login` 令牌。

1.  按照以下配置设置运行器：![图片](img/B14094_10_12.png)

    图 10.12：集合运行器配置

1.  选择 **LemonMart** 集合。

    选择包含 `token` 变量的 **LemonMart Server** 环境。

    只选择 **Create Users** 请求。

    点击 **Run LemonMart** 来执行。

如果您的运行成功，您应该看到以下输出：

![图片](img/B14094_10_13.png)

图 10.13：集合运行器结果

如果您使用 Studio 3T 作为 MongoDB 探索器，您可以看到所有记录都已创建，或者您可以使用 Postman 检查它们，当我们实现 `/v2/users` 端点时。

注意，由于我们有一个唯一的电子邮件索引，您的下一次运行部分成功。对于已创建的记录的 POST 请求将返回 `400 Bad Request`。

您可以在 [`studio3t.com/`](https://studio3t.com/) 上了解更多关于 Studio 3T 的信息。

## 添加用户

我们已经在本章前面的 *Services* 部分介绍了如何创建 POST 请求。现在，让我们看看您如何更新现有的用户记录：

```js
**server/src/v2/routes/userRouter.ts**
/**
 * @swagger
 * /v2/users/{id}:
 *   put:
 */
router.put(
  '/:userId',
  authenticate({
    requiredRole: Role.Manager,
    permitIfSelf: {
      idGetter: (req: Request) => req.body._id,
      requiredRoleCanOverride: true,
    },
  }),
  async (req: Request, res: Response) => {
    const userData = req.body as User
    delete userData._id
    await UserCollection.findOneAndUpdate(
      { _id: new ObjectID(req.params.userId) },
      {
        $set: userData,
      }
    )
    const user = await UserCollection
      .findOne({ _id: new ObjectID(req.params.userId) })
    if (!user) {
      res.status(404).send({ message: 'User not found.' })
    } else {
      res.send(user)
    }
  }
) 
```

我们从请求体中设置 `userData`。然后我们 `delete` 请求体中的 `_id` 属性，因为 URL 参数是信息的权威来源。此外，这还可以防止用户的 ID 被意外更改成不同的值。

然后，我们利用`findOneAndUpdate`方法定位并更新记录。我们使用 ID 查询记录。通过使用 MongoDB 的`$set`运算符来更新记录。

最后，我们从数据库中加载保存的记录并将其返回给客户端。

POST 和 PUT 方法应始终响应记录的更新状态。

对于我们最后的实现部分，让我们回顾一下可以支持分页数据表的 API 端点。

## 使用 DocumentTS 进行分页和过滤

到目前为止，DocumentTS 最有用的功能是`findWithPagination`，如*关于 DocumentTS*部分所述。让我们利用`findWithPagination`来实现`/v2/users`端点，它可以返回所有用户：

```js
**server/src/v2/routes/userRouter.ts**
/**
 * @swagger
 * components:
 *   parameters:
 *     filterParam: …
 *     skipParam: …
 *     limitParam: …
 *     sortKeyParam: …
 */
/**
 * @swagger
 * /v2/users:
 *   get:
 */
router.get(
  '/',
  authenticate({ requiredRole: Role.Manager }),
  async (req: Request, res: Response) => {
    const query: Partial<IQueryParameters> = {
      filter: req.query.filter,
      limit: req.query.limit,
      skip: req.query.skip,
      sortKeyOrList: req.query.sortKey,
      projectionKeyOrList: ['email', 'role', '_id', 'name'],
    }
    const users = await UserCollection.findWithPagination<User>(query)
    res.send(users)
  }
) 
```

我们使用`req.query`对象作为局部变量复制 URL 中的所有参数。我们定义了一个名为`projectionKeyOrList`的附加属性，以限制可以返回给客户端的记录属性。在这种情况下，仅返回`email`、`role`、`_id`和`name`属性。这最小化了通过网络发送的数据量。

最后，我们只需将新的`query`对象传递给`findWithPagination`函数，并将结果返回给客户端。

您可以在 Postman 中创建一个新的请求来验证您的新端点的正确功能，如下面的截图所示：

![图片](img/B14094_10_14.png)

图 10.14：使用 Postman 调用获取用户

在*第十二章*，*食谱 - 主/详细，数据表和 NgRx*中，我们将实现一个利用过滤、排序和数据限制功能的分页数据表。

恭喜！您现在掌握了代码在整个软件栈中如何工作的知识，从数据库到前端和后端。

# 摘要

在本章中，我们介绍了全栈架构。您学习了如何构建最小化的 MEAN 栈。您现在知道如何为全栈应用程序创建 monorepo，并使用 TypeScript 配置 Node.js 服务器。您将 Node.js 服务器容器化，并使用 Docker Compose 声明性地定义了您的基础设施。使用 Docker Compose 与 CircleCI，您在 CI 环境中验证了您的基础设施。

您使用 Swagger 和 OpenAPI 规范设计了 RESTful API，设置了 Express.js 应用程序，并配置了它，以便您可以将 Swagger 定义作为 API 的文档进行集成。您使用 DocumentTS ODM 配置 MongoDB，以便您可以轻松连接和查询文档。您定义了一个具有密码散列功能的用户模型。

然后您实现了基于 JWT 的认证服务。您实现了一个`authenticate`中间件来保护 API 端点并允许基于角色的访问。您学习了如何使用 Postman 与 RESTful API 交互。使用 Postman 的自动化功能，您生成了测试数据。最后，您实现了认证功能的 RESTful API 和用户的 CRUD 操作。

在接下来的两个章节中，我们将介绍 Angular 食谱来创建表单和数据表。当你实现它们时，你将希望 Lemon Mart 服务器运行起来，以验证表单和表格的正确功能。

# 练习

你使用`authenticate`中间件来保护你的端点。你已配置 Postman 发送有效的令牌，以便你可以与受保护的端点通信。作为练习，尝试移除`authenticate`中间件，并使用和没有有效令牌的方式调用相同的端点。重新添加中间件，再次尝试相同的事情。观察你从服务器收到的不同响应。

# 进一步阅读

+   *什么是 DX？（开发者体验）*，Albert Cavalcante，2019：[`medium.com/@albertcavalcante/what-is-dx-developer-experience-401a0e44a9d9`](https://medium.com/@albertcavalcante/what-is-dx-developer-experience-401a0e44a9d9)

+   *阻塞与非阻塞概述*，2020：[`nodejs.org/en/docs/guides/blocking-vs-non-blocking/`](https://nodejs.org/en/docs/guides/blocking-vs-non-blocking/)

+   *像对我五岁孩子解释非阻塞 I/O，Frank Rosner*，2019：[`blog.codecentric.de/en/2019/04/explain-non-blocking-i-o-like-im-five/`](https://blog.codecentric.de/en/2019/04/explain-non-blocking-i-o-like-im-five/)

+   *OpenAPI 规范*，2020：[`swagger.io/docs/specification`](https://swagger.io/docs/specification)

+   *序列化*，2020：[`en.wikipedia.org/wiki/Serialization`](https://en.wikipedia.org/wiki/Serialization)

+   *JSON*，2020：[`en.wikipedia.org/wiki/JSON`](https://en.wikipedia.org/wiki/JSON)

+   *MongoDB 中的聚合*，2020：[`docs.mongodb.com/manual/aggregation`](https://docs.mongodb.com/manual/aggregation)

# 问题

尽可能好地回答以下问题，以确保你在不使用 Google 的情况下理解了本章的关键概念。你需要帮助回答这些问题吗？请参阅*附录 D*，*自我评估答案*，在线位于[`static.packt-cdn.com/downloads/9781838648800_Appendix_D_Self-Assessment_Answers.pdf`](https://static.packt-cdn.com/downloads/9781838648800_Appendix_D_Self-Assessment_Answers.pdf)或访问[`expertlysimple.io/angular-self-assessment`](https://expertlysimple.io/angular-self-assessment)。

1.  构成优秀开发者体验的主要组件有哪些？

1.  `.env`文件是什么？

1.  `authenticate`中间件的作用是什么？

1.  Docker Compose 与使用`Dockerfile`有何不同？

1.  什么是 ODM？它与 ORM 有何不同？

1.  中间件是什么？

1.  Swagger 的用途是什么？

1.  你会如何重构`userRouter.ts`中`/v2/users/{id} PUT`端点的代码，以便代码可重用？
