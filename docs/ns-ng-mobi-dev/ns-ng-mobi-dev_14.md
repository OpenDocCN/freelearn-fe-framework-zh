# 第十四章：使用 webpack 进行部署准备

我们希望将我们的应用程序部署到两个主要的移动应用商店，苹果应用商店和谷歌 Play 商店；然而，有一些事情我们需要做来准备我们的应用程序进行分发。

为了确保你使用最小的 JavaScript 大小，以及 Angular 的 AoT 编译器来帮助我们的应用尽可能快地执行，我们将使用 webpack 来捆绑所有内容。值得注意的是，webpack 并不是创建可分发的 NativeScript 应用程序的必需条件。然而，它提供了非常好的好处，应该使它成为任何人在分发他们的应用程序时的重要步骤。

在本章中，我们将涵盖以下主题：

+   为 NativeScript for Angular 项目安装 webpack

+   准备项目以使用 webpack 进行捆绑

+   解决各种 webpack 捆绑问题

+   编写自己的自定义 webpack 插件以解决特定情况的入门指南

# 使用 webpack 来捆绑应用程序

如果不是 Sean Larkin，你可能永远不会听说过 webpack。他在捆绑器社区的贡献和参与帮助将 webpack 引入了 Angular CLI，并使其成为许多事情的主要*首选*捆绑器。我们非常感谢他在社区中的努力和善意。

# 准备使用 webpack

让我们看看如何利用 webpack 来减少我们的 NativeScript for Angular 应用程序的打包大小，以确保它在用户的移动设备上执行得尽可能优化。

让我们首先安装插件：

```ts
npm install nativescript-dev-webpack --save-dev
```

这将自动创建一个`webpack.config.js`文件（在项目的根目录），预先配置了一个基本设置，可以让你在大多数应用中进一步使用。此外，它还创建了一个`tsconfig.aot.json`文件（同样在项目的根目录），因为 NativeScript 的 webpack 使用将使用 Angular 的 AoT 编译器进行捆绑。它还在我们的`package.json`中添加了一些巧妙的 npm 脚本，以帮助处理我们想要的各种捆绑选项；请考虑以下示例：

+   `npm run build-android-bundle` 用于构建 Android

+   `npm run build-ios-bundle` 用于构建 iOS

+   `npm run start-android-bundle` 用于在 Android 上运行

+   `npm run start-ios-bundle` 用于在 iOS 上运行

但是，在我们尝试这些新命令之前，我们需要审查我们的应用程序的一些内容。

我们应该首先确保所有 NativeScript 导入路径都以`tns-core-modules/[module]`开头；请考虑以下示例：

```ts
BEFORE:
import { isIOS } from 'platform';
import { topmost } from 'ui/frame';
import * as app from 'application';

AFTER:
import { isIOS } from 'tns-core-modules/platform';
import { topmost } from 'tns-core-modules/ui/frame';
import * as app from 'tns-core-modules/application';
```

我们现在将浏览我们的应用程序并执行此操作。这对开发和生产构建都有效。

你可能会想，*嘿！如果我们需要在事后遍历整个代码库并更改导入，为什么你还要使用另一种形式？*

非常关注！实际上有很多示例显示了方便的简写导入路径，所以我们选择在本章中始终使用它来构建应用程序，以证明它对开发非常有效，以帮助避免混淆，以防将来遇到这样的示例。此外，事后编辑以准备 webpack 并不需要太多时间，现在你知道了。

立即运行以下命令：

```ts
npm run build-ios-bundle
```

我们可以看到以下错误——我已经列举出来——我们将在下一节中按顺序提出解决方案：

1.  意外值`SlimSliderDirective`在`/path/to/TNSStudio/app/modules/player/directives/slider.directive.d.ts`中的模块 PlayerModule 中声明。请添加`@Pipe/@Directive/@Component`注释。

