# 第六章：测试方法和模拟依赖

在方法中我们应该测试什么？这是我们开始进行单元测试时遇到的问题。一切归结为*测试该方法做了什么，只有那个*。这意味着我们需要*避免调用任何依赖项*，因此我们需要对它们进行模拟。

让我们在上一章中创建的`Form.vue`组件中的表单中添加一个`submit`事件：

```js
<form @submit.prevent="onSubmit(inputValue)"></form>
```

`.prevent`修饰符只是一种方便的方式来调用`event.preventDefault()`，以便它不会重新加载页面。现在，进行一些修改来调用 API，然后通过向数据添加一个`results`数组和一个`onSubmit`方法来存储结果：

```js
export default {
  data: () => ({
    inputValue: "",
    results: []
  }),
  methods: {
    onSubmit(value) {
      axios
        .get("https://jsonplaceholder.typicode.com/posts?q=" + value)
        .then(results => {
          this.results = results.data;
        });
    }
  }
};
```

在这里，该方法使用`axios`执行对`jsonplaceholder`的**posts**端点的 HTTP 调用，这只是这种示例的 RESTful API。另外，通过`q`查询参数，我们可以使用提供的`value`参数搜索帖子。

用于测试`onSubmit`方法：

+   我们不希望调用`axios.get`实际方法。

+   我们希望检查它是否调用了 axios（但不是真正的 axios），并且它返回一个`promise`。

+   该`promise`回调应将`this.results`设置为承诺的结果。

当您有外部依赖项以及返回承诺并在其中执行操作时，这可能是最难测试的事情之一。我们需要做的是**模拟外部依赖项**。

# 模拟外部模块依赖

Jest 提供了一个非常好的模拟系统，可以让您以非常方便的方式模拟所有内容。事实上，您不需要任何额外的库来做到这一点。我们已经看到了`jest.spyOn`和`jest.fn`用于监视和创建存根函数，尽管这对于这种情况还不够。

在这里，我们需要模拟整个`axios`模块。这就是`jest.mock`发挥作用的地方。它允许我们通过在文件顶部编写来轻松模拟模块依赖项：

```js
jest.mock("dependency-path", implementationFunction);
```

您必须知道`jest.mock`*是被提升的*，这意味着它将被放置在顶部：

```js
jest.mock("something", jest.fn);
import foo from "bar";
// ...
```

因此，前面的代码等效于这个：

```js
import foo from "bar";
jest.mock("something", jest.fn); // this will end up above all imports and everything
// ...
```

在撰写本文时，我仍然没有在互联网上找到有关如何在 Jest 中执行我们将在这里执行的操作的信息。幸运的是，您不必经历同样的挣扎。

让我们在`Form.test.js`测试文件的顶部编写`axios`的模拟和相应的测试用例：

```js
jest.mock("axios", () => ({
  get: jest.fn()
}));
import { shallowMount } from "@vue/test-utils";
import Form from "../src/components/Form";
import axios from "axios"; // axios here is the mock from above!
// ...
it("Calls axios.get", () => {
  cmp.vm.onSubmit("an");
  expect(axios.get).toBeCalledWith(
    "https://jsonplaceholder.typicode.com/posts?q=an"
  );
});
```

这很棒。我们确实在模拟`axios`，所以原始的 axios 没有被调用，也没有任何 HTTP 被调用。而且我们甚至通过使用`toBeCalledWith`来检查它是否被正确地调用了。然而，我们仍然缺少一些东西：*我们没有检查它是否返回了* `promise`。

首先，我们需要让我们模拟的`axios.get`方法返回一个`promise`。`jest.fn`接受一个工厂函数作为参数，所以我们可以用它来定义它的实现：

```js
jest.mock("axios", () => ({
  get: jest.fn(() => Promise.resolve({ data: 3 }))
}));
```

然而，我们仍然无法访问`promise`，因为我们没有返回它。在测试中，尽可能从函数中返回一些东西是一个好习惯，因为这样测试会更容易。所以，现在让我们在`Form.vue`组件的`onSubmit`方法中做这个：

```js
export default {
  methods: {
    // ...
    onSubmit(value) {
      const getPromise = axios.get(
        "https://jsonplaceholder.typicode.com/posts?q=" + value
      );
      getPromise.then(results => {
        this.results = results.data;
      });
      return getPromise;
    }
  }
};
```

然后，我们可以在测试中使用非常干净的 ES2017 `async/await`语法来检查 promise 的结果：

```js
it("Calls axios.get and checks promise result", async () => {
  const result = await cmp.vm.onSubmit("an");
  expect(result).toEqual({ data: [3] });
  expect(cmp.vm.results).toEqual([3]);
  expect(axios.get).toBeCalledWith(
    "https://jsonplaceholder.typicode.com/posts?q=an"
  );
});
```

在这里，你可以看到我们不仅检查了 promise 的结果，还检查了组件的`results`内部状态是否按预期更新，通过`expect(cmp.vm.results).toEqual([3])`。

# 保持模拟外部化

Jest 允许我们将所有的模拟分开放在自己的 JavaScript 文件中，将它们放在`__mocks__`文件夹下，并尽可能保持测试的干净。

因此，我们可以将`Form.test.js`文件顶部的`jest.mock...`块移到自己的文件中：

```js
// test/__mocks__/axios.js
module.exports = {
  get: jest.fn(() => Promise.resolve({ data: [3] }))
};
```

就像这样，而且不需要额外的努力，Jest 会自动在所有的测试中应用模拟，这样我们就不必做任何额外的事情，或者在每个测试中手动模拟它。请注意，模块名称必须与文件名匹配。如果再次运行测试，它们应该仍然通过。

请记住，模块注册表和模拟状态保持不变，因此，如果之后再编写另一个测试，可能会得到不良的结果：

```js
it("Calls axios.get", async () => {
  const result = await cmp.vm.onSubmit("an");
  expect(result).toEqual({ data: [3] });
  expect(cmp.vm.results).toEqual([3]);
  expect(axios.get).toBeCalledWith(
    "https://jsonplaceholder.typicode.com/posts?q=an"
  );
});
it("Axios should not be called here", () => {
  expect(axios.get).toBeCalledWith(
    "https://jsonplaceholder.typicode.com/posts?q=an"
  );
});
```

第二个测试应该失败，但它没有。那是因为在测试之前调用了`axios.get`。

因此，清理模块注册表和模拟是一个好习惯，因为它们是由 Jest 操纵的，以便进行模拟。为此，你可以在`beforeEach`中添加：

```js
beforeEach(() => {
  cmp = shallowMount(Form);
  jest.resetModules();
  jest.clearAllMocks();
});
```

这将确保每个测试都从干净的模拟和模块开始，这在单元测试中应该是这样的。

# 总结

Jest 的模拟功能，以及快照测试，是我最喜欢的两个功能。因为它们使通常很难测试的东西变得非常容易，让你可以专注于编写更快速和更好隔离的测试，保持你的代码库无懈可击。

你可以在**GitHub**上找到本章的所有代码（[`github.com/alexjoverm/vue-testing-series/tree/Test-State-Computed-Properties-and-Methods-in-Vue-js-Components-with-Jest`](https://github.com/alexjoverm/vue-testing-series/tree/Test-State-Computed-Properties-and-Methods-in-Vue-js-Components-with-Jest)）。
