# 前言

欢迎阅读 *学习 Web 组件开发*。如果你想要学习和理解 W3C 的 Web 组件规范，并使用 Polymer、Bosonic、Mozilla Brick 和 ReactJS 开发自定义 Web 组件，那么这本书就是为你准备的。它提供了一个系统的方法来构建响应式 Web 应用程序。本书解释了所有可以帮助构建 Web 组件的 Web 组件规范的关键特性，并附带了你需要的大量详细代码。

# 本书涵盖的内容

第一章, *介绍 Web 组件*，将提供关于 Web 组件的介绍。它包括对 Web 组件构建块的详细解释。

第二章, *介绍 Polymer*，主要介绍 Google 的 Polymer 库。它解释了这个库的架构。它还探讨了核心和纸元素。

第三章, *使用 Polymer 开发 Web 组件*，主要介绍使用 PolymerJS 进行自定义 Web 组件的开发。它提供了一个逐步指南，指导如何使用这个库开发一个自定义组件。

第四章, *探索用于 Web 组件开发的 Bosonic 工具*，专注于 Bosonic 工具。它解释了如何使用这些工具创建一个自定义组件。

第五章, *使用 Mozilla Brick 开发 Web 组件*，涉及 Mozilla Brick 库。它包括对 Brick 库的简要介绍，还包括了使用 Brick 的各种组件的代码示例。

第六章, *使用 ReactJS 构建 Web 组件*，主要介绍 ReactJS。它解释了什么是响应式方法。它还包括了使用 ReactJS 创建 Web 组件的代码示例。

附录, *Web 组件参考*，列出了所有关于 Web 组件的在线网站和论坛，供进一步学习。

# 为本书所需的条件

以下列表列出了本书所需的工具和库：

+   WebStorm IDE。

+   最新版的 Chrome 浏览器。

+   GIT

+   npm

+   Bower

+   Yeoman

+   Grunt

# 本书面向的对象

本书是为想要学习和开发 Web 组件的 JavaScript 开发者而写的。本书对那些想要学习市场上可用的不同框架，通过这些框架开发 Web 组件的人来说也很有帮助。最后，本书适合所有想要更好地理解 Web 组件、开发他们自己的自定义组件的人。

# 规范

在这本书中，您将找到许多文本样式，用于区分不同类型的信息。以下是一些这些样式的示例及其含义的解释。

文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 用户名如下所示：“我们可以通过使用`include`指令来包含其他上下文。”

代码块设置如下：

```js
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title>
        Web Component: template support
    </title>
</head>
<body>
```

当我们希望您注意代码块中的特定部分时，相关的行或项目将以粗体显示：

```js
<body>
<div id="container"></div>
<template id="aTemplate">
 <h1>Template is activated using importNode method.</h1>
</template>
<script>
```

任何命令行输入或输出如下所示：

```js
npm install --save b-sortable

```

**新术语**和**重要词汇**以粗体显示。您在屏幕上看到的单词，例如在菜单或对话框中，在文本中显示如下：“现在，按下**删除 SayHello 元素**按钮，这将从 DOM 树中删除该元素。”

### 注意

警告或重要注意事项以如下框显示。

### 小贴士

小技巧和窍门看起来像这样。

# 读者反馈

我们始终欢迎读者的反馈。请告诉我们您对这本书的看法——您喜欢什么或不喜欢什么。读者反馈对我们来说非常重要，因为它帮助我们开发出您真正能从中获得最大收益的书籍。

要向我们发送一般反馈，只需发送电子邮件至 `<feedback@packtpub.com>`，并在邮件主题中提及书籍的标题。

如果您在某个主题上具有专业知识，并且您对撰写或为书籍做出贡献感兴趣，请参阅我们的作者指南[www.packtpub.com/authors](http://www.packtpub.com/authors)。

# 客户支持

现在，您是 Packt 书籍的骄傲拥有者，我们有多种方式帮助您从您的购买中获得最大收益。

## 下载示例代码

您可以从您在[`www.packtpub.com`](http://www.packtpub.com)的账户中下载示例代码文件，适用于您购买的所有 Packt Publishing 书籍。如果您在其他地方购买了这本书，您可以访问[`www.packtpub.com/support`](http://www.packtpub.com/support)并注册，以便将文件直接通过电子邮件发送给您。

## 勘误

尽管我们已经尽最大努力确保内容的准确性，但错误仍然可能发生。如果您在我们的书中发现错误——可能是文本或代码中的错误——如果您能向我们报告这一点，我们将不胜感激。通过这样做，您可以避免其他读者的挫败感，并帮助我们改进本书的后续版本。如果您发现任何勘误，请通过访问[`www.packtpub.com/submit-errata`](http://www.packtpub.com/submit-errata)来报告它们，选择您的书籍，点击**勘误提交表单**链接，并输入您的勘误详情。一旦您的勘误得到验证，您的提交将被接受，勘误将被上传到我们的网站或添加到该标题的勘误部分下的现有勘误列表中。

要查看之前提交的勘误表，请访问[`www.packtpub.com/books/content/support`](https://www.packtpub.com/books/content/support)，并在搜索字段中输入书籍名称。所需信息将出现在**勘误**部分下。

## 盗版

在互联网上，版权材料的盗版是一个跨所有媒体的持续问题。在 Packt，我们非常重视我们版权和许可证的保护。如果您在互联网上发现我们作品的任何非法副本，请立即提供位置地址或网站名称，以便我们可以寻求补救措施。

请通过链接<版权@packtpub.com>与我们联系，并提供涉嫌盗版材料的链接。

我们感谢您在保护我们作者和我们提供有价值内容的能力方面所提供的帮助。

## 问题

如果您对本书的任何方面有问题，请通过链接<问题@packtpub.com>与我们联系，我们将尽力解决问题。
