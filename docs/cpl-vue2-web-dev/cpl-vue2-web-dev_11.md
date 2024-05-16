# 第十一章：构建电子商务商店-添加结账功能

在过去的几章中，我们创建了一个电子商务商店。到目前为止，我们已经创建了一个产品页面，允许我们查看图像和产品变体，可能是尺寸或样式。我们还创建了一个带有过滤器和分页的类别页面，包括一个主页类别页面，其中包含特定的选定产品。

我们的用户可以浏览和筛选产品，并查看有关特定产品的更多信息。现在我们要做的是：

+   构建功能，允许用户将产品添加到购物篮中并从中删除

+   允许用户结账

+   添加订单确认页面

提醒一下-我们不会获取任何账单详细信息，但我们会创建一个订单确认屏幕。

# 创建购物篮数组占位符

为了帮助我们在整个应用程序中持久保存购物篮中的产品，我们将把用户选择的产品存储在 Vuex 存储中。这将以对象数组的形式存在。每个对象将包含几个关键信息，这些信息将允许我们在不必每次查询 Vuex 存储时都能显示购物篮中的产品。它还允许我们存储有关产品页面当前状态的详细信息-当选择变体时，记住图像更新。

我们将为添加到购物篮中的每个产品存储以下详细信息：

+   产品标题

+   产品句柄，以便我们可以链接回产品

+   选定的变体标题（在选择框中显示）

+   当前选定的图像，以便我们可以在结账时显示适当的图像

+   变体详细信息，包括价格、重量和其他细节

+   变体 SKU，这将帮助我们确定产品是否已经添加

+   数量，用户已添加到购物篮中的物品数量

由于我们将把所有这些信息存储在一个对象中，该对象包含在一个数组中，我们需要在存储中创建一个占位符数组。在存储的`state`对象中添加一个名为`basket`的新键，并将其设置为空数组：

```js
const store = new Vuex.Store({
  state: {
    products: {},
    categories: {},

    categoryHome: {
      title: 'Welcome to the Shop',
      handle: 'home',
      products: [
        ...
      ]
    },

    basket: []

  },

  mutations: {
    ...
  },

  actions: {
    ...
  },

  getters: {
    ...
  }
});
```

# 将产品信息添加到存储中

准备好我们的`basket`数组来接收数据后，我们现在可以创建一个 mutation 来添加产品对象。打开`ProductPage.js`文件，更新`addToBasket`方法，调用`$store`的 commit 函数，而不是我们放置的`alert`。

我们需要将产品添加到购物篮的所有所需信息都存储在`ProductPage`组件上，因此我们可以使用`this`关键字将组件实例传递给`commit()`函数。当我们构建突变时，这将变得清晰。

将函数调用添加到`ProductPage`方法中：

```js
methods: {
  ...

 addToBasket() {
 this.$store.commit('addToBasket', this);
 }
}
```

# 创建存储突变以将产品添加到购物篮中

转到 Vuex 存储并创建一个名为`addToBasket`的新突变。这将接受`state`作为第一个参数，组件实例作为第二个参数。通过传递实例，我们可以访问组件上的变量、方法和计算值。

```js
mutations: {
  products(state, payload) {
    ...
  },

  categories(state, payload) {
    ...
  },

 addToBasket(state, item) {

 }
}
```

现在，我们可以继续将产品添加到`basket`数组中。第一步是添加具有所述属性的产品对象。由于它是一个数组，我们可以使用`push()`函数来添加对象。

接下来，使用`item`及其属性构建对象，将对象添加到数组中。通过访问`ProductPage`组件，我们可以使用`variantTitle`方法构建变体标题，该标题将显示在选择框中。默认情况下，将数量设置为`1`：

```js
addToBasket(state, item) {
  state.basket.push({
 sku: item.variation.sku,
 title: item.product.title,
 handle: item.slug,
 image: item.image,
 variationTitle: item.variantTitle(item.variation),
 variation: item.variation,
 quantity: 1
 });
}
```

现在，将产品添加到`basket`数组中。然而，当您将两个相同的商品添加到购物篮时，会出现一个问题。它不会增加`quantity`，而是简单地添加第二个产品。

可以通过检查数组中是否已存在`sku`来解决此问题。如果存在，我们可以增加该商品的数量，如果不存在，我们可以将新商品添加到`basket`数组中。每个产品的每个变体的`sku`是唯一的。或者，我们可以使用条形码属性。

