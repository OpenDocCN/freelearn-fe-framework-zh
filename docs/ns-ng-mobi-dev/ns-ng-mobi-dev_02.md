# 第二章：功能模块

我们将继续通过搭建我们的应用的核心功能模块来构建我们应用的基础，即播放器和录音机。我们还将要记住，录音功能只有在用户进行身份验证时才会被加载和可用。最后，我们将完成我们在第一章中创建的`CoreModule`中的服务的实现，*使用@NgModule 塑造*。

在本章中，我们将涵盖以下主题：

+   创建功能模块

+   应用功能的分离

+   设置`AppModule`以有效地引导，仅在我们第一个视图中需要时加载功能模块

+   使用 NativeScript 的`application-settings`模块作为我们的键/值存储

+   提供在一个地方控制我们应用的调试日志的能力

+   创建一个新的服务，该服务将使用其他服务来演示我们可扩展的架构

# 播放器和录音机模块

让我们创建两个主要功能模块的框架。请注意，我们还将`NativeScriptModule`添加到以下两个模块的导入中：

1.  `PlayerModule`：它将提供特定于播放器的服务和组件，无论用户是否经过身份验证都可以使用。

让我们创建`app/modules/player/player.module.ts`：

```ts
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

1.  `RecorderModule`：这将提供特定于录音的服务和组件，仅在用户进行身份验证并首次进入录音模式时才会加载。

让我们创建`app/modules/recorder/recorder.module.ts`：

```ts
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

# 我们数据的共享模型

在我们创建服务之前，让我们为我们的应用将使用的核心数据创建一个接口和模型实现。`TrackModel`将表示具有以下内容的单个轨道：

+   `filepath`：（到本地文件）

+   `name`：（用于我们的视图）

+   `order`：位置（用于轨道的视图列表）

+   `volume`：我们希望我们的播放器能够以不同的音量级别设置混合不同的轨道。

+   `solo`：我们是否只想在我们的混音中听到这个轨道

我们还将为我们的模型添加一个方便的构造函数，该构造函数将使用对象来初始化我们的模型。

创建`app/modules/core/models/track.model.ts`，因为它将在我们的播放器和录音机之间共享：

```ts
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

# 搭建服务 API

现在，让我们创建我们的服务将为我们的应用程序提供的 API。从`PlayerService`开始，我们可以想象以下 API 可能对管理轨道和控制播放很有用。大部分内容应该是相当不言自明的。我们以后可能会重构这个，但这是一个很好的开始：

+   `playing: boolean;`

+   `tracks: Array<ITrack>;`

+   `play(index: number): void;`

+   `pause(index: number): void;`

+   `addTrack(track: ITrack): void;`

+   `removeTrack(track: ITrack): void;`

+   `reorderTrack(track: ITrack, newIndex: number): void;`

创建`app/modules/player/services/player.service.ts`并且存根一些方法；其中一些我们可以继续实现：

```ts
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

现在，让我们按照标准导出这个服务给我们的模块。

创建`app/modules/player/services/index.ts`：

```ts
import { PlayerService } from './player.service';

export const PROVIDERS: any[] = [
  PlayerService
];

export * from './player.service';
```

最后，修改我们的`PlayerModule`以指定正确的提供者，这样我们最终的模块应该如下所示：

```ts
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

接下来，我们可以设计`RecorderService`来提供一个简单的录制 API。

创建`app/modules/recorder/services/recorder.service.ts`：

+   `record(): void`

+   `stop(): void`

```ts
// angular
import { Injectable } from '@angular/core';
@Injectable()
export class RecorderService {
  public record(): void { }
  public stop(): void { }
}
```

现在，按照标准导出这个服务给我们的模块。

创建`app/modules/recorder/services/index.ts`：

```ts
import { RecorderService } from './recorder.service';

export const PROVIDERS: any[] = [
  RecorderService
];

export * from './recorder.service';
```

最后，修改我们的`RecorderModule`以指定正确的提供者，这样我们最终的模块应该如下所示：

```ts
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

我们的两个主要功能模块已经搭好架子，准备就绪，让我们重新审视我们在第一章“使用@NgModule 进入形状”中创建的两个低级服务，并提供实现。

# 实现 LogService

