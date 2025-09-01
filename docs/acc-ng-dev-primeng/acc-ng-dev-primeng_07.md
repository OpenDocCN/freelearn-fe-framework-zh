# 第八章：*第十一章*：从 View Engine 迁移到 Ivy 的 Angular 应用程序

每年都会发布几个 Angular 功能版本。更新我们的 Angular 应用程序需要了解 Angular 更新过程，尤其是在从 Angular View Engine 迁移到 Angular Ivy 时，因为有很多差异，其中大部分由自动化的 Angular 迁移管理。

在本章中，你将了解更新 Angular 应用程序所需的步骤，按照*Angular 更新指南*的说明，如何管理 Angular 的第三方依赖项，`ng update`命令最有用的参数，最重要的自动化 Angular Ivy 迁移如何改变我们的应用程序，以及如何应用推荐的但可选的自动化和手动 Angular Ivy 迁移。

在本章中，我们将涵盖以下主题：

+   学习 Angular 更新过程

+   执行自动化的 Angular Ivy 迁移

+   执行手动 Angular Ivy 迁移

# 技术要求

本章讨论的迁移适用于以下或更高版本的应用程序：

+   Angular Ivy 版本 12.1

+   TypeScript 版本 4.2

确保你已全局安装了 Angular CLI 的最新版本，以便你可以在终端中运行`ng update`命令。

# 学习 Angular 更新过程

Angular CLI 为我们提供了一个结构化的方法来更新应用程序的 Angular 特定部分。Angular schematics 的一种类型是**迁移**，它修改我们的应用程序代码以符合破坏性更改。Angular 的主要和次要版本发布通常伴随着迁移 schematics。

建议按顺序逐个主要版本更新过程。例如，如果我们的应用程序当前正在使用 Angular View Engine 版本 8.2，我们将其更新到 Angular Ivy 版本 9.1，并在我们采取下一步更新从 Angular 版本 9.1 到版本 10.2 之前，验证所有方面是否按预期运行，依此类推，直到我们达到计划更新的 Angular 发布版本。

我们一次执行的更新步骤越少，当事情没有按计划进行时，就越容易识别出了什么问题。

在本节中，我们将首先了解 Angular 更新指南，这是一个官方网络应用程序，列出了逐步说明。之后，我们将讨论 Angular 的第三方依赖项及其发布如何影响我们的 Angular 应用程序。

## Angular 更新指南