使用原生的`find` JavaScript 函数，我们可以识别出具有与传入的 SKU 相匹配的任何产品：

```js
addToBasket(state, item) {
 let product = state.basket.find(p => {
 if(p.sku == item.variation.sku) {
 }
 });

  state.basket.push({
    sku: item.variation.sku,
    title: item.product.title,
    handle: item.slug,
    image: item.image,
```

```js
    variationTitle: item.variantTitle(item.variation),
    variation: item.variation,
    quantity: 1
  });
}
```

如果匹配，我们可以使用 JavaScript 中的`++`符号将该对象的数量增加一。如果不匹配，我们可以将新对象添加到`basket`数组中。使用`find`函数时，如果产品存在，我们可以返回该产品。如果不存在，我们可以添加一个新商品：

```js
addToBasket(state, item) {
  let product = state.basket.find(p => {
    if(p.sku == item.variation.sku) {
      p.quantity++;

 return p;
    }
  });

  if(!product) {
    state.basket.push({
      sku: item.variation.sku,
      title: item.product.title,
      handle: item.slug,
      image: item.image,
      variationTitle: item.variantTitle(item.variation),
      variation: item.variation,
      quantity: 1
    });
 }
}
```

现在，当商品添加到购物篮中时，购物篮会被填充，并且在已存在时会递增。

为了提高应用的可用性，当用户将商品添加到购物篮时，我们应该给予用户一些反馈。可以通过更新“添加到购物篮”按钮并在网站头部显示一个带有指向购物篮的链接的产品计数来实现这一点。

# 在添加商品时更新“添加到购物篮”按钮

作为对我们商店的可用性改进，当用户点击“添加到购物篮”按钮时，我们将更新该按钮。它将变为“已添加到您的购物篮”，并在一段时间内应用一个类，例如两秒钟，然后返回到之前的状态。CSS 类将允许您对按钮进行不同的样式设置，例如将背景改为绿色或稍微进行变换。

通过在组件上使用一个数据属性来实现这一点，将其设置为`true`和`false`，当商品被添加时。CSS 类和文本将使用此属性来确定要显示的内容，而`setTimeout` JavaScript 函数将更改属性的状态。

打开`ProductPage`组件，并在数据对象中添加一个名为`addedToBasket`的新键。默认将其设置为`false`：

```js
data() {
  return {
    slug: this.$route.params.slug,
    productNotFound: false,
    image: false,
    variation: false,
    addedToBasket: false
  }
}
```

更新按钮文本以适应这个变化。由于已经有一个三元`if`，我们将在其中嵌套另一个。如果需要，这可以抽象成一个方法。

在按钮中，用一个额外的三元运算符替换`Add to basket`条件，取决于`addedToBasket`变量是否为 true。我们还可以根据此属性添加一个条件类：

```js
<button 
  @click="addToBasket()" 
  :class="(addedToBasket) ? 'isAdded' : ''" 
  :disabled="!variation.quantity"
>
  {{ 
    (variation.quantity) ? 
    ((addedToBasket) ? 'Added to your basket' : 'Add to basket') : 
    'Out of stock'
```

```js
  }}
</button>
```

刷新应用并导航到一个产品页面，确保正确的文本被显示。将`addedToBasket`变量更新为`true`，以确保一切都显示正常。然后将其设置回`false`。

接下来，在`addToBasket()`方法中，将该属性设置为 true。当商品被添加到购物篮时，这将更新文本：

```js
addToBasket() {
  this.$store.commit('addToBasket', this);

 this.addedToBasket = true;
}
```

当您点击按钮时，文本现在会更新，但它永远不会重置。在之后添加一个`setTimeout` JavaScript 函数，它会在一定时间后将其设置回`false`：

```js
addToBasket() {
  this.$store.commit('addToBasket', this);

  this.addedToBasket = true;
  setTimeout(() => this.addedToBasket = false, 2000);
}
```

`setTimeout`的时间单位是毫秒，所以`2000`等于两秒。根据需要随意调整和修改这个数字。

最后一个添加是，如果变体被更新或产品被更改，将此值重置为`false`。将该语句添加到两个`watch`函数中：

```js
watch: {
  variation(v) {
    if(v.hasOwnProperty('image')) {
      this.updateImage(v.image);
    }

    this.addedToBasket = false;
  },

  '$route'(to) {
    this.slug = to.params.slug;
    this.addedToBasket = false;
  }
}
```