日志记录是您在应用程序的开发生命周期以及在生产中想要的重要工具。它可以帮助您调试，以及获得对应用程序使用方式的重要见解。通过一个单一的路径运行所有日志也提供了一个机会，通过翻转开关重新路由所有应用程序日志到其他地方。例如，您可以使用第三方调试跟踪服务，如 TrackJS（[`trackjs.com`](https://trackjs.com)），通过 Segment（[`segment.com`](https://segment.com)）。您将希望通过日志记录运行应用程序的许多重要方面，它是一个很好的地方，可以有很多控制和灵活性。

让我们打开`app/modules/core/services/log.service.ts`并开始工作。让我们首先定义一个静态布尔值，它将作为一个简单的标志，在我们的`AppModule`中可以切换启用/禁用。让我们还添加一些有用的方法：

```ts
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

+   `error`：当我们知道某种条件是错误时，这将有助于识别我们日志中的这些位置。

+   `inspect`：有时查看对象可以帮助找到错误或帮助我们了解我们的应用程序在任何给定时刻的状态。

有了我们实现的`LogService`，我们现在将在整个应用程序和本书的其余部分中使用它，而不是直接使用控制台。

# 实现 DatabaseService

我们的`DatabaseService`需要提供几件事情：

+   一个持久存储来保存和检索我们的应用程序需要的任何数据。

+   它应该允许存储任何类型的数据；然而，我们特别希望它处理 JSON 序列化。

+   我们将要存储的所有数据的静态键。

+   静态引用保存的用户？是的，它可以。然而，这引出了一个我们将在一会儿讨论的观点。

关于第一项，我们可以使用 NativeScript 的`application-settings`模块。在底层，该模块提供了一个一致的 API，用于处理两个本机移动 API：

+   **iOS**：`NSUserDefaults`：[`developer.apple.com/reference/foundation/userdefaults`](https://developer.apple.com/reference/foundation/userdefaults)

+   **Android**：`SharedPreferences`：[`developer.android.com/reference/android/content/SharedPreferences.html`](https://developer.android.com/reference/android/content/SharedPreferences.html)

关于序列化 JSON 数据，`application-settings`模块提供了`setString`和`getString`方法，这将允许我们将其与`JSON.stringify`和`JSON.parse`一起使用。

在整个代码库中使用字符串值来引用应保持不变的相同键的几个不同位置可能会出现错误。因此，我们将保留一个类型化（用于类型安全）的静态哈希，其中包含我们的应用程序将使用的有效键。我们可能目前只知道一个（经过身份验证的用户为`'current-user'`），但创建这个将提供一个单一的地方来随着时间的推移扩展这些。

四个？我们将在一会儿讨论四个。

打开`app/modules/core/services/database.service.ts`并修改它，以提供类似于 Web 的`localStorage` API 的简化 API：

```ts
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

该服务现在提供了一种通过`setItem`存储对象的方式，该方式通过`JSON.stringify`确保对象被正确存储为字符串。它还提供了一种通过`getItem`检索值的方式，该方式还通过`JSON.parse`处理反序列化为对象。我们还有`remove` API 来简单地从持久存储中删除值。最后，我们有一个对我们持久存储将跟踪的所有有效键的静态引用。

那么，关于保存用户的静态引用呢？

我们希望能够轻松地从应用程序的任何位置访问我们经过身份验证的用户。为简单起见，我们可以在`DatabaseService`中提供一个静态引用，但我们的目标是清晰地分离关注点。由于我们知道我们将希望能够显示一个模态框，要求用户注册并解锁那些录制功能，因此管理这一点的新服务是有意义的。由于我们设计了可扩展的架构，我们可以轻松地将另一个服务添加到其中，所以现在让我们这样做！

# 创建`AuthService`来帮助处理我们应用程序的经过身份验证的状态。

对于我们的`AuthService`的一个重要考虑是要理解我们应用程序中的某些组件可能会受益于在经过身份验证状态发生变化时得到通知。这是利用 RxJS 的一个完美用例。RxJS 是一个非常强大的库，用于简化使用可观察对象处理变化的数据和事件。可观察对象是一种数据类型，您不仅可以使用它来监听事件，还可以对事件进行过滤、映射、减少，并在发生任何事情时运行代码序列。通过使用可观察对象，我们可以大大简化我们的异步开发。我们将使用一种特定类型的可观察对象，称为`BehaviorSubject`来发出我们的组件可以订阅的更改。

创建`app/modules/core/services/auth.service.ts`并添加以下内容：

```ts
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

这里有一些有趣的事情正在发生。我们立即让我们设计的另外两个服务`LogService`和`DatabaseService`开始工作。它们帮助我们检查用户是否已保存/经过身份验证，并记录结果。

当我们的服务通过 Angular 的依赖注入系统构建时，我们还调用了一个`private _init`方法。这允许我们立即检查持久存储中是否存在经过身份验证的用户。然后，我们调用一个私有的可重用方法`_notifyState`，它将在我们的`authenticated$`可观察对象上发出`true`或`false`。这将为其他组件提供一个很好的方式，通过订阅这个可观察对象，轻松地得到通知当身份验证状态发生变化时。我们已经使`_notifyState`可重用，因为我们将来要实现的登录和注册方法将能够在 UI 中显示的模态返回结果时使用它。

我们现在可以很容易地将`AuthService`添加到我们的`PROVIDERS`中，而且我们不需要做任何其他事情来确保它被添加到我们的`CoreModule`中，因为我们的`PROVIDERS`已经被添加到`CoreModule`中。

我们所需要做的就是修改`app/modules/core/services/index.ts`并添加我们的服务：

```ts
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

等等！有一件重要的事情我们想要做，以确保我们的 AuthService 初始化！

Angular 的依赖注入系统只会实例化在某处被注入的服务。虽然我们在`CoreModule`中将所有服务指定为提供者，但直到它们在某处被注入之前，它们实际上都不会被构建！

打开`app/app.component.ts`并用以下内容替换它：

```ts
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

我们通过将其指定为组件构造函数的参数来注入我们的`AuthService`。这将导致 Angular 构造我们的服务。我们代码中的所有后续注入都将接收相同的单例。

# 准备引导 AppModule

我们现在已经为我们的特性模块建立了一个良好的设置，现在是时候将它们全部汇集在我们的根`AppModule`中，负责引导我们的应用程序。

只引导初始视图所需的内容。延迟加载其余部分。

保持应用程序的引导尽可能快速是很重要的。为了实现这一点，我们只想在初始视图中引导应用程序所需的主要功能，并在需要时进行延迟加载其余部分。我们知道我们希望我们的低级服务在应用程序中随时可用和准备就绪，所以我们肯定会希望`CoreModule`是最前面的。

我们的草图中的初始视图将从播放器和列表中的 2-3 个轨迹开始，因此用户可以立即回放我们将与应用程序一起提供的预先录制轨迹的混音，以进行演示。因此，我们将指定在我们的应用程序引导时预先加载`PlayerModule`，因为这将是我们希望立即参与的主要功能。

我们将设置路由配置，当用户点击初始视图右上角的录制按钮开始录制会话时，将懒加载我们的`RecorderModule`。

考虑到这一点，我们可以设置位于`app/app.module.ts`的`AppModule`如下：

```ts
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

# 总结

在整个过程中，我们一直在努力创建一个坚实的基础来构建我们的应用程序。我们创建了一个`CoreModule`来提供一些低级服务，如日志记录和持久存储，并设计了该模块，以便根据需要轻松扩展更多服务。此外，该模块是可移植的，并且可以与您公司自己的特殊功能一起放入其他项目中。

在典型的应用程序开发中，您可能希望在整个过程中在 iOS 和/或 Android 模拟器上运行您的应用程序，以便再次检查一些设计/架构选择，这是明智的！我们还没有做到这一点，因为我们在这里预先规划了一个应用程序，并希望您专注于我们正在做出的选择以及原因。

我们还创建了我们的应用程序核心竞争力所需的两个主要功能模块，`PlayerModule`和`RecorderModule`。播放器将预先设置为加载 2-3 个已录制的轨迹，并在启动时准备好播放，因此我们将使用`PlayerModule`功能引导我们的应用程序。

我们将提供一种简单的方式，允许用户注册一个帐户，这将允许他们记录自己的轨迹以投入混音中。一旦他们登录，他们将能够通过路由进入录制模式，该模式将懒加载`RecorderModule`。

在下一章中，我们将创建我们的第一个视图，配置我们的路由，并最终看到我们的应用程序的第一印象。
