# 第九章. 考虑插件

在上一章中，我们探讨了我们可以用来构建应用程序的一些设计模式。使用 Flux 和 Redux 有理由和反对的理由，但它们通常可以提高 React 应用程序的结构。

良好的结构对于任何大型应用程序都是必不可少的。对于小型实验，拼凑东西可能可行，但设计模式是维护任何更大规模事物的组成部分。尽管如此，它们并没有太多关于创建可扩展组件的内容。在本章中，我们将探讨一些我们可以用来使我们的组件通过替换、注入功能以及从组件动态列表中组合接口来扩展组件的方法。

我们将回顾一些相关的软件设计概念，并看看它们如何帮助我们（以及其他人）在想要用修改后的组件和替代实现替换应用程序的部分时提供帮助。

# 依赖注入和服务定位

*依赖注入*和*服务定位*是两个有趣的概念，它们并不仅限于 React 开发。要真正理解它们，让我们暂时远离组件。想象一下，如果我们想创建一个网站地图。为此，我们可能可以使用类似于以下代码：

```js
let backend = {
    getAll() {
        // ...return pages
    }
};
class SitemapFormatter {
    format(items) {
        // ...generate xml from items
    }
}
function createSitemap() {
    const pages = backend.getAll();
    const formatter = new SitemapFormatter();

    return formatter.format(
        pages.filter(page => page.isPublic)
    );
}

let sitemap = createSitemap();
```

在这个例子中，`createSitemap`有两个依赖。首先，我们从`backend`获取页面。这是一个全球存储对象。当我们查看 Flux 架构时，我们使用过类似的东西。

第二个依赖是`SitemapFormatter`实现。我们使用它来获取页面列表并返回某种形式的标记，总结列表中的这些页面。我们以某种方式硬编码了这两个依赖，这在小剂量下是可以接受的，但随着应用程序的扩展，它们会变得有问题。

例如，如果我们想使用这个网站地图生成器与多个后端？如果我们还想尝试格式化器的替代实现？目前，我们已经将网站地图生成器耦合到单个后端和单个格式化器实现。

依赖注入和服务定位是解决这个问题的两种可能方案。

## 依赖注入

我们已经以微妙的方式使用了依赖注入。这看起来如下所示：

```js
function createSitemap(backend, formatter) {
    const pages = backend.getAll();

    return formatter.format(
        pages.filter(page => page.isPublic)
    );
}

let formatter = new SitemapFormatter();
let sitemap = createSitemap(backend, formatter);
```

依赖注入的全部内容是将依赖项从使用它们的函数和类中移出。这并不是要避免它们的使用；而是创建外部的新实例，并通过函数参数传递它们。

有两种依赖注入：构造函数注入和设置器注入。这可以说明它们之间的区别：

```js
class SitemapGenerator {
    constructor(formatter) {
        this._formatter = formatter;
    }

    set formatter(formatter) {
        this._formatter = formatter;
    }
}

let generator = new SitemapGenerator(new SitemapFormatter());

generator.formatter = new AlternativeSitemapFormatter();

```

我们可以通过构造函数注入依赖项并将它们分配给属性，或者为它们创建设置器。我们已经多次看到了构造函数注入，但设置器注入是另一种有效的注入依赖项的方式。我们也可以使用普通函数来注入依赖项，但那样我们就无法通过对象属性来设置或获取它们。

类似地，当我们定义组件属性时，我们实际上是将这些属性值作为构造函数依赖项注入。

## 工厂和服务定位器

另一个解决方案是将创建新实例的逻辑封装起来，并在查找依赖项时使用对这种类似工厂对象的引用：

```js
class Factory {
    createNewFormatter() {
        // ...create new formatter instance
    }

    getSharedBackend() {
        // ...get shared backend instance
    }
}

const factory = new Factory();

const formatter = factory.createNewFormatter();
const backend = factory.getSharedBackend();

```

