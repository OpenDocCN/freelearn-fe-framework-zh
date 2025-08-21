# 第八章：其他方法

> 选择的自由是你获得的
> 
> 选择的自由是你想要的
> 
> - Devo,
> 
> "选择的自由"

在前面的章节中，我们已经审查了许多类别的 jQuery 方法。但是到目前为止，该库提供的一些方法还不属于任何类别。在本章中，我们将探讨用于缩写常见 JavaScript 习语的方法。

# 设置方法

这些函数在主代码体开始之前很有用。

## $.browser

| 包含有关当前运行浏览器的信息。

```js
$.browser

```

|

### 参数

无。

### 返回值

每个用户代理可能的布尔标记。

### 描述

`$.browser`属性允许我们检测访问页面的是哪个 Web 浏览器，如浏览器本身所报告的。它包含每个最常见的浏览器类别——Internet Explorer，Mozilla，Safari 和 Opera 的标志。可以独立测试这些浏览器：

```js
$()
  .log('Safari: ' + $.browser.safari)
  .log('Opera: ' + $.browser.opera)
  .log('MSIE: ' + $.browser.msie)
  .log('Mozilla: ' + $.browser.mozilla);
```

在 Firefox 浏览器上执行时，结果是：

```js
Safari: false
Opera: false
MSIE: false
Mozilla: true

```

这个属性立即可用。因此可以用它来确定是否调用`$(document).ready()`是安全的。

因为`$.browser`使用`navigator.useragent`来确定平台，所以用户可能会伪装它。最好在可能的情况下完全避免特定于浏览器的代码。在需要为不同代理编写它的特殊情况下，最好的选择是测试你想要使用的 JavaScript 功能的存在。如果这不能很好地区分客户端，可以使用`$.browser`属性进行进一步的区分。

## $.noConflict()

| 放弃 jQuery 对`$`变量的控制。

```js
$.noConflict()
```

|

### 参数

无。

### 返回值

全局 jQuery 对象。这可以设置为一个变量，提供一个替代快捷键给`$`.

### 描述

许多 JavaScript 库使用`$`作为函数或变量名，就像 jQuery 一样。在 jQuery 的情况下，`$`只是`jQuery`的别名，所以所有功能都可以在不使用`$`的情况下实现。如果我们需要在 jQuery 旁边使用另一个 JavaScript 库，我们可以通过调用`$.noConflict()`来让`$`的控制权归还给其他库：

```js
// Import other library
// Import jQuery
$.noConflict();
// Code that uses other library’s $ can follow here.

```

这种技术特别适用于`.ready()`方法与`jQuery`对象的别名，因为在`.ready()`中，我们可以使用`$`而不必担心后续的冲突：

```js
// Import other library
// Import jQuery
$.noConflict();
jQuery(document).ready(function($) {
  // Code that uses jQuery’s $ can follow here.
});
// Code that uses other library’s $ can follow here.
```

# DOM 元素方法

这些方法帮助我们处理每个 jQuery 对象下的 DOM 元素。

## .length

| 返回 jQuery 对象匹配的 DOM 元素的数量。

```js
.length

```

|

### 参数

无。

### 返回值

匹配的元素数量。

### 描述

假设页面上有一个简单的无序列表：

```js
<ul>
  <li>foo</li>
  <li>bar</li>
</ul>
```

我们可以通过调用`.length`来确定列表项的数量：

```js
$().log('Length: ' + $('li’).length);

```

## .size()

| 返回 jQuery 对象匹配的 DOM 元素的数量。

```js
.size()
```

|

### 参数

无。

### 返回值

匹配的元素数量。

### 描述

假设页面上有一个简单的无序列表：

```js
<ul>
  <li>foo</li>
  <li>bar</li>
</ul>
```

我们可以通过调用`.size()`来确定列表项的数量：

```js
$().log('Size: ' + $('li’).size());

```

## .get()

| 检索与 jQuery 对象匹配的 DOM 元素。

```js
.get([index])
```

|

### 参数

+   索引（可选）：指示要检索的元素的整数

### 返回值

一个 DOM 元素，或者如果省略索引，则是一组 DOM 元素的数组。

### 描述

`.get()`方法允许我们访问每个 jQuery 对象下面的 DOM 节点。假设我们在页面上有一个简单的无序列表：

