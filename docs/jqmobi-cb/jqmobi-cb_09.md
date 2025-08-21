# 第九章：方法和实用工具

本章将介绍以下食谱：

+   使用`loadPage()`加载页面

+   使用`changePage()`来更改页面

+   使用`jqmData()`和`jqmRemoveData()`

+   使用`jqmEnhanceable()`

+   使用`jqmHijackable()`

+   使用`$.mobile.base`

+   解析 URL

+   使用`$.mobile.path` 实用方法

+   使用静默滚动

# 介绍

jQuery Mobile 框架提供了许多在`$.mobile`对象上工作的方法和实用工具。 本章向您展示如何使用这些方法和实用工具。

本章所有食谱的源文件都在存档的`code/09`文件夹下。 每个食谱都在自己的子文件夹中列出，其名称相应命名。

# 使用 loadPage()加载页面

使用`$.mobile.loadPage()`方法，您可以将外部页面在后台加载到 DOM 中并增强其内容，而不影响当前页面。 本食谱向您展示如何执行此操作。

## 准备工作

从`code/09/loadpage`源文件夹中复制此食谱的完整代码。 您可以通过 URL:`http://localhost:8080/09/loadpage/main.html`来启动此代码。

## 如何进行...

1.  创建`main.html`，带有页面`id="main"`，并添加一个空的`div`标签和一个指向`#page1`的链接，如下所示：

    ```js
    <div data-role="content">
      <div id="msgdiv"></div>
      <a href="#page1" data-role="button">Show Page 1</a>
    </div>
    ```

1.  为`#main`的`pagebeforeshow`事件添加事件处理程序，并使用`loadPage()`方法加载`#page1`：

    ```js
    $("#main").live("pagebeforeshow", function(event, data) {
      $("#msgdiv").html("<p>Current Active Page : " 
        + $.mobile.activePage.attr("data-url") + "</p>");
     $.mobile.loadPage( "page1.html", {role: "dialog"});
    });
    ```

1.  为`#page1`的`pagebeforeshow`事件添加事件处理程序，以更新显示的消息：

    ```js
    $("#page1").live("pagebeforeshow", function(event, data) {
      $("#page1content").html("<p>Current Active Page : " 
     + $.mobile.activePage.attr("data-url") + "</p>");
    });
    ```

1.  最后，创建`page1.html`，如下所示：

    ```js
    <div id="page1" data-role="page" data-theme="e">
      <div data-role="header">
        <h1>Header of Page 1</h1>
      </div>
      <div id="page1content" data-role="content"></div>
    </div>
    ```

## 它是如何工作的...

创建具有`#main`页面的`main.html`，并添加一个空的`div`，其中`id="msgdiv"`和一个打开其中`#page1`链接的链接。 在`pageinit`期间，`#page1`引用尚不可用，因为它来自外部的`page1.html`文件。 在`#main`页面上添加`pagebeforeshow`事件处理程序。 在这里，使用`$.mobile.activePage()`方法获取当前活动页面，并使用 jQuery 的`attr()`方法将其`data-url`属性显示在`#msgdiv`中。 接下来，使用`$.mobile.loadPage()`调用加载`page1.html`。 同时，设置`loadPage()`选项，并将`role`属性设置为`dialog`。 页面现在在后台加载。

为`#page1`的`pagebeforeshow`事件添加事件处理程序。 获取之前完成的活动页面的`data-url`并在`#page1content` div 容器中显示它。 最后，创建一个带有`id="page1content"`的空 div 的`page1.html`页面。

当`main.html`加载时，您将看到**显示页面 1**按钮。 点击它，`page1.html`将以默认弹出过渡方式显示为对话框。 另外，活动页面的数据 URL 将在这两个页面中正确显示。

## 还有更多...

`$.mobile.loadPage()`返回一个延迟的`promise`对象，一旦页面被增强并加载到 DOM 中，它就会自动解决。

### loadPage()选项

`loadPage()`方法将一个可选的`options`对象作为第二个参数。 可在`options`对象上设置以下属性：

+   `data`：这是 Ajax 页面请求的数据

+   `loadMsgDelay`（默认为 50 秒）：这是显示页面加载消息之前的延迟时间

+   `pageContainer`：这是包含加载页面的元素

+   `reloadPage`（默认为 `false`）：这将强制重新加载页面

+   `role`：这是页面加载的 `data-role` 值

+   `showLoadMsg`（默认为 `false`）：这决定是否显示页面加载消息

+   `type`（默认为 `get`）：这指定了 Ajax 请求的类型（`get` 或 `post`）

## 另请参阅

+   *使用 changePage() 更改页面* 小节

