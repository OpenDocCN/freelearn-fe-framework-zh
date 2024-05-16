# 第六章：VuePress 中的主题开发

在上一章中，我们学习了如何使用 Markdown 格式化我们的内容。在那之前，我们已经建立了一个基本的 VuePress 网站。此外，我们还了解了 VuePress 提供的大部分配置设置。

然而，网站开发的一个关键方面仍然缺失。

是的，我们正在谈论主题开发。正如您可能知道的，一个好的主题或模板是任何网站或博客的无形部分。

VuePress 自带默认主题。在第四章 *在 VuePress 中创建网站*中，当您设置您的第一个 VuePress 网站时，您已经了解了默认主题以及如何使用它来展示您的内容。

然而，在本章中，我们将重新审视与 VuePress 主题开发和配置相关的一切。这意味着我们不仅将涵盖配置实体和变量，还将重新审视默认的 VuePress 主题以及它的工作原理，它的代码的具体部分可以用于什么等等。

但除此之外，我们还将把注意力转向 VuePress 主题的定制。我们将在本章的后半部分详细讨论自定义主题配置。

在这一章中，您将学习以下内容：

+   VuePress 的主题开发

+   主题开发的配置实体和值

+   在 VuePress 中使用默认主题配置进行工作

+   在 VuePress 中的自定义主题开发

+   在 VuePress 中使用 CSS 自定义

+   与 Git 存储库同步

# VuePress 中的主题开发

所以现在，不再浪费时间，让我们开始吧！

# 在 VuePress 中使用主题

VuePress 作为一个静态网站生成器，专注于简单和敏捷。这意味着，与 WordPress 不同，您不会找到大量的主题和插件可供使用。虽然这可能会对一些人构成障碍，因为在某些情况下，易于安装的主题是真正节省时间的，但这种功能性和开发者导向的方法对许多其他人非常有用。

它可以帮助您按照您想要的方式编写主题。此外，您还可以仅使用默认主题，并在几分钟内快速搭建一个现场网站。在第四章 *在 VuePress 中创建网站*中，我们看到在 VuePress 中设置默认主页就像编辑一个简单的 Markdown 文件一样容易！

# 是什么以及如何？

当前章节的布局非常简单，因为我们只涉及 VuePress 的一个特定概念——主题。话虽如此，值得记住的是，主题开发本身是一个相当广泛的主题。随着 VuePress 的规模和受欢迎程度的增长，以及随着时间的推移出现新版本，您可能需要复习和修订您的主题开发技能，以确保您遵守 VuePress 和 Vue.js 提出的最新编码标准。

在这一点上，我们将讨论或者说解剖 VuePress 的默认主题结构。这意味着我们将涵盖主页、导航、侧边栏和所有其他相关实体。你可能已经在第四章中学习了基础知识，但现在，我们更感兴趣的是理解这里的代码是如何工作的。你已经在第四章中看到了默认主题的实际应用。

在此之后，我们将把注意力转向定制。在 VuePress 中定制主题需要对默认结构进行一些更改，我们很快就会看到。

但在继续之前，让我们花一点时间来了解一些 VuePress 中基本主题配置值。与我们在之前章节中介绍的 Markdown 配置实体或基本配置实体不同，主题配置并不那么复杂。

# 主题的配置实体

在这一点上，让我们首先了解一些与 VuePress 主题开发相关的基本配置实体。我们已经在之前的章节中学习了其他配置实体。

# 主题

主题配置值用于指定自定义主题。这意味着如果您在项目中使用自定义主题，您需要调用主题配置来指定它。

它是字符串类型，默认情况下未定义，因为 VuePress 使用自己的默认主题，除非另有规定。

# themeConfig

顾名思义，`themeConfig`为当前主题提供配置选项。

它是对象类型，默认情况下写作`{}`。

请注意，`themeConfig`的内容和选项将根据您的主题结构方式而有所不同。随着您在主题定制中的进展，您将经常使用`themeConfig`实体。

这里有一个方便的例子。还记得我们在第三章中学到的 Service Worker，*VuePress 开发-初步步骤*吗？那是一个站点级别的 Service Worker。但是主题级别的呢？

当你使用`themeConfig.serviceWorker`时，你可以将其定制为仅适用于主题级别的内容。通过`themeConfig`，你可以定制 VuePress 开发的多个元素，以满足你的主题需求。

**这有什么用？**

