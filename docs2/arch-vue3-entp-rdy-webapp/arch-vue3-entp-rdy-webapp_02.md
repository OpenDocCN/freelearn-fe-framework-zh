

# 第二章：使用库构建大型应用程序

在你开始学习如何使用不同的库使用 Vue.js 3 开发大型和面向企业的应用程序之前，你需要了解这些单个库以及它们捆绑的各种功能，以帮助你导航构建可扩展和面向企业的应用程序。

在本章中，我们将涵盖 Vuex、Axios 和 Vue Router 的基本方面，以及如何将它们与 Vue 3 集成以开发企业级应用程序。这些背景信息将使你更好地理解这些库的术语和概念，并帮助你了解如何构建和扩展企业级应用程序。

我们在本章中将涵盖以下关键主题：

+   探索大型 Vuex

+   使用存储模式进行结构化

+   创建存储文件夹

+   使用 Vue Router 结构化 Vue 导航

一旦你掌握了这些主题中的每一个，你将准备好开始使用 Vue 3 构建你的第一个企业级应用程序。

# 技术要求

要开始本章的学习，我们建议阅读 *第一章*，“Vue.js 3 入门”，以及其中对 Vue 3 和组合 API 的概述，这些将在本章中广泛使用。

# 探索大型 Vuex

**Vuex** 是 Vue 应用程序的状态管理库。它作为 Vue 应用程序中所有组件的中心存储。它也是一个专门针对 Vue.js 定制的库实现，以利用其细粒度响应性系统进行高效更新。

使用 Vuex 对 Vue 应用程序进行状态管理可以带来显著的好处。然而，如果不进行适当的结构化，它很容易被误用和压倒，尤其是在构建大型企业级应用程序时——由于项目的规模以及将在项目中引入的组件和功能数量。

为了解决这个结构问题，我们将向您介绍不同的结构来安排您的 Vuex 存储，以及在第 *第四章* 中讨论的可预测性法则，“大型 Web 应用程序的架构”，以适应大型 Vue 应用程序。

在本节中，我们将讨论 Vuex 的状态、获取器、突变和动作。

实际上，结构化 Vuex 存储的常用方法是让所有代码都位于一个名为 **单状态树** 的单个 `index.js` 文件中。这种方法对于小型项目来说非常完美，有助于避免在不同文件中导航以找到单个方法。

然而，当使用 Vuex 开发企业项目时，使用单状态树会变得非常臃肿且难以维护。

为了减少这个大文件并将其拆分为不同的功能，Vuex 允许我们将我们的存储拆分为 **Vuex 模块**。

