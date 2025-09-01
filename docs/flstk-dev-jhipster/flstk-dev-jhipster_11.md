# 第十一章：使用 Docker Compose 进行部署

我们已经生成了应用程序，并且它已准备好投入生产。在本章中，我们将重点介绍如何使用 Docker Compose 部署应用程序。我们还将了解 JHipster 提供的各种部署选项，以及如何将我们的注册表和控制台与应用程序一起部署。

在本章中，我们将探讨：

+   Docker Compose 的简要介绍

+   启动 Kubernetes

+   介绍 OpenShift

+   解释 Rancher

然后我们将讨论使用 JHipster Registry 和 JHipster Console 进行本地讨论

# 介绍微服务部署选项

应用程序的成功不仅取决于我们设计得有多好。它还取决于我们实施（部署和维护）得有多好。

在低可用性环境中设计良好的微服务应用是无用的。因此，决定一个能增加其成功机会的部署策略同样重要。在部署方面，有大量的工具可供选择。每个工具都有其优缺点，我们必须选择一个适合我们需求的。JHipster 目前提供子生成器来创建配置文件，以便通过以下方式容器化、部署和管理微服务：

+   Docker

+   Kubernetes（也有助于编排您的部署）

+   OpenShift（也提供私有云部署）

+   Rancher（也提供完整的容器管理）

我们将在以下章节中详细说明。

# Docker Compose 的简要介绍

将代码发送到服务器总是困难的，尤其是当你想要扩展它时。这主要是因为我们必须手动创建相同的环境，并确保应用程序具有所有必要的连接性（到其他服务），这是必需的。这在团队发送和扩展代码时是一个主要痛点。

将代码发送到服务器是困难的。

容器是这个领域的游戏改变者。它们帮助将整个应用程序及其依赖项捆绑在一个可发送的容器中，而我们所需做的就是提供一个环境，让这些容器可以运行。这简化了将代码发送到服务器以及开发团队之间的过程。这也减少了团队确保应用程序在环境中无缝运行所需的时间。

容器解决了应用程序部署问题，但我们如何扩展它们？

Docker Compose 工具在这里提供了帮助。首先，让我们看看 Docker Compose 是什么，然后看看它解决了什么问题。

Docker Compose 是一个工具，它可以帮助使用单个文件定义和运行多容器 Docker 应用程序。也就是说，我们使用`.yaml`文件来定义应用程序的要求和/或依赖关系。然后，使用`docker-compose`，我们可以创建新的部署并启动在`docker-compose`文件中定义的应用程序。

那么，`docker-compose`文件中需要什么？

以下是一个示例 `docker-compose` 文件，它将在端口 `5000` 上启动 Redis 数据库：

```js
version: '3'
services:
  web:
    build: .
    ports:
     - "5000:5000"
  redis:
    image: "redis:alpine"
```

`docker-compose` 文件的第 一行应该是 `docker-compose` 工具的版本。

然后我们需要指定应用程序运行所需的所有必要服务。它们应该在 `services:` 部分中定义。

我们也可以在这里定义多个服务，并为每个服务命名（例如 `web` 和 `redis`）。

这随后是构建 `service` 的方法（通过命令构建或引用 Dockerfile）。

如果应用程序需要任何端口访问，我们可以使用 `5000:5000`（即内部端口：外部端口）进行配置。

然后，我们必须指定卷信息。这基本上告诉 `docker-compose` 从指定的位置提供文件。

一旦我们指定了应用程序所需的各项服务，就可以通过 `docker-compose` 启动应用程序。这将启动整个应用程序以及服务，并将 `services` 暴露在指定的端口上。

使用 `docker-compose`，我们可以执行以下操作：

+   **启动**：`docker-compose -f <docker_file> up`

+   **停止**：`docker-compose -f <docker_file> down`

我们还可以执行以下操作：

+   **列出正在运行的服务及其状态**：`docker ps`

+   **日志**：`docker log <container_id>`

在 `compose` 文件中，我们可以添加项目名称，如下所示：

```js
version: '3'
COMPOSE_PROJECT_NAME: "myapp"
services:
  web:
    build: .
    ports:
     - "5000:5000"
  redis:
    image: "redis:alpine"
```

