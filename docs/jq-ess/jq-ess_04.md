# 第四章：事件

在前几章中，我们了解了如何在 DOM 中查找元素以及在找到它们后如何操作它们。在本章中，我们实际开始了解如何使用 jQuery 构建应用程序以及事件所起的重要作用。Web 应用程序使用事件驱动的编程模型，因此深入了解事件非常重要。没有事件，我们现在所知的 Web 应用程序将不可能存在。但在我们进一步深入之前，让我们先了解一下什么是事件。

**事件**是系统认为重要的任何事物的发生。它可以起源于浏览器、表单、键盘或任何其他子系统，也可以由应用程序通过触发生成。事件可以简单到按键，也可以复杂到完成 Ajax 请求。

虽然存在大量潜在的事件，但只有在应用程序监听它们时事件才重要。这也被称为挂钩事件。通过挂钩事件，您告诉浏览器此发生对您很重要，并让它在发生时通知您。当事件发生时，浏览器调用您的事件处理代码并将事件对象传递给它。事件对象保存重要的事件数据，包括触发它的页面元素。我们将在本章后面更详细地查看事件对象。以下是本章将涵盖的内容列表：

+   就绪事件

+   挂钩和取消事件

+   命名空间

+   事件处理程序和对象

+   向事件传递数据

+   事件快捷方式方法

+   自定义事件

+   触发事件

# 就绪事件

刚接触 jQuery 的事件编程者通常会学习的第一件事是 ready 事件，有时也被称为**文档就绪事件**。该事件表示 DOM 已完全加载，并且 jQuery 可以正常运行。就绪事件类似于文档加载事件，但不会等待页面的所有图像和其他资源加载完毕。它只等待 DOM 就绪。此外，如果就绪事件在挂钩之前触发，处理程序代码将至少被调用一次，与大多数事件不同。`.ready()`事件只能附加到文档元素上。仔细想想，这是有道理的，因为它在 DOM 完全加载时触发。

`.ready()`事件有几种不同的挂钩样式。所有样式都做同样的事情：它们挂钩事件。使用哪种挂钩取决于你。在其最基本的形式中，挂钩代码看起来像下面这样：

```js
$(document).ready(handler);
```

由于它只能附加到文档元素，因此选择器可以省略。在这种情况下，事件挂钩如下所示：

```js
$().ready(handler);
```

然而，jQuery 文档不推荐使用上述形式。此事件的挂钩有一个更简洁的版本。此版本几乎什么都省略了，只将事件处理程序传递给 jQuery 函数。它看起来像这样：

```js
$(handler);
```

虽然所有不同的样式都能起作用，但我只推荐第一种形式，因为它最清晰。虽然其他形式也能起作用并节省一些字节的字符，但这是以代码清晰度为代价的。如果你担心表达式使用的字节数，你应该使用 JavaScript 缩小器；它会比你手工做的工作更彻底地缩小代码。

准备事件可以挂接多次。当事件被触发时，处理程序按照挂接的顺序调用。让我们通过代码示例来看一下：

```js
// ready event style no# 1
    $(document).ready(function () {
        console.log("document ready event handler style no# 1");
    // we're in the event handler so the event has already fired.
    // let's hook it again and see what happens
        $(document).ready(function () {
        console.log("We get this handler even though the ready event has already fired");
    });
    });
// ready event style no# 2
    $().ready(function () {
        console.log("document ready event handler style no# 2");
    });
// ready event style no# 3
    $(function () {
        console.log("document ready event handler style no# 3");
    });
```

在上述代码中，我们三次挂接准备事件，每次使用不同的挂接样式。处理程序按照它们被挂接的顺序调用。在第一个事件处理程序中，我们再次挂接事件。由于事件已经被触发，我们可能期望处理程序永远不会被调用，但我们会错。jQuery 对待准备事件与其他事件不同。它的处理程序总是会被调用，即使事件已经被触发。这使得准备事件成为初始化和其他必须运行的代码的理想场所。

# 挂接事件

