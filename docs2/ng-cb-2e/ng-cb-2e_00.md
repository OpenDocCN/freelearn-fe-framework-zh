# 前言

Angular 是世界上最受欢迎的框架之一，不仅用于构建网络应用，甚至还可以用于构建移动和桌面应用。由 Google 支持并由 Google 使用，这个框架被数百万个应用使用。尽管该框架非常适合任何规模的应用，但企业尤其偏爱 Angular，因为它具有明确的性质和一致的生态系统，该生态系统包括创建现代和可生产网络应用所需的所有工具。

虽然学习 JavaScript、HTML 和 CSS 等核心技术对于成为一名网络开发者来说是绝对必要的，但当涉及到框架时，学习框架本身的核心概念也同样重要。当我们处理 Angular 时，通过了解并使用 Angular 生态系统中的正确工具，我们可以对我们的网络应用做很多事情。这正是本书的用武之地。

本书是为中级和高级 Angular 开发者编写的，旨在通过易于遵循、可以玩转和实践自己变体的食谱来磨练他们的 Angular 开发技能。你不仅可以从食谱本身学习，还可以从与食谱相关的实际真实项目中学习。因此，这些食谱和项目中有很多隐藏的宝藏等你去发现。

开心编码！

# 本书面向对象

本书面向寻求解决 Angular 企业开发中常见问题的中级 Angular 网络开发者。使用 Angular 技术的移动开发者也会发现本书很有用。理解本书涵盖的主题需要具备 JavaScript 和 TypeScript 的工作经验。

# 本书涵盖内容

*第一章*，*赢得组件通信*，解释了实现 Angular 组件间通信的不同技术。涵盖了`@Input()`和`@Output()`装饰器、服务以及生命周期钩子。本章还涵盖了 Angular Signals，它在 Angular v17 中变得稳定。

*第二章*，*与 Angular 指令和内置控制流一起工作*，介绍了 Angular 指令以及使用属性指令、结构指令和指令组合 API 的一些食谱。

*第三章*，*Angular 中依赖注入的魔法*，包括覆盖可选依赖、配置注入令牌、使用`providedIn: 'root'`元数据为 Angular 服务、值提供者和别名类提供者的食谱。

*第四章*，*理解 Angular 动画*，包含了实现多状态动画、交错动画、关键帧动画以及在不同路由间切换的 Angular 应用的动画的食谱。

*第五章*，*Angular 和 RxJS – 强强联合*，涵盖了关于执行顺序和并行 HTTP 调用的 RxJS 食谱。它还包括关于使用`combineLatest`、`flatMap`、`debounceTime`和`switchMap`运算符的食谱，以及关于使用 RxJS 流的技巧和窍门。

*第六章*，*使用 NgRx 进行响应式状态管理*，包含关于著名 NgRX 库及其核心概念的食谱。它涵盖了 NgRx 动作、reducer、selectors 和 effects 等核心概念，并探讨了使用`@ngrx/store-devtools`和`@component/store`等包。

*第七章*，*理解 Angular 导航和路由*，探讨了关于懒加载路由、路由守卫、路由预加载策略以及与 Angular 路由一起使用的有趣技术的食谱。

*第八章*，*精通 Angular 表单*，涵盖了模板驱动表单、响应式表单、表单验证、测试表单、表单数组和创建自己的表单控件。

*第九章*，*Angular 和 Angular CDK*，包含许多有趣的 Angular CDK 食谱，包括虚拟滚动、键盘导航、overlay API、CDK 菜单、CDK 拖放、CDK 步骤 API 和 CDK Listbox API。

*第十章*，*使用 Jest 在 Angular 中编写单元测试*，涵盖了使用 Jest 进行单元测试的食谱，探索 Jest 中的全局模拟，模拟服务/子组件/管道，使用 Angular CDK 组件工具包，以及单元测试 Observables。

*第十一章*，*使用 Cypress 在 Angular 中进行端到端测试*，提供了在 Angular 应用程序中使用 Cypress 进行端到端测试的食谱。它涵盖了验证表单、等待 XHR 调用、模拟 HTTP 调用响应、使用 Cypress 捆绑包以及使用 Cypress 中的固定值。

*第十二章*，*Angular 的性能优化*，包含了一些使用`OnPush`变更检测策略、懒加载功能路由、从组件中分离变更检测器、使用 Angular 的 web workers、使用纯管道、向 Angular 应用程序添加性能预算以及使用`webpack-bundle`分析器来提高 Angular 应用程序性能的技巧。

*第十三章*，*使用 Angular 构建 PWA*，包含使用 Angular 创建 PWA 的食谱。它涵盖了为 PWA 指定主题颜色、使用设备的暗黑模式、提供自定义 PWA 安装提示、使用 Angular 的服务工作者进行预缓存请求以及使用 App Shell。

# 为了充分利用这本书

这本书的食谱是用 Angular v17 构建的，Angular 为其发布遵循语义版本控制。由于 Angular 不断改进，为了稳定起见，Angular 团队为更新提供了可预测的发布周期。发布频率如下：

