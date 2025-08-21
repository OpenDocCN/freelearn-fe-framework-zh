# 第三章：DOM 遍历方法

> 因为有火车驶入车站
> 
> 但它正朝着新的目的地前进
> 
> —Devo，
> 
> “对我来说没有关系”

除了 第二章 中描述的选择器表达式之外，jQuery 还具有各种 DOM 遍历方法，帮助我们在文档中选择元素。这些方法提供了很大的灵活性，甚至允许我们在单个链中对多个元素集进行操作，如下所示：

```js
$('div.section > p').addClass('lit').lt(1).addClass('profound');

```

有时，在选择器表达式和相应的 DOM 遍历方法之间的选择仅仅是品味的问题，但毫无疑问，结合在一起的表达式和方法集合构成了一个极为强大的工具集，可以获取我们想要的任何内容。

截至 jQuery 1.1 版本，DOM 遍历方法不会修改它们发送的 jQuery 对象。相反，会构造一个新的 jQuery 对象，其中包含对原始对象的引用。可以使用 `.end` 方法检索原始对象。

# jQuery 工厂函数

以下函数支撑整个 jQuery 库，因为它允许我们创建所有其他方法附加到的 jQuery 对象。

# $()

| 在 DOM 中创建匹配元素的新 jQuery 对象。

```js
$(selector[, context])
$(element)
$(elementArray)
$(object)
$(html)

```

|

## 参数（第一个版本）

+   选择器：包含选择器表达式的字符串

+   上下文（可选）：要在其中搜索的 DOM 树的部分

## 参数（第二个版本）

+   元素：要包装在 jQuery 对象中的 DOM 元素

## 参数（第三个版本）

+   elementArray：包含要包装在 jQuery 对象中的一组 DOM 元素的数组

## 参数（第四个版本）

+   object：要克隆的现有 jQuery 对象

## 参数（第五个版本）

+   html：包含描述要创建的新 DOM 元素的 HTML 片段的字符串

## 返回值

新构造的 jQuery 对象。

## 描述

在上述列出的第一种公式中，`$()` 会在 DOM 中搜索与提供的选择器匹配的任何元素，并创建一个新的 jQuery 对象，该对象引用这些元素：

```js
$('div.foo');

```

在 第二章 中，我们探讨了可在此字符串中使用的选择器表达式的范围。

## 选择器上下文

默认情况下，选择器会从文档根开始在 DOM 中执行搜索。但是，可以通过使用 `$()` 函数的可选第二个参数为搜索提供替代上下文。例如，如果在回调函数中我们希望搜索元素，则可以限制该搜索：

```js
$('div.foo').click(function() {
  $('span', this).addClass('bar');
});
```

由于我们将 span 选择器限制为 `this` 的上下文，因此只有点击的元素内的 span 才会获得额外的类。

选择器上下文对于 XML 文档也很有用，因为它们不属于默认的 DOM 树的一部分。例如，如果 AJAX 调用返回了一个名为 data 的 XML 结构，则我们可以在该结构内执行搜索：

```js
$('//foo/bar', data)

```

在内部，选择器上下文是使用 `.find` 方法实现的，因此 `$(selector, context)` 等效于 `$(context).find(selector)`。

### 注意

虽然 jQuery API 只指定 DOM 元素、DOM 元素数组和 jQuery 对象作为有效的上下文，但在实践中，选择器和 HTML 片段也可以在这里使用。

## 包装 DOM 元素

此函数的第二和第三种表达形式使我们能够使用其他方式已经找到的 DOM 元素或元素来创建一个 jQuery 对象。此功能的常见用法是对作为关键字`this`传递给回调函数的元素执行 jQuery 方法：

```js
$('div.foo').click(function() {
  $(this).slideUp();
});
```

该示例导致元素在单击时使用滑动动画隐藏。在调用 jQuery 方法之前，必须将元素包装在 jQuery 对象中，因为处理程序将接收关键字`this`中的单击项目作为裸 DOM 元素。

## 克隆 jQuery 对象

当一个 jQuery 对象被传递给`$()`作为参数时，将创建一个引用相同 DOM 元素的新 jQuery 对象。然后可以修改初始对象而不影响新对象。

## 创建新元素