这可以用于识别多个环境。借助这个工具，我们可以隔离多个环境。这有助于我们处理跨各种 `dev`、`QA` 和 `prod` 环境的多个实例。

`Docker-compose` 本身就是一个部署应用程序及其所需所有服务的优秀工具。它提供基础设施即代码。它是开发、QA 和其他环境的绝佳选择，除了生产环境。但为什么？

`Docker-compose` 确实是一个创建和启动应用程序的好工具。然而，当你想要更新现有的容器时，将会有一个确定性的停机时间，因为 `docker-compose` 将重新创建整个容器（虽然有一些方法可以使这成为可能，但 `docker-compose` 在这个领域仍需要一些改进。）

# 启动 Kubernetes

根据 Kubernetes 网站：

*Kubernetes 是一个用于自动化部署、扩展和管理容器化应用程序的开源系统。*

它是一个简单而强大的工具，用于自动部署、扩展和管理容器化应用程序。当你推出新应用程序或更新现有应用程序时，它提供零停机时间。你可以根据某些因素自动扩展和缩小。它还提供自我修复功能，即 Kubernetes 会自动检测失败的应用程序并启动一个新的实例。我们还可以定义可以在实例间使用的密钥和配置。

Kubernetes 主要关注零停机时间生产应用程序的升级，并根据需要对其进行扩展。

Kubernetes 中单个可部署的组件被称为 **pod**。这可以简单到容器中运行的一个进程。一组 pod 可以组合在一起形成一个 **deployment**。

类似于 `docker-compose`，我们可以在单个 YAML 文件或多个文件（根据我们的方便）中定义应用程序及其所需的服务。

在这里，我们同样从一个 Kubernetes 部署文件中的 `apiVersion` 开始。

以下代码是一个示例 Kubernetes 文件，它将启动一个 Nginx 服务器：

```js
apiVersion: v1
kind: Service
metadata:
 name: nginxsvc
 labels:
 app: nginx
spec:
 type: NodePort
 ports:
 - port: 80
 protocol: TCP
 name: http
 - port: 443
 protocol: TCP
 name: https
 selector:
 app: nginx
```

接着是类型，它可以是 pod、deployment、namespace、ingress（负载均衡 pod）、role 以及更多。

Ingress 在服务和互联网之间形成一层，这样在将连接发送到集群上的 Kubernetes 服务之前，所有传入的连接都由 ingress 控制器进行控制或配置。另一方面，egress 控制器控制或配置离开 Kubernetes 集群的服务。

接下来是元数据信息，例如环境类型、应用程序名称（nginxsvc）和标签（Kubernetes 使用这些信息来识别和隔离 pod）。Kubernetes 使用这些元数据信息来识别特定的 pod 或 pod 组，我们可以使用这些元数据来管理实例。这是与 `docker-compose` 的一大区别，因为 `docker-compose` 没有定义容器元数据的灵活性。

接下来是 spec，在这里我们定义镜像或应用程序的规范。我们还可以定义镜像的拉取策略，以及定义暴露的端口和环境变量。我们可以在机器（或 VM）上为特定服务定义资源限制。它们提供健康检查，即每个服务都会被监控其健康状态，当某些服务失败时，它们会立即被新的服务替换。它们还提供开箱即用的服务发现，通过为每个 pod 分配一个 IP 地址，这使得服务更容易识别和与之交互。它们还提供了一个更好的仪表板，用于可视化您的架构和应用程序的状态。您可以通过这个仪表板进行大部分管理，例如检查状态、日志、扩展或缩减服务，等等。

由于 Kubernetes 提供了完整的配置选项来编排我们的服务，这使得它最初设置起来非常困难，这意味着它不适合开发环境。我们还需要 **kubectl** CLI 工具进行管理。尽管我们在内部使用 Docker 镜像，但 Docker CLI 不能使用。

此外还有 **Minikube**（精简版 Kubernetes），它用于本地开发和测试应用程序。

Kubernetes 不仅负责将您的应用程序容器化，它还帮助扩展、管理和部署您的应用程序。它编排了您的整个应用程序部署。此外，它还提供服务发现和自动健康检查。

我们将在下一章中更多地关注 Kubernetes 子生成器。

# 介绍 OpenShift