```js
<ul>
  <li>foo</li>
  <li>bar</li>
</ul>
```

指定了索引，`.get()`将检索单个元素：

```js
$().log('Get(0): ' + $('li’).get(0));

```

由于索引是从零开始的，因此返回第一个列表项：

```js
Get(0): [object HTMLLIElement]

```

每个 jQuery 对象也都伪装成数组，因此我们可以使用数组解引用运算符来获取列表项：

```js
$().log('Get(0): ' + $('li’)[0]);

```

没有参数时，`.get()`返回正常数组中的所有匹配的 DOM 节点：

```js
$().log('Get(): ' + $('li’).get());

```

在我们的示例中，这意味着返回了所有列表项：

```js
Get(): [object HTMLLIElement],[object HTMLLIElement]

```

## .index()

| 从匹配的元素中搜索给定的 DOM 节点。

```js
.index(node)

```

|

### 参数

+   节点：要查找的 DOM 元素

### 返回值

元素在 jQuery 对象中的位置，如果找不到则为`-1`。

### 描述

与`.get()`相对应的操作，它接受一个索引并返回一个 DOM 节点，`.index()`接受一个 DOM 节点并返回一个索引。假设我们在页面上有一个简单的无序列表：

```js
<ul>
  <li>foo</li>
  <li>bar</li>
</ul>
```

如果我们检索了两个列表项中的一个，我们可以将其存储在一个变量中。然后，`.index()`可以在匹配元素集中搜索此列表项：

```js
var listItem = $('li’)[1];
$().log('Index: ' + $('li’).index(listItem));

```

我们得到了列表项的从零开始的位置：

```js
Index: 1

```

# 集合操作

这些辅助函数操作数组，映射和字符串。

## .each()

| 在集合上进行迭代，在每个项目上触发回调函数。

```js
.each(callback)
$.each(collection, callback)

```

|

### 参数（第一版）

+   回调：要对每个匹配的元素执行的函数

### 返回值（第一版）

jQuery 对象，用于链接目的。

### 参数（第二版）

+   集合：要迭代的对象或数组

+   回调：要在集合中的每个项目上执行的函数

### 返回值（第二版）

集合。

### 描述

`.each()`方法和`$.each()`函数是设计用于创建简洁且不易出错的循环结构的通用迭代器。它们在集合上操作，并且对该集合中的每个项目执行一次回调函数。

上面列出的第一种语法是 jQuery 对象的一种方法，当调用它时，它会迭代对象的一部分 DOM 元素。每次回调运行时，都将当前循环迭代作为参数传递，从`0`开始。更重要的是，回调在当前 DOM 元素的上下文中触发，因此关键字`this`指的是该元素。

假设我们在页面上有一个简单的无序列表：

```js
<ul>
  <li>foo</li>
  <li>bar</li>
</ul>
```

我们可以选择列表项并对它们进行迭代：

```js
$('li’).each(function(index) {
  $(this).log(index + ': ' + $(this).text());
});
```

因此，对列表中的每个项目记录了一条消息：

```js
0: foo
1: bar

```

第二种语法类似，但它是一个全局函数，而不是一个方法。在这种情况下，集合作为第一个参数传递，并且可以是映射（JavaScript 对象）或数组。在数组的情况下，每次回调都会将数组索引和相应的数组值作为参数传递：

```js
$.each([52, 97], function(key, value) {
  $().log(key + ': ' + value);
});
```

这产生了两条消息：

```js
0: 52
1: 97
```

如果作为集合使用映射，每次回调都会把一个键值对传递为参数：

```js
$.each({'flammable’: 'inflammable’, 'duh’: 'no duh’}, function(index, value) {
  $().log(index + ': ' + value);
});
```

再次，这产生了两条消息：

```js
flammable: inflammable
duh: no duh

```

## $.grep()

| 筛选一个数组，只留下所选的项目。

```js
$.grep(array, filter[, invert])

```

|

### 参数

+   数组: 要搜索的数组

+   filter: 要对每个项目应用的测试函数，或包含要用作测试的表达式的字符串

+   invert（可选）：一个布尔值，指示是否倒转过滤条件

### 返回值

新构建的、经过过滤的数组。

### 描述

`$.grep()` 方法根据需要从数组中移除项，以便所有剩余项都通过提供的测试。 测试是一个函数，它以数组项和数组中的索引作为参数； 只有在测试返回 true 时，项才会在结果数组中。

