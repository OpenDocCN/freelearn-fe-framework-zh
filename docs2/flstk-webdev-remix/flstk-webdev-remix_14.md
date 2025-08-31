# 14

# 实时与 Remix

网络平台提供了发送实时数据的标准和能力。通过实时技术，我们可以实现聊天功能和多人 UI，以实现实时协作和交互。我们已经看到几个具有实时功能的 App 在受欢迎程度上增长，并重新定义了它们的产品类别，例如 Google Docs 和 Figma。在本章中，我们将学习使用 Remix 的实时 UI。

本章分为两个部分：

+   与实时技术一起工作

+   使用 Remix 构建实时 UI

首先，我们将比较实时技术并讨论托管提供商和服务器环境的要求。接下来，我们将概述使用 Remix 的实现。最后，我们将通过利用**服务器发送事件**（**SSE**）在 BeeRich 中实现一个简单的实时 UI。

阅读本章后，您将了解在 Remix 中与实时技术一起工作的要求。您还将能够命名轮询、SSE 和 WebSocket 之间的区别。最后，您将了解如何在 Remix 中使用 SSE。

# 技术要求

您可以在此处找到本章的代码：[`github.com/PacktPublishing/Full-Stack-Web-Development-with-Remix/tree/main/14-real-time-with-remix`](https://github.com/PacktPublishing/Full-Stack-Web-Development-with-Remix/tree/main/14-real-time-with-remix)。本章不需要进行额外的设置。

# 与实时技术一起工作

网络平台为实时通信提供了不同的协议和标准。在本节中，我们将回顾不同的技术和方法，并讨论使用 Remix 利用它们的要求。我们将讨论轮询，了解 SSE，并回顾 WebSocket API。首先，让我们看看轮询技术。

## 理解轮询

轮询是一种客户端拉取技术，其中客户端从服务器请求数据。而不是依赖服务器推送更新，轮询通过间隔来检查服务器上的最新数据。

我们可以区分短轮询和长轮询。短轮询在基于时间的间隔内向服务器发送请求。服务器立即响应，要么提供新数据，要么表示没有变化。长轮询中，服务器只有在有新数据可用时才响应，在此期间保持请求未回答。一旦服务器响应或请求超时，客户端就会发送新的请求。

轮询的优势在于它不需要服务器环境和托管提供商支持 WebSocket、HTTP/2 或长时间运行的流式响应。当与不支持实时协议和标准的服务器环境和托管提供商一起工作时，轮询可以是一个很好的折衷方案。它也更容易实现，并且可能是一个很好的原型设计工具。

轮询的缺点是资源消耗浪费和延迟的实时行为。短轮询会产生许多不必要的请求，而长轮询则迫使服务器处理空闲请求，直到接收到新数据。短轮询还可能基于间隔重试时间延迟实时更新。

接下来，让我们看看 SSE。

## 理解 SSE

SSE 是一种基于 HTTP 的服务器推送标准，是 HTML5 规范的一部分。SSE 需要客户端和服务器。客户端使用 `EventSource` API 请求连接；服务器实现一个端点，返回带有 `text/event-stream` 媒体类型的流式响应。

流式响应从服务器到客户端创建了一条单向通信线路。这允许服务器向客户端发送事件，而无需客户端使用轮询。

SSE 的优势在于减少了资源消耗。客户端不需要向服务器发送不必要的请求。相反，服务器只在有更新可用时向客户端发送事件。

SSE 的缺点是长时间运行的 HTTP 连接，需要服务器维护。此外，SSE 只提供单向通信线路。客户端无法向服务器发送消息。最后，HTTP/1 只允许服务器同时维护六个并发连接。幸运的是，大多数服务器环境支持 HTTP/2，但 HTTP/1 的限制可能仍然相关，具体取决于您的托管提供商。

## 理解 WebSocket

WebSocket 是一种通过 Web 的 WebSocket API 实现的通信协议，它创建了一个持久的双向通信线路。与 SSE 和轮询解决方案不同，WebSocket 直接在 TCP 上操作，而不是 HTTP。

一旦建立了 WebSocket 连接，双方（例如，浏览器和服务器）可以同时发送和接收消息。由于该协议在 TCP 上运行，它可以传输不仅 UTF-8 编码的数据，还可以传输二进制数据，使其成为一种性能强大的低级协议。

WebSocket 连接的优势在于其双向通信通道和直接使用 TCP 的性能提升。然而，WebSocket 连接并不被所有托管提供商和 JavaScript 运行时支持，因为它们需要长时间运行的服务器。WebSocket API 也是最复杂实现和利用的，需要设置 WebSocket 服务器。

这三种技术使我们能够实现实时功能。轮询允许我们构建多人 UI，即使我们的服务器环境不支持流式响应或设置 WebSocket 服务器。SSE 为服务器向客户端发送事件和数据提供了一种更简单的方式。WebSocket API 是一种低级协议，允许我们创建双向通信通道，从而创建性能强大且可扩展的多人 UI。

现在您已经了解了轮询、SSE 和 WebSocket API 之间的区别，我们可以在 BeeRich 中实现实时 UI。在下一节中，我们将这样做。

# 使用 Remix 构建实时 UI

BeeRich 使用 Remix 的 Express.js 适配器，并在一个长时间运行的服务器上运行。因此，BeeRich 可以利用轮询、SSE 和 WebSocket API 来实现实时功能。

短轮询设置简单。我们可以在 Remix 中通过使用 Remix 的 `useRevalidator` 钩子来实现短轮询：

```js
import { useEffect } from 'react';import { useRevalidator } from '@remix-run/react';
function Component() {
  const { revalidate } = useRevalidator();
  useEffect(() => {
    const interval = setInterval(revalidate, 4000);
    return () => {
      clearInterval(interval);
    };
  }, [revalidate]);
}
```

`useRevalidator` 钩子的 `revalidate` 函数会触发 `loader` 的重新验证。这允许我们重新获取所有 loader 数据，类似于 Remix 在执行 `action` 函数后重新获取所有 loader 数据的方式。

由于 WebSocket 协议是基于 TCP 的，我们需要在 Remix 应用程序之外使用项目根目录下的 `server.js` 文件或使用完全不同的服务器环境来创建 WebSocket 服务器和端点。这是可行的，但超出了本书的范围。相反，我们将回顾如何使用 SSE 与 Remix 一起使用。

Remix 的 `loader` 和 `action` 函数可以创建基于 HTTP 的资源路由。我们可以在长时间运行的服务器环境中通过使用 `loader` 函数返回带有 `text/event-stream` 媒体类型的流响应来实现 SSE 端点。

我们的目标是通知所有使用相同用户登录的设备和打开的浏览器标签页，告知支出和发票数据的变化。我们还希望在检测到此类更改时重新验证 UI。让我们开始吧：

1.  首先，在 `app/modules/` 中创建一个新的 `server-sent-events` 文件夹。

1.  接下来，在新建的文件夹中创建一个 `events.server.ts` 文件，并添加以下代码：

    ```js
    import { EventEmitter } from 'events';declare global {  // eslint-disable-next-line no-var  var emitter: EventEmitter;}global.emitter = global.emitter || new EventEmitter();export const emitter = global.emitter;
    ```

    我们使用 Node.js 的 `EventEmitter` API 并为我们的服务器环境声明一个全局可访问的事件发射器。`EventEmitter` 对象可用于监听和发出事件。我们将在 `action` 函数中使用 `emit` 函数将数据更改通知给服务器发送事件连接处理代码。

    注意，`EventEmitter` API 与 SSE 没有关系，但它为我们的 Node.js 服务器环境提供了一种基于事件的通信的便捷方式。

1.  现在，在 `events.server.ts` 中实现一个 `eventStream` 辅助函数：

    ```js
    export type SendEvent = (event: string, data: string) => void;export type OnSetup = (send: SendEvent) => OnClose;export type OnClose = () => void;export function eventStream(request: Request, onSetup: OnSetup) {  eventStream function creates a new ReadableStream object. The stream object contains a start function. In start, we define the send function, which is responsible for adding events to the stream that will be sent to the client. The code also includes logic to correctly close the stream. Finally, the function returns an event stream Response using the ReadableStream object as the response body.
    ```

1.  接下来，实现负责提供事件流响应的端点。在 `/routes` 文件夹中创建一个新的 `/sse.tsx` 路由：

    ```js
    import type { LoaderFunctionArgs } from '@remix-run/node';import type { OnSetup } from '~/modules/server-sent-events/events.server';import { emitter, eventStream } from '~/modules/server-sent-events/events.server';import { requireUserId } from '~/modules/session/session.server';export async function loader({ request }: LoaderFunctionArgs) {  requireUserId). Next, we implement a helper function that we will pass to eventStream. This helper function uses our EventEmitter object to listen to events on the server. emitter listens for events that match the userId property of the authenticated user and triggers a new sever-sent event using the event stream once such event is received.
    ```

1.  接下来，将全局 `emitter` 对象添加到所有支出和发票 `action` 函数中。每当操作成功时，我们希望在服务器上发出 `server-change` 事件并触发对所有连接客户端的新事件：

    ```js
    emitter.emit(userId);
    ```

1.  例如，在 `dashboard.expenses._index.tsx` 路由模块的 `action` 函数中，在返回重定向之前添加事件发射器调用。这确保了事件仅在操作成功且数据库已更新后发出：

    ```js
    emitter object on the global object and can access it on the server without importing it. However, you can also import it if you like:

    ```

    import { emitter } from '~/modules/server-sent-events/events.server';

    ```js

    ```

1.  在将`emit`函数调用添加到费用和发票创建操作之后，将`emit`函数调用添加到`dashboard.expenses.$id._index.tsx`路由模块中的`handleDelete`、`handleUpdate`和`handleRemoveAttachment`函数。再次强调，在数据修改成功后调用`emit`以避免竞争条件。

1.  确保你也将相同的更改应用到发票的`action`函数中。你总是可以在本章的解决方案文件夹中查看最终的实现。

1.  让我们把注意力转向客户端环境。在`app/modules/server-sent-events`文件夹中添加一个新的`event-source.tsx`文件，并实现事件流连接请求：

    ```js
    import { revalidate function to revalidate all loader data once a server-sent event is received. The hook uses the EventSource API to connect to the /sse route, where we implemented our event stream loader function. The hook then adds an event listener to listen for server-change events – an arbitrary event name we specified in the loader code.
    ```

1.  最后，在`dashboard.tsx`路由模块中导入新的钩子，并在路由模块组件中调用该钩子：

    ```js
    import { EventSource API and SSE standard.Whenever the user navigates to a dashboard route, we now initiate a request to the `/sse` endpoint. The endpoint authenticates the user and returns a streaming response. The server further listens for events from `action` functions using the `EventEmitter` API. Once the same user calls an `action` function (for example, by submitting a form), the `action` function emits an event that is then handled by the `loader` code of the streaming response. The `handler` function is executed on the server and sends a `server-change` event to all connected clients of the same user. The clients receive the event and initiate a loader revalidation.
    ```

1.  在终端中通过调用`npm run dev`来本地运行应用程序。

1.  通过在两个或更多标签页中打开 BeeRich 来测试实现。你还可以在几个浏览器中运行 BeeRich 以调查实时行为。以相同用户身份登录并更新和删除发票和费用。你能看到费用历史记录随着每次实时变化而增长吗？你能看到不同窗口和标签页中的 UI 更新吗？

干得好！就这样，我们可以在 Remix 中实现实时 UI。然而，当前的实现有一个显著的问题：调用`action`函数的客户端会重新验证其 UI 两次——一次是使用 Remix 的内置重新验证步骤，另一次是在接收到服务器发送的事件后。这给服务器和用户的网络带宽带来了额外的负担。你有没有想法如何避免双重重新验证？

考虑自己实现它以练习在 Remix 中使用 SSE！也许你可以使用一个唯一的连接标识符来避免在修改数据的浏览器标签页中重新验证服务器发送的事件。你可以在服务器发送事件的有效负载中添加该标识符，并与客户端上存储的本地版本进行比较。或者，你可以使用 Remix 的`shouldRevalidate`路由模块 API 来避免在触发服务器发送事件的`action`函数调用后 Remix 内置的重新验证。有关`shouldRevalidate`函数的更多信息，请参阅 Remix 文档：[`remix.run/docs/en/2/route/should-revalidate#shouldrevalidate`](https://remix.run/docs/en/2/route/should-revalidate#shouldrevalidate)。

在本节中，你在 BeeRich 中实现了一个 SSE 端点，以便在用户在不同标签页、浏览器和设备上的`action`函数中修改数据时重新验证加载器数据。

# 摘要

在本章中，你学习了关于实时技术和技巧，以及如何在 Remix 中使用它们。

首先，我们讨论了轮询、SSE 和 WebSocket API，并比较了它们的优缺点。轮询最容易设置。简单的轮询实现不需要在服务器上进行更改。SSE 使用 HTTP 协议提供单向通信线路，而 WebSocket 连接使用 TCP，是双向的。

其次，你了解了 SSE 和 WebSocket 的服务器要求。你现在明白 SSE 需要支持流式响应，而 WebSocket 服务器只能在长时间运行的服务器上运行。

最后，我们在 BeeRich 中通过利用 SEE 实现了一个实时用户界面。我们使用 `EventSource` API 实现了一个新的端点和相关的 React 钩子。由于 Remix 的 `loader` 函数返回 HTTP `Response` 对象，我们可以使用资源路由在 Remix 中实现服务器端发送事件端点。

在下一章中，我们将学习更多关于会话管理的内容，并讨论 Remix 的高级会话管理模式。

# 进一步阅读

你可以在 MDN Web 文档中了解更多关于 SSE 和 WebSocket 的信息：

+   [`developer.mozilla.org/en-US/docs/Web/API/Server-sent_events`](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events)

+   [`developer.mozilla.org/en-US/docs/Web/API/Websockets_API`](https://developer.mozilla.org/en-US/docs/Web/API/Websockets_API)

这里是 Remix Conf 2023 上关于 SSE 的一个精彩演讲，由 Alex Anderson 演讲：[`www.youtube.com/watch?v=cAYHw_dP-Lc`](https://www.youtube.com/watch?v=cAYHw_dP-Lc).

Sergio Xalambrí 撰写了一篇关于如何使用 socket.io 配置 Remix 来创建 WebSocket 连接的文章：[`sergiodxa.com/articles/use-remix-with-socket-io`](https://sergiodxa.com/articles/use-remix-with-socket-io).
