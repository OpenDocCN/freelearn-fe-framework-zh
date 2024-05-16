# 第五章：通过文件树导航并从 URL 加载文件夹

在第四章中，我们创建了一个应用程序，列出了指定 Dropbox 文件夹的文件和文件夹内容。现在我们需要使我们的应用程序易于导航。这意味着用户将能够点击文件夹名称以导航到并列出其内容，并且还能够下载文件。

在继续之前，请确保在 HTML 中包含了 Vue 和 Dropbox 的 JavaScript 文件。

在本章中，我们将：

+   为文件和文件夹分别创建一个组件

+   为文件夹组件添加链接以更新目录列表

+   为文件组件添加下载按钮

+   创建一个面包屑组件，以便用户可以轻松地返回上一级目录

+   动态更新浏览器的 URL，以便如果文件夹被收藏夹或链接共享，正确的文件夹加载

# 将文件和文件夹分开

在创建组件之前，我们需要在结构中分离文件和文件夹，以便我们可以轻松地识别和显示不同类型。由于每个项目上都有`.tag`属性，我们可以将文件夹和文件分开。

首先，我们需要更新我们的`structure`数据属性，使其成为一个包含`files`和`folders`数组的对象：

```js
      data() {
        return {
          accessToken: 'XXXX',
          structure: {
 files: [],
 folders: []
 },
          byteSizes: ['Bytes', 'KB', 'MB', 'GB', 'TB'],
          isLoading: true
        }
      }
```

这使我们能够将文件和文件夹追加到不同的数组中，从而可以在视图中以不同的方式显示它们。

下一步是使用当前文件夹的数据填充这些数组。以下所有代码都在`getFolderStructure`方法的第一个`then()`函数中执行。

创建一个 JavaScript 循环来遍历条目并检查项目的`.tag`属性。如果它等于`folder`，则将其追加到`structure.folder`数组中，否则将其添加到`structure.files`数组中：

```js
      getFolderStructure(path) {
        this.dropbox().filesListFolder({
          path: path, 
          include_media_info: true
        })
        .then(response => {
          for (let entry of response.entries) {
 // Check ".tag" prop for type
 if(entry['.tag'] === 'folder') {
 this.structure.folders.push(entry);
 } else {
 this.structure.files.push(entry);
 }
 }
          this.isLoading = false;
        })
        .catch(error => {
          console.log(error);
        });
      },
```

这段代码通过循环遍历条目，就像我们在视图中一样，并检查`.tag`属性。由于属性本身以`.`开头，我们无法像访问`entry.name`属性那样使用对象样式的表示法来访问该属性。然后，根据类型，我们使用 JavaScript 的 push 方法将条目追加到`files`或`folders`数组中。

为了显示这些新数据，我们需要更新视图，循环遍历两种类型的数组。这是使用`<template>`标签的一个完美用例，因为我们希望将两个数组都追加到同一个无序列表中。

更新视图以单独列出这两个数组。我们可以从文件夹显示部分中删除大小选项，因为它永远不会包含`size`属性：

```js
      <ul v-if="!isLoading">
        <template v-for="entry in structure.folders">
 <li>
 <strong>{{entry.name }}</strong>
 </li>
 </template>
 <template v-for="entry in structure.files">
 <li>
 <strong>{{ entry.name }}</strong>
 <span v-if="entry.size">- {{ bytesToSize(entry.size)       }}</span>
 </li>
 </template>
      </ul>
```

现在我们有机会为两种类型创建组件。

# 创建文件和文件夹组件

将我们的数据类型分开后，我们可以创建单独的组件来分隔数据和方法。创建一个`folder`组件，接受一个属性，允许通过`folder`对象变量传递。由于模板非常小，不需要基于视图或`<script>`块的模板；相反，我们可以将其作为字符串传递给组件：

```js
      Vue.component('folder', {
        template: '<li><strong>{{ f.name }}</strong>      
        </li>',
        props: {
          f: Object
        },
      });
```

为了使我们的代码更小、更少重复，属性被称为`f`。这样可以整理视图，并让组件名称决定显示类型，而不需要多次重复单词`folder`。

更新视图以使用文件夹组件，并将`entry`变量传递给`f`属性：

```js
      <template v-for="entry in structure.folders">
        <folder :f="entry"></folder>
      </template>
```

通过创建一个`file`组件来重复这个过程。在创建`file`组件时，我们可以将`bytesToSize`方法和`byteSizes`数据属性从父级`dropbox-viewer`组件中移动，因为它们只会在显示文件时使用：

```js
      Vue.component('file', {
        template: '<li><strong>{{ f.name }}</strong><span       v-if="f.size"> - {{ bytesToSize(f.size) }}</span>         </li>',
        props: {
          f: Object
        },
        data() {
          return {
            byteSizes: ['Bytes', 'KB', 'MB', 'GB', 'TB']
          }
        }, 
        methods: {
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
            output = Math.round(bytes / Math.pow(1024, i), 
             2) + ' ' + this.byteSizes[i];
            }   
            return output
          }
        }
      });
```

