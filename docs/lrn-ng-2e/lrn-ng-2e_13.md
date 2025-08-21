# 第十三章：Angular 中的单元测试

前几章的辛勤工作已经变成了一个我们可以引以为傲的工作应用程序。但是，我们如何确保未来的可维护性？一套全面的自动化测试层将成为我们的生命线，一旦我们的应用程序开始扩展，我们就必须减轻由新功能与已经存在的功能相冲突而引起的错误的影响。

测试（更具体地说，单元测试）应该由开发人员在项目开发过程中进行。然而，在本章中，我们将简要介绍测试 Angular 模块的所有复杂性，因为项目已经处于成熟阶段。

在本章中，您将看到如何实现测试工具，以对应用程序的类和组件进行适当的单元测试。

在本章中，我们将：

+   看看测试的重要性，更具体地说，单元测试

+   构建测试管道的测试规范

+   为具有或不具有依赖项的组件设计单元测试

+   对我们的路由进行测试

+   为服务实现测试，模拟依赖项和存根

+   拦截 XHR 请求并提供模拟响应以进行精细控制

+   了解如何测试指令作为没有视图的组件

+   介绍其他概念和工具，如 Karma、代码覆盖工具

和端到端（E2E）测试

# 为什么我们需要测试？

什么是单元测试？如果您已经熟悉单元测试和测试驱动开发，可以安全地跳过下一节。如果不熟悉，让我们说单元测试是工程哲学的一部分，它支持高效和敏捷的开发过程，通过在代码开发之前为代码添加一层自动化测试。核心概念是每一段代码都有自己的测试，并且这两段代码都是由正在开发该代码的开发人员构建的。首先，我们设计针对我们要交付的模块的测试，检查其输出和行为的准确性。由于模块还没有实现，测试将失败。因此，我们的工作是以使模块通过自己的测试的方式构建模块。

单元测试是相当有争议的。虽然人们普遍认为测试驱动开发对于确保代码质量和随时间的维护是有益的，但并不是每个人在日常实践中都进行单元测试。为什么呢？嗯，在开发代码的同时构建测试有时可能会感觉像是一种负担，特别是当测试的规模比它旨在测试的功能部分还要大时。

然而，支持测试的论点比反对它的论点多得多：

+   构建测试有助于更好的代码设计。我们的代码必须符合测试要求，而不是相反。在这种意义上，如果我们试图测试现有的一段代码，并且在某个时候发现自己被阻止了，那么这段代码很可能设计不良，并展示出需要重新思考的复杂接口。另一方面，构建可测试的模块可以帮助早期发现对其他模块的副作用。

+   重构经过测试的代码是防止在后期引入错误的生命线。任何开发都意味着随着时间的推移而发展，每次重构都会引入错误的风险，这些错误可能只会在我们应用程序的另一个部分中出现。单元测试是确保我们在早期捕捉错误的好方法，无论是在引入新功能还是更新现有功能时。

+   构建测试是记录我们的代码 API 和功能的好方法。当一个不熟悉代码库的人接手开发工作时，这将成为无价的资源。

这只是一些论点，但你可以在网上找到无数关于测试代码好处的资源。如果你还不感到满意，不妨试一试。否则，让我们继续我们的旅程，看看测试的整体形式。

# 单元测试的解剖结构

有许多不同的方法来测试一段代码，但在本章中，我们将看看测试的解剖结构，它由什么组成。测试任何代码的第一件事是测试框架。测试框架应该提供用于构建测试套件的实用函数，每个套件包含一个或多个测试规范。那么这些概念是什么呢？

+   **测试套件**：套件为一组测试创建了一个逻辑分组。例如，一个套件可以是产品页面的所有测试。

+   **测试规范**：这是单元测试的另一个名称。

以下显示了一个测试文件的样子，我们在其中使用了一个测试套件，并放置了许多相关的测试。我们选择的框架是 Jasmine。在 Jasmine 中，`describe()`函数帮助我们定义一个测试套件。`describe()`方法以名称作为第一个参数，以函数作为第二个参数。在`describe()`函数内部有许多对`it()`方法的调用。`it()`函数是我们的单元测试；它以测试名称作为第一个参数，以函数作为第二个参数：

```ts
// Test suite
describe('A math library', () => { 
 // Test spec
 it('add(1,1,) should return 2', () => {
 // Test spec implementation goes here
 });
});
```

每个测试规范检查套件描述参数中描述的功能的特定功能，并在其主体中声明一个或多个期望。每个期望都取一个值，我们称之为期望值，并通过匹配器函数与实际值进行比较，该函数检查期望值和实际值是否相匹配。这就是我们所说的断言，测试框架将根据这些断言的结果通过或失败规范。代码如下：

```ts
// Test suite
describe('A math library', () => {
 // Test spec
 it('add(1,1) should return 2', () => {
 // Test assertion
 expect(add(1,1,)).toBe(2);
 });

 it('subtract(2,1)', () =>{
 //Test assertion
 expect(subtract(2,1)).toBe(1);
 })
});
```

在前面的例子中，`add(1,1)`将返回实际值，这个值应该与`toBe()`匹配器函数中声明的期望值相匹配。

在前面的例子中值得注意的是添加了第二个测试，测试了我们的`subtract()`函数。我们可以清楚地看到，这个测试处理了另一个数学运算，因此将这两个测试分组在一个套件下是有意义的。

到目前为止，我们已经了解了测试套件以及如何根据其功能对测试进行分组。此外，我们已经了解了调用要测试的代码并断言它是否按照你所想的那样做的概念。然而，单元测试还有更多值得了解的概念，即设置和拆卸功能。设置功能是在测试运行之前设置代码的功能。这是一种使代码更清晰的方式，因此您可以专注于调用代码和断言。拆卸功能是设置功能的相反，专门用于拆卸最初设置的内容；本质上，这是一种在测试后进行清理的方式。让我们看看这在实践中是什么样子，使用 Jasmine 框架的代码示例。在 Jasmine 中，`beforeEach()`方法用于设置功能；它在每个单元测试之前运行。`afterEach()`方法用于运行拆卸逻辑。代码如下：

```ts
describe('a Product service', () => {
 let productService;

 beforeEach(() => {
 productService = new ProductService(); 
 });

 it('should return data', () => {
 let actual = productService.getData();
 assert(actual.length).toBe(1);
 });

 afterEach(() => {
 productService = null; 
 });
});
```

我们可以在前面的代码中看到`beforeEach()`函数负责实例化`productService`，这意味着测试只需要关心调用生产代码和断言结果。这使得测试看起来更清晰。不过，实际上，测试往往需要进行大量的设置，有一个`beforeEach()`函数可以使测试看起来更清晰；最重要的是，它往往使添加新测试变得更容易，这是很棒的。最终你想要的是经过充分测试的代码；编写和维护这样的代码越容易，对你的软件就越好。

# 在 Angular 中进行测试的介绍

在*单元测试的解剖*部分，我们熟悉了单元测试及其一般概念，比如测试套件、测试规范和断言。掌握了这些知识，现在是时候深入了解在 Angular 中进行单元测试了。不过，在我们开始为 Angular 编写测试之前，我们将首先介绍 Angular CLI 中存在的工具，以使单元测试成为一种愉快的体验。在 Angular 中进行单元测试时，了解它由哪些主要部分组成是很重要的。在 Angular 中，这些部分包括：

