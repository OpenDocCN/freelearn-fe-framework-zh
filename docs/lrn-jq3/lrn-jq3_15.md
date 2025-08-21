# 附录 B：快速参考

本附录旨在快速参考 jQuery API，包括其选择器表达式和方法。每个方法和选择器的更详细讨论可在 jQuery 文档站点[`api.jquery.com`](http://api.jquery.com)上找到。

# 选择器表达式

jQuery 工厂函数`$()`用于查找页面上要处理的元素。此函数采用由类似 CSS 语法构成的字符串，称为选择器表达式。选择器表达式在第二章*选择元素*中有详细讨论。

# 简单 CSS

| **选择器** | **匹配** |
| --- | --- |
| `*` | 所有元素。 |
| `#id` | 具有给定 ID 的元素。 |
| `element` | 给定类型的所有元素。 |
| `.class` | 所有具有给定类的元素。 |
| `a, b` | 被`a`或`b`匹配的元素。 |
| `a b` | 是`a`后代的元素`b`。 |
| `a > b` | 是`a`的子元素`b`。 |
| `a + b` | 紧接着`a`的元素`b`。 |
| `a ~ b` | 是`a`兄弟且在`a`之后的元素`b`。 |

# 兄弟节点位置

| **选择器** | **匹配** |
| --- | --- |
| `:nth-child(index)` | 是其父元素的`index`子元素（基于 1）。 |
| `:nth-child(even)` | 是其父元素的偶数子元素（基于 1）。 |
| `:nth-child(odd)` | 元素是其父元素的奇数子元素（基于 1）。 |
| `:nth-child(formula)` | 是其父元素的第 n 个子元素（基于 1）。公式的形式为`an+b`，其中`a`和`b`为整数。 |
| `:nth-last-child()` | 与`:nth-child()`相同，但从最后一个元素向第一个元素计数。 |
| `:first-child` | 其父元素的第一个子元素。 |
| `:last-child` | 其父元素的最后一个子元素。 |
| `:only-child` | 其父元素的唯一子元素。 |
| `:nth-of-type()` | 与`:nth-child()`相同，但仅计算相同元素名称的元素。 |
| `:nth-last-of-type()` | 与`:nth-last-child()`相同，但仅计算相同元素名称的元素。 |
| `:first-of-type` | 是其兄弟中相同元素名称的第一个子元素。 |
| `:last-of-type` | 是其兄弟元素中相同元素名称的最后一个子元素。 |
| `:only-of-type()` | 是其兄弟中相同元素名称的唯一子元素。 |

# 匹配元素位置

| **选择器** | **匹配** |
| --- | --- |
| `:first` | 结果集中的第一个元素。 |
| `:last` | 结果集中的最后一个元素。 |
| `:not(a)` | 结果集中不与`a`匹配的所有元素。 |
| `:even` | 结果集中的偶数元素（基于 0）。 |
| `:odd` | 结果集中的奇数元素（基于 0）。 |
| `:eq(index)` | 结果集中的编号元素（基于 0）。 |
| `:gt(index)` | 结果集中给定索引（基于 0）之后的所有元素。 |
| `:lt(index)` | 在给定索引（基于 0）之前（小于）结果集中的所有元素。 |

# 属性

| **选择器** | **匹配** |
| --- | --- |
| `[attr]` | 具有`attr`属性的元素。 |
| `[attr="value"]` | `attr`属性为`value`的元素。 |
| `[attr!="value"]` | `attr`属性不是`value`的元素。 |
| `[attr^="value"]` | `attr`属性以`value`开头的元素。 |
| `[attr$="value"]` | `attr`属性以`value`结束的元素。 |
| `[attr*="value"]` | 包含子字符串`value`的`attr`属性的元素。 |
| `[attr~="value"]` | `attr`属性是一个以空格分隔的字符串集，其中之一是`value`的元素。 |
| `[attr&#124;="value"]` | 其`attr`属性等于`value`或以`value`连字符后跟的元素。 |

# 表单

| **选择器** | **匹配** |
| --- | --- |
| `:input` | 所有`<input>`、`<select>`、`<textarea>`和`<button>`元素。 |
| `:text` | `type="text"`的`<input>`元素。 |
| `:password` | `type="password"`的`<input>`元素。 |
| `:file` | `type="file"`的`<input>`元素。 |
| `:radio` | `type="radio"`的`<input>`元素。 |
| `:checkbox` | `type="checkbox"`的`<input>`元素。 |
| `:submit` | `type="submit"`的`<input>`元素。 |
| `:image` | `type="image"`的`<input>`元素。 |
| `:reset` | `type="reset"`的`<input>`元素。 |
| `:button` | `type="button"`的`<input>`元素和`<button>`元素。 |
| `:enabled` | 启用的表单元素。 |
| `:disabled` | 禁用的表单元素。 |
| `:checked` | 已选中的复选框和单选按钮。 |
| `:selected` | 已选中的`<option>`元素。 |

# 杂项选择器

| **选择器** | **匹配** |
| --- | --- |
| `:root` | 文档的根元素。 |
| `:header` | 标题元素（例如，`<h1>`、`<h2>`）。 |
| `:animated` | 正在进行动画的元素。 |
| `:contains(text)` | 包含给定文本的元素。 |
| `:empty` | 没有子节点的元素。 |
| `:has(a)` | 包含匹配`a`的后代元素。 |
| `:parent` | 具有子节点的元素。 |
| `:hidden` | 被隐藏的元素，无论是通过 CSS 还是因为它们是`<input type="hidden" />`。 |
| `:visible` | `:hidden`的反义。 |
| `:focus` | 具有键盘焦点的元素。 |
| `:lang(language)` | 具有给定语言代码的元素（可能是由元素或祖先上的`lang`属性或`<meta>`声明引起的）。 |
| `:target` | 如果有，URI 片段标识符指定的元素。 |

# DOM 遍历方法

使用`$()`创建 jQuery 对象后，我们可以通过调用其中一个 DOM 遍历方法来修改我们正在处理的匹配元素集。DOM 遍历方法在第二章 *选择元素*中有详细讨论。

# 过滤

| **遍历方法** | **返回一个包含...的 jQuery 对象** |
| --- | --- |
| `.filter(selector)` | 匹配给定选择器的选定元素。 |
| `.filter(callback)` | 回调函数返回 `true` 的选定元素。 |
| `.eq(index)` | 给定基于 0 的索引处的选定元素。 |
| `.first()` | 第一个选定元素。 |
| `.last()` | 最后一个选定元素。 |
| `.slice(start, [end])` | 在给定的以 0 为基础的索引范围内选择元素。 |
| `.not(selector)` | 不匹配给定选择器的选定元素。 |
| `.has(selector)` | 具有与 `selector` 匹配的后代元素的选定元素。 |

# 后代

| **遍历方法** | **返回一个包含...的 jQuery 对象** |
| --- | --- |
| `.find(selector)` | 与选择器匹配的后代元素。 |
| `.contents()` | 子节点（包括文本节点）。 |
| `.children([selector])` | 子节点，可选择由选择器进行过滤。 |

# 兄弟

| **遍历方法** | **返回一个包含...的 jQuery 对象** |
| --- | --- |
| `.next([selector])` | 每个选定元素后面紧邻的兄弟元素，可选择由选择器进行过滤。 |
| `.nextAll([selector])` | 每个选定元素后面的所有兄弟元素，可选择由选择器进行过滤。 |
| `.nextUntil([selector], [filter])` | 每个选定元素后面的所有兄弟元素，直到但不包括第一个匹配 `selector` 的元素，可选择由附加选择器进行过滤。 |
| `.prev([selector])` | 每个选定元素前面紧邻的兄弟元素，可选择由选择器进行过滤。 |
| `.prevAll([selector])` | 每个选定元素之前的所有兄弟元素，可选择由选择器进行过滤。 |
| `.prevUntil([selector], [filter])` | 每个选定元素前面的所有兄弟元素，直到但不包括第一个匹配 `selector` 的元素，可选择由附加选择器进行过滤。 |
| `.siblings([selector])` | 所有兄弟元素，可选择由选择器进行过滤。 |

# 祖先

| **遍历方法** | **返回一个包含...的 jQuery 对象** |
| --- | --- |
| `.parent([selector])` | 每个选定元素的父元素，可选择由选择器进行过滤。 |
| `.parents([selector])` | 所有祖先元素，可选择由选择器进行过滤。 |
| `.parentsUntil([selector], [filter])` | 每个选定元素的所有祖先元素，直到但不包括第一个匹配 `selector` 的元素，可选择由附加选择器进行过滤。 |
| `.closest(selector)` | 从选定元素开始，并在 DOM 树中沿着其祖先移动，找到与选择器匹配的第一个元素。 |
| `.offsetParent()` | 第一个选定元素的定位父元素，可以是相对定位或绝对定位。 |

# 集合操作

| **遍历方法** | **返回一个包含...的 jQuery 对象** |
| --- | --- |
| `.add(selector)` | 选定的元素，加上与给定选择器匹配的任何其他元素。 |
| `.addBack()` | 选定的元素，加上内部 jQuery 堆栈上先前选择的一组元素。 |
| `.end()` | 内部 jQuery 堆栈上先前选择的一组元素。 |
| `.map(callback)` | 在每个选定元素上调用回调函数的结果。 |
| `.pushStack(elements)` | 指定的元素。 |

# 处理选定元素

| **穿越方法** | **描述** |
| --- | --- |
| `.is(selector)` | 确定任何匹配元素是否被给定的选择器表达式匹配。 |
| `.index()` | 获取匹配元素相对于其兄弟元素的索引。 |
| `.index(element)` | 获取给定 DOM 节点在匹配元素集合中的索引。 |
| `$.contains(a, b)` | 确定 DOM 节点`b`是否包含 DOM 节点`a`。 |
| `.each(callback)` | 遍历匹配的元素，为每个元素执行`callback`。 |
| `.length` | 获取匹配元素的数量。 |
| `.get()` | 获取与匹配元素对应的 DOM 节点数组。 |
| `.get(index)` | 获取给定索引处匹配元素对应的 DOM 节点。 |
| `.toArray()` | 获取与匹配元素对应的 DOM 节点数组。 |

# 事件方法

为了对用户行为做出反应，我们需要使用这些事件方法注册我们的处理程序。请注意，许多 DOM 事件仅适用于特定的元素类型；这些细微之处在此未涉及。事件方法在第三章中详细讨论，*处理事件*。

# 绑定

| **事件方法** | **描述** |
| --- | --- |
| `.ready(handler)` | 绑定`handler`以在 DOM 和 CSS 完全加载时调用。 |
| `.on(type, [selector], [data], handler)` | 绑定`handler`以在给定类型的事件发送到元素时调用。如果提供了`selector`，则执行事件委托。 |
| `.on(events, [selector], [data])` | 根据`events`对象参数中指定的多个事件为事件绑定多个处理程序。 |
| `.off(type, [selector], [handler])` | 删除元素上的绑定。 |
| `.one(type, [data], handler)` | 绑定`handler`以在给定类型的事件发送到元素时调用。在处理程序被调用时删除绑定。 |

# 缩略绑定

| **事件方法** | **描述** |
| --- | --- |
| `.blur(handler)` | 绑定`handler`以在元素失去键盘焦点时调用。 |
| `.change(handler)` | 绑定`handler`以在元素的值更改时调用。 |
| `.click(handler)` | 绑定`handler`以在单击元素时调用。 |
| `.dblclick(handler)` | 绑定`handler`以在元素被双击时调用。 |
| `.focus(handler)` | 绑定`handler`以在元素获得键盘焦点时调用。 |
| `.focusin(handler)` | 绑定`handler`以在元素或后代获得键盘焦点时调用。 |
| `.focusout(handler)` | 绑定`handler`以在元素或后代失去键盘焦点时调用。 |
| `.keydown(handler)` | 绑定`handler`以在按键按下且元素具有键盘焦点时调用。 |
| `.keypress(handler)` | 绑定`handler`以在发生按键事件且元素具有键盘焦点时调用。 |
| `.keyup(handler)` | 当释放按键且元素具有键盘焦点时调用`handler`。 |
| `.mousedown(handler)` | 当鼠标按钮在元素内按下时调用`handler`。 |
| `.mouseenter(handler)` | 当鼠标指针进入元素时调用`handler`。不受事件冒泡影响。 |
| `.mouseleave(handler)` | 当鼠标指针离开元素时调用`handler`。不受事件冒泡影响。 |
| `.mousemove(handler)` | 当鼠标指针在元素内移动时调用`handler`。 |
| `.mouseout(handler)` | 当鼠标指针离开元素时调用`handler`。 |
| `.mouseover(handler)` | 当鼠标指针进入元素时调用`handler`。 |
| `.mouseup(handler)` | 当鼠标按钮在元素内释放时调用`handler`。 |
| `.resize(handler)` | 当元素大小改变时调用`handler`。 |
| `.scroll(handler)` | 当元素的滚动位置发生变化时调用`handler`。 |
| `.select(handler)` | 当元素中的文本被选择时绑定`handler`。 |
| `.submit(handler)` | 当表单元素提交时调用`handler`。 |
| `.hover(enter, leave)` | 当鼠标进入元素时绑定`enter`，当鼠标离开时绑定`leave`。 |

# 触发

| **事件方法** | **描述** |
| --- | --- |
| `.trigger(type, [data])` | 在元素上触发事件的处理程序，并执行事件的默认操作。 |
| `.triggerHandler(type, [data])` | 在元素上触发事件的处理程序，而不执行任何默认操作。 |

# 简写触发

| **事件方法** | **描述** |
| --- | --- |
| `.blur()` | 触发`blur`事件。 |
| `.change()` | 触发`change`事件。 |
| `.click()` | 触发`click`事件。 |
| `.dblclick()` | 触发`dblclick`事件。 |
| `.error()` | 触发`error`事件。 |
| `.focus()` | 触发`focus`事件。 |
| `.keydown()` | 触发`keydown`事件。 |
| `.keypress()` | 触发`keypress`事件。 |
| `.keyup()` | 触发`keyup`事件。 |
| `.select()` | 触发`select`事件。 |
| `.submit()` | 触发`submit`事件。 |

# 实用程序

| **事件方法** | **描述** |
| --- | --- |
| `$.proxy(fn, context)` | 创建一个以给定上下文执行的新函数。 |

# 效果方法

这些效果方法可用于对 DOM 元素执行动画。有关详细信息，请参阅第四章 *样式和动画*。

# 预定义效果

| **效果方法** | **描述** |
| --- | --- |
| `.show()` | 显示匹配的元素。 |
| `.hide()` | 隐藏匹配的元素。 |
| `.show(speed, [callback])` | 通过动画`height`、`width`和`opacity`显示匹配的元素。 |
| `.hide(speed, [callback])` | 通过动画`height`，`width`和`opacity`隐藏匹配元素。 |
| `.slideDown([speed], [callback])` | 通过滑动动作显示匹配元素。 |
| `.slideUp([speed], [callback])` | 通过滑动动作隐藏匹配元素。 |
| `.slideToggle([speed], [callback])` | 显示或隐藏匹配元素并带有滑动动作。 |
| `.fadeIn([speed], [callback])` | 通过使元素淡入到不透明来显示匹配元素。 |
| `.fadeOut([speed], [callback])` | 通过使元素淡出到透明来隐藏匹配元素。 |
| `.fadeToggle([speed], [callback])` | 显示或隐藏匹配元素并带有幻灯片动画。 |
| `.fadeTo(speed, opacity, [callback])` | 调整匹配元素的不透明度。 |

# 自定义动画

| **效果方法** | **描述** |
| --- | --- |
| `.animate(properties, [speed], [easing], [callback])` | 执行指定 CSS 属性的自定义动画。 |
| `.animate(properties, options)` | 一个更低层次的`.animate()`接口，允许控制动画队列。 |

# 队列操作

| **效果方法** | **描述** |
| --- | --- |
| `.queue([queueName])` | 检索第一个匹配元素上的函数队列。 |
| `.queue([queueName], callback)` | 将`callback`添加到队列的末尾。 |
| `.queue([queueName], newQueue)` | 用新队列替换当前队列。 |
| `.dequeue([queueName])` | 在队列上执行下一个函数。 |
| `.clearQueue([queueName])` | 清空所有待处理函数的队列。 |
| `.stop([clearQueue], [jumpToEnd])` | 停止当前运行的动画，然后启动排队的动画（如果有的话）。 |
| `.finish([queueName])` | 停止当前运行的动画，立即将所有排队的动画推进到它们的目标值。 |
| `.delay(duration, [queueName])` | 在执行队列中的下一项之前等待`duration`毫秒。 |
| `.promise([queueName], [target])` | 返回一个 Promise 对象，一旦集合上的所有排队动作完成，就会被解析。 |

# DOM 操作方法

DOM 操作方法在第五章中详细讨论，*操作 DOM*。

# 属性和属性

| **操作方法** | **描述** |
| --- | --- |
| `.attr(key)` | 获取名为`key`的属性。 |
| `.attr(key, value)` | 将名为`key`的属性设置为`value`。 |
| `.attr(key, fn)` | 将名为`key`的属性设置为`fn`的结果（分别在每个匹配元素上调用）。 |
| `.attr(obj)` | 设置以键值对形式给出的属性值。 |
| `.removeAttr(key)` | 删除名为`key`的属性。 |
| `.prop(key)` | 获取名为`key`的属性。 |
| `.prop(key, value)` | 设置名为`key`的属性为`value`。 |
| `.prop(key, fn)` | 将名为`key`的属性设置为`fn`的结果（分别在每个匹配元素上调用）。 |
| `.prop(obj)` | 设置以键值对形式给出的属性值。 |
| `.removeProp(key)` | 移除名为`key`的属性。 |
| `.addClass(class)` | 将给定的类添加到每个匹配元素中。 |
| `.removeClass(class)` | 从每个匹配元素中删除给定的类。 |
| `.toggleClass(class)` | 如果存在，则从每个匹配元素中删除给定的类，并在不存在时添加。 |
| `.hasClass(class)` | 如果任何匹配的元素具有给定的类，则返回`true`。 |
| `.val()` | 获取第一个匹配元素的值属性。 |
| `.val(value)` | 将每个元素的值属性设置为`value`。 |

# 内容

| **操作方法** | **描述** |
| --- | --- |
| `.html()` | 获取第一个匹配元素的 HTML 内容。 |
| `.html(value)` | 将每个匹配元素的 HTML 内容设置为 value。 |
| `.text()` | 将所有匹配元素的文本内容作为单个字符串获取。 |
| `.text(value)` | 将每个匹配元素的文本内容设置为`value`。 |

# CSS

| **操作方法** | **描述** |
| --- | --- |
| `.css(key)` | 获取名为`key`的 CSS 属性。 |
| `.css(key, value)` | 将名为`key`的 CSS 属性设置为`value`。 |
| `.css(obj)` | 设置以键值对给出的 CSS 属性值。 |

# 尺寸

| **操作方法** | **描述** |
| --- | --- |
| `.offset()` | 获取第一个匹配元素相对于视口的顶部和左侧像素坐标。 |
| `.position()` | 获取第一个匹配元素相对于`.offsetParent()`返回的元素的顶部和左侧像素坐标。 |
| `.scrollTop()` | 获取第一个匹配元素的垂直滚动位置。 |
| `.scrollTop(value)` | 将所有匹配元素的垂直滚动位置设置为`value`。 |
| `.scrollLeft()` | 获取第一个匹配元素的水平滚动位置。 |
| `.scrollLeft(value)` | 将所有匹配元素的水平滚动位置设置为`value`。 |
| `.height()` | 获取第一个匹配元素的高度。 |
| `.height(value)` | 将所有匹配元素的高度设置为`value`。 |
| `.width()` | 获取第一个匹配元素的宽度。 |
| `.width(value)` | 将所有匹配元素的宽度设置为`value`。 |
| `.innerHeight()` | 获取第一个匹配元素的高度，包括填充，但不包括边框。 |
| `.innerWidth()` | 获取第一个匹配元素的宽度，包括填充，但不包括边框。 |
| `.outerHeight(includeMargin)` | 获取第一个匹配元素的高度，包括填充，边框和可选的外边距。 |
| `.outerWidth(includeMargin)` | 获取第一个匹配元素的宽度，包括填充，边框和可选的外边距。 |

# 插入

| **操作方法** | **描述** |
| --- | --- |
| `.append(content)` | 将`content`插入到每个匹配元素的内部末尾。 |
| `.appendTo(selector)` | 将匹配的元素插入到由`selector`匹配的元素内部的末尾。 |
| `.prepend(content)` | 将`content`插入到每个匹配元素的内部开头。 |
| `.prependTo(selector)` | 将匹配的元素插入到由`selector`匹配的元素的内部开头。 |
| `.after(content)` | 在每个匹配的元素之后插入`content`。 |
| `.insertAfter(selector)` | 在每个由`selector`匹配的元素之后插入匹配的元素。 |
| `.before(content)` | 在每个匹配的元素之前插入`content`。 |
| `.insertBefore(selector)` | 将匹配的元素插入到由`selector`匹配的每个元素之前。 |
| `.wrap(content)` | 将每个匹配的元素包裹在`content`中。 |
| `.wrapAll(content)` | 将所有匹配的元素作为单个单元包裹在`content`中。 |
| `.wrapInner(content)` | 将每个匹配元素的内部内容包裹在`content`中。 |

# 替换

| **操作方法** | **描述** |
| --- | --- |
| `.replaceWith(content)` | 用`content`替换匹配的元素。 |
| `.replaceAll(selector)` | 用匹配的元素替换由`selector`匹配的元素。 |

# 移除

| **操作方法** | **描述** |
| --- | --- |
| `.empty()` | 移除每个匹配元素的子节点。 |
| `.remove([selector])` | 从 DOM 中删除匹配的节点（可选地通过`selector`过滤）。 |
| `.detach([selector])` | 从 DOM 中删除匹配的节点（可选地通过`selector`过滤），保留附加到它们的 jQuery 数据。 |
| `.unwrap()` | 删除元素的父元素。 |

# 复制

| **操作方法** | **描述** |
| --- | --- |
| `.clone([withHandlers], [deepWithHandlers])` | 复制所有匹配的元素，可选择地也复制事件处理程序。 |

# 数据

| **操作方法** | **描述** |
| --- | --- |
| `.data(key)` | 获取第一个匹配元素关联的名为`key`的数据项。 |
| `.data(key, value)` | 将名为`key`的数据项与每个匹配的元素关联到`value`。 |
| `.removeData(key)` | 删除与每个匹配元素关联的名为`key`的数据项。 |

# Ajax 方法

通过调用其中一个 Ajax 方法，我们可以在不需要页面刷新的情况下从服务器检索信息。Ajax 方法在第六章中详细讨论，*使用 Ajax 发送数据*。

# 发出请求

| **Ajax 方法** | **描述** |
| --- | --- |
| `$.ajax([url], options)` | 使用提供的选项集进行 Ajax 请求。这是一个低级方法，通常通过其他便利方法调用。 |
| `.load(url, [data], [callback])` | 发送 Ajax 请求到`url`，并将响应放入匹配的元素中。 |
| `$.get(url, [data], [callback], [returnType])` | 使用`GET`方法向`url`发出 Ajax 请求。 |
| `$.getJSON(url, [data], [callback])` | 发送 Ajax 请求到`url`，将响应解释为 JSON 数据结构。 |
| `$.getScript(url, [callback])` | 发送 Ajax 请求到`url`，执行响应作为 JavaScript。 |
| `$.post(url, [data], [callback], [returnType])` | 使用`POST`方法向`url`发出 Ajax 请求。 |

# 请求监控

| **Ajax 方法** | **描述** |
| --- | --- |
| `.ajaxComplete(handler)` | 将`handler`绑定到在任何 Ajax 事务完成时调用。 |
| `.ajaxError(handler)` | 将`handler`绑定到任何 Ajax 事务以错误完成时调用。 |
| `.ajaxSend(handler)` | 将`handler`绑定到在任何 Ajax 事务开始时调用。 |
| `.ajaxStart(handler)` | 将`handler`绑定到在任何 Ajax 事务开始且没有其他事务活动时调用。 |
| `.ajaxStop(handler)` | 将`handler`绑定到在任何 Ajax 事务结束且没有其他事务仍在进行时调用。 |
| `.ajaxSuccess(handler)` | 将`handler`绑定到在任何 Ajax 事务成功完成时调用。 |

# 配置

| **Ajax 方法** | **描述** |
| --- | --- |
| `$.ajaxSetup(options)` | 为所有后续 Ajax 事务设置默认选项。 |
| `$.ajaxPrefilter([dataTypes], handler)` | 在`$.ajax()`处理之前修改每个 Ajax 请求的选项。 |
| `$.ajaxTransport(transportFunction)` | 定义 Ajax 事务的新传输机制。 |

# 实用工具

| **Ajax 方法** | **描述** |
| --- | --- |
| `.serialize()` | 将一组表单控件的值编码为查询字符串。 |
| `.serializeArray()` | 将一组表单控件的值编码为 JavaScript 数据结构。 |
| `$.param(obj)` | 将键值对的任意对象编码为查询字符串。 |
| `$.globalEval(code)` | 在全局上下文中评估给定的 JavaScript 字符串。 |
| `$.parseJSON(json)` | 将给定的 JSON 字符串转换为 JavaScript 对象。 |
| `$.parseXML(xml)` | 将给定的 XML 字符串转换为 XML 文档。 |
| `$.parseHTML(html)` | 将给定的 HTML 字符串转换为一组 DOM 元素。 |

# 延迟对象

延迟对象及其承诺使我们能够以方便的语法对长时间运行的任务的完成做出反应。它们在第十一章，*高级效果*中详细讨论。

# 对象创建

| **函数** | **描述** |
| --- | --- |
| `$.Deferred([setupFunction])` | 返回一个新的延迟对象。 |
| `$.when(deferreds)` | 返回一个承诺对象，以便在给定的延迟对象解决时解决。 |

# Deferred 对象的方法

| **方法** | **描述** |
| --- | --- |
| `.resolve([args])` | 将对象的状态设置为已解决。 |
| `.resolveWith(context, [args])` | 将对象的状态设置为已解决，同时使关键字`this`在回调中指向`context`。 |
| `.reject([args])` | 将对象的状态设置为被拒绝。 |
| `.rejectWith(context, [args])` | 将对象的状态设置为被拒绝，同时使关键字`this`在回调中指向`context`。 |
| `.notify([args])` | 执行任何进度回调。 |
| `.notifyWith(context, [args])` | 在执行任何进度回调时，使关键字`this`指向`context`。 |
| `.promise([target])` | 返回与此延迟对象对应的 promise 对象。 |

# promise 对象的方法

| **方法** | **描述** |
| --- | --- |
| `.done(callback)` | 在对象被解决时执行`callback`。 |
| `.fail(callback)` | 在对象被拒绝时执行`callback`。 |
| `.catch(callback)` | 在对象被拒绝时执行**`callback`**。 |
| `.always(callback)` | 在对象被解决或被拒绝时执行`callback`。 |
| `.then(doneCallbacks, failCallbacks)` | 当对象被解决时执行`doneCallbacks`，或当对象被拒绝时执行`failCallbacks`。 |
| `.progress(callback)` | 每次对象接收到进度通知时执行`callback`。 |
| `.state()` | 根据当前状态返回`'pending'`、`'resolved'`或`'rejected'`。 |

# 杂项属性和函数

这些实用方法不能很好地归入之前的类别，但在使用 jQuery 编写脚本时通常非常有用。

# jQuery 对象的属性

| **属性** | **描述** |
| --- | --- |
| `$.ready` | 一个 promise 实例，一旦 DOM 准备就绪就解决。 |

# 数组和对象

| **函数** | **描述** |
| --- | --- |
| `$.each(collection, callback)` | 遍历`collection`，为每个项执行`callback`。 |
| `$.extend(target, addition, ...)` | 通过从其他提供的对象中添加属性修改对象`target`。 |
| `$.grep(array, callback, [invert])` | 使用`callback`作为测试过滤`array`。 |
| `$.makeArray(object)` | 将`object`转换为数组。 |
| `$.map(array, callback)` | 构造由对每个项调用`callback`的结果组成的新数组。 |
| `$.inArray(value, array)` | 判断`value`是否在`array`中。 |
| `$.merge(array1, array2)` | 合并`array1`和`array2`的内容。 |
| `$.unique(array)` | 从`array`中删除任何重复的 DOM 元素。 |

# 对象内省

| **函数** | **描述** |
| --- | --- |
| `$.isArray(object)` | 判断`object`是否为真正的 JavaScript 数组。 |
| `$.isEmptyObject(object)` | 判断`object`是否为空。 |
| `$.isFunction(object)` | 判断`object`是否为函数。 |
| `$.isPlainObject(object)` | 判断`object`是否以对象字面量形式创建或使用`new Object`创建。 |
| `$.isNumeric(object)` | 判断`object`是否为数值标量。 |
| `$.isWindow(object)` | 判断`object`是否表示浏览器窗口。 |
| `$.isXMLDoc(object)` | 判断`object`是否为 XML 节点。 |
| `$.type(object)` | 获取`object`的 JavaScript 类。 |

# 其他

| **函数** | **描述** |
| --- | --- |
| `$.trim(string)` | 从`string`的两端删除空格。 |
| `$.noConflict([removeAll])` | 将`$`恢复到其 jQuery 前定义。 |
| `$.noop()` | 一个什么也不做的函数。 |
| `$.now()` | 自纪元以来的当前时间（毫秒）。 |
| `$.holdReady(hold)` | 阻止`ready`事件的触发，或释放此保持。 |
