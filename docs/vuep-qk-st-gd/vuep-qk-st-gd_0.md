# 前言

VuePress 自称为“Vue 动力的静态站点生成器”。换句话说，VuePress 是一个简单的工具，可以让您在几分钟内创建静态站点和单页应用（SPA）。

在 VuePress 中构建的任何内容都是搜索引擎友好的，完全优化的，并且也是移动友好的，由于没有数据库驱动的操作或外部引擎的工作，因此速度非常快。您只需在 Markdown 中输入内容，VuePress 将解析 Markdown 文件为有效的 HTML。

但 VuePress 不仅仅是静态站点生成！您可以自定义其外观，调整默认的最小主题，并利用 Vue.js 专业知识来扩展站点的功能。考虑到 Vue.js 的受欢迎程度在稳步上升，VuePress 已经站在了一个巨人的肩膀上，并且有很大的增长潜力！

这本快速入门指南将帮助您在短时间内开始使用 VuePress！

# 本书适合的读者

本书适用于希望学习如何在 VuePress 中构建静态站点的任何人。所有内容都由 VuePress 呈现为 HTML，然后作为 SPA 执行。这意味着一切都可以快速运行并在瞬间加载。

如果您是 JavaScript 开发人员或希望专门研究 Vue.js，VuePress 对您来说可能是一个方便的工具。本书将帮助您了解 VuePress 的功能和方法，以帮助您适应 Vue.js 的操作方式。此外，如果您希望使用 Markdown 创建一个简单的站点，比如为现有或即将推出的项目创建一个文档站点，VuePress 可能是完美的解决方案。在这种情况下，这本快速入门指南就是您掌握 VuePress 所需的全部。

此外，如果您只是想学习一些关于静态站点和静态站点生成器的知识，那么这本书也适合您！

# 本书涵盖的内容

第一章《静态站点生成器和 VuePress 简介》向读者介绍了静态站点生成器，如 Jekyll、Hugo 和 Hexo。在特别讨论 VuePress 之前，本章还将讨论与静态站点生成器相关的各种优势和可能的缺点。

第二章《开始使用 VuePress》介绍了 VuePress 的大致情况。本章将涵盖诸如对 Vue.js 的基本理解、VuePress 是什么、它能做什么（以及不能做什么），以及如何在 VuePress 中开始的概念。此外，本章还介绍了 VuePress 的全局级别和依赖项安装。

第三章《VuePress 开发-初步步骤》包括了与 VuePres 配置数值相关的笔记，包括基本级别和构建流水线。除此之外，本章还包括了 VuePress 中的资源处理和 URL 结构。

第四章《在 VuePress 中创建网站》是我们开始实际网站开发的地方！本章包括了关于如何在 VuePress 中构建关于咖啡的静态网站的逐步教程。该网站的代码（Markdown 和渲染的 HTML）可以在本书的 GitHub 存储库中找到。

第五章《在 VuePress 中使用 Markdown》涵盖了 VuePress 中的 Markdown 使用。如果您对 Markdown 不熟悉，本章还包括了对其语法和格式的简要介绍，以及如何在 VuePress 中使用 Markdown。

第六章《在 VuePress 中进行主题开发》涉及 VuePress 中的主题开发。本章讨论了默认主题定制、导航栏和侧边栏设置、Git 集成以及自定义主题开发等主题。

第七章《在 VuePress 中做更多》以讨论 VuePress 中的国际化开始。它还教你如何在 VuePress 中本地化你的网站，然后讨论了 VuePress 的增长潜力、未来路线图以及与 VuePress 相关的其他因素。

第八章《将 VuePress 部署到网络上》涵盖了将 VuePress 部署到 Heroku、Netlify 和 Surge.sh 等托管提供商的远程服务器上。本章采用了详细的逐步方法，以帮助您轻松部署 VuePress 网站。

# 为了充分利用本书

以下技能、工具和实用程序可能会对您有所帮助，并帮助您充分利用本书：

+   **工作站**：显然，为了真正学习 VuePress 开发，您需要一台计算机，无论是笔记本还是台式机。如果您的计算机内存不多，也不用担心；VuePress 对资源使用并不是很大。

+   **基本编码技能**：您需要对 JavaScript 和 Vue 有基本的了解。如果没有，您可能仍然能够使用 VuePress，尽管需要额外的时间。然而，为了在 VuePress 中构建合法的网站，肯定需要对 Markdown 有一定的了解。

+   **代码编辑器**：建议使用一个好的代码编辑器来更好地格式化您的代码和文件。作者通常使用 Visual Studio Code，但您可以选择任何您喜欢的免费或付费编辑器。

+   **Node.js 和 npm**：运行 VuePress 需要 Node.js 8+。安装方法在本书的第二章中有介绍。

+   **互联网连接和网络浏览器**：这个显而易见！没有浏览器和互联网，您无法构建和部署网站。

# 下载示例代码文件

您可以从您在[www.packt.com](http://www.packt.com)的账户中下载本书的示例代码文件。如果您在其他地方购买了本书，您可以访问[www.packt.com/support](http://www.packt.com/support)注册并直接将文件发送到您的邮箱。

您可以按照以下步骤下载代码文件：

1.  在[www.packt.com](http://www.packt.com)上登录或注册。

1.  选择“支持”选项卡。

1.  点击“代码下载和勘误”。

1.  在搜索框中输入书名，并按照屏幕上的说明进行操作。

下载文件后，请确保使用最新版本的解压软件解压文件夹：

+   WinRAR/7-Zip 适用于 Windows

+   Zipeg/iZip/UnRarX 适用于 Mac

+   7-Zip/PeaZip 适用于 Linux

该书的代码捆绑包也托管在 GitHub 上，网址为[`github.com/PacktPublishing/VuePress-Quick-Start-Guide`](https://github.com/PacktPublishing/VuePress-Quick-Start-Guide)。如果代码有更新，将在现有的 GitHub 存储库上进行更新。

我们还有来自我们丰富的图书和视频目录的其他代码捆绑包，可在[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)上找到。去看看吧！

# 下载彩色图片

我们还提供了一个 PDF 文件，其中包含本书中使用的屏幕截图/图表的彩色图像。您可以在这里下载：[`www.packtpub.com/sites/default/files/downloads/9781789535754_ColorImages.pdf`](http://www.packtpub.com/sites/default/files/downloads/9781789535754_ColorImages.pdf)。

# 使用的约定

本书中使用了许多文本约定。

`CodeInText`：表示文本中的代码单词，数据库表名，文件夹名，文件名，文件扩展名，路径名，虚拟 URL，用户输入和 Twitter 句柄。这是一个例子：“将下载的`WebStorm-10*.dmg`磁盘映像文件挂载为系统中的另一个磁盘。”

代码块设置如下：

```js
module
    .exports = {
        head: [
            [ ... ]
        ]
    }
```

任何命令行输入或输出都是这样写的：

```js
**git add --all**
**git commit -m "initial commit"**
**git push -u origin master** 
```

**粗体**：表示一个新术语，一个重要单词，或者您在屏幕上看到的单词。例如，菜单或对话框中的单词会在文本中出现。这是一个例子：“从管理面板中选择系统信息。”

警告或重要提示会出现在这样的地方。提示和技巧会出现在这样的地方。