再次，我们可以使用`f`作为属性名称来减少重复（和应用程序的文件大小）。再次更新视图以使用这个新组件。

```js
      <template v-for="entry in structure.files">
        <file :f="entry"></file>
      </template>
```

# 链接文件夹并更新结构

现在我们将文件夹和文件分开后，我们可以将文件夹名称转换为链接。这些链接将更新结构以显示所选文件夹的内容。为此，我们将使用每个文件夹中的`path_lower`属性来构建链接目标。

为每个文件夹的`name`创建一个动态链接，链接到文件夹的`path_lower`。由于我们对 Vue 越来越熟悉，`v-bind`属性已经缩短为冒号表示法：

```js
      Vue.component('folder', {
        template: '<li><strong><a :href="f.path_lower">{{ 
        f.name }}</a></strong></li>',
        props: {
          f: Object
        },
      });
```

现在我们需要为此链接添加一个`click`监听器。当点击时，我们需要在`dropbox-viewer`组件上触发`getFolderStructure`方法。虽然点击方法将使用每个实例上的`f`变量来获取数据，但将`href`属性设置为文件夹 URL 是一个好的做法。

使用我们在本书早期章节中学到的知识，在`folder`组件上创建一个方法，当触发时将文件夹路径发送到父组件。当触发时，`dropbox-viewer`组件还需要一个新的方法来使用给定的参数更新结构。

在`folder`组件上创建新的方法，并将`click`事件添加到文件夹链接上。与`v-bind`指令一样，我们现在使用`v-on`的简写表示法，表示为`@`符号：

```js
      Vue.component('folder', {
        template: '<li><strong><a          
 @click.prevent="navigate()" :href="f.path_lower">{{ 
       f.name }}</a></strong></li>',
        props: {
          f: Object
        },
        methods: {
          navigate() {
 this.$emit('path', this.f.path_lower);
 }
        }
      });
```

除了定义`click`事件之外，还添加了一个事件修饰符。在点击事件之后使用`.prevent`将`preventDefault`添加到链接操作中，这样可以阻止链接实际上转到指定的 URL，而是让`click`方法处理一切。有关更多事件修饰符和详细信息，请参阅 Vue 文档。

当点击时，将触发导航方法，该方法使用`path`变量发出文件夹的较低路径。

现在我们有了`click`处理程序和被发出的变量，我们需要更新视图以触发父组件`dropbox-viewer`上的一个方法：

```js
      <template v-for="entry in structure.folders">
        <folder :f="entry" @path="updateStructure">      
        </folder>
      </template>
```

在 Dropbox 组件上创建一个与`v-on`属性的值相同的新方法，这里是`updateStructure`。这个方法将有一个参数，即我们之前发出的路径。从这里开始，我们可以使用路径变量触发我们原来的`getFolderStructure`方法：

```js
      updateStructure(path) {
        this.getFolderStructure(path);
      }
```

在浏览器中查看我们的应用程序，现在应该列出文件夹和链接，并且在点击时显示新文件夹的内容。

然而，在这样做时，会引发一些问题。首先，文件和文件夹被追加到现有列表中，而不是替换它。其次，用户没有任何反馈，表明应用正在加载下一个文件夹。

第一个问题可以通过在追加新结构之前清除文件夹和文件数组来解决。第二个问题可以通过使用我们在应用程序开始时使用的加载屏幕来解决-这将给用户一些反馈。

为了解决第一个问题，在`getFolderStructure`方法的成功 promise 函数中创建一个新的`structure`对象。这个对象应该复制`data`对象中的`structure`对象。这应该为文件和文件夹设置空数组。更新`for`循环以使用本地结构数组而不是组件数组。最后，使用新版本更新组件`structure`对象，包括更新后的文件和文件夹：

```js
      getFolderStructure(path) {
        this.dropbox().filesListFolder({
          path: path, 
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
          console.log(error);
        });
      }
```

由于此方法在应用程序挂载时调用并创建自己的结构对象，所以不需要在`data`函数中声明数组。将数据对象更新为只初始化`structure`属性为对象：

```js
      data() {
        return {
          accessToken: 'XXXX',
          structure: {},
          isLoading: true
        }
      }
```

现在运行应用程序将呈现文件列表，当点击进入新文件夹时，文件列表将被清除并更新。为了给用户一些反馈并让他们知道应用程序正在工作，让我们在每次点击后切换加载屏幕。

然而，在我们这样做之前，让我们充分了解延迟来自何处以及何时最好触发加载屏幕。

点击链接是瞬时的，它触发文件夹组件上的导航方法，进而触发 Dropbox 组件上的`updateStructure`方法。当应用程序到达 Dropbox 实例上的`filesListFolder`函数时，延迟就会出现在`getFolderStructure`方法内部。由于我们可能希望在以后的某个日期触发`getFolderStucture`方法而不触发加载屏幕，所以在`updateStructure`方法内将`isLoading`变量设置为`true`：

```js
      updateStructure(path) {
        this.isLoading = true;
        this.getFolderStructure(path);
      }
```

