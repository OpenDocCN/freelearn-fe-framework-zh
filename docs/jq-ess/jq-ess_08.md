# 第八章：写出稍后可读的代码

jQuery 真的帮助我们用更少的代码做更多的事情，但有一件事它没有解决，那就是如何组织我们的代码。起初这可能不是一个问题，但随着您的应用程序在年龄和功能上的增长，它的组织（或者说缺乏组织）会成为一个问题。

在本章中，我们将介绍一些组织 JavaScript 的成熟方法。在本章中我们将：

+   学习一些面向对象的技术，使我们的代码易于理解和维护

+   使用事件解耦我们的代码，并确保不相关的部分不需要直接相互通信

+   快速了解编写 JavaScript 单元测试，特别是使用 Jasmine，这是一个用于测试 JavaScript 代码的行为驱动开发框架

# 关注点分离

软件架构模式，比如**模型-视图-控制器**（**MVC**），主要因为它们直接解决了代码组织的问题而变得流行起来。模型-视图-控制器将应用程序分成三个主要部分。模型部分处理应用程序的数据。控制器从模型获取数据并将其提供给视图，同时它从视图获取用户输入并将其反馈给模型。关于此模式最重要的一点是您永远不应该混合责任。模型永远不包含控制器代码，控制器永远不包含视图，依此类推。这被称为**关注点分离**，或 SoC。如果应用程序的任何部分违反了此规则，您的应用程序将陷入紧密耦合、相互依赖的代码的烂摊子。

我们不需要完全采用 MVC 来获得一些好处。我们可以将其用作指导我们开发的方法。首先，它帮助我们回答这个问题：这段代码应该放在哪里？让我们看一个例子。我们收到的要求是编写代码从我们的 Web 服务中检索会员数据并将其呈现给用户以供选择。我们应该如何进行？您的第一反应可能是编写类似以下的代码：

```js
<!DOCTYPE html>
<html>
<head lang="en">
  <meta charset="UTF-8">
  <script src="img/"></script>
  <title>Chapter08-Clean Code</title>
</head>
<body>
<div>Super Coding Club Members</div>
<hr/>
<ul id="myList"></ul>
<hr/>

<script type="text/javascript">
  // Hook the document ready event and
  $(document).ready(function () {
    // get the user data
    $.getJSON('users.json', function (members) {
      var index, htmlTemplate = '';
      // make sure there are some members
      if (members && members.length) {
        // create the markup
        for (index = 0; index < members.length; index += 1) {
          htmlTemplate += '<li>' + members[index].name.first + '</li>';
        }
        // render the member names
        $('#myList').html(htmlTemplate);
      }
      return members;
    }).fail(function (error) {
      alert("Error: " + error.status + ": " + error.statusText);
    });
  });
</script>
</body>
</html>
```

这段代码实现了我们的需求，但存在几个问题。首先，没有关注点分离。虽然我们并不是在努力创建一个 MVC 应用程序，但我们至少应该努力让函数按照模型、视图和控制器的方式分解。在本例中，我们的模型由`$.getJSON()`方法调用表示。它直接绑定到我们的控制器代码，该控制器代码在此示例中获取模型数据并将其创建为 HTML 模板。最后，我们的视图代码使用`$.html()`方法呈现 HTML 模板。

这段代码也是紧耦合的一个例子。代码的每个部分直接依赖于下一个部分，并且没有办法将它们分开。紧密耦合的代码更难测试。没有简单的方法来测试这段代码的功能。只要看看它。代码位于文档准备好事件内；你必须模拟该事件才能开始测试代码的功能。一旦你模拟了文档准备好事件，你还需要以某种方式模拟`getJSON()`方法，因为代码的其余部分都被深埋在其中。

# 将代码分解成逻辑单元

使前面的代码示例难以理解的原因之一是它没有被分解成逻辑单元。在 JavaScript 中，我们没有像其他面向对象语言那样的类，但我们有对象，甚至有文件来将逻辑相关的代码单元组合在一起。

我们从函数开始分解代码。与其拥有一个做所有事情的函数，不如努力拥有许多函数，每个函数只做一件事。当函数做了太多事情时，它们变得难以理解。注释可能有助于解释代码正在做什么，但编写良好的代码应该能够自我注释。函数有助于清晰地分离不同功能的各个部分。

也许你会想知道为什么这些都很重要，尤其是因为我们有实现我们需求的可工作代码。这很重要，因为典型的程序花费的时间在维护而不是编写上更多。所以，编写易于维护的代码是很重要的。让我们再试一次：

