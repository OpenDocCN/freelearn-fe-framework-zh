# Angular 中的依赖注入

在本章中，我们将解释如何利用框架的**依赖注入**（**DI**）机制及其所有各种功能。

我们将探讨以下主题：

+   配置和创建提供者

+   注入由声明提供者实例化的依赖项

+   创建和配置注入器的底层 API

+   在我们的 UI 组件之间重用服务中定义的业务逻辑

# 为什么我需要依赖注入？

假设我们有一个依赖于`Engine`和`Transmission`类的`Car`类。我们如何实现这个系统？让我们看看：

```js
class Engine {...} 

class Transmission {...}

class Car { 
  engine; 
  transmission;

  constructor() { 
    this.engine = new Engine(); 
    this.transmission = new Transmission(); 
  } 
} 
```

在前面的示例中，我们在`Car`类的构造函数中创建了`Car`类的依赖项。尽管看起来很简单，但它远非灵活。每次我们创建`Car`类的实例时，在其构造函数中，都会创建相同`Engine`和`Transmission`类的实例。这可能会因为以下原因而成为问题：

+   由于我们无法独立于其`engine`和`transmission`依赖项对其进行测试，`Car`类变得难以测试。

+   我们将`Car`类与其依赖项的实例化逻辑耦合在一起。

# Angular 中的依赖注入

我们还可以通过利用依赖注入模式来解决这个问题。我们已经从 AngularJS 中熟悉了它；让我们演示如何在 Angular 的上下文中使用 DI 重构前面的代码：

```js
class Engine {...} 
class Transmission {...} 

@Injectable() 
class Car { 
  engine; 
  transmission;

  constructor(engine: Engine, transmission: Transmission) { 
    this.engine = engine; 
    this.transmission = transmission; 
  } 
} 
```

在前面的代码片段中，我们只是在`Car`类的定义上方添加了`@Injectable`类装饰器，并为构造函数的参数提供了类型注解。

# 使用依赖注入的优点

还有一个步骤尚未完成，我们将在下一节中探讨。在此之前，让我们看看这种方法的优点：

+   我们可以轻松地为测试环境或为实例化不同的`Car`模型传递`Car`类的不同依赖项版本。

+   我们与依赖项实例化的逻辑没有耦合。

`Car`类只负责实现其自身的领域特定逻辑，而不是与额外的功能耦合，例如其依赖项的管理。我们的代码也变得更加声明性，更容易阅读。

既然我们已经意识到 DI 的一些好处，让我们看看为了让这段代码工作所缺少的部分。

# 声明提供者

在我们的 Angular 应用程序中，通过框架的 DI 机制实例化单个依赖项所使用的原始数据类型称为**注入器**。注入器包含一组**提供者**，它们封装了与**令牌**关联的已注册依赖项的实例化逻辑。我们可以将令牌视为注入器内注册的不同提供者的标识符。

在 Angular 中，我们可以使用`@NgModule`声明单个依赖的提供者。内部，**Angular 将根据我们在模块中声明的提供者创建一个注入器**。

让我们看看以下代码片段，它位于`ch6/injector-basics/basics/app.ts`：

```js
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';
import {
  NgModule,
  Component,
  Inject,
  InjectionToken,
  Injectable
} from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

const BUFFER_SIZE = new InjectionToken<number>('buffer-size');

class Buffer {
  constructor(@Inject(BUFFER_SIZE) private size: Number) {
    console.log(this.size);
  }
}

@Injectable()
class Socket {
  constructor(private buffer: Buffer) {}
}

@Component({
  selector: 'app',
  template: ''
})
class AppComponent {
  constructor(private socket: Socket) {
    console.log(socket);
  }
}

@NgModule({
  providers: [{ provide: BUFFER_SIZE, useValue: 42 }, Buffer, Socket],
  declarations: [AppComponent],
  bootstrap: [AppComponent],
  imports: [BrowserModule]
})
class AppModule {}

platformBrowserDynamic().bootstrapModule(AppModule);
```

一旦你为本书设置了代码（有关说明，请参阅第五章*，开始使用 Angular 组件和指令*)并运行`npm start`，你就可以在`http://localhost:5555/dist/dev/ch6/injector-basics/basics/`地址上看到执行结果。当你打开浏览器的控制台时，你会看到以下这些行：

```js
42
 Socket {buffer: Buffer}
```

在*图 1*中，我们可以看到`AppComponent`依赖于`Socket`类，而`Socket`类依赖于`Buffer`类，`Buffer`类又依赖于`BUFFER_SIZE`类：

![](img/ce5069e4-fd32-4490-97ce-2508046d432e.png)

图 1

