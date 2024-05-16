# 第十章：练习解决方案

# 第一章的练习

在第一章的结尾，有以下练习：

### 注意

我们在前几章中构建的番茄钟毫无疑问非常棒，但仍然缺少一些不错的功能。它可以提供的一个非常好的功能是在休息时间显示来自[`thecatapi.com/`](http://thecatapi.com/)的随机小猫。你能实现这个吗？当然可以！但请不要把休息时间和工作时间搞混了！我几乎可以肯定，如果你盯着小猫而不是工作，你的项目经理是不会喜欢的 :)

让我们解决这个问题。

查看 Pomodoro 的代码[`jsfiddle.net/chudaol/b6vmtzq1/`](https://jsfiddle.net/chudaol/b6vmtzq1/)。

检查[`thecatapi.com/`](http://thecatapi.com/)网站。

让我们首先添加一个带有指向猫 API 的图像的 Bootstrap 元素：

```js
<div id="app" class="container">
  <...>
  **<div class="well">
    <img :src="’ http://thecatapi.com/api/images/get?**
 **type=gif&size=med’" />
  <div>**
</div>

```

如果你打开页面，你会发现图像总是可见的。这不是我们想要的，我们希望它只在我们的 Pomodoro 休息间隔时可见。你已经知道如何做了。有几种方法可以实现这一点；让我们使用类绑定方法，并在状态为工作时绑定一个隐藏的类：

```js
<div class="well" **:class="{ 'hidden': pomodoroState === 'work' }**">
  <img :src="'http://thecatapi.com/api/
    images/get?type=gif&size=med'" />
</div>

```

现在，如果你打开页面，你会发现图像只在工作的 Pomodoro 完成后出现。

然而，问题在于我们休息的所有时间，图像都是一样的。如果我们每隔，比如，10 秒更新一次，那就太好了。

让我们为此目的使用缓存破坏机制。如果我们将一些属性附加到我们的 URL 并每隔 10 秒更改它，URL 将改变，因此我们将获得另一只随机的猫。让我们向我们的 Vue 应用程序添加一个`timestamp`变量，并在`_tick`函数内更改它：

```js
<...>
new Vue({
  el: "#app",
  data: {
    <...>
    **timestamp: 0**
  },
  <...>
  methods: {
    <...>
    _tick: function () {
      //update timestamp that is used in image src
      **if (this.second % 10 === 0) {
        this.timestamp = new Date().getTime();
      }**
      <...>
    }
  }
});

```

时间戳创建和更新后，我们可以在图像源 URL 中使用它：

```js
<div class="well" :class="{ 'hidden': pomodoroState === 'work' }">
  <img :src="**'http://thecatapi.com/api/images/get?
    type=gif&size=med&ts=' + timestamp"** />
</div>
```

就是这样！在这个 JSFiddle 中检查整个代码[`jsfiddle.net/chudaol/4hnbt0pd/2/`](https://jsfiddle.net/chudaol/4hnbt0pd/2/)。

# 第二章的练习

## 增强 MathPlugin

用三角函数（正弦、余弦和正切）增强我们的`MathPlugin`。

实际上，这只是添加缺失的指令并在其中使用`Math`对象的函数。打开`VueMathPlugin.js`并添加以下内容：

```js
//VueMathPlugin.js
export default {
  install: function (Vue) {
    Vue.directive('square', function (el, binding) {
      el.innerHTML = Math.pow(binding.value, 2);
    });
    Vue.directive('sqrt', function (el, binding) {
      el.innerHTML = Math.sqrt(binding.value);
    });
    **Vue.directive('sin', function (el, binding) {
      el.innerHTML = Math.sin(binding.value);
    });
    Vue.directive('cos', function (el, binding) {
      el.innerHTML = Math.cos(binding.value);
    });
    Vue.directive('tan', function (el, binding) {
      el.innerHTML = Math.tan(binding.value);
    });**
  }
};
```

你可以在 HTML 文件中检查这个指令是如何工作的：

```js
//index.html 
<div id="app">
  <input v-model="item"/>
  <hr>
  <div><strong>Square:</strong> <span v-square="item"></span></div>
  <div><strong>Root:</strong> <span v-sqrt="item"></span></div> **<div><strong>Sine:</strong> <span v-sin="item"></span></div>
  <div><strong>Cosine:</strong> <span v-cos="item"></span></div>
  <div><strong>Tangent:</strong> <span v-tan="item"></span></div>**
</div>
```

就是这样！

## 创建 Pomodoro 计时器的 Chrome 应用程序

请结合使用符合 SCP 标准的 Vue.js 版本和我们在第一章中创建的简单番茄钟应用程序的解决方案。检查`chrome-app-pomodoro`文件夹中的代码。

# 第三章练习

## 练习 1

当我们使用简单组件重写购物清单应用程序时，我们失去了应用程序的功能。这个练习建议使用事件发射系统来恢复功能。

在本节中，我们最终得到的代码看起来与[chapter3/vue-shopping-list-simple-components](https://github.com/PacktPublishing/Learning-Vue.js-2/tree/master/chapter3/vue-shopping-list-simple-components)文件夹中的代码类似。

为什么它不起作用？检查开发工具的错误控制台。它显示如下内容：

```js
**[Vue warn]: Property or method "addItem" is not defined on the instance but referenced during render. Make sure to declare reactive data properties in the data option.**
**(found in component <add-item-component>)**

```

啊哈！这是因为在`add-item-template`中，我们调用了不属于这个组件的`addItem`方法。这个方法属于父组件，当然，子组件没有访问权限。我们该怎么办？让我们发出事件！我们已经知道如何做了。所以，我们不需要做太多事情。实际上，我们只需要做三件小事：

+   将`addItem`方法附加到`add-item-component`中，我们将在其中发出一个事件，并将这个组件的`newItem`属性传递给它。

+   修改/简化父组件的`addItem`方法。现在它只需要接收一个文本并将其添加到其`items`属性中。

+   在主标记中，使用`v-on`修饰符和事件的名称将组件的调用绑定到`addItem`方法，每次事件被发出时都会调用它。

让我们首先将`addItem`方法添加到`add-item-component`中。每次点击添加按钮或按下*Enter*键时都会调用它。这个方法应该检查`newItem`属性，如果包含文本，就应该发出一个事件。让我们把这个事件叫做`add`。因此，我们组件的 JavaScript 代码现在应该如下所示：

```js
//add item component
Vue.component('add-item-component', {
  template: '#add-item-template',
  data: function () {
    return {
      newItem: ''
    }
  },
  **methods: {
    addItem: function () {
      var text;

      text = this.newItem.trim();
      if (text) {
        this.$emit('add', this.newItem);
        this.newItem = '';
      }
    }
  }**
});

```

当发出`add`事件时，一定要以某种方式调用主组件的`addItem`方法。让我们通过在`add-item-component`的调用中附加`v-on:add`修饰符来将`add`事件绑定到`addItem`：

```js
<add-item-component **v-on:add="addItem"** :items="items">
</add-item-component>
```

好吧。正如你所看到的，这种方法几乎与主组件的`addItem`方法之前所做的事情相同。它只是不将`newItem`推送到`items`数组中。让我们修改主组件的`addItem`方法，使其只接收已处理的文本并将其推入物品数组中：

```js
new Vue({
  el: '#app',
  data: data,
  methods: { **addItem: function (text) {
      this.items.push({
        text: text,
        checked: false
      });
    }** }
});

```

我们完成了！这个练习的完整解决方案可以在[附录/第三章/vue-shopping-list-simple-components](https://github.com/PacktPublishing/Learning-Vue.js-2/tree/master/Appendix/chapter3/vue-shopping-list-simple-components)文件夹中找到。

## 练习 2

在第三章的*使用单文件组件重写购物清单应用程序*部分，*组件-理解和使用*，我们很好地改变了使用单文件组件的购物清单应用程序，但还有一些事情没有完成。我们有两个缺失的功能：向物品列表添加物品和更改标题。

为了实现第一个功能，我们必须从`AddItemComponent`中发出一个事件，并在主`App.vue`组件中的`add-item-component`调用上附加`v-on`修饰符，就像我们在处理简单组件的情况下所做的那样。你基本上只需复制并粘贴代码。

更改标题功能也是如此。我们还应该发出一个`input`事件，就像我们在简单组件示例中所做的那样。

不要忘记向`App.vue`组件添加样式，使其看起来与以前一样。

在[附录/第三章/shopping-list-single-file-components](https://github.com/PacktPublishing/Learning-Vue.js-2/tree/master/Appendix/chapter3/shopping-list-single-file-components)文件夹中检查完整的代码。

# 总结

在本章中，您学会了如何使我们的应用程序对每个人都可用。您还学会了如何使用 Heroku 与 GitHub 存储库集成部署它们。您还学会了如何在每次提交和推送时自动执行此操作。我们还使用 Travis 在每次部署时进行自动构建。现在我们的应用程序正在进行全面测试，并在每次提交更改时自动重新部署。恭喜！

你可能认为这是旅程的终点。不，不是。这只是开始。在下一章中，我们将看到您可以学到什么，以及您可以用 Pomodoro 和购物清单应用程序做些什么好事。和我在一起！