+   Jasmine，测试框架

+   Angular 测试工具

+   Karma，一个用于运行单元测试的测试运行器，还有其他功能

+   Protractor，Angular 的 E2E 测试框架

# 配置和设置

在配置方面，当使用 Angular CLI 时，你不需要做任何事情来使其工作。一旦你搭建一个项目，就可以运行你的第一个测试，它就会工作。当你深入研究 Angular 中的单元测试时，你需要了解一些概念，这些概念可以提高你测试不同构造的能力，比如组件和指令。Angular CLI 使用 Karma 作为测试运行器。关于 Karma 我们需要知道的是它使用一个`karma.conf.js`文件，一个配置文件，其中指定了很多东西，比如：

+   增强你的测试运行器的各种插件。

+   在哪里找到要运行的测试？应该说的是，通常在这个文件中有一个 files 属性，指定了在哪里找到应用程序和测试。然而，对于 Angular CLI，这个规范是在另一个名为`src/tscconfig-spec.json`的文件中找到的。

+   你选择的覆盖工具的设置，一个衡量你的测试覆盖生产代码程度的工具。

+   报告者，在控制台窗口、浏览器或其他方式中报告每个执行的测试。

+   用于运行测试的浏览器：例如，Chrome 或 PhantomJS。

使用 Angular CLI，您很可能不需要自己更改或编辑此文件。知道它的存在以及它为您做了什么是很好的。

# Angular 测试工具

Angular 测试工具有助于创建一个测试环境，使得为各种构造编写测试变得非常容易。它由`TestBed`类和各种辅助函数组成，位于`@angular/core/testing`命名空间下。随着本章的进行，我们将学习这些是什么以及它们如何帮助我们测试各种构造。我们将很快介绍最常用的概念，以便在我们进一步介绍它们时您对它们有所了解：

+   `TestBed`类是最重要的概念，它创建自己的测试模块。实际上，当您测试一个构造以将其从所在的模块中分离出来并重新连接到`TestBed`创建的测试模块时。`TestBed`类有一个`configureTestModule()`辅助方法，我们用它来设置所需的测试模块。`TestBed`还可以实例化组件。

+   `ComponentFixture`是一个包装组件实例的类。这意味着它具有一些功能，并且它有一个成员，即组件实例本身。

+   `DebugElement`，就像`ComponentFixture`一样，充当包装器。但是，它包装的是 DOM 元素，而不是组件实例。它还有一个注入器，允许我们访问已注入到组件中的服务。稍后会详细介绍这个主题。

这是对我们的测试环境、使用的框架和库以及一些重要概念的简要概述，我们将在接下来的部分中大量使用它们。

# 组件测试简介

到目前为止，我们进行任何 Angular 操作的通常方法是使用 Angular CLI。处理测试也不例外。Angular CLI 让我们创建测试，调试它们并运行它们；它还让我们了解我们的测试覆盖了代码及其许多场景的程度。让我们快速看一下如何使用 Angular CLI 进行单元测试，并尝试理解默认情况下给我们的内容。

如果您想跟着本章的代码进行编写，可以使用旧的 Angular 项目并为其添加测试，或者创建一个新的独立项目，如果您只想专注于实践测试。选择权在您。

如果您选择创建一个新项目，然后键入以下内容进行搭建：

```ts
ng new AngularTestDemo
// go make coffee :)
cd AngularTestDemo
ng serve
```

Angular CLI 已经设置好了测试，所以我们需要做的唯一的事情就是跟随它的步伐并添加更多的测试，但让我们首先检查一下我们已经得到了什么，并学习一些很棒的命令，以使测试工作更容易。

我们想要做的第一件事是：

+   调查 Angular CLI 给我们的测试

+   运行测试

通过查看搭建的`directory/app`，我们看到了以下内容：

```ts
app.component.ts
app.component.spec.ts
```

我们看到一个组件被声明，连同一个单元测试。这意味着我们可以对我们的组件进行测试，这是非常好的消息，因为它节省了我们一些输入。

让我们看一下给我们的测试：

```ts
import { TestBed, async } from  '@angular/core/testing'; import { AppComponent } from  './app.component'; 
describe('AppComponent', () => {  beforeEach(async(() => { TestBed.configureTestingModule({ declarations: [ AppComponent
 ],
 }).compileComponents();
 }));

 it('should create the app', async(() => { const  fixture  =  TestBed.createComponent(AppComponent); const  app  =  fixture.debugElement.componentInstance; expect(app).toBeTruthy();
 }));

 it(`should have as title 'app works!'`, async(() => { const  fixture  =  TestBed.createComponent(AppComponent); const  app  =  fixture.debugElement.componentInstance; expect(app.title).toEqual('app works!'); }));

 it('should render title in a h1 tag', async(() => { const  fixture  =  TestBed.createComponent(AppComponent); fixture.detectChanges(); const  compiled  =  fixture.debugElement.nativeElement;
 const actual = compiled.querySelector('h1').textContent; expect(actual).toContain('app works!');
  }));
});
```

这是很多代码，但我们会逐步分解它。我们看到在文件的开头有测试设置，编写了三个不同的测试。让我们先看一下设置阶段：

```ts
beforeEach(async(() => { TestBed.configureTestingModule({ declarations: [ AppComponent
 ],
 }).compileComponents(); }));
```

在这里我们调用`beforeEach()`，就像我们在 Jasmine 测试中通常做的那样，以便在每个测试实际发生之前运行代码。在`beforeEach()`中，我们调用`TestBed.configureTestingModule()`方法，带有一个对象作为参数。这个对象类似于我们给`NgModule`作为参数的对象。这意味着我们可以利用我们对`NgModule`以及如何设置 Angular 模块的知识，并将其应用到如何设置测试模块，因为它实际上是一样的。从代码中可以看出，我们指定了一个包含`AppComponent`的声明数组。对于`NgModule`来说，这意味着`AppComponent`属于该模块。最后，我们调用了`compileComponents()`方法，设置完成。

那么`compileComponents()`是做什么的呢？根据它的名称，它编译了在测试模块中配置的组件。在编译过程中，它还内联外部 CSS 文件以及外部模板。通过调用`compileComponents()`，我们也关闭了进一步配置测试模块实例的可能性。

我们测试文件的第二部分是测试。看一下第一个测试：

```ts
it('should create the app', async(() => {
&gt; const  fixture  =  TestBed.createComponent(AppComponent); const  app  =  fixture.componentInstance; expect(app).toBeTruthy(); }));
```

我们看到我们调用了`TestBed.createComponent(AppComponent)`，这返回一个类型为`ComponentFixture<AppComponent>`的对象。我们可以通过调用这个对象进一步进行交互：

```ts
const  app  =  fixture.debugElement.componentInstance;
```

这给了我们一个组件实例，这就是当我们从以下类实例化一个对象时得到的东西：

```ts
@Component({})
export class AppComponent {
 title: string;
}
```

第一个测试只是想验证我们能否创建一个组件，`expect`条件测试的就是这个，即`expect(app)`是真实的，意思是它是否被声明；而事实上它是。

对于第二个测试，我们实际上是要调查我们的组件是否包含我们认为的属性和值；所以测试看起来像这样：

```ts
it(`should have as title 'app works!'`, async(() => { const  fixture  =  TestBed.createComponent(AppComponent); const  app  =  fixture.debugElement.componentInstance; expect(app.title).toEqual('app works!'); }));
```

