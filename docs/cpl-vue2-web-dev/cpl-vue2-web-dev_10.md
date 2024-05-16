# 第十章：构建电子商务商店-浏览产品

在第九章中，我们将产品数据加载到 Vuex 存储中，并创建了一个产品详细页面，用户可以在该页面查看产品及其变体。在查看产品详细页面时，用户可以从下拉菜单中更改变体，价格和其他详细信息将更新。

在本章中，我们将：

+   创建一个具有特定产品的主页列表页面

+   创建一个具有可重用组件的类别页面

+   创建一个排序机制

+   动态创建过滤器，并允许用户过滤产品

# 列出产品

在创建任何过滤、精选列表、排序组件和功能之前，我们需要创建一个基本的产品列表-首先显示所有产品，然后我们可以创建一个分页组件，然后在整个应用程序中重复使用它。

# 添加一个新的路由

让我们向我们的`routes`数组添加一个新的路由。现在，我们将在`HomePage`组件上工作，它将有一个`/`路由。确保将其添加到`routes`数组的顶部，这样它就不会被其他组件覆盖：

```js
const router = new VueRouter({
  routes: [
 {
 path: '/',
 name: 'Home',
 component: HomePage
 },
    {
      path: '/product/:slug', 
      component: ProductPage
    },

    {
      path: '/404', 
      alias: '*',
      component: PageNotFound
    }
  ]
});
```

在`HomePage`组件内部，创建一个新的`computed`属性，并从`store`中收集所有产品。在模板中显示任何内容之前，请确保产品已加载。使用以下代码填充`HomePage`组件：

```js
const HomePage = {
  name: 'HomePage',

  template: `<div v-if="products"></div>`,

  computed: {
 products() {
 return this.$store.state.products;
 }
 }
};
```

# 循环遍历产品

在查看任何商店的类别列表时，显示的数据往往具有重复的主题。通常包括图像、标题、价格和制造商。

在模板中添加一个有序列表-由于产品将按顺序排列，将它们放在有序列表中在语义上是有意义的。在`<ol>`中，添加一个`v-for`循环遍历产品，并显示每个产品的标题，如下所示。在继续显示之前，确保`product`变量存在也是一个好习惯：

```js
template: `<div v-if="products">
  <ol>
 <li v-for="product in products" v-if="product">
 <h3>{{ product.title }}</h3>
 </li>
 </ol>
</div>`,
```

在浏览器中查看页面时，您可能会注意到产品列表非常长。为所有这些产品加载图像将对用户的计算机造成巨大负担，并且会用那么多产品来压倒用户。在向我们的模板添加更多信息（如价格和图像）之前，我们将查看对产品进行分页，允许以更可管理的方式访问数据。

# 创建分页

创建分页，最初似乎相当简单——只需要返回一定数量的产品即可。然而，如果我们希望使我们的分页与产品列表交互并对其进行响应，它就需要更加先进一些。我们需要构建我们的分页能够处理不同长度的产品——在产品列表被过滤为较少产品的情况下。

# 计算数值

创建分页组件和显示正确产品的算术依赖于四个主要变量：

+   **每页项目数**：通常由用户设置；然而，我们将首先使用一个固定的数值 12

+   **总项目数**：这是要显示的产品总数

+   **页数**：这可以通过将产品数除以每页项目数来计算得到

+   **当前页码**：这个与其他变量结合起来，将允许我们返回我们需要的确切产品

通过这些数字，我们可以计算出我们分页所需的一切。这包括要显示的产品、是否显示下一页/上一页链接，以及如果需要的话，跳转到不同链接的组件。

在继续之前，我们将把我们的`products`对象转换成一个数组。这样我们就可以在其上使用 split 方法，从而返回一个特定的产品列表。这也意味着我们可以很容易地计算出总项目数。

更新你的`products`计算函数，将其返回一个数组而不是一个对象。这可以通过使用`map()`函数来实现——这是一个 ES2015 中用来替代简单`for`循环的函数。这个函数现在返回一个包含产品对象的数组：

```js
products() {
  let products = this.$store.state.products;
 return Object.keys(products).map(key => products[key]);
},
```

在计算对象中创建一个名为`pagination`的新函数。这个函数将返回一个包含有关我们分页的各种数据的对象，例如总页数。这将允许我们创建一个产品列表并更新导航组件。只有在我们的`products`变量有数据时才需要返回这个对象。下面的代码片段显示了这个函数：

```js
computed: {
  products() {
    let products = this.$store.state.products;
    return Object.keys(products).map(key => products[key]);
  },

  pagination() {
 if(this.products) {

 return {

 }
 }
 }
},
```

现在我们需要跟踪两个变量——每页项目数和当前页码。在你的`HomePage`组件上创建一个`data`函数并存储这两个变量。我们将在稍后给用户更新`perPage`变量的能力。下面的代码部分显示了我们的`data`函数：

```js
const HomePage = {
  name: 'HomePage',

  template: `...`,

 data() {
 return {
 perPage: 12, 
 currentPage: 1
 }
 },

  computed: {
    ...
  }
};
```

你可能想知道何时在组件上使用本地数据，何时将信息存储在 Vuex 存储中。这完全取决于您将在何处使用数据以及将对其进行何种操作。一般规则是，如果只有一个组件使用数据并对其进行操作，则使用本地的`data()`函数。然而，如果有多个组件将与变量进行交互，则将其保存在中央存储中。

回到`pagination()`计算函数，存储一个变量，其值为`products`数组的长度。有了这个变量，我们现在可以计算总页数。为了做到这一点，我们将进行以下方程计算：

*产品总数 / 每页项目数*

一旦我们得到了这个结果，我们需要将其四舍五入到最近的整数。这是因为如果有任何余数，我们需要为其创建一个新页面。

例如，如果您每页显示 12 个项目，并且您有 14 个产品，那么结果将是 1.1666 页-这不是一个有效的页码。将其四舍五入确保我们有两个页面来显示我们的产品。为此，使用`Math.ceil()` JavaScript 函数。我们还可以将产品的总数添加到输出中。请查看以下代码以使用`Math.ceil()`函数：

```js
pagination() {
  if(this.products) {
    let totalProducts = this.products.length;

    return {
 totalProducts: totalProducts,
 totalPages: Math.ceil(totalProducts / this.perPage)
    }
  }
}
```

我们需要做的下一个计算是确定当前页面的产品范围是什么。这有点复杂，因为我们不仅需要确定我们从页码中需要什么，而且数组切片是基于项目索引的-这意味着第一个项目是`0`。

为了确定从哪里获取切片，我们可以使用以下计算：

*(当前页码 * 每页项目数) - 每页项目数*

最后的减法可能看起来很奇怪，但它意味着在第一页上，结果是 0。这使我们能够计算出我们需要从`products`数组中切片的索引位置。

举个例子，如果我们在第三页，结果将是 24，这是第三页的起始位置。切片的结束位置是这个结果加上每页的项目数。这种方法的优点是我们可以更新每页的项目数，所有的计算都会更新。

在`pagination`结果中创建一个对象，其中包含这两个结果-这将使我们以后可以轻松访问它们：

```js
pagination() {
  if(this.products) {
    let totalProducts = this.products.length,
      pageFrom = (this.currentPage * this.perPage) - this.perPage;

    return {
      totalProducts: totalProducts,
      totalPages: Math.ceil(totalProducts / this.perPage),
      range: {
 from: pageFrom,
 to: pageFrom + this.perPage
 }
    }
  }
}
```

# 显示分页列表

计算出我们的分页属性后，我们现在可以使用起始点和结束点来操作我们的`products`数组。我们将使用一个方法来截断产品列表，而不是使用硬编码的值或使用另一个计算函数。这样做的好处是可以传递任何产品列表，并且意味着 Vue 不会缓存结果。

在组件中创建一个新的方法对象，其中包含一个名为`paginate`的新方法。这个方法应该接受一个参数，这个参数将是我们要切片的`products`数组。在函数内部，我们可以使用之前计算的两个变量来返回正确数量的产品：

```js
methods: {
  paginate(list) {
    return list.slice(
      this.pagination.range.from, 
      this.pagination.range.to
    );
  }
}
```

更新模板以在循环遍历产品时使用此方法：

```js
template: `<div v-if="products">
  <ol>
    <li v-for="product in paginate(products)" v-if="product">
      <h3>{{ product.title }}</h3>
    </li>
  </ol>
</div>`,
```

现在我们可以在浏览器中查看它，并注意它返回我们对象中的前 12 个产品。在`data`对象中更新`currentPage`变量为 2 或 3 将显示不同的产品列表，具体取决于数量。

为了继续我们对产品列表的语义化方法，当不在第一页时，我们应该更新有序列表的起始位置。这可以使用`start`的 HTML 属性来完成 - 这允许您指定应该从哪个数字开始有序列表。

使用`pagination.range.from`变量来设置有序列表的起始点 - 记住在第一页上要添加`1`，因为它将是`0`：

```js
template: `<div v-if="products">
  <ol :start="pagination.range.from + 1">
    <li v-for="product in paginate(products)" v-if="product">
      <h3>{{ product.title }}</h3>
    </li>
  </ol>
</div>`
```

现在在代码中递增页面编号时，您会注意到有序列表从每个页面的适当位置开始。

# 创建分页按钮。

通过代码更新页面编号不够用户友好，所以我们应该添加一些页面来增加和减少页面编号变量。为此，我们将创建一个函数，将`currentPage`变量更改为其值。这样我们就可以同时用于“下一页”和“上一页”按钮，以及一个带有编号的页面列表（如果需要）。

首先，在您的`pagination`容器中创建两个按钮。如果我们处于导航的极限位置，我们希望禁用这些按钮 - 例如，当返回时，您不希望能够低于`1`，并且在向前时超过最大页面数。我们可以通过在按钮上设置`disabled`属性来实现这一点 - 就像我们在产品详细信息页面上所做的，并将当前页面与这些限制进行比较。

在上一页按钮上添加`disabled`属性，并检查当前页面是否为第一页。在下一页按钮上，将其与我们的`pagination`方法的`totalPages`值进行比较。实现前面提到的属性的代码如下所示：

```js
<button :disabled="currentPage == 1">Previous page</button>
<button :disabled="currentPage == pagination.totalPages">Next page</button>
```

