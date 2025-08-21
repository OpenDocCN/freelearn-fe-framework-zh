# 第六章：使用 jQuery 创建更好的表单

在上一章中，我们讨论了动画以及它们如何使您的网站生动起来。在本章中，我们将研究任何网站最重要的功能之一——表单。一个精心制作的表单可能是获取新客户和错失机会之间的区别。所以表单值得仔细研究。

让用户完成表单并提交可能是具有挑战性的。如果我们的网站在任何时候让用户感到沮丧，他们可能会放弃填写表单和离开我们的网站。因此，我们通过使用工具提示、占位符文本和视觉指示器为用户提供温和的提示，让他们知道表单的每个输入元素需要什么。

jQuery 并没有太多专门处理表单的方法。其中的第一个都是快捷方式方法；它们用事件的名称作为第一个参数替换了`.on()`的使用。让我们来看看它们，并学习如何使用它们。

# 使用表单方法

让我们来看看一些与表单一起使用的 jQuery 方法。

## .submit()

最重要的表单方法之一是`.submit()`。它将处理程序绑定到浏览器提交事件。当用户已经（希望）填写了您的表单并单击提交按钮时，此处的事件处理程序将被激活。如果您想要自己处理此事件，而不实际提交表单，则必须调用`event.preventDefault()`或从方法中返回`false`。如果您没有执行其中一项操作，表单将被提交到您的服务器。

```js
// Cancel the default action by returning false from event handler
$('form').submit(function(event){
    alert('submit');
    return false;
});
```

在上述事件处理程序中，我们返回`false`来阻止浏览器提交我们的表单。我们也可以调用`event.preventDefault()`。我们可以将上述内容写成：

```js
// Cancel the default action by call 
$('form').on('submit', function(event){
    alert('submit');
    event.preventDefault();
});
```

这个示例的工作方式与第一个示例完全相同，但使用了更多的文本。我们用长格式的`.on()`方法替换了提交的快捷方式方法，并且我们还用事件对象直接调用了`preventDefault()`方法来替换了`return false`。

## .focus()

当用户在表单元素上切换或单击时，浏览器会触发一个焦点事件。焦点方法为此事件创建了一个处理程序。如果您希望为用户创建某种指示，表明这是活动元素，则这可能很方便。查看`.blur()`方法的代码示例，了解如何与`.focus()`一起使用。

需要注意的是，只有表单元素才能接收焦点。以下所有内容都是表单元素：

+   `<input>`

+   `<select>`

+   `<textarea>`

+   `<button>`

## .blur()

`.blur()`方法是`.focus()`方法的伴侣。它创建了一个处理程序，当用户切换或离开此元素时触发，导致它失去焦点。此事件也可以用于对元素进行验证，但实际上使用变化事件更好，并且将在稍后使用`.change()`方法进行解释。

```js
// Adds / removes the active class using the focus/blur events
$("input, textarea").focus(function(event){
    $(this).addClass('active');
});

$("input, textarea").blur(function(event){
    $(this).removeClass('active');
});
```

我们可以一起使用`.focus()`和`.blur()`方法，为活动元素添加一个类，并在失去焦点时移除它，以为我们的用户提供更好的视觉提示。请注意，我们通过用逗号分隔标签名称来挂接输入元素和文本区域元素。

使用`.focus()`和`.blur()`方法的一个潜在问题是它们不会冒泡。如果将要接收焦点的子元素放置在父元素中，并挂接父元素的焦点事件，那么事件将永远不会触发。这意味着您无法将这些事件委托给它们的父级。如果您需要挂接动态生成的输入标签的焦点/失焦事件，您也会遇到麻烦。

```js
// These handlers will never be triggered
$('#fiOne').focus(function (event) {
    console.info('Focus: ' + this.id + ', triggered by: ' + event.target.id);
    $(this).addClass('active');
});

$('#fiOne').blur(function (event) {
    console.info('Blur: ' + this.id + ', triggered by: ' + event.target.id);
    $(this).removeClass('active');
});
```

在上述代码中，我们挂接了`fieldset`，即所有单选按钮的父元素`fiOne`的焦点和失焦事件。在任何单选按钮子元素上都没有这些事件的处理程序。不幸的是，由于这两个事件都不会冒泡到其父元素，因此永远不会触发任何事件。

## `.focusin()`和`.focusout()`

