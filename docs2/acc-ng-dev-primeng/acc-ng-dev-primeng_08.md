# *第十二章*：拥抱即时编译

Angular Ivy 是 Angular 框架的最新一代。它具有一个新的编译器和一个新的运行时，它们都保持了与之前一代 Angular 的编译器和运行时（称为 Angular 视图引擎）使用的多数 API 的兼容性。

在本章中，我们将了解 Angular Ivy 如何使**即时编译**成为开发所有阶段的默认 Angular 编译器，以及它对我们开发者工作流程的影响。

随后，我们将探讨在使用即时编译的 Angular 编译器时可能出现的元数据错误，以及伴随的修复错误的技术。

最后，我们将演示两种在启动我们的 Angular Ivy 应用程序之前解决异步依赖的技术。

本章我们将涵盖以下主题：

+   在所有开发阶段使用即时编译器

+   处理即时编译器的限制

+   初始化异步依赖

在阅读本章后，你将了解 Angular Ivy 如何使即时编译的 Angular 编译器成为我们开发工作流程所有阶段的默认编译器成为可能。你将理解即时编译器如何影响我们开发工作流程的不同阶段。

本章介绍了与即时编译的 Angular 编译器不兼容的边缘情况，同时也教你如何处理它们或绕过它们。

你将学习两种在启动 Angular 应用程序之前解决异步依赖的技术，以及它们带来的权衡。

# 技术要求

本章讨论的技术细节适用于以下版本或更高版本的应用程序：

+   Angular Ivy 版本 12.2

+   TypeScript 版本 4.2

在 Angular 和 TypeScript 的早期版本中可能会出现更多的元数据错误。