+   在第八章的 *Events* 中的 *使用页面加载和删除事件* 小节

# 使用 changePage() 更改页面

此小节向您展示如何使用 `$.mobile.changePage()` 方法使用 JavaScript 从一个页面切换到另一个页面。此小节扩展了第六章的 *使用分割按钮列表* 小节，*列表视图*，并在新页面中显示所选列表项中的图像。

## 准备工作

从 `code/09/changepage` 源文件夹中复制此小节的完整代码。还要重新查看第六章的 *使用分割按钮列表* 小节。您可以通过以下 URL 启动此代码：`http://localhost:8080/09/changepage/main.html`。

## 如何做...

1.  创建 `main.html`，其中包含一个分割按钮列表，`<img>` 标签的 `href` 属性具有一个 `file` 参数，在左按钮中有图像文件的路径，如下所示：

    ```js
    <div data-role="content">
      <ul data-role="listview" data-inset="true" 
        data-theme="b" data-split-theme="e" 
        data-split-icon="arrow-d">
        <li>
     <a href="#viewphoto&file=img1.png">
            <img style="margin: 10px" 
              src="img/img1.png" />
              <h3>Lal Bagh</h3>
          </a>
          <a href='#' data-rel='dialog'>Download</a>
        </li>
        <li>
     <a href="#viewphoto&file=img2.png">
            <img style="margin: 10px" 
              src="img/img2.png" />
              <h3>Peacock</h3>
          </a>
          <a href='#' data-rel='dialog'>Download</a>
        </li>
        <li>
     <a href="#viewphoto&file=img3.png">
            <img style="margin: 10px"  
              src="img/img3.png"
              height=75% />
              <h3>Ganesha</h3>
          </a>
          <a href='#' data-rel='dialog'>Download</a>
        </li>
      </ul>
    </div>
    ```

1.  添加 `#viewphoto` 页面，并在点击分割按钮的左部时打开它：

    ```js
    <div id="viewphoto" data-role="page" data-theme="e" data-add-back-btn="true">
    ….....
      <div data-role="content">
        <div id="imgid">
        <p>Displaying Image ...</p>
        </div>
      </div>
    </div>
    ```

1.  在 `<head>` 部分添加以下脚本，并在 `pagebeforechange` 事件处理程序中调用 `$.mobile.changePage()`：

    ```js
    $(document).live( "pagebeforechange", function( e, data ) {
      if ( typeof data.toPage === "string" ) {
        var u = $.mobile.path.parseUrl( data.toPage );
        var re = /^#viewphoto&file/;
        if ( u.hash.search(re) !== -1 ) {
     $.mobile.changePage("main.html#viewphoto",
          {
            transition: "pop",
            dataUrl: u.hash.split("=")[1],
            type: "get"
          });
     e.preventDefault();
        }
      }
    });
    ```

1.  在 `#viewphoto` 页面的 `pagebeforeshow` 事件处理程序中显示图像：

    ```js
    $("#viewphoto").live( "pagebeforeshow", function( e, data ) {
      var u = $.mobile.path.parseUrl( document.location.href );
      var re = /^#img/;
      if ( u.hash.search(re) !== -1 ) {
        var str="<img src='../../images/" + u.hash.substr(1) + "' />";
        $("#imgid").html(str).trigger("refresh");
      }
    });
    ```

## 它是如何工作的...

在 `main.html` 中添加分割按钮列表和 `#viewphoto` 页面，如代码所示。在 `#viewphoto` 页面的 `div` 标签中添加一个空的 `#imgid` 属性，以显示完整的图像。分割按钮列表和 `#viewphoto` 页面的代码已在第六章中解释过。右按钮的 `href` 属性只是指向 `#`，因为在此小节中未使用。将左按钮中列表项的 `href` 属性更改为包括文件参数；例如，`href="#viewphoto&file=img1.png"`。启动应用程序时，将显示如下屏幕，其中显示了缩略图，如分割按钮列表所示。

![它是如何工作的...](img/7225_09_01.jpg)

但是，当你点击分割列表按钮时，没有任何反应，因为框架不理解带有 `href` 属性的文件参数。要打开并显示图像，您将需要手动处理页面更改。要手动调用 `pageChange()`，请为 `pagebeforechange` 事件添加事件处理程序。在这里，检查目标页面（`data.toPage`）是否是 URL 字符串，并使用 `$.mobile.path.parseUrl()` 方法获取 URL 组件。现在，使用正则表达式搜索 URL 哈希中的文件参数 — `#viewphoto&file`。如果找到，则是查看图像的请求。现在，你必须处理页面更改。

