# 第二章：jQuery 选择器和过滤器

名称“jQuery”来自于该库快速而智能地查询**DOM**（**文档对象模型**）的能力。

在本章中，我们将学习如何自己查询 DOM，并且一旦我们有了一组项目，我们将学习如何使用过滤器进一步细化我们的数据集。我们将涵盖以下主题：

+   选择器和过滤器是什么

+   如何创建选择器

+   如何创建过滤器

+   如何根据元素属性进行查询

+   如何使用链式方法快速而整洁地继续查询

# jQuery 选择器

在每个浏览器网页的底层是 DOM。DOM 跟踪渲染到页面上的所有单个对象。能够找到 DOM 元素是创建动态网页的第一步。浏览器带有内置方法来查询 DOM。这些方法通常以`document`开头。有`document.getElementById`、`document.getElementsByClass`等等。这些方法的问题在于它们的接口既不一致也不完整。

jQuery 之所以得名于其查询 DOM 的能力。与浏览器方法不同，它的接口既完整又功能丰富。查询 DOM 的开始是创建选择器。选择器是一个字符串，告诉 jQuery 如何找到你想要的元素。由于它是一串文本，因此可能的选择器数量是无限的。但不要惊慌；它们都属于几个广泛的类别。

## 章节代码

本章的代码包含在一个名为`chapter02`的目录中。请记住，这是用于教学的示例代码，不适用于生产。代码做了两件特别乏味而值得一提的事情。首先，它使用内联 JavaScript，这意味着 JavaScript 包含在主页面的 HTML 中的脚本标签中。这违反了关注点分离的规则，可能会导致性能不佳的代码。其次，它大量使用警报。浏览器的`alert`方法是在屏幕上快速而肮脏地显示东西的一种方法，与`console.log`方法不同，所有浏览器都支持它。但不建议在生产中使用。警报无法进行样式设置，因此除非您的网站未进行样式设置，否则它们会显得非常突兀。其次，警报会使浏览器停止所有操作，并强制用户承认它们，这显然是会让用户快速变得烦躁的事情：

```js
    function showJqueryObject(title, $ptr) {
        var ndx, sb = title + "\n";

        if ($ptr) {
            for (ndx = 0; ndx < $ptr.length; ndx += 1) {
                sb += $ptr[ndx].outerHTML + "\n";
            }
            alert(sb);
        }
    }
```

用于显示 jQuery 对象的方法被命名为`showJqueryObject()`。它通过 jQuery 对象进行迭代，依次显示每个节点。现在，说实话，我只在这本书中使用这个方法。在开发自己的程序时，处理问题时，我通常依赖于浏览器的`console.log`方法。但由于并非所有浏览器都支持它，而且对支持它的浏览器来说，显示东西在屏幕上的最简单方法是编写自己的方法。

## 协议相对 URL

一件敏锐的读者可能会注意到的有趣事情是脚本标签中缺少了 URL 的协议。我不是网络安全专家，但我足够聪明，足以留意网络安全专家的警告，所有这些专家都会说混合使用 HTTP 协议是危险的。协议相对 URL 使得保持网站安全变得容易。现在，许多网站都会在开放的 HTTP 或安全的 HTTP（HTTPS）上运行。以前实现这一点需要从你自己的网站加载所有 JavaScript 库，放弃 CDN 的好处，或者包含一些复杂的内联 JavaScript 代码来检测你网站的协议，并使用`document.write`将一些新的 JavaScript 注入到页面中。

使用协议相对 URL，你只需省略 URL 中的协议。然后，当你的网站被加载时，如果它是通过 HTTP 加载的，则库也将通过 HTTP 加载。如果是通过 HTTPS 加载的，那么所有库也将通过 HTTPS 加载。

## jQuery 对象

在我们查看 jQuery 选择器的类之前，让我们先看看选择器返回了什么。调用选择器的结果始终是一个 jQuery 对象。jQuery 对象具有许多类似数组的功能，但它不是数组。如果你在其上使用 jQuery 的 `isArray()` 函数，它将返回 false。对于许多事情，这种差异并不重要，但偶尔你可能想要执行像 `concat()` 这样的操作，但不幸的是，这个数组方法在 jQuery 对象上不存在，尽管它有一个几乎相等的方法 `add()`。

