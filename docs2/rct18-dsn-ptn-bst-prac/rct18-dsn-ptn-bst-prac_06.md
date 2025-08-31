# 6

# 让你的组件看起来美观

我们对 React 最佳实践和设计模式的探索已经到了想要让我们的组件看起来美观的阶段。为了做到这一点，我们将探讨为什么常规 CSS 可能不是为组件设置样式的最佳方法，并检查各种替代解决方案。

从内联样式开始，然后是 CSS 模块和`styled-components`，本章将引导你进入 CSS 在 JavaScript 中的神奇世界。

在本章中，我们将涵盖以下主题：

+   规范 CSS 在规模上的常见问题

+   在 React 中使用内联样式意味着什么以及它们的缺点

+   如何使用 Webpack 和 CSS 模块从头开始设置项目

+   CSS 模块的特点以及为什么它们代表避免全局 CSS 的绝佳解决方案

+   `styled-components`，一个提供现代方法来设置 React 组件样式的库

# 技术要求

要完成本章，你需要以下内容：

+   Node.js 19+

+   Visual Studio Code

你可以在本书的 GitHub 仓库中找到本章的代码：[`github.com/PacktPublishing/React-18-Design-Patterns-and-Best-Practices-Fourth-Edition/tree/main/Chapter06`](https://github.com/PacktPublishing/React-18-Design-Patterns-and-Best-Practices-Fourth-Edition/tree/main/Chapter06)。

# CSS in JavaScript

2014 年 11 月，Christopher Chedeau，也被称为*vjeux*，在 NationJS 大会上发表了一次演讲（[`blog.vjeux.com/2014/javascript/react-css-in-js-nationjs.xhtml`](https://blog.vjeux.com/2014/javascript/react-css-in-js-nationjs.xhtml)），这引发了一场关于 React 组件样式的革命。作为 React 的贡献者和 Meta 的员工，Christopher 概述了 Facebook 在规模上遇到的许多 CSS 问题。了解这些问题很重要，因为它们在 Web 开发中很常见，并将帮助我们介绍内联样式和局部作用域的类名等概念。

以下是一个 CSS 问题的列表，这些问题基本上是 CSS 在规模上的问题：

+   全局命名空间

+   依赖项

+   死代码消除

+   压缩

+   共享常量

+   非确定性解析

+   隔离

CSS 的第一个众所周知的问题是所有选择器都是全局的。无论我们如何通过使用命名空间或**块**、**元素**、**修饰符**（**BEM**）方法等来组织样式，我们总是在污染全局命名空间，这是我们大家都知道是错误的。这不仅从原则上讲是错误的，而且还会导致大型代码库中许多错误，并且从长远来看，它使得可维护性变得非常困难。与大型团队合作时，了解特定的类或元素是否已经被样式化是非平凡的，而且大多数时候，我们倾向于添加更多类而不是重用现有的类。

CSS 的第二个问题与依赖关系的定义有关。实际上，很难清楚地说明特定的组件依赖于特定的 CSS，并且必须加载 CSS 才能应用样式。由于样式是全局的，任何文件中的任何样式都可以应用到任何元素上，失去控制很容易。

前端开发者倾向于使用预处理器将 CSS 分割成子模块，但最终，浏览器会生成一个大的、全局的 CSS 包。

由于 CSS 代码库往往很快就会变得很大，我们失去了对它们的控制，第三个问题与**死代码消除**有关。很难快速识别哪些样式属于哪个组件，这使得删除代码变得极其困难。实际上，由于 CSS 的级联性质，删除选择器或规则可能会在浏览器中产生意外的结果。

与 CSS 一起工作的另一个痛点涉及选择器和类名的压缩，这在 CSS 和 JavaScript 应用程序中都是如此。这看起来可能是一个简单的任务，但实际上并非如此，尤其是在类名在客户端动态应用或连接时；这是第四个问题。

无法压缩和优化类名会对性能产生不利影响，并且可以极大地影响 CSS 的大小。另一个在常规 CSS 中非平凡的常见操作是在样式和客户端应用程序之间共享常量。例如，我们经常需要知道标题的高度，以便重新计算依赖于它的其他元素的位置。

通常，我们使用 JavaScript API 在客户端读取值，但最佳解决方案是共享常量并避免在运行时进行昂贵的计算。这代表了 `vjeux` 和 Facebook 的其他开发者试图解决的第五个问题。

第六个问题与 CSS 的非确定性解析有关。实际上，在 CSS 中，顺序很重要，如果 CSS 是按需加载的，则顺序无法保证，这会导致错误样式被应用到元素上。

假设，例如，我们想要优化请求 CSS 的方式，只有在用户导航到特定页面时才加载与该页面相关的 CSS。如果与最后一个页面相关的 CSS 中有一些规则也适用于不同页面的元素，那么它最后被加载的事实可能会影响应用程序其余部分的样式。例如，如果用户返回到上一个页面，他们可能会看到一个与第一次访问时略有不同的 UI 页面。

控制所有各种样式、规则和导航路径的组合非常困难，但再次强调，能够在需要时加载 CSS 可以对 Web 应用的性能产生关键影响。

最后但同样重要的是，根据克里斯托弗·舍代乌的说法，CSS 的第七个问题是与隔离相关的。在 CSS 中，几乎不可能在文件或组件之间实现适当的隔离。选择器是全局的，并且很容易被覆盖。仅通过知道应用于元素的类名来预测元素的最终样式是困难的，因为样式不是隔离的，应用程序其他部分的规则可能会影响无关的元素。这可以通过使用内联样式来解决。

在下一节中，我们将探讨使用 React 中的内联样式意味着什么，以及它的优点和缺点。

# 理解和实现内联样式

官方的 React 文档建议开发者使用 **内联样式** 来样式化他们的 React 组件。这似乎很奇怪，因为我们都知道在过去的几年里，关注点的分离很重要，我们不应该混合标记和 CSS。

React 通过将关注点的分离从技术的分离转变为组件的分离，试图改变关注点分离的概念。当标记、样式和逻辑紧密耦合，并且一个不能在没有另一个的情况下工作时，将它们分离到不同的文件中只是一个幻象。即使这有助于保持项目结构更清晰，但它并不提供任何真正的益处。

在 React 中，我们通过组合组件来创建应用程序，其中组件是我们结构的基本单元。我们应该能够将组件移动到应用程序的任何地方，并且它们应该在逻辑和 UI 方面提供相同的结果，无论它们在哪里渲染。

这就是为什么在 React 中将样式与我们的组件一起放置，并在元素上使用内联样式应用它们是有意义的其中一个原因。

首先，让我们看看如何使用 React 中节点的 `style` 属性来对我们的组件应用样式的一个例子。我们将创建一个带有文本 `点击我！` 的按钮，并给它应用颜色和背景颜色：

```js
const style = {
  color: 'palevioletred',
  backgroundColor: 'papayawhip'
}

const Button = () => <button style={style}>Click me!</button> 
```

如您所见，在 React 中使用内联样式来样式化元素非常简单。我们只需要创建一个对象，其中属性是 CSS 规则，值是我们会在常规 CSS 文件中使用的值。

唯一的不同之处在于，连字符 CSS 规则必须是 `camelCased` 才符合 JavaScript 规范，并且值是字符串，因此它们必须用引号括起来。

关于供应商前缀有一些例外。例如，如果我们想在 `webkit` 上定义一个过渡效果，我们应该使用 `WebkitTransition` 属性，其中 `webkit` 前缀以大写字母开头。这个规则适用于所有供应商前缀，除了 `ms`，它是小写的。

其他用例是数字——它们可以不带引号或单位来编写，默认情况下，它们被视为像素。

以下规则应用了 100 像素的高度：

```js
const style = {
  height: 100
} 
```

通过使用内联样式，我们还可以做一些用常规 CSS 难以实现的事情。例如，我们可以在客户端运行时重新计算一些 CSS 值，这是一个非常强大的概念，你将在下面的示例中看到。

假设你想创建一个表单字段，其字体大小会根据其值而改变。所以，如果字段的值是`24`，字体大小将是 24 像素。使用正常的 CSS，这种行为几乎不可能实现，除非投入巨大的努力和重复的代码。

让我们来看看使用内联样式有多简单，首先创建一个`FontSize`组件，然后声明一个值状态：

```js
import { useState, ChangeEvent } from 'react'
const FontSize = () => {
  const [value, setValue] = useState<number>(16)
}
export default FontSize 
```

我们实现了一个简单的更改处理程序，其中我们使用事件的`target`属性来检索字段的当前值：

```js
const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
  setValue(Number(e.target.value))
} 
```

最后，我们渲染数字类型的输入文件，它是一个受控组件，因为我们通过使用状态来保持其值的更新。它还有一个事件处理程序，每当字段值改变时都会触发。

最后但同样重要的是，我们使用字段的`style`属性来设置其字体大小值。正如你所见，我们正在使用 CSS 规则的`camelCased`版本来遵循 React 约定：

```js
return (
  <input
    type="number"
    value={value}
    onChange={handleChange}
    style={{ fontSize: value }}
  />
) 
```

渲染前面的组件，我们可以看到一个输入字段，其字体大小会根据其值而改变。其工作原理是，当值改变时，我们将字段的新值存储在状态中。修改状态会强制组件重新渲染，我们使用新的状态值来设置字段的显示值及其字体大小；这既简单又强大。

计算机科学中的每个解决方案都有其缺点，并且总是代表一种权衡。在内联样式中，不幸的是，问题很多。

例如，使用内联样式，无法使用伪选择器（例如，`:hover`）和伪元素，这对于创建具有交互和动画的 UI 来说是一个相当大的限制。

有一些解决方案，例如，你总是可以创建真实元素而不是伪元素，但对于伪类，必须使用 JavaScript 来模拟 CSS 行为，这并不是最优的。

同样适用于**媒体查询**，无法使用内联样式定义，这使得创建响应式 Web 应用更加困难。由于样式是使用 JavaScript 对象声明的，因此也无法使用`style`回退：

```js
display: -webkit-flex;
display: flex; 
```

JavaScript 对象不能有两个具有相同名称的属性。应该避免使用样式回退，但如果有需要，始终具备使用它们的能力是好的。

CSS 的另一个特性是内联样式无法模拟，那就是**动画**。这里的解决方案是全局定义动画，并在元素的 style 属性中使用它们。使用内联样式时，每当我们需要用常规 CSS 覆盖样式时，我们总是被迫使用`!important`关键字，这不符合最佳实践，因为它会阻止其他样式应用于该元素。

与内联样式一起工作最困难的事情是调试。我们倾向于使用类名在浏览器 DevTools 中查找元素以进行调试和检查应用了哪些样式。使用内联样式时，所有项目的样式都列在它们的`style`属性中，这使得检查和调试结果变得非常困难。

例如，本节中我们创建的按钮是以以下方式渲染的：

```js
<button style="color:palevioletred;background-color:papayawhip;">Click me!</button> 
```

单独来看，它似乎并不难读，但如果你想象有成百上千个元素和风格，你就会意识到问题变得非常复杂。

此外，如果你正在调试一个每个项目都有相同`style`属性的列表，并且如果你在浏览器中实时修改一个以检查结果，你会看到你只应用了样式到它，而没有应用到所有其他兄弟元素上，即使它们共享相同的样式。

最后但同样重要的是，如果我们将应用渲染在服务器端（我们将在*第十二章*，*服务器端渲染*）中讨论这个话题），使用内联样式时页面的大小会更大。

使用内联样式，我们将 CSS 的全部内容放入了标记中，这给发送给客户端的文件增加了额外的字节数，使得网络应用看起来运行得更慢。压缩算法可以解决这个问题，因为它们可以轻松压缩相似的模式，在某些情况下，加载关键路径 CSS 是一个不错的解决方案；但总的来说，我们应该尽量避免这样做。

结果表明，内联样式带来的问题比它们试图解决的问题还要多。因此，社区创建了不同的工具来解决内联样式的问题，同时保持样式在组件内部或局部，以获得两全其美的效果。

在 Christopher Chedeau 的演讲之后，许多开发者开始讨论内联样式，并提出了许多解决方案和实验，以寻找在 JavaScript 中编写 CSS 的新方法。最初，有二三种解决方案，而如今已有超过 40 种。

在下一节中，我们将学习如何使用 CSS 模块。

# 使用 CSS 模块

如果你认为内联样式不适合你的项目或团队，但你仍然希望将样式尽可能靠近你的组件，有一个解决方案适合你，称为**CSS 模块**。CSS 模块是 CSS 文件，其中所有类名和动画名默认都是本地作用域的。让我们看看我们如何在项目中使用它们；但首先，我们需要配置**webpack**。

## Webpack 5

在深入研究 CSS 模块并了解它们是如何工作之前，了解它们的创建过程以及支持它们的工具非常重要。

在*第三章*，*清理你的代码*中，我们探讨了如何使用 Babel 及其预设来编写 ES6 代码并进行转换。一旦应用程序增长，你可能还想将你的代码库拆分成模块。

你可以使用 Webpack 或 Browserify 将应用程序分割成小模块，你可以在需要时导入它们，同时仍然为浏览器创建一个大包。这些工具被称为**模块打包器**，它们所做的是将你的应用程序的所有依赖项加载到一个单独的包中，这个包可以在浏览器中执行，而浏览器（目前）还没有模块的概念。

在 React 的世界里，Webpack 特别受欢迎，因为它提供了许多有趣和有用的功能，其中第一个就是加载器的概念。使用 Webpack，你可以潜在地加载除 JavaScript 之外的所有依赖项，如果存在相应的加载器的话。例如，你可以加载 JSON 文件，以及图片和其他资产到包中。

2015 年 5 月，CSS 模块的创造者之一 Mark Dalgleish 发现，你还可以在 Webpack 包中导入 CSS，并将这一概念推进了一步。他认为，由于 CSS 可以本地导入到组件中，所有导入的类名也可以本地作用域化，这真是太好了，因为这将会隔离样式。

在追踪了 CSS 模块本地作用域概念演变的一位先驱者 Mark Dalgleish 的思路，并理解了它是如何革命性地改变 Webpack 包中的样式隔离之后，让我们过渡到一个更实际的领域。下一节将指导我们设置一个利用这些原则的项目。

# 设置项目

在本节中，我们将探讨如何设置一个非常简单的 Webpack 应用程序，使用 Babel 将 JavaScript 和 CSS 模块转换为加载本地作用域的 CSS 到包中。我们还将了解 CSS 模块的所有功能，并查看它们可以解决的问题。首先，我们需要移动到一个空文件夹并运行以下命令：

```js
npm init 
```

这将创建一个包含一些默认设置的`package.json`文件。

现在，是时候安装依赖项了，首先是 Webpack，其次是`webpack-dev-server`，我们将使用它来本地运行应用程序并动态创建包：

```js
npm install --save-dev webpack webpack-dev-server webpack-cli 
```

一旦 Webpack 安装完成，就是安装 Babel 及其加载器的时候了。由于我们使用 Webpack 来创建包，因此我们将在 Webpack 本身中使用 Babel 加载器来转译我们的 ES6 代码：

```js
npm install --save-dev @babel/core @babel/preset-env @babel/preset-react ts-loader 
```

最后，我们需要安装`style-loader`和 CSS 加载器，这两个加载器是我们启用 CSS 模块所必需的：

```js
npm install --save-dev style-loader css-loader 
```

为了使事情更简单，我们还需要做一件事，那就是安装`html-webpack-plugin`，这是一个插件，可以创建一个 HTML 页面来即时托管我们的 JavaScript 应用程序，只需查看 Webpack 配置，而无需我们创建常规文件。此外，我们还需要安装`fork-ts-checker-webpack-plugin`包以使 TypeScript 与 Webpack 协同工作：

```js
npm install --save-dev html-webpack-plugin fork-ts-checker-webpack-plugin typescript 
```

最后但同样重要的是，我们安装`react`和`react-dom`以在简单示例中使用它们：

```js
npm install react react-dom 
```

现在所有依赖项都已安装，是时候配置一切使其工作。

首先，你需要在你的根路径下创建一个`.babelrc`文件：

```js
{
  "presets": ["@babel/preset-env", "@babel/preset-react"]
} 
```

首先要做的事情是在`package.json`中添加一个 npm 脚本以运行`webpack-dev-server`，这将服务于开发中的应用程序：

```js
"scripts": {
  "dev": "webpack serve --mode development --port 3000"
} 
```

在 Webpack 5 中，你需要使用这种方式调用 webpack 而不是`webpack-dev-server`，但你仍然需要安装此包。

Webpack 需要一个配置文件来了解我们应用程序中使用的不同类型的依赖项，为此，我们必须创建一个名为`webpack.config.ts`的文件，该文件导出一个对象：

```js
module.exports = {} 
```

我们导出的对象代表 Webpack 用于创建包的配置对象，它可以根据项目的规模和功能具有不同的属性。

我们希望保持示例非常简单，所以我们将添加三个属性。第一个是`entry`，它告诉 Webpack 我们的应用程序的主文件在哪里：

```js
entry: './src/index.tsx' 
```

第二个是`module`，这是我们告诉 Webpack 如何加载外部依赖项的地方。它有一个名为`rules`的属性，其中我们为每种文件类型设置一个特定的加载器：

```js
module: {
  rules: [
    {
        test: /\.(tsx|ts)$/,
        exclude: /node_modules/,
        use: {
            loader: 'ts-loader',
            options: {
          transpileOnly: true
            }
        }
    },
    {
        test: /\.css/,
        use: [
            'style-loader',
            'css-loader?modules=true'
        ]
    }
  ]
} 
```

我们说匹配`.ts`或`.tsx`正则表达式的文件将使用`ts-loader`加载，以便它们被转译并加载到包中。

你可能也注意到了，我们在`.babelrc`文件中添加了我们的预设。正如我们在第三章*清理你的代码*中看到的，预设是一组配置选项，它指导 Babel 如何处理不同类型的语法（例如，TSX）。

`rules`数组中的第二个条目告诉 Webpack 在导入 CSS 文件时应该做什么，它使用带有`modules`标志的`css-loader`来激活**CSS 模块**。转换的结果随后传递给`style-loader`，它将样式注入到页面的头部。

最后，我们启用 HTML 插件为我们生成页面，自动添加我们之前指定的`entry`路径的脚本标签：

```js
const HtmlWebpackPlugin = require('html-webpack-plugin')
const ForkTsCheckerWebpackPlugin = require('fork-ts-checker-webpack-plugin')
plugins: [
  new ForkTsCheckerWebpackPlugin(),
  new HtmlWebpackPlugin({
    title: 'Your project name',
    template: './src/index.xhtml',
    filename: './index.xhtml'
  })
] 
```

完整的`webpack.config.ts`应该如下所示：

```js
const HtmlWebpackPlugin = require('html-webpack-plugin')
const path = require('path')
const ForkTsCheckerWebpackPlugin = require('fork-ts-checker-webpack-plugin')
const isProduction = process.env.NODE_ENV === 'production'
module.exports = {
  devtool: !isProduction ? 'source-map' : false, // We generate source maps
  // only for development
  entry: './src/index.tsx',
  output: { // The path where we want to output our bundles
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].[hash:8].js',
    sourceMapFilename: '[name].[hash:8].map',
    chunkFilename: '[id].[hash:8].js',
    publicPath: '/'
  },
  resolve: {
    extensions: ['.ts', '.tsx', '.js', '.json', '.css'] // Here we add the
    // extensions we want to support
  },
  target: 'web',
  mode: isProduction ? 'production' : 'development', // production mode
  // minifies the code
  module: {
    rules: [
        {
            test: /\.(tsx|ts)$/,
            exclude: /node_modules/,
            use: {
            loader: 'ts-loader',
          options: {
              transpileOnly: true
          }
         }
        },
        {
            test: /\.css/,
            use: [
          'style-loader',
          'css-loader?modules=true'
            ]
        }
    ]
  },
  plugins: [
    new ForkTsCheckerWebpackPlugin(),
    new HtmlWebpackPlugin({
      title: 'Your project name',
      template: './src/index.xhtml',
      filename: './index.xhtml'
    })
  ],
  optimization: { // This is to split our bundles into vendor and main
    splitChunks: {
      cacheGroups: {
          default: false,
          commons: {
        test: /node_modules/,
        name: 'vendor',
        chunks: 'all'   
          }
      }
  }
}
} 
```

然后，为了配置 TypeScript，你需要这个 `tsconfig.json` 文件：

```js
{
  "compilerOptions": {
    "allowJs": true,
    "allowSyntheticDefaultImports": true,
    "baseUrl": "src",
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "isolatedModules": true,
    "jsx": "react-jsx",
    "lib": ["dom", "dom.iterable", "esnext"],
    "module": "esnext",
    "moduleResolution": "node",
    "noEmit": true,
    "noFallthroughCasesInSwitch": true,
    "noImplicitAny": false,
    "resolveJsonModule": true,
    "skipLibCheck": true,
    "sourceMap": true,
    "strict": true,
    "target": "esnext"
  },
  "include": ["src/**/*.ts", "src/**/*.tsx"],
  "exclude": ["node_modules"]
} 
```

为了使用 TypeScript 导入 CSS 文件，你需要在 `src/declarations.d.ts` 创建一个声明文件：

```js
declare module '*.css' {
  const content: Record<string, string>
  export default content
} 
```

然后，你需要创建 `src/index.tsx` 的主文件：

```js
import { createRoot } from 'react-dom/client'
const App = () => {
  return <div>Hello World</div>
}
 createRoot(document.getElementById('root') as HTMLElement).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
) 
```

最后，你需要创建初始的 HTML 文件 `src/index.xhtml`：

```js
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0"
      />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title><%= htmlWebpackPlugin.options.title %></title>
  </head>
  <body>
    <div id="root"></div>
  </body>
</html> 
```

我们完成了，如果我们终端中运行 `npm run dev` 命令并将浏览器指向 `http://localhost:8080`，我们应该能够看到以下标记正在被提供：

```js
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Your project name</title>
    <script defer src="img/vendor.12472959.js"></script>
    <script defer src="img/main.12472959.js"></script>
  </head>
  <body>
    <div id="root"></div>
  </body>
</html> 
```

完美！我们的 React 应用程序正在运行！现在让我们看看我们如何给我们的项目添加一些 CSS。

# 局部作用域的 CSS

现在是时候创建我们的应用程序了，它将包含一个与之前示例中使用的相同类型的简单按钮。我们将使用它来展示 CSS 模块的所有功能。

让我们更新 `src/index.tsx` 文件，这是我们指定的 Webpack 配置的入口：

```js
import { createRoot } from 'react-dom/client' 
```

然后，我们可以创建一个简单的按钮。像往常一样，我们将从一个未加样式的按钮开始，并将样式逐步添加：

```js
const Button = () => <button>Click me!</button> 
```

最后，我们可以将按钮渲染到 DOM 中：

```js
createRoot(document.getElementById('root') as HTMLElement).render(<Button />) 
```

现在，假设我们想要给按钮应用一些样式——背景颜色、大小等等。我们创建一个普通的 CSS 文件，命名为 `index.css`，并将以下类放入其中：

```js
.button {
  background-color: #ff0000;
  width: 320px;
  padding: 20px;
  border-radius: 5px;
  border: none;
  outline: none;
} 
```

现在，我们说过，使用 CSS 模块，我们可以将 CSS 文件导入到 JavaScript 中；让我们看看它是如何工作的。

在我们定义 `button` 组件的 `index.ts` 文件内部，我们可以添加以下行：

```js
import styles from './index.css' 
```

这个 `import` 语句的结果是一个 `styles` 对象，其中所有属性都是 `index.css` 中定义的类。

如果我们运行 `console.log(styles)`，我们可以在 `DevTools` 中看到以下对象：

```js
{
  button: "_2wpxM3yizfwbWee6k0UlD4"
} 
```

因此，我们有一个对象，其中属性是类名，值是（表面上）随机的字符串。我们稍后会看到它们不是随机的，但让我们先看看我们可以用这个对象做什么。

我们可以使用这个对象来设置按钮的类名属性，如下所示：

```js
const Button = () => (
  <button className={styles.button}>Click me!</button>
); 
```

如果我们回到浏览器，现在我们可以看到我们在 `index.css` 中定义的样式已经应用到了按钮上。这不是魔法，因为如果我们检查 `DevTools`，应用到的元素类与我们在代码中导入的 `style` 对象中附加的相同字符串：

```js
<button class="_2wpxM3yizfwbWee6k0UlD4">Click me!</button> 
```

如果我们查看页面的头部部分，现在我们可以看到相同的类名也已注入到页面中：

```js
<style type="text/css">
  ._2wpxM3yizfwbWee6k0UlD4 {
    background-color: #ff0000;
    width: 320px;
    padding: 20px;
    border-radius: 5px;
    border: none;
    outline: none;
  }
</style> 
```

这就是 CSS 和样式加载器的工作方式。CSS 加载器允许你将 CSS 文件导入到你的 JavaScript 模块中，当 `module` 标志被激活时，所有类名都局部作用在导入它们的模块中。

如我们之前提到的，我们导入的字符串不是随机的，但它使用文件哈希和一些其他参数以在代码库中唯一的方式生成。

最后，`style-loader` 会将 CSS 模块转换的结果注入到页面的头部部分。这非常强大，因为我们拥有了 CSS 的全部功能和表现力，同时结合了局部作用域类名和显式依赖的优势。

如本章开头所述，CSS 是全局的，这使得它在大型应用中很难维护。使用 CSS 模块，类名是局部作用域的，它们不会与其他部分的类名冲突，从而确保结果的可预测性。

此外，在组件内部显式导入 CSS 依赖项可以帮助我们清楚地看到哪些组件需要哪些 CSS。这对于消除死代码也非常有用，因为当我们出于任何原因删除一个组件时，我们可以确切地知道它使用了哪些 CSS。

CSS 模块是常规的 CSS，因此我们可以使用伪类、媒体查询和动画。

例如，我们可以添加如下 CSS 规则：

```js
.button:hover {
  color: #fff;
}

.button:active {
  position: relative;
  top: 2px;
}

@media (max-width: 480px) {
  .button {
    width: 160px;
  }
} 
```

这将被转换成以下代码并注入到文档中：

```js
._2wpxM3yizfwbWee6k0UlD4:hover {
  color: #fff;
}

._2wpxM3yizfwbWee6k0UlD4:active {
  position: relative;
  top: 2px;
}

@media (max-width: 480px) {
  ._2wpxM3yizfwbWee6k0UlD4 {
    width: 160px;
  }
} 
```

类名被创建，并在按钮被使用的地方被替换，这使得它既可靠又局部，正如预期的那样。

正如你可能已经注意到的，这些类名很好，但它们使得调试变得相当困难，因为我们无法轻易地知道哪个类生成了哈希。在开发模式下，我们可以添加一个特殊的配置参数，通过它可以选择用于生成作用域类名的模式。

例如，我们可以更改加载器的值如下：

```js
{
  test: /\.css/,
  use: [
    {
        loader: 'style-loader'
    },
    {
        loader: 'css-loader',
        options: {
            modules: {
          localIdentName: '[local]--[hash:base64:5]'
        }
     }
    }
  ]
} 
```

在这里，`localIdentName` 是参数，而 `[local]` 和 `[hash:base64:5]` 是原始类名值和五个字符哈希的占位符。其他可用的占位符包括 `[path]`，它代表 CSS 文件的路径，以及 `[name]`，它是源 CSS 文件的名字。

激活之前的配置选项，我们在浏览器中看到的结果如下：

```js
<button class="button--2wpxM">Click me!</button> 
```

这使得代码更易于阅读和调试。

在生产环境中，我们不需要这样的类名，我们更关注性能，因此我们可能希望有更短的类名和哈希。

使用 Webpack，这非常直接，因为我们可以在应用生命周期的不同阶段使用多个配置文件。此外，在生产环境中，我们可能希望提取 CSS 文件而不是将其从包中注入到浏览器中，这样我们可以有一个更轻量的包，并在**内容分发网络**（**CDN**）上缓存 CSS 以提高性能。

要做到这一点，你需要安装另一个 Webpack 插件，名为 `mini-css-extract-plugin`，它可以写入实际的 CSS 文件，并将从 CSS 模块生成的所有作用域类名放入其中。

CSS 模块有几个值得注意的特性。

第一项是 `global` 关键字。在任意类名前加上 `:global`，实际上意味着要求 CSS 模块不要将当前选择器本地化。

例如，假设我们按以下方式更改我们的 CSS：

```js
:global .button {
  ...
} 
```

输出将如下所示：

```js
.button {
  ...
} 
```

如果你想要应用无法本地化的样式，比如第三方小部件，这会很好。

我最喜欢的 CSS 模块功能是 **组合**。通过组合，我们可以从同一文件或外部依赖中提取类，并将所有样式应用到元素上。

例如，将设置按钮背景为红色的规则从按钮的规则中提取到一个单独的块中，如下所示：

```js
.background-red {
  background-color: #ff0000;
} 
```

我们可以按以下方式在我们的按钮内部组合它：

```js
.button {
  composes: background-red;
  width: 320px;
  padding: 20px;
  border-radius: 5px;
  border: none;
  outline: none;
} 
```

结果是，按钮的所有规则和 `composes` 声明的所有规则都应用到该元素上。

这是一个非常强大的功能，它以令人着迷的方式工作。你可能预期所有组合的类都会在它们被引用的类中重复，就像 **SASS @extend** 一样，但这并不是事实。简单来说，所有组合的类名都是按顺序应用到 DOM 中的组件上的。

在我们的特定情况下，我们将有以下内容：

```js
<button class="_2wpxM3yizfwbWee6k0UlD4 Sf8w9cFdQXdRV_i9dgcOq">Click me!</button> 
```

这里，注入到页面中的 CSS 如下所示：

```js
.Sf8w9cFdQXdRV_i9dgcOq {
  background-color: #ff0000;
}

._2wpxM3yizfwbWee6k0UlD4 {
  width: 320px;
  padding: 20px;
  border-radius: 5px;
  border: none;
  outline: none;
} 
```

如你所见，我们的 CSS 类名具有唯一名称，这有助于隔离我们的样式。现在，让我们看看原子 CSS 模块。

## 原子 CSS 模块

应该很清楚组合是如何工作的，以及为什么它是 CSS 模块的一个非常强大的功能。在我开始写这本书的时候，我在迪士尼公司工作，我们试图更进一步，将 `composes` 的力量与 `Atomic CSS`（也称为 **功能性 CSS**）的灵活性结合起来。

原子 CSS 是一种使用 CSS 的方法，其中每个类只有一个规则。

例如，我们可以创建一个类来设置 `margin-bottom` 为 `0`：

```js
.mb0 {
  margin-bottom: 0;
} 
```

我们可以使用另一个类来设置 `font-weight` 为 `600`：

```js
.fw6 {
  font-weight: 600;
} 
```

然后，我们可以将这些原子类应用到元素上：

```js
<h2 class="mb0 fw6">Hello React</h2> 
```

这种技术具有争议性，同时也很高效。很难开始使用它，因为你最终会在你的标记中拥有太多的类，这使得预测最终结果变得困难。如果你这么想，它和内联样式非常相似，因为你为每条规则应用一个类，除了你使用一个较短的类名作为代理之外。

反对原子 CSS 的最大论点通常是你将样式逻辑从 CSS 移动到标记，这是错误的。类是在 CSS 文件中定义的，但它们是在视图中组合的，每次你不得不修改元素的样式时，你最终都会编辑标记。

另一方面，我们尝试使用原子 CSS 一段时间，我们发现它使原型设计变得非常快。

事实上，当所有基本规则都生成后，将这些类应用到元素上并创建新样式是一个非常快速的过程，这是很好的。其次，使用 Atomic CSS，我们可以控制 CSS 文件的大小，因为一旦我们创建了带有其样式的新的组件，我们就在使用现有的类，而不需要创建新的类，这对性能来说是非常好的。

因此，我们尝试使用 CSS 模块来解决 Atomic CSS 的问题，并将其技术命名为**Atomic CSS modules**。

从本质上讲，你开始创建你的基础 CSS 类（例如，`mb0`），然后，而不是在标记中逐个应用类名，你使用 CSS 模块将它们组合成占位符类。

让我们来看一个例子：

```js
.title {
  composes: mb0 fw6;
} 
```

这里有一个另一个例子：

```js
<h2 className={styles.title}>Hello React</h2> 
```

这很棒，因为你仍然将样式逻辑保留在 CSS 中，CSS 模块的`composes`为你完成了工作，通过在标记中应用所有单个类。

上述代码的结果如下：

```js
<h2 class="title--3JCJR mb0--21SyP fw6--1JRhZ">Hello React</h2> 
```

在这里，`title`、`mb0`和`fw6`都被自动应用到元素上。它们也被局部作用域化，因此我们拥有 CSS 模块的所有优势。

# 实现 styled-components

有一个库非常有前景，因为它考虑到了其他库在组件样式方面遇到的所有问题。在 JavaScript 中编写 CSS 已经遵循了不同的路径，并且尝试了许多解决方案，因此现在是时候有一个库能够吸收所有这些经验，并在其基础上构建一些东西了。

这个库由 JavaScript 社区中的两位流行开发者构想和维护：*Glenn Maddern*和*Max Stoiber*。它代表了一种非常现代的解决问题的方法，它使用了 ES2015 的边缘特性以及一些应用于 React 的高级技术，以提供完整的样式解决方案。

让我们看看如何创建我们在上一节中看到的相同按钮，并检查我们感兴趣的 CSS 特性（例如，伪类和媒体查询）是否与`styled-components`一起工作。

首先，我们必须通过运行以下命令来安装库：

```js
npm install styled-components 
```

一旦库安装完成，我们就要在我们的组件文件中导入它：

```js
import styled from 'styled-components' 
```

在那个阶段，我们可以使用`styled`函数通过`styled.elementName`创建任何元素，其中`elementName`可以是 div、button 或其他任何有效的 DOM 元素。

第二件事是要定义我们创建的元素的样式，为此，我们使用一个名为**tagged template literals**的 ES6 特性，这是一种在不预先插值的情况下将模板字符串传递给函数的方法。

这意味着函数接收包含所有 JavaScript 表达式的实际模板，这使得库能够利用 JavaScript 的全部力量来应用样式到元素上。

让我们从一个具有基本样式的简单按钮开始创建：

```js
const Button = styled.button`
  backgroundColor: #ff0000;
  width: 320px;
  padding: 20px;
  borderRadius: 5px;
  border: none;
  outline: none;
`; 
```

这种“有点奇怪”的语法返回一个正确的 React 组件，称为 `Button`，它渲染一个 `button` 元素，并将模板中定义的所有样式应用于它。应用样式的做法是通过创建一个唯一的类名，将其添加到元素中，然后在文档的头部注入相应的样式。

下面是将被渲染的组件：

```js
<button class="kYvFOg">Click me!</button> 
```

添加到页面上的样式如下：

```js
.kYvFOg {
  background-color: #ff0000;
  width: 320px;
  padding: 20px;
  border-radius: 5px;
  border: none;
  outline: none;
} 
```

`styled-components` 的好处是它几乎支持所有 CSS 功能，这使得它成为在现实世界中使用的良好候选者。

例如，它支持使用类似 SASS 的语法使用伪类：

```js
const Button = styled.button`
  background-color: #ff0000;
  width: 320px;
  padding: 20px;
  border-radius: 5px;
  border: none;
  outline: none;
  &:hover {
    color: #fff;
  }
  &:active {
    position: relative;
    top: 2px;
  }
` 
```

它还支持媒体查询：

```js
const Button = styled.button`
  background-color: #ff0000;
  width: 320px;
  padding: 20px;
  border-radius: 5px;
  border: none;
  outline: none;
  &:hover {
    color: #fff;
  }
  &:active {
    position: relative;
    top: 2px;
  }
  @media (max-width: 480px) {
    width: 160px;
  }
`; 
```

该库可以为你的项目带来许多其他功能。

例如，一旦创建了按钮，你就可以轻松地覆盖其样式，并使用不同的属性多次使用它。在模板内部，也可以使用组件接收到的 props 并相应地更改样式。

另一个很棒的功能是**主题化**。通过将你的组件包裹在 `ThemeProvider` 组件中，你可以将 `theme` 属性注入到三个组件的子组件中，这使得创建部分样式在组件之间共享，而其他一些属性依赖于当前所选主题的 UI 变得极其简单。

毫无疑问，`styled-components` 库在将你的样式提升到下一个层次时是一个变革者。一开始，它可能看起来有点奇怪，因为它涉及到使用组件实现样式，但一旦你习惯了它，我保证它将成为你最喜欢的样式包。

# 摘要

在本章中，我们探讨了旨在帮助读者导航 React 中样式复杂性的重要主题。我们讨论了扩展 CSS 的挑战，并以 Meta 的经验为例，突出大型组织面临的现实世界困难。这强调了我们所分享知识的相关性和适用性。

为了使 React 中的样式更加直观和高效，我们研究了内联样式的工作原理以及将样式与组件本地化放置的好处。这种方法促进了代码的有序和可读性，这对于希望掌握 React 的开发者来说至关重要。

认识到内联样式的局限性，我们介绍了 CSS 模块作为替代方案。我们提供了一个逐步指南来设置项目，让读者通过动手实验来学习。

将 CSS 文件导入组件也被强调为一种重要的实践。这有助于明确依赖关系，并通过将类名本地化来防止问题，确保代码的可扩展性和无冲突。

最后，我们向读者介绍了 `styled-components`，这是一个与本书目标一致的库，旨在提供创新的方式来样式化组件并优化 React 中的开发实践。

到目前为止，我们已经探讨了在 React 中管理 CSS 样式的各种方法，每种方法都展示了我们核心论点的不同方面。在下一章中，我们将通过深入研究服务器端渲染的实际实现和好处来进一步加深你对 React 的理解——这是一种提高应用程序性能和用户体验的技术。

# 加入我们的 Discord 社区

加入我们的 Discord 空间，与作者和其他读者进行讨论：

[`packt.link/React18DesignPatterns4e`](https://packt.link/React18DesignPatterns4e)

![二维码](img/QR_Code2450023176943770109.png)