# 在应用的页眉中显示产品数量

在网站的页眉中显示购物车链接以及购物车中的商品数量是常见的做法。为了实现这一点，我们将使用一个 Vuex getter 来计算并返回购物篮中的商品数量。

打开`index.html`文件并在应用程序 HTML 中添加一个`<header>`元素，并插入一个占位符`span` - 一旦我们设置了路由，我们将把它转换为链接。在 span 中，输出一个`cartQuantity`变量：

```js
<div id="app">
  <header>
 <span>Cart {{ cartQuantity }}</span>
 </header>
  <main>
    <router-view></router-view>
  </main>
  <aside>
    <router-view name="sidebar"></router-view>
  </aside>
</div>
```

转到你的`Vue`实例并创建一个包含`cartQuantity`函数的`computed`对象：

```js
new Vue({
  el: '#app',

  store,
  router,

 computed: {
 cartQuantity() {

 }
 },

  created() {
    CSV.fetch({url: './data/csv-files/bicycles.csv'}).then(data => {
      this.$store.dispatch('initializeShop', this.$formatProducts(data));
    });
  }
});
```

如果我们的标题中的商品比购物车链接多，最好将其抽象为一个单独的组件，以保持方法、布局和函数的封装。然而，由于在我们的示例应用程序中只会显示这一个链接，将函数添加到`Vue`实例中就足够了。

在 store 中创建一个名为`cartQuantity`的新 getter。作为占位符，返回`1`。`state`将被用于计算数量，所以现在确保将其传递给函数：

```js
getters: {
  ...

 cartQuantity: (state) => { 
 return 1;
 }
}
```

返回到你的`Vue`实例并返回 getter 的结果。理想情况下，我们希望在括号中显示`basket`的数量，但只有在有商品时才显示括号。在计算函数中，检查这个 getter 的结果，并在结果存在时输出带有括号的结果：

```js
cartQuantity() {
  const quantity = this.$store.getters.cartQuantity;
 return quantity ? `(${quantity})` : '';
}
```

在 Vuex getter 中更改结果应该会显示带括号的数字或根本不显示任何内容。

# 计算购物篮数量

有了显示逻辑，我们现在可以继续计算购物篮中有多少商品。我们可以计算`basket`数组中的商品数量，但是这只会告诉我们现在有多少不同的商品，而不是同一种商品被添加了多次。

相反，我们需要遍历购物篮中的每个商品并将数量相加。创建一个名为`quantity`的变量并将其设置为`0`。遍历购物篮中的商品并将`item.quantity`变量添加到`quantity`变量中。最后，返回我们的变量与正确的总和：

```js
cartQuantity: (state) => {
 let quantity = 0;
 for(let item of state.basket) {
 quantity += item.quantity;
 }
 return quantity;
}
```

转到应用程序并添加一些商品到购物篮中，以验证购物篮数量是否被正确计算。

# 最终确定 Shop Vue-router 的 URL

我们现在处于一个可以最终确定我们商店的 URL 的阶段 - 包括创建重定向和结账链接。回顾第八章，*介绍 Vue-Router 和加载基于 URL 的组件*，我们可以看到我们缺少哪些。这些是：

+   `/category` - 重定向到`/`

+   `/product` - 重定向到`/`

+   `/basket` - 加载`OrderBasket`组件

+   `/checkout` - 加载`OrderCheckout`组件

+   `/complete` - 加载`OrderConfirmation`组件

在路由数组的适当位置创建重定向。在路由数组的底部，为`Order`组件创建三个新路由：

```js
routes: [
  {
    path: '/',
    name: 'Home',
    ...
  },
  {
 path: '/category',
 redirect: {name: 'Home'}
 },
  {
    path: '/category/:slug',
    name: 'Category',
    ...
  },
  {
 path: '/product',
 redirect: {name: 'Home'}
 },
  {
    path: '/product/:slug',
    name: 'Product',
    component: ProductPage
  },
  {
path: '/basket',
 name: 'Basket',
 component: OrderBasket
 },
 {
 path: '/checkout',
 name: 'Checkout',
 component: OrderCheckout
 },
 {
 path: '/complete',
 name: 'Confirmation',
 component: OrderConfirmation
 },

  {
    path: '/404', 
    alias: '*',
    component: PageNotFound
  }
]
```

