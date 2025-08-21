# 第十四章：SystemJS

SystemJS 是一个模块加载器，可以在以下 GitHub 链接中找到[`github.com/SystemJS/SystemJS`](https://github.com/systemjs/systemjs)。

它是建立在原始的 ES6 模块加载器 polyfill 之上的。它旨在解决在浏览器中加载模块的基本问题，目前除非浏览器得到一些来自库的帮助，否则是行不通的，比如 SystemJS。

在这个附录中，我们将涵盖：

+   SystemJS 本身

+   一个实用的 SystemJS 示例，使用 Angular 的快速启动存储库

# SystemJS 介绍

SystemJS 从上到下加载文件，然后从下到上实例化。不过，这意味着什么呢？这意味着如果你有一个名为`Module1`的文件需要加载，它依赖于`Module2`，那么`Module1`将首先被加载。加载完毕后，我们有了执行代码的部分，它采取相反的方向。在这种情况下，它将执行`Module2`，以获得它的一个实例并将其传递给`Module1`。

SystemJS 的典型用法如下：

```ts
System.import('./file.js').then( file => // do something )
```

SystemJS 在处理脚本时会进行不同的步骤：

1.  **规范化文件路径**：路径可以是相对的、绝对的和别名的，SystemJS 将所有这些转换为一个格式

1.  **XHR 或提供它**：当一个模块被要求时，可能会发生两种情况；如果它已经在之前被预加载过，它将从内部注册表中加载，或者会为它发出 XHR 请求

1.  **准备好使用**：在最后一步中，模块将被执行，添加到注册表中，并解析其承诺

# 使用快速启动存储库快速入门

要开始使用`quickstart`存储库，你需要使用以下命令获取该项目的副本：

```ts
git clone https://github.com/angular/quickstart.git quickstart
```

这将从 GitHub 下载所有所需的文件，并将它们放在一个名为`quickstart`的目录中。现在，进入该目录：

```ts
cd quickstart
```

该项目将指定一堆它依赖的库。你需要安装这些库。这可以通过输入以下命令来完成：

```ts
npm install
```

最后，我们需要提供应用程序，也就是在浏览器中显示它。这可以通过输入以下命令来完成：

```ts
npm start
```

值得一提的是，该存储库使用 SystemJS 作为模块加载器和引导我们的 Angular 应用程序。

# 理解各个部分

获得 GitHub 存储库或使用脚手架工具是很好的。您可以快速开始，并且几乎立即感到高效。不过，这里有一个*但*。如果出了问题，我们该如何解决？为了能够做到这一点，我们需要更好地了解底层发生了什么。

# 使用 SystemJS 设置任何 Angular 项目的基本概念

这些概念构成了您的应用程序的核心。它们将出现在每个项目中：

+   起始网页

+   **Node 包管理器**（**npm**）

+   SystemJS

+   TypeScript 设置和 TypeScript 定义文件

+   Linting

让我们讨论这些概念，以介绍设置。

所有 Web 项目都需要一个起始网页。

Node.js 是服务器端的 JavaScript。在*Angular 构建*的上下文中，Node.js 被用来引入许多库（来自 npm）来帮助处理诸如打包、测试和最小化等任务。至少要对如何使用 Node.js 及其生态系统有一定了解是至关重要的。关于这一点的更详细描述将在接下来的小节中进行。

至于 SystemJS，它是一个模块打包工具。JavaScript 项目不再只是写在一个文件中；有时候，它们是由成千上万个文件组成的。这些文件之间的关系是通过使用模块系统来实现的，而 SystemJS 是众多模块打包工具之一。Angular 团队选择了 TypeScript 作为编写 Angular 应用程序的*通用语言*，这意味着我们需要正确设置 TypeScript 来编译它，并确保 TypeScript 知道如何使用 ES5 编写的依赖库。

最后，linting 是确保我们在编写代码时遵循最佳实践的过程，既为了一致性，也为了避免错误。

现在，让我们详细讨论这些概念。

# 起始网页 - index.html

这个文件的目的是呈现给 Web 服务器，最终将其渲染成一个应用程序。它将包含一些标记，但更重要的是我们应用程序运行所需的`script`标签。

`index.html`也包含了许多`script`标签。这些`script`标签是项目运行所需的。

# 核心文件 - Angular 依赖的文件

许多浏览器缺乏 ES2015 带来的一些功能。为了解决这个问题，我们可以通过添加一些称为 polyfill 的东西来增强我们的浏览器，以弥补这些缺失的功能。除了利用现代 JavaScript 的 polyfill 之外，Angular 还使用了一种全新的方式来检测应用程序中的变化，这是通过使用`zone.js`库来实现的。最后，Angular 团队决定使用 Rxjs 来处理 HTTP 请求。他们甚至进一步将其集成到许多其他方面，比如处理表单和路由。这三个东西构成了我们需要导入的核心功能，以使我们的应用程序正常工作。

# core-js

这个文件将 ES2015 的功能带给了 ES5 浏览器。由于您将使用相当多的 ES2015 构造，这对一切都是必要的：

```ts
<script scr="node_modules/core-js/client/shim.min.js"></script>
```

# zone.js

这个文件被 Angular 用来处理变化检测和数据绑定，没有这个库什么都不会工作：

```ts
<script scr="node_modules/zone.js/dist/zone.js"></script>
```

# rxjs

RxJS 是 Angular 大量使用的异步库，用于处理从 HTTP 请求到表单和路由的所有内容。

# SystemJS - 我们的模块加载器

SystemJS 是您用来处理模块加载的库，由两个链接组成：

+   SystemJS 核心文件

+   SystemJS 配置文件

前者是 SystemJS 运行所需的，后者是您指示 SystemJS 加载哪些文件以及找到您的应用程序和相关资产的位置。

这指出了核心 SystemJS 文件：

```ts
<script src="node_modules/SystemJS/dist/system.scr.js"></script>
```

这指出了如何配置 SystemJS。您需要调用这个文件`SystemJS.config.js`：

```ts
<script src="SystemJS.config.js"></script>
```

查看`SystemJS.config.js`显示了以下配置调用：

```ts
System.config({
 paths: {
 // paths serve as alias
 'npm:': 'node_modules/'
 },
 // map tells the System loader where to look for things
 map: {
 // our app is within the app folder
 'app': 'app',
 // angular bundles
 '@angular/core': 'npm:@angular/core/bundles/core.umd.js',
 '@angular/common': 'npm:@angular/common/bundles/common.umd.js',
 '@angular/compiler': 'npm:@angular/compiler/bundles/compiler.umd.js',
 '@angular/platform-browser': 'npm:@angular/platform-browser/bundles/platform-browser.umd.js',
 '@angular/platform-browser-dynamic': 'npm:@angular/platform-browser-dynamic/bundles/platform-browser-dynamic.umd.js',
 '@angular/http': 'npm:@angular/http/bundles/http.umd.js',
 '@angular/router': 'npm:@angular/router/bundles/router.umd.js',
 '@angular/forms': 'npm:@angular/forms/bundles/forms.umd.js',
 // other libraries
 'rxjs': 'npm:rxjs',
 'angular-in-memory-web-api': 'npm:angular-in-memory-web-api/bundles/in-memory-web-api.umd.js'
 },
 // packages tells the System loader how to load when no filename and/or no extension
 packages: {
 app: {
 defaultExtension: 'js',
 meta: {
 './*.js': {
 loader: 'SystemJS-angular-loader.js'
 }
 }
 },
 rxjs: {
 defaultExtension: 'js'
 }
 }
});
```

看起来相当长而令人生畏，但让我们分解不同的部分，如下所示：

+   `paths`：系统文件的别名位置。值得注意的是，我们通过输入以下内容来创建对`node_modules`的别名：

```ts
 path: { 'npm:': 'node_modules/'}
```

这将在以后为我们服务，当我们需要提及应用程序需要的所有库时。

+   `map`：这是我们需要告诉 SystemJS 它可以在哪里找到所有部分的地方。

以下代码片段显示了以下内容：

+   +   找到我们的应用程序的位置，名为 app 的键

+   找到 Angular 文件的位置，名为`@angular/...`

+   找到支持库的位置，这些库包括 angular 库（框架分成许多较小的库）以及上一节中提到的核心库

```ts
 map : {
 app : app,  // instruct that our app can be found in the app directory
 '@angular/core': 'npm:@angular/core/bundles/core.umd.js'
 // supporting libraries omitted for brevity
 }
```

在这里，我们可以看到我们在引用`@angular/core`时使用了别名`npm`，这意味着以下内容：

```ts
 'npm: @angular/core/bundles/core.umd.js'
```

使用以下完整路径：

```ts
 'node_modules/@angular/core/bundles/core.umd.js'
```

+   `packages`: 这是配置文件的最后部分。它指示应该首先加载应用程序文件夹中的哪些文件，也提供了`defaultExtension`。

# Node.js 设置 - package.json

`package.json`是 Node.js 项目的描述文件。它包括元数据信息，如`name`，`author`和`description`，但它还包含一个`script`属性，允许我们运行执行工作的脚本，例如：

+   创建一个 bundle

+   运行测试

+   执行 linting

要运行`script`标签中的命令之一，您需要输入：

```ts
npm run <command>
```

您的应用程序将依赖于许多库来构建和运行。列在`dependencies`或`devDependencies`中的库将通过您输入`npm install`来下载。

在`dependencies`和`devDependencies`中列出哪些库应该有一个语义上的区别。最终将帮助应用程序运行的任何内容都将最终出现在`dependencies`中，包括 Angular 库以及支持库。`devDependencies`有些不同；这里放置的内容更多是支持性质的。例如 TypeScript，Linter，测试库以及用于处理 CSS 和创建 bundle 本身的不同工具。

至于`dependencies`中的 angular 部分，这些都是纯 Angular 依赖项，用`@angular`表示：

+   `@angular/common`

+   `@angular/compiler`

+   `@angular/core`

+   `@angular/forms`

+   `@angular/http`

+   `@angular/platform-browser`

+   `@angular/platform-browser-dynamic`

+   `@angular/router`

其余的依赖项是我们在本节中提到的*Angular 依赖的核心文件*列表：

+   `core-js`

+   `reflect-metadata.js`

+   `rxjs`

+   `system.js`

+   `zone.js`

# TypeScript 设置

`tsconfig.json`是 TypeScript 编译器将处理并确定编译应该如何发生的文件。

以下是基本设置：

```ts
target: 'es5',
module : 'commonjs',
emitDecoratorMetadata : true, // needed for compilation to work
experimentalDecorators : true // needed for compilation to work
```

如前面的代码注释中所述，`emitDecoratorMetadata`和`experimentalDecorators`需要设置为`true`，因为 Angular 大量使用这些功能。

# 摘要

本附录介绍了 SystemJS，并描述了它如何处理文件以及以何种顺序处理文件，因为它是一个模块加载器。随后，介绍了官方的快速启动存储库。然后，我们看了 SystemJS 需要的不同部分或者它需要解决的问题。在这一点上，我们已经准备好深入了解如何使用 SystemJS 来设置 Angular 应用程序。我们还看了 Angular 框架需要 SystemJS 加载的核心部分以及顺序。离开这个附录，我们现在对 SystemJS 解决的问题有了更清晰的理解，以及如何使用它来设置 Angular 应用程序。值得注意的是，大多数 Angular 应用程序都在使用 Angular CLI 或 webpack，但这绝对是一个将在一段时间内得到支持的好选择。
