# 第十二章：使用 Vue Dev Tools 和测试您的 SPA

在过去的 11 章中，我们使用`Vue.js`开发了几个**单页应用程序**（**SPAs**）。尽管开发是创建 SPA 的重要部分，但测试也是创建任何 JavaScript Web 应用程序的重要组成部分。

Vue 开发者工具在 Chrome 和 Firefox 中提供了对在某个视图中使用的组件或 Vuex 存储的当前状态的深入洞察 - 以及从 JavaScript 中发出的任何事件。这些工具允许您在开发过程中检查和验证应用程序中的数据，以确保一切都正常。

SPA 测试的另一方面是自动化测试。您编写的条件、规则和路由用于自动化应用程序中的任务，允许您指定输出应该是什么，并且测试运行条件以验证结果是否匹配。

在本章中，我们将：

+   涵盖使用我们开发的应用程序的 Vue 开发者工具的使用

+   了解测试工具和应用程序的概述

# 使用 Vue.js 开发者工具

Vue 开发者工具适用于 Chrome 和 Firefox，并可以从 GitHub（[`github.com/vuejs/vue-devtools`](https://github.com/vuejs/vue-devtools)）下载。安装后，它们成为浏览器开发者工具的扩展。例如，在 Chrome 中，它们出现在审核标签之后。

![](img/e359dd13-20e7-4814-83e5-39484009bdff.png)

只有在使用 Vue 的开发模式时，Vue 开发者工具才能正常工作。默认情况下，未压缩版本的 Vue 已启用开发模式。然而，如果您使用的是代码的生产版本，则可以通过在代码中将`devtools`变量设置为`true`来启用开发工具：

```js
Vue.config.devtools = true
```

在整本书中，我们一直使用的是 Vue 的开发版本，所以开发工具应该可以与我们开发的所有三个单页应用程序一起使用。打开 Dropbox 示例并打开 Vue 开发者工具。

# 检查 Vue 组件的数据和计算值

Vue 开发者工具提供了对页面上使用的组件的很好的概述。您还可以深入到组件中，并预览该特定实例中使用的数据。这非常适合在任何给定时间检查页面上每个组件的属性。

例如，如果我们检查 Dropbox 应用程序并导航到组件选项卡，我们可以看到<Root> Vue 实例和<DropboxViewer>组件。点击这个将显示组件的所有数据属性 - 以及任何计算属性。这样我们就可以验证结构是否正确构建，以及计算路径属性：

![](img/fd41fc73-2502-42c2-b4ec-218588b0bd34.png)

深入研究每个组件，我们可以访问单个数据对象和计算属性。

使用 Vue 开发者工具来检查应用程序是一种更高效的验证数据的方式，因为它可以避免使用多个`console.log()`语句。

# 查看 Vuex 的 mutations 和时间旅行

导航到下一个选项卡 Vuex，可以实时观察存储变化的发生。每次发生变化时，左侧面板中都会创建一行。这个元素允许我们查看发送的数据以及数据提交之前和之后的 Vuex 存储的样子。

它还提供了几个选项来还原、提交和时间旅行到任何点。加载 Dropbox 应用程序后，左侧面板中立即出现几个结构变化，列出了变化的名称和发生的时间。这是预缓存文件夹的代码。点击每个变化将显示 Vuex 存储状态 - 以及包含的负载的变化。状态显示是在负载发送和变化提交之后。要预览在该变化之前状态的样子，选择前面的选项：

![](img/ea5f9e57-a1da-4dbb-bb62-aa744d97cc83.png)

在每个条目旁边，您会注意到三个符号，允许您执行几个操作并直接在浏览器中改变存储：

+   **提交此变化**：这允许您提交到该点的所有数据。这将从开发工具中*删除*所有的变化，并将基本状态更新到此点。如果有多个变化发生，您希望跟踪它们，这将非常方便。

+   **撤销此突变**：这将撤销该突变和此点之后的所有突变。这样可以让你一遍又一遍地执行相同的操作，而无需刷新或丢失当前位置。例如，在我们的商店应用程序中将产品添加到购物篮时，会发生突变。使用此功能可以让你从购物篮中删除产品并撤销任何后续的突变，而无需离开产品页面。

+   **时间旅行到此状态**：这允许您预览应用程序和该特定突变时的状态，而不会撤销所选点之后发生的任何突变。

突变选项卡还允许您在左侧面板顶部提交或撤销所有突变。在右侧面板中，您还可以导入和导出存储状态的 JSON 编码版本。当您想要重新测试多种情况和实例而无需重现多个步骤时，这非常方便。

# 预览事件数据

Vue 开发者工具的事件选项卡与 Vuex 选项卡的工作方式类似，允许您检查应用程序中发出的任何事件。我们的 Dropbox 应用程序不使用事件，因此打开我们在本书的第二章“显示、循环、搜索和过滤数据”中创建的 people-filtering 应用程序，以及本书的第三章“优化我们的应用程序并使用组件显示数据”。

更改此应用程序中的过滤器会在每次更新过滤器类型时发出一个事件，以及过滤器查询：

![](img/f7219298-cb16-4f17-9c40-750c9b9eeb1b.png)

左侧面板再次列出了事件的名称和发生的时间。右侧面板包含有关事件的信息，包括其组件来源和有效负载。这些数据可以确保事件数据与您预期的一样，并且如果不是，可以帮助您找到触发事件的位置。

Vue 开发工具是非常宝贵的，特别是当你的 JavaScript 应用程序变得越来越大和复杂时。打开我们开发的商店 SPA，检查各个组件和 Vuex 数据，了解这个工具如何帮助你创建只提交所需的突变并发出所需的事件的应用程序。

# 测试你的 SPA

大多数 Vue 测试套件都围绕着具备命令行知识并使用**CLI**（**命令行界面**）创建 Vue 应用程序。除了使用前端兼容的 JavaScript 创建应用程序外，Vue 还有一个 CLI，允许您使用基于组件的文件创建应用程序。这些文件具有`.vue`扩展名，包含模板 HTML 以及组件所需的 JavaScript。它们还允许您创建作用域 CSS-仅适用于该组件的样式。如果选择使用 CLI 创建应用程序，则本书中学到的所有理论和大部分实践知识都可以轻松移植过来。

# 命令行单元测试

除了组件文件，Vue CLI 还允许您更轻松地集成命令行单元测试，例如 Jest、Mocha、Chai 和 TestCafe（[`testcafe.devexpress.com/`](https://testcafe.devexpress.com/)）。例如，TestCafe 允许您指定多个不同的测试，包括检查内容是否存在，点击按钮以测试功能等。一个 TestCafe 测试的示例是检查我们第一个应用程序中的过滤组件是否包含单词*Field*：

```js
test('The filtering contains the word "filter"', async testController => {
  const filterSelector = await new Selector('body > #app > form > label:nth-child(1)');

  await testController.expect(paragraphSelector.innerText).eql('Filter');
});
```

这个测试将返回`true`或`false`。单元测试通常与组件本身一起编写，允许组件在隔离环境中被重用和测试。这样可以确保外部因素对测试结果没有影响。

大多数命令行 JavaScript 测试库都可以与 Vue.js 集成；在 awesome Vue GitHub 存储库（[`github.com/vuejs/awesome-vue#test`](https://github.com/vuejs/awesome-vue#test)）中有一个很棒的列表可用。

# 浏览器自动化

使用命令行单元测试的替代方法是使用测试套件自动化浏览器。这种测试仍然通过命令行触发，但不是直接与 Vue 应用程序集成，而是在浏览器中打开页面并像用户一样与之交互。一个常用的工具是`Nightwatch.js`（[`nightwatchjs.org/`](http://nightwatchjs.org/)）。

您可以使用这个套件来打开您的商店，并与过滤组件或产品列表排序进行交互，并比较结果。这些测试用例使用非正式的英语编写，并不限于与要测试的站点在同一域名或文件网络上。该库也是语言无关的，适用于任何网站，无论它是用什么构建的。

`Nightwatch.js`在他们的网站上给出的示例是打开 Google 并确保`rembrandt van rijn`的谷歌搜索结果的第一个结果是维基百科条目：

```js
module.exports = {
  'Demo test Google' : function (client) {
    client
      .url('http://www.google.com')
      .waitForElementVisible('body', 1000)
      .assert.title('Google')
      .assert.visible('input[type=text]')
      .setValue('input[type=text]', 'rembrandt van rijn')
      .waitForElementVisible('button[name=btnG]', 1000)
      .click('button[name=btnG]')
      .pause(1000)
      .assert.containsText('ol#rso li:first-child',
        'Rembrandt - Wikipedia')
      .end();
  }
};
```

Nightwatch 的替代品是 Selenium（[`www.seleniumhq.org/`](http://www.seleniumhq.org/)）。 Selenium 的优点是有一个 Firefox 扩展，可以让您可视化地创建测试和命令。

测试，特别是对于大型应用程序来说，是至关重要的，尤其是在将应用程序部署到开发环境时。无论您选择单元测试还是浏览器自动化，都有大量关于这个主题的文章和书籍可供参考。

# 摘要

到目前为止，我们创建了一个模拟商店。使用 Shopify CSV 文件中的真实数据，我们创建了一个允许单独查看产品的应用程序。我们还创建了一个可以进行过滤和排序的类别列表页面，使用户可以找到他们想要的特定产品。为了完善体验，我们构建了一个可编辑的购物篮、结账和订单确认屏幕。在本章中，我们介绍了 Vue 开发工具的使用，以及如何构建测试。
