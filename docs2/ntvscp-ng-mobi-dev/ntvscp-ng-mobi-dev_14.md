# 第十四章：使用 webpack 打包进行部署准备

我们希望将我们的应用部署到两个领先的移动应用商店，即 Apple App Store 和 Google Play；然而，我们需要做一些准备，以便我们的应用可以分发。

为了确保我们使用最小的 JavaScript 大小，并辅以 Angular 的 AoT 编译器，以帮助我们的应用尽可能快地执行，我们将使用 webpack 来打包所有内容。值得注意的是，webpack 不是创建可分发 NativeScript 应用的必需品。然而，它提供了非常不错的优势，这应该使它成为任何人在分发他们的应用时的重要步骤。

在本章中，我们将涵盖以下主题：

+   为 NativeScript for Angular 项目安装 webpack

+   准备项目以使用 webpack 进行打包

+   解决各种 webpack 打包问题

+   自定义 webpack 插件编写指南，用于解决特定情况

# 使用 webpack 打包应用

如果没有 Sean Larkin，你可能永远不会听说 webpack。他对打包器社区的贡献和参与帮助 webpack 进入 Angular CLI，并使其成为许多事物的首选*bundler*。我们非常感激他在社区中的努力和善意。

# 准备使用 webpack

让我们看看 webpack 如何被利用来减少我们的 NativeScript for Angular 应用的打包大小，同时确保它在用户的移动设备上执行得最优。

让我们首先安装插件：

```js
npm install nativescript-dev-webpack --save-dev
```

这会自动创建一个`webpack.config.js`文件（位于项目根目录），预先配置了基本设置，这将使大多数应用能够合理地进一步发展。此外，它还会创建一个`tsconfig.aot.json`文件（同样位于项目根目录），因为 NativeScript 的 webpack 使用将在打包时使用 Angular 的 AoT 编译器。它还在`package.json`中添加了一些巧妙的 npm 脚本，以帮助我们处理所有我们想要的打包选项；考虑以下示例：

+   使用`npm run build-android-bundle`为 Android 构建

+   使用`npm run build-ios-bundle`为 iOS 构建

+   使用`npm run start-android-bundle`在 Android 上运行

+   使用`npm run start-ios-bundle`在 iOS 上运行

然而，在我们尝试这些新命令之前，我们需要对我们的应用进行一些审计。

我们应该首先确保所有 NativeScript 导入路径都以前缀`tns-core-modules/[module]`开头；考虑以下示例：

```js
BEFORE:
import { isIOS } from 'platform';
import { topmost } from 'ui/frame';
import * as app from 'application';

AFTER:
import { isIOS } from 'tns-core-modules/platform';
import { topmost } from 'tns-core-modules/ui/frame';
import * as app from 'tns-core-modules/application';
```

我们将遍历我们的应用并立即这样做。这对于开发和生产构建都适用。

你可能会想，“嘿！既然我们需要遍历整个代码库并在事后更改导入，为什么还要使用其他形式呢？”

极大的关注！实际上，有很多示例展示了方便的短形式导入路径，所以我们选择在整个章节中使用该路径构建应用程序，以演示它对开发来说效果良好，以帮助避免混淆，以防你在未来遇到这样的示例。此外，实际上在事后编辑它以准备 webpack 并不需要太多，但现在你知道了。

立即运行以下命令：

```js
npm run build-ios-bundle
```

我们可以看到以下错误——我已经列举了它们——我们将在下一节中按顺序提供解决方案：

1.  错误：在`/path/to/TNSStudio/app/modules/player/directives/slider.directive.d.ts`中意外值`SlimSliderDirective`，由模块`PlayerModule`在`/path/to/TNSStudio/app/modules/player/player.module.ts`中声明。请添加`@Pipe/@Directive/@Component`注解。

1.  错误：无法确定类`SlimSliderDirective`的模块，在`/path/to/TNSStudio/app/modules/player/directives/slider.directive.android.ts`！将`SlimSliderDirective`添加到`NgModule`中修复它。无法确定类`SlimSliderDirective`的模块，在`/path/to/TNSStudio/app/modules/player/directives/slider.directive.ios.ts`！将`SlimSliderDirective`添加到`NgModule`中修复它。

1.  错误：在静态解析符号值时遇到错误。调用函数`ModalDialogParams`，不支持函数调用。考虑用导出函数的引用替换该函数或 lambda，解析符号`RecorderModule`在`/path/to/TNSStudio/app/modules/recorder/recorder.module.ts`，解析符号`RecorderModule`在`/path/to/TNSStudio/app/modules/recorder/recorder.module.ts`。

