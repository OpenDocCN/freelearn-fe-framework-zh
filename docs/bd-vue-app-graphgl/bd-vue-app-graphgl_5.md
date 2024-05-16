# 第五章：创建用户 Vuex 模块、页面和路由

现在，是时候给应用程序一个可识别的面孔了。在本章中，我们将开始开发用户与应用程序之间的交互。

我们将利用我们从前面章节中收集的知识，通过使用自定义业务规则、Vuex 数据存储、特殊应用程序布局和用户可以交互的页面，将这个应用程序变得生动起来。

在本章中，我们将学习如何创建用户 Vuex 模块，以便我们可以存储和管理与用户、用户注册、登录、验证和编辑页面相关的一切。

在本章中，我们将涵盖以下配方：

+   在您的应用程序中创建用户 Vuex 模块

+   为您的应用程序创建用户页面和路由

让我们开始吧！

# 技术要求

在本章中，我们将使用**Node.js**、**AWS Amplify**和**Quasar Framework**。

**注意，Windows 用户！** 您需要安装一个名为`windows-build-tools`的`npm`包，以便能够安装所需的软件包。要做到这一点，以管理员身份打开 PowerShell 并执行`> npm install -g windows-build-tools`命令。

要安装**Quasar Framework**，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> npm install -g @quasar/cli
```

要安装**AWS Amplify**，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> npm install -g @aws-amplify/cli
```

# 在您的应用程序中创建用户 Vuex 模块

现在，是时候开始在我们的应用状态管理器或 Vuex 中存储数据了。在应用上下文中，存储的所有数据都保存在命名空间中。

在这个配方中，我们将学习如何创建用户 Vuex 模块。利用我们从上一章中获得的知识，然后创建动作来创建新用户、更新他们的数据、验证用户、在 Amplify 上登录用户，并列出应用程序中的所有用户。

## 准备工作

本配方的先决条件是 Node.js 12+。

本章所需的 Node.js 全局对象如下：

+   `@aws-amplify/cli`

+   `@quasar/cli`

要开始我们的用户 Vuex 存储模块，我们将继续使用我们在第四章中创建的项目，即*创建自定义应用程序组件和布局*。

这个食谱将使用 GraphQL 查询和突变完成，以及它们的驱动程序，这些驱动程序是在第三章的*创建您的第一个 GraphQL API*和*为您的应用程序创建 AWS Amplify 驱动程序*食谱中编写的。

## 如何做...

我们将把用户的 Vuex 模块的创建分为五个部分：创建**state**，**mutations**，**getters**和**actions**，然后将模块添加到 Vuex 中。

### 创建用户的 Vuex 状态

要在 Vuex 模块上存储数据，我们需要一个将为我们存储数据的状态。按照以下步骤创建用户状态：

1.  在`store`文件夹中，创建一个名为`user`的新文件夹。在内部，创建一个名为`state.js`的新文件并打开它。

1.  创建一个名为`createState`的新函数，它返回一个 JavaScript 对象，提供`id`，`username`，`email`，`name`，`avatar`，`password`，`loading`，`validated`和`error`属性。`id`，`username`，`email`，`name`和`password`属性将被定义为空字符串，而`loading`和`validated`属性将被定义为`false`。`error`将被定义为`undefined`，`avatar`是一个具有三个属性的 JavaScript 对象-`key`，`bucket`和`region`：

```js
export function createState() {
  return {
  id: '',
  username: '',
  email: '',
  name: '',
  avatar: {
  key: '',
  bucket: '',
  region: '',
  },
  password: '',
  loading: false,
  validated: false,
  error: undefined,
  }; }
```

1.  最后，为了将状态导出为单例并将其作为 JavaScript 对象可用，我们需要`export default`执行`createState`函数：

```js
export default createState();
```

### 创建用户的 Vuex mutations

要在状态上保存任何数据，Vuex 需要一个 mutation。按照以下步骤创建将管理此模块的 mutations 的用户 mutation：

1.  在`store/user`文件夹内创建一个名为`types.js`的新文件并打开它。

1.  在文件中，导出一个默认的 JavaScript 对象，提供`CREATE_USER`，`SET_USER_DATA`，`CLEAR_USER`，`USER_VALIDATED`，`LOADING`和`ERROR`属性。值与属性相同，但格式为字符串。

```js
export default {
  CREATE_USER: 'CREATE_USER',   SET_USER_DATA: 'SET_USER_DATA',
  CLEAR_USER: 'CLEAR_USER',
  USER_VALIDATED: 'USER_VALIDATED',
  LOADING: 'LOADING',
  ERROR: 'ERROR', };
```

1.  在`store/user`文件夹内创建一个名为`mutations.js`的新文件并打开它。

1.  导入新创建的`types.js`文件和`state.js`中的`createState`JavaScript 对象：

```js
import MT from './types';  import { createState } from './state';
```

1.  创建一个名为`setLoading`的新函数，状态作为第一个参数。在内部，我们将设置`state.loading`为`true`：

```js
function setLoading(state) {
 state.loading = true; }
```

1.  创建一个名为`setError`的新函数，以`state`作为第一个参数，并以`error`作为第二个参数，其默认值为`new Error()`。在内部，我们将将`state.error`设置为`error`，将`state.loading`设置为`false`：

```js
function setError(state, error = new Error()) {
 state.error = error;
  state.loading = false; }
```

1.  创建一个名为`createUser`的新函数，以`state`作为第一个参数，并以 JavaScript 对象作为第二个参数。这个 JavaScript 对象将提供`id`、`email`、`password`、`name`和`username`属性。所有属性都将是空字符串。在函数内部，我们将定义`state`属性为函数参数中收到的属性：

```js
function createUser(state, {
 id = '',
  email = '',
  password = '',
  name = '',
  username = '', }) {
 state.username = username;
  state.email = email;
  state.name = name;
  state.id = id;
  state.password = window.btoa(password);
  state.loading = false; }
```

1.  创建一个名为`validateUser`的新函数，以`state`作为第一个参数。在其中，我们将将`state.validated`属性设置为`true`，删除`state.password`属性，并将`state.loading`属性设置为`false`：

```js
function validateUser(state) {
 state.validated = true;
  delete state.password;
  state.loading = false; }
```

1.  创建一个名为`setUserData`的新函数，以`state`作为第一个参数，并以 JavaScript 对象作为第二个参数。这个对象将提供`id`、`email`、`password`、`name`和`username`属性。它们都将是空字符串。`avatar`是一个具有三个属性的 JavaScript 对象：`key`、`bucket`和`region`。在函数内部，我们将定义`state`属性为函数参数中收到的属性：

