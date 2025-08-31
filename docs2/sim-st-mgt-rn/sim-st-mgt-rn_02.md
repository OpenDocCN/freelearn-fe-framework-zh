

# 第二章：在简单的 React 应用程序中管理状态

在上一章中，我们简要回顾了网络开发、**JavaScript**、**ReactJS** 和 **React Native** 的历史。尽管历史知识不是编写出色代码的必要条件，但我发现它很有用。一旦我们了解到为什么特定的库创建者鼓励某些模式并阻止其他模式，我们就可以编写出更少错误和更高效的代码。啊，是的！编写代码！这就是你在这里的原因，亲爱的读者，不是吗？好消息是，在本章中，我们将深入研究代码示例。我们将从查看 React 中最基本的数据和状态管理策略开始：使用状态和属性。然后，我们将深入比较有状态和无状态组件。一旦我们对 React 应用程序中状态的工作方式有了很好的理解，我们将继续讨论 hooks。我们将通过完成我们自己的小应用程序的设置和配置来结束本章。

这里是我们将要涵盖的要点：

+   状态是什么？它与属性有何不同？

+   有状态和无状态组件是什么？

+   什么是 hooks？为什么使用它们？

+   设置示例应用程序

到本章结束时，你应该对 React 代码感到舒适。我们还将为我们的应用程序设置基础。尽管应用程序可能彼此非常不同，但这个基本设置对于大多数应用程序来说都将保持不变。请随意将其用于你想要工作的任何其他项目。

# 技术要求

如果你熟悉 **ReactJS** 但尚未使用过 **React Native**，你将能够毫无问题地跟随本节内容。

