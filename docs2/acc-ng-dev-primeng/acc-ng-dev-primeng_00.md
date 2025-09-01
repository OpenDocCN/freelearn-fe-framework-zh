# *第四章*：探索 Angular 组件功能

在*第三章*，“介绍 CSS 自定义属性和新提供者作用域”，我们深入探讨了新的平台和框架功能，为*第二部分，使用你学到的 Angular Ivy 功能构建真实世界应用程序*做准备。让我们继续，但这次将重点放在 Angular 组件包中引入的新 API 上。

随着 Angular Ivy 的推出，推出了两个官方 Angular 包用于 Google 产品：YouTube Player 和嵌入式 Google Maps。在本章中，我们将探讨这两个包。

最后，我们将介绍 Angular CDK 引入的两个新 API：剪贴板 API 和组件测试工具。Angular CDK 的剪贴板指令、服务和域对象与操作系统的原生剪贴板交互。组件工具是一个测试 API，它使用测试即用户的方法包装一个或多个 Angular 组件。它可以在单元测试和端到端测试的上下文中使用。

本章涵盖了以下主题：

+   Angular YouTube Player

+   Angular Google Maps 组件

+   剪贴板 API

+   使用组件工具进行用户测试

了解这些主题将使您能够在*第二部分，使用你学到的 Angular Ivy 功能构建真实世界应用程序*中使用这些强大的功能。

# 技术要求

要继续本章，您需要以下内容：

+   Angular 9.0

+   TypeScript 3.6