OpenShift 是一个多云、开源的容器应用程序平台。它基于 Kubernetes，用于开发、部署和管理应用程序。它是开发者和运维人员的通用平台。它帮助他们一致地在混合云和多云基础设施上构建、部署和管理应用程序。

对于开发者，它提供了一个自助平台，他们可以在其中配置、构建和部署应用程序及其组件。通过自动工作流程将源代码转换为镜像，它帮助开发者从源代码到可运行的、docker 化的镜像。

对于运维人员，它提供了一个安全的、企业级的 Kubernetes，用于基于策略的控制和自动化应用程序管理，例如集群服务、调度和编排，具有负载均衡和自动扩展功能。

JHipster 还提供了一个独立的子生成器来提供 OpenShift 部署文件。我们可以通过运行 `jhipster openshift` 并根据需要回答问题来生成它们。这将生成与 OpenShift 相关的部署文件。

# 解释 Rancher

Rancher 是一个容器管理平台。它也是开源的。它帮助任何组织部署和维护容器。Rancher 只是一个部署服务器，它安装在任意的 Linux 机器或集群上。因此，要使用 Rancher，我们首先应该启动 Rancher 容器，这需要 Docker 可用。

一旦启动，我们就可以登录到 Rancher 并开始部署我们的应用程序。它还具有角色管理功能。Rancher 提供了在 Swarm、Kubernetes 或 Cattle（以及其他集群部署选项）之间进行选择的选项。它还提供了有关已部署的基础设施和应用程序的详细信息。它显示了有关容器、注册表、数据池和其他信息（与容器和基础设施相关的信息）的详细信息。

它还提供了根据需要调整 Kubernetes 或 Swarm 设置的选项，这使得扩展和缩减规模变得更加容易。它还提供了通过其 UI 或使用 `docker-compose.yml` 和 `rancher-compose.yml` 启动整个应用程序堆栈的选项。它还具有加载外部服务并使用它们的 capability（例如负载均衡器）。

JHipster 还提供了一个独立的子生成器来提供 Rancher 部署文件。我们可以通过运行 `jhipster rancher` 并根据需要回答问题来生成它们。这将生成 Rancher 配置文件。

# 生成的 Docker Compose 文件

默认情况下，JHipster 会生成 Docker Compose 文件，使我们能够在容器化环境中完全运行应用，无论选择了哪些选项。例如，在我们生成的网关应用中，以下文件默认在`src/main/docker`下生成：

+   `sonar.yml`：此文件创建并启动一个 SonarQube 服务器

+   `mysql.yml`：此文件创建并启动一个 MySQL 数据库服务器，并创建一个用户和模式

+   `jhipster-registry.yml`：此文件创建并启动一个 JHipster 注册表服务

+   `app.yml`：这是创建并启动应用以及如 JHipster 注册表和数据库等服务的主要文件

此外，JHipster 还创建了一个 Dockerfile，这有助于您将应用容器化。

然后我们可以看到一个名为`central-server-config`的文件夹。这将被用作 JHipster 注册表的中央配置服务器。

当注册表和应用在 Docker 中运行时，它使用`docker-config`文件夹中的`application.yml`作为中央配置服务器。

另一方面，当仅以 Docker 模式运行注册表时，非 Docker 的应用将使用`localhost-config`文件夹中的`application.yml`。关键区别在于定义 Eureka 客户端的 URL 不同。

让我们看看生成的 Docker 文件。

# 查看生成的文件

让我们从位于您的网关应用`src/main/docker`目录下的`app.yml`文件开始。

正如我们在本章开头所看到的，文件以它支持的 Docker 版本开始：

```js
version: '2'
```

这之后是服务部分，我们在这里定义了各种服务、应用或组件，我们将使用此 Docker 文件启动它们。

在服务部分，我们将为服务定义一个名称，在我们的例子中我们使用了`gateway-app`，然后是我们要用作容器的图像。这个图像是通过我们那个文件夹中的 Docker 文件生成的。

这之后是我们应用将依赖的一系列环境变量，它们包括：

+   `SPRING_PROFILES_ACTIVE`：告诉应用以生产模式运行并暴露 Swagger 端点。

+   `EUREKA_CLIENT_SERVICE_URL_DEFAULTZONE`：告诉应用在哪里检查 JHipster 注册表（这是我们使用的 Eureka 客户端。如果我们在这里选择了 Consul，那么应用将指向 Consul URL）