准备事件与所有其他事件不同。它的处理程序将被调用一次，不像其他事件。它也与其他事件挂接方式不同。所有其他事件都是通过将`.on()`方法链接到您希望触发事件的元素集来挂接的。传递给挂接的第一个参数是事件的名称，后跟处理函数，它可以是匿名函数或函数的名称。这是事件挂接的基本模式。它看起来像这样：

```js
$(selector).on('event name', handling function);
```

`.on()`方法及其伴侣`.off()`方法首次在 jQuery 的 1.7 版本中添加。对于旧版本的 jQuery，用于挂接事件的方法是`.bind()`。`.bind()`方法及其伴侣`.unbind()`方法都没有被弃用，但是`.on()`和`.off()`比它们更受青睐。如果您从`.bind()`切换，那么`.on()`的调用在最简单的层面上是相同的。`.on()`方法具有超出`.bind()`方法的能力，需要传递不同的参数集。我们将在本章后面探讨这些功能。

如果您希望多个事件共享相同的处理程序，只需在前一个事件之后用空格分隔它们的名称：

```js
$("#clickA").on("mouseenter mouseleave", eventHandler);
```

# 取消事件挂接

主要用于取消事件处理程序的方法是`.off()`，调用它很简单。它看起来像这样：

```js
$(elements).off('event name', handling function);
```

处理函数是可选的，事件名称也是可选的。如果省略了事件名称，那么所有添加到元素的事件都被移除。如果包括了事件名称，那么所有指定事件的处理程序都被移除。这可能会造成问题。想象一下这种情况：你为一个按钮编写了一个点击事件处理程序。在应用程序的后期，其他人需要知道按钮何时被点击。他们不想干扰已经工作的代码，所以他们添加了一个第二个处理程序。当他们的代码完成后，他们移除了处理程序，如下所示：

```js
$('#myButton').off('click');
```

由于处理程序只使用事件名称被调用，它不仅移除了添加的处理程序，还移除了所有关于点击事件的处理程序。这不是想要的结果。然而，不要绝望；对于这个问题有两种解决方法：

```js
function clickBHandler(event){
    console.log('Button B has been clicked, external');
}
$('#clickB').on('click', clickBHandler);
$('#clickB').on('click', function(event){
    console.log('Button B has been clicked, anonymous');
    // turn off the 1st handler without during off the 2nd
    $('#clickB').off('click', clickBHandler);
});
```

第一个解决方法是将事件处理程序传递给`.off()`方法。在前面的代码中，我们在名为`clickB`的按钮上放置了两个点击事件处理程序。第一个事件处理程序是使用函数声明安装的，第二个是使用匿名函数安装的。当按钮被点击时，两个事件处理程序都会被调用。第二个通过调用`.off()`方法并将其事件处理程序作为参数传递来关闭第一个处理程序。通过传递事件处理程序，`.off()`方法能够匹配你想要关闭的处理程序的签名。如果你不使用匿名函数，这种修复方法很有效。但是如果你想要将匿名函数作为事件处理程序传递怎么办？有没有办法关闭一个处理程序而不关闭另一个处理程序呢？是的，有；第二种解决方法是使用事件命名空间。

# 事件命名空间

有时需要能够在不使用处理程序函数的情况下区分相同事件的不同处理程序。当出现这种需求时，jQuery 提供了对事件进行命名空间的能力。要给事件设置命名空间，你需要在事件名称后加上一个句点和命名空间。例如，要给点击事件设置`alpha`的命名空间，执行以下操作：

```js
$("button").on("click.alpha", handler);
```

jQuery 只允许你创建一级深的命名空间。如果添加第二级命名空间，你不会创建第二级，而是为同一事件创建第二个命名空间。看一下下面的代码：

```js
$("button").on("click.alpha.beta", handler);
```

前面的代码等同于创建两个独立的命名空间，如下所示：

```js
$("button").on("click.alpha", handler);
$("button").on("click.beta", handler);
```

使用命名空间使我们能够更细粒度地处理我们的事件，以及如何在程序中触发它们。我们将在本章后面探讨如何以编程方式触发事件。

## 事件处理程序