如果没有找到匹配选择器的元素，则 jQuery 对象的长度为零。永远不会返回 null 值。结果是一个长度为零或更多的 jQuery 对象。理解这一点很重要，因为它减少了在检查 jQuery 调用结果时需要做的工作量。你只需检查长度是否大于零即可。

当调用 jQuery 时，你可以使用其正式名称`jQuery`，也可以使用其缩写名称`$`。此外，请注意，在 JavaScript 中，字符串可以以单引号或双引号开头，只要它们以相同的引号结束即可。因此，在示例中，你可能会看到单引号或双引号，而且通常没有选择单引号或双引号的原因。

### 注意

关于浏览器中 JavaScript 的有趣之处之一是，一些方法实际上不是 JavaScript 的一部分；相反，它们是由浏览器提供的。当你在非基于浏览器的环境中使用 JavaScript，比如 `node.js` 时，这一点就变得很明显。例如，文档方法的方法不是 JavaScript 的一部分；它们是浏览器的一部分。在 `node.js` 中，没有文档对象，因此也没有文档方法。浏览器方法的另外两个来源是 window 和 navigator 对象。

# 创建选择器

使 jQuery 选择器如此酷且易学的一点是它们基于 CSS 中使用的选择器；这在浏览器方法中并非如此。因此，如果您已经知道如何创建 CSS 选择器，那么学习 jQuery 选择器将毫不费力。如果您不了解 CSS 选择器，也不用担心；jQuery 选择器仍然很容易学习，并且了解它们将让您提前学习 CSS 选择器。

## ID 选择器

我们将要看的第一种选择器是 ID 选择器。它以井号开头，后跟所需元素的 ID 属性中使用的相同字符串。看看这个例子：

```js
<div id="my-element"></div>
var jqObject = $("#my-element");
or 
var jqObject = jQuery('#my-element');

```

从上述示例可以看出，选择器调用的结果是一个 jQuery 对象。我们可以自由地使用引号的任一样式。此外，我们可以使用正式名称或缩写名称进行调用。在本书的其余部分，我将使用简称`$`。`ID`选择器返回一个数组，其中包含零个或一个元素。它永远不会返回多于一个元素。

根据**W3C**（**万维网联盟**）规范，每个 DOM 元素最多可以有一个`ID 选择器`，并且每个`ID 选择器`必须对网页唯一。现在，我见过很多违反唯一性规则的网站。这些都是糟糕的网站；不要效仿它们。而且，它们有不可预测的代码。当存在多个具有相同 ID 的元素时，只返回一个元素，但没有规定应该返回哪一个。因此，代码可能会根据运行的浏览器不同而表现不同。

## 类选择器

我们将要检查的下一个选择器类型是类选择器。类选择器以句点开头，后跟所有所需元素的类的名称。与最多返回一个元素的 ID 选择器不同，类选择器可以返回零个、一个或多个元素。

元素中类的顺序无关紧要。它可以是第一个、最后一个或中间的某个类，jQuery 都可以找到它：

```js
var jqClassObject = $('.active');

```

## 标签选择器

有时，您想要的元素既没有 ID 也没有类名；它们只是具有相同的标签名称。这就是您将使用标签选择器的时候。它搜索具有特定标签名称的元素，例如`div`、`p`、`li`等等。要创建标签选择器，只需传递带引号括起来的标签名称：

```js
var jqTagObject = $('div');

```

## 结合选择器

如果我们想要在页面上将所有段落标签和所有包含`active`类的元素放在一个集合中，我们可以进行两次调用，然后使用 jQuery 的`add`方法将结果对象相加。更好的方法是组合选择器，让 jQuery 执行组合结果的繁重工作。要组合选择器，只需将两个或更多选择器放在字符串中并用逗号分隔：

```js
    var jqClassResults = $('.special'),
            jqTagResults = $('p'),
            jqTotal = jqClassResults.add(jqTagResults);

var jqTotal = $('.special, p'); // give me all of the elements with the special class and all of the paragraphs
```