如果将字符串作为参数传递给`$()`，jQuery 会检查字符串是否看起来像 HTML。如果不是，则将字符串解释为选择器表达式，如上所述。但是如果该字符串看起来像 HTML 片段，jQuery 会尝试根据 HTML 描述创建新的 DOM 元素。然后将创建并返回一个引用这些元素的 jQuery 对象。我们可以对此对象执行任何通常的 jQuery 方法：

```js
$('<p>My <em>new</em> paragraph</p>').appendTo('body');

```

实际创建元素的工作由浏览器的**innerHTML**机制处理。具体来说，jQuery 创建一个新的`<div>`元素，并将元素的 innerHTML 属性设置为传入的 HTML 片段。这意味着为了确保跨平台兼容性，片段必须是格式良好的。始终应将可以包含其他元素的标签与闭合标签配对：

```js
$('<a></a>');

```

不能包含元素的标签应快速关闭：

```js
$('<img />');

```

# 过滤方法

这些方法会从由 jQuery 对象匹配的集合中移除元素。

## .filter()

| 将匹配选择器或通过函数测试的元素集减少到那些匹配的元素。

```js
.filter(selector)
.filter(function)

```

|

### 参数（第一版本）

+   选择器：包含要与元素匹配的选择器表达式的字符串

### 参数（第二版本）

+   函数：用作集合中每个元素的测试的函数

### 返回值

新的 jQuery 对象。

### 描述

给定表示一组 DOM 元素的 jQuery 对象，`.filter`方法会根据匹配元素的子集构造一个新的 jQuery 对象。针对每个元素测试提供的选择器；所有匹配选择器的元素都将包括在结果中。

考虑一个带有简单列表的页面：

```js
<ul>
  <li>list item 1</li>
  <li>list item 2</li>
  <li>list item 3</li>
  <li>list item 4</li>
  <li>list item 5</li>
  <li>list item 6</li>
</ul>
```

我们可以将此方法应用于列表项集：

```js
$('li').filter(':even')

```

此调用的结果是一个包装项`1, 3`和`5`的 jQuery 对象，因为它们匹配选择器（请记住，:even 和:odd 使用基于 0 的索引）。

### 使用过滤函数

此方法的第二种形式允许我们针对函数而不是选择器来过滤元素。假设我们有一个更复杂的 HTML 片段：

```js
<ul>
  <li><strong>list</strong> item 1 - one strong</li>
  <li><strong>list</strong> item <strong>2</strong> - two <span>strongs</span></li>
  <li>list item 3</li>
  <li>list item 4</li>
  <li>list item 5</li>
  <li>list item 6</li>
</ul>
```

我们可以选择列表项，然后根据它们的内容进行过滤：

```js
$('li').filter(function(index) {
  return $("strong", this).length == 1;
})
```

这个表达式的结果将只是第一个列表项，因为它只包含一个 `<strong>` 标签。在过滤函数中，`this`依次引用每个 DOM 元素。传递给函数的参数告诉我们该 DOM 元素在由 jQuery 对象匹配的集合中的索引。

我们还可以利用通过函数传递的`index`：

```js
$('li').filter(function(index) {
  return index % 3 == 2;
})
```

这个表达式的结果将是第三和第六个列表项，因为它使用模运算符（%）来选择每个索引值除以 3 余数为 2 的项。

## .not()

| 从匹配元素的集合中移除元素。

```js
.not(selector) 
.not(elements) 

```

|

### 参数（第一个版本）

+   selector: 包含要匹配元素的选择器表达式的字符串

### 参数（第二个版本）

+   elements: 一个或多个要从匹配集合中删除的 DOM 元素

### 返回值

新的 jQuery 对象。

### 描述

给定一个表示一组 DOM 元素的 jQuery 对象，`.not` 方法会从匹配元素的子集构造一个新的 jQuery 对象。提供的选择器会被测试以匹配每个元素；不匹配选择器的元素将包含在结果中。

考虑一个简单列表的页面：

```js
<ul>
  <li>list item 1</li>
  <li>list item 2</li>
  <li>list item 3</li>
  <li>list item 4</li>
  <li>list item 5</li>
</ul>
```

我们可以将此方法应用于列表项集合：

```js
$('li').not(':even')

```

这个调用的结果是一个包含项`2`和`4`的 jQuery 对象，因为它们不匹配选择器（请回忆一下，:even 和 :odd 使用的是基于 0 的索引）。

### 移除特定元素