调用 `pageChange()` 方法并传递 `main.html#viewphoto` URL。另外，用自定义值设置 `options` 参数的`transition`、`type` 和 `dataUrl`。你可以按照示例将文件名信息存储在 `dataUrl` 中，通过分割 URL 哈希来实现。最后，防止默认的 `pagebeforechange` 事件处理，因为你已经在这里处理页面更改。

接下来，您将需要查询提供给 `pageChange()` 的 URL 字符串，以获取 `file` 参数，并显示图像。要做到这一点，请为 `#viewphoto` 页面的 `pagebeforeshow` 事件添加事件处理程序。使用 `$.mobile.path.parseUrl()` 方法获取 URL 组件。搜索 `img` 表达式；如果找到，从 URL 哈希中获取文件名，并在代码中显示在 `#imgid` div 容器中。现在，如果你点击任何列表项，相应的图像将在 `#viewphoto` 页面中以较大尺寸显示，如下面的截图所示：

![工作原理...](img/7225_09_02.jpg)

## 还有更多...

`$.mobile.changePage()` 方法在页面更改期间内部使用 `$.mobile.loadPage()` 方法来获取新页面。

### changePage() 选项

`changePage()` 方法接受可选的 `options` 对象作为第二个参数。可以在 `options` 对象上设置以下属性：

+   `allowSamePageTransition`（默认为`false`）：默认情况下忽略对当前活动页面的转换，但可以通过使用 `allowSamePageTransition` 选项启用

+   `changeHash`（默认为`true`）：这会更新位置栏中的哈希

+   `data`：这是 Ajax 页面请求的数据

+   `dataUrl`：这是页面更改后更新浏览器位置的 URL

+   `pageContainer`：这是包含加载的页面的元素

+   `reloadPage`（默认为`false`）：这会强制重新加载页面

+   `reverse`（默认为`false`）：这是页面显示过渡方向

+   `role`：为页面显示提供 `data-role` 值

+   `showLoadMsg`（默认为`false`）：决定是否显示页面加载消息

+   `transition`：这是用于页面更改的过渡效果

+   `type`（默认为`get`）：指定 Ajax 请求的类型（`get` 或 `post`）

## 另请参见

+   使用 `loadPage()` 加载页面 和 解析 URL 的示例

+   第六章 *列表视图*下的*使用分隔按钮列表*示例

+   第八章 *事件*下的*使用页面加载和移除事件*示例

# 使用 jqmData()和 jqmRemoveData()

`jqmData()`和`jqmRemoveData()`方法可用于向 jQuery 移动应用程序的元素添加或移除数据属性。它们会自动处理自定义命名空间。本节介绍了如何使用这些方法。

## 准备就绪

从`code/09/jqmdata`源文件夹复制此示例的完整代码。你可以通过 URL 访问此代码：`http://localhost:8080/09/jqmdata/main.html`。

## 如何实现...

1.  在包含`jquery.``mobile.js`之前，将以下脚本添加到`main.html`中：

    ```js
    $(document).bind("mobileinit", function() {
     $.mobile.ns = "my-";
    });
    ```

1.  在页面中添加两个文本输入和一个按钮，如下所示：

    ```js
    <div data-my-role="content">
      <div data-role="fieldcontain">
        <label for="pgtheme">Page Theme : </label>
        <input type="text" id="pgtheme" />
      </div>
      <div data-role="fieldcontain">
        <label for="customdata">Custom Data : </label>
        <input type="text" id="customdata" />
      </div>
      <button id="clearbtn">Clear Custom Data</button>
    </div>
    ```

1.  将以下脚本添加到`<head>`部分以调用`jqmData()`和`jqmRemoveData(``)`方法：

    ```js
    $("#main").live("pageinit", function(event) {
     var pg = $("div:jqmData(role='page')");
     pg.jqmData("custom", "Custom data text");
       $("#pgtheme").attr("value", pg.jqmData("theme"));
       $("#customdata").attr("value", pg.jqmData("custom"));
       $("#clearbtn").bind("click", function(event, ui) {
     pg.jqmRemoveData("custom");
     $("#customdata").attr("value", 
     ""+pg.jqmData("custom")); 
       });
    });
    ```

## 工作原理...

在`main.html`中，在包含对`jquery.mobile.js`的引用之前，为`mobileinit`事件添加事件处理程序。这会在应用程序启动时调用。在这里，设置`$.mobile.ns="my-"`命名空间配置。

添加两个文本输入，分别为`id="pgtheme"`和`id="customdata"`，用于显示页面主题和自定义数据。添加一个`id="clearbtn"`的按钮。接下来，将`pageinit`事件绑定到回调函数。在此函数中，使用`div:jqmData(role='page')`自定义选择器获取`page`元素。使用`jqmData()`确保自动处理带有自定义命名空间的数据属性（`data-my-role`）的查找。

