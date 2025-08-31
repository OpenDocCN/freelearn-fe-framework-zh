# 第十三章。Redux 和 React

在上一章中，我们深入探讨了 Flux 作为架构。我们看到了在组件间共享数据时出现的问题。我们看到了这个架构的不同部分——动作、商店、视图和分发器——并基于我们的纯 Flux 示例、Facebook 的分发器和 EventEmitter 进行了构建。最后，我们构建了一个简单的应用来查看所有这些组件是如何结合在一起以创建一个简单的流程，在组件间共享公共状态。

在本章中，我们将探讨在流行的基于 Flux 的状态管理实现中如何使用 Flux。我们将看到它与之前看到的纯 Flux 实现有何不同。我们将查看 Redux 的不同组件——它的商店、动作和 reducer。最后，我们将看到应用如何与商店连接，维护商店的单个状态，并在视图中传递信息。

在本章中，我们将涵盖以下主题：

+   Redux

+   设置 Redux

+   Redux 商店

+   Reducers

+   将商店连接到应用组件

+   Redux 中的数据流

在本章结束时，我们将能够开始在应用中使用 Redux 来维护视图的状态。我们将能够设置它并与应用的各个部分连接。我们将能够看到如何在商店中分配数据，并使用 reducer 和 actions 来管理商店数据。

# Redux

"早上好，肖恩，"迈克开始说道。

"早上好，迈克。我们今天要做什么？"

"啊，昨天，我们使用了 Flux。这是为了向您介绍 Flux 的基础。在我们的大多数项目中，我们使用了类似的方法来管理应用的状态。"

"今天，我们将看到如何使用 Redux。"

"酷。"

"正如在[`github.coktreactjs/redux`](https://github.coktreactjs/redux)中所述，*Redux 是 JavaScript 应用的可预测状态容器*。它有点像我们之前实现的那样。"

"使用 Redux，我们为整个应用维护一个单一的状态树，并添加 reducer 来增强商店的状态。之前，我们直接修改`_state`的值，然后通知订阅者关于变化。让我们看看我们的应用设置，如下开始："

`├── actions`

`│ └── social.js`

`├── components`

`│ └── SocialTracker.js`

`├── config.js`

`├── containers`

`│ └── App.js`

`├── reducers`

`│ ├── index.js`

`│ └── social.js`

`├── server.js`

`├── store`

`│ └── configureStore.js`

`├── styles`

`│ └── App.css`

`├── utils`

"这是基于我们之前的应用和来自[`github.acktreactjs/redux/tree/master/examples/todomvc`](https://github.acktreactjs/redux/tree/master/examples/todomvc)的示例。"

"嗯，我没有在这里看到分发器。"

"正确。Redux 与正常的 Flux 不同；在这里，它没有分发器。在设置过程中，我们将看到如何将商店连接到组件，以便组件能够获取商店状态变化的更新。"

"明白了。那我们就像以前一样开始吧？"

"是的。"

"让我们从我们的主组件开始；这将通过 Redux 进行包装以监听存储，如下所示："

```js
      // App.js
import { bindActionCreators } from 'redux'
import { connect } from 'react-redux'
import SocialTracker from '../components/SocialTracker'
import * as SocialActions from '../actions/social'

function mapStateToProps(state) {
  return {
    social: state.social
  }
}

function mapDispatchToProps(dispatch) {
  return bindActionCreators(SocialActions, dispatch)
}

export default connect(mapStateToProps, mapDispatchToProps)(SocialTracker)
```

# 设置 Redux

"现在，这里有一些不同的事情发生来设置我们的存储。让我们逐一来看："

+   `mapStateToProps`：我们使用这个方法来定义我们将如何将 Redux 存储的状态映射到发送给连接到存储的组件的 props。每当存储中发生新的变化时，组件会收到通知，并通过此方法传递给对象有效载荷。

+   `mapDispatchToProps`：这个方法用于映射动作并将它们传递到 props 上，以便它们可以在组件内部使用。

+   `bindActionCreators`：这用于将我们的动作创建器（`SocialActions`）包装成分发调用，以支持直接调用动作。这有助于调用动作并通知存储进行更新，这些更新是由于分发而进行的。

