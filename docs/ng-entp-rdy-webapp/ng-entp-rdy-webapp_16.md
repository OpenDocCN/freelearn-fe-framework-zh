# 附录 B

# Angular 快速参考

这里有一份快速参考表，帮助你熟悉常见的 Angular 语法和 CLI 命令。花点时间复习并熟悉新的 Angular 语法、主要组件、CLI 框架和常见管道。

如果你之前使用过 AngularJS，你会发现这个列表特别有用，因为你需要学习一些旧的语法。

如果你刚开始使用 Angular 或者不是 CLI 命令的粉丝，可以查看 Nx Console 在 [`nx.dev/angular/cli/console`](https://nx.dev/angular/cli/console)，这是一个出色的桌面应用程序，也是 Visual Studio Code 的扩展，它可以为你编写 CLI 参数。话虽如此，我仍然建议你首先熟悉 CLI 命令，并强迫自己使用它们一段时间，这样你就能更好地理解 Nx Console 的工作原理。

绑定，或数据绑定，指的是代码中的变量与在 HTML 模板或其他组件中显示或输入的值之间自动的单一或双向连接：

| **类型** | **语法** | **数据方向** |
| --- | --- | --- |
| 插值属性属性类样式 | `{{expression}} [target]="expression" bind-target="expression"` | 单向，从数据源到视图目标 |
| 事件 | `(target)="statement" on-target="statement"` | 单向，从视图目标到数据源 |
| 双向 | `[(target)]="expression" bindon-target="expression"` | 双向 |

# 内置指令

指令封装了可以应用于 HTML 元素或其他组件的编码行为：

| **名称** | **语法** | **目的** |
| --- | --- | --- |
| 结构指令 | `*ngIf``*ngFor``*ngSwitch` | 控制 HTML 的结构布局以及元素是否被添加或从 DOM 中移除 |
| 属性指令 | `[class] [style] [(model)]` | 监听并修改其他 HTML 元素、属性、属性和组件的行为，例如 CSS 类、HTML 样式和 HTML 表单元素 |

# 常见管道

管道（在 AngularJS 中称为过滤器）修改了数据绑定值在 HTML 模板中的显示方式：

| **名称** | **目的** | **用法** |
| --- | --- | --- |
| 异步 | 管理对可观察对象的订阅，并提供对模板中变量的同步访问 | `someVariable$ &#124; async as someVariable` |
| 日期 | 根据区域规则格式化日期 | `{{date_value &#124; date[:format]}}` |
| 文本转换 | 将文本转换为大写、小写或标题格式 | `{{value &#124; uppercase}}``{{value &#124; lowercase}}``{{value &#124; titlecase }}` |
| 小数 | 根据区域规则格式化数字 | `{{number &#124; number[:digitInfo]}}` |
| 百分比 | 根据区域规则格式化数字为百分比 | `{{number &#124; percent[:digitInfo]}}` |
| 货币 | 根据区域规则使用货币代码和符号格式化数字为货币 | `{{number &#124; currency [:currencyCode [:symbolDisplay [:digitInfo]]]}}` |

# 启动命令，主要组件和 CLI 框架

启动命令帮助生成新项目或添加依赖。Angular CLI 命令通过自动生成样板代码轻松创建主要组件。要查看完整命令列表，请访问 [`github.com/angular/angular-cli/wiki`](https://github.com/angular/angular-cli/wiki)。

## 启动命令

这里是最基本的命令，您可能会随着时间的推移记住并频繁使用。请记住，不要像在 *第三章*，*创建基本的 Angular 应用程序* 中所述那样全局安装 `@angular/cli`：

| **名称** | **用途** | **CLI 命令** |
| --- | --- | --- |
| 新建 | 创建一个新的 Angular 应用程序，包含初始化的 Git 仓库、`package.json`，已配置路由并启用 Ivy。从父文件夹运行。 | `npx @angular/cli new project-name --routing` |
| 更新 | 更新 Angular、RxJS 和 Angular Material 依赖项。如有必要，重写代码以保持兼容性。 | `npx ng update` |
| 安装 Angular Material | 安装并配置 Angular Material 依赖项。 | `npx ng add @angular/material` |

## 主要组件模板

在您的日常工作流程中使用以下命令添加新组件、服务和其他主要组件到您的 Angular 应用程序中。这些命令将为您节省大量时间并帮助您避免简单的配置错误：

| **名称** | **用途** | **CLI 命令** |
| --- | --- | --- |
| 模块 | 创建一个新的 `@NgModule` 类。使用 `-- routing` 为子模块添加路由。可选地，使用 `--module` 将新模块导入父模块。 | `ng g module my-module` `ng g m my-module` |
| 组件 | 创建一个新的 `@Component` 类。使用 `-- module` 指定父模块。可选地，使用 `--flat` 跳过目录创建，`-t` 用于内联模板，`-s` 用于内联样式。 | `ng g component my-component` `ng g c my-component` |
| 指令 | 创建一个新的 `@Directive` 类。可选地，使用 `--module` 将指令作用域限定为指定的子模块。 | `ng g directive my-directive` `ng g d my-directive` |
| 管道 | 创建一个新的 `@Pipe` 类。可选地，使用 `--module` 将管道作用域限定为指定的子模块。 | `ng g pipe my-pipe` `ng g p my-pipe` |
| 服务 | 创建一个新的 `@Injectable` 类。使用 `--module` 为指定的子模块提供服务。服务不会自动导入到模块中。可选地使用 `--flat false` 在目录下创建服务。 | `ng g service my-service` `ng g s my-service` |
| 守卫 | 创建一个新的 `@Injectable` 类，该类实现了路由生命周期钩子 `CanActivate`。使用 `--module` 为指定的子模块提供守卫。守卫不会自动导入到模块中。 | `ng g guard my-guard` `ng g g my-guard` |

为了正确地在一个自定义模块（如`my-module`）下搭建一些之前列出的组件，你可以在你打算生成的名称之前添加模块名称，例如，`ng g c my-module/my-new-component`。Angular CLI 将正确连接并将新组件放置在`my-module`文件夹下。

## TypeScript 搭建

如果你不太熟悉 TypeScript 语法，这些 TypeScript 特定的搭建将帮助你创建类、接口和枚举，这样你就可以利用面向对象编程原则来减少代码重复，并在类中而不是在组件中实现代码行为，如计算属性：

| **名称** | **用途** | **CLI 命令** |
| --- | --- | --- |
| 类 | 创建一个基本类 | `ng g class my-class` |
| 接口 | 创建一个基本接口 | `ng g interface my-interface` |
| 枚举 | 创建一个基本枚举 | `ng g enum my-enum` |

# 常见的 RxJS 函数/操作符

为了成为一名有效的 Angular 开发者，你需要成为 RxJS 的大师。以下是一些最常见和有用的 RxJS 操作符，供快速参考：

## 函数

| **名称** | **用途** |
| --- | --- |
| `pipe` | 接受一个或多个可观察对象作为输入，并生成一个可观察对象作为输出，允许你构建自定义数据流。 |
| `subscribe` | 必须激活一个可观察对象。从`subscribe`操作中提取可观察数据流的值是一种反模式。可以使用异步管道或`tap`函数来检查或使用当前值。 |
| `unsubscribe` | 释放资源并取消可观察对象的执行。不取消订阅可能导致性能问题和内存泄漏。使用异步管道或`SubSink`库来管理订阅。 |

## 操作符

| **名称** | **用途** |
| --- | --- |
| `of` | 将提供的值转换为可观察序列。对于将同步代码集成到可观察数据流中非常有用。 |
| `from` | 从数组、可迭代对象或承诺创建一个可观察对象。 |
| `map` | 允许你遍历可观察对象发出的每个值。 |
| `merge` | 创建一个输出可观察对象，同时并发地发出所有给定输入可观察对象的所有值。对于基于多个可观察对象触发操作非常有用。 |
| `combineLatest` | 将多个可观察对象中的值与每个可观察对象的最新值组合。当与`merge`操作符一起使用时非常有用。 |
| `filter` | 过滤数据流中的值。用于忽略 null 值或仅在满足某些条件时执行管道的其余部分。 |
| `concat` | 顺序地从一个或多个可观察对象中发出值。对于同步多个操作非常有用。类似`concatMap`的变体也可以扁平化可观察对象，这对于遍历集合的值非常有用。 |
| `take` | 给定一个计数，在消耗指定次数后自动完成可观察对象。 |
| `catchError` | 捕获可观察对象上的错误，通过返回一个新的可观察对象或抛出一个错误来处理。 |
| `scan` | 使用累加器函数，它可以增量地处理数据。也就是说，随着数字的增加，可以得到一个运行的小计。这对于需要更新的长时间运行的操作非常有用。 |

感谢 Jan-Niklas Wortmann 对本节的审阅。在 Twitter 上关注他：`@niklas_wortmann`。

# 进一步阅读

+   *绑定*，[`angular.io/guide/template-syntax#binding-syntax-an-overview`](https://angular.io/guide/template-syntax#binding-syntax-an-overview)

+   *结构指令*，[`angular.io/guide/structural-directives`](https://angular.io/guide/structural-directives)

+   *属性指令*，[`angular.io/guide/template-syntax#built-in-attribute-directives`](https://angular.io/guide/template-syntax#built-in-attribute-directives)

+   *管道*，[`angular.io/guide/pipes`](https://angular.io/guide/pipes)

+   *CLI 命令*，[`angular.io/cli`](https://angular.io/cli)

+   *永远学习 RxJS 的 switchMap、mergeMap、concatMap 和 exhaustMap！*，[`medium.com/@shairez/a-super-ninja-trick-to-learn-rxjss-switchmap-mergemap-concatmap-and-exhaustmap-forever-88e178a75f1b`](https://medium.com/@shairez/a-super-ninja-trick-to-learn-rxjss-switchmap-mergemap-concatmap-and-exhaustmap-forever-88e178a75f1b)

+   *JavaScript 的响应式扩展库*，[`rxjs.dev`](https://rxjs.dev)

+   *这是我们的 RxJS 文档写作之处。它旨在成为所有对 RxJS 感兴趣的人的聚集地*，[`dev.to/rxjs`](https://dev.to/rxjs)