通过动画，应用程序在导航文件夹时在加载屏幕和文件夹结构之间淡入淡出。

# 从当前路径创建面包屑

在导航文件夹或任何嵌套结构时，始终有一个可用的面包屑对用户来说是很好的，这样他们就知道自己在哪里，走了多远，还可以轻松返回到以前的文件夹。我们将为面包屑制作一个组件，因为它将具有各种属性、计算函数和方法。

面包屑组件将以链接的形式列出每个文件夹的深度，链接将直接将用户带到该文件夹 - 即使它是几层上面的。为了实现这一点，我们需要一个链接列表，我们可以循环遍历，每个链接都有两个属性 - 一个是文件夹的完整路径，另一个只是文件夹的名称。

例如，如果我们有文件夹结构`img/holiday/summer/iphone`，我们希望能够点击`Holiday`并使应用程序导航到`img/holiday`。

创建您的面包屑组件 - 现在，在模板属性中添加一个空的`<div>`：

```js
      Vue.component('breadcrumb', {
        template: '<div></div>'
      });
```

将组件添加到您的视图中。我们希望面包屑能够与结构列表一起淡入淡出，因此我们需要调整 HTML，将列表和面包屑组件都包裹在一个具有`v-if`声明的容器中：

```js
      <transition name="fade">
        <div v-if="!isLoading">
          <breadcrumb></breadcrumb>
          <ul>
            <template v-for="entry in structure.folders">
              <folder :f="entry" @path="updateStructure">              </folder>
            </template>  
            <template v-for="entry in structure.files">
              <file :f="entry"></file>
            </template>
          </ul>
        </div>
      </transition>
```

现在我们需要一个变量来存储当前文件夹路径。然后我们可以在面包屑组件中操作这个变量。这个变量将被存储和更新在 Dropbox 组件中，并传递给面包屑组件。

在`dropbox-viewer`组件上创建一个名为`path`的新属性：

```js
      data() {
        return {
          accessToken: 'XXXXX',
          structure: {},
          isLoading: true,
          path: ''
        }
      }
```

现在我们需要确保当从 Dropbox API 检索到结构时，该路径会得到更新。在`getFolderStructure`方法中进行此操作，就在`isLoading`变量被禁用之前。这样可以确保它只在结构加载完成之后但在文件和文件夹显示之前更新：

```js
      getFolderStructure(path) {
        this.dropbox().filesListFolder({
          path: path, 
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
          this.path = path;
          this.structure = structure;
          this.isLoading = false;
        })
        .catch(error => {
          console.log(error);
        });
      },
```

现在我们有一个填充了当前路径的变量，我们可以将其作为属性传递给面包屑组件。在面包屑中添加一个新的属性，将路径变量作为值：

```js
      <breadcrumb :p="path"></breadcrumb>
```

更新组件以接受字符串作为属性：

```js
      Vue.component('breadcrumb', {
        template: '<div></div>',
        props: {
 p: String
 }
      });
```

`p`属性现在包含我们所在位置的完整路径（例如 img/holiday/summer）。我们想要将这个字符串分解，以便我们可以识别文件夹名称并构建面包屑供组件渲染。

在组件上创建一个`computed`对象，并创建一个名为`folders()`的新函数。这将为我们创建面包屑数组，供我们在模板中循环使用：

```js
      computed: {
       folders() {   
        }
      }
```

现在我们需要设置一些变量供我们使用。创建一个新的空数组`output`，这是我们要构建面包屑的地方。我们还需要一个空的字符串变量`titled slug`。`slug`变量是 URL 的一部分，它的使用在 WordPress 中很流行。最后一个变量是作为数组创建的路径。我们知道，每个文件夹都是由`/`分隔的，我们可以使用这个来将字符串分解成各个部分：

```js
      computed: {
        folders() {
 let output = [],
 slug = '',
 parts = this.p.split('/');
        }
      }
```

如果我们查看`Summer`文件夹的`parts`变量，它将如下所示：

```js
      ['images', 'holiday', 'summer']
```

现在我们可以循环遍历数组来创建面包屑。每个面包屑项将是一个对象，包含个别文件夹的`name`，例如`holiday`或`summer`，以及`slug`，前者为 img/holiday，后者为 img/holiday/summer。

每个对象将被构建，然后添加到`output`数组中。然后我们可以返回输出供我们的模板使用：

```js
      folders() {
        let output = [],
          slug = '',
          parts = this.p.split('/'); 
 for (let item of parts) {
 slug += item;
 output.push({'name': item, 'path': slug});
 slug += '/';
 }  
        return output;
      }
```

这个循环通过以下步骤创建我们的面包屑。以 img/holiday 文件夹为例：

1.  `parts`现在是一个包含三个项目的数组，`['', 'images', holiday']`。如果你分割的字符串以你要分割的项目开头，那么一个空项目将作为第一个项目。

1.  在循环开始时，第一个 slug 变量将等于`''`，因为它是第一个项目。

1.  `output`数组将附加一个新项，对象为`{'name': '', 'path': ''}`。