使用`jqmData()`方法在页面上设置值为**自定义数据文本**的**自定义数据**属性，如下截图所示。最后，在两个文本输入框中显示**页面主题**和**自定义数据**属性。页面显示如下：

![工作原理...](img/7225_09_03.jpg)

接下来，为`#clearbtn`添加一个`click`事件处理程序，使用`jqmRemoveData()`方法移除页面上设置的自定义数据属性，并更新**自定义数据**文本字段的值。现在，当你点击**清除自定义数据**按钮时，文本框将显示**未定义**。

## 更多信息...

jQuery 方法`data()`、`hasData()`和`removeData()`不考虑`data-`属性的命名空间。你将需要编写自定义代码来处理它。相反，使用本节中所示的`jqmData()`和`jqmRemoveData()`方法。你可以使用 DOM 检查器来检查代码，以验证对自定义命名空间的使用。

## 另请参阅

+   第七章 *配置*下的*配置默认命名空间*示例

# 使用 jqmEnhanceable()

当在父元素上设置了`data-enhance="false"`时，它会被所有子元素继承。为了搜索可以使用手动增强或用于自定义插件编写的元素，jQuery Mobile 框架提供了一个名为`jqmEnhanceable()`的过滤方法。本配方向您展示如何使用它。

## 准备工作

从`code/09/jqmenhance`源文件夹复制此配方的完整代码。你可以使用 URL `http://localhost:8080/09/jqmenhance/main.html`来启动此代码。

## 怎么做...

1.  在包含`j``query.mobile.js`之前，将以下脚本添加到`main.html`中：

    ```js
    $(document).bind("mobileinit", function() {
     $.mobile.ignoreContentEnabled = true; 
    });
    ```

1.  如所示，在页面中添加两个锚按钮。第二个按钮位于一个具有`data-enhance="false"`的`div`标签内。

    ```js
    <div data-role="content">
      <div>
        <a href="#">Link 1</a>
      </div>
     <div data-enhance="false">
        <a href="#">Link 2</a>
      </div>
    </div>
    ```

1.  在`<head>`部分添加以下脚本来调用`jqmEnha``nceable()`方法：

    ```js
    $("#main").live("pagecreate", function(event) {
     $("a").jqmEnhanceable().attr("data-role", "button");
    });
    ```

## 它的工作原理...

在`main.html`中，在包含对`jquery.mobile.js`的引用之前为`mobileinit`事件添加一个事件处理程序，该事件在应用程序启动时被调用。设置`$.mobile.ignoreContentEnabled=true`配置。

在`#main`的内容中添加两个`div`标签。将一个`#`链接添加到这两个`div`标签中。不要在任何一个链接上设置`data-role="button"`属性。第二个`div`标签设置了`data-enhance="false"`属性。接下来，将`pagecreate`事件绑定到事件处理程序上。此时，页面已经被`initialized`，但是小部件还没有被增强。现在按照所示在锚元素上调用`jqmEnhanceable()`方法。此方法会过滤并提供仅仅不从父级继承`data-enhance="false"`的那些锚元素。所以在代码中，`Link 1`被提供了。使用 jQuery 的`attr()`调用将其`data-role`属性设置为`button`，如代码所示。

现在，当你打开应用程序时，只有**Link 1**被增强为按钮，而**Link 2**没有被增强，如下截图所示：

![它的工作原理...](img/7225_09_04.jpg)

## 更多内容...

当设置了`$.mobile.ignoreContentEnabled=true`配置时，`jqmEnhanceable()`方法才能工作。将访问每个元素的父节点并检查`data-enhance`值，任何设置为`false`的父节点都将被从过滤集中移除。

### 注意

即使对一小组元素使用`jqmEnhanceable()`也很耗费资源，因为所有父元素都会被检查`data-enhance`的值。

## 参见

+   *使用 jqmHijackable()* 配方

# 使用 jqmHijackable

当在父元素上设置`data-ajax="false"`时，这会被所有子元素继承。有一个名为`jqmHijackable()`的过滤方法可用于搜索可以使用自定义表单和链接绑定的子元素。本配方向您展示如何使用此方法。

## 准备工作

从`code/09/jqmhijack`源文件夹复制此配方的完整代码。你可以使用 URL: `http://localhost:8080/09/jqmhijack/main.html`来启动此代码。

## 怎么做...

1.  在包含`jquery.mobile.js`之前，将以下脚本添加到`main.html`中：

    ```js
    $(document).bind("mobileinit", function() {
      $.mobile.ignoreContentEnabled = true;   
    });
    ```

