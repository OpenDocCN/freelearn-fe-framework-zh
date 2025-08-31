# 前言

本书通过结合 React 的强大功能和经过行业验证的服务端技术（如 Node.js、Express 和 MongoDB）来探讨全栈 JavaScript Web 应用的开发。JavaScript 领域已经迅速发展了一段时间。在有关全栈 JavaScript Web 应用方面，有大量的选项和资源可用，当你需要从这些经常变化的实体中选择、了解它们并使它们协同工作以构建自己的 Web 应用时，很容易感到迷茫。为了解决这个痛点，本书采用了一种实用方法，帮助你使用流行的 MERN 框架搭建和构建各种工作应用。

# 本书面向的对象

本书面向的是可能使用过 React 但全栈开发（涉及 Node.js、Express 和 MongoDB）经验有限的 JavaScript 开发者。

# 本书涵盖的内容

第一章，*利用 MERN 释放 React 应用的力量*，介绍了 MERN 框架技术以及本书将要开发的应用。我们将讨论使用 React、Node.js、Express 和 MongoDB 开发 Web 应用。

第二章，*准备开发环境*，帮助你设置 MERN 框架技术以进行开发。我们将探讨基本开发工具；安装 Node.js、MongoDB、Express、React 以及任何其他必需的库；然后运行代码以检查设置。

第三章，*使用 MongoDB、Express 和 Node 构建后端*，实现了 MERN 框架应用的骨架后端。我们将构建一个使用 MongoDB、Express 和 Node.js 的独立服务器端应用，存储用户详细信息，并提供用户认证和 CRUD 操作的 API。

第四章，*添加 React 前端以完善 MERN*，通过集成 React 前端来完善 MERN 框架应用。我们将实现一个具有 React 视图的前端，用于与服务器上的用户 CRUD 操作和认证 API 交互。

第五章，*将骨架扩展为社交媒体应用*，通过扩展骨架应用来构建一个社交媒体应用。我们将通过实现社交媒体功能（如帖子分享、点赞、评论、关注朋友和聚合新闻源）来探索 MERN 框架的能力。

第六章，*构建基于 Web 的课堂应用*，专注于通过扩展 MERN 框架骨架应用来构建一个简单的在线课堂应用。这个课堂应用将支持多个用户角色，添加课程内容和课程，学生注册，进度跟踪以及课程注册统计。

第七章，*使用在线市场锻炼 MERN 技能*，利用 MERN 框架技术开发在线市场应用的基本功能。我们将实现买卖相关功能，包括卖家账户、产品列表和按类别搜索产品。

第八章，*扩展市场以支持订单和支付*，专注于通过实现买家添加产品到购物车、结账和下订单的能力，以及卖家管理这些订单和从市场应用中处理支付的能力来扩展我们在上一章中构建的在线市场。我们还将集成 Stripe 以收集和处理支付。

第九章，*为市场添加实时竞价功能*，专注于教你如何使用 MERN 框架技术以及 Socket.IO，轻松集成全栈应用中的实时行为。我们将通过在 MERN 市场应用中集成具有实时竞价功能的拍卖功能来实现这一点。

第十章，*将数据可视化集成到支出跟踪应用中*，专注于使用 MERN 框架技术以及 Victory——一个用于 React 的图表库，以轻松集成全栈应用中的数据可视化功能。我们将扩展 MERN 框架基础应用以构建一个支出跟踪应用，该应用将包含用户随时间记录的支出数据处理和可视化功能。

第十一章，*构建媒体流应用*，专注于扩展 MERN 框架基础应用以构建一个使用 MongoDB GridFS 的媒体上传和流应用。我们将从构建一个基本的媒体流应用开始，允许注册用户上传视频文件，这些文件将被存储在 MongoDB 中并通过流回放，以便观众可以在简单的 React 媒体播放器中播放每个视频。

第十二章，*定制媒体播放器和优化 SEO*，通过定制媒体播放器和自动播放媒体列表升级我们的媒体应用观看能力。我们将实现自定义控制默认 React 媒体播放器，添加一个可以自动播放的播放列表，并通过添加仅针对媒体详情视图的数据的选区服务器端渲染来优化媒体详情的 SEO。

第十三章，*开发基于 Web 的 VR 游戏*，使用 React 360 开发一个三维**虚拟现实**（**VR**）游戏。我们将探索 React 360 的三维和 VR 功能，并构建一个简单的基于 Web 的 VR 游戏。

第十四章，*使用 MERN 使 VR 游戏动态化*，您将通过扩展 MERN 骨架应用程序并集成 React 360 来构建一个动态的 VR 游戏应用程序。我们将实现一个游戏数据模型，使用户能够创建自己的 VR 游戏，并将动态游戏数据与使用 React 360 开发的游戏相结合。

第十五章，*遵循最佳实践并进一步开发 MERN*，回顾了前几章学到的经验教训，并提出了进一步基于 MERN 的应用程序开发的改进建议。我们将扩展一些已经应用的最佳实践，例如应用程序结构中的模块化，其他应该应用的做法，例如编写测试代码，以及可能的改进，例如优化包大小。

