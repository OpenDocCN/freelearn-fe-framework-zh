# 第七章：测试 Vue.js 插槽

插槽是在 Web 组件世界中进行内容分发的一种方式。Vue.js 插槽是根据**Web 组件规范**制作的（[`github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md`](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md)），这意味着如果你学会了如何在 Vue.js 中使用它们，它们将对你以后很有用。

它们使组件的结构更加灵活，将管理状态的责任转移到父组件。例如，我们可以有一个`List`组件，以及不同类型的项目组件，比如`ListItem`和`ListItemImage`。它们将被如下使用：

```js
<template>
  <List>
    <ListItem :someProp="someValue" />
    <ListItem :someProp="someValue" />
    <ListItemImage :image="imageUrl" :someProp="someValue" />
  </List>
</template>
```

`List`的内部内容就是插槽本身，可以通过`<slot>`标签访问。因此，`List`的实现如下：

```js
<template>
  <ul>
    <!-- slot here will equal to what's inside <List> -->
    <slot></slot>
  </ul>
</template>
```

现在，假设`ListItem`组件如下所示：

```js
<template>
  <li> {{ someProp }} </li>
</template>
```

然后，Vue.js 渲染的最终结果将是：

```js
<ul>
  <li> someValue </li>
  <li> someValue </li>
  <li> someValue </li> <!-- assume the same implementation for ListItemImage -->
</ul>
```

# 使 MessageList 基于插槽

现在，让我们来看看`MessageList.vue`组件：

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
```

`MessageList`在内部*硬编码*了`Message`组件。在某种程度上，这更加自动化，但在另一方面，它完全缺乏灵活性。如果你想要不同类型的`Message`组件怎么办？改变它们的结构或样式呢？这就是插槽派上用场的地方。

现在，让我们将`Message.vue`更改为使用插槽。首先，将`<Message...`部分移动到`App.vue`组件中，连同`handleMessageClick`方法，以便在外部使用：

```js
<template>
  <div id="app">
    <MessageList>
      <Message
        @message-clicked="handleMessageClick"
        :message="message"
        v-for="message in messages"
        :key="message"/>
    </MessageList>
  </div>
</template>
<script>
import MessageList from "./components/MessageList";
import Message from "./components/Message";
export default {
  name: "app",
  data: () => ({ messages: ["Hey John", "Howdy Paco"] }),
  methods: {
    handleMessageClick(message) {
      console.log(message);
    }
  },
  components: {
    MessageList,
    Message
  }
};
</script>
```

不要忘记导入`Message`组件，并将其添加到`App.vue`中的`components`选项中。

然后，在`MessageList.vue`中，我们可以删除对`Message`的引用。现在看起来如下：

```js
<template>
  <ul class="list-messages">
    <slot></slot>
  </ul>
</template>
<script>
export default {
  name: "MessageList"
};
</script>
```

# $children 和$slots

Vue 组件有两个实例变量，对于访问插槽非常有用：

+   `$children`：默认插槽的 Vue 组件实例数组

+   `$slots`：一个 VNodes 对象，映射了组件实例中定义的所有插槽

`$slots`对象有更多可用的数据。实际上，`$children`只是`$slots`变量的一部分，可以通过在`$slots.default`数组上进行映射，通过 Vue 组件实例进行过滤来访问相同的方式：

```js
const children = this.$slots.default
  .map(vnode => vnode.componentInstance)
  .filter(cmp => !!cmp);
```

# 测试插槽

我们可能最想测试的插槽方面是它们在组件中的位置，为此，我们可以重复在*第三章*中学到的技能，*在 Jest 中测试 Vue.js 组件的样式和结构*。

现在，`MessageList.test.js`中的大多数测试都会失败，所以让我们移除它们（或者将它们注释掉），并专注于插槽测试。

我们可以测试的一件事是确保`Message`组件最终出现在具有`list-messages`类的`ul`元素中。为了将插槽传递给`MessageList`组件，我们可以使用`mount`或`shallowMount`方法的`options`对象的`slots`属性。因此，让我们创建一个`beforeEach`方法（[`jestjs.io/docs/en/api.html#beforeeachfn-timeout`](https://jestjs.io/docs/en/api.html#beforeeachfn-timeout)），其中包含以下代码：

```js
beforeEach(() => {
  cmp = shallowMount(MessageList, {
    slots: {
      default: '<div class="fake-msg"></div>'
    }
  });
});
```

由于我们只想测试消息是否被渲染，我们可以按如下方式搜索`<div class="fake-msg"></div>`：

```js
it("Messages are inserted in a ul.list-messages element", () => {
  const list = cmp.find("ul.list-messages");
  expect(list.findAll(".fake-msg").length).toBe(1);
});
```

然后就可以进行了。插槽选项还接受组件声明，甚至是数组，所以我们可以编写以下内容：

```js
import AnyComponent from "anycomponent";
shallowMount(MessageList, {
  slots: {
    default: AnyComponent // or [AnyComponent, AnyComponent]
  }
});
```

