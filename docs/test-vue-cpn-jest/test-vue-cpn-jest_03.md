# 第三章：测试样式和结构

到目前为止，我们使用了**Jest 快照**进行测试（[`facebook.github.io/jest/docs/snapshot-testing.html`](https://facebook.github.io/jest/docs/snapshot-testing.html)）。在大多数情况下，这就是我们会使用的，但有时我们可能想要断言更具体的内容。

虽然你可以通过`cmp.vm`访问 Vue 实例（[`github.com/alexjoverm/vue-testing-series/blob/master/test/MessageList.test.js#L17`](https://github.com/alexjoverm/vue-testing-series/blob/master/test/MessageList.test.js#L17)），但你可以利用一系列工具来更轻松地进行操作。让我们看看我们能做什么。

# 包装对象

`Wrapper`是`vue-test-utils`的主要对象。它是由`mount`，`shallowMount`，`find`和`findAll`函数返回的类型。你可以在**这里**看到整个 API 和类型（[`github.com/vuejs/vue-test-utils/blob/v1.0.0-beta.27/packages/test-utils/types/index.d.ts`](https://github.com/vuejs/vue-test-utils/blob/v1.0.0-beta.27/packages/test-utils/types/index.d.ts)）。

## 查找和查找所有

`find`和`findAll`接受一个**选择器**（[`github.com/vuejs/vue-test-utils/blob/v1.0.0-beta.27/packages/test-utils/types/index.d.ts#L92`](https://github.com/vuejs/vue-test-utils/blob/v1.0.0-beta.27/packages/test-utils/types/index.d.ts#L92)）作为参数，它可以是 CSS 选择器，也可以是 Vue 组件。

因此，我们可以做一些事情，比如：

```js
let cmp = mount(MessageList);
expect(cmp.find(".message").element).toBeInstanceOf(HTMLElement);
// Or even call it multiple times
let el = cmp.find(".message").find("span").element;
// Although for the previous example, we could do it in one
let el = cmp.find(".message span").element;
```

## 断言结构和样式

让我们在`MessageList.test.js`中添加更多测试：

```js
it("is a MessageList component", () => {
  expect(cmp.is(MessageList)).toBe(true);
  // Or with CSS selector
  expect(cmp.is("ul")).toBe(true);
});
it("contains a Message component", () => {
  expect(cmp.contains(Message)).toBe(true);
  // Or with CSS selector
  expect(cmp.contains(".message")).toBe(true);
});
```

在这里，我们使用`is`来断言根组件类型，使用`contains`来检查子组件的存在。就像`find`一样，它们接收一个选择器，可以是 CSS 选择器或组件。

我们有一些工具来断言**Vue 实例**：

```js
it("Both MessageList and Message are vue instances", () => {
  expect(cmp.isVueInstance()).toBe(true);
  expect(cmp.find(Message).isVueInstance()).toBe(true);
});
```

现在我们将更详细地断言**结构**：

```js
it("Message element exists", () => {
  expect(cmp.find(".message").exists()).toBe(true);
});
it("Message is not empty", () => {
  expect(cmp.find(Message).isEmpty()).toBe(false);
});
it('Message has a class attribute set to "message"', () => {
  expect(cmp.find(Message).attributes().class).toBe("message");
});
```

`exists`，`isEmpty`和`attributes`方法对此非常有用。

然后，我们有`classes`和`attributes().style`来断言**样式**。让我们用样式更新`Message.vue`组件，因为`attributes().style`只断言内联样式：

```js
<li style="margin-top: 10px" class="message">{{message}}</li>
```

以下是测试：

```js
it("Message component has the .message class", () => {
  expect(cmp.find(Message).classes()).toContain("message");
});
it("Message component has style padding-top: 10", () => {
  expect(cmp.find(Message).attributes().style).toBe("padding-top: 10px;");
});
```

有一堆工具可以让测试 Vue 组件变得更容易。你可以在**类型文件**中找到它们（[`github.com/vuejs/vue-test-utils/blob/v1.0.0-beta.27/packages/test-utils/types/index.d.ts`](https://github.com/vuejs/vue-test-utils/blob/v1.0.0-beta.27/packages/test-utils/types/index.d.ts)）。

您可以在 **GitHub** 上找到本章的工作代码（[`github.com/alexjoverm/vue-testing-series/blob/Test-Styles-and-Structure-in-Vue-js-and-Jest/test/MessageList.test.js`](https://github.com/alexjoverm/vue-testing-series/blob/Test-Styles-and-Structure-in-Vue-js-and-Jest/test/MessageList.test.js)）。
