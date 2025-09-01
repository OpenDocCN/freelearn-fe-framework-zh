# 4

# 像专业人士一样构建表单

**表单**是许多前端应用的核心。它们允许您收集用户输入以保存并处理您的应用用户提供的数据。构建表单是 Angular 擅长的方面之一。Angular 提供了两种不同的构建表单的方法，这两种方法都提供了工具来验证单个表单字段以及整个表单的有效性。

在本章中，您将学习如何创建 Angular 的**模板驱动**和**响应式**表单，同步您的表单字段与应用程序状态，验证表单和单个表单字段，并了解动态表单创建等高级概念。到本章结束时，您将能够像专业人士一样创建表单！

本章将涵盖以下主要主题：

+   理解 Angular 中不同类型的表单

+   构建模板驱动表单

+   构建响应式表单

+   动态创建表单

# 理解 Angular 中不同类型的表单

Angular 提供了两种不同类型的表单：模板驱动表单和响应式表单。本节将讨论模板驱动表单和响应式表单之间的区别，并帮助您评估哪种方法最适合您的需求。

让我们先解释一下构建表单的两种方法的关键特点。

### 模板驱动表单的特点

模板驱动表单是一种构建表单的方式，其中 HTML 模板扮演着核心角色。模板驱动表单在 HTML 模板中隐式定义表单控件和验证规则，使用指令。这意味着表单控件和验证规则是由您放置在 HTML 元素上的指令创建和管理的，而不是在 TypeScript 文件中手动创建表单控件和验证，并直接与表单 API 进行通信。

模板驱动的方法依赖于使用`ngModel`指令进行双向数据绑定，以同步用户输入和程序性更改的数据模型中的变化。当您将`ngModel`指令添加到 HTML 元素时，该指令会为您创建并管理一个`FormControl`实例。这些由`ngModel`指令创建的`FormControl`类用于跟踪和验证表单中单个字段的状态。

当使用模板驱动表单时，表单数据是可变的，这意味着您不是使用表单 API 来更新表单值，而是直接更改在`FormControl`实例中使用的值。

重要提示

还需要注意的是，模板驱动表单的数据流是异步的，通过事件和订阅进行更新。虽然这种异步行为由系统管理，但了解这一点很重要，因为它可能会影响测试，并且如果您不知道数据流是异步的，有时会导致意外的行为。

模板驱动的表单易于构建且快速，与信号（我们将在*第七章*中了解更多关于信号的内容）结合使用效果很好，可以减少触发变更检测的次数，从而提高性能。

然而，模板驱动的表单的数据模型和表单控件复用和测试起来比较复杂；此外，在构建大型表单时，你的 HTML 模板可能会变得臃肿。当创建需要严格测试、可重用表单模型和控件或需要动态构建方式的表单时，响应式表单可能更为合适。

### 响应式表单的特点

响应式表单提供了一种灵活的、以模型驱动的创建表单的方法。与模板驱动的表单相比，响应式表单提供了更多的程序化控制。在使用响应式方法时，你通过在 TypeScript 文件中创建`FormGroup`、`FormControl`和`FormArray`实例来显式定义表单模型。响应式表单的显式特性允许实现更复杂的逻辑、易于测试以及更好的表单控件和模型的重用性。在响应式表单中，验证规则也通过`Validator`类或自定义验证器显式定义，从而允许更复杂的验证。

响应式表单提供了对表单状态的精细控制，允许你通过程序方式设置、获取和操作值。一旦创建了一个表单及其表单控件，就不能直接修改其值。这使得响应式表单不可变。不可变的表单提供了一个更可靠的数据模型，这反过来又导致错误更少。

由于你直接使用表单 API 在你的 TypeScript 文件中定义表单模型，因此为响应式表单创建的表单模型可以很容易地复用和测试。在编写测试时，响应式表单非常简单，因为你可以直接在测试中使用表单 API，就像你在组件类中做的那样。

响应式表单在视图（我们所说的视图是指浏览器中显示的 HTML 模板）和数据模型之间具有同步的数据流。因此，Angular 可以精确地知道何时在响应式表单上运行变更检测，从而提高性能。

与模板驱动的表单相比，响应式表单虽然需要更多的初始设置，但在处理大型、复杂的表单时更为合适，因为在这种情况下，额外的控制、测试以及表单控件和模型的可重用性至关重要。

### 模板驱动的表单和响应式表单之间的主要差异

下表概述了模板驱动的表单和响应式表单之间的差异：

|  | **模板驱动的表单** | **响应式表单** |
| --- | --- | --- |
| 表单、模型和验证创建 | 隐式使用 HTML 模板中的指令 | 显式使用 TypeScript 文件中的类 |
| 设置和创建表单 | 易于设置和简单 | 需要更多的初始设置，可能感觉更复杂 |
| 数据模型 | 非结构化和可变 | 结构化和不可变 |
| 数据流 | 异步 | 同步 |
| 与信号兼容 | 好 | 不好 |
| 测试性 | 单元测试困难 | 使用表单 API 容易测试 |
| 表单的可重用性和动态创建 | 更难重用或动态构建 | 容易重用和动态构建 |

表 4.1：模板驱动表单和响应式表单之间的关键区别

现在你已经了解了 Angular 模板驱动和响应式表单的关键特性，让我们深入探讨并学习如何创建这两种类型的表单。

# 构建模板驱动表单

在本节中，我们将构建一个模板驱动表单。你将学习如何将数据绑定到输入字段，分组表单字段，并在模板驱动表单中执行内置和自定义验证规则。你还将了解模板驱动表单在幕后是如何工作的，以更好地理解模板驱动表单。

到本节结束时，你将能够构建健壮的模板驱动表单，并为我们的演示应用程序创建一个模板驱动表单来添加费用。

## 创建带有表单组件的表单库

在我们开始创建表单之前，我们需要一个新的库。我们将使用我们在*第一章*中制作的自定义 Nx 生成器来生成新的库。

你可以就如何分离表单库进行辩论。你可以创建一个包含特定领域所有表单的库，为特定领域的每个应用程序创建一个表单库，或者为每个表单创建一个新的库。

使用每个表单的单个库是使用 Nx 缓存和增量构建系统的最佳方式，但它也在开发和维护方面带来了一些额外的开销。如果你的组织有很多在多个应用程序之间重用的表单，将它们拆分到单独的库中可能值得额外的设置，因为它将加快你的构建和管道。

在我们的示例中，我将创建一个专门用于 *expenses-registration* *应用程序* 的表单库，以便该库将包含此特定应用程序的所有表单：

1.  运行自定义 Nx 生成器以创建库。将其命名为`expenses-registration-forms`。

1.  选择域为**finance**，类型为**ui**。然后，点击**生成**。

1.  一旦生成了库，重新启动 VS Code，以便 Nx 规范与你的新库更新。

1.  我们将使用 Nx 生成器为我们的模板驱动表单创建一个组件。将组件命名为`add-expense`。

1.  选择为项目创建的新库，勾选**独立**复选框，点击**显示所有选项**，并为**changeDetection**选项选择**OnPush**。

1.  在右上角点击**生成**。