我们将`BUFFER_SIZE`类常量的值设置为`new InjectionToken<number>('buffer-size')`。我们可以把`BUFFER_SIZE`的值看作是一个在应用程序中不能重复的唯一标识符。"InjectionToken"是 ES2015 中的`Symbol`类的替代品，因为在编写本书的时候，TypeScript 不支持它。"InjectionToken"提供了一个`Symbol`没有的额外功能：更好的类型检查；Angular 和 TypeScript 可以使用我们传递给`InjectionToken`的类型参数（在前面的例子中是`number`）来执行更复杂的类型检查算法。

我们定义了两个类：`Buffer`和`Socket`。`Buffer`类有一个构造函数，它只接受一个名为`size`的单个依赖项，其类型为`number`。为了在依赖项解析过程中添加额外的元数据（即提示 Angular 应该注入与`BUFFER_SIZE`令牌关联的值），我们使用`@Inject`参数装饰器。这个装饰器接受我们想要注入的依赖项的令牌。通常，这个令牌是依赖项的类型（即类的引用），但在某些情况下，它可以是不同类型的值。例如，在我们的案例中，我们使用了`InjectionToken`类的实例。

# 使用`@Injectable`装饰器

现在，让我们来看看`Socket`类。我们用`@Injectable`装饰器来装饰它。这个装饰器应该被任何接受依赖并通过 Angular 的 DI 机制注入依赖的类使用。

`@Injectable`装饰器向 Angular 暗示，一个给定的类接受应该通过框架的依赖注入机制注入的参数。这意味着如果我们省略`@Injectable`装饰器，Angular 的 DI 机制将不知道在实例化类之前需要解决类的依赖。

在 Angular 5 版本之前，`@Injectable` 装饰器与 TypeScript 编译器生成带有类型信息的元数据语义不同。尽管这是一个重要的细节，但它对我们使用框架的依赖注入机制或特定的 `@Injectable` 装饰器方式没有任何影响。

作为一条经验法则，当给定的类接受需要通过 Angular 的依赖注入机制注入的依赖项时，始终使用 `@Injectable` 装饰器。

# 引入前向引用

Angular 引入了**前向引用**的概念。这是由于以下原因所必需的：

+   ES2015 类不会被提升

+   允许在声明依赖提供者之后解决声明的依赖项

在本节中，我们将解释前向引用解决的问题以及我们如何利用它们。

现在，假设我们以相反的顺序定义了 `Buffer` 和 `Socket` 类：

```js
// ch6/injector-basics/forward-ref/app.ts 

@Injectable() 
class Socket { 
  constructor(private buffer: Buffer) {...} 
} 

// undefined 
console.log(Buffer); 

class Buffer { 
  constructor(@Inject(BUFFER_SIZE) private size: Number) {...} 
} 

// [Function: Buffer] 
console.log(Buffer); 
```

在这里，我们具有与上一个示例中完全相同的依赖项；然而，在这种情况下，`Socket` 类的定义先于 `Buffer` 类的定义。请注意，在 `Buffer` 类声明之前，`Buffer` 标识符的值将是 `undefined`。这意味着在生成的 JavaScript 代码的解释过程中，`Buffer` 标识符的值将是 `undefined`：也就是说，作为依赖项的标记，框架将获得一个无效的值。

运行前面的代码片段将导致以下形式的运行时错误：

```js
Error: Cannot resolve all parameters for Socket(?). Make sure they all have valid type or annotations.
```

解决这个问题的最佳方式是通过交换定义以正确的顺序。我们还可以利用 Angular 提供的解决方案：前向引用：

```js
... 
import {forwardRef} from '@angular/core'; 
...

@Injectable() 
class Socket { 
  constructor(@Inject(forwardRef(() => Buffer)) 
    private buffer: Buffer) {} 
}

class Buffer {...} 
```

之前的代码片段展示了我们如何利用前向引用。我们只需要使用 `@Inject` 参数装饰器，并将 `forwardRef` 函数的调用结果作为参数。`forwardRef` 函数是一个高阶函数，它接受一个参数：另一个函数，该函数负责返回需要注入的依赖项（或更精确地说，与其提供者关联的依赖项）的标记。这样，框架提供了一种延迟解决依赖项（标记）类型的过程的方法。

依赖项的标记将在 `Socket` 需要实例化的第一次被解决，这与默认行为不同，在默认行为中，标记在给定类的声明时就需要。

# 配置提供者

现在，让我们看看一个与之前使用的示例类似，但具有不同语法配置的注入器示例：

```js
@NgModule({
  // ...
  providers: [ 
    { provide: BUFFER_SIZE, useValue: 42 }, 
    { provide: Buffer, useClass: Buffer }, 
    { provide: Socket, useClass: Socket } 
  ]
  // ...
})
class AppModule {}
```

