# 12

# 部署 Angular 应用程序

在本章的最后部分，你将把我们所创建的演示应用程序部署到 GitHub Pages。你将学习如何在你的 Nx 单一代码库中检查和构建 Angular 应用程序和库。我们将探讨 Angular 和 Nx 在构建 Angular 应用程序时为你创建的内容，以及你部署到托管平台上的内容。当你知道如何构建你的 Angular 应用程序后，我们将检查应用程序的构建并分析构建中的不同包，以确定我们可以从哪里进行一些改进来减小应用程序包的大小。在分析应用程序包之后，我们首先将在本地托管我们的应用程序的生产构建。接下来，我们将深入研究 GitHub Pages 和 GitHub Actions 以创建自动部署流程。GitHub Pages 是一个静态网站托管服务，允许你免费托管你的静态网站和应用程序。GitHub Actions 用于设置**持续集成和持续交付**（**CI/CD**）流程来自动化你的代码检查、测试和部署过程。你创建的 CI/CD 流程将在你将代码合并到主 GitHub 分支时自动在 Nx 单一代码库中检查、测试和部署你的 Angular 应用程序。 

本章将涵盖以下主题：

+   在你的 Nx 单一代码库中构建和检查 Angular 应用程序

+   分析构建输出

+   自动部署 Angular 应用程序

# 在你的 Nx 单一代码库中构建和检查 Angular 应用程序

本节将探讨在 Nx 单一代码库中构建和检查 Angular 应用程序的步骤。我们首先学习如何在你的 Nx 单一代码库中检查项目。接下来，我们将探索不同的代码检查过程配置以及如何应用它们。一旦你知道在创建构建之前如何以及为什么要检查你的项目，我们将学习如何在 Nx 单一代码库中构建项目。

此外，我们还将探讨 Nx 如何通过利用高级缓存和并行任务执行来优化构建时间来增强构建过程。Nx 的增量构建显著减少了大型和复杂项目的构建时间，确保只有应用程序的必要部分被重新构建。增量构建和 Nx 缓存将加速你的 CI/CD 管道和整体部署过程。让我们从开始学习如何在你的 Nx 单一代码库中检查项目代码检查。

## 检查 Nx 项目

**代码检查**是软件开发过程中的关键步骤。它涉及分析你的代码以查找潜在的错误、强制执行编码标准和维护代码质量。代码检查有助于在开发周期早期捕捉问题，降低出现错误的可能性，并确保你的代码库遵循最佳实践。

代码风格检查可以提高您代码的可维护性和可读性，并通过提供一致的编码风格来增强团队内部的协作。我们已经在*第一章*中讨论了代码风格检查。根据您的配置，您将在开发过程中通过 ESLint 获得关于代码风格检查错误的提示。然而，在代码合并或部署之前始终运行代码风格检查过程是很重要的。在 CI/CD 管道中运行代码风格检查过程确保不符合您配置的指南的代码不会被合并。通过将代码风格检查集成到您的管道中，您可以确保项目健壮、可靠且适用于生产，从而实现更高效和更无错误的构建。

与所有其他任务（如测试和构建）一样，您可以使用 Nx 控制台或通过运行终端命令来进行代码风格检查。

### 使用 Nx 控制台进行代码风格检查

如果您想使用 Nx 控制台对项目进行代码风格检查，请按照以下步骤操作：

1.  在 VS Code 左侧点击 Nx 标志。

1.  在 **项目** 选项卡下找到您想要进行代码风格检查的项目。

1.  将鼠标悬停在 `lint` 上。

1.  点击播放按钮：

![图 12.1：Nx 控制台代码风格检查](img/B21625_12_001.jpg)

图 12.1：Nx 控制台代码风格检查

*图 12.1* 展示了 Nx 控制台的视觉表示以及如何找到 `lint` 任务。当您点击 `lint` 任务的播放按钮时，VS Code 内部将打开一个终端，您可以在其中看到代码风格检查的结果。在 *finance-expenses-registration 应用程序* 的例子中，代码风格检查过程将无任何错误或警告通过您的代码风格检查规则。对于 Nx Cloud 将会有一个警告，但可以忽略，因为我们没有使用 Nx Cloud。当使用 Nx 控制台时，您只能单独对项目进行代码风格检查。或者，您也可以使用终端命令通过 Nx 控制台对项目进行代码风格检查。

### 使用终端命令进行代码风格检查

您可以通过在 Nx 单个项目仓库的根目录下运行以下终端命令来对单个项目进行代码风格检查：

```js
nx run <project name>:lint
```

在前面的终端命令中，您需要将 `<project name>` 替换为您想要进行代码风格检查的项目名称。这个项目名称可以在您想要目标项目的 `project.json` 文件中找到。因此，要对 *finance-expenses-registration 应用程序* 进行代码风格检查，您需要运行以下命令：

```js
nx run finance-expenses-registration:lint
```

由于 Nx 缓存，第二次运行代码风格检查过程要快得多。在终端中，您会发现 Nx 从缓存中读取输出而不是为 1 个任务中的 1 个运行命令，这表明 Nx 从缓存中获取了代码风格检查的输出，因为它检测到自上次代码风格检查以来项目没有发生变化。当对多个项目运行代码风格检查时，Nx 缓存可以显著减少代码风格检查完成所需的时间，大大提高您的开发过程和体验。

