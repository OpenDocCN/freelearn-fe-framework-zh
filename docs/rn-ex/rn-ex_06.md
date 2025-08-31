# 第六章：高级 Facebook 应用功能

现在我们已经获得了访问 Facebook 的 Graph API 的权限，是时候完成我们应用的构建了。

在本章中，我们将：

+   通过从 Graph APIs 获取更多数据来继续构建我们的 Facebook 连接应用`Friends`，例如从我们动态中的每个现有帖子获取媒体附件、评论和点赞数量

+   为我们的应用添加一个下拉刷新机制，允许用户重新加载数据

+   了解`Image`组件，它将允许我们在应用中渲染图片

+   发现 WebView，在本地可用的`View`组件中打开链接

+   为应用添加一个注销屏幕

+   对应用进行修改以构建 Android 版本

让我们继续上一章的遗留内容，并扩展我们的`FeedListRow`组件。

# 创建 PostView

在第五章的结尾，“第三项目 - Facebook 客户端”，我们创建了一个带有`TouchableHighlight`的`FeedListRow`组件，当按下时会触发以下函数：

```js
// Friends/app/components/FeedListRow/index.js 

... 
  _navigateToPostView () { 
    console.log('pushed'); 
  } 
... 

```

我们将构建一个`PostView`组件，当在`FeedListRow`中按下`TouchableHighlight`组件时，用户将导航到该组件，并在`_navigfateToPostView`函数中替换当前的登录以处理该导航。

这个`PostView`组件在加载时应该在`AsyncStorage`中查找该帖子的详细信息，并在存在的情况下加载它们。如果不存在，则应向 Facebook Graph API 请求帖子的详细信息并将它们保存到`AsyncStorage`中供将来使用。

我们感兴趣的是帖子的附件、评论和点赞。由于 Facebook 上的每个帖子都分配了一个唯一的帖子 ID，我们还可以在`AsyncStorage`中将包含附件、评论和点赞数据的对象保存到该帖子 ID 下作为其键。

首先，我们将在`storageMethods.js`中创建一个新的函数，该函数执行以下功能：

+   接受帖子 ID 和批处理回调作为参数

+   创建三个单独的`GraphRequest`实例，每个实例对应我们将获取的三个边缘（附件、评论和点赞），并将返回的数据保存到对象中

+   启动一个`GraphRequestManager`，链接三个`GraphRequest`实例，并传入批处理回调，从而将返回的数据对象传递给批处理回调函数

然后，创建一个`PostView`组件，执行以下操作：

+   它渲染与`FeedListRow`创建的相同的故事和消息字符串，以便用户保留他们点击的内容的上下文。

+   它使用一种存储方法来检查与该特定帖子 ID 相关的数据是否存在。如果存在，则`PostView`将使用它。如果不存在，则应使用我们新的存储方法来获取该帖子 ID 的附件、评论和点赞。

+   传入我们新存储方法的批处理回调应包括将结果保存到`AsyncStorage`中，其键与帖子 ID 相同。

+   它将帖子的评论和点赞数以视觉形式显示在一行中。

最后，修改 `FeedListRow` 组件，使其使用其现有的 `_navigateToPostView` 方法导航到 `PostView`，并传递任何必要的属性。

创建一个 `resultsObject` 来存储每个独特的 `GraphRequest` 的结果：

```js
// Friends/app/utils/graphMethods.js 

... 
export const getPostDetails = (id, batchCallback) => { 
  let resultsObject = { 
    attachments: undefined, 
    comments: undefined, 
    likes: undefined 
  } 

```

在前面的代码中的三个 `GraphRequest` 实例中，使用给定的帖子 ID 调用其相应的 `attachments`、`comments` 和 `likes` 边从 API。然后，将这些结果保存到 `resultsObject` 中，其键对应于相应的键：

```js
  const attachmentsRequest = new GraphRequest('/' + id + 
  '/attachments', null, (error, response) => { 
    if (error) { 
      console.log(error); 
    } 
    resultsObject.attachments = response.data; 
  }); 

  const commentsRequest = new GraphRequest('/' + id + '/comments', 
  null, (error, response) => { 
    if (error) { 
      console.log(error); 
    } 
    resultsObject.comments = response.data; 
  }); 

  const likesRequest = new GraphRequest('/' + id + '/likes', null, 
  (error, response) => { 
    if (error) { 
      console.log(error); 
    } 
    resultsObject.likes = response.data; 
  }); 

```

最后，创建一个新的 `GraphRequestManager` 实例，并将所有三个请求以及作为参数传递给此函数的 `batchCallback` 添加到其中。将 `resultsObject` 传递给 `batchCallback` 以使该回调能够访问从 `attachments`、`comments` 和 `likes` 边获得的数据：

```js
  new GraphRequestManager() 
    .addRequest(attachmentsRequest) 
    .addRequest(commentsRequest) 
    .addRequest(likesRequest) 
    .addBatchCallback(() => batchCallback(resultsObject)) 
    .start(); 
} 

```

然后，导入将在该组件中使用到的各种不同的辅助方法，如下所示：

```js
// Friends/app/components/PostView/index.js 

import React, { Component } from 'react'; 
import { 
  ActivityIndicator, 
  Text, 
  TouchableHighlight, 
  View 
} from 'react-native'; 

import { getAsyncStorage, setAsyncStorage } from '../../utils/storageMethods'; 
import { getDateTimeString } from '../../utils/dateMethods'; 
import { getPostDetails } from '../../utils/graphMethods'; 
import styles from './styles'; 

```

将状态中的 `loading` 布尔值设置为 `true` 以用于 `ActivityIndicator`：

```js
export default class PostView extends Component { 
  constructor (props) { 
    super (props); 

    this.state = { 
      loading: true 
    } 
  } 

```

在 `componentWillMount` 期间，获取存储在此帖子 ID 键下的对象。检查数据是否存在：如果不存在数据，`getAsyncStorage` 被配置为返回一个空对象。如果这是 `true`，则调用 `_getPostDetails`；否则，将详细信息保存到本地状态：

```js
  async componentWillMount () { 
    const result = await getAsyncStorage(this.props.postID); 

    if (Object.keys(result).length === 0) { 
      this._getPostDetails(); 
      return; 
    } 
    this._savePostDetailsToState(result); 
  } 

```

就像 `FeedListRow` 一样，如果适用，渲染创建日期、故事和信息。有条件地调用 `_renderActivityIndicator` 或 `_renderDetails`，取决于 `loading` 布尔值。最后，渲染一个分隔符，以期待向此组件添加评论：

