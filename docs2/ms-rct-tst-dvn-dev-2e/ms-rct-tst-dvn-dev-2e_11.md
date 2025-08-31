# 11

# 测试驱动 React Router

**React Router** 是一个流行的组件库，它与浏览器的自身导航系统集成。它操作浏览器的地址栏，使得你的 UI 变化看起来像是页面转换。对于用户来说，他们似乎在导航到不同的页面。实际上，他们仍然停留在同一个页面上，避免了昂贵的页面重新加载。

在本章中，我们将重构我们的示例预约系统以使用 React Router。与本书的其余部分不同，本章不是一个逐步指南。这是因为重构过程相当长且费力。相反，我们将依次查看每个主要更改。

本章涵盖了以下内容：

+   从测试优先的角度设计 React Router 应用程序

+   在路由器内测试组件

+   测试路由链接

+   测试编程式导航

到本章结束时，你将学会所有测试驱动 React Router 集成所必需的技术。

# 技术要求

本章的代码文件可以在以下位置找到：

[`github.com/PacktPublishing/Mastering-React-Test-Driven-Development-Second-Edition/tree/main/Chapter11`](https://github.com/PacktPublishing/Mastering-React-Test-Driven-Development-Second-Edition/tree/main/Chapter11)

# 从测试优先的角度设计 React Router 应用程序

本节是对 React Router 生态系统所有主要部分的概述，以防你不熟悉它。它还包含了如何测试依赖于 React Router 的系统的指导。

## 所有 React Router 组件的列表

你将使用 React Router 库中的以下内容：

+   一个 `路由器` 组件。你通常会有一个这样的组件，并且有很多不同的类型。基本的是 `BrowserRouter`，但如果你需要在外部路由器中操作历史记录，你无疑会升级到 `HistoryRouter`，因为你正在编写测试。在 *第十二章* *测试驱动 Redux* 中，你也会看到这是在 Redux 动作中引起页面转换所必需的。

+   一个 `路由` 组件。这类似于我们现有 `App` 组件中的 `switch` 语句。它有一个 `路由` 子组件列表，并且一次只会选择其中一个子组件来显示。

+   一组带有 `路由` 父组件的 `路由` 组件。每个 `路由` 都有一个路径属性，例如 `/addCustomer`，`路由器` 组件会使用它来与窗口的当前位置进行比较。匹配的路由就是显示的那个。

+   一个或多个 `链接` 组件。这些显示得像正常的 HTML 超链接，但它们的行为不同；React Router 会阻止浏览器接收这些导航事件，并将它们发送回 `路由` 组件，这意味着不会发生页面转换。

+   `useNavigate` 钩子。这用于在 React 侧效应或事件处理程序中执行页面转换。

+   `useLocation`和`useSearchParams`钩子。这些用于在组件中获取当前窗口位置的某些部分。

## 当窗口位置改变时拆分测试

您可以从这个列表中看到，React Router 的核心功能是操作窗口位置并根据该位置修改您应用程序的行为。

关于这一点的一种思考方式是，我们将利用窗口位置作为应用程序状态的一种形式，这种状态对所有我们的组件都是可访问的。重要的是，这种状态在 Web 请求之间持续存在，因为用户可以保存或收藏链接以供以后使用。

这的结果是我们现在必须将一些单元测试分开。以之前用于切换页面上显示的主要组件的**创建预约**按钮为例。在 React Router 到位后，这个按钮将变成一个链接。之前，我们有一个名为以下内容的单个单元测试：

```js
displays the AppointmentFormLoader after the CustomerForm is submitted
```

但现在，我们将它拆分为两个测试：

```js
navigates to /addAppointment after the CustomerForm is submitted
renders AppointmentFormRoute at /addAppointment
```

您可以看到，第一个测试在窗口位置改变时停止。第二个测试在浏览器导航到相同位置时开始。

进行这项更改很重要，因为 React Router 不仅仅是重构，它还在添加一个新功能：URL 现在可以作为您应用程序的入口点。

也就是说，在本质上，这是在将 React Router 引入您的项目之前您需要知道的最重要的事情。

## 为我们新的路由进行前期设计

在开始重构之前，让我们看看我们将要引入的路由：

+   默认路由`/`将保持为我们的`AppointmentsDayViewLoader`，以及导航按钮。这被提取出来作为一个名为`MainScreen`的新组件。

+   一个用于添加新客户的路由，位于`/addCustomer`。

+   一个用于为特定客户添加新预约的路由，位于`/addAppointment?customer=<id>`。

+   一个用于在`/searchCustomers`搜索客户的路由。它可以接收一组查询字符串值：`searchTerm`、`limit`和`previousRowIds`。例如，查询字符串可能如下所示：

    ```js
    ?searchTerm=An&limit=20&previousRowIds=123,456
    ```

接下来，我们将查看如何测试`Router`组件及其`Route`子组件。

# 在路由器内测试组件

在本节中，我们将探讨如何使用主要的`Router`、`Routes`和`Route`组件。

本章没有演练内容

如章节介绍中所述，本章不遵循通常的演练方法。这里显示的示例是从我们 Appointments 代码库的完成重构中提取的，您可以在 GitHub 仓库的`Chapter11/Complete`目录中找到。

## 路由器组件及其测试等效组件

这是一个顶级组件，它连接到您浏览器的位置机制。我们通常不进行测试驱动，因为 JSDOM 不处理页面转换，也没有对`window.location` API 的完全支持。

相反，我们将其放在`src/index.js`文件中：

```js
import React from "react";
import ReactDOM from "react-dom/client";
import { BrowserRouter } from "react-router-dom";
import { App } from "./App";
ReactDOM.createRoot(
  document.getElementById("root")
).render(
  <BrowserRouter>
    <App />
  </BrowserRouter>
);
```

这是因为如果你尝试在`Router`组件的子组件之外使用任何其他 React Router 组件，它将会崩溃。对于我们的测试也是如此：我们的组件需要在路由器内部渲染。因此，我们引入了一个新的渲染辅助函数，称为`renderWithRouter`。

这个定义在`test/reactTestExtensions.js`中：

```js
import { createMemoryHistory } from "history";
import {
  unstable_HistoryRouter as HistoryRouter
} from "react-router-dom";
export let history;
export const renderWithRouter = (
  component,
  { location } = { location: "" }
) => {
  history = createMemoryHistory({
    initialEntries: [location]
  });
  act(() =>
    reactRoot.render(
      <HistoryRouter history={history}>
        {component}
      </HistoryRouter>
    )
  );
};
```

MemoryRouter 与 HistoryRouter

React Router 文档建议你使用`MemoryRouter`，这通常足够好。使用`HistoryRouter`允许你控制传入的历史实例，这意味着你可以在测试中操作它。

更多信息，请参阅[`reacttdd.com/memory-router-vs-history-router`](https://reacttdd.com/unpublished/memory-router-vs-history-router/)。

如果你想在测试中操作窗口位置，那么导出`history`变量本身是很重要的。这种情况的一个特例是，如果你想在挂载组件之前设置窗口位置；在这种情况下，你可以简单地将一个`location`属性传递给`renderWithRouter`函数。你将在下一节中看到它是如何工作的。

## 使用`Routes`组件替换 switch 语句

现在，让我们看看如何使用`Routes`组件根据窗口位置切换组件。这个组件通常位于应用程序组件层次结构的顶部，在我们的例子中，它确实是`App`中的第一个组件。

`Routes`组件与原始应用程序中存在的`switch`语句类似。`switch`语句使用状态变量来确定应该显示哪个组件。`Routes`组件依赖于父`Router`来提供窗口位置作为上下文。

这是`App`组件中原始的`switch`语句的样子：

```js
const [view, setView] = useState("dayView");
...
switch (view) {
  case "addCustomer":
    return (
      <CustomerForm ... />
    );
  case "searchCustomers":
    return (
      <CustomerSearch ... />
    );
  case "addAppointment":
    return (
      <AppointmentFormLoader ... />
    );
  default:
    return ...
}
```

它的`Router`替代品看起来像这样：

```js
<Routes>
  <Route
    path="/addCustomer"
    element={<CustomerForm ... />}
  />
  <Route
    path="/addAppointment"
    element={<AppointmentFormRoute ... />}
  />
  <Route
    path="/searchCustomers"
    element={<CustomerSearchRoute ... />}
  />
  <Route path="/" element={<MainScreen />} />
</Routes>
```

`view`状态变量不再需要。注意我们有几个带有`Route`后缀的新组件。这些组件是小的包装器，在将它们传递给原始组件之前，从窗口位置中提取客户 ID 和其他参数。我们很快就会看到它们。

但首先，这些新路由的测试看起来是怎样的？

对于默认路由，测试很简单，是对之前存在的测试的更新：

```js
it("initially shows the AppointmentDayViewLoader", () => {
  renderWithRouter(<App />);
  expect(AppointmentsDayViewLoader).toBeRendered();
});
it("has a menu bar", () => {
  renderWithRouter(<App />);
  expect(element("menu")).not.toBeNull();
});
```

唯一的区别是我们使用`renderWithRouter`辅助函数，而不是`render`。

其他路由类似，只是它们使用`location`属性设置初始窗口位置，并且它们的断言基于模拟组件：

```js
it("renders CustomerForm at the /addCustomer endpoint", () => {
  renderWithRouter(<App />, {
    location: "/addCustomer"
  });
  expect(CustomerForm).toBeRendered();
});
it("renders AppointmentFormRoute at /addAppointment", () => {
  renderWithRouter(<App />, {
    location: "/addAppointment?customer=123",
  });
  expect(AppointmentFormRoute).toBeRendered();
});
it("renders CustomerSearchRoute at /searchCustomers", () => {
  renderWithRouter(<App />, {
    location: "/searchCustomers"
  });
  expect(CustomerSearchRoute).toBeRendered();
});
```

## 使用中间组件来转换 URL 状态

让我们更仔细地看看`AppointmentFormRoute`和`CustomerSearchRoute`。这些组件在做什么？

这是`AppointmentFormRoute`的定义：

```js
import React from "react";
import { useSearchParams } from "react-router-dom";
import {
  AppointmentFormLoader
} from "./AppointmentFormLoader";
const blankAppointment = {
  service: "",
  stylist: "",
  startsAt: null,
};
export const AppointmentFormRoute = (props) => {
  const [params, _] = useSearchParams();
  return (
    <AppointmentFormLoader
      {...props}
      original={{
        ...blankAppointment,
        customer: params.get("customer"),
      }}
    />
  );
};
```

这个组件是一个中间组件，位于`/addAppointment`的`Route`组件实例和`AppointmentFormLoader`组件实例之间。

本可以直接在`AppointmentFormLoader`内部引用`useSearchParams`函数，但通过使用这个中间类，我们可以避免修改该组件，并保持两个职责的分离。

每个组件只负责一个任务有助于理解。这也意味着，如果我们以后希望移除 React Router，`AppointmentFormLoader`就不需要被修改。

对于这个组件有一些有趣的测试。第一个是检查解析`customer`搜索参数：

```js
it("adds the customer id into the original appointment object", () => {
  renderWithRouter(<AppointmentFormRoute />, {
    location: "?customer=123",
  });
  expect(AppointmentFormLoader).toBeRenderedWithProps({
    original: expect.objectContaining({
      customer: "123",
    }),
  });
});
```

发送到`renderWithRouter`的`location`属性只是一个标准的查询字符串：`?customer=123`。我们本可以在这里输入一个完整的 URL，但通过仅关注 URL 的查询字符串部分，测试会更清晰。

第二个测试是对剩余的 props：

```js
it("passes all other props through to AppointmentForm", () => {
  const props = { a: "123", b: "456" };
  renderWithRouter(<AppointmentFormRoute {...props} />);
  expect(AppointmentFormLoader).toBeRenderedWithProps(
    expect.objectContaining({
      a: "123",
      b: "456",
    })
  );
});
```

这个测试很重要，因为`Route`元素传递了一个`onSave`属性，它是为`AppointmentFormLoader`准备的：

```js
<Route
  path="/addAppointment"
  element={
    <AppointmentFormRoute onSave={transitionToDayView} />
  }
/>
```

我们将在稍后的*测试导航*部分看看`transitionToDayView`函数做了什么。

现在让我们看看`CustomerSearchRoute`。这稍微复杂一些，因为它使用名为`convertParams`的函数解析了一些查询字符串参数：

```js
const convertParams = () => {
  const [params] = useSearchParams();
  const obj = {};
  if (params.has("searchTerm")) {
    obj.searchTerm = params.get("searchTerm");
  }
  if (params.has("limit")) {
    obj.limit = parseInt(params.get("limit"), 10);
  }
  if (params.has("lastRowIds")) {
    obj.lastRowIds = params
      .get("lastRowIds")
      .split(",")
      .filter((id) => id !== "");
  }
  return obj;
};
```

这个函数替换了现有`CustomerSearch`组件中使用的三个状态变量。由于所有查询字符串参数都是字符串，每个值都需要解析成正确的格式。然后这些值作为 props 传递给`CustomerSearch`：

```js
import React from "react";
import {
  useNavigate,
  useSearchParams,
} from "react-router-dom";
import {
  CustomerSearch
} from "./CustomerSearch/CustomerSearch";
const convertParams = ...; // as above
export const CustomerSearchRoute = (props) => (
  <CustomerSearch
    {...props}
    navigate={useNavigate()}
    {...convertParams()}
  />
);
```

这个参数解析功能原本可以放入`CustomerSearch`中，但将这个逻辑放在一个单独的组件中有助于提高可读性。

这个例子还展示了`useNavigate`的使用，它被传递给`CustomerSearch`。将这个钩子函数的返回值作为 prop 传递意味着我们可以使用标准的 Jest spy 函数测试`navigate`的值，从而避免在路由中渲染测试组件。

这个组件的测试很简单。让我们看看一个例子：

```js
it("parses lastRowIds from query string", () => {
  const location =
    "?lastRowIds=" + encodeURIComponent("1,2,3");
  renderWithRouter(<CustomerSearchRoute />, { location });
  expect(CustomerSearch).toBeRenderedWithProps(
    expect.objectContaining({
      lastRowIds: ["1", "2", "3"],
    })
  );
});
```

你现在已经了解了如何与三个组件一起工作：`Router`、`Routes`和`Route`。接下来是`Link`组件。

# 测试路由链接

在本节中，你将学习如何使用和测试`Link`组件。这个组件是 React Router 对谦逊的 HTML 锚点（或`a`）标签的版本。

我们使用了两种形式的`Link`组件。第一种使用`to`属性作为字符串，例如，`/addCustomer`：

```js
<Link to="/addCustomer" role="button">
  Add customer and appointment
</Link>
```

第二个设置`to`属性为一个具有`search`属性的`object`：

```js
<Link
    to={{
      search: objectToQueryString(queryParams),
    }}
>
  {children}
</Link>
```

这个对象形式也接受一个`pathname`属性，但我们可以避免设置它，因为对于我们的用例，路径保持不变。

我们将探讨两种不同的测试链接的方法：标准方法（通过检查超链接），以及稍微痛苦一些的模拟方法。

## 检查页面中的超链接

这是`src/App.js`中的`MainScreen`组件，它显示了导航链接和预约日视图：

```js
export const MainScreen = () => (
  <>
    <menu>
      <li>
        <Link to="/addCustomer" role="button">
          Add customer and appointment
        </Link>
      </li>
      <li>
        <Link to="/searchCustomers" role="button">
          Search customers
        </Link>
      </li>
    </menu>
    <AppointmentsDayViewLoader />
  </>
);
```

提取组件

`MainScreen`组件已被从`App`中提取出来。相同的代码之前位于`switch`语句的默认情况下。

`Link`组件生成一个标准的 HTML 锚标签。这意味着我们创建了一个辅助工具，通过查找具有匹配`href`属性的锚标签来找到特定的链接。这位于`test/reactTestExtensions.js`中：

```js
export const linkFor = (href) =>
  elements("a").find(
    (el) => el.getAttribute("href") === href
  );
```

然后，你可以用来测试链接的存在及其标题：

```js
it("renders a link to the /addCustomer route", async () => {
  renderWithRouter(<App />);
  expect(linkFor("/addCustomer")).toBeDefined();
});
it("captions the /addCustomer link as 'Add customer and appointment'", async () => {
  renderWithRouter(<App />);
  expect(linkFor("/addCustomer")).toContainText(
    "Add customer and appointment"
  );
});
```

测试这个问题的另一种方法是通过点击链接并检查其是否正常工作，如下面的测试所示。然而，正如本章开头提到的，这个测试是不必要的，因为你已经测试了测试的两个“部分”：链接是否显示，以及导航到 URL 是否渲染了正确的组件：

```js
it("displays the CustomerSearch when link is clicked", async () => {
  renderWithRouter(<App />);
  click(linkFor("/searchCustomers"));
  expect(CustomerSearchRoute).toBeRendered();
});
```

这涵盖了测试`Link`组件的主要方法。另一种测试链接的方法是模拟`Link`组件，我们将在下一节中介绍。

## 模拟`Link`组件

这种方法比简单地测试 HTML 超链接要复杂一些。然而，这意味着你可以避免在`Router`组件内渲染你的测试组件。

`src/CustomerSearch/RouterButton.js`文件包含这个组件：

```js
import React from "react";
import {
  objectToQueryString
} from "../objectToQueryString";
import { Link } from "react-router-dom";
export const RouterButton = ({
  queryParams,
  children,
  disabled,
}) => (
  <Link
    className={disabled ? "disabled" : ""}
    role="button"
    to={{
      search: objectToQueryString(queryParams),
    }}
  >
    {children}
  </Link>
);
```

要使用普通的`render`测试而不是`renderWithRouter`，我们需要模拟`Link`组件。以下是`test/CustomerSearch/RouterButton.test.js`中的样子：

```js
import { Link } from "react-router-dom";
import {
  RouterButton
} from "../../src/CustomerSearch/RouterButton";
jest.mock("react-router-dom", () => ({
  Link: jest.fn(({ children }) => (
    <div id="Link">{children}</div>
  )),
}));
```

现在，你可以在测试中愉快地使用这个模拟：

```js
it("renders a Link", () => {
  render(<RouterButton queryParams={queryParams} />);
  expect(Link).toBeRenderedWithProps({
    className: "",
    role: "button",
    to: {
      search: "?a=123&b=234",
    },
  });
});
```

有一个最后的要点需要考虑。有时候，你有一个单个的模拟组件在同一页面上有多个渲染实例，这种情况在`Link`实例中经常发生。

在我们的案例中，这是`SearchButtons`组件，它包含一个`RouterButton`和`ToggleRouterButton`组件的列表：

```js
<menu>
  ...
  <li>
    <RouterButton
      id="previous-page"
      queryParams={previousPageParams()}
      disabled={!hasPrevious}
    >
      Previous
    </RouterButton>
  </li>
  <li>
    <RouterButton
      id="next-page"
      queryParams={nextPageParams()}
      disabled={!hasNext}
    >
      Next
    </RouterButton>
  </li>
</menu>
```

当涉及到测试这些链接时，最简单的方法是使用`renderWithRouter`来渲染`SearchButtons`组件，然后检查渲染的 HTML 超链接。

然而，如果你已经决定模拟，那么你需要一种方法来轻松地找到你渲染的元素。

首先，你需要指定模拟包括一个`id`属性：

```js
jest.mock("../../src/CustomerSearch/RouterButton", () => ({
  RouterButton: jest.fn(({ id, children }) => (
    <div id={id}>{children}</div>
  )),
}));
```

然后，你可以使用一个新的测试扩展`propsMatching`来找到特定的实例。以下是来自`test/reactTestExtensions.js`的定义：

```js
export const propsMatching = (mockComponent, matching) => {
  const [k, v] = Object.entries(matching)[0];
  const call = mockComponent.mock.calls.find(
    ([props]) => props[k] === v
  );
  return call?.[0];
};
```

然后，你可以编写测试来利用这一点，如下面的代码所示。但请记住，可能更容易不模拟这个组件，而直接使用`renderWithRouter`，然后直接检查 HTML 超链接：

```js
const previousPageButtonProps = () =>
  propsMatching(RouterButton, { id: "previous-page" });
it("renders", () => {
  render(<SearchButtons {...testProps} />);
  expect(previousPageButtonProps()).toMatchObject({
    disabled: false,
  });
  expect(element("#previous-page")).toContainText(
    "Previous"
  );
});
```

这就是测试`Link`组件的所有内容。在下一节中，我们将探讨测试 React Router 的最后一个方面：程序化导航。

# 测试程序化导航

有时候，你可能想要程序化地触发位置变化——换句话说，不等待用户点击链接。

有两种方法可以做到这一点：一种使用`useNavigate`钩子，另一种使用传递给顶级路由器的`history`实例。

组件内外的导航

在本章中，我们将仅探讨第一种方法，即使用钩子。稍后，在*第十二章*“测试驱动 Redux”中，我们将使用第二种方法来更改 Redux 动作中的位置。

当你能够在 React 组件内部进行导航时，`useNavigate`钩子是适当的方法。

在预约应用程序中，这发生在两个地方。第一个是在客户被添加后，我们希望将用户移动到`/addAppointment`路由。第二个是在表单填写完成并且预约被创建后——然后我们希望将他们移回默认路由。

由于这些非常相似，我们只需查看第一个。

这是`/addCustomer`路由定义在`src/App.js`中的样子：

```js
<Route
  path="/addCustomer"
  element={
    <CustomerForm
      original={blankCustomer}
      onSave={transitionToAddAppointment}
    />
  }
/>
```

注意到`onSave`属性；这是在客户表单提交完成后被调用的回调。以下是该回调定义，以及与`useNavigate`钩子相关的部分：

```js
import {
  ...,
  useNavigate,
} from "react-router-dom";
export const App = () => {
  const navigate = useNavigate();
  const transitionToAddAppointment = (customer) =>
    navigate(`/addAppointment?customer=${customer.id}`);
  ...
};
```

当涉及到测试这一点时，显然，我们不能仅仅依赖于`Link`组件的存在，因为并没有。相反，我们必须调用`onSave`回调：

```js
import {
  ...,
  history,
} from "./reactTestExtensions";
...
it("navigates to /addAppointment after the CustomerForm is submitted", () => {
  renderWithRouter(<App />);
  click(linkFor("/addCustomer"));
  const onSave = propsOf(CustomerForm).onSave;
  act(() => onSave(customer));
  expect(history.location.pathname).toEqual(
    "/addAppointment"
  );
});
```

预期是要测试历史记录是否正确更新。这个历史记录是来自`test/reactTestExtensions.js`的导出常量，它在我们在*在路由器中测试组件*部分定义的`renderWithRouter`函数中被设置。

这有一个变体。你不仅可以使用`history`导入，还可以简单地使用`window.location`实例：

```js
expect(
  window.location.pathname
).toEqual("/addAppointment");
```

你现在已经学会了如何测试程序化的 React Router 导航。

在下一章“测试驱动 Redux”中，我们将看到如何使用这个相同的历史实例从 Redux sagas 中。

# 摘要

本章向您展示了如何以可测试的方式使用 React Router。你已经学会了如何测试驱动`Router`、`Routes`、`Route`和`Link`组件。你已经看到了如何使用 React Router 的`useSearchParams`和`useNavigate`钩子。

最重要的是，你已经看到，由于路由为你的应用程序提供了额外的入口级别，你必须将现有的导航测试分成两部分：一部分测试链接是否存在（或被跟随），另一部分检查如果你访问该 URL，是否显示正确的组件。

现在我们已经成功集成了一个库，下一个库应该不会太棘手，对吧？在下一章中，我们将把本章学到的所有技能应用到另一个库的集成中：Redux。

# 练习

在本章中，没有进行详细说明，因为重构过程相当复杂，会占用相当的时间和空间。

利用这个机会尝试自我重构。使用*系统化重构*方法将 React Router 的更改分解成许多小步骤。在每一步中，你仍然应该有可工作的软件。

你可以在[`reacttdd.com/refactoring-to-react-router`](https://reacttdd.com/unpublished/refactoring-to-react-router/)找到如何处理此类重构的指南。

# 进一步阅读

官方 React Router 文档可以在以下链接找到：

[`reacttraining.com/react-router/`](https://reacttraining.com/react-router/)
