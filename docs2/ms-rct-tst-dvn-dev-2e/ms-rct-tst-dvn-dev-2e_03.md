

# 第三章：重构测试套件

到目前为止，你已经编写了一些测试。尽管它们可能已经足够简单，但它们可以更简单。

构建一个可维护的测试套件非常重要：一个快速且痛苦程度低的构建和适应的测试套件。一种大致衡量可维护性的方法是通过查看每个测试中的代码行数。为了与之前看到的进行比较，在 Ruby 语言中，超过*三行*的测试被认为是一个长测试！

本章将探讨一些你可以使你的测试套件更简洁的方法。我们将通过将常用代码提取到一个模块中，该模块可以在所有测试套件中重用来实现这一点。我们还将创建一个自定义的 Jest 匹配器。

何时是提取可重用代码的正确时机？

到目前为止，你已经在其中编写了一个模块，该模块包含两个测试套件。可以说，现在寻找提取重复代码的机会还为时过早。在非教育环境中，你可能希望在第三个或第四个测试套件之前才开始寻找任何重复代码。

本章将涵盖以下主题：

+   提取可重用的渲染逻辑

+   使用 TDD 创建 Jest 匹配器

+   提取 DOM 辅助函数

到本章结束时，你将学会如何以批判性的眼光对待你的测试套件，以确保其可维护性。

# 技术要求

