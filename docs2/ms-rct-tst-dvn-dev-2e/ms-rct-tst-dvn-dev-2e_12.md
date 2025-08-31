# 12

# 测试驱动 Redux

**Redux**是一个**可预测的状态容器**。对于初学者来说，这些词意义不大。幸运的是，TDD 可以帮助我们理解如何思考和实现 Redux 应用架构。本章中的测试将帮助你了解 Redux 如何集成到任何应用中。

Redux 的主要优势是能够在异步浏览器环境中以提供数据一致性的方式在组件之间共享状态。其重大缺点是必须在应用中引入大量管道和复杂性。

这里是龙

对于许多应用来说，Redux 的复杂性超过了其好处。仅仅因为本章存在于这本书中，并不意味着你应该急忙去使用 Redux。实际上，我希望本书中的代码示例足以作为警告，提醒你将要引入的复杂性。

在本章中，我们将构建一个 reducer 和一个 saga 来管理`CustomerForm`组件的提交。

我们将使用一个名为`expect-redux`的测试库来测试 Redux 交互。这个库允许我们编写与`redux-saga`库无关的测试。独立于库是确保测试不脆弱且对变化有弹性的好方法：你可以用`redux-thunk`替换`redux-saga`，测试仍然会工作。

本章涵盖了以下主题：

+   为 reducer 和 saga 进行前期设计

+   测试驱动 reducer

+   测试驱动 saga

+   将组件状态切换为 Redux 状态

到本章结束时，你将看到测试 Redux 所需的所有技术。

# 技术要求

本章的代码文件可以在以下位置找到：