在这种情况下，在提供者内部，我们明确声明我们希望使用`Buffer`类来构建与`Buffer`标识符引用相等的令牌的依赖项。我们为与`Socket`令牌关联的依赖项做完全相同的事情；然而，这次我们提供了`Socket`类。这就是 Angular 在我们省略显式提供者声明并仅传递类引用时将如何操作。

明确声明用于创建同一类实例的类可能看起来毫无价值，并且鉴于我们迄今为止看到的示例，这将是完全正确的。然而，在某些情况下，我们可能希望为与给定令牌关联的依赖项的实例化提供不同的类。

例如，假设我们有一个在名为`UserService`的服务中使用的`Http`服务：

```js
class Http {...} 

@Injectable() 
class UserService { 
  constructor(private http: Http) {} 
} 

@NgModule({
  // ...
  providers: [ 
    UserService, 
    Http 
  ]
})
class AppModule {}
```

现在，让我们追踪`UserService`服务实例化的过程，以防我们想在应用程序的某个地方注入它。

内部，Angular 将根据传递给`@NgModule`的提供者创建一个注入器：这就是 Angular 将用于实例化`UserService`服务的注入器。最初，提供者会发现`UserService`服务接受一个带有`Http`令牌的依赖项，因此提供者会尝试找到与该令牌关联的提供者。由于在同一注入器中存在这样的提供者，它将创建一个`Http`服务的实例并将其传递给`UserService`。

到目前为止一切顺利；然而，如果我们想测试`UserService`服务，我们实际上并不需要通过网络进行 HTTP 调用。在单元测试的情况下，我们可以提供一个模拟实现，它只会伪造这些 HTTP 调用。为了向`UserService`服务注入不同类的实例，我们可以将提供者的配置更改如下：

```js
class DummyHttp {...} 

// ... 

@NgModule({
  // ...
  providers: [ 
    UserService, 
    { provide: Http, useClass: DummyHttp }
  ]
})
class TestingModule {}
```

在这种情况下，Angular 将再次根据传递给`@NgModule`的提供者创建一个注入器。这次的不同之处在于，我们用`DummyHttp`服务关联了`Http`令牌。现在当注入器实例化`UserService`时，它会在它维护的提供者列表中寻找与`Http`令牌关联的提供者，并发现它需要使用`DummyHttp`服务来创建所需的依赖项。当 Angular 发现我们已声明一个`useClass`提供者时，它将使用`new DummyHttp()`创建`DummyHttp`服务的实例。

此代码位于`ch6/configuring-providers/dummy-http/app.ts`。

# 使用现有提供者

另一种进行的方式是使用提供者配置对象的`useExisting`属性：

```js
@NgModule({
  // ...
  providers: [ 
    DummyHttp, 
    { provide: Http, useExisting: DummyHttp }, 
    UserService 
  ]
})
class TestingModule {}
```

在前面的片段中，我们为三个令牌注册了提供者：`DummyHttp`、`UserService`和`Http`。我们声明我们想要将`Http`令牌绑定到现有的令牌`DummyHttp`。这意味着当请求`Http`服务时，注入器将找到用作`useExisting`属性值的令牌的提供者，并实例化它或获取与之关联的值（如果它已经被实例化）。我们可以将`useExisting`视为创建给定令牌的别名：

```js
// ch6/configuring-providers/existing/app.ts

// ...

const dummyHttp = new DummyHttp();

@Component(...)
class AppComponent {
  constructor(private service: UserService) {
    console.log(service.http === dummyHttp);
  }
}

@NgModule({
  providers: [
    { provide: DummyHttp, useValue: dummyHttp },
    { provide: Http, useExisting: DummyHttp },
    UserService
  ],
  // ...
})
class AppModule {}
```

上述片段将创建`Http`令牌到`DummyHttp`令牌的别名。这意味着一旦请求`Http`令牌，调用将被转发到与`DummyHttp`令牌关联的提供者，它将被解析为`dummyHttp`的值。

`useValue`提供者返回设置到提供者声明中`useValue`属性的值。

# 定义用于实例化服务的工厂

现在，假设我们想要创建一个复杂对象，例如，一个代表**传输层安全性**（**TLS**）连接的对象。此类对象的一些属性包括套接字、一组加密协议和证书。在这个问题的背景下，我们迄今为止所查看的 Angular 的 DI 机制的功能可能看起来有点有限。

例如，我们可能需要配置`TLSConnection`类的一些属性，而不将其实例化过程与所有配置细节耦合（选择合适的加密算法、打开我们将通过它建立安全连接的 TCP 套接字等）。

在这种情况下，我们可以利用提供者配置对象的`useFactory`属性：

