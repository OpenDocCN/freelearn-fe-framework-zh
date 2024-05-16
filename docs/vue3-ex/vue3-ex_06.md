# 第六章：*第六章*：使用 PrimeVue UI 框架构建度假预订应用

在*第五章*中，*使用 Ionic 构建多用途计算器移动应用*，我们使用 Ionic 移动应用框架构建了一个移动应用，该框架构建在 Vue.js 之上。然而，到目前为止，在本书中，我们尚未使用基于 Vue.js 的 UI 库或框架构建任何 Web 应用程序。此外，我们还没有构建具有自己后端的任何内容。大多数系统肯定需要后端，因为我们需要一个地方来存储我们的数据、验证用户、运行后台任务等。在本章中，我们将使用 PrimeVue UI 框架构建度假预订应用。

我们将使用 Vue 3 前端进行管理，另一个前端供用户添加他们的预订。我们还将包括一个简单的后端，以在管理员执行只有他们才能完成的任务之前对其进行身份验证。为了尽可能简化项目，普通公众的前端不需要身份验证。

在本章中，我们将专注于以下主题：

+   使用 PrimeVue UI 框架构建前端

+   使用 Express 构建简单的后端进行身份验证

+   使用 SQLite 在后端持久化数据

+   在前端使用 Vue Router 进行身份验证

+   使用 Vee-Validate 和 Yup 进行表单验证

# 技术要求

