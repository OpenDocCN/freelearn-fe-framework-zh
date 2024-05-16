# 第三章：VuePress 开发 - 初步步骤

在第二章 *开始使用 VuePress* 中，我们介绍了 VuePress 的安装和设置。在这一点上，你已经学会了如何安装 VuePress，它的系统要求是什么，以及如何设置好 VuePress 以准备实际工作。

然而，安装 VuePress 只是第一步。为了构建一个功能完善的项目，并充分利用它，你需要学会如何更多地使用 VuePress。

这一章将帮助你实现这一点。

在这一章中，你将学习以下内容：

+   VuePress 开发基础

+   VuePress 的基本配置值

+   VuePress 的浏览器配置值

+   VuePress 的构建流程配置值

+   VuePress 中的资产处理

+   处理图片、公共文件和 URL

# VuePress 开发 - 入门

在这一章中，你将学习关于在 VuePress 中处理资产和处理文件和 URL 的概念。现在，由于 VuePress 是一个静态网站生成器，事情往往与你在 WordPress 等数据库驱动的内容管理系统中找到的有些不同。

考虑到在第四章 *在 VuePress 中创建网站* 中，我们将实际上使用 VuePress 设置一个演示网站，因此你需要对 VuePress 中如何处理文件和 URL 有一个很好的理解。第四章 *在 VuePress 中创建网站* 将帮助你规划网站或博客的完美结构。

# VuePress 配置值

在继续之前，我们需要花一些时间确保我们理解 VuePress 通常支持的配置的性质。

# VuePress 的基本配置值

首先，我们需要熟悉 VuePress 中使用的基本配置值。这个列表并不完整，你应该参考 VuePress 文档获取完整的列表，可能会跨越多个页面。

为了清晰起见，避免混淆，我们将在讨论自定义主题的章节中讨论与主题相关的配置。同样，我们将在有关 VuePress 中的 Markdown 的章节中讨论与 Markdown 相关的配置值。

# 标题

此值显示 VuePress 站点的标题。默认情况下，它被添加到站点上的所有页面，并显示在导航栏中，但您可以更改此功能：

```js
Type: string
Default: undefined
```

# 描述

这显示了站点的元描述。因此，在页面 HTML 中，它在`<meta>`标签内呈现：

```js
Type: string
Default: undefined
```

# 头部

这包括在 HTML 中呈现页面时在`<head>`标签内呈现的附加数据。通常，它采用在`[`和`]`方括号内封装的内容的合并形式。

基本语法如下：

```js
module
    .exports = {
        head: [
            [ ... ]
        ]
    }
```

自然，头部配置是数组类型，其一般默认形式为`[ ]`（空数组）。

# ga

此配置实体帮助您将 VuePress 站点与 Google Analytics 集成。如果您在 Web 开发领域活跃，您可能已经意识到 Google Analytics 可以为您的站点提供大量资源。因此，您只需将您的 Google Analytics ID 传递给此配置值，就可以了。

`ga`是数组类型，默认值为未定义，因为您需要将您的 Google Analytics ID 传递给它。

# 区域设置

此特定配置变量支持国际化和本地化；也就是说，与将站点翻译成其他语言的兼容性。由于我们已经有了一个专门讨论 VuePress 中国际化和本地化处理的章节，我们很快将详细讨论这个配置值。

默认值为未定义，接受的类型是此处所示格式的字符串对象：

```js
{ [path: string]: Object }
```

# shouldPrefetch

有时，一些文件倾向于附有预加载资源提示。`shouldPrefetch`函数处理这些限制和提示。

`shouldPrefetch`是函数类型，默认值为`( ) => true`格式。

对于我们所说的限制和提示感到困惑？嗯，Vue.js 有一种称为`<link rel="preload">`的资源提示。默认情况下，几乎所有要异步加载的资产都会被预取，以加快页面加载时间和性能。但是，如果您在带宽有限的较弱服务器上呢？预取所有内容肯定会消耗大量带宽。

在这种情况下，您可以使用`shouldPrefetch`函数来控制哪些文件具有预取提示，哪些文件没有。通过这种选择性选项，您可以避免在服务器上消耗大量带宽。

# serviceWorker

这个相当有趣。在继续之前，你需要确保满足一定的条件才能使用`serviceWorker`配置实体：

+   `serviceWorker`只能通过 HTTPS 注册，所以你需要确保你的网站使用 SSL 证书

+   `serviceWorker`仅在生产模式下工作；也就是说，在实际的网站上

现在，serviceWorker 是一个默认为 false 的布尔类型。但是当你将它设置为 true 时，VuePress 将自动生成并注册一个 service worker 脚本。这可以用来帮助缓存内容以供离线使用。

