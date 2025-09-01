# 第二章：开始使用 JHipster

JHipster 是一个开发平台，它能帮助你从零开始，迅速成为英雄！JHipster 可以帮助你快速创建美观的 Web 应用程序和复杂的微服务架构。JHipster 还提供了各种工具，使用业务实体进一步开发应用程序，并将其部署到各种云服务和平台。在核心上，JHipster 是一个 Yeoman 生成器，用于创建基于 Spring Boot 和 Angular/React 的应用程序。它可以创建单体架构以及具有所有功能开箱即用的微服务架构。

在本章中，我们将涵盖以下主题：

+   为什么使用 JHipster 以及它与传统开发方法相比如何帮助

+   JHipster 的目标是什么？

+   JHipster 中可用的各种服务器端和客户端技术选项

+   准备开发环境

+   JHipster 及其依赖项的安装

Yeoman ([`yeoman.io`](http://yeoman.io)) 是一个脚手架工具，它可以帮助你创建代码生成器。你可以使用它，借助内置的模板引擎和工具，创建任何类型的应用程序生成器。

# 为什么选择 JHipster？

如果你想知道为什么你应该使用 JHipster，那么请想象以下场景。你被要求构建一个 Web 应用程序，比如说一个具有 Angular 前端和 Java 后端的博客，具有用户创建博客文章和根据用户权限显示博客文章的功能。你还被要求构建管理模块，如用户管理、监控等。最后，你必须测试并将应用程序部署到云服务。

如果你以传统方式来应对这个挑战，你很可能会做以下步骤。为了简单起见，让我们跳过细节。所以，步骤如下：

1.  设计架构栈并决定使用各种库（比如说你选择了 Spring 框架作为后端，使用 Spring Security 和 Spring MVC）

1.  创建一个包含所有技术连接的应用程序基础（例如，你必须确保 Angular 客户端和 Spring Security 之间的身份验证流程正确连接）

1.  为应用程序编写构建系统（比如说你使用了 webpack 来构建 Angular 客户端，Gradle 来构建服务器端）

1.  为基础编写集成测试和单元测试

1.  创建管理模块

1.  设计业务实体，并在 Angular 客户端和 Java 服务器端以及测试覆盖率下创建它们

1.  编写所有业务逻辑，测试应用程序，并部署它

虽然这种方法确实有效，但对于这个简单的应用程序，你可能需要花费四到六周的时间，具体取决于团队规模。现在，超过 70%的努力将花费在编写样板代码和确保所有库能够良好协作上。现在，如果你说我可以在不到 30 分钟内使用 JHipster 开发、测试和部署这个应用程序，你会相信我吗？是的，你可以，同时还能获得高质量的、带有许多额外功能的实际生产级代码。我们将在下一章中看到这一功能的应用，我们将使用 JHipster 构建一个真实世界的应用程序。

# JHipster 的目标和采用情况

JHipster 的目标是为开发者提供一个平台，在这里你可以专注于业务逻辑，而不是担心将不同的技术连接在一起，同时也提供了一个出色的开发者体验。当然，你可以在组织内部或从互联网上使用可用的样板代码，并尝试将它们连接起来，但这样你会浪费很多时间重新发明轮子。使用 JHipster，你将创建一个现代 Web 应用程序或微服务架构，其中所有必需的技术都已连接并开箱即用，例如以下内容：

+   后端基于稳健且高性能的 Spring Framework 的 Java 堆栈

+   基于 Bootstrap 的丰富移动优先前端，支持 Angular 或 React

+   经受战火考验的微服务架构，统一了 Netflix OSS、Elastic stack 和 Docker

+   使用 Maven/Gradle、webpack 和 Yarn/NPM 的出色工具和开发工作流程

+   开箱即用的持续集成，使用 Jenkins、Travis 或 GitLab

+   开箱即用的出色 Docker 支持和 Kubernetes、Rancher、OpenShift 等编排工具的支持

+   支持各种云部署的即插即用功能

+   最重要的是，拥有大量最佳实践和行业标准的手指尖上的优秀代码

Netflix OSS ([`netflix.github.io`](https://netflix.github.io)) 是由 NETFLIX, INC 团队生产的开源工具和软件集合，旨在面向微服务架构。Elastic stack ([`www.elastic.co/products`](https://www.elastic.co/products))（以前称为 ELK stack）是一系列软件工具集合，有助于监控和分析由 Elasticsearch ([`www.elastic.co`](https://www.elastic.co))团队开发的微服务。

随着 Spring Boot 和 Angular 的兴起，JHipster 的受欢迎程度稳步上升，许多开发者开始将其作为实际上的 Web 开发框架。根据撰写本文时的官方统计数据（2018 年初），每月生成的应用程序超过 5,000 个，JHipster 的安装量约为 1 百万次。它拥有超过 400 位贡献者，其中包括来自 Google、RedHat、Heroku 等机构的官方贡献。

# 技术介绍

JHipster 默认支持大量现代网络应用程序技术。其中一些被用作生成应用程序的基础或核心，而一些技术则通过在应用程序生成过程中所做的选择进行选择。让我们简要地看看主要针对单体应用程序支持的不同技术：

+   客户端技术

+   服务器端技术

+   数据库选项

支持的技术还有很多，我们将在后续章节中探讨，当涉及到微服务时。

# 客户端技术

在全栈开发中，客户端技术的作用已经从仅使用 JavaScript 进行客户端验证，发展到使用客户端 MVVM 框架编写完整的单页应用程序。对于刚开始接触客户端领域的开发者来说，所使用的框架和工具链变得复杂且令人不知所措。幸运的是，JHipster 为以下广泛使用的客户端技术提供了支持。让我们简要地看一下，并熟悉我们将使用的重要工具和技术。无需担心它会令人不知所措，我们将在本书的进程中深入探讨一些更重要的话题。

# HTML5 和 CSS3

网络技术，尤其是 HTML 和 CSS，由于现代浏览器的出色支持，已经经历了重大更新，并且每天都在变得越来越好。

# HTML5

HTML5 ([`developer.mozilla.org/en-US/docs/Web/Guide/HTML/HTML5`](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/HTML5)) 是 **HTML** (超文本标记语言) 标准的最新版本，它引入了新的元素、属性和行为。该术语用于统称构建现代网络应用程序所使用的所有 HTML 技术。这一版本引入了对离线存储、WebSockets、Web Workers、WebGL 等功能的支持。JHipster 还使用了来自 HTML5 Boilerplate ([`html5boilerplate.com`](https://html5boilerplate.com)) 的最佳实践。

**HTML5 Boilerplate** 是一组现代技术、默认设置和最佳实践，它能更快地启动现代网络开发。

# CSS3

CSS3 ([`developer.mozilla.org/en-US/docs/Web/CSS/CSS3`](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS3)) 是 **层叠样式表** (CSS) 规范的最新版本。它增加了对媒体查询、动画、flexbox、圆角等功能的支持。CSS3 使得原生动画元素、应用特殊效果、应用过滤器等成为可能，从而消除了之前使用的许多 JavaScript 诡计。

弹性盒模型，或称 flexbox，是一种布局模式 ([`developer.mozilla.org/en-US/docs/Web/CSS/Layout_mode`](https://developer.mozilla.org/en-US/docs/Web/CSS/Layout_mode))，它可以替代传统上使用的盒模型。这使得拥有灵活的盒模型，使得响应式布局更容易处理，无需处理浮动和边距塌陷问题。

# Sass

**语法优美的样式表** (**Sass**) ([`sass-lang.com`](http://sass-lang.com)) 是一种 CSS 扩展语言。它在编译时预处理并转换为 CSS。它与 CSS 有类似的语义，并且与所有版本的 CSS 100% 兼容。它还支持嵌套语法、变量、混合、继承、部分等高级功能。Sass 使得重用 CSS 和编写可维护的样式表成为可能。

# Bootstrap

Bootstrap ([`getbootstrap.com`](https://getbootstrap.com)) 是一种用于现代 Web 开发的响应式 UI 框架。它为 Web 开发提供了一种移动优先的方法，包括完全响应式的实用工具和 UI 组件。Bootstrap 4 是最新版本，使用 flexbox 进行布局，并完全使用 Sass 编写，这使得它更容易定制。Bootstrap 支持一个 12 列的网格框架，让您能够轻松构建响应式网页。JHipster 使用 ng-bootstrap ([`ng-bootstrap.github.io`](https://ng-bootstrap.github.io))，因此使用纯 Angular 组件而不是 Bootstrap 提供的组件，这些组件是使用 JQuery 构建的，Bootstrap 仅用于样式。

移动优先的 Web 开发是一种设计方法，首先为较小的屏幕尺寸设计 UX/UI，从而迫使您关注要展示的最重要数据/元素。然后，这种设计逐渐增强以适应更大的屏幕尺寸，使最终结果既响应又高效。

# MVVM 框架

**模型-视图-视图-模型** (**MVVM**) 是由微软最初开发的一种架构模式。它有助于将客户端（GUI）开发与服务器端（数据模型）抽象或分离。视图模型是视图的抽象，代表模型中的数据状态。使用 JHipster，您可以选择 Angular 或 React 作为客户端框架。

# Angular

AngularJS ([`angularjs.org`](https://angularjs.org))(版本 1.x) 是由 Google 维护的客户端 MVVM 框架，有助于开发 **单页应用程序** (**SPA**)。它基于声明式编程模型，并扩展了标准 HTML，通过指令添加了额外的行为、元素和属性。它还引入了诸如 **SPA** 的概念，并扩展了标准 HTML，通过指令添加了额外的行为、元素和属性。

Angular ([`angular.io`](https://angular.io))(版本 2 及以上) 是框架的完全重写，因此与 AngularJS 不兼容。Angular 使用 TypeScript 编写，并推荐使用 TypeScript 编写 Angular 应用程序。Angular 移除了一些在 AngularJS 中使用的概念，例如作用域、控制器、工厂等。它还有绑定属性和事件的不同的语法。另一个主要区别是 Angular 库是模块化的，因此您可以选择所需的模块，以减少包大小。Angular 还引入了诸如 **AOT** (**提前编译**)、懒加载、响应式编程等高级概念。

TypeScript 是 ECMAScript 6 (ES6 - JavaScript 的第 6 个版本) 的超集，并且与 ES5 兼容。它具有静态类型、泛型、类属性可见性修饰符等附加功能。由于 TypeScript 是 ES6 的超集，我们还可以使用 ES6 功能（[`es6-features.org`](http://es6-features.org)），如模块、lambda（箭头函数）、生成器、迭代器、字符串模板、反射、扩展运算符等。

# React

React ([`reactjs.org`](https://reactjs.org)) 不是一个完整的 MVVM 框架。它是一个用于构建客户端视图或用户界面的 JavaScript 库。它由 Facebook 开发并支持，背后有一个充满活力的社区和生态系统。React 采用 HTML in JS 方法，并有一个称为 **JSX** 的特殊格式来帮助我们编写 React 组件。与 Angular 不同，React 没有太多概念或 API 需要学习，因此更容易上手，但 React 只关注渲染 UI，因此要获得 Angular 提供的类似功能，我们不得不将 React 与其他库如 React Router ([`reacttraining.com/react-router`](https://reacttraining.com/react-router))、Redux ([`redux.js.org`](https://redux.js.org))、MobX ([`mobx.js.org`](https://mobx.js.org)) 等搭配使用。JHipster 使用 React 以及 Redux 和 React Router，类似于 Angular，JHipster 也为 React 使用 TypeScript。但这不是必需的，因为 React 也可以使用 JavaScript 编写，最好是 ES6 ([`es6-features.org`](http://es6-features.org))。React 由于使用了虚拟 DOM ([`reactjs.org/docs/faq-internals.html`](https://reactjs.org/docs/faq-internals.html)) 来操作视图而不是实际浏览器 DOM，因此渲染速度快。

如果你正在启动一个新项目，最好选择 Angular 或 React，因为它们都得到了良好的维护。然而，在 JHipster 的旧版本中，AngularJS 1.x 也被提供作为选项，但它正在成为过时的技术，并将很快在 JHipster 5.x 中被停止支持。JHipster 将为仍然对使用 AngularJS 1.x 感兴趣的人提供官方蓝图。只需运行命令 `jhipster --blueprint generator-jhipster-angularjs` 即可使用它。

# 构建工具

客户端已经发展了很多，变得和服务器端一样复杂，因此它需要更多的工具来产生优化的结果。你需要一个构建工具来转换、最小化和优化你的 HTML、JavaScript 和 CSS 代码。其中最受欢迎的是 Webpack。JHipster 使用 Webpack 为 Angular 和 React。

# Webpack

Webpack ([`webpack.js.org`](https://webpack.js.org)) 是一个具有非常灵活的加载器/插件系统的模块打包器。Webpack 会遍历依赖图，并通过配置的加载器和插件传递它。使用 Webpack，你可以将 TypeScript 转换为 JavaScript，最小化和优化 CSS 和 JS，编译 Sass，修订，对你的资产进行哈希处理等。Webpack 可以通过称为 **摇树干** 的过程删除死代码，从而减小包的大小。Webpack 使用配置文件进行配置，可以从命令行或通过 NPM/YARN 脚本运行。

# BrowserSync

BrowserSync ([`browsersync.io`](https://browsersync.io)) 是一个 NodeJS 工具，通过同步多个浏览器和设备上的网页文件更改和交互，帮助进行浏览器测试。它提供了诸如文件更改时自动重新加载、同步 UI 交互、滚动等功能。它集成了 Webpack/GulpJS，以提供高效的开发环境。它使得在多个浏览器和设备上测试网页变得非常简单。

# 测试工具

那些客户端代码不需要单元测试的日子已经过去了。随着客户端框架的发展，测试的可能性也得到了提高。现在有许多框架和工具可用于单元测试、端到端测试等。JHipster 使用 Karma 和 Jasmine 开箱即地为客户端代码创建单元测试，并支持使用 Protractor 创建端到端测试。

# Karma

Karma ([`karma-runner.github.io/2.0/index.html`](https://karma-runner.github.io/2.0/index.html)) 是一个可以在真实浏览器中执行 JavaScript 代码的测试运行器。它创建一个 web 服务器，并针对源代码执行测试代码。Karma 支持多个测试框架，如 Jasmine、Mocha 和 Qunit，并且与持续集成工具集成良好。

# Protractor

Protractor ([`www.protractortest.org`](http://www.protractortest.org)) 是由 Angular 团队开发的一个端到端测试框架。它最初是为 Angular 和 AngularJS 应用程序设计的，但它足够灵活，可以与任何框架一起使用，例如 React、JQuery、VueJS 等。Protractor 使用 Selenium WebDriver API 对真实浏览器运行 e2e 测试。

# 国际化

国际化（i18n）是当今非常重要的一个特性，JHipster 支持开箱即用的国际化。在应用程序创建过程中可以选择多种语言。在客户端，这是通过为每种语言存储 JSON 文件中的 GUI 文本，并使用 Angular/React 库根据运行时选择的语言动态加载这些文本来实现的。

你知道为什么国际化被缩写为 i18n 吗？因为 I 和 N 之间有 18 个字符。在 Web 技术中还有其他类似命名的缩写，例如，无障碍性（a11y）、本地化（l10n）、全球化（g11n）和可本地化（l12y）。

# 服务器端技术

网络开发中的服务器端技术已经发展了很多，随着 Spring 和 Play 等框架的兴起，Java EE 的需求减少了，为更多功能丰富的替代品打开了大门，例如 Spring Boot。一些核心技术，如 Hibernate，将长期存在，而像 JWT、Liquibase、Swagger、Kafka 和 WebSockets 等新概念则带来了很多额外的机会。让我们快速了解一下 JHipster 支持的一些重要技术；我们将在本书的后续部分遇到这些技术，并对其中一些技术进行更深入的探讨。

# Spring 框架

Spring 框架([`spring.io`](https://spring.io))可能是 Java 世界中最棒的事情。它改善了 Java 网络应用领域的格局。在 Spring 兴起之前，JavaEE 供应商垄断了这一领域，Spring 兴起后不久，它成为了 Java 网络开发者的首选，为 JavaEE 带来了竞争。在核心上，Spring 是一个提供依赖注入和应用程序上下文的**控制反转**(**IoC**) ([`docs.spring.io/spring/docs/current/spring-framework-reference/core.html#beans`](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#beans))容器。Spring 的主要特性或 Spring 三角，以一致的方式将 IoC、**面向切面编程**(**AOP**) ([`docs.spring.io/spring/docs/current/spring-framework-reference/core.html#aop`](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#aop))和技术抽象结合起来。该框架有众多模块，针对不同的任务，如数据管理、安全、REST、网络服务等。Spring 框架及其模块是免费和开源的。让我们更详细地了解一下一些重要的模块。 

IoC 是一种软件设计模式，其中自定义或任务特定的代码由库调用，而不是传统的程序性编程方法，在需要时自定义代码调用库。IoC 有助于使代码更加模块化和可扩展。AOP 提供了另一种思考程序结构的方式。模块化的单位是方面，它使诸如事务管理等跨越多个类型和对象的关注点模块化。

# Spring Boot

Spring Boot ([`projects.spring.io/spring-boot`](https://projects.spring.io/spring-boot)) 是目前广泛使用的 Java 网络应用程序开发解决方案。它采用了一种基于配置的约定优先方法。它是完全由配置驱动的，使得使用 Spring 框架和许多其他第三方库变得愉快。Spring Boot 应用程序是生产级别的，可以在安装了 JVM 的任何环境中运行。它使用嵌入式 Servlet 容器（如 Tomcat、Jetty 或 Undertow）来运行应用程序。它尽可能自动配置 Spring，并为许多模块和第三方库提供启动 POM。它不需要任何 XML 配置，并允许您使用 Java 配置自定义自动配置的 bean。

JHipster 默认使用 Undertow 作为生成应用程序中的嵌入式服务器。Undertow 非常轻量级且启动速度快，非常适合轻量级应用程序的开发和生产。

# Spring Security

Spring Security ([`projects.spring.io/spring-security`](https://projects.spring.io/spring-security)) 是基于 Spring 框架的应用程序中事实上的安全解决方案。它提供 API 和实用工具来管理安全的所有方面，例如身份验证和授权。它支持广泛的身份验证机制，如 OAuth2、JWT、会话（Web 表单）、LDAP、**SSO**（**单点登录**）服务器、**JAAS**（**Java 身份验证和授权服务**）、Kerberos 等。它还具有诸如记住我、并发会话等功能。

# Spring MVC

Spring MVC ([`docs.spring.io/spring/docs/current/spring-framework-reference/web.html`](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html)) 是在 Spring 应用程序中与 Servlet API 交互的事实上的解决方案。它是一个基于请求的系统，并抽象化 Servlet API 以简化控制器的设计，以服务 HTTP 请求。REST 是当今设计 API 端点的既定标准，Spring MVC REST 是一个特定的子集，使得设计和实现 RESTful 服务更加容易。

# Spring 数据

Spring data ([`projects.spring.io/spring-data`](http://projects.spring.io/spring-data)) 是一个模块，它抽象了多种不同的数据访问技术和数据库的数据访问操作。它提供了一个一致的 API，以便与不同的底层实现无缝工作。这使我们免于担心底层数据库和数据访问技术。它具有诸如从方法名称动态生成查询、自定义对象映射抽象等功能。Spring data 支持 JPA、MongoDB、Redis、Elasticsearch 等多种数据源的工作。它还允许您将 Spring data 存储库作为 RESTful 资源导出。

# 安全

在现代 Web 应用程序中，有多种方式来实现身份验证和授权。Spring 安全支持广泛的机制，正如我们之前所看到的，JHipster 提供了对以下标准的支持。

# JWT

**JSON Web Token**（JWT）([`jwt.io`](https://jwt.io)) 是一个开放的行业标准，用于安全令牌。JWT 认证通过服务器和客户端传递和验证声明来实现。当用户凭据成功验证后，服务器生成 JWT 令牌并将其返回给客户端。客户端将此令牌本地存储，并在稍后通过在请求头中传递令牌来请求从服务器获取受保护资源。这是一个无状态的认证机制。这在本章的详细说明中有所解释，第九章，*使用 JHipster 构建微服务*。

# 会话

基于会话的身份验证是传统的基于表单的认证机制，其中服务器为验证过的用户凭据创建并维护一个会话。这是有状态的，除非你使用分布式 HTTP 会话，例如使用 Hazelcast 这样的分布式缓存，或者使用专用 Web 服务器或负载均衡器的会话复制功能，否则通常不太可扩展。JHipster 在标准机制之上添加了许多功能，例如存储在数据库中的安全令牌，可以被无效化，用于“记住我”机制等。

# OAuth2

OAuth2 ([`developer.okta.com/blog/2017/06/21/what-the-heck-is-oauth`](https://developer.okta.com/blog/2017/06/21/what-the-heck-is-oauth)) 是一个无状态认证和授权协议。与之前提到的机制相比，该协议允许应用程序获取对服务用户账户的有限访问权限。用户认证委托给一个服务，通常是 OAuth2 服务器。与之前提到的机制相比，OAuth2 的设置更为复杂。JHipster 支持使用**OpenID Connect**（OIDC）设置 OAuth，并且可以开箱即用使用 Keycloak ([`keycloak.org`](https://keycloak.org)) 或 Okta ([`developer.okta.com/blog/2017/10/20/oidc-with-jhipster`](https://developer.okta.com/blog/2017/10/20/oidc-with-jhipster))。

# 构建工具

JHipster 支持使用 Maven 或 Gradle 作为服务器端代码的构建工具。两者都是免费且开源的。

# Maven

Maven ([`maven.apache.org`](https://maven.apache.org)) 是一个构建自动化工具，它使用一个名为 `pom.xml` 的 XML 文档来指定应用程序的构建方式和其依赖项。插件和依赖项从中央服务器下载并本地缓存。Maven 构建文件被称为**项目对象模型**（POM），它描述了构建过程本身。Maven 有着悠久的历史，与 Gradle 相比，它更加稳定和可靠。它还有一个庞大的插件生态系统。

# Gradle

Gradle ([`gradle.org`](https://gradle.org)) 是一个构建自动化工具，它使用 Groovy DSL 来指定构建计划和依赖关系。它是一个强劲的竞争者，迅速获得流行度和采用率。Gradle 比 Maven 更灵活、功能更丰富，使其成为非常复杂构建设置的理想选择。Gradle 的最新版本在速度和功能方面都轻松超越了 Maven。Gradle 的另一个独特优势是能够在构建脚本中编写标准的 Groovy 代码，这使得几乎可以程序化地完成任何事情。它还拥有出色的插件支持。

# Hibernate

Hibernate ([`hibernate.org`](http://hibernate.org)) 是最流行的 **ORM**（**对象关系映射**）工具，用于 Java。它通过 Java 注解帮助将面向对象的领域模型映射到关系数据库模式。Hibernate 实现了 **JPA**（**Java 持久化 API**），并且是 JPA 实现的首选提供商。Hibernate 还提供了许多附加功能，如实体审计、Bean 验证等。Hibernate 会根据底层数据库语义自动生成 SQL 查询，使得切换应用程序的数据库变得非常容易。它还使应用程序数据库独立，没有任何供应商锁定。Hibernate 是免费的开源软件。

# Liquibase

Liquibase ([`www.liquibase.org`](http://www.liquibase.org)) 是一个免费的开源数据库版本控制工具。它允许您使用配置文件跟踪、管理和应用数据库模式更改，而无需与 SQL 打交道。它是数据库无关的，并且与 JPA 兼容良好，使应用程序数据库独立。Liquibase 可以在应用程序内部运行，使得数据库设置和管理无缝，并且对于大多数数据库管理来说，无需 DBA。Liquibase 还可以向数据库添加或从数据库中删除数据，使其非常适合迁移。

# 缓存

缓存是软件开发中的良好实践，并且可以显著提高读取操作的性能。可以为 Hibernate 的第二级缓存启用缓存，也可以使用 Spring Cache 抽象在方法级别启用缓存。JHipster 支持 EhCache、Hazelcast 和 Infinispan 提供的与 JCache 兼容的 Hibernate 第二级缓存。

# Ehcache

Ehcache ([`www.ehcache.org`](http://www.ehcache.org)) 是一个开源的 JCache 提供商，并且是使用最广泛的 Java 缓存解决方案之一。它与 JCache 兼容，对于非集群应用程序来说是一个不错的选择。对于集群环境，需要额外的 Terracotta 服务器。它稳定、快速，并且易于设置。

# Hazelcast

Hazelcast ([`hazelcast.org`](https://hazelcast.org)) 是一个开源的分布式内存数据网格解决方案。它对集群应用程序和分布式环境有出色的支持，因此成为缓存的一个好选择。虽然 Hazelcast 有许多其他功能和用例，但缓存仍然是其中之一。由于其分布式特性，它高度可扩展，是微服务的一个好选择。

# Infinispan

Infinispan ([`infinispan.org`](http://infinispan.org)) 是来自 Red Hat 的分布式缓存和键值存储。它是免费和开源的。它支持集群环境，因此是微服务的一个好选择。它还具有内存数据网格、MapReduce 支持等功能。

# Swagger

OpenAPI 规范（之前被称为 **Swagger 规范**）是设计和使用 RESTful 网络服务和 API 的开放标准。OpenAPI 规范是由包括 Google、Microsoft 和 IBM 在内的多家公司共同创立的标准。Swagger ([`swagger.io`](https://swagger.io)) 的名字现在用于相关的工具。JHipster 支持使用 Swagger code-gen 进行 API-first 开发模型，同时也支持使用 Swagger UI 进行 API 可视化。

# Thymeleaf

Thymeleaf ([`www.thymeleaf.org`](http://www.thymeleaf.org)) 是一个与 Spring 集成非常良好的开源 Java 服务器端模板引擎。Thymeleaf 可以用于在服务器端生成网页，用于模板化电子邮件消息等。尽管服务器端网页模板正逐渐被客户端 MVVM 框架所取代，但如果想要比单页应用使用 Angular 更多的功能，它仍然是一个有用的工具。

# Dropwizard metrics

Dropwizard metrics ([`metrics.dropwizard.io/4.0.0/`](http://metrics.dropwizard.io/4.0.0/)) 是一个用于测量 Java 网络应用程序性能的出色开源库。与 Spring Boot 配对，通过测量 REST API 的性能、测量缓存层和数据库的性能等方式，可以带来很多价值。Dropwizard 提供了方便的注解来标记要监控的方法。它支持计数器、计时器等。

# WebSocket

WebSocket([`developer.mozilla.org/en-US/docs/Web/API/WebSockets_API`](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API)) 是一种在 TCP 之上工作的通信协议。它通过单个 TCP 连接提供全双工通信通道。它由 W3C([`www.w3.org`](https://www.w3.org))标准化。它轻量级，并允许客户端和服务器之间进行实时通信。在 Web 应用方面，这允许服务器在客户端没有请求的情况下与浏览器中的客户端应用进行通信。这为实时推送数据从服务器到客户端以及实现实时聊天、通知等打开了大门。在服务器端，JHipster 依赖于 Spring，它提供了必要的支持([`spring.io/guides/gs/messaging-stomp-websocket/`](https://spring.io/guides/gs/messaging-stomp-websocket/))以与 WebSocket 协同工作。

# Kafka

Kafka([`kafka.apache.org`](https://kafka.apache.org)) 是一个开源的流处理系统。它有一个基于分布式 pub/sub 的消息队列用于存储。它的容错性和可扩展性帮助它取代 JMS 和 AMQP，成为首选的消息队列。Spring 在 Kafka 之上提供了一个抽象层，使其更容易配置和使用 Kafka。

JMS（Java 消息服务）([`en.wikipedia.org/wiki/Java_Message_Service`](https://en.wikipedia.org/wiki/Java_Message_Service)) 是为 Java EE 开发的消息标准，它允许使用主题和队列在组件之间发送和接收异步消息。AMQP（高级消息队列协议）([`www.amqp.org/`](https://www.amqp.org/)) 是一种面向消息中间件的开源标准协议，提供如队列、路由和发布/订阅机制等功能。

# 测试框架

服务器端测试可以主要分为单元测试、集成测试、性能测试和行为测试。JHipster 支持所有这些测试，其中 JUnit 是默认提供的，其他则是可选的。

# JUnit

JUnit([`junit.org/junit5/`](https://junit.org/junit5/)) 是最广泛使用的 Java 测试框架。它是一个免费的开源软件。最初它旨在进行单元测试，但与 Spring Test Framework([`docs.spring.io/spring/docs/current/spring-framework-reference/testing.html#testing-introduction`](https://docs.spring.io/spring/docs/current/spring-framework-reference/testing.html#testing-introduction))结合使用后，也可以用于集成测试。JHipster 使用 JUnit 和 Spring Test Framework 创建单元测试和 REST API 集成测试。

# Gatling

Gatling ([`gatling.io/`](https://gatling.io/)) 是一款免费且开源的性能和负载测试工具。它基于 Scala，并使用 Scala DSL 编写测试规范。它能够创建详细的负载测试报告，并可以用来模拟系统上的各种负载。它是性能关键应用中必备的工具。

# Cucumber

Cucumber ([`cucumber.io/`](https://cucumber.io/)) 是一个主要用于验收测试的**行为驱动开发**（**BDD**）测试框架。它使用一种名为**Gherkin**的语言解析器，这种语言解析器非常易于人类阅读，看起来类似于普通的英语。

# 数据库选项简介

今天，市场上提供了各种各样的数据库选项。这些可以大致分为以下几类：

+   SQL 数据库

+   NoSQL 数据库

您可以访问[`db-engines.com/en/ranking`](https://db-engines.com/en/ranking)以查看不同数据库的流行度。

JHipster 支持一些最广泛使用的数据库，具体详情请见此处。

# SQL 数据库

SQL 数据库或**关系数据库管理系统**（**RDBMS**）是指支持关系表型数据模型的数据库。它们支持由固定名称和列/属性数量以及固定数据类型定义的表模式。表中的每一行都包含每个列的值。表可以相互关联。

# H2

H2 ([`www.h2database.com/html/main.html`](http://www.h2database.com/html/main.html)) 是一款免费嵌入式的 RDBMS，通常用于开发和测试。它通常可以在文件系统模式下运行以实现持久性，或在内存模式下运行。它具有非常小的体积，并且配置和使用极其简单。它没有提供其他主流数据库引擎所提供的许多企业级功能，因此通常不适用于生产使用。

# MySQL

MySQL ([`www.mysql.com/`](https://www.mysql.com/)) 是最受欢迎的数据库引擎之一，它是一款免费且开源的软件。它由甲骨文公司开发，但也拥有一个非常活跃的社区。它具备诸如分片、复制、分区等企业级功能。它是目前最受欢迎的 SQL 数据库之一。

# MariaDB

MariaDB ([`mariadb.org/`](https://mariadb.org/)) 是一个符合 MySQL 规范的数据库引擎，它还特别关注安全性、性能和高可用性。它越来越受欢迎，被视为 MySQL 的良好替代品。它是一款免费且开源的软件。

# PostgreSQL

PostgreSQL ([`www.postgresql.org/`](https://www.postgresql.org/)) 是另一个非常受欢迎的免费和开源数据库系统。它由一个社区积极维护。PostgreSQL 的一个独特特性是具有索引和查询 JSON 对象的先进 JSON 对象存储能力。这使得它能够作为 NoSQL 数据库或混合模式使用。它还具备复制、高可用性等企业级功能。

# MS SQL

MS SQL 服务器 ([`www.microsoft.com/nl-nl/sql-server/sql-server-2017`](https://www.microsoft.com/nl-nl/sql-server/sql-server-2017)) 是由微软开发和支持的数据库系统，是一个企业级数据库系统。它是商业软件，需要付费许可才能使用。它具有企业级功能，并享有微软的高级支持。它是用于关键任务系统的流行选择之一。

# Oracle

Oracle ([`www.oracle.com/database/index.html`](https://www.oracle.com/database/index.html)) 由于其传统和企业功能而成为最常用的数据库。它是商业软件，需要付费许可才能使用。它具有企业级功能，如分片、复制、高可用性等。

# NoSQL 数据库

这是一个广泛的范畴，包括所有不是关系型数据库管理系统（RDBMS）的数据库。这包括文档存储、宽列存储、搜索引擎、键值存储、图数据库管理系统、内容存储等等。这类数据库的一般特点是它们可以是模式无关的，并且不依赖于关系数据。

# MongoDB

MongoDB ([`www.mongodb.com/`](https://www.mongodb.com/)) 是一个跨平台文档存储，是 NoSQL 数据库中最受欢迎的选择之一。它拥有专有的基于 JSON 的 API 和查询语言。它支持 MapReduce 和企业级功能，如分片、复制等。它是免费和开源软件。

MapReduce 是一种数据处理范式，将一个任务分割成多个并行映射任务，对产生的输出进行排序和归约成结果。这使得处理大型数据集变得高效且更快。

# Cassandra

Apache Cassandra ([`cassandra.apache.org/`](http://cassandra.apache.org/)) 是一个分布式列存储，专注于高可用性、可扩展性和性能。由于其分布式特性，它没有单点故障，因此成为最流行的关键高可用性系统选择。它最初由 Facebook 开发和开源。

你知道吗，Cassandra 每行可以拥有高达 20 亿个列？

# Elasticsearch

Elasticsearch ([`www.elastic.co/products/elasticsearch`](https://www.elastic.co/products/elasticsearch)) 是一个基于 Apache Lucene ([`lucene.apache.org/`](http://lucene.apache.org/)) 的搜索和分析引擎。它在技术上是一个 NoSQL 数据库，但由于其索引能力和高性能，主要用作搜索引擎。它具有分布式和多租户功能，并具备全文搜索能力。它拥有网络界面和 JSON 文档。它是使用最广泛的搜索引擎之一。

# 安装和设置

要开始使用 JHipster，您必须安装 JHipster CLI 工具。JHipster CLI 包含使用平台提供的所有功能所需的命令。

JHipster 在线：如果您想创建一个不需要安装任何东西的应用程序，您可以通过访问 [`start.jhipster.tech`](https://start.jhipster.tech) 来实现。您可以将应用程序授权以直接在您的 GitHub 账户中生成项目，或者您可以将其作为 ZIP 文件下载。

# 先决条件

在我们安装 JHipster CLI 之前，让我们先看看先决条件。我们需要安装一些依赖项，并配置我们最喜欢的 IDE 以最佳方式与生成的代码一起工作。您可以访问 [`www.jhipster.tech/installation/`](http://www.jhipster.tech/installation/) 以获取有关此信息的最新信息。

# 需要的工具

以下是需要安装 JHipster 以及与生成的应用程序一起工作的工具。如果您尚未安装，请按照以下步骤进行安装。

在本节中，您需要使用命令行界面（命令提示符或终端应用程序），因此最好有一个终端已经打开。由于以下工具的安装将更改环境变量，您可能需要在安装一个工具后关闭并重新打开终端：

+   在 Windows 上，使用默认的 **命令提示符** (**CMD**) 或 PowerShell

+   在 Linux 上，使用 Bash 或您喜欢的终端模拟器

+   在 macOS 上，使用 iTerm 或您喜欢的终端应用程序

# 安装过程

让我们看看每个工具的安装过程。

# Java 8

Java 9 是最新版本的 Java，引入了模块、响应式流等功能。虽然 JHipster 应用程序可以与 Java 9 一起工作，但在 Java 9 支持在所有依赖项中稳定之前，建议继续使用更稳定的 Java 8。

生成的应用程序使用 Java 8，因此需要编译应用程序：

1.  通过在终端中运行命令 `java -version` 来检查您的 Java 版本。它应该显示 `java version "1.8.x"`，其中 x 可以是任何补丁版本。

1.  如果您没有安装正确的版本，您可以访问 Oracle 网站 ([`www.oracle.com/technetwork/java/javase/downloads/index.html`](http://www.oracle.com/technetwork/java/javase/downloads/index.html)) 并按照说明安装 Java 8 的 JDK。

1.  安装完成后，再次检查步骤 1 中的命令，以确保无误。由于 JDK 会更改环境变量以设置 `JAVA_HOME`，因此您在这里需要打开一个新的终端。

# Git

Git 是最常用的源代码管理版本控制系统。它促进了分布式修订控制，并且现在是开发的一个基本组成部分。

JHipster 使用 Git 进行应用程序升级，并且也推荐使用 Git 来确保 NodeJS 和 NPM 生态系统的顺畅运行：

1.  通过在终端中运行 `git --version` 来检查 Git。它应该显示 `git version x.x.x`；版本号可以是任何数字。

1.  如果命令未找到，您可以访问 git-scm（[`git-scm.com/downloads`](https://git-scm.com/downloads)）并按照说明在您的操作系统上安装 Git。

1.  安装完成后，再次检查步骤 1 中的命令以确保无误。

# Node.js

Node.js 是一个 JavaScript 运行环境。它彻底改变了 JavaScript 世界，使 JavaScript 成为当今开发者中最受欢迎的开发语言（根据[`insights.stackoverflow.com/survey/2017#technology-programming-languages`](https://insights.stackoverflow.com/survey/2017#technology-programming-languages)）。Node 生态系统是世界上最大的，拥有超过 600,000 个包，并由 NPM（默认包管理器）管理。

JHipster CLI 是一个 NodeJS 应用程序，因此需要 NodeJS 来运行，并且生成应用程序中使用的许多工具也将需要 NodeJS：

1.  在终端中键入`node -v`来检查 NodeJS。它应该显示一个版本号。请确保版本号大于 8.9，并且对应于 NodeJS 的最新 LTS 版本。

1.  如果命令未找到或您有较低版本的 NodeJS，则可以访问 Node.js 网站（[`nodejs.org/en/download/`](https://nodejs.org/en/download/)）并按照说明安装最新可用的 LTS 版本。请注意，非 LTS 版本（当前版本）可能不稳定，建议不要使用它们。

1.  安装完成后，再次检查步骤 1 中的命令以确保无误。由于 NodeJS 会修改环境变量，因此在这里您需要打开一个新的终端。

1.  当您安装 NodeJS 时，NPM 会自动安装。您可以在终端中运行`npm -v`来检查这一点。

您可以通过运行命令`npm -g install bower gulp-cli`或使用 Yarn，`yarn global add bower gulp-cli`来安装多个 NPM 包。

# Yarn

Yarn 是 NodeJS 的包管理器。它与 NPM 的 API 和功能兼容，并提供更好的性能和平坦的包树。

默认情况下，JHipster 使用 Yarn 而不是 NPM，因为 Yarn 在撰写本文时速度更快。如果您希望使用 NPM，则可以跳过此步骤：

1.  您可以访问 Yarn 网站（[`yarnpkg.com/en/docs/install`](https://yarnpkg.com/en/docs/install)）并按照说明安装 Yarn。

1.  安装完成后，通过运行`yarn --version`来检查以确保无误。

# Docker

Docker 是容器管理的默认标准，它使使用容器变得轻而易举。它提供了创建、共享和部署容器的工具。

您将需要 Docker 和`docker-compose`来运行生成的数据库镜像以及微服务开发：

1.  在终端中运行`docker -v`来检查 Docker。它应该显示一个版本号。

1.  在终端中运行`docker-compose -v`来检查`docker-compose`。它应该显示一个版本号。如果您使用的是 Mac 或 Linux，您可以一起运行`docker -v && docker-compose -v`。

1.  如果找不到命令，您可以访问 Docker 网站 ([`docs.docker.com/install/`](https://docs.docker.com/install/)) 并按照说明进行安装。此外，按照说明安装 Docker Compose ([`docs.docker.com/compose/install/`](https://docs.docker.com/compose/install/))。

1.  安装完成后，再次检查步骤 1 中的命令，以确保无误。

可选安装 Java 构建工具：通常 JHipster 会根据您的构建工具选择自动安装 Maven Wrapper ([`github.com/takari/maven-wrapper`](https://github.com/takari/maven-wrapper)) 或 Gradle Wrapper ([`docs.gradle.org/current/userguide/gradle_wrapper.html`](https://docs.gradle.org/current/userguide/gradle_wrapper.html))，如果您不想使用这些包装器，请访问官方 Maven 网站 ([`maven.apache.org/`](http://maven.apache.org/)) 或 Gradle 网站 ([`gradle.org/`](https://gradle.org/)) 进行自己的安装。

# IDE 配置

JHipster 应用程序可以通过使用命令行界面和 JHipster CLI 创建。从技术上讲，IDE 不是必需的，但在继续开发生成的应用程序时，强烈建议您使用适当的 Java IDE，例如 IntelliJ、Eclipse 或 Netbeans。有时您也可以使用带有适当插件的先进文本编辑器，如 Visual Studio Code 或 Atom，以完成工作。根据您选择的 IDE/文本编辑器，建议使用以下插件以提高开发效率：

+   Angular/React：Tslint、TypeScript、编辑器配置

+   Java：Spring、Gradle/Maven、Java 语言支持（VS Code）

无论使用 IDE/文本编辑器，始终排除 `node_modules`、`git`、`build` 和 `target` 文件夹以加快索引速度。一些 IDE 会根据 `.gitignore` 文件自动执行此操作。

在您喜欢的浏览器中访问 [`www.jhipster.tech/configuring-ide/`](http://www.jhipster.tech/configuring-ide/) 了解更多信息。

# 系统设置

在安装和深入研究 JHipster 之前，这里有一些提示，以帮助您准备可能遇到的一些常见问题：

+   当在 macOS 或 Linux 上使用 Yarn 时，您需要在路径中包含 `$HOME/.config/yarn/global/node_modules/.bin`。这通常在您安装 Yarn 时自动完成，如果没有，您可以在终端中运行命令 ```export PATH="$PATH:`yarn global bin`:$HOME/.config/yarn/global/node_modules/.bin"` 来完成此操作。

+   如果您在公司代理后面，您将需要绕过它以使 NPM、Bower 和 Maven/Gradle 正常工作。请访问 [`www.jhipster.tech/configuring-a-corporate-proxy/`](http://www.jhipster.tech/configuring-a-corporate-proxy/) 了解可以为不同工具设置哪些代理选项。

如果你使用的是 Mac 或 Linux，并且如果你使用的是 Oh-My-Zsh 或 Fisherman shell，那么你可以使用 JHipster 为此提供的特定插件。访问 [`www.jhipster.tech/shell-plugins/`](http://www.jhipster.tech/shell-plugins/) 获取详细信息。

# JHipster 安装

好的，现在让我们真正开始吧。JHipster 可以通过本地安装使用 NPM 或 Yarn，通过团队提供的 Vagrant 镜像，或者使用 Docker 镜像。或者，还有我们之前看到的 JHipster 在线应用程序。

在所有选项中，要充分利用 JHipster 的全部功能，最佳方式是通过使用 Yarn 或 NPM 安装 JHipster CLI。打开一个终端并运行：

```js
> yarn add global generator-jhipster
```

如果你更喜欢使用 NPM，那么只需运行：

```js
> npm install -g generator-jhipster
```

等待安装完成，然后在终端运行 `jhipster --version`。你应该会看到如下所示的版本信息：

![图片](img/ed5d567c-fccc-4564-b9d8-c115205405d6.png)

就这些了；我们已经准备好开始了。

如果你是一个迫不及待想要新版本的人，你可以在安装 JHipster CLI 后按照以下步骤使用当前的开发代码：

1.  在终端中，导航到你想要使用的目录。例如，如果你在主目录中有一个名为 `project` 的文件夹，运行 `cd ~/projects/`，对于 Windows 运行 `cd c:\Users\<username>\Desktop\projects`。

1.  运行 `git clone https://github.com/jhipster/generator-jhipster.git`

1.  现在，通过运行 `cd generator-jhipster` 导航到该文件夹。

1.  运行 `npm link` 以在此文件夹中创建到全局安装的应用程序在 `global node_modules` 中的符号链接。

1.  现在当你运行 JHipster 命令时，你将使用你克隆的版本，而不是你安装的版本

请注意，你应该只在绝对确定自己在做什么的情况下才这样做。另外请注意，软件的开发版本将始终是不稳定的，可能包含错误。

如果你更喜欢在虚拟环境中隔离安装，那么你可以使用 JHipster 团队提供的 Vagrant 开发箱或 Docker 镜像。访问 [`github.com/jhipster/jhipster-devbox`](https://github.com/jhipster/jhipster-devbox) 获取使用 Vagrant 箱的说明，或者访问 [`www.jhipster.tech/installation`](http://www.jhipster.tech/installation) 并向下滚动到 Docker 安装（仅适用于高级用户）部分，获取使用 Docker 镜像的说明。

# 摘要

在本章中，我们发现了 JHipster 以及它提供的不同技术选项。我们对客户端和服务器端栈的重要组件进行了简要介绍。我们对 Spring 技术、Angular、Bootstrap 等进行了快速概述。我们还对 JHipster 支持的不同数据库选项进行了概述。我们了解了与 JHipster 一起工作所需的工具，并且我们已经成功设置了与 JHipster 一起工作的环境，并安装了 JHipster CLI。在下一章中，我们将看到如何使用 JHipster 来构建一个生产级别的单体 Web 应用程序。
