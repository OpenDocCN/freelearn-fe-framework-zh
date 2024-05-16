# 第四章：使用 Dropbox API 获取文件列表

在接下来的几章中，我们将构建一个基于 Vue 的 Dropbox 浏览器。这个应用程序将使用你的 Dropbox API 密钥，允许你浏览文件夹并下载文件。你将学习如何在 Vue 应用程序中与 API 进行交互，了解 Vue 的生命周期钩子，包括`created()`方法，最后我们将介绍一个名为`Vuex`的库来处理应用程序的缓存和状态。该应用程序将具有可共享的 URL，并通过`#`URL 参数检索文件夹的内容。

如果你想让用户访问你的 Dropbox 内容而不提供用户名和密码，这种应用程序将非常有用。但要注意，一个精通技术的用户可能会在代码中找到你的 API 密钥并滥用它，所以不要将这段代码发布到互联网上。

本章将涵盖以下内容：

+   加载和查询 Dropbox API

+   列出你的 Dropbox 账户中的目录和文件

+   为你的应用程序添加加载状态

+   使用 Vue 动画

在接下来的几章中，你将需要一个 Dropbox 账户。如果你没有账户，请注册并添加一些虚拟文件和文件夹。Dropbox 的内容并不重要，但有文件夹可以帮助你理解代码。

# 开始加载库

为你的应用程序创建一个新的 HTML 页面运行。创建一个网页所需的 HTML 结构，并包含你的应用程序视图包装器：

```js
      <!DOCTYPE html>
      <html>
      <head>
        <title>Dropbox App</title>
      </head>
      <body>  
 <div id="app">
 </div>  
      </body>
      </html>
```

这里称之为`#app`，但你可以随意命名 - 只需记得更新 JavaScript。

由于我们的应用程序代码将变得相当庞大，所以创建一个单独的 JavaScript 文件，并将其包含在文档底部。你还需要包含 Vue 和 Dropbox API SDK。

与之前一样，你可以引用远程文件或下载库文件的本地副本。出于速度和兼容性的原因，下载一个本地副本。在 HTML 文件的底部包含你的三个 JavaScript 文件：

```js
      <script src="js/vue.js"></script>
      <script src="js/dropbox.js"></script>
      <script src="js/app.js"></script>
```

创建你的`app.js`并初始化一个新的 Vue 实例，使用`el`标签将实例挂载到视图中的 ID 上。

```js
      new Vue({
          el: '#app'
      });
```

# 创建一个 Dropbox 应用程序并初始化 SDK

在与 Vue 实例交互之前，我们需要通过 SDK 连接到 Dropbox API。这是通过 Dropbox 自动生成的 API 密钥来完成的，用于跟踪连接到你的账户的内容和位置，Dropbox 要求你创建一个自定义的 Dropbox 应用程序。

前往 Dropbox 开发者区域并选择创建你的应用。选择 Dropbox API 并选择一个受限文件夹或完全访问权限。这取决于你的需求，但是为了测试，选择完全访问权限。给你的应用取一个名字并点击创建应用按钮。

为你的应用生成一个访问令牌。要这样做，在查看应用详情页面时，点击“生成访问令牌”下的“生成”按钮。这将给你一个由数字和字母组成的长字符串 - 将其复制并粘贴到你的编辑器中，并将其作为一个变量存储在你的 JavaScript 的顶部。在本书中，API 密钥将被称为`XXXX`：

```js
      /**
       * API Access Token
       */
      let accessToken = 'XXXX';
```

现在我们有了 API 密钥，我们可以访问我们 Dropbox 中的文件和文件夹。初始化 API 并将你的`accessToken`变量传递给 Dropbox API 的`accessToken`属性：

```js
      /**
      * Dropbox Client
      * @type {Dropbox}
      */
      const dbx = new Dropbox({
        accessToken: accessToken
      });
```

现在我们可以通过`dbx`变量访问 Dropbox。我们可以通过连接并输出根路径的内容来验证我们与 Dropbox 的连接是否正常：

```js
      dbx.filesListFolder({path: ''})
          .then(response => {
            console.log(response.entries);
          })
          .catch(error => {
            console.log(error);
          });
```

