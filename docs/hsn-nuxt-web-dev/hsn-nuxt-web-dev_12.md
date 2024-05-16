添加服务器端数据库

在上一章中，我们为 Nuxt 应用程序添加了 Koa 作为服务器端框架，并添加了一些虚拟数据。在本章中，我们将设置 MongoDB 作为服务器端数据库，以替换虚拟数据。我们将编写一些 MongoDB CRUD 查询，向数据库添加数据，并使用`asyncData`从数据库中获取数据。

本章我们将涵盖的主题如下：

+   介绍 MongoDB

+   编写基本的 MongoDB 查询

+   编写 MongoDB CRUD 操作

+   使用 MongoDB CRUD 查询注入数据

+   将 MongoDB 集成到 Koa 中

+   与 Nuxt 页面集成

# 第九章：介绍 MongoDB

MongoDB 是一个开源的面向文档的数据库管理系统（DBMS），它以类似 JSON 的文档形式存储数据，称为二进制 JSON（BSON）- MongoDB 的 JSON 文档的二进制表示，可以比普通 JSON 更快地解析。自 2009 年以来，它是最受欢迎的 NoSQL 数据库系统之一，不使用表和行，与关系数据库管理系统（RDBMS）相反。在 MongoDB 中，您的数据记录是由名称-值对（或字段和值对）组成的文档，类似于 JSON 对象，但是二进制编码以支持 JSON 范围之外的数据类型，例如 ObjectId、Date 和 Binary data（https://docs.mongodb.com/manual/reference/bson-types/）。因此，它被称为二进制 JSON。例如，`{"hello":"world"}`的文档将存储在`.bson`文件中，如下所示：

```js
1600 0000 0268 656c 6c6f 0006 0000 0077
 6f72 6c64 0000
```

实际上，BSON 中编码的数据不是人类可读的，但是在使用 MongoDB 时，我们不必过多担心，因为它们将由 MongoDB 驱动程序为您自动编码和解码。您只需要使用 MongoDB 的语法、方法、操作和选择器与您熟悉的 JSON 文档一起构建 BSON 存储的文档。让我们安装 MongoDB 并开始编写。

## 安装 MongoDB

根据版本（社区版或企业版）和平台（Windows、Ubuntu 或 macOS），安装 MongoDB 有几种方法。您可以按照这里提供的链接进行操作：