想象一下。你的访问者在多个标签中打开了你的 VuePress 网站，并浏览你的内容。现在，如果你在那个时候更新了你的内容呢？在正常情况下，你的访问者直到关闭并重新打开所有标签，也就是说，硬性的浏览器端刷新或重新加载打开的页面，才能看到新的内容。

当你配置 Service Worker 与`themeConfig`一起工作时，你也可以用它做一些有趣的事情。其中之一就是弹出窗口的用户界面。

是的，每个人都知道弹出窗口是什么，以及它们有多烦人。但我们不是在谈论类似广告的垃圾弹出窗口。相反，`themeConfig.serviceWorker`元素将添加一个`updatePopup`选项，通知用户有新内容可用。

换句话说，当你更新你的网站并且用户打开了标签时，一个弹出窗口将出现，通知用户有新内容，并提供一个刷新按钮，这样新内容就可以立即显示，而不需要关闭客户端。

这个选项在你经常更新的网站上非常有用，比如技术项目的文档等等。

`themeConfig.serviceWorker.updatePopup`选项的基本语法如下（布尔值）：

```js
module.exports = {
  themeConfig: {
    serviceWorker: {
      updatePopup: true //now popup is set to true and will show up
      // default display for frontend is:  
      // updatePopup: { 
      // message: "New content is available.", 
      // buttonText: "Refresh" 
      // }
    }
  }
}
```

请注意，截至目前，VuePress 中的弹出功能仍处于测试阶段。

正如你所看到的，`themeConfig`可以用于各种目的，为你的 VuePress 安装增加更多的功能。

目前就这些了。这是我们需要记住的唯一两个配置值。因此，我们可以把注意力转向默认的 VuePress 主题，以便更好地理解它的功能。

# VuePress 中的默认主题配置

这个特定的部分完全只涉及默认的 VuePress 主题。如果你使用自定义主题，大部分或所有这些设置可能不适用。

在这个阶段，强烈建议您转到您在第四章中创建的虚拟站点，*在 VuePress 中创建站点*。我们涵盖了首页、导航、侧边栏、搜索功能等概念。我们现在将更好地理解这些内容，并了解默认 VuePress 主题提供的其他功能（例如与 GitHub 仓库的集成）。

# 首页

我们知道默认的 VuePress 主题自带自己的首页布局。为了使用这个布局，我们只需要在根目录的`README.md`文件中将`home:`的值设置为`true`。然后该文件将被 VuePress 解析为`index.html`，并显示默认的首页。

我们可以通过`README.md`文件直接向首页添加元数据。在上一章中，我们已经学会了如何以 YAML 格式编写前置内容。因此，现在是分析首页代码的时候了。从第四章中复制相同的首页前置内容，*在 VuePress 中创建站点*，我们得到以下内容：

```js
---
meta:
  - name: description
    content: Just a simple VuePress site about coffee.
  - name: keywords
    content: vuepress coffee
lang: en-US
home: true
navbar: true
actionText: Learn More →
actionLink: /about
features:
- title: Caffeinated
  details: It is obvious that coffee is surely the greatest beverage known to humans.
- title: Keeps You Awake
  details: Grab some strong coffee and stay awake forever... probably not a healthy idea though. 
- title: Good for Coding
  details: Nobody accepts this but programming is definitely impossible without coffee.
footer: MIT Licensed | A sample VuePress site built by Sufyan bin Uzayr
---
```

在上面的代码中，我们首先指定了关于我们站点的元数据。然后，我们添加了调用按钮的链接和锚文本详细信息。最后，我们输入要显示的附加信息。

正如我们在第四章中所看到的，*在 VuePress 中创建站点*，最终结果如下：

![](img/7f6e8c14-1a4d-4972-a8c9-8d825689150e.png)

在前置内容之后放置的任何内容也会被解析为简化的 Markdown，并显示在首页的前置内容下方。

但是，如果您不想使用默认的首页呢？当然，您可以创建一个自定义主题，但为什么要为添加一个新页面而构建一个新主题呢？

在这种情况下，更好的选择是自定义首页布局。我们可以通过自定义布局来实现这一点。

# 页面的自定义布局

我们知道每个 Markdown `.md`文件都是由 VuePress 在 Web 浏览器中呈现为 HTML。一般来说，VuePress 会在 HTML 中的`<div class="page">`容器中解析 Markdown 文件的内容。

这意味着我们的默认布局——包括主要内容、导航栏和其他链接——都包含在容器内。

因此，这里的任务很简单。如果我们需要添加任何自定义布局或组件，我们只需要确保它包含在我们的默认容器内。

例如，假设我们已经指定了一个名为`MyLayout`的自定义`layout`。现在，在 YAML 前置内容中，我们只需要添加以下行：