我们现在可以使用`router-link`来更新应用程序标题中的占位符`<span>`：

```js
<header>
  <router-link :to="{name: 'Basket'}">Cart {{ cartQuantity }}</router-link>
</header>
```

# 构建订单流程和 ListProducts 组件

对于结账的三个步骤，我们将在所有三个步骤中使用相同的组件：`ListProducts`组件。在`OrderCheckout`和`OrderConfirmation`组件中，它将处于固定的、不可编辑的状态，而在`OrderBasket`组件中，用户需要能够更新数量和删除物品。

由于我们将在结账时进行操作，我们需要在`basket`数组中存在产品。为了避免每次刷新应用程序时都要查找产品并将其添加到购物篮中，我们可以通过在存储中硬编码一个数组来确保`basket`数组中有一些产品。

为了实现这一点，导航到一些产品并将它们添加到购物篮中。确保有一些产品和数量进行测试。接下来，在浏览器中打开 JavaScript 控制台并输入以下命令：

```js
console.log(JSON.stringify(store.state.basket));
```

这将输出一个您的产品数组的字符串。将其复制并粘贴到您的存储中，替换`basket`数组：

```js
state: {
  products: {},
  categories: {},

  categoryHome: {
    title: 'Welcome to the Shop',
    handle: 'home',
    products: [
      ...
    ]
  },

  basket: [{"sku":...}]
},
```

页面加载时，标题中的购物车计数应更新为您添加的正确数量的物品。

现在我们可以继续构建我们的结账流程了。购物篮中的产品显示比结账和订单确认屏幕更复杂，因此我们将反向工作。从订单确认页面开始，然后转到结账页面，在前往购物篮之前增加更多复杂性，添加退出产品的功能。

# 订单确认屏幕

订单确认屏幕是在订单完成后显示的屏幕。它确认购买的物品，并可能包括预计的交货日期。

在`OrderConfirmation.js`文件中创建一个包含`<h1>`和与订单完成相关的一些相关内容的模板：

```js
const OrderConfirmation = {
  name: 'OrderConfirmation',

  template: `<div>
    <h1>Order Complete!</h1>
    <p>Thanks for shopping with us - you can expect your products within 2 - 3 working days</p>
  </div>`
};
```

在浏览器中打开应用程序，将产品添加到购物篮中并完成订单以确认其是否正常工作。下一步是包含`ListProducts`组件。首先，确保`ListProducts`组件正确初始化并具有初始模板：

```js
const ListPurchases = {
  name: 'ListPurchases',

  template: `<table></table>`
};
```

将`components`对象添加到`OrderConfirmation`组件中，并包含`ListProducts`组件。接下来，在模板中包含它：

```js
const OrderConfirmation = {
  name: 'OrderConfirmation',

  template: `<div>
    <h1>Order Complete!</h1>
    <p>Thanks for shopping with us - you can expect your products within 2 - 3 working days</p>
    <list-purchases />
  </div>`,

 components: {
 ListPurchases
 }
};
```

再次打开`ListPurchases`组件以开始显示产品。该组件的默认状态将是列出购物篮中的产品，以及所选的变体。每个产品的价格将被显示出来，如果数量大于一，则还会显示价格。最后，将显示一个总计。

第一步是将购物篮列表放入我们的组件中。创建一个带有`products`函数的`computed`对象。这个函数应该返回购物篮中的产品：

```js
const ListPurchases = {
  name: 'ListPurchases',

  template: `<table></table>`,

  computed: {
 products() {
 return this.$store.state.basket;
 }
 }
};
```

现在我们可以在表格中循环遍历购物篮中的产品，并显示所需的信息。这包括缩略图、产品和变体标题、价格、数量和项目的总价格。还要在表格中添加一个标题行，以便用户知道每列的内容：

```js
  template: `<table>
    <thead>
      <tr>
        <th></th>
        <th>Title</th>
        <th>Unit price</th>
        <th>Quantity</th>
        <th>Price</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="product in products">
        <td>
          <img 
            :src="product.image.source" 
            :alt="product.image.alt || product.variationTitle"
            width="80"
          >
        </td>
        <td>
          <router-link :to="{name: 'Product', params: {slug: product.handle}}">
            {{ product.title }}
          </router-link><br>
          {{ product.variationTitle }}
        </td>
        <td>{{ product.variation.price }}</td>
        <td>{{ product.quantity }}</td>
        <td>{{ product.variation.price * product.quantity }}</td>
      </tr>
    </tbody>
  </table>`,
```