1.  然后，在`slug`变量的末尾添加一个`/`。

1.  循环遍历下一个项目时，`slug`变量将其名称（`images`）添加到其中。

1.  `output`现在添加了一个新的对象，值为`{'name': 'images', 'path': '/images'}`。

1.  对于最后一个项目，还会添加另一个`/`以及下一个名称`holiday`。

1.  `output`获取最后一个添加的对象，其值为`{'name': 'holiday', 'path':img/holiday'}` - 注意路径正在构建，而名称保持为单个文件夹名称。

现在我们有了可以在视图中循环遍历的面包屑输出数组。

我们在将输出数组附加后添加斜杠的原因是 API 规定要获取 Dropbox 的根目录，我们传入一个空字符串，而所有其他路径必须以`/`开头。

下一步是将面包屑输出到我们的视图中。由于这个模板很小，我们将使用多行 JavaScript 表示法。循环遍历`folders`计算变量中的项目，为每个项目输出一个链接。不要忘记在所有链接周围保留一个包含元素：

```js
      template: '<div>' +
 '<span v-for="f in folders">' +
 '<a :href="f.path">{{ f.name }}</a>' +
 '</span>' + 
      '</div>'
```

在浏览器中渲染此应用程序应该会显示一个面包屑 - 尽管有点挤在一起并且缺少一个主页链接（因为第一个项目没有名称）。返回到`folders`函数并添加一个`if`语句 - 检查项目是否有名称，如果没有，则添加一个硬编码的值：

```js
      folders() {
        let output = [],
          slug = '',
          parts = this.p.split('/');
        console.log(parts);
        for (let item of parts) {
          slug += item;
          output.push({'name': item || 'home', 'path':      
            slug});
          slug += '/';
        }  
        return output;
      }
```

另一个选项是在模板本身中添加`if`语句：

```js
      template: '<div>' +
        '<span v-for="f in folders">' +
          '<a :href="f.path">{{ f.name || 'Home' }}</a>' +
        '</span>' + 
      '</div>'
```

如果我们想在文件夹名称之间显示一个分隔符，比如斜杠或箭头，这可以很容易地添加。然而，当我们想要在链接之间显示分隔符，但不在开头或结尾时，会出现一个小障碍。为了解决这个问题，我们将利用循环时可用的`index`关键字。然后，我们将将其与数组的长度进行比较，并在元素上操作`v-if`声明。

在循环数组时，Vue 允许您利用另一个变量。默认情况下，这是索引（数组中项目的位置）；然而，如果您的数组以键/值方式构建，则可以将索引设置为一个值。如果是这种情况，您仍然可以通过添加第三个变量来访问索引。由于我们的数组是一个简单的列表，我们可以轻松使用这个变量：

```js
      template: '<div>' +
        '<span v-for="(f, i) in folders">' +
          '<a :href="f.path">{{ f.name || 'Home' }}</a>' +
          '<span v-if="i !== (folders.length - 1)"> » 
            </span>' +
        '</span>' + 
      '</div>',
```

将`f`变量更新为包含`f`和`i`的一对括号，用逗号分隔。变量`f`是循环中的当前文件夹，而已创建的变量`i`是项目的索引。请记住，数组索引从 0 开始，而不是从 1 开始。

我们添加的分隔符包含在一个带有`v-if`属性的 span 标签中，其内容可能看起来很困惑。这是将当前索引与`folders`数组的长度（它有多少项）减 1 混淆在一起。减 1 是因为索引从 0 开始，而不是从 1 开始，这是您所期望的。如果数字不匹配，则显示`span`元素。

我们需要做的最后一件事是使面包屑导航到选定的文件夹。我们可以通过调整我们为“文件夹”组件编写的导航函数来实现这一点。然而，由于我们的整个组件是面包屑，而不是每个单独的链接，我们需要修改它以接受一个参数。

首先，为链接添加`click`事件，传入`folder`对象：

```js
      template: '<div>' +
        '<span v-for="(f, i) in folders">' +
          '<a @click.prevent="navigate(f)"          
            :href="f.path"> 
            {{ f.name || 'Home' }}</a>' +
          '<i v-if="i !== (folders.length - 1)"> &raquo; 
           </i>' +
        '</span>' + 
      '</div>',
```

接下来，在面包屑组件上创建`navigate`方法，确保接受`folder`参数并发出路径：

```js
      methods: {
        navigate(folder) {
          this.$emit('path', folder.path);
        }
      }
```

最后一步是在路径发出时触发父方法。为此，我们可以利用`dropbox-viewer`组件上的相同`updateStructure`方法：

```js
      <breadcrumb :p="path" @path="updateStructure">      
      </breadcrumb>
```

现在，我们有了一个完全可操作的面包屑，允许用户使用文件夹链接导航到文件夹结构下方，并通过面包屑链接返回上级。

我们完整的面包屑组件如下所示：

