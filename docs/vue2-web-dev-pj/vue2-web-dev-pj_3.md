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

Now if you open the app, you should see the `'Hello world!'` text displayed. As you guessed, we won't inline the template in the `#app` element going forward.

# The app state

As explained before, the `state.js` file will help us consolidate the main data of our application in one place. That way, it will be easier to write game logic functions without polluting the definition object with a lot of methods.

1.  The `state.js` file declares a state variable that we will use as the data of our app. We can use it directly as the data option, as follows:

```js
      new Vue({
        // …
        data: state,
      })
```

Now, if you open the devtools, you should see the only data property already declared in the state object:

![](img/06ba9d2b-5ad4-4ed0-ae51-c1774600dde0.png)

The world ratio is a number representing how much we should scale the game objects to fit in the window. For example, `.6` means that the world should scale at 60% of its original size. It is computed with the `getWorldRatio` function in the `utils.js` file.

There is one thing missing, though--it is not recomputed when the window is resized. This is something we have to implement ourselves. After the Vue instance constructor, add an event listener to the window object to detect when it is resized.

2.  Inside the handler, update the `worldRatio` data property of the state. You can also display `worldRatio` in the template:

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

Try resizing your browser window horizontally--the `worldRatio` data property is updated in the Vue app.

*But wait! We are modifying the state object, not the Vue instance...*

You are right! However, we have set the Vue instance `data` property with the `state` object. This means Vue has set up reactivity on it, and we can change its attributes to update our app, as we will see in a second.

3.  To ensure that `state` is the app's reactive data, try comparing the instance data object and the global state object:

```js
      new Vue({
        // ...
        mounted () {
          console.log(this.$data === state)
        },
      })
```

These are the same objects we set with the data option. So when you do:

```js
this.worldRatio = 42
```

You are also doing this:

```js
this.$data.worldRatio = 42
```

This is, in fact, the same as follows:

```js
state.worldRatio = 42
```

This will be useful in the gameplay function that will use the state object to update the game data.

# The almighty components

Components are the building blocks that will compose our app--it's the central concept of the Vue apps. They are small parts of the view, and they should be relatively small, reusable, and as self-sufficient as possible--structuring an app with components will then help maintain and evolve it, especially if it becomes large. In fact, this is becoming the standard method for creating huge web apps in an efficient and manageable way.

In concrete terms, your app will be a giant tree of smaller components:

![](img/b20410bf-c9fc-47ea-8474-f65537e325f3.png)

For example, your app could have a form component, which could contain several input components and button components. Each one would be a very specific part of the UI, and they would be reusable all across the app. Being quite small in scope, they would be easy to understand and reason about, and thus easier to maintain (issue fixing) or to evolve.

# Building the user interface

The first components we will create are part of the UI layer. There will be a top bar with the players' names and a turn counter, the cards with their name and description, the hand with the current player cards, and the three overlays.

# Our first component - the top bar

The top bar, our first component, will be placed at the top of the page and will display the names of the two players and a turn counter in the middle. It will also show an arrow facing the name of the player currently taking their turn.

It will look like this:

![](img/8739ced8-2ec7-4a72-af0f-fa01f814c080.png)

# Adding some gameplay data to the state

Before creating the component, we need some new data properties:

*   `turn`: The number of the current turn; starts at 1
*   `players`: The array of player objects
*   `currentPlayerIndex`: The index of the current player in the `players` array

Add them in the state in the `state.js` file:

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

`Math.round(Math.random())` will use `0` or `1` randomly to choose who goes first.

We will use these properties to display the player names and the turn counter in the top bar.

# Defining and using the components

We will write our UI components in a new file:

1.  Create a `components` folder and a new `ui.js` file inside it. Include it in the main `index.html` page, just before the main script:

```js
      <!-- Scripts -->
      <script src="img/utils.js"></script>
      <script src="img/cards.js"></script>
      <script src="img/state.js"></script>
      <script src="img/ui.js"></script>
      <script src="img/main.js"></script>
```

In this file, we will register our components, so it's important that the main Vue instance is created afterward, and not before. Else, we would get errors of components not being found.

To register a component, we can use the global `Vue.component()` function. It takes two arguments; the name under which we register the component, and its definition object, which is using the exact same options as the Vue instance that we already know.

2.  Let's create the `top-bar` component in the `ui.js` file:

```js
 Vue.component('top-bar', {
        template: `<div class="top-bar">
          Top bar
        </div>`,
      })
```

Now, we can use the `top-bar` component in our templates, just like any other HTML tags, for instance, `<top-bar>`.

3.  In the main template, add a new `top-bar` tag:

```js
      new Vue({
        // ...
        template: `<div id="#app">
          <top-bar/>
        </div>`,
      })
```

This template will create a new `top-bar` component and render it inside the `#app` element, using the definition object we just defined. If you open the devtools, you should see two entries:

![](img/8ea8c881-2a6b-4eb0-be9e-4c711323bb9c.png)

Each one is a Vue instance--Vue actually created a second instance using the definition we provided for the top-bar component.

# Parent-to-child communication with Props

As we saw in the The almighty components section, our component-based app will have a tree of components, and we need them to communicate with each other. For now, we will only focus on descending, parent-to-child communication. This is accomplished with "props".

Our `top-bar` component needs to know who the players are, which one is currently playing, and what the current turn number is. So, we will need three props--`players`, `currentPlayerIndex`, and `turn`.

To add props to a component definition, use the `props` option. For now, we will simply list the names of our props. However, you should know that there is a more detailed notation with an object instead, which we will cover in the next chapters.

1.  Let's add the props to our component:

```js
      Vue.component('top-bar', {
        // ...
        props: ['players', 'currentPlayerIndex', 'turn'],
      })
```

In the parent component, which is the root application, we can set the props value the exact same way we would for HTML attributes.

2.  Go ahead and use the `v-bind` shorthand to wire the props value with the app data in the main template:

```js
      <top-bar :turn="turn" :current-player-index="currentPlayerIndex"         
      :players="players" />
```

Note that since HTML is case-insensitive and by convention, it is recommended to use the kebab-case (with dashes) names of our props, and the camel-case names in the JavaScript code.

Now, we can use the props in our `top-bar` component just like data properties. For example, you could write something like this:

```js
Vue.component('top-bar', {
  // ...
  created () {
    console.log(this.players)
  },
})
```

This would print the `players` array sent by the parent component (our app) in the browser console.

# Props in our template

We will now use the props we created in the template of the `top-bar` component.

1.  Change the `top-bar` template to display the player's name with the `players` prop:

```js
      template: `<div class="top-bar">
        <div class="player p0">{{ players[0].name }}</div>
        <div class="player p1">{{ players[1].name }}</div>
      </div>`,
```

As you can see in the preceding code, we are also using the props like we did with properties in templates. You should see the player names displayed in the app.

2.  Continue with the turn counter between `players` using the `turn` prop:

