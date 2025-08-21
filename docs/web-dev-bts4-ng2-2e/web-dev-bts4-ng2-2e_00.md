# 前言

这本书是关于当代网页开发中两个巨大和最受欢迎的名字，Angular 2 和 Bootstrap 4。

Angular 2 是 AngularJS 的继任者，但在许多方面都比前任更好。它在处理大量数据需求时结合了在网页浏览器上可能的最大速度和可扩展性。这使得 Angular 2 成为构建新系统或从旧系统升级时的首选候选人。

Bootstrap 4 是构建响应式、移动优先网页应用程序的下一个进化阶段。它可以轻松高效地将网站从移动端扩展到桌面端，只需一个代码库。

如果您想利用 Angular 2 的强大功能和 Bootstrap 4 的灵活性来构建强大的大规模或企业级网页应用程序，您来对地方了。

我有写一本关于 Angular 2 和 Bootstrap 4 的书的愿望，不假设读者有先前的经验和知识。我的想法是书中充满了技术细节。现在，你手中拿着的书就是这个愿望的实现，因为它既适合初学者，又在技术上很深入。它涵盖了开发人员使用这两个框架进行严肃网页开发所需的一切。

# 这本书涵盖了什么

第一章 ，*打招呼！*，指导您建立一个开发环境，以便展示如何轻松地使用 Angular 2 和 Bootstrap 4 快速启动网页应用程序的最简单应用程序。

第二章 ，*使用 Bootstrap 组件*，展示了如何通过展示演示布局页面来开始使用 Bootstrap 4，以及如何探索框架并根据您的需求进行定制。

第三章 ，*高级 Bootstrap 组件和定制*，解释了如何使用诸如 Jumbotron、Carousel 之类的组件，并通过输入组件为您节省时间。

第四章 ，*创建模板*，让您学习如何使用内置的 Angular 2 指令构建 UI 模板。您将熟悉模板语法，以及如何在 HTML 页面中绑定属性和事件，并使用管道转换显示。

第五章，*路由*，帮助您了解路由器代码在用户执行应用程序任务时如何管理视图之间的导航。我们将看看如何创建静态路由以及包含参数的路由以及如何配置它们。

第六章，*依赖注入*，教读者如何解耦应用程序的需求，以及如何创建一个一致的数据源作为服务。

第七章，*使用表单*，向读者展示了如何使用与表单创建相关的 Angular 2 指令，以及如何将基于代码的表单组件用于 HTML 表单。我们将使用 Bootstrap 4 来增强表单的外观，并指示我们的 Web 应用程序的无效输入。

第八章，*高级组件*，描述了组件的生命周期以及可以在不同阶段使用的方法。我们将分析这个周期的每个阶段，并学习如何充分利用当组件从一个阶段转移到另一个阶段时触发的挂钩方法。

第九章，*通信和数据持久性*，解释了如何使用内置的 HTTP 库来处理端点。我们将学习如何使用 Firebase 作为应用程序的持久层。

第十章，*高级 Angular 技术*，介绍了高级的 Angular 技术。我们将借助 Webpack 来改造我们的应用程序，并学习如何安装和使用 ng2-bootstrap。我们将探索 Angular CLI 的世界，并使用 AOT 来显著减少生产代码的大小。

# 本书所需的内容

任何安装了 Windows、Linux 或 Mac OS 的现代个人电脑都足以运行本书中的代码示例。本书中使用的所有软件都是开源的，并且可以在网上免费获得：

+   [`git-scm.com`](https://git-scm.com)

+   [`nodejs.org`](https://nodejs.org)

+   [`www.npmjs.com`](https://www.npmjs.com)

+   [`v4-alpha.getbootstrap.com`](http://v4-alpha.getbootstrap.com)

+   [`www.ruby-lang.org`](https://www.ruby-lang.org)

+   [`firebase.google.com`](https://firebase.google.com)

# 本书适合谁

无论您对 Bootstrap 或 Angular 了解多少，还是完全初学者，本书都将增强您在这两个框架中的能力，并帮助您构建一个完全功能的 Web 应用程序。需要对 HTML、CSS 和 JavaScript 有一定了解，才能充分掌握 Bootstrap 和 Angular。

# 读累了记得休息一会哦~

**公众号：古德猫宁李**

+   电子书搜索下载

+   书单分享

+   书友学习交流

**网站：**[沉金书屋 https://www.chenjin5.com](https://www.chenjin5.com)

+   电子书搜索下载

+   电子书打包资源分享

+   学习资源分享

# 约定

在本书中，您将找到许多文本样式，用于区分不同类型的信息。以下是一些这些样式的示例及其含义的解释。

文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 用户名显示如下：“它支持`number`，`boolean`和`string`类型注释，用于原始类型和任何动态类型结构。”

代码块设置如下：

```ts
function add(first: number, second: number): number {
   return first + second;
}
```

当我们希望引起您对代码块的特定部分的注意时，相关行或项目将以粗体显示：

```ts
var x = 3;
function random(randomize) {
    if (randomize) {
        // x initialized as reference on function
 **        var x = Math.random();** 

        return x;
    }
    return x; // x is not defined
}
random(false); // undefined
```

任何命令行输入或输出都以以下方式编写：

```ts
 **npm config list** 

```

**新术语**和**重要单词**以粗体显示。您在屏幕上看到的单词，例如菜单或对话框中的单词，会在文本中显示为：“单击**下一步**按钮将您移至下一个屏幕。”

### 注意

警告或重要说明会以这样的方式出现在一个框中。 

### 提示

技巧和诀窍会以这种方式出现。
