# 第九章：加速 jQuery

jQuery 的批评者有两个合理的抱怨。第一个抱怨是 jQuery 会创建难以阅读的、纷乱的代码。在上一章中，我们通过展示如何编写既易于阅读又易于维护的代码来解决了这个问题。第二个抱怨是 jQuery 会创建慢速的代码。这也是一个合理的抱怨。jQuery 或任何其他库的问题在于，如果你不理解它，很容易选择错误的方式来做某事。在本章中，我们将解决第二个抱怨：慢速的 jQuery 代码。

当然，jQuery 本身是用高度性能调优的 JavaScript 编写的；事实上，我强烈建议你研究它的源代码。jQuery 的性能问题通常在于不理解它的工作原理。正是这种缺乏理解导致程序员编写了低效的代码。但幸运的是，jQuery 并不难理解，当我们将这种理解与性能测量工具结合起来时，我们可以轻松提高代码的性能。

在本章中，我们将：

+   学习如何测量我们的 JavaScript 代码的速度

+   测量不同 jQuery 代码片段的性能

+   学会何时不使用 jQuery，而是使用纯 JavaScript

# 编写性能测试

在我们担心如何提高应用程序的性能之前，我们应该先学习如何衡量它。简单地说“应用程序感觉迟缓”是不够的。要想提高应用程序的性能，必须在改进之前能够对其进行测量。幸运的是，过去几年我们的浏览器已经有了许多改进。其中一项改进是用户计时 API。虽然它只是 W3C 的建议，不是所有浏览器的官方组成部分，但现代版本的所有主要浏览器都支持它，除了 Safari。我们不会将测量代码与应用程序一起部署，因此缺少 Safari 支持虽然令人遗憾，但并不致命。

我知道有些人想知道为什么我们需要一种新的时间测量方式。自从 2009 年 ECMAScript 5.1 引入以来，我们就有了 `Date.now()`，之前是 `new Date().getTime()`。问题在于分辨率；最好的情况下，`Date.now()` 的准确度只有 1 毫秒，这显然是不够好的。计算机可以在 1 毫秒内执行大量的 JavaScript 指令。

用户定时 API 易于使用。我们不打算解释其所有功能。我们只会展示足够的内容来帮助我们编写性能测量代码。我们需要了解的第一个函数是`performance.now()`。它类似于`Date.now()`，因为它返回当前系统时间，但它在两个重要方面有所不同：首先，它返回一个浮点值，而不是像`Date.now()`一样返回整数值。浮点值表示 1 微秒或千分之一毫秒的精度。其次，`performance.now()`是单调递增的，这是一个花哨的说法，意味着它总是增加的。这意味着每当连续调用时，第二次调用的值总是大于第一次调用的值。这是`Date.now()`无法保证的。这可能看起来很奇怪，但`Date.now()`不是单调递增的。`Date.now()`基于时间，大多数系统都有一个进程，通过每 15 或 20 分钟调整`Date.now()`几毫秒来保持时间同步。因为`Date.now()`的分辨率最好是毫秒级，所以在比它更短的时间内发生的任何事情都会被舍入为 0。一个简单的例子会更好地解释这个问题：

```js
  function dateVsPerformance() {
    var dNow1 = Date.now();
    var dNow2 = Date.now();
    var pNow1 = performance.now();
    var pNow2 = performance.now();

    console.info('date.now elapsed: ' + (dNow2 - dNow1));
    console.info('performance.now elapsed: ' + (pNow2 - pNow1));
  }
```

前面的代码非常简单。我们连续调用了`Date.now()`和`performance.now()`，然后显示经过的时间。在大多数浏览器中，`Date.now()`的经过时间将为零，我们本能地知道这不可能是真的。无论你的计算机有多快，执行每条指令总是需要一定的时间。问题在于分辨率：`Date.now()`以毫秒分辨率运行，而 JavaScript 指令需要微秒级别的执行时间。

### 提示

1 毫秒等于 1,000 微秒。

幸运的是，`performance.now()`具有微秒分辨率；它总是显示任意两次调用之间的差异。连续调用时，它通常处于次毫秒级别。

`Performance.now()`是一个非常有用的方法，但它并不是性能工具箱中的唯一工具。用户定时 API 的创建者意识到我们大多数人都会测量我们应用的性能，所以他们编写了方法来简化操作。首先，有`performance.mark()`方法；当传递一个字符串时，它将使用传递的字符串作为键内部存储`performance.now()`的值：

