添加 Vue 表单

在本章中，您将使用`v-model`和`v-bind`创建表单。您将学习在将表单数据发送到服务器之前在客户端验证表单。您将创建具有基本元素的表单，绑定动态值，并使用修饰符修改输入元素的行为。您还将学习如何使用`vee-validate`插件验证表单并将其应用于 Nuxt 应用程序。在本章中学习如何在 Vue 表单中使用`v-model`和`v-bind`非常重要，因为我们将在接下来的章节中使用表单，例如在“添加 Vuex 存储”第十章和“创建用户登录和 API 身份验证”第十二章中。

本章中我们将涵盖以下主题：

+   理解`v-model`

+   使用基本数据绑定验证表单

+   创建动态值绑定

+   使用`vee-validate`验证表单

+   在 Nuxt 中应用 Vue 表单

# 第七章：理解`v-model`

`v-model`是 Vue 指令（自定义内置 Vue HTML 属性），允许我们在表单的`input`、`textarea`和`select`元素上创建双向绑定。您可以将表单输入与 Vue 数据绑定，以便在用户与输入字段交互时更新数据。`v-model`始终会跳过您在表单元素上设置的初始值，而将 Vue 数据视为真相的来源。因此，您应该在 Vue 端，在`data`选项或函数内声明初始值。

`v-model`将根据输入类型选择适当的方式来更新元素，这意味着如果您在`type="text"`的表单输入上使用它，它将使用`value`作为属性，并使用`input`作为事件来执行双向绑定。让我们看看在接下来的部分中包括哪些内容。

## 在文本和文本区域元素中使用 v-model

记得我们在《添加 Vue 组件》的第五章中使用`v-model`实现双向绑定来创建自定义输入组件吗？在该章节的“创建自定义输入组件”部分，我们学到了输入框的`v-model`语法 - `<input v-model="username">` - 实际上是以下内容的简写：

```js
<input
  v-bind:value="username"
  v-on:input="username = $event.target.value"
>
```

这个文本`input`元素在幕后绑定了`value`属性，该属性从处理程序`username`中获取值，而`username`又从`input`事件中获取值。因此，自定义的文本输入组件也必须始终在`model`属性中使用`value`属性和`input`事件，如下所示：

```js
Vue.component('custom-input', {
  props: {
    value: String
  },
  model: {
    prop: 'value',
    event: 'input'
  },
  template: `<input v-on:input="$emit('input', $event.target.value)">`,
})
```

这仅仅是因为`v-model`输入的性质是由`v-bind:value`和`v-on:input`组成。当在`textarea`元素中使用`v-model`指令时，情况也是一样的，如下例所示：

```js
<textarea v-model="message"></textarea>
```

这个`v-model` `textarea`元素是以下内容的简写：

```js
<textarea
  v-bind:value="message"
  v-on:input="message = $event.target.value"
></textarea>
```

这个`textarea`输入元素在幕后绑定了`value`属性，该属性从处理程序`message`中获取值，而`message`又从`input`事件中获取值。因此，自定义的`textarea`组件也必须始终遵守`v-model` `textarea`元素的性质，通过使用`value`属性和`input`事件在`model`属性中，如下所示：

```js
Vue.component('custom-textarea', {
  props: {
    value: null
  },
  model: {
    prop: 'value',
    event: 'input'
  }
})
```

简而言之，`v-model`文本`input`元素和`v-model` `textarea`输入元素始终将`value`属性与处理程序绑定，以在输入事件上获取新值，因此自定义输入组件也必须采用相同的属性和事件。那么复选框和单选按钮元素中的`v-model`又是怎样的呢？让我们在下一节中深入了解它们。

## 在复选框和单选按钮元素中使用 v-model

另一方面，`v-model`复选框和单选按钮输入元素始终将`checked`属性与在`change`事件上更新的布尔值绑定，如下例所示：

```js
<input type="checkbox" v-model="subscribe" value="yes" name="subscribe">
```

在上面的代码片段中，`v-model` `checkbox`输入元素确实是以下内容的简写：

```js
<input
  type="checkbox"
  name="subscribe"
  value="yes"
  v-bind:checked="false"
  v-on:change="subscribe = $event.target.checked"
>
```

因此，自定义的复选框输入元素也必须始终遵守`v-model`复选框输入元素的性质（如前面的代码块中所示），通过在`model`属性中采用`checked`属性和`change`事件，如下所示：