本章的代码位于[`github.com/PacktPublishing/-Vue.js-3-By-Example/tree/master/Chapter06`](https://github.com/PacktPublishing/-Vue.js-3-By-Example/tree/master/Chapter06)。

**PrimeVue**是基于 Vue 3 的 UI 框架。这意味着我们可以将其用于 Vue 3 应用程序。基于 Vue 2 的框架无法用于 Vue 3 应用程序，因为 API 已经经历了重大变化。Vue 3 的底层代码也与 Vue 2 不同。PrimeVue 包括许多我们在 Web 应用程序中使用的常见组件，如文本输入、按钮、菜单栏、表格等。它在包含的内容方面非常全面。此外，它还提供了主题样式。这意味着我们可以立即使用内置组件。由于 PrimeVue 是为 Vue 3 而制作的，我们可以简单地注册组件、导入 CSS，并在我们的代码中使用组件。我们还可以根据需要为给定组件在本地或全局注册它们。

## 理解 PrimeVue

PrimeVue 带有输入框和各种文本的样式，例如验证错误和按钮。它还带有 flexbox 助手，我们可以使用它们轻松设置组件的间距。这非常有用，因为我们可以简单地使用 PrimeVue 提供的 CSS 类来设置组件的位置和间距。

到目前为止，在本书中，我们还没有使用任何库来使表单验证更加方便。表单验证是大多数 Web 应用程序中我们必须经常做的事情。

# 理解 Vee-Validate 和 Yup

**Vee-Validate 4**是与 Vue 3 兼容的表单验证库。借助它，我们可以添加组件，以在前端对我们的表单进行验证。我们将与 Yup 数据验证库一起使用它，以创建表单验证模式，Vee-Validate 可以用于验证。通过`Form`组件，我们可以添加一个表单，使我们能够使用 Vee-Validate 4 进行表单验证。然后，`Field`组件可以用于验证 PrimeVue 提供的表单控件组件。它通过包装它们并将`Field`组件提供的插槽属性作为输入组件的属性传递来实现这一点。

Yup 库将与 Vee-Validate 一起使用，让我们可以轻松验证表单值，而无需从头开始编写所有代码。它允许我们创建表单验证模式对象，我们可以将其传递到使用 Vee-Validate 创建的表单中，以添加表单验证。

# 理解 Express

为了创建一个简单的后端来存储数据，我们使用**Express 框架**。这是一个非常简单的 Node.js 后端框架，可以让我们快速创建一个简单的后端。为了存储数据，我们将使用 SQLite 数据库来保持项目简单。我们将使用 Express 框架创建一个 API，以便前端可以向其发出 HTTP 请求。我们通过公开 API 端点来让他们发出请求，前端可以通过添加路由来使用它。每个路由都有一个处理程序函数，用于处理前端提交的数据。我们从前端发出的 HTTP 请求中获取请求数据，其中包括头部和主体，并在路由处理程序中使用它们来获取和存储我们想要的数据。

# 连接前端和后端

为了使前端应用与后端应用通信，我们需要在后端启用跨域通信，以便来自前端的流量可以通过到达后端。这可以通过我们将添加到 Express 应用程序的**跨域资源共享**（**CORS**）中间件轻松完成。

为了使用 SQLite 数据库，我们使用`sqlite3`库，它允许我们在 Node.js 应用程序中操作 SQLite 数据库。我们可以进行查询并运行 SQL 命令来插入或删除数据库中的数据。

此外，我们将为管理员前端设置简单的身份验证。我们将检查管理员登录的用户名和密码，如果有效，我们可以发放一个令牌并将其发送到前端。然后，前端将使用存储在标头中的令牌来检查请求是否可以从前端发出。我们仅为*仅限管理员*路由添加身份验证，因此我们只需要在加载需要身份验证的路由之前检查令牌。

为了创建和检查令牌，我们使用`jsonwebtoken`库。这允许我们创建一个令牌并用秘密字符串签名。它还使我们能够使用秘密检查令牌是否有效。我们将`jsonwebtoken`库放在一个中间件中，在路由处理程序之前运行以进行检查。

如果令牌有效，则调用函数继续到路由处理程序。否则，我们向客户端发送`401`状态。

现在，我们将构建项目。

# 创建度假预订项目

创建度假预订应用程序，我们需要为前端、管理员前端和后端创建子项目。我们使用 Vue CLI 来创建`frontend`和`admin frontend`项目脚手架。我们使用`Express Generator`全局包来创建`backend`文件夹。

按照以下步骤设置项目：

1.  首先，创建`travel-booking-app`文件夹来容纳所有项目。

1.  接下来，在主文件夹内创建`admin-frontend`、`frontend`和`backend`文件夹。

1.  进入`admin-frontend`文件夹并运行以下命令：

```js
npx vue create
```

这将在`admin-frontend`文件夹内添加 Vue 项目的脚手架代码。

1.  如果要在当前文件夹中创建项目，请选择*Y*。然后，在要求选择项目的 Vue 版本时，选择`Vue 3`。

同样，以相同的方式在`frontend`文件夹中运行 Vue CLI。

1.  要创建一个 Express 项目，请运行 Express 应用程序生成器应用程序。要做到这一点，请进入`backend`文件夹并运行以下命令：

```js
npx express-generator
```

上述命令将在`backend`文件夹中添加我们项目所需的所有文件。如果出现错误，请尝试以“管理员”身份运行`express-generator`。

现在我们已经创建了项目的脚手架文件和文件夹，我们准备开始在后端上工作。

## 创建后端

现在我们已经创建了带有脚手架代码的项目文件夹，我们可以开始编写项目代码了。我们将从后端开始，因为我们需要它用于两个前端。

要开始，让我们添加一些需要用来操作 SQLite 数据库并向我们的应用程序添加身份验证的库。此外，我们需要一个库来向我们的应用程序添加 CORS。

要安装它们，请运行以下命令：

```js
npm i cors jsonwebtoken sqlite3
```

安装完这些包后，我们就可以开始编写代码了。

### 添加身份验证中间件

首先，我们添加我们将用来检查令牌的中间件。我们可以很容易地使用 jsonwebtoken 库来做到这一点。它具有 verify 方法来检查令牌。

要添加中间件，请在后端文件夹中创建`middlewares`文件夹，然后添加`verify-token.js`文件。接下来，添加以下中间件的代码：

```js
const jwt = require('jsonwebtoken');
module.exports = (req, res, next) => {
  const token = req.get('x-token')
  try {
    jwt.verify(token, 'secret');
    next()
  } catch (err) {
    res.status(401)
  }
}
```

在这里，我们使用`req.get`方法获取`x-token`请求头。然后，我们使用返回的`token`和`secret`调用`jwt.verify`来验证令牌是否有效。然后，如果有效，我们调用`next`。如果无效，将抛出错误，并运行`catch`块。运行`res.status`与`401`来将`401`响应返回给前端，因为在这种情况下令牌无效。

`module.exports`属性设置为`middleware`函数作为值，我们正在导出它。导出函数使其在我们后端应用程序的其他模块中可用。

### 添加处理请求的路由

接下来，我们将添加带有路由的`router`模块。首先，添加用于操作预订的路由。要做到这一点，请将`bookings.js`文件添加到`routes`文件夹中。在文件中，编写以下代码：

```js
const express = require('express');
const sqlite3 = require('sqlite3').verbose();
const router = express.Router();
const verifyToken = require('../middlewares/verify-token')
router.get('/', (req, res, next) => {
  const db = new sqlite3.Database('./db.sqlite');
  db.serialize(() => {
    db.all(`
      SELECT
        bookings.*,
        catalog_items.name AS catalog_item_name,
        catalog_items.description AS catalog_item_description
      FROM bookings
      INNER JOIN catalog_items ON catalog_items.id = 
        bookings.catalog_item_id
    `,
      [],
      (err, rows = []) => {
        res.json(rows)
      });
  })
  db.close();
});
...
```

在这里，我们导入所需的模块，包括我们刚刚创建的“verify-token 中间件”文件。

`router.get`方法允许我们创建一个 GET 请求 API 端点。路径在第一个参数中。它是路由的路径，相对于路由器的路径。因此，路由器的路由路径是第一个部分，而`router.get`的第一个参数中的路径形成 URL 的其余部分。

`router.get`方法的第二个参数是路由处理程序。`req`参数是一个包含请求数据的对象。`res`参数是一个对象，让我们可以向前端发送各种类型的响应。我们使用`sqlite3.Database`构造函数获取数据库，并指定数据库文件的路径。

接下来，我们调用`db.serialize`函数，以便可以按顺序运行回调中的代码。

`db.all`方法获取查询返回的所有结果。字符串是从 bookings 表中检索所有数据的 SQL 命令，我们将使用我们自己的 SQL 代码创建它。`bookings`表与`catalog_items`表连接，以便`vacation`套餐数据与预订相关联。

`db.all`的第二个参数是我们想要传递的额外参数，我们通过传入一个空数组来忽略它们。然后，在最后一个参数中，我们有一个带有`err`参数的函数，其中包含对象形式的错误。`rows`参数包含查询结果。在回调中，我们调用`res.json`返回带有`rows`参数数据的 JSON 响应。

然后，我们调用`db.close`来在所需操作完成后关闭数据库连接。

接下来，我们将创建一个 POST 路由。这将允许我们运行`INSERT` SQL 命令将条目插入到 bookings 表中。添加以下代码：

```js
...
router.post('/', (req, res) => {
  const db = new sqlite3.Database('./db.sqlite');
  const { catalogItemId, name, address, startDate, endDate } = 
   req.body
  db.serialize(() => {
    const stmt = db.prepare(`
      INSERT INTO bookings (
        catalog_item_id,
        name,
        address,
        start_date,
        end_date
      ) VALUES (?, ?, ?, ?, ?)
    `);
    stmt.run(catalogItemId, name, address, startDate, endDate)
    stmt.finalize();
    res.json({ catalogItemId, name, address, startDate,
      endDate })
  })
  db.close();
});
...
```

在这里，我们使用`req.body`属性获取 body 属性。我们获取所有要插入条目的属性。接下来，我们使用`INSERT`语句创建一个预处理语句。最后的值是问号；这意味着它们是占位符，我们可以在运行`stmt.run`方法时放入我们自己的值。

准备好的语句很有用，因为它们使我们能够安全地传递值到我们的 SQL 命令中。这些值都经过了净化，以便恶意代码无法在代码中运行。我们运行`stmt.run`来运行带有我们想要替换占位符的值的准备好的语句。然后，我们调用`stmt.finalize`来通过写入数据来完成操作。接下来，我们调用`res.json`将 JSON 响应返回给前端作为响应。然后，我们调用`db.close`再次关闭数据库连接。

接下来，我们将使用`router.delete`方法创建一个`DELETE`端点。为此，请编写以下代码：

```js
...
router.delete('/:id', verifyToken, (req, res) => {
  const db = new sqlite3.Database('./db.sqlite');
  const { id } = req.params
  db.serialize(() => {
    const stmt = db.prepare("DELETE FROM bookings WHERE id = (?)");
    stmt.run(id)
    stmt.finalize();
    res.json({ status: 'success' })
  })
  db.close();
});
...
```

在这里，我们有`/:id`路径。`：id`是路由的 URL 参数占位符。我们还有`verifyToken`中间件，我们在`booking.js`文件的顶部导入了它。我们可以使用它来在继续运行路由处理程序之前验证令牌。这意味着这个路由是一个需要在 API 端点调用的标头中需要令牌的经过身份验证的路由。

在路由处理程序中，我们从`req.params`属性中获取`id` URL 参数。然后，我们调用`db.serialize`，就像我们在之前的路由中所做的那样。在回调中，我们有准备好的语句，因此我们可以使用`DELETE` SQL 命令和我们在`stmt.run`方法中设置的`id`值。然后，我们调用`stmt.finalize`，`res.json`和`db.close`，就像我们在其他路由中所做的那样。

最后，在`booking.js`文件的末尾，让我们添加以下内容：

```js
module.exports= = router;
```

添加上述语句使我们能够将其导入到另一个文件中以注册路由。注册路由将使其可访问。

接下来，我们将在`routes`文件夹中创建`catalog.js`文件。这个文件是一个具有 API 端点的`router`模块，用于添加我们的度假套餐。首先，我们开始如下：

```js
const express = require('express');
const router = express.Router();
const sqlite3 = require('sqlite3').verbose();
const verifyToken = require('../middlewares/verify-token')
router.get('/', (req, res,) => {
  const db = new sqlite3.Database('./db.sqlite');
  db.serialize(() => {
    db.all("SELECT * FROM catalog_items", [], (err, rows = []) 
      => {
      res.json(rows)
    });
  })
  db.close();
});
...
```

这几乎与`bookings.js`中的`GET`路由相同；但是，在这里，我们从`catalog_items`表中检索所有项目。

接下来，让我们添加一个`POST`路由来将条目添加到`catalog_items`表中。编写以下代码：

```js
...
router.post('/', verifyToken, (req, res,) => {
  const { name, description, imageUrl } = req.body
  const db = new sqlite3.Database('./db.sqlite');
  db.serialize(() => {
    const stmt = db.prepare(`
    INSERT INTO catalog_items (
      name,
      description,
      image_url
    ) VALUES (?, ?, ?)
  `
    );
    stmt.run(name, description, imageUrl)
    stmt.finalize();
    res.json({ status: 'success' })
  })
  db.close();
});
...
```

在这里，我们在第二个参数中有`verifyToken`来检查此路由中的令牌，然后再运行第三个参数中的路由处理程序。

接下来，我们添加一个路由，使我们能够删除`catalog_items`条目。我们使用以下代码来实现这一点：

```js
...
router.delete('/:id', verifyToken, (req, res,) => {
  const { id } = req.params
  const db = new sqlite3.Database('./db.sqlite');
  db.serialize(() => {
    const stmt = db.prepare("DELETE FROM catalog_items WHERE 
      id = (?)");
stmt.run(id)
stmt.finalize();
res.json({status:'success'})
db.close();
});
...
```

最后，我们导出路由：

```js
module.exports = router;
```

这个模块与`booking.js`并没有太大的不同。

接下来，我们删除`routes/users.js`文件的内容，或者如果不存在，则创建它。然后，我们添加以下代码：

```js
const express = require('express');
const jwt = require('jsonwebtoken');
const router = express.Router();
router.post('/login', (req, res) => {
  const { username, password } = req.body
  if (username === 'admin' && password === 'password') {
    res.json({ token: jwt.sign({ username }, 'secret') })
  }
  res.status(401)
});
module.exports= = router;
```

这是我们检查管理员用户的`username`和`password`是否有效的地方。我们只有一个用户需要检查，以保持项目简单。我们从`req.body`对象中获取`username`和`password`，该对象具有 JSON 请求对象。然后，我们使用`if`语句检查`username`和`password`，如果`if`中的表达式返回`true`，我们调用`jwt.sign`使用第一个参数中的令牌数据和第二个参数中的`secret`创建令牌。然后，我们使用`res.json`返回带有认证令牌的响应。

否则，我们调用`res.status`和`401`返回`401`响应，因为`username`或`password`无效。

接下来，在`app.js`中注册我们的`router`模块和全局中间件。为此，我们编写以下代码：

```js
...
const indexRouter = require('./routes/index');
const usersRouter = require('./routes/users');
const catalogRouter = require('./routes/catalog');
const bookingsRouter = require('./routes/bookings');
const app = express();
const cors = require('cors')
...
app.use('/users', usersRouter);
app.use('/catalog', catalogRouter);
app.use('/bookings', bookingsRouter);
```

我们导入了之前使用`require`导出的`router`模块。然后，我们导入了`cors`模块。

```js
const cors = require('cors')
```

我们调用`app.use`添加`cors`中间件，然后添加`router`模块。在最后三行中，我们将`path`作为第一个参数，`router`模块作为第二个参数。这使我们能够访问之前创建的端点。使用`cors`模块，我们可以在 Express 应用程序中启用跨域通信。

接下来，让我们创建我们的 SQL 脚本，以便我们可以轻松地删除和创建表。为此，在`backend`文件夹中创建`db.sql`文件，并编写以下代码：

```js
DROP TABLE IF EXISTS bookings;
DROP TABLE IF EXISTS catalog_items;
CREATE TABLE catalog_items (
  id INTEGER NOT NULL PRIMARY KEY,
  name TEXT NOT NULL,
  description TEXT NOT NULL,
  image_url TEXT NOT NULL
);
CREATE TABLE bookings (
  id INTEGER NOT NULL PRIMARY KEY,
  catalog_item_id INTEGER NOT NULL,
  name TEXT NOT NULL,
  address TEXT NOT NULL,
  start_date TEXT NOT NULL,
  end_date TEXT NOT NULL,
  FOREIGN KEY (catalog_item_id) REFERENCES catalog_items(id)
);
```

在这里，我们创建了`bookings`和`catalog_items`表。每个表都有各种字段。`TEXT`创建文本列。`NOT NULL`使列不可为空。`PRIMARY KEY`表示该列是主键列。`FOREIGN KEY`表示一个列是另一个列的外键。

我们可以通过安装 SQLite 程序的 DB 浏览器来运行 SQL 代码，可以在[`sqlitebrowser.org/`](https://sqlitebrowser.org/)下载，然后在`backend`文件夹中创建`db.sqlite`。然后，我们可以转到**执行 SQL**选项卡，并将代码粘贴到文本输入中。之后，我们可以选择所有文本，按下*F5*来运行代码。这将删除任何现有的`bookings`和`catalog_items`表，并重新创建它们。要将更改写入磁盘，您必须保存它们。要做到这一点，点击**文件**菜单，然后点击**写入更改**。我们也可以按下*Ctrl + S*键组合来保存更改。

最后，为了使我们的应用程序在更改代码时自动运行和重新启动，我们可以全局安装`nodemon`包。要做到这一点，请运行以下命令：

```js
npm i -g nodemon
```

然后，在`package.json`文件中，将`script.start`属性的值更改为以下代码：

```js
{
  ...
  "scripts": {
    "start": "nodemon ./bin/www"
  },
  ...
}
```

我们可以使用`nodemon`来运行`npm start`，而不是常规的 node 可执行文件，这意味着当我们更改任何代码文件并保存时，应用程序将自动重新启动。

现在我们已经为前端创建了一个基本的后端来消耗，我们可以继续使用 PrimeVue 和 Vue 3 创建我们的前端应用程序。

## 创建管理前端

现在后端应用程序已经完成，我们可以继续在管理前端上工作。我们之前已经在`admin-frontend`文件夹中为管理前端创建了 Vue 3 项目，因此我们只需要安装所需的包并处理代码。我们将需要 PrimeVue 包 - 即 Vee-Validate、Vue Router、Axios 和 Yup 包。

要安装它们，请在`admin-frontend`文件夹中运行以下命令：

```js
npm i axios primeflex primeicons primevue@³.1.1 vee-validate@next vue-router@4 yup
```

Axios 允许我们向后端发出 HTTP 请求。Vue Router 允许我们将 URL 映射到`page`组件。Vee-Validate 和 Yup 允许我们轻松地向我们的表单添加表单验证，其余的包是 PrimeVue 包。

### 创建管理前端页面

安装完包后，我们可以开始处理代码。首先，我们将处理组件。在`components`文件夹中，添加`CatalogForm.vue`文件，并编写以下代码：

```js
<template>
  <Form @submit="onSubmit" :validation-schema="schema">
    <Field v-slot="{ field, errors }" v-model="name" 
      name="name">
      <div class="p-col-12">
        <div class="p-inputgroup">
          <InputText
            placeholder="Name"
            :class="{ 'p-invalid': errors.length > 0 }"
            v-bind="field"
          />
        </div>
        <small class="p-error" v-if="errors.length > 0">
          Name is invalid.
        </small>
      </div>
    </Field>
    ...
  </Form>
</template>
```

在这里，我们有来自 Vee-Validate 包的`Form`组件，用于添加具有表单验证的表单。只有当所有表单值都有效时，才会触发`submit`事件。我们稍后将注册`Form`组件。`validation-schema`属性设置为 Yup 创建的验证模式对象。

在`Form`组件内，我们有一个`Field`组件，这也是由 Vee-Validate 包提供的。稍后我们还将全局注册此组件，以便我们可以使用它。在`Field`组件内，我们有`InputText`组件，用于在我们的应用程序中添加输入字段。为了为`InputText`组件启用表单验证，我们将`field`对象传递给`slot`属性，并将整个内容作为`v-bind`指令的值。`v-bind`指令允许 Vee-Validate 处理表单值并向我们的`form`字段添加验证。`errors`数组提供了可能发生的任何验证错误。

`p-col-12`类由 PrimeVue 的 PrimeFlex 包提供。它允许我们将`div`标签的宽度设置为全宽，这意味着它占据页面上的 12 列中的 12 列。使用`p-inputgroup`类，我们可以创建一个输入组。`p-error`类将文本颜色样式设置为红色，以便我们可以以用户易于看到的方式显示表单验证消息。`p-invalid`类使输入的边缘变为红色。仅当错误的长度大于`0`时，我们才将其更改为红色，因为这意味着存在验证错误，而且仅在错误的长度大于`0`时才显示较小的元素。

`Field`组件具有`v-model`指令，将输入的值绑定到相应的响应式属性。我们还有一个`name`属性，也用作`submit`事件处理程序的`value`参数的属性名称，该处理程序具有输入的值。这些值始终有效，因为只有当所有表单值都有效时，才会运行提交处理程序。

使用`name`字段，我们可以输入`度假`套餐的名称。

接下来，我们需要添加一个文本区域，以允许用户为度假套餐输入描述。要做到这一点，请编写以下代码：

```js
<template>
  <Form @submit="onSubmit" :validation-schema="schema">
    ...
    <Field v-slot="{ field, errors }" v-model="description" 
      name="description">
      <div class="p-col-12">
        <div class="p-inputgroup">
          <Textarea
            placeholder="Description"
            :class="{ 'p-invalid': errors.length > 0 }"
            v-bind="field"
          />
        </div>
        <small class="p-error" v-if="errors.length > 0">
          Description is invalid
        </small>
      </div>
    </Field>
    ...
  </Form>
</template>
```

这与`name`字段几乎相同；但是，在这种情况下，我们将`InputText`组件替换为`Textarea`组件。我们还更改了`v-model`和`name`的值。`Textarea`组件来自 PrimeVue 包，它以自己的样式呈现为`textarea`元素。

接下来，我们添加图像 URL 字段，以便为度假套餐添加图像 URL。我们只需让用户输入图像 URL，以使我们的项目更简单。要将字段添加到`Form`组件中，请编写以下代码：

```js
<template>
  <Form @submit="onSubmit" :validation-schema="schema">
    ...
    <Field v-slot="{ field, errors }" v-model="imageUrl" 
      name="imageUrl">
      <div class="p-col-12">
        <div class="p-inputgroup">
          <InputText
            placeholder="Image URL"
            :class="{ 'p-invalid': errors.length > 0 }"
            v-bind="field"
          />
        </div>
        <small class="p-error" v-if="errors.length > 0">
          Image URL is invalid.
        </small>
      </div>
    </Field>
    ...
  </Form>
</template>
```

这只是另一个文本输入，名称和`v-model`值不同。最后，让我们使用以下代码向表单添加一个`submit`按钮：

```js
<template>
  <Form @submit="onSubmit" :validation-schema="schema">
    ...
    <div class="p-col-12">
      <Button label="Add" type="submit" />
    </div>
  </Form>
</template>
```

`Button`组件来自 PrimeVue 包，我们稍后将全局注册它，以使其在任何地方都可用。

接下来，我们添加`component`选项对象。我们使用`component`选项 API 来创建我们的组件。首先，我们导入所有内容，并使用 Yup 库创建表单验证模式。要添加代码，请在`components/CatalogForm.vue`中编写以下内容：

```js
<script>
import * as yup from "yup";
import axios from "axios";
import { APIURL } from "@/constants";
const schema = yup.object().shape({
  name: yup.string().required(),
  description: yup.string().required(),
  imageUrl: yup.string().url().required(),
});
export default {
  name: "BookingForm",
  data() {
    return {
      name: "",
      description: "",
      imageUrl: "",
      schema,
    };
  },
  ...
};
</script>
```

在这里，我们使用`yup.object`方法创建了模式对象，它允许我们验证具有一些属性的对象。`validation`模式与`v-model`绑定是分开的。我们传递给`shape`方法的对象属性必须与`Field`组件的`name`属性值匹配。

为了验证具有`name`设置为`name`的字段的值，我们将`name`属性设置为`yup.string().required()`，以确保`name`字段是一个字符串并且有值。我们为`description`设置相同的值。`imageUrl`值设置为`yup.string().url().required()`，以确保输入的值是一个 URL 并且已填写。

`data`方法返回模式，以便我们可以使用`Form`组件的`validation-schema`属性。

为了完成组件，我们添加了`onSubmit`方法，当`Form`组件发出`submit`事件时被调用：

```js
<script>
...
export default {
  ...
  methods: {
    async onSubmit(value) {
      const { name, description, imageUrl } = value;
      await axios.post(`${APIURL}/catalog`, {
        name,
        description,
        imageUrl,
      });
      this.$emit("catalog-form-close");
    },
  },
};
</script>
```

在这里，我们只是从`value`参数中获取`property`值，该参数具有有效的表单字段值。然后，我们使用 JSON 负载进行 POST 请求到目录端点。随后，我们调用`this.$emit`方法来发出`catalog-form-close`事件，以通知对话框组件关闭此表单。

### 添加一个顶部栏和菜单栏

接下来，我们将在我们的应用程序中添加一个`top bar`组件。要做到这一点，在`src/components`文件夹中创建`TopBar.vue`。然后，将以下模板代码添加到文件中：

```js
<template>
  <Menubar :model="items">
    <template #start>
      <b>Admin Frontend</b>
    </template>
  </Menubar>
</template>
```

`Menubar`组件由 PrimeVue 组件提供。我们可以使用它来添加一个菜单栏，其中包含一些我们可以点击以导航到不同页面的项目。`model`属性设置为`items`响应式属性，它是一个我们即将添加的菜单项对象数组。`start`插槽让我们可以在菜单栏的左侧添加项目。我们可以将一些粗体文本放入插槽中，它将显示在左侧。

接下来，我们可以为组件添加一个`component`对象。要添加它，请编写以下代码：

```js
<script>
export default {
  name: "TopBar",
  props: {
    title: String,
  },
  data() {
    return {
      items: [
        {
          label: "Manage Bookings",
          command: () => {
            this.$router.push("/bookings");
          },
        },
...
  methods: {
    logOut() {
      localStorage.clear();
      this.$router.push("/");
    },
  },
  beforeMount() {
    document.title= = this.title;
  },
};
</script>
```

在这里，我们注册`title`属性，用它来设置`document.title`的值。`document.title`属性设置了顶部栏的标题。在`data`方法中，我们返回一个具有项目的响应式属性的对象。这被设置为一个具有`label`和`command`属性的对象。`label`属性显示在用户的菜单栏项目中。该项目显示为一个链接。当我们点击该项目时，`command`方法被运行。

通过`this.$router.push`方法，我们可以导航到与给定 URL 映射的页面。`logOut`方法导航回到与`/`路径映射的页面，这是我们稍后将讨论的*login*页面。另外，我们清除本地存储，以便清除身份验证令牌。

在`beforeMount`钩子中，我们将`document.title`属性设置为`title`属性的值。

### 添加共享代码以处理请求

接下来，让我们编写 Axios 请求拦截器的代码，以便让我们将身份验证令牌添加到除了我们对`/login`端点进行请求之外的所有请求的`x-token`请求头中。为此，创建`src/plugins`文件夹，并将`axios.js`添加到其中。然后，在此文件中，编写以下代码：

```js
import axios from 'axios'
import { APIURL } from '@/constants'
axios.interceptors.request.use((config) => {
  if (config.url.includes(APIURL) 
   && !config.url.includes('login')) {
    config.headers['x-token'] = localStorage.getItem('token')
    return config
  }
  return config
}, (error) => {
  return Promise.reject(error)
})
```

在这里，我们通过检索具有`config.url`属性的 URL 来检查正在进行请求的 URL。然后，如果我们对除`/login`之外的端点进行任何请求，我们设置`x-token`请求头：

```js
config.headers['x-token'] = localStorage.getItem('token')
```

请注意，我们从本地存储中获取令牌，并将其设置为`config.headers['x-token']`的值。`config.headers`属性是一个具有请求头的对象。第二个参数是请求错误处理程序。在这里，我们只是返回一个带有`Promise.reject`的拒绝承诺，以便我们可以处理错误。

接下来，我们向我们的路由添加 Vue Router 路由。我们留在`src/plugins`文件夹中，并创建一个`vue-router.js`文件。然后，我们向文件中添加以下代码：

```js
import { createWebHashHistory, createRouter } from 'vue-
 router'
import Login from '../views/Login.vue'
import Bookings from '../views/Bookings.vue'
import Catalog from '../views/Catalog.vue'
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
  { path: '/bookings', component: Bookings, beforeEnter },
  { path: '/catalog', component: Catalog, beforeEnter },
]
const router = createRouter({
  history: createWebHashHistory(),
  routes,
})
export default router
```

我们添加`beforeEnter`函数来检查前端页面上除首页之外的任何页面的令牌。我们可以使用`to.fullPath`属性检查用户尝试访问的路径。如果不是`'/'`，并且本地存储中没有令牌，那么我们调用带有对象的`next`，并将`fullPath`属性设置为`'/'`以转到登录页面。否则，我们调用没有参数的`next`以转到我们应该去的页面。如果出现错误，那么我们也转到登录页面，正如您从`catch`块中的代码所看到的。

接下来，我们有包含路由定义的`routes`数组。这个数组中`path`属性是路由的路径，`component`是路径映射到的组件。`beforeEnter`属性被添加到最后两个路由对象，这样我们只能在登录后才能访问那里。

然后，为了创建`router`对象，我们使用带有`history`属性设置为`createWebHashHistory`函数返回的对象的对象调用`createRouter`。这样我们就可以在主机名和 URL 的其余部分之间保持哈希。我们将`routes`属性设置为`routes`数组，以便注册路由。这样我们就可以在访问路由时看到正确的组件。

最后，我们将`router`对象作为默认导出，以便稍后可以使用`app.use`将路由对象添加到我们的应用中。

接下来，我们在`src`文件夹内创建`views`文件夹。这意味着我们可以添加用户可以访问的页面。现在，让我们通过将`Bookings.vue`文件添加到`src/views`文件夹来添加一个页面，以允许管理员管理任何预订。我们打开文件并向组件添加以下模板。这样我们就可以添加之前创建的`TopBar`组件：

```js
<template>
  <TopBar title="Manage Bookings" />
  <div class="p-col-12">
    <h1>Manage Bookings</h1>
  </div>
  <div class="p-col-12">
    <Card v-for="b of bookings" :key="b.id">
      <template #title>{{ b.name }} </template>
      <template #content>
        <p>Address: {{ b.address }}</p>
        <p>Description: {{ b.description }}</p>
        <p>Start Date: {{ b.start_date }}</p>
        <p>End Date: {{ b.end_date }}</p>
      </template>
      <template #footer>
        <Button
          icon="pi pi-times"
          label="Cancel"
          class="p-button-secondary"
          @click="deleteBooking(b.id)"
        />
      </template>
    </Card>
  </div>
</template>
```

请注意，我们使用`h1`元素添加页面标题。然后，我们添加`Card`组件来向管理员显示预订情况。`Card`组件由 PrimeVue 提供，我们稍后将注册它。我们使用`v-for`指令将预订数组渲染为多个`Card`组件。`key`属性设置为唯一 ID，以便 Vue 3 可以正确区分每个项目。

我们用不同的内容填充`title`、`content`和`footer`插槽。`footer`插槽有一个`Button`组件，当我们点击按钮时运行`deleteBooking`函数。`icon`属性允许我们在按钮的左侧设置图标。`label`属性在图标的右侧有按钮文本。通过`p-button-secondary`类，我们可以设置按钮的颜色。

接下来，我们可以添加`component`选项对象，其中包含`getBooking`和`deleteBooking`方法，分别通过后端 API 检索预订和删除预订。要添加它们，请编写以下代码：

```js
<script>
import axios from "axios";
import { APIURL } from "@/constants";
import TopBar from "@/components/TopBar";
export default {
  name: "Bookings",
  components: {
    TopBar,
  },
  data() {
    return {
      bookings: [],
    };
  },
...
  beforeMount() {
    this.getBookings();
  },
};
</script>
```

我们还在`components`属性中注册了`TopBar`组件。`getBookings`方法调用`axios.get`发出 GET 请求，并将`this.bookings`的值设置为响应对象的响应式属性。

`bookings`存储在返回的解析值的对象的`data`属性中。

同样，我们在`deleteBooking`方法中调用`axios.delete`发出`DELETE`请求以删除项目。然后，我们调用`this.getBookings`再次获取数据。我们还在`beforeMount`钩子中调用`this.getBookings`，以在页面加载时获取数据。

接下来，我们添加一个页面，允许管理员管理度假套餐项目。为此，让我们将`Catalog.vue`文件添加到`src/views`文件夹中。然后，在文件中，编写以下内容：

```js
<template>
  <TopBar title="Manage Vacation Packages" />
  <div class="p-col-12">
    <h1>Manage Vacation Packages</h1>
  </div>
  <div class="p-col-12">
    <Button label="Add Vacation Package" 
      @click="displayCatalog= = true" />
    <Dialog header="Add Vacation Package" v-
      model:visible="displayCatalog">
      <CatalogForm
        @catalog-form-close="
          displayCatalog= = false;
          getCatalog();
        "
      />
    </Dialog>
  </div>
  ...
</template>
```

在这里，我们添加`TopBar`组件来显示顶部栏；`h1`显示一个标题。接下来，我们添加一个按钮，通过将`displayCatalog`设置为`true`来显示对话框。然后，我们通过将`v-model`指令与可见修饰符设置为`displayCatalog`值来显示`Dialog`组件。使用这个，我们可以控制`Dialog`组件何时显示。`Dialog`组件显示一个对话框，这个组件由 PrimeVue 提供。

`header`属性设置对话框框的标题文本。我们使用`CatalogForm`作为内容，并监听`CatalogForm`组件发出的`catalog-form-close`事件。当它被发出时，我们将`displayCatalog`设置为`false`，并调用`getCatalog`再次获取数据：

```js
<template>
  ...
  <div class="p-col-12">
    <Card v-for="c of catalog" :key="c.id">
      <template #header>
        <img :alt="c.description" :src="c.image_url" />
      </template>
      <template #title> {{ c.name }} </template>
      <template #content>
        {{ c.description }}
      </template>
      <template #footer>
        <Button
          icon="pi pi-times"
          label="Delete"
          class="p-button-secondary"
          @click="deleteCatalogItem(c.id)"
        />
      </template>
    </Card>
  </div>
</template>
```

接下来，我们添加从目录响应属性渲染的`Card`组件，使用`v-for`指令来渲染目录条目。其余代码与我们在`Bookings.vue`文件中的代码类似，但现在渲染属性不同，当我们点击它时，`Button`调用不同的方法。

接着，我们通过向`src/views/Catalog.vue`添加以下代码来添加组件对象：

```js
<script>
import axios from "axios";
import { APIURL } from "@/constants";
import TopBar from "@/components/TopBar";
import CatalogForm from "@/components/CatalogForm";
...
  methods: {
    async getCatalog() {
      const{ { data } = await axios.get(`${APIURL}/catalog`);
      this.catalog = data;
    async deleteCatalogItem(id) {
      await axios.delete(`${APIURL}/catalog/${id}`);
      this.getCatalog();
    },
  },
  beforeMount() {
    this.getCatalog();
  },
};
</script>
```

在这里，代码与我们在`src/views/Bookings.vue`中的代码类似，只是在这里，我们向目录端点发出请求以获取和删除数据。

然后，我们在管理前端应用程序中创建最后一个页面，即登录页面。要添加登录页面，我们将`Login.vue`文件添加到`src/views`文件夹中。然后，在文件内部，我们使用以下代码添加`form`和`username`字段：

```js
<template>
  <Form @submit="onSubmit" :validation-schema="schema">
    <div class="p-col-12">
      <h1>Admin Log In</h1>
    </div>
    <Field v-slot="{ field, errors }" v-model="username" 
      name="username">
      <div class="p-col-12">
        <div class="p-inputgroup">
          <InputText
            placeholder="Username"
            :class="{ 'p-invalid': errors.length > 0 }"
            v-bind="field"
          />
        </div>
        <small class="p-error" v-if="errors.length > 0">
          Username is invalid.
        </small>
      </div>
    </Field>
    ...
  </Form>
</template>
```

`username`字段与我们之前添加的所有其他字段类似。接下来，我们使用以下代码添加`password`输入和按钮：

```js
<template>
  <Form @submit="onSubmit" :validation-schema="schema">
    ...
    <Field v-slot="{ field, errors }" v-model="password" 
      name="password">
      <div class="p-col-12">
        <div class="p-inputgroup">
          <InputText
            placeholder="Password"
            type="password"
            :class="{ 'p-invalid': errors.length > 0 }"
            v-bind="field"
          />
        </div>
        <small class="p-error" v-if="errors.length > 0">
          Password is invalid
        </small>
      </div>
    </Field>
    <div class="p-col-12">
      <Button label="Log In" type="submit" />
    </div>
  </Form>
</template>
```

我们将`type`属性设置为`password`，使字段成为密码输入。按钮的`type`属性设置为`submit`，以便在单击按钮时触发`submit`事件，并且所有表单值保持有效。

接下来，我们添加`Login.vue`文件的组件对象部分，其中包含`onSubmit`方法来发起登录请求：

```js
<script>
import * as yup from "yup";
import axios from "axios";
import { APIURL } from "@/constants";
const schema = yup.object().shape({
  username: yup.string().required(),
  password: yup.string().required(),
});
export default {
  name: "Login",
  data() {
    return {
      username: "",
      password: "",
      schema,
    };
  },
  methods: {
    async onSubmit(values) {
      const { username, password } = values;
      try {
        const {
          data: { token },
        } = await axios.post(`${APIURL}/users/login`, {
          username,
          password,
        });
        localStorage.setItem("token", token);
        this.$router.push("/bookings");
      } catch (error) {
        alert("Login failed");
      }
    },
  },
};
</script>
```

我们创建了验证模式的`schema`对象，它类似于我们之前使用的其他模式。然后，我们将其添加到`data`方法中返回的对象中。`onSubmit`方法从`value`参数中获取`username`和`password`属性，以便我们可以将其用于向`/users/login`端点发起 POST 请求。

完成后，如果请求成功，我们从响应中获取一个令牌，以及`localStorage.setItem`方法。接下来，我们调用`this.$router.push`方法重定向到`/bookings` URL。如果有任何错误，我们将显示一个带有“登录失败”消息的警报。

接下来，我们将`router-view`组件添加到`App.vue`中，这样我们就可以显示在`routes`对象中创建的页面。要添加它，编写管理前端：共享代码，添加以处理请求的代码：

```js
<template>
  <router-view></router-view>
</template>
<script>
export default {
  name: "App",
};
</script>
<style>
body {
  background-color: #ffffff;
  font-family: -apple-system, BlinkMacSystemFont, Segoe UI,
    Roboto, Helvetica,
    Arial, sans-serif, Apple Color Emoji, Segoe UI Emoji, 
      Segoe UI Symbol;
  font-weight: normal;
  color: #495057;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  margin: 0px;
}
</style>
```

我们还有一个`style`标签来设置字体系列，并将页面的边距设置为`0px`，这样元素与页面边缘之间就没有空白。

接下来，我们将`constants.js`添加到`src`文件夹，然后将`APIURL`添加到其中：

```js
export const APIURL = 'http://localhost:3000'
```

在`main.js`中，我们注册了所有全局组件以及之前创建的路由对象。我们还导入了 PrimeVue 提供的全局样式，所以一切看起来都很好：

```js
import { createApp } from 'vue'
import App from './App.vue'
import PrimeVue from 'primevue/config';
import InputText from "primevue/inputtext";
import Button from "primevue/button";
import Card from 'primevue/card';
import Toolbar from 'primevue/toolbar';
import router from './plugins/vue-router'
import Textarea from 'primevue/textarea';
import Dialog from 'primevue/dialog';
import Menubar from 'primevue/menubar';
import { Form, Field } from "vee-validate";
import "primeflex/primeflex.css";
import 'primevue/resources/themes/bootstrap4-light-blue/theme.css'
import "primevue/resources/primevue.min.css";
import "primeicons/primeicons.css";
import './plugins/axios'
...
app.component("Form", Form);
app.component("Field", Field);
app.use(PrimeVue);
app.use(router)
app.mount('#app')
```

在`package.json`中，通过将`script.serve`属性更改为以下内容，更改开发服务器运行的端口：

```js
{
  ...
  "scripts": {
    "serve": "vue-cli-service serve --port 8082",
    "build": "vue-cli-service build",
    "lint": "vue-cli-service lint"
  },
  ...
}
```

现在，当我们运行`npm run serve`时，我们会得到以下截图：

![图 6.1 - 管理前端](img/Figure_6.1_B14405.jpg)

图 6.1 - 管理前端

现在我们已经创建了管理前端应用程序，我们唯一需要添加的是用户前端。

## 创建用户前端

现在我们已经完成了管理前端，我们将通过创建用户前端来完成本章的项目。用户前端与管理前端类似；但是，在这种情况下，不需要身份验证即可使用。

我们将从安装与管理前端相同的软件包开始。转到前端文件夹并运行以下命令：

```js
npm i axios primeflex primeicons primevue@³.1.1 vee-validate@next vue-router@4 yup
```

接下来，如果不存在，创建`src/components`文件夹。然后，在`src/components`中创建`BookingForm.vue`文件，以便用户可以添加他们的预订。

添加`form`和`name`字段，以允许用户输入他们的姓名：

```js
<template>
  <Form @submit="onSubmit" :validation-schema="schema">
    <Field v-slot="{ field, errors }" v-model="name" 
      name="name">
      <div class="p-col-12">
        <div class="p-inputgroup">
          <InputText
            placeholder="Name"
            :class="{ 'p-invalid': errors.length > 0 }"
            v-bind="field"
          />
        </div>
        <small class="p-error" v-if="errors.length > 0">Name 
          is invalid </small>
      </div>
    </Field>
    ...
  </Form>
</template>
```

这与我们之前添加的其他文本输入字段非常相似。然后，使用以下代码添加`地址`字段：

```js
<template>
  <Form @submit="onSubmit" :validation-schema="schema">
    ...
    <Field v-slot="{ field, errors }" v-model="address" 
      name="address">
      <div class="p-col-12">
        <div class="p-inputgroup">
          <InputText
            placeholder="Address"
            :class="{ 'p-invalid': errors.length > 0 }"
            v-bind="field"
          />
        </div>
        <small class="p-error" v-if="errors.length > 0"
          >Address is invalid</small
        >
      </div>
    </Field>
    ...
  </Form>
</template>
```

现在，让我们添加 PrimeVue 提供的`Calendar`组件，这是我们在此项目中以前未使用过的。`Calendar`组件允许用户选择日期。我们可以添加`开始日期`字段，以允许用户选择他们假期的开始日期：

```js
<template>
  <Form @submit="onSubmit" :validation-schema="schema">
    ...
    <Field v-slot="{ field, errors }" v-model="startDate"         name="startDate">
      <div class="p-col-12">
        <label>Start Date</label>
        <div class="p-inputgroup">
          <Calendar
            inline
            placeholder="Start Date"
            :class="{ 'p-invalid': errors.length > 0 }"
            :minDate="new Date()"
            v-bind="field"
            v-model="startDate"
          />
        </div>
        <small class="p-error" v-if="errors.length > 0">
          Start date is invalid
        </small>
      </div>
    </Field>
    ...
  </Form>
</template>
```

在这里，我们有`minDate`属性，它设置用户可以选择的最早日期。`inline`属性将使日期选择器显示在表单上，而不是在弹出窗口中。同样，我们可以使用以下代码添加`结束日期`字段：

```js
<template>
  <Form @submit="onSubmit" :validation-schema="schema">
    ...
    <Field v-slot="{ field, errors }" v-model="endDate"         name="endDate">
      <div class="p-col-12">
        <label>End Date</label>
        <div class="p-inputgroup">
          <Calendar
            inline
            placeholder="End Date"
            :class="{ 'p-invalid': errors.length > 0 }"
            v-bind="field"
            v-model="endDate"
            :minDate="new Date(+startDate + 24 * 3600 * 1000)"
          />
        </div>
        <small class="p-error" v-if="errors.length > 0"
          >End date is invalid</small
        >
      </div>
    </Field>
    ...
  </Form>
</template>
```

在这里，我们将`minDate`属性设置为`startDate`后的一天。`24 * 3600 * 1000`毫秒等于一天。最后，我们添加`submit`按钮，就像我们在其他表单中所做的那样：

```js
<template>
  <Form @submit="onSubmit" :validation-schema="schema">
    ...
    <div class="p-col-12">
      <Button label="Book" type="submit" />
    </div>
    ...
  </Form>
</template>
```

接下来，我们通过编写以下内容创建`schema`：

```js
<script>
import { Form, Field } from "vee-validate";
import * as yup from "yup";
import axios from "axios";
import { APIURL } from "@/constants";
const schema = yup.object().shape({
  name: yup.string().required(),
  address: yup.string().required(),
  startDate: yup.date().required().min(new Date()),
  endDate: yup
    .date()
    .required()
    .when(
      "startDate",
      (startDate, schema) => startDate && 
        schema.min(startDate)
    ),
});
...
</script>
```

为了验证`endDate`，我们使用`when`方法调用要检查的`field`名称。然后，我们调用`schema.min`来确保`endDate`晚于`startDate`。

接下来，我们添加`component`对象来注册`selectedCatalogId`属性并添加`onSubmit`方法。我们编写以下代码：

```js
<script>
...
export default {
  name: "BookingForm",
  components: {
    Form,
    Field,
  },
  props: {
...
  methods: {
    async onSubmit(values) {
      const { name, address, startDate, endDate } = values;
      await axios.post(`${APIURL}/bookings`, {
        name,
        address,
        startDate,
        endDate,
        catalogItemId: this.selectedCatalogId,
      });
      this.$emit("booking-form-close");
    },
  },
};
</script>
```

`onSubmit`方法从`values`参数中获取表单字段值，并向 bookings 端点发出 POST 请求以添加预订。我们使用`selectedCatalogId`来添加预订。然后，我们发出`booking-form-close`事件，向父级发出事件，以信号表单关闭。

接下来，我们通过将`vue-router.js`添加到`src/plugins`文件夹中，向我们的应用程序添加 Vue Router：

```js
import { createWebHashHistory, createRouter } from 'vue-router'
import Catalog from '../views/Catalog.vue'
const routes = [
  { path:'/', component: Catalog },
]
const router = createRouter({
  history: createWebHashHistory(),
  routes,
})
export default router
```

这与我们在管理前端中所做的非常相似。

接下来，我们创建一个页面，通过添加`src/views/Catalog.vue`文件，然后添加以下模板代码来向用户展示所有的度假套餐：

```js
<template>
  <Card v-for="c of catalog" :key="c.id">
    <template #header>
      <img :alt="c.description" :src="c.image_url" />
    </template>
    <template #title> {{ c.name }} </template>
    <template #content>
      {{ c.description }}
    </template>
    <template #footer>
      <Button
        icon="pi pi-check"
        label="Book"
        class="p-button-secondary"
        @click="book(c.id)"
      />
...
          :selectedCatalogId="selectedCatalogId"
        />
      </Dialog>
    </template>
  </Card>
</template>
```

在这里，我们只是从目录数组中呈现一个表单。我们有一个带有`BookingForm`组件的`Dialog`组件。我们监听由其发出的`booking-form-close`事件，通过将`selectedCatalogId`作为同名 prop 的值传递给其来关闭`Dialog`组件，将`displayBookingForm`设置为`false`并调用`displayMessage`来显示警报。

模板代码的其余部分几乎与我们之前的相同，除了显示的属性名称和标题插槽中图像的添加。 

接下来，我们通过编写以下代码将`component`选项对象添加到同一文件中：

```js
<script>
import axios from "axios";
import { APIURL } from "@/constants";
import BookingForm from "../components/BookingForm.vue";
export default {
  name: "Catalog",
  components: {
    BookingForm,
  },
  data() {
    return {
      selectedCatalogId: undefined,
      displayBookingForm: false,
...
    displayMessage() {
      alert("Booking successful");
    },
  },
  beforeMount() {
    this.getCatalog();
  },
};
</script>
```

我们在`components`属性中注册`BookingForm`组件。`getCatalog`函数从 API 获取度假套餐目录项。`booking`函数将`displayBookingForm`设置为`true`以打开`Dialog`组件，并在那里也设置了`selectedCatalogId`。`beforeMount`钩子调用`getCatalog`以检索目录数据。

### 添加路由视图和入口点代码

在`App.vue`中，我们编写以下代码来添加`router-view`并设置与我们在管理前端中相同的样式：

```js
<template>
  <router-view></router-view>
</template>
<script>
export default {
  name: "App",
};
</script>
<style>
body {
  background-color: #ffffff;
  font-family: -apple-system, BlinkMacSystemFont, Segoe UI,
    Roboto, Helvetica, Arial, sans-serif, Apple Color Emoji, 
     Segoe UI Emoji, Segoe UI Symbol;
  font-weight: normal;
  color: #495057;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  margin: 0px;
}
</style>
```

然后，我们在`src`文件夹中创建`constants.js`，并添加以下行来添加`APIURL`：

```js
export const APIURL = 'http://localhost:3000'
```

然后，在`main.js`中，我们用以下代码替换文件的内容，以全局注册组件和路由。我们还导入了 PrimeVue 提供的样式，让我们的应用程序看起来很好：

```js
import { createApp } from 'vue'
import App from './App.vue'
import PrimeVue from 'primevue/config';
import InputText from "primevue/inputtext";
import Button from "primevue/button";
import Card from 'primevue/card';
import Toolbar from 'primevue/toolbar';
import Calendar from 'primevue/calendar';
import Dialog from 'primevue/dialog';
import router from './plugins/vue-router'
import "primeflex/primeflex.css";
import 'primevue/resources/themes/bootstrap4-light-blue/theme.css'
import "primevue/resources/primevue.min.css";
import "primeicons/primeicons.css";
const app = createApp(App)
app.component("InputText", InputText);
app.component("Button", Button);
app.component("Card", Card);
app.component("Toolbar", Toolbar);
app.component("Calendar", Calendar);
app.component("Dialog", Dialog);
app.use(PrimeVue);
app.use(router)
app.mount('#app')
```

在`package.json`中，我们通过将`script.serve`属性更改为以下内容来更改开发服务器运行的端口：

```js
{
  ...
  "scripts": {
    "serve": "vue-cli-service serve --port 8082",
    "build": "vue-cli-service build",
    "lint": "vue-cli-service lint"
  },
  ...
}
```

现在，当我们运行`npm run serve`时，我们会得到以下截图：

![图 6.2 - 用户前端](img/Figure_6.2_B14405.jpg)

图 6.2 - 用户前端

用户前端创建完成后，度假预订系统现在已经完成。

# 总结

在本章中，我们学习了如何有效地使用 PrimeVue 来构建一个度假预订应用程序。通过 PrimeVue，我们可以轻松创建漂亮的 Vue 3 web 应用程序。PrimeVue 带有许多有用的组件，我们可以添加到我们的 web 应用程序中，比如输入框、文本区域、表格、对话框、日期选择器等等。它还内置了样式，因此我们不必自己从头开始添加任何样式。此外，我们还可以添加 PrimeVue 提供的 PrimeFlex 包；通过 flexbox，我们可以轻松地改变元素和组件的间距和位置。

Vee-Validate 和 Yup 允许我们在 Vue 3 应用程序中添加表单验证。这与 PrimeVue 提供的输入组件很容易集成。这两个库使得大部分表单验证工作变得简单，因为我们不必自己编写所有的表单验证代码。

为了创建一个简单的后端，我们使用 Express 创建了一个简单的 API 来与前端交互。我们还使用了`sqlite3`包来操作我们 API 中的 SQLite 数据库。Express 带有许多附加组件，我们可以使用它们来添加许多功能，比如跨域通信。我们还可以通过`jsonwebtoken`库很容易地向我们的 Express 应用程序添加 JSON Web Token 身份验证。

在下一章中，我们将学习如何使用 GraphQL 和 Vue 3 构建一个商店前端。