然后，我们可以传递 `Factory` 类的实例，甚至将其作为依赖项注入。在其他动态语言中，如 PHP，这已成为一种常见做法。然后我们可以使用这些工厂来创建基于某些初始标准的新实例。我们可以有一个工厂来创建新的数据库连接，并连接到 MySQL 或 SQLite，这取决于我们指定的连接类型。

另一个选择是创建多个对象并将它们存储在一个公共服务定位器对象中：

```js
let locator = new ServiceLocator();
locator.set("formatter", new Formatter());
locator.set("backend", new Backend());
```

同样，我们可以将定位器作为依赖项注入，并根据需要获取实际的依赖项：

```js
class SitemapGenerator {
    constructor(locator) {
        this.formatter = locator.get("formatter");
        this.backend = locator.get("backend");
    }
}

let generator = new SitemapGenerator(locator);
```

## Fold

幸运的是，我们不需要构建和维护工厂、依赖注入器和服务定位器。JavaScript 中已经有了很多可供选择，我们将特别讨论其中一个。记得我们讨论在服务器上渲染 React 组件的时候吗？我们查看了一个名为 AdonisJS 的 MVC 应用程序框架。

AdonisJS 的创建者也维护了一个依赖注入容器，称为 *Fold*。Fold 做的一些事情很有趣，我们希望与您分享。

我们可以使用以下命令安装 Fold：

```js
$ npm install --save adonis-fold

```

### 注意

在前面的章节中，我们创建了一个工作流程来运行 ES6 代码通过 Node.js。我们建议您为本章的一些代码重新创建此设置。

然后，我们可以开始使用它来注册和解析对象：

```js
import { Ioc } from "adonis-fold";

Ioc.bind("App/Authenticator", function() {
    // ...return a new authenticator object
});

let authenticator = Ioc.use("App/Authenticator");
```

### 注意

Fold 引入了一个全局的 `use` 函数，因此我们可以在不需要每次都导入 `Ioc` 的情况下使用它。

我们使用 `bind` 为类似工厂的函数分配别名。当这个别名被 *使用* 时，这个工厂函数将被调用，并返回结果。当我们拥有大型依赖图时，这变得更加强大：

```js
Ioc.bind("App/Authenticator", function() {
    const repository = Ioc.use("App/UserRepository");
    const crypto = Ioc.use("App/Crypto");

    return new Authenticator(repository, crypto);
});
```

我们可以组合对 `bind` 和 `use` 的调用。在这个例子中，创建一个新的 `App/Authenticator` 将会从容器中解析出 `App/UserRepository` 和 `App/Crypto`。

更重要的是，我们可以使用 Fold 自动加载类文件。所以，假设我们有一个类似于以下内容的 `Authenticator` 类文件（在 `src/Authenticator.js` 中）：

```js
class Authenticator {
    // ...do some authentication!
}

module.exports = Authenticator;
```

### 注意

通常我们使用 `export default ...` 来导出类或函数；但在这个情况下，我们只是将类分配给 `module.exports`。这使得 Fold 能够对我们的代码做更多有趣的事情。

我们可以用以下代码来自动加载：

```js
Ioc.autoload("App", __dirname + "/src");

const Authenticator = Ioc.use("App/Authenticator");

let authenticator = new Authenticator();
```

我们也可以使用常规类作为单例，只需稍微不同的绑定语法：

```js
Ioc.singleton("App/Backend", function() {
    // ...this will only be run once!
 return new Backend();
});

```

### 注意

`use`无论您使用`bind`还是`singleton`，都按相同的方式工作。

当涉及到递归解析依赖关系时，折叠功能特别出色。让我们改变`Authenticator`：

```js
class Authenticator {
    static get inject() {
        return ["App/Repository", "App/Crypto"];
    }

    constructor(repository, crypto) {
        this.repository = repository;
        this.crypto = crypto;
    }
}

module.exports = Authenticator;
```