+   `SPRING_CLOUD_CONFIG_URI`：告诉应用在哪里查找应用的`config`服务。

+   `SPRING_DATASOURCE_URL`：告诉应用在哪里查找数据源。

+   `JHIPSTER_SLEEP`：这是一个自定义属性，我们用它来确保 JHipster 注册表在应用启动之前启动。

最后，我们指定应用应在哪个端口上运行并暴露：

```js
services:
    gateway-app:
        image: 'gateway'
        environment: 
            - SPRING_PROFILES_ACTIVE=prod,swagger
            - EUREKA_CLIENT_SERVICE_URL_DEFAULTZONE=http://admin:$${jhipster.registry.password}@jhipster-registry:8761/eureka
            - SPRING_CLOUD_CONFIG_URI=http://admin:$${jhipster.registry.password}@jhipster-registry:8761/config
            - SPRING_DATASOURCE_URL=jdbc:mysql://gateway-mysql:3306/gateway?.....
            - JHIPSTER_SLEEP=30
        ports:
            8080:8080
```

我们刚刚使用 `docker-compose` 文件定义了服务；现在我们必须指定两个其他服务，这些服务对于我们的应用程序运行是必需的。它们是数据库和 JHipster 注册中心。

因此，我们注册了另一个名为 `gateway-mysql` 的服务，该服务创建并启动 MySQL 服务器。我们可以将 MySQL 定义为单独的 Docker Compose 文件，并将其链接在这里。因此，我们放置一个 `extends` 关键字，后面跟着 `docker-compose` 文件和从指定的 `docker-compose` 文件中启动的服务：

```js
   gateway-mysql:
        extends:
            file: mysql.yml
            service: gateway-mysql.yml
```

然后我们输入以下代码到 `mysql.yml` 文件中，如下所示：

```js
version: '2'
services:
    gateway-mysql: 
        image: mysql:5.7.20 
        # volumes: 
        # - ~/volumes/jhipster/gateway/mysql/:/var/lib/mysql/ 
        environment: 
            - MYSQL_USER=root 
            - MYSQL_ALLOW_EMPTY_PASSWORD=yes 
            - MYSQL_DATABASE=gateway 
        ports: 
            - 3306:3306 
        command: mysqld --lower_case_table_names=1 --skip-ssl --character_set_server=utf8 --explicit_defaults_for_timestamp
```

我们再次从它支持的版本开始，接着是 `services` 关键字，然后指定 `service` 名称，`gateway-mysql`，这是在 `app.yml` 文件中使用的。如果您想指定用于持久数据存储的卷，可以取消注释已注释的卷段。这基本上是将本地文件位置映射到 Docker 的内部位置，以便即使 Docker 镜像本身被替换或更新，数据也能保持持久。

这之后是一系列环境变量，例如用户名和密码（我们在这里将其设置为空，但对于真正的生产应用程序，建议设置一个更复杂的密码），然后是数据库模式名称。

我们还指定了启动 MySQL 服务器所需的命令。

然后我们回到 `app.yml` 文件，并定义 JHipster 注册服务。这将再次扩展 `jhipster-registry.yml` 和 `docker-compose` 文件。在此需要注意的是，尽管我们从另一个 Docker 文件扩展了服务，但我们仍然可以覆盖在原始 `docker-compose` 文件中指定的环境变量。在某些情况下，当我们需要使用不同的或定制的值启动应用程序时，这非常有用。在我们的例子中，我们已经覆盖了 Spring Cloud Config 服务器文件位置，从原始位置更改过来：

```js
    jhipster-registry:
        extends:
            file: jhipster-registry.yml
            service: jhipster-registry
        environment:
            - SPRING_CLOUD_CONFIG_SERVER_NATIVE_SEARCH_LOCATIONS=file:./central-config/docker-config/

```

`Jhipster-registry.yml` 文件：