什么是 service worker？在**JavaScript**（**JS**）中，service worker 是一种特殊类型的脚本，它是在后台由 Web 浏览器动态生成并运行的。它的运行方式与实际的网页加载不同。这里的目标是执行一些非常重要的后台功能，但不需要实际的网页或用户配置。

现在使用 service worker 脚本的情况的例子包括浏览器中的推送通知，浏览器中的离线缓存，后台数据同步以便在连接失败时更容易恢复等。

当使用自定义主题时，正如你将在后面的章节中看到的，`serviceWorker`配置值有时也会发出以下事件：

+   `sw-ready`

+   ``sw-cached``

+   `sw-updated`

+   `sw-offline`

+   `sw-error`

这里的`serviceWorker`选项只会处理 JS `serviceWorker`脚本。这意味着它不会自行生成单页面 Web 应用程序；它取决于你是否利用`serviceWorker`脚本来满足项目的需求。

再次强调，确保只有在 SSL 激活时将`serviceWorker`设置为 true，因为`serviceWorker`脚本在非 HTTPS URL 上不起作用。

# dest

这个配置变量指定了 VuePress 最终构建过程的输出目录。默认路径是`.vuepress/dist`，但你可以通过指定任何其他字符串类型的路径来更改它。

# 端口

这是用来指定连接到开发服务器的端口值。除了 localhost，你的网络托管提供商应该能够更准确地告诉你可能希望在这里使用哪个端口。

默认值是`8080`，你只能使用整数类型的值，因为端口值只能指定为整数。

# 主机

此术语指定了您可以用来连接服务器的主机地址。再次强调，您的网络托管提供商或服务器管理员应该有关于此字段的准确细节。

它接受字符串类型的值，默认设置为`'0.0.0.0'`。

# 与浏览器兼容性相关的配置值

VuePress 目前只有一个与主要浏览器兼容性相关的配置值，您应该知道。

# 常青

此配置值是`boolean`类型，默认设置为 false。

但是，如果您希望您的 VuePress 网站仅针对常青网页浏览器，可以将其设置为 true。设置为 true 时，它将禁用对 ES5 转译和 polyfill 的支持。这意味着您的网站将无法与较旧版本的**Internet Explorer**（**IE**）很好地配合。

优势是什么？构建时间将大大缩短，事情将更快地运行。此外，您的网站的最终构建大小将更小。

什么是常青网页浏览器？常青网页浏览器是指自动更新到未来版本的浏览器。因此，用户无需下载新版本并从头安装。例如，如果您正在运行 IE 8 并希望安装 IE 9，则需要下载可安装文件然后运行它。另一方面，现代浏览器的版本，如 Chrome 或 Firefox，可以在发布新更新时自动更新，而无需再次运行安装程序。

这种滚动式的网页浏览器更新模式被称为“常青网页浏览器”。大多数较新的网页浏览器是常青的，而较旧的版本则不是。

# 与构建流水线相关的配置值

现在，让我们把注意力转向与构建流水线相关的配置值。在静态内容管理系统中，如 VuePress 和 Jekyll 中，构建流水线指的是生产线；也就是说，代码从开发到生产的流程。

在这一点上，您对 JS 和 CSS 的知识将会派上用场。这些与构建流水线相关的配置值大多围绕着 webpack。由于深入研究 JS 和 CSS 加载器超出了这个快速入门指南的范围，我们将只引用相关项目的 GitHub 页面，以便您可以根据需要学习更多。

# postcss

