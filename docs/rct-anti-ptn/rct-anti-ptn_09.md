

# 第九章：在 React 中应用设计原则

设计原则就像指导软件开发的基本规则，确保代码在长时间内保持可维护性、可扩展性和可读性。在技术不断变化的领域中，坚持这些原则可能是项目长期成功与陷入“代码地狱”之间的区别，在“代码地狱”中，更改变得越来越困难，错误也变得频繁。

对于 React 应用程序，由于库的声明性特性和基于组件的架构，设计原则的重要性日益凸显。React 使开发者能够从称为组件的小型、独立的代码片段构建复杂的 UI。虽然这种模块化方法是 React 最强大的功能之一，但如果忽视设计原则，也可能导致代码库变得混乱且难以管理。

在一个典型的 React 项目中，组件通常共享状态和行为，相互嵌套，并在应用程序的不同部分中重用。如果不遵循设计原则，你可能会发现自己陷入依赖关系的网中，这使得更改或甚至理解代码变得困难。例如，忽视**单一职责原则**可能导致难以测试和重构的组件，而忽视**接口隔离原则**可能导致你的组件更难以重用，并且与特定用例耦合得更紧密。

此外，随着 React 的不断发展，新特性如 Hooks 和并发模式，以设计原则为中心的方法确保你可以适应这些变化，而无需进行重大的重写。这让你可以专注于构建功能、修复错误和交付价值，而不是与技术债务作斗争。

在 React 开发中坚持设计原则不仅是一种最佳实践，也是一种必要性。它作为一种主动措施来对抗复杂性，使你的 React 代码更容易阅读、测试和维护。

在本章中，我们首先回顾了 SRP（单一职责原则），这是一个经常作为干净、可维护代码基石的核心概念。从简单的字符串转换函数的朴素起点，我们将探讨这一原则如何扩展到渲染属性等复杂性的复杂性，从而丰富你的 React 组件的结构和可读性。

从这里过渡，我们介绍了**依赖倒置原则**（**DIP**），这是一种改变组件设计的革命性方法。本节强调，关注接口——而不是实现的细节——是构建可重用且易于理解的组件的途径。

本章的结尾，我们深入探讨了**命令查询责任分离**（**CQRS**），这是一个随着你的 React 应用程序规模和复杂性的增长而变得重要的模式。通过讨论 CQRS，你将发现分离应用程序的命令和查询责任的战略，从而使应用程序更易于管理和扩展。

本章旨在帮助你全面理解关键设计原则，这将为你掌握 React 的其余旅程打下坚实的基础。

在本章中，我们将涵盖以下主题：

+   重温单一职责原则

+   接受依赖倒置原则

+   理解 React 中的命令和查询职责分离

# 技术要求