`.not` 方法的第二个版本允许我们从匹配集合中移除元素，假设我们以其他方式找到了这些元素。例如，假设我们的列表中的一个项目有一个标识符：

```js
<ul>
  <li>list item 1</li>
  <li>list item 2</li>
  <li id="notli">list item 3</li>
  <li>list item 4</li>
  <li>list item 5</li>
</ul>
```

我们可以使用原生 JavaScript 的 `getElementById` 函数获取第三个列表项，然后从 jQuery 对象中移除它：

```js
$('li').not(document.getElementById('notli'))

```

此表达式产生一个匹配项 `1, 2, 4` 和 `5` 的 jQuery 对象。我们本可以用更简单的 jQuery 表达式来完成同样的事情，但是这种技术在其他库提供对普通 DOM 节点的引用时可能会有用。

## .contains()

| 将匹配元素集合减少到包含指定文本的元素。

```js
.contains(text)

```

|

### 参数

+   text: 要搜索的文本字符串

### 返回值

新的 jQuery 对象。

### 描述

给定一个表示一组 DOM 元素的 jQuery 对象，`.contains` 方法会从匹配元素的子集构造一个新的 jQuery 对象。提供的文本会在每个元素中进行搜索；包含文本的所有元素（即使在后代元素中）都将包含在结果中。

考虑一个简单列表的页面：

```js
<ul>
  <li>list item 1</li>
  <li>list <strong>item</strong> 2</li>
  <li>list item 3</li>
  <li>list item 4</li>
  <li>list item 5</li>
</ul>
```

我们可以将此方法应用于列表项集合：

```js
$('li').contains('item 2')

```

此调用的结果是包含指定文本的`item 2`的 jQuery 对象。使用 jQuery 的`.text`方法执行搜索，因此搜索文本可以位于匹配元素或其任何后代的文本字符串的连接中的任何位置。

## .eq()

| 将匹配元素的集合减少到指定索引处的一个元素。

```js
.eq(index)

```

|

### 参数

+   index：指示元素的*从 0 开始计数*的位置的整数

### 返回值

新的 jQuery 对象。

### 描述

给定表示一组 DOM 元素的 jQuery 对象，`.eq`方法从匹配元素中构造一个新的 jQuery 对象。提供的索引标识集合中此元素的位置。

考虑一个包含简单列表的页面：

```js
<ul>
  <li>list item 1</li>
  <li>list item 2</li>
  <li>list item 3</li>
  <li>list item 4</li>
  <li>list item 5</li>
</ul>
```

我们可以将此方法应用于列表项的集合：

```js
$('li').eq(2)

```

此调用的结果是包含`item 3`的 jQuery 对象。注意，提供的索引是*从 0 开始计数*的，并且是指 jQuery 对象内元素的位置，*而不是*DOM 树内的位置。

## .lt()

| 将匹配元素的集合减少到指定索引之前的元素。

```js
.lt(index)

```

|

### 参数

+   index：指示选择元素之前的*从 0 开始计数*的位置的整数

### 返回值

新的 jQuery 对象。

### 描述

给定表示一组 DOM 元素的 jQuery 对象，`.lt`方法从匹配元素的子集中构造一个新的 jQuery 对象。提供的`index`标识集合中一个元素的位置；此元素之前的所有元素都将包含在结果中。

考虑一个包含简单列表的页面：

```js
<ul>
  <li>list item 1</li>
  <li>list item 2</li>
  <li>list item 3</li>
  <li>list item 4</li>
  <li>list item 5</li>
</ul>
```

我们可以将此方法应用于列表项的集合：

```js
$('li').lt(2)

```

此调用的结果是包含项目`1`和`2`的 jQuery 对象。注意，提供的索引是*从 0 开始计数*的，并且是指 jQuery 对象内的元素位置，*而不是*DOM 树内的位置。

## .gt()

| 将匹配元素的集合减少到指定索引之后的元素。

```js
.gt(index)

```

|

### 参数

+   index：指示选择元素之后的*从 0 开始计数*的位置的整数

### 返回值

新的 jQuery 对象。

### 描述

给定表示一组 DOM 元素的 jQuery 对象，`.gt`方法从匹配元素的子集中构造一个新的 jQuery 对象。提供的`index`标识集合中一个元素的位置；此元素之后的所有元素都将包含在结果中。

考虑一个包含简单列表的页面：