您可以在本书的配套 GitHub 仓库中找到视频和映射示例的完整代码：[`github.com/PacktPublishing/Accelerating-Angular-Development-with-Ivy/tree/main/projects/chapter4`](https://github.com/PacktPublishing/Accelerating-Angular-Development-with-Ivy/tree/main/projects/chapter4)。

# Angular YouTube Player

作为 Angular Ivy 的一部分，Angular 团队发布了官方的 Angular 组件用于 Google 产品。其中之一是 Angular YouTube Player。正如其名称所示，它用于在您的 Angular 应用程序中嵌入 YouTube 视频播放器，同时获得 Angular 数据绑定的便利性以及通过组件引用以编程方式访问 YouTube Player API。

在本节中，我们将介绍开始使用 Angular YouTube Player 所需的设置。然后，我们将查看其整个 API，以便熟悉其功能和用法。

## 入门

首先，请确保使用以下命令安装`@angular/youtube-player`包：

```js
ng add @angular/youtube-player
```

现在将`YouTubePlayerModule`添加到声明将使用 YouTube Player 组件的组件的模块中，如下面的代码所示：

```js
import { NgModule } from '@angular/core';
import { YouTubePlayerModule } from '@angular/youtube-player';
import { VideoComponent } from './video.component';
@NgModule({
  declarations: [VideoComponent],
  exports: [VideoComponent],
  imports: [YouTubePlayerModule],
})
export class VideoModule {}
```

在第一次使用 Angular YouTube Player 组件之前，我们必须加载 YouTube IFrame API 脚本。这是一个 100 KB 的脚本，因此根据您的优先级，有几种加载方式。您可以将它作为 `scripts` 选项的一部分加载到您的应用程序项目配置中的 `angular.json` 中，但这样用户就必须始终承担加载、解析和执行此脚本的前期费用。

相反，我们可以在使用它的组件激活时加载它，正如我们将在以下教程中看到的那样：

1.  首先，我们导入我们需要的 Angular 核心和常用 API，并声明组件元数据和组件名称，在这种情况下为 `VideoComponent`：

    ```js
    import { DOCUMENT } from '@angular/common';
    import { Component, Inject, OnDestroy, OnInit } from '@angular/core';
    @Component({
      selector: 'app-video',
      templateUrl: './video.component.html',
    })
    export class VideoComponent implements OnDestroy, OnInit {
    ```

1.  接下来，我们添加一个私有属性以保持对将要动态加载的脚本的引用：

    ```js
      #youtubeIframeScript: HTMLScriptElement;
    ```

1.  现在，我们注入我们平台的 `Document` 对象，即浏览器或服务器：

    ```js
      constructor(@Inject(DOCUMENT) private document: 
       Document) {
    ```

1.  我们创建一个指向 https://www.youtube.com/iframe_api 的 `async` 脚本元素。这是一个初始化脚本的加载器脚本，它设置了嵌入 YouTube 视频所需的 YouTube API。Angular YouTube Player 组件是这个 API 的便捷包装器：

    ```js
        this.#youtubeIframeScript = 
         this.document.createElement('script');
        this.#youtubeIframeScript.src = 
         'https://www.youtube.com/iframe_api';
        this.#youtubeIframeScript.async = true;
      }
    ```

1.  当 `VideoComponent` 初始化时，将 YouTube IFrame API 脚本元素添加到 HTML 文档的 body 元素中：

    ```js
      ngOnInit(): void {
        this.document.body.appendChild(
         this.#youtubeIframeScript);
      }
    ```

1.  当 `VideoComponent` 停用时，我们移除脚本元素：

    ```js
      ngOnDestroy(): void {
        this.document.body.removeChild(
         this.#youtubeIframeScript);
      }
    }
    ```

不要担心脚本多次加载。浏览器将缓存它，YouTube IFrame API 脚本将检测到它已经被加载。为了更健壮的设置解决方案，我们可以监听脚本元素的 `loaded` 事件，在代码中设置一个已加载标志，并在下次此组件激活时添加一个条件以避免再次加载脚本。

这就是我们使用 Angular YouTube Player 组件所需的所有设置。完整的示例可以在本书的配套 GitHub 仓库中找到，如本章引言中所述。现在，让我们继续到使用说明。

## 使用方法

使用 Angular YouTube Player 的最简单示例是将以下代码片段放入组件模板中：

```js
<youtube-player videoId="8NQCgmAQEdE"></youtube-player>
```

我们将 YouTube 视频 ID 传递给 `videoId` 输入属性，Angular YouTube Player 将处理其他所有事情。当然，它也允许进行更多定制。让我们首先看看 `YouTubePlayer` 组件的数据绑定 API。

## API 参考

截至 Angular 版本 12，YouTube Player 没有 API 参考。本节为您提供了这些信息，这样您就不必查找源代码并将其与在线 YouTube JavaScript API 参考进行交叉引用，以便使用它。

### 数据绑定 API

使用数据绑定 API，我们可以声明式地配置 Angular YouTube Player 组件。输入属性用于配置设置，而输出属性则发出关于用户交互和视频环境的事件。

首先，我们将查看输入属性。

#### 输入属性

输入属性用于配置嵌入播放器的播放和视觉效果：

+   `@Input() endSeconds: number | undefined;`

    要设置 YouTube 视频的播放结束点，请传递从视频开始处的秒数偏移量。

+   `@Input() height: number | undefined;`

    YouTube 播放器的高度通过`height`输入属性指定，单位为 CSS 像素。默认值为`390`。

+   `@Input() playerVars: YT.PlayerVars | undefined;`

    可以通过`playerVars`输入属性传递许多其他选项，例如，我们可以传递`{ modestbranding: YT.ModestBranding.Modest }`来隐藏 YouTube 标志。有关完整参考，请参阅[`developers.google.com/youtube/player_parameters.html?playerVersion=HTML5#Parameters`](https://developers.google.com/youtube/player_parameters.html?playerVersion=HTML5#Parameters)。

+   `@Input() showBeforeIframeApiLoads: boolean | undefined;`

    `showBeforeIframeApiLoads`输入属性默认为`false`，但可以设置为`true`，以使 YouTube 播放器组件在 YouTube IFrame API 加载之前抛出错误。

+   `@Input() startSeconds: number | undefined;`

    要设置 YouTube 视频的播放起始点，请传递从视频开始处的秒数偏移量。

+   `@Input() suggestedQuality: YT.SuggestedVideoQuality | undefined;`

    `suggestedQuality`输入属性接受以下质量标识符之一：`'default'`、`'small'`、`'medium'`、`'large'`、`'hd720'`、`'hd1080'`、`'highres'`。

+   `@Input() videoId: string | undefined;`

    `videoId`输入属性接受要播放的 YouTube 视频的 ID。

+   `@Input() width: number | undefined;`

    YouTube 播放器的宽度通过`width`输入属性指定，单位为 CSS 像素。默认值为`640`。

接下来，我们将查看输出属性，它们会发出有关用户交互和视频环境的事件。

#### 输出属性

输出属性暴露了由 YouTube IFrame API 发出的事件。有关事件的完整参考，请参阅 https://developers.google.com/youtube/iframe_api_reference#Events:

+   `@Output() apiChange: Observable<YT.PlayerEvent>;`

    当字幕模块加载或卸载时，`apiChange`输出属性会发出事件。

+   `@Output() error: Observable<YT.OnErrorEvent>;`

    当发生以下错误之一时，`error`输出属性会发出事件，所有这些错误都可以通过`YT.PlayerError`枚举访问：`EmbeddingNotAllowed`、`EmbeddingNotAllowed2`、`Html5Error`、`InvalidParam`、`VideoNotFound`。

+   `@Output() playbackQualityChange: Observable<YT.OnPlaybackQualityChangeEvent>;`

    当播放质量改变时，以下质量标识符之一将通过`playbackQualityChange`输出属性发出：`'default'`、`'small'`、`'medium'`、`'large'`、`'hd720'`、`'hd1080'`、`'highres'`。

+   `@Output() playbackRateChange:Observable<YT.OnPlaybackRateChangeEvent>;`

    当视频播放速率改变时，`playbackRateChange`输出属性会触发一个事件，其中其`data`属性是一个数字，例如`1.0`，表示播放速度。

+   `@Output() ready: Observable<YT.PlayerEvent>;`

    当 YouTube 播放器完全加载并准备好被控制时，会触发此事件。

+   `@Output() stateChange: Observable<YT.OnStateChangeEvent>;`

    每当 YouTube 播放器状态改变为以下之一时，都会输出一个事件，这些都可以通过`YT.PlayerState`枚举访问：`BUFFERING`、`CUED`、`ENDED`、`PAUSED`、`PLAYING`、`UNSTARTED`。

我们可以通过其组件方法向 YouTube 播放器发送命令或读取信息，我们将在下一节讨论这些方法。

### 组件方法

`YouTubePlayer`组件有几个公共方法，我们可以使用这些方法来控制嵌入的视频播放器：

+   `getAvailablePlaybackRates(): number[];`

    确定当前活动或队列视频支持的播放速率。对于仅支持正常播放速度的视频，返回`[1.0]`。可能返回类似`[0.25, 0.5, 1.0, 1.5, 2.0]`的数组。

+   `getCurrentTime(): number;`

    确定视频开始以来的秒数。

+   `getDuration(): number;`

    确定当前活动视频的持续时间（以秒为单位）。如果视频的元数据尚未加载，则返回`0`。

    对于直播流，它将返回自流开始、重置或中断以来的秒数。

+   `getPlaybackRate(): number;`

    确定当前活动或队列视频的播放速率，其中`1.0`是正常播放速度。

+   `getPlayerState(): YT.PlayerState | undefined;`

    确定当前播放器状态，可以是以下之一，这些都可以通过`YT.PlayerState`枚举访问：`BUFFERING`、`CUED`、`ENDED`、`PAUSED`、`PLAYING`、`UNSTARTED`。返回由`stateChange`输出属性触发的事件表示的最新值。

+   `getVideoEmbedCode(): string;`

    确定嵌入 HTML 页面中视频所需的 HTML 标记，例如：

    ```js
    <iframe id="ytplayer" type="text/html" width="720" height="405"
    src="img/8NQCgmAQEdE"
    frameborder="0" allowfullscreen></iframe>
    ```

+   `getVideoLoadedFraction(): number;`

    确定播放器已缓冲的视频百分比，其中`0.0`是 0%，`1.0`是 100%。

+   `getVideoUrl(): string;`

    确定视频在[youtube.com](http://youtube.com)上的完整 URL。

+   `getVolume(): number;`

    确定音量级别在`0`到`100`之间。仅返回整数。当静音时，此方法将返回音频静音时的活动级别。

+   `isMuted(): boolean;`

    检查音频是否静音。`true`表示静音，`false`表示未静音。

+   `mute(): void;`

    静音音频。

+   `pauseVideo(): void;`

    暂停视频。通过`stateChange`触发一个事件。

+   `playVideo(): void;`

    开始播放视频。不计入 YouTube 视频的观看次数。通过`stateChange`触发一个事件。

+   `seekTo(seconds: number, allowSeekAhead: boolean): void;`

    跳转到指定的标记时间戳。如果跳转前视频处于暂停状态，视频将继续暂停。将 `allowSeekAhead` 设置为 `false` 可以防止播放器从服务器下载未缓冲的内容。这可以与进度条一起使用。

+   `setPlaybackRate(playbackRate: number): void;`

    调整播放速度。仅影响当前活动或排队的视频。将 `1.0` 传递给 `playbackRate` 将播放速度设置为正常。

    我们首先应该调用 `getAvailablePlaybackRates` 来检查视频支持哪些播放速率。监听由 `playbackRateChange` 输出属性发出的事件，以验证播放速度是否已成功调整。

    如果传递的 `playbackRate` 与支持的播放速率不完全匹配，则将匹配最近的速率，四舍五入到 `1.0`。

+   `setVolume(volume: number): void;`

    将音量调整到 `0` 到 `100` 之间的水平。仅接受整数。

+   `stopVideo(): void;`

    停止加载或播放视频。如果我们知道用户不会在 YouTube 播放器中观看其他视频，我们可以使用这个方法。在播放不同视频之前不需要调用它。通过 `stateChange` 发出事件，但状态可以是 `CUED`、`ENDED`、`PAUSED` 或 `UNSTARTED` 中的任何一个。

+   `unMute(): void;`

    取消静音。

在了解 `YouTubePlayer` 组件的完整组件 API 后，您可以在其之上构建自己的控件，配置应用程序中所有 YouTube Player 的默认设置，同时控制多个 YouTube Player，或使用 Angular 实现一个 YouTube 视频片段小部件。

我们已经讨论了如何安装和设置您的应用程序以使用 Angular YouTube Player。我们看到了一个简单的示例用法，列出了其完整 API，并讨论了其用例。现在您可以使用 Angular YouTube Player 在 *第二部分，使用您学到的 Angular Ivy 功能构建实际应用程序*。

接下来，我们将查看官方的 Angular Google Maps 组件。

# Angular Google Maps 组件

在本节中，我们将查看名为 Angular Google Maps 的官方 Google 产品组件包。由于 Google Maps API 很大，因此此包包括 `GoogleMap` 组件以及用于配置和控制其许多功能的几个其他组件。

它由以下 Angular 组件和指令组成：

+   `GoogleMap`

+   `MapBicyclingLayer`

+   `MapCircle`

+   `MapGroundOverlay`

+   `MapInfoWindow`

+   `MapKmlLayer`

+   `MapMarker`

+   `MapMarkerClusterer`

+   `MapPolygon`

+   `MapRectangle`

+   `MapTrafficLayer`

+   `MapTransitLayer`

我们将探索必要的组件 `GoogleMap` 以及常用的组件 `MapInfoWindow`、`MapMarker` 和 `MapMarkerClusterer`。

## 入门

要使用 Angular Google Maps 组件，我们首先必须加载 Google Maps JavaScript API。此示例包装组件说明了在 Google Maps JavaScript API 初始化后如何有条件地渲染 Google Maps 组件：

```js
import { Component, ViewChild } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { GoogleMap }from '@angular/google-maps';
import { Observable, of } from 'rxjs';
import { catchError, mapTo } from 'rxjs/operators';
import { AppConfig } from '../app-config';
@Component({
  selector: 'app-map',
  templateUrl: './map.component.html',
})
export class MapComponent {
  @ViewChild(GoogleMap, { static: false })
map?: GoogleMap;
isGoogleMapsApiLoaded$: Observable<boolean> = this.http.jsonp('https://maps.googleapis.com/maps/api/js?key=${this.config.googleMapsApiKey}','callback').pipe(
mapTo(true),
   catchError(() => of(false)),
  );
  constructor(
    private config: AppConfig,
    private http: HttpClient,
  ) {}
}
```

我们的 `MapComponent` 有一个名为 `isGoogleMapsApiLoaded$` 的可观察 UI 属性，该属性使用预配置的 API 密钥加载 Google Maps JavaScript API。我们使用此属性在组件模板中条件性地渲染 `GoogleMap` 组件，如下面的代码所示：

```js
<google-map *ngIf="isGoogleMapsApiLoaded$ | async; else spinner"></google-map>
<ng-template #spinner>
<mat-spinner></mat-spinner>
</ng-template>
```

在 Google Maps JavaScript API 加载之前，会显示一个 Angular Material Spinner 组件。

注意，我们为 `GoogleMap` 创建了一个视图子查询，并将其存储在 `map` 属性中。这可以用于从组件模型中编程控制地图。

以下是我们声明的示例 `MapComponent` 的 Angular 模块：

```js
import { CommonModule } from '@angular/common';
import { HttpClientModule, HttpClientJsonpModule } from '@angular/common/http';
import { NgModule } from '@angular/core';
import { GoogleMapsModule } from '@angular/google-maps';
import { MatProgressSpinnerModule } from'@angular/material/progress-spinner';
import { MapComponent } from './map.component';
@NgModule({
  declarations: [MapComponent],
  exports: [MapComponent],
  imports: [
    HttpClientModule,
    HttpClientJsonpModule,
    GoogleMapsModule,
    MatProgressSpinnerModule,
  ],
})
export class MapModule {}
```

现在我们已经拥有了使用 Angular Google Maps API 所需的所有设置，让我们更详细地看看 `@angular/google-maps` 包中包含的最常见的组件。

## GoogleMap 组件

`GoogleMap` 组件是 Angular Google Maps 包的主要入口点。它是可以包含此包中其他组件的顶层组件。

此组件是 Google Maps JavaScript API 中的 `google.maps.Map` 类的声明性、Angular 特定的包装器。请参阅 API 参考以获取有关 `Map` 类的更多详细信息（https://developers.google.com/maps/documentation/javascript/reference/map）。

Google 地图组件具有 `center`、`height`、`mapTypeId`、`width` 和 `zoom` 输入属性。它还接受一个 `options` 输入，其类型为 `google.maps.MapOptions`。它有 19 个不同的输出属性，所有这些属性都与 Google Maps JavaScript API 参考中描述的 `Map` 类的 DOM 事件相匹配。它还提供了一系列可用于控制地图的方法。

## MapMarker 组件

`MapMarker` 组件的元素 `<map-marker>` 要么嵌套在 `<google-map>` 元素内部，要么嵌套在 `<map-marker-clusterer>` 元素内部。

此组件代表 Google 地图上的一个标记。我们可以使用标签、标记图标或标记符号来自定义它。

`MapMarker` 是 Angular 特定的 `google.maps.Marker` 类的包装器。它具有 `clickable`、`label`、`position` 和 `title` 输入属性。它还接受一个 `options` 输入，其类型为 `google.maps.MarkerOptions`。

我们可以通过标记选项传递自定义图标，例如，如下所示，我们使用海滩旗帜图标：

```js
<google-map
  [center]="{ lat: 56.783778, lng: 8.228937 }"
>
  <map-marker
    [options]="{ icon: 'https://developers.google.com/
     maps/documentation/javascript/examples/full/
     images/beachflag.png' }"
    [position]="{ lat: 56.783778, lng: 8.228937 }"
  ></map-marker>
</google-map>
```

请参阅 API 参考以获取有关 `Marker` 类的更多详细信息 ([`developers.google.com/maps/documentation/javascript/reference/marker`](https://developers.google.com/maps/documentation/javascript/reference/marker))。

## MapMarkerClusterer 组件

`MapMarkerClusterer` 组件是 Angular 特定的 `MarkerClusterer` 类的包装器，该类来自 `@googlemaps/markerclustererplus` 包。其元素 `<map-marker-clusterer>` 嵌套在 `<google-map>` 元素内部，并包含多个 `<map-marker>` 元素。

此组件用于在地图缩放时将许多地图标记分组到簇中。

在我们能够使用它之前，我们必须通过在适当的地点插入以下脚本标签将其加载到全局变量中，为了简单起见，在组件的模板中使用：

```js
<script src="img/index.min.js"></script>
```

`MapMarkerClusterer` 组件有 18 个不同的输入属性，例如 `minimumClusterSize`、`maxZoom` 和 `zoomOnClick`。`imagePath` 输入属性可以用来指定自定义地图标记聚类图像，其中此路径默认自动附加 `[1-5].png`。

有两个输出属性可用：`clusteringbegin` 和 `clusteringend`。它们在标记首次聚类和它们被拆分为单独标记时分别发出。

请参阅 API 参考以获取有关 `MarkerClusterer` 类的更多详细信息（https://developers.google.com/maps/documentation/javascript/marker-clustering）。

## MapInfoWindow 组件

`MapInfoWindow` 组件是 Angular 特定的 `google.maps.InfoWindow` 类的包装器。其元素 `<map-info-window>` 嵌套在 `<google-map>` 中。

它是一个覆盖层，用于在地图上显示通知或元数据，通常在地图标记附近。

它的 `position` 输入属性声明了它在地图上的位置。此外，它接受一个类型为 `google.maps.InfoWindowOptions` 的 `options` 输入。它有五个不同的输出属性——`closeClick`、`contentChanged`、`domready`、`positionChanged` 和 `zindexChanged`——所有这些都与 Google Maps JavaScript API 参考中 `InfoWindow` 类描述的 DOM 事件相匹配。

`MapInfoWindow` 组件使用内容投影，这意味着我们放入其自定义元素标签中的内容在打开时将在其覆盖层中渲染。

要显示 `MapInfoWindow` 组件，调用其 `open` 方法，该方法可选地接受 `MapMarker`，信息窗口将附加到该标记上。`close` 方法隐藏 `MapInfoWindow` 组件。

请参阅 API 参考以获取有关 `InfoWindow` 类的更多详细信息（https://developers.google.com/maps/documentation/javascript/reference/info-window）。

现在，你已经了解了官方 Google Maps Angular 组件中最常用的部分，你准备好在 *Part 2, Build a Real-World Application with the Angular Ivy Features You Learned* 中使用 Google Maps。

在下一节中，我们将了解 Angular CDK 的 Clipboard API。

# Clipboard API

Angular CDK 的 Clipboard API 提供了一个指令和一个服务，通过浏览器与操作系统的剪贴板进行交互。`CdkCopyToClipboard` 指令可以声明式地使用，而 `Clipboard` 服务用于更适合程序化 API 的用例。Clipboard API 还通过 `PendingCopy` 类处理长文本。

在本节中，你将学习如何使用 Angular CDK 包中的每个这些类。

## CdkCopyToClipboard 指令

`CdkCopyToClipboard` 指令由 `ClipboardModule` 导出，该模块位于 `@angular/cdk/clipboard` 子包中。其指令选择器为 `[cdkCopyToClipboard]`。该指令有一个与指令同名的输入属性，它接受当附加到其上的元素被点击时复制的文本。

由于浏览器安全考虑，复制文本到剪贴板必须在用户触发的点击事件之后进行。

复制到剪贴板指令还有一个名为 `cdkCopyToClipboardAttempts` 的输入属性。它接受一个数字，这是指令在放弃之前尝试复制文本的宏任务周期数。这在处理更大的文本时是相关的，因为一个确保跨浏览器兼容性的实现细节，直到即将到来的 Clipboard API 在所有主要浏览器中得到支持。我们将在探索 `PendingCopy` 类时进一步讨论这个注意事项。

下面的代码片段演示了复制到剪贴板指令及其重试参数：

```js
<button
  [cdkCopyToClipboard]="transactionLog"
  [cdkCopyToClipboardAttempts]="5"
>
  Copy transaction log
</button>
```

最后，`CdkCopyToClipboard` 指令有一个名为 `cdkCopyToClipboardCopied` 的输出属性，每次尝试复制到剪贴板时都会发出一个布尔值，指示复制是否成功。

## 剪贴板服务

当我们想在复制文本到剪贴板之前或之后执行其他操作，或者文本不易从组件模板中访问，或者我们希望在复制大文本时拥有更精细的控制时，`Clipboard` 服务非常有用。

剪贴板服务有两种方法。`Clipboard#copy` 方法接受要复制到剪贴板的文本，并返回一个布尔值，指示复制操作是否成功。

对于某些大文本，`Clipboard#copy` 方法会失败，我们必须改用 `Clipboard#beginCopy` 方法。此方法也接受我们想要复制到剪贴板的文本，但返回一个 `PendingCopy` 类的实例，我们必须进一步与之交互以完成复制到剪贴板的操作。这个类将在下一节中讨论。

## `PendingCopy` 类

`Clipboard#beginCopy` 方法返回 `PendingCopy` 类的一个实例。如本节前面所述，这与确保复制大文本的跨浏览器兼容性的实现细节有关。

我们必须了解 `PendingCopy` 类的第一件事是，一旦我们完成使用它们，就必须通过调用 `PendingCopy#destroy` 方法来销毁所有实例，否则我们的应用程序将泄漏资源。

`PendingCopy#copy` 方法不接受任何参数，并返回一个布尔值，指示复制到剪贴板的操作是否成功。如果返回 `false`，我们应该安排稍后再次尝试。

如本节前面所述，`CdkCopyToClipboard` 指令通过传递最大尝试次数到其 `cdkCopyToClipboardAttempts` 输入属性，支持复制大文本的重试策略。

既然我们已经讨论了 Angular CDK 的 Clipboard API 的所有部分，我们就准备好在 *第二部分，使用你学到的 Angular Ivy 功能构建真实世界应用程序* 中实现一个实际应用的功能。

在下一节中，你将了解组件测试 Harness，这是一个用于测试组件的创新 API，以及为在库包中公开的组件编写测试 API。

# 使用组件 Harness 进行用户测试

Angular CDK 的用于编写和使用组件测试 Harness 的 API，是一种以“测试即用户”哲学为出发点的新方法。每个组件或相关组件集都可以有一个用于测试的组件 Harness。组件 Harness 是一个用于与这些组件交互的测试 API，可用于单元测试、集成测试和端到端测试。

组件测试 Harness 内部仅依赖于它们所包装组件的单个选择器。库作者可以为他们的 Angular 组件发布组件 Harness。这样，他们的消费者测试（这些测试依赖于库的组件）除了那个选择器之外，不会对 DOM 结构有依赖，而库作者如果需要可以更改该选择器。

这正是 Angular 组件团队为 Angular CDK 和 Angular Material 所做的事情。他们发布并维护所有 Angular 组件的组件 Harness。

## Harness 环境和 Harness 加载器

Harness 环境代表使用组件 Harness 运行的测试上下文。对于使用 Karma、Jasmine 或 Jest 等测试运行器的单元和集成测试，我们使用 `TestbedHarnessEnvironment`，它是 Angular CDK 的一部分。对于 Protractor 端到端测试，我们使用 `ProtractorHarnessEnvironment`，它也作为 Angular CDK 的一部分发布。

重要提示

根据 Angular 版本的不同，Protractor 支持可能已被弃用或移除。

如果你想要与其他端到端测试框架一起使用组件 Harness，你必须扩展 `HarnessEnvironment` 基类并实现 `TestElement` 接口，以便在不同的测试环境中工作。当然，首先确保在 Angular 生态系统中寻找现有的解决方案。

任何时候只能有一个 Harness 环境处于活动状态。我们使用 Harness 环境来创建 Harness 加载器。一个 Harness 加载器具有特定 DOM 元素的上下文，并用于根据选择器查询和创建组件 Harness。

在讨论完作为 Angular Material 部分发布的组件 Harness 的 API 之后，我们将通过一些简单的代码示例来介绍 Harness 环境和 Harness 加载器的使用。

## 组件 Harness

从技术上讲，组件 Harness 可以代表任何 DOM 元素以及一组用户交互和特性。

为了感受组件 Harness 的使用，让我们首先看看 Angular Material Button 组件的测试 Harness。

以下为 `MatButtonHarness` 的 API，它不是从公共组件 Harness 基类继承的：

+   `blur(): Promise<void>;`

+   `click(relativeX: number, relativeY: number): Promise<void>;`

    `click('center'): Promise<void>;`

    `click(): Promise<void>;`

+   `focus(): Promise<void>;`

+   `getText(): Promise<string>;`

+   `isDisabled(): Promise<boolean>;`

+   `isFocused(): Promise<boolean>;`

它还继承自 Angular CDK 基类的一些其他方法，但我们现在不会讨论这些。

我相信您可以根据它们的名字、参数和返回值猜出这些方法的功能。请注意，它们都是异步的，也就是说，它们都返回一个`Promise`。

除了`getText`方法之外，每个方法都代表用户交互。`getText`方法从 DOM 中读取内容，这是显示给用户的，即按钮的文本。

接下来，让我们探索 Angular Material Select 组件测试 harness 的 API。

以下是与`MatSelectHarness`特定的 API：

+   `blur(): Promise<void>;`

+   `clickOptions(filter?: OptionFilters): Promise<void>;`

    选择与指定过滤器匹配的下拉选项（复选）。对于多选项选择，可以选择多个选项。对于单选项选择，选择第一个匹配的选项。

+   `close(): Promise<void>;`

    关闭下拉面板。

+   `focus(): Promise<void>;`

+   `getOptionGroups(filter?: OptionGroupFilters): Promise<OptionGroup[]>;`

    读取与指定过滤器匹配的下拉选项组。

+   `getOptions(filter?: OptionFilters): Promise<Option[]>;`

    读取与指定过滤器匹配的下拉选项。

+   `getValueText(): Promise<string>;`

    读取所选下拉选项的值。

+   `isDisabled(): Promise<boolean>;`

+   `isEmpty(): Promise<boolean>;`

    如果没有选择任何值，则解析为`false`。如果已选择值，则解析为`true`。

+   `isFocused(): Promise<boolean>;`

+   `isMultiple(): Promise<boolean>;`

    如果 harness 包裹了一个多选项选择组件，则解析为`true`。如果它包裹了一个单选项选择组件，则解析为`false`。

+   `isOpen(): Promise<boolean>;

+   `isRequired(): Promise<boolean>;`

+   `isValid(): Promise<boolean>;`

+   `open(): Promise<void>;`

这些方法对应于我们对下拉选择器（如 Angular Material **Select**组件）的行为和信息表示的预期。

现在我们已经讨论了组件 harness 的最重要的概念并看到了一些组件 harness API，是时候看看一个结合这些概念的测试用例了。

以下是一个在线服装店测试的示例。您将不得不想象`ShirtComponent`和协作服务的实现。事实上，这正是测试即用户方法的核心所在。它是组件和实现无关的：

1.  首先，我们导入必要的 Angular 包：

    ```js
    import { HarnessLoader } from '@angular/cdk/testing';
    import { TestbedHarnessEnvironment } from '@angular/cdk/testing/testbed';
    import { TestBed } from '@angular/core/testing';
    import { MatButtonModule } from '@angular/material/button';
    import { MatButtonHarness } from '@angular/material/button/testing';
    import { MatSelectModule } from '@angular/material/select';
    import { MatSelectHarness } from '@angular/material/select/testing';
    ```

1.  接下来，我们导入`ShirtComponent`组件、协作的`OrderService`和`OrderSpyService`类来替换它：

    ```js
    import { OrderService } form './order.service';
    import { OrderSpyService } form './order-spy.service';
    import { ShirtComponent } from './shirt.component';
    ```

1.  在我们可以实现测试用例之前，我们通过设置必要的声明式配置和将 `OrderService` 替换为用于测试的间谍服务来配置 Angular 测试模块：

    ```js
    describe('ShirtComponent', () => {
    beforeEach(() => {
        TestBed.configureTestingModule({
    declarations: [ShirtComponent],
          imports: [MatButtonModule, MatSelectModule],
          providers: [
            { provide: OrderService, useClass:          OrderSpyService },
          ],
        });
        const fixture = TestBed.createComponent(
         ShirtComponent);
    ```

1.  我们使用前一步骤中的组件固定工具来创建单元测试的 harness loader：

    ```js
        loader = TestbedHarnessEnvironment.
         loader(fixture);
    ```

1.  最后，我们将订单间谍服务存储在共享的 `orderSpy` 变量中：

    ```js
        orderSpy = TestBed.inject(OrderService) as 
         OrderSpyService;
    });
      let loader: HarnessLoader;
      let orderSpy: OrderSpyService;
    ```

1.  现在我们加载了 shirt size picker 的组件 harness，该 harness 是通过 Angular Material Select 组件实现的，如本步骤所示：

    ```js
    it('orders a Large shirt', async () => {
    const shirtSizePicker = await loader.getHarness(MatSelectHarness);
    ```

1.  对于这个测试用例，我们还需要加载一个 **购买** 按钮的组件 harness，该按钮是通过 Angular Material Button 组件实现的：

    ```js
        constpurchaseButton = awaitloader.getHarness(
         MatButtonHarness.with({ text: '1-click purchase' });
    ```

1.  接下来，我们作为用户执行一次销售，选择 `Large` 衣服尺寸并点击 **购买** 按钮：

    ```js
    await shirtSizePicker.clickOptions({ text: 'Large' });
        await purchaseButton.click('center');
    ```

1.  最后，我们断言订单服务间谍已被按预期调用：

    ```js
        expect(orderSpy.purchase).
         toHaveBeenCalledTimes(1);
      });
    });
    ```

我们看到，由于使用了 Angular Material 的组件 harness，测试相对简单。首先，选择了 *大号* 衣服尺寸，然后点击了一键 **购买** 按钮，我们期望我们的订单服务间谍已被调用并带有订单。

注意我们是如何在组件测试中像往常一样配置 Angular 测试模块的。在创建组件固定工具后，我们用它来创建一个 harness loader。然后，harness loader 被用来查询 Angular Material Select 和 Button 组件的组件 harness。传递一个过滤器以确保我们与正确的按钮进行交互。

我们使用组件 harness 而不是与组件实例交互或传递选择器到 DOM 查询。我们的测试与结构 DOM 变化和 Angular Material 组件的实现细节解耦。

现在，我们已经探讨了组件 harness 的最重要的概念，你准备好实现自己的 harness 并使用它们在 *第二部分，使用你学到的 Angular Ivy 功能构建真实世界应用程序* 中作为用户进行测试了。

# 摘要

在本章中，我们探讨了 Angular YouTube Player 的 API、Google Maps Angular 组件、Angular CDK 的 Clipboard API，以及 Angular CDK 的组件 harness 以及它们是如何被 Angular Material 使用的，反过来我们也可以在我们的应用程序中使用。

YouTube Player 组件是围绕嵌入的 YouTube Player 的 Angular 特定包装器。我们学习了如何初始化它并详细探讨了其 API。

许多官方 Angular 组件包装器可用于创建和交互 Google Maps 的丰富 API。我们学习了 `GoogleMap`、`MapMarker`、`MapMarkerClusterer` 和 `MapInfoWindow` 组件，它们用于常见的 **地理信息系统** (**GIS**) 用例。

Angular CDK 的 Clipboard API 是一个跨浏览器和跨平台的 API，用于与原生剪贴板交互。我们学习了 `CdkCopyToClipboard` 指令、`Clipboard` 服务和 `PendingCopy` 类。

最后，我们讨论了 Angular CDK 引入的 Angular 组件工具包的主要概念。我们看到了 Angular Material 暴露的组件工具包 API 的示例，以及我们如何使用它们来测试自己的组件，而无需依赖于实现细节或 DOM 结构，这些可能在包的未来版本中发生变化。

在心中牢记所有这些令人兴奋的新特性和 API 之后，让我们继续进入*第二部分，使用你学到的 Angular Ivy 特性构建一个真实世界的应用程序*，其中我们将向现有的 Angular 应用程序添加新功能。当然，这些知识将会非常有用。

*第五章*，*使用 CSS 自定义属性*，通过结合 CSS 自定义属性和 Angular，在*第二部分，使用你学到的 Angular Ivy 特性构建一个真实世界的应用程序*中开始，为 Angular Academy 应用程序添加一个主题选择器。