到目前为止，我们只是大致了解了事件处理程序。我们使用了它，但并没有真正解释它。是时候纠正这一点，彻底了解事件处理程序了。让我们从 jQuery 传递给事件处理程序的内容开始。jQuery 将两个东西传递给每个事件处理程序：`event` 对象和 `this` 对象。`this` 对象是隐式传递的，这意味着它不像 `event` 对象那样是一个参数。它由 jQuery 设置为指向绑定事件处理程序的元素。JavaScript 中的 `this` 对象有点像 Java 和 C# 中的 `this` 或 Objective-C 中的 `self`；它指向活动对象。这非常方便，特别是当一组元素共享相同的处理程序时。`this` 对象的使用使得在许多其他元素中轻松地对正确的元素进行操作：

```js
$(document).ready(function(){
    // place a click event on the <li> tags
    $('ul> li').on('click', function(){
        console.log(this.id + " was clicked");
    });
});
```

在前面的代码中，我们在每个 `<li>` 标签上放置了一个点击事件。我们使用隐式传递给我们的 `this` 对象来告诉我们哪个 `<li>` 标签触发了事件。还要注意，我们没有使用事件参数，因为它对我们的示例不需要。

## 事件对象

事件对象是基于 W3C 规范的，并作为参数明确传递给所有事件处理程序，它拥有许多重要属性，其中许多属性对事件处理程序函数可能很有用。因为每个事件都不同，所以在事件对象中传递的属性值也不同。并非每个事件都填充每个属性，因此某些属性可能未定义。但有一些属性是通用的，我们将在下面详细探讨它们。

### event.target

这是触发事件的元素。这与绑定到事件处理程序的元素（由 `this` 对象指向的元素）不同。例如，如果单击一个没有处理程序但其父级 `<div>` 有处理程序的 `<a>` 标签，则事件会冒泡到父级。在这种情况下，`event.target` 指向 `<a>` 标签，但 `this` 对象指向 `<div>` 元素。让我们用代码来探索一下：

```js
$('#theParent').on('click', function (event) {
    console.log("this points to: "+this.id+", event.target points to: "+event.target.id);
    return false;
});
```

在示例中，我们在包围 `<a>` 标签的 `<div>` 标签上放置了一个点击事件处理程序。在 `<a>` 中没有放置处理程序。当单击 `<a>` 时，由于它没有电梯，它会将事件冒泡到其父级，即 `<div>` 标签。现在 `this` 对象将指向 `<div>` 元素，而 `event.target` 仍将指向 `<a>` 标签。

### event.relatedTarget

`relatedTarget` 属性在有效时也指向一个元素，但与触发事件的元素不同，它是与事件相关的元素。一个简单的例子是使用 `mouseenter` 事件。看看下面的代码：

```js
$("form").on('mouseenter', function (event) {
    console.log("target is: " + event.target);
    console.log("relatedTarget is: " + event.relatedTarget);
});
```

当触发`mouseenter`事件时，`relatedTarget`属性指向将接收`mouseleave`事件的元素。在我们的示例中，如果我们从顶部`<a>`开始并移动光标并跨越`<input>`标签，则相关目标将是包围`<a>`标签的`<div>`标签。

### event.type

此属性保存当前事件的名称。如果您对多个事件使用单个事件处理程序，这可能会派上用场：

```js
function eventHandler(event) {
    console.log("event type = " + event.type);
}
$("#clickA").on("mouseenter", eventHandler);
$("#clickB").on("mouseleave", eventHandler);
```

在上面的代码中，我们有两个共享相同处理程序的不同事件。当任一事件发生时，它显示事件类型以使我们能够将它们区分开。

### event.which

当鼠标或键盘事件发生时，可以使用此属性来告诉按下了哪个按钮或键。让我们快速看一个代码示例：

```js
$("#itemName").on('keypress', function (event) {
    console.log("key type: " + event.which);
});
```

按下键时，`which`属性保存键的代码，这是一个数字值。

### event.metaKey

这是一个简单的属性，它保存一个布尔值。如果在事件触发时按下了`metaKey`，则设置为`true`；如果没有按下，则设置为`false`。Macintosh 键盘上的`metaKey`方法通常是命令键；在 Windows 机器上，通常是 Windows 键。

### event.pageX 和 event.pageY

