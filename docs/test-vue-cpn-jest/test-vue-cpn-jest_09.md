# 第九章：快照测试

到目前为止，你已经看到了如何测试 Vue.js 组件的结构、样式、方法、计算属性、事件、观察者等等。你已经学会了通过使用各种技术和方法来做到这一点。

但如果我告诉你，你可以通过简单地使用快照测试来测试大部分内容呢？

你已经在*第一章，使用 Jest 编写第一个 Vue.js 组件单元测试*和*第二章，深度测试 Vue.js 组件*中看到了快照测试的使用，但这些章节更多地侧重于解释浅渲染和深渲染，所以我还没有详细解释过它。

快照测试是通过比较两个不同的输出来进行断言的技术。

把它想象成类似于端到端测试中用于检查回归的屏幕截图技术：第一次测试运行时会对屏幕的一部分（例如一个按钮）进行截图，从那一刻起，同一个测试的所有后续运行都会将新的截图与原始截图进行比较。如果它们相同，测试通过；否则就会有回归。

快照测试的工作方式类似，但它不是比较图像，而是比较可序列化的输出，比如 JSON 和 HTML，或者只是字符串。

由于 Vue.js 渲染 HTML，你可以使用快照测试来断言组件在不同状态下渲染的 HTML。

# 重新思考快照

对于这个例子，让我们考虑以下的`ContactBox.vue`组件：

```js
<template>
  <div :class="{ selected: selected }" @click="handleClick">
    <p>{{ fullName }}</p>
  </div>
</template>
<script>
  export default {
    props: ["id", "name", "surname", "selected"],
    computed: {
      fullName() {
        return `${this.name} ${this.surname}`;
      }
    },
    methods: {
      handleClick() {
        this.$emit("contact-click", this.id);
      }
    }
  };
</script>
```

在这种情况下，我们可以测试这个组件的几个方面：

+   `fullName`是`name` + `surname`的组合。

+   当组件被选中时，它具有`selected`类。

+   它会触发`contact-click`事件。

创建验证这些规范的测试的一种方法是分别检查所有内容 - 附加到 DOM 元素的类、HTML 结构、计算属性和状态。

正如你在其他章节中看到的，你可以按照以下方式执行这些测试：

```js
import { mount } from "vue-test-utils";
import ContactBox from "../src/components/ContactBox";
const createContactBox = (id, name, surname, selected) =>
  mount(ContactBox, {
    propsData: { id, name, surname, selected }
  });
describe("ContactBox.test.js", () => {
  it("fullName should be the combination of name + surname", () => {
    const cmp = createContactBox(0, "John", "Doe", false);
    expect(cmp.vm.fullName).toBe("John Doe");
  });
  it("should have a selected class when the selected prop is true", () => {
    const cmp = createContactBox(0, "John", "Doe", true);
    expect(cmp.classes()).toContain("selected");
  });
  it("should emit a contact-click event with its id when the component is clicked", () => {
    const cmp = createContactBox(0, "John", "Doe", false);
    cmp.trigger("click");
    const payload = cmp.emitted("contact-click")[0][0];
    expect(payload).toBe(0);
  });
});
```

但现在，让我们想一想快照测试如何帮助我们。

如果你仔细想想，组件会根据它的状态进行渲染。让我们把这称为**渲染状态**。

通过快照测试，我们可以不用担心检查特定的东西，比如属性、类、方法和计算属性，而是可以检查渲染状态，因为这是组件状态的预期结果。

为此，你可以按照以下方式对先前的测试使用快照测试：

```js
it("fullName should be the combination of name + surname", () => {
  const cmp = createContactBox(0, "John", "Doe", false);
  expect(cmp.element).toMatchSnapshot();
});
```

正如您所看到的，现在不再单独检查事物，我只是断言`cmp.element`的快照，这是组件的呈现 HTML。

如果您现在运行测试套件，应该已经创建了一个`ContactBox.test.js.snap`文件，并且您还会在控制台输出中看到一条消息：

![图 9.1](img/Image56419.jpg)

图 9.1

让我们分析生成的快照：

```js
// Jest Snapshot v1, https://goo.gl/fbAQLP
exports[
  `ContactBox.test.js fullName should be the combination of name + surname 1`
] = `
<div
  class=""