```js
      template: `<div class="top-bar">
        <div class="player p0">{{ players[0].name }}</div>
        <div class="turn-counter">
        <div class="turn">Turn {{ turn }}</div>
        </div>
        <div class="player p1">{{ players[1].name }}</div>
        </div>`,
```

In addition to the label, we also want to display a big arrow facing the current player to make it more obvious.

3.  Add the arrow image inside the `.turn-counter` element, and add a dynamic class using the `currentPlayerIndex` prop with the `v-bind` shorthand we used in Chapter 2, *Markdown Notebook*:

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

Now, the app should display the fully featured top bar, with the two players, names and the turn counter between them. You can test the Vue-automated reactivity by typing these commands into the browser console:

```js
state.currentPlayerIndex = 1
state.currentPlayerIndex = 0
```

You should see the arrow turning around to face the correct player name, which gets emphasized:

![](img/a667ef7b-5325-4e1a-bf9f-4555b64d5b38.png)

# Displaying a card

All the cards are described in the card definition objects, declared in the `cards.js` file. You can open it, but you shouldn't have to modify its content. Each card definition has the following fields:

*   `id`: Unique for each card
*   `type`: Changes the color background to help distinguish the cards from each other
*   `title`: The displayed name of the card
*   `description`: An HTML text explaining what the card does
*   `note`: An optional flavor text, in HTML too
*   `play`: The function we will call when the card is played

We need a new component to display any card, either in the player hand or in the overlay, that describes what the opponent played last turn. It will look like this:

![](img/bdcb0ac6-585b-4c0f-a2e3-fdbe7c28385b.png)

1.  In the `components/ui.js` file, create a new `card` component:

```js
 Vue.component('card', {
        // Definition here
      })
```

2.  This component will receive a `def` prop that will be the card definition object we described above. Declare it with the `props` option as we did for the `top-bar` component:

```js
      Vue.component('card', {
        props: ['def'],
      })
```

3.  Now, we can add the template. Start with the main `div` element, with the `card` class:

```js
      Vue.component('card', {
        template: `<div class="card">
        </div>`,
        props: ['def'],
      })
```

4.  To change the background color depending on the card type, add a dynamic CSS class that uses the `type` property of the card object:

```js
      <div class="card" :class="'type-' + def.type">
```

For example, if the card has the `'attack'` type, the element will get the `type-attack` class. Then, it will have a red background.

5.  Now, add the title of the card with the corresponding class:

```js
      <div class="card" :class="'type-' + def.type">
        <div class="title">{{ def.title }}</div>
      </div>
```

6.  Add the separator image, which will display some lines between the card title and the description:

```js
      <div class="title">{{ def.title }}</div>
      <img class="separator" src="img/card-separator.svg" />
```

After the image, append the description element.

Note that since the `description` property of the card object is an HTML-formatted text, we need to use the special `v-html` directive introduced in the Chapter 2, *Markdown Notebook.*

7.  Use the `v-html` directive to display the description:

```js
      <div class="description"><div v-html="def.description"></div>             
      </div>
```

You may have noted that we added a nested `div` element, which will contain the description text. This is to center the text vertically using CSS flexbox.

8.  Finally, add the card note (which is also in an HTML-formatted text). Note that some cards don't have note, so we have to use the `v-if` directive here:

```js
      <div class="note" v-if="def.note"><div v-html="def.note"></div>        
      </div>
```

The card component should now look like this:

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

Now, we can try our new card component in the main application component.

9.  Edit the main template as follows and add a `card` component just after the top bar:

```js
      template: `<div id="#app">
        <top-bar :turn="turn" :current-player-             
         index="currentPlayerIndex" :players="players" />
        <card :def="testCard" />
      </div>`,
```

10.  We also need to define a temporary computed property:

```js
 computed: {
        testCard () {
          return cards.archers
        },
      },
```

Now, you should see a red attack card with a title, description, and flavor text:

![](img/6b8c4676-e72a-41bb-915f-24b2ed0c0023.png)

# Listening to native events on components

Let's try adding a click event handler on our card:

```js
<card :def="testCard" @click="handlePlay" />
```

With a dumb method in the main component:

```js
methods: {
  handlePlay () {
    console.log('You played a card!')
  }
}
```

If you test this in the browser, you may be surprised that it doesn't work as expected. Nothing is output to the console...

This is because Vue has its own event system for components, called "custom events", that we will learn about in a moment. This system is separate from the browser events, so here Vue expects a custom `'click'` event, and not the browser one. Thus, the `handler` method is not called.

To get around this, you should use the `.native` modifier on the `v-on` directive, as follows:

```js
<card :def="testCard" @click.native="handlePlay" />
```

Now, the `handlePlay` method is called when you click on the card, as expected.

# Child-to-parent communication with custom events

Previously, we used props to communicate from a parent component to its children. Now, we would like to do the opposite and communicate from one child component to its parent. For our card component, we would like to tell the parent component that the card is being played by the player when they click on it. We can't use props here, but we can use custom events. In our components, we can emit events that can be caught by the parent component with the `$emit` special method. It takes one mandatory argument, which is the event type:

```js
this.$emit('play')
```

We can listen to the custom events inside the same Vue instance with the `$on` special method:

```js
this.$on('play', () => {
  console.log('Caught a play event!')
})
```

The `$emit` method also sends a `'play'` event to the parent component. We can listen to it in the parent component template with the `v-on` directive just like we did before:

```js
<card v-on:play="handlePlay" />
```

You can also use the `v-bind` shorthand:

```js
<card @play="handlePlay" />
```

We can also add as many arguments as we like that will get passed to the handler methods:

```js
this.$emit('play', 'orange', 42)
```

Here, we emitted a `'play'` event with the following two arguments-- `'orange'` and `42`.

In the handle, we can get them via the arguments, as follows:

```js
handlePlay (color, number) {
  console.log('handle play event', 'color=', color, 'number=', number)
}
```

The `color` argument will have the `'orange'` value and the `number` argument will have the `42` value.

Like we saw in the preceding section, custom events are completely separate from the browser event system. The special methods--`$on` and `$emit`--are not aliases to the standard `addEventListener` and `dispatchEvent`. That explains why we need the `.native` modifier on components to listen to browser events such as `'click'`.

Back to our card component, we just need to emit a very simple event to tell the parent component that the card is being played:

1.  First, add the method that will emit the event:

```js
 methods: {
        play () {
          this.$emit('play')
        },
      },
```

2.  We would like to call this method when the user clicks on the card. Just listen to a browser click event on the main card `div` element:

```js
      <div class="card" :class="'type-' + def.type" @click="play">
```

3.  We are done with the card component. To test this, listen to the `'play'` custom event in the main component template:

```js
      <card :def="testCard" @play="handlePlay" />
```

Now, the `handlePlay` method will be called whenever the `'play'` event is emitted.