```js
Vue.component('custom-checkbox', {
  props: {
    checked: Boolean,
  },
  model: {
    prop: 'checked',
    event: 'change'
  }
})
```

同样适用于`v-model`单选按钮输入元素，如下所示：

```js
<input type="radio" v-model="answer" value="yes" name="answer">
```

前面的`v-model`元素是以下内容的另一种简写：

```js
<input
  type="radio"
  name="answer"
  value="yes"
  v-bind:checked="answer == 'yes'"
  v-on:change="answer = $event.target.value"
>
```

因此，自定义的单选按钮输入元素也必须始终遵守`v-model`元素的性质，如下所示：

```js
Vue.component('custom-radio', {
  props: {
    checked: String,
    value: String
  },
  model: {
    prop: 'checked',
    event: 'change'
  }
})
```

简而言之，`v-model`、`checkbox` 和 `radio` 按钮输入元素总是绑定 `value` 属性，并在 `change` 事件上更新，因此自定义输入组件也必须采用相同的属性和事件。现在，让我们看看 `v-model` 在下一节中如何在 `select` 元素中工作。

## 在选择元素中使用 v-model

毫不奇怪，`v-model` `select` 输入元素总是将 `value` 属性与在 `change` 事件上获取其选定值的处理程序绑定，如下例所示：

```js
<select
  v-model="favourite"
  name="favourite"
>
  //...
</select>
```

前面的 `v-model` `checkbox` 输入元素只是以下内容的另一种简写：

```js
<select
  v-bind:value="favourite"
  v-on:change="favourite = $event.target.value"
  name="favourite"
>
  //...
</select>
```

因此，自定义的 `checkbox` 输入元素也必须始终遵守 `v-model` 元素的特性，使用 `value` 属性和 `model` 属性中的 `change` 事件，如下所示：

```js
Vue.component('custom-select', {
  props: {
    value: String
  },
  model: {
    prop: 'value',
    event: 'change'
  }
})
```

正如你所看到的，`v-model` 在 `v-bind` 的基础上是一种语法糖，它将一个值绑定到标记上，并在用户输入事件上更新数据，这些事件可以是 `change` 或 `input` 事件。简而言之，`v-model` 在幕后结合了 `v-bind` 和 `v-on`，但重要的是要理解语法下面的内容，作为 Vue/Nuxt 应用程序开发者。

你可以在我们的 GitHub 存储库的`/chapter-7/vue/html/`目录中找到本节中涵盖的示例。

现在你已经了解了 `v-model` 指令在表单输入元素中的工作方式，让我们在下一节中在表单上使用这些 `v-model` 元素并对其进行验证。

# 使用基本数据绑定验证表单

表单是收集信息的文件。HTML `<form>` 元素是一个可以从网页用户那里收集数据或信息的表单。这个元素需要在其中使用 `<input>` 元素来指定我们想要收集的数据。但在接受数据之前，我们通常会希望对其进行验证和过滤，以便从用户那里获得真实和正确的数据。

Vue 允许我们轻松地从 `v-model` 输入元素中验证数据，因此让我们从单文件组件（SFC）Vue 应用程序和 webpack 开始，你可以在第五章中了解到，*添加 Vue 组件*，在*使用 webpack 编译单文件组件*部分。首先，我们将创建一个非常简单的表单，其中包括一个 `submit` 按钮和在 `<template>` 块中显示错误消息的标记，如下所示：

```js
// src/components/basic.vue
<form v-on:submit.prevent="checkForm" action="/" method="post">
  <p v-if="errors.length">
    <b>Please correct the following error(s):</b>
    <ul>
      <li v-for="error in errors">{{ error }}</li>
    </ul>
  </p>
  <p>
    <input type="submit" value="Submit">
  </p>
</form>
```

稍后我们将在`<form>`中添加其余的输入元素。现在，让我们设置基本结构并了解我们将需要什么。我们使用`v-on:submit.prevent`来防止浏览器默认发送表单数据，因为我们将在 Vue 实例的`<script>`块中使用`checkForm`方法来处理提交：

```js
// src/components/basic.vue
export default {
  data () {
    return {
      errors: [],
      form: {...}
    }
  },
  methods:{
    checkForm (e) {
      this.errors = []
      if (!this.errors.length) {
        this.processForm(e)
      }
    },
    processForm (e) {...}
  }
}
```

