# 第七章：与服务器交谈

在第六章中，我们学会了如何让 jQuery 帮助我们为用户制作更好的表单。一旦表单被填写，我们将需要使用 jQuery 将其发送回服务器并获取新鲜数据。我们生活在单页面，流动应用的世界。互联网上大多数顶级网站通过 Ajax 以无缝方式更新需要更改的页面部分，这对用户来说比通过提交按钮发布数据并加载新页面的老式方式更好。jQuery 准备好在这里帮助我们。我们可以使用它从服务器动态获取新鲜数据。

在本章中，我们将涵盖以下主题：

+   jQuery 之前的时代

+   今天 jQuery 如何帮助我们

+   帮助方法

+   Ajax 事件

为了理解 jQuery 如何帮助我们与服务器通信，我们应该先退一步，探索 jQuery 之前的世界。在那个时候，网站有两种向服务器发送数据的方式：`<form>` 和 `<a>` 标签。

# jQuery 之前

HTML 的`<form>`标签是将数据发送到服务器的元素。它有两个处理发送数据到服务器的属性。首先是`method`属性，它让它指定如何将数据发送回服务器。它有两个可能的值：`get`或`post`。

将`method`属性设置为`get`，它会将表单数据附加到请求的末尾发送到由`action`属性指定的服务器页面。表单将发送所有已启用的具有定义的`name`元素的表单元素的数据。`get`方法只应用于小部分不敏感数据。通过`get`发送的所有数据都可以从浏览器的 URL 栏中看到。

将`method`属性设置为`post`被认为比`get`更安全，因为它将其数据发送在消息正文中，所以在查询字符串中不可见；但不要被愚弄以为数据是安全的，它只是不那么明显。当你需要向服务器发送新数据时，`post`应该是你首选的方法。

`<form>`标签包含了在单击提交按钮时将发送到服务器的所有表单数据元素。请记住，只有有效的元素才会被发送。为了有效，一个元素必须是启用的，并且有一个`name`属性。`name`属性是服务器上将赋予值的名称。与`id`属性不同，`name`值可以重复，但如果它们在一个表单内重复，那么你就需要弄清楚哪一个是哪一个。

另一种有时被忽视的向服务器发送小量信息的方式是通过设置`<a>`标签的`href`属性的查询参数。诚然，它只能发送小片段的信息，但当你需要从一个页面发送数据到下一个页面时，它可以非常有用。

`<form>` 和 `<a>` 标记都会导致页面刷新，但在 jQuery 之前就已经可以使用 Ajax 了。许多人不知道自从 1990 年代后期以来，Microsoft 浏览器就已经支持 Ajax。它们是使用 Microsoft 的专有 ActiveX 对象实现的，但这种功能的有用性并没有被其他浏览器制造商忽略，他们将其作为浏览器的一个对象，即 `XMLHTTPRequest` 对象，简称 XHR。

不幸的是，编写支持这个功能的代码并不容易。就像我们过去在浏览器编程中看到的许多事情一样，相似功能的不同实现使我们开发人员不得不在开始编写功能代码之前编写大量的管道代码。让我们看看 jQuery 带给 Ajax 派对的东西。

# jQuery 如何帮助我们

jQuery 帮助我们的一种方式是通过简化 Ajax 的痛苦。用户不再愿意等待点击提交，页面变空，然后加载新内容的循环。像 Facebook、Gmail 和 Twitter 这样的网站向用户展示了 Web 可以非常像应用程序。尽管 jQuery 是一个库，而不是像 AngularJS 或 Ember 那样的编程框架，但它可以轻松地实现获取和发送服务器数据而无需重新加载页面。

### 提示

为了演示本章中的代码片段，您需要设置一个 Web 服务器。但是，设置 Web 服务器超出了本书的范围。一种简单的方法是使用包含内置 Web 服务器的编辑器/IDE。JetBrains WebStorm 和 Adobe 的 Brackets 就是这样的编辑器。它们都适用于 Windows、Mac OS X 和 Linux。