现在我们知道焦点和失焦事件都不会冒泡到它们的父级。我们还在之前的章节中了解了冒泡如何帮助我们创建更具动态性的应用程序。有没有办法规避冒泡的缺乏呢？幸运的是，有一个解决方案：`.focusin()`和`.focusout()`方法。`.focusin()`方法为`focusin`事件创建一个处理程序，当元素即将获得焦点时触发。`.focusout()`方法与`.focusin()`方法相同，只是它与`focusout`事件一起工作，当元素即将失去焦点时触发。这两种方法都会冒泡到它们的父元素。

```js
// These handlers use the focusin/focusout, which bubble up
$('#fiOne').focusin(function (event) {
    console.info('Focusin: ' + this.id + ', triggered by: ' + event.target.id);
    $(this).addClass('active');
});

$('#fiOne').focusout(function (event) {
    console.info('Focusout: ' + this.id + ', triggered by: ' + event.target.id);
    $(this).removeClass('active');
});
```

此代码示例与前一个示例几乎相同，只是焦点和失焦事件分别替换为`focusin`和`focusout`事件。我们再次挂接父级`fieldset`元素。但是，这次，事件会冒泡到它们的父级。我们将活动类添加到`fieldset`，甚至通过从事件对象的目标属性获取其 ID 来显示生成事件的元素。

```js
// Adds an input tag dynamically by clicking the "Add Another" button
var inputCounter = 0;
$('#addAnother').click(function (event) {
    console.info("Adding another");
    $('#inputMomma').append($("<input>").attr({'type': 'text', 'id': 'newInput' + inputCounter++}));
});

// Makes the parent element the active class
$('#inputMomma').focusin(function (event) {
    console.info('Focusin: ' + this.id + ', triggered by: ' + event.target.id);
    $(this).addClass('active');
});

// Removes the active class from the parent
$('#inputMomma').focusout(function (event) {
    console.info('FocusOut: ' + this.id + ', triggered by: ' + event.target.id);
    $(this).removeClass('active');
});
```

在此示例中，我们使用 jQuery 动态创建新的输入标签。请注意我们如何使用链式操作来附加新的输入标签并设置其属性。即使这些标签在它们的父级挂接`focusin`和`focusout`事件时并不存在，它们仍然会将它们的事件冒泡到它。

## `.change()`

`.change()`方法为 change 事件创建一个处理程序。change 事件的好处是只有在输入或文本元素的值发生更改并且字段不再具有焦点时才触发。这使其比使用失焦事件更适合用于验证，因为失焦事件总是在元素失去焦点时触发，无论其值是否已更改。通过使用 change 事件，我们可以避免进行一些不必要的处理。

```js
// only called once focus is lost and contents have changed
$("input, textarea").change(function (event) {
    console.info('Change is good:' + this.id);
});
```

## `.select()`

我们在本章将要检查的事件处理程序方法中的最后一个是`.select()`方法。它绑定到 select 事件。此事件仅在允许输入文本的两个元素上触发：`<textarea>`和`<input type='text'>`。当用户选择某些文本时才会发生 select 事件。

```js
// Triggered when some text is selected
$('textarea').select(function(event){
    console.info("Something was selected: " + this.tagName);
});
```

在这个例子中，我们简单地挂接了 select 事件，并在接收到该事件时显示标签的名称。像大多数事件一样，我们也可以使用`.trigger()`方法触发 select 事件。

```js
// Selects all of the textarea text
$('#selectText').click(function (event) {
    console.info("Adding another");
    $('textarea').select();
});
```

```js
textarea and also gives <textarea> the focus. Other shortcomings of the select event are that there is no standard way to retrieve the selected text or to declare the range of characters to select. When the select event is fired, it always selects all of the characters. There are jQuery plugins available to fill the gap.
```

# 工具提示

自 HTML 4.01 以来，除了`<base>`、`<basefont>`、`<head>`、`<html>`、`<meta>`、`<param>`、`<script>`和`<title>`元素之外，title 属性可用。它定义了一个字符串，大多数浏览器在鼠标位于元素上方时会在元素上方附近渲染。显示的字符串通常称为工具提示。

标准的 HTML 工具提示留下了很多问题。默认情况下，它们的样式通常很简单，可能与您的网站不搭配。例如，如果您的网站使用大号字体来帮助用户，标准的工具提示看起来会非常尴尬。幸运的是，jQuery 有解决方案，尽管它不在核心库中。