```js
<ul>
  <li>list item 1</li>
  <li>list item 2</li>
  <li>list item 3</li>
  <li>list item 4</li>
  <li>list item 5</li>
</ul>
```

我们可以将此方法应用于列表项的集合：

```js
$('li').gt(2)

```

此调用的结果是包含项目 4 和 5 的 jQuery 对象。注意，提供的索引是*从 0 开始计数*的，并且是指 jQuery 对象内的元素位置，*而不是*DOM 树内的位置。

# 树遍历方法

这些方法利用 DOM 树的结构来定位一组新的元素。

## .find()

| 获取当前匹配的每个元素的后代，该后代经过选择器筛选。

```js
.find(selector)

```

|

### 参数

+   selector：包含要匹配元素的选择器表达式的字符串

### 返回值

新的 jQuery 对象。

### 描述

给定一个代表一组 DOM 元素的 jQuery 对象，`.find` 方法允许我们在 DOM 树的后代中搜索这些元素，并从匹配的元素构建一个新的 jQuery 对象。`.find` 和 `.children` 方法类似，不同之处在于后者只向下遍历 DOM 树的一级。

该方法接受与我们可以传递给 `$()` 函数的相同类型的选择器表达式。将通过测试它们是否与此选择器匹配来过滤元素。

考虑一个带有基本嵌套列表的页面：

```js
<ul class="level-1">
  <li class="item-i">I</li>
  <li class="item-ii">II
    <ul class="level-2">
      <li class="item-a">A</li>
      <li class="item-b">B
        <ul class="level-3">
          <li class="item-1">1</li>
          <li class="item-2">2</li>
          <li class="item-3">3</li>
        </ul>
      </li>
      <li class="item-c">C</li>
    </ul>
  </li>
  <li class="item-iii">III</li>
</ul>
```

如果我们从项目 II 开始，我们可以找到其中的列表项：

```js
$('li.item-ii').find('li')

```

这次调用的结果是一个包含项 `A, B, 1, 2, 3` 和 `C` 的 jQuery 对象。即使项目 `II` 匹配了选择器表达式，它也不会被包含在结果中；只有后代被视为匹配项的候选者。

如 *jQuery 工厂函数* 部分所讨论的，选择器上下文是使用 `.find` 方法实现的；因此，`$('li.item-ii').find('li')` 等效于 `$('li', 'li.item-ii')`。

### 注意

与其它树遍历方法不同，在调用 `.find()` 时需要选择器表达式。如果我们需要检索所有后代元素，可以传递选择器 * 来完成此操作。

## .children()

| 获取匹配元素集合中每个元素的子元素，可选择性地通过选择器进行过滤。

```js
.children([selector])

```

|

### 参数

+   选择器（可选）：包含一个选择器表达式以匹配元素的字符串

### 返回值

新的 jQuery 对象。

### 描述

给定一个代表一组 DOM 元素的 jQuery 对象，`.children` 方法允许我们在 DOM 树中搜索这些元素的直接子元素，并从匹配的元素构建一个新的 jQuery 对象。`.find` 和 `.children` 方法类似，不同之处在于后者只向下遍历 DOM 树的一级。

该方法可选择地接受与我们可以传递给 `$()` 函数的相同类型的选择器表达式。如果提供了选择器，则将通过测试它们是否与选择器匹配来过滤元素。

考虑一个带有基本嵌套列表的页面：

```js
<ul class="level-1">
  <li class="item-i">I</li>
  <li class="item-ii">II
    <ul class="level-2">
      <li class="item-a">A</li>
      <li class="item-b">B
        <ul class="level-3">
          <li class="item-1">1</li>
          <li class="item-2">2</li>
          <li class="item-3">3</li>
        </ul>
      </li>
      <li class="item-c">C</li>
    </ul>
  </li>
  <li class="item-iii">III</li>
</ul>
```

如果我们从第二级列表开始，我们可以找到它的子元素：

```js
$('ul.level-2').children()

```

这次调用的结果是一个包含项 `A, B` 和 `C` 的 jQuery 对象。由于我们没有提供选择器表达式，所有子元素都是对象的一部分。如果我们提供了一个，只有这三个中的匹配项将被包含在内。

## .parents()

| 获取匹配元素集合中每个元素的祖先元素，可选择性地通过选择器进行过滤。