## 加载 HTML – `.load()`

我们想让我们的网站能够做的第一件事情之一是在页面上加载新的 HTML 标记。这就是 `.load()` 方法派上用场的地方。它使用 Ajax 从服务器的 URL 下载 HTML，并将其插入到指定位置的页面上。如果您需要创建一个简单的单页应用程序，这个方法会让它变得容易。在底层，`.load()` 使用 `HTTP GET` 方法，这是浏览器加载 HTML 时使用的方法。让我们看一些代码：

```js
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <script src="img/"></script>
    <title>Chapter07-AJAX</title>
    <style type="text/css">
        .output-region {
            border: 2px dashed lightblue;
            width: 100%;
            height: 200px;
        }
    </style>
</head>
<body>
<div>
    <div class="output-region" id="outputRegion"></div>
    <form id="myForm">
        <select name="greeting">
            <option selected value="Mr.">Mr.</option>
            <option value="Mrs.">Mrs.</option>
            <option value="Ms.">Ms.</option>
            <option value="Miss">Miss.</option>
            <option value="Dr.">Dr.</option>
        </select>
        <input name="firstName" value="Abel"/>
        <!-- is disabled -->
        <input name="middleName" disabled value="Middle"/>
        <input name="lastName" value="Alpha"/>
        <!-- doesn't have a name attribute -->
        <input id="suffix" value="Suffix"/>
        <input name="age" value="42"/>
    </form>
    <div id="dataTransfer" style="display: none;"><hr/>Data transfer in progress...</div>
    <hr/>
    <button type="button" class="load-page" data-page="page1">Get Page 1</button>
    <button type="button" class="load-page" data-page="page2">Get Page 2</button>
    <button type="button" class="load-page" data-page="page3">Get Page 3</button>
    <button type="button" id="get-javascript">Get JavaScript</button>
    <button type="button" id="get-json">Get JSON</button>
    <button type="button" id="get-get">Get Get Data</button>
    <button type="button" id="get-post">Get Post Data</button>
    <button type="button" id="jq-param">Create Param</button>
    <button type="button" id="serialize">Serialize</button>
    <button type="button" id="serializeArray">Serialize Array</button>
</div>
<script type="text/javascript">
    (function (window, $, undefined) {
        "use strict";

        // Hook the document ready event and
        $(document).ready(function () {
            // error display
            function showError(err) {
                alert('ERROR: ' + err.status + ' - ' + err.statusText);
            }

            function showJsonMessage(data, erase) {
                if(erase){
                    $('#outputRegion').text("");
                }
                $('#outputRegion').append($("<div>").text(JSON.stringify(data)));
            }
            // load new HTML markup code
            $('.load-page').click(function (event) {
                var fileToLoad = $(this).attr("data-page") + ".html";
                $('#outputRegion').load(fileToLoad);
            });
        });
    }());
</script>
</body>
</html>
```

上面的代码分为三个部分。第一部分是 `<head>` 标记中的所有内容。这里唯一重要的是我们从在线仓库加载 jQuery，并包含一些内联 CSS 来标明我们最终将注入标记和 JSON 数据的位置。

下一节是我们的 HTML 标记。我们有一个大的 `<div>`，`id`为 `output-region`，它将保存我们代码的结果。在 `<form>` 标记中，有几个表单元素将为表单提供一些数据。HTML 的最后一行是一系列按钮，将激活我们的每个代码片段。

文件的最后一节是我们的 JavaScript。最初，我们只有一个函数，但随着我们在本章的进展，我们将添加更多的代码。让我们从检查 `load-page` 点击事件处理程序开始。