我们可以使用 getter（ES6 的一个特性）来重载静态`inject`属性的属性访问。这仅仅意味着每当我们在`Authenticator.inject`中写入时，都会运行一个函数。Fold 使用这个静态数组属性来确定要解析哪些依赖项。因此，我们可以创建`Repository`（在`src/Repository.js`中），如下所示：

```js
class Repository {
    // ...probably fetches users from a data source
}

module.exports = Repository;
```

我们还可以创建`Crypto`（在`src/Crypto.js`中），如下所示：

```js
class Crypto {
    // ...probably performs cryptographic comparisons
}

module.exports = Crypto;
```

这些类的作用并不重要。重要的是 Fold 如何将它们连接起来：

```js
Ioc.autoload("App", __dirname + "/src");

let authenticator = Ioc.make("App/Authenticator");
```

第一行在`App/`类前缀和我们要加载的类文件之间创建了一个链接。因此，当创建`App/Foo/Bar`时，`src/Foo/Bar.js`中的类将被加载。同样，当使用`Ioc.make`时，在静态注入数组属性中定义的别名将与它们的相应构造函数参数连接起来。

## 为什么这很重要

如果我们注入依赖项，我们可以轻松地用另一个部分替换应用程序的一部分，因为依赖项没有在依赖它们的类中命名。这些类不负责创建新实例，只接收外部创建的实例。

如果我们使用服务定位器（特别是递归解析依赖项的那种），我们可以在引导阶段避免很多样板代码。

我们能够替换应用程序的部分，我们能从中得到什么？

我们允许其他开发者通过替换应用程序的核心部分来将行为注入我们的应用程序。想象一下我们有以下`Authenticator`方法：

```js
class Authenticator {
    static get inject() {
        return ["App/Repository", "App/Crypto"];
    }

    constructor(repository, crypto) {
        this.repository = repository;
        this.crypto = crypto;
    }

    authenticate(email, password) {
        // ...authenticate the user details
    }
}
```

现在想象一下，我们想要为所有认证添加日志记录。我们可以直接更改`Authenticator`类。如果我们*拥有*代码，这很容易，但我们经常使用第三方库。我们可以在`src/AuthenticatorLogger.js`中创建一个装饰器：

```js
class AuthenticatorLogger {
    static get inject() {
        return ["App/Authenticator"];
    }

    constructor(authenticator) {
        this.authenticator = authenticator;
    }

    authenticate(email, password) {
        this.log("authentication attempted");
        return this.authenticator.authenticate(email, password);
    }

    log(message) {
        // ...store the log message
    }
}

module.exports = AuthenticatorLogger;
```

### 注意