We could just have listened to a native click event instead, but it's in most cases a good idea to use custom events to communicate between components. For example, we could also emit the `'play'` event when the user uses another method, such as using the keyboard to select the card and pressing *Enter*; we won't implement that method in this book though.

# The hand

Our next component will be the current player hand, holding the five cards they have. It will be animated with a 3D transition and will also be responsible for the card animations (when the card is drawn, and when it is played).

1.  In the `components/ui.js` file, add a component registration with the `'hand'` ID and a basic template, with two `div` elements:

```js
 Vue.component('hand', {
        template: `<div class="hand">
          <div class="wrapper">
            <!-- Cards -->
          </div>
        </div>`,
      })
```

The wrapper element will help us position and animate the cards.

Each card in the hand will be represented by an object. For now, it will have the following properties:

*   `id`: The card definition unique identifier
*   `def`: The card definition object

As a reminder, all the card definitions are declared in the `cards.js` file.

2.  Our hand component will receive these card objects representing the player hand via a new array prop called `cards`:

```js
      Vue.component('hand', {
        // ...
        props: ['cards'],
      })
```

3.  We can now add the card components with the `v-for` directive:

```js
      <div class="wrapper">
        <card v-for="card of cards" :def="card.def" />
      </div>
```

4.  To test our hand component, we will create in the app state a temporary property called `testHand` (in the `state.js` file):

```js
      var state = {
        // ...
        testHand: [],
      }
```

5.  Add a `createTestHand` method in the main component (in the `main.js` file):

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

6.  To test the hand, we also need this temporary `testDrawCard` method that simulates a random card draw:

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

7.  Use the `created` lifecycle hook to initialize the hand:

```js
 created () {
        this.testHand = this.createTestHand()
      },
```

`cardUid` is a unique identifier on cards drawn by the players that will be useful to identify each of the cards in the hand, because many cards can share the exact same card definition, and we will need a way to differentiate them.

8.  In the main template, add the hand component:

```js
      template: `<div id="#app">
        <top-bar :turn="turn" :current-player-           
         index="currentPlayerIndex" :players="players" />
        <hand :cards="testHand" />
      </div>`,
```

The result in your browser should look like this:

![](img/928716a8-049d-4deb-a1f5-c3827a3f597f.png)

# Animating the hand with transitions

During a game, the hand will be hidden when any overlay is shown. To make the app prettier, we will animate the hand when it is added or removed from the DOM. To do that, we will use CSS transitions together with a powerful Vue tool--the special `<transition>` component. It will help us work with CSS transitions when adding or removing elements with the `v-if` or `v-show` directives.

1.  First, add a new `activeOverlay` data property to the app state in the `state.js` file:

```js
      // The consolidated state of our app
      var state = {
        // UI
        activeOverlay: null,
        // ...
      }
```

2.  In the main template, we will show the hand component only if `activeOverlay` is not defined, thanks to the `v-if` directive:

```js
      <hand :cards="testHand" v-if="!activeOverlay" />
```

3.  Now, if you change `state.activeOverlay` to any truthy value in the browser console, the hand will disappear:

```js
      state.activeOverlay = 'player-turn'
```

4.  Also, if you set it back to `null`, the hand will be shown again:

```js
      state.activeOverlay = null
```

5.  To apply a transition when a component is added or removed by a `v-if` or `v-show` directive, surround it with a transition component like this:

```js
      <transition>
        <hand v-if="!activeOverlay" />
      </transition>
```

Note that this also works on HTML elements:

```js
<transition>
  <h1 v-if="showTitle">Title</h1>
</transition>
```

The `<transition>` special component will not appear in the DOM, like the `<template>` tag we used in Chapter 2, *Markdown Notebook*.

When the element is added to the DOM (the enter phase), the transition component will automatically apply the following CSS classes to the element:

*   `v-enter-active`: Apply the class while the enter transition is active. This class is added before the element is inserted to the DOM, and it is removed when the animation finishes. You should add some `transition` CSS properties in this class and define their duration.
*   `v-enter`: The starting state of the element. This class is added before the element is inserted, and it is removed one frame after the element is inserted. For example, you could set the opacity to `0` in this class.
*   `v-enter-to`: The target state of the element. This class is added one frame after the element is inserted, at the same time `v-enter` is removed. It is removed when the animation finishes.

When the element is being removed from the DOM (the leave phase), they are replaced by the following:

*   `v-leave-active`: Applied while the leave transition is active. This class is added when the leaving transition triggers, and it is removed after the element is removed from the DOM. You should add some `transition` CSS properties in this class and define their duration.
*   `v-leave`: The starting state of the element when being removed. This class is also added when the leaving transition triggers and is removed one frame after.
*   `v-leave-to`: The target state of the element. This class is added one frame after the leaving transition triggers, at the same time `v-leave` is removed. It is removed when the element is removed from the DOM.

During the leave phase, the element is not immediately removed from the DOM. It will be removed when the transition finishes to allow the user to see the animation.

Here is a schema that summarizes the two enter and leave phases, with the corresponding CSS classes:

![](img/6159a0e0-22b4-4c34-aef2-b7e7730b7cd2.png)The transition component will automatically detect the duration of the CSS transitions applied on the element.

6.  We need to write some CSS to make our animation. Create a new `transitions.css` file and include it in the web page:

```js
      <link rel="stylesheet" href="transitions.css" />
```

Let's try a basic fading animation first. We want to apply a CSS transition on the opacity CSS property for 1 second.

7.  To do that, use both the `v-enter-active` and `v-leave-active` classes since it will be the same animation:

```js
      .hand.v-enter-active,
      .hand.v-leave-active {
        transition: opacity 1s;
      }
```

When the hand is either being added or removed from the DOM, we want it to have an opacity of `0` (so it will be fully transparent).

8.  Use both the `v-enter` and `v-leave-to` classes to apply this full transparency:

```js
      .hand.v-enter,
      .hand.v-leave-to {
        opacity: 0;
      }
```

9.  Back to the main template, surround the hand component with a transition special component:

```js
      <transition>
        <hand v-if="!activeOverlay" :cards="testHand" />
      </transition>
```

Now, when you hide or show the hand, it will fade in and out.

10.  Since we may have to reuse this animation, we should give it a name:

```js
      <transition name="fade">
        <hand v-if="!activeOverlay" :cards="testHand" />
      </transition>
```

We have to change our CSS classes, because Vue will now use `fade-enter-active` instead of `v-enter-active`.

11.  In the `transition.css` file, modify the CSS selector to match this change:

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

Now, we can reuse this animation on any element with `<transition name="fade">`.

# A prettier animation

We will now make a more complex but better animation, with some 3D effects. In addition to the hand, we will animate the `.wrapper` element (for a 3D flip) and the `.card` elements. The cards will start being piled up and will progressively expand to their expected position in the hand. At the end, it will animate as if the player is picking up the cards from a table.

