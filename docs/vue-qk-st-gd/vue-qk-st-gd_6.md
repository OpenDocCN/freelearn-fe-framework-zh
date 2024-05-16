# 第六章：过渡和动画

在这一章中，我们将看看如何在 Vue 中使用过渡和动画。这是一个很大的主题，需要更多的章节来覆盖。因此，我们将处理一些基本概念，以便在将来构建。

我们将重点关注以下主题：

+   理解 CSS 过渡和动画

+   使用`transition`组件实现过渡

+   在 Vue 中使用 CSS 过渡和动画

+   与第三方 CSS 和 JS 库集成

+   绑定 CSS 样式

+   与过渡组一起工作

+   JavaScript 动画钩子

阅读完本章后，您应该对 Vue 中如何使用过渡和动画有扎实的理解。

# CSS 中的过渡和动画

要了解 Vue.js 如何处理过渡和动画，我们首先需要快速复习一下它们在 CSS 中的工作原理。我们将专注于基础知识，目标是重新审视管理过渡和动画的原则。我们还将看看它们的区别。目标是更好地理解 Vue 如何帮助，而不是深入了解过渡和动画的细微差别。

# CSS 过渡的工作原理

当我们悬停在一个元素上时，我们将该元素置于悬停状态。当用户通过与我们的网页进行交互触发悬停状态时，我们可能希望*强调*这种状态的变化已经发生。

为了强调状态的变化，我们可以例如在用户悬停在元素上时改变该元素的 CSS `background-color`属性。

这就是 CSS 过渡发挥作用的地方。当我们为 CSS 过渡编写代码时，我们*指示*浏览器如何显示对该特定 CSS 属性所做的更改-在我们的例子中是`background-color`属性。

假设我们有一个 HTML `button`元素。该元素的 CSS 属性`background-color`设置为`red`：

```js
button {
  background-color: red;
}
```

当用户悬停在按钮上时，我们希望将`background-color`属性的值从`red`更改为`blue`。我们可以这样做：

```js
button:hover {
  background-color: blue;
}
```

