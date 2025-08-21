# 第十一章：优化模式

本章介绍了几种优化技术，可用于改善 jQuery 应用程序的性能，特别是当它们变得庞大和复杂时。

我们将从捆绑和最小化我们的 JavaScript 文件等简单实践开始，并讨论使用**CDN**加载第三方库的好处。然后，我们将继续分析一些简单的编写高效 JavaScript 代码的模式，并学习如何编写高效的 CSS 选择器，以提高页面的渲染速度和使用 jQuery 进行 DOM 遍历。

然后，我们将研究特定于 jQuery 的实践，如缓存 jQuery 复合集合对象，如何最小化 DOM 操作，并将**委托观察者模式**作为**享元模式**的一个好例子。最后，我们将介绍**惰性加载**的高级技术，并演示如何根据用户操作逐步加载实施的不同模块。

到本章结束时，我们将能够在实施中应用最常见的优化模式，并将本章用作将应用程序移至生产环境之前的最佳实践和性能提示的检查表。

在本章中，我们将：

+   学习捆绑和最小化我们的 JavaScript 文件的好处

+   学习如何通过 CDN 服务器加载第三方库

+   学习一些简单的 JavaScript 性能提示

+   学习如何优化我们的 jQuery 代码

+   介绍享元模式并展示一些例子

+   学习在用户操作时如何按需惰性加载我们应用的部分

# 将脚本放置在页面末尾

提高页面初始渲染速度的第一个提示是收集所有所需的 JavaScript 文件，并将它们的`<script>`标签置于页面末尾，最好就在关闭`</body>`标签之前。这个改变会对页面的初始渲染时间产生很大的影响，特别是对于使用低速连接的用户（如移动用户）。如果您已经为所有与 DOM 相关的初始化目的使用了`$(document).ready()`方法，将`<script>`标签移动到其他位置不会对您的实施功能产生任何影响。

其主要原因是，即使浏览器并行下载页面的 HTML 和其他资源（CSS、图像等），当遇到`<script>`标签时，浏览器会暂停一切，直到它被下载和执行。为了解决规范的这一限制，HTML5 引入了`defer`和`async`等属性作为`<script>`标签规范的一部分，但不幸的是，直到最近才开始被一些浏览器采用。因此，即使在旧版浏览器上仍广泛使用这种做法来获得良好的页面加载速度。

### 注：

有关`<script>`标签的更多信息，请访问：[`developer.mozilla.org/en-US/docs/Web/HTML/Element/script`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/script)

# 捆绑和缩小资源

要使页面加载速度更快，首先要寻找减少 HTTP 请求的数量和总大小的方法。好处在于浏览器下载内容时可以更大块地下载，而不是花时间等待许多小的往返请求完成。这对于低速连接的用户（如移动用户）尤其有益。