1.  错误：入口模块未找到：错误：无法解析`./app.css`在`/path/to/TNSStudio/app`。

1.  错误：[copy-webpack-plugin]无法在`/path/to/TNSStudio/app/app.css`中找到`app.css`。

前三个错误纯粹与 Angular **Ahead of Time** （**AoT**）编译相关。最后两个纯粹与 webpack 配置相关。让我们逐一查看每个错误以及如何正确解决它。

# 解决方案 #1：意外值 'SlimSliderDirective...'

考虑前一小节中提到的第一个完整错误：

```js
ERROR in Unexpected value 'SlimSliderDirective in /path/to/TNSStudio/app/modules/player/directives/slider.directive.d.ts' declared by the module 'PlayerModule in /path/to/TNSStudio/app/modules/player/player.module.ts'. Please add a @Pipe/@Directive/@Component annotation.
```

解决前述错误的方案是安装一个额外的 webpack 插件：

```js
npm install nativescript-webpack-import-replace --save-dev
```

然后，打开`webpack.config.js`并按以下方式配置插件：

```js
function getPlugins(platform, env) {
    let plugins = [
      ...
      new ImportReplacePlugin({
          platform: platform,
          files: [
              'slider.directive'
          ]
      }),
      ...
```

这将找到`app/modules/players/directives/index.ts`中的`slider.directive`导入，并附加正确的目标平台后缀，因此 AoT 编译器将选择正确的目标平台实现文件。

在撰写本书时，该错误还没有解决方案，因此我们开发了 `nativescript-webpack-import-replace` 插件来解决这个问题。由于你可能会遇到需要通过插件提供一些额外 webpack 帮助的 webpack 捆绑情况，我们将分享我们如何开发插件来解决该错误的概述，以防你遇到其他可能需要创建插件的晦涩错误。

让我们先看看剩余的初始错误解决方案，然后我们将突出 webpack 插件开发。

# 解决方案 #2：无法确定类 SlimSliderDirective 的模块...

考虑到在 *准备使用 webpack* 部分提到的第二个完整错误：

```js
ERROR in Cannot determine the module for class SlimSliderDirective in /path/to/TNSStudio/app/modules/player/directives/slider.directive.android.ts! Add SlimSliderDirective to the NgModule to fix it.
Cannot determine the module for class SlimSliderDirective in /path/to/TNSStudio/app/modules/player/directives/slider.directive.ios.ts! Add SlimSliderDirective to the NgModule to fix it.
```

解决前面错误的方法是打开 `tsconfig.aot.json`，并做出以下更改：

```js
BEFORE:
  ...
  "exclude": [
    "node_modules",
    "platforms"
  ],

AFTER:
  ...
  "files": [
 "./app/main.ts"
 ]
```

由于 AoT 编译使用 `tsconfig.aot.json` 配置，我们希望针对编译的目标文件更加具体。由于 `./app/main.ts` 是我们启动应用的入口点，我们将针对该文件，并移除 `exclude` 块。

如果我们现在尝试捆绑，我们会解决我们看到的错误；然而，我们会看到以下 *新* 错误：

```js
ERROR in .. lazy
Module not found: Error: Can't resolve '/path/to/TNSStudio/app/modules/mixer/mixer.module.ngfactory.ts' in '/path/to/TNSStudio'
 @ .. lazy
 @ ../~/@angular/core/@angular/core.es5.js
 @ ./vendor.ts

ERROR in .. lazy
Module not found: Error: Can't resolve '/path/to/TNSStudio/app/modules/recorder/recorder.module.ngfactory.ts' in '/path/to/TNSStudio'
 @ .. lazy
 @ ../~/@angular/core/@angular/core.es5.js
 @ ./vendor.ts
```

这是因为我们针对 `./app/main.ts`，它分支到我们应用的所有其他导入文件，除了那些懒加载的模块。

解决前面错误的方法是在 `files` 部分添加懒加载模块路径：

```js
"files": [
  "./app/main.ts",
  "./app/modules/mixer/mixer.module.ts",
 "./app/modules/recorder/recorder.module.ts"
 ],
```

好吧，我们已经解决了 `lazy` 错误；然而，现在这揭示了几个 *新* 错误，如下所示：

