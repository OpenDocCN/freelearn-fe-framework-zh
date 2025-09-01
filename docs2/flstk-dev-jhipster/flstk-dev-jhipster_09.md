# 使用 JHipster 构建微服务

现在，是时候构建一个完整的微服务堆栈了。到目前为止，我们已经使用 JHipster 生成了、开发了和部署了一个单体应用程序，在前一章中，我们看到了微服务堆栈提供的优势。在这一章中，我们将探讨如何使用 JHipster 构建微服务。

我们将首先将我们的单体商店应用程序转换为微服务网关应用程序。接下来，我们将作为独立的微服务应用程序向我们的电子商务商店添加新的功能。然后我们将看到这些应用程序如何相互通信，并作为一个单一的应用程序为我们的最终用户提供服务。

在这一章中，我们将：

+   生成网关应用程序：

    +   检查生成的代码

    +   简要介绍 JWT

+   生成微服务应用程序：

    +   发票服务

    +   通知服务

# 应用程序架构

我们在第三章中使用了 JHipster 构建了一个在线电子商务商店，标题为*使用 JHipster 构建单体 Web 应用程序*。第三章。由于范围较小，这是一个更容易的选择。假设我们的电子商务商店在用户和范围方面增长巨大，导致了一种更加苛刻的情况。团队发现很难以单体架构快速推出功能，并希望对应用程序的各个部分有更多的控制。

解决这个问题的方法之一是采用微服务架构。该应用程序是使用 JHipster 创建的；迁移到微服务的选项更容易实现。JHipster 遵循**代理微服务模式**，其中在服务前面有一个聚合器/代理，它作为最终用户的网关。用更简单的话说，JHipster 创建了一个网关（处理所有用户请求）和通过网关与用户通信的各个服务。

也就是说，我们需要有一个网关服务，以及一个或几个可以独立运行的微服务应用程序。

我们的客户在发票方面遇到了一些问题，因为系统响应时间变长了。客户还抱怨他们没有收到通知，无法跟踪他们的订单。为了解决这个问题，我们将从我们的单体应用程序中移除发票服务，并使其成为一个独立的服务，然后创建一个独立的**通知服务**来处理通知。对于前者，我们将继续使用相同的 SQL 数据库。对于后者，我们将使用 NoSQL 数据库。

让我们看看我们将要生成的应用程序架构：

![图片](img/6a4c466f-edc1-453a-9658-6cc03e7fe770.png)

# 网关应用程序生成

我们将首先将我们生成的单体应用程序转换为微服务网关应用程序。

尽管微服务由内部的不同服务组成，但对于最终用户来说，它应该是一个单一、统一的产品。有许多服务被设计成以多种不同的方式工作，但应该有一个单一的入口点供用户使用。因此，我们需要一个网关应用程序，因为它们构成了应用程序的前端。

将内部合约和服务与外部用户分离。我们可能有一些应用级别的内部服务，我们不应该将其暴露给外部用户，因此这些可以被隐藏起来。这也为应用程序增加了另一个安全层。

更容易模拟服务进行测试，有助于在集成测试中独立验证服务。

# 将单体应用转换为微服务网关

我们已经生成了我们的单体应用程序以及我们的实体。作为单体应用程序生成的一部分，我们已经通过 JHipster CLI 选择了一些选项。当我们生成微服务网关应用程序时，我们将坚持相同的选项（数据库、认证类型、包名、i18n 等）。

注意：我们将在稍后看到如何在单体应用中应用我们应用的定制化。

现在是编码时间了，让我们使用 JHipster CLI 开始构建网关应用程序。

这里的第一步是将单体应用转换为具有几乎相同配置的微服务网关应用，就像我们创建单体应用时使用的配置一样。

现在，让我们转到终端（如果使用 Windows，则为命令提示符），首先导航到我们创建单体应用的文件夹。一旦进入文件夹，创建一个新的 Git 分支，这样我们就可以在完成后干净地合并回 master 分支：

```js
> cd e-commerce-app/online-store
> git checkout -b gateway-conversion
```

现在，打开您最喜欢的文本编辑器或 IDE 中的`.yo-rc.json`文件，并更改以下值：

![图片](img/c58dc656-48af-47ab-8020-75c30440f768.png)