将`currentPage`变量设置回`1`，并在浏览器中加载主页。您会注意到上一页按钮被禁用了。如果您更改了`currentPage`变量，您会注意到按钮会按预期地变为激活或非激活状态。

现在我们需要为按钮创建一个点击方法来更新`currentPage`。创建一个名为`toPage()`的新函数。它应该接受一个变量 - 这将直接更新`currentPage`变量：

```js
methods: {
 toPage(page) {
 this.currentPage = page;
 },

  paginate(list) {
    return list.slice(this.pagination.range.from, this.pagination.range.to);
  }
}
```

为按钮添加点击处理程序，对于下一页按钮，传递`currentPage + 1`，对于上一页按钮，传递`currentPage - 1`：

```js
template: `<div v-if="products">
  <button @click="toPage(currentPage - 1)" :disabled="currentPage == 1">Previous page</button>
  <button @click="toPage(currentPage + 1)" :disabled="currentPage == pagination.totalPages">Next page</button>

  <ol :start="pagination.range.from + 1">
    <li v-for="product in paginate(products)" v-if="product">
      <h3>{{ product.title }}</h3>
    </li>
  </ol>
</div>`
```

现在我们可以在产品之间来回导航了。作为用户界面的一个很好的补充，我们可以使用代码中提到的可用变量来指示页面编号和剩余页面数量：

```js
template: `<div v-if="products">
  <p>
 Page {{ currentPage }} out of {{ pagination.totalPages }}
 </p>
  <button @click="toPage(currentPage - 1)" :disabled="currentPage == 1">Previous page</button>
  <button @click="toPage(currentPage + 1)" :disabled="currentPage == pagination.totalPages">Next page</button>

  <ol :start="pagination.range.from + 1">
    <li v-for="product in paginate(products)" v-if="product">
      <h3>{{ product.title }}</h3>
    </li>
  </ol>
</div>`
```

# 在导航时更新 URL

改进用户体验的另一个方法是在页面导航时更新 URL - 这将允许用户分享 URL、将其加入书签并稍后返回。在分页时，页面是一个*临时*状态，不应该是 URL 的主要终点。相反，我们可以利用 Vue 路由的查询参数。

更新`toPage`方法，在页面更改时将参数添加到 URL 中。这可以通过使用`$router.push`来实现，但是，我们需要小心，不要删除可能用于将来的过滤的任何现有参数。这可以通过将路由的当前查询对象与包含`page`变量的新对象合并来实现：

```js
toPage(page) {
  this.$router.push({
 query: Object.assign({}, this.$route.query, {
 page
 })
 }); 
  this.currentPage = page;
},
```

在从页面到页面的导航过程中，您会注意到 URL 获取了一个新的参数`?page=`，其值等于当前页面的名称。然而，按下刷新按钮将不会得到正确的页面结果，而是重新回到第一页。这是因为我们需要将当前的`page`查询参数传递给我们的`HomePage`组件中的`currentPage`变量。

这可以通过`created()`函数来完成 - 更新变量 - 确保我们首先检查了它的存在。`created`函数是 Vue 生命周期的一部分，在第四章中有介绍，*使用 Dropbox API 获取文件列表*：

```js
created() {
 if(this.$route.query.page) {
 this.currentPage = parseInt(this.$route.query.page);
 }
}
```

我们需要确保`currentPage`变量是一个整数，以帮助我们进行后续的算术运算，因为`string`不喜欢计算。

# 创建分页链接

在查看分页产品时，通常最好的做法是有一个截断的页面数字列表，允许用户跳转到多个页面。我们已经有了在页面之间导航的机制 - 这可以扩展它。

作为一个简单的入口点，我们可以通过循环直到达到`totalPages`值来为每个页面创建一个链接。Vue 允许我们在没有任何 JavaScript 的情况下做到这一点。在组件底部创建一个带有列表的`nav`元素。使用`v-for`，并为`totalPages`变量中的每个项目创建一个`page`变量：

```js
<nav>
  <ol>
    <li v-for="page in pagination.totalPages">
      <button @click="toPage(page)">{{ page }}</button>
    </li>
  </ol>
</nav>
```

这将为每个页面创建一个按钮 - 例如，如果总共有 24 个页面，这将创建 24 个链接。这不是期望的效果，因为我们希望在当前页面之前和之后显示几个页面。例如，如果当前页面是 15，页面链接应该是 12、13、14、15、16、17 和 18。这意味着链接较少，对用户来说不那么压倒性。

首先，在`data`对象中创建一个新变量，用于记录选定页面两侧要显示的页面数量 - 一个好的起始值是三：

```js
data() {
  return {
    perPage: 12, 
    currentPage: 1,
    pageLinksCount: 3
  }
},
```

接下来，创建一个名为`pageLinks`的新计算函数。这个函数需要获取当前页面，并计算出比它小三个和比它大三个的页面数字。从那里，我们需要检查较低范围是否小于 1，较高范围是否大于总页数。在继续之前，检查产品数组是否有项目：

```js
pageLinks() {
  if(this.products.length) {
    let negativePoint = parseInt(this.currentPage) - this.pageLinksCount,
      positivePoint = parseInt(this.currentPage) + this.pageLinksCount;

    if(negativePoint < 1) {
      negativePoint = 1;
    }

    if(positivePoint > this.pagination.totalPages) {
      positivePoint = this.pagination.totalPages;
    }

    return pages;
  }
}
```

最后一步是创建一个数组和一个`for`循环，从较低范围循环到较高范围。这将创建一个包含最多七个数字的页面范围的数组：

```js
pageLinks() {
  if(this.products.length) {
    let negativePoint = parseInt(this.currentPage) - this.pageLinksCount,
      positivePoint = parseInt(this.currentPage) + this.pageLinksCount,
      pages = [];

    if(negativePoint < 1) {
      negativePoint = 1;
    }

    if(positivePoint > this.pagination.totalPages) {
      positivePoint = this.pagination.totalPages;
    }

    for (var i = negativePoint; i <= positivePoint; i++) {
 pages.push(i)
 }

 return pages;
  }
}
```

现在，我们可以用新的`pageLinks`变量替换导航组件中的`pagination.totalPages`变量，将创建正确数量的链接，如下所示：

```js
<nav>
  <ul>
    <li v-for="page in pageLinks">
      <button @click="toPage(page)">{{ page }}</button>
    </li>
  </ul>
</nav>
```

然而，在浏览器中查看时，会呈现一些奇怪的行为。虽然会生成正确数量的链接，但点击它们或使用下一个/上一个按钮将导致按钮保持不变 - 即使您导航到超出按钮范围的位置。这是因为计算值被缓存了。我们可以通过两种方式解决这个问题 - 要么将函数移动到`method`对象中，要么在路由上添加一个`watch`函数来监听并更新当前页面。

选择第二个选项意味着我们可以确保没有其他结果和输出被缓存并相应地更新。在组件中添加一个`watch`对象，并将`currentPage`变量更新为页面查询变量的值。确保它存在，否则默认为 1。`watch`方法如下所示：

```js
watch: {
  '$route'(to) {
    this.currentPage = parseInt(to.query.page) || 1;
  }
}
```

这样可以确保在导航到不同页面时，所有计算的变量都会更新。打开您的`HomePage`组件，并确保所有分页组件按预期工作并更新列表。

# 更新每页的项目

我们需要创建的最后一个用户界面添加是允许用户更新每页产品数量。为了最初设置这个，我们可以创建一个带有`v-model`属性的`<select>`框，直接更新值。这按预期工作，并相应地更新产品列表，如下所示：

```js
template: `<div v-if="products">
  <p>
    Page {{ currentPage }} out of {{ pagination.totalPages }}
  </p>

 Products per page: 
 <select v-model="perPage">
 <option>12</option>
 <option>24</option>
 <option>48</option>
 <option>60</option>
 </select>

  <button @click="toPage(currentPage - 1)" :disabled="currentPage == 1">Previous page</button>
  <button @click="toPage(currentPage + 1)" :disabled="currentPage == pagination.totalPages">Next page</button>

  <ol :start="pagination.range.from + 1">
    <li v-for="product in paginate(products)" v-if="product">
      <h3>{{ product.title }}</h3>
    </li>
  </ol>

  <nav>
    <ul>
      <li v-for="page in pageLinks">
        <button @click="toPage(page)">{{ page }}</button>
      </li>
    </ul>
  </nav>
