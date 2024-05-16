# 第三章：*第二章*

# 深度渲染 Vue.js 组件的测试

到目前为止，我们已经看到了如何使用浅渲染来测试一个组件，以防止组件的子树渲染。

但在某些情况下，我们希望测试作为一组行为的组件，或者**分子**（[`atomicdesign.bradfrost.com/chapter-2/#molecules`](http://atomicdesign.bradfrost.com/chapter-2/#molecules)）,正如*Atomic Design*中所述。请记住，这适用于**呈现组件**（[`medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0`](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)），因为它们不知道应用程序的状态和逻辑。在大多数情况下，你可能会希望为容器组件使用浅渲染。

# 添加一个 Message 组件

在`Message`和`MessageList`组件的情况下，除了编写它们各自的单元测试之外，我们可能还想将它们作为一个单元进行测试。

让我们首先创建`components/Message.vue`：

```js
<template>
  <li class="message">{{ message }}</li>
</template>
<script>
  export default {
    props: ["message"]
  };
</script>
```

并更新`components/MessageList.vue`来使用它：

```js
<template>
  <ul>
    <Message :message="message" v-for="message in messages" />
  </ul>
</template>
<script>
  import Message from "./Message";
  export default {
    props: ["messages"],
    components: {
      Message
    }
  };
</script>
```

# 使用 Message 组件测试 MessageList

要使用深度渲染测试`MessageList`，我们只需要在先前创建的`test/MessageList.test.js`中使用`mount`而不是`shallowMount`：

```js
import { mount } from "@vue/test-utils";
import MessageList from "../src/components/MessageList";
describe("MessageList.test.js", () => {
  let cmp;
  beforeEach(() => {
    cmp = mount(MessageList, {
      // Be aware that props is overridden using 'propsData'
      propsData: {
        messages: ["Cat"]
      }
    });
  });
  it('has received ["Cat"] as the message property', () => {
    expect(cmp.vm.messages).toEqual(["Cat"]);
  });
  it("has the expected html structure", () => {
    expect(cmp.element).toMatchSnapshot();
  });
});
```

顺便说一句，你有没有注意到`beforeEach`这个东西？这是一种非常干净的方式，在每个测试之前创建一个干净的组件，这在单元测试中非常重要，因为它定义了测试不应该相互依赖。

`mount`和`shallowMount`使用完全相同的 API；区别在于渲染。随着我们在本系列中的进展，我会逐渐向你展示更多的 API。

如果你运行`npm t`，你会看到测试失败，因为快照与`MessageList.test.js`不匹配。要重新生成它，请使用`-u`选项运行：

```js
npm t -- -u
```

然后，如果你打开并检查`test/__snapshots__/MessageList.test.js.snap`，你会看到`class="message"`在那里，这意味着组件已经被渲染：

```js
// Jest Snapshot v1, https://goo.gl/fbAQLP
exports['MessageList.test.js has the expected html structure 1'] = '
<ul>
  <li class="message">
    Cat
  </li>
</ul>
';
```

请记住，在可能存在副作用的情况下避免深度渲染，因为子组件的钩子，比如`created`和`mount`，将被触发，那里可能会有 HTTP 调用或其他副作用，我们不希望被调用。如果你想尝试一下我说的话，可以在`Message.vue`组件的`created`钩子中添加一个`console.log`：

```js
export default {
  props: ["message"],
  created() {
    console.log("CREATED!");
  }
};
```

然后，如果你再次用`npm t`运行测试，你会在终端输出中看到`"CREATED!"`文本。所以，要小心。

您可以在 GitHub 上找到本章的代码和示例（[`github.com/alexjoverm/vue-testing-series/tree/Test-fully-rendered-Vue-js-Components-in-Jest`](https://github.com/alexjoverm/vue-testing-series/tree/Test-fully-rendered-Vue-js-Components-in-Jest)）。
