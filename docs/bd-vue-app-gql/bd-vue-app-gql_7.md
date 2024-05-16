# 第七章：将您的应用程序转换为 PWA 并部署到 Web

当我们编码完成时，是时候完成我们的应用程序并准备发布了。现在，我们的自定义聊天应用程序已经与后端的 Amplify 服务以及前端的 Quasar 和 Vue 一起运行，我们已经准备好在网络上运行。

在本章中，您将学习如何将您的应用程序转换为**渐进式 Web 应用程序**（**PWA**），在服务工作者上添加一些事件以通知用户您的应用程序的新版本，为 iOS 设备创建自定义横幅以进行安装，并最终部署应用程序。

因此，在本章中，我们将涵盖以下配方：

+   将应用程序转换为 PWA

+   创建应用程序更新通知

+   在 iOS 上添加自定义 PWA 安装通知

+   创建生产环境和部署

# 技术要求

在本章中，我们将使用**Node.js**，**AWS Amplify**和**Quasar Framework**。

注意，Windows 用户！您需要安装一个名为`windows-build-tools`的`npm`包，以便能够安装所需的软件包。要执行此操作，请以管理员身份打开 PowerShell 并执行以下命令：

`> npm install -g windows-build-tools`

要安装**Quasar Framework**，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> npm install -g @quasar/cli
```

要安装**AWS Amplify**，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> npm install -g @aws-amplify/cli
```

# 将应用程序转换为 PWA

为了在当前网络上获得应用程序的最佳体验，您需要有一个**PWA**，在其中可以使您的应用程序缓存代码的某些部分，离线工作，接收推送通知等等。

在这个配方中，您将学习如何将您的**单页应用程序**（**SPA**）转换为 PWA，并重新配置 Amplify CLI 以适应新的配置。

## 做好准备

此配方的先决条件是 Node.js 12+。

所需的 Node.js 全局对象如下：

+   `@aws-amplify/cli`

+   `@quasar/cli`

要开始将我们的项目转换为 PWA，我们将继续使用在*第六章，创建聊天和消息 Vuex，页面和路由*中创建的项目。

## 如何做...

在将应用程序发布到生产环境之前，是时候将我们的应用程序转换为 PWA 了。按照以下步骤将 PWA 模式添加到 Quasar 中：

1.  首先，我们需要将 PWA 模式添加到 Quasar 应用程序中。要做到这一点，在项目文件夹中，打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows），并执行以下命令：

```js
> quasar m add pwa
```

1.  在项目根文件夹中打开`quasar.conf.js`文件，并找到`pwa`属性。从 JavaScript 对象中删除`workboxPluginMode`和`workboxOptions`属性，并添加`cleanupOutdatedCaches`，`skipWaiting`和`clientsClaim`属性，定义为`true`。最后，在`manifest`属性上，更改`name`，`short_name`和`description`以匹配您的应用程序，如下面的代码所示：

```js
pwa: {
 cleanupOutdatedCaches: true,
 skipWaiting: true,
 clientsClaim: true,
   manifest: {
     name: 'Chat Application',
     short_name: 'Chat App',
     description: 'Quasar & AWS Amplify Chat Application',
     ...
   },
 ...
}
```

1.  现在是时候将 Amplify CLI 的配置更改为新的配置。为此，在项目文件夹中，打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows），并执行以下命令：

```js
> amplify configure project
```

1.  CLI 将要求您是否要更改项目名称。无需更改，因此按*Enter*继续：

```js
? Enter a name for the project (chatapp) 
```

1.  CLI 将要求您是否要更改项目默认编辑器。选择`Visual Studio Code`（或者您将在项目中使用的默认编辑器），然后按*Enter*继续：

```js
? Choose your default editor: (Use arrow keys)
❯ Visual Studio Code 
  Atom Editor 
  Sublime Text 
  IntelliJ IDEA 
  Vim (via Terminal, Mac OS only) 
  Emacs (via Terminal, Mac OS only) 
  None 
```

1.  CLI 将要求您是否要更改项目应用程序类型。选择`javascript`选项，然后按*Enter*继续：

```js
? Choose the type of app that you're building (Use arrow keys)
  android 
  ios 
❯ javascript 
```

1.  CLI 将要求您是否要更改项目的 JavaScript 框架。选择`none`，然后按*Enter*继续：

```js
Please tell us about your project
? What javascript framework are you using (Use arrow keys)
  angular 
  ember 
  ionic 
  react 
  react-native 
  vue 
❯ none 
```

1.  CLI 将要求您是否要更改项目应用程序源目录。无需更改，因此按*Enter*继续：

```js
? Source Directory Path:  (src)
```

