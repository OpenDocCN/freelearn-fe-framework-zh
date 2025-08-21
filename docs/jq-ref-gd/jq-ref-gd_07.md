# 第七章：AJAX 方法

> 她失去了同步
> 
> 她从出口进入
> 
> 从未停下来思考
> 
> ——Devo
> 
> "失去同步"

jQuery 中的 AJAX 功能帮助我们从服务器加载数据，而不需要浏览器页面刷新。在本章中，我们将检查每个可用的 AJAX 方法和函数。我们将看到启动 AJAX 请求的各种方式，以及可以随时观察到正在进行的请求的几种方法。

# 低级接口

这些方法可用于进行任意的 AJAX 请求。

## $.ajax()

| 执行异步 HTTP（AJAX）请求。

```js
$.ajax(settings)

```

|

### 参数

+   settings：请求选项的映射可以包含以下项：

    +   `url`：包含发送请求的 URL 的字符串。

    +   `type`（可选）：定义用于请求的 HTTP 方法的字符串（`GET` 或 `POST`）。默认值为 `GET`。

    +   `dataType`（可选）：定义从服务器返回的数据类型的字符串（`xml，html，json` 或 `script`）。

    +   `ifModified`（可选）：一个布尔值，指示服务器在响应请求之前是否应检查页面是否已修改。

    +   `timeout`（可选）：请求失败时的超时毫秒数。

    +   `global`（可选）：一个布尔值，指示此请求是否会触发全局 AJAX 事件处理程序。默认值为 `true`。

    +   `beforeSend`（可选）：在发送请求之前执行的回调函数。

    +   `error`（可选）：请求失败时执行的回调函数。

    +   `success`（可选）：请求成功时执行的回调函数。

    +   `complete`（可选）：无论请求是否完成都会执行的回调函数。

    +   `data`（可选）：与请求一起发送到服务器的映射或字符串。

    +   `processData`（可选）：一个布尔值，指示是否将提交的数据从对象形式转换为查询字符串形式。默认值为 `true`。

    +   `contentType`（可选）：包含要为请求设置的 MIME 内容类型的字符串。默认值为 `application/x-www-form-urlencoded`。

    +   `async`（可选）：一个布尔值，指示是否异步执行请求。默认值为 `true`。

### 返回值

创建的 XMLHttpRequest 对象。

### 描述

`$.ajax()` 函数是 jQuery 发送的所有 AJAX 请求的基础。这个函数很少直接调用，因为有几个更高级的替代方法可用，如 `$.post()` 和 `.load()`，并且更容易使用。但是，如果需要不太常见的选项，`$.ajax()` 可以提供更大的灵活性。

在其最简单的形式下，`$.ajax()` 函数必须至少指定要加载数据的 URL：

```js
$.ajax({
  url: 'ajax/test.html',
});
```

### 注意

即使这个唯一的必需参数也可以通过使用 `$.ajaxSetup()` 函数设置默认值来变为可选。

这个例子，使用唯一的必需选项，加载了指定 URL 的内容，但对结果没有做任何操作。要使用结果，我们可以实现其中一个回调函数。`beforeSend，error，success`和`complete`选项接受在适当时候被调用的回调函数：

+   `beforeSend`：在发送请求之前调用；`XMLHttpRequest`对象作为一个参数传递给它。

+   `error`：如果请求失败，将`XMLHttpRequest`对象作为参数调用，以及一个指示错误类型的字符串和一个（如适用）异常对象。

+   `success`：如果请求成功，将返回的数据作为其参数传递。

+   `complete`：当请求完成时调用，无论成功与否。将`XMLHttpRequest`对象以及一个包含成功或错误代码的字符串作为参数传递给它。

为了使用返回的 HTML，我们可以实现一个`success`处理程序：

```js
$.ajax({
  url: 'ajax/test.html',
  success: function(data) {
    $('.result').html(data);
    $().log('Load was performed.');
  },
});
```