```js
<script type="text/javascript">
  function showHttpError(error) {
    alert("Error: " + error.status + ": " + error.statusText);
  }

  function getMembers(errorHandler) {
    return $.getJSON('users.json').fail(errorHandler);
  }

  function createMemberMarkup(members) {
    var index, htmlTemplate = '';
    members.forEach(function (member) {
      htmlTemplate += '<li>' + member.name.first + '</li>';
    });
    return htmlTemplate;
  }

  function renderMembers($ptr, membersMarkup) {
    $ptr.html(membersMarkup);
  }

  function showMembers() {
    getMembers(showHttpError)
      .then(function (members) {
        renderMembers($('#members'), createMemberMarkup(members));
      });
  }

  // Hook the document ready event
  $(document).ready(function () {
    showMembers();
  });
</script>
```

代码的第二个版本比原始版本更长，即使它缺乏注释，它也更易读。将代码分成单独的函数使得理解它在做什么变得容易。

在完整的 MVC 应用程序中，我们可能会为每个关注点创建单独的类，然后将每个函数移动到它所属的类中。但我们不需要如此正式。首先，我们在 JavaScript 中没有类，但我们有非常强大的对象可以包含函数。所以这一次让我们再试一次，这次使用 JavaScript 对象来捆绑我们的代码：

```js
<script type="text/javascript">
  var members = {
      showHttpError: function (error) {
        alert("Error: " + error.status + ": " + error.statusText);
      },

      get: function (errorHandler) {
        return $.getJSON('users.json').fail(errorHandler);
      },

      createMarkup: function (members) {
        var index, htmlTemplate = '';
        members.forEach(function (member) {
          htmlTemplate += '<li>' + member.name.first + '</li>';
        });
        return htmlTemplate;
      },

      render: function ($ptr, membersMarkup) {
        $ptr.html(membersMarkup);
      },

      show: function () {
        var that = this;
        that.get(that.showHttpError)
          .then(function (members) {
            that.render($('#members'), that.createMarkup(members));
          });
      }
    };

  // Hook the document ready event
  $(document).ready(function () {
    members.show();
  });
</script>

```

在这个版本中，我们将所有代码捆绑到了成员对象中。这样做可以轻松移动我们的代码，并帮助我们将其视为一个单一的、连贯的单位。将代码放入对象中也使得使用`this`构造成为可能。这是否是一种改进还有争议。我见过许多 JavaScript 开发人员将所有对象名称都写出来，只是为了避免考虑`this`。注意，我们还缩短了大多数方法的名称。在许多方法名称中使用`member`这个词是多余的，因为对象的名称是`member`。此外，请注意，我们现在将我们的函数称为“方法”。当一个函数是类或对象的一部分时，它就是一个方法。

# 使用事件解耦代码

通过本书，我们一直在使用事件，但它们总是被 jQuery 或浏览器触发，而不是由我们触发。现在，这将发生变化。事件对于代码的解耦非常有用。想象一下这样一个场景：你和另一个开发者一起在一个应用程序上工作。另一个开发者将向你的代码提供数据。这些数据——让我们称之为数据源——将会间歇性地可用，不像之前通过单个 Ajax 调用提供数据的示例那样。当数据可用时，你的代码，即数据读取器，将把数据呈现到页面上。有几种方法可以做到这一点。

数据源可以提供一个轮询方法供我们调用。我们会重复调用此方法，直到数据源为我们提供一些新数据为止。然而，我们知道轮询是低效的。大多数时候，我们调用轮询服务时，都不会有任何新数据，我们将浪费 CPU 周期而没有返回任何新数据。

我们可以为数据源提供一个方法，每当它有新数据时就调用。这解决了效率问题，因为我们的代码只会在有新数据时被调用。想象一下，如果我们这样做会引入多么糟糕的维护噩梦。当只有一个依赖模块需要数据源提供数据时，似乎很容易。但如果第二个或第三个模块也需要数据源，我们就必须不断更新数据源模块。如果任何数据读取器模块发生变化，数据源也可能需要更新。我们该怎么办呢？

我们可以使用自定义事件。使用自定义事件可以减少耦合度。双方都不直接调用对方。相反，当数据源有新数据时，它会触发自定义事件。任何想要新数据的数据读取器只需注册一个自定义事件的处理程序。

使用自定义事件有一些好处。首先，耦合度较低。其次，可以注册监听器的数据读取器数量没有限制。第三，如果事件被触发而没有监听器，不会发生任何错误。最后，如果有一堆读取器注册但新数据从未到来，也不会发生错误。