你可以在本书的配套 GitHub 仓库中找到功能标志和功能标志初始化器的完整代码示例，该仓库地址为 [`github.com/PacktPublishing/Accelerating-Angular-Development-with-Ivy/tree/main/projects/chapter12`](https://github.com/PacktPublishing/Accelerating-Angular-Development-with-Ivy/tree/main/projects/chapter12)。

# 在所有开发阶段使用即时编译器

在 Angular 的前几代中，即时编译器比即时 Angular 编译器慢得多。由于这个和其他因素，即时编译在所有或几个开发阶段中是默认的编译器，这取决于 Angular 的版本。这反过来又导致了问题，因为错误只有在进行生产构建时或更糟糕的是在生产环境中运行时才会被发现。

Angular Ivy 默认在所有开发阶段使用其**即时编译器**，包括在运行开发服务器、运行测试、服务器端渲染以及在浏览器中，而不是在运行时捆绑并运行**即时编译器**。

本节讨论了即时 Angular 编译器如何影响我们开发工作流程的这些阶段。

## 构建时的即时编译

除了 Angular Ivy 中改进的编译速度外，默认使用即时编译器的另一个关键因素是 Angular Ivy 在某些条件下会减小我们应用的包大小。一般来说，与 View Engine 相比，无论是小型还是大型应用，在 Ivy 编译时都会看到整体大小的减小，而中等大小的应用可能不会看到显著的变化。

更具体地说，当使用 Angular Ivy 时，小型和简单应用的包大小会减小。对于复杂应用，主包大小增加，而与 Angular View Engine 相比，懒加载的块变得更小。这有点权衡，因为更大的主包大小会增加几个性能时间测量指标。

解锁更小包机会的秘诀是从 View Engine 在运行时解释的数据结构过渡到所谓的 **Ivy 指令集**，它重用运行时命令或指令，而不是像 View Engine 那样为应用每个部分都有一个独特的数据结构。

View Engine 编译的数据结构有一个缺点，即存在一个拐点，此时编译的数据结构比编译器和我们的应用源代码更大。

相比之下，Ivy 指令集是可摇树的，这意味着只有我们应用使用的指令包含在生产包中。例如，如果我们的应用不是多语言，国际化指令将从生产包中移除。同样，如果我们的应用不使用动画，动画指令将排除在生产包之外。

与 View Engine 相比，Ivy 指令集由一个显著更快的运行时使用，因为预编译的指令可以立即执行，而 View Engine 的数据结构必须在运行时被解释为指令后才能执行。

## 组件模板的即时编译

当使用 Angular Ivy 的即时编译器时，建议启用严格模板类型检查，如*第二章*中所述，*通过工具、配置和便利性提高开发者生产力*。

严格的模板类型检查可以捕获 Angular 元数据、组件模型和组件模板中的大多数类型错误。它们将在构建我们的应用时出现，或者在使用 Angular 语言服务时，在我们的代码编辑器中内联出现。

如果没有严格的模板类型检查，这些错误可能在运行时表现为令人沮丧的 bug。

## 单元测试的即时编译

Angular Ivy 减少了构建时间和重建时间。这对于开发服务器和单元测试都是一个节省时间的改进。除了更快的编译速度外，Angular Ivy 还引入了编译缓存，以便在测试用例之间缓存编译后的 Angular 模块、组件、指令、管道和服务。

在 Angular View Engine 中，不支持单元测试的即时编译。Angular Ivy 引入了单元测试的即时编译支持，同时仍然允许动态创建 Angular 模块、组件、指令和管道以进行测试。单元测试期间的动态创建仍然使用即时 Angular 编译器。

## 运行时的即时编译

当使用即时 Angular 编译器时，我们的 Angular 应用程序加载速度更快，因为即时 Angular 编译器并未与应用程序捆绑在一起。我们的应用程序启动速度更快，因为编译器是在构建时而不是在运行时运行的。

由于 Ivy 指令集，Angular Ivy 运行时比 Angular View Engine 运行时更快。View Engine 运行时必须在初始化或更新由 Angular 组件模板管理的 DOM 之前解释视图编译器数据结构。相比之下，Ivy 指令立即执行。

在本节中，我们讨论了即时 Angular 编译器对我们开发工作流程不同阶段的影响。在下一节中，我们将探讨即时编译器的限制，并通过简单的代码示例来探讨我们如何解决这些问题。

# 处理即时编译器的限制

使用 Angular Ivy 的即时编译器的优点是运行时速度更快，捆绑包更小，因为不需要将编译器发送到运行时捆绑包或编译器，在渲染应用程序之前。

当使用即时编译器时，需要注意权衡。声明式组件——即指令、组件和管道——不能依赖于运行时信息，因为它们必须在构建时而不是在运行时编译。

这为在运行时动态创建声明式组件设定了限制，例如，基于服务器端配置或静态配置文件。除非，当然，我们将 Angular 编译器与应用程序捆绑在一起并在运行时使用它，但那样的话，又有什么意义呢？

好消息是，注入的依赖项——即基于类的服务、提供的值或函数——可以在运行时解析。请记住，只有同步解析的值可以直接提供。如果我们需要一个异步过程来解析一个值，我们必须将其包装在一个基于类的服务、一个函数、一个承诺或一个可观察对象中。这一点在本章的最后部分进行了讨论和解决。

在本节中，我们将简要讨论使用提前编译的 Angular 编译器时出现的元数据错误。我们不会讨论通过使用严格的 TypeScript 编译或严格的模板类型检查来解决元数据错误的错误，正如在*第二章*中讨论的，*通过工具、配置和便利提高开发者生产力*。

## 使用函数提供值

将函数调用的结果传递给值提供者是不支持的。相反，我们使用工厂函数并将它们声明为称为工厂提供者的提供者。例如，假设我们有以下与提前编译不兼容的**值提供者**：

```js
{ provide: timeZoneToken, useValue: guessTimeZone() }
```

我们可以用以下提前编译兼容的**工厂提供者**来替换它：

```js
{ provide: timeZoneToken, useFactory: guessTimeZone }
```

这配置 Angular 在我们应用程序生命周期的适当时间运行工厂函数，以解决由`timeZoneToken`表示的时间区域依赖性，同时保持与提前编译的 Angular 编译器的兼容性。

## 使用函数声明元数据

已知的一个提前编译边缘情况是使用函数或静态方法来确定声明的元数据，例如 Angular 模块的`imports`或`declarations`。

在以下用例中，我们尝试在 Angular 开发模式下启动一个假根组件：

```js
import { NgModule, Type } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { environment } from '../environments/environment';
import { AppFakeComponent } from './app-fake.component';
import { AppComponent } from './app.component';
function determineAppComponent(isDevelopment: boolean): 
  Type<any>[] {
  if (isDevelopment) {
    return [AppFakeComponent];
  } else {
    return [AppComponent];
  }
}
const developmentEnvironment = environment.production === false;
@NgModule({
  bootstrap: determineAppComponent(developmentEnvironment),
  declarations: determineAppComponent(
   developmentEnvironment),
  imports: [BrowserModule],
})
export class AppModule {}
```

当我们尝试提前编译此代码时，我们会遇到 Angular 元数据的限制。用于元数据声明的函数必须只包含一个`return`语句，不能包含其他内容。

为了符合元数据限制，我们将`determineAppComponent`函数重构为以下实现：

```js
function determineAppComponent(isDevelopment: boolean):
  Type<any>[] {
  return isDevelopment ? [AppFakeComponent] : 
   [AppComponent];
}
```

函数现在只包含一个表达式，一个评估三元表达式的`return`语句。这符合提前编译的 Angular 编译器的规范。

## 在组件模板中使用带标签的模板字面量

不幸的是，提前编译的 Angular 不支持带标签的模板字面量。例如，以下组件会导致编译错误：

```js
import { Component } from '@angular/core';
const subject = 'World';
const greeting = String.raw`Hello, ${subject}!`;
@Component({
  selector: 'app-root',
  template: '<h1>' + greeting + '</h1>',
})
export class AppComponent {}
```

相反，我们可以使用常规函数来创建模板的编译时动态部分，如下面的实现所示：

```js
import { Component } from '@angular/core';
const subject = 'World';
const greeting = `Hello, ${subject}!`;
@Component({
  selector: 'app-root',
  template: '<h1>' + greeting + '</h1>',
})
export class AppComponent {}
```

为了符合提前编译的 Angular 编译器对 Angular 元数据的限制，我们必须避免在组件模板元数据中使用带标签的模板字面量。然而，我们可以在模板绑定中使用的 UI 属性中使用带标签的模板字面量，如下面的重构实现所示：

```js
import { Component } from '@angular/core';
const subject = 'World';
@Component({
  selector: 'app-root',
  template: '<h1>{{ greeting }}</h1>',
})
export class AppComponent {
  greeting = String.raw`Hello, ${subject}!`;
}
```

分别选择这两种技术来处理或支持 Angular 组件模板中的带标签的模板字面量。

## 初始化元数据变量

元数据必须立即对提前编译器可用。以下示例由于延迟初始化而无效：

```js
import { Component } from '@angular/core';
let greeting: string;
setTimeout(() => {
  greeting = '<h1>Hello, World!</h1>';
}, 0);
@Component({
  selector: 'app-hello',
  template: greeting,
})
export class HelloComponent {}
```

当组件元数据被提前编译的 Angular 编译器转换为注解时，`greeting`变量尚未初始化。

更令人惊讶的是，以下示例也是无效的：

```js
import { Component } from '@angular/core';
let greeting: string;
greeting = '<h1>Hello, World!</h1>';
@Component({
  selector: 'app-hello',
  template: greeting,
})
export class HelloComponent {}
```

虽然这不是一个常见的用例，但请记住这个限制，因为它相当令人惊讶。

让我们先看看固定实现：

```js
import { Component } from '@angular/core';
let greeting = '<h1>Hello, World!</h1>';
@Component({
  selector: 'app-hello',
  template: greeting,
})
export class HelloComponent {}
```

`greeting` 变量现在同时定义和初始化，这样组件模板才能按预期工作。

现在，让我们在初始化 `greeting` 变量之后立即更改其值：

```js
import { Component } from '@angular/core';
let greeting = '<h1>Hello, World!</h1>';
greeting = '<h1>Hello, JIT compiler!</h1>';
@Component({
  selector: 'app-hello',
  template: greeting,
})
export class HelloComponent {}
```

当使用即时 Angular 编译器时，模板是 `<h1>Hello, World!</h1>`。如果我们切换到即时 Angular 编译器，模板是 `<h1>Hello, JIT compiler!</h1>`。

当使用即时 Angular 编译器时，用于可声明元数据的变量必须在同一时间定义和初始化。

在本节中，我们探讨了即时编译器兼容性的边缘情况，并学习了如何解决这些问题。在下一节中，我们将学习如何在引导我们的应用程序之前初始化异步依赖项。

# 初始化异步依赖项

引用异步值是有害的，因为从引用的值计算出的每个值都必须是异步的。有一些技术可以绕过这个问题，但它们都会以延迟应用程序引导直到值被解析为代价。这些技术在本节中进行了演示。

## 使用静态平台提供者提供异步依赖项

要将异步依赖项解析器转换为静态依赖项，我们可以延迟引导我们的应用程序，以在平台级别提供静态提供者，使其作为静态依赖项在我们的应用程序中可用。

例如，假设我们有一个包含布尔值的对象的 JSON 文件。我们在应用程序项目的 `src/app/assets/features.json` 文件中创建它。此文件包含我们的**功能标志**，它们在运行时加载。此文件中的设置可以在编译我们的应用程序后更改。

在我们的应用程序项目的 `src/load-feature-flags.ts` 文件中，我们添加以下函数：

```js
export function loadFeatureFlags():
  Promise<{ [feature: string]: boolean }> {
  return fetch('/assets/features.json')
    .then((response) => response.json());
}
```

在我们的应用程序主文件中调用此函数之前，我们创建一个依赖注入令牌来表示功能标志。

以下代码块显示了我们的应用程序项目的 `src/app/feature-flags.token.ts` 文件：

```js
import { InjectionToken } from '@angular/core';
export const featureFlagsToken =
  new InjectionToken<Record<string, boolean>>(
    'Feature flags'
  );
```

最后，我们修改我们的主文件，使其包含以下内容：

```js
import { enableProdMode } from '@angular/core';
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';
import { AppModule } from './app/app.module';
import { featureFlagsToken } from './app/feature-flags.token';
import { environment } from './environments/environment';
import { loadFeatureFlags } from './load-feature-flags';
if (environment.production) {
  enableProdMode();
}
loadFeatureFlags()
  .then((featureFlags) =>
    platformBrowserDynamic([
      { provide: featureFlagsToken, useValue: featureFlags 
       },
    ]).bootstrapModule(AppModule)
  )
  .catch((err) => console.error(err));
```

注意我们是如何加载功能标志，然后在能够引导我们的 Angular 应用程序模块之前，将它们作为功能标志令牌的平台提供者的值传递。

现在，我们可以在任何 Angular 特定的类中注入功能标志，例如组件，如下面的示例所示：

```js
import { Component, Inject } from '@angular/core';
import { featureFlagsToken } from './feature-flags.token';
@Component({
  selector: 'app-root',
  template: `
    <div *ngFor="let feature of features | keyvalue">
      <mat-slide-toggle [checked]="feature.value">
        {{ feature.key }}
      </mat-slide-toggle>
    </div>
  `,
})
export class AppComponent {
  constructor(
    @Inject(featureFlagsToken)
    public features: { [feature: string]: boolean }
  ) {}
}
```

功能标志是这种技术的良好用例。其他配置也适合这种方法。此外，如果多个应用程序初始化器需要共享依赖项，这种方法是最佳选择。

在下一节中，我们将介绍一种替代技术，并讨论其差异。

## 使用应用程序初始化器解析异步依赖项

处理异步解析的依赖项的另一种技术是应用程序初始化器。

在引导根应用程序组件之前解析应用程序初始化器。这对于设置不需要其他应用程序初始化器的初始根级状态是理想的。

我们将考虑一种处理功能标志的替代方法。这次，我们使用的是通过应用程序初始化器配置的功能标志服务。

功能标志服务具有以下实现：

```js
import { Injectable } from '@angular/core';
@Injectable({
  providedIn: 'root',
})
export class FeatureFlagService {
  #featureFlags = new Map<string, boolean>();
  configureFeatures(featureFlags: { [feature: string]: 
   boolean }): void {
    Object.entries(featureFlags).forEach(([feature, state]) 
     =>
      this.#featureFlags.set(feature, state)
    );
  }
  isEnabled(feature: string): boolean {
    return this.#featureFlags.get(feature) ?? false;
  }
}
```

功能标志初始化器在调用`FeatureFlagService#configureFeatures`之前使用`HttpClient`加载功能标志。这如下面的代码示例所示：

```js
import { HttpClient } from '@angular/common/http';
import { APP_INITIALIZER, FactoryProvider } from '@angular/core';
import { Observable } from 'rxjs';
import { mapTo, tap } from 'rxjs/operators';
import { FeatureFlagService } from './feature-flag.service';
function configureFeatureFlags(
  featureFlagService: FeatureFlagService,
  http: HttpClient
): () => Observable<void> {
  return () =>
    http.get<{ [feature: string]: boolean 
     }>('/assets/features.json').pipe(
      tap((features) => 
       featureFlagService.configureFeatures(features)),
      mapTo(undefined)
    );
}
export const featureFlagInitializer: FactoryProvider = {
  deps: [FeatureFlagService, HttpClient],
  multi: true,
  provide: APP_INITIALIZER,
  useFactory: configureFeatureFlags,
};
```

最后，我们通过将其添加到`providers`数组中，在我们的根模块中注册功能标志初始化器，如下面的代码片段所示：

```js
import { HttpClientModule } from '@angular/common/http';
import { NgModule } from '@angular/core';
import { MatSlideToggleModule } from '@angular/material/slide-toggle';
import { BrowserModule } from '@angular/platform-browser';
import { AppComponent } from './app.component';
import { featureFlagInitializer } from './feature-flag.initializer';
@NgModule({
  bootstrap: [AppComponent],
  declarations: [AppComponent],
  imports: [BrowserModule, HttpClientModule, 
   MatSlideToggleModule],
  providers: [featureFlagInitializer],
})
export class AppModule {}
```

在设置好所有这些之后，任何 Angular 特定的类都可以注入`FeatureFlagService`类的实例，并使用其`isEnabled`方法来检查功能标志的状态，如下面的代码示例所示：

```js
import { Component } from '@angular/core';
import { FeatureFlagService } from './feature-flag.service';
@Component({
  selector: 'app-root',
  template: `
    <div>
      <mat-slide-toggle
        [checked]="featureFlagService.isEnabled(
         'middleOutCompression')"
      >
        Middle-out compression
      </mat-slide-toggle>
    </div>
    <div>
      <mat-slide-toggle
        [checked]="featureFlagService.isEnabled(
         'decentralized')"
      >
        Decentralized application
      </mat-slide-toggle>
    </div>
  `,
})
export class AppComponent {
  constructor(public featureFlagService: 
    FeatureFlagService) {}
}
```