```js
function setUserData(state, {
 id = '',
  email = '',
  name = '',
  username = '',
  avatar = {
  key: '',
  bucket: '',
  region: '',
  }, }) {
 state.id = id;
  state.email = email;
  state.name = name;
  state.username = username;
  state.avatar = avatar || {
  key: '',
  bucket: '',
  region: '',
  };    delete state.password;    state.validated = true;
  state.loading = false; }
```

1.  创建一个名为`clearUser`的新函数，以`state`作为第一个参数。然后，在其中的函数中，我们将从`createState`函数获取一个新的干净的`state`，并迭代当前的`state`，将`state`属性的值重新定义为默认值：

```js
function clearUser(state) {
  const newState = createState();    Object.keys(state).forEach((key) => {
 state[key] = newState[key];
  }); }
```

1.  最后，导出一个默认的 JavaScript 对象，其中键是导入的变异类型，值是对应于每种类型的函数：

+   将`MT.LOADING`设置为`setLoading`

+   将`MT.ERROR`设置为`setError`

+   将`MT.CREATE_USER`设置为`createUser`

+   将`MT.USER_VALIDATED`设置为`validateUser`

+   将`MT.SET_USER_DATA`设置为`setUserData`

+   将`MT.CLEAR_USER`设置为`clearUser`

```js
export default {
 [MT.LOADING]: setLoading,
  [MT.ERROR]: setError,
  [MT.CREATE_USER]: createUser,
  [MT.USER_VALIDATED]: validateUser,
  [MT.SET_USER_DATA]: setUserData,
  [MT.CLEAR_USER]: clearUser, };  
```

### 创建用户 Vuex getter

要访问存储在状态中的数据，我们需要创建一些“getter”。按照以下步骤为用户模块创建“getter”：

在`getter`函数中，该函数将始终接收到 Vuex`store`的当前`state`作为第一个参数。

1.  在`store/user`文件夹内创建一个名为`getters.js`的新文件。

1.  创建一个名为`getUserId`的新函数，返回`state.id`：

```js
const getUserId = (state) => state.id;
```

1.  创建一个名为`getUserEmail`的新函数，返回`state.email`：

```js
const getUserEmail = (state) => state.email;
```

1.  创建一个名为`getUserUsername`的新函数，返回`state.username`：

```js
const getUserUsername = (state) => state.username;
```

1.  创建一个名为`getUserAvatar`的新函数，返回`state.avatar`：

```js
const getUserAvatar = (state) => state.avatar;
```

1.  创建一个名为`getUser`的新函数，返回一个提供`id`、`name`、`username`、`avatar`和`email`属性的 JavaScript 对象。这些属性的值将对应于`state`：

```js
const getUser = (state) => ({
  id: state.id,
  name: state.name,
  username: state.username,
  avatar: state.avatar,
  email: state.email, });
```

1.  创建一个名为`isLoading`的新函数，返回`state.loading`：

```js
const isLoading = (state) => state.loading;
```

1.  创建一个名为`hasError`的新函数，返回`state.error`：

```js
const hasError = (state) => state.error;
```

1.  最后，导出一个带有创建的函数（`getUserId`、`getUserEmail`、`getUserUsername`、`getUserAvatar`、`getUser`、`isLoading`和`hasError`）作为属性的`default`JavaScript 对象：

```js
export default {
  getUserId,
  getUserEmail,
  getUserUsername,
  getUserAvatar,
  getUser,
  isLoading,
  hasError, };
```

### 创建用户 Vuex 操作

按照以下步骤创建用户 Vuex 操作：

1.  在`store/user`文件夹内创建一个名为`actions.js`的文件并打开它。

1.  首先，我们需要导入这里将要使用的函数、枚举和类。

+   从`aws-amplify`npm 包中导入`graphqlOperation`。

+   从 GraphQL 查询中导入`getUser`和`listUsers`。

+   从 GraphQL 变异中导入`createUser`和`updateUser`。

+   从`driver/auth.js`中导入`signUp`、`validateUser`、`signIn`、`getCurrentAuthUser`和`changePassword`函数。

+   从`driver/appsync`导入`AuthAPI`。

+   从`./types.js`导入 Vuex 变异类型：

```js
import { graphqlOperation } from 'aws-amplify';
import { getUser, listUsers } from 'src/graphql/queries';
import { createUser, updateUser } from 'src/graphql/mutations';
import { AuthAPI } from 'src/driver/appsync';
import {
  signUp,
  validateUser,
  signIn,
  getCurrentAuthUser,
  changePassword,
} from 'src/driver/auth';
import MT from './types';

```

1.  创建一个名为`initialLogin`的新异步函数。此函数将接收一个 JavaScript 对象作为第一个参数。这将提供一个`commit`属性。在这个函数中，我们将获取当前认证的用户，从 GraphQL API 获取他们的数据，并将用户数据提交到 Vuex 存储中：

```js
async function initialLogin({ commit }) {
  try {
  commit(MT.LOADING);    const AuthUser = await getCurrentAuthUser();    const { data } = await AuthAPI.graphql(graphqlOperation(getUser, {
    id: AuthUser.username,
  }));    commit(MT.SET_USER_DATA, data.getUser);    return Promise.resolve(AuthUser);
  } catch (err) {
  commit(MT.ERROR, err);
  return Promise.reject(err);
  } }
```

1.  创建一个名为`signUpNewUser`的新异步函数。此函数将接收一个带有`commit`属性的 JavaScript 对象作为第一个参数。第二个参数也是一个 JavaScript 对象，但具有`email`、`name`和`password`属性。在这个函数中，我们将执行`auth.js`驱动器中的`signUp`函数来注册并在 AWS Cognito 用户池中创建用户，然后将用户数据提交到 Vuex 存储中：

```js
async function signUpNewUser({ commit }, {
  email = '',
  name = '',
  username = '',
  password = '', }) {
  try {
  commit(MT.LOADING);    const userData = await signUp(email, password);    commit(MT.CREATE_USER, {
    id: userData.userSub,
    email,
    password,
    name,
    username,
  });    return Promise.resolve(userData);
  } catch (err) {
  commit(MT.ERROR, err);
  return Promise.reject(err);
  } }
```

1.  创建一个名为`createNewUser`的新异步函数。这个函数将接收一个 JavaScript 对象作为第一个参数，其中包含`commit`和`state`属性。对于第二个参数，函数将接收一个`code`字符串。在这个函数中，我们将从`state`中获取用户数据，并执行`auth.js`驱动器中的`validateUser`函数，以检查用户是否是 AWS Cognito 用户池中的有效用户。然后，我们将执行`auth.js`中的`signIn`函数，将`email`和`password`作为参数传递，需要将`password`转换为加密的 base64 字符串，然后发送到函数。之后，我们将获取经过身份验证的用户数据，并将其发送到 GraphQL API 以创建一个新用户：

