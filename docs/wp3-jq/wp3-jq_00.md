# 前言

这本易于使用的指南将带领您深入了解创建精致的专业增强功能的方方面面，特别定制以充分利用 WordPress 个人发布平台的优势。它将通过清晰的、逐步的说明，指导您构建几种针对不同类型的假想客户的自定义 jQuery 解决方案，并向您展示如何创建一个 jQuery 和 WordPress 插件。

# 本书涵盖内容

*第一章*, *入门：WordPress 和 jQuery*...本章向读者介绍了他们需要熟悉的核心基础知识，以便充分利用本书。涵盖了 HTML、CSS、PHP 和 JavaScript 的语法，以及如何识别这些语法的各个部分，还有一份“行业工具”清单，涵盖了他们的代码编辑器、浏览器甚至图像编辑器应具备的功能。本章还详细说明了 CSS、JavaScript 和 jQuery 如何在浏览器中与从 WordPress 站点提供的 HTML 配合使用。

*第二章*, *在 WordPress 中使用 jQuery*...本章详细介绍了如何在 WordPress 中开始使用 jQuery。它涵盖了如何使用脚本 API 正确地包含 jQuery，并专注于 jQuery 的选择器（在 WordPress 中非常重要），以及 jQuery 的顶级功能。

*第三章*, *深入了解 jQuery 和 WordPress*...本章将读者带入更深层次，介绍了 jQuery 可以应用于 WordPress 站点的各种方式：通过 WordPress 主题中的自定义脚本，作为通过主题调用的 jQuery 插件，以及作为应用于 WordPress 插件的自定义 jQuery 脚本或插件！通过 jQuery 影响 WordPress 站点的方法有很多，本章考虑了每种方法的利弊，以便读者能够准确评估自己的项目。本章还向读者介绍了他们的第一个“假想客户”，并介绍了如何创建自己的 jQuery 插件，然后将该 jQuery 插件封装成 WordPress 插件，以便站点管理员可以轻松实施增强功能，而不必知道如何编辑主题。

*第四章*, *用更少的代码做更多事情: 利用 jQuery 和 WordPress 的插件*...你以为你在 第三章 中学到了不少？抓住你的鼠标。你将开始一个小项目，需要你熟悉流行的 jQuery 插件 Colorbox，以及流行的 WordPress 插件 Cforms II，并且将两者与你自己的自定义 jQuery 魔术融合在一起，以制作一些漂亮的事件注册，会让客户大吃一惊。

*第五章*, *在 WordPress 中使用 jQuery 动画*...如果你要使用 jQuery，最好真正充分利用它，这意味着动画。本章介绍了使用 jQuery 的动画函数和快捷方式来创建一些锐利、精确的视觉增强效果，吸引网站用户的注意力，以及创建一个超级流畅的导航增强和一个精彩的旋转幻灯片展示粘性帖子。

*第六章*, *WordPress 和 jQuery 的 UI*...现在我们已经掌握了一些动画技巧，我们可以通过使用 jQuery 的 UI 插件来更轻松地进行工作，该插件包括我们在 第五章 中学到的 Easing 和 Color 插件。在本章中，我们还将利用 UI 插件的小部件和事件功能，在我们的 WordPress 站点中创建一些非常有用的界面。

*第七章*, *使用 jQuery 和 WordPress 进行 AJAX*...本章介绍了什么是 AJAX，以及在您的 WordPress 站点中开始使用 AJAX 技术的最佳方法；您将加载来自站点其他页面的 HTML，通过 JSON 获取您的推文和喜爱的 flickr 图片，并且 last but not least，自定义 AJAXing 内置的 WordPress 评论表单。

*第八章*, *使用 jQuery 和 WordPress 的技巧和诀窍*...本章介绍了在 WordPress 中充分利用 jQuery 的顶级技巧和诀窍。大多数这些最佳实践都在整个标题中涵盖，但在本章中，我们将看看它们为什么如此重要，特别是在 WordPress 的环境中如何实现它们。

*附录 A*，*jQuery 和 WordPress 参考指南*……将此附录折角，并将其视为您的“备忘单”。一旦您通过本书，为什么要浪费时间来回翻阅，以回忆某个功能的语法及其参数？本书提取了关于 jQuery 和 WordPress 的最重要信息，并将其拆分成易于快速浏览的参考指南，以便您可以轻松找到大多数 jQuery 选择器的语法，提醒自己关于大多数 WordPress 开发所需的顶级 jQuery 函数及其参数，以及有用的 WordPress 模板标记和 API 函数以及其他有用的 WordPress 技巧，如结构化循环和主题模板层次结构。

# 本书所需材料

+   WordPress（2.9.2 或 3.0）

+   jQuery 库（1.4.2）

+   Web 服务器（本地 WAMP 或 MAMP 安装或由提供商托管）

+   一个 Web 浏览器（Firefox 或更好）

+   一个好的代码或 HTML 编辑器

# 本书适合谁

本书适用于任何有兴趣在 WordPress 网站中使用 jQuery 的人。假定大多数读者将是具有相当了解 PHP 或 JavaScript 编程并且至少具有 HTML/CSS 开发经验的 WordPress 开发人员，他们想要学习如何快速将 jQuery 应用于他们的 WordPress 项目。

# 约定

在本书中，您将找到一些区分不同类型信息的文本样式。以下是一些这些样式的示例，以及它们的含义解释。

文本中的代码词如下所示：“我们可以通过使用`include`指令来包含其他上下文。”

代码块设置如下：

```js
<script type="text/javascript">
jQuery("document").ready(function(){
jQuery("p").css("background-color", "#ff6600");
});
</script>

```

当我们希望引起您对代码块的特定部分的注意时，相关的行或项目将以粗体显示：

```js
<script type="text/javascript">
jQuery("document").ready(function(){
jQuery("p").css("background-color", "#ff6600");
});
</script>

```

为了清晰和简洁，本标题中的许多代码示例都是提取的。提取的代码块设置如下：

```js
...
jQuery("p").css("background-color", "#ff6600");
}
...

```

前后用省略号“…”标记的代码和标记是从完整代码和/或更大代码和标记主体中提取的。请参考可下载的代码包以查看整个作品。

**新术语**和**重要单词**以粗体显示。屏幕上看到的单词，例如菜单或对话框中的单词，会出现在文本中，如下所示：“单击**下一步**按钮将您移到下一个屏幕”。

### 注意

警告或重要说明以这样的框出现。

### 注意

提示和技巧显示如下。
