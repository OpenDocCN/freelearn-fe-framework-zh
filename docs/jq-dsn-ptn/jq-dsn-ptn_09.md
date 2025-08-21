# 第九章：客户端模板

本章将演示一些最常用的库，以更快速地创建复杂的 HTML 模板，同时使我们的实现在与传统字符串拼接技术相比更容易阅读和理解。我们将更详细地了解如何使用`Underscore.js`和`Handlebars.js`模板库，体验它们的约定，评估它们的特性，并找到最适合我们口味的。

本章结束时，我们将能够通过可读的模板在浏览器中有效地生成复杂的 HTML 结构，并利用每个模板库的独特特性。

在本章中，我们将：

+   讨论使用专门的模板库的好处

+   介绍当前客户端模板中的潮流，特别是使用 `<% %>` 和 `{{ }}` 作为占位符的家族中的顶级代表

+   以`Underscore.js`为例，介绍一族使用`<% %>`占位符的模板引擎

+   以`Handlebars.js`为例，介绍一族使用大括号 `{{ }}` 占位符的模板引擎

# 介绍 Underscore.js

`Underscore.js`是一个 JavaScript 库，提供了一系列实用方法，帮助 Web 开发人员更有效地工作，专注于应用程序的实际实现，而不必为重复的算法问题烦恼。 `Underscore.js`默认情况下通过全局命名空间的“`_`”标识符访问，这也正是它的名称的由来。

### 注意

与 jQuery 中的 `$` 标识符一样，underscore "`_`" 标识符也可以在 JavaScript 中作为变量名使用。

其中提供的实用程序函数之一是`_.template()`方法，它为我们提供了一种便利的方式，将特定值插入到遵循特定格式的现有模板字符串中。 `_.template()`方法在模板内部识别三种特殊的占位符符号，用于添加动态特性：

+   `<%= %>`符号用作在模板中插入变量或表达式值的最简单方式。

+   `<%- %>`符号对变量或表达式进行 HTML 转义，然后将其插入模板中。

+   `<% %>`标记用于执行任何有效的 JavaScript 语句作为模板生成的一部分。

`_.template()`方法接受遵循这些特征的模板字符串，并返回一个纯 JavaScript 函数，通常称为模板函数，可以使用包含将在模板中插入的值的对象调用。模板函数的调用结果是一个字符串值，这是提供的值在模板内插值的结果：

```js
var templateFn = _.template('<h1><%= title %></h1>');
var resultHtml = templateFn({ 
  title: 'Underscore.js example' 
});
```

例如，上面的代码返回`<h1>Underscore.js 示例</h1>`，等效于以下简写调用：

```js
var resultHtml = _.template('<h1><%= title %></h1>')({ 
  title: 'Underscore.js example' 
});
```

### 注意

