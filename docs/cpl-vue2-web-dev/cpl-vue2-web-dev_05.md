# 第五章：通过文件树导航和从 URL 加载文件夹

在第四章中，*使用 Dropbox API 获取文件列表*，我们创建了一个应用程序，列出了指定 Dropbox 文件夹的文件和文件夹内容。现在我们需要使我们的应用程序易于导航。这意味着用户将能够点击文件夹名称以进入并列出其内容，并且还能够下载文件。

在继续之前，请确保在 HTML 中包含了 Vue 和 Dropbox 的 JavaScript 文件。

在本章中，我们将会：

+   为文件和文件夹创建一个组件

+   为文件夹组件添加链接以更新目录列表

+   为文件组件添加下载按钮

+   创建一个面包屑组件，以便用户可以轻松地返回到上一级目录

+   动态更新浏览器的 URL，这样如果一个文件夹被收藏夹或链接分享，正确的文件夹将被加载

# 分离文件和文件夹

在创建组件之前，我们需要将文件和文件夹分开放置在我们的结构中，这样我们就可以轻松地识别和显示不同类型的文件和文件夹。由于每个项目上都有`.tag`属性，我们可以将文件夹和文件分开。

首先，我们需要更新我们的`structure`数据属性，使其成为一个包含`files`和`folders`数组的对象。

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

下一步是用当前文件夹的数据填充这些数组。所有以下代码都在`getFolderStructure`方法的第一个`then()`函数中执行。

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

这段代码通过循环遍历条目，并检查`.tag`属性。由于属性本身以`.`开头，我们无法像访问`entry.name`属性那样使用对象样式的表示法来访问该属性。然后，根据类型，我们使用 JavaScript 的 push 方法将条目追加到`files`或`folders`数组中。

为了显示这些新数据，我们需要更新视图以循环遍历这两种类型的数组。这是使用`<template>`标签的一个完美用例，因为我们想要将这两个数组都追加到同一个无序列表中。

更新视图以单独列出这两个数组。我们可以从文件夹显示部分中删除`size`选项，因为它永远不会有`size`属性：

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

通过将数据类型分离出来，我们可以创建单独的组件来分隔数据和方法。创建一个`folder`组件，它接受一个属性，允许通过`folder`对象变量传递。由于模板非常小，所以不需要基于视图或`<script>`块的模板；相反，我们可以将其作为字符串传递给组件：

```js
      Vue.component('folder', {
        template: '<li><strong>{{ f.name }}</strong>      
        </li>',
        props: {
          f: Object
        },
      });
```

为了使我们的代码更小、更少重复，属性被称为`f`。这样可以整理视图，并让组件名称确定显示类型，而不需要多次重复使用单词`folder`。

更新视图以使用文件夹组件，并将`entry`变量传递给`f`属性：

```js
      <template v-for="entry in structure.folders">
        <folder :f="entry"></folder>
      </template>
```

通过创建一个`file`组件来重复这个过程。创建`file`组件时，我们可以将`bytesToSize`方法和`byteSizes`数据属性从父`dropbox-viewer`组件中移动，因为它只会在显示文件时使用：

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

再次，我们可以使用`f`作为属性名称来减少重复（和应用程序的文件大小）。再次更新视图以使用这个新组件：

```js
      <template v-for="entry in structure.files">
        <file :f="entry"></file>
      </template>
```

# 链接文件夹并更新结构

现在我们已经将文件夹和文件分开了，我们可以将文件夹名称转换为链接。这些链接将更新结构以显示所选文件夹的内容。为此，我们将使用每个文件夹的`path_lower`属性来构建链接目标。

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

现在我们需要为此链接添加一个`click`监听器。当点击时，我们需要在`dropbox-viewer`组件上触发`getFolderStructure`方法。虽然点击方法将使用每个实例上的`f`变量来获取数据，但最好将`href`属性设置为文件夹的 URL。