```js
   render () { 
    return ( 
      <View style={ styles.container }> 
        <View> 
          <Text style={ styles.created }> 
            { this._renderCreatedString() } 
          </Text> 
            { this._renderStoryString() } 
          <Text> 
            { this._renderMessageString() } 
          </Text> 
        </View> 
        <View> 
          { this.state.loading ? this._renderActivityIndicator() : 
          this._renderDetails() } 
        </View> 
        <View style={ styles.separator } /> 
      </View> 
    ) 
  } 

```

调用我们在 `graphMethods` 中刚刚创建的 `getPostDetails` 方法，并传递一个回调，该回调使用 `getPostDetails` 的结果对象将内容保存到状态中；然后将其保存到 `AsyncStorage` 中，键等于此帖子的 ID：

```js
  async _getPostDetails () { 
    await getPostDetails(this.props.postID, (result) => { 
      this._savePostDetailsToState(result); 
      setAsyncStorage(this.props.postID, result); 
    }); 
  } 

```

渲染一个 `ActivityIndicator` 组件：

```js
  _renderActivityIndicator () { 
    return ( 
      <ActivityIndicator 
        animating={ this.state.spinning } 
        size={ 'large' } 
      /> 
    ) 
  } 

```

按如下方式渲染此帖子拥有的 `Likes` 和 `Comments` 数量：

```js
  _renderCreatedString () { 
    return 'Posted ' + getDateTimeString(this.props.createdTime); 
  } 

  _renderDetails () { 
    return ( 
      <View style={ styles.detailsContainer }> 
        <Text style={ styles.detailsRow }> 
          { this.state.likes.length } Likes, {  
          this.state.comments.length } Comments 
        </Text> 
      </View> 
    ) 
  } 

```

`_renderCreatedString`、`_renderMessageString` 和 `_renderStoryString` 方法与 `FeedListRow` 中的方法保持不变：

```js
  _renderMessageString () { 
    return this.props.message 
  } 

  _renderStoryString () { 
    if (this.props.story) { 
      return ( 
        <Text style={ styles.story }> 
          { this.props.story } 
        </Text> 
      ) 
    } 
  } 

```

将此帖子的 `attachments`、`comments` 和 `likes` 边的数据保存到状态中，并关闭旋转的 `ActivityIndicator`：

```js
  _savePostDetailsToState (data) { 
    this.setState({ 
      attachments: data.attachments, 
      comments: data.comments, 
      likes: data.likes, 
      loading: false 
    }); 
  } 
} 

```

这是 `PostView` 的样式：

```js
// Friends/app/components/PostView/index.js 

import { StyleSheet } from 'react-native'; 

const styles = StyleSheet.create({ 
  container: { 
    flex: 1, 
    margin: 10, 
    marginTop: 75, 
  }, 
  created: { 
    color: '#365899', 
    fontWeight: 'bold', 
    marginBottom: 5 
  }, 
  detailsContainer: { 
    flexDirection: 'row', 
    justifyContent: 'space-between' 
  }, 
  detailsRow: { 
    color: '#365899', 
    marginBottom: 15, 
    marginTop: 15, 
    textAlign: 'left' 
  }, 
  separator: { 
    height: 2, 
    marginLeft: 15, 
    marginRight: 15, 
    backgroundColor: '#365899' 
  }, 
  story: { 
    marginBottom: 5, 
    textDecorationLine: 'underline' 
  } 
}); 

export default styles; 

```

最后，修改 `FeedListRow` 中的 `_navigateToPostView` 函数：

```js
// Friends/app/components/FeedListRow/index.js 

... 
export default class FeedListRow extends Component { 
  ... 
  _navigateToPostView () { 
    this.props.navigator.push({ 
      component: PostView, 
      passProps: { 
        createdTime: this.props.createdTime, 
        message: this.props.message, 
        postID: this.props.postID, 
        story: this.props.story 
      } 
    }); 
  } 
  ... 
} 

```

接下来，我们将添加一个 `ListView` 来填充该帖子的评论，并在 `PostView` 中的分隔线下方渲染它。

# 向 `PostView` 添加评论

在这一步中，我们将编辑 `PostView` 以包含一个 `ListView` 来渲染所有评论。由于 `PostView` 会在 `componentWillMount` 生命周期方法加载信息后将其评论数据保存到其状态中，我们可以使用这些数据来渲染评论。

首先，创建一个组件来容纳这个 `ListView`；让我们称它为 `CommentList`。它应该执行以下操作：

+   包含一个由 `PostView` 通过属性传递给它的评论列表

+   使用这些评论渲染一个 `ListView`：

+   行应该由子组件`CommentListRow`渲染

您的`CommentListRow`组件应该执行以下操作：

+   每行应包含其发布者的名字和他们写的消息

+   使用`ListView`组件分隔每个评论

最后，更新`PostView`，使其在`PostView`的`render`方法中直接在分隔符下方渲染`CommentList`。实例化一个新的`ListView.DataSource`实例：

```js
// Friends/app/components/CommentList/index.js 

import React, { Component } from 'react'; 
import { 
  ListView, 
  Text, 
  View 
} from 'react-native'; 

import CommentListRow from '../CommentListRow'; 
import styles from './styles'; 

export default class CommentList extends Component { 
  constructor (props) { 
    super (props); 
    this.state = { 
      ds: new ListView.DataSource({ 
        rowHasChanged: (r1, r2) => r1 !== r2 
      }) 
    } 
  } 

```

设置`dataSource`常量，传入`comments`属性或一个空数组：

```js
  render () { 
    const dataSource = this.state.ds.cloneWithRows(this.props.comments || []); 

```

每一行应该是一个新的`CommentListRow`组件：

```js
    return ( 
      <View style={ styles.container }> 
        <ListView 
          automaticallyAdjustContentInsets={ false } 
          dataSource={ dataSource } 
          renderRow={ (rowData, sectionID, rowID) => 
            <CommentListRow 
              message={ rowData.message } 
              name={ rowData.from.name } /> 
          } 

```

为每个评论渲染一个分隔符：

```js
          renderSeparator={ (sectionID, rowID) => 
            <View 
              key={ rowID } 
              style={ styles.separator } /> 
          } /> 
      </View> 
    ) 
  } 
}

```

这是`CommmentList`样式块的样式：

```js
// Friends/app/components/CommentList/styles.js 

import { StyleSheet } from 'react-native'; 

const styles = StyleSheet.create({ 
  container: { 
    flex: 1 
  }, 
  separator: { 
    flex: 1, 
    height: StyleSheet.hairlineWidth, 
    marginLeft: 15, 
    marginRight: 15, 
    backgroundColor: '#1d2129' 
  } 
}); 

export default styles; 

```

