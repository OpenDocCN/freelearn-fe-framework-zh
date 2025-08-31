# Reason 中的 JSON

在本章中，我们将通过构建一个简单的客户管理应用程序来学习如何使用 JSON。此应用程序位于我们现有应用程序的 `/customers` 路径中，可以创建、读取和更新客户。JSON 数据持久化到 `localStorage`。在本章中，我们将以两种不同的方式将外部 JSON 转换为 Reason 可以理解的类型化数据结构：

+   使用纯粹的理由

+   使用 `bs-json` 库

我们将在本章末尾比较和对比每种方法。我们还将讨论 **GraphQL** 如何帮助在静态类型语言（如 Reason）中处理 JSON 时提供愉快的开发者体验。

要跟随构建客户管理应用程序，请克隆本书的 GitHub 仓库并从 `Chapter07/app-start` 开始：

```js
git clone https://github.com/PacktPublishing/ReasonML-Quick-Start-Guide.git
cd ReasonML-Quick-Start-Guide
cd Chapter07/app-start
npm install
```

在本章中，我们将探讨以下主题：

+   构建视图

+   与 localStorage 集成

+   使用 bs-json

+   使用 GraphQL

# 构建视图

总共，我们将有三个视图：

+   列表视图

+   创建视图

+   更新视图

每个视图都有自己的路由。创建和更新视图共享一个公共组件，因为它们非常相似。

# 文件结构

由于我们的 `bsconfig.json` 包含子目录，我们可以创建一个 `src/customers` 目录来存放相关组件，BuckleScript 将递归地在 `src` 的子目录中查找 Reason（和 OCaml）文件：

```js
/* bsconfig.json */
...
"sources": {
  "dir": "src",
  "subdirs": true
},
...
```

让我们继续并将 `src/Page1.re` 组件重命名为 `src/customers/CustomerList.re`。在同一个目录中，我们稍后创建 `Customer.re`，它将用于创建和更新单个客户。

# 更新路由和导航菜单

在 `Router.re` 中，我们将用以下内容替换 `/page1` 路由：

```js
/* Router.re */
let routes = [
  ...
  {href: "/customers", title: "Customer List", component: <CustomerList />}
  ...
];
```

我们还将添加 `/customers/create` 和 `/customers/:id` 的路由：

```js
/* Router.re */
let routes = [
  ...
  {href: "/customers/create", title: "Create Customer", component: <Customer />,},
  {href: "/customers/:id", title: "Update Customer", component: <Customer />}
  ...
];
```

路由已更新，以便它可以处理路由变量（例如 `/customers/:id`）。此更改已在 `Chapter07/app-start` 中为您完成。

最后，务必更新 `<App.re />` 中的导航菜单：

```js
/* App.re */
render: self =>
  ...
  <ul>
    <li>
      <NavLink href="/customers">
        {ReasonReact.string("Customers")}
      </NavLink>
    </li>
  ...
```

# CustomerType.re

此文件将包含 `<CustomerList />` 和 `<Customer />` 都使用的客户类型。这样做是为了避免任何循环依赖编译器错误：

```js
/* CustomerType.re */
type address = {
  street: string,
  city: string,
  state: string,
  zip: string,
};

type t = {
  id: int,
  name: string,
  address,
  phone: string,
  email: string,
};
```

# CustomerList.re

目前，我们将使用硬编码的客户数组。很快，我们将从 `localStorage` 中检索这些数据。以下组件渲染一个样式化的客户数组。每个客户都被 `<Link />` 组件包裹。点击客户将导航到更新视图：