这段代码使用了 JavaScript 的 Promise，它是一种在代码中添加操作而不需要回调函数的方法。如果你对 Promise 不熟悉，可以查看 Google 的这篇博文（[`developers.google.com/web/fundamentals/primers/promises`](https://developers.google.com/web/fundamentals/primers/promises)）。

注意第一行，特别是`path`变量。这使我们可以传递一个文件夹路径来列出该目录中的文件和文件夹。例如，如果你的 Dropbox 中有一个名为`images`的文件夹，你可以将参数值更改为`/images`，返回的文件列表将是该目录中的文件和文件夹。

打开你的 JavaScript 控制台并检查输出；你应该得到一个包含多个对象的数组 - 每个对象对应你 Dropbox 根目录中的一个文件或文件夹。

# 显示你的数据并使用 Vue 获取它

现在我们可以使用 Dropbox API 检索我们的数据了，是时候在我们的 Vue 实例中检索它并在我们的视图中显示了。这个应用将完全使用组件构建，这样我们就可以利用分隔的数据和方法。这也意味着代码是模块化和可共享的，如果你想将其集成到其他应用中。

我们还将利用 Vue 的原生`created()`函数 - 我们将在稍后讨论它被触发的时候。

# 创建组件

首先，在你的视图中创建自定义的 HTML 元素`<dropbox-viewer>`。在页面底部创建一个`<script>`模板块，用于我们的 HTML 布局：

```js
      <div id="app">
        <dropbox-viewer></dropbox-viewer>
      </div> 
      <script type="text/x-template" id="dropbox-viewer-          
       template">
        <h1>Dropbox</h1>
      </script>
```

在`app.js`文件中初始化你的组件，将其指向模板 ID：

```js
      Vue.component('dropbox-viewer', {
        template: '#dropbox-viewer-template'
      });
```

在浏览器中查看应用程序应该显示模板中的标题。下一步是将 Dropbox API 集成到组件中。

# 检索 Dropbox 数据

创建一个名为`dropbox`的新方法。在其中，将调用 Dropbox 类并返回实例的代码移动到这里。现在通过调用`this.dropbox()`可以通过组件访问 Dropbox API：

```js
      Vue.component('dropbox-viewer', {
        template: '#dropbox-viewer-template',  
        methods: {
 dropbox() {
 return new Dropbox({
 accessToken: this.accessToken
 });
 }
 }
      });
```

我们还要将 API 密钥集成到组件中。创建一个返回包含你的访问令牌的对象的数据函数。更新 Dropbox 方法，使用本地版本的密钥：

```js
      Vue.component('dropbox-viewer', {
        template: '#dropbox-viewer-template',  
        data() {
 return {
 accessToken: 'XXXX'
 }
 },
        methods: {
          dropbox() {
            return new Dropbox({
              accessToken: this.accessToken
            });
          }
        }
      });
```

现在我们需要为组件添加获取目录列表的功能。为此，我们将创建另一个方法，它接受一个参数-路径。这将使我们以后能够根据需要请求不同路径或文件夹的结构。

使用之前提供的代码-将`dbx`变量更改为`this.dropbox()`：

```js
      getFolderStructure(path) {
        this.dropbox().filesListFolder({path: path})
        .then(response => {
          console.log(response.entries);
        })
        .catch(error => {
          console.log(error);
        });
      }
```

更新 Dropbox 的`filesListFolder`函数，接受传入的路径参数，而不是固定值。在浏览器中运行此应用程序将显示 Dropbox 标题，但不会检索任何文件夹，因为尚未调用这些方法。

# Vue 生命周期钩子

这就是`created()`函数的作用。`created()`函数在 Vue 实例初始化数据和方法后调用，但尚未将实例挂载到 HTML 组件上。在生命周期的不同阶段还有其他几个可用的函数；关于这些函数的更多信息可以在 Alligator.io 上阅读。生命周期如下：

![](img/02ada895-fdcb-4485-af9c-4c0b4d76c68d.png)

使用`created()`函数使我们能够在 Vue 挂载应用程序时访问方法和数据，并能够启动我们的检索过程。这些不同阶段之间的时间是分秒必争的，但在性能和创建快速应用程序方面，每一刻都很重要。如果我们可以提前开始任务，就没有必要等待应用程序完全挂载。

在你的组件上创建`created()`函数，并调用`getFolderStructure`方法，传入一个空字符串作为获取 Dropbox 根目录的路径：

```js
      Vue.component('dropbox-viewer', {
        template: '#dropbox-viewer-template',  
        data() {
          return {
            accessToken: 'XXXX'
          }
        }, 
        methods: {
         ...
        }, 
        created() {
 this.getFolderStructure('');
 }
      });
```

现在在浏览器中运行应用程序，将文件夹列表输出到控制台，结果应与之前相同。

现在我们需要在视图中显示文件列表。为此，我们将在组件中创建一个空数组，并用我们的 Dropbox 查询结果填充它。这样做的好处是，即使在没有任何内容之前，Vue 也可以在视图中循环遍历一个变量。

# 显示 Dropbox 数据

在数据对象中创建一个名为`structure`的新属性，并将其赋值为空数组。在文件夹检索的响应函数中，将`response.entries`赋值给`this.structure`。保留`console.log`，因为我们需要检查条目以确定在模板中输出什么：

```js
      Vue.component('dropbox-viewer', {
        template: '#dropbox-viewer-template', 
        data() {
          return {
            accessToken: 'XXXX',
            structure: []
          }
        },
        methods: {
          dropbox() {
            return new Dropbox({
              accessToken: this.accessToken
            });
          },
          getFolderStructure(path) {
            this.dropbox().filesListFolder({path: path})
            .then(response => {
              console.log(response.entries);
              this.structure = response.entries;
            })
            .catch(error => {
              console.log(error);
            });
          }
        },  
        created() {
          this.getFolderStructure('');
        }
      });
```

现在我们可以更新视图以显示来自 Dropbox 的文件夹和文件。由于结构数组在视图中可用，因此创建一个可重复的`<li>`循环遍历结构的`<ul>`。

由于我们现在添加了第二个元素，Vue 要求模板必须包含一个包含元素的容器，所以将标题和列表包装在一个`<div>`中：

```js
      <script type="text/x-template" id="dropbox-viewer-         
        template">
        <div>
          <h1>Dropbox</h1>
          <ul>
 <li v-for="entry in structure">
 </li>
 </ul>
 </div>
      </script>
```

在浏览器中查看应用程序时，当数组出现在 JavaScript 控制台中时，将显示一些空的项目符号。为了确定可以显示哪些字段和属性，请展开 JavaScript 控制台中的数组，然后进一步展开每个对象。您应该注意到每个对象都有一组相似的属性和一些在文件夹和文件之间有所不同的属性。

第一个属性`.tag`帮助我们确定该项是文件还是文件夹。然后，这两种类型都具有以下共同属性：

+   `id`: Dropbox 的唯一标识符。

+   `name`: 文件或文件夹的名称，与该项所在位置无关。

+   `path_display`: 项目的完整路径，与文件和文件夹的大小写匹配。

+   `path_lower`: 与`path_display`相同，但全部小写。

`.tag`为文件的项还包含了一些更多的字段供我们显示：

+   `client_modified`: 文件添加到 Dropbox 的日期。

+   `content_hash`: 文件的哈希值，用于判断它是否与本地或远程副本不同。关于此内容可以在 Dropbox 网站上阅读更多信息。

+   `rev`: 文件版本的唯一标识符。

+   `server_modified`: 文件在 Dropbox 上最后修改的时间。

+   `size`: 文件的大小（以字节为单位）。

首先，我们将显示项目的名称和大小（如果有）。更新列表项以显示这些属性：

```js
      <li v-for="entry in structure">
        <strong>{{ entry.name }}</strong>
        <span v-if="entry.size"> - {{ entry.size }}</span>
      </li>
```

# 更多文件元信息

为了使我们的文件和文件夹视图更有用，我们可以为图像等文件添加更多丰富的内容和元数据。通过在 Dropbox API 中启用`include_media_info`选项，可以获得这些详细信息。

返回到您的`getFolderStructure`方法，并在`path`之后添加该参数。以下是一些新的可读性行：

```js
      getFolderStructure(path) {
        this.dropbox().filesListFolder({
          path: path, 
          include_media_info: true
        })
        .then(response => {
          console.log(response.entries);
          this.structure = response.entries;
        })
        .catch(error => {
          console.log(error);
        });
      }
```

检查这个新 API 调用的结果将显示视频和图像的`media_info`键。展开它将显示有关文件的更多信息，例如尺寸。如果您想添加这些信息，您需要在显示信息之前检查`media_info`对象是否存在：

```js
      <li>
        <strong>{{ f.name }}</strong>
        <span v-if="f.size"> - {{ bytesToSize(f.size) }}          
        </span> - 
        <span v-if="f.media_info">
 [
 {{ f.media_info.metadata.dimensions.width }}px x 
 {{ f.media_info.metadata.dimensions.height }}px
 ]
 </span>
      </li>
```

尝试在从 Dropbox 检索数据时更新路径。例如，如果您有一个名为`images`的文件夹，将`this.getFolderStructure`参数更改为`/images`。如果您不确定路径是什么，请在 JavaScript 控制台中分析数据，并复制文件夹的`path_lower`属性的值，例如：

```js
      created() {
        this.getFolderStructure('/images');
      }
```

# 格式化文件大小

由于文件大小以纯字节输出，对于用户来说可能很难理解。为了解决这个问题，我们可以添加一个格式化方法来输出一个更用户友好的文件大小，例如显示`<q>1kb</q>`而不是`<q>1024</q>`。

首先，在数据对象上创建一个包含单位数组的新键`byteSizes`：

```js
      data() {
        return {
          accessToken: 'XXXX',
          structure: [],
          byteSizes: ['Bytes', 'KB', 'MB', 'GB', 'TB']
        }
      }
```

这是将附加到图形的内容，所以可以将这些属性设置为小写或全词，例如*megabyte*。

接下来，在您的组件中添加一个新的方法`bytesToSize`。它将接受一个`bytes`参数，并输出一个带有单位的格式化字符串：

```js
      bytesToSize(bytes) {
        // Set a default
        let output = '0 Byte'; 
        // If the bytes are bigger than 0
        if (bytes > 0) {
          // Divide by 1024 and make an int
          let i = parseInt(Math.floor(Math.log(bytes) /              
           Math.log(1024)));
          // Round to 2 decimal places and select the                 
             appropriate unit from the array
            output = Math.round(bytes / Math.pow(1024, i), 
              2) + ' ' + this.byteSizes[i];
            }
            return output
          }
```

现在我们可以在视图中利用这个方法：

```js
      <li v-for="entry in structure">
        <strong>{{ entry.name }}</strong>
        <span v-if="entry.size"> - {{ 
        bytesToSize(entry.size) }}</span>
      </li>
```

# 添加一个加载屏幕

本章的最后一步是为我们的应用程序创建一个加载屏幕。这将告诉用户应用程序正在加载，如果 Dropbox API 运行缓慢（或者您有很多数据要显示！）。

这个加载屏幕背后的理论相当基本。我们将默认将加载变量设置为`true`，一旦数据加载完成，它将被设置为`false`。根据这个变量的结果，我们将利用视图属性来显示和隐藏带有加载文本或动画的元素，并显示加载的数据列表。

在数据对象中创建一个名为`isLoading`的新键。将这个变量默认设置为`true`：

```js
      data() {
        return {
          accessToken: 'XXXX',
          structure: [],
          byteSizes: ['Bytes', 'KB', 'MB', 'GB', 'TB'],
          isLoading: true
        }
      }
```

在组件的`getFolderStructure`方法中，将`isLoading`变量设置为`false`。这应该在您设置结构之后的 promise 中发生：

```js
      getFolderStructure(path) {
        this.dropbox().filesListFolder({
          path: path, 
          include_media_info: true
        })
        .then(response => {
          console.log(response.entries);
          this.structure = response.entries;
          this.isLoading = false;
        })
        .catch(error => {
          console.log(error);
        });
      }
```

现在我们可以在视图中利用这个变量来显示和隐藏加载容器。

在无序列表之前创建一个新的`<div>`，其中包含一些加载文本。随意添加 CSS 动画或动画 gif-任何东西都可以让用户知道应用程序正在检索数据：

```js
      <h1>Dropbox</h1>
 <div>Loading...</div>
      <ul>
      ...
```

现在，我们只需要在应用程序加载时显示加载 div，数据加载后显示列表。由于这只是对 DOM 的一个更改，我们可以使用`v-if`指令。为了让您可以自由地重新排列 HTML，将属性添加到两者而不是使用`v-else`。

要显示或隐藏，我们只需要检查`isLoading`变量的状态。我们可以在列表前面添加一个感叹号，只有在应用程序没有加载时才显示：

```js
      <div>
        <h1>Dropbox</h1>
        <div v-if="isLoading">Loading...</div>
         <ul v-if="!isLoading">
          <li v-for="entry in structure">
            <strong>{{ entry.name }}</strong>
            <span v-if="entry.size">- {{ 
             bytesToSize(entry.size) }}</span>
          </li>
        </ul>
      </div>
```

我们的应用程序现在应该在挂载后显示加载容器，然后在收集到应用程序数据后显示列表。总结一下，我们完整的组件代码现在看起来像这样：

```js
      Vue.component('dropbox-viewer', {
        template: '#dropbox-viewer-template',
        data() {
          return {
            accessToken: 'XXXX',
            structure: [],
            byteSizes: ['Bytes', 'KB', 'MB', 'GB', 'TB'],
            isLoading: true
          }
        },
        methods: {
          dropbox() {
            return new Dropbox({
              accessToken: this.accessToken
            });
          },
          getFolderStructure(path) {
            this.dropbox().filesListFolder({
              path: path, 
              include_media_info: true
            })
            .then(response => {
              console.log(response.entries);
              this.structure = response.entries;
              this.isLoading = false;
            })
            .catch(error => {
              console.log(error);
            });
          },

          bytesToSize(bytes) {
            // Set a default
            let output = '0 Byte';

            // If the bytes are bigger than 0
            if (bytes > 0) {
              // Divide by 1024 and make an int
              let i = parseInt(Math.floor(Math.log(bytes)               
              / Math.log(1024)));
              // Round to 2 decimal places and select the                 
                appropriate unit from the array
                output = Math.round(bytes / Math.pow(1024, 
                i), 2) + ' ' + this.byteSizes[i];
            }
           return output
          }
        },
        created() {
          this.getFolderStructure('');
        }
      });
```

# 在状态之间进行动画切换

作为对用户的一个很好的增强，我们可以在组件和状态之间添加一些过渡效果。幸运的是，Vue 包含了一些内置的过渡效果。通过使用 CSS，这些过渡效果允许您在插入 DOM 元素时轻松地添加淡入淡出、滑动和其他 CSS 动画。有关过渡的更多信息可以在 Vue 文档中找到。

第一步是添加 Vue 自定义 HTML `<transition>`元素。用单独的过渡元素包裹您的加载和列表，并给它一个`name`属性和一个`fade`值：

```js
      <script type="text/x-template" id="dropbox-viewer-      
       template">
        <div>
          <h1>Dropbox</h1>
          <transition name="fade">
            <div v-if="isLoading">Loading...</div>
          </transition>
          <transition name="fade">
            <ul v-if="!isLoading">
              <li v-for="entry in structure">
                <strong>{{ entry.name }}</strong>
                <span v-if="entry.size">- {{         
                bytesToSize(entry.size) }}</span>
              </li>
            </ul>
          </transition>
        </div>
</script>
```

现在将以下 CSS 添加到您的文档头部或一个单独的样式表中（如果您已经有一个）：

```js
      .fade-enter-active,
      .fade-leave-active {
        transition: opacity .5s
      }
      .fade-enter, 
      .fade-leave-to {
        opacity: 0
      }
```

使用过渡元素，Vue 根据过渡的状态和时间添加和删除各种 CSS 类。所有这些类都以通过属性传递的名称开头，并附加有关过渡当前阶段的信息：

![](img/e49dfffb-4158-44a3-abcb-0a1955bb862e.png)

在浏览器中尝试该应用程序，您应该注意到加载容器的淡出和文件列表的淡入。尽管在这个基本示例中，列表在淡出完成后会跳动一次，但这是一个示例，可以帮助您了解在 Vue 中使用过渡效果。

# 总结

在本章中，我们学习了如何制作一个 Dropbox 查看器，它是一个单页面应用程序，可以列出我们 Dropbox 账户中的文件和文件夹，并允许我们通过更新代码来显示不同的文件夹内容。我们学习了如何为我们的应用程序添加基本的加载状态，并使用 Vue 动画进行导航。

在第五章中，*通过文件树导航和从 URL 加载文件夹*，我们将浏览我们的应用程序文件夹，并为我们的文件添加下载链接。
