

# 第十五章：添加动画

动画与其他任何功能一样，也适合测试驱动开发。在本章中，我们将根据用户输入的命令动画化 Logo 乌龟的移动。

Spec Logo 中有两种类型的动画：

+   首先，当乌龟向前移动时。例如，当用户输入`forward 100`作为指令时，乌龟应该以固定速度沿 100 个单位移动。在移动过程中，它将在后面画一条线。

+   其次，当乌龟旋转时。例如，如果用户输入`rotate 90`，那么乌龟应该缓慢旋转，直到它完成四分之一转弯。

本章的大部分内容是关于测试驱动`window.requestAnimationFrame`函数。这是浏览器 API，允许我们在屏幕上动画化视觉元素，例如乌龟的位置或线的长度。这个函数的机制在本章的第三部分*使用 requestAnimationFrame 进行动画*中解释。

手动测试的重要性

在编写动画代码时，自然想要直观地检查我们正在构建的内容。自动测试是不够的。手动测试也很重要，因为动画不是大多数程序员每天都会做的事情。当某事是新的时，通常最好进行大量的手动测试来验证行为，除了你的自动测试之外。

事实上，在准备本章时，我进行了大量的手动测试。这里展示的试验了几种不同的方法。有很多次我打开浏览器输入`forward 100`或`right 90`来直观地验证发生了什么。

本章涵盖了以下主题：

+   设计动画

+   构建动画线条组件

+   使用`requestAnimationFrame`进行动画

+   使用`cancelAnimationFrame`取消动画

+   变化动画行为

我们将要编写的代码与本书中其他部分的代码相比相对复杂，因此我们需要先做一些前期设计。

到本章结束时，你将深入理解如何测试驱动更复杂的浏览器 API 之一。

# 技术要求

本章的代码文件可以在这里找到：

