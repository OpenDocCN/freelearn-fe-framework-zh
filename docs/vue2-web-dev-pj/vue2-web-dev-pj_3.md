# 第三章：项目 2 - 城堡决斗浏览器游戏

在本章中，我们将创建一个完全不同的应用程序--一个浏览器游戏。它将由两名玩家组成，每个玩家指挥一座令人印象深刻的城堡，并试图通过行动卡将对手的食物或伤害水平降低到零来摧毁对方。

在这个项目和接下来的项目中，我们将把我们的应用程序分成可重用的组件。这是框架的核心，其所有 API 都是围绕这个想法构建的。我们将看到如何定义和使用组件，以及如何使它们相互通信。结果将是我们应用程序的更好结构。

# 游戏规则

以下是我们将在游戏中实施的规则：

+   两名玩家轮流进行游戏

+   每个玩家游戏开始时拥有 10 点健康值，10 点食物和 5 张手牌

+   玩家的健康和食物值不能超过 10 点。

+   当玩家的食物或健康值达到零时，玩家将失败。

+   两名玩家都可以在平局中失败。

+   在一个玩家的回合中，每个玩家唯一可能的行动是打出一张卡牌，然后将其放入弃牌堆

+   每个玩家在回合开始时从抽牌堆中抽一张牌（除了他们的第一回合）

+   由于前两条规则，每个玩家在开始他们的回合时手中正好有五张牌

+   如果玩家抽牌时抽牌堆为空，则将弃牌堆重新填满抽牌堆

+   卡片可以修改玩家或对手的健康和食物值

+   有些卡片还可以让玩家跳过他们的回合。

游戏玩法建立在玩家每回合只能打出一张卡牌，并且大多数卡牌会对他们产生负面影响（最常见的是失去食物）。你必须在出牌前考虑好你的策略。

应用程序将由两层组成--世界，游戏对象（如风景和城堡）在其中绘制，以及用户界面。

世界将有两座城堡彼此对峙，一个地面和一个天空，有多个动画云；每座城堡将有两面旗帜--绿色的代表玩家的食物，红色的代表玩家的健康--并显示剩余食物或健康值的气泡：

![](img/66e1a50b-ee36-4040-998f-f14783618466.png)

UI 界面顶部将有一个条形菜单，显示回合计数器和两名玩家的姓名。屏幕底部，手牌将显示当前玩家的卡牌。

![](img/c23e599c-d371-4475-9db2-e39f306bedab.png)

除此之外，还会定期显示一些叠加层，隐藏手牌。其中一个将显示接下来轮到的玩家的名字：

！[](assets/f8d92822-cde6-4725-ae5b-9b2889da3c15.png)

接下来将是另一个叠加层，显示对手上一轮打出的牌。这将允许游戏在同一屏幕上进行（例如，平板电脑）。

