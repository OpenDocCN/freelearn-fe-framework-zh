# @ngrx/store + @ngrx/effects 用于状态管理

随着应用随时间扩展，管理任何应用的状态可能会变得麻烦。我们希望对我们的应用行为的可预测性有完全的信心，而掌握其状态是获得这种信心的关键。

状态可以广泛地定义为某人在特定时间所处的特定条件。就我们的应用而言，状态可以包括我们的播放器是否在播放，录音机是否在录音，以及轨道列表 UI 是否在混音模式中等。

将状态存储在单个位置允许你确切地知道在任何给定时刻应用的状态。没有单一的状态存储，你通常会在不同的组件和服务中找到隐藏的状态，这通常会导致随着功能的构建出现两个或更多不同的状态版本。当不同的功能需要相互交互时，这种状态的不规则增长变得更加麻烦，而这些交互可能或可能不依赖于彼此。

在本章中，我们将涵盖以下主题：

+   理解 Redux 是什么

+   理解 ngrx 是什么以及它与 Redux 的关系

+   定义应用的状态

+   集成@ngrx/store 来管理状态

+   理解@ngrx/effects 是什么

+   集成副作用来帮助我们的状态管理

+   将我们的代码库从*非活动状态*转换为*响应状态*（Mike Ryan/Brandon Roberts^(TM)）

# 理解 Redux 和集成@ngrx/store

Redux 是一个开源库，它将自己定义为 JavaScript 应用的预测状态容器。这些概念并不完全新颖，但细节是在 2015 年由 Dan Abramov 开发的，他受到了 Facebook 的 Flux 和函数式编程语言 Elm 的影响。它在 React 社区中迅速流行起来，因为 Facebook 广泛使用了它。