如果你从未阅读或编写过任何 **ReactJS** 或 **React Native** 代码，学习基本概念非常重要。请访问官方 **React Native** 文档[`reactnative.dev/docs/intro-react`](https://reactnative.dev/docs/intro-react)，并熟悉如 **组件、JSX、状态** 和 **属性** 等关键概念。

本章的最低要求是了解 Git、基本的 **命令行界面**（**CLIs**）知识，以及 JavaScript 的实际应用。

# 状态是什么？它与属性有何不同？

每个 **React Native** 应用程序都是为了显示某种类型的数据而创建的。这可能包括天气数据、图片、市场数据、地图……使用 **React Native**，我们管理这些数据如何在用户屏幕上显示。**React Native** 提供了强大的工具来样式化和动画化内容。然而，在这本书中，我们专注于你的应用程序中使用的数据的原始材料。

为了有一个动态的数据片段，它能够自动与我们的组件保持同步，我们需要将列表声明为组件状态。

重要提示

关于状态，需要记住的最重要的事情是：状态是在组件内部管理的；它是组件的 **内存**。

状态的任何变化都会导致你的组件及其所有子组件重新渲染。这是一个预期的行为：如果你的数据发生变化，你希望你的 UI 也相应地改变。然而，多个组件的重新渲染可能会导致你的应用遇到性能问题。

让我们通过一个例子来更好地理解状态。我们将从一个非常基本的组件开始，包含一个`<Text>`元素和一个`<Pressable>`元素。`<Pressable>`是 React Native 应用中推荐使用的组件，在 Web 开发者会使用`<button>`标签的地方：

```js
import React from "react";
import { View, Text, Pressable } from "react-native";
export const ManagedButton = () => {
  return (
    <View>
        <Text>this text will display the current status</Text>
        <Pressable onPress="">
            <Text>Press here to check/uncheck</Text>
        </Pressable>
    </View>
  );
};
```

你可能已经观察到，亲爱的读者，当点击`<Pressable>`组件时不会发生任何事，因为我们还没有提供`onPress`函数。

现在，我们将向这个简单的组件添加状态。我们将在`<Text>`组件内设置一个选中/未选中的文本，并将其与组件状态相关联：

```js
import React, { useState } from "react";
import { View, Text, Pressable } from "react-native";
export const ManagedButton = () => {
    const [checkedState, setCHeckedState] =      useState("unchecked");
  return (
    <View>
          <Text>this text will display the current status,            which is: {checkedState}</Text>
        <Pressable onPress="">
            <Text>Press here to check/uncheck</Text>
        </Pressable>
    </View>
  );
};
```

测试 React Native 代码比测试在浏览器中运行的代码（如 JavaScript 或 ReactJS）要复杂一些。幸运的是，Expo 的好人们创建了一个在线工具来测试代码片段。它被称为 Expo Snack，你可以用它来测试前面的代码，网址是[`snack.expo.dev/@p-syche/simplifying-state-management---chapter-2-example-1`](https://snack.expo.dev/@p-syche/simplifying-state-management---chapter-2-example-1)。

让我们逐个查看这些变化。我们首先在第一行添加从 React 库中导入`useState`钩子的语句。然后，在组件内部，我们设置这个变量：

```js
const [checkedState, setCheckedState] = useState(“unchecked”);
```

`useState`钩子接受一个数组，其中第一个元素是状态值，第二个元素是设置值的函数。如果你在组件中不会改变状态，你可以省略第二个参数。关于数组中元素的名称没有官方规则，但将设置函数命名为与状态值类似，但使用`"set"`关键字是一种公认的习惯。最后但同样重要的是，将`"unchecked"`字符串传递给`useState`钩子。这是`useState`钩子的默认值。如果你不希望设置默认状态，你可以将括号留空。

现在我们已经导入了状态钩子，并使用`useState`钩子设置了组件状态，我们可以在组件中使用它。因此，这一行：

```js
<Text>this text will display the current status, which is: {checkedState}</Text>
```

包围状态的括号是**JSX**的一部分。**JSX**是**JavaScript**的语法扩展，它是编写所有**React**组件所使用的语法。*"这对你来说意味着什么？"*你，亲爱的读者，问。这意味着在编写**JSX**时，你可以编写任何**JavaScript**代码，并且还可以编写额外的内容，例如用括号包裹的**组件状态**。你可以将**JSX**与**JavaScript**相比，就像海盗说话与普通英语相比一样。所有说英语的海盗都会理解所有的英语短语，但一个普通的英语人可能不会理解所有的海盗短语。好吧，伙计？那么我们就继续吧，嘿嘿嘿！

我们已经设置了状态，但我们的`<Pressable>`组件仍然没有任何作用，对吧？让我们添加一个`onPress`函数，它将设置状态。实现这一点最简单的方法是将`setCheckedState`函数从`useState`钩子直接传递到`onPress`函数中：

```js
<Pressable onPress={setCheckedState("checked")}>
```

现在，当按下`<Pressable>`按钮时，它将改变组件的状态，这反过来又会改变`<Text>`组件中显示的文本。

你可以用`useState`钩子实现更多的事情。你可以将其设置为任何你喜欢的值，包括一个对象。每个组件都可以有多个状态，实际上可以有很多！如果你想看看如何在 React 组件中实现状态的其它示例，我邀请你查看*进一步阅读*部分的第一个链接。

让我们继续到本节第二个英雄：props。Props 是**属性**的简称。Props 就像 state 一样是 JavaScript 对象；它们之间最大的区别是 props 是只读的。

重要提示

关于 props，最重要的是记住这一点：props 是不可变的（或只读的）。

`"checked"`/`"unchecked"`状态的天然流动。父组件有子组件：带有图像或文本的组件等，我们将状态以 prop 的形式传递给它们。在这种情况下，子组件可以是`"checked"`或`"unchecked"`。但是，子组件永远不会**改变**文本的状态。文本的状态只能在声明状态的父组件内部改变。让我们更新我们的代码示例，包括一个父组件和一个子组件，状态在父组件中设置并通过 props 传递给子组件：

```js
import React, { useState } from "react";
import { View, Text, Pressable } from "react-native";
const ManagedText = ({checkedState}) => {
  return (
      <Text>this text will display the current status,        which is: {checkedState}</Text>
  );
};
export const ParentComponent = () => {
      const [checkedState, setCheckedState] =        useState("unchecked");
    return (
        <View>
            <ManagedText checkedState={checkedState} />
              <Pressable onPress={() =>                setCheckedState("checked")}>
              <Text>Press here to check/uncheck</Text>
            </Pressable>
      </View>
    );
  };
```

你可以在以下 Expo Snack 中找到前面的代码：[`snack.expo.dev/@p-syche/simplifying-state-management---chapter-2-example-2`](https://snack.expo.dev/@p-syche/simplifying-state-management---chapter-2-example-2)。

让我们从与上一个示例相同的地方开始。我们有我们的`<ParentComponent>`，之前它被命名为`<ManagedButton>`。但是说句实话，这个组件与上一个版本相比变化不大。这里唯一的改变是，我们看到了一个`<ManagedText>`组件，而不是一个`<Text>`组件，它有一个神秘的`checkedState`属性。这个属性被传递给`<ManagedText>`组件，然后传递给它内部的`<Text>`组件。按下`<Pressable>`组件将改变`<ParentComponent>`的状态，这也会反映在子组件`<ManagedText>`中。我相信，亲爱的读者，父/子命名法是非常容易理解的，不需要额外的解释。至于`checkedState`属性，或者简称为 prop，你应该知道你可以给它取任何你喜欢的名字；没有必要将 prop 的名字设置为与它的值相同。例如，你可以这样写：

```js
const ManagedText = (fancyComponentStuff) => {
  return (
      <Text>this text will display the current status,        which is:{fancyComponentStuff}</Text>
  );
};
export const ParentComponent = () => {
      const [checkedState, setCheckedState] =        useState("unchecked");
    return (
        <View>
            <ManagedText fancyComponentStuff={checkedState} />
            <Pressable onPress={setCheckedState("checked")}>
              <Text>Press here to check/uncheck</Text>
            </Pressable>
      </View>
    );
  };
```

如果你想了解更多关于 props 和状态的信息，可以查看官方 React 团队推荐的文章。它们列在*进一步阅读*部分。

现在你已经知道了状态和 props 是什么，以及它们彼此之间的不同，在接下来的部分，我们将探讨有状态组件和无状态组件。

# 什么是有状态组件和无状态组件？

无论你是完全新接触 React 世界，还是在这里已经有一段时间了，你很可能已经听说过**有状态**和**无状态**组件这两个术语。在 ReactJS v16.8 引入 hooks 之前，这些术语特别有用。现在不用担心 hooks——我们将在本章末尾讨论它们。

从一个高层次的角度来看，**ReactJS**和**React Native**组件不过是**JavaScript**函数。React 库为这些函数添加了一些特定的功能。其中之一就是**状态**，这是一种我们在上一节中探讨的特殊类型的组件内存。

一个可以接受状态的 React 组件可能看起来像这样：

```js
class Welcome extends React.Component {
  constructor(props) {
    super(props);
    this.state = {name: "World"}
  };
  render() {
    return <Text>Hello, {this.state.name}</Text>;
  }
}
```

这种类型的组件也通常被称为“类组件”，因为它需要以这种方式声明。类组件，或称有状态组件，在`componentDidMount()`、`componentWillUnmount()`、`shouldComponentUpdate()`以及一些其他函数出现之前是一等公民。这些函数对于许多面临边缘情况的开发者来说是一大救星。例如，他们需要在**组件**的其他部分加载一些数据之前，或者在他们确保在**组件**卸载前清理一些副作用函数之前。不幸的是，这也意味着他们的**组件**在逻辑上变得越来越复杂。试图理解包含多个**“生命周期方法”**的文件的代码流程是一项真正的挑战。如果你想了解更多关于生命周期方法的信息，请查看*进一步阅读*部分，那里你可以找到一个链接到 ReactJS 文档中一篇题为*在类中添加生命周期方法*的文章。

**有状态组件**比**无状态组件**更难测试，而且它们编译速度较慢，编译后体积也更大。

**无状态组件**，也称为**函数组件**，是类组件的轻量级兄弟。以下是一个无状态组件的例子：

```js
const Welcome = (props) => {
  return <Text>Hello, World! </Text>;
}
```

比较前面片段中显示的两个示例**组件**，你应该注意到编写给定**组件**所需的代码行数有很大的差异。我们简单的有状态**组件**需要九行代码，而**函数组件**在三个代码行内就实现了相同的功能！

这意味着`constructor`或特殊的`componentDidUpdate`。当然，它们有一个很大的缺点，就是无法管理状态。所以，一个理想的**ReactJS**或**React Native**应用至少应该包含一个父组件，一个有状态的组件，然后它会将属性传递给各种无状态的子组件。然而，在现实世界中几乎没有理想的应用。开发者经常会编写有状态的组件，并添加生命周期方法来管理何时以及何时不应更新 UI。

这种趋势随着之前提到的**ReactJS** v16.8 的发布而改变，当时在**ReactJS**世界中引入了钩子的概念，我们将在下一节中探讨。

# 钩子是什么？为什么使用它们？

正如我之前提到的，无状态组件通常更容易编写和测试。当`useState`是一个返回状态值和更新它的函数时，它们应该是`useState`的默认组件。你可能在我们之前关于 React 组件状态的章节中见过它。

让我们回到我们之前的有状态组件的例子，将其改为函数式组件，并添加`useState`钩子，如下所示：

```js
import React, {useState} from "react";
import {Text} from "react-native";
const Welcome = () => {
  const [name, setName] = useState('World!');
  return <Text>Hello, {name}</Text>;
}
```

哇塞！这看起来比之前的例子干净多了！我们仍然有一个能够持有和管理状态变化的组件，但它比有状态类组件要短得多。我还觉得这种组件的逻辑流程非常好，我们可以在一行中声明状态值和状态设置函数。

如果你想看到这段代码的实际应用，你可以访问[`snack.expo.dev/@p-syche/example-of-functional-component-with-usestate`](https://snack.expo.dev/@p-syche/example-of-functional-component-with-usestate)。

这是一个**Expo Snack**——类似于网络开发的代码片段。

## 你应该了解哪些钩子？

我们之前讨论的第一个钩子是`useState`，这是你应该首先熟悉的一个。第二个最常用的钩子是`useEffect`。我也相信这是命名最好的钩子之一。你可以用它给你的组件添加各种副作用。你可能会问，“什么是副作用？”亲爱的读者，让我们通过例子来尝试理解这个概念：想象一个社交媒体应用（就像我们在本书中将要构建的应用一样！）现在，让我们想象你被分配了一个添加点赞计数器的任务。你有一个带有计数的父`<Text>`组件。它看起来可能像这样：

```js
const LikesParentComponent = () => {
    const getCounterNumberFromApi =      someFunctionRetrievingDataFromAPI();
    const [counterNumber, setCounterNumber] =      useState(getCounterNumberFromApi)
  return (
    <LikesComponent counterNumber={counterNumber} />
  );
};
const LikesComponent = (counterNumber) => {
      const [likeState, setLikedState] = useState         ("haven't yet liked");
    return (
        <View>
            <Text>you {likeState} this post</Text>
            <Pressable onPress={setLikedState("liked")}>
              <Text>Press here to check/uncheck</Text>
            </Pressable>
              <Text>{counterNumber} other people liked this                 post</Text>
      </View>
    );
  };
```

我们正在将`counterNumber`从`<LikesParentComponent>`作为属性传递。让我们假设这个父组件使用名为`someFunctionRetrievingDataFromAPI()`的非常贴切的功能来从 API 获取点赞数。

到目前为止，看起来相当不错，对吧？我们加载了组件；它们从 API 中检索点赞数据并将其传递给我们的 `<LikesComponent>`，它以很好的方式显示。但是等等！如果用户触摸了 `<Pressable>` 组件会发生什么？我们将 `<Text>` 设置为 `liked`，但计数器不会增加！我们绝对不能就这样留下！这是一个经典的副作用：用户操作需要组件状态中的额外更改。首先，我们不能在 `<LikesComponent>` 内部更改 `counterNumber`，因为我们之前在状态和属性部分学到的是属性是不可变的。那么我们能做什么呢？我们可以使用父组件的状态设置函数。这个函数可以作为属性传递。这意味着 `<LikesParentComponent>` 将像这样调用其子组件：

```js
<LikesComponent counterNumber={counterNumber} setCounterNumber={setCounterNumber} />
```

到目前为止，一切顺利。现在，我们只需要在适当的时候调用这个设置函数，这意味着在 `<LikesComponent>` 中按下按钮时。这就是使用 `useEffect` 钩子时的样子：

```js
const LikesComponent = (counterNumber, setCountNumber) => {
      const [likeState, setLikedState] = useState         ("haven't yet liked");
    useEffect(() => {
        if (likeState === "liked") {
            setCounterNumber(counterNumber++)
        } 
        else {
            setCounterNumber(counterNumber-1)
        }
    }, [likeState])
    return (
        <View>
            <Text>you {likeState} this post</Text>
            <Pressable onPress={setLikedState("liked")}>
              <Text>Press here to check/uncheck</Text>
            </Pressable>
              <Text>{counterNumber} other people liked this                 post</Text>
      </View>
    );
  };
```

如您可能注意到的，`useEffect` 钩子看起来与 `useState` 钩子非常不同。不必过于担心这一点。这两个钩子是最常用的，您会习惯它们的构思和消费方式。

我们示例中的 `useEffect` 钩子内部是一个常见的 `if`/`else` 语句，用于检查状态的值是否等于 `"liked"`。这个钩子最关键和有趣的部分是位于最后的数组。这个数组被称为依赖数组。它用于通知钩子函数何时运行。在我们的例子中，当 `likeState` 的值发生变化时，`useEffect` 钩子应该运行。

`useEffect` 钩子可以用来更新应用的不同部分，帮助数据获取、用户驱动的交互等等。这个钩子非常强大，但它有一个非常大的风险：如果编写不当，它会导致许多重新渲染。

关于 `useEffect` 最重要的事情要记住

确保正确设置 `useEffect` 的依赖数组！

正如您可能在官方 `useEffect` 的依赖数组中找到的那样。如果我们设置它，那么我们的效果将仅在依赖数组中的任何项目发生变化时运行。

还有几个其他的内置钩子。在开始编写 `useState` 和 `useEffect` 时，您不必了解它们的所有内容——这足以让您开始。当您到达这两个钩子不足以满足需求的时候，您可以回到 **ReactJS** 文档中阅读关于其他钩子的内容。您还可以编写适用于您特定应用的自己的自定义钩子。

现在我们知道了什么是钩子以及为什么我们要使用它们，让我们开始设置我们的示例应用！

# 设置示例应用

啊！您可能一直在等待的时刻：实际上创建一个应用！

我们将首先准备我们的开发环境。你需要一个 **集成开发环境**（**IDE**）例如 VS Code、Sublime Text、Atom 或你可能喜欢的任何其他东西。IDE 是你编写 **React Native** 代码所需的一切。但我们还需要一种方式来查看代码的渲染效果，不是吗？

在网页开发的情况下，我们只需使用浏览器来查看和测试我们的代码。然而，**React Native** 应用不能在网页浏览器中轻松测试。它们可以在真实或模拟设备上进行测试。在理想情况下，你会拥有多部手机，并将它们通过 USB 插入电脑以查看你的应用。尽管如此，我们大多数人并没有多部手机。这就是为什么我们可以使用手机模拟器。在移动世界中，有两个主要玩家：Android 和 Apple。由于 Android Studio 应用，Android 模拟器几乎可以在任何桌面平台上使用。不幸的是，iPhone 模拟器只能在 Mac 电脑上运行。

设置模拟器可能是一项艰巨的任务，但不必过于担心！有 **Expo** 帮助！

我在第一章中提到了 **Expo**。如果你跳过了那部分，让我快速介绍一下：**Expo** 是 **React Native** 开发工具。它使得构建、测试和发布应用变得更加容易。**Expo** 是在 **React Native** 之上的一层包装，旨在使开发者体验更加流畅。

## 环境设置

让我们确保你的开发环境已经准备好。根据 Expo 网站上的说明，你需要最新的 Node、Git 和 Watchman。所有这些的链接都可以在 Expo 的文档中找到，地址是 [`docs.expo.dev/get-started/installation/`](https://docs.expo.dev/get-started/installation/)。我们在开发过程中将使用 Yarn，所以请确保你已经安装了它。你可以在这里找到详细说明：[`classic.yarnpkg.com/en/docs/install`](https://classic.yarnpkg.com/en/docs/install)。一旦你浏览了这些链接，请按照以下步骤操作：

1.  当你准备好时，请继续安装 Expo 的 CLI 工具：

    ```js
    $ npm install –global expo-cli
    ```

1.  通过运行 `expo whoami` 来验证安装是否成功。你还没有登录，所以你会看到 `expo register`，或者你可以使用 `expo login` 登录现有的账户。

1.  下一步是在你的手机上安装 Expo Go 应用。你可以在 Android 商店中找到它，地址是 [`play.google.com/store/apps/details?id=host.exp.exponent`](https://play.google.com/store/apps/details?id=host.exp.exponent)，以及在 App Store 中的 [`apps.apple.com/app/expo-go/id982107779`](https://apps.apple.com/app/expo-go/id982107779)。

多亏了 Expo，无论你使用的是 Mac 电脑还是 Windows 电脑，以及你拥有什么类型的手机，**Expo Go** 应用都会“自动”在 Android 和 Apple 设备上运行。

1.  我们已经准备好了——是时候创建应用了。打开你的终端并运行以下命令：

    ```js
    $ npx create-expo-app funbook-app
    ```

1.  当被提示选择模板时，请选择 **空白**。

你可以为你的应用选择任何你喜欢的名字。我建议使用“`Funbook`”，因为它听起来有点像“Facebook”，我们将创建一个社交媒体应用克隆。保持和我一样的名字可能会使跟随代码示例更容易。

1.  应用初始化成功运行后，你可以通过运行以下命令进入你的应用文件夹：

    ```js
    $ cd funbook-app
    ```

1.  然后运行开发服务器，如下所示：

    ```js
    $ expo start
    ```

或者，如果你使用 Yarn，运行这个命令：

```js
$ yarn start
```

Expo CLI 启动 Metro Bundler，这是一个编译我们应用 JavaScript 代码的 HTTP 服务器。你应该会看到一个二维码，你现在可以使用手机上的 Expo Go 应用扫描它。你可以在你想要的任何设备上运行你的 Funbook 应用。

应用开发一开始可能会显得有些令人畏惧，但如果第一次尝试不是所有东西都完美运行，请不要担心。有很大可能性你会在终端窗口中找到罪魁祸首。终端输出是你获取信息的最佳来源。

如果你看到终端中有任何错误，或者感觉有点迷茫，请确保查看 Expo 安装文档：[`docs.expo.dev/get-started/create-a-new-app/`](https://docs.expo.dev/get-started/create-a-new-app/)。

我设置了一个公共存储库，我们将在这本书的整个过程中使用它。你可以在这里找到它：[`github.com/PacktPublishing/Simplifying-State-Management-in-React-Native`](https://github.com/PacktPublishing/Simplifying-State-Management-in-React-Native)。

在这个存储库的`main`分支上，你会找到一个已经设置好的应用。你可以随意克隆或分叉这个存储库。记住，如果你想在你电脑上运行这个应用，你仍然需要安装`node`、`watchman`和`yarn`）。

## 应用结构

让我们考虑一下我们需要哪些界面和组件来构建一个简单的社交媒体应用。在这里，“界面”指的是在网页开发中通常所说的“页面”。这是应用的一个大块，由许多组件组成，一起呈现在屏幕上。

我们的应用肯定需要一个登录界面、一个社交媒体信息流界面和一个个人资料界面。我们还会添加一个包含收藏帖子的屏幕，以及一个用户可以添加他们帖子的屏幕。我们将为信息流和个人资料使用假数据，并为登录使用单个用户名和密码。我们不会实现注册流程，以便保持事情简单。

我们想专注于数据流，所以我们将使用一个免费的社交媒体 UI 工具包来“移除”设计，换句话说。以下是我们将使用的设计文件链接：[`www.pixeltrue.com/free-ui-kits/social-media-app`](https://www.pixeltrue.com/free-ui-kits/social-media-app)。

### 应用根目录

我们的应用将至少包含五个界面，这意味着我们需要设置导航以便在那些界面之间移动。用户将从登录界面开始。他们将填写他们的信息，然后将被重定向到社交媒体信息流界面。

显然，我们需要一种方式让我们的用户在应用中移动。最常用的导航库之一叫做**React Navigation**。这是一个专门为**React Native**应用创建的库。它提供了三种类型的导航：抽屉导航、标签导航和堆栈导航。抽屉导航是指你应用侧边有一个小抽屉，里面包含指向应用中不同位置的链接。标签导航将显示标签（底部或顶部），包含指向不同位置的链接。堆栈导航就像一堆卡片——每个屏幕都是一个卡片，具有重定向到任何其他卡片的能力。如果你想了解更多关于这个库的信息，你可以在*进一步阅读*部分找到文档链接。

现在还有其他导航库，但 React Navigation 无疑是**React Native**社区中最受欢迎的一个。它也正在积极维护和更新，以与最新的**React** **Native**版本兼容。

我们需要首先将库作为依赖项添加到我们的项目中。为此，请按照以下步骤操作：

1.  我们可以通过运行以下命令来添加库：

    ```js
    $ yarn add @react-navigation/native
    ```

1.  如果你访问文档网站，你会注意到有针对“**Expo**管理项目”和“裸**React Native**项目”的不同 CLI 命令。请确保遵循 Expo 管理项目的说明。在我们的情况下，我们需要运行以下命令：

    ```js
    $ expo install react-native-screens react-native-safe-area-context
    ```

1.  我们首先需要显示一个登录界面，这将把我们的用户重定向到主应用屏幕。为了做到这一点，我们将使用一个堆栈导航器。让我们将它的依赖项添加到我们的项目中，具体如下所示，请参阅[`reactnavigation.org/docs/stack-navigator/`](https://reactnavigation.org/docs/stack-navigator/)：

    ```js
    $ yarn add @react-navigation/stack
    ```

    ```js
    $ expo install react-native-gesture-handler
    ```

堆栈导航器的最后一步设置是在我们的`App.js`文件的最顶部导入手势处理库。

堆栈导航器将非常有用，可以管理我们应用的登录状态，但一旦用户登录，我们还需要底部标签导航来在其他屏幕之间切换。标签导航对应用用户来说感觉非常自然。它在所有屏幕上都是可见的，使得使用应用变得容易。

就目前而言，我们只需要运行一个命令：

```js
$ yarn add @react-navigation/bottom-tabs
```

此命令将底部标签导航作为依赖项添加到我们的项目中，这样我们就可以稍后使用它了。

你可能会想知道为什么我们需要分别添加这么多不同的依赖项。这是由于**React Navigation**的作者决定如何构建他们的库。他们确信大多数人不需要他们应用中的每一种导航，那么为什么他们应该在应用包中包含它呢？每个库用户都可以决定**React Navigation**的哪一部分对他们有用，并只包含那一部分。

让我们继续为我们的基本应用添加一些结构。每个应用至少由几个不同的界面组成，而这些界面又是由组件构成的。我们的基本社交媒体克隆应用需要一个登录界面和一个主界面，登录后可见。由于我们正在创建一个社交媒体应用，我们将主界面命名为“**Feed**”，因为它将包含用户的新闻源。随着我们的进展，我们肯定会添加更多界面，但这两个将是一个良好的起点。

### 设置界面

登录界面需要一个用户名输入字段、一个密码输入字段以及一个登录按钮。但到目前为止，我们将创建一个包含一些文本的虚拟组件。

我们将首先创建登录界面。您可能会想知道“创建一个界面”是什么意思。我的意思是，一些组件将是整个应用界面的包装器。有些人喜欢称它们为屏幕，在 Web 开发中，您会称它们为站点或页面。从编程的角度来看，它们就像任何其他组件一样是组件。但我们决定，从逻辑上讲，它们代表应用的一个更大的部分，我们将它们放在一个特殊的文件夹中，称为 `surfaces`。

这是我们的登录界面：

```js
// ./src/surfaces/Login.js
import React from "react";
import { View, Text } from "react-native";
export const Login = () => {
  return (
    <View>
      <Text>this will be the login screen</Text>
    </View>
  );
};
```

如您所注意到的，它实际上是一个名为 `Login` 的虚拟组件，放置在 `surfaces` 文件夹中。

使用相同的逻辑，我们将创建一个 `Feed` 界面，用户登录后应该显示：

```js
// ./src/surfaces/Feed.js
import React from "react";
import { View, Text } from "react-native";
export const Feed = () => {
  return (
    <View>
      <Text>this will be the feed screen</Text>
    </View>
  );
};
```

我们已经准备好了应用的两个基本部分；现在我们需要将它们组合起来。这就是 React Navigation 发挥作用的地方。

每个 React Native 应用都需要一个根文件，就像每个网站都需要在根目录下的 `index.html` 文件一样。这个根文件通常被称为 `App.js`。这是显示任何和所有内容的**真相来源**（**SOT**）。您可以将其视为一棵树的树干，从它那里长出了许多分支。在这个比喻中，分支是不同的应用界面。您明白了吗？我相信您一定明白了！您很聪明！毕竟，您正在**阅读**我的书。

让我们设置父组件以显示正确的流程——首先，登录界面，然后，内容流：

```js
// ./App.js
import 'react-native-gesture-handler';
import React, { useState } from "react";
import { NavigationContainer } from "@react-navigation/native";
import { createStackNavigator } from "@react-navigation/stack";
import { createBottomTabNavigator } from "@react-navigation/  bottom-tabs";
import { Login } from "./src/surfaces/Login";
import { Feed } from "./src/surfaces/Feed";
const Stack = createStackNavigator();
const Tab = createBottomTabNavigator();
function Home() {
  return (
    <Tab.Navigator>
      <Tab.Screen name="Feed" component={Feed} />
    </Tab.Navigator>
  );
}
export default function App() {
  const [userLoggedIn, setIsUserLoggedIn] = useState(true);
  return (
    <NavigationContainer>
      <Stack.Navigator>
        {!userLoggedIn ? (
          <Stack.Screen name="Login" component={Login} />
        ) : (
          <Stack.Screen
            name="Home"
            component={Home}
            options={{ headerShown: false }}
          />
        )}
      </Stack.Navigator>
    </NavigationContainer>
  );
}
```

您可以在以下 Expo Snack 中找到前面的代码：[`snack.expo.dev/@p-syche/simplifying-state-management---chapter-2-example-3`](https://snack.expo.dev/@p-syche/simplifying-state-management---chapter-2-example-3)。

在前面的代码中，您会注意到我们使用了 `useState` 钩子。这样，我们很容易地为我们的函数式 `App` 组件添加状态。我们将初始状态设置为 `false`——用户首次打开应用程序时不应登录。当用户登录时，他们将被重定向到我们的堆栈中的第二个“卡片”。这个“卡片”是 `Home` 组件。这是一个包装组件，用于包含我们应用程序的更大部分：除了 `Login` 之外的所有带有标签底部导航的表面。如您所注意到的，导航器是嵌套的：标签导航嵌套在堆栈导航器中。这在 **React Native** 应用程序中是一种常见且实用的做法。您可以在 **React Navigation** 文档中了解更多关于嵌套导航的信息：[`reactnavigation.org/docs/nesting-navigators`](https://reactnavigation.org/docs/nesting-navigators)。

就这样！我们使用 **Expo** 设置了一个应用程序。我们添加了代表应用程序未来表面的多个组件。我们还添加并配置了 **React** **Navigation** 库。我们的应用程序现在看起来不太美观，但它应该能工作。您可以通过 Expo Go 应用程序在您的手机上查看它，或者在计算机屏幕上的手机模拟器中查看。

我在 GitHub 上设置了一个公共仓库，以便您，亲爱的读者，可以更轻松地跟随本书中展示的代码片段和示例。您可以在以下位置找到该仓库：[`github.com/PacktPublishing/Simplifying-State-Management-in-React-Native`](https://github.com/PacktPublishing/Simplifying-State-Management-in-React-Native)。请随意克隆或分叉它。`main` 分支包括基本的应用程序设置。每个状态管理库的实现都在不同的分支上。我们将随着前进讨论细节。如果您决定使用这个仓库，您会注意到 UI 工具包中的样式已经实现。本书中我们将不会专注于样式，但它是对任何应用程序的一个很好的补充。

# 摘要

我们在这里做了一些真正的好工作！我们开始时查看了一些简单的代码示例，这些示例对于理解一些 ReactJS 编程概念是必要的，例如组件状态和属性、生命周期方法和钩子。理解并内化状态和属性、有状态和无状态组件之间的差异非常重要。对这些概念的良好掌握可以决定您的应用程序是否能够顺利运行。

在深入研究重要的 React 概念和示例之后，我们开始实际设置我们的应用程序。这是一个非常激动人心的时刻！我们有了基础，我们准备构建一个真实的社交媒体克隆应用程序。在下一章中，我们将熟悉预览和调试我们的应用程序。我们将设置所有必要的表面，我们将添加示例数据，最后我们将为应用程序添加样式。我迫不及待了！

# 进一步阅读

+   React Native 的文档——具有状态的组件示例：[`reactnative.dev/docs/intro-react#state.`](https://reactnative.dev/docs/intro-react#state)

+   状态与属性：

https://lucybain.com/blog/2016/react-state-vs-pros/.

[`github.com/uberVU/react-guide/blob/master/props-vs-state.md`](https://github.com/uberVU/react-guide/blob/master/props-vs-state.md).

+   将生命周期方法添加到类中—ReactJS 文档：

https://reactjs.org/docs/state-and-lifecycle.html#adding-lifecycle-methods-to-a-class.

+   关于 hooks 的完整博客文章：

https://pl.reactjs.org/blog/2019/02/06/react-v16.8.0.html.

+   ReactJS 关于 hooks 的文档：

https://reactjs.org/docs/hooks-reference.html#useeffect.

+   React Navigation 文档：

https://reactnavigation.org/docs/getting-started/.

+   React Navigation—底部标签导航：[`reactnavigation.org/docs/tab-based-navigation.`](https://reactnavigation.org/docs/tab-based-navigation)

+   React Navigation 关于身份验证流程的指南：[`reactnavigation.org/docs/auth-flow.`](https://reactnavigation.org/docs/auth-flow)

+   React Navigation 关于嵌套导航器的指南：[`reactnavigation.org/docs/nesting-navigators`](https://reactnavigation.org/docs/nesting-navigators).

# 第二部分 – 创建一个真实、可工作的应用

在本部分中，我们将专注于构建一个真实、可工作的移动应用。读者将学习如何规划应用功能并配置 Funbook 应用的真正设置；然后，他们将学习如何为 React Native 应用添加样式，使其与给定的设计相匹配，以及如何引入真实数据。

本部分包括以下章节：

+   *第三章*, [*规划和设置 Funbook 应用*](https://epic.packtpub.services/index.php?module=oss_Chapters&action=DetailView&record=552b05fa-8391-bf2a-d8d5-61f378c66211)

+   *第四章*, [*为 Funbook 应用添加样式和内容*](https://epic.packtpub.services/index.php?module=oss_Chapters&action=DetailView&record=e4875054-2e9a-297d-67db-61f3786c1c05)