```js
.parents([selector])

```

|

### 参数

+   选择器（可选）：包含一个选择器表达式以匹配元素的字符串

### 返回值

新的 jQuery 对象。

### 描述

给定表示一组 DOM 元素的 jQuery 对象，`.parents` 方法允许我们在 DOM 树中搜索这些元素的祖先，并从匹配的元素构造一个新的 jQuery 对象。`.parents()` 和 `.parent()` 方法是类似的，不同之处在于后者只在 DOM 树中向上移动一个级别。

方法可选地接受与我们可以传递给 `$()` 函数的相同类型的选择器表达式。如果提供了选择器，则将通过测试它们是否匹配选择器来过滤元素。

考虑一个上面有一个基本嵌套列表的页面：

```js
<ul class="level-1">
  <li class="item-i">I</li>
  <li class="item-ii">II
    <ul class="level-2">
      <li class="item-a">A</li>
      <li class="item-b">B
        <ul class="level-3">
          <li class="item-1">1</li>
          <li class="item-2">2</li>
          <li class="item-3">3</li>
        </ul>
      </li>
      <li class="item-c">C</li>
    </ul>
  </li>
  <li class="item-iii">III</li>
</ul>
```

如果我们从项目 `A` 开始，我们可以找到它的祖先：

```js
$('li.item-a').parents()

```

这个调用的结果是一个包装着 `level-2` 列表、`item ii` 和 `level-1` 列表的 jQuery 对象（一直向上直到 `<html>` 元素的 DOM 树）。因为我们没有提供选择器表达式，所有祖先都是对象的一部分。如果我们提供了一个，只有这些匹配的项会被包含在其中。

## .parent()

| 获取当前匹配的每个元素的父级，可以选择使用选择器进行过滤。

```js
.parent([selector])

```

|

### 参数

+   选择器（可选）：包含要将元素与之匹配的选择器表达式的字符串。

### 返回值

新的 jQuery 对象。

### 描述

给定表示一组 DOM 元素的 jQuery 对象，`.parent` 方法允许我们在 DOM 树中搜索这些元素的父级，并从匹配的元素构造一个新的 jQuery 对象。`.parents` 和 `.parent` 方法是类似的，不同之处在于后者只在 DOM 树中向上移动一个级别。

方法可选地接受与我们可以传递给 `$()` 函数的相同类型的选择器表达式。如果提供了选择器，则将通过测试它们是否匹配选择器来过滤元素。

考虑一个上面有一个基本嵌套列表的页面：

```js
<ul class="level-1">
  <li class="item-i">I</li>
  <li class="item-ii">II
    <ul class="level-2">
      <li class="item-a">A</li>
      <li class="item-b">B
        <ul class="level-3">
          <li class="item-1">1</li>
          <li class="item-2">2</li>
          <li class="item-3">3</li>
        </ul>
      </li>
      <li class="item-c">C</li>
    </ul>
  </li>
  <li class="item-iii">III</li>
</ul>
```

如果我们从项目 `A` 开始，我们可以找到它的父元素：

```js
$('li.item-a').parent()

```

这个调用的结果是一个包装着 `level-2` 列表的 jQuery 对象。因为我们没有提供选择器表达式，父元素明确包括在对象中。如果我们提供了一个，那么在包含之前会对元素进行匹配测试。

## .siblings()

| 获取匹配元素集合中每个元素的同级，可以选择使用选择器进行过滤。

```js
.siblings([selector])

```

|

### 参数

+   选择器（可选）：包含要将元素与之匹配的选择器表达式的字符串

### 返回值

新的 jQuery 对象。

### 描述

给定表示一组 DOM 元素的 jQuery 对象，`.siblings` 方法允许我们在 DOM 树中搜索这些元素的兄弟元素，并从匹配的元素构造一个新的 jQuery 对象。

方法可选地接受与我们可以传递给 `$()` 函数的相同类型的选择器表达式。如果提供了选择器，则将通过测试它们是否匹配选择器来过滤元素。

考虑一个上面有一个简单列表的页面：

```js
<ul>
   <li>list item 1</li>
   <li>list item 2</li>
   <li class="third-item">list item 3</li>
   <li>list item 4</li>
   <li>list item 5</li>
</ul>
```

如果我们从第三个项目开始，我们可以找到它的兄弟：

```js
$('li.third-item').siblings()

```