为了将单体应用转换为微服务网关应用，我们只需更改`.yo-rc.json`文件中的前述值。由于对于单体应用来说，服务发现不是强制性的，我们已经将服务发现类型添加到 Eureka 中。

显然，下一个更改是将应用程序类型从单体更改为网关。

# 应用程序生成

现在，让我们运行`jhipster`命令来生成应用程序：

![图片](img/e157b3a8-ae89-494b-bae6-9ab95f4d9f7a.png)

JHipster 会询问您是否想要覆盖冲突的文件或使用您现有的文件，以及一些其他选项。用户可以使用任何想要的选项。

目前，我们将选择选项`a`。它将覆盖所有其他文件，包括高亮显示的文件。

如果您在应用程序上编写了大量自定义代码，此提示将非常有用。您可以选择适当的选项以获得所需的结果。

![图片](img/7a386280-9640-4fd0-84c8-8487b2d4bb17.png)

这将覆盖我们在单体应用程序中做的所有自定义设置。我们可以通过使用 GIT 从我们的 master 分支中 cherry pick 所需的更改，轻松地将它们带回这个分支。你可以遵循我们在第五章 Customization and Further Development 中看到的类似方法。一旦所有更改都应用了，我们就可以将这个分支合并回 master。你还需要在 第十章 *Working with Microservices* 中对实体文件做同样的操作。

# 生成新的网关

如果你不想转换现有的单体应用程序并希望从头开始，请遵循以下步骤。

在终端中，导航到 `e-commerce-app` 文件夹，创建一个名为 `app-gateway` 的新文件夹，然后切换到 `app-gateway` 目录，并运行 `jhipster` 命令。

因此，显然，第一个问题是，我们想创建哪种 *类型* 的应用程序？我们将选择微服务网关（第三个选项），然后按 *Enter*：

![图片](img/bfa5cc5a-e486-4c5d-b189-0ce4693d240c.png)

然后，我们将输入我们应用程序的基本名称。我们将使用名称 `store`：

![图片](img/1ffbf974-3a76-47c8-b4b4-a9bb4122d43b.png)

由于我们正在使用微服务，存在很高的端口冲突风险。为了避免这些冲突，JHipster 将要求你为每个微服务应用程序（包括网关和应用程序）选择一个端口。默认情况下，我们将使用 `8080` 作为端口，但我们可以根据需要更改端口。现在，我们将使用默认端口，因为网关将在 `8080` 上运行，类似于我们的单体应用程序：

![图片](img/7d85a3d1-a89a-4e9b-8f5f-ced6207d3cc6.png)

然后，我们输入我们应用程序的包名。我们将使用可用的默认名称，即 `com.mycompany.store`：

![图片](img/d47aabf3-4b7b-48b1-bed7-e98a36b075b1.png)

对于下一个问题，JHipster 将要求你配置注册服务。我们将选择要配置、监控和扩展我们的微服务和网关应用程序所需的注册服务。我们可以选择使用 JHipster 注册或 Consul。这也是可选的；我们在这里不需要选择任何注册服务。然后我们可以选择无服务发现。

当你选择无服务发现时，微服务 URL 将硬编码在属性文件中。

![图片](img/804d5adf-7774-4922-bdcf-2509505072f5.png)

对于下一个问题，JHipster 将要求你选择认证类型。JHipster 提供了三种认证类型的选项，分别是 JWT、OAuth2 和基于 UAA 服务器的。JWT 是无状态的，而 UAA 在不同的服务器（和应用程序）上运行。另一方面，OAuth2 将提供授权令牌，而授权是在第三方系统上完成的。

JHipster 提供了一个创建 UAA 服务器应用程序的选项。

我们将在稍后更详细地了解 JWT。现在，我们将选择 JWT 认证。

![图片](img/443b3149-1253-4a02-aa27-54bb51cbb2b2.png)

我们将选择数据库类型。我们有选项选择 SQL 和 NoSQL。在 NoSQL 方面，我们可以选择 MongoDB 或 Cassandra。我们将选择 SQL 数据库：

![图片](img/84c34984-58bb-4d75-a980-1dda176da298.png)

然后，我们将选择我们将用于生产和开发的数据库。JHipster 提供了一个选项，可以在生产和开发环境中使用不同的数据库。这确实有助于更快、更轻松地启动应用程序开发。