1.  向页面添加两个锚按钮，如下所示。第二个按钮位于具有`data-ajax="false"`属性的`div`标签内：

    ```js
    <div data-role="content">
      <div>
        <a href="page1.html" data-role="button">Link 1</a>
      </div>
     <div data-ajax="false">
        <a href="page1.html" data-role="button">Link 2</a>
      </div>
    </div>
    ```

1.  将以下脚本添加到`<head>`部分以调用`jqmHijackable()`方法：

    ```js
    $("#main").live("pageinit", function(event) {
     $("a").jqmHijackable().each(function() {
        $(this).attr("data-transition", "flip");
      });
    });
    ```

1.  最后，按下面的代码片段创建`page1.html`：

    ```js
    <div id="page1" data-role="page" data-theme="e">
    ….....
      <div data-role="content">
        <p>Page 1 Content</p>
     <a href="main.html" data-direction="reverse" data-ajax="false"
          data-role="button">Go Back</a>
      </div>
    </div>
    ```

## 它是如何工作的...

在`main.html`中，在包含对`jquery.mobile.js`的引用之前，为`mobileinit`事件添加一个事件处理程序。这在应用程序开始时被调用。设置`$.mobile.ignoreContentEnabled=true`配置。

在`#main`的内容中添加两个`div`标签。在这些`div`标签中都添加一个链接到外部的`page1.html`文件。第二个`div`标签设置了`data-ajax="false"`属性。接下来，将`pageinit`事件绑定到事件处理程序，并在锚元素上调用`jqmHijackable()`方法，如代码所示。这将筛选并提供那些没有继承自父元素的`data-ajax="false"`的锚元素。所以，代码中，`Link 1`被使可用。使用 jQuery 的`attr()`调用，将其`data-transition`属性设置为`flip`，如代码所示。最后，创建`page1.html`，并在**Go Back**链接返回到`#main`页面。

现在，当您点击**Link 1**时，`page1.html`将以翻转转场打开。但是，如果您点击**Link 2**，`page1.html`将以无翻转打开。

## 还有更多...

在该教程中，**Link 2**使用`data-ajax="false"`打开`page1.html`。这将从 DOM 中清除`main.html`。返回到`main.html`将把`main.html`重新加载到 DOM 中，但不会触发`mobileinit`事件。这将导致**Link 1**在打开`page1.html`时不使用翻转转场。为了解决这个问题，在`page1.html`中添加`data-ajax="false"`属性以返回链接。这将重新加载`main.html`到全新的 DOM 中，并触发`mobileinit`事件。现在，通过两个链接从`main.html`到`page1.html`的移动可以顺利进行任意次数。

### $.mobile.ignoreContentEnabled 配置

当设置`$.mobile.ignoreContentEnabled=true`配置时，`jqmHijackable()`方法只能工作。访问每个元素的父节点并检查`data-ajax`值，任何具有`false`设置的父节点及其子元素将从过滤集中删除。

### 注意

即使对一小部分元素使用`jqmHijackable()`也很昂贵，因为要检查所有父元素的`data-ajax`值。

## 参见

+   *使用 jqmEnhanceable()*教程

# 使用$.mobile.base

`$.mobile.base`对象提供对原始文档基础的引用。可以使用`set()`方法在基础对象上设置自定义值。可以使用`reset()`方法恢复原始值。本教程展示了如何使用这些实用方法。

## 准备工作

从`code/09/base`源文件夹中复制此教程的完整代码。可以通过以下网址启动此代码：`http://localhost:8080/09/base/main.html`。

## 如何做...

1.  使用以下代码片段在`main.html`中创建两个按钮：

    ```js
    <div id="content" data-role="content">
      <div id="dispdiv"></div>
      <button id="changebtn">Set Document Base</button>
      <button id="resetbtn">Reset Document Base</button>
    </div>
    ```

1.  添加以下脚本以显示文档基础对象的各个值：

    ```js
    function disp() {
     var str = "<p>Original Document Base: " + $.mobile.getDocumentBase()
        + "</p>" + "<p>Document Base set to : " 
        + $.mobile.base.element.attr("href");
      $("#dispdiv").html(str);
    }
    ```

1.  在`pageinit`事件处理程序中调用`$.mobile.base`实用方法：

    ```js
    $("#main").live("pageinit", function(event) {
      disp();
      $("#changebtn").bind("click", function(event, ui) {
     $.mobile.base.set("http://localhost:8080/");
        disp();
      });
      $("#resetbtn").bind("click", function(event, ui) {
     $.mobile.base.reset();
        disp();
      });
    });
    ```

## 它是如何工作的...

