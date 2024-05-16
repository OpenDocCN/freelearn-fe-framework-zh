# 第四章：创建自定义应用程序组件和布局

要开始我们应用程序的开发，我们需要创建整个应用程序将使用的自定义组件和输入。这些组件将采用无状态的方法创建。

我们将开发`UsernameInput`组件，`PasswordInput`组件，`EmailInput`组件和`AvatarInput`组件。我们还将开发应用程序页面和聊天布局的基本布局，它将包装聊天页面。

在本章中，我们将涵盖以下示例：

+   为应用程序创建自定义输入

+   创建应用程序布局

# 技术要求

在本章中，我们将使用**Node.js**和**Quasar Framework**。

注意，Windows 用户！您需要安装一个名为`windows-build-tools`的`npm`包，以便能够安装所需的包。要做到这一点，以管理员身份打开 PowerShell 并执行以下命令：

`> npm install -g windows-build-tools`

要安装 Quasar Framework，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> npm install -g @quasar/cli 
```

# 为应用程序创建自定义输入

创建应用程序需要创建大量的表单。所有这些表单都需要输入，这些输入很可能在应用程序中重复出现。

在这个示例中，我们将创建自定义输入表单，我们将在几乎每个表单中使用它们。

创建自定义输入表单的过程有助于开发人员节省调试时间，代码的可重用性和未来的改进。

## 准备工作

这个示例的先决条件如下：

+   最后的示例项目

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   `@aws-amplify/cli`

+   `@quasar/cli`

要开始我们的自定义组件，我们将继续使用在第三章*设置我们的聊天应用程序 - AWS Amplify 环境和 GraphQL*中创建的项目。

## 如何做...

为了更好地重用代码，我们将创建单独的组件来处理应用程序上的自定义表单。在这种情况下，我们将创建六个组件：

+   `UsernameInput`

+   `PasswordInput`

+   `NameInput`

+   `EmailInput`

+   `AvatarInput`

+   `AvatarDisplay`

所以，让我们开始吧。

### 创建 UsernameInput 组件

`UsernameInput`将负责处理用户名的检查和验证，这样我们就不需要在每个需要使用它的页面上重新编写所有规则。

#### 单文件组件`<script>`部分

在这里，我们将创建`UsernameInput`组件的`<script>`部分：

1.  在`src/components`文件夹中创建一个名为`UsernameInput.vue`的新文件，并打开它。

1.  创建一个带有`name`和`props`属性的默认导出的 JavaScript 对象：

```js
export default {
  name: '',
  props: {}, };
```

1.  对于`name`属性，将其定义为`"UsernameInput"`：

```js
name: 'UsernameInput',
```

1.  对于`props`属性，将其定义为一个 JavaScript 对象，并添加一个名为`value`的新属性，它也将是一个具有`type`，`default`和`required`属性的 JavaScript 对象。`type`属性需要定义为`String`，`default`为`''`，`required`为`false`： 

```js
props: {
  value: {
    type: String,
    default: '',
    required: false,
  },
},
```

#### 单文件组件`<template>`部分

在这里，我们将创建`UsernameInput`组件的`<template>`部分：

1.  在`<template>`部分，创建一个`QInput`组件。创建两个动态属性，`value`和`rules`。现在，`value`将绑定到`value`属性，`rules`属性将接收一个数组。数组的第一项是一个函数，用于验证输入，第二项是出现错误时的消息。

1.  将`outlined`和`lazy-rules`属性设置为`true`，并将`label`属性定义为`"Your Username"`。

1.  最后，通过创建一个`v-on`指令，使用`$listeners` Vue API 作为值来为事件创建事件侦听器。

完成所有步骤后，您的最终代码应该像这样：

```js
<template>
  <q-input
  :value="value"
  :rules="[ val => (val && val.length > 5 || 'Please type a valid 
      Username')]"
  outlined
  label="Your Username"
  lazy-rules
  v-on="$listeners"
  /> </template>
```

这是您的组件呈现出来的样子：

![](img/e6398356-c190-45f1-96fa-26d3c7d632a6.png)

### 创建一个 PasswordInput 组件

`PasswordInput`将是一个组件，具有特殊逻辑，通过单击按钮切换密码的可见性。我们将在这个组件中包装这个逻辑，这样每次使用这个组件时就不需要重新编写它。

#### 单文件组件`<script>`部分

在这部分，我们将创建`PasswordInput`组件的`<script>`部分：

1.  在`components`文件夹中创建一个名为`PasswordInput.vue`的新文件，并打开它。

1.  创建一个默认导出的 JavaScript 对象，具有三个属性，`name`，`props`和`data`：

```js
export default {
  name: '',
  props: {},
  data: () => (), };
