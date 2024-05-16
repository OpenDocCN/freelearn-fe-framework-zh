# 第七章：*第七章*：使用 GraphQL 创建购物车系统

在上一章中，我们使用 Vue 3 和 Express 构建了一个旅行预订系统。这是我们从头开始构建自己的后端的第一个项目，该后端被前端使用。拥有自己的后端让我们能够做更多的事情，否则我们无法做到，例如，我们可以将喜欢的数据保存在我们自己创建的数据库中。此外，我们为管理员用户添加了自己的身份验证系统。在管理员前端，我们使用`beforeEnter`路由守卫保护我们的路由，在管理员用户登录之前检查身份验证令牌。

在本章中，我们将研究以下主题：

+   介绍 GraphQL 应用程序编程接口（API）

+   使用 Express 创建 GraphQL API

+   创建管理员前端

+   创建客户前端

# 技术要求

本章项目的代码可以在[`github.com/PacktPublishing/-Vue.js-3-By-Example/tree/master/Chapter07`](https://github.com/PacktPublishing/-Vue.js-3-By-Example/tree/master/Chapter07)找到。

# 介绍 GraphQL API

在上一章中，我们使用 Express 创建了一个后端。该端点接受 JSON 数据作为输入，并返回 JSON 数据作为响应。然而，它可以接受任何 JSON 数据，后端可能不会预期。此外，没有一种简单的方法可以在没有前端的情况下测试我们的 API 端点。这是我们可以用 GraphQL API 解决的问题。GraphQL 是一种特殊的查询语言，使客户端和服务器之间的通信更加容易。GraphQL API 具有内置的数据结构验证。每个属性都有一个数据类型，可以是简单或复杂类型，由许多具有简单数据类型的属性组成。

我们还可以使用 GraphiQL 测试 GraphQL API，这是一个网页，让我们轻松地进行自己的 GraphQL API 请求。由于每个请求都有数据类型验证，它可以根据 GraphQL API 模式的定义提供自动完成功能。该模式为我们提供了与查询和变异一起使用的所有数据类型定义。查询是让我们使用 GraphQL API 查询数据的请求，而变异是让我们以某种方式更改数据的 GraphQL 请求。

我们使用模式字符串明确定义查询和变异。查询和变异将输入类型作为输入数据的数据类型，并使用指定的输出数据类型返回数据。因此，我们永远不会对我们必须发送的数据的结构产生任何疑问，也永远不必猜测请求将返回什么类型的数据。

GraphQL API 请求基本上只是常规的**超文本传输协议**（**HTTP**）请求，只是它们具有特殊的结构。所有请求默认都发送到`/graphql`端点，并且我们将查询或变异作为 JSON 请求中`query`属性的字符串值发送。变量值与`variable`参数一起发送。

查询和变异是有名称的，并且所有查询和变异都以相同的名称发送到解析器函数中的代码，而不是路由处理程序。然后函数根据模式指定的参数获取请求数据，并在解析器函数代码中对其进行处理。

对于 Vue 3 应用程序，我们可以使用专门的 GraphQL API 客户端来更轻松地创建 GraphQL API 请求。我们只需传入一个字符串来进行查询和变异，以及与查询和变异相关的变量。

在本章中，我们将使用 Vue 3 创建一个带有管理前端和客户前端的购物车系统。然后，我们将使用 Express 和`express-graphql`库创建一个后端，该后端接收 GraphQL API 请求并将数据存储在 SQLite 数据库中。

## 设置购物车系统项目

为了创建度假预订项目，我们必须为前端、管理前端和后端创建子项目。为了创建前端和管理前端项目，我们将使用 Vue CLI。为了创建后端项目，我们将使用`express-generator`全局包。

为了设置本章的项目，我们执行以下步骤：

1.  首先，我们创建一个文件夹来存放所有项目，并将其命名为`shopping-cart`。

1.  然后我们在主文件夹内创建`admin-frontend`，`frontend`和`backend`文件夹。

1.  接下来，我们进入`admin-frontend`文件夹，并运行`npx vue create`来为`admin-frontend`文件夹添加 Vue 项目的脚手架代码。

1.  如果我们被要求在当前文件夹中创建项目，我们选择`Y`，然后当被要求选择项目的 Vue 版本时，我们选择`Vue 3`。同样，我们以`frontend`文件夹的方式运行 Vue CLI。

1.  要创建 Express 项目，我们运行 Express 应用程序生成器应用程序。要运行它，我们进入`backend`文件夹，然后运行`npx express-generator`。

这个命令将把所有需要的文件添加到`backend`文件夹中。如果出现错误，请尝试以管理员身份运行`express-generator`包。

现在我们已经完成了项目的设置，我们可以开始编写代码了。接下来，我们将开始创建 GraphQL 后端。

# 使用 Express 创建 GraphQL API

要开始购物车系统项目，我们首先要使用 Express 创建一个 GraphQL API。我们从后端开始，因为我们需要它用于两个前端。要开始，我们必须添加一些需要用于操作 SQLite 数据库并向我们的应用程序添加身份验证的库。此外，我们需要启用应用程序中的**跨域资源共享**（**CORS**）的库。

CORS 是一种让我们能够从浏览器向不同域中托管的端点发出请求的方法，与前端托管的域不同。

为了使我们的 Express 应用程序接受 GraphQL 请求，我们使用`graphql`和`express-graphql`库。要安装两者，我们运行以下命令：

```js
npm i cors jsonwebtoken sqlite3 express-graphql graphql
```

安装完包后，我们就可以开始编写代码了。

## 使用解析器函数

首先，我们要处理解析器函数。为了添加它们，我们首先在`backend`文件夹中添加一个`resolvers`文件夹。然后，我们可以为身份验证编写解析器。在`resolvers`文件夹中，我们创建一个`auth.js`文件，并编写以下代码：

```js
const jwt = require('jsonwebtoken');
module.exports = {
  login: ({ user: { username, password } }) => {
    if (username === 'admin' && password === 'password') {
      return { token: jwt.sign({ username }, 'secret') }
    }
    throw new Error('authentication failed');
  }
}
```

`login`方法是一个解析器函数。它接受具有`username`和`password`属性的`user object`属性，我们使用这些属性来检查凭据。我们检查用户名是否为`'admin'`，密码是否为`'password'`。如果凭据正确，我们就发出令牌。否则，我们抛出一个错误，这将作为`/graphql`端点的错误响应返回。

### 为订单逻辑添加解析器

接下来，我们为订单逻辑添加解析器。在`resolvers`文件夹中，我们添加`orders.js`文件。然后，我们开始编写解析器函数以获取订单数据。订单数据包含有关订单本身以及客户购买的商品的信息。为了添加解析器，我们编写以下代码：

```js
const sqlite3 = require('sqlite3').verbose();
module.exports = {
  getOrders: () => {
    const db = new sqlite3.Database('./db.sqlite');
    return new Promise((resolve, reject) => {
      db.serialize(() => {
        db.all(`
          SELECT *,
            orders.name AS purchaser_name,
            shop_items.name AS shop_item_name
          FROM orders
          INNER JOIN order_shop_items ON orders.order_id = 
            order_shop_items.order_id
          INNER JOIN shop_items ON 
           order_shop_items.shop_item_id = shop_items.
             shop_item_id
        `, [], (err, rows = []) => {
          ...
        });
      })
      db.close();
    })
  },
  ...
}
```

我们使用`sqlite3.Database`构造函数打开数据库，指定数据库路径。然后，我们返回一个查询所有订单及客户购买商品的承诺。订单存储在`orders`表中。商店库存商品存储在`shop_items`表中，我们有`order_shop_items`表来链接订单和购买的商品。

我们使用`db.all`方法进行`select`查询以获取所有数据，并使用`inner join`连接所有相关表以获取其他表中的相关数据。在回调中，我们编写以下代码来循环遍历行以创建`order`对象：

```js
const sqlite3 = require('sqlite3').verbose();
module.exports = {
  getOrders: () => {
    const db = new sqlite3.Database('./db.sqlite');
    return new Promise((resolve, reject) => {
      db.serialize(() => {
        db.all(`
          ...
        `, [], (err, rows = []) => {
          if (err) {
            reject(err)
...
          const orderArr = Object.values(orders)
          for (const order of orderArr) {
            order.ordered_items = rows
              .filter(({ order_id }) => order_id === 
                order.order_id)
              .map(({ shop_item_id, shop_item_name: name, 
                price, description }) => ({
                shop_item_id, name, price, description
              }))
          }
          resolve(orderArr)
        });
      })
      db.close();
    })
  },
  ...
}
```

这样我们就可以删除行中的重复订单条目。键是`order_id`值，值是订单数据本身。然后，我们使用`Object.values`方法获取所有订单值。我们将返回的数组分配给`orderArr`变量。然后，我们循环遍历`orderArr`数组，使用`filter`方法从原始行的数组中获取所有已订购的商店商品，以通过`order_id`查找商品。我们调用`map`从行中提取订单的商店商品数据。

我们在数据上调用`resolve`，以便从`/graphql`端点返回它作为响应。在回调的前几行中，当`err`为真时，我们调用`reject`，以便如果有错误，我们可以将错误返回给用户。

最后，我们调用`db.close()`来关闭数据库。我们可以在最后这样做，因为我们使用`db.serialize`来运行`serialize`回调中的所有语句，以便**结构化查询语言**（**SQL**）代码可以按顺序运行。

#### 添加订单

我们添加一个解析器函数来添加订单。为此，我们编写以下代码：

```js
const sqlite3 = require('sqlite3').verbose();
module.exports = {
  ...
  addOrder: ({ order: { name, address, phone, ordered_items:
    orderedItems } }) => {
    const db = new sqlite3.Database('./db.sqlite');
    return new Promise((resolve) => {
      db.serialize(() => {
        const orderStmt = db.prepare(`
          INSERT INTO orders (
            name,
            address,
            phone
...
                  shop_item_id: shopItemId
                } = orderItem
                orderShopItemStmt.run(orderId, shopItemId)
              }
              orderShopItemStmt.finalize()
            })
            resolve({ status: 'success' })
            db.close();
          });
      })
    })
  },
  ...
}
```

我们获取订单的请求有效负载，其中包括我们在参数中解构的变量。我们以相同的方式打开数据库，并且我们从相同的承诺代码和`db.serialize`调用开始，但在其中我们使用`db.prepare`方法创建一个准备好的语句。我们发出`INSERT`语句以将数据添加到订单条目中。

然后，我们使用要插入的变量值调用`run`来运行 SQL 语句。准备好的语句很好，因为我们传递给`db.run`的所有变量值都经过了清理，以防止 SQL 注入攻击。然后，我们调用`finalize`来提交事务。

接下来，我们使用`db.all`调用和`SELECT`语句获取刚刚插入到`orders`表中的行的 ID 值。在`db.all`方法的回调中，我们获取返回的数据并从返回的数据中解构`orderId`。

然后，我们创建另一个准备好的语句，将购买的商店物品的数据插入到`order_shop_items`表中。我们只是插入`order_id`和`shop_item_id`来将订单与购买的商店物品关联起来。

我们循环遍历`orderedItems`数组并调用`run`来添加条目，然后我们调用`finalize`来完成所有数据库事务。

最后，我们调用`resolve`向客户端返回成功响应。

为了完成这个文件，我们添加`removeOrder`解析器，让我们能够从数据库中删除订单。为此，我们编写以下代码：

```js
const sqlite3 = require('sqlite3').verbose();
module.exports = {
  ...
  removeOrder: ({ orderId }) => {
    const db = new sqlite3.Database('./db.sqlite');
    return new Promise((resolve) => {
      db.serialize(() => {
        const delOrderShopItemsStmt = db.prepare("DELETE FROM 
          order_shop_items WHERE order_id = (?)");
        delOrderShopItemsStmt.run(orderId)
        delOrderShopItemsStmt.finalize();
        const delOrderStmt = db.prepare("DELETE FROM orders 
          WHERE order_id = (?)");
        delOrderStmt.run(orderId)
        delOrderStmt.finalize();
        resolve({ status: 'success' })
      })
      db.close();
    })
  },
}
```

我们以与之前相同的方式调用`db.serialize`和`db.prepare`。唯一的区别是，我们正在发出`DELETE`语句来删除`order_shop_items`和`orders`表中具有给定`order_id`的所有内容。我们需要先从`order_shop_items`表中删除项目，因为订单仍然在那里被引用。

一旦我们清除了`orders`表之外对订单的所有引用，我们就可以在`orders`表中删除订单本身。

### 获取商店物品

我们在`resolvers`文件夹中创建一个`shopItems.js`文件，用于保存获取和设置商店物品的解析器函数。首先，我们从一个解析器函数开始获取所有商店物品。为此，我们编写以下代码：

```js
const sqlite3 = require('sqlite3').verbose();
module.exports = {
  getShopItems: () => {
    const db = new sqlite3.Database('./db.sqlite');
    return new Promise((resolve, reject) => {
      db.serialize(() => {
        db.all("SELECT * FROM shop_items", [], (err, rows = 
          []) => {
          if (err) {
            reject(err)
          }
          resolve(rows)
        });
      })
      db.close();
    })
  },
  ...
}
```

我们像之前一样调用`db.serialize`和`db.all`。我们只是用查询获取所有的`shop_items`条目，然后调用`resolve`将选定的数据作为响应返回给客户端。

#### 添加一个解析器函数来添加商店物品

现在，我们将添加一个解析器函数来添加商店物品。为此，我们编写以下代码：

```js
const sqlite3 = require('sqlite3').verbose();
module.exports = {
  ...
  addShopItem: ({ shopItem: { name, description, image_url: 
    imageUrl, price } }) => {
    const db = new sqlite3.Database('./db.sqlite');
    return new Promise((resolve) => {
      db.serialize(() => {
        const stmt = db.prepare(`
          INSERT INTO shop_items (
            name,
            description,
            image_url,
            price
          ) VALUES (?, ?, ?, ?)
        `
        );
        stmt.run(name, description, imageUrl, price)
        stmt.finalize();
        resolve({ status: 'success' })
      })
      db.close();
    })
  },
  ...
}
```

我们发出`INSERT`语句来插入一个条目，其中的值是从参数中解构出来的。

最后，我们通过编写以下代码添加`removeShopItem`解析器，让我们能够根据其 ID 从`shop_items`表中删除条目：

```js
const sqlite3 = require('sqlite3').verbose();
module.exports = {
  ...
  removeShopItem: ({ shopItemId }) => {
    const db = new sqlite3.Database('./db.sqlite');
    return new Promise((resolve) => {
      db.serialize(() => {
        const stmt = db.prepare("DELETE FROM shop_items WHERE 
          shop_item_id = (?)");
        stmt.run(shopItemId)
        stmt.finalize();
        resolve({ status: 'success' })
      })
      db.close();
    })
  },
}
```

### 将解析器映射到查询和变异

我们需要将解析器映射到查询和变异，以便在进行 GraphQL API 请求时调用它们。为此，我们转到`app.js`文件并添加一些内容。我们还将添加一些中间件，以便我们可以启用跨域通信和对某些请求进行令牌检查。为此，我们首先编写以下代码：

```js
const createError = require('http-errors');
const express = require('express');
const path = require('path');
const cookieParser = require('cookie-parser');
const logger = require('morgan');
const { graphqlHTTP } = require('express-graphql');
const { buildSchema } = require('graphql');
const cors = require('cors')
const shopItemResolvers = require('./resolvers/shopItems')
const orderResolvers = require('./resolvers/orders')
const authResolvers = require('./resolvers/auth')
const jwt = require('jsonwebtoken');
```

我们使用`require`函数导入所需的所有内容。我们可以用前面的代码块替换文件顶部的所有内容。我们导入解析器、CORS 中间件、GraphQL 库项和`jsonwebtoken`模块。

接下来，我们通过调用`buildSchema`函数为我们的 GraphQL API 创建模式。为此，我们编写以下代码：

```js
...
const schema = buildSchema(`
  type Response {
    status: String
  }
  ...
  input Order {
    order_id: Int
    name: String
    address: String
    phone: String
    ordered_items: [ShopItem]
  }
  ...
  type Query {
    getShopItems: [ShopItemOutput],
    getOrders: [OrderOutput]
  }
  type Mutation {
    addShopItem(shopItem: ShopItem): Response
    removeShopItem(shopItemId: Int): Response
    addOrder(order: Order): Response
    removeOrder(orderId: Int): Response
    login(user: User): Token
  }
`);
...
```

完整的模式定义可以在[`github.com/PacktPublishing/-Vue.js-3-By-Example/blob/master/Chapter07/backend/app.js`](https://github.com/PacktPublishing/-Vue.js-3-By-Example/blob/master/Chapter07/backend/app.js)找到。

我们有`type`关键字来定义响应的数据类型，我们有`Response`和`Token`类型用作响应。`express-graphql`库将检查响应的结构是否符合数据类型中指定的内容，因此任何返回具有`Response`类型的查询或变异都应该有一个`status string`属性。这是可选的，因为字符串后面没有感叹号。

`input`关键字让我们定义了一个`input`类型。一个`input`类型用于指定请求有效载荷的数据结构。它们与`output`类型的定义方式相同，具有一系列属性，冒号后面是它们的数据类型。

我们可以将一个数据类型嵌套在另一个数据类型中，就像我们在`OrderOutput`类型的`ordered_items`属性中所做的那样。我们指定它包含一个具有`ShopItemOutput`数据类型的对象数组。同样，我们为`Order`数据类型中的`ordered_items`属性指定了类似的数据类型。方括号表示数据类型是一个数组。

`Query`和`Mutation`是特殊的数据类型，它们让我们在冒号前添加解析器名称，在冒号后添加输出的数据类型。`Query`类型指定查询，`Mutation`类型指定变异。

接下来，我们通过编写以下代码指定了带有所有解析器的`root`对象：

```js
const root = {
  ...shopItemResolvers,
  ...orderResolvers,
  ...authResolvers
}
```

我们只需将导入的所有解析器放入`root`对象中，并将所有条目展开到`root`对象中，以将它们合并为一个对象。

然后，我们添加`authMiddleware`以对一些 GraphQL 请求进行身份验证检查。为此，我们编写以下代码：

```js
const authMiddleware = (req, res, next) => {
  const { query } = req.body
  const token = req.get('authorization')
  const requiresAuth = query.includes('removeOrder') ||
    query.includes('removeShopItem') ||
    query.includes('addShopItem')
  if (requiresAuth) {
    try {
      jwt.verify(token, 'secret');
      next()
      return
    } catch (error) {
      res.status(401).json({})
      return
    }
  }
  next();
}
```

我们从 JSON 请求有效负载中获取`query`属性，以检查 GraphQL 请求调用的查询或变异。然后，我们使用`req.get`方法获取`authorization`标头。接下来，我们定义一个`requiresAuth`布尔变量，以检查客户端是否正在发出调用受限制的查询或变异的请求。

如果为`true`，我们调用`jwt.verify`以使用密钥验证令牌。如果有效，则调用`next`继续到`/graphql`端点。否则，我们返回`401`响应。如果`query`或`mutation`属性不需要身份验证，则只需调用`next`继续到`/graphql`端点。

### 添加中间件

接下来，我们添加了所有需要的中间件以启用跨域通信，并添加了`/graphql`端点以接受 GraphQL 请求。为此，我们编写以下代码：

```js
...
const app = express();
app.use(cors())
app.use(logger('dev'));
app.use(express.json());
app.use(express.urlencoded({ extended: false }));
app.use(cookieParser());
app.use(authMiddleware)
app.use('/graphql', graphqlHTTP({
  schema,
  rootValue: root,
  graphiql: true,
}));
...
```

我们编写以下代码行以启用跨域通信：

```js
app.use(cors())
```

以下代码行使我们能够接受 JSON 请求，这也是我们接受 GraphQL 请求所需的：

```js
app.use(express.json());
```

以下代码行将身份验证检查添加到受限制的 GraphQL 查询中：

```js
app.use(authMiddleware)
```

在以下代码块之前必须添加上述代码行：

```js
app.use('/graphql', graphqlHTTP({
  schema,
  rootValue: root,
  graphiql: true,
}));
```

这样，身份验证检查将在进行 GraphQL 请求之前完成。最后，以下代码块添加了一个`/graphql`端点，以便我们接受 GraphQL 请求：

```js
app.use('/graphql', graphqlHTTP({
  schema,
  rootValue: root,
  graphiql: true,
}));
```

`grapgqlHTTP`函数在传入一堆选项后返回一个中间件。我们为 GraphQL API 设置模式。`rootValue`属性具有包含所有解析器的对象。解析器名称应与`Query`和`Mutation`类型中指定的名称匹配。将`graphiql`属性设置为`true`，以便在浏览器中转到`/graphql`页面时可以使用 GraphiQL Web 应用程序。

要测试经过身份验证的端点，我们可以使用 Chrome 和 Firefox 提供的`ModHeader`扩展程序，将身份验证标头与令牌添加到请求标头中。然后，我们可以轻松测试经过身份验证的 GraphQL 请求。

注意：

该扩展可以从[`chrome.google.com/webstore/detail/modheader/idgpnmonknjnojddfkpgkljpfnnfcklj?hl=en`](https://chrome.google.com/webstore/detail/modheader/idgpnmonknjnojddfkpgkljpfnnfcklj?hl=en)下载到 Chromium 浏览器，[`addons.mozilla.org/en-CA/firefox/addon/modheader-firefox/?utm_source=addons.mozilla.org&utm_medium=referral&utm_content=search`](https://addons.mozilla.org/en-CA/firefox/addon/modheader-firefox/?utm_source=addons.mozilla.org&utm_medium=referral&utm_content=search)下载到 Firefox 浏览器。

以下屏幕截图显示了 GraphiQL 界面的外观。我们还有`ModHeader`扩展，让我们可以在屏幕右上角添加所需的标头以进行身份验证请求：

![图 7.1 – 带有 ModHeader 扩展的 GraphiQL](img/Figure_7.1_B14405.jpg)

图 7.1 – 带有 ModHeader 扩展的 GraphiQL

接下来，我们创建一个`db.sql`脚本，让我们可以通过编写以下代码创建我们需要使用的数据库：

```js
DROP TABLE IF EXISTS order_shop_items;
DROP TABLE IF EXISTS orders;
DROP TABLE IF EXISTS shop_items;
CREATE TABLE shop_items (
  shop_item_id INTEGER NOT NULL PRIMARY KEY,
  name TEXT NOT NULL,
  description TEXT NOT NULL,
  price NUMBER NOT NULL,
  image_url TEXT NOT NULL
);
CREATE TABLE orders (
  order_id INTEGER NOT NULL PRIMARY KEY,
  name TEXT NOT NULL,
  address TEXT NOT NULL,
  phone TEXT NOT NULL
);
CREATE TABLE order_shop_items (
  order_id INTEGER NOT NULL,
  shop_item_id INTEGER NOT NULL,
  FOREIGN KEY (order_id) REFERENCES orders(order_id)
  FOREIGN KEY (shop_item_id) REFERENCES 
   shop_items(shop_item_id)
);
```

我们创建了在解析器脚本中使用的表。`TEXT`让我们在列中存储文本；`INTEGER`让我们存储整数；`FOREIGN KEY`指定引用表和列后的外键；`NOT NULL`使列成为必需的；`DROP TABLE IF EXISTS`删除表（如果存在）；`CREATE TABLE`创建表；`PRIMARY KEY`指定主键列。

### 创建 SQLite 数据库

使用**DB Browser for SQLite**（**DB4S**）程序来创建和操作 SQLite 数据库，我们可以从[`sqlitebrowser.org/`](https://sqlitebrowser.org/)下载。该程序适用于 Windows、Mac 和 Linux。然后，我们可以点击**New Database**，将`db.sqlite`数据库保存在`backend`文件夹中，以便后端可以访问数据库。然后，在**Execute SQL**选项卡中，我们粘贴脚本以向数据库添加表。要将数据库更改写入磁盘，必须保存它们。要做到这一点，点击**File**菜单，然后点击**Write Changes**。我们也可以按下*Ctrl* + *S*键组合来保存更改。

最后，在`package.json`中，我们通过编写以下代码来更改`start`脚本：

```js
{
  ...
  "scripts": {
    "start": "nodemon ./bin/www"
  },
  ...
}
```

我们切换`nodemon`，这样当我们更改代码并保存时，应用程序将重新启动。我们运行`npm I –g nodemon`来全局安装`nodemon`。

现在我们已经完成了后端，可以继续进行前端，以便拥有完整的购物车系统。

# 创建管理员前端

现在我们已经完成了后端应用程序，我们可以继续处理前端。由于我们之前已经在`admin-frontend`文件夹中为管理员前端创建了 Vue 3 项目，我们只需安装我们需要的包，然后开始编写代码。我们需要`graphql-request` GraphQL 包和 GraphQL 客户端库，以及 VeeValidate、Vue Router、Axios 和 Yup 包。

要安装它们，我们在`admin-frontend`文件夹中运行以下命令：

```js
npm i vee-validate@next vue-router@4 yup graphql graphql-request
```

安装完包后，我们可以开始编写代码。

## 处理组件

首先，我们开始处理组件。在`components`文件夹中，我们通过编写以下代码将`TopBar`组件添加到`components/TopBar.vue`文件中，以容纳路由链接和**退出**按钮：

```js
<template>
  <p>
    <router-link to="/orders">Orders</router-link>
    <router-link to="/shop-items">Shop Items</router-link>
    <button @click="logOut">Log Out</button>
  </p>
</template>
<script>
export default {
  name: "TopBar",
  methods: {
    logOut() {
      localStorage.clear();
      this.$router.push("/");
    },
  },
};
</script>
<style scoped>
a {
  margin-right: 5px;
}
</style>
```

我们添加了 Vue Router `router-link`组件，让管理员用户点击它们以转到不同的页面。

**退出**按钮在点击时运行`logOut`方法，以清除本地存储并使用`this.$router.push`重定向回登录页面。`/`路径将映射到登录页面，我们稍后会看到。

接下来，在`src/plugins`文件夹中，我们添加`router.js`文件。为此，我们编写以下代码：

```js
import { createRouter, createWebHashHistory } from 'vue-router'
import Login from '@/views/Login'
import Orders from '@/views/Orders'
import ShopItems from '@/views/ShopItems'
const beforeEnter = (to, from, next) => {
  try {
    const token = localStorage.getItem('token')
    if (to.fullPath !== '/' && !token) {
      return next({ fullPath: '/' })
    }
    return next()
  } catch (error) {
    return next({ fullPath: '/' })
  }
}
const routes = [
  { path: '/', component: Login },
  { path: '/orders', component: Orders, beforeEnter },
  { path: '/shop-items', component: ShopItems, beforeEnter },
]
const router = createRouter({
  history: createWebHashHistory(),
  routes,
})
export default router
```

我们添加了`beforeEnter`路由守卫来检查身份验证令牌是否存储在本地存储中。如果已经存储，并且我们要前往经过身份验证的路由，那么我们通过调用`next`而不带参数来继续到页面。否则，我们通过调用带有`fullPath`属性设置为`'/'`的对象的`next`来重定向回登录页面。如果有任何错误，我们也会返回到登录页面。

接下来，我们有路由映射的`routes`数组。我们将路径映射到组件，这样当我们在浏览器中输入**统一资源定位符**（**URL**）或单击页面上的路由链接时，我们就会转到我们映射到的页面。我们在需要身份验证的路由上添加`beforeEnter`路由守卫。

然后，我们调用`createRouter`来创建`router`对象，并调用`createWebHashHistory`来使用哈希模式。使用哈希模式，主机名和 URL 的其余部分将由`#`符号分隔。我们还将`routes`数组添加到传递给`createRouter`的对象中，以添加路由映射。

然后，我们导出`router`对象，以便稍后将其添加到我们的应用程序中。

接下来，我们创建登录页面组件。为此，我们创建`views`文件夹，将`Login.vue`文件添加到其中，然后编写以下代码：

```js
<template>
  <h1>Admin Login</h1>
  <Form :validationSchema="schema" @submit="submitForm">
    <div>
      <label for="name">Username</label>
      <br />
      <Field name="username" type="text" 
        placeholder="Username" />
      <ErrorMessage name="username" />
    </div>
    <br />
    <div>
      <label for="password">Password</label>
      <br />
      <Field name="password" placeholder="Password" 
        type="password" />
      <ErrorMessage name="password" />
    </div>
    <input type="submit" />
  </Form>
</template>
```

我们将`Form`组件添加到`validationSchema`属性设置为`yup`模式。我们监听`submit`事件，当所有字段都有效时会触发该事件，然后点击**提交**按钮。`submitForm`方法将包含我们输入的表单字段值，`Field`组件让我们创建一个表单字段。

`ErrorMessage`显示带有表单字段的错误消息。如果`Field`和`ErrorMessage`的`name`属性值匹配，那么任何给定名称的字段的表单验证都将自动显示。`placeholder`属性让我们添加表单占位符，`type`属性设置`form`输入类型。

接下来，我们添加组件的脚本部分。为此，我们编写以下代码：

```js
<script>
import { GraphQLClient, gql } from "graphql-request";
import * as yup from "yup";
import { Form, Field, ErrorMessage } from "vee-validate";
const APIURL = "http://localhost:3000/graphql";
const graphQLClient = new GraphQLClient(APIURL, {
  headers: {
    authorization: "",
  },
});
const schema = yup.object({
  name: yup.string().required(),
  password: yup.string().required(),
});
...
</script>
```

我们使用`GraphQLClient`构造函数创建 GraphQL 客户端对象。它接受 GraphQL 端点 URL 和各种我们可以传递的选项。我们将在需要身份验证的组件中传递所需的请求标头。

`schema`变量保存了`yup`验证模式，其中包含`name`和`password`字段。两个字段都是字符串类型，都是必填的，如方法调用所示。属性名称必须与`Field`和`ErrorMessage`组件的`name`属性值匹配，以便触发字段的验证。

## 添加登录逻辑并进行第一个 GraphQL 请求

接下来，我们通过编写以下代码添加登录逻辑：

```js
<script>
...
export default {
  name: "Login",
  components: {
    Form,
    Field,
    ErrorMessage,
  },
  data() {
    return {
      schema,
    };
  },
...
        } = await graphQLClient.request(mutation, variables);
        localStorage.setItem("token", token);
        this.$router.push('/orders')
      } catch (error) {
        alert("Login failed");
      }
    },
  },
};
</script>
```

我们注册了从 VeeValidate 包导入的`Form`、`Field`和`ErrorMessage`组件。我们有`data`方法，它返回一个包含模式的对象，以便我们可以在模板中使用它。最后，我们有`submitForm`方法，用于从`Field`组件获取`username`和`password`值，并进行登录 mutation GraphQL 请求。

我们将`$username`和`$password`值传递到括号中，以便将它们传递到我们的 mutation 中。这些值将从我们传递给`graphQLClient.request`方法的`variables`对象中获取。如果请求成功，我们将从请求中获取令牌。一旦获取到令牌，我们将其放入`localStorage.setItem`中以将其放入本地存储。

`gql`标签是一个函数，它让我们将字符串转换为可以发送到服务器的查询 JSON 对象。

如果登录请求失败，我们会显示一个警报。以下截图显示了登录界面：

![图 7.2 - 管理员登录界面](img/Figure_7.2_B14405.jpg)

图 7.2 - 管理员登录界面

## 创建订单页面

接下来，我们通过创建`views/Orders.vue`文件来创建一个订单页面。为此，我们更新以下代码：

```js
<template>
  <TopBar />
  <h1>Orders</h1>
  <div v-for="order of orders" :key="order.order_id">
    <h2>Order ID: {{ order.order_id }}</h2>
    <p>Name: {{ order.name }}</p>
    <p>Address: {{ order.address }}</p>
    <p>Phone: {{ order.phone }}</p>
    <div>
      <h3>Ordered Items</h3>
      <div
        v-for="orderedItems of order.ordered_items"
        :key="orderedItems.shop_item_id"
      >
        <h4>Name: {{ orderedItems.name }}</h4>
        <p>Description: {{ orderedItems.description }}</p>
        <p>Price: ${{ orderedItems.price }}</p>
      </div>
    </div>
    <p>
      <b>Total: ${{ calcTotal(order.ordered_items) }}</b>
    </p>
    <button type="button" @click="deleteOrder(order)">Delete 
      Order</button>
  </div>
</template>
```

我们添加了`TopBar`并使用`v-for`循环遍历订单以渲染条目。我们还循环遍历`ordered_items`。我们使用`calcTotal`方法显示了订单物品的总价格。我们还有**删除订单**按钮，当我们点击它时会调用`deleteOrder`方法。必须指定`key`属性，以便 Vue 3 可以识别这些条目。

接下来，我们通过编写以下代码使用 GraphQL 客户端创建脚本：

```js
<script>
import { GraphQLClient, gql } from "graphql-request";
import TopBar from '@/components/TopBar'
const APIURL = "http://localhost:3000/graphql";
const graphQLClient = new GraphQLClient(APIURL, {
  headers: {
    authorization: localStorage.getItem("token"),
  },
});
...
</script>
```

这与登录页面不同，因为我们将授权标头设置为我们从本地存储中获取的令牌。接下来，我们通过编写以下代码创建组件对象：

```js
<script>
...
export default {
  name: "Orders",
  components: {
    TopBar
...
        {
          getOrders {
            order_id
            name
            address
            phone
            ordered_items {
              shop_item_id
              name
              description
              image_url
              price
            }
          }
        }
      `;
...
      await graphQLClient.request(mutation, variables);
      await this.getOrders();
    },
  },
};
</script>
```

我们使用`components`属性注册`TopBar`组件。我们有`data`方法返回一个带有`orders`响应式属性的对象。在`beforeMount`钩子中，当组件挂载时，我们调用`getOrders`方法获取订单。`calcTotal`方法通过使用`map`从所有`orderedItems`对象中获取价格，然后调用`reduce`将所有价格相加来计算所有订单物品的总价格。

`getOrders`方法发出 GraphQL 查询请求以获取所有订单。我们使用请求指定要获取的字段。我们还指定了要获取的嵌套对象的字段，因此我们也对`ordered_items`做同样的操作。只有指定的字段才会被返回。

然后，我们使用查询调用`graphQlClient.request`进行查询请求，并将返回的数据分配给`orders`响应式属性。

`deleteOrder`方法接受一个`order`对象，并向服务器发出`removeOrder`变更请求。`orderId`在变量中，因此将删除正确的订单。我们在删除订单后再次调用`getOrders`以获取最新的订单。

以下截图显示了管理员看到的订单页面：

![图 7.3 - 订单页面：管理员视图](img/Figure_7.3_B14405.jpg)

图 7.3 - 订单页面：管理员视图

现在我们已经添加了订单页面，接下来我们将添加一个页面，让管理员可以添加和删除他们想要在商店出售的物品。

## 添加和删除出售物品

接下来，我们添加一个商店物品页面，让我们可以添加和删除商店物品。为此，我们从模板开始。我们通过编写以下代码来渲染商店物品：

```js
<template>
  <TopBar />
  <h1>Shop Items</h1>
  <button @click="showDialog = true">Add Item to Shop</button>
  <div v-for="shopItem of shopItems" 
    :key="shopItem.shop_item_id">
    <h2>{{ shopItem.name }}</h2>
    <p>Description: {{ shopItem.description }}</p>
    <p>Price: ${{ shopItem.price }}</p>
    <img :src="shopItem.image_url" :alt="shopItem.name" />
    <br />
    <button type="button" @click="deleteItem(shopItem)">
      Delete Item from Shop
    </button>
  </div>
  ...