`postcss` 可以用来指定 webpack 中 PostCSS loader 的自定义选项。有关该加载程序的详细信息，请参阅：[`github.com/postcss/postcss-loader`](https://github.com/postcss/postcss-loader)。

一旦您指定了 `postcss` 值，您将覆盖其中的任何其他 autoprefix 值。它只接受对象类型的值，其默认语法如下：

```js
{ plugins: [require('autoprefixer')] }
```

# scss

`scss` 提供了用于将 SASS 转换为 CSS 的 SCSS loader 的选项。您可以在[`github.com/webpack-contrib/sass-loader`](https://github.com/webpack-contrib/sass-loader)了解更多关于此加载程序的信息。

此配置实体接受对象类型的值，其默认格式为 `{ }`。

# stylus

stylus 配置实体提供了调整 `webpack` 中 Stylus loader 的选项。默认情况下，它接受对象类型的值，语法如下：

```js
{ preferPathResolver: 'webpack' }
```

您可以在[`github.com/shama/stylus-loader`](https://github.com/shama/stylus-loader)了解更多关于 stylus loader 的信息。

# less

此术语指定了可以用于将 LESS 编译为 CSS 的 LESS loader 的选项。有关详细指南，请参阅[`github.com/webpack-contrib/less-loader`](https://github.com/webpack-contrib/less-loader)。

它接受对象类型的值，其默认格式为 `{ }`。

# sass

这提供了可以将 SASS 编译为 CSS 并加载 `*.sass` 文件的 SASS loader 的选项。有关此加载程序的其他详细信息，请参阅[`github.com/webpack-contrib/sass-loader`](https://github.com/webpack-contrib/sass-loader)。

它接受对象类型的值，其默认格式如下：

```js
{ indentedSyntax: true }
```

# chainWebpack

`chainWebpack` 是一个具有未定义默认值的函数。

它可以帮助您通过使用 Mozilla 的 Webpack Chain API 修改内部的 `webpack` 配置。这个特定的 API 可以生成并简化 webpack 的定制和配置过程。您可以在 GitHub 上了解更多信息：[`github.com/mozilla-neutrino/webpack-chain`](https://github.com/mozilla-neutrino/webpack-chain)。

以下是一些示例代码，显示了在 VuePress 中使用 `chainWebpack` 的语法：

```js
module.exports = {
    chainWebpack: (config, isServer) => {
    // provide instance and config details here
    }
}
```

# configureWebpack

正如其名称所示，这个特定的配置实体允许您修改 `webpack` 的内部配置。因此，它主要适用于已经熟悉 `webpack` 及其用法的人。

它使用了`webpack-merge`，详细信息可以在[`github.com/survivejs/webpack-merge`](https://github.com/survivejs/webpack-merge)找到。

通常，`webpack-merge`为你提供了一个合并函数，它简单地连接数组并合并对象。这将导致创建新的对象，你可以使用它们。

在 VuePress 中，`configureWebpack`的默认值为 undefined。但是，你应该注意它可以是对象类型或函数类型之一：

+   当值为对象类型时，该值通过使用`webpack-merge`合并到最终配置中，如前所述。

+   当值为函数类型时，它接收与第一个参数相同的配置细节（第二个参数通常是一个`isServer`标志）。然后，你可以选择直接修改作为参数传递的配置，或者将其详细信息作为一个可以合并的对象返回。

当使用`configureWebpack`作为函数类型时，语法如下：

```js
module.exports = {
    configureWebpack: (configArgs, isServer) => {
    if (!isServer) {
        // modify or work with the configArgs here
        }
    }
}
```

所以，你现在已经了解了 VuePress 中所有必需的配置实体的基本细节。再次强调，我们已经省略了与主题配置相关的配置术语，以及 Markdown，这些将在后续章节中讨论。

然而，现在你知道可以期待什么术语以及在哪里——例如，当处理要用作站点图标的图像资产时，你应该在头部配置中调用或引用它，依此类推。

现在是时候学习一些关于 VuePress 中 URL 和资产处理的理论了。然后，你将准备好开发你的第一个 VuePress 站点。

# 资产处理是什么？

当我们说*资产处理*时，我们指的是什么？

在开发领域，特别是在网络开发中，资产是指项目使用或处理的任何内容。这可能包括但不限于以下内容：

+   图片

+   视频

+   其他媒体文件

+   文件，比如 PDF

自然地，为了处理这些数据文件和资产，CMS 需要实施专门的措施和协议。换句话说，你不能把 JPG 文件当作代码文件来处理——它并不能被内容管理系统完全读取，但系统必须意识到它是图像类型的媒体资产，并应该相应地处理。

在 VuePress 中，方法论相当简单。首先，我们必须了解 VuePress 中 URL 的配置方式。然后，我们可以将注意力转向公开可见的文件，如图片和图形。

# VuePress 中的 URL

通常，您希望将实时的 VuePress 站点保留在以下两个位置之一：

+   在根 URL

+   在非根 URL

对于非根 URL，您需要在您的`config.js`中指定`base`选项-是的，就是我们在第二章中讨论的同一个`config.js`文件，它位于`.vuepress`目录中。

假设我们在[mysite.example.com](http://mysite.example.com)上有一个项目，并希望在名为`vuep`的目录中部署 VuePress。因此，我们的 VuePress 实例将在`mysite.example.com/vuep/`上运行。

在这种情况下，`base`值应设置为`/vuep/`。

请注意，`base`应以斜杠开头和结尾；即`/`。

现在，一旦您指定了`base`选项，它将自动添加到所有资产 URL 之前。这意味着您在`.vuepress/config.js`文件中指定的任何资产 URL 都将在其前面添加`base`值。

目前，理论部分就是您需要了解的全部内容。此方面的实际用法将在第四章中进行，即*使用 VuePress 创建站点*，在那里我们将使用 VuePress 设置站点。

# 在 VuePress 中处理公共文件

有多种方式可以访问 VuePress 中的文件。重申一下，我们指的是媒体元素和其他资产。

您可以通过以下方式来做：

+   在您的 Markdown 组件中引用文件

+   引用主题组件中的文件

+   将文件视为公共文件

我们将在随后的章节中介绍前两个步骤。

假设您有一个需要公开查看的图像文件。通过公开查看，我们指的是它应该对站点的所有访问者可见，而不仅仅是管理员，例如站点的 favicon。

您只需要将所述图像文件放在`.vuepress/public`目录中。此后，该文件将位于生成目录的根目录，并且可以公开访问。

然后，您可以参考或指定如下示例中的内容：

```js
mysite.example.com/vuep/favfile.png
```

在这里，`favfile.png`是您放置在`/public/`目录中的文件。

简单，不是吗？

那么，如果您希望重新定位 VuePress 站点怎么办？比如，更改`/vuep`位置？您可能需要更改以这种方式制作的所有 URL 的引用。

为了防止这种情况发生，VuePress 提供了一个相当简单的解决方案。

您可以使用内置的`$withBase`助手（在 VuePress 中原生构建，无需扩展），它将自动生成正确的路径，您无需担心指定的绝对路径可能会在以后更改。

以下是如何使用它的示例：

```js
<img :src="$withBase('/favfile.png')" alt="My Fancy Image File">
```

这段代码将确保引用保持在指定的文件上，而不管您在 VuePress 中使用的基本 URL 是什么。您可以使用它来处理您的公共文件，甚至在主题组件和 Markdown 文件中使用它。

# 相对 URL 呢？

在 VuePress 中，与现代网络上的任何其他内容管理系统一样，最好和建议通过相对 URL 引用所有资产元素。例如，`./sample.jpg`可以在任何文件模板中使用。在这种情况下，目标图像将首先使用 URL-loader 和 file-loader 进行处理，然后复制到最终静态构建所需的位置。

在使用`webpack`模块（以及在本章前面提到的构建管道配置部分中提到的配置实体）时，您可以使用`~`前缀。这在许多 JS 框架中都是标准的，如果您有任何这些 JS 框架的经验，您可能已经知道它。例如，`~some-directory/sample.jpg`是使用`webpack`别名引用文件的一种好方法。

在本章的前面，我们谈到了用于配置的`configureWebpack`选项。您可以在`.vuepress/config.js`文件中使用此选项轻松配置您的`webpack`别名。

这是一个这样的示例：

```js
module.exports = {
    configureWebpack: {
        resolve: {
          alias: {
            '@alias': 'custom/path/to/directory/relative-url'
          }
        }
      }
    }
```

在前面的例子中，您只是利用`configureWebpack`来确保所说的别名指向您为项目中要使用的所需资产或元素指定的自定义路径。与其他配置工具一起使用时，它可以帮助您轻松获取资产。

这就是 VuePress 中的资产处理。在这一点上，您已经准备好进行实际的站点开发了。

# 摘要

在本章中，您已经了解了 VuePress 中大多数基本和高级配置实体。您了解了基本术语，以及在哪里可能调用值，以及哪个函数可能返回哪个值。

显然，这将假定您熟悉基本的 JS 对象、函数和调用。

此外，您还了解了 VuePress 中的资产处理基础知识，URL 是如何相对处理的，媒体元素如何被引用等等。

然而，目前看起来可能会有信息过载，因为你已经学到了相当多的理论，但到目前为止还没有将其编译成生产或开发。但就像任何其他建立在现有语言之上并提供丰富可配置选项框架的软件或产品一样，VuePress 也需要被视为这样的产品。

在《第四章》*在 VuePress 中创建站点*中，我们将构建一个实时的 VuePress 网站。您可以使用任何您选择的代码编辑器或 IDE，以及任何平台。请注意，我们将在后面的章节中专注于部署，因此建议为下一章设置一个本地站点。您已经在上一章中学习了如何安装 VuePress。

因此，在阅读《第四章》*在 VuePress 中创建站点*时，有必要在需要时参考本章。这将帮助你了解哪些配置选项可以在哪里使用，以及如何避免一些常见的问题。因此，当我们在下一章中构建 VuePress 站点时遇到特定的配置时，我们将会实际学习它。

重点将放在处理媒体文件（如图像）上，因为没有图像，你实际上无法构建一个真正的实时站点！此外，我们还将涵盖一些真实世界的概念，比如与 Google Analytics 等服务的集成（请注意，我们已经在本章的*基本配置*部分中学习了配置引用的语法；请参见本章前面的`ga`）。这将帮助您构建一个真正可用而不仅仅是花哨示例的 VuePress 站点。

因此，确保您已经掌握并对本章涵盖的术语有了相当好的理解。在那之后，让我们继续前进，构建一个 VuePress 站点！