1.  CLI 将要求您是否要更改项目应用程序分发目录；更改路径为`dist/pwa`，然后按*Enter*继续：

```js
? Distribution Directory Path: dist/pwa
```

1.  CLI 将要求您输入`Build Command`选项；更改命令为`quasar build -m pwa`，然后按*Enter*继续：

```js
? Build Command: quasar build -m pwa
```

1.  CLI 将要求您输入`Start Command`选项；更改为`quasar dev -m pwa`，然后按*Enter*继续：

```js
? Start Command: quasar dev -m pwa
```

1.  CLI 将要求您是否要更新或删除项目配置。选择`update`，然后按*Enter*继续：

```js
Using default provider awscloudformation

For more information on AWS Profiles, see:
https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-profiles.html

For the awscloudformation provider.
? Do you want to update or remove the project level configuration (Use arrow keys)
❯ update 
  remove 
  cancel 
```

1.  CLI 将要求您在此更新中是否要使用 AWS 配置文件。输入*Y*，然后按*Enter*继续：

```js
? Do you want to use an AWS profile? Y
```

1.  最后，选择您想要使用的配置文件，然后按*Enter*继续：

```js
? Please choose the profile you want to use (Use arrow keys)
❯ default 
```

## 工作原理...

在本教程中，我们使用 Quasar CLI 将 PWA 开发环境添加到我们的项目中，使用内置的`quasar -m add`命令来添加新的开发环境。

然后，我们配置了`quasar.conf.js`文件，以在`pwa`属性上添加新属性，以便我们可以在部署的应用程序中添加更好的用户体验。

最后，我们更改了 Amplify CLI 配置，以便它将使用新的`pwa`环境作为构建命令和分发文件夹。

## 另请参阅