使用应用程序初始化器的优点是，可以并行运行多个初始化器，这比在前一节中延迟整个引导过程直到响应完成要快。

代价是我们必须将功能标志包装在一个基于服务的类中，该类具有编写和读取功能标志配置的方法，而使用我们之前探索的第一种技术时，功能标志作为静态依赖项可用，这是一个非常简单的对象。选择最适合您用例的技术。

# 摘要

在本章中，我们学习了增强的 Angular Ivy 编译器和运行时如何使提前时间 Angular 编译器成为所有开发阶段的良好选择。可摇树优化、可重用的 Ivy 指令集为各种应用程序留下了更小的包。

我们讨论了提前时间编译如何影响我们的应用程序构建、组件模板、单元测试以及在运行时的浏览器。

接下来，我们探讨了在使用提前时间 Angular 编译器时出现的元数据错误的解决方案。关于由严格的 TypeScript 和 Angular 编译设置检测到的元数据错误没有讨论。请参阅*第二章*，*通过工具、配置和便利提高开发者生产力*中关于严格模板类型检查的内容。

在最后几节中，我们学习了在引导应用程序之前如何使用两种技术来解决和初始化异步依赖：

+   使用静态平台提供者提供异步依赖

+   使用应用程序初始化器解决异步依赖

这些技术对于功能标志和其他配置来说都很棒，但它们各自都有权衡，你现在能够识别出来。

这本书就到这里结束了。我们希望您在学习 Angular Ivy 及其配套版本的 TypeScript 引入的一些最有趣的新特性时感到愉快。Angular 是一个不断发展的框架，每年都会发布几个新特性。

继续学习！
