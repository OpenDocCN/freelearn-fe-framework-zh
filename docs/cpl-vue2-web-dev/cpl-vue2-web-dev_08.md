# 第八章：介绍 Vue-Router 和加载基于 URL 的组件

在接下来的几章中，我们将创建一个商店界面。这个商店将结合我们迄今为止学到的所有知识，同时引入一些更多的技术、插件和功能。我们将学习如何从 CSV 文件中获取产品列表，显示它们及其变体，并按制造商或标签对产品进行过滤。我们还将学习如何创建产品详细视图，并允许用户向其在线购物篮中添加和删除产品和产品变体，例如尺寸或颜色。

所有这些都将使用 Vue、Vuex 和一个新的 Vue 插件 Vue-router 来实现。Vue-router 用于构建单页应用程序（SPA），它允许您将组件映射到 URL，或者在 VueRouter 术语中称为路由和路径。这是一个非常强大的插件，处理了许多处理 URL 所需的复杂细节。

本章将涵盖以下内容：

+   初始化 Vue-router 及其选项

+   使用 Vue-router 创建链接

+   创建动态路由以根据 URL 更新视图

+   在 URL 中使用 props

+   嵌套和命名路由

+   如何使用 Vue-router 进行编程导航

# 安装和初始化 Vue-router