请注意，每行的价格只是单位价格乘以数量。现在我们有了用户购买的标准产品列表。

# 使用 Vue 过滤器格式化价格

价格目前是一个整数，因为它在数据中是这样的。在产品页面上，我们只是在价格前面加了一个`$`符号来表示价格，然而，现在正是利用 Vue 过滤器的绝佳机会。过滤器允许您在模板中操作数据，而无需使用方法。过滤器可以链接在一起，用于执行通常的单一修改，例如将字符串转换为小写或将数字格式化为货币。

过滤器使用管道（`|`）运算符。例如，如果我们有一个将文本转换为小写的过滤器，可以像下面这样使用它：

```js
{{ product.title | lowercase }}
```

过滤器在组件的`filters`对象中声明，并接受一个输出前置参数。

在`ListPurchases`组件中创建一个`filters`对象，并在其中创建一个名为`currency()`的函数。这个函数接受一个名为`val`的参数，并应该返回该变量的值：

```js
filters: {
  currency(val) {
    return val;
  }
},
```

现在我们可以使用这个函数来操作价格整数。在模板中将过滤器添加到单位价格和总价格中：

```js
<td>{{ product.variation.price | currency }}</td>
<td>{{ product.quantity }}</td>
<td>{{ product.variation.price * product.quantity | currency }}</td>
```

在浏览器中您不会注意到任何变化，因为我们还没有对值进行操作。更新函数以确保数字保留两位小数，并在前面加上`$`符号：

```js
filters: {
  currency(val) {
    return ' + val.toFixed(2);
  }
},
```

我们的价格现在已经很好地格式化并正确显示。

# 计算总价格

购物清单的下一个添加是购物篮的总价值。这需要以与我们之前计算购物篮数量的方式进行计算。

创建一个新的`computed`函数标题为`totalPrice`。该函数应该循环遍历产品并累加价格，考虑到任何多个数量：

```js
totalPrice() {
  let total = 0;

  for(let p of this.products) {
    total += (p.variation.price * p.quantity);
  }

  return total;
}
```

现在我们可以更新模板以包括总价格 - 确保我们通过`currency`过滤器传递它：

```js
template: `<table>
  <thead>
    <tr>
      <th></th>
      <th>Title</th>
      <th>Unit price</th>
      <th>Quantity</th>
      <th>Price</th>
    </tr>
  </thead>
  <tbody>
    <tr v-for="product in products">
      <td>
        <img 
          :src="product.image.source" 
          :alt="product.image.alt || product.variationTitle"
          width="80"
        >
      </td>
      <td>
        <router-link :to="{name: 'Product', params: {slug: product.handle}}">
          {{ product.title }}
        </router-link><br>
        {{ product.variationTitle }}
      </td>
      <td>{{ product.variation.price | currency }}</td>
      <td>{{ product.quantity }}</td>
      <td>{{ product.variation.price * product.quantity | currency }}</td>
    </tr>
  </tbody>
  <tfoot>
 <td colspan="4">
 <strong>Total:</strong>
 </td>
 <td>{{ totalPrice | currency }}</td>
 </tfoot>
</table>`,
```

# 创建一个订单结账页面

我们的`OrderCheckout`页面的构成与`OrderConfirmation`页面类似 - 但是，在真实的商店中，这将是付款之前的页面。该页面允许用户在导航到付款页面之前填写其帐单和交付详细信息。复制`OrderConfirmation`页面并更新标题和信息文本：

```js
const OrderCheckout = {
  name: 'OrderCheckout',

  template: '<div>;
    <h1>Order Confirmation</h1>
    <p>Please check the items below and fill in your details to complete your order</p>
    <list-purchases />
  </div>',

  components: {
    ListPurchases
  }
};
```

在`<list-purchases />`组件下方，创建一个表单，包含几个字段，以便我们可以收集帐单和交付名称和地址。对于这个示例，只需收集姓名、地址的第一行和邮政编码：