1.  Start by creating new transition CSS classes with the `'hand'` name instead of `'fade'`:

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

2.  Change the transition name in the main template too:

```js
      <transition name="hand">
        <hand v-if="!activeOverlay" :cards="testHand" />
      </transition>
```

3.  Let's animate the wrapper element. Use the CSS transform property to apply a 3D transformation to the element:

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

The right rotating axis is the horizontal one, which is `x`. This will animate the cards just as if they were being picked up by the player. Note that there is a cubic-bezier easing function defined to make the animation smoother.

4.  Finally, animate the cards themselves by setting a negative horizontal margin so that they will seem to be piled up:

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

Now, if you hide and show the hand with the browser console like we did before, it will have a nice animation.

# Playing a card

Now, we need to handle the `'play'` event in the hand component we emit in the cards when the user clicks on them, and emit a new `'card-play'` event to the main component with an additional argument--the played card in question.

1.  First, create a new method called `handlePlay`. It takes a `card` argument and emits the new event to the parent component:

```js
      methods: {
        handlePlay (card) {
          this.$emit('card-play', card)
        },
      },
```

2.  Then, add a listener to our cards for the `'play'` event:

```js
      <card v-for="card of cards" :def="card.def" 
      @play="handlePlay(card) />
```

As you can see here, we directly use the iterator variable `card` of the `v-for` loop. That way, we don't need the card component to emit its `card` item since we already know what it is.

To test the card play, we will only remove it from the hand for now.

3.  Create a new temporary method called `testPlayCard` in the main component in the `main.js` file:

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

4.  Add the event listener for the `'card-play'` event on the `hand` component in the main template:

```js
      <hand v-if="!activeOverlay" :cards="testHand" @card-play="testPlayCard" />
```

If you click on a card, it should now emit a `'play'` event to the hand component, which will then emit a `'card-play'` event to the main component. It will, in turn, remove the card from the hand, making it disappear. To help you debug this sort of use case, the devtools have an Events tab:

![](img/32fa4cee-0203-43fe-b413-eafcccce1ea5.png)

# Animating the card list

There are three missing animations for our hand--when a card is either added or removed from the player hand, and when it is moved. When the turn begins, the player will draw a card. It means that we will add a card to the hand cards list, and it will slide from the right into the hand. When a card is played, we want it to go up and grow bigger.

To animate a list of elements, we will need another special component--`<transition-group>`. It animates the children when they are added, removed, and moved. In a template, it looks like this:

```js
<transition-group>
  <div v-for="item of items" />
</transition-group>
```

Unlike the `<transition>` element, the transition group will appear in the DOM as a `<span>` element by default. You can change the HTML element with the `tag` prop:

```js
<transition-group tag="ul">
  <li v-for="item of items" />
</transition-group>
```

In the template of our `hand` component, enclose the card components with a transition group, specify the name of the transition that we will call `"card"`, and add the `"cards"` CSS class:

```js
<transition-group name="card" tag="div" class="cards">
  <card v-for="card of cards" :def="card.def" @play="handlePlay(card) />
</transition-group>
```

Before we can continue, there is one important thing missing--the children of the transition group must be identified by a unique key.

# The key special attribute

When Vue is updating a list of DOM elements in a `v-for` loop, it tries to minimize the number of operations applied to the DOM, such as adding or removing elements. This is a very efficient way of updating the DOM in most cases and can improve the performance.

In order to do this, it reuses elements as much as it can and patches the DOM only where it is needed to have the desired result. It means repeated elements will be patched in place and won't be moved if an item is added or removed from the list. However, this also means that they won't animate if we apply transitions on them.

The following is a schema of how this works:

![](img/607e2253-c68b-41da-89b6-689de2112ef8.png)

In this schema, we remove the third item in the list, which is `c`. However, the third `div` element will not be destroyed--it will be reused with the fourth item in the list, which is `d`. Actually, this is the fourth `div` element that is destroyed.

Fortunately, we can tell Vue how each element is identified so that it can reuse and reorder them. To do that, we will need to specify a unique identifier with the `key` special attribute. For example, each of our items could have a unique ID that we would use as the key:

![](img/8daeec3d-58b6-44e3-a822-86c57e8e518b.png)

Here, we specify keys so that Vue knows the third `div` element should be destroyed and the fourth div element moved.

The key special attribute works like a standard attribute, so we need to use the `v-bind` directive if we want to assign a dynamic value to it.

Back to our cards, we can use the unique identifier on the cards as the key:

```js
<card v-for="card of cards" :def="card.def" :key="card.uid" @play="handlePlay(card) />
```

Now, if we add, move, or delete a card item in the JavaScript, it will be reflected with the right order in the DOM.

# The CSS transitions

Like before, we have the following six CSS classes at our disposable, prefixed with the name of our group transition, `'card'`: `card-enter-active`, `card-enter`, `card-enter-to`, `card-leave-active`, `card-leave`, and `card-leave-to`. They will be applied to the direct children of the group transition, that is, our cards components.

1.  The group transition has an additional class applied to moving items--`v-move`. Vue will use the CSS `transform` property on the items to make them move, so we just need to apply a CSS transition on it with at least a duration:

```js
      .card-move {
        transition: transform .3s;
      }      
```

Now, when you click on a card to play it, it should disappear and the remaining cards will move to their new position. You can also add cards to the hand.

2.  Select the main component in the Vue devtools and execute this into the browser console:

```js
      state.testHand.push($vm.testDrawCard())
```

Selecting a component in the devtools exposes it in the browser console as `$vm`.

Like we did for the hand, we will also add animations for the cards when they enter the hand, and when they are played (and thus leave the hand).

3.  Since we need to transition multiple CSS properties on the card with the same timings all the time (except during the leave transition), we will change the `.card-move` rule we just wrote into this:

```js
 .card {
        /* Used for enter, move and mouse over animations */
        transition: all .3s;
      }
```

4.  For the enter animation, specify the state of the card for the start of the transition:

```js
 .card-enter {
        opacity: 0;
        /* Slide from the right */
        transform: scale(.8) translateX(100px);
      }
```

5.  The leave animation requires a few more rules since the play card animation is more complex, and involves zooming the card upward:

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

This is enough to make your cards all properly animated. You can try playing and adding cards to the hand again to see the result.

# The overlays

The last UI elements we need are the overlays. The following are three of them:

*   The 'new turn' overlay shows the name of the current player when it is their turn. Clicking on the 'new turn' player switches to the 'last play' overlay.
*   The 'last play' overlay shows the player what their opponent did just before. It displays either of the following:
    *   The card played by the opponent during the preceding turn
    *   A reminder that their turn was skipped
*   The 'game over' overlay shows whenever a player or both players lose. It displays the names of the players with the phrase "is victorious" or "is defeated". Clicking on the 'game over' overlay reloads the game.