在 JavaScript 方面，我们定义一个数组来保存在验证过程中可能遇到的错误。`checkForm`逻辑验证我们稍后将在本节中添加的必填字段。如果必填字段未能通过验证，我们将错误消息推送到`errors`中。当表单填写正确和/或未发现错误时，它将被传递到`processForm`逻辑，在那里我们可以在将其发送到服务器之前对表单数据进行进一步处理。

## 验证文本元素

让我们开始添加一个用于单行文本的`<input>`元素：

```js
// src/components/basic.vue
<label for="name">Name</label>
<input v-model="form.name" type="text">

export default {
  data () {
    return {
      form: { name: null }
    }
  },
  methods:{
    checkForm (e) {
      this.errors = []
      if (!this.form.name) {
        this.errors.push('Name required')
      }
    }
  }
}
```

在`<script>`块中，我们在`data`函数中定义了一个`name`属性，它保存初始的`null`值，并将在`<input>`元素的`input`事件上进行更新。当您点击`submit`按钮时，我们在`if`条件块中验证`name`数据；如果没有提供数据，那么我们将错误消息`push`到`errors`中。

## 验证文本区域元素

我们要添加的下一个元素是`<textarea>`，用于多行文本，其工作方式与`<input>`相同：

```js
// src/components/basic.vue
<label for="message">Message</label>
<textarea v-model="form.message"></textarea>

export default {
  data () {
    return {
      form: { message: null }
    }
  },
  methods:{
    checkForm (e) {
      this.errors = []
      if (!this.form.message) {
        this.errors.push('Message required')
      }
    }
  }
}
```

在`<script>`块中，我们在`data`函数中定义了一个`message`属性，它保存初始的`null`值，并将在`<textarea>`元素的`input`事件上进行更新。当您点击`submit`按钮时，我们在`if`条件块中验证`message`数据；如果没有提供数据，那么我们将错误消息`push`到`errors`中。

## 验证复选框元素

下一个元素是一个单个复选框`<input>`元素，它将保存默认的布尔值：

```js
// src/components/basic.vue
<label class="label">Subscribe</label>
<input type="checkbox" v-model="form.subscribe">

export default {
  data () {
    return {
      form: { subscribe: false }
    }
  },
  methods:{
    checkForm (e) {
      this.errors = []
      if (!this.form.subscribe) {
        this.errors.push('Subscription required')
      }
    }
  }
}
```

我们还将添加以下多个复选框`<input>`元素，它们绑定到同一个数组`books: []`：

```js
// src/components/basic.vue
<input type="checkbox" v-model="form.books" value="On the Origin of Species">
<label for="On the Origin of Species">On the Origin of Species</label>

<input type="checkbox" v-model="form.books" value="A Brief History of Time">
<label for="A Brief History of Time">A Brief History of Time</label>

<input type="checkbox" v-model="form.books" value="The Selfish Gene">
<label for="The Selfish Gene">The Selfish Gene</label>

export default {
  data () {
    return {
      form: { books: [] }
    }
  },
  methods:{
    checkForm (e) {
      this.errors = []
      if (this.form.books.length === 0) {
        this.errors.push('Books required')
      }
    }
  }
}
```

在`<script>`块中，我们在`data`函数中定义了一个`subscribe`属性，它保存初始的布尔值`false`，并将在复选框`<input>`元素的`change`事件上进行更新。当您点击`submit`按钮时，我们在`if`条件块中验证`subscribe`数据；如果没有提供数据或者为`false`，那么我们将错误消息`push`到`errors`中。

我们通过定义一个`books`属性来实现多个复选框`<input>`元素的相同功能，它保存了初始的空数组，并将在复选框`<input>`元素的`change`事件上进行更新。我们在`if`条件块中验证`books`数据；如果长度为`0`，那么我们将错误消息`push`到`errors`中。

## 验证单选按钮元素

接下来是绑定到相同属性名称的多个单选按钮`<input>`元素，即`gender`：

```js
// src/components/basic.vue
<label for="male">Male</label>
<input type="radio" v-model="form.gender" value="male">

<label for="female">Female</label>
<input type="radio" v-model="form.gender" value="female">

<label for="other">Other</label>
<input type="radio" v-model="form.gender" value="other">

export default {
  data () {
    return {
      form: { gender: null }
    }
  },
  methods:{
    checkForm (e) {
      this.errors = []
      if (!this.form.gender) {
        this.errors.push('Gender required')
      }
    }
  }
}
```