```js
layout: MyLayout
```

有了这个，给定页面将呈现新的布局。

通过这种方式，您可以为尽可能多的页面指定自定义布局。

# 导航栏

VuePress 中的导航栏包括一组通用项目，例如导航菜单、搜索栏、指向您的 GitHub 存储库的链接（如果有的话）等等。

# 导航栏链接

还记得我们之前讨论过的`themeConfig`变量吗？您可以使用它向导航栏添加任意数量的链接。

让我们分析以下代码：

```js
// Goes to .vuepress/config.js
 module.exports = {
  themeConfig: {
    nav: [
      { text: 'Home', link: '/' },
      { text: 'About', link: '/about' },
      { text: 'Coffee', link: '/coffee' },
      { text: 'External', link: 'http://sufyanism.com' },
 ]
 }
 }
```

在上面的例子中，我们创建了一个简单的导航链接菜单，提供了四个链接：

+   主页

+   关于页面

+   关于咖啡的页面

+   外部链接（GitHub）

相当简单，不是吗？您可以根据需要在这里不断添加链接。在浏览器中的效果如下：

！[](assets/ba00b3ac-cc02-4fe4-9c19-2097184e3126.png)

那么下拉菜单呢？您只需要将所述链接提供为数组，而不是独立的链接。例如，考虑以下代码：

```js
module.exports = {
  themeConfig: {
    nav: [
      {
        text: 'Linkz',
        items: [
          { text: 'Home', link: '/' },
          { text: 'About', link: '/about/' }
        ]
      }
    ]
  }
 }
```

有了这个，链接将显示为下拉菜单。

此外，您还可以拥有非常复杂的菜单，包括下拉条目下的子组和嵌套项目。对于 VuePress 网站，这种类型的菜单结构极不可能被使用，可能也不太有用。尽管如此，这里是这种菜单结构的默认语法，您可以根据自己的需求进行修改：

```js
module.exports = {
  themeConfig: {
    nav: [
      {
        text: 'Label',
        items: [
          { text: 'First', items: [/*  */] },
          { text: 'Second', items: [/*  */] }
        ]
      }
    ]
  }
 }
```

# 搜索栏

您可以选择在 VuePress 中使用原生搜索或 Algolia DocSearch。默认搜索栏位置就在导航菜单链接旁边，就像我们在第四章中看到的那样，*在 VuePress 中创建站点*：

！[](assets/82f865c1-dd3b-49ec-84a4-5e0ab99d5521.png)

# 原生搜索框

VuePress 自带其自己的原生搜索框，您无需进行调整。但是，如果您愿意，您可以使用`themeConfig`值完全禁用搜索框，或者限制显示搜索词的建议数量。

以下是如何禁用原生搜索框：

```js
module.exports = {
  themeConfig: {
    search: false,
  }
 }
```

或者，为了限制建议的数量，尝试以下代码：

```js
module.exports = {
  themeConfig: {
    searchMaxSuggestions: 5
  }
 }
```

VuePress 中原生搜索的一个很大的缺点是它只从`H1`、`H2`和`H3`标签构建其索引。这意味着它不会扫描实际的正文内容以搜索词。

当然，这样的搜索功能可能不适合每个人，因为通常正确的关键词在内容中而不是标题中。

为了克服这一点，我们可以将外部搜索机制集成到我们的主题中。

# 使用 Algolia 搜索