+   每半年一个大版本发布。

+   每个大版本发布 1 到 3 个小版本。

+   每周几乎都会有一个补丁版本和预发布（下一个或 rc）构建。

来源：[`angular.io/guide/releases#release-frequency`](https://angular.io/guide/releases#release-frequency)

| **本书中涵盖的软件** | **操作系统要求** |
| --- | --- |
| Angular v17 | Windows, macOS, or Linux |
| TypeScript 5.x+ |  |
| Nx v17 |  |

如果您正在使用本书的数字版，我们建议您亲自输入代码或从本书的 GitHub 仓库（下一节中提供链接）获取代码。这样做将帮助您避免与代码复制粘贴相关的任何潜在错误。

读完本书后，请确保发推文告诉我您对本书的反馈。此外，您可以根据自己的喜好修改本书提供的代码，将其上传到您的 GitHub 仓库并分享。我会确保转发它：)

## 下载示例代码文件

您可以从 GitHub 下载本书的示例代码文件[`github.com/PacktPublishing/Angular-Cookbook-2E`](https://github.com/PacktPublishing/Angular-Cookbook-2E)。如果代码有更新，它将在 GitHub 仓库中更新。

我们还有其他来自我们丰富图书和视频目录的代码包，可在[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)找到。查看它们吧！

## 下载彩色图像

我们还提供了一份包含本书中使用的截图和图表彩色图像的 PDF 文件。您可以从这里下载：[`packt.link/gbp/9781803233444`](https://packt.link/gbp/9781803233444)。

## 使用的约定

本书使用了多种文本约定。

`文本中的代码`: 表示文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 昵称。以下是一个示例：“现在，我们将代码从`the-amazing-list-component.html`文件移动到`the-amazing-list-item.component.html`文件，以进行项目的标记。”

代码块设置如下：

```js
openMenu($event, itemTrigger) {
    if ($event) {
      $event.stopImmediatePropagation();
    }
    this.popoverMenuTrigger = itemTrigger;
    this.menuShown = true;
  } 
```

当我们希望您注意代码块中的特定部分时，相关的行或项目将以粗体显示：

```js
.menu-popover {
  ...
  &::before {...}
  &--up {
    **transform****:** **translateY****(-****20px****);**
**&****::before** **{**
**top****: unset** **!important****;**
**transform****:** **rotate****(****180deg****);**
**bottom****: -****10px****;**
**}**
  }
  &__list {...}
} 
```

**粗体**: 表示新术语、重要单词或您在屏幕上看到的单词。例如，菜单或对话框中的单词以粗体显示。以下是一个示例：“您会注意到我们看不到输入内容的全部内容——在最理想的情况下，这也是有点令人烦恼的，因为您在按下**操作**按钮之前无法真正地审查它。”

**重要提示**

应该看起来像这样。

**提示**

应该看起来像这样。

# 联系我们

我们欢迎读者的反馈。

**一般反馈**: 如果您对本书的任何方面有疑问，请通过电子邮件发送至 customercare@packtpub.com，并在邮件主题中提及书名。

**勘误表**: 尽管我们已经尽一切努力确保内容的准确性，但错误仍然可能发生。如果您在本书中发现错误，我们将非常感激您向我们报告。请访问[www.packtpub.com/support/errata](http://www.packtpub.com/support/errata)并填写表格。

**盗版**：如果您在互联网上以任何形式遇到我们作品的非法副本，如果您能提供位置地址或网站名称，我们将不胜感激。请通过 copyright@packt.com 与我们联系，并提供材料的链接。

**如果您有兴趣成为作者**：如果您在某个领域有专业知识，并且您有兴趣撰写或为书籍做出贡献，请访问[authors.packtpub.com](http://authors.packtpub.com)。

# 分享您的想法

一旦您阅读了《Angular Cookbook - 第二版》，我们非常乐意听听您的想法！请[点击此处直接进入此书的亚马逊评论页面](https://www.packtpub.com/)并分享您的反馈。

您的评论对我们和科技社区非常重要，并将帮助我们确保我们提供高质量的内容。

# 下载此书的免费 PDF 副本

感谢您购买此书！

您喜欢在路上阅读，但无法携带您的印刷书籍到处走？

您的电子书购买是否与您选择的设备不兼容？

别担心，现在，每购买一本 Packt 书籍，您都可以免费获得该书的 DRM 免费 PDF 版本。

在任何地方、任何设备上阅读。直接从您喜欢的技术书籍中搜索、复制和粘贴代码到您的应用程序中。

优惠远不止于此，您还可以获得独家折扣、时事通讯和每日免费内容的每日电子邮件。

按照以下简单步骤获取优惠：

1.  扫描下面的二维码或访问以下链接

![](img/B18469_Free_PDF.png)

https://packt.link/free-ebook/9781803233444

1.  提交您的购买证明

1.  就这些！我们将直接将您的免费 PDF 和其他优惠发送到您的电子邮件
