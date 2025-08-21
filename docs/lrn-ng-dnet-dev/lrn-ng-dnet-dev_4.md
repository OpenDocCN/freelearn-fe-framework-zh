## 第四章：使用 TypeScript 与 Angular

本章讨论了 TypeScript 的基本原理以及使用 TypeScript 编写 Angular 应用程序的好处。

在这一章中，我们将涵盖以下主题：

+   什么是 TypeScript？

+   基本类型

+   接口

+   类

+   模块

+   函数

+   泛型

+   装饰器

+   TypeScript 和 Angular

## 什么是 TypeScript？

* * *

TypeScript 是由微软开发和维护的开源编程语言。它是 JavaScript 的超集，并且方便编写面向对象的编程。它应该与 JavaScript 一起编译，以在任何浏览器上运行。

TypeScript 提供了最好的工具和高级的自动完成、导航和重构功能。它用于开发客户端和服务器端的 JavaScript 应用程序。借助类、模块和接口，我们可以使用 TypeScript 构建强大的组件。

它相对于 JavaScript 提供的主要优势在于，它能够在编译时进行类型检查以避免错误。由于类型转换问题，可以避免意外的运行时错误。此外，它提供了写面向对象编程的语法糖。

## 基本类型

* * *

在编程语言中，我们处理各种小单位的数据，比如`numbers`、`sting`和布尔值。TypeScript 支持这些类型的数据，与 JavaScript 一样，支持枚举和结构类型。

### 布尔值

布尔数据类型可以保存`true`或`false`。声明和初始化这种数据类型非常简单，如下所示：

```ts
let isSaved: boolean = false; 
```

在这里，`isSaved`变量被声明为`boolean`类型，并赋值为`false`。如果开发人员错误地将一个字符串值赋给`isSaved`变量，TypeScript 会显示错误并突出显示该语句。

### 数字

数字数据类型保存浮点值。与 JavaScript 类似，TypeScript 将所有数字视为浮点值。声明和初始化数字数据类型变量可以使用以下方法：

```ts
let price: number = 101; 
```

在这里，`price`变量被声明为`number`类型，并赋值为 101。Number 类型可以包含十进制、二进制、十六进制和八进制等不同的值，如下所示：

```ts
let decimal: number = 6; 
let hex: number = 0xf00d; 
let binary: number = 0b1010; 
let octal: number = 0o744; 
```

### 字符串

字符串数据类型可以保存一系列字符。声明和初始化`string`变量非常简单，如下所示：

```ts
let authorName: string = "Rajesh Gunasundaram"; 
```