```js
      Vue.component('breadcrumb', {
        template: '<div>' +
          '<span v-for="(f, i) in folders">' +
            '<a @click.prevent="navigate(f)" 
             :href="f.path">{{ 
              f.name || 'Home' }}</a>' +
              '<i v-if="i !== (folders.length - 1)"> » 
              </i>' + '</span>' + 
             '</div>',

        props: {
    p: String
  },

  computed: {
    folders() {
      let output = [],
        slug = '',
        parts = this.p.split('/');
      console.log(parts);
      for (let item of parts) {
        slug += item;
        output.push({'name': item || 'home', 'path':   
        slug});
        slug += '/';
      }

      return output;
    }
  },

   methods: {
    navigate(folder) {
      this.$emit('path', folder.path);
    }
  }
});
```

# 添加下载文件的功能

现在，我们的用户可以通过文件夹结构导航，我们需要添加下载文件的功能。不幸的是，这并不像访问文件上的链接属性那样简单。要获取下载链接，我们必须为每个文件查询 Dropbox API。

在创建文件组件时，我们将查询 API，这将异步获取下载链接并在可用时显示它。在此之前，我们需要将 Dropbox 实例提供给文件组件。

在视图中为文件组件添加一个新属性，并将 Dropbox 方法作为值传递：

```js
      <file :d="dropbox()" :f="entry"></file>
```

将`d`变量添加到接受对象的组件的`props`对象中：

```js
    props: {
      f: Object,
      d: Object
    },
```

现在，我们将添加一个名为`link`的数据属性。默认情况下，它应该设置为`false`，这样我们就可以隐藏链接，并在 API 返回值后填充它。

在文件组件中添加`created()`函数，并在其中添加 API 调用：

```js
     created() {
      this.d.filesGetTemporaryLink({path:    
       this.f.path_lower}).then(data => {
        this.link = data.link;
     });
    }
```

这个 API 方法接受一个对象，类似于`filesListFolder`函数。我们传递当前文件的路径。一旦数据返回，我们就可以将组件的`link`属性设置为下载链接。

现在我们可以在组件的模板中添加一个下载链接。添加一个`v-if`，只有在获取到下载链接后才显示`<a>`：

```js
   template: '<li><strong>{{ f.name }}</strong><span v-  
    if="f.size"> - {{ bytesToSize(f.size) }}</span><span    
    v-if="link"> - <a :href="link">Download</a></span>  
   </li>'
```

浏览文件时，我们现在可以看到每个文件旁边出现了一个下载链接，其速度取决于您的互联网连接和 API 速度。

完整的文件组件，添加了下载链接后，现在看起来是这样的：

```js
    Vue.component('file', {
     template: '<li><strong>{{ f.name }}</strong><span v-   
     if="f.size"> - {{ bytesToSize(f.size) }}</span><span 
     v-if="link"> - <a :href="link">Download</a></span>
     </li>',
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
        let i = parseInt(Math.floor(Math.log(bytes) / 
          Math.log(1024)));
        // Round to 2 decimal places and select the 
         appropriate unit from the array
        output = Math.round(bytes / Math.pow(1024, i), 2) 
         + ' ' + this.byteSizes[i];
      }

      return output
      }
     },

     created() {
    this.d.filesGetTemporaryLink({path:    
     this.f.path_lower}).then(data => {
      this.link = data.link;
      });
    },
  });
```

# 更新 URL 哈希并使用它浏览文件夹

通过结构列表和面包屑，我们的 Dropbox Web 应用程序现在可以完全导航，现在我们可以添加和更新浏览器 URL 以快速访问和共享文件夹。我们可以通过两种方式实现这一点：我们可以更新哈希，例如`www.domain.comimg/holiday/summer`，或者我们可以将所有路径重定向到单个页面，并处理 URL 中的路由而不使用哈希。

对于这个应用程序，我们将在 URL 中使用`#`方法。当我们介绍`vue-router`时，我们将在本书的第三部分介绍 URL 路由技术。

在我们让应用程序显示与 URL 对应的文件夹之前，我们首先需要在导航到新文件夹时获取 URL。我们可以使用原生的`window.location.hash` JavaScript 对象来实现这一点。我们希望在用户点击链接时立即更新 URL，而不是等待数据加载完成后再更新。

由于`getFolderStructure`方法在更新结构时被触发，所以将代码添加到该函数的顶部。这意味着 URL 会被更新，然后调用 Dropbox API 来更新结构：

```js
    getFolderStructure(path) {
      window.location.hash = path;

      this.dropbox().filesListFolder({
       path: path, 
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

      this.path = path;
      this.structure = structure;
      this.isLoading = false;
   })
     .catch(error => {
      console.log(error);
   });
 }
```

当您浏览应用程序时，它应该会更新 URL 以包括当前文件夹路径。

然而，当你按下刷新按钮时，你会发现一个问题：URL 会重置，只剩下一个哈希，后面没有文件夹，因为它是通过`created()`函数中传入的空路径重置的。

我们可以通过在`created`函数中将当前哈希传递给`getFolderStructure`来解决这个问题，但是如果这样做，我们需要进行一些检查和错误捕获。

