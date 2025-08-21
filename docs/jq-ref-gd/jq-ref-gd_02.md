# 第二章：选择器表达式

> 你让我仰望高处
> 
> 你让我搜索到最低处
> 
> —Devo，
> 
> "Jerkin' Back 'n' Forth"

借鉴了 CSS 1-3 和基本 XPath，然后添加了自己的内容，jQuery 提供了一组强大的选择器表达式，用于在文档中匹配一组元素。在本章中，我们将依次检查 jQuery 提供的每个选择器表达式。

# CSS 选择器

以下选择器基于 W3C 制定的 CSS 1-3。有关规范的更多信息，请访问[`www.w3.org/Style/CSS/#specs`](http://www.w3.org/Style/CSS/#specs)。

## 元素：T

所有标签名为`T`的元素。

### 示例

1.  `$('div')`: 选择文档中所有标签名为 `div` 的元素

1.  `$('em')`: 选择文档中所有标签名为 `em` 的元素

### 描述

jQuery 使用 JavaScript 的 `getElementsByTagName()` 函数进行标签名选择器。

## ID：#myid

具有 ID 等于 `myid` 的唯一元素。

### 示例

1.  `$('#myid')`: 选择具有 `id='myid'` 的唯一元素，无论其标签名是什么

1.  `$('p#myid')`: 选择具有 `'myid'` `id` 的单个段落；换句话说，选择唯一的元素 `<p id='myid'>`

### 描述

每个 `id` 值在文档中只能使用一次。如果有多个元素被分配了相同的 `id`，那些使用该 `id` 的查询将只选择 DOM 中匹配的第一个元素。

可能不会立即清楚为什么有人想要指定与特定 `id` 关联的标签名，因为该 `id` 本身必须是唯一的。然而，在某些用户生成 DOM 的情况下，可能需要更具体的表达式以避免误报。此外，当相同的脚本在多个页面上运行时，可能需要识别 `id` 的元素，因为页面可能将相同的 `id` 与不同的元素关联起来。例如，页面 A 可能具有 `<h1 id='title'>` 而页面 B 具有 `<h2 id='title'>`。

对于简单的 `id` 选择器，例如上面的示例 2，jQuery 使用 JavaScript 函数 `getElementById()`。如果脚本的执行速度至关重要，则应使用简单的 `id` 选择器。

## 类：.myclass

所有具有类名为 `myclass` 的元素。

### 示例

1.  `$('.myclass')`: 选择所有具有类名为 `myclass` 的元素

1.  `$('p.myclass')`: 选择所有具有类名为 `myclass` 的段落

1.  `$('.myclass.otherclass')`: 选择所有具有类名为 `myclass` 和 `otherclass` 的元素

### 描述

就速度而言，示例 2 通常比示例 1 更可取（如果我们可以限制查询到给定的标签名称），因为它首先使用本机 JavaScript 函数 `getElementsByTagName()` 来过滤其搜索，然后在匹配的 DOM 元素子集中查找类。相反，目前没有本机的 `getElementsByClassName()` 供 jQuery 使用，因此使用裸类名会迫使 jQuery 将其与 DOM 中的每个元素匹配。然而，速度上的差异取决于页面的复杂性和 DOM 元素的数量。

一如既往，请记住开发时间通常是最宝贵的资源。除非明确需要改善性能，否则不要专注于选择器速度的优化。

作为 CSS 选择器，现代所有网络浏览器都支持示例 3 的多类语法，但不包括 Internet Explorer 6 及以下版本，这使得该语法特别适用于通过 jQuery 跨浏览器应用样式。

## 后代：E F

所有由 `F` 匹配的元素，这些元素是由 `E` 匹配的元素的后代。

### 举例

1.  `$('#container p')`: 选择所有由`<p>`匹配的元素，这些元素是具有`container` id 的元素的后代。

1.  `$('a img')`: 选择所有由 `<a>` 匹配的元素的后代 `<img>` 元素。

### 描述

元素的后代可以是该元素的子元素、孙子元素、曾孙元素等等。例如，在以下 HTML 中，`<img>` 元素是 `<span>、<p>、<div id="inner">` 和 `<div id="container">` 元素的后代：

```js
<div id="container">
  <div id="inner">
    <p>
      <span><img src="img/example.jpg" alt="" /></span>
    </p>
  </div>
</div>
```

## 子级：E > F

所有由 `F` 匹配的元素，这些元素是由 `E` 匹配的元素的子级。

### 举例

1.  `$('li > ul')`: 选择所有由`<li>`匹配的元素的子元素`<ul>`。

1.  `$('p > code')`: 选择所有由`<p>`匹配的元素的子元素`<code>`。

### 描述

作为 CSS 选择器，子级组合器被所有现代网络浏览器支持，包括 Safari、Mozilla/Firefox 和 Internet Explorer 7，但显然不包括 Internet Explorer 6 及以下版本。示例 1 是选择所有嵌套无序列表（即除了顶层之外）的方便方法。

子级组合器可以被视为（单空格）后代组合器的更具体形式，因为它只选择第一级后代。因此，在以下 HTML 中，`<img>` 元素只是 `<span>` 元素的子元素。

```js
<div id="container">
  <div id="inner">
    <p>
      <span><img src="img/example.jpg" alt="" /></span>
    </p>
  </div>
</div>
```

## 相邻兄弟：E + F

所有由`F`匹配且*紧接着*跟在与`E`匹配的元素相同父级的元素。

### 举例

1.  `$('ul + p')`: 选择所有由 `<ul>`（无序列表）匹配的兄弟元素后立即跟随的元素`<p>`（段落）。

1.  `$('strong + em')`: 选择所有由 `<strong>` 匹配的兄弟元素后立即跟随的元素`<em>`。

### 描述

要考虑的一个重要点是`+`组合器和`~`组合器（下面介绍）只选择兄弟元素。 考虑以下 HTML：

```js
<div id="container">
  <ul>
    <li></li>
    <li></li>
  </ul>
  <p>
    <img/>
  </p>
</div>
```

`$('ul + p')`选择`<p>`，因为它紧接在`<ul>`后面，并且这两个元素共享相同的父级，即`<div id="container">`。

`$('ul + img')`不选择任何东西，因为（除其他原因外）`<ul>`在 DOM 树中比`<img>`高一个级别。

`$('li + img')`不选择任何内容，因为即使`<li>`和`<img>`在 DOM 树中处于同一级别，它们也不共享相同的父级。

## 一般兄弟：E ~ F

所有由`E`匹配的元素，其后跟一个由`E`匹配的元素，并且具有相同的父级。

### 例子

1.  `$('p ~ ul')`：选择所有由`<p>`匹配的元素，后跟由`<ul>`匹配的兄弟元素

1.  `$('code ~ code')`：选择所有由`<code>`匹配的元素，后跟由`<code>`匹配的兄弟元素

### 描述

要考虑的一个重要点是`+`组合器和`~`组合器只选择*兄弟元素*。 两者之间的显着区别在于它们各自的范围。 虽然`+`组合器仅达到*紧接着*的下一个兄弟元素，但`~`组合器将该范围扩展到*所有*后续兄弟元素。

考虑以下 HTML：

```js
<ul>
  <li class="first"></li>
  <li class="second"></li>
  <li class="third></li>
</ul>
<ul>
  <li class="fourth"></li>
  <li class="fifth"></li>
  <li class="sixth"></li>
</ul>
```

`$('li.first ~ li')`选择`<li class="second">`和`<li class="third">`。

`$('li.first + li')`选择`<li class="second">`。

## 多个元素：E,F,G

选择所有由选择器表达式`E, F`或`G`匹配的元素。

### 例子

1.  `$('code, em, strong')`：选择所有由`<code>`或`<em>`或`<strong>`匹配的元素

1.  `$('p strong, .myclass')`：选择所有由`<p>`匹配的元素的后代 `<strong>` 以及具有 `myclass` 类的所有元素

### 描述

这个逗号（,）组合器是选择不同元素的有效方式。 这个组合器的另一个选择是在第三章中描述的`.add()`方法。

## Nth Child（:nth-child(n)）

所有是其父级的第`n`个子元素的元素。

### 例子

1.  `$('li:nth-child(2)')`：选择所有由`<li>`匹配的元素，它们是其父级的第二个子元素

1.  `$('p:nth-child(5)')`：选择所有由`<p>`匹配的元素，它们是其父级的第五个子元素

### 描述

因为 jQuery 对 `:nth-child(n)` 的实现严格来自 CSS 规范，因此 `n` 的值是*基于 1 的*，这意味着计数从 1 开始。 但是，对于所有其他选择器表达式，jQuery 遵循 JavaScript 的“基于 0 的”计数。 因此，鉴于一个包含两个 `<li>` 的单个 `<ul>`，`$('li:nth-child(1)')`选择第一个 `<li>`，而`$('li:nth(1)')`选择第二个。

由于两者看起来非常相似，`:nth-child(n)` 伪类很容易与 `:nth(n)` 混淆，尽管正如我们刚刚看到的，它们可能会导致截然不同的匹配元素。 使用 `:nth-child(n)`，所有子元素都被计数，无论它们是什么，只有当它们与附加到伪类的选择器匹配时才会选择指定的元素。 使用 `:nth(n)` 仅计数附加到伪类的选择器，不限于任何其他元素的子元素，并选择第 n 个元素。 为了证明这一区别，让我们来看看以下 HTML 给出的几个选择器表达式的结果：

```js
<div>
  <h2></h2>
  <p></p>
  <h2></h2>
  <p></p>
  <p></p>
</div>
```

`$('p:nth(1)')` 选择第二个 `<p>`，因为 `:nth(n)` 的编号从 `0` 开始。

`$('p:nth-child(1)')` 选择不到任何内容，因为没有 `<p>` 元素是其父元素的第一个子元素。

`$('p:nth(2)')` 选择第三个 `<p>`。

`$('p:nth-child(2)')` 选择第一个 `<p>`，因为它是其父元素的第二个子元素。

除了接受整数外，`:nth-child(n)` 还可以接受 `even` 或 `odd`。 当文档中出现多个表格时，这使得它特别适用于表行条纹解决方案。 再次考虑上面的 HTML 片段：

`$('p:nth-child(even)')` 选择第一个和第三个 `<p>`，因为它们是其父元素的第二个和第四个子元素（都是偶数）。

## 第一个子元素 (:first-child)

所有是其父元素的第一个子元素的元素：

### 示例

1.  `$('li:first-child')`: 选择所有与 `<li>` 匹配的元素，它们是其父元素的第一个子元素。

1.  `$(strong:first-child')`: 选择所有与 `<strong>` 匹配的元素，它们是其父元素的第一个子元素。

### 描述

`:first-child` 伪类是 `:nth-child(1)` 的简写。 关于 `:X-child` 伪类的更多信息，请参阅 `:nth-child(n)` 的讨论。

## 最后一个子元素 (:last-child)

所有是其父元素的最后一个子元素的元素。

### 示例

1.  `$('li:last-child')`: 选择所有与 `<li>` 匹配的元素，它们是其父元素的最后一个子元素。

1.  `$('code:last-child')`: 选择所有与 `<code>` 匹配的元素，它们是其父元素的最后一个子元素。

### 描述

关于 `:X-child` 伪类的更多信息，请参阅 `:nth-child(n)` 的讨论。

## 只有一个子元素 :only-child

所有只有一个子元素的元素。

### 示例

1.  `$(':only-child')`: 选择所有只有一个子元素的元素。

1.  `$('code:only-child')`: 选择所有只有一个子元素的 `<code>` 元素。

## 不是 :not(s)

所有不匹配选择器 `s` 的元素。

### 示例

1.  `$('li:not(.myclass)')`: 选择所有与 `<li>` 匹配的元素，它们没有 `class="myclass"`。

1.  `$('li:not(:last-child)')`: 选择所有与 `<li>` 匹配的元素，它们不是其父元素的最后一个子元素。

## 空 :empty

所有没有子元素（包括文本节点）的元素。

### 示例

1.  `$(':empty')`: 选择所有没有子元素的元素。

1.  `$('p:empty')`: 选择所有与 `<p>` 匹配的元素，它们没有子元素。

### 描述

W3C 建议`<p>`元素至少有一个子节点，即使该子节点只是文本（请参阅[`www.w3.org/TR/html401/struct/text.html#edef-P`](http://www.w3.org/TR/html401/struct/text.html#edef-P)）。另一方面，一些其他元素是空的（即没有子元素）：例如`<input>, <img>, <br>`和`<hr>`。

注意使用`:empty`（和`:parent`）时的一件重要事情是*子元素包括文本节点*。

## 通用：*

所有元素。

### 例子

1.  `$('*')`：选择文档中的所有元素

1.  `$('p > *')`：选择所有作为段落元素的子元素的元素

### 描述

当与其他元素结合形成更具体的选择器表达式时，`*`选择器尤其有用。

# XPath 选择器

仿照文件系统的目录树导航，XPath 选择器表达式提供了一种替代方式来访问 DOM 元素。尽管 XPath 是为 XML 文档开发的选择器语言，但 jQuery 提供了一组基本的选择器，可用于 XML 和 HTML 文档。

有关 XPath 1.0 的更多信息，请访问 W3C 的规范：[`www.w3.org/TR/xpath`](http://www.w3.org/TR/xpath)。

## 后代：E//F

所有由`E`匹配的元素的后代，这些元素是由`F`匹配的元素的后代。

### 例子

1.  `$('div//code')`：选择所有由`<div>`匹配的元素的后代 `<code>`

1.  `$('//p//a')`：选择所有由`<p>`匹配的元素的后代 `<a>`

### 描述

此 XPath 后代选择器与相应的 CSS 后代选择器`($('E F'))`的工作方式相同，只是 XPath 版本可以指定从文档根开始，这在查询 XML 文档时可能很有用。

在示例 2 中，初始的`//p`告诉 jQuery 从文档根开始匹配所有`<p>`元素及其后代。请注意，如果此选择器表达式跟随 DOM 遍历方法（如`.find()`），则此语法将不会选择任何内容，因为文档根不能是任何其他元素的子元素。由于 jQuery 允许自由混合 CSS 和 XPath 选择器，因此初始的`//`是多余的，因此可以省略。

## 子元素：E/F

所有由`E`匹配的元素的子元素，这些元素是由`F`匹配的元素的子元素。

### 例子

1.  `$('div/p')`：选择所有由`<div>`匹配的元素的子元素 `<p>`

1.  `$('p/a')`：选择所有由`<p>`匹配的元素的子元素 `<a>`

1.  `$('/docroot/el')`：选择所有由`<docroot>`匹配的元素的子元素 `<el>`，只要`<docroot>`实际上位于文档根

### 描述

XPath 子选择器 `$('E/F')` 是 CSS 子选择器 `$('E > F')` 的替代方案。如果选择器表达式以单斜杠开头，例如示例 3 中的情况，则紧随斜杠之后的选择器必须位于文档根目录。在 HTML 文档中不推荐以单斜杠开头，因为它始终必须跟随 `body` 才能匹配页面上的任何元素。然而，在 XML 文档中，识别文档根中的特定元素或属性可能是有用的。

## 父元素：E/..

所有与 `E` 匹配的元素的父元素。

### 示例

1.  `$('.myclass/..')`: 选择所有具有类名为 `myclass` 的元素的父元素。

1.  `$('.myclass/../')`: 选择所有是具有类名为 `myclass` 的元素的父元素的子元素。换句话说，它选择所有具有类名为 `myclass` 的元素，以及它们的兄弟元素。

1.  `$('.myclass/../p')`: 选择所有匹配 `<p>` 的元素，这些元素是具有类名为 `myclass` 的元素的父元素的子元素。

### 描述

让我们看一些示例 HTML 来帮助理解这个选择器：

```js
<div>
  <p id="firstp"></p>
  <div id="subdiv"></div>
  <p id="secondp">
    <span class="myclass"></span>
  </p>
</div>
<div>
  <p></p>
</div>
```

`$('span.myclass/..')` 选择 `<p id="secondp">`，因为它是 `<span class="myclass">` 的父元素。

`$('#firstp/../')` 选择 `<p id="firstp">, <div id="subdiv">` 和 `<p id="secondp">`，因为选择器 (a) 以 `<p id="firstp">` 开头， (b) 在 DOM 树中向上遍历一级（到第一个顶级 `<div>` 元素），以及 (c) 选择该 `<div>` 的所有子元素。

`$('.myclass/../../p')` 选择 `<p id="firstp">` 和 `<p id="secondp">`，因为选择器 (a) 以 `<span class="myclass">` 开头， (b) 在 DOM 树中向上遍历两级（到第一个顶级 `<div>` 元素），以及 (c) 选择所有 `<p>` 元素，这些元素是该 `<div>` 的子元素。

## 包含: [F]

包含由 `F` 匹配的所有元素。

### 示例

1.  `$('div[p]')`: 选择所有匹配 `<div>` 的元素，这些元素包含匹配 `<p>` 的元素。

1.  `$('p[.myclass]')`: 选择所有匹配 `<p>` 的元素，其中包含类名为 `myclass` 的元素。

### 描述

这个选择器类似于后代选择器的反向（`E//F` 或 `E F`），它选择所有具有匹配 `F` 的后代元素，而不是所有由其他元素的后代匹配 `F` 的元素。

XPath 的 *包含* 选择器不应与 CSS 的 *属性* 选择器混淆，后者共享此语法。jQuery 也使用 XPath 风格的表达式来表示属性选择器，如下文 *属性选择器* 部分所述。

# 属性选择器

因为 jQuery 支持 CSS 和 XPath 样式的表达式，并且两者在使用方括号时冲突，jQuery 采用 XPath 符号来表示属性选择器，以 `@` 符号开头。

使用以下任何属性选择器时，我们应考虑具有多个、以空格分隔的值的属性。由于这些选择器将属性值视为单个字符串，因此，例如此选择器 `$('[a@rel=nofollow]')` 将选择 `<a rel="nofollow" href="example.html">Some text</a>`，但 *不会* 选择 `<a rel="nofollow self" href="example.html">Some text</a>`。

选择器表达式中的属性值可以写成裸字或用引号括起来。因此，以下变体同样正确：

+   裸字：`$('[a@rel=nofollow self]')`

+   单引号内部的双引号：`$('[a@rel="nofollow self"]')`

+   双引号内部的单引号：`$("[a@rel='nofollow self']")`

+   在单引号内部转义单引号：`$('[a@rel=\'nofollow self\']')`

+   在双引号内部转义双引号：`$("[a@rel=\"nofollow self\"]")`

我们选择的变体通常是风格或便利性的问题。

## 具有属性：[@foo]

所有具有 `foo` 属性的元素。

### 示例

1.  `$('a[@rel]')`：选择所有具有 `rel` 属性的 `<a>` 元素

1.  `$('p[@class]')`：选择所有具有 `class` 属性的 `<p>` 元素

### 描述

关于此属性选择器的更多信息，请参见上面的 *属性选择器* 介绍。

## 属性值等于：[@foo=bar]

具有值完全等于 `bar` 的 `foo` 属性的元素。

### 示例

1.  `$('a[@rel=nofollow]')`：选择所有具有 `rel` 值完全等于 `nofollow` 的 `<a>` 元素

1.  `$('input[@name=myname]')`：选择所有具有 `name` 值完全等于 `myname` 的 `<input>` 元素

### 描述

关于此属性选择器的更多信息，请参见上面的 *属性选择器* 介绍。

## 属性值不等于：[@foo!=bar]

所有不具有值完全等于 `bar` 的 `foo` 属性的元素。

### 示例

1.  `$('a[@rel!=nofollow]')`：选择所有没有 `rel` 属性值完全等于 `nofollow` 的 `<a>` 元素

1.  `$('input[@name!=myname]')`：选择所有不具有 `name` 值完全等于 `myname` 的 `<input>` 元素

### 描述

由于这些选择器将属性值视为单个字符串，因此 `$('[a@rel!=nofollow]')` 我们 *将* 选择 `<a rel="nofollow self" href="example.htm">Some text</a>`。

如果我们需要仅选择 `<a>` 元素，并且它们的 `rel` 属性中没有任何地方包含 `nofollow`，我们可以使用以下选择器表达式代替：`$('a:not([@rel*=nofollow])')`。

## 属性值开头：[@foo^=bar]

所有具有值 *以* 字符串 `bar` 开头的 `foo` 属性的元素。

### 示例

1.  `$('a[@rel^=no]')`：选择所有具有 `rel` 属性值以 `no` 开头的 `<a>` 元素

1.  `$('input[@name^=my]')`：选择所有具有 `name` 值以 `my` 开头的 `<input>` 元素

### 描述

由于这些选择器将属性值视为单个字符串，`$('[a@rel^=no]')` 将选择 `<a rel="nofollow self" href="example.htm">Some text</a>`，但不选择 `<a rel="self nofollow" href="example.htm">Some text</a>`。

## 属性值结尾: [@foo$=bar]

所有具有以字符串 `bar` 结尾的值的 `foo` 属性的元素。

### 示例

1.  `$('a[@href$=index.htm]')`：选择所有 `href` 值以 `index.htm` 结尾的 `<a>` 元素

1.  `$('a[@rel$=self]')`：选择所有 `class` 值以 `bar` 结尾的 `<p>` 元素

### 描述

由于这些选择器将属性值视为单个字符串，`$('[a@rel$=self]')` 将选择 `<a rel="nofollow self" href="example.htm">Some text</a>`，但不选择 `<a rel="self nofollow" href="example.htm">Some text</a>`。

## 属性值包含: [@foo*=bar]

所有具有包含子字符串 `bar` 的 `foo` 属性的元素。

### 示例

1.  `$('p[@class*=bar]')`：选择所有 `class` 值包含 `bar` 的 `<p>` 元素

1.  `$('a[@href*=example.com]')`：选择所有 `href` 值包含 `example.com` 的 `<a>` 元素

### 描述

这是 jQuery 属性选择器中最宽松的选择器。如果选择器的字符串出现在元素的属性值的任何位置，它都将选择该元素。因此，`$('p[@class*=my]')` 将选择 `<p class="yourclass myclass">Some text</p>, <p class="myclass yourclass">Some text</p>` 和 `<p class="thisismyclass">Some text</p>`。

# 表单选择器

以下选择器可用于访问各种状态下的表单元素。在使用除 `:input` 外的任何表单选择器时，建议同时提供标签名（例如，使用 `input:text` 而不是 `:text`）。

+   所有表单元素（`<input>`（所有类型），`<select>, <textarea>, <button>`）

+   所有文本字段（`<input type="text">`)

+   所有密码字段（`<input type="password">`)

+   所有单选按钮字段（`<input type="radio">`)