现在，这个测试创建了一个组件，但它也调用了`fixture.detectChanges`，这告诉 Angular 强制进行变更检测。这将确保构造函数中的代码和任何`ngInit()`（如果存在）都被执行。

通过组件规范，我们期望在创建组件时`title`属性应该被设置，就像这样：

```ts
@Component({})
export class AppComponent {
 title: string = 'app works!'
}
```

这正是第二个测试正在测试的：

```ts
expect(app.title).toEqual('app works!');
```

让我们看看如何通过在`app.component.ts`中添加一个字段来扩展它的功能：

```ts
@Component({})
export class AppComponent {
 title: string;
 description: string;
 constructor() {
 this.title = 'app works'
    this.description ='description';
 }
}
```

我们添加了描述字段，并用一个值进行了初始化；我们将测试这个值是否设置为我们的属性。因此，我们需要在我们的测试中添加额外的`expect`条件，所以测试现在看起来像这样：

```ts
it(`should have as title 'app works!'`, async(() => { const  fixture  =  TestBed.createComponent(AppComponent); const  app  =  fixture.debugElement.componentInstance; expect(app.title).toEqual('app works!');
   **expect(app.description).toEqual('description');** }));
```

正如你所看到的，我们有了额外的`expect`条件，测试通过了，这正是应该的。不过，不要只听我们的话；让我们使用 node 命令运行我们的测试运行程序。我们通过输入以下内容来做到这一点：

```ts
npm test
```

这将执行测试运行程序，应该看起来像这样：

![](img/c1d52a8b-e0d5-421b-9c89-baf25a27f352.png)

这意味着我们知道如何扩展我们的组件并对其进行测试。作为奖励，我们现在也知道如何运行我们的测试。让我们看看第三个测试。它有点不同，因为它测试模板：

```ts
it('should render title in a h1 tag', async(() => { const  fixture  =  TestBed.createComponent(AppComponent); fixture.detectChanges(); const  compiled  =  fixture.debugElement.nativeElement; expect(compiled.querySelector('h1').textContent).toContain('app works!'); }));
```

我们不再与`fixture.debugElement.componentInstance`交谈，而是与`fixture.debugElement.nativeElement`交谈。这将允许我们验证预期的 HTML 标记是否与我们认为的一样。当我们可以访问`nativeElement`时，我们可以使用`querySelector`并找到我们在模板中定义的元素并验证它们的内容。

通过查看我们得到的测试，我们获得了很多见解。我们现在知道以下内容：

+   我们通过调用`TestBed.configureTestingModule()`并传递一个类似于我们传递给`NgModule`的对象来设置测试

+   我们调用`TestBed.createComponent(<Component>)`来获取对组件的引用

+   我们调用`debugElement.componentInstance`来获取到实际的组件，我们可以测试组件对象上应该存在的属性的存在和值

+   我们调用`debugElement.nativeElement`来获取对`nativeElement`的引用，现在可以开始验证生成的 HTML

+   我们还学会了如何通过输入`npm test`在浏览器中运行我们的测试

`fixture.debugElement.nativeElement`指向 HTML 元素本身。当我们使用`querySelector()`方法时，实际上使用的是 Web API 中可用的方法；这不是 Angular 方法。

# 具有依赖关系的组件测试

我们已经学到了很多，但让我们面对现实，我们构建的任何组件都不会像我们在前面的部分中编写的那样简单。几乎肯定会至少有一个依赖项，看起来像这样：

```ts
@Component({})
export class ExampleComponent {
 constructor(dependency:Dependency) {}
}
```

我们有不同的方法来处理测试这样的情况。不过有一点是清楚的：如果我们正在测试组件，那么我们不应该同时测试服务。这意味着当我们设置这样的测试时，依赖项不应该是真正的东西。在进行单元测试时，处理这种情况有不同的方法；没有一种解决方案比另一种严格更好：

+   使用存根意味着我们告诉依赖注入器注入我们提供的存根，而不是真正的东西

+   注入真正的东西，但附加一个间谍，调用我们组件中的方法

无论采用何种方法，我们都确保测试不会执行诸如与文件系统交谈或尝试通过 HTTP 进行通信等副作用；使用这种方法，我们是隔离的。

# 使用存根来替换依赖项

使用存根意味着我们完全替换了以前的东西。指导`TestBed`进行这样的操作就像这样简单：

```ts
TestBed.configureTestingModule({
 declarations: [ExampleComponent]
 providers: [{ 
 provide: DependencyService, 
 useClass: DependencyServiceStub 
 }]
});
```

我们像在`NgModule`中那样定义一个`providers`数组，并给它一个指出我们打算替换的定义的列表项，然后给它替换；那就是我们的存根。

现在让我们构建我们的`DependencyStub`看起来像这样：

```ts
class DependencyServiceStub {
 getData() { return 'stub'; }
}
```

就像使用`@NgModule`一样，我们能够用我们自己的存根覆盖我们的依赖的定义。想象一下我们的组件看起来像下面这样：

```ts
import { Component } from  '@angular/core'; import { DependencyService } from  "./dependency.service"; 
@Component({
 selector:  'example',
 template: `
 <div>{{ title }}</div>
 `
})
export  class  ExampleComponent { title:  string; 
 constructor(private  dependency:  DependencyService) {
 this.title  =  this.dependency.getData();
 }
}
```

在构造函数中传递依赖的一个实例。通过正确设置我们的测试模块，使用我们的存根，我们现在可以编写一个像这样的测试：

```ts
it(`should have as title 'stub'`, async(() => { const  fixture  =  TestBed.createComponent(AppComponent); const  app  =  fixture.debugElement.componentInstance; expect(app.title).toEqual('stub'**);** }));
```

测试看起来正常，但在组件代码中调用依赖项时，我们的存根会代替它并做出响应。我们的依赖应该被覆盖，正如你所看到的，`expect(app.title).toEqual('stub')`假设存根会回答，而它确实会回答。

# 对依赖方法进行间谍监视

前面提到的使用存根的方法并不是在单元测试中隔离自己的唯一方法。我们不必替换整个依赖项，只需替换组件正在使用的部分。替换某些部分意味着我们指出依赖项上的特定方法，并对其进行间谍监视。间谍是一个有趣的构造；它有能力回答你想要的问题，但你也可以看到它被调用了多少次以及使用了什么参数，因此间谍可以为你提供更多关于发生了什么的信息。让我们看看我们如何设置一个间谍：

```ts
beforeEach(() => {
 TestBed.configureTestingModule({
 declarations: [ExampleComponent],
 providers: [DependencyService]
 });

 dependency = TestBed.get(DependencyService);

 spy = spyOn( dependency,'getData');
 fixture = TestBed.createComponent(ExampleComponent);
})

```

现在你可以看到，实际的依赖项被注入到了组件中。之后，我们获取了组件的引用，即我们的 fixture 变量。然后，我们使用`TestBed.get('Dependency')`来获取组件内的依赖项。在这一点上，我们通过`spyOn( dependency,'getData')`来对其`getData()`方法进行间谍监视。

然而，这还不够；我们还需要指示间谍在被调用时如何回应。让我们来做到这一点：

```ts
spyOn(dependency,'getData').and.returnValue('spy value');
```

