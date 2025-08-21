# 第三章：经典模式

TypeScript 是一种面向对象的编程语言，因此我们可以利用几十年关于面向对象架构的知识。在本章中，我们将探索一些最有用的面向对象设计模式，并学习如何在 Angular 中应用它们。

Angular 本身就是一个面向对象的框架，它强制你以某种方式进行大部分开发。例如，你需要有组件、服务、管道等。强制这些构建块对你有助于构建良好的架构，就像 Zend 框架对 PHP 或 Ruby on Rails 对 Ruby 所做的那样。当然，框架的存在是为了让你的生活更轻松，加快开发时间。

虽然 Angular 的设计方式远远超出了平均水平，但我们总是可以做得更好。我并不是说我在本章中提出的是最终设计，或者你将能够用它来解决从面包店网页到火星一号任务的仪表板的任何问题——不幸的是，这样的设计并不存在——但它肯定会丰富你的工具库。

在本章中，我们将看到以下经典模式：

+   组件

+   单例

+   观察者

# 组件

在这本书的前三章中，我们看到了大量的 Angular 组件。Angular `Component`是 Angular 应用程序的主要构建块之一，例如`services`，`pipes`等。作为提醒，TypeScript 类使用以下注解成为 Angular 组件：

```ts
import { Component } from '@angular/core'; 

@Component({ 
  selector: 'app-root', 
  templateUrl: './app.component.html', 
  styleUrls: ['./app.component.css'] 
}) 
export class AppComponent { 
  title = 'app'; 
} 
```

在这里，`AppComponent`类通过`selector`，`templateUrl`和`styleUrls` Angular 组件的行为得到了增强。

# 单例模式

用于前端应用程序的另一个方便的模式是单例模式。单例模式确保你的程序中只存在一个给定对象的实例。此外，它提供了对对象的全局访问点。

实际上看起来是这样的：

```ts
export class MySingleton{ 

    //The constructor is private so we  
    //can't do `let singleton:MySingleton = new MySingleton();` 
    private static instance:MySingleton = null; 

    private constructor(){ 

    } 

    public static getInstance():MySingleton{ 
        if(MySingleton.instance == null){ 
            MySingleton.instance = new MySingleton(); 
        }
```

```ts
        return MySingleton.instance; 
    } 
} 
 let singleton:MySingleton = MySingleton.getInstance();
```

我们有一个具有`private static instance:MySingleton`属性的类。然后，我们有一个私有构造函数，使以下操作失败：

```ts
let singleton:MySingleton = new MySingleton(); 
```

请注意，它失败是因为你的 TypeScript 转译器对可见性提出了抱怨。然而，如果你将`MySingleton`类转译为 JavaScript 并将其导入到另一个 TypeScript 项目中，你将能够使用*new*运算符，因为转译后的 TypeScript 没有任何可见性。

这种相当简单的单例模式实现的问题在于并发。确实，如果两个进程同时调用`getInstance():MySingleton`，那么程序中将会有两个`MySingleton`的实例。为了确保这种情况不会发生，我们可以使用一种称为早期实例化的技术：

```ts
export

 class MySingleton
 {
   private static instance : MySingleton = new MySingleton();

 private constructor()
  {

  }

 }

singleton: MySingleton = MySingleton.getInstance();
```

虽然你可以在 TypeScript 中实现你的单例，但你也可以利用 Angular 创建单例的方式：服务！确实，在 Angular 中，服务只被实例化一次，并且被注入到任何需要它的组件中。下面是一个通过本书之前看到的`NgModule`进行服务和注入的示例：

```ts

 import { Injectable } from '@angular/core'; 

@Injectable() 
export class ApiService { 

  private static increment:number = 0; 

  public constructor(){ 
    ApiService.increment++; 
  } 

  public toString() :string { 
    return "Current instance: " + ApiService.increment; 
  } 

} 

 // ./app.component.ts

 import { Component } from '@angular/core'; 
import { ApiService } from './api.service'; 

@Component({ 
  selector: 'app-root', 
  templateUrl: './app.component.html', 
  styleUrls: ['./app.component.css'] 
}) 
export class AppComponent { 
  title = 'app'; 

  public constructor(api:ApiService){ 
    console.log(api); 
  } 
} 

 // ./other/other.component.ts

 import { Component, OnInit } from '@angular/core'; 
import { ApiService } from './../api.service'; 

@Component({ 
  selector: 'app-other', 
  templateUrl: './other.component.html', 
  styleUrls: ['./other.component.css'] 
}) 
export class OtherComponent implements OnInit { 

  public constructor(api:ApiService){ 
    console.log(api); 
  } 

  ngOnInit() { 
  } 

} 

 //app.module.ts

 import { BrowserModule } from '@angular/platform-browser'; 
import { NgModule } from '@angular/core'; 
import { MySingleton } from './singleton'; 

import { AppComponent } from './app.component'; 
import { OtherComponent } from './other/other.component'; 

import { ApiService } from './api.service'; 

@NgModule({ 
  declarations: [ 
    AppComponent, 
    OtherComponent 
  ], 
  imports: [ 
    BrowserModule 
  ], 
  providers: [ApiService], 
  bootstrap: [AppComponent] 
}) 
export class AppModule { 

} 
```

在上述代码中，我们有以下内容：

+   `APIService`显示了`@Injectable()`注解，使其可以被注入。此外，`APIService`有一个`increment:number`属性，每次创建新实例时都会递增。由于`increment:number`是静态的，它将准确告诉我们程序中有多少个实例。最后，`APIService`有一个`toString:string`方法，返回当前实例编号。

+   `AppComponent`是一个经典组件，它接收了`APIService`的注入。

+   `OtherComponent`是另一个经典组件，它接收了`APIService`的注入。

+   `/app.module.ts`包含了`NgModule`。在`NgModule`中，这里显示的大部分声明已经在本书中讨论过。新颖之处来自于`providers: [APIService]`部分。在这里，我们为`APIService`本身声明了一个提供者。由于`APIService`并没有做什么太疯狂的事情，它本身就足够了，并且可以通过引用类来提供。而更复杂的服务，例如它们自己需要注入的服务，需要定制的提供者。

现在，如果我们导航到这两个组件，结果将如下：

```ts
Current instance: 1
Current instance: 1
```

这证明只创建了一个实例，并且相同的实例已被注入到两个组件中。因此，我们有一个单例。然而，这个单例虽然方便，但并不是真正安全的。你为什么这样问？嗯，`APIService`也可以在组件级别提供，就像这样：

```ts
import { Component } from '@angular/core'; 
import { ApiService } from './api.service'; 

@Component({ 
  selector: 'app-root', 
  templateUrl: './app.component.html', 
  styleUrls: ['./app.component.css'] 
}) 
export class AppComponent { 
  title = 'app'; 

  public constructor(api:ApiService){ 
    console.log(api); 
  } 
} 
 // ./other.component.ts

 @Component({ 
  selector: 'app-root', 
  templateUrl: './app.component.html', 
  styleUrls: ['./app.component.css'] 
  providers: [APIService],
 })
 export class OtherComponent implements OnInit { 

  public constructor(api:ApiService){ 
    console.log(api); 
  } 

  ngOnInit() { 
  } 

} 
```

