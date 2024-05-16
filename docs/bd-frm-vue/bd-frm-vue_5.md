# 第五章：使用 Vuelidate 进行输入验证

在生产就绪的表单中，验证用户输入是必须的。即使在服务器端，应用程序也应该对传递给它们的所有数据进行双重检查，同时在前端对数据进行预验证应该是任何有经验的开发人员的强制性实践。

在这一章中，我们将看一下一个非常著名和强大的表单验证库 Vuelidate。您将学习如何在项目中使用这个库，并且能够成功地用它来验证用户输入。

值得庆幸的是，在 Vue 中，我们有一些不同的选项可以选择第三方库，比如 Vuelidate、VeeValidate，甚至 Vuetify 都有自己的验证方法。

在这一章中，我们将涵盖 Vuelidate。从安装到创建规则并将其应用于我们的表单输入，以及使用错误状态来通知用户存在问题。

本章将涵盖以下主题：

+   安装依赖项

+   创建验证规则

+   将验证移入我们的自定义输入

+   添加最后的修饰

# 技术要求

本章的代码可以在以下 GitHub 存储库中找到：

[`github.com/PacktPublishing/Building-Forms-with-Vue.js/tree/master/Chapter05`](https://github.com/PacktPublishing/Building-Forms-with-Vue.js/tree/master/Chapter05)。

查看以下视频，看看代码是如何运行的：

[`bit.ly/2VJIL8E`](http://bit.ly/2VJIL8E)

# 安装依赖项

让我们首先将 Vuelidate 作为一个依赖项安装到我们的项目中，然后我们将用它进行验证。按照以下步骤进行：

1.  打开终端并执行以下命令：

```js
> npm install vuelidate
```

一旦库被安装，我们必须将其导入到`main.js`中并将其用作插件，以便它在全局范围内对所有组件都可用。

1.  在`main.js`中添加以下代码，放在导入`Vue`和`App`的代码之后：

```js
import Vuelidate from 'vuelidate';
Vue.use(Vuelidate);
```

现在 Vuelidate 已经安装并成为我们项目依赖的一部分，我们准备让它承担一些繁重的工作。在下一节中，我们将创建我们的验证规则。

# 创建验证规则

当我们通过`Vue.use`将 Vuelidate 添加到我们的项目中时，该库会在我们的组件上添加一个新的保留属性：`validations`。

这个属性被添加到组件的配置对象上，与`data()`、`computed`等一起。它也将是一个对象，为我们想要验证的每个输入保留一个属性。

让我们创建这个属性并设置一个新的输入，没有自定义组件包装器进行测试。一旦我们理解了基础知识，我们就可以开始将所有这些内容翻译成我们的`BaseInput`和`BaseSelect`组件。

按照以下步骤创建验证规则：

1.  在`App.vue`中的`BaseInput`的`telephone`对象下面创建一个新的`<input>`表单：

```js
<input type="text" v-model="form.website" />
```

1.  记得将这个新属性`website`添加到`data()`的`form`对象中：

```js
form: {
  firstName: '',
  lastName: '',
  email: '',
  love: 'fun',
  telephone: '',
  website: ''
},
```

现在，让我们实际创建一个`validations`属性；现在，我们只会添加`form.website`的验证。

1.  将其放置在`component`对象的顶层，与您的`data()`和计算属性处于同一级别：

```js
validations: {
  form: {
    website: {
      // our validations will go here
    }
  }
}
```

对于这个特定的字段，我们希望确保验证用户提供的输入是一个有效的 URL。在 Vuelidate 中，我们有几个内置的验证器可以直接使用。完整列表可以在[`vuelidate.netlify.com/#sub-builtin-validators`](https://vuelidate.netlify.com/#sub-builtin-validators)找到。

为了验证输入是否为有效的 URL，我们有 URL 验证器。但是，为了将其添加到我们网站的`validators`对象中，我们必须首先导入它。Vuelidate 允许我们只导入我们实际要使用的验证器；这样，我们可以确保我们部署的代码保持较小。

1.  在`App.vue`中的其他导入附近添加以下导入语句：

```js
import { url } from 'vuelidate/lib/validators';
```

1.  现在我们已经导入了语句，我们最终可以将其添加到`validations.website`对象中：

```js
validations: {
  form: {
    website: {
      url // Validate that the "website" input is a valid URL
    }
   }
 },
```

设置好规则就够了。记得我们之前创建的新的`<input>`表单来保存`v-model="form.website"`吗？我们需要对`v-model`的设置进行一些调整，以便 Vuelidate 负责验证。

除了我们之前用来设置规则的`validations`属性之外，Vuelidate 还为我们在组件实例内部提供了一个新属性：`$v`。

`$v`是一个特殊的对象，它保存了我们验证结构的副本。除其他事项外，一个显著的特点是，它为我们添加到`validations`中的每个元素都有一个`$model`属性。Vuelidate 将成为我们的*中介*模型，并且反过来，它将自动绑定到我们`data()`中的`form.website`属性。

让我们来看看实际操作中是怎样的：

1.  更新`<input>`网站元素，使用 Vuelidate 期望的新的`v-model`格式。此外，我们将在其下插入`$v`对象，以便您可以更清楚地看到幕后发生了什么，如下所示：

```js
<input type="text" v-model="$v.form.website.$model" />
<pre>{{ $v }}</pre>
```

1.  回到你的浏览器，看看`$v`对象的结构，在你在新的表单字段中输入任何内容之前。

要特别注意的第一件事是`form.website`对象。在此对象内部，Vuelidate 将保持此输入的验证状态。`$model`属性将保存用户的输入，就像我们告诉`v-model`要做的那样。`$error`属性实际上将切换布尔值，并且会告诉我们输入是否有错误。

尝试在字段中输入一些随机的胡言乱语，并观察更新的属性。`$error`属性将更新为`true`，表示存在错误。与 URL 规则直接关联的`url`属性将切换为`false`，表示未满足 URL 验证条件。

1.  让我们在`<input>`上添加一些 CSS 绑定，以便在我们的输入上直观显示出未通过验证的情况：

```js
<input 
  type="text"
  v-model="$v.form.website.$model" 
  class="form-control"
  :class="{ 
    'is-valid': !$v.form.website.$error && $v.form.website.$dirty,
    'is-invalid': $v.form.website.$error
  }"
/>
```

在我们进一步解释之前，请在浏览器中尝试一下。尝试输入一个有效的 URL，例如[`google.com`](http://google.com)，并注意输入如何反映您的更改。

`:class`绑定是一种在 Vue 中有条件地向任何 HTML 元素添加类的方法。在我们这里使用的语法类型中，它允许我们设置一个键值对，其中键定义了我们要切换的类，例如`is-valid`。

该值是一个将被评估的 JavaScript 条件，以确定是否应该应用该类。这些条件是响应式的，并且每当条件的依赖项发生变化时，将被重新执行。

在我们的示例中，只要没有`$error`并且输入是`$dirty`，`is-valid`就会被切换为*on*。如果你想知道为什么我们还要检查`$dirty`，请尝试删除条件的这一部分，然后重新加载浏览器。您会立即注意到，即使元素中没有任何值，输入上也会出现绿色边框和复选标记。我们确定`<input>`是否在任何时候被用户修改的方式是通过`$dirty`属性；在这种情况下，从用户体验的角度来看，直到实际有一些输入时才显示有效的视觉提示是有意义的。

在`is-invalid`的情况下，我们正在检查字段中是否存在任何`$errors`，并使用漂亮的红色边框和 x 图标设置字段。

现在我们已经有了一些基本规则，让我们继续下一节，在那里我们将学习如何将所有这些内容合并到我们的自定义组件中。

# 将验证移入我们的自定义输入中

拥有自定义组件的惊人之处在于您可以以任何您喜欢的方式来打造它们。在本章中，我们将为我们的组件添加对有效和无效状态的支持。主要的验证逻辑仍将由父组件`App.vue`持有，因为它是包含表单的组件。

按照以下步骤添加验证：

1.  首先，让我们为每个输入添加新的规则。将以下内容添加到`validations`属性中：

```js
validations: {
form: {
first_name: { alpha, required },
last_name: { alpha },
    email: { email, required },
  telephone: {
      validPhone: phone => phone.match(/((\(\d{3}\) ?)|(\d{3}-))? 
      \d{3}-\d{4}/) !== null
    },
    website: { url },
    love: { required }
  }
},
```

1.  不要忘记更新您的导入语句，以引入我们现在使用的新验证器，如下所示：

```js
import { url, alpha, email, required } from 'vuelidate/lib/validators';
```

让我们来看看新的验证器：

+   `alpha`：这将限制字段只能包含字母数字字符。

+   `required`：此字段使字段成为必填项；如果没有值，则无效。

+   `email`：此字段确保输入保持有效的电子邮件格式。

对于`telephone`字段，我们将进行一些自定义验证，因为该字段被掩码为特定格式`(###)###-####`，我们需要编写自己的验证函数。

在这种情况下，我们正在调用验证器`validPhone`，它是一个返回布尔值的函数。这个布尔值是通过将电话与正则表达式进行匹配并确保它不为空来计算的；也就是说，它确实有一个匹配项。

现在我们已经将所有的`validations`放在了适当的位置，我们需要更新我们的`App.vue`模板。我们的`BaseInput`组件和`BaseSelect`组件需要更新`v-model`，以便它指向 Vuelidate 模型而不是我们的本地状态。此外，我们需要将我们的网站输入更新为完整的`BaseInput`组件。

1.  对您的代码进行以下更改；我们正在更新`v-model`和输入类型：

```js
<form>
  <BaseInput 
    label="First Name:" 
    v-model="$v.form.firstName.$model" 
  />
  <BaseInput 
    label="Last Name:" 
    v-model="$v.form.lastName.$model" 
  />
  <BaseInput 
    label="Email:" 
    v-model="$v.form.email.$model" 
    type="email" 
  />
  <BaseInput 
    label="The URL of your favorite Vue-made website"
    v-model="$v.form.website.$model"
  />
  <BaseInput 
    label="Telephone"
    type="text" 
    v-model="$v.form.telephone.$model"
    :mask="'(###)###-####'"
  />
  <BaseSelect 
    label="What do you love most about Vue?" 
    :options="loveOptions"
    v-model="$v.form.love.$model"
  />
  <div class="form-group">
    <button 
      :disabled="!formIsValid" 
      @click.prevent="onSubmit" 
      type="submit" 
      class="btn btn-primary"
    >Submit</button>
  </div>
</form>
```

为了使我们的自定义组件显示正确的 CSS 类，我们将为它们添加一个名为`validator`的新属性，并将引用传递给与特定元素匹配的 Vuelidate 对象的 prop。

1.  打开`BaseInput.vue`并创建`validator`属性，如下所示：

```js
validator: {
type: Object,
  required: false,
  validator($v) {
    return $v.hasOwnProperty('$model');
  }
}
```

在属性的“验证器”方法中，我们将检查作为属性传递的“验证器”对象中是否有一个`$model`属性（即“验证器.$model”），这对于 Vuelidate 的所有字段属性都是 true。这样，我们可以确保我们可以访问我们需要的属性。

接下来，让我们将之前在<input>元素上的:class 绑定带过来，但我们会做一些小的调整，以适应这是一个组件属性。

1.  将以下内容添加到 BaseInput.vue 中的<input>元素：

```js
:class="{
  'is-valid': validator && !validator.$error && validator.$dirty,
  'is-invalid': validator && validator.$error
}"
```

由于验证器不是我们组件上的必需属性，我们必须再次检查实际设置的条件，然后再检查它的$error 和$dirty 属性。

1.  最后，回到 App.vue 并为所有的 BasicInput 元素添加:validator 属性：

```js
<BaseInput 
  label="First Name:" 
  v-model="$v.form.firstName.$model" 
  :validator="$v.form.firstName"
/>
<BaseInput 
  label="Last Name:" 
  v-model="$v.form.lastName.$model" 
  :validator="$v.form.lastName"
/>
<BaseInput 
  label="Email:" 
  v-model="$v.form.email.$model" 
  :validator="$v.form.email"
  type="email" 
/>
<BaseInput 
  label="The URL of your favorite Vue-made website"
  v-model="$v.form.website.$model"
  :validator="$v.form.website"
/>
<BaseInput 
  label="Telephone"
  type="text" 
  v-model="$v.form.telephone.$model"
  :validator="$v.form.telephone"
  :mask="'(###)###-####'"
/>
```

回到你的浏览器，玩弄一下输入框，现在它们都在后台由 Vuelidate 进行验证！

哇，这是相当多的信息 - 休息一下，吃点鳄梨土司；你值得拥有！在下一节中，我们将对我们的表单、BaseSelect 和 onSubmit 方法进行一些最终的更改，以便我们可以结束这一切。

# 添加最后的修饰

在我们结束本章之前，还有一些事情需要做。首先，让我们处理 BaseSelect；它仍然需要一个验证器属性和一些:class 绑定。

按照以下步骤找出我们如何做到这一点：

1.  首先，在 BaseSelect.vue 中添加验证器属性：

```js
validator: {
type: Object,
 required: false,
  validator($v) {
   return $v.hasOwnProperty('$model');
  }
}
```

现在，让我们添加:class 绑定；但这里，我们不会根据$dirty 进行检查，因为我们没有初始空值。

1.  将以下代码添加到<select>元素中：

```js
:class="{
  'is-valid': validator && !validator.$error,
  'is-invalid': validator && validator.$error
}"
```

1.  现在组件准备好了，回到 App.vue 并更新我们的 BaseSelect 元素，加上它自己的:validator 属性：

```js
<BaseSelect 
  label="What do you love most about Vue?" 
  :options="loveOptions"
  v-model="$v.form.love.$model"
  :validator="$v.form.love"
/>
```

1.  回到你的浏览器，验证元素是否按预期行为。

另一件我们不应忘记改变的事情是 App.vue 上的 onSubmit 方法。现在，我们正在使用一个计算属性，它在检查表单的有效性方面做得很差。让我们通过利用 Vuelidate 的更多功能来检查我们的表单是否准备好提交来解决这个问题。为了做到这一点，让我们首先删除我们的 formIsValid 计算属性。

Vuelidate 在`$v`对象的根部有一个`$invalid`属性，我们可以检查它来查看表单是否准备好提交。我们将在下一分钟内用于我们的`onSubmit`方法。

1.  完全删除`formIsValid` `computed`属性：

```js
computed: {}
```

默认情况下，所有表单最初都处于`$invalid`状态，因为 Vuelidate 在用户`$touches`并修改输入字段时触发其验证。我们需要进行一些微调以适应我们的提交按钮与此行为的兼容性。

1.  首先更改按钮的`:disabled`属性，以便根据`$error`而不是我们旧的`computed`属性进行检查：

```js
<button 
 :disabled="$v.$error" 
  @click.prevent="onSubmit" 
  type="submit" 
  class="btn btn-primary"
>Submit</button>
```

1.  接下来，让我们修改`onSubmit`方法，强制执行所有输入的`$touch`方法（并触发所有输入的验证），然后检查表单是否实际上是有效的并准备好提交：

```js
onSubmit() {
  this.$v.$touch();
  if (!this.$v.$invalid) return;   
  axios.post('http://localhost:3000/dolphins', { params:        
  this.form }).then(response => {
  console.log('Form has been posted', response);
  }).catch(err => {
  console.log('An error occurred', err);
  });
}
```

返回到您的浏览器并重新加载窗口以清除输入。不输入任何内容，点击提交按钮。您会看到`$v.$touch()`方法将触发，无效的输入（例如必填项）将变成红色，表示存在问题。

在下面的截图中，您可以看到`validator`是如何工作的，以及它如何直观地向用户确认发生了什么：

![](img/11fd20c9-49ef-4626-893d-e6b33dfd20b7.png)

就是这样！当涉及到表单验证时，Vuelidate 是一个很棒的工具——它非常灵活，并允许连接到外部数据源，比如 Vuex，我们将在下一章中看到。

# 摘要

在本章中，您已经学会了如何将 Vuelidate 作为 Vue 项目的依赖项，并获得了在常规输入和自定义组件上设置和管理表单验证的技能。在下一章中，我们将更进一步，看看全局状态管理——*鼓声*——Vuex！
