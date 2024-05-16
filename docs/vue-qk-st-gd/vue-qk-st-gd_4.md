# 第四章：过滤器和混合器

在本章中，我们将展示如何使用过滤器来更改屏幕上呈现的内容，而不更改基础数据。我们还将介绍混合器，这是一种扩展组件并遵守编程的 DRY 规则的实用方法。

更具体地，在本章中，我们将讨论以下内容：

+   使用过滤器：

+   使用全局和本地过滤器

+   用过滤器替换条件指令

+   链接过滤器

+   使用混合器：

+   避免在混合器方法中重复代码

+   使用数据选项为我们的混合器添加更多功能

+   在混合器中使用生命周期钩子

# 使用过滤器

过滤器只是一个函数。它接受一些数据（作为参数传递给过滤器函数），并对该数据执行一些简单的操作。执行的操作的结果从过滤器函数返回，并显示在应用程序中的适当位置。重要的是要注意，过滤器不会影响基础数据；它们只会影响数据在屏幕上的显示方式。

就像组件一样，过滤器也可以注册为全局或本地。注册全局过滤器的语法如下：

```js
Vue.filter('justAnotherFilter', function(someData1, someData2, someDataN) {
  // the filter function definition goes here (it takes N number of arguments)
});
```

除了全局注册，我们还可以像这样在本地注册过滤器：

```js
filters: {
  justAnotherFilter(someData1, someData2, someDataN) {
    // the filter function is defined here...
  }
}
```

正如我们在这里所看到的，在本地注册的情况下，过滤器被添加为 Vue 组件的选项。

# 一个将学生成绩四舍五入的过滤器示例

假设我们有一个朋友是教授，他们需要一些帮助来处理他们学生的测试。学生参加的测试总是以小数形式产生分数。学生可以在该测试中获得的分数范围在 0 到 100 之间。

作为我们的好朋友，我们将制作一个简单的 Vue 应用程序，其中包含一个将小数分数四舍五入为整数的过滤器。我们还会偏向于学生，这意味着我们将始终将结果四舍五入。