```js
let component = ReasonReact.statelessComponent("CustomerList");

let customers: array(CustomerType.t) = [
  {
    id: 1,
    name: "Christina Langworth",
    address: {
      street: "81 Casey Stravenue",
      city: "Beattyview",
      state: "TX",
      zip: "57918",
    },
    phone: "877-549-1362",
    email: "Christina.Langworth@gmail.com",
  },
  ...
];

module Styles = {
  open Css;

  let list =
    style([
      ...
    ]);
};

let make = _children => {
  ...component,
  render: _self =>
    <div>
      <ul className=Styles.list>
        {
          ReasonReact.array(
            Belt.Array.map(customers, customer =>
              <li key={string_of_int(customer.id)}>
                <Link href={"/customers/" ++ string_of_int(customer.id)}>
                  <p> {ReasonReact.string(customer.name)} </p>
                  <p> {ReasonReact.string(customer.address.street)} </p>
                  <p> {ReasonReact.string(customer.phone)} </p>
                  <p> {ReasonReact.string(customer.email)} </p>
                </Link>
              </li>
            )
          )
        }
      </ul>
    </div>,
};
```

# Customer.re

此还原组件渲染一个表单，其中每个客户字段都可以在输入元素内编辑。组件有两个模式——`Create` 和 `Update`——基于 `window.location.pathname`。

我们首先绑定到 `window.location.pathname`，并定义组件的动作和状态：

```js
/* Customer.re */
[@bs.val] external pathname: string = "window.location.pathname";

type mode =
  | Create
  | Update;

type state = {
  mode,
  customer: CustomerType.t,
};

type action =
  | Save(ReactEvent.Form.t);

let component = ReasonReact.reducerComponent("Customer");
```

接下来，我们使用 `bs-css` 添加我们的组件样式。要查看样式，请查看 `Chapter07/app-end/src/customers/Customer.re`：

```js
/* Customer.re */
module Styles = {
  open Css;

  let form =
    style([
      ...
    ]);
};
Chapter07/app-end/src/customers/Customer.re:
```

```js
/* Customer.re */
let customers: array(CustomerType.t) = [|
  {
    id: 1,
    name: "Christina Langworth",
    address: {
      street: "81 Casey Stravenue",
      city: "Beattyview",
      state: "TX",
      zip: "57918",
    },
    phone: "877-549-1362",
    email: "Christina.Langworth@gmail.com",
  },
  ...
|];
```

我们还提供了辅助函数，以下是一些原因：

+   从 `window.location.pathname` 提取客户 ID

+   要通过 ID 获取客户

+   要生成默认客户：

```js
let getId = pathname =>
  try (Js.String.replaceByRe([%bs.re "/\\D/g"], "", pathname)->int_of_string) {
  | _ => (-1)
  };

let getCustomer = customers => {
  let id = getId(pathname);
  customers |> Js.Array.find(customer => customer.CustomerType.id == id);
};

let getDefault = customers: CustomerType.t => {
  id: Belt.Array.length(customers) + 1,
  name: "",
  address: {
    street: "",
    city: "",
    state: "",
    zip: "",
  },
  phone: "",
  email: "",
};
```

当然，以下是我们组件的 `make` 函数：

```js
let make = _children => {
  ...component,
  initialState: () => {
    let mode = Js.String.includes("create", pathname) ? Create : Update;
    {
      mode,
      customer:
        switch (mode) {
        | Create => getDefault(customers)
        | Update =>
          Belt.Option.getWithDefault(
            getCustomer(customers),
            getDefault(customers),
          )
        },
    };
  },
  reducer: (action, state) =>
    switch (action) {
    | Save(event) =>
      ReactEvent.Form.preventDefault(event);
      ReasonReact.Update(state);
    },
  render: self =>
    <form
      className=Styles.form
      onSubmit={
        event => {
          ReactEvent.Form.persist(event);
          self.send(Save(event));
        }
      }>
      <label>
        {ReasonReact.string("Name")}
        <input type_="text" defaultValue={self.state.customer.name} />
      </label>
      <label>
        {ReasonReact.string("Street Address")}
        <input
          type_="text"
          defaultValue={self.state.customer.address.street}
        />
      </label>
      <label>
        {ReasonReact.string("City")}
        <input type_="text" defaultValue={self.state.customer.address.city} />
      </label>
      <label>
        {ReasonReact.string("State")}
        <input type_="text" defaultValue={self.state.customer.address.state} />
      </label>
      <label>
        {ReasonReact.string("Zip")}
        <input type_="text" defaultValue={self.state.customer.address.zip} />
      </label>
      <label>
        {ReasonReact.string("Phone")}
        <input type_="text" defaultValue={self.state.customer.phone} />
      </label>
      <label>
        {ReasonReact.string("Email")}
        <input type_="text" defaultValue={self.state.customer.email} />
      </label>
      <input
        type_="submit"
        value={
          switch (self.state.mode) {
          | Create => "Create"
          | Update => "Update"
          }
        }
      />
    </form>,
};
```

