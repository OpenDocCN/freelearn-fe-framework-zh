# 第五章：制作您自己的指令和插件

在这一章中，我们将看看扩展 Vue 的方法。首先，我们将编写自己的指令并看看如何使用它们。接下来，我们将制作一个自定义的 Vue 插件。

更具体地，在这一章中，我们将研究以下内容：

+   自定义指令的结构以及如何制作它们

+   使用全局和本地自定义指令

+   向自定义指令传递值

+   编写 Vue 插件

+   发布 Vue 插件

# 制作我们自己的指令

在 Vue 2 中，组件是使用的主要策略，无论是保持 DRY 还是抽象化一些功能。然而，你可以采取的另一种方法是利用自定义指令。

# 理解自定义指令

正如我们在本书中之前讨论过的，指令帮助我们解释给 Vue 什么样的行为我们想要附加到一段标记上。正如我们之前看到的，Vue 内置了许多指令。一些例子是`v-on`，`v-if`，`v-model`等等。简单回顾一下，指令是以`v-`开头的 HTML 属性。

当我们需要构建一个自定义指令时，我们只需在连字符后面提供一个自定义单词。例如，我们可以创建一个自定义指令，我们将其称为`v-custom-directive`，然后我们可以在我们的标记中使用这个名称，例如，像这样：

```js
<div id="app">
  <div v-custom-directive>{{ something }}</div>
<!-- etc -->
```

请注意，拥有一个没有值的指令是完全正常的，就像为它提供一个值一样，就像这样：

```js
<div id="app">
  <div v-custom-directive="aValue">{{ something }}</div>
<!-- etc -->
```

接下来，在我们的 JS 代码中，我们需要注册这个指令，如下所示：

```js
Vue.directive('customDirective', {
  // details for the directive go here
}
```

因此，正如我们所看到的，提供给`Vue.directive`的第一个参数是我们自定义指令的名称。请注意，Vue 在 HTML 中使用 kebab-case，而在 JS 中使用 lowerCamelCase 的约定也适用于自定义指令。

提供给我们自定义指令的第二个参数是一个包含所有指令功能的对象。

正如你现在可能推断的那样，前面的代码给出了全局注册指令的一个例子。如果你想要本地注册一个指令，你需要为特定组件指定一个`directives`选项。

例如，我们可以注册一个本地组件如下：

```js
directives: {
  directiveName: {
    // some code to describe functionality
  }
}
```

就像组件一样，指令也使用钩子，这允许我们控制它们的功能何时被调用。有五个指令钩子：`bind`，`inserted`，`update`，`componentUpdated`和`unbind`。

