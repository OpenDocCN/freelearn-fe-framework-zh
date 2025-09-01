

# 第五章：Angular 服务和单例模式

静态网页和单页应用之间的一大区别是用户浏览器中的处理能力和交互，给人一种在设备上安装了应用程序的感觉。在 Angular 框架中，进行这种处理和交互的元素，不仅与后端，而且与用户，是**服务**。

这个元素对 Angular 来说非常重要，以至于团队创建了一个依赖管理系统，它允许以简化的方式在组件中创建、组合和使用服务。

在本章中，我们将探讨这个元素，了解它使用的模式以及在你的项目中应遵循的最佳实践。

在这里，我们将涵盖以下主题：

+   创建服务

+   理解依赖注入模式

+   使用服务在组件之间进行通信

+   消费 REST API

到本章结束时，你将能够创建可重用和可维护的服务，同时了解将提高你生产力的实践。

# 技术要求

要遵循本章的说明，你需要以下内容：

+   Visual Studio Code ([`code.visualstudio.com/Download`](https://code.visualstudio.com/Download))

+   Node.js 18 或更高版本 ([`nodejs.org/en/download/`](https://nodejs.org/en/download/))

本章的代码文件可在[`github.com/PacktPublishing/Angular-Design-Patterns-and-Best-Practices/tree/main/ch5`](https://github.com/PacktPublishing/Angular-Design-Patterns-and-Best-Practices/tree/main/ch5)找到。

# 创建服务

Angular 中的**服务**是 TypeScript 类，旨在实现我们接口的业务逻辑。在前端项目中，业务逻辑可能是一个有争议的问题，因为理想情况下，所有逻辑和处理都应该在后台进行，这是正确的。

这里我们使用的是业务规则；这些规则是通用的行为，不依赖于视觉组件，可以在其他组件中重用。

前端业务规则的例子可能如下所示：

+   应用状态控制

+   与后端的通信

+   使用固定规则（如电话号码中的数字数量）进行信息验证

我们将把这个概念付诸实践，并在我们的健身房日记应用程序中创建第一个服务。在命令行中，我们将使用 Angular CLI：

```js
ng generate service diary/services/ExerciseSets
```

与组件不同，我们可以看到 Angular CLI 创建的元素仅由一个 TypeScript 文件（及其相应的单元测试文件）组成。

在这个文件中，我们将看到 Angular CLI 生成的样板代码：

```js
import { Injectable } from '@angular/core';
@Injectable({
  providedIn: 'root'
})
export class ExerciseSetsService {
  constructor() { }
}
```

在这里，我们有一个名为`ExerciseSetsService`的 TypeScript 类，它有一个名为`@Injectable`的装饰器。正是这个装饰器定义了 Angular 中的服务；我们将在本章后面了解更多关于它的细节。

让我们重构我们的项目，并将日记的初始系列设置放在这个服务中。

首先，我们将创建获取初始列表并在后端刷新它的方法：

```js
private setList?: ExerciseSetList;
getInitialList(): ExerciseSetList {
  this.setList = [
    { id: 1, date: new Date(), exercise: 'Deadlift', reps: 15, sets: 3 },
    { id: 2, date: new Date(), exercise: 'Squat', reps: 15, sets: 3 },
    { id: 3, date: new Date(), exercise: 'Barbell row', reps: 15, sets: 3 },
  ];
  return this.setList;
}
refreshList(): ExerciseSetList {
  this.setList = [
    { id: 1, date: new Date(), exercise: 'Deadlift', reps: 15, sets: 3 },
    { id: 2, date: new Date(), exercise: 'Squat', reps: 15, sets: 3 },
    { id: 3, date: new Date(), exercise: 'Barbell row', reps: 15, sets: 3 },
    { id: 4, date: new Date(), exercise: 'Leg Press', reps: 15, sets: 3 },
  ];
  return this.setList;
}
```

在服务中，我们将日记组件的初始化和刷新操作移动到服务中，使用 `getInitialList` 和 `refreshList` 方法。

当我们看到与后端的通信时，这些方法将得到改进，但在这里，我们已经在将管理练习列表的业务规则从渲染用户界面的组件中解耦，创建了一个特定的服务。

现在让我们考虑向练习列表添加项的方法：

```js
addNewItem(item: ExerciseSet): ExerciseSetList {
  if (this.setList) {
    this.setList = [...this.setList, item];
  } else {
    this.setList = [item];
  }
  return this.setList;
}
```

服务的 `setList` 属性可以是 null，因此在这里我们使用 TypeScript 类型守卫概念（更多详情见 *第三章*，*Angular 的 TypeScript 模式*）来操作数组。在这里，我们也使用不可变性的概念，在添加新元素后返回一个新的数组。

在 `DiaryComponent` 组件中，我们将使用我们创建的服务：

```js
export class DiaryComponent {
  constructor(private exerciseSetsService: ExerciseSetsService) {}
  exerciseList = this.exerciseSetsService.getInitialList();
  newList() {
    this.exerciseList = this.exerciseSetsService.refreshList();
  }
  addExercise(newSet: ExerciseSet) {
    this.exerciseList = this.exerciseSetsService.addNewItem(newSet);
  }
}
```

在组件中，我们首先可以观察到的是类构造函数的使用，声明了一个类型为 `ExerciseSetsService` 的私有属性 `exerciseSetsService`。通过这个声明，我们实例化了一个对象，并重构了我们的组件，用服务方法替换了列表的初始化和刷新操作。

从现在起，组件不再关心如何获取和管理练习列表；这是服务的责任，如果需要，我们现在可以在其他组件中使用这个服务。在这段代码中，你可能想知道为什么我们使用了 `ExerciseSetsService` 服务，如果我们没有实例化该类的对象。

这里，Angular 有一个很好的特性，即依赖注入机制，我们将在下一节深入探讨这个话题。

# 理解依赖注入模式

在面向对象的软件开发中，优先考虑组合而非继承是一个好的实践，这意味着一个类应该由其他类（最好是接口）组成。

在我们之前的例子中，我们可以看到 `service` 类包含了 `DiaryComponent` 组件。另一种使用此服务的方法如下：

```js
. . .
export class DiaryComponent {
  private exerciseSetsService: ExerciseSetsService;
  exerciseList: ExerciseSetList;
  constructor() {
    this.exerciseSetsService = new ExerciseSetsService();
    this.exerciseList = this.exerciseSetsService.getInitialList();
  }
. . .
}
```

在这里，我们修改我们的代码，明确地将服务类对象的创建留在了组件的构造函数方法中。再次运行我们的代码，我们可以看到界面保持不变。

这种方法虽然功能齐全，但存在一些问题，例如以下内容：

+   组件和服务之间的高耦合，这意味着如果我们需要更改服务的实现，例如构建单元测试，我们可能会遇到问题。

+   如果服务依赖于另一个类，正如我们将要在 Angular 的 HTTP 请求服务 `HttpClient` 类中看到的那样，我们将在我们的组件中实现这个依赖，从而增加其复杂性。

为了简化开发并解决我们所描述的问题，Angular 有一个依赖注入机制。这个特性允许我们仅通过在构造函数中声明所需的对象来组合一个类。

Angular 利用 TypeScript，将使用在此声明中定义的类型来组装我们所需的类的依赖树，并创建所需的对象。

让我们回到我们的代码，分析这个机制是如何工作的：

```js
. . .
export class DiaryComponent {
  constructor(private exerciseSetsService: ExerciseSetsService) {}
  exerciseList = this.exerciseSetsService.getInitialList();
. . .
}
```

在代码中，我们在构造函数中声明了我们类的依赖，创建了`exerciseSetsService`属性。有了这个，我们就可以在它的声明中初始化`exerciseList`属性。

在*第十章*，*为测试而设计：最佳实践*中，我们将替换测试运行时中此服务的实现。所有这一切都得益于 Angular 的依赖注入功能。

从 Angular 的 14 版开始，我们有了一个依赖注入的替代方案，我们将在下一节中看到。

## 使用 inject()函数

`inject()`函数允许你以更简单的方式使用相同的依赖注入功能。

让我们重构我们的组件代码：

```js
import { Component, inject } from '@angular/core';
import { ExerciseSet } from '../interfaces/exercise-set';
import { ExerciseSetsService } from '../services/exercise-sets.service';
. . .
export class DiaryComponent {
  private exerciseSetsService = inject(ExerciseSetsService);
  exerciseList = this.exerciseSetsService.getInitialList();
. . .
}
```

在这里，我们移除了依赖注入的构造函数声明，并直接声明了`exerciseSetsService`服务。对于对象的创建，我们使用`inject`函数。

需要注意的是，我们使用的是`@angular/core`模块中的`inject`函数，而不是`@angular/core/testing`模块中存在的函数，后者将用于其他目的。

这种方法，除了更简单、更清晰（服务是通过函数注入的）之外，如果需要为特定组件使用继承，还可以简化开发。记住，良好的实践建议我们应优先选择组合而非继承，但在库中，这个特性可能很有趣。

关于`inject`函数的一个需要注意的点是其只能在组件的构造阶段使用，即在方法的属性声明或类的构造方法中。

在其他上下文中的任何使用都将生成以下编译错误：

```js
inject() must be called from an injection context
such as a constructor, a factory function, a field initializer,
or a function used with `runInInjectionContext`.
```

现在，让我们深入探讨 Angular 服务的另一个方面，即单例设计模式的使用，以及我们如何利用这种能力在组件之间进行通信。

# 使用服务进行组件间的通信

关于 Angular 服务，我们必须理解的一个特点是，默认情况下，由依赖注入机制实例化的每个服务都有相同的引用；也就是说，不会创建新的对象，而是重用。

这是因为依赖注入机制实现了单例设计模式来创建和传递对象。**单例模式**是一种创建型设计模式，允许创建在系统中具有全局访问权限的对象。

这个特性对于服务很重要，因为服务处理可重用的业务规则，我们可以在组件之间使用相同的实例，而无需重建整个对象。此外，我们可以利用这个特性，将服务用作组件之间通信的替代方案。

让我们修改我们的健身房日记，使`ListEntriesComponent`组件通过服务而不是`@Input`接收初始列表：

```js
export class ListEntriesComponent {
  private exerciseSetsService = inject(ExerciseSetsService);
  exerciseList = this.exerciseSetsService.getInitialList();
  itemTrackBy(index: number, item: ExerciseSet) {
    return item.id;
  }
}
```

在`DiaryComponent`组件中，我们将从输入中删除列表：

```js
<main class="mx-auto mt-8 max-w-6xl px-4">
  <app-list-entries />
  <app-new-item-button (newExerciseEvent)="addExercise($event)" />
  <br />
  <br />
  <button
    class="rounded bg-blue-500 py-2 px-4 font-bold text-white hover:bg-blue-700"
    (click)="newList()"
  >
    Server Sync
  </button>
</main>
```

再次运行它，我们可以看到列表继续出现。这是因为两个组件中使用的服务实例是相同的。然而，这种通信形式需要我们使用 RxJS 通过日记屏幕上的按钮来更新值。我们将在*第九章*中更深入地探讨这个主题，*使用 RxJS 探索反应性*。

我们看到，默认情况下，服务是单例的，但在 Angular 中，如果需要解决应用程序中的某些边缘情况，可以更改此配置以用于其他服务。

当我们创建一个服务时，它有一个`@Injectable`装饰器，就像我们的例子一样：

```js
@Injectable({
  providedIn: 'root',
})
export class ExerciseSetsService {
```

`provideIn`元数据决定了服务的范围。值`'root'`表示每个应用程序都将有一个唯一的服务实例；这就是为什么默认情况下，Angular 服务是单例的。

要更改此行为，让我们首先回到`ListEntriesComponent`组件以接收`@Input`：

```js
export class ListEntriesComponent {
  @Input() exerciseList!: ExerciseSetList;
  itemTrackBy(index: number, item: ExerciseSet) {
    return item.id;
  }
}
```

让我们回到`DiaryComponent`组件中通知属性：

```js
<main class="mx-auto mt-8 max-w-6xl px-4">
  <app-list-entries [exerciseList]="exerciseList" />
  <app-new-item-button (newExerciseEvent)="addExercise($event)" />
  <br />
  <br />
  <button
    class="rounded bg-blue-500 py-2 px-4 font-bold text-white hover:bg-blue-700"
    (click)="newList()"
  >
    Server Sync
  </button>
</main>
```

在`ExerciseSetsService`服务中，我们将删除`provideIn`元数据：

```js
@Injectable()
export class ExerciseSetsService {
```

如果我们现在运行我们的应用程序，将发生以下错误：

```js
ERROR Error: Uncaught (in promise): NullInjectorError: R3InjectorError(DiaryModule)[ExerciseSetsService -> ExerciseSetsService -> ExerciseSetsService -> ExerciseSetsService]: NullInjectorError: No provider for ExerciseSetsService!
```

这个错误发生在我们通知 Angular 服务不应该在应用程序范围内实例化时。为了解决这个问题，让我们直接在`DiaryComponent`组件中声明对服务的使用：

```js
@Component({
  templateUrl: './diary.component.html',
  styleUrls: ['./diary.component.css'],
  providers: [ExerciseSetsService],
})
export class DiaryComponent {
```

因此，我们的系统再次工作，并且组件有自己的服务实例。

然而，这种技术必须在特定情况下使用，其中组件必须使用它自己的服务实例；建议在服务中保留`provideIn`。

现在，让我们开始使用 Angular 探索我们的应用程序与后端之间的通信。

# REST API 消费

毫无疑问，Angular 服务的主要用途之一是与应用程序的后端通信，使用**表示状态传输**（**REST**）协议。

让我们通过准备我们的项目以使用其后端来实际了解这个功能。

首先，让我们通过访问`gym-diary-backend`文件夹并在您的命令行提示符中使用以下命令来本地上传后端：

```js
npm start
```

我们可以保留这个命令运行，并现在可以创建用于消费 API 的服务。

为了执行这种消费，Angular 有一个专门的服务——`HttpClient`。要使用它，我们首先将其模块导入到`app.module.ts`文件中：

```js
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { AppRoutingModule } from './app-routing.module';
import { HttpClientModule } from '@angular/common/http';
import { AppComponent } from './app.component';
@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule, AppRoutingModule, HttpClientModule],
  providers: [],
  bootstrap: [AppComponent],
})
export class AppModule {}
```

我们项目的后端 API 返回一些 JSON，包含当天的练习列表。作为良好的实践，我们应该创建一个界面来简化我们在前端应用程序中输入和操作结果。在`exercise-set.ts`文件中，我们将添加以下接口：

```js
export interface ExerciseSetListAPI {
  hasNext: boolean;
  items: ExerciseSetList;
}
```

现在我们可以重构我们的`ExerciseSetsService`服务以使用`HttpClient`：

```js
export class ExerciseSetsService {
  private httpClient = inject(HttpClient);
  private url = 'http://localhost:3000/diary';
  getInitialList(): Observable<ExerciseSetListAPI> {
    return this.httpClient.get<ExerciseSetListAPI>(this.url);
  }
  refreshList(): Observable<ExerciseSetListAPI> {
    return this.httpClient.get<ExerciseSetListAPI>(this.url);
  }
}
```

首先，我们使用`inject`函数将`HttpClient`服务注入到我们的类中。然后我们创建`url`变量来包含该服务将用于其方法的端点。

最后，我们将`getInitialList`和`refreshList`方法重构为消费项目的 API。最初，它们有相同的实现，但我们将在整个书中改进这段代码。

进行了一个重要的更改，使得该方法不返回练习列表，而是一个包含练习列表的 Observable。这是因为涉及消费 REST API 的操作是异步的，通过使用 RxJS 及其 Observables，Angular 处理这种异步性。我们将在*第九章*中更深入地探讨这个主题，*使用 RxJS 探索反应性*。

使用`HttpClient`服务消费*GET 类型*API，我们声明由`ExerciseSetListAPI`类型表示的返回类型和服务的`get`方法，将我们要消费的端点的 URL 作为参数传递。

现在我们添加其他方法以完善我们的服务：

```js
addNewItem(item: ExerciseSet): Observable<ExerciseSet> {
  return this.httpClient.post<ExerciseSet>(this.url, item);
 }
updateItem(id: string, item: ExerciseSet): Observable<ExerciseSet> {
  return this.httpClient.put<ExerciseSet>(`${this.url}/${id}`, item);
  deleteItem(id: string): Observable<boolean> {
    return this.httpClient.delete<boolean>(`${this.url}/${id}`);
  }
}
```

对于包含一个新的集合，我们使用服务中的`POST`方法，该方法使用同名的动词调用 API。我们始终传递 URL，在这种情况下，请求正文将是新的练习集合。

要更新集合，我们使用带有正文的`PUT`方法，并使用字符串插值传递 API 合同中要求的`id`值。最后，为了删除，我们使用`DELETE`方法，并使用插值传递我们想要删除的元素的`id`值。

让我们调整我们的`DiaryComponent`组件以消费重构后的服务。我们的挑战是如何处理通过 HTTP 请求消费 REST API 的异步性。

首先，让我们调整练习列表的初始化：

```js
@Component({
  templateUrl: './diary.component.html',
  styleUrls: ['./diary.component.css'],
})
export class DiaryComponent implements OnInit {
  private exerciseSetsService = inject(ExerciseSetsService);
  exerciseList!: ExerciseSetList;
  ngOnInit(): void {
    this.exerciseSetsService
      .getInitialList()
      .subscribe((dataApi) => (this.exerciseList = dataApi.items));
  }
}
```

在`DiaryComponent`类中，我们将实现`OnInit`接口并创建`onInit`方法。这是 Angular 组件的生命周期事件之一，这意味着 Angular 将在构建和渲染界面时在某个时刻调用它。

`onInit`方法在构建组件后、渲染组件之前被调用。我们需要实现这个方法，因为练习列表的填充将异步发生。在`onInit`方法中实现这个初始化将确保当 Angular 开始渲染屏幕时数据已经存在。

在这个方法中，我们正在使用该服务，但由于它现在返回一个 Observable，我们需要调用 `subscribe` 方法，并在其中实现列表的初始化。由于我们正在使用智能和展示组件架构，我们可以在 `DiaryComponent` 智能组件中实现按钮方法如下：

```js
newList() {
  this.exerciseSetsService
    .refreshList()
    .subscribe((dataApi) => (this.exerciseList = dataApi.items));
}
addExercise(newSet: ExerciseSet) {
  this.exerciseSetsService
    .addNewItem(newSet)
    .subscribe((_) => this.newList());
}
deleteItem(id: string) {
  this.exerciseSetsService.deleteItem(id).subscribe(() => {
    this.exerciseList = this.exerciseList.filter(
      (exerciseSet) => exerciseSet.id !== id
    );
  });
}
newRep(updateSet: ExerciseSet) {
  const id = updateSet.id ?? '';
  this.exerciseSetsService
    .updateItem(id, updateSet)
    .subscribe();
}
```

在 `newList` 方法中，我们将它重构为通过 `refreshList` 方法获取列表元素。

在 `addExercise`、`deleteItem` 和 `newRep` 方法中，我们将之前的逻辑重构为使用 `exerciseSetsService` 服务。

# 摘要

在本章中，我们学习了 Angular 服务以及如何以简单和可重用的方式从我们的应用程序中正确隔离业务规则，以及 Angular 服务如何使用单例模式进行内存和性能优化。

我们与 Angular 的依赖注入机制进行了合作并研究，注意到能够组织和重用组件和其他服务之间的服务是多么重要。我们还学习了如何使用 `inject` 函数作为 Angular 服务的替代，以通过 Angular 的构造函数进行依赖注入。

最后，我们与服务的其中一个主要用途——与后端通信——进行了合作，并在本章中，我们开始探索将我们的前端应用程序与后端集成的过程。

在下一章中，我们将研究使用表单的最佳实践，这是用户将信息输入到我们系统中的主要方式。

# 第二部分：利用 Angular 的功能

在本部分中，你将使用 Angular 的更高级功能，并了解你如何使用此框架的常见任务。你将了解表单的最佳实践，如何正确使用 Angular 的路由机制，以及最后如何使用拦截器设计模式和 RxJS 库优化 API 消费。

本部分包含以下章节：

+   *第六章**，处理用户输入：表单*

+   *第七章**，路由和路由器*

+   *第八章**，改进后端集成：拦截器模式*

+   *第九章**，使用 RXJS 探索响应性*
