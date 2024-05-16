# 第十章：将应用程序部署到云平台

现在是时候将您的应用程序部署到全球范围内，使其对全球所有人都可用。

在这一章中，我们将学习如何在三个不同的托管平台上进行操作 - Netlify，Now 和 Firebase。在这里，我们将学习在每个平台上创建帐户的过程，设置环境，配置应用程序以进行部署，最后将其部署到网络上。

在本章中，我们将涵盖以下配方：

+   创建 Netlify 帐户

+   为在 Netlify 上部署应用程序做准备

+   为在 GitHub 上的 Netlify 进行自动部署做准备

+   创建一个 Vercel 帐户

+   配置 Vercel-CLI 并部署您的项目

+   为在 GitHub 上的 Vercel 进行自动部署做准备

+   创建一个 Firebase 项目

+   配置 Firebase-CLI 并部署您的项目

# 技术要求

在本章中，我们将使用**Node.js**和**Vue-CLI**。

注意 Windows 用户！您需要安装一个名为 windows-build-tools 的 NPM 包，以便能够安装以下必需的软件包。要做到这一点，以管理员身份打开 PowerShell 并执行以下命令：`> npm install -g windows-build-tools`

要安装 Vue-CLI，您需要打开 Terminal（macOS 或 Linux）或 Command Prompt/PowerShell（Windows）并执行以下命令：

```js
> npm install -g @vue/cli @vue/cli-service-global
```

## 创建一个 Vue 项目

要创建一个 Vue-CLI 项目，请按照以下步骤进行：

1.  打开 Terminal（macOS 或 Linux）或 Command Prompt/PowerShell（Windows）并执行以下命令：

```js
> vue create vue-project
```

1.  Vue-CLI 将要求您选择一个预设；使用*空格键*选择`Manually select features`：

```js
?  Please pick a preset: (Use arrow keys) default (babel, eslint) ❯ Manually select features 
```

1.  现在，Vue-CLI 将要求选择功能，您需要选择`Router`，`Vuex`和`Linter / Formatter`作为默认功能之外的附加功能：

```js
?  Check the features needed for your project: (Use arrow keys) ❯ Babel
 TypeScript Progressive Web App (PWA) Support ❯ Router ❯ Vuex
  CSS Pre-processors ❯ Linter / Formatter
 Unit Testing E2E Testing
```

1.  现在，Vue-CLI 将询问您是否要使用历史模式进行路由管理。我们将选择`y`（是）：

```js
?  Use history mode for router? (Requires proper server setup for 
  index fallback in production)  (Y**/n) y** 
```

1.  通过选择 linter 和 formatter 来继续该过程。我们将选择`ESLint + Airbnb config`：

```js
?  Pick a linter / formatter config: (Use arrow keys) ESLint with error prevention only ❯ ESLint + Airbnb config   ESLint + Standard config 
  ESLint + Prettier
```

1.  选择 linter 的附加功能（这里是`Lint and fix on commit`）：

```js
?  Pick additional lint features: (Use arrow keys) Lint on save ❯ Lint and fix on commit
```

1.  选择您想要放置 linter 和 formatter 配置文件的位置（这里是`In dedicated config files`）：

```js
?  Where do you prefer placing config for Babel, ESLint, etc.?  (Use arrow keys) ❯ In dedicated config files  **In package.json** 
```

1.  最后，CLI 将询问您是否要保存未来项目的设置；您将选择`N`。之后，Vue-CLI 将为您创建文件夹并安装依赖项：

```js
?  Save this as a preset for future projects?  (y/N) n
```

# 创建 Netlify 帐户

现在是时候开始将部署过程到 Netlify 平台。在这个教程中，我们将学习如何创建我们的 Netlify 账户，以便我们可以将我们的应用程序部署到网络上。

## 准备就绪

本教程的先决条件如下：

+   一个电子邮件地址

+   GitHub 账户

+   一个 GitLab 账户

+   一个 BitBucket 账户

在创建 Netlify 账户的过程中，您可以使用**电子邮件**地址、**GitHub**账户、**GitLab**账户或**BitBucket**账户来实现这一点。

## 如何做...

在这里，我们将学习如何使用电子邮件地址创建 Netlify 账户：

