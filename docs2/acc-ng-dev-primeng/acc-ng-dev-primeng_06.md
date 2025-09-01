# *第十章*：使用 Angular 兼容性编译器

Angular Ivy 替换了之前一代的 Angular 编译器和渲染运行时，即 Angular View Engine。支持 View Engine 运行时的最后一个版本是 Angular 版本 11.2。

在本章中，我们将学习 npm 上 View Engine 编译的 Angular 包与您的 Angular Ivy 应用程序之间的桥梁，即 **Angular 兼容性编译器**（**ngcc**）。

本章涵盖以下主题：

+   介绍 Angular 兼容性编译器

+   使用 Angular 兼容性编译器

+   在您的 CI/CD 工作流程中改进 Angular 兼容性编译器

如果您的 Angular Ivy 应用程序从包注册表中消费 View Engine 编译的库，您必须使用 Angular 兼容性编译器。在了解本章涵盖的主题之后，您将了解您本地开发工作流程中正在发生的事情，并能够微调您的 CI/CD 工作流程中的 Angular 兼容性编译器。

# 技术要求

对于本章中演示的技术，您的应用程序至少需要以下要求：

+   Angular Ivy 版本 11.1

+   TypeScript 版本 4.0

# 介绍 Angular 兼容性编译器

Angular 库的源代码在发布到 npm 等包注册表之前进行编译。直到 Angular 版本 12.0，无法使用部分 Angular Ivy 编译来编译 Angular 库；它们必须使用 View Engine 编译器进行编译。作为过渡期的一部分，Angular 使用 Angular 兼容性编译器，允许 Angular Ivy 应用程序使用使用 View Engine 编译器编译并发布到包注册表的库。

截至 Angular 版本 12.2，Angular 兼容性编译器仍然作为 Angular CLI 的一部分包含在内，这意味着我们的 Angular Ivy 应用程序可以消费使用 View Engine 或 Angular Ivy 编译器编译的库。

在 Angular CLI 版本 12.0 中，引入了 Angular 库的局部 Ivy 编译。简而言之，它编译了所有 Angular 特定代码，除了组件模板。然而，局部 Ivy 编译破坏了库的向后兼容性，因为消费者也必须至少使用 Angular CLI 版本 12.0。在此发布之后的时期，我们将看到从 View Engine 编译的 Angular 库向部分 Ivy 编译的 Angular 库的过渡。

好消息是，我们不需要在我们的 Angular Ivy 应用程序中进行任何更改，只需保持 Angular 包更新即可。一旦我们至少有 Angular 12.0，我们的应用程序就通过 Angular 框架的一个内部部分——Angular Linker，支持部分 Ivy 编译的 Angular 库。

Angular Linker 是 Angular 兼容性编译器的替代品，它在将部分 Ivy 编译的 Angular 库包包含到我们的应用程序编译之前，将其转换为完全 Ivy 编译的库包。

因此，Angular 兼容性编译器将在撰写本文时未知的 Angular 版本中移除，但晚于版本 12.2。当这种情况发生时，我们的 Angular 应用程序将只能使用部分 Ivy 编译的 Angular 库。

现在您已经了解了 Angular 兼容性编译器和 Angular 链接器是什么，以及为什么需要它们，在下一节中，我们将讨论如何使用 Angular 兼容性编译器。

# 使用 Angular 兼容性编译器

在某些 Angular 9 版本发布中，我们必须在构建、测试或提供 Angular Ivy 应用程序之前手动运行 Angular 兼容性编译器。在后续版本中，这发生了变化，使得 Angular CLI 根据需要触发 Angular 兼容性编译器。

仍然可以手动运行 Angular 兼容性编译器。实际上，这允许我们将其微调到最佳编译速度。

在以下操作之前，Angular 兼容性编译器需要至少运行一次：

+   启动开发服务器

+   执行自动化测试

+   构建我们的应用程序

每次我们安装 Angular 库的新版本或从包注册表中安装的附加 Angular 库时，我们必须再次运行 Angular 兼容性编译器。

考虑将 Angular 兼容性编译器作为 Git 仓库的`postinstall`钩子的一部分运行。当使用此技术时，我们不必等待下一次执行前述列表中提到的任何操作。当 Angular 兼容性编译器正在运行时，我们可以自由更改我们的源代码。