现在我们可以像往常一样编写我们的测试：

```ts
it('test our spy dependency', () => {
 var component = fixture.debugElement.componentInstance;
 expect(component.title).toBe('spy value');
});
```

这符合预期，我们的间谍回应得当。还记得我们说过间谍不仅能够回应一个值，还能够检查它们是否被调用以及使用了什么吗？为了展示这一点，我们需要稍微改进我们的测试，并检查这个扩展功能，就像这样：

```ts
it('test our spy dependency', () => {
 var component = fixture.debugElement.componentInstance;
 expect(spy.calls.any()).toBeTruthy();
})
```

您还可以检查它被调用的次数，使用`spy.callCount`，或者它是否被调用以及具体的参数：`spy.mostRecentCalls.args`或`spy.toHaveBeenCalledWith('arg1', 'arg2')`。请记住，如果您使用间谍，请确保它通过您需要进行这些检查来支付自己的代价；否则，您可能还不如使用存根。

间谍是 Jasmine 框架的一个特性，而不是 Angular。建议感兴趣的读者在[`tobyho.com/2011/12/15/jasmine-spy-cheatsheet/`](http://tobyho.com/2011/12/15/jasmine-spy-cheatsheet/)上进一步研究这个主题。

# 异步服务

很少有服务是良好且行为端正的，就是它们是同步的意义上。大部分时间，您的服务将是异步的，而从中返回的最可能是一个 observable 或一个 promise。如果您正在使用 RxJS 与`Http`服务或`HttpClient`，它将是一个 observable，但如果使用`fetch`API，它将是一个 promise。这两种处理 HTTP 的方法都很好，但 Angular 团队将 RxJS 库添加到 Angular 中，以使开发人员的生活更轻松。最终由您决定，但我们建议使用 RxJS。

Angular 已经准备好了两种构造来处理测试时的异步场景。

+   `async()`和`whenStable()`：这段代码确保任何承诺都会立即解决；尽管看起来更同步

+   `fakeAsync()`和`tick()`：这段代码做了 async 的事情，但在使用时看起来更同步

让我们描述一下`async()`和`whenStable()`的方法。当我们调用服务时，我们的服务现在已经成熟并且正在执行一些异步操作，比如超时或 HTTP 调用。无论如何，答案不会立即传达给我们。然而，通过结合使用`async()`和`whenStable()`，我们可以确保任何承诺都会立即解决。想象一下我们的服务现在是这样的：

```ts
export class AsyncDependencyService {
 getData(): Promise<string> {
 return new Promise((resolve, reject) => {
 setTimeout(() => { resolve('data') }, 3000);
 })
 }
}
```

我们需要更改我们的 spy 设置，以返回一个 promise 而不是返回一个静态字符串，就像这样：

```ts
spy = spyOn(dependency,'getData')
.and.returnValue(Promise.resolve('spy data'));
```

我们确实需要在我们的组件内部进行更改，就像这样：

```ts
import { Component, OnInit } from  '@angular/core'; import { AsyncDependencyService } from  "./async.dependency.service"; @Component({
 selector:  'async-example',
 template: `
 <div>{{ title }}</div>
 `
})
export  class  AsyncExampleComponent { title:  string; 
 constructor(private  service:  AsyncDependencyService) {
 this.service.getData().then(data  =>  this.title  =  data);
 }
}
```

此时，是时候更新我们的测试了。我们需要做两件事。我们需要告诉我们的测试方法使用`async()`函数，就像这样：

```ts
it('async test', async() => {
 // the test body
})
```

我们还需要调用`fixture.whenStable()`，以确保 promise 有足够的时间来解决，就像这样：

```ts
import { TestBed } from  "@angular/core/testing"; import { AsyncExampleComponent } from  "./async.example.component"; import { AsyncDependencyService } from  "./async.dependency.service"; 
describe('test an component with an async service', () => { let  fixture;

 beforeEach(() => { TestBed.configureTestingModule({
 declarations: [AsyncExampleComponent],
 providers: [AsyncDependencyService]
 });

 fixture  =  TestBed.createComponent(AsyncExampleComponent);
 });

 it('should contain async data', async () => { const  component  =  fixture.componentInstance;
    fixture.whenStable.then(() => {
 fixture.detectChanges();
 expect(component.title).toBe('async data');
 });
 });
});
```

这种做法可以正常工作，但感觉有点笨拙。还有另一种方法，使用`fakeAsync()`和`tick()`。基本上，`fakeAsync()`替换了`async()`调用，我们摆脱了`whenStable()`。然而，最大的好处是我们不再需要将断言语句放在 promise 的`then()`回调中。这给我们提供了看起来是同步的代码。回到`fakeAsync()`，我们需要调用`tick()`，它只能在`fakeAsync()`调用内部调用，就像这样：

```ts
it('async test', fakeAsync() => {
 let component = fixture.componentInstance;
 fixture.detectChanges();
 fixture.tick();
 expect(component.title).toBe('spy data');
});
```

正如您所看到的，这看起来更清晰；您想要使用哪个版本进行异步测试取决于您。

# 测试管道

管道基本上是实现`PipeTransform`接口的类，因此公开了通常是同步的`transform()`方法。因此，管道非常容易测试。我们将从测试一个简单的管道开始，创建一个测试规范，就像我们提到的，紧挨着它的代码单元文件。代码如下：

```ts
import { Pipe, PipeTransform } from  '@angular/core'; 
@Pipe({
  name:  'formattedpipe' })
export  class  FormattedPipe  implements  PipeTransform { transform(value:  any, ...args:  any[]):  any { return  "banana"  +  value; }
}
```

我们的代码非常简单；我们取一个值并添加`banana`。为它编写一个测试同样简单。我们需要做的唯一一件事就是导入管道并验证两件事：

+   它是否有一个 transform 方法

+   它产生了预期的结果

以下代码为前面列出的每个要点编写了一个测试：

```ts
import FormattedTimePipe from './formatted-time.pipe';
import { TestBed } from  '@angular/core/testing';

describe('A formatted time pipe' , () => {
 let fixture;
 beforeEach(() => {
 fixture = new FormattedTimePipe();
 }) // Specs with assertions
  it('should expose a transform() method', () => {
 expect(typeof formattedTimePipe.transform).toEqual('function');
 });

  it('should produce expected result', () => {
 expect(fixture.transform( 'val' )).toBe('bananaval');
 })
});
```

在我们的`beforeEach()`方法中，我们通过实例化管道类来设置 fixture。在第一个测试中，我们确保`transform()`方法存在。接着是我们的第二个测试，断言`transform()`方法产生了预期的结果。

# 使用 HttpClientTestingController 模拟 HTTP 响应

一旦你理解了如何开始模拟 HTTP，就会变得非常简单。让我们首先看一下我们打算测试的服务：

```ts
import { HttpClient } from  '@angular/common/http';  import { Injectable } from  '@angular/core'; @Injectable() export  class  JediService { apiUrl:  string  =  'something'; constructor(private  http:  HttpClient) {} getJedis() { return  this.http.get(`/api/jedis`); }
}
```

在测试我们的服务时，有两个重要的参与者：

+   `HttpTestingController`，我们可以指示这个类监听特定的 URL 以及在被调用时如何做出响应

+   我们要测试的是我们的服务；我们真正想要做的唯一一件事就是调用它

与所有测试一样，我们有一个设置阶段。在这里，我们需要导入包含我们的`HttpTestingController`的模块`HttpClientTestingModule`。我们还需要告诉它为我们提供服务，就像这样：

```ts
import { 
 HttpClientTestingModule, 
 HttpTestingController 
} from '@angular/common/http/testing';
import { JediService } from  './jedi.service'; describe('testing our service', () => {
 beforeEach(() => {
 TestBed.configureTestingModule({
     imports: [HttpClientTestingModule],
 providers: [JediService] 
 });
 });
});
```

下一步是设置测试，通过设置我们需要获取我们的服务的实例以及`HttpTestingController`来设置。我们还需要指示后者期望的 API 调用类型，并提供适当的模拟数据以做出响应：

```ts
it('testing getJedis() and expect a list of jedis back', () => {
 // get an instance of a Jedi service and HttpTestingController
  const jediService = TestBed.get(JediService);
  const http = TestBed.get(HttpTestingController);

 // define our mock data
 const  expected  = [{ name:  'Luke' }, { name:  'Darth Vader' }]; let  actual  = [];

 // we actively call getJedis() on jediService, 
 // we will set that response to our 'actual' variable jediService.getJedis().subscribe( data  => {  expect(data).toEqual(expected**);**  });

 /* 
 when someone calls URL /api/jedis 
 we will resolve that asynchronous operation 
 with .flush() while also answering with 
 'expected' variable as response data
 */
 http.expectOne('/api/jedis').flush(expected);  });
```

我们为前面的代码片段提供了内联注释，但只是为了再次描述发生了什么，我们的测试有三个阶段：

1.  **安排**：这是我们获取`JediService`实例以及`HttpTestingController`实例的地方。我们还通过设置`expected`变量来定义我们的模拟数据。

1.  **行动**：我们通过调用`jediService.getJedis()`来执行测试。这是一个 observable，所以我们需要订阅它的内容。

1.  **断言**：我们通过调用`flush(expected)`来解析异步代码，并断言我们通过进行断言`expect(actual).toEqual(expected)`得到了正确的数据。

如您所见，伪造对 HTTP 的调用非常容易。让我们展示整个单元测试代码：

```ts
import { HttpTestingController, 
 HttpClientTestingModule } from  '@angular/common/http/testing/'; import { TestBed } from '@angular/core/testing'; import { JediService } from  './jedi-service';    describe('a jedi service', () => {  beforeEach(() =>  TestBed.configureTestingModule({ imports: [HttpClientTestingModule], providers: [JediService] }));

 it('should list the jedis', () => { const  jediService  =  TestBed.get(JediService); const  http  =  TestBed.get(HttpTestingController); // fake response
 const  expected  = [{ name:  'Luke' }, { name:  'Darth Vader' }]; let  actual  = []; jediService.getJedis().subscribe( data  => { expect(data).toEqual(expected); });

 http.expectOne('/api/jedis').flush(expected);  });
});
```

# 输入和输出

到目前为止，我们已经测试了组件，即我们已经测试了组件上的简单属性以及如何处理依赖项，同步和异步，但组件还有更多内容。组件还可以具有应该进行测试的输入和输出。因为我们的上下文是绝地武士，我们知道绝地武士通常有方法可以转向光明面或黑暗面。想象一下我们的组件在绝地管理系统的上下文中使用；我们希望能够将绝地武士转向黑暗面，也能够将其带回光明面。我们讨论的当然是切换功能。

因此，想象一下我们有一个看起来像这样的组件：

```ts
@Component({
 selector : 'jedi-detail'
 template : `
 <div class="jedi" 
 (click)="switchSide.emit(jedi)">
 {{ jedi.name }} {{ jedi.side }}
 </div>
 `
})
export class JediComponent {
 @Input() jedi:Jedi;
 @Output() switchSide = new EventEmitter<Jedi>(); 
}
```

测试这样一个组件应该以两种方式进行：

+   我们应该验证我们的输入绑定是否正确设置

+   我们应该验证我们的输出绑定是否正确触发，以及接收到的内容

从`@Input`开始，对其进行测试如下：

```ts
describe('A Jedi detail component', () => {
 it('should display the jedi name Luke when input is assigned a Jedi object', () => {
 const component = fixture.debugElement.componentInstance;
 component.jedi = new Jedi(1, 'Luke', 'Light');
 fixture.detectChanges();
 expect(component.jedi.name).toBe('Luke');
 });
});

```

这里值得注意的是我们对`fixture.detectChanges()`的调用，这确保了绑定发生在组件中。

让我们现在来看看如何测试`@Output`。我们需要做的是以某种方式触发它。我们需要点击模板中定义的 div。为了接收`switchSide`属性发出的值，我们需要订阅它，所以我们需要做两件事：

+   找到`div`元素并触发点击

+   订阅数据的发射并验证我们是否收到了`jedi`对象

至于获取 div 的引用，可以很容易地完成，如下所示：

```ts
const elem = fixture.debugElement.query(By.css('.jedi'));
elem.triggerEventHandler('click', null);
```

对于第二部分，我们需要订阅`switchSide` Observable 并捕获数据，如下所示：

```ts
it('should invoke switchSide with the correct Jedi instance, () => {
 let selectedJedi;
 // emitting data
 component.switchSide.subscribe(data => {
 expect(data.name).toBe('Luke');
 });
 const elem = fixture.debugElement.query(By.css('.jedi'));
 elem.triggerEventHandler('click', null);
})
```

通过这段代码，我们能够间接触发输出的发射，通过点击事件监听输出，通过订阅。

# 测试路由

就像组件一样，路由在我们的应用程序提供高效用户体验方面发挥着重要作用。因此，测试路由变得至关重要，以确保无缝的性能。我们可以对路由进行不同的测试，并且需要针对不同的场景进行测试。这些场景包括：

+   确保导航指向正确的路由地址

+   确保正确的参数可用，以便您可以为组件获取正确的数据，或者过滤组件需要的数据集

+   确保某个路由最终加载预期的组件

# 测试导航

让我们看看第一个要点。要加载特定路由，我们可以在`Router`类上调用`navigateToUrl(url)`方法。一个很好的测试是确保当组件中发生某种状态时，会调用这样的方法。例如，可能会有一个创建组件页面，在保存后应该导航回到列表页面，或者缺少路由参数应该导航回到主页。在组件内部进行程序化导航有多个很好的理由。让我们看一些组件中的代码，其中进行这样的导航：

```ts
@Component({})
export class ExampleComponent {
 constructor(private router: Router) {}

 back() {
 this.router.navigateByUrl('/list'); 
 }
}
```

在这里我们可以看到调用`back()`方法将执行导航。为此编写测试非常简单。测试应该测试`navigateToUrl()`方法是否被调用。我们的方法将包括在路由服务中存根化以及在`navigateToUrl()`方法本身上添加一个间谍。首先，我们定义一个存根，然后指示我们的测试模块使用该存根。我们还确保我们创建了组件的一个实例，以便稍后在其上调用`back()`方法，就像这样：

```ts
describe('Testing routing in a component using a Stub', () => {
 let component, fixture;

 class RouterStub {
 navigateByUrl() {}
 } 

 beforeEach(() => {
 TestBed.configureTestingModule({
 declarations: [ExampleRoutingComponent],
 providers: [{
 // replace 'Router' with our Stub
        provide: Router, useClass: RouterStub
 }]
 }).compileComponents();
 })

 beforeEach(() => {
 fixture  =  TestBed.createComponent(Component); component  =  fixture.debugElement.componentInstance;
 })
 // ... test to be defined here
}

```

接下来我们需要做的是定义我们的测试并注入路由实例。一旦我们这样做了，我们就可以在`navigateToUrl()`方法上设置一个间谍：

```ts
import { inject } from '@angular/core/testing';

it('test back() method', inject([Router], router: Router) => {
 const spy = spyOn(router, 'navigateByUrl');
 // ... more to come here
})
```

现在在这一点上，我们希望测试测试的是方法是否被调用。编写这样的测试可以被视为防御性的。和测试正确性一样重要的是，编写测试以确保另一个开发人员，或者你自己，不会删除应该工作的行为。因此，让我们添加一些验证逻辑，以确保我们的间谍被调用：

```ts
import { inject } from '@angular/core/testing';

it('test back() method', inject([Router], (router: Router)) => {
 const spy = spyOn(router, 'navigateByUrl');
 // invoking  our back method that should call the spy in turn
 component.back();
 expect(spy.calls.any()).toBe(true);
}))
```

整个测试现在是用存根替换原始的路由服务。我们在存根上的`navigateByUrl()`方法上附加了一个间谍，最后我们断言该间谍在调用`back()`方法时被调用如预期：

```ts
describe('Testing routing in a component', () => {
 class RouterStub {
 navigateByUrl() {}
 }

 beforeEach(() => {
 TestBed.configureTestingModule({
 providers: [{
 provide: Router, useClass: RouterStub
 }]
 }).compileComponents();
 });

 beforeEach(() => {
 fixture  =  TestBed.createComponent(Component); component  =  fixture.debugElement.componentInstance;
 });

 it('should call navigateToUrl with argument /list', () => {
 spyOn(router, 'navigateByUrl');
 /* 
 invoking our back() method 
 that should call the spy in turn
 */
 component.back();
 expect(router.navigateByUrl).toHaveBeenCalledWithArgs('/list');
 })
})
```

# 通过 URL 测试路由

到目前为止，我们已经通过在导航方法上放置间谍来测试路由，并且在具有路由参数的情况下，我们必须为 Observable 构建一个模拟。不过，还有另一种方法，那就是让路由发生，然后调查我们最终停留在哪里。假设我们有以下情景：我们在列表组件上，想要导航到详细组件。导航发生后，我们想要调查我们所处的状态。让我们首先定义我们的列表组件：

```ts
import { Router } from  '@angular/router'; import { Component, OnInit } from  '@angular/core'; 
@Component({
  selector:  'list-component', template : `` })
export  class  ListComponent {
 constructor(private  router:  Router) {}

 goToDetail() { this.router.navigateByUrl('detail/1'); }  }  
```

如您所见，我们有一个`goToDetail()`方法，如果调用，将会将您导航到一个新的路由。但是，为了使其工作，我们需要在模块文件中正确设置路由，如下所示：

```ts
const  appRoutes:  Routes  = [ { path:  'detail/:id', component:  DetailComponent } ];

@NgModule({
 ...
 imports: [ BrowserModule, FormsModule, HttpClientModule, RouterModule.forRoot(appRoutes**),** TestModule
 ],
 ...  })
export  class  AppModule { }
```

重要的部分在于`appRoutes`的定义和在导入数组中调用`RouterModule.forRoot()`。

现在是定义此测试的时候了。我们需要与一个名为`RouterTestingModule`的模块进行交互，并且我们需要为该模块提供应该包含的路由。`RouterTestingModule`是一个非常合格的路由存根版本，因此从原则上讲，与创建自己的存根没有太大区别。不过，可以这样看待，您可以创建自己的存根，但随着您使用越来越多的高级功能，使用高级存根很快就会得到回报。

我们将首先指示我们的`RouterTestingModule`，当命中`detail/:id`路由时，它应该加载`DetailComponent`。这与我们如何从我们的`root`模块设置路由没有太大区别。好处在于，我们只需要为我们的测试设置我们需要的路由，而不是应用中的每一个路由都需要设置：

```ts
beforeEach(() => { TestBed.configureTestingModule({ imports: [ RouterTestingModule.withRoutes([{ 
 path:  'detail/:id', 
 component:  DetailComponent }]) 
 ], declarations: [ListComponent, DetailComponent] });
});
```

完成设置后，我们需要在测试中获取组件的副本，以便调用将我们从列表组件导航出去的方法。您的测试应该如下所示：

```ts
it('should navigate to /detail/1 when invoking gotoDetail()', async() => { let  fixture  =  TestBed.createComponent(ListComponent); let  router =  TestBed.get(Router); let  component  =  fixture.debugElement.componentInstance;
  fixture.whenStable().then(() => { expect(router.url).toBe('/detail/1');
 });
  **component.goToDetail();** }) 
```

这里重要的部分是调用使我们导航的方法：

```ts
component.goToDetail();
```

以及我们验证我们的路由器确实已经改变状态的断言：

```ts
expect(router.url).toBe('/detail/1');
```

# 测试路由参数

您将拥有一些执行路由的组件和一些被路由到的组件。有时，被路由到的组件会有一个参数，通常它们的路由看起来像这样：`/jedis/:id`。然后，组件的任务是挖出 ID 参数，并在匹配此 ID 的具体绝地武士上进行查找。因此，将调用一个服务，并且响应应该填充我们组件中的适当参数，然后我们可以在模板中显示。这样的组件通常看起来像这样：

```ts
import { ActivatedRoute, Router } from  '@angular/router'; import { Component, OnInit } from  '@angular/core'; import { Observable } from  'rxjs/Rx'; import { Jedi } from  './jedi.model'; import { JediService } from  './jedi.service';   @Component({
  selector:  'detail-component', templateUrl:  'detail.component.html' })
export  class ExampleRoutingParamsComponent{
 jedi: Jedi; constructor( private  router:  Router, private  route:  ActivatedRoute, private  jediService  :  JediService ) {
 route.paramMap.subscribe( p  => { const  id  =  p.get('id'); jediService.getJedi( id ).subscribe( data => this.jedi = data ); });
 }  }  
```

值得强调的是我们如何获取路由中的参数。我们与`ActivatedRouter`实例交互，我们将其命名为`route`，以及它的`paramMap`属性，这是一个可观察对象，如下所示：

```ts
route.paramMap.subscribe( p  => { const  id  =  p.get('id'); jediService.getJedi(id).subscribe( data => this.jedi = data ) })
```

那么我们想要测试什么呢？我们想知道，如果某个路由包含一个 ID 参数，那么我们的`jedi`属性应该通过我们的服务正确填充。我们不想进行实际的 HTTP 调用，因此我们的`JediService`需要以某种方式进行模拟，并且还有另一件使事情复杂化的事情，即`route.paramMap`也需要被模拟，而那个东西是一个可观察对象。

这意味着我们需要一种创建可观察对象存根的方法。这可能听起来有点令人生畏，但实际上并不是；多亏了`Subject`，我们可以很容易地做到这一点。`Subject`具有一个很好的能力，即我们可以订阅它，但我们也可以向它传递值。有了这个知识，让我们开始创建我们的`ActivatedRouteStub`：

```ts
import  { convertToParamMap  }  from  '@angular/router';

class ActivatedRouteStub {
 private subject: Subject<any>;

 constructor() {
 this.subject = new Subject();
 }

 sendParameters( params : {}) {
 this.subject.next(convertToParamMap(params)); // emitting data
 }

 get paramMap() {
 return this.subject.asObservable();
 }
}
```

现在，让我们解释一下这段代码，我们添加了`sendValue()`方法，以便它可以将我们给它的值传递给主题。我们公开了`paramMap`属性，作为一个可观察对象，这样我们就可以在主题发出任何值时监听它。但这如何与我们的测试相关呢？嗯，在存储阶段，我们希望在`beforeEach()`内调用存根的`sendValue`。这是我们模拟通过路由到达我们的组件并传递参数的一种方式。在测试本身中，我们希望监听路由参数何时被发送给我们，以便我们可以将其传递给我们的`jediService`。因此，让我们开始勾勒测试。我们将分两步构建测试：

1.  第一步是通过传递`ActivatedRouteStub`来支持对`ActivatedRoute`的模拟。

1.  第二步是设置`jediService`的模拟，确保拦截所有 HTTP 调用，并且当发生 HTTP 调用时我们能够用模拟数据做出响应。

首先，我们设置测试，就像我们迄今为止所做的那样，调用`TestBed.configureTestingModule()`并传递一个对象。我们提到我们已经为激活的路由构建了一个存根，并且我们需要确保提供这个存根而不是真正的`ActivatedRoute`。代码如下所示：

```ts
describe('A detail component', () => {
 let fixture, component, activatedRoute;

 beforeEach(() => {
 TestBed.configureTestingModule({
      providers: [{ 
 provide: ActivatedRoute, 
 useClass: ActivatedRouteStub 
 }, JediService] 
 })
 })
})
```

这意味着当我们的组件在构造函数中获取`ActivatedRoute`依赖注入时，它将注入`ActivatedRouteStub`，就像这样：

```ts
@Component({})
export class ExampleRoutingParamsComponent {
 // will inject ActivatedRouteStub 
 constructor(activatedRoute: ActivatedRoute) {} 
}
```

继续我们的测试，我们需要做三件事：

+   实例化组件

+   将路由参数传递给我们的`ActivatedRouteStub`，以便发出路由参数

+   订阅`ActivatedRouteStub`，以便我们可以断言参数确实被发出

让我们将这些添加到我们的测试代码中：

```ts
beforeEach(() => {
 fixture = TestBed.createComponent(ExampleRoutingParamsComponent);
 component  =  fixture.debugElement.componentInstance; activatedRoute  =  TestBed.get(ActivatedRoute); })
```

现在我们已经设置好了 fixture、组件和我们的`activatedRouteStub`。下一步是将实际的路由参数传递给`activatedRouteStub`，并设置一个`subscribe`来知道何时接收到新的路由参数。我们在测试本身中执行这个操作，而不是在`beforeEach()`方法中，就像这样：

```ts
it('should execute the ExampleRoutingParamsComponent', () => {
 // listen for the router parameter
 activatedRoute.paramMap.subscribe(para  => { const  id  =  para.get('id');
 // assert that the correct routing parameter is being emitted expect(id).toBe(1);
 });
 // send the route parameter 
 activatedRoute.sendParameters({ id :  1 }); })
```

那么这对我们的组件意味着什么？在这个阶段我们测试了多少我们的组件？让我们看看我们的`DetailComponent`，并突出显示到目前为止我们测试覆盖的代码：

```ts
@Component({})
export class ExampleRoutingParamsComponent {
 constructor( activatedRoute: ActivatedRoute ) {
 activatedRoute.paramMap.subscribe( paramMap => {
 const id = paramMap.get('id');
 // TODO call service with id parameter
 })
 }
}
```

正如你所看到的，在测试中，我们已经覆盖了`activatedRoute`的模拟，并成功订阅了它。在组件和测试中都缺少的是要考虑到调用一个调用 HTTP 的服务。让我们首先将该代码添加到组件中，就像这样：

```ts
@Component({})
export class ExampleRoutingParamsComponent implements OnInit {
 jedi: Jedi;
 constructor(
 private activatedRoute: ActivatedRoute, 
 private jediService: JediService ) {}

 ngOnInit() { 
 this.activatedRoute.paramMap.subscribe(route => {
 const id = route.get('id')
 this.jediService.getJedi(id).subscribe(data => this.jedi = data);
 });
 }
}
```

在代码中，我们添加了`Jedi`字段以及对`this.jediService.getJedi()`的调用。我们订阅了结果，并将操作的结果分配给了`Jedi`字段。为这部分添加测试支持是我们在前面关于模拟 HTTP 的部分已经涵盖过的。重复这一点是很好的，所以让我们添加必要的代码到单元测试中，就像这样：

```ts
it('should call the Http service with link /api/jedis/1', () => {
 .. rest of the test remains the same

 const  jediService  =  TestBed.get(JediService); const  http  =  TestBed.get(HttpTestingController);

  // fake response
 const  expected  = { name:  'Luke', id:  1 }; let  actual  = {}; http.expectOne('/api/jedis/1').flush(expected);

 ... rest of the test remains the same })
```

我们在这里做的是通过从`TestBed.get()`方法请求`JediService`的副本。此外，我们要求一个`HttpTestingController`的实例。我们继续定义我们想要响应的预期数据，并指示`HttpTestingController`的实例应该期望调用`/api/jedis/1`，当发生这种情况时，预期的数据应该被返回。所以现在我们有一个测试，涵盖了测试`ActivatedRoute`参数的场景，以及 HTTP 调用。测试的完整代码如下：

```ts
import { Subject } from  'rxjs/Rx'; import { ActivatedRoute, convertToParamMap } from  '@angular/router'; import { TestBed } from  '@angular/core/testing'; import { HttpClientTestingModule, 
 HttpTestingController } from  "@angular/common/http/testing"; import { JediService } from  './jedi-service'; import { ExampleRoutingParamsComponent } from  './example.routing.params.component'; class  ActivatedRouteStub {  subject:  Subject<any>; constructor() { this.subject  =  new  Subject();
 }

 sendParameters(params: {}) {
 const  paramMap  =  convertToParamMap(params);  this.subject.next( paramMap ); }

 get  paramMap() { return  this.subject.asObservable(); }
}

describe('A detail component', () => { let  activatedRoute, fixture, component; beforeEach(async() => { TestBed.configureTestingModule({ imports: [HttpClientTestingModule ], declarations: [ ExampleRoutingParamsComponent ], providers: [
 { provide:  ActivatedRoute, useClass:  ActivatedRouteStub }, 
 JediService ] });
 })

 beforeEach(() => { fixture  =  TestBed.createComponent(ExampleRoutingParamsComponent); component  =  fixture.componentInstance; activatedRoute  =  TestBed.get(ActivatedRoute); });

 it('should call the Http service with the route /api/jedis/1 and should display the jedi name corresponding to the id number in the route', async() => { activatedRoute.paramMap.subscribe((para) => { const  id  =  para.get('id'); expect(id).toBe(1); });

 activatedRoute.sendParameters({ id :  1 }); const  http  =  TestBed.get(HttpTestingController); // fake response
 const  expected  = { name:  'Luke', id:  1 }; let  actual  = {}; http.expectOne('/api/jedis/1').flush(expected); fixture.detectChanges(); fixture.whenStable().then(() => { expect(component.jedi.name).toBe('Luke'); });
 });
});
```

那么我们从测试路由参数中学到了什么？由于我们需要创建我们的`ActivatedRouteStub`，所以有点麻烦，但总的来说，还是相当简单的。

# 测试指令

我们的单元测试 Angular 元素之旅的最后一站将涵盖指令。指令通常在整体形状上会相当简单，基本上就是没有附加视图的组件。指令通常与组件一起工作的事实给了我们一个很好的想法，该如何进行测试。

指令可以简单地表示为没有外部依赖项。它看起来像这样：

```ts
@Directive({
 selector: 'some-directive'
})
export class SomeDirective {
 someMethod() {}
}
```

测试很容易，你只需要从`SomeDirective`类中实例化一个对象。然而，你的指令可能会有依赖项，在这种情况下，我们需要通过将其附加到组件来隐式测试指令。让我们看一个例子。让我们首先定义指令，就像这样：

```ts
import { Directive, 
 ElementRef, 
 HostListener } from  '@angular/core'; 
@Directive({ selector:  '[banana]' }) export  class  BananaDirective { constructor(private  elementRef:  ElementRef) { } @HostListener('mouseover') onMouseOver() { this.elementRef.nativeElement.style.color  =  'yellow'; }

 @HostListener('mouseout') onMouseOut() { this.elementRef.nativeElement.style.color  =  'inherit';
 }
}
```

在这里，你看到的是一个简单的指令，如果我们悬停在上面，它会将字体颜色变成黄色。我们需要将它附加到一个组件上。让我们接下来定义一个元素，就像这样：

```ts
import { Component } from  '@angular/core'; @Component({
  selector:  'banana', template: ` <p  class="banana"  banana>hover me</p> `
})
export  class  BananaComponent  {}  
```

在这里，我们可以看到我们将元素作为属性添加到组件模板中定义的`p`标签中。

接下来，让我们来看看我们的测试。我们现在知道如何编写测试，特别是如何测试元素，所以下面的测试代码应该不会让你感到意外：

```ts
import { By } from  '@angular/platform-browser'; import { TestBed } from  "@angular/core/testing"; import { BananaComponent } from  './banana.component'; import { BananaDirective } from  './banana.directive'; describe('A banana directive', () => {  beforeEach(() => { TestBed.configureTestingModule({ declarations: [BananaDirective, BananaComponent] }).compileComponents(); });

 it('should set color property to yellow when mouseover event happens', () => { const  fixture  =  TestBed.createComponent(BananaComponent); const  element  =  fixture.debugElement.query(By.css('.banana')); element.triggerEventHandler('mouseover', null); fixture.detectChanges(); expect(element.nativeElement.style.color).toBe('yellow'); });
})
```

在`beforeEach()`方法中，我们与`TestBed`交谈，配置我们的测试模块，并告诉它关于`BananaDirective`和`BananaComponent`的信息，代码如下：

```ts
  beforeEach(() => { TestBed.configureTestingModule({ declarations: [ BananaDirective, BananaComponent ] }).compileComponents(); });
```

在测试本身中，我们再次使用`TestBed`来创建一个组件。然后，我们通过 CSS 类找到我们的元素。我们找到元素以便触发一个事件，即`mouseover`。触发`mouseover`事件将触发指令中的代码，使字体颜色变为黄色。触发事件后，我们可以使用这行代码来断言元素的字体颜色：

```ts
expect(element.nativeElement.style.color).toBe('yellow');
```

现在，这就是测试指令的简单方法，即使它有依赖关系。关键是，如果是这种情况，您需要一个元素来放置指令，并且您通过元素隐式测试指令。

# 前方的道路

这个最后的测试示例总结了我们对 Angular 单元测试的探索，但请记住，我们只是触及了皮毛。一般来说，测试 Web 应用程序，特别是 Angular 应用程序，会出现许多通常需要特定方法的情况。请记住，如果一个特定的测试需要繁琐和复杂的解决方案，那么我们可能面临着模块重新设计的一个好案例。

我们应该从这里走向何方？有几条路径可以增进我们对 Angular 中 Web 应用程序测试的知识，并使我们成为优秀的测试忍者。

# 在测试堆栈中引入代码覆盖率报告

我们如何知道我们的测试有多远地测试了应用程序？我们能确定我们没有留下任何未经测试的代码吗？如果有，它是否相关？我们如何检测超出当前测试范围的代码片段，以便更好地评估它们是否值得测试？

这些问题可以通过在应用程序测试堆栈中引入代码覆盖率报告来轻松解决。代码覆盖工具旨在跟踪我们单元测试层的范围，并生成一个教育性报告，告诉您测试规范的整体覆盖范围以及仍未覆盖的代码片段。

有几种工具可以在我们的应用程序中实施代码覆盖率分析，目前最流行的是 Blanket（[`blanketjs.org`](http://blanketjs.org)）和 Istanbul（[`gotwarlost.github.io/istanbul`](https://gotwarlost.github.io/istanbul)）。在这两种情况下，安装过程都非常快速和简单。

# 实施端到端测试

在本章中，我们看到了如何通过评估 DOM 的状态来测试 UI 的某些部分。这给了我们一个很好的想法，从最终用户的角度来看事物会是什么样子，但最终这只是一个经过推敲的猜测。

端到端（E2E）测试是一种测试 Web 应用程序的方法，使用自动化代理程序，可以按照用户的流程从开始到结束进行程序化测试。与单元测试的要求相反，这里并不关心代码实现的细微差别，因为 E2E 测试涉及从用户端点开始到结束测试我们的应用程序。这种方法允许我们以集成的方式测试应用程序。而单元测试侧重于每个部分的可靠性，E2E 测试评估整体拼图的完整性，发现单元测试经常忽视的组件之间的集成问题。

对于 Angular 框架的上一个版本，Angular 团队构建了一个强大的工具，名为 Protractor（[`www.protractortest.org/`](http://www.protractortest.org/)），其定义如下：

“端到端测试运行器，模拟用户交互，将帮助您验证 Angular 应用程序的健康状况。”

测试的语法会变得非常熟悉，因为它也使用 Jasmine 来组织测试规范。不幸的是，E2E 超出了本书的范围，但有几个资源可以帮助您扩展对这一主题的了解。在这方面，我们推荐书籍《Angular 测试驱动开发》，*Packt Publishing*，它提供了关于使用 Protractor 为我们的 Angular 应用程序创建 E2E 测试套件的广泛见解。

# 摘要

我们已经走到了旅程的尽头，这绝对是一个漫长但令人兴奋的旅程。在本章中，您看到了在我们的 Angular 应用程序中引入单元测试的重要性，单元测试的基本形式，以及为我们的测试设置 Jasmine 的过程。您还看到了如何为我们的组件、指令、管道、路由和服务编写强大的测试。我们还讨论了在掌握 Angular 过程中的新挑战。可以说前方仍有很长的道路要走，而且绝对是一个令人兴奋的道路。

本章的结束也意味着这本书的结束，但体验将超越其界限。Angular 仍然是一个相当年轻的框架，因此，它将为社区带来的所有伟大事物尚未被创造出来。希望您能成为其中的创造者之一。如果是这样，请让作者知道。

感谢您抽出时间阅读这本书。
