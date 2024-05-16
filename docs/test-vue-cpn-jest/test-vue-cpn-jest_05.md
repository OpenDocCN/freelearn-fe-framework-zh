# 第五章

# 测试计算属性和观察者

计算属性和观察者是 Vue.js 组件逻辑的响应式部分。它们各自具有完全不同的目的，即一个是同步的，另一个是异步的，这使它们的行为略有不同。

在本章中，我们将介绍测试它们的过程，并看看在这一过程中我们可以找到哪些不同的情况。

# 计算属性

计算属性是简单的响应式函数，以另一种形式返回数据。它们的行为与语言标准的`get/set`属性完全相同：

```js
class X {
  get fullName() {
    return `${this.name} ${this.surname}`;
  }
  set fullName(value) {
    // ...
  }
}
```

当您使用普通对象时，情况如下：

```js
export default {
  computed: {
    fullName() {
      return `${this.name} ${this.surname}`;
    }
  }
};
```

您甚至可以添加`set`属性如下：

```js
export default {
  computed: {
    fullName: {
      get() {
        return `${this.name} ${this.surname}`;
      },
      set(value) {
        // ...
      }
    }
  }
};
```

## 测试计算属性

测试计算属性非常简单。有时，您可能不会单独测试计算属性，而是将其作为其他测试的一部分进行测试。但是，大多数情况下最好为其编写测试；无论该计算属性是清理输入还是组合数据，我们都希望确保它按预期工作。所以，让我们开始吧。

首先，创建一个`Form.vue`组件：

```js
<template>
  <div>
    <form>
      <input type="text" v-model="inputValue">
      <span class="reversed">{{ reversedInput }}</span>
    </form>
  </div>
</template>
<script>
export default {
  props: ["reversed"],
  data: () => ({
    inputValue: ""
  }),
  computed: {
    reversedInput() {
      return this.reversed ?
        this.inputValue.split("").reverse().join("") :
        this.inputValue;
    }
  }
};
</script>
```

它将显示一个输入框，旁边是相同的字符串但是反转的。这只是一个愚蠢的例子，但足够测试了。

现在，将其添加到`App.vue`，然后将其放在`MessageList`组件之后，并记得导入它并在`components`组件选项中包含它。然后，创建一个带有我们在其他测试中使用过的基本结构的`test/Form.test.js`文件：

```js
import { shallowMount } from "@vue/test-utils";
import Form from "../src/components/Form";
describe("Form.test.js", () => {
  let cmp;
  beforeEach(() => {
    cmp = shallowMount(Form);
  });
});
```

现在，创建一个包含两个测试用例的测试套件：

```js
describe("Properties", () => {
  it("returns the string in normal order if reversed property is not true", () => {
    cmp.setData({ inputValue: "Yoo" });
    expect(cmp.vm.reversedInput).toBe("Yoo");
  });
  it("returns the reversed string if reversed property is true", () => {
    cmp.setData({ inputValue: "Yoo" });
    cmp.setProps({ reversed: true });
    expect(cmp.vm.reversedInput).toBe("ooY");
  });
});
```

我们可以在`cmp.vm`中访问组件实例，以便访问内部状态、计算属性和方法。然后，测试就是改变值并确保在`reversed`为`false`时返回相同的字符串。

对于第二种情况，几乎相同，唯一的区别是我们必须将`reversed`属性设置为`true`。我们可以通过`cmp.vm...`进行导航来更改它，但`vue-test-utils`给了我们一个辅助方法`setProps({ property: value, ... })`，这使得它非常容易。

就是这样；根据计算属性的不同，可能需要更多的测试用例。

# 观察者

老实说，我还没有遇到过真正需要使用观察者的测试用例，而我的计算属性无法解决的。我也看到它们被滥用，导致组件之间的数据工作流非常不清晰，搞乱了一切。因此，不要急着使用它们，事先考虑一下。

