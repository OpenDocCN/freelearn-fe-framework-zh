# 15

# 高级会话管理

会话管理对于构建良好的用户体验至关重要。通过记住用户设置、选择和偏好，持久化会话数据可以提高用户体验和生产力。

我们在*第八章*，*会话管理*中学习了如何管理用户会话。在本章中，我们将研究高级会话管理模式。

本章分为两个部分：

+   管理访客会话

+   实现分页

首先，我们将实现访客会话，并使用 Remix 的 cookie 辅助函数在登录或注册后重定向用户到正确的页面。接下来，我们将学习如何使用 Remix 和 Prisma 添加分页。我们将通过将分页应用于 BeeRich 中的费用和发票列表来练习分页。

在阅读本章后，您将了解如何使用 cookie 在 Remix 中持久化任意会话数据。您还将理解 Remix 的会话 cookie 和 cookie 辅助函数之间的区别。此外，您还将学习何时将会话数据存储在 cookie 中，而不是数据库中。最后，您将了解如何使用 Remix 实现分页。

# 技术要求

您可以在此处找到本章的代码：[`github.com/PacktPublishing/Full-Stack-Web-Development-with-Remix/tree/main/15-advanced-session-management`](https://github.com/PacktPublishing/Full-Stack-Web-Development-with-Remix/tree/main/15-advanced-session-management)。本章不需要额外的设置。

# 管理访客会话

在*第八章*，*会话管理*中，我们使用了 Remix 的会话 cookie 辅助函数来实现登录和注册流程。在本节中，我们将使用 Remix 的 cookie 辅助函数来持久化额外的会话数据。

您可能还记得在*第八章*，*会话管理*中，cookie 是通过**Set-Cookie**头在服务器上添加到 HTTP 响应中的。一旦收到，浏览器会使用**Cookie**头将 cookie 附加到所有后续的 HTTP 请求中。

在 Remix 中，我们可以在`loader`和`action`函数中访问传入的 HTTP 请求。在我们的加载器和操作中，我们可以使用 Remix 的 cookie 辅助函数来解析请求头中的 cookie 数据，并使用它来提升用户体验。

在 BeeRich 中，我们已利用 cookie 来处理用户的身份验证。然而，cookie 还有许多其他用例。

考虑以下高级用例：我们旨在向访客提供我们应用程序功能的一瞥，而不需要账户。访客应能够直接与内容互动。在某个时刻，访客决定创建账户。现在，我们想要确保与访客关联的数据被转移到新的用户账户。我们如何实现这一点？

根据使用场景，会话数据可以使用本地存储、cookies、内存或数据库进行持久化。我们可以直接将所有生成数据存储在本地存储或 cookie 中，并在用户账户创建后一次性提交到数据库。然而，这种方法仅适用于数据不打算对其他用户可见的情况。

如果我们希望将访客生成的内容视为任何其他用户的内容？首先，我们必须为访客分配一个唯一的标识符，以便在不同页面转换之间进行跟踪。每当访客触发一个变更时，我们将持久化数据与唯一标识符关联。一旦访客注册，我们将与访客标识符关联的所有数据迁移到新的用户账户。

在处理会话时生成一个唯一的会话标识符是一个常见的模式，将其存储在 cookie 中是确保我们可以在服务器上访问标识符的好方法。这个例子说明了 cookie 有多么强大。cookie 可以用来实现复杂用户界面和功能。然而，cookie 也可以用来持久化短暂会话数据。

让我们通过在 BeeRich 中实现登录和注册后的重定向流程来练习使用 Remix 的 cookie 助手。如果一个用户未经授权尝试访问仪表板页面，我们目前将其重定向到登录页面。一旦用户登录或注册，我们将用户导航到`/dashboard`。现在我们想要更新这个逻辑，并将用户导航到最初请求的仪表板页面。

我们将首先创建一个访客 cookie：

1.  在`app/modules`中创建一个`visitors.server.ts`文件。

1.  接下来，从 Remix 导入`createCookie`并创建一个`visitorCookie`对象：

    ```js
    import { createCookie } from '@remix-run/node';const visitorCookie = createCookie('visitor-cookie', {  maxAge: 60 * 5, // 5 minutes});
    ```

    `createCookie`函数接收一个 cookie 名称参数和一个配置对象。可以在 Remix 文档中找到可能的配置选项列表：[`remix.run/docs/en/2/utils/cookies#createcookie`](https://remix.run/docs/en/2/utils/cookies#createcookie)。

    记住，Remix 提供了 cookie 助手实用工具和会话 cookie 助手实用工具。参考`session.server.ts`，我们在这里使用了 Remix 的`createCookieSessionStorage`函数。`createCookieSessionStorage`提供了三个函数：

    +   `getSession`

    +   `commitSession`

    +   `destroySession`

    相比之下，Remix 的`createCookie`函数只提供了两个函数：`parse`和`serialize`。

    会话 cookie 是 Remix 会话抽象的多种实现之一。另一方面，`createCookie`提供了一个简单的助手来读取（`parse`）和写入（`serialize`）cookie 到和从 cookie 头。

    我们使用 Remix 的会话助手来实现用户会话流程，而`createCookie`是一个用于读写 cookie 的实用工具。

1.  接下来，定义我们将存储在访客 cookie 中的数据类型：

    ```js
    type VisitorCookieData = {  redirectUrl?: string;};
    ```

    我们的目标是在将访客重定向到登录页面之前，持久化访客想要访问的 URL。

    例如，想象一个用户登录并正在使用 BeeRich 的仪表板。几天后，该用户想继续使用 BeeRich 管理他们的财务。由于会话已过期，BeeRich 将用户重定向到登录页面。到目前为止，我们在用户成功登录后已将其导航回仪表板，但我们不记得用户确切地停在了哪里。让我们改变这一点！

1.  在`visitors.server.ts`中创建一个函数来从请求中获取 cookie 数据：

    ```js
    export async function getVisitorCookieData(request: Request): Promise<VisitorCookieData> {  const cookieHeader = request.headers.get('Cookie');  const cookie = await visitorCookie.parse(cookieHeader);  return cookie && cookie.redirectUrl ? cookie : { redirectUrl: undefined };}
    ```

    我们使用 cookie 对象来解析`Cookie`头并提取访客 cookie 数据。

1.  类似地，创建一个函数将访客 cookie 数据写入`Set-Cookie`头：

    ```js
    export async function setVisitorCookieData(data: VisitorCookieData, headers = new Headers()): Promise<Headers> {  const cookie = await visitorCookie.serialize(data);  headers.append('Set-Cookie', cookie);  return headers;}
    ```

1.  有这些实用工具在位，我们可以读取传入请求的 cookie 数据，并在用户被重定向到登录时将 cookie 写入响应。

1.  在`app/modules/session/session.server.ts`中导入`setVisitorCookieData`：

    ```js
    import { setVisitorCookieData } from '~/modules/session/session.server.ts';
    ```

1.  接下来，更新`requireUserId`函数，在重定向到登录时添加访客 cookie：

    ```js
    export async function requireUserId(request: Request) {  const session = await getUserSession(request);  const userId = session.get('userId');  if (!userId || typeof userId !== 'string') {url property on the Request object to access the URL the user wanted to visit before the redirect.
    ```

1.  接下来，打开`_layout.login.tsx`路由模块并导入`getVisitorCookieData`函数：

    ```js
    import { getVisitorCookieData } from '~/modules/visitors.server';
    ```

1.  更新`_layout.login.tsx`路由模块的`action`函数，使其从访客 cookie 中读取`redirectUrl`：

    ```js
    try {  const user = await loginUser({ email, password });  /dashboard instead.
    ```

1.  通过在本地运行 BeeRich 来测试实现。

1.  首先，登录并访问仪表板上的一个路由。例如，导航到支出详情页面。从地址栏复制 URL 以方便访问，然后从 BeeRich 注销。

1.  现在，将复制的 URL 输入到地址栏中。由于我们已注销，我们被重定向到登录页面。

1.  接下来，登录到您的账户并注意重定向回请求的仪表板页面。

1.  在实现上稍作尝试。注意，无论你离开登录页面多少次，关闭浏览器标签或重新加载它，都不会影响结果。在 cookie 过期前的五分钟内，cookie 会持续存在并记住用户最新的请求 URL。

干得好！同样的流程也适用于注册吗？在 BeeRich 中，并不适用，因为所有仪表板 URL 都是针对特定账户的。然而，想象一个你可以邀请同事协作的应用程序。你可能可以分享一个项目的邀请链接。第一次加入的同事将被重定向到登录页面，但会导航到注册页面以创建新账户。从那里，我们可以利用访客 cookie 来读取邀请 URL 并将新用户导航到协作项目。

通过在注册页面上实现相同的流程来练习使用访客 cookie。遵循`_layout.login.tsx action`函数的实现，并在`_layout.signup.tsx action`函数中读取访客 cookie 数据，以便相应地引导用户。

在本节中，你练习了使用 Remix 的`createCookie`辅助函数，并了解了高级会话管理实现。你现在知道 Remix 的会话 cookie 和 cookie 实用工具之间的区别。接下来，我们将使用 Remix 实现分页。

# 实现分页

分页是在处理大型和用户生成对象列表时的重要模式。分页将内容分成单独的页面，从而限制了给定页面必须加载的对象数量。分页旨在减少加载时间并提高性能。

在本节中，我们将为 BeeRich 中的费用和发票实现分页：

1.  首先，打开`dashboard.expenses.tsx`路由模块，并定义一个页面大小的常量：

    ```js
    const PAGE_SIZE = 10;
    ```

    页面大小定义了我们在费用概览列表中一次显示的费用数量。要查看更多费用，用户必须导航到下一页。

1.  更新`dashboard.expenses.tsx`中的`loader`函数，并访问一个名为`page`的新搜索参数：

    ```js
    const userId = await requireUserId(request);const url = new URL(request.url);const searchString = url.searchParams.get('q');@prisma/client`.

    ```

    import type { Prisma } from '@prisma/client';

    ```js

    ```

1.  现在，更新费用数据库查询，使其只为当前页面查询总共 10 项费用，跳过所有之前的页面：

    ```js
    const where: Prisma.ExpenseWhereInput = {  userId,  title: {    contains: searchString ? searchString : '',  },};const [count, expenses] = $transaction utility instead of Promise.all for the additional performance benefit of making one big call to the database instead of two.
    ```

1.  更新`loader`函数的返回语句，使其返回费用列表和计数：

    ```js
    return json({ count, expenses });
    ```

1.  更新`useLoaderData`调用，使其读取更新的加载器数据：

    ```js
    const useSearchParams hook to read the page query parameter:

    ```

    const [searchParams] = useSearchParams();const searchQuery = searchParams.get('q') || '';showPagination 用于显示或隐藏分页按钮。

    ```js

    ```

1.  在费用列表（`<ul>…</ul>`）下方添加以下表单：

    ```js
    {showPagination && (  <Form expense-search feature.The `page` search parameter. Since the form uses GET, the route’s `loader` function is called (not its `action`).We could also use anchor tags instead of a form. Both initiate an HTTP GET request. The reason we decided to use a form here is so that we can utilize HTML button elements. We want to show the `disabled` attribute.Note that we include a hidden input field for the expense search filter parameter called `q`. This is necessary as we would otherwise reset the search filter when navigating between the different pages. By persisting the filter, the pagination works together with the search functionality and allows us to navigate between different pages of the filtered expenses list.
    ```

1.  最后，更新搜索表单，以便在搜索过滤器更改时重置分页：

    ```js
    <Form method="get" action={location.pathname}>  <input type="hidden" name="page" value={1} />  <SearchInput name="q" type="search" label="Search by title" defaultValue={searchQuery} key={searchQuery} /></Form>
    ```

    在更新搜索过滤器时，费用数量可能会发生变化。因此，我们需要重置分页。

1.  在本地运行 BeeRich 并尝试实现。注意，在页面之间导航时，URL 会更新。

    如果创建或删除费用会发生什么？

Remix 在每次突变后都会重新验证所有加载器数据。当我们添加费用时，会调用`loader`函数，并更新`count`加载器数据。这确保了如果费用超过第一页，将添加分页按钮。结果证明，加载器重新验证几乎解决了本书几乎每个章节中的陈旧数据问题！

类似地，在删除时更新计数值。然而，由于我们在删除后会将用户重定向回他们当前页面，用户可能仍然停留在没有费用的页面上。例如，如果我们有 11 项费用，并在第二页删除最后剩下的费用，用户最终会停留在空页面上。

如果我们在页面上保留分页**上一页**按钮，以便用户可以导航到上一页，这是可以的。我们通过始终显示分页按钮来确保这一点，如果用户当前不在第一页：

```js
const isOnFirstPage = pageNumber === 1;const showPagination = count > PAGE_SIZE || !isOnFirstPage;
```

看起来我们已经涵盖了所有边缘情况！做得好！

让我们花点时间反思一下我们与 BeeRich 一起的开发之旅。自从我们开始 BeeRich 的工作以来，我们已经走了很长的路。从头开始，我们构建了一个功能丰富的功能集，包括：

+   带有嵌套路由的路由层次结构

+   与多个模式的 SQLite 数据库集成

+   管理费用和收入的表单

+   用户登录、注册和注销流程

+   服务器端访问授权

+   文件上传功能

+   待定、乐观和实时 UI

+   各种缓存技术

+   使用 React streaming 和 Remix 的 `defer` 进行延迟数据加载

+   费用和发票列表的分页

恭喜你完成 BeeRich，这是一个充分利用 Remix 和 Web 平台的全栈 Web 应用程序。现在是时候接管 BeeRich 并继续练习了。你可以从为更多链接和重定向添加两个搜索参数 `q` 和 `page` 开始，以在不同用户操作和导航中持久化它们。或者，也许你已经有一段时间想要改变某些内容了？现在是时候了！

并且，一如既往地，通过在发票列表的收入路由上实现相同的分页逻辑来练习本章学到的内容。如果遇到困难，请参考 Prisma 和 Remix 文档。如果你需要更多指导，请参考

到实施在费用路由上。

在本节中，我们使用 URL 搜索参数在 Remix 中实现了分页。你学习了如何在不同的表单提交之间传递搜索参数，并在 Remix 中练习了高级会话管理。

# 摘要

在本章中，你学习了使用 Remix 的高级会话管理模式，并完成了 BeeRich 的工作。

Remix 提供了一个 `createCookie` 辅助函数来处理 cookie 数据。该函数返回一个 cookie 抽象，用于将 cookie 数据解析和序列化到请求头中。

在阅读本章之后，你了解了如何使用 `createCookie` 在 cookie 中存储和访问任意用户会话数据。你通过在 BeeRich 的登录和注册流程中添加访客 cookie 来练习与 cookie 一起工作，该 cookie 持续保存访客想要访问的 URL。

你还学会了如何使用 Remix 和 Prisma 实现简单的分页功能。分页是一种可以提高性能并避免处理数据列表时长时间加载时间的模式。利用分页可以限制每次页面加载需要获取的数据量。

在下一章中，我们将学习更多关于在边缘部署 Remix 应用程序的内容。

# 进一步阅读

你可以在 MDN Web Docs 中找到更多关于通过搜索参数工作的信息：[`developer.mozilla.org/en-US/docs/Web/API/URL/searchParams`](https://developer.mozilla.org/en-US/docs/Web/API/URL/searchParams)。

你可以通过参考 Remix 文档来了解更多关于 `createCookie` 辅助函数的信息：[`remix.run/docs/en/2/utils/cookies`](https://remix.run/docs/en/2/utils/cookies)。

你可以在 Prisma 文档中了解更多关于分页的信息：[`www.prisma.io/docs/concepts/components/prisma-client/pagination`](https://www.prisma.io/docs/concepts/components/prisma-client/pagination)。