```

1.  对于`name`属性，将值定义为`"PasswordInput"`：

```js
name: 'PasswordInput',
```

1.  对于`props`属性，添加两个属性，`value`和`label`，都是 JavaScript 对象。每个对象内部应该有三个属性：`type`，`default`和`required`。将`value.type`设置为`String`，`value.default`设置为`''`，`value.required`设置为`false`。然后，将`label.type`设置为`String`，`label.default`设置为`'Your Password'`，`label.required`设置为`false`：

```js
props: {
  value: {
  type: String,
  default: '',
  required: false,
  },
  label: {
  type: String,
  default: 'Your password',
  required: false,
  }, }, 
```

1.  最后，在`data`属性中，添加一个 JavaScript 对象作为返回值，其中`isPwd`值设置为`true`：

```js
data: () => ({
  isPwd: true, }),
```

#### 单文件组件<template>部分

现在我们将创建`PasswordInput`的`<template>`部分。按照以下说明来实现正确的输入组件：

1.  在`<template>`部分，创建一个`QInput`组件，并将`value`，`label`和`rules`属性添加为变量。`value`将绑定到`value`属性，`label`将绑定到`label`属性，`rules`将接收一个函数数组，用于执行对表单输入的基本验证。

1.  对于`type`属性，将其定义为一个变量，并将其设置为对`isPwd`的三元验证，在`"password"`和`"text"`之间切换。

1.  将`outlined`和`lazy-rules`属性设置为`true`。

1.  创建一个`hint`变量属性，并将其定义为三元运算符，它将检查当前值的长度是否匹配最小值大小；否则，它将向用户显示一条消息。

1.  然后，通过创建一个`v-on`指令并使用`$listeners`Vue API 作为值来为事件创建事件侦听器。

1.  在`QInput`模板内部，我们将添加一个子组件，该组件将占据一个命名插槽`v-slot:append`，该插槽将容纳一个`QIcon`组件。

1.  对于`QIcon`组件，定义`name`属性以对`isPwd`变量进行响应，因此当`isPwd`设置为`true`时，它将是`'visibility_off'`，或者当`isPwd`设置为`false`时，它将是`'visibility'`。将`class`属性定义为`"cursor-pointer"`，以便鼠标具有实际鼠标的外观和`"hover hand icon"`，并在`@click`事件侦听器上，我们将设置`isPwd`为当前`isPwd`的相反值。

完成所有步骤后，您的最终代码应该像这样：

```js
<template>
  <q-input
  :value="value"
  :type="isPwd ? 'password' : 'text'"
  :rules="[ val => val.length >= 8 || 'Your password need to have 8
             or more characters', val => val !== null && val !== '' || 
              'Please type your password']"
  :hint=" value.length < 8 ? 'Your password has a minimum of 8 
             characters' : ''"
  :label="label"
  outlined
  lazy-rules
  v-on="$listeners"
  >
  <template v-slot:append>
    <q-icon
      :name="isPwd ? 'visibility_off' : 'visibility'"
      class="cursor-pointer"
      @click="isPwd = !isPwd"
    />
  </template>
  </q-input> </template>
```

这是您的组件呈现的方式：

![](img/c6f0729d-0592-4f9b-b117-2ebad5254985.png)

### 创建 NameInput 组件

在我们创建的所有组件中，`NameInput`组件是最简单的，几乎没有改变`QInput`组件的行为，只是添加了验证规则和一些个性化。

#### 单文件组件<script>部分

在这部分，我们将创建`NameInput`组件的`<script>`部分：

1.  创建一个默认导出的 JavaScript 对象，有两个属性：`name`和`props`：

```js
export default {
  name: '',
  props: {},  }; 
```

1.  在`name`属性中，将值定义为`'NameInput'`：

```js
name: 'NameInput',
```

1.  在`props`属性中，添加一个属性`value`，作为一个 JavaScript 对象，里面有三个属性：`type`，`default`和`required`。将`value.type`设置为`String`，`value.default`设置为`**''**`，`value.required`设置为`false`：

```js
props: {
  value: {
  type: String,
  default: '',
  required: false,
  },  },