此示例的代码可在[`codepen.io/AjdinImsirovic/pen/MqBNBR`](https://codepen.io/AjdinImsirovic/pen/MqBNBR)上找到。

我们的过滤器函数将非常简单：它将接受一个浮点数，并根据接收到的浮点数返回一个四舍五入的整数。过滤器函数将被称为`pointsRoundedUp`，并且看起来像这样：

```js
  filters: {
    pointsRoundedUp(points){
      return Math.ceil(parseFloat(points));
    }
  }
```

因此，我们的`pointsRoundedUp`函数接受来自应用程序`data()`函数的`points`实例，并使用 JavaScript 的内置`parseFloat()`和`Math.ceil()`函数调用`points`值返回这些`points`实例。

在我们的 HTML 中使用过滤器时，我们采用以下语法：

```js
{{ points| pointsRoundedUp }}
```

`points`值是应用程序中存储的实际数据。`pointsRoundedUp`是我们用来格式化从 Vue 组件的数据选项接收到的数据的过滤器。

一般来说，我们可以说所有过滤器的基本逻辑如下：

```js
{{ data | formattedData }}
```

这个一般原则可以这样阅读：为了格式化返回的数据，我们用管道符号(`|`)跟着调用特定的过滤器。

让我们检查一下我们应用程序的完整代码。HTML 将如下所示：

```js
<div id="app">
 <h1>A simple grade-rounding Vue app</h1>
 <p>Points from test: {{ points }}</p>
 <p>Rounded points are: {{ points | pointsRoundedUp }}</p>
</div>
```

JS 也将很简单：

```js
new Vue({
  el: "#app",
  data() {
    return {
      points: 74.44
    }
  },
  filters: {
    pointsRoundedUp(points){
      return Math.ceil(parseFloat(points));
    }
  }
});
```

应用程序将在屏幕上输出以下内容：

```js
A simple grade-rounding Vue app
Points from test: 74.44
Rounded points are: 75
```

应用程序现在已经完成。

然而，过了一段时间，我们的朋友又向我们请求另一个帮助：根据分数计算学生的等级。最初，我们意识到这只是一个小小的计算，我们可以简单地将其放入条件指令中。

更新示例的代码可以在这里找到：[`codepen.io/AjdinImsirovic/pen/XPPrEN`](https://codepen.io/AjdinImsirovic/pen/XPPrEN)。

基本上，我们在这个新示例中所做的是用几个条件指令扩展了我们的 HTML。虽然这解决了问题，但我们混乱了我们的 HTML，而我们的 JS 保持不变。更新后的 HTML 代码如下：

```js
<div id="app">
  <h1>A simple grade-rounding Vue app</h1>
  <p>Points from test: {{ points }}</p>
  <p>Rounded points are: {{ points | pointsRoundedUp }}</p>
  <p v-if="points > 90">Final grade: A</p>
  <p v-else-if="points > 80 && points <= 90">Final grade: B</p>
  <p v-else-if="points > 70 && points <= 80">Final grade: C</p>
  <p v-else-if="points > 60 && points <= 70">Final grade: D</p>
  <p v-else-if="points > 50 && points <= 86">Final grade: E</p>
  <p v-else="points <= 50">Final grade: F</p>
</div>
```

我们的问题得到了解决。这次测试的分数是 94.44，应用程序成功地将以下信息打印到屏幕上：

```js
A simple grade-rounding Vue app
Points from test: 94.44
Rounded points are: 95
Final grade: A
```

然而，我们意识到我们的 HTML 现在很混乱。幸运的是，我们可以利用过滤器使事情变得不那么混乱。

# 使用过滤器替换条件指令

在本节中，我们将使用过滤器返回学生的适当等级。

更新后的应用程序代码可以在这里找到：[`codepen.io/AjdinImsirovic/pen/LJJPKm`](https://codepen.io/AjdinImsirovic/pen/LJJPKm)。

我们对该应用程序的 HTML 所做的更改如下：

```js
<div id="app">
  <h1>A simple grade-rounding Vue app</h1>
  <p>Points from test: {{ points }}</p>
  <p>Rounded points are: {{ points | pointsRoundedUp }}</p>
  <p>Final grade: {{ points | pointsToGrade }}</p>
</div>
```

我们将条件功能移到了我们的 JavaScript 中，即一个名为`pointsToGrade`的新过滤器中：

```js
new Vue({
  el: "#app",
  data() {
    return {
      points: 84.44
    }
  },
  filters: {
    pointsRoundedUp(points){
      return Math.ceil(parseFloat(points));
    },
    pointsToGrade(points){
      if(points>90) {
        return "A"
      } else if(points>80 && points<=90) {
        return "B"
      } else if(points>70 && points<=80) {
        return "C"
      } else if(points>60 && points<=70) {
        return "D"
      } else if(points>50 && points<=60) {
        return "E"
      } else {
        return "F"
      }
    }
  }
});
```

作为对我们更新的代码进行快速测试，我们还将点数更改为 84.44，成功地从`pointsToGrade`过滤器返回了 B 等级。

然而，不完全出乎意料的是，我们的朋友再次回来，并要求我们再次扩展应用程序。这一次，我们需要再次显示我们学生的姓名的正确格式，格式如下：

```js
Last name, First name, year of study, grade.
```

这意味着我们将不得不扩展我们的应用程序以获得额外的功能。幸运的是，这不难，因为我们可以利用过滤器的另一个很好的特性：链接。

# 在 Vue 中链接过滤器

我们应用的要求已经更新，现在我们需要在屏幕上显示一些额外的、格式良好的数据。

由于要求已经改变，我们还需要更新数据。

本节的代码可在此处找到：[`codepen.io/AjdinImsirovic/pen/BOOazy`](https://codepen.io/AjdinImsirovic/pen/BOOazy)。

这是更新后的 JavaScript。首先，我们将添加`el`和`data`选项：

```js
new Vue({
  el: "#app",
  data() {
    return {
      firstName: "JANE",
      lastName: "DOE",
      yearOfStudy: 1, 
      points: 84.44,
      additionalPoints: 8
    }
  },
```

在 JS 中，我们将添加过滤器：

```js
  filters: {
    pointsRoundedUp(points){
      return Math.ceil(parseFloat(points));
    },
    pointsToGrade(points){
      if(points>90) {
        return "A"
      }
      else if(points>80 && points<=90) {
        return "B"
      }
      else if(points>70 && points<=80) {
        return "C"
      }
      else if(points>60 && points<=70) {
        return "D"
      }
      else if(points>50 && points<=60) {
        return "E"
      }
      else {
        return "F"
      }
    },
    yearNumberToWord(yearOfStudy){
      // freshman 1, sophomore 2, junior 3, senior 4 
      if(yearOfStudy==1) {
        return "freshman"
      } else if(yearOfStudy==2){
        return "sophomore"
      } else if(yearOfStudy==3){
        return "junior"
      } else if(yearOfStudy==4){
        return "senior"
      } else {
        return "unknown"
      }
    },
    firstAndLastName(firstName, lastName){
      return lastName + ", " + firstName
    },
    toLowerCase(value){
      return value.toLowerCase()
    },
    capitalizeFirstLetter(string) {
        return string.charAt(0).toUpperCase() + string.slice(1);
    }
  }
});
```

更新后的 HTML 如下所示：

```js
<div id="app">
  <h1>A simple grade-rounding Vue app</h1>
  <p>Points from test: {{ points }}</p>
  <p>Rounded points are: {{ points | pointsRoundedUp }}</p>
  <p>Student info: 
  <!--
  <p>Name: {{ firstName, lastName | firstAndLastName | toLowerCase | capitalizeFirstLetter}}</p>
  -->
  <p>
    Name: 
    {{ lastName | toLowerCase | capitalizeFirstLetter }}, 
    {{ firstName | toLowerCase | capitalizeFirstLetter }}
  </p>
  <p>Year of study: {{ yearOfStudy | yearNumberToWord }}</p>
  <p>Final grade: <strong>{{ points | pointsToGrade }}</strong></p>
</div>
```

通过这些链接的过滤器，我们通过两个过滤器`toLowerCase`和`capitalizeFirstLetter`正确格式化了学生的姓名，通过这两个过滤器将数据（以全大写形式出现）传递过去。

我们还可以看到一个被注释掉的段落，显示了一个不成功的方法，它只将姓氏的第一个字母大写，而不是名字的第一个字母。原因是`firstAndLastName`过滤器，当应用时，将全名组合成一个字符串。

请注意，过滤器不会被缓存，这意味着它们将始终被运行，就像方法一样。

有关过滤器的更多信息，请参阅官方文档：[`vuejs.org/v2/guide/filters.html`](https://vuejs.org/v2/guide/filters.html)。

# 使用混合

混合是我们在 Vue 代码中抽象出可重用功能的一种方式。在前端世界中，由 Sass 广泛使用，混合的概念现在出现在许多现代 JavaScript 框架中。

当我们有一些功能希望在多个组件中重用时，最好使用混合。在接下来的例子中，我们将创建一个非常简单的 Vue 应用程序，在页面上显示两个 Bootstrap 警报。当用户点击其中任何一个警报时，浏览器的视口尺寸将被记录到控制台中。

为了使这个例子工作，我们需要从 Bootstrap 框架中获取一些普通的 HTML 组件。具体来说，我们将使用警报组件。

关于这个 Bootstrap 组件的官方文档可以在这个链接找到：[`getbootstrap.com/docs/4.1/components/alerts/`](https://getbootstrap.com/docs/4.1/components/alerts/)。

重要的是要注意，Bootstrap 组件和 Vue 组件是不同的东西，不应混淆。

运行应用程序时，将产生以下结果：

![](img/e09d84d6-6887-4b67-b0b4-470f14216b0f.png)

这个例子的代码可以在这里找到：[`codepen.io/AjdinImsirovic/pen/jvvybq`](https://codepen.io/AjdinImsirovic/pen/jvvybq)。

# 在不同组件中构建具有重复功能的简单应用程序

首先，让我们构建我们简单的 HTML：

```js
<div id="app">
  <div class="container mt-4">
    <h1>{{heading}}</h1>
    <primary-alert></primary-alert>
    <warning-alert></warning-alert>
  </div>
</div>
```

我们正在使用 Bootstrap 的`container`和`mt-4`的 CSS 类。常规的 HTML `h1`标签也获得了一些特定于 Bootstrap 的样式。我们还在前面的代码中使用了两个 Vue 组件：`primary-alert`和`warning-alert`。

在我们的 JavaScript 代码中，我们将这两个组件定义为`primaryAlert`和`warningAlert`，然后在它们的父组件的`components`选项中列出它们：

```js
const primaryAlert = {
  template: `
    <div class="alert alert-primary" role="alert" v-on:click="viewportSizeOnClick">
      A simple primary alert—check it out!
    </div>`,
    methods: {
    viewportSizeOnClick(){
      const width = window.innerWidth;
      const height = window.innerHeight;
      console.log("Viewport width:", width, "px, viewport height:", height, "px");
    }
  }
}
const warningAlert = {
  template: `
    <div class="alert alert-warning" role="alert" v-on:click="viewportSizeOnClick">
      A simple warning alert—check it out!
    </div>`,
    methods: {
    viewportSizeOnClick(){
      const width = window.innerWidth;
      const height = window.innerHeight; 
      console.log("Viewport width:", width, "px, viewport height:", height, "px");
    }
  }
}
```

现在，仍然在 JS 中，我们可以指定构造函数：

```js
new Vue({
  el: '#app',
  data() {
    return {
      heading: 'Extracting reusable functionality into mixins in Vue'
    }
  },
  components: {
    primaryAlert: primaryAlert,
    warningAlert: warningAlert
  }
})
```

要查看这个小应用程序的结果，请打开控制台并单击两个警报组件中的任何一个。控制台输出将类似于以下内容：

```js
Viewport width: 930 px, viewport height: 969 px
```

正如我们在 JavaScript 代码中所看到的，我们还定义了一个`viewportSizeOnClick`

方法在`primaryAlert`和`warningAlert`组件的`methods`选项内。这种功能上的不必要重复是一个完美的抽象成 mixin 的候选，接下来我们将这样做。

# 使用 mixin 保持 DRY

改进后的应用程序的代码可以在这里找到：[`codepen.io/AjdinImsirovic/pen/NLLgWP`](https://codepen.io/AjdinImsirovic/pen/NLLgWP)。

在这个例子中，虽然我们的 HTML 保持完全相同，但更新后的 JavaScript 代码将如下所示：

```js
const viewportSize = {
    methods: {
      viewportSizeOnClick(){
        const width = window.innerWidth;
        const height = window.innerHeight;
        console.log("Viewport width:", width, "px, viewport height:", height, "px");
      }
  } 
}
const primaryAlert = {
  template: `
    <div class="alert alert-primary" role="alert" v-on:click="viewportSizeOnClick">
      A simple primary alert—check it out!
    </div>`,
  mixins: [viewportSize]
}
const warningAlert = {
  template: `
    <div class="alert alert-warning" role="alert" v-on:mouseenter="viewportSizeOnClick">
      A simple warning alert—check it out!
    </div>`,
  mixins: [viewportSize]
}
new Vue({
  el: '#app',
  data() {
    return {
      heading: 'Extracting reusable functionality into mixins in Vue'
    }
  },
  components: {
    primaryAlert: primaryAlert,
    warningAlert: warningAlert
  }
})
```

正如在这里所看到的，我们已经从两个组件中删除了`methods`选项，并添加了一个名为`viewportSize`的新对象。在这个对象内部，我们已经移动了共享的`methods`选项：

```js
const viewportSize = {
    methods: {
      viewportSizeOnClick(){
        const width = window.innerWidth;
        const height = window.innerHeight;
        console.log("Viewport width:", width, "px, viewport height:", height, "px");
      }
  } 
}
```

`methods`选项只包含`viewportSizeOnClick`函数。

顺便说一句，`vieportSizeOnClick`方法的名称有点误导。如果你仔细看第二个组件（`warningAlert`组件）的代码，你会注意到我们更新了指令，所以它使用的是`v-on:mouseenter`，而不是`v-on:click`。这意味着方法的名称需要更改为更合适的名称。因此，我们将把方法重命名为`logOutViewportSize`。

另外，让我们想象一下，我们希望以另一种方式显示视口信息。例如，我们可能会在警报框中显示它，而不是将其记录到控制台中。这就是为什么我们将引入另一种方法`alertViewportSize`。

随着所有这些小改变的积累，现在是时候看到我们小应用的另一个更新版本了。新的代码可以在以下网址找到：[`codepen.io/AjdinImsirovic/pen/aaawJY`](https://codepen.io/AjdinImsirovic/pen/aaawJY)。

与以前的更新类似，更新后的示例只有对 JS 进行了更改，如下所示。我们从`viewportSize`开始：

```js
const viewportSize = {
    methods: {
      logOutViewportSize(){
        const width = window.innerWidth;
        const height = window.innerHeight; 
        console.log("Viewport width:", width, "px, viewport height:", height, "px");
      },
      alertViewPortSize() {
        const width = window.innerWidth;
        const height = window.innerHeight;
        alert("Viewport width: " + width + " px, viewport height: " + height + " px");
      }
  }
}
```

接下来，我们将设置警报：

```js
const primaryAlert = {
  template: `
    <div class="alert alert-primary" role="alert" v-on:click="alertViewPortSize">
      A simple primary alert—check it out!
    </div>`,
  mixins: [viewportSize]
}
const warningAlert = {
  template: `
    <div class="alert alert-warning" role="alert" v-on:mouseenter="logOutViewportSize">
      A simple warning alert—check it out!
    </div>`,
  mixins: [viewportSize]
}
```

最后，让我们用指定 Vue 构造函数来结束：

```js
new Vue({
  el: '#app',
  data() {
    return {
      heading: 'Extracting reusable functionality into mixins in Vue'
    }
  },
  components: {
    primaryAlert: primaryAlert,
    warningAlert: warningAlert
  }
})
```

在下一节中，我们将看看如何通过重构来进一步改进我们的混合方法。

# 重构我们的 viewportSize 混合方法

在本节中，我们将探讨进一步改进我们的混合方法。虽然我们的代码既可读又易于理解，但在`const`声明中存在一些代码重复。此外，我们将利用这个机会来探讨混合重构的方法。更新后的代码将包括一些基本的事件处理。

有关可用事件的列表，请参阅此链接：[`developer.mozilla.org/en-US/docs/Web/Events`](https://developer.mozilla.org/en-US/docs/Web/Events)。

由于我们还将使用 JavaScript 的内置`addEventListener()`方法，因此也很有必要在 MDN 上获取有关它的更多信息，网址如下：[`developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener`](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener)。

在我们开始重构之前，我们将利用混合的能力来插入 Vue 的生命周期功能（就像组件一样）。此外，在我们的混合的这个迭代中，我们除了`methods`之外还引入了另一个选项。我们使用的选项是`data`。为了避免在混合的`methods`选项中重复`const`声明，我们将要处理的值存储在`data`选项中。

虽然 HTML 仍然保持不变，但我们的 JavaScript 文件将会有很大的不同。让我们从设置数据开始：

```js
const viewportSize = {
    data(){
      return {
        viewport: {
          width: 0,
          height: 0
        }
      }
    },
```

接下来，我们将添加方法，即`getViewportSize`、`logOutViewportSize`和`alertViewportSize`：

```js
    methods: {
      measureViewportSize(){
        this.viewport.width = window.innerWidth;
        this.viewport.height = window.innerHeight;
      },
      logOutViewportSize(){
        console.log("Viewport width:", this.viewport.width, "px, viewport height:", this.viewport.height, "px");
      },
      alertViewPortSize() {
        alert("Viewport width: " + this.viewport.width + " px, viewport height: " + this.viewport.height + " px");
      }
  },
```

接下来，让我们添加`created`：

```js
created() {
    this.listener =
      window.addEventListener('mousemove',this.measureViewportSize); 
    this.measureViewportSize();
 }
}
```

现在，我们可以设置`primaryAlert`：

```js
const primaryAlert = {
  template: `
    <div class="alert alert-primary" role="alert" v-on:click="alertViewPortSize">
      A simple primary alert—check it out!
    </div>`,
  mixins: [viewportSize]
}
```

我们将继续添加`warningAlert`：

```js
const warningAlert = {
  template: `
    <div class="alert alert-warning" role="alert" v-on:mouseenter="logOutViewportSize">
      A simple warning alert—check it out!
    </div>`,
  mixins: [viewportSize]
}
```

最后，让我们添加 Vue 构造函数：

```js
new Vue({
  el: '#app',
  data() {
    return {
      heading: 'Extracting reusable functionality into mixins in Vue'
    }
  },
  components: {
    primaryAlert: primaryAlert,
    warningAlert: warningAlert
  }
})
```

本节的代码可以在以下代码 pen 中找到：[`codepen.io/AjdinImsirovic/pen/oPPGLW`](https://codepen.io/AjdinImsirovic/pen/oPPGLW)。

在我们重构的混合中，我们有`data`、`methods`和`created`这些选项。`created`函数是一个生命周期钩子，我们使用这个钩子来监听`mousemove`事件。当发生这样的事件时，我们运行我们的混合的`this.getViewportSize`方法，该方法更新视口尺寸，然后将其记录下来或显示在警报框中。

永远不要使用全局混合！全局混合会影响应用程序中的所有组件。这种情况并不多见，所以通常最好避免使用全局混合。

通过这个，我们结束了对 Vue 中混合的简要讨论。有关此主题的更多信息，请访问此官方链接：

[`vuejs.org/v2/guide/mixins.html`](https://vuejs.org/v2/guide/mixins.html)

# 总结

在本章中，我们讨论了 Vue 中的过滤器和混合。我们讨论了在何种情况下使用过滤器是有意义的，并且我们看了一下如何使用全局和本地过滤器。我们还讨论了过滤器如何用来替换条件指令，并且我们研究了如何将过滤器串联在一起。

我们还探讨了如何通过将功能从组件移动到混合中来抽象可重用功能，并且我们看了一下如何避免在混合本身内部重复代码。我们用一个在混合中使用生命周期钩子的例子来结束了这一部分。

在下一章中，我们将看看如何构建我们自己的自定义指令。