重要的是要记住，逗号用来分隔选择器。如果忘记了逗号，你不会收到错误消息；你会得到一个结果，只是不是你所期望的结果。相反，它将是后代选择器的最终结果，这是我们马上就会讨论的一个东西。

## 后代选择器

有时，你希望选择的元素没有一个易于设计的选择器。也许它们都是特定元素的子元素或孙子元素。这就是后代选择器的用武之地。它们允许你将查询的焦点缩小到特定的祖先，然后在那里查询。后代选择器有两种类型：子选择器和后代选择器。所需的子元素必须是父元素的直接子元素。要创建一个子选择器，你需要创建父选择器，加上一个大于符号，然后加上要在父元素结果集中查找子元素的选择器。

考虑以下 HTML 标记：

```js
<ul id="languages">
    <li>Mandarin</li>
    <li>English
        <ul class="greetings">
            <li class="main-greeting">Hi</li>
            <li>Hello</li>
            <li>Slang
                <ul data-troy>
                    <li>What's up doc?</li>
                </ul>
            </li>
        </ul>
    </li>
    <li>Hindustani</li>
    <li data-troy="true">Spanish</li>
    <li>Russian</li>
    <li>Arabic</li>
    <li>Bengali</li>
    <li>Portuguese</li>
    <li>Malay-Indonesian</li>
    <li>French</li>
</ul>
```

还看一下以下代码：

```js
    var jqChildren = $('#languages>li');
    showJqueryObject("Direct", jqChildren);
```

上面的代码示例将返回所有是 `<ul id="languages">` 元素的子级的 `<li>` 标签。它不会返回 `<ul class="greetings">` 标签的子级 `<li>` 标签，因为这些是它的孙子：

```js
    var jqDescendant = $('#languages li');
    showJqueryObject("Direct", jqDescendant);
```

后代选择器几乎与子选择器完全相同，只是没有大于号。第二个查询将返回包含在 `<ul id="languages">` 中的所有 `<li>` 标签，而不管它们在后代树中的位置。

## 属性选择器

除了根据元素的基本特征（如名称、标签和类）选择元素外，我们还可以根据它们的属性选择元素。属性选择器在工作时有点棘手，因为它们的语法比较复杂。另外，请记住，与其他 jQuery 选择器一样，如果你的语法错误，不会收到错误消息；你只会得到一个长度为零的 jQuery 对象。

只有九个属性选择器，所以它们相当容易记住，具体如下：

+   `有属性`选择器

+   `属性等于`选择器

+   `属性不等于`选择器

+   `属性包含`选择器

+   `属性起始为`选择器

+   `属性结尾为`选择器

+   `属性包含前缀`选择器

+   `属性包含单词`选择器

+   `多个属性`选择器

让我们从最简单的一个开始：`有属性`选择器。它选择所有具有指定属性的元素。属性的值无关紧要；事实上，甚至不必有值：

```js
var jqHasAttr = $("[name]");
```

就选择器而言，这个非常简单。它由所需属性的名称括在方括号中组成。接下来的两个选择器略微复杂一些。

`Has Attribute` 选择器不关心属性的值，但有时，您需要属性要么具有特定的值，要么不具有特定的值。这就是 `Attribute Equals` 选择器及其相反者 `Attribute Not Equals` 选择器的作用。前者返回所有具有所需属性和所需值的元素。请记住，这必须是精确匹配；接近的东西不算。后者返回所有没有所需属性或者具有但不具有所需值的元素。`Attribute Not Equals` 选择器返回 `Attribute Equals` 选择器未返回的所有元素。有时这会让人感到困惑，但不应该。只要记住这两个选择器是相反的：

```js
    var jqEqualAttr = $('[data-employee="Bob"]');

    var jqNotEqualAttr = $('[data-employee!="Bob"]');
```

下面的三个选择器也彼此相关。每个选择器都寻找一个具有值的属性，只是现在值是子字符串而不是完全匹配。区分这三者的是它们寻找子字符串的位置。第一个选择器，`Attribute Contains`，在属性值的任何位置寻找子字符串。只要字符串包含在值中，它就通过：

```js
    var jqContainsAttr = $('[data-employee*="Bob"]');
    showJqueryObject("Contains Attribute", jqContainsAttr);
```

