# 第六章：使用 Vuex 缓存当前文件夹结构

在本章中，我们将介绍一个名为 Vuex 的官方 Vue 插件。Vuex 是一种状态管理模式和库，允许您为所有 Vue 组件创建一个集中的存储，无论它们是子组件还是 Vue 实例。它为我们提供了一种集中、简单的方式来保持整个应用程序中的数据同步。

本章将涵盖以下内容：

+   开始使用 Vuex

+   从 Vuex 存储中存储和检索数据

+   将 Vuex 与我们的 Dropbox 应用程序集成

+   缓存当前 Dropbox 文件夹内容，并在需要时从存储中加载数据

不再需要在每个组件上使用自定义事件和`$emit`函数，并尝试保持组件和子组件的更新，您的 Vue 应用程序的每个部分都可以更新中央存储，并且其他部分可以根据该信息来更新其数据和状态。它还为我们提供了一个共同的存储数据的地方，因此，我们不再需要决定将数据对象放在组件、父组件还是 Vue 实例上更具语义性，我们可以使用 Vuex 存储。

Vuex 还集成到 Vue 开发工具中，这将在第十二章“使用 Vue 开发工具和测试您的 SPA”中介绍。通过集成该库，可以轻松调试和查看存储的当前状态和过去状态。开发工具反映状态变化或数据更新，并允许您检查存储的每个部分。

如前所述，Vuex 是一种状态管理模式，是 Vue 应用程序的真相来源。例如，跟踪购物篮或已登录用户对于某些应用程序至关重要，如果这些数据在组件之间不同步，可能会造成严重问题。而且，如果没有使用父组件来处理数据交换，就无法在子组件之间传递数据。Vuex 通过处理数据的存储、变化和操作来解决这个问题。

![](img/02e6fa58-a9f3-4654-88d4-7747265331a2.png)

当初次使用 Vuex 时，它可能看起来相当冗长，似乎超出了所需的范围；然而，这是一个很好的例子，可以帮助我们熟悉该库。有关 Vuex 的更多信息可以在其文档中找到。

对于我们的 Dropbox 应用程序，Vuex store 可以用来存储文件夹结构、文件列表和下载链接。这意味着如果用户多次访问同一个文件夹，API 将不需要再次查询，因为所有的信息已经存储好了。这将加快文件夹的导航速度。

# 包含和初始化 Vuex

