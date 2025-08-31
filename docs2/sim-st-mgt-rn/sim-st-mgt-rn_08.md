# 8

# 在 React Native 应用中集成 Jotai

在上一章中，我们探索了**XState**的数学世界。我们将继续我们的旅程，通过探索另一个名为**Jotai**的年轻状态管理库来继续前进。**Jotai**受到了在**Facebook**创建的一个实验性状态管理库 Recoil 的启发。在本章中，我们将简要了解**Recoil**，这是 Facebook 创建的一个实验性状态管理库。一旦我们熟悉了这个库的主要思想，即一个名为“原子状态”的新概念，我们将深入探讨**Jotai**。我们将在我们的应用中配置 Jotai，并借助**Jotai**继续进行数据获取和管理点赞图片的工作。以下是本章我们将涉及的内容：

+   **Recoil**和原子状态是什么？

+   什么是**Jotai**？

+   在 Funbook 应用中配置**Jotai**

+   使用`FavoritedImages`

到本章结束时，你将有一种新的看待全局状态管理的方法——通过将其划分为称为**原子**的小项目。你还将了解如何在新的项目中设置**Jotai**，以及如何使用它进行数据获取和数据管理。

# 技术要求

为了跟上本章的内容，你需要了解一些**JavaScript**和**ReactJS**的知识。如果你至少跟随着本书的*第一章到第四章*，你应该能够没有问题地继续学习。

随意使用你选择的 IDE，因为**React Native**不需要任何特定功能。目前，前端开发者中最受欢迎的 IDE 是微软的 VSCode、Atom、Sublime Text 和 WebStorm。

本章提供的代码片段旨在说明我们应该如何使用代码。它们并不提供完整的画面。为了更容易地编码，请在你选择的 IDE 中打开 GitHub 仓库，查看其中的文件。你可以从名为`example-app-full`或`chapter-8`的文件夹中的文件开始。如果你从`example-app-full`开始，你将负责实现本章描述的解决方案。如果你选择查看`chapter-8`，你将看到我实现的整个解决方案。