All of these overlays have two things in common--they do something when the user clicks on them, and they have a similar layout design. So, we should be smart here and structure our components to reuse code as much as we can where it makes sense. The idea here is to create a generic overlay component, which will take care of the click event and the layout and three specific overlay-content components for each one of the overlays we need.

Before starting, add a new `activeOverlay` property to the app state in the `state.js` file:

```js
// The consolidated state of our app
var state = {
  // UI
  activeOverlay: null,
  // ...
}
```

This will hold the name of the currently displayed overlay or will be `null` if no overlay is shown.

# Content distribution with slots

It would be very convenient if we could put contents inside the overlay component in the main template, like this:

```js
<overlay>
  <overlay-content-player-turn />
</overlay>
```

We would encapsulate additional layout and logic inside the `overlay` component while still being able to put any content inside. This is done through a special element--the `<slot>`.

1.  Let's create our `overlay` component with two `div` elements:

```js
 Vue.component('overlay', {
        template: `<div class="overlay">
          <div class="content">
            <!-- Our slot will be there -->
          </div>
        </div>`,
      })
```

2.  Add a click event listener on the `.overlay` div, which calls the `handleClick` method:

```js
      <div class="overlay" @click="handleClick">
```

3.  Then, add the mentioned method where we emit a custom `'close'` event:

```js
      methods: {
        handleClick () {
          this.$emit('close')
        },
      },
```

This event will be helpful to know when to switch from one overlay to the next at the start of the turn.

4.  Now, put a `<slot>` element inside the `.content` div:

```js
      template: `<div class="overlay" @click="handleClick">
        <div class="content">
          <slot />
        </div>
      </div>`,
```

Now, if we put something between the `overlay` tags when using our component, it will be included in the DOM and replace the `<slot>` tag. For example, we could do this:

```js
<overlay>
  Hello world!
</overlay>
```

Also, it will render like this in the page:

```js
<div class="overlay">
  <div class="content">
    Hello world!
  </div>
</div>
```

It works with anything, so you can also put HTML or Vue components, and it will still work the same way!

5.  The component is ready to be used in the main template, so add it at the end:

```js
      <overlay>
        Hello world!
      </overlay>
```

Each of the three overlay contents will be a separate component:

*   `overlay-content-player-turn` shows the beginning of the turn
*   `overlay-content-last-play` displays the last card played by the opponent
*   `overlay-content-game-over` shows when the game is over

Before diving into these, we need a bit more data about the two players in our state.

6.  Go back to the `state.js` file and add the following properties for each player:

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

You should now have two items in the `players` array with the same properties, expect for the player names.

# The 'player turn' overlay

The first overlay will display two different messages to the current player, depending on whether it is skipping their turn or not. The player prop will receive the current player so that we can access its data. We will use a `v-if` directive paired with a `v-else` directive and the `skipTurn` property we just added to the players:

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

# The 'last play' overlay

This one is a bit more complex. We need a new function to get the last played card by a player. In the `utils.js` file, add the new `getLastPlayedCard` function:

```js
function getLastPlayedCard (player) {
  return cards[player.lastPlayedCardId]
}
```

We can now use this function in a `lastPlayedCard` computed property by passing the `opponent` prop:

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

Note that we are directly reusing the `card` component we made earlier to display the card.

# The 'game over' overlay

For this one, we will create another component called `player-result` that will show whether a player is victorious or defeated. We will display the name of the player passed with a prop. We will compute the result for this player with a computed property, which we will also use as a dynamic CSS class:

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

Now, we can create the game over overlay by looping over the `players` props and using the `player-result` component:

```js
Vue.component('overlay-content-game-over', {
  template: `<div>
    <div class="big">Game Over</div>
    <player-result v-for="player in players" :player="player" />
  </div>`,
  props: ['players'],
})
```

# Dynamic component

Now, it is time to put all of these into our overlay component and use the `activeOverlay` property we defined earlier.

1.  Add the components and display them with the corresponding value of `activeOverlay` in the main template:

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

We will remove the overlay completely if the `activeOverlay` property is equal to `null`.

Before adding the props, we will need to modify the app state in the `state.js` file with a few getters.

2.  The first one will return the `player` object from the `currentPlayerIndex` property:

```js
      get currentPlayer () {
        return state.players[state.currentPlayerIndex]
      },
```

3.  The second one will return the opposing `player` index:

```js
      get currentOpponentId () {
        return state.currentPlayerIndex === 0 ? 1 : 0
      },
```

4.  Finally, the third one will return the corresponding player object:

```js
      get currentOpponent () {
        return state.players[state.currentOpponentId]
      },
```

5.  Now, we can add the props to the overlay contents:

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

You can test the overlays by setting the `activeOverlay` property in the browser console:

```js
state.activeOverlay = 'player-turn'
state.activeOverlay = 'last-play'
state.activeOverlay = 'game-over'
state.activeOverlay = null
```

If you want to test the `last-play` overlay, you need to specify a valid value to the player `lastPlayedCardId` property, such as `'catapult'` or `'farm'`.

Our code is starting to be messy, with three conditionals. Thankfully, there is a special component that can turn itself into any component--it is the `component` component. You just have to set its `is` prop to a component name, a component definition object, or even an HTML tag, and it will morph into it:

```js
<component is="h1">Title</component>
<component is="overlay-content-player-turn" />
```

It's a prop like any other, so we can use the `v-bind` directive to dynamically change the very nature of the component with a JavaScript expression. What if we used our `activeOverlay` property to do just that? Are our overlay content components conveniently named with the same `'over-content-'` prefix? Take a look:

```js
<component :is="'overlay-content-' + activeOverlay" />
```

That's it. Now, by changing the value of the `activeOverlay` property, we will change the component displayed inside the overlay.

6.  After adding back the props, the overlay should look like this in the main template:

```js
      <overlay v-if="activeOverlay">
        <component :is="'overlay-content-' + activeOverlay"
          :player="currentPlayer" :opponent="currentOpponent"
          :players="players" />
      </overlay>
```

Don't worry, unused props won't interfere with the different overlays workings.

# The overlay animation

Like we did with the hand, we will use a transition to animate the overlay.

1.  Add a transition called "zoom" around the overlay component:

```js
      <transition name="zoom">
        <overlay v-if="activeOverlay">
          <component :is="'overlay-content-' + activeOverlay"                    
          :player="currentPlayer" :opponent="currentOpponent"                      
          :players="players" />
        </overlay>
      </transition>
```

2.  Add the following CSS rules in the `transition.css` file:

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

This is a simple animation that will zoom out the overlay while fading it out.

# Key attribute

For now, if you try the animation in the browser, it should only work in two cases:

*   When you don't have any overlay displayed, and you set one
*   When you have an overlay shown and you set `activeOverlay` to `null` to hide it

If you switch between the overlays, the animation will not work. This is because of the way Vue updates the DOM; as we saw earlier in the *The key special attribute* section, it will reuse DOM elements as much as possible to optimize performance. In that case, we will need to use the key special attribute to give Vue a hint that we would like to treat the different overlays as separate elements. So, when we transition from one overlay to the other, both will be present in the DOM, and the animations can be played.