这样一个简单的例子通常最好使用`.load()`或`$.get()`来提供服务。

`$.ajax()`函数依赖于服务器提供有关检索数据的信息。如果服务器将返回数据报告为 XML，结果可以使用普通的 XML 方法或 jQuery 的选择器来遍历。如果检测到其他类型，比如上面的例子中的 HTML，数据将被视为文本。

通过使用`dataType`选项，可以实现不同的数据处理。除了纯粹的`xml`之外，`dataType`还可以是`html，json`或`script`。如果指定了`html`，检索数据中的任何嵌入 JavaScript 在返回 HTML 字符串之前将被执行。类似地，`script`将执行从服务器拉回的 JavaScript，并将脚本本身作为文本数据返回。`json`选项使用`eval()`来解析获取的数据文件，并将构造的对象作为结果数据返回。

### 注意

我们必须确保 Web 服务器报告的 MIME 类型与我们选择的`dataType`匹配。特别是，`xml`必须由服务器声明为`text/xml`以获得一致的结果。

默认情况下，AJAX 请求使用`GET HTTP`方法发送。如果需要`POST`方法，可以通过为`type`选项设置一个值来指定方法。这个选项影响`data`选项的内容如何发送到服务器。

`data`选项可以包含形式为`key1=value1&key2=value2`的查询字符串，或形式为`{key1: 'value1', key2: 'value2'}`的映射。如果使用后一种形式，数据在发送之前会被转换为查询字符串。如果希望将 XML 对象发送到服务器，则可以通过将`processData`设置为`false`来阻止这种处理。如果我们希望发送 XML 对象到服务器，可能并不需要这个处理；在这种情况下，我们还希望将`contentType`选项从`application/x-www-form-urlencoded`更改为一个更合适的 MIME 类型。

其余选项——`ifModified、timeout、global`和`async`——很少需要。有关`ifModified`的信息，请参阅`$.getIfModified()`函数。请求超时通常可以使用`$.ajaxSetup()`设置为全局默认值，而不是使用`timeout`选项针对特定请求设置。`global`选项阻止注册的处理程序使用`.ajaxSend()、.ajaxError()`或类似方法在此请求触发时触发。例如，如果请求频繁且简短，则可以使用此选项来禁止我们使用`.ajaxSend()`实现的加载指示器。最后，`async`选项的默认值为`true`，表示在请求完成后可以继续执行代码。强烈不建议将此选项设置为`false`，因为它可能导致浏览器无响应。

### 提示

使用此选项使请求同步不如使用*blockUI*插件效果更好。

`$.ajax()`函数返回它创建的`XMLHttpRequest`对象。通常可以丢弃此对象，但它确实提供了一个更低级别的接口来观察和操作请求。特别是，在对象上调用`.abort()`将在请求完成之前停止请求。

## $.ajaxSetup()

| 为将来的 AJAX 请求设置默认值。

```js
$.ajaxSetup(settings)

```

|

### 参数

+   设置：用于未来请求的选项映射。与`$.ajax()`中可能的项相同。

### 返回值

无。

### 描述

有关`$.ajaxSetup()`可用设置的详细信息，请参阅`$.ajax()`。所有使用任何函数的后续 AJAX 调用将使用新的设置，除非被单独调用覆盖，直到下一次调用`$.ajaxSetup()`为止。

例如，在反复 ping 服务器之前，我们可以为 URL 参数设置一个默认值：

```js
$.ajaxSetup({
  url: 'ping.php',
});
```

现在每次进行 AJAX 请求时，将自动使用此 URL：

```js
$.ajax({});
$.ajax({
  data: {'date': Date()},
});
```

# 快捷方法

这些方法使用更少的代码执行更常见类型的 AJAX 请求。

## $.get()

| 使用 GET HTTP 请求从服务器加载数据。

```js
$.get(url[, data][, success])

```

|

### 参数

+   URL：包含要发送请求的 URL 的字符串