```js
version: '2'
services:
    jhipster-registry:
        image: jhipster/jhipster-registry:v3.2.3
    volumes:
        - ./central-server-config:/central-config
    # When run with the "dev" Spring profile, the JHipster Registry will
    # read the config from the local filesystem (central-server-config directory)
    # When run with the "prod" Spring profile, it will read the configuration from a Git             repository
        # See http://www.jhipster.tech/microservices-architecture/#registry_app_configuration
    environment:
        - SPRING_PROFILES_ACTIVE=dev
        - SECURITY_USER_PASSWORD=admin
        - JHIPSTER_REGISTRY_PASSWORD=admin
        - SPRING_CLOUD_CONFIG_SERVER_NATIVE_SEARCH_LOCATIONS=file:./central-config/localhost-config/
        # - GIT_URI=https://github.com/jhipster/jhipster-registry/
        # - GIT_SEARCH_PATHS=central-config
    ports:
        - 8761:8761
```

我们已经定义了 JHipster 注册中心的中央配置如下。我们已配置 JWT 的密钥和 Eureka 客户端的 URL。指定的 JWT 令牌用于服务进行授权以及在它们之间以及与注册中心之间的通信：

```js
# Common configuration shared between all applications
configserver:
    name: Docker JHipster Registry
    status: Connected to the JHipster Registry running in Docker
jhipster:
    security:
        authentication:
            jwt:
                secret: my-secret-token-to-change-in-production
eureka:
    client:
        service-url:
            defaultZone: http://admin:${jhipster.registry.password}@localhost:8761/eureka/
```

除了这些，我们还生成了一个 `sonar.yml` 文件（此文件对于部署您的应用程序不是很重要）：

```js
version: '2'
services:
    gateway-sonar:
        image: sonarqube:6.5-alpine
        ports:
            - 9000:9000
            - 9092:9092
```

类似地，在微服务中，即在我们的发票和通知应用程序中，我们将生成类似的文件。它们除了服务名称的变化外，都是相同的。

与 MySQL 不同，MongoDB 也能够在具有不同节点和配置的集群中运行。我们需要在这里不同地指定它们。因此，我们将创建两个 docker-compose 文件。`mongodb.yml` 用于启动具有单个节点的 MongoDB，而 `mongodb-cluster.yml` 用于启动作为集群的 MongoDB。

请检查网关和微服务应用程序之间的数据库端口号。如果它们使用相同的数据库，由于 JHipster 为两者生成相同的端口号，因此可能会在端口号上发生冲突。将其更改为任何其他未使用的端口号，否则 Docker Compose 将显示错误。在我们的例子中，我已经将其更改为`3307`。

# 在本地构建和部署一切到 Docker

我们可以根据需要使用多种方式使用`docker-compose`文件。

通常，当我们开发应用程序时，我们可以使用通用的 Maven 或 Gradle 命令来运行应用程序，这样我们就可以调试应用程序并更快地重新加载更改，同时使用 Docker 启动数据库和 JHipster 注册表。

否则，您可以从`app.yml`文件启动整个应用程序，这将启动数据库、JHipster 注册表，然后是应用程序本身。为此，打开您的终端或命令提示符，转到应用程序文件夹，然后运行以下命令：

```js
> cd gateway
```

然后，我们必须首先通过以下命令对我们的应用程序进行生产构建以 Docker 化应用程序：

```js
> ./gradlew bootRepackage -Pprod buildDocker
```

完成后，我们可以通过`docker-compose`命令启动应用程序：

```js
> docker-compose -f src/main/docker/app.yml up -d
```

`-f`指定`docker-compose`应该启动服务器的文件。`-d`标志告诉`docker-compose`以分离模式运行一切。这将启动 Docker 中的应用程序，并在端口`8080`上公开应用程序，在端口`8761`上公开注册服务器，在端口`3306`上公开数据库。

然后，我们可以进入各自的微服务文件夹并执行相同的操作，使用以下命令创建 Docker 镜像：

```js
> ./gradlew bootRepackage -Pprod buildDocker
```

然后，我们可以通过以下命令使用`docker-compose`启动应用程序：

```js
> docker-compose -f <filename> -d
```

我们可以使用以下命令检查正在运行的 Docker 容器：

```js
> docker ps -a
```

它应该列出所有七个容器：

![](img/823303fc-e453-42ca-b415-eb29d6ec4793.png)

如您所见，有三个应用程序容器（网关/通知和发票），然后是一个 JHipster-Registry，接着是三个数据库容器（两个 MySQL 和一个 MongoDB。顺序可能有所不同）。

