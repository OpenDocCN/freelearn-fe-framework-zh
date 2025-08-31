

# 第六章：探索测试替身

在本章中，我们将查看 TDD 谜题中最复杂的一部分：测试替身。

Jest 提供了一系列方便的测试替身函数，例如`jest.spyOn`和`jest.fn`。不幸的是，正确使用测试替身有点像一门黑暗的艺术。如果您不知道自己在做什么，可能会得到复杂、脆弱的测试。也许这就是为什么 Jest 没有将其作为其框架的一级特性来推广。

不要被吓倒：测试替身是一种高度有效且多功能的工具。诀窍是限制您的使用范围在一个小型、定义良好的模式集中，您将在接下来的几章中了解到这些模式。

在本章中，我们将构建自己的手工艺品测试替身函数集。它们几乎与 Jest 函数一样工作，但具有更简单（且更笨拙）的接口。目标是去除这些函数的魔力，向您展示它们是如何构建的以及如何使用它们来简化您的测试。

在您迄今为止构建的测试套件中，一些测试没有使用正常的`expect.hasAssertions`。在一个真实的代码库中，我会始终避免使用此函数，而是使用测试替身，这有助于将测试重新排序为 AAA 顺序。我们将从这里开始：重构现有测试以使用我们手工制作的测试替身，然后将其替换为 Jest 自己的测试替身函数。

本章将涵盖以下主题：

+   什么是测试替身？

+   使用间谍提交表单

+   监控 Fetch API

+   模拟`fetch`响应

+   迁移到 Jest 内置的测试替身支持

到本章结束时，您将学会如何有效地使用 Jest 的测试替身功能。

# 技术要求

本章的代码文件可以在此处找到：