或者，使用下一节中描述的`--target`选项，即*Angular 兼容性编译器选项*部分。

## Angular 兼容性编译器选项

Angular 兼容性编译器捆绑了一个名为`ngcc`的可执行文件。当运行此命令时，我们可以传递以下选项：

+   `--create-ivy-entry-points`

    在每个 Angular 库包目录内创建一个`__ivy_ngcc_`子目录。在此目录内，将创建一个以输出包格式命名的另一个子目录，例如，`fesm2015`。在包格式文件夹内，将放置 Ivy 编译的包和源映射。如果不传递此选项，则原始包将被覆盖。

+   `--first-only`

    当此选项与`--properties`结合使用时，Angular 兼容性编译器将根据`--properties`指定的包属性名称顺序，仅编译库包中识别的第一个模块格式。

+   `--properties <package-property-names>`

    此选项指定了使用 Angular 兼容性编译器编译的可接受的库包格式。包属性名称指的是库包的`package.json`模块声明中的属性。

    示例：`--properties es2015 browser module main`

+   `--target <package-name>`

    此选项仅编译指定的包。

    示例：`--target @angular/material/button`

+   `--tsconfig <tsconfig-path>`

    您可以使用此选项与 `--use-program-dependencies` 一起使用，以针对您的 Angular 工作空间中的特定项目。

    示例：`--tsconfig projects/music-app/tsconfig.app.json`

+   `--use-program-dependencies`

    您可以使用此选项根据您的 Angular 工作空间或项目中的源代码来决定您想要使用 Angular 兼容编译器编译哪些库包。

还有一些其他选项，但它们主要用于特殊情况。

手动运行 Angular 兼容编译器，例如，在修改或添加包依赖项之后，可以让我们优化编译速度。当我们手动触发整个工作空间的编译时，我们通常应该使用以下命令：

```js
ngcc --first-only --properties es2015 module fesm2015 esm2015 browser main --create-ivy-entry-points
```

`--first-only` 选项确保仅使用 `esm2015` 捆绑格式将一个包格式编译成与 Angular Ivy 兼容的包捆绑。`--properties` 选项列出首选的包格式。研究表明，`es2015` 格式通常是编译从视图引擎兼容捆绑到 Angular Ivy 兼容捆绑的最快包格式，其次是 `module` 格式。最后，`--create-ivy-entry-points` 选项通常比原地捆绑替换更快。

重要提示

如果您正在使用 Angular 版本 9.0 或 11.1，请考虑省略 `--create-ivy-entry-points` 选项，以使用原地捆绑替换。研究发现，在这些特定版本中，此选项略快。

还可以考虑添加 `--use-program-dependencies` 选项，仅编译应用程序导入的包。当使用此选项时，我们必须在应用程序中首次使用包时运行 Angular 兼容编译器。

`--use-program-dependencies` 选项在使用 Angular CDK 和 Angular Material 时特别有用，因为它们有许多子包，这些子包都是单独编译的。此外，默认情况下，每个 Angular CDK 和 Angular Material 子包都会被编译，而不仅仅是应用程序使用的那些。这显著影响了编译速度。

重要提示

本章中列出的 `ngcc` 命令旨在用于 `package.json` 中 `scripts` 属性预定义的命令列表中。要从终端运行它们，请在前面加上 `npx`，例如，`npx ngcc --create-ivy-entry-points`。

这就是关于您本地开发工作流程中的选项和常见技术的全部内容。在下一节中，您将学习如何优化 CI/CD 工作流程中的 Angular 兼容编译器以提高速度。

# 在 CI/CD 工作流程中改进 Angular 兼容编译器

如您从 Angular 兼容性编译器支持的一些选项的描述中可以看出，它维护您应用程序的`node_modules`文件夹中的文件。根据您的 CI 环境，缓存和恢复整个`node_modules`文件夹可能太慢。在这种情况下，缓存您的包管理器的包缓存文件夹。

可能你的 CI/CD 工作流程中根本未启用缓存。在两种情况下，我们必须在每次 CI/CD 工作流程运行中运行 Angular 兼容性编译器。它从它管理的文件开始从头开始。