</div>
```

这个问题是，如果用户在值改变后处于一个超过可能的页面。例如，如果有 30 个产品，每页 12 个产品，这将创建三个页面。如果用户导航到第三页，然后选择每页 24 个产品，只需要两个页面，第三页将为空。

这可以再次通过一个`watch`函数解决。当`perPage`变量更新时，我们可以检查当前页面是否高于`totalPages`变量。如果是，我们可以将其重定向到最后一页：

```js
watch: {
  '$route'(to) {
    this.currentPage = parseInt(to.query.page);
  },

  perPage() {
 if(this.currentPage > this.pagination.totalPages) {
 this.$router.push({
 query: Object.assign({}, this.$route.query, {
 page: this.pagination.totalPages
 })
 })
 }
 }
}
```

# 创建 ListProducts 组件

在继续创建过滤和排序之前，我们需要提取我们的产品列表逻辑并将其模板化到我们的组件中 - 以便我们可以轻松重用它。这个组件应该接受一个名为`products`的 prop，它应该能够列出和分页。

打开`ListProducts.js`文件，将代码从`HomePage.js`文件复制到组件中。将数据对象和复制`pagination`和`pageLinks`计算函数。将 watch 和 methods 对象以及`created()`函数从`HomePage`移动到`ListProducts`文件中。

更新`HomePage`模板，使用带有`products`属性的`<list-products>`组件，并传入`products`计算值。相比之下，`HomePage`组件现在应该更小：

```js
const HomePage = {
  name: 'HomePage',

  template: `<div>
    <list-products :products="products"></list-products>
  </div>`,

  computed: {
    products() {
      let products = this.$store.state.products;
      return Object.keys(products).map(key => products[key]);
    }
  }
};
```

在`ListProducts`组件中，我们需要添加一个 props 对象，让组件知道要期望什么。这个组件现在很重要。我们还需要添加一些东西到这个组件中，使其更加通用。它们包括：

+   如果有多个页面，则显示下一页/上一页链接

+   如果有超过 12 个产品，则显示“每页产品”组件，并且只在比前一步骤中有更多产品时显示每个步骤

+   只有当`pageLinks`组件超过我们的`pageLinksCount`变量时才显示

所有这些添加都已添加到以下组件代码中。我们还删除了不必要的`products`计算值：

```js
Vue.component('list-products', {
  template: `<div v-if="products">
    <p v-if="pagination.totalPages > 1">
      Page {{ currentPage }} out of {{ pagination.totalPages }}
    </p>

    <div v-if="pagination.totalProducts > 12">
      Products per page: 
      <select v-model="perPage">
        <option>12</option>
        <option>24</option>
        <option v-if="pagination.totalProducts > 24">48</option>
        <option v-if="pagination.totalProducts > 48">60</option>
      </select>
    </div>

    <button 
      @click="toPage(currentPage - 1)" 
      :disabled="currentPage == 1" 
      v-if="pagination.totalPages > 1"
    >
      Previous page
    </button>
    <button 
      @click="toPage(currentPage + 1)" 
      :disabled="currentPage == pagination.totalPages" 
      v-if="pagination.totalPages > 1"
    >
      Next page
    </button>

    <ol :start="pagination.range.from + 1">
      <li v-for="product in paginate(products)" v-if="product">
        <h3>{{ product.title }}</h3>
      </li>
    </ol>

    <nav v-if="pagination.totalPages > pageLinksCount">
      <ul>
        <li v-for="page in pageLinks">
          <button @click="toPage(page)">{{ page }}</button>
        </li>
      </ul>
    </nav>
  </div>`,

 props: {
 products: Array
 },

  data() {
    return {
      perPage: 12, 
      currentPage: 1,
      pageLinksCount: 3
    }
  },

  computed: {
    pagination() {
      if(this.products) {
        let totalProducts = this.products.length,
          pageFrom = (this.currentPage * this.perPage) - this.perPage,
          totalPages = Math.ceil(totalProducts / this.perPage);

        return {
          totalProducts: totalProducts,
          totalPages: Math.ceil(totalProducts / this.perPage),
          range: {
            from: pageFrom,
            to: pageFrom + this.perPage
          }
        }
      }
    },

    pageLinks() {
      if(this.products.length) {
        let negativePoint = this.currentPage - this.pageLinksCount,
          positivePoint = this.currentPage + this.pageLinksCount,
          pages = [];

        if(negativePoint < 1) {
          negativePoint = 1;
        }

        if(positivePoint > this.pagination.totalPages) {
          positivePoint = this.pagination.totalPages;
        }

        for (var i = negativePoint; i <= positivePoint; i++) {
          pages.push(i)
        }

        return pages;
      }
    }
  },

  watch: {
    '$route'(to) {
      this.currentPage = parseInt(to.query.page);
    },
    perPage() {
      if(this.currentPage > this.pagination.totalPages) {
        this.$router.push({
          query: Object.assign({}, this.$route.query, {
            page: this.pagination.totalPages
          })
        })
      }
    }
  },

  created() {
    if(this.$route.query.page) {
      this.currentPage = parseInt(this.$route.query.page);
    }
  },

  methods: {
    toPage(page) {
      this.$router.push({
        query: Object.assign({}, this.$route.query, {
          page
        })
      });

      this.currentPage = page;
    },

    paginate(list) {
      return list.slice(this.pagination.range.from, this.pagination.range.to)
    }
  }
});
```

您可以通过在`HomePage`模板中临时截断产品数组来验证您的条件渲染标签是否正常工作-完成后不要忘记删除它：

```js
products() {
  let products = this.$store.state.products;
  return Object.keys(products).map(key => products[key]).slice(1, 10);
}
```

# 为主页创建一个精选列表

有了我们的产品列表组件，我们可以继续为主页创建一个精选产品列表，并向产品列表添加更多信息。

在这个例子中，我们将在主页组件上硬编码一个产品句柄数组，我们希望显示这些产品。如果这是在开发中，你会期望这个列表通过内容管理系统或类似方式进行控制。

在`HomePage`组件上创建一个`data`函数，其中包含一个名为`selectedProducts`的数组：

```js
data() {
  return {
    selectedProducts: []
  }
},
```

用产品列表中的几个`handles`填充数组。尽量选择六个，但如果超过 12 个，请记住它会与我们的组件分页。将您选择的句柄添加到`selectedProducts`数组中：

```js
data() {
  return {
    selectedProducts: [
      'adjustable-stem',
 'colorful-fixie-lima',
 'fizik-saddle-pak',
 'kenda-tube',
 'oury-grip-set',
 'pure-fix-pedals-with-cages'
    ]
  }
},
```

使用我们选择的句柄，我们现在可以将产品列表过滤为仅包含在我们的`selectedProducts`数组中的产品列表。最初的直觉可能是在产品数组上结合使用 JavaScript 的`filter()`函数和`includes()`函数：

```js
products() {
  let products = this.$store.state.products;

  products = Object.keys(products).map(key => products[key]);
  products = products.filter(product => this.selectedProducts.includes(product.handle));

  return products;
}
```

问题在于，尽管它似乎工作正常，但它不遵守所选产品的顺序。过滤函数只是删除不匹配的项目，并按加载的顺序保留剩余的产品。

幸运的是，我们的产品以键/值对的形式保存，键是句柄。利用这一点，我们可以利用产品对象并使用`for`循环返回一个数组。

在计算函数中创建一个空数组`output`。遍历`selectedProducts`数组，找到每个所需的产品并添加到`output`数组中：

```js
products() {
  let products = this.$store.state.products,
    output = [];

  if(Object.keys(products).length) {
 for(let featured of this.selectedProducts) {
 output.push(products[featured]);
 }
 return output;
 }
}
```

这将创建相同的产品列表，但这次是按正确的顺序。尝试重新排序、添加和删除项目，以确保您的列表能够相应地做出反应。

# 显示更多信息

现在我们可以在`ListProduct`组件中显示更多的产品信息了。正如在本章开头提到的，我们应该显示：

+   图像

+   标题

+   价格

+   制造商

我们已经显示了标题，并且可以轻松地从产品信息中提取出图像和制造商。不要忘记始终从`images`数组中检索第一个图像。打开`ListProducts.js`文件并更新产品以显示这些信息 - 确保在显示之前检查图像是否存在。制造商标题在产品数据的`vendor`对象下列出：

```js
<ol :start="pagination.range.from + 1">
  <li v-for="product in paginate(products)" v-if="product">
    <img v-if="product.images[0]" :src="product.images[0].source" :alt="product.title" width="120">
    <h3>{{ product.title }}</h3>
    <p>Made by: {{ product.vendor.title }}</p>
  </li>
</ol>
```

价格会更复杂一些。这是因为产品的每个变体可能有不同的价格，但通常是相同的。如果有不同的价格，我们应该显示最便宜的价格，并在前面加上*from*。

我们需要创建一个循环遍历变体并计算出最低价格的函数，如果有价格范围，还要添加*from*这个词。为了实现这个目标，我们将遍历变体并建立一个唯一价格的数组 - 如果价格在数组中不存在的话。完成后，我们可以检查数组的长度 - 如果有多个价格，我们可以添加前缀，如果没有，这意味着所有的变体都是相同的价格。

在`ListProducts`组件上创建一个名为`productPrice`的新方法。这个方法接受一个参数，即变体。在方法内部，创建一个空数组`prices`：

```js
productPrice(variations) {
  let prices = [];
}
```

遍历变体，如果价格在`prices`数组中不存在，则将价格添加到`prices`数组中。创建一个使用`includes()`函数检查价格是否存在于数组中的`for`循环：

```js
productPrice(variations) {
  let prices = [];

  for(let variation of variations) {
 if(!prices.includes(variation.price)) {
 prices.push(variation.price);
 }
 }
}
```

有了价格数组，我们现在可以提取最低的数字并检查是否有多个项目。

要从数组中提取最小的数字，我们可以使用 JavaScript 的`Math.min()`函数。使用`.length`属性来检查数组的长度。最后，返回`price`变量：

```js
productPrice(variations) {
  let prices = [];

  for(let variation of variations) {
    if(!prices.includes(variation.price)) {
      prices.push(variation.price);
    }
  }

 let price = '$' + Math.min(...prices);

 if(prices.length > 1) {
 price = 'From: ' + price;
 }

  return price;
}
```

将您的`productPrice`方法添加到模板中，记得将`product.variationProducts`传递给它。我们需要在模板中添加的最后一件事是一个指向产品的链接：

```js
<ol :start="pagination.range.from + 1">
  <li v-for="product in paginate(products)" v-if="product">
    <router-link :to="'/product/' + product.handle">
      <img v-if="product.images[0]" :src="product.images[0].source" :alt="product.title" width="120">
    </router-link> 
    <h3>
      <router-link :to="'/product/' + product.handle">
        {{ product.title }}
      </router-link>
    </h3>

    <p>Made by: {{ product.vendor.title }}</p>
    <p>Price {{ productPrice(product.variationProducts) }}</p>
  </li>
</ol>
```

理想情况下，产品链接应该使用命名路由而不是硬编码的链接，以防路由发生变化。给产品路由添加一个名称，并更新`to`属性以使用该名称。

```js
{
  path: '/product/:slug',
  name: 'Product',
  component: ProductPage
}
```

更新模板，现在使用路由名称和`params`对象：

```js
<ol :start="pagination.range.from + 1">
  <li v-for="product in paginate(products)" v-if="product">
    <router-link :to="{name: 'Product', params: {slug: product.handle}}">
      <img v-if="product.images[0]" :src="product.images[0].source" :alt="product.title" width="120">
    </router-link>
    <h3>
      <router-link :to="{name: 'Product', params: {slug: product.handle}}">
        {{ product.title }}
      </router-link>
    </h3>
    <p>Made by: {{ product.vendor.title }}</p>
    <p>Price {{ productPrice(product.variationProducts) }}</p>
  </li>