Vuex 库的包含方式与 Vue 本身相同。您可以使用之前提到的 unpkg 服务（[`unpkg.com/vuex`](https://unpkg.com/vuex)）使用托管版本，或者您可以从他们的[`github.com/vuejs/vuex`](https://github.com/vuejs/vuex)下载 JavaScript 库。

在 HTML 文件的底部添加一个新的`<script>`块。确保在应用程序 JavaScript 之前，Vuex 库被包含在`vue.js`库之后：

```js
<script type="text/javascript" src="js/vue.js"></script>
<script type="text/javascript" src="js/vuex.js"></script>
<script type="text/javascript" src="js/dropbox.js"></script>
<script type="text/javascript" src="js/app.js"></script>
```

如果您正在部署一个包含多个 JavaScript 文件的应用程序，值得研究一下是将它们合并压缩成一个文件更高效，还是配置服务器使用 HTTP/2 推送。

在包含了库之后，我们可以初始化并在我们的应用程序中包含 store。创建一个名为`store`的新变量，并初始化`Vuex.Store`类，并将其赋值给该变量：

```js
const store = new Vuex.Store({

});
```

初始化了 Vuex store 之后，我们现在可以使用`store`变量来利用其功能。使用`store`，我们可以访问其中的数据，并通过 mutations 来修改这些数据。通过一个独立的`store`，许多 Vue 实例可以更新同一个`store`；这在某些情况下可能是需要的，但在其他情况下可能是一个不希望的副作用。

为了绕过这个问题，我们可以将一个 store 与特定的 Vue 实例关联起来。通过将`store`变量传递给我们的 Vue 类来实现这一点。这样做也会将`store`实例注入到所有的子组件中。虽然对于我们的应用程序来说并不是必需的，但将 store 与应用程序关联起来是一个好的实践习惯：

```js
const app = new Vue({
  el: '#app',

  store,
  data: {
    path: ''
  }, 
  methods: {
    updateHash() {
      let hash = window.location.hash.substring(1);
      this.path = (hash || '');
    }
  },
  created() {
    this.updateHash()
  }
});
```

添加了`store`变量后，我们现在可以使用`this.$store`变量在我们的组件中访问`store`了。

# 利用 store

为了帮助我们掌握如何使用 store，让我们将当前存储在父 Vue 实例上的`path`变量移动起来。在我们开始编写和移动代码之前，有一些在使用 Vuex store 时不同的短语和词汇，我们应该熟悉一下：

+   `state`：这是 store 中数据对象的等效部分；原始数据存储在这个对象中。

+   `getters`：这些是 Vuex 中与计算值相当的函数；`store`的函数可以在返回组件中使用之前处理原始状态值。

+   `mutations`：Vuex 不允许直接在`store`之外修改状态对象，必须通过变异处理程序来完成；这些是`store`上的函数，然后允许更新状态。它们始终将`state`作为第一个参数。

这些对象直接属于`store`。但是，更新`store`并不像调用`store.mutationName()`那样简单。相反，我们必须使用一个新的`commit()`函数来调用该方法。此函数接受两个参数：变异的名称和传递给它的数据。

虽然最初很难理解，但 Vuex 存储的冗长性允许强大的功能。以下是一个使用存储的示例，将第一章“开始使用 Vue.js”的原始示例进行了调整：

```js
const store = new Vuex.Store({
  state: {
    message: 'HelLO Vue!'
  },

  getters: {
    message: state => {
      return state.message.toLowerCase();
    }
  },

  mutations: {
    updateMessage(state, msg) {
      state.message = msg;
    }
  }
});
```

前面的`store`示例包括`state`对象，这是我们的原始数据存储；`getters`对象，其中包括我们对状态的处理；最后，`mutations`对象，允许我们更新消息。请注意，`message` getter 和`updateMessage`变异都将存储的状态作为第一个参数。

要使用此`store`，您可以执行以下操作：

```js
new Vue({
  el: '#app',

  store,
  computed: {
    message() {
      return this.$store.state.message
    },
    formatted() {
      return this.$store.getters.message
    }
  }
});
```

# 检索消息

在`{{ message }}`计算函数中，我们从状态对象中检索了原始的、未经处理的消息，并使用了以下路径：

```js
this.$store.state.message
```

这实际上是访问`store`，然后是状态对象，然后是消息对象键。

类似地，`{{ formatted }}`计算值使用`store`中的 getter，将字符串转换为小写。这是通过访问`getters`对象来检索的：

```js
this.$store.getters.message
```

# 更新消息

要更新消息，您需要调用`commit`函数。这将接受方法名称作为第一个参数，负载或数据作为第二个参数。负载可以是简单变量、数组或对象，如果需要传递多个变量，则可以使用对象。

`store`中的`updateMessage`变异接受一个参数，并将消息设置为相等，因此要更新我们的消息，代码应该是：

```js
store.commit('updateMessage', 'VUEX Store');
```

这可以在应用程序的任何地方运行，并且将自动更新之前使用的值，因为它们都依赖于相同的`store`。

现在返回我们的消息 getter 将返回 VUEX Store，因为我们已经更新了状态。考虑到这一点，让我们更新我们的应用程序，使用`store`中的路径变量，而不是 Vue 实例。

# 使用 Vuex store 来获取文件夹路径

在使用 Vue store 作为全局 Dropbox 路径变量的第一步是将数据对象从 Vue 实例移动到`Store`，并将其重命名为`state`：

```js
const store = new Vuex.Store({
  state: {
 path: ''
 }
});
```

我们还需要创建一个 mutation，以允许从 URL 的哈希值更新路径。在 store 中添加一个`mutations`对象，并将`updateHash`函数从 Vue 实例中移动-不要忘记更新函数以接受 store 作为第一个参数。还要更改方法，使其更新`state.path`而不是`this.path`：

```js
const store = new Vuex.Store({
  state: {
    path: ''
  },
  mutations: {
 updateHash(state) {
 let hash = window.location.hash.substring(1);
 state.path = (hash || '');
 }
 }
});
```

通过将路径变量和 mutation 移动到 store 中，可以使 Vue 实例变得更小，同时删除`methods`和`data`对象：

```js
const app = new Vue({
  el: '#app',

  store,
  created() {
    this.updateHash()
  }
});
```

我们现在需要更新我们的应用程序，使用`store`中的路径变量，而不是在 Vue 实例上。我们还需要确保调用`store`的`mutation`函数来更新路径变量，而不是在 Vue 实例上的方法。

# 更新路径方法以使用 store commits

从 Vue 实例开始，将`this.Updatehash`更改为`store.commit('updateHash')`。不要忘记还要更新`onhashchange`函数中的方法。第二个函数应该引用我们 Vue 实例上的`store`对象，而不是直接引用`store`。这可以通过访问 Vue 实例变量`app`，然后在此实例中引用 Vuex store 来完成。

在 Vue 实例上引用 Vuex store 时，它保存在变量`$store`下，无论最初对该变量进行了什么样的声明：

```js
const app = new Vue({
  el: '#app',

  store,
  created() {
    store.commit('updateHash');
  }
});

window.onhashchange = () => {
  app.$store.commit('updateHash');
}
```

# 使用路径变量

我们现在需要更新组件，使用`store`中的路径，而不是通过组件传递的路径。`breadcrumb`和`dropbox-viewer`都需要更新以接受这个新变量。我们还可以从组件中删除不必要的 props。

# 更新面包屑组件

从 HTML 中删除`:p` prop，只留下一个简单的面包屑 HTML 标签：

```js
<breadcrumb></breadcrumb>
```

接下来，从 JavaScript 文件中删除组件的`props`对象。`parts`变量也需要更新为使用`this.$store.state.path`，而不是`this.p`：

```js
Vue.component('breadcrumb', {
  template: '<div>' +
    '<span v-for="(f, i) in folders">' +
      '<a :href="f.path">[F] {{ f.name }}</a>' +
      '<i v-if="i !== (folders.length - 1)"> &raquo; </i>' +
    '</span>' + 
  '</div>',

  computed: {
    folders() {
      let output = [],
        slug = '',
        parts = this.$store.state.path.split('/');

      for (let item of parts) {
        slug += item;
        output.push({'name': item || 'home', 'path': '#' + slug});
        slug += '/';
      }

      return output;
    }
  }
});
```

# 更新 dropbox-viewer 组件以与 Vuex 一起使用

与`breadcrumb`组件一样，第一步是从视图中删除 HTML 属性。这将进一步简化您的应用程序视图，您将只剩下一些 HTML 标签：

```js
<div id="app">
  <dropbox-viewer></dropbox-viewer>
</div>
```

下一步是清理 JavaScript 代码，删除任何不必要的函数参数。从`dropbox-viewer`组件中删除`props`对象。接下来，更新`getFolderStructure`中的`filesListFolder` Dropbox 方法，以使用存储路径，而不是使用路径变量：

```js
this.dropbox().filesListFolder({
  path: this.$store.state.path, 
  include_media_info: true
})
```

由于该方法现在使用`store`而不是函数参数，我们可以从方法声明本身中删除变量，以及从`updateStructure`方法和调用这两个函数的任何地方删除它。例如：

```js
updateStructure(path) {
  this.isLoading = true;
  this.getFolderStructure(path);
}
```

这将变成以下内容：

```js
updateStructure() {
  this.isLoading = true;
  this.getFolderStructure();
}
```

然而，我们仍然需要将路径存储为此组件上的变量。这是由于我们的`watch`方法调用了`updateStructure`函数。为此，我们需要将路径存储为计算值，而不是固定变量。这样，当`store`更新时，它可以动态更新，而不是在组件初始化时固定值。

在`dropbox-viewer`组件上创建一个计算对象，其中包含一个名为`path`的方法，该方法只返回`store`的路径：

```js
computed: {
  path() {
    return this.$store.state.path
  }
}
```

现在我们将其作为局部变量，因此 Dropbox 的`filesListFolder`方法可以更新为再次使用`this.path`。

新更新的`dropbox-viewer`组件应如下所示。在浏览器中查看应用程序时，它应该看起来好像没有任何变化-然而，应用程序的内部工作现在依赖于新的 Vuex 存储，而不是存储在 Vue 实例上的变量。

```js
Vue.component('dropbox-viewer', {
  template: '#dropbox-viewer-template',

  data() {
    return {
      accessToken: 'XXXX',
      structure: {},
      isLoading: true
    }
  },

  computed: {
 path() {
 return this.$store.state.path
 }
 },

  methods: {
    dropbox() {
      return new Dropbox({
        accessToken: this.accessToken
      });
    },

    getFolderStructure() { 
      this.dropbox().filesListFolder({
        path: this.path, 
        include_media_info: true
      })
      .then(response => {

        const structure = {
          folders: [],
          files: []
        }

        for (let entry of response.entries) {
          // Check ".tag" prop for type
          if(entry['.tag'] == 'folder') {
            structure.folders.push(entry);
          } else {
            structure.files.push(entry);
          }
        }

        this.structure = structure;
        this.isLoading = false;
      })
      .catch(error => {
        this.isLoading = 'error';
        console.log(error);
      });
    },

    updateStructure() {
      this.isLoading = true;
      this.getFolderStructure();
    }
  },

  created() {
    this.getFolderStructure();
  },

  watch: {
    path() {
      this.updateStructure();
    }
  },
});
```

# 缓存文件夹内容

现在我们在应用程序中使用 Vuex 并利用它来获取路径，我们可以开始考虑存储当前显示文件夹的内容，以便如果用户返回到相同的位置，API 不需要查询以检索结果。我们将通过将 API 返回的对象存储在 Vuex 存储中来实现这一点。

当请求文件夹时，应用程序将检查存储中是否存在数据。如果存在，则省略 API 调用，并从存储中加载数据。如果不存在，则查询 API 并将结果保存在 Vuex 存储中。

第一步是将数据处理分离到自己的方法中。这是因为文件和文件夹需要被拆分，无论数据是来自存储还是 API。

在`dropbox-viewer`组件中创建一个名为`createFolderStructure()`的新方法，并将代码从 Dropbox 的`filesListFolder`方法的`then()`函数内移动到其中。在这个函数内调用新方法。

现在你的两个方法应该如下所示，并且你的应用程序应该像之前一样工作：

```js
createFolderStructure(response) {
  const structure = {
    folders: [],
    files: []
  }

  for (let entry of response.entries) {
    // Check ".tag" prop for type
    if(entry['.tag'] == 'folder') {
      structure.folders.push(entry);
    } else {
      structure.files.push(entry);
    }
  }

  this.structure = structure;
  this.isLoading = false;
},

getFolderStructure() { 
  this.dropbox().filesListFolder({
    path: this.path, 
    include_media_info: true
  })
  .then(this.createFolderStructure)
  .catch(error => {
    this.isLoading = 'error';
    console.log(error);
  });
}
```

使用 promises，我们可以将`createFolderStructure`作为 API 调用的操作。

下一步是存储我们正在处理的数据。为此，我们将利用将对象传递给`store`的`commit`函数的能力，并使用路径作为存储对象中的键。我们将不再嵌套文件结构，而是将信息存储在一个扁平的结构中。例如，在我们浏览了几个文件夹之后，我们的存储将如下所示：

```js
structure: {
  'images': [{...}],
  'images-holiday': [{...}],
  'images-holiday-summer': [{...}]
}
```

路径将进行几个转换，以使其适合作为对象键。它将被转换为小写，并删除任何标点符号。我们还将用连字符替换所有空格和斜杠。

首先，在 Vuex 存储状态对象中创建一个名为`structure`的空对象；这是我们将存储数据的地方：

```js
state: {
  path: '',
  structure: {}
}
```

现在，我们需要创建一个新的`mutation`，以便在加载数据时存储它。在`mutations`对象中创建一个名为`structure`的新函数。它需要接受`state`作为参数，以及一个作为对象传递的`payload`变量：

```js
structure(state, payload) {
}
```

路径对象将包括一个`path`变量和从 API 返回的`data`。例如：

```js
{
  path: 'images-holiday',
  data: [{...}]
}
```

通过传入这个对象，我们可以使用路径作为键，数据作为值。将数据存储在突变中的路径键内：

```js
structure(state, payload) {
  state.structure[payload.path] = payload.data;
}
```

我们现在可以在组件的`createFolderStructure`方法的末尾提交这些数据：

```js
createFolderStructure(response) {
  const structure = {
    folders: [],
    files: []
  }

  for (let entry of response.entries) {
    // Check ".tag" prop for type
    if(entry['.tag'] == 'folder') {
      structure.folders.push(entry);
    } else {
      structure.files.push(entry);
    }
  }

  this.structure = structure;
  this.isLoading = false;

 this.$store.commit('structure', {
 path: this.path,
 data: response
 });
}
```

现在，当浏览应用程序时，它将存储每个文件夹的数据。可以通过在结构突变中添加`console.log(state.structure)`来验证这一点。

虽然目前的代码可以工作，但最好在将路径用作对象键时对其进行清理。为此，我们将删除任何标点符号，将任何空格和斜杠替换为连字符，并将路径转换为小写。

在`dropbox-viewer`组件上创建一个名为`slug`的新计算函数。术语 slug 通常用于清理过的 URL，并源自报纸和编辑如何引用故事的方式。此函数将运行多个 JavaScript `replace`方法来创建一个安全的对象键：

```js
slug() {
  return this.path.toLowerCase()
    .replace(/^\/|\/$/g, '')
    .replace(/ /g,'-')
    .replace(/\//g,'-')
    .replace(/[-]+/g, '-')
    .replace(/[^\w-]+/g,'');
}
```

slug 函数执行以下操作。例如路径`/images/iPhone/mom's Birthday - 40th`将受到以下影响：

+   将字符串转换为小写：`/images/iphone/mom's birthday - 40th`

+   删除路径开头和结尾的任何斜杠：`images/iphone/mom birthday - 40th`

+   将任何空格替换为连字符：`images/iphone/mom-birthday---40th`

+   将任何斜杠替换为连字符：`images-iphone-mom-birthday---40th`

+   将任何多个连字符替换为单个连字符：`images-iphone-mom-birthday-40th`

+   最后，删除任何标点符号：`images-iphone-moms-birthday-40th`

现在创建了 slug，我们可以将其用作存储数据时的键：

```js
this.$store.commit('structure', {
  path: this.slug,
  data: response
});
```

现在，我们的文件夹内容已经被缓存在 Vuex 存储中，我们可以添加一个检查，看看数据是否存在于存储中，如果存在，则从存储中加载数据。

# 如果存在，则从存储中加载数据

从存储中加载数据需要对我们的代码进行一些更改。第一步是检查`store`中是否存在结构，如果存在，则加载它。第二步是仅在数据是新数据时将数据提交到存储中-调用现有的`createFolderStructure`方法将更新结构，但也会重新提交数据到存储中。尽管当前情况下对用户没有害处，但不必要地将数据写入`store`可能会在应用程序增长时引发问题。这也将在我们进行预缓存文件夹和文件时对我们有所帮助。

# 从存储中加载数据

由于`store`是一个 JavaScript 对象，而我们的`slug`变量是组件上的一个计算值，我们可以使用`if`语句来检查对象键是否存在：

```js
if(this.$store.state.structure[this.slug]) {
  // The data exists
}
```

这使我们可以根据需要从`store`加载数据，使用`createFolderStructure`方法，如果不存在，则触发 Dropbox API 调用。

更新`getFolderStructure`方法以包含`if`语句，并在数据存在时添加方法调用：

```js
getFolderStructure() {
  if(this.$store.state.structure[this.slug]) {
 this.createFolderStructure(this.$store.state.structure[this.slug]);
 } else {
    this.dropbox().filesListFolder({
      path: this.path, 
      include_media_info: true
    })
    .then(this.createFolderStructure)
    .catch(error => {
      this.isLoading = 'error';
      console.log(error);
    });
  }
}
```

数据的路径非常长，可能会导致我们的代码难以阅读。为了更容易理解，将数据赋值给一个变量，这样我们可以检查它是否存在，并使用更清晰、更简洁、更少重复的代码返回数据。这也意味着如果数据路径发生变化，我们只需要更新一行代码：

```js
getFolderStructure() {
  let data = this.$store.state.structure[this.slug]; 
  if(data) {
    this.createFolderStructure(data);
  } else {
    this.dropbox().filesListFolder({
      path: this.path, 
      include_media_info: true
    })
    .then(this.createFolderStructure)
    .catch(error => {
      this.isLoading = 'error';
      console.log(error);
    });
  }
}
```

# 仅存储新数据

如前所述，当前的`createFolderStructure`方法既显示结构，又将响应缓存到`store`中，因此即使从缓存中加载数据，也会重新保存结构。

创建一个新的方法，当数据加载完成后，Dropbox API 将调用该方法。将其命名为`createStructureAndSave`。该方法应该只接受一个参数，即`response`变量：

```js
createStructureAndSave(response) {

}
```

现在，我们可以将`store`的`commit`函数从`createFolderStructure`方法中移动到这个新方法中，同时调用现有方法并传递数据：

```js
createStructureAndSave(response) {

  this.createFolderStructure(response)

 this.$store.commit('structure', {
 path: this.slug,
 data: response
 });
}
```

最后，更新 Dropbox API 函数以调用此方法：

```js
getFolderStructure() {
  let data = this.$store.state.structure[this.slug]; 
  if(data) {
    this.createFolderStructure(data);
  } else {
    this.dropbox().filesListFolder({
      path: this.path, 
      include_media_info: true
    })
    .then(this.createStructureAndSave)
    .catch(error => {
      this.isLoading = 'error';
      console.log(error);
    });
  }

},
```

在浏览器中打开应用程序并浏览文件夹。当您使用面包屑导航返回上一级时，响应速度应该更快，因为它现在是从您创建的缓存中加载，而不是每次查询 API。

在第七章《预缓存其他文件夹和文件以加快导航速度》中，我们将研究预缓存文件夹，以尝试预测用户接下来的操作。我们还将研究缓存文件的下载链接。

我们的完整应用程序 JavaScript 现在应该如下所示：

```js
Vue.component('breadcrumb', {
  template: '<div>' +
    '<span v-for="(f, i) in folders">' +
      '<a :href="f.path">[F] {{ f.name }}</a>' +
      '<i v-if="i !== (folders.length - 1)"> &raquo; </i>' +
    '</span>' + 
  '</div>',
  computed: {
    folders() {
      let output = [],
        slug = '',
        parts = this.$store.state.path.split('/');

      for (let item of parts) {
        slug += item;
        output.push({'name': item || 'home', 'path': '#' + slug});
        slug += '/';
      }

      return output;
    }
  }
});

Vue.component('folder', {
  template: '<li><strong><a :href="\'#\' + f.path_lower">{{ f.name }}</a></strong></li>',
  props: {
    f: Object
  }
});

Vue.component('file', {
  template: '<li><strong>{{ f.name }}</strong><span v-if="f.size"> - {{ bytesToSize(f.size) }}</span> - <a v-if="link" :href="link">Download</a></li>',
  props: {
    f: Object,
    d: Object
  },

  data() {
    return {
      byteSizes: ['Bytes', 'KB', 'MB', 'GB', 'TB'],
      link: false
    }
  },

  methods: {
    bytesToSize(bytes) {
      // Set a default
      let output = '0 Byte';

      // If the bytes are bigger than 0
      if (bytes > 0) {
        // Divide by 1024 and make an int
        let i = parseInt(Math.floor(Math.log(bytes) / Math.log(1024)));
        // Round to 2 decimal places and select the appropriate unit from the array
        output = Math.round(bytes / Math.pow(1024, i), 2) + ' ' + this.byteSizes[i];
      }

      return output
    }
  },

  created() {
    this.d.filesGetTemporaryLink({path: this.f.path_lower}).then(data => {
      this.link = data.link;
    });
  },
});

Vue.component('dropbox-viewer', {
  template: '#dropbox-viewer-template',

  data() {
    return {
      accessToken: 'XXXX',
      structure: {},
      isLoading: true
    }
  },

  computed: {
    path() {
      return this.$store.state.path
    },
    slug() {
      return this.path.toLowerCase()
        .replace(/^\/|\/$/g, '')
        .replace(/ /g,'-')
        .replace(/\//g,'-')
        .replace(/[-]+/g, '-')
        .replace(/[^\w-]+/g,'');
    }
  },

  methods: {
    dropbox() {
      return new Dropbox({
        accessToken: this.accessToken
      });
    },

    createFolderStructure(response) {

      const structure = {
        folders: [],
        files: []
      }

      for (let entry of response.entries) {
        // Check ".tag" prop for type
        if(entry['.tag'] == 'folder') {
          structure.folders.push(entry);
        } else {
          structure.files.push(entry);
        }
      }

      this.structure = structure;
      this.isLoading = false;

    },

    createStructureAndSave(response) {

      this.createFolderStructure(response)

      this.$store.commit('structure', {
        path: this.slug,
        data: response
      });
    },

    getFolderStructure() {
      let data = this.$store.state.structure[this.slug]; 
      if(data) {
        this.createFolderStructure(data);
      } else {
        this.dropbox().filesListFolder({
          path: this.path, 
          include_media_info: true
        })
        .then(this.createStructureAndSave)
        .catch(error => {
          this.isLoading = 'error';
          console.log(error);
        });
      }

    },

    updateStructure() {
      this.isLoading = true;
      this.getFolderStructure();
    }
  },

  created() {
    this.getFolderStructure();
  },

  watch: {
    path() {
      this.updateStructure();
    }
  },
});

const store = new Vuex.Store({
  state: {
    path: '',
    structure: {}
  },
  mutations: {
    updateHash(state) {
      let hash = window.location.hash.substring(1);
      state.path = (hash || '');
    },
    structure(state, payload) {
      state.structure[payload.path] = payload.data;
    }
  }
});

const app = new Vue({
  el: '#app',

  store,
  created() {
    store.commit('updateHash');
  }
});

window.onhashchange = () => {
  app.$store.commit('updateHash');
}
```

# 摘要

在本章之后，您的应用程序现在应该与 Vuex 集成，并缓存 Dropbox 文件夹的内容。Dropbox 文件夹路径也应该利用`store`来使应用程序更高效。我们只在需要时查询 API。

在[第七章](https://cdp.packtpub.com/vue_js_by_example/wp-admin/post.php?post=71&action=edit#post_82)中，《预缓存其他文件夹和文件以加快导航速度》，我们将研究预缓存文件夹-预先查询 API 以加快应用程序的导航和可用性。