使用本书早期章节中学到的知识，在`folder`组件上创建一个方法，当触发时将文件夹路径发送到父组件。当触发时，`dropbox-viewer`组件还需要一个新的方法来使用给定的参数更新结构。

在`folder`组件上创建新方法，并将`click`事件添加到文件夹链接上。与`v-bind`指令一样，我们现在使用`v-on`的简写符号`@`表示：

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

除了定义`click`事件之外，还添加了一个事件修饰符。在点击事件之后使用`.prevent`将`preventDefault`添加到链接操作中，这样可以阻止链接实际上转到指定的 URL，而是让`click`方法处理所有事情。有关更多事件修饰符及其详细信息，请参阅 Vue 文档。

当点击时，将触发`navigate`方法，该方法使用`path`变量发出文件夹的较低路径。

现在我们有了`click`处理程序和被发出的变量，我们需要更新视图以触发父`dropbox-viewer`组件上的方法：

```js
      <template v-for="entry in structure.folders">
        <folder :f="entry" @path="updateStructure">      
        </folder>
      </template>
```

在 Dropbox 组件上创建一个与`v-on`属性的值相同的新方法，本例中为`updateStructure`。此方法将有一个参数，即我们之前发出的路径。从这里，我们可以使用路径变量触发我们原始的`getFolderStructure`方法：

```js
      updateStructure(path) {
        this.getFolderStructure(path);
      }
```

在浏览器中查看我们的应用程序现在应该列出文件夹和链接，并在点击时显示新文件夹的内容。

但是，在这样做时，会引发一些问题。首先，文件和文件夹被附加到现有列表而不是替换它。其次，用户没有任何反馈，表明应用程序正在加载下一个文件夹。

可以通过在附加新结构之前清除文件夹和文件数组来解决第一个问题。可以通过使用应用程序开始时使用的加载屏幕来解决第二个问题-这将为用户提供一些反馈。

为了解决第一个问题，在`getFolderStructure`方法的成功 promise 函数中创建一个新的`structure`对象。该对象应该复制`data`对象中的`structure`对象。这将为文件和文件夹设置空数组。更新`for`循环以使用本地结构数组而不是组件数组。最后，使用更新后的文件和文件夹更新组件的`structure`对象：

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

当应用程序挂载并创建自己的结构对象时，会调用此方法，因此不需要在`data`函数中声明数组。将数据对象更新为仅将`structure`属性初始化为对象：

```js
      data() {
        return {
          accessToken: 'XXXX',
          structure: {},
          isLoading: true
        }
      }
```

现在运行应用程序将渲染文件列表，当点击进入新文件夹时，文件列表将被清除并更新。为了给用户一些反馈并让他们知道应用程序正在工作，让我们在每次点击后切换加载屏幕。

然而，在此之前，让我们充分了解延迟来自何处以及何时最好触发加载屏幕。

点击链接是瞬间的，这会触发文件夹组件上的导航方法，进而触发 Dropbox 组件上的`updateStructure`方法。当应用程序到达 Dropbox 实例上的`filesListFolder`函数时，延迟发生在`getFolderStructure`方法内部。由于我们可能希望在以后的某个日期触发`getFolderStucture`方法而不触发加载屏幕，所以在`updateStructure`方法中将`isLoading`变量设置为`true`：

```js
      updateStructure(path) {
        this.isLoading = true;
        this.getFolderStructure(path);
      }
```

通过动画，应用程序在导航文件夹时在加载屏幕和文件夹结构之间淡入淡出。

# 从当前路径创建面包屑

在浏览文件夹或任何嵌套结构时，始终有一个可用的面包屑非常好，这样用户就知道他们在哪里，他们走了多远，也可以轻松返回到以前的文件夹。我们将为面包屑制作一个组件，因为它将包含各种属性、计算函数和方法。

面包屑组件将以链接的形式列出每个文件夹的深度，链接指向文件夹图标。点击链接将直接将用户导航到该文件夹 - 即使它在多个层级上。为了实现这一点，我们需要一个链接列表，我们可以循环遍历，每个链接都有两个属性 - 一个是文件夹的完整路径，另一个只是文件夹的名称。

