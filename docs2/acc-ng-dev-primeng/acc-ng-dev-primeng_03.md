# 第四章：*第七章*：组件连接器

测试是软件开发的基本部分。它有助于确保交付的代码覆盖了功能需求且没有实现问题。有时，在测试 UI 代码时，很难避免在 DOM 结构上执行紧密耦合的测试。然而，Angular Ivy 为这个问题带来了一个新的解决方案。组件测试连接器使我们能够使用行业标准页面对象模式为我们的组件开发测试 API，但更细粒度。

更进一步，Angular Ivy 已经包含了 Angular Material 指令和组件的组件连接器。在本章中，我们将学习如何使用 Angular 组件中的组件测试连接器，以及如何实现自定义组件连接器以简化我们的组件测试。

我们在本章中将涵盖以下主题：

+   使用 Angular Material 的组件连接器

+   创建组件连接器

到本章结束时，你应该已经了解了如何以及在哪里使用组件连接器。

# 使用 Angular Material 的组件连接器

你在*第四章*“探索 Angular 组件功能”中看到了如何使用**Material 按钮连接器**的示例。现在，让我们探讨如何使用材料测试连接器以及“以用户身份测试”策略来测试**主题**组件。

如你所记，主题组件让我们可以选择 Angular Academy 的颜色和大小设置。用户可以通过选择颜色并访问具有`#headerBackground`选择器的`MatInputHarness`来完成此操作：

```js
  it('should be able to read default header background 
   color theme setting', async () => {
   const headerBackground: MatInputHarness = await 
    loader.getHarness(
      MatInputHarness.with({ selector: '#headerBackground' 
      })
    );
    expect(await headerBackground.getValue()
     ).toBe('#00aa00');
  });
```

在这里，我们期望默认设置为`'#00aa00'`。我们使用`getValue`方法从测试连接器中检索值。

在这个例子中，我们也可以简单地通过在具有`'#headerBackground'` ID 的输入字段中找到值并检查其值。所以，让我们构建一个更复杂的测试，其中我们应该能够更改标题背景颜色主题设置：

```js
  it('should be able to change the header background color 
   theme setting', async () => {
    const headerBackground: MatInputHarness = await 
     loader.getHarness(
      MatInputHarness.with({ selector: '#headerBackground' 
       })
    );
    headerBackground.setValue('#ffbbcc').then(() => {
      expect(themeService.getSetting(
       'headerBackground')).toBe('#ffbbcc');
    });
  });
```

正如我们之前所做的那样，我们将使用组件连接器与`'#ffbbccc'`进行交互，并检查此设置是否已被主题设置所采用。

你注意到我们没有在这个测试中编写`fixture.detectChanges()`吗？我们可以避免这样做，因为我们正在使用将处理 DOM 操作的组件连接器。点击这个输入字段并像用户一样与颜色选择器交互是使用 DOM 操作相当复杂的，但在这里，我们正在使用组件测试连接器上的操作。通过这样做，我们可以避免与变更检测相关的测试中的脆弱变化。

同样，我们可以使用`MatSliderHarness`到`MatInputHarness`来避免在测试主题组件的**视频大小**滑块设置时执行 DOM 操作：

```js
  it('should be able to check default text and video slider 
   settings', async () => {
    const videoSizeSetting = 
     Number(themeService.getSetting('videoSize'));
    expect(videoSizeSetting).toBe(7);
    const videoSizeSlider: MatSliderHarness = await 
     loader.getHarness(
      MatSliderHarness.with({ selector: '#videoSizeSlider' 
       })
    );
    expect(await videoSizeSlider.getId()
     ).toBe('videoSizeSlider');
    expect(await videoSizeSlider.getValue()
     ).toBe(Number(videoSizeSetting));
```

现在，我们可以从主题服务和使用屏幕组件检索默认的 `videoSize` 设置，并使用测试 harness 的 API 操作来检查它是否为 `7`。使用 `async`/`await` 构造与测试 harness 结合使用，这里代码相当紧凑。

到目前为止，你应该知道如何使用现有的材料组件 harness。接下来，让我们深入了解如何为 Angular Academy 应用程序构建组件 harness。

# 创建组件 harness

让我们假设我们希望公开我们的 `Video` 组件，以便它可以与其他应用程序集成。在这里，为它编写一个测试 harness 是有意义的——但我们应该如何构建它？我们使用 YouTube Player 组件在 `Video` 组件内部显示 YouTube 视频的示例，该 `Video` 组件将位于 Course 组件内部，直接在 DOM 中使用“测试作为用户”的方法进行测试证明是困难的。因此，让我们采取分层的方法。

