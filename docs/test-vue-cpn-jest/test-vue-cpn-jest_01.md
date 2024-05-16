# 第一章：在 Jest 中编写第一个 Vue.js 组件单元测试

官方的 VueJS 测试库，**vue-test-utils** ([`github.com/vuejs/vue-test-utils`](https://github.com/vuejs/vue-test-utils))，基于 **avoriaz** ([`github.com/eddyerburgh/avoriaz`](https://github.com/eddyerburgh/avoriaz))，即将推出。事实上，**@EddYerburgh** ([`twitter.com/EddYerburgh`](https://twitter.com/EddYerburgh)) 在创建它方面做得非常好。这个库提供了所有必要的工具，使得在 VueJS 应用程序中编写单元测试变得容易。

**Jest** ([`facebook.github.io/jest`](https://facebook.github.io/jest))，另一方面，是 Facebook 开发的测试框架，使用一些令人惊叹的功能使得测试变得轻而易举，包括以下内容：

+   几乎没有默认配置

+   非常酷的交互模式

+   并行运行测试

+   开箱即用的间谍、存根和模拟测试

+   内置代码覆盖

+   快照测试

+   模块模拟工具

你可能已经在不使用这些工具的情况下编写了测试，只是使用 Karma、Mocha、Chai、Sinon 等，但你会看到使用这些工具会更容易。

# 设置一个 vue-test 示例项目

让我们通过使用 `vue-cli` ([`github.com/vuejs/vue-cli`](https://github.com/vuejs/vue-cli)) 创建一个新项目，并对所有 yes/no 问题回答 NO：

```js
npm install -g vue-cli
vue init webpack vue-test
cd vue-test
```

然后，我们需要安装一些依赖，如下所示：

```js
# Install dependencies
npm i -D jest vue-jest babel-jest
```

`jest-vue-preprocessor` ([`github.com/vire/jest-vue-preprocessor`](https://github.com/vire/jest-vue-preprocessor)) 是必需的，让 Jest 理解 `.vue` 文件，而 `babel-jest` ([`github.com/facebook/jest/tree/master/packages/babel-jest`](https://github.com/facebook/jest/tree/master/packages/babel-jest)) 是与 Babel 集成所必需的。

现在安装 'vue-test-utils' 库。

```js
npm i -D @vue/test-utils
```

让我们在 `package.json` 中添加以下 Jest 配置：

```js
{
  "jest": {
    "moduleNameMapper": {
      "^vue$": "vue/dist/vue.common.js"
    },
    "moduleFileExtensions": ["js", "vue"],
    "transform": {
      "^.+\\.js$": "<rootDir>/node_modules/babel-jest",
      ".*\\.(vue)$": "<rootDir>/node_modules/vue-jest"
    }
  }
}
```

`moduleFileExtensions` 将告诉 Jest 要查找哪些扩展名，而 `transform` 将告诉 Jest 要使用哪个预处理器来处理文件扩展名。

最后，在 `package.json` 中添加一个 `test` 脚本：

```js
{
  "scripts": {
    "test": "jest"
  }
}
```

# 测试一个组件

我将在这里使用单文件组件，并且我还没有检查它们是否可以分割成它们自己的 `HTML`、`CSS` 或 `js` 文件，所以让我们假设你也在这样做。

首先，在 `src/components` 下创建一个 `MessageList.vue` 组件：

```js
<template>
  <ul>
    <li v-for="message in messages">
      {{ message }}
    </li>
  </ul>
</template>
<script>
  export default {
    name: "list",
    props: ["messages"]
  };
</script>
```

然后更新 `App.vue` 如下使用它：

```js
<template>
  <div id="app">
    <MessageList :messages="messages" />
  </div>
</template>
<script>
  import MessageList from "./components/MessageList";
  export default {
    name: "app",
    data: () => ({ messages: ["Hey John", "Howdy Paco"] }),
    components: {
      MessageList
    }
  };
</script>
```

我们已经有了一些可以测试的组件。让我们在项目根目录下创建一个`test`文件夹和一个`App.test.js`文件：

```js
import Vue from "vue";
import App from "../src/App";
describe("App.test.js", () => {
  let cmp, vm;
  beforeEach(() => {
    cmp = Vue.extend(App); // Create a copy of the original component
    vm = new cmp({
      data: {
        // Replace data value with this fake data
        messages: ["Cat"]
      }
    }).$mount(); // Instances and mounts the component
  });
  it('equals messages to ["Cat"]', () => {
    expect(vm.messages).toEqual(["Cat"]);
  });
});
```

现在，如果我们运行`npm test`（或`npm t`作为缩写版本），测试应该会运行并通过。由于我们正在修改测试，让我们以**watch 模式**运行它：

```js
npm t -- --watch
```

## 嵌套组件的问题

这个测试太简单了。让我们也检查一下输出是否符合预期。为此，我们可以使用 Jest 的惊人快照功能，它将生成输出的快照并与即将到来的运行进行比较。在`App.test.js`中的前一个`it`之后添加：

```js
it("has the expected html structure", () => {
  expect(cmp.element).toMatchSnapshot();
});
```

这将创建一个`test/__snapshots__/App.test.js.snap`文件。让我们打开并检查它：

```js
// Jest Snapshot v1, https://goo.gl/fbAQLP
exports['App.test.js has the expected html structure 1'] = '
<div id="app">
  <ul>
    <li>
      Cat
    </li>
  </ul>
</div>
';
```

如果您对快照不太了解，不用担心；我将在*第九章*，*快照测试*中更深入地介绍它。

如果您还没有注意到，这里有一个大问题：`MessageList`组件也已被渲染。**单元测试**必须作为**独立单元**进行测试，这意味着在`App.test.js`中，我们要测试`App`组件，而不必关心其他任何东西。

这可能是几个问题的原因。例如，想象一下，子组件（在这种情况下是`MessageList`）在`created`钩子上执行副作用操作，比如调用`fetch`，有一个 Vuex 动作，或者状态改变。这绝对不是我们想要的。

幸运的是，**浅渲染**很好地解决了这个问题。

## 什么是浅渲染？

**浅渲染**（[`airbnb.io/enzyme/docs/api/shallow.html`](http://airbnb.io/enzyme/docs/api/shallow.html)）是一种确保您的组件在没有子组件的情况下进行渲染的技术。这对于以下情况很有用：

+   只测试您想要测试的组件（这就是单元测试的含义）

+   避免子组件可能产生的副作用，比如发起 HTTP 调用，调用存储操作等

# 使用 Vue-Test-Utils 测试组件

`vue-test-utils`为我们提供了浅渲染，以及其他功能。我们可以将上一个测试重写如下：

```js
import { shallowMount } from "@vue/test-utils";
import App from "../src/App";
describe("App.test.js", () => {
  let cmp;
  beforeEach(() => {
    cmp = shallowMount(App, {
      // Create a shallow instance of the component
      data: {
        messages: ["Cat"]
      }
    });
  });
  it('equals messages to ["Cat"]', () => {
    // Within cmp.vm, we can access all Vue instance methods
    expect(cmp.vm.messages).toEqual(["Cat"]);
  });
  it("has the expected html structure", () => {
    expect(cmp.element).toMatchSnapshot();
  });
});
```

现在，如果您仍在以`watch`模式运行 Jest，您会发现测试仍然通过，但快照不匹配。按下*u*重新生成它。然后，再次打开并检查它：

```js
// Jest Snapshot v1, https://goo.gl/fbAQLP
exports['App.test.js has the expected html structure 1'] = '
<div id="app">
  <!--  -->
</div>
';
```

你看到了吗？现在，没有子组件被渲染，我们完全隔离地测试了`App`组件。此外，如果子组件中有任何`created`或其他钩子，它们也没有被调用。

如果你对*浅渲染的实现方式*感兴趣，可以查看**源代码**（[`github.com/vuejs/vue-test-utils/blob/dev/packages/test-utils/src/shallow-mount.js`](https://github.com/vuejs/vue-test-utils/blob/dev/packages/test-utils/src/shallow-mount.js)），你会发现它基本上是对`components`键、`render`方法和生命周期钩子进行存根处理。

在同样的思路下，你可以按照以下方式实现`MessageList.test.js`测试：

```js
import { mount } from '@vue/test-utils'
import MessageList from '../src/components/MessageList'
describe('MessageList.test.js', () => {
  let cmp
  beforeEach(() => {
    cmp = mount(MessageList, {
      // Be aware that props is overridden using 'propsData'
      propsData: {
        messages: ['Cat']
      }
    })
  })
  it('has received ['Cat'] as the message property', () => {
    expect(cmp.vm.messages).toEqual(['Cat'])
  })
  it('has the expected html structure', () => {
    expect(cmp.element).toMatchSnapshot()
  })
})
```

你可以在**GitHub**上找到本章的完整示例（[`github.com/alexjoverm/vue-testing-series/tree/lesson-1`](https://github.com/alexjoverm/vue-testing-series/tree/lesson-1)）。