与我们向应用程序添加 Vue 和 Vuex 的方式类似，您可以直接从 unpkg 中包含该库，或者转到以下 URL 并下载一个本地副本：[`unpkg.com/Vue-router`](https://unpkg.com/vue-router)。将 JavaScript 添加到新的 HTML 文档中，以及 Vue 和应用程序的 JavaScript。还要创建一个应用程序容器元素作为您的视图。在下面的示例中，我将 Vue-router 的 JavaScript 文件保存为`router.js`：

```js
<!DOCTYPE html>
<html>
<head>
  <title></title>
</head>
<body>
  <div id="app"></div>

  <script type="text/javascript" src="js/vue.js"></script>
  <script type="text/javascript" src="js/router.js"></script>
  <script type="text/javascript" src="js/app.js"></script>
</body>
</html>
```

在应用程序的 JavaScript 中初始化一个新的 Vue 实例：

```js
new Vue({
  el: '#app'
});
```

现在我们已经准备好添加 VueRouter 并利用其功能了。然而，在此之前，我们需要创建一些非常简单的组件，根据 URL 加载和显示它们。由于我们将使用路由器加载组件，因此不需要使用`Vue.component`注册它们，而是创建具有与 Vue 组件相同属性的 JavaScript 对象。

在这个第一个练习中，我们将创建两个页面——主页和关于页面。在大多数网站上都可以找到这些页面，它们应该帮助您了解加载的内容以及何时加载。在您的 HTML 页面中创建两个模板供我们使用：

```js
<script type="text/x-template" id="homepage">
  <div>
    <h1>Hello &amp; Welcome</h1>
    <p>Welcome to my website. Feel free to browse around.</p>
  </div>
</script>

<script type="text/x-template" id="about">
  <div>
    <h1>About Me</h1>
    <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Vivamus sed metus magna. Vivamus eget est nisi. Phasellus vitae nisi sagittis, ornare dui quis, pharetra leo. Nullam eget tellus velit. Sed tempor lorem augue, vitae luctus urna ultricies nec. Curabitur luctus sapien elit, non pretium ante sagittis blandit. Nulla egestas nunc sit amet tellus rhoncus, a ultrices nisl varius. Nam scelerisque lacus id justo congue maximus. Etiam rhoncus, libero at facilisis gravida, nibh nisi venenatis ante, sit amet viverra justo urna vel neque.</p>
    <p>Curabitur et arcu fermentum, viverra lorem ut, pulvinar arcu. Fusce ex massa, vehicula id eros vel, feugiat commodo leo. Etiam in sem rutrum, porttitor velit in, sollicitudin tortor. Interdum et malesuada fames ac ante ipsum primis in faucibus. Donec ac sapien efficitur, pretium massa at, vehicula ligula. Vestibulum turpis quam, feugiat sed orci id, eleifend pretium urna. Nullam faucibus arcu eget odio venenatis ornare.</p>
  </div>
</script>
```

不要忘记将所有内容封装在一个“根”元素中（在这里用包裹的`<div>`标签表示）。您还需要确保在加载应用程序 JavaScript 之前声明模板。

我们创建了一个 Home 页面模板，其`id`为`homepage`，以及一个 About 页面，其中包含一些来自*lorem ipsum*的占位文本，其`id`为`about`。在您的 JavaScript 中创建两个引用这两个模板的组件：

```js
const Home = {
  template: '#homepage'
};

const About = {
  template: '#about'
};
```

下一步是为路由器提供一个占位符来渲染视图中的组件。这可以通过使用自定义的`<router-view>` HTML 元素来完成。使用此元素可以控制内容的渲染位置。它允许我们在应用程序视图中拥有一个头部和页脚，而无需处理混乱的模板或包含组件本身。

在您的应用程序中添加一个`header`、`main`和`footer`元素。在 header 中放置一个 logo，在 footer 中放置 credits；在`main` HTML 元素中，放置`router-view`占位符：

```js
<div id="app">
  <header>
    <div>LOGO</div>
  </header>

  <main>
    <router-view></router-view>
  </main>

  <footer>
    <small>© Myself</small>
  </footer>
</div>
```

应用程序视图中的所有内容都是可选的，除了`router-view`之外，但它可以让您了解如何将路由器 HTML 元素实现到站点结构中。

下一步是初始化 Vue-router 并指示 Vue 使用它。创建一个`VueRouter`的新实例并将其添加到`Vue`实例中，类似于我们在前一节中添加`Vuex`的方式：

```js
const router = new VueRouter();

new Vue({
  el: '#app',

  router
});
```

现在我们需要告诉路由器我们的路由（或路径）以及在遇到每个路由时应加载的组件。在 Vue-router 实例内部创建一个具有`routes`键和数组值的对象。此数组需要为每个路由包含一个对象：

```js
const router = new VueRouter({
  routes: [
    {
 path: '/',
 component: Home
 },
 {
```

```js
 path: '/about',
 component: About
 }
  ]
});
```

每个路由对象都包含一个`path`和`component`键。`path`是您要在其上加载`component`的 URL 字符串。Vue-router 根据先到先得的原则提供组件。例如，如果有多个具有相同路径的路由，则使用遇到的第一个路由。确保每个路由都有开始斜杠-这告诉路由器它是一个根页面而不是子页面，我们将在本章后面介绍子页面。

按下保存并在浏览器中查看你的应用程序。你应该看到`Home`模板组件的内容。如果你观察 URL，你会注意到在页面加载时，路径后面会添加一个哈希和斜杠(`#/`)。这是路由器创建的一种浏览组件和利用地址栏的方法。如果你将其更改为第二个路由的路径，`#/about`，你将看到`About`组件的内容。

Vue-router 还可以使用 JavaScript 历史 API 来创建更漂亮的 URL。例如，`yourdomain.com/index.html#about`将变成`yourdomain.com/about`。这是通过在你的`VueRouter`实例中添加`mode: 'history'`来激活的：

```js
const router = new VueRouter({
  mode: 'history',

  routes: [
    {
      path: '/',
      component: Home
    },
    {
      path: '/about',
      component: About
    }
  ]
});
```

然而，这也需要一些服务器配置来捕获所有请求并将它们重定向到你的`index.html`页面，这超出了本书的范围，但在 Vue-router 文档中有详细说明。

# 更改 Vue-router 的文件夹

有时候你可能想要将 Vue 应用程序托管在网站的子文件夹中。在这种情况下，你需要声明项目的基本文件夹，以便 Vue-router 可以构建和监听正确的 URL。

例如，如果你的应用程序基于一个`/shop/`文件夹，你可以使用 Vue-router 实例上的`base`参数来声明它：

```js
const router = new VueRouter({
  base: '/shop/',

  routes: [
    {
      path: '/',
      component: Home
    },
    {
      path: '/about',
      component: About
    }
  ]
});
```

这个值需要在开头和结尾都有斜杠。

除了`base`之外，Vue-router 还有其他几个配置选项可用，值得熟悉它们，因为它们可能会解决你以后遇到的问题。

# 链接到不同的路由

路由器正常工作后，我们现在可以继续向应用程序中添加链接，允许用户在网站中导航。链接可以通过两种方式实现：我们可以使用传统的`<a href="#/about">`标签，或者我们可以利用路由器提供的新的 HTML 元素`<router-link to="/about">`。当使用`router-link`元素时，它的工作方式与`<a>`标签相同，在浏览器中运行时实际上会被转换为`<a>`标签，但允许更多的自定义和与路由器的集成。

强烈建议在可能的情况下使用`router-link`元素，因为它比标准链接具有几个优点：

+   **模式更改**：第一个优点与路由器的`mode`相关。使用路由链接可以更改路由器的模式，例如从哈希模式更改为历史模式，而不必更改应用程序中的每个链接。

+   **CSS 类**：使用路由链接的另一个优点是应用于“树”中活动链接和当前正在查看的页面的 CSS 类。树中的链接是父页面，也包括根页面（例如，任何链接到`/`的链接将始终具有活动类）。这是使用路由的一个重要优势，因为手动添加和删除这些类将需要复杂的编码。这些类可以进行自定义，我们将在稍后进行。

+   **URL 参数和命名路由**：使用路由元素的另一个优点是它使您能够使用命名路由和传递 URL 参数。这进一步允许您在页面的 URL 上拥有一个真实的来源，并使用名称和快捷方式引用路由。关于这一点，稍后在本章中将进行更详细的介绍。

在视图中添加页面链接，以便您可以在页面之间导航。在您的网站的`<header>`中，创建一个新的`<nav>`元素，其中包含一个无序列表。对于每个页面，添加一个包含`router-link`元素的新列表项。在链接路径上添加一个`to`属性：

```js
<nav>
  <ul>
    <li>
      <router-link to="/">Home</router-link>
    </li>
    <li>
      <router-link to="/about">About</router-link>
    </li>
  </ul>
</nav>
```

在浏览器中查看应用程序应该显示两个链接，允许您在两个内容页面之间切换。您还会注意到，通过点击链接，URL 也会更新。

如果您使用浏览器的 HTML 检查器检查链接，您会注意到 CSS 类的变化。主页链接始终具有`router-link-active`类 - 这是因为它本身处于活动状态，或者它有一个活动的子页面，例如关于页面。当您在两个页面之间导航时，还会添加和删除另一个 CSS 类 - `router-link-exact-active`。这个类仅适用于当前活动页面上的链接。

让我们自定义应用于视图的类。在 JavaScript 中的路由初始化部分，向对象添加两个新键 - `linkActiveClass`和`linkExactActiveClass`：

```js
const router = new VueRouter({
  routes: [
    {
      path: '/',
      component: Home
    },
    {
      path: '/about',
      component: About
    }
  ],

  linkActiveClass: 'active',
 linkExactActiveClass: 'current'
});
```

这些键应该相当容易理解，但是`linkExactActiveClass`应用于当前页面，即正在查看的页面，而`linkActiveClass`是当页面或其子页面之一处于活动状态时应用的类。

# 链接到子路由

有时您可能希望链接到子页面。例如`/about/meet-the-team`。幸运的是，不需要太多工作来实现这个功能。在`routes`数组中创建一个指向具有模板的新组件的新对象：

```js
const router = new VueRouter({
  routes: [
    {
      path: '/',
      component: Home
    },
    {
      path: '/about',
      component: About
    },
    {
 path: '/about/meet-the-team',
 component: MeetTheTeam
 }
  ],

  linkActiveClass: 'active',
  linkExactActiveClass: 'current'
});  
```

当导航到这个页面时，你会注意到 Home 和 About 链接都有`active`类，而没有我们创建的`current`类。如果你在导航中创建一个链接到这个页面，那么一个`current`类将被应用到它上面。

# 带参数的动态路由

Vue 路由器很容易让你拥有动态 URL。动态 URL 允许你使用相同的组件来显示不同的数据，同时使用相同的模板。一个例子是商店，所有的类别页面看起来都一样，但根据 URL 显示不同的数据。另一个例子是产品详情页面，你不想为每个产品都创建一个组件，所以你可以使用一个带有 URL 参数的组件。

URL 参数可以出现在路径的任何位置，可以有一个或多个。每个参数都被分配一个键，因此可以一致地创建和访问它们。我们将在第九章“使用 Vue-Router 动态路由加载数据”中更详细地介绍动态路由和参数。现在，我们将构建一个基本的示例。

在我们开始创建组件之前，让我们来看一下一个新的可用变量——`this.$route`。类似于我们如何使用 Vuex 访问全局存储一样，这个变量允许我们访问关于路由、URL 和参数的许多信息。

在你的 Vue 实例中，作为一个测试，添加一个`mounted()`函数。在`console.log`中插入`this.$route`参数：

```js
new Vue({
  el: '#app',

  router,
  mounted() {
 console.log(this.$route);
 }
});
```

如果你打开浏览器并查看开发者工具，你应该会看到一个对象被输出。查看这个对象将显示一些信息，比如路径和与当前路径匹配的组件。前往`/about` URL 将显示关于该对象的不同信息：

![](img/7dc8379e-4c58-4733-b535-4ba77c32c3a3.png)

让我们创建一个使用这个对象参数的组件。在你的路由数组中创建一个新对象：

```js
const router = new VueRouter({
  routes: [
    {
      path: '/',
      component: Home
    },
    {
      path: '/about',
      component: About
    },
    {
 path: '/user/:name',
 component: User
 }
  ],

  linkActiveClass: 'active',
  linkExactActiveClass: 'current'
}); 
```

你会注意到这个路径与之前的路径不同的地方是在路径中`name`之前有一个冒号。这告诉 Vue-router 这个 URL 的这部分是动态的，但该部分的变量名是`name`。

现在创建一个名为`User`的新组件，并为其创建一个模板。在这个例子中，我们的模板将是内联的，并且我们将使用 ES2015 模板语法。这种语法使用反引号，可以直接将变量和换行符传递到模板中，而无需对它们进行转义：

```js
const User = {
  template: `<h1>Hello {{ $route.params.name }}</h1>`
};
```

模板中输出的变量来自全局路由实例，并且是参数对象中的`name`变量。变量`name`引用路由路径中冒号前面的变量，在`routes`数组中。在组件模板中，我们还可以省略`$route`中的`this`变量。

返回浏览器，然后在 URL 末尾输入`#/user/sarah`。您应该在网页的主体中看到 Hello sarah。查看 JavaScript 浏览器控制台，您应该看到`params`对象中有一个键/值对`name: sarah`：

！[](assets/4a642ae5-1ec3-4b35-9d77-815766ecf654.png)

此变量也可在组件本身中使用。例如，如果我们想要将用户姓名的第一个字母大写，我们可以创建一个计算变量，该变量接受路由参数并进行转换：

```js
const User = {
  template: `<h1>Hello {{ name }}</h1>`,

  computed: {
 name() {
 let name = this.$route.params.name;
 return name.charAt(0).toUpperCase() + name.slice(1);
 }
 }
};
```

如果您不熟悉前面的代码在做什么，它会获取字符串的第一个字符并将其大写。然后，它在大写字母后拆分字符串（即，单词的其余部分）并将其附加到大写字母上。

添加此`computed`函数并刷新应用程序将产生 Hello sarah。

如前所述，路由可以接受任意数量的参数，并且可以由静态或动态变量分隔。

将路径更改为以下内容（保持组件名称相同）：

```js
/:name/user/:emotion
```

这意味着您需要转到`/sarah/user/happy`才能看到用户组件。但是，您将可以访问一个名为`emotion`的新参数，这意味着您可以使用以下模板来呈现 sarah is happy！：

```js
const User = {
  template: `<h1>{{ name }} is {{ $route.params.emotion }}</h1>`,

  computed: {
    name() {
      let name = this.$route.params.name;
      return name.charAt(0).toUpperCase() + name.slice(1);
    }
  }
};

const router = new VueRouter({
  routes: [
    {
      path: '/',
      component: Home
    },
    {
      path: '/about',
      component: About
    },
    {
 path: '/:name/user/:emotion',
      component: User
    }
  ],

  linkActiveClass: 'active',
  linkExactActiveClass: 'current'
});
```

在接下来的几章中，当我们构建商店时，动态路由将非常有用，因为我们将同时用于产品和类别。

# GET 参数

除了动态路由，Vue-router 还以一种非常简单的方式处理 GET 参数。GET 参数是您可以传递给网页的额外 URL 参数，它们显示为键/值对。使用 GET 参数，第一个参数前面有一个`?`-这告诉浏览器要期望参数。任何其他参数都由和号分隔。例如：

```js
example.com/?name=sarah&amp;emotion=happy
```

此 URL 将返回`name`的值为`sarah`，`emotion`的值为`happy`。它们通常用于过滤或搜索-下次在 Google 上搜索时，请查看 URL，您会注意到地址栏中的`?q=Your+search+query`。

Vue 路由器将这些参数在`this.$route`变量的`query`对象中提供给开发者。尝试在 URL 末尾添加`?name=sarah`并打开 JavaScript 开发者工具。检查查询对象将显示一个以`name`为键，`sarah`为值的对象：

![](img/456ca4d1-ec7e-4ea7-9dc1-9016bb558e65.png)

在构建商店类别的过滤器时，我们将使用查询对象。

# 使用 props

尽管直接在组件中使用路由参数完全可以正常工作，但这不是一个好的做法，因为它将组件直接与路由绑定在一起。相反，应该使用`props`，就像我们在本书中之前为 HTML 组件使用它们一样。当启用和声明后，通过 URL 传递的参数可以像通过 HTML 属性传递的参数一样使用。

使用 props 作为路由组件传递选项和参数是一种更好的方式，因为它有很多好处。首先，它将组件与特定的 URL 结构解耦-正如您将看到的，我们可以直接将 props 传递给组件本身。它还有助于使您的路由组件更清晰；传入的参数在组件本身中清晰地列出，并且整个组件的代码更清晰。

props 仅适用于动态路由-GET 参数仍然可以通过前面的技术访问。

使用前面的示例，为`name`和`emotion`参数声明`props`。当使用基于 URL 的变量时，您将希望使用`String`数据类型：

```js
const User = {
  template: `<h1>{{ name }} is {{ $route.params.emotion }}</h1>`,
  props: {
 name: String,
 emotion: String
 },
  computed: {
    name() {
      let name = this.$route.params.name;
      return name.charAt(0).toUpperCase() + name.slice(1);
    }
  }
};
```

现在，我们有了`this.name`可以通过`props`和计算值两次使用。然而，由于我们通过`props`有了`this.name`和`this.emotion`，我们可以更新我们的组件来使用这些变量，而不是`$route`参数。

为了避免与 prop 冲突，将计算函数更新为`formattedName()`。我们还可以从函数中删除变量声明，因为新变量更易读：

```js
const User = {
  template: `<h1>{{ formattedName }} is {{ this.emotion }}</h1>`,
  props: {
    name: String,
    emotion: String
  },
  computed: {
    formattedName() {
      return this.name.charAt(0).toUpperCase() + this.name.slice(1);
    }
  }
};
```

在`props`起作用之前，需要告诉 Vue-router 在特定路由中使用它们。这在`routes`数组中启用，逐个路由设置，并且最初设置为`props: true`：

```js
const router = new VueRouter({
  routes: [
    {
      path: '/',
      component: Home
    },
    {
      path: '/about',
      component: About
    },
    {
      path: '/:name/user/:emotion',
      component: User,
      props: true
    }
  ],

  linkActiveClass: 'active',
  linkExactActiveClass: 'current'
});
```

# 设置 prop 默认值

现在，由于路由参数可用作`props`，这使我们可以轻松创建默认值。如果我们想要使参数可选，我们需要添加几个`if()`语句来检查变量的存在。

然而，使用 props，我们可以像之前一样声明默认值。为情感变量添加一个默认值：

```js
const User = {
  template: `<h1>{{ formattedName }} is {{ this.emotion }}</h1>`,
  props: {
    name: String,
    emotion: {
 type: String,
 default: 'happy'
 }
  },
  computed: {
    formattedName() {
      return this.name.charAt(0).toUpperCase() + this.name.slice(1);
    }
  }
};
```

我们现在可以在路由器中创建一个新的路由，该路由使用相同的组件，但没有最后的变量。不要忘记为新路由启用`props`：

```js
const router = new VueRouter({
  routes: [
    {
      path: '/',
      component: Home
    },
    {
      path: '/about',
      component: About
    },
    {
 path: '/:name/user',
 component: User,
 props: true
 }, 
    {
      path: '/:name/user/:emotion',
      component: User,
      props: true
    }
  ],

  linkActiveClass: 'active',
  linkExactActiveClass: 'current'
});
```

现在，通过访问`/sarah/user`，我们应该看到声明 sarah 很开心的文本。

# 使用静态 props

除了布尔值之外，路由中的 props 参数还可以接受一个带有要传递的 props 列表的对象。这使您可以使用相同的组件并根据 URL 更改其状态，而无需通过路径传递变量，例如，如果您想要激活或停用模板的一部分。

当通过 URL 传递 props 对象时，它会覆盖整个 props 对象，这意味着您必须声明全部或全部。props 变量也将优先于动态的基于 URL 的变量。

将您的新的`/:name/user`路径更新为在路由中包含`props` - 从路径中删除`:name`变量，使其变为`/user`：

```js
const router = new VueRouter({
  routes: [
    {
      path: '/',
      component: Home
    },
    {
      path: '/about',
      component: About
    },
    {
      path: '/user',
      component: User,
      props: {
 name: 'Sarah',
 emotion: 'happy'
 }
    }, 
    {
      path: '/:name/user/:emotion',
      component: User,
      props: true
    }
  ],

  linkActiveClass: 'active',
  linkExactActiveClass: 'current'
});
```

导航到`/user`应该显示与之前相同的句子。在某些情况下，通过“幕后”传递`props`（而不是使用 URL）是理想的，因为您可能不希望用户共享特定的 URL 或根据易于更改的参数更改应用程序的状态。

# 嵌套路由

嵌套路由与子路由不同，因为它们存在于已经匹配路由开始部分的组件中。这使您可以在现有视图中显示不同的内容。

一个很好的例子是 Twitter。如果您访问 Twitter 用户的个人资料页面，您可以查看他们关注的人，关注他们的人以及他们创建的列表。如果您在浏览页面时观察 URL，您会注意到一个重复的模式：用户名后跟不同的页面。嵌套路由和子路由之间的区别在于，嵌套路由允许您在不同的子页面中保持组件相同（例如，标题和侧边栏）。

这样做的优点是用户可以收藏和分享链接，使页面更易访问，并且有利于 SEO。使用简单的切换或选项卡框来在视图中显示不同内容，很难实现这些优点。

要将 Twitter 模式复制到 Vue 路由中，可以按照以下方式进行设置：

```js
https://twitter.com/:user/:page
```

如果我们使用之前的路由方法来创建这个，我们将不得不为每个页面构建组件，在它们的模板中包含侧边栏中的标题和用户信息——如果您需要更新代码，这将是一件麻烦的事情！

让我们为我们的 About 页面创建一些嵌套路由。我们不会在我们的购物应用程序中使用嵌套路由，但了解 Vue 路由的功能是很重要的。

创建两个新组件——`AboutContact`，用于显示联系信息，以及`AboutFood`，用于详细介绍您喜欢吃的食物！虽然不是必需的，但在组件名称中保留对父组件（在本例中为 About）的引用是一个好主意——这样可以在以后查看它们时将组件联系在一起！为每个组件添加一个带有一些固定内容的模板：

```js
const AboutContact = {
  template: `<div>
    <h2>This is some contact information about me</h2>
    <p>Find me online, in person or on the phone</p>
  </div>`
};

const AboutFood = {
  template: `<div>
    <h2>Food</h2>
    <p>I really like chocolate, sweets and apples.</p>
  </div>`
};
```

下一步是在您的`#about`模板中创建用于渲染嵌套路由的占位符。该元素与我们之前看到的元素完全相同——`<router-view>`元素。为了证明它可以放置在任何位置，在模板的两个段落之间添加它：

```js
<script type="text/x-template" id="about">
  <div>
    <h1>About Me</h1>
    <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Vivamus sed metus magna. Vivamus eget est nisi. Phasellus vitae nisi sagittis, ornare dui quis, pharetra leo. Nullam eget tellus velit. Sed tempor lorem augue, vitae luctus urna ultricies nec. Curabitur luctus sapien elit, non pretium ante sagittis blandit. Nulla egestas nunc sit amet tellus rhoncus, a ultrices nisl varius. Nam scelerisque lacus id justo congue maximus. Etiam rhoncus, libero at facilisis gravida, nibh nisi venenatis ante, sit amet viverra justo urna vel neque.</p>

    <router-view></router-view>

    <p>Curabitur et arcu fermentum, viverra lorem ut, pulvinar arcu. Fusce ex massa, vehicula id eros vel, feugiat commodo leo. Etiam in sem rutrum, porttitor velit in, sollicitudin tortor. Interdum et malesuada fames ac ante ipsum primis in faucibus. Donec ac sapien efficitur, pretium massa at, vehicula ligula. Vestibulum turpis quam, feugiat sed orci id, eleifend pretium urna. Nullam faucibus arcu eget odio venenatis ornare.</p>
  </div>
</script>
```

在浏览器中查看 About 页面不会渲染任何内容，也不会破坏应用程序。下一步是为这些组件添加嵌套路由到路由器中。我们不是将它们添加到顶级`routes`数组中，而是在`/about`路由内创建一个数组，键为`children`。该数组的语法与主数组完全相同，即路由对象的数组。

为每个`routes`添加一个包含`path`和`component`键的对象。需要注意的是，如果希望路径添加到父路径的末尾，路径不应以`/`开头。

例如，如果您希望 URL 为`/about/contact`来渲染`AboutContact`组件，您可以将路由组件设置如下：

```js
const router = new VueRouter({
  routes: [
    {
      path: '/',
      component: Home
    },
    {
      path: '/about',
      component: About,
      children: [
 {
 path: 'contact', 
 component: AboutContact
 }, 
 {
 path: 'food', 
 component: AboutFood
 }
 ]
    }
  ],

  linkActiveClass: 'active',
  linkExactActiveClass: 'current'
});
```

然而，如果你希望 URL 只是简单地为`/contact`，但仍然在`About`组件内渲染`AboutContact`组件，你可以添加前导斜杠。尝试在没有斜杠的情况下查看应用程序，然后添加斜杠，看看它所产生的差异。如果你希望在父级加载时显示一个子路由，而 URL 没有第二部分，你可以使用空路径——`path: ''`。

现在，保留没有斜杠，并添加前导的`children`数组。转到浏览器并导航到 About 页面。在 URL 的末尾添加`/contact`或`/food`，注意新内容出现在您之前添加到模板中的`<router-link>`元素的位置。

可以从任何地方创建到这些组件的链接，方式与您链接 Home 和 About 页面的方式相同。您可以将它们添加到`about`模板中，这样它们只会在导航到该页面时出现，或者将它们添加到应用程序视图中的主导航中。

# 创建一个 404 页面

在构建应用程序或网站时，尽管有着良好的意图，问题、错误和失误仍然会发生。因此，设置错误页面是一个好主意。最常见的页面是 404 页面——当链接不正确或页面已移动时显示的消息。404 是页面未找到的官方 HTTP 代码。

如前所述，Vue-router 将根据先到先得的原则匹配路由。我们可以利用这一点，将通配符（`*`）字符作为最后一个路由。由于通配符匹配*每个*路由，只有未匹配到先前路由的 URL 才会被此路由捕获。

创建一个名为`PageNotFound`的新组件，使用简单的模板，并添加一个新的路由，使用通配符作为路径：

```js
const PageNotFound = {
 template: `<h1>404: Page Not Found</h1>`
};

const router = new VueRouter({
  routes: [
    {
      path: '/',
      component: Home
    },
    {
      path: '/about',
      component: About,
      children: [
        {
          path: 'contact', 
          component: AboutContact
        }, 
        {
          path: 'food', 
          component: AboutFood
        }
      ]
    },
 {
 path: '*', 
 component: PageNotFound
 }
  ],

  linkActiveClass: 'active',
  linkExactActiveClass: 'current'
});
```

在浏览器中打开应用程序，输入 URL 的末尾的任何内容（除了`about`），然后按下*Enter*键，您应该看到 404 标题。

尽管这是模拟一个页面未找到的请求，但它实际上没有向浏览器发送正确的 HTTP 代码。如果您在生产中使用 Vue Web 应用程序，建议设置服务器端错误检查，以便在 URL 不正确的情况下可以正确通知浏览器。

# 命名组件、路由和视图

在使用`Vue-router`时，不需要为路由和组件添加名称，但这是一个好的实践和一个好习惯。

# 命名组件

具有名称的组件使您能够更轻松地调试错误。在 Vue 中，当从组件中抛出 JavaScript 错误时，它将给出该组件的名称，而不是将`Anonymous`列为组件。

例如，如果您尝试在食品组件中输出一个不可用的变量`{{ test }}`，默认情况下，JavaScript 控制台错误将如下所示：

![](img/347a8709-6e4e-4b4a-9355-8d11bcbb4002.png)

请注意堆栈中的两个`<Anonymous>`组件。

通过为我们的组件添加名称，我们可以轻松地确定问题所在。在下面的示例中，已经为`About`和`AboutFood`组件添加了名称：

![](img/143646ff-e373-42ac-ace2-b2e48a68ae04.png)

您可以轻松地看到错误在`<AboutFood>`组件中。

为组件添加名称就像在对象中添加一个名为`name`的键，并将名称作为值一样简单。这些名称遵循与创建 HTML 元素组件时相同的规则：不允许空格，但允许连字符和字母。为了让我能够快速识别代码，我选择将组件命名为与定义它的变量相同：

```js
const About = {
  name: 'About',
  template: '#about'
};

const AboutFood = {
  name: 'AboutFood',
  template: `<div>
    <h2>Food</h2>
    <p>I really like chocolate, sweets and apples.</p>
  </div>`
}
```

# 命名路由

在使用`VueRouter`时，您还可以为路由本身命名。这使您能够简化路由的位置并更新路径，而无需在应用程序中查找和替换所有实例。

请按照以下示例将`name`键添加到您的`routes`中：

```js
const router = new VueRouter({
  routes: [
    {
      path: '/',
      component: Home
    },
    {
      path: '/about',
      component: About,
      children: [
        {
          name: 'contact',
          path: 'contact', 
          component: AboutContact
        }, 
        {
          name: 'food',
          path: 'food', 
          component: AboutFood
        }
      ]
    },
    {
      path: '*', 
      component: PageNotFound
    }
  ],

  linkActiveClass: 'active',
  linkExactActiveClass: 'current'
});
```

现在，您可以在创建`router-link`组件时使用该名称，如下所示：

```js
<router-link :to="{name: 'food'}">Food</router-link>
```

注意`to`属性前面的冒号。这确保内容被解析为对象，而不是字面字符串。使用命名路由的另一个优点是能够向我们的动态路径传递特定属性。使用本章前面的示例，我们可以以编程方式构建 URL，将数据从路径构建中抽象出来。这就是命名路由真正发挥作用的地方。假设我们有以下路径：

```js
{ name: 'user', path: '/:name/user/:emotion', component: User }
```

我们需要向 URL 传递一个名称和情感变量以供组件渲染。我们可以像之前那样直接传递给 URL，或者使用带有命名路由的`to`对象表示法：

```js
<router-link :to="{name: 'user', params: { name: 'sarah', emotion: 'happy' }}">
  Sarah is Happy
</router-link>
```

在浏览器中查看将显示生成的锚链接：

```js
/sarah/user/happy
```

这使我们能够重新排列 URL，使用变量，而无需更新应用的其余部分。如果您想在 URL 末尾传递参数（例如`?name=sarah`），则可以将`params`键更改为`query`，因为它遵循相同的格式：

```js
<router-link :to="{name: 'user', query: { name: 'sarah', emotion: 'happy' }}">
  Sarah is Happy
</router-link>
```

通过重新配置路径以不接受参数，将生成以下链接：

```js
/user?name=sarah&amp;emotion=happy
```

在交换`params`和`query`时要小心-它们可能会影响您使用`path`还是`name`。当使用`path`时，`params`对象将被忽略，而`query`对象不会被忽略。要使用`params`对象，您需要使用命名路由。或者，使用`$`变量将参数传递到`path`中。

# 命名视图

Vue 路由还允许您为视图命名，从而可以将不同的组件传递给应用程序的不同部分。一个例子是商店，其中有侧边栏和主要内容区域。不同的页面可以以不同的方式利用这些区域。

About 页面可以使用主要内容显示关于内容，同时使用侧边栏显示联系方式。然而，商店页面将使用主要内容列出产品，并使用侧边栏显示过滤器。

为此，请创建第二个`router-view`元素作为原始元素的兄弟元素。保留原始元素的位置，但是给第二个元素添加一个`name`属性，并给它一个适当的标题：

```js
<main>
  <router-view></router-view>
</main>

<aside>
    <router-view name="sidebar"></router-view>
</aside>
```

在路由实例中声明路由时，我们现在将使用一个新的键`components`，并删除之前的单数`component`键。这个键接受一个对象，其中包含视图的名称和组件的名称的键值对。

建议将主路由保持未命名，这样您就不需要更新每个路由。如果您决定给主路由命名，那么您需要为应用中的每个路由执行下一步操作。

更新`About`路由以使用这个新的键，并将其转换为一个对象。下一步是告诉代码每个组件将放在哪里。

使用`default`作为键，将`About`组件设置为值。这将把 About 组件的内容放在未命名的`router-view`中，即主要的那个。这也是使用单数`component`键的简写方式：

```js
const router = new VueRouter({
  routes: [
    {
      path: '/',
      component: Home
    },
    {
      path: '/about',
      components: {
 default: About
 }
    },
    {
      path: '*', 
      component: PageNotFound
    }
  ],

  linkActiveClass: 'active',
  linkExactActiveClass: 'current'
});
```

接下来，添加第二个键值对，指定第二个`router-view`的名称为`sidebar`。当导航到`/about` URL 时，将使用您想要填充此区域的组件，我们将使用`AboutContact`组件：

```js
const router = new VueRouter({
  routes: [
    {
      path: '/',
      component: Home
    },
    {
      path: '/about',
      components: {
        default: About,
        sidebar: AboutContact
      }
    },
    {
      path: '*', 
      component: PageNotFound
    }
  ],

  linkActiveClass: 'active',
  linkExactActiveClass: 'current'
});
```

在浏览器中运行应用程序将呈现两个组件，联系组件的内容将显示在侧边栏中。

# 以编程方式导航、重定向和添加别名

在构建应用程序时，可能会遇到一些需要不同导航技术的情况。这些可能是以编程方式导航，例如在组件或主 Vue 实例中，当用户访问特定 URL 时重定向用户，或者使用不同的 URL 加载相同的组件。

# 以编程方式导航

您可能希望从代码、组件或操作中更改路径、URL 或用户流程。一个例子是在用户添加了一个项目后将其发送到购物篮。

为此，您可以在路由器实例上使用`push()`函数。push 的值可以是直接 URL 的字符串，也可以是接受命名路由或路由参数的对象。`push`函数的允许内容与`router-link`元素上的`to=""`属性完全相同。例如：

```js
const About = {
  name: 'About',
  template: '#about',
  methods: {
    someAction() {
      /* Some code here */

      // direct user to contact page
      this.$router.push('/contact');
    }
  }
};
```

或者，您可以使用带参数的命名路由进行重定向：

```js
this.$router.push({name: 'user', params: { name: 'sarah', emotion: 'happy' }});
```

# 重定向

使用`VueRouter`进行重定向非常简单。重定向的一个例子可能是将您的`/about`页面移动到`/about-us` URL。您将希望将第一个 URL 重定向到第二个 URL，以防有人分享或收藏了您的链接，或者搜索引擎缓存了 URL。

您可能会想要创建一个基本组件，当创建时使用`router.push()`函数将用户发送到新的 URL。

相反，您可以添加一个路由并在其中指定重定向：

```js
const router = new VueRouter({
  routes: [
    {
      path: '/',
      component: Home
    },
    {
 path: '/about',
 redirect: '/about-us'
 },
    {
      path: '/about-us',
      component: About
    },
    {
      path: '*', 
      component: PageNotFound
    }
  ],

  linkActiveClass: 'active',
  linkExactActiveClass: 'current'
});
```

再次强调，重定向键的内容可以是一个字面字符串或一个对象，就像`push()`函数一样。在上述示例中，如果用户访问`/about`，他们将立即重定向到`/about-us`并显示`About`组件。

# 别名路由

可能有一些情况下，您希望在两个 URL 下显示相同的组件。虽然不推荐作为标准做法，但在某些边缘情况下，这是必需的。

别名键会添加到现有路由中，并接受一个路径的字符串。使用上述示例，无论用户访问`/about`还是`/about-us`，都将显示`About`组件：

```js
const router = new VueRouter({
  routes: [
    {
      path: '/',
      component: Home
    },
    {
      path: '/about',
 alias: '/about-us',
      component: About,
    },
    {
      path: '*', 
      component: PageNotFound
    }
  ],

  linkActiveClass: 'active',
  linkExactActiveClass: 'current'
});
```

# 摘要

现在，您应该熟悉 Vue-router，知道如何初始化它，了解可用的选项，以及如何创建静态和动态的新路由。在接下来的几章中，我们将开始创建我们的商店，首先加载一些商店数据并创建一个产品页面。
