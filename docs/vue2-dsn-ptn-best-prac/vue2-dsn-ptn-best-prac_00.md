# 前言

Vue.js 是一个允许您创建高性能 Web 应用程序的 JavaScript 框架。它比竞争对手拥有更小的学习曲线，并且具有详细的文档，其中包含各种示例和用例。Vue.js 应用程序可以使用 Vue CLI 创建，也可以通过`script`标签将其包含在页面中，因此在没有构建系统的项目中使用非常简单。

与其他现代 Web 框架/库类似，Vue.js 是组件驱动的，这意味着您可以创建具有自己视图/数据逻辑的自包含单元。这使得我们的应用程序可以随着规模的扩大而扩展，因为任何更改都可以从彼此中封装起来。

在扩展应用程序时，状态管理是前端领域的热门话题，Vue.js 通过`Vuex`库解决了这个问题。这使我们能够在应用程序中定义操作并相应地行事，为我们提供可预测的状态，可用于构建用户界面和其他交互的基础。

本书探讨了所有这些内容，还提供了如何将这些原则应用于新旧 Vue.js 应用程序的示例。

# 这本书适合谁

这本书适合有兴趣使用 JavaScript 创建 Web 和移动应用程序的人。具有 HTML5/ES2015 的先验经验将有所帮助，因为本书中使用了现代 JavaScript 概念。您可能希望利用这些知识在个人项目或工作角色中创建交互式基于 Web 的体验。

# 本书涵盖的内容

第一章，*Vue.js 原理和比较*，向读者介绍了 Vue.js，并激励他们将其用作创建 Web 应用程序的框架。读者将了解 Vue.js 与其他流行框架（如 React 和 Angular）之间的区别。

第二章，*Vue 项目的正确创建*，探讨了创建 Vue 项目的适当方式。这包括使用 Webpack、Babel/TypeScript（用于 ES6）、`.vue`文件、linting 等。

第三章《使用 Vue 编写干净、精简的代码》深入研究了 Vue.js 实例和不同的保留属性，如`data`、`computed`和`watch`，以及创建 getter 和 setter。本章特别考虑了何时使用计算属性和何时使用观察属性。它还概述了为什么模板应该特别精简以便更容易维护。

第四章《Vue.js 指令》介绍了开发人员在编写响应式 Vue 应用程序时可以访问一组强大指令，如`v-for`、`v-if`、`v-model`等。本章详细介绍了每个指令，以及最佳实践和模式。此外，读者还将介绍使用事件绑定的简写语法。

第五章《使用 Vue.js 组件进行安全通信》更深入地研究了 Vue 组件，包括组件通信。我们将研究传递属性，以及验证属性类型和考虑不同类型的属性和数据流。

第六章《创建更好的 UI》着重介绍了 Vue 的常见 UI 模式。它再次介绍了如何使用`v-model`从用户那里获取输入，以及绑定到文本、复选框、单选按钮等输入的方法。还介绍了下拉框和动态输入。最后，本章涵盖了表单提交和各种修饰符，如惰性绑定、数字类型转换和字符串修剪。

第七章《HTTP 和 WebSocket 通信》介绍了将 HTTP 整合到 Vue.js 应用程序的最佳实践。它介绍了`Axios`以及发送 HTTP 请求的各种方法（即`根实例/组件/导航守卫`）。本章还介绍了使用`socket.io`，以及创建用于实时集成的基本 Node/Express API。

第八章《Vue 路由模式》描述了路由是任何 SPA 的重要组成部分。本章重点介绍了 Vue 路由，并探讨了如何在多个页面之间进行路由。它涵盖了从匹配路径和组件到使用导航参数、正则表达式等动态匹配的所有内容。

第九章，*使用 Vuex 进行状态管理*，演示了使用 Vuex 进行状态管理。首先介绍了 Flux 架构和单向数据流。然后，它介绍了 Vuex，这是 Vue 的状态管理系统。本章还讨论了在应用程序中实现这一点，以及常见的陷阱和使用模式。它还介绍了`Vue-devtools`来捕获操作和 Vue 实例数据。

