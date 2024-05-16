# 第七章：预缓存其他文件夹和文件以实现更快的导航

在本章中，本节的最后一部分，我们将通过引入更多的缓存来进一步加快我们的 Dropbox 文件浏览器的速度。到目前为止，我们已经构建了一个可以查询 Dropbox API 并返回文件和文件夹的应用程序。从那里开始，我们添加了文件夹导航，包括更新用于链接共享的 URL 以及能够使用后退和前进按钮。有了这个功能，在第六章中，我们引入了 Vuex 来存储当前文件夹路径和我们访问过的文件夹的内容。

本章将讨论以下内容：

+   预缓存不仅用户当前所在的文件夹，还包括子文件夹。这将通过循环遍历当前显示的文件夹并检查它们是否已经被缓存来完成。如果没有，我们可以从 API 中获取数据。

+   如果用户通过直接 URL 进入，存储父文件夹的内容。这将通过利用面包屑路径向上遍历树来完成。

+   缓存文件的下载链接。目前，无论文件夹是否已被我们的代码缓存，都需要为每个遇到的文件调用 API。

通过这些改进，我们可以确保应用程序每个项目只与 API 联系一次，而不是像最初那样无数次。

# 缓存子文件夹

通过子文件夹和父文件夹缓存，我们不一定需要编写新代码，而是将现有代码重新组织和重新用途化为一个更模块化的系统，以便可以单独调用每个部分。

以下流程图应该帮助您可视化缓存当前文件夹和子文件夹所需的步骤：

![](img/00012.jpeg)

在查看流程图时，您可以立即看到应用程序所需的事件中存在一些重复。在两个点上，应用程序需要决定缓存中是否存在一个文件夹，如果不存在，则查询 API 以获取数据并存储结果。尽管在流程图上只出现两次，但这个功能需要多次，每次都需要针对当前位置的每个文件夹。

我们还需要将显示逻辑与查询和存储逻辑分开，因为我们可能需要从 API 加载并存储，而不更新视图。

# 计划应用程序方法

考虑到前一节的内容，我们可以借此机会修订和重构我们的`dropbox-viewer`应用程序上的方法，确保每个操作都有自己的方法。这样我们就可以在需要时调用每个操作。在进入代码之前，让我们根据前面的流程图规划出需要创建的方法。

首先要注意的是，每次查询 API 时，我们都需要将结果存储在缓存中。由于我们不需要在缓存中存储任何内容，除非调用 API，我们可以将这两个操作合并在同一个方法中。我们还经常需要检查特定路径的缓存中是否有内容，并根据需要加载或从 API 中检索它。我们可以将此添加到自己的方法中返回数据。

让我们绘制出我们需要创建的方法：

+   `getFolderStructure`：此方法将接受路径的单个参数，并返回文件夹条目的对象。它将负责检查数据是否在缓存中，如果不在，则查询 Dropbox API。

+   `displayFolderStructure`：此方法将触发前面的函数，并使用数据更新组件上的`structure`对象，以显示视图中的文件和文件夹。

+   `cacheFolderStructure`：此方法将包含`getFolderStructure`方法以缓存每个子文件夹-我们将探讨几种触发此方法的方式。