接下来，让我们看看`CommentListRow`：

```js
// Friends/app/components/CommentListRow/index.js 

import React, { Component } from 'react'; 
import { 
  Text, 
  View 
} from 'react-native'; 

import styles from './styles'; 

export default (props) => { 
  return ( 
    <View style={ styles.container }> 
      <View style={ styles.header }> 
        <Text style={ styles.name }> 
          { props.name } 
        </Text> 
      </View> 
      <View style={ styles.body }> 
        <Text style={ styles.comment }> 
          { props.message } 
        </Text> 
      </View> 
    </View> 
  ) 
} 

```

一个简单的无状态函数组件返回带有发布者名字和他们的评论的评论行。以下代码块包含`CommentListRow`的样式：

```js
// Friends/app/components/CommentListRow/styles.js 

import { StyleSheet } from 'react-native'; 

const styles = StyleSheet.create({ 
  body: { 
    marginBottom: 20, 
    marginLeft: 30, 
    marginRight: 30, 
    marginTop: 10, 
  }, 
  comment: { 
    color: '#1d2129' 
  }, 
  container: { 
    flex: 1 
  }, 
  header: { 
    marginTop: 5, 
    marginLeft: 10, 
    marginRight: 10 
  }, 
  name: { 
    color: '#1d2129', 
    fontWeight: 'bold' 
  } 
}); 

export default styles; 

```

最后，让我们看看对`PostView`所做的更改：

```js
// Friends/app/components/PostView/index.js 

... 
import CommentList from '../CommentList'; 

export default class PostView extends Component { 
  ... 
  render () { 
    return ( 
      <View style={ styles.container }> 
        ... 
        <View style={ styles.separator } /> 
        <View style={ styles.commentListContainer }> 
          <CommentList comments={ this.state.comments } /> 
        </View> 
      </View> 
    ) 
  } 
  ... 
} 

```

上述代码导入并渲染`CommentList`在分隔符下方。

`commentListContainer`样式看起来是这样的：

```js
// Friends/app/components/PostView/styles.js 

   commentListContainer: { 
    flex: 1,                               
    marginTop: 20 
  } 

```

在这一点上，我们应该继续完善`PostView`，添加我们在本章开头描述的其他功能。在下一节中，我们将探讨如何在用户帖子或单个帖子中添加更多数据时刷新我们已有的现有数据。

# 使用 RefreshControl 重新加载数据

下拉刷新交互最初是在 2008 年创建的流行 Twitter iOS 应用**Tweetie**中构思的。这种交互涉及用户将屏幕向下拉，直到达到某个阈值，然后释放以表示他们想要刷新屏幕内容。

使用 React Native SDK，我们可以使用`RefreshControl`来获得相同的下拉刷新交互，并允许我们的用户在应用中随意重新加载数据。

本章我们将使用以下`RefreshControl`属性：

+   `onRefresh`：这是一个在执行刷新操作时被调用的函数

+   `refreshing`：这是一个布尔值，表示视图是否应该被动画化

+   `tintColor`：这是刷新指示器的颜色

+   `title`：这是一个在刷新指示器下方显示的字符串

+   `titleColor`：这是标题的颜色

要使用`RefreshControl`，将其渲染到具有`refreshControl`属性的`ListView`或`ScrollView`组件中。

对于我们的实现，我们首先想要修改`App.js`，使其执行以下操作：

+   在其状态中包含一个`refreshControlSpinning`布尔值

+   修改当前`_checkLoginStatus`函数，将获取存储中数据逻辑移动到其自己的函数`_getFeedData`中；新的`_getFeedData`函数在完成后也应该将`refreshControlSpinning`布尔值切换到`false`

+   包含一个函数`_refreshFeedList`，用于刷新帖子，将`refreshControlSpinning`设置为`true`，然后调用新的`_getFeedData`函数

+   将 `refreshControlSpinning` 布尔值和 `_refreshFeedList` 函数传递给它渲染的 `FeedList` 组件

然后，修改 `FeedList` 以执行以下操作：

+   将 `RefreshControl` 组件渲染到 `ListView` 的 `refreshControl` 属性中

+   将其旋转属性指向 `App.js` 中的 `refreshControlSpinning` 布尔值

+   将 `onRefresh` 属性指向 `App.js` 中的 `_refreshFeedList` 函数。

这里是我的对 `App` 组件的修改：

```js
// Friends/app/App.js 

... 
export default class App extends Component { 
  constructor (props) { 
    ... 
    this.state = { 
      ... 
      refreshControlSpinning: false 
    } 
  } 

```

我们在状态中添加了一个新的 `refreshControlSpinning` 布尔值。旧 `spinner` 布尔值被重命名为 `activityIndicatorSpinning`。在 `_checkLoginStatus` 的最后一行被拆分成自己的方法，以便稍后在以下片段中重用。同时，更新传递给 `LoginPage` 的 `getFeed` 属性，以反映新的拆分方法：

```js
  async _checkLoginStatus () { 
    ... 
    if (result === null) { 
      this.props.navigator.push({ 
        ... 
        passProps: { 
          getFeed: () => _getFeed() 
        } 
      }); 
      ... 
    } 

    this._getFeed(); 
  } 

  _getFeed () { 
    getFeed((error, result) => this._responseInfoCallback
    (error, result)); 
  } 

```

让我们将 `refreshControlSpinning` 和 `_refreshFeedList` 传递给 `FeedList`:

```js
  _renderView () { 
    ... 
    return ( 
      <FeedList 
        ... 
        refreshControlSpinning={ this.state.refreshControlSpinning } 
        refreshFeedList={ () => this._refreshFeedList() } 
      /> 
    ); 
  } 

```

将 `refreshControlSpinning` 布尔值设置为 `true` 并调用 `_getFeed`：

```js
  _refreshFeedList () { 
    this.setState({ 
      refreshControlSpinning: true 
    }); 

    this._getFeed(); 
  } 

```

一旦数据已加载到状态和 `AsyncStorage` 中，将 `refreshControlSpinning` 设置为 `false`：

```js
  _responseInfoCallback (error, result) { 
    ... 
    this.setState({ 
      refreshControlSpinning: false 
      ... 
    }); 
  } 
} 

```

向 `ListView` 添加一个 `refreshControl` 属性，它指向 `_renderRefreshControl`：