1.  无法确定`SlimSliderDirective`类在`/path/to/TNSStudio/app/modules/player/directives/slider.directive.android.ts`中的模块！将`SlimSliderDirective`添加到`NgModule`中以修复它。无法确定`SlimSliderDirective`类在`/path/to/TNSStudio/app/modules/player/directives/slider.directive.ios.ts`中的模块！将`SlimSliderDirective`添加到`NgModule`中以修复它。

1.  错误在静态解析符号值时遇到错误。调用函数`ModalDialogParams`，不支持函数调用。考虑用对导出函数的引用替换函数或 lambda，解析符号`RecorderModule`在`/path/to/TNSStudio/app/modules/recorder/recorder.module.ts`中，解析符号`RecorderModule`在`/path/to/TNSStudio/app/modules/recorder/recorder.module.ts`中。

1.  入口模块未找到：错误：无法解析`/path/to/TNSStudio/app`中的`./app.css`。

1.  错误在[copy-webpack-plugin]无法在`/path/to/TNSStudio/app/app.css`中找到`app.css`。

前三个错误纯粹与 Angular **Ahead of Time** (**AoT**)编译相关。最后两个纯粹与 webpack 配置相关。让我们看看每个错误以及如何正确解决它。

# 解决方案＃1：意外值'SlimSliderDirective...'

考虑前一节中提到的第一个完整错误：

```ts
ERROR in Unexpected value 'SlimSliderDirective in /path/to/TNSStudio/app/modules/player/directives/slider.directive.d.ts' declared by the module 'PlayerModule in /path/to/TNSStudio/app/modules/player/player.module.ts'. Please add a @Pipe/@Directive/@Component annotation.
```

解决前面的错误是安装额外的 webpack 插件：

```ts
npm install nativescript-webpack-import-replace --save-dev
```

然后，打开`webpack.config.js`并配置插件如下：

```ts
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

这将在`app/modules/players/directives/index.ts`中找到`slider.directive`的导入，并附加正确的目标平台后缀，这样 AoT 编译器就会选择正确的目标平台实现文件。

在撰写本书时，对于该错误尚不存在解决方案，因此我们开发了`nativescript-webpack-import-replace`插件来解决。由于您可能会遇到需要通过插件提供一些额外 webpack 帮助的 webpack 捆绑情况，我们将分享我们如何开发插件来解决该错误的概述，以防您遇到其他可能需要您创建插件的模糊错误。

首先让我们看看如何解决最初剩下的错误，然后我们将重点介绍 webpack 插件开发。

# 解决方案＃2：无法确定 SlimSliderDirective 类的模块...

考虑*准备使用 webpack*部分提到的第二个完整错误：

```ts
ERROR in Cannot determine the module for class SlimSliderDirective in /path/to/TNSStudio/app/modules/player/directives/slider.directive.android.ts! Add SlimSliderDirective to the NgModule to fix it.
Cannot determine the module for class SlimSliderDirective in /path/to/TNSStudio/app/modules/player/directives/slider.directive.ios.ts! Add SlimSliderDirective to the NgModule to fix it.
```

解决上述错误的方法是打开`tsconfig.aot.json`，并进行以下更改：

```ts
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

由于 AoT 编译使用`tsconfig.aot.json`配置，我们希望更具体地指定要编译的文件。由于`./app/main.ts`是引导应用程序的入口点，我们将针对该文件并删除`exclude`块。

如果我们现在尝试进行捆绑，我们将解决我们看到的错误；然而，我们将看到以下*新*错误：

```ts
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

这是因为我们的目标是`./app/main.ts`，它会分支到我们应用程序文件的所有其他导入，除了那些懒加载的模块。

解决上述错误的方法是在`files`部分中添加懒加载模块路径：

```ts
"files": [
  "./app/main.ts",
  "./app/modules/mixer/mixer.module.ts",
 "./app/modules/recorder/recorder.module.ts"
 ],