例如，如果我们有文件夹结构为`/images/holiday/summer/iphone`，我们希望能够点击`Holiday`并且应用程序导航到`/images/holiday`。

创建面包屑组件 - 现在，在模板属性中添加一个空的`<div>`：

```js
      Vue.component('breadcrumb', {
        template: '<div></div>'
      });
```

将组件添加到视图中。我们希望面包屑与结构列表一起淡入淡出，因此我们需要调整 HTML，将列表和面包屑组件都包裹在具有`v-if`声明的容器中：

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

现在我们需要一个变量来存储当前文件夹路径。然后我们可以在面包屑组件中操作这个变量。这个变量将被存储并在 Dropbox 组件上更新，并传递给面包屑组件。

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

现在我们需要确保当从 Dropbox API 检索到结构时，该路径会得到更新。在`getFolderStructure`方法中，在禁用`isLoading`变量之前执行此操作。这样可以确保它只在结构加载完成之后，但在文件和文件夹显示之前更新：

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

现在我们有了一个填充了当前路径的变量，我们可以将其作为属性传递给面包屑组件。在面包屑上添加一个新的属性，将路径变量作为值：

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

`p`属性现在包含了我们所在位置的完整路径（例如`/images/holiday/summer`）。我们想要将这个字符串分解，以便我们可以识别文件夹名称并构建面包屑以供组件渲染。

在组件上创建一个`computed`对象，并创建一个名为`folders()`的新函数。这将为我们创建面包屑数组，供我们在模板中循环遍历：

```js
      computed: {
       folders() {   
        }
      }
```

现在我们需要设置一些变量供我们使用。创建一个新的空数组`output`。这是我们要构建面包屑的地方。我们还需要一个空字符串变量`slug`。`slug`变量是指 URL 的一部分，它的使用在 WordPress 中很流行。最后一个变量是作为数组创建的路径。我们知道，每个文件夹都是由`/`分隔的，我们可以使用这个来将字符串分解成各个部分：

```js
      computed: {
        folders() {
 let output = [],
 slug = '',
 parts = this.p.split('/');
        }
      }
```

如果我们查看`Summer`文件夹的`parts`变量，它会像下面这样：

```js
      ['images', 'holiday', 'summer']
```

现在我们可以循环遍历数组来创建面包屑。每个面包屑项都将是一个对象，包含个别文件夹的`name`，例如`holiday`或`summer`，以及`slug`，前者为`/images/holiday`，后者为`/images/holiday/summer`。

每个对象都将被构建并添加到`output`数组中。然后我们可以返回这个输出供我们的模板使用：

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

这个循环通过以下步骤创建我们的面包屑。以这个例子为例，我们假设我们在`/images/holiday`文件夹中：

1.  `parts`现在将是一个包含三个项的数组，`['', 'images', holiday']`。如果你要拆分的字符串以你正在拆分的项开头，那么将创建一个空项作为第一项。

1.  在循环开始时，第一个 slug 变量将等于`''`，因为它是第一个项。

1.  `output`数组将附加一个新项，其对象为`{'name': '', 'path': ''}`。

1.  然后，`slug`变量在末尾添加了一个斜杠。

1.  循环遍历下一个项时，`slug`变量会将该项的名称（`images`）添加到其中。

1.  `output`现在添加了一个新对象，其值为`{'name': 'images', 'path': '/images'}`。

1.  对于最后一项，除了下一个名称`holiday`之外，还添加了一个`/`。

1.  `output`获取最后添加的对象，其值为`{'name': 'holiday', 'path': '/images/holiday'}` - 注意路径正在构建，而名称仍然是单数文件夹名称。

现在我们有了可以在视图中循环遍历的面包屑输出数组。