我们可能需要创建更多的方法，但这三个方法将是组件的主干。我们将保留路径和 slug-computed 属性以及`dropbox()`方法。删除其余的对象、方法和函数，使您的`dropbox-viewer`回到基本状态：

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
  }
});
```

# 创建`getFolderStructure`方法

在组件上创建一个名为`getFolderStructure`的新方法。如前所述，此方法需要接受一个路径参数。这样我们就可以同时使用当前路径和子路径：

```js
getFolderStructure(path) {

}
```

此方法需要检查缓存并返回数据。在方法内部创建一个名为`output`的新变量，并返回它：

```js
getFolderStructure(path) {
 let output;

 return output;
}
```

在[第六章](https://cdp.packtpub.com/vue_js_by_example/wp-admin/post.php?post=82&action=edit#post_71)中缓存数据时，我们使用`slug`作为存储中的键。`slug`是通过使用当前路径生成的；然而，我们不能在新方法中使用它，因为它固定在其当前位置。

创建一个名为`generateSlug`的新方法。它将接受一个参数，即路径，并返回使用 slug-computed 函数中的替换后的字符串：

```js
generateSlug(path) {
  return path.toLowerCase()
    .replace(/^\/|\/$/g, '')
    .replace(/ /g,'-')
    .replace(/\//g,'-')
    .replace(/[-]+/g, '-')
    .replace(/[^\w-]+/g,'');
}
```

现在我们可以删除计算的`slug`函数，这样我们就不会有重复的代码了。

回到我们的`getFolderStructure`方法，创建一个新变量，使用新方法存储路径的 slug 版本。为此，我们将使用`const`创建一个不可更改的变量。

```js
getFolderStructure(path) {
  let output;

  const slug = this.generateSlug(path);

  return output;
}
```

我们将创建的最后一个变量是数据路径，就像我们在第八章中所做的那样，*介绍 Vue-Router 和加载基于 URL 的组件*。这将使用我们刚刚创建的新`slug`变量：

```js
getFolderStructure(path) {
  let output;

  const slug = this.generateSlug(path),
      data = this.$store.state.structure[slug];

  return output;
}
```

现在我们可以在这里使用先前代码中的`data` `if`语句，其中包含 Dropbox 函数调用的空间。如果存储中存在`data`，我们可以立即将其分配给`output`：

```js
getFolderStructure(path) {
  let output;

  const slug = this.generateSlug(path),
      data = this.$store.state.structure[slug];

 if(data) {
 output = data;
 } else {

 }

  return output;
}
```

然而，通过 Dropbox API 调用，我们可以对其进行调整以适应这段新代码。之前，它是从 API 中检索数据，然后触发一个方法来保存和显示结构。由于我们需要将检索到的数据存储在`output`变量中，我们将改变数据的流动方式。我们将不再触发一个方法，而是利用这个机会首先将响应存储在缓存中，然后将数据返回给`output`变量。

由于我们只使用 API 调用的条目，我们还将更新存储以仅缓存响应的这部分。这将减少应用程序的代码和复杂性：

```js
getFolderStructure(path) {
  let output;

  const slug = this.generateSlug(path),
      data = this.$store.state.structure[slug];

  if(data) {
    output = data;
  } else {

    output = this.dropbox().filesListFolder({
 path: path, 
 include_media_info: true
 })
 .then(response => {
 let entries = response.entries;
 this.$store.commit('structure', {
 path: slug,
 data: entries
 });

 return entries;
 })
 .catch(error => {
 this.isLoading = 'error';
 console.log(error);
 });

  }

  return output;
}
```

Dropbox 的`filesListFolder`方法使用传入的`path`变量，而不是之前使用的全局变量。然后，将响应中的条目存储在一个变量中，然后使用相同的 mutation 将其缓存到 Vuex 存储中。然后，`entries`变量从 promise 中返回，将结果存储在`output`中。`catch()`函数与之前相同。

当数据从缓存或 API 返回时，我们可以在组件创建时和路径更新时触发和处理这些数据。然而，在这之前，我们需要处理各种数据类型的混合。

当从 API 返回数据时，数据仍然是一个需要解析的 promise；将其赋值给一个变量只是将 promise 传递给稍后解析。然而，来自存储的数据是一个处理方式完全不同的普通数组。为了给我们一个统一的数据类型来处理，我们将把存储的数组作为 promise 来`resolve`，这意味着`getFolderStructure`无论数据从何处加载，都会返回一个 promise：

```js
getFolderStructure(path) {
  let output;

  const slug = this.generateSlug(path),
      data = this.$store.state.structure[slug];

  if(data) {
    output = Promise.resolve(data);
  } else {

    output = this.dropbox().filesListFolder({
      path: path, 
      include_media_info: true
    })
    .then(response => {
      let entries = response.entries;

      this.$store.commit('structure', {
        path: slug,
        data: entries
      });

      return entries;
    })
    .catch(error => {
      this.isLoading = 'error';
      console.log(error);
    });

  }
  return output;
}
```

有了这个`getFolderStructure`方法，我们现在可以从 API 加载一些数据并将结果存储在全局缓存中，而不更新视图。然而，如果我们希望进一步处理这些信息，该函数会返回这些信息，使用 JavaScript promise。

我们现在可以继续创建我们的下一个方法`displayFolderStructure`，它将使用我们刚刚创建的方法的结果并更新我们的视图，以便应用程序可以再次进行导航。

# 使用`displayFolderStructure`方法显示数据。

现在我们的数据已经准备好被缓存并从存储中提供，我们可以继续使用我们的新方法*display*数据。在`dropbox-viewer`组件中创建一个名为`displayFolderStructure`的新方法：

```js
displayFolderStructure() {

} 
```

这个方法将从之前版本的组件中借用很多代码。请记住，这个方法仅用于显示文件夹，与缓存内容无关。

该方法的过程如下：

1.  将应用程序的加载状态设置为`active`。这让用户知道有事情正在发生。

1.  创建一个空的`structure`对象。

1.  加载`getFolderStructure`方法的内容。

1.  循环遍历结果，并将每个项目添加到`folders`或`files`数组中。

1.  将全局结构对象设置为新创建的对象。

1.  将加载状态设置为`false`，以便可以显示内容。

# 将加载状态设置为 true，并创建一个空的结构对象

该方法的第一步是隐藏结构树并显示加载消息。这可以像以前一样通过将`isLoading`变量设置为`true`来完成。我们还可以在这里创建一个空的`structure`对象，准备好由数据填充：

```js
displayFolderStructure() {
 this.isLoading = true;

 const structure = {
 folders: [],
 files: []
 }
}
```

# 加载`getFolderStructure`方法的内容。

由于`getFolderStructure`方法返回一个 promise，我们需要在继续操作之前解析结果。这可以通过`.then()`函数来完成；我们已经在 Dropbox 类中使用过这个函数。调用该方法，然后将结果赋值给一个变量：

```js
displayFolderStructure() {
  this.isLoading = true;

  const structure = {
    folders: [],
    files: []
  }

 this.getFolderStructure(this.path).then(data => {

 });
}
```

此代码将组件的`path`对象传递给该方法。此路径是用户正在尝试查看的*当前*路径。一旦返回数据，我们可以将其分配给`data`变量，然后在函数内部使用它。

# 循环遍历结果，并将每个项添加到文件夹或文件数组中。

我们已经熟悉了循环遍历条目并检查每个条目的`.tag`属性的代码。如果结果是文件夹，则将其添加到`structure.folders`数组中，否则将其附加到`structure.files`中。

我们只在缓存中存储条目，因此请确保`for`循环更新为直接使用数据，而不是访问条目的属性：

```js
displayFolderStructure() {
  this.isLoading = true;

  const structure = {
    folders: [],
    files: []
  }

  this.getFolderStructure(this.path).then(data => {

    for (let entry of data) {
 // Check ".tag" prop for type
 if(entry['.tag'] == 'folder') {
 structure.folders.push(entry);
 } else {
 structure.files.push(entry);
 }
 }
  });
}
```

# 更新全局结构对象并删除加载状态

此方法的最后一个任务是更新全局结构并删除加载状态。此代码与之前相同：

```js
displayFolderStructure() {
  this.isLoading = true;

  const structure = {
    folders: [],
    files: []
  }

  this.getFolderStructure(this.path).then(data => {

    for (let entry of data) {
      // Check ".tag" prop for type
      if(entry['.tag'] == 'folder') {
        structure.folders.push(entry);
      } else {
        structure.files.push(entry);
      }
    }

    this.structure = structure;
 this.isLoading = false;
  });
}
```

现在我们有了一个将显示数据检索结果的方法。

# 启动该方法

当创建`dropbox-viewer`组件时，可以调用此方法。由于全局 Vue 实例上的`created`函数将 URL 哈希提交到存储中，从而创建了路径变量，因此路径已经被填充。因此，我们不需要向函数传递任何内容。将`created`函数添加到组件中，并在其中调用新方法：

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
    ...
  },

  methods: {

    ...
  },

 created() {
 this.displayFolderStructure();
 }
});
```

现在刷新应用程序将加载文件夹内容。更新 URL 哈希并重新加载页面也将显示该文件夹的内容；但是，单击任何文件夹链接将更新面包屑，但不会更新数据结构。可以通过监视计算的`path`变量来解决此问题。当哈希更新时，它将被更新，因此可以在`watch`对象中触发一个函数。添加一个函数来监视`path`变量的更新，并在更新时触发新方法：

```js
  created() {
    this.displayFolderStructure();
  },

  watch: {
 path() {
 this.displayFolderStructure();
 }
 }
```

通过这样做，我们创建了一个应用程序，再次缓存您访问过的任何文件夹。第一次点击结构时，速度可能会很慢，但是一旦您返回到树的上层并重新进入子文件夹，您几乎看不到加载屏幕。

尽管该应用程序在本章开始时具有相同的功能，但我们已经重构了代码，将检索和缓存以及数据的显示分开。让我们进一步改进我们的应用程序，通过预缓存所选路径的子文件夹。

# 缓存子文件夹

现在，我们可以在不更新 Vue 的情况下缓存文件夹，使用我们的`structure`对象来获取子文件夹的内容。使用`structure`对象中的`folders`数组，我们可以循环遍历并依次缓存每个文件夹。

我们必须确保不会影响应用程序的性能；缓存必须是异步完成的，这样用户就不会意识到这个过程。我们还需要确保不会不必要地运行缓存。

为了实现这一点，我们可以监视`structure`对象。只有在数据从缓存或 API 加载并且 Vue 已更新后，它才会更新。当用户查看文件夹的内容时，我们可以继续循环遍历文件夹以存储它们的内容。

然而，有一个小问题。如果我们监视`structure`变量，我们的代码将永远不会运行，因为对象的直接*内容*不会更新，尽管我们每次都用一个新的对象替换`structure`对象。从一个文件夹到另一个文件夹，结构对象始终具有两个键，即`files`和`folders`，它们都是数组。就 Vue 和 JavaScript 而言，`structure`对象从不改变。

然而，Vue 可以检测到`deep`变量的嵌套更改。这可以在每个变量的基础上启用。类似于组件上的 props，要在 watch 属性上启用更多选项，您需要将其传递给一个对象，而不是直接的函数。

为结构创建一个新的`watch`键，它是一个包含两个值的对象，`deep`和`handler`。`deep`键将被设置为`true`，而`handler`将是在变量更改时触发的函数：

```js
watch: {
  path() {
    this.displayFolderStructure();
  },

  structure: {
 deep: true,
 handler() {

 }
 }
}
```

在这个`handler`中，我们现在可以循环遍历每个文件夹，并为每个文件夹运行`getFolderStructure`方法，使用每个文件夹的`path_lower`属性作为函数参数：

```js
structure: {
  deep: true,
  handler() {
    for (let folder of this.structure.folders) {
 this.getFolderStructure(folder.path_lower);
 }
  }
}
```

通过这段简单的代码，我们的应用程序似乎加快了十倍。您导航到的每个子文件夹都会立即加载（除非您有一个特别长的文件夹列表，并且您快速导航到最后一个文件夹）。为了让您了解缓存的速度和时间，将`console.log()`添加到您的`getFolderStructure`方法中，并打开浏览器开发者工具：

```js
if(data) {
  output = Promise.resolve(data);
} else {

  console.log(`API query for ${path}`);
  output = this.dropbox().filesListFolder({
    path: path, 
    include_media_info: true
  })
  .then(response => {
    console.log(`Response for ${path}`);

    ... 
```

这样您就可以看到所有的 API 调用也是异步完成的 - 应用程序在移动到下一个文件夹之前不会等待前一个文件夹被加载和缓存。这样做的好处是允许较小的文件夹在等待较大的文件夹从 API 返回之前被缓存。

# 替代缓存方法

与任何事物一样，在创建应用程序时，有许多方法可以实现相同的结果。这种方法的缺点是，即使您的文件夹只包含文件，这个函数也会触发，尽管没有任何操作。

另一种方法是再次使用我们的`created`函数，这次在`folder`组件本身上触发，将路径作为参数触发父级方法。

一种方法是使用`$parent`属性。在`folder`组件中，使用`this.$parent`将允许访问`dropbox-viewer`组件上的变量、方法和计算值。

在`folder`组件中添加一个`created`函数，并从 Dropbox 组件中删除`structure`的`watch`属性。然后，调用父级的`getFolderStructure`方法：

```js
Vue.component('folder', {
  template: '<li><strong><a :href="\'#\' + f.path_lower">{{ f.name }}</a></strong></li>',
  props: {
    f: Object
  },
  created() {
 this.$parent.getFolderStructure(this.f.path_lower);
 }
});
```

预览应用程序证明了这种方法的有效性。只有在结构中有文件夹时才触发，这种更清晰的技术将文件夹缓存与文件夹本身绑定在一起，而不是与 Dropbox 代码混在一起。

然而，除非必要，否则应避免使用`this.$parent`，并且只应在极端情况下使用。由于我们有机会使用 props，我们应该这样做。这还给了我们在文件夹上下文中给函数一个更有意义的名称的机会。

导航到 HTML 视图并更新文件夹组件以接受一个新的 prop。我们将称之为 prop cache，并将函数作为值传递。由于属性是动态的，请不要忘记添加一个前导冒号：

```js
<folder :f="entry" :cache="getFolderStructure"></folder>
```

在 JavaScript 的`folder`组件中，在 props 键中添加`cache`关键字。告诉 Vue 输入将是一个函数：

```js
Vue.component('folder', {
  template: '<li><strong><a :href="\'#\' + f.path_lower">{{ f.name }}</a></strong></li>',
  props: {
    f: Object,
    cache: Function
  }
});
```

最后，在`created`函数中调用我们的新`cache()`方法：

```js
Vue.component('folder', {
  template: '<li><strong><a :href="\'#\' + f.path_lower">{{ f.name }}</a></strong></li>',
  props: {
    f: Object,
    cache: Function
  },
 created() {
 this.cache(this.f.path_lower);
 }
});
```

可以通过使用之前的控制台日志来验证缓存。这样可以创建更清晰的代码，更容易阅读，也更容易让其他开发人员阅读。

现在我们的 Dropbox 应用程序正在进展，我们可以继续缓存父文件夹，如果您使用 URL 中的哈希进入子文件夹。

# 缓存父文件夹

缓存父级结构是我们可以采取的下一个预防措施，以帮助加快应用程序的速度。假设我们已经导航到了我们的图像目录`img/holiday/summer`，并希望与朋友或同事共享。我们会将带有此 URL 的 URL 哈希发送给他们，在页面加载时，他们将看到内容。如果他们然后使用面包屑导航到`img/holiday`，例如，他们需要等待应用程序检索内容。

使用`breadcrumb`组件，我们可以缓存父目录，因此在导航到`holiday`文件夹时，用户将立即看到其内容。当用户浏览此文件夹时，所有子文件夹都会使用先前的方法进行缓存。

为了缓存父文件夹，我们已经有一个组件显示具有访问所有父文件夹的 slug 的路径，我们可以通过面包屑循环遍历。

在开始缓存过程之前，我们需要更新组件中的`folders`计算函数。因为目前我们存储的路径是带有散列前缀的，这会导致 Dropbox API 无效的路径。从被推送到输出数组的对象中删除散列，并在模板中以类似的方式添加它，就像`folder`组件一样：

```js
Vue.component('breadcrumb', {
  template: '<div>' +
    '<span v-for="(f, i) in folders">' +
      '<a :href="\'#\' + f.path">{{ f.name || 'Home' }}</a>' +
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
        output.push({'name': item || 'home', 'path': slug});
        slug += '/';
      }

      return output;
    }
  }
});
```

现在我们可以使用输出来显示面包屑并缓存父级结构。

第一步是让`breadcrumb`组件可以访问缓存函数。类似于`folder`组件，将函数作为一个 prop 添加到你的视图中的`breadcrumb`组件中：

```js
<breadcrumb :cache="getFolderStructure"></breadcrumb>
```

在 JavaScript 代码中为组件添加`props`对象。将`cache`属性声明为一个函数，这样 Vue 就知道要期望什么：

```js
Vue.component('breadcrumb', {
  template: '...',
 props: {
 cache: Function
 },
  computed: {
    folders() {
      ...
  }
});
```

父级结构将在`breadcrumb`组件创建时生成。然而，由于我们不希望这个过程阻塞加载过程，所以我们将在组件被`mounted`而不是`created`时触发它。

在组件中添加一个`mounted`函数，并将文件夹的计算值赋给一个变量：

```js
Vue.component('breadcrumb', {
  template: '...',
  props: {
    cache: Function
  },
  computed: {
    folders() {
      ...
    }
  },
  mounted() {
 let parents = this.folders;
 }
});
```

现在我们需要开始缓存文件夹；然而，我们可以在执行缓存的顺序上做得更聪明。我们可以假设用户通常会返回到文件夹树的上一级，所以我们应该在移动到其父级之前理想情况下先缓存直接父级，依此类推。由于我们的文件夹变量是从上到下的，所以我们需要将其反转。

我们可以做的另一件事是删除当前文件夹；因为我们已经在其中，应用程序已经缓存了它。在组件中，反转数组并删除第一个项目：

```js
mounted() {
  let parents = this.folders;
  parents.reverse().shift();
}
```

如果我们在父变量的函数中添加一个控制台日志，我们可以看到它包含了我们现在希望缓存的文件夹。现在，我们可以循环遍历这个数组，为数组中的每个项目调用`cache`函数：

```js
mounted() {
  let parents = this.folders;
  parents.reverse().shift();

  for(let parent of parents) {
 this.cache(parent.path);
 }
}
```

有了这个，我们的父文件夹和子文件夹都被应用程序缓存，使得导航树的上下移动非常快速。然而，在`mounted`函数内部运行`console.log()`会发现，每次导航到一个文件夹时，面包屑都会重新挂载。这是因为视图中的`v-if`语句会每次删除和添加 HTML。

由于我们只需要在初始加载应用程序时缓存父文件夹一次，让我们考虑更改触发缓存的位置。我们只需要在第一次运行这个函数；一旦用户开始在树上向上和向下导航，沿途访问的所有文件夹都将被缓存。

# 缓存父文件夹一次

为了确保我们使用的资源最少，我们可以将面包屑使用的文件夹数组保存在存储中。这意味着`breadcrumb`组件和我们的父级缓存函数可以访问同一个数组。

在存储状态中添加一个`breadcrumb`键，这是我们将存储数组的地方：

```js
const store = new Vuex.Store({
  state: {
    path: '',
    structure: {},
    breadcrumb: []
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
```

接下来，将`breadcrumb`组件中的代码移到`updateHash`mutation 中，以便我们可以更新`path`和`breadcrumb`变量：

```js
updateHash(state) {
  let hash = window.location.hash.substring(1);
  state.path = (hash || '');

 let output = [],
 slug = '',
 parts = state.path.split('/');

 for (let item of parts) {
 slug += item;
 output.push({'name': item || 'home', 'path': slug});
 slug += '/';
 }

 state.breadcrumb = output;
},
```

请注意，不再返回`output`数组，而是将其存储在`state`对象中。现在，我们可以更新`breadcrumb`组件上的文件夹计算函数，以返回存储的数据：

```js
computed: {
  folders() {
 return this.$store.state.breadcrumb;
 }
}
```

现在，我们可以在`dropbox-viewer`组件上创建一个新的方法`cacheParentFolders`，触发我们为`breadcrumb`组件编写的代码。

在`Dropbox`组件上创建一个新的方法，并将代码移到其中。更新父级的位置，并确保触发正确的路径：

```js
cacheParentFolders() {
  let parents = this.$store.state.breadcrumb;
  parents.reverse().shift();

  for(let parent of parents) {
    this.getFolderStructure(parent.path);
  }
}
```

现在，当创建`Dropbox`组件时，我们可以在`created`函数中调用这个方法一次。在现有的方法调用之后添加它：

```js
created() {
  this.displayFolderStructure();
  this.cacheParentFolders();
}
```

现在，我们可以进行一些清理工作，从`breadcrumb`组件中删除`mounted`方法，以及从视图中删除`props`对象和`:cache`属性。这意味着我们的`breadcrumb`组件现在比以前更简单：

```js
Vue.component('breadcrumb', {
  template: '<div>' +
    '<span v-for="(f, i) in folders">' +
      '<a :href="\'#\' + f.path">{{ f.name || 'Home' }}</a>' +
      '<i v-if="i !== (folders.length - 1)"> &raquo; </i>' +
    '</span>' + 
  '</div>',
  computed: {
    folders() {
      return this.$store.state.breadcrumb;
    }
  }
});
```

HTML 恢复到原来的状态：

```js
<breadcrumb></breadcrumb>
```

我们还可以将存储中的`updateHash`变异变得更整洁和更易理解：

```js
updateHash(state, val) {
  let path = (window.location.hash.substring(1) || ''),
    breadcrumb = [],
    slug = '',
    parts = path.split('/');

  for (let item of parts) {
    slug += item;
    breadcrumb.push({'name': item || 'home', 'path': slug});
    slug += '/';
  }

  state.path = path
  state.breadcrumb = breadcrumb;
}
```

现在所有的变量都在顶部声明，`state`在底部更新。变量的数量也减少了。

现在查看应用程序，它似乎工作正常；然而，仔细检查后，`breadcrumb`在初始页面加载时似乎有点滞后。一旦导航到一个文件夹，它就会追上来，但在第一次加载时，它似乎少了一个项目，而在查看 Dropbox 的根目录时则没有任何项目。

这是因为在我们提交`updateHash`变异之前，存储尚未完全初始化。如果我们回忆一下在第四章中介绍的 Vue 实例生命周期，我们可以看到 created 函数非常早就被触发了。将主 Vue 实例更新为在`mounted`上触发变异可以解决这个问题：

```js
const app = new Vue({
  el: '#app',

  store,
  mounted() {
    store.commit('updateHash');
  }
});
```

所有文件夹都已经被缓存，现在我们可以通过存储每个文件的下载链接来继续缓存更多的 API 调用。

我们还可以尝试缓存子文件夹的子文件夹，通过循环遍历每个缓存文件夹的内容，最终缓存整个树。我们不会详细介绍这个，但可以自己尝试一下。

# 缓存文件的下载链接

当用户在文档树中导航时，Dropbox API 仍然被查询了多次。这是因为每次显示一个文件时，我们都会查询 API 以检索下载链接。通过将下载链接响应存储在缓存中，并在导航回到它所在的文件夹时重新显示，可以减少额外的 API 查询。

每次显示一个文件时，都会使用存储中的数据初始化一个新的组件。我们可以利用这一点，因为这意味着我们只需要更新组件实例，然后结果就会被缓存。

在您的文件组件中，更新 API 响应，不仅将结果保存在数据属性的`link`属性上，还保存在文件实例`f`上。这将作为一个新的键`download_link`存储。

在存储数据时，我们可以将两个单独的命令合并为一个命令，使用两个等号：

```js
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
    this.d.filesGetTemporaryLink({path: this.f.path_lower})
      .then(data => {
        this.f.download_link = this.link = data.link;
      });
  }
});
```

这基本上意味着`this.f.download_link`等于`this.link`，它也等于`data.link`，即来自 API 的下载链接。通过在导航到文件夹时存储和显示此信息，我们可以添加一个`if`语句来检查数据是否存在，如果不存在，则查询 API 获取它。

```js
created() {
  if(this.f.download_link) {
 this.link = this.f.download_link;
 } else {
    this.d.filesGetTemporaryLink({path: this.f.path_lower})
      .then(data => {
        this.f.download_link = this.link = data.link;
      });
  }
}
```

在文件创建时这样做可以避免不必要地查询 API。如果我们在缓存文件夹时获取了这些信息，可能会减慢应用程序的速度并存储非必要的信息。想象一下一个包含数百张照片的文件夹 - 我们不希望为每个照片都查询 API，只是为了用户可能进入该文件夹。

这意味着我们应用程序中的所有内容只需要查询 API 一次以获取信息。用户可以随意在文件夹结构中上下导航，随着操作的进行，应用程序只会变得更快。

# 完整的代码 - 带有附加的文档

完成我们的应用程序后，我们现在可以添加一些非常需要的文档。文档化代码总是很好的，因为它给出了代码的原因和解释。良好的文档不仅应该说明代码做什么，还应该说明为什么这样做，允许什么，不允许什么。

一种常用的文档方法是 JavaScript DocBlock 标准。这套约定规定了一些样式指南，供您在文档化代码时遵循。DocBlock 格式化为注释块，并以`@`开头的关键字为特色，例如`@author`，`@example`，或者使用`@param`关键字列出函数可以接受的参数。一个例子是：

```js
/**
 * Displays a folder with a link and cache its contents
 * @example <folder :f="entry" :cache="getFolderStructure"></folder>
 *
 * @param {object} f The folder entry from the tree
 * @param {function} cache The getFolderStructure method from the dropbox-viewer component
 */
```

首先是一个描述，DocBlock 有几个关键字可以帮助布置文档。我们将通过添加文档来完成我们的 Dropbox 应用程序。

让我们首先看一下`breadcrumb`组件：

```js
/**
 * Displays the folder tree breadcrumb
 * @example <breadcrumb></breadcrumb>
 */
Vue.component('breadcrumb', {
  template: '<div>' +
    '<span v-for="(f, i) in folders">' +
      '<a :href="\'#\' + f.path">{{ f.name || 'Home' }}</a>' +
      '<i v-if="i !== (folders.length - 1)"> &raquo; </i>' +
    '</span>' + 
  '</div>',

  computed: {
    folders() {
      return this.$store.state.breadcrumb;
    }
  }
});
```

继续看`folder`组件：

```js
/**
 * Displays a folder with a link and cache its contents
 * @example <folder :f="entry" :cache="getFolderStructure"></folder>
 *
 * @param {object} f The folder entry from the tree
 * @param {function} cache The getFolderStructure method from the dropbox-viewer component
 */
Vue.component('folder', {
  template: '<li><strong><a :href="\'#\' + f.path_lower">{{ f.name }}</a></strong></li>',
  props: {
    f: Object,
    cache: Function
  },
  created() {
    // Cache the contents of the folder
    this.cache(this.f.path_lower);
  }
});
```

接下来，我们看到`file`组件：

```js
/**
 * File component display size of file and download link
 * @example <file :d="dropbox()" :f="entry"></file>
 * 
 * @param {object} f The file entry from the tree
 * @param {object} d The dropbox instance from the parent component
 */
Vue.component('file', {
  template: '<li><strong>{{ f.name }}</strong><span v-if="f.size"> - {{ bytesToSize(f.size) }}</span> - <a v-if="link" :href="link">Download</a></li>',
  props: {
    f: Object,
    d: Object
  },

  data() {
    return {
      // List of file size
      byteSizes: ['Bytes', 'KB', 'MB', 'GB', 'TB'],

      // The download link
      link: false
    }
  },

  methods: {
    /**
     * Convert an integer to a human readable file size
     * @param {integer} bytes
     * @return {string}
     */
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
    // If the download link has be retrieved from the API, use it
    // if not, aquery the API
    if(this.f.download_link) {
      this.link = this.f.download_link;
    } else {
      this.d.filesGetTemporaryLink({path: this.f.path_lower})
        .then(data => {
          this.f.download_link = this.link = data.link;
        });
    }
  }
});
```

现在我们来看一下`dropbox-viewer`组件：

```js
/**
 * The dropbox component
 * @example <dropbox-viewer></dropbox-viewer>
 */
Vue.component('dropbox-viewer', {
  template: '#dropbox-viewer-template',

  data() {
    return {
      // Dropbox API token
      accessToken: 'XXXX',

      // Current folder structure
      structure: {},
      isLoading: true
    }
  },

  computed: {
    // The current folder path
    path() {
      return this.$store.state.path
    }
  },

  methods: {

    /**
     * Dropbox API instance
     * @return {object}
     */
    dropbox() {
      return new Dropbox({
        accessToken: this.accessToken
      });
    },

    /**
     * @param {string} path The path to a folder
     * @return {string} A cache-friendly URL without punctuation/symbals
     */
    generateSlug(path) {
      return path.toLowerCase()
        .replace(/^\/|\/$/g, '')
        .replace(/ /g,'-')
        .replace(/\//g,'-')
        .replace(/[-]+/g, '-')
        .replace(/[^\w-]+/g,'');
    },

    /**
     * Retrieve the folder structure form the cache or Dropbox API
     * @param {string} path The folder path
     * @return {Promise} A promise containing the folder data
     */
    getFolderStructure(path) {
      let output;

      const slug = this.generateSlug(path),
          data = this.$store.state.structure[slug];

      if(data) {
        output = Promise.resolve(data);
      } else {
        output = this.dropbox().filesListFolder({
          path: path, 
          include_media_info: true
        })
        .then(response => {
          let entries = response.entries;

          this.$store.commit('structure', {
            path: slug,
            data: entries
          });

          return entries;
        })
        .catch(error => {
          this.isLoading = 'error';
          console.log(error);
        });

      }
      return output;
    },

    /**
     * Display the contents of getFolderStructure
     * Updates the output to display the folders and folders
     */
    displayFolderStructure() {
      // Set the app to loading
      this.isLoading = true;

      // Create an empty object
      const structure = {
        folders: [],
        files: []
      }

      // Get the structure
      this.getFolderStructure(this.path).then(data => {

        for (let entry of data) {
          // Check ".tag" prop for type
          if(entry['.tag'] == 'folder') {
            structure.folders.push(entry);
          } else {
            structure.files.push(entry);
          }
        }

        // Update the data object
        this.structure = structure;
        this.isLoading = false;
      });
    },

    /**
     * Loop through the breadcrumb and cache parent folders
     */
    cacheParentFolders() {
      let parents = this.$store.state.breadcrumb;
      parents.reverse().shift();

      for(let parent of parents) {
        this.getFolderStructure(parent.path);
      }
    }
  },

  created() {
    // Display the current path & cache parent folders
    this.displayFolderStructure();
    this.cacheParentFolders();
  },

  watch: {
    // Update the view when the path gets updated
    path() {
      this.displayFolderStructure();
    }
  }
});
```

我们还要检查一下 Vuex 存储：

```js
/**
 * The Vuex Store
 */
const store = new Vuex.Store({
  state: {
    // Current folder path
    path: '',

    // The current breadcrumb
    breadcrumb: [],

    // The cached folder contents
    structure: {},
  },
  mutations: {
    /**
     * Update the path & breadcrumb components
     * @param {object} state The state object of the store
     */
    updateHash(state) {

      let path = (window.location.hash.substring(1) || ''),
        breadcrumb = [],
        slug = '',
        parts = path.split('/');

      for (let item of parts) {
        slug += item;
        breadcrumb.push({'name': item || 'home', 'path': slug});
        slug += '/';
      }

      state.path = path
      state.breadcrumb = breadcrumb;
    },

    /**
     * Cache a folder structure
     * @param {object} state The state objet of the store
     * @param {object} payload An object containing the slug and data to store
     */
    structure(state, payload) {
      state.structure[payload.path] = payload.data;
    }
  }
});
```

我们进一步转到 Vue 应用程序*：*

```js
/**
 * The Vue app
 */
const app = new Vue({
  el: '#app',

  // Initialize the store
  store,

  // Update the current path on page load
  mounted() {
    store.commit('updateHash');
  }
});
```

最后，我们通过`window.onhashchange`函数：

```js
/**
 * Update the path & store when the URL hash changes
 */
window.onhashchange = () => {
  app.$store.commit('updateHash');
}
```

最后，视图中的 HTML 如下所示：

```js
<div id="app">
  <dropbox-viewer></dropbox-viewer>
</div>
```

Dropbox 查看器的模板如下所示：

```js
<script type="text/x-template" id="dropbox-viewer-template">
  <div>
    <h1>Dropbox</h1>

    <transition name="fade">
      <div v-if="isLoading">
        <div v-if="isLoading == 'error'">
          <p>There seems to be an issue with the URL entered.</p>
          <p><a href="">Go home</a></p>
        </div>
        <div v-else>
          Loading...
        </div>
      </div>
    </transition>

    <transition name="fade">
      <div v-if="!isLoading">
        <breadcrumb></breadcrumb>
        <ul>
          <template v-for="entry in structure.folders">
            <folder :f="entry" :cache="getFolderStructure"></folder>
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

您会注意到并非所有内容都已记录。一个简单的函数或变量赋值不需要重新解释它的作用，但是对主要变量的注释将帮助任何查看它的人。

# 总结

在本书的这一部分，我们涵盖了很多内容！我们从查询 Dropbox API 以获取文件和文件夹列表开始。然后我们继续添加导航功能，允许用户点击文件夹并下载文件。接下来，我们介绍了 Vuex 和 store 到我们的应用程序中，这意味着我们可以集中路径、面包屑，最重要的是，缓存文件夹内容。最后，我们看了一下缓存子文件夹和文件下载链接。

在本书的下一部分，我们将看看如何创建一个商店。这将包括使用一个名为 Vue router 的新 Vue 插件浏览类别和产品页面。我们还将研究如何将产品添加到购物篮中，并将产品列表和偏好存储在 Vuex store 中。