与 jQuery 方法一样，回调函数通常是匿名定义的：

```js
var array = [0, 1, 52, 97];
$(this).log('Before: ' + array);
array = $.grep(array, function(a) {
  return (a > 50);
});
$(this).log('After: ' + array);
```

结果数组中保留了所有大于`50`的项目：

```js
Before: 0,1,52,97
After: 52,97

```

由于过滤函数往往非常简短，jQuery 提供了一个更进一步的快捷方式。 过滤函数可以定义为对数组中的每个项目`a`进行评估的单个表达式：

```js
var array = [0, 1, 52, 97];
$(this).log('Before: ' + array);
array = $.grep(array, 'a > 50’);
$(this).log('After: ' + array);

```

这产生了与以前相同的结果。 通过添加第三个参数，我们可以反转这个测试：

```js
var array = [0, 1, 52, 97];
$(this).log('Before: ' + array);
array = $.grep(array, 'a > 50’, true);
$(this).log('After: ' + array);

```

现在产生了一个小于或等于`50`的项目数组：

```js
Before: 0,1,52,97
After: 0,1

```

## $.map()

| 通过使用一个过滤函数将一个数组转换成另一个数组。

```js
$.map(array, filter)

```

|

### 参数

+   数组: 要转换的数组

+   过滤器: 要应用于每个项的函数，或包含要应用的表达式的字符串

### 返回值

新构建的转换数组。

### 描述

`$.map()` 方法对数组中的每个项目应用一个函数，将结果收集到一个新数组中。 过滤器是一个函数，它以数组项和数组中的索引作为参数。

与 jQuery 方法一样，回调函数通常是匿名定义的：

```js
var array = [0, 1, 52, 97];
$(this).log('Before: ' + array);
array = $.map(array, function(a) {
  return (a - 45);
});
$(this).log('After: ' + array);
```

结果数组中的所有项目都减去了`45`：

```js
Before: 0,1,52,97
After: -45,-44,7,52

```

由于过滤函数往往非常简短，jQuery 提供了一个更进一步的快捷方式。 过滤函数可以定义为应用于数组中每个项目`a`的单个表达式：

```js
var array = [0, 1, 52, 97];
$(this).log('Before: ' + array);
array = $.map(array, 'a - 45’);
$(this).log('After: ' + array);

```

这产生了与以前相同的结果。 通过从过滤函数中返回`null`，我们可以从数组中移除项目：

```js
var array = [0, 1, 52, 97];
$(this).log('Before: ' + array);
array = $.map(array, 'a > 50 ? a - 45 : null’);
$(this).log('After: ' + array);

```

现在产生了一个大于`50`的项目数组，每个都减去了`45`：

```js
Before: 0,1,52,97
After: 7,52

```

如果过滤函数返回的是一个数组而不是一个标量，则返回的数组将连接在一起形成结果：

```js
var array = [0, 1, 52, 97];
$(this).log('Before: ' + array);
array = $.map(array, function(a, i) {
  return [a - 45, i];
});
$(this).log('After: ' + array);
```

而不是二维结果数组，映射形成了一个扁平化的结果：

```js
Before: 0,1,52,97
After: -45,0,-44,1,7,2,52,3

```

## $.merge()

| 将两个数组的内容合并到第一个数组中。

```js
$.merge(array1, array2)
```

|

### 参数

+   array1: 要合并的第一个数组

+   array2: 要合并的第二个数组

### 返回值

由两个提供的数组元素组成的数组。

### 描述

`$.merge()` 操作形成一个包含两个数组中所有元素的数组，重复项已被移除。第一个数组中的项目顺序被保留，第二个数组中的项目被附加：

```js
var array1 = [0, 1, 52];
var array2 = [52, 97];
$(this).log('Array 1: ' + array1);
$(this).log('Array 2: ' + array2);
array = $.merge(array1, array2);
$(this).log('After: ' + array);

```

结果数组包含所有四个不同的项目：

```js
Array 1: 0,1,52
Array 2: 52,97
After: 0,1,52,97

```

`$.merge()` 函数是破坏性的。它修改第一个参数以添加第二个参数中的项目。如果需要原始的第一个数组，在调用 `$.merge()` 之前复制它。幸运的是，`$.merge()` 本身可以用于这种复制：

