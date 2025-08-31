

# 第一章：什么是 React 和 React Native？

欢迎来到**React**和**React Native**的神奇世界。希望您在这里能感到宾至如归。如果您是第一次接触这些框架，或者您已经稍微尝试过它们，那都没关系。本书将专注于**管理 React Native 应用的状态**，但我们将从基础知识开始讲起。

如果 React 和 React Native 是人的话，第一个将是第二个的父亲。您可以专注于子框架，但您会发现了解 React Native 的“父亲”——**ReactJS**有很大的好处。

我们将开始这段旅程，先回顾 ReactJS 的历史，特别是它为什么被创造出来。然后，我们将继续研究 ReactJS，探讨*React 思维模式*或拥有*React 心态*的含义。熟悉 ReactJS 后，我们将尝试理解**跨平台软件开发**的含义以及 React Native 在跨平台开发生态系统中的位置。为了理解这个生态系统，我们将专注于 React Native 本身，它的简要历史和当前状态。我们将通过一些用 React Native 编写的原生应用示例结束我们的旅程。

在本章中，我们将涵盖以下主题：

+   理解 ReactJS 的历史

+   React 思维模式（React 心态）

+   理解跨平台软件开发

+   了解 React Native 的历史

+   查看使用 React Native 的流行应用的示例

到本章结束时，您将具备 React 和 React Native 的高级知识。您还将了解它们在软件开发生态系统中的位置。

# 理解 ReactJS 的历史

在本节中，我们将简要回顾 ReactJS 的历史。如果您对这个特定主题不感兴趣，请随意跳过本节，直接进入*React 思维模式*。了解一个框架的历史对于使用它并不是强制性的。如果您更喜欢 YouTube 上的浓缩知识，我强烈推荐观看由*uidotdev*发布的 10 分钟视频，名为*React 的故事*。

## 前驱者