jQuery UI 是一个包含一组用户界面组件的库。设计该组是为了可定制。该组的成员之一是工具提示。对于缺乏本机工具提示（title 属性支持）的浏览器，它提供支持。对于具有本机工具提示支持的浏览器，它通过使它们可定制和动画化来增强它们。

jQuery UI 的所有组件都需要 jQuery 核心库以及 CSS 和 JavaScript 文件才能正常工作。CSS 文件必须在 jQuery 之前添加到您的 HTML 文件中，JavaScript 文件必须在 jQuery 之后添加。

```js
<head lang="en">
<meta charset="UTF-8">
<link rel="stylesheet" href="//code.jquery.com/ui/1.11.4/themes/smoothness/jquery-ui.css">
<script src="img/"></script>
<script src="img/jquery-ui.js"></script>
<title>Chapter06-Forms</title>
```

上面的标记显示了如何正确地将 jQuery UI 支持添加到网站中。首先，我们添加 CSS 文件，然后是 jQuery，最后我们添加 jQuery UI。

### 注意

在上面的代码中，使用内容交付网络（CDN）来托管文件。您也可以将文件托管在您自己的服务器上，但是使用 CDN，您可以获得潜在的性能提升，因为用户浏览器可能已经在第一次访问您的网站时缓存了您的文件。

一旦我们加载了 jQuery 库，使用工具提示就很简单。我们需要挂接 jQuery 文档准备事件并在其中设置我们的工具提示。

```js
// Hook the document ready event and
$(document).ready(function () {
    // bind the tooltip plugin to the document
    $(document).tooltip({
        show: {
            effect: "slideDown",
            delay: 150
        }
    });
});
```

在上面的示例中，我们等待文档准备事件。在事件处理程序中，我们将工具提示绑定到文档上。这样就可以在整个网站上使用它。最后一步是添加动画效果。工具提示可以定制为在页面上以及页面外播放动画。在这里，我们让工具提示在 150 毫秒延迟后以`slideDown`的动画效果进入页面。

# 占位符

另一个现代浏览器具有但旧版本浏览器缺少的功能是占位符文本。它是出现在输入元素内部的略微灰色的文本，一旦用户开始输入就会消失。占位符对于表单很重要。它们为用户提供关于内容格式的提示，不像 `<label>` 元素，它提供了什么类型的信息是预期的。占位符属性自 HTML5 以来才有。仍然有很多浏览器不支持它。