</template>
```

我们像之前一样添加`TopBar`组件，并渲染`shopItems`，就像我们对订单所做的那样。

接下来，我们添加一个带有 HTML 对话框元素的对话框，以便我们可以添加商店物品。为此，我们编写以下代码：

```js
<template>
  ...
  <dialog :open="showDialog" class="center">
    <h2>Add Item to Shop</h2>
    <Form :validationSchema="schema" @submit="submitForm">
      <div>
...
        <Field name="imageUrl" type="text" placeholder=" Image 
          URL" />
        <ErrorMessage name="imageUrl" />
      </div>
      <br />
      <div>
        <label for="price">Price</label>
        <br />
        <Field name="price" type="text" placeholder="Price" />
        <ErrorMessage name="price" />
      </div>
      <br />
      <input type="submit" />
      <button @click="showDialog = false" type="button">
        Cancel</button>
    </Form>
  </dialog>
</template>
```

我们设置`open`属性来控制对话框何时打开，并将类设置为`center`，以便我们可以应用样式将对话框居中并在页面的其余部分上方显示它。

在对话框中，我们以与登录页面相同的方式创建了表单。唯一的区别是表单中的字段。在表单底部，我们有一个**取消**按钮，将`showDialog`响应属性设置为`false`以关闭对话框，因为它被设置为`open`属性的值。

接下来，我们创建带有 GraphQL 客户端和表单验证模式的脚本（与之前一样），如下所示：

```js
<script>
import { GraphQLClient, gql } from "graphql-request";
import * as yup from "yup";
import TopBar from "@/components/TopBar";
import { Form, Field, ErrorMessage } from "vee-validate";
const APIURL = "http://localhost:3000/graphql";
const graphQLClient = new GraphQLClient(APIURL, {
  headers: {
    authorization: localStorage.getItem("token"),
  },
});
const schema = yup.object({
  name: yup.string().required(),
  description: yup.string().required(),
  imageUrl: yup.string().required(),
  price: yup.number().required().min(0),
});
...
</script>
```

然后，我们通过编写以下代码来添加`组件选项`对象：

```js
<script>
... 
export default {
  name: "ShopItems",
  components: {
    Form,
    Field,
    ErrorMessage,
    TopBar,
  },
  data() {
    return {
      shopItems: [],
      showDialog: false,
      schema,
    };
  },
  beforeMount() {
    this.getShopItems();
  },
  ...
};
</script>
```

我们注册组件并创建一个`data`方法来返回我们使用的响应属性。`beforeMount`钩子调用`getShopItems`方法以从 API 获取商店物品。

接下来，我们通过编写以下代码来添加`getShopItems`方法：

```js
<script>
... 
export default {
  ...
  methods: {
    async getShopItems() {
      const query = gql`
        {
          getShopItems {
            shop_item_id
            name
            description
            image_url
            price
          }
        }
      `;
      const { getShopItems: data } = await 
        graphQLClient.request(query);
      this.shopItems = data;
    },
    ...
  },
};
</script>
```

我们只需发出`getShopItems`查询请求，以获取带有大括号中字段的数据。

接下来，我们通过编写以下代码来添加`submitForm`方法，以发出变更请求以添加商店物品条目：

```js
<script>
... 
export default {
  ...
  methods: {
    ...
    async submitForm({ name, description, imageUrl, price: 
      oldPrice }) {
      const mutation = gql`
        mutation addShopItem(
          $name: String
          $description: String
          $image_url: String
          $price: Float
        ) {
...
        description,
        image_url: imageUrl,
        price: +oldPrice,
      };
      await graphQLClient.request(mutation, variables);
      this.showDialog = false;
      await this.getShopItems();
    },
    ...
  },
};
</script>
```

我们通过解构参数中的对象来获取所有表单字段的值，然后调用`graphQLClient.request`以使用从参数的解构属性中设置的变量进行请求。我们将`price`转换为数字，因为根据我们在后端创建的模式，`price`应该是浮点数。

请求完成后，我们将`showDialog`设置为`false`以关闭对话框，并再次调用`getShopItems`以获取商店物品。

我们将添加的最后一个方法是`deleteItem`方法。该方法的代码可以在以下代码片段中看到：

```js
<script>
... 
export default {
  ...
  methods: {
    ...
    async deleteItem({ shop_item_id: shopItemId }) {
      const mutation = gql`
        mutation removeShopItem($shopItemId: Int) {
          removeShopItem(shopItemId: $shopItemId) {
            status
          }
        }
      `;
      const variables = {
        shopItemId,
      };
      await graphQLClient.request(mutation, variables);
      await this.getShopItems();
    },
    ...
  },
};
</script>
```

我们发出`removeShopItem`变更请求，以删除商店物品条目。请求完成后，我们再次调用`getShopItems`以获取最新数据。

可以在以下截图中看到管理员视图的商店物品页面：

![图 7.4 – 商店物品页面：管理员视图](img/Figure_7.4_B14405.jpg)

图 7.4 – 商店物品页面：管理员视图

在`src/App.vue`中，我们编写以下代码来添加`router-view`组件以显示路由组件内容：

```js
<template>
  <router-view></router-view>
</template>
<script>
export default {
  name: "App",
};
</script>
```

在`src/main.js`中，我们编写以下代码来将路由器添加到我们的应用程序中：

```js
import { createApp } from 'vue'
import App from './App.vue'
import router from '@/plugins/router'
const app = createApp(App)
app.use(router)
app.mount('#app')
```

最后，在`package.json`中，我们将服务器脚本更改为从不同端口提供应用程序，以避免与前端发生冲突。为此，我们编写以下代码：

```js
{
  ...
  "scripts": {
    "serve": "vue-cli-service serve --port 8090",
    ...
  },
  ...
}
```

我们现在已经完成了管理员前端，将继续进行这个项目的最后部分，即为客户创建一个前端，以便他们可以订购商品。

# 创建客户前端

现在我们已经完成了管理员前端，通过创建客户前端来完成本章的项目。这与管理员前端类似，只是不需要进行身份验证。

我们首先安装与管理员前端相同的包。因此，我们转到`frontend`文件夹并运行以下命令来安装所有包：

```js
npm i vee-validate@next vue-router@4 yup vuex@4 vuex-persistedstate@ ⁴.0.0-beta.3 graphql graphql-request
```

我们需要使用`Vuex-Persistedstate`插件来存储购物车项目。其余的包与管理员前端相同。

## 创建插件文件夹

我们在`src`文件夹中创建一个`plugins`文件夹，并通过在该文件夹中创建`router.js`文件并编写以下代码来添加路由：

```js
import { createRouter, createWebHashHistory } from 'vue-router'
import Shop from '@/views/Shop'
import OrderForm from '@/views/OrderForm'
import Success from '@/views/Success'
const routes = [
  { path: '/', component: Shop },
  { path: '/order-form', component: OrderForm },
  { path: '/success', component: Success },
]
const router = createRouter({
  history: createWebHashHistory(),
  routes,
})
```

接下来，我们通过创建`src/plugins/vuex.js`文件来创建我们的 Vuex 存储，然后编写以下代码：

```js
import { createStore } from "vuex";
import createPersistedState from "vuex-persistedstate";
const store = createStore({
  state() {
    return {
      cartItems: []
    }
  },
  getters: {
    cartItemsAdded(state) {
      return state.cartItems
    }
  },
  mutations: {
    addCartItem(state, cartItem) {
      const cartItemIds = state.cartItems.map(c => 
        c.cartItemId).filter(id => typeof id === 'number')
      state.cartItems.push({
...
      state.cartItems = []
    }
  },
  plugins: [createPersistedState({
    key: 'cart'
  })],
});
export default store
```

我们调用`createStore`来创建 Vuex 存储。在传递给`createStore`的对象中，我们有`state`方法来返回初始化为数组的`cartItems`状态。`getters`属性有一个对象，其中包含`cartItemsAdded`方法来返回`cartItems`状态值。

在`mutations`属性对象中，我们有`addCartItem`方法来调用`state.cartItems.push`将`cartItem`值添加到`cartItems`状态中。我们使用`map`和`filter`方法获取现有的购物车项目 ID。我们只想要数字的 ID。新购物车项目的 ID 将是`cartItemIds`数组中最高的 ID 加上`1`。

`removeCartItem`方法让我们调用`splice`通过索引删除购物车项目，`clearCart`将`cartItems`状态重置为空数组。

最后，我们将`plugins`属性设置为一个具有`createPersistedState`函数的对象，以创建一个`Vuex-Persistedstate`插件来将`cartItems`状态存储到本地存储中。`key`值是存储`cartItem`值的键。然后，我们导出存储，以便稍后将其添加到我们的应用程序中。

## 创建订单表单页面

接下来，我们创建一个订单表单页面。这个页面有一个表单，让顾客输入个人信息并编辑购物车。为了创建它，如果还没有，我们创建一个`src/views`文件夹，然后创建一个`OrderForm.vue`组件文件。我们首先编写以下模板代码：

```js
<template>
  <h1>Order Form</h1>
  <div v-for="(cartItem, index) of cartItemsAdded" 
    :key="cartItem.cartItemId">
    <h2>{{ cartItem.name }}</h2>
    <p>Description: {{ cartItem.description }}</p>
    <p>Price: ${{ cartItem.price }}</p>
    <br />
...
      <Field name="phone" type="text" placeholder="Phone" />
      <ErrorMessage name="phone" />
    </div>
    <br />
    <div>
      <label for="address">Address</label>
      <br />
      <Field name="address" type="text" placeholder="Address" 
         />
      <ErrorMessage name="address" />
    </div>
    <br />
    <input type="submit" />
  </Form>
</template>
```

我们有类似于管理员前端的表单。我们使用来自 VeeValidate 的相同`Form`、`Field`和`ErrorMessage`组件。

我们使用`v-for`循环遍历购物车项目，将它们渲染到屏幕上。它们通过`Vuex-Persistedstate`和`cartItemsAdded` getter 从本地存储中检索出来。

接下来，我们通过编写以下代码以相同的方式创建脚本：

```js
<script>
import { GraphQLClient, gql } from "graphql-request";
import { mapMutations, mapGetters } from "vuex";
import { Form, Field, ErrorMessage } from "vee-validate";
import * as yup from "yup";
...
export default {
  name: "OrderForm",
  data() {
    return {
      schema,
    };
  },
  components: {
    Form,
    Field,
    ErrorMessage,
  },
  computed: {
    ...mapGetters(["cartItemsAdded"]),
  },
  ...
};
</script>
```

我们创建了 GraphQL 客户端和验证模式，并且我们以与管理员前端的商店项目页面相同的方式注册组件。唯一的新事物是调用`mapGetters`方法，将 Vuex getters 作为组件的计算属性添加进去。我们只需传入一个包含 getter 名称的字符串数组，将计算属性映射到这些 getter。接下来，我们通过编写以下代码添加方法：

```js
<script>
...
export default {  
  ...
  methods: {
    async submitOrder({ name, phone, address }) {
      const mutation = gql`
        mutation addOrder(
          $name: String
          $phone: String
          $address: String
          $ordered_items: [ShopItem]
...
            shop_item_id,
            name,
            description,
            image_url,
            price,,
          })
        ),
      };
      await graphQLClient.request(mutation, variables);
      this.clearCart();
      this.$router.push("/success");
    },
    ...mapMutations(["addCartItem", "removeCartItem", 
        "clearCart"]),
  },
};
</script>
```

我们有一个`submitOrder`方法，从订单表单中获取输入的数据，并向服务器发出`addOrder`变异请求。在`variables`对象中，我们需要从每个`ordered_items`对象中删除`cartItemId`，以使其与我们在后端创建的`ShopItem`模式匹配。我们不能在发送到服务器的对象中有不包含在模式中的额外属性。

一旦请求成功，我们调用`clearCart`来清空购物车，然后调用`thus.$router.push`去到成功页面。`mapMutation`方法将变异映射到我们组件中的方法。`clearCart`方法与`clearCart` Vuex 存储变异相同。

以下截图显示了订单表单的管理员视图：

![图 7.5 – 订单表单：管理员视图](img/Figure_7.5_B14405.jpg)

图 7.5 – 订单表单：管理员视图

接下来，我们通过编写以下代码创建一个`src/views/Shop.vue`文件：

```js
<template>
  <h1>Shop</h1>
  <div>
    <router-link to="/order-form">Check Out</router-link>
  </div>
  <button type="button" @click="clearCart()">Clear Shopping 
     Cart</button>
  <p>{{ cartItemsAdded.length }} item(s) added to cart.</p>
  <div v-for="shopItem of shopItems" :key="shopItem.
     shop_item_id">
    <h2>{{ shopItem.name }}</h2>
    <p>Description: {{ shopItem.description }}</p>
    <p>Price: ${{ shopItem.price }}</p>
    <img :src="shopItem.image_url" :alt="shopItem.name" />
    <br />
    <button type="button" @click="addCartItem(shopItem)">Add
       to Cart</button>
  </div>