```

好了，我们解决了`lazy`错误；然而，现在这揭示了几个*新*错误，如下所示：

```ts
ERROR in /path/to/TNSStudio/app/modules/recorder/components/record.component.ts (128,19): Cannot find name 'CFRunLoopGetMain'.
ERROR in /path/to/TNSStudio/app/modules/recorder/components/record.component.ts (130,9): Cannot find name 'CFRunLoopPerformBlock'.
ERROR in /path/to/TNSStudio/app/modules/recorder/components/record.component.ts (130,40): Cannot find name 'kCFRunLoopDefaultMode'.
ERROR in /path/to/TNSStudio/app/modules/recorder/components/record.component.ts (131,9): Cannot find name 'CFRunLoopWakeUp'.
```

就在此时...

放克灵魂兄弟。

是的，你可能正在唱 Fatboy Slim 或即将失去理智，我们理解。使用 webpack 进行捆绑有时可能会是一次非常冒险的经历。我们能提供的最好建议是保持耐心和勤奋，逐个解决错误；我们几乎到了。

解决上述错误的方法是包含 iOS 和 Android 平台声明，因为我们在应用程序中使用原生 API：

```ts
"files": [
  "./app/main.ts",
  "./app/modules/mixer/mixer.module.ts",
  "./app/modules/recorder/recorder.module.ts",
  "./node_modules/tns-platform-declarations/ios.d.ts",
 "./node_modules/tns-platform-declarations/android.d.ts"
]
```

万岁，我们现在已完全解决了第二个问题。让我们继续下一个。

# 解决方案＃3：遇到静态解析符号值的错误

考虑*准备使用 webpack*部分提到的第三个完整错误：

```ts
ERROR in Error encountered resolving symbol values statically. Calling function 'ModalDialogParams', function calls are not supported. Consider replacing the function or lambda with a reference to an exported function, resolving symbol RecorderModule in /path/to/TNSStudio/app/modules/recorder/recorder.module.ts, resolving symbol RecorderModule in /path/to/TNSStudio/app/modules/recorder/recorder.module.ts
```

前面错误的解决方案是打开`app/modules/recorder/recorder.module.ts`并进行以下更改：

```ts
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

# 解决方案＃4 和＃5：无法解析'./app.css'

考虑在*准备使用 webpack*部分中提到的第 4 和第 5 个错误：

```ts
4\. ERROR in Entry module not found: Error: Can't resolve './app.css' in '/path/to/TNSStudio/app'

5\. ERROR in [copy-webpack-plugin] unable to locate 'app.css' at '/path/to/TNSStudio/app/app.css'
```

前面错误的解决方案实际上与我们使用特定于平台的`.ios.css`和`.android.css`有关，这是通过 SASS 编译的。我们需要更新我们的 webpack 配置，以便它知道这一点。打开`webpack.config.js`，插件已自动为我们添加，并进行以下更改：

```ts
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

好吧，我们现在已经解决了所有捆绑问题，或者等一下....**我们吗？！**

我们还没有尝试在模拟器或设备上运行应用程序。如果我们现在尝试使用`npm run start-ios-bundle`或通过 XCode 或`npm run start-android-bundle`进行此操作，当它尝试启动时，您可能会遇到应用程序崩溃的错误，如下所示：

```ts
JS ERROR Error: No NgModule metadata found for 'AppModule'.
```

前面错误的解决方案是确保您的应用程序包含一个`./app/main.aot.ts`文件，其中包含以下内容：

```ts
import { platformNativeScript } from "nativescript-angular/platform-static";
import { AppModuleNgFactory } from "./app.module.ngfactory";

platformNativeScript().bootstrapModuleFactory(AppModuleNgFactory);
```

如果您还记得，我们有一个演示组合设置，它从`audio`文件夹加载其轨道文件。我们还利用了 font-awesome 图标，借助于从`assets`文件夹加载的 font-awesome.css 文件。我们需要确保这些文件夹也被复制到我们的生产 webpack 构建中。打开`webpack.config.js`并进行以下更改：

```ts
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