```js
async function createNewUser({ commit, state }, code) {
  try {
  commit(MT.LOADING);
  const {
    email,
    name,
    username,
    password,
  } = state;
  const userData = await validateUser(email, code);    await signIn(`${email}`, `${window.atob(password)}`);    const { id } = await getCurrentAuthUser();    await AuthAPI.graphql(graphqlOperation(
    createUser,
    {
      input: {
        id,
        username,
        email,
        name,
      },
    },
  ));    commit(MT.USER_VALIDATED);    return Promise.resolve(userData);
  } catch (err) {
  commit(MT.ERROR, err);
  return Promise.reject(err);
  } }
```

1.  创建一个名为`signInUser`的新异步函数。这个函数将接收一个 JavaScript 对象作为第一个参数，其中包含`commit`和`dispatch`属性。第二个参数也是一个 JavaScript 对象，包含`email`和`password`属性。在这个函数内部，我们将执行`auth.js`驱动器中的`signIn`函数，将`email`和`password`作为参数传递，然后触发`initialLogin` Vuex 动作：

```js
async function signInUser({ commit, dispatch }, { email = '', password = '' }) {
  try {
  commit(MT.LOADING);    await signIn(`${email}`, `${password}`);    await dispatch('initialLogin');    return Promise.resolve(true);
  } catch (err) {
  commit(MT.ERROR);
  return Promise.reject(err);
  } }
```

1.  创建一个名为`editUser`的新异步函数。这个函数将接收一个 JavaScript 对象作为第一个参数，其中包含`commit`和`state`属性。第二个参数也是一个 JavaScript 对象，包含`username`、`name`、`avatar`、`password`和`newPassword`属性。在这个函数内部，我们将合并`state`的值和作为参数接收到的新值。然后将它们发送到 GraphQL API 以更新用户信息。然后，我们将检查是否`password`和`newPasssword`属性都填写了。如果是，我们将执行`auth.js`驱动器中的`changePassword`函数，以在 AWS Cognito 用户池中更改用户的密码：

```js
async function editUser({ commit, state }, {
  username = '',
  name = '',
  avatar = {
  key: '',
  bucket: '',
  region: '',
  },
  password = '',
  newPassword = '', }) {
  try {
  commit(MT.LOADING);    const updateObject = {
    ...{
      name: state.name,
      username: state.username,
      avatar: state.avatar,
    },
    ...{
      name,
      username,
      avatar,
    },
  };    const { data } = await AuthAPI.graphql(graphqlOperation(updateUser,
    { input: { id: state.id, ...updateObject } }));    if (password && newPassword) {
    await changePassword(password, newPassword);
  }    commit(MT.SET_USER_DATA, data.updateUser);    return Promise.resolve(data.updateUser);
  } catch (err) {
  return Promise.reject(err);
  } }
```

1.  创建一个名为`listAllUsers`的新异步函数。这个函数将获取数据库中的所有用户并返回一个列表：

```js
async function listAllUsers() {
  try {
  const {
    data: {
      listUsers: {
        items: usersList,
      },
    },
  } = await AuthAPI.graphql(graphqlOperation(
    listUsers,
  ));    return Promise.resolve(usersList);
  } catch (e) {
  return Promise.reject(e);
  } }
```

1.  最后，我们将导出所有默认创建的函数：

```js
export default {
  initialLogin,
  signUpNewUser,
  createNewUser,
  signInUser,
  editUser,
  listAllUsers, };
```

### 将用户模块添加到 Vuex

按照以下步骤将创建的用户模块导入到 Vuex 状态中：

1.  在`store/user`文件夹内创建一个名为`index.js`的新文件。

1.  导入我们刚刚创建的`state.js`、`actions.js`、`mutation.js`和`getters.js`文件：

```js
import state from './state'; import actions from './actions'; import mutations from './mutations'; import getters from './getters';
```

1.  创建一个带有 JavaScript 对象的`export default`，提供`state`、`actions`、`mutations`、`getters`和`namespaced`（设置为`true`）属性：

```js
export default {
  namespaced: true,
  state,
  actions,
  mutations,
  getters, };
```

1.  打开`store`文件夹中的`index.js`文件。

1.  在`store/user`文件夹中导入新创建的`index.js`：

```js
import Vue from 'vue'; import Vuex from 'vuex'; import user from './user';
```

1.  在新的 Vuex 类实例化中，我们需要添加一个名为`modules`的新属性，并将其定义为 JavaScript 对象。然后，我们需要添加一个新的`user`属性-这将自动用作值，因为它与上一步中导入的 User 模块具有相同的名称：

```js
export default function (/* { ssrContext } */) {
  const Store = new Vuex.Store({
  modules: {
  user,
  },
  strict: process.env.DEV,
  });    return Store; }
```

## 工作原理...

当声明你的 Vuex 存储时，你需要创建三个主要属性：`state`、`mutations`和`actions`。这些属性作为一个单一的结构，通过注入的`$store`原型或导出的`store`变量绑定到 Vue 应用程序。

`state`是一个集中的对象，保存着你的信息，并使其可以被`mutations`、`actions`或`components`使用。改变`state`总是需要通过`mutation`执行同步函数。

`mutation`是一个同步函数，可以改变`state`并被追踪。这意味着在开发时，你可以在 Vuex 存储中时间旅行通过所有执行的`mutations`。

`action`是一个异步函数，可以用来保存业务逻辑、API 调用、分发其他`actions`和执行`mutations`。这些函数是当你需要对 Vuex 存储进行更改时的常见入口点。

Vuex 存储的简单表示可以在以下图表中看到：

![](img/f7d5f47b-8fd3-4f9e-99e2-1d7df4243318.png)

在这个示例中，我们创建了 User Vuex 模块。该模块包括了所有的业务逻辑，将帮助我们在应用程序中管理用户，从创建新用户到更新用户。

当我们查看 Vuex actions 时，我们使用 AppSync API 客户端来获取数据并将其发送到我们的 GraphQL API。我们使用了由 Amplify CLI 创建的查询和 mutations。为了能够与 GraphQL API 通信，以便我们可以更新用户，我们从*为应用程序创建 AWS Amplify driver*配方中获取了我们在 Auth Driver 中使用的数据，第三章，*设置我们的聊天应用程序 - AWS Amplify 环境和 GraphQL*。

这些 API 请求由 Vuex mutations 操纵，并存储在 Vuex 状态中，我们可以通过 Vuex getter 访问。

## 另请参阅