+   所有复选框字段（`<input type="checkbox">`)

+   所有提交输入和按钮元素（`<input type="submit">, <button>`）

+   所有图像输入（`<input type="image">）

+   所有重置按钮（`<input type="reset">`)

+   所有按钮元素和类型为 `button` 的输入元素（`<button>,<input type="button">`）

+   所有已启用的用户界面元素

+   所有已禁用的用户界面元素

+   所有已选中的用户界面元素—复选框和单选按钮

+   所有元素，包括 `<input type="hidden" />`，都处于隐藏状态

欲了解更多信息，请参阅下文的 *自定义选择器* 部分中关于 `:hidden` 的讨论。

# 自定义选择器

以下选择器被添加到 jQuery 库中，以满足 CSS 或基本 XPath 无法满足的常见 DOM 遍历需求。

## 偶数元素 (:even) 奇数元素 (:odd)

所有具有偶数索引的元素：

`:even`

所有具有奇数索引的元素：

`:odd`

### 示例

1.  `$('li:even')`: 选择所有 `<li>` 元素匹配的元素，其 `index` 值为偶数

1.  `$('tr:odd')`: 选择所有被 `<tr>` 匹配的元素，其 `index` 值为奇数

### 描述

因为自定义的 `:even` 和 `:odd` 伪类基于它们的 `index` 匹配元素，它们使用 JavaScript 的本机基于零的编号。

有些令人感到反直觉，因此，`:even` 选择第一、第三、第五（等等）个元素，而 `:odd` 选择第二、第四、第六（等等）个元素。

这条规则的唯一例外是 `:nth-child(n)` 选择器，它是基于一的。所以，`:nth-child(even)` 选择其父级的第二、第四、第六（等等）个子元素。还值得注意的是，在与 `:nth-child()` 一起使用 `even` 或 `odd` 时，没有冒号前缀。

## 第 N 个元素（:eq(n)，:nth(n)）

具有索引值等于 n 的元素。

### 举例

1.  `$('li:eq(2)')`: 选择第三个 `<li>` 元素

1.  `$('p:nth(1)')`: 选择第二个 `<p>` 元素

### 描述

由于 JavaScript 的 `index` 是基于零的，`:eq(0)` 和 `:nth(0)` 选择第一个匹配的元素，`:eq(1)` 和 `:nth(1)` 选择第二个，依此类推。

## 大于 :gt(n)

所有索引大于 N 的元素。

### 举例

1.  `$('li:gt(1)')`: 选择所有被 `<li>` 元素匹配的元素，第二个之后的

1.  `$('a:gt(2)')`: 选择所有被 `<a>` 匹配的元素，第三个之后的

### 描述

由于 JavaScript 的 `index` 是基于零的，`:gt(1)` 选择从第三个开始的所有匹配的元素，`:gt(2)` 选择从第四个开始的所有匹配的元素，依此类推。考虑以下 HTML：

```js
<ul>
  <li id="first">index 0</li>
  <li id="second">index 1</li>
  <li id="third">index 2</li>
  <li id="fourth">index 3</li>