```js
// Friends/app/components/FeedList/index.js 

import { 
  ... 
  RefreshControl, 
} from 'react-native'; 
... 
export default class FeedList extends Component { 
  ... 
  render () { 
    ... 
    return ( 
      <View style={ styles.container }> 
        <ListView 
          refreshControl={ this._renderRefreshControl() } 
          ... 
        /> 
      </View> 
    ) 
  } 

```

返回 `RefreshControl` 组件。它的 `onRefresh` 属性指向 `App.js` 中的 `_refreshFeedList` 方法，并且它刷新布尔值也指向 `App.js` 中的 `refreshControlSpinning` 属性：

```js
  _renderRefreshControl () { 
    return ( 
      <RefreshControl 
        onRefresh={ () => this.props.refreshFeedList() } 
        refreshing={ this.props.refreshControlSpinning } 
        tintColor={ '#365899' } 
        title={ 'Refresh Feed' } 
        titleColor={ '#365899' } 
      /> 
    ) 
  } 
} 

```

下一步是将任何图像附件渲染到 `PostView` 中。

# 渲染图像

要使用 React Native 显示图像，我们使用 `Image` 组件。它允许我们从本地和远程源显示图像。你还可以像为任何其他 React 组件添加样式一样为图像添加样式。

在本章中，我们将使用以下属性来为我们的 `Image` 组件设置样式：

+   `resizeMode`: 我们将使用以下字符串之一：

    +   `cover`: 这会均匀地缩放图像并保持其宽高比，使得图像的宽度和高度将等于或大于封装 `Image` 组件的视图。

    +   `contain`: 这个字符串也会均匀地缩放图像并保持其宽高比，使得图像的宽度和高度将等于或小于封装 `Image` 组件的视图。

    +   `stretch`: 这会独立地缩放宽度和高度，并可以改变源图像的宽高比。

    +   `repeat`: 这会将图像重复以覆盖封装视图的整个框架。此选项在 iOS 上也保持原始大小和宽高比，但在 Android 上则不保持。

    +   `center`: 这会将图像居中。

+   `source`: 这将是渲染的图像的远程 URL 或本地路径。

+   `style`: 这是一个样式对象。

在基本层面上，你可以这样加载静态图像资源：

```js
<Image source={ require('../images/my-icon.png') } /> 

```

此外，你也可以对远程的做同样的处理：

```js
<Image 
  source={{ uri: 'https://www.link-to-my-image.com/image.png' }} 
  style={{ 
    width: 400, 
    height: 400 
  }} /> 

```

用户动态中每一条带有图像的帖子都可以使用 `Image` 组件来渲染该图像。

从 Facebook Graph API 结构图像的方式如下：

```js
attachments: [{ 
  media: { 
    image: { 
      height: 400, 
      src: 'https://www.link-to-my-image.com/image.png', 
      width: 400 
    } 
  } 
}] 

```

在此基础上，让我们首先创建一个名为`imageMethods`的新工具文件。此文件应执行以下操作：

+   从 React Native 导入`Dimensions` API。

+   导出`getHeightRatio`函数，该函数接受图像的高度和宽度，并返回图像应有的高度。我们可以通过执行以下操作来计算它：

    +   获取用户设备的宽度尺寸，并从中减去一定的量以适应左右边距。

    +   使用这个边距偏移量，并将其除以图像的原始宽度以获得所需的比率。

    +   返回将高度乘以比例得到正确图像高度的乘积结果。

        +   导出另一个函数`getWidthOffset`，它接受用户的设备宽度并返回它，减去一定的量以适应左右边距。为了代码重用，我们应该将其用作`getHeightRatio`的第一个要点的一部分。

修改`PostView`以执行以下操作：

+   考虑到较长的图片，顶级`View`应替换为`ScrollView`组件。

+   如果帖子已加载完成并且`attachments`数组中包含任何图像，则渲染帖子`attachments`数组中的第一张图像。

+   `Image`组件应将其`resizeMode`属性设置为`contain`，以便图像不会超出屏幕。它应该有一些左和右边距，以便它不会接触到屏幕边缘，其宽度和高度应由`imageMethods`文件计算。

+   这种渲染应放置在帖子的详细信息（时间、消息和故事）下方，但在点赞和评论数量上方。

获取`gridWidthOffset`，将其除以图像的`width`，然后将图像的`height`除以这个结果，如下所示：

```js
// Friends/app/utils/imageMethods.js 

import { Dimensions } from 'react-native'; 

export const getHeightRatio = (height, width) => { 
  return height * (getWidthOffset()/width); 
} 

```

获取用户的`width`，然后从中减去`20`像素：

```js
export const getWidthOffset = () => { 
  return Dimensions.get('window').width - 20; 
} 

```

将`Image`、`ScrollView`和`imageMethods`导入到`PostView`组件中：

```js
// Friends/app/components/PostView/index.js 

import { 
  ... 
  Image, 
  ScrollView, 
} from 'react-native'; 

import { getHeightRatio, getWidthOffset } from '../../utils/imageMethods'; 

```

预计到较长的帖子，将顶级视图替换为`ScrollView`。添加条件逻辑以触发`_renderAttachments`，将其放在调用`_renderDetails`之前：

```js
... 
export default class PostView extends Component { 
  ... 
  render () { 
    return ( 
      <ScrollView style={ styles.container }> 
        ... 
        <View> 
          { !this.state.loading && this._renderAttachments() } 
        </View> 
        ... 
      </ScrollView> 
    ) 
  } 

```

为涉及某些照片/相册的非常特定边缘情况分配`subattachments`：

```js
  ... 
  _renderAttachments () { 
    let attachment = this.state.attachments[0] 
    let media; 

    if (attachment && attachment.hasOwnProperty('subattachments')) { 
      attachment = attachment.subattachments.data[0]; 
    } 

```

检查`media`属性的存在，如下所示：

```js
    if (attachment && attachment.hasOwnProperty('media')) { 
      media = attachment.media; 
    } 

```

如果`media`属性存在并且包含`image`属性，则渲染`Image`：

```js
    if (media && media.image) { 

```

返回具有确定属性的`Image`组件：

```js
      const imageObject = media.image; 

      return ( 
        <Image 
          resizeMode={ 'contain' } 
          source={{ uri: imageObject.src }} 
          style={{ 
            marginRight: 10, 
            marginTop: 30, 
            width: getWidthOffset(), 
            height: getHeightRatio(imageObject.height, 
            imageObject.width) 
          }} 
        /> 
      ) 
    } 
  } 
  ... 
} 

```

`PostView`的`container`样式已更改，省略了`marginTop`属性：