`pageX`和`pageY`属性保存鼠标相对于页面左上角的位置。这在创建随着用户移动鼠标而更新页面的动态应用程序时非常有用，就像在绘图程序中所做的那样：

```js
$(document).on('mousemove', function (event) {
    console.log("x position: " + event.pageX + ", y position: " + event.pageY);
});
```

在代码示例中，我们挂钩`mousemove`事件并动态显示鼠标当前的 x 和 y 位置。

### event.originalEvent

当事件发生时，jQuery 会对其进行归一化，以便每个浏览器中的事件表现出相同的行为。偶尔，jQuery 的标准化事件对象缺少原始事件对象具有的某些内容，而您的应用程序需要。正是出于这个原因，jQuery 在`originalEvent`属性中放置了原始事件对象的完整副本。

# 向事件传递数据

如果您曾经需要向事件传递数据，您需要做的就是在挂钩事件之后传递数据。您几乎可以传递任何类型的数据，但有一些注意事项。首先，如果数据是字符串，那么您还必须设置参数列表中它之前的可选选择器参数。如果您不需要选择器参数，可以将其设置为 null。其次，传递的数据不能是 null 或 undefined。以下是一个小示例，展示了如何向事件传递数据：

```js
// here we pass an object
// we don't have to pass the selector param
$('#clickA').on('click',{msg: "The answer is: ", val: 42}, function (event) {
    alert(event.data.msg + event.data.val);
    return false;
});
// here we pass a string as the data param. 
// Note the null selector param
$('clickB').on('click', null, "The answer is 42.", function(event){
    alert(event.data);
    return false;
});
```

在第二个事件挂钩中，我们传递了一个空的选择器参数，以避免混淆，因为我们将字符串作为数据参数传递。

# 事件快捷方法

Web 编程是事件驱动的，一些事件是如此常用，以至于 jQuery 已经创建了快捷方法来挂钩它们。以下两种方法是相等的：

```js
$(element).on('click', handling function);
$(element).click(handling function);
```

第二种形式更短，可能更易阅读，但有一个缺点。在简写形式中，没有办法添加额外的和可选的参数。如果你需要选择器或数据参数，那么必须使用长格式。以下是所有简写方法的列表：

```js
.change();
.click();
.dblclick();
.error();
.focus();
.focusin();
.focusout();
.hover();
.keydown();
.keypress();
.keyup();
.load();
.mousedown();
.mouseenter();
.mouseleave();
.mousemove();
.mouseout();
.mouseover();
.resize();
.scroll();
.select();
.submit();
```

# 创建您自己的事件

在 JavaScript 中创建自定义事件是常见实践。这样做有很多原因。首先，这是最佳实践，因为它促进了代码的松耦合。使用事件进行通信的代码不是紧密耦合的。这很容易做到；你可以以与为系统事件创建处理程序相同的方式为自己的事件创建事件处理程序。假设我们需要创建一个事件并希望调用`superDuperEvent`。这是创建其处理程序的代码：

```js
$(document).on('superDuperEvent', function (event) {
    console.log(event.type + " triggered");
});
$('#clickA').click(function(){
    $(document).trigger('superDuperEvent');
})
```

在代码中，我们创建了两个事件处理程序。第一个为我们的`superDuperEvent 方法`创建了一个处理程序。如果代码看起来与我们为系统事件创建的处理程序代码几乎相同，那么这就是意图。

# 触发事件

一旦为您的自定义事件创建了处理程序代码，您需要回答的下一个问题是：如何触发事件？这是我们还没有提到的事情，但你所需要的只是`.trigger()`方法。`.trigger()`方法执行与事件类型匹配的元素集绑定的所有处理程序。如上述代码所示，要触发我们的自定义事件，我们所需的就是在一组元素上调用`.trigger()`方法并传入事件的名称。

如果我们愿意，我们也可以将自定义数据传递给事件处理程序。再次强调，这与我们对常规事件所做的操作是一样的。我们只需调用`.trigger()`方法，然后在传递事件名称之后传递自定义数据：