正如您在 Vue.js 文档中所看到的（[`vuejs.org/v2/guide/computed.html#Watchers`](https://vuejs.org/v2/guide/computed.html#Watchers)），观察者经常用于对数据变化做出反应并执行异步操作，比如执行 ajax 请求。

## 测试观察者

假设我们希望在状态中的`inputValue`发生变化时执行某些操作。我们可以执行 ajax 请求，但由于那更复杂（我们将在下一课中更详细地介绍），所以让我们只使用`console.log`函数。在`Form.vue`组件选项中添加一个`watch`属性：

```js
watch: {
  inputValue(newVal, oldVal) {
    if (newVal.trim().length && newVal !== oldVal) {
      console.log(newVal)
    }
  }
}
```

注意`inputValue`观察者函数与状态变量名称匹配。按照惯例，Vue 将通过使用观察者函数名称，在这种情况下是`inputValue`，在`properties`和`data`状态中查找它，并且由于它将在`data`中找到它，它将在那里添加观察者。

请注意，观察者函数将新值作为第一个参数，旧值作为第二个参数。在这种情况下，我们选择仅在值不为空且值不同的情况下记录。通常，我们会根据您的时间和代码的重要性来为每种情况编写测试。

那么，我们应该测试观察者函数的什么内容呢？嗯，这也是我们在下一课中讨论测试方法时会进一步讨论的内容，但让我们假设我们只想知道它在应该调用`console.log`时是否调用。因此，让我们在`Form.test.js`中添加观察者测试套件的基本内容，如下所示：

```js
describe("Form.test.js", () => {
  let cmp;
  describe("Watchers - inputValue", () => {
    let spy;
    beforeAll(() => {
      spy = jest.spyOn(console, "log");
    });
    afterEach(() => {
      spy.mockClear();
    });
    it("is not called if value is empty (trimmed)", () => {
      // TODO
    });
    it("is not called if values are the same", () => {
      // TODO
    });
    it("is called with the new value in other cases", () => {
      // TODO
    });
  });
});
```

在这里，我们使用了`console.log`方法的 spy，在开始任何测试之前对其进行初始化，然后在每个测试之后重置其状态，以便它们从一个干净的 spy 开始。

要测试观察者函数，我们只需要改变被观察的值，这种情况下就是`inputValue`状态。但有一件有趣的事情...让我们从最后一个测试开始：

```js
it("is called with the new value in other cases", () => {
  cmp.vm.inputValue = "foo";
  expect(spy).toBeCalled();
});
```

在这里，我们改变了`inputValue`，所以`console.log` spy 应该被调用，对吗？嗯，它不会被调用。但等等，这有一个解释：与计算属性不同，观察者被*延迟到下一个更新周期*，Vue 用它来查找变化。因此，基本上这里发生的是`console.log`确实被调用，但在测试结束后才被调用。

请注意，我们通过访问`vm`属性以*原始*方式改变了`inputValue`。如果我们想以这种方式做，我们需要使用`vm.$nextTick`（[`vuejs.org/v2/api/#vm-nextTick`](https://vuejs.org/v2/api/#vm-nextTick)）函数将代码推迟到下一个更新周期：

```js
it("is called with the new value in other cases", done => {
  cmp.vm.inputValue = "foo";
  cmp.vm.$nextTick(() => {
    expect(spy).toBeCalled();
    done();
  });
});
```

*请注意这里我们调用了*作为参数接收到的`done` *函数。这是* **Jest 的一种方式** *（[`jestjs.io/docs/en/asynchronous.html`](https://jestjs.io/docs/en/asynchronous.html)）可以测试异步代码。*

然而，有**更好的方法**。`vue-test-utils`给我们的方法，比如`emitted`或`setData`，在幕后处理了这个问题。这意味着最后一个测试可以通过简单地使用`setData`来更清晰地编写：

```js
it("is called with the new value in other cases", () => {
  cmp.setData({ inputValue: "foo" });
  expect(spy).toBeCalled();
});
```

我们也可以对下一个测试应用相同的策略，唯一的区别是间谍不应该被调用：

```js
it("is not called if value is empty (trimmed)", () => {
  cmp.setData({ inputValue: "   " });
  expect(spy).not.toBeCalled();
});
```

最后，测试*如果值相同则不调用*的情况会更复杂一些。默认的内部状态是空的；因此，首先，我们需要改变它，等待下一个时刻，然后清除模拟以重置调用计数，并再次改变它。然后，在第二个时刻之后，我们可以检查间谍并完成测试。

如果我们在开始时重新创建组件，覆盖`data`属性，这将简单得多。请记住，我们可以通过使用`mount`或`shallowMount`函数的第二个参数来覆盖任何组件选项：

```js
it("is not called if values are the same", () => {
  cmp = shallowMount(Form, {
    data: () => ({ inputValue: "foo" })
  });
  cmp.setData({ inputValue: "foo" });
  expect(spy).not.toBeCalled();
});
```

# 总结

在本章中，您已经学会了如何测试 Vue 组件的一部分逻辑：计算属性和观察者。我们已经经历了不同的示例测试用例，这些测试用例在测试它们两者时可能会遇到。您还了解了一些 Vue 内部知识，比如`nextTick`更新周期。

您可以在**GitHub**上找到本章的代码（[`github.com/alexjoverm/vue-testing-series/tree/Test-State-Computed-Properties-and-Methods-in-Vue-js-Components-with-Jest`](https://github.com/alexjoverm/vue-testing-series/tree/Test-State-Computed-Properties-and-Methods-in-Vue-js-Components-with-Jest)）。