+   `connect`：最后，我们有`connect`调用。这实际上将 React 组件连接到存储。它不会改变原始组件，而是增强并创建一个新的组件。然后我们可以开始使用组件中的动作。

"明白了。所以，我们创建了两个方法来映射 Redux 中的动作和状态如何提供给组件。然后我们将存储连接到 Redux 以监听更新，并在存储有更新时将动作和存储提供给组件。"

"明白了。然后我们将开始在 index 中使用这个设置，如下面的代码所示："

```js
import React from 'react'
import { render } from 'react-dom'
import { Provider } from 'react-redux'
import App from './containers/App'
import configureStore from './store/configureStore'

const store = configureStore()

render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
)
```

"来自`react-redux`模块的`Provider`组件允许我们将组件连接到存储。它接受一个我们设置的初始存储状态。`Provider`组件使这个存储对连接到存储的组件可用。这就是我们在上一个文件中通过连接到存储所做的事情。"

"明白了。这就是*Redux 作为一个单一存储*出现的地方，对吧？我注意到我们有一个完整的应用程序将要使用的单一存储。"

"是的。"

"最后，我们将通过定义传递给`<Provider>`标签的存储来完成我们的设置，如下所示："

```js
import { createStore, applyMiddleware } from 'redux'
import thunk from 'redux-thunk'
import reducer from '../reducers'

const createStoreWithMiddleware = applyMiddleware(
  thunk
)(createStore)

export default function configureStore(initialState) {
  const store = createStoreWithMiddleware(reducer, initialState)

  if (module.hot) {
    // Enable Webpack hot module replacement for reducers
    module.hot.accept('../reducers', () => {
      const nextReducer = require('../reducers')
      store.replaceReducer(nextReducer)
    })
  }

  return store
}
```

"再次，设置存储需要执行不同的操作，让我们逐一来看："

+   `createStore`：这为我们创建一个存储，以表示应用程序的完整状态树。它接受参数——reducer（我们很快将看到的 reducer）和存储的初始状态。

+   `applyMiddleware`：这用于增强 Redux 以使用中间件。在这里，我们使用 thunk 中间件，它允许我们进行异步分发。

+   `configureStore`：最后，在`configureStore`中，我们通过调用增强的`createStore`：`createStoreWithMiddleware`来创建存储。我们这里有一些条件来处理热模块替换以自动重新加载代码更改，这是我们之前在 HMR 中看到的。

"明白了。"

"接下来，让我们看看以下操作："

```js
import JSONUtil from '../utils/jsonutil'
import ArrayUtil from '../utils/array'

export const FILTER_BY_TWEETS = 'FILTER_BY_TWEETS';
export const FILTER_BY_REDDITS = 'FILTER_BY_REDDITS';
export const SYNC_TWEETS = 'SYNC_TWEETS';
export const SYNC_REDDITS = 'SYNC_REDDITS';

export function filterTweets(event) {
  return {
    type: FILTER_BY_TWEETS,
    showTweets: event.target.checked
  }
}

export function filterReddits(event) {
  return {
    type: FILTER_BY_REDDITS,
    showReddits: event.target.checked
  }
}

export function syncTweets(json) {
  return {
    type: SYNC_TWEETS,
    tweets: json.map((tweet) => {
      return {...tweet, type: 'tweet'}
    }),
    receivedAt: Date.now()
  }
}

export function syncReddits(json) {
  return {
    type: SYNC_REDDITS,
    reddits: json.data.children.map((child) => {
      return {...child.data, type: 'reddit'}
    }),
    receivedAt: Date.now()
  }
}

export function fetchTweets(username) {
  return dispatch => {
    fetch(`/tweets.json?username=${username}`)
        .then(JSONUtil.parseJSON)
        .then(json => dispatch(syncTweets(json))).catch(JSONUtil.handleParseException)
  }
}

export function fetchReddits(topic) {
  return dispatch => {
    fetch(`https://www.reddit.com/r/${topic}.json`)
        .then(JSONUtil.parseJSON)
        .then(json => dispatch(syncReddits(json))).catch(JSONUtil.handleParseException)
  }
}
```

"我们正在导入以下代码："

```js
import JSONUtil from '../utils/jsonutil'
import ArrayUtil from '../utils/array'
```

"与之前一样，`JSONUtil`和`ArrayUtil`类。我已经将它们移动到使用类而不是模块。"

"`ArrayUtil`类的代码如下："

```js
class ArrayUtil {
  static in_groups_of(arr, n) {
    var ret = [];
    var group = [];
    var len = arr.length;
    for (var i = 0; i < len; ++i) {
      group.push(arr[i]);
      if ((i + 1) % n == 0) {
        ret.push(group);
        group = [];
      }
    }
    if (group.length) ret.push(group);
    return ret;
  };
}