# 为了充分利用这本书

本书假设您熟悉基本的网络技术，了解 JavaScript 中的编程结构，并对 React 应用程序的工作原理有一个大致的了解。随着您阅读本书，您将发现这些概念如何在构建使用 React 16.13.1、Node.js 13.12.0、Express 4.17.1 和 MongoDB 4.2.5 的完整功能网络应用程序时结合在一起。

为了在阅读章节时最大限度地提高您的学习体验，建议您并行运行相关应用程序代码，保持指定的包版本，并使用每章中提供的相关说明。

**如果您使用的是这本书的数字版，我们建议您自己输入代码或通过 GitHub 仓库（下一节中提供链接）访问代码。这样做将帮助您避免与代码复制粘贴相关的任何潜在错误。**

# 下载示例代码文件

您可以从[www.packt.com](http://www.packt.com)的账户下载本书的示例代码文件。如果您在其他地方购买了这本书，您可以访问[www.packtpub.com/support](https://www.packtpub.com/support)并注册，以便将文件直接通过电子邮件发送给您。

您可以通过以下步骤下载代码文件：

1.  在[www.packt.com](http://www.packt.com)登录或注册。

1.  选择“支持”标签。

1.  点击“代码下载”。

1.  在搜索框中输入书名，并遵循屏幕上的说明。

文件下载完成后，请确保使用最新版本的以下软件解压或提取文件夹：

+   Windows 版的 WinRAR/7-Zip

+   Mac 版的 Zipeg/iZip/UnRarX

+   Linux 版的 7-Zip/PeaZip

该书的代码包也托管在 GitHub 上，网址为[`github.com/PacktPublishing/Full-Stack-React-Projects-Second-Edition`](https://github.com/PacktPublishing/Full-Stack-React-Projects-Second-Edition)。如果代码有更新，它将在现有的 GitHub 仓库中更新。

我们还有其他来自我们丰富的书籍和视频目录的代码包，可在 [`github.com/PacktPublishing/`](https://github.com/PacktPublishing/) 上找到。查看它们吧！

# 使用的约定

在本书中使用了多种文本约定。

`CodeInText`：表示文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 处理。以下是一个示例：“将下载的 `WebStorm-10*.dmg` 磁盘映像文件作为系统中的另一个磁盘挂载。”

代码块按以下方式设置：

```js
addItem(item, cb) {
    let cart = []
    if (typeof window !== "undefined") {
      if (localStorage.getItem('cart')) {
        cart = JSON.parse(localStorage.getItem('cart'))
      }
      cart.push({
        product: item,
        quantity: 1,
        shop: item.shop._id
      })
      localStorage.setItem('cart', JSON.stringify(cart))
      cb()
    }
}
```

当我们希望您注意代码块中的特定部分时，相关的行或项目将以粗体显示：

```js
<Grid container spacing={24}>
      <Grid item xs={6} sm={6}>
            <CartItems checkout={checkout}
 setCheckout={showCheckout}/>
      </Grid>
 {checkout && 
      <Grid item xs={6} sm={6}>
        <Checkout/>
      </Grid>}
</Grid>
```

任何命令行输入或输出都按以下方式编写：

```js
yarn add --dev @babel/preset-react 
```

**粗体**：表示新术语、重要单词或您在屏幕上看到的单词。例如，菜单或对话框中的单词在文本中显示如下。以下是一个示例：“从管理面板中选择系统信息。”

警告或重要提示看起来像这样。

小技巧和技巧看起来像这样。

# 联系我们

我们欢迎读者的反馈。

**一般反馈**：如果您对本书的任何方面有疑问，请在邮件主题中提及书名，并通过 `customercare@packtpub.com` 邮箱联系我们。

**勘误**：尽管我们已经尽最大努力确保内容的准确性，但错误仍然可能发生。如果您在这本书中发现了错误，我们将不胜感激，如果您能向我们报告，我们将不胜感激。请访问 [www.packtpub.com/support/errata](https://www.packtpub.com/support/errata)，选择您的书籍，点击勘误提交表单链接，并输入详细信息。

**盗版**：如果您在互联网上以任何形式发现我们作品的非法副本，我们将不胜感激，如果您能提供位置地址或网站名称，我们将不胜感激。请通过 `copyright@packt.com` 联系我们，并提供材料的链接。

**如果您有兴趣成为作者**：如果您在某个领域有专业知识，并且您有兴趣撰写或为书籍做出贡献，请访问 [authors.packtpub.com](http://authors.packtpub.com/)。

# 评论

请留下评论。一旦您阅读并使用了这本书，为何不在您购买它的网站上留下评论呢？潜在读者可以查看并使用您的客观意见来做出购买决定，Packt 公司可以了解您对我们产品的看法，我们的作者也可以看到他们对书籍的反馈。谢谢！

有关 Packt 的更多信息，请访问 [packt.com](http://www.packt.com/)。
