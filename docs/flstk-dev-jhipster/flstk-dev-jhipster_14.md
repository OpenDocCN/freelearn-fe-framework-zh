# 第十四章：JHipster 的最佳实践

在本书的前几章中，我们详细学习了 JHipster 以及它所支持的多种工具和技术。以下是到目前为止我们所学到的东西：

+   我们学习了如何开发单体和微服务应用程序。我们还了解了架构上的差异以及选择其中一个而不是另一个的原因。

+   我们使用 JDL 创建了实体，并根据我们的业务需求对生成的应用程序进行了定制。

+   我们使用 Jenkins 创建了一个 CI-CD 设置。

+   我们将单体应用程序部署到了 Heroku 云平台

+   我们使用 Kubernetes 和 Docker 将微服务架构部署到了 Google 云平台。

+   我们学习了 Spring 框架、Spring Boot、Angular、React、Docker 等等。

在本章中，我们将看到下一步要采取的步骤，以及如何使用本书中学到的知识，我们还将讨论一些来自 JHipster 社区的最佳实践、技巧、窍门和建议。作为 JHipster 的核心贡献者，我们还将提供一些我们在本章中学到的见解和经验教训。以下是一些我们将涉及的主题：

+   接下来的步骤

+   需要牢记的最佳实践

+   使用 JHipster 模块

# 接下来的步骤

JHipster 支持许多技术，了解所有这些技术需要大量的时间和精力；这不可能在一本书中完成。每个支持的技术都需要一本单独的书来学习和掌握。如果你已经熟悉了网络开发的核心概念，那么你现在应该已经相当清楚地了解 JHipster 应用程序是如何工作的了。我们希望这本书为你提供了技术和 JHipster 本身的良好介绍。但仅仅这样是不够的；你需要继续学习才能成为大师。以下是一些你可以追求的任务，以进一步提高你在使用 JHipster 进行网络开发方面的技能。但在那之前，我们建议你更多地了解 Spring 框架和 Angular/React 生态系统，以补充你在本书中学到的内容。

# 为应用程序添加购物车

在第五章*定制和进一步开发*中，我们看到了生成的应用程序如何被定制以使其看起来和表现得像一个电子商务网站。正如那里所提到的，仅仅使应用程序真正可用是不够的。以下是一些你可以尝试实现的功能，以使应用程序更加功能完善：

1.  在客户端添加一个简单的购物车功能：

    1.  创建一个`ProductOrder`对象来保存`OrderItems`。`ProductOrder`与客户相关联，因此使用当前登录用户的详细信息将其标记为客户。

    1.  在列表中的产品项上添加一个“添加到购物车”按钮。点击按钮时，为产品创建一个新的 `OrderItem` 并将其添加到 `ProductOrder` 的 `OrderItems` 数组中。如果同一产品被点击多次，则增加现有 `OrderItem` 的数量属性。添加一个购物车对话框以列出添加到 `ProductOrder` 的所有 `OrderItems`。它可以使用与产品类似的列表 UI，或者一个简单的表格来显示产品、总价和数量。

    1.  在产品列表页面上添加一个“查看购物车”按钮以查看购物车对话框。

1.  添加“现在下单”功能：

    1.  在产品列表页面上添加一个“现在下单”按钮。

    1.  点击按钮时，将 `ProductOrder` 发送到 REST API 以创建一个新的 `ProductOrder`，使用 `product-order.service.ts` 进行此操作。

    1.  在后端，修改 `ProductOrderService.java` 的保存方法以创建 `ProductOrder` 的发票和装运，并将它们全部保存。

    1.  假设我们接受货到付款，所以现在让我们跳过与支付网关的集成。