```

#### 单文件组件<template>部分

在这部分，我们将创建`NameInput`组件的`<template>`部分：

1.  在`<template>`部分，创建一个`QInput`组件，并添加`value`和`rules`属性作为变量。`value`将绑定到`value`属性，`rules`将接收一个函数数组，用于检查表单输入的基本验证。

1.  将`outlined`和`lazy-rules`属性设置为`true`，并将`label`属性定义为`"Your Name"`。

1.  最后，通过创建一个`v-on`指令并将`"$listeners"` Vue API 作为值来为事件创建事件监听器。

完成所有步骤后，你的最终代码应该像这样：

```js
<template>
  <q-input
  :value="value"
  :rules="[ val => (val && val.length > 0
    || 'Please type a valid Name')]"
  outlined
  label="Your Name"
  lazy-rules
  v-on="$listeners"
  /> </template>
```

这是你的组件渲染结果：

![](img/43ecd588-f672-498b-9372-34fc3df16edf.png)

### 创建 EmailInput 组件

在`EmailInput`组件中，我们需要特别注意规则验证的处理，因为我们需要检查正在输入的电子邮件是否是有效的电子邮件地址。

#### 单文件组件<script>部分

在这部分，我们将创建`EmailInput`组件的`<script>`部分：

1.  创建一个默认导出的 JavaScript 对象，有三个属性：`name`，`props`和`methods`：

```js
export default {
  name: '',
  props: {},
 methods: {}, }; 
```

1.  在`name`属性中，将值定义为`'EmailInput'`：

```js
name: 'EmailInput',
```

1.  在`props`属性中，添加一个属性`value`，作为一个 JavaScript 对象，里面有三个属性：`type`，`default`和`required`。将`value.type`设置为`String`，`value.default`设置为`**'**`，`value.required`设置为`false`：

```js
props: {
  value: {
  type: String,
  default: '',
  required: false,
  },  },
```

1.  在`methods`属性中，我们需要添加一个名为`validateEmail`的新方法，该方法接收一个名为`email`的参数。此方法将通过正则表达式测试接收到的参数，以检查它是否是有效的表达式，并返回结果：

```js
methods: {
  validateEmail(email) {
  const regex = /^(([^\s"(),.:;<>@[\\\]]+(\.[^\s"(),.:;
     <>@[\\\]]+)*)|(".+"))@((\[(?:\d{1,3}\.){3}\d{1,3}])|(([\dA-Za-
 z\-]+\.)+[A-Za-z]{2,}))$/;
  return regex.test(email);
  }, }, 
```

#### 单文件组件<template>部分

在这里，我们将创建`EmailInput`组件的`<template>`部分：

1.  在`<template>`部分，创建一个`QInput`组件，并将`value`和`rules`属性作为变量添加。`value`将绑定到`value`属性，`rules`将接收一个函数数组，用于执行基本验证表单输入的检查。

1.  将`outlined`和`lazy-rules`属性添加为`true`，将`label`属性定义为`"Your E-Mail"`，将`type`属性定义为`"email"`*.*

1.  最后，通过创建一个`v-on`指令并将`"$listeners"`作为值，为事件创建事件侦听器。

完成所有步骤后，您的最终代码应该像这样：

```js
<template>
  <q-input
  :value="value"
  :rules="[ val => (val && val.length > 0 && validateEmail(val)
    || 'Please type a valid E-mail')]"
  outlined
  type="email"
  label="Your E-mail"
  lazy-rules
  v-on="$listeners"
  /> </template>
```

这是您的组件呈现：

！[](assets/20533ea7-b331-4aac-b6d5-2b4f2d0d8743.png)

### 创建 AvatarInput 组件

对于`AvatarInput`组件，我们需要添加使用`AWS-Amplify Storage`API 驱动程序的逻辑。通过这样做，我们可以直接通过组件上传文件，并使逻辑和组件在整个应用程序中更具可重用性。

#### 单文件组件<script>部分

在这部分，我们将创建`AvatarInput`组件的`<script>`部分：

1.  从`quasar`包中导入`uid`和从`'src/driver/bucket'`中导入`uploadFile`：

```js
import { uid } from 'quasar';
import { uploadFile } from 'src/driver/bucket';
```

1.  创建一个默认导出的 JavaScript 对象，具有四个属性，`name`，`props`，`data`和`methods`：

```js
export default {
  name: '',
  props: {},
  data: () => ({})
 methods: {}, };
```