`Attribute Starts With` 选择器更加具体地指定了子字符串的位置。它要求字符串必须位于值的开头。值字符串不必完全匹配；只有字符串的开头必须匹配：

```js
    var jqStartsAttr = $('[data-employee^="Bob"]');
    showJqueryObject("Starts Attribute", jqStartsAttr);
```

`Attribute Ends With` 选择器将指定的字符串与值字符串的末尾匹配。如果值字符串的末尾与指定的字符串匹配，那就没问题。就像 `Attribute Starts With` 选择器一样，剩下的字符串不必匹配：

```js
    var jqEndsAttr = $('[data-employee$="Bob"]');
    showJqueryObject("Ends Attribute", jqEndsAttr);
```

区分这些选择器有点麻烦，因为它们只有一个字符的差异，但是如果您了解 JavaScript 正则表达式，您会认识到 jQuery 借鉴了它们。正则表达式中的 '`^`' 表示字符串的开头，它用于 `Attribute Begins With` 选择器。正则表达式中的 '`$`' 表示字符串的结尾，它用于 `Attribute Ends With` 选择器。`Attribute Contains` 选择器使用了星号 `*`，它是正则表达式通配符字符。

`Attribute Contains Prefix` 选择器在值字符串的开头寻找指定的字符串。它与 `Attribute Begins With` 选择器的区别在于，如果没有连字符，则字符串必须与值完全匹配。如果值中有连字符，则字符串需要与连字符匹配。

`Attribute Contains Word` 选择器检查值字符串中的指定字符串。这听起来可能与 `Attribute Contains` 选择器很相似，但有一个微妙的区别。指定的字符串必须被空白符包围，而 `Attribute Contains` 选择器不关心字符串在哪里或者什么是分隔它的：

```js
Attribute Contains Word Selector [name~="value"]
```

最后一个属性选择器是`Multiple Attribute`选择器，它只是前面任何属性选择器的组合。每个选择器都包含在自己的方括号内，并连接在一起。不需要任何分隔字符：

```js
Multiple Attribute Selector [name="value"][name2="value2"]
```

现在我们已经学会了一些选择元素的方法，让我们学习如何过滤我们选择的结果集。

# 创建基本过滤器选择器

过滤器将选择器调用的结果进一步减少。有三种类型的过滤器选择器：基本、子、内容。基本选择器仅在 jQuery 对象结果集上操作。子选择器在元素之间的父子关系上操作。内容过滤器处理结果集中每个元素的内容。

有 14 个基本过滤器选择器。第一组处理结果集中结果的位置。让我们首先处理它们。

最容易理解的之一是`:eq()`过滤选择器。当传递索引号时，它检索结果。它非常类似于访问 JavaScript 数组中的元素。就像数组一样，它是从零开始的，所以第一个元素是零，而不是一：

```js
var jqEq = $("a:eq(1)");
showJqueryObject("EQ Attribute", jqEq);
```

您可以通过在常规选择器后添加冒号和`eq(x)`来创建一个`:eq()`过滤选择器。这里的`x`是元素的索引。如果索引超出范围，将不会生成错误。

`:eq()`过滤器选择器允许您访问结果集中的任何项，只要您知道可能的索引范围。但有时候，这太费力了。您可能只想知道第一个或最后一个元素。对于这些情况，jQuery 提供了`:first`和`:last`过滤选择器。每个选择器都正如它们的名字所说的那样：它获取第一个或最后一个元素：

```js
    var jqFirst = $("a:first");
    showJqueryObject("First Attribute", jqFirst);
    var jqLast = $("a:last");
    showJqueryObject("Last Attribute", jqLast);
```

保持与索引操作的主题一致，有时我们想要获取到某个索引之前的所有元素或所有索引大于某个数字的元素。对于这些时候，有`:lt()`和`:gt()`选择器。`:lt()`选择器返回所有索引小于传递值的元素。`:gt()`选择器返回所有索引大于传递值的元素。这两个选择器还接受负值，它们是从结果集的末尾开始计数而不是从开头开始：