```js
performance.mark('startTask1');
```

上面的代码存储了一个名称为`startTask1`的性能标记。

接下来是`performance.measure()`。它将创建一个命名的时间测量。它有三个字符串作为参数。第一个字符串是测量的名称。第二个字符串是起始性能标记的名称，最后一个字符串是结束性能标记的名称：

```js
performance.measure('task', 'startTask1', 'endTask1');
```

用户定时 API 将使用名称作为键在内部存储测量值。为了查看我们的性能测量，我们只需要请求它们。最简单的方法是请求所有测量值，然后循环显示每个测量值。以下代码演示了这种技术：

```js
<!DOCTYPE html>
<html>
<head lang="en">
  <meta charset="UTF-8">
  <script src="img/"></script>
  <title>Chapter09 - User Timing API</title>
</head>
<body>
<script>
  function showMeasurements() {
    console.log('\n');
    var entries = performance.getEntriesByType('measure');
    for (var i = 0; i < entries.length; i++) {
      console.log('Name: ' + entries[i].name +' Start Time: ' + entries[i].startTime +' Duration: ' + entries[i].duration + '\n');
    }
  }
  function delay(delayCount) {
    for (var ndx = 0; ndx < delayCount; ndx++) {
    }
  }
  function init() {
    performance.mark('mark1');
    delay(1000);
    performance.mark('mark2');
    delay(10);
    performance.mark('mark3');
    performance.measure('task1', 'mark1', 'mark2');
    performance.measure('task2', 'mark2', 'mark3');
    showMeasurements();
    performance.clearMeasures();
  }
  $(document).ready(init);
 </script>
</body>
</html>
```

### 提示

前面的代码将所有结果显示在浏览器控制台上；不会显示到文档中。

操作始于代码挂钩文档就绪事件，调用 `init()` 函数。调用 `performance.mark()` 创建了一个 `mark1` 标记。然后我们调用 `delay()`，计数器值为 1,000，模拟一个有用任务的性能，然后再次调用 `performance.mark()`，创建另一个性能标记 `mark2`。同样，我们调用 `delay()`，这次计数器为 10，创建另一个性能标记 `mark3`。

现在我们有了三个性能标记。为了确定每个模拟任务花费了多长时间，我们需要使用 `performance.measure()` 方法来测量标记。它需要三个参数：测量名称、初始标记名称和最终标记名称。每个测量将被记录并存储在性能对象的内部。为了查看测量值，我们调用 `showMeasurements()` 方法。

`showMeasurements()` 方法首先调用 `performance.getEntriesByType('measure')`。这个方法返回一个数组，其中包含由 performance 对象记录的所有性能测量。数组中的每个项目都是一个包含性能测量名称、开始时间和持续时间的对象。它还包含其性能类型，但我们不显示它。

我们最后要做的事情是调用 `performance.clearMeasures()`。请记住，性能对象在内部存储所有的标记和测量值。如果偶尔不清除它们，你的测量列表可能会变得非常长。当调用 `performance.clearMeasures()` 时没有参数，它会清除所有保存的测量值。它还可以用测量名称调用以清除。你可以通过调用 `performance.clearMarks()` 来清除已保存的标记。不带参数调用会清除所有保存的标记，带有标记名称的调用会清除标记。

## 测量 jQuery

现在我们有了一种测量 JavaScript 性能的方法，让我们测量一些 jQuery：

```js
<!DOCTYPE html>
<html>
<head lang="en">
  <meta charset="UTF-8">
  <link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.2/css/bootstrap.min.css" rel="stylesheet"/>
  <script src="img/jquery.js"></script>
  <script src="img/bootstrap.min.js"></script>
  <title>Chapter 9 - Measure jQuery</title>
</head>
<body>
<nav class="navbar navbar-inverse navbar-fixed-top">
  <div class="container">
    <div class="navbar-header">
      <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#navbar"
              aria-expanded="false" aria-controls="navbar">
        <span class="sr-only">Toggle navigation</span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
      </button>
      <a class="navbar-brand" href="#">Measuring jQuery</a>
    </div>
    <div id="navbar" class="navbar-collapse collapse">
      <form class="navbar-form navbar-right">
        <div class="form-group">
          <input type="text" placeholder="Email" class="form-control">
        </div>
        <div class="form-group">
          <input type="password" placeholder="Password" class="form-control">
        </div>
        <button type="submit" class="btn btn-success">Sign in</button>
      </form>
    </div>
  </div>
</nav>
```

