# 功能模块

我们将通过构建应用程序所需的核心功能模块（播放器和录音器）的基础来继续构建我们应用程序的基础。我们还将注意，录音功能只有在用户认证并首次进入录音模式时才会加载和可用。最后，我们将完成从我们在第一章中创建的 `CoreModule` 的服务实现，*使用 @NgModule 进入形状*。

在本章中，我们将涵盖以下主题：

+   创建功能模块

+   应用程序功能的关注点分离

+   设置 `AppModule` 以高效引导，仅加载我们首次视图所需的功能模块

+   使用 NativeScript 的 `application-settings` 模块作为我们的键/值存储

+   在一个地方提供控制我们应用程序调试日志的能力

+   创建一个新服务，该服务将使用其他服务来展示我们的可扩展架构

# 播放器和录音模块

让我们创建我们两个主要功能模块的框架。请注意，我们还向以下模块的导入中添加了 `NativeScriptModule`：

1.  `PlayerModule`: 它将提供播放器特定的服务和组件，无论用户是否认证，都将可用。

让我们创建 `app/modules/player/player.module.ts`:

```js
// nativescript
import { NativeScriptModule } from 'nativescript-angular/nativescript.module'; 
// angular
import { NgModule, NO_ERRORS_SCHEMA } from '@angular/core';

@NgModule({
  imports: [ NativeScriptModule ]
  schemas: [ NO_ERRORS_SCHEMA ]
})
export class PlayerModule { }
```

1.  `RecorderModule`: 这将提供仅当用户认证并首次进入录音模式时才会加载和提供的录音特定服务和组件。

让我们创建 `app/modules/recorder/recorder.module.ts`:

```js
// nativescript
import { NativeScriptModule } from 'nativescript-angular/nativescript.module'; 

// angular
import { NgModule, NO_ERRORS_SCHEMA } from '@angular/core';

@NgModule({
  imports: [ NativeScriptModule ],
  schemas: [ NO_ERRORS_SCHEMA ]
})
export class RecorderModule { }
```

# 我们数据的一个共享模型

在我们创建服务之前，让我们为我们的应用程序将使用的数据的核心部分创建一个接口和模型实现。`TrackModel` 将代表一个单独的轨道，如下所示：

+   `filepath`: （到本地文件）

+   `name`: （用于我们的视图）

+   `order`: 位置（用于轨道查看列表）

+   `volume`: 我们希望我们的播放器能够通过不同的音量级别设置混合不同的轨道

+   `solo`: 我们是否只想在我们的混合中听到这个轨道

我们还将向我们的模型添加一个方便的构造函数，它将接受一个对象来初始化我们的模型。

创建 `app/modules/core/models/track.model.ts`，因为它将在我们的播放器和录音器之间共享：

```js
export interface ITrack {
  filepath?: string;
  name?: string;
  order?: number;
  volume?: number;
  solo?: boolean;
}
export class TrackModel implements ITrack {
  public filepath: string;
  public name: string;
  public order: number;
  public volume: number = 1; // set default to full volume
  public solo: boolean;

  constructor(model?: any) {
    if (model) {
      for (let key in model) {
        this[key] = model[key];
      }
    }
  }
}
```

# 构建服务 API 的框架

现在，让我们创建我们的服务将为我们的应用程序提供的服务 API。从 `PlayerService` 开始，我们可以想象以下 API 可能对管理轨道和控制播放很有用。其中大部分应该是相当自解释的。我们可能稍后会重构它，但这是一个很好的开始：

+   `playing: boolean;`

+   `tracks: Array<ITrack>;`

+   `play(index: number): void;`

+   `pause(index: number): void;`

+   `addTrack(track: ITrack): void;`

+   `removeTrack(track: ITrack): void;`

+   `reorderTrack(track: ITrack, newIndex: number): void;`

创建 `app/modules/player/services/player.service.ts` 并为其中的一些方法提供占位符；其中一些我们可以立即实现：

```js
// angular
import { Injectable } from '@angular/core';

// app
import { ITrack } from '../../core/models';
@Injectable()
export class PlayerService {

  public playing: boolean;
  public tracks: Array<ITrack>;

  constructor() {
    this.tracks = [];
  }

  public play(index: number): void {
    this.playing = true;
  }
  public pause(index: number): void {
    this.playing = false;
  }
  public addTrack(track: ITrack): void {
    this.tracks.push(track);
  }
  public removeTrack(track: ITrack): void {
    let index = this.getTrackIndex(track);
    if (index > -1) {
      this.tracks.splice(index, 1);
    }
  }
  public reorderTrack(track: ITrack, newIndex: number) {
    let index = this.getTrackIndex(track);
    if (index > -1) {
      this.tracks.splice(newIndex, 0, this.tracks.splice(index, 1)[0]);
    }
  }
  private getTrackIndex(track: ITrack): number {
    let index = -1;
    for (let i = 0; i < this.tracks.length; i++) {
      if (this.tracks[i].filepath === track.filepath) {
        index = i;
        break;
      }
    }
    return index;
  }
}
```

