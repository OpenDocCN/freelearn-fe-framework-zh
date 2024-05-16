# 第五章：*第四章*

# 测试属性和自定义事件

有不同的方法来测试属性、事件和自定义事件。

属性是从父组件传递到子组件的自定义属性。自定义事件则恰恰相反：它们通过事件将数据发送到直接父级。当它们结合在一起时，它们是 Vue.js 组件中交互和通信的线路。

在单元测试中，测试内部和外部（属性和自定义事件）意味着测试组件在隔离状态下接收和发送数据的行为。所以，让我们动手吧。

# 属性

当我们测试组件属性时，我们可以测试当我们传递某些属性时组件的行为。然而，在继续之前，请考虑这个重要的注意事项：

要将属性传递给组件，请使用`propsData`而不是`props`。后者用于定义属性，而不是传递数据。

首先，创建一个`Message.test.js`文件并添加以下代码：

```js
describe("Message.test.js", () => {
  let cmp;
  describe("Properties", () => {
    // @TODO
  });
});
```

我们在`describe`表达式中对测试用例进行分组，它们可以被嵌套。因此，我们可以使用这种策略来分别对属性和事件进行分组测试。

然后，我们将创建一个辅助工厂函数来创建一个消息组件，并给它一些属性：

```js
const createCmp = propsData => mount(Message, { propsData });
```

## 测试属性的存在性

我们可以测试的最明显的事情是属性是否存在。记住`Message.vue`组件有一个`message`属性，所以让我们假设它正确接收了该属性。vue-test-utils 带有一个`hasProp(prop, value)`函数，对于这种情况非常方便：

```js
it("has a message property", () => {
  cmp = createCmp({ message: "hey" });
  expect(cmp.hasProp("message", "hey")).toBeTruthy();
});
```

属性的行为是这样的，只有在组件中声明了它们才会被接收。这意味着如果我们传递了一个未定义的属性，那么它就不会被接收。因此，要检查属性的不存在，可以使用一个不存在的属性：

```js
it("has no cat property", () => {
  cmp = createCmp({ cat: "hey" });
  expect(cmp.hasProp("cat", "hey")).toBeFalsy();
});
```

然而，在这种情况下，测试将失败，因为 Vue 具有非 props 属性。这将其设置为`Message`组件的根部，因此被识别为一个 prop，因此测试将返回`true`。将其更改为`toBeTruty`将使其通过此示例：

```js
it("has no cat property", () => {
  cmp = createCmp({ cat: "hey" });
  expect(cmp.hasProp("cat", "hey")).toBeTruthy();
});
```

我们也可以测试**默认值**。转到`Message.vue`并将`props`更改如下：

```js
props: {
  message: String,
  author: {
    type: String,
    default: 'Paco'
  }
},
```

然后，测试可以如下所示：

```js
it("Paco is the default author", () => {
  cmp = createCmp({ message: "hey" });
  expect(cmp.hasProp("author", "Paco")).toBeTruthy();
});
```

## 断言属性验证

属性可以具有验证规则，确保属性是必需的或者是确定类型的。让我们将`message`属性编写如下：

```js
props: {
  message: {
    type: String,
    required: true,
    validator: message => message.length > 1
  }
}
```