在上述代码中，实际上没有什么复杂的。它使用 Bootstrap 和 jQuery 创建一个导航栏。`nav` 栏并不完全功能；它只是让我们的 jQuery 代码遵循一些标记来解析：

```js
<!-- Main jumbotron for a primary marketing message or call to action -->
<div class="jumbotron">
  <div class="container">
    <h1>Chapter 9</h1>

    <p>This is a template for a simple marketing or informational website. It includes a large callout called a
      jumbotron and three supporting pieces of content. Use it as a starting point to create something more
      unique.</p>

    <p><a class="btn btn-primary btn-lg" href="#" role="button">Learn more "</a></p>
  </div>
</div>

<div class="container">
  <!-- Example row of columns -->
  <div class="row">
    <div class="col-md-4 bosco">
      <h2>First</h2>

      <p>I am the first div. Initially I am the on the left side of the page. </p>

      <p><a class="btn btn-default" href="#" role="button" name="alpha">View details "</a></p>
    </div>
    <div id="testClass" class="col-md-4 ">
      <h2>Second</h2>

      <p>I am the second div. I begin in-between the other two divs. </p>

      <p><a id='find-me' class="btn btn-default find-me" href="#" role="button" name="beta">View details "</a></p>
    </div>
    <div class="col-md-4">
      <h2>Third</h2>

      <p>I am the third div. Initially I am on the right side of the page</p>

      <p><a class="btn btn-default" href="http://www.google.com" role="button" name="delta">View details "</a></p>
    </div>
  </div>

  <hr>
  <form class="myForm">
    <div class="input-group">
      <select id="make" class="form-control">
        <option value="buick">Buick</option>
        <option value="cadillac">Cadillac</option>
        <option value="chevrolet">Chevrolet</option>
        <option value="chrysler">Chrysler</option>
        <option value="dodge">Dodge</option>
        <option value="ford">Ford</option>
      </select>
    </div>
    <div class="input-group">
      <select id="vehicleOptions" multiple class="form-control">
        <option selected value="airConditioning">air conditioning</option>
        <option value="cdPlayer">CD player</option>
        <option selected value="satelliteRadio">satellite radio</option>
        <option value="powerSeats">power seats</option>
        <option value="navigation">navigation</option>
        <option value="moonRoof">moon roof</option>
      </select>
    </div>
    <div class="input-group">
      <label for="comments" class="">Comments:</label>
      <textarea id="comments" class="form-control"></textarea>
    </div>
    <div class="input-group">
      <input type="text" id="firstName" class="form-control" placeholder="first name" value="Bob"/>
      <input type="text" id="lastName" class="form-control" value="" placeholder="last name"/>
    </div>
  </form>
  <hr>

  <footer>
    <p>© Company 2015</p>
  </footer>
</div>
```

前面的标记是主要内容。同样，我们只是给自己一些丰富的 HTML 来解析：

```js
<script type="text/javascript">
  function showMeasurements() {
    console.log('\n');
    var entries = performance.getEntriesByType('measure');
    for (var i = 0; i < entries.length; i++) {
      console.log('Name: ' + entries[i].name +
        ' Duration: ' + entries[i].duration + '\n');
    }
  }

  function init() {
    var ptr1, ptr2;

    performance.mark('mark1');
    ptr1 = $('#testClass > .find-me');
    performance.mark('mark2');
    ptr2 = $('#testClass').find('#find-me');
    performance.mark('mark3');

    performance.measure('with selectors', 'mark1', 'mark2');
    performance.measure('selector+find ', 'mark2', 'mark3');
    showMeasurements();
    performance.clearMeasures();
  }

  $(document).ready(init);
</script>
</body>
</html>
```

上述代码测量了两种不同的 jQuery 代码片段的速度。这两个片段返回一个指向相同元素的 jQuery 对象：具有类名 `find-me` 的唯一锚点标签。有更快的方法来查找元素，我们稍后会介绍这些方法，但现在，我们希望解决我们测量技术中的一个问题。

当代码运行时，它在控制台中显示两个测量值。第一个测量值是使用选择器查找 jQuery 对象所花费的时间。第二个测量值是使用 `id` 选择器结合 `find()` 方法的时间。第二种方法更加优化，应该更快。