首先，当调用`window.location.hash`时，你也会得到哈希作为字符串的一部分返回，所以我们需要将其删除。其次，我们需要处理 URL 不正确的情况，如果用户输入了不正确的路径或者文件夹被移动了。最后，我们需要让用户在浏览器中使用后退和前进按钮（或键盘快捷键）。

# 根据 URL 显示文件夹

当我们的应用挂载时，它已经调用了一个函数来请求基本文件夹的结构。我们编写了这个函数，允许传入路径，并且在`created()`函数中，我们已经将值固定为根文件夹`''`。这使我们能够灵活地调整这个函数，以传入 URL 的哈希，而不是固定的字符串。

更新函数以接受 URL 的哈希，如果没有哈希，则使用原始的固定字符串：

```js
  created() {
    let hash = window.location.hash.substring(1);
    this.getFolderStructure(hash || '');
  }
```

创建一个名为`hash`的新变量，并将`window.location.hash`赋值给它。因为变量以`#`开头，对于我们的应用来说是不需要的，所以使用`substring`函数从字符串中删除第一个字符。然后，我们可以使用逻辑运算符来使用 hash 变量，或者如果它等于空，使用原始的固定字符串。

现在你应该能够通过更新 URL 来浏览你的应用。如果你随时按下刷新按钮或将 URL 复制粘贴到另一个浏览器窗口中，你所在的文件夹应该会加载。

# 显示错误消息

由于我们的应用接受 URL，我们需要处理一种情况，即有人输入了一个 URL 并犯了一个错误，或者共享的文件夹已经被移动了。

由于这个错误是一个边缘情况，如果加载数据时出现错误，我们将劫持`isLoading`参数。在`getFolderStructure`函数中，我们返回一个作为 promise 的`catch`函数，如果 API 调用出错，就会触发这个函数。在这个函数中，将`isLoading`变量设置为`'error'`：

```js
   getFolderStructure(path) {
     window.location.hash = path;

     this.dropbox().filesListFolder({
      path: path, 
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

     this.path = path;
     this.structure = structure;
     this.isLoading = false;
   })
    .catch(error => {
      this.isLoading = 'error';
      console.log(error);
    });
  }
```

`console.log`已经保留下来，以防需要诊断除了错误文件路径之外的问题。虽然 API 可能会抛出多种不同的错误，但我们将假设这个应用程序的错误是由于错误的路径。如果您想在应用程序中处理其他错误，可以通过其`status_code`属性识别错误类型。有关此的更多详细信息可以在 Dropbox API 文档中找到。

更新视图以处理这个新的`isLoading`变量属性。当设置为错误时，`isLoading`变量仍然为“true”，所以在加载元素中，添加一个新的`v-if`来检查加载变量是否设置为`error`：

```js
   <transition name="fade">
    <div v-if="isLoading">
      <div v-if="isLoading === 'error'">
 <p>There seems to be an issue with the URL entered.  
       </p>
 <p><a href="">Go home</a></p>
 </div>
 <div v-else>
 Loading...
 </div>
    </div>
  </transition>
```

这是设置为显示`isLoading`变量的第一个元素设置为`error`；否则，显示加载文本。在错误文本中，包含一个链接，将用户发送回当前 URL，不带任何 URL 哈希。这将使他们“重置”回到文档树的顶部，以便他们可以返回。一个改进是将当前 URL 拆分，并建议删除最后一个文件夹后相同的 URL。

通过在 URL 末尾添加一个不存在的路径并确保显示错误消息来验证错误代码是否正在加载。请记住，您的用户可能会在某种意义上对此错误消息产生误报，即如果 Dropbox API 抛出任何类型的错误，将显示此消息。

# 使用浏览器的后退和前进按钮

为了使用浏览器的后退和前进按钮，我们需要大幅更新我们的代码。目前，当用户从结构或面包屑中点击一个文件夹时，我们通过在`click`处理程序上使用`.prevent`来阻止浏览器的默认行为。然后，我们立即更新 URL，然后处理文件夹。

然而，如果我们允许应用程序使用本机行为更新 URL，我们可以监听哈希 URL 的更新，并使用它来检索我们的新结构。使用这种方法，后退和前进按钮将无需任何进一步的干预，因为它们将更新 URL 哈希。

这也将改善我们应用程序的可读性，并减少代码量，因为我们将能够删除链接上的`navigate`方法和`click`处理程序。

# 删除不需要的代码

在添加更多代码之前，第一步是从我们的组件中删除不必要的代码。从面包屑开始，从组件中删除`navigate`方法，并从模板中的链接中删除`@click.prevent`属性。

我们还需要更新每个项目的`slug`，在前面添加一个`#`，这样可以确保应用程序在单击时不会尝试导航到一个全新的页面。当我们在文件夹的`computed`函数中循环遍历面包屑项时，在将对象推送到`output`数组时，为每个`slug`添加一个哈希：