```js
template: '<div>
  <h1>Order Confirmation</h1>
  <p>Please check the items below and fill in your details to complete your order</p>
  <list-purchases />

  <form>
 <fieldset>
 <h2>Billing Details</h2>
 <label for="billingName">Name:</label>
 <input type="text" id="billingName">
 <label for="billingAddress">Address:</label>
 <input type="text" id="billingAddress">
 <label for="billingZipcode">Post code/Zip code:</label>
 <input type="text" id="billingZipcode">
 </fieldset>
 <fieldset>
 <h2>Delivery Details</h2>
 <label for="deliveryName">Name:</label>
 <input type="text" id="deliveryName">
 <label for="deliveryAddress">Address:</label>
 <input type="text" id="deliveryAddress">
 <label for="deliveryZipcode">Post code/Zip code:</label>
 <input type="text" id="deliveryZipcode">
 </fieldset>
 </form>
</div>',
```

现在我们需要创建一个数据对象，并将每个字段绑定到一个键。为了帮助分组每个集合，为`delivery`和`billing`分别创建一个对象，并在内部创建正确名称的字段：

```js
data() {
  return {
    billing: {
      name: '',
      address: '',
      zipcode: ''
    },
    delivery: {
      name: '',
      address: '',
      zipcode: ''
    }
  }
}
```

为每个输入添加`v-model`，将其链接到相应的数据键：

```js
<form>
  <fieldset>
    <h2>Billing Details</h2>
    <label for="billingName">Name:</label>
    <input type="text" id="billingName" v-model="billing.name">
    <label for="billingAddress">Address:</label>
    <input type="text" id="billingAddress" v-model="billing.address">
    <label for="billingZipcode">Post code/Zip code:</label>
    <input type="text" id="billingZipcode" v-model="billing.zipcode">
  </fieldset>
  <fieldset>
    <h2>Delivery Details</h2>
    <label for="deliveryName">Name:</label>
    <input type="text" id="deliveryName" v-model="delivery.name">
    <label for="deliveryAddress">Address:</label>
    <input type="text" id="deliveryAddress" v-model="delivery.address">
    <label for="deliveryZipcode">Post code/Zip code:</label>
    <input type="text" id="deliveryZipcode" v-model="delivery.zipcode">
  </fieldset>
</form>
```

下一步是创建一个`submit`方法并整理数据以便能够将其传递给下一个屏幕。创建一个名为`submitForm()`的新方法。由于本示例中不处理付款，所以可以在该方法中路由到确认页面：

```js
methods: {
  submitForm() {
    // this.billing = billing details
    // this.delivery = delivery details

    this.$router.push({name: 'Confirmation'});
  }
}
```

现在我们可以将`submit`事件绑定到表单上，并添加一个提交按钮。与`v-bind:click`属性（或`@click`）类似，Vue 允许您使用`@submit=""`属性将`submit`事件绑定到一个方法上。

在`<form>`元素中添加声明并在表单中创建一个提交按钮：

```js
<form @submit="submitForm()">
  <fieldset>
    ...
  </fieldset>

  <fieldset>
    ...
  </fieldset>

  <input type="submit" value="Purchase items">
</form>
```

在提交表单时，应用程序应将您重定向到我们的确认页面。

# 在地址之间复制详细信息

几个商店都有的一个功能是将交付地址标记为与帐单地址相同。我们可以采用几种方法来实现这一点，您可以根据自己的选择来做。即时选项有：

+   有一个“复制详细信息”按钮 - 这将从帐单复制详细信息到交付，但不会保持它们同步

+   有一个复选框，可以保持两者同步 - 勾选该框会禁用交付框字段，但会填充帐单详细信息

对于这个示例，我们将编写第二个选项。

在两个字段集之间创建一个复选框，通过`v-model`将其绑定到数据对象中的一个属性`sameAddress`：

```js
<form @submit="submitForm()">
  <fieldset>
     ...
  </fieldset>
 <label for="sameAddress">
 <input type="checkbox" id="sameAddress" v-model ="sameAddress">
 Delivery address is the same as billing
 </label>
  <fieldset>
    ...
  </fieldset>

  <input type="submit" value="Purchase items">
</form>
```

在数据对象中创建一个新的键，并将其默认设置为`false`：

```js
data() {
  return {
    sameAddress: false,

    billing: {
      name: '',
      address: '',
      zipcode: ''
    },
    delivery: {
      name: '',
      address: '',
      zipcode: ''
    }
  }
},
```

下一步是如果复选框被选中，则禁用 delivery 字段。这可以通过根据复选框的结果激活`disabled`HTML 属性来实现。类似于我们在产品页面上禁用“添加到购物车”按钮的方式，将 delivery 字段上的 disabled 属性绑定到`sameAddress`变量上：

