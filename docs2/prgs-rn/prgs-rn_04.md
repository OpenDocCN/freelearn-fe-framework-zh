# 4

# React Native 中的设计、存储和导航

现在您已经了解了 React Native 背后的基本概念，是时候深入探讨 React Native 最常见的一些领域了。

本章涵盖了不同的领域，所有这些在处理 React Native 时都很重要。当使用 React Native 创建大型应用时，您始终需要深入了解您应用的设计风格，以创建一个美观的产品。除了设计风格外，还有另一个决定用户是否从美学角度喜欢您的应用的因素——**动画**。然而，这将在*第六章*中介绍，*与动画一起工作*。

本章我们将关注的另一件事是如何在用户的设备上本地存储数据。每个平台的工作方式都不同。虽然 Android 和 iOS 相当相似，并且您可以访问具有巨大容量的设备存储，但在与网络工作时就完全不同了，那里的容量非常有限。

我们将要讨论的最后一件事是如何在您的 React Native 应用中在不同屏幕间导航。再次强调，这可能会因平台而异，但您将获得不同导航概念的全面概述。

在本章中，我们将涵盖以下主题：

+   理解如何设计 React Native 应用

+   在 React Native 中使用本地存储解决方案

+   理解 React Native 中的导航

# 技术要求

要运行本章中的代码，您必须设置以下内容：