```js
    var jqLt = $("a:lt(1)");
    showJqueryObject("Less Than Selector", jqLt);
    var jqGt = $("a:gt(1)");
    showJqueryObject("Greater Than Attribute", jqGt);
    jqLt = $("a:lt(-4)");
    showJqueryObject("Less Than Selector", jqLt);
    jqGt = $("a:gt(-2)");
    showJqueryObject("Greater Than Attribute", jqGt);
```

最后两个基本过滤器非常方便。它们是`:even`和`:odd`选择器。它们非常简单，除了一个小小的怪异之处。JavaScript 是从零开始的，所以 0 是第一个元素，零是偶数，这意味着偶数选择器将获取第一个、第三个、第五个（以此类推）元素。另外，奇数选择器将获取第二个、第四个、第六个（以此类推）元素。看起来有些奇怪，但只要记住 JavaScript 是从零开始的就完全说得通。

剩下的基本属性过滤器没有清晰地归入任何类别。因此，我们将逐一讨论它们，从 `:animated` 选择器开始。`:animated` 选择器返回当前正在执行动画的所有元素。请记住，它不会自动更新，因此在运行查询时，事物的状态是在那时存在的，但之后事物会发生变化。

`:focus` 选择器返回当前选定的元素（如果它在当前结果集中）。对于这个选择器要小心。我将在后面的章节中讨论性能问题，但是如果不与结果集一起使用，这个选择器的性能可能非常差。考虑一下，你只是简单地调用以下内容：

```js
    var jqFocus = $(":focus");
    showJqueryObject("The Focused Element", jqFocus);
```

它将搜索整个 DOM 树以找到焦点元素，该元素可能存在，也可能不存在。虽然前面的调用会起作用，但是有更快的方法可以做到这一点。

`:header` 选择器是一个方便的实用方法，返回所有 `<h1>`、`<h2>`、`<h3>`、`<h4>`、`<h5>` 和 `<h6>` 元素。你自己制作这个方法的等效版本应该相当容易，但是为什么要费这个事呢，当这个快捷方式随手可得时：

```js
    var jqHeader = $(":header");
    showJqueryObject("The Header Elements", jqHeader);
```

`:lang` 选择器将查找所有与指定语言匹配的元素。它将匹配单独的语言代码，如 `en`，或者语言代码与国家代码配对，如 `en-us`。随着越来越多的全球性网站的重视，这个选择器每天都变得更加重要：

```js
    var jqLang = $("a:lang('en')");
    showJqueryObject("The Lang Elements", jqLang);
```

`:not` 选择器不是我的最爱之一。它对传递的选择器执行逻辑 `not` 操作。

它从 jQuery 的开始就存在了，但是在我看来，它导致了混乱的代码。而 jQuery 最不需要的就是更难以阅读的代码。我将在第六章中更多地讨论如何编写可读的 jQuery 代码，*使用 jQuery 改进表单*，但是现在，如果可以的话，你应该避免使用它：

```js
    var jqNot = $( "a:not(:lang('en'))");
    showJqueryObject("The Not Elements", jqNot);
```

`:target` 选择器听起来很复杂，但是鉴于单页 JavaScript 应用程序的丰富性，它可能非常有用。它查看当前页面的 URL。如果它有一个片段，即用散列符号标识的片段，它将匹配 ID 与该片段相匹配的元素。这是 jQuery 的较新的添加之一，它是在 1.9 版本中添加的。

最后，让我们谈谈 `:root` 选择器。我要诚实地承认，我还没有需要使用这个选择器。它返回文档的根元素，这个根元素始终是 HTML 中的 `<html>` 元素：

```js
    var jqRoot = $( ":root");
    alert("The Root Element: " + jqRoot.get(0).tagName);
```

## 内容过滤器

只有四个内容过滤选择器。我会对所有这些选择器提出一个一般性的警告，因为它们可能性能不佳。我将在第七章中更详细地讨论性能，*与服务器交流*，但是在那之前，只有在必要时才使用这些选择器。

这些选择器中的前两个，`:empty`和`:parent`，是彼此的反义词。第一个，`:empty`选择器，返回所有没有子元素的元素。第二个，`:parent`选择器，选择至少有一个子元素的所有元素。