在这种情况下，将创建两个单独的实例，导致以下输出：

```ts
Current instance: 1
Current instance: 2
```

因此，使用 Angular 服务，你无法强制使用单例模式，与其普通的 TypeScript 对应相反。此外，普通的 TypeScript 比 Angular 服务快上一个数量级，因为我们完全跳过了注入过程。确切的数字严重依赖于你的机器的 CPU/RAM。

在单例的情况下，唯一剩下的问题是何时使用它或哪种实现效果最好。单例只强制在程序中给定类的一个实例。因此，它非常适合与后端的任何通信或任何硬件访问。例如，在与后端的通信的情况下，可能希望只有一个`APIService`处理 API 密钥、API 限制和整个板块的`csrf`令牌，而无需确保我们在所有组件、模型等中传递相同的服务实例。在硬件访问的情况下，您可能希望确保您只打开一个与用户的网络摄像头或麦克风的连接，以便在完成后可以正确释放它们。

在性能方面，以下是每种实现的结果，以毫秒为单位。我运行了每个版本 100 次，排除了异常值（最好和最差的 5%），并对剩下的 90 次调用进行了平均，如下表所示：

| **单例懒加载** | **单例早期加载** | **服务注入** |
| --- | --- | --- |
| 196 毫秒 | 183 毫秒 | 186 毫秒 |

我运行的代码如下：

```ts
import { Component } from '@angular/core';

 import {MySingleton} from './singleton';
 import { SingletonService } from './singleton.service';

 @Component({
   selector: 'app-root',
   templateUrl: './app.component.html',
   styleUrls: ['./app.component.css']
 })
 export class AppComponent {
   title = 'app works!';

   constructor(private singleton:SingletonService){
     singleton.doStuff();
   }
   //OR
   constructor(){
     MySingleton.getInstance().doStuff();
   }
 }
```

对于服务注入的实验，我不得不在`app.module.ts`中添加以下行：`providers: [SingletonService]`。

令我惊讶的是，两种方法的结果相差不大。早期实例化的单例实现仅比更实用的服务注入好 2%。懒加载的单例排名第三，用时 196 毫秒（比早期实例化的单例差 7%，比服务注入差 5%）。

# 工厂方法

假设我们有一个带有两个私有变量`lastName:string`和`firstName:string`的`User`类。此外，这个简单的类提供了`hello`方法，打印出`"Hi I am", this.firstName, this.lastName`：

```ts
class User{
     constructor(private lastName:string, private firstName:string){
     }
     hello(){
         console.log("Hi I am", this.firstName, this.lastName);
     }
 }
```

现在，考虑到我们通过 JSON API 接收用户。它很可能看起来像这样：

```ts
[{"lastName":"Nayrolles","firstName":"Mathieu"}...].  
```

通过以下代码片段，我们可以创建一个`User`：

```ts
let userFromJSONAPI: User = JSON.parse('[{"lastName":"Nayrolles","firstName":"Mathieu"}]')[0];
```

到目前为止，TypeScript 编译器没有抱怨，并且执行顺利。这是因为`parse`方法返回`any`（例如，Java 对象的 TypeScript 等价物）。当然，我们可以将`any`转换为`User`。然而，`userFromJSONAPI.hello();`将产生以下结果：

```ts
json.ts:19
 userFromJSONAPI.hello();
                  ^
 TypeError: userFromUJSONAPI.hello is not a function
     at Object.<anonymous> (json.ts:19:18)
     at Module._compile (module.js:541:32)
     at Object.loader (/usr/lib/node_modules/ts-node/src/ts-node.ts:225:14)
     at Module.load (module.js:458:32)
     at tryModuleLoad (module.js:417:12)
     at Function.Module._load (module.js:409:3)
     at Function.Module.runMain (module.js:575:10)
     at Object.<anonymous> (/usr/lib/node_modules/ts-node/src/bin/ts-node.ts:110:12)
     at Module._compile (module.js:541:32)
     at Object.Module._extensions..js (module.js:550:10)
```

为什么？好吧，赋值的左侧被定义为`User`，但当我们将其转译为 JavaScript 时，它将被抹去。

在 TypeScript 中进行类型安全的方式如下：

```ts
let validUser = JSON.parse('[{"lastName":"Nayrolles","firstName":"Mathieu"}]')
 .map((json: any):User => {
     return new User(json.lastName, json.firstName);
 })[0];
```

有趣的是，函数的类型也不会帮助你。在这两种情况下，它都会显示`object`而不是`User`，因为 JavaScript 中并不存在用户的概念。

虽然直接的类型安全方法可以工作，但它并不是非常可扩展或可重用的。事实上，地图回调方法必须在接收 JSON 用户的任何地方重复。最方便的方法是通过`Factory`模式来做。工厂用于创建对象，而不会将实例化逻辑暴露给客户端。

如果我们要创建一个用户的工厂，它会是这样的：

```ts

 export class POTOFactory{

     /**
      * Builds an User from json response
      * @param  {any}  jsonUser
      * @return {User}         
      */
     static buildUser(jsonUser: any): User {

         return new User(
             jsonUser.firstName,
             jsonUser.lastName
         );
     }

 }
```

在这里，我们有一个名为`buildUser`的`static`方法，它接收一个 JSON 对象，并从 JSON 对象中获取所有必需的值，以调用一个假设的`User`构造函数。这个方法是静态的，就像工厂的所有方法一样。事实上，在工厂中我们不需要保存任何状态或实例绑定的变量；我们只是将用户的创建封装起来。请注意，你的工厂可能会与你的 POTO 的其余部分共享。

# 观察者

允许一个名为主题的对象跟踪其他对象（称为观察者）对主题状态感兴趣的可观察模式。当主题状态改变时，它会通知观察者。这背后的机制非常简单。

让我们来看一下在纯 TypeScript 中（没有任何 Angular 2 或任何框架，只是 TypeScript）实现的观察者/主题实现。首先，我定义了一个`Observer`接口，任何具体的实现都必须实现：

```ts
export interface Observer{ 
    notify(); 
}
```

这个接口只定义了`notify()`方法。当被观察对象的状态改变时，主题（观察者观察的对象）将调用这个方法。然后，我有一个这个接口的实现，名为`HumanObserver`：

```ts
export class HumanObserver implements Observer{ 
    constructor(private name:string){}

    notify(){

        console.log(this.name, 'Notified');
    } 
} 
```

这个实现利用了 TypeScript 属性构造函数，其中你可以在构造函数内部定义类的属性。这种表示法与以下表示法完全等效，但更短：

```ts
private name:string; 
constructor(name:string){

        this.name = name;
}
```

在定义了`Observer`接口和`HumanObserver`之后，我们可以继续进行主题。我定义了一个管理观察者的主题类。这个类有三个方法：`attachObserver`，`detachObserver`和`notifyObservers`：