为了在旧版本浏览器中添加对占位符属性的支持，我们将再次使用一个插件，但不是来自 jQuery 团队的插件。相反，我们将使用出色的 jquery-placeholder，来自 Mathias Bynens。可以从 bower 和 npm 下载，但我们将直接从其 GitHub 仓库下载：[`mathiasbynens.github.io/jquery-placeholder/`](http://mathiasbynens.github.io/jquery-placeholder/)。由于我们不关心它的工作原理，只关心如何使用它，我们将在我们的网站上安装缩小版。为此，我们在 HTML 文件中添加以下行：

```js
<script src="img/jquery.placeholder.min.js"></script>
```

占位符是一种称为 polyfill 的插件类型。这意味着它的目标只是给缺少标准功能的浏览器提供该功能。如果浏览器已经支持该标准，它将不起作用。为了激活插件，我们将其添加到 jQuery 文档就绪事件中，方式与之前处理工具提示相同。

```js
// Hook the document ready event and
$(document).ready(function () {
    // bind the placeholder to the input & textarea elements
    $('input, textarea').placeholder();
});
```

# 启用和禁用元素

表单中无效的元素应该被禁用。禁用的元素通常显示为灰色文本。禁用的元素无法聚焦，不响应用户，并且在提交表单时不会被发送。

关于禁用属性的奇怪之处在于，它在元素内的存在就会禁用它。它不需要设置为 `true` 或 `false`。实际上，将其设置为 `true` 或 `false` 没有任何效果。要禁用元素，请添加禁用属性。要启用元素，请移除禁用属性。幸运的是，jQuery 理解这种奇怪的行为并为我们处理了这个细节。

我们可以使用 jQuery 的 `.prop()` 方法来帮助我们。当我们想要禁用元素时，我们执行以下操作：

```js
$('#someId).prop('disabled', true);
```

当我们想要启用元素时，我们执行以下操作：

```js
$('#someId).prop('disabled', false);
```

尽管事情看起来很奇怪，但 jQuery 将会按照我们说的那样执行。第一行代码将向元素添加禁用属性，第二行将其移除。这里是一个更全面的代码片段：

```js
// disables/enable elements
$('#disableEnable').click(function (event) {
    var $ptr = $('#names > *');
    if ($ptr.prop('disabled')) {
        $ptr.prop('disabled', false);
        $(this).text('Disable Them');
    } else {
        $ptr.prop('disabled', true);
        $(this).text('Enable Them');
    }
});
```

我们首先通过为 `disableEnable` 按钮的点击事件绑定事件监听器。一旦接收到事件，我们检查按钮当前是否已禁用。如果是，则启用它并更改按钮的文本标签。如果元素未禁用，则禁用它并更改文本消息。

# 验证

到目前为止，我们学会了如何挂钩表单事件，如何为旧版浏览器提供现代浏览器功能，如占位符和工具提示，以及如何使用 jQuery 表单方法来收集所有的表单数据。但是我们没有做一件非常重要的事情：验证数据。

对用户和我们作为站点创建者来说，验证都很重要。对于用户，验证可以用来告诉他们如何正确填写表单。当他们犯错时，我们可以轻轻地提醒他们，而不是让他们提交一个错误的表单，然后告诉他们表单中包含错误。作为站点的维护者，发现一个应该有电话号码的字段中却找到了地址，这可能会令人沮丧。HTML5 为 Web 添加了许多验证功能。在我们看看 jQuery 提供了什么之前，让我们先看看现代浏览器中免费提供了什么。

我们将看到的第一个 HTML5 新添加的属性似乎如此简单和微不足道，以至于很难想象它以前并不存在：`autofocus`。`autofocus` 属性声明了在加载表单时应该拥有焦点的表单元素。在它出现之前，用户必须点击一个元素才能选择它，或者我们必须使用一点 jQuery 代码，如下所示：

```js
// Hook the document ready event and
$(document).ready(function () {
    // Give the title select element the focus
    $('#title').focus();
});
```

使用 HTML5，先前的代码被替换为：

```js
<select id="title" name="title" autofocus>
```

`autofocus` 属性声明该元素获得焦点。在任何给定时间，只应该有一个元素具有该属性。

HTML5 还增加了 `<input>` 元素类型的数量。以前，唯一可用的类型是：

| 类型 | 用途 |
| --- | --- |
| 按钮 | 按钮 |
| 复选框 | 复选框 |
| 文件 | 文件选择 |
| 隐藏 | 不显示但提交到服务器 |
| 图片 | 提交按钮的图形版本 |
| 密码 | 值被隐藏的文本字段 |
| 单选按钮 | 单选按钮 |
| 重置 | 将表单内容重置为默认值 |
| 提交 | 提交表单 |
| 文本 | 单行文本字段 |

HTML5 添加了以下新的 `<input>` 类型：

| 类型 | 用途 |
| --- | --- |
| 颜色 | 颜色选择器 |
| 日期 | 日期选择器（不含时间） |
| 日期/时间 | UTC 的日期/时间选择器 |
| 本地日期/时间 | 本地时区的日期/时间选择器 |
| 电子邮件 | 电子邮件地址 |
| 月份 | 月份/年份选择器 |
| 数字 | 浮点数的文本字段 |
| 范围 | 范围滑块 |
| 搜索 | 用于搜索字符串的文本字段 |
| 电话 | 用于电话号码的文本字段 |
| 时间 | 无时区的时间选择器 |
| 链接 | 用于 URL 的文本字段 |
| 周 | 周/年选择器 |

非常重要的一点是，对不同类型的检查非常少。例如，Tel 类型允许输入通常不是电话号码的字符。HTML5 提供了三个属性可以帮助：`minlength`、`maxlength` 和 `pattern`。`minlength` 属性表示输入的字符串必须包含的最小字符数才能被视为有效。`maxlength` 属性也是如此，只不过是最大字符数。最后一个属性是 `pattern`；它指定了要检查输入字符串的正则表达式。为了使字符串被视为有效，它必须通过。正则表达式在验证目的上非常方便，但编写正确的正则表达式可能会有些棘手。请务必对你网站中添加的任何正则表达式进行彻底测试。我还强烈建议使用一个有流行验证方案的网站。其中一个很受欢迎的正则表达式网站是：[`www.regular-expressions.info`](http://www.regular-expressions.info)。

HTML5 还添加了一个非常简单但重要的验证属性：required。required 属性简单地表示输入元素必须填写才能使表单被视为有效。如果它为空或填写但无效，符合规范的浏览器在用户尝试提交表单时会标记错误。不幸的是，错误消息和样式因浏览器而异。所以，如果我们真的想要掌控我们网站的样式，我们必须再次求助于我们的好朋友 jQuery。

验证并不是 jQuery 或 jQuery UI 的一部分，而是 jquery-validate 插件的主要功能。该插件由 jQuery、jQuery UI 和 QUnit 团队成员 Jörn Zaefferer 编写和维护。它始于 2006 年，至今仍在维护，是最古老的 jQuery 插件之一。jquery-validate 的主页是：[`jqueryvalidation.org/`](http://jqueryvalidation.org/)。你可以在那里下载 zip 文件，也可以通过 bower 或 nuget 包管理器下载。该插件的核心在文件 `jquery.validate.js` 中。对于大多数安装来说，这就是你所需要的。

一旦你将插件添加到你的脚本文件中，接下来你需要在你的 jQuery 文档准备好的事件处理器中，对你想要验证的表单添加调用验证方法。为了最小化验证，仅增强 HTML5 提供的内容，你只需添加如下一行代码：

```js
$('#personalInfo').validate();
```

这一行告诉插件验证名为 `personalInfo` 的表单。不需要其他进一步的操作。该插件将根据你在表单元素上放置的验证属性的行为进行操作，即使是不符合 HTML5 规范的浏览器也是如此。

如果您想要更多自定义，您将需要将一个初始化对象传递给插件。最重要的两个属性是 rules 和 messages。rules 属性定义插件将如何验证每个表单元素。messages 属性定义了当元素验证失败时插件将显示哪个消息。以下是我们验证示例的代码：

```js
<!DOCTYPE html>
<html>
<head lang="en">
<meta charset="UTF-8">
<link rel="stylesheet" href="//code.jquery.com/ui/1.11.4/themes/smoothness/jquery-ui.css">
<script src="img/"></script>
<script src="img/jquery-ui.js"></script>
<script src="img/jquery.validate.min.js"></script>
<script src="img/jquery.placeholder.min.js"></script>
<title>Chapter06-Forms</title>
<style type="text/css">
        .wider {
            display: inline-block;
            width: 125px;
            margin-right: 8px;
        }
        select {
            margin-right: 8px;
        }
        .error{
            color: red;
        }
</style>
</head>
<body>
<div>
<form id="personalInfo">
<fieldset>
<legend>Personal Info</legend>
<p>
<label for="title" class="wider">Greeting</label>
<select id="title" name="title" class="wider" autofocus>
<option selected></option>
<option>Mr.</option>
<option>Ms.</option>
<option>Miss</option>
<option>Mrs.</option>
<option>Dr.</option>
</select>
</p>
<p>
<label for="firstName" class="wider">First Name:</label>
<input id="firstName" name="firstName" class="wider" type="text" title="Your first name"/>
</p>
<p>
<label for="lastName" class="wider">Last Name:</label>
<input id="lastName" name="lastName" class="wider" type="text" title="Your last name"/>
</p>
<p>
<label for="password" class="wider">Password:</label>
<input id="password" name="password" class="wider" type="password"  title="Your password" title="Your password"/>
</p>
<p>
<label for="confirmPassword" class="wider">Confirm Password</label>
<input id="confirmPassword" name="confirmPassword" class="wider" type="password"  title="Confirm your password"/>
</p>
<p>
<label for="email" class="wider">E-Mail:</label>
<input id="email" name="email" class="wider" type="email" title="Your email address" placeholder="yourname@email.com" />
</p>
</fieldset>
<input type="reset" value="Reset" class="wider"/>
<input type="submit" value="Submit" class="wider"/>
</form>
</div>
```

除了添加验证插件之外，我们还需要包含 jQuery，因为我们仍然使用 jQuery 的提示和占位符插件，我们也将它们包含在内。接下来，我们添加了一点内联 CSS 来为我们增加一些样式。

我们的表单相当标准，除了一件事：我们不再添加任何内联验证属性。相反，我们在传递给验证方法的 JavaScript 对象中定义验证规则，接下来我们将看到：

```js
<script type="text/javascript">
    (function () {
        "use strict";

        // Hook the document ready event and
        $(document).ready(function () {
            // bind the placeholder polyfill to the input + textarea elements
            $('input, textarea').placeholder();
            // bind the tooltip plugin to the document
            $(document).tooltip({
                show: {
                    effect: "slideDown",
                    delay: 150
                }
            });
```

此代码示例的第一部分相当直接了当。我们绑定到 jQuery 的就绪事件，然后启用占位符 polyfill 和工具提示。

```js
            // bind validation to the personalInfo form
            $('#personalInfo').validate({
                rules: {
                    title: {
                        required: true
                    },
                    firstName: {
                        required: true,
                        minlength: 5
                    },
                    lastName: {
                        required: true,
                        minlength: 5
                    },
                    password: {
                        required: true,
                        minlength: 5
                    },
                    confirmPassword: {
                        required: true,
                        minlength: 5,
                        equalTo: '#password'
                    },
                    email: {
                        required: true,
                        email: true
                    }
                },
```

在验证对象的 rules 属性中，我们传递了我们希望验证的所有元素的名称。我们可以告诉验证器哪些元素是必需的，它们的最小长度，它们是否应该与另一个元素匹配等等。验证器可以做的远远超出了代码所示的内容，因此一定要阅读文档以了解更多信息。

```js
                messages: {
                    title: "Please choose a title.",
                    firstName: "Please enter your first name.",
                    lastName: "Please enter your last name.",
                    password: "Please create a password.",
                    confirmPassword: {
                        required: "Please confirm your password.",
                        equalTo: "Your passwords must match."
                    },
                    email: "Please enter a valid email."
                },
                submitHandler: function(form) {
                    alert('submit');
                }
            });
        });
    }());
</script>
</body>
</html>
```

在验证对象的 message 属性中，我们传递了我们希望显示的所有消息。这里未定义的任何元素或状态将简单地分配一个默认的验证错误消息，这在许多情况下可能已经足够了。

传递给验证方法的最后一个属性是 submit 处理程序。这是用户成功提交表单后调用的方法。您必须使用 submit 处理程序，而不是 jQuery 的 submit 处理程序。

# 过滤掉不需要的字符

作为 web 开发人员，我们的工作是防止用户意外做出一些不好的事情。验证可以让用户知道他们输入了错误的内容。过滤有助于防止用户输入无效字符。为了过滤输入到文本字段中的字符，我们需要挂钩两个事件："keypress" 和 "paste"。

```js
// filters out unwanted characters
$('#alphaNumOnly').on('keypress paste', function (event) {
    // convert the keycode into a character
    var nextChar = String.fromCharCode(event.which);
    if(event.type === 'keypress'){
    // add it to the current input text string, the remove any
    bad chars via regex
    this.value = (this.value + nextChar).replace(/[⁰-9|a-z|AZ]+/g, '');
}
    // let the browser know we've handled this event
    event.preventDefault();
    return false;
});
```

挂钩 `keypress` 事件允许我们在按下每个键时查看每个键，并决定我们是否希望这个字符出现在我们的文本字段中。我们挂钩粘贴键以阻止用户将字符串剪切并粘贴到我们的文本字段中。大部分工作由正则表达式完成。它过滤掉除数字和字母之外的所有内容。

# 概要

表单对于大多数网站来说非常重要。它们是我们网站用户与网站交流的主要方式。帮助我们的用户填写表单，并确保我们获得的数据是良好的。我们已经看到了 jQuery 如何帮助我们处理表单的许多方式。工具提示插件帮助我们向缺乏它的浏览器添加工具提示，并将工具提示样式设置为与我们网站外观相匹配。占位符填充为较旧的浏览器提供占位符属性，并对已支持该属性的浏览器静默地退出。

jQuery 也为我们提供了简单的方法来挂钩提交、更改和其他表单事件。这些事件还提供了在我们提交数据之前或数据更改后验证数据的点。

在下一章中，我们将学习关于 Ajax 以及 jQuery 如何使得从我们的服务器发送和接收数据几乎变得轻而易举。