```js
<fieldset>
  <h2>Delivery Details</h2>
  <label for="deliveryName">Name:</label>
  <input type="text" id="deliveryName" v-model="delivery.name" :disabled="sameAddress">
  <label for="deliveryAddress">Address:</label>
  <input type="text" id="deliveryAddress" v-model="delivery.address" :disabled="sameAddress">
  <label for="deliveryZipcode">Post code/Zip code:</label>
  <input type="text" id="deliveryZipcode" v-model="delivery.zipcode" :disabled="sameAddress">
</fieldset>
```

现在勾选复选框将禁用字段，使用户无法输入任何数据。下一步是在两个部分之间复制数据。由于我们的数据对象具有相同的结构，我们可以创建一个`watch`函数，当复选框被选中时，将`delivery`对象设置为与`billing`对象相同。

创建一个新的`watch`对象和函数来处理`sameAddress`变量。如果它为`true`，则将 delivery 对象设置为与 billing 对象相同：

```js
watch: {
  sameAddress() {
    if(this.sameAddress) {
      this.delivery = this.billing;
    }
  }
}
```

添加了`watch`函数后，我们可以输入数据到 billing 地址，勾选复选框，然后 delivery 地址会自动填充。最好的是它们现在保持同步，所以如果你更新 billing 地址，delivery 地址会实时更新。问题出现在当你取消勾选复选框并编辑 billing 地址时，delivery 地址仍然会更新。这是因为我们将这两个对象绑定在一起。

添加一个`else`语句，当复选框未选中时，复制 billing 地址：

```js
watch: {
  sameAddress() {
    if(this.sameAddress) {
      this.delivery = this.billing;
    } else {
 this.delivery = Object.assign({}, this.billing);
 }
  }
}
```

现在我们有一个功能完善的订单确认页面，可以收集账单和交付细节。

# 创建可编辑的购物篮

现在我们需要创建我们的购物篮。它需要以类似于结账和确认页面的方式显示产品，但它需要给用户编辑购物篮内容的能力-删除项目或更新数量。

作为起点，打开`OrderBasket.js`并包含`list-purchases`组件，就像我们在确认页面上所做的那样：

```js
const OrderBasket = {
  name: 'OrderBasket',

  template: `<div>
    <h1>Basket</h1>
    <list-purchases />
  </div>`,

  components: {
    ListPurchases
  }
};
```

接下来我们需要编辑`list-purchases`组件。为了确保我们可以区分视图，我们将添加一个`editable`属性。默认情况下设置为`false`，在购物篮中设置为`true`。在购物篮中的组件中添加这个属性：

```js
template: `<div>
  <h1>Basket</h1>
  <list-purchases :editable="true" />
</div>`,
```

现在我们需要告诉`ListPurchases`组件接受这个参数，以便我们可以在组件内部对其进行操作：

```js
props: {
  editable: {
    type: Boolean,
    default: false
  }
},
```

# 创建可编辑字段

现在我们有一个属性来确定我们的购物篮是否可编辑。这允许我们显示删除链接并使数量成为可编辑框。

在`ListPurchases`组件中，在数量旁边创建一个新的表格单元格，并仅在购买可见时显示它。在这种状态下，将静态数量隐藏。在新的单元格中，添加一个值设置为数量的输入框。我们还将绑定一个`blur`事件到该框。`blur`事件是一个原生 JavaScript 事件，当输入框失去焦点时触发。在失去焦点时，触发一个`updateQuantity`方法。该方法应该接受两个参数：事件，其中包含新的数量，以及该特定产品的 SKU：

```js
<tbody>
  <tr v-for="product in products">
    <td>
      <img 
        :src="product.image.source" 
        :alt="product.image.alt || product.variationTitle"
        width="80"
      >
    </td>
    <td>
      <router-link :to="{name: 'Product', params: {slug: product.handle}}">
        {{ product.title }}
      </router-link><br>
      {{ product.variationTitle }}
    </td>
    <td>{{ product.variation.price | currency }}</td>
    <td v-if="!editable">{{ product.quantity }}</td>
    <td v-if="editable">
      <input 
 type="text"
 :value="product.quantity" 
 @blur="updateQuantity($event, product.sku)"
 >
    </td>
    <td>{{ product.variation.price * product.quantity | currency }}</td>
  </tr>
</tbody>
```