在`<script>`块中，我们在`data`函数中定义了一个`gender`属性，它保存了初始的`null`值，并将在选定的`<input>`单选按钮元素的`change`事件上进行更新。当点击`submit`按钮时，我们在`if`条件块中验证`gender`数据。如果没有提供数据，那么我们将错误消息`push`到`errors`中。

## 验证选择元素

接下来是一个单个`<select>`元素，其中包含多个`<option>`元素，如下所示：

```js
// src/components/basic.vue
<select v-model="form.favourite">
  <option disabled value="">Please select one</option>
  <option value="On the Origin of Species">On the Origin of 
   Species</option>
  <option value="A Brief History of Time">A Brief History of Time</option>
  <option value="The Selfish Gene">The Selfish Gene</option>
</select>

export default {
  data () {
    return {
      form: { favourite: null }
    }
  },
  methods:{
    checkForm (e) {
      this.errors = []
      if (!this.form.favourite) {
        this.errors.push('Favourite required')
      }
    }
  }
}
```

最后是多个绑定到相同`Array`的多个`<option>`元素的多个`<select>`元素，即`favourites: []`：

```js
// src/components/basic.vue
<select v-model="form.favourites" multiple >
  <option value="On the Origin of Species">On the Origin of 
   Species</option>
  <option value="A Brief History of Time">A Brief History of Time</option>
  <option value="The Selfish Gene">The Selfish Gene</option>
</select>

export default {
  data () {
    return {
      form: { favourites: [] }
    }
  },
  methods:{
    checkForm (e) {
      this.errors = []
      if (this.form.favourites.length === 0) {
        this.errors.push('Favourites required')
      }
    }
  }
}
```

在`<script>`块中，我们在`data`函数中定义了一个`favourites`属性，它保存了初始的`null`值，并将在`<select>`元素的`change`事件上进行更新。当点击`submit`按钮时，我们在`if`条件块中验证`favourites`数据。如果没有提供数据，那么我们将错误消息`push`到`errors`中。对于多个`<select>`元素，我们也是通过定义一个`favourites`属性来实现相同的功能，它保存了初始的空数组，并将在`<select>`元素的`change`事件上进行更新。我们在`if`条件块中验证`favourites`数据；如果长度为`0`，那么我们将错误消息`push`到`errors`中。

然后我们将使用`processForm`逻辑完成这个表单，只有在`checkForm`逻辑中没有发现错误时才会调用。我们使用 Node.js 包`qs`对`this.form`对象进行字符串化，以便以以下格式将数据发送到服务器：

```js
name=John&message=Hello%20World&subscribe=true&gender=other
```

让我们使用 npm 安装`qs`：

```js
$ npm i qs

```

然后我们可以按照以下方式使用它：

```js
import axios from 'axios'
import qs from 'qs'

processForm (e) {
  var data = qs.stringify(this.form)
  axios.post('../server.php', data)
  .then((response) => {
    // success callback
  }, (response) => {
    // error callback
  })
}
```

我们使用`axios`发送数据，并从服务器获取响应（通常是 JSON 格式），然后您可以对响应数据进行操作，例如在服务器端显示“成功”或“失败”消息。