</ol>
```

# 创建类别

如果商店没有可导航的类别，那么它实际上不是一个可用的商店。幸运的是，我们的每个产品都有一个`type`键，指示它所属的类别。现在我们可以创建一个类别页面，列出该特定类别的产品。

# 创建类别列表

在我们能够显示特定类别的产品之前，我们首先需要生成一个可用类别的列表。为了提高应用程序的性能，我们还将在每个类别中存储产品的句柄。类别结构将如下所示：

```js
categories = {
  tools: {
    name: 'Tools',
    handle: 'tools',
    products: ['product-handle', 'product-handle'...]
  },
  freewheels: {
    name: 'Freewheels',
    handle: 'freewheels',
    products: ['another-product-handle', 'product'...]
  }
};
```

创建这样的类别列表意味着我们可以轻松地获取类别内的产品列表，同时可以循环遍历类别并输出`title`和`handle`以创建一个链接到类别的列表。由于我们已经有了这些信息，所以我们将在获取产品列表后创建类别列表。

打开`app.js`并导航到`Vue`实例上的`created()`方法。我们将不再在`products`存储方法下面创建第二个`$store.commit`，而是将使用 Vuex 的另一个功能-`actions`。

Actions 允许您在存储库本身中创建函数。Actions 无法直接改变状态-这仍然是 mutations 的工作，但它允许您将多个 mutations 组合在一起，这在这种情况下非常适合我们。如果您想在改变状态之前运行异步操作，例如使用`setTimeout` JavaScript 函数，那么 Actions 也非常适合。

导航到您的`Vuex.Store`实例，并在 mutations 之后添加一个新的`actions`对象。在其中，创建一个名为`initializeShop`的新函数：

```js
const store = new Vuex.Store({
  state: {
    products: {}
  },

  mutations: {
    products(state, payload) {
      state.products = payload;
    }
  },

 actions: {
 initializeShop() {

 }
 }
});
```

在动作参数中，第一个参数是存储本身，我们需要使用它来利用突变。有两种方法可以做到这一点，第一种是使用一个变量并在函数内部访问其属性。例如：

```js
actions: {
  initializeShop(store) {
    store.commit('products');
  }
}
```

然而，使用 ES2015，我们可以使用参数解构并利用我们需要的属性。对于这个动作，我们只需要`commit`函数，就像这样：

```js
actions: {
  initializeShop({commit}) {
    commit('products');
  }
}
```

如果我们还想要存储中的状态，我们可以将其添加到花括号中：

```js
actions: {
  initializeShop({state, commit}) {
    commit('products');
    // state.products
  }
}
```

使用这种“分解”的方法访问属性使我们的代码更清晰，减少了重复。删除`state`属性，并在花括号后面添加一个标记为`products`的第二个参数。这将是我们格式化的产品数据。将该变量直接传递给产品的`commit`函数：

```js
initializeShop({commit}, products) {
  commit('products', products);
}
```

使用动作与使用`mutations`一样简单，只是不使用`$store.commit`，而是使用`$store.dispatch`。更新您的`created`方法-不要忘记更改函数名称，并检查您的应用程序是否仍然正常工作：

```js
created() {
  CSV.fetch({url: './data/csv-files/bicycles.csv'}).then(data => {
    this.$store.dispatch('initializeShop', this.$formatProducts(data));
  });
}
```

下一步是为我们的类别创建一个突变。由于我们可能希望独立于我们的产品更新我们的类别-我们应该在`mutations`中创建第二个函数。它也应该是这个函数遍历产品并创建类别列表。

首先，在状态对象中创建一个名为`categories`的新属性。这应该是一个默认的对象：

```js
state: {
  products: {},
  categories: {}
}
```

接下来，创建一个名为`categories`的新突变。除了状态之外，这应该有一个第二个参数。为了保持一致，将其命名为`payload`-因为这是 Vuex 所指的：

```js
mutations: {
  products(state, payload) {
    state.products = payload;
  },

 categories(state, payload) {

 }
},
```

现在是功能的时间。这个突变需要遍历产品。对于每个产品，它需要隔离`type`。一旦它有了标题和 slug，它就需要检查是否存在具有该 slug 的条目；如果存在，则将产品句柄附加到`products`数组中，如果不存在-它需要创建一个新数组和详细信息。

创建一个空的`categories`对象，并循环遍历`payload`，为产品和类型设置一个变量：

```js
categories(state, payload) {
 let categories = {}; 
 Object.keys(payload).forEach(key => {
 let product = payload[key],
 type = product.type;
 });
}
```

现在，我们需要检查是否存在具有当前`type.handle`键的条目。如果不存在，我们需要创建一个新条目。该条目需要具有标题、句柄和一个空的产品数组：

```js
categories(state, payload) {
  let categories = {};

  Object.keys(payload).forEach(key => {
    let product = payload[key],
      type = product.type;

 if(!categories.hasOwnProperty(type.handle)) {
 categories[type.handle] = {
 title: type.title,
 handle: type.handle,
 products: []
 }
 }
  });
}
```

最后，我们需要将当前产品句柄附加到条目的产品数组中：

```js
categories(state, payload) {
  let categories = {};

  Object.keys(payload).forEach(key => {
    let product = payload[key],
      type = product.type;

    if(!categories.hasOwnProperty(type.handle)) {
      categories[type.handle] = {
        title: type.title,
        handle: type.handle,
        products: []
      }
    }

    categories[type.handle].products.push(product.handle);
  });
}
```

您可以通过在函数末尾添加`console.log`来查看`categories`输出：

```js

categories(state, payload) {
  let categories = {};

  Object.keys(payload).forEach(key => {
    ...
  });

  console.log(categories);
}
```

将突变添加到`initializeShop`动作中：

```js
initializeShop({commit}, products) {
  commit('products', products);
  commit('categories', products);
}
```

在浏览器中查看应用程序时，您将面临一个 JavaScript 错误。这是因为一些产品没有包含我们用来对其进行分类的“类型”。即使解决了 JavaScript 错误，仍然有很多类别被列出。

为了帮助处理类别的数量，并将未分类的产品分组，我们应该创建一个“杂项”类别。这将汇总所有包含两个或更少产品的类别，并将产品分组到它们自己的组中。

# 创建一个“杂项”类别

我们需要解决的第一个问题是无名称的类别。在循环遍历产品时，如果找不到类型，则应插入一个类别，以便对所有内容进行分类。

在`categories`方法中创建一个新对象，其中包含一个新类别的标题和句柄。对于句柄和变量，称其为“other”。通过将标题称为“杂项”来使其更加用户友好。

```js
let categories = {},
  other = {
 title: 'Miscellaneous',
 handle: 'other'
 };
```

在循环遍历产品时，我们可以检查`type`键是否存在，如果不存在，则创建一个`other`类别并将其附加到其中：

```js
Object.keys(payload).forEach(key => {
  let product = payload[key],
    type = product.hasOwnProperty('type') ? product.type : other;

  if(!categories.hasOwnProperty(type.handle)) {
    categories[type.handle] = {
      title: type.title,
      handle: type.handle,
      products: []
    }
  }

  categories[type.handle].products.push(product.handle);
});
```

现在查看应用程序将在 JavaScript 控制台中显示所有类别-让您看到有多少类别。

让我们将任何包含两个或更少产品的类别合并到“other”类别中-不要忘记之后删除该类别。在产品循环之后，循环遍历类别，检查可用产品的数量。如果少于三个，则将它们添加到“other”类别中：

```js
Object.keys(categories).forEach(key => {
  let category = categories[key];

  if(category.products.length < 3) {
    categories.other.products = categories.other.products.concat(category.products);
  }
});
```

然后，我们可以删除刚刚从中窃取产品的类别：

```js
Object.keys(categories).forEach(key => {
  let category = categories[key];

  if(category.products.length < 3) {
    categories.other.products = categories.other.products.concat(category.products);
    delete categories[key];
  }
});
```

有了这个，我们有了一个更易于管理的类别列表。我们可以做的另一个改进是确保类别按字母顺序排列。这有助于用户更快地找到他们想要的类别。在 JavaScript 中，数组比对象更容易排序，因此我们需要再次循环遍历对象键的数组并对其进行排序。创建一个新对象，并将排序后的类别添加到其中。然后，将其存储在`state`对象上，以便我们可以使用这些类别：

```js
categories(state, payload) {
  let categories = {},
    other = {
      title: 'Miscellaneous',
      handle: 'other'
    };

  Object.keys(payload).forEach(key => {
    let product = payload[key],
      type = product.hasOwnProperty('type') ? product.type : other;

    if(!categories.hasOwnProperty(type.handle)) {
      categories[type.handle] = {
        title: type.title,
        handle: type.handle,
        products: []
      }
    }

    categories[type.handle].products.push(product.handle);
  });

  Object.keys(categories).forEach(key => {
    let category = categories[key];

    if(category.products.length < 3) {
      categories.other.products =      categories.other.products.concat(category.products);
      delete categories[key];
    }
  });

  let categoriesSorted = {}
 Object.keys(categories).sort().forEach(key => {
 categoriesSorted[key] = categories[key]
 });
 state.categories = categoriesSorted;
}
```

有了这个，我们现在可以在`HomePage`模板中添加一个类别列表。为此，我们将创建命名的`router-view`组件-允许我们将东西放在选定页面的商店侧边栏中。

# 显示类别

有了我们存储的类别，我们现在可以继续创建我们的`ListCategories`组件。我们希望在主页的侧边栏和商店类别页面中显示我们的类别导航。由于我们希望在多个位置显示它，我们有几个选项可以选择如何显示它。

我们可以像使用`<list-products>`组件一样在模板中使用该组件。但是，问题在于如果我们想在侧边栏中显示我们的列表，并且我们的侧边栏需要在整个站点上保持一致，我们将不得不在视图之间复制和粘贴大量的 HTML。

更好的方法是使用命名路由，并在我们的`index.html`中设置模板。

更新应用程序模板以包含一个`<main>`和一个`<aside>`元素。在其中创建一个`router-view`，将`main`内部的一个未命名，而将`aside`元素内部的一个命名为`sidebar`：

```js
<div id="app">
  <main>
    <router-view></router-view>
  </main>
 <aside>
 <router-view name="sidebar"></router-view>
 </aside>