</template>
```

我们使用`v-for`渲染商店项目，就像我们对其他组件所做的那样。我们还有一个`router-link`组件，在页面上渲染一个链接。

我们使用`cartItemsAdded` getter 显示添加的购物车项目数量。当我们点击**清空购物车**时，将调用`clearCart` Vuex 变异方法。接下来，我们通过编写以下代码为组件添加脚本：

```js
<script>
import { GraphQLClient, gql } from "graphql-request";
import { mapMutations, mapGetters } from "vuex";
const APIURL = "http://localhost:3000/graphql";
const graphQLClient = new GraphQLClient(APIURL);
...
    async getShopItems() {
      const query = gql`
        {
          getShopItems {
            shop_item_id
            name
            description
            image_url
            price
          }
        }
      `;
      const { getShopItems: data } = await 
        graphQLClient.request(query);
      this.shopItems = data;
    },
    ...mapMutations(["addCartItem", "clearCart"]),
  },
};
</script>
```

我们以相同的方式创建 GraphQL 客户端。在组件中，我们在`beforeMount`钩子中调用`getShopItems`来获取购物车商品。我们还调用`mapMutations`将我们需要的 Vuex 变异映射到组件中的方法。

最后，我们通过编写以下代码将`img`元素缩小到`100px`宽度：

```js
<style scoped>
img {
  width: 100px;
}
</style>
```

接下来，我们通过创建`src/views/Success.vue`文件并编写以下代码来创建一个订单成功页面：

```js
<template>
  <div>
    <h1>Order Successful</h1>
    <router-link to="/">Go Back to Shop</router-link>
  </div>