如果您对 Algolia DocSearch 不熟悉，可以访问他们的网站并在[`community.algolia.com/docsearch/`](https://community.algolia.com/docsearch/)上了解更多。

基本上，Algolia DocSearch 是文档搜索引擎的增强版本。它会爬取您的内容，对其进行索引，然后返回更好的搜索结果，涵盖并爬取整个内容，而不仅仅是标题。他们网站的截图如下：

![](img/4fc86854-b6d5-4a6f-9f8b-6c371eb60711.png)

Algolia DocSearch 与各种环境完全兼容，包括 Bootstrap、React，当然还有 Vue.js。此外，它是一种上下文感知的搜索机制，可以随着您的输入而学习。因此，Algolia DocSearch 可以提供一系列功能，这些功能在本机搜索方法中可能缺失，例如：

+   自动完成建议

+   定制分析以查看详细的搜索统计信息

Algolia DocSearch 采用 MIT 许可证，是开源的。因此，为了在您的 VuePress 网站上使用它，您需要在 Algolia 注册一个账户。

Algolia DocSearch 与您的网站集成的详细文档可在[`github.com/algolia/docsearch#docsearch-options`](https://github.com/algolia/docsearch#docsearch-options)上找到。

一旦您在 Algolia 注册了一个账户，您就可以提交您的网站进行索引和爬取。之后，您只需要告知 VuePress 有关 Algolia DocSearch 引擎。为此，您将再次使用非常方便的`themeConfig`选项，如下所示：

```js
module.exports = {
 themeConfig: {
 algolia: {
 apiKey: '<API>',
 indexName: '<INDEX>'
 }
 }
 }
```

请注意，当网站索引完成时，您需要指定自己的 API 密钥和索引名称，这些信息由 Algolia DocSearch 提供。如果缺少 API 密钥或索引名称，或者其中任何一个值不正确，搜索功能将无法正常工作。

# 如何禁用导航栏？

有时，您可能需要禁用导航栏。在这种情况下，您可以通过`themeConfig`选项指定如下：

```js
// Goes to .vuepress/config.js
 module.exports = {
  themeConfig: {
    navbar: false
  }
 }
```

导航栏显示值现在设置为 false，因此它将不会显示在前端。

然而，前面的选项会全局禁用导航栏。要仅针对特定页面禁用它，最好编辑该页面的前置内容，如下所示：

```js
---
 navbar: false
 ---
```

# 上一页和下一页的链接

默认情况下，VuePress 会在站点上每个页面的末尾添加上一页和下一页的导航链接。这些链接是根据侧边栏中的标题自动推断的，并从当前页面计算得出。

然而，你也可以覆盖它们的外观和选择。在当前页面的前置内容中，你只需要指定你的选择。例如，对于自定义页面的选择，你可以添加以下内容：

```js
---
 prev: ./another-page
 next: ./yet-another-page
 ---
```

可选地，你也可以同时禁用两者：

```js
---
 prev: false
 next: false
 ---
```

或者，只需使用以下代码禁用其中一个：

```js
---
 prev: ./some-fancy-page
 next: false
 ---
```

或者：

```js
---
 prev: false
 next: ./my-awesome-page
 ---
```

等等。

这涵盖了关于导航本身的部分。现在，是时候把注意力转向侧边栏了。

# 侧边栏

VuePress 为你的站点提供了一个基于页面结构和其中的标题链接构建的原生侧边栏。

要为你的站点启用侧边栏，你需要使用`themeConfig.sidebar`选项通过链接数组进行配置。请注意，以下代码放入你的`config.js`文件中：

```js
// goes to .vuepress/config.js
 module.exports = {
  themeConfig: {
    sidebar: [
      '/',
      '/page-1',
      ['/page-2', 'optional link text']
    ]
  }
 }
```

在这个示例中，默认情况下链接会指向`README.md`文件。这意味着任何以`/`斜杠结尾的链接都会指向相关的`README.md`文件。

注意可选的链接文本？如果你在这里指定了某些内容，那么它将显示为侧边栏中的链接文本。或者，如果你在页面的前置内容中指定了标题，那么它将显示为侧边栏中的链接文本。最后，如果在任一位置未指定标题，则 VuePress 将自动从相关页面的第一个标题中选择链接文本。

# 修改侧边栏中的标题链接

你可以在 VuePress 的侧边栏中玩转标题链接。这里将讨论一些示例。

# 显示每个页面的标题链接

默认情况下，侧边栏只会显示当前页面的标题链接。你可以将其更改为立即显示所有页面的标题链接。你只需要将相关值设置为`true`即可，如下所示：

```js
module.exports = {
  themeConfig: {
    displayAllHeaders: true // Default is false
  }
 }
```

# 嵌套标题链接

侧边栏，正如你所知，将自动显示当前活动页面的标题链接。因此，当你浏览页面时，相关的标题链接将显示出来，以便更容易导航。

这样的标题是嵌套在活动页面下的，默认深度为`1`（这意味着所有`H2`标签都显示为标题，页面标题本身是`H1`）。

您可以使用`themeConfig.sidebarDepth`选项修改此行为，以显示更深层次的标题链接嵌套。例如，如果将嵌套深度更改为`0`，则所有标题链接都将被隐藏，只有`H1`值（页面标题）会显示。类似地，将深度更改为`2`将意味着所有嵌套在`H2`标题链接下的`H3`标题也会显示，依此类推。

您可以在 front matter 中指定相关深度。例如，要显示`H1`页面标题，`H2`标题链接以及`H3`和`H4`链接，您将指定深度为`3`，如下所示：

```js
 ---
 sidebarDepth: 3
 ---
```

# 活动标题链接

当用户向下滚动页面时，侧边栏的活动链接会自动更新。这是几乎所有静态站点生成器的默认行为，它们倾向于作为单页面应用程序工作。这样一来，根本不需要刷新或重新加载整个页面。

但是，如果需要的话，您可以通过`themeConfig.activeHeaderLinks`选项来禁用此行为，如下所示：

```js
module.exports = {
 themeConfig: {
 activeHeaderLinks: false, // Default is true
 }
 }
```

请注意，这意味着在浏览时不会突出显示活动标题链接，这可能会影响站点的整体用户体验。但是，这也可能会略微提高页面速度，因为相应的脚本将不再加载。

# 组织侧边栏

您可以选择将侧边栏组织或分成多个组。这是通过对象的帮助实现的。

VuePress 中的侧边栏组默认是可折叠的。但是，您也可以将可折叠选项指定为 false。创建侧边栏组的语法如下：

```js
// goes in .vuepress/config.js
 module.exports = {
  themeConfig: {
    sidebar: [
      {
        title: 'A Group',
        children: [
          '/'
        ]
      },
      {
        title: 'B Group',
        children: [ /* ... */ ]
      }
    ]
  }
 }
```

这个语法很简单。我们只是用给定的标题创建链接组，然后将页面和链接作为每个组的子项添加。

# 使用多个侧边栏

确实，您可以为内容的不同部分创建多个侧边栏。

但是，要使其正常工作，您首先需要相应地组织您的内容。一个好主意是将页面组织成目录和子目录。

例如，考虑以下目录结构，其中页面按目录组织：

现在，要创建多个侧边栏，我们需要将以下内容添加到我们的`config.js`文件中：

```js
// goes in .vuepress/config.js
 module.exports = {
  themeConfig: {
    sidebar: {
      '/testx/': [
        '', /* /testx/readme.md */
        'one', /* /testx/one.md */
        ],

      '/testxy/': [
        '', /* /testxy/readme.md */
       'two' /* /testxy/two.md */
      ],

      // fallback
      '/': [
        '',   /* readme.md at / */
        'about'   /* /about.md */
      ]
    }
  }
 }
```

上述配置将为每个部分声明侧边栏。请注意，强烈建议仅在最后声明 fallback 选项，因为 VuePress 按照从上到下的顺序读取侧边栏配置；也就是说，按照声明的时间顺序。

# 为单个页面添加侧边栏

您可以生成包含特定页面的标题链接的自定义迷你侧边栏-通常是当前活动页面。

要实现这一点，您首先需要在相关页面的 front matter 中指定如下：

```js
---
 sidebar: auto
 ---
```

您也可以在全局范围内重复这个过程。在这种情况下，您可以修改您的`config.js`文件如下：

```js
// goes in .vuepress/config.js
 module.exports = {
  themeConfig: {
    sidebar: 'auto'
  }
 }
```

# 如何禁用侧边栏？

如果您希望在特定页面上禁用侧边栏，可以在 front matter 中指定。这将使页面呈现全宽外观，并且可以按如下方式完成：

```js
---
 sidebar: false
 ---
```

# 对于 GitHub 用户

考虑到 VuePress 在其自己的 GitHub 存储库上得到积极维护，并且许多 Vue.js 用户倾向于偏爱 GitHub，VuePress 自然会加载一些对 GitHub 的本地支持。

# 同步到 GitHub 存储库

您可以通过简单指定存储库名称来轻松地向您的 GitHub 存储库添加编辑链接和更新。

然而，如果您不是 GitHub 用户，依赖于其他服务，如 BitBucket 或 GitLab，您仍然无需担心。在这种情况下，您可以在`config.js`文件中简单地提供存储库的完整 URL（正如我们将在即将介绍的语法中看到的），您会发现 VuePress 可以从中提取相关信息。

因此，总结一下：

+   GitHub 用户只需要告诉 VuePress 要使用哪个存储库

+   GitLab 和 BitBucket（或其他基于 Git 的平台用户）需要指定其存储库的完整 URL

要启用此特定功能，您只需要以下代码片段。请特别注意此代码中的注释，因为您需要根据需要更改它以使其正常工作。另外，根据您组织存储库的方式，您可能不需要大部分此代码（例如，如果您的文档不在不同的存储库中，您可以安全地从以下代码中省略该行）：

```js
// goes in .vuepress/config.js
 module.exports = {
  themeConfig: {
    // GitHub by default, provide full URLs for others.
    repo: 'repo-address-here',
    // Customize the header label that is shown in menu
    repoLabel: 'Contribute!',

    // Further options related to “Edit on Git” link

    // if docs are maintained in a separate repo:
    docsRepo: 'docs-repo-here-full-address',
    // if docs are in a sub-directory of main repo:
    docsDir: 'docs-directory-here',
    // if you do not want to use the Master branch on Git:
    docsBranch: 'master-or-branch-name',
    // do you want people to edit your docs? Boolean value
    editLinks: true,
    // modify the “edit this page” link that is shown
    editLinkText: 'Help me edit this page!'
  }
 }
```

在上面的代码片段中，您需要指定您的存储库地址，以及文档存储库（或您在 VuePress 上托管的任何文学作品）的地址，如果有的话。然后，您可以调整链接的外观，并选择是否希望其他人编辑或为您的存储库做出贡献。

# 如何在某些页面上隐藏编辑链接？

通常，您希望开源用户为项目或站点上的所有页面做出贡献，但可能有某些页面您希望免受编辑。这在涉及许可、法律条款、版权所有者等页面的情况下尤为重要。

在这种情况下，您只需要使用给定页面的前置内容，并关闭`editLink`属性，如下所示：

```js
 ---
 editLink: false
 ---
```

# 来自 GitHub 的时间戳

在使用 GitHub 存储库时，开发人员通常会展示或显示*上次更新时间*时间戳，以便跟踪进度，并通知用户所说的项目实际上正在积极开发中。

此时间戳来自 Git 提交，并在给定页面进行第一次提交时显示。之后，每当进行新的提交时，它都会更新。

![](img/9fe6c5a0-d25c-4394-bf3b-ff662faab38e.png)

在 VuePress 中，默认情况下是关闭的。但您可以选择打开它，并将其显示为上次更新的显示。

您可以使用`themeConfig.lastUpdated`选项，该选项将提取每个页面的最后一个 Git 提交的时间戳，并在页面底部显示它。

语法如下。请注意，时间戳是一个字符串值：

```js
module.exports = {
  themeConfig: {
    lastUpdated: 'Last Updated', // timestamp at bottom of page
  }
 }
```

这就是在为 VuePress 站点使用 GitHub 存储库时的全部内容。

现在，由于我们正在谈论主题开发，我们需要转向使网页显示其外观的内容。

是的，我们在谈论层叠样式表，或者 CSS。

# VuePress 主题开发中的 CSS

此时，我们将看看在 VuePress 中可以使用哪些自定义 CSS 覆盖和更改。

请注意，您需要对 CSS 有一定的了解，以执行本节中建议的任何调整。

# 页面的自定义类

往往，您可能需要为 VuePress 站点上的特定页面添加自定义 CSS。在这种情况下，您需要添加一个对于给定页面是唯一的自定义类。

到目前为止，您可能已经意识到需要使用 YAML 前置内容声明特定页面的内容。

因此，我们首先需要在 front matter 中的主题容器`div`中添加唯一的页面类：

```js
 ---
 pageClass: custom-page-class-name
 ---
```

之后，我们可以安全地为特定页面编写自定义 CSS，如下所示：

```js
/* this will NOT go to .vuepress/config.js */

 .theme-container.custom-page-class-name {
  /* page-specific custom CSS comes here */
 }
```

但是一个重要的问题是：我们在哪里添加主题容器并编写我们自定义的 CSS 呢？当然不是在 Markdown 文件中。

# CSS 覆盖文件结构

第一步是在您的`.vuepress/`目录中创建一个`override.styl`文件。这里的一般想法是在这里添加站点范围的常量覆盖，例如颜色，文本颜色等。

例如，要使整个文本颜色为黑色，您可以添加以下内容：

```js
$textColor = #000000
```

您可以使用普通的 CSS 语法进行编写。但如果您愿意，使用 Stylus 也是一个好主意。

Stylus 是一种 CSS 预处理器，以使在处理 CSS 时更容易阅读和编写而闻名。它支持多次迭代，嵌套运算符，并且不需要冒号，大括号和其他语法装饰。在[`stylus-lang.com/`](http://stylus-lang.com/)了解更多关于 Stylus 的信息。

然而，`override.styl`文件仅适用于 Stylus 常量。那么你自己的额外样式呢？

为此，您需要在`.vuepress/`目录中添加另一个`style.styl`文件。在这个文件中，您可以编写任何您希望的 CSS 样式。再次，您可以使用 Stylus 或普通 CSS 语法。

如果您不太喜欢 Stylus，请记住您也可以使用其他 CSS 预处理器，如 LESS 或 SASS。还记得您在第三章中学到的构建流水线配置值，《VuePress 开发-初步步骤》吗？您只需要根据您选择的 CSS 预处理器使用正确的值。

在任何时候，您都可以转到配置值列表（构建流水线）并参考这些内容；这就是为什么本书在第三章中提前很好地涵盖了这一点，《VuePress 开发-初步步骤》。

# 向后兼容性

这些信息对于较新版本的 VuePress 不再相关。但是，如果您使用的是较旧版本的 VuePress，您甚至可以只使用`override.styl`文件而不是第二个`style.styl`文件。考虑以下示例：

```js
// .vuepress/override.styl
 $textColor = black // stylus constants override.

 #my-style {} // extra CSS styles in the same file.
```

虽然这在实践中对于较旧版本的 VuePress 有效，但即使是这样，它也是双倍的处理。这是因为 Stylus 要求首先编译所有 Stylus 常量覆盖，然后再编译所有用户指定的额外 CSS。

如果你要在`override.styl`文件中编写你的样式，这意味着同一个文件在被导入一次后会被多次复制。为了避免这种情况，从版本 0.12.0 开始，VuePress 将 CSS 处理分成了`override.styl`和`style.styl`文件，如前所述。这是添加自定义 CSS 样式的正确方式。

学习更多关于老版本的 VuePress 是如何处理相同文件的，可以查看这个 GitHub 问题，链接在[`github.com/vuejs/vuepress/issues/637`](https://github.com/vuejs/vuepress/issues/637)。

# 弹出主题

有时，并不需要从头开始创建一个自定义主题。原因有很多：

+   这需要很长时间，特别是在像 VuePress 这样的平台上，它仍处于起步阶段。

+   编写自定义主题需要对 JavaScript 有广泛的了解，而且可能会破坏使用静态网站生成器的初衷，即节省时间和精力。

+   在错误修复、定期更新等方面，维护自定义主题并不总是容易的。

话虽如此，你可能也不喜欢使用默认主题，因为它不能使你的网站脱颖而出。在这种情况下，你可以选择使用自定义 CSS 样式和其他措施，如前所述，即使使用默认主题，也可以使你的网站具有独特的外观。

但如果你希望对默认主题进行重大更改呢？一旦你更新 VuePress，对默认主题源代码所做的任何更改都将丢失。

在这些情况下，你可以复制默认主题，然后编辑复制品，进行更改，就像它是一个自定义主题一样。这就是弹出主题。

要弹出你的主题，你需要输入以下命令：

```js
vuepress eject [target-directory-here]
```

这个命令将拉取默认主题的源代码，并将其复制到`.vuepress/theme`子目录中。这个特定的目录将作为你自定义主题的新家，你可以在其中进行更改。

请注意，一旦你弹出你的主题，你将负责它的维护和错误修复。你仍然可以根据需要更新 VuePress，但你将不再获得与默认主题相关的错误修复或功能更新。

确保只有当你知道自己在做什么，并且对 Vue.js 有很好的了解时，才弹出你的 VuePress 主题。当然，这已经超出了这个快速入门指南的范围。

但一旦你确实弹出了你的主题，就是时候转向自定义主题开发了！

# VuePress 中的自定义主题开发

为了创建自定义 VuePress 主题，需要使用 Vue 单文件组件。这意味着您需要对 Vue.js 有功能性的了解才能创建 VuePress 主题。如果您发现自己在时间或技能方面缺乏，建议您定制默认的 VuePress 主题，就像本章前面讨论的那样。

说到这里，创建自定义 VuePress 主题的第一步是在 VuePress 的`根`文件夹中创建一个`/theme/`目录。

然后，在`.vuepress/theme/`目录中创建一个`layout.vue`文件。

然后，您可以像为自定义 Vue.js 应用程序一样进行自定义主题创建。布局选项和设置完全取决于您。

您还可以选择从 npm 依赖项中使用自定义 VuePress 主题。在这种情况下，您需要在`config.js`文件中使用主题配置选项。只需将以下代码添加到您的`.vuepress/config.js`文件中：

```js
module.exports = {
  theme: 'your-theme-name'
}
```

请注意，您需要事先声明并发布主题到 npm，以便将其用作依赖项。

# 定制默认主题

我们之前讨论过的默认 VuePress 主题可以定制为自定义主题。然而，在这种情况下，您需要弹出默认主题，就像本章前面提到的那样。

在那之后，您可以开始自己调整自定义主题——请注意，一旦您弹出主题，将不会自动复制默认主题的任何未来更新或错误修复。但您可以像往常一样更新 VuePress。

# 处理元数据

每个 VuePress 主题都需要元数据来正确显示站点详细信息，正如我们已经学到的那样，最好使用布局组件来完成这一点。

在这个阶段，您应该熟悉两个 Vue.js 属性，即`$page`和`$site`。这两个属性都被注入到每个组件中。

因此，每次调用布局组件时，`$site`和`$page`属性也会被调用。因此，将您站点的元数据放置在`this.$site`和`this.$page`中是一个合乎逻辑的想法。

让我们先看看`$site`。这里是一个示例值：

```js
{
  "title": "Site-Title",
  "description": "Site description comes here",
  "base": "/",
  "pages": [
    {
      "path": "/",
      "title": "Title",
      "frontmatter": { }
    },
    ...
  ]
}
```

前面的语法非常清楚地显示了`$site`保存了与整个站点元数据相关的值。

对于`$page`，我们有以下语法：

```js
{
  "path": "/current/path/to/page",
  "title": "Page Title",
  "headers": [/* ... */],
  "frontmatter": { }
}
```

可以看到，`$page`保存了特定页面的元数据。

对于$site 的数值，大部分细节都是从`.vuepress/config.js`文件中复制而来。但是 pages 数值包含一个数组，其中包括每个页面的元数据对象。这些元数据对象通常是从相关页面的前置内容中提取的，或者是推断出来的，比如页面标题，可以在前置内容中指定，也可以从标题标签中获取。

$page 的数值经常可以用来构建主题的定制行为。您也可以根据需要对页面进行排序。这与定制 Vue.js 应用程序的顺序非常相似。

# 其他可能的增强

以下是一些其他潜在的指针，您可以在自定义 VuePress 主题时使用。

请注意，您需要对 Vue.js 工作流程有一定的了解，才能实现这些增强。

# 应用级别的增强

在您的主题根目录中，您可以创建一个`enhanceApp.js`文件来处理应用级别的增强。

这个特定的文件将导出一个钩子函数。在这个函数中，您应该接收一个包含特定应用程序细节或数据的对象数值。

然后，您可以非常容易地利用这个钩子来注册自定义插件，添加扩展，注册全局组件，定制其他功能等。

这个钩子函数的一般语法如下：

```js
export default ({
  Vue, // the current Vue.js version 
  options, // specify the options for Vue instance
  router, // the router for our app
  siteData // site metadata
}) => {
  // enter custom enhancements here 
}
```

听起来很混乱？只有当您是经验丰富的 Vue.js 开发人员，并且希望使用 VuePress 构建定制内容时，所有这些才有用。如果您只是想快速建立一个简单的站点，您可以安全地忽略这些细节。

这些都是您开始自定义 VuePress 主题开发所需的所有细节。再次强调，VuePress 中的主题需要对 Vue.js 有相当的了解，最好在进入生产级开发之前先进行一些实验。

# 总结

这标志着与 VuePress 主题相关的这一特定章节的结束。

在这一章中，我们已经涵盖了相当多的内容。现在我们对默认的 VuePress 主题有了很好的理解，知道如何调整它，如何改变标题链接、导航栏、侧边栏等外观。

此外，对于基于 GitHub 项目构建文档站点的情况，我们还学习了如何将站点与我们的 GitHub 存储库集成。除此之外，我们还学习了如何向我们的主题添加 CSS 样式来改变站点的外观。

不仅如此，我们现在知道如何弹出 VuePress 的默认主题并开始创建我们自己的自定义主题，当然，这也需要 Vue.js 应用程序的知识。

目前，您在本地主机上已经建立了一个活跃的网站，这是您在第四章中创建的*在 VuePress 中创建网站*。在该网站上尝试主题的更改和调整是一个好主意。本章中有所有的语法和代码示例，但为了清晰起见，我已经避免让它们特定于用例。因此，您可以修改这些通用的代码示例以适应您的生产和网站特定的需求。

结合我们迄今为止的进展，现在您应该能够安装 VuePress，调整`config.js`文件以修改主题和其他项目，以及在 Markdown 文件中创建内容并上传这些内容。

现在剩下的就是让网站上线，让全世界看到它！

但在此之前，我们需要掌握另一个学习步骤——本地化和国际化。在这方面，我们将讨论 VuePress 对多种语言的支持，以及我们何时以及如何利用这一点。下一章将涉及到这一点，之后我们将把注意力转向让我们的网站在互联网上发布。