我们已经看到了很多事件处理程序代码，在这里没有什么新的。 前三个按钮在点击时都使用此处理程序。 处理程序将获取所点击按钮的`data-page`属性。 `data-page`属性告诉代码要加载哪个页面，并将其附加扩展名`.html`。 这将传递给`.load()`方法，它将用它从服务器中抓取的新标记写入到由选择器指定的位置。 如果`.load()`成功检索到 HTML，它将写入到指定的选择器中， 在我们的例子中是具有`outputRegion`ID 的`<div>`。

## 加载 JSON 数据 – .getJSON()

`.getJSON()`方法从传递的 URL 加载 JSON 数据，并调用一个返回数据的成功函数，或者将错误对象传递给一个失败函数。 像 jQuery 1.5 及更高版本中的大多数 Ajax 方法一样，它还返回一个 jQuery promise。

### 提示

promise 是表示异步操作最终结果的对象。 它可以有三种状态：挂起，履行和拒绝。 当它刚创建且尚未解决时，状态为挂起。 如果承诺成功解决，其状态将更改为履行。 如果承诺失败，其状态将更改为拒绝。 一旦承诺的状态从挂起变化，它就永远不会再次更改。

有了 jQuery promise，我们可以将一个`then`函数链接到`$.getJSON`方法。 `then`函数接受两个参数。 第一个是在承诺被成功履行时要调用的函数。 第二个参数是在出现错误时要调用的函数。 如果一切顺利， JSON 数据将被转换为 JavaScript 对象并传递给`success`函数，然后会在警报提示中显示一条消息； 否则，错误对象的内容将被显示。

```js
// load JSON data
$('#get-json').click(function (event) {
    $.getJSON('data.json').then(function(data){
        alert(data.message);
    }, function(err){
        alert('ERROR:' + JSON.stringify(err));
    });
}0029;
```

# 加载和执行 JavaScript – getScript()

大多数 Ajax 方法会从服务器获取某种数据。 `.getScript()`方法有所不同。 它从服务器检索 JavaScript，解析，然后执行它。 像其他 Ajax 方法一样，它返回一个 promise，但在这种情况下，`success`函数不会传递任何数据。

```js
// load and run javascript
$('#get-javascript').click(function (event) {
    $.getScript('script.js').then(function () {
        alert("getScript() was successful.");
    }, function(err){
        alert('ERROR:' + JSON.stringify(err));
    });
});
```

执行`.getScript()`方法加载的代码在执行后仍然可用，但除非您保留对其的引用，否则没有简便的方法来再次调用该代码。在示例代码中，我们将`incrementer`函数分配给 window 对象，以便以后可以调用它。

```js
// wrap the code in a function for information hiding
(function () {
    "use strict"

    // we show a message to the user
    alert("We're from another script file, trust us");

    // bind the function to the window global object so I can call it if I need it.
    window.incrementer = (function () {
        var lastNum = 0;
        return function (num) {
            lastNum += num;
            return lastNum;
        }
    }());
}());
```

## 读取和写入数据：jQuery.get() 和 jQuery.post()

最后两个快捷方法是`$.get()`和`$.post()`方法。 我们将它们一起描述，因为这两种方法都是`jQuery.ajax()`方法的快捷方式。 请记住，任何使用快捷方法完成的任务也可以通过调用`$.ajax()`来完成。 快捷方法会处理大量冗长的`ajax`调用工作。 让我们看一些代码：

```js
// load JSON via $.ajax
$('#get-get').click(function (event) {
    $.ajax({
     method: "GET",url: "data1.json",
     success: function (data) {
       showJsonMessage(data);
    });
});
```

该代码使用`HTTP GET`方法加载数据。`$.get()`快捷方法允许我们将其重写为：

```js
// load JSON via $.get
$('#get-get').click(function (event) {
    $.get("data1.json", function (data) {
        showJsonMessage(data);
    });
});
```

我们只向`$.get()`方法传递了两个参数：数据的 URL 和一个成功函数；我们甚至都没有费心传递错误处理函数。请记住，没有错误处理程序，浏览器将悄无声息地吞掉任何错误。上述代码演示了如何使用带有回调函数的`$.get()`方法。让我们通过演示如何使用 promise 链来使事情更加有趣。

