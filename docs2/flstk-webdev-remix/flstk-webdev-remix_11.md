# 11

# 乐观 UI

乐观 UI 通过提供即时反馈，即使在操作需要一点时间的情况下也能让您的应用感觉更加敏捷。这在等待网络响应时尤其有用。乐观更新可以使 UI 感觉更加响应迅速，并改善用户体验。在本章中，您将学习如何使用 Remix 添加乐观 UI 更新。

本章分为两个部分：

+   考虑乐观 UI

+   在 Remix 中添加乐观 UI 更新

首先，我们将讨论使用乐观 UI 更新的权衡，并调查客户端/服务器状态同步和回滚的复杂性和风险。接下来，我们将回顾 BeeRich 的当前状态，并调查哪些突变可以通过乐观 UI 更新来增强。然后，我们将使用 Remix 的原语在合适的地方添加乐观 UI 更新。

阅读本章后，您将了解如何评估乐观 UI 的使用。您还将练习使用 Remix 的原语，如 `useNavigation` 和 `useFetcher`，来实现乐观 UI。最后，您将理解 Remix 如何通过提供一个有弹性的基线来简化乐观 UI 的实现。

# 技术要求

您可以在此处找到本章的代码：[`github.com/PacktPublishing/Full-Stack-Web-Development-with-Remix/tree/main/11-optimistic-ui`](https://github.com/PacktPublishing/Full-Stack-Web-Development-with-Remix/tree/main/11-optimistic-ui)。

BeeRich 已经成长了很多。现在是重构代码的好时机。在开始本章之前，我们希望更新当前代码。我们还将使用 `zod` 增强我们的表单验证和解析。按照 `README.md` 文件中的逐步指南准备 BeeRich 以迎接即将到来的高级主题。

# 考虑乐观 UI

网络应用程序的真相来源通常存储在远程数据库中。我们只能在更新数据库并从服务器收到确认后才能确定突变是否成功。因此，UI 对突变的响应将被延迟，直到我们从服务器得到回复。

乐观 UI 是一种用于在等待执行解决时向用户提供即时反馈的模式。在乐观地更新 UI 时，我们在收到来自服务器的最终响应之前就应用 UI 更新。大多数时候，我们的突变都成功了，那么为什么还要等待服务器响应呢？在本节中，我们将讨论乐观 UI 更新的某些权衡。

## 传达回滚

乐观地更新 UI 可以加快当乐观状态与服务器响应一致时的感知响应时间。当乐观更新与服务器响应不一致时，则需要回滚或纠正乐观更新。这就是乐观 UI 模式开始变得复杂的地方。

当乐观突变出现问题时，我们必须向用户传达错误并突出显示回滚。否则，我们可能会失去用户对我们应用程序的信任和信心。例如，在尝试删除一个项目后，我们可能需要回滚到乐观删除该项目，并告诉用户为什么项目再次出现——“*我刚刚删除了那个项目；为什么* *它又回来了*？”

在考虑乐观 UI 时，调查突变的错误率是一个好主意。如果错误率很高，那么回滚的数量可能会比增加的响应时间更有害于用户体验。像往常一样，这取决于用例、应用程序类型和用户。

我们可以总结说，随着回滚操作需要正确沟通，使用乐观 UI 的错误处理变得更加难以实现。此外，乐观 UI 还需要重新同步客户端和服务器状态，导致客户端代码更加复杂。

## 同步客户端和服务器状态

乐观 UI 的最大风险之一是在 UI 中引入过时状态。在应用乐观更新时，与服务器响应一致地同步 UI 状态可能变得相当具有挑战性。结果逻辑可能很复杂，并可能导致应用程序 UI 的一部分与服务器状态不同步的 bug。

当添加乐观更新时，我们可能允许用户连续提交几个更新。我们每次都乐观地更新 UI。然后，我们必须处理 UI 与服务器响应的同步。当几个更新同时发生时，这可能会导致竞争条件和其他需要彻底同步逻辑和错误处理的难题。

乐观 UI 更新是可选的。如果正确实现，它们可能会通过加快感知响应时间来改善用户体验。然而，也存在风险，即如果未彻底实现，乐观 UI 更新可能会不成比例地增加我们应用程序状态管理的复杂性，并降低用户体验。

如果回滚操作没有正确沟通，乐观 UI 更新可能会导致状态过时、复杂的客户端-服务器状态同步逻辑，以及更差的用户体验。总之，我们必须谨慎评估某个突变是否因添加乐观 UI 更新而受益，或者它是否会不成比例地增加复杂性。

幸运的是，Remix 为实施乐观 UI 更新提供了一个很好的基础，并允许我们通过对我们现有的挂起 UI 进行增量更改来实现乐观 UI 更新。让我们再次提醒自己 Remix 的`loader`重新验证功能。

## 在 Remix 中同步客户端和服务器状态

Remix 通过提供数据重新验证流程，开箱即用地管理了乐观 UI 的复杂性。在深入代码之前，让我们快速回顾一下 Remix 内置的`loader`重新验证功能。

无论何时我们在 Remix 中提交表单并执行 `action` 函数，Remix 都会自动从所有活动的 `loader` 函数重新获取数据。这确保了在每次数据突变后，我们总是更新页面上的所有数据。

当使用 Remix 的 `loader` 和 `action` 函数进行数据读取和写入时，我们避免了在 UI 中引入过时数据，并消除了在实现乐观用户界面更新时降低用户体验的主要担忧。

此外，Remix 的原语，如 `useNavigation` 和 `useFetcher`，允许我们在不添加自定义 React 状态的情况下读取挂起的提交数据，这有助于将添加乐观用户界面时的复杂性保持在较低水平。让我们通过向 BeeRich 添加乐观用户界面来看看这一点。首先，让我们回顾 BeeRich 应用程序中的当前突变，并调查添加乐观用户界面是否会改善用户体验。

# 在 Remix 中添加乐观用户界面更新

在本节中，我们将回顾我们的 BeeRich 应用程序，并讨论哪些用户操作通过添加乐观的用户界面更新将获得最大的收益。然后，我们将继续进行必要的代码更改。

## 创建费用

通过在项目的根目录中执行 `npm run dev` 来在本地运行 BeeRich，并导航到费用概览页面（[`localhost:3000/dashboard/expenses`](http://localhost:3000/dashboard/expenses)）。现在，创建一个新的费用。

注意，在提交费用创建表单后，我们将被重定向到费用详情页面。现在，URL 包含新的费用标识符。在重定向后，我们可以访问新创建的费用加载器数据，包括费用标识符。所有对费用的进一步更新都需要费用标识符。

在费用创建表单中添加乐观的用户界面更新可能会变得相当复杂。实现这一目标的一种方法是在将用户重定向到实际详情页面之前，乐观地更新创建表单的外观和感觉，使其看起来像费用更新表单。然而，在我们从费用创建提交中接收到费用 `id` 参数之前，我们无法执行任何后续的费用更新提交。我们可以禁用所有提交按钮，直到我们收到服务器响应，或者我们可以排队后续提交，并使用用户尝试提交的最新更改以编程方式提交更新。这可能会变得相当复杂。

当考虑附件逻辑时，事情变得更加复杂。如果我们仍在等待 `id` 参数，而用户想要删除附加文件或尝试上传新的附件怎么办？我们可以通过禁用附件操作直到我们从服务器获取费用 `id` 参数来防止对附件的所有后续更改。

和往常一样，这归结为权衡。通过添加乐观更新，我们能够增加多少响应时间并提升用户体验？这是否值得增加的复杂性？由于我们的应用程序相当快，我们决定不在费用创建表单中添加乐观更新。相反，让我们继续并调查费用更新表单。

## 更新费用

导航到费用概览页面([`localhost:3000/dashboard/expenses`](http://localhost:3000/dashboard/expenses))并选择一个费用。这将带我们到费用详情页面，该页面渲染费用更新表单。现在，对现有的费用进行一些修改并点击`id`参数。相反，我们显示一个成功消息：**更改已保存**。

技术上，UI 已经显示了乐观更新，因为我们总是向用户显示最新的输入值。让我们也更新`dashboard.expenses.$id._index.tsx`路由模块：

1.  移除`disabled`属性和待处理的“保存…”UI 状态：

    ```js
    <Button type="submit" name="intent" value="update" isPrimary>  Save</Button>
    ```

    刚开始移除待处理的 UI 可能会感觉有些奇怪。让我们仔细思考一下。现在表单支持后续更新，因为我们不再在待处理提交时禁用提交按钮。由于我们总是显示用户输入的值在更新表单中，输入状态本身已经是乐观的。由于我们仍然在待处理导航时显示全局过渡动画和费用详情脉冲动画，我们仍然传达更新正在进行的信息。此外，我们还在成功更新时显示成功消息。这可能是一个不错的折衷方案。

    但附件怎么办？添加附件会创建一个新的`expense.attachment`值。我们需要附件文件名值来执行视图和删除附件操作。

    一种解决方案是在乐观地添加附件的同时禁用附件链接和删除按钮，直到我们收到包含新添加的附件值的服务器响应。让我们实现它！

1.  在`dashboard.expenses.$id._index.tsx`路由模块组件中，使用 Remix 的全局导航对象来确定附件是否正在上传：

    ```js
    const navigation = useNavigation();const attachment = formData property. This property contains the data of the currently submitted form or undefined if no submission is in progress. By checking for the name property of the attachment input value, we can verify whether a file has been appended to the expense update form.
    ```

1.  接下来，更新`Attachment`组件的条件渲染语句，以便在附件正在上传时进行渲染。此外，将`disabled`属性传递给`Attachment`组件：

    ```js
    {Attachment component when an upload is still in progress. This is an optimistic update to the UI since the upload is still in progress.By disabling the `Attachment` component’s actions if an upload is still in progress, we prevent users from viewing or removing a pending attachment.
    ```

Remix 的`useNavigation`钩子和其`formData`属性允许我们有条件地更新 UI，而无需创建额外的自定义 React 状态。这很好，因为我们完全避免了同步逻辑的需要。Remix 的`ErrorBoundary`组件进一步确保在发生错误时有一个健壮的基线。

太好了！就这样，我们在附加文件和更新费用时添加了乐观 UI 更新。现在，用户可以在不等待服务器响应的情况下进行多次更新。如果出现问题，Remix 将显示我们的`ErrorBoundary`，让用户知道错误信息。

就像往常一样，为收入路由实现相同的功能。这确保你在继续前进之前重新回顾所学的内容。

接下来，让我们调查机会删除表单，以便添加乐观的 UI 更新。

## 删除支出

在支出概览页面上，我们为每个支出渲染一个`ListLinkItem`组件。`app/components/links.tsx`中的`ListLinkItem`组件使用`useFetcher.Form`提交删除突变。在列表中乐观地删除或添加元素是提供即时反馈的好方法。让我们看看我们如何将乐观 UI 添加到我们的支出删除表单中。

在删除时实现乐观更新的方法之一是在进入挂起状态后立即隐藏列表项。按照以下方式更新`app/components/links.tsx`中的`ListLinkItem`组件：

```js
const fetcher = useFetcher();const isSubmitting = fetcher.state !== 'idle';
if (isSubmitting) {
  return null;
}
```

你已经知道`useFetcher`钩子管理其转换生命周期。当尝试使用`useFetcher.Form`实现挂起和乐观 UI 时，我们不使用`useNavigation`；相反，我们使用`useFetcher`钩子的`state`和`formData`属性。

就这样，当提交挂起时，我们从列表中删除支出项。一旦`action`函数完成，Remix 刷新加载器数据并将导航状态设置回`idle`。如果突变成功，则更新的加载器数据不再包含已删除的支出，并且我们的 UI 更新持续存在。但如果发生错误呢？

1.  查看位于`dashboard.expenses.$id._index.tsx`路由模块中的`handleDelete`函数。目前，如果删除支出失败，我们抛出一个 404`Response`。这触发了`ErrorBoundary`。让我们通过在 UI 中为用户提供直接反馈来改进这一点，如果删除操作失败。

1.  将`handleDelete`更新为在删除操作失败时返回一个 JSON`Response`：

    ```js
    try {  await deleteExpense(id, userId);} catch (err) {  ListLinkItem component in components/links.tsx.

    ```

    如果操作数据存在且成功为 false，我们知道支出的删除失败了。

    ```js

    ```

1.  最后，有条件地更新列表元素的`className`属性，以便在`hasFailed`为`true`时将列表项文本样式设置为红色。

    ```js
    className={clsx(  'w-full flex flex-row items-center border',  isActive    ? 'bg-secondary dark:bg-darkSecondary border-secondary dark:border-darkSecondary'    : 'hover:bg-backgroundPrimary dark:hover:bg-darkBackgroundPrimary border-background dark:border-darkBackground hover:border-secondary dark:hover:border-darkSecondary',ErrorBoundary and instead display error feedback right where the error happened in the UI.
    ```

1.  通过在`dashboard.expenses.$id._index.tsx`中的`handleDelete`的 try-case 内部抛出错误来测试更改：

    ```js
    try {  ErrorBoundary is a great fallback in case something goes wrong. However, sometimes, it is a good idea to enhance the user experience further by providing inline feedback. This allows the user to retry the failed action immediately, and Remix’s loader data revalidation takes care of the rest.
    ```

Remix 为乐观 UI 提供了一个很好的基础

Remix 在突变后自动更新加载器数据，以保持客户端和服务器同步。这极大地简化了创建乐观 UI 的过程。

干得好！一如既往，确保也要更新本节中的收入路由，以反映最新的更改。这确保了你在进入下一节之前已经练习了所学的内容。

在这部分中，我们为删除支出添加了乐观 UI 更新。我们还使用了 fetcher 的`data`属性来表示操作失败并需要回滚。接下来，让我们调查我们是否可以乐观地删除支出和收入附件。

## 移除附件

我们已经乐观地显示了待上传文件中的`附件`组件。当点击附件删除按钮时，我们也应考虑乐观地删除附件。

这次，我们不想在待删除的附件上显示`附件`组件，而是显示文件输入。然而，我们还想防止竞争条件，并应确保在服务器确认删除之前禁用输入。

由于附件删除表单提交使用的是`Form`组件（而不是`useFetcher.Form`），我们知道提交是通过全局导航对象处理的。因此，我们可以通过检查全局导航对象上的`formData`属性来检测用户是否删除了附件：

1.  将以下布尔标志添加到`dashboard.expenses.$id._index.tsx`路由模块组件中：

    ```js
    const isUploadingAttachment = attachment instanceof File && attachment.name !== '';Attachment component if an upload is pending or an attachment exists on the expense but not if an attachment is currently being removed. Additionally, disable the input field if a submission is pending:

    ```

    {(isUploadingAttachment || expense.attachment) && !isRemovingAttachment ? (  <Attachment    label="当前附件"    attachmentUrl={`/dashboard/expenses/${expense.id}/attachments/${expense.attachment}`}    disabled={isUploadingAttachment}  />) : (  <Input label="新附件" type="file" name="attachment" disabled={isSubmitting} />)}

    ```js

    ```

1.  尝试运行实现以验证一切是否按预期工作。一如既往，使用**网络**选项卡来限制连接以检查待处理状态。

太好了！我们向附件删除表单和费用更新表单添加了乐观 UI 更新。你了解到 Remix 的`useFetcher`和`useNavigation`原始功能包含当前正在提交的表单的`formData`属性。我们可以使用`formData`属性来乐观地更新 UI，直到`loader`重新验证将 UI 与服务器状态同步。

# 摘要

在本章中，你学习了如何在 Remix 中添加乐观 UI 更新。你了解了乐观 UI 的权衡，例如客户端逻辑的复杂性增加以及在回滚情况下用户反馈的必要性。

Remix 的`loader`重新验证是同步 UI 与服务器状态的一个很好的起点。你现在明白，Remix 的`loader`重新验证使我们能够避免自定义客户端-服务器状态同步，并让我们避免过时的状态。在依赖加载器数据时，我们能够自动获得回滚。每次突变后，我们都会收到最新的加载器数据，并且我们的 UI 会自动更新。

仍然值得说明为什么突变失败。无论是否有乐观更新，向用户显示错误消息都很重要。对于乐观更新，也可能有突出显示回滚数据的视觉意义。Remix 的`ErrorBoundary`组件是恢复错误的一个很好的起点。然而，如果我们想要更精细的反馈，我们必须添加自定义错误消息并利用 Remix 的原始功能来突出显示回滚数据。

当实现乐观式用户界面时，我们通常首先从移除挂起的用户界面开始。在列表中添加和移除挂起实体是一种显示即时反馈的简单方法。

你还学习了如何使用 Remix 的原语，如 `useNavigation` 和 `useFetcher` 来实现乐观式用户界面更新。我们可以在客户端使用 `formData` 属性在服务器返回最终响应之前显示用户数据。

在下一章中，我们将学习不同的缓存策略，以进一步提高 Remix 应用程序的反应时间和性能。

# 进一步阅读

你可以在 Remix 文档中找到更多关于如何实现乐观式用户界面的信息：[`remix.run/docs/en/2/discussion/pending-ui`](https://remix.run/docs/en/2/discussion/pending-ui)。

在 Remix 的 YouTube 频道上还有一个关于乐观式用户界面的精彩 Remix 单视频：[`www.youtube.com/watch?v=EdB_nj01C80`](https://www.youtube.com/watch?v=EdB_nj01C80)。