</div>
```

在我们的路由对象中，我们现在可以为不同的命名视图添加不同的组件。在`Home`路由上，将`component`键更改为`components`，并添加一个对象-指定每个组件及其视图：

```js
{
  path: '/',
  name: 'Home',
  components: {
 default: HomePage,
 sidebar: ListCategories
 }
}
```

默认情况下，组件将进入未命名的`router-view`中。这使我们仍然可以使用单数的`component`键（如果需要的话）。为了正确加载组件到侧边栏视图中，我们需要修改`ListCategories`组件的初始化方式。不要使用`Vue.component`，而是像初始化`view`组件一样进行初始化：

```js
const ListCategories = {
  name: 'ListCategories'

};
```

现在我们可以继续创建类别列表的模板了。由于我们的类别保存在 store 中，加载和显示它们应该是熟悉的。建议您将类别从状态加载到计算函数中-这样可以使模板代码更清晰，并且在需要以任何方式操作它时更容易适应。

在创建模板之前，我们需要为该类别创建一个路由。回顾一下第九章中的计划，*使用 Vue-Router 动态路由加载数据*，我们可以看到路由将是`/category/:slug` - 添加此路由并启用 props，因为我们将在`slug`中使用它们。确保您已经创建了`CategoryPage`文件并初始化了组件。

```js
const router = new VueRouter({
  routes: [
    {
      path: '/',
      name: 'Home',
      components: {
        default: HomePage,
        sidebar: ListCategories
      }
    },
    {
 path: '/category/:slug',
 name: 'Category',
 component: CategoryPage,
 props: true
 },
    {
      path: '/product/:slug',
      name: 'Product',
      component: ProductPage
    },

    {
      path: '/404', 
      alias: '*',
      component: PageNotFound
    }
  ]
});
```

回到我们的`ListCategories`组件；循环遍历存储的类别，并为每个类别创建一个链接。在每个名称后面用括号显示产品数量：

```js
const ListCategories = {
  name: 'ListCategories',

 template: `<div v-if="categories">
 <ul>
 <li v-for="category in categories">
 <router-link :to="{name: 'Category', params: {slug: category.handle}}">
 {{ category.title }} ({{ category.products.length }})
 </router-link>
 </li>
 </ul>
 </div>`,

 computed: {
 categories() {
 return this.$store.state.categories;
 }
 } 
};
```

现在我们的类别链接已经显示在主页上，我们可以继续创建一个类别页面。

# 显示类别中的产品

点击其中一个类别链接（即`/#/category/grips`）将导航到一个空白页面 - 这要归功于我们的路由。我们需要创建一个模板并设置类别页面以显示产品。作为起点，创建一个类似于产品页面的`CategoryPage`组件。

创建一个带有空容器和`PageNotFound`组件的模板。创建一个名为`categoryNotFound`的数据变量，并确保如果它设置为`true`，则显示`PageNotFound`组件。创建一个`props`对象，允许传递`slug`属性，并最后创建一个`category`计算函数。

`CategoryPage`组件应该如下所示：

```js
const CategoryPage = {
  name: 'CategoryPage',

  template: `<div>
    <div v-if="category"></div>
    <page-not-found v-if="categoryNotFound"></page-not-found>
  </div>`,

  components: {
    PageNotFound
  },

  props: {
    slug: String
  },

  data() {
    return {
      categoryNotFound: false,
    }
  },

  computed: {
    category() {
    }
  }
};
```

在`category`计算函数内，根据 slug 从存储中加载正确的类别。如果它不在列表中，则将`categoryNotFound`变量标记为 true - 类似于我们在`ProductPage`组件中所做的操作：

```js
computed: {
  category() {
    let category;

 if(Object.keys(this.$store.state.categories).length) {

 category = this.$store.state.categories[this.slug];

 if(!category) {
 this.categoryNotFound = true;
 }
 }

 return category;
  }
}
```

在加载了我们的类别后，我们可以在模板中输出标题：

```js
template: `<div>
  <div v-if="category">
    <h1>{{ category.title }}</h1>
  </div>
  <page-not-found v-if="categoryNotFound"></page-not-found>
</div>`,
```

现在，我们可以继续在类别页面上显示产品。为此，我们可以使用`HomePage`组件中的代码，因为我们有完全相同的情况 - 一个产品句柄数组。

创建一个新的`computed`函数，它接受当前类别产品并像在主页上那样对它们进行处理：

```js
computed: {
  category() {
    ...
  },

  products() {
    if(this.category) {
 let products = this.$store.state.products,
 output = [];

 for(let featured of this.category.products) {
 output.push(products[featured]);
 }

 return output; 
 }
  }
}
```

在这个函数中，我们不需要检查产品是否存在，因为我们正在检查类别是否存在，只有在数据加载完成时才会返回 true。将组件添加到 HTML 中并传入`products`变量：

```js
template: `<div>
  <div v-if="category">
    <h1>{{ category.title }}</h1>
    <list-products :products="products"></list-products>
  </div>
  <page-not-found v-if="categoryNotFound"></page-not-found>
</div>`
```

有了这个，我们就可以为每个类别列出我们的类别产品。

# 代码优化

完成`CategoryPage`组件后，我们可以看到它与主页之间有很多相似之处 - 唯一的区别是主页有一个固定的产品数组。为了避免重复，我们可以将这两个组件合并 - 这意味着如果需要，我们只需要更新一个组件。

我们可以通过在识别我们是否在主页时显示它来解决固定数组问题。做法是检查 slug 属性是否有值。如果没有，我们可以假设我们在主页上。

首先，将`Home`路由更新为指向`CategoryPage`组件并启用 props。在使用命名视图时，您必须为每个视图启用 props。更新 props 值为一个对象，其中包含每个命名视图，为每个视图启用 props：

```js
{
  path: '/',
  name: 'Home',
  components: {
    default: CategoryPage,
    sidebar: ListCategories
  },
  props: {
 default: true, 
 sidebar: true
 }
}
```

接下来，在`CategoryPage`的`data`函数中创建一个名为`categoryHome`的新变量。这将是一个对象，其结构与类别对象相同，包含一个`products`数组，标题和句柄。虽然句柄不会被使用，但遵循惯例是一个好习惯：

```js
data() {
  return {
    categoryNotFound: false,
    categoryHome: {
 title: 'Welcome to the Shop',
 handle: 'home',
 products: [
 'adjustable-stem',
 'fizik-saddle-pak',
 'kenda-tube',
 'colorful-fixie-lima',
 'oury-grip-set',
 'pure-fix-pedals-with-cages'
 ]
 }
  }
}
```

我们需要做的最后一件事是检查 slug 是否存在。如果不存在，则将我们的新对象分配给计算函数中的类别变量：

```js
category() {
  let category;

  if(Object.keys(this.$store.state.categories).length) {
    if(this.slug) {
 category = this.$store.state.categories[this.slug];
 } else {
 category = this.categoryHome;
 }

    if(!category) {
      this.categoryNotFound = true;
    }
  }

  return category;
}
```

转到主页并验证您的新组件是否正常工作。如果是，请删除`HomePage.js`并从`index.html`中删除它。更新类别路由以在侧边栏中包含类别列表并使用`props`对象：

```js
{
  path: '/category/:slug',
  name: 'Category',
  components: {
 default: CategoryPage,
 sidebar: ListCategories
 },
  props: {
 default: true, 
 sidebar: true
 }
},
```

# 对类别中的产品进行排序

当我们的类别页面显示正确的产品时，现在是时候在`ListProducts`组件中添加一些排序选项了。在在线商店中查看产品时，通常可以按以下方式对产品进行排序：

+   标题：升序（A - Z）

+   标题：降序（Z - A）

+   价格：升序（$1 - $999）

+   价格：降序（$999 - $1）

然而，一旦我们有了这个机制，您可以添加任何您想要的排序标准。

首先，在`ListProducts`组件中创建一个选择框，其中包含上述每个值。添加一个额外的第一个值为“按产品排序...”：

```js
<div class="ordering">
  <select>
    <option>Order products</option>
    <option>Title - ascending (A - Z)</option>
    <option>Title - descending (Z - A)</option>
    <option>Price - ascending ($1 - $999)</option>
    <option>Price - descending ($999 - $1)</option>
  </select>
</div>
```

现在我们需要在`data`函数中为选择框创建一个变量以便更新。添加一个名为`ordering`的新键，并为每个选项添加一个值，以便更容易解释该值。通过使用字段和顺序，用连字符分隔来构造值。例如，`Title - ascending (`A - Z`)`将变为`title-asc`：

```js
<div class="ordering">
  <select v-model="ordering">
    <option value="">Order products</option>
    <option value="title-asc">Title - ascending (A - Z)</option>
    <option value="title-desc">Title - descending (Z - A)</option>
    <option value="price-asc">Price - ascending ($1 - $999)</option>
    <option value="price-desc">Price - descending ($999 - $1)</option>
  </select>
</div>
```

更新后的`data`函数如下：

```js
data() {
  return {
    perPage: 12, 
    currentPage: 1,
    pageLinksCount: 3,

    ordering: ''
  }
}
```

要更新产品的顺序，我们现在需要操作产品列表。这需要在列表被分割为分页之前完成 - 因为用户期望整个列表被排序，而不仅仅是当前页面。

# 存储产品价格

在继续之前，我们需要解决一个问题。要按价格排序，最好将价格作为产品本身的属性而不是专门为模板计算的属性。为了解决这个问题，我们将在将产品添加到存储之前计算价格。这意味着它将作为产品本身的属性可用，而不是动态创建的属性。

我们需要知道的细节是最便宜的价格以及产品是否有多个价格。后者意味着我们知道在列出产品时是否需要显示`"From:"`。我们将为每个产品创建两个新属性：`price`和`hasManyPrices`。

转到存储中的`products`mutation，并创建一个新对象和产品的循环：

```js
products(state, payload) {
 let products = {};

 Object.keys(payload).forEach(key => {
 let product = payload[key];

 products[key] = product;
 });

  state.products = payload;
}
```

从`ListProducts`组件的`productPrice`方法中复制代码，并将其放置在循环内。更新第二个`for`循环，使其循环遍历`product.variationProducts`。完成此`for`循环后，我们可以向产品添加新属性。最后，使用新的产品对象更新状态：