我们在将项附加到输出数组之后添加斜杠的原因是 API 规定，要获取 Dropbox 的根目录，我们传入一个空字符串，而所有其他路径必须以`/`开头。

下一步是将面包屑输出到我们的视图中。由于这个模板很小，我们将使用多行 JavaScript 表示法。循环遍历`folders`计算变量中的项，为每个项输出一个链接。不要忘记在所有链接周围保留一个包含元素：

```js
      template: '<div>' +
 '<span v-for="f in folders">' +
 '<a :href="f.path">{{ f.name }}</a>' +
 '</span>' + 
      '</div>'
```

在浏览器中渲染此应用程序应该显示一个面包屑 - 尽管有点挤在一起，缺少一个主页链接（因为第一个项没有名称）。返回到`folders`函数并添加一个`if`语句 - 检查该项是否有名称，如果没有，则添加一个硬编码的值：

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

另一种选择是在模板本身中添加`if`语句：

```js
      template: '<div>' +
        '<span v-for="f in folders">' +
          '<a :href="f.path">{{ f.name || 'Home' }}</a>' +
        '</span>' + 
      '</div>'
```

如果我们想在文件夹名称之间显示分隔符，比如斜杠或箭头，这可以很容易地添加。然而，当我们想在链接之间显示分隔符，但不在开头或结尾时，会出现一个小障碍。为了解决这个问题，我们将利用循环时可用的`index`关键字。然后，我们将将其与数组的长度进行比较，并在元素上操作`v-if`声明。

在循环遍历数组时，Vue 允许您利用另一个变量。默认情况下，这是索引（数组中项的位置）；然而，如果您的数组是以键/值方式构建的，则索引可以设置为一个值。如果是这种情况，您仍然可以通过添加第三个变量来访问索引。由于我们的数组是一个简单的列表，我们可以轻松地使用这个变量：

```js
      template: '<div>' +
        '<span v-for="(f, i) in folders">' +
          '<a :href="f.path">{{ f.name || 'Home' }}</a>' +
          '<span v-if="i !== (folders.length - 1)"> » 
            </span>' +
        '</span>' + 
      '</div>',
```

更新`f`变量为包含一个`f`和一个`i`的括号，用逗号分隔。`f`变量是循环中的当前文件夹，而已创建的`i`变量是该项的索引。请记住，数组索引从`0`开始，而不是从`1`开始。

我们添加的分隔符包含在一个带有`v-if`属性的 span 标签中，其内容可能看起来令人困惑。这是将当前索引与`folders`数组的长度（它有多少项）减 1 混淆了。`- 1`是因为索引从 0 开始，而不是从 1 开始，正如您所期望的那样。如果数字不匹配，则显示`span`元素。

我们需要做的最后一件事是使我们的面包屑导航到所选文件夹。我们可以通过修改我们为`folder`组件编写的导航函数来实现这一点。然而，由于我们的整个组件是面包屑，而不是每个单独的链接，所以我们需要修改它以接受一个参数。

首先，在链接上添加`click`事件，传入`folder`对象：

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

最后一步是在路径被发出时触发父方法。为此，我们可以在`dropbox-viewer`组件上利用相同的`updateStructure`方法：

```js
      <breadcrumb :p="path" @path="updateStructure">      
      </breadcrumb>
```

现在我们有了一个完全可操作的面包屑，允许用户通过文件夹链接向下导航文件夹结构，并通过面包屑链接向上导航。

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

现在，我们的用户可以通过文件夹结构导航，我们需要添加下载文件的功能。不幸的是，这并不像访问文件上的链接属性那样简单。为了获取下载链接，我们必须查询 Dropbox API 以获取每个文件。

在创建文件组件时，我们将查询 API，这将异步获取下载链接并在可用时显示。在此之前，我们需要将 Dropbox 实例提供给文件组件。

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

现在，我们将添加一个名为`link`的数据属性。默认情况下，它应该设置为`false`，以便我们可以隐藏链接，并在 API 返回值后填充它的下载链接。