```ts
export class Subject{ 
private observers:Observer[] = [];

/**
* Adding an observer to the list of observers
*/
attachObserver(observer:Observer):void{

        this.observers.push(observer);
}

/**
* Detaching an observer
*/
detachObserver(observer:Observer):void{

    let index:number = this.observers.indexOf(observer);

    if(index > -1){

        this.observers.splice(index, 1);
    }else{

        throw "Unknown observer";
    }
}

/**
* Notify all the observers in this.observers
*/
protected notifyObservers(){

    for (var i = 0; i < this.observers.length; ++i) {

        this.observers[i].notify();
    }
} 
} 
```

`attachObserver`方法将新的观察者推入`observers`属性中，而`detachObserver`则将它们移除。

主题实现通常以 attach/detach，subscribe/unsubscribe 或 add/delete 前缀的形式出现。

最后一个方法是`notifyObservers`，它遍历观察者并调用它们的通知方法。允许我们展示可观察机制的最后一个类是 IMDB，它扩展了`subject`。它将在添加电影时通知观察者：

```ts
export class IMDB extends Subject{

    private movies:string[] = [];

     public addMovie(movie:string){

         this.movies.push(movie);
         this.notifyObservers();
     }
 }
```

为了使这些部分彼此通信，我们必须：创建一个`Subject`，创建一个`Observer`，将`Observer`附加到`Subject`，并通过`addMovie`方法改变主题的状态。

更具体地说，以下是先前列表的实现：

```ts
let imdb:IMDB = new IMDB();
 let mathieu:HumanObserver = new HumanObserver("Mathieu");
 imbd.attachObserver(mathieu);
 imbd.addMovie("Jaws");
```

为了加快我们的开发过程，我们将安装`ts-node`。这个 node 包将把 TypeScript 文件转译成 JavaScript，并解决这些文件之间的依赖关系。

输出是`Mathieu Notified`。我们可以尝试分离`mathieu`并添加另一部电影：

```ts
imdb.detachObserver(mathieu);
 imdb.addMovie("Die Hard");
```

输出仍然是`Mathieu Notified`，这发生在我们添加`Jaws`电影之后。第二部电影（`Die Hard`）的添加并不会触发控制台打印`Mathieu Notified`，因为它已经被分离。

# 带参数的 TypeScript 可观察对象

因此，这是观察者模式的一个基本实现。然而，它并不完全成熟，因为`HumanObserver`只知道它观察的主题中的某些东西发生了变化。因此，它必须遍历它观察的所有主题，并检查它们的先前状态与当前状态，以确定发生了什么变化以及在哪里发生了变化。更好的方法是修改`Observer`的`notify`，使其包含更多信息。例如，我们可以添加可选参数如下：

```ts
export interface Observer{

     notify(value?:any, subject?:Subject);
 }

 export class HumanObserver implements Observer{

     constructor(private name:string){}

     notify(value?:any, subject?:Subject){

         console.log(this.name, 'received', value, 'from', subject);
     }
 }
```

`notify()`方法现在接受一个可选的值参数，用于描述`subject`对象的新状态。我们还可以接收到`Subject`对象本身的引用。这在观察者观察多个主题时非常有用。在这种情况下，我们需要能够区分它们。因此，我们必须稍微更改 Subject 和 IMDB，以便它们使用新的通知：

```ts
export class Subject{

     private observers:Observer[] = [];

     attachObserver(oberver:Observer):void{

         this.obervers.push(oberver);
     }

     detachObserver(observer:Observer):void{
         let index:number = this.obervers.indexOf(observer);
         if(index > -1){
             this.observers.splice(index, 1);

         }else{

             throw "Unknown observer";
         }
     }

     protected notifyObservers(value?:any){

         for (var i = 0; i < this.obervers.length; ++i) {

             this.observers[i].notify(value, this);
         }
     }
 }

 export class IMDB extends Subject{

     private movies:string[] = [];

     public addMovie(movie:string){

         this.movies.push(movie);
         this.notifyObservers(movie);
     }
 }
```

最后，输出如下：

```ts
 Mathieu received Jaws from IMDB {

   observers: [ HumanObserver { name: 'Mathieu' } ],
   movies: [ 'Jaws' ] }
```

这比`Mathieu Notified`更具表现力。现在，当我们使用观察者模式进行异步编程时，我们真正的意思是要求某些东西，并且在其处理过程中不想等待做任何事情。相反，我们订阅响应事件以在响应到来时得到通知。在接下来的章节中，我们将使用相同的模式和机制与 Angular 一起使用。

# 观察 HTTP 响应