```js
@NgModule({
  // ...
  providers: [
    { 
      provide: TLSConnection, 
      useFactory: function(
        socket: Socket, certificate: Certificate, crypto: Crypto) { 
          let connection = new TLSConnection(); 
          connection.certificate = certificate; 
          connection.socket = socket; 
          connection.crypto = crypto; 
          socket.open(); 
          return connection; 
        }, 
        deps: [Socket, Certificate, Crypto] 
    }, 
    { provide: BUFFER_SIZE, useValue: 42 },
    Buffer, 
    Socket, 
    Certificate, 
    Crypto 
  ]
})
class AppModule {}
```

最初的片段看起来可能有点复杂，但让我们一步一步地来看。我们可以从我们已经熟悉的部分开始：

```js
@NgModule({
  // ...
  providers: [
    // ...
    { provide: BUFFER_SIZE, useValue: 42 },
    Buffer, 
    Socket, 
    Certificate, 
    Crypto 
  ]
})
class AppModule {} 
```

初始时，我们注册了多个提供者：`Buffer`、`Socket`、`Certificate`和`Crypto`。就像在之前的例子中一样，我们也注册了`BUFFER_SIZE`令牌并将其关联到值`42`。这意味着我们已经在我们的应用程序中类的构造函数中注入了`Buffer`、`Socket`、`Certificate`和`Crypto`类型的依赖项。

我们可以通过以下方式创建和配置`TLSConnection`对象的一个实例：

```js
let connection = new TLSConnection();
connection.certificate = certificate;
connection.socket = socket;
connection.crypto = crypto;
socket.open();
return connection;
```

现在，为了允许 Angular 使用前面的片段来实例化`TLSConnection`，我们可以使用提供者配置对象的`useFactory`属性。这样，我们可以指定一个函数，在其中我们可以手动创建与提供者令牌关联的对象的实例。

我们可以使用`useFactory`属性与`deps`属性一起指定要传递给工厂的依赖项：

```js
{
  provide: TLSConnection,
  useFactory: function (socket: Socket, certificate: Certificate, crypto: Crypto) { 
    // ... 
  }, 
  deps: [Socket, Certificate, Crypto] 
}
```

在前面的代码片段中，我们定义了用于 `TLSConnection` 实例化的工厂函数。作为依赖项，我们声明了 `Socket`、`Certificate` 和 `Crypto`。这些依赖项由 Angular 的 DI 机制解析并注入到工厂函数中。您可以查看整个实现并在 `ch6/configuring-providers/factory/app.ts` 中尝试它。

值得注意的是，内部，Angular 将 `useClass` 提供者转换为 `useFactory`。Angular 在 `deps` 数组中列出类的依赖项，并使用 `new` 操作符调用该类，将工厂接收到的依赖项作为参数传递。

# 声明可选依赖项

Angular 引入了 `@Optional` 装饰器，它允许我们处理没有与它们关联已注册提供者的依赖项。假设一个提供者的依赖项在任何负责其实例化的目标注入器中都不可用。如果我们使用 `@Optional` 装饰器，在依赖提供者的实例化过程中，缺失的依赖项的值将被传递为 `null`。

现在，让我们看一下以下示例：

```js
abstract class SortingAlgorithm {
  abstract sort(collection: BaseCollection): Collection;
}

class BaseCollection {
  getDefaultSort(): SortingAlgorithm {
    // get some generic sorting algorithm...
    return null;
  }
}

class Collection extends BaseCollection {
  public sort: SortingAlgorithm;

  constructor(sort: SortingAlgorithm) {
    super();
    this.sort = sort || this.getDefaultSort();
  }
}

@Component({
  selector: 'app',
  template: "Open your browser's console"
})
class AppComponent {
  constructor(private collection: Collection) {
    console.log(collection);
  }
}

@NgModule({
  providers: [Collection],
  declarations: [AppComponent],
  bootstrap: [AppComponent],
  imports: [BrowserModule]
})
class AppModule { }
```

在这种情况下，我们定义了一个名为 `SortingAlgorithm` 的抽象类和一个名为 `Collection` 的类，它作为依赖项接受一个扩展 `SortingAlgorithm` 的具体类的实例。在 `Collection` 构造函数内部，我们将 `sort` 实例属性设置为传递的依赖项或默认排序算法实现。

我们在声明的 `@NgModule` 提供者中没有为 `SortingAlgorithm` 标记定义任何提供者。因此，如果我们想在 `AppComponent` 中注入 `Collection` 类的实例，我们将得到一个运行时错误。这意味着，如果我们想使用框架的 DI 机制获取 `Collection` 类的实例，我们必须为 `SortingAlgorithm` 标记注册一个提供者，尽管我们可能希望回退到由 `getDefaultSort` 方法返回的默认排序算法。

Angular 通过 `@Optional` 装饰器提供了这个问题的解决方案。以下是我们可以如何使用它来解决这个问题：