在组件上创建新的方法。该方法应该循环遍历产品，找到具有匹配 SKU 的产品并将数量更新为整数。我们还需要更新存储的结果，以便在页面顶部更新数量。我们将创建一个通用的 mutation，接受带有新值的完整`basket`数组，以允许相同的 mutation 用于产品删除。

创建更新数量并提交名为`updatePurchases`的 mutation：

```js
methods: {
  updateQuantity(e, sku) {
    let products = this.products.map(p => {
      if(p.sku == sku) {
        p.quantity = parseInt(e.target.value);
      }
      return p;
    });

    this.$store.commit('updatePurchases', products);
  }
}
```

在 store 中，创建将`state.basket`设置为 payload 的 mutation：

```js
updatePurchases(state, payload) {
  state.basket = payload;
}
```

现在，更新数量应该更新页面顶部的商品总价和购物车数量。

# 从购物车中删除商品

下一步是让用户能够从购物车中删除商品。在`ListPurchases`组件中创建一个带有点击绑定的按钮。这个按钮可以放在任何你想要的地方 - 我们的示例将其显示为行末的额外单元格。将点击操作绑定到一个名为`removeItem`的方法。这只需要接受一个 SKU 的参数。在`ListPurchases`组件中添加以下内容：

```js
<tbody>
  <tr v-for="product in products">
    <td>
      <img 
        :src="product.image.source" 
        :alt="product.image.alt || product.variationTitle"
        width="80"
      >
    </td>
    <td>
      <router-link :to="{name: 'Product', params: {slug: product.handle}}">
        {{ product.title }}
      </router-link><br>
      {{ product.variationTitle }}
    </td>
    <td>{{ product.variation.price | currency }}</td>
    <td v-if="!editable">{{ product.quantity }}</td>
    <td v-if="editable"><input 
      type="text"
      :value="product.quantity" 
      @blur="updateQuantity($event, product.sku)"
    ></td>
    <td>{{ product.variation.price * product.quantity | currency }}</td>
    <td v-if="editable">
 <button @click="removeItem(product.sku)">Remove item</button>
 </td>
  </tr>
</tbody>
```

创建`removeItem`方法。该方法应该过滤`basket`数组，只返回不匹配传入的 SKU 的对象。一旦结果被过滤，将结果传递给与`updateQuantity()`方法中使用的相同的 mutation：

```js
removeItem(sku) {
  let products = this.products.filter(p => {
    if(p.sku != sku) {
      return p;
    }
  });

  this.$store.commit('updatePurchases', products);
}
```

我们可以做的最后一个改进是如果数量设置为 0，则触发`removeItem`方法。在`updateQuantity`方法中，在循环遍历产品之前检查值。如果它是`0`或不存在，则运行`removeItem`方法 - 通过传递 SKU：

```js
updateQuantity(e, sku) {
  if(!parseInt(e.target.value)) {
 this.removeItem(sku);
 } else {
    let products = this.products.map(p => {
      if(p.sku == sku) {
        p.quantity = parseInt(e.target.value);
      }
      return p;
    });

    this.$store.commit('updatePurchases', products);
  }
},
```

# 完成购物 SPA

最后一步是从`OrderBasket`组件添加到`OrderCheckout`页面的链接。这可以通过链接到`Checkout`路由来完成。有了这个，您的结账就完成了，您的商店也完成了！在购物篮中添加以下链接：

```js
template: `<div>
  <h1>Basket</h1>
  <list-purchases :editable="true" />
  <router-link :to="{name: 'Checkout'}">Proceed to Checkout</router-link>
</div>`,
```

# 摘要

干得好！您已经使用`Vue.js`创建了一个完整的商店单页面应用程序。您已经学会了如何列出产品及其变体，以及如何将特定的变体添加到购物篮中。您已经学会了如何创建商店过滤器和类别链接，以及创建可编辑的购物篮。

就像任何事情一样，总是有改进的空间。为什么不尝试一些这些想法呢？

+   使用`localStorage`持久化购物篮-这样添加到购物篮中的产品在访问和用户按下刷新之间保留

+   根据购物篮中产品的重量属性计算运费-使用 switch 语句创建带有不同范围的运费

+   允许从类别列表页面将没有变体的产品添加到购物篮中

+   当在类别页面上过滤某个变体时，指示哪些产品缺货

+   您自己的任何想法！