### 对多个项目进行代码风格检查

在前面的示例中，我们看到了如何为单个项目运行 linting，但在大多数情况下，尤其是在您的 CI/CD 管道中，您希望 lint 多个项目。当您在 CI 管道（在每次代码合并之前运行的管道）中运行 linting 时，您通常希望 lint 受您即将合并的更改影响的项目；或者，当运行部署您的系统的 CD 管道时，您通常会在您的 monorepo 中的所有项目或与部署相关的所有项目中运行 linting。

要运行受您更改影响的所有项目的 linting，您可以使用`nx affected`命令。终端命令与我们在为受影响的项目运行测试时使用的`affected`命令类似；您只需更改目标作业从`test`到`lint`：

```js
nx affected -t lint –base=main
```

如您在前面的命令中看到的，您使用`affected`关键字，添加`-t`标志，并定义您想要运行的目标任务。在这个场景中，它是`lint`。在定义任务之后，我们还需要配置`–base`标志，并为其提供要与之比较更改的基分支；通常，这将是你主分支。您不必手动定义`–base`标志，也可以在您的`nx.json`文件中配置`defaultBase`属性。要配置您的默认基分支，请在您的`nx.json`文件的根目录下添加以下内容：

```js
"affected": {
  "defaultBase": "main"
}
```

在添加上述代码之后，您可以在不提供`–base`标志的情况下运行`affected`命令。目前，如果您运行`affected`命令，您会注意到 monorepo 中的所有项目都被 linted。所有项目都被 linted，因为 Nx 没有为您的`defaultBase`属性缓存受影响的 lint。除了所有项目都被 linted 之外，您还会注意到 linting 过程中有 4 个项目中的 4 个失败了。

### 修复 linting 错误

在我们继续之前，让我们修复 linting 错误，以便所有 linted 项目都能成功。

#### `--fix`标志

您可以手动逐个查看终端输出中的所有错误并逐一修复它们，但大多数 linting 错误很容易修复，Nx 的 linting 过程提供了一种为您解决所有简单 linting 错误的方法。只需将`–-fix`标志附加到您的终端命令中，Nx 将自动修复它可以修复的问题。要使用`--fix`标志获得最佳结果，您需要为每个项目单独运行 linting 过程。所以，让我们取有 linting 错误的项目，并逐个运行它们，命令中附加了`–-fix`标志：

```js
nx run workspace-generators-plugin:lint --fix
nx run shared-ui-common-components:lint --fix
nx run shared-data-access-generic-http:lint --fix
nx run shared-util-custom-decorators:lint --fix
```

在运行上述命令之后，所有的 linting 错误都已被修复。现在您可以重新运行受影响的`lint`命令，所有项目的 linting 都将成功。

#### 修复选项

如您所想象，为每个有 linting 错误的工程运行 linting 命令可能会变得繁琐，尤其是在您的 monorepo 增长时。另外，您可以在各个项目的`project.json`文件内配置`fix`选项。在`project.json`文件中，您会找到一个`lint`部分，在这个部分内，您可以添加`fix`选项，如下所示：

```js
"lint": {
  ……
  "options": {
    "fix": true
  }
}
```

在将前面的配置添加到每个项目的`project.json`文件后，您只需运行`lint affected`命令，Nx 将为每个项目修复其可以修复的 linting 问题。

#### 其他配置