1.  在`name`属性中，将值定义为`"AvatarInput"`：

```js
name: 'AvatarInput',
```

1.  在`props`属性中，添加一个属性`value`，作为 JavaScript 对象，内部有三个属性 - `type`，`default`和`required`。将`value.type`设置为`Object`，将`value.default`设置为返回 JavaScript 对象的工厂函数，将`value.required`设置为`false`：

```js
props: {
  value: {
  type: Object,
  required: false,
  default: () => ({}),
  }, }, 
```

1.  在`data`属性中，我们需要添加六个新属性：`file`，`type`，`name`，`s3file`，`photoUrl`和`canUpload`：

+   `file`属性将是一个数组。

+   `type`，`name`和`photoUrl`将是字符串。

+   `canUpload`属性将是一个布尔值，定义为`false`。

+   `s3file`将是一个具有三个属性的 JavaScript 对象，`key`，`bucket`和`region`，它们都是字符串：

```js
data: () => ({
  file: [],
  type: '',
  name: '',
  s3file: {
  key: '',
  bucket: '',
  region: '',
  },
  photoUrl: '',
  canUpload: false, }),
```

1.  在`methods`属性上，我们需要添加一个名为`uploadFile`的新方法。这个方法将检查是否可以开始上传过程，然后调用`uploadFile`函数，传递`this.file`、`this.name`和`this.type`作为参数。在我们收到上传函数的响应后，我们将使用结果来定义`this.s3File`和`$emit`以及事件`'input'`。最后，我们将`this.canUpload`定义为`false`：

```js
async uploadFile() {
  try {
  if (this.canUpload) {
  const file = await uploadFile(this.file, this.name, 
         this.type);
  this.s3file = file;
  this.$emit('input', file);
  this.canUpload = false;
 } } catch (err) {
  console.error(err);
 } }, 
```

1.  最后，创建一个名为`getFile`的方法，它接收`$event`作为参数。在函数中，我们将把`this.type`定义为`$event.type`，将`this.name`定义为`uid`生成函数和文件名的连接。然后，我们将为`FileReader`实例创建一个监听器，它将把`that.photoURL`设置为读取的结果，并将`that.canUpload`设置为`true`：

```js
getFile($event) {
  this.type = $event.type;
  this.name = `${uid()}-${$event.name}`;
  const that = this;
  const reader = new FileReader();
  reader.onload = ({ target }) => {
  that.photoUrl = target.result;
  that.canUpload = true;
  };
  reader.readAsDataURL(this.file); },
```

#### 单文件组件<template>部分

现在是创建`AvatarInput`组件的`<template>`部分的时候了：

1.  创建一个`QFile`组件，将`v-model`指令绑定到`file`数据属性。将`outlined`和`bottom-slots`属性定义为`true`，并将`label`属性设置为`"Your Avatar"`。对于`class`属性，将其设置为`"q-pr-md"`，最后将`@input`事件监听器设置为目标`getFile`方法：

```js
<q-file
  v-model="file"
  outlined
 bottom-slots label="Your Avatar"
  class="q-pr-md"
  @input="getFile" >
</q-file>
```

1.  在`QFile`组件内部，我们将添加一个直接子组件，它将放置在一个命名为`v-slot:before`的插槽中，并且只有在数据属性中存在任何`photoUrl`时才会显示。在这个插槽中，我们将添加一个`QAvatar`组件，其子组件是一个`HTML img`标签，其中`src`属性绑定到`photoUrl`数据属性：

```js
<template
  v-if="photoUrl"
  v-slot:before >
 <q-avatar>
 <img :src="photoUrl">
 </q-avatar> </template>
```

1.  在我们创建的插槽之后，我们需要创建另一个插槽，现在放置在名为`v-slot:after`的插槽下面，里面有一个`QBtn`组件。`QBtn`将具有以下属性：`round`、`dense`、`flat`、`icon`定义为`"cloud_upload"`，并且`@click`事件监听器绑定到`uploadFile`方法：

```js
<template v-slot:after>
 <q-btn
  round
 dense flat icon="cloud_upload"
  @click="uploadFile"
  /> </template>
```

这是您的组件渲染结果：

![](img/a28e22e8-4f2a-4a9e-aef7-9680c4f9e122.png)

### 创建 avatar mixin

在这里，我们将创建一个简单的 mixin，它将用于在新的对话组件和联系人页面中显示用户头像，或者如果没有定义头像，则显示用户名的首字母：