[`github.com/PacktPublishing/Mastering-React-Test-Driven-Development-Second-Edition/tree/main/Chapter06`](https://github.com/PacktPublishing/Mastering-React-Test-Driven-Development-Second-Edition/tree/main/Chapter06)

本章及以后的代码示例包含额外的提交，这些提交为应用程序添加了一个可工作的后端。这允许您发出请求以获取数据，您将在本章开始这样做。

在配套代码存储库中，从`Chapter06/Start`开始，`npm run build`命令将自动构建服务器。

然后，您可以通过使用`npm run serve`命令并浏览到`http://localhost:3000`或`http://127.0.0.1:3000`来启动应用程序。

如果您遇到问题

如果您无法运行应用程序，请查看存储库`README.md`文件的*故障排除*部分。

# 什么是测试替身？

单元测试中的**单元**指的是在测试期间我们关注的单个函数或组件。测试的**行为**阶段应该只涉及一个单元的一个动作。但单元不会孤立行动：函数调用其他函数，组件渲染子组件并调用从父组件传入的回调属性。可以将您的应用程序视为一个依赖关系的网络，测试替身帮助我们设计和测试这些依赖关系。

当我们编写测试时，我们会**隔离**被测试的单元。通常这意味着我们避免对任何协作对象进行操作。为什么？首先，这有助于我们朝着独立、专注的测试目标前进。其次，有时那些协作对象会有副作用，这会复杂化我们的测试。

以一个例子来说明，在 React 组件中，我们有时想避免渲染子组件，因为它们在挂载时会执行网络请求。

一个`onSubmit`函数，它被传递给`CustomerForm`和`AppointmentForm`两个组件。我们可以在测试中用测试替身替换它。正如我们将看到的，这有助于我们定义两者之间的关系。

在我们的系统中，测试替身最重要的使用地方是在与页面内容之外的任何外部事物交互的边缘：**超文本传输协议**（**HTTP**）请求、文件系统访问、套接字、本地存储等等。

测试替身被分为几种不同的类型：间谍、存根、模拟、哑元和伪造。我们通常只使用前两种，这也是本章我们将要集中讨论的内容。

## 学习避免伪造

**伪造**是指任何包含任何逻辑或控制结构的测试替身，例如条件语句或循环。其他类型的测试对象，如间谍和存根，完全由变量赋值和函数调用组成。

您会看到的一种伪造类型是内存中的存储库。您可以使用它来替代**结构化查询语言**（**SQL**）数据存储、消息代理和其他复杂的数据源。

伪造在测试两个单元之间的复杂协作时很有用。我们通常会先使用间谍和存根，然后在代码开始变得难以控制时重构为伪造。一个伪造可以覆盖一组测试，这比维护大量间谍和存根要简单。

我们避免使用伪造的原因如下：

+   任何逻辑都需要测试，这意味着我们必须为伪造编写测试，即使它们是测试代码的一部分。间谍和存根不需要测试。

+   通常，间谍和存根可以替代伪造。当我们使用伪造时，只有一小部分测试会更简单。

+   伪造增加了测试的脆弱性，因为它们在测试之间是共享的，而其他测试替身则不是。

既然我们已经涵盖了测试替身的理论，让我们继续在代码中使用它们。

# 使用间谍提交表单

在本节中，您将手动创建一个可重用的间谍函数，并调整您的测试以使它们回到 AAA 顺序。

这里是一个提醒，说明了`CustomerForm`测试套件中的一个测试看起来是怎样的。由于它被测试生成器包裹，所以有点复杂，但你现在可以忽略这一点——重要的是测试内容：

```js
const itSubmitsExistingValue = (fieldName, value) =>
  it("saves existing value when submitted", () => {
    expect.hasAssertions();
    const customer = { [fieldName]: value };
    render(
      <CustomerForm
        original={customer}
        onSubmit={(props) =>
          expect(props[fieldName]).toEqual(value)
      }
    />
  );
  click(submitButton());
});
```

这段代码有几个问题，如下所述：

+   测试的**断言**阶段——期望——似乎被包裹在**行为**阶段中。这使得测试难以阅读和理解。

+   调用`expect.hasAssertions`很丑陋，它只在那里是因为我们的期望被作为`onSubmit`函数的一部分调用，而这个函数可能被调用也可能不被调用。

我们可以通过构建一个间谍来解决这两个问题。

什么是间谍？

**间谍**是一种测试双重角色，它记录了被调用的参数，以便稍后可以检查这些值。

## 解开 AAA

将期望放在传递给`onSubmit`函数的`firstName`值下面。然后我们针对存储的值编写期望。

让我们现在这样做，如下所示：

1.  修改`test/CustomerForm.test.js`中的`saves existing value when submitted`测试生成器函数，如下所示：

    ```js
    const itSubmitsExistingValue = (fieldName, value) =>
      it("saves existing value when submitted", () => {
        let submitArg;
        const customer = { [fieldName]: value };
        render(
          <CustomerForm
            original={customer}
            onSubmit={submittedCustomer => (
              submitArg = submittedCustomer
            )}
          />
        );
        click(submitButton());
        expect(submitArg).toEqual(customer);
      });
    ```

`submitArg`变量在`onSubmit`处理程序中分配，然后在测试的最后一条语句中断言。这解决了第一个测试中的两个问题：我们的测试回到了 AAA 顺序，并且我们摆脱了丑陋的`expect.hasAssertions()`调用。

1.  如果你现在运行测试，它们应该都是绿色的。然而，每次你以这种方式重构测试时，你应该通过展开生产代码并观察测试失败来验证你仍在测试正确的事情。为了检查我们的测试是否仍然有效，定位`src/CustomerForm.js`中的这一行：

    ```js
    <form id="customer" onSubmit={handleSubmit}>
    ```

完全移除`onSubmit`属性，如下所示：

```js
<form id="customer">
```

1.  运行`npm test`。你会从不同的测试中得到多个测试失败。然而，我们只对这一个测试生成器感兴趣，所以将其声明更新为`it.only`而不是`it`，如下所示：

    ```js
    it.only("saves existing value when submitted", () => {
    ```

1.  现在，你应该只有三个失败，每个字段使用此生成器函数一个，如下面的代码片段所示。这是一个好兆头；如果更少，我们就可能产生了假阳性：

    ```js
    FAIL test/CustomerForm.test.js
      ● CustomerForm › first name field › saves existing value when submitted
        expect(received).toEqual(expected) // deep equality
        Expected: {"firstName": "existingValue"}
        Received: undefined
    ```

1.  我们已经证明了测试是有效的，所以你可以继续将`it.only`声明改回`it`，并重新插入你从`CustomerForm.js`中移除的`onSubmit`属性。

你在这个测试中编写的代码显示了间谍函数的本质：当间谍被调用时，我们设置一个变量，然后基于该变量的值编写期望。

但我们还没有一个真正的间谍函数。我们将在下一步创建它。

## 制作可重用的间谍函数

我们仍然在`CustomerForm`和`AppointmentForm`中都有其他使用`expect.hasAssertions`形式的测试。我们如何将这个测试中构建的内容重用于其他所有内容？我们可以创建一个通用的`spy`函数，这样我们就可以在需要间谍功能时使用它。

让我们首先定义一个可以替代任何单个参数函数的函数，例如我们传递给`onSubmit`表单属性的处理器，如下所示：

1.  在`test/CustomerForm.test.js`的顶部定义以下函数。注意`fn`定义的格式与我们在上一个测试中使用的`onSubmit`处理器相似：

    ```js
    const singleArgumentSpy = () => {
      let receivedArgument;
      return {
        fn: arg => (receivedArgument = arg),
        receivedArgument: () => receivedArgument
      };
    };
    ```

1.  重新编写您的测试生成器以使用此函数。尽管您的测试应该仍然通过，但请记住通过撤销生产代码来观察测试失败。代码如下所示：

    ```js
    const itSubmitsExistingValue = (fieldName, value) =>
      it("saves existing value when submitted", () => {
        const submitSpy = singleArgumentSpy();
        const customer = { [fieldName]: value };
        render(
          <CustomerForm
            original={customer}
            onSubmit={submitSpy.fn}
          />
        );
        click(submitButton());
        expect(submitSpy.receivedArgument()).toEqual(
          customer
        );
      });
    ```

1.  通过将`singleArgumentSpy`替换为以下函数，使你的间谍函数能够适用于任何数量的参数的函数：

    ```js
    const spy = () => {
      let receivedArguments;
      return {
        fn: (...args) => (receivedArguments = args),
     receivedArguments: () => receivedArguments,
        receivedArgument: n => receivedArguments[n]
      };
    };
    ```

这使用参数解构来保存整个参数数组。我们可以使用`receivedArguments`来返回该数组，或者使用`receivedArgument(n)`来检索第*n*个参数。

1.  更新您的测试代码以使用这个新函数，如下面的代码片段所示。您可以在`receivedArguments`上添加一个额外的期望来检查`toBeDefined`。这是一种表示“我期望函数被调用”的方式：

    ```js
    const itSubmitsExistingValue = (fieldName, value) =>
      it("saves existing value when submitted", () => {
        const submitSpy = spy();
        const customer = { [fieldName]: value };
        render(
          <CustomerForm
            original={customer}
            onSubmit={submitSpy.fn}
          />
        );
        click(submitButton());
        expect(
          submitSpy.receivedArguments()
        ).toBeDefined();
        expect(submitSpy.receivedArgument(0)).toEqual(
          customer
        );
      });
    ```

间谍（spy）实际上很简单：它只是用来跟踪何时被调用以及调用时的参数。

## 使用 matcher 简化间谍期望

让我们编写一个 matcher，将这些期望封装成一个单独的语句，如下所示：

```js
expect(submitSpy).toBeCalledWith(value);
```

这比在 matcher 上使用`toBeDefined()`参数更具有描述性。它还封装了如果`receivedArguments`尚未设置，则它尚未被调用的概念。

抛弃代码

我们将**spike**这段代码——换句话说，我们不会编写测试。这是因为不久之后，我们将用 Jest 的内置间谍功能来替换它。由于我们并不打算长时间保留它，所以深入到“真实”实现中是没有意义的。

我们将首先替换第一个期望的功能，如下所示：

1.  在`test/domMatchers.js`的底部添加以下代码。它添加了新的 matcher，为我们的测试做好准备：

    ```js
    expect.extend({
      toBeCalled(received) {
        if (received.receivedArguments() === undefined) {
          return {
            pass: false,
            message: () => "Spy was not called.",
          };
        }
        return {
          pass: true,
          message: () => "Spy was called.",
        };
      },
    });
    ```

1.  更新测试以使用新的 matcher，如下所示，替换使用`toBeDefined`的第一个期望：

    ```js
    const itSubmitsExistingValue = (fieldName, value) =>
      it("saves existing value when submitted", () => {
        const submitSpy = spy();
        const customer = { [fieldName]: value };
        render(
          <CustomerForm
            original={customer}
            onSubmit={submitSpy.fn}
          />
        );
        click(submitButton());
        expect(submitSpy).toBeCalled(customer);
        expect(submitSpy.receivedArgument(0)).toEqual(
          customer
        );
      });
    ```

1.  通过在您的生产代码中注释掉对`onSubmit`的调用并观察测试失败来验证新的 matcher 是否工作。然后，取消注释并尝试在`.not.toBeCalled`测试中的否定形式。

1.  现在，我们可以开始处理第二个期望——检查函数参数的期望。将以下代码添加到您的新 matcher 中，并将名称从`toBeCalled`更改为`toBeCalledWith`：

    ```js
    expect.extend({
      toBeCalledWith(received, ...expectedArguments) {
        if (received.receivedArguments() === undefined) {
          ...
        }
        const notMatch = !this.equals(
          received.receivedArguments(),
          expectedArguments
        );
        if (notMatch) {
          return {
            pass: false,
            message: () =>
              "Spy called with the wrong arguments: " +
              received.receivedArguments() +
              ".",
          };
        }
        return ...;
      },
    });
    ```

在 matcher 中使用 this.equals

`this.equals`方法是一种特殊类型的相等函数，可以在 matcher 中使用。它执行深度相等匹配，这意味着它会递归遍历散列和数组以查找差异。它还允许使用`expect.anything()`、`expect.objectContaining()`和`expect.arrayContaining()`特殊函数。

如果你正在测试驱动此匹配器并将其提取到自己的文件中，你将不会使用 `this.equals`。相反，你会从 `@jest/expect-utils` 包中导入 `equals` 函数。我们将在 *第七章** 测试 useEffect 和组件模拟* 中这样做。

1.  更新你的测试以将这两个期望合并为一个，如下所示：

    ```js
    const itSubmitsExistingValue = (fieldName, value) =>
      it("saves existing value when submitted", () => {
        ...
        click(submitButton());
        expect(submitSpy).toBeCalledWith(customer);
      });
    ```

1.  通过在 `CustomerForm` 测试套件中更改 `onSubmit` 调用来发送明显错误的数据（例如，`onSubmit(1, 2, 3)`）来使它失败。然后，也尝试匹配器的否定形式。

这完成了我们的监视器实现，你已经看到了如何测试回调属性。接下来，我们将探讨如何监视更复杂的函数：`global.fetch`。

# 监视 fetch API

在本节中，我们将使用 `Fetch` API 将客户数据发送到我们的后端服务。我们已经有了一个在表单提交时被调用的 `onSubmit` 属性。我们将在这个过程中将 `onSubmit` 调用转换为 `global.fetch` 调用，并调整我们的现有测试。

在我们更新的组件中，当通过 `fetch` 函数将 `POST` HTTP 请求发送到 `/customers` 端点时，请求体将是我们客户的 **JavaScript 对象表示法**（**JSON**）对象。

包含在 GitHub 仓库中的服务器实现将返回一个包含额外字段的更新后的 `customer` 对象：客户 `id` 值。

如果 `fetch` 请求成功，我们将调用一个新的 `onSave` 回调属性，并带有 `fetch` 响应。如果请求不成功，则不会调用 `onSave`，我们将渲染一个错误消息。

你可以将 `fetch` 视为 `onSubmit` 的更高级形式：两者都是我们将用客户对象调用的函数。但是 `fetch` 需要一组特殊的参数来定义正在进行的 HTTP 请求。它还返回一个 `Promise` 对象，因此我们需要考虑这一点，并且请求体需要是一个字符串，而不是一个普通对象，因此我们需要确保在我们的组件和测试套件中将其转换。

最后的一个区别是：`fetch` 是一个全局函数，可以通过 `global.fetch` 访问。我们不需要将其作为属性传递。为了监视它，我们用我们的监视器替换了原始函数。

理解 Fetch API

以下代码示例显示了 `fetch` 函数期望如何被调用。如果你不熟悉这个函数，请参阅本章末尾的 *进一步阅读* 部分。

考虑到所有这些，我们可以规划我们的前进路线：我们首先将全局函数替换为我们自己的监视器，然后添加新的测试以确保我们正确调用它，最后我们将更新 `onSubmit` 测试以调整其现有行为。

## 替换全局函数为监视器

我们已经看到了如何通过简单地将监视器作为回调属性的值来监视回调属性。要监视全局函数，我们只需在测试运行之前覆盖其值，并在之后将其重置回原始函数。

由于`global.fetch`是组件的必需依赖——没有它将无法工作——因此，在测试套件的`beforeEach`块中设置一个默认的 spy 是有意义的，这样 spy 就可以在所有测试中预先设置。`beforeEach`块也是设置 stubs 默认返回值的好地方，我们将在本章稍后进行操作。

按照以下步骤为你的测试套件在`global.fetch`上设置默认 spy：

1.  在`test/CustomerForm.test.js`的外部`describe`块的顶部添加以下声明：

    ```js
    describe("CustomerForm", () => {
      const originalFetch = global.fetch;
      let fetchSpy;
      ...
    })
    ```

`originalFetch`常量将在测试完成后恢复 spy 时使用。`fetchSpy`变量将用于存储我们的`fetch`对象，这样我们就可以针对它编写期望。

1.  将`beforeEach`块修改如下。这将为你的测试套件中的每个测试设置`global.fetch`作为 spy：

    ```js
    beforeEach(() => {
      initializeReactContainer();
      fetchSpy = spy();
      global.fetch = fetchSpy.fn;
    });
    ```

1.  在`beforeEach`块下方，添加一个`afterEach`块来取消 mock，如下所示：

    ```js
    afterEach(() => {
      global.fetch = originalFetch;
    });
    ```

使用原始值重置全局 spies

重置任何用 spies 替换的全局变量是很重要的。这是测试相互依赖的常见原因：由于一个“脏”的 spy，一个测试可能会因为另一个测试未能重置其 spies 而失败。

在这个特定情况下，Node.js 运行时环境实际上没有`global.fetch`函数，所以`originalFetch`常量最终会是`undefined`。然后，你可以争论说这是不必要的：在我们的`afterEach`块中，我们只需简单地从`global`中删除`fetch`属性即可。

在本章的后面，我们将修改设置全局 spies 的方法，当我们使用 Jest 内置的 spy 函数时。

在全局 spy 就位后，你就可以在测试中使用它了。

## 测试驱动 fetch 参数值

是时候将`global.fetch`添加到我们的组件中了。当`global.fetch`使用正确的参数被调用时。类似于我们测试`onSubmit`的方式，我们将这个测试拆分为针对每个字段的测试，指定每个字段都必须传递。

结果表明`global.fetch`需要传递一大堆参数。我们不会在一个单独的单元测试中测试它们，而是根据它们的含义将测试拆分。

我们首先检查请求的基本情况：这是一个对`/customers`端点的`POST`请求。按照以下步骤操作：

1.  在你的`CustomerForm`测试套件的底部添加以下新测试。注意`onSubmit`被赋予了一个空函数定义—`() => {}`—而不是 spy，因为我们对这个测试中的属性不感兴趣：

    ```js
    it("sends request to POST /customers when submitting the form", () => {
      render(
        <CustomerForm
          original={blankCustomer}
          onSubmit={() => {}}
        />
      );
      click(submitButton());
      expect(fetchSpy).toBeCalledWith(
        "/customers",
        expect.objectContaining({
          method: "POST",
        })
      );
    });
    ```

1.  使用`npm test`运行测试，并验证你是否收到一个期望失败的消息，显示为以下代码片段：

    ```js
      ● CustomerForm › sends request to POST /customers when submitting the form
        Spy was not called.
          163 |     );
          164 |     click(submitButton());
        > 165 |     expect(fetchSpy).toBeCalledWith(
              |                      ^
          166 |       "/customers",
          167 |       expect.objectContaining({
          168 |         method: "POST",
    ```

1.  为了使其通过，通过在`onSubmit`调用之前添加对`global.fetch`的调用，修改`CustomerForm`的`handleSubmit`函数，如下所示代码片段：

    ```js
    const handleSubmit = (event) => {
      event.preventDefault();
      global.fetch("/customers", {
        method: "POST",
      });
      onSubmit(customer);
    };
    ```

并行实现

这是一个并行实现。我们保留“旧”实现——即对 `onSubmit` 的调用——以便其他测试继续通过。

1.  在这个测试通过后，添加下一个测试。在这个测试中，我们测试了请求所需的全部管道，我们将其称为“配置”，但您可以将这视为将所有常量、不太相关的信息批量处理。这个测试还使用了两个新函数，`expect.anything` 和 `expect.objectContaining`，如下面的代码片段所示：

    ```js
    it("calls fetch with the right configuration", () => {
      render(
        <CustomerForm
          original={blankCustomer}
          onSubmit={() => {}}
        />
      );
      click(submitButton());
      expect(fetchSpy).toBeCalledWith(
        expect.anything(),
        expect.objectContaining({
          credentials: "same-origin",
          headers: {
            "Content-Type": "application/json",
          },
        })
      );
    });
    ```

使用 `expect.anything` 和 `expect.objectContaining` 测试属性子集

`expect.anything` 函数是一种很有用的说法：“在这个测试中，我不关心这个参数；我在别处已经测试过了。”这是保持测试相互独立的好方法。在这种情况下，我们之前的测试检查第一个参数是否设置为 `/customers`，因此我们不需要在这个测试中再次测试这一点。

`expect.objectContaining` 函数与 `expect.arrayContaining` 函数类似，它允许我们测试完整参数值的一个子集。

1.  运行该测试并观察测试失败。您可以在以下代码片段中看到，我们的匹配器在打印消息方面做得并不出色：第二个实际参数被打印为 `[object Object]`。现在我们先忽略这个问题，因为在本章的后面部分，我们将转向使用 Jest 的内置匹配器：

    ```js
      ● CustomerForm › calls fetch with the right configuration when submitting the form
        Spy was called with the wrong arguments: /customers,[object Object].
    ```

1.  要使测试通过，只需将剩余的属性插入到您的 `global.fetch` 调用中：

    ```js
    const handleSubmit = (event) => {
      event.preventDefault();
      global.fetch("/customers", {
        method: "POST",
        credentials: "same-origin",
        headers: { "Content-Type": "application/json" },
      });
      onSubmit(customer);
    };
    ```

这为我们的 `global.fetch` 调用设置了管道，每个常量参数都已定义并放在适当的位置。接下来，我们将添加动态参数：请求体。

## 使用并行实现重写现有测试

您已经通过使用新测试开始构建并行实现。现在，是时候重写现有测试了。我们将移除旧实现（在这个例子中是 `onSubmit`）并用新实现（`global.fetch`）替换它。

完成这一步后，所有测试都将指向 `global.fetch`，因此我们可以更新我们的实现，从 `handleSubmit` 函数中移除 `onSubmit` 调用。

我们有两个测试需要更新：一个是检查提交现有值的测试，另一个是检查提交新值的测试。由于它们被封装在测试生成函数中，这使得测试变得复杂。这意味着当我们修改它们时，我们应该预期所有生成的测试——每个字段一个——作为一个组都会失败。这不是理想的情况，但即使只是一个普通的测试，我们遵循的过程也会是相同的。

让我们从本章中已经练习过的测试开始，提交现有值。请按照以下步骤操作：

1.  返回到 `itSubmitsExistingValue` 测试生成函数，并在底部插入一个新的期望值。暂时保留现有的期望值。运行测试并确保生成的测试失败。代码如下所示：

    ```js
    const itSubmitsExistingValue = (fieldName, value) =>
      it("saves existing value when submitted", () => {
        const customer = { [fieldName]: value };
        const submitSpy = spy();
        render(
          <CustomerForm
            original={customer}
            onSubmit={submitSpy.fn}
          />
        );
        click(submitButton());
        expect(submitSpy).toBeCalledWith(customer);
        expect(fetchSpy).toBeCalledWith(
          expect.anything(),
          expect.objectContaining({
            body: JSON.stringify(customer),
          })
        );
      });
    ```

1.  为了使它通过，更新你的`CustomerForm`组件中的`handleSubmit`函数，如下所示代码片段。在此更改之后，你的测试应该会通过：

    ```js
    const handleSubmit = (event) => {
      event.preventDefault();
      global.fetch("/customers", {
        method: "POST",
        credentials: "same-origin",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(original),
      });
      onSubmit(customer);
    };
    ```

1.  对`onSubmit`属性的最终测试引用是`itSubmitsNewValue`测试生成器。此测试仍然使用旧的`expect.hasAssertions`样式；我们稍后会删除它。现在，只需在测试底部添加一个新的期望，如下所示：

    ```js
    const itSubmitsNewValue = (fieldName, value) =>
      it("saves new value when submitted", () => {
        ...
        expect(fetchSpy).toBeCalledWith(
          expect.anything(),
          expect.objectContaining({
            body: JSON.stringify({
              ...blankCustomer,
              [fieldName]: value,
            }),
          })
        );
      });
    ```

1.  运行测试并验证此测试失败，失败信息为`Spy was called with the wrong arguments: /customers,[object Object]`。

1.  为了使它通过，你需要在`handleSubmit`函数中将`original`更改为`customer`，如下所示：

    ```js
    const handleSubmit = (event) => {
      event.preventDefault();
      global.fetch("/customers", {
        method: "POST",
        credentials: "same-origin",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(customer),
      });
      onSubmit(customer);
    };
    ```

1.  你对`fetch`的调用现在已完成，因此你可以删除原始实现。首先从`itSubmitsExistingValue`测试生成器中移除`onSubmit`属性和`submitSpy`变量。新版本如下所示：

    ```js
    const itSubmitsExistingValue = (fieldName, value) =>
      it("saves existing value when submitted", () => {
        const customer = { [fieldName]: value };
        render(<CustomerForm original={customer} />);
        click(submitButton());
        expect(fetchSpy).toBeCalledWith(
          expect.anything(),
          expect.objectContaining({
            body: JSON.stringify(customer),
          })
        );
      });
    ```

1.  对于`itSubmitsNewValue`也做同样的操作——你还可以删除`hasAssertions`调用。新版本如下所示：

    ```js
    const itSubmitsNewValue = (fieldName, value) =>
      it("saves new value when submitted", () => {
        render(<CustomerForm original={blankCustomer} />);
        change(field(fieldName), value);
        click(submitButton());
        expect(fetchSpy).toBeCalledWith(
          expect.anything(),
          expect.objectContaining({
            body: JSON.stringify({
              ...blankCustomer,
              [fieldName]: value,
            }),
          })
        );
      });
    ```

1.  从`handleSubmit`方法中移除对`onSubmit`的调用。

1.  从`CustomerForm`组件定义中移除`onSubmit`属性。

1.  最后，从`prevents the default action...`测试中移除`onSubmit`属性。

1.  使用`npm test`验证所有测试是否通过。

你现在已经看到了如何通过重构测试来继续并行实现。一旦所有测试都已完成重构，你可以删除原始实现。

我们的测试又变得相当冗长。让我们通过一点清理来完成这一部分。

## 使用辅助函数改进间谍期望

当我们为我们的间谍编写期望时，我们不仅限于使用`toBeCalledWith`匹配器。我们可以提取参数并给它们命名，然后使用标准的 Jest 匹配器来处理它们。这样，我们可以避免使用`expect.anything`和`expect.objectContaining`的所有仪式。

让我们现在就做。按照以下步骤进行：

1.  在`CustomerForm`顶部添加一个新的辅助函数`bodyOfLastFetchRequest`，如下所示：

    ```js
    const bodyOfLastFetchRequest = () =>
      JSON.parse(fetchSpy.receivedArgument(1).body);
    ```

1.  更新你的`itSubmitsExistingValue`测试生成器，使用这个新助手来简化其期望。注意这里使用了`toMatchObject`，它取代了之前版本此测试中的`expect.objectContaining`：

    ```js
    const itSubmitsExistingValue = (fieldName, value) =>
      it("saves existing value when submitted", () => {
        const customer = { [fieldName]: value };
        render(<CustomerForm original={customer} />);
        click(submitButton());
        expect(bodyOfLastFetchRequest()).toMatchObject(
          customer
        );
      });
    ```

1.  由于你已修改了测试，你应该验证它仍然测试正确的事情：将其标记为`it.only`，然后从`global.fetch`调用中删除`body`属性。检查测试失败，然后撤销更改，使测试重新通过。

1.  如此重复`itSubmitsNewValue`测试生成器的操作，如下所示：

    ```js
    const itSubmitsNewValue = (fieldName, value) =>
      it("saves new value when submitted", () => {
        render(<CustomerForm original={blankCustomer} />);
        change(field(fieldName), value);
        click(submitButton());
        expect(bodyOfLastFetchRequest()).toMatchObject({
          [fieldName]: value,
        });
      });
    ```

这些测试现在看起来非常聪明！

这是一次复杂的变更：我们用对`global.fetch`的调用替换了`onSubmit`属性。我们通过在`beforeEach`块中引入一个全局间谍并在重构测试的同时编写并行实现来完成这项工作。

在本章的下一部分，我们将扩展我们对间谍的了解，将它们变成存根。

# 模拟获取响应

就像许多 HTTP 请求一样，我们的 `POST /customers` 端点返回数据：它将返回客户对象以及后端为我们选择的新生成的标识符。我们的应用程序将通过获取新 ID 并将其发送回父组件来使用它（尽管我们不会在 *第八章*，*构建应用程序组件*）之前构建这个父组件）。

要做到这一点，我们将创建一个新的 `CustomerForm` 属性，`onSave`，它将使用 `fetch` 调用的结果被调用。

但是等等——我们不是刚刚移除了一个 `onSubmit` 属性吗？是的，但这不是同一回事。原始的 `onSubmit` 属性接收用户提交的表单值。这个 `onSave` 属性将接收在成功保存后从服务器返回的客户对象。

要为这个新的 `onSave` 属性编写测试，我们需要为 `global.fetch` 提供一个模拟值，这本质上意味着，“这是调用 `POST /customers` 端点时 `global.fetch` 的返回值。”

什么是模拟？

**模拟**是一个测试双胞胎，当它被调用时总是返回相同的值。你在构造模拟时决定这个值是什么。

在本节中，我们将升级我们手工制作的间谍函数，使其也能模拟函数返回值。然后，我们将使用它来测试 `CustomerForm` 中新 `onSave` 属性的添加。最后，我们将使用它来在服务器由于某种原因未能保存新的客户对象时向用户显示错误。

## 升级间谍为模拟

模拟与间谍不同，因为它对跟踪被模拟函数的调用历史不感兴趣——它只关心返回单个值。

然而，结果证明，我们现有的使用间谍的测试也需要模拟值。这是因为一旦我们在生产代码中使用返回值，间谍必须返回一些东西；否则，测试会失败。所以，所有间谍最终也变成了模拟。

由于我们已经有了一个 `spy` 函数，我们可以“升级”它，使其也有模拟值的能力。以下是我们可以这样做的方法：

1.  在 `test/CustomerForm.test.js` 中，将 `spy` 函数更改为在顶部包含以下新的变量声明。这个变量将存储值，以便我们的函数返回：

    ```js
    let returnValue;
    ```

1.  将 `fn` 定义更改为以下所示：

    ```js
    fn: (...args) => {
      receivedArguments = args;
      return returnValue;
    },
    ```

1.  将这个新函数添加到你的间谍对象中，该函数设置 `returnValue` 变量：

    ```js
    stubReturnValue: value => returnValue = value
    ```

就这么简单：你的函数现在既是间谍也是模拟。让我们在我们的测试中使用它。

## 对获取响应采取行动

到目前为止，`handleSubmit` 函数会导致发起一个 `fetch` 请求，但它对响应没有任何操作。特别是，它不会*等待*响应；`fetch` API 是异步的，并返回一个承诺。一旦这个承诺解决，我们就可以对返回的数据做些事情。

我们将要编写的下一个测试将指定我们的组件应该对解决的数据做什么。

### `act`的异步形式

当我们在 React 回调中处理承诺时，我们需要使用`act`的异步形式。它看起来是这样的：

```js
await act(async () => performAsyncAction());
```

`performAsyncAction`函数不一定需要返回一个承诺；`act`将在返回之前等待浏览器`async`任务队列完成。

动作可能是一个按钮点击、表单提交或任何类型的输入字段事件。它也可能是具有执行某些异步副作用（如加载数据）的`useEffect`钩子的组件渲染。

### 向现有组件添加异步任务

现在，我们将使用`act`的异步形式来测试`fetch`承诺是否被等待。不幸的是，将`async`/`await`引入我们的`handleSubmit`函数将需要我们更新所有提交测试以使用`act`的异步形式。

如同往常，我们从测试开始。按照以下步骤进行：

1.  在`test/CustomerForm.test.js`中定义一个测试辅助函数，该函数构建一个`Response`对象类型，以模拟`fetch` API 返回的内容。这意味着它返回一个具有`ok`属性值为`true`的`Promise`对象，以及一个`json`函数，该函数返回另一个`Promise`，当解决时返回我们传递的 JSON。你可以在你的`spy`函数下面定义这个，如下所示：

    ```js
    const fetchResponseOk = (body) =>
      Promise.resolve({
        ok: true,
        json: () => Promise.resolve(body)
      });
    ```

`fetch`返回值

`ok`属性在 HTTP 响应状态码在`2xx`范围内时返回`true`。任何其他类型的响应，如`404`或`500`，都会导致`ok`为`false`。

1.  在`test/reactTestExtensions.js`中添加以下代码，位于`click`定义下方：

    ```js
    export const clickAndWait = async (element) =>
      act(async () => click(element));
    ```

1.  现在，将新的辅助函数导入到`test/CustomerForm.test.js`中，如下所示：

    ```js
    import {
      ...,
      clickAndWait,
    } from "./reactTestExtensions";
    ```

1.  将下一个测试添加到`CustomerForm`测试套件中，该测试检查当用户提交表单时是否调用`onSave`属性函数，并返回客户对象。这个测试的最佳位置是在`calls fetch with correct configuration`测试之下。以下代码片段展示了代码示例：

    ```js
    it("notifies onSave when form is submitted", async () => {
      const customer = { id: 123 };
      fetchSpy.stubReturnValue(fetchResponseOk(customer));
      const saveSpy = spy();
      render(
        <CustomerForm
          original={customer}
          onSave={saveSpy.fn}
        />
      );
      await clickAndWait(submitButton());
      expect(saveSpy).toBeCalledWith(customer);
    });
    ```

1.  要使这个测试通过，首先在`src/CustomerForm.js`中为`CustomerForm`定义一个新的`onSave`属性，如下所示：

    ```js
    export const CustomerForm = ({
      original, onSave
    }) => {
      ...
    };
    ```

1.  在`handleSubmit`的末尾添加以下代码。现在，该函数被声明为`async`，并使用`await`来展开`global.fetch`返回的承诺：

    ```js
    const handleSubmit = async (event) => {
      event.preventDefault();
      const result = await global.fetch(...);
      const customerWithId = await result.json();
      onSave(customerWithId);
    };
    ```

1.  如果你运行测试，你会注意到尽管你的最新测试通过了，但之前的测试失败了，并且有一大堆未处理的承诺异常。实际上，任何提交表单的操作都会失败，因为它们使用了在`beforeEach`块中初始化的`fetchSpy`变量，而这不是一个存根——它只是一个普通的间谍。现在通过在`beforeEach`中给间谍一个返回值来修复这个问题。在这种情况下，我们不需要给它一个客户；一个空对象就足够了，以下代码片段展示了这一点：

    ```js
    beforeEach(() => {
      ...
      fetchSpy.stubReturnValue(fetchResponseOk({}));
    });
    ```

`beforeEach`块中的占位值

当模拟全局函数，如`global.fetch`时，始终在`beforeEach`块中设置一个默认的虚拟值，然后在需要特定模拟值的单个测试中覆盖它。

1.  再次运行测试。此时你可能会看到一些奇怪的行为；我看到我的最近测试据说运行了六次，并且失败了！发生的事情是，我们之前的测试现在正在触发一大堆承诺，即使在测试结束时这些异步任务仍在继续运行。这些异步任务导致 Jest 错误地报告失败。为了解决这个问题，我们需要更新所有测试以使用`await clickAndWait`。此外，测试需要标记为`async`。现在为每个调用`click`的测试执行此操作。这里有一个示例：

    ```js
    it("sends HTTP request to POST /customers when submitting data", async () => {
      render(<CustomerForm original={blankCustomer} />);
      await clickAndWait(submitButton());
      ...
    });
    ```

1.  删除`click`导入，留下`clickAndWait`。

1.  还有一个测试存在这个问题，那就是提交表单的测试：`在提交表单时阻止默认操作`。这个测试调用了我们的`submit`辅助函数。我们也需要在`act`中包裹它。让我们在我们的测试扩展文件中创建一个`submitAndWait`辅助函数。将以下函数添加到`submit`下面，在`test/reactTestExtensions.js`中：

    ```js
    export const submitAndWait = async (formElement) =>
      act(async () => submit(formElement));
    ```

1.  在你的`import`语句中添加`submitAndWait`，在`clickAndWait`下面，如下所示：

    ```js
    import {
      ...,
      submitAndWait,
    } from "./reactTestExtensions";
    ```

1.  现在，你可以更新测试以使用新的辅助函数，如下所示：

    ```js
    it("prevents the default action when submitting the form", async () => {
      render(<CustomerForm original={blankCustomer} />);
      const event = await submitAndWait(form());
      expect(event.defaultPrevented).toBe(true);
    });
    ```

1.  如果你再次运行测试，我们仍然有测试失败（尽管幸运的是，`async`任务被正确地考虑在内，事情看起来更有序）。你会看到现在有一大堆失败，说`onSave 不是一个函数`。为了解决这个问题，我们需要确保为每个提交表单的测试指定`onSave`属性。一个空的无操作函数就可以。这里有一个示例。现在将这个属性添加到每个提交表单的测试中。在这个更改之后，你的测试应该会通过，没有任何警告：

    ```js
    it("calls fetch with correct configuration", async () => {
      render(
        <CustomerForm
          original={blankCustomer}
          onSave={() => {}}
        />
      );
      ...
    });
    ```

在需要时引入 testProps 对象

引入这个`onSave`无操作函数会导致噪音，这并不利于我们测试的可读性。这是一个引入`testProps`对象的绝佳机会，正如在*第五章*中所述，*添加复杂表单交互*。

1.  添加另一个测试以确保在`fetch`响应有错误状态（换句话说，当`ok`属性设置为`false`）时，我们不调用`onSave`。首先定义另一个辅助函数`fetchResponseError`，在`fetchResponseOk`下面，如下代码片段所示。这个不需要体，因为我们目前对它不感兴趣：

    ```js
    const fetchResponseError = () =>
      Promise.resolve({ ok: false });
    ```

1.  在下一个`CustomerForm`测试中使用这个新函数，如下所示：

    ```js
    it("does not notify onSave if the POST request returns an error", async () => {
      fetchSpy.stubReturnValue(fetchResponseError());
      const saveSpy = spy();
      render(
        <CustomerForm
          original={blankCustomer}
          onSave={saveSpy.fn}
        />
      );
      await clickAndWait(submitButton());
      expect(saveSpy).not.toBeCalledWith();
    });
    ```

取反 toBeCalledWith

这个期望并不是我们真正想要的：如果我们仍然调用`onSave`但传递了错误的参数——例如，如果我们写了`onSave(null)`，这个期望就会通过。我们真正想要的是`.not.toBeCalled()`，这将导致`onSave`以任何形式被调用时失败。但我们还没有构建这个匹配器。在本章的后面，我们将通过移动到 Jest 的内置 spy 函数来修复这个期望。

1.  要使这通过，将`onSave`调用移动到`handleSubmit`中的新条件中，如下所示：

    ```js
    const handleSubmit = async (event) => {
      ...
      const result = ...;
      if (result.ok) {
        const customerWithId = await result.json();
        onSave(customerWithId);
      }
    };
    ```

正如你所看到的，将组件从同步行为移动到异步行为真的会扰乱我们的测试套件。上面概述的步骤是这种情况所需工作的典型步骤。

异步组件操作可能导致 Jest 测试失败报告不准确

如果你看到测试失败而感到惊讶，并且无法解释为什么它失败了，请仔细检查测试套件中的所有测试，以确保在需要时使用了`act`的异步形式。Jest 在测试以异步任务完成时不会警告你，并且由于你的测试使用的是共享的 DOM 文档，这些异步任务将影响后续测试的结果。

这些就是处理测试中异步行为的基本方法。现在，让我们对我们的实现添加一些细节。

## 向用户显示错误

如果 fetch 返回的`ok`值为`false`，则向用户显示错误。这会在 HTTP 状态码返回在`4xx`或`5xx`范围内时发生，尽管对于我们的测试，我们不需要担心具体的状态码。遵循以下步骤：

1.  将以下测试添加到`test/CustomerForm.test.js`。这个测试检查页面上是否显示了错误区域。它依赖于 ARIA 角色`alert`，这是屏幕阅读器的特殊标识符，表示该区域可能改变以包含重要信息：

    ```js
    it("renders an alert space", async () => {
      render(<CustomerForm original={blankCustomer} />);
      expect(element("[role=alert]")).not.toBeNull();
    });
    ```

1.  要使这通过，首先，定义一个新的`Error`组件，如下所示。这可以放在`src/CustomerForm.js`中，正好在`CustomerForm`组件本身之上：

    ```js
    const Error = () => (
      <p role="alert" />
    );
    ```

1.  然后，在`CustomerForm`的 JSX 中添加该组件的一个实例，正好在`form`元素顶部，如下所示：

    ```js
    <form>
      <Error />
      ...
    </form>
    ```

1.  回到`test/CustomerForm.test.js`，添加下一个测试，该测试检查警告中的错误信息，如下所示：

    ```js
    it("renders error message when fetch call fails", async () => {
      fetchSpy.mockReturnValue(fetchResponseError());
      render(<CustomerForm original={blankCustomer} />);
      await clickAndWait(submitButton());
      expect(element("[role=alert]")).toContainText(
        "error occurred"
      );
    });
    ```

1.  要使这通过，我们只需要在`Error`组件中硬编码字符串。我们将使用另一个测试来三角定位以到达真正的实现，如下所示：

    ```js
    const Error = () => (
      <p role="alert">
        An error occurred during save.
      </p>
    );
    ```

1.  将最后的测试添加到`test/CustomerForm.test.js`中，如下所示：

    ```js
    it("initially hano text in the alert space", async () => {
      render(<CustomerForm original={blankCustomer} />);
      expect(element("[role=alert]")).not.toContainText(
        "error occurred"
      );
    });
    ```

1.  要使这通过，在`CustomerForm`定义的顶部引入一个新的`error`状态变量，如下所示：

    ```js
    const [error, setError] = useState(false);
    ```

1.  修改`handleSubmit`函数，如下所示：

    ```js
    const handleSubmit = async (event) => {
      ...
      if (result.ok) {
        ...
      } else {
        setError(true);
      }
    }
    ```

1.  在组件的 JSX 中，更新`Error`实例以包括新的`hasError`prop 并将其设置为`error`状态，如下所示：

    ```js
    <form>
      <Error hasError={error} />
      ...
    </form>
    ```

1.  剩下的只是用新的 prop 完成`Error`组件，如下所示：

    ```js
    const Error = ({ hasError }) => (
      <p role="alert">
        {hasError ? "An error occurred during save." : ""}
      </p>
    ); 
    ```

我们的`CustomerForm`实现到此结束。现在是时候对我们的测试进行一点清理了。

## 在嵌套的 describe 上下文中对 stub 场景进行分组

一种常见的做法是使用嵌套的`describe`块来设置存根值作为一组测试的场景。我们刚刚编写了四个测试，这些测试处理了`POST /customers`端点返回错误的场景。其中两个是嵌套`describe`上下文的良好候选。

然后，我们可以将存根值拉入一个`beforeEach`块中。让我们从`describe`块开始。按照以下步骤进行：

1.  看看您最后写的四个测试。其中两个是关于警报空间的，与错误情况无关。保留这两个测试，并将另外两个移动到一个新的`describe`块中，命名为`when POST requests return an error`，如下所示：

    ```js
    it("renders an alert space", ...)
    it("initially has no text in the alert space", ...)
    describe("when POST request returns an error", () => {
      it("does not notify onSave if the POST request returns an error", ...)
      it("renders error message when fetch call fails", ...)
    });
    ```

1.  注意，两个测试描述是如何重复的，它们以不同的方式说同样的话，就像`describe`块一样？从两个测试描述中删除`if`/`when`语句，如下所示：

    ```js
    describe("when POST request returns an error", () => {
      it("does not notify onSave", ...)
      it("renders error message ", ...)
    });
    ```

1.  这两个测试具有相同的`global.fetch`存根。将这个存根拉入一个新的`beforeEach`块中，如下所示：

    ```js
    describe("when POST request returns an error", () => {
      beforeEach(() => {
        fetchSpy.stubReturnValue(fetchResponseError());
      });
      ...
    })
    ```

1.  最后，从两个测试中删除存根调用，只留下`beforeEach`块中的存根调用。

您现在已经看到了如何使用嵌套的`describe`块来描述特定的测试场景，这涵盖了所有基本的存根技术。在下一节中，我们将通过介绍 Jest 的自有存根和存根函数来继续我们的清理工作，这些函数比我们自己构建的稍微简单一些。

# 迁移到 Jest 的内置测试双倍支持

到目前为止，在本章中，您已经构建了自己的手工制作的存根函数，支持存根值和具有自己的匹配器。这样做的目的是为了教您如何使用测试双倍，并展示您将在组件测试中使用的基本存根和存根模式。

然而，我们的存根函数和`toBeCalledWith`匹配器还远远不够完善。与其在我们手工制作的版本上投入更多时间，现在切换到 Jest 的自有函数似乎更有意义。这些函数基本上与我们的`spy`函数以相同的方式工作，但有一些细微的差别。

本节首先概述了 Jest 的测试双倍功能。然后，我们将`CustomerForm`测试套件从我们手工制作的存根函数迁移出去。最后，我们将通过提取更多测试辅助工具进行一些清理。

## 使用 Jest 进行存根和存根

下面是 Jest 测试双倍支持的概述：

+   要创建一个新的存根函数，请调用`jest.fn()`。例如，您可能编写`const fetchSpy = jest.fn()`。

+   要覆盖现有属性，请调用`jest.spyOn(object, property)`。例如，您可能编写`jest.spyOn(global, "fetch")`。

+   要设置返回值，请调用`spy.mockReturnValue()`。您也可以直接将此值传递给`jest.fn()`调用。

+   您可以通过链式调用`spy.mockReturnValueOnce()`来设置多个返回值。

+   当您的函数返回承诺时，您可以使用`spy.mockResolvedValue()`和`spy.mockRejectedValue()`。

+   要检查您的存根是否被调用，请使用`expect(spy).toBeCalled()`。

+   要检查传递给您的间谍的参数，您可以使用 `expect(spy).toBeCalledWith(arguments)`。或者，如果您的间谍被多次调用，并且您想检查它最后一次被调用的情况，您可以使用 `expect(spy).toHaveLastBeenCalledWith(arguments)`。

+   调用 `spy.mockReset()` 将从间谍中移除所有模拟实现、返回值和现有的调用历史。

+   调用 `spy.mockRestore()` 将移除模拟并恢复原始实现。

+   在您的 `package.json` 文件的 Jest 配置部分，您可以将 `restoreMocks` 设置为 `true`，这样在每次测试之后，使用 `jest.spyOn` 创建的所有间谍都将自动恢复。

+   当使用 `toBeCalledWith` 时，您可以将 `expect.anything()` 作为参数值传递，表示您不关心该参数的值是什么。

+   您可以使用 `expect.objectMatching(object)` 来检查一个参数是否具有您传递的对象的所有属性，而不是与该对象完全相等。

+   当您的间谍被多次调用时，您可以使用 `spy.mock.calls[n]` 来检查特定调用传递的参数，其中 `n` 是调用编号（例如，`calls[0]` 将返回第一次被调用的参数）。

+   如果您需要对特定参数执行复杂的匹配，您可以使用 `spy.mock.calls[0][n]`，其中 `n` 是参数编号。

+   您可以使用 `jest.mock()` 函数来模拟和间谍化整个模块，我们将在下一章中探讨这一点。

Jest API 提供了更多功能，但这些是核心特性，应该覆盖您的大多数测试驱动用例。

## 将测试套件迁移到使用 Jest 的测试双工支持

让我们将 `CustomerForm` 测试从我们手工制作的间谍函数中转换出来。我们将从 `fetchSpy` 变量开始。

我们将使用 `jest.spyOn` 来完成这项工作。它本质上创建了一个使用 `jest.fn()` 的间谍，并将其分配给 `global.fetch` 变量。`jest.spyOn` 函数会跟踪所有被间谍监视的对象，以便在没有我们干预的情况下自动恢复它们，使用 `restoreMock` 配置属性。

它还有一个特性，阻止我们间谍化任何不是函数的属性。这会影响我们，因为 Node.js 没有默认的 `global.fetch` 实现。我们将在下一组步骤中看到如何解决这个问题。

值得指出的是，`jest.fn()` 函数的一个非常出色的特性。返回的间谍对象既充当函数本身，也充当模拟对象。它是通过将一个特殊的 `mock` 属性附加到返回的函数上来实现这一点的。结果是，我们不再需要一个 `fetchSpy` 变量来存储我们的间谍对象。我们可以直接引用 `global.fetch`，正如我们即将看到的。

按照以下步骤操作：

1.  更新 `beforeEach` 块，如下所示。这使用 `mockResolvedValue` 来设置一个被 promise 包装的返回值（与 `mockReturnedValue` 相反，后者只是返回一个值，不涉及任何 promise）：

    ```js
    beforeEach(() => {
      initializeReactContainer();
      jest
        .spyOn(global, "fetch")
        .mockResolvedValue(fetchResponseOk({}));
    });
    ```

1.  `CustomerForm`测试套件中有两行遵循此模式：

    ```js
    fetchSpy.stubResolvedValue(...);
    ```

将它们替换为以下代码：

```js
global.fetch.mockResolvedValue(...);
```

1.  有两个期望检查`fetchSpy`。将`expect(fetchSpy)`替换为`expect(global.fetch)`。移除`fetchSpy`变量可以提供更好的可读性和对正在发生的事情的理解。以下是一个期望的例子：

    ```js
    expect(global.fetch).toBeCalledWith(
      "/customers",
      expect.objectContaining({
        method: "POST",
      })
    );
    ```

1.  `bodyOflastFetchRequest`函数需要更新以使用 Jest 间谍对象的`mock`属性。更新如下所示：

    ```js
    const bodyOfLastFetchRequest = () => {
      const allCalls = global.fetch.mock.calls;
      const lastCall = allCalls[allCalls.length - 1];
      return JSON.parse(lastCall[1].body);
    };
    ```

1.  打开`package.json`并添加`restoreMocks`属性，这确保在每个测试之后将`global.fetch`间谍重置到其原始设置。代码如下所示：

    ```js
    "jest": {
      ...,
      "restoreMocks": true
    }
    ```

1.  这应该就是你的`global.fetch`间谍的全部内容。你可以删除`afterEach`块、`fetchSpy`变量声明和`originalFetch`常量定义。

1.  让我们继续到`saveSpy`。回到你的`CustomerForm`测试套件中，找到`表单提交时通知 onSave`测试。按照以下代码片段更新它。我们正在用`jest.fn()`替换`spy()`的使用。注意我们不再需要将`onSave`属性设置为`saveSpy.fn`，而是直接设置为`saveSpy`：

    ```js
    it("notifies onSave when form is submitted", async () => {
      const customer = { id: 123 };
      global.fetch.mockResolvedValue(
        fetchResponseOk(customer)
      );
      const saveSpy = jest.fn();
      render(
        <CustomerForm
          original={blankCustomer}
          onSave={saveSpy}
        />
      );
      await clickAndWait(submitButton());
      expect(saveSpy).toBeCalledWith(customer);
    });
    ```

1.  为`如果 POST 请求返回错误则不通知 onSave`测试重复此操作。

1.  在测试套件的顶部删除你的`spy`函数定义。

1.  在`test/domMatchers.js`中删除你的`toBeCalledWith`匹配器。

1.  我们现在几乎接近一个可工作的测试套件。尝试运行你的测试——你会看到以下错误：

    ```js
    Cannot spy the fetch property because it is not a function; undefined given instead
    ```

1.  为了解决这个问题，我们需要让 Jest 认为`global.fetch`确实是一个函数。最简单的方法是在测试套件启动时设置一个虚拟实现。创建一个`test/globals.js`文件，并在其中添加以下定义：

    ```js
    global.fetch = () => Promise.resolve({});
    ```

1.  现在，回到`package.json`中，将此文件添加到`setupFilesAfterEnv`属性中，如下所示：

    ```js
    "setupFilesAfterEnv": [
      "./test/domMatchers.js",
      "./test/globals.js"
    ],
    ```

1.  使用`npm test`运行所有测试。它们应该会通过。

1.  最后还需要做一件清理工作。找到以下期望：

    ```js
    expect(saveSpy).not.toBeCalledWith();
    ```

如本章前面所述，这个期望是不正确的，我们之所以使用它，仅仅是因为我们手工制作的匹配器并没有完全支持这个用例。我们想要的期望是在任何形式下调用`onSave`时失败。现在我们正在使用 Jest 自己的匹配器，我们可以更优雅地解决这个问题。用以下代码替换这个期望：

```js
expect(saveSpy).not.toBeCalled();
```

你的`CustomerForm`测试套件现在已经完全迁移。我们将以提取一些额外的辅助函数来结束本章。

## 提取 fetch 测试功能

`CustomerForm`不是唯一会调用`fetch`的组件：其中一个练习是更新`AppointmentForm`以将预约提交到服务器。将我们使用的通用代码抽取出来作为一个单独的模块是有意义的。按照以下步骤进行：

1.  创建一个名为`test/spyHelpers.js`的文件，并添加以下函数定义，它与测试套件中的函数相同，但这次标记为导出：

    ```js
    export const bodyOfLastFetchRequest = () => {
      const allCalls = global.fetch.mock.calls;
      const lastCall = allCalls[allCalls.length - 1];
      return JSON.parse(lastCall[1].body);
    };
    ```

1.  创建一个名为 `test/builders/fetch.js` 的文件，并将以下两个函数添加到其中：

    ```js
    export const fetchResponseOk = (body) =>
      Promise.resolve({
        ok: true,
        json: () => Promise.resolve(body),
      });
    export const fetchResponseError = () =>
      Promise.resolve({ ok: false });
    ```

1.  从 `test/CustomerForm.test.js` 内删除那些定义，并用以下代码片段中的 `import` 语句替换它们。在此更改后，运行您的测试并检查它们是否仍然通过：

    ```js
    import { bodyOfLastFetchRequest } from "./spyHelpers";
    import {
      fetchResponseOk,
      fetchResponseError,
    } from "./builders/fetch";
    ```

1.  最后，我们可以通过移除此处显示的 `Promise.resolve` 调用来简化 `fetchResponseOk` 和 `fetchResponseError`。这是因为 Jest 的 `mockResolvedValue` 函数将自动将值包装在一个承诺中：

    ```js
    export const fetchResponseOk = (body) => ({
      ok: true,
      json: () => Promise.resolve(body),
    });
    export const fetchResponseError = () => ({
      ok: false,
    });
    ```

1.  确保您已运行所有测试，并且在继续之前状态为绿色。

现在，您已经准备好在 `AppointmentForm` 测试套件中重用这些函数。

# 摘要

我们刚刚探讨了测试替身及其如何用于验证与协作对象的交互，例如组件属性（`onSave`）和浏览器 API 函数（`global.fetch`）。我们详细研究了间谍和存根，这是您将使用的两种主要类型的替身。您还看到了如何使用并排实现作为一项技术，在您从一个实现切换到另一个实现时，保持测试失败在可控范围内。

尽管本章涵盖了您在处理测试替身时将使用的核心模式，但我们还有一个主要模式尚未介绍，那就是如何监视和模拟 React 组件。这就是我们将在下一章中探讨的内容。

# 练习

尝试以下练习：

1.  向 `CustomerForm` 测试套件添加一个测试，指定当表单在所有验证错误纠正后第二次提交时，错误状态会被清除。

1.  更新 `AppointmentForm` 测试套件以使用 `jest.fn()` 和 `jest.spyOn()`。

1.  扩展 `AppointmentForm` 以使其通过向 `/appointments` 发送 `POST` 请求来提交预约。`/appointments` 端点返回一个没有主体的 `201 Created` 响应，因此您不需要在响应对象上调用 `json` 或向 `onSave` 发送任何参数。

# 进一步阅读

更多信息，请参考以下资源：

+   一个速查表，显示了您在测试 React 代码库时需要的所有 Jest 模拟构造函数

[`reacttdd.com/mocking-cheatsheet`](https://reacttdd.com/mocking-cheatsheet)

+   不同类型测试替身的好介绍

[`martinfowler.com/articles/mocksArentStubs.xhtml`](https://martinfowler.com/articles/mocksArentStubs.xhtml)

+   Fetch API 使用简介

[`github.github.io/fetch`](https://github.github.io/fetch)

+   关于 ARIA 提醒角色信息：[`developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles/alert_role`](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles/alert_role)