```js
 Vue.component('breadcrumb', {
   template: '<div>' +
     '<span v-for="(f, i) in folders">' +
       '<a :href="f.path">{{ f.name || 'Home' }}</a>' +
       '<i v-if="i !== (folders.length - 1)"> &raquo;   
       </i>' + '</span>' + 
       '</div>',
    props: {
      p: String
     },
    computed: {
      folders() {
        let output = [],
          slug = '',
          parts = this.p.split('/');

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

我们还可以从`dropbox-viewer-template`中的面包屑组件中删除`v-on`声明。它只应该作为属性传递路径：

```js
    <breadcrumb :p="path"></breadcrumb>
```

现在我们可以为文件夹组件重复相同的模式。从链接中删除`@click.prevent`声明并删除`navigate`方法。

由于我们在显示之前不会循环遍历或编辑文件夹对象，所以我们可以在模板中添加`#`。由于我们告诉 Vue`href`绑定到一个 JavaScript 对象（使用冒号），我们需要将哈希封装在引号中，并使用 JavaScript 的`+`符号将其与文件夹路径连接起来。

我们已经在单引号和双引号内部，所以我们需要告诉 JavaScript 我们是*字面上*意味着一个单引号，这可以通过在单引号字符前面使用反斜杠来实现：

```js
   Vue.component('folder', {
    template: '<li><strong><a :href="\'#\' +   
    f.path_lower">{{ f.name }}</a></strong></li>',
     props: {
      f: Object
     }
   });
```

我们还可以从视图中的`<folder>`组件中删除`@path`属性：

```js
   <template v-for="entry in structure.folders">
     <folder :f="entry"></folder>
   </template>
```

我们的代码看起来更整洁、更简洁，文件大小更小。在浏览器中查看应用程序将呈现所在文件夹的结构；但是，点击链接将更新 URL 但不会更改显示内容。

# 通过 URL 更改更新结构并在实例外部设置 Vue 数据

现在我们的 URL 已经正确更新，我们可以在哈希更改时获取新的结构。这可以使用 JavaScript 的`onhashchange`函数来实现。

我们将创建一个函数，每当 URL 的哈希更新时触发，然后将更新父 Vue 实例上的路径变量。这个变量将作为属性传递给子组件`dropbox-viewer`。该组件将监听变量的变化，并在更新时检索新的结构。

首先，更新父 Vue 实例，使其具有一个数据对象，其中包含一个路径键-设置为空字符串属性。我们还将将 Vue 实例分配给一个名为`app`的常量变量-这允许我们在实例外部设置数据和调用方法：

```js
 const app = new Vue({
    el: '#app',
 data: {
 path: ''
 }
 });
```

下一步是在 URL 更新时每次更新这个数据属性。这是使用`window.onhashchange`完成的，它是一个原生 JavaScript 函数，每当 URL 中的哈希发生变化时触发。

从 Dropbox 组件的`created`函数中复制并粘贴哈希修改器，并使用它来修改哈希并将值存储在 Vue 实例上。如果哈希不存在，我们将传递一个空字符串给路径变量：

```js
   window.onhashchange = () => {
    let hash = window.location.hash.substring(1);
    app.path = (hash || '');
   }
```

现在，我们需要将这个路径变量传递给 Dropbox 组件。在视图中添加一个名为`p`的 prop，将`path`变量作为值：

```js
   <div id="app">
    <dropbox-viewer :p="path"></dropbox-viewer>
   </div>
```

在 Dropbox 组件中添加`props`对象以接受一个字符串：

```js
   props: {
     p: String
    },
```

现在，我们将在`dropbox-viewer`组件中添加一个`watch`函数。这个函数将监视`p` prop，并在更新时使用修改后的路径调用`updateStructure()`方法：

```js
   watch: {
     p() {
      this.updateStructure(this.p);
     }
   }
```

回到浏览器，我们现在应该能够像以前一样通过文件夹链接和面包屑导航浏览我们的 Dropbox 结构。我们现在还可以使用浏览器的后退和前进按钮，以及任何键盘快捷键，通过文件夹进行导航。

在我们前往第六章之前，*使用 Vuex 缓存当前文件夹结构*，并在我们的应用程序中引入文件夹缓存，我们可以对 Dropbox 组件进行一些优化。

首先，在`getFolderStructure`函数中，我们可以删除第一行，其中 URL 哈希被设置为路径。这是因为当链接被使用时，URL 已经被更新。从代码中删除这行：

```js
   window.location.hash = path;
```

其次，在 Dropbox 组件中，`this.path`变量和`p` prop 中现在存在重复。消除这种重复需要进行一些轻微的改动，因为你不能像处理路径那样直接修改 prop；然而，它需要保持同步，以便正确渲染面包屑。

从 Dropbox 组件的数据对象中删除`path`属性，并从`getFolderStructure`函数中删除`this.path = path`这一行。

接下来，将 prop 更新为等于`path`，而不是`p`。这还需要更新`watch`函数，以监视`path`变量而不是`p()`。

将`created`方法更新为只使用`this.path`作为函数的参数。Dropbox 组件现在应该是这样的：