当你重复运行测试代码时，问题最明显。每次运行的时间都会有所不同，但它们的变化可以很大，有时，本应更快的代码却更慢。再次运行计时代码，突然间它就变快了。怎么回事？嗯，虽然 JavaScript 是单线程的，我们无法中断我们的代码，但浏览器不是单线程的，操作系统也不是。有时，在我们的测试代码运行时，另一个线程上可能会发生其他事情，导致它看起来变慢。我们能做些什么来解决这个问题呢？

答案是利用平均法则，执行足够多次代码以消除偶尔的小问题。考虑到这一点，这是我们计时代码的改进版本。标记与之前版本相同；只有`<script>`标签中的代码发生了变化：

```js
<script type="text/javascript">
  function showMeasurements() {
    console.log('\n');
    var entries = performance.getEntriesByType('measure');
    for (var i = 0; i < entries.length; i++) {
      console.log('Name: ' + entries[i].name +
        ' Duration: ' + entries[i].duration + '\n');
    }
  }

  function multiExecuteFunction(func) {
    var ndx, counter = 50000;
    for (ndx = 0; ndx < counter; ndx += 1) {
      func();
    }
  }

  function init() {     performance.mark('mark1');
    multiExecuteFunction(function () {
      var ptr1 = $('#testClass > .find-me');
    });
    performance.mark('mark2');

    multiExecuteFunction(function () {
      var ptr2 = $('#testClass').find('#find-me');
    });
    performance.mark('mark3');

    performance.measure('with selectors', 'mark1', 'mark2');
    performance.measure('selector+find ', 'mark2', 'mark3');
    showMeasurements();
    performance.clearMeasures();
  }

  $(document).ready(init);
</script>
```

在代码的新版本中，我们唯一改变的是调用 jQuery 代码的方式。我们不再只调用一次，而是将其传递给一个调用它数千次的函数。代码应该被调用的实际次数取决于你。我喜欢在 10,000 到 100,000 次之间调用它。

现在我们有了一个相当直接和精确的方法来测量我们代码的速度。请记住，我们不应该将性能测量代码与我们的生产网站一起部署。让我们深入了解 jQuery 选择器，以便我们可以理解如何使用正确的选择器可以显著提高我们代码的性能。

# jQuery 选择器

关于选择器的第一件事情是，它们是对浏览器的文档对象模型（DOM）的调用，而所有与 DOM 的交互都很慢。即使是了解 DOM 很慢的开发人员有时也不明白 jQuery 像所有代码一样使用 DOM，它将标记呈现到浏览器页面。选择器是 jQuery 的核心，选择器中的小差异可能导致代码速度的巨大差异。重要的是我们要了解如何编写快速有效的选择器。

## 使用 ID 而不是其他选择器

最快的选择器是基于最快的底层 DOM 代码的选择器。一个快速的基于 DOM 的元素查找方法是 `document.getElementById()`，所以最快的 jQuery 选择器是基于 `id` 选择器的那个。

这并不意味着你应该在你的标记上给每个元素都标上 ID。当有必要时，你应该继续在元素上使用 ID，并使用`id`选择器快速找到它们或靠近它们的元素。

## 缓存你的选择器

对 jQuery 进行选择器评估的每次调用都是一个很大的处理时间投资。jQuery 必须首先解析选择器，调用 DOM 方法执行选择器，最后将结果转换为 jQuery 对象。记住，DOM 很慢。幸运的是，你可以缓存你的选择器。虽然你的代码第一次被调用时会有一个时间惩罚，但随后的调用速度会像他们能达到的那样快。

只要你不进行大量的 DOM 操作，这种方法就可以。通过大量，我是指在页面中添加或删除元素或使缓存选择器失效的其他操作。

## 选择器优化

所有的选择器并不都是平等的。选择正确的选择器可以对你的应用程序性能产生很大的影响，但选择正确的选择器可能会有些棘手。以下是一些提示，可以帮助你创建正确的选择器。请记住，如果对性能感到不确定，请进行测量。

### 从右到左

在 jQuery 的核心深处是 Sizzle 选择器引擎。Sizzle 从右往左读取。所以，你最具体的选择器应该在右边。想象一下，我们正在尝试找到一个具有`bubble`类的`<p>`标签。我们如何优化选择器呢？让我们看一个例子：

```js
var unoptimized = $('div.col-md-4 .bubble');
```

我们的第一个尝试看起来很好。但我们知道最具体的选择器应该在最右边，所以我们在第二个例子中稍微改变了一下：