在构建组件时，我们应该努力使每个页面只有一个单一参考点——DOM。采用分层方法，我们从 Course 组件开始测试，该组件了解 Video 组件，而 Video 组件反过来又了解 YouTube Player 组件。通过这样做，我们可以通过暴露封装 `workspace-video` 选择器的 `Video` harness 作为每个 `Video` 组件实例的 Page Object 来从 `Course` 组件进行测试，如下所示：

```js
export class VideoHarness extends ComponentHarness {
  static hostSelector = 'workspace-video';
  protected getTextElement = this.locatorFor('.text');
  async getText(): Promise<string|null> {
    const textElement = await this.getTextElement();
    return textElement.text()
  }
  textEquals(video: IVideo, text: string): boolean {
    return
      text?.toLowerCase().trim().includes(
         video.title.trim().toLowerCase()
      );
  }
}
```

现在，我们可以通过从 `course.component.spec.ts` 文件中调用 `getText()` 来显示每个视频的文本。然后，我们可以使用提供的 `textEquals` 调用来测试相等性：

```js
 it('should render the video title in text when displaying it', async () => {
  const renderedVideos =
    await loader.getAllHarnesses(VideoHarness);
  courseService.getCourse('1').subscribe((course) => {
      renderedVideos.forEach(async (video: VideoHarness) => {
        const text = await video.getText()|| "";
        expect(course.videos.find((v) => 
         video.textEquals(v, text))).toBeTruthy();
      });
    });
 });
```

在这里，我们遍历了课程组件中渲染的 `'1'` 课程的所有视频，并检查每个与渲染视频一起出现的文本是否包含我们可以通过课程服务检索的课程标题。请注意，这里的 `textEquals` 函数是由测试 harness 提供的，这意味着我们可以在组件库的后续版本中更改该函数。

我们将让 `Video` harness 使用 Page Object 方法隐藏与视频相关的 DOM 操作。然后，`Video` harness 将了解针对 `YouTubePlayer` harness 的特定实现，该 harness 封装了 `youtube-player` 选择器，如下所示：

```js
class YoutubePlayerHarness extends ComponentHarness {
  static hostSelector = 'youtube-player';
  async getVideoId(): Promise<string|null> {
    const host = await this.host();
    return await host.getAttribute('ng-reflect-video-id');
  }
}
```

当使用 Angular YouTube Player 渲染视频时，我们期望视频 ID 可用。这里的技巧是我们希望隐藏 Angular YouTube Player 的实现细节，以便于对 Course 组件的测试。因此，让我们在 `Video` harness 中引入 `getVideoId` 函数，以便在从 Course 组件进行测试时可用：

```js
  it('should have the videoId available when rendering the 
   video', async() => {
    const renderedVideos = await 
     loader.getAllHarnesses(VideoHarness);
    renderedVideos.forEach( async(video: VideoHarness) => {
      const videoId = await video.getVideoId();
      expect(videoId).toBeTruthy();
    })
  })
```

完整的 `Video` harness 将看起来像这样：

```js
export class VideoHarness extends ComponentHarness {
  static hostSelector = 'workspace-video';
  protected getTextElement = this.locatorFor('.text');
  protected getVideoElement = 
   this.locatorFor(YoutubePlayerHarness);
  async getText(): Promise<string | null> {
    const textElement = await this.getTextElement();
    return textElement.text();
  }
  async getVideoId(): Promise<string | null> {
    const videoElement = await this.getVideoElement();
    return videoElement.getVideoId();
  }
  textEquals(video: IVideo, text: string): boolean {
     return  text?.toLowerCase().trim().includes(
      video.title.trim().toLowerCase());
  }
}
```

在这一点上，我们可以将 `Video` test harness 以及 `Video` 组件公开给任何希望在其应用程序中使用我们的 `Video` 组件的人。

# 摘要

在本章中，我们探讨了如何在 Angular Academy 应用程序的环境中，使用一些现有的测试 Material UI 测试工具的示例。此外，我们还介绍了如何实现 Angular Academy 应用程序中使用的 `Video` 组件的组件工具。

在下一章中，我们将通过向您展示如何使用新的提供者作用域来总结我们的 Angular Academy 应用程序。