1.  在`src`文件夹下创建一个名为`mixins`的新文件夹，然后创建一个名为`getAvatar.js`的文件，并打开它。

1.  从`driver/bucket`文件中导入`getFile`函数。

1.  导出一个带有`methods`属性的`default` JavaScript 对象。在`methods`属性内部，创建一个名为`getAvatar`的新函数。此函数将接收两个参数，`object`和`name`。对于此函数，我们将检查对象是否为`null`，以及是否有一个名字来显示初始字母。如果 JavaScript 对象中有属性，我们将返回`getFile`函数的结果，将`key`属性作为参数传递：

```js
import { uploadFile } from 'src/driver/bucket';   export default {
  methods: {
  async getAvatar(object, name) {
  const baseUrl = 'http://placehold.jp/350/9c27b0/FFFFFF/600x600.png?text=';    if (object === null && !name) return `${baseUrl}%20`;    if (!object && name) return `${baseUrl}${name.split('').shift()}`;    return getFile(object.key);
 }, }, }; 
```

### 创建 AvatarDisplay 组件

`AvatarDisplay`将负责处理用户名的检查和验证，因此我们不需要在每个需要使用它的页面上重新编写所有规则。

#### 单文件组件<script>部分

在这里，我们将创建`AvatarDisplay`组件的`<script>`部分：

1.  在`components`文件夹中创建一个名为`AvatarDisplay.vue`的新文件，并打开它。

1.  创建一个带有以下属性的`export default` JavaScript 对象：`name`，`props`，`mixins`，`beforeMount`，`data`，`watch`，`computed`和`methods`：

```js
import { QImg } from 'quasar'; import getAvatar from 'src/mixins/getAvatar';   export default {
  name: '',
  props: {},
  mixins: [],
  async beforeMount() {},
  data: () => ({}),
  watch: {},
  computed: {},
  methods: {}, };
```

1.  对于`name`属性，将其定义为`"AvatarDisplay"`：

```js
name: 'UsernameInput',
```

1.  对于`props`属性，将其定义为 JavaScript 对象，并添加三个新属性，分别称为`avatarObject`，`name`和`tag`。`avatarObject`属性将是一个具有`type`，`default`和`required`属性的 JavaScript 对象。`name`和`tag`属性需要定义为`String`，`default`为`''`，`required`为`false`。对于`tag`属性，我们将将默认属性设置为`'q-img'`：

```js
props: {
  avatarObject: {
  type: Object,
  required: false,
  default: () => ({}),
  },
  name: {
  type: String,
  required: false,
  default: '',
  },
  tag: {
  type: String,
  required: false,
  default: 'q-img',
  }, },
```

1.  对于`mixins`属性，我们将在数组中添加导入的`getAvatar` mixin：

```js
mixins: [getAvatar],
```

1.  现在，在`data`中返回 JavaScript 对象，我们将创建一个名为`src`的属性，其默认值为`''`：

```js
data: () => ({
  src: '', }),
```

1.  然后对于`computed`属性，创建一个名为 components 的新属性，返回一个三元运算符，检查`tag`属性是否等于`'q-img'`，并返回 Quasar 中导入的`QImg`组件；如果不是，则返回`'img'`标签：

```js
computed: {
  componentIs() {
  return this.tag === 'q-img' ? QImg : 'img';
  }, },
```

1.  在`methods`属性中，创建一个名为`updateSrc`的新方法。在这个方法中，我们将`src`定义为`getAvatar`方法的结果。我们将函数的参数传递给`avatarObject`和`name`属性：

```js
methods: {
  async updateSrc() {
  this.src = await this.getAvatar(this.avatarObject, this.name);
  }, },
```

1.  在`beforeMount`生命周期钩子中，我们将调用`updateSrc`方法：

```js
async beforeMount() {
  await this.updateSrc(); },
```

1.  最后，对于`watch`属性，创建两个属性，`avatarObject`和`name`。对于`avatarObject`属性，将其定义为一个具有两个属性`handler`和`deep`的 Javascript 对象。在`deep`属性中，将其定义为`true`，在`handler`属性上，将其定义为调用`updateSrc`方法的函数。然后在`name`属性上，创建一个`handler`属性，定义为调用`updateSrc`方法的函数：