资源串联是一个简单的概念，无需任何介绍。这可以手动完成，但最好用捆绑脚本自动化此任务，或者为项目引入一个构建步骤。根据您的开发环境，有不同的捆绑解决方案可供选择。如果您在开发栈中使用**grunt**或**gulp**，您可以使用像`grunt-contrib-concat`（[`github.com/gruntjs/grunt-contrib-concat`](https://github.com/gruntjs/grunt-contrib-concat)）和`gulp-concat`（[`github.com/contra/gulp-concat`](https://github.com/contra/gulp-concat)）这样的解决方案。

缩小 JavaScript 文件是一个更复杂的过程，包括一系列应用于目标源代码的代码转换，从简单的空格删除到更复杂的任务如变量重命名。流行的缩小 JavaScript 的解决方案包括：

+   YUI 压缩器可在[`yui.github.io/yuicompressor/`](http://yui.github.io/yuicompressor/)找到。

+   谷歌的闭包编译器可在[`developers.google.com/closure/compiler/`](https://developers.google.com/closure/compiler/)找到。

+   UglifyJS 可在[`github.com/mishoo/UglifyJS2`](https://github.com/mishoo/UglifyJS2)找到。

再次强调，有各种解决方案可以很好地将上述库与您喜欢的开发环境集成，使缩小成为一个简单的任务。例如，grunt 和 gulp 的集成示例包括`grunt-contrib-uglify`（[`github.com/gruntjs/grunt-contrib-uglify`](https://github.com/gruntjs/grunt-contrib-uglify)）和`gulp-uglify`（[`github.com/terinjokes/gulp-uglify`](https://github.com/terinjokes/gulp-uglify)）。

作为最后的建议，要记住，你的代码应该尽可能地易读和逻辑结构清晰。将 JavaScript 和 CSS 文件进行捆绑和缩小，最有效的方法是作为开发和部署过程的构建步骤来完成。

## 使用 IIFE 参数

除了有助于避免污染全局命名空间之外，使用 IIFE 来包装您的实现也对缩小后的 JavaScript 文件大小有益。让我们看看下面的代码，其中`jQuery`、`window`和`document`变量作为调用参数传递到模块的 IIFE 中。

```js
(function ( $, window, document, undefined ) { 
    if (window.myModule === undefined) { 
        window.myModule = {}; 
    } 

    myModule.init = function() { /*...*/ };

    $(document).ready(myModule.init); 

})( jQuery, window, document ); 
```

我们在上一章中看到了类似的模式，作为创建 jQuery 插件的建议模板的一部分。尽管变量别名不影响实现的功能，但它允许代码最小化器在更多地方应用变量重命名，导致以下代码：

```js
(function(b, a, c, d) { 
    a.myModule === d && (a.myModule = {}); 
    myModule.init = function() { /*...*/ };
    b(c).ready(myModule.init); 
})(jQuery, window, document); 
```

正如您在上面的代码中可以看到的，所有 IIFE 的调用参数都被缩小器重命名为单个字母标识符，这尤其增加了最小化的收益，特别是如果原始标识符在多个地方使用。

### 提示

作为附加好处，别名还可以保护我们的模块，防止原始变量意外赋予不同的值。例如，当 IIFE 参数未被使用时，来自不同模块的赋值，如`$ = {}`或`undefined = 7`，会破坏所有实现。

# 使用 CDN

不要从您的网站服务器提供所有的第三方库的 JavaScript 和 CSS 文件，您应该考虑使用**内容交付网络**（**CDN**）。使用 CDN 来提供您的网站所使用的库的静态文件可以使它加载更快，因为：

+   CDN 具有高速连接和多个缓存级别。

+   CDN 有许多地理分布的服务器，可以更快地传送所请求的文件，因为它们离最终用户更近。

+   CDN 有助于并行化资源请求，因为大多数浏览器只能同时从任何特定域下载最多四个资源。

而且，如果用户从使用相同 CDN 的另一个网站上缓存了静态资源，他或她将不必再次下载它们，减少了您的网站需要加载的时间。

下面是一个使用 JavaScript 库的最广泛使用的 CDN 列表，您可以在您的实现中使用它们：

+   [`code.jquery.com/`](https://code.jquery.com/)

+   [`developers.google.com/speed/libraries/`](https://developers.google.com/speed/libraries/)

+   [`cdnjs.com/`](https://cdnjs.com/)

+   [`www.jsdelivr.com/`](http://www.jsdelivr.com/)

## 使用 JSDelivr API

CDN 世界的新来者是 JSDelivr，由于其独特的功能而备受欢迎。除了简单地提供现有的静态文件外，JSDelivr 还提供一个 API（[`github.com/jsdelivr/api`](https://github.com/jsdelivr/api)），允许我们创建和使用带有我们需要加载的资源的自定义捆绑包，帮助我们最小化网站所需的 HTTP 请求。此外，其 API 允许我们以不同级别的特定性（主要、次要或错误修复版本）定位库，甚至允许我们只加载库的特定部分。

例如，看一下以下 URL，它允许我们使用单个请求加载 jQuery v1.11.x 的最新 bug 修复版本，以及 jQuery-UI v1.10.x 和 Bootstrap v3.3.x 的一些部分：[`cdn.jsdelivr.net/g/jquery@1.11,jquery.ui@1.10(jquery.ui.core.min.js+jquery.ui.widget.min.js+jquery.ui.mouse.min.js+jquery.ui.sortable.min.js),bootstrap@3.3`](http://cdn.jsdelivr.net/g/jquery@1.11,jquery.ui@1.10(jquery.ui.core.min.js+jquery.ui.widget.min.js+jquery.ui.mouse.min.js+jquery.ui.sortable.min.js),bootstrap@3.3)

# 优化常见的 JavaScript 代码

在本节中，我们将分析一些不特定于 jQuery 的性能提示，并且可以应用于大多数 JavaScript 实现。

## 编写更好的 for 循环

当使用`for`循环遍历数组或类似数组的集合时，提高迭代性能的一个简单方法是避免在每个循环中访问`length`属性。可以通过将迭代`length`存储到一个单独的变量中，在循环之前声明，甚至与循环一起声明，如下所示：

```js
for (var i = 0, len = myArray.length; i < len; i++) { 
    var item = myArray[i]; 
    /*...*/ 
} 
```

此外，如果我们需要迭代不包含**假值**的数组项，我们可以使用一个更好的模式，通常用于迭代包含对象的数组：

```js
var objects = [{ }, { }, { }]; 
for (var i = 0, item; item = objects[i]; i++) { 
    console.log(item); 
}
```

在这种情况下，我们利用了数组的超出边界位置返回`undefined`的事实，这是假值并且停止迭代。可以在迭代**节点列表**或 jQuery 复合集合对象时使用此技巧的另一个示例情况如下：

```js
var anchors = $('a'); // or document.getElementsByTagName('a');
for (var i = 0, anchor; anchor = anchors[i]; i++) { 
    console.log(anchor.href); 
} 
```

### 注意

有关 JavaScript 中**真值**和**假值**的更多信息，请访问：[`developer.mozilla.org/en-US/docs/Glossary/Truthy`](https://developer.mozilla.org/en-US/docs/Glossary/Truthy) 和 [`developer.mozilla.org/en-US/docs/Glossary/Falsy`](https://developer.mozilla.org/en-US/docs/Glossary/Falsy)

# 编写高性能的 CSS 选择器

尽管**Sizzle**（jQuery 的选择引擎）隐藏了基于复杂 CSS 选择器的 DOM 遍历的复杂性，我们应该了解我们的选择器是如何执行的。了解 CSS 选择器如何匹配 DOM 的元素可以帮助我们编写更有效的选择器，在与 jQuery 一起使用时性能更佳。

有效 CSS 选择器的关键特征是特异性。根据这一点，ID 和类选择器总是比`div`和`*`这样结果较多的选择器更高效。在编写复杂的 CSS 选择器时，要记住它们是从右到左进行评估的，并且在递归测试每个父元素直到 DOM 根元素后，选择器将被拒绝。

因此，在执行选择器期间，尽量使最右边的选择器尽可能具体，以尽快减少匹配的元素数量。

```js
// initially matches all the anchors of the page 
// and then removes those that are not children of the container 
$('.container a'); 

// performs better, since it matches fewer elements 
// in the first step of the selector's evaluation 
$('.container .mySpecialLinks');
```

另一个性能提示是在适用的地方使用子选择器（"parent > child"），以消除对 DOM 树层次结构的递归。一个极好的应用案例是目标元素可以在共同祖先元素的特定后代级别找到的情况：

```js
// initially matches all the div's of the page, which is bad 
$('.container div') ;

// a lot faster than the previous one,
// since it avoids the recursive class checks
// until reaching the root of the DOM tree 
$('.container > div');

// best of all, but can't be used always 
$('.container > .specialDivs');
```

### 提示

相同的技巧也适用于用于页面样式的 CSS 选择器。尽管浏览器一直在尝试优化任何给定的 CSS 选择器，上述技巧可以极大地减少渲染网页所需的时间。

### 注意

有关 jQuery CSS 选择器性能的更多信息，您可以访问：[`learn.jquery.com/performance/optimize-selectors/`](http://learn.jquery.com/performance/optimize-selectors/)

# 编写高效的 jQuery 代码

现在让我们继续分析最重要的 jQuery 特定性能提示。有关 jQuery 最新性能提示的更多信息，请关注 jQuery 学习中心的相关页面：[`learn.jquery.com/performance`](http://learn.jquery.com/performance)

## 减少 DOM 遍历

由于 jQuery 使 DOM 遍历变得如此简单，许多 web 开发人员在每处都过度使用 `$()` 函数，甚至在后续的代码行中使用，通过执行不必要的代码来使其实现变慢。操作复杂性如此经常被忽视的主要原因之一是 jQuery 使用优雅和极简的语法。尽管 JavaScript 浏览器引擎在过去几年变得多次更快，性能可与许多编译语言媲美，但是 DOM API 仍然是它们最慢的组件之一，因此开发人员必须尽量减少与它的交互。

### 缓存 jQuery 对象

将 `$()` 函数的结果存储到本地变量中，并随后在检索到的元素上操作是消除不必要的相同 DOM 遍历执行的最简单方法。

```js
var $element = $('.boxHeader');
if ($element.css('position') === 'static') {
  $element.css({ position: 'relative' });
}
$element.height('40px');
$element.wrapInner('<b>');
```

在之前的章节中，我们甚至建议将重要页面元素的组合集合对象作为模块的属性进行存储，并在应用程序的各个地方重复使用：

```js
    dashboard.$container = null; 
    dashboard.init = function() { 
        dashboard.$container = $('.dashboardContainer'); 
    };
```

### 提示

当元素不会从页面中移除时，将检索到的元素缓存在模块上是一种非常好的做法。请记住，当处理生命周期较短的元素时，为了避免内存泄漏，您必须确保在从页面中删除它们时清除所有引用，或者在需要时重新检索新的引用，并仅在函数内部缓存它。

### 作用域元素遍历

而不是为遍历编写复杂的 CSS 选择器：

```js
$('.dashboardContainer .dashboardCategories');
```

你可以通过使用已经检索到的祖先元素来限定 DOM 遍历，以更高效的方式获得相同的结果。这样做，不仅使用了更简单的 CSS 选择器来匹配页面元素，而且减少了需要检查的元素数量。此外，生成的实现代码重复性较少（更干净），使用的 CSS 选择器简单，因此更易读。

```js
var $container = $('.dashboardContainer');
$container.find('.dashboardCategories');
```

另外，这种做法与整个模块范围的缓存元素一起使用效果更佳，就像我们在前几章中使用的那样：

```js
$boxContainer = dashboard.$container.find('.boxContainer');
```

### 链式调用 jQuery 方法

所有 jQuery API 的特点之一是它们是**流式**接口实现，使我们能够在单个组合集合对象上链式调用多个方法。

```js
$('.boxContent').html('') 
    .append('<a href="#">') 
    .height('40px') 
    .wrapInner('<b>');
```

正如我们在前几章中讨论的，链式调用可以减少所用变量的数量，并且以更少的代码重复实现更易读的实现。

## 不要过度使用

请记住，jQuery 还提供了 `$.fn.end()` 方法（[`api.jquery.com/end/`](http://api.jquery.com/end/)）作为从链式遍历中返回的一种方式。

```js
$('.box') 
    .filter(':even') 
    .find('.boxHeader') 
    .css('background-color', '#0F0') 
    .end() 
    .end() // undo the filter and find traversals
    .filter(':odd') // applied on  the initial .box results
    .find('.boxHeader') 
    .css('background-color', '#F00');
```

尽管这在许多情况下都是一个方便的方法，但你应该避免过度使用它，因为它可能会损害代码的可读性和性能。在许多情况下，使用缓存的元素集合而不是 `$.fn.end()` 可以获得更快、更可读的实现。

## 改进 DOM 操作

正如我们之前所说的，广泛使用 DOM API 是使应用程序变慢的最常见因素之一，特别是在用于操作 DOM 树状态时。在本节中，我们将展示一些改进操作 DOM 树性能的技巧。

### 创建 DOM 元素

创建 DOM 元素最有效的方式是构造一个 HTML 字符串并使用 `$.fn.html()` 方法将其附加到 DOM 树中。此外，由于在某些用例中这太过限制，你也可以使用 `$.fn.append()` 和 `$.fn.prepend()` 方法，虽然稍微慢一些，但可能更适合你的实现。理想情况下，如果需要创建多个元素，你应该尝试通过创建一个定义所有元素的 HTML 字符串，然后将其插入到 DOM 树中，如下所示：

```js
var finalHtml = ''; 
for (var i = 0, len = questions.length; i < len; i++) { 
  var question = questions[i]; 
  finalHtml += '<div><label><span>' + question.title + ':</span>' + 
    '<input type="checkbox" name="' + question.name + '" />' + 
  '</label></div>'; 
} 
$('form').html(finalHtml);
```

另一种实现相同结果的方法是使用数组来存储每个中间元素的 HTML，然后在插入到 DOM 树之前将它们连接起来：

```js
var parts = []; 
for (var i = 0, len = questions.length; i < len; i++) { 
  var question = questions[i]; 
  parts.push('<div><label><span>' + question.title + ':</span>' + 
    '<input type="checkbox" name="' + question.name + '" />' + 
  '</label></div>'); 
} 
$('form').html(parts.join(''));
```

### 注意

自近些年来，这是一个常用的模式，因为它比使用 "+=" 连接中间结果性能更好。

### 样式和动画

在可能的情况下，通过利用`$.fn.addClass()`和`$.fn.removeClass()`方法使用 CSS 类进行样式操作，而不是通过`$.fn.css()`方法手动操纵元素的样式。当你需要为大量元素设置样式时，这特别有用，因为这是 CSS 类的主要用途，并且浏览器已经花费了数年的时间对其进行优化。

### 提示

作为最小化操作元素数量的额外优化步骤，你可以在单个公共祖先元素上应用 CSS 类，并使用后代 CSS 选择器来应用你的样式，如此处所示：[`developer.mozilla.org/en-US/docs/Web/CSS/Descendant_selectors`](https://developer.mozilla.org/en-US/docs/Web/CSS/Descendant_selectors)

当你仍然需要使用`$.fn.css()`方法时，例如，当你的实现需要是命令式的时候，使用接受对象参数的调用重载：[`api.jquery.com/css/#css-properties`](http://api.jquery.com/css/#css-properties)。这样，在为元素应用多个样式时，所需的方法调用将被最小化，而且你的代码组织得更好。

此外，避免混合使用操纵 DOM 的方法和从 DOM 中读取数据的方法，因为这会强制页面重新排版，以便浏览器计算页面元素的新位置。

而不是像这样做：

```js
$('h1').css('padding-left', '2%'); 
$('h1').css('padding-right', '2%'); 
$('h1').append('<b>!!</b>'); 
var h1OuterWidth = $('h1').outerWidth(); 

$('h1').css('margin-top', '5%'); 
$('body').prepend('<b>--!!--</b>'); 
var h1Offset = $('h1').offset();
```

更好地将非冲突的操作分组在一起，像这样：

```js
$('h1').css({ 
    'padding-left': '2%', 
    'padding-right': '2%', 
    'margin-top': '5%' 
}).append('<b>!!</b>'); 
$('body').prepend('<b>--!!--</b>'); 

var h1OuterWidth = $('h1').outerWidth(); 
var h1Offset = $('h1').offset();
```

浏览器因此可以跳过对页面的一些重新渲染，从而减少代码执行时的暂停。

### 注意

有关重排的更多信息，请访问以下页面：[`developers.google.com/speed/articles/reflow`](https://developers.google.com/speed/articles/reflow)

最后，请注意，v1.x 和 v2.x 中所有由 jQuery 生成的动画都是使用`setTimeout()`函数实现的。这将在 jQuery 的 v3.x 中发生变化，该版本计划使用`requestAnimationFrame()`函数，这更适合创建命令式动画。在那之前，你可以使用**jQuery-requestAnimationFrame**插件 ([`github.com/gnarf/jquery-requestAnimationFrame`](https://github.com/gnarf/jquery-requestAnimationFrame))，它对 jQuery 进行了猴子补丁，以便在可用时使用`requestAnimationFrame()`函数进行动画。

### 操纵分离的元素

操纵 DOM 元素时避免页面不必要的重绘的另一种方法是将元素从页面中分离，并在完成操作后重新附加它。使用分离的内存中元素要快得多，并且不会导致页面重排。

为了实现这一点，我们使用`$.fn.detach()`方法，与`$.fn.remove()`相比，它保留了分离元素上的所有事件处理程序和 jQuery 数据。

```js
var $h1 = $('#pageHeader'); 
var $h1Cont = $h1.parent(); 
$h1.detach(); 

$h1.css({ 
    'padding-left': '2%', 
    'padding-right': '2%', 
    'margin-top': '5%' 
}).append('<b>!!</b>'); 

$h1Cont.append($h1); 
```

另外，为了能够将操作过的元素放回其原始位置，我们可以在 DOM 中创建并插入一个隐藏的占位符元素。这个空的隐藏元素不会影响页面的渲染，并在将原始项目放回其原始位置后被移除。

```js
var $h1PlaceHolder = $('<div style="display: none;"></div>'); 
var $h1 = $('#pageHeader'); 
$h1PlaceHolder.insertAfter($h1); 

$h1.detach(); 

$h1.css({ 
    'padding-left': '2%', 
    'padding-right': '2%', 
    'margin-top': '5%' 
}).append('<b>!!</b>'); 

$h1.insertAfter($h1PlaceHolder); 
$h1PlaceHolder.remove(); 
$h1PlaceHolder = null; 
```

### 注意

欲了解有关 `$.fn.detach()` 方法的更多信息，请阅读文档：[`api.jquery.com/detach/`](http://api.jquery.com/detach/)

### 引入 Flyweight 模式

根据计算机科学的说法，Flyweight 是一种对象，用于通过提供与其他对象实例共享的功能和/或数据来减少实现的内存消耗。JavaScript 构造函数的**原型**可以被定义为 Flyweights，因为每个对象实例都可以使用其原型中定义的所有方法和属性，直到覆盖它们为止。另一方面，经典的 Flyweights 是与它们一起使用的对象系列中的独立对象，并经常在特殊的数据结构中保存共享的数据和功能。

## 使用委托观察者

在 jQuery 应用程序中，委托观察者是 Flyweights 的一个很好的示例，正如我们在第二章中看到的*观察者模式*中的仪表板示例一样，它可以通过作为大量元素的集中事件处理程序来大大降低实现的内存需求。通过这种方式，我们可以避免为每个元素设置单独的观察者和事件处理程序的成本，并使用浏览器的事件冒泡机制在单个共同的祖先元素上观察它们并过滤它们的来源。

```js
$boxContainer.on('click', '.boxCloseButton', function() { 
    var $button = $(this); 
    dashboard.informationBox.close($button); 
});
```

### 注意

实际的 Flyweight 对象是与祖先元素附加的回调一起的事件处理程序。

## 使用 $.noop() 方法

jQuery 库提供了 `$.noop()` 方法，实际上是一个可以在不同实现之间共享的空函数。使用空函数作为默认回调值通过减少 `if` 语句的数量来简化和提高实现的可读性。这对于已经封装了复杂功能的 jQuery 插件非常方便。

```js
function doLater(callbackFn) { 
    setTimeout(function() { 
        if (callbackFn) { 
            callbackFn(); 
        } 
    }, 500); 
} 

// with $.noop() 
function doLater(callbackFn) { 
    callbackFn = callbackFn || $.noop(); 
    setTimeout(function() { 
        callbackFn(); 
    }, 500); 
} 
```

在这种情况下，无论是由于实现需求还是开发者的个人品味，都导致了使用空函数，`$.noop()` 方法都是一种降低内存消耗的有效方式，它通过在整个实现的所有不同部分之间共享一个空函数实例来实现。使用 `$.noop()` 方法的另一个好处是，我们还可以通过简单检查 `callbackFn === $.noop()` 来检查传递的函数引用是否为空函数。

### 注意

欲了解更多信息，请参阅文档：[`api.jquery.com/jQuery.noop/`](http://api.jquery.com/jQuery.noop/)

## 使用 $.single 插件

在 jQuery 应用程序中，另一个简单的享元模式示例是 *James Padolsey* 在他的文章 *76 bytes for faster jQuery* 中描述的 `jQuery.single` 插件，该插件尝试在单个页面元素上应用 jQuery 方法时消除创建新的 jQuery 对象。该实现非常小，创建一个单一的 jQuery 复合集合对象，在每次调用 `jQuery.single()` 方法时返回，该对象包含用作参数的页面元素。

```js
jQuery.single = (function(){ 
    var collection = jQuery([1]);
    // Fill with 1 item, to make sure length === 1 
    return function(element) { 
        collection[0] = element; // Give collection the element: 
        return collection; // Return the collection: 
    }; 
}());
```

当在 `$.fn.on()` 这样的观察者和 `$.each()` 这样的方法迭代中使用时，`jQuery.single` 插件非常有用。

```js
$boxContainer.on('click', '.boxCloseButton', function() { 
    // var $button = $(this); 
    var $button = $.single(this);
    // this is not creating any new object
    dashboard.informationBox.close($button); 
});
```

使用 `jQuery.single` 插件的好处在于我们创建的对象更少，因此当释放短生命周期对象的内存时，浏览器的垃圾回收器的工作量也会减少。

作为一个副作用，请注意每次调用`$.single()`方法时返回的单个 jQuery 对象以及最后一个调用参数将存储到下一次调用该方法之前的事实：

```js
var buttons = document.getElementsByTagName('button'); 
var $btn0 = $.single(buttons[0]); 
var $btn1 = $.single(buttons[1]); 
$btn0 === $btn1 // this is true
```

另外，如果你使用类似 `$btn1.remove()` 这样的方法，那么该元素将一直保留，直到下一次调用 `$.single()` 方法将其从插件的内部集合对象中删除为止。

另一个类似但更全面的插件是 `jQuery.fly` 插件，它可以使用数组和 jQuery 对象作为参数调用。

### 注意

关于 `jQuery.single` 和 `jQuery.fly` 的更多信息，请访问以下链接：[`james.padolsey.com/javascript/76-bytes-for-faster-jquery/`](http://james.padolsey.com/javascript/76-bytes-for-faster-jquery/) 和 [`github.com/matjaz/jquery.fly`](https://github.com/matjaz/jquery.fly)。

另一方面，处理带有单个页面元素的 `$()` 方法调用的 jQuery 实现并不复杂，只创建一个简单的对象。

```js
jQuery = function( selector, context ) { 
  return new jQuery.fn.init( selector, context ); 
}; 
/*...*/ init = jQuery.fn.init = function( selector, context, root ) { 
  /*... else */ 
  if ( selector.nodeType ) { 
    this.context = this[ 0 ] = selector; 
    this.length = 1; 
    return this; 
  } /* ... */ 
}; 
```

此外，现代浏览器的 JavaScript 引擎在处理短生命周期对象时已经非常高效，因为这些对象通常作为方法调用参数在应用程序中传递。

# 延迟加载模块

最后，我们将介绍一种高级技术——**延迟加载模块**。这种实践的关键概念是，在页面加载期间，浏览器仅下载并执行那些在页面的初始渲染过程中所需的模块，而其余的应用程序模块则在页面完全加载后，并且需要响应用户操作时才被请求。`RequireJS` ([`requirejs.org/`](http://requirejs.org/)) 是一个常用的 JavaScript 库，用作模块加载器，但对于简单情况，我们可以使用 jQuery 来实现相同的效果。

作为此的示例，我们将在用户首次单击仪表板上的 `<button>` 后，使用它来延迟加载我们在以前章节中看到的 Dashboard 示例的 `informationBox` 模块。我们将抽象出负责下载和执行 JavaScript 文件的实现，成为一个通用且可重用的模块，名为 `moduleUtils`。

```js
(function() { 
    'use strict'; 

    dashboard.moduleUtils = dashboard.moduleUtils || {}; 

    dashboard.moduleUtils.getModule = function(namespaceString) { 
        var parts = namespaceString.split('.'); 
        var result = parts.reduce(function(crnt, next){ 
            return crnt && crnt[next]; 
        }, window); 
        return result; 
    }; 

    var ongoingModuleRequests = {}; 

    dashboard.moduleUtils.ensureLoaded = function(namespaceString) { 
        var existingNamespace = this.getModule(namespaceString); 
        if (existingNamespace) { 
            return $.Deferred().resolve(existingNamespace); 
        } 

        if (ongoingModuleRequests[namespaceString]) { 
            return ongoingModuleRequests[namespaceString]; 
        } 

        var modulePromise = $.getScript(namespaceString.toLowerCase() + '.js') 
            .always(function() { 
                ongoingModuleRequests[namespaceString] = null; 
            }).then(function() { 
               return dashboard.moduleUtils.getModule(namespaceString); 
            }); 
        ongoingModuleRequests[namespaceString] = modulePromise; 
        return modulePromise; 
    };

})(); 
```

`getModule()` 方法接受模块的命名空间作为字符串参数，并返回模块的**单例对象**本身，或者如果模块尚未加载，则返回假值。这是通过使用 `Array.reduce()` 方法完成的，该方法用于迭代命名空间字符串的不同部分，使用点（.）作为分隔符，并在先前对象上下文中评估每个部分，从`window`开始。

### 注意

有关 `Array.reduce()` 方法的更多信息，请访问：[`developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)

`ensureLoaded()` 是 `moduleUtils` 模块的主要方法，负责检索并执行尚未加载的模块。它首先使用 `getModule()` 方法检查请求的模块是否已加载，如果是，则将其命名空间对象作为已解决的 Promise 返回。

下一步，如果模块尚未加载，则是检查 `ongoingModuleRequests` 对象，以验证请求的模块是否尚未下载。为了做到这一点，`ongoingModuleRequests` 对象将模块的命名空间字符串作为属性，并存储用于从服务器检索 `.js` 文件的 AJAX 请求的 Promises。如果有一个 Promise 对象可用，那么我们可以推断出 AJAX 请求仍在进行中，并且我们不会启动新的请求，而是返回现有的 Promise。

最后，当上述任何一个都没有返回结果时，我们使用在之前章节中讨论的小写模块文件命名约定，并使用 jQuery 的 `$.getScript()` 方法发起 AJAX 请求以检索所请求的模块文件。为 AJAX 请求创建的 Promise 被分配为 `ongoingModuleRequests` 对象的适当属性，并随后返回给方法的调用者。当在以后的时间点，Promise 被完成时，我们重新评估模块并将其作为返回的 Promise 的最终结果返回。此外，无论 AJAX 请求的结果如何，Promise 也会从 `ongoingModuleRequests` 对象中删除，以便在网络故障时保持实现的可重用性，并释放为请求分配的内存。

### 注意

请记住，当页面通过文件系统加载时，`$.getScript()` 方法可能在某些浏览器中无法工作，但在像 Apache、IIS 或 nginx 这样的 Web 服务器上加载时则可以正常工作。有关 `$.getScript()` 的更多信息，请访问：[`api.jquery.com/jQuery.getScript/`](http://api.jquery.com/jQuery.getScript/)

我们对现有的 `informationBox` 模块的实现仅做了一个改变，即使其自我初始化，以尝试减少 `ensureLoaded()` 方法的复杂性。

```js
(function() { 
    'use strict'; 

    dashboard.informationBox = dashboard.informationBox || {}; 

    var $boxContainer = null; 

    dashboard.informationBox.init = function() { /* … */ };

    $(document).ready(dashboard.informationBox.init); 

   /*...*/
})(); 
```

最后，我们还必须更改 `categories` 模块的实现，以便在使用 `informationBox` 模块之前使用 `ensureLoaded()` 方法。正如您下面所见，我们不得不重构处理仪表板 `<button>` 的点击事件的代码，因为 `ensureLoaded()` 方法返回一个 Promise 作为结果：

```js
// in dashboard.categories.init 
dashboard.$container.find('.dashboardCategories').on('click', 'button', function() { 
    var $button = $(this); 
    var itemName = $button.text(); 

    var p = dashboard.moduleUtils.ensureLoaded('dashboard.informationBox');

    p.then(function(){ 
        dashboard.informationBox.openNew(itemName); 
    }); 
});
```

# 摘要

在本章中，我们学习了几种优化技术，可以用来提高 jQuery 应用程序的性能，特别是当它们变得庞大和复杂时。

我们从简单的实践开始，比如捆绑和缩小我们的 JavaScript 文件，并讨论了使用 CDN 加载第三方库的好处。然后，我们继续分析了一些编写高效 JavaScript 代码的简单模式，并学习了如何编写高效的 CSS 选择器来提高页面的渲染速度，并使用 jQuery 来改进 DOM 遍历。

我们继续使用 jQuery 特定的实践，例如缓存 jQuery 组合集合对象、如何最小化 DOM 操作，并提醒代理观察者模式，作为享元模式的一个很好的例子。最后，我们介绍了惰性加载的高级技术，并演示了如何根据用户操作逐步加载实现的各个模块。

完成本章后，我们现在能够将最常见的优化模式应用于我们的实现，并在将应用程序移至生产环境之前，将本章用作最佳实践和性能提示的检查清单。