更进一步，您可以使用自定义构造函数类型或自定义验证规则，如您可以在`文档`中看到的（[`vuejs.org/v2/guide/components.html#Prop-Validation`](https://vuejs.org/v2/guide/components.html#Prop-Validation)）。现在不要这样做；我只是举个例子：

```js
class Message {}

props: {
  message: {
    type: Message, // It's compared using instance of
    ...
    }
  }
}
```

每当验证规则不满足时，Vue 会显示`console.error`。例如，对于`createCmp({ message: 1 })`，错误将如下所示：

```js
 [Vue warn]: Invalid prop: type check failed for prop "message". Expected String, got Number.
(found in <Root>)
```

在撰写本文时，`vue-test-utils`没有用于测试这一点的实用程序。我们可以使用`jest.spyOn`来代替测试：

```js
it("message is of type string", () => {
  let spy = jest.spyOn(console, "error");
  cmp = createCmp({ message: 1 });
  expect(spy).toBeCalledWith(
    expect.stringContaining("[Vue warn]: Invalid prop")
  );
  spy.mockReset(); // or mockRestore() to completely remove the mock
});
```

在这里，我们正在监视`console.error`函数，并检查它是否显示包含特定字符串的消息。这不是检查它的理想方式，因为我们正在监视全局对象并依赖副作用。

幸运的是，有一种更简单的方法来做到这一点，那就是通过检查`vm.$options`。这是 Vue 存储的组件选项的扩展。通过扩展，我的意思是您可以以不同的方式定义属性：

```js
props: ["message"];
// or
props: {
  message: String;
}
// or
props: {
  message: {
    type: String;
  }
}
```

但它们最终都会以最扩展的对象形式结束（例如最后一个）。因此，如果我们检查第一个案例的`cmp.vm.$option.props.message`，它们都将以`{ type: X }`格式存在（尽管对于第一个示例，它将是`{ type: null}`）。

考虑到这一点，我们可以编写一个测试套件来测试`message`属性是否具有预期的验证规则：

```js
describe('Message.test.js', () => {
  ...
  describe('Properties', () => {
    ...
    describe('Validation', () => {
      const message = createCmp().vm.$options.props.message
      it('message is of type string', () => {
       expect(message.type).toBe(String)
      })
      it('message is required', () => {
        expect(message.required).toBeTruthy()
      })
      it('message has at least length 2', () => {
        expect(message.validator && message.validator('a')).toBeFalsy()
        expect(message.validator && message.validator('aa')).toBeTruthy()
      })
    })
```

# 自定义事件

我们可以在自定义事件中测试至少两件事：

+   断言在动作之后触发了事件

+   检查事件监听器在触发时是否调用

在`MessageList.vue`和`Message.vue`组件示例中，这被翻译为以下内容：

+   断言`Message`组件在单击消息时触发`message-clicked`。

+   检查`MessageList`以确保当触发`message-clicked`时，会调用`handleMessageClick`函数

首先，转到`Message.vue`并使用`$emit`来触发自定义事件：

```js
<template>
  <li
    style="margin-top: 10px"
    class="message"
    @click="handleClick">
      {{message}}
  </li>
</template>
<script>
export default {
  name: "Message",
  props: ["message"],
  methods: {
    handleClick() {
      this.$emit("message-clicked", this.message)
    }
  }
};
</script>
```

然后，在`MessageList.vue`中，使用`@message-clicked`处理事件：

```js
<template>
  <ul>
    <Message
      @message-clicked="handleMessageClick"
      :message="message"
      v-for="message in messages"
      :key="message"/>
  </ul>
</template>
<script>
import Message from "./Message";
export default {
  name: "MessageList",
  props: ["messages"],
  methods: {
    handleMessageClick(message) {
      console.log(message)
    }
  },
  components: {
    Message
  }
};
</script>
```

现在是时候编写单元测试了。在`test/Message.spec.js`文件中创建一个嵌套的`describe`，并准备前面提到的*"断言`Message`组件在单击消息时触发`message-clicked`"*的基本内容：

```js
describe("Message.test.js", () => {
  describe("Events", () => {
    beforeEach(() => {
      cmp = createCmp({ message: "Cat" });
    });
    it("calls handleClick when click on message", () => {
      // @TODO
    });
  });
});
```

## 测试事件点击是否调用了方法处理程序

我们可以测试的第一件事是，当点击消息时，`handleClick`函数是否被调用。为此，我们可以使用包装组件的`trigger`和使用`spyOn`函数的 Jest 间谍：

```js
it("calls handleClick when click on message", () => {
  const spy = spyOn(cmp.vm, "handleClick");
  cmp.update(); // Forces to re-render, applying changes on template
  const el = cmp.find(".message").trigger("click");
  expect(cmp.vm.handleClick).toBeCalled();
});
```

查看`cmp.update()`。当我们更改模板中使用的内容 - 在这种情况下是`handleClick` - 并且我们希望模板应用这些更改时，我们需要使用`update`函数。

请记住，通过使用间谍，将调用原始的`handleClick`方法。你可能有意想要这样做；然而，通常情况下，我们希望避免这种情况，只需检查点击时是否确实调用了该方法。为此，我们可以使用 Jest Mock 函数：

```js
it("calls handleClick when click on message", () => {
  cmp.vm.handleClick = jest.fn();
  cmp.update();
  const el = cmp.find(".message").trigger("click");
  expect(cmp.vm.handleClick).toBeCalled();
});
```

在这里，我们完全替换了`vm`的`mount`函数返回的包装组件上可访问的`handleClick`方法。

我们可以通过使用官方工具提供的`setMethods`助手来使其更加简单：

```js
it("calls handleClick when click on message", () => {
  const stub = jest.spy();
  cmp.setMethods({ handleClick: stub });
  cmp.update();
  const el = cmp.find(".message").trigger("click");
  expect(stub).toBeCalled();
});
```

使用`setMethods`是建议的方法，因为它是官方工具在 Vue 内部发生变化时提供给我们的抽象。

## 测试自定义事件 message-clicked 是否被触发

我们已经测试了点击方法是否调用了其处理程序，但我们还没有测试处理程序是否发出了`message-clicked`事件。我们可以直接调用`handleClick`方法，并结合 Vue 的`vm` `$on`方法使用 Jest Mock 函数：

```js
it("triggers a message-clicked event when a handleClick method is called", () => {
  const stub = jest.fn();
  cmp.vm.$on("message-clicked", stub);
  cmp.vm.handleClick();
  expect(stub).toBeCalledWith("Cat");
});
```

请注意，这里我们使用了`toBeCalledWith`，因此我们可以确切断言我们期望的参数，使测试更加健壮。这并不是说我们在这里没有使用`cmp.update()`，因为我们没有进行需要传播到模板的更改。

## 测试@message-clicked 是否触发了事件

对于自定义事件，我们不能使用`trigger`方法，因为它只适用于 DOM 事件。但是，我们可以通过获取`Message`组件并使用其`vm.$emit`方法来自己发出事件。因此，在`MessageList.test.js`中添加以下测试：

```js
it("Calls handleMessageClick when @message-click happens", () => {
  const stub = jest.fn();
  cmp.setMethods({ handleMessageClick: stub });
  cmp.update();
  const el = cmp.find(Message).vm.$emit("message-clicked", "cat");
  expect(stub).toBeCalledWith("cat");
});
```

我将把测试`handleMessageClicked`的工作留给你。

# 总结

在本章中，我们探讨了测试属性和事件的几种情况。`vue-test-utils`，官方的 Vue 测试工具，确实使这变得更加容易。

你可以在**GitHub**上找到我们使用的工作代码（[`github.com/alexjoverm/vue-testing-series/tree/Test-Properties-and-Custom-Events-in-Vue-js-Components-with-Jest`](https://github.com/alexjoverm/vue-testing-series/tree/Test-Properties-and-Custom-Events-in-Vue-js-Components-with-Jest)）。
