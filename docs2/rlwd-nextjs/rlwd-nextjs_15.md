# 第十二章：*第十二章*：管理认证和用户会话

在前面的章节中，我们看到了如何使用一些基本的 Next.js 功能。我们学习了如何在渲染策略之间进行选择以及这些策略如何影响 SEO 和性能。我们还学习了如何使用内置和外部样式方法和库来设计我们的应用，管理应用状态，集成外部 API，以及许多其他有价值的内容。

从本章开始，我们将通过结合之前学到的课程和行业标准策略，开始学习和开发实际应用，以保持我们的应用在各个方面都安全、高效和高度优化。

在本章中，我们将看到如何管理用户会话和认证，这是每个高度动态的 Web 应用的一个基本部分。

我们将详细介绍以下主题：

+   如何将我们的应用与自定义认证服务集成

+   如何使用行业标准的服务提供商，如 Auth0、NextAuth.js 和 Firebase

+   如何在页面变化之间保持会话

+   如何确保用户数据的安全和隐私

到本章结束时，您将能够对 Next.js 应用中的用户进行认证和管理他们的会话，了解不同认证策略之间的差异，甚至采用一个自定义的认证策略。

# 技术要求

要运行本章中的代码示例，您需要在您的本地机器上安装 Node.js 和 npm。如果您愿意，可以使用在线 IDE，例如[`repl.it`](https://repl.it)或[`codesandbox.io`](https://codesandbox.io)；它们都支持 Next.js，您不需要在您的计算机上安装任何依赖项。至于其他章节，您可以在 GitHub 上找到本章的代码库：[`github.com/PacktPublishing/Real-World-Next.js`](https://github.com/PacktPublishing/Real-World-Next.js)。

# 用户会话和认证的简要介绍

当谈到用户认证时，我们指的是识别特定用户的过程，根据他们的授权级别，让他们读取、写入、更新或删除任何受保护的内容。

一个典型的例子可能是一个简单的博客系统：我们只能在验证身份后发布、编辑甚至删除内容。

有许多不同的认证策略，但最常见的是：

+   **基于凭证的认证**：这种方法允许我们使用个人凭证登录系统，通常是一个电子邮件地址和密码。

+   **社交登录**：我们可以使用我们的社交账户（Facebook、Twitter、LinkedIn 等）登录系统。

+   **无密码登录**：近年来，这种方法已经成为一种相当流行的认证方式。例如 Medium 和 Slack 这样的平台会向您发送所谓的“魔法链接”到您的电子邮件地址，让您无需输入任何密码即可登录您的账户。

+   **单点登录（SSO）**：如果你在大公司工作过，你可能经历过这种情况。例如，Okta 等服务提供了一种使用唯一凭证访问许多不同服务的方法，通过它们自己的服务集中用户认证。一旦你登录到 SSO 系统，它将把你重定向到所需的网站，并授予你的身份。

但一旦我们登录到系统，我们希望它记住我们，这样我们就不必在导航过程中每次页面变化时都进行认证。这就是会话管理介入的地方。

再次强调，管理用户会话有许多方法。如果你熟悉 PHP，你可能知道它提供了一个内置方法来控制用户会话。让我们看看下面的代码片段：

```js
<?php
  session_start();
  $_SESSION["first_name"] = "John";
  $_SESSION["last_name"]  = "Doe";
?>
```

这是一个典型的服务器端会话管理示例。

它创建了一个会话 cookie，并跟踪与该会话相关联的所有属性。例如，我们可以将登录用户的电子邮件或用户名与该会话关联，每次我们渲染页面时，我们都可以根据认证用户的数据来完成。

我们可以将这种策略称为*状态化会话*，因为用户状态保存在服务器端，并通过特定的会话 cookie 与客户端关联。

在原型设计阶段管理状态化会话相对容易，但一旦你开始在生产中进行扩展，事情往往会变得复杂一些。

在上一章中，我们讨论了将我们的应用程序部署到 Vercel、AWS 或其他任何托管平台。以 Vercel 为例，因为它是最直接（但也是优化过的）用于托管我们的 Next.js Web 应用程序的平台。我们已经看到了每个 API 和 SSR 页面是如何在无服务器函数上渲染的，对吧？现在想象一下，在这种情况下，当我们甚至没有服务器来管理时，如何保持服务器端的状态化会话？

让我们假设我们在用户登录后为他们渲染一个欢迎页面。我们可以设置一个会话 cookie，但每当 Lambda 函数终止其执行时，每个服务器端状态化数据实例都将被取消。那么我们如何保持会话？一旦用户离开这个页面会发生什么？服务器端会话将会丢失，他们需要重新进行认证。

这正是*无状态会话*概念真正发挥作用的地方。

我们不想设置一个将服务器端会话与前端关联的会话 cookie，我们希望释放一些信息，以识别每个新请求中的用户。每当认证用户向后端发送请求时，他们必须遵循一个授权机制，例如传递特定的 cookie 或 HTTP 头。在每次新请求中，服务器将获取这些信息，验证它们，识别用户（如果传递的 cookie 或头是有效的），然后提供所需的内容。

一种遵循此模式的行业标准方法是*JWT（JSON Web Tokens）基于的*认证，但我们将在这个下一节中讨论。

# 理解 JSON Web Tokens

如[`jwt.io`](https://jwt.io)网站所述，**JWT**（即**JSON Web Token**）是一种开放、行业标准的*RFC 7519*方法，用于在双方之间安全地表示声明。

为了简单起见，我们可以将 JWT 视为三个不同的 base64 编码的 JSON 数据块。

让我们以下面的 JWT 为例：

```js
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI5MDhlYWZhNy03MWJkLTQyMDMtOGY3Ni1iNjA3MmNkMTFlODciLCJuYW1lIjoiSmFuZSBEb2UiLCJpYXQiOjE1MTYyMzkwMjJ9.HCl73CTg8960TvLP7i5mV2hKQlSJLaLAlmvHk38kL8o
```

如果我们足够注意，我们可以看到由句点分隔的三个不同的数据块。

第一部分代表 JWT 头。它包含两个基本信息：令牌类型和用于签名的算法（我们将在下一秒讨论更多关于这一点的内容）。

第二部分是有效负载。在这里，我们放置所有可以帮助我们识别用户的不敏感数据。*永远不要在 JWT 有效负载中存储如密码和银行详情等数据*。

JWT 令牌的第三部分和最后一部分是其签名。这就是 JWT 令牌之所以安全的原因，我们将在本节稍后详细讨论。

如果我们使用任何客户端库或专门的网站（如[`jwt.io`](https://jwt.io)）解码我们的 JWT 令牌，我们将看到以下 JSON 数据：

```js
// First chunk
{
  "alg": "HS256", // Algorithm used to sign the token
  "typ": "JWT"    // Token type
}
// Second chunk
{
  "sub": "908eafa7-71bd-4203-8f76-b6072cd11e87", // JWT subject
  "name": "Jane Doe",                            // User name
  "iat": 1516239022                              // Issued at
}
```

第一部分告诉我们，给定的令牌是使用 HS256 算法签名的 JWT。

第二个数据块为我们提供了有关用户的一些有用信息，例如 JWT 主题（通常是用户 ID）、用户名以及我们签发令牌的时间戳。

JWT 有效负载最佳实践

官方的*RFC7519*指定了一些可选的有效负载属性，例如`"sub"`（主题）、`"aud"`（受众）、`"exp"`（过期时间）等。尽管它们是可选的，但最佳实践是根据官方 RFC 规范实现它们，该规范可在[`datatracker.ietf.org/doc/html/rfc7519#section-4`](https://datatracker.ietf.org/doc/html/rfc7519#section-4)找到。

一旦我们需要个人用户数据，我们可以将 JWT 作为 cookie 设置，或者将其用作 HTTP 授权头中的 bearer 令牌。一旦服务器获取这些数据，它将验证令牌，这就是第三令牌部分变得至关重要的地方。

正如我们已经看到的，任何 JWT 的第三部分是其签名。让我们再次保持简单，并举例说明为什么（以及如何）我们想要对 JWT 令牌进行签名。

对任何人来说，解码 JWT 令牌都非常简单；它只是一个 base64 编码的 JSON，因此我们可以使用 JavaScript 内置函数来解码它，对其进行操作（例如添加`"admin": true`属性），然后再以所需的格式重新编码它。

如果 JWT 令牌如此容易被破解，那会非常糟糕，对吧？好消息是：解码、操作然后再次编码令牌是不够的。我们还需要使用在签发 JWT 的服务器上使用的相同密钥对其进行签名。

例如，我们可以使用`jsonwebtoken`库为我们用户生成令牌，如下所示：

```js
const jwt = require('jsonwebtoken');
const myToken = jwt.sign(
  {
    name: 'Jane Doe',
    admin: false,
  },
  'secretpassword',
);
```

我们最终会得到以下 JWT 令牌：

```js
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJuYW1lIjoiSmFuZSBEb2UiLCJhZG1pbiI6ZmFsc2UsImlhdCI6MTYzNDEzMTI2OH0.AxLW0CwWpsIUk71WNbbZS9jTPpab8z4LVfJH6rsa4Nk
```

现在，我们想要验证它，只是为了确保它按预期工作：

```js
const jwt = require('jsonwebtoken');
const myToken = jwt.sign(
  {
    name: 'Jane Doe',
    admin: false,
  },
  'secretpassword',
);
const tokenValue = jwt.verify(myToken, 'secretpassword');
console.log(tokenValue);
// => { name: 'Jane Doe', admin: false, iat: 1634131396 }
```

在那个库中，`jwt.verify`方法在签名验证成功后返回解码后的有效载荷。如果验证失败，它将抛出一个错误。

我们可以通过复制并粘贴前面的 JWT 到[`jwt.io`](https://jwt.io)首页来测试这一点。它将允许我们自由编辑，因此我们可以尝试将`"admin": true`声明设置到我们的 JWT 中：

![图 12.1 – 在 https://jwt.io 上编辑 JWT 令牌](img/Figure_12.01_B16985.jpg)

图 12.1 – 在 https://jwt.io 上编辑 JWT 令牌

如您所注意到的，Web 应用将立即在我们在头部或有效载荷部分输入内容时更新 JWT 令牌。一旦我们完成编辑，我们就可以用我们的脚本最终测试它：

```js
const tokenValue = jwt.verify(
'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJuYW1lIjoiSmFuZSBEb2UiLCJhZG1pbiI6dHJ1ZSwiaWF0IjoxNjM0MTMxMjY4fQ.SAv6UDKdlb3CokrC8o3e_pZs_Fk0JyY2oDpYJ0bDUyU',
  'secretpassword',
);
```

一旦我们尝试验证这个令牌，我们将在控制台看到以下错误：

```js
JsonWebTokenError: invalid signature
```

正是这一点使得 JWT 安全：每个人都可以潜在地读取和操作它。但一旦你这样做，你就无法使用有效的签名来签名它，因为它在服务器端保持秘密和隐藏。

在下一节中，我们将看到一个将 JWT 认证集成到 Next.js 应用中的实际示例。

# 自定义认证 – 好的、坏的和不那么好的

让我们从一开始就明确这一点：在可能的情况下，我们应该避免实现自定义认证策略。有多个优秀的提供商（包括 Auth0、Firebase、AWS Cognito 和 Magic.link 等，仅举几个例子）正在投入大量精力使认证安全、可靠，并针对许多不同情况进行优化。在调查 Web 应用的认证策略时，我强烈建议考虑一个成熟的供应商，因为这可能是动态 Web 应用最关键的部分之一。

在本节中，我们正在探讨为简单原因创建自定义认证机制：我们只是想从高层次上了解认证是如何工作的，如何使其尽可能安全，以及自定义认证系统的关键因素是什么。

正如我们将在本节中发现的那样，在实现自定义认证机制时将存在一些限制。例如，我强烈反对在静态生成的网站上实现客户端认证，因为它迫使我们仅在客户端进行用户认证，可能将敏感数据暴露给网络。

因此，我们将创建一个新的 Next.js Web 应用，该应用将使用 API 路由与数据源（通常是数据库）进行通信并检索用户数据。

因此，让我们首先创建一个新的、空的 Next.js 应用：

```js
npx create-next-app with-custom-auth
```

一旦准备好样板代码，我们就可以开始编写登录 API。请注意，以下代码并不打算用于生产；我们只是在简化、高层次地概述认证的工作原理。

让我们从创建一个`/pages/api/login.js`文件开始，该文件通过导出以下函数：

```js
export default (req, res) => {}
```

这是我们处理用户输入并进行验证的地方。

我们可以做的第一件事是获取用户输入并过滤请求方法，只接受 POST 请求：

```js
export default (req, res) => {
  const { method } = req;
  const { email, password } = req.body;
  if (method !== 'POST') {
    return res.status(404).end();
  }
}
```

我们为什么需要过滤 POST 请求？

默认情况下，所有 Next.js API 路由都接受任何 HTTP 方法。顺便说一句，在特定路由上只允许特定方法是最佳实践，例如，在创建新内容时启用`POST`请求，在读取数据时启用`GET`，在修改内容时启用`PUT`，或在删除数据时启用`DELETE`。

我们现在可以验证用户输入。例如，在验证电子邮件和密码时，我们可以检查传递的电子邮件是否在有效格式中，以及密码是否遵循特定的策略。这样，如果任何给定的数据无效，我们只需回复一个`401`状态码（未授权），因为我们不会在数据库中找到该电子邮件和密码组合的任何记录。这也有助于我们避免无用的数据库调用。

目前，我们没有数据库，我们将依赖于硬编码的值，因为我们只想从高层次上理解认证。话虽如此，我们只会检查请求体中是否包含电子邮件和密码，因此我们可以保持简单：

```js
export default (req, res) => {
  const { method } = req;
  const { email, password } = req.body;
  if (method !== 'POST') {
    return res.status(404).end();
  }
  if (!email || !password) {
    return res.status(400).json({
      error: 'Missing required params',
    });
  }
}
```

如果请求体中不存在电子邮件或密码，我们将返回一个`400`状态码（错误请求）并附带一个错误信息，解释请求失败的原因。

如果请求使用 HTTP POST 方法发送并提供电子邮件和密码，我们可以使用任何认证机制来处理它们。例如，我们可以在数据库中查找具有该特定电子邮件的用户，检索他们的密码，然后在服务器端验证它或请求外部认证服务为我们完成这项工作。

目前，鉴于我们只是在概述自定义认证策略，我们将使用一个基础的函数来检查电子邮件和密码的组合是否与两个固定的字符串匹配。再次强调，这并不是为了生产使用。

在同一个`pages/api/login.js`文件中，我们可以创建一个非常基础的函数来为我们完成这项工作：

```js
function authenticateUser(email, password) {
  const validEmail = 'johndoe@somecompany.com';
  const validPassword = 'strongpassword';
  if (email === validEmail && password === validPassword) {
    return {
      id: 'f678f078-fcfe-43ca-9d20-e8c9a95209b6',
      name: 'John Doe',
      email: 'johndoe@somecompany.com',
    };
  }
  return null;
}
```

在生产环境中，我们永远不会使用这样的认证函数。相反，我们将与数据库或外部服务通信，以动态检索用户数据。

我们终于可以将前面的函数与我们的 API 处理器结合起来。如果传递的数据是正确的，我们将获取一些用户数据并将其发送给客户端。否则，我们只需发送一个`401`状态码（未授权）并附带一个错误信息，解释传递的数据是不正确的：

```js
export default (req, res) => {
  const { method } = req;
  const { email, password } = req.body;
  if (method !== 'POST') {
    return res.status(404).end();
  }
  if (!email || !password) {
    return res.status(400).json({
      error: 'Missing required params',
    });
  }
  const user = authenticateUser(email, password);
  if (user) {
    return res.json({ user });
  } else {
    return res.status(401).json({
      error: 'Wrong email of password',
    });
  }
};
```

在这一点上，我们可以开始分析这种方法的潜在风险。让我们假设一下，我们将从前端登录，服务器将回复这样的信息，我们将将其存储在 cookie 中。一旦我们需要获取更多关于（比如说）我们的用户的数据，我们只需向服务器提交一个请求，服务器将读取 cookie，获取当前用户 ID，然后查询数据库以获取他们的数据。

你能看出这种解决方案的失败点吗？

每个人都可能通过使用每个现代网络浏览器内置的开发者工具来编辑他们的 Cookies。这意味着每个人都可以简单地读取 Cookie，更改它，并且可以冒充另一个用户，甚至不需要登录。

我们为什么要讨论 Cookies？

Cookies 是存储会话数据的好方法。我们可以使用不同的浏览器功能，如`localStorage`、`sessionStorage`，甚至`indexedDB`。问题是，任何人都可以通过在你的网页中注入恶意脚本来窃取这些数据。在处理 Cookies 时，我们可以（并且我们应该）将`httpOnly`标志设置为`true`，以便仅在服务器端提供 Cookies。这为存储这些数据添加了一个额外的安全层。尽管我们应该意识到，每个用户都可以通过使用现代浏览器提供的开发者工具来检查 Cookies，但我们绝不应该在那里共享敏感信息。

这就是 JWT 可以发挥作用的地方。我们可以简单地编辑我们的登录处理程序，通过在返回任何数据之前设置包含 JWT 的 Cookie 来使其更加安全。

让我们从安装`jsonwebtoken`npm 包开始：

```js
yarn add jsonwebtoken
```

让我们创建一个新文件，`lib/jwt.js`，并添加以下内容：

```js
import jwt from 'jsonwebtoken';
const JWT_SECRET = 'my_jwt_password';
export function encode(payload) {
  return jwt.sign(payload, JWT_SECRET);
}
export function decode(token) {
  return jwt.verify(token, JWT_SECRET);
}
```

现在，回到我们的`pages/api/login.js`文件，我们可以通过将用户有效负载编码成 JWT 来编辑它：

```js
import { encode } from '../../lib/jwt';
function authenticateUser(email, password) {
  const validEmail = 'johndoe@somecompany.com';
  const validPassword = 'strongpassword';
  if (email === validEmail && password === validPassword) {
    return encode({
      id: 'f678f078-fcfe-43ca-9d20-e8c9a95209b6',
      name: 'John Doe',
      email: 'johndoe@somecompany.com',
    });
  }
  return null;
}
```

最后一点：我们说过我们想要设置一个包含我们刚刚创建的 JWT 的 Cookie。我们可以安装一个方便的库来帮助我们实现这一点：

```js
yarn add cookie
```

安装完成后，我们可以通过设置会话 Cookie 来编辑我们的`pages/api/login.js`文件：

```js
import { serialize } from 'cookie';
// ...
export default (req, res) => {
  const { method } = req;
  const { email, password } = req.body;
  if (method !== 'POST') {
    return res.status(404).end();
  }
  if (!email || !password) {
    return res.status(400).json({
      error: 'Missing required params',
    });
  }
  const user = authenticateUser(email, password);
  if (user) {
    res.setHeader('Set-Cookie',
        serialize('my_auth', user, { path: '/', httpOnly:                   true })
    );
    return res.json({ success: true });
  } else {
    return res.status(401).json({
      success: false,
      error: 'Wrong email of password',
    });
  }
};
```

如你所见，我们正在创建一个名为`my_auth`的 Cookie，它将包含用户 JWT。我们不会直接将 JWT 传递给客户端，因为我们希望将其隐藏在客户端上可能运行的任何潜在恶意脚本之外。

我们可以通过使用 Postman 或 Insomnia 等有用的 HTTP 客户端来测试该过程是否按预期工作（你可以免费下载 Insomnia：[`insomnia.rest`](https://insomnia.rest)）：

![图 12.2 – Insomnia 中的登录 API 响应](img/Figure_12.02_B16985.jpg)

图 12.2 – Insomnia 中的登录 API 响应

如果我们切换到我们选择工具（在我的情况下，是 Insomnia）的响应部分的**Cookie**标签页，我们最终可以看到认证 Cookie：

![图 12.3 – Insomnia 中的认证 Cookie](img/Figure_12.03_B16985.jpg)

图 12.3 – Insomnia 中的认证 Cookie

现在是时候在客户端通过创建登录表单和受保护的路由来管理认证了，只有登录后才能看到这些路由。所以，让我们从这里开始：让我们创建一个包含以下内容的新的`/pages/protected-route.js`文件：

```js
import styles from '../styles/app.module.css';
export default function ProtectedRoute() {
  return (
    <div className={styles.container}>
      <h1>Protected Route</h1>
      <p>You can't see me if not logged-in!</p>
    </div>
  );
}
```

通过查看`ProtectedRoute`函数，你可以看出我们并没有阻止匿名用户浏览该页面；我们将在创建登录页面后立即做到这一点。

让我们再创建一个 `/styles/app.module.css` 文件，我们将在这里放置我们应用程序的所有样式；我们并不旨在制作一个获奖的 UI，所以我们只需在那里创建几个简单的样式：

```js
.container {
  min-height: 100vh;
  padding: 0 0.5rem;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
}
```

现在，我们可以开始专注于登录。让我们创建一个新的页面，`/pages/login.js`，并开始编写登录 UI：

```js
import { useState } from 'react';
import { useRouter } from 'next/router';
import styles from '../styles/app.module.css';
export default function Home() {
  const [loginError, setLoginError] = useState(null);
  return (
    <div className={styles.container}>
      <h1>Login</h1>
      <form className={styles.form} 
        onSubmit={handleSubmit}>
        <label htmlFor="email">Email</label>
        <input type="email" id="email" />
        <label htmlFor="password">Password</label>
        <input type="password" id="password" />
        <button type="submit">Login</button>
        {loginError && (
          <div className={styles.formError}> 
            {loginError} </div>
      )}
      </form>
    </div>
  );
}
```

在创建缺少的 `handleSubmit` 函数之前，让我们向 `styles/app.module.css` 文件添加一些样式：

```js
.form {
  display: flex;
  flex-direction: column;
}
.form input {
  padding: 0.5rem;
  margin: 0.5rem;
  border: 1px solid #ccc;
  border-radius: 4px;
  width: 15rem;
}
.form label {
  margin: 0 0.5rem;
}
.form button {
  padding: 0.5rem;
  margin: 0.5rem;
  border: 1px solid #ccc;
  border-radius: 4px;
  width: 15rem;
  cursor: pointer;
}
.formError {
  color: red;
  font-size: 0.8rem;
  text-align: center;
}
```

现在，我们可以编写 `handleSubmit` 函数。在这里，我们将捕获表单提交事件，阻止浏览器默认行为（向远程 API 提交请求），并处理我们登录的两个可能情况：成功和失败。如果登录成功，我们将重定向用户到我们的受保护页面。如果失败，我们将在 `loginError` 状态中设置一个错误：

```js
export default function Home() {
  const router = useRouter();
  const [loginError, setLoginError] = useState(null);
  const handleSubmit = (event) => {
    event.preventDefault();
    const { email, password } = event.target.elements;
    setLoginError(null);
    handleLogin(email.value, password.value)
      .then(() => router.push('/protected-route'))
      .catch((err) => setLoginError(err.message));
  };
  // ...
```

我们现在缺少最后一个函数，即负责发起登录 API 请求的函数。由于在测试阶段，我们可能想单独测试它，所以我们可以将其创建在 `Home` 组件外部：

```js
// ...
async function handleLogin(email, password) {
  const resp = await fetch('/api/login', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      email,
      password,
    }),
  });
  const data = await resp.json();
  if (data.success) {
    return;
  }
  throw new Error('Wrong email or password');
}
// ...
```

我们现在可以最终测试我们的登录页面，看看它是否工作正常！如果它确实工作，我们应该被重定向到我们的私有路由；否则，我们应该在表单提交按钮下方看到一个友好的错误消息。

现在是时候保护我们的私有页面了。如果我们没有登录，就不应该能看到它。类似的事情也应该适用于登录页面：一旦我们登录，就不应该能看到它。

在继续实施之前，我们应该决定如何在我们的应用程序中实现认证。

我们可以在服务器端渲染我们的页面来检查每个请求的 cookie（记得吗？我们不想在客户端访问认证 cookie！），或者我们可以在前端简单地渲染一个加载器，并在渲染实际页面内容之前等待钩子检查我们是否已登录。

在做出这样的选择之前，我们应该考虑什么？

有几种情况可能会影响这个选择。例如，让我们考虑 SEO；如果我们正在构建一个只有登录用户可以（例如）发表评论的博客，那不是什么大问题。我们可以发送一个静态生成的页面，并等待钩子告诉我们用户是否已认证。同时，我们可以渲染公共内容（如文章正文、作者和标签），这样 SEO 就不会受到影响。用户一旦客户端知道他们已登录，就可以立即发表评论。

此外，性能将非常出色，因为我们可以用静态生成的页面来提供，动态数据将在客户端独立渲染。

作为一种替代方案，我们可以在服务器端简单地获取用户 cookie，验证 JWT，然后根据用户的认证状态渲染页面；这可能更容易实现（我们可以在内置的 `getServerSideProps` 函数内部做这个），但无疑会增加一些延迟，并迫使我们必须在服务器端渲染所有页面。

我们将实施第一个解决方案，其中我们需要创建一个自定义钩子来决定用户是否已登录。

为了做到这一点，我们首先需要实现一个 API，该 API 解析我们的 cookie 并回复关于我们会话的最基本信息。让我们通过创建一个`pages/api/get-session.js`文件来实现这一点：

```js
import { parse } from 'cookie';
import { decode } from '../../lib/jwt';
export default (req, res) => {
  if (req.method !== 'GET') {
    return res.status(404).end();
  }
  const { my_auth } = parse(req.headers.cookie || '');
  if (!my_auth) {
    return res.json({ loggedIn: false });
  }
  return res.json({
    loggedIn: true,
    user: decode(my_auth),
  });
};
```

我们现在可以使用我们刚刚创建的表单进行登录，然后通过`http://localhost:3000/api/get-session`调用 API。我们将看到以下类似的结果：

```js
{
  "loggedIn": true,
  "user": {
    "id": "f678f078-fcfe-43ca-9d20-e8c9a95209b6",
    "name": "John Doe",
    "email": "johndoe@somecompany.com",
    "iat": 1635085226
  }
}
```

如果我们在隐身会话中调用相同的 API，我们只会得到一个`{ "loggedIn": false }`的响应。

我们可以通过创建一个自定义钩子来使用这个 API 来确定用户是否已登录。让我们通过创建一个包含以下内容的`lib/hooks/auth.js`文件来实现这一点：

```js
import { useState, useEffect } from 'react';
export function useAuth() {
  const [loggedIn, setLoggedIn] = useState(false);
  const [user, setUser]         = useState(null);
  const [loading, setLoading]   = useState(true);
  const [error, setError]       = useState(null);
  useEffect(() => {
    setLoading(true);
    fetch('/api/get-session')
      .then((res) => res.json())
      .then((data) => {
        if (data.loggedIn) {
          setLoggedIn(true);
          setUser(data.user);
        }
      })
      .catch((err) => setError(err))
      .finally(() => setLoading(false));
  }, []);
  return {
    user,
    loggedIn,
    loading,
    error,
  };
}
```

钩子本身很简单。一旦它加载（也就是说，当`useEffect` React 钩子被触发时），它将向我们的`/api/get-session` API 发起 HTTP 调用。一旦 API 成功（或失败），它将返回用户状态、错误（如果有），并将`loading`状态设置为`false`，这样我们就会知道是时候重新渲染 UI 了。

我们现在可以通过导入它并根据认证状态显示私有内容来在我们的受保护页面上实现这个钩子：

```js
import { useRouter } from 'next/router';
import { useAuth } from '../lib/hooks/auth';
import styles from '../styles/app.module.css';
export default function ProtectedRoute() {
  const router = useRouter();
  const { loading, error, loggedIn } = useAuth();
  if (!loading && !loggedIn) {
    router.push('/login');
  }
  return (
    <div className={styles.container}>
      {loading && <p>Loading...</p>}
      {error && <p> An error occurred. </p>}
      {loggedIn && (
        <>
          <h1>Protected Route</h1>
          <p>You can't see me if not logged-in!</p>
        </>
      )}
    </div>
  );
}
```

我们现在可以尝试访问我们的私有页面，看看登录后是否工作正常！首先，应该有一个短暂的“加载”文本出现；然后，我们应该看到受保护的路由内容。

我们可以采用类似的方法来隐藏登录页面，不让已登录的用户看到；让我们打开`pages/login.js`文件并按照以下方式编辑它：

```js
import { useState } from 'react';
import { useRouter } from 'next/router';
import { useAuth } from '../lib/hooks/auth';
import styles from '../styles/app.module.css';
// ...
```

一旦我们导入`useAuth`钩子，我们就可以开始编写组件逻辑。在我们知道用户是否已登录之前，我们不会渲染登录表单：

```js
// ...
export default function Home() {
  const router = useRouter();
  const [loginError, setLoginError] = useState(null);
  const { loading, loggedIn } = useAuth();
  if (loading) {
    return <p>Loading...</p>;
  }
  if (!loading && loggedIn) {
    router.push('/protected-route');
    return null;
  }
// ...
```

在这里，我们告诉我们的登录页面与受保护的路由页面相反。我们将等待钩子完成加载阶段，当它结束时，我们将检查用户是否已登录。如果他们已登录，我们将简单地使用 Next.js 的`useRouter`钩子将他们重定向到受保护页面。

我们成功实现了一个非常简单（但无论如何都不适合生产环境）的登录策略，但我们错过了什么？接下来会出现什么问题？我们应该追求编写自定义认证策略吗？

嗯，我认为除非我们在一个大型的专家团队中工作，否则这并不值得。

这本书的这一部分标题为*自定义认证 – 优点、缺点和丑陋之处*，所以让我们将一些考虑因素分为这三个类别：

*优点*：我们可能都会欣赏编写自定义认证系统，因为它让我们对安全有了很多了解，并让我们完全控制整个认证流程。

*The bad*: 我们将承担相当大的风险。编写健壮的认证机制并不容易，公司投入大量资金以提供安全的认证策略。对于在这个行业外工作的公司来说，达到 Auth0、Okta、Google 或 Amazon AWS 相同的保安级别是很困难的。

*The ugly*: 即使我们可以创建一个健壮的认证系统，我们也必须手动实现许多自定义流程——重置密码和用户注册工作流程、双因素认证和交易性电子邮件，仅举几例。这将需要大量的额外工作，并且会导致复制现有服务，而无法达到相同的保安和可靠性水平，因为要与 Auth0、Google 或 AWS 标准相匹配是困难的。

在下一节中，我们将看到如何使用一个行业标准、广为人知的认证提供商——Auth0——为任何 Next.js 应用实现认证。

# 使用 Auth0 实现认证

在上一节中，我们看到了如何实现一个基本且直接的认证方法。我不会重复得太多：我们所看到的是一个高级概述，不应用于任何生产就绪的产品。

当构建生产就绪的 Web 应用时，我们可能会采用外部认证方法，这些方法是安全和可靠的。

有许多不同的认证提供商（AWS Cognito、Firebase、Magic.link 等等），我相信它们都在出色地保护他们的用户。在本章中，我们将使用一个流行、安全且经济的认证提供商，利用其慷慨的免费计划：Auth0。

如果你想跟随本章内容，你可以在[`auth0.com`](https://auth0.com)（免费计划用户无需信用卡）创建一个免费账户。

Auth0 将管理任何认证策略中最复杂的步骤，并将提供一些友好的 API 供我们使用。

多亏了这个认证提供商，我们不必担心以下任何一点：

+   用户注册

+   用户登录

+   邮件验证

+   忘记密码流程

+   重置密码流程

我们也不必担心任何认证策略的其他许多关键部分。

因此，让我们先创建一个新的 Next.js 应用：

```js
npx create-next-app with-auth0
```

现在，登录 Auth0 并创建一个新的应用：

![图 12.4 – 创建新的 Auth0 应用](img/Figure_12.04_B16985.jpg)

图 12.4 – 创建新的 Auth0 应用

一旦我们创建了我们的应用程序，Auth0 将询问我们将使用哪种技术。我们可以选择**Next.js**，Auth0 将带我们到一个关于如何在框架中采用其认证机制的优秀教程。

如果我们进入**设置**，我们将能够设置我们的回调 URL。这些 URL 代表用户完成特定操作（如登录、登出和注册）后将被重定向到的页面。

在这一点上，我们需要通过添加 http://localhost:3000/api/auth/callback 来设置**允许的回调 URL**，并通过设置 `http://localhost:3000/` 来设置**允许的注销 URL**。

这将授权我们在每次 Auth0 相关操作（如登录、注册和密码重置）之后采用 Auth0 进行本地开发，因为 Auth0 将将我们重定向到操作起源的 URL。

因此，例如，如果我们想登录到 [`example.com`](https://example.com)，在登录操作之后，Auth0 将自动将我们重定向到 [`example.com/api/auth/callback`](https://example.com/api/auth/callback)，这需要在刚刚看到的章节中进行授权。

由于我们的本地开发 URL 很可能是 http://localhost:3000（这是 Next.js 的默认设置），我们可能需要在**允许的回调 URL**和**允许的注销 URL**部分授权其他预发布或生产 URL。当然，我们总是可以通过添加更多 URL 并用逗号分隔它们来实现这一点。

一旦我们设置好了重定向 URL，我们就可以开始设置我们的本地环境。

首先，我们需要为本地环境创建一个环境文件。所以，让我们创建它，并将其命名为 `.env.local`，然后添加以下内容：

```js
AUTH0_SECRET=f915324d4e18d45318179e733fc25d7aed95ee6d6734c8786c03 AUTH0_BASE_URL='http://localhost:3000'AUTH0_ISSUER_BASE_URL='https://YOUR_AUTH0_DOMAIN.auth0.com'AUTH0_CLIENT_ID='YOUR_AUTH0_CLIENT_ID'AUTH0_CLIENT_SECRET='YOUR_AUTH0_CLIENT_SECRET'
```

请记住，我们绝不应该提交环境文件，因为它包含可能危害我们应用程序安全性的敏感数据。

如您所见，我们正在设置五个基本的环境变量：

+   `AUTH0_SECRET`：Auth0 用来加密会话 cookie 的随机生成的字符串。您可以在终端中运行 `openssl rand -hex 32` 来生成一个新的、安全的随机密钥。

+   `AUTH0_BASE_URL`：我们应用程序的基本 URL。对于本地开发环境，它将是 `http://localhost:3000`。如果您想在不同的端口上启动应用程序，请确保更新 `.env.local` 文件以反映此更改。

+   `AUTH0_ISSUER_BASE_URL`：您的 Auth0 应用程序的 URL。您可以在我们刚刚访问的**设置**部分的开始处找到它，用于设置回调 URL（在 Auth0 控制台中标记为**域名**）。

+   `AUTH0_CLIENT_ID`：Auth0 应用程序的客户端 ID。您可以在**域名**设置下方找到它。

+   `AUTH0_CLIENT_SECRET`：Auth0 应用程序的客户端密钥。您可以在 Auth0 控制台中的**客户端 ID**设置下找到它。

一旦我们设置了所有这些环境变量，我们就可以在我们的 Next.js 应用程序中为 Auth0 创建一个 API 路由。记得我们之前讨论过在编写自定义身份验证策略时应该实现多少事情吗？登录、注销、密码重置、用户注册... Auth0 为我们处理一切，它通过要求我们在 `/pages/api/auth/[...auth0].js` 下创建一个简单的 API 路由来实现这一点。

一旦我们创建了此页面，我们就可以向其中添加以下内容：

```js
import { handleAuth } from '@auth0/nextjs-auth0';
export default handleAuth();
```

如果您还没有这样做，您可以通过运行以下命令来安装官方的 Auth0 Next.js SDK：

```js
yarn add @auth0/nextjs-auth0
```

一旦我们启动 Next.js 服务器，`handleAuth()`方法将为我们创建以下路由：

+   `/api/auth/login`，这是允许我们登录到我们应用的路径

+   `/api/auth/callback`，这是 Auth0 在登录成功后立即重定向我们的回调 URL

+   `/api/auth/logout`，我们可以从这里注销我们的 Web 应用

+   `/api/auth/me`，这是一个端点，一旦我们登录，我们可以从中以 JSON 格式获取我们自己的信息

要使我们的会话在所有 Web 应用页面之间持久化，我们可以将我们的组件包裹在官方 Auth0 `UserProvider`上下文中。我们可以通过打开我们的`pages/_app.js`文件并添加以下内容来实现这一点：

```js
import { UserProvider } from '@auth0/nextjs-auth0';
export default function App({ Component, pageProps }) {
  return (
    <UserProvider>
      <Component {...pageProps} />
    </UserProvider>
  );
}
```

我们现在可以通过浏览 http://localhost:3000/api/auth/login 来尝试访问我们的应用登录页面。我们最终应该看到以下页面：

![图 12.5 – 默认 Auth0 登录页面![图片](img/Figure_12.05_B16985.jpg)

图 12.5 – 默认 Auth0 登录页面

我们还没有账户，因为这是我们第一次访问登录页面。我们可以点击**注册**来创建一个新账户。

一旦创建，我们将被重定向到应用主页，并收到一封确认邮件。

现在我们已经登录，我们可以在前端显示一些根据登录用户显示的有用信息；让我们从一个简单的事情开始，只显示一个问候消息。

我们可以通过打开`/pages/index.js`文件并添加以下内容来实现这一点：

```js
import { useUser } from '@auth0/nextjs-auth0';
export default function Index() {
  const { user, error, isLoading } = useUser();
  if (isLoading) {
    return <div>Loading...</div>;
  }
  if (error) {
    return <div>{error.message}</div>;
  }
  if (user) {
    return (
      <div>
        <h1> Welcome back! </h1>
        <p>
          You're logged in with the following email    
            address:
          {user.email}!
        </p>
        <a href="/api/auth/logout">Logout</a>
      </div>
    );
  }
  return (
    <div>
      <h1> Welcome, stranger! </h1>
      <p>Please <a href="/api/auth/login">Login</a>.</p>
    </div>
  );
}
```

如您所见，这个模式与我们实现自定义认证机制时使用的模式非常相似。我们静态生成页面，然后等待客户端获取用户信息，一旦我们得到它，我们就在屏幕上打印私有内容。

您现在可以尝试登录和注销应用以测试它是否正常工作。

一旦我们登录和注销，我们可能会想知道：我们如何自定义认证表单？如果我们想将数据保留在我们自己的数据库中怎么办？我们将在下一节讨论这个问题。

## 定制 Auth0

到目前为止，我们已经使用 Auth0 构建了一个简单的认证机制。然而，与自定义认证机制相比，它带来的优势是显而易见的：安全的认证流程，功能齐全的认证管理，等等，仅举几例。

我们可能遗漏的一件事是在构建自定义认证策略时我们有多少控制权；我们可以控制每个认证步骤，表单的外观和感觉，以及创建新账户所需的数据...我们如何使用 Auth0 来做这些？

谈到登录/注册表单方面，我们可以通过导航到 Auth0 仪表板中的**品牌**部分来自定义它：

![图 12.6 – Auth0 品牌部分![图片](img/Figure_12.06_B16985.jpg)

图 12.6 – Auth0 品牌部分

在这里，我们可以直接编辑 HTML 表单以符合我们的应用风格。我们还可以自定义电子邮件模板，使其与我们的 Web 应用外观和感觉保持一致。

另一个重要的话题是 Auth0 如何存储用户数据。默认情况下，它将所有登录数据保存在自己的数据库中，但一旦进入 Auth0 仪表板，我们可以前往认证/数据库/自定义数据库页面，并设置一些自定义脚本来授予访问外部数据库的权限，在那里我们对数据所有权拥有完全的控制。

我们还可以设置一系列 webhooks，以便每次有新用户注册、登录、删除账户等操作时，一个由我们管理的外部 REST API（得到通知），我们可以在外部服务和数据库上复制数据变化。

Auth0 为我们提供了很多定制整个认证体验的可能性，它是市场上最完整的提供商之一。它还提供了一项慷慨的免费计划，我们可以免费测试其许多功能，在决定它是否满足所有需求之前。因此，如果你愿意构建一个生产就绪的应用程序，我强烈建议考虑使用 Auth0 来安全地管理认证。

# 摘要

在本章中，我们看到了使用第三方认证提供商如何在我们处理复杂且敏感的话题，如私人数据管理和用户会话时，避免许多问题。

因此，最终的问题可能是：在什么情况下实施自定义认证策略是有意义的？根据我谦逊的观点，我们应该尽量避免在几乎所有场景下编写自定义认证机制，除非我们与一个能够检测安全漏洞并识别整个认证流程中漏洞的专家团队合作。

有许多好的 Auth0 替代品（NextAuth.js、Firebase、AWS Cognito 等），复制它们经过实战检验的功能风险太大。

如果你不喜欢与外部供应商合作，你也可以使用任何 Web 框架及其内置的认证策略。例如，假设你习惯使用 Ruby on Rails、Laravel 或 Spring Boot。在这种情况下，这些都是在外部认证供应商之上的优秀替代品。它们也会为你提供你可能需要的所有灵活性和安全性，同时得到社区的大量支持和持续的安全发布和修复。

另一个选择可能是使用无头 CMS 来管理用户及其数据；例如，开源 CMS 如 Strapi，它原生支持认证，并允许我们利用社区和开发 CMS 的公司支持的认证机制。

在任何情况下，实施自定义认证是一项非常有教育意义的任务，因为它教会你很多关于安全机制如何工作以及如何保护自己免受恶意用户侵害的知识。例如，在下一章中，我们将构建一个使用 GraphCMS 的电子商务网站；想象一下，如果我们在这里实施一个自定义认证机制，让恶意用户利用漏洞并访问用户的私人数据，这会值得冒险吗？