Angular 更新过程的一个重要工具是 Angular 更新指南。位于[`update.angular.io`](https://update.angular.io)，这个网络应用程序提供了更新我们的 Angular 应用程序的逐步说明。

要使用 Angular 更新指南，我们首先选择以下内容：

+   我们目前正在使用哪个 Angular 版本

+   我们想要更新到哪个 Angular 版本

+   我们应用程序的复杂性

+   我们的应用程序是否是使用`ngUpgrade`的混合 AngularJS 和 Angular 应用程序

+   我们是否正在使用 Angular Material

即使我们在赶时间，我们也应该选择**高级**作为我们的**应用复杂度**，并遍历所有可用的指令，以确保我们不遗漏任何推荐的迁移步骤。

在选择与我们的应用程序匹配的选项后，我们将会看到一个包含以下部分的指令清单：

+   **在更新前**

+   **在更新期间**

+   **更新后**

并非总是清楚哪些指令被列在**在更新期间**或**更新后**部分。为了有一个愉快的更新过程，我们在遵循**在更新期间**部分的指令之前，确保遵循**在更新前**部分的指令。

**在更新期间**部分的指令必须按照列出的顺序执行，因为更新和迁移命令通常相互依赖。

`ng update`命令中的指令，但在 Angular 更新指南中未列出。同样，一些推荐的自动和手动迁移在 Angular 文档中列出，但在 Angular 更新指南中未列出。

## 管理 Angular 依赖

除了官方的 Angular 包之外，Angular 只有少数依赖。以下包依赖在 Angular 的`package.json`文件中列出：

+   **RxJS**

+   **tslib**

+   **Zone.js**

从历史上看，这些包依赖的版本由 Angular 更新过程管理。然而，对于破坏性变更的迁移并不总是可用。例如，RxJS 没有计划从 6.x 版本更新到 7.x 版本的迁移。

### Zone.js

在撰写本文时，Zone.js 仍处于预发布版本。每个小版本预发布都包含破坏性变更。通常，对于我们的 Angular 应用程序，迁移不是必需的，因为我们没有直接使用 Zone.js。相反，`NgZone` API 包装了 Zone.js。

然而，我们在几个应用程序文件中导入了 Zone.js，Zone.js 版本 0.11.1 改变了其导入路径。Angular 版本 11 提供了一个自动迁移来更新 Zone.js。

### TypeScript

TypeScript 不遵循语义版本控制。每个小版本都包含破坏性变更。没有可用的自动迁移，所以如果我们的应用程序在更新 Angular 后输出编译错误，我们必须参考 TypeScript 官方公告博客文章的**破坏性变更**部分。

### RxJS

可以通过检查`@angular/core`的`package.json`文件的`dependencies`属性来读取 Angular 官方支持的 RxJS 版本。Angular 版本 9.0–10.0 官方支持 RxJS 版本 6.5 和 6.6，而 Angular 版本 10.1–12.1 仅对 RxJS 版本 6.6 提供官方支持。Angular 版本 12.2 可选择支持 RxJS 版本 7.0 及以后的次要版本。

### Node.js

Angular CLI 通常对两个主要版本的 Node.js 有官方支持。不稳定的（奇数）主要版本 Node.js 发布版不受 Angular CLI 的官方支持。Angular CLI 版本 9.0–11.2 对 Node.js 10.13 和 12.11 或更高版本的次要版本有官方支持。Angular 版本 12 移除了对 Node.js 10 的支持，但除了对 Node.js 12.14 或更高版本的次要版本外，还增加了对 Node.js 14.15 或更高版本的次要版本的官方支持。

在本节中，我们学习了 Angular 更新指南以及如何管理 Angular 的依赖。在下一节中，我们将学习 `ng update` 命令和自动化的 Angular Ivy 迁移。

# 执行自动化的 Angular Ivy 迁移

Angular CLI 支持对 Angular 框架包和第三方 Angular 库的自动化迁移。在本节中，我们将学习如何充分利用 `ng update` 命令。最后，我们将讨论重要的自动化 Angular Ivy 迁移。

## 充分利用 ng update 命令

`ng update` 命令用于更新 Angular 特定的包依赖，包括 Angular 框架包和第三方 Angular 库。在更新到指定包版本时，`ng update` 命令会在包捆绑中寻找自动迁移。

更新 Angular 时，可以使用以下命令：

```js
ng update @angular/cli @angular/core
```

这将更新所有主要的 Angular 框架包到最新版本，并执行它们的自动化迁移。Angular CLI 负责工作区迁移，而 Angular 核心包负责迁移到 Angular 的运行时包。

在 *学习 Angular 更新过程* 这一部分，我们建议一次只更新一个主要版本。例如，要指定 Angular 版本 9，请使用以下命令：

```js
ng update @angular/cli⁹ @angular/core⁹
```

这将更新主要的 Angular 框架包到最新的版本 9 补丁版本。

可以通过指定 `--create-commits` 参数来分别在每个提交中执行每个迁移，如下所示：

```js
ng update @angular/cli⁹ @angular/core⁹ --create-commits
```

建议使用此选项，因为它使得检查每个迁移相关的更改变得更容易，或者可以使用 Git 进行 cherry-pick 我们想要的自动迁移，甚至可以撤销迁移的更改。

如果我们选择通过 Git cherry-pick 撤销或省略迁移，我们通常希望手动执行迁移。或者，我们可以使用以下命令格式重新运行特定的迁移：

```js
ng update <package-name>[@<package-version>] --migrate-only <migration-name>
```

我们在指定 `--create-commits` 参数时创建的 Git 提交消息中找到迁移的名称。

在某些情况下，可选的迁移是可用的。例如，Angular 版本 12 引入了一个可选的自动化迁移，用于将 `production` 构建配置设置为默认：

```js
ng update @angular/cli@¹² --migrate-only production-by-default
```

至于何时运行主要的 `ng update` 命令，我们遵循 *学习 Angular 更新过程* 部分中描述的 Angular 更新指南的说明。

对于通过`ng update`命令运行的每个迁移，我们都会在显示`迁移完成`之前看到受迁移影响的文件列表——如果有的话。

一些迁移会引用一个描述迁移的网页。例如，除了在运行迁移前后提供代码片段的示例之外，还需要说明为什么需要这种变化。这是审查自动化迁移所做的更改或手动执行迁移步骤的极好信息。

## 审查自动化 Angular Ivy 迁移

让我们回顾一些最重要的自动化 Angular Ivy 迁移，以了解它们的重要性。

### Angular 工作区版本 9 迁移

命名为`workspace-version-9`的这次迁移修改了构建配置，使得`aot`选项被设置为`true`，即使在默认的开发构建配置中也是如此。实际上，如果我们使用 Angular CLI 12 生成一个新的 Angular Ivy 工作区或应用程序，则没有指定`aot`选项，因为它的默认值是`true`。

这次迁移还改变了`tsconfig.app.json`文件的`include`属性，以匹配`"src/**/*.d.ts"`模式。

### 懒加载语法迁移

这次名为`lazy-loading-syntax`的迁移将基于字符串的懒加载路由路径更改为使用动态`import`语句。例如，看看以下路由配置：

```js
{
  path: 'dashboard',
  loadChildren: './dashboard.module#DashboardModule',
},
```

迁移后，它被更改为以下内容：

```js
{
  path: 'dashboard',
  loadChildren: () => import('./dashboard.module')
    .then(m => m.DashboardModule),
},
```

基于字符串的懒加载路由语法已被弃用，必须避免使用。

### 静态标志迁移

请注意名为`migration-v9-dynamic-queries`的这次迁移。在 Angular 版本 8 中，将必需的`static`选项添加到`ViewChild`和`ContentChild`查询中。在 Angular 版本 9 中，`static`选项变为可选，默认为`false`。

考虑以下 Angular 版本 9 组件：

```js
import { Component, ElementRef, ViewChild } from '@angular/core';
@Component({
  selector: 'app-hello',
  template: '
    <h1 #greeting>
      Hello, World!
    </h1>
    <div #error *ngIf="hasError">
      An error occurred
    </div>
  ',
})
export class HelloComponent {
  @ViewChild('error')
  errorElement?: ElementRef<HTMLElement>;
  @ViewChild('greeting', { static: true })
  greetingElement?: ElementRef<HTMLElement>;
}
```

在 Angular View Engine 版本 7 中，在静态`option`可用之前，其视图查询属性会是这样：

```js
@ViewChild('error')
errorElement?: ElementRef<HTMLElement>;
@ViewChild('greeting')
greetingElement?: ElementRef<HTMLElement>;
```

迁移到 Angular View Engine 版本 8 之后，我们有了以下视图查询属性，因为需要`static`选项：

```js
@ViewChild('error', { static: false })
errorElement?: ElementRef<HTMLElement>;
@ViewChild('greeting', { static: true })
greetingElement?: ElementRef<HTMLElement>;
```

如您可能看到的，Angular 版本 8 的`static query migration`擅长猜测视图查询和内容查询属性的最佳选项。嵌套在嵌入视图中的项目查询，例如由结构指令创建的视图，被转换为动态查询，即`{ static: false }`。

当我们迁移到 Angular Ivy 版本 9 时，`static`选项是可选的，但默认为`false`，因此我们有以下视图查询属性：

```js
@ViewChild('error')
errorElement?: ElementRef<HTMLElement>;
@ViewChild('greeting', { static: true })
greetingElement?: ElementRef<HTMLElement>;
```

动态查询自动通过 Angular 版本 9 的`static`标志迁移移除了`static`选项。

重要提示

查询列表不受本节中涵盖的历史性变化的影响，因为查询列表始终是动态的。

在迁移到 Angular Ivy 版本 9 之前，请确保审查您的内容和视图查询。请参阅*静态查询迁移指南*和*动态查询标志迁移*指南，这些指南在 Angular 版本 12.2 中仍然可在 Angular 文档中找到，分别位于[`angular.io/guide/static-query-migration`](https://angular.io/guide/static-query-migration)和[`angular.io/guide/migration-dynamic-flag`](https://angular.io/guide/migration-dynamic-flag)。

### 异步等待`waitForAsync`迁移

此迁移，名为`migration-v11-wait-for-async`，将`async`测试回调包装器的名称重命名为`waitForAsync`，以避免与`async`-`await`混淆。新名称更好地解释了当我们在这个测试函数中包装测试用例回调时会发生什么。

`waitForAsync`在完成包装的测试用例之前等待所有微任务和宏任务完成。这有点像注入 Jasmine 和 Jest 的`done`回调参数，并在测试用例中的最终异步副作用之后调用它。

### 缺少@Injectable 和不完整提供者定义迁移

这个名为`migration-v9-missing-injectable`的自动 Angular 版本 9 迁移会进行以下类型的代码更改：

+   使用基于类的模块提供者注册的类中添加了`@Injectable`装饰器。

+   不完整的 Angular View Engine 模块提供者被转换为`undefined`值的值提供者。

基于类的模块提供者可以有以下格式之一：

```js
@NgModule({
  providers: [
    DashboardService,
    {
      provide: weatherServiceToken,
      useClass: HttpWeatherService,
    },
  ],
})
export class DashboardServiceModule { }
```

如果`DashboardService`或`HttpWeatherService`没有应用`Injectable`装饰器，此迁移将向它们的类定义添加`Injectable`装饰器。

使用以下格式的模块提供者在 Angular View Engine 和 Angular Ivy 中的评估方式不同：

```js
@NgModule({
  providers: [
    { provide: MusicPlayerService },
  ],
})
export class MusicServiceModule { }
```

Angular View Engine 将提供者评估为以下值提供者：

```js
{ provide: MusicPlayerService, useValue: undefined }
```

Angular Ivy 将提供者评估为以下类提供者：

```js
{ provide: MusicPlayerService, useClass: MusicPlayerService }
```

注意，前面的类提供者等同于以下类提供者简写：

```js
@NgModule({
  providers: [
    MusicPlayerService,
  ],
})
export class MusicServiceModule { }
```

由于提供者评估之间的差异，此迁移将不完整的 Angular View Engine 提供者更改为指定`undefined`值的值提供者。

迁移运行后，请审查所有包含`useValue: undefined`部分的提供者。这很可能不是我们应用程序的意图。

### 可选迁移以将 Angular CLI 工作区配置更新为默认生产模式

Angular CLI 版本 12 生成项目构建配置，其中`production`是默认配置。结果是，我们不需要在`ng build`命令中指定`--configuration=production`参数。

然而，现有项目默认不会自动迁移到使用生产配置。使用名为`production-by-default`的可选迁移将现有项目迁移到这个新默认设置。这主要使用 Angular 版本 12 引入的`defaultConfiguration`设置来完成。

这些是从 Angular View Engine 更新到 Angular Ivy 时需要注意的一些最值得注意的自动化迁移。在下一节中，我们将讨论可选的手动迁移，以确保我们的 Angular Ivy 应用程序处于最佳状态。

# 执行手动 Angular Ivy 迁移

在本节中，我们将介绍可选的迁移，使我们的应用程序为未来的 Angular 版本做好准备。我们将讨论微调初始导航、通过配置`NgZone`优化变更检测以及提高单元测试的类型安全性。

## 管理初始导航

Angular Ivy 版本 11 移除了`RouterModule.forRoot`的`initialNavigation`选项的以下旧值：

+   `true`

+   `false`

+   `'legacy_enabled'`

+   `'legacy_disabled'`

Angular Ivy 版本 11 也弃用了`'enabled'`值，但引入了以下新值：

+   `'enabledBlocking'`

+   `'enabledNonBlocking'`（默认）

`'enabledBlocking'`与`'enabled'`等价，并推荐用于 Angular Universal 的服务器端渲染。此值在 Angular 创建我们应用程序根组件实例之前启动初始导航过程，但直到初始导航完成之前阻止根组件的引导。

默认的`'enabledNonBlocking'`值在 Angular 创建我们应用程序根组件实例之后启动初始导航，但允许在初始导航完成之前引导根组件。这种行为类似于已删除的`true`值。

`'disabled'`是第三个可用的、非弃用值。它禁用初始导航过程，并推迟到我们的应用程序代码通过使用`Location`和`Router`服务来执行。此值仅应用于高级用例。

## 通过配置 NgZone 优化变更检测

当我们调用`PlatformRef#bootstrapModule`方法——通常在我们的应用程序主文件中——我们可以指定编译器和引导选项。截至 Angular 版本 12.2，引导选项未在 Angular 文档中列出。然而，内联文档是可用的。

除了允许我们完全禁用`NgZone`的传统`ngZone`选项之外，Angular Ivy 还添加了以下两个选项：

+   `ngZoneEventCoalescing`

+   `ngZoneRunCoalescing`

它们都接受一个默认为`false`的布尔值。这两个选项通过将同一 VM 转换中的多个变更检测周期请求合并为一个操作，并使用动画帧来同步变更检测与当前帧率，从而针对特定用例优化变更检测。

事件合并（`ngZoneEventCoalescing`）指的是原生 DOM 事件冒泡。例如，如果单个用户点击触发了多个点击事件处理器，则变更检测只会触发一次。

`ngZoneRunCoalescing`管理在同一个 VM 转换中多次调用`NgZone#run`方法。

由于它们可以提高性能，因此启用这两个选项是一个好的默认设置。然而，它们可能会在某些边缘情况下改变我们的应用程序行为，例如在 Angular 开发模式下抛出`NG0100`错误，即`ExpressionChangedAfterItHasBeenCheckedError`。

因此，在启用这些引导设置时，请特别注意。

## 使用 TestBed.inject 提高单元测试类型安全性

Angular Ivy 引入了静态的`TestBed.inject`方法，这是一个强类型方法，它取代了弱类型的静态`TestBed.get`方法。

`TestBed.get`方法返回一个`any`类型的值。在下面的示例中，我们看到这迫使我们为存储返回依赖项的变量指定类型注解：

```js
it('displays dashboard tiles', () => {
  const dashboardService: DashboardService =
    TestBed.get(DashboardService);
  // (...)
});
```

当迁移到`TestBed.inject`时，我们通常可以省略类型注解，如下面的等效代码片段所示：

```js
it('displays dashboard tiles', () => {
  const dashboardService = 
   TestBed.inject(DashboardService);
  // (...)
});
```

如果提供的类型与提供者令牌不同，我们现在必须在将其转换为注册类型之前将返回的依赖项转换为`unknown`，如下面的示例所示：

```js
it('displays dashboard tiles', () => {
  TestBed.configureTestingModule({
    providers: [
      {
        provide: DashboardService,
        useClass: DashboardServiceStub
      },
    ],
  });
  const dashboardServiceStub =
    TestBed.inject(DashboardService)
      as unknown as DashboardServiceStub;
  // (...)
});
```

值得注意的是，`TestBed.inject`在严格性上比`TestBed.get`更强，因为它只接受类型为`Type<T> | AbstractType<T> | InjectionToken<T>`的提供者令牌参数，即一个具体类、一个抽象类或依赖注入令牌。

这与`TestBed.get`不同，它支持类型为`any`的提供者令牌，例如字符串、数字或符号。

避免使用`TestBed.inject`不支持提供者令牌，因为这些自 Angular 版本 4 以来已被弃用，例如用于在运行时解析依赖项的弱类型`Injector#get`签名。

# 摘要

在本章中，我们讨论了 Angular 更新过程，包括*Angular 更新指南*、`ng update`命令以及管理 Angular 的第三方依赖项。

我们学习了如何通过简单的代码示例来审查某些重要的自动化 Angular Ivy 迁移。

最后，我们考虑了几种可选的迁移，包括自动和手动迁移 Angular Ivy。我们学习了如何根据我们的应用程序平台微调 Angular 路由器的初始导航。

之后，我们讨论了两个未记录的`NgZone`配置设置，这些设置通过合并多个请求的变更检测周期为某些原生事件和用例优化变更检测。

我们讨论的最后一种手动迁移通过使用强类型的静态`TestBed.inject`方法而不是已弃用的静态`TestBed.get`方法，提高了我们的单元测试类型安全性。

在下一章中，我们将探讨 Angular 预编译编译器的影响和限制，这是 Angular Ivy 中应用程序的默认编译器。