1.  转到 Netlify 网站[`www.netlify.com/`](https://www.netlify.com/)，并在页眉菜单中单击**注册→**。您将被重定向到初始**注册**页面。

1.  在这个页面上，您可以选择您想要使用的注册 Netlify 的方法。在这个过程中，我们将继续使用电子邮件地址。点击**电子邮件**按钮，将被重定向到电子邮件注册表单。

1.  填写表单，使用您选择的电子邮件地址和密码。密码规则为最少 8 个字符。填写完表单后，点击**注册**按钮。然后，您将被重定向到成功页面。

1.  现在，您将在收件箱中收到一封验证电子邮件，您需要这封邮件才能继续使用 Netlify 平台。要继续，请打开您的电子邮件收件箱并检查 Netlify 的电子邮件。

1.  在您的电子邮件收件箱中，打开 Netlify 的电子邮件，然后点击**验证电子邮件**按钮。此时，将打开一个新窗口，您将能够使用最近注册的电子邮件和密码登录。

1.  在这里，您可以使用您在第 3 步选择的电子邮件地址和密码填写登录表单。之后，点击登录按钮，将被重定向到 Netlify 平台的主窗口。

1.  最后，您将发现自己在 Netlify 平台的主屏幕上，有一个空白页面可以开始在平台上部署。

## 它是如何工作的...

在这个教程中，我们学习了如何在 Netlify 上创建我们的账户。我们看到可以使用各种 OAuth 方法和我们在教程中使用的基本电子邮件来实现这一点。

电子邮件地址创建过程涉及定义将要使用的电子邮件地址和账户密码，验证账户电子邮件。然后，您可以登录到平台。

## 另请参阅

在[`docs.netlify.com/`](https://docs.netlify.com/)找到更多关于 Netlify 的信息。

# 为在 Netlify 中部署应用程序做准备

要开始部署过程，我们需要配置我们的项目以具有有效的 Netlify 部署模式。在这个食谱中，您将学习如何在任何基于 Vue 的应用程序上设置 Netlify 部署模式。

## 做好准备

这个食谱的先决条件如下：

+   Node.js 12+

+   一个 Vue 项目

所需的 Node.js 全局对象如下：

+   `@vue/cli`

+   `@vue/cli-service-global`

## 如何做…

在这个食谱中，我们将学习如何准备我们的应用程序以部署到 Netlify：

1.  打开您的 Vue 项目并打开`package.json`文件。检查是否已定义`build`脚本，如下例所示：

```js
"scripts": {
  "serve": "Vue-CLI-service serve",
  "build": "Vue-CLI-service build",
  "lint": "Vue-CLI-service lint" },
```

1.  打开 Terminal（macOS 或 Linux）或 Command Prompt/PowerShell（Windows）并执行以下命令：

```js
> npm run build
```

1.  确保您的应用程序的`build`脚本在主文件夹中创建一个`dist`文件夹。

1.  如果您的`vue-router`被定义为与历史模式一起工作，您必须在`public`文件夹中创建一个`_redirects`文件。在这个文件中，您需要添加指示给 Netlify 路由器的指令：

```js
# Netlify settings for single-page application
/*    /index.html   200
```

1.  将您的应用程序发布到 GitHub 存储库。不用担心构建文件夹，因为它已经在`.gitignore`文件中声明，并且不会发送到您的存储库。

## 它是如何工作的…

在这个食谱中，我们学习了如何检查和准备我们的应用程序进行 Netlify 部署。

为了使部署工作，我们需要确保在`package.json`的脚本部分中有`build`命令，并验证构建目标是`dist`文件夹。

最后，我们在 public 文件夹中创建了一个`_redirects`文件，以指示 Netlify 路由器理解 vue-router 的历史模式。

## 另请参阅

在[`cli.vuejs.org/guide/deployment.html#netlify`](https://cli.vuejs.org/guide/deployment.html#netlify)上查找有关 Netlify 部署的官方 Vue-CLI 文档的更多信息。

在[`docs.netlify.com/routing/redirects/rewrites-proxies/#history-pushstate-and-single-page-apps`](https://docs.netlify.com/routing/redirects/rewrites-proxies/#history-pushstate-and-single-page-apps)上查找有关 Netlify 路由器重写的更多信息。

# 为在 GitHub 上自动部署到 Netlify 做准备

是时候为部署做准备了。在这个食谱中，您将学习如何设置 Netlify 部署过程，以便在 GitHub 上自动获取并部署您的应用程序。

## 做好准备

这个食谱的先决条件如下：

+   一个 Netlify 账户

+   一个 Vue 项目

+   一个 GitHub 账户

## 如何做…

最后，在创建 Netlify 帐户，将项目发布到 GitHub 存储库并配置所有内容之后，现在是时候准备 Netlify 平台以在每次 GitHub 推送时执行自动部署了：

1.  转到 Netlify（[`www.netlify.com/`](https://www.netlify.com/)），登录并打开您的初始仪表板。在那里，您会找到一个来自 Git 的新站点按钮。您将被重定向到创建新站点页面。

1.  现在，您可以单击 GitHub 按钮打开一个新窗口，以在 GitHub 上进行 Netlify 授权并在那里继续该过程。

1.  使用您的 GitHub 帐户登录，然后您将被重定向到应用程序安装页面。

1.  在此页面上，您可以选择向 Netlify 提供对所有存储库的访问权限，或仅选择的存储库，但请确保使您的应用程序的存储库可用。

1.  在 GitHub 上完成 Netlify 的安装后，您在上一步中授予访问权限的存储库将可以在 Netlify 平台上选择。选择包含您的应用程序的存储库。

1.  完成创建过程，您需要选择用于自动部署的分支。然后，您需要填写应用程序中使用的构建命令，在我们的情况下是`npm run build`。打开包含构建文件的文件夹，在我们的情况下是`dist`文件夹，并单击“部署站点”按钮。

1.  最后，Netlify-CLI 将启动构建过程，并在构建完成且没有任何错误时发布您的应用程序。

## 工作原理...

Netlify 平台连接到您的 GitHub 帐户并安装为应用程序，从而可以访问选定的存储库。然后，在平台上，您可以选择要用于部署的存储库。选择存储库后，我们需要使用构建说明和构建目标文件夹配置 Netlify-CLI。最后，CLI 运行，我们的应用程序就可以在网络上运行了。

## 另请参阅

在[`docs.netlify.com/configure-builds/file-based-configuration/`](https://docs.netlify.com/configure-builds/file-based-configuration/)上查找有关高级 Netlify 部署的更多信息。

# 创建 Vercel 帐户

Vercel 是一个著名的平台，用于在网络上部署您的应用程序。使用 Vercel，您可以自动化与 GitHub、GitLab 和 BitBucket 的部署过程。在本教程中，我们将学习如何在 Vercel 平台上创建我们的帐户。

## 准备就绪

这个教程的先决条件只是以下选项之一：

+   一个 GitHub 账户

+   一个 GitLab 账户

+   一个 BitBucket 账户

## 如何做...

让我们开始在 Vercel 平台上的旅程。在这里，我们将学习如何在平台上创建我们的账户以开始我们的项目部署：

1.  打开 Vercel 网站（[`vercel.com/`](https://vercel.com/)），并点击顶部栏的注册按钮。您将被重定向到注册页面。

1.  在这里，您可以选择这些存储库管理器中的一个 - GitHub、GitLab 或 BitBucket。我们将继续点击**GitHub**按钮。选择注册方法后，您将被重定向到授权页面。

1.  在此页面上，您正在授权 Vercel 平台访问您账户上的信息。点击**授权**按钮后，您将被重定向回 Vercel 仪表板。

1.  最后，您已经创建了 Vercel 账户，并准备好使用。

## 它是如何工作的...

在这个教程中，我们进入了 Vercel 平台，并使用存储库管理器注册了它。我们能够创建我们的账户，并且现在可以通过存储库集成或 CLI 工具在平台上开始部署过程。

## 另请参阅

您可以在[`vercel.com/`](https://vercel.com/)找到有关 Vercel 的更多信息。

# 配置 Vercel-CLI 并部署您的项目

您已经创建了一个 Vercel 账户。现在是时候在您的项目上配置 Vercel-CLI，以便它可以在 Vercel 平台和 Web 上使用。

## 准备工作

这个教程的先决条件如下：

+   一个 Vercel 账户

+   一个 Vue 项目

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   `@vue/cli`

+   `@vue/cli-service-global`

+   `vercel`

要安装`vercel`，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows），并执行以下命令：

```js
> npm i -g vercel
```

## 如何做...

在这个教程中，我们将学习如何通过 Vercel-CLI 将我们的项目链接到 Vercel 平台，然后使用它部署平台：

1.  打开您的 Vue 项目，然后打开`package.json`文件。检查您是否定义了`build`脚本，如下例所示：

```js
"scripts": {
  "serve": "Vue-CLI-service serve",
  "build": "Vue-CLI-service build",
  "lint": "Vue-CLI-service lint" },
```

1.  确保您的应用构建脚本在主文件夹中创建一个`dist`文件夹。

1.  在您的`project`文件夹中，打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows），并执行以下命令：

```js
> vercel
```

这将提示您登录 Vercel 平台：

```js
> No existing credentials found. Please log in:
 Enter your email:
```

1.  输入与您用于登录 Vercel 的仓库管理器相关联的电子邮件地址。您将收到一封带有“验证”按钮的电子邮件；点击它以验证您的电子邮件地址：

1.  一旦您的电子邮件地址得到验证，您就可以使用`> vercel`命令在您的终端中部署应用程序。

1.  要将应用程序部署到网络上，我们需要在`project`文件夹中执行`> vercel`命令，并且在部署之前它会询问一些关于项目设置的问题。第一个问题将涉及项目路径：

```js
? Set up and deploy "~/Versionamento/Vue.js-3.0-Cookbook/chapter-
   14/14.5"? [Y/n] y
```

1.  现在它将要求您定义将部署项目的范围。当您在同一用户名下定义了多个帐户访问选项时使用。在大多数情况下，它只会有一个，您可以按*Enter*：

```js
? Set up and deploy "~/Versionamento/Vue.js-3.0-Cookbook/chapter-
   14/14.5"? y
? Which scope do you want to deploy to? ❯ **Heitor Ramon Ribeiro**  
```

1.  然后，它将要求链接到 Vercel 上的现有项目。在我们的情况下，这是一个全新的项目，所以我们将选择`n`：

```js
? Link to existing project? [Y/n] n
```

1.  您将被要求定义项目的名称（只允许小写字母数字字符和连字符）：

```js
? What's your project's name? vuejscookbook-12-5
```

1.  您现在需要定义项目源代码的位置。此位置是`package.json`文件所在的位置；在我们的情况下，这将是`./`文件夹，或者主项目文件夹：

```js
? In which directory is your code located? ./
```

1.  Vercel-CLI 将检测到项目是一个 Vue-CLI 项目，并将自动定义所有用于部署应用程序的命令和目录设置。在我们的情况下，我们将选择`n`。

```js
Auto-detected Project Settings (Vue.js): - Build Command: `npm run build` or `Vue-CLI-service build` - Output Directory: dist - Development Command: Vue-CLI-service serve --port $PORT ? Want to override the settings? [y/N] n
```

1.  一切设置完成后，CLI 将部署应用程序的第一个预览，并将发送一个链接以访问应用程序的预览。要将应用程序部署为生产就绪状态，您需要在 Terminal（macOS 或 Linux）或 Command Prompt/PowerShell（Windows）中执行以下命令：

```js
> vercel --prod
```

## 它是如何工作的...

在这个教程中，我们学习了如何将 Vercel-CLI 连接到与仓库管理器相关联的电子邮件地址的在线平台，并设置项目部署。

在这个过程中，我们学习了如何通过定义构建命令、分发文件夹和开发命令来配置 CLI 的高级选项。

最后，我们能够在将项目部署到生产环境之前获得项目的预览 URL。

## 另请参阅

您可以在[`vercel.com/docs/cli?query=CLI#getting-started`](https://vercel.com/docs/cli?query=CLI#getting-started)找到有关 Vercel-CLI 的更多信息。

您可以在[`vercel.com/docs/configuration?query=now.json#introduction/configuration-reference`](https://vercel.com/docs/configuration?query=now.json#introduction/configuration-reference)找到有关 Vercel 高级配置的更多信息。

# 准备在 GitHub 上使用 Vercel 进行自动部署

在上一个教程中，我们学习了如何使用 Vercel-CLI 将我们的应用程序部署到网络上，使用您的本地终端，但是可以将存储库管理器与 Vercel 平台集成，并通过任何推送或打开拉取请求自动部署。这就是我们将在这个教程中做的事情。

## 准备工作

本教程的先决条件如下：

+   Vercel 帐户

+   存储库管理器上的 Vue 项目

## 如何做...

在这个教程中，我们将学习如何将 Vercel 平台与存储库管理器集成，并进行自动部署：

1.  打开您的 Vercel 仪表板（[`vercel.com/dashboard`](https://vercel.com/dashboard)）并点击导入项目按钮。

1.  在导入项目页面上，点击**继续**按钮，位于来自 Git 存储库卡内。

1.  现在，Vercel 网站将询问持有您要导入的项目的存储库的用户是否是您的个人帐户。如果是，请点击**是**。如果不是，Vercel 将在开始该过程之前将该项目分叉到您的个人帐户。

1.  然后，Vercel 将询问您要将项目绑定到哪个帐户。在我们的情况下，这将是我们的个人帐户。选择它，然后点击**继续**按钮。

1.  您将被重定向到 GitHub 网页，以授予 Vercel 对您的存储库的访问权限。您可以授予对所有存储库的访问权限，或者只是您想要部署的存储库。在我们的情况下，我们将授予对我们帐户上所有存储库的访问权限。

1.  在您的 GitHub 帐户上安装 Vercel 应用程序后，您将被发送回 Vercel 网页。在这里，您可以定义您正在创建的项目的设置，包括项目名称、您正在使用的预设、构建说明和环境变量。Vercel 将自动检测到我们的项目是一个 Vue-CLI 项目，并为我们配置构建和部署设置。然后，点击**部署**按钮继续。

1.  Vercel 将启动第一个部署过程。完成后，Vercel 将为您提供应用程序的链接，以及一个打开仪表板的链接。

## 它是如何工作的...

Vercel 平台连接到您的 GitHub 帐户并安装为应用程序，从而可以访问选定的存储库。然后，在平台上，您可以选择要用于部署的存储库。

选择存储库后，您需要使用构建说明和构建目标文件夹配置 Vercel-CLI。

最后，CLI 运行，我们的应用程序在 Web 上运行起来了。

## 参见

在[`zeit.co/docs/v2/git-integrations`](https://zeit.co/docs/v2/git-integrations)找到有关 Vercel 与 Git 存储库集成的更多信息。

# 创建 Firebase 项目

Firebase 是由 Google 创建的一体化解决方案，旨在为开发人员提供专用的分析、通知、机器学习和云解决方案工具。他们提供的云解决方案之一是托管平台。

有了托管平台，我们可以在 Google 云服务器上托管我们的单页应用程序，并通过全球内容传送网络使其对所有人可用。

## 准备工作

此处的先决条件如下：

+   一个 Google 帐户

+   一个 Vue 项目

## 如何做...

在这个教程中，我们将学习如何创建我们的 Firebase 项目，以便我们可以将我们的应用程序部署到 Firebase 托管：

1.  打开 Firebase 主页（[`firebase.google.com/`](https://firebase.google.com)）并单击页眉菜单中的“登录”链接。如果您已经登录到您的 Google 帐户，请单击“转到控制台”链接。

1.  在控制台页面上，单击“创建项目”按钮以创建新的 Firebase 项目。

1.  Firebase 将要求输入项目名称（您只能使用字母数字字符和空格）。

1.  然后，Firebase 会询问您是否要在此项目中启用 Google Analytics。在我们的情况下，我们将禁用此选项。

1.  最后，您将被重定向到项目概览仪表板。

## 它是如何工作的...

在这个教程中，我们创建了我们的第一个 Firebase 项目。为此，我们首先登录到我们的 Google 帐户并转到 Firebase 控制台。在 Firebase 控制台上，我们创建了一个新项目，并在设置向导步骤中禁用了 Google Analytics 选项，因为我们在这个教程中不会使用附加的分析。最后，当我们完成设置向导时，我们的项目已经准备就绪。

## 参见

在[`firebase.google.com`](https://firebase.google.com)找到有关 Google Firebase 的更多信息。

# 配置 Firebase-CLI 并部署您的项目

要将我们的应用程序部署到 Firebase Hosting，我们需要使用 Firebase-CLI。CLI 将帮助打包文件并将它们发送到 Google Cloud 服务器的过程。

在本配方中，我们将学习如何配置 Firebase-CLI 以使用本地终端将应用程序部署到网络。

## 准备工作

本配方的先决条件如下：

+   一个 Google 帐户

+   一个 Vue 项目

+   一个 Firebase 项目

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   `@vue/cli`

+   `@vue/cli-service-global`

+   `firebase-tools`

安装`firebase-tools`，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> npm install -g firebase-tools
```

## 如何做...

在本配方中，我们将学习如何在我们的项目中设置 Firebase-CLI，并如何使用上一个配方中创建的项目对其进行初始化：

1.  在项目的根文件夹中打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> firebase login
```

1.  Firebase-CLI 将打开一个浏览器窗口，以便您登录到您的 Google 帐户，并允许 Firebase-CLI 访问您的 Google 帐户的部分。（如果浏览器没有自动打开，Firebase-CLI 上会出现一个链接，复制链接，然后粘贴到浏览器中继续。）

1.  在项目的根文件夹中打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> firebase init
```

1.  现在我们正在使用我们的项目初始化 CLI 的配置过程。对于 CLI 的第一个问题，我们将只使用**Hosting**功能，因此我们需要只选择`Hosting`：

```js
**? Which Firebase CLI features do you want to set up 
    for this folder?** 
 **Press space to select feature, then Enter to confirm 
    your choices.** 
  Database: Deploy Firebase Realtime Database Rules
  Firestore: Deploy rules and create indexes for Firestore
 Functions: Configure and deploy Cloud Functions ❯ Hosting: Configure and deploy Firebase Hosting sites
 Storage: Deploy Cloud Storage security rules Emulators: Set up local emulators for Firebase features
```

1.  然后，CLI 将询问我们要使用哪个 Firebase 项目。在我们的情况下，我们在上一个配方中早些时候创建了项目，因此我们将选择`使用现有项目`：

```js
? Use an existing project ❯ Use an existing project
  Create a new project
  Add Firebase to an existing Google Cloud Platform project
  Don't set up a default project
```

1.  现在将显示您帐户上可用项目的列表。选择要与此应用程序一起部署的项目：

```js
? **Select a default Firebase project for this directory: (Use arrow** 
   **keys)** ❯ vue-3-cookbook-firebase-18921 (Vue 3 Cookbook Firebase)
```

1.  CLI 将询问应用程序的公共目录，或者在我们的情况下，因为它是单页面应用程序，我们需要使用构建目标文件夹。键入目标文件夹的名称，在我们的情况下是`dist`：

```js
? **What do you want to use as your project public directory?** **dist**
```

1.  最后，过程中的最后一步是选择是否要将配置用作单页面应用程序。键入`y`以启用所有 URL 的重写为`index.html`，以便我们可以使用`vue-router`的历史模式：

```js
? Configure as a single-page app (rewrite all urls to /index.html)? 
  (y/N) y
```

1.  打开项目根目录下的`package.json`文件，并添加一个新的脚本来自动化构建和部署过程：

```js
"scripts": {
  "serve": "Vue-CLI-service serve",
  "build": "Vue-CLI-service build",
  "deploy": "npm run build && firebase deploy",
  "lint": "Vue-CLI-service lint" },
```

1.  打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows），并在项目的根目录下执行以下命令：

```js
> npm run deploy
```

现在您的项目已经部署并可以在网络上访问，CLI 将为您提供访问链接：

![](img/ae1dd54b-f854-42d2-a206-7d58799dc231.png)

## 工作原理...

在这个教程中，我们学习了如何配置 Firebase CLI 并部署我们的应用程序。

首先，我们安装了 Firebase-CLI 并登录到 Google 身份验证平台。然后，我们能够在项目文件夹中初始化 CLI。

在这个过程中，我们选择了在上一个教程中创建的项目，并将构建文件夹指向 Vue-CLI 项目中的正确文件夹。

然后，我们配置了要使用单页面应用程序路由结构，并向`package.json`添加了部署脚本。最后，我们能够部署我们的应用程序并使其对所有人可用。

## 另请参阅

在[`firebase.google.com/docs/hosting`](https://firebase.google.com/docs/hosting)上找到有关 Firebase Hosting 的更多信息。