```js
$(document).on('superDuperEvent', function (event, message) {
    console.log(event.type + " triggered with message: " + message);
});
$('#clickA').click(function(){
    $(document).trigger('superDuperEvent', ["Hello from the trigger function at: "+(new Date()).getTime()]);
})
```

如上述代码所示，向我们的事件处理程序传递数据可以做一件钩子事件绑定数据无法做到的事情：我们可以传递新鲜的数据。当我们在事件钩子中传递数据时，它永远不会改变，这限制了其有用性。但是在触发器中的数据可以每次调用事件时更改。看一下这个代码示例：

```js
$(document).on('superDuperEvent', function (event, message) {
    console.log(event.type + " triggered with message: " + message);
});
$('#clickA').click(function(){
    $(document).trigger('superDuperEvent', ["Hello from the trigger function at: "+(new Date()).getTime()]);
});
```

每次我们触发自定义事件时，我们向其传递当前时间的毫秒数。当挂接事件时，无法传递新鲜的数据。

# `.live()`和`.die()`方法的消失

从 jQuery 1.7 版开始，`.live()`方法及其伴侣`.die()`方法已被弃用。并且从版本 1.9 开始从库中删除了它们。尽管它们仍存在于 jQuery 迁移插件中，但不应用于编写新代码，并且应该重写任何旧代码使用它们。许多用户真的很喜欢这些方法，尤其是`.live()`方法。它被用来编写非常动态的代码。那么，为什么这些方法从库中删除了呢？

jQuery 文档在列出 `.live()` 方法的一些问题方面做得很好。其中最重要的是性能问题。尽管传递给选择器的内容不同，`.live()` 方法实际上绑定到了文档元素上。然而，它仍然会检索由选择器指定的元素集，这在大型文档中可能是耗时的。当事件发生时，它必须冒泡到文档才能被处理。这意味着每个由 `.live()` 处理的事件都保证要走最长、最慢的路径到其处理函数。

`.live()` 方法的行为与其他 jQuery 方法不同，这导致了 bug 的产生。它不支持事件链接，尽管看起来像支持。调用 `event.stopProgation()` 什么也不做，因为没有比文档更高级别的东西。它也不与其他事件协调。因此，决定废弃此事件并最终删除它。

# 深入研究 .on()

`.on()` 方法不仅仅是一个重命名的 `.bind()` 方法。它具有前者缺乏的功能。这些新功能的部分原因是为了给开发人员提供一种以与 `.live()` 方法相似的方式编写代码的方法。

`.on()` 方法有一个可选参数；`which` 选择器是一个字符串。大多数情况下，它并不是必需的，所以要么没有传递它，要么传递了一个空值。当你想要替换 `.live()` 方法而又不希望效率低下时，可以使用 `.on()` 方法：

```js
$(document).ready(function (event) {
    var count = 0;
        // hooks the live replacement
    $('#holder').on('click', "li", function (event) {
        console.log("<li> clicked: " + this.id);
    });
    // clicking on the a button will add another element to the ul
    $('#clickA').on('click', function (event) {
        var id = "Li_" + count++;
        $('#holder').append('<li id="' + id + '">' + id + '</li>');
    });
});
```

在上面的代码中，我们挂接了两个事件。首先挂接父元素，即 `<ul>` 标签，它将作为所有 `<li>` 标签的容器：现在存在的和以后创建的。然后，我们挂接了用于生成新 `<li>` 标签的按钮。每次我们创建一个新标签时，我们都会增加一个计数器并将其连接到用于新标签 `id` 的字符串，然后将其附加到 `<ul>` 标签上。

# 摘要

我们学到了 Web 编程中最重要的事情之一：事件。事件使网站具有交互性。我们首先从最重要的事件之一开始，即 jQuery 就绪事件。我们继续讨论挂接和取消挂接事件、命名空间，并最终使用事件对象编写事件处理程序。基础知识介绍完毕后，我们展示了如何编写自己的事件并触发它们。

在下一章中，我们将学习如何使用 jQuery 的内置和自定义动画使我们的网站变得流畅和精致。动画有助于使应用程序状态之间的过渡变得平滑。没有它，当页面上的元素突然出现和消失时，网站对用户来说可能会显得令人不适。