+   数据：（可选）：发送请求的数据的映射

+   成功：（可选）：如果请求成功则执行的函数

### 返回值

创建的`XMLHttpRequest`对象。

### 描述

这是一个简写的 AJAX 函数，等价于：

```js
$.ajax({
  url: url,
  data: data,
  success: success
});
```

回调函数传递了返回的数据，这将是一个 XML 根元素或文本字符串，具体取决于响应的 MIME 类型。

大多数实现将指定一个成功处理程序：

```js
$.get('ajax/test.html', function(data) {
  $('.result').html(data);
  $().log('Load was performed.');
});
```

此示例获取所请求的 HTML 片段并将其插入页面。

## $.getIfModified()

| 如果自上次请求以来已更改，则使用`GET HTTP`请求从服务器加载数据。

```js
$.getIfModified(url[, data][, success])

```

|

### 参数

+   URL：包含要发送请求的 URL 的字符串

+   数据：（可选）：发送请求的数据的映射

+   成功：（可选）：如果请求成功则执行的函数

### 返回值

创建的`XMLHttpRequest`对象。

### 描述

这是一个简写的 AJAX 函数，相当于：

```js
$.ajax({
  url: url,
  data: data,
  success: success,
  ifModified: true
});
```

回调传递了返回的数据，这将是一个 XML 根元素或一个文本字符串，具体取决于响应的 MIME 类型。

大多数实现都会指定一个成功处理程序：

```js
$.getIfModified('ajax/test.html', function(data) {
  if (data) {
    $('.result').html(data);
  }
  $().log('Load was performed.');
});
```

此示例获取所请求的 HTML 片段，并将其插入到页面中。

发送 AJAX 请求时，将添加一个`If-Modified-Since`的 HTTP 标头。Web 服务器应该遵守这一点，并在文件未更改时省略数据。这可以用来节省带宽，当在页面内刷新数据时。

仍将未修改的页面响应视为`success`。在这种情况下，回调仍将被执行，但不会有数据可用。回调应该捕获这一点，以避免丢弃以前获取的数据。

## .load()

| 从服务器加载数据，并将返回的 HTML 放入匹配的元素中。

```js
.load(url[, data][, success])

```

|

### 参数

+   url: 包含发送请求的 URL 的字符串

+   data (可选): 要发送的数据的映射

+   success (可选): 如果请求成功，则执行的函数

### 返回值

jQuery 对象，用于链式调用。

### 描述

此方法是从服务器获取数据的最简单方法。它大致等同于`$.get(url, data, success)`，不同之处在于它是一个方法而不是一个全局函数，并且它具有一个隐式回调函数。当检测到成功响应时，`.load()`将匹配元素的 HTML 内容设置为返回的数据。这意味着该方法的大多数用法都可以非常简单：

```js
$('.result').load('ajax/test.html');

```

提供的回调（可选）在执行此后处理后执行：

```js
$('.result').load('ajax/test.html', function() {
  $(this).log('Load was performed.');
});
```

如果提供了数据，则使用 POST 方法；否则，假定为 GET。

### 注意

事件处理套件还有一个名为`.load()`的方法。哪一个被触发取决于传递的参数集。

## .loadIfModified()

| 从服务器加载数据，如果自上次请求以来已更改，则将返回的 HTML 放入匹配的元素中。

```js
.loadIfModified(url[, data][, success])

```

|

### 参数

+   url: 包含发送请求的 URL 的字符串

+    (可选): 要发送的数据的映射

+   success: (可选): 如果请求成功，则执行的函数

### 返回值

jQuery 对象，用于链式调用。

### 描述

此方法大致等同于`$.getIfModified(url, data, success)`，不同之处在于它是一个方法而不是一个全局函数，并且它具有一个隐式回调函数。当检测到成功响应时，`.loadIfModified()`将匹配元素的 HTML 内容设置为返回的数据。这意味着该方法的大多数用法都可以非常简单：