+   在[`quasar.dev/quasar-cli/developing-pwa/introduction`](https://quasar.dev/quasar-cli/developing-pwa/introduction)找到有关使用 Quasar 开发 PWA 的更多信息。

+   在[`docs.amplify.aws/cli`](https://docs.amplify.aws/cli)找到有关 Amplify CLI 的更多信息。

# 创建应用程序更新通知

让用户始终了解应用程序的更新是一个好习惯，因为他们将始终知道应用程序正在维护和改进。

使用 PWA，您可以访问诸如创建原生移动应用程序、允许在移动设备上安装应用程序等功能。

发布更新时，我们需要通知用户并更新当前安装的代码。

在本教程中，我们将学习如何使用服务工作程序生命周期来注册应用程序安装，并将其用于在有新更新时通知用户并应用新的更新版本。

## 准备好了吗？

本教程的先决条件是 Node.js 12+。

所需的 Node.js 全局对象如下：

+   `@aws-amplify/cli`

+   `@quasar/cli`

要开始添加自定义更新通知，我们将继续使用在*将应用程序转换为 PWA*教程中创建的项目。

## 如何做...

按照以下步骤在我们的 PWA 中添加更新通知：

1.  打开项目根文件夹中的`quasar.conf.js`文件，找到`framework`属性。然后对于`plugins`属性，将`'Notify'`字符串添加到数组中，以便 Quasar 在应用程序启动时加载`Notify`插件：

```js
framework: {
 ...
  plugins: [
   'Dialog',
  'Notify',
 ],
 ...
},
```

1.  打开`src-pwa`文件夹中的`register-service-worker.js`文件，并从 Quasar 导入`Notify`插件：

```js
import { Notify } from 'quasar';
```

1.  创建一个名为`clearLocalCache`的异步函数。然后创建一个名为`cachedFiles`的常量，并将其定义为`await caches.keys()`；在`cachedFiles`常量上，使用参数`file`执行一个数组`map`函数；在函数内部执行`await caches.delete(file)`。最后，重新加载应用程序：

```js
async function clearLocalCache() {
  const cachedFiles = await caches.keys();    await cachedFiles.map(async (file) => {
  await caches.delete(file);
  });    window.location.reload(); }
```

1.  找到`updatefound`函数，并创建一个名为`installKey`的常量，并将其定义为`'chatAppInstalled'`。然后验证浏览器的`localStorage`项中是否有与您创建的常量名称相同的项。如果存在该项，则执行`Notify.create`函数，并将 JavaScript 对象作为参数传递，其中`color`属性定义为`'dark'`，`message`定义为更新消息。如果`localStorage`项不存在，则向`localStorage`添加一个名为`installKey`常量的项，其值为`'1'`：

```js
updatefound(/* registration */) {
  const installKey = 'chatAppInstalled';
  if (localStorage.getItem(installKey)) {
    Notify.create({
      color: 'dark',
      message: 'An update is being downloaded from the server.',
    });
  } else {
    localStorage.setItem(installKey, '1');
  }
},
```

1.  最后，找到`updated`函数，并添加一个`Notify.create`函数，将 JavaScript 对象作为参数传递。在此对象中，添加一个`type`属性，定义为`'positive'`，一个`message`属性，定义为成功更新的消息，一个带有刷新应用程序指示的`caption`属性，以及一个定义为数组的`actions`属性。在`actions`数组中，添加一个 JavaScript 对象，其中`label`属性定义为`'Refresh'`，`color`属性定义为`'white'`，`handler`属性定义为`clearLocalCache`函数：

```js
updated(/* registration */) {
  Notify.create({
  type: 'positive',
  message: 'The application was updated successfully!',
  caption: 'Please refresh the page to apply the new update.',
  actions: [
 {  label: 'Refresh',
  color: 'white',
  handler: clearLocalCache,
  },
  ],
  }); },
```

以下是通知的预览：

+   发现新更新：

![](img/8b444e5c-d289-4f6a-9d61-743880d74740.png)

+   更新已应用：

![](img/665cbf77-cab3-4f35-b56d-c0a409ef6789.png)

## 它是如何工作的...

首先，我们将`Notify`插件添加到`quasar.conf.js`文件的插件属性中，这样 Quasar CLI 就可以在执行运行时为我们提供它。

然后，在`register-service-worker.js`文件中，我们添加了 Notify 插件并创建了一个自定义的缓存清除函数。

在`updatefound`生命周期中，我们添加了安装验证，以便新的更新通知只会显示给已在其浏览器上安装了应用程序的用户。

最后，我们在更新的生命周期中添加了更新完成过程的通知，并为用户添加了一个清除缓存并重新启动应用程序的操作按钮。

## 另请参阅

+   在[`quasar.dev/quasar-plugins/notify`](https://quasar.dev/quasar-plugins/notify)找到有关 Quasar Notify 插件的更多信息。

+   在[`developer.mozilla.org/en-US/docs/Web/API/Cache`](https://developer.mozilla.org/en-US/docs/Web/API/Cache)找到有关 JavaScript 缓存接口的更多信息。

# 在 iOS 上添加自定义 PWA 安装通知

不幸的是，在 iOS 上，Safari 浏览器引擎没有默认的 PWA 安装横幅。在这种情况下，我们必须实现自己的版本。使用名为`a2hs.js`（用于*添加到主屏幕*）的社区插件，我们可以为 iOS 用户启用自定义安装消息的显示。

在这个教程中，我们将学习如何在项目中添加`a2hs.js`插件，并如何使用 Quasar 引导文件将其添加到项目引导序列中。

## 准备工作

这个教程的先决条件是 Node.js 12+。

需要的 Node.js 全局对象如下：

+   `@aws-amplify/cli`

+   `@quasar/cli`

+   `a2h2.js`

要开始开发自定义 iOS PWA 安装横幅，我们将继续使用在“创建应用程序更新通知”教程中创建的项目。

## 如何做...

在 Safari 上的 iOS 平台上，浏览器中没有 PWA 应用的安装横幅。在这些步骤中，我们将添加`a2hs.js`插件以添加此缺失的功能：

1.  首先，我们需要在项目中安装`a2js.js`插件。要做到这一点，在项目文件夹中，打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows），并执行以下命令：

```js
> npm install --save a2hs.js
```

1.  在`boot`文件夹中，然后在`src`文件夹内，创建一个`a2hs.js`文件并打开它。接下来，导入`a2hs.js`插件：

```js
import AddToHomeScreen from 'a2hs.js';
```

1.  要使 Quasar 引导文件起作用，我们需要创建一个默认导出的函数。在这个函数中，创建一个名为`options`的常量，并将其定义为一个 JavaScript 对象，其中`brandName`属性作为应用程序的名称：

```js
export default() => {
 const options = {
  brandName: 'Chat App',  };
  AddToHomeScreen(options); };
```

1.  最后，在项目根文件夹中，打开`quasar.conf.js`文件并找到`boot`属性。在数组中，添加`'a2hs'`字符串，以使其对 Quasar CLI 可用，并加载新创建的引导文件：

```js
boot: [
  'amplify',
  'axios',
  'a2hs', ],
```

这是在 iOS 设备上弹出的警报的预览：

![](img/e8ff8fe2-d307-4148-9223-38c1d933674c.png)

## 它是如何工作的...

首先，我们使用`npm`安装了`a2hs.js`插件到项目中。然后，我们在 Quasar 上创建了一个`a2hs.js`文件，用作引导文件。

然后，在新创建的文件中，我们导入了`a2hs.js`插件和应用程序 logo，然后实例化了具有自定义选项的`a2hs.js`插件。

最后，我们将`a2hs`引导文件添加到`quasar.conf.js`文件的`boot`属性中。

## 另请参阅

+   您可以在[`quasar.dev/quasar-cli/boot-files`](https://quasar.dev/quasar-cli/boot-files)找到有关 Quasar 引导文件结构的更多信息。

+   您可以在[`github.com/koddr/a2hs.js/`](https://github.com/koddr/a2hs.js/)找到有关`a2hs.js`的更多信息。

# 创建生产环境和部署

在所有工作都已经完成，准备好我们的应用程序后，现在是时候将其构建为一个生产就绪的分发，通过创建一个生产环境并将其部署到该环境。这个新环境将不会有来自测试的数据，并且我们将确保这个环境将专门用于生产状态。

生产环境可以被描述为一个最终用户放置您的应用程序的环境，具有准备好接收最终用户数据的代码和数据库。

在这个教程中，我们将学习如何使用 Amplify CLI 创建一个生产环境，以及如何在 Amplify 控制台上将其定义为默认的生产环境。

## 准备工作

这个教程的先决条件是 Node.js 12+。

所需的 Node.js 全局对象如下：

+   `@aws-amplify/cli`

+   `@quasar/cli`

要开始创建生产环境，我们将继续使用在*在 iOS 上添加自定义 PWA 安装通知*教程中创建的项目。

## 如何做...

我们需要准备好我们的应用程序，以在生产环境中发布给我们的用户。按照以下步骤在 Amplify 控制台中创建生产环境并将其定义为我们应用程序的默认生产环境：

1.  在项目根目录中，打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> amplify env add
```

1.  Amplify CLI 会询问您是否要使用现有环境作为基础；按下*N*和*Enter*继续：

```js
? Do you want to use an existing environment? (Y/n) n
```

1.  现在 Amplify CLI 会询问您新环境的名称；将`production`作为名称输入并按*Enter*继续：

```js
? Enter a name for the environment production production
```

1.  CLI 会询问您是否要在此更新中使用 AWS 配置文件；输入*Y*并按*Enter*继续：

```js
? Do you want to use an AWS profile? (Y/n) y
```

1.  选择要使用的配置文件并按*Enter*继续：

```js
? Please choose the profile you want to use (Use arrow keys)
❯ default 
```

1.  现在，我们需要将所做的更改推送到服务器；为此，请打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows），进入项目文件夹，并执行以下命令：

```js
> amplify push
```

1.  Amplify CLI 将询问您是否要更新自动生成的 GraphQl 代码；输入*Y*并按*Enter*继续：

```js
? Do you want to update code for your updated GraphQL API (Y/n) y
```

1.  Amplify CLI 将询问您是否要覆盖当前现有的代码；输入*Y*并按*Enter*继续：

```js
? Do you want to generate GraphQL statements (queries, mutations and 
  subscription) based on your schema types?
This will overwrite your current graphql queries, mutations and subscriptions (Y/n) y
```

1.  然后将您的网站发布到生产环境。要做到这一点，打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows），进入项目文件夹，并执行以下命令：

```js
> amplify publish
```

1.  对于最后一部分，我们需要配置应用程序设置以使用我们创建的新`production`环境。为此，请打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows），进入项目文件夹，并执行以下命令：

```js
> amplify console
```

1.  打开侧边菜单，点击“常规”链接：

！[](assets/b7081945-0d9a-4a5b-a68a-cc92f0dd841c.png)

1.  现在，在应用程序详细信息卡中，点击右上角的“编辑”按钮：

！[](assets/47f656dd-14e9-44e7-941f-753000177c57.png)

1.  然后在设置中，打开生产环境选择框，并选择生产环境：

！[](assets/b1fba80a-b5bd-4cbf-a2e5-f12549521816.png)

1.  最后，要检查更改是否已正确保存，请刷新页面并检查应用程序详细信息卡的“设置”部分：

！[](assets/5cf19669-f198-4def-b807-dce6e093918b.png)

## 它是如何工作的...

在这个教程中，我们首先使用 Amplify CLI 向本地 Amplify 实例添加了一个新环境，并选择使用全新的环境。然后，我们将这个新环境发送到云端，更新我们的本地代码库，并最终使用这个新环境发布项目。

最后，我们去 Amplify 控制台配置应用程序的生产环境，作为我们创建的新环境。

## 另请参阅

+   在[`docs.amplify.aws/cli`](https://docs.amplify.aws/cli)上查找有关 Amplify CLI 的更多信息。

+   在[`aws.amazon.com/amplify/console/?nc1=h_ls`](https://aws.amazon.com/amplify/console/?nc1=h_ls)上查找有关 Amplify 控制台的更多信息。
