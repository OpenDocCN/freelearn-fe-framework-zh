# 前言

网络组件长期以来一直被誉为网络开发的下一个重大飞跃。随着 Angular 框架的新版本，我们比以往任何时候都更接近这一目标。在过去的几年里，网络开发社区中关于网络组件的讨论很多。Angular 中的新组件样式指令将改变开发者的工作流程和他们对阴影 DOM 中共享的可重用自定义 HTML 块的看法。通过从头开始构建整个应用程序，这本书是一种实用的学习方法，给读者提供了构建自己组件的机会。通过《精通 Angular 组件》，学习者将专注于一个关键领域，从而在新的网络开发浪潮中走在前列。

《精通 Angular 组件》教导读者在开发用户界面时以组件为基础进行思考。这本关于 Angular 中新的以组件为中心的做事方式的丰富指南，教导读者如何为他们的 Web 项目发明、构建和管理共享的可重用组件。这本书将改变开发者对如何在 Angular 中完成事情的看法，读者将通过对有用的和有趣的示例组件进行工作。

# 本书面向对象

本书面向已经对基本前端网络技术有良好理解的开发者，如 JavaScript、HTML 和 CSS。你将了解 Angular 中的新组件化架构以及如何使用它来构建现代、干净的用户界面。

# 本书涵盖内容

第一章，*基于组件的用户界面*，简要介绍了 UI 开发的历史以及基于组件的用户界面的一般概念。我们将看到 Angular 2 如何处理这个概念。

第二章，*准备，出发！*，让读者开始他们的旅程，构建一个基于组件的 Angular 2 应用程序。它涵盖了使用组件结构化应用程序的基本要素。

第三章，*处理数据和状态*，专注于如何将干净的数据和状态架构构建到我们的应用程序中。我们将学习使用 RxJS 进行响应式编程、纯组件、容器组件以及许多其他概念和工具，这些都可以用来对抗应用程序状态混乱。

第四章，*项目思维*，专注于用户界面结构和其基本组件。读者将通过将应用程序布局组织成组件、建立组件的组成以及创建可重用标签组件来结构化应用程序界面来构建一个应用程序。读者还将构建一个可重用编辑组件和评论系统。

第五章，*基于组件的路由*，解释了组件如何响应路由，并使读者能够向任务管理应用程序中的现有组件添加简单路由。读者还将处理登录过程，并了解如何使用路由器保护组件。

第六章，*跟上活动*，涵盖了创建将在项目和任务级别上可视活动流的组件。

第七章，*用户体验组件*，指导读者创建许多小型可重用组件，这些组件将对任务管理应用程序的整体用户体验产生重大影响。包括文本字段的就地编辑、无限滚动、弹出通知和拖放支持等好处。

第八章，*时间会证明一切*，专注于创建时间跟踪组件，这些组件有助于在项目和任务级别上估算时间，同时也让用户能够记录他们在任务上花费的时间。

第九章，*飞船仪表盘*，专注于使用第三方库 Chartist 创建组件以在任务管理应用程序中可视化数据。

第十章，*对事物进行测试*，涵盖了测试 Angular 组件的一些基本方法。我们将探讨为测试而模拟/覆盖组件特定部分的可选方案。

# 为了充分利用本书

本书需要在您的 Windows、Mac 或 Linux 机器上安装 Node.js 的基本版本。由于本书依赖于 Angular CLI 6.0.8，至少需要 Node.js 8.9.0。

# 下载示例代码文件

您可以从[www.packtpub.com](http://www.packtpub.com)的账户下载本书的示例代码文件。如果您在其他地方购买了此书，您可以访问[www.packtpub.com/support](http://www.packtpub.com/support)并注册，以便将文件直接通过电子邮件发送给您。

您可以通过以下步骤下载代码文件：

1.  在[www.packtpub.com](http://www.packtpub.com/support)上登录或注册。

1.  选择“支持”标签。

1.  点击“代码下载与勘误”。

1.  在搜索框中输入本书的名称，并遵循屏幕上的说明。

下载文件后，请确保使用最新版本解压缩或提取文件夹：

+   Windows 上的 WinRAR/7-Zip

+   Mac 上的 Zipeg/iZip/UnRarX

+   Linux 上的 7-Zip/PeaZip

本书代码包也托管在 GitHub 上，网址为[`github.com/PacktPublishing/Mastering-Angular-Components-Second-Edition`](https://github.com/PacktPublishing/Mastering-Angular-Components-Second-Edition)。如果代码有更新，它将在现有的 GitHub 仓库中更新。

我们还有其他来自我们丰富的书籍和视频目录的代码包可供选择，请访问**[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)**。查看它们吧！

# 使用的约定

本书使用了多种文本约定。

`CodeInText`：表示文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 昵称。以下是一个示例：“让我们将主组件的视图封装更改到使用`ViewEncapsulation.None`模式。”

代码块按照以下方式设置：

```js
import {Component, ViewEncapsulation} from '@angular/core';

@Component({
  selector: 'mac-root',
  templateUrl: './app.component.html',
  encapsulation: ViewEncapsulation.None
})
export class AppComponent {
  title = 'mac';
}
```

当我们希望您注意代码块中的特定部分时，相关的行或项目将以粗体显示：

```js
...
import {TaskService} from './tasks/task.service';
...
@NgModule({
...
 providers: [TaskService],
 ...
})
export class AppModule {
}
```

有时，在需要您在现有代码文件中实现代码更改的大型代码摘录中，我们使用以下格式：

+   新或替换的代码部分将以粗体标记

+   已经存在且不相关的代码部分使用省略号字符隐藏。

任何命令行输入或输出都按照以下方式编写：

```js
ng new mastering-angular-components --prefix=mac
```

**粗体**：表示新术语、重要单词或屏幕上看到的单词。例如，菜单或对话框中的单词在文本中显示如下。以下是一个示例：“你应该能够看到带有欢迎消息“欢迎使用 mac！”的生成应用程序 app。”

警告或重要注意事项看起来像这样。

小贴士和技巧看起来像这样。

# 联系我们

我们始终欢迎读者的反馈。

**一般反馈**：请发送电子邮件至`feedback@packtpub.com`，并在邮件主题中提及书籍标题。如果您对本书的任何方面有疑问，请发送电子邮件至`questions@packtpub.com`。

**勘误**：尽管我们已经尽一切努力确保内容的准确性，但错误仍然会发生。如果您在这本书中发现了错误，我们将非常感激您能向我们报告。请访问[www.packtpub.com/submit-errata](http://www.packtpub.com/submit-errata)，选择您的书籍，点击勘误提交表单链接，并输入详细信息。

**盗版**：如果您在互联网上以任何形式发现我们作品的非法副本，我们将非常感激您能提供位置地址或网站名称。请通过链接至材料与我们联系至`copyright@packtpub.com`。

**如果您有兴趣成为作者**：如果您在某个主题上具有专业知识，并且您有兴趣撰写或为书籍做出贡献，请访问[authors.packtpub.com](http://authors.packtpub.com/)。

# 评论

请留下评论。一旦您阅读并使用过这本书，为什么不在您购买它的网站上留下评论呢？潜在读者可以查看并使用您的客观意见来做出购买决定，Packt 公司可以了解您对我们产品的看法，我们的作者也可以看到他们对书籍的反馈。谢谢！

有关 Packt 的更多信息，请访问[packtpub.com](https://www.packtpub.com/)。