！[](assets/0f3ed8f3-ae57-404b-863a-bd3f274f1e13.png）

第三个叠加层只有在游戏结束时才会显示，显示玩家是赢了还是输了。单击此叠加层将重新加载页面，允许玩家开始新游戏。

！[](assets/1744d0e2-78e2-44a2-8ad8-2a6ab7656f5c.png)

# 设置项目

下载`第二章`文件并将项目设置提取到一个空文件夹中。您应该有以下内容：

+   `index.html`：网页

+   `style.css`：CSS 文件

+   `svg`：包含游戏的所有 SVG 图像

+   `cards.js`：所有卡片数据都已准备好使用

+   `state.js`：我们将在这里整合游戏的主要数据属性

+   `utils.js`：我们将编写有用的函数的地方

+   `banner-template.svg`：我们稍后将使用此文件的内容

我们将从我们的主 JavaScript 文件开始--创建一个名为`main.js`的新文件。

打开`index.html`文件，并在`state.js`之后添加一个引用新文件的新脚本标记：

```js
<!-- Scripts -->
<script src="img/utils.js"></script>
<script src="img/cards.js"></script>
<script src="img/state.js"></script>
<script src="img/main.js"></script>
```

让我们在`main.js`文件中创建我们应用程序的主要实例：

```js
new Vue({
  name: 'game',
  el: '#app',
})
```

我们现在已经准备好了！

# 风平浪静

在这一部分，我们将介绍一些新的 Vue 功能，这些功能将帮助我们构建游戏，比如组件、props 和事件发射！

# 模板选项

如果您查看`index.html`文件，您会看到`＃app`元素已经存在且为空。实际上，我们不会在里面写任何东西。相反，我们将直接在定义对象上使用模板选项。让我们尝试一个愚蠢的模板：

```js
new Vue({
  name: 'game',
  el: '#app',

  template: `<div id="#app">
    Hello world!
  </div>`,
})
```

在这里，我们使用了新的 JavaScript 字符串，带有`` ` ``字符（反引号）。它允许我们编写跨越多行的文本，而不必编写冗长的字符串连接。

现在，如果你打开应用程序，你应该看到`'Hello world!'`文本显示出来。正如你猜到的那样，从现在开始我们不会将模板内联到`#app`元素中。

# 应用程序状态

正如之前解释的那样，`state.js` 文件将帮助我们将应用程序的主要数据整合到一个地方。这样，我们将能更容易地编写游戏逻辑函数，而不会用大量方法污染定义对象。

1.  `state.js` 文件声明了我们将用作应用程序数据的数据变量。我们可以直接将其用作数据选项，如下所示：

```js
      new Vue({
        // …
        data: state,
      })
```

现在，如果你打开开发工具，你应该看到状态对象中已经声明的唯一数据属性：

![](img/06ba9d2b-5ad4-4ed0-ae51-c1774600dde0.png)

世界比例是一个表示我们应该如何缩放游戏对象以适应窗口的数字。例如，`.6`表示世界应该以其原始大小的 60%进行缩放。它是在`utils.js`文件中使用`getWorldRatio`函数计算的。

有一件事情还缺少 - 当窗口大小调整时它不会被重新计算。这是我们必须自己实现的。在 Vue 实例构造函数之后，添加一个事件监听器到窗口对象，以便在窗口大小调整时检测。

1.  在处理程序内，更新状态的`worldRatio`数据属性。你也可以在模板中显示`worldRatio`：

```js
      new Vue({
        name: 'game',
        el: '#app',

        data: state,

        template: `<div id="#app">
          {{ worldRatio }}
        </div>`,
      })

      // Window resize handling
      window.addEventListener('resize', () => {
        state.worldRatio = getWorldRatio()
      })
```

尝试水平调整浏览器窗口大小 - `worldRatio` 数据属性在 Vue 应用中被更新。

*等等！我们正在修改状态对象，而不是 Vue 实例...*

你是对的！然而，我们使用`state`对象设置了 Vue 实例`数据`属性。这意味着 Vue 已经在其上设置了响应性，并且我们可以改变它的属性来更新我们的应用程序，正如我们将在下面看到的那样。

1.  为了确保`state`是应用的反应性数据，请尝试比较实例数据对象和全局状态对象：

```js
      new Vue({
        // ...
        mounted () {
          console.log(this.$data === state)
        },
      })
```

这些是我们使用数据选项设置的相同对象。所以当你这样做时：

```js
this.worldRatio = 42
```

你也在做这个：

```js
this.$data.worldRatio = 42
```

这实际上和以下一样：

```js
state.worldRatio = 42
```

这将在游戏功能中非常有用，该功能将使用状态对象来更新游戏数据。

# 全能的组件

组件是构成我们应用程序的构建块 - 这是 Vue 应用程序的核心概念。它们是视图的小部分，应该相对小，可重用，并且尽可能自包含 - 使用组件来构建应用程序将有助于维护和发展它，特别是当应用程序变得庞大时。事实上，这已经成为以高效和可管理的方式创建庞大 Web 应用程序的标准方法。

具体而言，你的应用程序将是一个由小组件组成的巨大树：

![](img/b20410bf-c9fc-47ea-8474-f65537e325f3.png)

例如，你的应用程序可能有一个表单组件，其中可以包含多个输入组件和按钮组件。每个组件都是 UI 的一个非常具体的部分，并且它们可以在整个应用程序中重复使用。作用域非常小，它们很容易理解和推理，因此更容易维护（修复问题）或发展。

# 构建用户界面

我们将创建的第一个组件是 UI 层的一部分。它将包括一个带有玩家姓名和回合计数器的顶端栏、带有名称和描述的卡片、当前玩家卡片的手牌和三个叠加层。

# 我们的第一个组件 - 顶端栏

顶端栏，我们的第一个组件，将被放置在页面的顶部，并且在中间显示两个玩家的姓名和回合计数器。它还将显示一个箭头指向当前正在进行回合的玩家的姓名。

它将如下所示：

![](img/8739ced8-2ec7-4a72-af0f-fa01f814c080.png)

# 在状态中添加一些游戏数据

在创建组件之前，我们需要一些新的数据属性：

+   `turn`: 当前回合数；从 1 开始

+   `players`: 玩家对象的数组

+   `currentPlayerIndex`: `players` 数组中当前玩家的索引

在 `state.js` 文件中将它们添加到状态中：

```js
// The consolidated state of our app
var state = {
  // World
  worldRatio: getWorldRatio(),
  // Game
  turn: 1,
  players: [
    {
      name: 'Anne of Cleves',
    },
    {
      name: 'William the Bald',
    },
  ],
  currentPlayerIndex: Math.round(Math.random()),
}
```

`Math.round(Math.random())` 将使用随机选择 `0` 或 `1` 来确定谁先行。

我们将使用这些属性来在顶端栏中显示玩家姓名和回合计数器。

# 定义和使用组件

我们将在一个新的文件中编写我们的 UI 组件：

1.  创建一个 `components` 文件夹并在其中创建一个新的 `ui.js` 文件。在主 `index.html` 页面中引入它，就在主要脚本之前：

```js
      <!-- Scripts -->
      <script src="img/utils.js"></script>
      <script src="img/cards.js"></script>
      <script src="img/state.js"></script>
      <script src="img/ui.js"></script>
      <script src="img/main.js"></script>
```

在这个文件中，我们将注册我们的组件，所以主要的 Vue 实例创建在后面而不是前面，否则，我们将得到组件不存在的错误。

要注册一个组件，我们可以使用全局的 `Vue.component()` 函数。它接受两个参数；我们注册组件的名称，以及它的定义对象，该对象使用了我们已经了解的 Vue 实例的完全相同的选项。

1.  让我们在 `ui.js` 文件中创建`top-bar`组件：

```js
 Vue.component('top-bar', {
        template: `<div class="top-bar">
          Top bar
        </div>`,
      })
```

现在，我们可以在模板中使用 `top-bar` 组件，就像使用任何其他 HTML 标签一样，例如 `<top-bar>`。

1.  在主模板中，添加一个新的 `top-bar` 标签：

```js
      new Vue({
        // ...
        template: `<div id="#app">
          <top-bar/>
        </div>`,
      })
```

这个模板将创建一个新的 `top-bar` 组件，并使用我们刚刚定义的定义对象在 `#app` 元素内呈现它。如果你打开开发工具，你应该会看到两个条目：

![](img/8ea8c881-2a6b-4eb0-be9e-4c711323bb9c.png)

每个都是一个 Vue 实例--Vue 实际上使用我们为顶端栏组件提供的定义创建了第二个实例。

# 使用 props 进行从父组件到子组件的通信

正如我们在强大的组件部分中所见，我们基于组件的应用程序将具有一系列组件，并且我们需要它们相互通信。目前，我们只关注下行、从父级到子级的通信。这通过"props"完成。

我们的`top-bar`组件需要知道玩家是谁，当前正在玩谁，以及当前回合数是多少。因此，我们将需要三个 props--`players`、`currentPlayerIndex`和`turn`。

要向组件定义添加 props，请使用`props`选项。目前，我们只会简单列出我们的 props 的名称。但是，你应该知道还有一种更详细的符号，使用对象代替，我们将在接下来的章节中介绍。

1.  让我们将 props 添加到我们的组件中：

```js
      Vue.component('top-bar', {
        // ...
        props: ['players', 'currentPlayerIndex', 'turn'],
      })
```

在父组件中，即根应用程序中，我们可以以与 HTML 属性相同的方式设置 props 值。

1.  继续使用`v-bind`简写将 props 值与主模板中的应用程序数据进行连接：

```js
      <top-bar :turn="turn" :current-player-index="currentPlayerIndex"         
      :players="players" />
```

请注意，由于 HTML 不区分大小写并且按照惯例，建议在 JavaScript 代码中使用连字符的 kebab-case（带有短横线）名称和 props 的骆驼式命名。

现在，我们可以像数据属性一样在我们的`top-bar`组件中使用 props。例如，你可以这样写：

```js
Vue.component('top-bar', {
  // ...
  created () {
    console.log(this.players)
  },
})
```

这将在浏览器控制台中打印由父组件（我们的应用程序）发送的`players`数组。

# 我们模板中的 props

现在，我们将在`top-bar`组件的模板中使用我们创建的 props。

1.  更改`top-bar`模板以使用`players` prop 显示玩家的名称：

```js
      template: `<div class="top-bar">
        <div class="player p0">{{ players[0].name }}</div>
        <div class="player p1">{{ players[1].name }}</div>
      </div>`,
```

正如你在上述代码中所看到的，我们也像在模板中使用属性一样使用 props。你应该在应用程序中看到玩家名称显示。

1.  继续使用`turn` prop 在`players`之间显示回合计数器：

```js
      template: `<div class="top-bar">
        <div class="player p0">{{ players[0].name }}</div>
        <div class="turn-counter">
        <div class="turn">Turn {{ turn }}</div>
        </div>
        <div class="player p1">{{ players[1].name }}</div>
        </div>`,
```

除了标签外，我们还希望显示一个面向当前玩家的大箭头，以使其更加明显。

1.  在`.turn-counter`元素内添加箭头图像，并使用我们在第二章*Markdown 笔记本*中使用的`v-bind`简写为`currentPlayerIndex` prop 添加动态类：

```js
      template: `<div class="top-bar" :class="'player-' + 
 currentPlayerIndex">
        <div class="player p0">{{ players[0].name }}</div>
        <div class="turn-counter">
          <img class="arrow" src="img/turn.svg" />
          <div class="turn">Turn {{ turn }}</div>
        </div>
        <div class="player p1">{{ players[1].name }}</div>
      </div>`,
```

现在，应用程序应该显示具有两个玩家、名称和它们之间的回合计数器的完整功能顶栏。你可以通过在浏览器控制台中输入这些命令来测试 Vue 自动反应性：

```js
state.currentPlayerIndex = 1
state.currentPlayerIndex = 0
```

你应该看到箭头转向正确的玩家名称，这将被强调：

![](img/a667ef7b-5325-4e1a-bf9f-4555b64d5b38.png)

# 显示一张卡片

所有的卡片都在`cards.js`文件中声明的卡片定义对象中描述。你可以打开它，但你不应该修改其内容。每个卡片定义都具有以下字段：

+   `id`：每张卡片的唯一标识符

+   `type`：更改颜色背景以帮助区分卡片

+   `title`：卡片的显示名称

+   `description`：解释卡片功能的 HTML 文本

+   `note`：一个可选的 HTML 风格文本

+   `play`：当卡片被玩时我们将调用的函数

我们需要一个新组件来显示任何卡片，无论是在玩家手中还是在覆盖层中，描述对手上一轮玩的是什么牌。它将如下所示：

![](img/bdcb0ac6-585b-4c0f-a2e3-fdbe7c28385b.png)

1.  在 `components/ui.js` 文件中，创建一个新的 `card` 组件：

```js
 Vue.component('card', {
        // Definition here
      })
```

1.  此组件将接收一个 `def` 属性，该属性将是我们上面描述的卡片定义对象。声明它与我们为 `top-bar` 组件所做的方式相同的 `props` 选项：

```js
      Vue.component('card', {
        props: ['def'],
      })
```

1.  现在，我们可以添加模板。从主要的 `div` 元素开始，带有 `card` 类：

```js
      Vue.component('card', {
        template: `<div class="card">
        </div>`,
        props: ['def'],
      })
```

1.  根据卡片类型更改背景颜色，添加一个使用卡片对象的 `type` 属性的动态 CSS 类：

```js
      <div class="card" :class="'type-' + def.type">
```

例如，如果卡片具有 `'attack'` 类型，则元素将获得 `type-attack` 类。然后，它将具有红色背景。

1.  现在，添加带有相应类的卡片标题：

```js
      <div class="card" :class="'type-' + def.type">
        <div class="title">{{ def.title }}</div>
      </div>
```

1.  添加分隔图像，该图像将在卡片标题和描述之间显示一些线条：

```js
      <div class="title">{{ def.title }}</div>
      <img class="separator" src="img/card-separator.svg" />
```

图像后附加描述元素。

注意，由于卡片对象的 `description` 属性是 HTML 格式化的文本，我们需要使用第二章介绍的特殊 `v-html` 指令。

1.  使用 `v-html` 指令来显示描述：

```js
      <div class="description"><div v-html="def.description"></div>             
      </div>
```

你可能已经注意到我们添加了一个嵌套的 `div` 元素，它将包含描述文本。这是为了使用 CSS flexbox 垂直居中文本。

1.  最后，添加卡片注释（也是 HTML 格式化的文本）。注意，有些卡片没有注释，因此我们必须在这里使用 `v-if` 指令：

```js
      <div class="note" v-if="def.note"><div v-html="def.note"></div>        
      </div>
```

现在卡片组件应该看起来像这样：

```js
Vue.component('card', {
  props: ['def'],
  template: `<div class="card" :class="'type-' + def.type">
    <div class="title">{{ def.title }}</div>
    <img class="separator" src="img/card-separator.svg" />
    <div class="description"><div v-html="def.description"></div></div>
    <div class="note" v-if="def.note"><div v-html="def.note"></div></div>
  </div>`,
})
```

现在，我们可以在主应用程序组件中尝试我们的新卡片组件。

1.  编辑主模板如下，并在顶部栏后添加一个 `card` 组件：

```js
      template: `<div id="#app">
        <top-bar :turn="turn" :current-player-             
         index="currentPlayerIndex" :players="players" />
        <card :def="testCard" />
      </div>`,
```

1.  我们还需要定义一个临时计算属性：

```js
 computed: {
        testCard () {
          return cards.archers
        },
      },
```

现在，您应该看到一个红色的攻击卡片，带有标题、描述和口味文本：

![](img/6b8c4676-e72a-41bb-915f-24b2ed0c0023.png)

# 监听组件上的原生事件

让我们尝试在我们的卡片上添加一个点击事件处理程序：

```js
<card :def="testCard" @click="handlePlay" />
```

在主要组件中使用愚蠢的方法：

```js
methods: {
  handlePlay () {
    console.log('You played a card!')
  }
}
```

如果你在浏览器中测试这个，你可能会惊讶地发现它不像预期那样工作。控制台什么也没有输出……

这是因为 Vue 有自己的组件事件系统，称为“自定义事件”，我们马上就会学到。该系统与浏览器事件分开，因此在这里 Vue 期望一个自定义的 `'click'` 事件，而不是浏览器事件。因此，`handler` 方法不会被调用。

为了解决这个问题，你应该在 `v-on` 指令上使用 `.native` 修饰符，如下所示：

```js
<card :def="testCard" @click.native="handlePlay" />
```

现在，当你点击卡片时，`handlePlay` 方法会按预期调用。

# 使用自定义事件进行子到父的通信

以前，我们使用 props 从父组件向其子组件通信。现在，我们想要做相反的事情，即从一个子组件向其父组件通信。对于我们的卡片组件，我们想要告诉父组件，当玩家点击卡片时，卡片正在被玩家播放。我们不能在这里使用 props，但是我们可以使用自定义事件。在我们的组件中，我们可以发出事件，父组件可以使用`$emit`特殊方法捕获。它接受一个必需的参数，即事件类型：

```js
this.$emit('play')
```

我们可以使用`$on`特殊方法在同一个 Vue 实例内监听自定义事件：

```js
this.$on('play', () => {
  console.log('Caught a play event!')
})
```

`$emit`方法还向父组件发送一个`'play'`事件。我们可以像以前一样在父组件模板中使用`v-on`指令来监听它：

```js
<card v-on:play="handlePlay" />
```

您还可以使用`v-bind`的快捷方式：

```js
<card @play="handlePlay" />
```

我们也可以添加任意数量的参数，这些参数将传递给处理程序方法：

```js
this.$emit('play', 'orange', 42)
```

在这里，我们发出了一个带有以下两个参数的`'play'`事件-- `'orange'`和`42`。

在处理中，我们可以通过参数获取它们，如下所示：

```js
handlePlay (color, number) {
  console.log('handle play event', 'color=', color, 'number=', number)
}
```

`color`参数将具有`'orange'`值，`number`参数将具有`42`值。

正如我们在前一节中所看到的，自定义事件与浏览器事件系统完全分开。特殊方法--`$on`和`$emit`--不是标准`addEventListener`和`dispatchEvent`的别名。这就解释了为什么我们需要在组件上使用`.native`修饰符来监听浏览器事件，如`'click'`。

回到我们的卡片组件，我们只需要发出一个非常简单的事件，告诉父组件卡片正在被播放：

1.  首先，添加会触发事件的方法：

```js
 methods: {
        play () {
          this.$emit('play')
        },
      },
```

1.  我们想在用户点击卡片时调用此方法。只需在主卡片`div`元素上监听浏览器点击事件：

```js
      <div class="card" :class="'type-' + def.type" @click="play">
```

1.  我们完成了卡片组件。要测试这一点，在主组件模板中监听`'play'`自定义事件：

```js
      <card :def="testCard" @play="handlePlay" />
```

现在，每当发出`'play'`事件时，将调用`handlePlay`方法。

我们本可以只监听本机点击事件，但通常最好使用自定义事件在组件之间进行通信。例如，当用户使用其他方法时，例如使用键盘选择卡片并按*Enter*键，我们也可以发出`'play'`事件；尽管我们不会在本书中实现该方法。

# 手牌

我们的下一个组件将是当前玩家的手牌，持有他们手中的五张牌。它将使用 3D 过渡进行动画处理，并且还将负责卡片动画（当卡片被抽取时，以及当它被打出时）。

1.  在`components/ui.js`文件中，添加一个具有`'hand'`ID 和一个基本模板的组件注册，带有两个`div`元素：

```js
 Vue.component('hand', {
        template: `<div class="hand">
          <div class="wrapper">
            <!-- Cards -->
          </div>
        </div>`,
      })
```

包装元素将帮助我们定位和动画处理卡片。

手中的每张卡片将由一个对象表示。目前，它将具有以下属性：

+   `id`：卡片定义的唯一标识符

+   `def`：卡片定义对象

作为提醒，所有的卡片定义都在 `cards.js` 文件中声明。

1.  我们的手部组件将通过一个名为 `cards` 的新数组属性接收代表玩家手牌的卡对象：

```js
      Vue.component('hand', {
        // ...
        props: ['cards'],
      })
```

1.  现在，我们可以使用 `v-for` 指令添加卡片组件了：

```js
      <div class="wrapper">
        <card v-for="card of cards" :def="card.def" />
      </div>
```

1.  为了测试我们的手部组件，我们将在应用程序状态中创建一个名为 `testHand` 的临时属性（在 `state.js` 文件中）：

```js
      var state = {
        // ...
        testHand: [],
      }
```

1.  在主组件中添加一个 `createTestHand` 方法（在 `main.js` 文件中）：

```js
      methods: {
        createTestHand () {
          const cards = []
          // Get the possible ids
          const ids = Object.keys(cards)

          // Draw 5 cards
          for (let i = 0; i < 5; i++) {
            cards.push(testDrawCard())
          }

          return cards
        },
      },
```

1.  为了测试手部，我们还需要这个临时的 `testDrawCard` 方法来模拟随机抽卡：

```js
      methods: {
        // ...
        testDrawCard () {
          // Choose a card at random with the ids
          const ids = Object.keys(cards)
          const randomId = ids[Math.floor(Math.random() * ids.length)]
          // Return a new card with this definition
          return {
            // Unique id for the card
            uid: cardUid++,
            // Id of the definition
            id: randomId,
            // Definition object
            def: cards[randomId],
          }
        }
      }
```

1.  使用 `created` 生命周期钩子来初始化手部：

```js
 created () {
        this.testHand = this.createTestHand()
      },
```

`cardUid` 是玩家抽取的卡片上的唯一标识符，对于识别手中的每张卡片都很有用，因为许多卡片可能共享完全相同的卡片定义，我们需要一种区分它们的方法。

1.  在主模板中，添加手部组件：

```js
      template: `<div id="#app">
        <top-bar :turn="turn" :current-player-           
         index="currentPlayerIndex" :players="players" />
        <hand :cards="testHand" />
      </div>`,
```

在您的浏览器中的结果应如下所示：

![](img/928716a8-049d-4deb-a1f5-c3827a3f597f.png)

# 使用过渡动画手部

在游戏过程中，当显示任何叠加时，手部将被隐藏。为了使应用程序更美观，当手部从 DOM 中添加或移除时，我们将对其进行动画处理。为此，我们将与强大的 Vue 工具--特殊的 `<transition>` 组件一起使用 CSS 过渡。它将帮助我们在使用 `v-if` 或 `v-show` 指令添加或移除元素时使用 CSS 过渡。

1.  首先，在 `state.js` 文件中的应用程序状态中添加一个新的 `activeOverlay` 数据属性：

```js
      // The consolidated state of our app
      var state = {
        // UI
        activeOverlay: null,
        // ...
      }
```

1.  在主模板中，我们将仅在 `activeOverlay` 未定义时显示手部组件，感谢 `v-if` 指令：

```js
      <hand :cards="testHand" v-if="!activeOverlay" />
```

1.  现在，如果您在浏览器控制台中将 `state.activeOverlay` 更改为任何真值，手部将消失：

```js
      state.activeOverlay = 'player-turn'
```

1.  另外，如果将其设置回 `null`，手部将再次显示：

```js
      state.activeOverlay = null
```

1.  当使用 `v-if` 或 `v-show` 指令添加或移除组件时应用过渡，请像这样将其包裹在过渡组件中：

```js
      <transition>
        <hand v-if="!activeOverlay" />
      </transition>
```

注意，这也适用于 HTML 元素：

```js
<transition>
  <h1 v-if="showTitle">Title</h1>
</transition>
```

`<transition>` 特殊组件不会出现在 DOM 中，就像我们在第二章 *Markdown Notebook* 中使用的 `<template>` 标签一样。

当元素被添加到 DOM 中（进入阶段）时，过渡组件将自动向元素应用以下 CSS 类：

+   `v-enter-active`：在进入过渡处于活动状态时应用此类。此类在元素插入到 DOM 中之前添加，并在动画完成时删除。您应该在此类中添加一些 `transition` CSS 属性，并定义它们的持续时间。

+   `v-enter`：元素的起始状态。此类在元素插入前添加，在元素插入后一帧删除。例如，你可以在此类中将不透明度设置为 `0`。

+   `v-enter-to`：元素的目标状态。此类在元素插入后一帧添加，与删除 `v-enter` 时同时发生。在动画完成时删除。

当元素从 DOM 中移除时（离开阶段），它们将被以下内容替换：

+   `v-leave-active`：在离开过渡处于活动状态时应用。此类在离开过渡触发时添加，并在元素从 DOM 中移除后删除。您应该在此类中添加一些`transition` CSS 属性并定义它们的持续时间。

+   `v-leave`：元素被移除时的起始状态。这个类也会在离开过渡触发时添加，并在一帧后删除。

+   `v-leave-to`：元素的目标状态。此类在离开过渡触发一帧后添加，与`v-leave`同时删除。当元素从 DOM 中移除时，它将被删除。

在离开阶段，元素不会立即从 DOM 中移除。在过渡完成后才会移除它，以便用户可以看到动画。

这里是一个总结了两个进入和离开阶段以及相应的 CSS 类的模式图：

![图](img/6159a0e0-22b4-4c34-aef2-b7e7730b7cd2.png)过渡组件将自动检测应用在元素上的 CSS 过渡的持续时间。

1.  我们需要编写一些 CSS 来制作我们的动画。创建一个新的`transitions.css`文件并将其包含在网页中：

```js
      <link rel="stylesheet" href="transitions.css" />
```

首先尝试基本的淡入淡出动画。我们希望在 1 秒钟内对不透明度 CSS 属性应用 CSS 过渡。

1.  为此，请同时使用`v-enter-active`和`v-leave-active`类，因为它们是相同的动画：

```js
      .hand.v-enter-active,
      .hand.v-leave-active {
        transition: opacity 1s;
      }
```

当手被添加或从 DOM 中移除时，我们希望它的不透明度为`0`（因此它将完全透明）。

1.  使用`v-enter`和`v-leave-to`类来应用这种完全透明：

```js
      .hand.v-enter,
      .hand.v-leave-to {
        opacity: 0;
      }
```

1.  回到主模板，使用过渡特殊组件将手组件包围起来：

```js
      <transition>
        <hand v-if="!activeOverlay" :cards="testHand" />
      </transition>
```

现在，当您隐藏或显示手时，它将淡入淡出。

1.  由于我们可能需要重用此动画，我们应该给它一个名称：

```js
      <transition name="fade">
        <hand v-if="!activeOverlay" :cards="testHand" />
      </transition>
```

我们必须更改我们的 CSS 类，因为 Vue 现在将使用`fade-enter-active`而不是`v-enter-active`。

1.  在`transition.css`文件中，修改 CSS 选择器以匹配此更改：

```js
      .fade-enter-active,
      .fade-leave-active {
        transition: opacity 1s;
      }

      .fade-enter,
      .fade-leave-to {
        opacity: 0;
      }
```

现在，我们可以在任何带有`<transition name="fade">`的元素上重复使用此动画。

# 更漂亮的动画

现在我们将制作一个更复杂但更好的动画，带有一些 3D 效果。除了手之外，我们还将为`.wrapper`元素（用于 3D 翻转）和`.card`元素添加动画。卡片将开始堆叠，并逐渐扩展到手中的预期位置。最后，它将以玩家从桌子上拿起卡片的方式进行动画。

1.  首先创建新的过渡 CSS 类，使用`'hand'`名称代替`'fade'`：

```js
      .hand-enter-active,
      .hand-leave-active {
        transition: opacity .5s;
      }

      .hand-enter,
      .hand-leave-to {
        opacity: 0;
      }
```

1.  在主模板中也更改过渡名称：

```js
      <transition name="hand">
        <hand v-if="!activeOverlay" :cards="testHand" />
      </transition>
```

1.  让我们对.wrapper 元素进行动画处理。使用 CSS transform 属性将 3D 变换应用于元素：

```js
      .hand-enter-active .wrapper,
      .hand-leave-active .wrapper {
        transition: transform .8s cubic-bezier(.08,.74,.34,1);
        transform-origin: bottom center;
      }

      .hand-enter .wrapper,
      .hand-leave-to .wrapper {
        transform: rotateX(90deg);
      }
```

右旋转轴是水平轴，即`x`。这将使卡片动画看起来就像被玩家拿起一样。请注意，定义了一个立方贝塞尔缓动函数，以使动画更平滑。

1.  最后，通过设置负的水平边距来为卡片本身添加动画，这样它们看起来就像是堆叠起来的：

```js
      .hand-enter-active .card,
      .hand-leave-active .card {
        transition: margin .8s cubic-bezier(.08,.74,.34,1);
      }

      .hand-enter .card,
      .hand-leave-to .card {
        margin: 0 -100px;
      }
```

现在，如果您像以前那样使用浏览器控制台隐藏和显示手牌，它将有一个漂亮的动画。

# 打出一张牌

现在，我们需要处理手牌组件中的`'play'`事件，当用户点击它们时，我们会发出一个新的`'card-play'`事件到主组件，并附加一个额外的参数--所打出的牌。

1.  首先，创建一个名为`handlePlay`的新方法。它接受一个`card`参数，并向父组件发出新事件：

```js
      methods: {
        handlePlay (card) {
          this.$emit('card-play', card)
        },
      },
```

1.  然后，为我们的卡片添加一个对`'play'`事件的监听器：

```js
      <card v-for="card of cards" :def="card.def" 
      @play="handlePlay(card) />
```

正如您在这里看到的，我们直接使用了`v-for`循环的迭代变量`card`。这样，我们就不需要卡片组件发出它的`card`项目，因为我们已经知道它是什么。

为了测试牌的打出，我们现在只会从手牌中移除它。

1.  在`main.js`文件的主组件中创建一个名为`testPlayCard`的新临时方法：

```js
      methods: {
        // ...
        testPlayCard (card) {
          // Remove the card from player hand
          const index = this.testHand.indexOf(card)
          this.testHand.splice(index, 1)
        }
      },
```

1.  在主模板中的`hand`组件上添加`'card-play'`事件的事件侦听器：

```js
      <hand v-if="!activeOverlay" :cards="testHand" @card-play="testPlayCard" />
```

如果您点击一张卡片，它现在应该向手牌组件发出一个`'play'`事件，然后手牌组件将向主组件发出一个`'card-play'`事件。接着，它将从手牌中移除该卡片，使其消失。为了帮助您调试这种情况，开发工具有一个事件选项卡：

![](img/32fa4cee-0203-43fe-b413-eafcccce1ea5.png)

# 为卡片列表添加动画

对于我们的手牌，有三个缺失的动画--当一张牌被添加或从玩家手牌中移除时，以及当它被移动时。当回合开始时，玩家会抽一张牌。这意味着我们将向手牌列表中添加一张牌，并且它将从右侧滑入手牌。当打出一张牌时，我们希望它上移并变大。

要为一系列元素添加动画，我们将需要另一个特殊的组件--`<transition-group>`。当它们被添加、移除和移动时，它会对子元素进行动画处理。在模板中，它看起来像这样：

```js
<transition-group>
  <div v-for="item of items" />
</transition-group>
```

与`<transition>`元素不同，过渡组在 DOM 中默认显示为一个`<span>`元素。您可以使用`tag`属性更改 HTML 元素：

```js
<transition-group tag="ul">
  <li v-for="item of items" />
</transition-group>
```

在我们`hand`组件的模板中，用一个过渡组件将卡片组件包起来，指定我们将调用的过渡的名称为`"card"`，并添加`"cards"` CSS 类：

```js
<transition-group name="card" tag="div" class="cards">
  <card v-for="card of cards" :def="card.def" @play="handlePlay(card) />
</transition-group>
```

在我们继续之前，还缺少一个重要的事情--过渡组的子元素必须通过唯一键来标识。

# 关键特殊属性

当 Vue 在`v-for`循环中更新 DOM 元素列表时，它会尽量减少应用于 DOM 的操作数量，如添加或移除元素。这是在大多数情况下更新 DOM 的非常高效的方法，可以提高性能。

为了做到这一点，Vue 会尽可能地重复使用元素，并且只在需要的地方打补丁来达到所需的结果。这意味着重复的元素将在原地打补丁，如果列表中添加或删除项，则不会被移动。然而，这也意味着如果我们对它们应用过渡，它们就不会发生动画。

以下是这样运作的示意图：

![](img/607e2253-c68b-41da-89b6-689de2112ef8.png)

在这个示例中，我们移除了列表中的第三个项目，即`c`。然而，第三个`div`元素不会被销毁--它将会被重复利用，使用列表中的第四个项目`d`。实际上，这是第四个被销毁的`div`元素。

幸运的是，我们可以告诉 Vue 每个元素如何被识别，这样它就可以重复使用和重新排序它们。为了做到这一点，我们需要使用`key`特殊属性指定一个唯一标识。例如，我们的每个项目都可以有一个我们将用作关键字的唯一 ID：

![](img/8daeec3d-58b6-44e3-a822-86c57e8e518b.png)

在这里，我们指定了关键字，以便 Vue 知道第三个`div`元素应该被销毁，第四个 div 元素应该被移动。

关键特殊属性的工作方式与标准属性类似，因此，如果我们想为其分配动态值，就需要使用`v-bind`指令。

回到我们的卡片，我们可以使用卡片上的唯一标识作为关键字：

```js
<card v-for="card of cards" :def="card.def" :key="card.uid" @play="handlePlay(card) />
```

现在，如果我们在 JavaScript 中添加、移动或删除一个卡片项，它将会在 DOM 中以正确的顺序体现出来。

# CSS 过渡

与之前一样，我们有以下六个可用的 CSS 类，以我们的组过渡名称`'card'`为前缀：`card-enter-active`, `card-enter`, `card-enter-to`, `card-leave-active`, `card-leave`, 和 `card-leave-to`。它们将被应用于组过渡的直接子元素，也就是我们的卡片组件。

1.  组过渡器中对于移动的元素有一个额外的类名--`v-move`。Vue 会使用 CSS 的`transform`属性来使它们移动，所以我们只需要为其应用至少带有持续时间的 CSS 过渡就行了：

```js
      .card-move {
        transition: transform .3s;
      }      
```

现在，当你点击卡片进行打出时，它应该消失，而其余的卡片将移动到它们的新位置。你也可以向手牌中添加卡片。

1.  在 Vue devtools 中选择主组件，并在浏览器控制台中执行以下内容：

```js
      state.testHand.push($vm.testDrawCard())
```

在 devtools 中选择一个组件会在浏览器控制台中公开它作为`$vm`。

就像我们对手牌做的那样，当卡片进入手牌时，我们也会为它们添加动画，当它们被打出时（因此离开手牌）。

1.  由于我们需要在卡片上始终以相同时机过渡多个 CSS 属性（除了在离开过渡期间），我们将刚刚写的`.card-move`规则改成这样：

```js
 .card {
        /* Used for enter, move and mouse over animations */
        transition: all .3s;
      }
```

1.  对于进入动画，请指定卡片的状态作为过渡的开始：

```js
 .card-enter {
        opacity: 0;
        /* Slide from the right */
        transform: scale(.8) translateX(100px);
      }
```

1.  离开动画需要更多规则，因为打出卡片的动画更复杂，涉及将卡片向上缩放：

```js
 .card-leave-active {
        /* We need different timings for the leave transition */
        transition: all 1s, opacity .5s .5s;
        /* Keep it in the same horizontal position */
        position: absolute !important;
        /* Make it painted over the other cards */
        z-index: 10;
        /* Unclickable during the transition */
        pointer-events: none;
      }

      .card-leave-to {
        opacity: 0;
        /* Zoom the card upwards */
        transform: translateX(-106px) translateY(-300px) scale(1.5);
      }
```

这就足以使你的卡片都正确地动画化。你可以尝试再次玩耍并添加卡片到手中，看看结果。

# 叠加层

我们需要的最后一个 UI 元素是叠加层。以下是其中的三个：

+   '新回合'叠加层在轮到当前玩家时显示当前玩家的名字。点击'新回合'玩家会切换到'上一回合'叠加层。

+   '上一回合'叠加层显示玩家之前对手做的事情。它显示以下内容之一：

    +   上一回合对手出的卡片

    +   提醒玩家他们的回合被跳过了

+   '游戏结束'叠加层显示玩家或两个玩家输掉时。它显示玩家的名字与短语“获胜”或“被击败”。点击'游戏结束'叠加层重新加载游戏。

所有这些叠加层有两个共同点--当用户点击它们时它们会执行某些操作，并且它们具有类似的布局设计。因此，我们应该在这里做得更聪明，结构化我们的组件以在合适的地方尽可能地重用代码。这里的想法是创建一个通用的叠加层组件，该组件将负责处理点击事件和布局以及三个特定的叠加层内容组件，用于我们需要的每个叠加层。

在开始之前，在`state.js`文件中的应用状态中添加一个新的`activeOverlay`属性：

```js
// The consolidated state of our app
var state = {
  // UI
  activeOverlay: null,
  // ...
}
```

这将保存当前显示的叠加层的名称，如果没有显示叠加层，则为`null`。

# 使用插槽进行内容分发

如果我们可以在主模板中的叠加层组件中放置内容，这将非常方便，就像这样：

```js
<overlay>
  <overlay-content-player-turn />
</overlay>
```

我们将在`overlay`组件中封装额外的布局和逻辑，同时仍然能够放置任何内容。这是通过一个特殊的元素--`<slot>`完成的。

1.  让我们创建我们的`overlay`组件，并加上两个`div`元素：

```js
 Vue.component('overlay', {
        template: `<div class="overlay">
          <div class="content">
            <!-- Our slot will be there -->
          </div>
        </div>`,
      })
```

1.  在`.overlay` div 上添加点击事件监听器，调用`handleClick`方法：

```js
      <div class="overlay" @click="handleClick">
```

1.  然后，在我们发出自定义`'close'`事件的地方添加上述方法：

```js
      methods: {
        handleClick () {
          this.$emit('close')
        },
      },
```

此事件将有助于知道何时在回合开始时从一个叠加层切换到下一个。

1.  现在，在`.content` div 中放置一个`<slot>`元素：

```js
      template: `<div class="overlay" @click="handleClick">
        <div class="content">
          <slot />
        </div>
      </div>`,
```

现在，如果我们在使用我们的组件时在`overlay`标签之间放置了一些内容，它将被包含在 DOM 中，并替换`<slot>`标签。例如，我们可以这样做：

```js
<overlay>
  Hello world!
</overlay>
```

另外，它将在页面中呈现如下：

```js
<div class="overlay">
  <div class="content">
    Hello world!
  </div>
</div>
```

它与任何内容一起使用，因此您也可以放置 HTML 或 Vue 组件，它仍将以相同的方式工作！

1.  该组件已准备好在主模板中使用，因此将其添加到最后：

```js
      <overlay>
        Hello world!
      </overlay>
```

这三个叠加层内容将是独立的组件：

+   `overlay-content-player-turn` 显示回合的开始

+   `overlay-content-last-play` 显示上一回合对手打出的最后一张卡片

+   `overlay-content-game-over`在游戏结束时显示

在深入研究这些内容之前，我们需要有关状态中两个玩家的一些更多数据。

1.  回到`state.js`文件，并为每个玩家添加以下属性：

```js
      // Starting stats
      food: 10,
      health: 10,
      // Is skipping is next turn
      skipTurn: false,
      // Skiped turn last time
      skippedTurn: false,
      hand: [],
      lastPlayedCardId: null,
      dead: false,
```

现在，你应该在`players`数组中有两个具有相同属性的项目，除了玩家名称。

# '玩家回合'叠加层

第一个叠加层将向当前玩家显示两条不同的消息，具体取决于是否跳过了他们的回合。玩家属性将接收当前玩家，以便我们可以访问其数据。我们将使用`v-if`指令与`v-else`指令和刚刚添加到玩家的`skipTurn`属性：

```js
 Vue.component('overlay-content-player-turn', {
        template: `<div>
          <div class="big" v-if="player.skipTurn">{{ player.name }},      <br>your turn is skipped!</div>
          <div class="big" v-else>{{ player.name }},<br>your turn has       come!</div>
          <div>Tap to continue</div>
        </div>`,
        props: ['player'],
      })
```

# '最后一次出牌'叠加层

这个比较复杂。我们需要一个新函数来获取玩家最后打出的卡片。在`utils.js`文件中，添加新函数`getLastPlayedCard`：

```js
function getLastPlayedCard (player) {
  return cards[player.lastPlayedCardId]
}
```

我们现在可以通过传递`opponent`prop 在`lastPlayedCard`计算属性中使用此函数：

```js
Vue.component('overlay-content-last-play', {
  template: `<div>
    <div v-if="opponent.skippedTurn">{{ opponent.name }} turn was skipped!</div>
    <template v-else>
      <div>{{ opponent.name }} just played:</div>
      <card :def="lastPlayedCard" />
    </template>
  </div>`,
  props: ['opponent'],
  computed: {
    lastPlayedCard () {
      return getLastPlayedCard(this.opponent)
    },
  },
})
```

请注意，我们是直接重用了之前创建的`card`组件来展示卡片。

# '游戏结束'叠加层

对于这个，我们将创建另一个组件，名为`player-result`，它将显示玩家是胜利还是失败。我们将通过一个 prop 传递玩家的名称。我们将使用计算属性为该玩家计算结果，并将其作为动态 CSS 类使用：

```js
Vue.component('player-result', {
  template: `<div class="player-result" :class="result">
    <span class="name">{{ player.name }}</span> is
    <span class="result">{{ result }}</span>
  </div>`,
  props: ['player'],
  computed: {
    result () {
      return this.player.dead ? 'defeated' : 'victorious'
    },
  },
})
```

现在，我们可以通过循环遍历`players`属性并使用`player-result`组件来创建游戏结束叠加层：

```js
Vue.component('overlay-content-game-over', {
  template: `<div>
    <div class="big">Game Over</div>
    <player-result v-for="player in players" :player="player" />
  </div>`,
  props: ['players'],
})
```

# 动态组件

现在，是时候将所有这些内容放入我们的叠加层组件中，并使用之前定义的`activeOverlay`属性。

1.  添加组件并在主模板中使用相应的`activeOverlay`值来显示它们：

```js
      <overlay v-if="activeOverlay">
        <overlay-content-player-turn
          v-if="activeOverlay === 'player-turn'" />
        <overlay-content-last-play
          v-else-if="activeOverlay === 'last-play'" />
        <overlay-content-game-over
          v-else-if="activeOverlay === 'game-over'" />
      </overlay>
```

如果`activeOverlay`属性等于`null`，我们将完全移除叠加层。

在添加 props 之前，我们将需要修改`state.js`文件中的应用程序状态，并添加一些 getter 函数。

1.  第一个将从`currentPlayerIndex`属性返回`player`对象：

```js
      get currentPlayer () {
        return state.players[state.currentPlayerIndex]
      },
```

1.  第二个将返回对手的`player`索引：

```js
      get currentOpponentId () {
        return state.currentPlayerIndex === 0 ? 1 : 0
      },
```

1.  最后，第三个将返回相应的玩家对象：

```js
      get currentOpponent () {
        return state.players[state.currentOpponentId]
      },
```

1.  现在，我们可以为叠加层内容添加 props：

```js
      <overlay v-if="activeOverlay">
        <overlay-content-player-turn
          v-if="activeOverlay === 'player-turn'"
          :player="currentPlayer" />
        <overlay-content-last-play
          v-else-if="activeOverlay === 'last-play'"
          :opponent="currentOpponent" />
        <overlay-content-game-over
          v-else-if="activeOverlay === 'game-over'"
          :players="players" />
      </overlay>
```

你可以通过在浏览器控制台中设置`activeOverlay`属性来测试这些叠加层：

```js
state.activeOverlay = 'player-turn'
state.activeOverlay = 'last-play'
state.activeOverlay = 'game-over'
state.activeOverlay = null
```

如果你想测试`last-play`叠加层，你需要为玩家的`lastPlayedCardId`属性指定一个有效的值，如`'catapult'`或`'farm'`。

我们的代码开始变得杂乱，在三个条件语句中。幸运的是，有一个特殊的组件可以将自身转换为任何组件 - 那就是`component`组件。只需将其`is`属性设置为组件名称，组件定义对象，甚至是 HTML 标签，它将变形为该组件：

```js
<component is="h1">Title</component>
<component is="overlay-content-player-turn" />
```

它就像任何其他的 prop 一样，所以我们可以使用`v-bind`指令来通过 JavaScript 表达式动态改变组件的本质。如果我们使用我们的`activeOverlay`属性来做到这一点会怎么样？我们的覆盖层内容组件是否方便地以相同的`'over-content-'`前缀命名？看一下：

```js
<component :is="'overlay-content-' + activeOverlay" />
```

就是这样。现在，通过改变`activeOverlay`属性的值，我们将改变覆盖层内显示的组件。

1.  在添加回 props 后，主模板中的覆盖层应该如下所示：

```js
      <overlay v-if="activeOverlay">
        <component :is="'overlay-content-' + activeOverlay"
          :player="currentPlayer" :opponent="currentOpponent"
          :players="players" />
      </overlay>
```

别担心，未使用的 props 不会影响不同覆盖层的工作方式。

# 覆盖层动画

就像我们用手做的那样，我们将使用过渡来动画显示覆盖层。

1.  在覆盖层组件周围添加一个名为“zoom”的过渡：

```js
      <transition name="zoom">
        <overlay v-if="activeOverlay">
          <component :is="'overlay-content-' + activeOverlay"                    
          :player="currentPlayer" :opponent="currentOpponent"                      
          :players="players" />
        </overlay>
      </transition>
```

1.  在`transition.css`文件中添加以下 CSS 规则：

```js
      .zoom-enter-active,
      .zoom-leave-active {
        transition: opacity .3s, transform .3s;
      }

      .zoom-enter,
      .zoom-leave-to {
        opacity: 0;
        transform: scale(.7);
      }
```

这是一个简单的动画，会在淡出的同时缩小覆盖层。

# 关键属性

现在，如果您在浏览器中尝试动画，它应该只在两种情况下起作用：

+   当您没有显示任何覆盖层，并且您设置了一个时

+   当您有一个显示的覆盖层，并且您将`activeOverlay`设置为`null`以隐藏它时

如果您在不同的覆盖层之间切换，动画将不起作用。这是由于 Vue 更新 DOM 的方式；正如我们在*关键特殊属性*部分中看到的那样，它将尽可能地重用 DOM 元素以优化性能。在这种情况下，我们需要使用关键特殊属性来向 Vue 发出提示，表明我们希望将不同的覆盖层视为单独的元素。因此，当我们从一个覆盖层过渡到另一个覆盖层时，两者都将存在于 DOM 中，并且可以播放动画。

让我们给我们的覆盖层组件添加键，这样 Vue 在更改`activeOverlay`值时将其视为多个单独的元素：

```js
<transition name="zoom">
  <overlay v-if="activeOverlay" :key="activeOverlay">
    <component :is="'overlay-content-' + activeOverlay" :player="currentPlayer" :opponent="currentOpponent" :players="players" />
  </overlay>
</transition>
```

现在，如果我们将`activeOverlay`设置为`'player-turn'`，覆盖层将具有键为`'player-turn'`。然后，如果我们将`activeOverlay`设置为`'last-play'`，将创建一个完全新的键为`'last-play'`的覆盖层，我们可以在两者之间进行过渡动画。您可以在浏览器中通过将不同的值设置为`state.activeOverlay`来尝试此操作。

# 覆盖层背景

此时，有些东西丢失了--覆盖层背景。我们不能将其包含在覆盖层组件内部，因为在动画期间它会被放大--这会非常尴尬。相反，我们将使用我们已经创建的简单`fade`动画。

在主模板中，在`zoom`过渡和`overlay`组件之前添加一个带有`overlay-background`类的新的`div`元素：

```js
<transition name="fade">
  <div class="overlay-background" v-if="activeOverlay" />
</transition>
```

使用`v-if`指令，只有在显示任何覆盖层时才会显示它。

# 游戏世界和场景

我们大部分完成了 UI 元素，所以现在我们可以进入游戏场景组件。我们将有一些新组件要做--玩家城堡，每个城堡都有一个健康和食物气泡，以及一些背景中的动画云，以增加乐趣。

在`components`文件夹中创建一个新的`world.js`文件，并在页面中包含它：

```js
<!-- ... -->
<script src="img/ui.js"></script>
<script src="img/world.js"></script>
<script src="img/main.js"></script>
```

让我们从城堡开始。

# 城堡们

这个实际上相当简单，因为它仅包含两个图像和一个城堡旗帜组件，主要负责显示健康和食物状态：

1.  在`world.js`文件中，创建一个新的城堡组件，其中包含接受`players`和`index`属性的两个图像：

```js
 Vue.component('castle', {
        template: `<div class="castle" :class="'player-' + index">
          <img class="building" :src="img/castle' + index + '.svg'" />
          <img class="ground" :src="img/ground' + index + '.svg'" />
          <!-- Later, we will add a castle-banners component here -->
        </div>`,
        props: ['player', 'index'],
      })
```

对于此组件，每个玩家有一个城堡和一个地面图像；这意味着总共有四幅图像。例如，对于索引为`0`的玩家，有`castle0.svg`和`ground0.svg`图像。

1.  在主模板中，在`top-bar`组件的正下方，创建一个具有`world` CSS 类的新`div`元素，循环遍历玩家以显示两座城堡，并添加另一个具有`land`类的`div`元素：

```js
      <div class="world">
        <castle v-for="(player, index) in players" :player="player"                 
         :index="index" />
        <div class="land" />
      </div>
```

在浏览器中，应该看到每个玩家的一个城堡，如下所示：

![](img/fb8e96b4-1277-46dd-b2e7-8fe28baddcad.png)

# 城堡旗帜

城堡旗帜将显示城堡的健康和食物。`castle-banners`组件内部将包含两个组件：

+   一个垂直的横幅，其高度根据状态的数量而变化

+   一个显示实际数字的气泡

它将看起来像这样：

![](img/9673ab25-0535-4f51-a154-facc7e9087d3.png)

1.  首先，创建一个只包含状态图标和一个`player`属性的新`castle-banners`组件：

```js
 Vue.component('castle-banners', {
        template: `<div class="banners">
          <!-- Food -->
          <img class="food-icon" src="img/food-icon.svg" />
          <!-- Bubble here -->
          <!-- Banner bar here -->

          <!-- Health -->
          <img class="health-icon" src="img/health-icon.svg" />
          <!-- Bubble here -->
          <!-- Banner bar here -->
        </div>`,
        props: ['player'],
      })
```

1.  我们还需要两个计算属性来计算健康和食物比例：

```js
      computed: {
        foodRatio () {
          return this.player.food / maxFood
        },
        healthRatio () {
          return this.player.health / maxHealth
        },
      }
```

`maxFood`和`maxHealth`变量在`state.js`文件的开头定义。

1.  在`castle`组件中添加新的`castle-banners`组件：

```js
      template: `<div class="castle" :class="'player-' + index">
        <img class="building" :src="img/castle' + index + '.svg'" />
        <img class="ground" :src="img/ground' + index + '.svg'" />
        <castle-banners :player="player" />
      </div>`,
```

# 食物和健康气泡

此组件包含一个图像和一个显示城堡食物或健康当前数量的文本。其位置将根据此数量变化--当数量减少时会上升，而在重新补充时会下降。

对于这个组件，我们将需要三个属性：

+   `type`是食物或健康之一；它将用于 CSS 类和图像路径

+   `value`是在气泡中显示的数量

+   `ratio`是数量除以最大数量

我们还需要一个计算属性来计算气泡随`ratio`属性的垂直位置。位置将在 40 像素到 260 像素之间变化。因此，位置值由以下表达式给出：

```js
(this.ratio * 220 + 40) * state.worldRatio + 'px'
```

记得要用`worldRatio`值将每个位置或大小相乘，这样游戏才能考虑窗口大小（如果窗口更大，则它变大，反之亦然）。

1.  让我们编写我们的新`bubble`组件：

```js
 Vue.component('bubble', {
        template: `<div class="stat-bubble" :class="type + '-bubble'"               
        :style="bubbleStyle">
          <img :src="img/' + type + '-bubble.svg'" />
          <div class="counter">{{ value }}</div>
        </div>`,
        props: ['type', 'value', 'ratio'],
        computed: {
          bubbleStyle () {
            return {
              top: (this.ratio * 220 + 40) * state.worldRatio + 'px',
            }
          },
        },
      })
```

它有一个根`div`元素，具有`stat-bubble` CSS 类，以及一个动态类（根据`type`属性值，可以是`'food-bubble'`或`'health-bubble'`），再加上我们用`bubbleStyle`计算属性设置的动态 CSS 样式。

它包含一个 SVG 图像，食物和健康不一样，并且一个具有`counter`类的`div`元素显示数量。

1.  向`castle-banners`组件添加一个食物和一个健康气泡：

```js
      template: `<div class="banners">
        <!-- Food -->
        <img class="food-icon" src="img/food-icon.svg" />
        <bubble type="food" :value="player.food" :ratio="foodRatio" />
        <!-- Banner bar here -->

        <!-- Health -->
        <img class="health-icon" src="img/health-icon.svg" />
        <bubble type="health" :value="player.health"             
      :ratio="healthRatio" />
        <!-- Banner bar here -->
      </div>`,
```

# 横幅条

我们需要的另一个组件是挂在城堡塔楼上的垂直横幅。其长度将根据食物或健康的数量而变化。这一次，我们将创建一个动态 SVG 模板，以便我们可以修改横幅的高度。

1.  首先，使用两个属性（颜色和比例）和计算属性`height`创建组件：

```js
 Vue.component('banner-bar', {
        props: ['color', 'ratio'],
        computed: {
          height () {
            return 220 * this.ratio + 40
          },
        },
      })
```

到目前为止，我们以两种不同的方式定义了我们的模板——我们要么使用了我们页面的 HTML，要么将字符串设置为组件的`template`选项。我们将使用另一种编写组件模板的方法——在 HTML 中使用特殊的脚本标签。通过在此脚本标签内部编写带有唯一 ID 的模板，并在定义组件时引用此 ID，它的工作原理是。

1.  打开`banner-template.svg`文件，其中包含我们将用作动态模板的横幅图像的 SVG 标记。复制文件的内容。

1.  在`<div id="app">`元素后的`index.html`文件中，添加一个`script`标签，类型为`text/x-template`，并带有`banner`ID，然后粘贴`svg`内容：

```js
      <script type="text/x-template" id="banner">
        <svg viewBox="0 0 20 260">
          <path :d="`m 0,0 20,0 0,${height} -10,-10 -10,10 z`"                    
          :style="`fill:${color};stroke:none;`" />
        </svg>
      </script>
```

正如您所看到的，这是一个标准模板，具有可用于使用的所有语法和指令。在这里，我们两次使用了`v-bind`指令的缩写。请注意，您可以在所有 Vue 模板中使用 SVG 标记。

1.  现在，回到我们的组件定义中，添加`template`选项，并使用井号标记前面的脚本标签模板的 ID：

```js
      Vue.component('banner-bar', {
        template: '#banner',
        // ...
      })
```

完成！该组件现在将查找页面中带有`banner`ID 的脚本标签模板，并将其用作模板。

1.  在`castle-banners`组件中，使用相应的颜色和比例添加另外两个`banner-bar`组件：

```js
      template: `<div class="banners">
        <!-- Food -->
        <img class="food-icon" src="img/food-icon.svg" />
        <bubble type="food" :value="player.food" :ratio="foodRatio" />
        <banner-bar class="food-bar" color="#288339" :ratio="foodRatio"        
        />

        <!-- Health -->
        <img class="health-icon" src="img/health-icon.svg" />
        <bubble type="health" :value="player.health"                   
        :ratio="healthRatio" />
        <banner-bar class="health-bar" color="#9b2e2e"                         
       :ratio="healthRatio" />
      </div>`,
```

现在，您应该能够看到悬挂在城堡上的横幅，并且如果您更改食物和健康值，则它们会收缩。

# 动画化数值

如果我们可以在它们收缩或扩展时对它们进行动画处理，这些横幅会更漂亮。我们不能依赖于 CSS 过渡，因为我们需要动态更改 SVG 路径，所以我们需要另一种方式——我们将动画化模板中使用的`height`属性的值。

1.  首先，让我们将模板的计算属性重命名为`targetHeight`：

```js
      computed: {
        targetHeight () {
          return 220 * this.ratio + 40
        },
      },
```

`targetHeight`属性将在比例变化时仅计算一次。

1.  添加一个新的`height`数据属性，我们将能够在`targetHeight`更改时对其进行动画处理：

```js
 data () {
        return {
          height: 0,
        }
      },
```

1.  在组件创建后，在`created`钩子中将`height`的值初始化为`targetHeight`的值：

```js
 created () {
        this.height = this.targetHeight
      },
```

为了使高度值动画化，我们将使用流行的`**TWEEN.js**`库，该库已经包含在`index.html`文件中。该库通过创建一个新的`Tween`对象来工作，该对象采用起始值、缓动函数和结束值。它提供了诸如`onUpdate`之类的回调，我们将使用这些回调来更新动画的`height`属性。

1.  我们希望在`targetHeight`属性更改时启动动画，因此添加一个带有以下动画代码的监视程序：

```js
 watch: {
        targetHeight (newValue, oldValue) {
          const vm = this
          new TWEEN.Tween({ value: oldValue })
            .easing(TWEEN.Easing.Cubic.InOut)
            .to({ value: newValue }, 500)
            .onUpdate(function () {
              vm.height = this.value.toFixed(0)
            })
            .start()
        },
      },
```

`onUpdate` 回调中的 `this` 上下文是 `Tween` 对象，而不是 Vue 组件实例。这就是为什么我们需要一个好的临时变量来保存组件实例 `this`（这里，`vm` 变量就是那个）。

1.  我们需要最后一件事来使我们的动画工作。在 `main.js` 文件中，请求浏览器从浏览器请求绘画帧以使 `TWEEN.js` 库滴答作响，感谢浏览器的 `requestAnimationFrame` 函数：

```js
      // Tween.js
      requestAnimationFrame(animate);

      function animate(time) {
        requestAnimationFrame(animate);
        TWEEN.update(time);
      }
```

如果标签在后台，则 `requestAnimationFrame` 函数将等待标签再次变为可见。这意味着如果用户看不到页面，动画将不会播放，从而节省计算机资源和电池电量。请注意，CSS 过渡和动画也是如此。

现在当你改变玩家的食物或健康状态时，横幅将逐渐缩小或增大。

# 动态云

为了为游戏世界增添一些生气，我们将创建一些在天空中滑动的云。它们的位置和动画持续时间将是随机的，它们将从窗口的左侧移动到右侧。

1.  在 `world.js 文件` 中，添加云动画的最小和最大持续时间：

```js
      const cloudAnimationDurations = {
        min: 10000, // 10 sec
        max: 50000, // 50 sec
      }
```

1.  然后，创建云组件，包括图像和 `type` 属性：

```js
 Vue.component('cloud', {
        template: `<div class="cloud" :class="'cloud-' + type" >
          <img :src="img/strong> + '.svg'" />
        </div>`,
        props: ['type'],
      })
```

将有五个不同的云，因此 `type` 属性将从 1 到 5。

1.  我们将需要使用一个响应式的 `style` 数据属性来更改组件的 `z-index` 和 `transform` CSS 属性：

```js
 data () {
        return {
          style: {
            transform: 'none',
            zIndex: 0,
          },
        }
      },
```

1.  使用 `v-bind` 指令应用这些样式属性：

```js
      <div class="cloud" :class="'cloud-' + type" :style="style">
```

1.  让我们创建一个方法来使用 `transform` CSS 属性设置云组件的位置：

```js
      methods: {
        setPosition (left, top) {
          // Use transform for better performance
          this.style.transform = `translate(${left}px, ${top}px)`
        },
      }
```

1.  当图片加载时，我们需要初始化云的水平位置，使其位于视口之外。创建一个新的 `initPosition`，它使用 `setPosition` 方法：

```js
      methods: {
        // ...
        initPosition () {
          // Element width
          const width = this.$el.clientWidth
          this.setPosition(-width, 0)
        },
      }
```

1.  在图像上添加一个事件监听器，使用 `v-on` 指令缩写监听 `load` 事件并调用 `initPosition` 方法：

```js
      <img :src="img/cloud' + type + '.svg'" @load="initPosition" />
```

# 动画

现在，让我们继续进行动画本身。就像我们为城堡横幅所做的那样，我们将使用 `TWEEN.js` 库：

1.  首先，创建一个新的 `startAnimation` 方法，计算一个随机的动画持续时间，并接受一个延迟参数：

```js
      methods: {
        // ...

        startAnimation (delay = 0) {
          const vm = this

          // Element width
          const width = this.$el.clientWidth

          // Random animation duration
          const { min, max } = cloudAnimationDurations
          const animationDuration = Math.random() * (max - min) + min

          // Bing faster clouds forward
          this.style.zIndex = Math.round(max - animationDuration)

          // Animation will be there
        },
      }
```

云越快，其动画持续时间就越低。更快的云将在较慢的云之前显示，这要归功于 `z-index` CSS 属性。

1.  在 `startAnimation` 方法内部，计算云的随机垂直位置，然后创建一个 `Tween` 对象。它将以延迟动画水平位置，并在每次更新时设置云的位置。当它完成时，我们将以随机延迟启动另一个动画：

```js
      // Random position
      const top = Math.random() * (window.innerHeight * 0.3)

      new TWEEN.Tween({ value: -width })
        .to({ value: window.innerWidth }, animationDuration)
        .delay(delay)
        .onUpdate(function () {
          vm.setPosition(this.value, top)
        })
        .onComplete(() => {
          // With a random delay
          this.startAnimation(Math.random() * 10000)
        })
        .start()
```

1.  在组件的 `mounted` 钩子中，调用 `startAnimation` 方法开始初始动画（带有随机延迟）：

```js
 mounted () {
        // We start the animation with a negative delay
        // So it begins midway
        this.startAnimation(-Math.random() *                   
      cloudAnimationDurations.min)
      },
```

我们的云组件已准备好。

1.  在 `world` 元素的主模板中添加一些云：

```js
      <div class="clouds">
        <cloud v-for="index in 10" :type="(index - 1) % 5 + 1" />
      </div>
```

要小心将值传递给 `type` 属性，其取值范围为 1 到 5。在这里，我们使用 `%` 运算符来返回 5 的除法余数。

它应该是这样的：

![图片](img/52782d12-755c-4eb2-bbe8-811741339acf.png)

# 游戏过程

所有的组件都完成了！ 我们只需要为应用添加一些游戏逻辑，使其可玩。 游戏开始时，每个玩家都会抽取他们的初始手牌。

然后，每个玩家的回合都按照以下步骤进行：

1.  `player-turn`覆盖层显示，以便玩家知道轮到他们了。

1.  `last-play`覆盖层显示了上次游戏中另一位玩家打出的牌。

1.  玩家通过点击卡片来出牌。

1.  卡片从他们的手中移除，并应用其效果。

1.  我们稍等一下，以便玩家可以看到这些效果的发生。

1.  然后，回合结束，并将当前玩家切换到另一个玩家。

# 抽牌

在抽牌之前，我们需要在`state.js`文件中的应用状态中添加两个属性：

```js
var state = {
  // ...
  drawPile: pile,
  discardPile: {},
}
```

`drawPile`属性是玩家可以抽取的牌堆。 它使用在`cards.js`文件中定义的`pile`对象进行初始化。 每个键都是卡片定义的 ID，值是此类型卡片在堆叠中的数量。

`discardPile`属性是`drawPile`属性的等价物，但它有不同的用途--玩家打出的所有卡片都将从他们的手中移除并放入弃牌堆中。 在某个时刻，如果抽牌堆为空，它将被弃牌堆重新填充（弃牌堆将被清空）。

# 初始手牌

游戏开始时，每个玩家都会抽取一些牌。

1.  在`utils.js`文件中，有一个函数用于抽取玩家的手牌：

```js
      drawInitialHand(player)
```

1.  在`main.js`文件中，添加一个调用`drawInitialHand`函数为每个玩家发牌的新的`beginGame`函数：

```js
      function beginGame () {
        state.players.forEach(drawInitialHand)
      }
```

1.  当应用准备就绪时，在`main.js`文件中我们的主组件的`mounted`钩子内调用此函数：

```js
 mounted () {
        beginGame()
      },
```

# 手牌

要显示当前玩家手中的卡片，我们需要在应用状态中添加一个新的 getter：

1.  在`state.js`文件中的`state`对象中添加`currentHand`的 getter：

```js
      get currentHand () {
        return state.currentPlayer.hand
      },
```

1.  我们现在可以在主模板中删除`testHand`属性，并用`currentHand`替换它： 

```js
      <hand v-if="!activeOverlay" :cards="currentHand" @card-            
      play="testPlayCard" />
```

1.  你也可以移除`main`组件上为测试目的编写的`createTestHand`方法和这个`created`钩子：

```js
      created () {
        this.testHand = this.createTestHand()
      },
```

# 出牌

出牌分为以下三个步骤：

1.  我们将卡片从玩家手中移除并将其添加到堆叠中。 这会触发卡片动画。

1.  我们等待卡片动画完成。

1.  我们应用卡片的效果。

# 不允许作弊

在游戏中，不应允许作弊。 在编写游戏逻辑时，我们应该记住这一点：

1.  让我们首先在`state.js`文件中的应用状态中添加一个新的`canPlay`属性：

```js
      var state = {
        // ...
        canPlay: false,
      }
```

这将阻止玩家在他们的回合中重复出牌--我们有很多动画和等待，所以我们不希望他们作弊。

我们将在玩家出牌时使用它来检查他们是否已经出过牌，并且还将在 CSS 中使用它来禁用手牌上的鼠标事件。

1.  因此，在主组件中添加一个 `cssClass` 计算属性，如果 `canPlay` 属性为真，则添加 `can-play` CSS 类：

```js
      computed: {
        cssClass () {
          return {
            'can-play': this.canPlay,
          }
        },
      },
```

1.  并在主模板的根 `div` 元素上添加一个动态 CSS 类：

```js
      <div id="#app" :class="cssClass">
```

# 从手牌中移除卡牌

当卡牌被打出时，它应该从当前玩家手中移除；按照以下步骤执行：

1.  在 `main.js` 文件中创建一个新的 `playCard` 函数，接受一张卡牌作为参数，检查玩家是否可以打出卡牌，然后将卡牌从手牌中移除，放入弃牌堆中使用 `utils.js` 文件中定义的 `addCardToPile` 函数：

```js
      function playCard (card) {
        if (state.canPlay) {
          state.canPlay = false
          currentPlayingCard = card

          // Remove the card from player hand
          const index = state.currentPlayer.hand.indexOf(card)
          state.currentPlayer.hand.splice(index, 1)

          // Add the card to the discard pile
          addCardToPile(state.discardPile, card.id)
        }
      }
```

我们将玩家打出的卡牌存储在 `currentPlayingCard` 变量中，因为我们稍后需要应用其效果。

1.  在主组件中，用一个新的 `handlePlayCard` 方法替换 `testPlayCard` 方法，调用 `playCard` 函数：

```js
      methods: {
        handlePlayCard (card) {
          playCard(card)
        },
      },
```

1.  别忘了在主模板中更改对 `hand` 组件的事件监听器：

```js
      <hand v-if="!activeOverlay" :cards="currentHand" @card- 
 play="handlePlayCard" />
```

# 等待卡牌过渡结束

当卡牌被打出时，也就是从手牌列表中移除时，它会触发一个离开动画。我们希望在继续之前等待它完成。幸运的是，`transition` 和 `transition-group` 组件会发出事件。

我们这里需要的是 `'after-leave'` 事件，但是每个转换阶段都对应着其他事件——`'before-enter'`、`'enter'`、`'after-enter'`等。

1.  在 `hand` 组件中，添加一个 `'after-leave'` 类型的事件监听器：

```js
      <transition-group name="card" tag="div" class="cards" @after- 
 leave="handleLeaveTransitionEnd">
```

1.  创建相应的方法，向主模板发出 `'card-leave-end'` 事件：

```js
      methods: {
        // ...
        handleLeaveTransitionEnd () {
          this.$emit('card-leave-end')
        },
      },
```

1.  在主模板中，在 `hand` 组件上添加一个 `'card-leave-end'` 类型的新事件监听器：

```js
      <hand v-if="!activeOverlay" :cards="currentHand" @card-                
      play="handlePlayCard" @card-leave-end="handleCardLeaveEnd" />
```

1.  创建相应的方法：

```js
      methods: {
        // ...

        handleCardLeaveEnd () {
          console.log('card leave end')
        },
      }
```

我们稍后会编写它的逻辑。

# 应用卡牌效果

动画播放后，将为玩家应用卡牌效果。例如，它可能增加当前玩家的食物量或减少对手的生命值。

1.  在 `main.js` 文件中，添加使用 `utils.js` 文件中定义的 `applyCardEffect` 的 `applyCard` 函数：

```js
      function applyCard () {
        const card = currentPlayingCard

        applyCardEffect(card)
      }
```

然后，我们将等待一段时间，以便玩家能够看到效果被应用，并了解正在发生的事情。然后，我们将检查至少有一名玩家是否已死亡以结束游戏（借助 `utils.js` 中定义的 `checkPlayerLost` 函数），或者继续下一回合。

1.  在 `applyCard` 函数中，添加以下相应逻辑：

```js
      // Wait a bit for the player to see what's going on
      setTimeout(() => {
        // Check if the players are dead
        state.players.forEach(checkPlayerLost)

        if (isOnePlayerDead()) {
          endGame()
        } else {
          nextTurn()
        }
      }, 700)
```

1.  现在，就在 `applyCard` 函数之后添加空的 `nextTurn` 和 `endGame` 函数：

```js
      function nextTurn () {
        // TODO
      }

      function endGame () {
        // TODO
      }
```

1.  现在我们可以在主组件中修改 `handleCardLeaveEnd` 方法，调用我们刚刚创建的 `applyCard` 函数：

```js
      methods: {
        // ...

        handleCardLeaveEnd () {
          applyCard()
        },
      }
```

# 下一个回合

`nextTurn` 函数非常简单——我们将回合计数器增加一，更改当前玩家，并显示玩家回合覆盖层。

将相应的代码添加到 `nextTurn` 函数中：

```js
function nextTurn () {
  state.turn ++
  state.currentPlayerIndex = state.currentOpponentId
  state.activeOverlay = 'player-turn'
}
```

# 新的回合

在覆盖层之后，回合开始时我们还需要一些逻辑：

1.  首先是`newTurn`函数，它隐藏了任何活动的叠加层；它要么跳过当前玩家的回合，因为有一张卡片，要么开始回合：

```js
      function newTurn () {
        state.activeOverlay = null
        if (state.currentPlayer.skipTurn) {
          skipTurn()
        } else {
          startTurn()
        }
      }
```

如果玩家的`skipTurn`属性为 true，那么他们的回合将被跳过——这个属性将由一些卡片设置。他们还有一个`skippedTurn`属性，我们需要在`last-play`叠加层中向下一个玩家显示，告诉他们对手已经跳过了上一回合。

1.  创建`skipTurn`函数，将`skippedTurn`设置为`true`，将`skipTurn`属性设置为`false`并直接进入下一轮：

```js
      function skipTurn () {
        state.currentPlayer.skippedTurn = true
        state.currentPlayer.skipTurn = false
        nextTurn()
      }
```

1.  创建`startTurn`函数，它重置了玩家的`skippedTurn`属性，并使他们在第二轮时抽一张卡片（这样他们每回合开始时都有五张卡片）：

```js
      function startTurn () {
        state.currentPlayer.skippedTurn = false
        // If both player already had a first turn
        if (state.turn > 2) {
          // Draw new card
          setTimeout(() => {
            state.currentPlayer.hand.push(drawCard())
            state.canPlay = true
          }, 800)
        } else {
          state.canPlay = true
        }
      }
```

就在这一刻，我们可以使用`canPlay`属性允许玩家打出一张卡片。

# 叠加关闭动作

现在，我们需要处理当用户点击每个叠加层时触发的动作。我们将创建一个映射，键为叠加层类型，值为触发动作时调用的函数。

1.  将其添加到`main.js`文件中：

```js
      var overlayCloseHandlers = {
        'player-turn' () {
          if (state.turn > 1) {
            state.activeOverlay = 'last-play'
          } else {
            newTurn()
          }
        },

        'last-play' () {
          newTurn()
        },
        'game-over' () {
          // Reload the game
          document.location.reload()
        },
      }
```

对于玩家回合叠加层，只有在第二轮或更多轮时才切换到`last-play`叠加层，因为在第一轮开始时，对手不会打出任何卡片。

1.  在主组件中，添加`handleOverlayClose`方法，该方法调用与当前活动叠加层对应的动作函数，并传入`activeOverlay`属性：

```js
      methods: {
        // ...
        handleOverlayClose () {
          overlayCloseHandlers[this.activeOverlay]()
        },
      },
```

1.  在叠加层组件上，添加一个`'close'`类型的事件侦听器，当用户点击叠加层时触发：

```js
      <overlay v-if="activeOverlay" :key="activeOverlay"                  
      @close="handleOverlayClose">
```

# 游戏结束！

最后，在`endGame`函数中将`activeOverlay`属性设置为`'game-over'`：

```js
function endGame () {
  state.activeOverlay = 'game-over'
}
```

如果至少有一个玩家死亡，这将显示`game-over`叠加层。

# Summary

我们的纸牌游戏结束了。我们看到了 Vue 提供的许多新功能，使我们能够轻松创建丰富和交互式的体验。然而，在本章中介绍和使用的最重要的一点是基于组件的 Web 应用程序开发方法。这有助于我们通过将前端逻辑拆分为小型、隔离和可重用的组件来开发更大的应用程序。我们介绍了如何使组件彼此通信，从父组件到子组件使用 props，从子组件到父组件使用自定义事件。我们还为游戏添加了动画和过渡（使用`<transition>`和`<transition-group>`特殊组件），使其更加生动。我们甚至在模板中操纵了 SVG，并使用特殊的`<component>`组件动态显示了一个组件。

在下一章中，我们将使用 Vue 组件文件等其他功能来设置一个更高级的应用程序，这些功能将帮助我们构建更大的应用程序。