我们将选择 MySQL 数据库用于生产：

![图片](img/aeb5da7b-b329-4619-aac0-b210a80dcff4.png)

然后，我们将选择基于磁盘持久性的 H2 用于开发：

![图片](img/e7d52a14-8a6e-4e02-9308-16911416e47d.png)

在选择数据库之后，我们将为第二级 Hibernate 缓存选择“是”：

![图片](img/2b6f1490-0960-4328-ba4d-38618a10e329.png)

然后，我们将选择 Gradle 用于构建后端。我们有选项选择 Gradle 用于后端开发：

![图片](img/a48dce56-58d2-4880-b47e-8d93dc072e52.png)

然后，我们可以选择我们需要的任何其他附加技术。JHipster 提供了一个选项来选择 Elasticsearch、使用 Hazelcast 进行集群应用程序、WebSocket 和 Swagger Codegen 用于基于 API 的开发以及基于 Kafka 的异步消息传递。我们将在此选择 WebSocket，类似于我们在单体存储中使用的：

![图片](img/d69c4e3c-e728-47eb-a145-37edc6a2c490.png)

由于我们的网关应用程序需要一个用户界面，对于下一个问题，我们可以选择我们需要的客户端框架。我们将选择`Angular 5`：

![图片](img/419cca66-7b4e-449d-9c6b-76a92e9b7be8.png)

然后，我们将选择是否需要使用基于 SASS 的预处理器用于 CSS。我们将在此使用 SASS，因此我们将选择 y：

![图片](img/352fdcac-4156-4c4c-a5ae-71e47f12cd1d.png)

然后，我们将选择是否需要启用国际化支持。我们将为此选择“是”：

![图片](img/0fc1836a-ec40-47ce-a0fd-bd32b0218efc.png)

然后，我们将选择英语作为我们的母语：

![图片](img/c5173b16-8f5f-4751-a6e1-6eee2d2e5b3a.png)

然后，选择任何其他附加语言：

![图片](img/166821e5-0441-48ac-99fa-6571be79b2f2.png)

然后，选择任何其他测试框架，例如 Gatling、Cucumber 和/或 Protractor，因为这是必需的。我们将选择 Protractor 作为测试工具：

![图片](img/9732311a-69d6-4d73-961d-51b0660747fc.png)

最后，JHipster 要求我们安装来自市场的任何其他生成器；我们将在此选择“否”：

![图片](img/d93c0cd7-4a57-4c2b-b0a4-69e9472ef74e.png)

这将创建所有必要的文件并使用 Yarn 安装前端依赖项：

![图片](img/6135ec47-8d4a-4998-b5d6-4ae12bb2c9b0.png)

现在，我们的网关应用程序已生成。JHipster 将自动将生成的文件提交到 Git；如果您希望手动执行此步骤，可以在执行期间传递 `skip-git` 标志，例如，`jhipster --skip-git`，然后手动执行以下步骤：

```js
> git init
> git add --all
> git commit -am "converted into gateway application"
```

# 网关配置

网关应用程序的生成方式与单体应用程序类似，但涉及 Zuul 代理、Eureka 客户端和 Hystrix 的配置：

```js
@ComponentScan
@EnableAutoConfiguration(exclude = {MetricFilterAutoConfiguration.class, MetricRepositoryAutoConfiguration.class, MetricsDropwizardAutoConfiguration.class})
@EnableConfigurationProperties({LiquibaseProperties.class, ApplicationProperties.class})
@EnableDiscoveryClient
@EnableZuulProxy
public class GatewayApp {
...
}
```

我们为我们的注册表服务选择了 JHipster 注册表。这将是一个独立的注册服务器，其他微服务应用程序和网关将自动注册自己：

+   `@EnableDiscoveryClient` 添加到 Spring Boot 的主类中，这将启用 Netflix Discovery 客户端。微服务应用程序和网关需要将自己注册到注册表服务。它使用 Spring Cloud 的发现客户端抽象来查询其自己的主机和端口，然后将它们添加到注册服务器。

+   另一方面，Zuul 是门卫。这有助于将授权请求路由到相应的端点，限制每个路由的请求，并将必要的令牌中继到微服务应用程序。