在`main.html`中添加一个空的`div`标签，`id="dispdiv"`，并添加两个按钮(`#changebtn`和`#resetbtn`)，如所示。添加一个`disp()`函数来显示`#dispdiv` div 容器中的当前文档基础和原始文档基础值。您可以使用`$.mobile.getDocumentBase()`方法获取原始文档基础。在`pageinit`事件上调用`disp()`函数。首次加载时，基础值显示如下：

![它是如何工作的...](img/7225_09_05.jpg)

现在，将`#changebtn`的点击事件绑定到事件处理程序，并使用`$.mobile.base.set()`方法将文档基础设置为自定义值。现在单击**设置文档基础**按钮，自定义基础值将显示，如以下截图所示：

![它是如何工作的...](img/7225_09_06.jpg)

将`#resetbtn`绑定到事件处理程序，并通过调用`$.mobile.base.reset()`方法重置文档基础。单击**重置文档基础**按钮，您将看到基础值已恢复。

# 解析 URL

`$.mobile.path`对象提供了您可以使用的属性和方法来处理 URL。本配方向您展示了如何使用`$.mobile.path.parseUrl()`方法获取 URL 的各个组件。

## 准备工作

从`code/09/parseurl`源文件夹复制此配方的完整代码。您可以使用以下 URL 启动此代码：`http://localhost:8080/09/parseurl/main.html`。

## 怎么做...

1.  使用空的`div`标签和一个锚链接创建`main.html`，如下面的代码片段所示：

    ```js
    <div data-role="content">
      <div id="msgdiv"></div>
     <a href="http://user:pwd@localhost:8080/09/main.html?img=img1.png#imgview"
        data-role="button">Link 1</a>
    </div>
    ```

1.  将以下脚本添加到`<head>`部分以在单击时获取锚点按钮的 URL：

    ```js
    $("#main").live("pageinit", function(event) {
     dispPath($.mobile.getDocumentUrl());
      $("a").bind("click", function(event, ui) {
        dispPath($(this).attr("href"));
        event.preventDefault();
        event.stopPropagation();
      });
    ```

1.  添加以下方法以显示 URL 的各个组件：

    ```js
      function dispPath(urlstr) {
     var urlcomp = $.mobile.path.parseUrl(urlstr);
        var str = "<p>href: " + urlcomp.href + "</p>"
          + "<p>hrefNoHash: " + urlcomp.hrefNoHash + "</p>"
          + "<p>hrefNoSearch: " + urlcomp.hrefNoSearch + "</p>"
          + "<p>domain: " + urlcomp.domain + "</p>"
          + "<p>protocol: " + urlcomp.protocol + "</p>"
          + "<p>authority: " + urlcomp.authority + "</p>"
          + "<p>username: " + urlcomp.username + "</p>"
          + "<p>password: " + urlcomp.password + "</p>"
          + "<p>host: " + urlcomp.host + "</p>"
          + "<p>hostname: " + urlcomp.hostname + "</p>"
          + "<p>port: " + urlcomp.port + "</p>"
          + "<p>pathname: " + urlcomp.pathname + "</p>"
          + "<p>directory: " + urlcomp.directory + "</p>"
          + "<p>filename: " + urlcomp.filename + "</p>"
          + "<p>hash: " + urlcomp.hash + "</p>"
          + "<p>search: " + urlcomp.search + "</p>";
        $("#msgdiv").html(str);
      }
    });
    ```

## 它是如何工作的...

向`main.html`添加一个空的`div`标签，`id="msgdiv"`。添加一个带有复杂`href`字符串的链接，如代码所示。创建一个`dispPath`函数，接受一个 URL 字符串。在这里，调用`$.mobile.path.parseUrl`方法来获取包含 URL 各个组件的对象(`#urlcomp`)。在`#msgdiv` div 容器中显示这些 URL 组件。当应用程序首次加载时，在`pageinit`事件处理程序中调用`dispPath()`方法，并将其传递给文档 URL 参数`got`，通过调用`$.mobile.getDocumentUrl()`方法。首次加载时显示以下截图：

![它是如何工作的...](img/7225_09_07.jpg)

接下来，为锚链接的`click`事件添加一个事件处理程序。调用`dispPath()`函数，并将`href`属性作为参数传递给它。通过在锚对象上调用 jQuery 的`attr("href")`方法来获取`href`属性。最后，调用`event.preventDefault()`和`event.stopPropagation()`方法来阻止点击事件的默认操作。现在，当你点击**Link 1**时，复杂`href`属性的 URL 组件将显示如下：

![工作原理...](img/7225_09_08.jpg)

## 还有更多...

`$.mobile.parseUrl()`方法返回一个包含各种 URL 组件的字符串值的对象，如下所示；当特定的 URL 组件未被使用时，存储空字符串：