```js
// Friends/app/components/PostView/styles.js 

   commentListContainer: { 
    flex: 1, 
    marginTop: 20 
  } 

```

`commentListContainer`样式与新的`ScrollView`组件相匹配。

现在图像已经渲染，我们应该处理其他类型的附件--链接。

# 使用 WebView 渲染链接

当用户选择一个链接时，在您的应用程序中渲染该链接是有益的，这样用户就不会被抛出应用程序并进入他们的浏览器。为了使用 React Native 完成此任务，我们将使用`WebView`组件。

`WebView`组件在原生、应用程序包含的视图中渲染 Web 内容。对于这个应用程序，我们将使用其众多属性中的其中一个：

+   `source`：这将在`WebView`中加载带有可选头部的 URI 或静态 HTML。

渲染`WebView`组件很简单：

```js
import { 
  WebView 
} from 'react-native'; 

class WebViewSample extends Component { 
  render () { 
    return ( 
      <WebView 
        source={{uri: 'https://www.google.com'}} /> 
    ) 
  } 
} 

```

并非所有帖子都包含附件中的链接。当它们包含链接时，其层次结构如下：

```js
attachments: [{ 
title: 'Link to Google' 
  url: 'https://www.google.com' 
}] 

```

让我们做一些修改以适应`WebView`。首先，创建一个名为`WebViewComponent`的新组件；它应该是一个无状态的函数，返回一个`WebView`，并将其`source`设置为它接收的属性中的任何链接。

然后，修改`PostView`，使其执行以下功能：

+   如果帖子中包含图片，则直接在渲染图片的地方渲染按钮。

+   该按钮仅在帖子的第一个附件与链接相关联时渲染。按钮应包含链接的标题，并且当点击时，导航到您的`WebViewComponent`以打开链接。

从 iOS 9 开始，未加密的 HTTP 链接被 iOS 的 App Transport Security 自动阻止。您可以在 Xcode 项目中项目文件的`Info.plist`文件中逐个案例地将这些链接列入白名单。苹果公司不推荐这样做，并将在不久的将来要求所有提交的应用遵守这项新政策。

以下是一个无状态的函数组件，它只返回一个带有`source` URI 的`WebView`：

```js
// Friends/app/components/WebViewComponent/index.js 

import React, { Component } from 'react'; 

import { 
  WebView 
} from 'react-native'; 

export default (props) => { 
  return ( 
    <WebView 
      source={{ uri: props.url }} 
    /> 
  ) 
} 

```

导入`Button`和`WebViewComponent`依赖项：

```js
// Friends/app/components/PostView/index.js 

import { 
  Button, 
  ... 
} from 'react-native'; 

import WebViewComponent from '../WebViewComponent'; 

```

如果`PostView`已加载完成，则条件调用`_renderLink`：

```js
... 
export default class PostView extends Component { 
  ... 
  render () { 
    return ( 
      <ScrollView style={ styles.container }> 
        ... 
        <View> 
          { !this.state.loading && this._renderLink() } 
        </View> 
        ... 
      </ScrollView> 
    ) 
  } 

```

获取第一个附件对象：

```js
  ... 
  _renderLink () { 
    let attachment = this.state.attachments[0]; 
    let link; 
    let title; 

```

再次检查`subattachments`：

```js
    if (attachment && attachment.hasOwnProperty('subattachments')) { 
      attachment = attachment.subattachments.data[0]; 
    } 

```

如果`title`是空字符串或未定义，则将其通用地命名为`Link`：

```js
    if (attachment && attachment.hasOwnProperty('url')) { 
      link = attachment.url; 
      title = attachment.title || 'Link'; 

```

渲染一个在按下时调用`_renderWebView`的`Button`：

```js
      return ( 
        <Button 
          color={ '#365899' } 
          onPress={ () => this._renderWebView(link) } 
          title={ title } 
        /> 
      ) 
    } 
  } 

```

将用户导航到`WebViewComponent`，并发送提供的 URL。

```js
  _renderWebView (url) { 
    this.props.navigator.push({ 
      component: WebViewComponent, 
      passProps: { 
        url 
      } 
    }); 
  } 
  ... 
} 

```

我们对这个应用程序的最后一点润色是让用户能够从应用程序中注销。

# 使用 TabBarIOS 注销

我们的最后一步是为用户添加一个注销页面。使用`TabBarIOS`组件和`react-native-vector-icons`，我们将创建一个标签视图，允许用户注销。

让我们为此进行一些修改。首先，我们需要修改`App.js`，使其执行以下功能：

+   导入`TabBarIOS`和`react-native-vector-icons`依赖项

+   如果活动指示器没有旋转，则在`_renderView`方法中返回一个`TabBarIOS`组件

+   在`App`组件的状态中添加一个`selectedTab`字符串以跟踪当前选择的标签，默认为`FeedList`组件

+   有单独的函数来渲染`FeedList`和`LoginPage`组件而不进行导航

+   向`LoginPage`传递一个回调，该回调执行`_checkLoginStatus`方法

+   修改其`container`样式，不再对任何项目进行居中或对齐

然后，修改`LoginPage`组件，使其`onLogoutFinished`回调执行`_checkLoginStatus`。将新依赖项导入到项目中：

```js
// Friends/app/App.js 

import { 
  TabBarIOS, 
  ... 
} from 'react-native'; 
... 
import Icon from 'react-native-vector-icons/FontAwesome'; 

```

在状态中存储`selectedTab`字符串，默认为`feed`：

```js
... 
export default class App extends Component { 
  constructor (props) { 
    ... 
    this.state = { 
      ... 
      selectedTab: 'feed' 
    } 
  } 

```

使用之前相同的逻辑渲染`FeedList`组件：

```js
  ... 
  _renderFeedList () { 
    return ( 
      <FeedList 
        feed={ this.state.feed } 
        navigator={ this.props.navigator } 
        refreshControlSpinning={ this.state.refreshControlSpinning } 
        refreshFeedList={ () => this._refreshFeedList() } 
      /> 
    ) 
  } 

```

渲染`LoginPrompt`组件，传递`_checkLoginStatus`方法：

```js
  _renderLoginPrompt () { 
    return ( 
      <LoginPage checkLoginStatus={ () => this._checkLoginStatus() } /> 
    ) 
  } 

```

当用户使用以下代码注销时，这将导致应用导航回`LoginPage`：

