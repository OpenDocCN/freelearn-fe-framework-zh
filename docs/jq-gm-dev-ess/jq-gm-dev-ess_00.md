# 前言

编写游戏不仅有趣，而且是通过透彻地学习一项技术的非常好的方法。尽管 HTML 和 JavaScript 并不是为了运行游戏而设计的，但在过去的几年中，一系列事件发生，使得用 JavaScript 编写游戏成为可行的解决方案：

+   浏览器的 JavaScript 引擎性能有了显著提高，现代引擎比 2008 年的最先进引擎快了十倍。

+   jQuery 和其他类似的库使得与 DOM 的操作尽可能轻松。

+   Flash 在某种程度上由于在 iOS 上的缺失而失去了很多地位。

+   W3C 开始了许多面向游戏的 API 的工作，如 canvas、WebGL 和全屏 API。

在整本书中，你将制作三款游戏并学习各种技术。你不仅可以使用自己的游戏，更重要的是你将在过程中获得乐趣！

# 本书内容

第一章, *游戏中的 jQuery*，深入探讨了对游戏开发可能有用的 jQuery 函数。

第二章, *创建我们的第一个游戏*，使用精灵、动画和预加载实现了一个简单的游戏。

第三章, *更好、更快、但不更难*，通过各种技术如超时内联、键盘轮询和 HTML 片段优化了我们在第二章中看到的游戏*创建我们的第一个游戏*。

第四章, *横向看*，用瓷砖地图和碰撞检测编写了一个平台游戏。

第五章, *物以类聚*，创建了一个正交 RPG 游戏，采用了瓦片地图优化、精灵遮挡和更好的碰撞检测。

第六章, *给你的游戏添加关卡*，通过使用 JSON 和 AJAX 添加多个关卡，扩展了我们在第四章中看到的游戏*横向看*。

第七章, *制作多人游戏*，将我们在第五章中看到的游戏转变为支持多台机器上的多个玩家。

第八章, *让我们变得社交化*，将平台游戏与 Facebook 和 Twitter 集成，并创建一个防作弊的排行榜。

第九章, *让你的游戏移动起来*，将我们在第五章中看到的游戏优化为适用于移动设备和触摸控制。

第十章, *制造一些声音*，通过音频元素、Web Audio API 或 Flash，为你的游戏添加音效和音乐。

# 本书的需要

使用 web 技术的一个优势是，你无需任何复杂或昂贵的软件即可开始。对于纯粹的客户端游戏，你只需要你最喜欢的代码编辑器（甚至是一个简单的文本编辑器，如果你不介意在没有任何语法高亮的情况下工作）。如果你还没有选择的话，那么你可以试试周围的免费软件，从非常老式的软件，比如 VIM（[`www.vim.org/`](http://www.vim.org/)）和 Emacs（[`www.gnu.org/software/emacs/`](http://www.gnu.org/software/emacs/)）到更现代的软件，比如 Eclipse（[`www.eclipse.org/`](http://www.eclipse.org/)）和 Aptana（[`www.aptana.com/`](http://www.aptana.com/)），Notepad++（[`notepad-plus-plus.org/`](http://notepad-plus-plus.org/)），或者 Komodo Edit（[`www.activestate.com/komodo-edit`](http://www.activestate.com/komodo-edit)）。这些只是你可以找到的一些可用编辑器。对于 JavaScript，你不需要一个非常先进的编辑器，所以只需使用你更熟悉的那个。

如果你创建自己的图形，你还需要一款图像编辑软件。在这方面，你会有很多选择。最著名的开源软件是 Gimp（[`www.gimp.org/`](http://www.gimp.org/)），还有我个人最喜欢的 Pixen（[`pixenapp.com/`](http://pixenapp.com/)）。

对于书中需要一些服务器端脚本的部分，我们将会使用 PHP 和 MySQL。如果你还没有支持它们的服务器，你可以在你的机器上安装它们，你可以使用 MAMP（[`www.mamp.info/`](http://www.mamp.info/)）、XAMPP（[`www.apachefriends.org/en/xampp.html`](http://www.apachefriends.org/en/xampp.html)），或者 EasyPHP（[`www.easyphp.org/`](http://www.easyphp.org/)），具体根据你的操作系统来选择。

# 本书适合人群

本书的主要观众是有一些 JavaScript 和 jQuery 经验的初学者 web 开发人员。由于服务器端部分是用 PHP 实现的，如果你对 PHP 也有一些了解的话会有所帮助，但是如果你更喜欢其他服务器端语言的话，你也可以使用其他语言代替 PHP 而不会有太多麻烦。

你完全不需要任何游戏开发的先验知识就能享受本书！

# 约定

在这本书中，你会发现许多不同种类的信息的文本样式。以下是其中一些样式的例子，以及它们的含义解释。

文本中的代码单词显示如下: "jQuery 的`.animate()`函数允许你让一个属性从当前值按照时间变化到一个新值。"

一个代码块设置如下：

```js
$("#myElementId")
.animate({top: 200})
.animate({left: 200})
.dequeue();
```

当我们想吸引你的注意到代码块的特定部分时，相关的行或项用粗体表示：

```js
gf.keyboard = [];
// keyboard state handler
 $(document).keydown(function(event){
 gf.keyboard[event.keyCode] = true;
});
$(document).keyup(function(event){
    gf.keyboard[event.keyCode] = false;
});
```

任何命令行输入或输出都如下所示：

```js
# cp /usr/src/asterisk-addons/configs/cdr_mysql.conf.sample
     /etc/asterisk/cdr_mysql.conf
```

**新术语**和**重要词汇**以粗体显示。例如，在屏幕上看到的单词，在菜单或对话框中出现的单词，将以这样的方式出现在文本中："下图显示了两个段 **a** 和 **b** 的一维交集 **i** 的典型情况"。

### 注意

警告或重要提示将以这样的方式出现在一个框中。

### 提示

提示和技巧会以这样的方式出现。