现在我们可以使用以下命令运行我们捆绑的应用程序，而不会出现错误：

+   `npm run start-ios-bundle`

+   打开 XCode 项目并运行`npm run start-android-bundle`

值得注意的是，我们为发布应用启用 webpack 捆绑所做的所有更改在开发中也完全有效，因此请放心，您目前只是改进了应用的设置。

# 绕道-开发 webpack 插件概述

现在我们想要回到我们在捆绑应用程序时遇到的第一个错误，即：

+   ERROR in 意外值`SlimSliderDirective`在`/path/to/TNSStudio/app/modules/player/directives/slider.directive.d.ts`中由`PlayerModule`模块声明在`/path/to/TNSStudio/app/modules/player/player.module.ts`中。请添加`@Pipe/@Directive/@Component`注释。

在撰写本书时，尚不存在此错误的解决方案，因此我们创建了`nativescript-webpack-import-replace`（[`github.com/NathanWalker/nativescript-webpack-import-replace`](https://github.com/NathanWalker/nativescript-webpack-import-replace)）插件来解决这个问题。

详细开发 webpack 插件超出了本书的范围，但我们希望为您提供一些过程的亮点，以防您最终需要创建一个来解决应用程序的特定情况。

我们首先创建了一个单独的项目，其中包含一个`package.json`文件，以便像安装其他 npm 插件一样安装我们的 webpack 插件：

```ts
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

`nativescript`关键字实际上有助于在各种 NativeScript 插件列表网站上对此插件进行分类。

然后，我们创建了`lib/ImportReplacePlugin.js`来表示我们可以导入并在 webpack 配置中使用的实际插件类。我们将此文件创建在`lib`文件夹中，以防需要添加额外的支持文件来帮助我们的插件进行良好的分离。在这个文件中，我们通过定义一个包含我们插件构造函数的闭包来设置导出：

```ts
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

这将获取我们 webpack 配置中定义的目标`platform`，并将其作为选项传递，同时还有一个`files`集合，其中包含我们需要替换的所有导入文件的文件名。

然后，我们希望在 webpack 的`make`生命周期钩子中插入，以便抓住正在处理的源文件以进行解析：

```ts
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

这抓住了所有的 AoT 源文件。然后我们设置一个循环，逐个处理它们，并为我们需要的内容添加处理方法：

```ts
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

构建 webpack 插件的一个有趣部分（*可能是最具挑战性的*）是处理源代码的**抽象语法树**（**ASTs**）。我们插件的一个关键方面是从 AST 中获取“源文件”节点，方法如下：

```ts
ImportReplacePlugin.prototype.getSourceFileOfNode = function (node) {
  while (node && node.kind !== SyntaxKind.SourceFile) {
    node = node.parent;
  }
  return node;
}
```

这有效地清除了除源文件之外的任何其他节点，因为这是我们的插件需要处理的所有内容。

最后，我们在根目录创建了一个`index.js`文件，只需导出插件文件供使用：

```ts
module.exports = require("./lib/ImportReplacePlugin").ImportReplacePlugin;
```

借助这个 webpack 插件，我们能够完全解决我们应用程序中遇到的所有 webpack 捆绑错误。

# 总结

在本章中，我们通过将 webpack 添加到构建链中，为应用程序的分发做好了准备，以帮助确保我们的 JavaScript 大小最小，代码执行性能最佳。这也使得 Angular 的 AoT 编译在我们的应用程序上可用，有助于提供我们代码的最佳性能。

在此过程中，我们提供了一些解决各种 webpack 捆绑错误的解决方案，这些错误可能在应用程序开发过程中遇到。此外，我们还从高层次上看了一下开发自定义 webpack 插件，以帮助解决应用程序中特定的错误条件，从而实现成功的捆绑。

现在我们已经有了应用程序代码的最佳捆绑，我们现在准备完成我们的分发步骤，最终在下一章部署我们的应用程序。