Let's add the key to our overlay component so that Vue will treat it as multiple separate elements when we change the `activeOverlay` value:

```js
<transition name="zoom">
  <overlay v-if="activeOverlay" :key="activeOverlay">
    <component :is="'overlay-content-' + activeOverlay" :player="currentPlayer" :opponent="currentOpponent" :players="players" />
  </overlay>
</transition>
```

Now, if we set `activeOverlay` to `'player-turn'`, the overlay will have a key of `'player-turn'`. Then, if we set `activeOverlay` to `'last-play'`, an entirely new overlay will be created with a key of `'last-play'`, and we can animate the transition between the two. You can try this in the browser by setting different values to `state.activeOverlay`.

# The overlay background

At this point, there is something missing--the overlay background. We can't include it inside the overlay component because it would be zoomed during the animation--this would be quite awkward. Instead, we will use the simple `fade` animation we have created already.

In the main template, add a new `div` element with the `overlay-background` class just before the `zoom` transition and the `overlay` component:

```js
<transition name="fade">
  <div class="overlay-background" v-if="activeOverlay" />
</transition>
```

With the `v-if` directive, it will only be displayed when any overlay is displayed.

# Game world and scenery

We are mostly done with the UI elements, so we can now go into the game scenery components. We will have some new components to do--the player castles, a health and food bubble for each one, and some animated clouds in the background for good measure.

Create a new `world.js` file in the `components` folder, and include it in the page:

```js
<!-- ... -->
<script src="img/ui.js"></script>
<script src="img/world.js"></script>
<script src="img/main.js"></script>
```

We will start with the castles.

# The castles

This one is actually pretty simple since it consists of only two images and a castle-banners component that will take care of the health and food display:

1.  In the `world.js` file, create a new castle component with two images that accepts a `players` and an `index` prop:

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

For this component, there is a castle and a ground image for each player; that means four images in total. For example, for the player at index `0`, there are `castle0.svg` and the `ground0.svg` images.

2.  In the main template, just below the `top-bar` component, create a new `div` element with the `world` CSS class, loop over the players to display the two castles, and add another `div` element with the `land` class:

```js
      <div class="world">
        <castle v-for="(player, index) in players" :player="player"                 
         :index="index" />
        <div class="land" />
      </div>
```

In the browser, you should see one castle for each player, as follows:

![](img/fb8e96b4-1277-46dd-b2e7-8fe28baddcad.png)

# Castle banners

The castle banners will display the health and food for the castle. There will be two components inside the `castle-banners` component:

*   A vertical banner whose height changes, depending of the amount of the stat
*   A bubble with the actual number displayed

It will look like this:

![](img/9673ab25-0535-4f51-a154-facc7e9087d3.png)

1.  First, create a new `castle-banners` component with only the stat icons and a `player` prop:

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

2.  We also need two computed properties that calculate the health and food ratios:

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

The `maxFood` and `maxHealth` variables are defined at the beginning of the `state.js` file.

3.  In the `castle` component, add the new `castle-banners` component:

```js
      template: `<div class="castle" :class="'player-' + index">
        <img class="building" :src="img/castle' + index + '.svg'" />
        <img class="ground" :src="img/ground' + index + '.svg'" />
        <castle-banners :player="player" />
      </div>`,
```

# Food and health bubbles

This component contains an image and a text that displays the current amount for either the food or health of the castle. Its position will change depending on this amount--it will go up as the amount diminishes and will go down when it replenishes.

We will need three props for this component:

*   `type` is either food or health; it will used for the CSS class and for the image path
*   `value` is the amount displayed in the bubble
*   `ratio` is the amount divided by the maximum amount

We also need a computed property to calculate the vertical position of the bubble with the `ratio` prop. The position will range from 40 pixels to 260 pixels. So, the position value will be given by this expression:

```js
(this.ratio * 220 + 40) * state.worldRatio + 'px'
```

Remember to multiply every position or size with the `worldRatio` value, so the game takes into account the window size (it gets bigger if the window is bigger, or vice versa).

1.  Let's write our new `bubble` component:

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

It has a root `div` element with the `stat-bubble` CSS class, a dynamic class (either `'food-bubble'` or `'health-bubble'`, depending on the `type` prop value) plus a dynamic CSS style we set with the `bubbleStyle` computed property.

It contains an SVG image, which is not the same for food and health, and a `div` element with the `counter` class that displays the amount.

2.  Add a food and an health bubble to the `castle-banners` component:

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

# Banner bars

The other component we need is a vertical banner hanging up on one of the castle's towers. Its length will change depending on the amount of food or health. This time, we will create a dynamic SVG template so that we can modify the height of the banner.

1.  First, create the component with two props (the color and the ratio) and the `height` computed property:

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

For now, we defined our templates in two different ways--we either used the HTML of our page or we set a string into the `template` option of our components. We will use another method of writing component templates--a special script tag in the HTML. It works by writing the template inside this script tag with a unique ID and referencing this ID when defining the component.

2.  Open the `banner-template.svg` file, which contains the SVG markup of the banner image we will use as a dynamic template. Copy the content of the file.
3.  In the `index.html` file, after the `<div id="app">` element, add a `script` tag with the `text/x-template` type and the `banner` ID, and paste the `svg` content inside:

```js
      <script type="text/x-template" id="banner">
        <svg viewBox="0 0 20 260">
          <path :d="`m 0,0 20,0 0,${height} -10,-10 -10,10 z`"                    
          :style="`fill:${color};stroke:none;`" />
        </svg>
      </script>
```

As you can see, this is a standard template with all the syntax and directives available to use. Here, we use the `v-bind` directive shorthand twice. Note that you can use SVG markup inside all of your Vue templates.

4.  Now, back in our component definition, add the `template` option with the ID of our script tag template preceded by a hashtag:

```js
      Vue.component('banner-bar', {
        template: '#banner',
        // ...
      })
```

Done! The component will now look up for a scrip tag template with the `banner` ID in the page and will use it as its template.

5.  In the `castle-banners` component, add the two remaining `banner-bar` components with the corresponding colors and ratios:

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

You should now see the banners that hang up on the castles and shrink if you change the food and health values.

# Animating a value

These banners would be prettier if we could animate them when they shrink or grow. We can't rely on CSS transitions since we need to dynamically change the SVG path, so we need another way--we will animate the value of the `height` property used in the template.

1.  First, let's rename our template computed property to `targetHeight`:

```js
      computed: {
        targetHeight () {
          return 220 * this.ratio + 40
        },
      },
```

This `targetHeight` property will be calculated only once whenever the ratio changes.

2.  Add a new `height` data property that we will be able to animate each time `targetHeight` changes:

```js
 data () {
        return {
          height: 0,
        }
      },
```