`Save` 动作还没有保存到 `localStorage`。当导航到 `/customers/create` 时，表单是空的，当导航到例如 `/customers/1` 时，表单会被填充。

# 与 localStorage 集成

让我们创建一个单独的模块来与数据层交互，我们将它称为 `DataPureReason.re`。在这里，我们公开 `localStorage.getItem` 和 `localStorage.setItem` 的绑定，以及一个解析函数，用于将 JSON 字符串解析为之前定义的 `CustomerType.t` 记录。

# 填充 localStorage

你可以在 `Chapter07/app-end/src/customers/data.json` 中找到一些初始数据。请在浏览器控制台中运行 `localStorage.setItem("customers", JSON.stringify(/* paste JSON data here */))` 以填充 `localStorage` 中的这些初始数据。

# DataPureReason.re

记得当 BuckleScript 绑定感觉有点神秘的时候吗？希望现在它们开始感觉稍微直接一些：

```js
[@bs.val] [@bs.scope "localStorage"] external getItem: string => string = "";
[@bs.val] [@bs.scope "localStorage"]
external setItem: (string, string) => unit = "";
```

要解析 JSON，我们将使用 `Js.Json` 模块。

Js.Json 文档可以在以下 URL 找到：

[`bucklescript.github.io/bucklescript/api/Js_json.html`](https://bucklescript.github.io/bucklescript/api/Js_json.html)

很快，你就会看到一种使用 `Js.Json` 模块解析 JSON 字符串的方法。不过有一个注意事项：这有点繁琐。但了解正在发生的事情以及为什么我们需要为像 Reason 这样的类型化语言做这件事是很重要的。从高层次上讲，我们将验证 JSON 字符串以确保它是有效的 JSON，如果是的话，使用 `Js.Json.classify` 函数将 JSON 字符串（`Js.Json.t`）转换为标签类型（`Js.Json.tagged_t`）。可用的标签如下：

```js
type tagged_t =
  | JSONFalse
  | JSONTrue
  | JSONNull
  | JSONString(string)
  | JSONNumber(float)
  | JSONObject(Js_dict.t(t))
  | JSONArray(array(t));
```

这样，我们可以将 JSON 字符串转换为 Reason 数据结构。

# 验证 JSON 字符串

上一节中定义的 `getItem` 绑定将返回一个字符串：

```js
let unvalidated = DataPureReason.getItem("customers");
```

我们可以这样验证 JSON 字符串：

```js
let validated =
  try (Js.Json.parseExn(unvalidated)) {
  | _ => failwith("Error parsing JSON string")
  };
```

如果 JSON 无效，它将生成一个运行时错误。在本章的结尾，我们将学习 GraphQL 如何帮助改善这种情况。

# 使用 Js.Json.classify

假设我们已经验证了以下 JSON（它是一个对象数组）：

```js
[
  {
    "id": 1,
    "name": "Christina Langworth",
    "address": {
      "street": "81 Casey Stravenue",
      "city": "Beattyview",
      "state": "TX",
      "zip": "57918"
    },
    "phone": "877-549-1362",
    "email": "Christina.Langworth@gmail.com"
  },
  {
    "id": 2,
    "name": "Victor Tillman",
    "address": {
      "street": "2811 Toby Gardens",
      "city": "West Enrique",
      "state": "NV",
      "zip": "40465"
    },
    "phone": "(502) 091-2292",
    "email": "Victor.Tillman30@gmail.com"
  }
]
```

现在我们已经验证了 JSON，我们准备对其进行分类：

```js
switch (Js.Json.classify(validated)) {
| Js.Json.JSONArray(array) =>
  Belt.Array.map(array, customer => ...)
| _ => failwith("Expected an array")
};
```

我们对 `Js.Json.tagged_t` 的可能标签进行模式匹配。如果它是一个数组，我们就会使用 `Belt.Array.map`（或 `Js.Array.map`）遍历它。否则，在我们的应用程序上下文中会得到一个运行时错误。

`map` 函数传递一个对数组中每个对象的引用。但 Reason 还不知道每个元素实际上是一个对象。在 `map` 内部，我们再次对数组中的每个元素进行分类。分类后，Reason 现在知道每个元素实际上是一个对象。我们将定义一个名为 `parseCustomer` 的自定义辅助函数，用于与 `map` 函数一起使用：

```js
switch (Js.Json.classify(validated)) {
| Js.Json.JSONArray(array) =>
  Belt.Array.map(array, customer => parseCustomer(customer))
| _ => failwith("Expected an array")
};

let parseCustomer = json =>
  switch (Js.Json.classify(json)) {
  | Js.Json.JSONObject(json) => (
      ...
    )
  | _ => failwith("Expected an object")
  };
```

现在，如果数组中的每个元素都是一个对象，我们希望返回一个新的记录。这个记录将是 `CustomerType.t` 类型。否则，我们会得到一个运行时错误：

```js
let parseCustomer = json =>
  switch (Js.Json.classify(json)) {
  | Js.Json.JSONObject(json) => (
      {
        id: ...,
        name: ...,
        address: ...,
        phone: ...,
        email: ...,
      }: CustomerType.t
    )
  | _ => failwith("Expected an object")
  };
```

现在，对于每个字段（即`id`、`name`、`address`等），我们使用`Js.Dict.get`来获取和分类每个字段：

`Js.Dict`的文档可以在以下 URL 找到：

[`bucklescript.github.io/bucklescript/api/Js.Dict.html`](https://bucklescript.github.io/bucklescript/api/Js.Dict.html)

```js
let parseCustomer = json =>
  switch (Js.Json.classify(json)) {
  | Js.Json.JSONObject(json) => (
      {
        id:
          switch (Js.Dict.get(json, "id")) {
          | Some(id) =>
            switch (Js.Json.classify(id)) {
            | Js.Json.JSONNumber(id) => int_of_float(id)
            | _ => failwith("Field 'id' should be a number")
            }
          | None => failwith("Missing field: id")
          },
        name:
          switch (Js.Dict.get(json, "name")) {
          | Some(name) =>
            switch (Js.Json.classify(name)) {
            | Js.Json.JSONString(name) => name
            | _ => failwith("Field 'name' should be a string")
            }
          | None => failwith("Missing field: name")
          },
        address:
          switch (Js.Dict.get(json, "address")) {
          | Some(address) =>
            switch (Js.Json.classify(address)) {
            | Js.Json.JSONObject(address) => {
                street:
                  switch (Js.Dict.get(address, "street")) {
                  | Some(street) =>
                    switch (Js.Json.classify(street)) {
                    | Js.Json.JSONString(street) => street
                    | _ => failwith("Field 'street' should be a string")
                    }
                  | None => failwith("Missing field: street")
                  },
                city: ...,
                state: ...,
                zip: ...,
              }
            | _ => failwith("Field 'address' should be a object")
            }
          | None => failwith("Missing field: address")
          },
        phone: ...,
        email: ...,
      }: CustomerType.t
    )
  | _ => failwith("Expected an object")
  };
```

查看`src/customers/DataPureReason.re`以获取完整的实现。`DataPureReason.rei`隐藏了实现细节，只暴露了`localStorage`绑定和一个解析函数。

哎呀，这有点繁琐，不是吗？不过现在完成了，我们可以将`CustomerList.re`和`Customer.re`中的硬编码客户数组替换为以下内容：

```js
let customers =
  DataBsJson.(parse(getItem("customers")));
```

目前一切顺利！JSON 数据正在动态地被拉取、解析，现在的工作方式与硬编码时相同。

# 写入 localStorage

现在，让我们添加创建和更新客户的功能。为此，我们需要将我们的 Reason 数据结构转换为 JSON。在接口文件`DataPureReason.rei`中，我们将暴露一个`toJson`函数：

```js
/* DataPureReason.rei */
let parse: string => array(CustomerType.t);
let toJson: array(CustomerType.t) => string;
```

然后我们将实现它：

```js
/* DataPureReason.re */
let customerToJson = (customer: CustomerType.t) => {
  let id = customer.id;
  let name = customer.name;
  let street = customer.address.street;
  let city = customer.address.city;
  let state = customer.address.state;
  let zip = customer.address.zip;
  let phone = customer.phone;
  let email = customer.email;

  {j|
    {
      "id": $id,
      "name": "$name",
      "address": {
        "street": "$street",
        "city": "$city",
        "state": "$state",
        "zip": "$zip"
      },
      "phone": "$phone",
      "email": "$email"
    }
  |j};
};

let toJson = (customers: array(CustomerType.t)) =>
  Belt.Array.map(customers, customer => customerToJson(customer))
  ->Belt.Array.reduce("[", (acc, customer) => acc ++ customer ++ ",")
  ->Js.String.replaceByRe([%bs.re "/,$/"], "", _)
  ++ "]"
     ->Js.String.split("/n", _)
     ->Js.Array.map(line => Js.String.trim(line), _)
     ->Js.Array.joinWith("", _);
```

然后我们将在`Customer.re`的 reducer 中使用`toJson`函数：

```js
reducer: (action, state) =>
  switch (action) {
  | Save(event) =>
    let getInputValue: string => string = [%raw
      (selector => "return document.querySelector(selector).value")
    ];
    ReactEvent.Form.preventDefault(event);
    ReasonReact.UpdateWithSideEffects(
      {
        ...state,
        customer: {
          id: state.customer.id,
          name: getInputValue("input[name=name]"),
          address: {
            street: getInputValue("input[name=street]"),
            city: getInputValue("input[name=city]"),
            state: getInputValue("input[name=state]"),
            zip: getInputValue("input[name=zip]"),
          },
          phone: getInputValue("input[name=phone]"),
          email: getInputValue("input[name=email]"),
        },
      },
      (
        self => {
          let customers =
            switch (self.state.mode) {
            | Create =>
              Belt.Array.concat(customers, [|self.state.customer|])
            | Update =>
              Belt.Array.setExn(
                customers,
                Js.Array.findIndex(
                  customer =>
                    customer.CustomerType.id == self.state.customer.id,
                  customers,
                ),
                self.state.customer,
              );
              customers;
            };

          let json = customers->DataPureReason.toJson;
          DataPureReason.setItem("customers", json);
        }
      ),
    );
  },
```

在 reducer 中，我们使用 DOM 中的值更新`self.state.customer`，然后调用一个更新`localStorage`的函数。现在，我们能够通过创建或更新客户来写入`localStorage`。导航到`/customers/create`以创建新客户，然后导航回`/customers`以查看您新添加的客户。点击客户以导航到更新视图，更新客户，点击更新按钮，然后刷新页面。

# 使用 bs-json

现在我们确切地了解了如何将 JSON 字符串转换为类型化的 Reason 数据结构，我们注意到这个过程有点繁琐。与 JavaScript 这样的动态语言相比，代码行数更多。此外，还有相当多的重复代码。作为替代方案，Reason 社区中的许多人已经采用了`bs-json`作为编码和解码 JSON 的“官方”解决方案。

让我们创建一个新的模块`DataBsJson.re`和一个新的接口文件`DataBsJson.rei`。我们将复制与`DataPureReason.rei`中完全相同的接口，这样我们就可以知道，一旦我们完成，我们就可以用`DataBsJson`替换所有对`DataPureReason`的引用，一切应该都能正常工作。

暴露的接口如下：

```js
/* DataBsJson.rei */
[@bs.val] [@bs.scope "localStorage"] external getItem: string => string = "";
[@bs.val] [@bs.scope "localStorage"]
external setItem: (string, string) => unit = "";

let parse: string => array(CustomerType.t);
let toJson: array(CustomerType.t) => string;
```

让我们关注`parse`函数：

```js
let parse = json =>
  json |> Json.parseOrRaise |> Json.Decode.array(customerDecoder);
```

在这里，我们接受与之前相同的 JSON 字符串，验证它，将其转换为`Js.Json.t`（通过`Json.parseOrRaise`），然后将结果传递给这个新的`Json.Decode.array(customerDecoder)`函数。`Json.Decode.array`将尝试将 JSON 字符串解码为数组，并使用一个名为`customerDecoder`的自定义函数解码数组的每个元素——我们将在下面看到：

```js
let customerDecoder = json =>
  Json.Decode.(
    (
      {
        id: json |> field("id", int),
        name: json |> field("name", string),
        address: json |> field("address", addressDecoder),
        phone: json |> field("phone", string),
        email: json |> field("email", string),
      }: CustomerType.t
    )
  );
```

`customerDecoder`函数接受与数组中每个元素关联的 JSON，并尝试将其解码为`CustomerType.t`类型的记录。这基本上与我们之前所做的是完全相同的，但它更加简洁，更容易阅读。正如你所看到的，我们还有一个名为`addressDecoder`的客户解码器，用于解码`CustomerType.address`类型：

```js
let addressDecoder = json =>
  Json.Decode.(
    (
      {
        street: json |> field("street", string),
        city: json |> field("city", string),
        state: json |> field("state", string),
        zip: json |> field("zip", string),
      }: CustomerType.address
    )
  );
```

注意自定义解码器是如何轻松组合的。每个记录字段都是通过调用`Json.Decode.field`，传递字段名称（在 JSON 方面），并传递一个`Json.Decode`函数来解码的，该函数最终将 JSON 字段转换为 Reason 可以理解的数据类型。

编码的工作方式类似，但顺序相反：

```js
let toJson = (customers: array(CustomerType.t)) =>
  customers->Belt.Array.map(customer =>
    Json.Encode.(
      object_([
        ("id", int(customer.id)),
        ("name", string(customer.name)),
        (
          "address",
          object_([
            ("street", string(customer.address.street)),
            ("city", string(customer.address.city)),
            ("state", string(customer.address.state)),
            ("zip", string(customer.address.zip)),
          ]),
        ),
        ("phone", string(customer.phone)),
        ("email", string(customer.email)),
      ])
    )
  )
  |> Json.Encode.jsonArray
  |> Json.stringify;
```

客户数组被映射，每个客户都被编码为一个 JSON 对象。结果是 JSON 对象的数组，然后被编码为 JSON，并转换为字符串。这比我们之前的实现要好得多。

在将`DataPureReason.re`中的相同`localStorage`绑定复制过来之后，我们的界面现在已经实现。在将所有对`DataPureReason`的引用替换为`DataBsJson`之后，我们看到我们的应用程序仍然在正常工作。

# 使用 GraphQL

在 2018 年 ReactiveConf 上，Sean Grove 关于 Reason 和 GraphQL 的演讲非常精彩，标题为*ReactiveMeetups w/ Sean Grove | ReasonML GraphQL.*以下是从这次演讲中摘录的内容，很好地总结了在 Reason 中使用 JSON 的问题和解决方案：

因此，我认为，在像 Reason 这样的类型语言中，当你想要与现实世界交互时，有三个真正非常大的问题。第一个是，将数据放入和从你的类型系统中取出所需的所有样板代码。

第二个是，即使你可以通过编程方式摆脱样板代码，你仍然会担心转换的准确性和安全性。

最后，即使你得到了所有这些，并且你绝对确信你已经捕捉到了所有的变化，有人仍然可以在你不知情的情况下从下面改变它。

每当服务器更改字段时，我们有多少次会得到一个变更日志？在一个理想的世界里，我们会。但大多数时候我们不会。我们只能反向工程我们的服务器发生了什么变化。

因此，我认为，为了以广泛适用的方式解决这个问题，我们想要四件事情：

1) 以编程方式访问 API 可以为我们提供的数据类型。

2) 保证安全的自动转换。

