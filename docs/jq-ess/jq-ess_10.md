# 第十章：利用插件充分利用他人的工作

在上一章中，我们学习了如何计时我们的代码，然后了解了如何改进我们的 jQuery 代码的性能。完成这些后，让我们把注意力转向第十章，关于 jQuery 插件的第十章。插件坚持 jQuery 的口号“写得更少，做得更多”。它们使您能够利用他人的工作，并轻松地将他们的工作插入到您的应用程序中。

在本章中，我们将学习关于 jQuery 插件的知识。jQuery 的核心是其原型对象。插件是一个扩展了 jQuery 原型对象的对象，使得所有 jQuery 对象都能够获得新的功能。jQuery 有一个官方支持的一组 UI 插件，称为 jQuery UI。有数千个免费插件可用，但要找到好的插件需要耐心和谨慎。在本章中，我们将涵盖以下内容：

+   查找和安装插件

+   jQuery UI

+   编写您自己的插件

+   插件最佳实践

# 查找插件

如果你在 jQuery 的任何网站上点击**插件**菜单，你将被带到 jQuery 插件注册页面。虽然该网站确实有很多插件，但它们都很老旧，多年未更新。不用担心，jQuery Foundation 的人们决定鉴于他们有限的资源，没有必要自己打包插件。互联网已经有几个流行的包管理器；其中两个比较流行的是 npm 和 Bower。jQuery 团队建议插件发布者切换到使用 npm。

Node Package Manager，或者 npm，最初只是为 Node.js web 框架提供包。但它们易于使用和原生跨平台的能力使得 npm 被广泛应用为各种应用程序的包管理器。许多命令行工具、移动框架和其他实用程序应用程序都以 npm 模块的形式实现。难怪 jQuery 团队将其作为 jQuery 插件的首选包管理器。