</template>
<script>
export default {
  name: "Success",
};
</script>
```

订单成功页面只有一些文本和一个链接，可以返回到商店的主页。

接下来，在`src/App.vue`中，我们编写以下代码来添加`router-view`组件以显示路由页面：

```js
<template>
  <router-view></router-view>
</template>
<script>
export default {
  name: "App",
};
</script>
```

在`src/main.js`中，我们添加以下代码来将路由器和 Vuex 存储添加到我们的应用程序：

```js
import { createApp } from 'vue'
import App from './App.vue'
import router from '@/plugins/router'
import store from '@/plugins/vuex'
const app = createApp(App)
app.use(router)
app.use(store)
app.mount('#app')
```

最后，我们通过编写以下代码来更改应用项目的端口：

```js
{
  ...
  "scripts": {
    "serve": "vue-cli-service serve --port 8091",
    ...
  },
  ...
}
```

我们的项目现在已经完成。

我们可以用`npm run serve`来运行前端项目，用`npm start`来运行后端项目。

通过开发购物车项目，我们学会了如何创建 GraphQL API，这些 API 是可以通过查询和变异处理 GraphQL 指令的 JSON API。

# 总结

我们可以很容易地使用 Express 和`express-graphql`库创建 GraphQL API。为了更轻松地进行 GraphQL HTTP 请求，我们使用了在浏览器中工作的`graphql-request` JavaScript GraphQL 客户端。这使我们能够轻松设置请求选项，如标头、要进行的查询和与查询一起使用的变量。

我们使用`graphql-request` GraphQL 客户端来向后端发出请求，而不是使用常规的 HTTP 客户端。`graphql-request`库让我们比使用常规的 HTTP 客户端更轻松地进行 GraphQL HTTP 请求。借助它，我们可以轻松地传递带有变量的 GraphQL 查询和变异。

使用映射到解析器函数的模式创建了 GraphQL API。模式让我们定义输入和输出数据的所有数据类型，这样我们就不必猜测要发送哪些数据。如果发送任何无效数据，我们将收到一个错误，告诉我们请求出了什么问题。我们还必须指定我们想要在 GraphQL 查询中返回的数据字段，只有我们指定的字段才会返回。这使我们能够返回我们需要使用的数据，使其更加高效。

此外，我们可以在向`/graphql`端点发出请求之前进行常规令牌检查，为 GraphQL API 请求添加身份验证。

我们可以使用 GraphiQL 交互式沙盒轻松测试 GraphQL 请求，让我们可以发出想要的请求。要测试经过身份验证的请求，我们可以使用`ModHeader`扩展来设置标头，以便我们可以成功地进行经过身份验证的请求。

在下一章中，我们将看看如何使用 Laravel 和 Vue 3 创建实时聊天应用程序。
