# 附录 B. 开发工具

> 当问题来临时
> 
> 你必须鞭打它
> 
> —Devo，
> 
> "鞭打它"

文档可以帮助解决我们 JavaScript 应用程序的问题，但没有好的软件开发工具来替代。幸运的是，有许多可用于检查和调试 JavaScript 代码的软件包，其中大多数都可以免费获取。

# Firefox 工具

Mozilla Firefox 是大多数 Web 开发人员首选的浏览器，因此拥有一些最全面和备受尊敬的开发工具。

## Firebug

用于 jQuery 开发的*Firebug*扩展不可或缺：

[`www.getfirebug.com/`](http://www.getfirebug.com/)

Firebug 的一些功能包括：

+   一个出色的 DOM 检查器，用于查找文档片段的名称和选择器

+   CSS 操作工具，用于查找页面外观原因并进行更改

+   一个交互式 JavaScript 控制台

+   一个 JavaScript 调试器，可以监视变量和跟踪代码执行

## Web 开发者工具栏

这不仅在 DOM 检查方面与 Firebug 重叠，还包含用于常见任务的工具，如 cookie 操作，表单检查和页面调整。您还可以使用此工具栏快速轻松地为站点禁用 JavaScript，以确保在用户的浏览器功能较差时功能优雅降级：

[`chrispederick.com/work/web-developer/`](http://chrispederick.com/work/web-developer/)

## Venkman

*Venkman*是 Mozilla 项目的官方 JavaScript 调试器。它提供了一个类似于用于调试其他语言编写的程序的 GDB 系统的故障排除环境。

[`www.mozilla.org/projects/venkman/`](http://www.mozilla.org/projects/venkman/)

## 正则表达式测试器

用于在 JavaScript 中匹配字符串的正则表达式可能难以编写。这个 Firefox 扩展允许使用界面轻松地尝试正则表达式以输入搜索文本：

[`sebastianzartner.ath.cx/new/downloads/RExT/`](http://sebastianzartner.ath.cx/new/downloads/RExT/)

# Internet Explorer 的工具

网站在 IE 中的行为通常与其他 Web 浏览器不同，因此对于该平台具有调试工具是重要的。

## 微软 Internet Explorer 开发者工具栏

*开发者*工具栏主要提供了网页的 DOM 树视图。可以通过视觉定位元素，并通过新的 CSS 规则即时修改。它还提供其他杂项开发辅助工具，如用于测量页面元素的标尺：

[`www.microsoft.com/downloads/details.aspx?FamilyID=e59c3964-672d-4511-bb3e-2d5e1db91038`](http://www.microsoft.com/downloads/details.aspx?FamilyID=e59c3964-672d-4511-bb3e-2d5e1db91038)

## 微软 Visual Web Developer

*微软的 Visual Studio*软件包可用于检查和调试 JavaScript 代码：

[`msdn.microsoft.com/vstudio/express/vwd/`](http://msdn.microsoft.com/vstudio/express/vwd/)

要在免费版本（Visual Web Developer Express）中交互式运行调试器，请按照此处概述的过程进行操作：

[`www.berniecode.com/blog/2007/03/08/how-to-debug-javascript-with-visual-web-developer-express/`](http://www.berniecode.com/blog/2007/03/08/how-to-debug-javascript-with-visual-web-developer-express/)

## DebugBar

*DebugBar* 提供了 DOM 检查器以及用于调试的 JavaScript 控制台：

[`www.debugbar.com/`](http://www.debugbar.com/)

## Drip

JavaScript 代码中的内存泄漏可能会导致 Internet Explorer 的性能和稳定性问题。*Drip* 有助于检测和隔离这些内存问题：

[`Sourceforge.net/projects/ieleak/`](http://Sourceforge.net/projects/ieleak/)

# Safari 的工具

Safari 作为开发平台仍处于起步阶段，但仍然可以为代码在此浏览器中的行为与其他地方不同的情况提供可用的工具。

## Web Inspector

Safari 的 Nightly 构建版本包括检查单个页面元素并收集有关每个元素应用的 CSS 规则的信息的功能。

[`trac.webkit.org/projects/webkit/wiki/Web%20Inspector`](http://trac.webkit.org/projects/webkit/wiki/Web%20Inspector)

## Drosera

*Drosera* 是 Safari 和其他基于 WebKit 的应用程序的 JavaScript 调试器。它启用了断点、变量监视和交互式控制台。

# 其他工具

## Firebug Lite

虽然 Firebug 扩展本身仅限于 Firefox web 浏览器，但一些功能可以通过在网页上包含 *Firebug Lite* 脚本来复制。此包模拟了 Firebug 控制台，包括允许调用 `console.log()` 的功能，通常会导致其他浏览器中抛出 JavaScript 错误：

[`www.getfirebug.com/lite.html`](http://www.getfirebug.com/lite.html)

**TextMate jQuery Bundle**

这个为流行的 Mac OS X 文本编辑器 *TextMate* 提供了语法高亮显示 jQuery 方法和选择器、方法代码完成以及代码内快速 API 参考。该包还与 *E* 文本编辑器兼容（适用于 Windows）：

[`www.learningjquery.com/2006/09/textmate-bundle-for-jquery`](http://www.learningjquery.com/2006/09/textmate-bundle-for-jquery)

## Charles

在开发 AJAX 密集型应用程序时，查看浏览器和服务器之间发送的确切数据可能很有用。*Charles* web 调试代理显示两个点之间的所有 HTTP 流量，包括正常的 web 请求、HTTPS 流量、Flash 远程和 AJAX 响应：

[`www.xk72.com/charles/`](http://www.xk72.com/charles/)