```js
ERROR in /path/to/TNSStudio/app/modules/recorder/components/record.component.ts (128,19): Cannot find name 'CFRunLoopGetMain'.
ERROR in /path/to/TNSStudio/app/modules/recorder/components/record.component.ts (130,9): Cannot find name 'CFRunLoopPerformBlock'.
ERROR in /path/to/TNSStudio/app/modules/recorder/components/record.component.ts (130,40): Cannot find name 'kCFRunLoopDefaultMode'.
ERROR in /path/to/TNSStudio/app/modules/recorder/components/record.component.ts (131,9): Cannot find name 'CFRunLoopWakeUp'.
```

大概就在这个时候...

那个 funk soul 兄弟。

是的，你可能正在唱 Fatboy Slim 或即将失去理智，我们理解。有时使用 webpack 捆绑可能相当冒险。我们能提供的最好建议是保持耐心和勤奋，一次处理一个错误；我们几乎做到了。

解决前面错误的方法是包含 iOS 和 Android 平台声明，因为我们正在我们的应用中使用原生 API：

```js
"files": [
  "./app/main.ts",
  "./app/modules/mixer/mixer.module.ts",
  "./app/modules/recorder/recorder.module.ts",
  "./node_modules/tns-platform-declarations/ios.d.ts",
 "./node_modules/tns-platform-declarations/android.d.ts"
]
```

哈喽，我们现在已经完全解决了第二个问题。让我们继续下一个问题。

# 解决方案 #3：在静态解析符号值时遇到错误

考虑到在 *准备使用 webpack* 部分提到的第三个完整错误：

```js
ERROR in Error encountered resolving symbol values statically. Calling function 'ModalDialogParams', function calls are not supported. Consider replacing the function or lambda with a reference to an exported function, resolving symbol RecorderModule in /path/to/TNSStudio/app/modules/recorder/recorder.module.ts, resolving symbol RecorderModule in /path/to/TNSStudio/app/modules/recorder/recorder.module.ts
```

解决前面错误的方法是打开 `app/modules/recorder/recorder.module.ts`，并做出以下更改：

```js
...
// factory functions
export function defaultModalParamsFactory() {
 return new ModalDialogParams({}, null);
};
...
@NgModule({
  ...
  providers: [
    ...PROVIDERS,
    { 
 provide: ModalDialogParams, 
 useFactory: defaultModalParamsFactory 
 }
  ],
  ...
})
export class RecorderModule { }
```

这将满足 Angular AoT 编译器静态解析符号的需求。

# 解决方案 #4 和 #5：无法解析 './app.css'

考虑到在 *准备使用 webpack* 部分提到的第 4 和第 5 个错误：

```js
4\. ERROR in Entry module not found: Error: Can't resolve './app.css' in '/path/to/TNSStudio/app'

5\. ERROR in [copy-webpack-plugin] unable to locate 'app.css' at '/path/to/TNSStudio/app/app.css'
```

解决前面错误的方法实际上与我们在使用特定平台的 `.ios.css` 和 `.android.css` 有关，这些文件通过 SASS 编译。我们需要更新我们的 webpack 配置，使其知道这一点。打开插件为我们自动添加的 `webpack.config.js`，并做出以下更改：

```js
module.exports = env => {
  const platform = getPlatform(env);

  // Default destination inside platforms/<platform>/...
  const path = resolve(nsWebpack.getAppPath(platform));

  const entry = {
    // Discover entry module from package.json
    bundle: `./${nsWebpack.getEntryModule()}`,
    // Vendor entry with third-party libraries
    vendor: `./vendor`,
    // Entry for stylesheet with global application styles
    [mainSheet]: `./app.${platform}.css`,
  };
  ...

function getPlugins(platform, env) {
  ...
  // Copy assets to out dir. Add your own globs as needed.
  new CopyWebpackPlugin([
    { from: "app." + platform + ".css", to: mainSheet },
    { from: "css/**" },
    { from: "fonts/**" },
    { from: "**/*.jpg" },
    { from: "**/*.png" },
    { from: "**/*.xml" },
  ], { ignore: ["App_Resources/**"] }),
  ...
```

好吧，我们现在已经解决了所有捆绑问题，或者等等....**我们真的解决了吗？！**

我们还没有在模拟器或设备上尝试运行应用程序。如果我们现在使用`npm run start-ios-bundle`或通过 XCode 或`npm run start-android-bundle`来尝试这样做，您可能会在应用程序尝试启动时遇到崩溃，并出现如下错误：