+   `@EnableZuulProxy` 帮助微服务网关应用程序根据 `application.yml` 中提供的配置将请求路由到相应的微服务应用程序：

```js
zuul: # those values must be configured depending on the application specific needs
    host:
        max-total-connections: 1000
        max-per-route-connections: 100
    semaphore:
        max-semaphores: 500
```

在网关应用程序中，我们已经指定了上述 Zuul 配置设置。一个代理可以保持打开的最大总连接数保持在 `1000`。一个代理可以保持打开的最大路由连接数保持在 `100`。信号量保持在最大 `500`。 （信号量类似于一个用于线程和进程之间同步的计数器。）

后端微服务端点的访问由 `AccessControlFilter` 控制，该过滤器将检查请求是否已授权，并允许请求端点：

```js
public class AccessControlFilter extends ZuulFilter {
     ...
     public boolean shouldFilter() {
       ...
       return !isAuthorizedRequests(serviceUrl, serviceName, 
       requestUri);
     }
    ...
}
```

Zuul 作为门卫，还充当速率限制器。在生成的应用程序中添加了一个速率限制过滤器，该过滤器限制了每个客户端发出的 HTTP 调用次数。这可以通过以下条件启用：

```js
@ConditionalOnProperty("jhipster.gateway.rate-limiting.enabled")
public static class RateLimitingConfiguration { 
... 
}
```

`SwaggerBasePathRewritingFilter` 也被使用，这将有助于重写微服务 Swagger URL 基础路径：

```js
@Component
public class SwaggerBasePathRewritingFilter extends SendResponseFilter {
    @Override
    public Object run() {
        RequestContext ctx = RequestContext.getCurrentContext();
        if(!context.getResponseGzipped()) {
            context.getResponse().setCharacterEncoding("UTF-8");
        }
        // rewrite the base path and send down the response
    }
...
```

添加了一个 `TokenRelayFilter` 以从 Zuul 的忽略列表中移除授权。这将有助于传播生成的授权令牌：

```js
@Component
public class TokenRelayFilter extends ZuulFilter {
    @Override
    public Object run() {
        RequestContext ctx = RequestContext.getCurrentContext();
        Set<String> headers = (Set<String>) ctx.get("ignoredHeaders");
        // JWT tokens should be relayed to the resource servers
        headers.remove("authorization");
        return null;
    }
...
```

每个应用程序都应该有一个 Eureka 客户端，该客户端帮助在服务之间进行请求负载均衡，并将健康信息发送到 Eureka 服务器或注册表。Eureka 客户端在 `application-dev.yml` 中配置如下：

```js
eureka:
    client:
        enabled: true
        healthcheck:
            enabled: true
        fetch-registry: true
        register-with-eureka: true
        instance-info-replication-interval-seconds: 10
        registry-fetch-interval-seconds: 10
    instance:
        appname: gateway
        instanceId: gateway:${spring.application.instance-id:${random.value}}
        lease-renewal-interval-in-seconds: 5
        lease-expiration-duration-in-seconds: 10
        status-page-url-path: ${management.context-path}/info
        health-check-url-path: ${management.context-path}/health
        metadata-map:
            zone: primary # This is needed for the load balancer
            profile: ${spring.profiles.active}
            version: ${info.project.version}
```

我们选择启用健康检查，并将注册和复制的间隔设置为 `10` 秒，以及我们定义的租约更新间隔和过期持续时间。

我们将在 Hystrix 中配置超时，超过这个时间服务器将被认为是关闭的：

```js
hystrix:
    command:
        default:
            execution:
                isolation:
                    thread:
                        timeoutInMilliseconds: 10000
```

如果服务器在`10`秒内没有响应，则认为服务器已死亡，并在注册服务中注册。这确保了在服务器变为活动状态之前不会向该服务器发送后续请求。

# JWT 身份验证

我们需要在微服务之间安全地传输信息。请求必须经过验证并数字签名，应用程序验证请求的真实性并对其进行响应。

我们需要在 REST 或 HTTP 世界中以紧凑的方式处理这些信息，因为信息需要与每个请求一起发送。JWT 正是为此而来。JWT 基本上是开放网络标准中的 JSON Web Tokens，有助于在各方（应用程序）之间安全地传输信息。JWT 将使用秘密、基于 HMAC 算法或使用公钥/私钥进行签名。它们是紧凑且自包含的。