使用自定义事件时，我们必须注意记得在使用完毕后释放事件处理程序。如果我们忘记了会发生两件事。首先，我们可能会导致内存泄漏。内存泄漏是指 JavaScript 无法释放一块内存，因为某些东西——在我们的情况下是一个事件处理程序——仍然持有对它的引用。

随着时间的推移，浏览器会在最终崩溃之前开始变得越来越迟缓，因为它开始耗尽内存。其次，我们的事件处理程序可能会被调用太多次。当钩子事件的代码被调用超过一次而事件从未被释放时，就会发生这种情况。在你意识到之前，事件处理程序会被调用两次、三次，甚至更多次，而不是只被调用一次。

自定义事件的最后一个好处是我们已经知道我们需要的大部分内容以实现它们。代码与我们学习的用于执行常规浏览器事件的代码仅略有不同。让我们首先看一个简单的例子：

```js
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <script src="img/jquery-2.1.1.js"></script>
  <title>Chapter 8 Simple Custom Events</title>
</head>
<body>
<button id="eventGen">Trigger Event</button>
<button id="releaseEvent">Release Event Handlers</button>
<div id="console-out" style="width: 100%">
</div>

<script>
  var customEvent = "customEvent";

  function consoleOut(msg){
    console.info(msg);
    $('#console-out').append("<p>" + msg + "</p>");
  }

  function customEventHandler1(eventObj) {
    return consoleOut("Custom Event Handler 1");
  }

  function customEventHandler2(eventObj) {
    return consoleOut("Custom Event Handler 2");
  }

  $(document).ready(function () {
    // Notice we can hook the event before it is called
    $(document).on(customEvent, customEventHandler1);
    $(document).on(customEvent, customEventHandler2);

    // generate a new custom event for each click
    $('#eventGen').on('click', function () {
      $.event.trigger(customEvent);
    });
    // once we release the handlers, they are not called again
    $('#releaseEvent').on('click', function () {
      consoleOut("Handlers released");
      $(document).off(customEvent, customEventHandler1);
      $(document).off(customEvent, customEventHandler2);
    });
  });
</script>
</body>
</html>
```

在这个例子中，我们在页面上放置了两个按钮。第一个按钮触发一个事件，第二个按钮释放事件处理程序。在按钮下方是一个宽的`<div>`，用于显示消息。

在脚本标记内的代码首先创建一个名为`customEvent`的变量，它保存自定义事件的名称。事件的名称由您决定，但我建议使用包含公司反向域名的名称，因为您不希望将来的浏览器发布破坏您的代码，因为它使用了相同的事件名。然后，我们有两个不做任何特别有趣的事件处理程序。

最后，在文档准备就绪事件处理程序中，我们两次挂接了自定义事件。我们使用第一个按钮的点击事件来触发自定义事件，第二个按钮的点击事件来释放处理程序。

在这个例子中我们没有展示的一件事是如何将数据传递给自定义事件处理程序代码。幸运的是，这并不难，所以让我们用另一个例子来展示如何：

```js
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <script src="img/jquery-2.1.1.js"></script>
  <title>Chapter 8 Custom Events 2</title>
</head>
<body>
<div>&nbsp;<button id="stop">Stop Polling</button>&nbsp;<span id="lastNewUser"></span></div>
<hr/>
<div id="showUsers">
</div>
<script>
  var users = [];
  var newUser = "com.therockncoder.new-user";
  function showMessage(msg) {
    $('#lastNewUser').show().html(msg + ' welcome to the group!').fadeOut(3000);
  }
    function init() {
    function getUserName(user) {
      return user.name.first + " " + user.name.last;
    }
    function showUsers(users) {
      var ndx, $ptr = $('#showUsers');
      $ptr.html("");
      for (ndx = 0; ndx < users.length; ndx += 1) {
        $ptr.append('<p>' + getUserName(users[ndx]) + '</p>')
		}
    }
    function addNewNameToList(eventObj, user, count) {
      console.info("Add New Name to List = " + getUserName(user));
      users.push(user);
      showUsers(users);
    }
    function welcomeNewUser(eventObj, user) {
      var name = getUserName(user);
      showMessage(name);
      console.info("got New User " + name);
    }
    $(document).on(newUser, addNewNameToList);
    $(document).on(newUser, welcomeNewUser);}
  function startTimer() {
    init();
    var handleId = setInterval(function () {
      $.getJSON('http://api.randomuser.me/?format=json&nat=us').success(function (data) {
        var user = data.results[0].user;
        $.event.trigger(newUser, [user]);
      });
    }, 5000);
    $('#stop').on('click', function(){
      clearInterval(handleId);
      showMessage('Cancelled polling');
    });
  }
  $(document).ready(startTimer);
 </script>
</body>
</html>
```

