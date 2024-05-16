使用 vue-router 管理路由

您的应用程序的主要部分之一是路由管理。在这里，可以将无限的组件组合在一个地方。

路由能够协调组件的渲染，并根据 URL 指示应用程序应该在哪里。有许多方法可以增加`vue-router`的定制化。您可以添加路由守卫来检查特定路由是否可由访问级别导航，或在进入路由之前获取数据以管理应用程序中的错误。

在本章中，您将学习如何创建应用程序路由、动态路由、别名和信任路由，以及嵌套路由视图。我们还将看看如何管理错误，创建路由守卫，并延迟加载您的页面。

在本章中，我们将涵盖以下教程：

+   创建一个简单的路由

+   创建一个程序化导航

+   创建一个动态路由路径

+   创建一个路由别名

+   创建一个路由重定向

+   创建一个嵌套路由视图

+   创建一个 404 错误页面

+   创建一个身份验证中间件

+   异步延迟加载您的页面

# 第七章：技术要求

在本章中，我们将使用**Node.js**和**Vue-CLI**。

注意 Windows 用户：您需要安装一个名为`windows-build-tools`的 npm 包，以便能够安装以下所需的包。为此，请以管理员身份打开 PowerShell 并执行以下命令：

`> npm install -g windows-build-tools`

要安装 Vue-CLI，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> npm install -g @vue/cli @vue/cli-service-global
```

# 创建一个简单的路由

在您的应用程序中，您可以创建无限组合的路由，可以导向任意数量的页面和组件。

`vue-router`是这个组合的维护者。我们需要使用它来设置如何创建路径并为我们的访问者建立路由的指令。

在这个教程中，我们将学习如何创建一个初始路由，该路由将引导到不同的组件。

## 准备工作

这个教程的先决条件如下：

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   `@vue/cli`

+   `@vue/cli-service-global`

## 如何做…

创建一个 Vue-CLI 项目，按照以下步骤进行：

1.  我们需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> vue create initial-routes
```

1.  CLI 将询问一些问题，这些问题将有助于创建项目。您可以使用箭头键导航，*Enter*键继续，*Spacebar*选择选项。

1.  有两种方法可以启动新项目。默认方法是基本的 Babel 和 ESLint 项目，没有任何插件或配置，还有`手动`模式，您可以选择更多模式、插件、代码检查器和选项。我们将选择`手动`：

```js
?  Please pick a preset: (Use arrow keys) default (babel, eslint**)** ❯ **Manually select features** ‌
```

1.  现在我们被问及项目中想要的功能。这些功能包括一些 Vue 插件，如 Vuex 或 Vue Router（Vue-Router）、测试器、代码检查器等。选择`Babel`、`Router`和`Linter / Formatter`**：**

```js
?  Check the features needed for your project: (Use arrow keys) ❯ Babel
 TypeScript Progressive Web App (PWA) Support ❯ Router
 Vuex  CSS Pre-processors ❯ Linter / Formatter
 Unit Testing E2E Testing
```

1.  现在 Vue-CLI 会询问您是否要在路由管理中使用历史模式。我们会选择`Y`（是）：

```js
?  Use history mode for router? (Requires proper server setup for
  index fallback in production)  (Y**/n) y**
```

1.  继续此过程，选择一个代码检查器和格式化程序。在我们的情况下，我们将选择`ESLint + Airbnb config`：

```js
?  Pick a linter / formatter config: (Use arrow keys) ESLint with error prevention only ❯ **ESLint + Airbnb config** ESLint + Standard config 
  ESLint + Prettier
```

1.  设置完代码检查规则后，我们需要定义它们何时应用于您的代码。它们可以在保存时应用，也可以在提交时修复：

```js
?  Pick additional lint features: (Use arrow keys)  Lint on save ❯ Lint and fix on commit
```

1.  在定义了所有这些插件、代码检查器和处理器之后，我们需要选择设置和配置存储的位置。最佳存储位置是专用文件，但也可以将它们存储在`package.json`中：

```js
?  Where do you prefer placing config for Babel, ESLint, etc.?  (Use arrow keys) ❯ **In dedicated config files****In package.json** 
```

1.  现在您可以选择是否要将此选择作为将来项目的预设，这样您就不需要再次重新选择所有内容：

```js
?  Save this as a preset for future projects?  (y/N) n
```

我们的步骤将分为五个部分：

+   创建`NavigationBar`组件

+   创建联系页面

+   创建关于页面

+   更改应用程序的主要组件

+   创建路由

让我们开始吧。

### 创建 NavigationBar 组件

现在我们将创建将在我们的应用程序中使用的`NavigationBar`组件。

#### 单文件组件<script>部分

在这一部分，我们将创建单文件组件的<script>部分。按照这些说明正确创建组件：

1.  在`src/components`文件夹中创建一个`navigationBar.vue`文件并打开它。

1.  创建组件的默认`export`对象，具有 Vue 属性`name`：

```js
<script> export default {
  name: 'NavigationBar', }; </script>
```

#### 单文件组件<template>部分

在这一部分，我们将创建单文件组件的<template>部分。按照这些说明正确创建组件：

1.  创建一个带有`id`属性定义为`"nav"`的`div` HTML 元素，并在其中创建三个`RouterLink`组件。这些组件将指向`Home`、`About`和`Contact`路由。在`RouterLink`组件中，我们将添加一个`to`属性，分别定义为每个组件的路由，并将文本内容定义为菜单的名称： 

```js
<div id="nav">
 <router-link to="/">
  Home
  </router-link> |
  <router-link to="/about">
  About
  </router-link> |
  <router-link to="/contact">
  Contact
  </router-link> </div>
```

### 创建联系页面

我们需要确保当用户输入`/contact` URL 时，联系页面会被渲染。为此，我们需要创建一个单文件组件，用作联系页面。

#### 单文件组件 <script> 部分

在这部分，我们将创建单文件组件的`<script>`部分。按照以下说明正确创建组件：

1.  在`src/views`文件夹中，创建一个名为`contact.vue`的新文件并打开它。

1.  创建组件的默认`export`对象，其中包含 Vue 属性`name`：

```js
<script> export default {
  name: 'ContactPage', }; </script>
```

#### 单文件组件 <template> 部分

在这部分，我们将创建单文件组件的`<template>`部分。按照以下说明正确创建组件：

1.  创建一个`div` HTML 元素，其中`class`属性定义为`"contact"`。

1.  在`<h1>`HTML 元素内部，添加一个显示当前页面的文本内容：

```js
<template>
 <div class="contact">
 <h1>This is a contact page</h1>
 </div> </template>
```

### 创建关于页面

我们需要确保当用户输入`/about` URL 时，关于页面会被渲染。在接下来的小节中，我们将为关于页面创建单文件组件。

#### 单文件组件 <script> 部分

在这部分，我们将创建单文件组件的`<script>`部分。按照以下说明正确创建组件：

1.  在`src/views`文件夹中，创建一个名为`About.vue`的新文件并打开它。

1.  创建组件的默认导出对象，其中包含 Vue 属性`name`：

```js
<script> export default {
  name: 'AboutPage', }; </script>
```

#### 单文件组件 <template> 部分

在这部分，我们将创建单文件组件的`<template>`部分。按照以下说明正确创建组件：

1.  创建一个`div` HTML 元素，其中`class`属性定义为`"about"`。

1.  在其中，放置一个带有显示当前页面文本内容的`<h1>`元素：

```js
<template>
 <div class="about">
 <h1>This is an about page</h1>
 </div> </template>
```

### 更改应用程序的主要组件