```js
$('.result').loadIfModified('ajax/test.html');

```

提供的回调（如果有）在执行此后处理后执行：

```js
$('.result').loadIfModified('ajax/test.html', function() {
  $(this).log('Load was performed.');
});
```

如果提供了数据，则使用 POST 方法；否则，假定为 GET。

要了解修改日期检查的工作原理，请参见`$.getIfModified()`。

## $.post()

| 使用 `POST HTTP` 请求从服务器加载数据。

```js
$.post(url[, data][, success])

```

|

### 参数

+   url: 包含发送请求的 URL 的字符串

+   （可选）：随请求发送的数据映射

+   success:（可选）：如果请求成功，则执行的函数

### 返回值

创建的 `XMLHttpRequest` 对象。

### 描述

这是一个简写的 AJAX 函数，相当于：

```js
$.ajax({
  type: 'POST',
  url: url,
  data: data,
  success: success
});
```

回调函数传递返回的数据，这将是一个 XML 根元素或一个取决于响应的 MIME 类型的文本字符串。

大多数实现将指定一个成功处理程序：

```js
$.post('ajax/test.html', function(data) {
  $('.result').html(data);
  $().log('Load was performed.');
});
```

这个例子获取请求的 HTML 片段并将其插入到页面上。

使用 `POST` 获取的页面永远不会被缓存，因此 `ifModified` 选项对这些请求没有影响。

## $.getJSON()

| 使用 `GET HTTP` 请求从服务器加载 JSON 编码的数据。

```js
$.getJSON(url[, data][, success])

```

|

### 参数

+   url: 包含发送请求的 URL 的字符串

+   （可选）：随请求发送的数据映射

+   success:（可选）：如果请求成功，则执行的函数

### 返回值

创建的 `XMLHttpRequest` 对象。

### 描述

这是一个简写的 AJAX 函数，相当于：

```js
$.ajax({
  url: url,
  dataType: 'json',
  data: data,
  success: success
});
```

回调函数传递返回的数据，这将根据 JSON 结构定义并使用 `eval()` 函数解析为 JavaScript 对象或数组。