```js
products(state, payload) {
  let products = {};

  Object.keys(payload).forEach(key => {
    let product = payload[key];

    let prices = [];
 for(let variation of product.variationProducts) {
 if(!prices.includes(variation.price)) {
 prices.push(variation.price);
 }
 }

 product.price = Math.min(...prices);
 product.hasManyPrices = prices.length > 1;

    products[key] = product;
  });

  state.products = products;
}
```

现在我们可以更新`ListProducts`组件上的`productPrice`方法。更新函数，使其接受产品而不是变体。从函数中删除`for`循环，并更新变量，使其使用产品的`price`和`hasManyPrices`属性：

```js
productPrice(product) {
  let price = '$' + product.price;

  if(product.hasManyPrices) {
    price = 'From: ' + price;
  }

  return price;
}
```

更新模板，以便将产品传递给函数：

```js
<p>Price {{ productPrice(product) }}</p>
```

# 连接排序

有了我们需要的价格，我们可以继续连接排序。创建一个名为`orderProducts`的新的`computed`函数，它返回`this.products`。我们希望确保我们始终从源头进行排序，而不是对之前已经排序过的东西进行排序。从`paginate`函数中调用这个新函数，并从该方法和模板中删除参数：

```js
computed: {
 ...

  orderProducts() {
 return this.products;
 }, },

methods: {
  paginate() {
    return this.orderProducts.slice(
      this.pagination.range.from,  
      this.pagination.range.to
    );
  },
}
```

为了确定我们需要如何对产品进行排序，我们可以使用`this.ordering`的值。如果存在，我们可以在连字符上拆分字符串，这意味着我们有一个包含字段和排序类型的数组。如果不存在，我们只需返回现有的产品数组：

```js
orderProducts() {
  let output;

 if(this.ordering.length) {
 let orders = this.ordering.split('-');
 } else {
 output = this.products;
 }
 return output;
}
```

根据排序数组的第一个项目的值对`products`数组进行排序。如果它是一个字符串，我们将使用`localCompare`进行比较，它在比较时忽略大小写。否则，我们将简单地从另一个值中减去一个值 - 这是`sort`函数所期望的：

```js
orderProducts() {
  let output;

  if(this.ordering.length) {
    let orders = this.ordering.split('-');

    output = this.products.sort(function(a, b) {
 if(typeof a[orders[0]] == 'string') {
 return a[orders[0]].localeCompare(b[orders[0]]);
 } else {
 return a[orders[0]] - b[orders[0]];
 }
 });

  } else {
    output = this.products;
  }
  return output;
}
```

最后，我们需要检查`orders`数组中的第二个项目是`asc`还是`desc`。默认情况下，当前排序函数将以“升序”方式返回排序后的项目，因此如果值为`desc`，我们可以反转数组：

```js
orderProducts() {
  let output;

  if(this.ordering.length) {
    let orders = this.ordering.split('-');

    output = this.products.sort(function(a, b) {
      if(typeof a[orders[0]] == 'string') {
        return a[orders[0]].localeCompare(b[orders[0]]);
      } else {
        return a[orders[0]] - b[orders[0]];
      }
    });

 if(orders[1] == 'desc') {
 output.reverse();
 }
  } else {
    output = this.products;
  }
  return output;
}
```

前往浏览器，查看产品的排序！

# 创建 Vuex 的 getter

使我们的类别页面与其他任何商店一样的最后一步是引入过滤。过滤允许您查找具有特定尺寸、颜色、标签或制造商的产品。我们的过滤选项将从页面上的产品构建。例如，如果没有产品具有 XL 尺寸或蓝色，那么将其显示为过滤器就没有意义。

为了实现这一点，我们还需要将当前类别的产品传递给过滤组件。然而，产品在`CategoryPage`组件上进行处理。我们可以将这个功能移动到 Vuex 存储的 getter 中，而不是重复这个处理过程。Getter 允许您从存储中检索数据并像在组件的函数中一样操作它。然而，因为它是一个中心位置，这意味着多个组件可以从处理中受益。

Getter 是 Vuex 中计算函数的等效物。它们被声明为函数，但作为变量调用。然而，它们可以通过返回一个函数来进行参数化处理。

我们将把`CategoryPage`组件中的`category`和`products`函数都移动到 getter 中。然后，`getter`函数将返回一个包含类别和产品的对象。

在您的存储中创建一个名为`getters`的新对象。在其中创建一个名为`categoryProducts`的新函数：

```js
getters: {
  categoryProducts: () => {

  }
}
```

Getter 本身接收两个参数，第一个是状态，第二个是任何其他 getter。要将参数传递给 getter，必须在 getter 内部返回一个接收参数的函数。幸运的是，在 ES2015 中，可以使用双箭头（`=>`）语法实现这一点。由于在此函数中不会使用任何其他 getter，因此我们不需要调用第二个参数。

由于我们正在将所有逻辑抽象出来，所以将`slug`变量作为第二个函数的参数传入：

```js
categoryProducts: (state) => (slug) => {

}
```

由于我们正在将选择和检索类别和产品的逻辑转移到存储中，所以将`HomePage`类别内容存储在`state`本身是有意义的。

```js
state: {
  products: {},
  categories: {},

  categoryHome: {
 title: 'Welcome to the Shop',
 handle: 'home',
 products: [
 'adjustable-stem',
 'fizik-saddle-pak',
 'kenda-tube',
 'colorful-fixie-lima',
 'oury-grip-set',
 'pure-fix-pedals-with-cages'
 ]
 }
}
```

将`CategoryPage`组件中的`category`计算函数中的类别选择逻辑移动到 getter 中。更新`slug`和`categoryHome`变量，使用相关位置的内容：

```js
categoryProducts: (state) => (slug) => {
  if(Object.keys(state.categories).length) {
    let category = false;

    if(slug) {
      category = this.$store.state.categories[this.slug];
    } else {
      category = state.categoryHome;
    }
  }
}
```

现在，我们可以根据类别分配加载基于类别中存储的句柄的产品。将代码从`products`计算函数移动到 getter 中。将变量赋值合并在一起，并删除存储产品检索变量，因为我们已经有了可用的状态。确保检查类别是否存在的代码仍然存在：

```js
categoryProducts: (state) => (slug) => {
  if(Object.keys(state.categories).length) {
    let category = false,
      products = [];

    if(slug) {
      category = this.$store.state.categories[this.slug];
    } else {
      category = state.categoryHome;
    }

    if(category) {
 for(let featured of category.products) {
 products.push(state.products[featured]);
 }
 }
  }
}
```

最后，在`category`上添加一个新的`productDetails`数组，其中包含详细的产品数据。在函数的末尾返回`category`。如果`slug`变量输入存在作为一个类别，我们将得到所有的数据。如果没有，它将返回`false` - 我们可以显示我们的`PageNotFound`组件：

```js
categoryProducts: (state) => (slug) => {
  if(Object.keys(state.categories).length) {
    let category = false,
      products = [];

    if(slug) {
      category = state.categories[slug];
    } else {
      category = state.categoryHome;
    }

    if(category) {
      for(let featured of category.products) {
        products.push(state.products[featured]);
      }

      category.productDetails = products;
    }

    return category;
  }
}
```

在我们的`CategoryPage`组件中，我们可以删除`products()`计算函数并更新`category()`函数。要调用一个`getter`函数，可以引用`this.$store.getters`：

```js
computed: {
  category() {
    if(Object.keys(this.$store.state.categories).length) {
      let category = this.$store.getters.categoryProducts(this.slug);

      if(!category) {
        this.categoryNotFound = true;
      }
      return category;
    }
  }
}
```

不幸的是，我们仍然需要在继续之前检查类别是否存在。这样我们就可以知道是否有一个名为的类别，而不是一个未加载的类别。

为了使这个更整洁，我们可以将这个检查提取到另一个 getter 中，并在我们的其他 getter 和组件中使用它。

创建一个名为`categoriesExist`的新 getter，并返回`if`语句的内容。

```js
categoriesExist: (state) => {
  return Object.keys(state.categories).length;
},
```

更新`categoryProducts`getter 以接受第一个函数的 getter 作为参数，并使用这个新的 getter 来指示其输出：

```js
categoryProducts: (state, getters) => (slug) => {
  if(getters.categoriesExist) {
    ...
  }
}
```

在我们的`CategoryPage`组件中，我们现在可以使用`this.$store.getters.categoriesExist()`调用新的 getter。为了避免在这个函数中重复使用`this.$store.getters`两次，我们可以将 getter 映射为本地访问。这允许我们调用`this.categoriesExist()`作为一个更可读的函数名。

在`computed`对象的开头，添加一个名为`...Vuex.mapGetters()`的新函数。这个函数接受一个数组或一个对象作为参数，而开头的三个点确保内容被展开以与`computed`对象合并。

传入一个包含两个 getter 名称的数组：

```js
computed: {
 ...Vuex.mapGetters([
 'categoryProducts',
 'categoriesExist'
 ]),

  category() {
    ...
  }
}
```

这意味着我们现在可以使用`this.categoriesExist`和`this.categoryProducts`。更新类别函数以使用这些新函数：

```js
computed: {
  ...Vuex.mapGetters([
    'categoriesExist',
    'categoryProducts'
  ]),

  category() {
    if(this.categoriesExist) {
      let category = this.categoryProducts(this.slug);

      if(!category) {
        this.categoryNotFound = true;
      }
      return category;
    }
  }
}
```

更新模板以反映计算数据的更改：

```js
template: `<div>
  <div v-if="category">
    <h1>{{ category.title }}</h1>
    <list-products :products="category.productDetails"></list-products>
  </div>
  <page-not-found v-if="categoryNotFound"></page-not-found>
</div>`,
```

# 基于产品构建过滤组件

如前所述，我们所有的过滤器都将根据当前类别中的产品创建。这意味着如果没有*IceToolz*制造的产品，它将不会显示为可用的过滤器。

首先打开`ProductFiltering.js`组件文件。我们的产品过滤将放在侧边栏中，所以将组件定义从`Vue.component`更改为一个对象。我们仍然希望在过滤之后显示我们的类别，所以将`ListCategories`组件添加为`ProductFiltering`中的一个声明组件。添加一个模板键并包含`<list-categories>`组件：