示例代码在这里可用：[`codepen.io/AjdinImsirovic/pen/LJKJYY`](https://codepen.io/AjdinImsirovic/pen/LJKJYY)。

然而，这种颜色的变化是突然的。为了*平滑过渡*HTML 元素的 CSS 属性从一个值到另一个值，我们使用 CSS 的`transition`属性。`transition`属性是一个简写的 CSS 属性。它只是另一个我们在目标元素上指定的 CSS 属性，我们希望对其应用这种平滑过渡。

在我们的例子中，我们希望平滑地将按钮从红色背景过渡到蓝色背景。我们只需在按钮元素上添加简写的`transition`属性，并在这个`transition`属性上设置两个值：

```js
button {
 background-color: red;
 transition: background-color 4s;
}
button:hover {
 background-color: blue;
}
```

这是公式：

```js
transition: property-to-transition transition-duration, property-to-transition transition-duration
```

在我们的例子中，我们只为一个属性指定了持续时间，但我们可以根据需要添加更多。

# CSS 动画的工作原理

在上一个例子中，我们看到了一个简单的过渡。在这个例子中，我们将把过渡转换为动画。更新后的 CSS 代码将如下所示：

```js
button {
  background-color: red;
}
button:hover {
  animation: change-color 4s;
}
@keyframes change-color {
  0% {
    background: red;
  }
  100% {
    background: blue;
  }
}
```

在上一个代码中，我们已经将我们简单的 CSS 过渡转换为了 CSS 动画。

此示例可在此链接找到：[`codepen.io/AjdinImsirovic/pen/WaNePm`](https://codepen.io/AjdinImsirovic/pen/WaNePm)。

然而，它并不完全相同。当我们悬停在按钮上时，我们并没有得到与过渡示例中相同的行为。原因是我们已经指定了动画的初始状态（为`0%`）和最终状态（为`100%`）。因此，我们实际上是将我们在过渡示例中的行为映射到动画示例中的行为。

然而，当我们将鼠标指针从按钮上移开时，动画并不会倒回到初始状态，而是突然切换回原始的红色背景颜色。在 CSS 中，没有`mouseout`属性。

然而，我们可以在中间添加额外的步骤。例如，我们可以在变化动画的 50%处将背景颜色设置为绿色。结果可以在此 URL 中看到：[`codepen.io/AjdinImsirovic/pen/QZWWje`](https://codepen.io/AjdinImsirovic/pen/QZWWje)。

在我们深入了解 Vue 如何实现过渡和动画之前，让我们先看看它们在 CSS 中的区别。

# CSS 中过渡和动画的区别

以下是 CSS 中过渡和动画之间的两个快速、不完整的区别列表。

# CSS 过渡的规则

以下是 CSS 过渡的一些重要规则：

+   过渡只有暗示的开始和结束状态

+   浏览器决定了过渡的执行方式；换句话说，浏览器决定了过渡的中间步骤如何执行

+   我们只能指定要过渡的确切 CSS 属性，以及持续时间、缓动等

+   过渡是*被触发的；*触发可以是悬停或页面上元素的出现（通过 JavaScript）

+   过渡不能循环

+   当触发状态（悬停状态）被恢复时，过渡会以相反的方式播放，也就是当鼠标*取消悬停*时

+   过渡语法比动画的语法更简单

接下来，让我们列出 CSS 动画的重要概念。

# CSS 动画规则

以下是 CSS 动画的不完整规则列表：

+   动画允许我们指定 CSS 属性的初始状态、中间状态和结束状态

+   我们的 CSS 动画中可以有尽可能多的步骤

+   我们可以延迟动画，播放*x*次（无限次），或者以相反的方向播放它们

+   动画不一定要被触发，但它们可以被触发

在弄清楚这些基本区别之后，让我们接下来看看如何在 Vue 中处理过渡和动画。

# Vue 中的过渡元素

让我们看看之前的纯 CSS 过渡的示例，转换成 Vue。在下面的示例中，第一个按钮包裹在一个自定义组件中，而第二个按钮只是常规的 HTML 按钮元素。它们仍然共享相同的样式，如应用程序的 CSS 中指定的那样：

```js
<!-- HTML -->
<div id="app">
  <button>Hover me!</button>
  <custom-component></custom-component>
</div>

// JS
Vue.component('customComponent', {
  template: `
    <button>Hover me too!</button>
  `
});
new Vue({
  el: '#app'
});

/* CSS */
button {
  background-color: red;
  transition: background-color 4s;
}
button:hover {
  background-color: blue;
}
/* some additional styling */
* {
  border: none;
  color: white;
  padding: 10px;
  font-size: 18px;
  font-weight: 600;
}

```

之前的代码可以在这里找到：[`codepen.io/AjdinImsirovic/pen/vVYERO`](https://codepen.io/AjdinImsirovic/pen/vVYERO)[.](https://codepen.io/AjdinImsirovic/pen/vVYERO) 如例所示，在这种情况下，Vue 并没有偏离纯 HTML 和 CSS 中过渡和动画的工作方式。

Vue 并不是为了覆盖 CSS 过渡和动画的正常用例而设计的，而是为了与它们一起工作，有一个特定的目标：在屏幕上过渡其*组件*的出现和移除。这些组件的添加和移除是通过 Vue 的`transition`元素来完成的。

例如，当您希望一个组件中的事件影响另一个组件的添加和移除时，您只需将另一个组件包裹在`transition`元素中。从之前的纯 CSS 示例构建，这是 Vue 中的一个简单实现：

```js
<!-- HTML -->
<div id="app">
  <button v-on:click="show = !show">
    Show? {{ show }}
  </button>
  <transition>
    <span v-if="show">
      <custom-component></custom-component>
    </span>
  </transition>
</div>

// JS
Vue.component('customComponent', {
  template: `
    <button>Hover me!</button>
  `
});
new Vue({
  el: '#app',
  data: {
    show: true
  }
});

/* CSS is the same as in the previous example */
```

示例代码可以在这里找到：[`codepen.io/AjdinImsirovic/pen/ZqExJO`](https://codepen.io/AjdinImsirovic/pen/ZqExJO)。

如果您需要元素在初始页面加载时平滑出现，而不受条件限制，那么您可以在转换包装器上使用`appear`属性，就像这样：`<transition appear>`。

在上面的代码中发生的是，我们有条件地切换`custom-component`元素的挂载，这取决于用户是否点击了第一个按钮。请注意，原始的 CSS 转换在两个按钮中仍然以完全相同的方式工作。当我们悬停在它们中的任何一个上时，我们仍然会得到从红色到蓝色的四秒过渡背景颜色。当我们从任何一个按钮悬停离开时，浏览器仍然会处理按钮背景的*反向*过渡。

然而，第二个按钮在屏幕上的挂载是没有任何转换的。第二个按钮简单地在点击第一个按钮时出现和消失，没有任何缓入或缓出。

为了实现这种逐渐出现和消失的效果，`transition`元素带有内置的 CSS 类名。这些内置的转换类名也被称为**动画钩子**。这些动画钩子描述了包裹在`transition`元素内的组件的起始状态、结束状态和中间状态；也就是说，它们描述了受影响的组件将如何在屏幕上切换开和关闭。

我们可以将动画钩子添加到*进入*转换或*离开*转换。进入转换类是`v-enter`，`v-enter-active`和`v-enter-to`。离开转换类是`v-leave`，`v-leave-active`和`v-leave-to`。

# 设置进入转换

为了在上一个例子的基础上进行扩展，我们将利用这些动画钩子使第二个按钮的出现和消失更加平滑。上一个例子和这个例子之间唯一的区别是在我们的 CSS 中添加了动画钩子：

```js
.v-enter {
  opacity: 0;
}
.v-enter-active {
 transition: opacity 3s;
}
```

这个例子的代码可以在以下链接找到：[`codepen.io/AjdinImsirovic/pen/MPWVNm`](https://codepen.io/AjdinImsirovic/pen/MPWVNm)。

如果我们将第二个按钮的外观想象成一个常规的 CSS 转换，那么`.v-enter`动画钩子将是初始转换状态，`.v-enter-active`将是中间步骤，`.v-enter-to`将是最终转换状态，也就是元素将要转换*到*的状态。

因为我们在示例中没有使用`.v-enter-to`动画钩子，所以我们得到的行为如下：当点击第一个按钮时，第二个按钮需要三秒钟才能从初始值零过渡到暗示的值一（不透明度）。这就完成了我们的进入过渡。

# 设置离开过渡

我们之前的示例有一个小问题：当我们再次点击第一个按钮时，第二个按钮会立即消失，因为它的不透明度值会在没有任何过渡的情况下重置为零。原因很简单：我们没有指定任何*离开*过渡钩子，所以按钮就会消失。我们将在下一个示例中修复这个问题，只需简单地指定离开过渡，就像这样：

```js
.v-leave {
  opacity: 1;
}
.v-leave-active {
  transition: opacity 3s;
}
.v-leave-to {
  opacity: 0;
}
```

完整的代码可以在这里找到：[`codepen.io/AjdinImsirovic/pen/XxWqOy`](https://codepen.io/AjdinImsirovic/pen/XxWqOy)。在这段代码中，我们做的是：当组件需要被动画移出时，我们过渡的初始状态是`.v-leave`。在`.v-leave`动画钩子中的 CSS 声明是`opacity: 1`。接下来，我们指定中间步骤：要过渡的 CSS 属性，即`opacity`，以及过渡的持续时间：`3s`。最后，我们指定过渡的完成状态，其中`opacity`被设置为零的值。

从这些示例中我们可以得出结论，*离开*过渡的动画钩子（`v-leave`，`v-leave-active`和`v-leave-to`）应该是*镜像的*，就像进入过渡的动画钩子（`v-enter`，`v-enter-active`和`v-enter-to`）一样。

我们还可以得出结论，过渡组件和随之而来的动画钩子用于在屏幕上挂载和卸载组件。当在屏幕上过渡组件时，动画钩子的自然进展是这样的：

```js
.v-enter --> .v-enter-active --> .v-enter-to --> .v-leave --> v-leave-active --> .v-leave-to
```

我们还可以将共享相同值的某些 CSS 选择器分组，如下所示：

```js
.v-enter, .v-leave-to {
  opacity: 0;
}
.v-enter-active, .v-leave-active {
  transition: opacity 3s;
}
.v-enter-to, .v-leave {
  opacity: 1;
}
```

这个示例可以在以下网址找到：[`codepen.io/AjdinImsirovic/pen/dgyKMG`](https://codepen.io/AjdinImsirovic/pen/dgyKMG)。

正如在这里所看到的，`.v-enter`（初始进入动画钩子）与`.v-leave-to`（最后离开动画钩子）被合并在一起，这是因为过渡必须以相反的方式播放，以获得最符合预期的行为。同样，我们将中间步骤，即`-active`钩子分组，以具有相同的`transition` CSS 属性。最后，进入动画的最终钩子需要与初始离开动画钩子共享 CSS 声明。此外，由于`.v-enter-to`和`.v-leave`的值默认情况下是隐含的，我们甚至可以省略它们，仍然可以拥有一个正常工作的组件过渡，类似于官方文档中描述的过渡。

为了简化推理，我们在最近的示例中还将`data`选项的`show`键更改为`false`的值。这样，最初组件不会挂载到 DOM 上。只有当用户点击第一个按钮时，第二个按钮的进入动画钩子才会启动，并平滑地过渡组件。再次点击时，第二个按钮的离开动画钩子将启动，并以相反的方式过渡组件。这很重要，因为最初我们让进入动画过渡组件的卸载，而离开动画过渡组件重新挂载到页面上，这可能会使事情变得更加复杂。

# 命名过渡组件

我们可以给过渡元素添加`name`属性。这样做会改变动画钩子的命名约定。例如，如果我们给过渡命名为`named`，那么动画钩子将需要按照以下方式重新命名。对于每个过渡类，我们将用`name`属性的值替换`v-`的开头。因此，`v-enter`将变成`named-enter`，`v-leave`将变成`named-leave`，依此类推。

让我们用一个命名过渡来重写前面的例子：

```js
<!-- HTML -->
<div id="app">
  <button v-on:click="show = !show">
    Show? {{ show }}
  </button>
  <transition name="named">
    <span v-if="show">
      <custom-component></custom-component>
    </span>
  </transition>
</div>

/* CSS */
/* 'named' transition */
.named-enter, .named-leave-to {
  opacity: 0;
}
.named-enter-active, .named-leave-active {
  transition: opacity 3s;
}
.named-enter-to, .named-leave {
  opacity: 1;
}

// JS is unchanged
```

此示例的代码可在此 CodePen 中找到：[`codepen.io/AjdinImsirovic/pen/MPWqgm`](https://codepen.io/AjdinImsirovic/pen/MPWqgm)。

# 使用过渡组件的 CSS 动画

CSS 动画也可以通过过渡组件来实现。以下是将以 CSS 过渡为例的先前示例转换为使用 CSS 动画的示例。我们将从 HTML 开始：

```js
<div id="app">
  <button v-on:click="show = !show">
    Show? {{ show }}
  </button>
  <transition name="converted">
    <span v-if="show">
      <custom-component></custom-component>
    </span>
  </transition>
</div>
```

接下来，我们将添加以下 JavaScript 代码：

```js
Vue.component('customComponent', {
  template: `
    <button>Lorem ipsum</button>
  `
});
new Vue({
  el: '#app',
  data: {
    show: false
  }
});
```

我们也会添加一些简单的样式：

```js
/* 'named' transition is replaced with 'converted' animation */
.converted-enter-active {
  animation: converted .5s;
}
.converted-leave-active {
  animation: converted .5s reverse;
}
@keyframes converted {
  0% { opacity: 0; }
  35% { background-color: purple; }
  65% { background-color: green; }
  100% { opacity: 1; }
}
/* other styles */
button {
  background-color: red;
  transition: background-color 4s;
}
button:hover {
  background-color: blue;
}
/* some additional styling */
* {
  border: none;
  color: white;
  padding: 10px;
  font-size: 18px;
  font-weight: 600;
}
span {
  display: inline-block;
}
```

此示例的代码在此处可用：[`codepen.io/AjdinImsirovic/pen/vVEXEv`](https://codepen.io/AjdinImsirovic/pen/vVEXEv)。转换后的动画与以前使用 CSS 过渡的示例完全相同，只是在动画完成的 35%和 65%处改变了动画行为。我们得到的效果有点像边框颜色效果，即使我们正在改变此元素的`background-color`属性。这证实了我们已经讨论过的一些结论，即以下内容：

+   Vue 中的`transition`元素影响整个`<transition>`组件的出现和消失，而不是其内容

+   实际动画可以有许多步骤；换句话说，要获得与 CSS 过渡示例中相同的效果，只需简单地删除我们在动画完成的 35%和 65%处指定的步骤即可。

在下一节中，我们将讨论自定义过渡类。

# 自定义过渡类

自定义过渡类在我们想要从第三方 CSS 动画库中添加功能时非常有用。在这个例子中，我们将使用`Animate.CSS`动画库，可以在这里找到：[`daneden.github.io/animate.css/`](https://daneden.github.io/animate.css/)。

官方文档在此 URL 上充分涵盖了自定义过渡类的使用：[`vuejs.org/v2/guide/transitions.html#Custom-Transition-Classes`](https://vuejs.org/v2/guide/transitions.html#Custom-Transition-Classes)。

唯一需要添加的是我们一直在构建的示例，可以在这里找到：[`codepen.io/AjdinImsirovic/pen/rqazXZ`](https://codepen.io/AjdinImsirovic/pen/rqazXZ)。

示例的代码如下。首先，我们将从 HTML 开始：

```js
<div id="app">
  <button v-on:click="show = !show">
    Show? {{ show }}
  </button>
  <transition :duration="4000"
     name="converted"
     enter-active-class="rubberBand animated"
     leave-active-class="bounceOut animated">
        <div v-if="show">
          <custom-component>
          </custom-component>
        </div>
  </transition>
</div>
```

接下来，让我们看看 JavaScript：

```js
Vue.component('customComponent', {
  template: `
    <button>Lorem ipsum</button>
  `
});
new Vue({
  el: '#app',
  data: {
    show: false
  }
});
```

最后，在我们的样式中，我们将设置一些基本的 CSS 声明：

```js
button {
  background-color: red;
  transition: background-color 4s;
}
button:hover {
  background-color: blue;
}

* {
  border: none;
  color: white;
  padding: 10px;
  font-size: 18px;
  font-weight: 600;
}
* { overflow: hidden }
```

基本上，我们指定了与动画钩子相同名称的属性，以及属性名称末尾的额外`-class`。因此，默认的`v-enter-active` CSS 类变成了自定义的`enter-active-class` HTML 属性。然后我们给这个自定义的 HTML 属性赋予我们选择的值。我们给它的值是我们之前选择的 CSS 动画库中要使用的效果的类名——在这种情况下是`Animate.CSS`库。在之前的代码中，我们还设置了`:duration`属性，指定过渡的持续时间为 4000 毫秒。在我们的示例中，如果我们设置的`:duration`属性比我们从第三方库提供的动画的持续时间短，这实际上只会产生效果。例如，尝试将`:duration`属性设置为 100 毫秒，看看动画被截断。这可能会产生一些有趣的效果。

# 结合过渡模式、持续时间、键和 v-if。

过渡模式用于在屏幕上平滑地移除一个元素，并无缝地替换为另一个元素。`<transition>`组件默认的过渡模式是同时过渡：一个元素被移除的同时另一个元素被添加。

但是，在某些过渡中，最好让新元素出现，只有当这个过渡完成时，旧元素才被移除。这种过渡模式被称为`in-out`过渡模式。要添加它，我们只需使用自定义模式 HTML 属性，并给它赋值`in-out`，就像这样：

```js
<transition mode="in-out">
```

或者，我们可能想要使用`out-in`过渡模式，首先让旧元素过渡出去，然后，当过渡完成时，新元素才过渡进来。

让我们看看实际操作。示例可在此处找到：[`codepen.io/AjdinImsirovic/pen/yRyPed`](https://codepen.io/AjdinImsirovic/pen/yRyPed)。

以下是 HTML 代码：

```js
<div id="app">
  <transition name="smooth" mode="out-in" :duration="500">
      <button v-if="show" 
              key="first" 
              v-on:click="show = !show">
                Show? {{ show }}
      </button>
      <button v-else
              key="second" 
              v-on:click="show = !show">
                Show? {{ show }}
      </button> 
  </transition>
  <transition :duration="1000"
     enter-active-class="slideInDown animated"
     leave-active-class="slideOutDown animated">
        <div v-if="show">
          <custom-component>
          </custom-component>
        </div>
  </transition>
</div>
```

我们仍然使用相同的 JS：

```js
Vue.component('customComponent', {
  template: `
    <button>Lorem ipsum</button>
  `
});
new Vue({
  el: '#app',
  data: {
    show: false
  }
});
```

我们的 CSS 发生了一些变化：

```js
/* CSS classes used are imported from the Animate CSS library
and can be found in Settings of this pen */
/* other styles */
.smooth-enter, .smooth-leave-to {
  opacity: 0;
}
.smooth-enter-active, .smooth-leave-active {
  transition: opacity .5s;
}
.smooth-enter-to, .smooth-leave {
  opacity: 1;
}

button {
  background-color: red;
  transition: background-color 4s;
}
button:hover {
  background-color: blue;
}

* {
  border: none;
  color: white;
  padding: 10px;
  font-size: 18px;
  font-weight: 600;
}
* { overflow: hidden }
```

我们在转换过渡中在两个`button`元素之间切换开关。由于这两个元素具有相同的标签名称，我们需要给它们不同的`key`属性，以便 Vue 可以区分它们。

此外，我们有条件地渲染我们的按钮。虽然我们在第一个按钮中保留了`v-if="show"`的检查，但在第二个按钮中，我们只是使用了`v-else`指令，而没有给它一个要检查的值。

# 在 Vue 中绑定 CSS 样式

在这一部分，我们将讨论如何在组件挂载或移除时，对页面的其他部分进行动画处理。为此，我们将使用`v-bind`指令，正如我们在前几章中所看到的，我们可以使用这个指令来绑定 HTML 属性。一旦绑定，这些属性就可以从我们的 Vue 实例中进行操作。

我们将演示 CSS 样式绑定的示例是一个简单的入职演示。在网页可用性方面，入职是向 Web 应用的新用户展示网页的整体功能的做法，这是通过突出显示页面的某个部分并显示一个包含一些信息的气泡窗口来实现的，进一步描述了入职过程中特定步骤的功能。

首先，我们需要了解，我们可以通过将`v-bind:class`指令的值作为对象来静态地绑定 CSS 类，就像下面的例子一样：

```js
<p v-bind:class="{}">Some text...</p>
```

在对象内部，我们可以简单地将 CSS 类添加为键，将布尔值`true`和`false`作为值。设置为`true`的 CSS 值将被使用，否则将不会被使用，就像下面的例子一样：

```js
<button v-bind:class="{'btn': true, 'btn-lg': true, 'btn-primary': true, 'btn-secondary': false}">A button</button>
```

在这个例子中，我们使用了 Bootstrap 框架的 CSS 类。我们将按钮设置为`btn-primary`类，因为它被设置为`true`，而不是`btn-secondary`，它被设置为 false。

因为`v-bind`指令允许我们以编程方式控制 HTML 属性，所以我们可能会在点击时使我们的应用切换 CSS 类。例如，在一个基本的 Vue 应用中，我们可能会在我们的 HTML 中这样做：

```js
<button v-bind:class="'btn':true','btn-lg':true, 'btn-primary':true, 'btn-secondary':btnClicked">
A button
</button>
```

在前面的代码中，我们将`btn`、`btn-lg`和`btn-primary`的类设置为`true`，并将`btn-secondary`的值设置为`btnClicked`。接下来，我们将在 JavaScript 中将`btnClicked`的值设置为`false`：

```js
data: {
  btnClicked: false,
}
```

最后，我们将为我们的按钮添加点击事件，因此当点击时，`btnClicked`的值将从`true`切换到`false`，反之亦然。以下是代码：

```js
<button 
  v-on:click="btnClicked = !btnClicked" 
  v-bind:class="'btn':true','btn-lg':true, 'btn-primary':true, 'btn-secondary':btnClicked">
    A button
</button>
```

此示例可在以下网址找到：[`codepen.io/AjdinImsirovic/pen/KGVvML`](https://codepen.io/AjdinImsirovic/pen/KGVvML)。

我们可以进一步扩展这个示例，使用`data`属性来存储一组 CSS 类，并使用 JavaScript 三元表达式来检查`btnClicked`值当前是否设置为`true`或`false`：

```js
<!-- HTML -->
<div id="app" class="p-4">
  <h1>Improving dynamic CSS classes example</h1>
  <p class="lead">Click the button below a few times</p>
  <button 
    v-on:click="btnClicked = !btnClicked" 
    v-bind:class="btnClicked ? btnPrimary : btnSecondary">
      btnClicked {{ btnClicked }} 
  </button>
</div>

// JS
new Vue({
  el: '#app',
  data() {
    return {
      btnClicked: false,
      btnPrimary: 'btn btn-lg btn-primary',
      btnSecondary: 'btn btn-lg btn-secondary'
    }
  }
})
```

前一个示例的代码可以在[`codepen.io/AjdinImsirovic/pen/wYMEJQ`](https://codepen.io/AjdinImsirovic/pen/wYMEJQ)找到。

# 使用动态 CSS 类在单击时为按钮添加动画

现在，我们可以通过简单地添加来自上述 Animate.CSS 动画库的额外 CSS 类来添加动画。对于前一个示例代码的更新很少。我们只在这里添加了两个 CSS 类：

```js
      btnPrimary: 'btn btn-lg btn-primary bounce animated',
```

当然，我们还必须包括 Animate.CSS 库，如下所示：[`codepen.io/AjdinImsirovic/pen/RerEyy`](https://codepen.io/AjdinImsirovic/pen/RerEyy)。要在两次点击时添加动画，我们只需将`btnSecondary`的条目更改为：

```js
btnSecondary: 'btn btn-lg btn-secondary tada animated'
```

现在，按钮将在每次点击时都会有动画。

# 使用过渡组

虽然单个过渡组件用于包装单个元素，但过渡组用于为多个元素添加动画。它们带有额外的动画钩子：`v-move`。

在接下来的示例中，我们将构建一个简单的功能，用户可以在线奖励一段内容，类似于[`medium.com/`](https://medium.com/)的鼓掌功能，工作原理如下：如果网站的访问者喜欢一段内容，他们可以通过点击鼓掌按钮最多 50 次来奖励它。因此，鼓掌功能就像是网站访问者对一段内容的欣赏程度的计数器。

在我们的实现中，我们将结合我们已经介绍的功能。不同之处在于，我们将使用`transition-group`组件而不是过渡。这是 HTML 代码：

```js
<!-- HTML -->
<div id="app">
    <div class="tale">
        <transition-group>
          <button 
                  class="bare" 
                  key="howManyClaps" 
                  v-if="clapCount">
                    {{ clapCount }}
          </button>
          <button 
                  class="fa fa-thumbs-o-up animated orange" 
                  key="theClapButton" 
                  v-on:click="aClap()">
          </button>
        </transition-group>
    </div>
</div>
```

以下是 JS 代码：

```js
new Vue({
  el: "#app",
  data: { 
    clapCount: false
  },
  methods: {
    aClap() {
      var target = document.querySelector('.fa-thumbs-o-up');
      if (!target.classList.contains('wobble')) {
        target.classList.add('wobble');
      }
      setTimeout(function() {
        target.classList.remove('wobble')}, 300
      )
      if (this.clapCount < 10) {
        this.clapCount++
      } else {
        target.classList.remove('orange','wobble')
      }
    }
  }
});
```

以下是 CSS 代码：

```js
button.bare {
  font-size: 30px;
  background: white;
  border: none;
  margin: 0 20px;
}
button:focus.bare, button:focus.fa {
  outline: 0;
}
button.fa {
  cursor: pointer;
  color: white;
  padding: 20px;
  border-radius: 50%;
  font-size: 30px;
  border: none;
}
.orange {
  background: orange;
}

/* animation hooks */
.v-enter,
.v-leave-to{
  opacity: 0;
  transform: translate(1000px, 500px);
}
.v-enter-active,
.v-leave-active {
  transition: opacity 5s, transform 1s
}
```

前面的代码可以在此 URL 作为笔记本使用：[`codepen.io/AjdinImsirovic/pen/JmXJgd`](https://codepen.io/AjdinImsirovic/pen/JmXJgd)。

这段代码中发生了几件事情。在 HTML 中，我们使用`transition-group`组件来处理两个按钮。在 JS 中，我们设置了我们掌声行为的逻辑。我们开始将`clapCount`设置为`false`，这将强制转换为零。在 CSS 中，我们为按钮设置样式，并使用动画钩子。`transform`和`transition`的值已经设置为极端值，以便通过调整这些值来更好地理解它们的工作方式（例如，在*X*轴上的平移为`1000 px`，在*Y*轴上的平移为`500 px`）。

# JavaScript 动画钩子

我们可以将 Vue 的`transition`类用作 JavaScript 方法。就像生命周期钩子一样，我们不必访问它们中的任何一个。或者我们可以挑选出我们想要使用的那些。首先，在我们的 Vue 构造函数的`methods`选项中，我们可以指定要对所有这些方法做什么：

```js
  methods: {
    // ENTER transitions...
    beforeEnter: function(el) {},
    enter: function(el, done) {},
    afterEnter: function(el) {},
    enterCancelled: function(el) {},
    // LEAVE transitions...
    beforeLeave: function(el) {},
    leave: function(el,done) {},
    afterLeave: function(el) {},
    leaveCancelled: function(el) {},
  }
```

正如我们所看到的，我们有四种进入过渡的方法，另外还有四种离开过渡的方法。所有的方法都接受`el`参数，而`enter`和`leave`方法还接受`done`参数，表示动画完成。如果没有使用`done`参数，钩子将在不等待`done`回调完成的情况下被调用，过渡将立即完成。

让我们使用这些 JavaScript 动画钩子来重写前面的例子。为了保持易于理解，我们将把官方文档的例子整合到我们的例子中，这样我们就可以看到当动画钩子仅通过 JavaScript 调用时，这个例子是如何工作的。

这是我们将在 HTML 中使用的代码：

```js
<transition 
  v-on:before-enter="beforeEnter"
  v-on:enter="enter"
  v-on:leave="leave"
  v-bind:css="false">
<p v-if="show" style="font-size:25px">Animation example with velocity</p>
</transition>
```

这是我们将在 JS 中使用的代码：

```js
new Vue({
  el: "#app",
  data: { 
    clapCount: false
  },
  methods: {
    beforeEnter: function(el) {
      el.style.opacity = 0
    },
        enter: function (el, done) {
      Velocity(el, { opacity: 1, fontSize: '1.4em' }, { duration: 300 })
      Velocity(el, { fontSize: '1em' }, { complete: done })
    },
    leave: function (el, done) {
      Velocity(el, { translateX: '15px', rotateZ: '50deg' }, { 
      duration: 600 })
      Velocity(el, { rotateZ: '100deg' }, { loop: 2 })
      Velocity(el, {
        rotateZ: '45deg',
        translateY: '30px',
        translateX: '30px',
        opacity: 0
      }, { complete: done })},
    aClap() {
      var target = document.querySelector('.fa-thumbs-o-up');
      if (!target.classList.contains('wobble')) {
        target.classList.add('wobble');
      }
      setTimeout(function() {
        target.classList.remove('wobble')}, 300
      )
      if (this.clapCount < 10) {
        this.clapCount++
      } else {
        target.classList.remove('orange','wobble')
      }
    }
  }
});
```

这是 CSS：

```js
button.bare {
  font-size: 30px;
  background: white;
  border: none;
  margin: 0 20px;
}
button:focus.bare, button:focus.fa {
  outline: 0;
}
button.fa {
  cursor: pointer;
  color: white;
  padding: 20px;
  border-radius: 50%;
  font-size: 30px;
  border: none;
}
.orange {
  background: orange;
}
```

示例在这里：[`codepen.io/AjdinImsirovic/pen/PyzqxM`](https://codepen.io/AjdinImsirovic/pen/PyzqxM)。

通过这种理解，很容易在我们的 Vue 构造函数中更改特定方法中的参数，以实现我们 JavaScript 动画的期望效果。

# 总结

在这一章中，我们讨论了在 Vue.js 中使用过渡和动画。具体来说，我们研究了 CSS 中过渡和动画的工作原理。我们分析了 CSS 中过渡和动画的区别，并建立了它们的规则。我们使用了 Vue 中的过渡和过渡组件，并讨论了动画钩子及其分组进入和离开过渡。我们看到了过渡组件如何命名，并给定了键值，以及如何分配自定义过渡类，以便更轻松地与第三方动画库集成。

我们解释了何时使用过渡模式，以及如何使用`:duration`和`conditional`指令进一步调整我们的动画。我们提到了在 Vue 中绑定 CSS 样式的重要性，以及这种方法如何用于为我们的 Web 应用程序添加动画。最后，我们看到了如何将基于 CSS 类的过渡转换为基于 JavaScript 的动画钩子。

在下一章中，我们将讨论如何使用 Vuex。
