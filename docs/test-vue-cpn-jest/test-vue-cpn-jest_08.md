# 第八章：使用模块别名增强 Jest 配置

我们在 JavaScript 社区中拥有的模块管理器，主要是 ES 模块和 CommonJS，并不支持基于项目的路径。它们只支持我们自己模块的相对路径和 `node_modules` 文件夹的路径。当项目稍微增长时，通常会看到以下路径：

```js
import SomeComponent from "../../../../components/SomeComponent";
```

幸运的是，我们有不同的方法来应对这个问题，以便我们可以为相对于项目根目录的文件夹定义别名，这样我们可以将前一行写成如下形式：

```js
import SomeComponent from "@/components/SomeComponent";
```

在这里，`@` 是一个任意字符，用于定义根项目。您可以自行定义。让我们看看我们有哪些可用的解决方案来应用模块别名。让我们从**上一章**中离开的地方开始（[`github.com/alexjoverm/vue-testing-series/tree/test-slots`](https://github.com/alexjoverm/vue-testing-series/tree/test-slots)）。

# Webpack 别名

**Webpack 别名** ([`webpack.js.org/configuration/resolve/#resolve-alias`](https://webpack.js.org/configuration/resolve/#resolve-alias)) 设置非常简单。您只需要在 webpack 配置中添加一个 `resolve.alias` 属性。如果您查看 `build/webpack.base.conf.js`，您会发现它已经定义了：

```js
module.exports = {
  // ...
  resolve: {
    extensions: [".js", ".vue", ".json"],
    alias: {
      vue$: "vue/dist/vue.esm.js"
    }
  }
};
```

以此为起点，我们可以添加一个简单的别名，指向 `src` 文件夹，并将其用作根目录：

```js
module.exports = {
  // ...
  resolve: {
    extensions: [".js", ".vue", ".json"],
    alias: {
      vue$: "vue/dist/vue.esm.js",
      "@": path.join(__dirname, "..", "src")
    }
  }
};
```

仅凭此，我们就可以访问任何东西，以根项目作为 `@` 符号。让我们去 `src/App.vue` 并更改对这两个组件的引用：

```js
import MessageList from "@/components/MessageList";
import Message from "@/components/Message";
// ...
```

如果我们运行 `npm start` 并在 `localhost:8080` 打开浏览器，那应该可以直接使用。

然而，如果我们尝试通过运行 `npm t` 来运行测试，我们会发现 Jest 找不到模块。我们还没有配置 Jest 来做这个。因此，让我们去 `package.json`，Jest 配置所在的地方，并将 `"@/([^\\.]*)$": "<rootDir>/src/$1"` 添加到 `moduleNameMapper` 中：

```js
{
  "jest": {
    "moduleNameMapper": {
      "@(.*)$": "<rootDir>/src/$1",
      "^vue$": "vue/dist/vue.common.js"
    }
  }
}
```

以下是前面代码片段的解释：

+   `@(.*)$`：任何以 `@` 开头，并且继续以任何字符 (`(.*)$`) 直到字符串结束，通过使用括号进行分组。

+   `<rootDir>/src/$1`：`<rootDir>` 是 Jest 的特殊词，表示根目录。然后，我们将其映射到 `src`，并且使用 `$1`，我们将 `(.*)` 语句中的任何子句附加上去。

例如，当你从 `src` 或 `test` 文件夹导入时，`@/components/MessageList` 将被映射到 `../src/components/MessageList`。

就是这样。现在，你甚至可以更新你的`App.test.js`文件来使用别名，因为它也可以在测试中使用：

```js
import { shallowMount } from "@vue/test-utils";
import App from "@/App";
// ...
```

而且，它对`.vue`和`.js`文件都适用。

# 多个别名

经常使用多个别名以方便起见，所以不仅仅使用一个`@`来定义你的根文件夹，你可以使用多个。例如，假设你有一个`actions`文件夹和一个`models`文件夹。如果你为每个文件夹创建一个别名，然后移动文件夹，你只需要改变别名，而不是在代码库中更新所有对它的引用。这就是模块别名的威力 - 它们使得你的代码库更容易维护和更清晰。

现在，让我们在`build/webpack.base.conf.js`中添加一个`components`别名：

```js
module.exports = {
  // ...
  resolve: {
    extensions: [".js", ".vue", ".json"],
    alias: {
      vue$: "vue/dist/vue.esm.js",
      "@": path.join(__dirname, "..", "src"),
      components: path.join(__dirname, "..", "src", "components")
    }
  }
};
```

然后，我们只需要在`package.json`中的 Jest 配置中添加它：

```js
{
  "jest": {
    "moduleNameMapper": {
      "@(.*)$": "<rootDir>/src/$1",
      "components(.*)$": "<rootDir>/src/components/$1",
      "^vue$": "vue/dist/vue.common.js"
    }
  }
}
```

就是这么简单。现在，我们可以在`App.vue`中尝试使用两种形式：

```js
import MessageList from "components/MessageList";
import Message from "@/components/Message";
```

停止并重新运行测试，那应该可以工作。你也可以运行`npm start`来尝试一下。

# 其他解决方案

我看到了**babel-plugin-webpack-alias**（[`github.com/trayio/babel-plugin-webpack-alias`](https://github.com/trayio/babel-plugin-webpack-alias)），特别是用于其他测试框架，比如**mocha**（[`mochajs.org/`](https://mochajs.org/)），它没有模块映射。

我自己还没有尝试过，因为 Jest 已经为你提供了这个功能，但如果你已经这样做了，或者想尝试一下，请分享一下效果如何。

# 总结

添加模块别名非常简单，可以使你的代码库更清晰、更容易维护。Jest 也很容易定义它们；你只需要与 webpack 别名保持同步，然后你就可以告别点地狱的引用了。

你可以在**GitHub**上找到与本章相关的工作代码（[`github.com/alexjoverm/vue-testing-series/tree/Enhance-Jest-configuration-with-Module-Aliases`](https://github.com/alexjoverm/vue-testing-series/tree/Enhance-Jest-configuration-with-Module-Aliases)）。