有关某些这些钩子可以接受的参数的完整列表，您可以参考[`vuejs.org/v2/guide/custom-directive.html#Directive-Hook-Arguments`](https://vuejs.org/v2/guide/custom-directive.html#Directive-Hook-Arguments)。

# 构建一个简单的自定义指令

此示例的完整代码在此处可用：[`codepen.io/AjdinImsirovic/pen/yxWObV`](https://codepen.io/AjdinImsirovic/pen/yxWObV)。

在我们的 HTML 中，我们将添加以下简单代码：

```js
<div id="app" class="container mt-5">
  <h1 class="h2">{{ heading }}</h1>
  <div v-custom-directive>
    Just some text here
  </div>
</div>
```

在我们的 JS 中，我们将全局添加我们的`customDirective`：

```js
Vue.directive('customDirective', {
  inserted: function(el) {
    el.style.cssText = `
      color: blue; 
      border: 1px solid black; 
      background: gray; 
      padding: 20px; 
      width: 50%;
    `
  }
});

new Vue({
  el: '#app',
  data() {
    return {
      heading: 'A custom global directive'
    }
  }
});
```

在先前的代码中，我们使用了`inserted`指令钩子。使用此钩子，当将指令绑定到的元素*插入*到其父节点中时，将运行指令的代码。

当这种情况发生时，该元素将根据我们分配给`el.style.cssText`的值进行样式设置。

当然，没有什么能阻止我们在一个元素上使用多个自定义指令。例如，我们可以指定几个自定义指令，然后根据需要混合和匹配它们。

在下一节中，我们将重写全局自定义指令为本地自定义指令。

# 使用本地指令

现在让我们看看如何重写先前的代码，使我们的指令使用本地指令而不是全局指令。

在本节中，我们将构建一个非常简单的自定义指令。我们将使用第四章中的一个示例，*过滤器和混合*，并在此基础上构建，以便我们可以轻松地将其与先前的示例进行比较，只是这次使用一个简单的本地自定义指令。

此示例的代码在此处可用：[`codepen.io/AjdinImsirovic/pen/yxWJNp`](https://codepen.io/AjdinImsirovic/pen/yxWJNp)。

在我们的 HTML 中，我们将指定以下代码：

```js
<main id="app">
    <custom-article v-custom-directive></custom-article>
    <custom-article></custom-article>
    <another-custom-article v-another-custom></another-custom-article>
</main>
```

在我们的 JS 中，我们将指定以下代码：

```js
const anotherCustom = {
  inserted: function(el) {
    el.style.cssText = `
      color: green; 
      border: 1px solid black; 
      background: yellow; 
      padding: 20px; 
      width: 50%;
    ` 
  }
}

const customArticle = {
  template: `
    <article>
      Our own custom article component!
    </article>`
}

Vue.component('another-custom-article', {
  template: `
    <article>
      Another custom article component! 
      This one has it's own child component too!
      Here it is:
      <custom-article v-custom-directive></custom-article>
    </article>`,
    components: {
      'customArticle': customArticle
    },
    directives: {
      customDirective: {
        inserted: function(el) {
          el.style.cssText = `
            color: blue; 
            border: 1px solid black; 
            background: gray; 
            padding: 20px; 
            width: 50%;
          ` 
      }
    } 
  } 
})

new Vue({
    el: '#app',
    components: { 
      'customArticle': customArticle,
    },
    directives: {
      customDirective: {
        inserted: function(el) {
          el.style.cssText = `
            color: blue; 
            border: 1px solid black; 
            background: gray; 
            padding: 20px; 
            width: 50%;
          ` 
       }
     },
     'anotherCustom': anotherCustom
  }
})
```

在下一节中，我们将看到如何将值传递给自定义指令。

# 将值传递给自定义指令

我们将通过允许我们的自定义指令接收参数来改进本章的初始示例。此示例的代码在此笔中可用：[`codepen.io/AjdinImsirovic/pen/xaNgPN`](https://codepen.io/AjdinImsirovic/pen/xaNgPN)。

这是我们示例中将值传递给自定义指令的 HTML：

```js
<div id="app" class="container mt-5">
  <h1 class="h2">{{ heading }}</h1>
  <button v-buttonize="tomato">
    Just some text here
  </button>
  <button v-buttonize="lightgoldenrod">
    Just some text here
  </button>
  <button v-buttonize="potato">
    Just some text here
  </button> 
</div>
```

以下是 JavaScript 代码：

```js
Vue.directive('buttonize', {
  bind(el, binding) {
    var exp = binding.expression;
    el.style.cssText += `
      padding: 10px 20px; 
      border: none; 
      border-radius: 3px; 
      cursor: pointer
    `;
    switch(exp) {
      case 'tomato':
          el.style.cssText += `
            background: tomato;
            color: white;
          `;
          break;
       case 'lightgoldenrod':
          el.style.cssText += `
            background: darkgoldenrod;
            color: lightgoldenrod;
          `;
          break;
        default:
            el.style.cssText += `
              background: gray;
              color: white;
            `
    }
  }
});
```

最后，在 JS 中，我们添加带有`options`对象的 Vue 构造函数：

```js
new Vue({
  el: '#app',
  data() {
    return {
      heading: 'A custom global directive'
    }
  }
});
```

请注意，指令钩子参数的具体设置可以在[`vuejs.org/v2/guide/custom-directive.html#Directive-Hook-Arguments`](https://vuejs.org/v2/guide/custom-directive.html#Directive-Hook-Arguments)找到。对我们最感兴趣的一个参数是`binding`，它是一个带有这些属性的对象：`name`、`value`、`oldValue`、`expression`、`arg`和`modifiers`。

在上面的代码中，我们看到了传递两个不同值的示例，这些值根据传递的值给出了不同的结果。我们还看到了当我们传递一个无意义的值（利用`switch`语句的`default`分支）时会发生什么。

在下一节中，我们将讨论通过构建 Vue 插件来进一步扩展 Vue 功能的方法。

# 使用 Vue 插件。

一些流行的 Vue 插件是 Vuex 和 Vue-router。当我们需要为 Vue 全局提供额外的功能时，就会使用 Vue 插件。有一些非常常见的情况下，Vue 插件可能会有用：添加全局方法，添加全局资产，添加`Vue.prototype`上的实例方法，或者添加全局混合。

Vue 插件的亮点在于能够与社区共享。要了解 Vue 插件系统的广泛性，可以导航到以下网址：[`github.com/vuejs/awesome-vue#components--libraries`](https://github.com/vuejs/awesome-vue#components--libraries)和[`vuejsexamples.com/`](https://vuejsexamples.com/)。

接下来，我们将创建一个简单的 Vue 插件。

# 创建最简单的 Vue 插件

我们将从创建最简单的 Vue 插件开始。为了做到这一点，我们将再次使用 Vue CLI，版本 3。设置 Vue CLI 的说明在第三章中可用，*使用 Vue-CLI、组件、Props 和 Slots*。

首先，我们需要初始化一个新项目。将控制台导航到要创建新 Vue 项目的父文件夹，并运行以下命令：

```js
vue create simple-plugin
cd simple-plugin
npm run-serve
```

当我们运行这三个命令中的第一个时，会有一些问题，之后会运行大量的软件包。这可能需要一些时间——一个休息的好机会。一旦完成，并且我们已经运行了前面列出的另外两个命令，我们的样板 Vue 应用将在`localhost:8080`上可用。

首先，让我们在`src`文件夹内创建一个新的文件夹，并将其命名为`plugins`。接下来，在`plugins`文件夹内，让我们创建另一个文件夹，我们将其称为`SimplePlugin`。在`SimplePlugin`文件夹内，让我们创建一个新文件，并将其命名为`index.js`。

Vue 插件是一个对象。为了让我们的 Vue 应用程序可以访问插件对象，我们需要通过导出来使其可用。因此，让我们将这个导出代码添加到我们的`index.js`文件中：

```js
export default {

}
```

Vue 的插件对象有一个`install`方法。`install`方法接受两个参数。第一个参数是`Vue`对象，第二个参数是`options`对象。因此，我们将在`plugin`对象内添加`install`方法：

```js
export default {
    install(Vue, options) {
        alert('This is a simple plugin and currently the options argument is ' + options);
    }
}
```

目前，在我们的`install`方法内，我们只是向浏览器发出警报消息。这是我们的插件可以具有的功能的绝对最小值。有了这个功能，现在是时候在我们的应用程序中使用我们的插件了。

请注意，我们还将`options`参数连接到我们的警报消息中。如果我们不这样做，我们的 Vue-cli 将抛出一个错误，指出<q>options 已定义但从未使用</q>。显然，它更青睐于<q>(no-unused-vars)</q>的情况。

要使用插件，我们需要打开我们的`main.js`文件，并通过在`main.js`文件的第三行添加这两行代码来导入插件：

```js
import SimplePlugin from './plugins/SimplePlugin'
Vue.use(SimplePlugin)
```

首先，我们导入插件并指定导入路径。接下来，我们将我们的插件作为参数添加到`Vue.use`方法中。

有了这个，我们已经成功地创建了最简单的插件。打开本地项目`localhost:8080`，你将看到一个警报消息，其中说明：

```js
This is the simplest possible Vue plugin and currently the options argument is undefined
```

接下来，我们将看到如何将选项对象添加到我们的插件中。

# 创建带有定义选项的插件

由于我们设置项目的方式，我们将保持`SimplePlugin`不变，在 Vue 插件探索的这一部分，我们将在项目的`plugins`文件夹内添加另一个文件夹。我们将称这个文件夹为`OptionsPlugin`，并在其中再次创建一个`index.js`文件。

接下来，让我们更新`main.js`文件，使其看起来像这样：

```js
import Vue from 'vue'
import App from './App.vue'

//import SimplePlugin from './plugins/SimplePlugin'
import OptionsPlugin from './plugins/OptionsPlugin'

//Vue.use(SimplestPlugin)
Vue.use(OptionsPlugin)

Vue.config.productionTip = false

new Vue({
  render: h => h(App)
}).$mount('#app')
```

现在，回到`OptionsPlugin/index.js`，我们将添加以下代码：

```js
export default {
  install(Vue) {
    Vue.directive('text-length', {
        bind(el, binding, vnode) {
            const textLength = el.innerText.length;
            console.log("This element, " + el.nodeName + ", has text with " + textLength + " characters");

            el.style.cssText = "border: 2px solid tomato";
        }
    })
  }
}
```

请注意，我们在`install`方法中完全省略了`options`对象。原因很简单：`options`对象是可选的，不提供它不会破坏我们的代码。

在先前的插件定义中，我们获取了`el.innerText`字符串的长度，然后将其记录到控制台中。此外，应用了我们插件自定义的`v-text-length`指令的`el`也将通过红色边框更加显眼。

接下来，让我们在组件的模板中使用来自我们的插件的功能。具体来说，我们将在`src/components`文件夹中的`HelloWorld.vue`文件的开头使用它：

```js
<template>
  <div class="hello">
    <h1 v-text-length>{{ msg }}</h1>
```

此时在浏览器中运行我们的应用程序将在控制台中产生以下消息：

```js
This element, H1, has text with 26 characters
```

现在，我们可以引入我们的`options`对象。`options`对象的目的是允许我们自定义受`v-text-length`指令影响的 HTML 元素的显示方式。换句话说，我们可以决定让我们的插件的用户根据我们传递的选项来选择不同类型的样式。

因此，让我们使用以下代码更新我们的插件：

```js
const OptionsPlugin = { 
  install(Vue, options) {
    Vue.directive('text-length', {
        bind(el, binding, vnode) {
            const textLength = el.innerText.length;
            console.log("This element, " + el.nodeName + ", has text with " + textLength + " characters");

      if (textLength < 40) {
        el.style.cssText += "border:" + options.selectedOption.plum;
      } else if (textLength >= 40) {
        el.style.cssText += "border:" + options.selectedOption.orange;
      }
        }
    })
  }
};

export default OptionsPlugin;
```

在上述代码中发生了一些事情。首先，我们正在即时创建一个对象，并将其分配给`const OptionsPlugin`。在文件底部，我们正在导出我们刚刚定义的`OptionsPlugin`。

在`optionsPlugin`对象内部，我们使用了一些 if 语句，根据`el`元素的文本节点中找到的文本长度来提供不同的样式。如果文本长度小于 40 个字符，那么我们将为`options.selectedOption`赋值

将`.plum`赋给`border` CSS 属性。

否则，如果文本长度等于或大于 40 个字符，我们将在问题元素的内联`style`属性中的`border` CSS 属性赋值为`options.selectedOption.orange`的值。

接下来，让我们设置这些选项值。我们将在我们的`main.js`文件中进行。我们将更新使用插件的部分为以下代码：

```js
Vue.use(OptionsPlugin, {
  selectedOption: {
    plum: "5px dashed purple",
    orange: "10px double orange"
  }
})
```

最后，在`HelloWorld.vue`文件中，我们只进行了轻微的更新。我们将插件定义的指令添加到紧随`h1`标签之后的`p`标签中：

```js
<template>
  <div class="hello">
    <h1 v-text-length>{{ msg }}</h1>
    <p v-text-length>
```

现在，当我们运行我们的应用程序时，我们将在控制台中看到以下文本：

```js
This element, H1, has text with 26 characters
This element, P, has text with 121 characters
```

在我们的视口中，这个插件将在`h1`标签周围添加一个虚线紫色边框，并在`p`标签周围添加一个双层橙色边框。

现在我们了解了插件可以被创建和使用的基本方式，我们可以想出创造性的方法让我们的插件做一些更有用的事情。例如，我们可以通过添加一个工具提示来改进现有的插件，该工具提示将显示页面上不同元素中存在的单词数量。我们还可以添加颜色强度：单词越多，我们可以给这个“字符计数”徽章更多的颜色。

或者，我们可以列出样式属性或类属性中存在的值，或者两者都有。这个插件对于快速检查样式而不用打开开发工具可能会很有用，这在较小的屏幕或只有一个屏幕的工作站上可能会很有用。

接下来，我们将讨论如何发布一个 Vue 插件。具体来说，我们将发布我们刚刚制作的 OptionsPlugin。

# 发布一个 Vue 插件

撰写一个 `npm` 插件的先决条件是在网站上注册并验证你的电子邮件地址。因此，在 `npm` 上撰写你的 Vue 插件的第一步是访问 [`www.npmjs.com`](https://www.npmjs.com) 并注册一个账户。

我们将在 `npm` 上发布我们的 Vue 插件。首先，让我们检查是否已经有一个用户。在控制台中运行以下命令：

```js
npm whoami
```

如果出现错误，您需要通过运行此命令创建一个新用户：

```js
npm adduser
```

然后，只需按照说明将自己添加为用户。

# 添加一个简单的插件

添加一个简单的、单文件插件，只需在您选择的文件夹中运行 `npm init`。这个命令将帮助您创建一个 `package.json` 文件。

这是提供的问题和答案列表：

```js
package name: "vue-options-plugin"
version: (1.0.0)
description: A simple Vue plugin that shows how to use the options object
entry point: "OptionsPlugin.vue"
test command:
git repository:
keywords:
license: (ISC)
About to write to ...
Is this ok? (yes)
```

`npm init` 实用程序提供的默认答案列在圆括号中。要接受默认值，只需按 *Enter* 键。否则，只需输入所需的答案。

`npm` 作者还有一个作用域的概念。作用域就是你的用户名。不用担心作用域的最佳方法是在你的 `.npmrc` 文件中设置它，通过命令行运行以下命令：

```js
npm init --scope=username
```

当然，您需要用您的实际用户名替换 `username` 这个词。

完成后，运行 `dir` 命令列出文件夹的内容。它应该只列出一个文件：`package.json`。现在，我们可以创建另一个文件，命名为 `OptionsPlugin.vue`：

```js
touch OptionsPlugin.vue
```

让我们快速验证我们的 `package.json` 文件是否像这样：

```js
{
  "name": "vue-options-plugin",
  "version": "1.0.0",
  "description": "A simple Vue plugin that shows how to use options object",
  "main": "OptionsPlugin.vue",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "<your-username-here>",
  "license": "ISC"
}
```

接下来，让我们用这段代码更新 `OptionsPlugin.vue` 文件：

```js
const OptionsPlugin = { 
  install(Vue, options) {
    Vue.directive('text-length', {
        bind(el, binding, vnode) {
            const textLength = el.innerText.length;
            console.log("This element, " + el.nodeName + ", has text with " + textLength + " characters");

      if (textLength < 40) {
        el.style.cssText += "border:" + options.selectedOption.plum;
      } else if (textLength >= 40) {
        el.style.cssText += "border:" + options.selectedOption.orange;
      }
        }
    })
  }
};

export default OptionsPlugin;
```

最后，让我们添加一个`README.md`文件。`md`文件扩展名代表 Markdown，这是一种使在线内容编写变得非常容易的格式。我们将向`README`添加以下内容：

```js
# optionsplugin
<p> A demo of making a simple Vue 2 plugin and using it with values stored in the options object. This plugin logs out to the console the number of characters in an element. It also adds different CSS styles based on the length of characters in the element.</p>

## Installation
```bash

npm install --save optionsplugin

```js
## Configuration
```javascript

import Vue from 'vue';

import OptionsPlugin from 'optionsplugin'

Vue.use(OptionsPlugin, {

selectedOption: {

plum: "5px dashed purple",

orange: "10px double orange"

}

})

```js
## Usage
<p>To use it, simply add the plugin's custom directive of v-text-length to an element in your template's code.</p>
```

这应该是我们插件描述的一个很好的起点。我们随时可以稍后改进`README`。现在我们已经准备好`package.json`，`README.md`和`OptionsPlugin.vue`，我们可以通过运行以下命令简单地发布我们的插件：

```js
npm publish --access=public
```

我们需要为我们的`npm publish`命令提供`--access=public`标志，因为作用域包默认为私有访问，我们需要明确覆盖此设置。

一旦发布，我们的控制台将输出以下信息：

```js
+ vue-options-plugin@1.0.0
```

这是我们成功发布插件的标志。我们的新插件现在有了自己的主页，位于以下 URL：

[`www.npmjs.com/package/vue-options-plugin`](https://www.npmjs.com/package/vue-options-plugin)。

最后，让我们看看如何将我们新添加的插件安装到另一个项目中。

# 在 Vue CLI 3 中安装我们的 NPM 插件

从`npm`安装我们的 Vue 插件，我们需要首先创建一个新项目。让我们运行这些命令：

```js
vue create just-another-project
cd just-another-project
npm run-serve
```

现在，我们可以通过运行以下命令添加我们的`npm`插件：

```js
npm install --save vue-options-plugin
```

就是这样；现在，我们的插件已经在我们的项目中可用，我们可以像之前描述的那样导入它来使用：

```js
import VueOptionsPlugin from 'vue-options-plugin'
```

现在，我们可以根据需要使用我们插件的功能。

# 要学习的其他插件

查看其他人的代码的良好编码示例总是很有益的，这样我们就可以从中学习。我们可以学习并可能贡献的一些有用的插件包括：

+   一个引导游览插件，vue-tour：[`github.com/pulsardev/vue-tour`](https://github.com/pulsardev/vue-tour)

+   多选插件，vue-multiselect：[`vue-multiselect.js.org/`](https://vue-multiselect.js.org/)

+   一个工具提示插件，v-tooltip：[`akryum.github.io/v-tooltip`](https://akryum.github.io/v-tooltip)

# 摘要

在本章中，我们讨论了在 Vue 中创建自定义指令和自定义插件。我们介绍了如何构建自定义指令，以及如何创建全局和局部自定义指令。我们还讨论了如何向自定义指令传递数值以及如何使用 Vue 插件。我们还介绍了如何创建一些自定义 Vue 插件。最后，我们看到了如何将我们的插件发布到`npm`，以及如何从 NPM 在我们的项目中安装它。

在接下来的章节中，我们将探讨如何通过过渡和动画使我们的应用程序更具交互性。