```js
JS ERROR Error: No NgModule metadata found for 'AppModule'.
```

解决前面错误的方法是确保您的应用程序包含一个`./app/main.aot.ts`文件，并包含以下内容：

```js
import { platformNativeScript } from "nativescript-angular/platform-static";
import { AppModuleNgFactory } from "./app.module.ngfactory";

platformNativeScript().bootstrapModuleFactory(AppModuleNgFactory);
```

如果您还记得，我们有一个演示组合设置，它从`audio`文件夹加载其音轨文件。我们还利用了来自`assets`文件夹的 font-awesome.css 文件中的 font-awesome 图标。我们需要确保这些文件夹也复制到我们的生产 webpack 构建中。打开`webpack.config.js`并做出以下更改：

```js
new CopyWebpackPlugin([
  { from: "app." + platform + ".css", to: mainSheet },
  { from: "assets/**" },
 { from: "audio/**" },
  { from: "css/**" },
  { from: "fonts/**" },
  { from: "**/*.jpg" },
  { from: "**/*.png" },
  { from: "**/*.xml" },
], { ignore: ["App_Resources/**"] }),
```

成功！

我们现在可以使用以下命令运行我们的捆绑应用程序，而不会出现任何错误：

+   `npm run start-ios-bundle`

+   打开 XCode 项目并运行`npm run start-android-bundle`

值得注意的是，我们为了使应用程序能够进行 webpack 捆绑发布所做的所有更改，在开发中也同样完美有效，所以请放心，您在这个阶段只是改进了应用程序的设置。

# 偏航 - 开发 webpack 插件的概述

我们现在想回到我们在捆绑应用程序时遇到的第一个错误，它是这样的：

+   错误：在`/path/to/TNSStudio/app/modules/player/directives/slider.directive.d.ts`中意外值`SlimSliderDirective`由模块`PlayerModule`在`/path/to/TNSStudio/app/modules/player/player.module.ts`中声明。请添加`@Pipe/@Directive/@Component`注解。