如果您使用 JHipster 版本 5 或更高版本，请使用`bootWar`而不是 Gradle 中的`bootRepackage`命令。

# 为微服务生成 docker-compose 文件

有许多`docker-compose`文件，维护它们很困难。幸运的是，JHipster 附带了一个`docker-compose`子生成器。`Docker-compose`子生成器帮助您将所有应用程序的 Dockerfile 组织在一起。它创建一个单独的 Dockerfile，该文件引用应用程序的 Dockerfile。

让我们转到基本文件夹并创建一个文件夹，并将其命名为`docker-compose`：

```js
> mkdir docker-compose && cd docker-compose
```

一旦进入`docker-compose`文件夹，我们可以运行以下命令：

```js
 jhipster docker-compose 
```

这将生成 Dockerfile。

如同往常，在生成文件之前，它将询问我们一系列问题：

![](img/6fc3ae79-37a7-4a6c-a6a1-458d5c2f7a51.png)

首先，它会询问我们想部署哪种类型的应用程序。我们将选择微服务应用程序作为选项。

这之后，它将询问我们希望使用哪种类型的网关；有两个选项可供选择，一个是带有 Zuul 代理的 JHipster-gateway，另一个是更令人兴奋的、带有 Consul 的 Traefik 网关

让我们选择带有 Zuul 代理的 JHipster-gateway：

![图片](img/2d028b76-22d8-4930-8ee0-f5f9f8b45bfe.png)

然后，我们必须选择微服务网关和应用程序的位置。这是我们为什么在单个父文件夹内生成应用程序的主要原因。这将帮助插件和子生成器轻松找到创建的 docker 配置文件。我们将选择默认选项（../）

![图片](img/a480c434-a824-4568-bcc5-cca434bbe6d5.png)

选择位置后，JHipster 将在给定的文件夹内搜索任何由 JHipster 生成的应用程序，并在下一个问题中列出它们。

在我们的案例中，它列出了通知、发票和网关。我们可以选择所有这些并按*Enter*键：

![图片](img/9af279b6-42ad-4a37-9413-0b1590627a08.png)

它会自动检测我们已经使用了 MongoDB，并询问我们下一个问题；是否希望将 MongoDB 作为集群使用。我们在这里不会选择任何内容：

![图片](img/36154e7c-2dee-4177-93c5-7e844dd8ee14.png)

然后它会询问关于控制台的问题；是否需要为应用程序设置任何控制台。我们将选择带有 JHipster 控制台（基于 ELK 和 Zipkin）的日志和指标：

![图片](img/604d8af5-db29-4b8e-923f-da698e6691c7.png)

我们可以选择退出监控选项或选择 Prometheus。这将连接到 Prometheus 并仅显示指标。

然后，JHipster 会询问您是否需要 Curator 或 Zipkin：

+   索引管理员将帮助您管理和维护由 Elasticsearch 创建的索引。

+   Zipkin（如前一章所述）

![图片](img/cf0fdc3c-a344-4a4f-9b3f-5e89b59f50df.png)

由于选择了 JHipster 控制台，它将询问控制台支持的其他信息。它们包括 Zipkin 和 Curator。我们已经看到了 Zipkin。另一方面，Curator 将帮助我们管理和维护 Elasticsearch 中的索引。

我们在这里将只选择 Zipkin。

![图片](img/59d1b8c4-99c8-468a-b7c5-d49e46f271d9.png)

我们也可以在这里选择不选择任何内容，并使用默认选项。

最后，它要求输入 JHipster Registry 的密码；我们将在这里使用默认设置：

![图片](img/0b3bd232-743e-416d-9c10-d6c2451fbfb5.png)

就这样；我们刚刚创建了一个更高层次的 Dockerfile，其中包含所有我们需要成功运行应用程序所需的信息。

现在，我们可以使用以下命令运行整个套件：

```js
> docker-compose up -d
```

这将启动网关、通知、发票和注册表，以及控制台和所有其他所需服务。

# 部署应用程序的功能

因此，部署的应用程序已准备好启动。我们可以在`http://localhost:8761`启动 JHipster Registry；它将列出所有已注册的应用程序：

![图片](img/3eb7cfcc-036a-43f0-9766-e232a6c9196f.png)

此外，注册表还告诉我们已注册的实例数量。导航到 Eureka | 实例以检查。目前，每种实例都注册了一个：

