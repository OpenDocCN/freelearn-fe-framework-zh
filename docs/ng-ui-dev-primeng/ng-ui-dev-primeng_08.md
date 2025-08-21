# 第八章：创建图表和地图

在本章中，我们将介绍如何使用 PrimeNG 的丰富图表功能和基于谷歌地图的地图来创建可视化图表的方法。PrimeNG 提供了基本和高级的图表功能，其易于使用和用户友好的图表基础设施。除了标准图表外，还有一种特殊的图表用于可视化分层组织数据。在本章中，还将解释绘制折线、多边形、处理标记和事件等地图功能。

在本章中，我们将涵盖以下主题：

+   使用图表模型

+   用线图和条形图表示数据

+   用饼图和圆环图表示数据

+   用雷达和极地区域图表表示数据

+   绘制关系层次的组织图表

+   与谷歌地图 API 的基本集成

+   GMap 组件的各种用例

# 使用图表模型

图表组件通过在网页上使用图表来对数据进行可视化表示。PrimeNG 图表组件基于**Charts.js 2.x**库（作为依赖项），这是一个 HTML5 开源库。图表模型基于`UIChart`类名，并且可以用元素名`p-chart`表示。

通过将图表模型文件（`chart.js`）附加到项目中，图表组件将有效地工作。它可以配置为 CDN 资源、本地资源或 CLI 配置：

+   **CDN 资源配置**：

```ts
 <script src="https://cdnjs.cloudflare.com/ajax/libs/
        Chart.js/2.5.0/Chart.bundle.min.js"></script>

```

+   **Angular CLI 配置**：

```ts
 "scripts":  [  "../node_modules/chart.js/dist/
        Chart.js",  //..others  ]

```