如果你遇到困难或迷失方向，可以查看 GitHub 仓库中的代码：[`github.com/PacktPublishing/Simplifying-State-Management-in-React-Native/tree/main/chapter-8`](https://github.com/PacktPublishing/Simplifying-State-Management-in-React-Native/tree/main/chapter-8)。

# **Recoil**和原子状态是什么？

如果你一直按章节顺序阅读这本书，你可能觉得不同类型的状态管理库列表永远没有尽头。在某种程度上，这是正确的。每隔几周就会出现新的状态管理库；它们有时是纯开源的，有时是公司支持的。然而，它们很少提出突破性的解决方案。更常见的是，它们是已知概念的较新实现。这些实现受到了极大的欢迎，因为每个开发者都喜欢舒适地工作——那么，那些已知的概念是什么呢，你可能想知道？

在**ReactJS**世界中，有一个共识，即状态管理库可以分为三种类型：

1.  Flux 类型 - 这些是持有状态在组件之外的状态管理库，并使用单向数据流。它们受到了**Facebook 的 Flux**的启发，最著名的例子是**Redux**。这种流有现代的实现，如**Redux Toolkit**或**Zustand**。

1.  代理类型 - 这些库“包装”状态，概念上类似于代理所做的那样。当使用这种类型的状态管理时，开发者可以像读取组件中的任何其他值一样订阅和读取包装值。代理类型状态管理的最佳例子是**React 的 Context**、**MobX**或**Valtio**。

1.  原子类型 - 这是最低级别的状态集合，由类组件中的`setState`和函数组件中的`useState`钩子自然管理。以这种方式设置的价值可以在应用程序中传递并用于更大的上下文中。**Facebook**创建了一个实验性库来推广这种类型的状态管理，称为**Recoil**。**Jotai**很快也效仿。

**回弹**（Recoil）是在 2020 年中期创建的，并迅速获得了大量关注。它是由 Facebook 自己发布的，Facebook 也是 React 的创造者，因此每个人都期待着一个全新的解决方案。使用尽可能小的状态片段，并在**React**应用中分散和可访问的想法非常吸引人。不幸的是，在最初的兴奋之后，React 社区中很大一部分人对**Recoil**失去了兴趣，继续使用 Redux 进行日常工作。两年后，**Recoil**的文档仍然声明它是实验性的，而且很少有人谈论它。

尽管如此，有一小群开发者比我们其他人更加关注。*Poimandres*，一个开源开发者集体，开始工作并创建了他们自己的原子状态实现。他们称之为**Jotai**。如果你访问他们的 GitHub 页面，你会看到他们也开发了**Valtio**，一个代理类型的状态管理库，以及**Zustand**，一个轻量级的 flux 类型状态管理库。**Valtio**和**Zustand**到目前为止在更著名的替代品阴影之下，但**Jotai**在原子状态管理舞台上占据了主导地位。这个库是生产就绪的；它正在通过**GitHub**积极开发，并且其开发者在一个开放的 Discord 服务器上提供持续的支持。这就是为什么我们将讨论**Jotai**，而不是**Recoil**，在本章中。

# 什么是 Jotai？

如前所述，`useState`)或外部。Daishi 一直在努力开发新事物；你可以在*Jotai Labs* **GitHub**仓库[`github.com/jotai-labs`](https://github.com/jotai-labs)中观察他的所有工作。他对开发用于获取和**React 的 Suspense**的功能也很感兴趣。你可以在*进一步* *阅读*部分找到更多关于他的项目的链接。

我们现在对为什么创建 Jotai 有了很好的理解。它旨在从新的视角解决状态管理问题，遵循 React 的最佳实践和实验性 Recoil 库提出的概念。是时候在我们应用中尝试这种“原子”状态方法了。让我们开始编码吧！

# 在 Funbook 应用中配置 Jotai

如果你是一个简单性的粉丝，我亲爱的读者，你可能会爱上这个状态管理库。在我们的应用中配置它只需要在终端运行`install`命令：

```js
npm install jotai
```

或者，查看以下内容：

```js
Yarn add jotai
```

需要添加的一个隐藏的配置宝石是 Suspense。我特意用了宝石这个词，因为**Jotai**的这项配置要求将使你的应用崩溃更少。Suspense 是 ReactJS 的新功能，旨在只能渲染准备好渲染的组件。就像任何新功能一样，用户需要习惯它，有时甚至需要被迫尝试它。**Jotai**正是这样做的：强迫用户使用 Suspense，这对他们自己是有好处的！让我们继续在我们的应用根目录中添加它：

```js
// ./App.js
import React, { useState, Suspense } from "react";
export default function App() {
//…
  if (!fontsLoaded) {
    return <AppLoading />;
  }
  return (
    <SafeAreaProvider>
//…
            <Suspense fallback={<AppLoading />}>
              <NavigationContainer theme={MyTheme}>
                <Stack.Navigator>
//…
```

现在，我们的应用可以使用`ListOfFavoritedImages`。

# 使用 Jotai 进行 ListOfFavoritedImages

你可能注意到我们没有对**Jotai**进行太多的理论介绍。这是因为这个库非常简洁。没有样板代码，没有复杂的概念。我们只需要创建一个原子并使用它，多亏了应用中的自定义钩子。让我们先创建一个带有一些模拟数据的喜欢图片原子的例子：

```js
// src/atoms/imagesAtoms.js
import { atom } from "jotai";
export const imageListAtom = atom([
  {
    "itemId": 1,
    "authorId": 11,
    "timeStamp": "2 hrs ago",
    "url": "…",
    "likes": "28",
    "conversations": "12"
  },
  {
    "itemId": 2,
    "authorId": 7,
    "timeStamp": "1 week ago",
    "url": "…",
    "likes": "8",
    "conversations": "123"
  },
]);
```

我们已经有了模拟的图像数组；我们现在需要做的就是使用它。鉴于我们之前与其他状态管理库的经验，你可能期望看到某种设置、包装器、订阅或其他类似的东西。很抱歉让你失望，但我们现在需要做的只是如下使用`ListOfFavoritedImages`组件：

```js
import { useAtom } from "jotai";
import { imageListAtom } from "../atoms/imagesAtoms";
export const ListOfFavorites = ({ navigation }) => {
  const [imageList] = useAtom(imageListAtom);
  if (!imageList) {
    return <AppLoading />;
  }
//…
  return (
    //…
      <FlatList
        data={imageList}
//…
```

在前面的代码中，我们导入了`useAtom`以及我们在`imagesAtom`文件中创建的原子。那么结果如何呢？让我们在模拟器中运行应用并找出答案！

![图 8.1 – 基于 Jotai 原子的应用显示图像](img/Figure_8.01_B18396.jpg)

图 8.1 – 基于 Jotai 原子的应用显示图像

一切都正常！我必须承认，这感觉几乎像魔法。当然，获取数据会更复杂吗？

## 使用 Jotai 获取数据

我们在我们的应用中成功设置了模拟的图像数据，但我们希望从服务器获取真实数据。回到**Jotai**文档，我们将找到一个关于异步原子的指南（你可以在*进一步阅读*部分找到该文档的链接）。以下是我们的用于获取图像的异步原子的样子：

```js
// src/atoms/imageAtoms.js
import { requestBase } from "../utils/constants";
import { atom } from "jotai";
export const imageListAtom = atom([]);
  const urlAtom = atom(requestBase + "/john_doe/likedImages.    json");
export const fetchImagesAtom = atom(async (get) => {
  const response = await fetch(get(urlAtom));
  return await response.json();
});
```

我们添加了`requestBase`导入以更舒适地使用 URL。然后，我们继续创建一个具有特定 URL 的基本原子。最后一个函数是异步原子。我们知道它是异步的，因为它使用了`async`关键字。异步原子函数的主体是一个`fetch`函数和数据返回。原子已经准备好了，但还没有连接到任何东西。我们需要在应用中调用它，并让它填充`imageListAtom`。让我们从调用获取操作开始。这样做的好地方是在用户登录后应用根目录。这意味着我们不会在`App.js`根组件中获取数据，而是在`Home`组件中：

```js
// src/surfaces/Home.js
import { useAtom } from "jotai";
import { fetchImagesAtom } from "../atoms/imageAtoms";
//…
export const Home = () => {
  const [json] = useAtom(fetchImagesAtom);
```

我们首先导入必要的部分：从`console.log`到组件的自定义钩子，并查看`json`的值是否与预期相同。顺便说一句，原子返回的命名没有规则。你也可以这样写：

```js
  const [thisIsAVeryFancyAndCuteFetchingMechanism] =    useAtom(fetchImagesAtom);
```

如果你使用 linter 插件（例如`json`值被声明但未使用。如果我们不对它们做任何事情，获取图像有什么好处？我们应该如何处理它们？我们应该让新获取的图像数组填充`imageListAtom`。完成这个任务的方法是将我们的只读`imageListAtom`改为读写原子。

## 读取和写入原子

啊！终于有一些理论了！我敢肯定，亲爱的读者，你一定渴望这些！（由于在技术文本中传达讽刺很难，让我抓住这个机会解释一下：上一句话是讽刺的）。

原子有三种类型：只读、只写和读写原子。只读原子是最简单的：你所做的就是创建它们，并设置它们需要保留的值，例如：

```js
const onlyReadMe = atom('I like to read')
```

只读原子可以保存比简单的值或字符串更多的内容。如果你需要在原子中实现更复杂的逻辑，你应该使用以下语法：

```js
  const readMeButInUpperCase  = atom((get) =>    get(onlyReadMe).toUpperCase())
```

在前一个简短的代码片段中，你可以观察到原子可以访问一个`getter`函数，而这个函数反过来又可以访问其他原子。

如果我们想要给我们的原子添加写功能，我们可以在原子的第二个参数中添加一个`setter`函数：

```js
const readMeButInUpperCase  = atom(
      (get) => get(onlyReadMe).toUpperCase(),
      (get, set, newText) => {
          set(onlyReadMe, newText)
       }
)
```

我们添加了一个新函数，它将接受一个新的文本并将其传递给`onlyReadMe`原子。如果你要在组件中使用它，它看起来会是这样：

```js
const FancyTextComponent = () => {
    const [fancyText, setFancyText] =      useAtom(readMeButInUpperCase  );
return (
      <Pressable onPress={() => setFancyText         ('I do not like to swim')>
        <Text>Likes and dislikes: {fancyText}</Text>
    </Pressable>
)
```

在前一个截图中的示例组件中，你可以观察到如何实现读写原子。我们首先导入原子，但声明了两个值：值和设置器，这与我们在常规`useState`钩子中使用的方法非常相似。在组件的较低部分，我们使用`{fancyText}`来显示原子中的文本，并使用`setFancyText`函数通过按钮点击来设置新的文本。

我们可以讨论的最后一种原子是只写原子。这种原子与读写原子的唯一区别在于我们声明读取参数为`null`。以下是一个示例：

```js
const onlyUsedForSettingValues  = atom(null,
       (get, set) => {
           set(onlyReadMe, 'I like using write only atoms')
       }
)
```

当使用这种类型的原子时，你总是需要确保为非存在的默认值适配钩子。以下是如何在前面的示例组件中使用这个只写钩子的方法：

```js
const FancyTextComponent = () => {
const [readOnlyFancyText] = useAtom(onlyReadMe);
    const [, setStaticText] =      useAtom(onlyUsedForSettingValues  );
return (
    <Pressable onPress={() => setFancyText()>
        <Text>Likes and dislikes: { readOnlyFancyText }</Text>
    </Pressable>
)
```

注意到数组中有来自`useAtom`钩子的值，其中的逗号表示第一个索引处有一个空值，但我们选择不使用它。

## 向 imageListAtom 添加读写功能

到目前为止，我们有一个只读的`imageListAtom`和一个异步的`fetchImagesAtom`。让我们给`imageListAtom`添加写功能，以便它可以接受来自`fetchImagesAtom`的值：

```js
// src/atoms/imageAtoms.js
export const imageListAtom = atom([], (get, set, newArray) => {
  set(imageListAtom, newArray);
});
```

原子已经准备好接收值了，所以让我们给它一些值。我们必须回到启动数据获取的`Home`组件，并添加一个`useEffect`，它将更新`imageListAtom`。以下是代码应该看起来像什么：

```js
// src/surfaces/Home.js
export const Home = () => {
  const [json] = useAtom(fetchImagesAtom);
  const [, setAllImages] = useAtom(imageListAtom);
  useEffect(() => {
    if (json) {
      setAllImages(json);
    }
  }, [json]);
```

这是个检查应用是否一切正常的好时机，因为我们刚刚实现了数据获取。如果一切确实按预期工作，我们将继续实现`console.log`的功能，以检查原子是否持有并返回你期望它们拥有的值。如果你继续遇到问题，可以加入*Poimandres* Discord 服务器（在*进一步阅读*部分有链接），在那里你可以找到一个**Jotai**专属频道。**Jotai**的作者**Daishi Kato**会亲自回答这个频道上的各种问题。

一旦你确定一切正常，我们将继续实现`ImageDetailsModal`。

## 实现喜欢按钮

`ImageDetailsModal`的完整功能由两部分组成：心形图标是否完整——表示图片是否已被喜欢，以及实际喜欢图片的动作——这意味着将新图片添加到收藏表面的图片数组中。

让我们先创建心形图标所需的原子。我们需要知道一个给定的图像是否已被点赞。我们可以通过过滤图像数组并检查给定的图像是否存在于数组中来确定它是否已被点赞。下面是生成的原子将看起来像什么：

```js
// src/atoms/imageAtoms.js
  export const isImageLikedAtom = atom(false,    (get, set, newImage) => {
  const imageList = get(imageListAtom);
  const checkIfLiked =
      imageList?.filter((favoritedImg) => favoritedImg.itemId         === newImage.itemId)
      .length > 0;
  set(isImageLikedAtom, checkIfLiked);
});
```

根据原子语法，我们首先将默认值设置为`false`。然后添加一个设置函数，该函数将接收新的图像对象。在设置函数内部，我们使用`get`函数获取`imageListAtom`并检查当前的图像对象是否与之匹配。最后，我们将`isImageLikedAtom`设置为正确的值。一旦原子创建完成，我们就需要在组件中使用它：

```js
// src/surfaces/ImageDetailsModal.js
export const ImageDetailsModal = ({ navigation, route }) => {
    const [isCurrentImageLiked, setIsLiked] =      useAtom(isImageLikedAtom);
  setIsLiked(route.params.imageItem);
//…
```

你可能想知道为什么我们如此粗糙地调用`setIsLiked`函数——为什么不使用`useEffect`？事实上，我们需要这个函数在组件渲染时被调用，并且仅在此之后。我们可以添加一个带有空依赖数组的`useEffect`钩子，但这会使结果看起来更复杂。

它何时运行？

React 组件的生命周期有一些微妙之处。在类组件中，这些微妙之处更为明显，我们会使用`componentDidMount`、`componentWillUnmount`等。函数组件有相同的生命周期，但没有那么明显。而且，`useEffect`钩子仅在给定组件完成渲染后运行，而直接调用的函数则不需要等待渲染完成。

就我们的示例而言，我们不需要在调用`setIsLiked`函数之前确保渲染完成。然而，大型应用程序往往对开发者要求很高，你可能会遇到需要密切控制给定原子设置函数（或任何其他函数）何时运行的情况。你可以在“*Further reading*”部分的链接中了解更多关于这个主题的信息：“*Difference between ‘useEffect’ and calling function directly inside a component*”。

回到我们的用例：我们有一个非常好的`isImageLiked`原子。你可以通过在**Feed**表面打开图像模态来测试它是否正确工作——那里的心形图标应该是空的——以及在**收藏**表面——那里的心形图标应该是满的。

现在，让我们转到点赞操作！我们在这里不需要做任何太花哨的事情。我们必须获取`imageListAtom`并向其中添加一个新图像：

```js
// src/atoms/imageAtoms.js
export const addImageToArray = atom(
         null,
         (get, set, newImage) => {
          const clonedArray = get(imageListAtom);
          clonedArray.unshift(newImage);
          set(imageListAtom, clonedArray);
          set(isImageLikedAtom, newImage);
         }
);
```

就像示例中的只写原子一样，我们首先声明一个空值作为默认原子值。在设置函数中，我们获取`imageListAtom`并使用`unshift`函数添加新图像，该函数将项目添加到原始数组的开头。我们通过将新创建的数组设置为`imageListAtom`并在`isImageLikedAtom`中触发设置器来完成。让我们将此添加到模态组件中：

```js
// src/surfaces/ImageDetailsModal.js
export const ImageDetailsModal = ({ navigation, route }) => {
  const [, addImage] = useAtom(addImageToArray);
  const [isCurrentImageLiked, setIsLiked] = useAtom(isImageLikedAtom);
  setIsLiked(route.params.imageItem);
return (
//…
    <Pressable
          onPress={() => {
            if (isCurrentImageLiked) {
              // add remove image functionality here
            } else {
              addImage(route.params.imageItem);
            }
          }}
        >
            <Ionicons name={isCurrentImageLiked ? "heart" :              "heart-outline"} />
        </Pressable>
//…
```

我们必须将`addImageToArray`原子导入到我们的组件中，然后在按钮被点击的正确位置调用它。让我们测试我们的应用！很可能会一切正常。你可以点击空的心形图标，它会变成满的，然后关闭模态框并转到`FlatList`。

React Native 的`FlatList`是一个纯组件，这意味着除非明确指示，否则它不会重新渲染。我们已经在使用`FlatList`时遇到过这个问题。`FlatList`的`extraData`属性——我们可以将原子值传递给`useState`，让自然状态重新渲染组件。我们还可以利用 React Navigation 库提供的工具。这是我最喜欢的方法，也是我选择使用的方法。在`Favorites`界面中有一个`useIsFocused`自定义钩子：

```js
// src/surfaces/Favorites.js
import { useIsFocused } from "@react-navigation/native";
export const Favorites = ({ navigation }) => {
  const isFocused = useIsFocused();
  return (
      <SafeAreaView style={{ flex: 1, paddingTop: headerHeight         }}>
      <Suspense fallback={<AppLoading />}>
        <ListOfFavorites navigation={navigation} isFocused={isFocused} />
//…
```

使用这个钩子，每次这个标签页获得焦点时，`Favorites`界面都会重新渲染。当然，这是一个需要谨慎使用的钩子。过多的重新渲染会导致应用意外崩溃。如果你决定使用它，请确保重新渲染是必要的。

是时候再次访问 Funbook 应用了！在本节中，我们首先使用了一个基本的钩子和一个模拟的图片数组。然后我们实现了使用`ImageDetailsModal`的数据获取，并检查你的**收藏**界面上的图片是否正确更新。

# 摘要

在本章中，我们介绍了**Jotai**，这是状态管理库中的新成员。受 Facebook 通过其名为**Recoil**的库提出的新原子状态管理方法的启发，**Jotai**在 React 社区中越来越受欢迎。它提供了一种自下而上的方法，与自上而下的库（如**Redux**或**MobX**）相反。它确实非常容易配置和使用。它不提供很多工具，但文档非常清晰且易于使用。在本章中，我们成功地使用它来获取和存储数据，我们还用它来实现对数据的操作，例如向数组中添加项目。**Jotai**标志着我们与经典状态管理库的旅程的结束。

在下一章中，我们将讨论**React Query**，它不是一个状态管理库，而是一个数据获取库。然而，它在这本书中也有其位置。更多内容将在下一章中介绍！那里见！

# 进一步阅读

+   [`marmelab.com/blog/2022/06/23/proxy-state-with-valtio.html`](https://marmelab.com/blog/2022/06/23/proxy-state-with-valtio.html): 状态管理之旅：使用 Valtio 进行代理状态。

+   [`github.com/facebookexperimental/Recoil/tree/main`](https://github.com/facebookexperimental/Recoil/tree/main): *Recoil* GitHub 页面。

+   [`opencollective.com/pmndrs`](https://opencollective.com/pmndrs): Poimandres 网站。

+   [`github.com/dai-shi/react-suspense-fetch`](https://github.com/dai-shi/react-suspense-fetch): `react-suspense-fetch`.

+   [`github.com/dai-shi/react-hooks-fetch`](https://github.com/dai-shi/react-hooks-fetch): `react-hooks-fetch`。

+   [`github.com/dai-shi/react-hooks-worker`](https://github.com/dai-shi/react-hooks-worker): `react-hooks-worker`。

+   [`jotai.org/docs/guides/async`](https://jotai.org/docs/guides/async): *Jotai –* *异步*。

+   [`discord.com/invite/poimandres`](https://discord.com/invite/poimandres): Poimandres Discord 服务器。

+   [`www.geekyhub.in/post/difference-between-useeffect-and-direct-function-call/`](https://www.geekyhub.in/post/difference-between-useeffect-and-direct-function-call/): *“useEffect”与在组件内部直接调用函数之间的区别*。

+   [`reactnavigation.org/docs/function-after-focusing-screen/#re-rendering-screen-with-the-useisfocused-hook`](https://reactnavigation.org/docs/function-after-focusing-screen/#re-rendering-screen-with-the-useisfocused-hook): React Navigation `useIsFocused` 钩子。