装饰器是增强其他类功能的类，通常是通过组合它们增强的类的实例。您可以在[`en.wikipedia.org/wiki/Decorator_pattern`](https://en.wikipedia.org/wiki/Decorator_pattern)了解更多关于这种模式的信息。

这个新类期望一个`Authenticator`依赖项，并为`authenticate`方法添加了透明的日志记录。我们可以通过重新绑定`App/Authenticator`来覆盖默认（自动加载）行为：

```js
const Authenticator = Ioc.use("App/Authenticator");
const AuthenticatorLogger = Ioc.use("App/AuthenticatorLogger");

Ioc.bind("App/Authenticator", function() {
    return new AuthenticatorLogger(Ioc.make(Authenticator));
});

let authenticator = Ioc.make("App/Authenticator");
```

让我们从这个组件的角度来思考这个问题。想象一下，我们有一个页面列表，这些页面是由`PagesComponent`组件（在`src/Page.js`中）展示给我们的：

```js
import React from "react";

class PagesComponent extends React.Component {
    constructor(props, context) {
        super(props, context);
        // ...get context.store state
    }
    componentDidMount() {
        // ...add context.store change listener
    }
    componentWillUnmount() {
        // ...remove context.store change listener
    }
    render() {
        // ...return a list of pages
        return <div>pages</div>;
    }
}

module.exports = PagesComponent;
```

我们可以使用折叠来自动加载，如下所示：

```js
import React from "react";
import ReactDOMServer from "react-dom/server";

const PagesComponent = Ioc.use("App/PagesComponent");

let rendered = ReactDOMServer.renderToString(
    <PagesComponent />
);
```

现在，想象一下，另一位开发者出现了，并想围绕页面列表添加一些额外的装饰。他们可以深入到 `node_modules` 文件夹并直接编辑组件，但这会很混乱。相反（并且因为我们使用依赖注入容器），他们可以覆盖到 `App/PagesComponent` 的别名：

```js
const PagesComponent = Ioc.use("App/PagesComponent");

Ioc.bind("App/PagesComponent{}", function() {
    return (
        <PagesComponent />
    );
});

// ...then, when we want to decorate the component

class PagesComponentChrome extends React.Component {
    render() {
        return (
            <div className="chrome">
                {this.props.children}
            </div>
        )
    }
}

Ioc.bind("App/PagesComponent{}", function() {
    return (
        <PagesComponentChrome>
            <PagesComponent />
        </PagesComponentChrome>
    );
});

// ...some time later

let rendered = ReactDOMServer.renderToString(
    Ioc.use("App/PagesComponent{}")
);
```

### 注意

当涉及到 `React.Component` 子类与这些子类的实例时，事情会变得有点棘手。`ReactDOM.render` 和 `ReactDOMServer.renderToString` 期望实例是在我们使用 `<SomeComponent/>` 在 JSX 中创建时。这可能有助于在容器中注册这两种形式：类引用的绑定和创建这些类实例的工厂函数的绑定。我们给后者添加了 `{}` 后缀，我们可以在 `render` 方法中直接使用它。

通过进行以下小的更改，可能更容易理解最后一部分：

```js
// ...some time later

const NewPagesComponent = Ioc.use("App/PagesComponent{}");

let rendered = ReactDOMServer.renderToString(
    <div>{NewPagesComponent}</div>
);
```

以这种方式，我们允许其他开发者用自定义类或组件装饰器替换应用程序的部分。在创建团队标准方面肯定有一些工作要做，但基本想法是稳固的。

### 注意

你可以在 [`adonisjs.com/docs/2.0/ioc-container`](http://adonisjs.com/docs/2.0/ioc-container) 上了解更多关于 Fold 的信息。

# 通过回调扩展

创建更多可插拔组件的另一种方法是公开（并操作）事件回调。我们已经看到了类似的东西，但让我们再看一看。假设我们有一个 `PageEditorComponent` 类，如下所示：

```js
import React from "react";

class PageEditorComponent extends React.Component {
    onSave(e, refs) {
        this.props.onSave(e, refs);
    }
    onCancel(e, refs) {
        this.props.onCancel(e, refs);
    }
    render() {
        let refs = {};

        return (
            <div>
                <input type="text"
                    ref={ref => refs.title = ref} />
                <input type="text"
                    ref={ref => refs.body = ref} />
                <button onClick={e => this.onSave(e, refs)}>
                    save
                </button>
                <button onClick={e => this.onCancel(e, refs)}>
                    cancel
                </button>
            </div>
        );
    }
}

PageEditorComponent.propTypes = {
    "onSave": React.PropTypes.func.isRequired,
    "onCancel": React.PropTypes.func.isRequired
};

module.exports = PageEditorComponent;
```

### 注意

这是一段代码，最好通过我们之前创建的工作流程之一（允许在浏览器中渲染组件）或 [`jsbin.com`](https://jsbin.com) 来运行。我们感兴趣的是查看一些动态行为，因此我们能够点击东西非常重要！

如我们之前所见，我们可以通过属性从某些更高组件传递 `onSave` 和 `onCancel` 回调。每个 React 组件都可以有一个 `ref` 回调。将 DOM 节点的引用传递给此回调，因此我们可以使用 `focus` 等方法以及 `value` 等属性。这对于与公共后端或存储同步状态非常有用。然而，我们该如何添加一些自定义验证呢？

我们可以添加可选的回调属性（和 `propTypes`），并将这些属性纳入我们的 `onSave` 和 `onCancel` 方法中：

```js
class PageEditorComponent extends React.Component {
    onSave(e, refs) {
        if (this.props.onBeforeSave) {
            if (!this.props.onBeforeSave(e, refs)) {
                return;
            }
        }

        this.props.onSave(e, refs);

        if (this.props.onAfterSave) {
            this.props.onAfterSave(e, refs);
        }
    }
    onCancel(e, refs) {
        this.props.onCancel(e, refs);
    }
    render() {
        let refs = {};

        return (
            <div>
                <input type="text"
                    ref={ref => refs.title = ref} />
                <input type="text"
                    ref={ref => refs.body = ref} />
                <button onClick={e => this.onSave(e, refs)}>
                    save
                </button>
                <button onClick={e => this.onCancel(e, refs)}>
                    cancel
                </button>
            </div>
        );
    }
}

PageEditorComponent.propTypes = {
    "onSave": React.PropTypes.func.isRequired,
    "onCancel": React.PropTypes.func.isRequired,
    "onBeforeSave": React.PropTypes.func,
    "onAfterSave": React.PropTypes.func,
};
```

我们可以在组件行为的要点处定义额外的步骤：

```js
const onSave = (e, refs) => {
    // ...save the data
    console.log("saved");
};

const onCancel = (e, refs) => {
    // ...cancel the edit
    console.log("cancelled");
};

const onBeforeSave = (e, refs) => {
    if (refs.title.value == "a bad title") {
        console.log("validation failed");
        return false;
    }

    return true;
};

ReactDOM.render(
    <PageEditorComponent
        onBeforeSave={onBeforeSave}
        onSave={onSave}
        onCancel={onCancel} />,
    document.querySelector(".react")
);
```

`onSave` 方法检查是否定义了可选的 `onBeforeSave` 属性。如果是这样，则运行此回调。如果回调返回 `false`，我们可以将其用作防止默认组件保存行为的一种方式。我们仍然需要默认的保存或取消行为正常工作，因此这些属性是必需的。其他属性是可选的但很有用。

# 存储、reducer 和组件

在这些概念的基础上，我们想要你查看的最后一件事是所有这些如何在 Redux 架构内部结合在一起。

### 注意

如果你跳到了这一章，请确保你已经通过阅读上一章对 Redux 有了牢固的理解。

让我们从`PageComponent`类开始（用于列表中的单个页面）：

```js
import React from "react";

class PageComponent extends React.Component {
    constructor(props) {
        super(props);
        this.state = props.store.getState();
    }
    componentDidMount() {
        this.remove = this.props.store.register(
            this.onChange
        );
    }
    componentWillUnmount() {
        this.remove();
    }
    onChange() {
        this.setState(this.props.store.getState());
    }
    render() {
        const DummyPageViewComponent = use(
            "App/DummyPageViewComponent"
        );

        const DummyPageEditorComponent = use(
            "App/DummyPageEditorComponent"
        );

        const DummyPageActionsComponent = use(
            "App/DummyPageActionsComponent"
        );

        return (
            <div>
                <DummyPageViewComponent />
                <DummyPageEditorComponent />
                <DummyPageActionsComponent />
            </div>
        );
    }
}

module.exports = PageComponent;
```

### 注意

`Dummy*Component`类可以是任何东西。我们在与本章相关的源代码中创建了一些“空”组件。主要的事情是`PageComponent`组合了几个其他组件。

这里没有太多花哨的东西：我们组合了一些组件，并将其连接到常规的 Redux 功能。这由一些新的服务位置功能补充：

```js
import { combineReducers, createStore } from "redux";

Ioc.bind("App/Reducers", function() {
    return [
        (state = {}, action) => {
            let pages = state.pages || [];

            if (action.type == "UPDATE_TITLE") {
                pages = pages.map(page => {
                    if (page.id = payload.id) {
                        page.title = payload.title;
                    }

                    return page;
                });
            }

            return {
                pages
            };
        }
    ];
});

Ioc.bind("App/Store", function() {
    const reducers = combineReducers(
        Ioc.use("App/Reducers")
    );

    return createStore(reducers);
});

const Store = Ioc.use("App/Store");
const PageComponent = Ioc.use("App/PageComponent");

let rendered = ReactDOMServer.renderToString(
    <PageComponent store={Store} />
)
```

我们正在使用一个新的`combineReducers`方法，它接受一个 reducer 数组并生成一个新的超级 reducer。让我们使子组件的顺序和包含变得可配置：

```js
render() {
    const components = [
        use("App/DummyPageViewComponent"),
        use("App/DummyPageEditorComponent"),
        use("App/DummyPageActionsComponent"),
    ];

    return (
        <div>
            {components.map((Component, i) => {
                return <Component key={i} />;
            })}
        </div>
    );
}
```

这里正在发生两个有趣的事情：

+   在 ES6 中，`Array.prototype.map`方法将第二个参数传递给回调。这是正在映射的数组的当前迭代的数字索引。我们可以将其用作创建子组件列表时的`key`参数。

+   我们可以使用动态组件名称。请注意`Component`的字母大小写。如果组件名称变量以小写字母开头，React 将假设它是一个字面量值。

现在我们正在构建一个动态的组件列表，我们可以将默认列表从这个组件中移除：

```js
Ioc.bind("App/PageComponentChildren", function() {
    return [
        use("App/DummyPageViewComponent"),
        use("App/DummyPageEditorComponent"),
        use("App/DummyPageActionsComponent"),
    ];
});
```

然后，我们可以在`PageComponent.render`中替换这个列表：

```js
render() {
    const components = use("App/PageComponentChildren");

    return (
        <div>
            {components.map((Component, i) => {
                return <Component key={i} />;
            })}
        </div>
    );
}
```

建立在之前关于 Fold 学习的知识之上，当我们想要添加插件时，我们可以覆盖这个列表！我们可以包含一个插件，将页面的快照通过电子邮件发送给某人：

```js
const PageComponentChildren = use("App/PageComponentChildren");

Ioc.bind("App/PageComponentChildren", function() {
    return [
        ...PageComponentChildren,
        use("App/DummyPageEmailPluginComponent"),
    ];
});

let extended = ReactDOMServer.renderToString(
    <PageComponent store={Store} />
);
```

这个新的插件配置可能远离`PageComponent`的核心定义，我们不需要更改任何核心代码来使它工作。我们可以以完全相同的方式添加新的 reducer（从而改变我们的存储或分发器行为）：

```js
const Reducers = use("App/Reducers");

Ioc.bind("App/Reducers", function() {
    return [
        ...Reducers,
        (state, action) => {
            if (action.type == "EMAIL_PAGE") {
                // ...email the page
            }

            return state;
        }
    ]
});
```

以这种方式，其他开发者可以创建全新的组件和 reducer，并且可以轻松地将它们应用到我们已构建的系统上。

# 摘要

在本章中，我们探讨了我们可以使用的一些方法，使我们的组件（和通用架构）易于扩展，而无需修改核心代码。这里有很多东西需要吸收，而且社区标准化远远不够，这不能成为插件组件的最终结论。希望这里的内容足以让你为你的应用程序设计正确的插件架构。

在下一章中，我们将探讨测试我们迄今为止构建的组件和类的方法。我们将继续看到诸如依赖注入和服务位置等事物的优势，同时也会了解一些新工具。