你知道吗，第一个创建的网站至今仍然存在？你可以在这里找到它：[`info.cern.ch/hypertext/WWW/TheProject.html`](http://info.cern.ch/hypertext/WWW/TheProject.html)。它是在 1991 年创建的！从那时起，发生了许多变化。首先，网页开发者们想要改变他们网站的样式，因此 CSS 被创造出来。几年后，同样的开发者们希望在他们的美丽网站上看到更多的互动性。这就是**JavaScript**在互联网上找到其位置的时候。但正如我们所知，网络永远不会停止发展。JavaScript 的过度使用导致了像 jQuery、BackboneJS 和 Ember 这样的库的创建。每个库的创造者都从他们的竞争对手那里吸取了教训。他们做出了导致创建非常不同的开发者体验的决定。开发者们有自己的偏好，并且就哪个库更好展开了小规模的战争。

对于这个问题没有正确答案。然而，可以肯定的是，无论幕后使用的是哪个库，网站的用户体验都发生了演变。网站变得更加互动，能够适应用户的屏幕大小。例如，今天为移动视图和桌面视图创建单独菜单是一种常见的做法。这可以通过 JavaScript 或 CSS 单独实现。这种用户体验的转变没有 JavaScript**开源**库的演变是不可能发生的。

几年后，随着越来越多的 JavaScript 代码片段被添加到网站上，是时候需要一个更全面的解决方案了。第一个突破来自谷歌，推出了**AngularJS**。AngularJS 于 2010 年正式发布，与当时市场上的其他解决方案不同。这不仅仅是一个库；这是一个框架。开发者们能够快速创建复杂的交互，他们不再害怕对 JavaScript 文件的任何更改都会破坏整个页面。我不想深入探讨 AngularJS 的实现细节。毕竟，这不是本书的重点。总的来说，AngularJS 引入了后台运行的框架观察到的特殊 HTML 属性。正如你可能想象的那样，当 JavaScript 观察数十个甚至数百个元素和事件时，它会减慢速度。因此，用户体验正在受到影响，世界正准备迎接另一场 JavaScript 革命。谷歌认为他们的 Angular 版本 2 将保持领先地位，但到了 2013 年，Facebook 的开发者宣布了 ReactJS 的发布。

## 然后出现了 React

ReactJS 被特别定位为一个**用户界面**（**UI**）库。它被构思用于网站上的最终用户交互。它还使用了**JSX**——为 React 创建的 JavaScript 扩展。许多开发者对这个新语法表示愤怒，这并不令人意外。不过，我要说的是，在科技界，愤怒的反应并不意外。任何新的技术解决方案都必须经受住愤怒的 Reddit 帖子，这些帖子说它丑陋、无用，或者简单地说，很糟糕。幸运的是，ReactJS 的开发者并没有因为这种最初的负面反应而停止工作。此外，了解 ReactJS 的开发者成为了它的倡导者。你可能会问，为什么 ReactJS 能够经受住时间的考验，而 Angular 却没有？我认为这与框架的高级思维模式有关。ReactJS 提出了优雅、简单的解决方案，同时完全可配置以满足任何需求。我将在下一节进一步探讨这种思维模式。

回到我们的历史课程！我们回到了 2013 年，ReactJS 以一种震撼的方式进入舞台。许多人讨厌它，但其他人用它来构建越来越复杂的网站。不幸的是，ReactJS 的扩展性并不好。你的 React 组件使用状态和属性。如果父组件创建了一个状态，这个状态需要被层级较低的四个或五个组件读取，你将遇到被称作**属性钻取**的问题。属性钻取意味着开发者必须通过许多父组件传递必要的属性，以便到达最终需要读取它的子组件。这个过程既令人烦恼又无聊！这就是第一个状态管理库——**Redux**诞生的时刻。我们将在下一章详细讨论 Redux 和其他状态管理库。

在撰写这本书的时候，ReactJS 是最受欢迎的 JavaScript 库之一。它不断进化，其维护者对公众讨论和建议持开放态度。2019 年，他们引入了 hooks 和 context。这两个 React 实用工具可以满足你大部分的状态管理需求。它们之所以被创建，是因为 React 团队意识到使用 React 的开发者需要在状态管理方面得到改进。

在 hooks 和 context 引入之前几年，具体是在 2015 年，Facebook 开发者发布了 React Native。这本书的真正英雄！但让我们不要走得太远。在这个时候，了解 React 的基本概念非常重要。让我们继续探讨 React 思维模式。

# React 思维（React 思维模式）

官方 ReactJS 文档中包含一个名为 *React 思维* 的章节：https://reactjs.org/docs/getting-started.html#thinking-in-react.

重要提示

许多 React 用户认为阅读*React 思维*([`reactjs.org/docs/thinking-in-react.html`](https://reactjs.org/docs/thinking-in-react.html))是他们理解 React 的关键时刻。这可能是最古老的 React 教程，但它仍然同样相关。

让我们尝试捕捉那篇文章中最重要且仍然相关的部分。

首先，当我们用 ReactJS 创建一个网站时，我们需要考虑我们将如何构建我们的组件。不是 HTML 块，不是 DOM 元素，而是组件。理想情况下，每个组件都将是一个独立的实体，它要么创建状态，要么消费属性，有时两者都有。组件是我们应用程序的最小部分，就像原子是我们世界的最小部分一样。

好吧，我意识到原子可以进一步分为中子、质子和电子。**ReactJS 组件**也可以分为处理逻辑和实际渲染的部分。然而，原子和 ReactJS 组件都是它们各自领域的基本构建块。

现在我们已经想象出了我们的组件，我们需要知道它们应该如何相互交互。让我们回到 ReactJS 文档，在那里我们将找到一个很好的章节，*组合与继承*：[`reactjs.org/docs/composition-vs-inheritance.html`](https://reactjs.org/docs/composition-vs-inheritance.html)。

这篇文章非常明确地指出，ReactJS 组件应该是组合的，而不是严格分层堆叠的。这基本上意味着任何子组件都应该以可以被应用程序中的其他父组件重用的方式创建。这促进了原子组件的高可重用性，同时，减少了创建应用程序所需的代码量。

现在我们已经掌握了理论，让我们继续探讨具体实践。我们如何在实践中组合 ReactJS 组件？通过使用状态和属性。你可能想知道那些是什么？好吧，我很乐意解释！

**状态**和**属性**（简称属性）都是普通的 JavaScript 对象。它们之间最大的区别在于属性是只读的，而状态可以在管理它的组件内部进行更改。状态是真相的来源，而属性是应用程序当前状态的表示。让我们看看一个最小化的代码示例：

```js
import React, { useState } from "react";
const PrettyButton = ({ updateCount, count }) => {
  return (
      <button onClick={updateCount}>This was clicked {count} of         times</button>
  );
};
export default function App() {
  const [counter, updateCount] = useState(0);
  const handleClick = () => {
    updateCount(counter + 1);
  };
  return (
    <div>
      <h1>Hello There!</h1>
        <PrettyButton count={counter} updateCount={handleClick}          />
    </div>
  );
}
```

你可以通过这个 CodeSandbox 在线尝试这段示例代码：[`codesandbox.io/s/admiring-fire-68k94x?file=/src/App.js`](https://codesandbox.io/s/admiring-fire-68k94x?file=/src/App.js)。

从前面的代码示例中，你可以看到`App`组件创建了计数器状态，以及负责更新它的函数。`PrettyButton`以属性的形式消费这个状态。`PrettyButton`不能直接更改`counter`或`updateCounter`的值。

如果我们要编写另一个需要使用`PrettyButton`的父组件，它将需要创建自己的`counter`和`updateCounter`状态。正因为如此，我们可能想要在我们的 web 应用中使用`PrettyButton`的每一个实例都将独立于其他实例。

我们还可能在主`App`组件中导入多个子组件。这是完全自然的。我们可能有一个带有按钮、文本和模态框的应用，所有这些都需要显示按钮被点击的次数。我们只需要将必要的组件添加到父组件中，并传递`counter`属性。状态仅在父组件中更改，然后传递给子组件。

现在，我们来到了需要决定哪个组件应该处理状态变化的时候。在我们的简单代码示例中，答案很明显：我们只有一个父组件。在现实世界中，这个问题可能要难得多。幸运的是，我们将在整本书中探讨状态管理策略。我希望，在阅读这本书之后，你将准备好在你的 React Native 应用中选择最佳位置来存储和管理你的应用状态。

在上一节中，我们讨论了在 ReactJS 中编写代码的高级方面。记住我们查看的模式是很有用的，因为它们在 React Native 开发中同样有用。而且由于我们已经熟悉了 ReactJS，我们准备好深入到用 JavaScript 编写的原生应用的世界。

# 理解跨平台软件开发

在谈论 React Native 之前，我们需要回顾一下移动应用开发的格局。

显然，可以使用原生平台编程语言来创建移动应用。被认为最现代的是用于 iOS 开发的 Swift 和用于 Android 开发的 Kotlin。许多开发者仍然使用 Objective-C 和 Java。然而，当手机市场在苹果和谷歌两大巨头之间稳定下来时，创建一次编写即可用于两个平台的解决方案是非常诱人的。同样，对于可以在任何浏览器中打开的网站，为什么我们不能有可以在任何设备上运行的应用呢？

寻找这种神话般的跨平台解决方案对许多公司来说极具吸引力。他们分别从 iOS 和 Android 团队中招聘了不同的团队，最终得到的 app 在外观和感觉上并不相同。

软件开发的世界非常广阔，我们可以找到许多针对单个问题的解决方案。跨平台开发也不例外。如果你搜索“跨平台应用”，你会找到一个由微软提供的解决方案，名为**Xamarin**。你还会找到一个名为**Flutter**的解决方案，它使用一种名为**Dart**的语言编写。最后，你还会发现许多基于 JavaScript 的解决方案。其中第一个有意义的参与者是**Ionic**。Ionic 是一个框架，建于 2013 年，用于 AngularJS 的开发，并在幕后使用**Apache Cordova**。Ionic 开发者使用与创建网站相同的语法来构建他们的应用。在构建时，创建一个包含单个 WebView 的原生应用**包装器**。Ionic 代码在这个 WebView 中运行。鉴于这种结构，许多人将 Ionic 应用称为**混合应用**，以区分它们与**跨平台**应用。

React Native 是一个完全不同的解决方案。在这种情况下，代码被编译成一个完整的原生应用。JavaScript 代码在应用中运行，并通过一个**桥接器**与手机的本地模块进行通信。但你可能会问，React Native 是从哪里来的呢？

让我们在下一节深入探讨这个话题。

# 探索 React Native 的历史

回到 2012 年，Facebook 宣布他们正在成为一个**移动优先**的公司。Facebook 意识到用户在手机上花费的时间比在电脑上多。他们需要确保他们的网站和应用在智能设备上无缝工作。然而，Facebook 的大多数工程师都是网页开发者。公司开始研究如何利用这些网页开发者的知识来开发移动应用。在尝试了几种不同的想法后，他们不想跟随 Ionic 的脚步，将应用封装在 WebView 中。他们需要一些新的东西。

就在这个时候，一位名叫*克里斯托弗·切德欧*的开发者在软件开发的历史上留下了自己的印记。他与*乔丹·沃尔克*、*阿什温·布拉姆贝*和*林·何*一起参加了一个 Facebook 内部的黑客马拉松。基于乔丹最初的努力——此时他已经能够从 JavaScript 生成 iOS 的*UILabel*——他们创建了一个工作原型，可以在用户设备上从 JavaScript 生成原生 UI 元素。而且他们只用了 2 天时间！

React Native 的历史：Facebook 的开源应用开发框架

你可以在这里阅读文章：[`www.techaheadcorp.com/blog/history-of-react-native/`](https://www.techaheadcorp.com/blog/history-of-react-native/)。

在这次初步成功之后，乔丹和克里斯托弗能够继续他们名为 React Native 的新产品开发工作，这个产品拥有一个由工程师组成的完整团队。

经过 3 年的努力，他们终于准备好向世界展示他们的成果。React Native 的官方发布是在 2015 年的 ReactJS Conf 上进行的。这是第一届 ReactJS Conf，React Native 在主题演讲中被介绍！这显示了 Facebook 对这个框架的信心。我鼓励你查看这个演讲；你可以在官方 ReactJS 文档中找到链接：[`reactjs.org/blog/2015/02/18/react-conf-roundup-2015.html`](https://reactjs.org/blog/2015/02/18/react-conf-roundup-2015.html)。

自 2015 年以来，React Native 经历了很大的成长和变化。一些变化，如 hooks 和 context 的引入，是 ReactJS 中发生变化的简单后续。在其他情况下，变化是由社区推动或由框架的维护者提出的。React Native 在[github.com](http://github.com)上有一个名为*讨论和提案*（[`github.com/react-native-community/discussions-and-proposals`](https://github.com/react-native-community/discussions-and-proposals)）的整个部分。每个人都可以添加他们想讨论的关于 React Native 实现、生态系统等主题的内容。这个论坛是了解当前正在进行的事情以及未来可能发生的事情的一个极好的资源。这个论坛上的第一个问题，确切地说，是第六个问题，是一个关于*精益核心*的提案。到那时，React Native 已经在野外至少 3 年了，并且已经成长了很多。这个框架已经包括了 UI 细节的实现，如开关，或原生功能，如推送通知。该仓库的核心维护者之一提议，所有非绝对必要的代码都应该从主包中移除。你可以在*精益核心*这里了解更多细节：[`github.com/react-native-community/discussions-and-proposals/issues/6`](https://github.com/react-native-community/discussions-and-proposals/issues/6)。

当然，回答“什么是必要的”和“什么不是”的问题并不容易。*精益核心*经过了几个月的讨论和重大变更。今天主 React Native 包的形状代表了这一努力的成果。

同时，*精益核心计划*激发了社区的热情，让他们继续创建自己的库，这些库对 React Native 应用可能很有用。截至撰写本书时，当你决定创建一个 React Native 应用时，可以选择的库有数百个。这里有 UI 库、导航库、异步存储管理库等等。这既是福也是祸，因为并非每个库都写得很好且维护得当。不幸的是，你可能会用到一些可能在将来破坏你应用的库。所以，在你跑到终端并输入`yarn add`之前，你可能想使用 React Native 目录：[`reactnative.directory`](https://reactnative.directory)。这个网站提供了开源库的指标，当你想为你的项目添加一个好的依赖项时，这些指标非常有帮助。

有几个库非常突出，它们被认为是 React Native 项目的*推荐*库。这些库通常非常成熟且维护良好。一个例子是**React Navigation**，这是需要多于一个屏幕的应用的首选库。**React Native Testing Library**是与 Kent C. Dodd 的**React Testing Library**官方结合的库。**Reanimated**是一个动画库，其性能优于任何竞争对手。

React Native 生态系统的一个重要部分是**Expo**：[`expo.dev/`](https://expo.dev/). Expo 既是 React Native 应用的框架，也是平台。它为用户提供了一套用于开发、构建和部署应用的工具。

这具体意味着什么？Expo 是 React Native 之上的一层薄层，旨在让开发者的生活更轻松。如果你在 React Native 中编写应用就像用手吃烤牛排一样，那么 Expo 就像是带着烤土豆和凯撒沙拉一起吃菲力牛排。在一家高档餐厅。你可能非常偏好前者，但无法否认后者明显的优势。如果你决定使用 Expo，你将在官方 React Native 文档中找到本地环境设置说明：[`reactnative.dev/docs/environment-setup`](https://reactnative.dev/docs/environment-setup)。一旦应用设置完成，你将能够利用 Expo 团队创建和维护的许多组件。这样，你可能会节省一些头痛和性能问题。当你准备好向世界展示你的应用时，你可以将你的应用包上传到 Expo 网站，并用于测试和部署。正如你所见，Expo 是一个非常通用的工具。

既然我们已经了解了 React Native 的历史和当前状态，让我们继续看看一些实际使用它的应用。

# 检查使用 React Native 的流行应用的示例

现在我们对 React Native 有了一些了解，是时候对其感到兴奋了。了解一项新技术的一个好方法就是看看这项技术已经被用于什么。当你必须决定使用特定技术时，这也是一个好的策略。

显而易见的例子来自**Meta**——React Native 的诞生地。ReactJS 的最初实现发生在 Facebook Ads 中。React Native 在移动设备上用于相同的功能是合适的。Facebook 的移动应用并非完全使用 React Native 创建，但其中一些部分使用了它。这意味着 Facebook 应用是一个 React Native 的*棕色地带*应用。与之相反的是仅使用 React Native 编写的应用，这类应用被称为*绿色地带*。

当我们在元宇宙中时，我会提到 Instagram 应用使用 React Native，Oculus 应用也是如此。

别担心，Meta 并不是唯一一家使用 React Native 的知名公司。Discord 不仅使用 React Native 来开发他们的应用，还撰写了关于他们如何维护应用的博客文章。在这篇 Medium 文章中，[`blog.discord.com/how-discord-achieves-native-ios-performance-with-react-native-390c84dcd502`](https://blog.discord.com/how-discord-achieves-native-ios-performance-with-react-native-390c84dcd502)，Discord 团队表示，他们一开源就采用了 React Native，并且几年后仍然对他们的决定感到满意。

Shopify 是 React Native 生态系统中的另一大重要玩家。他们在博客上有一篇文章，标题为《React Native 是 Shopify 移动未来的未来》：[`shopify.engineering/react-native-future-mobile-shopify`](https://shopify.engineering/react-native-future-mobile-shopify)。Shopify 工程师还撰写了更多技术文章，例如关于无障碍的：[`www.shopify.com/partners/blog/react-native-accessibility`](https://www.shopify.com/partners/blog/react-native-accessibility)。

网站构建巨头**Wix**也在 React Native 领域非常活跃。他们也写了一些关于他们与 React Native 冒险经历的文章（https://medium.com/wix-engineering/react-native-at-wix-the-architecture-db6361764da6），但他们还创建了开源库，例如这个 UI 库：[`github.com/wix/react-native-ui-lib.`](https://github.com/wix/react-native-ui-lib)

回到使用 React Native 构建的具体应用列表，我必须提到**Coinbase**。以可靠的方式管理用户的财务是这家加密市场领先者的首要任务。他们分析了、迭代了，并最终选择了 React Native 作为他们的主要移动技术。你可以在他们的博客上阅读他们关于从原生技术过渡到 React Native 的文章：[`blog.coinbase.com/announcing-coinbases-successful-transition-to-react-native-af4c591df971`](https://blog.coinbase.com/announcing-coinbases-successful-transition-to-react-native-af4c591df971)。

你可能听说过像特斯拉、沃尔玛、Salesforce、彭博社和《Vogue》这样的公司。你可能也使用过像 Uber Eats、Artsy、Words with Friends 和 SoundCloud Pulse 这样的应用。它们有什么共同点？惊喜！（其实不是。）它们都使用了 React Native。你可以在 React Native 展示区找到更多例子和文章链接：[`reactnative.dev/showcase`](https://reactnative.dev/showcase)。

并非所有 React Native 的故事都是成功的。有一个著名的案例（我所说的“著名”是指它在推特上讨论了好几天）是 Airbnb。Airbnb 的网站使用 ReactJS，因此他们尝试为他们的移动应用使用 React Native 是合乎逻辑的。经过几年的开发，他们遇到了开发瓶颈和性能问题。他们的应用包含一个非常大的地图，需要完美运行。负责该应用的开发者经常需要 React Native 开发者的帮助，这对这家以网络技术为重点的公司来说是一个瓶颈。他们在 2018 年宣布与 React Native 分手：[`medium.com/airbnb-engineering/sunsetting-react-native-1868ba28e30a`](https://medium.com/airbnb-engineering/sunsetting-react-native-1868ba28e30a)。幸运的是，他们仍然在开发他们惊人的动画库 **Lottie**（http://airbnb.io/lottie/#/），它可以在 React Native 应用中使用。

# 摘要

哎呦！对于一本编程书来说，理论内容确实挺多的，对吧？然而，即使你觉得有点枯燥，我坚信这些理论知识对于下一章将非常有用。我们已经了解了一些关于网络开发的历史，以及 ReactJS 和 React Native 的创造者的动机。了解这些将使我们能够理解不同状态管理解决方案背后的理念。在下一章中，我们将探讨在 React Native 应用中管理状态的最基本方式：使用 hooks 和 context。