`:contains`选择器返回所有具有指定字符串的元素。字符串的大小写必须匹配。字符串可以位于任何后代元素中。

`:has()`选择器选择所有包含至少一个匹配项的元素。

接下来是子选择器。这些都涉及元素之间的父子关系。第一组很容易理解；它处理子元素在所有子元素中的位置。所有示例代码都将引用以下标记：

```js
<div>
    <p>I am the first child of div #1.</p>
    <p>I am the second child of div #1.</p>
    <p>I am the third child of div #1.</p>
    <p>I am the fourth child of div #1.</p>
    <p>I am the fifth child of div #1.</p>
</div>
<div>
    <p>I am the first child of div #2.</p>
    <p>I am the second child of div #2.</p>
    <p>I am the third child of div #2.</p>
</div>
<div>
    <p>I am the only child of div #3.</p>
</div>
```

首先是`:first-child`选择器；它选择所有作为其父元素的第一个子元素的元素。与`:first`选择器不同，它可以返回多个元素：

```js
    var jqChild = $('div p:first-child');
    showJqueryObject("First Child", jqChild);
```

示例代码的第一行说你应该获取所有`div`标签的段落后代。有三个定义的`<div>`标签；第一个有五个段落，第二个有三个段落，第三个只有一个段落。`:first-child`选择器查看这九个段落，并找到它们各自父元素的前三个子元素。这里它们是：

```js
<p>I am the first child of div #1.</p>
<p>I am the first child of div #2.</p>
<p>I am the only child of div #3.</p>
```

`:last-child`选择器的操作方式类似于`first-child`，只是它搜索其各自父元素的最后一个子元素：

```js
    var jqLastChild = $('div p:last-child');
    showJqueryObject("Last Child", jqLastChild);
```

再次，我们要求获取所有`div`标签的段落子元素，然后用子选择器过滤返回集。这个示例几乎与之前的示例相同，只是返回的元素是从末尾而不是从开头计数的：

```js
<p>I am the fifth child of div #1.</p>
<p>I am the third child of div #2.</p>
<p>I am the only child of div #3.</p>
```

如果你对最后一段`<p>我是第三个 div 的唯一子元素。</p>`与两个方法调用相同这一事实感到惊讶，不要惊慌。记住，它是第三个`<div>`的唯一子元素，因此它既是第一个也是最后一个子元素。

接下来，我们有`:nth-child()`选择器。它返回传递给方法的索引的子元素。`first-child`选择器可以被视为传递索引为 1 的这个选择器的特殊情况。正如我之前提到的，如果传递了超出范围的索引值，不会出现错误；你只会得到一个长度等于 0 的 jQuery 对象：

```js
    var jqNthChild = $('div p:nth-child(2)');
    showJqueryObject("Nth Child", jqNthChild);
```

关于这个和`:nth-last-child()`选择器的一个特殊之处是，除了传递索引值之外，你还可以传递一些特殊值。前两个特殊值是`even`和`odd`。这些将返回其各自父元素的偶数和奇数子元素。结果集与`:even`选择器的结果集不同。因为这些选择器是基于 CSS 的，计数从一开始，而不是 JavaScript 中的零。所以，看一下以下代码：

```js
    var jqNthChild = $('div p:nth-child(odd)');
    showJqueryObject("Nth Child", jqNthChild);
```

奇数参数将返回第一个、第三个、第五个（依此类推）元素：

```js
<p>I am the first child of div #1.</p>
<p>I am the third child of div #1.</p>
<p>I am the fifth child of div #1.</p>
<p>I am the first child of div #2.</p>
<p>I am the third child of div #2.</p>
```

`:nth-last-child()` 选择器与 `nth-child()` 选择器本质上相同，只是从列表末尾向前计数而不是从开头。同样，`:last-child` 选择器也可以被视为该选择器的特例，传递的索引为 1。和 `:nth-child` 选择器一样，它可以接受 even、odd 或公式等特殊参数：

```js
    var jqNthLastChild = $('div p:nth-last-child(3)');
    showJqueryObject("Nth Last Child", jqNthLastChild);
```

