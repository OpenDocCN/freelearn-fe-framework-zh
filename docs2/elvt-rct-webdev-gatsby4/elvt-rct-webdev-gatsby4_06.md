# *第五章*：与图片一起工作

在本章中，你将掌握将图片添加到你的 Gatsby 网站中的技巧。首先，我们将简要了解网络图片的历史，然后理解为什么导入图片并不像你想象中那么简单。接下来，我们将学习创建渐进式加载且性能优异的图片。

在本章中，我们将涵盖以下主题：

+   网络上的图片

+   `StaticImage` 组件

+   `GatsbyImage` 组件

+   覆盖 `gatsby-plugin-image` 的默认设置

+   从 CMS 获取图片

# 技术要求

要完成本章，你需要完成 *第三章*，*从任何地方获取和查询数据*。

本章的代码可以在 [`github.com/PacktPublishing/Elevating-React-Web-Development-with-Gatsby-4/tree/main/Chapter05`](https://github.com/PacktPublishing/Elevating-React-Web-Development-with-Gatsby-4/tree/main/Chapter05) 找到。

# 网络上的图片

你上次访问没有任何图片的网站是什么时候？你可能觉得这是一个很难回答的问题。图片是网站和我们的浏览体验的关键部分。我们使用图片来展示标志、产品、个人资料和营销，通过视觉媒介传达信息、吸引或激发情感。虽然图片在这些用例（以及更多用例）中非常出色，但它们是页面大小最大的单一贡献者。根据 HTTP Archive ([`httparchive.org/`](https://httparchive.org/))，桌面上的平均页面大小为 2,124 KB。图片占用了 973 KB 的空间，大约占总页面大小的 45%。

由于图片对我们浏览体验的至关重要性，我们无法摒弃它们。但当它们占据了页面大小的很大一部分时，我们应该尽我们所能确保它们得到优化、可访问，并且尽可能高效。较新版本的浏览器（包括 Chrome）内置了 **响应式图片** 功能。浏览器不再提供单个图片源，而是可以接受一个源集。浏览器使用这个集来根据设备的大小加载不同尺寸的图片。这确保浏览器永远不会加载比可用空间大的图片。开发者可以通过 **懒加载** 来优化图片，特别是在 React 中。懒加载是将图片的加载推迟到稍后的时间点的过程。这可能是初始加载之后，或者当它们在屏幕上变得可见时。使用这种技术，你可以提高你网站的速度，更好地利用设备的资源，并减少用户的流量消耗。

## Gatsby 中的图片

手动创建包含懒加载图片的高性能网站本身就是一个项目。幸运的是，Gatsby 有一个插件，当你生成响应式、优化后的图片时，它可以减轻你的痛苦——`gatsby-plugin-image`。

此插件包含两个具有特定功能的 React 图像组件，旨在在使用图像时为开发者和网站访客提供更好的用户体验：

+   加载设备查看网站的正确图像。

+   在图像加载时保持其位置，从而减少累积布局偏移。

+   使用懒加载策略来加快网站初始加载时间。

+   在图像加载时提供了多个占位符图像选项。你可以使图像模糊，或者使用图像的**可缩放矢量图形**（**SVG**）作为替代。

+   如果浏览器支持，支持新的图像格式，如**WebP**。

在下一节中，我们将开始探讨本章的两个图像组件中的第一个——`StaticImage`组件。

# `StaticImage`组件

当图像始终保持不变时，`StaticImage`组件使用最佳。它可能是你的网站标志，它在所有页面上都是相同的，或者是在博客文章末尾使用的个人照片，或者是在主页的英雄部分，或者在任何不需要动态图像的地方。

与大多数 React 组件不同，`StaticImage`组件在传递 props 时有某些限制。它不会接受和使用其父组件的任何 props。如果你需要这种功能，你将想要使用`GatsbyImage`组件。

为了了解我们如何利用`StaticImage`组件，我们将在主页的英雄部分实现一个图像：

1.  在你的`src`文件夹旁边创建一个`assets`文件夹。为了保持整洁，将图像与源代码分开是一个好的做法。我们将使用`assets`文件夹来存放任何视觉资产。

1.  在`assets`文件夹内创建一个名为`images`的文件夹。我们将使用这个文件夹来存储图像类型的视觉资产。

1.  在你的`images`文件夹中添加一个图像。图像文件类型必须是`.png`、`.jpg`、`.jpeg`、`.webp`或`.avif`格式。

1.  安装`gatsby-plugin-image`及其依赖项：

    ```js
    npm install gatsby-plugin-image gatsby-plugin-sharp
    gatsby-source-filesystem
    ```

    这些依赖项会生成其他 node 进程，与之前的安装相比，可能需要更长的时间来安装。

1.  更新你的`gatsby-config.js`文件，使其包含三个新的依赖项：

    ```js
    plugins: [
        'gatsby-plugin-image',
        'gatsby-plugin-sharp',
        'gatsby-transformer-sharp',
        // Other plugins
    ],
    ```

1.  将`StaticImage`组件导入到你的`index.js`文件中：

    ```js
    import { StaticImage } from "gatsby-plugin-image";
    ```

1.  在页面的渲染中使用`StaticImage`组件：

    ```js
    <StaticImage 
       src="img/sample-photo.jpeg" 
    />
    ```

    `src`属性应该是从当前文件到图像的相对路径，*不是*根目录。

1.  通过 props 修改通过渲染的图像：

    ```js
    <StaticImage
        src="img/sample-photo.jpeg"
        alt="A man smiling"
        placeholder="tracedSVG"
        layout="fixed"
        width={400}
        height={600}
    />
    ```

    让我们详细看看这些 props：

    +   `src`：此属性是从当前文件到图像的相对路径。

    +   `alt`：就像使用正常的`img`标签一样，确保包含一个带有描述图像的`alt`属性，以便你的图像保持可访问性。

    +   `Placeholder`：这个属性告诉组件在图像加载时显示什么。在这里，我们将其设置为`tracedSVG`，它使用由追踪图像创建的占位符 SVG 来填充图像将加载的位置，同时也给用户一个关于照片中形状的感觉。其他选项包括`blurred`、`dominantColor`和`none`。

    +   `layout`：这个属性决定了插件输出图像的大小及其缩放。在这里，我们将其设置为`fixed`——这意味着图像在渲染时将保持一致的大小。其他布局选项包括`constrained`，它接受最大高度或宽度并可以缩小，以及`fullWidth`，它也会缩放以适应容器，但不受最大高度或宽度的限制。

    +   `width`和`height`：我们可以使用这些属性来指定图像的宽度和高度。

        小贴士

        `StaticImage`也可以将其`src`属性作为远程源。任何指定为 URL 的图像将在构建时下载并缩放。使用远程图像而不是本地图像是保持你的存储库小的一种好方法，但应该记住，如果图像在存储库之外，Gatsby 并不知道该图像何时发生变化。如果远程服务器上的图像被更改，它只有在重新构建你的项目时才会更新。

现在我们已经了解了如何利用`StaticImage`组件，让我们将注意力转向`gatsby-plugin-image`的另一部分，并了解`GatsbyImage`组件。

# GatsbyImage 组件

如果你需要使用动态图像，例如嵌入在 Markdown 内容中的图像，那么你可以使用`GatsbyImage`组件。

让我们使用`GatsbyImage`组件为我们的 Markdown/MDX 博客文章添加英雄图像：

1.  安装`gatsby-transformer-sharp` npm 包：

    ```js
    npm install gatsby-transformer-sharp
    ```

1.  将一些图像添加到`assets/images`中，用作你博客文章的封面——每篇文章一个。

1.  更新你的`Gatsby-config.js`文件，使其包含你的`assets`源：

    ```js
    {
          resolve: 'gatsby-source-filesystem',
          options: {
            path: '${__dirname}/assets/images',
          },
     },
    ```

    与`StaticImage`不同，`GatsbyImage`要求图像被摄入我们的数据层。我们可以使用`gatsby-source-filesystem`插件来实现这一点，但需要提供我们图像的路径。

1.  对于每篇博客文章，修改文章文件的`frontmatter`，使其包含一个包含图像相对路径的`hero`键：

    ```js
    ---
    type: Blog
    title: My First Hackathon Experience
    desc: This post is all about my learnings from my
     first hackathon experience in London.
    date: 2020-06-20
    placeholder Markdown with a hero key added. Be sure to replace the relative path in this example with the one to your image. 
    ```

1.  在`src/templates/blog-page.js`文件的顶部添加`GatsbyImage`组件和`getImage`函数作为导入：

    ```js
    import { GatsbyImage, getImage } from "gatsby-plugin-
    image";
    ```

1.  修改文件的页面查询以引用新的图像：

    ```js
    export const pageQuery = graphql'
      query($slug: String!) {
        blogpost: markdownRemark(fields: { slug: { eq:
          $slug } }) {
          frontmatter {
            date
            title
            tags
            gatsbyImageData function looks very similar to the props of the StaticImage component that we saw in *Step 8* of the previous section. In this instance, we are using the BLURRED placeholder for the image, which uses a blurred, lower-resolution version of the image in place of the original image while it is loading. We are now able to retrieve the hero data from the component as it is included in the page query.
    ```

1.  使用`data`属性中的新数据来获取组件内的图像：

    ```js
    const {
        blogpost: {
          frontmatter: { date, tags, title, hero from the data prop, and then use the getImage utility from gatsby-plugin-image to retrieve the image data that's required to render it and assign it to a const.
    ```

1.  在你的`return`语句中渲染你的图像：

    ```js
    <GatsbyImage image={const defined in *Step 7* to render the image within a GatsbyImage component. Be sure to provide it with alt text to keep your image accessible – you could provide this via frontmatter as well if you wish.
    ```

1.  启动或重新启动你的开发服务器，欣赏你的辛勤工作。导航到一篇博客文章，你应该会看到你的图像优雅地模糊。

    进一步练习

    我们已经学会了如何将图像添加到我们的博客页面，那么为什么不利用你所学的知识，将同一图像的较小版本添加到博客预览页面呢？如果您想了解如何实现，可以在本书的 GitHub 存储库中找到实现方法（[`github.com/PacktPublishing/Elevating-React-Web-Development-with-Gatsby-3/tree/main/Chapter05`](https://github.com/PacktPublishing/Elevating-React-Web-Development-with-Gatsby-3/tree/main/Chapter05)）。

您可能会发现自己需要在整个网站上的图像中添加相同的配置。让我们找出我们如何使用默认值来保持我们的代码以**不要重复自己**（**DRY**）的形式。

# 覆盖 gatsby-plugin-image 默认值

为了保持一致的外观和感觉，你可能已经在两个图像组件的许多实例中包含了相同的道具。如果你的网站图片很多，保持这些图像组件更新可能是一项单调的任务。相反，你可以在`gatsby-plugin-sharp`的选项中配置默认值：

```js
{
      resolve: 'gatsby-plugin-sharp',
      options: {
        defaults: {
 formats: ['auto', 'webp'],
 placeholder: 'blurred'
 quality: 70
 breakpoints: [300…]
 backgroundColor: 'transparent'
 tracedSVGOptions: {}
 blurredOptions: {}
 jpgOptions: {}
 pngOptions: {}
 webpOptions: {}
 avifOptions: {}
        }
      }
    },
```

让我们详细看看这些选项：

+   `formats`：插件生成的文件格式。

+   `placeholder`：覆盖临时图像的样式。

+   `quality`：创建的默认图像质量。

+   `breakpoints`：用于全宽图像的宽度。它永远不会创建一个宽度超过源图像的图像。

+   `backgroundColor`：图像的默认背景颜色。

+   `tracedSVGOptions`和`blurredOptions`：在占位符图像类型与全局默认值不同的情况下，用于占位符图像类型的默认选项。

+   `jpgOptions`、`pngOptions`、`webpOptions`和`avifOptions`：在占位符图像类型与全局默认值不同的情况下，用于图像类型的默认选项。

我们现在对`gatsby-plugin-image`包有了很好的理解。然而，使用此包与其他来源（如**内容管理系统**（**CMS**））结合使用时，还有一些重要的细分市场——让我们来看看。

# 从 CMS 获取图像

并非总是实际在您的存储库中存储图像。您可能希望其他人能够在不修改代码的情况下更新或添加图像到您的网站。在这些情况下，通过 CMS 提供图像更为可取。尽管如此，我们仍然需要使用 Gatsby 图像插件，因为我们希望无论来源如何，我们的图像都能有良好的性能。为了了解我们如何通过 CMS 集成图像，让我们用一个例子来说明。让我们使用`gatsby-plugin-image`和 CMS 在我们的*关于*页面添加一个个人资料图像。

重要提示

由于市场上存在大量的无头 CMS，我们将继续关注在*第三章*的*从无头 CMS 获取数据*部分中提到的两个：GraphCMS 和 Prismic。

以下两个部分将假设你已经安装了 CMS 的依赖项，并通过你的 `gatsby-config.js` 文件集成了 CMS。请只实现以下之一。

## 从 GraphCMS 获取图片

通过对我们的配置和查询进行一些小的修改，我们可以确保我们可以从 GraphCMS 获取使用 `gatsby-plugin-image` 的图片，并以与本地获取的图片相同的方式在网站上加载：

1.  导航到 GraphCMS 的网站 ([graphcms.com](https://graphcms.com)) 并登录。

1.  导航到你的项目资产并点击 **upload** 按钮。

1.  将你希望使用的本地图片拖放到页面上。请务必注意文件的名称，因为我们稍后会需要它。

1.  发布资产。

1.  修改你的 `gatsby-source-graphcms` 插件：

    ```js
    {
          resolve: 'gatsby-source-graphcms',
          options: {
            endpoint: process.env.GRAPHCMS_ENDPOINT,
            gatsby-source-graphcms plugin's options to include the downloadLocalImages option, the plugin will download and cache the CMS's image assets within your Gatsby project.
    ```

1.  修改你的关于页面上的 `query`，使其包含 `graphCmsAsset` 源：

    ```js
    export const query = graphql'
      {
        markdownRemark(frontmatter: { type: { eq: "bio" } }) {
          html
        }
        gatsbyImageData within our query and can make use of any of the configuration options that it supports. Here, we are specifying that the image should be full width.
    ```

1.  在 `src/pages/about.js` 文件的顶部添加 `GatsbyImage` 组件和 `getImage` 函数作为导入：

    ```js
    import { GatsbyImage, getImage } from "gatsby-plugin-
    image";
    ```

1.  使用 `data` 属性中的新数据来获取组件内的图片：

    ```js
    const {
        markdownRemark: { html },
        graphCmsAsset: { localFile },
      } = data;
      const profileImage = getImage(localFile);
    ```

    首先，从 `data` 属性中检索 `graphCmsAsset`，然后使用 `gatsby-plugin-image` 的 `getImage` 工具检索渲染图片所需的数据。最后，将其分配给名为 `profileImage` 的 `const`。

1.  在你的 `return` 语句中渲染你的图片：

    ```js
    return (
        <Layout>
          <div className="max-w-5xl mx-auto py-16 lg:py-24
            text-center">
            const parameter that we defined in *Step 8* to render the image within a GatsbyImage component. Be sure to provide it with alt text to keep your image accessible – you could provide this via frontmatter as well if you wish.
    ```

1.  重新启动你的开发服务器，并通过导航到你的 *about* 页面来欣赏你的辛勤工作。

现在我们已经了解了如何在 GraphCMS 中获取图片，让我们来看看如何在 Prismic 中实现同样的效果。

## 从 Prismic 获取图片

通过对我们的配置和查询进行一些简单的更改，我们可以使用 `gatsby-plugin-image` 从 Prismic 获取图片，并以与本地图片相同的方式在网站上加载它们：

1.  导航到 Prismic 的网站 ([prismic.io](https://prismic.io)) 并登录。选择你的现有存储库。

1.  导航到 **CustomTypes** 选项卡，点击 **Create new** 按钮，并选择 **Single Type**。将你的类型命名为 **Profile** 并提交。

1.  使用 **build-mode** 侧边栏（在右侧），将一个图片组件拖入类型中。

1.  将字段命名为 **photo**；相应的 API ID 应该会自动填充。点击 **OK** 以确认。如果你操作正确，你的个人资料类型应该看起来如下所示：![图 5.1 – Prismic 个人资料类型

    ![图 5.1 – Prismic 个人资料类型

    图 5.1 – Prismic 个人资料类型

1.  保存文档。

1.  导航到 JSON 编辑器并将内容复制到你的 `src/schemas` 文件夹中名为 `profile.json` 的新文件中。

1.  导航到 **Documents** 选项卡并点击 **Create new** 按钮。如果 Prismic 没有自动打开你的新类型，请选择 **Profile**。使用界面，在 **photo** 字段中上传新的图片到文档中。

1.  保存并发布新文档。我们现在已经在 CMS 中设置了一切，可以返回到我们的 Gatsby 项目。

1.  在你的 `gatsby-config.js` 文件中更新 `gatsby-source-prismic` 的配置：

    ```js
    {
          resolve: "gatsby-source-prismic",
          options: {
            repositoryName: "elevating-gatsby",
            schemas: {
              icebreaker:
               require("./src/schemas/icebreaker.json"),
              schemas that we added in *Step 6*. We will also add the shouldDownloadFiles option. This is a function that determines whether to download images. In our case, we always want it to download images so that we can use gatsby-plugin-image, and therefore set the function to always return true.
    ```

1.  在`src/pages/about.js`文件的顶部添加`GatsbyImage`组件和`getImage`函数作为导入：

    ```js
    import { GatsbyImage, getImage } from "gatsby-plugin-
    image";
    ```

1.  修改您的关于页面的`query`，使其包括`prismicProfile`源：

    ```js
    export const query = graphql'
      {
        markdownRemark(frontmatter: { type: { eq: "bio" }
     }) {
          html
        }
        prismicProfile {
          data {
            photo {
              localFile {
                childImageSharp {
                  gatsbyImageData(layout: FULL_WIDTH)
                }
              }
            }
          }
        }
      }
    ';
    ```

    我们在`query`中使用`gatsbyImageData`并可以利用它支持的任何配置选项。在这里，我们指定图像应全宽显示。

1.  使用`data`属性中的新数据在组件内获取图像：

    ```js
    const {
        markdownRemark: { html },
        prismicProfile: {
          data: {
            photo: { localFile },
          },
        },
      } = data;
      const profileImage = getImage(localFile);
    ```

    首先，从`data`属性中检索`prismicProfile`数据，然后使用`gatsby-plugin-image`中的`getImage`实用工具来检索渲染图像所需的图像数据，并将其分配给名为`profileImage`的`const`。

1.  在您的`return`语句中渲染您的图像：

    ```js
    <GatsbyImage
              image={profileImage}
              alt="Your alt text"
              className="mx-auto max-w-sm"
            />
    ```

    使用在*步骤 12*中定义的`const`参数在`GatsbyImage`组件中渲染图像。确保提供`alt`文本以保持图像的可访问性——如果您愿意，也可以通过`frontmatter`提供。

1.  重新启动您的开发服务器，通过导航到您的*关于*页面来欣赏您的辛勤工作。

您现在应该能够舒适地使用图像并通过 Prismic CMS 获取图像。虽然我们只看了两个 CMS，但这些概念可以应用到任何支持图像的无头 CMS 中。现在，让我们总结一下本章所学的内容。

# 摘要

在本章中，我们学习了网页上的图像以及它们对我们浏览体验的重要性。我们学习了`gatsby-plugin-image`并实现了其中包含的两个图像组件。我们还学习了在什么情况下使用哪个组件。

到目前为止，您应该能够舒适地开发各种类型的 Gatsby 网站页面。在下一章中，我们将开始探讨如何将我们的开发网站上线。我们将从查看**搜索引擎优化**开始这一旅程。