此调用的结果是一个包裹着`1, 2, 4`和`5`项的 jQuery 对象。因为我们没有提供选择器表达式，所以所有的兄弟元素都是对象的一部分。如果我们提供了一个选择器，那么这四个元素中的匹配项才会被包含。

在兄弟元素中不包括原始元素，这一点在我们希望找到 DOM 树特定级别的所有元素时很重要。

## .prev()

| 获取匹配元素集合中每个元素的紧邻前序兄弟元素，可选择性地通过选择器进行过滤。

```js
.prev([selector])

```

|

### 参数

+   selector（可选）: 包含用于匹配元素的选择器表达式的字符串

### 返回值

新的 jQuery 对象。

### 描述

给定一个代表一组 DOM 元素的 jQuery 对象，`.prev`方法允许我们在 DOM 树中搜索这些元素的前驱，并从匹配的元素构建一个新的 jQuery 对象。

此方法可选择性地接受与我们可以传递给`$()`函数的相同类型的选择器表达式。如果提供了选择器，则将通过测试它们是否与选择器匹配来对元素进行过滤。

考虑一个简单列表的页面：

```js
<ul>
   <li>list item 1</li>
   <li>list item 2</li>
   <li class="third-item">list item 3</li>
   <li>list item 4</li>
   <li>list item 5</li>
</ul>
```

如果我们从第三项开始，我们可以找到它之前的元素：

```js
$('li.third-item').prev()

```

此调用的结果是一个包裹着`item 2`的 jQuery 对象。因为我们没有提供选择器表达式，所以这个前序元素明确地被包含在对象中。如果我们提供了一个选择器，那么在包含之前会测试该元素是否匹配。

## .next()

| 获取匹配元素集合中每个元素的紧邻后续兄弟元素，可选择性地通过选择器进行过滤。

```js
.next([selector])

```

|

### 参数

+   selector（可选）: 包含用于匹配元素的选择器表达式的字符串

### 返回值

新的 jQuery 对象。

### 描述

给定一个代表一组 DOM 元素的 jQuery 对象，`.next`方法允许我们在 DOM 树中搜索这些元素的后继，并从匹配的元素构建一个新的 jQuery 对象。

此方法可选择性地接受与我们可以传递给`$()`函数的相同类型的选择器表达式。如果提供了选择器，则将通过测试它们是否与选择器匹配来对元素进行过滤。

考虑一个简单列表的页面：

```js
<ul>
   <li>list item 1</li>
   <li>list item 2</li>
   <li class="third-item">list item 3</li>
   <li>list item 4</li>
   <li>list item 5</li>
</ul>
```

如果我们从第三项开始，我们可以找到它之后的元素：

```js
$('li.third-item').next()

```

此调用的结果是一个包裹着`item 4`的 jQuery 对象。因为我们没有提供选择器表达式，所以这个后续元素明确地被包含在对象中。如果我们提供了一个选择器，那么在包含之前会测试该元素是否匹配。

# 杂项遍历方法

这些方法提供了在 jQuery 对象中操作匹配的 DOM 元素集合的其他机制。

## .add()

| 将元素添加到匹配元素集合中。

```js
.add(selector)
.add(elements)
.add(html)

```

|

### 参数（第一版本）

+   selector: 包含用于匹配额外元素的选择器表达式的字符串

### 参数（第二版本）

+   元素：要添加到匹配元素集的一个或多个元素

### 参数（第三个版本）

+   html：要添加到匹配元素集的 HTML 片段

### 返回值

新的 jQuery 对象。

### 描述

给定一个代表一组 DOM 元素的 jQuery 对象，`.add`方法从这些元素的并集和传递给该方法的元素构造一个新的 jQuery 对象。`.add`的参数几乎可以是`$()`接受的任何东西，包括一个 jQuery 选择器表达式、对 DOM 元素的引用或者一个 HTML 片段。

考虑一个页面，有一个简单的列表和一个随后的段落：

```js
<ul>
  <li>list item 1</li>
  <li>list item 2</li>
  <li>list item 3</li>
</ul>
<p>a paragraph</p>
```

我们可以通过使用选择器或对 DOM 元素本身的引用作为`.add`方法的参数来选择列表项，然后选择段落：

```js
$('li').add('p') or
$('li').add(document.getElementsByTagName('p')[0])

```