创建页面和导航栏后，我们需要更改应用程序的主要组件，以便能够渲染路由并在顶部拥有导航栏。

#### 单文件组件 <script> 部分

在这部分中，我们将创建单文件组件的`<script>`部分。按照以下说明正确创建组件：

1.  在`src`文件夹中打开`App.vue`。

1.  导入`NavigationBar`组件：

```js
import NavigationBar from './components/navigationBar.vue';
```

1.  在 Vue 的`components`属性中，声明导入的`NavigationBar`：

```js
export default {
  components: { NavigationBar }, };
```

#### 单文件组件`<template>`部分

在这部分中，我们将创建单文件组件的`<template>`部分。在`div` HTML 元素内，添加`NavigationBar`组件和`RouterView`组件：

```js
<template>
 <div id="app">
 <navigation-bar />
 <router-view/>
 </div> </template>
```

### 创建路由

现在我们需要在应用程序中使路由可用。为此，首先需要声明路由和路由将呈现的组件。按照以下步骤正确创建 Vue 应用程序路由：

1.  在`src/router`文件夹中，打开`index.js`文件。

1.  导入`Contact`组件页面：

```js
import Vue from 'vue'; import VueRouter from 'vue-router'; import Home from '../views/Home.vue'; import Contact from '../views/contact.vue';
```

1.  在`routes`数组中，我们需要创建一个新的`route`对象。该对象将具有`path`属性定义为`'/contact'`，`name`定义为`'contact'`，并且`component`指向导入的`Contact`组件：

```js
{
  path: '/contact',
  name: 'contact',
  component: Contact, },
```

要运行服务器并查看组件，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> npm run serve
```

这是您的组件呈现并运行的地方：

![](img/62dcc997-24fa-40f8-b29c-13cd4caca6cd.png)

## 工作原理...

当将`vue-router`添加到 Vue 作为插件时，它开始监视`window.location.pathname`和其他 URL 属性的更改，以检查当前 URL 在浏览器上的权重与路由配置中的 URL 列表的匹配情况。

在这种情况下，我们使用直接 URL 和非动态 URL。因此，`vue-router`插件只需要检查 URL 路径的直接匹配，而不需要将可能的匹配与正则表达式验证器进行比较。

匹配 URL 后，`router-view`组件充当**动态组件**，并呈现我们在`vue-router`配置中定义的组件。

## 另请参阅

您可以在[`router.vuejs.org/.`](https://router.vuejs.org/)找到有关`vue-router`的更多信息。

您可以在[`cli.vuejs.org/.`](https://cli.vuejs.org/)找到有关 Vue CLI 的更多信息。

# 创建程序化导航

使用`vue-router`时，还可以通过函数执行来更改应用程序的当前路由，而无需特殊的`vue-router`组件来创建链接。

使用程序化导航，您可以确保所有路由重定向可以在代码的任何位置执行。使用此方法可以使用特殊的路由方向，例如传递参数和使用路由名称进行导航。

在这个食谱中，我们将学习如何执行程序化导航函数，以及它提供的额外可能性。

## 准备工作

此食谱的先决条件如下：

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   @vue/cli

+   `@vue/cli-service-global`

## 如何做...

要启动我们的组件，我们可以使用在“*创建简单路由*”中创建的 Vue 项目与 Vue-CLI，或者我们可以开始一个新的项目。

要开始一个新的项目，打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows），并执行以下命令：

```js
> vue create route-project
```

选择手动功能并将`Router`作为所需功能添加，如“*如何做...*”部分和“*创建简单路由*”食谱中所示。

我们的食谱将分为两部分：

+   更改应用程序的主要组件

+   更改联系视图

让我们开始吧。

### 更改应用程序的主要组件

我们将从`App.vue`文件开始。我们将添加一个在超时后执行的程序化导航函数，该函数将添加到组件生命周期钩子中。

#### 单文件组件`<script>`部分

在这部分中，我们将创建单文件组件的`<script>`部分。按照以下说明正确创建组件：

1.  在`src`文件夹中打开`App.vue`。

1.  添加一个`mounted`属性：

```js
mounted() {}
```

1.  在`mounted`属性中，我们需要添加一个`setTimeout`函数，该函数将执行`$router.push`函数。当执行时，此函数将接收一个 JavaScript 对象作为参数，其中包含两个属性，`name`和`params`：

```js
mounted() {
  setTimeout(() => {
  this.$router.push({
  name: 'contact',
  params: {
  name: 'Heitor Ribeiro',
  age: 31,
 }, }); }, 5000); },
```

### 更改联系视图

在联系视图上，我们需要添加一个事件侦听器，该侦听器将抓取路由更改并执行操作。

#### 单文件组件`<script>`部分

在这部分中，我们将创建单文件组件的`<script>`部分。按照以下说明正确创建组件：

1.  在`src/views`文件夹中打开`contact.vue`。

1.  添加一个新的`mounted`属性：

```js
mounted() {}
```

1.  在此属性中，我们将添加一个验证，检查`$route.params`对象上是否有任何参数，并显示具有该`$route.params`的`alert`：

```js
mounted() {
  if (Object.keys(this.$route.params).length) {
  alert(`Hey! I've got some parameter! 
       ${JSON.stringify(this.$route.params)}`);
  } },
```

要运行服务器并查看您的组件，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> npm run serve
```

这是您的组件呈现和运行的方式：

![](img/751bd657-b3cf-44c2-9fbc-ad4027477d67.png)

## 它是如何工作的...

当执行`$router.push`函数时，告诉`vue-router`改变应用程序所在的位置，在这个过程中，您将向新的路由器传递一些参数，这些参数将替换当前路由。在这些参数中，有一个名为`params`的属性，它将一组参数发送到新的路由器。

当进入这个新的路由器时，我们将从路由器内部调用的所有参数都将在`$route.params`对象中可用；在那里，我们可以在我们的视图或组件中使用它。

## 还有更多...

在程序化导航中，可以通过`$router.push`函数导航到路由器，并将它们添加到浏览器历史记录中，但也可以使用其他函数。

`$router.replace`函数将替换用户导航历史记录为新的历史记录，使其无法返回到上一页。

`$router.go`用于以步骤方式移动用户导航历史记录。要前进，您需要传递正数，要后退，您需要传递负数。

## 参见