我们不想重新定义 Redux 是什么，所以我们将直接引用 Redux 仓库([`github.com/reactjs/redux`](https://github.com/reactjs/redux))：

应用程序的全部状态都存储在单个*store*中的对象树内。

改变状态树的唯一方法是发出一个*action*，一个描述发生了什么的对象。

要指定动作如何转换状态树，你需要编写纯*reducers*。

就这样！

这个概念相当简单且非常出色。你向系统发出动作（这些是带有表示要传递数据的有效载荷的简单字符串类型对象），这些动作最终会击中 reducer（一个纯函数）来定义这些动作如何转换状态。

重要的是不要混淆 transform 和 mutate。Redux 的一个基本概念是所有状态都是**不可变的**；因此，每个 reducer 都是一个**纯函数**。

纯函数总是对相同的参数返回相同的结果。它的执行不依赖于整个系统的状态[[`en.wikipedia.org/wiki/Pure_function`](https://en.wikipedia.org/wiki/Pure_function)]。

因此，尽管 reducer 会转换状态，但它不会对其进行修改。

深入研究，已经对变化检测系统进行了工程研究，并且与深层嵌套属性上的对象比较检查相比，对象相等性/引用检查在速度上具有优势。我们不会深入探讨这一点的理由，但你的应用程序数据流的不可变性对你的性能微调有重大影响，尤其是在 Angular 方面。

除了性能提升外，Redux 的概念进一步增强了整个代码库的解耦，导致各种分散的依赖减少。有了描述我们应用程序各种交互的动作的强大功能，我们不再需要注入显式的服务依赖来执行其 API。相反，我们可以简单地发出动作，Redux 的原则将为我们传播和处理应用程序所需的功能，同时保持一个单一且可靠的真相来源。

# @ngrx/store 是什么？

在 Angular 的重写早期（从 1.x 到 2.x+），一位核心团队成员在 Google 担任开发者倡导者，Rob Wormald，开发了**ngrx/store**，这是一个“由 RxJS 驱动的 Angular 应用程序的状态管理[系统]，灵感来自 Redux”。这个短语中的关键点是术语“**RxJS**”。因此，**ngrx**这个名字是从将“**ng**”与 Angular 结合，以及从**Rx**JS 中的“**rx**”中得来的。这个开源库迅速吸引了像 Mike Ryan、Brian Troncone 和 Brandon Roberts 这样才华横溢的贡献者，并迅速发展成为现代 Angular 应用程序的一个极其智能和强大的状态管理系统。

虽然它深受 Redux 的启发并利用了相同的概念，但在将 RxJS 作为系统连接的第一公民方面，它具有独特的不同之处。它使**Observables**在 Redux 的所有概念中完整地循环，使真正**响应式**的用户界面和应用程序成为可能。

如果这些概念对你来说都是新的，Brian Troncone 的详尽文章肯定会帮助你更好地理解，因为我们在这里无法涵盖 ngrx 的所有细节。请参阅这篇文章：

+   [`gist.github.com/btroncone/a6e4347326749f938510`](https://gist.github.com/btroncone/a6e4347326749f938510)

# 设计状态模型

在集成 ngrx 之前，首先思考一下应用程序中状态的各种方面以及它们可能属于哪个模块是个好主意。就我们的应用程序而言，这里有一个合理的起始列表（目前不是完整或详尽的）：

+   `CoreModule`：

    +   `user: any;` 与用户相关的状态：

        +   `recentUsername: string`；最近使用的成功用户名

        +   `current: any`；认证用户（如果有）

+   `MixerModule`：

    +   `mixer: any`；与 mixer 相关的状态

        +   `compositions: Array<IComposition>`；用户保存的组成列表

        +   `activeComposition: CompositionModel`；当前组成

+   `PlayerModule`：

    +   `player: any`；玩家状态的各个方面。

        +   `playing: boolean`; 音频是否正在播放。

        +   `duration: number`; 播放的总时长。

        +   `completed: boolean`; 是否播放已达到末尾并完成。这将有助于确定用户停止播放或播放器因到达末尾而自动停止之间的区别。

        +   `seeking: boolean`; 是否正在播放寻求。

+   `RecorderModule`：

    +   `recorder: RecordState`; 通过枚举简单表示的录制状态

没有特定的模块，只是我们想要观察的状态：

+   `ui: any`; 用户界面状态

    +   `trackListViewType: string`; 轨迹列表当前活动的视图切换

这里的关键点不是担心第一次就完全正确。当你第一次构建应用程序时，很难知道精确的状态模型，并且它很可能会随着时间的推移而略有变化，这是可以接受的。

目前，我们的应用程序状态更易于理解，因为我们已经构建了一个工作中的应用程序，所以这稍微容易一些。通常，在构建应用程序之前规划这一点会更困难；然而，再次提醒，不要担心第一次就完全正确！你可以很容易地在以后重构和调整它。

让我们用 ngrx 将这个状态整合到我们的应用程序中。

# 安装和集成 @ngrx/store

我们首先想安装 `@ngrx/store`：

```js
npm i @ngrx/store --save
```

我们现在可以通过 `StoreModule` 为我们的应用程序提供单个存储库。我们在 `CoreModule` 中定义这些初始状态切片，当应用程序启动时它们将可用，而每个懒加载的功能模块将在需要时添加自己的状态和减少器。

# 提供初始应用程序状态，排除任何懒加载模块状态

我们想首先定义初始应用程序状态，排除任何懒加载的功能模块状态。由于我们的 `CoreModule` 提供了 `AuthService`，它负责处理我们的用户，因此我们将 **用户** 切片视为我们应用程序初始状态的基本关键。

尤其是让我们首先定义我们的用户状态的结构。

创建 `app/modules/core/states/user.state.ts`：

```js
export interface IUserState {
  recentUsername?: string;
  current?: any;
  loginCanceled?: boolean;
}

export const userInitialState: IUserState = {};
```

我们的用户状态非常简单。它包含一个 `recentUsername`，代表最近成功认证的用户名字符串（如果用户注销并稍后返回登录，这很有用）。然后，我们有 **当前**，如果认证，将代表用户对象，如果没有，则为 null。我们还包含一个 `loginCanceled` 布尔值，因为我们推测，如果我们开始将状态作为分析数据报告，它可能对分析用户交互有用。

与认证相关的任何数据点都可能对我们应用程序的用户基础的理解至关重要。例如，了解是否要求认证来记录是否导致取消登录比注册更多，这可能会直接影响用户保留。

为了与本书中的方法保持一致，也创建 `app/modules/core/states/index.ts`：

```js
export * from './user.state';
```

现在，让我们创建我们的用户操作；创建 `app/modules/core/actions/user.action.ts`：

```js
import { Action } from '@ngrx/store';
import { IUserState } from '../states';

export namespace 

UserActions {
  const CATEGORY: string = 'User';

  export interface IUserActions {
    INIT: 

string;
    LOGIN: string;
    LOGIN_SUCCESS: string;
    LOGIN_CANCELED: string;
    LOGOUT: 

string;
    UPDATED: string;
  }

  export const ActionTypes: IUserActions = {
    INIT:           

`${CATEGORY} Init`,
    LOGIN:          `${CATEGORY} Login`,
    LOGIN_SUCCESS:  `${CATEGORY} Login Success`,
    LOGIN_CANCELED: `${CATEGORY} Login Canceled`,
    LOGOUT:         `${CATEGORY} Logout`,
    UPDATED:        

`${CATEGORY} Updated`
  };

  export class InitAction implements Action {
    type = 

ActionTypes.INIT;
    payload = null;
  }

  export class LoginAction implements Action {
    type 

= ActionTypes.LOGIN;
    constructor(public payload: { msg: string; usernameAttempt?: string}) { }
  }

  export class LoginSuccessAction implements Action {
    type = ActionTypes.LOGIN_SUCCESS;
    constructor

(public payload: any /*user object*/) { }
  }

  export class LoginCanceledAction implements Action {

  type = ActionTypes.LOGIN_CANCELED;
    constructor(public payload?: string /*last attempted username*/) { }

}

  export class LogoutAction implements Action {
    type = ActionTypes.LOGOUT;
    payload = 

null;
  }

  export class UpdatedAction implements Action {
    type = ActionTypes.UPDATED;

constructor(public payload: IUserState) { }
  }

  export type Actions =
    InitAction
    | 

LoginAction
    | LoginSuccessAction
    | LoginCanceledAction
    | LogoutAction
    | 

UpdatedAction;
}
```

然后，按照我们的标准；创建 `app/modules/core/actions/index.ts`:

```js
export * from './user.action';
```

好吧，现在这些动作有什么问题？！以下是我们定义的内容：

+   `INIT`: 当应用启动时立即初始化用户。换句话说，这个动作将用于检查持久性和在启动时将用户对象恢复到应用的状态中。

+   `LOGIN`: 开始登录序列。在我们的应用中，这将显示登录对话框。

+   `LOGIN_SUCCESS`: 由于登录是异步的，这个动作将在登录完成后分发。

+   `LOGIN_CANCELED`: 如果用户取消登录。

+   `LOGOUT`: 当用户注销时。

+   `UPDATED`: 我们将使用这个简单的动作来更新我们的用户状态。这通常不会直接分发，但将在我们稍后创建的 reducer 中使用。

你在这里看到的正式性提供了统一且强类型的结构。通过使用命名空间，我们能够通过名称唯一地识别这一组动作，即`UserActions`。这允许内部命名在许多其他我们将为懒加载模块状态创建的命名空间动作中保持一致，提供了一个很好的工作标准。`CATEGORY`是必要的，因为每个动作都必须是唯一的，不仅在这个动作集中，在整个应用中都必须是唯一的。接口帮助在使用我们的动作时提供良好的智能，同时提供类型安全。各种动作类有助于确保所有分发的动作都是新实例，并提供了一种强大的方式来强类型化我们的动作负载。这也使得我们的代码在未来易于重构。我们结构中的最后一个实用工具是底部的联合类型，它帮助我们的 reducer 确定它应该关注的适用动作。

说到那个 reducer，我们现在就创建它：`app/modules/core/reducers/user.reducer.ts`:

```js
import { IUserState, userInitialState } from '../states/user.state';
import { UserActions } from 

'../actions/user.action';

export function userReducer(
  state: IUserState = userInitialState,

action: UserActions.Actions
): IUserState {
  switch (action.type) {
    case 

UserActions.ActionTypes.UPDATED:
      return Object.assign({}, state, action.payload);
    default:

return state;
  }
}
```

Reducer 非常简单。如前所述，它是一个纯函数，它接受现有的状态以及一个动作，并返回一个新的状态（除非是默认的起始情况，否则是一个新对象）。这保持了不可变性，并保持了事物的优雅。`UPDATED` 动作将是任何动作链中最后触发的动作，并最终改变用户状态。在这种情况下，我们将保持简单，并允许我们的 `UPDATED` 动作是唯一实际改变用户状态的动作。其他动作将设置一个链，最终在需要改变用户状态时触发 `UPDATED`。你当然可以根据我们的动作设置更多的情况来改变状态；然而，在我们的应用中，这将唯一改变用户状态的动作。

*动作链？* 我们所说的 *动作链* 是什么意思？！你可能想知道如果需要，我们如何将这些动作连接起来进行交互？

# 安装和集成 @ngrx/effects

不重新定义，让我们看看 @ngrx/effects 的描述，直接来自仓库 ([`github.com/ngrx/effects`](https://github.com/ngrx/effects)):

在 `@ngrx/effects` 中，效果是动作的来源。您使用 `@Effect()` 装饰器来提示服务上的哪些可观察者是动作源，并且 `@ngrx/effects` 会自动合并您的动作流，让您可以订阅它们到存储。

为了帮助您组合新的动作源，`@ngrx/effects` 导出一个动作可观察服务，该服务会发出您应用程序中发出的每个动作。

换句话说，我们可以通过效果将我们的动作链起来，以在我们的应用程序中提供强大的数据流组合。它们允许我们在动作发出和最终更改状态之前插入应该发生的行为。最常见的情况是处理 HTTP 请求和/或其他异步操作；然而，它们有许多有用的应用。

要使用，我们首先安装 `@ngrx/effects`:

```js
npm i @ngrx/effects --save
```

现在，让我们看看我们的用户操作在效果链中的样子。

然而，为了保持命名结构的一致性，让我们将 `auth.service.ts` 重命名为 `user.service.ts`。当我们有一个在整个范围内一致的命名标准时，这有助于我们。

现在，创建 `app/modules/core/effects/user.effect.ts`:

```js
// angular
import { Injectable } from '@angular/core';

// libs
import { Store, Action } from 

'@ngrx/store';
import { Effect, Actions } from '@ngrx/effects';
import { Observable } from 

'rxjs/Observable';

// module
import { LogService } from '../../core/services/log.service';
import { 

DatabaseService } from '../services/database.service';
import { UserService } from '../services/user.service';
import { UserActions } from '../actions/user.action';

@Injectable()
export class UserEffects {

  @Effect() init$: Observable<Action> = this.actions$
    .ofType(UserActions.ActionTypes.INIT)
    .startWith(new UserActions.InitAction())
    .map(action => {
      const current = 

this.databaseService
        .getItem(DatabaseService.KEYS.currentUser);
      const recentUsername = 

this.databaseService
        .getItem(DatabaseService.KEYS.recentUsername);
      this.log.debug(`Current user: 

`, current || 'Unauthenticated');
        return new UserActions.UpdatedAction({ current, recentUsername });

});

  @Effect() login$: Observable<Action> = this.actions$
    .ofType

(UserActions.ActionTypes.LOGIN)
    .withLatestFrom(this.store)
    .switchMap(([action, state]) => {

  const current = state.user.current;
      if (current) {
        // user already logged in, just fire 

updated
        return Observable.of(
          new UserActions.UpdatedAction({ current })
        );

    } else {
        this._loginPromptMsg = action.payload.msg;
        const usernameAttempt =

action.payload.usernameAttempt
          || state.user.recentUsername;

        return 

Observable.fromPromise(
          this.userService.promptLogin(this._loginPromptMsg, 
          usernameAttempt)

        )
        .map(user => (new UserActions.LoginSuccessAction(user)))
        .catch

(usernameAttempt => Observable.of(
          new UserActions.LoginCanceledAction(usernameAttempt)

));
      }
    });

  @Effect() loginSuccess$: Observable<Action> = this.actions$

.ofType(UserActions.ActionTypes.LOGIN_SUCCESS)
    .map((action) => {
      const user = action.payload;

     const recentUsername = user.username;
      this.databaseService
        .setItem

(DatabaseService.KEYS.currentUser, user);
      this.databaseService
        .setItem

(DatabaseService.KEYS.recentUsername, recentUsername);
      this._loginPromptMsg = null; // clear, no longer 

needed
      return (new UserActions.UpdatedAction({
        current: user,
        recentUsername,

    loginCanceled: false
      }));
    });

  @Effect() loginCancel$ = this.actions$

.ofType(UserActions.ActionTypes.LOGIN_CANCELED)
    .map(action => {
      const usernameAttempt = 

action.payload;
      if (usernameAttempt) {
        // reinitiate sequence, login failed, retry

return new UserActions.LoginAction({
          msg: this._loginPromptMsg,
          usernameAttempt

});
      } else {
        return new UserActions.UpdatedAction({
          loginCanceled: true

});
      }
    });

  @Effect() logout$: Observable<Action> = this.actions$

.ofType(UserActions.ActionTypes.LOGOUT)
    .map(action => {
      this.databaseService

.removeItem(DatabaseService.KEYS.currentUser);
      return new UserActions.UpdatedAction({
        current: 

null
      });
    });

  private _loginPromptMsg: string;

  constructor(
    private 

store: Store<any>,
    private actions$: Actions,
    private log: LogService,
    private 

databaseService: DatabaseService,
    private userService: UserService
  ) { }
}
```

我们已经澄清了我们关于 `UserService` 的数据流意图，并将责任委托给这个效果链。这使我们能够以清晰和一致的方式，以及极大的灵活性和能力来组合我们的数据流。例如，我们的 `InitAction` 链现在允许我们通过以下方式自动初始化用户：

```js
.startWith(new UserActions.InitAction())
```

之前，我们在服务构造函数内部调用了一个私有方法--`this._init()`--但是，一旦模块启动，我们不再需要这样的显式调用，因为效果会被运行并排队。`.startWith` 操作符将导致可观察者一次性触发（在模块创建时），允许在应用程序初始化的特定有利时机执行初始化序列。我们的初始化序列与我们之前在服务中处理的是相同的；然而，这次我们考虑了新的 `recentUsername` 持久值（如果存在）。然后我们以 `UserActions.UpdatedAction` 结束初始化序列：

```js
new UserActions.UpdatedAction({ current, recentUsername })
```

注意，没有效果链连接到 `UserActions.ActionTypes.UPDATED`。这是因为当 `Action` 发生时，不应该发生任何副作用。由于没有更多的副作用，可观察序列最终会进入具有 `switch` 语句以处理它的还原器：

```js
export function userReducer(
  state: IUserState = userInitialState,
  action: UserActions.Actions
): 

IUserState {
  switch (action.type) {
    case UserActions.ActionTypes.UPDATED:

return Object.assign({}, state, action.payload);
    default:
      return state;
  }
}
```

这将获取有效负载（其类型为用户状态形状，`IUserState`），并覆盖现有状态中的值以返回一个新的用户状态。重要的是，`Object.assign` 允许源对象中任何现有的值不被覆盖，除非显式由传入的有效负载定义。这允许只有新的传入有效负载值反映在我们的状态上，同时仍然保持现有值。

我们`UserEffect`链的其余部分相当直观。主要的是，它处理了服务之前处理的大部分内容，除了提示登录对话框，效果链正在使用服务方法来完成。然而，值得一提的是，我们可以完全移除这个服务，因为`promptLogin`方法的内容现在可以直接在我们的效果中执行。

当决定是否在效果或指定服务中处理更多逻辑时，这实际上取决于个人偏好和/或可扩展性。如果你有相当长的服务逻辑，并且在与效果一起工作时需要处理多个方法，创建一个指定服务将非常有帮助。你可以将更多功能扩展到服务中，而不会稀释你的效果链的清晰度。

最后，有了专门的服务和更多的逻辑，单元测试将会更容易。在这种情况下，我们的逻辑相当简单；然而，为了示例目的以及最佳实践，我们还将保留`UserService`。

说到这里，让我们看看我们的`UserService`现在看起来有多简化

在`app/modules/core/services/user.service.ts`中：

```js
// angular
import { Injectable } from '@angular/core';

// app
import { DialogService } from 

'./dialog.service';

@Injectable()
export class UserService {

  constructor(

private dialogService: DialogService
  ) { } 

  public promptLogin(msg: string, username: string = ''): 

Promise<any> {
    return new Promise((resolve, reject) => {
      this.dialogService.login(msg, 

username, '').then((input) => {
        if (input.result) { // result will be false when canceled
          if 

(input.userName && input.userName.indexOf('@') > -1) {
            if (input.password) {

    resolve({
                username: input.userName,
                password: input.password

  });
            } else {
              this.dialogService.alert('You must provide a password.')

     .then(reject.bind(this, input.userName));
            }
          } else {
            // reject, 

passing userName back to try again
            this.dialogService.alert('You must provide a valid email 

   address.')
              .then(reject.bind(this, input.userName));
          }
        } else {

     // user chose cancel
          reject(false);
        }
      });
    });
  }
}
```

现在要干净多了。好吧，那么我们如何让我们的应用知道所有这些新功能呢？

首先，让我们遵循我们的一个标准，为整个核心模块添加一个索引；添加`app/modules/core/index.ts`：

```js
export * from './actions';
export * from './effects';
export * from './reducers';
export * from 

'./services';
export * from './states';
export * from './core.module';
```

我们简单地导出核心模块现在提供的所有好东西，包括模块本身。

然后，打开`app/modules/core/core.module.ts`来完成我们的连接：

```js
// nativescript
import { NativeScriptModule } from 'nativescript-angular/nativescript.module'; 
import { 

NativeScriptFormsModule } from 'nativescript-angular/forms'; 
import { NativeScriptHttpModule } from 'nativescript-

angular/http'; 

// angular
import { NgModule, Optional, SkipSelf } from '@angular/core';

// libs
import { StoreModule } from '@ngrx/store';
import { 

EffectsModule } from '@ngrx/effects';

// app
import { UserEffects } from 

'./effects';
import { userReducer } from './reducers';
import { PROVIDERS } from 

'./services';
import { PROVIDERS as MIXER_PROVIDERS } from '../mixer/services';
import { PROVIDERS as 

PLAYER_PROVIDERS } from '../player/services';

const MODULES: any[] = [
  NativeScriptModule,

NativeScriptFormsModule,
  NativeScriptHttpModule
];

@NgModule({
  imports: [

...MODULES,
    // define core app state
    StoreModule.forRoot({
 user: 

userReducer
 }),
    // register core effects

EffectsModule.forRoot([
      UserEffects
    ]), 
  ],
  providers: [

...PROVIDERS,
    ...MIXER_PROVIDERS,
    ...PLAYER_PROVIDERS
  ],
  exports: [
    ...MODULES
  ]
})
export class CoreModule {
  constructor (@Optional() @SkipSelf() parentModule: CoreModule) 

{
    if (parentModule) {
      throw new Error(
        'CoreModule is already loaded. Import it in the 

AppModule only');
    }
  }
}
```

在这里，我们确保定义我们的`user`状态键以使用`userReducer`，并将其与`StoreModule`注册。然后我们调用`EffectsModule.forRoot()`，使用一系列单例效果提供者进行注册，如我们的`UserEffects`。

现在，让我们看看这如何改进代码库的其余部分，因为我们无疑在几个地方注入了`UserService`（之前命名为`AuthService`）。

我们之前在`AppComponent`中注入`AuthService`，以确保 Angular 的依赖注入在应用启动时尽早构建它，创建应用所需的单例。然而，随着`UserEffects`现在在启动时自动运行，它反过来注入（现在重命名为）`UserService`，我们不再需要这个相当愚蠢的必要性，因此，我们可以更新`AppComponent`如下：

```js
@Component({
  moduleId: module.id,
  selector: 'my-app',
  templateUrl: 'app.component.html',
})
export class AppComponent {

  constructor() { // we removed AuthService (UserService) here  
```

一举一动，我们的代码库现在变得更聪明、更精简。让我们继续看看我们的 ngrx 集成带来的其他好处。

打开`app/auth-guard.service.ts`，我们现在可以做出以下简化：

```js
import { Injectable } from '@angular/core';
import { Route, CanActivate, CanLoad } from 

'@angular/router';

// libs
import { Store } from '@ngrx/store';
import { Subscription } from 'rxjs/Subscription';

// app
import { IUserState, 

UserActions } from '../modules/core';

@Injectable()
export class AuthGuard implements 

CanActivate, CanLoad {

  private _sub: Subscription;

  constructor(private 

store: Store<any>) { }

  canActivate(): Promise<boolean> {
    return new Promise

((resolve, reject) => {
      this.store.dispatch(
 new 

UserActions.LoginAction({ msg: 'Authenticate to record.' })
 );
 this._sub = this.store.select(s => s.user).subscribe((state: 
      IUserState) => {
 if (state.current) {
 this._reset();
 resolve

(true);
 } else if (state.loginCanceled) {
 this._reset

();
 resolve(false);
 }
 });
    });
  }

  canLoad(route: Route): Promise<boolean> {
    // reuse same 

logic to activate
    return this.canActivate();
  }

  private _reset() {
 if (this._sub) this._sub.unsubscribe();
 }
}
```

当激活`/record`路由时，由于我们需要一个经过身份验证的用户来使用录制功能，我们每次都会分发`LoginAction`。我们的登录效果链正确处理了用户已经经过身份验证的情况，所以我们只需要设置我们的状态订阅以相应地做出反应。

Ngrx 是灵活的，你如何设置你的动作和效果链完全取决于你。

# 提供懒加载的功能模块状态

我们现在可以将可扩展的 ngrx 结构构建到我们的各个功能模块中，这将提供状态。从 `MixerModule` 开始，让我们用以下内容修改 `app/modules/mixer/mixer.module.ts`：

```js
...
// libs
import { StoreModule } from '@ngrx/store';
...

@NgModule({
  imports: [
    PlayerModule,
    SharedModule,

NativeScriptRouterModule.forChild(routes),
    StoreModule.forFeature('mixerModule', {
 mixer: {}     // TODO: add reducer when ready
 })
  ],
  ...
})
export class MixerModule { }
```

在这里，我们定义了 `MixerModule` 状态将提供的内容。现在，让我们定义其形状；创建

`app/modules/mixer/states/mixer.state.ts`：

```js
import { IComposition } from '../../shared/models';

export interface IMixerState {
  compositions?: 

Array<IComposition>;
  activeComposition?: any;
}

export const mixerInitialState: IMixerState = 

{
  compositions: []
};
```

为了保持本书中的一致性，也创建 `app/modules/mixer/states/index.ts`：

```js
export * from './mixer.state';
```

现在，让我们创建我们的混音动作；创建 `app/modules/mixer/actions/mixer.action.ts`：

```js
import { ViewContainerRef } from '@angular/core';
import { Action } from '@ngrx/store';
import { 

IMixerState } from '../states';
import { IComposition, CompositionModel, TrackModel } from '../../shared/models';

export namespace MixerActions {
  const CATEGORY: string = 'Mixer';

  export interface 

IMixerActions {
    INIT: string;
    ADD: string;
    EDIT: string;
    SAVE: string;
    CANCEL: 

string;
    SELECT: string;
    OPEN_RECORD: string;
    UPDATE: string;
    UPDATED: string;
  }

  export const ActionTypes: IMixerActions = {
    INIT: `${CATEGORY} Init`,
    ADD: `${CATEGORY} 

Add`,
    EDIT: `${CATEGORY} Edit`,
    SAVE: `${CATEGORY} Save`,
    CANCEL: `${CATEGORY} Cancel`,

SELECT: `${CATEGORY} Select`,
    OPEN_RECORD: `${CATEGORY} Open Record`,
    UPDATE: `${CATEGORY} Update`,

   UPDATED: `${CATEGORY} Updated`,
  };

  export class InitAction implements Action {
    type = 

ActionTypes.INIT;
    payload = null;
  }

  export class AddAction implements Action {
    type = 

ActionTypes.ADD;
    payload = null;
  }

  export class EditAction implements Action {
    type = 

ActionTypes.EDIT;
    constructor(public payload: CompositionModel) { }
  }

  export class SaveAction 

implements Action {
    type = ActionTypes.SAVE;
    constructor(public payload?: Array<CompositionModel>) 

{ }
  }

  export class CancelAction implements Action {
    type = ActionTypes.CANCEL;

payload = null;
  }

  export class SelectAction implements Action {
    type = ActionTypes.SELECT;
    constructor(public payload: CompositionModel) { }
  }

  export class OpenRecordAction implements 

Action {
    type = ActionTypes.OPEN_RECORD;
    constructor(public payload?: { 
      vcRef: 

ViewContainerRef, track?: TrackModel 
    }) { }
  }

  export class UpdateAction implements Action 

{
    type = ActionTypes.UPDATE;
    constructor(public payload: CompositionModel) { }
  }

export class UpdatedAction implements Action {
    type = ActionTypes.UPDATED;
    constructor(public payload: 

IMixerState) { }
  }

  export type Actions =
    InitAction
    | AddAction
    | 

EditAction
    | SaveAction
    | CancelAction
    | SelectAction
    | OpenRecordAction
    | 

UpdateAction
    | UpdatedAction;
}
```

与我们的 UserActions 类似，我们也将使用一个 `INIT` 动作来自动使用用户保存的合成（或我们的示例演示合成）初始化此状态。以下是简要概述：

+   `INIT`: 在应用启动时立即初始化混音。就像我们使用 `UserActions` 一样，这个动作将用于检查持久性并在启动时将任何用户保存的合成恢复到混音状态。

+   `ADD`: 显示添加新合成对话框。

+   `EDIT`: 通过提示对话框编辑合成的名称。

+   `SAVE`: 保存合成。

+   `CANCEL`: 通用动作，用于取消任何效果链。

+   `SELECT`: 选择一个合成。我们将使用此动作来驱动 Angular 路由到主选定的合成视图。

+   `OPEN_RECORD`: 处理打开录音视图的准备，包括检查认证、暂停播放（如果正在播放）以及在模态中打开或将其路由到。

+   `UPDATE`: 启动对合成的更新。

+   `UPDATED`: 这通常不会直接分发，但会在效果序列的末尾使用，以便还原器最终更改混音状态。

现在，我们可以创建一个与我们的用户还原器类似的还原器：

```js
import { IMixerState, mixerInitialState } from '../states';
import { MixerActions } from '../actions';

export function mixerReducer(
  state: IMixerState = mixerInitialState,
  action: MixerActions.Actions
): 

IMixerState {
  switch (action.type) {
    case MixerActions.ActionTypes.UPDATED:
      return 

Object.assign({}, state, action.payload);
    default:
      return state;
  }
}
```

然后，让我们在 `app/modules/mixer/effects/mixer.effect.ts` 中创建我们的 `MixerEffects`：

```js
// angular
import { Injectable, ViewContainerRef } from '@angular/core';

// nativescript
import { RouterExtensions } from 'nativescript-angular/router';

// libs
import { Store, Action } from 

'@ngrx/store';
import { Effect, Actions } from '@ngrx/effects';
import { Observable } from 

'rxjs/Observable';

// module
import { CompositionModel } from '../../shared/models';
import { 

PlayerActions } from '../../player/actions';
import { RecordComponent } from 

'../../recorder/components/record.component';
import { MixerService } from '../services/mixer.service';
import { 

MixerActions } from '../actions';

@Injectable()
export class MixerEffects {

  @Effect() 

init$: Observable<Action> = this.actions$
    .ofType(MixerActions.ActionTypes.INIT)
    .startWith(new 

MixerActions.InitAction())
    .map(action =>
      new MixerActions.UpdatedAction({

compositions: this.mixerService.hydrate(
          this.mixerService.savedCompositions()
          || 

this.mixerService.demoComposition())
      })
    );

  @Effect() add$: Observable<Action> = 

this.actions$
    .ofType(MixerActions.ActionTypes.ADD)
    .withLatestFrom(this.store)
    .switchMap

(([action, state]) =>
      Observable.fromPromise(this.mixerService.add())
        .map(value => {

       if (value.result) {
            let compositions = [...state.mixerModule.mixer.compositions];

let composition = new CompositionModel({
              id: compositions.length + 1,
              name: 

value.text,
              order: compositions.length // next one in line
            });

compositions.push(composition);
            // persist changes
            return new MixerActions.SaveAction

(compositions);
          } else {
            return new MixerActions.CancelAction();
          }

   })
    );

  @Effect() edit$: Observable<Action> = this.actions$
    .ofType

(MixerActions.ActionTypes.EDIT)
    .withLatestFrom(this.store)
    .switchMap(([action, state]) => {

  const composition = action.payload;
      return Observable.fromPromise(this.mixerService.edit(composition.name))
        .map(value => {
          if (value.result) {
            let compositions = 

[...state.mixerModule.mixer.compositions];
            for (let i = 0; i < compositions.length; i++) {

      if (compositions[i].id === composition.id) {
                compositions[i].name = value.text;

    break;
              }
            }
            // persist changes
            return new 

MixerActions.SaveAction(compositions);
          } else {
            return new MixerActions.CancelAction();
          }
        })
      });

  @Effect() update$: Observable<Action> = this.actions

$
    .ofType(MixerActions.ActionTypes.UPDATE)
    .withLatestFrom(this.store)
    .map(([action, state]) 

=> {
      let compositions = [...state.mixerModule.mixer.compositions];
      const composition = 

action.payload;
      for (let i = 0; i < compositions.length; i++) {
        if (compositions[i].id === 

composition.id) {
          compositions[i] = composition;
          break;
        }
      }

 // persist changes
      return new MixerActions.SaveAction(compositions);
    });

  @Effect() 

select$: Observable<Action> = this.actions$
    .ofType(MixerActions.ActionTypes.SELECT)
    .map(action 

=> {
      this.router.navigate(['/mixer', action.payload.id]);
      return new MixerActions.UpdatedAction

({
        activeComposition: action.payload
      });
    });

  @Effect({ dispatch: false }) 

openRecord$: Observable<Action> = this.actions$
    .ofType(MixerActions.ActionTypes.OPEN_RECORD)

.withLatestFrom(this.store)
    // always pause/reset playback before handling
    .do(action => new 

PlayerActions.PauseAction(0))
    .map(([action, state]) => {
      if 

(state.mixerModule.mixer.activeComposition &&
        state.mixerModule.mixer.activeComposition.tracks.length) 

{
        // show record modal but check authentication
        if (state.user.current) {
          if 

(action.payload.track) {
            // rerecording
            this.dialogService
              .confirm

(
                'Are you sure you want to re-record this track?'
              ).then((ok) => {

        if (ok) 
                  this._showRecordModal(
                    action.payload.vcRef, 

            action.payload.track
                  );
              });
          } else {

this._showRecordModal(action.payload.vcRef);
          }
        } else {
          this.store.dispatch(
            new UserActions.LoginToRecordAction(action.payload));
        }
      } else {
        // 

navigate to it
        this.router.navigate(['/record']);
      }
      return action;
    });

  @Effect() save$: Observable<Action> = this.actions$
    .ofType(MixerActions.ActionTypes.SAVE)

.withLatestFrom(this.store)
    .map(([action, state]) => {
      const compositions = action.payload || 
                           state.mixerModule.mixer.compositions;
      // persist
      this.mixerService.save

(compositions);
      return new MixerActions.UpdatedAction({ compositions });
    });

  constructor

(
    private store: Store<any>,
    private actions$: Actions,
    private router: 

RouterExtensions,
    private dialogService: DialogService,
    private mixerService: MixerService
  ) { }

  private _showRecordModal(vcRef: ViewContainerRef, track?: TrackModel) {
    let context: any = { 

isModal: true };
    if (track) {
      // re-recording track
      context.track = track;
    }

  this.dialogService.openModal(
      RecordComponent,
      vcRef,
      context,

'./modules/recorder/recorder.module#RecorderModule'
    );
  }
}
```

可能最有趣的效果是 `openRecord$` 链。我们使用 `@Effect({ dispatch: false })` 来指示它不应该在末尾分发任何动作，因为我们正在使用它直接执行工作，例如检查用户是否已认证或 `activeComposition` 是否包含轨道，以便有条件地在模态中打开记录视图或将其作为路由。我们使用了另一个操作符：

```js
.do(action => new PlayerActions.PauseAction(0))
```

这允许我们插入任意动作而不影响事件序列。在这种情况下，我们确保当用户尝试打开记录视图时播放总是暂停（因为他们在播放时可以尝试打开记录视图）。我们通过这个链提供了一些更高级的使用选项，只是为了展示什么是可能的。我们也在向前迈进，因为我们还没有展示 `PlayerActions` 的创建；然而，我们将在本章中只展示几个亮点。

通过这个效果链，我们可以简化我们的 `MixerService`，如下所示：

```js
...
@Injectable()
export class MixerService {
  ...
  public add() {
    return 

this.dialogService.prompt('Composition name:');
  }

  public edit(name: string) {

return this.dialogService.prompt('Edit name:', name);
  }
  ...
```

我们简化了服务逻辑，将大部分结果处理工作留在了效果链中。你可能会决定在服务中保留更多逻辑，并保持效果链更简单；然而，我们设计这个设置作为一个示例，以展示 ngrx 的更多灵活设置。

为了完成我们的懒加载状态处理，确保这些效果被运行；当 `MixerModule` 加载时，我们可以对这些模块进行调整：

```js
...
// libs
import { StoreModule } from '@ngrx/store';
import { EffectsModule } from 

'@ngrx/effects';
...
import { MixerEffects } from './effects';
import 

{ mixerReducer } from './reducers';

@NgModule({
  imports: [
    PlayerModule,

SharedModule,
    NativeScriptRouterModule.forChild(routes),
    // mixer state
    StoreModule.forFeature

('mixerModule', {
      mixer: mixerReducer
    }),
    // mixer effects

EffectsModule.forFeature([
      MixerEffects
    ])
  ],
  ...
})
export 

class MixerModule { }
```

现在，让我们看看这是如何改善我们的组件处理的，从 `app/modules/mixer/components/mixer.component.ts` 开始：

```js
...
export class MixerComponent implements OnInit, OnDestroy {
  ...
  constructor( private store: Store<any>,
    private vcRef: ViewContainerRef  ) { }

  ngOnInit() 

{
    this._sub = this.store.select(s => s.mixerModule.mixer)
 .subscribe

((state: IMixerState) => {
 this.composition = state.activeComposition;
 });
  }

  public record(track?: TrackModel) {

this.store.dispatch(new MixerActions.OpenRecordAction({
 vcRef: this.vcRef,
 track
 }));
  }

  ngOnDestroy() {
 this._sub.unsubscribe();
 }
}
```

这次，在 `ngOnInit` 中，我们只是将组件设置为对混合器的状态做出响应，将组成设置为 `activeComposition`。这保证它总是用户当前选中并正在工作的任何组成。我们在 `record` 方法内部触发 `OpenRecordAction`，传递适当的 `ViewContainerRef` 和一个跟踪，如果用户正在重新录制。

接下来是对 `app/modules/mixer/components/mix-list.component.ts` 的简化：

```js
// angular
import { Component } from '@angular/core';

// libs
import { Store } from 

'@ngrx/store';
import { Observable } from 'rxjs/Observable';

// app
import { MixerActions } from '../actions';
import { IMixerState } from '../states';

@Component({
  moduleId: module.id,
  selector: 'mix-list',
  templateUrl: 'mix-list.component.html'
})
export class MixListComponent {
  public mixer$: Observable<IMixerState>;

  constructor(private store: Store<any>) {
    this.mixer$ = store.select(s => s.mixerModule.mixer);
  } 

  public add() {
    this.store.dispatch(new MixerActions.AddAction());
  }

  public edit(composition) {
    this.store.dispatch(new MixerActions.EditAction(composition));
  }

  public select(composition) {
    this.store.dispatch(new MixerActions.SelectAction(composition));
  }
}
```

我们已经移除了 `MixerService` 注入，并通过设置状态可观察对象--`mixer$`--使其变得响应式，并集成了我们的 `MixerActions`。这使得组件变得更轻量，更容易测试和维护，因为它不再有对 `MixerService` 的显式依赖，之前它还被用于视图绑定。如果我们查看视图，我们现在可以利用 Angular 的异步管道通过状态访问用户保存的组成：

```js
<ActionBar title="Compositions" class="action-bar">
  <ActionItem (tap)="add()" 

ios.position="right">
    <Button text="New" class="action-item"></Button>

</ActionItem>
</ActionBar>
<ListView [items]="(mixer$ | async)?.compositions | 

orderBy: 'order'" class="list-group">
  <ng-template let-composition="item">
    <GridLayout 

rows="auto" columns="100,*,auto" class="list-group-item">
      <Button text="Edit" (tap)="edit(composition)" 

row="0" col="0"></Button>
      <Label [text]="composition.name" (tap)="select(composition)" row="0" 

col="1" class="h2"></Label>
      <Label [text]="composition.tracks.length" row="0" col="2" class="text-

right"></Label>
    </GridLayout>
  </ng-template>
</ListView>
```

根据官方文档：Angular 的异步管道订阅一个可观察对象或 Promise，并返回它发出的最新值。当有新值发出时，异步管道标记组件以检查更改。当组件被销毁时，异步管道会自动取消订阅，以避免潜在的内存泄漏。

这确实非常出色且极其方便，使我们能够创建高度可维护和灵活的响应式组件。

# 检查代码！自行探索更多

由于我们之前看到的大部分内容都是应用于我们代码库其余部分的相同原则，为了避免进一步增加本书的篇幅，我们邀请你探索本书附带的代码仓库中同一章节分支上的 ngrx 集成。

通过查看实际代码、运行它，甚至逐步执行它，希望你能对 ngrx 如何融入你的应用以及它能够带来的许多优势有一个坚实的理解。

社区很幸运有像 Rob Wormald、Mike Ryan、Brian Troncone、Brandon Roberts 等成员，他们帮助使 ngrx 的使用变得如此愉快，因此向所有贡献者表示衷心的感谢！

# 摘要

希望你已经开始在整合 ngrx 的过程中看到数据流简化和清晰化的模式。它通过为各种可能发生（从懒加载模块或非懒加载模块）的动作提供一致的效果链，帮助减少了代码量，并改善了数据流。通过减少管理显式注入依赖项的开销，并转而依赖 Store 和 Actions 来启动适当的工作，我们正在提高我们应用的维护性和可扩展性。除此之外，它还在为有效的可测试性铺平道路，这将在第十二章 单元测试中进行介绍。

本章强调了将 NativeScript 与 Angular 结合使用时的额外优势，通过开放与丰富库如 ngrx 的集成潜力，以改善我们的应用架构和数据流。

这已经期待已久，我们对即将到来的第十一章 Polish with SASS 感到无比兴奋。终于，我们准备好对我们的应用进行打磨，让它散发出那独特的火花！