现在，让我们通过为我们模块导出此服务来应用我们的标准。

创建 `app/modules/player/services/index.ts`：

```js
import { PlayerService } from './player.service';

export const PROVIDERS: any[] = [
  PlayerService
];

export * from './player.service';
```

最后，修改我们的 `PlayerModule` 以指定正确的提供者，因此我们的最终模块应该看起来如下：

```js
// nativescript
import { NativeScriptModule } from 'nativescript-angular/nativescript.module'; 

// angular
import { NgModule, NO_ERRORS_SCHEMA } from '@angular/core';

// app
import { PROVIDERS } from './services';

@NgModule({
  imports: [ NativeScriptModule ],
  providers: [ ...PROVIDERS ],
  schemas: [ NO_ERRORS_SCHEMA ]
})
export class PlayerModule { }
```

接下来，我们可以设计 `RecorderService` 以提供简单的记录 API。

创建 `app/modules/recorder/services/recorder.service.ts`：

+   `record(): void`

+   `stop(): void`

```js
// angular
import { Injectable } from '@angular/core';
@Injectable()
export class RecorderService {
  public record(): void { }
  public stop(): void { }
}
```

现在，通过导出此服务为我们模块应用我们的标准。

创建 `app/modules/recorder/services/index.ts`：

```js
import { RecorderService } from './recorder.service';

export const PROVIDERS: any[] = [
  RecorderService
];

export * from './recorder.service';
```

最后，修改我们的 `RecorderModule` 以指定正确的提供者，因此我们的最终模块应该看起来如下：

```js
// nativescript
import { NativeScriptModule } from 'nativescript-angular/nativescript.module'; 

// angular
import { NgModule, NO_ERRORS_SCHEMA } from '@angular/core';

// app
import { PROVIDERS } from './services';

@NgModule({
  imports: [ NativeScriptModule ],
  providers: [ ...PROVIDERS ],
  schemas: [ NO_ERRORS_SCHEMA ]
})
export class RecorderModule { }
```

在我们的两个主要功能模块搭建并准备就绪后，让我们回顾一下在第一章 *Get Into Shape with @NgModule* 中创建的两个低级服务，并提供实现。

# 实现 LogService