3.  Initialize the value of `height` with the value of `targetHeight` when the component has been created. Do this in the `created` hook:

```js
 created () {
        this.height = this.targetHeight
      },
```

To animate the height value, we will use the popular `**TWEEN.js**` library, which is already included in the `index.html` file. This library works by creating a new `Tween` object that takes the starting values, an easing function, and the ending values. It provide callbacks such as `onUpdate` that we will use to update the `height` property from the animation.

4.  We would like to start the animation whenever the `targetHeight` property changes, so add a watcher with the following animation code:

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

The `this` context in the `onUpdate` callback is the `Tween` object and not the Vue component instance. That's why we need a good old temporary variable to hold the component instance `this` (here, that is the `vm` variable).

5.  We need one last thing to make our animation work. In the `main.js` file, request the paint frames from the browser to make the `TWEEN.js` library tick, thanks to the browser's `requestAnimationFrame` function:

```js
      // Tween.js
      requestAnimationFrame(animate);

      function animate(time) {
        requestAnimationFrame(animate);
        TWEEN.update(time);
      }
```

If the tab is in the background, the `requestAnimationFrame` function will wait for the tab to become visible again. This means the animations won't play if the user doesn't see the page, saving the computer resources and battery. Note that it is also the case for CSS transitions and animations.

Now when you change the food or the health of a player, the banners will progressively shrink or grow.

# The animated clouds

To add some life to the game world, we will create a few clouds that will slide in the sky. Their position and animation duration will be random and they will go from the left to the right of the window.

1.  In the `world.js file`, add the minimum and maximum durations for the cloud animation:

```js
      const cloudAnimationDurations = {
        min: 10000, // 10 sec
        max: 50000, // 50 sec
      }
```

2.  Then, create the cloud component with an image and a `type` prop:

```js
 Vue.component('cloud', {
        template: `<div class="cloud" :class="'cloud-' + type" >
          <img :src="img/strong> + '.svg'" />
        </div>`,
        props: ['type'],
      })
```

There will be five different clouds, so the `type` prop will range from 1 to 5.

3.  We will need to change the `z-index` and `transform` CSS properties of the component with a reactive `style` data property:

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

4.  Apply these style properties with the `v-bind` directive:

```js
      <div class="cloud" :class="'cloud-' + type" :style="style">
```

5.  Let's create a method to set the position of the cloud component using the `transform` CSS property:

```js
      methods: {
        setPosition (left, top) {
          // Use transform for better performance
          this.style.transform = `translate(${left}px, ${top}px)`
        },
      }
```

6.  We need to initialize the horizontal position of the cloud when the image is loaded, so that it's outside of the viewport. Create a new `initPosition` that uses the `setPosition` method:

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

7.  Add an event listener on the image with the `v-on` directive shorthand that listens to the `load` event and calls the `initPosition` method:

```js
      <img :src="img/cloud' + type + '.svg'" @load="initPosition" />
```

# The animation

Now, let's move on to the animation itself. Like we did for the castle banners, we will use the `TWEEN.js` library:

1.  First, create a new `startAnimation` method that calculates a random animation duration and accepts a delay parameter:

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

The faster a cloud is, the lower its animation duration will be. Faster clouds will be displayed before slower clouds, thanks to the `z-index` CSS property.

2.  Inside the `startAnimation` method, calculate a random vertical position for the cloud and then create a `Tween` object. It will animate the horizontal position with a delay and set the position of the cloud each time it updates. When it completes, we will start another animation with a random delay:

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

3.  In the `mounted` hook of the component, call the `startAnimation` method to begin the initial animation (with a random delay):

```js
 mounted () {
        // We start the animation with a negative delay
        // So it begins midway
        this.startAnimation(-Math.random() *                   
      cloudAnimationDurations.min)
      },
```

Our cloud component is ready.

4.  Add some clouds to the main template in the `world` element:

```js
      <div class="clouds">
        <cloud v-for="index in 10" :type="(index - 1) % 5 + 1" />
      </div>
```

Be careful to pass a value to the `type` prop ranging from 1 to 5\. Here, we use the `%` operator to return the division remainder for 5.

Here is what it should look like:

![](img/52782d12-755c-4eb2-bbe8-811741339acf.png)

# Gameplay

All of our components are done! We only need to add some gameplay logic for the app to be playable. When the game begins, each players draws their initial hand of cards.

Then, each player's turn follows these steps:

1.  The `player-turn` overlay is displayed so that the player knows it's their turn.
2.  The `last-play` overlay shows them what the other player played during the last run.
3.  The player plays a card by clicking on it.
4.  The card is removed from their hand and its effects applied.
5.  We wait a bit so that the player can see these effects in action.
6.  Then, the turn ends, and we switch the current player to the other one.

# Drawing cards

Before drawing the cards, we will need to add two properties to the app state in the `state.js` file:

```js
var state = {
  // ...
  drawPile: pile,
  discardPile: {},
}
```

The `drawPile` property is the pile of cards that can be drawn by the players. It is initialized with the `pile` object defined in the `cards.js` file. Each key is the ID of a card definition, and the value is the amount of cards of this type in the pile.

The `discardPile` property is the equivalent of the `drawPile` property, but it serves a different purpose--all the cards played by the player will be removed from their hand and put into the discard pile. At some point, if the draw pile is empty, it will be refilled with the discard pile (which will be emptied).

# The initial hand

At the beginning of the game, each player draws some cards.

1.  In the `utils.js` file, there is a function that draws the hand of a player:

```js
      drawInitialHand(player)
```

2.  In the `main.js` file, add a new `beginGame` function that calls the `drawInitialHand` function for each player:

```js
      function beginGame () {
        state.players.forEach(drawInitialHand)
      }
```

3.  Call this inside the `mounted` hook of our main component in the `main.js` file, when the app is ready:

```js
 mounted () {
        beginGame()
      },
```

# The hand

To display the cards in the current player hand, we need a new getter in the app state:

1.  Add the `currentHand` getter to the `state` object in the `state.js` file:

```js
      get currentHand () {
        return state.currentPlayer.hand
      },
```

2.  We can now remove the `testHand` property and replace it with `currentHand` in the main template:

```js
      <hand v-if="!activeOverlay" :cards="currentHand" @card-            
      play="testPlayCard" />
```

3.  You can also remove the `createTestHand` method and this `created` hook we wrote on the main component for testing purposes:

```js
      created () {
        this.testHand = this.createTestHand()
      },
```

# Playing a card

Playing the card is split into the following three steps:

1.  We remove the card from the player's hand and add it to the pile. This triggers the card animation.
2.  We wait for the card animation to finish.
3.  We apply the effect of the card.

# No cheating allowed

When playing, cheating shouldn't be allowed. When writing the gameplay logic, we should keep this in mind:

1.  Let's start by adding a new `canPlay` property to the app state in the `state.js` file:

```js
      var state = {
        // ...
        canPlay: false,
      }
```

This will prevent the player from playing a card, if it has been already played during their turn--we have a lot of animation and waiting going on, so we don't want them to cheat.

We will use it both when a player plays a card to check whether they played one already, and also in the CSS to disable mouse events on the hand cards.

2.  So, add a `cssClass` computed property in the main component that will add the `can-play` CSS class if the `canPlay` property is true:

```js
      computed: {
        cssClass () {
          return {
            'can-play': this.canPlay,
          }
        },
      },
```

3.  And add a dynamic CSS class on the root `div` element in the main template:

```js
      <div id="#app" :class="cssClass">
```

# Removing the card from the hand

When the card is played, it should be removed from the current player hand; follow these steps to do so:

1.  Create a new `playCard` function in the `main.js` file that takes a card as an argument, checks whether the player can play a card, and then removes the card from their hand to put it into the discard pile with the `addCardToPile` function (defined in the `utils.js` file):

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

We store the card the player played in the `currentPlayingCard` variable, because we need to apply its effect later.

2.  In the main component, replace the `testPlayCard` method with a new `handlePlayCard` one that calls the `playCard` function:

```js
      methods: {
        handlePlayCard (card) {
          playCard(card)
        },
      },
```

3.  Don't forget to change the event listener on the `hand` component in the main template:

```js
      <hand v-if="!activeOverlay" :cards="currentHand" @card- 
 play="handlePlayCard" />
```

# Waiting for the card transition to end

When the card is played, which means removed from the hand card list, it triggers a leaving animation. We would like to wait for it to finish before continuing. Fortunately, the `transition` and `transition-group` components emit events.

The one we need here is the `'after-leave'` event, but there are other events corresponding to each phase of the transitions--`'before-enter'`, `'enter'`, `'after-enter'`, and so on.

1.  In the `hand` component, add an event listener of the type `'after-leave'`:

```js
      <transition-group name="card" tag="div" class="cards" @after- 
 leave="handleLeaveTransitionEnd">
```

2.  Create the corresponding method that emits a `'card-leave-end'` event to the main template:

```js
      methods: {
        // ...
        handleLeaveTransitionEnd () {
          this.$emit('card-leave-end')
        },
      },
```

3.  In the main template, add a new event listener of the `'card-leave-end'` type on the `hand` component:

```js
      <hand v-if="!activeOverlay" :cards="currentHand" @card-                
      play="handlePlayCard" @card-leave-end="handleCardLeaveEnd" />
```

4.  Create the corresponding method:

```js
      methods: {
        // ...

        handleCardLeaveEnd () {
          console.log('card leave end')
        },
      }
```

We will write its logic a bit later.

# Applying the card effect

After the animation is played, the card effects will be applied to the players. For example, it could increase the current player's food or decrease the opponent's health.

1.  In the `main.js` file, add the `applyCard` function that uses the `applyCardEffect` defined in the `utils.js` file:

```js
      function applyCard () {
        const card = currentPlayingCard

        applyCardEffect(card)
      }
```

Then, we will wait for some time so that the player can see the effects being applied and understand what is going on. Then, we will check whether at least one player is dead to end the game (thanks to the `checkPlayerLost` function defined in `utils.js`) or continue to the next turn.

2.  In the `applyCard` function, add the following corresponding logic:

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

3.  For now, add the empty `nextTurn` and `endGame` functions just after the `applyCard` one:

```js
      function nextTurn () {
        // TODO
      }

      function endGame () {
        // TODO
      }
```

4.  We can now change the `handleCardLeaveEnd` method in the main component to call the `applyCard` function we just created:

```js
      methods: {
        // ...

        handleCardLeaveEnd () {
          applyCard()
        },
      }
```

# The next turn

The `nextTurn` function is quite simple--we will increment the turn counter by one, change the current player, and display the player-turn overlay.

Add the corresponding code into the `nextTurn` function:

```js
function nextTurn () {
  state.turn ++
  state.currentPlayerIndex = state.currentOpponentId
  state.activeOverlay = 'player-turn'
}
```

# New turn

We also need some logic when a turn begins after the overlays:

1.  First is the `newTurn` function that hides any active overlay; it either skips the turn of the current player because of a card or starts the turn:

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

A player will have their turn skipped if its `skipTurn` property is true--this property will be set by some of the cards. They also have a `skippedTurn` property, which we will need to show the next player that their opponent has skipped their last turn in the `last-play` overlay.

2.  Create the `skipTurn` function that sets `skippedTurn` to `true` and the `skipTurn` property to `false` and go directly to the next turn:

```js
      function skipTurn () {
        state.currentPlayer.skippedTurn = true
        state.currentPlayer.skipTurn = false
        nextTurn()
      }
```

3.  Create the `startTurn` function, which reset the `skippedTurn` property of the player and makes them draw a card if it's their second turn (so that they always have five cards at the beginning of their turn):

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

It is at this moment that we can allow the player to play a card using the `canPlay` property.

# Overlay close actions

Now, we will need to handle the action triggered when the user clicks on each overlay. We will create a map, with the key being the type of overlay and the value a function called when the action is triggered.

1.  Add it in the `main.js` file:

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

For the player-turn overlay, we only switch to the `last-play` overlay if it's the second or more turn, since at the start of the very first turn, the opponent does not play any card.

2.  In the main component, add the `handleOverlayClose` method that calls the action function corresponding to the currently active overlay with the `activeOverlay` property:

```js
      methods: {
        // ...
        handleOverlayClose () {
          overlayCloseHandlers[this.activeOverlay]()
        },
      },
```

3.  On the overlay component, add an event listener of the `'close'` type that will be triggered when the user clicks on the overlay:

```js
      <overlay v-if="activeOverlay" :key="activeOverlay"                  
      @close="handleOverlayClose">
```

# Game Over!

Finally, set the `activeOverlay` property to `'game-over'` inside the `endGame` function:

```js
function endGame () {
  state.activeOverlay = 'game-over'
}
```

如果至少有一个玩家死亡，这将显示`game-over`叠加层。

# Summary

我们的纸牌游戏结束了。我们看到了 Vue 提供的许多新功能，使我们能够轻松创建丰富和交互式的体验。然而，在本章中介绍和使用的最重要的一点是基于组件的 Web 应用程序开发方法。这有助于我们通过将前端逻辑拆分为小型、隔离和可重用的组件来开发更大的应用程序。我们介绍了如何使组件彼此通信，从父组件到子组件使用 props，从子组件到父组件使用自定义事件。我们还为游戏添加了动画和过渡（使用`<transition>`和`<transition-group>`特殊组件），使其更加生动。我们甚至在模板中操纵了 SVG，并使用特殊的`<component>`组件动态显示了一个组件。

在下一章中，我们将使用 Vue 组件文件等其他功能来设置一个更高级的应用程序，这些功能将帮助我们构建更大的应用程序。