```js
// ch6/decorators/optional/app.ts

@Injectable() 
class Collection extends BaseCollection { 
  private sort: SortingAlgorithm;

  constructor(@Optional() sort: SortingAlgorithm) { 
    super(); 
    this.sort = sort || this.getDefaultSort(); 
  } 
} 
```

在前面的代码片段中，我们将 `sort` 依赖项声明为可选的，这意味着如果 Angular 没有找到任何为其标记提供提供者的，它将传递 `null` 值。

# 理解多提供者

多提供者（Multiproviders）是 Angular 依赖注入（DI）机制中引入的另一个新概念。它们允许我们将多个提供者与同一个令牌关联起来。如果我们正在开发一个带有一些默认服务实现的第三方库，但希望用户能够用自定义实现来扩展它，这将非常有用。例如，在 Angular 的表单模块中，多提供者专门用于对单个控件声明多个验证。我们将在 第七章，*使用 Angular 路由和表单*，和 第八章，*解释管道和与 RESTful 服务通信*中解释这个模块。

多提供者的另一个适用用例示例是 Angular 在其 Web Workers 实现中用于事件管理。用户为事件管理插件创建多提供者。每个提供者返回不同的策略，支持不同的事件集（触摸事件、键盘事件等）。一旦发生特定事件，Angular 可以选择处理该事件的适当插件。

让我们看看一个示例，它说明了多提供者（multiproviders）的典型用法：

```js
// ch6/configuring-providers/multi-providers/app.ts

const VALIDATOR = new InjectionToken('validator'); 

interface EmployeeValidator { 
  (person: Employee): string; 
} 

class Employee {...} 

@NgModule({
  providers: [
    {
      provide: VALIDATOR,
      multi: true,
      useValue: (person: Employee) => {
        if (!person.name) {
          return 'The name is required';
        }
      }
    },
    {
      provide: VALIDATOR,
      multi: true,
      useValue: (person: Employee) => {
        if (!person.name || person.name.length < 1) {
          return 'The name should be more than 1 symbol long';
        }
      }
    },
    Employee
  ],
  declarations: [AppComponent],
  bootstrap: [AppComponent],
  imports: [BrowserModule]
})
class AppModule { }
```

在前面的代码片段中，我们声明了一个名为 `VALIDATOR` 的常量，并将其值设置为 `InjectionToken` 的新实例。我们还创建了一个 `@NgModule`，在其中注册了三个提供者：其中两个提供者提供基于不同标准的函数，这些函数用于验证 `Employee` 类的实例。这些函数的类型是 `EmployeeValidator`。

为了声明我们希望注入器将所有注册的验证器传递给 `Employee` 类的构造函数，我们需要使用以下 `constructor` 定义：

```js
class Employee { 
  name: string;

  constructor(@Inject(VALIDATOR) private validators: EmployeeValidator[]) {}

  validate() { 
    return this.validators 
      .map(v => v(this)) 
      .filter(value => !!value); 
  } 
} 
```

在示例中，我们声明了一个 `Employee` 类，它接受单个依赖项：一个 `EmployeeValidator` 数组。在 `validate` 方法中，我们对当前类实例应用单个验证器，并过滤结果以仅获取返回错误消息的验证器。

注意，`validators` 构造函数参数的类型是 `EmployeeValidator[]`。由于我们不能使用 *对象数组* 类型作为提供者的令牌，因为它在 JavaScript 中不是一个有效的值，也不能用作令牌，因此我们需要使用 `@Inject` 参数装饰器。

之后，我们可以像往常一样注入 `Employee` 类型的实例：

```js
@;Component({
  selector: 'app',
  template: '...'
})
class AppComponent {
  constructor(private employee: Employee) {
    console.log(employee);
  }
}
```

# 子注入器和可见性

在本节中，我们将探讨如何构建注入器的层次结构。在 AngularJS 中，这个概念没有替代方案。每个注入器可以有零个或一个父注入器，每个父注入器可以有一个或多个子注入器。与 AngularJS 中所有注册的提供者都存储在扁平结构中不同，在 Angular 中它们存储在树形结构中。扁平结构更为有限；例如，它不支持令牌的命名空间；我们不能为相同的令牌声明不同的提供者。到目前为止，我们已经看到了一个没有子注入器或父注入器的注入器示例。现在，让我们构建一个注入器的层次结构。

为了更好地理解注入器的这种分层结构，让我们看一下下面的图示：

![图示](img/863d4bb1-1b86-4fa5-b44d-798f056b7c93.png)

图 2

在这里，我们看到一个树，每个节点都是一个注入器，这些注入器都保留对其父注入器的引用。`House` 注入器有三个子注入器：`Bathroom`、`Kitchen` 和 `Garage`。

`Garage` 有两个子注入器：`Car` 和 `Storage`。我们可以将这些注入器视为内部注册了提供者的容器。

