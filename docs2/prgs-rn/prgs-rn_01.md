# 1

# 什么是 React Native？

为多个平台构建高质量的应用程序是应用程序开发的圣杯。自从 React Native 发布以来，它一直在非常竞争激烈的环境中受到挑战，因为它似乎一直是这个圣杯。它在 2015 年由 Facebook 发布时的性能比任何竞争对手（Ionic、Cordova）都要好得多，其开发速度也比创建独立的 Android 和 iOS 应用程序要快得多。

自 2015 年以来，关于 React Native 发生了许多事情。Facebook 开源了这个框架，许多贡献者甚至像微软、Discord 和 Shopify 这样的大公司也大力投资 React Native，同时新的竞争对手如 Flutter 和 Kotlin Multiplatform Mobile 也发展起来。

在 7 年的时间里，许多公司成功地将他们的应用程序迁移到了 React Native，而其他一些公司则失败了，转而回到原生开发，或者最终选择了其他多平台技术。

到 2022 年，React Native 被用于比以往更多的产品中，并且它比早期变得更加开发者友好。它不仅适用于 iOS 和 Android，还适用于 macOS、Windows、Web、VR 和其他平台。最重要的是，尽管有许多谣言称并非如此，Facebook 仍然在大力押注 React Native。

Facebook 的 React Native 核心团队刚刚完成了对其主要应用程序中超过 1,000 个 React Native 屏幕的重写，包括约会、工作和市场，这些应用程序每月有超过 10 亿用户访问。这意味着 React Native 为世界上最大和最常用的应用程序的重要和业务关键部分提供了动力，这是它作为一个稳定且受支持的框架的最终证明。

如你所见，React Native 已经变得非常强大并且被广泛使用。但你必须知道如何利用其优势以及如何处理其劣势，以创建高质量的应用程序和良好的软件产品。本书包含了你需要了解的学习成果、最佳实践以及基本架构和流程概念，以便能够决定以下事情：

+   何时在你的项目中使用 React Native

+   如何设置你的 React Native 项目以支持更大规模的工作

+   如何使用 React Native 创建世界级的产品

+   如何在 React Native 项目中组织团队

+   如何通过有用的工具和流程支持你的开发团队

本章简要介绍了 React 的主要概念，这是 React Native 构建的基础，以及 React Native 本身和 Expo 框架，这是一个建立在 React Native 之上的工具和库集合。我们将关注与理解本书后面将要涵盖的内容相关的关键概念。

如果你已经对 React、React Native 和 Expo 的工作原理有非常好的理解，你可以自由地跳过本章。

在本章中，我们将涵盖以下主题：

+   探索 React

+   理解 React 基础

+   介绍 React Native

+   介绍 Expo

# 技术要求