[`github.com/PacktPublishing/Mastering-React-Test-Driven-Development-Second-Edition/tree/main/Chapter12`](https://github.com/PacktPublishing/Mastering-React-Test-Driven-Development-Second-Edition/tree/main/Chapter12)

# 为 reducer 和 saga 进行前期设计

在本节中，我们将像往常一样制定一个粗略的计划，说明我们将要构建什么。

让我们先看看实际的技术变化是什么，并讨论我们为什么要这样做。

我们将把提交客户的逻辑——`CustomerForm`中的`doSave`函数——从 React 组件移到 Redux 中。我们将使用 Redux reducer 来管理操作的状态：它是否正在提交、已完成或发生了验证错误。我们将使用 Redux saga 来执行异步操作。

## 为什么选择 Redux？

考虑到当前的应用功能集，实际上没有理由使用 Redux。然而，想象一下，在未来，我们希望支持以下功能：

+   在添加新客户后，`AppointmentForm`组件会在提交之前显示客户信息，而无需从服务器重新获取数据

+   在从`CustomerSearch`组件中找到客户并选择创建预约后，相同的客户信息将显示在预约屏幕上，无需重新获取数据

在这个未来的场景中，可能有必要有一个共享的 Redux 状态来存储客户数据。

我说“可能”，因为还有其他可能更简单的解决方案：组件上下文，或者可能某种类型的 HTTP 响应缓存。谁知道解决方案会是什么样子？没有具体要求很难说。

总结一下：在本章中，我们将使用 Redux 来存储客户数据。它没有比我们当前方法更多的实际好处，实际上，它有所有额外管道的缺点。然而，鉴于本书的教育目的，让我们继续前进。

## 设计存储状态和动作

Redux 存储只是一个具有一些访问限制的数据对象。这是我们希望它看起来的样子。该对象编码了`CustomerForm`已经使用关于保存客户数据的`fetch`请求的所有信息：

```js
{
  customer: {
    status: SUBMITTING | SUCCESSFUL | FAILED | ...
    // only present if the customer was saved successfully
    customer: { id: 123, firstName: "Ashley" ... },
    // only present if there are validation errors
    validationErrors: { phoneNumber: "..." },
    // only present if there was another type of error
    error: true | false
  }
}
```

Redux 通过命名动作来改变这个状态。我们将有以下动作：

+   `ADD_CUSTOMER_REQUEST`, 当用户按下提交客户按钮时调用。这触发了 saga，然后触发剩余的操作

+   `ADD_CUSTOMER_SUBMITTING`，当 saga 开始其工作时

+   `ADD_CUSTOMER_SUCCESSFUL`，当服务器保存客户并返回一个新的客户 ID。使用这个动作，我们还将保存新的客户信息到 reducer 中，以便以后使用

+   `ADD_CUSTOMER_VALIDATION_FAILED`，如果提供的客户数据无效

+   `ADD_CUSTOMER_FAILED`，如果服务器由于其他原因无法保存数据

作为参考，以下是现有代码，我们将从`CustomerForm`中提取这些代码。它都在一个名为`doSave`的函数中，尽管它相当长：

```js
const doSave = async () => {
  setSubmitting(true);
  const result = await global.fetch("/customers", {
    method: "POST",
    credentials: "same-origin",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(customer),
  });
  setSubmitting(false);
  if (result.ok) {
    setError(false);
    const customerWithId = await result.json();
    onSave(customerWithId);
  } else if (result.status === 422) {
    const response = await result.json();
    setValidationErrors(response.errors);
  } else {
    setError(true);
  }
};
```

我们将用 saga 和 reducer 的组合替换所有这些代码。我们将在下一节中开始从 reducer 开始。

# 测试驱动 reducer

在本节中，我们将测试驱动一个新的 reducer 函数，然后提取一些重复的代码。

一个 reducer 是一个简单的函数，它接受一个动作和当前存储状态作为输入，并返回一个新的状态对象作为输出。现在让我们按照以下方式构建它：

1.  创建一个名为`test/reducers/customer.test.js`的新文件（在新的目录中）。添加以下第一个测试，该测试检查如果 reducer 被一个未知动作调用，我们的 reducer 应该返回一个默认状态给我们的对象。这是 Redux reducer 的标准行为，所以你应该始终从一个这样的测试开始：

    ```js
    import { reducer } from "../../src/reducers/customer";
    describe("customer reducer", () => {
      it("returns a default state for an undefined existing state", () => {
        expect(reducer(undefined, {})).toEqual({
          customer: {},
          status: undefined,
          validationErrors: {},
          error: false
        });
      });
    });
    ```

1.  创建一个`src/reducers/customer.js`文件，如下所示，并让这个测试通过：

    ```js
    const defaultState = {
      customer: {},
      status: undefined,
      validationErrors: {},
      error: false
    };
    export const reducer = (state = defaultState, action) => {
      return state;
    };
    ```

1.  对于下一个测试，添加对`ADD_CUSTOMER_SUBMITTING`动作的支持，如下所示。这个测试检查当接收到这个动作时，状态值更新为`SUBMITTING`：

    ```js
    describe("ADD_CUSTOMER_SUBMITTING action", () => {
      const action = { type: "ADD_CUSTOMER_SUBMITTING" };
      it("sets status to SUBMITTING", () => {
        expect(reducer(undefined, action)).toMatchObject({
          status: "SUBMITTING"
        });
      });
    });
    ```

1.  通过用以下代码替换 reducer 的主体来实现这个过渡。我们可以直接使用`switch`语句（而不是使用`if`语句），因为我们确定我们将填充其他动作类型：

    ```js
    switch(action.type) {
      case "ADD_CUSTOMER_SUBMITTING":
        return { status: "SUBMITTING" };
      default:
        return state;
    }
    ```

1.  在`ADD_CUSTOMER_SUBMITTING` `describe`块中添加第二个测试，如下所示。这个测试指定了 reducer 动作的预期行为：我们不关心的任何状态（在这个例子中是`status`）保持不变：

    ```js
    it("maintains existing state", () => {
      expect(reducer({ a: 123 }, action)).toMatchObject({
        a: 123
      });
    });
    ```

1.  通过修改 reducer 来实现这个过渡，如下所示：

    ```js
    export const reducer = (state = defaultState, action) => {
      switch (action.type) {
        case "ADD_CUSTOMER_SUBMITTING":
          return { ...state, status: "SUBMITTING" };
        default:
          return state;
      }
    };
    ```

1.  我们需要处理`ADD_CUSTOMER_SUCCESSFUL`动作。从下面显示的两个测试开始。我通过一次编写两个测试来作弊，但这没关系，因为我知道它们是`ADD_CUSTOMER_SUBMITTING`测试的近似复制品：

    ```js
    describe("ADD_CUSTOMER_SUCCESSFUL action", () => {
      const customer = { id: 123 };
      const action = {
        type: "ADD_CUSTOMER_SUCCESSFUL",
        customer
      };
      it("sets status to SUCCESSFUL", () => {
        expect(reducer(undefined, action)).toMatchObject({
          status: "SUCCESSFUL"
        });
      });
      it("maintains existing state", () => {
        expect(
          reducer({ a: 123 }, action)
        ).toMatchObject({ a: 123 });
      });
    });
    ```

1.  要实现这个过渡，请向您的 reducer 添加一个最后的`case`语句，如下所示：

    ```js
    case "ADD_CUSTOMER_SUCCESSFUL":
      return { ...state, status: "SUCCESSFUL" };
    ```

1.  添加下一个测试，如下所示。动作提供了一个带有分配 ID 的新`customer`对象，我们应该将其保存在 reducer 中以供以后使用：

    ```js
    it("sets customer to provided customer", () => {
      expect(reducer(undefined, action)).toMatchObject({
        customer
      });
    });
    ```

1.  通过添加`customer`属性来实现这个过渡，如下所示：

    ```js
    case "ADD_CUSTOMER_SUCCESSFUL":
      return {
        ...state,
        status: "SUCCESSFUL",
        customer: action.customer
      };
    ```

1.  添加下一个`describe`块，用于`ADD_CUSTOMER_FAILED`，如下所示：

    ```js
    describe("ADD_CUSTOMER_FAILED action", () => {
      const action = { type: "ADD_CUSTOMER_FAILED" };
      it("sets status to FAILED", () => {
        expect(reducer(undefined, action)).toMatchObject({
          status: "FAILED"
        });
      });
      it("maintains existing state", () => {
        expect(
          reducer({ a: 123 }, action)
        ).toMatchObject({ a: 123 });
      });
    });
    ```

1.  通过向`switch` reducer 添加一个新的`case`语句来实现这两个测试的通过，如下所示：

    ```js
    case "ADD_CUSTOMER_FAILED":
      return { ...state, status: "FAILED" };
    ```

1.  我们还没有完成`ADD_CUSTOMER_FAILED`。在这种情况下，我们还想将`error`设置为`true`。回想一下，我们在`CustomerForm`组件中使用了`error`状态变量来标记何时发生了未解释的错误。我们需要在这里复制它。向`describe`块添加以下第三个测试：

    ```js
    it("sets error to true", () => {
      expect(reducer(undefined, action)).toMatchObject({
        error: true
      });
    });
    ```

1.  通过修改`case`语句来实现这个过渡，如下所示：

    ```js
    case "ADD_CUSTOMER_FAILED":
      return { ...state, status: "FAILED", error: true };
    ```

1.  为`ADD_CUSTOMER_VALIDATION_FAILED`动作添加测试，该动作发生在字段验证失败的情况下。代码如下所示：

    ```js
    describe("ADD_CUSTOMER_VALIDATION_FAILED action", () => {
      const validationErrors = { field: "error text" };
      const action = {
        type: "ADD_CUSTOMER_VALIDATION_FAILED",
        validationErrors
      };
      it("sets status to VALIDATION_FAILED", () => {
        expect(reducer(undefined, action)).toMatchObject({
          status: "VALIDATION_FAILED"
        });
      });
      it("maintains existing state", () => {
        expect(
          reducer({ a: 123 }, action)
        ).toMatchObject({ a: 123 });
      });
    });
    ```

1.  通过在 reducer 中添加另一个`case`语句来实现这些测试的通过，如下所示：

    ```js
    case "ADD_CUSTOMER_VALIDATION_FAILED":
      return { ...state, status: "VALIDATION_FAILED" };
    ```

1.  这个动作也需要第三个测试。这次，动作可以包含有关验证错误的错误信息，如下面的代码片段所示：

    ```js
    it("sets validation errors to provided errors", () => {
      expect(reducer(undefined, action)).toMatchObject({
        validationErrors
      });
    });
    ```

1.  按照下面的更改实现这个过渡：

    ```js
    case "ADD_CUSTOMER_VALIDATION_FAILED":
      return {
        ...state,
        status: "VALIDATION_FAILED",
        validationErrors: action.validationErrors
      };
    ```

这样就完成了 reducer，但在我们将其用于 saga 之前，我们不妨稍微简化一下这些测试？

## 提取 reducer 动作的生成函数

大多数 reducer 将遵循相同的模式：每个动作都将设置一些新数据以确保现有状态不会丢失。

让我们编写几个测试生成函数来帮我们完成这个任务，以帮助我们简化测试。按照以下步骤进行：

1.  创建一个新文件，`test/reducerGenerators.js`，并向其中添加以下函数：

    ```js
    export const itMaintainsExistingState = (reducer, action) => {
      it("maintains existing state", () => {
        const existing = { a: 123 };
        expect(
          reducer(existing, action)
        ).toMatchObject(existing);
      });
    };
    ```

1.  将以下`import`语句添加到`src/reducers/customer.test.js`的顶部：

    ```js
    import {
      itMaintainsExistingState
    } from "../reducerGenerators";
    ```

1.  修改您的测试以使用此函数，删除每个`describe`块中的测试，并用以下单行替换它：

    ```js
    itMaintainsExistingState(reducer, action);
    ```

1.  在`test/reducerGenerators.js`中，定义以下函数：

    ```js
    export const itSetsStatus = (reducer, action, value) => {
      it(`sets status to ${value}`, () => {
        expect(reducer(undefined, action)).toMatchObject({
          status: value
        });
      });
    };
    ```

1.  修改现有的`import`语句以引入新函数，如下所示：

    ```js
    import {
      itMaintainsExistingState,
      itSetsStatus
    } from "../reducerGenerators";
    ```

1.  修改你的测试以使用此函数，就像你之前做的那样。确保运行你的测试以证明它们可以工作！你的测试现在应该会短得多。以下是一个 `describe` 块的示例，用于 `ADD_CUSTOMER_SUCCESSFUL`：

    ```js
    describe("ADD_CUSTOMER_SUBMITTING action", () => {
      const action = { type: "ADD_CUSTOMER_SUBMITTING" };
      itMaintainsExistingState(reducer, action);
      itSetsStatus(reducer, action, "SUBMITTING");
    });
    ```

这就完成了 reducer。在我们继续 saga 之前，让我们将其与应用程序连接起来。我们根本不会使用它，但现在建立基础设施是好的。

## 设置存储和入口点

除了我们编写的 reducer 之外，我们还需要定义一个名为 `configureStore` 的函数，然后在我们应用程序启动时调用它。按照以下步骤进行：

1.  创建一个名为 `src/store.js` 的新文件，并包含以下内容。目前不需要测试这个文件，因为它有点像 `src/index.js`：连接一切的基础设施。然而，我们将在下一节测试 saga 时使用它：

    ```js
    import { createStore, combineReducers } from "redux";
    import {
     reducer as customerReducer
    } from "./reducers/customer";
    export const configureStore = (storeEnhancers = []) => 
      createStore(
        combineReducers({ customer: customerReducer }),
        storeEnhancers
      );
    ```

1.  在 `src/index.js` 中，在文件顶部添加以下两个 `import` 语句：

    ```js
    import { Provider } from "react-redux";
    import { configureStore } from "./store";
    ```

1.  然后，像下面这样将现有的 JSX 包装在 `Provider` 组件中。这就是我们的所有组件如何获得访问 Redux 存储的权限：

    ```js
    ReactDOM.createRoot(
      document.getElementById("root")
    ).render(
      <Provider store={configureStore()}>
        <BrowserRouter>
          <App />
        </BrowserRouter>
      </Provider>
    );
    ```

这样一来，我们就准备好编写复杂的部分了：saga。

# 测试驱动 saga

Saga 是一段特殊的代码，它使用 JavaScript 生成器函数来管理对 Redux 存储的异步操作。因为它非常复杂，我们实际上不会测试 saga 本身；相反，我们将向存储派发一个动作并观察结果。

在我们开始 saga 测试之前，我们需要一个名为 `renderWithStore` 的新测试辅助函数。

### 添加 renderWithStore 测试扩展

按照以下步骤进行：

1.  在 `test/reactTestExtensions.js` 的顶部，添加以下新的 `import` 语句：

    ```js
    import { Provider } from "react-redux";
    import { storeSpy } from "expect-redux";
    import { configureStore } from "../src/store";
    ```

expect-redux 包

为了做到这一点，我们将使用 NPM 中的 `expect-redux` 包，它已经包含在 `package.json` 文件中供你使用——确保在开始之前运行 `npm install`。

1.  添加一个新的变量 `store`，并在 `initializeReactContainer` 中初始化它，如下代码片段所示。这使用了来自 `expect-redux` 的 `storeSpy`，我们将在测试中用它来检查对存储的调用：

    ```js
    export let store;
    export const initializeReactContainer = () => {
      store = configureStore([storeSpy]);
      container = document.createElement("div");
      document.body.replaceChildren(container);
      reactRoot = ReactDOM.createRoot(container);
    };
    ```

1.  在 `renderWithRouter` 函数下方添加你的新渲染函数，如下代码片段所示：

    ```js
    export const renderWithStore = (component) =>
      act(() =>
        reactRoot.render(
          <Provider store={store}>{component}</Provider>
        )
      );
    ```

1.  最后，添加 `dispatchStore`，当我们在组件中开始派发动作时将需要它，如下所示：

    ```js
    export const dispatchToStore = (action) =>
      act(() => store.dispatch(action));
    ```

现在你已经拥有了开始测试连接到 Redux 存储的 sagas 和组件所需的所有辅助工具。所有这些都已就绪，让我们开始 saga 测试。

## 使用 expect-redux 编写期望

我们编写的 saga 将响应从 `CustomerForm` 组件派发的 `ADD_CUSTOMER_REQUEST` 动作。saga 的功能与本章开头 *设计存储状态和动作* 部分中列出的 `doSave` 函数相同。区别在于我们需要使用 saga 的 `put`、`call` 等函数调用。

让我们从编写一个名为 `addCustomer` 的生成器函数开始。按照以下步骤进行：

1.  创建一个新文件（在新的目录中），命名为 `test/sagas/customer.test.js`，并添加以下代码来设置我们的 `describe` 块。我们初始化一个 `store` 变量，我们的 sagas 和测试期望都将使用它。这是我们在 `initializeReactContainer` 测试辅助程序中之前使用的代码的重复——我们在这里不能使用它，因为我们不是在编写组件：

    ```js
    import { storeSpy, expectRedux } from "expect-redux";
    import { configureStore } from "../../src/store";
    describe("addCustomer", () => {
      let store;
      beforeEach(() => {
        store = configureStore([ storeSpy ]);
      });
    });
    ```

1.  在 `beforeEach` 块下面，添加以下辅助函数，它为我们提供了一种构建动作的更优雅的方式——你将在下一个测试中看到：

    ```js
    const addCustomerRequest = (customer) => ({
      type: "ADD_CUSTOMER_REQUEST",
      customer,
    });
    ```

1.  现在是第一个测试。我们的 saga 应该首先做什么？它必须更新我们的存储状态，以反映表单正在提交。这样，`CustomerForm` 组件就可以立即向用户显示提交指示器。我们使用 `expect-redux` 的期望来确保我们派发了正确的动作，如下所示：

    ```js
    it("sets current status to submitting", () => {
      store.dispatch(addCustomerRequest());
      return expectRedux(store)
        .toDispatchAnAction()
        .matching({ type: "ADD_CUSTOMER_SUBMITTING" });
    });
    ```

从测试中返回承诺

这个测试返回一个承诺。这是一个我们可以使用的快捷方式，而不是将我们的测试函数标记为 `async` 并使用 `await` 来设置期望。Jest 知道如果测试函数返回一个承诺，就需要等待。

1.  让我们从 saga 实现开始。创建一个名为 `src/sagas/customer.js` 的新文件，并添加以下内容。注意 `function*` 语法，它表示一个生成器函数，以及使用 `put` 向存储发射另一个动作：

    ```js
    import { put } from "redux-saga/effects";
    export function* addCustomer() {
      yield put({ type: "ADD_CUSTOMER_SUBMITTING" });
    }
    ```

生成器函数语法

我们在整个书中一直在使用的箭头函数语法不适用于生成器函数，因此我们需要回退到使用 `function` 关键字。

1.  在那个测试通过之前，我们需要使用 `addCustomer` saga 更新存储。从导入语句开始，将 `src/store.js` 更改为以下内容：

    ```js
    import {
      createStore,
      applyMiddleware,
      compose,
      combineReducers
    } from "redux";
    import createSagaMiddleware from "redux-saga";
    import { takeLatest } from "redux-saga/effects";
    import { addCustomer } from "./sagas/customer";
    import {
      reducer as customerReducer
    } from "./sagas/customer";
    ```

1.  在那些导入下面，添加以下 `rootSaga` 定义：

    ```js
    function* rootSaga() {
      yield takeLatest(
        "ADD_CUSTOMER_REQUEST",
        addCustomer
      );
    }
    ```

1.  现在，更新 `configureStore` 以包括 saga 中间件和“运行” `rootSaga`，如下所示。在此更改之后，你的测试应该可以通过：

    ```js
    export const configureStore = (storeEnhancers = []) => {
      const sagaMiddleware = createSagaMiddleware();
      const store = createStore(
        combineReducers({ customer: customerReducer }),
        compose(
          applyMiddleware(sagaMiddleware),
          ...storeEnhancers
        )
      );
      sagaMiddleware.run(rootSaga);
      return store;
    };
    ```

这完成了 saga 的第一个测试，并放置了所有必要的管道。你还看到了如何使用 `put`。接下来，让我们介绍 `call`。

## 使用 sagas 进行异步请求

在 saga 中，`call` 允许我们执行异步请求。现在让我们介绍这一点。按照以下步骤进行：

1.  添加以下测试，以检查对 `fetch` 的调用：

    ```js
    it("sends HTTP request to POST /customers", async () => {
      const inputCustomer = { firstName: "Ashley" };
      store.dispatch(addCustomerRequest(inputCustomer));
      expect(global.fetch).toBeCalledWith(
        "/customers",
        expect.objectContaining({
          method: "POST",
        })
      );
    });
    ```

1.  为了使这个功能正常工作，我们需要在 `global.fetch` 上定义一个间谍。将 `beforeEach` 块更改为如下，包括新的客户常量：

    ```js
    beforeEach(() => {
      jest.spyOn(global, "fetch");
      store = configureStore([ storeSpy ]);
    });
    ```

1.  在 `src/sagas/customer.js` 中，更新 saga 导入以包括 `call` 函数，如下所示：

    ```js
    import { put, call } from "redux-saga/effects";
    ```

1.  现在，创建一个名为 `fetch` 的函数，并在 saga 中使用 `call` 来调用它，如下所示。之后，你的测试应该可以通过：

    ```js
    const fetch = (url, data) =>
      global.fetch(url, {
        method: "POST",
      });
    export function* addCustomer({ customer }) {
      yield put({ type: "ADD_CUSTOMER_SUBMITTING" });
      yield call(fetch, "/customers", customer);
    }
    ```

1.  好吧——现在，让我们添加一个测试来添加我们的 `fetch` 请求的配置，如下所示：

    ```js
    it("calls fetch with correct configuration", async () => {
      const inputCustomer = { firstName: "Ashley" };
      store.dispatch(addCustomerRequest(inputCustomer));
      expect(global.fetch).toBeCalledWith(
        expect.anything(),
        expect.objectContaining({
          credentials: "same-origin",
          headers: { "Content-Type": "application/json" },
        })
      );
    });
    ```

1.  为了让它通过，请将以下行添加到 `fetch` 定义中：

    ```js
    const fetch = (url, data) =>
      global.fetch(url, {
        method: "POST",
        credentials: "same-origin",
        headers: { "Content-Type": "application/json" }
      });
    ```

1.  现在，让我们测试一下我们是否正在发送正确的客户数据。以下是我们可以这样做的方法：

    ```js
    it("calls fetch with customer as request body", async () => {
      const inputCustomer = { firstName: "Ashley" };
      store.dispatch(addCustomerRequest(inputCustomer));
      expect(global.fetch).toBeCalledWith(
        expect.anything(),
        expect.objectContaining({
          body: JSON.stringify(inputCustomer),
        })
     );
    });
    ```

1.  为了实现这一点，完成 `fetch` 定义，如下所示：

    ```js
    const fetch = (url, data) =>
      global.fetch(url, {
        body: JSON.stringify(data),
        method: "POST",
        credentials: "same-origin",
        headers: { "Content-Type": "application/json" }
      });
    ```

1.  对于下一个测试，我们希望在 `fetch` 调用成功返回时分派一个 `ADD_CUSTOMER_SUCCESSFUL` 事件。它使用一个名为 `customer` 的常量，我们将在下一步定义。以下是我们需要执行的代码：

    ```js
    it("dispatches ADD_CUSTOMER_SUCCESSFUL on success", () => {
      store.dispatch(addCustomerRequest());
      return expectRedux(store)
        .toDispatchAnAction()
        .matching({
          type: "ADD_CUSTOMER_SUCCESSFUL",
          customer
        });
    });
    ```

1.  在我们之前设置 `fetch` 间谍之前，我们没有设置返回值。因此，现在创建一个 `customer` 常量，并设置 `fetch` 间谍以返回它，如下所示：

    ```js
    const customer = { id: 123 };
    beforeEach(() => {
      jest
        .spyOn(global, "fetch")
        .mockReturnValue(fetchResponseOk(customer));
      store = configureStore([ storeSpy ]);
    });
    ```

1.  按照如下方式导入 `fetchResponseOk`。在此之后，你将能够运行你的测试：

    ```js
    import { fetchResponseOk } from "../builders/fetch";
    ```

1.  通过处理 `call` 函数的结果，使测试通过，如下所示：

    ```js
    export function* addCustomer({ customer }) {
      yield put({ type: "ADD_CUSTOMER_SUBMITTING" });
    const result = yield call(fetch, "/customers", customer);
      const customerWithId = yield call([result, "json"]);
      yield put({
        type: "ADD_CUSTOMER_SUCCESSFUL",
        customer: customerWithId
      });
    }
    ```

1.  如果 `fetch` 调用不成功，可能是由于网络故障，该怎么办？添加一个测试，如下所示：

    ```js
    it("dispatches ADD_CUSTOMER_FAILED on non-specific error", () => {
      global.fetch.mockReturnValue(fetchResponseError());
      store.dispatch(addCustomerRequest());
      return expectRedux(store)
        .toDispatchAnAction()
        .matching({ type: "ADD_CUSTOMER_FAILED" });
    });
    ```

1.  该测试使用了 `fetchResponseError`；现在像这样导入它：

    ```js
    import {
      fetchResponseOk,
      fetchResponseError
    } from "../builders/fetch";
    ```

1.  通过将现有代码包裹在一个带有 `else` 子句的 `if` 语句中，使测试通过，如下所示：

    ```js
    export function* addCustomer({ customer }) {
      yield put({ type: "ADD_CUSTOMER_SUBMITTING" });
      const result = yield call(
        fetch,
        "/customers",
        customer
      );
      if(result.ok) {
       const customerWithId = yield call(
          [result, "json"]
        );
        yield put({
          type: "ADD_CUSTOMER_SUCCESSFUL",
          customer: customerWithId
        });
      } else {
        yield put({ type: "ADD_CUSTOMER_FAILED" });
      }
    }
    ```

1.  最后，添加一个针对更具体类型的失败的测试——验证失败，如下所示：

    ```js
    it("dispatches ADD_CUSTOMER_VALIDATION_FAILED if validation errors were returned", () => {
      const errors = {
        field: "field",
        description: "error text"
      };
      global.fetch.mockReturnValue(
        fetchResponseError(422, { errors })
      );
      store.dispatch(addCustomerRequest());
      return expectRedux(store)
        .toDispatchAnAction()
        .matching({
          type: "ADD_CUSTOMER_VALIDATION_FAILED",
          validationErrors: errors
        });
    });
    ```

1.  使用以下代码使测试通过：

    ```js
    export function* addCustomer({ customer }) {
      yield put({ type: "ADD_CUSTOMER_SUBMITTING" });
      const result = yield call(fetch, "/customers", customer);
      if(result.ok) {
        const customerWithId = yield call(
         [result, "json"]
        );
        yield put({
          type: "ADD_CUSTOMER_SUCCESSFUL",
          customer: customerWithId
        });
      } else if (result.status === 422) {
        const response = yield call([result, "json"]);
        yield put({
          type: "ADD_CUSTOMER_VALIDATION_FAILED",
          validationErrors: response.errors
        });
      } else {
        yield put({ type: "ADD_CUSTOMER_FAILED" });
      }
    }
    ```

现在 saga 已经完成。将此函数与我们要替换的 `CustomerForm` 中的函数进行比较：`doSave`。结构是相同的。这是一个好的迹象，表明我们准备好从 `CustomerForm` 中移除 `doSave`。

在下一节中，我们将更新 `CustomerForm` 以使用我们新的 Redux 存储。

# 将组件状态切换为 Redux 状态

现在 saga 和 reducer 已经完成并准备好在 `CustomerForm` React 组件中使用。在本节中，我们将替换 `doSave` 的使用，然后作为最后的润色，我们将把我们的 React Router 导航推入 saga，从 `App` 中移除 `onSave` 回调。

## 通过分派 Redux 动作提交 React 表单

在本章的开头，我们探讨了这次更改的目的基本上是将 `CustomerForm` 的 `doSave` 函数移植到 Redux 动作中。

使用我们新的 Redux 设置，我们使用组件状态来显示提交指示器并显示任何验证错误。这些信息现在存储在 Redux 存储中，而不是组件状态中。因此，除了分派一个替换 `doSave` 的动作外，组件还需要从存储中读取状态。组件状态变量可以被删除。

这对我们的测试也有影响。由于 saga 测试失败模式，我们的 `CustomerForm` 组件测试只需要处理 Redux 存储的各种状态，我们将使用我们的 `dispatchToStore` 扩展来操作这些状态。

我们将首先使我们的组件具有 Redux 意识，如下所示：

1.  将以下 `import` 语句添加到 `test/CustomerForm.test.js` 的顶部：

    ```js
    import { expectRedux } from "expect-redux";
    ```

1.  更新测试扩展的 `import` 语句，将 `render` 替换为 `renderWithStore`，并添加两个新的导入，如下所示：

    ```js
    import {
      initializeReactContainer,
      renderWithStore,
      dispatchToStore,
      store,
      ...
    } from "./reactTestExtensions";
    ```

1.  将所有对`render`的调用替换为`renderWithStore`。如果你正在进行搜索和替换操作，请注意：单词*render*出现在一些测试描述中，你应该保持它们不变。

1.  让我们重写一个单独的测试：描述为`sends HTTP request to POST /customers when submitting data`的那个测试。将该测试更改为以下内容：

    ```js
    it("dispatches ADD_CUSTOMER_REQUEST when submitting data", async () => {
      renderWithStore(
        <CustomerForm {...validCustomer} />
      );
      await clickAndWait(submitButton());
      return expectRedux(store)
        .toDispatchAnAction()
        .matching({
          type: 'ADD_CUSTOMER_REQUEST',
          customer: validCustomer
      });
    });
    ```

1.  为了使这个通过，我们将使用并排实现来确保我们的其他测试继续通过。在`handleSubmit`中添加以下代码片段中突出显示的行。这调用了一个我们很快就会定义的新`addCustomerRequest`属性：

    ```js
    const handleSubmit = async (event) => {
      event.preventDefault();
      const validationResult = validateMany(
        validators, customer
      );
      if (!anyErrors(validationResult)) {
        await doSave();
        dispatch(addCustomerRequest(customer));
      } else {
        setValidationErrors(validationResult);
      }
    };
    ```

1.  这使用了`useDispatch`钩子。现在按照以下方式导入它：

    ```js
    import {  useDispatch } from "react-redux";
    ```

1.  然后，将此行添加到`CustomerForm`组件的顶部：

    ```js
    const dispatch = useDispatch();
    ```

1.  为了使测试通过，剩下的只是`addCustomerRequest`的定义，你可以在`import`语句和`CustomerForm`组件定义之间添加它，如下所示：

    ```js
    const addCustomerRequest = (customer) => ({
      type: "ADD_CUSTOMER_REQUEST",
      customer,
    });
    ```

到目前为止，你的组件现在是 Redux 感知的，并且正在向 Redux 派遣正确的动作。剩余的工作是修改组件以处理来自 Redux 的验证错误，而不是组件状态。

## 在组件中使用存储状态

现在，是时候引入`useSelector`钩子来从存储中提取状态了。我们将从`ADD_CUSTOMER_FAILED`通用错误动作开始。回想一下，当 reducer 接收到这个动作时，它会将`error`存储状态值更新为`true`。按照以下步骤操作：

1.  找到名为`renders error message when fetch call fails`的测试，并用下面的实现替换它。它模拟了一个`ADD_CUSTOMER_FAILED`动作，以确保所有 Redux 连接都是正确的。别忘了从测试函数中移除`async`关键字：

    ```js
    it("renders error message when error prop is true", () => {
      renderWithStore(
        <CustomerForm {...validCustomer} />
      );
      dispatchToStore({ type: "ADD_CUSTOMER_FAILED" });
      expect(element("[role=alert]")).toContainText(
        "error occurred"
      );
    });
    ```

1.  在`src/CustomerForm.js`的顶部添加一个`useSelector`钩子的`import`语句，如下所示：

    ```js
    import {
      useDispatch,
      useSelector
     } from "react-redux";
    ```

1.  在`CustomerForm`组件的顶部调用`useSelector`钩子，如下面的代码片段所示。它从 Redux 存储的`customer`部分提取出`error`状态值：

    ```js
    const {
      error,
    } = useSelector(({ customer }) => customer);
    ```

1.  删除任何调用`setError`的行。在`doSave`中有两个出现。

1.  现在，你可以删除在`CustomerForm`组件顶部使用`useState`钩子定义的`error`/`setError`变量对。由于`error`被声明了两次，你的测试将无法运行，直到你这样做。在这个阶段，你的测试应该通过。

1.  下一个测试，`clears error message when fetch call succeeds`，可以被删除。根据现状，reducer 实际上并没有做这件事；完成它是*练习*部分的一个练习。

1.  找到`does not submit the form when there are validation errors`测试，并按以下方式更新它。它应该已经通过：

    ```js
    it("does not submit the form when there are validation errors", async () => {
      renderWithStore(
        <CustomerForm original={blankCustomer} />
      );
      await clickAndWait(submitButton());
      return expectRedux(store)
        .toNotDispatchAnAction(100)
        .ofType("ADD_CUSTOMER_REQUEST");
    });
    ```

`toNotDispatchAnAction`匹配器

这个匹配器应该始终与超时一起使用，例如在这种情况下使用 100 毫秒。这是因为，在异步环境中，事件可能只是发生得较慢，而不是根本不发生。

1.  找到下一个测试，`renders field validation errors from server`。用以下代码替换它，记得从函数定义中删除`async`关键字：

    ```js
    it("renders field validation errors from server", () => {
      const errors = {
        phoneNumber: "Phone number already exists in the system"
      };
      renderWithStore(
        <CustomerForm {...validCustomer} />
      );
      dispatchToStore({
        type: "ADD_CUSTOMER_VALIDATION_FAILED",
        validationErrors: errors
      });
      expect(
        errorFor(phoneNumber)
      ).toContainText(errors.phoneNumber);
    });
    ```

1.  要使这个通过，我们需要从 Redux 客户存储中提取`validationErrors`。这里有一些复杂性：组件已经有一个`validationErrors`状态变量，它涵盖了服务器和客户端验证错误。我们无法完全替换它，因为它除了处理服务器错误外，还处理客户端错误。

因此，让我们将服务器返回的属性*重命名*，如下所示：

```js
const {
  error,
  validationErrors: serverValidationErrors,
} = useSelector(({ customer }) => customer);
```

设计问题

这突显了我们原始代码中的设计问题。`validationErrors`状态变量有两个用途，它们被混淆了。我们在这里的更改将分离这些用途。

1.  我们还没有完成这个测试。更新`renderError`函数以渲染`validationErrors`（客户端验证）和`serverValidationErrors`（服务器端验证）的错误，如下所示：

    ```js
    const renderError = fieldName => {
      const allValidationErrors = {
        ...validationErrors,
        ...serverValidationErrors
      };
      return (
        <span id={`${fieldname}error`} role="alert">
          {hasError(allValidationErrors, fieldName)
            ? allValidationErrors[fieldname]
            : ""}
        </span>
      );
    };
    ```

1.  我们接下来需要查看的测试是提交指示器的测试。我们将更新这些测试以响应存储操作而不是表单提交。这是第一个：

    ```js
    it("displays indicator when form is submitting", () => {
      renderWithStore(
        <CustomerForm {...validCustomer} />
      );
      dispatchToStore({
       type: "ADD_CUSTOMER_SUBMITTING"
      });
      expect(
        element(".submittingIndicator")
      ).not.toBeNull();
    });
    ```

1.  要使这个通过，需要在`useSelector`调用中添加`status`，如下所示：

    ```js
    const {
      error,
      status,
      validationErrors: serverValidationErrors,
    } = useSelector(({ customer }) => customer);
    ```

1.  删除在此组件内部任何调用`setSubmitting`的地方。

1.  删除`submitting`状态变量，并用以下代码行替换它。现在测试应该通过了：

    ```js
    const submitting = status === "SUBMITTING";
    ```

1.  然后，更新名为`hides indicator when form has submitted`的测试，如下所示。这个测试不需要对生产代码进行任何更改：

    ```js
    it("hides indicator when form has submitted", () => {
      renderWithStore(
        <CustomerForm {...validCustomer} />
      );
      dispatchToStore({
        type: "ADD_CUSTOMER_SUCCESSFUL"
      });
      expect(element(".submittingIndicator")).toBeNull();
    });
    ```

1.  最后，找到`disable the submit button when submitting`测试，并以与*步骤 12*相同的方式进行修改。

测试更改到此结束，`doSave`几乎完全冗余。然而，对`onSave`的调用仍然需要迁移到 Redux saga 中，我们将在下一节中这样做。

## 在 Redux saga 中导航路由历史

回想一下，是`App`组件渲染`CustomerForm`，并且`App`通过将一个函数传递给`CustomerForm`的`onSave`属性来导致页面导航。当客户信息已提交时，用户将被移动到`/addAppointment`路由。

但是，现在表单提交发生在 Redux saga 中，我们如何调用`onSave`属性？答案是，我们不能。相反，我们可以将页面导航移动到 saga 本身，并完全删除`onSave`属性。

要做到这一点，我们必须更新`src/index.js`以使用`HistoryRouter`而不是`BrowserRouter`。这允许你传递自己的历史单例对象，然后你可以显式地构造它并通过 saga 访问它。按照以下步骤进行：

1.  创建一个名为`src/history.js`的新文件，并将以下内容添加到其中。这与我们在`test/reactTestExtensions.js`中已经做过的非常相似：

    ```js
    import { createBrowserHistory } from "history";
    export const appHistory = createBrowserHistory();
    ```

1.  更新`src/index.js`，如下所示：

    ```js
    import React from "react";
    import ReactDOM from "react-dom/client";
    import { Provider } from "react-redux";
    import {
      unstable_HistoryRouter as HistoryRouter
    } from "react-router-dom";
    import { appHistory } from "./history";
    import { configureStore } from "./store";
    import { App } from "./App";
    ReactDOM.createRoot(
      document.getElementById("root")
    ).render(
      <Provider store={configureStore()}>
        <HistoryRouter history={appHistory}>
          <App />
       </HistoryRouter>
      </Provider>
    );
    ```

1.  现在，我们可以在 saga 中使用`appHistory`。打开`test/sagas/customer.js`，并将以下`import`语句添加到文件顶部：

    ```js
    import { appHistory } from "../../src/history";
    ```

1.  然后，添加以下两个测试以定义导航应该如何发生：

    ```js
    it("navigates to /addAppointment on success", () => {
      store.dispatch(addCustomerRequest());
      expect(appHistory.location.pathname).toEqual(
        "/addAppointment"
      );
    });
    it("includes the customer id in the query string when navigating to /addAppointment", () => {
      store.dispatch(addCustomerRequest());
      expect(
        appHistory.location.search
      ).toEqual("?customer=123");
    });
    ```

1.  要使这些测试通过，首先打开 `src/sagas/customer.js` 并添加以下 `import` 语句：

    ```js
    import { appHistory } from "../history";
    ```

1.  然后，更新 `addCustomer` 生成器函数，在成功添加客户后进行导航，如下所示：

    ```js
    export function* addCustomer({ customer }) {
      ...
      yield put({
        type: "ADD_CUSTOMER_SUCCESSFUL",
        customer: customerWithId,
      });
      appHistory.push(
        `/addAppointment?customer=${customerWithId.id}`
      );
    }
    ```

1.  现在，剩下要做的就是从 `App` 和 `CustomerForm` 中删除现有的 `onSave` 配管。打开 `test/App.test.js` 并删除以下三个测试：

    +   `使用正确的配置调用 fetch`

    +   `在 CustomerForm 提交后导航到/addAppointment`

    +   `在 CustomerForm 提交后将保存的客户传递给 AppointmentFormLoader`

1.  您还可以删除在标记为 `when POST 请求返回错误` 的嵌套 `describe` 块中设置 `global.fetch` 的 `beforeEach` 块。

1.  在 `src/App.js` 中，删除 `transitionToAddAppointment` 的定义，并将 `/addCustomer` 路由的 `onSave` 属性更改为无，如下代码片段所示。此时，您的 `App` 测试应该已经通过：

    ```js
    <Route
      path="/addCustomer"
      element={<CustomerForm original={blankCustomer} />}
    />
    ```

1.  现在，我们可以从 `CustomerForm` 中删除 `onSave` 属性。首先，从 `CustomerForm` 测试套件中删除以下不再必要的测试：

    +   `当表单提交时通知 onSave`

    +   `如果 POST 请求返回错误则不通知 onSave`

1.  从 `CustomerForm` 组件中删除 `onSave` 属性。

1.  最后，从 `handleSubmit` 中移除对 `doSave` 的调用。此函数不再等待任何内容，因此您可以从函数定义中安全地移除 `async`。此时，所有您的测试应该都通过了。

您现在已经看到了如何将 Redux 存储集成到您的 React 组件中，以及如何在 Redux 桥接中控制 React Router 导航。

如果一切顺利，您的应用程序现在应该已经运行，由 Redux 管理工作流程。

# 摘要

这是对 Redux 及如何使用 TDD 重构您的应用程序进行的一次快速浏览。

如本章引言中所警告的，Redux 是一个复杂的库，它将大量的额外配管引入到您的应用程序中。幸运的是，测试方法很简单。

在下一章中，我们将添加另一个库：Relay，GraphQL 客户端。

# 练习

+   修改客户还原器以确保在 `ADD_CUSTOMER_SUCCESSFUL` 动作发生时将 `error` 重置为 `false`。

# 进一步阅读

更多信息，请参阅以下资源：

+   MDN 关于生成器函数的文档：

[`developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/function*`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/function*)

+   `expect-redux` 包的主页：

[`github.com/rradczewski/expect-redux`](https://github.com/rradczewski/expect-redux)