本章的代码文件可以在以下位置找到：[`github.com/PacktPublishing/Mastering-React-Test-Driven-Development-Second-Edition/tree/main/Chapter03`](https://github.com/PacktPublishing/Mastering-React-Test-Driven-Development-Second-Edition/tree/main/Chapter03)。

# 提取可重用的渲染逻辑

在本节中，我们将提取一个模块，为每个测试初始化一个唯一的 DOM 容器元素。然后，我们将构建一个使用此容器元素的渲染函数。

我们构建的两个测试套件都包含相同的`beforeEach`块，该块在每个测试之前运行：

```js
let container;
beforeEach(() => {
  container = document.createElement("div");
  document.body.replaceChildren(container);
});
```

如果我们能够以某种方式告诉 Jest，任何测试 React 组件的测试套件都应该*始终*使用这个`beforeEach`块并使`container`变量可用于我们的测试，那岂不是很好？

在这里，我们将提取一个新的模块，导出两个东西：`container`变量和`initializeReactContainer`函数。这不会节省我们任何打字时间，但它将隐藏讨厌的`let`声明，并为`createElement`的调用提供一个描述性的名称。

描述性命名的小函数的重要性

通常，提取只包含一行代码的函数是有帮助的。好处是你可以给它一个描述性的名称，这个名称可以作为注释说明这一行代码的作用。这比使用实际的注释更好，因为名称会随着你使用代码而移动。

在这种情况下，对 `document.createElement` 的调用可能会让未来的软件维护者感到困惑。想象一下，这是一个从未对 React 代码进行过单元测试的人。他们可能会问，“为什么测试为每个测试创建一个新的 DOM 元素？”你可以通过给它一个名字，比如 `initializeReactContainer`，来部分回答这个问题。它并不提供完整的答案来说明为什么它是必要的，但它确实暗示了一些关于“初始化”的概念。

让我们继续提取这段代码：

1.  创建一个名为 `test/reactTestExtensions.js` 的新文件。这个文件最终将包含我们将在 React 组件测试中使用的所有辅助方法。

1.  将以下内容添加到文件中。该函数在模块内部隐式地更新 `container` 变量。然后该变量被导出——我们的测试套件可以像访问“只读”常量一样访问这个变量：

    ```js
    export let container;
    export const initializeReactContainer = () => {
      container = document.createElement("div");
      document.body.replaceChildren(container);
    }
    ```

1.  移动到 `test/AppointmentsDayView.test.js` 文件。在现有的导入下面添加以下导入：

    ```js
    import {
      initializeReactContainer,
      container,
    } from "./reactTestExtensions";
    ```

1.  现在，将两个 `beforeEach` 块——记住每个 `describe` 块中都有一个——替换为以下代码：

    ```js
    beforeEach(() => {
      initializeReactContainer();
    });
    ```

1.  从两个 `describe` 块的顶部删除 `let container` 定义。

1.  运行 `npm test` 并验证你的测试是否仍然通过。

现在，我们继续处理 `render` 函数？让我们将其移动到我们的新模块中。这次，它是一个直接的复制和替换工作：

1.  从一个 `describe` 块中复制 `render` 的定义。

1.  将其粘贴到 `reactTestExtensions.js` 文件中。为了参考，这里再次列出：

    ```js
    export const render = (component) =>
      act(() => 
        ReactDOM.createRoot(container).render(component)
      );
    ```

1.  你还需要在文件顶部添加以下导入：

    ```js
    import ReactDOM from "react-dom/client";
    import { act } from "react-dom/test-utils";
    ```

1.  在你的测试文件中，你现在可以更改测试扩展的导入，使其包括新的 `render` 函数，然后删除 `container` 导入：

    ```js
    import {
      initializeReactContainer,
      render,
    } from "./reactTestExtensions";
    ```

1.  从两个测试套件中删除两个 `render` 定义。

1.  运行 `npm test` 并验证你的测试是否仍然通过。

到目前为止，我们已经提取了两个函数。我们还有一个函数要做：`click` 函数。然而，我们还可以创建一个额外的“动作”函数：`click`。现在就让我们来做这件事：

1.  在你的测试扩展文件中创建 `click` 函数，如下所示：

    ```js
    export const click = (element) =>
      act(() => element.click());
    ```

1.  在你的测试文件中，调整你的导入：

    ```js
    import {
      initializeReactContainer,
      container,
      render,
      click,
    } from "./reactTestExtensions";
    ```

1.  在你的测试套件中，将每个 `click` 函数的调用替换为以下行：

    ```js
    click(button);
    ```

1.  `act` 导入在测试套件中不再需要。请从你的测试文件中删除该导入。

1.  运行 `npm test` 并验证你的测试是否仍然通过。

在测试代码中避免使用 act 函数

`act` 函数在测试中引起了很多杂乱，这并不有助于我们追求简洁。幸运的是，我们可以将其推入我们的扩展模块，然后就可以结束了。

记得我们的测试应该始终遵循的 **安排-执行-断言** 模式吗？好吧，我们现在已经从 **安排** 和 **执行** 部分提取了一切。

我们在这里采取的方法，即使用导出的`container`变量，并不是唯一值得探索的方法。例如，您可以创建一个`describe`的包装函数，该函数自动包含一个`beforeEach`块并构建一个在`describe`块作用域内可访问的`container`变量。您可以将其命名为类似`describeReactComponent`的名称。

这种方法的优点是它涉及的代码要少得多——您将不会处理所有那些导入，并且可以在测试套件中删除您的`beforeEach`块。缺点是它非常*巧妙*，这并不总是维护性的好事情。它有点神奇，需要一定的先验知识。

话虽如此，如果您觉得这种方法吸引您，我鼓励您尝试一下。

在下一节中，我们将开始处理测试的**断言**部分。

# 使用 TDD 创建 Jest 匹配器

在我们之前的测试中，我们使用了各种`expect`函数调用：

```js
expect(appointmentTable()).not.toBeNull();
```

在本节中，您将使用测试驱动的方法构建一个匹配器，以确保它正在做正确的事情。在构建测试套件的过程中，您将了解 Jest 匹配器 API。

您已经看到了相当多的匹配器：`toBeNull`、`toContain`、`toEqual`和`toHaveLength`。您也看到了它们如何通过`not`来否定。

匹配器是构建表达性强且简洁的测试的强大方式。您应该花些时间学习 Jest 提供的所有匹配器。

Jest 匹配器库

有很多不同的匹配器库作为 npm 包提供。尽管我们在这本书中不会使用它们（因为我们是从第一原理构建一切的），但您应该利用这些库。请参阅本章末尾的*进一步阅读*部分，以获取在测试 React 组件时对您有用的库列表。

通常，您会想构建匹配器。至少有几次情况会促使您这样做：

+   您所编写的期望语句可能相当冗长、篇幅较长，或者用普通语言读起来并不顺畅。

+   一些测试反复重复相同的期望组。这是您有一个可以编码到单个匹配器中的业务概念，该匹配器将专门针对您的项目的迹象。

第二点很有趣。如果您在多个测试中多次编写相同的期望，您应该像对待生产源代码中的重复代码一样对待它。您会将其提取到一个函数中。在这里，匹配器起到了相同的作用，只不过使用匹配器而不是函数可以帮助您记住这一行代码是关于您软件的特殊事实声明：一个规范。

每个测试一个期望

你通常应该只为每个测试设定一个期望。“未来的你”会感谢你保持事情简单！（在*第五章*，*添加复杂表单交互*中，我们将探讨一个多个期望有益的情况。）

你可能会听到这个指南，立刻感到惊恐。你可能想象会有无数的小测试爆炸。但如果你准备好编写匹配器，你可以为每个测试设定一个期望，同时仍然保持测试数量在可控范围内。

我们在本节将要构建的匹配器被称为 `toContainText`。它将替换以下期望：

```js
expect(appointmentTable().textContent).toContain("Ashley");
```

它将替换成以下形式，这稍微更容易阅读：

```js
expect(appointmentTable()).toContainText("Ashley");
```

下面是终端上的输出效果：

![图 3.1 – 当 `toContainText` 匹配器失败时的输出](img/Figure_3.01_B18423.jpg)

图 3.1 – 当 `toContainText` 匹配器失败时的输出

让我们开始吧：

1.  创建一个名为 `test/matchers` 的新目录。这是匹配器的源代码和测试将存放的地方。

1.  创建新的 `test/matchers/toContainText.test.js` 文件。

1.  按照下面的示例编写第一个测试。这个测试引入了一些新概念。首先，它显示 `matcher` 是一个接受两个参数的函数：实际元素和要匹配的数据。其次，它显示该函数返回一个具有 `pass` 属性的对象。如果匹配器成功“匹配”——换句话说，它通过了，那么这个属性就是 `true`：

    ```js
    import { toContainText } from "./toContainText";
    describe("toContainText matcher", () => {
      it("returns pass is true when text is found in the given DOM element", () => {
        const domElement = {
          textContent: "text to find"
        };
        const result = toContainText(
          domElement,
          "text to find"
        );
        expect(result.pass).toBe(true);
      });
    });
    ```

1.  创建另一个新文件，命名为 `test/matchers/toContainText.js`。这个第一个测试很容易通过：

    ```js
    export const toContainText = (
      received,
      expectedText
    ) => ({
      pass: true
    });
    ```

1.  我们需要三管齐下才能到达真正的实现。按照下面的示例编写下一个测试：

    ```js
    it("return pass is false when the text is not found in the given DOM element", () => {
      const domElement = { textContent: "" };
      const result = toContainText(
        domElement,
        "text to find"
      );
      expect(result.pass).toBe(false);
    });
    ```

1.  现在，继续实现我们的匹配器，如下所示。在这个阶段，你有一个正在工作的匹配器——它只需要被连接到 Jest：

    ```js
    export const toContainText = (
      received,
      expectedText
    ) => ({
      pass: received.textContent.includes(expectedText)
    });
    ```

1.  在我们使用这个功能之前，先填充一下预期返回值的第二个属性：`message`。这是一个很好的实践。下面的测试显示，我们期望消息包含匹配文本本身，作为对程序员的实用提醒：

    ```js
    it("returns a message that contains the source line if no match", () => {
      const domElement = { textContent: "" };
      const result = toContainText(
        domElement,
        "text to find"
      );
      expect(
        stripTerminalColor(result.message())
      ).toContain(
        `expect(element).toContainText("text to find")`
      );
    });
    ```

理解消息函数

`message` 函数的要求很复杂。在基本层面上，它是一个当期望失败时显示的有用字符串。然而，它不仅仅是一个字符串——它是一个返回字符串的函数。这是一个性能特性：`message` 的值不需要在失败之前被评估。但更复杂的是，消息应该根据期望是否被否定而改变。如果 `pass` 是 `false`，那么 `message` 函数应该假设匹配器是在“正面”意义上被调用的——换句话说，没有 `.not` 修饰符。但如果 `pass` 是 `true`，并且 `message` 函数最终被调用，那么可以安全地假设它已经被否定。我们需要为这个否定情况编写另一个测试，这个测试稍后会出现。

1.  此函数使用一个 `stripTerminalColor` 函数，我们现在应该在测试套件上方定义它。它的目的是移除任何添加颜色的 ASCII 转义码：

    ```js
    const stripTerminalColor = (text) =>
        text.replace(/\x1B\[\d+m/g, "");
    ```

测试 ASCII 转义码

正如您已经看到的，当 Jest 打印出测试失败时，您会看到一大堆红色和绿色的彩色文本。这是通过在文本字符串中打印 ASCII 转义码来实现的。

这是一个难以测试的事情。因此，我们做出了实用主义的选择，不去麻烦测试颜色。相反，`stripTerminalColor` 函数从字符串中移除这些转义码，这样您就可以测试文本输出，就像它是纯文本一样。

1.  通过使用 Jest 的 `matcherHint` 和 `printExpected` 函数使该测试通过，如下所示。`matcherHint` 函数的工作方式并不特别清晰，但希望您可以通过运行测试并看到最后一个通过来说服自己它确实做了我们期望的事情！`printExpected` 函数给我们的值添加引号并将其颜色改为绿色。

    ```js
    import {
      matcherHint,
      printExpected,
    } from "jest-matcher-utils";
    export const toContainText = (
      received,
      expectedText
    ) => {
      const pass = 
        received.textContent.includes(expectedText);
      const message = () =>
        matcherHint(
          "toContainText",
          "element",
          printExpected(expectedText),
          { }
        );
      return { pass, message };
    };
    ```

了解 Jest 的匹配器实用工具

在撰写本文时，我发现了解 Jest 匹配器实用函数的最佳方式是阅读它们的源代码。如果您愿意，也可以完全避免使用它们 - 没有义务使用它们。

1.  现在是复杂部分。添加以下测试，它指定了使用否定匹配器时的失败期望场景。消息应该反映匹配器已被否定，如下所示：

    ```js
    it("returns a message that contains the source line if negated match", () => {
      const domElement = { textContent: "text to find" };
      const result = toContainText(
        domElement,
        "text to find"
      );
      expect(
        stripTerminalColor(result.message())
      ).toContain(
        `expect(container).not.toContainText("text to find")`
      );
    });
    ```

1.  要使其通过，向 `matcherHint` 传递一个新选项：

    ```js
    ...
    matcherHint(
      "toContainText",
      "element",
      printExpected(expectedText),
      { isNot: pass }
    );
    ...
    ```

1.  需要添加最后一个测试。我们可以打印出元素的 `textContent` 属性的实际值，这有助于在发生测试失败时进行调试。添加以下测试：

    ```js
    it("returns a message that contains the actual text", () => {
      const domElement = { textContent: "text to find" };
      const result = toContainText(
        domElement,
        "text to find"
      );
      expect(
        stripTerminalColor(result.message())
      ).toContain(`Actual text: "text to find"`);
    });
    ```

1.  通过调整您的匹配器代码使其通过，如下所示。注意新 `printReceived` 函数的使用，它与 `printExpected` 函数相同，只是它将文本颜色改为红色：

    ```js
    import {
      matcherHint,
      printExpected,
      printReceived,
    } from "jest-matcher-utils";
    export const toContainText = (
      received,
      expectedText
    ) => {
      const pass = 
        received.textContent.includes(expectedText);
      const sourceHint = () =>
        matcherHint(
          "toContainText",
          "element",
          printExpected(expectedText),
          { isNot: pass }
        );
      const actualTextHint = () =>
    "Actual text: " + 
        printReceived(received.textContent);
      const message = () =>
        [sourceHint(), actualTextHint()].join("\n\n");
      return { pass, message };
    };
    ```

1.  是时候将测试插入 Jest 中了。为此，创建一个名为 `test/domMatchers.js` 的新文件，内容如下：

    ```js
    import {
      toContainText
    } from "./matchers/toContainText";
    expect.extend({
      toContainText,
    });
    ```

1.  打开 `package.json` 并更新您的 Jest 配置，以便在运行测试之前加载此文件：

    ```js
    "jest": {
      ...,
      "setupFilesAfterEnv": ["./test/domMatchers.js"]
    }
    ```

1.  您的新匹配器已准备好使用。打开 `test/AppointmentsDayView.test.js` 并更改所有使用 `expect(<element>.textContent).toEqual(<text>)` 和 `expect(<element>.textContent).toContain(<text>)` 形式的测试。它们应该替换为 `expect(<element>).toContainText(<text>)`。

1.  运行您的测试；您应该看到它们仍然全部通过。花点时间玩一下，看看您的匹配器是如何工作的。首先，将其中一个期望的文本值更改为错误的内容，并观察匹配器失败。看看输出消息的样子。然后，将期望值改回正确的内容，但通过将其更改为 `.not.toContainText` 来否定匹配器。最后，将您的代码恢复到全绿色状态。

为什么我们要进行匹配器的测试驱动？

您应该为任何不仅仅是简单地调用其他函数或设置变量的代码编写测试。在本章的开始，您提取了 `render` 和 `click` 等函数。这些函数不需要测试，因为您只是将同一行代码从一个文件移植到另一个文件。但这个匹配器做了一些更复杂的事情——它必须返回一个符合 Jest 所需模式的对象。它还使用了 Jest 的实用函数来构建有用的消息。这种复杂性需要测试。

如果您正在为库构建匹配器，您应该对匹配器的实现更加小心。例如，我们没有麻烦去检查接收到的值是否是 HTML 元素。这没关系，因为这个匹配器只存在于我们的代码库中，我们控制了它的使用方式。当您将匹配器打包用于其他项目时，您也应该验证函数输入是否是您期望看到的值。

您现在已经成功驱动测试了您的第一个匹配器。随着本书的进展，您将有更多机会练习这项技能。现在，我们将继续进行清理工作的最后一部分：创建一些流畅的 DOM 辅助函数。

# 提取 DOM 辅助函数

在本节中，我们将提取一些小函数，这将帮助我们的测试变得更加易读。与刚刚构建的匹配器相比，这将更加直接。

`reactTestExtensions.js` 模块已经包含了您使用过的三个函数：`initializeReactContainer`、`render` 和 `click`。

现在，我们将添加四个新的：`element`、`elements`、`typesOf` 和 `textOf`。这些函数旨在帮助您的测试读起来更像普通英语。让我们看一个例子。以下是我们的测试期望之一：

```js
const listChildren = document.querySelectorAll("li");
expect(listChildren[0].textContent).toEqual("12:00");
expect(listChildren[1].textContent).toEqual("13:00");
```

我们可以引入一个函数 `elements`，它是 `document.querySelectorAll` 的简短版本。较短的名称意味着我们可以去掉额外的变量：

```js
expect(elements("li")[0].textContent).toEqual("12:00");
expect(elements("li")[1].textContent).toEqual("13:00");
```

这段代码现在调用 `querySelectorAll` 两次——所以它比以前做了更多的工作——但它也更短、更易读。我们可以更进一步。我们可以通过匹配 `elements` 数组本身来将这缩减为一个 `expect` 调用。由于我们需要 `textContent`，我们将简单地构建一个名为 `textOf` 的映射函数，它接受输入数组并返回其中每个元素的 `textContent` 属性：

```js
expect(textOf(elements("li"))).toEqual(["12:00", "13:00"]);
```

当 `toEqual` 匹配器应用于数组时，将检查每个数组具有相同数量的元素，并且每个元素出现在相同的位置。

我们已经将原始的三行代码缩减为仅仅一行！

让我们继续构建这些新的辅助函数：

1.  打开 `test/reactTestExtensions.js` 并在文件底部添加以下定义。您会注意到元素使用了 `Array.from`。这样做是为了使结果数组可以被 `typesOf` 和 `textOf` 映射。 

    ```js
    export const element = (selector) =>
      document.querySelector(selector);
    export const elements = (selector) =>
      Array.from(document.querySelectorAll(selector));
    export const typesOf = (elements) =>
      elements.map((element) => element.type);
    export const textOf = (elements) =>
      elements.map((element) => element.textContent);
    ```

1.  打开 `test/AppointmentsDayView.test.js` 并将扩展导入更改为包括所有这些新函数：

    ```js
    import {
      initializeReactContainer,
      render,
      click,
      element,
      elements,
      textOf,
      typesOf,
    } from "./reactTestExtensions";
    ```

1.  现在，进行搜索并替换`document.querySelectorAll`，将每个出现的位置替换为`elements`。运行`npm test`并验证测试是否仍然通过。

1.  搜索并替换`document.querySelector`，将每个出现的位置替换为`element`。再次运行你的测试并检查一切是否正常。

1.  你将看到测试在预约时间渲染。用这个期望替换现有的期望：

    ```js
    expect(textOf(elements("li"))).toEqual([
      "12:00", "13:00"
    ]);
    ```

1.  找到`"has a button element in each li"`测试，并用以下单个期望替换现有的期望。注意，如果你的期望测试整个数组，那么对数组长度的期望就不再必要了：

    ```js
    expect(typesOf(elements("li > *"))).toEqual([
      "button",
      "button",
    ]);
    ```

1.  最后三个测试使用`elements("button")[1]`提取屏幕上的第二个按钮。将这个定义向上推，紧接在`beforeEach`块下方，并给它一个更描述性的名称：

    ```js
    const secondButton = () => elements("button")[1];
    ```

1.  现在，你可以在三个测试中使用这个功能。现在就更新它们。例如，中间的测试可以更新如下：

    ```js
    click(secondButton());
    expect(secondButton().className).toContain("toggled");
    ```

1.  作为最后的润色，将出现在某些测试中的`listChild`和`listElement`变量内联化——换句话说，移除变量的使用，并在期望中直接调用函数。例如，`"renders an ol element to display appointments"`测试可以按照以下方式重写期望：

    ```js
    expect(element("ol")).not.toBeNull();
    ```

1.  再次运行`npm test`并验证一切是否仍然正常。

并非所有辅助函数都需要提取

你会注意到你提取的辅助函数都非常通用——它们没有提及正在测试的具体组件。尽可能保持辅助函数的通用性是好的。另一方面，有时拥有非常本地化的辅助函数很有帮助。在你的测试套件中，你已经有一个名为`appointmentsTable`的和一个名为`secondButton`的。这些应该保留在测试套件中，因为它们是本地化的。

在本节中，你看到了我们简化测试套件的最终技术，即提取流畅的辅助函数，这些函数有助于保持期望简短，并使它们读起来像普通的英语。

你还看到了在数组上运行期望而不是对单个项目有期望的技巧。这并不总是合适的行动方案。你将在*第五章*中看到这个例子，*添加复杂表单交互*。

# 摘要

本章重点介绍了改进我们的测试套件。可读性至关重要。你的测试充当了软件的规范。每个组件测试都必须清楚地说明组件的期望。当测试失败时，你希望尽可能快地了解为什么它失败了。

你已经看到，这些优先级通常与我们对良好代码的通常想法相冲突。例如，在我们的测试中，我们愿意牺牲性能，如果这使测试更易读。

如果您以前使用过 React 测试，想想平均测试的长度。在本章中，您已经看到了几种保持测试简短的方法：构建特定领域的匹配器和提取用于查询 DOM 的小函数。

您还学会了如何提取 React 初始化代码以避免测试套件中的杂乱。

在下一章，我们将回到为我们的应用添加新功能：使用表单进行数据录入。

# 练习

使用您刚刚学到的技术，创建一个名为 `toHaveClass` 的新匹配器，以替换以下期望：

```js
expect(secondButton().className).toContain("toggled");
```

在您的新匹配器设置完成后，它应该如下所示：

```js
expect(secondButton()).toHaveClass("toggled"); 
```

此匹配器也有否定形式：

```js
expect(secondButton().className).not.toContain("toggled");
```

您的匹配器应该适用于此表单并显示适当的失败信息。

# 进一步阅读

为了了解更多关于本章所涉及的主题，请查看以下资源：

+   以下 GitHub 仓库包含用于测试 React 组件的有用匹配器：[`github.com/jest-community/jest-extended`](https://github.com/jest-community/jest-extended)

+   以下链接提供了 Jest 匹配器工具的来源，我发现这对于了解如何编写简单的匹配器很有用：[`github.com/facebook/jest/tree/main/packages/jest-matcher-utils`](https://github.com/facebook/jest/tree/main/packages/jest-matcher-utils)