```js
   Vue.component('dropbox-viewer', {
     template: '#dropbox-viewer-template',

     props: {
      path: String
     },

     data() {
       return {
         accessToken: 'XXXX',
        structure: {},
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

           const structure = {
            folders: [],
            files: []
           }

          for (let entry of response.entries) {
            // Check ".tag" prop for type
            if(entry['.tag'] == 'folder') {
             structure.folders.push(entry);
             } else {
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

       updateStructure(path) {
        this.isLoading = true;
        this.getFolderStructure(path);
       }
    },

     created() {
       this.getFolderStructure(this.path);
     },

      watch: {
      path() {
        this.updateStructure(this.path);
      }
     },
   });
```

将视图更新为接受`prop`作为`path`：

```js
   <dropbox-viewer :path="path"></dropbox-viewer>
```

现在，我们需要确保父`Vue`实例在页面加载和哈希变化时具有正确的路径。为了避免重复，我们将使用一个方法和一个`created`函数来扩展我们的`Vue`实例。

将路径变量设置为空字符串。创建一个名为`updateHash()`的新方法，它会从窗口哈希中删除第一个字符，然后将`path`变量设置为哈希或空字符串。接下来，创建一个`created()`函数，运行`updateHash`方法。

`Vue`实例现在看起来像这样：

```js
  const app = new Vue({
    el: '#app',

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

最后，为了避免重复，当地址栏中的哈希发生变化时，我们可以触发`updateHash`方法：

```js
   window.onhashchange = () => {
     app.updateHash();
   }
```

# 最终代码

现在我们的代码已经完成，你的视图和 JavaScript 文件应该如下所示。首先，视图应该是这样的：

```js
   <div id="app">
      <dropbox-viewer :path="path"></dropbox-viewer>
    </div>

   <script type="text/x-template" id="dropbox-viewer- 
     template">
    <div>
      <h1>Dropbox</h1>

      <transition name="fade">
        <div v-if="isLoading">
          <div v-if="isLoading == 'error'">
            <p>There seems to be an issue with the URL 
            entered.</p>
            <p><a href="">Go home</a></p>
          </div>
          <div v-else>
            Loading...
          </div>
        </div>
      </transition>

      <transition name="fade">
        <div v-if="!isLoading">
          <breadcrumb :p="path"></breadcrumb>
          <ul>
            <template v-for="entry in structure.folders">
             <folder :f="entry"></folder>
            </template>

           <template v-for="entry in structure.files">
             <file :d="dropbox()" :f="entry"></file>
           </template>
         </ul>
       </div>
      </transition>

     </div>
    </script>
```

相应的 JavaScript 应用程序应该是这样的：

```js
   Vue.component('breadcrumb', {
        template: '<div>' +
        '<span v-for="(f, i) in folders">' +
         '<a :href="f.path">{{ f.name || 'Home' }}</a>' +
          '<i v-if="i !== (folders.length - 1)"> &raquo; 
           </i>' + '</span>' + 
        '</div>',
      props: {
      p: String
     },
     computed: {
        folders() {
          let output = [],
           slug = '',
           parts = this.p.split('/');

        for (let item of parts) {
          slug += item;
            output.push({'name': item || 'home', 'path': 
            '#' + slug});
          slug += '/';
         }

         return output;
        }
      }
    });

    Vue.component('folder', {
       template: '<li><strong><a :href="\'#\' + 
       f.path_lower">{{ f.name }}</a></strong></li>',
      props: {
       f: Object
      }
   });

   Vue.component('file', {
         template: '<li><strong>{{ f.name }}</strong><span 
         v-if="f.size"> - {{ bytesToSize(f.size) }}</span>
         <span v-if="link"> - <a :href="link">Download</a>
         </span></li>',
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
          let i = parseInt(Math.floor(Math.log(bytes) / 
           Math.log(1024)));
        // Round to 2 decimal places and select the 
           appropriate unit from the array
         output = Math.round(bytes / Math.pow(1024, i), 2)   
         + ' ' + this.byteSizes[i];
       }

       return output
      }
    },

     created() {
       this.d.filesGetTemporaryLink({path:   
       this.f.path_lower}).then(data => {
         this.link = data.link;
       });
      },
    });

     Vue.component('dropbox-viewer', {
       template: '#dropbox-viewer-template',

     props: {
       path: String
      },

     data() {
       return {
       accessToken: 'XXXX',
       structure: {},
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

     updateStructure(path) {
       this.isLoading = true;
       this.getFolderStructure(path);
      }
    },

    created() {
     this.getFolderStructure(this.path);
    },

   watch: {
     path() {
       this.updateStructure(this.path);
       }
     },
  });

     const app = new Vue({
      el: '#app',

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

   window.onhashchange = () => {
   app.updateHash();
 }
```

# 总结

现在，我们拥有一个完全功能的 Dropbox 查看器应用程序，具有文件夹导航和文件下载链接。我们可以使用文件夹链接或面包屑进行导航，并使用后退和/或前进按钮。我们还可以共享或书签一个链接，并加载该文件夹的内容。

在第六章中，*使用 Vuex 缓存当前文件夹结构*，我们将通过使用 Vuex 缓存当前文件夹内容来加快导航过程。