有关`qs`的更多信息，请访问[`www.npmjs.com/package/qs`](https://www.npmjs.com/package/qs)，有关`axios`，请访问[`github.com/axios/axios`](https://github.com/axios/axios)。

您可以在我们的 GitHub 存储库的`/chapter-7/vue/webpack/`中找到前面的示例应用程序。

然而，我们还没有完全完成，因为有时我们可能希望将动态值绑定到表单输入中，而不是从`v-model`中获取默认值。例如，在我们的示例应用程序中，我们只使用单个复选框`<input>`元素获取`subscribe`属性的布尔值，但我们希望使用字符串值`yes`或`no`。我们将在接下来的部分探讨如何更改默认值。

# 进行动态值绑定

在前一节的示例应用程序中，我们仅使用`v-model`获取`radio`，`checkbox`和`select`选项的字符串或布尔值。我们可以通过使用`true-value`，`false-value`和`v-bind`来更改此默认值。让我们深入了解。

## 用布尔值替换-复选框元素

我们可以通过使用`true-value`和`false-value`将我们的自定义值绑定到**单个**`checkbox`元素。例如，我们可以使用`true-value`将`yes`值绑定到替换默认的`true`布尔值，使用`false-value`将`no`值绑定到替换默认的`false`布尔值：

```js
// src/components/dynamic-values.vue
<input
  type="checkbox"
  v-model="form.subscribe"
  true-value="yes"
  false-value="no"
>

export default {
  data () {
    return {
      form: { subscribe: 'no' }
    }
  },
  methods:{
    checkForm (e) {
      this.errors = []
      if (this.form.subscribe !== 'yes') {
        this.errors.push('Subscription required')
      }
    }
  }
}
```

现在，当您将`subscribe`输入的值发送到服务器时，您将得到`yes`或`no`的响应。在`<script>`块中，我们现在将`no`声明为`subscribe`属性的初始值，并在`if`条件块中对其进行验证，以确保在点击`submit`按钮时它始终为`yes`，否则我们将错误消息推送到`errors`。

## 用动态属性替换字符串-单选按钮元素

关于单选按钮`<input>`元素，我们可以使用`v-bind`将它们的值绑定到 Vue 实例中的动态属性：

```js
// src/components/dynamic-values.vue
<input type="radio" v-model="form.gender" v-bind:value="gender.male">

export default {
  data () {
    return {
      gender: {
        male: 'm',
        female: 'f',
        other: 'o',
      },
      form: { gender: null }
    }
  }
}
```

现在，当选择此单选按钮时，您将得到`m`，验证逻辑与以前相同。

## 用对象替换字符串-选择选项元素

我们还可以使用`v-bind`将**非字符串**值（如`Object`）绑定到表单输入。请参阅以下示例：

```js
// src/components/dynamic-values.vue
<select v-model="form.favourite">
  <option v-bind:value="{ title: 'On the Origin of Species' }">On 
   the Origin of Species</option>
</select>

export default {
  data () {
    return {
      form: {
        favourite: null
      }
    }
  }
}
```

现在当选择此选项时，您将得到`typeof this.favourite`的`object`和`this.favourite.title`的`On the Origin of Species`。验证逻辑没有变化。

我们还可以使用动态值和`v-for`动态呈现`<option>`元素：

```js
// src/components/dynamic-values.vue
<select v-model="form.favourites" name="favourites_array[]" multiple >
  <option v-for="book in options.books" v-bind:value="book.value">
    {{ book.text }}
  </option>
</select>

data () {
  return {
    form: { favourites: [] },
    options: {
      books: [
        { value: { title: 'On the Origin of Species' }, text: 'On the 
         Origin of Species'},
        { value: { title: 'A Brief History of Time' }, text: 'A Brief 
         History of Time'},
        { value: { title: 'The Selfish Gene' }, text: 'The Selfish Gene'}
      ]
    }
  }
}
```

现在我们不再需要硬编码`<option>`元素了。我们可以从其他地方（如 API）获取`books`数据。

除了将动态值绑定到表单输入外，我们还可以修改输入元素上`v-model`的默认行为。例如，我们可以使用它们上的`change`事件，而不是将输入与数据同步。让我们在下一个主题中发现如何做到这一点。

# 使用修饰符

Vue 提供了这三个修饰符，`.lazy`、`.number`和`.trim`，我们可以与`v-model`一起使用，改变默认事件或为表单输入添加额外功能。让我们深入了解。

## 添加`.lazy`

我们可以使用`.lazy`和`v-model`来将`<input>`和`<textarea>`元素上的`input`事件改为`change`事件。看下面的例子：

```js
// src/components/modifiers.vue
<input v-model.lazy="form.name" type="text">
```

现在输入与数据在`change`之后同步，而不是默认的`input`事件。

## 添加`.number`

我们可以使用`.number`和`v-model`来改变`<input>`元素上`type="number"`的默认类型转换，将`string`转换为`number`。看下面的例子：

```js
// src/components/modifiers.vue
<input v-model.number="form.age" type="number">
```

现在你得到`typeof this.form.age`的`number`，而不是没有`.number`的`string`。

## 添加`.trim`

我们可以使用`.trim`和`v-model`来修剪用户输入的空白。看下面的例子：

```js
// src/components/modifiers.vue
<textarea v-model.lazy.trim="form.message"></textarea>
```

现在用户输入的文本会自动修剪。文本开头和结尾的任何额外空白都将被修剪掉。

虽然编写自定义验证逻辑是可能的，但已经有一个很棒的插件可以帮助轻松验证输入并显示相应的错误。这个插件叫做 VeeValidate，是一个基于 Vue 的模板验证框架。让我们在下一节中发现如何利用这个插件。

# 使用 VeeValidate 验证表单

使用 VeeValidate，我们将使用 VeeValidate 的组件来验证我们的 HTML 表单，并使用 Vue 的作用域插槽来暴露错误消息。例如，这是一个我们已经熟悉的`v-model`输入元素：

```js
<input v-model="username" type="text" />
```

如果你想用 VeeValidate 验证它，你只需要用`<ValidationProvider>`组件包装输入：

```js
<ValidationProvider name="message" rules="required" v-slot="{ errors }">
  <input v-model="username" name="username" type="text" />
  <span>{{ errors[0] }}</span>
</ValidationProvider>
```

通常情况下，我们使用`<ValidationProvider>`组件来验证`<input>`元素。我们可以使用`rules`属性将验证规则附加到这个组件上，并使用`v-slot`指令显示错误。让我们在以下步骤中发现如何利用这个插件来加快验证过程：

1.  使用 npm 安装 VeeValidate：

```js
$ npm i vee-validate
```

1.  在`/src/`目录中创建一个`.js`文件，并使用 VeeValidate 的`extend`函数添加规则：

```js
// src/vee-validate.js
import { extend } from 'vee-validate'
import { required } from 'vee-validate/dist/rules'

extend('required', {
  ...required,
  message: 'This field is required'
})
```

VeeValidate 提供了许多内置的验证规则，如`required`、`email`、`min`、`regex`等，因此我们可以导入我们应用程序所需的特定规则。因此，在上述代码中，我们导入`required`规则，并通过`extend`函数安装它，然后在`message`属性中添加我们的自定义消息。

1.  将`/src/vee-validate.js`导入到初始化 Vue 实例的主入口文件中：

```js
// src/main.js
import Vue from 'vue'
import './vee-validate'
```

1.  将`ValidationProvider`组件本地导入到页面中，并开始验证该页面上的输入字段：

```js
// src/components/vee-validation.vue
<ValidationProvider name="name" rules="required|min:3" v-slot="{ errors }">
  <input v-model.lazy="name" type="text" name="name">
  <span>{{ errors[0] }}</span>
</ValidationProvider>

import { ValidationProvider } from 'vee-validate'

export default {
  components: {
    ValidationProvider
  }
}
```

我们还可以在`/src/main.js`或`/src/plugins/vee-validate.js`中全局注册`ValidationProvider`：

```js
import Vue from 'vue'
import { ValidationProvider, extend } from 'vee-validate'

Vue.component('ValidationProvider', ValidationProvider)
```

但是，如果您不需要在应用程序的每个页面上使用此组件，这可能不是一个好主意。因此，如果您只需要在某个页面上使用它，则将其本地导入。

1.  本地导入`ValidationObserver`组件，并将`passes`对象添加到`v-slot`指令中。因此，让我们按照以下方式重构*步骤 4*中的 JavaScript 代码：

```js
// src/components/vee-validation.vue
<ValidationObserver v-slot="{ passes }">
  <form v-on:submit.prevent="passes(processForm)" novalidate="true">
    //...
    <input type="submit" value="Submit">
  </form>
</ValidationObserver>

import {
  ValidationObserver,
  ValidationProvider
} from 'vee-validate'

export default {
  components: {
    ValidationObserver,
    ValidationProvider
  },
  methods:{
    processForm () {
      console.log('Posting to the server...')
    }
  }
}
```

我们使用`<ValidationObserver>`组件来包装`<form>`元素，以在提交之前告知其是否有效。我们还在`<ValidationObserver>`组件的作用域插槽对象中使用`passes`属性，该属性用于在表单无效时阻止提交。然后我们将我们的`processForm`方法传递给表单元素上的`v-on:submit`事件中的`passes`函数。如果表单无效，则不会调用我们的`processForm`方法。

就是这样。我们完成了。您可以看到，我们不再需要`methods`属性中`v-on:submit`事件上的`checkForm`方法，因为 VeeValidate 已经为我们验证了元素，并且现在我们的 JavaScript 代码变得更短了。我们只需要使用`<ValidationProvider>`和`<ValidationObserver>`组件包装我们的输入字段。

如果您想了解有关 Vue 插槽和 VeeValidate 的更多信息，请访问以下链接：

+   [`logaretm.github.io/vee-validate/`](https://logaretm.github.io/vee-validate/) 用于 VeeValidate

+   [`vuejs.org/v2/guide/components-slots.html`](https://vuejs.org/v2/guide/components-slots.html) 用于 Vue 插槽

您可以在我们的 GitHub 存储库的`/chapter-7/vue/cli/`中找到我们先前的 Vue 应用程序的示例。

接下来，我们将在下一节中了解如何在 Nuxt 应用程序中应用 VeeValidate。

# 将自定义验证应用到 Nuxt 应用程序

让我们将自定义验证应用到我们已经有的示例网站中的联系页面。您可能已经注意到，现有的联系表单已经安装了 Foundation（Zurb）的验证。使用 Foundation 的表单验证是另一种提升我们的 HTML 表单验证的好方法。

如果您想了解更多关于 Foundation 的信息，可以在它们的官方指南中找到更多信息：[`foundation.zurb.com/sites/docs/abide.html`](https://foundation.zurb.com/sites/docs/abide.html)。

但是，如果我们想要在 Nuxt 中进行自定义验证，我们刚刚学习了在 Vue 应用程序中使用的 VeeValidate，那么让我们按照以下步骤安装和设置我们需要的内容：

1.  通过 npm 安装 VeeValidate：

```js
$ npm i vee-validate
```

1.  在`/plugins/`目录中创建一个插件文件，并添加我们需要的规则，如下所示：

```js
// plugins/vee-validate.js
import { extend } from 'vee-validate'
import {
  required,
  email
} from 'vee-validate/dist/rules'

extend('required', {
  ...required,
  message: 'This field is required'
})

extend('email', {
  ...email,
  message: 'This field must be a valid email'
})
```

这个文件中的一切都和我们在 Vue 应用程序中做的文件一样。

1.  在 Nuxt 配置文件的`plugins`选项中包含插件路径：

```js
// nuxt.config.js
plugins: [
  '~/plugins/vee-validate'
]
```

1.  在 Nuxt 配置文件的`build`选项中为`/vee-validate/dist/rules.js`文件添加一个例外。

```js
// nuxt.config.js
build: {
  transpile: [
    "vee-validate/dist/rules"
  ],
  extend(config, ctx) {}
}
```

在 Nuxt 中，默认情况下，`/node_modules/`文件夹被排除在转译之外，当使用`vee-validate`时，您将会收到一个错误消息`Unexpected token export`，因此在运行 Nuxt 应用程序之前，我们必须将`/vee-validate/dist/rules.js`添加到转译中。

1.  像我们在 Vue 应用程序中所做的那样，导入`ValidationObserver`和`ValidationProvider`组件：

```js
// pages/contact.vue
import {
  ValidationObserver,
  ValidationProvider
} from 'vee-validate'

export default {
  components: {
    ValidationObserver,
    ValidationProvider
  }
}
```

1.  从`<form>`元素中删除 Foundation 的`data-abide`属性，但使用`<ValidationObserver>`组件将其包装起来，并将`submit`事件与`passes`和`processForm`方法绑定到`<form>`元素，如下所示：

```js
// pages/contact.vue
<ValidationObserver v-slot="{ passes }" ref="observer">
  <form v-on:submit.prevent="passes(processForm)" novalidate>
  //...
  </form>
</option>
```

这一步和我们在 Vue 应用程序中所做的步骤是一样的，但在这个例子中，我们在*步骤 8*中添加了`ref="observer"`，因为我们将在后面需要它。

1.  开始使用`<ValidationProvider>`组件重构`<form>`元素内的所有`<input>`元素，如下所示：

```js
// pages/contact.vue
<ValidationProvider name="name" rules="required|min:3" v-slot="{ errors, invalid, validated }">
  <label v-bind:class="[invalid && validated ? {'is-invalid-label': 
   '{_field_}'} : '']">Name
    <input
      type="text"
      name="name"
      v-model.trim="name"
      v-bind:class="[invalid && validated ? {'is-invalid-input': 
       '{_field_}'} : '']"
    >
    <span class="form-error">{{ errors[0] }}</span>
  </label>
</ValidationProvider>
```

这一步和我们在 Vue 应用程序中所做的步骤是一样的，但在这个例子中，我们在`v-slot`指令中添加了两个作用域插槽数据属性，`invalid`和`validated`，以便根据条件将类绑定到`<label>`和`<input>`元素。因此，如果`invalid`和`validated`都为`true`，那么我们将分别将`is-invalid-label`和`is-invalid-input`类绑定到元素上。

有关验证提供程序的作用域插槽数据属性的更多信息，请访问[`vee-validate.logaretm.com/v2/guide/components/validation-provider.html#scoped-slot-data`](https://vee-validate.logaretm.com/v2/guide/components/validation-provider.html#scoped-slot-data)。

1.  通过向`<script>`块中的`data`函数添加以下数据属性来重构，以与`v-model`输入元素同步。我们还将在`methods`选项中添加两个方法，如下所示：

```js
// pages/contact.vue
export default {
  data () {
    return {
      name: null,
      email: null,
      subject: null,
      message: null
    }
  },
  methods:{
    clear () {
      this.name = null
      this.email = null
      this.subject = null
      this.message = null
    },
    processForm (event) {
      alert('Processing!')
      console.log('Posting to the server...')
      this.clear()
      this.$refs.observer.reset()
    }
  }
}
```

这一步与我们在 Vue 应用中所做的相同，但在这个例子中，我们在`methods`选项中的`processForm`中添加了`clear`方法和`reset`方法。`<ValidationObserver>`组件在提交后不会重置表单的状态，因此我们必须手动进行，通过在*步骤 6*中将观察者作为引用传递，然后我们可以通过`this.$refs`从 Vue 实例中访问它。

1.  将这三个作用域插槽数据属性`dirty`、`invalid`和`validated`添加到`<ValidationObserver>`组件中，以切换警报和成功消息，然后让我们按照以下方式重构这个组件：

```js
// pages/contact.vue
<ValidationObserver v-slot="{ passes, dirty, invalid, validated }" ref="observer">
  <div class="alert callout" v-if="invalid && validated">
    <p><i class="fi-alert"></i> There are some errors in your 
     form.</p>
  </div>
  //...
  <div class="success callout" v-if="submitted && !dirty">
    <p><i class="fi-like"></i>&nbsp; Thank you for contacting
      me.</p>
  </div>
</ValidationObserver>

export default {
  data () {
    return {
      submitted: false
      //...
    }
  },
  methods:{
    processForm (event) {
      console.log('Posting to the server...')
      this.submitted = true
      //...
    }
  }
}
```

在最后一步中，我们添加了一个默认为`false`的`submitted`数据属性，当表单在`processForm`方法中提交时，它将被设置为`true`。另一方面，当作用域插槽中的`invalid`和`validated`都为`true`时，警报消息块将可见，当`submitted`属性为`true`且`dirty`作用域插槽数据属性为`false`时，成功消息块将可见。如果输入字段中有一个字母存在，我们将从`dirty`属性中得到一个`true`。换句话说，当输入字段中存在字母时。

您可以看到，在我们的 Nuxt 应用中重构的代码与我们在 Vue 标准应用中所做的非常相似。但是在 Nuxt 应用中，我们为表单添加了更复杂的逻辑，例如切换警报和成功消息，有条件地将类绑定到`<label>`和`<input>`元素，并在表单提交时重置`<ValidationObserver>`组件。对于其余的输入元素，重构过程是相同的，您可以在书的 GitHub 存储库`/chapter-7/nuxt-universal/sample-website/`中找到。

# 摘要

在本章中，我们已经介绍了使用`v-model`在各种表单输入上进行 Vue 表单验证。您已经学会了基本和动态值绑定，以及如何使用修饰符来更改默认的输入事件和类型转换。您还学会了使用`vee-validate`插件来简化验证过程。最后，我们成功将这些应用到了 Nuxt 应用程序中。

在下一章中，我们将探讨如何在 Nuxt 应用程序中添加服务器端框架。您将学会使用 Koa 创建一个简单的 API，并将其与 Nuxt 集成，使用 HTTP 客户端 Axios 来请求 API 数据。此外，您还将介绍一个基于 webpack 的极简构建系统，称为 Backpack，它将简化我们用于单文件组件 Vue 应用程序的自定义 webpack 配置。您还将学会如何在 Nuxt 应用程序中使用这个构建系统。敬请关注！