在这里，我们声明了一个名为`authorName`的变量，类型为`string`，并赋值为"`Rajesh Gunasundaram`"。TypeScript 支持使用双引号(")或单引号(')括起来的`string`值。

### 数组

数组数据类型用于保存特定类型的值的集合。在 TypeScript 中，我们可以以两种方式定义`array`，如下所示：

```ts
var even:number[] = [2, 4, 6, 8, 10]; 
```

这个语句声明了一个`number`类型的数组变量，使用`number`数据类型后的方括号([])，并赋值为从 2 到 10 的一系列偶数。定义数组的第二种方法是这样的：

```ts
let even:Array<number> = [2, 4, 6, 8, 10]; 
```

这个语句使用了泛型的数组类型，它使用了`Array`关键字后面跟着包裹`number`数据类型的尖括号（<>）。

### 枚举

枚举数据类型将具有一组命名的值。我们使用枚举器为识别某些值的常量提供友好名称：

```ts
enum Day {Mon, Tue, Wed, Thu, Fri, Sat, Sun}; 
let firstDay: Day = Day.Mon; 
```

在这里，我们有`enum`类型`Day`变量，它包含代表一周中每一天的值的系列。第二个语句展示了如何访问一天中的特定`enum`值并将其赋值给另一个变量。

### 任意

`Any`数据类型是一个可以容纳`任何`值的动态数据类型。如果将 string 类型的变量赋给整数类型的变量，TypeScript 会抛出编译时错误。如果您不确定变量将持有什么值，并且希望在赋值时免除编译器对类型的检查，您可以使用`Any`数据类型：

```ts
let mixedList:any[] = [1, "I am string", false]; 
mixedList [2] = "no you are not"; 
```

在这里，我们使用了任意类型的数组，因此它可以容纳任何类型，比如`number`，`string`和`boolean`。

### 任何

Void 实际上是什么都没有。它可用作函数的返回类型，声明这个函数不会返回任何值：

```ts
function alertMessage(): void { 
    alert("This function does not return any value"); 
} 
```

## 接口

* * *

接口是定义类行为的抽象类型。它为可以在客户端之间交换的对象提供类型定义。这使得客户端只能交换符合接口类型定义的对象；否则，我们会得到编译时错误。

在 TypeScript 中，接口定义了您代码内部和项目外部的对象的约束。让我们看一个示例，介绍如何在 TypeScript 中使用：

```ts
function addCustomer(customerObj: {name: string}) { 
  console.log(customerObj.name); 
} 
let customer = {id: 101, name: "Rajesh Gunasundaram"}; 
addCustomer(customer); 
```

类型检查器验证了`addCustomer`方法调用并检查了其参数。`addCustomer`期望一个具有`string`类型的`name`属性的对象。然而，调用`addCustomer`的客户端传递了一个具有`id`和`name`两个参数的对象。

然而，编译器会忽略对`id`属性的检查，因为它不在`addCustomer`方法的参数类型中。对于编译器来说，重要的是所需的属性是否存在。

让我们演示将`interface`作为参数类型重写方法：

```ts
interface Customer { 
  name: string; 
} 
function addCustomer(customerObj: Customer) { 
  console.log(customerObj.name); 
}  
let customer = {id: 101, name: "Rajesh Gunasundaram"}; 
addCustomer(customer); 
Customer interface. It only looks for the name property of the string type in the parameter and then allows it if present.
```

### 可选属性

在某些情况下，我们可能只想为最小的参数传递值。在这种情况下，我们可以将接口中的属性定义为可选属性，如下所示：

```ts
interface Customer { 
  id: number; 
  name: string; 
  bonus?: number; 
}  
function addCustomer(customer: Customer) {  
  if (customer.bonus) { 
    console.log(customer.bonus); 
  } 
}  
addCustomer({id: 101, name: "Rajesh Gunasundaram"}); 
```

在这里，`bonus`属性通过在`name`属性末尾添加问号（?）来定义为可选属性。

### 函数类型接口

我们刚刚看到了如何在接口中定义属性。类似地，我们也可以在接口中定义函数类型。我们可以通过给出带有返回类型的函数签名来在接口中定义函数类型。请注意，在下面的代码片段中，我们没有添加函数名：

```ts
interface AddCustomerFunc { 
  (firstName: string, lastName: string); void 
} 
```

现在，`AddCustomerFunc`准备好了。让我们定义一个函数类型变量，`AddCustomerFunc`，并将具有相同签名的函数赋值给它，如下所示：

```ts
let addCustomer: AddCustomerFunc; 
addCustomer = function(firstName: string, lastName: string) { 
  console.log('Full Name: ' + firstName + ' ' + lastName); 
} 
```

函数签名中的参数名可以变化，但数据类型不能变化。例如，我们可以修改字符串类型的`fn`和`ln`函数参数，如下所示：

```ts
addCustomer = function(fn: string, ln: string) {
console.log('Full Name: ' + fn + ' ' + ln);
} 
```

因此，如果我们改变此处参数的数据类型或函数的返回类型，编译器将抛出关于参数不匹配或返回类型不匹配`AddCustomerFunc`接口的错误。

### **数组类型接口**

我们还可以为数组类型定义一个接口。我们可以指定`index`数组的数据类型和数组项的数据类型，如下所示：

```ts
interface CutomerNameArray { 
  [index: number]: string; 
}  
let customerNameList: CutomerNameArray; 
customerNameList = ["Rajesh", "Gunasundaram"]; 
```

TypeScript 支持`number`和`string`两种类型的`index`。此数组类型接口还强制数组的返回类型与声明匹配。

### **类类型接口**

类型接口定义了类的约定。实现接口的类应满足接口的要求：

```ts
interface CustomerInterface { 
    id: number; 
    firstName: string; 
    lastName: string; 
    addCustomer(firstName: string, lastName: string); 
    getCustomer(id: number): Customer; 
}  
class Customer implements CustomerInterface { 
    id: number; 
    firstName: string; 
    lastName: string; 
    constructor() { } 
    addCustomer(firstName: string, lastName: string): void { 
        // code to add customer 
    } 
    getCustomer(id: number): Customer { 
        // code to return customer where the id match with id parameter 
    } 
}  
```

类类型接口只处理类的公共成员。因此，不可能向接口添加私有成员。

### **扩展接口**

接口可以进行扩展；扩展一个接口使其共享另一个接口的属性，如下所示：

```ts
interface Manager { 
    hasPower: boolean; 
}
interface Employee extends Manager { 
    name: string; 
} 
let employee = <Employee>{}; 
employee.name = "Rajesh Gunasundaram"; 
employee.hasPower = true; 
```

这里，`Employee`接口扩展了`Manager`接口，并将`hasPower`与`Employee`接口共享。

### **混合类型接口**

当我们希望将对象既作为函数又作为对象使用时，就会使用混合类型接口。如果对象实现了混合类型接口，我们可以像调用函数一样调用对象，或者我们可以将其作为对象使用并访问其属性。这种类型的接口使您能够将一个接口用作对象和函数，如下所示：

```ts
interface Customer { 
    (name: string): string; 
    name: string; 
    deleteCustomer(id: number): void; 
} 
let c: Customer; 
c('Rajesh Gunasundaram'); 
c.name = 'Rajesh Gunasundaram'; 
c.deleteCustomer(101); 
```

## **Classes**

* * *

类是一个可扩展的模板，用于创建具有成员变量以保存对象状态和处理对象行为的成员函数的对象。

当前版本的 JavaScript 仅支持基于函数和基于原型的继承来构建可重用组件。JavaScript 的下一个版本 ECMAScript 6 支持面向对象编程，通过添加原型化类定义和继承的语法糖。然而，TypeScript 使开发人员能够使用面向对象编程技术编写代码，并将代码编译为与所有浏览器和平台兼容的 JavaScript：

```ts
class Customer { 
    name: string; 
    constructor(name: string) { 
        this.name = name; 
    } 
    logCustomer() { 
        console.log('customer name is ' + this.name); 
    } 
}  
let customer = new Customer("Rajesh Gunasundaram"); 
```

此`Customer`类有三个成员：`name`属性、构造函数和`logCustomer`方法。`Customer`类外的最后一条语句使用`new`关键字创建`customer`类的一个实例。

## **Inheritance**

* * *

继承是指继承另一个类或对象的一些行为的概念。它有助于实现代码的可重用性，并建立类或对象之间的关系层次结构。此外，继承可以帮助您对相似的类进行强制转换。

ES5 标准的 JavaScript 不支持类，因此在 JavaScript 中无法进行类继承。但是，我们可以通过原型继承来实现类继承。让我们看看 ES5 中的继承示例。

首先，创建一个名为`Animal`的函数，如下所示。在这里，我们创建一个名为`Animal`的函数，其包含两个方法：`sleep`和`eat`：

```ts
var Animal = function() { 
    this.sleep = function() { 
       console.log('sleeping'); 
    } 
    this.eat = function() { 
       console.log('eating'); 
    } 
} 
```

现在，让我们使用原型扩展这个`Animal`函数，如下所示：

```ts
Animal.prototype.bark = function() { 
    console.log('barking'); 
} 
```

现在，我们可以创建`Animal`的实例并调用扩展函数 bark，如下所示：

```ts
var a = new Animal(); 
a.bark(); 
```

我们可以使用`Object.Create`方法来克隆父级原型并创建一个子对象。然后，我们可以通过添加方法来扩展子对象。让我们创建一个名为`Dog`的对象，并从`Animal`继承它：

```ts
var Dog = function() { 
    this.bark = new function() { 
       console.log('barking'); 
    } 
} 
```

现在，让我们克隆`Animal`的原型，并继承`Dog`函数中的所有行为。然后，我们可以使用`Dog`实例调用`Animal`方法，如下所示：

```ts
Dog.prototype = Object.create(animal.prototype); 
var d = new Dog(); 
d.sleep(); 
d.eat(); 
```

### TypeScript 中的继承

我们刚刚看到了如何使用原型在 JavaScript 中实现继承。现在，我们将看到如何在 TypeScript 中实现继承。

在 TypeScript 中，类接口可以扩展，而且我们也可以通过继承另一个类来扩展一个类，如下所示：

```ts
class SimpleCalculator { 
    z: number; 
    constructor() { } 
    addition(x: number, y: number) { 
        z = x + y; 
    } 
    subtraction(x: number, y: number) { 
        z = x - y; 
    } 
}  
class ComplexCalculator extends SimpleCalculator { 
    constructor() { super(); } 
    multiplication(x: number, y: number) { 
        z = x * y; 
    } 
    division(x: number, y: number) { 
        z = x / y; 
    } 
} 
var calculator = new ComplexCalculator(); 
calculator.addition(10, 20); 
calculator.Substraction(20, 10); 
calculator.multiplication(10, 20); 
calculator.division(20, 10); 
```

在这里，我们能够通过扩展`SimpleCalculator`的实例来访问`SimpleCalculator`的方法，因为`ComplexCalculator`扩展了`SimpleCalculator`。

### 私有/公共修饰符

在 TypeScript 中，类中的所有成员默认都是`public`的。我们必须明确添加`private`关键字来控制成员的可见性：

```ts
class SimpleCalculator { 
    private x: number; 
    private y: number; 
    z: number; 
    constructor(x: number, y: number) { 
       this.x = x; 
       this.y = y; 
    } 
    addition() { 
        z = x + y; 
    } 
    subtraction() { 
        z = x - y; 
    } 
} 
class ComplexCalculator { 
    z: number; 
    constructor(private x: number, private y: number) { } 
    multiplication() { 
        z = this.x * this.y; 
    } 
    division() { 
        z = this.x / this.y; 
    } 
} 
```

请注意，在`SimpleCalculator`类中，我们将`x`和`y`定义为`private`属性，这将不会在类外可见。在`ComplexCalculator`中，我们使用参数属性定义了`x`和`y`。这些参数属性将使我们能够在一个语句中创建和初始化成员。在这里，`x`和`y`在构造函数中创建并初始化，而不需要在其中写任何其他语句。同时，`x`和`y`是私有的，以便将它们隐藏起来以避免被外部类或模块访问。

### 访问器

我们还可以对属性实现`getters`和`setters`，以控制客户端对它们的访问。我们可以在设置属性变量的值之前或获取属性变量值之前拦截一些过程：

```ts
var updateCustomerNameAllowed = true; 
Class Customer { 
    Private _name: string; 
    get name: string { 
          return this._name; 
    } 
    set name(newName: string) { 
          if (updateCustomerNameAllowed == true) { 
                this._name = newName; 
          } 
          else { 
                alert("Error: Updating Customer name not allowed!"); 
          } 
    } 
} 
```

这里，`name`属性的`setter`确保顾客的`name`可以更新。否则，它将显示一个不可能的警报消息。

### 静态属性

这些类型的属性不是特定于实例的，并且通过类名而不是使用`this`关键字来访问：

```ts
class Customer { 
     static bonusPercentage = 20; 
     constructor(public salary: number) {  } 
      calculateBonus() { 
          return this.salary * Customer.bonusPercentage/100; 
     } 
} 
var customer = new Customer(10000); 
var bonus = customer.calculateBonus(); 
```

在这里，我们声明了一个`static`变量`bonusPercentage`，在`calculateBonus`方法中使用`Customer`类名访问它。`bonusPercentage`属性不是特定于实例的。

## 模块

* * *

JavaScript 是一种强大而动态的语言。由于根据 ES5 和更早的标准在 JavaScript 中进行动态编程的自由，我们有责任结构化和组织代码。这将使代码的可维护性更容易，并且还可以使我们轻松地定位特定功能的代码。我们可以通过应用模块化模式来组织代码。代码可以分为各种模块，并且相关代码可以放在每个模块中。

TypeScript 使得按照 ECMAScript 6 规范实现模块化编程变得更容易。模块使您能够控制变量的范围，代码的重用性和封装性。TypeScript 支持两种类型的模块：内部和外部。

### 命名空间

我们可以使用 `namespace` 关键字在 TypeScript 中创建命名空间，如下所示。在命名空间下定义的所有类都将在该特定命名空间下使用，并且不会附加到全局范围下：

```ts
namespace Inventory { 
      Class Product { 
             constructor (public name: string, public quantity: number) {   } 
      } 
      // product is accessible 
      var p = new Product('mobile', 101); 
} 
// Product class is not accessible outside namespace 
var p = new Inventory.Product('mobile', 101); 
```

要使 `Product` 类在命名空间外部可用，我们在定义 `Product` 类时需要添加 `export` 关键字，如下所示：

```ts
namespace Inventory { 
      export Class Product { 
             constructor (public name: string, public quantity: number) {   } 
      } 
} 
// Product class is now accessible outside Inventory namespace 
var p = new Inventory.Product('mobile', 101); 
```

我们也可以通过在引用文件的代码开头添加 `reference` 语句来跨文件共享命名空间，如下所示：

```ts
/// <reference path="Inventory.ts" /> 
```

### 模块

TypeScript 也支持模块。由于我们处理大量的外部 JavaScript 库，这个模块将帮助我们引用和组织我们的代码。使用 `import` 语句，我们可以导入外部模块，如下所示：

```ts
Import { inv } from "./Inventory"; 
var p = new inv.Product('mobile', 101); 
```

在这里，我们只是导入了先前创建的模块 `Inventory`，并创建了一个分配给变量 `p` 的 `Product` 实例。

### 函数

遵循 ES5 规范的 JavaScript 不支持类和模块。但是，我们尝试使用 JavaScript 中的函数式编程来实现变量的作用域和模块化。函数是 JavaScript 应用程序的构建块。

尽管 TypeScript 支持类和模块，但函数在定义特定逻辑方面起着关键作用。我们可以在 JavaScript 中定义`function`和`匿名`函数，如下所示：

```ts
//Named function 
function multiply(a, b) { 
    return a * b; 
} 
//Anonymous function 
var result = function(a, b) { return a * b; }; 
```

在 TypeScript 中，我们使用函数箭头符号定义参数的类型和返回类型，这也适用于 ES6；表示如下：

```ts
var multiply(a: number, b: number) => number =  
          function(a: number, b: number): number { return a * b; }; 
```

### 可选和默认参数

假设我们有一个具有三个参数的函数，并且有时在函数中可能只为前两个参数传递值。在 TypeScript 中，我们可以使用可选参数处理这种情况。我们可以将前两个参数定义为正常参数，将第三个参数定义为可选参数，如下面的代码片段所示：

```ts
function CutomerName(firstName: string, lastName: string, middleName?: string) { 
    if (middleName) 
        return firstName + " " + middleName + " " + lastName; 
    else 
        return firstName + " " + lastName; 
} 
//ignored optional parameter middleName 
var customer1 = customerName("Rajesh", "Gunasundaram");  
//error, supplied too many parameters  
var customer2 = customerName("Scott", "Tiger", "Lion", "King");   
//supplied values for all 
var customer3 = customerName("Scott", "Tiger", "Lion");   
```

在这里，`middleName` 是可选参数，在调用函数时可以忽略它。

现在，让我们看看如何在函数中设置默认参数。如果没有为参数提供值，我们可以定义它为配置的默认值：

```ts
function CutomerName(firstName: string, lastName: string, middleName: string = 'No Middle Name') { 
    if (middleName) 
        return firstName + " " + middleName + " " + lastName; 
    else 
        return firstName + " " + lastName; 
} 
```

在这里，`middleName`是默认参数，如果调用者未提供值，则默认值为`'No Middle Name'`。

### 剩余参数

使用剩余参数，可以将值的数组传递给函数。这在您不确定将向函数提供多少值的场景中可以使用：

```ts
function clientName(firstClient: string, ...restOfClient: string[]) { 
   console.log(firstClient + " " + restOfClient.join(" ")); 
} 
clientName ("Scott", "Steve", "Bill", "Sergey", "Larry"); 
```

在这里，注意`restOfClient`剩余参数前缀带有省略号（...），它可以保存一个字符串数组。在函数的调用者中，只有提供的第一个参数的值将被赋给`firstClient`参数，并且剩余的值将被分配给`restOfClient`作为数组值。

## 泛型

* * *

当开发可对抗任何数据类型的可重用组件时，泛型非常有用。因此，使用此组件的客户端将决定它应该对哪种类型的数据进行操作。让我们创建一个简单的函数，该函数返回传递给它的任何数据类型：

```ts
function returnNumberReceived(arg: number): number { 
    return arg; 
} 
function returnStringReceived(arg: string): string { 
    return arg; 
} 
```

正如你所见，我们需要个别方法来处理每种数据类型。我们可以使用`any`数据类型在单个函数中实现相同的功能，如下所示：

```ts
function returnAnythingReceived (arg: any): any { 
    return arg; 
} 
```

这与泛型类似。然而，我们对返回类型没有控制。如果我们传递一个数字，并且无法预测函数是否会返回该数字，函数的返回类型可以是任意类型。

泛型提供了`T`类型的特殊变量。将此类型应用到函数中，如所示，使客户端能够传递他们希望该函数处理的数据类型：

```ts
function returnWhatReceived<T>(arg: T): T { 
    return arg; 
} 
```

因此，客户端可以为各种数据类型调用此函数，如所示：

```ts
var stringOutput = returnWhatReceived<string>("return this"); // type of output will be 'string' 
var numberOutput = returnWhatReceived<number>(101); // type of output will be number 
```

请注意，在函数调用中，应该将要处理的数据类型通过尖括号（`<>`）进行包裹传递。

### 泛型接口

我们还可以使用类型变量`T`来定义泛型接口，如下所示：

```ts
interface GenericFunc<T> { 
    (arg: T): T; 
} 
function func<T>(arg: T): T { 
    return arg; 
} 
var myFunc: GenericFunc<number> = func; 
```

在这里，我们定义了一个泛型接口和`myFunc`变量的`GenericFunc`类型，将数字数据类型传递给类型变量`T`。然后，将该变量分配给名为`func`的函数。

### 泛型类

与泛型接口类似，我们也可以定义泛型类。我们使用尖括号（`<>`）中的泛型类型来定义类，如下所示：

```ts
class GenericClass<T> { 
    add: (a: T, b: T) => T; 
}  
var myGenericClass = new GenericClass<number>(); 
myGenericClass.add = function(a, b) { return a + b; }; 
```

在这里，通过将泛型数据类型传递为`number`，实例化了泛型类。因此，`add`函数将处理并加上作为参数传递的两个数字。

## 装饰器

* * *

装饰器使我们能够通过添加行为来扩展类或对象，而无需修改代码。装饰器用额外功能包装类。它们可以附加到类、属性、方法、参数和访问器。在 ECMAScript 2016 中，装饰器被提议用于修改类的行为。装饰器用`@`符号和解析为在`runtime`调用的函数的装饰器名称进行前缀。

```ts
@authorize decorator on any other class:
```

```ts
function authorize(target) { 
    // check the authorization of the use to access the "target" 
} 
```

### 类装饰器

类装饰器是在类声明之前声明的。类装饰器可以通过应用于该类的构造函数来观察、修改和替换类的定义。TypeScript 中`ClassDecorator`的签名如下所示：

```ts
declare type ClassDecorator = <TFunction extends Function>(target: TFunction) => TFunction | void; 
```

假设有一个`Customer`类，我们希望该类被冻结。其现有属性不应被移除，也不应添加新属性。

我们可以创建一个单独的类，可以接受任何对象并将其冻结。然后，我们可以使用`@freezed`装饰器来装饰`Customer`类，以防止从类中添加新属性或移除现有属性：

```ts
@freezed 
class Customer {  
  public firstName: string; 
  public lastName: string; 
  constructor(firstName : string, lastName : string) {  
    this.firstName = firstName; 
    this.lastName = lastName; 
  } 
} 
freezed decorator:
```

```ts
function freezed(target: any) { 
    Object.freeze(target); 
} 
```

在这里，`freezed`装饰器获取`target`，即正在被装饰的`Customer`类，并在执行时将其冻结。

### 方法装饰器

方法装饰器是在方法声明之前声明的。此装饰器用于修改、观察或替换方法定义，并且应用于方法的属性描述符。下面的示例代码显示了一个应用了方法装饰器的简单类：

```ts
class Hello { 
    @logging 
    increment(n: number) { 
        return n++; 
    } 
} 
logging function:
```

```ts
function logging(target: Object, key: string, value: any) { 
            value = function (...args: any[]) { 
            var result = value.apply(this, args); 
            console.log(JSON.stringify(args)) 
            return result; 
        } 
    }; 
} 
```

方法装饰器函数接受三个参数：`target`，`key`和`value`。`target`参数保存了正在被装饰的方法；`key`保存了被装饰方法的名称，`value`是指定对象上存在的特定属性的属性描述符。

当调用`increment`方法时，`logging`装饰器被调用，并且`values`参数被传递给它。`logging`方法将在控制台上记录有关传递的参数的详细信息。

### 访问器装饰器

Accessor decorators are prefixed before the accessor declaration. These decorators are used to observe, modify, or replace an accessor definition and are applied to the property descriptor. The following code snippet shows a simple class with the accessor decorator applied:

```ts
class Customer {  
  private _firstname: string; 
  private _lastname: string; 
  constructor(firstname: string, lastname: string) { 
        this._firstname = firstname; 
        this._lastname = lastname; 
  } 
  @logging(false) 
  get firstname() { return this._firstname; } 
  @logging(false) 
  get lastname() { return this._lastname; } 
} 
@logging decorator:
```

```ts
function logging(value: boolean) { 
    return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) { 
        descriptor.logging = value; 
    }; 
} 
```

`logging`函数将`Boolean`值设置为`logging`属性描述符。

### 属性装饰器

属性装饰器是前缀到属性声明。在 TypeScript 源代码中，`PropertyDecorator`的签名是这样的：

```ts
declare type PropertyDecorator = (target: Object, propertyKey: string | symbol) => void; 
firstname property is decorated with the @hashify property decorator:
```

```ts
class Customer {  
  @hashify 
  public firstname: string; 
  public lastname: string; 
  constructor(firstname : string, lastname : string) {  
    this.firstname = firstname; 
    this.lastname = lastname; 
  } 
} 
@hashify property decorator function:
```

```ts
function hashify(target: any, key: string)
 { 
  var _value = this[key];
  var getter = function ()
    { 
        return '#' + _value; 
    }; 
  var setter = function (newValue)
   { 
      _value = newValue; 
    }; 
  if (delete this[key])
 { 
    Object.defineProperty(target, key,
 { 
      get: getter, 
      set: setter, 
      enumerable: true, 
      configurable: true 
    }); 
  } 
} 
```

`_value`变量保存了被装饰的属性的值。`getter`和`setter`函数都可以访问`_value`变量，在这里，我们可以通过添加额外行为来操纵`_value`变量。我在`getter`中连接了`#`来返回标记的名字。然后，使用`delete`操作符从类原型中删除原始属性。然后，一个新属性将会被创建，拥有原始属性名和额外的行为。

### 参数装饰器

参数装饰器是前缀到参数声明，并且它们应用于类构造函数或方法声明的函数。这是`ParameterDecorator`的签名：

```ts
declare type ParameterDecorator = (target: Object, propertyKey: string | symbol, parameterIndex: number) => void; 
```

现在，让我们定义`Customer`类，并使用参数装饰器来装饰一个参数，以使其必需，并验证其是否被提供：

```ts
class Customer { 
    constructor() {    } 
    getName(@logging name: string) { 
        return name; 
    } 
} 
```

在这里，name 参数已经被 `@logging` 装饰器修饰。参数装饰器隐式接收三个输入，即带有该装饰器的类的原型，带有该装饰器的方法的名称，以及被装饰的参数的索引。参数装饰器 `logging` 的实现如下所示：

```ts
function logging(target: any, key : string, index : number) {  
  console.log(target); 
  console.log(key); 
  console.log(index); 
} 
```

在这里，`target` 是带有装饰器的类，`key` 是函数名，`index` 包含参数索引。这段代码仅将 `target`、`key` 和 `index` 记录在控制台中。

## TypeScript 和 Angular

* * *

正如你在本章中所见，TypeScript 具有强大的类型检查能力，并支持面向对象编程。由于这些优势，Angular 团队选择了 TypeScript 来构建 Angular。Angular 完全重写了核心代码，使用 TypeScript，并且它的架构和编码模式完全改变了，就像你在 第二章 和 第三章 中看到的，*Angular 基本构件部分 1* 和 *Angular 基本构件部分 2*。因此，使用 TypeScript 编写 Angular 应用是最佳选择。

我们可以在 Angular 中实现类似 TypeScript 中的模块。Angular 应用中的组件实际上是一个带有 `@Component` 装饰器的 TypeScript 类。使用 import 语句可以将模块导入到当前的类文件中。`export` 关键字用于指示该组件可以在另一个模块中被导入和访问。使用 TypeScript 开发的示例组件代码如下所示：

```ts
import {Component} from '@angular/core' 
@Component({ 
  selector: 'my-component', 
  template: '<h1>Hello my Component</h1>' 
}) 
export class MyComponent { 
  constructor() {  } 
} 
```

## 总结

* * *

Voila! 现在你已经学会了 TypeScript 语言的基础知识。我们首先讨论了 TypeScript 是什么以及它的优势。然后，你学习了 TypeScript 中各种数据类型，并附有示例。我们还深入讲解了 TypeScript 中的面向对象编程和接口、类、模块、函数和泛型，并提供了示例。接下来，你学习了各种类型的装饰器及其实现方法，并给出了示例。最后，我们看到了为什么我们应该使用 TypeScript 来编写 Angular 应用以及使用 TypeScript 编写 Angular 应用的好处。

在下一章中，我们将讨论如何使用 Visual Studio 创建 Angular 单页面应用程序。