您可以在[`router.vuejs.org/guide/essentials/navigation.html`](https://router.vuejs.org/guide/essentials/navigation.html)找到有关`vue-router`程序化导航的更多信息。

# 创建动态路由器路径

向您的应用程序添加路由是必不可少的，但有时您需要的不仅仅是简单的路由。在这个食谱中，我们将看看动态路由是如何发挥作用的。通过动态路由，您可以定义可以通过 URL 设置的自定义变量，并且您的应用程序可以从这些变量开始定义。

在这个食谱中，我们将学习如何在 CRUD 列表上使用动态路由器路径。

## 准备就绪

这个食谱的先决条件如下：

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   `@vue/cli`

+   `@vue/cli-service-global`

## 如何做...

要启动我们的组件，我们将使用在第五章中完成的 Vue 项目和 Vue-CLI，*通过 HTTP 请求从 Web 获取数据*中的'*使用 axios 和 Vuesax 创建 CRUD 界面*'食谱。在接下来的步骤中，我们将通过 Vue UI 仪表板向项目添加`vue-router`：

1.  首先，您需要打开`vue ui`。要做到这一点，打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> vue ui
```

1.  在那里，您需要通过定位项目文件夹来导入项目。导入`vue ui`后，您将被重定向到仪表板。

1.  通过转到插件管理页面并单击“添加 vue-router”按钮，将`vue-router`添加到插件中。然后，单击“继续”按钮。

1.  Vue-CLI 将自动为我们在项目上安装和配置 vue-router。现在我们需要为**列表**，**视图**和**编辑**页面创建每个视图。

要开始视图开发，我们将首先进入用户列表路由。在每个路由中，我们将解构我们之前制作的旧组件，并将其重新创建为视图。

我们的步骤将分为八个部分：

+   更改应用程序的主要组件

+   更改路由 mixin

+   Axios 实例配置

+   用户列表视图

+   用户创建视图

+   用户信息视图

+   用户更新视图

+   创建动态路由

让我们开始吧。

### 更改应用程序的主要组件

添加 vue-router 插件后，`App.vue`将发生变化。我们需要撤销安装`vue-router`所做的更改。这是因为当`vue-ui`添加`vue-router`插件时，它会更改`App.vue`，添加我们不需要的示例代码。

#### 单文件组件<template>部分

在这部分中，我们将创建单文件组件的`<template>`部分。按照以下说明正确创建组件：

1.  在`src`文件夹中打开`App.vue`。

1.  删除所有内容，只留下`div#app` HTML 元素和`router-view`组件：

```js
<template>
 <div id="app">
 <router-view/>
 </div> </template>
```

### 更改路由 mixin

在上一个步骤中，我们使用了一个`changeComponent` mixin。现在我们要使用路由，我们需要将此 mixin 更改为`changeRoute` mixin 并更改其行为。在接下来的步骤中，我们将更改 mixin 的工作方式，以便能够更改路由而不是组件：

1.  在`src/mixin`文件夹中，将`changeComponent.js`重命名为`changeRoute.js`并打开它。

1.  我们将删除`changeComponent`方法并创建一个名为`changeRoute`的新方法。这个新方法将接收两个参数，`name`和`id`。`name`参数是路由名称，在`vue-router`配置中设置，`id`将是我们将在路由更改中传递的用户 id 参数。此方法将执行`$router.push`，将这些参数作为参数传递：

```js
export default {
  methods: {
  async changeRoute(name, id = 0) {
  await this.$router.push({
 name,
  params: {
 id,
  },
  });
  },
  } }
```

### Axios 实例配置

要在 MirageJS 服务器中获取数据，我们需要在 axios 实例中定义一些选项。现在，在以下步骤中，我们将配置 axios 实例以与新的路由系统一起工作：

1.  在`src/http`文件夹中，打开`baseFetch.js`文件。

1.  在`axios`的`localApi`实例的创建者中，我们需要添加一个`options`对象，传递`baseURL`属性。这个`baseURL`将是当前浏览器导航的 URL：

```js
const localApi = createAxios({
  baseURL: `${document.location.protocol}//${document.location.host}`, });
```

### 用户列表视图

为了创建我们的视图，我们将从`list.vue`组件中提取代码，并将其重塑为页面视图。

#### 单文件组件<script>部分

在这部分，我们将创建单文件组件的`<script>`部分。按照以下说明正确创建组件：

1.  将`list.vue`文件从`components`移动到`views`文件夹，并将其重命名为`List.vue`。

1.  删除旧的`changeComponent` mixin 导入，并导入新的`changeRoute` mixin：

```js
import changeRouteMixin from '@/mixin/changeRoute';
```

1.  在 Vue 的`mixins`属性中，我们需要用`changeRoute`替换`changeComponent`：

```js
mixins: [changeRouteMixin],
```

1.  在`getAllUsers`和`deleteUser`方法中，我们需要从`getHttp`和`deleteHttp`函数参数中删除`${window.location.href}`：

```js
methods: {
  async getAllUsers() {
  const { data } = await getHttp(`api/users`);
  this.userList = data;
  },
  async deleteUser(id) {
  await deleteHttp(`api/users/${id}`);
  await this.getAllUsers();
  }, }
```

#### 单文件组件<template>部分

在这部分，我们将创建单文件组件的`<template>`部分。按照以下说明正确创建组件：

1.  我们需要用`VsRow`和`VsCol`组件包装`VsCard`组件及其子内容。`VsCol`组件将`vs-type`属性定义为`'flex'`，`vs-justify`定义为`'left'`，`vs-align`定义为`'left'`，`vs-w`定义为`12`：

```js
<template>
 <vs-row>
 <vs-col
  vs-type="flex"
  vs-justify="left"
  vs-align="left"
  vs-w="12">
 <vs-card... />
    </vs-col>
  </vs-row>
</template>
```

1.  在操作按钮上，我们将把`changeComponent`函数改为`changeRoute`：

```js
<vs-td :data="data[index].id">
 <vs-button
  color="primary"
  type="filled"
  icon="remove_red_eye"
  size="small"
  @click="changeRoute('view', data[index].id)"
  />
 <vs-button
  color="success"
  type="filled"
  icon="edit"
  size="small"
  @click="changeRoute('edit', data[index].id)"
  />
 <vs-button
  color="danger"
  type="filled"
  icon="delete"
  size="small"
  @click="deleteUser(data[index].id)"
  /> </vs-td>
```

1.  在`VsCard`的页脚处，我们需要将操作按钮的`changeComponent`方法改为`changeRoute`方法：

```js
<template slot="footer">
 <vs-row vs-justify="flex-start">
 <vs-button
  color="primary"
  type="filled"
  icon="fiber_new"
  size="small"
  @click="changeRoute('create')"
  >
  Create User
    </vs-button>
 </vs-row> </template>
```

### 用户创建视图

为了创建我们的视图，我们将从`create.vue`组件中提取代码，并将其重塑为页面视图。

#### 单文件组件<script>部分

在这部分，我们将创建单文件组件的`<script>`部分。按照以下说明正确创建组件：

1.  将`create.vue`文件从`components`移动到`views`文件夹，并将其重命名为`Create.vue`。

1.  删除旧的`changeComponent` mixin 导入，并导入新的`changeRoute` mixin：

```js
import changeRouteMixin from '@/mixin/changeRoute';
```

1.  在 Vue 的`mixins`属性中，我们需要用`changeRoute`替换`changeComponent`：

```js
mixins: [changeRouteMixin],
```

1.  在`getUserById`方法中，我们需要从`postHttp`函数的 URL 中移除`${window.location.href}`，并将`changeComponent`函数更改为`changeRoute`：

```js
async createUser() {
  await postHttp(`/api/users`, {
  data: {
  ...this.userData,
  }
 });
  this.changeRoute('list'); },
```

#### 单文件组件<template>部分

在这部分，我们将创建单文件组件的`<template>`部分。按照这些说明正确创建组件：

1.  我们需要用`VsRow`和`VsCol`组件包裹`VsCard`组件及其子内容。`VsCol`组件将定义`vs-type`属性为`'flex'`，`vs-justify`属性为`'left'`，`vs-align`属性为`'left'`，`vs-w`属性为`12`：

```js
<template>
 <vs-row>
 <vs-col
  vs-type="flex"
  vs-justify="left"
  vs-align="left"
  vs-w="12">
 <vs-card... />
    </vs-col>
  </vs-row>
</template>
```

1.  在`VsCard`的页脚上，我们需要将`Cancel`按钮的`changeComponent`函数更改为`changeRoute`：

```js
<vs-button
  color="danger"
  type="filled"
  icon="cancel"
  size="small"
  style="margin-left: 5px"
  @click="changeRoute('list')" >
  Cancel
</vs-button>
```

### 用户信息视图

为了创建我们的视图，我们将从`view.vue`组件中提取代码，并将其重塑为页面视图。

#### 单文件组件<script>部分

在这部分，我们将创建单文件组件的`<script>`部分。按照这些说明正确创建组件：

1.  将`view.vue`文件从`src/components`移动到`src/views`文件夹，并将其重命名为`View.vue`。

1.  移除旧的`changeComponent`混入导入，并导入新的`changeRoute`：

```js
import changeRouteMixin from '@/mixin/changeRoute';
```

1.  在 Vue 的`mixins`属性中，我们需要用`changeRoute`替换`changeComponent`：

```js
mixins: [changeRouteMixin],
```

1.  在`component`对象中创建一个新的`computed`属性，属性为`userId`，它将返回`$route.params.id`：

```js
computed: {
  userId() {
  return this.$route.params.id;
  }, },
```

1.  在`getUserById`方法中，我们需要从`getHttp`函数的 URL 中移除`${window.location.href}`：

```js
methods: {
  async getUserById() {
  const { data } = await getHttp(`api/users/${this.userId}`);
  this.userData = data;
  }, }
```

#### 单文件组件<template>部分

在这部分，我们将创建单文件组件的`<template>`部分。按照这些说明正确创建组件：

1.  我们需要用`VsRow`和`VsCol`组件包裹`VsCard`组件及其子内容。`VsCol`组件将定义`vs-type`属性为`'flex'`，`vs-justify`属性为`'left'`，`vs-align`属性为`'left'`，`vs-w`属性为`12`：

```js
<template>
 <vs-row>
 <vs-col
  vs-type="flex"
  vs-justify="left"
  vs-align="left"
  vs-w="12">
 <vs-card... />
    </vs-col>
  </vs-row>
</template>
```

1.  在`VsCard`的页脚上，我们需要将返回按钮的`changeComponent`函数更改为`changeRoute`：

```js
<vs-button
  color="primary"
  type="filled"
  icon="arrow_back"
  size="small"
  style="margin-left: 5px"
  @click="changeRoute('list')" >
  Back
</vs-button>
```

### 用户更新视图

为了创建我们的视图，我们将从`update.vue`组件中提取代码，并将其重塑为页面视图。

#### 单文件组件<script>部分

在这部分，我们将创建单文件组件的`<script>`部分。按照这些说明正确创建组件：

1.  将`update.vue`文件从`src/components`移动到`src/views`文件夹，并将其重命名为`Edit.vue`。

1.  移除旧的`changeComponent`混入导入，并导入新的`changeRoute`混入：

```js
import changeRouteMixin from '@/mixin/changeRoute';
```

1.  在 Vue 的`mixins`属性中，我们需要用`changeRoute`替换`changeComponent`：

```js
mixins: [changeRouteMixin],
```

1.  在`component`对象中创建一个新的`computed`属性，具有`userId`属性，它将返回`$route.params.id`：

```js
computed: {
  userId() {
  return this.$route.params.id;
  }, },
```

1.  在`getUserById`和`updateUser`方法中，我们需要移除

从`getHttp`和`patchHttp`函数的 URL 中删除`${window.location.href}`，并将`changeComponent`函数改为`changeRoute`：

```js
methods: {
  async getUserById() {
  const { data } = await getHttp(`api/users/${this.userId}`);
  this.userData = data;
  },
  async updateUser() {
  await patchHttp(`api/users/${this.userData.id}`, {
  data: {
  ...this.userData,
  }
 });
  this.changeRoute('list');
  }, },
```

#### 单文件组件的<template>部分

在这部分，我们将创建单文件组件的`<template>`部分。按照以下说明正确创建组件：

1.  我们需要用`VsRow`和`VsCol`组件包裹`VsCard`组件及其子内容。`VsCol`组件将`vs-type`属性定义为`'flex'`，`vs-justify`定义为`'left'`，`vs-align`定义为`'left'`，`vs-w`定义为`12`：

```js
<template>
 <vs-row>
 <vs-col
  vs-type="flex"
  vs-justify="left"
  vs-align="left"
  vs-w="12">
 <vs-card... />
    </vs-col>
  </vs-row>
</template>
```

1.  在`VsCard`的页脚上，我们需要把`Cancel`按钮的`changeComponent`函数改为`changeRoute`：

```js
<vs-button
  color="danger"
  type="filled"
  icon="cancel"
  size="small"
  style="margin-left: 5px"
  @click="changeRoute('list')" >
  Cancel
</vs-button>
```

### 创建动态路由

现在，我们已经创建了页面视图，我们需要创建路由并使其接受参数，将它们转换为动态路由。在接下来的步骤中，我们将创建应用程序的动态路由：

1.  打开`src/router`文件夹中的`index.js`。

1.  首先，我们需要导入四个新页面 - `List`，`View`，`Edit`，`Create`和`Update`：

```js
import List from '@/views/List.vue'; import View from '@/views/View.vue'; import Edit from '@/views/Edit.vue'; import Create from '@/views/Create.vue'; 
```

1.  在`routes`数组上，我们将为每个导入的页面添加一个新的路由对象。在这个对象中，将有三个属性：`name`，`path`和`component`。

1.  对于`list`路由，我们将把`name`定义为`'list'`，`path`定义为`'/'`，并把`component`定义为导入的`List`组件：

```js
{
  path: '/',
  name: 'list',
  component: List, },
```

1.  在`view`路由上，我们将把`name`定义为`'view'`，`path`定义为`'/view/:id'`，并把`component`定义为导入的`View`组件：

```js
{
  path: '/view/:id',
  name: 'view',
  component: View, },
```

1.  在`edit`路由上，我们将把`name`定义为`'edit'`，`path`定义为`'/edit/:id'`，并把`component`定义为导入的`Edit`组件：

```js
{
  path: '/edit/:id',
  name: 'edit',
  component: Edit, },
```

1.  最后，在`create`路由上，我们将把`name`定义为`'create'`，`path`定义为`'/create'`，并把`component`定义为导入的`Create`组件：

```js
{
  path: '/create',
  name: 'create',
  component: Create, },
```

1.  当创建`VueRouter`时，我们将添加`mode`选项属性，并将其设置为`'history'`：

```js
const router = new VueRouter({
  mode: 'history',
  base: process.env.BASE_URL,
  routes });
```

要运行服务器并查看您的组件，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> npm run serve
```

这是您的组件渲染和运行的方式：

+   **列表视图路由 -** `/`将是您的用户列表页面，包含应用程序中所有用户的列表以及查看、编辑和删除用户的按钮，以及创建新用户的按钮：

![](img/4ba80475-521f-446f-8c30-8be4c9b739ed.png)

+   **用户视图路由 -** `/view/:id`将是您的用户查看页面，您可以在此页面查看用户信息，例如用户的姓名、电子邮件、国家、生日和电话号码：

![](img/10c4f103-0042-4f58-8c55-74f4ac6fbbef.png)

+   **用户编辑路由 -** `/update/:id`将是您的用户编辑页面，您可以在此页面编辑用户信息，更改用户的姓名、电子邮件、国家、生日和电话号码：

![](img/9010ffcc-0c76-4b96-be5e-d363d55d90d6.png)

+   **创建用户路由别名 -** `/update/:id`将是您的用户创建页面，您可以在系统上创建新用户：

![](img/42da8529-0e65-41c5-a7af-81b391e14ab0.png)

## 它是如何工作的...

当创建`vue-router`并将路由传递进行匹配时，路由分析会根据每个路由的权重定义的正则表达式来寻找最佳匹配路由。

当定义路由并在其路径中有一个变量时，您需要在变量参数之前添加`:`。此参数通过`$route.params`属性传递给组件。

## 另请参阅

您可以在[`router.vuejs.org/guide/essentials/dynamic-matching.html`](https://router.vuejs.org/guide/essentials/dynamic-matching.html)找到有关动态路由匹配的更多信息。

# 创建路由别名

每个应用程序都是一个活生生的有机体-它不断发展、突变和变化。有时，这些进化可以通过路由更改的形式来实现，以获得更好的命名或废弃的服务。在`vue-router`中，可以使所有这些更改对用户不可见，因此当他们使用旧链接时，仍然可以访问应用程序。

在这个教程中，我们将学习如何为我们的应用程序创建路由别名并使用它。

## 准备工作

此教程的先决条件如下：

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   `@vue/cli`

+   `@vue/cli-service-global`

## 如何做到这一点...

要启动我们的组件，我们将使用在“创建动态路由路径”配方中完成的 Vue 项目，或者我们可以开始一个新的项目。

要开始一个新的项目，打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> vue create http-project
```

选择手动功能，并按照“如何做…”部分的指示，将`router`添加为必需功能。

现在，在以下步骤中，我们将创建路由别名：

1.  在`src/router`文件夹中打开`index.js`。

1.  在`list`对象中，我们将把`path`属性从`'/'`改为`'/user'`，并为`alias`属性设置为`'/'`：

```js
{
  path: '/user',
  name: 'list',
  alias: '/',
  component: List, },
```

1.  在`view`对象中，我们将把`path`属性从`'/view/:id'`改为`'/user/:id'`，并将`alias`属性设置为`'/view/:id'`：

```js
{
  path: '/user/:id',
  name: 'view',
  alias: '/view/:id',
  component: View, },
```

1.  在`edit`对象中，我们将把`path`属性从`'/edit/:id'`改为`'/user/edit/:id'`，并将`alias`属性设置为`'/edit/:id'`。

```js
{
  path: '/user/edit/:id',
  name: 'edit',
  alias: '/edit/:id',
  component: Edit, },
```

1.  最后，在`create`对象中，我们将把`path`属性从`'/create'`改为`'/user/create'`，并将`alias`属性设置为`'/create'`：

```js
{
  path: '/user/create',
  name: 'create',
  alias: '/create',
  component: Create, },
```

## 工作原理…

当用户进入您的应用程序时，`vue-router`将尝试将路径与用户尝试访问的路径匹配。如果路由对象中有一个名为`alias`的属性，则`vue-router`将使用此属性在幕后维护旧路由，并使用别名路由。如果找到别名，则渲染该别名的组件，并且路由器保持为别名，不向用户显示更改，使其透明。

在我们的场景中，我们对应用程序进行了转换，现在处理所有在`/user`命名空间上调用的用户，但仍保持旧的 URL 结构，以便如果旧访问者尝试访问网站，他们将能够正常使用应用程序。

## 另请参阅

您可以在[`router.vuejs.org/guide/essentials/redirect-and-alias.html#alias`](https://router.vuejs.org/guide/essentials/redirect-and-alias.html#alias)找到有关`vue-router`别名的更多信息。

# 创建路由重定向

路由重定向几乎与路由别名相同，但主要区别在于用户确实被重定向到新的 URL。使用此过程，您可以管理如何加载新路由。

## 准备工作

此配方的先决条件如下：

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   `@vue/cli`

+   `@vue/cli-service-global`

## 如何做...

要启动我们的组件，我们将使用在'*创建路由别名*'配方中完成的 Vue-CLI 中的 Vue 项目，或者我们可以启动一个新的项目。

要启动一个新的项目，打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> vue create http-project
```

选择手动功能并将`Router`作为必需的功能添加到'*如何做...*'步骤中的'*创建一个简单路由*'配方中。

现在，在这些步骤中，我们将创建路由重定向规则：

1.  打开`src/router`文件夹中的`index.js`。

1.  在`routes`数组的末尾插入一个新的路由对象。这个对象将有两个属性，`path`和`redirect`。在`path`属性中，我们需要定义用户将输入的路径，`'/create-new-user'`，在`redirect`中，用户将被重定向到的路径，在这种情况下是`'/user/create'`。

```js
{
  path: '/create-new-user',
  redirect: '/user/create', },
```

1.  创建一个新对象，这个对象将有两个属性，`path`和`redirect`。在`path`属性中，我们需要定义用户将输入的路径，`'/users'`，在`redirect`中，我们将创建一个具有名为`name`的属性的对象，并将值设置为`'list'`。

```js
{
  path: '/users',
  redirect: {
  name: 'list',
  }, },
```

1.  创建一个新对象。这个对象将有两个属性，`path`和`redirect`。在`path`属性中，我们需要定义用户将输入的路径，`'/my-user/:id?'`，在`redirect`中，我们将创建一个函数，该函数将接收一个参数`to`，这是当前路由的对象。我们需要检查路由中是否存在用户 ID，以便将用户重定向到编辑页面。否则，我们将把他们重定向到用户列表。

```js
{
  path: '/my-user/:id?',
  redirect(to) {
  if (to.params.id) {
  return '/user/:id';
  }
  return '/user';
  }, },
```

1.  最后，在最后，我们将创建一个具有两个属性，`path`和`redirect`的路由对象。在`path`属性中，我们需要定义用户将输入的路径，`'/*'`，在`redirect`中，我们需要将`redirect`属性定义为`'/'`。

```js
{
  path: '*',
  redirect: '/', },
```

请记住，具有`'*'`的最后一个路由将始终是在用户尝试输入的 URL 中没有匹配时呈现的路由。

## 它是如何工作的...

当我们将`redirect`定义为一个新的路由时，它的工作方式类似于别名，但是`redirect`属性可以接收三种类型的参数：一个字符串，当重定向到路由本身时，对象，当使用其他参数重定向时，例如路由的名称，最后但并非最不重要的是函数类型，`redirect`可以处理并返回前两个对象中的一个，以便用户可以被重定向。

## 另请参阅

您可以在[`router.vuejs.org/guide/essentials/redirect-and-alias.html#redirect`](https://router.vuejs.org/guide/essentials/redirect-and-alias.html#redirect)找到有关`vue-router`重定向的更多信息。

# 创建嵌套路由视图

在`vue-router`中，嵌套路由就像是路由的命名空间，您可以在同一个路由内拥有多个级别的路由，使用基本视图作为主视图，并在其中呈现嵌套路由。

在多模块应用程序中，这用于处理像 CRUD 这样的路由，其中您将拥有一个基本路由，而子路由将是 CRUD 视图。

在这个配方中，您将学习如何创建嵌套路由。

## 准备工作

此配方的先决条件如下：

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   `@vue/cli`

+   `@vue/cli-service-global`

## 如何操作...

要启动我们的组件，我们将使用在“创建动态路由路径”配方中使用的 Vue 项目与 Vue-CLI，或者我们可以开始一个新的项目。

要开始一个新的，打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> vue create http-project
```

选择手动功能，并在“*如何操作...*”部分中将`Router`添加为必需功能，如“创建简单路由”配方中所示。

我们的配方将分为两部分：

+   在布局上创建`router-view`

+   更改路由文件

让我们开始吧。

### 在布局上创建`router-view`

在使用带有子路由的`vue-router`时，我们需要创建主视图，它将具有一个名为`RouterView`的特殊组件。此组件将在您呈现的布局或页面内呈现当前路由。

现在，在接下来的步骤中，我们将为页面创建布局：

1.  在`src/views`文件夹中，我们需要创建一个名为`user`的新文件夹，并将`Create`、`Edit`、`List`、和`View`页面移动到这个新文件夹中。

1.  在`user`文件夹中创建一个名为`Index.vue`的新文件并打开它。

1.  在单文件组件`<template>`部分中，添加一个`router-view`组件：

```js
<template>
  <router-view/>
</template>
<script>
  export default {
    name: 'User',
  }
</script>
```

### 更改路由文件

我们将创建一个新文件来管理用户的特定路由，这将帮助我们维护代码并使其更清晰。

#### 用户路由

在接下来的步骤中，我们将为用户创建路由：

1.  在`src/router`文件夹中创建一个名为`user.js`的新文件。

1.  导入`Index`、`List`、`View`、`Edit`和`Create`视图：

```js
import Index from '@/views/user/Index.vue'; import List from '@/views/user/List.vue'; import View from '@/views/user/View.vue'; import Edit from '@/views/user/Edit.vue'; import Create from '@/views/user/Create.vue';
```

1.  创建一个数组，并将其设置为文件的默认导出。在这个数组中，添加一个`route`对象，具有四个属性-`path`，`name`，`component`和`children`。将`path`属性设置为`'/user'`，将`name`属性定义为`'user'`，将`component`定义为导入的`Index`组件，最后，将`children`属性定义为空数组：

```js
export default [
 {  path: '/user',
  name: 'user',
  component: Index,
  children: [],
  }, ] 
```

1.  在`children`属性中，添加一个新的路由对象，具有三个属性-`path`，`name`和`component`。将`path`定义为`''`，`name`定义为`'list'`，最后，将`component`属性定义为导入的`List`组件：

```js
{
  path: '',
  name: 'list',
  component: List, },
```

1.  为视图路由创建一个路由对象，并使用与上一个`route`对象相同的结构。将`path`属性定义为`':id'`，将`name`定义为`'view'`，将`component`定义为导入的`View`组件：

```js
{
  path: ':id',
  name: 'view',
  component: View, },
```

1.  为`edit`路由创建一个路由对象，并使用与上一个`route`对象相同的结构。将`path`属性定义为`'edit/:id'`，将`name`定义为`'edit'`，将`component`定义为导入的`Edit`组件：

```js
{
  path: 'edit/:id',
  name: 'edit',
  component: Edit, },
```

1.  为`create`路由创建一个路由对象，使用与上一个`route`对象相同的结构。将`path`属性定义为`'create'`，将`name`定义为`'create'`，将`component`定义为导入的`Create`组件：

```js
{
  path: 'create',
  name: 'create',
  component: Create, },
```

#### 路由管理器

在以下步骤中，我们将创建路由管理器，该管理器将控制应用程序中的所有路由：

1.  在`src/router`文件夹中打开`index.js`。

1.  在`src/router`文件夹中导入新创建的`user.js`文件：

```js
import Vue from 'vue'; import VueRouter from 'vue-router'; import UserRoutes from './user';
```

1.  在`routes`数组中，将导入的`UserRoutes`作为解构数组添加：

```js
const routes = [
  ...UserRoutes,
  {
  path: '*',
  redirect: '/user',
  }, ];
```

## 工作原理...

`vue-router`提供了使用子路由作为当前视图或布局的内部组件的能力。这使得可以创建具有特殊布局文件的初始路由，并通过`RouterView`组件在此布局中呈现子组件。

这种技术通常用于在应用程序中定义布局并为模块设置命名空间，其中父路由可以具有一组特定顺序，这些顺序将对其每个子路由都可用。

## 另请参阅

您可以在[`router.vuejs.org/guide/essentials/nested-routes.html#nested-routes`](https://router.vuejs.org/guide/essentials/nested-routes.html#nested-routes)找到有关嵌套路由的更多信息。

# 创建 404 错误页面

有时您的用户可能会尝试输入旧链接或输入拼写错误，无法到达正确的路由，这应该直接导致找不到错误。

在这个配方中，您将学习如何在`vue-router`中处理 404 错误。

## 准备工作

此配方的先决条件如下：

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   `@vue/cli`

+   `@vue/cli-service-global`

## 如何做...

要开始我们的组件，我们将使用在“*创建嵌套路由视图*”配方中使用的 Vue 项目与 Vue-CLI，或者我们可以开始一个新的。

要开始一个新的，打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows），并执行以下命令：

```js
> vue create http-project
```

选择手动功能并将`Router`添加为所需功能，如“*如何做...*”部分在“*创建简单路由*”配方中所示。

我们的配方将分为两部分：

+   创建`NotFound`视图

+   更改路由文件

让我们开始吧。

### 创建 NotFound 视图

我们需要创建一个新的视图，当应用程序上没有匹配的路由时，将显示给用户。这个页面将是一个简单的通用页面。

#### 单文件组件<template>部分

在这部分中，我们将创建单文件组件的`<template>`部分。按照这些说明正确创建组件：

1.  在`src/views`文件夹中，创建一个名为`NotFound.vue`的新文件并打开它。

1.  创建一个`VsRow`组件，在其中创建四个`VsCol`组件。所有这些组件都将具有属性`vs-w`定义为`12`和`class`定义为`text-center`：

```js
<vs-row>
 <vs-col vs-w="12" class="text-center">
 <!-- Icon --> </vs-col>
 <vs-col vs-w="12" class="text-center">
 <!-- Title --> </vs-col>
 <vs-col vs-w="12" class="text-center">
 <!-- Text --> </vs-col>
 <vs-col vs-w="12" class="text-center">
 <!-- Button --> </vs-col> </vs-row>
```

1.  在第一个`VsCol`组件中，我们将添加一个`VsIcon`组件，并将属性 icon 定义为`sentiment_dissatisfied`，并将`size`定义为`large`：

```js
<vs-icon
  icon="sentiment_dissatisfied"
  size="large" />
```

1.  在第二个`VsCol`组件中，我们将为页面添加一个标题：

```js
<h1>Oops!</h1>
```

1.  在第三个`VsCol`组件中，我们需要创建将放置在页面上的文本：

```js
<h3>The page you are looking for are not here anymore...</h3>
```

1.  最后，在第四个`VsCol`组件上，我们将添加`VsButton`组件。此按钮将具有属性`type`定义为`relief`和`to`定义为`'/'`：

```js
<vs-button
  type="relief"
  to="/" >
  Back to Home...
</vs-button>
```

#### 单文件组件<style>部分

在这部分中，我们将创建单文件组件的`<style>`部分。按照这些说明正确创建组件：

1.  在`<style>`标签中添加`scoped`标签：

```js
<style scoped> </style>
```

1.  创建一个名为`.text-center`的新规则，其中`text-align`属性定义为`center`，`margin-bottom`定义为`20px;`：

```js
.text-center {
  text-align: center;
  margin-bottom: 20px; }  
```

### 更改路由文件

创建视图后，我们需要将其添加到路由并使其对用户可用。为此，我们需要将视图路由添加到路由管理器中。

在这些步骤中，我们将更改路由管理器，以添加新的错误页面：

1.  在`src/router`文件夹中打开`index.js`。

1.  导入`NotFound`组件：

```js
import Vue from 'vue'; import VueRouter from 'vue-router'; import UserRoutes from './user'; import NotFound from '@/views/NotFound';
```

1.  在`routes`数组中，在`UserRoutes`之后，添加一个新的`route`对象，具有两个属性，`path`和`redirect`。将`path`属性定义为`'/'`，将`redirect`属性定义为`'/user'`：

```js
{
  path: '/',
  redirect: '/user' },
```

1.  对于未找到的页面，我们需要创建一个新的路由对象，该对象需要放在`routes`数组的最后位置。这个路由对象将有两个属性，`path`和`component`。`path`属性将被定义为`'*'`，`component`将被定义为导入的`NotFound`视图：

```js
{
  path: '*',
  component: NotFound, },  
```

要运行服务器并查看您的组件，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> npm run serve
```

这是您的组件呈现并运行的方式：

![](img/1a7706e3-1b9c-4486-8750-851e47e3f35f.png)

## 它是如何工作的...

`vue-router`尝试找到用户想要访问的 URL 的最佳匹配；如果没有匹配项，`vue-router`将使用`'*'`路径作为这些情况的默认值，其中`*`表示用户输入的不在路由列表中的任何值。

因为在`vue-router`中匹配的过程是由路由的权重决定的，所以我们需要将错误页面放在最底部，这样`vue-router`在实际调用`NotFound`路由之前需要通过每个可能的路由。

## 另请参阅

您可以在[`router.vuejs.org/guide/essentials/history-mode.html#caveat`](https://router.vuejs.org/guide/essentials/history-mode.html#caveat)找到有关处理 vue-router 历史模式中的 404 错误的更多信息。

# 创建和应用身份验证中间件

在`vue-router`中，可以创建路由守卫-每次路由更改时运行的函数。这些守卫被用作路由管理过程中的中间件。通常将它们用作身份验证中间件或会话验证器。

在这个示例中，我们将学习如何创建身份验证中间件，向我们的路由添加元数据以使它们受限制，并创建登录页面。

## 准备工作

这个示例的先决条件如下：

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   `@vue/cli`

+   `@vue/cli-service-global`

## 如何做到...

要启动我们的组件，我们将使用在“创建 404 错误页面”配方中使用的 Vue-CLI 的 Vue 项目，或者我们可以启动一个新的项目。

要启动一个新的项目，打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> vue create http-project
```

选择手动特性，并在“*如何做...*”部分中添加`Router`作为必需特性，如“创建简单路由”配方中所示。

我们的配方将分为三个部分：

+   创建身份验证中间件

+   向路由添加元数据和中间件

+   将中间件附加到 vue-router 并创建登录页面

让我们开始。

### 创建登录视图

登录视图将是用户在未经过身份验证时看到的页面。我们将构建一个简单的页面，里面有两个输入框 - 一个卡片和一个按钮。

#### 单文件组件<script>部分

在这部分，我们将创建单文件组件的`<script>`部分。按照这些说明正确创建组件：

1.  在`src/views`文件夹中，创建一个名为`Login.vue`的新文件并打开它。

1.  创建一个包含`username`、`password`和`error`的`data`属性：

```js
data: () => ({
  username: '',
  password: '',
  error: false, }),
```

1.  然后创建一个名为`userSignIn`的方法的`methods`属性。此方法将验证`username`和`password`数据是否完整。如果是，它将在`sessionStorage`中创建一个名为`'auth'`的新密钥，其中包含`username`数据的加密字符串化 JSON。然后，将`error`设置为`false`并执行`$router.replace`将用户重定向到用户列表`'/user'`。如果任何字段未通过任何验证，该方法将将错误定义为`true`并返回`false`：

```js
methods: {
  userSignIn() {
  if (this.username && this.password) {
  window.sessionStorage.setItem('auth',
  window.btoa(JSON.stringify({
  username: this.username
          })
 ) );
  this.error = false;
  this.$router.replace('/user');
  }
  this.error = true;
  return false;
  }, }
```

#### 单文件组件<template>部分

在这部分，我们将创建单文件组件的`<template>`部分。按照这些说明正确创建组件：

1.  创建一个带有`VsRow`组件的`div.container`HTML 元素。`VsRow`组件将具有属性`vs-align`定义为`"center"`和`vs-justify`定义为`"center"`：

```js
<div class="container">
 <vs-row
  vs-align="center"
  vs-justify="center"
  >
  </vs-row>
</div>
```

1.  在`VsRow`组件内部，添加一个带有属性`vs-lg`定义为`4`，`vs-sm`定义为`6`和`vs-xs`定义为`10`的`VsCol`组件。然后，在`VsCol`组件内部，我们将创建一个带有`style`属性定义为`margin: 20px;`的`VsCard`组件：

```js
<vs-col
  vs-lg="4"
  vs-sm="6"
  vs-xs="10" >
 <vs-card
  style="margin: 20px;"
  >
  </vs-card>
</vs-col>
```

1.  在`VsCard`组件内部，创建一个带有名称为`header`的`slot`的动态`<template>`，一个`h3`HTML 元素和您的标题：

```js
<template slot="header">
 <h3>
  User Login
  </h3> </template>
```

1.  之后，创建一个`VsRow`组件，其中属性`vs-align`定义为`"center"`，`vs-justify`定义为`"center"`，并在其中放置两个`VsCol`组件，其中属性`vs-w`定义为`12`：

```js
<vs-row
  vs-align="center"
  vs-justify="center" >
 <vs-col vs-w="12">
  </vs-col>
 <vs-col vs-w="12">
  </vs-col>
</vs-row>
```

1.  在第一个`VsCol`组件上，我们将添加一个`VsInput`组件，其中属性`danger`定义为数据`error`的值，`danger-text`定义为错误时显示的文本，`label`定义为`"Username"`，`placeholder`定义为`"Username or e-mail"`，并且`v-model`指令绑定到`username`：

```js
<vs-input
  :danger="error"
  danger-text="Check your username or email"
  label="Username"
  placeholder="Username or e-mail"
  v-model="username" />
```

1.  在第二个`VsCol`组件中，我们将添加一个`VsInput`组件，其中属性`danger`定义为数据`error`的值，`danger-text`定义为错误时显示的文本，`label`定义为`"Password"`，`type`定义为`password`，`placeholder`定义为`"Your password"`，并且`v-model`指令绑定到`password`：

```js
<vs-input
  :danger="error"
  label="Password"
  type="password"
  danger-text="Check your password"
  placeholder="Your password"
  v-model="password" />
```

1.  最后，在卡片页脚中，我们需要创建一个动态的`<template>`，其中包含名为`footer`的插槽。在这个`<template>`中，我们将添加一个`VsRow`组件，其中`vs-justify`属性定义为`flex-start`，并插入一个`VsButton`，其中属性`color`定义为`success`，`type`定义为`filled`，`icon`定义为`account_circle`，`size`定义为`small`，并且`@click`事件监听器指向`userSignIn`方法：

```js
<template slot="footer">
 <vs-row vs-justify="flex-start">
 <vs-button
  color="success"
  type="filled"
  icon="account_circle"
  size="small"
  @click="userSignIn"
  >
  Sign-in
    </vs-button>
 </vs-row> </template>
```

#### 单文件组件<style>部分

在这部分，我们将创建单文件组件的`<style>`部分。按照以下说明正确创建组件：

1.  首先，我们需要使这个部分具有作用域，这样 CSS 规则就不会影响应用程序的任何其他组件：

```js
<style scoped></style>
```

1.  然后，我们需要为`container`类和`VsInput`组件添加规则：

```js
<style scoped>
  .container {
  height: 100vh;
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-content: center;
  }    .vs-input {
  margin: 5px;
  } </style>
```

要运行服务器并查看您的组件，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> npm run serve
```

这是您的组件呈现并运行的方式：

![](img/5b60b886-7306-4fd7-86ba-ea430ce27ea8.png)

### 创建中间件

所有`vue-router`中间件也可以称为导航守卫，并且它们可以附加到应用程序路由更改上。这些更改有一些钩子，您可以将其应用于您的中间件。身份验证中间件在路由更改之前发生，因此我们可以处理一切并将用户发送到正确的路由。

1.  在`src/router`文件夹中，创建一个名为`middleware`的新文件夹，然后创建并打开一个名为`authentication.js`的新文件。

1.  在这个文件中，我们将创建一个默认的`export`函数，它将有三个函数参数 - `to`，`from`和`next`。`to`和`from`参数是对象，`next`参数是一个回调函数：

```js
export default (to, from, next) => {  }; 
```

1.  我们需要检查我们被重定向到的路由是否具有设置为`true`的经过身份验证的`meta`属性，并且我们是否有一个具有`'auth'`键的`sessionStorage`项。如果通过了这些验证，我们可以执行`next`回调：

```js
if (to.meta.authenticated && sessionStorage.getItem('auth')) {
  return next(); }
```

1.  然后，如果第一个验证没有通过，我们需要检查我们将用户重定向到的路由是否具有经过身份验证的`meta`属性，并检查它是否为`false`值。如果验证通过，我们将执行`next`回调：

```js
if (!to.meta.authenticated) {
  return next(); }
```

1.  最后，如果我们的任何验证都没有通过，执行`next`回调，传递`'/login'`作为参数：

```js
next('/login');
```

### 将元数据和中间件添加到路由器

创建完我们的中间件后，我们需要定义哪些路由将被验证，哪些路由不会被验证。然后我们需要将中间件导入到路由器中，并在执行时定义它：

1.  在`src/router`文件夹中打开`user.js`。

1.  在每个`route`对象中，添加一个名为`meta`的新属性。这个属性将是一个对象，具有一个经过身份验证的`key`和一个值定义为`true`。我们需要对每个路由都这样做 - 即使是子路由：

```js
import Index from '@/views/user/Index.vue';  import List from '@/views/user/List.vue'; import View from '@/views/user/View.vue'; import Edit from '@/views/user/Edit.vue'; import Create from '@/views/user/Create.vue';   export default [
 {  path: '/user',
  name: 'user',
  component: Index,
  meta: {
  authenticated: true,
  },
  children: [
 {  path: '',
  name: 'list',
  component: List,
  meta: {
  authenticated: true,
  },
  },
  {
  path: ':id',
  name: 'view',
  component: View,
  meta: {
  authenticated: true,
  },
  },
  {
  path: 'edit/:id',
  name: 'edit',
  component: Edit,
  meta: {
  authenticated: true,
  },
  },
  {
  path: 'create',
  name: 'create',
  component: Create,
  meta: {
  authenticated: true,
  },
  },
  ],
  }, ]
```

1.  在`src/router`文件夹中打开`index.js`。

1.  导入新创建的中间件和`Login`视图组件：

```js
import Vue from 'vue'; import VueRouter from 'vue-router'; import UserRoutes from './user'; import NotFound from '@/views/NotFound'; import Login from '@/views/Login'; import AuthenticationMiddleware from './middleware/authentication';
```

1.  为登录页面视图创建一个新的`route`对象。这个路由对象将`path`设置为`'/login'`，`name`定义为`'login'`，`component`定义为`Login`，并且`meta`属性将具有`authenticated`键，其值设置为`false`：

```js
{
  path: '/login',
  name: 'login',
  component: Login,
  meta: {
  authenticated: false,
  }, },
```

1.  在错误处理路由上，我们将定义`meta`属性`authenticated`为`false`，因为登录视图是一个公共路由：

```js
{
  path: '*',
  component: NotFound,
  meta: {
  authenticated: false,
  }, },
```

1.  最后，在创建了`router`构造函数之后，我们需要在`beforeEach`执行中注入中间件：

```js
router.beforeEach(AuthenticationMiddleware);
```

## 它是如何工作的...

路由守卫作为中间件工作；它们在`vue-router`进程的每个生命周期中都有一个钩子被执行。对于这个示例，我们选择了`beforeEach`钩子来添加我们的中间件。

在这个钩子中，我们检查用户是否经过了身份验证，以及用户是否需要身份验证才能导航到该路由。在检查了这些变量之后，我们通过将用户发送到他们需要的路由来继续这个过程。

## 另请参阅

您可以在[`router.vuejs.org/guide/advanced/navigation-guards.html#global-before-guards`](https://router.vuejs.org/guide/advanced/navigation-guards.html#global-before-guards)找到有关 vue-router 路由守卫的更多信息。

# 异步加载您的页面

组件可以在需要时加载，路由也可以。使用`vue-router`的惰性加载技术可以在应用程序中进行更多的代码拆分和更小的最终捆绑包。

在这个配方中，我们将学习如何转换路由以异步加载它们。

## 准备工作

此配方的先决条件如下：

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   `@vue/cli`

+   `@vue/cli-service-global`

## 如何做...

要启动我们的组件，我们将使用在“*创建身份验证中间件*”配方中使用的 Vue 项目与 Vue-CLI，或者我们可以启动一个新的项目。

要启动新的路由管理器，请打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows），并执行以下命令：

```js
> vue create http-project
```

选择手动功能，并将`Router`添加为所需的功能，如“*如何做...*”部分和“*创建简单路由*”配方中所示。

我们的配方将分为两部分：

+   更新路由管理器

+   更新用户路由

让我们开始吧。

### 更新路由管理器

要更新路由管理器，请按照以下说明进行操作：

1.  在`src/router`文件夹中打开`index.js`文件。

1.  在每个具有`component`属性的路由中，我们将把组件的直接赋值转换为一个新函数。这将是一个返回 webpack 的`import()`方法的箭头函数：

```js
{
  path: '/login',
  name: 'login',
  component: () => import('@/views/Login'),
  meta: {
  authenticated: false,
 }, },
```

1.  在每个具有`component`属性的`route`对象上重复此过程。

### 更新用户路由

要更新用户路由，请按照以下说明进行操作：

1.  在`src/router`文件夹中打开`user.js`文件。

1.  在每个具有`component`属性的路由中，我们将把组件的直接赋值转换为一个新函数。这将是一个返回 webpack 的`import()`方法的箭头函数。

```js
{
  path: '/user',
  name: 'user',
  component: () => import('@/views/user/Index.vue'),
  meta: {
  authenticated: true,
  },
  children: [], },
```

1.  在每个具有`component`属性的`route`对象上重复此过程。

## 它是如何工作的...

在 ECMAScript 中，当我们使用`export default`方法时，`export`和`import`是具有预定义值的对象。这意味着当我们`import`一个新组件时，该组件已经指向该文件的`default export`。

为了进行延迟加载过程，我们需要传递一个在运行时执行的函数，该函数的返回值将是 webpack 在捆绑过程中分割的代码的一部分。

当我们在`vue-router`中调用这个函数时，`vue-router`不直接导入组件，而是进行验证检查，确保当前组件导入是一个需要执行的函数。在函数执行后，响应被用作将显示在用户屏幕上的组件。

由于 webpack 的`import()`方法是异步的，这个过程可以与其他代码执行同时进行，而不会干扰或阻塞 JavaScript 虚拟机的主线程。

## 另请参阅

您可以在[`router.vuejs.org/guide/advanced/lazy-loading.html`](https://router.vuejs.org/guide/advanced/lazy-loading.html)找到有关`vue-router`延迟加载的更多信息。

您可以在[`webpack.js.org/guides/code-splitting/`](https://webpack.js.org/guides/code-splitting/)找到有关`webpack`代码拆分的更多信息。

您可以在[`github.com/tc39/proposal-dynamic-import`](https://github.com/tc39/proposal-dynamic-import)找到有关 ECMAScript 动态导入提案的更多信息。