```js
// load JSON via $.get with promise chaining
$('#get-get2').click(function (event) {
    $.get("data1.json").
        then(function (data) {
            showJsonMessage(data, true);
            return $.get("data2.json");
        }).
        then(function (data) {
            showJsonMessage(data);
            return $.get("data3.json");
        }).
        then(function (data) {
            showJsonMessage(data);
            return $.get("data4.json");
        }).
        then(function (data) {
            showJsonMessage(data);
        }, showError);
});
```

上述代码对服务器进行了四个顺序调用。每个调用请求不同的 JSON 数据块，并通过`showJsonMethod()`函数呈现。如果其中任何一个调用失败，则调用最后一个`then()`方法的`showError()`函数，并且不会进行进一步的 Ajax 请求。每个成功的调用都返回下一个调用，因此可以将承诺链接在一起。

一个潜在的缺点是调用是顺序执行的。大多数浏览器至少可以同时进行两个 HTTP 请求，有些甚至可以进行更多。如果性能是一个问题，并且调用的顺序不重要，我们可以同时进行所有的 HTTP 请求，并让浏览器确定它可以处理多少个。幸运的是，jQuery 的承诺有一个`$.when()`方法。它接受您希望等待的所有承诺作为参数。一旦所有承诺都被解决或拒绝，就会调用`.then()`方法。发送给每个承诺的数据作为参数按照承诺在`.when()`方法中列出的顺序发送。

```js
// load JSON via $.get with concurrent promises
$('#get-con').click(function (event) {
    var data1 = $.get("data1.json"),
        data2 = $.get("data2.json"),
        data3 = $.get("data3.json"),
        data4 = $.get("data4.json");
    $.when(data1, data2, data3, data4).then(function(d1, d2, d3, d4){
        showJsonMessage(d1, true);
        showJsonMessage(d2);
        showJsonMessage(d3);
        showJsonMessage(d4);
    }, showError);
});
```

在上述代码中，进行了四个相同的 HTTP 请求，只是现在它们同时进行。每次调用返回数据的顺序是不确定的，但是代码将等待所有四个调用完成后再继续。如果任何调用失败，整个操作将被视为失败，并调用失败方法。这与我们顺序调用代码时不同。每个 HTTP 请求都可能返回数据，但一旦有一个失败，就会调用失败方法，并且不会再进行其他请求。

到目前为止，所有的请求都是使用`$.get()`方法进行的；我们完全忽略了`$.post()`方法。不用担心，这两种方法都是`$.ajax()`方法的快捷方式。我们几乎可以在任何地方使用`$.post()`代替`$.get()`方法。所以让我们替换第一个演示中的`$.get()`方法。

```js
// load JSON via $.post
,/. $('#get-post').click(function (event) {
    $.post("data1.json", function (data) {
        showJsonMessage(data, true);
    });
});
```

如果这些方法可以相互交换，为什么还要两者都有呢？公平地说，我们一直没有按照它们的用途来使用它们。四个主要的 HTTP 动词是 get、post、put 和 delete。Get 用于从数据库中检索一个或多个项目。Post 用于在数据库中创建新记录。Put 用于更新现有记录。最后，delete 从数据库中删除记录。正确使用这些动词是 RESTful 服务 API 的核心所在，这有点超出了本书的主题范围。

# 辅助方法

jQuery 为我们提供了一些 Ajax 辅助方法。只有三个函数，每个都很容易使用，并为我们消除了一些苦差事。

## 创建查询数据 - $.param()

Ajax 请求通常将数据编码为查询字符串形式传递到服务器。查询字符串跟随 URL 并以问号 "?" 开头。每个参数由一个等号分隔的名称和值组成。生成参数数据并不困难，但是有一些编码规则需要正确遵循，否则您的请求可能会失败。`$.param()` 将我们的数据编码成查询字符串格式。