```js
  _renderView () { 
    ... 
    return ( 
      <View style={ styles.container }> 
        <TabBarIOS> 
          <Icon.TabBarItemIOS 
            title={ 'Feed' } 
            selected={ this.state.selectedTab === 'feed' } 
            iconName={ 'newspaper-o' } 
            iconSize={ 20 } 
            onPress={ () => this._setSelectedTab('feed') } 
          > 
            { this._renderFeedList() } 
          </Icon.TabBarItemIOS> 
          <Icon.TabBarItemIOS 
            title={ 'Sign Out' } 
            selected={ this.state.selectedTab === 'signOut' } 
            iconName={ 'sign-out' } 
            iconSize={ 20 } 
            onPress={ () => this._setSelectedTab('signOut') } 
          > 
            { this._renderLoginPrompt() } 
          </Icon.TabBarItemIOS> 
        </TabBarIOS> 
      </View> 
    ) 
  } 

```

`_renderView`中之前存在`_renderFeedList`内容的地方现在渲染`TabBarIOS`组件。

```js
  ... 
  _setSelectedTab (selectedTab) { 
    this.setState({ 
      selectedTab 
    }); 
  } 
} 

```

之前的代码将状态中的`selectedTab`属性设置为用户点击的任何标签：

```js
// Friends/app/styles.js 

container: { 
  flex: 1, 
  backgroundColor: '#F5FCFF', 
} 

```

之前的代码从`container`属性中移除了所有其他样式，这样标签栏的图标就不会被强制居中显示在屏幕上：

```js
// Friends/app/components/LoginPage/index.js 

... 
export default class LoginPage extends Component { 
  render() { 
    return ( 
      <View style={ styles.container }> 
        <LoginButton 
          ... 
          onLogoutFinished={() => this.props.checkLoginStatus() } 
        /> 
      </View> 
    ); 
  } 
} 

```

在`LoginButton`的`onLogoutFinished`属性中的上一个警报调用已被替换为触发`checkLoginStatus`。

在这个应用中，你所有的进步都做得很好！下一步是针对 Android 开发进行修改。

# 移植到 Android

我们将为这个应用进行的 Android 修改与为`Expenses`所做的修改类似，这将在第九章，*额外的 React Native 组件*中稍后讨论。我们对`Friends`所做的修改如下：

+   将`TabBarIOS`替换为`DrawerLayoutAndroid`和`ToolbarAndroid`

+   创建`Drawer`和`DrawerRow`组件以支持`DrawerLayoutAndroid`

+   在根级别的`index.android.js`文件中使用`Navigator`

+   创建`App`组件的 Android 特定版本

+   为 Android 特定的样式更新`FeedList`

+   修改`FeedListRow`以支持 Android 导航

+   向`PostView`添加`BackAndroid`和`Navigator`支持

关于`DrawerLayoutAndroid`和`ToolbarAndroid`的深入解释可以在第九章，*额外的 React Native 组件*中找到。

# 添加`DrawerLayoutAndroid`和`ToolbarAndroid`

让我们从为 Android 版本的“朋友”添加基于工具栏/抽屉的导航开始。我们需要首先创建一个名为`Drawer`的组件，该组件执行以下功能：

+   这接受一个作为属性的路线数组。

+   这将返回一个包含每个路线作为行的`ListView`。每一行都应该包含一个`TouchableHighlight`组件，当点击时，将调用一个名为`navigateTo`的属性，我们最终将其传递到`Drawer`中。

我们还应该将`Drawer`渲染的行拆分成一个名为`DrawerRow`的独立组件。这个组件应该执行以下操作：

+   接受行的名称作为属性并在`Text`元素中渲染该名称

+   调用`setNativeProps`，以便其父`TouchableHighlight`组件将渲染此自定义组件

实例化一个新的`ListView.DataSource`：

```js
// Friends/app/components/Drawer/index.js 

import React, { Component } from 'react'; 

import { 
  ListView, 
  Text, 
  TouchableHighlight, 
  View 
} from 'react-native'; 

import DrawerRow from '../DrawerRow'; 
import styles from './styles'; 

export default class Drawer extends Component { 
  constructor (props) { 
    super (props); 
    this.state = { 
      ds: new ListView.DataSource({ 
        rowHasChanged: (r1, r2) => r1 !== r2 
      }) 
    } 
  } 

```

渲染一个带有分隔符的`ListView`组件。将我们行的渲染委托给`_renderDrawerRow`方法：

```js
  render () { 
    const dataSource = this.state.ds.cloneWithRows
    (this.props.routes || []); 
    return ( 
      <View style={ styles.container }> 
        <ListView 
          automaticallyAdjustContentInsets={ false } 
          dataSource={ dataSource } 
          enableEmptySections={ true } 
          renderRow={ (rowData, sectionID, rowID) => 
          this._renderDrawerRow(rowData, sectionID, rowID) } 
          renderSeparator={ (sectionID, rowID) => 
            <View 
              key={ rowID } 
              style={ styles.separator } /> 
          } /> 
      </View> 
    ) 
  } 

```

在自定义`DrawerRow`组件周围包裹一个`TouchableHighlight`，传递给它路由的名称。在`TouchableHighlight`的`onPress`方法中调用 props 中的`navigateTo`方法，传递给它`row`的`index`：

```js
  _renderDrawerRow (rowData, sectionID, rowID) { 
    return ( 
      <View> 
        <TouchableHighlight 
          style={ styles.row } 
          onPress={ () => this.props.navigateTo(rowData.index) }> 
          <DrawerRow 
            routeName={ rowData.title } /> 
          </TouchableHighlight> 
      </View> 
    ) 
  } 
} 

```

接下来，创建了`DrawerRow`组件：

```js
// Friends/app/components/Drawer/styles.js 

import { StyleSheet } from 'react-native'; 

const styles = StyleSheet.create({ 
  container: { 
    flex: 1 
  }, 
  separator: { 
    height: StyleSheet.hairlineWidth, 
    marginLeft: 10, 
    marginRight: 10, 
    backgroundColor: '#000000' 
  } 
}) 

export default styles; 

```

以下代码调用`setNativeProps`，因为`DrawerRow`被包裹在`TouchableHighlight`中：

```js
// Friends/app/components/DrawerRow/index.js 

import React, { Component } from 'react'; 

import { 
  Text, 
  View 
} from 'react-native'; 

import styles from './styles'; 

export default class DrawerRow extends Component { 
  setNativeProps (props) { 
    this._root.setNativeProps(props) 
  } 

```

渲染路由的名称：

```js
  render () { 
    return ( 
      <View 
        style={ styles.container } 
        ref={ component => this._root = component } 
        { ...this.props }> 
        <Text style={ styles.rowTitle }> 
          { this.props.routeName } 
        </Text> 
      </View> 
    ) 
  } 
} 

```