假设我们想要获取与 `Tire` 令牌关联的提供者的值。如果我们使用 `Car` 注入器，这意味着 Angular 的依赖注入机制将尝试在 `Car` 以及其所有父注入器 `Garage` 和 `House` 中找到与该令牌关联的提供者，直到达到根注入器。

# 实例化注入器

内部，Angular 构建了这个注入器的层次结构，但所有操作都是隐式的。为了我们自己实现这一点，我们将不得不使用较低级别的 API，这对于我们的日常开发过程来说是不寻常的。

首先，让我们创建一个注入器的实例，以便使用它来实例化注册的令牌：

```js
// ch6/manual-injector/instantiate.ts

import { Injector } from '@angular/core';

// ...

const injector = Injector.create([
  { provide: BUFFER_SIZE, useValue: 42 },
  {
    provide: Buffer,
    deps: [BUFFER_SIZE],
    useFactory: function (size: number) {
      return new Buffer(size);
    }
  },
  {
    provide: Socket,
    deps: [Buffer],
    useFactory: function (buffer: Buffer) {
      return new Socket(buffer);
    }
  }
]);
```

在这里，我们首先从 `@angular/core` 中导入 `Injector`。这个抽象类有一个名为 `create` 的静态方法，用于注入器的实例化。在 `create` 方法内部，我们传递一个提供者数组作为参数。我们可以看到从 *配置提供者* 部分已经熟悉的语法。

我们声明一个提供者用于 `BUFFER_SIZE`，使用值 `42`；我们声明一个 `Buffer` 的工厂，并列出其所有依赖项（在这种情况下，只有 `BUFFER_SIZE`）；最后，我们还声明了一个 `Socket` 的工厂提供者。`create` 方法将创建一个 `StaticInjector` 的实例，我们可以使用它来获取单个 `tokens` 的实例。提醒一下，**注入器**是包含单个提供者的抽象，并且知道如何实例化与它们关联的依赖项。

在前面的例子中，一个重要的细节是，在`StaticInjector`中，我们只能使用有限类型的提供者，例如，我们不能使用`useClass`提供者。这是因为 Angular 使用`StaticInjector`与提供者的标准化版本一起使用，而`useClass`的标准化版本是`useFactory`。内部，Angular 会收集传递给`@NgModule`的提供者，将它们转换为它们的标准化版本，并实例化`StaticInjector`。

# 构建注入器层次结构

为了更好地理解段落，让我们看看这个简单的例子：

```js
// ch6/manual-injector/simple-example.ts

class Http { }

class UserService {
  constructor(public http: Http) { }
}

const parentInjector = Injector.create([{
  provide: Http,
  deps: [],
  useFactory() {
    return new Http();
  }
}]);

const childInjector = Injector.create([{
  provide: UserService,
  deps: [Http],
  useFactory(http) {
    return new UserService(http);
  }
}], parentInjector);

console.log(childInjector.get(UserService));
console.log(childInjector.get(Http) === parentInjector.get(Http));

```

省略了导入，因为它们对于解释代码不是必需的。我们有两个服务，`Http`和`UserService`，其中`UserService`依赖于`Http`服务。

初始时，我们使用`Injector`类的`create`静态方法创建一个注入器。我们向这个注入器传递一个带有`Http`令牌的工厂提供者。稍后，再次使用`create`，我们通过传递包含`UserService`提供者的数组来实例化子注入器。请注意，作为第二个参数，我们传递了`parentInjector`常量，因此我们得到了与前面图中`Garage`和`House`之间相同的关系：`parentInjector`是`childInjector`的父级。

现在，使用`childInjector.get(UserService)`，我们能够获取与`UserService`令牌关联的值。同样，使用`childInjector.get(Http)`和`parentInjector.get(Http)`，我们获取与`Http`令牌关联的相同值。这意味着`childInjector`会向其父级请求请求令牌关联的值。

然而，如果我们尝试使用`parentInjector.get(UserService)`，由于它的提供者在`childInjector`中注册，我们将无法获取与令牌关联的值。

# 使用组件和指令进行依赖注入

在第五章“使用 Angular 组件和指令入门”中，当我们开发我们的第一个 Angular 指令时，我们看到了如何利用 DI 机制将服务注入到我们的 UI 相关构建块（即指令和组件）中。

让我们快速回顾一下我们之前做了什么，但从一个 DI 的角度来看：

```js
// ch5/tooltip/app.ts

// ... 
@Directive(...) 
export class Tooltip { 
  @Input() saTooltip: string; 

  constructor(private el: ElementRef, private overlay: Overlay) { 
    this.overlay.attach(el.nativeElement); 
  } 
  // ... 
}

@Component({ 
  // ... 
  providers: [Overlay]
}) 
class App {} 
```