对于高级用途，我们需要添加 Bouncy Castle（[库](https://en.wikipedia.org/wiki/Bouncy_Castle_(cryptography))）。

**紧凑型**：它们很小，可以发送到每个请求。

**自包含**：有效载荷包含有关用户的全部必要细节，这防止我们查询数据库进行用户身份验证。

JWT 由头部、有效载荷和签名组成。它们是 base64 编码的字符串，由`.`（点）分隔：

```js
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IlNlbmRpbCBLdW1hciBOIiwiYWRtaW4iOnRydWV9.ILwKeJ128TwDZmLGAeeY7qiROxA3kXiXOG4MxTQVk_I
```

```js
#Algorithm for JWT generation
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),

)
```

# JWT 是如何工作的

当用户登录系统时，会根据有效载荷（即用户信息和秘密密钥）生成一个令牌。生成的令牌将存储在本地。对于所有未来的请求，此令牌将添加到请求中，应用程序将在响应请求之前验证令牌：

![图片](img/06f4754b-3f61-4656-ba67-e7feac2d7197.jpg)

令牌的格式如下：

```js
Authorization: Bearer <token>
```

在 JHipster 中，我们使用来自 Okta 的**JJWT**（**基于 Java 的 JSON Web Tokens**）。这是一个基于简化构建者模式的库，用于生成和签名令牌作为生产者，以及解析和验证令牌作为消费者。

**创建令牌**：

```js
public class TokenProvider {
  ...
    public String createToken(Authentication authentication, Boolean rememberMe) {
        ...

        return Jwts.builder()
            .setSubject(authentication.getName())
            .claim(AUTHORITIES_KEY, authorities)
            .signWith(SignatureAlgorithm.HS512, secretKey)
            .setExpiration(validity)
            .compact();
    }
}
```

**验证令牌**：

```js
public boolean validateToken(String authToken) {
    try {
        Jwts.parser().setSigningKey(secretKey).parseClaimsJws(authToken);
        return true;
    } catch (SignatureException e) {
        log.info("Invalid JWT signature.");
        log.trace("Invalid JWT signature trace: {}", e);
    } catch (MalformedJwtException e) {
        log.info("Invalid JWT token.");
        log.trace("Invalid JWT token trace: {}", e);
    } catch (ExpiredJwtException e) {
        log.info("Expired JWT token.");
        log.trace("Expired JWT token trace: {}", e);
    } catch (UnsupportedJwtException e) {
        log.info("Unsupported JWT token.");
        log.trace("Unsupported JWT token trace: {}", e);
    } catch (IllegalArgumentException e) {
        log.info("JWT token compact of handler are invalid.");
        log.trace("JWT token compact of handler are invalid trace: {}", e);
    }
    return false;
}
```

到目前为止，我们已经创建了一个网关应用程序，它将作为我们应用程序和服务的单一入口点。现在，我们将使用 JHipster 生成一个微服务应用程序。

微服务应用程序是**服务**。在这本书中，我们将构建两个示例服务，并讨论 JHipster 提供的特点，一个使用 SQL 数据库（MySQL），另一个使用 NoSQL 数据库（MongoDB）。这些服务是独立的，并且松散耦合。

使用 JHipster，你可以构建作为 REST 端点的微服务应用程序。

# 微服务应用程序 - 带有 MySQL 数据库的发票服务

我们可以从我们的单体应用程序中提取发票服务，将其分离，并使其成为独立的微服务应用程序。让我们称它为`Invoice Service`。此服务负责创建和跟踪发票。

# 应用程序生成

首先，让我们看看我们如何生成一个微服务应用程序。在 `e-commerce-app` 文件夹中，创建一个新的文件夹，用于存放微服务应用程序。让我们将文件夹命名为 `invoice`。进入目录，通过输入 `jhipster` 开始创建应用程序：

第一个问题是我们被要求选择我们希望创建的应用程序类型。我们必须选择微服务应用程序，然后点击 *Enter*：

![图片](img/4e17b905-7db2-486e-bcbb-3b66de4aee72.png)

然后，您需要为您的应用程序提供一个基本名称。我们将使用默认的应用程序名称，`invoice`（默认情况下，JHipster 选择与应用程序名称相同的文件夹名称）：

![图片](img/9fd3d888-bb65-4f1c-8584-cc23c1f133ab.png)

然后，我们将选择应用程序必须运行的默认端口。默认情况下，JHipster 提示 `8081` 作为微服务的默认端口，因为我们使用 `8080` 作为网关应用程序：

![图片](img/8017b65a-03b6-4170-8285-9eba66127c9f.png)

然后，我们将选择默认的包名：

![图片](img/46ad4bf4-8b4d-49b7-8efc-5ae201772ab8.png)

由于我们已选择 JHipster Registry 作为网关应用程序，因此在这里我们将选择相同的选项。同样，如果我们选择了 Consul 作为网关应用程序，那么我们也可以选择 Consul。我们甚至可以选择不使用注册表，然后添加任何自定义注册表：

![图片](img/c801cc1d-6ed1-4fc2-abaa-eb2a625a2efc.png)

然后，JHipster 会询问我们希望使用的身份验证类型。我们将选择 JWT 身份验证，与网关应用程序中选择的相同：

![图片](img/0edeebcc-c85f-4b4c-a6f3-1d7a16cd319b.png)

然后，选择我们需要使用的数据库类型。如突出所示，发票服务将使用 SQL 数据库。我们将选择 SQL 选项。JHipster 提供了一个选项来选择不使用数据库本身。当未选择数据库时，应用程序将生成不带数据库连接：

![图片](img/c9bac2b4-739a-42ba-a709-0a6e3677a425.png)

我们将选择生产数据库为 MySQL：

![图片](img/38ca070e-3f68-4f09-b85a-c3d79ffb1eb5.png)

然后，我们将选择开发数据库为 H2，并使用基于磁盘的持久性：

![图片](img/8d9676cd-e413-4466-860e-d0f631aeef24.png)

然后，我们将选择 HazelCast 缓存作为 Spring 缓存抽象。Hazelcast 为所有会话提供共享缓存。可以在集群或 JVM 层面上保持持久数据。我们可以有不同的模式可供选择，包括单节点或多节点。

Ehcache 是一个本地缓存，它适用于在单个节点中存储信息。Infinispan 和 HazelCast 能够创建集群并在多个节点之间共享信息，其中 HazelCast 使用分布式缓存，每个节点相互连接。另一方面，Infinispan 是一个混合缓存：

![图片](img/0835f7db-0939-4f5c-9af0-d9e5e23d9e3c.png)

然后，我们将选择 Hibernate 2 级缓存：

![图片](img/34d9625d-60b7-49dc-9915-5b3bf5adf143.png)

我们将选择 Gradle 作为构建工具：

![图片](img/4f3a071c-bb09-4dbe-bfca-97b757a55100.png)

然后，JHipster 会询问我们是否还有其他技术想要添加。我们这里不会选择任何内容，并使用默认选项：

![图片](img/79e8b6a6-5547-431e-a07b-0c6f758d6aab.png)

然后，我们将选择**国际化**（**i18n**）：

![图片](img/8de93a2f-1f4e-4ff9-ae80-4d674117f23b.png)

然后，我们将选择默认选项为英语：

![图片](img/cee1b70b-d3d7-4908-9ad1-3077b6a6c761.png)

选择我们需要的附加语言：

![图片](img/590fd2e0-1caf-4c0c-a854-252000be3ad4.png)

然后，选择我们想要添加到 Gatling 或 Cucumber 的任何其他测试框架。请注意，由于它不会生成前端应用程序，因此选项如 Protractor 未列出：

![图片](img/d055b131-1c63-4e08-84e4-613406808f4b.png)

最后，我们将从 JHipster 市场中选择我们需要的任何其他生成器进行安装。目前，我们将不会选择任何其他生成器（默认选项）：

![图片](img/d8a4d1f5-1b1a-48ff-aceb-3c8860351241.png)

然后，生成服务器应用程序：

![图片](img/8d34b1df-4ba3-4ec6-bb2f-c67a21fcb26b.png)

我们已生成微服务应用程序。JHipster 将自动将生成的文件提交到 Git。如果您希望手动执行此步骤，可以在执行期间传递 `skip-git` 标志，例如，`jhipster --skip-git`，然后手动执行以下步骤：

```js
> git init
> git add --all
> git commit -am "generated invoice microservice application"
```

# 微服务配置

生成的应用程序将不包含任何前端。再次强调，发票服务是基于 Spring Boot 的应用程序。安全功能在 `MicroserviceSecurityConfiguration.java`*.* 中配置。

忽略所有与前端相关的请求，因此当用户尝试访问任何与前端相关的资源，如 HTML、CSS 和 JS 时，请求将由发票服务忽略：

```js
@Override
public void configure(WebSecurity web) throws Exception {
    web.ignoring()
        .antMatchers(HttpMethod.OPTIONS, "/**")
        .antMatchers("/app/**/*.{js,html}")
        .antMatchers("/bower_components/**")
        .antMatchers("/i18n/**")
        .antMatchers("/content/**")
        .antMatchers("/swagger-ui/index.html")
        .antMatchers("/test/**")
        .antMatchers("/h2-console/**");
}
```

由于服务是独立的，它们可以部署并运行在具有不同 IP 地址的另一个服务器上。这要求我们默认禁用 **CSRF**（**跨站请求伪造**）。我们还将启用会话管理中的无状态会话策略。这使得我们的应用程序无法创建或维护任何会话。每个请求都是基于令牌进行认证和授权的。

我们还将使用会话管理中的无状态会话策略。这是最严格的会话策略。这不会允许我们的应用程序生成会话，因此我们的请求必须附有每个请求的（时间限制）令牌。这增强了我们服务的安全性。它们的无状态约束是使用 REST API 的另一个优点。

关于会话策略的更多选项和信息，请参阅以下文档：[`docs.spring.io/autorepo/docs/spring-security/4.2.3.RELEASE/apidocs/org/springframework/security/config/http/SessionCreationPolicy.html`](https://docs.spring.io/autorepo/docs/spring-security/4.2.3.RELEASE/apidocs/org/springframework/security/config/http/SessionCreationPolicy.html)。

然后，一旦请求被授权（基于 JWT 令牌），就应该允许所有与 API 相关的请求和 Swagger 资源：

```js
@Override
protected void configure(HttpSecurity http) throws Exception {
    http
        .csrf()
        .disable()
        .headers()
        .frameOptions()
        .disable()
    .and()
        .sessionManagement()
        .sessionCreationPolicy(SessionCreationPolicy.STATELESS)
    .and()
        .authorizeRequests()
        .antMatchers("/api/**").authenticated()
        .antMatchers("/management/health").permitAll()
        .antMatchers("/management/**").hasAuthority(AuthoritiesConstants.ADMIN)
        .antMatchers("/swagger-resources/configuration/ui").permitAll()
    .and()
        .apply(securityConfigurerAdapter());
}
```

在资源方面，在`bootstrap.yml`中，我们已定义了与注册表相关的信息。

我们当前的微服务应用程序使用 JHipster 注册表作为注册表服务，以便在心跳中注册和注销其存在。我们需要提供注册表服务的密码，以便应用程序可以连接到注册表服务：

```js
jhipster:
    registry:
        password: admin
```

此外，Spring Boot 服务的名称和默认的 Spring Cloud Config 参数已在`bootstrap.yml`中启用。我们还添加了必须连接以获取注册表服务配置的 URI：

```js
spring:
    application:
        name: invoice
        ...
    cloud:
        config:
            fail-fast: false # if not in "prod" profile, do not force to use Spring Cloud Config
            uri: http://admin:${jhipster.registry.password}@localhost:8761/config
            # name of the config server's property source (file.yml) that we want to use
            name: invoice
            ...

```

与网关类似，其余的服务相关配置都是在`application.yml`文件中完成的。

Eureka 配置与网关应用程序中的配置完全相同。所有生成的应用程序都将具有类似的 Eureka 配置：

```js
eureka:
    client:
        enabled: true
        healthcheck:
            enabled: true
        fetch-registry: true
        register-with-eureka: true
        instance-info-replication-interval-seconds: 10
        registry-fetch-interval-seconds: 10
    instance:
        appname: invoice
        instanceId: invoice:${spring.application.instance-id:${random.value}}
        lease-renewal-interval-in-seconds: 5
        lease-expiration-duration-in-seconds: 10
        status-page-url-path: ${management.context-path}/info
        health-check-url-path: ${management.context-path}/health
        metadata-map:
            zone: primary # This is needed for the load balancer
            profile: ${spring.profiles.active}
            version: ${info.project.version}
```

数据库和 JPA 配置如下：

```js
spring:
    profiles:
        active: dev
    ...
    datasource:
        type: <connector jar>
        url: <db url>
        username: <username>
        password: <password>
    ...
    jpa:
        database-platform: <DB platform>
        database: <H2 or MySQL or any SQL database>
        show-sql: true
        properties:
            hibernate.id.new_generator_mappings: true
            hibernate.cache.use_second_level_cache: true
            hibernate.cache.use_query_cache: false
            hibernate.generate_statistics: true
            hibernate.cache.region.factory_class: com.hazelcast.hibernate.HazelcastCacheRegionFactory
            hibernate.cache.hazelcast.instance_name: invoice
            hibernate.cache.use_minimal_puts: true
            hibernate.cache.hazelcast.use_lite_member: true
```

其余的配置与网关应用程序中生成的配置相似，可以根据您的需求进行调整或定制。

现在，我们可以与应用程序和注册表服务一起启动应用程序。由于应用程序首先尝试连接到注册表服务，如果指定的位置没有可用的注册表服务，则应用程序将不知道连接何处以及响应谁。

因此，发票服务已生成。现在，我们可以使用 NoSQL 作为后端数据库生成通知服务。

# 微服务应用程序 - 使用 NoSQL 数据库的通知服务

对于电子商务网站来说，订单的跟踪和用户在正确的时间收到通知是非常关键的。我们将创建一个通知服务，该服务将在用户的订单状态发生变化时通知用户。

# 应用程序生成

让我们在`e-commerce-app`文件夹中生成我们的第二个微服务应用程序（通知服务）。在将微服务应用程序保存在的新文件夹中创建一个新文件夹。让我们将文件夹命名为`notification`。进入目录，通过运行`jhipster`开始创建应用程序。

我们被问到的第一个问题是选择我们想要创建的应用程序类型。我们必须选择微服务应用程序，然后点击*Enter*：

![图片](img/178c818e-593f-495b-9e30-6c70f754d4c1.png)

然后，我们将选择默认的应用程序名称，`notification`：

![图片](img/54fbf7a7-c148-41ba-8002-1be2d6173916.png)

然后，我们将选择应用程序的端口号。由于我们已将 `8080` 用于单体应用程序和 `8081` 用于发票服务，因此我们将使用端口号 `8082` 用于通知服务：

![图片](img/1ac1b9cd-bb3e-41d8-b3d2-1265b8987630.png)

对于接下来的三个问题，我们将使用之前相同的选项：

![图片](img/d475c013-8d0b-43fc-91f5-c874a3d462c6.png)

然后，我们将选择 MongoDB 作为数据库。在选择了 MongoDB 之后，JHipster 现在将询问您希望用于开发和生产服务器的不同类型的数据库。我们将使用 MongoDB 作为开发和生产数据库：

![图片](img/ccad6fc7-b2c4-454d-bd26-75100bde78b0.png)

对于剩余的问题，我们将选择与我们在发票服务中选择的选项类似的选项：

![图片](img/546e532b-012d-4490-851d-0b35940b89e0.png)

服务器已成功生成：

![图片](img/290c5027-31e8-4cc1-a49d-003da91c9138.png)

我们的微服务应用程序已生成。JHipster 将自动将生成的文件提交到 Git。如果您希望手动执行此步骤，可以在执行期间传递 `skip-git` 标志，例如，`jhipster --skip-git`，然后按照以下步骤手动执行：

```js
> git init
> git add --all
> git commit -am "generated notification microservice application"
```

# 微服务配置

由于我们为两个微服务选择了类似选项，应用程序最终生成。生成的代码将类似，但数据库配置除外：

![图片](img/75ed1cc5-10a4-4410-b82b-17934bb4a4b8.png)

# 摘要

好的，在本章中，我们已经生成了一个网关应用程序和两个微服务应用程序。我们已经向您展示了使用 JHipster 生成微服务包是多么容易。现在，在我们运行应用程序之前，我们需要启动我们的注册服务器。

在下一章中，我们将启动注册服务器，我们还将了解如何将实体添加到我们的新服务中。