export {ArrayUtil as default};
```

"`JSONUtil`类的代码如下："

```js
class JSONUtil{
  static parseJSON(response){
    return response.json()
  }

  static handleParseException(ex) {
    console.log('parsing failed', ex)
  }
}

export { JSONUtil as default }
```

"现在，我们将定义动作作为常量，而不是之前定义的动作对象，我们将跨命令引用它们。"

```js
export const FILTER_BY_TWEETS = 'FILTER_BY_TWEETS';
export const FILTER_BY_REDDITS = 'FILTER_BY_REDDITS';
export const SYNC_TWEETS = 'SYNC_TWEETS';
export const SYNC_REDDITS = 'SYNC_REDDITS';

```

"对于其他方法，我们简单地定义方法如下："

```js
export function filterTweets(event) {
  return {
    type: FILTER_BY_TWEETS,
    showTweets: event.target.checked
  }
}
```

"类似于我们之前的实现，我们包装并返回将用于 reducer 以突变存储的负载。"

"在从 API 获取数据的情况下，我们将实际的调用包装在`dispatch`中，如下所示："

```js
export function fetchTweets(username) {
  return dispatch => {
    fetch(`/tweets.json?username=${username}`)
        .then(JSONUtil.parseJSON)
        .then(json => dispatch(syncTweets(json))).catch(JSONUtil.handleParseException)
  }
}
```

"在这里，我们以不同的方式分发方法，它们将在结果返回时被链式调用和调用。正如我们之前看到的，当我们从`SocialActions`调用以下方法，我们将其包装在 dispatch 调用中以通知存储时："

```js
bindActionCreators(SocialActions, dispatch)
```

"在先前的方法中，因为它默认没有包装，所以我们将`fetchTweets`中的方法包装在`dispatch()`调用中。我们还将以下代码包装起来："

```js
dispatch(syncTweets(json))
```

"在收到响应后，我们将调用 syncTweets，它也会通知 Redux 存储。"

"明白了。接下来，我们应该看到 reducer，我想？"

"是的，让我们看看它："

```js
import { FILTER_BY_TWEETS, FILTER_BY_REDDITS, SYNC_REDDITS, SYNC_TWEETS } from '../actions/social'
import _ from 'underscore'

const mergeFeed = (tweets = [], reddits = [], showTweets = true, showReddits = true) => {
  let mergedFeed = []
  mergedFeed = showTweets ? mergedFeed.concat(tweets) : mergedFeed;
  mergedFeed = showReddits ? mergedFeed.concat(reddits) : mergedFeed;

  mergedFeed = _.sortBy(mergedFeed, (feedItem) => {
    if (feedItem.type == 'tweet') {
      let date = new Date(feedItem.created_at);
      return date.getTime();
    } else if ((feedItem.type == 'reddit')) {
      return feedItem.created_utc * 1000;
    }
  })
  return mergedFeed;
};