将`created()`函数添加到文件组件中，并在其中添加 API 调用：

```js
     created() {
      this.d.filesGetTemporaryLink({path:    
       this.f.path_lower}).then(data => {
        this.link = data.link;
     });
    }
```

此 API 方法接受一个对象，类似于`filesListFolder`函数。我们正在传递当前文件的路径。一旦数据返回，我们可以将组件的`link`属性设置为下载链接。

现在我们可以在组件的模板中添加一个下载链接。只有在检索到下载链接后才添加`v-if`以显示`<a>`：

```js
   template: '<li><strong>{{ f.name }}</strong><span v-  
    if="f.size"> - {{ bytesToSize(f.size) }}</span><span    
    v-if="link"> - <a :href="link">Download</a></span>  
   </li>'
```

浏览文件时，我们现在可以看到每个文件旁边出现一个下载链接，其速度取决于您的互联网连接和 API 速度。

现在添加了下载链接的完整文件组件如下所示：

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

# 更新 URL 哈希并使用它来浏览文件夹：

通过结构列表和面包屑，我们现在可以完全浏览我们的 Dropbox Web 应用程序，并且可以添加和更新浏览器 URL 以便快速访问和共享文件夹。我们可以通过两种方式实现：我们可以更新哈希，例如`www.domain.com/#/images/holiday/summer`，或者我们可以将所有路径重定向到单个页面，并处理 URL 中的路由而不使用哈希。

对于这个应用程序，我们将在 URL 中使用`#`方法。当我们介绍`vue-router`时，我们将在本书的第三部分介绍 URL 路由技术。

在让应用程序显示 URL 对应的文件夹之前，我们首先需要在导航到新文件夹时更新 URL。我们可以使用原生的`window.location.hash` JavaScript 对象来实现这一点。我们希望在用户点击链接时立即更新 URL，而不是等待数据加载后再更新。

由于每当我们更新结构时都会触发`getFolderStructure`方法，因此将代码添加到此函数的顶部。这意味着 URL 会被更新，然后调用 Dropbox API 来更新结构：

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

当您浏览应用程序时，应该更新 URL 以包括当前文件夹路径。

然而，当你按下刷新按钮时，你会发现一个问题，那就是 URL 会重置，只有一个哈希，后面没有文件夹，因为它是通过`created()`函数中传递的空路径重置的。

我们可以通过在`created`函数中将当前哈希传递给`getFolderStructure`来解决这个问题，但是如果这样做，我们需要进行一些检查和错误捕获。

首先，在调用`window.location.hash`时，你也会得到哈希作为字符串的一部分返回，所以我们需要将其删除。其次，我们需要处理 URL 不正确的情况，如果用户输入了不正确的路径或者文件夹被移动了。最后，我们需要让用户在浏览器中使用后退和前进按钮（或键盘快捷键）。

# 根据 URL 显示文件夹

当我们的应用程序挂载时，它已经调用了一个函数来请求基本文件夹的结构。我们编写了这个函数以允许路径被传递，并且在`created()`函数中，我们已经将值固定为根文件夹`''`。这使我们能够灵活地调整这个函数，以传递 URL 中的哈希值，而不是固定字符串。

更新函数以接受 URL 的哈希值，并且如果没有哈希值，则使用原始的固定字符串：

```js
  created() {
    let hash = window.location.hash.substring(1);
    this.getFolderStructure(hash || '');
  }
```

创建一个名为`hash`的新变量，并将`window.location.hash`赋值给它。因为变量以`#`开头，对于我们的应用程序来说是不需要的，所以使用`substring`函数从字符串中删除第一个字符。然后我们可以使用逻辑运算符来使用 hash 变量，或者如果它等于空，使用原始的固定字符串。

现在你应该能够通过更新 URL 来浏览你的应用程序。如果你随时按下刷新按钮或将 URL 复制粘贴到不同的浏览器窗口中，你所在的文件夹应该会加载。

# 显示错误消息