查找 jQuery 插件在 npm 上很容易。只需转到[`www.npmjs.com/`](https://www.npmjs.com/)网站。在搜索框中，输入`jquery-plugin`。在撰写本文时，已有超过 1200 个 jQuery 插件可用。找到插件很容易；难的是决定使用哪个插件。

想象一下你正在寻找一个工具提示插件。忽略，以本例为例，jQuery UI 库中是否有插件。你首先要做的事情是在 npm 搜索栏中输入`jquery-plugin tooltip`。在决定在你的代码中使用插件之前，你应该问自己哪些问题？首先可能是项目是否正在积极维护？其他问题可能包括它是否具有通过的单元测试？源代码是否干净且写得好？它是否依赖于其他插件？是否有清晰的带有示例代码的文档？它使用何种许可证？是否有任何未解决的问题？如果源代码在 GitHub 上，是否有任何星标？

只有在你做了尽职调查并确信这是一个高质量的插件后，才应该在你的代码中使用它。向 npm 添加插件的门槛非常低，所以有很多糟糕的插件。有些构建得很差，有些是老旧的且未被维护，甚至可能有一些恶意的。

### 提示

npm 管理的是包，而不是插件。jQuery 插件是一种特殊类型的 npm 包。在本章中，我将使用“插件”这个词，但实际上指的是一个包含 jQuery 插件的 npm 包。

# 安装插件

你已经找到了一个或两个喜欢的插件，那么接下来呢？通过 npm 安装插件也很容易，但首先你需要确保你的应用程序根目录下有一个 `package.json` 文件。这是 npm 要求的一个 JSON 文件。如果没有 `package.json` 文件，npm 将不会安装你的插件。以下是一个相当简约的 `package.json` 文件的示例：

```js
{
  "name": "plugins",
  "version": "1.0.0",
  "description": "A demo of using and writing plugins",
  "repository": "none",
  "license": "MIT",
  "dependencies": {
    "tipso": "¹.0.6"
  }
}

```

请注意它是一个 JSON 文件，而不是 JavaScript 对象。第一个字段是应用程序的名称。名称是一个必需的字段；如果没有，npm 将不会安装包。如果你正在创建一个插件并计划将其上传到 npm，那么名称必须是唯一的。

第二个字段是应用程序的版本号。这也是必需的。在创建自己的插件时，放置在这里的值非常重要。是否升级插件是基于 npm 存储的此字段值与用户本地副本进行比较而确定的。

接下来的三个字段不是必需的，但如果在安装插件时缺少它们，将会生成警告。第一个是描述字段，它是描述应用程序的简短语句。然后是存储库字段。如果存在有效的存储库，它将包含一个子对象，其中包含两个字段：类型和 URL。类型标识使用的源控制类型，如 `git` 和 `svn`。最后，有许可证字段，它是插件发布的软件许可证类型。在你的应用程序根目录下创建一个 `package.json` 文件。

在安装 npm 包之前，你需要安装 Node.js。npm 已经包含在 Node.js 中，所以请前往 [`nodejs.org/`](https://nodejs.org/) 下载并安装适合你系统的版本。安装完 Node 后，你会想要升级 npm。我知道这看起来很奇怪，但 Node 和 npm 是不同的发布周期，Node 自带的 npm 版本通常已经过时了。

要升级 npm，请输入：

```js
npm install npm -g

```

在 Mac 和 Linux 系统上，你可能需要使用 `sudo` 命令。安装并升级完 npm 后，你终于准备好安装一个插件了。在终端或命令提示符下输入以下内容：

```js
npm install <name of package> --save

```

示例`package.json`文件显示了另一个字段：dependencies。这是一个键值对的字典。键是您的应用程序依赖的软件包的名称，而值通常是它的版本号。在安装软件包时，如果在命令的末尾加上`--save`，此字段会自动生成。

使用`sudo`命令时需要特别小心。它以 root 权限执行命令。如果要执行的命令有恶意意图，它可以做几乎任何事。您可以将自己的帐户设置为 npm 安装软件包的目录（/user/local）的所有者，而不使用`sudo`命令。只需执行以下命令：

```js
sudo chown –R $USER /usr/local

```

这是更改所有者（`chown`）命令。它会将您的帐户设置为`/usr/local 目录`的所有者。`–R`告诉`chown`递归遍历所有子目录，使您的帐户也成为它们的所有者。

## 更新插件

偶尔，您的应用程序所依赖的软件包将得到改进。为了升级所有依赖项，您可以运行升级命令，而不指定软件包。

```js
npm update --save

```

此命令将检查`package.json`文件中的每个软件包，并更新所有过时的软件包。完成后，它还将使用新的版本号更新`package.json`文件。

如果您希望对使用更新命令更有针对性一些，可以提供要更新的软件包的名称。

```js
npm update <name of package> --save

```

此命令将仅更新命令中指定的软件包。如果更新了软件包，它将在`package.json`文件中更新其版本号。

## 卸载插件

如果您需要删除插件，可以使用`uninstall`命令。`uninstall`命令将通过从`node_modules`中删除所有相关文件并更新`package.json`文件来删除一个软件包。在执行此命令之前请三思，因为此操作不可逆转。如果使用`–save`选项，它还将更新`package.json`文件。

```js
npm uninstall <package-name> --save

```

## 添加插件

现在我们知道如何安装、更新和移除 npm 软件包了，让我们为我们的应用程序添加流行的`m-popup`插件。它是一个创建轻量级和可定制模态弹出窗口的插件。您可以在[npm](https://www.npmjs.com/package/m-popup)上找到它。这是插件的主页，您会在这里找到有关插件的大量信息。通常包含作者的姓名、许可证类型、编码示例和安装说明。安装说明通常在右上角。要安装`m-popup`，输入以下命令：

```js
npm install m-popup

```

请注意从应用程序的根目录执行该命令，并请注意这里没有`–g`。`–g`选项仅在全局安装软件包时使用，但这里不适用。安装插件时，是从位于`package.json`文件的根目录进行操作的。

在安装期间，npm 会创建一个目录，`node_modules`，如果之前不存在的话。在其中，将创建另一个目录，`m-popup`。目录的名称始终是包的名称。这也是为什么 npm 包必须具有唯一名称的部分原因。

每个包的内容都不同，因此您可能需要四处探索以找到您需要的文件；通常它们将位于名为 `dist` 或可能是名为 `src` 的目录中。我们正在寻找需要添加到我们的应用程序中的插件工作所需的文件。包页面上的说明通常会告诉我们文件的名称，但不会告诉我们它们所在的目录。在我们的情况下，我们需要两个文件，一个 CSS 和一个 JS，它们都在 `dist` 目录中。

```js
<!DOCTYPE html>
<html>
<head lang="en">
  <meta charset="UTF-8">
  <link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.2/css/bootstrap.min.css" rel="stylesheet"/><script src="img/jquery.js"></script>
  <script src="img/bootstrap.min.js"></script>
  <!-- These are the includes for the popup, one for CSS the other for JS -->
  <link rel="stylesheet" href="node_modules/m-popup/dist/mPopup.min.css"/>
  <script src="img/mPopup.jquery.min.js"></script>
  <style>
    .mPopup {
      /* popup modal dimensions */
      width: 60%;height: 300px;
    }
  </style>
  <title>Chapter 10 - Adding a Plugin</title>
</head>
<body>
<div class="jumbotron">
  <div class="container">
    <h1>Chapter 10</h1>
    <p>Adding a jQuery plugin is an easy way to add more functionality to your site.</p>
    <p><a id="displayPopup" class="btn btn-primary btn-lg" href="#" role="button">Display Popup</a></p>
  </div>
</div>
<!-- this is the popup's markup -->
<div id="sample1" class="mPopup">
  <button class="mPopup-close">×</button>
  <div class="popup-header">Popup title</div>
  <div class="popup-body">
    Content goes here.
    Dismiss popup by clicking the close x in the upper right corner or by clicking on the greyed out background.</div>
</div>
<script type="text/javascript">
  function init() {
    var popup = $('#sample1').mPopup();
    var button = $('#displayPopup').on('click',function(){
      popup.mPopup('open');
    });
  }
  $(document).ready(init);
</script>
</body>
</html>
```

此代码链接了 m-popup 的 CSS 文件和其 JavaScript 文件，然后创建了一个 CSS 类，该类设置了弹出模态框的宽度和高度。最好将 CSS 移动到自己的文件中。接下来，我们在标记的末尾添加了一些 HTML，就在脚本标记之前。

这是定义弹出窗口的 HTML。类 `mPopup` 还使标记在页面上最初处于隐藏状态。该插件定义了两个部分，标题和正文，分别由类 `popup-header` 和 `popup-body` 表示。

激活插件的代码非常简单。

代码等待文档就绪事件，然后调用 `init` 方法。在 `init` 方法中，我们获取对我们的弹出窗口的引用，并钩住按钮的点击事件。当单击按钮时，我们使用字符串 `open` 调用 `mPopup` 方法，这是我们调用的 `popup` 方法的名称。要退出模态框，请单击模态框右上角的 **close** 按钮或在灰色覆盖层的任何位置。

此插件还可以执行许多其他操作。要了解更多信息，请阅读 npm 上的插件包页面。您甚至可能想要研究其源代码。

# jQuery UI

jQuery 团队管理着称为 jQuery UI 的一组 UI 小部件、交互、效果和主题。该集合是一组插件。jQuery UI 的主页是 [`jqueryui.com/`](http://jqueryui.com/)。让我们快速了解一下 jQuery UI 是什么以及如何使用它。

jQuery UI 由四个主要组件组成：交互（interactions）、小部件（widgets）、效果（effects）和核心（core）。只有核心是必需的，因此下载系统允许您仅选择您想要的组件，以创建自定义版本。

## jQuery UI 交互

交互是使页面元素活跃并能够以新方式移动的方法。例如，您可以使可拖放的 div。其他交互方式包括：可调整大小的、可选择的和可排序的。交互可以单独或组合使用，并帮助您使您的网站流动和交互式。与大多数 jQuery UI 一样，交互很容易使用。让我们看看有多容易：

```js
<!DOCTYPE html>
<html>
<head lang="en">
  <meta charset="UTF-8">
  <link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.2/css/bootstrap.min.css" rel="stylesheet"/><script src="img/jquery.js"></script>
  <script src="img/bootstrap.min.js"></script>
  <link href="jquery-ui/jquery-ui.min.css" rel="stylesheet">
  <script src="img/jquery-ui.min.js"></script>
  <title>Chapter 10 - jQuery UI Widget Factory</title>
  <style>
    .box {
      width: 100px;
	  height: 100px;
	  background-color: pink;
	  margin: 5px;
    }
  </style>
</head>
<body>
<div class="jumbotron">
  <div class="container">
    <h1>Chapter 10</h1>
    <p>Interactions allow you to create an interactive and fluid site. Click and drag the boxes around the page.</p>
      <div class="box"><h1>1</h1></div>
      <div class="box"><h1>2</h1></div>
      <div class="box"><h1>3</h1></div>
      <div class="box"><h1>4</h1></div>
  </div>
</div>
<script type="text/javascript">
  function init() {
    $('.box').draggable();
  }
  $(document).ready(init);
</script>
</body>
</html>
```

让我们通过这个示例走一遍，并确保我们理解它在做什么。为了使用 jQuery UI，我们包含它的 CSS 和 JavaScript 文件。然后我们对 div 进行内联 `<style>`，使其看起来像粉色框。

在 body 部分，我们布局我们的页面，在一个容器 div 内创建了四个粉色框。尽管它是一个容器 div，在我们拖动它们时，它不会包含这些框。这些框只受浏览器窗口的约束。

在 JavaScript 中，我们等待文档就绪事件；我们调用 `init()` 方法，然后对每个类为 `box` 的 div 调用 `draggable`。在浏览器中呈现时，此示例允许您将带编号的框移动到浏览器窗口内的任何位置。

## jQuery UI 小部件

小部件是交互式和可自定义的 UI 元素。jQuery UI 自带 12 个小部件。与 HTML 元素不同，jQuery UI 的所有小部件都可以进行主题化，这意味着它们可以被设计成与您网站的设计相匹配的样式和颜色。按字母顺序排列，它们是：手风琴、自动完成、按钮、日期选择器、对话框、菜单、进度条、选择菜单、滑块、微调器、标签页和工具提示。

jQuery UI 小部件的一个好处是，与 HTML 元素不同，它们是可自定义和主题化的。你可以让所有 jQuery UI 小部件匹配。一个可能的问题是，并不是每个元素都有一个 jQuery UI 的等效物。一个明显的缺失是输入元素。但幸运的是，解决这个遗漏并不困难。

```js
<!DOCTYPE html>
<html>
<head lang="en">
  <meta charset="UTF-8">
  <link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.2/css/bootstrap.min.css" rel="stylesheet"/>
  <script src="img/jquery.js"></script>
  <script src="img/bootstrap.min.js"></script>
  <link href="jquery-ui/jquery-ui.min.css" rel="stylesheet">
  <link href="jquery-ui/jquery-ui.theme.min.css" rel="stylesheet">
  <script src="img/jquery-ui.min.js"></script>
  <style>
    label {
      display: block;
      margin: 30px 0 0 0;
    }

    .form-width {
      width: 200px;
    }

    /* fixes the issues with the input that the button causes */
    .styled-input {
      color: inherit;
      cursor: text;
      font: inherit;
      text-align: inherit;
    }
  </style>
  <title>Chapter 10 - jQuery UI Widgets</title>
</head>
<body>
<div class="jumbotron">
  <div class="container">
    <h1>Chapter 10</h1>
    <p>Widgets give your site a unified themed looked.</p>

    <fieldset>
      <label for="form-select">Salutation</label>
      <select name="form-select" id="form-select" class="form-width">
        <option selected="selected">Mr.</option>
        <option>Miss</option>
        <option>Mrs.</option>
        <option>Ms.</option>
        <option>Dr.</option>
      </select>

      <label for="form-input">Last Name</label>
      <input id="form-input" class="styled-input form-width" name="value">

      <label for="form-button">Submit Form</label>
      <button type="submit" id="form-button" class="form-width">Submit</button>
    </fieldset>
  </div>
</div>
<script type="text/javascript">
  function init() {
    $("#form-select").selectmenu();
    $("#form-button").button();
    // make the input a jQuery UI widget so that it matches our theme
    $("#form-input").button();
  }
  $(document).ready(init);
</script>
</body>
</html>
```

前面的代码创建了三个元素。两个 jQuery UI 元素：select 和 button。还创建了一个输入元素。虽然没有 jQuery UI 输入小部件，但这对我们来说不是问题。我们在输入上使用按钮的创建方法。这基本上有效，但有一些不愉快的副作用。按钮的标签居中，所以这也使我们的输入居中。此外，按钮使用指针光标样式，但输入通常具有文本插入符光标。我们用一个类 "styled-text" 修复这些以及其他几个小问题。最后，我们有三个样式化的输入框，全部与我们网站的主题相匹配。

## jQuery UI 小部件工厂

在下一节中，我们将只使用 jQuery 和 JavaScript 编写自己的 jQuery 插件，但在我们这样做之前，让我们看一看另一种编写插件的方法，使用 jQuery UI 小部件工厂。小部件，与常规的 jQuery 插件不同，对它们强制执行标准结构。这是好的，因为它使它们更容易编写，但缺点是为了使用它们，用户必须同时拥有 jQuery 和 jQuery UI 核心，而不只是拥有 jQuery。

您通过将它们传递给小部件工厂来创建小部件。小部件是 JavaScript 对象。它们必须有一个名为`_create`的属性，该属性必须是一个函数。这将是用于实例化小部件的函数。`create`函数被传递一个`this`对象，该对象有两个属性：`this.element`是指向当前元素的 jQuery 对象。它还传递了`this.options`，这是一个保存当前所有选项值的对象。为了让这更清晰些，让我们看一些代码：

```js
<!DOCTYPE html>
<html><head lang="en">
  <meta charset="UTF-8">
  <link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.2/css/bootstrap.min.css" rel="stylesheet"/><script src="img/jquery.js"></script>
  <script src="img/bootstrap.min.js"></script>
  <link href="jquery-ui/jquery-ui.min.css" rel="stylesheet">
  <script src="img/jquery-ui.min.js"></script>
  <title>Chapter 10 - jQuery UI Widget Factory</title>
</head>
<body>
<nav class="navbar navbar-inverse navbar-fixed-top">
  <div class="container">
    <div class="navbar-header">
      <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#navbar"aria-expanded="false" aria-controls="navbar">
        <span class="sr-only">Toggle navigation</span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
      </button>
      <a class="navbar-brand" href="#">Measuring jQuery</a>
    </div>
    <div id="navbar" class="navbar-collapse collapse">
      <form class="navbar-form navbar-right">
        <div class="form-group">
          <input type="text" placeholder="Email" class="form-control">
        </div>
        <div class="form-group">
          <input type="password" placeholder="Password" class="form-control">
        </div>
        <button type="submit" class="btn btn-success">Sign in</button>
      </form>
    </div>
  </div>
</nav>
<div class="jumbotron">
  <div class="container">
    <h1>Chapter 10</h1>
    <p>Writing your own jQuery UI widget is pretty easy. The extra benefit is that you can use your widget in all ofyour apps or give it to the community.</p>
    <p><a class="btn btn-primary btn-lg" href="#" role="button">Learn more "</a></p>
  </div>
</div>
<div class="container">
  <!-- Example row of columns --><div class="row">
    <div class="col-md-4 bosco">
      <h2>First</h2>
      <p>I am the first div. Initially I am the on the left side of the page. </p>
      <p><a class="btn btn-default" href="#" role="button" name="alpha">View details "</a></p>
    </div>
    <div id="testClass" class="col-md-4 ">
      <h2>Second</h2>
      <p>I am the second div. I begin in-between the other two divs. </p>
      <p><a id='find-me' class="btn btn-default find-me" href="#" role="button" name="beta">View details "</a></p>
    </div>
    <div class="col-md-4">
      <h2>Third</h2>
      <p>I am the third div. Initially I am on the right side of the page</p>
      <p><a class="btn btn-default" href="http://www.google.com" role="button" name="delta">View details "</a></p>
    </div>
  </div>
</div>
<script type="text/javascript">
  function init() {
    // create the yada yada widget
    $.widget("rnc.yadayada", {
      // default optionsoptions: {len: 99,min: 50,message: 'yada yada ' // the default message},
      // passed the this context pointing to the element_create: function () {// we only operate if the element has no children and has a text functionif (!this.element.children().length && this.element.text) {
          var currentLength = this.element.text().length;currentLength = currentLength == 0 ? this.options.min : currentLength;
          var copy = this._yadaIt(this.options.message, currentLength);
          this.element.text(copy);
          this.options.len = copy.length;
        }
      },
      _yadaIt: function (message, count) {
        var ndx, output = "", msgLen = message.length;
        for (ndx = 0; ndx < count; ndx += 1) {
          output = output.concat(message.substr(ndx % msgLen, 1));}
        console.log("output = " + output);
        return output;
      },// Create a public method.
      len: function (newLen) {
        console.log("len method");
        // No value passed, act as a getter.
        if (newLen === undefined) {
          return this.options.len;
        }
        // Value passed, act as a setter.
        this.options.len = newLen;
      },
      // begin the name with the '_' and it is private and// can't be called from the outside_privateLen: function(){
        console.log('_privateLen method');
        return this.options.len;}
    });
    // convert the <a> tags to yadas
    $('a').yadayada();
    // get the length of the second yada
    var len = $('a').eq(2).yadayada('len');
    console.log('len = ' + len);
    // this code won't work
    //len = $('a').eq(2).yadayada('_privateLen');
    //console.log('private len = ' + len);
  }
  $(document).ready(init);
</script>
</body>
</html>
```

程序通过等待文档准备就绪事件来启动。一旦接收到该事件，它就会调用其`init`方法。我们通过调用`$.widget()`来创建小部件，这是小部件工厂。在对象内部，我们必须定义一个`_create()`方法。`create`方法使用`this`上下文持有两个值：元素和选项。元素是指向当前元素的 jQuery 对象。重要的是要注意，这始终是单个元素。即使原始选择器引用了多个元素，小部件工厂也会一次将它们传递给我们。选项包含您小部件的默认值，如果您创建了它并且用户没有覆盖它们。

小部件可能定义其他方法。私有方法必须以下划线开头。私有方法在小部件外部不可见。尝试调用私有方法将生成错误。任何方法如果没有下划线作为第一个字符，则为公共方法。公共方法以一种相当不寻常的方式被调用。方法名称以字符串形式传递给小部件函数，如下所示：

```js
var len = $('a').eq(2).yadayada('len');
```

示例中的小部件有点儿异想天开。它用短语`yada yada`替换元素中的文本。只有在元素没有任何子元素并且具有文本函数的情况下才会如此操作。用户可以用更个性化的信息替换`yada yada`。该小部件还具有一个名为`len`的公共方法，该方法将返回渲染消息的长度。

小部件工厂可能是编写插件的最简单方法。具有更严格结构的工厂比常规插件更易于安装和使用，我们将在下面看到。

## 编写自己的插件

如果您想编写一个可以与全世界共享的插件，您最好创建一个常规的 jQuery 插件。它比 jQuery UI 小部件稍微复杂一些，但不需要 jQuery UI，可以轻松上传到 npm，并与世界共享。所以让我们将我们的`yada yada`小部件变成一个插件。

### 准备工作

在我们真正开始之前，我们需要一些工具等待；其中之一是 Node.js。如果之前没有安装它，现在应该这样做，同时要确保更新 npm。安装完成后，您需要登录。首先，查看 npm 是否显示您已登录：

```js
npm whoami

```

如果 npm 显示您的用户名，则说明没问题。如果没有，则需要添加自己：

```js
npm adduser

```

您将被要求输入用户名、密码和电子邮件地址。电子邮件地址将是公开的。浏览 npm 网站的任何人都将能够看到它。您应该只需要执行一次此操作。您的凭据将存储在一个隐藏文件中以供将来参考。准备工作完成后，让我们制作我们的插件然后发布它。

### 插件

插件的结构与 jQuery UI 小部件的结构非常不同。首先，让我们看看代码，然后我们将逐步进行，以便我们可以理解它在做什么。

```js
;(function ($) {
  //
  $.fn.yadayada = function (options) {
    var settings = $.extend({
      min: 13,
      message: 'yada yada '
    }, options);

    return this.each(function () {
      var $element = $(this);
      if (!$element.children().length && $element.text) {
        var currentLength = $element.text().length;
        currentLength = currentLength == 0 ? settings.min : currentLength;
        var copy = yadaIt(settings.message, currentLength);
        $element.text(copy);
      }
    });
  };

  //
  function yadaIt(message, count) {
    var ndx, output = "", msgLen = message.length;

    for (ndx = 0; ndx < count; ndx += 1) {
      output = output.concat(message.substr(ndx % msgLen, 1));
    }
    console.log("output = " + output);
    return output;
  }

}(jQuery));
```

插件通常以分号开头。这是一种安全预防措施。如果插件用于被缩小的网站，并且在它之前的文件忘记添加终止分号，那么两个文件中的代码将以不可预测但不好的方式合并在一起。如果存在此问题，则添加分号会修复此问题，如果不存在则不会造成任何伤害。

整个插件都包装在 IIFE（即立即调用的函数表达式）中。IIFE 允许我们保护我们的代码免受调用的任何环境的影响。除了通过预定义的接口之外，IIFE 外部的任何东西都不能影响它。在这种情况下，接口是我们传递的单个变量，即 jQuery 变量。请注意，我们将其拼写出来，而不仅仅是假设它被分配给美元符号；它可能不是。通过传递它，我们可以将其分配给美元符号，用于我们的插件。

我们只使用一次 jQuery 变量来创建我们的插件。传统上，插件只将一个函数分配给 jQuery 原型。虽然没有什么能阻止你这样做，但这被认为是不好的。

插件的实际代码内部，我们首先处理选项。在 jQuery UI 小部件中，框架会做将用户选项合并到您的选项中的繁重工作。在插件中，我们必须自己处理。幸运的是，我们可以通过使用`$.extend()`方法让 jQuery 来处理繁重的工作。在这里，我们创建我们的默认值，然后将用户的值与它们合并。参数的顺序非常重要；项目从右向左复制。

接下来，我们设置返回`this`对象。如果我们不返回`this`，用户将无法链式使用我们的插件。小部件工厂每次向我们发送一个元素进行操作。不幸的是，对于插件，我们没有这么幸运：我们必须自己迭代。同样，我们让 jQuery 来处理繁重的工作。我们使用`$.each()`方法。此方法每次向我们发送一个元素。这些元素是实际的 DOM 元素，因此我们将它们转换为 jQuery 对象，因为代码最初是为它们编写的。剩下的大部分代码基本上与小部件中的代码相同。

如前所述，`package.json`文件是必需的。名称和版本号字段是必需的，但您应尽可能填写更多字段。这将帮助用户决定这是否是适合他们的正确插件。这是我们的`package.json`文件：

```js
{
  "name": "yada-yada",
  "version": "0.0.1",
  "description": "An example of writing a jquery plugin",
  "repository": "none",
  "license": "MIT",
  "author":"Troy Miles",
  "main":"jquery.yada-yada.js",
  "keywords": "jquery-plugin,ecosystem:jquery,yada-yada",
}
```

除了我们包含的必需字段，我们还包括`description`、`repository`、`license`、`author`、`main`，这样用户就可以知道`main`文件是什么，以及`keywords`，这样我们可以被寻找到想要找 jQuery 插件的人。

现在我们既有代码又有`package.json`文件，让我们将工作发布到 npm。请记住，如果你决定发布此代码，我已经声明了`yada-yada`这个名字，所以你不能使用它。你必须想出一个独特的名字。进入包含你的插件和`package.json`文件的目录。为了发布，只需输入：

```js
npm publish

```

如果你做得一切正确，在几分钟后 npm 会显示你插件的名称和版本号，就这样。然后转到[`www.npmjs.com/`](https://www.npmjs.com/)，在搜索框中输入你的插件名称，它应该出现在结果列表中。

### 最佳实践

如果一切顺利，你的插件可能会被世界各地的很多人使用，所以重要的是它是精心编写的。以下是一些提示，可以帮助你的插件发挥全部潜力。

#### 保留链接

链接是 jQuery 最好的特性之一。它允许开发人员以一个整洁的包进行一切操作。每个 jQuery 开发者都在使用它，所以如果你破坏了链接，他们就不会喜欢你的插件。

#### 使用一个 IIFE

你不可能知道你的插件将在什么样的环境中使用。将代码包裹在 IIFE 中可能看起来是不必要的，但它有助于保持你的代码不影响其他代码，反之亦然。

#### 在 jQuery 中只添加一个函数

你的插件可能是自瑞士军刀以来最了不起的东西，但你还是只能使用一个函数。即使我们的小示例插件不需要额外的函数，你也只能使用一个函数。如果你需要更多的函数，就像其他插件一样，传入函数的名称作为字符串，并在插件内调用处理程序。

#### 让用户主题化

jQuery 非常注重自定义；你的插件也应该是。这个示例插件允许用户通过选项更改消息。这个概念可以扩展到包括样式和类。你的插件可能非常有用，但如果它与用户站点的其余部分不匹配，他们就不会使用它。

#### 测试，测试，测试

在将其发布到外部之前，确保你的插件能够胜任任务。在你能找到的每个浏览器上测试，考虑像`BrowserStack`这样的服务，不要忘记请朋友和同事们来试试。

#### 记录它

如果开发者无法理解你的插件，他们就不会使用它。你应该尽可能详细地记录它。将代码发布到 GitHub 之类的公共位置。在你的`package.json`文件中添加内容，以使你的 npm 页面尽可能完整。一定要包含尽可能多的使用代码示例。

#### 最小化它

要像 jQuery 一样。它提供了压缩和未压缩两个版本。开发者喜欢检查未压缩版本中的内容，并使用压缩版本。

# 总结

插件是最受欢迎的 jQuery 功能之一。在本章中，我们了解了插件从 jQuery 插件存储库切换到新的 npm 存储方式的过程。然后我们学习了如何在我们的应用程序中安装、更新和移除插件，甚至了解了 `--save` 选项的用途。

从 npm，我们转向了 jQuery UI，这是官方支持的 UI 小部件库。我们学习了如何使用这些小部件，并创建了一个自定义下载，只包含我们想要的小部件。

我们最后讨论的主题是如何编写我们自己的插件。我们解释了创建插件所需的每个步骤，并解释了为什么我们应该让 jQuery 处理大部分繁重的工作。最后，我们展示了如何将我们的插件上传到 npm，以便他人可以从我们的工作中受益。

我们从学习为什么创建 jQuery 开始了这本书：为了让跨浏览器的 Web 开发更容易。在 第二章 中，*jQuery 选择器和过滤器*，我们使用了 jQuery 的选择器和过滤器来找到页面上的元素，然后在接下来的章节中操作了这些元素。我们使用事件使我们的网站变得交互式，并使用动画使其更加华丽。我们学习了如何获取经过验证的表单数据并将其发送到服务器。我们学习了在 第八章，*编写以后可以阅读的代码*，和 第九章，*更快的 jQuery* 中编写干净和快速代码的技巧。最后，我们学习了如何使用和编写插件。

jQuery 仍然是现代网页开发中的重要组成部分。虽然多年来浏览器在遵循 Web 标准方面做得更好了，但 jQuery 仍然能让开发变得更简单。唯一一点 jQuery 无法帮助的是编写大型 Web 应用程序。在编写大型 Web 应用程序时，Angular 或 Ember 这样的框架，或者像 React 这样的库是更好的选择。