export default function social(state = {
  tweets: [],
  reddits: [],
  feed: [],
  showTweets: true,
  showReddits: true
}, action) {
  switch (action.type) {
    case FILTER_BY_TWEETS:
      return {...state, showTweets: action.showTweets, feed: mergeFeed(state.tweets, state.reddits, action.showTweets, state.showReddits)};
    case FILTER_BY_REDDITS:
      return {...state, showReddits: action.showReddits, feed: mergeFeed(state.tweets, state.reddits, state.showTweets, action.showReddits)};
    case SYNC_TWEETS:
      return {...state, tweets: action.tweets, feed: mergeFeed(action.tweets, state.reddits, state.showTweets, state.showReddits)};
    case SYNC_REDDITS:
      return {...state, reddits: action.reddits, feed: mergeFeed(state.tweets, action.reddits,  state.showTweets, state.showReddits)}
    default:
      return state
  }
}
```

"我们之前已经看到了`mergeFeed`。类似于迁移到类中，我将实现迁移到了 ES6。确定 feed 的逻辑与之前相同，我们将接受 Twitter 和 Reddit 的 feed 以及`showReddit`/`showTwitter`标志来决定如何构建 feed。"

"现在，特殊的方法如下："

```js
export default function social(state = {
  tweets: [],
  reddits: [],
  feed: [],
  showTweets: true,
  showReddits: true
}, action) 
```

"reducer 在动作分发时被调用。它接收状态中的上一个状态和在动作中的动作负载。如您所见，状态有一个默认值。"

"现在，根据动作负载，我们将确定需要使用数据运行什么，就像我们之前做的那样："

```js
switch (action.type) {
    case FILTER_BY_TWEETS:
      return {...state, showTweets: action.showTweets, feed: mergeFeed(state.tweets, state.reddits, action.showTweets, state.showReddits)};
  …
}
```

"这里的区别是我们没有直接突变状态。根据之前的状态，我们基于动作类型合并了之前和当前的计算状态，并返回它。"

"这是现在应用程序的当前状态。"

"明白了，我相信。我们现在只剩下应用程序了。"

"是的，让我们看看它会如何。我已经改为使用类。"

```js
class SocialTracker extends Component {
  constructor() {
    super();
    this.state = {twitter: 'twitter', reddit: 'twitter'}
  }
  componentDidMount() {
    this.syncFeed();
  }
  render() {
   let {filterTweets, filterReddits} = this.props;
    let {showTweets, showReddits} = this.props.social;
    return (
        <Grid className="grid">
          <Row>
            <Jumbotron className="center-text">
              <h1>Social Media Tracker</h1>
            </Jumbotron>
          </Row>
          <Row>
            <Col xs={8} md={8} mdOffset={2}>
              <Table striped  hover>
                <thead>
                <tr>
                  <th width='200'>Feed Type</th>
                  <th>Feed Source</th>
                </tr>
                </thead>
                <tbody>
                <tr>
                  <td><Input id='test' type="checkbox" label="Twitter" onChange={filterTweets} checked={showTweets}/></td>
                  <td><Input onChange={::this.changeTwitterSource} type="text" addonBefore="@" value={this.state.twitter}/></td>
                </tr>
                <tr>
                  <th><Input type="checkbox" label="Reddit" onChange={filterReddits} checked={showReddits}/></th>
                  <td><Input onChange={::this.changeRedditSource} type="text" addonBefore="@" value={this.state.twitter}/></td>
                </tr>
                <tr>
                  <th></th>
                  <td><Button bsStyle="primary" bsSize="large" onClick={::this.syncFeed}>Sync Feed</Button>
                  </td>
                </tr>
                </tbody>
              </Table>
            </Col>
          </Row>
          {this.renderFeed()}
        </Grid>
    )
  }

  changeTwitterSource(event) {
    this.setState({twitter: event.target.value});
  }

  changeRedditSource(event) {
    this.setState({reddit: event.target.value});
  }

  syncFeed() {
    const { fetchTweets, fetchReddits } = this.props;
    fetchReddits(this.state.reddit);
    fetchTweets(this.state.twitter);
    console.log('syncFeed was called');
  }