在撰写本书时，这个错误还没有解决方案，所以我们创建了`nativescript-webpack-import-replace`([`github.com/NathanWalker/nativescript-webpack-import-replace`](https://github.com/NathanWalker/nativescript-webpack-import-replace))插件来解决这个问题。

详细介绍开发 webpack 插件超出了本书的范围，但我们想为您提供一些关于该过程的亮点，以防您需要为您的应用程序创建一个来解决特定情况。

我们首先创建了一个单独的项目，并包含一个`package.json`文件，这样我们就可以像安装其他 npm 插件一样安装我们的 webpack 插件：

```js
{
  "name": "nativescript-webpack-import-replace",
  "version": "1.0.0",
  "description": "Replace imports with .ios or .android suffix for target mobile platforms.",
  "files": [
    "index.js",
    "lib"
  ],
  "engines": {
    "node": ">= 4.3 < 5.0.0 || >= 5.10"
  },
  "author": {
    "name": "Nathan Walker",
    "url": "http://github.com/NathanWalker"
  },
  "keywords": [
    "webpack",
    "nativescript",
    "angular"
  ],
  "nativescript": {
    "platforms": {
      "android": "3.0.0",
      "ios": "3.0.0"
    },
    "plugin": {
      "nan": "false",
      "pan": "false",
      "core3": "true",
      "webpack": "true",
      "category": "Developer"
    }
  },
  "homepage": "https://github.com/NathanWalker/nativescript-webpack-import-replace",
  "repository": "NathanWalker/nativescript-webpack-import-replace",
  "license": "MIT"
}
```

`nativescript`键实际上有助于在各个 NativeScript 插件列表网站上对这个插件进行分类。

然后，我们创建了`lib/ImportReplacePlugin.js`来表示我们将在 webpack 配置中导入和使用的实际插件类。我们在这个`lib`文件夹中创建了此文件，以防万一我们需要添加额外的支持文件来帮助我们的插件，以便在插件组织中获得良好的关注点分离。在这个文件中，我们通过定义一个包含我们的插件构造函数的闭包来设置导出：

```js
exports.ImportReplacePlugin = (function () {
  function ImportReplacePlugin(options) {
    if (!options || !options.platform) {
      throw new Error(`Target platform must be specified!`);
    }

    this.platform = options.platform;
    this.files = options.files;
    if (!this.files) {
      throw new Error(`An array of files containing just the filenames to replace with platform specific names must be specified.`);
    }
  }

  return ImportReplacePlugin;
})();
```

这将接受我们在 webpack 配置中定义的目标`platform`，并将其作为选项传递，同时附带一个`files`集合，该集合将包含我们需要替换的所有导入的文件名。

我们接下来想要连接到 webpack 的 `make` 生命周期钩子，以便抓取正在处理的源文件以便解析：

```js
ImportReplacePlugin.prototype.apply = function (compiler) {
    compiler.plugin("make", (compilation, callback) => {
      const aotPlugin = getAotPlugin(compilation);
      aotPlugin._program.getSourceFiles()
        .forEach(sf => {
          this.usePlatformUrl(sf)
        });

      callback();
    })
 };

  function getAotPlugin(compilation) {
    let maybeAotPlugin = compilation._ngToolsWebpackPluginInstance;
    if (!maybeAotPlugin) {
      throw new Error(`This plugin must be used with the AotPlugin!`);
    }
    return maybeAotPlugin;
  }
```

这将抓取所有的 AoT 源文件。然后我们设置一个循环逐个处理它们，并为所需的内容添加处理方法：

```js
ImportReplacePlugin.prototype.usePlatformUrl = function (sourceFile) {
    this.setCurrentDirectory(sourceFile);
    forEachChild(sourceFile, node => this.replaceImport(node));
}

ImportReplacePlugin.prototype.setCurrentDirectory = function (sourceFile) {
   this.currentDirectory = resolve(sourceFile.path, "..");
}

ImportReplacePlugin.prototype.replaceImport = function (node) {
    if (node.moduleSpecifier) {
      var sourceFile = this.getSourceFileOfNode(node);
      const sourceFileText = sourceFile.text;
      const result = this.checkMatch(sourceFileText);
      if (result.index > -1) {
        var platformSuffix = "." + this.platform;
        var additionLength = platformSuffix.length;
        var escapeAndEnding = 2; // usually "\";" or "\';"
        var remainingStartIndex = result.index + (result.match.length - 1) + (platformSuffix.length - 1) - escapeAndEnding;

        sourceFile.text =
          sourceFileText.substring(0, result.index) +
          result.match +
          platformSuffix +
          sourceFileText.substring(remainingStartIndex);

        node.moduleSpecifier.end += additionLength;
      }
    }
  }

  ImportReplacePlugin.prototype.getSourceFileOfNode = function (node) {
    while (node && node.kind !== SyntaxKind.SourceFile) {
      node = node.parent;
    }
    return node;
  }

  ImportReplacePlugin.prototype.checkMatch = function (text) {
    let match = '';
    let index = -1;
    this.files.forEach(name => {
      const matchIndex = text.indexOf(name);
      if (matchIndex > -1) {
        match = name;
        index = matchIndex;
      }
    });
    return { match, index };
  }
```

构建 webpack 插件的一个有趣的部分（并且可以说是最具挑战性的）是与你的源代码的 **抽象语法树（ASTs**）一起工作。我们插件的一个关键方面是从 AST 中获取“源文件”节点，如下所示：

```js
ImportReplacePlugin.prototype.getSourceFileOfNode = function (node) {
  while (node && node.kind !== SyntaxKind.SourceFile) {
    node = node.parent;
  }
  return node;
}
```

这有效地排除了任何其他不是源文件的节点，因为我们的插件只需要处理这些节点。

最后，我们在根目录创建一个 `index.js` 文件，以便简单地导出插件文件以供使用：

```js
module.exports = require("./lib/ImportReplacePlugin").ImportReplacePlugin;
```

在这个 webpack 插件的帮助下，我们能够完全解决我们在应用中遇到的所有 webpack 打包错误。

# 摘要

在这一章中，我们通过将 webpack 添加到我们的构建链中来准备我们的应用分发，以确保我们有最小的 JavaScript 大小和最佳代码执行性能。这也使得 Angular 的 AoT 编译在我们的应用中成为可能，这有助于提供我们代码的最佳性能。

在此过程中，我们提供了一系列解决方案的食谱，以解决你可能在应用开发过程中遇到的 webpack 打包错误。此外，我们还从高层次的角度探讨了开发自定义 webpack 插件，以帮助解决我们应用中特定的错误条件，从而实现成功的打包。

现在我们已经得到了我们应用代码的最佳打包，我们现在准备完成我们的分发步骤，以便在下一章中最终部署我们的应用。