+   一个有效的 React Native 开发环境([`reactnative.dev/docs/environment-setup`](https://reactnative.dev/docs/environment-setup) – React Native CLI 快速入门)

+   虽然本章的大部分内容也应该在 Windows 上工作，但我建议在 Mac 上工作

# 理解如何设计 React Native 应用

您可以从不同的解决方案中选择来处理 React Native 应用中的设计。但在我们查看最常见的一些之前，您必须理解其背后的概念。在本章中，我们将首先介绍所有这些解决方案试图实现的目标。

## 使设计可维护

在项目开始时，设计通常处理得非常糟糕，因为它不会干扰业务逻辑，因此不太可能引入错误。所以，大多数时候，当考虑应用架构时，大多数开发者会想到状态管理、数据流、组件结构等，但不会想到设计。当项目增长时，这总是要付出代价。保持一致的设计需要越来越多的时间，而更改 UI 变得真正痛苦。

因此，您应该在应用一开始时就考虑如何处理设计。无论您使用什么解决方案或库，您都应该始终遵循以下概念：

+   **使用中央文件存储颜色、字体和大小**：这应该是一个单独的文件，或者一个用于颜色，一个用于字体，一个用于大小，如边距、填充和边框半径。我更喜欢使用一个单独的文件。

+   **永远不要在您的组件/CSS 文件中硬编码值**：您永远不应该在组件中使用固定值。始终使用您在中央文件中定义的值。这保证了您的 UI 保持一致，并且如果您需要适应，您可以轻松地更改值。

+   **永远不要重复代码**：当您发现自己因为更容易、更快或更方便而复制组件部分样式时，请始终记住这并不是长期之计。重复的代码总是会导致 UI 不一致，并在您稍后想要更改某些内容时让您不得不触摸多个文件。因此，与其复制粘贴代码，不如将其提取到组件或样式文件中。您稍后将会了解更多关于这些选项的信息。

当我们带着这些概念回到我们的示例项目时，我们必须重构它，因为目前我们违反了所有这些概念。我们没有中央文件；我们在每个地方都硬编码了值，并且我们在多个文件中定义了`backButton`样式。

首先，让我们创建一个中央文件来存储我们的值。它可能看起来像这样：

```js
import {Appearance} from 'react-native';
const isDarkMode = Appearance.getColorScheme() === 'dark';
const FontConstants = {
  familyRegular: 'sans-serif',
  sizeTitle: 18,
  sizeRegular: 14,
  weightBold: 'bold',
};
const ColorConstants = {
  background: isDarkMode ? '#333333' : '#efefef',
  backgroundMedium: isDarkMode ? '#666666' : '#dddddd',
  font: isDarkMode ? '#eeeeee' : '#222222',
};
const SizeConstants = {
  paddingSmall: 2,
  paddingRegular: 8,
  paddingLarge: 16,
  borderRadius: 8,
};
export {FontConstants, ColorConstants, SizeConstants};
```

如您所见，我们所有的值都集中在一个地方。如果您再深入一点看，我们还为我们应用引入了暗黑模式，这只是一个使用我们的中央颜色存储的 3 分钟任务。我们只需要获取设备外观设置的信息，并相应地提供颜色。

注意

您可以在 iOS 模拟器上非常容易地测试您的暗黑模式应用。前往**设置**，滚动到最底部，选择**开发者**。**开发者**屏幕将打开；第一个开关激活**暗黑外观**。如果您使用我们的应用支持暗黑模式，您应该始终在两个模拟器上测试——一个在暗黑模式，一个在亮模式。

现在我们有了中央存储，让我们创建一个`<BackButton />`组件来消除重复的样式定义。它可能看起来像这样：

```js
interface BackButtonProps{
  text: string;
  onPress: () => void;
}
const BackButton = (props: BackButtonProps) => {
  return (
    <Pressable onPress={props.onPress}
      style={styles.backButton}>
      <Text>{props.text}</Text>
    </Pressable>
  );
};
const styles = StyleSheet.create({
  backButton: {
    padding: SizeConstants.paddingLarge,
    marginBottom: SizeConstants.paddingLarge,
    backgroundColor: ColorConstants.backgroundMedium,
  },
});
```

在我们新创建的组件中，我们不再使用固定值，而是引用我们的中央存储中的值。

最后，我们必须遍历我们的应用，用我们的新组件替换`backButton`可点击部分，并用对中央存储的引用替换固定值。这样，我们就遵守了这些概念。

这些概念是不同库或解决方案的核心。为了为您的项目选择正确的解决方案，最重要的决定之一是部署到哪个平台。以下小节将涵盖最常见解决方案，包括有关解决方案在哪个平台上运行最佳的信息。

## 选择正确的样式解决方案

在本小节中，我们将探讨内联样式、React Native 样式表、CSS 模块和 styled-components。所有四种解决方案都运行良好，各有优缺点。我们将从内联样式开始。

### 使用 React Native 内联样式

要理解内联样式，让我们看看一个代码示例。以下代码展示了上一章示例项目中的`<Header />`组件，但它使用了内联样式来设置`Text`组件的样式：

```js
const Header = (props: HeaderProps) => {
  return <Text style={{
          fontSize: 18,
          fontWeight: 'bold',
          marginBottom: 16}
        }>
            {props.text}
        </Text>;
};
```

如你所见，我们可以创建一个包含样式规则的对象。这可行，并且具有很大的优势。你不仅可以使用固定值，还可以使用你可以在组件中访问的任何静态或动态值。这非常有用，尤其是在你处理用户定义的主题时。但这种方法也有多个缺点。

首先，当项目规模扩大时，代码会变得相当混乱——至少，我认为当样式、组件和数据以这种方式混合时，代码难以阅读。因此，我会尽可能地将其分离。

接下来，你不能重用任何样式。每次你需要它们时，都必须复制你的样式。现在，你可以争辩说，你不需要复制样式，因为你可以简单地提取包含样式的组件到一个自定义组件中。尽管这是正确的，但有些情况下你不想这样做。我们将在下一小节中更深入地探讨这些场景。

接下来，我们必须考虑性能。内联样式对象将在每次渲染时被重新创建，这可能会对你的应用性能和内存使用产生负面影响。

最后，我们将探讨不同的平台。这种内联样式方法在构建不同平台时几乎没有优化空间。虽然这在 Android、iOS、Windows 和 macOS 上可能不是真正的问题，但对于 Web 来说，它可能会造成真正的痛苦，因为它会使你的包大小大大增加。

在 Web 上，你必须非常关注加载时间，因为用户没有安装你的应用程序的版本。此外，像 Google 这样的搜索引擎也非常关注加载时间，这会影响你的排名，产生正面或负面的影响。因此，你的样式代码必须在构建过程中进行优化，而内联样式无法做到这一点。

要利用优化优势，你必须使用样式表。我们将在下一节中探讨它们。

### 使用 React Native 样式表

在上一章的示例应用中，我们使用了样式表（StyleSheets），但在这里我们再次探讨它们，以便真正理解它们的优点。样式表不仅使代码更易读，支持样式和业务逻辑的良好分离，而且还能在应用的构建时间和运行时实现许多性能优化。

以下代码是我们示例应用中的`<Header />`组件。它使用 React Native 样式表进行样式设置：

```js
const Header = (props: HeaderProps) => {
  return <Text style={styles.title}>{props.text}</Text>;
};
const styles = StyleSheet.create({
  title: {
    fontSize: 18,
    fontWeight: 'bold',
    marginBottom: 16,
  },
});
```

在查看此代码时，你应该意识到以下几点：

+   首先，它更加清晰且易于分离。

+   第二，`StyleSheet` 是在组件外部定义的，这使得它在重新渲染之间保持持久。这在性能和内存使用方面更好。

+   第三，当你使用无法解释的样式时，`StyleSheet.create` 会在你的模拟器中创建错误。这可以在非常早期的阶段帮助你捕捉到错误。

但 StyleSheet 最大的好处是能够优化你的样式代码以适应网页。开源的网页库 react-native-web 能够很好地将你的应用程序中的所有 StyleSheet 分割成类，并将所需的类名添加到你的组件中。这使得你的代码更小、更优化，并且大大提高了加载时间。

除了所有这些优势之外，StyleSheet 还有一个问题。由于它们是在组件外部声明的，因此你无法访问组件变量，如状态和属性。这意味着，如果你想在你的样式中使用用户生成的值，你必须将 StyleSheet 的值与内联样式结合，如下所示：

```js
<Text style={[styles.title, {color:props.color}]}>{props.text}</Text>
```

这段代码会使用 StyleSheet 中的 `title` 风格，并为 `<Text />` 组件添加一个用户定义的颜色。这种结合方法也可以在处理动画时使用。你可以在*第六章* *处理动画* 中了解更多相关信息。

最后，我们将探讨 StyleSheet 的另一个好处。你可以在组件中多次使用一个样式。再次强调，如果你坚持我的建议，你永远不会需要这样做，因为在这种情况下你会创建一个自定义组件。但在日常工作中，有些情况下不创建组件更快，而且也不会造成伤害。

例如，如果你有一个包含两行文本的简单组件，你可以创建一个 `<TextLine />` 组件并使用两次，或者简单地使用两个具有相同样式引用的 `<Text />` 组件，并在 StyleSheet 中引用。

使用 `<TextLine />` 组件的第一种方法更为简洁，但第二种方法可以节省你的时间，并且从长远来看不会产生问题。因此，在这种情况下，StyleSheet 相比内联样式有另一个优势。

注意

当你多次使用相同的样式时，一定要小心。虽然这可能有帮助，但在许多情况下，你会重复代码，这些代码应该被提取到自定义组件中。

现在我们已经了解了这个内置解决方案，让我们看看需要外部库的两个解决方案。

### 使用 CSS 模块进行样式设置

CSS 模块在网页上非常流行。你使用 CSS、Sass 或 Less 来设置组件样式。在大多数情况下，你将为每个组件创建一个额外的样式文件。专家们经常争论这是好是坏。

你有一个额外的文件，但你将样式和组件之间的分离做得非常清晰。我确实喜欢这种分离，但如果你能将应用程序拆分成小的组件，直接将样式添加到组件中也是可以的，从我的角度来看。

在 React Native 中使用 CSS 模块需要一些额外的配置。由于 React Native 没有内置的 CSS 处理器，你必须将你的 CSS 代码转换为 JavaScript 样式，然后才能显示。这可以通过 babel 转换器来完成。

如果你需要在 React（网页）和 React Native 项目之间共享样式，而不使用 react-native-web 生成网页部分，CSS 模块可以是一个很好的选择。这尤其适用于你正在为现有的网页应用程序构建应用程序时。

这种方法的一个非常重要的问题是，你无法在 CSS 模块中使用你的 JavaScript 变量。尽管你可以创建和使用 CSS 变量，但这并不允许你在样式中使用用户生成的值。

如果你开始一个针对 Android、iOS、Windows 或 Mac 的新项目，我不建议使用 CSS 模块，因为这些平台，CSS 模块方法与 StyleSheets 没有优势。再次强调，我唯一推荐使用 CSS 模块的情况是当你为基于 CSS 模块的老旧网页应用程序构建应用程序时。

对于 React 网页项目，还有一个非常流行的解决方案，也可以用于 React Native。它被称为 **styled-components**，你将在下一个子节中了解它。

### 理解 styled-components

`View` 和 `Text`，你可以通过标签模板字面量来增强它们，创建新的组件，称为 styled-components。

以下代码展示了我们示例项目中用 styled-components 样式化的 `<Header />` 组件：

```js
import styled from 'styled-components/native';
const Header = (props: HeaderProps) => {
  return <StyledText>{props.text}</StyledText>;
};
const StyledText = styled.Text`
  font-size: ${FontConstants.sizeTitle};
  font-weight: ${FontConstants.weightBold};
  margin-bottom: ${SizeConstants.paddingLarge};
  color: ${ColorConstants.font};
`;
```

正如你所见，我们通过使用 styled-components 中的 `styled` 创建 `StyledText` 组件，并将模板字面量添加到 React Native 的 `Text` 组件中。在这个字面量内部，我们可以编写纯 CSS。这里酷的地方在于我们还可以使用 JavaScript 变量，甚至可以将属性传递给我们的 styled-component。这看起来会是这样：

```js
<StyledText primary>{props.text}</StyledText>;
```

这是我们向 `StyledText` 组件传递属性的方式。现在，我们可以在模板字面量中使用这个属性：

```js
const StyledText = styled.Text`
  font-size: ${props => props.primary ?
                          FontConstants.sizeTitle :
                            FontConstants.sizeRegular};
`;
```

这个函数被称为 **插值**，它使得在 styled-components 的 CSS 中使用用户生成的内容成为可能。

这太棒了，因为它解决了许多问题，支持结构和样式之间的清晰分离，并允许我们使用常规 CSS，这对于大多数开发者来说比 StyleSheets 中的驼峰式 CSS 更熟悉。

虽然我喜欢这种网络方法，但我对仅适用于应用的项目的这种方法持批评态度。styled-components 库为网络提供了许多有用的优化功能，但在纯 React Native 项目中，它也会将 CSS 编译成 JavaScript 风格。此外，它不提供对动画的支持，这是现代应用非常重要的一个部分。你可以在*第六章* *与动画一起工作*中了解更多关于这一点。

虽然我不会推荐在纯 React Native 项目中使用 styled-components，但当你尝试在 React Native 和 React 项目之间共享样式代码而不使用 react-native-web 时，它们可以非常有用。在这种情况下，你可以从 styled-components 中获得很多好处。

如果你想要深入了解 styled-components，我建议阅读官方文档[`styled-components.com/docs`](https://styled-components.com/docs)。

在本节中，我们学习了为 React Native 应用进行样式化的最重要的概念，并查看了一些最常用的实现样式的解决方案。大多数时候，你不会自己编写所有的样式，而是使用 UI 库。这将在*第九章* *提高 React Native 开发的必备工具*中处理。

如果你想要查看示例项目的所有更改，请查看此示例项目的存储库，并选择`chapter-4-styling`标签。

现在我们已经知道了如何为我们的应用进行样式化，是时候在用户的设备上存储一些数据了。

# 在 React Native 中使用本地存储解决方案

在移动应用中，存储数据是一个非常重要的任务。即使现在，你也无法保证移动设备始终连接到互联网。正因为如此，最佳实践是创建你的应用，使其尽可能多地具有功能，即使在没有互联网连接的情况下也是如此。话虽如此，你可以看到为什么在 React Native 应用中本地存储数据很重要。

对于本地存储解决方案来说，最重要的区分标准是它是否是一个安全的或非安全的存储解决方案。由于大多数应用至少存储了一些关于用户的信息，你应该始终考虑你想要将哪些信息放入哪个存储中。

重要

总是使用安全的存储解决方案来存储敏感信息。

虽然在安全存储中存储敏感数据很重要，但大多数数据，如用户进度、应用内容等，都可以存储在*普通*存储解决方案中。由于加密/解密和/或访问特殊设备功能，安全存储操作总是伴随着一些开销，因此你应该只为敏感信息使用它们，以防止对应用性能产生负面影响。

在下面的子节中，你将了解最常见的用于常规数据的存储解决方案。

## 存储非敏感数据

很长一段时间里，React Native 都自带了一个名为 AsyncStorage 的内置存储解决方案。但自从 Facebook 的 React Native 核心团队试图将 React Native 核心降至最低（轻量级核心）以来，AsyncStorage 被转交给社区进行进一步开发。

尽管如此，它得到了非常好的维护，并且很可能是最常用的存储解决方案。除了`AsyncStorage`之外，其他常见解决方案还包括`react-native-mmkv`/`react-native-mmkv-storage`、`react-native-sqlite-storage`/`react-native-quick-sqlite`和`react-native-fs`。所有这些解决方案都有其优缺点，工作方式完全不同，并且可以用于不同的任务。让我们从最受欢迎的一个开始。

### 与 AsyncStorage 一起工作

`AsyncStorage`是一个简单的键/值存储，可以用来存储数据。虽然它只能存储原始数据，但在存储之前必须将复杂对象序列化为 JSON。尽管如此，它非常易于使用。API 看起来是这样的：

```js
import AsyncStorage from '@react-native-async-storage/async-storage';
// set item
const jsonValue = JSON.stringify(value)
await AsyncStorage.setItem('@key', jsonValue)
// get item
const strValue = await AsyncStorage.getItem('@key')
const jsonValue = strValue != null ? JSON.parse(strValue) : null
```

如您所见，设置和获取数据的 API 非常简单。

`AsyncStorage`未加密，不能用于运行复杂查询。它是一个简单的键/值存储；没有数据库。它也不支持事务或锁定。这意味着在您向应用程序的不同部分写入/读取时必须非常小心。

我建议用它来存储用户进度、关于应用内容的信息以及任何不需要可搜索的数据。有关安装和使用`AsyncStorage`的更多信息，请参阅官方文档[`react-native-async-storage.github.io/async-storage/docs/install/`](https://react-native-async-storage.github.io/async-storage/docs/install/)。

相较于`AsyncStorage`，MMKV 是 React Native 的一个较新的替代方案。它的速度可以快到 30 倍，并且拥有更多功能。

### 在 React Native 中使用 MMKV

**MMKV**是由微信开发并用于其生产应用的本地存储解决方案。有多个 React Native 包装器用于这个本地解决方案；其中大多数已经基于 JSI，因此支持同步和超快访问。

与`AsyncStorage`一样，MMKV 是一个简单的键/值存储。这意味着在存储之前，复杂对象必须序列化为 JSON 字符串。API 几乎与`AsyncStorage`一样简单：

```js
import { MMKV } from 'react-native-mmkv'
export const storage = new MMKV()
// set data
const jsonValue = JSON.stringify(value)
storage.set('@key', jsonValue)
// get data
const strValue = storage.getString('@key')
const jsonValue = strValue!= null ? JSON.parse(strValue) : null
```

如您所见，得益于 JSI，API 是同步的，因此我们不需要处理 async/await 语法。在第二行，您可以看到存储的初始化。这是相较于`AsyncStorage`的一个优势，因为您可以使用多个 MMKV 存储实例。

虽然 MMKV 可以加密数据，但在撰写本文时，关于如何处理密钥尚无安全解决方案。因此，我仅建议用于存储非敏感数据。这在未来可能会有所改变。

MMKV 可以用作 `AsyncStorage` 的更快替代品。与 `AsyncStorage` 相比，MMKV 的唯一缺点是，在撰写本文时，React Native 包装器使用得并不多。有两个维护良好的 React Native MMKV 映射器，所以当你考虑在你的项目中使用 MMKV 时，你应该看看它们。你可以在那里找到有关安装、使用和 API 的更多信息。第一个是 `react-native-mmkv`。这是一个更精简的项目，并附带一个更简单的 API。它也更容易安装。你可以在这里查看它：[`github.com/mrousavy/react-native-mmkv`](https://github.com/mrousavy/react-native-mmkv)。第二个是 `react-native-mmkv-storage`。它提供了更多功能，例如索引和数据生命周期方法，这在锁定和事务处理时可能非常有用。你可以在这里查看它：[`github.com/ammarahm-ed/react-native-mmkv-storage`](https://github.com/ammarahm-ed/react-native-mmkv-storage)。

现在我们已经了解了处理非常类似用例的 `AsyncStorage` 和 MMKV，让我们看看一个带有更多功能的解决方案：SQLite。

### 与 SQLite 一起工作

与 `AsyncStorage` 和 MMKV 相比，SQLite 不仅仅是一个简单的键值存储 – 它是一个完整的数据库引擎，包括锁定、事务和高级查询等功能。

然而，这意味着你不能简单地以序列化数据的形式存储你的对象。SQLite 使用 SQL 查询和表来存储你的数据，这意味着你必须处理你的对象。要插入数据，你必须创建一个包含每个属性的列的表，然后使用 SQL 语句插入每个对象。让我们看看下面的代码：

```js
import { QuickSQLite } from 'react-native-quick-sqlite';
const dbOpenResult = QuickSQLite.open('myDB', 'databases');
// set data
let { status, rowsAffected } = QuickSQLite.executeSql(
  'myDB',
  'UPDATE users SET name = ? where userId = ?',
  ['John', 1]
);
if (!status) {
  console.log(`Update affected ${rowsAffected} rows`);
}
// get data
let { status, rows } = QuickSQLite.executeSql(
  'myDB',
  'SELECT name FROM users'
);
if (!status) {
  rows.forEach((row) => {
    console.log(row);
  });
}
```

如你所见，插入和查询数据需要更多的代码。你需要创建和执行 SQL，并处理你得到的数据，以便你可以以可以工作的格式使用它。这意味着 SQLite 并不像 `AsyncStorage` 和 MMKV 那样容易和快速使用，但它提供了高级查询功能。这意味着你可以过滤和搜索你的数据，甚至连接不同的表。

如果你拥有非常复杂的数据结构，需要大量地连接和查询不同的对象或表，我会推荐使用 SQLite。我更喜欢用于本地数据存储的简单解决方案，但也有一些情况，SQLite 是更好的选择。

除了使用它的更高复杂性之外，SQLite 还会增加一些 MB 到你的应用大小，因为它将它的 SQLite 数据库引擎实现添加到你的应用中。

最常用的 React Native SQLite 包装器是 `react-native-sqlite-storage`。API 简单，并被许多项目使用。你可以在 [`github.com/andpor/react-native-sqlite-storage`](https://github.com/andpor/react-native-sqlite-storage) 上了解更多信息。

另一个解决方案是 `react-native-quick-sqlite`。这是一个相对较新的库，但它基于 JSI，因此比其他解决方案快五倍。您可以在[`github.com/ospfranco/react-native-quick-sqlite`](https://github.com/ospfranco/react-native-quick-sqlite)了解更多信息。

现在您已经了解了 SQLite 数据库引擎，让我们看看另一个用例。有时，您需要存储大量数据，这意味着您需要直接访问文件系统。这就是我们接下来要探讨的。

### 在 React Native 中使用文件系统

要存储大量数据，创建并存储文件始终是一个好主意。在 iOS 和 Android 上，每个应用程序都在一个沙盒中运行，其他应用程序无法访问。虽然这并不意味着您的所有文件都是安全的——用户可以非常容易地检索它们——但它至少为您提供了关于您数据的一些隐私保护。然而，这种沙盒模式意味着您无法访问其他应用程序的数据。

在 React Native 中，要读取和写入应用程序沙盒中的数据，您可以使用如 `react-native-fs` 这样的库。这个库提供了您可访问的路径常量，并允许您从文件系统中读取和写入文件。

我建议在您从服务器同步文件或写入大量数据时使用这种方法。大多数情况下，您可以结合之前提到的一种方法来本地存储文件，然后将文件的路径存储在其他存储解决方案之一中。

如果您想了解更多关于 React Native 中文件系统访问的信息，请查看 `react-native-fs` 的文档，网址为[`github.com/itinance/react-native-fs`](https://github.com/itinance/react-native-fs)。

通过这样，我们已经涵盖了存储和访问非敏感数据的最常见解决方案。这是您应该存储大部分数据的地方。然而，某些数据包含敏感信息，如密码或其他用户信息。这些数据需要另一级别的保护。因此，让我们看看 React Native 中敏感信息的存储解决方案。

## 存储敏感数据

当您在用户的设备上存储敏感信息时，您应该始终考虑如何保护它。大多数情况下，这将是无关紧要的，但当一个用户丢失设备时，您应该确保他们的敏感信息尽可能安全。

当您无法控制设备时，您永远无法确保 100%的数据安全。然而，我们需要尽我们所能，使敏感信息尽可能难以被检索。

你首先应该考虑的是是否需要持久化信息。不存在的信息无法被盗取。如果你需要持久化信息，请使用安全存储。Android 和 iOS 提供了内置的安全存储数据解决方案。React Native 为这些原生内置解决方案提供了包装器。以下是一些维护良好且易于使用的解决方案：

+   `expo-secure-store`：使用 iOS Keychain 和 Android `SharedPreferences`结合 Keystore System。它提供了一个简单的 API，可以存储高达 2,048 字节的值。更多信息可以在[`docs.expo.dev/versions/latest/sdk/securestore/`](https://docs.expo.dev/versions/latest/sdk/securestore/)找到。

+   `react-native-sensitive-info`：这个库维护得很好，提供了很多功能。它还增加了一层安全保护，即使在 rooted 设备上也能保护你的数据。它支持 Android、iOS 和 Windows。更多信息可以在[`mcodex.dev/react-native-sensitive-info/`](https://mcodex.dev/react-native-sensitive-info/)找到。

+   `react-native-keychain`：这是一个维护得很好的库，具有简单的 API。它支持 Android 和 iOS，并在所有设备上加密数据。更多信息可以在[`github.com/oblador/react-native-keychain`](https://github.com/oblador/react-native-keychain)找到。

再次强调，尽管这些解决方案非常好且安全，基于原生实现，但数据永远无法达到 100%的安全。因此，请只保留必要的。

现在你已经了解了数据存储解决方案以及敏感数据和非敏感数据之间的区别，是时候看看 React Native 应用中的导航了。

# 理解 React Native 中的导航

React Native 没有内置的导航解决方案。这就是为什么我们在示例应用中与全局状态一起工作，并在导航时简单地切换组件。虽然这在技术上可行，但它并不提供良好的用户体验。

现代导航解决方案包括性能优化、动画、集成到全局状态管理解决方案中等等。在我们深入探讨这些解决方案之前，让我们看看不同平台上的导航是什么样的。

## 在不同平台上的导航

如果你打开任何 iOS 或 Android 应用，你很快就会意识到应用中的导航与在浏览器中导航网页完全不同。浏览器通过用新页面替换旧页面来从一个页面导航到另一个页面。除此之外，每个页面都有一个 URL，如果它在浏览器的地址栏中输入，可以直接访问。

在 iOS 或 Android 应用中，导航以不同导航器的组合形式出现。你导航离开的页面不一定会被新的页面替换。可以同时激活多个页面。

让我们来看看最常见的导航场景和用于处理这些场景的导航器：

+   **堆栈导航器**：当在堆栈导航器中导航到新页面时，新页面会推送到旧页面上方。然而，旧页面并不会被卸载。它将继续存在，如果你通过返回按钮离开新页面，你将自动导航回旧页面。新页面将从所谓的层堆栈中弹出，你将发现你的旧页面处于你离开时的相同状态。这也包括滚动位置。

+   **标签导航器**：一个非常受欢迎的导航器是标签导航器。这个导航器提供了最多五个可以通过标签栏选择的标签。这个标签栏包含文本和/或图标，可以位于屏幕顶部或底部。每个标签都有一个层堆栈。这意味着你可以单独导航每个标签。当你选择另一个标签时，标签的状态不会重置。在大多数情况下，你只是在你的标签导航器中有多重堆栈导航器。

+   **切换导航器**：这个导航器提供了与网络导航相同的行为。当使用这个导航器时，你会用新页面或层堆栈替换旧的一个。这意味着旧页面或层堆栈会被卸载并从内存中移除。如果你返回导航，旧页面或层堆栈将有一个完整的干净重启，就像你之前从未去过那里一样。

大多数应用都会结合这些导航器来为用户提供出色的导航体验。因为这种在移动应用中的常见导航体验与网络不同，所以在为移动和网页项目规划时，你应该始终牢记这一点。你将在*第十章* *结构化大规模、多平台项目*中了解更多关于这一点。

尽管多个社区项目为 React Native 应用中的导航提供了很好的支持，例如由 Wix 支持的 react-native-navigation（更多信息可以在[`wix.github.io/react-native-navigation/docs/before-you-start/`](https://wix.github.io/react-native-navigation/docs/before-you-start/)）和 react-router/native（更多信息可以在[`v5.reactrouter.com/native/guides/quick-start`](https://v5.reactrouter.com/native/guides/quick-start)）中，但本节我们将重点关注 react-navigation。它是迄今为止最常用的、最活跃维护的、最先进的 React Native 导航解决方案。

## 使用 React 导航

要了解 React 导航是如何工作的，最好的方法是将它简单地集成到我们的示例项目中。在这里，我们将做两件事。首先，我们将用 React 导航堆栈导航器替换我们的全局状态导航解决方案。然后，我们将添加一个标签导航器来创建第二个标签，我们将在下一章中使用它。

但在您开始使用 React Navigation 之前，您必须安装它。这个过程很简单——您只需通过 npm 安装包及其依赖项。这可以通过 `npm install @react-navigation/native react-native-screens react-native-safe-area-context` 命令完成。由于 `react-native-screens` 和 `react-native-safe-area-context` 有原生部分，您将需要使用 `npx pod-install` 命令安装 iOS Podfiles。之后，您将需要创建新的构建才能使用 React Navigation。对于 iOS，可以使用 `npx react-native run-ios` 来完成。

在撰写本文时，为了在 Android 上使 React Navigation 工作正常，需要一些额外的步骤。由于这可能会在未来发生变化，请查看官方文档中的安装部分，网址为 [`reactnavigation.org/docs/getting-started/#installation`](https://reactnavigation.org/docs/getting-started/#installation)。

现在我们已经安装了 React Navigation，是时候在我们的示例项目中使用它了。首先，我们将用 Stack Navigator 替换 `App.tsx` 中的全局状态导航。要使用 Stack Navigator，我们需要使用 `npm install @react-navigation/native-stack` 命令来安装它。然后，我们就可以在我们的应用中开始使用了：

```js
const MainStack = createNativeStackNavigator<MainStackParamList>();
const App = () => {
  return (
    <NavigationContainer>
      <MainStack.Navigator>
        <MainStack.Screen
          name="Home"
          component={Home}
          options={{title: 'Movie Genres'}}
        />
        <MainStack.Screen
          name="Genre"
          component={Genre}
          options={{title: 'Movies'}}
        />
        <MainStack.Screen
          name="Movie"
          component={Movie}
          options={({route}) =>
          ({title: route.params.movie.title})}
        />
      </MainStack.Navigator>
    </NavigationContainer>
  );
};
```

如您所见，我们的 `App.tsx` 变得简单多了。我们可以移除所有的 `useState` 钩子和所有的设置函数，因为 React Navigation 会处理所有这些。我们只需要创建一个 Stack Navigator，使用 React Navigation 的 `createNativeStackNavigator` 命令，然后在返回语句中返回我们的 Layer Stack。请注意 `<NavigationContainer />`，它包裹着整个应用。这是管理导航状态所必需的，通常应该包裹根组件。

在这里，每个屏幕都有一个名称、一个组件和一些选项。名称也是屏幕可以通过它导航到的键。`component` 是当屏幕被导航到时应挂载的组件。`options` 允许我们配置诸如标题和返回按钮等事项。

现在我们已经定义了 Layer Stack，是时候查看视图并看看那里有什么变化了。让我们看看 `<GenreView />`。这是我们可以看到所有变化最好的地方：

```js
type GenreProps = NativeStackScreenProps<MainStackParamList, 'Genre'>;
const Genre = (props: GenreProps) => {
  const [movies, setMovies] = useState<IMovie[]>([]);
  useEffect(() => {
    if (typeof props.route.params.genre !== 'undefined') {
      setMovies(getMovieByGenreId(props.route.params.genre.
        id));
    }
  }, [props.route.params.genre]);
  return (
    <ScrollContainer>
      {movies.map(movie => {
        return (
          <Pressable
            onPress={() =>
              props.navigation.navigate('Movie',
                {movie: movie})}>
            <Text
             style={styles.movieTitle}>{movie.title}</Text>
          </Pressable>
        );
      })}
    </ScrollContainer>
  );
};
```

您首先可以看到，有另一种方式可以访问通过 React Navigation 传递的属性。每个作为 React Navigation 屏幕的组件都会传递两个额外的属性——`navigation` 和 `route`。

`route` 包含关于当前路由的信息。`route` 中最重要的属性是 `params`。当我们导航到一个屏幕时，我们可以传递 `params`，然后可以通过 `route.params` 来检索它们。在这个例子中，这就是我们将类型传递给视图（`props.route.params.genre`）的方式，然后我们使用它来获取电影列表。

当你查看返回语句中`<Pressable />`组件的`onPress`函数时，你可以看到如何在 React Navigation 中导航到另一个页面。`navigation`属性提供了在不同屏幕之间导航的不同函数。在我们的例子中，我们使用带有`Movie`键的`navigate`函数来导航到`<Movie />`视图。我们还传递了当前电影作为参数。

当你将代码与上一节中的示例进行比较时，你会意识到`<Header />`和`<BackButton />`组件缺失。这是因为 React Navigation 自带内置的头部和后退按钮支持。虽然你可以禁用它，但它的默认行为是每个屏幕都有一个头部，包括返回到上一个屏幕的后退按钮。

如果你想查看所有这些更改，请查看此示例项目的仓库，并选择`chapter-4-navigation`标签。

如果你在这个标签上运行示例项目，你还会看到 React Native 为导航操作添加了动画。这些动画可以以任何可能的方式自定义。甚至有一个社区库来支持不同页面之间的共享动画元素。你可以在这里查看：[`github.com/IjzerenHein/react-navigation-shared-element`](https://github.com/IjzerenHein/react-navigation-shared-element)。

现在你已经学会了如何使用 Stack Navigator，我们将添加另一个导航器。我们想要创建一个第二个标签，因为我们想要创建一个用户可以保存他喜欢的电影的地方。这将通过 Tab Navigator 来完成。

与 Stack Navigator 一样，在使用之前我们必须安装 Tab Navigator。这可以通过`npm install @react-navigation/bottom-tabs`来完成。在我们安装了 Tab Navigator 之后，我们可以将其添加到我们的`App.tsx`中。请查看以下代码片段：

```js
const MainStackScreen = () => {
  return (
    <MainStack.Navigator>
      <MainStack.Screen component={Home}/>
      <MainStack.Screen component={Genre}/>
      <MainStack.Screen component={Movie}/>
    </MainStack.Navigator>
  );
};
const App = () => {
  return (
    <NavigationContainer>
      <TabNavigator.Navigator>
        <TabNavigator.Screen
          name="Main"
          component={MainStackScreen}
          options={{
            headerShown: false,
          }}
        />
        <TabNavigator.Screen
          name="User"
          component={User}
        />
      </TabNavigator.Navigator>
    </NavigationContainer>
  );
```

这是一个非常有限的示例。要查看工作代码，请查看示例仓库并选择`chapter-4-navigation-tabs`标签。正如你所看到的，我们将主 Stack 移动到其自己的函数组件中。我们的`App`组件现在包含`<TabNavigator />`和两个屏幕。

第一个屏幕使用`<MainStackScreen />`作为其组件。这意味着当我们处于第一个标签时，我们使用我们的 Stack Navigator。第二个屏幕使用一个新创建的`<User />`组件。你可以通过标签栏在这些标签之间切换，标签栏是由 React Navigation 自动创建的。

注意

当你与标签一起工作时，你应该始终安装一个图标库，例如`react-native-vector-icons`([`github.com/oblador/react-native-vector-icons`](https://github.com/oblador/react-native-vector-icons))。这样的库使得在标签栏中找到和使用表达性图标变得容易。

这个例子包含两个不同的导航器，展示了 React Navigation 的灵活性。我们可以在 `<Navigator.Screen />` 组件中使用我们的视图，或者使用其他导航器。这种导航嵌套给我们带来了几乎无限的可能性。请注意，在这种情况下，我们必须隐藏第一个标签页的标题，因为它已经被我们的 Stack Navigator 创建了。我们可以通过 `headerShown: false` 选项来实现这一点。

如您所见，使用 React Navigation 进行导航既简单又强大。它还提供了出色的 TypeScript 支持，正如您在仓库中可以看到的那样。您可以为每一层栈创建类型，并精确地定义可以传递给不同屏幕的内容。这包括不仅限于类型检查，还包括在大多数现代 IDE 中的自动完成功能。您可以在[`reactnavigation.org/docs/typescript/`](https://reactnavigation.org/docs/typescript/)了解更多关于 React Navigation 对 TypeScript 的支持。

React Navigation 支持许多其他功能，包括深度链接、测试、持久化导航状态以及集成不同的状态管理解决方案。如果您想了解更多信息，请访问官方文档：[`reactnavigation.org/docs/getting-started/`](https://reactnavigation.org/docs/getting-started/)。

# 摘要

现在我们已经将一个现代导航库添加到我们的示例项目中，是时候总结本章内容了。首先，您学习了在您希望美化应用程序时需要考虑的因素。您还了解了美化 React Native 应用程序最常见的方法，并学习了哪些方法适合与网络项目共享代码。

然后，您学习了如何在 React Native 应用程序中本地存储数据。最后，您学习了在网页和移动端之间导航的不同之处，以及如何使用现代导航库在 React Native 应用程序中实现最先进的导航解决方案。

在下一章中，我们将探讨创建和维护全局应用状态的方法，以及如何从外部资源获取数据。在学习这些内容的同时，我们将用一些酷炫的功能填充本章中创建的占位符屏幕。
