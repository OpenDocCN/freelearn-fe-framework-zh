# 第三章：设置我们的聊天应用程序 - AWS Amplify 环境和 GraphQL

自从 Facebook 在 2012 年推出 GraphQL 以来，它就像飓风一样席卷了网络。大公司开始采用它，而中小型公司也看到了这种基于查询的 API 的潜力。

一开始看起来很奇怪，但随着您开始阅读和体验更多，您就不想再使用 REST API 了。简单性和数据获取能力使前端开发人员的生活变得更轻松，因为他们可以只获取他们想要的内容，而不必受限于只提供单个信息片段的端点。

这是一个漫长的配方的开始，所有的配方都将形成一个完整的聊天应用程序，但您可以在不需要编写整个章节的情况下，在配方中学习有关 GraphQL 和 AWS Amplify 的知识。

在本章中，我们将学习更多关于 AWS Amplify 环境和 GraphQL 的知识，以及如何将其添加到我们的应用程序并使其可用作通信驱动程序。

在本章中，我们将涵盖以下配方：

+   创建您的 AWS Amplify 环境

+   创建您的第一个 GraphQL API

+   将 GraphQL 客户端添加到您的应用程序

+   为您的应用程序创建 AWS Amplify 驱动程序

# 技术要求

在本章中，我们将使用 Node.js、AWS Amplify 和 Quasar Framework。

注意，Windows 用户！您需要安装一个名为`windows-build-tools`的 NPM 包，以便能够安装所需的软件包。要执行此操作，请以管理员身份打开 PowerShell 并执行以下命令：

`> npm install -g windows-build-tools`

要安装 Quasar Framework，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> npm install -g @quasar/cli
```

要安装 AWS Amplify，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> npm install -g @aws-amplify/cli
```

# 创建您的 AWS Amplify 环境

借助 AWS Amplify 的帮助，我们可以在几分钟内创建一个后端环境，其中包括 NoSQL 数据库、GraphQL 解析器和一个在线存储桶，供我们在开发后部署我们的应用程序。

为了创建 Vue 应用程序，我们将使用 Quasar Framework。这是一个基于 Vue 的框架，提供了开发应用程序所需的所有工具、结构和组件。

在这个配方中，我们将学习如何创建我们的 AWS 账户，在本地配置 AWS Amplify 环境，并使用 Quasar Framework 创建我们的初始项目。

## 准备就绪

这个教程的先决条件是 Node.js 12+。

所需的 Node.js 全局对象如下：

+   `@aws-amplify/cli`

+   `@quasar/cli`

## 如何做...

我们将把这个教程的任务分成四个部分：创建 AWS 账户，配置 AWS Amplify，创建您的 Quasar 项目，以及初始化 AWS Amplify 项目。

### 创建 AWS 账户

在这里，我们将学习如何在 AWS 门户上创建一个账户，以便我们可以访问 AWS 控制台：