```js
var optimized = $('.col-md-4 p.bubble');
```

在大多数浏览器中，这样做比第一个例子稍微快一些。在以前的 jQuery 版本中，差异更大。但不要担心；我们还有更多的优化。

### 减少过于具体的选择器

作为开发者，有时候我们会过度做一些事情。尤其在定义选择器时。如果你添加比所需更多的选择器来找到你要找的元素，你会让 jQuery 做更多的工作。尽量减少你的选择器，只留下必需的部分。

让我们看一个例子：

```js
// Too specific – don't do this
var overlySpecific = $('div.container div.row div.col-md-4 p.bubble');
```

这个选择器比以前的例子慢了。你的选择器应该足够具体，可以找到所需的元素，但不要过于具体。

### 缩小你的搜索范围

默认情况下，jQuery 将搜索整个文档，寻找与你的查询匹配的内容。通过缩小你的搜索范围，来帮助它进行搜索。

如果我们想要更快，而不在标记上加入过多的 ID 会怎么样？我们可以利用最近具有 ID 的父标签来执行如下操作：

```js
var fastOptimized = $('#testClass').find('.bubble');
```

## 其他 jQuery 优化

将要来的优化可能最好称为经验法则。它们会让你的代码变得更快，但不会有很大的变化。而且幸运的是，它们很容易遵循。

### 更新到最新版本

更新到最新版本可能是加快 jQuery 代码速度最简单的事情之一。在升级到新版本 jQuery 时，应该始终谨慎，但升级通常会带来提高的速度，以及新功能。现在您知道如何测量代码的性能，您可以在更改版本之前和之后进行测量，看看是否有改进。

不要期望性能有巨大变化，并阅读发布说明，看看是否有任何破坏性的改变。

### 使用正确的版本 jQuery

目前，jQuery 有两个分支：1.x 分支和 2.x 分支。如果您需要支持旧版本的 Internet Explorer，应该只使用 1.x 分支。如果您的网站只在现代浏览器上运行，并且 Internet Explorer 9 是您需要支持的最旧版本，那么您应该切换到 2.x 分支的 jQuery。

jQuery 的 2.x 分支不再支持 Internet Explorer 6、7 和 8，以及与此相关的所有麻烦。这使得代码执行更快，并且使库文件更小，下载速度更快。

### 不要使用已弃用的方法

废弃的方法是 jQuery 开发团队决定在将来版本中删除的方法。该方法实际上可能需要多年才会被移除。您应该尽快将这些方法从代码中删除。方法被弃用的原因可能不是性能问题，但您可以确信 jQuery 团队不会浪费时间优化被标记为弃用的方法。

### 在适当的时候使用 preventDefault()

最快的代码是不运行的代码。一旦事件被处理，其默认行为是向父元素传递，然后再传递给其父元素，一直到达根文档为止。所有这些冒泡都需要时间，如果您已经完成了所有必需的处理，这段时间可能就白白浪费了。

幸运的是，通过在事件处理程序中调用 `event.preventDefault()`，很容易阻止这种默认行为。这样可以阻止不必要的代码执行，并加快您的应用程序速度。

### 永远不要在循环中修改 DOM

永远记住，访问 DOM 是一个缓慢的过程。在循环中进行访问会加剧问题。最好将 DOM 的部分复制到 JavaScript 中，进行修改，然后再将其复制回去。在此示例中，我们将修改一个 DOM 元素，然后与几乎完全相同的修改不在 DOM 中的元素进行比较：

```js
  function showMeasurements() {
    console.log('\n');
    var entries = performance.getEntriesByType('measure');
    for (var i = 0; i < entries.length; i++) {
      console.log('Name: ' + entries[i].name +
        ' Duration: ' + entries[i].duration + '\n');
    }
  }

  function multiExecuteFunction(func) {
    var ndx, counter = 50000;
    for (ndx = 0; ndx < counter; ndx += 1) {
      func();
    }
  }

  function measurePerformance() {
    console.log('\n');
    var entries = performance.getEntriesByType('measure');
    for (var i = 0; i < entries.length; i++) {
      console.log('Name: ' + entries[i].name +
        ' Duration: ' + entries[i].duration + '\n');
    }
  }

  function init() {

    // unoptimized, modifying the DOM in a loop

    var cnt = 0;
    performance.mark('mark1');

    var $firstName = $('.myForm').find('#firstName');
    multiExecuteFunction(function () {
      $firstName.val('Bob ' + cnt);
      cnt += 1;
    });
    performance.mark('mark2');

    // Second optimized, modifying a detached object  

    var myForm = $('.myForm');
    var parent = myForm.parent();
    myForm.detach();
    cnt = 0;

    var $firstName = $('.myForm').find('#firstName');
    multiExecuteFunction(function () {
      $firstName.val('Bob ' + cnt);
      cnt += 1;
    });

    parent.append(myForm);
    performance.mark('mark3');

    performance.measure('DOM mod in loop ', 'mark1', 'mark2');
    performance.measure('Detached in loop', 'mark2', 'mark3');
    measurePerformance();
  }

  $(document).ready(init);
```