在我们深入之前，Vue 社区引入了一个新的状态管理系统，称为 Pinia，它解决了 Vuex 的问题，并且与 Vue 3 直接兼容。截至写作时，Pinia 仍处于开发和测试阶段。你可以在[`pinia.vuejs.org/`](https://pinia.vuejs.org/)了解更多关于 Pinia 以及如何将其集成到你的 Vue 3 应用程序中的信息。

## Vuex 模块

Vuex 模块是根据功能拆分我们的存储的一种方式，其中每个模块可以包含其状态、获取器、动作、突变，甚至嵌套模块。

这种方法允许我们将存储拆分为功能，创建不同的文件和文件夹来正确地安排存储。

我们将在下一小节中学习如何将我们的存储拆分为功能。

假设我们的 *Pinterest* 应用程序将具有不同的状态，如照片、用户、评论等，我们可以将存储拆分为单独的模块，而不是将其放在一个文件中。

### 使用 Vuex 模块

如前所述，使用 Vuex 模块带来了巨大的好处，我们将在这本书中坚持使用它。首先，让我们看看我们的 Vuex 模块存储的文件夹结构：

```js
const moduleA = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}
const moduleB = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... }
}
const store = createStore({
  modules: {
    a: moduleA,
    b: moduleB
  }
})
store.state.a // -> `moduleA`'s state
store.state.b // -> `moduleB`'s state
```

如前代码块所示，我们创建了不同的模块来分别包装我们的 Vuex 状态、动作和突变。这有助于将我们的项目结构化为不同的功能。

现在我们已经了解了如何为企业项目构建 Vuex 存储，让我们在下一节中讨论如何在 Vue 组件中访问和管理存储。

## Vuex 状态

首先，也是最重要的，让我们讨论状态以及我们如何管理模块 Vuex 存储的状态。

**Vuex 状态**是你在 Vuex 存储内部存储的数据，可以在你的 Vue 应用程序的任何地方访问。

Vuex 状态遵循单状态树模式。这个单一的对象包含所有应用程序级别的状态。它作为“单一事实来源”。但是，由于我们正在采用模块化来管理我们的企业级应用程序，我们只将学习如何访问和管理我们的模块状态。

以下代码片段展示了如何创建一个简单的 Vuex 状态：

```js
// initial state
const state = () => ({
  photos: [],
})
```

此外，你还可以在组件外部访问 Vuex 存储。例如，你可以在 Vue 服务、辅助文件中等地方访问 Vuex。然而，在下一节中，我们将探讨在组件中访问状态的不同方法。

### 不映射访问状态

假设这是我们 *Pinterest* 应用程序中所有照片的存储，并且我们有那个 `photos` 状态，我们如何在组件中访问它？

要访问 `Photos` 数组，我们将使用模块名称与存储名称，如下面的代码片段所示：

```js
const Photos = {
  template: `<div v-for="(photo, index) in photos"
              :key="index"> <img :src="img/photo.url"></div>`,
  computed: {
    photos () {
      return this.$store.photos.state.photos
    }
  }
}
```

以下代码片段展示了如何通过创建一个新的 `Photos` 组件来访问模块存储，并显示照片状态中的所有照片。

要访问 `Photos` 状态数组，我们使用了它所属的模块名称，并在 `photos` 数组之前访问了状态属性。

接下来，我们将探讨如何使用映射方法访问 Vuex 状态。

### 使用映射访问状态

访问存储的最佳方式是使用 **Vuex 状态映射**（[`vuex.vuejs.org/guide/state.html#the-mapstate-helper`](https://vuex.vuejs.org/guide/state.html#the-mapstate-helper)），我们将在这本书的整个过程中使用它。你可以访问官方 Vuex（[`vuex.vuejs.org/`](https://vuex.vuejs.org/)）文档了解更多信息。

当你的组件需要使用多个存储状态属性或获取器时，使用 Vuex 映射器是非常好的。声明所有这些状态和获取器可能会变得重复和冗长，这正是 Vuex 映射器试图解决的问题。

让我们以创建一个简单的 `Photos` 组件并使用 Vuex 状态来显示不同图像为例：

```js
import { mapState } from 'vuex'
const Photos = {
  template: `<div v-for="(photo, index) in photos"
              :key="index"> <img :src="img/photo.url"></div>`,
  computed: mapState({
    photos: state => state.photos.photos,
    }
  })
}
```

以下代码片段创建了一个 `Photos` 组件，遍历 Vuex 状态中的数据，并在存储中显示图像。

就这样。

我们将在与动作、突变和获取器的进一步讨论中使用此方法。你绝对不应该忘记你模块的名称。

我们现在对 Vuex 状态和模块有了相当的了解，以及我们将如何构建我们的企业级和大规模 Vuex 应用程序，以便易于维护和访问。

让我们讨论获取器，以及我们如何在下一节中使用 **Vuex 获取器**和映射获取器来操作我们的 Vuex 状态。

## Vuex 获取器

Vuex 获取器（getters）在操作 Vuex 状态方面非常有用。有时，你可能想在将数据返回给组件之前过滤或排序 Vuex 状态。

Vuex 允许我们创建获取器（getters），就像 Vue 中的计算属性一样操作状态。它还会缓存结果，并在数据更改时更新缓存。

在每个模块中，我们将定义其特定的获取器来操作该模块的状态。例如，我们将在 `Photos` 模块中创建一个获取器，根据用户 ID 过滤照片，如下所示：

```js
  getters: {
    getPhotoByID: (state) => (id) => {
      return state.photos.find(photo => photo.id === id)
    }
  }
```

以下代码片段展示了如何创建一个获取器，该获取器可以过滤出通过 `getters` 方法传递的特定用户 ID 添加的所有照片。

接下来，让我们使用 `map` 辅助函数在我们的组件中访问获取器。请看以下代码片段：

```js
...mapGetters({
  // map `this.getPhotoByID` to
  // `this.$store.getters.getPhotoByID`
  getPhotoByID: 'getPhotoByID'
})
```

Vuex 获取器是操作和管理 Vuex 状态的绝佳方式，在它们发送到我们的组件之前非常有用，并且对于过滤、排序、更新和从 Vuex 状态中删除记录都很有帮助。

接下来，我们将讨论 **Vuex 突变**以及如何在企业级应用程序中使用它们。

## Vuex 突变（mutations）

更改 Vuex 状态的唯一方法是提交一个突变（mutation）。

Vuex 突变类似于事件。它包含一个名为 `type` 的字符串和一个名为 `handler` 的函数。`handler` 函数是执行突变的地方。

假设我们仍在使用我们的 *Pinterest* 照片存储，我们可以使用以下代码片段向我们的状态中添加一个新的 `Photo` 对象：

```js
const store = createStore({
  state: {
    photos: []
  },
  mutations: {
    ADD_NEW_PHOTO (state, photo) {
      // mutate state
      state.photos.push(photo)
    }
  }
})
```

接下来，我们将查看访问 Vuex 突变模块。我们可以使用 Vuex 映射辅助工具来访问它，这是企业项目的推荐方式：

```js
import { mapMutations } from 'vuex'
export default {
  methods: {
    ...mapMutations({
      addPhoto: 'photos/ADD_NEW_PHOTO'
    })
  }
}
```

最后，我们可以在组件的任何地方调用 `addPhoto()` 方法，将 `Photo` 对象作为唯一参数传递，让 Vuex 做它的事情。

此外，使用 Vuex 突变的最佳位置是在 **Vuex 动作** 中。在下一节中，我们将详细讨论 Vuex 动作并展示它们如何在企业应用程序中使用。

## Vuex 动作

Vuex 动作类似于 Vuex 突变，但它们是异步的，主要用于提交 Vuex 突变。

Vuex 动作可以向我们的后端服务器发出 API 调用，并使用 Vuex 突变将响应提交到 Vuex 状态。

传统上，要使用 Vuex 动作进行 API 调用，我们将在存储中直接进行，如下代码片段所示。

使用我们的 *Pinterest* 照片示例，我们将有一个类似这样的存储：

```js
const store = createStore({
  state: {
    photos: []
  },
  mutations: {
    ADD_NEW_PHOTO (state, photo) {
      state.photos.push(photo)
    }
  },
  actions: {
   async getPhoto (context, id) {
       const photo = await Axios.get('/photos/'+id);
       context.commit('ADD_NEW_PHOTO', photo)
    }
  }
})
```

接下来，为了在我们的组件中分发动作，我们将继续使用 Vuex 映射来分发动作并检索与传递到 `getPhoto()` 方法的 ID 对应的新照片：

```js
import { mapActions } from 'vuex'
export default {
  methods: {
    ...mapActions({
      getPhoto: 'photos/getPhoto' // map `this.getPhoto()`
      // to `this.$store.dispatch('photos/getPhoto')`
    })
  }
}
```

到目前为止，我们已经详细介绍了使用 Vuex 构建大规模应用程序的内容，并阐明了 Vuex 模块、状态、获取器、突变和动作以及如何在构建企业级应用程序中应用它们。

为了进一步解决结构问题，我们将向您介绍在安排 Vuex 存储时使用 **仓库模式** 的方法，将所有 API 调用结构化到一个仓库中，并在 Vuex 动作中访问它们。

# 使用仓库模式进行结构化

当构建一个大规模、企业级 Vue 应用程序时，你必须从项目结构开始就做对。

根据应用程序的功能将 Vuex 存储分离成单独的模块是很好的，它提供了直接访问文件，使得调试和维护变得容易。

单独使用这种方法会带来问题。您的 Vuex 动作会变得非常大，包含许多 API 调用，提取 API 数据和处理错误都在 Vuex 动作中发生。

介绍仓库模式有助于消除庞大的代码库，并将 API 调用和管理从 Vuex 中分离出来。

在本节中，我们将首先概述仓库模式。然后，我们将为我们的 Vue 应用程序创建一个仓库文件夹。

首先，在我们探讨如何在 Vuex 中使用仓库模式之前，让我们先对仓库模式有一个清晰的概述以及它能实现什么。

## 仓库模式概述

仓库模式是在创建企业级应用程序时使用的一个重要模式，无论是任何企业应用程序的前端还是后端。

它限制了我们直接在应用程序中处理数据，并为数据库操作、业务逻辑和应用程序 UI 创建了一个新的层级。

以下是一些你应该在你的前端开发中使用仓库模式的原因列表，尤其是在构建企业应用程序时：

+   数据访问代码在整个项目中任何地方都是可重用的

+   实现领域逻辑非常容易

+   你可以快速对业务逻辑进行单元测试，而无需任何形式的紧密耦合。

+   它有助于解耦业务逻辑和应用程序 UI

**依赖注入**（**DI**）在编写可测试的企业代码时很好，而仓库模式可以帮助你在前端项目中实现 DI。

DI

DI 是一种编程技术，它使一个类与其依赖项独立。

在仓库模式中，你通过隐藏如何在 Vuex 存储中检索和处理数据来编写封装的代码库。

要实现仓库模式，我们将遵循我写的关于“在 Vue.js 中使用仓库模式消费 API”的文章（[`medium.com/backenders-club/consuming-apis-using-the-repository-pattern-in-vue-js-e64671b27b09`](https://medium.com/backenders-club/consuming-apis-using-the-repository-pattern-in-vue-js-e64671b27b09)）。

要使用仓库模式在 Vue.js 中消费后端 API，让我们通过一个示例来展示如何做到这一点。假设我们有一个 Vuex 存储操作执行不同的 API 调用，如下面的代码片段所示，我们想在它上面实现仓库模式：

```js
  actions: {
    async all({ commit }, { size = 20, page = 0 }) {
      const response = await
          Axios.get(`/photos?size=${size}&page=${page}`);
      const { data } = response;
      if (data.success) {
        commit("STORE_PHOTOS", data.photos);
      } else {
        commit("STORE_ERROR", data.message);
      }
    },
    async getPhoto({ commit }, id) {
      const response = await Axios.get('/photos/'+id);
      const { data } = response;
      if (data.success) {
        commit("STORE_PHOTO", data.photo);
      } else {
        commit("STORE_ERROR", data.message);
      }
    },
  },
```

现在，我们可以按照接下来的步骤通过实现仓库模式来改进 Vuex 存储。

# 创建仓库文件夹

首先，通过运行以下命令在根目录或`src`文件夹中创建一个文件夹：

```js
mkdir repositories
```

我们将称之为`repositories`。这个文件夹将包含所有你的仓库和 HTTP 客户端配置。

## 创建客户文件夹

我们将在新创建的`repositories`文件夹内创建一个`Clients`文件夹。这个文件夹里将包含所有使用的 HTTP 客户端。

有时，由于项目的性质，一些项目可能需要多个 HTTP 客户端来执行 API 调用，原因各不相同。其中一个可以作为默认连接失败时的后备。

因此，创建一个`Clients`文件夹对于一次性配置所有 HTTP 客户端至关重要。运行以下命令来创建一个：

```js
cd repositories && mkdir Clients
```

## 创建 xxxClient.js 类

你可以创建一个与所使用的 HTTP 客户端对应的类文件。命名是主观的，对于`AxiosClient.js`文件，将其放入所有默认配置中。

Axios

Axios 是一个基于 Promise 的 HTTP 客户端，适用于 Node.js 和浏览器。它可以在浏览器和 Node.js 上使用相同的代码库运行。

运行以下命令来创建文件夹：

```js
touch AxiosClient.js
```

总结来说，你可能需要使用许多 HTTP 客户端，因此为每个客户端创建不同的`xxxClient.js`文件，并为其指定特定的配置。

对于 Axios，以下是我为这次测试设置的默认配置：

```js
import axios from "axios";
const baseDomain = "https://localhost:1337"; //For Strapi
const baseURL = `${baseDomain}`; // Incase of /api/v1;
// ALL DEFAULT CONFIGURATION HERE
export default axios.create({
  baseURL,
  headers: {
    // "Authorization": "Bearer xxxxx"
  }
});
```

你可以在前面的文件中为 Axios 添加更多默认配置，并导出 Axios 实例。

## 创建一个单独的仓库类

接下来，我们将根据我们在企业应用程序中的功能数量创建一个单独的仓库。

例如，我们正在构建一个*Pinterest*克隆应用程序，我们确信该应用程序将具有**照片**和**用户**功能。因此，我们可以通过运行以下命令开始为提到的功能创建仓库：

```js
cd repositories && touch PhotoRepository.js UserRepository.js
```

这些仓库将包含所有针对单个功能的 API 调用。我们将首先为相应的仓库创建一个**创建、读取、更新**和**删除**（**CRUD**）操作，以获得概述。相比之下，我们将随着本书的进展更新仓库。

打开`PhotoRepository.js`文件并添加以下脚本：

```js
import Axios from './Clients/AxiosClient';
const resource = '/photos;
export default {
    get() {
        return Axios.get(`${resource}`);
    },
    getPhoto(id) {
        return Axios.get(`${resource}/${id}`);
    },
    create(payload) {
        return Axios.post(`${resource}`, payload);
    },
    update(payload, id) {
        return Axios.put(`${resource}/${id}`, payload);
    },
    delete(id) {
        return Axios.delete(`${resource}/${id}`)
    },
    //b MANY OTHER RELATED ENDPOINTS.
};
```

接下来，我们将打开`UserRespository.js`文件并添加以下脚本：

```js
import Axios from './Clients/AxiosClient';
const resource = '/users;
export default {
    get() {
        return Axios.get(`${resource}`);
    },
    getUser(id) {
        return Axios.get(`${resource}/${id}`);
    },
    create(payload) {
        return Axios.post(`${resource}`, payload);
    },
    update(payload, id) {
        return Axios.put(`${resource}/${id}`, payload);
    },
    delete(id) {
        return Axios.delete(`${resource}/${id}`)
    },
    //b MANY OTHER RELATED ENDPOINTS.
};
```

我们为我们的*Pinterest*克隆应用程序创建了两个仓库，任何与 API 相关的代码都将放入单独的仓库中。

## 创建一个`RepositoryFactory.js`类

在`repositories`文件夹内通过运行以下命令创建一个`RepositoryFactory`工厂类，以导出您可能创建的所有不同单独的仓库，以便在应用程序的任何地方轻松使用：

```js
touch RepositoryFactory.js
```

完成后，粘贴以下代码：

```js
import PhotoRepository from './PhotoRepository';
import UserRepository from './UserRepository';
const repositories = {
    'Photos': PhotoRepository,
    'Users': UserRepository
}
export default {
    get: name => repositories[name]
};
```

现在我们通过创建仓库改进了我们的 Vuex 存储，让我们看看如何在下一节中使用这些仓库。

## 使用仓库模式

让我们看看如何在我们的 Vuex 存储中利用我们创建的仓库。打开您之前创建的 Vuex 存储`photos`文件，并将`getPhoto`动作方法替换为以下代码以利用仓库模式：

```js
import Repository from "@/repositories/RepositoryFactory";
const Photos = Repository.get("Photos");
const Users = Repository.get("Users");
actions: {
   async getPhoto (context, id) {
     const photo = await Photos.getPhoto(id);
     context.commit('ADD_NEW_PHOTO', photo)
    }
   async getUsers(context) {
     const users = await Users.get();
     context.commit('ADD_USERS', users)
    }
  }
```

使用仓库模式消除了处理错误、在 Vuex 存储中操作从 API 检索的数据以及只返回 Vuex 中实际需要的数据的需求。这种方法还利用了软件工程中的**不要重复自己**（**DRY**）原则，因为可以通过创建一个新的来在整个项目中使用仓库。

结构化并不随着您使用仓库模式整理好 HTTP API 调用而结束。它还扩展到您安排导航的方式。导航文件不应该包含大量难以理解的代码库。

在下一节中，我们将使用 Vue Router 来安排和结构化我们的导航，以确保在我们面向企业的项目中保持可维护性和可扩展性。

# 使用 Vue Router 构建 Vue 导航

当构建一个面向企业的应用程序时，很明显导航系统将会很大，因为会有很多导航、路由和页面。

本节将向您展示如何在企业项目中正确地构建 Vue Router。为了实现这一点，我们将使用*按功能分割*的方法来组织 Vue Router，使其易于导航，就像我们在本章早期使用 Vuex 时所做的那样。

这种方法将创建一个结构，其中公共和私有路由将被分离，并且还可以单独分离更多路由。

## 文件夹结构

该文件夹将包含一个 index 文件、一个公共文件和一个私有文件，其中包含每个类别的所有路由。

在您的 `src` 文件夹根目录下创建一个 `router` 文件夹，并在文件夹内通过在终端中依次输入以下命令创建以下文件：

```js
cd src && mkdir router
touch index.js
mkdir routes && cd routes
touch public.js private.js combine.js
```

当前文件夹结构相当简单，随着本书的进展，我们将对其进行更多定制。以下是每个文件将包含的内容：

+   `index.js`：此文件将包含 `beforeEach` 逻辑和所有其他路由的组装

+   `public.js`：此文件将包含所有不需要限制的面向公共的路由，例如登录页面、注册页面等

+   `private.js`：此文件将包含所有用于认证用户和许多限制以及元数据的私有路由

+   `combine.js`：此文件将合并私有和公共文件，并使其易于与主路由文件连接

接下来，让我们创建一个 index 文件来包含我们新创建项目的设置。

## index.js 文件

index 文件是核心。打开 `index.js` 文件，添加以下代码以导出在公共和私有文件中创建的所有路由：

```js
import { createRouter, createWebHistory } from "vue-router";
import routes from '@/router/routes/combine.js'
const routes = [
    {
      path: '/',
      redirect: '/'
    }
  ].concat(routes)
const router = createRouter({ history: createWebHistory(), routes });
// ....
// BeforeEach code here
//.....
export default router
```

在 Vue Router 中，当使用 Vue Router 开发 Vue 应用时，主要使用两种不同的历史模式：

+   Hash 模式

+   HTML5 模式

## Hash 模式

这种模式在 URL 前使用 `#`（哈希）符号来模拟完整 URL，这样当 URL 发生变化时页面不会重新加载。这是因为哈希符号后的页面或部分永远不会发送到服务器。这意味着它不会影响页面的 SEO，但这是 Vue Router 的默认设置。

## HTML5 模式

如前例所示，这是使用 `createWebHistory()` 函数创建的，并且是适用于企业和生产级应用的推荐方法。它需要在服务器上进行一些繁琐的配置才能正常工作。

## combine.js 文件

这是一个单一的实用文件，将所有路由合并到一个文件中，以便导出到主路由文件。打开文件并添加以下代码：

```js
import publicRoutes from '@/router/routes/public.js'
import privateRoutes from '@/router/routes/private.js'
export default publicRoutes.concat(privateRoutes)
```

在将路由添加到工具文件后，我们将如下一节所示将它们导入到 `main.js` 文件中。

## 将路由添加到 Vue

最后，我们将把我们的路由添加到 Vue 实例中，如下一节所示。打开 `main.js` 文件，添加以下代码：

```js
import { createApp } from "vue"
import App from "./App.vue"
import router from "./router/index.js"
import store from "./store"
createApp(App).use(router).use(store).mount("#app")
```

随着我们在本书中的进展，我们将重新访问 `public.js` 和 `private.js` 文件，根据我们开发的 *Pinterest* 克隆应用程序添加更多路由。

# 摘要

本章首先探讨了开发具有 Vue.js 3 的可扩展性和企业级应用程序的不同库。我们详细讨论了各个库及其不同的功能，以加深我们对构建可扩展和企业级应用程序的理解。我们还讨论了 Vuex 的基本要素，讨论了如何通过按功能拆分 Vuex 动作、模块、获取器和状态来构建我们的大规模 Vuex 存储库。

接下来，我们通过使用仓库模式将大型 Vuex 动作拆分为单独的仓库，并使我们的企业应用程序易于维护，讨论了**关注点分离**（**SoC**）的基本要素。仓库模式在创建企业应用程序中至关重要，我们展示了如何在 Vue 3 中实现它。

最后，我们讨论了如何构建 Vue Router，以避免在项目变得更大时出现臃肿和庞大的路由文件，因为那时将难以维护。我们讨论了将 Vue Router 文件拆分为不同文件的战略模式，以实现可维护性和易于调试。

在下一章中，我们将更深入地探讨企业 Vue 3 应用程序的性能扩展。我们将探索不同的性能和可扩展性技巧，以构建一个企业级 Vue 3 应用程序，例如异步组件的加载/懒加载、摇树优化、图像压缩等。你将学习如何通过在下一章中应用技巧来适当提高你的 Vue 3 应用程序的性能，以开发具有可维护性和可扩展性的大规模应用程序。

# 第二部分：大规模应用程序和 Vue.js 3 中的性能扩展

在本部分，你将学习构建大规模应用程序的最佳实践，其中可扩展性和性能是首要考虑的因素。此外，你还将学习和探索不同的技术，以扩展大型应用程序的性能。

本部分包括以下章节：

+   *第三章*，[*Vue.js 3 中的性能扩展*](https://epic.packtpub.services/index.php?module=oss_Chapters&action=DetailView&record=e17b2903-7123-3af0-d559-61d6c930c89c)

+   *第四章*，[*大规模 Web 应用程序的架构*](https://epic.packtpub.services/index.php?module=oss_Chapters&action=DetailView&record=2a7a717b-e1bb-58f4-e12f-61d6c9e83477) 应用程序