已经创建了一个 GitHub 仓库来托管本书中讨论的所有代码。对于本章，你可以在[`github.com/PacktPublishing/React-Anti-Patterns/tree/main/code/src/ch9`](https://github.com/PacktPublishing/React-Anti-Patterns/tree/main/code/src/ch9)找到推荐的架构。

# 重温单一职责原则

在*第四章*中，我们探讨了在设计和 React 组件的背景下 SRP（单一职责原则）。然而，这个原则更为普遍，是各种其他编程原则的基础。为了使这个想法变得生动，让我们通过一些实际案例来操作。

确定组件的核心职责对于遵循 SRP 至关重要。一旦你确定了组件的基本功能，就更容易重构和抽象出辅助功能。

SRP 作为一个高级指导原则，在直接应用于代码层面时具有优势。有无数种方法可以实现这个原则，但认识到何时应用它至关重要，尤其是在复杂性增加时。

我们将使用最常见的技术是*渲染属性*和*组合*。渲染属性指的是 React 中的一种技术，通过一个值为函数的 prop 在组件之间共享代码。具有渲染属性的组件接收一个返回 React 元素的函数，并调用它而不是实现自己的渲染逻辑。另一方面，React 中的组合是一种开发模式，其中你将组件构建成小的、可重用的部分，然后将它们组合在一起以创建更复杂的 UI。

在下一节中，我们将探讨两个具体的例子，展示我们如何分别使用渲染属性和组合来在实际中遵循这个原则。

## 探索渲染属性模式

让我们从简单的函数组件`Title`开始：

```js
const Title = () => <div>Title │ This is a title</div>
```

就目前而言，这个组件仅仅输出一个静态字符串。为了使其能够渲染不同的标题，我们引入了一个`title` prop：

```js
const Title = ({ title }: { title: string }) => <div>Title │ {title}</div>;
```

通过这个变化，组件变得更加灵活，将一个固定的前缀`Title |`附加到我们传递的任何标题上。但如果我们想进一步操作标题，比如将其转换为大写字母怎么办？

通过使用高阶函数——以下代码片段中的`transformer`参数——我们可以按如下方式修改我们的`Title`组件：

```js
const Title = ({
  title,
  transformer,
}: {
  title: string;
  transformer: (s: string) => string;
}) => <div>Title │ {transformer(title)}</div>;
```

注意

在许多编程语言（包括 JavaScript）中，数组的`map`、`filter`和`reduce`、函数组合、柯里化和事件处理。高阶函数简化了代码结构，提高了可维护性，并允许使用更高级的编程技术。

太好了——我们的标题现在可以完全自定义。但让我们更进一步。如果我们想将标题放在`h3`标签内而不是简单的`div`标签中怎么办？React 已经为我们解决了这个问题——我们可以传递一个返回 JSX 元素的函数：

```js
const Title = ({
  title,
  render,
}: {
  title: string;
  render: (s: string) => React.ReactNode;
}) => <div>{render(title)}</div>;
```

注意渲染属性的使用——我们将其作为一个函数调用，并传递`title`。

要使用渲染属性，我们可以将一个匿名函数（在大括号内）传递给它，如下面的代码所示：

```js
<Title
  title="This is a title"
  render={(s: string) => {
    const formatted = s.toUpperCase();
    return <h3>{formatted}</h3>;
  }}
/>
```

在 React 中，这个高阶函数不一定必须命名为`render`。我们同样可以使用`children`属性进行更直观的设计，如下所示：

```js
const Title = ({
  title,
  children,
}: {
  title: string;
  children: (s: string) => React.ReactNode;
}) => <div>{children(title)}</div>;
```

这允许我们像调用常规函数一样调用`children`：

```js
<Title title="This is a title">
  {(s: string) => {
    const formatted = s.toUpperCase();
    return <h3>{formatted}</h3>;
  }}
</Title>
```

`Title`组件接收一个`title`属性和一个子函数（后者被称为`s`，将其转换为大写，并在`h3`标签内渲染。`Title`组件使用提供的标题属性调用这个子函数以进行自定义渲染。

在 React 中，渲染属性模式涉及将一个函数作为属性传递给组件。这个函数返回组件将作为其输出的一部分渲染的 JSX。这种模式通过让父组件控制子组件渲染逻辑的一部分，使得组件更加灵活和可重用。这对于在多个组件之间共享行为特别有用。

注意这里起主导作用的模式：抽象。最初，我们可能会将`h2`或`h3`视为标题的具体实例。然而，当我们稍微放大视角时，我们开始理解它们是更广泛抽象的一部分：一个 React 组件，或者更技术性地，`ReactNode`。

这一认识使我们能够看到使用渲染属性或子组件作为高阶函数的实用性。它们不仅仅是特性；它们代表了我们所达到的抽象层次。现在，我们不再局限于特定的 HTML 标签，如`h3`，我们可以传递任何 JSX 元素作为参数，从标题到完全样式化的组件。

通过我们新创建的通用组件，它使用渲染属性，我们实际上创建了一个可重用的框架。其美妙之处在于我们只需要编写一次这种通用代码。

渲染属性和组合是处理此类问题的优秀技术。它们允许你在不改变组件核心逻辑的情况下扩展或自定义组件的行为。这使你的组件保持整洁、模块化，并且易于测试，因为每个组件只做一件事，并且做得很好。我们已经看到了在`Title`组件演变过程中渲染属性是如何工作的，现在让我们看看组合。

## 使用组合来应用 SRP（单一责任原则）

**组合**是我们全书多次使用的一个术语，其核心是 SRP。如果系统的每个部分都能很好地完成其工作，那么就可以将它们组合在一起。让我们检查一个具体的例子。

假设我们有一个包含便捷功能的`Avatar`组件的设计系统：如果用户将`name`属性传递给组件，那么当鼠标悬停在头像上时，一个包含名字内容的提示框会出现在头像底部：

![图 9.1：带有 Tooltip 的 Avatar 组件](img/B31103_09_01.jpg)

图 9.1：带有 Tooltip 的 Avatar 组件

在内部，`Avatar`组件利用另一个组件`Tooltip`来实现这一功能：

```js
import Tooltip from "@xui/tooltip";
type AvatarProps = {
  name?: string;
  url: string;
};
const Avatar = ({ name, url }: AvatarProps) => {
  if (name) {
    return (
      <Tooltip content={name}>
        <div className="rounded">
          <img src={url} alt={name} />
        </div>
      </Tooltip>
    );
  }
  return (
    <div className="rounded">
      <img src={url} alt="" />
    </div>
  );
};
```

`Avatar`组件接受两个可选属性`name`和`url`，并使用提供的 URL 显示图像。如果提供了`name`属性，它将图像包裹在一个`Tooltip`组件中，当鼠标悬停时显示名字。`div`标签使用了`rounded`类，这将使头像以圆形呈现。

`Avatar`组件的原始代码将其紧密耦合到`Tooltip`功能上。随着用户对 tooltip 的定制选项需求增加，保持这种耦合变得具有挑战性。添加更多属性来处理 tooltip 定制可能会使`Avatar`组件膨胀，并产生连锁反应：任何对`Tooltip`的改变都可能需要修改`Avatar`，这使得管理变得困难。

我们不是将`Tooltip`强加到`Avatar`中，而是可以将`Avatar`简化，仅关注其主要功能——显示图像。这个简化版的`Avatar`排除了 tooltip，减少了其包的大小，并使其更易于维护。以下是简化后的`Avatar`组件的示例：

```js
const Avatar = ({ name = "", url }: AvatarProps) => (
  <div className="rounded">
    <img src={url} alt={name} title={name} />
  </div>
);
```

通过这样做，我们使得`Avatar`和`Tooltip`组件可以组合使用，这意味着它们可以独立工作。消费者可以选择将`Tooltip`包裹在`Avatar`中，如下面的代码片段所示：

```js
import Avatar from "@xui/avatar";
import Tooltip from "@xui/tooltip";
const MyAvatar = (props) => (
  <Tooltip
    content="Juntao Qiu"
  >
    <Avatar
      name="Juntao Qiu"
      url="https://avatars.githubusercontent.com/u/122324"
    />
  </Tooltip>
);
```

代码从`"@xui"`库中导入`Avatar`和`Tooltip`组件。然后定义了一个`MyAvatar`组件，用于显示`"Juntao Qiu"`的头像（如果这里不需要名字，我们则不使用`Tooltip`组件）。当你鼠标悬停在头像上时，一个包含**Juntao Qiu**名字的提示框会出现在顶部，背景为蓝色，字体颜色为白色。

这种方法的优点是双重的：

+   `Avatar`组件保持精简，减少了其包的大小。

+   消费者有自由定制`Tooltip`或使用不同的 tooltip 库，而不会影响`Avatar`。

简而言之，这种分离使得代码更加模块化，用户只需为他们实际使用的功能“付费”，即支付代码和复杂度。

在渲染属性和组合的示例中，我们强调了现代 Web 开发中 SRP（单一职责原则）的本质。SRP 主张构建只做一件事并且做得好的组件，使它们更易于维护、重用和灵活。

接下来，让我们转向讨论 DIP，这是补充这些设计原则的另一个关键视角。

# 接受依赖倒置原则

**DIP** 是构成 SOLID 的五个原则之一，SOLID 是一套旨在帮助开发者创建更易于维护、灵活和可扩展的软件的指南。具体来说，DIP 鼓励开发者依赖于抽象，而不是具体实现。

注意

SOLID 的五个原则是单一职责原则、开闭原则、里氏替换原则、接口隔离原则和依赖倒置原则。

DIP 解决了开发者在构建和维护大型系统时面临的几个挑战。其中一个问题是紧密耦合模块带来的刚性。当高级模块依赖于低级模块时，即使是低级代码的微小更改也可能产生广泛的影响，需要整个系统进行更改。

## 理解 DIP 的工作原理

在高级模块和低级模块的术语中，让我们考虑一个系统中的通知功能。在这里，我们希望以用户偏好的形式发送通知，无论是电子邮件、短信还是两者兼而有之：

```js
class EmailNotification {
  send(message: string, type: string) {
    console.log(`Sending email with message: ${message}, type: ${type}`);
  }
}
class Application {
  private emailNotification: EmailNotification;
  constructor(emailNotification: EmailNotification) {
    this.emailNotification = emailNotification;
  }
  process() {
    // perform some actions to response user interaction
    this.emailNotification.send("Some events happened", "info");
  }
}
const app = new Application(new EmailNotification());
app.process();
```

在代码中，`EmailNotification` 类有一个名为 `send` 的方法，该方法接受 `message` 和 `type` 作为参数。然后它打印一条日志，表明正在发送一个具有此 `message` 和 `type` 的电子邮件。另一方面，`Application` 类有一个 `process` 方法，该方法模拟某种用户交互。在这个方法内部，`Application` 使用 `EmailNotification` 的一个实例在调用 `process` 时发送电子邮件。

在这里需要注意的一个重要事项是，`Application` 与 `EmailNotification` 是紧密耦合的。这意味着，如果你想要改变通知的发送方式，比如使用短信而不是电子邮件，你将不得不直接修改 `Application` 类，从而违反了单一职责原则（SRP），使系统变得不那么灵活。

因此，为了解决这个问题，我们可以引入一个 `Notification` 接口，并让 `EmailNotification` 实现该接口。这意味着我们可以有多个接口的实现。此外，`Application` 不再依赖于 `EmailNotification` 类，而是依赖于 `Notification` 接口。因为我们依赖于接口，从 `Application` 的角度来看，传入的具体实现是哪一个并不重要，只要它实现了 `Notification` 接口——这意味着我们可以轻松地将其更改为 `SMSNotification` 类。以下是所有这些代码的示例：

```js
interface Notification {
  send(message: string, type: string): void;
}
class EmailNotification implements Notification {
  send(message: string, type: string) {
    console.log(`Sending email with message: ${message}, type: 
     ${type}`);
  }
}
class Application {
  private notifier: Notification;
  constructor(notifier: Notification) {
    this.notifier = notifier;
  }
  process() {
    // perform some actions to response user interaction
    this.notifier.send("Some event happened", "info");
  }
}
```

代码定义了一个具有 `send` 方法的 `Notification` 接口，然后由 `EmailNotification` 类实现。现在，`Application` 类使用任何遵循 `Notification` 接口的对象进行构造。在其 `process` 方法中，`Application` 使用此对象发送通知。这种设置将 `Application` 类与特定的通知机制解耦，使其更加灵活且易于更改或扩展。

例如，如果我们决定用 `SMSNotification` 替换 `EmailNotification`，则 `Application` 类不需要任何修改；我们只需提供一个实现 `Notification` 接口的不同实例：

```js
const app = new Application(new EmailNotification());
app.process();
// or
const app = new Application(new SMSNotification());
app.process();
```

好的，这就是 DIP（依赖倒置原则）的简要介绍。让我们看看另一个例子，以了解如何在 React 应用程序内部应用相同的原理。

## 在分析按钮中应用 DIP

现在，假设你有一个通用的按钮组件，它在应用程序的各个部分中都被使用。你希望在按钮被点击时发送分析事件，但具体如何发送这些事件应该从按钮组件本身抽象出来。

问题在于，通用按钮已经在许多产品中被广泛使用，并不是所有这些都需要分析功能。所以，如果你简单地更改共享的 `Button` 组件中的 `onClick` 处理程序，将会让许多无辜的用户感到烦恼。

让我们看看当前的 `Button` 实现如下：

```js
const Button = ({ onClick: provided, name, ...rest }: ButtonProps) => {
  const onClick = (e) => {
    // emit an event to the analytic server
    return provided(e);
  };
  return <button onClick={onClick} {...rest} />;
};
```

相反，我们可以定义一个新的组件，它将原始按钮包裹起来，并劫持点击处理程序以进行分析：

```js
import { Button } from "@xui/button";
const FancyButton = ({
  onClick: originalOnClick,
  ...rest
}: FancyButtonProps) => {
  const onClick = (e) => {
    //emit an event to the analytic server
    console.log('sending analytics event to a remote server');
    return originalOnClick(e);
  };
  return <Button onClick={onClick} {...rest} />;
};
```

新代码定义了一个 `FancyButton` 组件，它围绕一个基本的 `Button` 组件。当点击时，`FancyButton` 首先向远程服务器发送一个分析事件，然后继续执行传递给它的原始 `onClick` 函数。所有其他属性都直接传递到底层的 `Button` 组件。

这里的问题是，许多使用 `Button` 组件的实例可能包含类似的分析代码，导致代码库中存在重复的逻辑。这种冗余是不希望的，因为任何对分析逻辑的更改都需要在多个位置进行更新，增加了出错的风险。

因此，让我们考虑 DIP。我们将在原始的 `Button` 组件中做一些更改，但不是直接发送分析事件，而是首先提取一个接口，并让按钮依赖于这个接口（记住，这个接口可能有多个实现）。

就像之前的 `Notification` 示例一样，`EmailNotification` 是发送电子邮件的通知渠道之一。在这个按钮示例中，一种实现方式是发送一个事件，而对于完全不使用分析的产品，它们只是传递一个空的实现。

为了进行这个更改，我们需要定义一个新的接口类型，并且需要一个上下文来存放接口的实现：

```js
import { createContext } from "react";
export interface InteractionMeasurement {
  measure(name: string | undefined, timestamp?: number): void;
}
export default createContext<InteractionMeasurement | null>(null);
```

此代码创建了一个名为`InteractionMeasurement`的 React 上下文，其中包含一个指定`measure`方法的接口。此方法接受一个名称（可以是字符串或`undefined`）和一个可选的时间戳，上下文初始化为`null`。

在`Button`组件内部，我们可以使用`useContext`来访问我们定义的上下文：

```js
import InteractionContext, {
  InteractionMeasurement
} from "./InteractionContext";
const Button = ({ name, onClick: providedOnClick, children }: ButtonType) => {
  const interactionContext = useContext<InteractionMeasurement | 
   null>(
    InteractionContext
  );
  const handleClick = useCallback(
    (e) => {
      interactionContext &&
      interactionContext.measure(name, e.timeStamp);
      providedOnClick(e);
    },
    [providedOnClick, interactionContext, name]
  );
  return <button onClick={handleClick}>{children}</button>;
};
```

代码定义了一个使用`InteractionContext`来跟踪点击的`Button`组件。当按钮被点击时，它会从上下文中调用`measure`方法，传入按钮的名称和点击事件的戳记。然后，它继续执行提供的任何额外的`onClick`逻辑。这样，点击跟踪就被抽象到上下文中，使`Button`组件更具可重用性和可维护性。

如果`interactionContext`为`null`，则不会调用`measure`函数，组件将仅执行作为属性传入的`providedOnClick`函数。这允许基于`InteractionContext`的可用性进行可选的分析跟踪。

这将完美解决我们遇到的问题——如果一个产品想要启用分析功能，他们可以在包含`InteractionMeasurement`实现的上下文中使用`Button`。

话虽如此，假设我们有一个使用`Button`组件的`FormApp`应用程序，该组件位于`InteractionContext`实例内部：

```js
import InteractionContext from "./InteractionContext";
import { Button } from "@xui/button";
const FormApp = () => {
  const context = {
    measure: (e, t) => {
      //send event and timestamp to remote
      console.log(`sending to remote server  ${e}: ${t}`);
    },
  };
  const onClick = () => {
    console.log("submit");
  };
  return (
    <InteractionContext.Provider value={context}>
      <form>
        <Button name="submit-button" onClick={onClick}>
          Submit
        </Button>
      </form>
    </InteractionContext.Provider>
  );
};
```

`FormApp`组件在其上下文对象内部的`measure`函数中定义了自己的分析逻辑。然后，它通过`InteractionContext.Provider`将此函数传递给子组件。当表单内的按钮被点击时，不仅会执行按钮的特定`onClick`逻辑，`measure`函数还会将事件和时间戳数据发送到远程服务器进行分析。这种设置允许基于上下文进行基于上下文的分析，而无需将`Button`组件绑定到特定的实现。

对于不想使用分析功能的用户，他们可以像平常一样使用`Button`组件：

```js
import { Button } from "@xui/button";
const App = () => {
  const onClick = () => {
    console.log("checkout");
  };
  return (
    <Button name="checkout-button" onClick={onClick}>
      Checkout
    </Button>
  );
};
```

这种方法提供了卓越的灵活性和动态性，对于设计通用组件来说非常宝贵。它增强了代码的可重用性和系统的可维护性，同时减少了整体包的大小。

请注意，在这种情况下添加额外的`context`对象可能最初看起来有些过度。然而，在大型代码库中，当不同的团队在独立的部分上工作时，这种方法变得更加相关。例如，一个专注于分析的团队可能与其他目标不同的设计系统团队相比，其目标是开发通用和原子的组件。设计系统团队可能不关心分析方面。因此，在这种情况下直接修改`Button`组件可能是不切实际或具有挑战性的。

话虽如此，我想介绍另一个我在代码中不断使用的原则；你可以将其视为 SRP（单一职责原则）的一种特殊形式。这个原则是 CQRS。

# 理解 React 中的命令和查询责任分离

**命令和查询责任分离**（**CQRS**）原则（也称为 **命令和查询分离原则**）是一种软件设计原则，它建议方法或函数应该是修改系统状态的命令或返回系统状态信息的查询，但不能两者兼具。

**命令**（或 **修饰符**）是执行操作或改变对象状态但不返回值的方法。另一方面，**查询**是读取对象状态而不进行任何更改的方法。将命令和查询分开可以帮助减少组件之间的耦合，使测试、维护和修改代码变得更加容易。它还使推理代码的行为变得更加容易，并可以改善系统的整体设计。

尽管这种模式在大型项目中广泛使用，例如在设计系统架构时，它同样在代码层面上也表现良好。我将在 `ShoppingCart` 组件中演示这一点：

```js
type Item = {
  id: string;
  name: string;
  price: number;
}
const ShoppingApplication = () => {
  const [cart, setCart] = useState<Item[]>([]);
  const addItemToCart = (item: Item) => {
    setCart([...cart, item]);
  };
  const removeItemFromCart = (id: string) => {
    setCart(cart.filter((item) => item.id !== id));
  };
  const totalPrice = cart.reduce((total, item) => total + item.price, 
   0);
  return (
    <div>
      <ProductList addToCart={addItemToCart} />
      <h2>Shopping Cart</h2>
      <ul>
        {cart.map((item) => (
          <li key={item.id}>
            {item.name} - {item.price}
            <button onClick={() => removeItemFromCart(item.
             id)}>Remove</button>
          </li>
        ))}
      </ul>
      <p>Total Price: {totalPrice}</p>
    </div>
  );
};
```

`ShoppingApplication` 组件使用 `useState` Hook 和一个类型为 `Item` 的项目数组来维护购物车。`addItemToCart` 函数向购物车添加新项目，而 `removeItemFromCart` 函数则根据其 `id` 值移除项目。`totalPrice` 是购物车中所有项目价格的累加。

组件渲染购物车中的项目列表及其总价。每个项目都有一个在点击时移除项目的 `removeItemFromCart`。同时渲染一个 `ProductList` 组件，它接收 `addItemToCart` 作为属性以添加产品到购物车。

初看之前的代码似乎没问题，但它包含一些微妙的问题。一个问题是在购物车中添加多个相同的产品时，键值会重叠，从而触发 React 关于唯一键的警告。此外，如果你在这种情况下点击 **移除** 按钮，它将删除购物车中该产品的所有实例，这远远不是理想的，并且会导致糟糕的用户体验。

为了修复这些问题，我们需要向 `Item` 类型引入一个新的 `uniqKey` 字段。我们还需要在将项目插入 `cart` 数组之前生成一个唯一的键。有了这个唯一的 ID，我们最终能够通过 `uniqKey` 而不是 `id` 来移除项目。代码将如下所示：

```js
const addItemToCart = (item: Item) => {
  setCart([...cart, { ...item, uniqKey: `${item.id}-${Date.now()}` }]);
};
const removeItemFromCart = (key: string) => {
  setCart(cart.filter((item) => item.uniqKey !== key));
};
```

我们还需要更新在 JSX 中渲染购物车的方式：

```js
<h2>Shopping Cart</h2>
<ul>
  {cart.map((item) => (
    <li key={item.uniqKey}>
      {item.name} - {item.price}
      <button onClick={() => removeItemFromCart(item.uniqKey)}>
        Remove
      </button>
    </li>
  ))}
</ul>
```

虽然代码在技术上听起来合理，并且对于当前的范围来说足够直接，但随着我们向 `ShoppingApplication` 组件添加更多状态和计算，应用 CQRS 原则可以提供一种结构化的方式来保持一切井然有序。

我们将使用 React Context API 和 `useReducer` Hook 来为 `ShoppingApplication` 组件实现 CQRS。现在让我们来看看它们。

## 引入 useReducer

React 中的`useReducer`钩子用于在函数组件中进行状态管理；它在下一个状态依赖于前一个状态或当你有复杂的状态逻辑时特别有用。`useReducer`钩子接受两个参数：一个 reducer 函数和一个初始状态，它返回当前状态和一个`dispatch`方法来触发更新。

对于第一个参数，reducer 函数接收当前状态和一个`action`对象，它包含有关如何更新状态的信息。该函数应根据操作类型和负载返回新状态。第二个参数是要传递的初始状态，它将在调用时用作默认值。

让我们为我们的`ShoppingApplication`组件定义一个 reducer 函数：

```js
type ShoppingCartState = {
  items: Item[];
  totalPrice: number;
};
type ActionType = {
  type: string;
  payload: Item;
};
const shoppingCartReducer = (
  state: ShoppingCartState = initState,
  action: ActionType
) => {
  switch (action.type) {
    case "ADD_ITEM": {
      const item = {
        ...action.payload,
        uniqKey: `${action.payload.id}-${Date.now()}`,
      };
      return { ...state, items: [...state.items, item] };
    }
    case "REMOVE_ITEM":
      const newItems = state.items.filter(
        (item) => item.uniqKey !== action.payload.uniqKey
      );
      return { ...state, items: newItems };
    default:
      return state;
  }
};
```

因此，`shoppingCartReducer`是一个函数，它接受两个参数——当前状态和一个操作：

+   状态的类型是`ShoppingCartState`，它包括一个项目数组和`totalPrice`

+   操作的类型是`ActionType`，它包括一个用于标识操作的`string`类型和一个包含`Item`对象的负载

在 reducer 函数内部，使用`switch`语句来确定正在派发的哪个操作。`"ADD_ITEM"`情况将一个新项目添加到状态中的`items`数组。这个项目被赋予一个唯一的键`uniqKey`，以区分相同的项目。`"REMOVE_ITEM"`情况根据这个唯一键从`items`数组中删除一个项目。

通过使用这种结构，reducer 函数提供了一种可预测的方式来管理购物车状态，以响应不同的操作。注意，在这个 reducer 函数中没有什么特别的地方；它只是一个普通的 JavaScript 函数。为了了解它是如何工作的，我们可以用以下代码测试 reducer 函数：

```js
const item = {
  id: "p1",
  name: "iPad",
  price: 666,
};
let x = shoppingCartReducer(initState, {
  type: "ADD_ITEM",
  payload: item,
});
console.log(x);
```

我们会得到类似这样的结果（显然，你的`uniqKey`值会与我的不同，因为它是当项目被添加时生成的）：

```js
{
    "items": [
        {
            "id": "p1",
            "name": "iPad",
            "price": 666,
            "uniqKey": "p1-1696059737801"
        }
    ],
    "totalPrice": 0
}
```

好吧——这应该能让你对 reducer 函数是什么以及它是如何与任何给定输入一起工作的有一个大致的了解。现在，让我们看看我们如何将其与我们的应用程序连接起来。

## 在上下文中使用 reducer 函数

让我们看看我们如何使用 reducer 函数来实现 CQRS 来简化我们的购物车示例。首先，我们需要一个上下文来管理购物车状态，并公开查询函数供组件使用：

```js
import React, { createContext, useContext, useReducer } from "react";
import { Item } from "./type";
type ShoppingCartContextType = {
  items: Item[];
  addItem: (item: Item) => void;
  removeItem: (item: Item) => void;
};
const ShoppingCartContext = createContext<ShoppingCartContextType | null>(null);
export const ShoppingCartProvider = ({
  children,
}: {
  children: React.ReactNode;
}) => {
  const [state, dispatch] = useReducer(shoppingCartReducer, {
    items: [],
    totalPrice: 0,
  });
  const addItem = (item: Item) => {
    dispatch({type: ADD_ITEM, payload: item});
  };
  const removeItem = (item: Item) => {
    dispatch({type: REMOVE_ITEM, payload: item});
  };
  return (
    <ShoppingCartContext.Provider value={{items: state.items, addItem, 
     removeItem}}>
      {children}
    </ShoppingCartContext.Provider>
  );
};
```

这段代码创建了一个用于管理购物车的 React 上下文。在`ShoppingCartProvider`内部，它使用`useReducer`钩子来处理购物车操作。两个函数`addItem`和`removeItem`派发操作来修改购物车。`Provider`组件通过`ShoppingCartContext`将购物车状态和这些函数提供给其子组件。这允许任何嵌套组件与购物车进行交互。

注意，`addItem`和`removeItem`是 CQRS 原则中的两个命令函数，它们只改变状态而不返回任何数据。如果我们想获取数据，我们可以定义一个查询函数，如下所示：

```js
export const useTotalPrice = () => {
  const context = useContext<ShoppingCartContextType>(
    ShoppingCartContext
  );
  const {items} = context;
  return items.reduce((acc, item) => acc + item.price, 0);
};
```

在这里，我们定义了一个名为 `useTotalPrice` 的自定义 Hook，用于计算购物车中商品的总价。它使用 React 的 `useContext` Hook 从 `ShoppingCartContext` 访问购物车数据。然后使用 `reduce` 方法将购物车中所有商品的价格相加，初始值为 `0`。

对于 `ShoppingApplication` 组件，我们可以在刚刚创建的 `ShoppingCartContext` 实例中简单地包裹 `ProductList` 和 `ShoppingCart`：

```js
const ShoppingApplication = () => {
  const context = useContext(ShoppingCartContext);
  const { items, addItem, removeItem } = context;
  const totalPrice = useTotalPrice();
  return (
    <div>
      <ProductList addToCart={addItem} />
      <h2>Shopping Cart</h2>
      <ul>
        {items.map((item) => (
          <li key={item.uniqKey}>
            {item.name} - {item.price}
            <button onClick={() => removeItem(item)}>Remove</button>
          </li>
        ))}
      </ul>
      <p>Total Price: {totalPrice}</p>
    </div>
  );
};
```

`ShoppingApplication` 组件作为购物应用程序的主要界面。它使用 React 的 `useContext` Hook 来访问购物车上下文，该上下文提供购物车中的商品列表（`items`）、添加商品的函数（`addItem`）和删除商品的函数（`removeItem`）。该组件还使用 `useTotalPrice` 自定义 Hook 来计算购物车中商品的总价。

在最外层的 `App` 组件中，我们可以封装 `ShoppingApplication` 组件：

```js
<ShoppingCartProvider>
  <ShoppingApplication />
</ShoppingCartProvider>
```

因此，CQRS 是一种设计模式，它将系统的修改和查询方面分离，以增强可伸缩性、可维护性和简单性。我们通过实现购物车功能来展示这一原则——修改购物车状态的命令（如添加或删除商品）与查询（包括获取商品列表和计算总价）被分离；这种分离通过使用 React 的 Context API 和自定义 Hook 来实现，这些 Hook 有效地隔离了每个责任。这不仅提高了代码的可读性，还使得在未来管理和扩展应用程序变得更加容易。

# 摘要

在本章中，我们探讨了三个关键的设计原则：SRP（单一职责原则）用于创建专注且易于理解的组件，DIP（依赖倒置原则）用于创建模块化和可测试的代码，以及 CQRS（命令查询责任分离）用于命令和查询之间的明确分离，增强了可维护性。这些原则为构建可伸缩且高质量的软件提供了坚实的基础。

在下一章中，我们将更深入地探讨组合原则，以进一步细化我们对 React 应用程序设计的处理方法。