在前面的代码中，所有操作都在 `init()` 方法中。我们正在修改一个 `<input>` 标签的值。在第一个未经优化的处理过程中，我们在循环中修改了 DOM。我们做了一些聪明的事情，例如在循环开始之前将选择器缓存到变量中。这段代码一开始看起来速度还挺快的。

在第二次遍历时，在开始操纵元素之前，我们会将元素从 DOM 分离。实际上，我们必须编写更多的代码来做到这一点。首先，我们将表单缓存到名为 `myForm` 的变量中。然后，我们也将其父级缓存到一个变量中。接下来，我们使用 jQuery 的 `detach()` 方法将 `myForm` 从 DOM 中分离。

循环中的代码与我们的第一个版本相同。一旦退出循环，我们将 `myForm` 添加到其父级以恢复 DOM。虽然第二个版本中有更多的 JavaScript 代码，但比第一个版本快了约 5 倍。这就是一直值得追求的性能提升。

# jQuery 并不总是答案

jQuery 是有史以来最流行的 JavaScript 开源库。它在前 100,000 个网站中使用率超过了 60%。但这并不意味着你总是应该使用 jQuery；有时纯 JavaScript 是更好的选择。

## 使用 document.getElementById

当你要查找具有 ID 的 DOM 元素时，调用 `document.getElementById()` DOM 方法比使用 jQuery 更快。为什么？因为这正是 jQuery 在解释你的选择器后会做的事情。如果你不需要一个 jQuery 对象，只想要元素，那就节省几微秒的时间，自己调用 DOM。

```js
var idName = document.getElementById('idName');

```

该方法接受一个参数：`id` 元素的名称。请注意，名称前面没有井号。这不是 jQuery。如果找到元素，则返回对元素对象的引用。如果找不到，则返回 `null`。再次提醒，返回的值不是 jQuery 对象，因此不会附加 jQuery 方法。

还有其他可用的原生浏览器方法，总的来说，它们比用 JavaScript 编写的代码更快，无论是 jQuery、你自己的代码，还是其他库中的代码。另外两种方法是 `document.getElementsByTag()` 和 `document.getElementsByClassName()`。它们返回一个 `HTMLCollection` 对象，这是任何类似数组的元素集合。如果没有匹配，集合为空，长度为零。

### 提示

旧版浏览器，如 Internet Explorer 8，没有 `document.getElementsByClassName()`。因此，如果你需要支持旧版浏览器，在使用它之前应该检查此方法是否存在。如果存在浏览器的原生版本，jQuery 足够智能以使用它，如果缺少则使用自己的代码。

## 使用 CSS

jQuery 和 JavaScript 对许多事情是有用的，但并不应该被用于一切。诸如对元素进行动画、旋转、变换和平移等操作通常可以更顺畅更快捷地使用 CSS。jQuery 有一些使用 CSS 的方法。然而，通过编写自己的 CSS，你可以获得符合自己需求的结果。CSS 可以利用主机系统的**图形处理单元**（**GPU**）来生成无法用任何 jQuery/JavaScript 复制的结果。

# 总结

我们从学习如何测量代码性能开始这一章节。然后，我们将这些知识应用到实践中，测量不同选择器的速度，同时学习如何编写更好、更快的选择器。我们还学习了一些改进代码速度的 jQuery 最佳实践。最后，我们意识到 jQuery 并不总是答案。有时，更好的代码来自于使用普通的 JavaScript 或 DOM 方法。

在最后一章中，我们将介绍 jQuery 插件。插件是一些令人惊叹的功能，全部包装在易于使用的包中。函数使我们能够轻松地将图形小部件，例如日历、滑块和图片轮播，添加到我们的应用中。我们将学习如何使用插件，如何找到它们，最后，如何编写我们自己的插件。