这里是我为`DrawerRow`创建的样式：

```js
// Friends/app/components/DrawerRow/styles.js 

import { StyleSheet } from 'react-native'; 

const styles = StyleSheet.create({ 
  container: { 
    flex: 1, 
    height: 40, 
    padding: 10 
  }, 
  rowTitle: { 
    fontSize: 20, 
    textAlign: 'left' 
  } 
}) 

export default styles; 

```

# 将抽屉与朋友集成

接下来，我们将修改根`index.android.js`文件，使其执行以下操作：

+   渲染一个包裹着`Icon.ToolbarAndroid`和`Navigator`的`DrawerLayoutAndroid`组件。

+   导入并设置`DrawerLayoutAndroid`的`renderNavigationView`为创建的`Drawer`组件。

+   创建一个回调以打开`DrawerLayoutAndroid`。

+   编写一个名为`_navigateTo`的回调，用于导航到给定的索引。将其作为属性传递给`LoginPage`。

+   使用`Navigator`中的`renderScene`回调导入并渲染`App`、`LoginPage`、`PostView`和`WebViewComponent`组件：

```js
// Friends/index.android.js 

import React, { Component } from 'react'; 
 import { 
  AppRegistry, 
  DrawerLayoutAndroid, 
  Navigator, 
  StyleSheet, 
  View 
} from 'react-native'; 

import App from './app/App'; 
import Drawer from './app/components/Drawer'; 
import LoginPage from './app/components/LoginPage'; 
import PostView from './app/components/PostView'; 
import WebViewComponent from './app/components/WebViewComponent'; 

import Icon from 'react-native-vector-icons/MaterialIcons'; 

```

让我们导入所有必要的依赖项，包括 React Native SDK 组件/API、`Navigator`渲染的每个自定义组件，以及来自`react-native-vector-icons`的 Material 图标包。

```js
export default class Friends extends Component { 
  constructor (props) { 
    super (props); 

    this.state = { 
      visibleRoutes: [ 
        { title: 'My Feed', index: 0 }, 
        { title: 'Log Out ', index: 1 } 
      ] 
    } 
  } 

```

建立要传递给`Drawer`组件的可见路由数组。

```js
  render() { 
    const routes = [ 
      { title: 'My Feed', index: 0 }, 
      { title: 'Sign In/Log Out', index: 1 }, 
      { title: 'Post Details', index: 2 }, 
      { title: 'Web View', index: 3 } 
    ]; 

    return ( 
      <View style={styles.container}> 
        <DrawerLayoutAndroid 
          drawerLockMode={ 'unlocked' } 
          ref={ 'drawer' } 
          renderNavigationView={ () => this._renderDrawerLayout() } 
        > 

```

渲染一个`DrawerLayoutAndroid`组件，其`renderNavigationView`属性委托给`_renderDrawerLayout`；给组件设置一个`ref`为`drawer`，这样我们就可以在`_openDrawer`中引用它。

```js
          <Icon.ToolbarAndroid 

            titleColor="#fafafa" 
            navIconName="menu" 
            height={ 56 } 
            backgroundColor="#365899" 
            onIconClicked={ () => this._openDrawer() } 
          /> 

```

渲染`Icon.ToolbarAndroid`组件以包含汉堡菜单。它的`onIconClicked`回调执行`_openDrawer`。

```js
          <Navigator 
            initialRoute={{ index: 0 }} 
            ref={ 'navigator' } 
            renderScene={ (routes, navigator) => 
            this._renderScene(routes, navigator) } 
          /> 
        </DrawerLayoutAndroid> 
      </View> 
    ); 
  } 

```

渲染`Navigator`，将其初始路由设置为`App`组件的`index`。将`renderScene`委托给`_renderScene`方法。给`navigator`一个`ref`，这样我们就可以在`_navigateTo`中引用它。

```js
  _checkLoginStatus () { 
    this._navigateTo(0); 
  } 

```

上述代码导航到`App`组件，这会触发它检查用户的登录状态。

```js
  _openDrawer () { 
    this.refs['drawer'].openDrawer(); 
  } 

```

`_openDrawer`方法在`DrawerLayoutAndroid`组件上调用`openDrawer`。

```js
  _navigateTo (index) { 
    this.refs['navigator'].push({ 
      index, 
      passProps: { 
        checkLoginStatus: () => this._checkLoginStatus() 
      } 
    }); 
    this.refs['drawer'].closeDrawer(); 
  } 

```

`_navigateTo`方法将给定的`index`推送到`navigator`。给定一个`checkLoginStatus`属性，该属性将被用于`LoginPage`组件。最后关闭`drawer`。

```js
  _renderDrawerLayout () { 
    return ( 
      <Drawer 
        navigateTo={ (index) => this._navigateTo(index) } 
        routes={ this.state.visibleRoutes } 
      /> 
    ); 
  } 

```

`_renderDrawerLayout`方法渲染`Drawer`组件，将其`_navigateTo`方法作为属性传递，以及路由数组。

```js
  _renderScene (route, navigator) { 
    if (route.index === 0) { 
      return ( 
        <App 
          title={ route.title } 
          navigator={ navigator } 
        /> 
      ); 
    } 

```

`_renderScene`方法负责渲染所有四个可用的路由。

```js
    if (route.index === 1) { 
      return ( 
        <LoginPage 
          title={ route.title } 
          navigator={ navigator } 
          { ...route.passProps } 
        /> 
      ); 
    } 

    if (route.index === 2) { 
      return ( 
        <PostView 
          title={ route.title } 
          navigator={ navigator } 
          { ...route.passProps } 
        /> 
      ); 
    } 

    if (route.index === 3) { 
      return ( 
        <WebViewComponent 
          title={ route.title } 
          navigator={ route.navigator } 
          { ...route.passProps } 
        /> 
      ); 
    } 
  } 

} 

const styles = StyleSheet.create({ 
  container: { 
    flex: 1, 
    backgroundColor: '#F5FCFF', 
  } 
}); 

AppRegistry.registerComponent('Friends', () => Friends); 

```

# 创建 App.js 的 Android 版本

现在，我们应该为“朋友”创建一个特定的 Android `App`组件。首先，将位于`Friends/app/App.js`的现有`App.js`文件重命名为`App.ios.js`，并创建一个名为`App.android.js`的新文件。

此文件应包含与`App.ios.js`类似的逻辑，但应删除任何对 iOS 特定组件的引用，例如`TabBarIOS`。此外，任何导航事件应更新以支持`Navigator`逻辑。