大部分早期实现中的代码都被省略了，因为它与我们当前的关注点没有直接关系。

注意，`Tooltip`的构造函数接受两个依赖项：

+   `ElementRef`类的实例

+   `Overlay`类的实例

依赖项的类型是与它们的提供者关联的令牌，以及从提供者获得的相应值将通过 Angular 的 DI 机制注入。

`Tooltip` 类的依赖声明与我们在前面的章节中所做的一样：我们只是将它们作为类的构造函数的参数列出。然而，请注意，在这种情况下，我们没有为 `ElementRef` 标记进行任何显式的提供者声明，我们只有一个为 `Overlay` 标记的提供者，它在 `App` 组件的元数据中声明。在这种情况下，Angular 内部创建并配置了所谓的 **元素注入器**。

# 介绍元素注入器

在底层，Angular 将为所有指令和组件创建注入器，并向它们添加一组默认的提供者。这些被称为 **元素注入器**，并且是框架自己负责处理的事情。与组件关联的注入器被称为 **宿主注入器**。每个元素注入器中的一个提供者与 `ElementRef` 标记相关联；它将返回指令的主元素引用。那么 `Overlay` 类的提供者怎么办？让我们看看顶级组件的实现：

```js
@Component({ 
  // ... 
  providers: [Overlay]
})
class App {} 
```

我们通过在 `@Component` 装饰器内部声明 `providers` 属性来配置 `App` 组件的元素注入器。在这个时候，注册的提供者将对与相应元素注入器关联的指令或组件以及组件的整个组件子树可见，除非它们在层次结构中的某个地方被覆盖。

# 声明元素注入器的提供者

将所有提供者的声明放在同一个地方可能相当不方便。例如，想象一下我们正在开发一个大型应用程序，该应用程序有数百个组件依赖于数千个服务。在这种情况下，在根组件中配置所有提供者不是一个实际的解决方案。当两个或多个提供者与同一个标记相关联时，将发生名称冲突。配置将非常庞大，并且很难追踪不同的依赖项需要注入的位置。

正如我们所提到的，Angular 的 `@Directive`（和 `@Component`）装饰器允许我们使用 `providers` 属性为给定指令对应的元素注入器声明一组提供者。以下是我们可以采取的方法：

```js
@Directive({ 
  selector: '[saTooltip]', 
  providers: [{ provide: Overlay, useClass: OverlayMock }] 
}) 
export class Tooltip { 
 @Input() saTooltip: string; 

  constructor(private el: ElementRef, private overlay: Overlay) { 
    this.overlay.attach(el.nativeElement); 
  } 
  // ... 
} 

// ... 

platformBrowserDynamic().bootstrapModule(AppModule);
```

之前的示例在 `Tooltip` 指令的声明中覆盖了 `Overlay` 标记的提供者。这样，Angular 将在提示框实例化期间注入 `OverlayMock` 实例而不是 `Overlay`。

# 探索组件中的依赖注入

由于组件通常是带有模板的指令，因此到目前为止我们所看到的所有关于 DI 机制如何与指令一起工作的内容也适用于组件。然而，由于组件提供的额外功能，我们允许对它们的提供者有更多的控制。

正如我们所说的，与每个组件关联的注入器将被标记为**宿主**注入器。有一个名为`@Host`的参数装饰器，它允许我们从任何注入器中检索给定的依赖项，直到它达到最近的宿主注入器。这意味着，在指令中使用`@Host`装饰器，我们可以声明我们想要从当前注入器或任何父注入器中检索给定的依赖项，直到我们达到最近父组件的注入器。

此外，Angular 的 API 允许我们通过`viewProviders`属性，它是`@Component`装饰器的配置对象的一部分，在组件树中更具体地指定提供者的可见性。

# 视图提供者与提供者

让我们看看一个名为`MarkdownPanel`的组件的例子。这个组件将被以下方式使用：

```js
<markdown-panel> 
  <panel-title># Title</pane-title> 
  <panel-content> 
# Content of the panel 
* First point 
* Second point 
  </panel-content> 
</markdown-panel> 
```

面板每个部分的内文将是从 Markdown 转换为 HTML。我们可以将此功能委托给一个名为`Markdown`的服务：

```js
import * as markdown from 'markdown';

class Markdown { 
  toHTML(md) { 
    return markdown.toHTML(md); 
  } 
} 
```

`Markdown`服务将`markdown`模块包装起来，以便通过 DI 机制进行注入。

现在让我们实现`MarkdownPanel`。

在以下代码片段中，我们可以找到组件实现的所有重要细节：