有关 JSON 格式的详细信息，请参阅 [`json.org/`](http://json.org/)。

大多数实现将指定一个成功处理程序：

```js
$.getJSON('ajax/test.json', function(data) {
  $('.result').html('<p>' + data.foo + '</p><p>' + data.baz[1]+ '</p>');
  $().log('Load was performed.');
});
```

当然，此示例依赖于 JSON 文件的结构：

```js
{
  “foo": “The quick brown fox jumps over the lazy dog.",
  “bar": “How razorback-jumping frogs can level six piqued gymnasts!",
  “baz": [52, 97]
}
```

使用这个结构，示例将文件的第一个字符串和第二个数字插入到页面上。如果 JSON 文件中存在语法错误，请求通常会静默失败；为此避免频繁手动编辑 JSON 数据。

## $.getScript()

| 使用 `GET HTTP` 请求从服务器加载 JavaScript，并执行它。

```js
$.getScript(url[, success])

```

|

### 参数

+   url: 包含发送请求的 URL 的字符串

+   success:（可选）：如果请求成功，则执行的函数

### 返回值

创建的 `XMLHttpRequest` 对象。

### 描述

这是一个简写的 AJAX 函数，相当于：

```js
$.ajax({
  url: url,
  type: 'script',
  success: success
});
```

回调函数传递返回的 JavaScript 文件。这通常是没有用的，因为此时脚本已经运行。

脚本在全局上下文中执行，因此可以引用其他变量并使用 jQuery 函数。包含的脚本应该对当前页面产生一定的影响：

```js
$('.result').html('<p>Lorem ipsum dolor sit amet.</p>');
```

然后可以通过引用文件名来包含并运行脚本：

```js
$.getScript('ajax/test.js', function() {
  $().log('Load was performed.');
});
```

在 Safari 中，不能保证在调用成功回调之前执行脚本。实际上，这意味着回调中的代码不应该在没有至少小延迟的情况下调用在外部脚本中定义的函数或引用变量。

# 全局 AJAX 事件处理程序

这些方法注册处理程序，以在页面上发生任何 AJAX 请求时调用。

## .ajaxComplete()

| 注册一个处理程序，以在 AJAX 请求完成时调用。

```js
.ajaxComplete(handler)

```

|

### 参数

+   处理程序：要调用的函数

### 返回值

用于链式调用的 jQuery 对象。

### 描述

每当 AJAX 请求完成时，jQuery 会触发 `ajaxComplete` 事件。所有已使用 `.ajaxComplete()` 方法注册的处理程序都在此时执行。

要观察此方法的操作，我们可以设置一个基本的 AJAX 加载请求：

```js
<div class="trigger button">Trigger</div>
<div class="result"></div>
<div class="log"></div>

```

我们可以将我们的事件处理程序附加到任何元素上：

```js
$('.log').ajaxComplete(function() {
  $(this).log('Triggered ajaxComplete handler.');
});
```

现在，我们可以使用任何 jQuery 方法进行 AJAX 请求：

```js
$('.trigger').click(function() {
  $('.result').load('ajax/test.html');
});
```

当用户单击按钮并且 AJAX 请求完成时，日志消息会被显示。

所有 `ajaxComplete` 处理程序都会被调用，不管完成了什么 AJAX 请求。如果我们必须区分这些请求，我们可以使用传递给处理程序的参数。每次执行 `ajaxComplete` 处理程序时，都会传递事件对象、`XMLHttpRequest` 对象以及用于创建请求的设置对象。例如，我们可以将回调限制为仅处理与特定 URL 相关的事件：

```js
$('.log').ajaxComplete(function(e, xhr, settings) {
  if (settings.url == 'ajax/test.html') {
    $(this).log('Triggered ajaxComplete handler for “ajax/test.html".');
  }
});
```

## .ajaxError()

| 注册一个处理程序，以在 AJAX 请求完成时带有错误时调用。

```js
.ajaxError(handler)
```

|

### 参数

+   处理程序：要调用的函数

### 返回值

用于链式调用的 jQuery 对象。

### 描述

每当 AJAX 请求完成并出现错误时，jQuery 会触发 `ajaxError` 事件。所有已使用 `.ajaxError()` 方法注册的处理程序都在此时执行。

要观察此方法的操作，我们可以设置一个基本的 AJAX 加载请求：

```js
<div class="trigger button">Trigger</div>
<div class="result"></div>
<div class="log"></div>

```

我们可以将我们的事件处理程序附加到任何元素上：

```js
$('.log').ajaxError(function() {
  $(this).log('Triggered ajaxError handler.');
});
```

现在，我们可以使用任何 jQuery 方法进行 AJAX 请求：

```js
$('.trigger').click(function() {
  $('.result').load('ajax/missing.html');
});
```

当用户单击按钮并且 AJAX 请求失败时，因为请求的文件不存在，日志消息会被显示。

所有 `ajaxError` 处理程序都会被调用，不管完成了什么 AJAX 请求。如果我们必须区分这些请求，我们可以使用传递给处理程序的参数。每次执行 `ajaxError` 处理程序时，都会传递事件对象、`XMLHttpRequest` 对象以及用于创建请求的设置对象。如果请求失败是因为 JavaScript 引发了异常，则异常对象会作为第四个参数传递给处理程序。例如，我们可以将回调限制为仅处理与特定 URL 相关的事件：

```js
$('.log').ajaxError(function(e, xhr, settings, exception) {
  if (settings.url == 'ajax/missing.html') {
    $(this).log('Triggered ajaxError handler for “ajax/missing.html".');
  }
});
```

## .ajaxSend()

| 注册一个处理程序，以在 AJAX 请求开始时调用。

```js
.ajaxSend(handler)
```

|

### 参数

+   处理程序：要调用的函数

### 返回值

用于链式调用的 jQuery 对象。

### 描述

每当 AJAX 请求即将发送时，jQuery 会触发 `ajaxSend` 事件。所有已使用 `.ajaxSend()` 方法注册的处理程序都在此时执行。

要观察此方法的操作，我们可以设置一个基本的 AJAX 加载请求：

```js
<div class="trigger button">Trigger</div>
<div class="result"></div>
<div class="log"></div>

```

我们可以将我们的事件处理程序附加到任何元素上：

```js
$('.log').ajaxSend(function() {
  $(this).log('Triggered ajaxSend handler.');
});
```

现在，我们可以使用任何 jQuery 方法进行 AJAX 请求：

```js
$('.trigger').click(function() {
  $('.result').load('ajax/test.html');
});
```

当用户点击按钮并且 AJAX 请求即将开始时，日志消息将被显示。

所有的`ajaxSend`处理程序都会被调用，无论要发送什么 AJAX 请求。如果我们必须区分这些请求，我们可以使用传递给处理程序的参数。每次执行`ajaxSend`处理程序时，都会传递事件对象、`XMLHttpRequest`对象和在创建请求时使用的设置对象。例如，我们可以限制我们的回调只处理与特定 URL 相关的事件：

```js
$('.log').ajaxSend(function(e, xhr, settings) {
  if (settings.url == 'ajax/test.html') {
    $(this).log('Triggered ajaxSend handler for “ajax/test.html".');
  }
});
```

## .ajaxStart()

| 注册一个处理程序，当第一个 AJAX 请求开始时调用。

```js
.ajaxStart(handler)
```

|

### 参数

+   处理程序：要调用的函数

### 返回值

用于链接目的的 jQuery 对象。

### 描述

每当一个 AJAX 请求即将发送，jQuery 都会检查是否还有其他尚未完成的 AJAX 请求。如果没有进行中的请求，jQuery 就会触发`ajaxStart`事件。所有使用`.ajaxStart()`方法注册的处理程序都会在这个时间点执行。

要观察这种方法的实际应用，我们可以设置一个基本的 AJAX 加载请求：

```js
<div class="trigger button">Trigger</div>
<div class="result"></div>
<div class="log"></div>

```

我们可以将我们的事件处理程序附加到任何元素：

```js
$('.log').ajaxStart(function() {
  $(this).log('Triggered ajaxStart handler.');
});
```

现在，我们可以使用任何 jQuery 方法进行 AJAX 请求：

```js
$('.trigger').click(function() {
  $('.result').load('ajax/test.html');
});
```

当用户点击按钮并且 AJAX 请求被发送时，日志消息将会被显示。

## .ajaxStop()

| 注册一个处理程序，当所有 AJAX 请求都完成时调用。

```js
.ajaxStop(handler)
```

|

### 参数

+   处理程序：要调用的函数

### 返回值

用于链接目的的 jQuery 对象。

### 描述

每当一个 AJAX 请求完成，jQuery 都会检查是否还有其他尚未完成的 AJAX 请求；如果没有，jQuery 就会触发`ajaxStop`事件。所有使用`.ajaxStop()`方法注册的处理程序都会在这个时间点执行。

要观察这种方法的实际应用，我们可以设置一个基本的 AJAX 加载请求：

```js
<div class="trigger button">Trigger</div>
<div class="result"></div>
<div class="log"></div>

```

我们可以将我们的事件处理程序附加到任何元素：

```js
$('.log').ajaxStop(function() {
  $(this).log('Triggered ajaxStop handler.');
});
```

现在，我们可以使用任何 jQuery 方法进行 AJAX 请求：

```js
$('.trigger').click(function() {
  $('.result').load('ajax/test.html');
});
```

当用户点击按钮并且 AJAX 请求完成时，日志消息将被显示。

### 注意

因为`.ajaxStart(), .ajaxStop(), .ajaxSend(), ajaxError()`和`.ajaxComplete()`都作为方法而不是全局函数实现，我们可以像这样使用关键字`this`来引用回调函数中的选定元素。

## .ajaxSuccess()

| 注册一个处理程序，当 AJAX 请求成功完成时调用。

```js
.ajaxSuccess(handler)

```

|

### 参数

+   处理程序：要调用的函数

### 返回值

用于链接目的的 jQuery 对象。

### 描述

每当一个 AJAX 请求成功完成，jQuery 就会触发`ajaxSuccess`事件。所有使用`.ajaxSuccess()`方法注册的处理程序都会在这个时间点执行。

要观察这种方法的实际应用，我们可以设置一个基本的 AJAX 加载请求：

```js
<div class="trigger button">Trigger</div>
<div class="result"></div>
<div class="log"></div>

```

我们可以将我们的事件处理程序附加到任何元素：

```js
$('.log').ajaxSuccess(function() {
  $(this).log('Triggered ajaxSuccess handler.');
});
```

现在，我们可以使用任何 jQuery 方法进行 AJAX 请求：

```js
$('.trigger').click(function() {
  $('.result').load('ajax/test.html');
});
```

当用户单击按钮并且 AJAX 请求成功完成时，将显示日志消息。

### 提示

因为 `.ajaxSuccess()` 被实现为方法而不是全局函数，所以我们可以像这样使用 `this` 关键字来在回调函数中引用所选元素。

所有的 `ajaxSuccess` 处理程序都会被调用，无论完成了什么 AJAX 请求。如果我们必须区分这些请求，我们可以使用传递给处理程序的参数。每次执行 `ajaxSuccess` 处理程序时，它都会传递事件对象、`XMLHttpRequest` 对象和用于创建请求的设置对象。例如，我们可以将我们的回调限制为仅处理涉及特定 URL 的事件：

```js
$('.log').ajaxSuccess(function(e, xhr, settings) {
  if (settings.url == 'ajax/test.html') {
    $(this).log('Triggered ajaxSuccess handler for “ajax/test.html".');
  }
});
```

# 辅助函数

此函数协助执行 AJAX 任务时遇到的常见习语。

## `.serialize()`

| 将一组表单元素编码为提交字符串。

```js
.serialize(param)

```

|

### 参数

无。

### 返回值

包含元素序列化表示的字符串。

### 描述

`.serialize()` 方法使用标准的 URL 编码表示法创建一个文本字符串。它在表示一组表单元素的 jQuery 对象上操作。表单元素可以是几种类型：

```js
<form>
  <div><input type="text" name="a" value="1" id="a" /></div>
  <div><input type="text" name="b" value="2" id="b" /></div>
  <div><input type="hidden" name="c" value="3" id="c" /></div>
  <div><textarea name="d" rows="8" cols="40">4</textarea></div>
  <div><select name="e">
    <option value="5" selected="selected">5</option>
    <option value="6">6</option>
    <option value="7">7</option>
  </select></div>
  <div><input type="checkbox" name="f" value="8" id="f" /></div>
  <div><input type="submit" name="g" value="Submit" id="g">
</form>
```

我们可以在选择它们之后对所有这些元素类型进行序列化：

```js
$('form').submit(function() {
  $(this).log($('input, textarea, select').serialize());
  return false;
});
```

这将生成一个标准的查询字符串。

```js
a=1&b=2&c=3&f=8&g=Submit&d=4&e=5

```

该字符串接近于，但不完全相同于，在正常表单提交期间浏览器将生成的字符串。`.submit()` 方法使用每个元素的 `.name` 和 `.value` 属性来创建字符串，因此在这些属性不反映实际表单值的情况下，字符串可能不正确。例如，上面的示例中的复选框始终具有 `.value` 为 `8` 的值，无论框是否被选中。

为了得到更健壮的解决方案，*form* 插件是可用的。它的方法提供了与浏览器提供的相匹配的编码。