```js
// converts an array of objects to an encoded query string
$('#jq-param').click(function (event) {
    var testData = [
        {name: "first", value: "Troy"},
        {name: "last", value: "Miles"},
        {name: "twitter", value: "@therockncoder"}
    ];
    var myParam = $.param(testData);
    $('#outputRegion').text(myParam);
});
```

在上述代码中，我们编码了一个名为 `testData` 的对象数组。每个对象都有一个 name 和一个 value 属性。这些属性将是编码字符串的名称和值。我们将数组传递给 `$.param()` 方法，并将结果字符串存储在 `myParam` 中，然后将其写入输出 `<div>`。

注意 `$.param()` 方法是如何为我们处理编码的。第三个数组参数包含一个 at 符号 `@`，它被正确编码为 `%40`。它还将编码其他符号和空格。这些符号必须被编码，否则您的 Ajax 请求将失败。或者更糟糕的是，它可能看起来正常工作，但发送和存储的数据却是不正确的。

`$.param()` 方法仅在手动创建 URL 时才必要。如果调用 `$.get()` 或 `$.post()` 方法并向它们传递数据，它们将正确编码并附加到 URL 或消息主体中。

## 从表单创建查询数据 - .serialize()

下一个助手方法 `serialize()`，与 `$.param()` 方法类似，只是它不是传递数据，而是使用由选择器指示的 `<form>` 标记来从所有有效的表单元素中提取数据。

```js
$('#serialize').click(function (event) {
    var myParam = $('#myForm').serialize();
    $('#outputRegion').text(myParam);
});
```

前面的代码在单击 Serialize 按钮时将 `myForm <form>` 标记中的所有表单元素序列化并呈现到页面上。请记住，只有有效的表单元素才会被序列化。如果一个元素被禁用或没有 name 属性，它将不会被序列化。这个方法允许您用 jQuery 替代老式的 HTML 表单提交。

## 从表单数据创建对象 - .serializeArray()

Ajax 助手的最后一个成员是 `.serializeArray()` 方法。与先前描述的 `.serialize()` 方法类似，它从指定 `<form>` 标记内的所有表单元素中获取数据。它只使用有效的表单元素，这些元素必须启用并具有名称元素。这个方法和 `.serialize()` 方法的区别在于数据的编码方式。`.serializeArray()` 方法将表单数据编码为 JavaScript 对象的数组。每个对象由一个 name 和一个 value 属性组成。name 是元素的 name 属性的内容，value 是元素的值。我们可以用这个方法代替 `.serialize()` 方法。

```js
// serialze the form data as an array of objects
$('#serializeArray').click(function (event) {
    var myParam = $('#myForm').serializeArray();
    $('#outputRegion').text(JSON.stringify(myParam));
});
```

对 `.serializeArray()` 调用的结果是一个 JavaScript 对象数组。我们将结果放在变量 `myParam` 中，然后将其发送到 JSON `stringify()` 方法以便显示。

# Ajax 事件

有时，你的应用程序希望知道各种 Ajax 事件何时发生。也许你的应用程序想要显示一个图标，表示数据正在被发送到服务器或从服务器接收，并在请求完成后隐藏该图标。幸运的是，jQuery 为我们提供了全局 Ajax 事件。这些事件使我们能够知道任何 Ajax 活动何时开始、停止、发送数据、错误或成功。这些事件是全局的，所以它们必须挂钩文档元素。让我们将它们添加到我们当前的示例代码中。