除了`--fix`标志和配置之外，还有其他有用的配置，例如在 linting 过程失败之前允许最大警告数，将您的 linting 结果输出到文件，或者即使存在 linting 错误也传递 linting 过程。您可以在以下 URL 的官方 Nx 文档中找到所有配置：[`nx.dev/nx-api/eslint/executors/lint`](https://nx.dev/nx-api/eslint/executors/lint)。

### 运行 linting 命令时针对多个项目

在查看剩余的配置选项后，我将简要解释如何在运行 linting 命令时同时针对多个项目。您可以使用`run-many`命令来完成此操作，类似于我们在*第十一章*中用单个命令测试多个项目时所做的那样。您使用`run-many`命令结合`-p`标志。`run-many`命令允许您为许多项目运行任务，而`-p`标志将允许您指定特定的项目：

```js
nx run-many -t lint -p project1 project2
```

在前面的例子中，您为`project1`和`project2`执行了 linting。另外，您可以通过以下命令为具有特定标签的项目运行 linting 过程：

```js
nx run-many -t lint --projects=tag:type:ui
```

如您在前面的命令中所见，我们不是定义项目名称，而是定义了我们想要针对的标签。

现在您已经知道了如何 lint 特定的项目以及如何配置 linting 过程，让我们深入下一个主题：在 Nx monorepo 内部构建您的项目。

## 构建您的 Angular 库和应用程序

要部署您的 Angular 项目，您需要创建一个应用程序构建。当您创建应用程序构建时，构建过程会创建一个代码包，您的运行时可以执行和运行它。在 Angular 应用程序的情况下，浏览器将是解释和运行应用程序代码的运行时。因此，要部署我们创建的演示应用程序，您首先需要使用`nx` `build`命令创建一个应用程序构建。

### 创建应用程序构建的幕后

在运行我们的*expenses-registration 应用程序的`nx build`命令之前，让我们详细检查运行`build`命令时会发生什么：

1.  您即将构建的项目中的 `project.json` 文件。这与标准 Angular CLI 类似，但支持单仓库中的多个项目，Nx 分析 `project.json` 文件而不是常规 Angular 项目中的 `angular.json` 文件。

1.  `project.json` 文件。如果您为构建目标配置了文件替换，Nx 将在继续构建过程之前替换文件。

1.  `ngc`，将 TypeScript 代码转换为 JavaScript，处理 Angular 装饰器和模板。

1.  **预编译（AOT）**：与 Angular CLI 类似，Nx 对生产构建执行 AOT 编译（除非配置不同），将 Angular 模板和组件转换为浏览器可以执行的效率高的 JavaScript 代码，而无需在渲染之前编译代码。

1.  **捆绑阶段**

    +   **模块解析**：Nx 使用配置的构建工具进行模块解析和创建依赖图，从入口点（s）开始。

    +   **摇树优化**：Nx 执行摇树优化以删除未使用的代码，减小最终捆绑包的大小。

    +   **代码拆分**：Nx 将应用程序的不同部分拆分为多个 JavaScript 捆绑包，这些捆绑包可以被浏览器懒加载。

    +   **资产优化**：Nx 优化 CSS、HTML、图像和其他资产，类似于 Angular CLI。

1.  **压缩** **和压缩**

    +   **JavaScript 压缩**：Nx 压缩 JavaScript 代码以减小文件大小。

    +   **压缩**：Nx 进一步混淆 JavaScript 代码。

1.  **哈希和缓存失效**

    +   **文件哈希**：Nx 将哈希值附加到生成的捆绑包和资产的文件名上，以实现缓存失效。

1.  `index.html` 文件，包括对哈希过的 JavaScript 和 CSS 捆绑的引用。

1.  `dist` 文件夹或某些其他配置的输出路径。

前面的步骤与使用 Angular CLI 时的常规构建流程相似。然而，Nx 有一些额外的功能，例如受影响构建、`run-many` 命令、增量构建和缓存，这使得在单仓库内为多个项目创建应用程序构建更加容易，并加快了构建时间。现在您已经更好地了解了构建项目时发生的情况，让我们为费用注册应用程序创建一个应用程序构建。

重要通知！

我们制作的 *费用注册应用程序* 只是一个简单的演示应用程序，并不真正适用于生产目的。在您实际部署应用程序之前，还有很多改进可以做出，需要添加页面，以及需要清理的代码。为了演示目的，我们将把应用程序部署到 GitHub Pages，但这并不是为了其他任何目的而使用。

### 运行构建命令

您可以在终端或 Nx 控制台中运行 *费用注册应用程序* 的 `build` 命令。如图 *图 12.2* 所示，您可以在 Nx 控制台中找到 `build` 命令，它在 `lint` 命令之上：

![图 12.2：Nx 控制台构建](img/B21625_12_002.jpg)

图 12.2：Nx 控制台构建

要运行`build`命令，在 Nx 控制台中点击`build`关键字旁边的播放按钮。Nx 将生产构建配置为默认配置；如果你想针对另一个构建配置，你可以在 Nx 控制台中展开`build`以查看所有构建配置。然后，你可以通过点击你想要运行的构建配置名称旁边的播放按钮来运行特定的构建配置。对于每个项目，Nx 都会在`project.json`文件中为你创建一个生产和开发构建配置。当需要时，你可以创建额外的构建配置；例如，当你有多个预发布环境，如测试和验收，这些环境需要从生产或开发构建中有所偏差时。

Nx 控制台是代码编辑器的扩展，因此它不可用于你的 CI/CD 管道中。由于 Nx 控制台不可用，你需要在管道中使用 Nx CLI 来创建应用程序构建并执行其他任务，例如构建和测试你的应用程序。`nx build`命令的语法与之前使用的`test`和`lint`命令类似；你只需将构建目标从`lint`或`test`更改为`build`：

```js
nx build finance-expenses-registration
```

如上述命令所示，我们输入`nx`来指定 Nx CLI，然后是我们要运行的任务，最后添加我们想要针对的项目名称。Nx 将使用默认的构建配置，除非你更改它。如果你想针对不同的构建配置，你可以在`build`命令中添加`–-configuration`标志：

```js
--configuration=development
```

通过在 CLI 命令的末尾添加前面的标志，你将使用`development`构建配置而不是生产配置。或者，你也可以使用以下语法：

```js
nx run finance-expenses-registration:build:production
```

上述命令为`finance-expenses-registration`项目运行了生产构建。正如我们通过`lint`和`test`命令所看到的，你也可以使用`affected`和`run-many`来构建多个项目或受你更改影响的项目。现在，你已经知道了如何使用 Nx 控制台和 Nx CLI 运行`build`命令，那么就继续为`finance-expenses-registration`项目运行生产构建吧。

### 修复失败的构建

在为`finance-expenses-registration`项目运行生产构建后，你会注意到 Nx 试图构建`finance-expenses-registration`项目以及它所依赖的所有项目——在我们的案例中，是三个其他库（`shared-ui-common-components`、`finance-data-access-expenses`和`shared-util-form-validator`）——在我们的 Nx 单仓库中。你还会注意到，四个构建中有三个失败了。这三个失败的构建是三个库，它们都有相同的错误：`'updateBuildableProjectDepsInPackageJson'`在`schema`中未找到。

为了修复你的构建，你需要在你的库的`project.json`文件中移除以下配置：

```js
"updateBuildableProjectDepsInPackageJson": true
```

在你的`project.json`文件中移除前面的配置后，你可以再次运行`finance-expenses-registration`项目的`build`命令，现在你的构建过程将成功。构建完成后，你会在 Nx 单体仓库的根目录下注意到已创建一个`dist`文件夹。`finance-expenses-registration`项目及其所有依赖的可构建项目的构建输出都位于这个`dist`文件夹内。`finance-expenses-registration`项目的构建输出可以在以下路径找到：`dist/apps/finance/expenses-registration`。

当你部署你的应用程序时，你需要将`dist/apps/finance/expenses-registration`目录内的输出上传到你的托管服务。在 Angular 应用程序的情况下，你可以使用诸如 GitHub Pages、Azure Blob Storage、Amazon **简单存储服务** (**S3**) 或你偏好的任何其他静态网站托管服务。如果你正在使用 Angular Universal 和**服务器端渲染** (**SSR**)，你需要一个不同的托管服务，例如 Azure App Service，但在这本书中我们不会涉及这一点。

在本章的最后部分，我们将使用 GitHub Actions 自动部署和托管我们的演示应用程序到 GitHub Pages，但现在，让我们看看如何使用`http-server`在我们的机器上本地托管我们的生产构建。

### 本地托管生产构建

为了托管我们的生产构建，我们需要一个可以托管我们的静态文件的服务器。市面上有许多可以做到这一点的服务，但我们将使用`http-server`。首先，你需要使用以下命令在你的机器上安装`http-server`：

```js
npm install http-server -g
```

安装`http-server`后，你可以使用它来本地托管你的 Angular 应用程序的生产构建。如果你没有诸如测试和验收之类的预发布环境，使用像`http-server`这样的工具可以在部署到生产环境之前测试你的构建是否按预期工作；然而，在专业设置中，我总是推荐使用一个测试和验收环境，在那里你可以在一个类似于生产环境的环境中测试你的应用程序。

要使用`http-server`在本地托管你的应用程序，你需要在 Nx 单体仓库的根目录下运行以下终端命令：

```js
http-server dist\apps\finance\expenses-registration
```

运行前面的命令后，将创建一个服务器，托管`dist\apps\finance\expenses-registration`文件夹内的静态文件。在终端中，你会找到两个以你的私有 IP 地址开头并以`8080`端口结尾的 URL。你可以在浏览器中访问这两个 URL 中的任何一个来查看你的应用程序。当你通过浏览器访问应用程序时，你会在终端中看到你的应用程序发出的请求。你可能注意到`api/expenses`请求失败；导致 API 请求失败的原因是我们没有运行中的 API，并且在生产构建中禁用了模拟 API 拦截器。

由于我们禁用了模拟数据拦截器并且我们没有运行中的 API，所以当你用浏览器访问它时，你不会在应用程序中看到任何数据。为了测试目的，你可以通过在 `mock.interceptor.ts` 文件中移除以下内容来为你的生产构建启用模拟拦截器：

```js
!isDevMode() ||
```

移除前面的代码后，模拟拦截器也将适用于你的生产构建。创建一个新的应用程序构建，使用 `http-server` 提供服务，然后在浏览器中重新访问应用程序（你可能需要通过打开开发者工具，右键单击浏览器中的重新加载符号，并选择 `api/expenses` 请求成功来清除浏览器缓存）。别忘了在 `mock.interceptor.ts` 文件中撤销更改，因为这只是为了测试目的；在实际的生产应用程序中，你希望使用真实的 API。

因此，总结一下，你学习了如何在 Nx 单一代码库内部对项目进行代码检查，以及如何通过使用 `–-fix` 标志自动修复基本的代码检查错误。你学习了当你为 Angular 应用程序创建应用程序构建时幕后发生了什么。你还学习了如何为特定的构建配置运行 `build` 命令以及在哪里可以找到应用程序构建的输出。最后，你使用 `http-server` `npm` 包在本地托管了构建输出。在下一节中，你将学习如何分析应用程序构建，以便你可以轻松地识别哪些构建部分可以工作以减小你的包大小。

# 分析你的构建输出

在你的构建输出中，你会找到不同的 JavaScript 文件。其中一些文件是你的应用程序包，浏览器将加载这些包来为最终用户渲染应用程序。这些包的大小直接影响到你应用程序的性能。JavaScript 对浏览器来说加载较慢，因此你的包越大，浏览器下载文件、在屏幕上渲染内容以及使网页响应用户交互所需的时间就越长。

为了减小你的包大小，你需要一种有效的方法来分析你的包。如果你为 `finance-expenses-registration` 项目运行 `build` 命令，你会在终端看到一个小的报告

终端内的构建报告包括创建的包列表以及每个包的大小。虽然这个列表给你一些关于包大小的指示，但你看不到包由什么组成以及你可以在哪里进行一些改进以减小包的大小。

有一个名为 `npm` 的工具包，使用以下命令：

```js
npm i -g webpack-bundle-analyzer
```

在全局安装包之后，你需要在 Nx 单一代码库内部将其安装为开发依赖。你通过在 Nx 单一代码库的根目录下运行以下命令来将 Webpack Bundle Analyzer 工具作为开发依赖项添加：

```js
npm i webpack-bundle-analyzer -save-dev
```

在运行前面的 `npm` 命令之后，您就可以开始使用 Webpack Bundle Analyzer 了。第一步是为您的应用程序创建一个新的生产构建，并在 `build` 命令中包含一个 `–-stats-json` 标志：

```js
nx build finance-expenses-registration --stats-json
```

通过添加 `–-stats-json` 标志，Webpack Bundle Analyzer 将在构建输出中创建一个 `stats.json` 文件。`stats.json` 文件位于 `dist\apps\finance\expenses-registration` 文件夹内，与您的应用程序构建的其他部分相邻。要检查 `stats.json` 文件，您可以在您的 Nx 单一代码库的根目录下运行以下命令：

```js
webpack-bundle-analyzer dist/apps/finance/expenses-registration/stats.json
```

运行前面的命令将为您提供应用程序包的视觉和详细概述。概述将在浏览器中打开，应该看起来像这样：

![图 12.3：Webpack Bundle Analyzer](img/B21625_12_003.jpg)

图 12.3：Webpack Bundle Analyzer

如 *图 12.3* 所示，Webpack Bundle Analyzer 工具为您提供了关于您的包的详细且易于阅读的概述以及每个包中包含的内容。您可以轻松地识别每个包中的大型部分，以便您可以查看可以改进的地方。由于我们的应用程序很简单，所以没有太多可以调整的，但例如，您可以看到 `ngrx-store` 和 `transloco` 包是我们添加到项目中最大的元素之一。如果您的包大小变得过大，您可以尝试寻找替换您导入的大型 `npm` 包，如果可用，仅导入小的子模块，或者将代码和其他资源移动到懒加载模块中，以便浏览器首先渲染页面并懒加载额外的资源。

如您在 *图 12.3* 中所见，在 Webpack Bundle Analyzer 的侧边栏中，您可以控制屏幕上显示哪些包；这使得关注构建输出中的特定包变得更容易。此外，您还可以以三种不同的格式查看包大小 – **Stat**、**Parsed** 和 **Gzipped** 格式：

+   **Stat** 是最大的；这表示您的包在没有任何压缩或优化情况下的原始大小。

+   **Parsed** 的大小与您的终端构建输出报告中所显示的大小相同。这是在应用了一些优化和压缩之后您包的大小。

+   如果您看到 `Content-Encoding: Gzip` 标头，则知道您的托管服务已为您启用了 Gzipping；如果该标头未包含在内，您需要自己添加 Gzipping。如何添加 Gzipping 本身超出了本书的范围。GitHub Pages 会为您压缩 Angular 应用程序。

总结一下，你现在知道如何有效地使用 Webpack Bundle Analyzer 分析你的应用程序包。如果包太大，你可以尝试将代码移动到懒加载模块中，并寻找替换你导入的大型 `npm` 包。你还了解到，对内容进行 Gzipping 可以显著减小包的大小，并且大多数现代托管服务为你处理 Gzipping 和必要的配置。在本书的下一节和最后一节中，我们将使用 GitHub Actions 创建一个 CI/CD 流程，该流程将自动将我们的演示应用程序部署到 GitHub Pages。

# 自动部署 Angular 应用程序

在本节的最后部分，你将学习如何自动部署 Angular 应用程序到你的 Nx 单一代码仓库中的 GitHub Pages。你将设置一个使用 GitHub Actions 的 CI/CD 流水线，每当你在 GitHub 的主分支上合并代码时，它就会部署我们创建的演示应用程序。我们将创建一个包含所有必要部署步骤的 `.yml` 文件。在 `.yml` 文件内部，我们将使用 YAML 语言，这是一种常用于创建各种 DevOps 工具和程序配置文件的通用语言。

## 创建访问令牌

在开始创建 `.yml` 文件之前，你需要在你的 GitHub 账户中创建一个访问令牌。GitHub 需要知道你已认证，可以部署应用程序到 GitHub Pages，然后才能执行部署。你向 GitHub 提供必要的认证，在 `.yml` 文件中的部署步骤提供访问令牌。因此，你需要做的第一件事是在你的 GitHub 账户中创建一个访问令牌。按照以下步骤操作：

1.  首先，点击 GitHub 中的你的账户图标并选择 **Settings**。

1.  在个人设置页面，你需要向下滚动并选择屏幕左侧的 **Develop settings**。

1.  现在，再次点击屏幕左侧的 **Personal access tokens**，然后在下拉菜单中选择 **Tokens (classic)**。

1.  接下来，你需要点击 **Generate new token** 按钮，并在下拉菜单中选择 **Generate new** **token (classic)**。

    在遵循 *步骤 1* 到 *4* 之后，你应该会看到 *图 12.4* 所示的页面：

![图 12.4：GitHub 个人访问令牌](img/B21625_12_004.jpg)

图 12.4：GitHub 个人访问令牌

1.  现在，在 `GH_PAGES` 下，在 `GH_PAGES` 名称下。

1.  对于 **Expiration** 字段，选择 **No expiration**。在实际的生产环境中，你可能出于安全原因想要一个过期日期，但这意味着你需要根据设定的过期间隔更新你的令牌。

1.  在 `gist` 下。

1.  在选择 **Select scopes** 下的复选框后，你可以向下滚动并选择 **Generate token**。点击 **Generate token** 后，你会看到你的个人访问令牌的密钥；它看起来可能类似于这个：

    ```js
    ghp_vnjAH0PRGRcIO6UQGO2GavdOUVUDPJ1Jrv77
    ```

    将访问令牌的密钥复制到你的系统中并安全存储；你将在自动化部署配置的下一步中需要它。

1.  在复制并保存您的密钥后，转到存储您在本书学习过程中创建的代码的 GitHub 仓库。

1.  接下来，您需要通过点击导航项中的**设置**来导航到仓库设置，如图*图 12.5*所示：

![图 12.5：GitHub 仓库设置](img/B21625_12_005.jpg)

图 12.5：GitHub 仓库设置

1.  现在，在屏幕左侧的**安全**部分下，点击**密钥和变量**，然后在下拉菜单中点击**动作**。

    这将带您到**动作密钥和变量**页面，如图*图 12.6*所示：

![图 12.6：动作密钥和变量](img/B21625_12_006.jpg)

图 12.6：动作密钥和变量

1.  在**动作密钥和变量**页面，您需要点击**新建仓库密钥**按钮。

1.  接下来，您将被要求提供名称和密钥。在`GH_PAGES`内部，并在**密钥**字段中，粘贴从个人访问令牌中保存的密钥。

1.  在输入必要的值后，点击**添加密钥**按钮以保存您的仓库密钥。

如果操作成功，你现在应该在**动作密钥**部分看到**Repository secrets**页面上的`GH_PAGES`密钥。

## 创建一个.yml 文件

现在您已经创建了一个个人访问令牌和一个仓库密钥，我们可以继续创建一个`.yml`文件。

要创建一个`.yml`文件，请按照以下步骤操作：

1.  首先点击仓库导航中的**动作**。

1.  接下来，点击**自行设置工作流程**。

    这将带您到一个页面，如图*图 12.7*所示：

![图 12.7：main.yml](img/B21625_12_007.jpg)

图 12.7：main.yml

如*图 12.7*所示，GitHub 在`.github/workflows`文件夹内为您创建了一个空的`main.yml`文件。您可以直接在 GitHub 中编辑`main.yml`文件，或者提交文件并在 VS Code 中编辑它。现在，我们将简单地编辑 GitHub 中的文件，并开始添加必要的步骤以部署我们的演示应用程序到 GitHub Pages。

### 添加触发器

您首先需要在`.yml`文件中添加一个触发器。在我们的例子中，我们希望我们的`.yml`文件在向 GitHub 仓库的主分支推送更改时被触发。您可以通过将以下内容添加到`.yml`文件中来添加此触发器：

```js
# Add trigger
name: Build and Deploy Script
on:
  push:
    branches:
      - main
```

上述代码片段以注释开始；接下来，我们为我们声明的步骤命名，然后使用`on:`关键字来表示我们正在定义一个触发器，并在其下方定义触发器的配置——在本例中为主分支的推送。`.yml`文件中的缩进很重要，所以请确保每行比上一行多一个制表符。

### 定义要运行的作业和相关步骤

在定义触发器后，您需要定义要运行的作业以及作业的步骤。

在我们的案例中，我们只有一个工作，它是一个由多个步骤组成的`build`工作。首先定义`jobs`和`build`关键字，然后定义构建工作将在其上运行的虚拟机的操作系统：

```js
# Jobs to run
jobs:
  build:
    runs-on: ubuntu-latest
```

在构建工作的`runs-on`配置下面，我们将定义执行构建工作所需的步骤。您需要执行的第一步是检出 GitHub 仓库：

```js
    # Checkout repository
    - name: Checkout Repository
      uses: actions/checkout@v4
      with:
        fetch-depth: 0
```

如您在前面的代码片段中所见，此步骤的缩进从与`runs-on`配置相同的位置开始。我们首先定义一个注释；接下来，我们定义步骤的名称和步骤使用的操作。最后，我们为步骤提供额外的配置 – 在这种情况下，`fetch-depth: 0`，这表示获取 GitHub 仓库中所有分支和标签的历史记录。

在获取 GitHub 仓库之后，您需要使用以下步骤在 Ubuntu 机器上安装 NodeJS：

```js
    # Install NodeJS
    - name: Adding Node.js
      uses: actions/setup-node@v2
      with:
        node-version: 20.1.0
```

在安装 NodeJS 之后，您需要安装`node_modules`。为了加快构建工作，我们将使用`pnpm`而不是`npm`。只需将以下步骤添加到您的`.yml`文件中，以安装 Nx monorepo 的`node_modules`：

```js
    # Setup pnpm
    - uses: pnpm/action-setup@v2
      with:
        version: 8.14.1
    - run: pnpm install --frozen-lockfile
```

我们流程中的下一步将是检查所有受影响的项目：

```js
    # Lint affected projects
    - name: Lint affected
      run: pnpm nx run-many -t lint --base=main --no-cloud
```

如您所见，我们在之前未见过的新命令中添加了一个新标志。我们添加了`–-no-cloud`标志来表示我们不在使用 Nx Cloud；否则，您的管道将尝试连接 Nx Cloud 而失败。接下来，我们将为我们的应用程序添加一个额外的单元测试步骤：

```js
    # Unit testing finance-expenses-registration
    - name: Unit test finance-expenses-registration
      run: pnpm nx run finance-expenses-registration:test –no-cloud
```

为了简化，我们将跳过`finance-expenses-registration`项目。如果您已经在 monorepo 中修复了所有单元测试，您可以将`.yml`文件更改为运行所有受影响项目的测试。在运行`lint`和`test`命令后，我们将使用以下步骤构建`finance-expenses-registration`项目：

```js
    # Build finance-expenses-registration application
    - name: Build Angular App
      run: pnpm nx build finance-expenses-registration --no-cloud --base-href /Effective-Angular/
```

如您可能已经注意到的，我们在`build`命令中添加了`–-base-href`标志。我们需要添加一个基本的`href`属性，因为 GitHub 会将您的应用程序部署到`<GitHub account name>.github.io/<repository name>`。如果您不使用`Effective-Angular`作为您的仓库名称，您需要更改`–-base-href`标志的值为您自己的仓库名称。

在构建仓库之后，您需要在虚拟机上初始化 Git，以便您可以将其部署到 GitHub Pages。只需将以下步骤添加到您的`.yml`文件中：

```js
    # Setup Git on the VM
    - name: Set up Git
      run: |
        git config --global user.email "youremail@gmail.com"
        git config --global user.name "your-git-username"
```

在添加前面的步骤之后，我们可以移动到构建工作内的最后一步，即我们的应用程序部署到 GitHub Pages。

## 将应用程序部署到 GitHub Pages

我们将使用一个名为`angular-cli-ghpages`的`npm`包来部署应用程序。将以下步骤添加到您的`.yml`文件中：

```js
    # Deploy the finance-expenses-registration to GitHub pages
    - name: Deploy to gh pages
      run: |
        npx angular-cli-ghpages --dir=dist/apps/finance/expenses-registration
      env:
        CI: true
        GH_TOKEN: ${{ secrets.GH_PAGES }}
```

如前述代码所示，我们使用`angular-cli-ghpages`运行终端命令来部署应用，并需要提供我们之前创建的`GH_PAGES`令牌，这样 GitHub 就知道我们有必要的授权来进行部署。添加部署步骤后，你已经添加了自动将你的演示应用部署到 GitHub Pages 所需的所有步骤。

你现在可以点击主分支上的`.yml`文件，`.yml`文件将立即被触发。你可以通过导航回你的仓库的**操作**选项卡来查看这一点，在那里你应该看到你的运行中的工作流程。不幸的是，当前的工作流程失败了。

## 工作流程修复失败

GitHub Actions 工作流程由于多个原因失败，所以让我们逐一修复问题。首先，你需要在你的 Nx 单仓库中安装`angular-cli-ghpages`包。只需运行以下命令在你的单仓库中安装该包：

```js
npm i angular-cli-ghpages
```

接下来，我们在 GitHub Actions 工作流程中使用`pnpm`而不是`npm`。因此，我们需要将`pnpm-lock.yaml`文件提交到我们的 GitHub 仓库。要生成此`pnpm-lock.yaml`文件，首先使用以下`npm`命令全局安装`pnpm`：

```js
npm install -g pnpm
```

安装`pnpm`后，你可以开始使用`pnpm`来替代你通常运行的`npm`命令。我建议使用`pnpm`，因为它比`npm`快得多。现在，要生成你的`pnpm-lock.yaml`文件，只需运行以下命令：

```js
pnpm install
```

上述命令类似于`npm install`命令，只是使用`pnpm`而不是`npm`。运行`pnpm install`命令后，你可以提交你的更改并将它们与 GitHub 上的主分支合并以再次触发工作流程。如*图 12.8*所示，在你的仓库的**操作**选项卡下，你现在应该看到两个工作流程运行，第二个运行应该成功：

![图 12.8：GitHub Actions 工作流程运行](img/B21625_12_008.jpg)

图 12.8：GitHub Actions 工作流程运行

现在你已经成功构建，但仍需处理部署步骤。前往你的仓库设置，然后在左侧点击**页面**并添加如图*图 12.9*所示的配置：

![图 12.9：构建和部署](img/B21625_12_009.jpg)

图 12.9：构建和部署

如*图 12.9*所示，配置分支为`gh-pages`后，部署过程将自动开始，几分钟后，你可以在本 GitHub 页面的顶部找到你应用的 URL。每次你的工作流程成功运行时，现在部署将自动完成。当你访问部署的 URL 上的演示应用时，你只会看到你的导航栏，因为应用部署在`<GitHub 账户名>.github.io/<仓库名>` URL 上，我们需要在获取应用内部的语言文件时考虑到仓库名，因此还需要进行最后的修复。

前往 `finance-expenses-registration` 项目的 `app.config.ts` 文件，并在你的 `providers` 数组顶部添加以下提供者：

```js
{ provide: APP_BASE_HREF, useValue: isDevMode() ? '' : '/Effective-Angular/' },
```

接下来，前往你的 `transloco-loader.ts` 文件，并调整它以包含以下内容：

```js
@Injectable({ providedIn: 'root' })
export class TranslocoHttpLoader implements TranslocoLoader {
  private http = inject(HttpClient);
  protected readonly baseHref = inject(APP_BASE_HREF);
  getTranslation(lang: 'en' | 'nl') {
    return this.http.get<Translation>(`${this.baseHref}assets/i18n/${lang}.json`);
  }
}
```

正如你所见，我们已经将 `APP_BASE_HREF` 注入器注入到文件中，并将 `baseHref` 属性包含在用于获取翻译的 URL 中。你现在可以推送更改，并将它们与你的主分支合并以再次触发工作流程。在你的工作流程运行成功后，你可以点击 **部署**，如图 *图 12.10* 所示。

![图 12.10：部署](img/B21625_12_010.jpg)

图 12.10：部署

当你点击 **部署**，如图 *图 12.10* 所示，你将被导航到一个页面，你可以看到你应用程序的所有部署。在同一个页面上，你还可以找到你部署应用程序的 URL 以及它最后一次更新的时间：

![图 12.11：部署列表](img/B21625_12_011.jpg)

图 12.11：部署列表

当你的新部署准备就绪时，如图 *图 12.11* 所示，你可以在浏览器中再次导航到你的部署应用程序，现在你应该看到一个包含表格和翻译值的工作应用程序。由于模拟拦截器在你的生产构建中被禁用，表格中没有数据，也没有你的应用程序可以连接的 API。你现在一切就绪，每次你将更改推送到你的主 GitHub 仓库时，你的应用程序都会自动进行代码检查、测试、构建和部署。

如前所述，这只是一个演示应用程序，还有很多改进的空间。所以，如果你想的话，你可以添加更多页面，为表格和其他你想要的功能创建独立的库组件，并将应用程序创建和转换为真正适合生产目的的东西。你现在拥有了所有使用 Nx 和 Angular 框架开发健壮和可扩展应用程序所需的构建块。

# 摘要

我们已经到达了这本书的结尾，首先，我想感谢你阅读这本书，并祝贺你完成它！在阅读这本书的过程中，你学到了很多，也做了很多。你创建了自己的 Nx monorepo，可以扩展到数百个 Angular 应用程序和库。你知道如何根据个人需求配置 Nx monorepo，并在你的 monorepo 中添加第一个库和应用程序。在创建 monorepo 并添加项目后，你了解了内置和自定义 Nx 生成器以及如何使用它们来生成样板代码并在你的 monorepo 中引入一致性。

接下来，我们转向 Angular，学习了框架的最新发展，以及如何利用强大的功能，如组件通信、Angular 路由和**依赖注入**（**DI**）。在探索框架最新和最强大的功能之后，你学习了管道、指令和动画。你看到了如何创建自定义管道和指令，学习了指令组合，并创建了可重用的动画，使你的应用程序对最终用户更具吸引力。

此外，我们深入研究了 Angular 的`forms`模块，学习了模板驱动、响应式和动态表单。在众多内容中，你学习了表单验证、表单构建器、错误处理以及创建动态表单和表单字段。完成 Angular 表单的学习后，我们回归到理论知识，探索了在 Angular 应用程序中常用的不同最佳实践、约定和设计模式。你学习了某些设计模式和最佳实践，为这本书的下一部分做好了准备，该部分专注于响应式编程和状态管理。你还学习了如何利用 RxJS 和 Signals 创建响应式代码。你了解了可观察流、可管道操作符以及处理嵌套数据流。我们将代码从 RxJS 转换为 Signals，以展示两者之间的差异，并解释了何时使用哪个工具以达到预期的结果。我们还探讨了结合 RxJS 和 Signals 以获得两者的最佳效果。在完成 RxJS 和 Signals 的基础知识后，我们使用这两个工具创建了一个自定义状态管理解决方案，并通过一个外观服务将其与我们的应用程序组件层连接起来。为了完成我们的状态管理之旅，我们将我们的自定义状态管理解决方案转换为使用 NgRx 的实现，这是 Angular 生态系统中最强大和最受欢迎的状态管理解决方案。

当我们完成了响应式编程和状态管理的学习后，我们继续阅读这本书的最后一部分。你学习了如何开发适用于全球各地、各种能力人群的应用程序。你使用了`transloco`为你的应用程序添加本地化和国际化，使得不同国家和语言的人们可以使用你应用并选择他们偏好的格式和语言。接下来，你使用 Jest 和 Cypress 创建了单元测试和端到端测试。

最后，我们通过创建一个使用 GitHub Actions 自动部署流程来将您的应用程序部署到 GitHub Pages 的方法完成了这本书。完成这本书后，您将拥有使用 Nx 和最新的 Angular 技术开发稳健和可扩展的 Angular 应用程序所需的所有知识。您可以通过添加更多页面、将表格抽象为其自己的组件、创建用于费用登记的功能组件，使得页面只需声明一个组件，添加新的功能，如分组费用、规划费用、上传收据等，来继续使用我们创建的演示应用程序。您还可以在 Nx monorepo 中创建多个应用程序，或者从您自己的项目从头开始。无论您想开发什么，现在您都有工具和知识来有效地使用 Angular 开发任何规模的应用程序。