```js
const ProductFiltering = {
  name: 'ProductFiltering',

  template: `<div>
    <list-categories />
  </div>`,

  components: {
    ListCategories
  }
}
```

更新类别路由，将`ProductFiltering`组件放在侧边栏中，而不是`ListCategories`：

```js
{
  path: '/category/:slug',
  name: 'Category',
  components: {
    default: CategoryPage,
    sidebar: ProductFiltering
  },
  props: {
    default: true, 
    sidebar: true
  }
}
```

现在，您应该有`Home`路由，其中包括`CategoryPage`和`ListCategories`组件，以及`Category`路由，其中包括`ProductFiltering`组件。

从`CategoryPage`组件中复制 props 和 computed 对象-因为我们将使用大量现有代码。将`category`计算函数重命名为`filters`。删除返回语句和`componentNotFound`的 if 语句。您的组件现在应该如下所示：

```js
const ProductFiltering = {
  name: 'ProductFiltering',

  template: `<div>
    <list-categories />
  </div>`,

  components: {
    ListCategories
  },

  props: {
 slug: String
 },

 computed: {
 ...Vuex.mapGetters([
 'categoriesExist',
 'categoryProducts'
 ]),
 filters() {
 if(this.categoriesExist) {
 let category = this.categoryProducts(this.slug);

 }
 }
 }
}
```

现在，我们需要根据类别中的产品构建我们的过滤器。我们将通过循环遍历产品，收集来自预选值的信息并显示它们来完成这个过程。

创建一个包含`topics`键的`data`对象。这将是一个包含子对象的对象，每个子对象都有一个现在熟悉的模式`'handle'：{}`，用于我们要过滤的每个属性。

每个子对象将包含一个`handle`，它是要过滤的产品的值（例如，供应商），一个`title`，它是键的用户友好版本，以及一个将被填充的值数组。

我们将从两个开始，`vendor`和`tags`；然而，随着我们处理产品的过程，会动态添加更多：

```js
data() {
  return {
    topics: {
      vendor: {
        title: 'Manufacturer',
        handle: 'vendor',
        values: {}
      },
      tags: {
        title: 'Tags',
        handle: 'tags',
        values: {}
      }
    }
  }
},
```

现在，我们将开始循环遍历产品。除了值之外，我们还将跟踪具有相同值的产品数量，以便向用户指示将显示多少产品。

在`filters`方法中循环遍历类别中的产品，并首先找到每个产品的`vendor`。对于每个遇到的产品，检查它是否存在于`values`数组中。

如果没有，添加一个新对象，其中包含`name`、`handle`和`count`，`count`是一个产品句柄的数组。我们存储句柄数组是为了验证产品是否已经被查看过。如果我们只是保留一个原始的数字计数，可能会遇到触发过滤器两次的情况，导致计数翻倍。通过检查产品句柄是否已经存在，我们可以确保它只被查看过一次。

如果已存在具有该名称的过滤器，则在检查其不存在后将句柄添加到数组中：

```js
filters() {
  if(this.categoriesExist) {

    let category = this.categoryProducts(this.slug),
      vendors = this.topics.vendor;

 for(let product of category.productDetails) {

        if(product.hasOwnProperty('vendor')) {
 let vendor = product.vendor; 
 if(vendor.handle) { if(!vendor.handle.count.includes(product.handle)) {
              category.values[item.handle].count.push(product.handle);
            }
          } else {
 vendors.values[vendor.handle] = {
 ...vendor,
 count: [product.handle]
 }
 }
 } 
 }

 }
  }
}
```

这里使用了之前使用的对象展开省略号（`...`），这样我们就不必写：

```js
vendors.values[product.vendor.handle] = {
  title: vendor.title,
 handle: vendor.handle,
  count: [product.handle]
}
```

如果您对此更熟悉，可以随意使用它。

复制代码以处理`tags`，但由于`tags`本身是一个数组，我们需要循环遍历每个标签并相应地添加：

```js
for(let product of category.productDetails) {

  if(product.hasOwnProperty('vendor')) {
    let vendor = product.vendor;

    if(vendor.handle) {
      if(!vendor.handle.count.includes(product.handle)) {
        category.values[item.handle].count.push(product.handle);
      }
    } else {
      vendors.values[vendor.handle] = {
        ...vendor,
        count: [product.handle]
      }
    }
  }

 if(product.hasOwnProperty('tags')) {
 for(let tag of product.tags) {
 if(tag.handle) {
 if(topicTags.values[tag.handle]) {
 if(!topicTags.values[tag.handle].count.includes(product.handle)) {
            topicTags.values[tag.handle].count.push(product.handle);
          }
 } else {
 topicTags.values[tag.handle] = {
 ...tag,
 count: [product.handle]
 }
 }
 }
 }
 }

}
```

我们的代码已经变得重复和复杂，让我们通过创建一个处理重复代码的方法来简化它。

创建一个`methods`对象，其中包含一个名为`addTopic`的函数。它将接受两个参数：要附加到的对象和单个项目。例如，使用方式如下：

```js
if(product.hasOwnProperty('vendor')) {
  this.addTopic(this.topics.vendor, product.vendor, product.handle);
}
```

创建函数并将`hasOwnProperty`内部的逻辑抽象出来。将两个参数命名为`category`和`item`，并相应地更新代码：

```js
methods: {
  addTopic(category, item, handle) {
    if(item.handle) {

      if(category.values[item.handle]) {
        if(!category.values[item.handle].count.includes(handle)) {
          category.values[item.handle].count.push(handle);
        }

      } else {

        category.values[item.handle] = {
          ...item,
          count: [handle]
        }
      }
    }
  }
}
```

更新`filters`计算属性函数，使用新的`addTopic`方法。删除函数顶部的变量声明，因为它们直接传递给方法：

```js
filters() {
  if(this.categoriesExist) {

    let category = this.categoryProducts(this.slug);

    for(let product of category.productDetails) {

      if(product.hasOwnProperty('vendor')) {
        this.addTopic(this.topics.vendor, product.vendor, product.handle);
      }

      if(product.hasOwnProperty('tags')) {
        for(let tag of product.tags) {
          this.addTopic(this.topics.tags, tag, product.handle);
        }
      }

    }
  }
}
```

在函数末尾返回`this.topics`。虽然我们可以直接在模板中引用`topics`，但我们需要确保`filters`计算属性被触发：

```js
filters() {
  if(this.categoriesExist) {
    ...
  }

  return this.topics;
}
```

在继续创建基于不同类型的动态过滤器之前，让我们显示当前的过滤器。

由于`topics`对象的设置方式，我们可以循环遍历每个子对象，然后遍历每个对象的`values`。我们将使用复选框来创建过滤器，输入的值将是每个过滤器的句柄：

```js

template: `<div>
 <div class="filters">
 <div class="filterGroup" v-for="filter in filters">
 <h3>{{ filter.title }}</h3>

 <label class="filter" v-for="value in filter.values">
 <input type="checkbox" :value="value.handle">
 {{ value.title }} ({{ value.count }})
 </label>
 </div> 
 </div>

  <list-categories />
</div>`,
```

为了跟踪已检查的内容，我们可以使用`v-model`属性。如果有多个复选框使用相同的`v-model`，Vue 会创建一个包含每个项目的数组。

在数据对象的每个`topic`对象中添加一个`checked`数组：

```js
data() {
  return {
    topics: {
      vendor: {
        title: 'Manufacturer',
        handle: 'vendor',
        checked: [],
        values: {}
      },
      tags: {
        title: 'Tags',
        handle: 'tags',
        checked: [],
        values: {}
      }
    }
  }
}
```

接下来，为每个复选框添加一个`v-model`属性，引用`filter`对象上的该数组，并添加一个点击绑定器，引用一个`updateFilters`方法：

```js
<div class="filters">
  <div class="filterGroup" v-for="filter in filters">
    <h3>{{ filter.title }}</h3>

    <label class="filter" v-for="value in filter.values">
      <input type="checkbox" :value="value.handle" v-model="filter.checked"  @click="updateFilters">
      {{ value.title }} ({{ value.count }})
    </label>
  </div> 
</div>
```

暂时创建一个空方法，稍后我们将配置它：

```js
methods: {
    addTopic(category, item) {
      ...
    },

 updateFilters() {

 }
}
```

# 动态创建过滤器

通过创建和监视固定的过滤器，我们可以利用这个机会创建动态过滤器。这些过滤器将观察产品上的`variationTypes`（例如颜色和尺寸），并列出选项 - 再次包括每个选项的计数。

为了实现这一点，我们首先需要循环遍历产品上的`variationTypes`。在添加任何内容之前，我们需要检查该变体类型是否存在于`topics`对象上，如果不存在 - 我们需要添加一个骨架对象。这会展开变体（其中包含`title`和`handle`），并且还包括空的`checked`和`value`属性：

```js
filters() {
  if(this.categoriesExist) {

    let category = this.categoryProducts(this.slug);

    for(let product of category.productDetails) {

      if(product.hasOwnProperty('vendor')) {
        this.addTopic(this.topics.vendor, product.vendor);
      }

      if(product.hasOwnProperty('tags')) {
        for(let tag of product.tags) {
          this.addTopic(this.topics.tags, tag);
        }
      }

 Object.keys(product.variationTypes).forEach(vkey => {
 let variation = product.variationTypes[vkey];

 if(!this.topics.hasOwnProperty(variation.handle)) {
 this.topics[variation.handle] = {
 ...variation,
 checked: [],
 values: {}
 }
 }
 });

    }
  }

  return this.topics;
}
```

创建了空对象后，我们现在可以循环遍历产品对象上的`variationProducts`。对于每个产品，我们可以通过当前变体的句柄访问变体。从那里，我们可以使用我们的`addTopic`方法在过滤器中包含值（例如，蓝色或 XL）：

```js
Object.keys(product.variationTypes).forEach(vkey => {
  let variation = product.variationTypes[vkey];

  if(!this.topics.hasOwnProperty(variation.handle)) {
    this.topics[variation.handle] = {
      ...variation,
      checked: [],
      values: {}
    }
  }

  Object.keys(product.variationProducts).forEach(pkey => {
 let variationProduct = product.variationProducts[pkey]; 
 this.addTopic(
 this.topics[variation.handle],
 variationProduct.variant[variation.handle],      product.handle
 );
 });

});
```