3) 我们希望有一个合同。我们希望服务器保证，如果它说一个字段不可为空，那么它们永远不会给我们空值。如果它们更改字段名称，那么我们立即知道，并且它们也知道。

4) 我们希望以编程方式实现所有这些。

那就是 GraphQL。

-Sean Grove

你可以在以下 URL 找到*ReactiveMeetups w/ Sean Grove | ReasonML GraphQL*的视频：

[`youtu.be/t9a-_VnNilE`](https://youtu.be/t9a-_VnNilE)

此外，这里是 ReactiveConf 的 YouTube 频道：

[`www.youtube.com/channel/UCBHdUnixTWymmXBIw12Y8Qg`](https://www.youtube.com/channel/UCBHdUnixTWymmXBIw12Y8Qg)

本书的内容并不深入探讨 GraphQL，但鉴于我们在讨论在 Reason 中使用 JSON，一个高级介绍似乎很合适。

# 什么是 GraphQL？

如果你属于 ReactJS 社区，那么你很可能已经听说过 GraphQL。GraphQL 是一种查询语言和运行时，我们可以用它来满足这些查询，它也是由 Facebook 创建的。使用 GraphQL，ReactJS 组件可以包含 GraphQL 片段，用于组件所需的数据——这意味着组件可以将 HTML、CSS、JavaScript 和其外部数据全部放在一个文件中。

# 当使用 GraphQL 时，我需要创建 JSON 解码器吗？

由于 GraphQL 对你的应用程序的外部数据了如指掌，GraphQL 客户端（`reason-apollo`）将为你自动生成解码器。当然，解码器必须自动生成，这样我们才能确信它们反映了外部数据的当前形状。这又是考虑在需要处理外部数据时使用 GraphQL 与你的 Reason 应用程序的一个原因。

# 摘要

只要我们在 Reason 中工作，类型系统就会阻止你遇到运行时类型错误。然而，当我们与外部世界交互——无论是 JavaScript 还是外部数据——我们就失去了这些保证。为了能够在 Reason 的边界内保留这些保证，我们需要在 Reason 外部使用事物时帮助类型系统。我们之前学习了如何在 Reason 中使用外部 JavaScript，在本章中我们学习了如何在 Reason 中使用外部数据。虽然编写解码器和编码器更具挑战性，但它与编写 JavaScript 绑定非常相似。最终，我们只是在告诉 Reason 外部事物的类型。使用 GraphQL，我们可以扩展 Reason 的边界以包括外部数据。当然，这会有权衡，没有什么是完美的，但绝对值得一试。

在下一章中，我们将探讨 Reason 上下文中的测试。我们应该编写哪些测试？我们应该避免哪些测试？我们还将探讨单元测试如何帮助我们改进本章中编写的代码。