由于我们的应用程序接受 URL，我们需要处理一种情况，即用户输入 URL 时出现错误，或者共享的文件夹已经被移动。

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

如果需要诊断除了错误文件路径之外的问题，可以保留`console.log`。尽管 API 可能会抛出多种不同的错误，但在这个应用程序中，我们假设错误是由于错误的路径。如果您想在应用程序中处理其他错误，可以通过其`status_code`属性来识别错误类型。有关此更多详细信息，请参阅 Dropbox API 文档。

更新视图以处理这个新的`isLoading`变量属性。当设置为错误时，`isLoading`变量仍然为"true"，因此在加载元素中，添加一个新的`v-if`来检查加载变量是否设置为`error`：

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

如果`isLoading`变量的第一个元素设置为`error`，则显示该元素；否则，显示加载文本。在错误文本中，包含一个链接，将用户发送回当前 URL，不带任何 URL 哈希。这将将他们“重置”回文档树的顶部，以便他们可以向下导航。一个改进可以是将当前 URL 拆分，并建议删除最后一个文件夹后相同的 URL。

验证错误代码是否加载，方法是在 URL 的末尾添加一个不存在的路径，并确保显示错误消息。请注意，用户可能会在某种意义上对此错误消息产生误报，即如果 Dropbox API 抛出任何类型的错误，将显示此消息。

# 在浏览器中使用返回和前进按钮

为了在浏览器中使用返回和前进按钮，我们需要大幅更新我们的代码。目前，当用户从结构或面包屑中的任何一个文件夹中单击时，我们通过在`click`处理程序上使用`.prevent`来阻止浏览器的默认行为。然后，在处理文件夹之前，我们立即更新 URL。

然而，如果我们允许应用程序使用本机行为更新 URL，我们可以监听哈希 URL 的更新，并使用它来检索我们的新结构。使用这种方法，返回和前进按钮将无需任何进一步的干预即可工作，因为它们将更新 URL 哈希。

这也将提高我们应用程序的可读性，并减少代码量，因为我们将能够删除链接上的`navigate`方法和`click`处理程序。

# 删除不需要的代码

在添加更多代码之前，第一步是从组件中删除不必要的代码。从面包屑开始，从组件中删除`navigate`方法，并从模板中的链接中删除`@click.prevent`属性。

我们还需要更新每个项目的`slug`，在其前面添加`#` - 这样可以确保应用程序在点击时不会尝试导航到全新的页面。由于我们在文件夹的`computed`函数中循环遍历面包屑项，所以在将对象推送到`output`数组时，为每个`slug`添加一个哈希：

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

我们还可以在`dropbox-viewer-template`中的面包屑组件上删除`v-on`声明。它只应该有传递的路径作为属性：

```js
    <breadcrumb :p="path"></breadcrumb>
```

现在我们可以为文件夹组件重复相同的模式。从链接中删除`@click.prevent`声明并删除`navigate`方法。

由于我们在显示之前不会循环遍历或编辑文件夹对象，所以我们可以在模板中添加`#`。由于我们告诉 Vue `href` 绑定到一个 JavaScript 对象（使用冒号），我们需要用引号将哈希封装起来，并使用 JavaScript 的`+`符号将其与文件夹路径连接起来。

我们已经在单引号和双引号之间，所以我们需要告诉 JavaScript 我们*确实*是一个单引号，这可以通过在单引号字符前面使用反斜杠来实现：

```js
   Vue.component('folder', {
    template: '<li><strong><a :href="\'#\' +   
    f.path_lower">{{ f.name }}</a></strong></li>',
     props: {
      f: Object
     }
   });
```

我们还可以从视图中的`<folder>`组件中删除`@path`属性。

```js
   <template v-for="entry in structure.folders">
     <folder :f="entry"></folder>
   </template>
```

现在我们的代码看起来更加清晰、简洁，并且文件大小更小。在浏览器中查看应用程序将呈现所在文件夹的结构；但是，点击链接将更新 URL 但不会更改显示内容。