日志是你在应用开发周期以及生产过程中都希望拥有的重要盟友。它可以帮助你调试，同时深入了解你的应用是如何被使用的。通过一个单一的路径运行所有日志也提供了一个机会，只需切换一下开关，就可以将所有应用日志重定向到其他地方。例如，你可以通过 Segment ([`segment.com`](https://segment.com)) 使用第三方调试跟踪服务，如 TrackJS ([`trackjs.com`](https://trackjs.com)))。你希望将应用的重要方面大量通过日志运行，并且它提供了一个很好的地方来拥有很多控制和灵活性。

让我们打开 `app/modules/core/services/log.service.ts` 并开始工作。让我们首先定义一个静态布尔值，它将作为一个简单的标志，我们可以在 `AppModule` 中切换以启用/禁用。同时，我们也添加一些有用的方法：

```js
import { Injectable } from '@angular/core';

@Injectable()
export class LogService {

 public static ENABLE: boolean = true;

 public debug(msg: any, ...formatParams: any[]) {
   if (LogService.ENABLE) {
     console.log(msg, formatParams);
   }
 }

 public error(msg: any, ...formatParams: any[]) {
   if (LogService.ENABLE) {
     console.error(msg, formatParams);
   }
 }

 public inspect(obj: any) {
   if (LogService.ENABLE) {
     console.log(obj);
     console.log('typeof: ', typeof obj);
     if (obj) {
       console.log('constructor: ', obj.constructor.name);
       for (let key in obj) {
         console.log(`${key}: `, obj[key]);
       }
     }
   }
  }
}
```

+   `debug`：这将作为我们最常用的日志输出 API。

+   `error`：当我们知道某个条件是错误时，这将帮助我们识别日志中的这些位置。

+   `inspect`：有时候查看一个对象可以帮助我们找到错误或帮助我们理解应用在任何给定时刻的状态。

在我们的 `LogService` 实现之后，我们现在将在整个应用以及本书的其余部分中使用它，而不是直接使用控制台。

# 实现 DatabaseService

我们的 `DatabaseService` 需要提供以下几项：

+   一个持久存储来保存和检索应用所需的所有数据。

+   它应该允许存储任何类型的数据；然而，我们特别希望它能够处理 JSON 序列化。

+   所有我们希望存储的数据的静态键。

+   一个指向已保存用户的静态引用？嗯，是的，可以。然而，这引出了一个我们将在稍后解决的问题。

关于第一项，我们可以使用 NativeScript 的 `application-settings` 模块。在底层，此模块提供了一个一致的 API 来与两个原生移动 API 一起工作：

+   **iOS**：`NSUserDefaults`：[`developer.apple.com/reference/foundation/userdefaults`](https://developer.apple.com/reference/foundation/userdefaults)

+   **Android**: `SharedPreferences`：[`developer.android.com/reference/android/content/SharedPreferences.html`](https://developer.android.com/reference/android/content/SharedPreferences.html)

关于序列化 JSON 数据，`application-settings`模块提供了`setString`和`getString`方法，这将允许我们结合使用`JSON.stringify`和`JSON.parse`。

在代码库的多个不同位置使用字符串值来引用应该保持恒定的相同键，可能会变得容易出错。因此，我们将保留一个类型化的（为了类型安全）静态哈希表，其中包含我们应用程序将使用的有效键。我们可能目前只知道一个（认证用户作为`'current-user'`），但创建这个哈希表将提供一个单一的位置来随着时间的推移扩展这些键。

四个？我们将在稍后解决四个问题。

打开`app/modules/core/services/database.service.ts`并修改它，以便提供类似于网络`localStorage` API 的类似 API，以简化操作：

```js
// angular
import { Injectable } from '@angular/core';

// nativescript
import * as appSettings from 'application-settings';

interface IKeys {
  currentUser: string;
}
@Injectable()
export class DatabaseService {

  public static KEYS: IKeys = {
    currentUser: 'current-user'
  };

  public setItem(key: string, value: any): void {
    appSettings.setString(key, JSON.stringify(value));
  }

  public getItem(key: string): any {
    let item = appSettings.getString(key);
    if (item) {
      return JSON.parse(item);
    } 
    return item;
  }

  public removeItem(key: string): void {
    appSettings.remove(key);
  }
}
```

此服务现在提供了一种通过`setItem`存储对象的方式，这确保了对象通过`JSON.stringify`正确地存储为字符串。它还提供了一种通过`getItem`检索值的方式，它还通过`JSON.parse`为我们处理序列化回对象。我们还有一个用于从持久存储中简单删除值的`remove` API。最后，我们有一个指向所有有效键的静态引用，这些键是我们持久存储将跟踪的。

那么，关于保存用户的静态引用呢？

我们希望能够在应用程序的任何地方轻松访问我们的认证用户。为了简单起见，我们可以在`DatabaseService`中提供一个静态引用，但我们的目标是实现关注点的清晰分离。既然我们知道我们将来会想要展示一个模态窗口让用户注册并解锁那些录制功能，那么创建一个新的服务来管理这一点是有意义的。由于我们已经设计了可扩展的架构，我们可以轻松地添加另一个服务，所以现在就让我们这么做吧！

# 创建`AuthService`以帮助处理我们应用程序的认证状态

对于我们的`AuthService`来说，一个重要的考虑因素是理解，我们的应用程序中的一些组件可能会从认证状态变化时得到通知。这是一个利用 RxJS 的完美用例。RxJS 是一个非常强大的库，用于通过观察者来简化处理变化的数据和事件。观察者是一种数据类型，您可以使用它不仅来监听事件，还可以在事件发生时进行过滤、映射、归约和运行代码序列。通过使用观察者，我们可以极大地简化我们的异步开发。我们将使用一种特定的观察者类型，即`BehaviorSubject`，来发出我们的组件可以订阅的变化。

创建`app/modules/core/services/auth.service.ts`并添加以下内容：

```js
// angular
import { Injectable } from '@angular/core';

// lib
import { BehaviorSubject } from 'rxjs/BehaviorSubject';

// app
import { DatabaseService } from './database.service';
import { LogService } from './log.service';

@Injectable()
export class AuthService {

 // access our current user from anywhere
 public static CURRENT_USER: any;

 // subscribe to authenticated state changes
 public authenticated$: BehaviorSubject<boolean> = 
   new BehaviorSubject(false);

 constructor(
   private databaseService: DatabaseService,
   private logService: LogService
 ) {
   this._init();
 } 

 private _init() {
   AuthService.CURRENT_USER = this.databaseService
     .getItem(DatabaseService.KEYS.currentUser);
   this.logService.debug(`Current user: `,
     AuthService.CURRENT_USER);
   this._notifyState(!!AuthService.CURRENT_USER);
 }

 private _notifyState(auth: boolean) {
   this.authenticated$.next(auth);
 }
}
```

这里有一些有趣的事情在进行。我们正在放置另外两个我们设计的立即工作的服务，`LogService`和`DatabaseService`。它们帮助我们检查用户是否被保存/认证，并记录这个结果。

我们在通过 Angular 的依赖注入系统构建我们的服务时，也会调用一个`private _init`方法。这允许我们立即检查在我们的持久存储中是否存在已认证的用户。然后，我们调用一个私有的可重用方法`_notifyState`，它将在我们的`authenticated$`可观察对象上发出`true`或`false`。这将提供一种很好的方式，让其他组件通过订阅这个可观察对象来轻松地得到认证状态变化的通知。我们使`_notifyState`可重用，因为我们的登录和注册方法（将在未来实现）将能够在从我们可能在 UI 中显示的模态返回结果时使用它。

我们现在可以轻松地将`AuthService`添加到我们的`PROVIDERS`中，并且我们不需要做任何其他事情来确保它被添加到我们的`CoreModule`中，因为我们的`PROVIDERS`已经添加到了`CoreModule`。

我们需要做的只是修改`app/modules/core/services/index.ts`并添加我们的服务：

```js
import { AuthService } from './auth.service';
import { DatabaseService } from './database.service';
import { LogService } from './log.service';

export const PROVIDERS: any[] = [
 AuthService,
 DatabaseService,
 LogService
];

export * from './auth.service';
export * from './database.service';
export * from './log.service';
```

等等！我们想要做的一件重要的事情是确保我们的 AuthService 初始化！

Angular 的依赖注入系统只会实例化在某个地方注入的服务。尽管我们已经在我们的`CoreModule`中将所有服务指定为提供者，但它们实际上只有在被注入到某个地方时才会被构建！

打开`app/app.component.ts`并将其内容替换为以下内容：

```js
// angular
import { Component } from '@angular/core';

// app
import { AuthService } from './modules/core/services';

@Component({
 selector: 'my-app',
 templateUrl: 'app.component.html',
})
export class AppComponent {

 constructor(private authService: AuthService) { }

}
```

我们通过将`AuthService`指定为组件构造函数的参数来注入我们的`AuthService`。这将导致 Angular 构建我们的服务。我们代码中的所有后续注入都将接收相同的单例。

# 准备启动 AppModule

我们现在为我们的功能模块提供了一个良好的设置，现在是时候将它们全部整合到我们的根`AppModule`中，它负责启动我们的应用程序。

只启动你初始视图所需的部分。其余部分按需懒加载。

重要的是要尽可能快地启动我们的应用程序。为了实现这一点，我们只想使用我们初始视图所需的主要功能来启动应用程序，并在需要时懒加载其余部分。我们知道我们希望我们的低级服务在任何地方都可用并准备好使用，所以我们肯定会希望`CoreModule`预先加载。

我们从草图中的初始视图将开始于播放器和列表上的 2-3 个曲目，这样用户就可以立即播放我们将与应用程序一起提供的预录制的曲目的混合。出于这个原因，当我们的应用程序启动时，我们将指定`PlayerModule`预先加载，因为它将是我们想要立即参与的主要功能。

我们将设置一个路由配置，当用户点击初始视图右上角的记录按钮以开始录音会话时，将懒加载我们的`RecorderModule`。

考虑到这一点，我们可以在`app/app.module.ts`中设置我们的`AppModule`，如下所示：

```js
// angular 
import { NgModule } from '@angular/core'; 

// app
import { AppComponent } from './app.component';
import { CoreModule } from './modules/core/core.module';
import { PlayerModule } from './modules/player/player.module'; 

@NgModule({ 
  imports: [ 
    CoreModule, 
    PlayerModule 
  ], 
  declarations: [AppComponent],
  bootstrap: [AppComponent] 
})
export class AppModule { }
```

# 摘要

在整个过程中，我们一直在努力创建一个坚实的基础来构建我们的应用程序。我们创建了一个`CoreModule`来提供一些低级服务，例如日志记录和持久存储，并设计了该模块以便在需要时轻松扩展更多服务。此外，此模块是可移植的，并且可以完整地插入到其他项目中。

在典型的应用程序开发中，你可能想在 iOS 和/或 Android 模拟器上运行你的应用程序，在这个过程中双重检查一些你的设计/架构选择，这是值得推荐的！我们还没有这样做，因为我们已经有一个预计划的应用程序，并希望你能专注于我们正在做出的选择以及为什么这样做。

我们还创建了两个主要的功能模块，这是我们的应用程序核心能力所需的，即`PlayerModule`和`RecorderModule`。播放器将预先设置，加载并准备好 2-3 条录音轨道，以便在启动时即可播放，因此我们将使用`PlayerModule`的功能来启动我们的应用程序。

我们将提供一个简单的方法，让用户能够注册账户，这样他们就可以记录自己的轨迹并添加到混合中。一旦他们登录，他们将通过一个路径进入记录模式，该路径会懒加载`RecorderModule`。

在下一章中，我们将创建我们的第一个视图，配置我们的路由，并最终，一睹我们的应用程序的真容。
