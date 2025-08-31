

# 第七章：测试 useEffect 和模拟组件

在上一章中，你看到了如何使用测试替身来验证用户操作（如点击提交按钮）时发生的网络请求。我们还可以使用它们来验证组件挂载时的副作用，例如当我们从服务器获取组件需要的数据时。此外，测试替身可以用来验证子组件的渲染。这两种用例通常与 **容器组件** 一起发生，容器组件负责简单地加载数据并将其传递给另一个组件进行显示。

在本章中，我们将构建一个新的组件，`AppointmentsDayViewLoader`，它从服务器加载当天的预约并将其传递给我们在 *第二章* 中实现的 `AppointmentsDayView` 组件，*渲染列表和详情视图*。通过这样做，用户可以查看今天发生的预约列表。

在本章中，我们将涵盖以下主题：

+   模拟子组件

+   使用 `useEffect` 在挂载时获取数据

+   `jest.mock` 调用的变体

这些可能是你在测试驱动 React 组件时遇到的最困难的任务。

# 技术要求

本章的代码文件可以在以下位置找到：[`github.com/PacktPublishing/Mastering-React-Test-Driven-Development-Second-Edition/tree/main/Chapter07`](https://github.com/PacktPublishing/Mastering-React-Test-Driven-Development-Second-Edition/tree/main/Chapter07)

# 模拟子组件

在本节中，我们将使用 `jest.mock` 测试辅助函数来用模拟实现替换子组件。然后，我们将编写期望来检查我们是否向子组件传递了正确的属性，并且它是否正确地渲染在屏幕上。

但首先，让我们详细了解一下模拟组件是如何工作的。

## 如何模拟组件，以及为什么？

本章我们将构建的组件具有以下形状：

```js
export const AppointmentsDayViewLoader = ({ today }) => {
  const [appointments, setAppointments] = useState([]);
  useEffect(() => {
    // fetch data from the server
    const result = await global.fetch(...);
    // populate the appointments array:
    setAppointments(await result.json());
  }, [today]);
  return (
    <AppointmentsDayView appointments={appointments} />
  );
};
```

其目的是显示给定日期的所有当前预约。然后，这些信息作为 `today` 属性传递给组件。组件的职责是从服务器获取数据，然后将其传递给之前构建并已测试过的 `AppointmentsDayView` 组件。

考虑我们可能需要的测试。首先，我们想要一个测试来证明 `AppointmentsDayView` 在没有显示任何预约的情况下加载。然后，我们想要一些测试来验证我们的 `global.fetch` 调用是否成功调用，并且返回的数据被传递到 `AppointmentsDayView`。

我们如何测试 `AppointmentsDayView` 是否以正确数据调用？我们可以在 `AppointmentsDayView` 的测试套件中重复一些已经编写的测试 – 例如，通过测试是否显示了一个预约列表，以及显示的相关预约数据。

然而，这样我们就会在我们的测试套件中引入重复。如果我们修改`AppointmentsDayView`的工作方式，我们将有两个地方需要更新测试。

另一个选择是使用间谍对象模拟组件。为此，我们可以使用`jest.mock`函数，与间谍一起使用。这将是这样看起来：

```js
jest.mock("../src/AppointmentsDayView", () => ({
  AppointmentsDayView: jest.fn(() => (
    <div id="AppointmentsDayView" />
  )),
}));
```

函数的第一个参数是要模拟的文件路径。它必须与传递给`import`语句的路径匹配。这个函数正在模拟整个模块：

```js
import { MyComponent } from "some/file/path";
jest.mock("/some/file/path", ...);
describe("something that uses MyComponent", () => {
});
```

在前面的代码中，Jest 将这个调用提升到文件顶部，并挂钩到导入逻辑，以便当`import`语句运行时，你的模拟会被返回。

无论何时在测试套件或被测试的组件中引用`AppointmentsDayView`，你都会得到这个模拟值而不是真实的组件。而不是渲染我们的日视图，我们会得到一个具有`AppointmentsDayView`的`id`值的单个`div`。

第二个参数是`AppointmentsDayView`。

因为模拟定义被提升到文件顶部，所以你无法在这个函数中引用任何变量：它们在你运行函数之前还没有被定义。然而，你可以编写 JSX，就像我们在这里所做的那样！

组件模拟设置的复杂性

这段代码非常晦涩难懂，我知道。幸运的是，你通常只需要写一次。当我需要在一个测试套件中引入一个新的模拟时，我经常发现自己是在复制粘贴模拟。我会查找我在其他测试套件中写的上一个模拟，并将其复制过来，更改相关细节。

所以，现在有一个大问题：你为什么要这样做？

首先，使用模拟可以通过鼓励具有独立表面的多个测试套件来改进测试组织。如果父组件和其子组件都是非平凡组件，那么为这些组件提供两个单独的测试套件可以帮助减少测试套件的复杂性。

父组件的测试套件将只包含少量测试，以证明子组件已被渲染并传递了预期的属性值。

通过在父组件的测试套件中模拟子组件，你实际上是在说，“*我现在想忽略这个子组件，但我保证我会在其他地方测试其功能!*”

另一个原因是，你可能已经对子组件进行了测试。这就是我们发现自己所处的场景：我们已经有`AppointmentsDayView`的测试，所以除非我们想要重复，否则在它被使用的地方模拟组件是有意义的。

这个原因的扩展是使用库组件。因为它们是由别人构建的，所以你有理由相信它们已经过测试并且能正确工作。而且由于它们是库组件，它们可能已经执行了一些相当复杂的操作，所以在你的测试中渲染它们可能会产生意外的副作用。

可能你有一个库组件，它可以构建各种复杂的 HTML 小部件，而你不想让测试代码知道这一点。相反，你可以将其视为**黑盒**。在这种情况下，最好是验证传递给组件的属性值，再次相信该组件按预期工作。

库组件通常具有复杂的组件 API，允许以多种方式配置组件。模拟组件允许你编写合同测试，确保你正确设置了属性。我们将在*第十一章* *测试驱动 React Router*中看到这一点，当我们模拟 React Router 的`Link`组件时。

模拟组件的最后一个原因是它们在挂载时可能有副作用，例如执行网络请求以拉取数据。通过模拟组件，你的测试套件不需要考虑这些副作用。我们将在*第八章* *构建应用程序组件*中这样做。

说了这么多，让我们开始构建我们的新组件。

## 测试初始组件属性

我们将首先为新的组件构建一个测试套件：

1.  创建一个新文件，`test/AppointmentsDayViewLoader.js`，并添加以下所有导入。我们不仅导入正在测试的组件（`AppointmentsDayViewLoader`），还导入我们将要模拟的子组件（`AppointmentsDayView`）：

    ```js
    import React from "react";
    import {
      initializeReactContainer,
      render,
      element,
    } from "./reactTestExtensions";
    import {
      AppointmentsDayViewLoader
    } from "../src/AppointmentsDayViewLoader";
    import {
      AppointmentsDayView
    } from "../src/AppointmentsDayView";
    ```

1.  在导入下面添加模拟设置：

    ```js
    jest.mock("../src/AppointmentsDayView", () => ({
      AppointmentsDayView: jest.fn(() => (
        <div id="AppointmentsDayView" />
      )),
    }));
    ```

1.  从这里显示的第一个测试开始。这检查我们刚刚模拟的组件是否被渲染。模拟渲染一个具有`id`值为`AppointmentsDayView`的`div`元素。测试使用`element`辅助函数查找`id`值，并检查它不为空：

    ```js
    describe("AppointmentsDayViewLoader", () => {
      beforeEach(() => {
        initializeReactContainer();
      });
      it("renders an AppointmentsDayView", () => {
        await render(<AppointmentsDayViewLoader />);
        expect(
          element("#AppointmentsDayView")
        ).not.toBeNull();
      });
    });
    ```

使用 ID 属性

如果你熟悉用于识别组件的`data-testid`。如果你想使用这些模拟技术与 React Testing Library 一起使用，那么你可以使用`data-testid`而不是`id`属性，然后使用`queryByTestId`函数查找你的元素。

虽然通常建议不要在测试套件中选择元素时依赖`data-testid`，但这并不适用于模拟组件。你需要 ID 来区分它们，因为你可能会遇到多个由同一父组件渲染的模拟组件。给每个组件分配 ID 是找到这些 DOM 存在性测试的最简单方法。记住，模拟永远不会超出你的单元测试环境，所以使用 ID 没有害处。

更多关于使用 React Testing Library 的模拟策略的讨论，请访问[`reacttdd.com/mocking-with-react-testing-library`](https://reacttdd.com/component-mocks-with-react-testing-library/)。

1.  让这个测试通过。创建一个新文件，`src/AppointmentsDayViewLoader.js`，并继续填写实现，如下所示。它只是渲染组件，这正是测试所要求的：

    ```js
    import React from "react";
    import {
      AppointmentsDayView
    } from "./AppointmentsDayView";
    export const AppointmentsDayViewLoader = () => (
      <AppointmentsDayView />
    );
    ```

1.  下次测试的时间到了。我们将检查发送给函数的 props 的初始值。

`AppointmentsDayView`是我们期望的结果。我们将通过使用`toBeCalledWith`匹配器来实现这一点，我们已经使用过它了。注意`expect.anything()`的第二个参数值：这是必需的，因为 React 在渲染组件函数时传递第二个参数。你永远不会需要在你的代码中关心这个参数——这是 React 实现的一个内部细节——因此我们可以安全地忽略它。我们将使用`expect.anything`来断言我们不在乎那个参数是什么：

```js
it("initially passes empty array of appointments to AppointmentsDayView", () => {
  await render(<AppointmentsDayViewLoader />);
  expect(AppointmentsDayView).toBeCalledWith(
    { appointments: [] },
    expect.anything()
  );
});
```

验证 props 及其在 DOM 中的存在

在这两个测试中，测试传递给模拟的 props 以及模拟值是否渲染在 DOM 中非常重要，正如我们所做的。在*第八章* *构建应用程序组件*中，我们将看到一个案例，其中我们想要检查在用户操作后模拟组件是否已卸载。

1.  通过更新你的组件定义来通过测试，如下所示：

    ```js
    export const AppointmentsDayViewLoader = () => (
      <AppointmentsDayView appointments={[]} />
    );
    ```

你刚刚使用了你的第一个模拟组件！你已经看到了如何创建模拟，以及验证其使用的两种类型的测试。接下来，我们将添加一个`useEffect`钩子，在组件挂载时加载数据，并通过`appointments` prop 传递它。

# 使用`useEffect`在挂载时获取数据

我们将加载的预约数据来自一个接受开始和结束日期的端点。这些值将结果过滤到特定的时间范围：

```js
GET /appointments/<from>-<to>
```

我们的新组件接收一个`today` prop，它是一个包含当前时间值的`Date`对象。我们将从`today` prop 计算`from`和`to`日期，并构建一个 URL 传递给`global.fetch`。

要达到这个目标，首先，我们将简要介绍一些关于测试`useEffect`钩子的理论。然后，我们将实现一个新的`renderAndWait`函数，因为我们将在组件挂载时调用一个 promise。最后，我们将使用这个函数在我们的新测试中，构建完整的`useEffect`实现。

## 理解`useEffect`钩子

`useEffect`钩子是 React 执行副作用的方式。想法是，你提供一个函数，该函数将在钩子的任何依赖项更改时运行。这个依赖项列表是`useEffect`调用的第二个参数。

让我们再次看看我们的示例：

```js
export const AppointmentsDayViewLoader = ({ today }) => {
  useEffect(() => {
    // ... code runs here
  }, [today]);

  // ... render something
}
```

当`today` prop 改变时，钩子代码将运行。这包括当组件首次挂载时。当我们进行测试驱动时，我们将从一个空的依赖项列表开始，然后使用一个特定的测试来强制在组件使用新的`today` prop 值重新挂载时刷新。

你传递给`useEffect`的函数应该返回另一个函数。这个函数执行清理操作：它在值改变时被调用，尤其是在钩子函数再次被调用之前，这使你能够取消任何正在运行的任务。

我们将在 *第十五章* *添加动画* 中详细探讨这个返回函数。然而，现在你应该知道这会影响我们调用 Promise 的方式。我们不能这样做：

```js
useEffect(async () => { ... }, []);
```

将外部函数定义为 `async` 意味着它返回一个 Promise，而不是一个函数。我们必须这样做：

```js
useEffect(() => {
  const fetchAppointments = async () => {
    const result = await global.fetch(...);      
    setAppointments(await result.json());
  };
  fetchAppointments();
}, [today]);
```

当运行测试时，如果你在 useEffect Hook 内直接调用 `global.fetch`，你会收到 React 的警告。它会提醒你 `useEffect` Hook 不应该返回一个 Promise。

在 useEffect Hook 函数中使用设置器

React 保证像 `setAppointments` 这样的设置器保持静态。这意味着它们不需要出现在 useEffect 依赖列表中。

要开始我们的实现，我们需要确保我们的测试为运行 Promise 的 render 调用做好准备。

## 添加 renderAndWait 辅助函数

就像我们对 clickAndWait 和 submitAndWait 所做的那样，现在我们需要 renderAndWait。这将渲染组件，然后等待我们的 useEffect Hook 运行，包括任何 Promise 任务。

为了清晰起见，这个函数的必要性不是因为 useEffect Hook 本身——一个普通的同步 act 调用就能确保它运行——而是因为 useEffect 运行的 Promise：

1.  在 `test/reactTestExtensions.js` 中，在 `render` 定义下方添加以下函数：

    ```js
    export const renderAndWait = (component) =>
      act(async () => (
        ReactDOM.createRoot(container).render(component)
      )
    );
    ```

1.  更新测试套件中的导入，使其引用这个新函数：

    ```js
    import {
      initializeReactContainer,
      renderAndWait,
      element,
    } from "./reactTestExtensions";
    ```

1.  然后，更新第一个测试：

    ```js
    it("renders an AppointmentsDayView", async () => {
      await renderAndWait(<AppointmentsDayViewLoader />);
      expect(
        element("#AppointmentsDayView")
      ).not.toBeNull();
    });
    ```

1.  添加第二个测试，检查在服务器返回任何数据之前，我们是否向 `AppointmentsDayView` 发送了一个空的预约数组：

    ```js
    it("initially passes empty array of appointments to AppointmentsDayView", async () => {
      await renderAndWait(<AppointmentsDayViewLoader />);
      expect(AppointmentsDayView).toBeCalledWith(
        { appointments: [] },
        expect.anything()
      );
    });
    ```

在继续之前，请确保这些测试通过。

## 添加 useEffect Hook

我们即将引入一个带有对 `global.fetch` 调用的 useEffect Hook。我们首先将使用 `jest.spyOn` 模拟这个调用。然后，我们继续进行测试：

1.  在测试套件的顶部添加以下新的导入：

    ```js
    import { todayAt } from "./builders/time";
    import { fetchResponseOk } from "./builders/fetch";
    ```

1.  在 `describe` 块的顶部定义一组示例预约：

    ```js
    describe("AppointmentsDayViewLoader", () => {
      const appointments = [
        { startsAt: todayAt(9) },
        { startsAt: todayAt(10) },
      ];
      ...
    });
    ```

1.  要设置 `global.fetch` 以返回此示例数组，修改测试套件的 `beforeEach` 块，如下所示：

    ```js
    beforeEach(() => {
      initializeReactContainer();
      jest
        .spyOn(global, "fetch")
        .mockResolvedValue(fetchResponseOk(appointments));
    });
    ```

1.  是时候进行我们的测试了。我们断言当组件挂载时，我们应该看到带有正确参数的 `global.fetch` 调用。我们的测试计算了正确的参数值——应该是从今晚午夜到明天午夜：

    ```js
    it("fetches data when component is mounted", async () => {
      const from = todayAt(0);
      const to = todayAt(23, 59, 59, 999);
      await renderAndWait(
        <AppointmentsDayViewLoader today={today} />
      );
      expect(global.fetch).toBeCalledWith(
        `/appointments/${from}-${to}`,
        {
          method: "GET",
          credentials: "same-origin",
          headers: { "Content-Type": "application/json" },
        }
      );
    });
    ```

1.  要使这个测试通过，首先，我们需要在组件文件中引入一个 useEffect Hook：

    ```js
    import React, { useEffect } from "react";
    ```

1.  现在，我们可以更新组件以进行调用，如下所示。尽管代码已经很多了，但请注意我们还没有使用 `return` 返回值：没有存储任何状态，`AppointmentsDayView` 仍然将 `appointments` 属性设置为空数组。我们稍后会填充它：

    ```js
    export const AppointmentsDayViewLoader = (
      { today }
    ) => {
      useEffect(() => {
        const from = today.setHours(0, 0, 0, 0);
        const to = today.setHours(23, 59, 59, 999);
        const fetchAppointments = async () => {
          await global.fetch(
            `/appointments/${from}-${to}`,
            {
              method: "GET",
              credentials: "same-origin",
              headers: {
    "Content-Type": "application/json" 
              },
            }
          );
        };
        fetchAppointments();
      }, []);
      return <AppointmentsDayView appointments={[]} />;
    };
    ```

1.  在继续下一个测试之前，让我们为 `today` 属性设置一个默认值，这样任何调用者都不需要指定这个值：

    ```js
    AppointmentsDayViewLoader.defaultProps = {
      today: new Date(),
    };
    ```

1.  下一个测试将确保我们使用`global.fetch`调用的返回值。注意我们如何使用`toHaveBeenLastCalledWith`匹配器。这很重要，因为组件的第一次渲染将是一个空数组。包含数据的是第二次调用：

    ```js
    it("passes fetched appointments to AppointmentsDayView once they have loaded", async () => {
      await renderAndWait(<AppointmentsDayViewLoader />);

      expect(
        AppointmentsDayView
      ).toHaveBeenLastCalledWith(
        { appointments },
        expect.anything()
      );
    });
    ```

1.  要使其通过，首先，更新你的组件的`import`以引入`useState`函数：

    ```js
    import React, { useEffect, useState } from "react";
    ```

1.  现在，更新你的组件定义，如下所示：

    ```js
    export const AppointmentsDayViewLoader = (
      { today }
    ) => {
      const [
        appointments, setAppointments
      ] = useState([]);
      useEffect(() => {
        ...
        const fetchAppointments = async () => {
    const result = await global.fetch(
            ...
          );
          setAppointments(await result.json());
        };
        fetchAppointments();
      }, []);
      return (
        <AppointmentsDayView
          appointments={appointments}
        />
      );
    };
    ```

这完成了基本的`useEffect`实现——我们的组件现在正在加载数据。然而，我们必须用`useEffect`实现来解决最后一个问题。

## 测试`useEffect`依赖列表

`useEffect`调用的第二个参数是一个依赖列表，它定义了应该导致效果重新评估的变量。在我们的例子中，`today`属性是重要的。如果组件以新的`today`值重新渲染，那么我们应该从服务器拉取新的预约。

我们将编写一个渲染组件两次的测试。这种测试在任何使用`useEffect`钩子的时候都非常重要。为了支持这一点，我们需要调整我们的渲染函数，确保它们只创建一个根：

1.  在`test/reactTestExtensions.js`中，添加一个名为`reactRoot`的新顶级变量，并更新`initializeReactContainer`以设置此变量：

    ```js
    export let container;
    let reactRoot;
    export const initializeReactContainer = () => {
       container = document.createElement("div");
       document.body.replaceChildren(container);
      reactRoot = ReactDOM.createRoot(container);
    };
    ```

1.  现在，更新`render`和`renderAndWait`的定义，使它们使用这个`reactRoot`变量。在做出这个更改后，你将能够在单个测试中重新挂载组件：

    ```js
    export const render = (component) =>
      act(() => reactRoot.render(component));
    export const renderAndWait = (component) =>
      act(async () => reactRoot.render(component));
    ```

1.  在你的测试套件中，更新`import`以包含`today`、`tomorrow`和`tomorrowAt`。我们将在下一个测试中使用这些：

    ```js
    import {
      today,
      todayAt,
      tomorrow,
      tomorrowAt
    } from "./builders/time";
    ```

1.  现在，添加测试。这会渲染组件两次，`today`属性有两个不同的值。然后，它检查`global.fetch`是否被调用两次：

    ```js
    it("re-requests appointment when today prop changes", async () => {
      const from = tomorrowAt(0);
      const to = tomorrowAt(23, 59, 59, 999);
      await renderAndWait(
        <AppointmentsDayViewLoader today={today} />
      );
      await renderAndWait(
        <AppointmentsDayViewLoader today={tomorrow} />
      );
      expect(global.fetch).toHaveBeenLastCalledWith(
        `/appointments/${from}-${to}`,
        expect.anything()
      );
    });
    ```

1.  如果你现在运行测试，你会看到`global.fetch`只被调用了一次：

    ```js
        AppointmentsDayViewLoader ' re-requests appointment when today prop changes
        expect(
          jest.fn()
        ).toHaveBeenLastCalledWith(...expected)
        Expected: "/appointments/1643932800000-1644019199999", Anything
        Received: "/appointments/1643846400000-1643932799999", {"credentials": "same-origin", "headers": {"Content-Type": "application/json"}, "method": "GET"}
    ```

1.  使其通过只需一个单词的更改。找到`useEffect`调用的第二个参数，并将其从空数组更改为如下所示：

    ```js
    useEffect(() => {
      ...
    }, [today]);
    ```

这就是本组件实现的全部内容。在下一节中，我们将使用一个新的匹配器清理我们的测试代码。

# 为组件模拟构建匹配器

在本节中，我们将介绍一个新的匹配器`toBeRenderedWithProps`，它简化了我们对模拟间谍对象的期望。

回想一下，我们的期望看起来是这样的：

```js
expect(AppointmentsDayView).toBeCalledWith(
  { appointments },
  expect.anything()
);
```

想象一下，如果你在一个有这种测试的团队中工作。新加入的人能理解第二个参数`expect.anything()`在做什么吗？如果你一段时间不回来，忘记了组件模拟的工作方式，你会理解这个吗？

让我们将它包装成一个匹配器，允许我们隐藏第二个属性。

我们需要两个匹配器来覆盖常见的用例。第一个，`toBeRenderedWithProps`，是我们将在本章中解决的问题。第二个，`toBeFirstRenderedWithProps`，留作你的练习。

匹配器 `toBeRenderedWithProps` 将在组件当前使用给定属性渲染时通过。这个函数将与使用 `toHaveBeenLastCalledWith` 匹配器等效。

这个匹配器的关键部分是当它从 `mock.calls` 数组中提取最后一个元素时：

```js
const mockedCall =
  mockedComponent.mock.calls[
    mockedComponent.mock.calls.length – 1
  ];
```

mock.calls 数组

回想一下，每个使用 `jest.spyOn` 或 `jest.fn` 创建的模拟函数都将有一个 `mock.calls` 属性，它是一个包含所有调用的数组。这一点在 *第六章* 中有介绍，*探索测试替身*。

第二个匹配器是 `toBeFirstRenderedWithProps`。这对于任何检查子属性初始值并且在任何 `useEffect` 钩子运行之前的测试都很有用。我们不会选择 `mock.calls` 数组的最后一个元素，而是直接选择第一个：

```js
const mockedCall = mockedComponent.mock.calls[0];
```

让我们从 `toBeRenderedWithProps` 开始：

1.  在 `test/matchers/toBeRenderedWithProps.test.js` 创建一个新的匹配器测试文件。添加以下导入：

    ```js
    import React from "react";
    import {
      toBeRenderedWithProps,
    } from "./toBeRenderedWithProps";
    import {
      initializeReactContainer,
      render,
    } from "../reactTestExtensions";
    ```

1.  添加以下测试设置。由于我们的测试将在一个间谍函数上操作，我们可以在 `beforeEach` 块中设置它，如下所示：

    ```js
    describe("toBeRenderedWithProps", () => {
      let Component;
      beforeEach(() => {
        initializeReactContainer();
        Component = jest.fn(() => <div />);
      });
    });
    ```

1.  如同往常，我们的第一个测试是检查 `pass` 返回 `true`。注意我们必须在调用匹配器之前渲染组件：

    ```js
    it("returns pass is true when mock has been rendered", () => {
      render(<Component />);
      const result = toBeRenderedWithProps(Component, {});
      expect(result.pass).toBe(true);
    });
    ```

1.  为了使这个测试通过，创建一个新的匹配器文件 `test/matchers/toBeRenderedWithProps.js`，并添加以下实现：

    ```js
    export const toBeRenderedWithProps = (
      mockedComponent,
      expectedProps
    ) => ({  pass: true });
    ```

1.  是时候进行三角测量了。对于下一个测试，让我们检查在调用组件之前没有渲染它时，`pass` 是否为 `false`：

    ```js
    it("returns pass is false when the mock has not been rendered", () => {
      const result = toBeRenderedWithProps(Component, {});
      expect(result.pass).toBe(false);
    });
    ```

1.  为了让测试通过，我们只需检查模拟至少被调用了一次：

    ```js
    export const toBeRenderedWithProps = (
      mockedComponent,
      expectedProps
    ) => ({
      pass: mockedComponent.mock.calls.length > 0,
    });
    ```

1.  接下来，我们需要检查如果属性不匹配，`pass` 是否为 `false`。我们无法编写相反的测试——即如果属性匹配，则 `pass` 为 `true`——因为根据我们当前的实现，这个测试已经通过了：

    ```js
    it("returns pass is false when the properties do not match", () => {
      render(<Component a="b" />);
      const result = toBeRenderedWithProps(
        Component, {
          c: "d",
        }
      );
      expect(result.pass).toBe(false);
    });
    ```

1.  对于组件代码，我们将使用 `expect-utils` 包内的 `equals` 函数，这个包已经作为 Jest 的一部分安装。这个函数测试深度相等，同时也允许你使用 `expect` 辅助函数，如 `expect.anything` 和 `expect.objectContaining`：

    ```js
    import { equals } from "@jest/expect-utils";
    export const toBeRenderedWithProps = (
      mockedComponent,
      expectedProps
    ) => {
      const mockedCall = mockedComponent.mock.calls[0];
      const actualProps = mockedCall ?
        mockedCall[0] : null;
      const pass = equals(actualProps, expectedProps);
      return { pass };
    };
    ```

1.  对于我们的最终测试，我们想要一个例子来展示这个匹配器可以在模拟的最后渲染上匹配期望：

    ```js
    it("returns pass is true when the properties of the last render match", () => {
      render(<Component a="b" />);
      render(<Component c="d" />);
      const result = toBeRenderedWithProps(
        Component,
        { c: "d" }
      );
      expect(result.pass).toBe(true);
    });
    ```

1.  为了使这个测试通过，我们需要更新实现，使其选择 `mock.calls` 数组的最后一个元素，而不是第一个：

    ```js
    export const toBeRenderedWithProps = (
      mockedComponent,
      expectedProps
    ) => {
      const mockedCall =
        mockedComponent.mock.calls[
          mockedComponent.mock.calls.length – 1
        ];
      ...
    };
    ```

1.  我们在这里留下我们的实现。完成消息属性的测试留作你的练习，但它们的顺序与 *第三章* 中显示的测试相同，*重构测试套件*。现在，转到 `test/domMatchers.js` 并注册你的新匹配器：

    ```js
    import {
      toBeRenderedWithProps,
    } from "./matchers/toBeRenderedWithProps";
    expect.extend({
      ...,
      toBeRenderedWithProps,
    });
    ```

1.  最后，回到你的测试套件中，更新检查 `appointments` 属性的测试。它应该看起来像这样；现在 `expect.anything` 参数值已经移除，它看起来更简洁了：

    ```js
    it("passes fetched appointments to AppointmentsDayView once they have loaded", async () => {
      await renderAndWait(<AppointmentsDayViewLoader />);
      expect(AppointmentsDayView).toBeRenderedWithProps({
        appointments,
      });
    });
    ```

通过这样，你已经学会了如何为组件模拟构建一个匹配器，这减少了我们最初使用内置的`toBeCalledWith`匹配器时所拥有的冗余。

这个测试套件中的另一个测试需要一个第二个匹配器，`toBeFirstRenderedWithProps`。这个实现的细节留给你作为练习。

在下一节中，我们将探讨组件模拟可以变得更加复杂的各种方式。

# jest.mock 调用的变体

在我们完成本章之前，让我们看看一些你可能最终会使用的`jest.mock`调用的变体。

需要记住的关键点是*尽可能保持你的模拟简单*。如果你开始觉得你的模拟需要变得更加复杂，你应该将其视为一个信号，表明你的组件过载了，并且应该以某种方式拆分。

话虽如此，有些情况下你必须使用基本组件模拟的不同形式。

## 移除间谍函数

首先，你可以通过不使用`jest.fn`来简化你的`jest.mock`调用：

```js
jest.mock("../src/AppointmentsDayView", () => ({
  AppointmentsDayView: () => (
    <div id="AppointmentsDayView" />
  ),
}));
```

使用这种形式，你已经设置了一个存根返回值，但你将无法监视任何属性。如果，例如，你有多个文件正在测试同一个组件，但只有其中一些验证了模拟组件的属性，这有时是有用的。它也可以与第三方组件一起使用。

## 渲染模拟组件的子组件

有时候，你可能想要渲染孙组件，跳过子组件（它们的父组件）。这种情况经常发生，例如，当第三方组件渲染一个复杂且难以测试的 UI 时：例如，它可能通过阴影 DOM 加载元素。在这种情况下，你可以通过你的模拟传递`children`：

```js
jest.mock("../src/AppointmentsDayView", () => ({
  AppointmentsDayView: jest.fn(({ children }) => (
    <div id="AppointmentsDayView">{children}</div>
  )),
}));
```

我们将在*第十一章*中看到这个例子，*测试驱动 React Router*。

## 检查渲染组件的多个实例

有时候，你可能想要模拟一个被多次渲染到文档中的组件。你如何区分它们？如果它们有一个唯一的 ID 属性（如`key`），你可以在`id`字段中使用它：

```js
jest.mock("../src/AppointmentsDayView", () => ({
  AppointmentsDayView: jest.fn(({ key }) => (
    <div id={`AppointmentsDayView${key}`} />
  )),
}));
```

小心行事！

模拟组件的最大问题之一是模拟定义可能会失控。但是模拟设置很复杂，可能会非常令人困惑。因此，你应该避免编写除了最简单的模拟之外的内容。

幸运的是，大多数时候，组件模拟的普通形式就足够了。这些变体偶尔是有用的，但应该避免使用。

我们将在*第十一章*中看到这个变体的实际应用，*测试驱动 React Router*。

## 模块模拟的替代方案

模拟整个模块相当直接。你设置的模拟必须用于同一测试模块中的所有测试：你不能混合使用测试，一些使用模拟，一些不使用。如果你想要使用`jest.mock`来做这件事，你必须创建两个测试套件。一个会有模拟，另一个则不会。

您还遇到了模拟处于模块级别的问题。您不能只是模拟模块的一部分。Jest 有允许您引用原始实现的函数，称为`requireActual`。对我来说，这涉及到进入过于复杂的测试替身的风险区域，所以我避免使用它——我遇到了一个需要它的用例。

然而，使用`jest.mock`有替代方案。一个是浅渲染，它使用一个特殊的渲染器，只渲染单个父组件，忽略所有非标准 HTML 元素的子组件。从某种意义上说，这甚至更加直接，因为*所有*您的组件最终都会被模拟。

对于`CommonJS`模块，您也可以通过简单地给它们赋新值来覆盖模块内的特定导出！这为您在测试级别设置模拟提供了一个更细粒度的方法。然而，这在**ECMAScript**中不受支持，因此为了最大程度地发挥能力，您可能希望避免这种方法。

为了了解这些替代方法的示例以及何时可能需要使用它们，请参阅[`reacttdd.com/alternatives-to-module-mocks`](https://reacttdd.com/alternatives-to-module-mocks)。

# 摘要

本章介绍了最复杂的模拟形式：使用`jest.mock`设置组件模拟。

由于模拟是一项复杂的艺术，因此最好坚持使用一组已建立的模式，我在本章中展示了这些模式。您还可以参考*第十一章*，*测试驱动 React Router*中的代码，以了解本章中描述的一些变体。

您还学习了如何在编写另一个匹配器之前测试驱动`useEffect`钩子。

现在，您应该对使用组件模拟测试子组件感到自信，包括通过`useEffect`动作将这些组件加载数据。

在下一章中，我们将通过从模拟组件中提取`callback`属性并在测试中调用它们来进一步扩展这项技术。

# 练习

以下是一些供您尝试的练习：

1.  完成`toBeRenderedWithProps`匹配器上的消息属性测试。

1.  添加`toBeFirstRenderedWithProps`匹配器，并更新您的测试套件以使用此匹配器。由于此匹配器与`toBeRenderedWithProps`非常相似，您可以将它添加到包含`toBeRenderedWithProps`匹配器的同一模块文件中。您还可以尝试将任何共享代码提取到其自己的函数中，这两个匹配器都可以使用。

1.  添加一个`toBeRendered`匹配器，该匹配器检查一个组件是否已渲染，而不检查其属性。

1.  完成您编写的匹配器，以便在传递的参数不是 Jest 模拟时抛出异常。

1.  创建一个新的组件，`AppointmentFormLoader`，当组件挂载时调用`GET /availableTimeSlots`端点。它应该渲染一个`AppointmentForm`组件，并将其`appointments`属性设置为从服务器返回的数据。

# 进一步阅读

要了解如何在不依赖 `jest.mock` 的情况下模拟组件，请查看[`reacttdd.com/alternatives-to-module-mocks`](https://reacttdd.com/alternatives-to-module-mocks)。