```js
watch: {
  avatarObject: {
  async handler() {
    await this.updateSrc();
  },
  deep: true,
  },
  name: {
  async handler() {
    await this.updateSrc();
  },
  }, },
```

#### 单文件组件<template>部分

在这里，我们将创建`AvatarDisplay`组件的`<template>`部分：

1.  在`<template>`部分，创建一个`component`元素。创建两个动态属性，`src`和`is`。现在，`src`将绑定到数据`src`，而`is`属性将绑定到`componentIs`计算属性。最后，创建一个`spinner-color`属性，并将其定义为`'primary'`。

完成所有步骤后，您的最终代码应该像这样：

```js
<template>
  <component
  :src="src"
  :is="componentIs"
  spinner-color="primary"
  /> </template>
```

## 它是如何工作的...

在这个示例中，我们学习了如何通过包装 Quasar Framework 的组件并在其上添加自定义逻辑来为我们的应用程序创建自定义组件。

这种技术允许开发独特的组件，可以在应用程序中重复使用，而无需重写逻辑使其正常工作。

对于`Usernameinput`和`Nameinput`，我们在`QInput`组件周围创建了一个包装器，添加了验证规则和文本，以便更轻松地开发和重用组件，而无需添加更多逻辑。

在`PasswordInput`组件中，我们添加了控制密码可见性的逻辑，该逻辑会更改输入的类型，并自定义了`QInput`组件，以便有一个特殊按钮来触发可见性控制。

对于`EmailInput`，我们需要基于正则表达式创建自定义验证规则，检查输入的电子邮件是否是有效的电子邮件，并防止用户意外输入无效的电子邮件。

最后，在`AvatarInput`中，使用`QFile`组件，我们创建了一个自定义输入，当浏览器读取文件并将文件上传到 AWS Amplify Storage 时，自动上传文件，并在文件上传后将文件 URL 返回给应用程序。

## 另请参阅