```js
// ch6/directives/app.ts 
@Component({ 
  selector: 'markdown-panel', 
  viewProviders: [Markdown], 
  styles: [...], 
  template: ` 
    <div class="panel"> 
      <div class="panel-title"> 
        <ng-content select="panel-title"></ng-content> 
      </div> 
      <div class="panel-content"> 
        <ng-content select="panel-content"></ng-content> 
      </div> 
    </div>` 
}) 
class MarkdownPanel { 
  constructor(private el: ElementRef, private md: Markdown) {}

  ngAfterContentInit() { 
    let el = this.el.nativeElement; 
    let title = el.querySelector('panel-title'); 
    let content = el.querySelector('panel-content'); 
    title.innerHTML = this.md.toHTML(title.innerHTML); 
    content.innerHTML = this.md.toHTML(content.innerHTML); 
  } 
} 
```

在`@Component`装饰器中，我们使用`markdown-panel`选择器并设置`viewProviders`属性。在这种情况下，只有一个视图提供者：为`Markdown`服务提供的那个。通过设置此属性，我们声明其中声明的所有提供者都将从组件本身及其所有**视图子项**中可访问。

现在，假设我们有一个名为`MarkdownButton`的组件，并且我们想以下这种方式将其添加到我们的模板中：

```js
<markdown-panel> 
  <panel-title>### Small title</panel-title> 
  <panel-content> 
    Some code 
  </panel-content> 
  <markdown-button>*Click to toggle*</markdown-button> 
</markdown-panel> 
```

以下`Markdown`服务将无法通过位于`panel-content`元素下方的`MarkdownButton`访问；然而，如果我们使用组件模板中的按钮，它将是可访问的：

```js
@Component({ 
  selector: 'markdown-panel', 
  viewProviders: [Markdown], 
  styles: [...], 
  template: ` 
    <div class="panel"> 
      <markdown-button>*Click to toggle*</markdown-button> 
      <div class="panel-title"> 
        <ng-content select="panel-title"></ng-content> 
      </div> 
      <div class="panel-content"> 
        <ng-content select="panel-content"></ng-content> 
      </div> 
    </div>` 
}) 
// ...
```

如果我们需要提供者在所有内容和视图子项中可见，我们只需将`viewProviders`属性的名称更改为`providers`即可。

你可以在`ch6/directives/app.ts`目录下的示例目录中找到这个例子。

注意，对于任何组件或指令，我们可以使用传递给`@Component`或`@Directive`装饰器的对象字面量的`providers`属性来覆盖在`@NgModule`中声明的现有提供者。如果我们只想为给定组件的视图子项覆盖特定的提供者，我们可以使用`viewProviders`。

# 使用`@SkipSelf`装饰器

有时候在层次结构中，我们在不同的注入器中为相同的令牌定义了提供者。例如，假设我们有前面的示例，但具有以下注入器配置：

```js
@Component({
  selector: 'markdown-panel',
  viewProviders: [{ provide: Markdown, useValue: null }],
  // ...
})
class MarkdownPanel {
  constructor(private el: ElementRef, private md: Markdown) { }

  // ...
}

@Component({
  selector: 'app',
  providers: [Markdown],
  // ...
})
class App {
  constructor() { }
}
```

在前面的例子中，如果我们尝试在`MarkdownPanel`的构造函数中注入`Markdown`服务，我们会得到`null`，因为这是与组件元数据中`viewProviders`声明中的`Markdown`令牌关联的值。

然而，请注意，在`App`组件中，我们还有另一个提供者声明，它将被用于实例化`App`组件的`ElementInjector`。我们如何使用在`App`组件元数据中声明的`Markdown`提供者而不是在`MarkdownPanel`元数据中声明的提供者？我们只需要在`MarkdownPanel`的构造函数中添加`@SkipSelf()`装饰器。这将提示 Angular 跳过当前注入器，并在层次结构中向上查找与所需令牌关联的提供者：

```js
@Component(...)
class MarkdownPanel {
  constructor(private el: ElementRef, @SkipSelf() private md: Markdown) { }
}
```

Angular 还提供了`@Self`装饰器，它向框架提示从当前注入器获取给定令牌的提供者。在这种情况下，如果 Angular 在当前注入器中找不到提供者，它将抛出一个错误。

# 摘要

在本章中，我们介绍了 Angular 的依赖注入（DI）机制。我们通过在框架的上下文中介绍它，简要讨论了在我们的项目中使用 DI 的优点。我们旅程的第二步是如何使用`@NgModule`配置注入器；我们还解释了注入器的层次结构和注册提供者的可见性。为了强制更好的关注点分离，我们提到了如何在我们的指令和组件中注入携带我们应用程序业务逻辑的服务。

在下一章中，我们将介绍框架的新路由机制。我们将解释如何配置基于组件的路由器并将多个视图添加到我们的应用程序中。我们还将涵盖另一个重要主题，即新的表单模块。通过构建一个简单的应用程序，我们将演示如何创建和管理表单。
