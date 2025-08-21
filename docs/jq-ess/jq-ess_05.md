# 第五章：用 jQuery 使你的网站漂亮

动画对于任何现代、精致的 Web 应用程序的重要性很容易被低估。有时，添加动画似乎是一种轻率且几乎是不必要的浪费时间，因为它们似乎并没有为应用程序增添任何内容。但对于用户来说，动画有着巨大的影响。它们有助于平稳过渡从一个应用程序状态到下一个。它们还有助于给用户一种位置感。当用户点击一个按钮导致页面向左移动，点击另一个按钮导致页面向右移动时，用户从动画中得到了一种位置感。在本章中，我们将学习如何使用 jQuery 动画和效果来使我们的网站漂亮起来。

我们将涵盖以下主题：

+   动画的重要性

+   隐藏和显示元素

+   滑动元素

+   创建自定义效果

+   正确使用效果

# 动画的重要性

作为开发人员，我们很容易忘记我们的应用程序有多抽象。一个 *home* 的概念被嵌入到我们的开发人员大脑中，但与任何其他页面相比，我们网站的一页并没有什么家的感觉。但如果所有其他页面都似乎集中在一个页面上而没有标记它，那么在大多数用户眼中，那个页面就成了主页。

动画效果在过渡到下一个应用程序状态时非常有帮助。没有动画，很容易忽略页面中添加或删除元素的情况。诀窍在于熟练使用动画。动画永远不应该让用户感觉它们阻碍了他们的目标。

# 隐藏和显示元素

让我们用 jQuery 来看一下动画，其中最有用的两个是 `hide` 和 `show`。jQuery 为我们提供了 `.hide()` 和 `.show()` 方法，它们正如它们的名字所暗示的那样。直接使用这两种方法，它们不会动画化；它们作为简单的开关，立即隐藏或显示附加的一组元素。然而，它们都有几种调用方式。首先，让我们看一下 `.hide()` 方法，然后再看看它的伴侣，`.show()` 方法。

在其最基本的模式下，`.hide()` 方法被调用时不带任何参数，如下所示：

```js
$(elements).hide();
```

在这种情况下调用时，它立即隐藏指定的一组元素。在内部，jQuery 向元素添加了一个样式属性，属性为 "`display: none`"。这意味着元素既不可见，也在 DOM 中放弃了它的空间。`.hide()` 方法还接受可选参数：

```js
$(elements).hide(duration, easing, complete);
```

第一个参数是持续时间。它可以是字符串或整数。如果是整数，则是完成动画所需的毫秒数。如果是字符串，则必须是持续时间的两个提供的便利名称之一，即 `fast` 或 `slow`。Fast 相当于 200 毫秒，slow 相当于 600 毫秒。

在其基本模式下，`.show()` 方法也是不带任何参数被调用的，如下所示：

```js
$(elements).show();
```

当以这种方式调用时，它将立即显示指定的元素。jQuery 将删除具有属性"`display: none;`"的样式属性。`.show()`方法接受与`.hide()`方法相同的参数。

让我们更实际地看看如何使用这些方法：

```js
<body>
<div>
<button id="hide">Hide Pics</button>
<button id="show">Show Pics</button>
</div>
<div id="allPictures">
<img id="picture1" src="img/1" class="thumbnail"/>
<img id="picture2" src="img/2" class="thumbnail"/>
<img id="picture3" src="img/3" class="thumbnail"/>
<img id="picture4" src="img/4" class="thumbnail"/>
</div>
<script type="text/javascript">
    $(document).ready(function () {
        var $hide = $('#hide'),
            $show = $('#show'),
            $allPictures = $('#allPictures');

        $hide.click(function () {
            $allPictures.hide();
        });
        $show.click(function () {
            $allPictures.show();
        });
    });
</script>
```