但是，我们确实需要更新我们的`addTopic`方法。这是因为动态属性具有`value`，而不是标题。

在您的`addTopic`方法中添加一个`if`语句，检查是否存在一个`value`，如果存在 - 将其设置为`title`：

```js
addTopic(category, item, handle) {
  if(item.handle) {

    if(category.values[item.handle]) {
      if(!category.values[item.handle].count.includes(handle)) {
        category.values[item.handle].count.push(handle);
      }

    } else {

 if(item.hasOwnProperty('value')) {
 item.title = item.value;
 }

      category.values[item.handle] = {
        ...item,
        count: [handle]
      }
    }
  }
}
```

在浏览器中查看应用程序应该显示出您动态添加的过滤器，以及我们添加的原始过滤器。

# 重置过滤器

在导航到不同类别之间，您会注意到，当前的过滤器不会重置。这是因为我们在每次导航之间没有清除过滤器，数组仍然存在。这并不理想，因为这意味着随着您的导航，过滤器会变得越来越长，并且不适用于产品列表。

为了解决这个问题，我们可以创建一个返回默认主题对象的方法，并在“slug”更新时调用该方法来重置`topics`对象。将`topics`对象移动到一个名为`defaultTopics`的新方法中：

```js
methods: {
 defaultTopics() {
 return {
 vendor: {
 title: 'Manufacturer',
 handle: 'vendor',
 checked: [],
 values: {}
 },
 tags: {
 title: 'Tags',
 handle: 'tags',
 checked: [],
 values: {}
 }
 }
 },

  addTopic(category, item) {
    ...
  }

  updateFilters() {

  }
}
```

在`data`函数中，将 topics 的值更改为`this.defaultTopics()`来调用该方法：

```js
data() {
  return {
    topics: this.defaultTopics()
  }
},
```

最后，在`slug`更新时，添加一个监视函数来重置主题键：

```js
watch: {
  slug() {
 this.topics = this.defaultTopics();
 }
}
```

# 在复选框过滤器更改时更新 URL

当与过滤器组件交互时，将更新 URL 查询参数。这允许用户看到过滤器的效果，将其加为书签，并在需要时共享 URL。我们已经在分页中使用了查询参数，将用户放回第一页进行过滤是有意义的 - 因为可能只有一页。

为了构建我们的过滤器查询参数，我们需要循环遍历每个过滤器类型，并为每个具有`checked`数组中的项目的过滤器添加一个新参数。然后，我们可以调用`router.push()`来更新 URL，并相应地更改显示的产品。

在`updateFilters`方法中创建一个空对象。循环遍历主题并使用选中的项目填充`filters`对象。将路由器中的`query`参数设置为`filters`对象：

```js
updateFilters() {
  let filters = {};

 Object.keys(this.topics).forEach(key => {
 let topic = this.topics[key];
 if(topic.checked.length) {
 filters[key] = topic.checked;
 }
 });

 this.$router.push({query: filters});
}
```

在右侧勾选和取消勾选过滤器应该更新 URL 中的已选项目。

# 在页面加载时预选过滤器

在加载已经在 URL 中具有过滤器的类别时，我们需要确保右侧的复选框被选中。这可以通过循环遍历现有的查询参数，并将任何匹配的键和数组添加到主题参数中来完成。由于`query`可以是数组或字符串，我们需要确保无论如何，选中的属性都是一个数组。我们还需要确保查询键确实是一个过滤器，而不是一个页面参数。

```js
filters() {
  if(this.categoriesExist) {

    let category = this.categoryProducts(this.slug);

    for(let product of category.productDetails) {
      ...
    }

 Object.keys(this.$route.query).forEach(key => {
      if(Object.keys(this.topics).includes(key)) {
        let query = this.$route.query[key];
        this.topics[key].checked = Array.isArray(query) ? query : [query];
      }
    });
  }

  return this.topics;
}
```

在页面加载时，URL 中的过滤器将被选中。

# 过滤产品

我们现在正在动态创建和附加过滤器，并且激活过滤器会更新 URL 中的查询参数。现在我们可以根据 URL 参数显示和隐藏产品。我们将通过在传递给`ListProducts`组件之前对产品进行过滤来实现这一点。这确保了分页的正确工作。

在过滤产品时，打开`ListProducts.js`并为每个列表项添加一个`：key`属性，其值为`handle`：

```js
<ol :start="pagination.range.from + 1">
  <li v-for="product in paginate(products)" :key="product.handle">
    ...
  </li>
</ol>
```

打开`CategoryPage`视图，并在`methods`对象中创建一个名为`filtering()`的方法，并添加`return true`以开始。该方法应接受两个参数，一个`product`和一个`query`对象：

```js
methods: {
  filtering(product, query) {

 return true;
 }
}
```

接下来，在`category`计算函数内部，如果存在查询参数，我们需要过滤产品。但是，我们需要小心，如果页面号也是一个查询参数，我们不要触发过滤器。

创建一个名为`filters`的新变量，它是来自路由的查询对象的副本。接下来，如果页面参数存在，则从我们的新对象中`删除`它。从那里，我们可以检查查询对象是否有其他内容，如果有，就可以在我们的产品数组上运行原生 JavaScript 的`filter()`函数 - 将产品和新的查询/过滤器对象传递给我们的方法：

```js
category() {
  if(this.categoriesExist) {
    let category = this.categoryProducts(this.slug),
 filters = Object.assign({}, this.$route.query);

 if(Object.keys(filters).length && filters.hasProperty('page')) {
 delete filters.page;
 }

 if(Object.keys(filters).length) {
 category.productDetails = category.productDetails.filter(
 p => this.filtering(p, filters)
 );
 }

    if(!category) {
      this.categoryNotFound = true;
    }
    return category;
  }
}
```

刷新您的应用程序以确保产品仍然显示。

要过滤产品，涉及到一个相当复杂的过程。我们想要检查一个属性是否在查询参数中；如果是，我们将设置一个占位值为`false`。如果产品上的属性与查询参数相匹配，我们将将占位符设置为`true`。然后我们对每个查询参数重复这个过程。完成后，我们只显示符合所有条件的产品。

我们构建的方式允许产品在类别内为“或”，但在不同部分为“与”。例如，如果用户选择了多种颜色（红色和绿色）和一个标签（配件），它将显示所有红色或绿色配件的产品。

我们的过滤器是通过标签、供应商和动态过滤器创建的。由于其中两个属性是固定的，我们将首先检查这些属性。动态过滤器将通过重构它们的构建方式进行验证。

创建一个`hasProperty`对象，它将是我们用来跟踪产品具有的查询参数的占位符对象。我们将从`vendor`开始 - 因为这是最简单的属性。

我们首先通过循环遍历查询属性 - 如果有多个属性（例如红色和绿色），接下来，我们需要确认`query`中是否存在`vendor` - 如果存在，我们将在`hasProperty`对象中将`vendor`属性设置为`false`，然后我们检查`vendor`句柄是否与查询属性相同，如果匹配，我们将更改`hasProperty.vendor`属性为`true`。

```js
filtering(product, query) {
  let display = false,
 hasProperty = {};

 Object.keys(query).forEach(key => {
 let filter = Array.isArray(query[key]) ? query[key] : [query[key]];

 for(attribute of filter) {
 if(key == 'vendor') {

 hasProperty.vendor = false;
 if(product.vendor.handle == attribute) {
 hasProperty.vendor = true;
 }

 }      }
 });

 return display;
}
```

这将根据供应商是否与所选过滤器匹配来更新`hasProperty`对象。我们可以使用`tags`复制该功能 - 记住产品上的标签是我们需要过滤的对象。

还需要检查过滤器构建的动态属性。这是通过检查每个`variationProduct`上的变体对象，并在匹配时更新`hasProperty`对象来完成的。

```js
filtering(product, query) {
  let display = false,
    hasProperty = {};

    Object.keys(query).forEach(key => {
      let filter = Array.isArray(query[key]) ? query[key] : [query[key]];

      for(attribute of filter) {
        if(key == 'vendor') {

          hasProperty.vendor = false;
          if(product.vendor.handle == attribute) {
            hasProperty.vendor = true;
          }

        } else if(key == 'tags') {
 hasProperty.tags = false;

 product[key].map(key => {
 if(key.handle == attribute) {
 hasProperty.tags = true;
 }
 });

 } else {
 hasProperty[key] = false;

 let variant = product.variationProducts.map(v => {
 if(v.variant[key] && v.variant[key].handle == attribute) {
 hasProperty[key] = true;
 }
 });
 }
 }
    });

  return display;
}
```

最后，我们需要检查`hasProperty`对象的每个属性。如果所有值都设置为`true`，我们可以将产品的显示设置为`true` - 表示它将显示。如果其中一个值为`false`，则产品将不会显示，因为它不符合所有条件。

```js
filtering(product, query) {
  let display = false,
    hasProperty = {};

    Object.keys(query).forEach(key => {
      let filter = Array.isArray(query[key]) ? query[key] : [query[key]];

      for(attribute of filter) {
        if(key == 'vendor') {

          hasProperty.vendor = false;
          if(product.vendor.handle == attribute) {
            hasProperty.vendor = true;
          }

        } else if(key == 'tags') {
          hasProperty.tags = false;

          product[key].map(key => {
            if(key.handle == attribute) {
              hasProperty.tags = true;
            }
          });

        } else {
          hasProperty[key] = false;

          let variant = product.variationProducts.map(v => {
            if(v.variant[key] && v.variant[key].handle == attribute) {
              hasProperty[key] = true;
            }
          });
        }
      }

 if(Object.keys(hasProperty).every(key => hasProperty[key])) {
 display = true;
 }

    });

  return display;
}
```

现在我们有了一个成功的过滤产品列表。在浏览器中查看您的应用程序并更新过滤器 - 注意每次单击时产品的显示和隐藏。请注意，即使您按下刷新，只有过滤后的产品会显示。

# 总结

在本章中，我们创建了一个分类列表页面，允许用户查看某一类别中的所有产品。该列表可以进行分页，并且可以改变排序。我们还创建了一个筛选组件，允许用户缩小结果范围。

现在我们的产品可以浏览、筛选和查看，我们可以继续制作购物车和结账页面。