  renderFeed() {
    let {feed} = this.props.social;
    let feedCollection = ArrayUtil.in_groups_of(feed, 3);
    if (feed.length > 0) {
      return feedCollection.map((feedGroup, index) => {
        return <Row key={`${feedGroup[0].id}${index}`}>
          {feedGroup.map((feed) => {
            if (feed.type == 'tweet') {
              return <Col md={4} key={feed.id}><div className="well twitter"><p>{feed.text}</p></div></Col>;
            } else {
              let display = feed.selftext == "" ? `${feed.title}: ${feed.url}` : feed.selftext;
              return <Col md={4} key={feed.id}><div className="well reddit"><p>{display}</p></div></Col>;
            }

          })}
        </Row>
      });
    } else {
      return <div></div>
    }
  }

}

export default SocialTracker
```

"所以，我们首先将本地状态设置为管理 Twitter 用户和 Reddit 用户，如下所示："

```js
constructor() {
    super();
    this.state = {twitter: 'twitter', reddit: 'twitter'}
  }
```

"在`render`方法中，我们获取 Redux 传递给组件作为 props 的值（即存储），以便显示："

```js
    let {filterTweets, filterReddits} = this.props;
    let {showTweets, showReddits} = this.props.social;
```

"现在，如果你还记得以下内容："

```js
function mapStateToProps(state) {
  return {
    social: state.social
  }
}
```

"我们将从 Redux 将状态转换为传递社交对象存储作为 props 到组件。然后我们从社交 prop 值获取诸如`showTweets`、`showReddits`等值。"

"同样，我们有以下代码："

```js
function mapDispatchToProps(dispatch) {
  return bindActionCreators(SocialActions, dispatch)
}
```

"这会将动作转换为 props 并传递下去。我们接收它们作为`filterTweets`和`filterReddits`在 props 上。然后我们将这些动作作为`onclick`事件处理器，如下所示："

```js
<Input id='test' type="checkbox" label="Twitter" onChange={filterTweets} checked={showTweets}/>
```

"最后，我们通过从相同的 props 中获取值来显示数据源本身："

```js
renderFeed() {
    let {feed} = this.props.social;
    let feedCollection = ArrayUtil.in_groups_of(feed, 3);
    if (feed.length > 0) {
      return feedCollection.map((feedGroup, index) => {
        console.log(feedGroup);
        return <Row key={`${feedGroup[0].id}${index}`}>
          {feedGroup.map((feed) => {
            if (feed.type == 'tweet') {
              return <Col md={4} key={feed.id}><div className="well twitter"><p>{feed.text}</p></div></Col>;
            } else {
              let display = feed.selftext == "" ? `${feed.title}: ${feed.url}` : feed.selftext;
              return <Col md={4} key={feed.id}><div className="well reddit"><p>{display}</p></div></Col>;
            }

          })}
        </Row>
      });
    } else {
      return <div></div>
    }
  }
```

"我们将从传递给我们的社交 prop 中获取数据源，如下："

```js
    let {feed} = this.props.social;
```

"最后，为了同步内容，我们有以下代码："

```js
  syncFeed() {
    const { fetchTweets, fetchReddits } = this.props;
    fetchReddits(this.state.reddit);
    fetchTweets(this.state.twitter);
    console.log('syncFeed was called');
  }
```

"太棒了。我想，有了这个，我们就完成了！"

"是的。你想回顾一下设置过程吗？"

"当然。我们首先设置了我们想要将 actions 和 stores 映射到传递给组件的 props 的方式。然后我们设置了 store 并将其连接到组件。"

"为了设置 store，我们使用了并应用了 thunk 中间件模块来增强 Redux，以便我们可以异步地分发 actions。"

"然后我们创建了从组件中调用的 actions，并将有效载荷和动作类型包装起来，以便发送到 store。"

"我们还创建了 reducers——社交 reducer——来实际操作、创建并返回新的 Redux 状态。"

"没错！让我们看看它看起来怎么样，好吗？"

![设置 Redux](img/4730_13_01.jpg)

"太棒了！卡拉一定会喜欢这个的。"

# 摘要

我们查看了一下使用 Redux 和设置它的方法。我们看到了它与之前看到的纯 Flux 实现的不同之处。我们查看了一下 Redux 的不同组件——它的 stores、actions 以及用于 stores 和 actions 的 reducers。最后，我们看到了应用如何与 store 连接，以及我们如何通过 props 使用 actions 和数据。