在本节中，我们将构建一个 JSON API，根据搜索参数返回电影。我们不仅仅是等待 HTTP 查询完成，而是利用观察者设计模式的力量，让用户知道我们正在等待，并且如果需要，执行其他进程。首先，我们需要一个数据源来构建我们的类似 IMDB 的应用程序。构建和部署一个能够解释 HTTP 查询并相应发送结果的服务器端应用程序现在相对简单。然而，这超出了本书的范围。相反，我们将获取托管在[`bit.ly/mastering-angular2-marvel`](http://bit.ly/mastering-angular2-marvel)的静态 JSON 文件。该文件包含漫威电影宇宙的一些最新电影。它包含一个描述 14 部电影的 JSON 数组作为 JSON 对象。这是第一部电影：

```ts
 {
 "movie_id" : 1,
 "title" : "The Incredible Hulk",
 "phase" : "Phase One: Avengers Assembled",
 "category_name" : "Action",
 "release_year" : 2005,
 "running_time" : 135,
 "rating_name" : "PG-13",
 "disc_format_name" : "Blu-ray",
 "number_discs" : 1,
 "viewing_format_name" : "Widescreen",
 "aspect_ratio_name" : " 2.35:1",
 "status" : 1,
 "release_date" : "June 8, 2008",
 "budget" : "150,000,000",
 "gross" : "263,400,000",
 "time_stamp" : "2018-06-08"
 },
```

您可以找到类似 IMDB 的应用程序提供的标准信息，例如发行年份，播放时间等。我们的目标是设计一个异步的 JSON API，使每个字段都可以搜索。

由于我们正在获取一个静态的 JSON 文件（我们不会插入、更新或删除任何元素），可接受的 API 调用如下：

```ts
IMDBAPI.fetchOneById(1);
 IMDBAPI.fetchByFields(MovieFields.release_date, 2015);
```

第一个调用只是获取`movie_id = 1`的电影；第二个调用是一个更通用的调用，可以在任何字段中工作。为了防止 API 使用者请求我们电影中不存在的字段，我们使用在`Movie`类内部定义的枚举器限制字段值。现在，这里的重要部分是这些调用的实际返回。实际上，它们将触发一个可观察机制，在这种机制中，调用者将附加到一个可观察的 HTTP 调用。然后，当 HTTP 调用完成并根据查询参数过滤结果时，被调用者将通知调用者有关响应。因此，调用者不必等待被调用者（`IMDBAPI`），因为他们将在请求完成时收到通知。

# 实施

让我们深入实现。首先，我们需要使用 Angular CLI 创建一个新的 Angular 项目：

```ts
mkdir angular-observable
 ng init
 ng serve
```

接下来，我们需要一个模型来表示电影概念。我们将使用`ng g class` models/Movie 命令行生成这个类。然后，我们可以添加一个构造函数，定义`Movie`模型的所有私有字段，这与我们为 getter 和 setter 所做的相同。

```ts
export class Movie {

     public constructor(
         private _movie_id:number,
         private _title: string,
         private _phase: string,
         private _category_name: string,
         private _release_year: number,
         private _running_time: number,
         private _rating_name: string,
         private _disc_format_name: string,
         private _number_discs: number,
         private _viewing_format_name: string,
         private _aspect_ratio_name: string,
         private _status: string,
         private _release_date: string,
         private _budget: number,
         private _gross: number,
         private _time_stamp:Date){
     }

     public toString = () : string => {

         return `Movie (movie_id: ${this._movie_id},
         title: ${this._title},
         phase: ${this._phase},
         category_name: ${this._category_name},
         release_year: ${this._release_year},
         running_time: ${this._running_time},
         rating_name: ${this._rating_name},
         disc_format_name: ${this._disc_format_name},
      number_discs: ${this._number_discs},
         viewing_format_name: ${this._viewing_format_name},
         aspect_ratio_name: ${this._aspect_ratio_name},
         status: ${this._status},
         release_date: ${this._release_date},
         budget: ${this._budget},
         gross: ${this._gross},
         time_stamp: ${this._time_stamp})`;

     }
    //GETTER
    //SETTER
 }

 export enum MovieFields{
     movie_id,
     title,
     phase,
     category_name,
     release_year,
     running_time,
     rating_name,
     disc_format_name,
     number_discs,
     viewing_format_name,
     aspect_ratio_name,
     status,
     release_date,
     budget,
     gross,
     time_stamp
 }
```

在这里，电影 JSON 定义的每个字段都使用构造函数属性声明映射到`Movie`类的私有成员

TypeScript。我们还覆盖了`toString`方法，以便打印每个字段。在`toString`方法中，我们利用反引号(` `)提供的多行字符串和`${}`语法，允许我们连接字符串和不同的变量。然后，我们有一个名为`MovieFields`的枚举器，它将允许我们限制可搜索的字段。

接下来，我们需要生成`IMDBAPI`类。由于`IMDBAPI`类可能会在程序的任何地方使用，我们将其定义为服务。其优势在于服务可以被注入到任何组件或指令中。此外，我们可以选择是否让 Angular 2 在每次注入时创建`IMDBAPI`的实例，或者始终注入相同的实例。如果为`IMDBAPI`创建的提供者是在应用程序级别的话，那么请求它的任何人都会得到同一个`IMDBAPI`的实例。然而，在组件级别，每次实例化该组件时都会创建新的`IMDBAPI`实例并提供给该组件。在我们的情况下，只有一个`IMDBAPI`实例更合理，因为它不会有任何特定于从一个组件到另一个组件可能会发生变化的状态。让我们生成`IMDBAPI`服务(`ng g s``services/IMDBAPI`)，并实现我们之前定义的两个方法：

```ts
IMDBAPI.fetchOneById(1);
 IMDBAPI.fetchByFields(MovieFields.release_date, 2015);
```

这是带有`fetchOneById`方法的 IMDAPI 服务：

```ts
import { Injectable } from '@angular/core';
 import { Http }  from '@angular/http';
 import { Movie, MovieFields } from '../models/movie';
 import { Observable } from 'rxjs/Rx';
 import 'rxjs/Rx';

 @Injectable()

 export class IMDBAPIService {

   private moviesUrl:string = "app/marvel-cinematic-universe.json";

   constructor(private http: Http) { }
   /**
    * Return an Observable to a Movie matching id
    * @param  {number}           id
    * @return {Observable<Movie>}  
    */
   public fetchOneById(id:number):Observable<Movie>{
     console.log('fetchOneById', id);

         return this.http.get(this.moviesUrl)
         /**
         * Transforms the result of the HTTP get, which is observable
         * into one observable by item.
         */
         .flatMap(res => res.json().movies)

         /**
         * Filters movies by their movie_id

         */
         .filter((movie:any)=>{

             console.log("filter", movie);
             return (movie.movie_id === id)
         })

         /**
         * Map the JSON movie item to the Movie Model
         */
         .map((movie:any) => {

             console.log("map", movie);

             return new Movie(

                 movie.movie_id,
                 movie.title,
                 movie.phase,
                 movie.category_name,
                 movie.release_year,
                 movie.running_time,
                 movie.rating_name,
                 movie.disc_format_name,
                 movie.number_discs,
                 movie.viewing_format_name,
                 movie.aspect_ratio_name,
                 movie.status,
                 movie.release_date,
                 movie.budget,
                 movie.gross,
                 movie.time_stamp
             );
         });
   }
 }
```

# 理解实现

让我们一步步来分解。首先，服务的声明非常标准：

```ts
import { Injectable } from '@angular/core'; 
import { Http } from '@angular/http'; 

import { Movie, MovieFields } from '../models/movie'; 
import { Observable } from 'rxjs/Rx'; 
import 'rxjs/Rx';

@Injectable()
 export class IMDBAPIService {
  private moviesUrl:string = "app/marvel-cinematic-universe.json";
  constructor(private http: Http) { }
```

服务是可注入的。因此，我们需要导入并添加`@Injectable`注解。我们还导入`Http`，`Movie`，`MovieFields`，`Observable`以及`Rxjs`的操作符。**RxJS**代表**JavaScript 的响应式扩展**。它是用于执行观察者、迭代器和函数式编程的 API。当涉及到 Angular 2 中的异步操作时，大部分情况下会依赖于 RxJS。

值得注意的是，我们使用的是 RxJS 5.0，它是一次完整的重写，基于相同概念的 RxJS 4.0。

`IMDBAPIService`还有一个对我们的 JSON 文件路径的引用，以及一个接收 HTTP 服务注入的构造函数。在`fetchOneById`方法的实现中，我们可以看到四个不同的操作链接在一起：`get`， `flatMap`，`filter`和`map`。 `Get`返回 HTTP 请求的主体上的 observable。 `flatMap`通过应用您指定的每个发射项目的 observable 函数来转换`get observable`，其中该函数返回发出项目的`observable`。然后，`flatMap`合并这些结果的发射，将这些合并的结果作为其序列发射。在我们的情况下，这意味着我们将对从 HTTP 获取的所有项目应用接下来的两个操作（filter 和 map）。筛选器检查当前电影的 ID 是否是我们要查找的 ID，Map 将电影的 JSON 表示转换为电影的 TypeScript 表示（例如`Movie`类）。

最后一个操作虽然反直觉，但却是必须的。事实上，人们可能会认为 JSON 表示和 TypeScript 表示是相同的，因为它们拥有相同的字段。然而，TypeScript 表示以及其属性定义了`toString`，getter 和 setter 等函数。移除`map`将返回一个包含`Movie`所有字段的`Object`实例，而不是`Movie`。此外，类型转换也无济于事。事实上，TypeScript 转换器将允许您将`Object`转换为`Movie`，但它仍然不会包含`Movie`类中定义的方法，因为当 TypeScript 转换为 JavaScript 时，静态类型概念消失。以下情况将无法在执行时转换：

```ts
movie.movie_id(25) TypeError: movie.movie_id is not a function at Object.<anonymous>
movie: Movie = JSON.parse(`{
                             "movie_id" : 1,
                              "title" : "Iron Man",
                              "phase" : "Phase One: Avengers Assembled",
                             "category_name" : "Action",
                             "release_year" : 2015,
                              "running_time" : 126,
                              "rating_name" : "PG-13",
                              "disc_format_name" : "Blu-ray",
                              "number_discs" : 1,
                              "viewing_format_name" : "Widescreen",
                              "aspect_ratio_name" : " 2.35:1",
                              "status" : 1,
                              "release_date" : "May 2, 2008",
                              "budget" : "140,000,000",
                              "gross" : "318,298,180",
                              "time_stamp" : "2015-05-03"
        }`);
 Console.log(movie.movie_id(25));
```

现在，如果我们想要使用我们的`IMDB`服务，则需要进一步修改由 Angular CLI 生成的代码。首先，我们需要修改`main.ts`，使其看起来像这样：

```ts
import{ bootstrap } from '@angular/platform-browser-dynamic';
 import{ enableProdMode } from '@angular/core';
 import{ AngularObservableAppComponent, environment } from './app/';
 import{ IMDBAPIService } from './app/services/imdbapi.service';
 import { HTTP_PROVIDERS } from '@angular/http';
 if(environment.production)  {
     enableProdMode();
}

```

```ts
 bootstrap(AngularObservableAppComponent, 
    [IMDBAPIService , HTTP_PROVIDERS]
);
```

粗体的行表示新增内容。我们导入我们的`IMDBService`和`HTTP_PROVIDERS`。这两个提供者在应用程序级别声明，这意味着将被注入到控制器或指令中的实例始终是相同的。

然后，我们修改了生成的`angular-observable.component.ts`文件，并添加了以下内容：

```ts
import { Component } from '@angular/core';
import { IMDBAPIService } from './services/imdbapi.service';
import { Movie } from './models/movie';

@Component({
  moduleId: module.id, 
  selector: 'angular-observable-app', 
  templateUrl: 'angular-observable.component.html', 
  styleUrls: ['angular-observable.component.css']
 })
 export class AngularObservableAppComponent {
   title = 'angular-observable works!'; 
   private movies:Movie[] = [];
   private error:boolean = false; 
   private finished:boolean = false;

 constructor(private IMDBAPI:IMDBAPIService){
    this.IMDBAPI.fetchOneById(1).subscribe(
       value => {this.movies.push(value); console.log("Component",value)},
       error => this.error = true, 
       () => this.finished =true 
      )
   }
 }
```

我们将几个属性添加到`AngularObservableAppComponent`：`movies`，`error`和`finished`。第一个属性是存储我们查询结果的`Movie`数组，而第二个和第三个属性是`error`和`termination`的标志。在构造函数中，我们注入了`IMDBAPIService`，并订阅了`fetchOneById`方法的结果。`subscribe`方法期望三个回调函数：

+   **观察者**：接收被观察方法产生的值。这是本章前面看到的通知方法的 RxJS 等效物。

+   **错误**（**可选**）：在观察到对象产生错误的情况下触发。

+   **Complete**（**可选**）：完成时触发。

最后，我们可以修改`angular-ob``servable.component.html`文件来映射`AngularObservableAppComponent`数组的`movie`属性：

```ts
<h1>
  {{title}}
</h1>

<ul>
   <li *ngFor = "let movie of movies">{{movie}}</li> 
</ul>
```

我们可以看到，第一个电影条目已经被正确插入到我们的`ul`/`li` HTML 结构中。关于这段代码真正有趣的地方在于事物执行的顺序。分析日志有助于我们掌握 Angular 与 RxJS 中异步性的真正力量。我们的代码执行后，控制台如下所示：

```ts
javascript fetchOneById 1 :4200/app/services/imdbapi.service.js:30 filter Object :4200/app/services/imdbapi.service.js:34 map Object :4200/app/angular-observable.component.js:21 Component Movie_aspect_ratio_name: " 2.35:1"_budget: "140,000,000"_category_name: "Action"_disc_format_name: "Blu-ray"_gross: "318,298,180"_movie_id: 1_number_discs: 1_phase: "Phase One: Avengers Assembled"_rating_name: "PG-13"_release_date: "May 2, 2008"_release_year: 2015_running_time: 126_status: 1_time_stamp: "2015-05-03"_title: "Iron Man"_viewing_format_name: "Widescreen"aspect_ratio_name: (...)budget: (...)category_name: (...)disc_format_name: (...)gross: (...)movie_id: (...)number_discs: (...)phase: (...)rating_name: (...)release_date: (...)release_year: (...)running_time: (...)status: (...)time_stamp: (...)title: (...)toString: ()viewing_format_name: (...)__proto__: Object :4200/app/services/imdbapi.service.js:30 filter Object :4200/app/services/imdbapi.service.js:30 filter Object :4200/app/services/imdbapi.service.js:30 filter Object :4200/app/services/imdbapi.service.js:30 filter Object :4200/app/services/imdbapi.service.js:30 filter Object :4200/app/services/imdbapi.service.js:30 filter Object :4200/app/services/imdbapi.service.js:30 filter Object :4200/app/services/imdbapi.service.js:30 filter Object :4200/app/services/imdbapi.service.js:30 filter Object :4200/app/services/imdbapi.service.js:30 filter Object :4200/app/services/imdbapi.service.js:30 filter Object :4200/app/services/imdbapi.service.js:30 filter Object :4200/app/services/imdbapi.service.js:30 filter Object
```

如你所见，`AngularObservableAppComponent`在过滤函数分析所有项之前就收到了匹配查询的电影的通知。提醒一下，在按 ID 获取时，`fetchOneById`方法的操作顺序是：`get`、`flatMap`、`filter`和`map`，而且`filter`和`map`方法也有日志记录语句。因此，这里的`filter`操作分析了第一项，恰好是我们寻找的那一项（`movie_id===1`），并将其转发给将其转化为`Movie`的`map`操作。这个`Movie`被立刻发送给了`AngularObservableAppComponent`。我们清楚地看到，在`AngularObservableAppComponent`组件中收到的对象是`Movie`类型，因为控制台给出了我们对`toString`方法的覆盖。然后，`filter`操作继续处理剩下的项。它们中没有一个匹配。因此，我们不会再收到任何通知了。让我们更进一步地用第二种方法`IMDBAPI.fetchByField`进行测试：

```ts

 public fetchByField(field:MovieFields, value:any){
 console.log('fetchByField', field, value); 
 return this.http.get (this.moviesUrl)
      .flatMap(res => res.json().movies)
 /**
 * Filters movies by their field
 */
 .filter((movie:any) =>{

     console.log("filter" , movie);
     return (movie[MovieFields[field]] === value)
  })

 /**
 * Map the JSON movie item to the Movie Model
 */
 .map(( movie: any) => {
     console.log ("map", movie);  
     return new Movie( 
         movie.movie_id, 
         movie.title,  
         movie.phase, 
         movie.category_name, 
         movie.release_year,  
         movie.running_time,  
         movie.rating_name,  
         movie.disc_format_name,  
         movie.number_discs, 
         movie.viewing_format_name,
         movie.aspect_ratio_name,  
         movie.status,
         movie.release_date,
         movie.budget,
         movie.gross,  
         movie.time_stamp
      );
   });
}
```

对于`fetchByField`方法，我们使用与`fetchById`相同的机制。毫不奇怪，操作仍然是一样的：`get`、`flatMap`、`filter`和`map`。唯一的变化在于过滤操作，这里我们现在必须根据作为参数接收的字段进行过滤：

```ts
return (movie[MovieFields[field]] === value).
```

对于 TypeScript 或 JavaScript 初学者来说，这个声明可能有点令人困惑。首先，`MovieFields[field]`部分的解释是`enum`将被转译为以下 JavaScript 函数：

```ts
(function(MovieFields) {
   MovieFields[MovieFields["movie_id"] = 0] = "movie_id";
   MovieFields[MovieFields["title"] = 1] = "title";
   MovieFields[MovieFields["phase"] = 2] = "phase"; 
   MovieFields[MovieFields["category_name"] = 3] = "category_name";
   MovieFields[MovieFields["release_year"] = 4] = "release_year";
   MovieFields[MovieFields["running_time"] = 5] = "running_time"; 
   MovieFields[MovieFields["rating_name"] = 6] = "rating_name";
   MovieFields[MovieFields["disc_format_name"] = 7] ="disc_format_name";
   MovieFields[MovieFields["number_discs"] = 8] = "number_discs";
   MovieFields[MovieFields["viewing_format_name"] = 9] = "viewing_format_name";
 MovieFields[MovieFields["aspect_ratio_name"] = 10] =  "aspect_ratio_name";
 MovieFields[MovieFields["status"] = 11] = "status"; 
 MovieFields[MovieFields["release_date"] = 12] = "release_date";
 MovieFields[MovieFields["budget"] = 13] = "budget";
 MovieFields[MovieFields["gross"] = 14] = "gross";
 MovieFields[MovieFields["time_stamp"] = 15] = "time_stamp";
 })(exports.MovieFields || (exports.MovieFields =  {}));
 var MovieFields = exports.MovieFields;
```

结果，`MovieFields.release_year`的值实际上是 4，而`MovieFields`是一个静态数组。因此，请求`MovieFields`数组的第四个索引会使我得到字符串`release_year is`。因此，在我们当前的示例中，`movie[MovieFields[field]]`被解释为`movie["release_year is"]`。

现在我们有了五个匹配项而不是一个。分析控制台，我们可以看到通知仍然在找到合适的对象时立即出现，而不是在所有被过滤完后出现：

```ts
fetchByField 4 2015
 imdbapi.service.js:43  filter Object  {movie_id: 1,  title: "Iron Man", phase: "Phase One: Avengers Assembled", category_name: "Action", release_year: 2015...}
 imdbapi.service.js:47 map Object {movie_id: 1, title: "Iron Man", phase: "Phase One: Avengers Assembled", category_name: "Action",  release_year: 2015...}
 angular-observable.component.js:22 Component Movie {_movie_id: 1, _title: "Iron Man", _phase: "Phase One: Avengers Assembled", _category_name: "Action", _release_year: 2015...}
 imdbapi.service.js:43 filter Object {movie_id: 2, title: "The Incredible Hulk", phase: "Phase One: Avengers Assembled", category_name: "Action", release_year: 2008...}
 imdbapi.service.js:43 filter Object {movie_id: 3, title: "Iron Man 2", phase: "Phase One: Avengers Assembled", category_name: "Action", release_year: 2015...}
 imdbapi.service.js:47map Object {movie_id: 3 =, title: "Iron Man 2", phase: "Phase One: Avengers Assembled", category_name: "Action", release_year: 2015...}
 angular-observable.component.js:22 Component Movie{_movie_id: 3, _title: "Iron Man 2", _phase: "Phase One: Avengers Assembled", _category_name: "Action", _release_year:2015...}
 imdbapi.service.js:43 filter Object {movie_id: 4, title: "Thor", phase: "Phase One: Avengers Assembled", category_name: "Action", release_year:2011...}
 imdbapi.service.js:43filter Object {movie_id: 5, title: "Captain America", phase: "Phase One: Avengers Assembled", category_name: "Action", release_year: 2011...}
 imdbapi.service.js:43 filter Object {movie_id: 6, title: "Avengers, The", phase: "Phase One: Avengers Assembled", category_name: "Science Fiction", release_year: 2012...}
 imdbapi.service.js:43 filter Object {movie_id: 7, title: "Iron Man 3", phase: "Phase Two", category_name: "Action", release_year : 2015...}
 imdbapi.service.js:47 map Object {movie_id: 7, title: "Iron Man 3", phase: "Phase Two", category_name: "Action", release_year:2015...}
 angular-observable.component.js: 22 Component Movie {_movie_id: 7, _title: "Iron Man 3", _phase: "Phase Two", _category_name:"Action", _release_year: 2015...}
 imdbapi.service.js:43 filter Object {movie_id: 8, title: "Thor: The Dark World", phase: "Phase Two", category_name: "Science Fiction", release_year: 2013...}
 imdbapi.service.js:43 filter Object {movie_id: 9, title: "Captain America: The Winter Soldier", phase: "Phase Two", category_name: "Action", release_year: 2014...}
 imdbapi.service.js:43 filter Object {movie_id: 10, title: "Guardians of the Galaxy", phase: "Phase Two", category_name: "Science Fiction", release_year: 2014...}
 imdbapi.service.js:43 filter Object {movie_id: 11, title: "Avengers: Age of Ultron", phase: "Phase Two", category_name: "Science Fiction", release_year: 2015...}
 imdbapi.service.js:47 map Object {movie_id: 11, title: "Avengers: Age of Ultron", phase:  "Phase Two", category_name: "Science Fiction", release_year: 2015...}
 angular-observable.component.js:22 Component Movie {_movie_id: 11, _title: "Avengers: Age of Ultron", _phase: "Phase Two", _category_name: "Science Fiction", _release_year:2015...}
 imdbapi.service.js:43 filter Object {movie_id: 12, title: "Ant-Man", phase: "Phase Two", category_name: "Science Fiction", release_year: 2015...}
 imdbapi.service.js:47 map Object {movie_id: 12, title: "Ant-Man", phase: "Phase Two", category_name: "Science Fiction", release_year: 2015...}
 angular-observable.component.js:22 Component Movie {_movie_id: 12, _title: "Ant-Man", _phase: "Phase Two", _category_name: "Science Fiction", _release_year: 2015...}
 imdbapi.service.js:43 filter Object {movie_id: 13, title: "Captain America: Civil War",phase: "Phase Three", category_name: "Science Fiction", release_year: 2016...}
imdbapi.service.js:43 filter Object {movie_id: 14, title: "Doctor Strange", phase: "Phase Two", category_name: "Science Fiction", release_year: 2016...}
```

现在，这种设计模式的另一个优势是能够自行取消订阅。要这样做，你只需获取对订阅的引用并调用`unsubscribe()`方法，如下所示：

```ts
constructor(private IMDBAPI:IMDBAPIService{ 
 let imdbSubscription = this.IMDBAPI.fetchByField(MovieFields.release_year, 2015).subscribe(
       value=> {
            this.movies.push(value);
            console.log("Component", value)
            if(this.movies.length > 2){
                    imdbSubscription.unsubscribe();
             }
      },
     error => this.error = true,
     () => this.finished = true
    );
 }
```

在这里，我们在第三个通知后取消订阅。除此之外，可观察对象甚至会检测到没有人再观察它，然后停止它正在做的任何事情。事实上，上一个带有`unsubscribe`的代码产生了：

```ts
fetchByField 4 2015
 imdbapi.service.js:43 filter Object {movie_id: 1, title: "Iron Man", phase: "Phase One: Avengers Assembled", category_name: "Action", release_year: 2015...}
 imdbapi.service.js:49 map Object {movie_id: 1, title: "Iron Man", phase: "Phase One: Avengers Assembled", category_name: "Action", release_year: 2015...}
 angular-observable.component.js:24 Component Movie {_movie_id: 1, _title: "Iron Man", _phase: "Phase One: Avengers Assembled", _category_name: "Action", _release_year: 2015...}
 imdbapi.service.js:43 filter Object {movie_id: 2, title: "The Incredible Hulk", phase: "Phase One: Avengers Assembled", category_name: "Action", release_year: 2008...}
 imdbapi.service.js:43 filter Object { movie_id: 3, title: "Iron Man 2", phase: "Phase One: Avengers Assembled", category_name: "Action", release_year: 2015...}
 imdbapi.service.js:49 map Object {movie_id: 3, title: "Iron Man 2", phase: "Phase One: Avengers Assembled", category_name: "Action", release_year: 2015...}
 angular-observable.component.js:24 Component Movie {_movie_id: 3, _title: "Iron Man 2", _phase:  "Phase One: Avengers Assembled", _category_name: "Action",_release_year: 2015...}
 imdbapi.service.js:43 filter Object {movie_id: 4, title: "Thor", phase: "Phase One: Avengers Assembled", category_name: "Action", release_year: 2011...}
 imdbapi.service.js:43 filter Object {movie_id: 5, title: "Captain America", phase: "Phase One: Avengers Assembled", category_name: "Action",release_year: 2011...}
 imdbapi.service.js:43 filter Object {movie_id: 6, title: "Avengers, The", phase: "Phase One: Avengers Assembled", category_name: "Science Fiction", release_year: 2012...}
 imdbapi.service.js:43 filter Object {movie_id: 7, title: "Iron Man 3", phase: "Phase Two", category_name: "Action", release_year: 2015...}
 imdbapi.service.js:49 map Object {movie_id: 7, title: "Iron Man 3", phase: "Phase Two", category_name: "Action", release_year: 2015...}
 angular-observable.component.js:24 Component Movie {_movie_id: 7, _title: "Iron Man 3", _phase: "Phase Two", _category_name: "Action", _release_year: 2015...}
```

所有事情在第三次通知后停止了。

# Promises

Promise 是 Angular 2 提供的另一个有用的异步概念。它承诺提供与`Observer`相同的功能：处理某些事情，并且异步地通知调用者答案已经准备好了。那么，为什么要同时存在两个做相同事情的概念呢？嗯，`Observer`的冗长使得`Promise`无法实现的一件事情是：取消订阅。因此，如果你永远不打算使用观察者模式的取消订阅功能，那么最好使用`Promises`，在我看来，它们在书写和理解上更直观。为了强调观察者和 Promise 之间的差异，我们将采用与之前相同的例子——从 JSON API 获取电影。`AngularObservableAppComponent`将向`IMDBAPIService`发出异步调用，并在答案到来时更新 HTML 视图。

这是使用`Promise`而不是`Observable`的`fetchOneById`方法：

```ts

 /**
 * Return a Promise to a Movie matching id
 *@param  {number}  id
 *@return {Promise<Movie>}
 */
 public fetchOneById(id:number) : Promise <Movie>{
 console.log('fecthOneById', id);

      return this.http.get(this.moviesUrl)
     /**
     * Transforms the result of the HTTP get, which is observable
     * into one observable by item.
     */
     .flatMap(res => res.json().movies)
     /**
     * Filters movies by their movie_id
     */
    .filter((movie:any) =>{
        console.log("filter", movie);
       return (movie.movie_id === id)
   })
   .toPromise()
   /**
 * Map the JSON movie item to the Movie Model
 */
    .then((movie:any) => {

       console.log("map", movie);
       return new Movie(
              movie.movie_id,
              movie.title,
              movie.phase,
              movie.category_name,
              movie.release_year,
              movie.running_time,
              movie.rating_name,
              movie.disc_format_name,
              movie.number_discs,
              movie.viewing_format_name,
              movie.aspect_ratio_name,
              movie.status,
              movie.release_date,
              movie.budget,
              movie.gross,
              movie.time_stamp
      )
});
 }
```

如此代码所示，我们从`flatMap`，`filter`，`map`变为了`flatMap`，`filter`，`Promise`，`then`。新的操作`toPromise`和`then`将创建一个包含`filter`操作结果的`Promise`对象，在`filter`操作完成时，`then`操作将被执行。`then`操作可以被视为一个 map；它做的事情是一样的。为了使用这段代码，我们还需要修改在`AngularObservableAppComponent`中调用`IMDBAPIService`的方式如下：

```ts

 this.IMDBAPI.fetchOneById(1).then(
        value => {
              this.movies.push(value);

              console.log("Component", value)
       },
       error => this.error = true
 );
```

一次又一次，我们可以看到一个`then`操作，该操作将在`IMDBAPIService.FetchOneById`的 promise 完成时执行。`then`操作接受两个回调函数：`onCompletion`和`onError`。第二个回调函数`onError`是可选的。现在，`onCompletion`回调函数仅在 Promise 完成时执行一次，如控制台所示：

```ts
imdbapi.service.js:30 filter Object {movie_id: 2, title: "The Incredible Hulk", phase: "Phase One: Avengers Assembled", category_name: "Action", release_year: 2008...}
 imdbapi.service.js:30 filter Object {movie_id: 3, title: "Iron Man 2", phase : "Phase One: Avengers Assembled", category_name: "Action", release_year: 2015...}
 imdbapi.service.js:30 filter Object {movie_id: 4, title: "Thor", phase: "Phase One: Avengers Assembled", category_name: "Action", release_year: 2011...}
 imdbapi.service.js:30 filter Object {movie_id: 5, title: "Captain America", phase:  "Phase One: Avengers Assembled", category_name: "Action", release_year: 2011...}
 imdbapi.service.js:30 filter Object {movie_id: 6, title: "Avengers, The", phase: "Phase One: Avengers Assembled", category_name:"Science Fiction", release_year: 2012...}
 imdbapi.service.js:30 filter Object {movie_id: 7, title: "Iron Man 3", phase: "Phase Two", category_name: "Action", release_year: 2015...}
 imdbapi.service.js:30 filter Object {movie_id: 8, title: "Thor: The Dark World", phase: "Phase Two", category_name: "Science Fiction", release_year: 2013...}
 imdbapi.service.js:30 filter Object {movie_id: 9, title: "Captain America: The Winter Soldier", phase: "Phase Two", category_name: "Action",release_year: 2014...}
 imdbapi.service.js:30 filter Object {movie_id: 10, title: "Guardians of the Galaxy", phase: "Phase Two", category_name: "Science Fiction", release_year: 2014...}
 imdbapi.service.js:30 filter Object { movie_id: 11, title: "Avengers: Age of Ultron", phase: "Phase Two", category_name: "Science Fiction", release_year: 2015...}
 imdbapi.service.js:30 filter Object {movie_id: 12, title: "Ant-Man", phase: "Phase Two", category_name: "Science Fiction", release_year: 2015...}
 imdbapi.service.js:30 filter Object {movie_id: 13, title: "Captain America: Civil War", phase: "Phase Three", category_name: "Science Fiction", release_year: 2016...}
 imdbapi.service.js:30 filter Object {movie_id: 14, title: "Doctor Strange", phase: "Phase Two", category_name: "Science Fiction", release_year: 2016...}
 imdbapi.service.js:35 map Object {movie_id: 1, title: "Iron Man", phase: "Phase One: Avengers Assembled", category_name: "Action", release_year: 2015...}
 angular-observable.component.js:23 Component Movie {_movie_id: 1, _title: "Iron Man", _phase: "Phase One: Avengers Assembled", _category_name: "Action",  _release_year: 2015...}
```

虽然对于`fetchOneById`方法，对`IMDBAPIService`的修改很小，但我们需要更显著地修改`fetchByField`。实际上，`onComplete`回调函数只会执行一次，所以我们需要返回一个`Movie`数组而不仅仅是一个`Movie`。以下是`fetchByField`方法的实现：

```ts
public fetchByField(field: MovieFields, value: any) :Promise<Movie[]>{
       console.log('fetchByField', field, value);
       return this.http.get(this.moviesUrl)
          .map(res => res.json().movies.filter(
              (movie)=>{
                  return (movie[MovieFields[field]] === value)
              })
         )
         .toPromise()
         /**
          * Map the JSON movie items to the Movie Model
         */
        .then((jsonMovies:any[]) => {
           console.log("map",jsonMovies);
           let movies:Movie[] = [];
           for (var i = 0; i < jsonMovies.length; i++) {
               movies.push(
                  new Movie(
                      jsonMovies[i].movie_id,
                      jsonMovies[i].title,
                      jsonMovies[i].phase,
                      jsonMovies[i].category_name,
                      jsonMovies[i].release_year,
                      jsonMovies[i].running_time,
                      jsonMovies[i].rating_name,
                      jsonMovies[i].disc_format_name,
                      jsonMovies[i].number_discs, 
                      jsonMovies[i].viewing_format_name, 
                      jsonMovies[i].aspect_ratio_name, 
                      jsonMovies[i].status,
                      jsonMovies[i].release_date, 
                      jsonMovies[i].budget, 
                      jsonMovies[i].gross,
                      jsonMovies[i].time_stamp
                  )
                )
              }
              return movies;  
           });
 }
```

为了实现这一点，我将`flatMap`替换为了一个经典的 map 作为第一个操作。在 map 中，我直接获取 JSON `movie`数组的引用并应用字段过滤。结果被转换为 promise 并在`then`中处理。`then`操作接收到一个 JSON `movies`数组并将其转换为一个`Movie`数组。这产生了一个被承诺的结果返回给调用者的`Movie`数组。在`AngularObservableAppComponent`中的调用也有些不同，因为我们现在期望一个数组：

```ts

 this.IMDBAPI.fetchByField(MovieFields.release_year, 2015).then(
     value => {
        this.movies = value;
        console.log("Component", value)
     },
     error => this.error = true
 )
```

使用 `Promise` 的另一种方式是通过 fork/join 范式。实际上，可以启动许多进程(fork)，并等待所有 promise 完成后再将聚合结果发送给调用者(join)。因此，相对来说很容易增强 `fetchByField` 方法,因为它可以使用逻辑 or 在多个字段中运行。以下是我们需要实现这个逻辑 or 的三个非常简短的方法:

```ts

 /**
 * Private member storing pending promises
 */
 private promises:Promise<Movie[]>[] = [];
 /**
  * Register one promise for field/value. Returns this
  * for chaining i.e.
  *
  * byField(Y, X)
  * .or(...)
  * .fetch()
  *
  * @param {MovieFields} field
  * @param {any}         value
  * @return {IMDBAPIService}
  */
public byField(field:MovieFields, value:any):IMDBAPIService{

   this.promises.push(this.fetchByField(field, value));
   return this; 
 }
 /**
 * Convenient method to make the calls more readable, i.e.
 *
 * byField(Y, X)
 * .or(...)
 * .fetch()
 *
 * instead of
 *
 * byField(Y, X)
 * .byField(...)
 * .fetch()
 *
 * @param {MovieFields} field
 * @param {any}         value
 * @return {IMDBAPIService}
 */
public or(field:MovieFields, value:any):IMDBAPIService{
 return this.byField(field, value);

}

 /** 
  * Join all the promises and return the aggregated result. 
  * 
  *@return {Promise<Movie[]>} 
  */
public fetch():Promise<Movie[]>{
 return Promise.all(this.promises).then((results:any) => {
         //result is an array of movie arrays. One array per
         //promise. We need to flatten it.
         return [].concat.apply([], results);
 }); 
}
```

这里我提供了两种便捷的方法 `field` 和 `or`，它们以 `MovieField` 和一个值作为参数，创建一个新的 promise。它们都返回 `this` 以支持链式调用。`fetch` 方法将所有 promise 连接在一起，并合并它们各自的结果。在 `AngularObservableAppComponent` 中，我们现在有以下内容:

```ts

 this.IMDBAPI.byField(MovieFields.release_year, 2015) 
             .or(MovieFields.release_year, 2014)
             .or(MovieFields.phase, "Phase Two") 
             .fetch()
             .then (
                value => {
                    this.movies = value;
                    console.log("Component", value)
                },
            error => this.error = true
        );
```

这很容易阅读和理解，同时保持了 Angular 2 的所有异步能力。

# 总结

在本章中，我们学习了如何使用一些最有用的经典模式：组件、单例和观察者。我们学会了如何在纯 TypeScript 中以及使用 Angular 2 构建块来实现。本章的代码可以在这里找到：[`github.com/MathieuNls/Angular-Design-Patterns-and-Best-Practices/tree/master/chap4`](https://github.com/MathieuNls/Angular-Design-Patterns-and-Best-Practices/tree/master/chap4)。

在下一章中，我们将专注于模式，旨在简化和组织我们的 Angular 2 应用程序中的导航。