+   **MongoDB 社区版：**[`docs.mongodb.com/manual/installation/#mongodb-community-edition-installation-tutorials`](https://docs.mongodb.com/manual/installation/#mongodb-community-edition-installation-tutorials)

+   **MongoDB 企业版：** [`docs.mongodb.com/manual/installation/#mongodb-enterprise-edition-installation-tutorial`](https://docs.mongodb.com/manual/installation/#mongodb-enterprise-edition-installation-tutorials)

+   **在 Ubuntu 上安装 MongoDB 社区版：** [`docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/`](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/)

## 在 Ubuntu 20.04 上安装

在本书中，我们将在 Ubuntu 20.04（Focal Fossa）上安装 MongoDB 4.2（社区版）。如果您使用的是 Ubuntu 19.10（Eoan Ermine），它也是一样的。如果您使用其他旧版本的 Ubuntu，比如 14.04 LTS（Trusty Tahr）、16.04 LTS（Xenial Xerus）或 18.04 LTS（Bionic Beaver），请在上一节中的链接中查看《在 Ubuntu 上安装 MongoDB 社区版》。所以，让我们开始吧：

1.  从`mongodb.org`导入公钥：

```js
$ wget -qO - https://www.mongodb.org/static/pgp/server-4.2.asc | sudo apt-key add -
```

您应该会得到一个`OK`的响应。

1.  为 MongoDB 创建一个列表文件：

```js
$ echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.2.list
```

1.  更新系统中的所有本地软件包：

```js
$ sudo apt-get update
```

1.  安装 MongoDB 软件包：

```js
$ sudo apt-get install -y mongodb-org
```

## 启动 MongoDB

一旦您安装了 MongoDB 软件包，您接下来应该做的是查看您是否可以从终端启动和连接 MongoDB 服务器。所以，让我们开始吧：

1.  在以下命令中手动或自动启动 MongoDB：

```js
$ sudo systemctl start mongod
$ sudo systemctl enable mongod
```

1.  通过检查其版本来验证它：

```js
$ mongo --version
```

您应该会在终端上得到类似的输出：

```js
MongoDB shell version v4.2.1
git version: edf6d45851c0b9ee15548f0f847df141764a317e
OpenSSL version: OpenSSL 1.1.1d 10 Sep 2019
allocator: tcmalloc
modules: none
build environment:
    distmod: ubuntu1804
    distarch: x86_64
    target_arch: x86_64
```

1.  可选地，使用以下命令检查 MongoDB 服务器的状态：

```js
$ sudo service mongod status
```

您应该会在终端上得到类似的输出：

```js
● mongod.service - MongoDB Database Server
   Loaded: loaded (/lib/systemd/system/mongod.service; enabled;
     vendor preset: enabled)
   Active: active (running) since Fri 2019-08-30 03:37:15 UTC;
     29s ago
     Docs: https://docs.mongodb.org/manual
 Main PID: 31961 (mongod)
   Memory: 68.2M
   CGroup: /system.slice/mongod.service
           └─31961 /usr/bin/mongod --config /etc/mongod.conf
```

1.  可选地，使用`netstat`命令检查 MongoDB 是否已在端口 27017 上启动：

```js
$ sudo netstat -plntu
```

您应该会看到类似的输出：

```js
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address Foreign Address State PID/Program name
tcp 0 0 127.0.0.1:27017 0.0.0.0: LISTEN 792/mongod
```

1.  连接到 MongoDB Shell：

```js
$ mongo
```

1.  退出 MongoDB Shell（当您想要时）：

```js
> exit
```

如果出于任何原因，您想要从系统完全删除 MongoDB，那么使用此命令：

```js
$ sudo apt-get purge mongodb-org*
```

在下一节中，您将开始从您刚刚学习的 MongoDB Shell 中编写一些基本查询。让我们开始吧。

# 编写基本的 MongoDB 查询

在您可以编写 MongoDB 查询和注入一些数据之前，首先您必须连接到 MongoDB，因此打开一个终端并输入以下内容：

```js
$ mongo
```

然后您可以列出 MongoDB 系统中的数据库：

```js
> show dbs
```

您应该会得到以下输出：

```js
admin 0.000GB
config 0.000GB
```

这两个数据库（`admin`和`config`）是 MongoDB 的默认数据库。但是，根据我们的需求和目的，我们应该创建新的数据库。

## 创建一个数据库

一旦你登录了 MongoDB shell，你可以使用`use`命令在 MongoDB 中创建一个新的数据库：

```js
> use nuxt-app
```

你应该得到以下结果：

```js
switched to db nuxt-app
```

但是，请注意，当你想选择一个现有的数据库时，情况是一样的：

```js
> use admin
```

你应该得到以下结果：

```js
switched to db admin
```

如果你想删除一个数据库，首先使用`use`命令选择数据库，例如，`use nuxt-app`，然后使用`dropDatabase`函数：

```js
> db.dropDatabase()
```

你应该得到以下结果：

```js
{ "dropped" : "nuxt-app", "ok" : 1 }
```

我们接下来要学习的是如何在我们创建的数据库中创建或添加集合。

## 创建一个新的集合

什么是 MongoDB 集合？如果你熟悉关系型数据库，集合类似于关系型数据库表，可以包含不同的字段，除了强制执行模式。我们使用`createCollection`方法以以下格式创建一个集合：

```js
> db.createCollection(<name>, <options>)
```

`<name>`参数是集合的名称，比如 user、article 或其他。`<options>`参数是可选的，用于指定创建一个固定大小的集合或验证更新和插入的集合字段。有关这些选项的更多信息，请访问[`docs.mongodb.com/manual/reference/method/db.createCollection/`](https://docs.mongodb.com/manual/reference/method/db.createCollection/)。让我们创建一个文档，看看在接下来的步骤中还可以做什么：

1.  创建一个没有任何选项的集合：

```js
> db.createCollection("users", {})
```

你应该得到以下结果：

```js
{ "ok" : 1 }
```

1.  使用`getCollectionNames`方法列出数据库中的所有集合：

```js
> db.getCollectionNames()
```

你应该得到以下结果：

```js
[ "users" ]
```

1.  使用`drop`方法删除`users`集合：

```js
> db.users.drop()
```

你应该得到以下结果：

```js
true
```

既然我们知道如何创建一个集合，下一件你应该知道的事情是如何向集合中添加文档。让我们在下一节中来看看。

# 编写 MongoDB CRUD 操作

当涉及到在数据库系统中管理和操作数据时，我们必须创建、读取、更新和删除（CRUD）文档。我们可以使用 MongoDB 的 CRUD 操作来实现这一点。你可以在[`docs.mongodb.com/manual/crud/`](https://docs.mongodb.com/manual/crud/)上阅读更多关于 MongoDB CRUD 操作的信息。在本书中，我们只会看到如何使用每一个的简单示例：

+   **创建操作**：我们可以使用以下方法向集合中创建或插入新文档：

```js
db.<collection>.insertOne(<document>)
db.<collection>.insertMany([<document>, <document>, <document>, ...])
```

请注意，如果您的数据库中不存在该集合，这些`insert`操作将自动为您创建它。

+   **读取操作**：

我们可以使用以下方法从集合中获取文档：

```js
db.<collection>.find(<query>, <projection>)
```

+   **更新操作**：我们可以使用以下方法修改集合中现有的文档：

```js
db.<collection>.updateOne(<filter>, <update>, <options>)
db.<collection>.updateMany(<filter>, <update>, <options>)
db.<collection>.replaceOne(<filter>, <replacement>, <options>)
```

+   **删除操作**：我们可以使用以下方法从集合中删除文档：

```js
db.<collection>.deleteOne(<filter>, <options>)
db.<collection>.deleteMany(<filter>, <options>)
```

通过这些简化的 CRUD 操作，您可以开始在下一节向数据库注入数据，然后您离创建一个完全功能的 API 又近了一步。让我们开始吧！

# 使用 MongoDB CRUD 注入数据

我们将使用您在上一节中学到的 MongoDB CRUD 操作向 nuxt-app 数据库中注入一些数据。

## 插入文档

我们可以使用`insertOne`或`insertMany`方法插入新文档如下：

+   **插入单个文档**：我们可以像这样插入新文档：

```js
> db.<collection>.insertOne(<document>)
```

让我们使用以下代码插入一个文档：

```js
db.user.insertOne(
  {
    name: "Alexandre",
    age: 30,
    slug: "alexandre",
    role: "admin",
    status: "ok"
  }
)
```

您应该得到类似于这样的结果：

```js
{
  "acknowledged" : true,
  "insertedId" : ObjectId("5ca...")
}
```

+   **插入多个文档**：我们可以像这样插入多个新文档：

```js
> db.<collection>.insertMany([<document>,<document>,<document>,...])
```

让我们使用以下代码插入两个文档：

```js
> db.user.insertMany([
  {
    name: "Pooya",
    age: 25,
    slug: "pooya",
    role: "admin",
    status: "ok"
  },
  {
    name: "Sébastien",
    age: 22,
    slug: "sebastien",
    role: "writer",
    status: "pending"
  }
])
```

您应该得到类似于这样的结果：

```js
{
  "acknowledged" : true,
  "insertedIds" : [
    ObjectId("5ca..."),
    ObjectId("5ca...")
  ]
}
```

在向`user`集合添加文档后，我们希望获取它们，这可以通过下一节中的读取操作简单完成。

## 查询文档

我们可以按以下方式使用`find`方法获取文档：

+   **选择集合中的所有文档**：我们可以像这样从集合中获取所有文档：

```js
> db.<collection>.find()
```

这个操作与以下 SQL 语句相同：

```js
SELECT  FROM <table>
```

让我们按以下方式从`user`集合中获取所有文档：

```js
> db.user.find()
```

您应该得到类似于这样的结果：

```js
{ "_id" : ObjectId("5ca..."), "name" : "Alexandre", "slug" :
 "alexandre", ... }
{ "_id" : ObjectId("5ca..."), "name" : "Pooya", "slug" : "pooya", ... }
{ "_id" : ObjectId("5ca..."), "name" : "Sébastien", "slug" : 
 "sebastien", ... }
```

+   **指定相等条件**：我们可以像这样从集合中获取特定文档：

```js
> db.<collection>.find(<query>, <projection>)
```

您可以看到，我们使用与上一个示例相同的`find`方法，但是在`<query>`参数中传入选项来过滤匹配特定查询的文档。例如，以下行选择了`status`等于`ok`的文档：

```js
> db.user.find( { status: "ok" } )
```

这个操作与以下 SQL 语句相同：

```js
SELECT  FROM user WHERE status = "ok"
```

您应该得到类似于这样的结果：

```js
{ "_id" : ObjectId("5ca..."), "name" : "Alexandre", ... "status" : "ok" }
{ "_id" : ObjectId("5ca..."), "name" : "Pooya", ... "status" : "ok" }
```

+   **使用查询操作符指定条件**：我们还可以在`find`方法的`<query>`参数中使用 MongoDB 查询选择器，例如`$eq`，`$gt`或`$in`。例如，以下行获取了`status`等于`ok`或`pending`的文档：

```js
> db.user.find( { status: { $in: [ "ok", "pending" ] } } )
```

此操作与以下 SQL 语句相同：

```js
SELECT  FROM user WHERE status in ("ok", "pending")
```

您可以在[`docs.mongodb.com/manual/reference/operator/query/query-selectors`](https://docs.mongodb.com/manual/reference/operator/query/#query-selectors)找到有关查询选择器的更多信息。

+   指定 AND 条件：您还可以将过滤器与查询选择器混合使用。例如，以下行获取`status`等于`ok` **并且** `age`小于($lt) 30 的文档：

```js
> db.user.find( { status: "ok", age: { $lt: 30 } } )
```

您应该得到类似于这样的结果：

```js
{ "_id" : ObjectId("5ca..."), "name" : "Pooya", "age" : 25, ... }
```

此操作与以下 SQL 语句相同：

```js
SELECT  FROM user WHERE status = "ok" AND age < 30
```

+   指定 OR 条件：您还可以使用`$or`选择器创建 OR 条件，以获取至少满足一个条件的文档。例如，以下行获取`status`等于`ok` **或** `age`小于($lt) 30 的文档：

```js
> db.user.find( { $or: [ { status: "ok" }, { age: { $lt: 30 } } ] } )
```

此操作与以下 SQL 语句相同：

```js
SELECT  FROM user WHERE status = "ok" OR age < 30
```

您应该得到类似于这样的结果：

```js
{ "_id" : ObjectId("5ca..."), "name" : "Pooya", "age" : 25, ... }
```

您可以在[`docs.mongodb.com/manual/reference/operator/query/`](https://docs.mongodb.com/manual/reference/operator/query/)找到有关查询和投影操作符的更多信息，以及在[`docs.mongodb.com/manual/reference/operator/query/logical`](https://docs.mongodb.com/manual/reference/operator/query/#logical)找到`$or`选择器的更多信息。

现在，我们感兴趣的下一件事是更新现有文档，所以让我们继续下一节。

## 更新文档

我们可以使用`updateOne`和`updateMany`方法更新现有文档，如下所示：

+   更新单个文档：我们可以像这样更新现有文档：

```js
> db.<collection>.updateOne(<filter>, <update>, <options>)
```

让我们使用`$set`操作符在`<update>`参数中更新数据，更新`<filter>`参数中`name`等于`Sébastien`的文档，如下所示：

```js
> db.user.updateOne(
   { name: "Sébastien" },
   {
     $set: { status: "ok" },
     $currentDate: { lastModified: true }
   }
)
```

您应该得到以下结果：

```js
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }
```

`$set`操作符用于用新值替换字段的值。它采用以下格式：

```js
{ $set: { <field1>: <value1>, ... } }
```

`$currentDate`操作符用于将字段的值设置为当前日期。它返回的值可以是人类可读的日期（默认值），例如`2013-10-02T01:11:18.965Z`，也可以是时间戳，例如`1573612039`。

您可以在[`docs.mongodb.com/manual/reference/operator/update/set/`](https://docs.mongodb.com/manual/reference/operator/update/set/)找到有关`$set`运算符的更多信息。您可以在[`docs.mongodb.com/manual/reference/operator/update/currentDate/`](https://docs.mongodb.com/manual/reference/operator/update/currentDate/)找到有关`$currentDate`的更多信息。

+   **更新多个文档**：我们可以像这样更新多个现有文档：

```js
> db.<collection>.updateMany(<filter>, <update>, <options>)
```

让我们更新`status`为`ok`的文档：

```js
> db.user.updateMany(
   { "status": "ok" },
   {
     $set: { status: "pending" },
     $currentDate: { lastModified: true }
   }
)
```

你应该得到以下结果：

```js
{ "acknowledged" : true, "matchedCount" : 3, "modifiedCount" : 3 }
```

您可以在[`docs.mongodb.com/manual/reference/operator/update/`](https://docs.mongodb.com/manual/reference/operator/update/)找到有关更新运算符的更多信息。

+   **替换文档**：我们可以像这样替换现有文档的内容，除了`_id`字段：

```js
> db.<collection>.replaceOne(<filter>, <replacement>, <options>)
```

让我们按如下方式用`<replacement>`参数替换`name`等于`Pooya`的文档为全新的文档：

```js
> db.user.replaceOne(
    { name: "Pooya" },
    {
      name: "Paula",
      age: "31",
      slug: "paula",
      role: "admin",
      status: "ok"
    }
)
```

你应该得到以下结果：

```js
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }
```

在学习如何更新现有文档之后，你应该学会的下一件事是如何删除现有文档。让我们深入下一节。

## 删除文档

我们可以通过以下方式使用`deleteOne`和`deleteMany`方法删除现有文档：

+   **仅删除匹配条件的一个文档**：我们可以像这样删除现有文档：

```js
> db.<collection>.deleteOne(<filter>, <options>)
```

让我们按如下方式删除`status`字段等于`pending`的文档：

```js
> db.user.deleteOne( { status: "pending" } )
```

你应该得到以下结果：

```js
{ "acknowledged" : true, "deletedCount" : 3 }
```

+   **删除匹配条件的文档**：我们可以像这样删除多个现有文档：

```js
> db.<collection>.deleteMany(<filter>, <options>)
```

让我们删除`status`字段等于`ok`的文档：

```js
> db.user.deleteMany({ status : "ok" })
```

你应该得到以下结果：

```js
{ "acknowledged" : true, "deletedCount" : 2 }
```

+   **删除所有文档**：我们可以通过将空过滤器传递给`deleteMany`方法来删除集合中的所有文档，如下所示：

```js
> db.<collection>.deleteMany({})
```

让我们使用以下代码从`user`集合中删除所有文档：

```js
> db.user.deleteMany({})
```

你应该得到以下结果：

```js
{ "acknowledged" : true, "deletedCount" : 1 }
```

干得好！您已经成功完成了这些部分中的 MongoDB CRUD 操作。您可以在[`docs.mongodb.com/manual/reference/method/js-collection/`](https://docs.mongodb.com/manual/reference/method/js-collection/)找到更多其他方法。在下一节中，我们将指导您如何使用 MongoDB 驱动程序将 CRUD 操作与服务器端框架集成。让我们开始吧。

# 将 MongoDB 与 Koa 集成

我们已经学习了一些通过 MongoDB Shell 执行 CRUD 操作的 MongoDB 查询。现在我们只需要 MongoDB 驱动程序来帮助我们连接到 MongoDB 服务器，并执行与 MongoDB Shell 相同的 CRUD 操作。我们将在我们的服务器端框架 Koa 中将此驱动程序安装为依赖项。

## 安装 MongoDB 驱动程序

Node.js 应用程序的官方 MongoDB 驱动程序是`mongodb`。它是一个高级 API，构建在 MongoDB 核心驱动程序`mongodb-core`之上，后者是一个低级 API。前者是为最终用户而设计的，而后者是为 MongoDB 库开发人员而设计的。`mongodb`包含了使 MongoDB 连接、CRUD 操作和身份验证变得容易的抽象和辅助功能，而`mongodb-core`只包含 MongoDB 拓扑连接的基本管理、核心 CRUD 操作和身份验证。

关于这两个软件包的更多信息，请访问以下网站：

+   [`www.npmjs.com/package/mongodb`](https://www.npmjs.com/package/mongodb) 用于 MongoDB 驱动程序

+   [`www.npmjs.com/package/mongodb-core`](https://www.npmjs.com/package/mongodb-core) 用于 MongoDB 核心驱动程序

+   [`mongodb.github.io/node-mongodb-native/3.0/api/`](http://mongodb.github.io/node-mongodb-native/3.0/api/) 用于 MongoDB 驱动程序 API

我们可以使用 npm 安装 MongoDB 驱动程序：

```js
$ npm i mongodb
```

接下来，我们将在接下来的部分中通过一个快速示例来了解如何使用它。

## 使用 MongoDB 驱动程序创建一个简单的应用程序

让我们使用 MongoDB 驱动程序设置一个简单的应用程序来执行简单的连接检查。在这个测试中，我们将使用我们在上一章中介绍的 Backpack 构建系统来运行我们的测试。所以，让我们按照以下步骤开始：

1.  按照前一节中所示的步骤安装 MongoDB 驱动程序，然后安装 Backpack 和 cross-env：

```js
$ npm i backpack-core
$ npm i cross-env
```

1.  创建一个`/src/`文件夹作为默认入口目录，并在其中创建一个`index.js`文件，然后从 Node.js 中导入 MongoDB 驱动程序和 Assert 模块，如下所示：

```js
// src/index.js
import { MongoClient } from 'mongodb'
import assert from 'assert'

const url = 'mongodb://localhost:27017'
const dbName = 'nuxt-app'
```

在这一步中，我们还应该提供 MongoDB 连接详细信息：MongoDB 服务器的默认地址是`mongodb://localhost:27017`，我们要连接的数据库是`nuxt-app`。

请注意，Assert 是一个 Node.js 内置模块，其中包含一组用于单元测试代码的断言函数，因此我们不必安装此模块。如果您想了解更多关于此模块的信息，请访问[`nodejs.org/api/assert.html#assert_assert`](https://nodejs.org/api/assert.html#assert_assert)。

1.  接下来，在 MongoDB 服务器中建立与数据库的连接，并使用 Assert 来确认连接，如下所示：

```js
// src/index.js
MongoClient.connect(url, {
  useUnifiedTopology: true,
  useNewUrlParser: true 
  }, (err, client) => {
  assert.equal(null, err)
  console.log('Connected to the MongoDB server')

  const db = client.db(dbName)
  client.close()
})
```

在这个例子中，我们使用了`assert`模块中的`equal`方法来确保在使用`client`回调创建数据库实例之前，`err`回调是`null`的。每当完成一个任务时，我们应该始终使用`close`方法关闭连接。

1.  如果您在终端上使用`npm run dev`运行此连接测试，您应该在终端上获得以下输出：

```js
Connected successfully to server 
```

您可以在我们的 GitHub 存储库的`/chapter-9/mongo-driver/`中找到这个简单的例子。

请注意，我们连接到 MongoDB 时没有进行任何身份验证，因为我们尚未保护我们的 MongoDB。您将在本书的最后一章[第十八章]“使用 CMS 和 GraphQL 创建 Nuxt 应用程序”中学习如何设置新的管理用户来保护您的 MongoDB。为了降低学习曲线并加快本章后续部分的开发过程，我们将选择不保护 MongoDB。现在，让我们更深入地了解如何配置 MongoDB 驱动程序。

## 配置 MongoDB 驱动程序

从前一节的代码中，您可以看到我们在执行 MongoDB CRUD 任务时应该始终导入`MongoClient`，提供 MongoDB 服务器 URL、数据库名称等。这可能很繁琐且低效。让我们将前面的 MongoDB 连接代码抽象成一个类，以下是具体步骤：

1.  将数据库连接细节抽象到一个文件中：

```js
// server/config/mongodb.js
const database = {
  host: 'localhost',
  port: 27017,
  dbname: 'nuxt-app'
}

export default {
  host: database.host,
  port: database.port,
  dbname: database.dbname,
  url: 'mongodb://' + database.host + ':' + database.port
}
```

1.  创建一个`class`函数来构建数据库连接，这样我们在执行 CRUD 操作时就不必重复这个过程。我们还在`class`函数中构建了一个`objectId`属性，用于存储我们需要的`ObjectId`方法，以便解析来自客户端的 ID 数据，使这个 ID 数据从字符串变为对象：

```js
// server/mongo.js
import mongodb from 'mongodb'
import config from './config/mongodb'

const MongoClient = mongodb.MongoClient

export default class Mongo {
  constructor () {
    this.connection = null
    this.objectId = mongodb.ObjectId
  }

  async connect () {
    this.connection = await MongoClient.connect(config.url, {
      useUnifiedTopology: true,
      useNewUrlParser: true
    })
    return this.connection.db(config.dbname)
  }

  close () {
    this.connection.close()
  }
}
```

1.  导入`class`并使用`new`语句进行实例化，如下所示：

```js
import Mongo from './mongo'
const mongo = new Mongo()
```

例如，我们可以在需要连接到 MongoDB 数据库执行 CRUD 操作的 API 路由中导入它，如下所示：

```js
// server/routes.js
import Router from 'koa-router'
import Mongo from './mongo'
const mongo = new Mongo()
const router = new Router({ prefix: '/api' })

router.post('/user', async (ctx, next) => {
  //...
})
```

在使用 MongoDB 驱动程序和我们的服务器端框架 Koa 创建 CRUD 操作之前，我们应该了解`ObjectId`和`ObjectId`方法。让我们开始吧。

## 理解 ObjectId 和 ObjectId 方法

`ObjectId`是 MongoDB 在集合中用作主键的快速生成且可能唯一的值。它由 12 个字节组成；时间戳占据前 4 个字节，记录了`ObjectId`值创建时的时间。它存储在集合中每个文档的唯一`_id`字段中。如果在注入文档时没有声明，此`_id`字段将自动生成。另一方面，`ObjectId(<十六进制>)`是我们可以使用的 MongoDB 方法，用于返回一个新的`ObjectId`值，并将`ObjectId`值从字符串解析为对象。这里有一个例子：

```js
// Pseudo code
var id = '5d2ba2bf089a7754e9094af5'
console.log(typeof id) // string
console.log(typeof ObjectId(id)) // object
```

在前面的伪代码中，您可以看到我们使用`ObjectId`方法创建的对象中的`getTimestamp`方法来从`ObjectId`值中获取时间戳。这里有一个例子：

```js
// Pseudo code
var object = ObjectId(id)
var timestamp = object.getTimestamp()
console.log(timestamp) // 2019-07-14T21:46:39.000Z
```

有关`ObjectId`和`ObjectId`方法的更多信息，请查看以下链接：

+   [`docs.mongodb.com/manual/reference/bson-types/#objectid`](https://docs.mongodb.com/manual/reference/bson-types/#objectid) 用于`ObjectId`

+   [`docs.mongodb.com/manual/reference/method/ObjectId/`](https://docs.mongodb.com/manual/reference/method/ObjectId/) 用于`ObjectId()`

现在，让我们在接下来的部分中使用 MongoDB 驱动程序编写一些 CRUD 操作。首先，我们将编写注入文档的操作。

## 注入一个文档

在开始之前，我们应该看一下我们将要创建的每个路由所需的代码结构：

```js
// server/routes.js
router.get('/user', async (ctx, next) => {
  let result
  try {
    const connection = await mongo.connect()
    const collectionUsers = connection.collection('users')
    result = await collectionUsers...
    mongo.close()
  } catch (err) {
    ctx.throw(500, err)
  }
  ctx.type = 'json'
  ctx.body = result
})
```

让我们讨论一下结构：

+   **捕获和抛出错误**：当我们使用`async/await`语句而不是`Promise`对象进行异步操作时，我们必须始终将它们包装在`try`/`catch`块中以处理错误。

```js
try {
  // async/await code
} catch (err) {
  // handle error
}
```

+   **连接到 MongoDB 数据库和集合**：在执行任何 CRUD 操作之前，我们必须建立连接并连接到我们想要操作的特定集合。在我们的情况下，集合是`users`：

```js
const connection = await mongo.connect()
const collectionUsers = connection.collection('users')
```

+   执行 CRUD 操作：这是我们使用 MongoDB API 方法读取、注入、更新和删除用户的地方：

```js
result = await collectionUsers...
```

+   **关闭 MongoDB 连接**：在执行 CRUD 操作后，我们必须确保关闭连接：

```js
mongo.close()
```

现在让我们使用前面的代码结构来在以下步骤中注入新用户：

1.  创建一个使用`post`方法来注入新用户文档的路由：

```js
// server/routes.js
router.post('/user', async (ctx, next) => {
  let result
  //...
})
```

1.  在`post`路由内，在执行与 MongoDB 的 CRUD 操作之前，对从客户端接收到的键和值进行检查：

```js
let body = ctx.request.body || {}

if (body.name === undefined) {
  ctx.throw(400, 'name is undefined')
}
if (body.slug === undefined) {
  ctx.throw(400, 'slug is undefined')
}
if (body.name === '') {
  ctx.throw(400, 'name is required')
}
if (body.slug === '') {
  ctx.throw(400, 'slug is required')
}
```

1.  在允许将新文档注入到“用户”集合之前，我们希望确保`slug`值尚不存在。为此，我们需要使用带有`slug`键的`findOne`API 方法。如果结果是积极的，那意味着`slug`值已被其他用户文档占用，因此我们向客户端抛出错误：

```js
const found = await collectionUsers.findOne({
  slug: body.slug
})
if (found) {
  ctx.throw(404, 'slug has been taken')
}
```

1.  如果`slug`是唯一的，那么我们使用`insertOne`API 方法来注入具有提供的数据的新文档：

```js
result = await collectionUsers.insertOne({
  name: body.name,
  slug: body.slug
})
```

在注入文档之后，我们需要做的下一件事是获取和查看我们已经注入的文档，这将在下一节中进行。

## 获取所有文档

在将用户添加到`users`集合后，我们可以通过在第八章中创建的路由中检索所有或其中一个用户。现在我们只需要重构它们，使用与上一节中获取数据库中真实数据相同的代码结构：

1.  重构使用`get`方法列出所有用户文档的路由：

```js
// server/routes.js
router.get('/users', async (ctx, next) => {
  let result
  //...
})
```

1.  在`get`路由内，使用`find`API 方法从`user`集合中获取所有文档：

```js
result = await collectionUser.find({
}, {
  // Exclude some fields
}).toArray()
```

如果要从查询结果中排除字段，请使用`projection`键和值`0`来表示不想在结果中显示的字段。例如，如果不希望在结果中的每个文档中看到`_id`字段，可以这样做：

```js
projection:{ _id: 0 }
```

1.  重构使用`get`方法获取用户文档的路由：

```js
// server/routes.js
router.get('/users/:id', async (ctx, next) => {
  let result
  //...
})
```

1.  使用`findOne`方法通过`_id`获取单个文档。我们必须使用`ObjectId`方法解析`id`字符串，我们在`constructor`函数中的`class`函数中有一个副本，名为`objectId`：

```js
const id = ctx.params.id
result = await collectionUsers.findOne({
  _id: mongo.objectId(id)
}, {
  // Exclude some fields
})
```

`mongo.objectId(id)`方法将`id`字符串解析为`ObjectID`对象，然后我们可以使用它来从集合中查询文档。现在我们可以获取我们创建的文档，接下来需要做的是更新它们。让我们在下一节中进行。

## 更新一个文档

在将用户添加到`users`集合后，我们还可以在以下步骤中使用与上一节中相同的代码结构来更新它们：

1.  创建一个带有`put`方法的路由，用于更新现有用户文档如下：

```js
// server/routes.js
router.put('/user', async (ctx, next) => {
  let result
  //...
})
```

1.  在更新文档之前，我们希望确保`slug`值是唯一的。因此，在`put`路由内，我们使用`findOne` API 和`$ne`来排除我们正在更新的文档。如果没有匹配项，那么我们将使用`updateOne` API 方法来更新文档：

```js
const found = await collectionUser.findOne({
  slug: body.slug,
  _id: { $ne: mongo.objectId(body.id) }
})
if (found) {
  ctx.throw(404, 'slug has been taken')
}

result = await collectionUser.updateOne({
  _id: mongo.objectId(body.id)
}, {
   $set: { name: body.name, slug: body.slug },
   $currentDate: { lastModified: true }
})
```

我们在这个 CRUD 操作中使用了三个操作符：`$set`操作符，`$currentDate`操作符和`$ne`选择器。这些是您经常用于更新文档的一些更新操作符和查询选择器：

+   **更新操作符**：`$set`操作符用于以以下格式替换字段的值为新指定的值：

```js
{ $set: { <field1>: <value1>, ... } }
```

`$currentDate`操作符用于将当前日期设置为指定字段，可以是 BSON 日期类型（默认）或 BSON 时间戳类型，格式如下：

```js
{ $currentDate: { <field1>: <typeSpecification1>, ... } }
```

有关这两个和其他更新操作符的更多信息，请访问[`docs.mongodb.com/manual/reference/operator/update/`](https://docs.mongodb.com/manual/reference/operator/update/)。

+   **查询选择器**：`$ne`选择器用于选择字段值不等于指定值的文档，包括那些不包含该字段的文档。以下是一个例子：

```js
db.user.find( { age: { $ne: 18 } } )
```

此查询将选择`user`集合中所有`age`字段值不等于`18`的文档，包括那些不包含`age`字段的文档。

有关此及其他查询选择器的更多信息，请访问[`docs.mongodb.com/manual/reference/operator/query/`](https://docs.mongodb.com/manual/reference/operator/query/)。

现在，让我们看看如何在下一节中删除我们创建的文档。

## 删除一个文档

最后，我们还将使用与上一节相同的代码结构来从`users`集合中删除现有用户，步骤如下：

1.  创建一个带有`del`方法的路由来删除现有用户文档：

```js
// server/routes.js
router.del('/user', async (ctx, next) => {
  let result
  //...
})
```

1.  在`del`路由内使用`deleteOne` API 方法删除文档之前，我们通常会使用`findOne` API 方法来查找`user`集合中的文档，以确保我们首先拥有它：

```js
let body = ctx.request.body || {}
const found = await collectionUser.findOne({
  _id: mongo.objectId(body.id)
})
if (!found) {
  ctx.throw(404, 'no user found')
}

result = await collectionUser.deleteOne({
  _id: mongo.objectId(body.id)
})
```

干得好！您已成功编写了 MongoDB CRUD 操作并将其集成到 API（Koa）中。本章的最后一部分涉及将这些操作与 Nuxt 页面集成。让我们在下一节中进行。

# 与 Nuxt 页面集成

我们已经准备好了服务器端，现在我们需要在客户端上创建用户界面，以便我们可以发送和获取数据。我们将在`/pages/users/`目录中创建三个新页面。这是我们的结构：

```js
users
├── index.vue
├── _id.vue
├── add
│ └── index.vue
├── update
│ └── _id.vue
└── delete
  └── _id.vue
```

一旦我们有了结构，我们就准备好在接下来的部分从 Nuxt 端（客户端）创建页面并编写 CRUD 任务。让我们从下一部分的*创建* CRUD 任务开始。

## 创建一个添加新用户的添加页面

我们将按照以下步骤创建此页面与服务器端的`POST`路由`/api/user/`进行通信，以添加新用户：

1.  在`<template>`块中创建一个表单来收集新用户数据，如下所示：

```js
// pages/users/add/index.vue
<form v-on:submit.prevent="add">
  <p>Name: <input v-model="name" type="text" name="name"></p>
  <p>Slug: <input v-model="slug" type="text" name="slug"></p>
  <button type="submit">Add</button>
  <button v-on:click="cancel">Cancel</button>
</form>
```

1.  在`<script>`块中创建一个`add`方法来将数据发送到服务器，并创建一个`cancel`方法来取消表单，如下所示：

```js
// pages/users/add/index.vue
export default {
  methods: {
    async add () {
      let { data } = await axios.post('/api/user/', {
        name: this.name,
        slug: this.slug,
      })
    },
    cancel () {
      this.$router.push('/users/')
    }
  }
}
```

通过这两个步骤，我们已经成功在客户端（Nuxt）与服务器端（API）上建立了*创建* CRUD 任务。现在，您可以使用刚刚创建的表单从客户端在`localhost:3000/users/add`向数据库添加新用户，并将它们发送到 API 的`POST`路由`localhost:3000/api/user/`。在能够添加新用户之后，我们应该继续在客户端进行*更新* CRUD 任务。让我们开始吧。

## 为更新现有用户创建一个更新页面

更新页面基本上与添加页面非常相似。此页面将与服务器端的`PUT`路由`/api/user/`通信，以更新现有用户，步骤如下：

1.  在`<template>`块中创建一个表单来显示现有数据并收集新数据。更新页面的区别在于我们绑定到`<form>`元素的方法：

```js
// pages/users/update/_id.vue
<form v-on:submit.prevent="update">
  //...
  <button type="submit">Update</button>
</form>
```

1.  在`<script>`块中创建一个`update`方法来将数据发送到服务器。我们将使用`asyncData`方法来获取现有数据，如下所示：

```js
// pages/users/update/_id.vue
export default {
  async asyncData ({ params, error }) {
    let { data } = await axios.get('/api/users/' + params.id)
    let user = data.data
    return { 
      id: user._id, 
      name: user.name, 
      slug: user.slug,
    }
  },
  methods: {
    async update () {
      let { data } = await axios.put('/api/user/', {
        name: this.name,
        slug: this.slug,
        id: this.id,
      })
    }
  }
}
```

同样，在客户端（Nuxt）与服务器端（API）上成功建立了更新 CRUD 任务的这两个步骤。现在，您可以使用表单从客户端在`localhost:3000/users/update`更新数据库中的现有用户，并将它们发送到 API 的`PUT`路由`localhost:3000/api/user/`。在能够更新用户之后，我们现在应该继续在客户端进行*删除* CRUD 任务。让我们开始吧。

## 创建一个删除页面来删除现有用户

此页面将与服务器端的`DELETE`路由`/api/user/`通信，以删除现有用户：

1.  在`<template>`块中创建一个`<button>`元素，我们可以使用它来删除文档。我们不需要一个表单来发送数据，因为我们可以在`remove`方法中收集数据（仅为文档`_id`数据）。我们只需要按钮来触发这个方法，如下所示：

```js
// pages/users/delete/_id.vue
<button v-on:click="remove">Delete</button>
```

1.  创建`remove`方法，将数据发送到服务器，就像我们在`<script>`块中解释的那样。但首先，我们需要使用`asyncData`方法来获取现有数据。

```js
// pages/users/delete/_id.vue
export default {
 async asyncData ({ params, error }) {
    // Fetch the existing user
    // Same as in update page
  },
  methods: {
    async remove () {
      let payload = { id: this.id }
      let { data } = await axios.delete('/api/user/', {
        data: payload,
      })
    }
  }
}
```

最后，我们已经成功在客户端（Nuxt）和服务器端（API）上完成了*删除* CRUD 任务的两个步骤。现在，您可以通过在`localhost:3000/users/delete`发送用户数据（仅为 ID），并将其发送到 API 的`DELETE`路由`localhost:3000/api/user/`，从客户端删除数据库中的现有用户。因此，如果您使用`npm run dev`启动应用程序，您应该可以在`localhost:3000`上看到它运行。

导航到以下路由以添加、更新、读取和删除用户：

+   `localhost:3000/users` 用于读取/列出所有用户

+   `localhost:3000/users/add` 用于添加新用户

+   `localhost:3000/users/update/<id>` 用于按 ID 更新现有用户

+   `localhost:3000/users/delete/<id>` 用于按 ID 删除现有用户

干得好！您终于成功完成了本章设定的里程碑。对于初学者来说，MongoDB 可能是一个令人难以置信的主题，但是如果您按照本章中设定的指南和里程碑进行操作，您可以轻松创建一个相当不错的 API。当您需要超越我们在本书中解释的 CRUD 操作时，请使用我们提供的链接。现在让我们总结一下您在本章学到的内容。

您可以在我们的 GitHub 存储库的`/chapter-9/nuxt-universal/koa-mongodb/axios/`中找到我们为本章创建的代码。

# 总结

在本章中，您已经学会了如何在本地计算机上安装 MongoDB，并在 MongoDB Shell 上使用一些基本的 MongoDB 查询进行 CRUD 操作。您还学会了如何安装和使用 MongoDB 驱动程序来从服务器端框架连接到 MongoDB，并编写了在 Koa 环境中执行 CRUD 操作的代码。最后，您已经从客户端 Nuxt 创建了前端页面，用于向 MongoDB 数据库添加新用户，并通过与使用 Koa 开发的 API 进行通信来更新和删除现有用户。

在下一章中，我们将探索 Vuex 存储并在 Nuxt 应用程序中使用它。在安装和编写 Vue 应用程序中的简单 Vuex 存储之前，您将了解 Vuex 架构。您还将学习有关 Vuex 核心概念的知识，包括状态、获取器、操作和模块，然后使用这些概念在 Nuxt 应用程序中编写 Vuex 存储。我们将引导您完成这些内容，敬请关注。