+   您可以在[`aws-amplify.github.io/docs/js/api#amplify-graphql-client`](https://aws-amplify.github.io/docs/js/api#amplify-graphql-client)找到有关 Amplify 的 AppSync GraphQL 客户端的更多信息。

+   您可以在[https:/​/​vuex.​vuejs.​org/​](https://vuex.vuejs.org/)找到有关 Vuex 的更多信息。

+   您可以在[`vuex.vuejs.org/guide/modules.html`](https://vuex.vuejs.org/guide/modules.html)找到有关 Vuex 模块的更多信息

# 为您的应用程序创建用户页面和路由

在使用 Vue 应用程序时，您需要一种管理用户位置的方法。您可以使用动态组件来处理这个问题，但最好的方法是通过路由管理。

在这个食谱中，我们将学习如何创建我们的应用程序页面，其中包含每个路由所需的业务规则。然后，我们将使用路由管理来处理一切。

## 准备工作

此食谱的先决条件如下：

+   我们在上一个食谱中创建的项目

+   Node.js 12+

此食谱所需的 Node.js 全局对象如下：

+   `@aws-amplify/cli`

+   `@quasar/cli`

要开始我们的用户页面和路由，我们将继续使用在*在应用程序上创建用户 Vuex 模块*食谱中创建的项目。

## 如何做...

在这个食谱中，我们将为我们的应用程序创建所有我们需要的用户页面：登录页面、注册页面和用户编辑页面。

### 将对话框插件添加到 Quasar

使用 Quasar 对话框插件，我们需要将其添加到配置文件中。

打开项目根文件夹内的`quasar.conf.js`文件，并找到`framework`属性。然后，在`plugins`属性中，将`'Dialog'`字符串添加到数组中，以便 Quasar 在启动应用程序时加载`Dialog`插件：

```js
framework: {
 ...
  plugins: [
  'Dialog',
 ],
 ...
},
```

### 创建用户登录页面

对于用户登录页面，我们将使用之前创建的两个组件：`PasswordInput`和`EmailInput`。

#### 单文件组件`<script>`部分

现在是创建用户登录页面的`<script>`部分的时候了：

1.  在`src/pages`文件夹中，打开`Index.vue`文件。

1.  从`vuex`包中导入`mapActions`和`mapGetters`函数：

```js
import { mapActions, mapGetters } from 'vuex';
```

1.  创建一个具有五个属性的`export default` JavaScript 对象；即`name`（定义为`'Index'`），`components`，`data`，`computed`和`methods`：

```js
export default {
  name: 'Index',
  components: {
  },
  data: () => ({   }),
  computed: {   },
  methods: {   }, }; 
```

1.  在`components`属性中，添加两个名为`PasswordInput`和`EmailInput`的新属性。将`PasswordInput`定义为一个匿名函数，其返回值为`import('components/PasswordInput')`，并将`EmailInput`定义为一个匿名函数，其返回值为`import('components/EmailInput')`：

```js
components: {
  PasswordInput: () => import('components/PasswordInput'),
  EmailInput: () => import('components/EmailInput'), },
```

1.  在`data`属性中，我们将返回一个提供两个属性`email`和`password`的 JavaScript 对象，它们都将是空字符串：

```js
data: () => ({
  email: '',
  password: '', }),
```

1.  在`computed`属性中，我们将解构`mapGetters`函数，将我们想要的模块的命名空间作为第一个参数（在本例中为`'user'`）。我们将把我们想要导入的`getters`数组（在本例中为`isLoading`）作为第二个参数传递进去：

```js
computed: {
  ...mapGetters('user', [
  'isLoading',
  'getUserId',
  ]), },
```

1.  在`beforeMount`生命周期钩子上，我们将添加一个`if`语句，检查`getUserId`是否为真，并将用户重定向到`Contacts`路由。

```js
async beforeMount() {
  if (this.getUserId) {
  await this.$router.replace({ name: 'Contacts' });
  } }, 
```

1.  最后，在`methods`属性中，我们将解构`mapActions`函数，将我们想要的模块的命名空间（在本例中为`'user'`）作为第一个参数传递进去。对于第二个参数，我们将使用一个包含我们想要导入的`actions`的数组（在这种情况下，这是`signInUser`）。接下来，我们需要添加异步的`onSubmit`方法，该方法将调度`signInUser`并将用户发送到`Contacts`路由，以及`createAccount`方法，该方法将用户发送到`SignUp`路由：

```js
methods: {
  ...mapActions('user', [
  'signInUser',
  ]),
  async onSubmit() {
  try {
    await this.signInUser({
      email: this.email,
      password: this.password,
    });
    await this.$router.push({ name: 'Contacts' });
  } catch (e) {
    this.$q.dialog({
      message: e.message,
    });
  }
  },
  createAccount() {
  this.$router.push({ name: 'SignUp' });
  }, },
```

#### 单文件组件`<template>`部分

现在，我们需要添加`<template>`部分来完成我们的页面：

1.  创建一个名为`QPage`的组件，其`class`属性定义为`"bg-grey-1 flex flex-center"`：

```js
<q-page padding class="bg-grey-1 flex flex-center">
</q-page>
```

1.  在`QPage`组件内部，创建一个`QCard`组件，其`style`属性定义为`"width: 350px"`：

```js
<q-card style="width: 350px">  </q-card> 
```

1.  在`QCard`组件内部，创建一个带有`class`属性定义为`no-margin`的`QCardSection`和一个`h6`子组件：

```js
<q-card-section>
  <h6 class="no-margin">Chat Application</h6>  </q-card-section>
```

1.  现在，创建一个`QCardSection`，其中包含一个`QForm`子组件，其 class 属性定义为`q-gutter-md`。在`QForm`组件内部，创建一个`EmailInput`组件，其`v-model`指令绑定到`data.email`，以及一个`PasswordInput`组件，其`v-model`指令绑定到`data.password`属性：

```js
<q-card-section>
 <q-form
  class="q-gutter-md"
  >
 <email-input
  v-model.trim="email"
  />
 <password-input
  v-model.trim="password"
  />
 </q-form> </q-card-section>
```

1.  然后，创建一个 `QCardActions` 组件，其中定义了一个 `align` 属性为 `right`。在内部，添加一个 `QBtn`，`label` 属性设置为 `"Create new Account"`，`color` 设置为 `primary`，`class` 设置为 `q-ml-sm`，`flat` 设置为 `true`，并且 `@click` 事件监听器绑定到 `createAccount` 方法。接下来，创建另一个 `QBtn` 组件，`label` 属性设置为 `"Login"`，`type` 设置为 `"submit"`，`color` 设置为 `primary`，并且 `@click` 事件监听器绑定到 `onSubmit` 方法：

```js
<q-card-actions align="right">
 <q-btn
  label="Create new account"
  color="primary"
  flat
 class="q-ml-sm"
  @click="createAccount"
  />
 <q-btn
  label="Login"
  type="submit"
  color="primary"
  @click="onSubmit"
  /> </q-card-actions>
```

1.  最后，创建一个 `QInnerLoading` 组件，将 `:showing` 属性绑定到 `computed.isLoading`。这将需要有一个 `QSpinner` 子组件，提供 `size` 属性。将其设置为 `50px`，`color` 设置为 `primary`：

```js
<q-inner-loading :showing="isLoading">
 <q-spinner size="50px" color="primary"/> </q-inner-loading>
```

要运行服务器并查看您的进度，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> quasar dev
```

请记住始终执行命令 `npm run lint --fix`，以自动修复任何代码 lint 错误。

这是页面预览的样子：

![](img/24406191-9d75-4c7f-80f1-f28a74bb3517.png)

### 创建用户注册页面

对于用户注册页面，我们将使用已经创建的四个组件：`NameInput`，`UsernameInput`，`PasswordInput` 和 `EmailInput`。

#### 单文件组件 <script> 部分

在这里，我们将创建用户注册页面的 `<script>` 部分：

1.  在 **`src/pages` ** 文件夹中，创建一个名为 `SignUp.vue` 的新文件并打开它。

1.  从 `vuex` 包中导入 `mapActions` 和 `mapGetters` 函数：

```js
import { mapActions, mapGetters } from 'vuex';
```

1.  创建一个 `export default` JavaScript 对象，提供五个属性：`name`（定义为 `'SignUp'`），`components`，`data`，`computed` 和 `methods`：

```js
export default {
  name: 'SignUp',
 components: {},  data: () => ({   }),
  computed: {   },
  methods: {   }, };
```

1.  在 `components` 属性中，添加四个新属性：`NameInput`，`UsernameInput`，`PasswordInput` 和 `EmailInput`。像这样定义它们：

+   `NameInput` 作为一个匿名函数，返回值为 `import('components/NameInput')`

+   `UsernameInput` 作为一个匿名函数，返回值为 `import('components/UsernameInput')`

+   `PasswordInput` 作为一个匿名函数，返回值为 `import('components/PasswordInput')`

+   `EmailInput` 作为一个匿名函数，返回值为 `import('components/EmailInput')`

这可以在以下代码中看到：

```js
components: {
  PasswordInput: () => import('components/PasswordInput'),
  EmailInput: () => import('components/EmailInput'),
  UsernameInput: () => import('components/UsernameInput'),
  NameInput: () => import('components/NameInput'), }, 
```

1.  在 `data` 属性中，我们将返回一个提供四个属性的 JavaScript 对象 - `name`，`username`，`email` 和 `password` - 其中所有属性都将是空字符串：

```js
data: () => ({
  name: '',
  username: '',
  email: '',
  password: '', }),
```

1.  在`computed`属性中，我们将解构`mapGetters`函数，将我们想要的模块的命名空间（在本例中为`'user'`）作为第一个参数传递。对于第二个参数，我们将使用一个要导入的`getters`数组，这种情况下是`isLoading`：

```js
computed: {
  ...mapGetters('user', [
  'isLoading',
  ]), },
```

1.  最后，对于`methods`属性，首先，我们将解构`mapActions`函数，将我们想要的模块的命名空间（在本例中为`'user'`）作为第一个参数传递。对于第二个参数，我们将传递一个要导入的`actions`数组，这种情况下是`signUpNewUser`。接下来，我们需要添加异步的`onSubmit`方法，它将分发`signUpNewUser`，然后将用户发送到`Validate`路由，以及`onReset`方法，它将清除数据：

```js
methods: {
  ...mapActions('user', [
  'signUpNewUser',
  ]),
  async onSubmit() {
  try {
    await this.signUpNewUser({
      name: this.name,
      username: this.username,
      email: this.email,
      password: this.password,
    });
    await this.$router.replace({ name: 'Validate' });
  } catch (e) {
    this.$q.dialog({
      message: e.message,
    });
  }
  },
  onReset() {
  this.email = '';
  this.password = '';
  }, },
```

#### 单文件组件`<template>`部分

要完成页面，我们需要添加`<template>`部分：

1.  创建一个`QPage`组件，`class`属性定义为`"bg-grey-1 flex flex-center"`：

```js
<q-page padding class="bg-grey-1 flex flex-center">
</q-page>
```

1.  在`QPage`组件内部，创建一个`QCard`组件，`style`属性定义为`"width: 350px"`：

```js
<q-card style="width: 350px">  </q-card>
```

1.  在`QCard`组件内部，创建一个`QCardSection`，其中包含一个`h6`子组件，其中`class`属性定义为`no-margin`：

```js
<q-card-section>
 <h6 class="no-margin">Create a new Account</h6> </q-card-section> 
```

1.  之后，创建一个`QCardSection`，其中包含一个`QForm`子组件，其中`class`属性定义为`q-gutter-md`。在`QForm`组件内部，创建一个`NameInput`组件，`v-model`指令绑定到`data.name`，一个`UsernameInput`组件，`v-model`指令绑定到`data.username`，一个`EmailInput`组件，`v-model`指令绑定到`data.email`，以及一个`PasswordInput`组件，`v-model`指令绑定到`data.password`属性：

```js
<q-card-section>
 <q-form   class="q-gutter-md"
  >
 <name-input
  v-model.trim="name"
  />
 <username-input
  v-model.trim="username"
  />
 <email-input
  v-model.trim="email"
  />
 <password-input
  v-model.trim="password"
  />
 </q-form> </q-card-section>
```

1.  现在，创建一个`QCardActions`组件，`align`属性设置为`right`。在内部，添加一个`QBtn`，`label`属性设置为`"Reset"`，`color`设置为`primary`，`class`设置为`q-ml-sm`，`flat`设置为`true`，`@click`事件监听器绑定到`onReset`方法。然后，创建另一个`QBtn`组件，`label`属性设置为`"Create"`，`type`设置为`"submit"`，`color`设置为`primary`，`@click`事件监听器绑定到`onSubmit`方法：

```js
<q-card-actions align="right">
 <q-btn
  label="Reset"
  type="reset"
  color="primary"
  flat
 class="q-ml-sm"
  @click="onReset"
  />
 <q-btn
  label="Create"
  type="submit"
  color="primary"
  @click="onSubmit"
  /> </q-card-actions>
```

1.  最后，创建一个`QInnerLoading`组件，`:showing`属性绑定到`computed.isLoading`。这将需要一个`QSpinner`子组件。`size`属性需要设置为`50px`，`color`需要设置为`primary`：

```js
<q-inner-loading :showing="isLoading">
 <q-spinner size="50px" color="primary"/> </q-inner-loading> 
```

要运行服务器并查看您的进度，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> quasar dev
```

记得始终执行命令`npm run lint --fix`，自动修复任何代码 lint 错误。

这是页面的预览：

![](img/11952d7a-f53c-4afb-b56b-fd669409c0e8.png)

### 创建用户验证页面。

用户创建了一个账户后，AWS Amplify 将发送一封带有验证 pin 码的电子邮件，我们需要将其发送回来进行验证。这个页面将是验证页面。

#### 单文件组件`<script>`部分

按照以下步骤创建用户验证页面的`<script>`部分：

1.  在`src/pages`文件夹内，创建一个名为`Validate.vue`的新文件并打开它。

1.  从`vuex`包中导入`mapActions`和`mapGetters`函数，以及从`src/driver/auth`导入`resendValidationCode`：

```js
import { mapActions, mapGetters } from 'vuex';
import { resendValidationCode } from 'src/driver/auth';    
```

1.  创建一个`export default`的 JavaScript 对象，提供四个属性：`name`（定义为`'Validate'`）、`data`、`computed`和`methods`：

```js
export default {
  name: 'Validate',   data: () => ({   }),
  computed: {   },
  methods: {   }, };
```

1.  在`data`属性内，我们将返回一个具有空字符串`code`属性的 JavaScript 对象：

```js
data: () => ({
  code: '', }),
```

1.  在`computed`属性内，我们将解构`mapGetters`函数，传递我们想要的模块的命名空间作为第一个参数，例如`'user'`。对于第二个参数，我们将传递一个要导入的`getters`数组，例如`isLoading`和`getUserEmail`：

```js
computed: {
  ...mapGetters('user', [
  'isLoading',
  'getUserEmail',
 ]), }, 
```

1.  最后，在`methods`属性中，我们将解构`mapActions`函数，传递我们想要的模块的命名空间作为第一个参数，例如`'user'`。对于第二个参数，我们将传递一个要导入的`actions`数组，例如`createNewUser`。接下来，我们需要添加异步的`onSubmit`方法，它将分发`createNewUser`并将用户发送到`Index`路由；`resendCode`方法，它将重新发送用户另一个验证代码；以及`onReset`方法，它将重置数据：

```js
methods: {
  ...mapActions('user', [
  'createNewUser',
  ]),
  async onSubmit() {
  try {
    await this.createNewUser(this.code);
    await this.$router.replace({ name: 'Index' });
  } catch (e) {
    console.error(e);
    this.$q.dialog({
      message: e.message,
    });
  }
  },
  async resendCode() {
  await resendValidationCode(this.getUserEmail);
  },
  onReset() {
  this.code = '';
  }, },
```

#### 单文件组件`<template>`部分

按照以下步骤创建用户验证页面的`<template>`部分：

1.  创建一个`QPage`组件，定义`class`属性为`"bg-grey-1 flex flex-center"`：

```js
<q-page padding class="bg-grey-1 flex flex-center">
</q-page>
```

1.  在`QPage`组件内部，创建一个`QCard`组件，定义`style`属性为`"width: 350px"`：

```js
<q-card style="width: 350px">  </q-card>
```

1.  在 `QCard` 组件内，创建一个带有 `h6` 子组件和定义为 `no-margin` 的 `class` 属性的 `QCardSection`。然后，创建一个兄弟元素，其 `class` 属性定义为 `text-subtitle2`：

```js
<q-card-section>
 <h6 class="no-margin">Validate new account</h6>
 <div class="text-subtitle2">{{ getUserEmail }}</div> </q-card-section>
```

1.  创建一个带有两个子组件的 `QCardSection`。这些将是 HTML 元素 `P`：

```js
<q-card-section>
 <p>A validation code were sent to you E-mail.</p>
 <p>Please enter it to validate your new account.</p> </q-card-section>
```

1.  之后，创建一个带有 `QForm` 子组件和定义为 `q-gutter-md` 的 `class` 属性的 `QCardSection`。在 `QForm` 组件内，添加 `QInput` 组件作为子元素。然后，在 `QInput` 组件内，将 `v-model` 指令绑定到 `data.code`。在 `QInput` 的 `rules` 属性内，将 `rules` 值定义为一个验证数组，用于检查是否已输入任何代码。启用 `lazy-rules`，以便它只在一段时间后进行验证：

```js
<q-card-section>
 <q-form
  class="q-gutter-md"
  >
 <q-input
  v-model.trim="code"
  :rules="[ val => val && val.length > 0
  || 'Please type the validation code']"
  outlined
 label="Validation Code"
  lazy-rules
  />
 </q-form> </q-card-section> 
```

1.  现在，创建一个带有 `align` 属性设置为 `right` 的 `QCardActions` 组件。在内部，添加一个 `label` 属性设置为 `"Reset"`、`color` 设置为 `primary`、`class` 设置为 `q-ml-sm`、`flat` 设置为 `true`，并且 `@click` 事件监听器绑定到 `onReset` 方法的 `QBtn`。创建另一个 `label` 属性设置为 `"Re-send code"`、`color` 设置为 `secondary`、`class` 设置为 `q-ml-sm`、`flat` 设置为 `true`，并且 `@click` 事件监听器绑定到 `resendCode` 方法的 `QBtn`。最后，创建一个带有 `label` 属性设置为 `"Validate"`、`type` 设置为 `"submit"`、`color` 设置为 `primary`，并且 `@click` 事件监听器绑定到 `onSubmit` 方法的 `QBtn` 组件：

```js
<q-card-actions align="right">
 <q-btn
  label="Reset"
  type="reset"
  color="primary"
  flat
 class="q-ml-sm"
  />
 <q-btn
  flat
 label="Re-send code"
  color="secondary"
  class="q-ml-sm"
  @click="resendCode"
  />
 <q-btn
  label="Validate"
  type="submit"
  color="primary"
  @click="onSubmit"
  /> </q-card-actions>
```

1.  最后，创建一个带有 `:showing` 属性绑定到 `computed.isLoading` 的 `QInnerLoading` 组件。它应该有一个 `size` 设置为 `50px` 和 `color` 设置为 `primary` 的 `QSpinner` 子组件：

```js
<q-inner-loading :showing="isLoading">
 <q-spinner size="50px" color="primary"/> </q-inner-loading>
```

要运行服务器并查看您的进度，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> quasar dev 
```

记得始终执行命令 `npm run lint --fix`，自动修复任何代码 lint 错误。

这是页面的预览：

![](img/b8f80c2c-f928-4240-9622-205a135d67b7.png)

### 创建用户编辑页面

对于用户编辑页面，我们将使用我们已经创建的四个组件：`NameInput`、`UsernameInput`、`AvatarInput` 和 `PasswordInput`。

#### 单文件组件 <script> 部分

按照以下步骤开始开发用户编辑页面的 `<script>` 部分：

1.  在 `src/pages` 文件夹内，创建一个名为 `Edit.vue` 的新文件并打开它。

1.  从`vuex`包中导入`mapActions`和`mapGetters`函数：

```js
import { mapActions, mapGetters } from 'vuex';
```

1.  创建一个`export default`的 JavaScript 对象，提供四个属性：`name`（定义为`'SignUp'`）、`data`、`computed`和`methods`：

```js
export default {
  name: 'EditUser',
  components: {},   data: () => ({   }),
  created() {},
  computed: {   },
  methods: {   }, };
```

1.  在`components`属性中，添加四个名为`NameInput`、`UsernameInput`、`PasswordInput`、`AvatarInput`的新属性。设置它们如下：

`NameInput`作为一个匿名函数，返回值为`import('components/NameInput')`

`UsernameInput`作为一个匿名函数，返回值为`import('components/UsernameInput')`

`PasswordInput`作为一个匿名函数，返回值为`import('components/PasswordInput')`

`AvatarInput`作为一个匿名函数，返回值为`import('components/AvatarInput')`：

```js
components: {
  AvatarInput: () => import('/components/AvatarInput'),
  PasswordInput: () => import('components/PasswordInput'),
  UsernameInput: () => import('components/UsernameInput'),
  NameInput: () => import('components/NameInput'), },
```

1.  在`data`属性中，我们将返回一个提供五个属性的 JavaScript 对象：`name`、`username`、`avatar`、`email`和`password`。所有这些都将是空字符串：

```js
data: () => ({
  name: '',
  username: '',
  avatar: '',
  password: '',
  newPassword: '', }), 
```

1.  在`created`生命周期钩子中，将`data.name`定义为`getUser.name`，`data.username`定义为`getUser.username`，以及`data.avatar`定义为`getUser.avatar`：

```js
created() {
  this.name = this.getUser.name;
  this.username = this.getUser.username;
  this.avatar = this.getUser.avatar; },
```

1.  在`computed`属性中，我们将解构`mapGetters`函数，传递我们想要的模块的命名空间作为第一个参数，这里是`'user'`。对于第二个参数，我们将传递一个要导入的`getters`数组，这种情况下是`isLoading`：

```js
computed: {
  ...mapGetters('user', [
  'getUser',
  'isLoading',
 ]), },
```

1.  最后，在`methods`属性中，我们将解构`mapActions`函数，传递我们想要的模块的命名空间作为第一个参数，这里是`'user'`。对于第二个参数，我们将传递一个要导入的`actions`数组，这种情况下是`editUser`。接下来，我们需要添加异步的`onSubmit`方法，它将调度`$refs.avatar.uploadFile()`，然后调度`editUser`发送用户到`Chat`路由，以及`onReset`方法，它将清除数据：

```js
methods: {
  ...mapActions('user', [
  'editUser',
  ]),
  async onSubmit() {
  try {
  await this.$refs.avatar.uploadFile();    await this.editUser({
  name: this.name,
  avatar: this.$refs.avatar.s3file,
  username: this.username,
  password: this.password,
  newPassword: this.newPassword,
  });   await this.$router.replace({ name: 'Contacts' });
  } catch (e) {
  this.$q.dialog({
  message: e.message,
  });
  }
 },
  onReset() {
  this.name = this.getUser.name;
  this.username = this.getUser.username;   this.password = '';
  this.newPassword = '';
  }, },
```

#### 单文件组件<template>部分

按照以下步骤创建用户编辑页面的`<template>`部分：

1.  创建一个带有`class`属性定义为`"bg-grey-1 flex flex-center"`的`QPage`组件：

```js
<q-page padding class="bg-grey-1 flex flex-center">
</q-page>
```

1.  在`QPage`组件内部，创建一个带有`style`属性定义为`"width: 350px"`的`QCard`组件：

```js
<q-card style="width: 350px">  </q-card>
```

1.  在`QCard`组件内部，创建一个带有`h6`子组件的`QCardSection`，并且`class`属性定义为`no-margin`：

```js
<q-card-section>
 <h6 class="no-margin">Edit user account</h6> </q-card-section> 
```

1.  之后，创建一个带有`class`属性定义为`q-gutter-md`的`QCardSection`，其中包含一个`QForm`子组件。在`QForm`组件内部，创建一个`AvatarInput`组件，其中`reference`指令定义为`avatar`，`v-model`指令绑定到`data.avatar`，一个`NameInput`组件，其中`v-model`指令绑定到`data.name`，一个`UsernameInput`组件，其中`v-model`指令绑定到`data.username`，一个`EmailInput`组件，其中`v-model`指令绑定到`data.email`，以及一个`PasswordInput`组件，其中`v-model`指令绑定到`data.password`属性：

```js
<q-card-section>
 <q-form
  class="q-gutter-md"
  >
 <avatar-input
  v-model="avatar"
  ref="avatar"
  />
 <name-input
  v-model.trim="name"
  />
 <username-input
  v-model.trim="username"
  />
 <q-separator/>
 <password-input
  v-model.trim="password"
  label="Your old password"
  />
 <password-input
  v-model.trim="newPassword"
  label="Your new password"
  />
 </q-form> </q-card-section> 
```

1.  现在，创建一个带有`align`属性设置为`right`的`QCardActions`组件。在内部，添加一个`label`属性设置为`"Reset"`，`color`设置为`primary`，`class`设置为`q-ml-sm`，`flat`设置为`true`，并且`@click`事件监听器绑定到`onReset`方法的`QBtn`。然后，创建另一个`QBtn`组件，其中`label`属性设置为`"Create"`，`type`设置为`"submit"`，`color`设置为`primary`，并且`@click`事件监听器绑定到`onSubmit`方法：

```js
<q-card-actions align="right">
 <q-btn
  label="Reset"
  type="reset"
  color="primary"
  flat
 class="q-ml-sm"
  @click="onReset"
  />
 <q-btn
  label="Update"
  type="submit"
  color="primary"
  @click="onSubmit"
  /> </q-card-actions> 
```

1.  最后，创建一个`QInnerLoading`组件，其中`:showing`属性绑定到`computed.isLoading`。它应该有一个`QSpinner`子组件，`size`设置为`50px`，`color`设置为`primary`：

```js
<q-inner-loading :showing="isLoading">
 <q-spinner size="50px" color="primary"/> </q-inner-loading>
```

要运行服务器并查看您的进度，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> quasar dev 
```

请记住始终执行命令`npm run lint --fix`，以自动修复任何代码 lint 错误。

这是页面的预览：

![](img/2ede7f2f-961d-49d4-89d9-be1b0d4cc045.png)

### 创建应用程序路由

现在我们已经创建了用户页面、组件和布局，我们需要将它们绑定在一起，以便用户可以访问。为此，我们需要创建路由并使其可用，以便用户可以在页面之间导航。按照以下步骤进行操作：

1.  打开`router`文件夹内的`routes.js`文件。

1.  将`routes`常量设置为空数组：

```js
const routes = [];
```

1.  向该数组添加一个具有三个属性`path`、`component`和`children`的 JavaScript 对象。`path`属性是一个字符串，将是一个静态 URL，`component`属性是一个匿名函数，将返回一个带有将要渲染的组件的 WebPack`import`函数，`children`属性是一个将在`path`内渲染的组件数组。每个子组件都是一个具有相同属性的 JavaScript 对象，另外还有一个叫做`name`的新属性。

```js
{
  path: '/',
  component: () => import('layouts/Base.vue'),
  children: [
  {
    path: '',
    name: 'Index',
    meta: {
      authenticated: false,
    },
    component: () => import('pages/Index.vue'),
  },
  ], },
```

1.  现在，对于`/chat`URL，我们需要在`pages`文件夹内创建两个新的占位符页面：`Contacts.vue`和`Messages.vue`。在这些文件内，创建一个带有以下模板的空组件：

```js
<template>
  <div />
</template>
<script>
export default {
  name: 'PlaceholderPage',
};
</script>
```

1.  在`message`路由内，我们需要添加两个特殊参数：`:id`和`path`。这些参数将用于在用户之间获取特定的消息。

```js
{
  path: '/chat',
  component: () => import('layouts/Chat.vue'),
  children: [
  {
    path: 'contacts',
    name: 'Contacts',
    component: () => import('pages/Contacts.vue'),
  },
  {
    path: 'messages/:id/:name',
    name: 'Messages',
    meta: {
      authenticated: true,
      goBack: {
        name: 'Contacts',
      },
    },
    component: () => import('pages/Messages.vue'),
  },
  ], },
```

1.  对于`/user`URL，我们将只创建一个子路由，即`edit`路由。在这个路由内，我们使用`alias`属性，因为`vue-router`需要有一个`path`为空的子路由进行首次子路由渲染。我们还将在我们的应用程序内有一个`/user/edit`路由可用。

```js
{
  path: '/user',
  component: () => import('layouts/Chat.vue'),
  children: [
  {
    path: '',
    alias: 'edit',
    name: 'Edit',
    meta: {
      authenticated: true,
      goBack: {
        name: 'Contacts',
      },
    },
    component: () => import('pages/Edit.vue'),
  },
  ], },
```

1.  最后，对于创建新用户，我们需要添加`/register`URL，其中包括两个子路由：`SignUp`和`Validate`。`SignUp`路由将是注册 URL 上的主要路由，并且当用户进入此 URL 时将直接调用。`Validate`路由只有在用户被重定向到`/register/validate`URL 时才会被调用。

```js
{
  path: '/register',
  component: () => import('layouts/Base.vue'),
  children: [
  {
    path: '',
    alias: 'sign-up',
    name: 'SignUp',
    meta: {
      authenticated: false,
    },
    component: () => import('pages/SignUp.vue'),
  },
  {
    path: 'validate',
    name: 'Validate',
    meta: {
      authenticated: false,
    },
    component: () => import('pages/Validate.vue'),
  },
  ], },
```

### 添加身份验证守卫

为了在用户进入应用程序时验证用户身份令牌，如果令牌有效，或者用户试图访问无权限的路由，我们需要为我们的应用程序创建一个身份验证守卫。

1.  在`src/boot`文件夹内创建一个名为`routeGuard.js`的新文件。

1.  创建一个默认的导出异步函数。在这个参数内，添加一个名为`app`的 JavaScript 对象属性。在函数内部，创建一个常量，使用`app`的对象重构获取`store`属性。然后，创建一个`try/catch`块。在`try`部分，检查`'user/getUserId'`是否存在，如果不存在则调度`'user/initialLogin'`。最后，在`catch`块内，将用户重定向到`Index`路由。

```js
export default async ({ app }) => {
  const { store } = app;    try {
  if (!store.getters['user/getUserId']) {
    await store.dispatch('user/initialLogin');
  }
  } catch {
  await app.router.replace({ name: 'Index' });
  } };  
```

1.  最后，打开项目根文件夹内的`quasar.conf.js`文件，并找到`boot`属性。将`'routerGuard'`项添加到数组中。

```js
boot: [
  'amplify',
  'axios',
  'routeGuard', ],
```

## 它是如何工作的...

在本章中，我们开发了微组件，如`NameInput`，`EmailInput`等，以简化开发宏组件或容器（如页面）的过程。

在这个配方中，我们使用了在上一个配方中开发的组件来创建完整的页面，例如用户登录、用户编辑和用户注册页面。

使用`vue-router`来管理使用自定义布局包装页面的父子过程，我们使用了本书先前配方中创建的布局来为我们的应用程序创建路由。我们使它们可用，以便我们可以像正常的 Web 应用程序一样访问应用程序，具有自定义 URL 和路由。

最后，我们在我们的主初始化 Vue 文件中添加了一些身份验证中间件，以便我们可以重定向已经经过身份验证的用户。这意味着当他们第二次进入应用程序时，他们不需要再次进行身份验证。

## 还有...

现在，您的应用程序已准备好进行用户注册和登录。可以浏览用户注册页面，并从亚马逊收到一封带有验证代码的电子邮件，以便您可以在服务器上验证用户。

要检查您的进程并在本地环境中运行它，请打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> quasar dev
```

请记住始终执行命令`npm run lint --fix`，以自动修复任何代码 lint 错误。

## 另请参阅

+   您可以在[`router.vuejs.org/guide/essentials/nested-routes.html`](https://router.vuejs.org/guide/essentials/nested-routes.html)找到有关`vue-router`嵌套路由的更多信息。

+   您可以在[`router.vuejs.org/guide/advanced/lazy-loading.html`](https://router.vuejs.org/guide/advanced/lazy-loading.html)找到有关`vue-router`懒加载的更多信息。

+   你可以在[`quasar.dev/vue-components/inner-loading`](https://quasar.dev/vue-components/inner-loading)找到有关 Quasar 框架的`QInnerLoading`组件的更多信息。
