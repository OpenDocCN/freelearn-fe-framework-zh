# *第十一章*：创建认证体验

在本书的上下文中，**认证**是指验证用户在网站中是否是他们所说的那个人。一旦他们的身份得到验证，我们就可以展示只有他们才能看到的内容。这可能包括他们的个人资料页面、送货地址、银行详情等等。在本章中，我们将更多地关注如何实现用于认证服务的路由，而不是关注如何实现认证服务或用户认证时显示什么内容。在将此知识应用于具有两种不同客户端实现的 Gatsby 站点之前，我们将回顾一下在传统 React 应用程序中是如何做到这一点的。

在本章中，我们将涵盖以下主题：

+   React 应用程序中的路由和认证

+   在 Gatsby 中使用客户端路由进行认证

+   在 Gatsby 中使用上下文进行全局认证

# 技术要求

要完成本章，你需要完成*第十章*，*创建 Gatsby 插件*。你还需要一个 GitHub 账户。

本章的代码可以在[`github.com/PacktPublishing/Elevating-React-Web-Development-with-Gatsby-4/tree/main/Chapter11`](https://github.com/PacktPublishing/Elevating-React-Web-Development-with-Gatsby-4/tree/main/Chapter11)找到。

# React 应用程序中的路由和认证

要实现认证体验，我们将使用**路由**。在我们深入探讨如何在 Gatsby 中实现路由之前，让我们先熟悉一下 React 应用程序中的路由工作原理。路由是用户在应用程序的不同部分之间导航的过程。

对于这个示例，我将使用`create-react-app`启动一个 React 项目。我已经包括了其安装步骤，但你可以选择跳过它们并使用你自己的 React 实现。*请将本节的示例与你的 Gatsby 项目分开*。

重要提示

在以下示例中，我们将使用`@reach/router`包进行路由。Gatsby 底层使用`@reach/router`，因此在我们转向在 Gatsby 中实现它们时，使用这个包在 React 中会很容易识别模式。

作为 React 开发者，路由是构建应用程序的常见部分——让我们回顾一下路由基础知识：

1.  为此示例创建一个新的文件夹。在这个新文件夹中打开一个终端，并运行以下命令：

    ```js
    npx create-react-app .
    ```

1.  在同一个终端中，运行以下命令：

    ```js
    @reach/router package within the project.
    ```

1.  打开`src/App.js`，将其替换为以下代码：

    ```js
    import { Router, Link } from "@reach/router";
    const Nav = () => (
      <nav>
        <Link to="/">Homepage</Link> | <Link
          to="about">About Me</Link>
      </nav>
    );
    {/* Code continued in next step */}
    ```

    在这里，我们已从`@reach/router`包中导入了`Router`和`Link`。我们还创建了一个`Nav`组件，我们可以用它来访问路由。这个`Nav`组件利用了`@reach/router`中的`Link`组件来提供路由间的导航。

1.  将以下代码添加到`src/App.js`中：

    ```js
    const HomePage = () => (
      <div>
        <Nav />
        <h1>Homepage</h1>
      </div>
    );
    const AboutPage = () => (
      <div>
        <Nav />
        <h1>About Me</h1>
      </div>
    );
    {/* Code continued in next step */}
    ```

    在这里，我们定义了一些用于路由之间的虚拟组件——一个“主页”和一个“关于”页面。所有这些都应该对你来说非常熟悉。

1.  最后，将以下代码添加到`src/App.js`中：

    ```js
    function App() {
      return (
        <Router>
          <HomePage path="/" />
          <AboutPage path="about" />
        </Router>
      );
    }
    export default App;
    ```

    这就是魔法发生的地方。通过将组件包裹在`Router`组件中，我们可以根据当前的 URL 路径切换显示哪个组件。在这个例子中，如果用户位于`/`路径（路由 URL），他们将看到`HomePage`组件，而如果`path`是`/about`，他们将看到`AboutPage`组件。他们可以使用这两个页面中的`Nav`组件在它们之间导航。

1.  通过在根目录下运行`npm start`来启动项目以尝试它。

重要的是要记住，在路由之间导航是快速的，因为所有路由都是在路由器渲染时加载的。当我们进入 Gatsby 时，确保我们只在必要时使用路由器非常重要，因为我们可能会添加页面重量来包含用户可能永远不会看到的组件。

现在我们已经通过一个基本的路由示例，接下来让我们开始添加只有用户登录后才能访问的页面。我们将通过**私有路由**来实现这一点。

## 私有路由

私有路由的行为与其他被`Router`包裹的组件相同，除了它有一个认证条件。如果条件不满足，用户将不会看到请求的内容，而是会被重定向到登录屏幕进行认证。现在让我们通过将之前公开的关于页面转换为私有路由来尝试一下：

1.  首先，我们需要定义我们的认证条件。在这个例子中，我们将保持简单。要被认为是“认证的”，用户必须已经调用了`login`函数，我们将通过登录页面上的按钮来触发它。为了实现这个条件，我们将创建一个可以存储当前认证状态的上下文。创建一个名为`auth-context.js`的新文件，并添加以下代码：

    ```js
    import React, { useState, useContext } from "react";
    import { navigate } from "@reach/router";
    const AuthContext = React.createContext();
    export const AuthProvider = ({ …props }) => {
      {/* Code continued in next step */}
    };
    export const useAuth = () => useContext(AuthContext);
    export default AuthContext;
    ```

    在这里，我们正在设置我们的授权上下文的模板。我们正在创建一个`useAuth`钩子来访问我们将在下一步定义的上下文值。

1.  在`auth-context.js`文件中的`AuthProvider`中添加以下代码：

    ```js
    Const [authenticated, setAuthenticated] =
     useState(false);
      const login = () => {
        // Make authentication request here and only
           trigger the following if successful.
        setAuthenticated(true);
        navigate("/");
      };
      const logout = () => {
        setAuthenticated(false);
        navigate("/login");
      };
      return (
        <AuthContext.Provider
          value={{
            login,
            logout,
            authenticated,
          }}
          {...props}
        />
      );
    ```

    在这里，我们创建了一个名为 `authenticated` 的 `useState` 值来跟踪用户是否已认证。然后我们创建了一个将 `authenticated` 设置为 `true` 的 `login` 函数。在这个函数中，你会向你的认证服务发送请求并验证用户，然后进行认证。很可能会也有一些关于用户的信息可以存储在你的状态或本地存储中。如果你添加了额外的信息，确保在 `logout` 函数中清除它。目前，`logout` 函数只是将 `authenticated` 设置为 `false` 并将用户导航回登录页面。在 `AuthContext.Provider` 中，我们暴露了 `login` 和 `logout` 函数，以及 `authenticated` 状态，给应用程序的其他部分。

1.  导航到你的 React 应用程序的 `index.js` 文件，并使用以下代码进行修改：

    ```js
    import React from "react";
    import ReactDOM from "react-dom";
    import "./index.css";
    import App from "./App";
    AuthProvider, we would not be able to access the authentication context within the application. 
    ```

1.  我们的认证条件现在已经定义，因此我们可以利用它来创建一个私有路由组件。创建一个名为 `PrivateRoute.js` 的新文件，并添加以下代码：

    ```js
    import React from "react";
    import { navigate } from "@reach/router"
    import { useAuth } from "./auth-context";
    const PrivateRoute = ({
      component: Component,
      ...rest
    }) => {
      const { authenticated } = useAuth();
      if (!authenticated) {
        navigate("/login");
        return null;
      }
      return <Component {...rest} />;
    };
    export default PrivateRoute;
    ```

    这个 `PrivateRoute` 组件使用 `useAuth` 钩子中的 `authenticated` 状态来有条件地渲染一个给定的组件。如果用户已认证，该组件将被渲染。然而，如果他们未认证，用户将被导航到 `login` 路由。

1.  返回你的 `App.js` 文件并更新文件，以下为导入：

    ```js
    import { useAuth } from "./auth-context";
    import PrivateRoute from "./PrivateRoute";
    ```

    在这里，我们正在导入 `useAuth` 钩子和我们新创建的 `PrivateRoute` 组件。

1.  使用以下代码修改 `HomePage` 组件：

    ```js
    import { Router, Link } from "@reach/router";
    // Predefined Nav Component Here.
    Const HomePage = () => {
      authenticated state from the useAuth hook to give the user some indication of their authentication status.
    ```

1.  使用以下代码修改 `AboutPage` 组件：

    ```js
    const AboutPage = () => {
      const { logout } = useAuth();
      return (
        <div>
          <Nav />
          <h1>About Me</h1>
          <button onClick={logout}>Logout</button>
        </div>
      );
    };
    ```

    这是我们在本演示中打算使其私有的路径。如果一个用户在这个页面上，我们可以假设他们已经认证，并渲染一个注销按钮，允许他们触发 `logout` 函数。

1.  将 `LoginPage` 组件添加到 `App.js` 中：

    ```js
    const LoginPage = () => {
      const { login } = useAuth();
      return (
        <div>
          <Nav />
          <h1>Login Page</h1>
          <button onClick={login}>Login</button>
        </div>
      );
    };
    ```

    这是一个基本的实现，它使用 `useAuth` 中的 `login` 函数来登录用户。在你的应用程序中，你可能希望用用户输入电子邮件和密码的输入字段来完善它。然后你将传递这些信息给 `login` 函数，以便它可以用作授权请求的一部分。

1.  最后，更新你的 `App` 函数，使其包括你的 `PrivateRoute` 组件：

    ```js
    function App() {
      return (
        <Router>
          <HomePage path="/" />
          <LoginPage path="login" />
          <PrivateRoute component={AboutPage} path="about" />
        </Router>
      );
    }
    export default App;
    ```

1.  通过在根目录下运行 `npm start` 来启动项目。如果你尝试导航到 `/about`，你会注意到你将被重定向到 `/login`，直到你点击了登录按钮。

我们现在对路由和私有路由有了牢固的掌握，所以让我们将本节中获得的知识应用到 Gatsby 上。

# 在 Gatsby 中使用仅客户端路由进行身份验证

虽然这不是常见的做法，但我们也可以在 Gatsby 页面中使用路由器。通常，Gatsby 会将所有路由抽象化，这样我们就不必担心它，但身份验证是我们需要将路由控制权重新掌握在我们手中的一个例子。我们将创建所谓的客户端路由。为了在我们的项目中演示这一点，我们将在`/private`创建一个页面。正如其名称可能暗示的那样，此路径包含一个受保护的页面，我们将通过身份验证来锁定它。让我们开始吧：

重要提示

此示例将与“在 Gatsby 中使用上下文进行全局身份验证”部分的代码发生冲突。最好选择这两种方法中的一种来实现，而不是尝试将它们结合起来。

1.  在`src`目录内创建一个名为`context`的新文件夹。

1.  创建一个名为`auth-context.js`的新文件，并添加以下代码：

    ```js
    import React, { useState, useContext } from "react";
    import { navigate } from "@reach/router";
    const AuthContext = React.createContext();
    export const AuthProvider = ({ ...props }) => {
      {/* Code continued in next step */}
    };
    export const useAuth = () => useContext(AuthContext);
    ```

    在这里，我们正在以与“React 应用程序中的路由和身份验证”示例代码中相同的方式设置身份验证上下文。请注意，我们仍然从`@reach/router`导入`navigate`，而不是从 Gatsby 库中导入。

1.  在`AuthProvider`中添加以下内容：

    ```js
      const [authenticated, setAuthenticated] =
        useState(false);
      const login = async () => {
        // Make authentication request here and only
           trigger the following if successful.
        setAuthenticated(true);
        navigate("/private")
      };
      const logout = () => {
        setAuthenticated(false);
      };
      return (
        <AuthContext.Provider
          value={{
            login,
            logout,
            authenticated,
          }}
          {...props}
        />
      );
    ```

    我们以与 React 演示相同的方式设置了`auth-context.js`文件，但这次我们在登录成功后导航到`/private`。

    重要提示

    在本节中，您将看到与上一节中的 React 演示代码非常相似的代码。请注意，尽管它们很相似，但它们并不相同。不要被诱惑从 React 示例中复制粘贴它们。

1.  将以下内容添加到你的`gatsby-browser.js`和`gatsby-ssr.js`文件中：

    ```js
    import React from "react";
    import { AuthProvider } from "./src/context/auth-
      context";
    export const wrapPageElement = ({ element }) => {
      return <AuthProvider>{element}</AuthProvider>;
    };
    ```

    我们希望确保身份验证上下文在整个应用程序中可用。通过在`gatsby-browser.js`和`gatsby-ssr.js`中添加前面的代码，我们可以确保它可以在任何地方访问。

1.  在`src/components`目录内创建一个名为`PrivateRoute.js`的新文件。

1.  将以下代码添加到新创建的`PrivateRoute.js`文件中：

    ```js
    import React from "react";
    import { navigate } from "gatsby";
    import { useAuth } from "../context/auth-context";
    const PrivateRoute = ({
      component: Component,
      location,
      basepath,
      ...rest
    }) => {
      const { authenticated } = useAuth();
      if (!authenticated) {
        navigate(basepath + "/login");
        return null;
      }
      return <Component {...rest} />;
    };
    export default PrivateRoute;
    ```

    这是`PrivateRoute`组件的 Gatsby 友好实现。请注意，我们正在将`navigate`中的`@reach/router`部分替换为 Gatsby 的实现。这是因为 Gatsby 的实现将以适合 Gatsby 项目的方式处理重定向。如果不进行此替换，当调用`navigate`时，您将看到一个空白屏幕。您还会注意到我们传递了一个名为`basepath`的属性。因为我们的路由器不会位于应用程序的顶部，所以`PrivateRoute`组件必须知道路由器的基路径位置，以确保将相应的用户导航到那里。

1.  在`src/pages`目录内创建一个名为`private`的新文件夹。

1.  在这个新文件夹中，创建一个名为 `[...].js` 的新文件。使用 Gatsby 的 `/private`。这一步至关重要，因为 Gatsby 不了解我们将设置的路由器，因此它需要理解如果它看到以 `/private` 开头的路径，例如 `/private/login`，它需要由这个文件处理，而不是以 404 状态码报错。

1.  将以下代码添加到 `src/pages/private/[...].js` 文件中：

    ```js
    import React from "react";
    import { Router } from "@reach/router";
    import Layout from "../components/layout/Layout";
    import PrivateRoute from "../components/PrivateRoute";
    import { useAuth } from "../context/auth-context";
    const LoginPage = () => {
      const { login } = useAuth();
      return (
        <Layout>
          <h1>Login Page</h1>
          <button onClick={login}>Login</button>
        </Layout>
      );
    };
    const AuthenticatedPage = () => {
      const { logout } = useAuth();
      return (
        <Layout>
          <h1>Authenticated Page</h1>
          <button onClick={logout}>Logout</button>
        </Layout>
      );
    };
    ```

    在这里，我们定义了两个可能可见的路径。要么你会看到 `AuthenticatedPage`，要么如果你没有登录，你会看到登录页面。这两个组件都使用了 `useAuth` 钩子来检索它们所需的函数。

1.  将以下代码添加到 `src/pages/private/[...].js` 文件中：

    ```js
    function PageWithRouter() {
      const basepath = "/private";
      return (
        <Router basepath={basepath}>
          <LoginPage path="login" />
          <PrivateRoute
            basepath={basepath}
            component={AuthenticatedPage}
            path="/"
          />
        </Router>
      );
    }
    export default PageWithRouter;
    ```

    在这一步中，我们定义了我们的 `basepath` —— 这必须与 Gatsby 页面的路径匹配（在这个例子中是 `/private`）。我们将此值作为属性传递给 `Router` 和 `PrivateRoute`。这个例子与 React 例子不同，因为基本路径是需要认证的路径。

1.  通过在根目录中运行 `npm start` 来启动项目。如果你尝试导航到 `/private`，你会注意到你被重定向到 `/private/login`，点击登录按钮将重定向你到 `/private`。

有了这个，我们已经学会了如何在 Gatsby 网站的一个特定部分中添加路由。现在，让我们将注意力转向一个当你整个网站需要认证时可以使用的实现。

# 在 Gatsby 中使用上下文实现网站范围的认证

可能会有这样的情况，你希望你的整个 Gatsby 网站都在认证之后。例如，你可能只为你公司的员工创建了文档网站。让我们看看我们如何使用上下文将每个页面都转换为私有路由：

1.  首先，让我们在 `components` 文件夹中创建一个登录组件。将此文件命名为 `Login.js` 并添加以下代码：

    ```js
    import React from "react";
    const Login = ({login}) => {
      return <button onClick={login}>Login</button>;
    };
    export default Login;
    ```

    你会注意到，与之前我们创建的 `Login` 组件不同，我们不是从上下文中检索 `login` 函数。这个原因将在我们创建上下文时变得清晰。

1.  在 `src` 中创建一个名为 `context` 的文件夹。

1.  在 `context` 文件夹中创建一个名为 `auth-context.js` 的文件并添加以下代码：

    ```js
    import React, { useState, useContext } from "react";
    Login component into our authentication context.
    ```

1.  在 `AuthProvider` 中添加以下代码：

    ```js
      const [authenticated, setAuthenticated] =
        useState(false);
      const login = async () => {
        // Make authentication request here and only
           trigger the following if successful.
        setAuthenticated(true);
      };
      const logout = () => {
        setAuthenticated(false);
      };
      Login component, being sure to pass in the login function as a prop. This does not cause a route change, which can be a great benefit. When a user navigates to a page, their requested path is not lost by navigating away to a login page and, as such, when the user has successfully logged in, they will jump right back into the application in the place they intended to be. As a developer, this can stop you from having to pass redirect URLs around in the browser, which can be a hassle. If, for some reason, you want to keep a few pages public, you can check for the path in this conditional statement and allow some paths to be accessible, even without being authenticated. Note that even on these pages, the `Login` component will be loaded in, despite the fact it is not being used and will add unnecessary page weight.
    ```

1.  将以下代码添加到你的 `gatsby-browser.js` 和 `gatsby-ssr.js` 文件中：

    ```js
    import React from "react";
    import { AuthProvider } from "./src/context/auth-
      context";
    export const wrapPageElement = ({ element }) => {
      return <AuthProvider>{element}</AuthProvider>;
    };
    ```

    我们希望确保认证上下文在应用程序的任何地方都是可用的。通过将此文件添加到 `gatsby-browser.js` 和 `gatsby-ssr.js`，我们可以确保它在任何地方都可以访问。

1.  通过在根目录中运行 `npm start` 并导航到网站上的任何页面来启动项目。你应该会发现你需要在查看页面之前被提示登录。

现在我们已经探讨了两种在 Gatsby 应用程序中实现认证体验的方法，让我们总结一下我们学到了什么。

# 摘要

在本章中，我们探讨了路由和认证体验。我们回顾了 React 中的路由工作原理，并为`@reach/router`创建了私有路由。然后，我们将这些知识应用到 Gatsby 中，创建了一个只有通过登录才能访问的私有页面。最后，我们研究了如何使用上下文来为需要认证的情况将整个应用程序包裹在认证中。

在下一章中，我们将学习另一个高级概念——如何使用套接字来创建利用实时数据的体验。