```js
var $doc = $(document);
// when an ajax request begins
$doc.ajaxStart(function () {
    console.info("<<<< Triggered ajax start handler.");
    $('#dataTransfer').show('fast');
});
// once all ajax request complete,
$doc.ajaxStop(function () {
    console.info(">>>> Triggered ajax stop handler.");
    $('#dataTransfer').hide('slow');
});
// called at the beginning of each request
$doc.ajaxSend(function (event, jqxhr, settings) {
    console.info("#### Triggered ajaxSend handler for: " + settings.url);
});
// called every time a request succeeds
$doc.ajaxSuccess(function (event, jqxhr, settings) {
    console.info("#### Triggered ajaxSuccess handler for: " + settings.url);
});
// called every time a request fails
$doc.ajaxError(function (event, jqxhr, settings) {
    console.info("#### Triggered ajaxError handler for: " + settings.url);
});
// called at the end of every request whether it succeeds or fails
$doc.ajaxComplete(function (event, jqxhr, settings) {
    console.info("#### Triggered ajaxComplete handler for: " + settings.url);
});
```

在示例代码中，我们挂钩了三个 Ajax 事件：`ajaxStart`、`ajaxStop`和`ajaxSend`。

## 当 Ajax 请求开始时 – .ajaxStart()

当第一个 Ajax 请求开始时触发`.ajaxStart()`方法。如果另一个请求已经在进行中，该事件不会被触发。在示例代码中，我们在此事件的处理程序中使隐藏的`<div>`与消息*数据传输正在进行中……*可见。

## 当 Ajax 请求完成时 – .ajaxStop()

`.ajaxStop()`方法会在所有 Ajax 请求完成后触发。与`.ajaxStart()`方法类似，它智能地在适当时候触发，允许我们将它们配对使用以隐藏和显示消息。

在本地运行代码时，你会发现`stop`事件在`start`事件之后很快就触发了。为了让消息能够被看到，我们给`hide`方法传递了一个`slow`参数。没有这个参数，用户很难读到消息。

## 当 Ajax 请求发送数据时 – .ajaxSend()

在发送 Ajax 数据之前，将调用`.ajaxSend()`处理程序。如果需要区分哪个 Ajax 请求触发了`.ajaxSend()`，它会向你的处理程序函数发送三个参数：`event`、`jqxhr`和`settings`。settings 参数是一个对象，包含两个重要属性：`url`和`type`。`url`属性是一个字符串，保存着请求调用的 URL。`type`是请求使用的 HTTP 动词。通过检查这两个属性，你应该能够确定哪个 Ajax 请求触发了该事件。

## 当 Ajax 请求失败时 – .ajaxError()

如果请求失败，将触发`.ajaxError()`处理程序。jQuery XHR 对象，参数`jqxhr`，将保存错误信息。状态码将在 status 属性中，错误消息将在`statusText`属性中。

## 当 Ajax 请求成功时 – .ajaxSuccess()

如果请求成功，将触发`.ajaxSuccess()`处理程序。jQuery XHR 对象将保存状态信息。同样，状态码在`status`属性中，状态文本在`statusText`属性中。

## 当 Ajax 请求完成时 – .ajaxComplete()

每当 Ajax 请求完成时，都会调用`.ajaxComplete()`处理程序。无论请求成功还是失败，此方法始终会被调用。它总是在成功或错误事件之后调用。

对于单个请求调用的事件顺序始终相同。首先是开始事件，然后是发送事件，接着是成功或错误事件，然后是完成事件，最后是停止事件。如果进行多个请求，则事件触发的顺序变得不确定。唯一可以保证的是开始是第一个事件，而停止是最后一个。

# 总结

现代 Web 应用必须能够与服务器顺畅地发送和检索数据。jQuery 帮助我们与服务器无缝交互，并消除了需要进行完整页面刷新的需要。

在本章中，我们学习了如何从服务器拉取新数据、JavaScript 和 HTML。还学习了如何在不进行页面刷新的情况下向服务器提交数据。我们还学习了一些 jQuery 提供的帮助方法，这些方法使我们更容易地正确打包数据以进行传输。

一个关于 jQuery 的主要批评并不是针对库本身，而是使用它编写的应用往往很快变得难以控制。在下一章中，我们将看看如何保持我们的代码不像意大利面条一样混乱。