1.  向客户发送订单确认：

    1.  JHipster 默认提供邮件配置和模板。您可以在 `src/main/resources/config/application-*.yml` 中配置自己的 SMTP 服务器详情。有关如何配置流行 SMTP 服务的说明，请参阅 [`www.jhipster.tech/tips/011_tip_configuring_email_in_jhipster.html`](http://www.jhipster.tech/tips/011_tip_configuring_email_in_jhipster.html)。

    1.  在 `src/main/resources/mails` 中创建一个新的订单确认电子邮件模板。在电子邮件中提供产品详情、总价和数量。

    1.  使用 `MailService.java` 中提供的 `sendEmailFromTemplate` 方法在成功创建发票时发送电子邮件。

1.  在注册新用户时创建客户档案：

    1.  在注册页面上添加字段，并从详情中自动为每个用户创建客户实体。

尝试将更改应用到微服务应用程序中。

# 改进端到端测试

在 第六章，“测试和持续集成”中，我们看到了由于为具有必需关系的实体生成测试的难度，一些端到端测试被注释掉了。尝试使用以下方法修复测试：

1.  添加一个在创建后删除实体的方法，类似于我们在 第六章，“测试和持续集成”中看到的客户实体规范。

1.  在 `src/test/javascript/e2e/entities` 下的文件中取消注释已注释的端到端测试。

1.  使用 Protractor 导航到相关实体页面并创建一个新条目。如果相关实体有必需的关系，则采用相同的方法并嵌套它们，直到所有必需的实体都到位。这也可以在测试的 `beforeAll` 方法中完成。

1.  现在回到测试的实体，看看测试是否正常工作。

1.  一旦测试完成，请在测试的`afterAll`方法中删除创建的实体。

1.  探索是否可以自动化在实体页面对象上创建实体项，并在需要时使用它。

# 改进 CI/CD 管道

在第六章“测试和持续集成”中，当我们使用 CI/CD 子生成器创建`Jenkinsfile`时，我们注释掉了部署阶段。重新启用它，并在你进行新提交时检查应用程序是否已部署到 Heroku：

+   看看你是否可以向管道中添加端到端测试。

+   如果你的应用程序在 GitHub 上，尝试使用 ci-cd 子生成器将 Travis 添加到项目中。

# 构建 JHipster 模块

JHipster 有两种机制来扩展其功能：

+   一个模块系统，允许用户构建自己的 Yeoman 生成器([`www.jhipster.tech/modules/creating-a-module/`](http://www.jhipster.tech/modules/creating-a-module/))来补充 JHipster

+   JHipster 5 引入的新蓝图机制，用于自定义 JHipster 生成的代码所需的部分

**模块**和**蓝图**之间的区别在于，蓝图允许你覆盖生成应用程序的某些部分，而 JHipster 则构建剩余的部分。例如，蓝图可以单独覆盖客户端代码，而服务器端则由 JHipster 生成。另一方面，模块只能更改由 JHipster 生成的部分，因此更适合在 JHipster 创建的部分之上添加补充功能。

尝试构建一个模块来向你的应用程序添加一个简单的页面。

你可以使用 JHipster 模块生成器([`github.com/jhipster/generator-jhipster-module`](https://github.com/jhipster/generator-jhipster-module))来构建一个新的模块。

# 需要牢记的最佳实践

几年来，JHipster 社区已经识别并采纳了它支持的技术和工具以及一般技术社区中的许多最佳实践。虽然 JHipster 已经在其创建的代码中尝试遵循这些最佳实践，但以下是一些最佳实践、技巧和窍门，作为用户你应该遵循。

# 选择客户端框架

当使用 JHipster 时，你可以选择 Angular 或 React 作为客户端框架。不要仅仅因为其炒作而选择，要根据你的需求、团队构成和熟悉程度来选择：

+   如果你来自重量级的 Java/Spring 背景，那么 Angular 将更容易遵循和操作

+   如果你的应用程序需要重型状态管理和共享状态，那么 React 将更适合

+   如果你计划为你的应用程序构建原生移动客户端，那么更成熟的 React 是这个空间的好选择，React Native 允许你在 Web 应用程序和移动应用程序之间重用大量代码

+   如果你的应用程序高度依赖于由设计团队或第三方生成的 HTML 页面，那么与 React 相比，Angular 将更容易集成。

如果你需要很多不属于标准 Bootstrap 的组件，那么请使用现有的组件库，例如 PrimeNG 或 VMware Clarity，而不是从不同来源组装组件。然而，如果你只需要在 Bootstrap 之上添加几个组件，那么请坚持使用 Bootstrap，并为 Angular 或 React 使用兼容 Bootstrap 的组件。

无论你选择什么，都要遵循该项目的社区提供的指南和最佳实践。

# 选择数据库选项

JHipster 支持许多类型的数据库，从 SQL 到 NoSQL。以下是在选择数据库时的一些考虑因素：

+   对于大多数情况，SQL 数据库就足够了，因此如果你没有理由选择其他 NoSQL 解决方案，请坚持使用 SQL，并从 MySQL、Postgres、Oracle、MariaDB 和 MS SQL 中选择：

    +   如果你在一个拥有 Oracle 或 MS SQL 订阅的企业中，那么选择它们是有意义的，因为你将受益于提供的支持和企业功能。

    +   如果你需要存储和查询大量的 JSON 数据，那么 Postgres 提供了最佳的 JSON 支持，并具有全文搜索功能。

    +   对于大多数简单用例，MySQL 或 MariaDB 就足够了。

    +   在与 SQL 数据库一起工作时，始终选择二级 Hibernate 缓存。

    +   在选择 SQL 开发数据库时：

        +   如果你想要一个简单的开发设置并希望数据持久化，请选择 H2 文件数据库。

        +   如果你想要更快的重启速度，并且你的持久数据不需要时不时地被清除，请选择与生产数据库相同的数据库。如果你正在使用提供的 Docker 镜像，那么清除数据将不会成为问题。

        +   如果你在开发过程中不需要任何持久数据，并且希望在每次重启时都有一个干净的状态，请选择 H2 内存数据库。

+   如果你的用例需要大量的重数据读写，并且数据不是非常关系型，那么 Cassandra 将是一个完美的选择，因为它可以分布式工作，并且能够在极端高负载下运行。

+   对于一个正常、非关系型数据结构，MongoDB 可能就足够了。如果需要，你也可以使用 Postgres 作为 NoSQL JSON 存储。

+   如果你需要 NoSQL 的企业级支持，CouchBase 是一个不错的选择。

+   使用 Elasticsearch 与主数据库一起进行全文搜索。如果你只需要简单的过滤，请使用提供的 JPA 过滤选项。请参阅：[`www.jhipster.tech/entities-filtering/`](http://www.jhipster.tech/entities-filtering/)。

# 架构考虑

我们已经在第一章“现代 Web 应用开发简介”中讨论了选择微服务或单体架构。以下是关于架构的一些更多要点：

+   如果你是一个小团队，不要使用微服务架构。微服务更多的是关于团队扩展。通常，分解单体架构比从微服务开始更容易。

+   如果你认为你可能需要在将来重构为微服务，请在单体中使用异步消息传递。JHipster 提供了对 Apache Kafka 的支持，这是一个异步消息传递的良好解决方案。

异步消息传递是构建无状态系统的最佳方式。在微服务架构中非常重要，因为你可能经常希望通信是无状态和非阻塞的。一些流行的解决方案包括 Apache Kafka ([`kafka.apache.org/`](http://kafka.apache.org/))、RabbitMQ ([`www.rabbitmq.com/`](https://www.rabbitmq.com/))和 gRPC ([`grpc.io`](https://grpc.io))。ReactiveX ([`reactivex.io/`](http://reactivex.io/))和 Spring Reactor ([`projectreactor.io/`](http://projectreactor.io/))是处理异步系统的流行抽象。异步消息传递也使得系统松散耦合。

+   如果你打算向第三方公开 API，请先进行*API first*开发。我们现在有很好的工作流程使用 Swagger Codegen 来完成它。有关更多信息，请参阅[`www.jhipster.tech/doing-api-first-development/`](http://www.jhipster.tech/doing-api-first-development/)。

+   在使用 REST 进行微服务之间的通信时，不要将接口代码放在共享包中；这将使 API 与其客户端紧密耦合，从而导致分布式单体。

+   使用 JHipster，可以分割客户端和服务器。请参阅[`www.jhipster.tech/separating-front-end-and-api/`](http://www.jhipster.tech/separating-front-end-and-api/)。然而，在分离它们之前要三思，因为这将要求你打开 CORS，这会使安全性更加脆弱，并且这种架构有其自身的问题。所以只有在你有充分的理由这样做时才这样做。

+   在服务层使用 DTOs，这样你就可以聚合实体并定义更好的 API，而无需向客户端暴露实体。你将需要为你的实体启用服务层，以便使用 JHipster 来实现这一点。

+   在开始开发之前，了解你应用程序的技术栈。

+   熟悉提供的工具集，例如构建工具（Maven/Gradle/Webpack）、BrowserSync 等。

# 安全性考虑

安全性是任何应用程序最重要的方面之一，在选择安全机制时应考虑以下因素：

+   对于大多数用例，JWT 认证将足够，所以如果你不确定，就坚持使用它。

+   如果你想在应用程序中实现单点登录，请使用 OAuth 2.0 / OIDC，而不是尝试让 JWT 或会话认证作为单点登录解决方案工作

+   如果你已经在公司中设置了 Keycloak 或 Okta，请选择 OAuth 2.0/OIDC 并将它们连接起来。

+   只有在你想要有状态认证的情况下，才选择基于会话的认证。

# 部署和维护

这里有很多好的实践；其中一些重要的包括：

+   Docker 是微服务集成测试的必备工具，但使用 Docker 进入生产并不容易，所以使用如 Kubernetes 之类的编排工具。

+   在应用程序生成后立即运行 prod 构建，并在你的应用程序仍然非常简单时立即部署到 prod。这将有助于缓解任何部署问题，因为你可以确信应用程序可以正常工作。

+   当涉及到客户端时，prod 构建与 dev 构建有很大不同，因为资源被压缩和优化。在添加任何前端代码时，库总是验证 prod 构建。

+   总是使用 prod 配置运行端到端 protractor 测试。

+   拥抱嵌入式 servlet 引擎，并忘记部署到 JEE 服务器，如 WebLogic、WebSphere、JBoss 等。产生的工件是可执行的，并包含一个嵌入的 Undertow 服务器。

你知道 Java EE 正在更名为 Jakarta EE 吗？有关更多信息，请参阅[`www.infoq.com/news/2018/03/java-ee-becomes-jakarta-ee`](https://www.infoq.com/news/2018/03/java-ee-becomes-jakarta-ee)。

+   经常使用 JHipster 升级子生成器进行升级。这将确保你使用的工具和技术都是最新和安全的。

+   从`application-prod.yml`中删除所有秘密，并使用占位符从命令行或环境变量中注入值。永远不要在代码或配置文件中放置任何秘密或密码。

# 一般最佳实践

通常，以下是一些你应该考虑的最佳实践：

+   如果你开始使用实体子生成器创建实体，那么一旦实体数量超过几个，就使用`export-jdl`并切换到 JDL。

+   首先不添加任何模块生成应用程序，只在需要时添加所需的模块。

+   在添加模块之前仔细评估模块。确保它支持你选择的堆栈。

+   遵循每个底层技术的**最佳实践**。Angular 最佳实践、Spring 最佳实践等。只有有充分的理由才改变它们。

+   在客户端和服务器端使用提供的库版本。让它们全部一起工作是一项艰巨的任务，所以坚持使用它们。当 JHipster 更新它们或你真的需要修复一个错误或安全问题时才更新它们。

+   遵循 JHipster 提供的流程。它们在这里是为了帮助你。通常，按照推荐的方式使用它们有非常好的理由。在寻求外部帮助之前，请先阅读 JHipster 文档。

+   你有一个非常好的默认工作环境；不要破坏它。

    +   使用实时重载，前端和后端更新是自动和快速的。充分利用它们。

    +   使用提供的子生成器进行生产部署很容易。

+   使用为你部署到的云平台提供的子生成器。

+   Git 是你的朋友。每次添加模块或实体，或使用子生成器时，都要提交。任何错误（包括数据库中的错误）都应该能够通过 Git 轻松回滚。

# 使用 JHipster 模块

JHipster 模块和蓝图是向生成的代码添加更多功能和功能的好方法。在撰写本文时，JHipster 市场上有许多模块（55 个）可供选择（[`www.jhipster.tech/modules/marketplace`](http://www.jhipster.tech/modules/marketplace)），您还可以根据自己的需求构建自己的模块。以下是一些值得注意的模块：

+   **Ignite JHipster**：这为 JHipster 应用程序提供了 React Native 的样板代码。使用 JHipster 作为后端来启动您的 React Native 应用程序是一个理想的方式。

+   **实体审计**：此模块启用实体审计。它使用 Hibernate 审计钩子为实体 CRUD 操作创建自定义审计。它还提供 Javers 作为审计机制，而不是自定义 Hibernate 审计。它还提供了一个漂亮的 UI，可以在 Angular 应用程序中查看审计。它将为新实体以及现有实体启用审计。

+   **Ionic**：这为 JHipster 应用程序提供了一个 Ionic 客户端。如果您想使用 JHipster 后端和 Angular 前端创建移动应用程序，这是一个理想的选择。

+   **Swagger CLI**：此模块为生成 JHipster 应用的 Swagger 客户端提供支持。

+   **gRPC**：此模块为 JHipster 应用程序生成 gRPC 反应式端点。它也支持实体，如果您想要一个非阻塞的反应式 API 用于您的 JHipster 应用程序，这是一个理想的选择。

+   **VueJS**：此模块为 JHipster 应用程序提供 VueJS 支持。它创建最小的样板代码以启动使用 VueJS 的 JHipster 应用的客户端开发。

查看以下步骤：

1.  要使用 JHipster 模块，首先使用 `npm i -g generator-<module-name>` 或 `yarn add global generator-<module-name>` 安装它。

1.  安装完成后，进入 JHipster 应用程序目录，并执行 `yo <module-name>` 以启动模块并按照提示操作。

# 贡献 JHipster

学习 JHipster 及其支持的技术的一个最好的方法是通过直接为 JHipster 做贡献。有关为开发设置 JHipster 的详细信息，请参阅贡献指南（[`github.com/jhipster/generator-jhipster/blob/master/CONTRIBUTING.md`](https://github.com/jhipster/generator-jhipster/blob/master/CONTRIBUTING.md)）。

您可以通过多种方式为项目做出贡献；以下是一些方法：

+   如果您发现了一个错误，请在 GitHub 项目中提交一个 issue（[`github.com/jhipster/generator-jhipster`](https://github.com/jhipster/generator-jhipster)），遵循 issue 模板中的指南，运行 `jhipster info`，并提供复现步骤。您也可以尝试自己修复问题，并在成功后提交一个 PR。

+   处理开放问题和功能请求。这样，您将学习 JHipster 及其使用的技术。

+   在 Stack Overflow 上回答与 JHipster 相关的问题 ([`stackoverflow.com/questions/tagged/jhipster`](https://stackoverflow.com/questions/tagged/jhipster))。

# 摘要

我们一起通过 JHipster 和全栈开发之旅已经结束。在本章中，我们学习了 JHipster 社区识别出的许多最佳实践。尝试完成“下一步行动”部分中的作业，因为它将帮助你应用所学知识，并帮助你更好地理解这些概念。

我们希望你的学习体验非常棒，并希望你在本书中学到的关于 JHipster 的知识能帮助你完成下一个项目。

关注 Twitter 上的 `@java_hipster`，这样你就可以看到新版本发布和安全性漏洞披露的情况。

如果你对 JHipster 有任何疑问或问题，请将你的问题发布到 Stack Overflow ([`stackoverflow.com/questions/tagged/jhipster`](https://stackoverflow.com/questions/tagged/jhipster)) 并添加 `jhipster` 标签。团队将会收到通知，并乐意提供帮助！