+   `href`：这是被解析的原始 URL

+   `hrefNoHash`：这是没有哈希组件的`href`属性

+   `hrefNoSearch`：这是没有查询字符串和哈希的`href`属性

+   `domain`：这包含协议和主机部分

+   `protocol`：这是协议（包括`:`字符）

+   `authority`：这包含用户名、密码和主机部分

+   `username`：这是用户名

+   `password`：这是密码

+   `host`：这是主机和端口

+   `hostname`：这是主机名

+   `port`：这是端口（如果协议使用其默认端口，则可能为空）

+   `pathname`：这是所引用文件或目录的路径

+   `directory`：这是不带文件名的路径名的目录部分

+   `filename`：这是不带目录的路径名的文件名部分

+   `hash`：这是哈希组件（包括`#`字符）

+   `search`：这是查询组件（包括`?`字符）

## 另请参阅

+   *使用$.mobile.path 实用方法*教程

# 使用`$.mobile.path`实用方法

本教程向你展示如何在你的应用程序中使用`$.mobile.path`对象提供的实用方法。

## 准备工作

从`code/09/path`源文件夹中复制本教程的完整代码。你可以使用 URL`http://localhost:8080/09/path/main.html`来启动此代码。

## 如何操作...

1.  使用以下代码片段在`main.html`页面上创建四个锚链接：

    ```js
    <div data-role="content">
      <div id="msgdiv"></div>
      <a href="http://localhost:8080/09/base/main.html"
        data-role="button">
        1: http://localhost:8080/09/base/main.html
      </a>
      <a href="http://localhost:8080/09/base/" data-
        role="button">
        2: http://localhost:8080/09/base/
      </a>
      <a href="page1.html" data-role="button">
        3: page1.html
      </a>
      <a href="../" data-role="button">4: ../</a>
    </div>
    ```

1.  在`<head>`部分添加以下脚本以获取点击链接的 URL。

    ```js
    $("#main").live("pageinit", function(event) {
     var docurl = $.mobile.getDocumentUrl();
      $("a").bind("click", function(event, ui) {
        dispPath($(this).attr("href"));
        event.preventDefault();
        event.stopPropagation();
      });
    ```

1.  添加`disppath()`函数以显示`$.mobile.path`实用方法的输出：

    ```js
    function dispPath(urlstr) {
      var urlcomp = $.mobile.path.parseUrl(urlstr);
      var str = "<p>Base: " + docurl + "</p>" 
        + "<p>Page: " + urlcomp.href + "</p>"
        + "<p>Same Domain: " + $.mobile.path.isSameDomain(
        docurl, urlcomp) + "</p>"
        + "<p>is Absolute: "
        + $.mobile.path.isAbsoluteUrl(urlcomp) + "</p>"
        + "<p>is Relative: "
        + $.mobile.path.isRelativeUrl(urlcomp) + "</p>";
     if ($.mobile.path.isRelativeUrl(urlcomp)) {
          str += "<p>Make Absolute Path: " 
              + $.mobile.path.makePathAbsolute(urlcomp.href, 
                $.mobile.path.parseUrl(docurl).pathname) + "</p>"
              + "<p>Make Absolute Url: " 
              + $.mobile.path.makeUrlAbsolute(urlcomp.href, 
              docurl) + "</p>"
        }
        $("#msgdiv").html(str);
      }
    });
    ```

## 工作原理...

在`main.html`中添加一个带有`id="msgdiv"`的空 div 标签。添加四个不同 URL 的链接，如代码所示。在`<head>`部分添加脚本，以在`pageinit`事件处理程序中使用`$.mobile.getDocumentUrl()`方法获取页面的原始文档 URL（`#docurl`）。使用此 URL 作为本教程中的比较参考点。

接下来，为四个锚链接的`click`事件添加事件处理程序。调用`dispPath()`函数，并将`link`的 href 属性作为参数传递给它。你可以通过调用锚对象上的 jQuery`attr("href")`方法来获取`href`属性。还要在此事件处理程序中调用`event.preventDefault()`和`event.stopPropagation()`方法，以防止`click`事件上的任何进一步操作。

在`dispPath`函数中，调用`$.mobile.path.parseUrl`方法来获取传入 URL 的`href`组件。现在，调用各种`$.mobile.path`实用方法，并在代码中显示它们的输出在`#msgdiv` div 容器中。使用`isRelativeUrl()`方法检查传入的 URL 是否是相对的。使用`makePathAbsolute()`和`makeUrlAbsolute()`方法将其转换为绝对值。原始文档 URL 用作这些转换的参考。