1.  转到[`aws.amazon.com`](https://aws.amazon.com)。

1.  在网站上，点击“创建 AWS 账户”按钮。

1.  选择创建一个“专业”账户或一个“个人”账户（因为我们将要探索平台并为自己开发示例应用程序，最好选择“个人”账户）。

1.  现在亚马逊将要求您提供付款信息，以防您的使用超出了免费套餐限制。

1.  现在是确认您的身份的时候 - 您需要提供一个有效的电话号码，亚马逊将用它来发送您需要输入的 PIN 码。

1.  在收到 PIN 码后，您将看到一个成功的屏幕和一个“继续”按钮。

1.  现在您需要为您的账户选择一个计划；您可以选择此教程的“基本计划”选项。

1.  现在您已经完成，可以登录到您的 Amazon AWS 账户控制台。

### 配置 AWS Amplify

让我们配置本地 AWS Amplify 环境，以准备开始开发我们的聊天应用程序：

1.  要设置 AWS Amplify，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> amplify configure
```

1.  浏览器将打开，您需要登录到您的 AWS 控制台账户。

1.  登录后，返回终端并按*Enter*。CLI 将要求您选择您希望应用程序执行的服务器区域。建议在`us-east-1`上运行。

1.  选择区域后，CLI 将要求您为**身份和访问管理**（**IAM**）定义用户名。您可以按*Enter*使用默认值，也可以输入您想要的值（但必须是唯一的）。

1.  现在浏览器将打开以定义您指定的用户的用户详细信息。点击“下一步：权限”按钮转到下一个屏幕。

1.  点击“下一步：标签”按钮转到 AWS 标签屏幕。在这个屏幕上，点击“下一步：审核”按钮来审查您定义的设置。

1.  现在你可以点击“创建用户”按钮来创建用户并转到**访问密钥**屏幕。

1.  最后，在此屏幕上，等待访问密钥 ID 和秘密访问密钥可用。在浏览器中复制访问密钥 ID，粘贴到终端中，然后按“Enter”键。

1.  粘贴访问密钥 ID 后，您必须返回浏览器，点击秘密访问密钥上的“显示”链接，复制该值，粘贴到终端中，然后按“Enter”键。

1.  最后，您需要定义 AWS 配置文件名称（您可以通过按“Enter”键使用默认值）。

您现在已在计算机上设置了 AWS Amplify 环境。

### 创建您的 Quasar 项目

现在我们将创建 Quasar Framework 项目，这将是我们的聊天应用程序：

1.  要创建您的 Quasar Framework 应用程序，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> quasar create chat-app
```

1.  Quasar CLI 将要求输入项目名称；它需要是有效的 npm 软件包名称：

```js
> ? Project name (internal usage for dev) chat-app
```

1.  CLI 将要求输入产品名称（通常用于**渐进式 Web 应用程序**（**PWA**），混合移动应用程序和 Electron 应用程序）：

```js
? Project product name (must start with letter if building mobile 
  apps) Chat App
```

1.  之后，CLI 将要求输入项目描述，这将用于混合应用程序和 PWA：

```js
? Project description A Chat Application
```

1.  现在 CLI 将要求输入项目的作者。通常，这是您的 npm 或 Git 配置的作者：

```js
? Author Heitor Ramon Ribeiro <heitor.ramon@example.com>
```

1.  现在您可以选择 CSS 预处理器。我们将选择`Stylus`（您可以选择最适合您的预处理器）：

```js
? Pick your favorite CSS preprocessor: (can be changed later) 
  Sass with indented syntax (recommended) 
  Sass with SCSS syntax (recommended) 
❯ Stylus 
  None (the others will still be available)
```

1.  Quasar 有两种将组件、指令和插件导入构建系统的方法。您可以通过在`quasar.conf.js`中声明来手动执行，也可以通过自动导入您在代码中使用的组件、指令和插件来自动执行。我们将使用自动导入方法：

```js
? Pick a Quasar components & directives import strategy: (can be changed later) (Use arrow key s)
❯ * Auto-import in-use Quasar components & directives - slightly
    higher compile time; next to minimum bundle size; most 
     convenient 
  * Manually specify what to import - fastest compile time; minimum 
     bundle size; most tedious 
  * Import everything from Quasar - not treeshaking Quasar; biggest 
     bundle size; convenient
```

1.  现在我们必须选择要添加到项目中的默认功能；我们将选择`ESLint`、`Vuex`、`Axios`和`Vue-i18n`：

```js
? Check the features needed for your project: (Press <space> to select, <a> to toggle all, <i> to invert selection) 
❯ ESLint 
 Vuex 
  TypeScript 
 Axios 
 Vue-i18n 
  IE11 support
```

1.  现在您可以选择要在项目中使用的`ESLint`预设；在这种情况下，我们将选择`AirBnB`：

```js
? Pick an ESLint preset: (Use arrow keys) 
  Standard (https://github.com/standard/standard) 
❯ Airbnb (https://github.com/airbnb/javascript) 
  Prettier (https://github.com/prettier/prettier)
```

1.  您需要定义一个 Cordova/Capacitor ID（即使您不构建混合应用程序，也可以使用默认值）：

```js
? Cordova/Capacitor id (disregard if not building mobile apps) 
  org.cordova.quasar.app
```

1.  最后，您可以选择要运行的软件包管理器，并安装您需要运行代码的软件包：

```js
? Should we run `npm install` for you after the project has been 
  created? (recommended) (Use arrow keys) 
  Yes, use Yarn (recommended) 
❯ Yes, use NPM 
  No, I will handle that myself
```

### 初始化 AWS Amplify 项目

要初始化您的 AWS Amplify 项目，请执行以下步骤：

1.  打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows），进入项目文件夹，并执行以下命令：

```js
> amplify init
```

1.  Amplify CLI 将要求输入项目名称：

```js
? Enter a name for the project: chatapp
```

1.  然后，您需要为您的机器上正在运行的当前项目定义一个环境：

```js
? Enter a name for the environment: dev
```

1.  现在您可以选择您将在项目中使用的默认编辑器：

```js
? Choose your default editor: (Use arrow keys) 
❯ Visual Studio Code
  Atom Editor 
  Sublime Text
  InteliJ IDEA
  Vim (via Terminal, Mac OS only)
  Emac (via Terminal, Mac OS only)
  None
```

1.  您需要决定由 AWS Amplify 托管的项目类型。在我们的情况下，这将是一个 JavaScript 应用程序：

```js
? Choose the type of app that you're building? (recommended) (Use 
   arrow keys) 
  android 
  ios 
❯ javascript
```

1.  对于框架，因为我们将使用 Quasar Framework 作为基础，我们需要从所呈现的框架列表中选择“无”：

```js
? What javascript framework are you using? (recommended) (Use arrow 
  keys) 
  angular 
  ember
  ionic
  react
  react-native
  vue 
❯ none
```

1.  您将需要定义应用程序的源路径；您可以将源目录路径保留为默认值`src`。然后按*Enter*继续：

```js
? Source Directory Path: (src)
```

1.  对于分发目录，由于 Quasar 使用不同类型的路径组织，我们需要将其定义为`dist/spa`：

```js
? Distribution Directory Path: dist/spa
```

1.  AWS Amplify 将在部署之前使用的构建命令，我们将将其定义为`quasar build`：

```js
? Build Command: quasar build
```

1.  对于启动命令，我们需要使用 Quasar 内置的`quasar dev`命令：

```js
? Start Command: quasar dev
```

对于 Windows 用户，由于 Amplify 和 WSL 不兼容，您可能需要将启动命令定义如下：

```js
? Start Command: quasar.cmd dev
```

1.  现在 CLI 会询问我们是否要为此配置使用本地 AWS 配置文件：

```js
? Do you want to use an AWS profile: y
```

1.  我们将选择之前创建的默认配置文件：

```js
? Please choose the profile you want to use: (Use arrow keys) 
❯ default
```

1.  CLI 完成初始化过程后，我们需要向项目添加托管。为此，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows），进入项目文件夹，并执行以下命令：

```js
> amplify add hosting
```

1.  CLI 会询问您的应用程序的托管过程。选择“使用 Amplify Console 进行托管”，然后按*Enter*继续：

```js
? Select the plugin module to execute 
❯ Hosting with Amplify Console (Managed hosting with custom domains,
  Continuous deployment) 
  Amazon CloudFront and S3 
```

1.  然后 CLI 会询问您部署过程将如何进行；选择“手动部署”，然后按*Enter*继续：

```js
? Choose a type (Use arrow keys)
  Continuous deployment (Git-based deployments) 
❯ Manual deployment 
  Learn more 
```

1.  当您完成所有操作后，要完成此过程，您需要发布它。打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows），进入项目文件夹，并执行以下命令：

```js
> amplify publish
```

1.  您将被问及是否要继续发布，您可以接受。完成所有操作后，浏览器将打开默认的 Quasar Framework 首页：

![](img/bb0ed845-562a-45b8-bb24-0ced0a643194.png)

## 它是如何工作的...

AWS Amplify 是 Web 开发人员的一体化解决方案，提供了一整套工具，从托管应用程序到后端开发。

我们能够快速轻松地构建应用程序并将其上线，完全没有遇到基础设施方面的问题。

在这个步骤中，我们设法创建了我们的 AWS 账户，并为本地开发和网页部署准备好了我们的第一个 AWS Amplify 环境。此外，我们还能够创建了将用作聊天应用程序的 Quasar Framework 项目，并将其部署到 AWS 基础设施中，以准备应用程序的未来发布。

## 另请参阅

+   您可以在[`aws.amazon.com/amplify/`](https://aws.amazon.com/amplify/)找到有关 AWS Amplify 的更多信息。

+   您可以在[`docs.amplify.aws/`](https://docs.amplify.aws/)找到有关 AWS Amplify 框架的更多信息。

+   您可以在[`quasar.dev/`](https://quasar.dev/)找到有关 Quasar Framework 的更多信息。

# 创建您的第一个 GraphQL API

AWS Amplify 提供了在简单步骤和许多附加选项（包括身份验证、部署和环境）的情况下，开箱即用地拥有 GraphQL API 的可能性。这使我们能够仅使用 GraphQL SDL 模式快速开发 API，并且 AWS Amplify 将为连接构建 API、DynamoDB 实例和代理服务器。

在这个步骤中，我们将学习如何使用 AWS Amplify 创建 GraphQL API，并为身份验证添加 AWS Cognito 功能。

## 准备工作

此步骤的先决条件如下：

+   上一个步骤的项目

+   Node.js 12+

所需的 Node.js 全局对象是`@aws-amplify/cli`。

要安装 AWS Amplify，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows），并执行以下命令：

```js
> npm install -g @aws-amplify/cli
```

在这个步骤中，我们将使用*创建您的 AWS Amplify 环境*步骤中的项目。请先完成该步骤中的说明。

## 如何做...

要启动我们的 GraphQL API，我们将继续使用在*创建您的 AWS Amplify 环境*步骤中创建的项目。

这个步骤将分为两部分：创建 AWS Cognito 和创建 GraphQL API。

### 创建 AWS Cognito 身份验证

为了给我们的 API 和应用程序增加一层安全性，我们将使用 AWS Cognito 服务。这将提供对用户和身份验证的控制作为服务：

1.  要初始化您的 AWS Cognito 配置，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows），进入项目文件夹，并执行以下命令：

```js
> amplify auth add
```

1.  现在 CLI 会要求您选择用于创建 Cognito 服务的配置类型。这些是预先制定的规则和配置的选择。我们将选择`默认配置`：

```js
Do you want to use default authentication and security configuration: (Use arrow keys) 
❯ Default configuration  Default configuration with Social Provider (Federation)
  Manual configuration
  I want to learn more.
```

1.  之后，您需要选择用户将如何登录；因为我们正在构建一个聊天应用程序，我们将选择`电子邮件`：

```js
Warning: you will not be able to edit these selections.
How do you want users to be able to sign in: (Use arrow keys) 
  Username
❯ Email  Phone Number
  Email and Phone Number
  I want to learn more.
```

1.  对于 AWS Cognito，不需要选择更高级的设置。我们可以通过选择`不，我完成了。`来跳过这一步。

```js
Do you want to configure advanced settings: (Use arrow keys) 
❯ No, I am done.  Yes, I want to make some additional changes.
```

1.  最后，我们需要将这个配置推送到云端。为此，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows），进入项目文件夹，并执行以下命令：

```js
> amplify auth push
```

1.  您将被问及是否要继续 - 输入`y`，CLI 将发布配置到 AWS Cognito 云：

```js
? Are you sure you want to continue: y 
```

### 创建 GraphQL API

在这部分，我们将把说明分为两部分，首先创建 GraphQL SDL 模式，然后创建 GraphQL API。

#### 创建 GraphQL SDL 模式

要使用 AWS Amplify 创建 GraphQL API，首先需要创建一个 GraphQL SDL 模式。AWS Amplify 将使用该模式生成 API 的数据库和解析器：

1.  在`src`文件夹中创建一个名为`chatApi.graphql`的新文件，并打开它。

1.  创建我们基本的`S3Object`模式类型，这是一个简单的模型，用于管理放置在 AWS S3 存储桶中的文件的存储：

```js
type S3Object {
  bucket: String!
  region: String!
  key: String! } 
```

1.  然后我们将创建我们的`用户类型`。这就像一个带有更多规则附加的数据库模型。这个`类型`将有一个`@auth`规则，只允许所有者，在这种情况下是`用户`，执行`创建`、`更新`和`删除`操作。之后，我们将声明`用户`字段：

```js
type User
@model(subscriptions: null) @auth(rules: [
  { allow: owner, ownerField: "id", queries: null },
  { allow: owner, ownerField: "owner", queries: null },
]) {
  id: ID!
  email: String!
  username: String!
  avatar: S3Object
  name: String
  conversations: [ConversationLink] @connection(name: "UserLinks")
  messages: [Message] @connection(name: "UserMessages", keyField: "authorId")
  createdAt: String
  updatedAt: String }
```

1.  我们的`用户`将与另一个用户进行对话。我们将创建一个`对话类型`，为了保护这个对话，我们将添加一个`@auth`规则，以确保只有这个对话的成员可以看到用户之间交换的消息。在`messages`字段中，我们将创建一个与`消息类型`的`@connection`，并在关联字段中创建一个与`对话链接类型`的`@connection`：

```js
type Conversation
@model(
  mutations: { create: "createConversation" }
  queries: { get: "getConversation" }
  subscriptions: null ) @auth(rules: [{ allow: owner, ownerField: "members" }]) {
  id: ID!
  messages: [Message] @connection(name: "ConversationMessages",
   sortField: "createdAt")
  associated: [ConversationLink] @connection(name: 
   "AssociatedLinks")
  name: String!
  members: [String!]!
  createdAt: String
  updatedAt: String }
```

1.  对于`消息类型`，我们需要添加一个`@auth`装饰器规则，只允许所有者对其进行操作。我们需要创建一个`@connection`装饰器，将`author`字段连接到`用户类型`，并创建一个`@connection`装饰器，将`conversation`字段连接到`对话类型`：

```js
type Message
@model(subscriptions: null, queries: null) @auth(rules: [{ allow: owner, ownerField: "authorId", operations: [create, update, delete]}]) {
  id: ID!
  author: User @connection(name: "UserMessages", keyField: 
   "authorId")
  authorId: String
  content: String!
  conversation: Conversation! @connection(name: "ConversationMessages")
  messageConversationId: ID!
  createdAt: String
  updatedAt: String }
```

1.  现在我们正在使用`type ConversationLink`将对话链接在一起。这个`type`需要`user`字段具有`@connection`装饰器到`User`和`@connection`对话到`type Conversation`：

```js
type ConversationLink
@model(
  mutations: { create: "createConversationLink", update: 
"updateConversationLink" }
  queries: null
  subscriptions: null ) {
  id: ID!
  user: User! @connection(name: "UserLinks")
  conversationLinkUserId: ID
  conversation: Conversation! @connection(name: "AssociatedLinks")
  conversationLinkConversationId: ID!
  createdAt: String
  updatedAt: String }
```

1.  最后，我们需要创建一个`type Subscription`来在 GraphQL API 内部具有事件处理程序。`Subscription`类型会监听并处理特定变化的特定变化，`createConversationLink`和`createMessage`，两者都会在数据库内触发事件：

```js
type Subscription {
  onCreateConversationLink(conversationLinkUserId: ID!): 
   ConversationLink
  @aws_subscribe(mutations: ["createConversationLink"])
  onCreateMessage(messageConversationId: ID!): Message
  @aws_subscribe(mutations: ["createMessage"])
  onCreateUser: User
  @aws_subscribe(mutations: ["createUser"])
  onDeleteUser: User
  @aws_subscribe(mutations: ["deleteUser"])
  onUpdateUser: User
  @aws_subscribe(mutations: ["updateUser"]) }
```

#### 使用 AWS Amplify 创建 GraphQL API

在这里，我们将使用 AWS Amplify API 使用先前创建的 GraphQL 模式来创建我们的 GraphQL API：

1.  要初始化您的 AWS Amplify API 配置，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows），进入项目文件夹，并执行以下命令：

```js
> amplify add api
```

1.  在这里，CLI 将询问您要创建什么类型的 API。我们将选择`GraphQL`：

```js
? Please select from one of the below mentioned services: (Use arrow 
  keys) 
❯ GraphQL  REST
```

1.  现在 CLI 将要求输入 API 名称（您可以选择）：

```js
? Provide API name: chatapp
```

1.  在这里，我们将选择 API 将使用的身份验证方法。由于我们将使用 AWS Cognito，我们需要选择`Amazon Cognito User Pool`选项：

```js
? Choose the default authorization type for the API: (Use arrow
  keys) 
  API key
❯ Amazon Cognito User Pool  IAM
  OpenID Connect
```

1.  然后 CLI 将询问您是否要在 API 上配置更多设置；我们将选择`No, I am done.`选项：

```js
? Do you want to configure advanced settings for the GraphQL API:
  (Use arrow keys) 
❯ No, I am done.  Yes, I want to make some additional changes.
```

1.  现在我们将被问及是否有注释的 GraphQL 模式；由于我们之前已经编写了一个，我们需要输入`y`：

```js
? Do you have an annotated GraphQL schema?: y
```

1.  在这里，我们需要输入刚刚创建的文件的路径`./src/chatApi.graphql`：

```js
? Provide your schema file path: ./src/chatApi.graphql
```

1.  完成后，我们需要将配置推送到 AWS Amplify。要执行此操作，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows），进入项目文件夹，并执行以下命令：

```js
> amplify push
```

1.  当询问是否要继续时，输入`y`：

```js
? Are you sure you want to continue?: y
```

1.  CLI 将询问您是否要为新创建的 GraphQL API 生成代码；再次输入`y`：

```js
? Do you want to generate code for your newly created GraphQL API: y
```

1.  在这里，您可以选择 CLI 要使用的语言来创建项目中使用的通信文件。我们将选择`javascript`，但您可以选择最符合您需求的语言：

```js
? Choose the code generation language target: (Use arrow keys) 
❯ javascript
  typescript
  flow
```

1.  CLI 将询问要放置将生成的文件的位置，我们将使用默认值：

```js
? Enter the file name pattern of graphql queries, mutation and
  subscriptions: (src/graphql/***/**.js) 
```

1.  现在 CLI 将询问有关 GraphQL 操作的生成。由于我们正在创建我们的第一个 GraphQL API，我们将选择`y`，因此 CLI 将为我们创建所有文件：

```js
? Do you want to generate/update all possible GraphQL operations - 
  queries, mutations and subscriptions: y 
```

1.  最后，我们可以定义文件中模式的最大深度，我们将使用默认值`2`：

```js
? Enter maximum statement depth [increase from default if your 
  schema is deeply nested]: (2) 
```

1.  当你完成所有的事情后，我们需要将配置发布到 AWS Amplify。要做到这一点，你需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows），进入项目文件夹，并执行以下命令：

```js
> amplify publish 
```

## 工作原理...

在创建一个带有 AWS Amplify 的 GraphQL API 的过程中，我们需要一个预先构建的模式，用于生成数据库和端点。这个模式是基于 GraphQL SDL 语言的。Amplify 已经在 SDL 中添加了更多的装饰符，这样我们就可以在 API 的开发中拥有更广泛的可能性。

与此同时，我们需要创建一个 AWS Cognito 用户池，用于保存将在应用程序上注册的用户。这是为了在应用程序外部管理和维护身份验证层，并作为一个服务使用，可以提供更多功能，包括双因素身份验证、必填字段和恢复模式。

最后，在一切都完成之后，我们的 API 已经在 AWS Amplify 上发布，并准备好进行开发，具有可以用作开发环境的 URL。

## 另请参阅

+   你可以在[`graphql.org/learn/schema/`](https://graphql.org/learn/schema/)找到更多关于 GraphQL SDL 的信息。

+   你可以在[`docs.amplify.aws/lib/graphqlapi/getting-started/q/platform/js`](https://docs.amplify.aws/lib/graphqlapi/getting-started/q/platform/js)找到更多关于 AWS Amplify API 的信息。

+   你可以在[`docs.amplify.aws/lib/auth/getting-started/q/platform/js`](https://docs.amplify.aws/lib/auth/getting-started/q/platform/js)找到更多关于 AWS Amplify 身份验证的信息。

# 将 GraphQL 客户端添加到你的应用程序

Apollo Client 目前是 JavaScript 生态系统中最好的 GraphQL 客户端实现。它有一个庞大的社区支持，并得到了大公司的支持。

我们的 AWS Amplify GraphQL API 的实现在后端使用了 Apollo Server，因此 Apollo Client 的使用将是一个完美的匹配。AWS AppSync 也使用他们自己的 Apollo 实现作为客户端，所以我们仍然会使用 Apollo 作为客户端，但不是直接使用。

在这个配方中，我们将学习如何将 GraphQL 客户端添加到我们的应用程序中，以及如何连接到 AWS Amplify GraphQL 服务器来执行查询。

## 准备工作

这个配方的先决条件如下：

+   上一个配方的项目

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   `@aws-amplify/cli`

+   `@quasar/cli`

在这个示例中，我们将使用*创建您的第一个 GraphQL API*示例中的项目。在遵循本示例之前，请按照上一个示例中的步骤进行操作。

## 如何做...

我们将使用 Amplify 客户端将 GraphQL 客户端添加到我们的应用程序中。按照以下步骤创建 GraphQL 驱动程序：

1.  要安装使用 GraphQL 客户端所需的软件包，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> npm install --save graphql aws-amplify graphql-tag aws-appsync
```

1.  在`boot`文件夹中创建一个名为`amplify.js`的新文件，并打开它。

1.  在这个文件中，我们将导入`aws-amplify`包和 AWS Amplify CLI 在配置过程中为我们创建的`aws-exports.js`文件。我们将使用我们拥有的配置来配置 Amplify。为了使 Quasar 引导文件起作用，我们需要导出一个`default`空函数：

```js
import Amplify from 'aws-amplify';   import AwsExports from '../aws-exports';   Amplify.configure(AwsExports);   export default () => {}; 
```

1.  在`root`文件夹中的`quasar.conf.js`文件中，我们需要向`webpack`捆绑器添加新规则。要做到这一点，找到`extendWebpack`函数。在函数的第一行之后，创建两个新规则给捆绑器，第一个规则将添加`graphql-loader`webpack 加载程序，第二个规则将允许捆绑器理解`.mjs`文件：

```js
// The rest of the quasar.conf.js... extendWebpack (cfg) {
  //New rules that need to be added
 cfg.module.rules.push({
  test: /\.(graphql|gql)$/,
  exclude: /node_modules/,
  loader: 'graphql-tag/loader',
  });    cfg.module.rules.push({
  test: /\.mjs$/,
  include: /node_modules/,
  type: 'javascript/auto',
  });
 // Maintain these rules  cfg.module.rules.push({
  enforce: 'pre',
  test: /\.(js|vue)$/,
  loader: 'eslint-loader',
  exclude: /node_modules/,
  options: {
  formatter: 
       require('eslint').CLIEngine.getFormatter('stylish'),
  },
  });    cfg.resolve.alias = {
  ...cfg.resolve.alias,
  driver: path.resolve(__dirname, './src/driver'),
  }; }, // The rest of the quasar.conf.js...
```

1.  现在，在`src/driver`文件夹中创建一个名为`graphql.js`的新文件，并打开它。

1.  在这个文件中，我们需要从`aws-appsync`包中导入`AWSAppSyncClient`，从`aws-amplify`包中导入`Auth`，并从`src`文件夹中的`aws-exports.js`文件中导入`AwsExports`。然后，我们需要使用`aws-exports`的配置实例化`AWSAppSyncClient`，并导出客户端的这个实例化：

```js
import AWSAppSyncClient from 'aws-appsync'; import { Auth } from 'aws-amplify'; import AwsExports from '../aws-exports';   export default new AWSAppSyncClient({
  url: AwsExports.aws_appsync_graphqlEndpoint,
  region: AwsExports.aws_appsync_region,
  auth: {
  type: AwsExports.aws_appsync_authenticationType,
  jwtToken: async () => (await 
      Auth.currentSession()).idToken.jwtToken,
  }, }); 
```

1.  在`quasar.conf.js`文件中的`root`文件夹中，我们需要将新创建的`amplify.js`文件添加到引导序列中，该文件位于`boot`文件夹中。要做到这一点，找到`boot`数组，并在末尾添加文件在`boot`文件夹中的路径作为字符串，不包括扩展名。在我们的情况下，这将是`'amplify'`：

```js
// The rest of the quasar.conf.js... 
boot: [   'axios',
  'amplify' ], // The rest of the quasar.conf.js...  
```

## 它是如何工作的...

我们在全局范围内将`aws-amplify`包添加到我们的应用程序中，并通过新的`graphql.js`文件中的导出条目使其可用于使用。这使得在应用程序中可以使用`AWSAmplifyAppSync`。

使用 Quasar Framework 的引导过程，我们能够在 Vue 应用程序开始在屏幕上呈现之前实例化 Amplify。

## 另请参阅

+   您可以在[`docs.amplify.aws/lib/graphqlapi/getting-started/q/platform/js`](https://docs.amplify.aws/lib/graphqlapi/getting-started/q/platform/js)找到有关 AWS Amplify AppSync 的更多信息。

+   您可以在[`quasar.dev/quasar-cli/developing-ssr/writing-universal-code#Boot-Files`](https://quasar.dev/quasar-cli/developing-ssr/writing-universal-code#Boot-Files)找到有关 Quasar Framework 引导文件的更多信息。

# 为您的应用程序创建 AWS Amplify 驱动程序

为了与 AWS Amplify 服务进行通信，我们需要使用他们的 SDK。这个过程是重复的，可以合并到我们将要使用的每个 Amplify 服务的驱动程序中。

在这个示例中，我们将学习如何创建通信驱动程序，以及如何使用 AWS Amplify 进行操作。

## 准备工作

这个示例的先决条件如下：

+   上一个示例的项目

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   @aws-amplify/cli

+   `@quasar/cli`

在这个示例中，我们将使用*将 GraphQL 客户端添加到您的应用程序*示例中的项目。请先完成该示例中的说明。

## 如何做...

在这个示例中，我们将其分为三个部分：第一部分将用于 AWS 存储驱动程序，第二部分将用于 Amplify Auth 驱动程序，最后，我们将看到 Amplify AppSync 实例的创建。

### 创建 AWS Amplify 存储驱动程序

要创建 AWS Amplify 存储驱动程序，我们首先需要创建 AWS Amplify 存储基础设施，并在我们的环境中设置好，之后我们需要创建 AWS Amplify 存储 SDK 与我们的应用程序之间的通信驱动程序。

#### 添加 AWS Amplify 存储

在这部分，我们将向我们的 Amplify 服务列表中添加 AWS S3 功能。这是必需的，这样我们就可以在 AWS S3 云基础设施上保存文件：

1.  首先，我们需要向项目添加 AWS 存储。为此，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows），进入项目文件夹并执行以下命令：

```js
> amplify add storage
```

1.  现在我们需要选择将上传什么内容。我们需要选择`内容（图片、音频、视频等）`：

```js
? Please select from one of the below mentioned services: (Use arrow 
  keys) 
❯ Content (Images, audio, video, etc.)  NoSQL Database
```

1.  我们需要为资源添加一个名称。我们将其称为`bucket`：

```js
? Please provide a friendly name for your resource that will be used 
  to label this category in the project: bucket
```

1.  现在我们需要提供一个 AWS S3 存储桶名称。我们将其称为`chatappbucket`：

```js
? Please provide bucket name: chatappbucket 
```

1.  然后我们需要选择谁可以操作存储桶文件。由于应用程序将仅基于授权，我们需要选择`仅授权用户`：

```js
? Who should have access: (Use arrow keys) 
❯ Auth users only  Auth and guest users
```

1.  现在您需要选择用户在存储桶中的访问级别：

```js
? What kind of access do you want for Authenticated users? 
  create/update
  read
❯ delete
```

1.  当被问及创建自定义 Lambda 触发器时，选择`n`：

```js
? Do you want to add a Lambda Trigger for you S3 Bucket: n
```

1.  最后，我们需要将更改推送到云端。为此，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows），进入项目文件夹，并执行以下命令：

```js
> amplify push
```

1.  当您完成所有操作后，我们需要将配置发布到 AWS Amplify。为此，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows），进入项目文件夹，并执行以下命令：

```js
> amplify publish
```

#### 创建 Amplify Storage 驱动程序

在这部分，我们将创建与 Amplify Storage 通信的驱动程序。该驱动程序将处理我们应用程序中的文件上传：

1.  在`src/driver`文件夹中创建一个名为`bucket.js`的新文件并打开它。

1.  从`aws-amplify`包中导入`Storage`类，从`quasar`中导入`uid`函数，以及`AwsExports`：

```js
import { Storage } from 'aws-amplify'; import { uid } from 'quasar'; import AwsExports from '../aws-exports'; 
```

1.  创建一个名为`uploadFile`的异步函数，它接收三个参数：`file`，`name`和`type`。`name`参数的默认值为`uid()`，`type`参数的默认值为`'image/png'`。在这个函数中，我们将调用`Storage.put`函数，传递`name`和`file`作为参数，作为第三个参数，我们将传递一个 JavaScript 对象，其中`contentType`属性定义为接收到的`type`，并且`accept`属性定义为`'**/**'`。上传完成后，我们将返回一个具有`bucket`，`region`和`uploadedFile`属性的 JavaScript 对象：

```js
export async function uploadFile(file, name = uid(), type = 'image/png') {
  try {
  const uploadedFile = await Storage.put(name, file, {
  contentType: type,
  accept: '*/*',
  });    return {
  ...uploadedFile,
  bucket: AwsConfig.aws_user_files_s3_bucket,
  region: AwsConfig.aws_user_files_s3_bucket_region,
  };
  } catch (err) {
  return Promise.reject(err);
  } }
```

1.  创建一个名为`getFile`的异步函数，它接收`name`参数，默认值为空字符串。在函数内部，我们将返回`Storage.get`，传递`name`参数和设置为`public`级别的选项：

```js
export async function getFile(name = '') {
  try {
  return await Storage.get(name, { level: 'public' });
  } catch (err) {
  return Promise.reject(err);
  } } 
```

1.  最后，导出一个默认的 JavaScript 对象，并将创建的函数`uploadFile`和`getFile`作为属性添加进去：

```js
export default {
  uploadFile,
  getFile, };  
```

### 创建 Amplify Auth 驱动程序

现在我们将创建认证驱动程序。该驱动程序负责处理应用程序中的所有认证请求并获取用户信息：

1.  在`src/driver`文件夹中创建一个名为`auth.js`的新文件并打开它。

1.  在新创建的文件中，从`aws-amplify`包中导入`Auth`类：

```js
import { Auth } from 'aws-amplify';
```

1.  创建一个名为`signIn`的新异步函数。它将接收`email`和`password`作为参数，并且该函数将返回`Auth.signIn`函数，传递`email`和`password`作为参数：

```js
export async function signIn(email = '', password = '') {
  try {
  return Auth.signIn({
  username: email,
  password,
  });
  } catch (err) {
  return Promise.reject(err);
  } }
```

1.  创建一个名为`signUp`的新异步函数，该函数将接收`email`和`password`作为参数。该函数将返回`Auth.signUp`函数，传递一个带有这些属性的 JavaScript 对象作为参数：`username`、`password`、`attributes`和`validationData`。

`username`属性将是作为参数接收的`email`值。

`password`属性将是作为参数接收的`password`值。

`attributes`属性将是一个带有`email`属性的 JavaScript 对象，该属性将作为参数接收：

```js
export async function signUp(email = '', password = '') {
  try {
  return Auth.signUp({
  username: email,
  password: `${password}`,
  attributes: {
 email,
  },
  validationData: [],
  });
  } catch (err) {
  return Promise.reject(err);
  } }
```

1.  创建一个名为`validateUser`的新异步函数，该函数将接收`username`和`code`作为参数。该函数等待`Auth.confirmSignUp`函数的响应，将`username`和`code`作为参数传递给该函数，并在完成时返回`true`：

```js
export async function validateUser(username = '', code = '') {
  try {
  await Auth.confirmSignUp(username, `${code}`);    return Promise.resolve(true);
  } catch (err) {
  return Promise.reject(err);
  } }
```

1.  创建一个名为`resendValidationCode`的新异步函数，该函数将接收`username`作为参数。该函数返回`Auth.resendSignUp`函数，将`username`作为参数：

```js
export async function resendValidationCode(username = '') {
  try {
  return Auth.resendSignUp(username);
  } catch (err) {
  return Promise.reject(err);
  } } 
```

1.  创建一个名为`signOut`的新异步函数，该函数返回`Auth.signOut`函数：

```js
export async function signOut() {
  try {
  return Auth.signOut();
  } catch (err) {
  return Promise.reject(err);
  } }
```

1.  创建一个名为`changePassword`的新异步函数，该函数将接收`oldPassword`和`newPassword`作为参数。该函数等待获取当前经过身份验证的用户，并返回`Auth.changePassword`函数，将获取的`user`、`oldPassword`和`newPassword`作为参数：

```js
export async function changePassword(oldPassword = '', newPassword = '') {
  try {
  const user = await Auth.currentAuthenticatedUser();
  return Auth.changePassword(user, `${oldPassword}`, `${newPassword}`);
  } catch (err) {
  return Promise.reject(err);
  } }
```

1.  创建一个名为`getCurrentAuthUser`的新异步函数；该函数将获取当前经过身份验证的用户，并返回一个带有`id`、`email`和`username`属性的 JavaScript 对象：

```js
export async function getCurrentAuthUser() {
  try {
  const user = await Auth.currentAuthenticatedUser();    return Promise.resolve({
  id: user.username,
  email: user.signInUserSession.idToken.payload.email,
  username: user.username,
  });
  } catch (err) {
  return Promise.reject(err);
  } } 
```

### 创建 Amplify AppSync 实例

在经过身份验证的情况下与 AWS Amplify API 通信，我们需要创建一个新的 AWS Amplify AppSync API 实例，其中包含用户身份验证信息：

1.  在`src/driver`文件夹中创建一个名为`appsync.js`的新文件并打开它。

1.  在新创建的文件中，从`aws-amplify`包中导入`Auth`和`API`，从`@aws-amplify/api`包中导入`GRAPHQL_AUTH_MODE`枚举，以及 AWS 配置：

```js
import { Auth, API } from 'aws-amplify'; import { GRAPHQL_AUTH_MODE } from '@aws-amplify/api'; import AwsExports from '../aws-exports';
```

1.  通过执行`API.configure`函数从`aws-amplify`包中配置 API，传递一个 JavaScript 对象作为参数，其中包含`url`、`region`和`auth`的属性。

在`url`属性中，传递 GraphQL 端点 URL 的配置。

在`region`属性中，传递当前正在使用的 AWS 区域的配置。

在`auth`属性中，我们需要传递一个具有两个属性`type`和`jwtToken`的 JavaScript 对象。

我们需要将`type`属性设置为`GRAPHQL_AUTH_MODE.AMAZON_COGNITO_USER_POOLS`。

在`jwtToken`中，我们将传递一个异步函数，该函数将返回当前登录用户的令牌：

```js
API.configure({
  url: awsconfig.aws_appsync_graphqlEndpoint,
  region: awsconfig.aws_appsync_region,
  auth: {
  type: GRAPHQL_AUTH_MODE.AMAZON_COGNITO_USER_POOLS,
  jwtToken: async () => (await Auth.currentSession()).getIdToken().getJwtToken(),
  }, });
```

1.  最后，我们将`API`导出为名为`AuthAPI`的常量：

```js
export const AuthAPI = API;
```

## 工作原理...

在这个示例中，我们学习了如何将应用程序的责任分离为可以在多个领域重复使用而无需重写整个代码的驱动程序。通过这个过程，我们能够创建一个用于 Amplify 存储的驱动程序，可以异步发送文件，并且这些文件被保存在 AWS S3 服务器上的存储桶中。

在我们对 Auth 驱动程序的工作中，我们能够创建一个可以管理 Amplify 身份验证 SDK 并在需要时提供信息并封装特殊功能以使在我们的应用程序中执行任务更容易的驱动程序。

最后，在 Amplify AppSync API 中，我们成功实例化了 API 连接器，并使用了所有需要的身份验证标头，以便应用程序可以在没有任何问题的情况下执行，并且用户可以在请求时访问所有信息。

## 另请参阅

+   在[`docs.amplify.aws/lib/storage/getting-started/q/platform/js`](https://docs.amplify.aws/lib/storage/getting-started/q/platform/js)上查找有关 AWS Amplify Storage 的更多信息。

+   在[`docs.amplify.aws/lib/auth/getting-started/q/platform/js`](https://docs.amplify.aws/lib/auth/getting-started/q/platform/js)上查找有关 AWS Amplify Auth 的更多信息。

+   在[`docs.amplify.aws/lib/graphqlapi/getting-started/q/platform/js`](https://docs.amplify.aws/lib/graphqlapi/getting-started/q/platform/js)上查找有关 AWS Amplify AppSync 的更多信息。
