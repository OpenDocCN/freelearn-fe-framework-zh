# 前言

学习 ReactJS 是一种轻量但强大的构建出色 UI 组件的方法！这本书将帮助你使用 ReactJS 开发健壮、可重用且快速的用户界面。这本书将确保平稳无缝地过渡到 ReactJS 生态系统。本书充满了实际应用。从设置到实现、测试和部署：探索前端 Web 开发的新前沿。ReactJS，作为 MVC 架构的 V，由 Facebook 和 Instagram 的开发者开发。我们将深入 ReactJS 的世界，探索单向数据流、虚拟 DOM、DOM 差异，这些都是 ReactJS 利用以提高 UI 性能的。你将通过逐步过程学习 ReactJS 的关键概念。你还将学习 ReactJS 中用于未来浏览器的 ES6 语法，以及用于在当前浏览器中支持它的转译技术。

你不仅将学习如何应用和实现 ReactJS 概念，还将了解如何测试基于 JS 的应用程序并将它们部署。除此之外，你还将使用 Flux 架构开发一个完整的应用程序。你还将了解 Redux，它让你通过引入一些对更新的限制，轻松地理解如何在 ReactJS 应用程序中操作数据。通过涵盖概念及其理论解释的大量代码以及应用程序的截图，你将获得对 ReactJS 的深入理解。

# 本书涵盖内容

第一章, *ReactJS 入门*，是关于 React 的简要概述，包括在哪里下载以及如何在你的网页上使其工作。它将演示如何创建你的第一个 React 组件。

第二章, *探索 JSX 和 ReactJS 结构*，将展示与第一章中创建的相同简单的 React 组件，使用 JSX 语法构建。它将解释 JSX 的目的并揭开其使用的神秘面纱。它将比较一些较旧的模板技术与 JSX，并试图澄清一些关于它的常见问题。

第三章, *使用属性*，将帮助你开始开发自己的应用程序。它将使用 Facebook Open Graph API。这将涵盖如何配置它、获取你的朋友列表以及使用 React 渲染它。在此之后，我们将把 UI 分解成小的组件。

第四章, *有状态组件和事件*，涵盖了具有状态的组件、它们之间的通信实践以及如何响应用户的输入/事件，以便 UI 反映这种状态。本章还涵盖了状态如何通过虚拟 DOM 改变 React UI 的性能。

第五章, *组件生命周期和 React 中的新 ECMAScript*，探讨了这种 React 组件的生命周期。此外，我们还将深入研究未来的 ECMA Script 语法以及 React 社区从 0.13.0 版本开始使用的少量变化。为此，我们将回顾 react 库中的某些 ES6 和 ES7 特性。

第六章, *使用 Flux 进行 React 操作*，将解释 Flux 架构，该架构用于构建客户端 Web 应用程序。它通过使用单向数据流来补充 React 的可组合视图组件。对 FLUX 架构的所有组件（视图、存储、动作和调度器）都有深入的解释。

第七章, *使你的组件可重用*，将涵盖 React 的良好实践和模式。这包括开发可重用组件的实践，如何以更好的数据流结构化你的组件层次结构，以及如何验证你的组件行为。

第八章, *测试 React 组件*，将展示如何测试你的 React 代码，因为这在 React 中从未如此简单。为此，我们将对迄今为止开发的 app 进行单元测试。

第九章, *为部署代码做准备*，告诉我们 React 自带一个 JSX 转换器，它可以在运行时工作。然而，这不应该在生产环境中部署。本章将指导你使用 node 库（如 Webpack 和 Gulp）离线构建这些文件的方法。

第十章, *接下来是什么*，解释了一些其他高级概念，例如 react-router、react-ajax、热重载、redux、同构应用程序等。

# 你需要这本书的以下内容

基本要求是安装 NodeJS，然后安装 npm 包，如 react、react-tools、express 等。按章节提供的完整列表如下：

| 章节编号 | 需要的软件（含版本） |
| --- | --- |

| 1 | Nodejs 4.2.4ReactJS:

+   [`fb.me/react-0.14.7.js`](http://fb.me/react-0.14.7.js)（开发版）

+   [`fb.me/react-0.14.7.min.js`](http://fb.me/react-0.14.7.min.js)（生产版）

JSXTransformer：

+   [`cdnjs.cloudflare.com/ajax/libs/react/0.13.3/JSXTransformer.js`](https://cdnjs.cloudflare.com/ajax/libs/react/0.13.3/JSXTransformer.js)

安装 Python 或 httpster 以提供 web 服务器 Chrome / Mozilla ReactJS 插件/扩展以用于浏览器 JS 工具 |

| 2 | npm install react-tools |
| --- | --- |
| 3 | Open-Graph JavaScript SDK: [`developers.facebook.com/docs/javascript`](https://developers.facebook.com/docs/javascript) |
| 5 | ReactJS 版本 0.13.0 或更高 JSXTransformer（0.13.3） |
| 8 | npm install -g -d chai mocha jest-cli babel-loader babel-preset-es2015 babel-preset-react babel-preset-stage-2 react-addons-test-utils |
| 9 | npm install -g webpack browserifynpm install --save-dev gulp gulp-concat gulp-uglify gulp-react gulp-html-replacenpm install --save-dev vinyl-source-stream browserify watchify reactify gulp-streamify |
| 10 | npm install expressnpm install react-redux |

# 这本书适合谁阅读

无论您是 JS 世界的初学者还是一个经验丰富的 JS 开发者，这本书都将确保您在 ReactJS 生态系统中顺利过渡。您不仅将了解并实现 ReactJS 的概念，还将学习如何测试基于 JS 的应用程序并将它们部署。除此之外，您还将被介绍到 Flux，并基于 Flux 应用架构构建应用程序，这不仅仅是一个完整的框架，而是一种架构。您还将了解 Redux，它通过引入一些更新限制，让您能够轻松地操作 ReactJS 应用程序中的数据。通过涵盖理论解释的概念的大量代码和应用程序的截图，您将获得对 ReactJS 的简单而深入的理解。

# 习惯用法

在这本书中，您将找到多种文本样式，用于区分不同类型的信息。以下是一些这些样式的示例及其含义的解释。

文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 昵称将如下所示：“一旦安装了 Sublime 编辑器，请转到安装目录，您可以通过在当前目录中运行`subl`来从终端打开 Sublime，您将在 Sublime 中打开当前目录的文件。”

代码块设置如下：

```js
<!DOCTYPE html>
<html>
<head>
  <script src="img/fb-react-0.12.2.js"></script>
</head>
<body>
  <div id="root"></div>
</body>
</html>
```

当我们希望您注意代码块中的特定部分时，相关的行或项目将以粗体显示：

```js
function loadUserAndLikes () {
  FB.api('/me', function (userResponse) {
    React.render(<UserDetails userDetails={userResponse} />, document.getElementById('user'));

    var fields = { fields: 'category,name,picture.type(normal)' };
    FB.api('/me/likes', fields, function (likesResponse) {
      React.render(<UserLikesList list={likesResponse.data} />, document.getElementById('main'));
    });
  });
}
```

任何命令行输入或输出将如下所示：

```js
sudo npm install jest-cli –save-dev

```

**新术语**和**重要词汇**将以粗体显示。您在屏幕上看到的单词，例如在菜单或对话框中，在文本中如下所示：“我在 MacOS X Yosemite 上使用 Atom 的经验是，字体质量看起来比 Sublime Text 差。如果您遇到这种情况，您只需在 Atom 的设置中取消选择**使用硬件加速**选项即可。”

### 注意

警告或重要注意事项将以这样的框框显示。

### 提示

技巧和窍门如下所示。

# 读者反馈

我们始终欢迎读者的反馈。请告诉我们您对这本书的看法——您喜欢或不喜欢的地方。读者反馈对我们很重要，因为它帮助我们开发出您真正能从中获得最大收益的标题。

要向我们发送一般反馈，请简单地通过电子邮件发送至 `<feedback@packtpub.com>`，并在邮件主题中提及本书的标题。

如果您在某个领域有专业知识，并且您有兴趣撰写或为书籍做出贡献，请参阅我们的作者指南，网址为[www.packtpub.com/authors](http://www.packtpub.com/authors)。

# 客户支持

现在您已经是 Packt 图书的骄傲拥有者，我们有一些事情可以帮助您从您的购买中获得最大收益。

## 下载示例代码

您可以从[`www.packtpub.com`](http://www.packtpub.com)的账户下载本书的示例代码文件。如果您在其他地方购买了此书，您可以访问[`www.packtpub.com/support`](http://www.packtpub.com/support)并注册，以便将文件直接通过电子邮件发送给您。

您可以通过以下步骤下载代码文件：

1.  使用您的电子邮件地址和密码登录或注册我们的网站。

1.  将鼠标指针悬停在顶部的**支持**标签上。

1.  点击**代码下载与勘误**。

1.  在**搜索**框中输入书籍名称。

1.  选择您想要下载代码文件的书籍。

1.  从下拉菜单中选择您购买此书的来源。

1.  点击**代码下载**。

下载文件后，请确保您使用最新版本解压缩或提取文件夹：

+   WinRAR / 7-Zip for Windows

+   Zipeg / iZip / UnRarX for Mac

+   7-Zip / PeaZip for Linux

## 下载本书的彩色图像

我们还为您提供了一个包含本书中使用的截图/图表彩色图像的 PDF 文件。彩色图像将帮助您更好地理解输出的变化。您可以从[`www.packtpub.com/sites/default/files/downloads/GettingStartedwithReact_ColorImages.pdf`](https://www.packtpub.com/sites/default/files/downloads/GettingStartedwithReact_ColorImages.pdf)下载此文件。

## 勘误

尽管我们已经尽一切努力确保内容的准确性，但错误仍然可能发生。如果您在我们的某本书中发现了错误——可能是文本或代码中的错误——如果您能向我们报告这一点，我们将不胜感激。通过这样做，您可以节省其他读者的挫败感，并帮助我们改进本书的后续版本。如果您发现任何勘误，请通过访问[`www.packtpub.com/submit-errata`](http://www.packtpub.com/submit-errata)，选择您的书籍，点击**勘误提交表单**链接，并输入您的勘误详情来报告它们。一旦您的勘误得到验证，您的提交将被接受，勘误将被上传到我们的网站或添加到该标题的勘误部分下的现有勘误列表中。

要查看之前提交的勘误，请访问[`www.packtpub.com/books/content/support`](https://www.packtpub.com/books/content/support)，并在搜索框中输入书籍名称。所需信息将出现在**勘误**部分下。

## 盗版

互联网上对版权材料的盗版是一个跨所有媒体的持续问题。在 Packt，我们非常重视我们版权和许可证的保护。如果您在互联网上发现任何形式的我们作品的非法副本，请立即提供位置地址或网站名称，以便我们可以寻求补救措施。

请通过链接将涉嫌盗版材料发送至 `<copyright@packtpub.com>`。

我们感谢您在保护我们的作者和我们为您提供有价值内容的能力方面的帮助。

## 问题

如果您对本书的任何方面有问题，您可以联系 `<questions@packtpub.com>`，我们将尽力解决问题。