```js
var newArray = $.merge([], oldArray);

```

这个快捷方式创建一个新的空数组，并将 `oldArray` 的内容合并到其中，从而有效地克隆数组。

## $.unique()

| 创建一个删除重复项的对象数组的副本。

```js
$.unique(array)
```

|

### 参数

+   数组：对象数组

### 返回值

仅由唯一对象组成的数组。

### 描述

`$.unique()` 函数搜索对象数组，形成一个不包含重复对象的新数组。如果两个对象引用内存中的不同位置，则认为它们是不同的对象，即使它们的内容相同也是如此。原始数组不会被修改。数组可以包含任何类型的 JavaScript 对象：

```js
var alice = {'alice’: 'alice’};
var bob = {'bob’: 'bob’};
var carol = {'carol’: 'carol’};
var ted = {'bob’: 'bob’};
var oldArray = [alice, bob, carol, bob, ted];
$(this).log('Before: ' + oldArray);
newArray = $.unique(oldArray);
$(this).log('After: ' + newArray);

```

结果数组仅包含四个不同的项目：

```js
Before: {alice: alice}, {bob: bob}, {carol: carol},
  {bob: bob}, {bob: bob}
After: {alice: alice, mergeNum: 52}, {bob: bob, mergeNum: 52},
  {carol: carol, mergeNum: 52}, {bob: bob, mergeNum: 52}
```

结果数组中移除了第二个名为 `bob` 的对象实例。但是，名为 `ted` 的对象保留了下来，即使它具有相同的内容，因为它是作为单独的对象创建的。

注意，`$.unique()` 修改数组中的对象，为每个对象添加了一个名为 `mergeNum` 的额外属性。这个属性是函数实现的副作用，并且对调用代码没有用处。

## $.extend()

| 将两个对象的内容合并到第一个对象中。

```js
$.extend([target, ]properties[, ...])

```

|

### 参数

+   目标（可选）：将接收新属性的对象

+   属性：包含要合并的其他属性的对象

### 返回值

修改后的目标对象。

### 描述

`$.extend()` 函数以与 `$.merge()` 合并数组相同的方式合并两个对象。将第二个对象的属性添加到第一个对象中，创建一个具有两个对象所有属性的对象：

```js
var object1 = {
  apple: 0,
  banana: 52,
  cherry: 97
};
var object2 = {
  banana: 1,
  durian: 100
};

$().log(object1);
$().log(object2);
var object = $.extend(object1, object2);
$().log(object);
```

第二个对象中的 `durian` 的值被添加到第一个对象中，而 `banana` 的值被覆盖：

```js
{apple: 0, banana: 52, cherry: 97, }
{banana: 1, durian: 100, }
{apple: 0, banana: 1, cherry: 97, durian: 100, }

```

`$.extend()` 函数是破坏性的；目标对象在过程中被修改。这通常是期望的行为，因为 `$.extend()` 可以用来模拟对象继承。添加到对象的方法可供所有引用对象的代码使用。然而，如果我们想保留原始对象的副本，我们可以通过将空对象作为目标来实现：

```js
var object = $.extend({}, object1, object2)

```

我们还可以向 `$.extend()` 提供两个以上的对象。在这种情况下，所有对象的属性都将添加到目标对象中。

如果给`$.extend()`提供了一个参数，这意味着目标参数被省略了。在这种情况下，jQuery 对象本身被假定为目标。通过这样做，我们可以向 jQuery 命名空间添加新功能。在讨论如何创建 jQuery 插件时，我们将探讨这种能力。

`$.extend()`执行的合并不是递归的；如果第一个对象的属性本身是对象或数组，则它将被第二个对象中具有相同键的属性完全覆盖。值不会合并。

## $.trim()

| 从字符串的两端删除空白。

```js
$.trim()

```

|

### 参数

+   string：要修剪的字符串

### 返回值

修剪后的字符串。

### 描述

`$.trim()`函数从提供的字符串的开头和结尾删除所有换行符、空格和制表符：

```js
var string = "\tYes, no, I, this is. \n ";
$(this).log('Before: ' + string);
string = $.trim(string);
$(this).log('After: ' + string);

```

所有空白字符都被修剪：

```js
Before: 	Yes, no, I, this is. 

After: Yes, no, I, this is.
```