在我们的示例应用中，有四个缩略图图像，由非常有用的网站提供，即 lorem pixel ([`lorempixel.com`](http://lorempixel.com))。Lorem pixel 为网页设计师提供占位图像，用于构建网站时使用。这些图像不应该用于生产。

占位符 URL 由主机站点组成，后面跟着所需图像的宽度和高度（以像素为单位）。接下来是图像的类别；在我们的案例中，它是`nature`，最后的数字标识了特定的图片。lorem pixel 网站上有关于如何为占位图像创建自己的 URL 的详细信息。

图像上方是一组按钮，触发缩略图上的操作。按钮被缓存在`$hide`和`$show`变量中。当单击隐藏按钮时，我们隐藏图像。当单击显示按钮时，我们执行相反的操作并显示图像。请注意我们如何通过将它们存储在 JavaScript 对象中而不是重新查找它们来缓存 jQuery 对象的方式。缓存它们在再次使用时会提高速度。在这个小应用中，这没有任何区别，但在更复杂的应用中，这可能会导致速度显著提高。我们将在第七章中更详细地讨论这个问题，*与服务器交互*。

### 提示

为了更容易知道哪些变量保存了 jQuery 对象，我们采用了以美元符号`$`开头的标准。一些开发人员不喜欢在变量名中使用助记符，但我发现这对我很有帮助，尤其是在阅读我的团队其他开发人员编写的代码时。

`hide`和`show`是如此密切相关，以至于 jQuery 有一个特殊的方法可以执行两者。`.toggle()`方法会在显示时隐藏元素，并在隐藏时显示元素。对于我们的用例来说，这是一个更合适的方法，所以让我们修改代码：

```js
$(document).ready(function () {
    var $hide = $('#hide'),
        $show = $('#show'),
        $toggle = $('#toggle'),
        $allPictures = $('#allPictures');

    $hide.click(function () {
        $allPictures.hide();
    });
    $show.click(function () {
        $allPictures.show();
    });
    $toggle.click(function () {
 $allPictures.toggle();
 });
});
```

我们只需要更改几行代码。首先，我们需要一个变量来保存切换按钮。然后，我们添加了钩子点击切换按钮的代码，最后，我们调用了`.toggle()`方法。切换按钮的好处是我们可以不断点击它，它将继续切换图片的状态。

隐藏和显示图片是有趣的，但并不是非常令人激动。因此，让我们在代码中添加持续时间。以下是应用程序的修改：

```js
$(document).ready(function () {
    var $hide = $('#hide'),
        $show = $('#show'),
        $toggle = $('#toggle'),
        $allPictures = $('#allPictures');

    $hide.click(function () {
 $allPictures.hide('slow');
    });
    $show.click(function () {
        $allPictures.show('fast');
    });
    $toggle.click(function () {
 $allPictures.toggle(1500);
    });
});
```

这次，我们只对三行进行了更改。每行都调用一个效果方法。我们将`slow`传递给`.hide()`方法，`fast`传递给`.show()`方法，将`1500`传递给`.toggle()`方法。记住，当一个整数被传递给一个效果方法时，它表示的是以毫秒为单位的时间，因此 1,500 表示 1.5 秒。

对我们目前已有的效果添加一些持续时间比简单地打开和关闭元素更吸引人，但让图像淡入淡出而不是缩小和放大可能更好。幸运的是，jQuery 提供了一些恰好可以做到这一点的方法：淡出和淡入。

## `.fadeOut()`

`.fadeOut()`方法逐渐将 CSS 不透明度属性减小到 0，使元素不再可见，然后将显示属性设置为`none`。该方法的参数与`.hide()`相同。

## `.fadeIn()`

`.fadeIn()`方法与`.fadeOut()`方法相反。它首先将显示属性设置为增加不透明度属性为 1，以便元素完全不透明。

## `.fadeToggle()`

与`.toggle()`方法类似，`.fadeToggle()`方法将会淡出元素（如果它们可见）并且淡入元素（如果它们不可见）。

为了更实际的例子，让我们将`.hide()`、`.show()`和`.toggle()`方法分别替换为`.fadeOut()`、`.fadeIn()`和`.fadeToggle()`：

```js
$(document).ready(function () {
    var $hide = $('#hide'),
        $show = $('#show'),
        $toggle = $('#toggle'),
        $allPictures = $('#allPictures');

    $hide.click(function () {
        $allPictures.fadeOut('slow');
    });
    $show.click(function () {
        $allPictures.fadeIn('fast');
    });
    $toggle.click(function () {
        $allPictures.fadeToggle(1500);
    });
});
```

在上述代码中，我们用它们的淡入淡出等效方法替换了隐藏/显示方法。代码仍然像之前一样运行，只是现在，我们有了一个新的动画效果：淡入淡出。

到目前为止，我们只使用了持续时间参数，但我们知道还有两个：`easing`和`complete`。让我们先查看`complete`，因为它相当容易理解。

`complete`参数是一个在动画完成后调用的函数。它没有显式传递任何参数，但隐式传递了指向动画元素的`this`对象。通过对我们的示例程序进行一些小的修改，我们可以看到这一点：

```js
$toggle.click(function () {
    $allPictures.fadeToggle(1500, function () {
 alert("Hola: "+this.id);
 });
});
```

在持续时间之后，我们添加了一个内联匿名函数，该函数在动画完成后调用。`this`对象指向动画元素，因此我们在警报消息框中显示它的`id`。完整函数对每个动画元素调用一次，这可能会让人感到惊讶。在我们当前的例子中，我们正在动画显示一些图片的`<div>`的内容。考虑到我们将我们的代码更改为以下内容：

```js
$toggle.click(function () {
    $('img').fadeToggle(1500, function () {
 alert("Hola: "+this.id);
 });
});
```

通过将我们的代码更改为指向各个`<img>`标签而不是它们的父`<div>`容器，完整函数现在针对每个标签调用一次，我们将逐个查看`<img>`标签的警报信息。

缓动参数处理动画对象的移动方式。在现实世界中，物体很少以恒定的速度移动。想象一下火车离开车站的情景。它慢慢加速离开。它缓慢加速直到达到所需速度，然后在某个时刻，它将开始减速，因为它接近下一个车站。如果我们将火车的速度图表化，它将是一条曲线，而不是一条直线。物体的加速和减速对我们来说似乎很自然，因为这是自然界中物体移动的方式，无论是火车，还是猎豹追逐羚羊，还是我们从桌子上站起来拿杯水。

jQuery 开箱即用提供了两种缓动效果：默认的名为 swing 和第二个称为 linear。到目前为止，我们一直在使用 swing，因为它是默认的。让我们也看看 linear，并比较两者：

```js
<div>
<button id="hide">Hide Pics</button>
<button id="show">Show Pics</button>
<button id="toggleSwing">Toggle Swing Pics</button>
<button id="toggleLinear">Toggle Linear Pics</button>
</div>
<div id="allPictures">
<img id="picture1" src="img/1" class="thumbnail"/>
<img id="picture2" src="img/2" class="thumbnail"/>
<img id="picture3" src="img/3" class="thumbnail"/>
<img id="picture4" src="img/4" class="thumbnail"/>
</div>
<script type="text/javascript">
    $(document).ready(function () {
        var $hide = $('#hide'),
                $show = $('#show'),
$toggleSwing = $('#toggleSwing'),
$toggleLinear = $('#toggleLinear'),
                $allPictures = $('#allPictures');

        $hide.click(function () {
            $allPictures.fadeOut('slow');
        });
        $show.click(function () {
            $allPictures.fadeIn('fast');
        });
$toggleSwing.click(function () {
 $allPictures.fadeToggle(1500, "swing");
 });
 $toggleLinear.click(function () {
 $allPictures.fadeToggle(1500, "linear");
 });
    });
</script>
```

在前述代码中，我们去掉了切换按钮，并用两个新的切换替换它：一个使用 `"swing"`，另一个使用 `"linear"`。我们还创建了两个事件处理程序来实现适当的淡入和淡出。虽然两种缓动之间的差异很细微，但确实存在。当物体在移动时更加明显。因此，在下一节中，我们将介绍滑动动画，并看看我们的两种缓动效果如何。

# 将元素滑动

下一组方法的调用方式与前两组相同。滑动方法与显示和隐藏方法非常相似，不同之处在于，它只改变集合元素的高度维度，而不是宽度维度。

## .slideUp()

`.slideUp()` 方法将一组元素的高度减小到零，然后将显示属性设置为 `none`。该方法采用了与之前讨论过的相同的三个可选参数：`duration`、`easing` 和 `complete`：

```js
$(elements).slideUp([duration],[easing], [complete]);
```

## .slideDown()

`.slideDown()` 方法将一组元素的高度增加到 100%。它的调用如下：

```js
$(elements).slideDown([duration],[easing], [complete]);
```

## .slideToggle()

此组的最后一个成员是 `.slideToggle()` 方法，它会在显示状态和隐藏状态之间交替切换一组元素：

```js
$(elements).slideDown([duration],[easing], [complete]);
```

我们已经修改了代码，使用了滑动方法而不是淡入淡出方法。缓动方法现在更加明显不同。这是更改后的代码：

```js
$hide.click(function () {
    $allPictures.slideUp('slow');
});
$show.click(function () {
    $allPictures.slideDown('fast');
});
$toggleSwing.click(function () {
    $allPictures.slideToggle(1500, "swing");
});
$toggleLinear.click(function () {
    $allPictures.slideToggle(1500, "linear");
});
```

由于 jQuery 动画方法的调用方式都很相似，我们只需简单地将 `fadeOut`、`fadeIn` 和 `fadeToggle` 这些词替换为它们的滑动效果对应词即可。

我认为我们已经花了足够的时间研究基本动画效果。真正有趣和引人注目的是自定义效果。

# 创建自定义效果

jQuery 并没有包含许多效果；开箱即用，只有隐藏和显示、淡入和淡出以及滑动方法。如果只有这些，可能不值得将它们包含在库中。幸运的是，jQuery 让我们可以创建自己的动画。

## .animate()

理解 `.animate()` 方法是理解如何创建自定义效果的关键。虽然其他方法也很重要，但在让动画方法生效之前，它们都无济于事，而让动画方法生效其实并不太难，特别是如果你记住动画属性的工作原理的话。它通过操纵 CSS 属性的值来实现。大多数——但不是全部——属性都可以操纵。让我们快速查看一下，以更好地解释 `.animate()` 方法的工作原理：

```js
<div>
<button id="hide">Hide Pics</button>
<button id="show">Show Pics</button>
<button id="toggleSwing">Animate Swing Pics</button>
<button id="toggleLinear">Animate Linear Pics</button>
</div>
<div id="allPictures" style="position: relative;">
<img id="picture1" src="img/1" class="thumbnail"/>
<img id="picture2" src="img/2" class="thumbnail"/>
<img id="picture3" src="img/3" class="thumbnail"/>
<img id="picture4" src="img/4" class="thumbnail"/>
</div>
<script type="text/javascript">
    $(document).ready(function () {
        var $hide = $('#hide'),
                $show = $('#show'),
                $toggleSwing = $('#toggleSwing'),
                $toggleLinear = $('#toggleLinear'),
                $allPictures = $('#allPictures');

        $hide.click(function () {
            $allPictures.slideUp('slow');
        });
        $show.click(function () {
            $allPictures.slideDown('fast');
        });
        $toggleSwing.click(function () {
 $allPictures.animate({left: "+=200"}, 1500, "swing");
        });
        $toggleLinear.click(function () {
$allPictures.animate({left: "-=200"}, 1500, "linear");
        });
```

我们对我们值得尊敬的示例进行了一些修改。我们改变了按钮的文本，添加了两个 `.animate()` 方法，并且还不得不添加一个相对于 `allPictures<div>` 的定位样式。相对定位的添加非常重要，其缺失可能是动画困扰的根源。jQuery 的动画函数不会改变 CSS 规则。为了移动 `<div>`，它必须是可移动的。缺少位置属性的元素默认为位置 static，这意味着它们与页面的布局一起定位，无法移动。所以如果你尝试对其进行动画，什么也不会发生。将 `<div>` 放置在相对定位中意味着它可以相对于其布局位置移动。

`.animate()` 方法接受我们已经熟悉的三个参数——持续时间、缓动和完成回调——并添加一个新参数：properties，其中包括我们希望动画的一个或多个 CSS 属性。属性的值可以是绝对的或相对的。如果你只是在属性中放入一个值，那么它是绝对的。jQuery 将会动画到这个值。如果你尝试第二次运行绝对动画，什么也不会发生，因为属性已经包含了所需的值。另一方面，相对值更具方向性。在早期的示例应用程序中，我们使用了两个不同的相对属性值。第一个告诉 jQuery 将 `<div>` 向右移动 200 像素。第二个，附加到切换线性按钮，告诉 jQuery 将 `<div>` 向左移动 200 像素。要使用相对值，只需将值放在引号中，并在前面加上 `+=xx` 或 `-=xx`，其中 `xx` 是要更改属性的量。

你可以一次修改多个属性。当你添加更多属性时，jQuery 将逐个动画到其值：

```js
$toggleSwing.click(function () {
    $allPictures.animate({
 left: "+=200",
 top: "+=200",
 opacity: "-=1"
    }, 1500, "swing");
});
$toggleLinear.click(function () {
        $allPictures.animate({
 left: "-=200",
 top: "-=200",
 opacity: "+=1"
    }, 1500, "linear");
});
```

在前面的示例中，我们给每个 `.animate()` 方法添加了两个更多属性：top 和 opacity，都像 left 属性一样是相对的。关于动画方法的一个重要事项是，与 show/hide、fade 或 slide 方法不同，它从不将 `"display: none"` 样式添加到元素。即使不透明度为 0，元素仍然占据页面上的所有空间。

## .delay()

动画可以通过将动画方法链接在一起按顺序运行。您还可以使用`.delay()`方法为动画引入延迟。它接受两个参数：持续时间和队列名称。`持续时间`告诉您应该暂停动画引擎多长时间（以毫秒为单位），而`queueName`是要延迟的队列的名称。在本书中，我们不会使用命名队列，因此我们不会讨论队列名称：

```js
$toggleSwing.click(function () {
    $allPictures
 .animate({
 left: "+=200"
 }, 1500, "swing")
 .delay(1000)
 .animate({
 top: "+=200",
 opacity: "-=1"
 }, 1500, "swing");
});
$toggleLinear.click(function () {
    $allPictures
 .animate({
 top: "-=200",
 opacity: "+=1"
 }, 1500, "linear")
 .delay(1000)
 .animate({
 left: "-=200"
 }, 1500, "linear");
});
```

在此示例中，我们将两个单独的动画链接在一起，并在它们之间延迟 1 秒。请注意，如果您快速按下任一按钮，很快，`<div>`将从页面中消失，并且可能再也看不到。这个问题是由于 jQuery 将每个新的动画请求添加到队列中引起的。很容易比 jQuery 执行它们更快地将项目添加到队列中。我们将在下一节中解决这个问题。

## `.queue()`、`.dequeue()`和`.clearQueue()`

队列方法为我们提供了访问 jQuery 用于运行动画的动画队列。jQuery 允许我们有多个队列，但很少需要多个队列。`.queue()`方法可用于获取和设置标准动画队列或自定义队列。我们可以使用队列方法查看当前队列中有多少项目，并且我们可以将回调函数传递给它，一旦队列为空，就会调用该函数。

当队列被停止并添加了新项目时，`.dequeue()`方法很方便。为了让队列再次运行，必须调用`.dequeue()`方法。`.clearQueue()`方法从队列中移除尚未执行的所有排队项目。

## `.stop()`和`.finish()`

`.stop()`方法停止当前动画。如果正在运行动画有关联的回调，则不会调用它们。`.finish()`方法与此非常相似，只是它做了所有事情。它停止正在运行的动画，清除队列，并完成匹配集的所有动画。

## jQuery.fx.interval 和 jQuery.fx.off

这些是 jQuery 内部的两个全局变量。`jQuery.fx.interval`方法设置动画速率。值越低，动画运行得越快，可能更平滑。一般来说，您可能不想改变此值。这是全局动画计时器。更改它会更改所有动画的时间，而不仅仅是您的动画。当`jQuery.fx.off`方法设置为 true 时，它会停止所有动画。

# 正确使用效果

当 jQuery 遇到需要执行的新动画时，它将新动画放在动画队列的末尾。虽然这在大多数情况下是处理事情的好方法，但可能会通过更快地将项目放入队列中而超载队列。一般来说，当向队列中添加更多项目时，您应该小心。您可能希望在向队列添加更多项目之前清除队列。

尽管 jQuery 的动画特性非常方便，但它们并不完全是最先进的，它们开始显得有些过时了。jQuery 源代码中可以看到一个例子。以下代码来自 jQuery 版本 2.1.1 的源代码：

```js
jQuery.fx.start = function() {
    if ( !timerId ) {
        timerId = setInterval( jQuery.fx.tick, jQuery.fx.interval );
    }
};
```

在运行动画时，jQuery 使用 `setInterval()` 方法来定时每个动画帧；事实上，`jQuery.fx.interval` 全局值被用作计时器值。虽然几年前这很酷，但大多数现代浏览器使用 `window.requestAnimationFrame()` 方法，对于缺乏该方法的浏览器，有可用的 polyfill。使用 `setInterval` 方法而不是 `requestAnimationFramework` 的最终效果是，即使在最新的浏览器和最快的硬件上，jQuery 的动画看起来仍然有点闪烁，因为渲染帧和 `setInterval` 方法之间没有协调，就像使用 `requestAnimationFrame` 一样。

# 摘要

我们在本章涵盖了大量代码。动画可能看起来很容易编写，但是当做得好时，它们可以为应用程序增加很多内容，并帮助用户了解应用程序状态的变化。我们首先学习了如何使用 jQuery 的易于使用的内置动画方法。一旦我们理解了它们，我们就转向了 `.animate()` 和 `.delay()` 方法，这些方法允许我们创建自己的自定义动画。

我们用一点关于 jQuery 执行动画的信息结束了本章。虽然在示例中显示的简单示例中是可以的，但实际上有点过时。如果您想执行更复杂的动画，您可能想看看更复杂的库，如 `Velocity.js`、`GSAP` 或其他库。

在下一章中，我们将学习使用 jQuery 提交表单数据。特别是，我们将学习如何在发送数据到服务器之前验证我们的表单。