+   在[`quasar.dev/vue-components/input`](https://quasar.dev/vue-components/input)找到有关 Quasar 输入组件的更多信息。

+   在[`quasar.dev/vue-components/file-picker`](https://quasar.dev/vue-components/file-picker)找到有关 Quasar 文件选择器组件的更多信息。

# 创建应用程序布局

在我们的应用程序中，我们将使用一个基于布局组件的父路由的`vue-router`结构，以及我们正在尝试访问的页面的最终路由。

这种模式改进了我们应用程序的开发，因为我们可以在`vue-router`上创建父子责任划分。

在本教程中，我们将学习如何创建自定义布局，将我们的页面包装在`vue-router`的父子结构中。

## 准备工作

本教程的先决条件如下：

+   最后的教程项目

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   `@aws-amplify/cli`

+   `@quasar/cli`

要开始我们的应用程序自定义布局，我们将继续使用在*为应用程序创建自定义输入*中创建的项目。

## 如何做...

准备好我们的组件后，我们可以开始创建用于用户登录或注册到聊天应用程序或编辑其信息的布局，以及用于聊天消息页面的聊天布局。

### 创建基本布局

在我们的应用程序中，我们将使用一种基本布局的技术。它将成为应用程序所有内容的包装器。此布局将应用在布局执行中没有自定义更改的地方。

#### 单文件组件<script>部分

在这部分，我们将创建基本布局的<script>部分：

1.  在`layouts`文件夹中创建一个名为`Base.vue`的新文件。

1.  使用 JavaScript 对象创建一个`export default`实例，其中`name`属性定义为`'BaseLayout'`：

```js
<script> export default {
  name: 'BaseLayout', }; </script> 
```

#### 单文件组件<template>部分

在这里，我们将创建基本布局的<template>部分：

1.  创建一个`QLayout`组件，其中`view`属性定义为`"hHh Lpr lff"`：

```js
<q-layout view="hHh Lpr lff"> </q-layout>
```

1.  在`QLayout`组件内部，我们需要添加一个带有`elevated`属性的`QHeader`组件：

```js
<q-header elevated> </q-header>
```

1.  在`QHeader`组件中，我们将添加一个`QToolbar`组件，其中包含一个`QToolbarTitle`组件作为子元素，以文本作为插槽占位符：

```js
<q-toolbar>
 <q-toolbar-title>
  Chat App
  </q-toolbar-title> </q-toolbar>
```

1.  在`QHeader`组件之后，创建一个带有`RouterView`组件的`QPageContainer`组件作为直接子元素：

```js
<q-page-container>
 <router-view /> </q-page-container>
```

### 创建聊天布局

对于我们应用程序的经过身份验证的页面，我们将使用不同的页面布局，其中将有按钮供用户注销、管理其用户并浏览应用程序。

#### 单文件组件<script>部分

让我们创建聊天布局的<script>部分：

1.  在`layouts`文件夹中创建一个名为`Chat.vue`的新文件。

1.  从`src/driver/auth.js`中导入`signOut`函数：

```js
import {signOut,} from 'src/driver/auth';
```

1.  创建一个`export default`实例，包括一个 JavaScript 对象，其中包括两个属性：一个名为`name`的属性，定义为`'ChatLayout'`，另一个名为`methods`的属性：

```js
export default {
  name: 'ChatLayout',
  methods: {   }, };
```

1.  在`methods`属性中，添加一个名为`logOff`的新异步函数；在这个函数中，我们将执行`signOut`函数，并在其后重新加载浏览器：

```js
async logOff() {
  await signOut();
  window.location.reload(); }
```

#### 单文件组件<template>部分

在这里，我们将创建聊天布局的`<template>`部分：

1.  创建一个带有`view`属性定义为`"hHh Lpr lff"`的`QLayout`组件：

```js
<q-layout view="hHh Lpr lff"> </q-layout>
```

1.  在`QLayout`组件内部，我们需要添加一个带有`elevated`属性的`QHeader`组件：

```js
<q-header elevated> </q-header> 
```

1.  对于`QHeader`组件，我们将添加一个`QToolbar`组件，其中包含一个`QToolbarTitle`组件作为子元素，文本作为插槽占位符：

```js
<q-toolbar>
 <q-toolbar-title>
  Chat App
  </q-toolbar-title> </q-toolbar>
```

1.  对于`QToolbar`组件，在`QToolbarTitle`组件之前，我们将添加一个带有`dense`、`flat`和`round`属性定义为`true`的`QBtn`组件。在`icon`属性中，我们将添加一个三元表达式，验证`$route.meta.goBack`是否存在，以显示*back*图标或*person*图标。最后，对于`to`属性，我们将做同样的操作，但值将是`$route.meta.goBack`或一个具有`name`属性为`Edit`的 JavaScript 对象。

```js
<q-btn
  dense
  flat
  round
  replace
  :icon="$route.meta.goBack ? 'keyboard_arrow_left' : 'person'"
  :to="$route.meta.goBack ? $route.meta.goBack : {name: 'Edit'}" />
```

1.  在`QToolbarTitle`组件之后，我们将添加一个带有`dense`、`flat`和`round`属性的`QBtn`组件，这些属性被定义为`true`。对于`icon`属性，我们将定义为`exit_to_app`，对于`@click`指令，我们将传递`logOff`方法：

```js
<q-btn
  dense
 flat round icon="exit_to_app"
  @click="logOff" /> 
```

1.  在`QHeader`组件之后，创建一个带有`RouterView`组件作为直接子元素的`QPageContainer`组件：

```js
<q-page-container>
 <router-view /> </q-page-container>
```

## 工作原理...

在这个示例中，我们学习了如何创建我们将在应用程序中使用的布局。这些布局是我们应用程序页面的包装器，使得在需要时可以轻松添加常见项目，如菜单、头部项目和页脚项目，而无需编辑每个页面文件。

对于创建的两种布局，我们使用了常见的`QLayout`、`QHeader`和`QToolbarTitle`组件。这些组件创建了页面的结构，包括布局容器、头部容器和自定义头部工具栏。

最后，对于聊天布局，我们在页眉菜单中添加了两个按钮：一个按钮可以是返回按钮或菜单，具体取决于路由中是否存在该参数；另一个是注销按钮，用户可以用它来从应用程序中注销。

## 另请参阅

+   关于 Quasar Framework `QLayout`组件的更多信息，请访问[`quasar.dev/layout/layout`](https://quasar.dev/layout/layout)。

+   关于 Quasar Framework `QHeader`组件的更多信息，请访问[`quasar.dev/layout/header-and-footer`](https://quasar.dev/layout/header-and-footer)。

+   关于 Quasar Framework `QPage`组件的更多信息，请访问[`quasar.dev/layout/page`](https://quasar.dev/layout/page)。

+   关于 Quasar Framework `QBtn`组件的更多信息，请访问[`quasar.dev/vue-components/button`](https://quasar.dev/vue-components/button)。