[`github.com/PacktPublishing/Mastering-React-Test-Driven-Development-Second-Edition/tree/main/Chapter15`](https://github.com/PacktPublishing/Mastering-React-Test-Driven-Development-Second-Edition/tree/main/Chapter15)

# 设计动画

在阅读本节时，你可能希望打开`src/Drawing.js`并阅读现有代码，以了解它在做什么。

当前的`Drawing`组件显示了在此点绘图的外观的静态快照。它渲染一组**可缩放矢量图形**（**SVG**）线条来表示乌龟到达此点的路径，以及一个三角形来表示乌龟。

组件使用了两个子组件：

+   `Turtle`组件只显示一次，并在指定位置绘制一个 SVG 三角形

+   `StaticLines` 组件是一组在屏幕上绘制的 SVG 线条，用于表示绘制的命令

我们将添加一个新的 `AnimatedLine` 组件，表示正在动画化的当前线条。当线条完成动画后，它们将移动到 `StaticLines` 集合中。

我们需要做一些工作来将这个静态视图转换为动画表示。

目前，该组件接受一个 `turtle` 属性和一个 `drawCommands` 属性。`turtle` 属性是乌龟的当前位置，前提是所有绘图命令都已经绘制完成。

在我们新的动画绘图中，我们仍然将 `drawCommands` 视为一组要执行的命令。但不是依赖于 `turtle` 属性来告诉我们乌龟的位置，我们将乌龟的 *当前* 位置存储为组件状态。我们将逐条指令通过 `drawCommands` 数组，每次一个指令，并在动画过程中更新乌龟组件状态。一旦所有指令都完成，乌龟组件状态将匹配最初为 `turtle` 属性设置的值。

乌龟始终从 `0,0` 坐标以 `0` 角度开始。

我们需要跟踪哪些命令已经被动画化。我们将创建另一个组件状态变量 `animatingCommandIndex`，以表示当前正在动画化的数组项的索引。

我们从索引 `0` 开始动画。一旦该命令被动画化，我们就将索引增加 `1`，移动到下一个命令，并对其动画化。这个过程会重复进行，直到我们达到数组的末尾。

这种设计意味着用户可以在动画运行时在提示符中输入新的 `drawCommands`。组件将确保在离开的点重新绘制带有动画的图形。

最后，有两种类型的绘图命令：`drawLine` 和 `rotate`。以下是一些将在 `drawCommands` 数组中出现的命令示例：

```js
{
  drawCommand: "drawLine",
  id: 123,
  x1: 100,
  y1: 100,
  x2: 200,
  y2: 100
}
{
  drawCommand: "rotate",
  id: 234,
  previousAngle: 0,
  newAngle: 90
}
```

每种类型的动画都需要不同的处理方式。例如，当乌龟旋转时，`AnimatedLine` 组件将被隐藏。

大概就是这样。我们将遵循以下方法：

+   从构建 `AnimatedLine` 组件开始

+   在 `Drawing` 中创建一个 `useEffect` 钩子，调用 `window.requestAnimationFrame` 函数来动画化 `drawLine` 命令

+   当添加新指令时取消 `drawLine` 命令的动画

+   添加乌龟旋转的动画

让我们从 `AnimatedLine` 组件开始。

# 构建一个动画线条组件

在本节中，我们将创建一个新的 `AnimatedLine` 组件。

此组件本身不包含动画逻辑，而是从动画线条的起点绘制到当前乌龟位置的一条线。因此，它需要两个属性：`commandToAnimate`，这将是之前显示的 `drawLine` 命令结构之一，以及包含位置的 `turtle` 属性。

让我们开始：

1.  创建一个新的文件，`test/AnimatedLine.test.js`，并使用以下导入和 `describe` 块设置初始化它。注意包括 `horizontalLine` 的样本指令定义：

    ```js
    import React from "react";
    import ReactDOM from "react-dom";
    import {
      initializeReactContainer,
      render,
      element,
    } from "./reactTestExtensions";
    import { AnimatedLine } from "../src/AnimatedLine";
    import { horizontalLine } from "./sampleInstructions";
    const turtle = { x: 10, y: 10, angle: 10 };
    describe("AnimatedLine", () => {
      beforeEach(() => {
        initializeReactContainer();
      });
      const renderSvg = (component) =>
        render(<svg>{component}</svg>);
      const line = () => element("line");
    });
    ```

1.  现在添加第一个测试，用于检查线的起始位置：

    ```js
    it("draws a line starting at the x1,y1 co-ordinate of the command being drawn", () => {
      renderSvg(
        <AnimatedLine
          commandToAnimate={horizontalLine}
          turtle={turtle}
        />
      );
      expect(line()).not.toBeNull();
      expect(line().getAttribute("x1")).toEqual(
        horizontalLine.x1.toString()
      );
      expect(line().getAttribute("y1")).toEqual(
        horizontalLine.y1.toString()
      );
    });
    ```

1.  创建一个新的文件，`src/AnimatedLine.js`，并通过以下实现使测试通过：

    ```js
    import React from "react";
    export const AnimatedLine = ({
      commandToAnimate: { x1, y1 }
    }) => (
      <line x1={x1} y1={y1} />
    );
    ```

1.  接下来是下一个测试。在这个测试中，我们明确设置海龟值，以便清楚地看到预期值来自何处：

    ```js
    it("draws a line ending at the current position of the turtle", () => {
      renderSvg(
        <AnimatedLine
          commandToAnimate={horizontalLine}
          turtle={{ x: 10, y: 20 }}
        />
      );
      expect(line().getAttribute("x2")).toEqual("10");
      expect(line().getAttribute("y2")).toEqual("20");
    });
    ```

1.  为了使其通过，我们只需要在线元素上设置 `x2` 和 `y2` 属性，从海龟那里拉取这些值：

    ```js
    export const AnimatedLine = ({
      commandToAnimate: { x1, y1 },
      turtle: { x, y }
    }) => (
      <line x1={x1} y1={y1} x2={x} y2={y} />
    );
    ```

1.  然后我们需要两个测试来设置 `strokeWidth` 和 `stroke` 属性：

    ```js
    it("sets a stroke width of 2", () => {
      renderSvg(
        <AnimatedLine
          commandToAnimate={horizontalLine}
          turtle={turtle}
        />
      );
      expect(
        line().getAttribute("stroke-width")
      ).toEqual("2");
    });
    it("sets a stroke color of black", () => {
      renderSvg(
        <AnimatedLine
          commandToAnimate={horizontalLine}
          turtle={turtle}
        />
      );
      expect(
        line().getAttribute("stroke")
      ).toEqual("black");
    });
    ```

1.  通过添加这两个属性来完成组件：

    ```js
    export const AnimatedLine = ({
      commandToAnimate: { x1, y1 },
      turtle: { x, y }
    }) => (
      <line
        x1={x1}
        y1={y1}
        x2={x}
        y2={y}
        strokeWidth="2"
        stroke="black"
      />
    );
    ```

这就完成了 `AnimatedLine` 组件。

接下来，是时候将其添加到 `Drawing` 中了，通过将 `commandToAnimate` 属性设置为当前正在动画化的线条，并使用 `requestAnimationFrame` 来改变 `turtle` 属性的位置。

# 使用 requestAnimationFrame 进行动画

在本节中，你将结合使用 `useEffect` 钩子和 `window.requestAnimationFrame` 来调整 `AnimatedLine` 和 `Turtle` 的位置。

`window.requestAnimationFrame` 函数用于动画视觉属性。例如，你可以用它在一个给定的时间段内，比如 2 秒内，将一条线的长度从 0 单位增加到 200 单位。

为了使这工作，你提供一个回调，该回调将在下一个重绘间隔运行。当调用时，该回调提供了当前的动画时间：

```js
const myCallback = time => {
  // animating code here
};
window.requestAnimationFrame(myCallback);
```

如果你已知动画的开始时间，你可以计算出已过的动画时间，并使用这个时间来计算动画属性的当前值。

浏览器可以以非常高的刷新率调用你的回调，例如每秒 60 次。因为这些非常小的时间间隔，你的更改看起来像是一个平滑的动画。

注意，浏览器只为每个请求的帧调用一次你的回调。这意味着你有责任重复调用 `requestAnimationFrame` 函数，直到动画时间达到你定义的结束时间，如下例所示。浏览器负责仅在屏幕需要重绘时调用你的回调：

```js
let startTime;
let endTimeMs = 2000;
const myCallback = time => {
  if (startTime === undefined) startTime = time;
  const elapsed = time - startTime;
  // ... modify visual state here ...
  if (elapsed < endTimeMs) {
    window.requestAnimationFrame(myCallback);
  }
};
// kick off the first animation frame
window.requestAnimationFrame(myCallback);
```

随着我们进入本节，你会看到如何使用这个来修改组件状态（例如 `AnimatedLine` 的位置），这会导致你的组件重新渲染。

让我们从 Redux 存储中移除现有的海龟值开始——我们不再使用这个值，而是依赖于从 `drawCommands` 数组中计算出的海龟位置：

1.  打开 `test/Drawing.test.js` 并找到名为 `passes the turtle x, y and angle as props to Turtle` 的测试。用以下内容替换它：

    ```js
    it("initially places the turtle at 0,0 with angle 0", () => {
      renderWithStore(<Drawing />);
      expect(Turtle).toBeRenderedWithProps({
        x: 0,
        y: 0,
        angle: 0
      });
    });
    ```

1.  现在，在 `src/Drawing.js` 文件中，你可以通过替换 `useSelector` 调用，移除从 Redux 存储中提取的海龟值：

    ```js
    const { drawCommands } = useSelector(
      ({ script }) => script
    );
    ```

1.  我们将用一个新的状态变量替换现有的乌龟值。当我们开始移动乌龟的位置时，这将非常有用。首先，将`useState`导入到`src/Drawing.js`中：

    ```js
    import React, { useState } from "react";
    ```

1.  然后，在`useSelector`调用下方添加另一个`useState`调用。在此更改之后，你的测试应该可以通过：

    ```js
    const [turtle, setTurtle] = useState({
      x: 0,
      y: 0,
      angle: 0
    });
    ```

1.  在`test/Drawing.test.js`中，在`describe`块的`beforeEach`中模拟`requestAnimationFrame`函数：

    ```js
    beforeEach(() => {
      ...
      jest
        .spyOn(window, "requestAnimationFrame");
    });
    ```

1.  将以下新的`describe`块和测试添加到现有`describe`块的底部，在现有`describe`块内部（因此它是嵌套的）。它定义了一个初始状态`horizontalLineDrawn`，它只有一条线——这条线在`sampleInstructions`文件中定义。测试表明我们期望在组件挂载时调用`requestAnimationFrame`：

    ```js
    describe("movement animation", () => {
      const horizontalLineDrawn = {
        script: {
          drawCommands: [horizontalLine],
          turtle: { x: 0, y: 0, angle: 0 },
        },
      };
      it("invokes requestAnimationFrame when the timeout fires", () => {
        renderWithStore(<Drawing />, horizontalLineDrawn);
        expect(window.requestAnimationFrame).toBeCalled();
      });
    });
    ```

1.  要使这个测试通过，打开`src/Drawing.js`并首先导入`useEffect`钩子：

    ```js
    import React, { useState, useEffect } from "react";
    ```

1.  然后，将新的`useEffect`钩子添加到`Drawing`组件中。在`return`语句 JSX 上方添加以下三行：

    ```js
    export const Drawing = () => {
      ...
      useEffect(() => {
        requestAnimationFrame();
      }, []);
      return ...
    };
    ```

1.  由于我们现在处于`useEffect`的领域，任何导致组件状态更新的操作都必须在`act`块内发生。这包括任何触发的动画帧，我们即将触发一些。因此，回到`test/Drawing.test.js`，现在添加`act`导入：

    ```js
    import { act } from "react-dom/test-utils";
    ```

1.  我们还需要导入`AnimatedLine`，因为在下一个测试中，我们将断言我们渲染了它。添加以下导入，以及其间谍设置，如下所示：

    ```js
    import { AnimatedLine } from "../src/AnimatedLine";
    jest.mock("../src/AnimatedLine", () => ({
      AnimatedLine: jest.fn(
        () => <div id="AnimatedLine" />
      ),
    }));
    ```

1.  `requestAnimationFrame`的调用需要一个`handler`函数作为参数。然后浏览器将在下一个动画帧期间调用此函数。对于下一个测试，我们将检查当计时器第一次触发时，乌龟是否位于第一条线的起点。我们需要定义一个新的辅助函数来完成这个任务，即`triggerRequestAnimationFrame`。在浏览器环境中，这个调用会自动发生，但在我们的测试中，我们扮演浏览器的角色，并在代码中触发它。正是这个调用必须被`act`函数调用包裹，因为我们的处理程序将导致组件状态改变：

    ```js
    const triggerRequestAnimationFrame = time => {
      act(() => {
        const mock = window.requestAnimationFrame.mock
        const lastCallFirstArg =
          mock.calls[mock.calls.length - 1][0]
        lastCallFirstArg(time);
      });
    };
    ```

1.  现在，我们准备好编写动画周期的测试了。第一个是一个简单的测试：在时间零时，乌龟位置被放置在线的**起点**。如果你检查`test/sampleInstructions.js`中的定义，你会看到`horizontalLine`从位置`100,100`开始：

    ```js
    it("renders an AnimatedLine with turtle at the start position when the animation has run for 0s", () => {
      renderWithStore(<Drawing />, horizontalLineDrawn);
      triggerRequestAnimationFrame(0);
      expect(AnimatedLine).toBeRenderedWithProps({
        commandToAnimate: horizontalLine,
        turtle: { x: 100, y: 100, angle: 0 }
      });
    });
    ```

使用乌龟位置进行动画

记住，`AnimatedLine`组件从`drawLine`指令的起始位置绘制到当前乌龟位置。然后，这个乌龟位置被动画化，这产生了`AnimatedLine`实例长度增长直到找到`drawLine`指令的终点位置的效果：

1.  使这个测试通过将是一个小小的“大爆炸”。首先，按照所示扩展 `useEffect`。我们定义了两个变量，`commandToAnimate` 和 `isDrawingLine`，我们使用它们来确定是否应该进行动画。`isDrawingLine` 测试是必要的，因为一些现有的测试根本不会向组件发送任何绘图命令，在这种情况下 `commandToAnimate` 将是 `null`。另一个测试将一个未知类型的命令传递到组件中，如果我们尝试从中提取 `x1` 和 `y1`，它也会崩溃。这就是为什么需要调用 `isDrawLineCommand` 的原因——这是一个已经在文件顶部定义好的函数：

    ```js
    const commandToAnimate = drawCommands[0];
    const isDrawingLine =
      commandToAnimate &&  
      isDrawLineCommand(commandToAnimate);
    useEffect(() => {
      const handleDrawLineFrame = time => {
        setTurtle(turtle => ({
          ...turtle,
          x: commandToAnimate.x1,
          y: commandToAnimate.y1,
        }));
      };
      if (isDrawingLine) {
        requestAnimationFrame(handleDrawLineFrame);
      }
    }, [commandToAnimate, isDrawingLine]);
    ```

使用函数式更新设置器

这段代码使用了 `setTurtle` 的 *函数式更新* 变体，它接受一个函数而不是一个值。当新的状态值依赖于旧值时，会使用这种形式的设置器。使用这种形式的设置器意味着乌龟不需要在 `useEffect` 的依赖列表中，并且不会导致 `useEffect` 钩子重置自己。

1.  到目前为止，我们还没有渲染 `AnimatedLine`，这正是我们的测试所期望的。现在让我们修复这个问题。首先，添加导入：

    ```js
    import { AnimatedLine } from "./AnimatedLine";
    ```

1.  在 `StaticLines` 的 JSX 下方插入此代码。此时，你的测试应该可以通过：

    ```js
    <AnimatedLine
      commandToAnimate={commandToAnimate}
      turtle={turtle}
    />
    ```

1.  我们需要进一步的测试来确保在没有动画线条时不会渲染 `AnimatedLine`。按照所示添加下一个测试，但不要将其添加到 `movement animation` 块中；相反，将其放入父上下文中：

    ```js
    it("does not render AnimatedLine when not moving", () => {
      renderWithStore(<Drawing />, {
        script: { drawCommands: [] }
      });
      expect(AnimatedLine).not.toBeRendered();
    });
    ```

1.  通过将 `AnimatedLine` 组件用三元运算符包裹来实现这个过渡。如果 `isDrawingLine` 为假，我们简单地返回 `null`：

    ```js
    {isDrawingLine ? (
      <AnimatedLine
        commandToAnimate={commandToAnimate}
        turtle={turtle}
    /> : null}
    ```

1.  我们已经处理了 *第一个* 动画帧应该做什么；现在让我们编写 *下一个* 动画帧的代码。在下面的测试中，有 *两个* 调用 `triggerRequestAnimationFrame`。第一个用于表示动画已经开始；第二个允许我们移动。我们需要第一个调用（时间索引为 `0`）来标记动画开始的时间：

    ```js
    it("renders an AnimatedLine with turtle at a position based on a speed of 5px per ms", () => {
      renderWithStore(<Drawing />, horizontalLineDrawn);
      triggerRequestAnimationFrame(0);
      triggerRequestAnimationFrame(250);
      expect(AnimatedLine).toBeRenderedWithProps({
        commandToAnimate: horizontalLine,
        turtle: { x: 150, y: 100, angle: 0 }
      });
    });
    ```

使用动画持续时间来计算移动的距离

当浏览器调用 `handleDrawLineFrame` 函数时，会传递一个时间参数。这是动画的当前持续时间。乌龟以恒定的速度移动，因此知道持续时间可以让我们计算出乌龟的位置。

1.  为了实现这个过渡，首先，我们需要定义几个函数。滚动到 `src/Drawing.js` 的顶部，直到你看到 `isDrawLineCommand` 的定义，然后在那里添加这两个新的定义。`distance` 和 `movementSpeed` 函数用于计算动画的持续时间：

    ```js
    const distance = ({ x1, y1, x2, y2 }) =>
      Math.sqrt(
        (x2 - x1) * (x2 - x1) + (y2 - y1) * (y2 - y1)
      );
    const movementSpeed = 5;
    ```

1.  现在我们可以计算动画的持续时间；按照所示修改 `useEffect`：

    ```js
    useEffect(() => {
      let duration;
      const handleDrawLineFrame = time => {
        setTurtle(...);
      };
      if (isDrawingLine) {
        duration =
          movementSpeed * distance(commandToAnimate);
        requestAnimationFrame(handleDrawLineFrame);
      }
    }, [commandToAnimate, isDrawingLine]);
    ```

1.  通过将 `duration` 声明为 `useEffect` 块中的第一行，该变量在 `requestAnimationFrame` 处理程序的作用域内，以便读取它来计算距离。为此，我们取经过的时间并将其除以总持续时间：

    ```js
    useEffect(() => {
      let duration;
      const handleDrawLineFrame = time => {
        const { x1, x2, y1, y2 } = commandToAnimate;
        setTurtle(turtle => ({
          ...turtle,
          x: x1 + ((x2 - x1) * (time / duration)),
          y: y1 + ((y2 - y1) * (time / duration)),
        }));
      };
      if (isDrawingLine) {
        ...
      }
    }, [commandToAnimate, isDrawingLine]);
    ```

1.  我们取得了很大的进展！在之前的测试中，我们假设起始时间是`0`，但实际上，浏览器可以给我们任何时间作为起始时间（它给出的时间被称为**时间原点**）。因此，让我们确保我们的计算对于非零起始时间也是有效的。添加以下测试：

    ```js
    it("calculates move distance with a non-zero animation start time", () => {
      const startTime = 12345;
      renderWithStore(<Drawing />, horizontalLineDrawn);
      triggerRequestAnimationFrame(startTime);
      triggerRequestAnimationFrame(startTime + 250);
      expect(AnimatedLine).toBeRenderedWithProps({
        commandToAnimate: horizontalLine,
        turtle: { x: 150, y: 100, angle: 0 }
      });
    });
    ```

1.  通过引入`start`和`elapsed`时间，实现这个过渡，如下所示：

    ```js
    useEffect(() => {
      let start, duration;
      const handleDrawLineFrame = time => {
        if (start === undefined) start = time;
        const elapsed = time - start;
        const { x1, x2, y1, y2 } = commandToAnimate;
        setTurtle(turtle => ({
          ...turtle,
          x: x1 + ((x2 - x1) * (elapsed / duration)),
          y: y1 + ((y2 - y1) * (elapsed / duration)),
        }));
      };
      if (isDrawingLine) {
        ...
      }
    }, [commandToAnimate, isDrawingLine]);
    ```

1.  我们需要确保我们的组件在达到持续时间之前重复调用`requestAnimationFrame`。到那时，线条应该已经完全绘制。在这个测试中，我们触发了三个动画帧，并期望`requestAnimationFrame`被调用了三次：

    ```js
    it("invokes requestAnimationFrame repeatedly until the duration is reached", () => {
      renderWithStore(<Drawing />, horizontalLineDrawn);
      triggerRequestAnimationFrame(0);
      triggerRequestAnimationFrame(250);
      triggerRequestAnimationFrame(500);
      expect(
        window.requestAnimationFrame.mock.calls 
      ).toHaveLength(3);
    });
    ```

1.  为了实现这个过渡，我们需要确保`handleDrawLineFrame`在运行时触发另一个`requestAnimationFrame`。然而，我们只应该在持续时间到达之前这样做。通过以下条件将`setTurtle`和`requestAnimationFrame`调用包装起来，以实现这个过渡：

    ```js
    const handleDrawLineFrame = (time) => {
      if (start === undefined) start = time;
      if (time < start + duration) {
        const elapsed = time - start;
        const { x1, x2, y1, y2 } = commandToAnimate;
        setTurtle(...);
        requestAnimationFrame(handleDrawLineFrame);
      }
    };
    ```

1.  对于下一个测试，我们将检查当一条线“完成”绘制后，如果还有下一条线，我们将继续绘制下一条（如果没有，则停止）。在刚刚实现的`describe`块下方添加一个新的`describe`块，并添加以下测试。第二个时间戳`500`是在`horizontalLine`绘制所需的时间之后，因此`AnimatedLine`应该显示`verticalLine`：

    ```js
    describe("after animation", () => {
      it("animates the next command", () => {
        renderWithStore(<Drawing />, {
          script: {
            drawCommands: [horizontalLine, verticalLine]
          }
        });
        triggerRequestAnimationFrame(0);
        triggerRequestAnimationFrame(500);
        expect(AnimatedLine).toBeRenderedWithProps(
          expect.objectContaining({
            commandToAnimate: verticalLine,
          })
        );
      });
    });
    ```

1.  为了实现这个过渡，我们需要引入一个指向当前正在动画化的命令的指针。这个指针将从`0`索引开始，每次动画完成后都会递增。在组件顶部添加以下新的状态变量：

    ```js
    const [
      animatingCommandIndex,
      setAnimatingCommandIndex
    ] = useState(0);
    ```

1.  将`commandToAnimate`常量更新为使用这个新变量：

    ```js
    const commandToAnimate = 
      drawCommands[animatingCommandIndex];
    ```

1.  在`handleDrawLineFrame`中的条件语句中添加一个`else`子句来增加值：

    ```js
    if (time < start + duration) {
      ...
    } else {
      setAnimatingCommandIndex(
        animatingCommandIndex => animatingCommandIndex + 1
      );
    }
    ```

1.  对于最后的测试，我们想要确保只有之前已经动画化的命令被发送到`StaticLines`。当前正在动画化的线条将由`AnimatedLine`渲染，而尚未动画化的线条根本不应被渲染：

    ```js
    it("places line in StaticLines", () => {
      renderWithStore(<Drawing />, {
        script: {
          drawCommands: [horizontalLine, verticalLine]
        }
      });
      triggerRequestAnimationFrame(0);
      triggerRequestAnimationFrame(500);
      expect(StaticLines).toBeRenderedWithProps({ 
        lineCommands: [horizontalLine]
      });
    });
    ```

1.  为了实现这个过渡，将`lineCommands`更新为只包含`drawCommands`中直到当前`animatingCommandIndex`值的部分：

    ```js
    const lineCommands = drawCommands
      .slice(0, animatingCommandIndex)
      .filter(isDrawLineCommand);
    ```

1.  虽然最新的测试现在会通过，但现有的测试`sends only line commands to StaticLines`现在会失败。由于我们最新的测试覆盖了基本相同的功能，你现在可以安全地删除那个测试了。

如果你运行应用程序，你现在将能够看到线条在屏幕上被动画化。

在下一节中，我们将确保当用户同时输入多个命令时，动画表现良好。

# 使用`cancelAnimationFrame`取消动画

我们编写的 `useEffect` 钩子在其依赖列表中有 `commandToAnimate` 和 `isDrawingLine`。这意味着当这两个值中的任何一个更新时，`useEffect` 钩子将被销毁并重新启动。但还有其他情况下我们想要取消动画。其中一种情况是当用户重置他们的屏幕时。

如果用户点击 **重置** 按钮时，命令正在动画化，我们不想让当前的动画帧继续。我们想要清理它。

现在让我们为这个功能添加一个测试：

1.  在 `test/Drawing.test.js` 的底部添加以下测试：

    ```js
    it("calls cancelAnimationFrame on reset", () => {
      renderWithStore(<Drawing />, {
        script: { drawCommands: [horizontalLine] }
      });
      renderWithStore(<Drawing />, {
        script: { drawCommands: [] }
      });
      expect(window.cancelAnimationFrame).toBeCalledWith(
        cancelToken
      );
    });
    ```

1.  你还需要更改 `beforeEach` 块，使 `requestAnimationFrame` 模拟返回一个虚拟的取消令牌，并为 `cancelAnimationFrame` 函数添加一个新的模拟：

    ```js
    describe("Drawing", () => {
      const cancelToken = "cancelToken";
      beforeEach(() => {
        ...
        jest
          .spyOn(window, "requestAnimationFrame")
          .mockReturnValue(cancelToken);
        jest.spyOn(window, "cancelAnimationFrame");
      });
    });
    ```

1.  为了使测试通过，更新 `useEffect` 钩子以存储 `requestAnimationFrame` 函数在调用时返回的 `cancelToken` 值。然后从 `useEffect` 钩子返回一个清理函数，该函数使用该令牌取消下一个请求的帧。这个函数将在 React 销毁钩子时被调用：

    ```js
    useEffect(() => {
      let start, duration, cancelToken;
      const handleDrawLineFrame = time => {
        if (start === undefined) start = time;
        if (time < start + duration) {
          ...
          cancelToken = requestAnimationFrame(
            handleDrawLineFrame
          );
        } else {
          ...
        }
      };
      if (isDrawingLine) {
        duration =
          movementSpeed * distance(commandToAnimate);
        cancelToken = requestAnimationFrame(
          handleDrawLineFrame
        );
      }
      return () => {
        cancelAnimationFrame(cancelToken);
      }
    });
    ```

1.  最后，我们不想在没有设置 `cancelToken` 的情况下运行这个清理。如果当前没有绘制线条，则不会设置令牌。我们可以通过以下测试来证明这一点，你应该现在添加它：

    ```js
    it("does not call cancelAnimationFrame if no line animating", () => {
      jest.spyOn(window, "cancelAnimationFrame");
      renderWithStore(<Drawing />, {
        script: { drawCommands: [] }
      });
      renderWithStore(<React.Fragment />);
      expect(
        window.cancelAnimationFrame
      ).not.toHaveBeenCalled();
    });
    ```

卸载组件

这个测试展示了如何在 React 中模拟组件的卸载，这仅仅是通过在测试组件的位置渲染 `<React.Fragment />` 来实现的。当发生这种情况时，React 将卸载你的组件。

1.  为了使测试通过，只需将返回的清理函数包裹在一个条件语句中：

    ```js
    return () => {
      if (cancelToken) {
        cancelAnimationFrame(cancelToken);
      }
    };
    ```

那就是我们为动画化 `drawLine` 命令需要做的所有事情。接下来是旋转海龟。

# 变化动画行为

我们现在可以看到线条和海龟正在很好地动画化。然而，我们仍然需要处理第二种类型的绘制命令：旋转。当海龟旋转到新的角度时，它将以恒定的速度移动。一个完整的旋转应该需要 1 秒来完成，我们可以用这个来计算旋转的持续时间。例如，四分之一旋转将需要 0.25 秒来完成。

在最后一节中，我们从一个测试开始，检查我们是否调用了 `requestAnimationFrame`。这次，这个测试不是必需的，因为我们已经通过绘制线条证明了相同的设计。我们可以直接进入更复杂的测试，使用之前相同的 `triggerRequestAnimationFrame` 辅助函数。

让我们更新 `Drawing` 以使海龟坐标动画化：

1.  在 `Drawing` 的 `describe` 块底部添加以下测试。在另一个嵌套的 `describe` 块中创建它，位于你刚刚编写的最后一个测试下面。这个测试遵循我们绘制线条测试的相同原则：我们触发两个动画帧，一个在 `0` 毫秒，一个在 `500` 毫秒，然后期望旋转发生。除了 *角度* 之外，还测试了 *x* 和 *y* 坐标；这是为了确保我们继续传递这些值：

    ```js
    describe("rotation animation", () => {
      const rotationPerformed = {
        script: { drawCommands: [rotate90] },
      };
      it("rotates the turtle", () => {
        renderWithStore(<Drawing />, rotationPerformed);
        triggerRequestAnimationFrame(0);
        triggerRequestAnimationFrame(500);
        expect(Turtle).toBeRenderedWithProps({
          x: 0,
          y: 0,
          angle: 90
        });
      });
    });
    ```

1.  移动到`src/Drawing.js`，首先在`isDrawLineCommand`定义下方添加`isRotateCommand`的定义：

    ```js
    const isRotateCommand = command =>
      command.drawCommand === "rotate";
    ```

1.  在`Drawing`组件中，在`isDrawingLine`定义下方添加一个新的常量，`isRotating`：

    ```js
    const isRotating =
      commandToAnimate &&
        isRotateCommand(commandToAnimate);
    ```

1.  在`useEffect`钩子中，在`handleDrawLineFrame`定义下方定义一个新的旋转处理器，`handleRotationFrame`。为了这个测试的目的，它不需要做太多，只需将角度设置为新的值：

    ```js
    const handleRotationFrame = time => {
      setTurtle(turtle => ({
        ...turtle,
        angle: commandToAnimate.newAngle
      }));
    };
    ```

1.  我们可以利用这个来在旋转命令动画时调用`requestAnimationFrame`。修改`useEffect`钩子的最后部分，使其看起来如下，确保你将`isRotating`添加到依赖列表中。更改后测试应该通过：

    ```js
    useEffect(() => {
      ...
      if (isDrawingLine) {
        ...
      } else if (isRotating) {
        requestAnimationFrame(handleRotationFrame);
      }
    }, [commandToAnimate, isDrawingLine, isRotating]);
    ```

1.  让我们添加一个测试来获取持续时间并在我们的计算中使用它。这基本上与上一个测试相同，但具有不同的持续时间，因此预期的旋转也不同：

    ```js
    it("rotates part-way at a speed of 1s per 180 degrees", () => {
      renderWithStore(<Drawing />, rotationPerformed);
      triggerRequestAnimationFrame(0);
      triggerRequestAnimationFrame(250);
      expect(Turtle).toBeRenderedWithProps({
        x: 0,
        y: 0,
        angle: 45
      });
    });
    ```

1.  为了使这个通过，首先，我们需要定义`rotateSpeed`。你可以在`movementSpeed`定义下方添加这个定义：

    ```js
    const rotateSpeed = 1000 / 180;
    ```

1.  接下来，更新`useEffect`处理器底部的条件，以计算`rotate`命令的持续时间：

    ```js
    } else if (isRotating) {
      duration =
        rotateSpeed *
        Math.abs(
          commandToAnimate.newAngle -
            commandToAnimate.previousAngle
        );
     requestAnimationFrame(handleRotationFrame);
    }
    ```

1.  更新`handleRotationFrame`以使用持续时间来计算一个成比例的角度来移动：

    ```js
    const handleRotationFrame = (time) => {
      const {
        previousAngle, newAngle
      } = commandToAnimate;
      setTurtle(turtle => ({
        ...turtle,
        angle:
          previousAngle +
          (newAngle - previousAngle) * (time / duration)
      }));
    };
    ```

1.  就像`handleDrawLineFrame`一样，我们需要确保我们可以处理除`0`之外的其他起始时间。添加以下测试：

    ```js
    it("calculates rotation with a non-zero animation start time", () => {
      const startTime = 12345;
      renderWithStore(<Drawing />, rotationPerformed);
      triggerRequestAnimationFrame(startTime);
      triggerRequestAnimationFrame(startTime + 250);
      expect(Turtle).toBeRenderedWithProps({
        x: 0,
        y: 0,
        angle: 45
      });
    });
    ```

1.  通过添加`start`和`elapsed`变量来使那个通过。之后，测试应该通过。你会注意到`handleDrawLineFrame`和`handleRotationFrame`之间的相似性：

    ```js
    const handleRotationFrame = (time) => {
      if (start === undefined) start = time;
      const elapsed = time - start;
      const {
       previousAngle, newAngle
      } = commandToAnimate;
      setTurtle(turtle => ({
        ...turtle,
        angle:
          previousAngle +
          (newAngle - previousAngle) *
          (elapsed / duration)
      }));
    };
    ```

1.  添加一个测试以确保我们反复调用`requestAnimationFrame`。这个测试与用于`drawLine`处理器的测试相同，但现在我们传递的是`rotate90`命令。请确保测试属于嵌套上下文，这样你可以确保没有名称冲突：

    ```js
    it("invokes requestAnimationFrame repeatedly until the duration is reached", () => {
      renderWithStore(<Drawing />, rotationPerformed);
      triggerRequestAnimationFrame(0);
      triggerRequestAnimationFrame(250);
      triggerRequestAnimationFrame(500);
      expect(
        window.requestAnimationFrame.mock.calls 
      ).toHaveLength(3);
    });
    ```

1.  为了使这个通过，我们需要做几件事情。首先，我们需要像修改`handleDrawLineFrame`一样修改`handleRotationFrame`，通过添加一个条件，在持续时间到达后停止动画。其次，我们还需要填写条件的第二部分，以设置动画完成后乌龟的位置：

    ```js
    const handleRotationFrame = (time) => {
      if (start === undefined) start = time;
      if (time < start + duration) {
        ...
      } else {
        setTurtle(turtle => ({
          ...turtle,
          angle: commandToAnimate.newAngle
        }));
      }
    };
    ```

处理结束动画状态

这个`else`子句在`drawLine`处理器中不是必要的，因为一旦线条动画完成，它将被传递到`StaticLines`，渲染所有线条的全长。但这与旋转角度不同：它保持固定，直到下一次旋转。因此，我们需要确保它处于正确的最终值。

1.  我们还有一个最后的测试。一旦动画完成，我们需要增加当前动画命令。与上一节中的相同测试一样，这个测试应该位于我们刚刚使用的`describe`块之外，因为它有不同的测试设置：

    ```js
    it("animates the next command once rotation is complete", async () => {
      renderWithStore(<Drawing />, {
        script: {
          drawCommands: [rotate90, horizontalLine]
        }
      });
      triggerRequestAnimationFrame(0);
      triggerRequestAnimationFrame(500);
      triggerRequestAnimationFrame(0);
      triggerRequestAnimationFrame(250);
      expect(Turtle).toBeRenderedWithProps({
        x: 150,
        y: 100,
        angle: 90
      });
    });
    ```

1.  为了使那个通过，将`setNextCommandToAnimate`的调用添加到`else`条件中：

    ```js
    } else {
      setTurtle(turtle => ({
        ...turtle,
        angle: commandToAnimate.newAngle
      }));
      setAnimatingCommandIndex(
        (animatingCommandToIndex) =>
          animatingCommandToIndex + 1
      );
    }
    ```

就这样！如果你还没有这样做，运行应用尝试一下是值得的。

# 摘要

在本章中，我们探讨了如何测试`requestAnimationFrame`浏览器 API。这不是一个简单的过程，如果你希望完全覆盖，需要编写多个测试。

尽管如此，你已经看到为屏幕上的动画编写自动化测试是完全可能的。这样做的好处是，复杂的生产代码通过测试得到了完全的文档记录。

在下一章中，我们将探讨如何将 WebSocket 通信添加到 Spec Logo 中。

# 练习

1.  更新`Drawing`，以便当用户使用**重置**按钮清除屏幕时，重置海龟位置。

1.  我们的测试有很多重复，因为重复调用`triggerRequestAnimationFrame`。通过创建一个名为`triggerAnimationSequence`的包装函数来简化调用方式，该函数接受一个帧时间数组，并为这些时间中的每一个调用`triggerRequestAnimationFrame`。

1.  加载现有脚本（例如，在启动时）将花费很长时间来动画化所有指令，粘贴代码片段也是如此。添加一个**跳过动画**按钮，可以用来跳过所有排队的动画。

1.  确保在动画进行时**撤销**按钮能正确工作。