页面加载时，你将看到四个链接按钮。点击第一个链接`http://localhost:8080/09/path/main.html`，将显示类似以下截图的输出。该 URL 与参考 URL 处于同一域中，并且该 URL 也是绝对的。

![工作原理...](img/7225_09_09.jpg)

第二个链接，`http://localhost:8080/09/base/`，指向一个文件夹。见下输出；域名相同且 URL 为绝对： 

![工作原理...](img/7225_09_10.jpg)

第三个链接，`page1.html`，是一个相对 URL。使用参考 URL 计算并显示绝对路径和绝对 URL，如下截图所示；这里的**Same Domain**值为**false**。

![工作原理...](img/7225_09_11.jpg)

最后一个链接指向父目录，`../`，再次是一个相对 URL。使用参考 URL 计算绝对路径和 URL，并如下截图所示显示；**Same Domain**值再次为**false**：

![工作原理...](img/7225_09_12.jpg)

## 还有更多...

此配方中使用的`$.mobile.path`实用方法如下：

+   `isAbsoluteUrl`：检查给定的 URL 是否为绝对

+   `isRelativeUrl`：检查给定的 URL 是否为相对的

+   `makePathAbsolute`：将相对路径转换为绝对路径；该方法使用参考路径参数进行转换

+   `makeUrlAbsolute`：将相对 URL 转换为绝对 URL；该方法使用参考 URL 参数进行转换

+   `isSameDomain`：检查两个 URL 是否属于同一个域

## 另请参阅

+   *解析 URL*配方

# 使用静默滚动

你可以使用`$.mobile.silentScroll`方法滚动到页面上的任何垂直位置，而不触发滚动事件监听器。此配方向你展示了如何使用静默滚动。

## 准备工作

从`code/09/silentscroll`源文件夹中复制此配方的完整代码。你可以使用 URL`http://localhost:8080/09/silentscroll/main.html`启动此代码。

## 如何做...

1.  创建带有空的 `div` 标签和两个按钮的 `main.html`，这些按钮将用于滚动到页面的顶部和底部：

    ```js
    <div data-role="content">
      <button id="bottombtn">Page Bottom</button>
      <div id="dispdiv"></div>
      <button id="topbtn">Page Top</button>
    </div>
    ```

1.  在 `<head>` 部分添加以下脚本以创建一个长度较长的页面：

    ```js
    $("#main").live("pageinit", function(event) {
      var str="";
      for (var i=0; i<100; i++) {
        str += i + "<br/>";
      }
      $("#dispdiv").html(str);
    ```

1.  现在，根据点击的按钮，滚动到页面的顶部或底部：

    ```js
      $("#topbtn").bind("click", function(event, ui) {
     $.mobile.silentScroll($.mobile.defaultHomeScroll); 
      });
      $("#bottombtn").bind("click", function(event, ui) {
     $.mobile.silentScroll(2000);
      });
    });
    ```

## 它的工作原理...

将两个 ID 分别为 `bottombtn` 和 `topbtn` 的按钮添加到 `main.html`。创建一个空的带有 `id="dispdiv"` 的 `div` 标签，并用一些长度较长的内容填充它。这里，使用 `pageinit` 事件上的脚本在循环中添加 100 行文本到 `#dispdiv`。页面最初显示如下：

![它的工作原理...](img/7225_09_13.jpg)

将 `#bottombtn` 按钮的 `click` 事件绑定到调用 `$.mobile.silentScroll`，并将一个大值（此处为 2000px）作为 Y 参数。现在，当您点击 **页面底部** 按钮时，页面将滚动到 Y 位置（2000px），该位置位于文档底部，如下面的屏幕截图所示：

![它的工作原理...](img/7225_09_14.jpg)

接下来，绑定 `#topbtn` 按钮的 `click` 事件，并将 `$.mobile.defaultHomeScroll` 属性作为参数传递给 `$.mobile.silentScroll`。现在，点击 **页面顶部** 按钮，页面将滚动回顶部。

## 还有更多...

`silentScroll` 方法不会触发滚动事件监听器。添加以下代码以验证点击任何按钮时不显示警报。但是使用滚动条时会显示警报。

```js
$(window).bind("scrollstop", function(event) {
  alert("Scroll event was fired");
});
```

### `$.mobile.defaultHomeScroll` 属性

此示例中使用的 `$.mobile.defaultHomeScroll` 属性是 jQuery Mobile 框架内部使用的，用于滚动到页面顶部。此值是使用 `$.support.scrollTop` 属性从浏览器获取的。如果此值不为 `0`，框架会将其设置为 `0`。

## 另请参阅

+   第八章 的 *使用滚动事件* 示例，*事件*