这种方法的问题在于它非常有限；例如，您不能覆盖 props，而我们需要为`Message`组件做到这一点，因为它有一个必需的属性。这应该影响到您真正需要测试预期组件的插槽的情况；例如，如果您想确保`MessageList`只期望`Message`组件作为插槽。这是在正确的轨道上，并且在某个时候，它将出现在`vue-test-utils`中（[`github.com/vuejs/vue-test-utils/issues/41#issue-255235880`](https://github.com/vuejs/vue-test-utils/issues/41#issue-255235880)）。

作为一种解决方法，我们可以通过使用**渲染函数**（[`vuejs.org/v2/guide/render-function.html`](https://vuejs.org/v2/guide/render-function.html)）来实现这一点。因此，我们可以重写测试以更具体：

```js
beforeEach(() => {
  const messageWrapper = {
    render(h) {
      return h(Message, { props: { message: "hey" } });
    }
  };
  cmp = shallowMount(MessageList, {
    slots: {
      default: messageWrapper
    }
  });
});
it("Messages are inserted in a MessageList component", () => {
  const list = cmp.find(MessageList);
  expect(list.find(Message).isVueInstance()).toBe(true);
});
```

# 测试命名插槽

我们之前使用的未命名插槽称为*默认插槽*，但我们可以通过使用命名插槽来拥有多个插槽。现在让我们给`MessageList.vue`组件添加一个标题：

```js
<template>
  <div>
    <header class="list-header">
      <slot name="header">
        This is a default header
      </slot>
    </header>
    <ul class="list-messages">
      <slot></slot>
    </ul>
  </div>
</template>
```

通过使用`<slot name="header">`，我们为标题定义了另一个插槽。您可以在插槽中看到`这是一个默认标题`文本。当未传递插槽给组件时，这将显示为默认内容，并且适用于默认插槽。

然后，从`App.vue`中，我们可以通过使用`slot="header"`属性为`MessageList`组件添加一个标题：

```js
<template>
  <div id="app">
    <MessageList>
      <header slot="header">
        Awesome header
      </header>
      <Message
        @message-clicked="handleMessageClick"
        :message="message"
        v-for="message in messages"
        :key="message"/>
    </MessageList>
  </div>
</template>
```

现在是时候为它编写一个单元测试了。测试命名插槽就像测试默认插槽一样；相同的动态适用。因此，我们可以首先验证标题插槽是否在`<header class="list-header">`元素内呈现，并且在没有传递标题插槽时呈现默认文本。在`MessageList.test.js`中，我们有以下内容：

```js
it("Header slot renders a default header text", () => {
  const header = cmp.find(".list-header");
  expect(header.text().trim()).toBe("This is a default header");
});
```

然后，相同的，但是在我们模拟`header`插槽时检查默认内容是否被替换：

```js
it("Header slot is rendered withing .list-header", () => {
  const component = shallowMount(MessageList, {
    slots: {
      header: "<div>What an awesome header</div>"
    }
  });
  const header = component.find(".list-header");
  expect(header.text().trim()).toBe("What an awesome header");
});
```

我们可以看到在最后一个测试中使用的标题插槽被包裹在`<div>`中。插槽被包裹在 HTML 标记中非常重要，否则`vue-test-utils`会抱怨。

# 测试上下文插槽规范

我们已经测试了插槽的渲染方式和位置，这可能是最重要的方面。然而，事情并不止于此。如果您将组件实例作为插槽传递，就像我们在`Message`默认插槽中所做的那样，您可以测试与它们相关的功能。

在这里要小心测试什么。这在大多数情况下可能是您不需要做的事情，因为组件的功能测试应该属于该组件的测试范畴。当谈到测试插槽功能时，我们测试插槽在*使用该插槽的组件的上下文中*应该如何行为，这是不太常见的。通常，我们只是传递插槽然后忘记它。所以，不要对下面的示例过于执着 - 它的唯一目的是演示工具的工作原理。

假设出于某种原因，在`MessageList`组件的上下文中，所有`Message`组件的长度都必须大于 5。我们可以这样测试：

```js
it("Message length is higher than 5", () => {
  const messages = cmp.findAll(Message);
  messages.wrappers.forEach(c => {
    expect(c.vm.message.length).toBeGreaterThan(5);
  });
});
```

`findAll`返回一个包含`wrappers`数组的对象，我们可以访问其`vm`组件实例属性。这个测试将失败，因为消息的长度为 3，所以去`beforeEach`函数并使其更长：

```js
beforeEach(() => {
  const messageWrapper = {
    render(h) {
      return h(Message, { props: { message: "hey yo" } });
    }
  };
});
```

然后，它应该通过。

# 总结

测试插槽非常简单。通常，我们希望测试它们是否按照我们的期望放置和渲染，因此这就像测试样式和结构一样，了解插槽的行为或可以进行模拟。您很可能不需要经常测试插槽功能。

请记住，只有在想要测试插槽并且三思而后行时，才应该测试与插槽相关的事物是否属于插槽测试还是组件测试本身。

您可以在 **GitHub** 上找到与本章相关的代码（[`github.com/alexjoverm/vue-testing-series/tree/test-slots`](https://github.com/alexjoverm/vue-testing-series/tree/test-slots)）。