此调用的结果是一个包装了所有四个元素的 jQuery 对象。

使用 HTML 片段作为`.add`方法的参数（如第三个版本中所示），我们可以动态创建额外的元素，并将这些元素添加到匹配的元素集中。举个例子，假设我们想要将一个`foo`类添加到列表项、段落以及一个新创建的段落中：

```js
$('li').add('p').add('<p id="new">new paragraph</p>').addClass('foo')

```

尽管新的段落已经被创建并且其`foo`类已添加，但它仍然不会出现在页面上。为了将其放置在页面上，我们可以在链中添加一个插入方法。

关于插入方法的更多信息，请参阅第四章。

## .is()

| 检查当前匹配的元素集是否与选择器匹配，并在至少有一个元素匹配选择器时返回`true`。

```js
.is(selector)

```

|

### 参数

+   选择器：一个包含用于匹配元素的选择器表达式的字符串。

### 返回值

一个布尔值，指示元素是否与选择器匹配。

### 描述

与本章其他方法不同，`.is()`不会创建新的 jQuery 对象。相反，它允许我们在不修改 jQuery 对象的情况下测试其内容。这在回调函数中经常很有用，比如事件处理程序。

假设我们有一个列表，其中两个项目包含一个子元素：

```js
<ul>
  <li>list <strong>item 1</strong></li>
  <li><span>list item 2</span></li>
  <li>list item 3</li>
</ul>
```

我们可以将单击处理程序附加到`<ul>`元素上，然后限制代码只在单击列表项本身时触发，而不是其子元素之一时触发：

```js
$('ul').click(function(event) {
  if ($(event.target).is('li') ) {
    $(event.target).remove();
  }
});
```

现在，当用户在第一项中单击`list`这个词或者在第三项的任何位置单击时，被点击的列表项将会从文档中移除。然而，当用户在第一项中的`item 1`或者在第二项的任何位置单击时，不会发生任何事情，因为对于这些事件的目标分别是`<strong>`和`<span>`。

## .end()

| 结束当前链中最近的过滤操作，并将匹配的元素集返回到其先前的状态。

```js
.end()

```

|

### 参数

无。

### 返回值

前一个 jQuery 对象。

### 描述

本章中的大多数方法操作一个 jQuery 对象并产生一个新的对象，匹配不同的 DOM 元素集。当这种情况发生时，就好像一个新的元素集被推送到对象内部维护的堆栈上一样。每个连续的过滤方法都会将一个新的元素集推送到堆栈上。如果我们需要一个旧的元素集，可以使用 `.end()` 将集合从堆栈中弹出。

假设我们在页面上有几个简短的列表：

```js
<ul class="first">
   <li class="foo">list item 1</li>
   <li>list item 2</li>
   <li class="bar">list item 3</li>
</ul>
<ul class="second">
   <li class="foo">list item 1</li>
   <li>list item 2</li>
   <li class="bar">list item 3</li>
</ul>
```

`.end` 方法主要在利用 jQuery 的链接属性时很有用。当不使用链接时，通常可以通过变量名调用前一个对象，这样我们就不需要操作堆栈。但是，使用 `.end()`，我们可以将所有方法调用串在一起：

```js
$('ul.first').find('.foo').addClass('some-class').end() .find('.bar').addClass('another-class');

```

此链在第一个列表中搜索具有类 `foo` 的项目，并向其添加类 `some-class`。然后 `.end()` 将对象返回到调用 `.find()` 之前的状态，因此第二个 `.find()` 不仅在该列表的 `<li class="foo">` 中查找 `.bar`，而且在 `<ul class="first">` 中查找，然后将类 `another-class` 添加到匹配的元素上。结果是第一个列表的项目 `1` 和 `3` 添加了一个类，而第二个列表的项目没有添加任何类。

长长的 jQuery 链可以被视为一个结构化的代码块，其中过滤方法提供了嵌套块的开头，而 `.end` 方法则关闭它们：

```js
$('#example-traversing-end ul.first').find('.foo')
  .addClass('some-class')
    .end()
      .find('.bar')
        .addClass('another-class');
.end();
```

最后一个 `.end()` 是不必要的，因为我们随后立即丢弃了 jQuery 对象。但是，当代码以这种形式编写时，`.end()` 提供了视觉对称性和闭合性——至少在某些开发人员眼中，这样做可以使程序更易读。