第十章，*测试 Vue.js 应用程序*，展示了测试是任何项目的重要部分，无论是哪种框架或语言。本章将讨论如何测试我们的应用程序以及如何编写可测试的代码。然后，我们将使用 Jasmine 和 Karma 测试我们的应用程序，以及在测试 mutations 时测试我们的 Vuex 代码。

第十一章，*优化*，概述了部署 Vue 应用程序和任何潜在的性能优化。然后，它介绍了将应用程序转换为**渐进式 Web 应用程序**（**PWA**）并添加`ServiceWorkers`、离线支持等。它还探讨了减少整体捆绑大小的方法，以及使用 SVG 获得性能优势。

第十二章，*使用 Nuxt 进行服务器端渲染*，展示了使用 Nuxt 创建服务器端渲染的 Vue 应用程序。该项目将使用 Vue CLI 创建，并且我们将从配置到路由、中间件和测试 Nuxt，一直到部署都会进行讨论。

第十三章，*模式*，帮助读者避免编写 Vue.js 应用程序时的常见反模式。提出了一份样式指南，以及关键问题，如使用`$parent`、`$ref`耦合问题、内联表达式等。

# 为了充分利用本书

1.  您应该已经了解并掌握 JavaScript（ES2015+）、HTML5 和 CSS3。

1.  本书不需要具备 Vue.js 的经验，尽管有其他 JavaScript 框架的经验将有助于比较和类似功能。

# 下载示例代码文件

您可以从[www.packtpub.com](http://www.packtpub.com)的帐户中下载本书的示例代码文件。如果您在其他地方购买了本书，可以访问[www.packtpub.com/support](http://www.packtpub.com/support)并注册，以便文件直接发送到您的邮箱。

您可以按照以下步骤下载代码文件：

1.  在[www.packtpub.com](http://www.packtpub.com/support)登录或注册。

1.  选择“支持”选项卡。

1.  单击“代码下载和勘误”。

1.  在搜索框中输入书名，然后按照屏幕上的说明操作。

下载文件后，请确保使用以下最新版本的软件解压或提取文件夹：

+   WinRAR/7-Zip 适用于 Windows

+   Zipeg/iZip/UnRarX 适用于 Mac

+   7-Zip/PeaZip 适用于 Linux

该书的代码包也托管在 GitHub 上，网址为[`github.com/PacktPublishing/Vue.js-2-Design-Patterns-and-Best-Practices`](https://github.com/PacktPublishing/Vue.js-2-Design-Patterns-and-Best-Practices)。

我们还有来自丰富图书和视频目录的其他代码包，可在**[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)**上找到。去看看吧！

# 下载彩色图片

我们还提供了一个 PDF 文件，其中包含本书中使用的屏幕截图/图表的彩色图片。您可以在这里下载：[`www.packtpub.com/sites/default/files/downloads/Vuejs2DesignPatternsandBestPractices_ColorImages.pdf.`](https://www.packtpub.com/sites/default/files/downloads/Vuejs2DesignPatternsandBestPractices_ColorImages.pdf)

# 使用的约定

本书中使用了许多文本约定。

`CodeInText`：表示文本中的代码词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 句柄。例如："将下载的`WebStorm-10*.dmg`磁盘映像文件挂载为系统中的另一个磁盘。"

代码块设置如下：

```js
// my-module.js
export default function add(x, y) {
 return x + y
}
```

任何命令行输入或输出都以以下方式编写：

```js
$ npm install
$ npm run dev
```

**粗体**：表示新术语、重要单词或屏幕上看到的单词。例如，菜单或对话框中的单词会以这种方式出现在文本中。例如："从管理面板中选择系统信息。"

警告或重要说明会以这种方式出现。提示和技巧会以这种方式出现。