对于这个用例，我们使用*Angular 兼容性编译器选项*章节中描述的指南。我们使用以下`postinstall`钩子来运行`ngcc`，在所有参数选项组合中，这是整体最快的组合：

```js
ngcc --first-only --properties es2015 module fesm2015 esm2015 browser main --create-ivy-entry-points
```

这只编译单个包格式到 Angular Ivy 包格式，并优先考虑编译速度整体最快的格式。包文件被编译到由 Angular 兼容性编译器管理的子文件夹中的新文件，而不是替换现有的、由 View Engine 编译的包文件。

重要提示

如果你使用的是 Angular 版本 9.0 或 11.1，考虑不使用`--create-ivy-entry-points`选项。研究表明，在这些版本中，原地 Ivy 编译更快。

将 Angular 兼容性编译器作为一个单独的步骤运行而不是按需运行的好处是，它可以像我们刚才做的那样进行微调。此外，它允许我们在排除 View Engine 到 Angular Ivy 编译时间的同时跟踪我们在测试或构建我们的应用上花费的时间。

## 针对一个 monorepo 工作区中的单个应用

如同在*Angular 兼容性编译器选项*章节中讨论的那样，Angular CDK 和 Angular Material 是 Angular 库包的例子，它们包含许多子包。如果我们有一个包含几个 Angular 应用的 monorepo 工作区，可能只有其中一些应用使用了 Angular CDK 或 Angular Material。此外，任何一个这样的应用很可能并没有使用 Angular CDK 或 Angular Material 的每一个子包。

如果我们有一个针对单个应用的 CI 或 CD 作业，例如，针对特定应用的测试或构建作业，我们可以考虑这一点。想象一下，我们有一个包含两个 Angular 应用的 monorepo 工作区，一个使用 Bootstrap UI 组件库，另一个使用 Angular Material。在针对使用 Bootstrap 的应用的测试或构建作业中，我们在安装包依赖项的步骤之后使用以下命令：

```js
npx ngcc --first-only --properties es2015 module fesm2015 esm2015 browser main --create-ivy-entry-points --tsconfig projects/bootstrap-app/tsconfig.app.json --use-program-dependencies
```

我们通过传递其 TypeScript 配置文件的路径到`--tsconfig`选项来针对 Bootstrap 应用，最后我们添加了`--use-program-dependencies`选项。

这将在我们的 CI/CD 作业中节省大量的计算时间，因为我们的 CI 服务器将不需要编译 Angular Material 的任何子包。

即使是使用 Angular Material 的应用程序，我们也可以使用类似的命令来节省时间，因为它只会编译由我们的应用程序导入的 Angular Material 子包，而不是所有子包。以下是一个示例命令：

```js
npx ngcc --first-only --properties es2015 module fesm2015 esm2015 browser main --create-ivy-entry-points --tsconfig projects/material-app/tsconfig.app.json --use-program-dependencies
```

在前面的命令中，我们更改了传递给`--tsconfig`选项的路径。

现在，你已经学会了 Angular 应用程序 CI/CD 工作流程中最常见的优化技术。

# 摘要

在本章中，我们首先讨论了 Angular 兼容性编译器在 Angular 库包仍然使用 Angular 视图引擎编译器编译的过渡阶段是一个需要的工具。Angular 兼容性编译器将这些包捆绑编译成 Angular Ivy 格式，以便它们可以被我们的 Angular Ivy 应用程序使用。

此外，我们还讨论了 Angular 的最新版本如何通过 Angular Linker 支持部分 Ivy 编译的 Angular 库包，这最终将完全取代 Angular 兼容性编译器。

在回顾了依赖于 Angular 兼容性编译器的用例之后，我们简要讨论了`ngcc`命令行工具最有用的选项。随后，我们通过使用这些选项介绍了常见的优化技术。

最后，本章通过考虑如何优化 Angular 兼容性编译器在 CI/CD 工作流程中的速度来结束。我们讨论了针对几个特定和常见用例的解决方案。

现在，你知道如何利用 Angular 兼容性编译器，也知道何时以及如何优化它。

在下一章中，你将指导如何将现有的 Angular 应用程序从视图引擎迁移到 Ivy。你将了解自动和手动迁移，以及从视图引擎迁移到 Angular Ivy 时的其他考虑因素。