![图片](img/639db113-5f5f-4644-90e3-673bb49742ba.png)

类似地，网关应用将列出连接到它的微服务。访问 `http://localhost:8080`。

导航到管理 | 网关，查看连接到此网关应用的微服务应用：

![图片](img/51b49e7e-d335-4760-9356-7aa15d0f1646.png)

# JHipster 控制台演示

JHipster 还提供了一个基于 ELK 堆栈的控制台应用程序，可用于应用程序的日志和指标监控。JHipster 控制台是另一个开源应用程序。它非常有用，并提供了一些很好的仪表板来可视化应用程序。与其他 JHipster 产品一样，使用 JHipster 控制台更容易上手。

让我们回到我们的书籍文件夹，然后从 GitHub 克隆 JHipster 控制台项目（[`github.com/jhipster/jhipster-console`](https://github.com/jhipster/jhipster-console)）：

```js
> git clone https://github.com/jhipster/jhipster-console
```

在我们开始控制台之前，我们需要让我们的应用程序记录指标并登录到控制台。为此，我们需要更改应用程序中的几个设置，然后重新启动它们。

让我们转到所有应用程序（网关和微服务应用）中的 `application-prod.yml` 文件，并启用 logstash 和日志：

```js
metrics: # DropWizard Metrics configuration, used by MetricsConfiguration    
    ...
    logs: # Reports Dropwizard metrics in the logs
        enabled: true
        report-frequency: 60 # in seconds
logging:
    logstash: # Forward logs to logstash over a socket, used by LoggingConfiguration
        enabled: true
        host: localhost
        port: 5000
        queue-size: 512
```

在 `metrics.logs.enabled` 和 `logging.logstash.enabled` 中将启用设置为 true。这将把日志推送到控制台应用程序。JHipster 控制台将收集这些信息，并借助 Kibana 在漂亮的仪表板中显示它们。

一旦克隆完成，我们可以进入这个文件夹，然后使用`docker-compose`启动`jhipster-console`：

```js
> cd jhipster-console
> docker-compose up -d
```

就这样。您的控制台正在 `http://localhost:5601` 上运行。

Kibana 提供以下（可自定义）仪表板：

![图片](img/490f238b-6224-40c9-90a9-45035675d289.png)

Kibana 还提供应用程序级别的指标，例如 JVM 线程指标和其他详细信息：

![图片](img/53dc92f3-6f24-41cf-86b2-623c87c342ff.png)

此外，控制台还有一个界面，我们可以看到应用程序日志。它显示了所有部署的应用程序的日志。我们可以根据应用程序过滤和搜索日志：

![图片](img/a12046f7-0bda-4168-a082-5bc6b2b2918a.png)

Kibana 还提供了一个机器学习标签，我们可以创建一个作业来跟踪数据，然后选择任何可用的指标来跟踪它，如总和、计数、高计数等。

# 使用 Docker Swarm 进行扩展

Docker Swarm 是 Docker 的编排层，用于管理容器。它是一个集群管理工具，专注于创建服务的副本、网络以及它可用的存储资源，并管理它们。

Docker Swarm 不过是一组 Docker 节点。它们充当管理节点或工作节点。一个值得注意的有趣特性是，Swarm 内部的 Docker 容器可以是管理节点、工作节点或两者都是。这有助于 Swarm 在管理节点故障时分配新的管理节点。

在高层次上，管理节点负责集群管理任务并执行容器。工作节点只负责执行容器。

JHipster 应用程序为我们提供了使用单个命令扩展整个应用程序的灵活性，通过 JHipster 的`docker-compose sub-generator`：

```js
> docker-compose scale <app-name>=<scale to>

```

现在我们可以使用以下命令来扩展实例：

```js
> docker-compose scale invoice-app=2
```

前面的命令将启动另一个发票实例，我们可以在以下仪表板上看到它：

![](img/8c9ea7db-888c-4ceb-87a6-dfc146928e9c.png)

# 摘要

到目前为止，我们已经看到了如何生成、设置和启动 JHipster Registry 和 console，以及我们查看它们的特性。这之后是关于如何使用`docker-swarm`扩展应用程序。

在下一章中，我们将看到如何使用 Kubernetes 将应用程序部署到 Google Cloud。