1.  一旦生成了组件，将其导出到库的`index.ts`文件中。我喜欢以`form`结束表单选择器，所以我将组件选择器重命名为`bt-libs-ui-add-expense-form`。

现在我们已经创建了库和组件，我们可以开始创建模板驱动的表单。

## 创建模板驱动的表单

我们将首先创建一个简单的 HTML 表单，然后逐步将其转换为 Angular 模板驱动的表单。将以下内容添加到您的`add-expense.component.html`文件中：

```js
<form>
  <div class=»form-field">
    <label for=»description»>Description:</label>
    <input type=»text» id=»description» name=»description»>
  </div>
  …………
  <button type=»submit»>Submit</button>
</form>
```

您可以用任何您想要的额外表单字段替换**点**。在这个例子中，我将拥有四个字段：**描述**、**不含增值税的金额**、**增值税百分比**和**日期**。

您可以在本书的 GitHub 仓库中找到完整的表单和样式：[`github.com/PacktPublishing/Effective-Angular/tree/feature/chapter-four/building-forms-like-a-pro`](https://github.com/PacktPublishing/Effective-Angular/tree/feature/chapter-four/building-forms-like-a-pro)。

将`AddExpenseForm`类导入到`expenses-overview-page.component.ts`中，并在相应的 HTML 文件中添加组件选择器以显示我们的 HTML 表单。当您点击提交按钮时，您会注意到页面被重新加载；这是提交表单时的默认原生行为。然而，当使用 Angular 等框架构建现代应用程序时，我们期望更好的用户体验，即在不重新加载页面的情况下处理表单提交，就像我们路由到页面而不重新加载页面一样。

我们将通过导入`FormsModule`开始将我们的原生 HTML 表单转换为 Angular 模板驱动的表单。这个`FormsModule`包含了我们构建模板驱动的表单所需的所有指令。如果您正在使用`NgModules`构建，您必须在相应的`NgModule`中导入`FormsModule`。我们正在使用独立组件，因此我们将`FormsModule`导入到构建表单的组件中。将`FormsModule`添加到我们新创建的`add-expense`组件的组件装饰器的`imports`数组中：

```js
imports: [CommonModule, FormsModule, the page won’t be reloaded when you click your form’s submit button, which is precisely what we wanted.
But how can this be, since we didn’t change our form? The Angular `ngForm` directive prevents the default browser behavior when submitting the form, and because of that, the page isn’t reloading. But we didn’t add the directive to our form, so why isn’t the page reloading?
The answer lies in the selector of the `ngForm` directive. When we inspect the selector of the `ngForm` directive, we can see that the directive is automatically applied to all form tags, and because we’ve added `FormsModule`, the `ngForm` directive is applied to the form in our component. Even though the directive is automatically applied to HTML form tags, it’s advised to write declarative code and manually apply the directive to form tags. So, add the directive to your form tag like this:

```

<form #addExpenseForm="ngForm">………</form>

```js

 On the left, we added a template variable called `#addExpenseForm`, while on the right, we assigned this template variable with an instance of the `ngForm` directive. By assigning the directive to a template variable, we can use the directive in all sibling and child elements of our HTML form tag.
Now that we’ve added the forms module and the `ngForm` directive, we can start to configure the fields of our template-driven form.
Configuring template-driven form fields
To have a template-driven form, we need to connect the form’s input fields to an object in our component class and bind the fields to our `ngForm` instance to validate the entire form.
Let’s start by creating an interface for the object we want to use in our form.
In the `add-expense` folder, create a new file, `add-expense.interface.ts`, and add the interface reflecting the fields in your form.
For our example, this is the interface definition:

```

export interface AddExpense {

description: string;

amountExclVat: number | null;

vatPercentage: number | null;

date: Date | null;

}

```js

 Also, export the interface in the `index.ts` file of the library. Now, in `add-expense.component.ts`, define a property of the `AddExpense` type, as follows:

```

@Input() expenseToAdd: AddExpense = { description: '', amountExclVat: null, vatPercentage: null, date: null }

```js

 Here, we used an input with a default value. We used an input because the form will be a dumb component, and the parent component will input any default values other than empty values.
Note that you don’t have to use an object in a template-driven form; you can also use separated properties or a combination of properties and objects. Once you’ve defined the object, you need to bind the properties of the `expenseToAdd` object to the fields of your form. We can do this using the `ngModel` directive.
When you add the `ngModel` directive, behind the scenes, Angular registers `FormControl` in the `ngForm` instance. The `ngModel` directive allows for two-way data binding, meaning the values of the `expenseToAdd` object and our form will automatically be synchronized if we change the properties in our component class or if the user enters values inside the form inputs.
You connect the properties of the `expenseToAdd` object to the form inputs by adding the `ngModel` directive and the `name` attribute to each of the form inputs, like this:

```

<input ngModel 指令，就像我们在*第二章*中为输入和输出进行双向数据绑定时所做的那样。

就像使用双向数据绑定来绑定输入值一样，您也可以将这个`ngModel`拆分为单独的输入和输出，如下所示：

```js
[ngModel]="expenseToAdd.description" (ngModelChange)="expenseToAdd.description = $event"
```

重要提示

使用输入和输出分离可以在处理信号或在进行数据模型绑定之前执行额外逻辑时非常有用。

当您创建输入字段时，向字段添加 `name` 属性很重要，因为这为 `ngForm` 实例提供了一个唯一的键来跟踪表单字段。如果您没有添加 `name` 属性，您的浏览器控制台将显示错误。现在，继续添加 `ngModel` 指令和 `name` 属性到您表单中的所有字段。在添加所有 `ngModel` 指令和 `name` 属性后，您可以通过临时将以下代码添加到您的 HTML 模板中来确认双向数据绑定是否工作：

```js
{{ expenseToAdd | json }}
```

在将前面的代码添加到您的 HTML 模板后，`expenseToAdd` 对象将在您的屏幕上以 JSON 格式显示。当您开始在表单输入框中键入时，您可以看到 `expenseToAdd` 对象的属性正在更新。反之，当您在组件类中为 `expenseToAdd` 的属性赋值时，表单也会更新，就这样，您已经创建了一个具有双向数据绑定的表单。

`ngModel` 和 `ngForm` 指令还有一些其他有趣的配置，非常有用。让我们首先检查 `ngModelOptions`。

其他表单字段选项

您可以使用 `ngModelOptions` 来配置模板驱动表单中的表单控件实例。`ngModelOptions` 指令可以用来定义 `name` 属性，控制更新行为，或者将 `ngModel` 实例标记为独立。

您可以通过将指令添加到声明 `ngModel` 指令的输入字段中来添加 `ngModelOptions`：

```js
[ngModelOptions]="{name: 'description', updateOn: 'blur', standalone: false}"
```

让我们更深入地了解您可以在 `ngModelOptions` 指令上设置的属性。

使用名称属性

当您设置 `ngModelOptions` 的 `name` 属性时，您可以移除 `input` 字段上的 `name` 属性，因为使用 `ngModelOptions` 中的 `name` 属性与提供 `name` 属性相同。

使用 `updateOn` 属性

接下来，我们有 `updateOn` 属性，它控制表单控件的更新行为，可以取三个值——`change`、`blur` 或 `submit`：

+   `change`：这是默认值，除非整个表单的更新策略被更改为其他值，否则不需要显式设置。使用 `change` 值，表单控件将在每次按键时更新。

+   `blur`：当您将 `updateOn` 设置为 `blur` 时，表单控件将在您从相关输入字段失去焦点（即模糊）时更新。这可以在您运行一些可能需要大量时间或资源的逻辑时很有用。如果您在每次按键时运行重逻辑，您的性能将受到影响。

+   `submit`：如果您将 `updateOn` 设置为 `submit`，表单控件只有在表单提交时才会更新。就像 `blur` 一样，`submit` 选项可以帮助提高您表单的性能。

你还可以通过在 HTML 表单标签上使用 `ngFormOptions` 指令来更改你表单中所有表单控件的更新行为。`ngFormOptions` 指令只接受 `updateOn` 属性。当你更改整个表单的更新行为时，你仍然可以使用 `ngModelOptions` 指令覆盖它。

使用 `standalone` 属性

最后，我们有 `standalone` 属性。当你将表单控件标记为独立时，表单控件不会在 `ngForm` 实例中注册自己。当一个表单控件被标记为独立时，你不需要提供 `name` 属性，因为 `ngForm` 实例不需要跟踪输入的值。确定表单的有效性也不会考虑标记为独立的字段。这在你有不代表表单模型的表单字段时非常有用。

独立表单控件的另一个场景是单个输入，你不需要创建整个表单，但你想使用 `ngModel` 指令。一些例子可能是一个主题切换、语言选择或搜索。

分组模板驱动表单字段

通常，你有一组属于一起的表单字段。一个很好的例子是地址字段，如街道、邮政编码和房屋号码。你可能想作为一个组检查字段的有效性，添加样式或用户反馈，或者对组执行验证逻辑而不是单个字段。

对于这些用例，Angular 提供了 `FormGroup` 类。在模板驱动表单中，你可以使用 `ngModelGroup` 指令创建 `FormGroup` 类。当你声明 `ngModel` 组时，Angular 将在幕后创建一个 `FormGroup` 类。

声明 `ngModelGroup` 非常简单！只需将 `ngModelGroup` 指令添加到 HTML 标签中，该标签包含带有 `ngModel` 指令的多个 HTML 元素。

例如，我们可以将金额、不含增值税和增值税百分比字段分组。

我们可以这样操作：

1.  将金额和增值税百分比字段的 HTML 包装在 `fieldset` 标签内，并在该 `fieldset` 标签上声明 `ngModelGroup` 指令。

1.  为您的 `FormGroup` 分配一个名称，然后就可以了：

    ```js
    <fieldset ngModelGroup="amount"></fieldset>
    ```

现在所有输入字段都已声明并使用 `ngModel` 指令绑定到您的数据字段，您需要一种方式来监听表单提交。

提交模板驱动表单

如你所见，我们在提交按钮上添加了 `submit` 类型：

```js
<button type="submit">Submit</button>
```

这将触发一个提交事件。当这个提交事件发生时，Angular 将触发其自身的内部提交事件，称为 `ngSubmit`。我们可以像监听任何其他浏览器事件或组件输出一样监听这个 `ngSubmit` 事件：

```js
<form #addExpenseForm="ngForm" ngSubmit on the form tag; on the right-hand side, you declare the ngSubmit event in round brackets; and on the left-hand side, you define a function that’s declared in the component class to handle the submission.
For our example, we will create an output event to output a clone of the form object and reset the form using the `ngForm` instance. To achieve this, we need a way to access the `ngForm` instance in our component class; we can do this using the `@``ViewChild` directive:

```

@ViewChild('addExpenseForm') form!: NgForm;

```js

 Next, we need to add the component’s output, which will allow us to send the submitted values to the parent component so that it can act upon it accordingly:

```

@Output() addExpense = new EventEmitter<AddExpense>();

```js

 Lastly, we need to add the `onSubmit` method with our logic:

```

onSubmit() {

this.addExpense.emit(structuredClone(this.expenseToAdd));

this.form.reset();

}

```js

 As you can see, we used the native `structuredClone` method to send a clone of the `expenseToAdd` object to the parent. We sent a clone because `expenseToAdd` is a reference object, meaning it will be cleared everywhere (also in the parent component) after we call `reset` on the form, and it will clear the object we bound to the `ngForm` instance.
Now that we’ve added the submit logic, we’ll start exploring validation rules, control status, and how to display messages to the user based on the control statuses and validity of the form and its controls.
Using built-in validation rules for template-driven forms
For a good user experience, it’s important to provide good user feedback on our form. You want to indicate when your form fields are valid or still require some changes to be made by the user. You also want to prevent the user from submitting the form when not all the fields are valid and provide good error messages for incorrect or incomplete form fields.
To achieve these feats, you must add form validations to the form fields and use the control statuses to add or remove styling and error messages. Let’s start by exploring how to validate form fields in template-driven forms.
Validating the form fields of template-driven forms can be done using directives. Most validations can be done using the built-in validation directives, but when needed, you can also create your own directives to validate template-driven form fields.
You can find all built-in validation directives on the official Angular website: [`angular.io/api/forms/Validators`](https://angular.io/api/forms/Validators).
Let’s learn how to add validators to our example form. We will start by making our form fields required using the `required` directive. You can apply the `required` validator by adding this directive to your `input` fields:

```

<input max 验证器。你仍然可以输入大于 100 的数字，但表单字段将是无效的。

要应用`max`验证器，将以下内容添加到输入标签中：

```js
[max]="100"
```

你也可以有条件地添加验证器。使用方括号表示法，就像我们使用`max`验证器一样，并为其提供一个属性值，当该值设置为`null`时，验证规则将被禁用：

```js
[max]="null" [disabled]="null"
```

如果你愿意，你可以向你的表单添加额外的验证规则；在 Angular 文档中查看验证器，并添加你想要使用的验证规则指令。

我简要地提一下模式验证器，因为这是一个特殊的验证器。模式验证器可以用于许多用例，因为它接受一个正则表达式，并检查输入字段中的值是否与正则表达式模式匹配。其他验证器用于单一目的，例如检查最大输入值或字段是否有值。

现在你已经知道了如何添加内置的验证规则，让我们来看看我们如何根据表单和表单控件的状态和有效性来样式化表单字段。

根据控制状态值对表单和表单字段进行样式设置

为了为你的应用程序用户提供良好的用户体验，提供有关表单及其字段状态的可视反馈非常重要。做到这一点的最佳方式是利用你的表单及其表单控件的控制状态。

表单以及其`FormGroup`和`FormControl`实例，都由 Angular 通过控制状态和相应的 CSS 类进行更新。提供有关表单及其字段状态的可视反馈的最佳方式是为控制状态 CSS 类提供样式。因为这些样式在所有应用程序中都是共享的，所以将它们作为全局样式创建是一个好习惯。

让我们从在共享库文件夹下创建一个新的`global-styling`文件夹开始。在这个`global-styling`文件夹中，创建一个名为`form-control-status.scss`的文件。在你创建好文件夹和文件后，在*Nx monorepo*根目录下的`package.json`文件中的`devDependencies`部分添加以下内容：

```js
"@global/styling": "file:libs/shared/global-styling"
```

接下来，你需要运行`npm install`，这样你就可以在*Nx monorepo*中的 CSS 文件中使用`@global/styling`来导入全局样式。

现在，是时候在我们的新创建的 CSS 文件中添加控制状态 CSS 类的样式了。

Angular 为我们提供了八个不同的控制状态 CSS 类，通过这些类我们可以对表单及其表单组和控件进行样式设置：

+   `ng-valid`：当表单控件或组根据验证规则有效时，应用此样式。当所有组和控件都有效时，应用于整个表单。

+   `ng-invalid`：当表单控件未通过所有验证规则时，应用此样式。当一个或多个控件无效时，应用于整个表单。

+   `ng-pending`：当异步验证正在进行验证时，应用此样式。

+   `ng-pristine`: 这是在未交互的表单控件上应用的。当没有任何表单控件被交互时，它应用于表单。

+   `ng-dirty`: 这是在已交互的表单控件上应用的。当至少有一个表单控件被交互时，它应用于表单。

+   `ng-untouched`: 这是在用户未聚焦或未交互过的表单控件上应用的。当没有字段被聚焦或交互时，它应用于表单。

+   `ng-touched`: 这是在用户已聚焦或交互过的表单控件上应用的。当至少有一个字段被聚焦或交互时，它应用于表单。

+   `ng-submitted`: 这是在表单提交时应用于表单元素的。

您可以使用这些 CSS 类来根据您的需求样式化表单。我们将在 `add-expense` 表单中根据其有效性样式化表单字段，但仅在表单已被触摸时这样做。

您可以在 `form-control-status.scss` 文件中添加以下内容：

```js
form.ng-touched {
  .ng-valid:not(fieldset)  {
    border-left: 5px solid #42A948; /* green */
  }
  .ng-invalid:not(form, fieldset) {
    border-left: 5px solid #a94442; /* red */
  }
}
```

现在，我们只需要将此文件导入到 `add-expense` 表单的 CSS 文件中。打开您的 `add-expense.component.scss` 文件，并在文件顶部添加以下 `import` 语句以导入 `form-control-status` 样式：

```js
@import '@global/styling/form-control-status';
```

添加导入后，样式将应用于表单。

现在您已经知道如何使用控件状态类来样式化表单及其表单组和控件，让我们学习如何根据表单控件的状态向用户显示消息。

根据表单控件的状态显示消息

您可以通过在表单字段无效时显示消息来进一步提高用户体验。您可以通过使用由 `ngModel` 创建的表单控件实例来实现这一点。

首先，在您声明 `ngModel` 的输入中添加一个模板变量，并将 `ngModel` 实例分配给模板变量，以便您可以在整个模板中使用它：

```js
<input required [(ngModel)]="expenseToAdd.description"
       #description template variable and assigned it with ngModel. Now, you can access and control the form control instance created by the ngModel instance through the #decscription template variable.
Let’s add a `span` element with an error message underneath the input element:

```

<span>This field is required</span>

```js

 Next, we want to ensure this message is only shown when the form has been touched, and the input validator needs to be satisfied. We can do this by using `ngForm` and the form control instance, which we bound to the template variables. The form control instance exposes several properties that we can check to determine the status of the correlating input field.
Like the control status CSS classes, the form and form control itself expose properties to assess the same statuses: `valid`, `invalid`, `pending`, `pristine`, `dirty`, `touched`, and `untouched`. The form control also exposes `disabled` and `enabled` as checks of the disabled status of the correlating input field.
Besides these Boolean properties, the form control exposes multiple values and methods; you can find all the properties on the official Angular website: [`angular.io/api/forms/FormControl`](https://angular.io/api/forms/FormControl).
Now, without further ado, let’s add a `*ngIf` statement to our span element so that it’s only displayed when the input form is touched and the required validator of the form control isn’t satisfied:

```

*ngIf="addExpenseForm.touched && description.hasError('required')"

```js

 As you can see, we use the `hasError` method to check for the required validator. You can go ahead and add template variables for `ngModel` and error messages for the other fields in the form. For our VAT percentage field, we will add an additional message to check for the `max` validator:

```

<span *ngIf="addExpenseForm.touched && hasError with the max parameter to get the validity of the max validator. If you want to display the message under other conditions, you can change the *ngIf statement however you see fit.

提高您表单用户体验的另一种常见方法是防止用户在表单尚未有效时提交表单。您可以通过将表单的有效性绑定到提交按钮的 `disabled` 属性来实现这一点。

```js
<button ngForm instance and made the button invalid when the invalid property of the form returned true.
Now that we’ve enhanced the user experience of our form by adding validation rules and supplying visual user feedback through styling and error messages, let’s move on and see how we can create custom validators for template-driven forms.
Custom validators for template-driven forms
To finish this section, we will learn how to create custom validators for template-driven forms. The built-in validators will cover most scenarios, but in some cases, you need to create custom validators – for example, when you want to check values in your database or state when you need to perform cross-field validations or need to perform other validations that can’t be done with built-in validators.
All the built-in validators for template-driven forms are directives, so we also need to create a directive to build custom validators.
Let’s start by creating a new library with our custom Nx generator. Name the library `form-validators`; then, select `max-word-count` and check the `template-driven-validators` folder and move the directive inside it. Now, add an export for the directive inside `index.ts`:

```

export * from './lib/template-driven-validators/max-word-count.directive';

```js

 Now, let’s add some validation logic inside our directive. We’ll start by creating an input for our custom validator directive:

```

@Input('btLibsUtilMaxWordCount') maxWords = 1;

```js

 Next, we must implement the `Validator` interface:

```

导出类 MaxWordCountDirective 实现了 Validator 接口。

```js

 The `Validator` interface requires you to implement a `validate` function:

```

validate(control: AbstractControl): ValidationErrors|null{}

```js

 As you can see, the `validate` function takes `AbstractControl` as input and returns either `null` or a `ValidationErrors` object. Inside the `validate` function, we will add our validation logic. When the validation passes, we will return `null`, and when the validation doesn’t pass, we will return a `ValidationErrors` object. To perform some validation logic, we need to access the form control of the HTML element on which our directive is declared.
The `AbstractControl` function parameter gives you access to a `FormControl` or `FormGroup` instance, depending on which HTML element you place the directive. When you place the directive on HTML elements that declare `ngModel`, you will receive a `FormControl` instance; if you put the directive on an HTML element with `ngModelGroup` declared, you will receive a `FormGroup` instance.
In our example, we will use the directive on elements with `ngModel` on it and receive a `FormControl` instance through the `function` parameter. We want to check the value of our form control and determine if more words are in the value than we defined in our `maxWords` input. When there are more words, our validation fails, and we return a `ValidationErrors` object; otherwise, we return `null`, which means our validation passes.
First, we must check the number of words:

```

const wordCount = control?.value?.trim().split(' ').length;

```js

 As you can see, we use `control.value` to get the value from the form control. If there is a value, we trim it and split it into spaces to get the number of words in the string. Next, we check if the number of words is bigger than the `maxWords` input; if that is the case, we return the error object; otherwise, we return `null`. The `error` object can have any format you like:

```

返回 wordCount > this.maxWords ?   {btLibsUtilMaxWordCount: { count: wordCount }} : null;

```js

 That is all the logic we need for our custom validator. But there is still one problem: when we declare the directive on our form, the form won’t know this directive is a custom validator; it will think it’s just a regular directive. Instead, we want our form to register the directive as a validator so that the form takes the directive into account when determining the form’s validity and its form groups and controls. We can achieve this by adding a provider to the directive decorator:

```

providers: [{

provide: NG_VALIDATORS,

useExisting: MaxWordCountDirective,

multi: true

}]

```js

 By adding the `NG_VALIDATORS` provider to our directive, the `ngForm` instance will register the directive as a validator and include the directive when determining if the form and its groups and controls are valid.
To use the directive, you need to add the directive class’ name to the imports of our `add-expense` component since both the directive and our component are standalone. After adding the directive to the imports array, you can use the directive in the HTML template:

```

<input hasError 方法在控件上，就像我们检查必填错误时那样：

```js
description.hasError('btLibsUtilMaxWordCount')
```

当你查看我们指令中返回的`error`对象时，你可能会注意到我们包含了一个带有我们表单控件中使用的单词数的`count`属性：

```js
{btLibsUtilMaxWordCount: { count: wordCount }}
```

你可以通过在表单控件实例上使用`getError`方法来检索`count`值：

```js
description.getError('btLibsUtilMaxWordCount').count
```

如你所见，我们使用了描述模板变量，它持有表单控件实例的引用，并调用`getError`来检索错误对象。你可以使用`error`对象在错误消息中向用户显示额外信息；在我们的例子中，你可以包括当前的单词数。

通过这样，你就知道了如何向模板驱动的表单添加自定义验证器，并在自定义错误发生时显示错误消息。接下来，我们将学习如何使用自定义验证器验证表单组。

使用自定义验证器验证表单组

在我们当前的示例中，我们正在使用在它上面声明了`ngModel`的 HTML 元素来声明我们的自定义验证器指令。然而，在某些情况下，你可能想要执行跨字段验证或一次性验证多个字段。一个典型的例子是，当你有一个密码和确认密码输入字段在你的表单中，并想要检查这两个字段是否持有相同的值。

当你想要对一组字段执行验证逻辑时，你可以在你的表单中使用`ngModelGroup`来分组字段，就像我们使用 VAT 字段那样。接下来，你必须声明带有`ngModelGroup`的 HTML 标签上的自定义验证器。

当你在带有`ngModelGroup`的元素上声明指令时，验证器指令将接收到`FormGroup`作为`AbstractControl`函数参数。然后，在自定义验证器内部，你可以访问表单组中各个表单控件的值，如下所示：

```js
const password = control.get('password').value;
const confirm = control.get('password-confirm').value;
```

在`get`方法内部的`string`值应该等于你在 HTML 模板中表单控件上声明的名称：

```js
<input required [(ngModel)]="formObj.password" null if the validation passes and a ValidationErrors object when the validation fails.
Async validations with custom validators
Lastly, you can create asynchronous validators. These asynchronous validators work similarly to regular custom validators; there are only two differences. First, the provider for the asynchronous validators is different. If you want to create an asynchronous validator, you must change the provider to the following values:

```

providers: [{

provide: NG_ASYNC_VALIDATORS,

useExisting: UsernameAvailabilityDirective,

multi: true,

}]

```js

 Secondly, you need to return `Promise` or `Observable` with either a `null` value or the `ValidationErrors` object. Here’s an example of such a function:

```

validate(control: AbstractControl): Promise<ValidationErrors | null> | Observable<ValidationErrors | null> {

const username = control.value;

return checkUsernameInDatabase(username).pipe(

map((isAvailable) => (isAvailable ? null : { usernameTaken: true }))

);

}

```js

 In this example, the `checkUsernameInDatabase` function is an API call that returns an observable, and we use RxJS’s pipe and map to map the result to `null` or the `ValidationErrors` object. You can use any asynchronous logic inside your validator, so long as you return `Promise` or `Observable` with `null` or the `ValidationErrors` object.
In this section, you learned how to create template-driven forms. You also learned how to create form controls using the `ngModel` directive, how to create form groups using `ngModelGroup`, and how to use built-in validators. Then, you learned about how to style a form and display error messages based on form control statuses. Lastly, you learned how to create custom validators for form controls, form groups, and asynchronous validation rules. Next, we will start learning how to build reactive forms.
Building reactive forms
In this section, you will learn how to build reactive forms. We will rebuild the form we used in the previous section but reactively. You will learn how to create `FormGroup`, `FormArray`, and `FormControl` instances, how to validate reactive forms, and how to create custom validators for reactive forms. You will also learn how to dynamically create form fields and how to change the update behavior of reactive form fields.
Creating a reactive form
Start by removing or commenting out the HTML template and TypeScript code for our template-driven form. I will comment out the code so that it remains an example of the template-driven approach.
Next, we’ll start with the same simple HTML form, including the description, amount excluding VAT, VAT percentage, and date fields. We will gradually transform the simple HTML form into a reactive form:

```

<form>

<div class=»form-field">

<label for=»description»>描述：</label>

<input type=»text» id=»description»>

</div>

…………

<button type=»submit»>Submit</button>

</form>

```js

 After creating the simple HTML form, we will start by importing `ReactiveFormsModule` inside our component. When building reactive forms, you import `ReactiveFormsModule` instead of the regular `FormsModule`. After adding `ReactiveFormsModule` to your component file, we can move on and start to create the form model using the `FormGroup` and `FormControl` classes.
At its core, an Angular form is a `FormGroup` class with `FormControl` elements inside `FormGroup`. When we created our template-driven form, Angular created a `FormGroup` class for our `ngForm` instance and added a `FormControl` element inside the `FormGroup` class for each `ngModel` directive we declared.
To construct our reactive form model, we need to do the same only manually:

```

addExpenseForm = new FormGroup({

description: new FormControl(''),

amountExclVat: new FormControl(null),

vatPercentage: new FormControl(null),

date: new FormControl(''),

});

```js

 As you can see, we’ve created a `FormGroup` instance and added a `FormControl` element inside for each form field. Inside the function brackets of the form control, we’ve added either an empty string or `null`; these are the default values for the form control instances.
If you want different default values, you can change the values inside the function brackets of the `FormControl` instances. Later in this section, we will create an input by which you can send default values from the parent, just like we did with the template-driven form.
After creating the form model, you need to bind the form model to the form inside your HTML template. You can bind the form model to the template by using the `FormGroup` directive and assigning the directive with the form model, as follows:

```

<form FormControl 实例到表单字段，使用 formControlName 指令：

```js
<input formControlName directive with the key that was used to assign FormControl inside the FormGroup class you declared inside your TypeScript file. After adding the formControlName directive to all your form fields, the form model is bound to the HTML form.
Now, when you change the values inside the input fields, `addExpenseForm` in your component class will be updated, and when you update the `FormControl` values inside your TypeScript file, the changes you made will be reflected inside the input fields in the browser. You can test this by adding a default value to one of the `FormControl` values inside your TypeScript file; when you do, the value should be reflected inside the HTML template.
To test if changing the input also changes the values of `addExpenseForm` in your component class, you can temporarily add the following code to your template so that you can see the changes to the `addExpenseForm` object in real time:

```

<div>{{addExpenseForm.value | json}}</div>

```js

 After creating the reactive form, you can remove the aforementioned line of code. Until then, it can be helpful to see if all values are synchronized.
Now that we’ve defined and tested our form fields, let’s learn how we can group form fields into reactive forms.
Grouping fields in reactive forms
In our template-driven form, we grouped the amount excluding VAT and VAT percentage fields using the `ngModelGroup` directive. By grouping fields, you can perform validation logic on the group instead of the individual fields, style the group, or just change the data structure to something better resembling your state or DTO objects.
Let’s also group the amount excluding VAT and the VAT percentage fields in our reactive form. Start by changing the HTML template and wrap the two input fields inside a fieldset HTML template:

```

<fieldset> ……… </fieldset>

```js

 Next, change the form model so that it reflects the structure where the two fields are grouped inside a `FormGroup` class:

```

addExpenseForm = new FormGroup({

description: new FormControl('Test'),

amount: new FormGroup({

amountExclVat: new FormControl(null),

vatPercentage: new FormControl(null),

}),

date: new FormControl(''),

});

```js

 When you’ve updated the form model, you need to bind the form group inside the HTML form. You can bind the form group using the `formGroupName` directive and assign it with the key of your `FormGroup` class inside the form model. In our case, this is `amount`:

```

<fieldset formGroupName="amount"> ……… </fieldset>

```js

 That is all you need to do. Now, when you log the form value, its structure will look like this:

```

{

"description": "",

"amount": {

"amountExclVat": null,

"vatPercentage": null },

"date": ""

}

```js

 Now that you know how to group fields using the `FormGroup` class, let’s explore how we can dynamically add fields and `FormControl` instances to our form and form model.
Dynamically adding fields and FormControl instances
Unlike template-driven forms, when creating reactive forms, you can also group fields using the `FormArray` class. The `FormArray` class is useful when you don’t know how many values will be supplied, and the user can dynamically add and remove input fields in the form. An example of this might be tags or comments.
To demonstrate this, we will add another field to our reactive form so that we can add tags. Start by adding the `FormArray` class to your form model:

```

tags: new FormArray([ new FormControl('')])

```js

 As you can see, `FormArray` takes an array instead of an object as a parameter. Inside this array, we declared a form control, which will be the first tag inside our form. Because `FormArray` takes an array, we can dynamically add (or remove) from controls, which, in turn, will add or remove input fields to/from our HTML form.
Now that we’ve added the `FormArray` class inside our form model, let’s add some HTML so that the user can add multiple tags.
Start by creating a `fieldset` value and declare the `formArrayName` directive on the HTML tag:

```

<fieldset formArrayName="tags"> </fieldset>

```js

 Inside `fieldset`, add the following HTML:

```

<div class="form-field" *ngFor="let item of addExpenseForm.controls.tags.controls; index as i">

<label for=»tag-{{i}}»>标签:</label>

<div class=»tag-field»>

<input [formControlName]="i" type="text" id="tag-{{i}}">

</div>

</div>

```js

 The aforementioned HTML will output a label and input for each `FormControl` inside `FormArray`. Now, we only need a way to add and remove `FormControl` instances.
Let’s add two buttons to the HTML underneath the input tag:

```

<div>

<button *ngIf="i > 0" (click)="removeTag(i)">-</button>

<button (click)=»addTag()">+</button>

</div>

```js

 Next, we need to add the logic for the click functions inside the component class:

```

addTag() {

this.addExpenseForm.controls.tags.insert(0, new FormControl(''));

}

removeTag(index: number) {

this.addExpenseForm.controls.tags.removeAt(index);

}

```js

 With the aforementioned functions added to the component class, you can now add and remove `FormControl` instances inside the `FormArray` class, which will result in added or removed input fields.
But what if you have data in an object format instead of an array and you need to keep the form format, and just like our previous example, you don’t know how many or what fields you will have and what the keys for these fields will be?
An example could be when you receive a list of statuses that our expense has gone through, such as submitted, waiting for revision, checked, approved, and so on. For these scenarios, you can use the `FormRecord` class. We won’t add this to our form, but I will outline an example of how to handle a `FormRecord` class:

```

statuses: new FormRecord({})

```js

 The `FormRecord` class receives an object with keys and `FormControl` or `FormGroup` instances. You can add the `FormControl` instances to the `FormRecord` class as follows:

```

this.form.controls.statuses.addControl('someKey', new FormControl(''));

```js

 As you can see, we utilize the `addControl` method, which is exposed on the `FormRecord` class. The `addControl` method is also available on `FormGroup`, but you should use `FormRecord` when you don’t know what fields will be added beforehand. You can use the `FormGroup` when you do know what the entire model will look like. You can strongly type `FormGroup` and `FormRecord`. To strongly type `FormGroup`, use the following syntax:

```

address: new FormGroup<IAddress>({…………})

```js

 In this example, we added an interface for `FormGroup` between the arrow brackets. By adding the type, you strongly typed `FormGroup` and can’t add fields not defined in the `IAddress` interface. When strongly typing a `FormRecord` class, we tell the record what value our dynamic controls will have. For example, if we have a list of keys with a Boolean value, we can use this syntax:

```

状态：new FormRecord<FormControl<boolean>>({…………})

```js

 Here, we tell `FormRecord` that each field that will be added should be a `FormContro` instance and that `FormControl` should have a Boolean value. If we have form controls with different values, we can change the Boolean value for string or any other type our controls will hold.
Now that you know how to group fields using `FormGroup` or dynamically add fields using `FormArray`, `FormGroup`, or `FormRecord`, let’s explore how we can control the update behavior of our fields and how to declare standalone controls in a reactive form.
Configuring update behavior and declaring standalone controls
With template-driven forms, we have the option to configure the update behavior of form control instances using the `ngModelOptions` directive; in reactive forms, we control the update behavior inside the `FormGroup`, `FormArray`, or `FormControl` class. You can add a configuration object to set the `updateOn` property. You can set the `updateOn` property for `FormGroup`, `FormArray`, and `FormControl` elements alike.
When you set the `updateOn` property for a `FormGroup` element of `FormArray`, it will be applied to all nested `FormControl` elements. If you define the `updateOn` property for a nested object inside the form model, that nested property will overrule the `updateOn` property of parent elements. Just like template-driven forms, you can set the update behavior to `change` (which is the default), `blur`, or `submit`:

```

description: new FormControl('', ngModelOptions 指令在我们的模板驱动表单中，你也有将输入字段标记为独立的选择。与模板驱动表单一样，你也可以有一个独立的响应式表单元素，但你不需要使用独立属性来设置它；相反，你只需声明一个没有 FormGroup 的 FormControl 实例：

```js
searchInput = new FormControl('');
```

对于一个独立的响应式表单字段，你可以在你的 HTML 模板中使用 `formControl` 指令而不是 `formControlName` 指令：

```js
<input [formControl]="searchInput" type="text">
```

你可以通过使用 `FormControl` 的 value 属性来访问你独立表单字段的值：

```js
this.searchInput.value;
```

除了使用 value 属性，你也可以更反应性地访问值并响应 `FormControl` 的每个更新。你可以通过订阅 `valueChanges` 可观察对象来反应性地处理更改：

```js
this.searchInput.valueChanges.subscribe(() => { ……… });
```

现在你已经知道了如何控制表单字段的更新行为以及如何反应性地创建独立的表单字段，让我们学习如何通过编程方式设置和更新表单值。

设置和更新值

通常，你需要在组件类内部以编程方式设置和更新值。在响应式表单中，你可以使用 `setValue` 来设置单个表单控件上的值，当你想要同时更新表单的多个字段时，可以使用 `patchValue` 方法。

在本节中，我们将创建我们的组件 `@Input()` 指令并使用 `patchValue` 来更新从父组件接收到的默认表单值。

首先，在 `add-expense.interface.ts` 文件中添加一个新的接口：

```js
export interface AddExpenseReactive {
  description?: string;
  amount?: {
    amountExclVat?: number;
    vatPercentage?: number;
  };
  date?: string[];
  tags?: string[];
}
```

接下来，我们将添加 `@Input()` 指令及其设置器。在这个设置器内部，我们将使用 `patchValue` 方法：

```js
@Input()
public set expenseToAdd(value: AddExpenseReactive) {
  this.addExpenseForm.patchValue(value);
}
```

`patchValue`方法将更新值对象内部提供的所有值。因此，如果值只包含描述键，则只更新描述；当值对象包含描述和金额，并且具有这两个属性时，所有这些值都将更新。唯一的例外是`date`和`tags`字段。

如您可能已注意到，当我们定义界面中的日期时，我们将`date`属性定义为字符串数组类型；这是因为为了设置默认值，我们需要向表单控件提供字符串数组，如下所示：

```js
['2023-10-15']
```

如果您从父组件提供类似格式的值，`patchValue`也会对日期有效；当您以简单字符串的形式提供它时，输入将不会被填充。

确保您还更新了`FormControl`内部的默认值；否则，您将因为控制内部和补丁值中的类型不匹配而得到编译器错误：

```js
date: new FormControl(['']),
```

除了`date`字段外，`tags`字段也有所不同，因为我们使用它来动态地向我们的表单添加控件。当我们将`addExpenseForm`与表单模型关联时，我们的`FormArray`标签将接收一个默认值，即一个`FormControl`。因为我们只在`FormArray`内部添加了一个`FormControl`，所以当我们对表单使用`patchValue`方法时，只有一个标签将被设置，即使提供了更多标签。要更新`FormArray`的值，我们需要在我们的 setter 内部添加一些额外的逻辑：

```js
this.addExpenseForm.controls.tags.clear();
value.tags?.forEach(tag => {
  this.addExpenseForm.controls.tags.push(new FormControl(tag)); });
```

首先，我们在`FormArray`标签上使用了`clear`方法。`clear`方法将清除`FormArray`内部声明的所有`FormControl`实例。在我们清除`FormArray`之后，我们将使用`forEach`循环为从父组件接收到的每个标签添加一个新的`FormControl`。

现在，当我们从父组件提供包含值的对象时，我们的表单将填充这些值。

在某些场景中，您可能只想设置单个控件的值。当您只想设置单个控件的值时，您必须使用`FormControl`实例上的`setValue`方法：

```js
this.addExpenseForm.controls.description.setValue('New description');
```

`setValue`方法不允许您将数字值分配给描述输入字段，这使得它成为一个类型安全和程序化的方式来设置表单控件的值。我们添加了输入以接收来自父组件的值。

通过这样，您已经学会了如何以编程方式为您的响应式表单设置和更新值。接下来，我们将开始学习响应式表单中的验证。

验证响应式表单

与模板驱动表单一样，您可以使用内置或自定义验证器来验证响应式表单。我们将首先查看内置验证器，然后创建一个自定义验证器。响应式表单具有与模板驱动表单相同的内置验证器，但我们不是使用指令声明它们，而是使用`Validator`类。在您的`FormControl`实例内部，您可以添加一个包含您想要应用的验证器的数组。

要将必需的验证器添加到我们的描述字段，我们可以使用以下语法：

```js
description: new FormControl('', [ValidatorFn interface. We will create the same max word count validator, just like we did with the template-driven form, now only as a function instead of a directive.
To create the custom validator, start by creating a new folder, `reactive-validators`, inside the `form-validators` library. Inside this folder, create a new file named `max-word-count.function.ts`. We will use this new file to create our validator function:

```

export function maxWordCountValidator(maxWords: number): ValidatorFn {

return (control: AbstractControl): ValidationErrors | null => {

const wordCount = control?.value?.trim().split(' ').length;

return wordCount > maxWords ? { maxWordCount: { count: wordCount } } : null; }; }

```js

 Here, we create the `maxWordCountValidator` function, which will receive an input for the maximum word count we will allow. Inside this function, we return an implementation of the `ValidatorFn` interface. Here, `ValidatorFn` is the same as the `validate` method we declared inside the directive; we check the word count and return `null` if the word count is equal to or smaller than the allowed count and return a `ValidationErrors` object otherwise.
Next, you can add this custom validator to your form controls:

```

description: new FormControl('', [Validators.required, null values for different validators; you simply call the addValidators or removeValidators method:

```js
this.addExpenseForm.controls.description.addValidators(Validators.required);
this.addExpenseForm.controls.description.removeValidators(Validators.required);
```

通过这样，你已经学会了如何使用内置验证器，如何使用`ValidatorFn`实现创建验证器，以及如何动态添加和删除验证器。在下一节中，你将学习如何在响应式表单中提供视觉反馈。

在响应式表单中提供关于表单状态的视觉反馈

Angular 将控制状态 CSS 类应用于表单元素，就像它对模板驱动表单所做的那样。控制状态 CSS 类与用于模板驱动和响应式表单的相同，因此我们不需要对已经创建的样式进行任何更改，以便它们能够应用。`FormGroup`、`FormArray`、`FormRecord`和`FormControl`实例将根据它们当前的状态接收控制值 CSS 类。

唯一真正的区别是我们如何显示错误消息。我们在模板驱动表单中创建了一个模板变量，并将其绑定到`ngModel`实例以访问表单控件。当我们使用响应式表单时，我们通过我们创建的表单模型来访问表单控件实例：

```js
<span *ngIf="addExpenseForm.touched && addExpenseForm.controls.description.hasError('required')">This field is required</span>
```

对于其他方面，没有变化，所以请继续添加您想要显示给用户的错误消息。我们已经在介绍模板驱动表单时讨论了提供视觉反馈，因此当涉及到响应式表单时，这就是我们需要涵盖的全部内容。

提交和重置响应式表单的工作方式与模板驱动表单相同，因此你可以从*构建模板驱动表单*部分复制提交和重置行为。为了完成这一章，我们将学习如何根据配置对象动态构建表单。

动态创建表单

创建一个良好的表单需要相当多的代码。无论你是使用模板驱动还是响应式方法，你都需要大量的 HTML；你需要定义模型，添加验证器，以及额外的逻辑，例如提交行为。

你可以使用一个基类来提供一些共享功能，但你也可以构建一个动态表单，该表单将根据 JSON 输入动态构建表单。在本节中，我们将构建一个动态表单的简单示例。你可以扩展动态表单以满足你的特定需求。例如，你可能想要从外部源获取配置或支持额外的验证器。

要开始我们的动态表单，在你的`expenses-registration-forms`库中创建一个名为`dynamic-form`的新表单组件。接下来，创建一个`dynamic-control.interfaces.ts`文件。我将在组件文件夹内创建新的接口，但你也可以将所有接口放在指定的文件夹中，或者使用你喜欢的任何其他文件夹结构。我们新的动态控制接口将定义表单控件的接口，该接口将动态生成：

```js
export interface DynamicControl {
  controlKey: string;
  formFieldType?: 'input' | 'select';
  inputType?: string;
  label?: string;
  defaultValue?: any;
  selectOptions?: string[];
  updateOn: 'change' | 'blur' | 'submit';
  validators?: ValidatorFn[];
}
```

一旦我们定义了接口，我们需要向组件添加一个输入属性，该属性将接收一个`DynamicControl`对象的数组，并添加一个`formModel`属性，该属性将保存我们的表单模型：

```js
@Input() formModelConfig: DynamicControl[] = [];
formModel = new FormGroup({});
```

当我们接收到表单配置作为输入时，我们需要构建我们的表单模型。我们可以使用`ngOnChanges`生命周期钩子来构建表单模型，每次我们接收到新的表单配置时：

```js
ngOnChanges(changes: SimpleChanges) {
  if (changes[‹formModelConfig']) {
    this.formModel = new FormGroup({});
    this.formModelConfig.forEach((control) => {
      this.formModel.addControl(
        control.controlKey,
        new FormControl(control.defaultValue, { updateOn: control.updateOn, validators: control.validators }));
    }); }}
```

如你所见，我们检查`changes`对象是否包含新的`formModelConfig`；当`formModelConfig`包含在更改中时，我们使用`forEach`循环将`formModelConfig`的表单控件添加到我们的表单模型中。我们还需要一个提交函数和一个输出，它将在提交时将表单模型发送到父组件：

```js
@Output() outputForm = new EventEmitter();
onSubmit() {
  this.outputForm.emit(structuredClone(this.formModel.value));
  this.formModel.reset();
}
```

对于我们的组件类，这就是我们所需要的一切。我们需要将其翻译成模板，以便我们的表单将根据配置构建。首先，添加表单标签，将其绑定到表单模型，并将`ngSubmit`函数添加到`form`标签：

```js
<div class="form-container">
  <form [formGroup]="formModel" (ngSubmit)="onSubmit()">
  </form>
</div>
```

接下来，我们需要在`formModelConfig`内部为每个配置创建一个输入。我们将使用`*ngFor`循环为`formModelConfig`中的每个实例输出元素，并使用`*ngSwitch`指令来确定要创建哪个元素。我们将使用`DynamicControl`的属性将元素绑定到表单并提供所有正确的值：

```js
<div class="form-field" *ngFor="let control of formModelConfig">
  <label for=»description»>{{control.label}}</label>
  <ng-container [ngSwitch]=»control.formFieldType»>
    <input *ngSwitchCase=»›input›»
            formControlName="{{control.controlKey}}"
            type="{{control.inputType}}">
    <select *ngSwitchCase=»›select›»
            formControlName="{{control.controlKey}}">
      <option
         *ngFor="let option of control.selectOptions"
         value="{{option}}">
      </option>
    </select>
  </ng-container>
</div>
```

一旦添加了此 HTML，每个表单元素将被渲染并绑定到表单模型。我们最后需要做的是添加错误信息。以下是在你的动态表单中显示错误信息的一个示例：

```js
<span *ngIf="formModel.touched && formModel.get(control.controlKey)?.hasError('required')"> This field is required</span>
```

为你表单支持的每个错误信息添加一个额外的`span`。为了测试动态表单，你可以在`expenses-overview-page.component`中导入它，并在 HTML 模板中添加选择器：

```js
<bt-libs-ui-dynamic-form [formModelConfig]="formModelConfig" (outputForm)="addExpense($event)" />
```

在组件类中创建`formModelConfig`，以便动态表单有字段可以生成：

```js
formModelConfig: DynamicControl[] = [
  {
    controlKey: 'description', formFieldType: 'input',
    inputType: 'text', label: 'Description',
    defaultValue: '', updateOn: 'change',
    validators: [Validators.required]
  },
  {
    controlKey: 'amount', formFieldType: 'input',
    inputType: 'number', label: 'Amount excl. VAT',
    defaultValue: null, updateOn: 'change',
    validators: [Validators.required]
  }
]
```

将我们在响应式和模板驱动的表单中使用的其余字段添加到`formModelConfig`中；你会发现相同的表单将动态生成，包括验证规则和错误信息。

这只是一个动态表单的简单示例；如果你想添加额外的逻辑，例如允许表单组、表单数组和表单记录，你可以这样做。概念保持不变；只需调整模型，在组件类中添加逻辑以正确生成表单模型，并调整模板以便你可以按预期渲染它。

摘要

在本章中，你学习了 Angular 的不同类型表单。现在你知道了模板驱动和响应式表单之间的区别以及何时使用哪种类型。我们创建了一个包含验证、错误信息、默认值和基于控件状态的样式的模板驱动表单。我们还为模板驱动表单创建了一个自定义验证指令。接下来，我们为使用响应式表单做了同样的操作。

我们还创建了一个自定义验证函数，可以在响应式表单内部使用。我们学习了如何在响应式表单内部动态添加表单组、表单数组或表单记录类中的字段。然后，我们学习了如何在模板驱动和响应式表单中更改我们字段的更新行为。最后，你构建了一个动态表单，该表单根据配置构建表单模型，并相应地渲染表单，包括验证和错误信息。

在下一章中，你将学习如何创建动态组件，这些组件可以在许多场景中重复使用。

```js

```

```js

```

```js

```

```js

```

```js

```

```js

```

```js

```

# 第二部分：处理应用程序状态和编写更干净、更可扩展的代码

在这部分，你将学习如何为你的 Angular 应用程序开发更干净、更可扩展和性能更好的代码。你将从开发适合更复杂 UI 场景的动态组件开始。你将学习如何按需懒加载单个组件以减少你的包大小并提高性能。然后，你将探索常用的约定和设计模式，以开发更健壮和可扩展的 Angular 应用程序。你将通过实际操作实现外观模式、使用 NgRx 进行状态管理和使用 RxJs 和 signals 进行响应式编程来完成这部分内容。

本部分包括以下章节：

+   *第五章*，*创建动态 Angular 组件*

+   *第六章*，*在 Angular 中应用代码约定和设计模式*

+   *第七章*，*精通 Angular 中的响应式编程*

+   *第八章*，*优雅地处理应用程序状态*