要尝试本章中的代码示例，你需要为 *探索 React* 和 *理解 React 基础* 部分设置一个小型 React 应用，并为 *介绍 React Native* 部分设置一个 React Native 应用。这需要你根据你使用的操作系统安装各种库。[`reactjs.org/`](https://reactjs.org/) 和 [`reactnative.dev/`](https://reactnative.dev/) 都提供了设置正确开发环境的逐步指南。

你可以在书的 GitHub 仓库中找到代码：

# 探索 React

在 [`reactjs.org/`](https://reactjs.org/) 上，React 被定义为 *用于构建用户界面的 JavaScript 库*。主页上使用的口号是声明式、组件化、一次学习，到处编写。

当 React 首次在 2013 年 5 月的 JSConf US 大会上由 Facebook 的 Jordan Walke 介绍时，观众非常怀疑，Facebook 决定开始一次 *React 巡回* 来说服人们这个新库的好处。如今，React 是最受欢迎的用于创建网络应用的框架之一，它不仅被 Facebook 本身使用，还被 Instagram、Netflix、Microsoft 和 Dropbox 等许多其他大公司使用。

在下一节中，我将向你展示 React 的工作原理，它与其他类似框架和方法的独特之处，以及它与 React Native 的关系。

小贴士

如果你已经安装了 Node 和 Node 包管理器，你可以在终端中使用以下命令设置一个新的应用：

**npx create-react-app name-of-your-app**

# 理解 React 基础

要开始，请在你的 IDE 中打开一个项目，这样我们就可以探索一个简单的例子。这是一个返回简单 `Hello World` 消息的 React 应用看起来像：

```js
function App() {
  return (
    <div>
      <p>Hello World!</p>
    </div>
  )
}
```

当看到这些代码行时，你首先想到的可能就是这看起来就像 XML/HTML！确实如此，但这些标签会被一个预处理器转换成 JavaScript，所以这是看起来像 XML/HTML 标签的 JavaScript 代码。因此得名 **JSX**，它是 **JavaScript XML** 的缩写。

JSX 标签可以像 XML/HTML 标签一样使用；你可以使用不同类型的标签来结构化你的代码，并且可以使用 CSS 文件和 `className` 属性来样式化它们，这是 React 对 HTML 的 `class` 属性的等效。

另一方面，你可以在 JSX 中的任何地方插入 JavaScript 代码，无论是作为属性的值还是标签内部。你只需要将它放在大括号中。请看以下代码，它使用了 JSX 中的 JavaScript 变量：

```js
function App() {
  const userName = 'Some Name';
  return (
    <div>
      <p>Hello {userName}!</p>
    </div>
  )
}
```

在这个例子中，我们通过将 `userName` 变量插入到我们的示例代码的 JSX 中，向一个我们之前存储在 `userName` 变量中的用户打招呼。

这些 JSX 标签非常实用，但如果我想在整个代码中重用代码的一部分，比如一种特殊的按钮或侧边栏元素呢？这就是 ReactJS 主页上“基于组件”的口号发挥作用的地方。

## 理解 React 组件

我们的例子包括一个名为`App`的组件。在这种情况下，它是一个函数式组件。在 React 中也可以使用类组件，但接下来的大多数示例将使用更常见的函数式组件。React 允许你编写自定义组件，并在代码的其他部分像正常 JSX 标签一样使用它们。

假设我们想要一个按钮，当点击时可以打开指向 ReactJS 主页的外部链接。我们可以定义一个自定义的`ReactButton`组件，如下所示：

```js
function ReactButton() {
  const link = 'https://reactjs.org';
  return (
    <div>
      <a href={link} target="_blank" rel="noopener noreferrer">
        Go To React
      </a>
  </div>
  )
} 
```

然后，我们可以在主组件中使用该按钮，使用空标签表示法，因为它没有子组件：

```js
function App() {
  const userName = 'Some Name';
  return (
    <div>
      <p>Hello {userName}!</p>
      <ReactButton/>
    </div>
  )
 }
```

如您所见，React 中的每个组件都必须实现`return`函数以在应用中渲染视图。JSX 代码只能在由`return`函数调用时执行，并且必须有一个 JSX 标签包裹所有其他标签和组件。没有必要明确实现当内容变化时视图应该如何行为——React 会自动处理这一点。这就是我们描述 React 为声明式时所意味着的。

到目前为止，我们已经看到了为什么 React 被定义为用于构建用户界面的声明式、基于组件的 JavaScript 库。但我们还没有谈到 React 的主要优势之一：它如何高效地重新渲染视图。为了理解这一点，我们需要看看 props 和 state。

## 理解 React 的 props 和 state

一个`WelcomeMessage`组件，用于显示欢迎文本，包括来自`App`组件的用户名。

这个组件可能看起来是这样的：

```js
function WelcomeMessage(props) {
  return (
    <div>
      <p>Welcome {props.userName}!</p>
      <p>It's nice to see you here!</p>
    </div>
  )
 }
```

然后，我们可以将其包含在`App`组件中：

```js
function App() {
  const userName = "Some Name";
  return (
    <div>
      <WelcomeMessage userName={userName}/>
      <ReactButton/>
    </div>
  )
 }
```

prop 的名称被用作 JSX 标签的属性。通过将`props`作为子组件的参数，所有这些属性都会自动在子组件中可用，例如我们例子中的`username`。

React 之所以高效，是因为每当 prop 的值发生变化时，只有那些受该变化影响的组件才会重新渲染。这大大减少了重新渲染的成本，尤其是在具有多层的大型应用中。

对于状态变化也是如此。React 提供了将任何组件转换为有状态组件的可能性，通过在类组件中实现`state`变量或在函数组件中使用`useState`钩子（更多关于 Hooks 的内容请见*第三章*，*Hello React Native*)。有状态组件的经典例子是一个`Counter`：

```js
function Counter () {
  const [numClicks, setNumClicks] = useState(0);
  return (
    <div>
      <p>You have clicked {numClicks} times!</>
      <button onClick={() => setNumClicks(numClicks+1)>
        Click Me
      </button>
    </div>
  )
 }
```

`numClicks`状态变量初始化为`0`。每当用户点击按钮并且`Counter`组件的内部状态发生变化时，只有`<p>`标签的内容会重新渲染。

ReactDOM 负责比较 UI 树中的所有元素与之前的元素，并仅更新内容已更改的节点。此包还使得将 React 代码轻松集成到现有 Web 应用程序中成为可能，无论它们是用什么语言编写的。

当 Facebook 在 2012 年决定成为一家以移动优先的公司时，React 的这种*一次学习，到处编写*的方法被应用于移动应用程序的开发，这导致了 2013 年 React Native 的出现，其中可以使用 JavaScript 或 TypeScript 仅编写 iOS 或 Android 应用程序。

既然我们已经了解了 React 是什么以及它的一般工作原理，让我们进一步了解 React Native。

# 介绍 React Native

React Native 是一个框架，它使得将 React 代码编写并部署到多个平台成为可能。最著名的是 iOS 和 Android，但您可以使用 React Native 创建 Windows、macOS、Oculus、Linux、tvOS 以及更多应用程序。使用 React Native for Web，您甚至可以使用相同的代码将移动应用程序作为 Web 应用程序部署。

小贴士

如果您不想花一个小时设置创建新 React Native 应用程序的开发环境并尝试代码示例，您可以使用`npm`或`yarn`安装 Expo CLI：

**npm install -g expo-cli** 或 **yarn global add expo-cli**

之后，只需在终端中运行一个命令即可设置新的 React Native 应用程序：

**expo init YourAppName**

`expo init`是 yarn。如果您想使用`npm`，请将`--npm`添加到`expo init`命令中。

在下一节中，您将学习如何在 React Native 框架中实现跨平台开发。

## React Native 基础知识

由于 React Native 在 React 的基础上构建，代码看起来非常相似；您使用组件来结构化代码，使用 props 将参数从一个组件传递到另一个组件，并在返回语句中使用 JSX 来渲染视图。主要区别之一是您可以使用的基本 JSX 组件类型。

在 React 中，它们看起来与我们在上一节中看到的 XML/HTML 标签非常相似。在 React Native 中，所谓的核心组件是从`react-native`库导入的，并且看起来不同：

```js
import React from 'react';
import {ScrollView, Text, View} from 'react-native';
const App = () => {
  return (
   <ScrollView contentInsetAdjustmentBehavior="automatic">
        <View>
           <Text>Hello World!</Text>
        </View>
     </ScrollView>
  );
};
export default App;
```

React Native 不像某些其他跨平台解决方案那样使用 Web 视图在设备上渲染 JavaScript 代码；相反，它将用 JavaScript 编写的 UI 转换为本地 UI 元素。例如，React Native 的`View`组件被转换为 Android 的`ViewGroup`组件，以及 iOS 的`UIView`组件。这种转换是通过 Yoga 引擎（[`yogalayout.com`](https://yogalayout.com)）完成的。

React Native 由两个线程驱动 - JavaScript 线程，其中执行 JavaScript 代码，以及本地线程（或 UI 线程），其中发生所有设备交互，如用户输入和屏幕绘制。

这两个线程之间的通信是通过所谓的**Bridge**进行的，它是 JavaScript 代码和应用程序原生部分之间的一种接口。例如，原生事件或指令等信息以序列化批量的形式从原生 UI 线程通过 Bridge 发送到 JavaScript 线程，然后再返回。这个过程在下面的图中展示：

![图 1.1 – React Native Bridge

![图片 B16694_01_01.jpg]

图 1.1 – React Native Bridge

如您所见，事件在原生线程中收集。然后信息被序列化并通过 Bridge 传递到 JavaScript 线程。在 JavaScript 线程中，信息被反序列化并处理。这也同样适用于相反的方向，如前图中*步骤 5*到*8*所示。您可以调用由原生组件提供的方法，或者 React Native 在必要时可以更新 UI。这也是通过序列化信息并通过 Bridge 将其传递到原生线程来完成的。这个 Bridge 使得原生和 JavaScript 之间的异步通信成为可能，这对于使用 JavaScript 创建真正的原生应用程序来说是非常好的。

但它也有一些缺点。信息的序列化和反序列化，以及作为原生和 JS 之间唯一的中心通信点，使得 Bridge 成为了一个瓶颈，在某些情况下可能导致性能问题。这就是为什么 React Native 在 2018 年至 2022 年之间被完全重写。

## 新的 React Native（2022）

由于之前提到的架构问题，React Native 的核心被完全重构并重写。主要目标是消除 Bridge 及其相关的性能问题。这是通过引入 JSI（JavaScript 接口）来实现的，它允许原生代码和 JavaScript 代码之间直接通信，无需进行序列化和反序列化。

JS 部分真正了解原生对象，这意味着您可以直接同步调用方法。此外，在重构过程中引入了一个新的渲染器，称为 Fabric。关于 React Native 重构的更多细节将在*第三章*，*Hello React Native*中提供。

重构使得原本出色的 React Native 框架更加出色，显著提高了其即插即用的性能。在撰写本文时，越来越多的包正在适应新的 React Native 架构。

## 更多 React Native 优势

自从 2015 年开源以来，已经形成了一个庞大且不断增长的社区，该社区为各种不同的问题和用例开发并提供了大量的附加包。这是 React Native 相对于其他类似跨平台方法的主要优势之一。

这些包大多数都得到了良好的维护，并提供了目前存在的几乎所有原生功能，因此你只需使用 JavaScript 来编写你的应用程序。

这意味着使用 React Native 进行移动应用开发可以大大减少开发团队的大小，因为你不再需要 Android 和 iOS 专家，或者至少可以显著减少原生专家的团队规模。

与这些维护良好的包一起工作的最好之处在于，当包更新时，React Native 核心重写等事物会自动应用到你的应用中。

此外，热重载功能通过使代码更改的效果在几秒钟内可见，从而加快了开发过程。还有其他几个工具使 React Native 开发者的生活更加舒适，我们将在*第九章*《提高 React Native 开发的基本工具》中更详细地探讨。

现在我们已经了解了 React 和 React Native 是什么，以及它们是如何相互关联的，让我们看看一个使整个开发过程变得更加容易的工具——Expo。

# 介绍 Expo

设置新的 React Native 应用有几种方法。对于本书中的示例项目，我们将使用 Expo。它是一个基于 React Native 构建的强大框架，包括许多不同的工具和库。Expo 使用纯 React Native，并增强了大量功能。

当涉及到核心组件和原生功能时，React Native 是一个非常精简的框架，而 Expo 提供了几乎你能在应用中使用到的所有功能。它为几乎所有原生设备功能提供了组件和 API，例如视频播放、传感器、位置、安全、设备信息以及更多。

将 Expo 视为一个全服务包，它可以让你的 React Native 开发者生活变得更加轻松。由于任何事物都有其缺点，Expo 会增加你最终应用包的大小，因为无论你是否使用，你都会将所有库添加到你的应用中。

它还使用了一种某种修改过的 React Native 版本，这通常比最新的 React Native 版本落后一到两个版本。因此，当使用 Expo 时，你必须在它们发布后等待几个月才能使用最新的 React Native 功能。

如果你想要以最大速度达到结果且不需要优化你的包大小，我会推荐使用 Expo。

当使用 Expo 设置新项目时，你可以选择两种不同的工作流程——裸工作流程和管理工作流程。在这两种工作流程中，框架为你提供了易于使用的库，用于包含相机、文件系统等原生元素。此外，还有推送通知处理、空中功能更新以及针对 iOS 和 Android 构建的特殊 Expo 构建服务等服务。

如果你选择裸工作流，你将拥有一个普通的 React Native 应用程序，并且可以添加你需要的 Expo 库。你还可以添加其他第三方库，这在托管工作流中是不可能的。在那里，你只需在你选择的 IDE 中编写 JavaScript 或 TypeScript 代码；其他所有事情都由框架处理。

在他们的主页 ([`docs.expo.dev/`](https://docs.expo.dev/)) 上，Expo 建议你从一个新的应用开始使用托管工作流，因为如果需要，你总是可以通过在 CLI 中使用 `expo eject` 命令切换到裸工作流。这种必要性可能出现在你需要集成一个由 Expo 不支持的第三方包或库，或者你想添加或更改原生代码的情况下。

初始化应用程序后，你可以使用 `expo start` 命令来运行它。这将启动 Metro 打包器，使用 Babel 编译应用程序的 JavaScript 代码。此外，它打开 Expo 开发者 CLI 界面，在那里你可以选择你想要在哪个模拟器中打开应用程序，如下面的截图所示：

![图 1.2 – Expo CLI 界面

![img/B16694_01_02.jpg]

图 1.2 – Expo CLI 界面

Expo 开发者工具提供了访问 Metro 打包器日志的功能。它还创建了关于如何运行应用程序的多个选项的关键绑定，例如 iOS 或 Android 模拟器。最后，它创建了一个可以用 Expo Go 应用扫描的二维码。对于大多数用例，Expo 还支持从 React Native 代码创建 Web 应用程序。

使用 Expo，在硬设备上运行你的应用程序非常简单——只需在你的智能手机或平板电脑上安装 Expo 应用，并扫描之前描述的二维码。同时运行在多个设备或模拟器上也是可能的。

所有这些特性使 Expo 成为使用 React Native 进行移动应用开发的非常方便且易于使用的框架。

# 摘要

在本章中，我们介绍了 JavaScript 库 React 的主要概念。我们已经展示了 React 是声明式的、基于组件的，并遵循“一次学习，到处编写”的方法。这些概念是跨平台移动开发框架 React Native 的基础。

你已经看到了这个框架的主要优势，即庞大的社区提供了额外的包和库，许多操作系统（除了 iOS 和 Android）都可用，以及通过 Bridge 或 JSI 使用原生元素。最后但同样重要的是，你发现了 Expo 作为设置 React Native 应用的一种方式，并且你知道何时使用哪种 Expo 工作流。

在下一章中，我们将简要介绍 JavaScript 和 TypeScript 的最重要的事实和特性。