# 通过更改 URL 并在实例外设置 Vue 数据来更新结构

现在我们的 URL 已经正确更新，我们可以在哈希更改时获取新的结构。这可以使用 JavaScript 的`onhashchange`函数来实现。

我们将创建一个函数，每当 URL 的哈希更新时触发，这将更新父 Vue 实例上的路径变量。这个变量将作为属性传递给子`dropbox-viewer`组件。该组件将监视变量的变化，并在更新时检索新的结构。

首先，更新父 Vue 实例，使其具有一个带有路径键的数据对象 - 设置为空字符串属性。我们还将将 Vue 实例分配给一个名为`app`的常量变量 - 这允许我们在实例外设置数据和调用方法：

```js
 const app = new Vue({
    el: '#app',
 data: {
 path: ''
 }
 });
```

下一步是在每次 URL 更新时更新这个数据属性。这是通过使用`window.onhashchange`来实现的，它是一个原生的 JavaScript 函数，每当 URL 中的哈希值发生变化时就会触发。

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

在 Dropbox 组件中添加`props`对象，以接受一个字符串：

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

回到浏览器，我们现在应该能够像以前一样通过 Dropbox 结构进行导航，使用文件夹链接和面包屑作为导航。我们现在应该能够使用浏览器的后退和前进按钮，以及任何键盘快捷键，来通过文件夹进行导航。

在我们进入第六章之前，使用`vuex`为我们的应用程序引入文件夹缓存，*使用 Vuex 缓存当前文件夹结构*，我们可以对 Dropbox 组件进行一些优化。

首先，在`getFolderStructure`函数中，我们可以删除设置 URL 哈希为路径的第一行代码。这是因为当链接被使用时，URL 已经被更新了。从你的代码中删除这一行：

```js
   window.location.hash = path;
```

其次，在 Dropbox 组件中，现在存在`this.path`变量和`p` prop 的重复。消除这个重复需要进行一些微小的改动，因为你不能像处理路径那样直接修改 prop；然而，它需要保持同步，以便正确渲染面包屑。

从 Dropbox 组件的数据对象中删除`path`属性，并且还要从`getFolderStructure`函数中删除`this.path = path`这一行。

接下来，将`prop`更新为等于`path`，而不是`p`。这还需要更新`watch`函数，以监视`path`变量而不是`p()`。

将`created`方法更新为只使用`this.path`作为函数的参数。现在，Dropbox 组件应该如下所示：

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

更新视图以接受`prop`作为`path`：

```js
   <dropbox-viewer :path="path"></dropbox-viewer>
```

现在，我们需要确保父级`Vue`实例在页面加载和哈希值变化时具有正确的路径。为了避免重复，我们将扩展我们的`Vue`实例，添加一个方法和一个`created`函数。

将路径变量设置为空字符串。创建一个名为`updateHash()`的新方法，它会从窗口哈希中删除第一个字符，然后将`path`变量设置为哈希值或空字符串。接下来，创建一个名为`created()`的函数，它会运行`updateHash`方法。

`Vue`实例现在看起来是这样的：

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

最后，为了避免重复，当地址栏中的哈希值发生变化时，我们可以触发`updateHash`方法：

```js
   window.onhashchange = () => {
     app.updateHash();
   }
```

# 最终代码

我们的代码现在已经完成，你的视图和 JavaScript 文件应该如下所示。首先，视图应该是这样的：

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

附带的 JavaScript 应用应该是这样的：

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

我们现在有一个完全功能的 Dropbox 查看器应用程序，具有文件夹导航和文件下载链接。我们可以使用文件夹链接或面包屑进行导航，并使用后退和/或前进按钮。我们还可以共享或书签一个链接，并加载该文件夹的内容。

在第六章《使用 Vuex 缓存当前文件夹结构》中，我们将通过使用 Vuex 缓存当前文件夹的内容来加快导航过程。