</ul>
```

`$('li:gt(1)')` 选择 `<li id="third">` 和 `<li id="fourth">`，因为它们的 `indexes` 大于 `1`。

`$(li:gt(2)')` 选择 `<li id="fourth">`，因为它的 `index` 大于 `2`。

## 小于 : lt(n)

所有索引小于 `N` 的元素。

### 举例

1.  `$('li:lt(2)')`: 选择所有被 `<li>` 元素匹配的元素，第三个之前的；换句话说，前两个 `<li>` 元素

1.  `$('p:lt(3)')`: 选择所有被 `<p>` 元素匹配的元素，第四个之前的；换句话说，前三个 `<p>` 元素

### 描述

由于 JavaScript 的 `index` 是基于零的，`:lt(2)` 选择前两个匹配的元素，或者选择第三个之前的所有匹配的元素；`:lt(3)` 选择前三个匹配的元素，或者选择第四个之前的所有匹配的元素；依此类推。

## 第一个 :first

元素的第一个实例。

### 举例

1.  `$('li:first')`: 选择第一个 `<li>` 元素

1.  `$('a:first')`: 选择第一个 `<a>` 元素

### 讨论

`:first` 伪类是 `:eq(0)` 的简写。它也可以写为 `:lt(1)`。

## 最后 :last

元素的最后一个实例。

### 举例

1.  `$('li:last)`: 选择最后一个 `<li>` 元素

1.  `$('#container .myclass:last)`: 选择具有 `class` 为 `myclass` 的最后一个元素，并且是具有 `id` 为 `container` 的元素的后代

### 描述

虽然`:first`具有等效选择器（`nth（0）和 eq（0）`），但`:last`伪类在仅选择匹配元素集中的最后一个元素方面是独特的。

## 父元素：:parent

所有是另一个元素的父元素的元素，包括文本。

### 示例

1.  `$(':parent')`: 选择所有是另一个元素的父元素的元素，包括文本

1.  `$(td:parent')`: 选择所有是另一个元素的父元素的`<td>`匹配的元素，包括文本

### 描述

W3C 建议`<p>`元素至少有一个子节点，即使该子节点只是文本（参见[`www.w3.org/TR/html401/struct/text.html#edef P`](http://www.w3.org/TR/html401/struct/text.html#edef)）。例如，另一方面，某些元素是空的（即没有子元素）：`<input>, <img>, <br>`和`<hr>`。

使用`:parent`（和`:empty`），一个重要的要注意的是子元素包括文本节点。

## 包含：:contains(text)

所有包含指定文本的元素。

### 示例

1.  `$('p:contains(nothing special)')`: 选择所有包含文本`nothing special`的`<p>`匹配的元素

1.  `$('li:contains(second)')`: 选择所有包含文本`second`的`<li>`匹配的元素

### 描述

匹配的文本可以出现在选择器元素中或该元素的任何后裔中。因此，示例 1 仍将选择以下段落：

```js
<p>This paragraph is <span>nothing <strong>special</strong>
                                                          </span></p>
```

与属性值选择器一样，`:contains()`括号内的文本可以写为裸体词或用引号括起来。另外，要被选择，文本必须匹配大小写。

## 可见：:visible

所有可见的元素。

### 示例

1.  `$('li:visible')`: 选择所有匹配`<li>`的可见元素

1.  `$('input:visible')`: 选择所有匹配`<input>`的可见元素

### 讨论

`:visible`选择器包括具有`block`或`inline`（或任何其他值而不是`none`）的显示和`visible`的可见性的元素。排除具有`type="hidden"`的表单元素。

需要注意的是，即使元素的父元素（或其他祖先）的显示是`none`，只要它们自身的显示是`block`或`inline`（或任何其他值而不是`none`），它们将被`:visible`伪类选择。因此，元素可能被隐藏但仍然被`:visible`选择。

考虑以下 HTML：

```js
<div id="parent" style="display:none">
  <div id="child" style="display:block">
  </div>
</div>
```

尽管由于其父元素的显示属性，`<div id="child">`在网页上看不见，但它仍然被`$('div:visible')`选择。

## 隐藏：:hidden

所有隐藏的元素

### 示例

1.  `$('li:hidden)`: 选择所有匹配`<li>`的隐藏元素

1.  `$('input:hidden)`: 选择所有匹配`<input>`的隐藏元素

#### 描述

`:hidden`选择器包括具有`display:none`或`visibility:hidden`的 CSS 声明的元素，以及带有`type="hidden"`的表单元素。

如果一个元素之所以从视图中隐藏，是因为其父级（或其他祖先）元素具有 `none` 的显示或 `hidden` 的可见性，当其自身的 `display` 属性不是 `none` 且其 `visibility` 属性不是 `hidden` 时，它将不会被 `:hidden` 选择。

考虑以下 HTML：

```js
<div id="parent" style="display:none">
  <div id="child" style="display:block">
  </div>
</div>
```

尽管子级 `<div>` 在网页上不可见是因为其父级 `<div>` 的显示属性，`$('div:hidden')` 只选择 `<div id="parent">`。