示例代码的结果集由两个元素组成，分别是它们各自父元素的倒数第三个子元素：

```js
<p>I am the third child of div #1.</p>
<p>I am the first child of div #2.</p>
```

子选择器中的最后一个是 `:only-child`。它返回其各自父元素的唯一子元素：

```js
    var jqOnlyChild = $('div p:only-child');
    showJqueryObject("Only Child", jqOnlyChild);
```

由于只有一个段落是唯一的子元素，所以下一个元素被显示：

```js
<p>I am the only child of div #3.</p>
```

接下来的选择器是 `of-type` 选择器。所有这些选择器都作用于相同元素类型的兄弟元素。其中第一个是 `:first-of-type` 选择器，它将返回每个父元素类型的第一个元素：

```js
    var jqFirstOfType = $('p:first-of-type');
    showJqueryObject("First Of Type", jqFirstOfType);
```

子选择器和 `of-type` 选择器之间的区别微妙但重要。`of-type` 选择器不指定父元素。你只需告诉 jQuery 关于子元素，它就会弄清哪些是兄弟元素，父元素是谁。

`:last-of-type` 选择器根据我们已经学到的关于 jQuery 的所有内容，做的正是我们所期望的。它返回了该类型的最后一个元素：

```js
    var jqLastOfType = $('p:last-of-type');
    showJqueryObject("Last Of Type", jqLastOfType);
```

`of-type` 选择器中的最后一个看起来很熟悉；它是 `:only-of-type` 选择器。像 `:only-child` 选择器一样，它返回唯一的子元素。但不像 `:only-child` 选择器，它查看元素的类型。所以，考虑到我们将第三个 `<div>` 更改为以下内容：

```js
<div>
    <p>I am the only child of div #3.</p>
    <span>Here is some text</span>
</div>
```

运行以下代码示例时，我们进行了更改：

```js
    var jqOnlyOfType = $('p:only-of-type');
    showJqueryObject("Only Of Type", jqOnlyOfType);
```

这将返回以下元素：

```js
<p>I am the only child of div #3.</p>
```

然而，使用 `:only-child` 选择器编写的相应代码不返回任何元素：

```js
    var jqOnlyChild = $('div p:only-child');
    showJqueryObject("Only Child", jqOnlyChild);
```

# 使用链接快速整洁地继续查询

jQuery 的一个好处是它能够链接查询。任何返回 jQuery 对象的方法调用都可以链接，这意味着你只需要添加一个句点和你的下一个方法调用。但要小心，因为并不是所有方法都返回 jQuery 对象。一些方法，如 `.width()`，返回无法链接的值。链中的每个方法都在前一个方法调用的结果集上执行其操作。如果在链的后续操作中执行过滤操作，则结果集将减少，链式方法将在减少的集合上工作而不是原始集合上。

下一个示例不是最好的，但希望它能开始展示链接的强大之处：

```js
    var jqLiUl = $("li").find("ul");
    showJqueryObject("LL UL", jqLiUl);
```

在这个示例中，我们首先要求页面上的所有 `<li>` 元素。然后，有了结果集，我们要求它包含的所有 `<ul>` 元素。我们可以用以下代码更简洁地请求：

```js
    var jqLiUl = $("li ul");
    showJqueryObject("LL UL concise", jqLiUl);
```

这里的要点是我们利用了 jQuery 的链式能力来在结果集上执行更多操作。通常情况下，您会使用链式来在链中的每个链接中执行不同的操作。您可能想在一个步骤中添加 CSS 类，对另一个步骤进行 DOM 操作，依此类推。为了清晰起见，请尽量保持所有操作相关，否则您可能会编写难以阅读的代码。

# 总结

在本章中，我们开始深入研究 jQuery，并希望发现它并不像从外面看起来那样复杂。我们也看到 jQuery 习惯于使用现有的做事方式。例如，它使用 CSS 选择器和与 JavaScript 正则表达式类似的符号。它还遵循一种常规模式来分解事物。一旦你习惯了这些模式，你几乎可以预测某些方法的存在。

现在我们已经学会了如何从 DOM 中读取元素，在下一章中，我们将学习如何向 DOM 写入标记，并开始制作动态网页。