关于`_.template`方法的更多信息，您可以在此处阅读文档：[`underscorejs.org/#template`](http://underscorejs.org/#template)。

使`Underscore.js`模板非常灵活的是`<% %>`符号，它允许我们执行任何方法调用，并且例如被用作在模板中创建循环的推荐方法。另一方面，过度使用此功能可能会向您的模板添加过多的逻辑，这是许多其他框架中的已知反模式，违反了**关注点分离**原则。

## 在我们的应用程序中使用 Underscore.js 模板

作为使用`Underscore.js`进行模板化的示例，我们现在将其用于重构仪表板示例中一些模块中发生的 HTML 代码生成，正如我们在之前的章节中所看到的。对现有实现所需的修改仅限于`categories`和`informationBox`模块，它们通过添加新元素来操作页面的 DOM 树。

此类重构可以应用的第一个地方是`categories`模块的`init()`方法。我们可以修改创建`<select>`类别的可用`<option>`的代码如下：

```js
var optionTemplate = _.template('<option value="<%= value %>"><%- title %></option>'); 
var optionsHtmlArray = [];
for (var i = 0; i < dashboard.categories.data.length; i++) { 
    var categoryInfo = dashboard.categories.data[i]; 
 optionsHtmlArray.push(optionTemplate({ 
 value: i, 
 title: categoryInfo.title 
 }));  
}
$categoriesSelector.append(optionsHtmlArray.join(''));
```

如您所见，我们遍历仪表板的类别，以创建并附加适当的`<option>`元素到`<select>`类别元素。在我们的模板中，我们使用`<%= %>`符号来表示`<option>`的`value`属性，因为我们知道它将保存一个不需要转义的整数值。另一方面，我们使用`<%- %>`符号来表示每个`<option>`的内容部分，以便为每个类别的标题进行转义，以防其值不是 HTML 安全字符串。

我们在`for`循环之外使用`_.template()`方法来创建一个单个编译的模板函数，在`for`循环的每次迭代中重复使用。这样一来，浏览器不仅仅执行一次`_.template()`方法，而且还会优化生成的模板函数，并使其在`for`循环中的每次后续执行速度更快。最后，我们使用`join('')`方法来将`optionsHtmlArray`变量的所有 HTML 字符串组合在一起，并通过单个操作将结果`append()`到 DOM 中。

实现相同结果的另一种可能更简单的方法是结合`<% %>`符号和`Underscore.js`提供的`_.each()`方法，使我们能够在模板本身中实现循环。这样，模板将负责对提供的类别数组进行迭代，将复杂性从模块的实现转移到模板中。

```js
var templateSource = ''.concat( 
 '<% _.each(categoryInfos, function(categoryInfo, i) { %>', 
 '<option value="<%= i %>"><%- categoryInfo.title %></option>', 
 '<% }); %>'); 
var optionsHtml = _.template(templateSource)({ 
    categoryInfos: dashboard.categories.data 
}); 
$categoriesSelector.append(optionsHtml);
```

如上面的代码所示，我们的 JavaScript 实现不再包含`for`循环，减少了其复杂性和所需的嵌套。只有一次对`_.template()`方法的调用，很好地将实现抽象为一个生成 HTML 并为所有类别渲染`<option>`元素的操作。您还可以看到这种技术与 jQuery 自身遵循的组合逻辑非常契合，其中方法旨在处理元素集合而不是单个项目。

### 将 HTML 模板与 JavaScript 代码分开

即使引入了上述所有改进，很快就会变得显而易见，在应用逻辑之间编写模板可能不是最佳的方法。一旦您的应用变得足够复杂，或者当您需要使用超过几行的模板时，实现起来会因为应用逻辑和 HTML 模板的混合而感到分散。

解决这个问题的更清晰的方法是将模板存储在页面其他部分的 HTML 代码旁边。这是朝着更好的**关注点分离**迈出的一大步，因为它适当地将呈现与应用逻辑隔离开来。

为了将 HTML 模板包含在不活动形式的网页中，我们需要使用一个宿主标签，这可以阻止它们被渲染，但也允许我们在需要时以程序方式检索其内容。为此，我们可以在页面的`<head>`或`<body>`内使用`<script>`标签，并指定除我们通常用于 JavaScript 代码的常见的`text/javascript`之外的任何`type`。这背后的操作原则是，浏览器在未识别其`type`属性的情况下不尝试解析、执行或呈现`<script>`标签的内容。经过一些实验，`Underscore.js`用户社区基本上采用了这种做法，并同意将`text/template`指定为这些`<script>`标签的首选类型，试图使这些实现在开发人员中更加统一。

### 提示

尽管`Underscore.js`既不是一个偏执的库，也不含有任何特定于模板变得可用的实现，但使用`text/template` `<script>`标签和/或 Ajax 请求都是有价值的技术，被广泛使用且被认为是最佳实践。

作为将复杂模板移入`<script>`标签中的受益示例，我们将重新构建`informationBox`模块的`openNew()`方法。如下所示，在下面的代码中，生成的`<script>`标签格式清晰，并且我们不再需要对多行模板的定义进行字符串拼接：

```js
<script id="box-template" type="text/template"> 
  <div class="boxsizer"> 
    <article class="box"> 
      <header class="boxHeader"> 
        <button class="boxCloseButton">&#10006;</button> 
        <%- itemName %> 
      </header> 
      <div class="boxContent">Loading...</div> 
    </article> 
  </div> 
</script>
```

将 HTML 模板移出我们的代码时的一个好的做法是编写一个抽象的机制来负责检索它们并提供编译后的模板函数。这种方法不仅将实现的其余部分与模板检索机制解耦，而且使其更少重复，并创建了一个专门设计为为应用程序的其余部分提供模板的集中方法。此外，正如我们下面可以看到的，这种方法还允许我们优化模板的检索方式，将好处传播到所有使用它们的地方。

```js
var templateCache = {}; 

function getEmbeddedTemplate(templateName) { 
    var compiledTemplate = templateCache[templateName]; 
    if (!compiledTemplate) { 
        var template = $('#' + templateName).html(); 
 compiledTemplate = _.template(template); 
        templateCache[templateName] = compiledTemplate; 
    } 
    return compiledTemplate; 
}

dashboard.informationBox.openNew = function(itemName) { 
 var boxCompiledTemplate = getEmbeddedTemplate('box-template'); 
    var boxHtml = boxCompiledTemplate({ 
        itemName: itemName 
    }); 
    var $box = $(boxHtml).appendTo($boxContainer); 

    /* ... */
};
```

如上所示的实现中，`informationBox` 模块的 `openNew()` 方法只是通过传递与请求模板相关联的唯一标识符来调用 `getEmbeddedTemplate()` 函数，并使用返回的模板函数生成新框的 HTML，最后将其附加到页面上。实现中最有趣的部分是 `getEmbeddedTemplate()` 方法，它使用 `templateCache` 变量作为字典来保存所有先前编译的模板函数。

第一步始终是检查请求的模板标识符是否存在于我们的模板缓存中。如果不存在，则搜索页面的 DOM 树以查找带有相关 ID 的 `<script>` 标签，并使用其 HTML 内容创建模板函数，然后将其存储在缓存中并返回给调用方。

请记住，在 HTML 模板的所有标识符中使用特定的前缀或后缀是一个好的做法，以避免与其他页面元素的 ID 冲突。为此，在上面的示例中，我们使用了 `-template` 作为我们框模板标识符的后缀。

理想情况下，模板提供程序方法的实现应该在一个单独的模块中，该模块将被应用程序的所有部分使用，但是，由于在我们的仪表板中只使用了一次，我们通过简单地使用一个函数来满足我们演示的需求。

# 引入 Handlebars.js

**Handlebars.js**，或简称 Handlebars，是一种专门的客户端模板库，使 Web 开发人员能够有效地创建语义化模板。使用 Handlebars 进行模板化会导致创建无逻辑的模板，这确保了视图和代码的隔离，有助于保持关注点分离原则。它与 Mustache 模板基本兼容，Mustache 是一个模板语言规范，随着时间的推移已经证明了其有效性，并且有许多主要编程语言的实现。此外，Handlebars 还提供了一组在 Mustache 模板规范之上的扩展，例如辅助方法和局部模板，作为扩展模板引擎并创建更有效模板的一种手段。

### 注意

你可以在[Handlebars 文档](http://handlebarsjs.com/)中查看所有 Handlebars 的文档。你可以在[JavaScript Mustache](https://github.com/janl/mustache.js/)中获取更多有关 Mustache 的信息。

Handlebars 提供的主要模板表示法是双花括号语法 `{{ }}`。由于 Handlebars 最初是为 HTML 模板设计的，所以默认情况下也适用于 HTML 转义，降低了未转义值可能到达模板并导致潜在安全问题的几率。如果需要特定部分的模板进行非转义的插值，我们可以使用三个花括号的表示法 `{{{ }}}`。

此外，由于 Handlebars 阻止我们直接从模板中调用方法，它为我们提供了定义和使用辅助方法和块表达式的能力，以涵盖更复杂的用例，同时帮助我们尽可能地保持模板的清晰和可读性。内置助手集包括 `{{#if }}` 和 `{{#each }}` 助手，它们允许我们非常轻松地对数组执行迭代，并根据条件更改模板的结果。

Handlebars 库的中心方法是 `Handlebars.compile()` 方法，它接受模板字符串作为参数，并返回一个函数，该函数可用于生成符合所提供模板形式的字符串值。然后，可以使用一个对象作为参数调用此函数（与 `Underscore.js` 中一样），其中的属性将用作对原始模板中定义的所有 Handlebars 表达式（花括号表示法）进行评估的上下文：

```js
var templateFn = Handlebars.compile('<h1>!!!{{ title }}!!!</h1>');
var resultHtml = templateFn({ 
  title: '> Handlebars example <'
});
```

作为示例，上述代码返回 `"<h1>!!!&gt; Handlebars example &lt;!!!</h1>"`，将插入的标题转换为安全的 HTML 字符串，但是当附加到页面的 DOM 树时，它将以正确的方式呈现。当然，如果我们不需要将编译后的模板函数的引用保留以供将来使用，则可以使用以下简写调用来实现相同的结果：

```js
var resultHtml = Handlebars.compile('<h1>!!!{{ title }}!!!</h1>')({ 
  title: '> Handlebars example <' 
});
```

## 在我们的应用程序中使用 Handlebars.js

作为使用 `Handlebars.js` 进行模板化的示例，并且为了展示它与 `Underscore.js` 模板的区别，我们现在将使用它来重构我们的仪表板示例，就像我们在前一节中所做的那样。与之前一样，重构仅限于 `categories` 和 `informationBox` 模块，这些模块通过添加新元素来操作页面的 DOM 树。

`categories` 模块的 `init()` 方法的重构实现应该如下所示：

```js
var optionTemplate = Handlebars.compile('<option value= "{{ value }}">{{ title }}</option>'); 
var optionsHtmlArray = []; 
for (var i = 0; i < dashboard.categories.data.length; i++) { 
    var categoryInfo = dashboard.categories.data[i]; 
 optionsHtmlArray .push(optionTemplate({ 
        value: i, 
        title: categoryInfo.title 
    })); 
}
$categoriesSelector.append(optionsHtmlArray.join(''));
```

首先，我们使用了`Handlebars.compile()`方法，该方法基于提供的模板字符串生成并返回模板函数。与我们在上一节中看到的`Underscore.js`的实现的主要区别在于，我们现在使用双花括号符号`{{ }}`来插值我们的模板中的值。除了外观上的差异外，`Handlebars.js`还默认执行 HTML 字符串转义，以尝试通过将转义作为其主要用例之一来消除 HTML 注入安全漏洞。

正如我们在本章前面所做的那样，我们将在`for`循环之外创建模板函数，并将其用于为每个`<option>`元素生成 HTML。所有生成的 HTML 字符串都被收集到一个数组中，最终通过一次操作使用`$.append()`方法将它们组合并附加到 DOM 树上。

减少我们实现复杂性的下一个渐进步骤是使用模板引擎本身的循环能力将迭代抽象化为我们的 JavaScript 代码之外：

```js
var templateSource = ''.concat( 
 '{{#each categoryInfos}}', 
 '<option value="{{@index}}">{{ title }}</option>', 
 '{{/each}}'); 
var optionsHtml = Handlebars.compile(templateSource)({ 
    categoryInfos: dashboard.categories.data 
}); 
$categoriesSelector.append(optionsHtml);
```

`Handlebars.js`库允许我们通过使用特殊的`{{#each }}`符号来实现这一点。在`{{#each }}`和`{{/each}}`之间，模板的上下文被更改以匹配迭代的每个单独对象，允许直接访问和插值`categoryInfos`数组中每个对象的`{{ title }}`。此外，为了访问循环计数器，Handlebars 提供了特殊的`@index`变量作为循环的上下文的一部分。

### 注意

您可以阅读[`handlebarsjs.com/reference.html`](http://handlebarsjs.com/reference.html)上的文档，获取 Handlebars 提供的所有特殊符号的完整列表。

### 将 HTML 模板与 JavaScript 代码分离

和大多数模板引擎一样，Handlebars 也让我们将模板与应用程序的 JavaScript 实现隔离开，并通过将它们包含在页面 HTML 中的`<script>`标签中，在浏览器中传递它们。此外，Handlebars 有一定的偏好，更喜欢特殊的`text/x-handlebars-template`作为所有包含 Handlebars 模板的`<script>`标签的 type 属性。例如，这是根据库推荐的方式定义仪表板框的模板的方式：

```js
<script id="box-template" type="text/x-handlebars-template"> 
  <div class="boxsizer"> 
    <article class="box"> 
      <header class="boxHeader"> 
        <button class="boxCloseButton">&#10006;</button> 
        {{ itemName }} 
      </header> 
      <div class="boxContent">Loading...</div> 
    </article> 
  </div> 
</script>
```

### 提示

尽管如果为`<script>`标签指定了不同的`type`，我们的实现仍然可以正常工作，但遵循库的指南显然可以使开发人员之间的实现更加统一。

正如我们在本章前面所做的那样，我们将遵循最佳实践，创建一个单独的函数负责在应用程序中需要的任何地方提供模板：

```js
var templateCache = {}; 

function getEmbeddedTemplate(templateName) { 
    var compiledTemplate = templateCache[templateName]; 
    if (!compiledTemplate) { 
        var template = $('#' + templateName).html(); 
 compiledTemplate = Handlebars.compile(template); 
        templateCache[templateName] = compiledTemplate; 
    } 
    return compiledTemplate; 
} 

dashboard.informationBox.openNew = function(itemName) { 
    var boxCompiledTemplate = getEmbeddedTemplate('box-template'); 
    var boxHtml = boxCompiledTemplate({ 
        itemName: itemName 
    }); 
    var $box = $(boxHtml).appendTo($boxContainer); 

    /* ... */ 
};
```

正如你所看到的，该实现与我们在本章前面看到的`Undescore.js`示例基本相同。唯一的区别是我们现在使用`Handlebars.compile()`方法来从检索到的模板生成已编译模板函数。

### 预编译模板

Handlebars 库的一个额外功能是支持模板预编译。这使我们可以使用一个简单的终端命令预先生成所有模板函数，然后让我们的服务器将它们传送到浏览器，而不是实际的模板。这样，浏览器就可以直接使用预编译的模板，而不需要对每个单独的模板进行编译，使得库和应用程序的执行速度更快。

为了预编译我们的模板，我们首先需要将它们放在单独的文件中。 Handlebars 文档建议我们的文件使用`.handlebars`扩展名，但如果更喜欢，我们仍然可以使用`.html`扩展名。在我们的开发机器上安装编译工具（使用`npm install handlebars -g`），我们可以在终端中发出以下命令来编译模板：

```js
handlebars box-template.handlebars -f box-template.js

```

这将生成实际上是一个将模板添加到`Handlebars.templates`的迷你模块定义的`box-template.js`文件。生成的文件可以像常规 JavaScript 文件一样合并和最小化，并且当被浏览器加载时，模板函数将通过`Handlebars.templates['box-template']`属性可用。

### 注意

请记住，如果模板使用`.html`扩展名，则预编译的模板函数将通过`Handlebars.templates['box-template.html']`属性可用。

正如您所见，使用模板提供者函数有助于将现有应用程序迁移到预编译模板，因为它允许我们封装模板的检索方式。只需将`getEmbeddedTemplate()`更改为以下内容即可将其迁移到预编译模板：

```js
function getEmbeddedTemplate(templateName) { 
    return Handlebars.templates[templateName]; 
}
```

### 注意

有关 Handlebars 中模板预编译的更多信息，请阅读：[`handlebarsjs.com/precompilation.html`](http://handlebarsjs.com/precompilation.html)。

# 异步检索 HTML 模板

掌握客户端模板的最后一步是一种开发实践，该实践允许我们动态加载模板并在已加载的网页中使用它们。这种方法可以导致比在每个页面的 HTML 源文件中将所有可用模板嵌入为`<script>`标签的方法更具可伸缩性的实现。

这种技术的关键要素是仅在需要呈现网页时加载每个模板，通常是在用户操作之后。这种方法的主要优点是：

+   初始页面加载时间减少，因为页面的 HTML 更小。如果我们的应用程序有很多只在特定情况下使用的模板，例如在特定用户交互后，页面尺寸减小的收益将变得更大。

+   用户只在实际使用模板时才会下载模板。通过这种方式，可以减少每个页面加载的总下载资源的大小。

+   对于已经加载的模板的后续请求不会导致额外的下载，因为浏览器的 HTTP 缓存机制将返回缓存的资源。此外，由于浏览器缓存用于所有 HTTP 请求，无论它们来自哪个页面，用户在使用我们的 Web 应用程序时只需下载所需的模板一次。

由于其对用户体验和可伸缩性的好处，这种技术被最流行的电子邮件和社交网络网站广泛使用，根据用户的操作动态加载各种 HTML 模板和 JavaScript 模块。

### 注意

关于如何使用 jQuery 在页面上动态加载 JavaScript 模块的更多信息，请阅读`$.getScript()`方法的文档：[`api.jquery.com/jQuery.getScript/`](https://api.jquery.com/jQuery.getScript/)。

## 采用它在一个已有的实现中

为了说明这个技术，我们将更改`informationBox`模块的`Underscore.js`和`Handlebars.js`实现，以便使用 AJAX 请求获取我们仪表板的盒子模板。

让我们通过分析我们的`Underscore.js`实现所需的改变来继续：

```js
var templateCache = {}; 

function getAjaxTemplate(templateName) { 
    var compiledTemplate = templateCache[templateName]; 
    if (compiledTemplate) { 
        return $.Deferred().resolve(compiledTemplate); 
    } 
    return $.ajax({ 
        mimeType: 'text/html', 
        url: templateName + '.html' 
    }).then(function(template) { 
 templateCache[templateName] = _.template(template); 
        return templateCache[templateName]; 
    }); 
} 
```

正如你在上面的代码中所看到的，我们已经实现了`getAjaxTemplate()`函数作为一种将负责获取模板的机制与使用它的实现解耦的方式。这个实现与我们之前使用的`getEmbeddedTemplate()`函数有很多相似之处，主要区别在于`getAjaxTemplate()`函数是异步的，因此返回一个**Promise**。

`getAjaxTemplate()`函数首先检查所请求的模板是否已经存在于其缓存中，这是为了进一步减少向服务器发出的 HTTP 请求。如果在缓存中找到模板，则它将作为已解决的 Promise 的一部分返回，否则我们将使用`$.ajax()`方法启动一个 AJAX 请求从服务器检索它。像以前一样，我们需要对模板 HTML 文件的命名和用于在服务器上存储它们的路径有一个约定。在我们的示例中，我们正在查找与网页本身相同的目录，并只附加`.html`文件扩展名。在某些情况下，根据所使用的 Web 服务器的不同，还需要额外考虑资源的`mimeType`定义为`text/html`。

当 AJAX 请求完成时，`then()` 方法将以模板内容作为字符串参数执行，用于生成编译后的模板函数。我们的实现最终将编译后的模板函数作为链式 Promise 的结果返回，直接将其添加到缓存中。由于 `getAjaxTemplate()` 函数是异步的，我们还必须更改 `openNew()` 方法的实现，并将所有使用返回的模板函数的代码移到 `then()` 回调内部。除此之外，实现保持不变，并且与之前完全相同地使用模板函数。

```js
dashboard.informationBox.openNew = function(itemName) { 
 var templatePromise = getAjaxTemplate('box-template'); 
    templatePromise.then(function(boxCompiledTemplate) { 
 var boxHtml = boxCompiledTemplate({ 
            itemName: itemName 
        }); 
        var $box = $(boxHtml).appendTo($boxContainer); box); 
        /* ... */ 
    }); 
};
```

当重新实现 `getAjaxTemplate()` 函数以使用 `Handlebars.js` 时，结果代码基本与以前相同。唯一的区别在于调用 `Handlebars.compile()` 方法而不是 `Underscore.js` 的等价方法。这是一个额外的好处，因为许多客户端模板引擎彼此影响，并已经在它们的模板函数的使用方式方面收敛到非常相似的 API，主要是因为现有实现的积极用户反馈。

```js
function getAjaxTemplate(templateName) { 
    /* …same as before... */
    return $.ajax({ /* …same as before... */ }).then(function(template) { 
 templateCache[templateName] = Handlebars.compile(template); 
        return templateCache[templateName]; 
    }); 
}
```

### 注意

请记住，当通过文件系统加载页面时，`$.ajax()` 方法可能在某些浏览器中无法工作，但在像 Apache、IIS 或 nginx 这样的 Web 服务器上加载时则能正常工作。

## 凡事适度

尽管这种技术减少了每个网页的总下载量，但也不可避免地增加了所发出的 HTTP 请求的数量。此外，懒加载每个模板的做法有时会增加用户等待的时间，特别是如果模板在页面的初始渲染中是必需的。

在懒加载和将模板嵌入 `<script>` 标签之间平衡加载模板的方式通常会带来最佳效果。这种混合方法被行业认为是最佳实践，因为它允许我们根据需要微观管理和微调每个实现。根据这种实践，用于页面主要内容呈现的模板被嵌入到其 HTML 中，而其余的模板则在需要时延迟提供，利用浏览器缓存。

此类模板提供程序函数的实现留给读者作为练习。作为提示，此类方法必须是异步的，因为当页面中未找到请求的模板嵌入在 `<script>` 标签中时，它将必须继续并发出 AJAX 请求从服务器检索它。

### 提示

请记住，通常更倾向于在服务器端生成页面的完整初始 HTML 内容，而不是使用客户端模板。这不仅会导致初始页面内容的加载时间更短，而且可以防止在 JavaScript 不可用或发生错误时向用户呈现空白页面的情况发生。

# 总结

在这一章中，我们学习了如何使用两个最常见的客户端模板库：`Underscore.js` 和 `Handlebars.js`。我们还学习了它们如何帮助我们更快地创建复杂的 HTML 模板，同时使我们的实现更易于阅读和理解。我们随后分析了它们的惯例，评估了它们的特性，并通过示例学习了它们如何可以有效且高效地在我们的实现中使用。

完成本章后，我们现在能够通过使用可读模板和利用模板库的独特特点，在浏览器中高效生成复杂的 HTML 结构。

在下一章中，我们将学习如何创建 jQuery 插件来将应用程序的部分抽象为可重用和可扩展的实现方式。我们将介绍开发 jQuery 插件最广泛使用的模式，并分析每种模式帮助解决的实现问题。
