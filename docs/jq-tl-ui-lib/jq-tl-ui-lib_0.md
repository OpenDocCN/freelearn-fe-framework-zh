# 前言

> "让我们面对现实吧——你真的需要在你的网站上拖放、可调整大小的窗口或可排序的列表吗…？"

如果答案是否定的，那么欢迎使用"jQuery Tools UI 库"！

jQuery Tools 是一个紧凑、功能强大的库，包含足够的组件来为任何网站提供最重要的功能。许多 UI 库包含大量组件，如列表框、范围、可排序列表等等。尽管这些可以用于构建各种在线应用程序，在公司内部网络中非常有用，但在构建普通网站时却不是很有用。

网站被设计用来呈现信息和外观良好——jQuery Tools 的设计旨在增强任何使用它们的网站，同时利用现代 JavaScript 技术所能提供的强大功能。使用 jQuery Tools，您不受任何预定义的 HTML、CSS 结构或严格的编程规则的约束——您可以将库包含在您的页面中，并立即开始使用其功能。这些工具被设计成可根据您的喜好进行定制，同时保持组成 JQuery Tools 核心功能。

如果你是 jQuery Tools 的新手，并且想要探索可用的功能，这本书就是为你准备的。通过简单易懂的逐步说明，你将找到启动使用这个库所需的一切，并发现如何只需几行代码就能实现一些复杂功能。

那么让我们开始吧...

# 本书涵盖的内容

第一章, *入门 jQuery Tools UI 库*，向读者介绍了这个被称为"缺失的 Web UI 库"的库。它解释了如何获得 jQuery Tools，概述了您将需要使用此功能开发网页所需的工具，并概述了一些在使用 jQuery Tools 时的最佳实践。

第二章, *与你的 UI 工具相处*，深入探讨了 jQuery Tools UI 库的每个部分，以及如何将基本工具实现到您的网站中。在 UI 库的每个部分中，都包含了一次性演示，以及使用该库可以实现的更高级的示例。

第三章, *表单工具*，介绍了 jQuery Tools 中的表单功能。它概述了如何提交和验证表单中的内容，以及如何使用`RangeInput`输入数字和使用`DateInput`输入日期。它还演示了如何确保所有内容都按照 HTML5 标准正确验证。

第四章，*jQuery 工具箱*，介绍了一小部分工具，这些工具在大多数情况下可以单独使用，也可以作为库中主要工具的一部分。它指出，尽管一些技术正在过时（由于 HTML、CSS3 和 JavaScript 的现代进步），但它们仍然可以在项目中执行一些有用的功能。

*在 WordPress 中使用 jQuery 工具* 是 Packt 网站上可供下载的额外 PDF，它附带在本书中。其中包含一些有关在内容管理系统的限制下使用 jQuery 工具的有用想法和示例。尽管示例是基于著名和流行的 WordPress™ 系统，但这些原则也可以轻松地应用于其他类似的系统。

# 这本书适合谁？

对于 jQuery 工具库的新手来说，这本书非常适合。假设你对该库没有任何先前知识，但可能有基本的 JavaScript 语法和概念知识。本书将使您掌握使用该库的基础知识，并了解如何使用它来构建引人注目、可定制的网页。

# 约定

在这本书中，你会发现一些不同种类信息之间的文本样式。以下是这些样式的一些示例，以及它们的含义解释。

文本中的代码字词显示如下："我们现在将构建一个名为 `myEffect` 的自定义效果，然后将其添加到叠加代码中。"

代码块设置如下：

```js
<!-- first overlay. id attribute matches our selector -->
<div class="simple_overlay" id="mies1">
<!-- large image -->
<img src="img/barcelona-pavilion-large.jpg" />
<!-- image details -->
<div class="details">
<h3>The Barcelona Pavilion</h3>
<h4>Barcelona, Spain</h4>
<p>The content ...</p>
</div>
</div>

```

当我们希望引起您对代码块的特定部分的注意时，相关行或项将以粗体显示：

```js
<!-- first overlay. id attribute matches our selector -->
<div class="simple_overlay" id="mies1">
<!-- large image -->
<img src="img/barcelona-pavilion-large.jpg" />
<!-- image details -->
<div class="details">
 <h3>The Barcelona Pavilion</h3>
<h4>Barcelona, Spain</h4>
<p>The content ...</p> 
</div>
</div>

```

**新术语**和**重要词汇**以粗体显示。屏幕上看到的字词，比如菜单或对话框中的内容，会以这种方式出现在文本中："点击**下一步**按钮将您移到下一个屏幕。"

### 注意

警告或重要提示会以这样的框形式出现。

### 提示

贴士和技巧会以这种形式出现。