让我们逐步走过代码，以确保我们理解它在做什么。

用户界面由一个标有**停止**的单个按钮和一条水平规则组成。会员将出现在水平规则下方，并且消息将出现在按钮旁边。

在脚本标记内，代码通过创建一个空数组来为用户准备和一个字符串来保存我们自定义事件的名称开始。代码包括三个主要函数：`showMessage`，`init`和`startTimer`。当文档准备就绪事件触发时，它调用`init`方法，然后调用`startTimer`方法。

`startTimer`方法每 5 秒重复调用一次随机用户 Web 服务。每次调用它都会给我们一个新的随机用户。在`init`方法中，我们为我们的自定义事件建立了两个处理程序：`addNewNameToList`和`welcomeNewUser`。每个方法都获取事件提供的用户数据，并对其执行不同的操作。要停止示例程序，请单击**停止**按钮，它将清除间隔计时器。

# 使用单元测试

单元测试是一个有趣的主题。似乎每个人都认同编写测试是好的，但很少有人真的这样做。对单元测试的完整检查需要一本完整的书来完成，但希望我们可以涵盖关于单元测试的足够内容，以向您展示如何将其添加到您的代码中，并解释为什么您应该这样做。

我偏爱的单元测试框架是 Jasmine，[`jasmine.github.io/`](http://jasmine.github.io/)。这并不是贬低其他优秀的可用框架，但 Jasmine 可以在前端和后端工作，在浏览器和命令行中工作，正在积极维护，并且提供了一个测试框架所需的所有功能。请记住，虽然这段代码是为 Jasmine 编写的，但这些原则可以应用到任何 JavaScript 单元测试框架上。在我们学习如何编写单元测试之前，我们应该先讨论为什么要编写单元测试。

## 到底为什么要编写单元测试呢？

起初，网站中极少使用 JavaScript。当使用时，只是做一些如果禁用 JavaScript 也能生存下去的小事情。所以，需要 JavaScript 的事物清单很少，主要是客户端表单验证和简单动画。

如今，事情已经改变了。许多网站在禁用 JavaScript 时根本无法运行。其他一些网站在有限功能下可以运行，并通常会显示缺少 JavaScript 的警告。因此，我们如此严重地依赖于它，测试它是有意义的。

我们编写单元测试有三个主要原因：验证我们的应用程序是否正常运行，验证修改后它是否继续正常运行，最后，当使用**测试驱动开发**（**TDD**）或**行为驱动开发**（**BDD**）时，指导我们的开发。我们将专注于第一个原因，验证我们的应用程序是否正常运行。为此，我们将使用 Jasmine 行为驱动的 JavaScript 测试框架。Jasmine 可以从命令行或浏览器运行。我们将只从浏览器中使用它。

为了保持我们的示例简短，我们将运行典型的单元测试示例：一个算术计算器。它将能够对两个数字进行加、减、乘和除运算。为了测试计算器，我们将需要三个文件：`SpecRunner.html`、`calculator.js`和`calculator-spec.js`。`SpecRunner.html`加载其他两个文件和 Jasmine 框架。这是`SpecRunner.html`文件：

```js
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>Jasmine Spec Runner v2.2.0</title>
  <!-- Jasmine files -->
  <link rel="shortcut icon" type="image/png" href="../libs/jasmine-2.3.4/jasmine_favicon.png">
  <link rel="stylesheet" href="../libs/jasmine-2.3.4/jasmine.css">
  <script src="img/jasmine.js"></script>
  <script src="img/jasmine-html.js"></script>
  <script src="img/boot.js"></script>

  <!-- System Under Test -->
  <script src="img/calculator.js"></script>

  <!-- include spec files here... -->
  <script src="img/calculator-spec.js"></script>
</head>
<body>
</body>
</html>
```

`SpecRunner.html`中没有特别具有挑战性的地方。除了 Jasmine 运行所需的文件之外，你还需要放置你的应用程序——不是全部，只需要单元测试能够运行的必要部分和单元测试文件。按照传统，单元测试文件的名称与它们测试的文件相同，只是在末尾加上了`-spec`。

这是`calculator.js`文件：

```js
var calculator = {
  add: function(a, b){
    return a + b;
  },
  subtract: function(a, b){
    return a -b;
  },
  multiply: function(a, b){
    return a * b;
  },
  divide: function(a, b){
    return a / b;
  }
};
```

我们的计算器简单易懂。它是一个 JavaScript，有四个功能：加法、减法、乘法和除法。测试其功能应该同样简单，实际上也是如此。这是测试计算器的`spec`文件：

```js
describe("Calculator", function() {

  it("can add numbers", function(){
    expect(calculator.add(12, 3)).toEqual(15);
    expect(calculator.add(3000, -100)).toEqual(2900);
  });

  it("can subtract numbers", function(){
    expect(calculator.subtract(12, 3)).toEqual(9);
  });

  it("can multiply numbers", function(){
    expect(calculator.multiply(12, 3)).toEqual(36);
  });

  it("can divide numbers", function(){
    expect(calculator.divide(12, 3)).toEqual(4);
  });
});
```

Jasmine 的单元测试框架围绕三种方法工作：`describe`、`it` 和 `expect`。`describe` 是一个全局函数，用于保存一个测试套件。它的第一个参数是一个字符串，表示测试套件的名称。第二个参数是一个作为测试套件的函数。接下来是 `it`。

与 `describe` 类似，`it` 是 Jasmine 中的全局函数，`it` 包含一个规范或 spec。传递给 `it` 的参数也是一个字符串和一个函数。按照传统，字符串的编写应该使得当与 `it` 一起阅读时，它完整描述了规范。函数使用一个或多个期望进行测试。

期望是使用 `expect` Jasmine 函数创建的。`expect` 相当于其他单元测试框架中的 `assert`。它被传递一个参数，即要测试的值，该值调用 Jasmine 中的实际值。在期望之后链接的是 `matcher` 函数。`matcher` 函数被传递了期望的值。

Jasmine 中 `expect` 和 `matcher` 函数的组合是其最美丽的之一。当编写正确时，它们的阅读就像一句英文句子。这使得编写测试和以后阅读测试都很容易。Jasmine 还提供了大量的匹配器，这有助于您编写出您想要的期望。还有一个 `not` 运算符，它将反转任何 `matcher` 函数的逻辑。

要运行示例测试，只需启动 `SpecRunner.html` 页面。它将加载 Jasmine，我们要测试的代码部分以及规范。按照单元测试的传统，页面要么是绿色的，这意味着我们的所有规范都已通过，要么是红色的，这意味着至少有一个规范失败了。

Jasmine 的目标是帮助我们找到并修复破碎的测试。例如，如果我们的添加规范失败了，我们将在红色中看到测试套件的名称和失败的规范一起显示："计算器可以添加数字。"下面是失败的期望。它将向我们展示它的值以及我们期望的值。

在示例代码中，我们使用 `describe` 方法创建我们的测试套件。我们有规范，每个规范测试我们的计算器的一个方面。请注意我们如何使我们的规范的措辞读起来像句子。我们的第一个规范是“它可以添加数字”。当我们的测试通过时，我们将看到测试套件显示单词“计算器”以及下面列出的每个规范，开始测试什么。

Jasmine 还具有 `setup` 和 `teardown` 函数。`setup` 函数在测试套件中的每个规范运行之前执行。`teardown` 函数在每个规范运行后执行。在示例代码中我们没有使用它们，但在更复杂的测试中它们可能非常有用，特别是当需要准备和清理对象之后。

Jasmine 是一个完整的单元测试框架。我们只是揭开了它的冰山一角。还要记住，虽然从浏览器运行 Jasmine 很方便，但是从命令行运行它会更加强大。那样，它就能够集成到你的网站构建流程中。但这只是一个介绍；要完全体会 Jasmine 如何帮助你编写更干净的代码，并帮助你测试 jQuery，你需要在你的代码中尝试它。

# 摘要

在本章中，我们涵盖了很多思想。开发者经常忘记 JavaScript 是一种面向对象的语言，但它不是一种基于类的语言。要牢记的最重要的事情之一是关注点分离的技术。这是保持你的代码易于理解和可维护的基石。学习关于 SoC 的知识引导我们进入了将代码分解为逻辑单元和使用事件来解耦我们的代码的主题。我们在本章结束时学习了如何使用流行的开源工具 Jasmine 对我们的代码进行单元测试。

现在我们已经学会了如何组织我们的代码，是时候把注意力转向与 jQuery 相关的其他人抱怨的事情了：性能。在下一章中，我们将学习如何衡量我们代码的性能以及可以做些什么简单的事情来加快它的速度。相当多的博客文章似乎认为 jQuery 是许多网站速度缓慢的原因。如果你不了解 JavaScript 或 jQuery，很容易得出这个结论。幸运的是，学习一些经验法则并不难，这些法则可以极大地提高你的 jQuery 代码的速度。