有关图表配置和选项的更多信息，请参阅 Chart.js 库的官方文档（[`www.chartjs.org/`](http://www.chartjs.org/)）。

# 图表类型

图表类型通过`type`属性定义。它支持七种不同类型的图表，并提供自定义选项：

+   `饼图`

+   `条形图`

+   `行`

+   `圆环图`

+   `极地区域图`

+   `雷达图`

+   `水平条形图`

每种类型都有自己的数据格式，可以通过`data`属性提供。例如，在圆环图中，类型应该是`doughnut`，`data`属性应该绑定到数据选项，如下所示：

```ts
<p-chart type="doughnut" [data]="doughnutdata"></p-chart>

```

组件类必须使用`labels`和`datasets`选项定义数据，如下所示：

```ts
this.doughnutdata = {
  labels: ['PrimeNG', 'PrimeUI', 'PrimeReact'],
  datasets: [
    {
      data: [3000, 1000, 2000],
      backgroundColor: [
        "#6544a9",
        "#51cc00",
        "#5d4361"
  ],
      hoverBackgroundColor: [
        "#6544a9",
        "#51cc00",
        "#5d4361"
  ]
    }
  ]
};

```

除了标签和数据选项之外，还可以应用与皮肤相关的其他属性。

图例默认是可关闭的（也就是说，如果您只想可视化特定的数据变体，那么可以通过折叠不需要的图例来实现）。折叠的图例用一条删除线表示。在图例上点击操作后，相应的数据组件将消失。

# 自定义

每个系列都是基于数据集进行自定义的，但您可以通过`options`属性来自定义通用的选项。例如，自定义默认选项的折线图将如下所示：

```ts
<p-chart type="line" [data]="linedata" [options]="options">
</p-chart>

```

该组件需要使用自定义的`title`和`legend`属性来定义图表选项，如下所示：

```ts
this.options = {
 title: {
    display: true,
    text: 'PrimeNG vs PrimeUI',
    fontSize: 16
  },
  legend: {
    position: 'bottom'
  }  };

```

根据上面的示例，`title`选项使用动态标题、字体大小和条件显示标题进行自定义，而`legend`属性用于将图例放置在`top`、`left`、`bottom`和`right`位置。默认的图例位置是`top`。在这个例子中，图例位置是`bottom`。

具有上述自定义选项的折线图将产生以下快照：

![](img/7bb09002-bafe-4a47-a1d8-438f8cf27443.png)

`Chart` API 还支持这里显示的实用方法：

| **方法** | **描述** |
| --- | --- |
| `refresh` | 用新数据重新绘制图表 |
| `reinit` | 销毁现有图表，然后重新创建 |
| `generateLegend` | 返回该图表图例的 HTML 字符串 |

# 事件

图表组件提供了对数据集的点击事件，以使用`onDataSelect`事件回调处理所选数据。

让我们通过传递`event`对象来使用`onDataSelect`事件回调来看一个折线图的例子：

```ts
<p-chart type="line" [data]="linedata" 
  (onDataSelect)="selectData($event)"></p-chart>

```

在组件类中，事件回调用于以以下消息格式显示所选数据信息：

```ts
selectData(event: any) {
  this.msgs = [];
  this.msgs.push({
    severity: 'info',
    summary: 'Data Selected',
    'detail': this.linedata.datasets[event.element._datasetIndex]
    .data[event.element._index]
  });
}

```

在上述事件回调（`onDataSelect`）中，我们使用数据集的索引来显示信息。还有许多其他来自`event`对象的选项：

+   `event.element`：选定的元素

+   `event.dataset`：选定的数据集

+   `event.element._datasetIndex`：图表数据系列的索引

+   `event.element._index`：图表系列内数据元素的索引

# 使用折线图和条形图进行数据表示

折线图是一种以一系列数据点（称为*标记*）通过直线段连接来显示信息的图表类型。折线图通常用于可视化定期时间间隔或时间序列中的实时数据。

关于 Prime 库下载量的线图使用的基本示例如下：

```ts
<p-chart type="line" [data]="linedata" width="300" height="100">
</p-chart>

```

组件类应该定义一条线图数据，其中一条是指 PrimeNG 系列，另一条是指过去一年的 PrimeUI 系列，如下所示：

```ts
this.linedata = {
 labels: ['January', 'February', 'March', 'April', 'May', 
 'June', 'July', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'],
    datasets: [
    {
      label: 'PrimeNG',
      backgroundColor: '#ffb870',
      borderColor: '#cc4e0e',
      data: [13, 22, 15, 38, 41, 42, 25, 53, 53, 63, 77, 93]
     },
     {
      label: 'PrimeUI',
      backgroundColor: '#66ff00',
      borderColor: '#6544a9',
      data: [15, 11, 18, 28, 32, 32, 42, 52, 48, 62, 77, 84]
     }
  ]
};

```

根据上述代码片段，除了数据和标签之外，我们还可以定义背景和边框颜色，使线图变得像我们喜欢的那样花哨和可定制。以下截图显示了线图的快照结果：

![](img/ffdb9569-4a3d-45d9-ad44-6eceac177cd8.png)

条形图或柱状图是一种呈现分组数据的图表，其中用矩形条表示的值是成比例的。PrimeNG 还支持图表中条的水平表示。

关于 Prime 库下载量的条形图使用的基本示例如下：

```ts
<p-chart type="bar" [data]="bardata" width="300" height="100">
</p-chart>

```

组件类应该定义条形图数据，其中一条是指 PrimeNG 数据，另一条是指过去一年的 PrimeUI 系列，如下所示：

```ts
this.bardata = {
  labels: ['January', 'February', 'March', 'April', 'May', 
 'June', 'July', 'Aug', 'Sep',
 'Oct', 'Nov', 'Dec'],
    datasets: [
    {
      label: 'PrimeNG',
      backgroundColor: '#66ff00',
      borderColor: '#6544a9',
      data: [10, 15, 13, 27, 22, 34, 44, 48, 42, 64, 77, 89]
    },
    {
      label: 'PrimeUI',
      backgroundColor: '#ffb870',
      borderColor: '#cc4e0e',
      data: [5, 14, 15, 22, 26, 24, 32, 42, 48, 62, 66, 72]
    }
  ]
};

```

以下截图显示了一年时间内 PrimeNG 和 PrimeUI 下载量的条形图的快照结果：

![](img/3522fac5-2b59-46bc-a5e7-91630bbce406.png)

在上面的图表中，只有两个数据集在一个固定的时间段内进行比较。这也可以应用于多个数据集。

# 使用饼图和圆环图表示数据

饼图（或圆环图）是一种将圆形分成片段以说明复合数据的数值比例的圆形统计图。每个片段的弧长等于数据实体的数量。关于 Prime 库下载量的饼图使用的基本示例如下：

```ts
<p-chart #pie type="pie" [data]="piedata" width="300" height="100">
</p-chart>

```

组件类应该定义饼图数据，其中有三个片段分别代表了三个 Prime 库在一段时间内的情况，如下所示：

```ts
this.piedata = {
  labels: ['PrimeNG', 'PrimeUI', 'PrimeReact'],
  datasets: [
    {
      data: [3000, 1000, 2000],
      backgroundColor: [
        "#6544a9",
        "#51cc00",
        "#5d4361"
  ],
      hoverBackgroundColor: [
        "#6544a9",
        "#51cc00",
        "#5d4361"
  ]
    }
  ]
};

```

以下截图显示了一年时间内 PrimeNG、PrimeUI 和 PrimeReact 下载量的饼图的快照结果：

![](img/d5913a24-d645-4136-89e5-42fbea2d8685.png)

通过悬停在每个饼图的片段上，您可以观察到相应的数据标签及其值。

甜甜圈图是饼图的一种变体，中间有一个空心中心，可以提供有关完整数据的附加信息（即，每个切片代表特定的唯一数据，中心圆表示所有切片的一般附加信息）。

Prime 库下载的甜甜圈图使用的基本示例如下：

```ts
<p-chart type="doughnut" [data]="doughnutdata" width="300" 
  height="100">
</p-chart>

```

组件类应该定义饼图数据，其中包括三个切片，用于一段时间内的三个 Prime 库，如下所示：

```ts
this.doughnutdata = {
 labels: ['PrimeNG', 'PrimeUI', 'PrimeReact'],
  datasets: [
    {
      data: [3000, 1000, 2000],
      backgroundColor: [
        "#6544a9",
        "#51cc00",
        "#5d4361"
  ],
      hoverBackgroundColor: [
        "#6544a9",
        "#51cc00",
        "#5d4361"
  ]
    }
  ]
};

```

以下是一个示例，显示了在一年的时间内使用 PrimeNG、PrimeUI 和 PrimeReact 下载的甜甜圈图的快照结果：

![](img/ecb71e44-f16b-42c7-9953-3ad839d3e516.png)

默认情况下，甜甜圈图的切除百分比为`50`（而饼图为`0`）。这可以通过`cutoutPercentage`图表选项进行自定义。

# 雷达和极地区域图的数据表示

雷达图是以二维图表的形式显示多变量数据的图形表示。它至少有三个或更多的定量变量，这些变量表示在从同一点开始的轴上。这种图表也被称为**蜘蛛图**或**星形图**。它在衡量任何正在进行的程序的绩效指标方面非常有用，以控制改进的质量。

PrimeNG 和 PrimeReact 项目进展的雷达图使用的基本示例如下：

```ts
<p-chart type="radar" [data]="radardata" width="300" height="100">
</p-chart>

```

组件类应该定义雷达图数据，其中包括两个数据集（PrimeNG 和 PrimeReact），用于 SDLC 过程的六个阶段，如下所示：

```ts
this.radardata = {
  labels: ['Requirement', 'Design', 'Implementation', 'Testing', 
 'Deployment', 'Maintainance'],
  datasets: [
    {
      label: 'PrimeNG',
      backgroundColor: 'rgba(162,141,158,0.4)',
      borderColor: 'rgba(145,171,188,1)',
      pointBackgroundColor: 'rgba(145,171,188,1)',
      pointBorderColor: '#fff',
      pointHoverBackgroundColor: '#fff',
      pointHoverBorderColor: 'rgba(145,171,188,1)',
      data: [76, 55, 66, 78, 93, 74]
    },
    {
      label: 'PrimeReact',
      backgroundColor: 'rgba(255,99,132,0.2)',
      borderColor: 'rgba(255,99,132,1)',
      pointBackgroundColor: 'rgba(255,99,132,1)',
      pointBorderColor: '#fff',
      pointHoverBackgroundColor: '#fff',
      pointHoverBorderColor: 'rgba(255,99,132,1)',
      data: [30, 43, 38, 17, 89, 33]
    }
  ]
};

```

在上面的示例中，数据集不仅指的是数据组件，还为图表提供了背景、边框颜色等皮肤。以下截图显示了雷达图的快照结果，其中包括 PrimeNG 和 PrimeReact 项目在 SDLC 生命周期过程的六个阶段的进展：

![](img/3637f524-12da-4e0e-b04b-f19c9af43c66.png)

极地区域图类似于饼图，但每个部分的角度相同（即，部分的半径根据值的不同而不同）。当我们想要显示与饼图类似的比较数据时，这种类型的图表通常很有用。但是，您也可以为给定上下文显示一组值的比例。

Prime 产品库下载的极坐标图使用的基本示例如下：

```ts
<p-chart type="polarArea" [data]="polardata" width="300" height="100">
</p-chart>

```

组件类应该定义各种 Prime 库的极地图下载数据，如下所示：

```ts
this.polardata = {
  datasets: [{
    data: [45, 35, 10, 15, 5],
    backgroundColor: ["#6544a9", "#51cc00", "#5d4361", "#E7E9ED", 
 "#36A2EB"],
    label: 'Prime Libraries'
  }],
  labels: ["PrimeFaces", "PrimeNG", "PrimeReact", "PrimeUI", 
 "PrimeMobile"]
}

```

组件类创建了数据选项以及外观属性。以下屏幕截图显示了使用 PrimeFaces、PrimeNG、PrimeUI、PrimeReact 和 PrimeMobile 下载的极地图的快照结果，作为一年时间段的示例：

![](img/d9b74d4b-f070-43ea-b525-d5e887e2ebb5.png)

根据数据集，提供了`min`和`max`值，并且极地图数据段值将被调整（1、2、3、4、50）。

完整的演示应用程序及说明可在 GitHub 上找到

[`github.com/ova2/angular-development-with-primeng/tree/master/chapter8/charts`](https://github.com/ova2/angular-development-with-primeng/tree/master/chapter8/charts)。

# 绘制关系层次结构的组织图

组织图是一种可视化分层组织数据的图表。PrimeNG 提供了一个名为`OrganizationChart`的组件，用于显示这种自上而下的关系层次结构。该组件需要`TreeNode`实例的模型作为其值。`TreeNode` API 在第五章中的*数据迭代组件*的*使用树形可视化数据*部分进行了解释。在本节中，我们将介绍`OrganizationChart`组件的详细信息，并开发一个图表，用于说明组织中的一个项目。

# 零配置的分层数据

绘制简单图表很容易--只需要`value`属性：

```ts
<p-organizationChart [value]="data"></p-organizationChart>

```

在组件类中，我们需要创建一个嵌套的`TreeNode`实例数组。在简单的用例中，提供标签就足够了：

```ts
data: TreeNode[];

ngOnInit() {
  this.data = [
    {
      label: 'CEO',
      expanded: true,
      children: [
        {
          label: 'Finance',
          expanded: true,
          children: [
            {label: 'Chief Accountant'},
            {label: 'Junior Accountant'}
          ]
        },
        {label: 'Marketing'},
        {
          label: 'Project Manager',
          expanded: true,
          children: [
            {label: 'Architect'},
            {label: 'Frontend Developer'},
            {label: 'Backend Developer'}
          ]
        }
      ]
    }
  ];
}

```

默认情况下，具有子节点（叶子）的树节点不会展开。要将树节点显示为展开状态，可以在模型中设置`expanded: true`。用户可以通过单击节点连接点处的小箭头图标来展开和折叠节点。

简单用例如下图所示：

![](img/db6471f0-e4cf-43dd-9168-3ea64b5d7fe4.png)

# 高级自定义

通过使用`ng-template`标签进行模板化，可以启用自定义。`TreeNode`具有`type`属性，用于匹配`pTemplate`属性的值。这种匹配允许您为每个单个节点自定义 UI。没有`type`属性的节点匹配`pTemplate="default"`。

下一个代码片段有两个`ng-template`标签。第一个匹配具有`type`属性`department`的节点。第二个匹配没有类型的节点。当前节点对象通过微语法`let-node`公开：

```ts
<p-organizationChart  [value]="data" styleClass="company">
 <ng-template  let-node pTemplate="department">
    <div  class="node-header ui-corner-top">
      {{node.label}}
    </div>
    <div  class="node-content ui-corner-bottom">
      <img  src="/assets/data/avatar/{{node.data.avatar}}" width="32">
      <div>{{node.data.name}}</div>
    </div>
  </ng-template>
  <ng-template  let-node pTemplate="default">
    {{node.label}}
  </ng-template>
</p-organizationChart>

```

我们只会展示`data`数组的一部分来传达这个想法。

完整的演示应用程序及说明可在 GitHub 上找到

[`github.com/ova2/angular-development-with-primeng/tree/master/chapter8/orgchart`](https://github.com/ova2/angular-development-with-primeng/tree/master/chapter8/orgchart)。

```ts
this.data = [
  {
    label: 'CEO',
    expanded: true,
    type: 'department',
    styleClass: 'org-dept',
    data: {id: '1', name: 'Alex Konradi', avatar: 'man.png'},
    children: [
      {
        label: 'Finance',
        expanded: true,
        type: 'department',
        styleClass: 'org-dept',
        data: {id: '2', name: 'Sara Schmidt', avatar: 'women.png'},
        children: [
          {
            label: 'Chief Accountant',
            styleClass: 'org-role'
  },
          {
            label: 'Junior Accountant',
            styleClass: 'org-role'
  }
        ]
      },
      ...
    ]
  }
];

```

自定义的组织图如下所示：

![](img/824a5462-8344-4089-96d6-692ea7c83fd0.png)

我们指定了自定义样式类来设置节点和切换器的颜色。例如：

```ts
.org-role {
 background-color: #00b60d;
 color: #ffffff;
}

.org-dept .ui-node-toggler {
 color: #bb0066 !important;
}

```

完整的样式设置可在 GitHub 上找到。

# 选择和事件

选择是通过将`selectionMode`设置为可能的值之一来启用的：`single`或`multiple`。在`single`模式下，预期`selection`属性的值是单个`TreeNode`。在`multiple`模式下，预期是一个数组。例如：

```ts
<p-organizationChart [value]="data"
  selectionMode="single" [(selection)]="selectedNode">
</p-organizationChart>

```

组织图支持两个事件：

| **名称** | **参数** | **描述** |
| --- | --- | --- |
| `onNodeSelect` |

+   `event.originalEvent`：浏览器事件

+   `event.node`：选定的节点实例

| 当通过单击选择节点时调用的回调。 |
| --- |
| `onNodeUnselect` |

+   `event.originalEvent`：浏览器事件

+   `event.node`：取消选择的节点实例

| 当通过单击取消选择节点时调用的回调。 |
| --- |

让我们扩展如下所示的先前开发的组织图：

```ts
<p-organizationChart  [value]="data" styleClass="company"
 selectionMode="single" [(selection)]="selectedNode"
  (onNodeSelect)="onNodeSelect($event)">
  ...
</p-organizationChart>

```

在 GitHub 上的演示应用程序中，我们定义了一个代表个人 VCard 的`VCard`接口：

```ts
export interface VCard {
 id: string;
  fullName: string;
  birthday: string;
  address: string;
  email: string;
}

```

所有 VCard 实例都是在`onNodeSelect`回调中延迟获取的。之后，VCard 会显示在 PrimeNG 对话框中：

```ts
display: boolean = false; selectedVCard: VCard;
private availableVCards: VCard[];

onNodeSelect(event: any) {
 if (this.availableVCards == null) {
    this.vcardService.getVCards().subscribe(
      (vcards: VCard[]) => {
        this.availableVCards = vcards;
        this.showInfo(event);
      });
  } else {
    this.showInfo(event);
  }
}

private showInfo(event: any) {
 this.selectedVCard = null;

  this.availableVCards.some((element: VCard) => {
    if (event.node.data && element.id === event.node.data.id) {
      this.selectedVCard = element;
      return true;
    }
  });

  if (this.selectedVCard) {
    // show VCard in dialog
  this.display = true;
  } else {
    // show node label in growl
  this.msgs = [];
    this.msgs.push({severity: 'Label', summary: event.node.label});
  }
}

```

对话框本身如下所示：

```ts
<p-dialog  header="VCard" [(visible)]="display"
 modal="modal" width="320" [responsive]="true">
 <i  class="fa fa-address-card-o"></i>
  <ul  style="padding: 0.2em 0.8em;">
    <li>Full name: {{selectedVCard?.fullName}}</li>
    <li>Birthday: {{selectedVCard?.birthday}}</li>
    <li>Address: {{selectedVCard?.address}}</li>
    <li>E-mail: {{selectedVCard?.email}}</li>
 </ul>
</p-dialog>

```

结果真是令人惊叹：

![](img/deea3d60-994e-4401-8a05-f6896dd74ec1.png)

# 与 Google Maps API 的基本集成

GMap 组件提供了与 Google Maps API 的集成，以便以更少的配置高效地使用它。它涵盖了诸如绑定选项、各种覆盖物、事件等主要功能。该组件需要 Google Maps API，因此需要在`script`部分中引用它。

JS 资源文件需要在脚本部分中添加，这需要由 GMap 组件利用，如下所示：

```ts
<script type="text/javascript"   
  src="https://maps.google.com/maps/api/js?
  key=AIzaSyA6Ar0UymhiklJBzEPLKKn2QHwbjdz3XV0"></script>

```

使用地图选项的 GMap 的基本示例如下：

```ts
<p-gmap [options]="options" [styleClass]="'dimensions'">
</p-gmap>

```

在页面加载期间，必须使用坐标/位置尺寸（*纬度*和*经度*）、缩放选项等来定义选项，如下所示：

```ts
this.options = {
 center: {lat: 14.4426, lng: 79.9865},
  zoom: 12 };

```

以下屏幕截图显示了 GMap 示例的快照结果：

![](img/a77f55ff-2206-468a-a0d0-3cee377db71f.png)GMap 示例的快照结果

根据前面的快照，根据提供的坐标和缩放设置的可见性模式，显示确切的区域位置。

# GMap 组件的各种用例

除了基本的 Google 地图用法之外，GMap 还可以用于各种用例。使用不同类型的覆盖物、地图上的事件、覆盖物等，地图将更加交互。

# 覆盖物

覆盖物是地图上绑定到纬度/经度坐标或尺寸的对象。覆盖实例数组通过`overlays`属性进行绑定。由于单向绑定的性质，当数组发生变化时，地图将自动更新。

GMap 支持各种类型的覆盖物，如下所示：

+   **标记**：地图上的单个位置。标记还可以显示自定义图标图像。

+   **折线**：地图上的一系列直线。

+   **多边形**：地图上的一系列直线，但形状是“闭合的”。

+   **圆形和矩形**：表示特定区域的圆形/矩形。

+   **信息窗口**：在地图顶部的气球中显示内容。

使用覆盖选项的 GMap 示例用法将被编写如下：

```ts
<p-gmap [options]="options" [overlays]="overlays"  
  [styleClass]="'dimensions'"></p-gmap>

```

让我们定义一个覆盖实例数组，例如标记、折线、多边形、圆形等，如下所示：

```ts
this.overlays = [
 new google.maps.Marker({position: {lat: 14.6188043, 
 lng: 79.9630253}, title:"Talamanchi"}),
  new google.maps.Marker({position: {lat: 14.4290442, 
 ng: 79.9456852}, title:"Nellore"}),
  new google.maps.Polygon({paths: [
    {lat: 14.1413809, lng: 79.8254154}, {lat: 11.1513809, 
 lng: 78.8354154},
    {lat: 15.1313809, lng: 78.8254154},{lat: 15.1613809, 
 lng: 79.8854154}
    ], strokeOpacity: 0.5, strokeWeight: 1,
 fillColor: '#1976D2', fillOpacity: 0.35
  }),
  new google.maps.Circle({center: {lat: 14.1413809, lng: 79.9513809},  
 fillColor: '#197642', fillOpacity: 0.25, strokeWeight: 1, 
 radius: 25000}), new google.maps.Polyline({path: [{lat: 14.1413809,  
 lng: 79.9254154}, {lat: 14.6413809, lng: 79.9254154}], 
 geodesic: true, strokeColor: '#F0F000', strokeOpacity: 0.5,  
 strokeWeight: 2})
];

```

以下屏幕截图显示了 GMap 的快照结果，其中包含各种覆盖物作为示例：

![](img/596d843f-6c1c-4c73-8828-1cb40d7fc438.png)

在上述地图中，您可以观察到基于提供的坐标以及其他覆盖物特定配置的标记、多边形和圆形。

# 事件

GMap 在地图上的交互事件中更加强大。有许多可用的回调函数可以钩入事件，例如单击地图、覆盖物单击和拖动覆盖物。

具有各种类型的覆盖物事件以及事件回调的地图组件示例将被编写如下：

```ts
<p-gmap #gmap [options]="options" [overlays]="overlaysEvents"
 (onMapReady)="handleMapReady($event)"  
  (onMapClick)="handleMapClick($event)"(onOverlayClick)="handleOverlayClick($event)" 
  (onOverlayDragStart)="handleDragStart($event)"
  (onOverlayDragEnd)="handleDragEnd($event)" 
  [styleClass]="'dimensions'"> 
</p-gmap>

```

可以通过单击覆盖物来更新现有事件，也可以通过单击地图并使用对话框组件来创建新事件，如下所示：

```ts
<p-dialog showEffect="fade" [(visible)]="dialogVisible" 
  header="New Location">
 <div class="ui-grid ui-grid-pad ui-fluid" *ngIf="selectedPosition">
    <div class="ui-grid-row">
      <div class="ui-grid-col-2"><label for="title">Label</label></div>
      <div class="ui-grid-col-10"><input type="text" 
        pInputText id="title"
        [(ngModel)]="markerTitle"></div> . 
      </div>
      <div class="ui-grid-row">
        <div class="ui-grid-col-2"><label for="lat">Lat</label></div>
        <div class="ui-grid-col-10"><input id="lat" 
          type="text" readonly pInputText
          [ngModel]="selectedPosition.lat()"></div> 
        </div>
        <div class="ui-grid-row">
          <div class="ui-grid-col-2"><label for="lng">Lng</label></div>
          <div class="ui-grid-col-10"><input id="lng" 
            type="text" readonly pInputText
            [ngModel]="selectedPosition.lng()"></div> 
        </div>
        <div class="ui-grid-row">
          <div class="ui-grid-col-2"><label for="drg">Drag</label> 
          </div>
          <div class="ui-grid-col-10">
            <p-checkbox [(ngModel)]="draggable" binary="true">
            </p-checkbox></div>     
        </div>
     </div>
    <p-footer>
      <div class="ui-dialog-buttonpane ui-widget-content 
        ui-helper-clearfix">
        <button type="button" pButton label="Add Marker" 
          icon="fa-plus" (click)="addMarker()">
        </button>
      </div>
    </p-footer>
</p-dialog>

```

组件类必须在初始页面加载时定义各种覆盖类型，如下所示：

```ts
if (!this.overlaysEvents || !this.overlaysEvents.length) {
 this.overlaysEvents = [
    new google.maps.Marker({position: {lat: 14.6188043, 
 lng: 79.9630253}, title:'Talamanchi'}),  
    new google.maps.Marker({position: {lat: 14.4290442, 
 lng: 79.9456852}, title:'Nellore'}),
    new google.maps.Polygon({paths: [
      {lat: 14.1413809, lng: 79.8254154}, 
      {lat: 11.1513809, lng: 78.8354154},
      {lat: 15.1313809, lng: 78.8254154}, 
      {lat: 15.1613809, lng: 79.8854154}], 
 strokeOpacity: 0.5, strokeWeight: 1, 
 fillColor: '#1976D2', fillOpacity: 0.35
  }),
    new google.maps.Circle({center: {lat: 14.1413809, 
 lng: 79.9513809}, fillColor: '#197642', 
 fillOpacity: 0.25, strokeWeight: 1, radius: 25000}),
    new google.maps.Polyline({path: [{lat: 14.1413809, 
 lng: 79.9254154}, {lat: 14.6413809, lng: 79.9254154}], 
 geodesic: true, strokeColor: '#F0F000',
      strokeOpacity: 0.5, strokeWeight: 2})];
}

```

以下快照显示了如何创建或更新叠加层事件：

![](img/45f32ea0-14f6-40f3-a612-71d5008bd82a.png)

地图组件支持以下列出的事件回调：

| **名称** | **参数** | **描述** |
| --- | --- | --- |
| `onMapClick` | `event`: Google 地图鼠标事件 | 当地图被点击时，除了标记。 |

| `onOverlayClick` | `originalEvent`: Google 地图鼠标事件 `overlay`: 点击的叠加层

`地图`: 地图实例 | 当叠加层被点击时。|

| `onOverlayDragStart` | `originalEvent`: Google 地图鼠标事件 `overlay`: 点击的叠加层

`地图`: 地图实例 | 当叠加层拖动开始时。|

| `onOverlayDrag` | `originalEvent`: Google 地图鼠标事件 `overlay`: 点击的叠加层

`地图`: 地图实例 | 当叠加层被拖动时。|

| `onOverlayDragEnd` | `originalEvent`: Google 地图鼠标事件 `overlay`: 点击的叠加层

`地图`: 地图实例 | 当叠加层拖动结束时。|

| `onMapReady` | `event.map`: Google 地图实例 | 当地图加载后地图准备就绪时。 |
| --- | --- | --- |
| `onMapDragEnd` | `originalEvent`: Google 地图 `dragend` | 当地图拖动（即平移）结束时调用的回调。 |
| `onZoomChanged` | `originalEvent`: Google 地图 `zoom_changed` | 当缩放级别发生变化时调用的回调。 |

有两种访问地图 API 的方式。其中一种是 GMap 组件的 `getMap()` 函数 (`gmap.getMap()`)，另一种是通过事件对象访问 (`event.map`)。一旦地图准备就绪，那么根据我们的需求就可以使用所有地图函数。例如，`getZoom()` 方法可用于从当前状态增加或减少。

完整的演示应用程序及说明可在 GitHub 上找到

[`github.com/ova2/angular-development-with-primeng/tree/master/chapter8/gmap`](https://github.com/ova2/angular-development-with-primeng/tree/master/chapter8/gmap).

# 摘要

通过完成本章，您将能够使用 PrimeNG 图表和 GMap 组件可视化数据表示。最初，我们从图表组件开始。首先，我们从图表模型 API 开始，然后学习如何使用各种图表类型（如饼图、柱状图、折线图、圆环图、极坐标图和雷达图）以编程方式创建图表。我们已经看到，组织图表完美地适应了关系层次的可视化。

接下来，我们转向基于谷歌地图的 GMap 组件。GMap 组件提供了一个方便的 API，用于与谷歌地图 API 进行交互，包括绘制标记、多边形、圆形，注册事件等等。在下一章中，我们将看一些其他用例和需要遵循的最佳实践。