>
  <p>
    John Doe
  </p>
</div>
`;
```

这个测试的目的是检查计算属性`fullName`是否将名字和姓氏结合起来，用空格分隔。从快照中可以看出，这是发生的，*John Doe*在那里，所以您可以认为这个测试是有效的。

同样，您也可以使用快照测试编写第二个测试：

```js
it("should have a selected class when the selected prop is true", () => {
  const cmp = createContactBox(0, "John", "Doe", true);
  expect(cmp.element).toMatchSnapshot();
});
```

请注意，这个测试和上一个测试之间唯一改变的方面是将`selected`属性设置为`true`。

这就是快照测试的威力：您可以玩弄*组件的不同状态*，而只需断言呈现状态。

这个测试的目的是验证当属性为`true`时它是否具有`selected`类。现在，让我们再次运行测试套件，如果再次检查`ContactBox.test.js.snap`，您会看到另一个快照已经被添加：

```js
exports[
  `ContactBox.test.js should have a selected class when the selected prop is true 1`
] = `
<div
  class="selected"
>
  <p>
    John Doe
  </p>
</div>
`;
```

并且所选的类在那里，正如预期的那样，所以我们可以认为这个也是有效的。

# 当快照测试无法帮助时

您是否注意到我没有提及第三个测试？为了回忆这个测试，让我们再次检查它：

```js
it("should emit a contact-click with its id when the component is clicked", () => {
  const cmp = createContactBox(0, "John", "Doe", false);
  cmp.trigger("click");
  const payload = cmp.emitted("contact-click")[0][0];
  expect(payload).toBe(0);
});
```

在这种情况下，当组件被点击时，它不执行任何改变组件状态的操作，这意味着呈现状态不会改变。我们在这里只是测试对组件呈现没有影响的行为。

因此，我们可以说*快照测试对于检查呈现状态的变化是有用的*。如果呈现状态不改变，快照测试就无法帮助我们。

# 当测试失败时

生成的快照是决定测试是否有效的真相来源。这就是检查回归的方式，最终取决于您的标准。

例如，转到`ContactBox.vue`组件，并将`fullName`计算属性更改为用逗号分隔：

```js
fullName() {
  return `${this.name}, ${this.surname}`;
}
```

如果您再次运行测试，其中一些测试将失败，因为呈现结果与以前不同。您将收到以下类似的错误：

```js
Received value does not match stored snapshot 1.
  - Snapshot
  + Received
    <div
      class=""
    >
      <p>
  -    John Doe
  +    John, Doe
      </p>
    </div>
```

从那时起，通常与测试相关，你必须决定这是一个有意的变化还是一个回归。你可以按下*'u'*来更新快照：

![图 9.2](img/Image56429.jpg)

图 9.2

在应用 TDD 时使用观察模式`npm run test -- --watch`会很方便。这将非常方便，因为 Jest 为更新快照提供了许多选项：

+   按下**'u'**以更新所有快照。

+   按下**'i'**以交互方式逐个更新快照。

# 结论

快照测试**节省了大量时间**。这个例子很基础，但想象一下测试一个具有许多不同渲染状态的更复杂的组件...

当然，你可以针对特定事物进行断言，但这比根据状态断言组件的渲染方式要麻烦得多，因为大多数情况下，如果你改变了代码，就必须根据测试改变断言，而使用快照测试则不需要。

此外，你可以**发现**你没有考虑到的**回归**，也许是你在测试中没有考虑到的东西，或者是改变了组件的渲染，但快照会提醒你这一点。

我现在想提到一些你应该记住的**注意事项**：

+   快照测试并不取代特定的断言。虽然大多数情况下可以这样做，但两种测试方式完全可以结合使用。

+   不要太轻易地更新快照。如果你发现一个测试失败是因为它与快照不匹配，那么在太快更新之前，仔细研究一下。我也有过这样的经历。

如果你想自己尝试一下，你可以在**GitHub**上找到本章中使用的完整示例（[`github.com/alexjoverm/vue-testing-series/tree/chapter-9`](https://github.com/alexjoverm/vue-testing-series/tree/chapter-9)）。