这是我的做法：

```js
// Friends/app/App.android.js 

... 

```

以下三个项目从导入语句中删除：`NavigatorIOS`, `TabBarIOS`, 和 `LoginPage`：

```js
export default class App extends Component { 
  constructor (props) { 
    ... 
  } 

```

状态中的 `selectedTab` 属性从 `constructor` 中移除：

```js
  ... 
  async _checkLoginStatus () { 
    ... 
    if (result === null) { 
      this.props.navigator.push({ 
        index: 1, 
        passProps: { 
          getFeed: () => this._getFeed() 
        } 
      }); 

      return; 
    } 
    ... 
  } 

```

`componentWillMount` 和 `render` 方法与 iOS 版本保持一致。在 `_checkLoginStatus` 中的导航方法被修改为传递一个 `index` 而不是 `LoginPage` 组件：

```js
... 
_renderView () { 
    ... 
    return this._renderFeedList(); 
  } 

```

`_getFeed`, `_renderFeedList`, 和 `_renderLoginPrompt` 方法也没有被修改。在 `_renderView` 中，我不再返回 `TabBarIOS`，而是返回对 `_renderFeedList` 的调用。

```js
  ... 
} 

```

最后，`_refreshFeedList` 和 `_responseInfoCallback` 方法也没有改变。然而，由于 `_setSelectedTab` 是一个 `TabBarIOS` 特定的方法，所以它从 `App.android.js` 中被移除。

# 修改 `FeedList`

在 Android 上，`FeedList` 的样式需要根据条件进行更改，以便其 `container` 样式不包含 `marginTop` 属性。修改 `FeedList` 以执行以下功能：

+   从 React Native 中导入 `Platform` API。

+   条件检查用户的平台，并根据检查结果在 iOS 设备上提供容器样式或一个新的不包含 `marginTop` 属性的 Android 特定样式。

这里是我的 `FeedList` 对 Android 的修改：

```js
// Friends/app/components/FeedList/index.js 

... 
import { 
  Platform, 
  ... 
} from 'react-native'; 
... 
export default class FeedList extends Component { 
  ... 
  render () { 
    ... 
    return ( 
      <View style={ Platform.OS === 'ios' ? styles.container : 
      styles.androidContainer }> 
        ... 
      </View> 
    ) 
  } 
  ... 
} 

```

我导入了 `Platform` API 并使用三元运算符来检查用户的操作系统，根据检查结果在 `FeedList` 的 `render` 方法中将顶层 `View` 组件分配一个适用的样式：

```js
// Friends/app/components/FeedList/styles.js 

   androidContainer: { 
    flex: 1 
  }, 

```

我将 `androidContainer` 样式添加到 `FeedList` 的 `StyleSheet` 中。

# 在 `FeedListRow` 中支持 `Navigator`

接下来，我们必须更新 `FeedListRow` 以执行以下操作：

+   导入 `Platform` API

+   修改 `navigateToPostView` 以检查用户的操作系统并使用适当的语法为每个操作系统推送 `PostView`

我创建了 `propsObject` 来存储分配给 `passProps` 的对象，这样我就不必再次重写它：

```js
// Friends/app/components/FeedListRow/index.js 

... 
import { 
  Platform, 
  ... 
} from 'react-native'; 
... 
export default class FeedListRow extends Component { 
  ... 
  _navigateToPostView () { 
    const propsObject = { 
      createdTime: this.props.createdTime, 
      message: this.props.message, 
      postID: this.props.postID, 
      story: this.props.story 
    }; 

```

这里我们查看 iOS 的条件逻辑：

```js
    if (Platform.OS === 'ios') { 
      this.props.navigator.push({ 
        component: PostView, 
        passProps: propsObject 
      }); 
      return; 
    } 

```

由于 iOS 逻辑以 `return` 语句结束，所以在 Android 上使用 `Navigator` 的 `push`。

```js
    this.props.navigator.push({ 
      index: 2, 
      passProps: propsObject 
    }); 
  } 
  ... 
} 

```

# 添加 `PostView` 导航器和 `BackAndroid` 支持

现在，让我们对 `PostView` 组件进行以下修改：

+   导入 `Platform` 和 `BackAndroid` API

+   在 `componentWillMount` 和 `componentWillUnmount` 中添加和移除 `BackAndroid` 的监听器。

+   在组件中编写一个回调来处理 Android 上的返回按钮点击，结果调用导航器的 `pop`。

+   创建类似于 `FeedListRow` 的条件逻辑来推送 `WebViewComponent`

我在 `componentWillMount` 生命周期中创建了一个 `BackAndroid` 的事件监听器：

```js
// Friends/app/components/PostView/index.js 

... 
import { 
  BackAndroid, 
  Platform, 
  ... 
} from 'react-native'; 
... 
export default class PostView extends Component { 
  ... 
  async componentWillMount () { 
    BackAndroid.addEventListener('hardwareButtonPress', () =>
    this._backButtonPress()); 
    ... 
  } 

```

同样，我在 `componentWillUnmount` 中移除了那个事件监听器：

```js
  componentWillUnmount () { 
    BackAndroid.removeEventListener('hardwareButtonPress', () =>
    this._backButtonPress()) 
  } 

```

此方法在按下返回按钮时在 `navigator` 上调用 `pop`：

```js
  ... 
  _backButtonPress () { 
    this.props.navigator.pop(); 
    return true; 
  } 

```

在 iOS 上推送 `WebViewComponent` 的条件逻辑如下：

```js
  ... 
  _renderWebView (url) { 
    if (Platform.OS === 'ios') { 
      this.props.navigator.push({ 
        component: WebViewComponent, 
        passProps: { 
          url 
        } 
      }); 
      return; 
    } 

```

相同功能的条件逻辑，但在 Android 上如下：

```js
    this.props.navigator.push({ 
      index: 3, 
      passProps: { 
        url 
      } 
    }); 
  } 
} 

```

# 摘要

恭喜！您已经成功构建了三款 React Native 应用程序，贯穿整本书的学习过程。在本章中，您学习了如何将下拉刷新交互添加到应用程序中，让您的应用用户能够通过一个众所周知的手势快速刷新数据。然后，您使用了`Image`组件，将远程图片渲染到您的应用程序中。接下来，您为应用程序创建了一个`WebView`组件，使用户能够在不离开应用进入系统浏览器的情况下查看与 Web 相关的内容。最后，您进行了必要的修改，以创建应用程序的 Android 版本。
