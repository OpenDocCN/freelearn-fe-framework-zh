# 第十五章：Angular 应用程序设计和技巧

在本章中，我们将完成 LemonMart 的实现。作为先路由的方法的一部分，我将展示如何创建可重用的可路由组件，同时支持数据绑定——使用辅助路由布置组件的能力，使用 resolve guards 减少样板代码，并利用类、接口、枚举、验证器和管道来最大程度地重用代码。此外，我们将创建多步骤表单，并实现带分页的数据表格，并探索响应式设计。在本书中，我们将触及 Angular 和 Angular Material 提供的大部分主要功能。

在这一章，训练车轮已经卸下。我将提供一般指导来帮助您开始实施；然而，您将需要自己尝试并完成实施。如果需要帮助，您可以参考本书附带的完整源代码，或在[Github.com/duluca/lemon-mart](https://github.com/duluca/lemon-mart)上查看最新的示例。

在本章中，您将学习以下主题：

+   面向对象类设计

+   可复用的可路由组件

+   缓存服务响应

+   HTTP POST 请求

+   多步骤响应表单

+   解析守卫

+   使用辅助路由进行主/细节视图

+   带分页的数据表格

# 用户类和面向对象编程

到目前为止，我们只是使用接口来表示数据，并且当在各个组件和服务之间传递数据时，我们仍然希望继续使用接口。然而，我们需要创建一个默认对象来初始化`BehaviorSubject`。在**面向对象编程**（**OOP**）中，让`User`对象拥有这个功能而不是一个服务，这样做非常有意义。所以，让我们实现一个`User`类来实现这个目标。

在`user/user`文件夹内，定义一个`IUser`接口和`UserModule`中提供的`User`类：

```ts
src/app/user/user/user.ts
import { Role } from '../../auth/role.enum'

export interface IUser {
  id: string
  email: string
  name: {
    first: string
    middle: string
    last: string
  }
  picture: string
  role: Role
  userStatus: boolean
  dateOfBirth: Date
  address: {
    line1: string
    line2: string
    city: string
    state: string
    zip: string
  }
  phones: IPhone[]
}

export interface IPhone {
  type: string
  number: string
  id: number
}

export class User implements IUser {
  constructor(
    public id = '',
    public email = '',
    public name = { first: '', middle: '', last: '' },
    public picture = '',
    public role = Role.None,
    public dateOfBirth = null,
    public userStatus = false,
    public address = {
      line1: '',
      line2: '',
      city: '',
      state: '',
      zip: '',
    },
    public phones = []
  ) {}

  static BuildUser(user: IUser) {
    return new User(
      user.id,
      user.email,
      user.name,
      user.picture,
      user.role,
      user.dateOfBirth,
      user.userStatus,
      user.address,
      user.phones
    )
  }
}
```

请注意，在构造函数中使用默认值定义所有属性为`public`属性，我们一举两得；否则，我们将需要分别定义属性并初始化它们。这样，我们就实现了一个简洁的实现。

您还可以实现计算属性以在模板中使用，比如可以方便地显示用户的`fullName`：

```ts
src/app/user/user/user.ts  
get fullName() {
  return `${this.name.first} ${this.name.middle} ${this.name.last}`
}
```

使用`static BuildUser`函数，您可以快速用从服务器接收的数据填充对象。您还可以实现`toJSON()`函数来自定义对象在发送数据到服务器之前的序列化行为。

# 重用组件

我们需要一个能够显示给定用户信息的组件。这些信息最自然的呈现位置是当用户导航到`/user/profile`时。您可以看到`User`概要文件的模拟：

![](img/eac254e3-0556-42ff-8841-6b44a5020fff.png)

用户概要模拟

用户信息也在应用程序的其他地方进行了模拟显示，在`/manager/users`：

![](img/b9b855d5-243b-4cc0-b625-511856a1f3b8.png)

用户管理模拟

为了最大限度地提高代码重用率，我们需要确保设计一个能在两种情境下使用的`User`组件。

例如，让我们完成两个与用户资料相关的屏幕的实现。

# 带有多步鉴权功能的响应式表单的用户资料

现在，让我们实现一个多步输入表单来捕获用户资料信息。我们还将使用媒体查询使这个多步表单对移动设备具有响应性。

1.  让我们首先添加一些辅助数据，这些数据将帮助我们显示具有选项的输入表单：

```ts
src/app/user/profile/data.ts
export interface IUSState {
  code: string
  name: string
}

export function USStateFilter(value: string): IUSState[] {
  return USStates.filter(state => {
    return (
      (state.code.length === 2 && state.code.toLowerCase() === value.toLowerCase()) ||
      state.name.toLowerCase().indexOf(value.toLowerCase()) === 0
    )
  })
}

export enum PhoneType {
  Mobile,
  Home,
  Work,
}

const USStates = [
  { code: 'AK', name: 'Alaska' },
  { code: 'AL', name: 'Alabama' },
  { code: 'AR', name: 'Arkansas' },
  { code: 'AS', name: 'American Samoa' },
  { code: 'AZ', name: 'Arizona' },
  { code: 'CA', name: 'California' },
  { code: 'CO', name: 'Colorado' },
  { code: 'CT', name: 'Connecticut' },
  { code: 'DC', name: 'District of Columbia' },
  { code: 'DE', name: 'Delaware' },
  { code: 'FL', name: 'Florida' },
  { code: 'GA', name: 'Georgia' },
  { code: 'GU', name: 'Guam' },
  { code: 'HI', name: 'Hawaii' },
  { code: 'IA', name: 'Iowa' },
  { code: 'ID', name: 'Idaho' },
  { code: 'IL', name: 'Illinois' },
  { code: 'IN', name: 'Indiana' },
  { code: 'KS', name: 'Kansas' },
  { code: 'KY', name: 'Kentucky' },
  { code: 'LA', name: 'Louisiana' },
  { code: 'MA', name: 'Massachusetts' },
  { code: 'MD', name: 'Maryland' },
  { code: 'ME', name: 'Maine' },
  { code: 'MI', name: 'Michigan' },
  { code: 'MN', name: 'Minnesota' },
  { code: 'MO', name: 'Missouri' },
  { code: 'MS', name: 'Mississippi' },
  { code: 'MT', name: 'Montana' },
  { code: 'NC', name: 'North Carolina' },
  { code: 'ND', name: 'North Dakota' },
  { code: 'NE', name: 'Nebraska' },
  { code: 'NH', name: 'New Hampshire' },
  { code: 'NJ', name: 'New Jersey' },
  { code: 'NM', name: 'New Mexico' },
  { code: 'NV', name: 'Nevada' },
  { code: 'NY', name: 'New York' },
  { code: 'OH', name: 'Ohio' },
  { code: 'OK', name: 'Oklahoma' },
  { code: 'OR', name: 'Oregon' },
  { code: 'PA', name: 'Pennsylvania' },
  { code: 'PR', name: 'Puerto Rico' },
  { code: 'RI', name: 'Rhode Island' },
  { code: 'SC', name: 'South Carolina' },
  { code: 'SD', name: 'South Dakota' },
  { code: 'TN', name: 'Tennessee' },
  { code: 'TX', name: 'Texas' },
  { code: 'UT', name: 'Utah' },
  { code: 'VA', name: 'Virginia' },
  { code: 'VI', name: 'Virgin Islands' },
  { code: 'VT', name: 'Vermont' },
  { code: 'WA', name: 'Washington' },
  { code: 'WI', name: 'Wisconsin' },
  { code: 'WV', name: 'West Virginia' },
  { code: 'WY', name: 'Wyoming' },
]

```

1.  安装一个帮助库来以编程方式访问 TypeScript 枚举值

```ts
$ npm i ts-enum-util
```

1.  在`common/validations.ts`中添加新的验证规则

```ts
src/app/common/validations.ts
...

export const OptionalTextValidation = [Validators.minLength(2), Validators.maxLength(50)]
export const RequiredTextValidation = OptionalTextValidation.concat([Validators.required])
export const OneCharValidation = [Validators.minLength(1), Validators.maxLength(1)]
export const BirthDateValidation = [
  Validators.required,
  Validators.min(new Date().getFullYear() - 100),
  Validators.max(new Date().getFullYear()),
]
export const USAZipCodeValidation = [
  Validators.required,
  Validators.pattern(/^\d{5}(?:[-\s]\d{4})?$/),
]
export const USAPhoneNumberValidation = [
  Validators.required,
  Validators.pattern(/^\D?(\d{3})\D?\D?(\d{3})\D?(\d{4})$/),
]
```

1.  现在按照以下方式实现`profile.component.ts`：

```ts
src/app/user/profile/profile.component.ts
import { Role as UserRole } from '../../auth/role.enum'
import { $enum } from 'ts-enum-util'
...
@Component({
  selector: 'app-profile',
  templateUrl: './profile.component.html',
  styleUrls: ['./profile.component.css'],
})
export class ProfileComponent implements OnInit {
  Role = UserRole
  PhoneTypes = $enum(PhoneType).getKeys()
  userForm: FormGroup
  states: Observable<IUSState[]>
  userError = ''
  currentUserRole = this.Role.None

  constructor(
    private formBuilder: FormBuilder,
    private router: Router,
    private userService: UserService,
    private authService: AuthService
  ) {}

  ngOnInit() {
    this.authService.authStatus.subscribe(
      authStatus => (this.currentUserRole = authStatus.userRole)
    )

    this.userService.getCurrentUser().subscribe(user => {
      this.buildUserForm(user)
    })

    this.buildUserForm()
  }
  ...
}
```

加载时，我们从`userService`请求当前用户，但这可能需要一段时间，因此我们必须首先用`this.buildUserForm()`构建一个空表单。在这个函数中，您还可以实现一个 resolve 守卫，如后面将要讨论的，根据路由提供的`userId`加载用户，并将数据传递到`buildUserForm(routeUser)`，然后跳过加载`currentUser`以增加此组件的可重用性。

# 表单组

我们的表单有许多输入字段，因此我们将使用`FormGroup`，由`this.formBuilder.group`创建以容纳我们的各种`FormControl`对象。此外，子`FormGroup`对象将允许我们保持数据结构的正确形状。

开始构建`buildUserForm`函数，如下所示：

```ts
src/app/user/profile/profile.component.ts
...
  buildUserForm(user?: IUser) {
    this.userForm = this.formBuilder.group({
      email: [
        {
          value: (user && user.email) || '',
          disabled: this.currentUserRole !== this.Role.Manager,
        },
        EmailValidation,
      ],
      name: this.formBuilder.group({
        first: [(user && user.name.first) || '', RequiredTextValidation],
        middle: [(user && user.name.middle) || '', OneCharValidation],
        last: [(user && user.name.last) || '', RequiredTextValidation],
      }),
      role: [
        {
          value: (user && user.role) || '',
          disabled: this.currentUserRole !== this.Role.Manager,
        },
        [Validators.required],
      ],
      dateOfBirth: [(user && user.dateOfBirth) || '', BirthDateValidation],
      address: this.formBuilder.group({
        line1: [
          (user && user.address && user.address.line1) || '',
          RequiredTextValidation,
        ],
        line2: [
          (user && user.address && user.address.line2) || '',
          OptionalTextValidation,
        ],
        city: [(user && user.address && user.address.city) || '', RequiredTextValidation],
        state: [
          (user && user.address && user.address.state) || '',
          RequiredTextValidation,
        ],
        zip: [(user && user.address && user.address.zip) || '', USAZipCodeValidation],
      }),
      ...
    })
    ...
  }
...
```

`buildUserForm`可选择接受一个`IUser`以预填表单，否则所有字段都设置为默认值。`userForm`本身是顶层`FormGroup`。其中添加了各种`FormControls`，例如`email`，根据需要连接到它们的验证器。注意`name`和`address`是它们自己的`FormGroup`对象。这种父子关系确保表单数据的正确结构，在序列化为 JSON 时，这适配了`IUser`的结构，以保证我们应用程序和服务端代码的运用。

您将独立完成`userForm`的实现，按照章节提供的示例代码，并且在接下来的几个章节中我将逐步解释代码的某些关键功能。

# 分步表单和响应式布局

Angular Material Stepper 附带了`MatStepperModule`。该步骤条允许将表单输入分解为多个步骤，以便用户不会被一次性处理数十个输入字段而感到不知所措。用户仍然可以跟踪他们在过程中的位置，作为开发人员的副作用，我们将我们的`<form>`实现分解并逐步强制执行验证规则，或者创建可以跳过或必填的可选工作流程。与所有 Material 用户控件一样，步骤条已经设计成具有响应式 UX。在接下来的几节中，我们将实现包括不同表单输入技术的三个步骤：

1.  账户信息

    +   输入验证

    +   使用媒体查询进行响应式布局

    +   计算属性

    +   日期选择器

1.  联系信息

    +   自动完成支持

    +   动态表单数组

1.  评论

    +   只读视图

    +   数据保存和清除

让我们为用户模块准备一些新的 Material 模块：

1.  创建一个`user-material.module`，其中包含以下 Material 模块：

```ts
MatAutocompleteModule,
MatDatepickerModule,
MatDividerModule,
MatLineModule,
MatNativeDateModule,
MatRadioModule,
MatSelectModule,
MatStepperModule,
```

1.  确保`user.module`正确导入：

    1.  新的`user-material.module`

    1.  基线`app-material.module`

    1.  必须引入`FormsModule`，`ReactiveFormsModule`和`FlexLayoutModule`

当我们开始添加子 Material 模块时，将根`material.module.ts`文件重命名为`app-material.modules.ts`是合理的，与`app-routing.module.ts`的命名方式一致。今后，我将使用后一种约定。

1.  现在，开始实现“账户信息”步骤的第一行：

```ts
src/app/user/profile/profile.component.html <mat-toolbar color="accent"> <h5>User Profile</h5>
</mat-toolbar>

<mat-horizontal-stepper #stepper="matHorizontalStepper">
  <mat-step [stepControl]="userForm">
    <form [formGroup]="userForm">
      <ng-template matStepLabel>Account Information</ng-template>
      <div class="stepContent">
        <div fxLayout="row" fxLayout.lt-sm="column" [formGroup]="userForm.get('name')" fxLayoutGap="10px">
          <mat-form-field fxFlex="40%">
            <input matInput placeholder="First Name" aria-label="First Name" formControlName="first">
            <mat-error *ngIf="userForm.get('name').get('first').hasError('required')">
              First Name is required
            </mat-error>
            <mat-error *ngIf="userForm.get('name').get('first').hasError('minLength')">
              Must be at least 2 characters
            </mat-error>
            <mat-error *ngIf="userForm.get('name').get('first').hasError('maxLength')">
              Can't exceed 50 characters
            </mat-error>
          </mat-form-field>
          <mat-form-field fxFlex="20%">
            <input matInput placeholder="MI" aria-label="Middle Initial" formControlName="middle">
            <mat-error *ngIf="userForm.get('name').get('middle').invalid">
              Only inital
            </mat-error>
          </mat-form-field>
          <mat-form-field fxFlex="40%">
            <input matInput placeholder="Last Name" aria-label="Last Name" formControlName="last">
            <mat-error *ngIf="userForm.get('name').get('last').hasError('required')">
              Last Name is required
            </mat-error>
            <mat-error *ngIf="userForm.get('name').get('last').hasError('minLength')">
              Must be at least 2 characters
            </mat-error>
            <mat-error *ngIf="userForm.get('name').get('last').hasError('maxLength')">
              Can't exceed 50 characters
            </mat-error>
          </mat-form-field>
        </div>
       ...
      </div>
    </form>
   </mat-step>
...
</mat-horizontal-stepper>
```

1.  请注意理解当前步骤条和表单配置的工作原理，你应该看到第一行渲染，并从模拟数据中拉取：

![](img/32843e4b-d12a-4a66-9b97-6c034e13732b.png)

多步表单 - 第 1 步

1.  为了完成表单的实现，请参考本章提供的示例代码或[GitHub.com/duluca/lemon-mart](https://github.com/duluca/lemon-mart)上的参考实现。

在你的实现过程中，你会注意到“评论”步骤使用名为`<app-view-user>`的指令。这个组件的最简版本在下面的 ViewUser 组件部分实现了。然而，现在可以自由地在页面内实现这个功能，并在“可绑定和路由数据”部分重构代码。

在下面的截图中，你可以看到在桌面端完成的多步表单的实现效果：

![](img/e9a485ac-1bb5-4881-9c2f-bffcee671f75.png)

桌面端多步表单

注意，在使用`fxLayout.lt-sm="column"`替代`fxLayout="row"`的情况下，使一行具有响应式布局形式，如下所示：

![](img/d8d82436-fc54-4675-8091-cf033e2123d3.png)

移动端多步表单

让我们看看下一节中日期选择器字段是如何工作的。

# 计算属性和日期选择器

如果你想根据用户输入显示已计算的属性，可以按照这里所示的模式进行：

```ts
src/app/user/profile/profile.component.ts ...
get dateOfBirth() {
  return this.userForm.get('dateOfBirth').value || new Date()
}

get age() {
  return new Date().getFullYear() - this.dateOfBirth.getFullYear()
}
...
```

模板中的计算属性使用如下所示：

```ts
src/app/user/profile/profile.component ...
<mat-form-field fxFlex="50%">
  <input matInput placeholder="Date of Birth" aria-label="Date of Birth" formControlName="dateOfBirth" [matDatepicker]="dateOfBirthPicker">
  <mat-hint *ngIf="userForm.get('dateOfBirth').touched">{{this.age}} year(s) old</mat-hint>
  <mat-datepicker-toggle matSuffix [for]="dateOfBirthPicker"></mat-datepicker-toggle>
  <mat-datepicker #dateOfBirthPicker></mat-datepicker>
  <mat-error *ngIf="userForm.get('dateOfBirth').invalid">
    Date must be with the last 100 years
  </mat-error>
</mat-form-field>
...
```

在下面的情况中，你可以看到它的实际效果：

![](img/8aff43df-f604-43cf-9eed-4fb412b9163d.png)

使用 DatePicker 选择日期

选择日期后，将显示计算的年龄，如下所示：

![](img/65a05b6a-4266-4a78-816f-1749e2a0bab1.png)

计算年龄属性

现在，让我们继续下一步，联系信息，并看看我们如何实现方便的方式来显示和输入地址字段的州部分。

# Type ahead 支持

在`buildUserForm`中，我们设置了对`address.state`的监听器，以支持类型前输入下拉筛选体验：

```ts
src/app/user/profile/profile.component.ts ...
this.states = this.userForm
  .get('address')
  .get('state')
  .valueChanges.pipe(startWith(''), map(value => USStateFilter(value)))
...
```

在模板上，使用`mat-autocomplete`绑定到过滤后的州数组，并使用`async`管道：

```ts
src/app/user/profile/profile.component.html ...
<mat-form-field fxFlex="30%">
  <input type="text" placeholder="State" aria-label="State" matInput formControlName="state" [matAutocomplete]="stateAuto">
  <mat-autocomplete #stateAuto="matAutocomplete">
    <mat-option *ngFor="let state of states | async" [value]="state.name">
      {{ state.name }}
    </mat-option>
  </mat-autocomplete>
  <mat-error *ngIf="userForm.get('address').get('state').hasError('required')">
    State is required
  </mat-error>
</mat-form-field>
...
```

当用户输入`V`字符时，它是这样的样子：

![](img/efe44748-d14c-4d5e-b990-004fd345bd1f.png)

下拉框与 Typeahead 支持

在下一节中，让我们启用多个电话号码的输入。

# 动态表单数组

请注意`phones`是一个数组，可能允许多个输入。我们可以通过使用`this.formBuilder.array`构建`FormArray`及使用几个辅助函数来实现这一点：

```ts
src/app/user/profile/profile.component.ts
...
  phones: this.formBuilder.array(this.buildPhoneArray(user ? user.phones : [])),
...
  private buildPhoneArray(phones: IPhone[]) {
    const groups = []

    if (!phones || (phones && phones.length === 0)) {
      groups.push(this.buildPhoneFormControl(1))
    } else {
      phones.forEach(p => {
        groups.push(this.buildPhoneFormControl(p.id, p.type, p.number))
      })
    }
    return groups
  }

  private buildPhoneFormControl(id, type?: string, number?: string) {
    return this.formBuilder.group({
      id: [id],
      type: [type || '', Validators.required],
      number: [number || '', USAPhoneNumberValidation],
    })
  }
...
```

`BuildPhoneArray`支持使用单个电话输入初始化表单或使用现有数据填充表单，与`BuildPhoneFormControl`协同工作。当用户单击 Add 按钮创建新的条目行时，后一个函数非常有用：

```ts
src/app/user/profile/profile.component.ts
...  
  addPhone() {
    this.phonesArray.push(
      this.buildPhoneFormControl(this.userForm.get('phones').value.length + 1)
    )
  }

  get phonesArray(): FormArray {
    return <FormArray>this.userForm.get('phones')
  }
...
```

`phonesArray`属性 getter 是一个常见的模式，可以更轻松地访问某些表单属性。然而，在这种情况下，这也是必要的，因为我们必须将`get('phones')`转换为`FormArray`，以便我们可以在模板上访问它的`length`属性：

```ts
src/app/user/profile/profile.component.html
...
<mat-list formArrayName="phones">
  <h2 mat-subheader>Phone Number(s)</h2>
  <button mat-button (click)="this.addPhone()">
    <mat-icon>add</mat-icon>
    Add Phone
  </button>
  <mat-list-item *ngFor="let position of this.phonesArray.controls let i=index" [formGroupName]="i">
  <mat-form-field fxFlex="100px">
    <mat-select placeholder="Type" formControlName="type">
      <mat-option *ngFor="let type of this.PhoneTypes" [value]="type">
      {{ type }}
      </mat-option>
    </mat-select>
  </mat-form-field>
  <mat-form-field fxFlex fxFlexOffset="10px">
    <input matInput type="text" placeholder="Number" formControlName="number">
    <mat-error *ngIf="this.phonesArray.controls[i].invalid">
      A valid phone number is required
    </mat-error>
  </mat-form-field>
  <button fxFlex="33px" mat-icon-button (click)="this.phonesArray.removeAt(i)">
    <mat-icon>close</mat-icon>
  </button>
  </mat-list-item>
</mat-list>
...
```

`remove`函数是内联实现的。

我们来看看它应该如何工作：

![](img/3fb49d34-6c03-4edb-86a6-43d3183d8068.png)

使用 FormArray 进行多个输入

现在我们已经完成了输入数据，我们可以继续进行步进器的最后一步：Review。然而，正如之前提到的，Review 步骤使用`app-view-user`指令来显示其数据。让我们先构建该视图。

# ViewUser 组件

这是`<app-view-user>`指令的最小实现，这是 Review 步骤的先决条件。

在`user`下创建一个新的`viewUser`组件，如下所示：

```ts
src/app/user/view-user/view-user.component.ts
import { Component, OnInit, Input } from '@angular/core'
import { IUser, User } from '../user/user'

@Component({
  selector: 'app-view-user',
  template: `
    <mat-card>
      <mat-card-header>
        <div mat-card-avatar><mat-icon>account_circle</mat-icon></div>
        <mat-card-title>{{currentUser.fullName}}</mat-card-title>
        <mat-card-subtitle>{{currentUser.role}}</mat-card-subtitle>
      </mat-card-header>
      <mat-card-content>
        <p><span class="mat-input bold">E-mail</span></p>
        <p>{{currentUser.email}}</p>
        <p><span class="mat-input bold">Date of Birth</span></p>
        <p>{{currentUser.dateOfBirth | date:'mediumDate'}}</p>
      </mat-card-content>
      <mat-card-actions *ngIf="!this.user">
        <button mat-button mat-raised-button>Edit</button>
      </mat-card-actions>
    </mat-card>
  `,
  styles: [
    `
    .bold {
      font-weight: bold
    }
  `,
  ],
})
export class ViewUserComponent implements OnChanges {
  @Input() user: IUser
  currentUser = new User()

  constructor() {}

  ngOnChanges() {
    if (this.user) {
      this.currentUser = User.BuildUser(this.user)
    }
  }
}
```

上面的组件使用`@Input`进行输入绑定，从外部组件获取符合`IUser`接口的用户数据。我们实现`ngOnChanges`事件，每当绑定的数据发生变化时触发。在此事件中，我们使用`User.BuildUser`将存储在`this.user`中的简单 JSON 对象填充为`User`类的实例，并将其分配给`this.currentUser`。模板使用此变量，因为像`currentUser.fullName`这样的计算属性只有在数据驻留在`User`类的实例中时才会起作用。

现在，我们准备完成多步表单。

# 检查组件并保存表单

在多步表单的最后一步，用户应该能够进行审查，然后保存表单数据。作为良好的做法，成功的`POST`请求将返回保存的数据到浏览器。然后我们可以使用从服务器收到的信息重新加载表单：

```ts
src/app/user/profile/profile.component 
...
async save(form: FormGroup) {
  this.userService
    .updateUser(form.value)
    .subscribe(res => this.buildUserForm(res), err => (this.userError = err))
 }
...
```

如果有错误，它们将被设置为`userError`来显示。在保存之前，我们将以紧凑的形式呈现数据，使用可重用组件将表单数据绑定到：

```ts
src/app/user/profile/profile.component.html
...
<mat-step [stepControl]="userForm">
  <form [formGroup]="userForm" (ngSubmit)="save(userForm)">
  <ng-template matStepLabel>Review</ng-template>
  <div class="stepContent">
    Review and update your user profile.
    <app-view-user [user]="this.userForm.value"></app-view-user>
  </div>
  <div fxLayout="row" class="margin-top">
    <button mat-button matStepperPrevious color="accent">Back</button>
    <div class="flex-spacer"></div>
    <div *ngIf="userError" class="mat-caption error">{{userError}}</div>
    <button mat-button color="warn" (click)="stepper.reset()">Reset</button>
    <button mat-raised-button matStepperNext color="primary" type="submit" [disabled]="this.userForm.invalid">Update</button>
  </div>
  </form>
</mat-step>
...
```

最终产品应该是这样的：

![](img/3f8afd93-786d-4ae7-b43b-d665971ae9a6.png)

审查步骤

注意重置表单的选项。添加一个警报对话框来确认重置用户输入数据将是良好的用户体验。

现在用户配置文件输入完成，我们正在逐渐地朝着最终目标迈进，即创建一个主/细节视图，其中经理可以点击用户并查看其个人资料详细信息。我们仍然需要添加更多的代码，并且在此过程中，我们已经陷入了一种向组件加载必要数据的样板代码模式。在下一部分中，我们将了解 resolve 守卫，以便我们可以简化我们的代码并减少样板内容。

# 解析守卫

解析守卫是路由守卫的一种类型，如 第十四章中所述，*设计身份验证和授权。* 解析守卫可以通过从路由参数中读取记录 ID 异步加载必要的数据，并在组件激活和初始化时准备好这些数据。

解析守卫的主要优势包括加载逻辑的可重用性，减少样板代码以及摆脱依赖关系，因为组件可以接收到其所需的数据而无需导入任何服务：

1.  在`user/user`下创建一个新的 `user.resolve.ts` 类：

```ts
src/app/user/user/user.resolve.ts
import { Injectable } from '@angular/core'
import { Resolve, ActivatedRouteSnapshot } from '@angular/router'
import { UserService } from './user.service'
import { IUser } from './user'

@Injectable()
export class UserResolve implements Resolve<IUser> {
  constructor(private userService: UserService) {}

  resolve(route: ActivatedRouteSnapshot) {
    return this.userService.getUser(route.paramMap.get('userId'))
  }
}

```

1.  您可以像这样使用 resolve 守卫：

```ts
example
{
  path: 'user',
  component: ViewUserComponent,
  resolve: {
    user: UserResolve,
  },
},
```

1.  `routerLink`将是这样的：

```ts
example
['user', {userId: row.id}]
```

1.  在目标组件的 ` ngOnInit` 挂钩中，您可以这样读取已解析的用户：

```ts
example
this.route.snapshot.data['user']
```

在我们更新`ViewUserComponent`和路由以利用 resolve 守卫后，您可以在接下来的两个部分中观察这种行为。

# 具有绑定和路由数据的可重用组件

现在，让我们重构`viewUser`组件，以便我们可以在多个上下文中重复使用它。一个是它可以使用 resolve 守卫加载自己的数据，适用于主/细节视图，另一个是可以将当前用户绑定到它上，在我们在前一节中构建的多步输入表单的审查步骤中已经完成了绑定：

1.  用以下更改更新`viewUser`组件：

```ts
src/app/user/view-user/view-user.component.ts
...
import { ActivatedRoute } from '@angular/router'

export class ViewUserComponent implements OnChanges, OnInit {
  ...
  constructor(private route: ActivatedRoute) {}

  ngOnInit() {
    if (this.route.snapshot && this.route.snapshot.data['user']) {
      this.currentUser = User.BuildUser(this.route.snapshot.data['user'])
      this.currentUser.dateOfBirth = Date.now() // for data mocking purposes only
    }
  }
  ...
```

现在我们有了两个独立的事件。一个用于`ngOnChanges`，它处理`this.user`已绑定的情况下`this.currentUser`被分配了哪个值。 `ngOnInit`只会在组件首次初始化或路由到达时触发一次。在这种情况下，如果路由的任何数据已被解析，那么它将被分配给`this.currentUser`。

要能够在多个延迟加载的模块中使用此组件，我们必须将其包装在自己的模块中。

1.  在`app`下创建一个新的`shared-components.module.ts`：

```ts
src/app/shared-components.module.ts
import { NgModule } from '@angular/core'
import { ViewUserComponent } from './user/view-user/view-user.component'
import { FormsModule, ReactiveFormsModule } from '@angular/forms'
import { FlexLayoutModule } from '@angular/flex-layout'
import { CommonModule } from '@angular/common'
import { MaterialModule } from './app-material.module'

@NgModule({
  imports: [
    CommonModule,
    FormsModule,
    ReactiveFormsModule,
    FlexLayoutModule,
    MaterialModule,
  ],
  declarations: [ViewUserComponent],
  exports: [ViewUserComponent],
})
export class SharedComponentsModule {}

```

1.  确保在你打算在`User`和`Manager`模块中使用`ViewUserComponent`时，将`SharedComponentsModule`模块引入到每个功能模块中。

1.  从`User`模块的声明中移除`ViewUserComponent`

我们现在已经具备开始实现主/细节视图的关键要素。

# 主/细节视图辅助路由

路由器优先架构的真正力量在于辅助路由的使用，通过仅通过路由器配置影响组件的布局，从而允许在不同布局中重新组合现有组件的丰富场景。辅助路由是彼此独立的路由，它们可以在标记中已定义的命名插座中呈现内容，例如`<router-outlet name="master">`或`<router-outlet name="detail">`。此外，辅助路由可以具有自己的参数、浏览器历史、子级和嵌套辅助路由。

在以下示例中，我们将使用辅助路由实现基本的主/细节视图：

1.  实现一个带有两个命名插座的简单组件：

```ts
src/app/manager/user-management/user-manager.component.ts
template: `
    <div class="horizontal-padding">
      <router-outlet name="master"></router-outlet>
      <div style="min-height: 10px"></div>
      <router-outlet name="detail"></router-outlet>
    </div>
  `
```

1.  在`manager`下创建一个`userTable`组件

1.  更新`manager-routing.module`以定义辅助路由：

```ts
src/app/manager/manager-routing.module.ts
  ...
      {
        path: 'users',
        component: UserManagementComponent,
        children: [
          { path: '', component: UserTableComponent, outlet: 
         'master' },
          {
            path: 'user',
            component: ViewUserComponent,
            outlet: 'detail',
            resolve: {
              user: UserResolve,
            },
          },
        ],
        canActivate: [AuthGuard],
        canActivateChild: [AuthGuard],
        data: {
          expectedRole: Role.Manager,
        },
      },
  ...
```

这意味着当用户导航到`/manager/users`时，他们将看到`UserTableComponent`，因为它是用`default`路径实现的。

1.  在`manager.module`中提供`UserResolve`，因为`viewUser`依赖于它

1.  在`userTable`中实现一个临时按钮

```ts
src/app/manager/user-table/user-table.component.html
<a mat-button mat-icon-button [routerLink]="['/manager/users', { outlets: { detail: ['user', {userId: 'fakeid'}] } }]" skipLocationChange>
  <mat-icon>visibility</mat-icon>
</a>
```

假设用户点击了上述定义的`View detail`按钮，那么`ViewUserComponent`将为具有给定`userId`的用户呈现。在下一张截图中，您可以看到在下一节中实现数据表后，`View Details`按钮将是什么样子：

![](img/27e24385-6a13-4375-8905-4e0a4da346c9.png)

查看详情按钮

您可以为主和详细信息定义多种组合和备用组件，从而允许无限可能的动态布局。然而，设置`routerLink`可能是一个令人沮丧的体验。根据确切的条件，您必须在链接中提供或不提供所有或一些插座。例如，在上述场景中，如果链接是`['/manager/users', { outlets: { master: [''], detail: ['user', {userId: row.id}] } }]`，则路由将悄无声息地加载失败。预计这些怪癖将在未来的 Angular 版本中得到解决。

现在，我们已经完成了对`ViewUserComponent`的解析守卫的实现，你可以使用 Chrome Dev Tools 查看数据是否被正确加载。在调试之前，请确保我们在第十三章，*持续集成和 API 设计*中创建的模拟服务器正在运行。

1.  确保模拟服务器正在运行，通过执行 `docker run -p 3000:3000 -t duluca/lemon-mart-swagger-server` 或者 `npm run mock:standalone`。

1.  在 Chrome Dev Tools 中，在`this.currentUser` 赋值后设置断点，如下所示：

![](img/6af6ebd9-acd6-4797-acf2-f02135a7c6a9.png)

Dev 工具调试 ViewUserComponent

你会注意到，在 `ngOnInit` 函数中正确设置了`this.currentUser`，展示了解析守卫的真正好处。`ViewUserComponent` 是详细视图；现在让我们实现带有分页的数据表作为主视图。

# 带有分页的数据表

我们已经创建了铺设主/详细视图的脚手架。在主出口中，我们将有一个用户的分页数据表，因此让我们实现 `UserTableComponent`，其中包含一个名为 `dataSource` 的 `MatTableDataSource` 属性。我们需要能够使用标准分页控件（如 `pageSize` 和 `pagesToSkip`）批量获取用户数据，并且能够通过用户提供的 `searchText` 进一步缩小选择范围。

让我们先为 `UserService` 添加必要的功能。

1.  实现一个新的接口 `IUsers` 来描述分页数据的数据结构

```ts
src/app/user/user/user.service.ts
...
export interface IUsers {
  items: IUser[]
  total: number
}
```

1.  向 `UserService` 添加 `getUsers`

```ts
src/app/user/user/user.service.ts
...
getUsers(pageSize: number, searchText = '', pagesToSkip = 0): Observable<IUsers> {
  return this.httpClient.get<IUsers>(`${environment.baseUrl}/v1/users`, {
    params: {
      search: searchText,
      offset: pagesToSkip.toString(),
      limit: pageSize.toString(),
    },
  })
}
...
```

1.  设置带有分页、排序和过滤的`UserTable`：

```ts
src/app/manager/user-table/user-table.component
import { AfterViewInit, Component, OnInit, ViewChild } from '@angular/core'
import { FormControl } from '@angular/forms'
import { MatPaginator, MatSort, MatTableDataSource } from '@angular/material'
import { merge, of } from 'rxjs'
import { catchError, debounceTime, map, startWith, switchMap } from 'rxjs/operators'
import { OptionalTextValidation } from '../../common/validations'
import { IUser } from '../../user/user/user'
import { UserService } from '../../user/user/user.service'

@Component({
  selector: 'app-user-table',
  templateUrl: './user-table.component.html',
  styleUrls: ['./user-table.component.css'],
})
export class UserTableComponent implements OnInit, AfterViewInit {
  displayedColumns = ['name', 'email', 'role', 'status', 'id']
  dataSource = new MatTableDataSource()
  resultsLength = 0
  _isLoadingResults = true
  _hasError = false
  errorText = ''
  _skipLoading = false

  search = new FormControl('', OptionalTextValidation)

  @ViewChild(MatPaginator) paginator: MatPaginator
  @ViewChild(MatSort) sort: MatSort

  constructor(private userService: UserService) {}

  ngOnInit() {}

  ngAfterViewInit() {
    this.dataSource.paginator = this.paginator
    this.dataSource.sort = this.sort

    this.sort.sortChange.subscribe(() => (this.paginator.pageIndex = 0))

    if (this._skipLoading) {
      return
    }

    merge(
      this.sort.sortChange,
      this.paginator.page,
      this.search.valueChanges.pipe(debounceTime(1000))
    )
      .pipe(
        startWith({}),
        switchMap(() => {
          this._isLoadingResults = true
          return this.userService.getUsers(
            this.paginator.pageSize,
            this.search.value,
            this.paginator.pageIndex
          )
        }),
        map((data: { total: number; items: IUser[] }) => {
          this._isLoadingResults = false
          this._hasError = false
          this.resultsLength = data.total

          return data.items
        }),
        catchError(err => {
          this._isLoadingResults = false
          this._hasError = true
          this.errorText = err
          return of([])
        })
      )
      .subscribe(data => (this.dataSource.data = data))
  }

  get isLoadingResults() {
    return this._isLoadingResults
  }

  get hasError() {
    return this._hasError
  }
}

```

初始化分页、排序和筛选属性后，我们使用 `merge` 方法来监听所有三个数据流的更改。如果有一个发生了变化，整个 `pipe` 就会被触发，其中包含对 `this.userService.getUsers` 的调用。然后将结果映射到表的 `datasource` 属性，否则捕获和处理错误。

1.  创建一个包含以下 Material 模块的 `manager-material.module`：

```ts
MatTableModule, 
MatSortModule, 
MatPaginatorModule, 
MatProgressSpinnerModule
```

1.  确保 `manager.module` 正确导入：

    1.  新的 `manager-material.module`

    1.  基线的 `app-material.module`

    1.  必需的 `FormsModule` 、 `ReactiveFormsModule` 和 `FlexLayoutModule`

1.  最后，实现 `userTable` 模板：

```ts
src/app/manager/user-table/user-table.component.html
<div class="filter-row">
  <form style="margin-bottom: 32px">
    <div fxLayout="row">
      <mat-form-field class="full-width">
        <mat-icon matPrefix>search</mat-icon>
        <input matInput placeholder="Search" aria-label="Search" [formControl]="search">
        <mat-hint>Search by e-mail or name</mat-hint>
        <mat-error *ngIf="search.invalid">
          Type more than one character to search
        </mat-error>
      </mat-form-field>
    </div>
  </form>
</div>
<div class="mat-elevation-z8">
  <div class="loading-shade" *ngIf="isLoadingResults">
    <mat-spinner *ngIf="isLoadingResults"></mat-spinner>
    <div class="error" *ngIf="hasError">
      {{errorText}}
    </div>
  </div>
  <mat-table [dataSource]="dataSource" matSort>
    <ng-container matColumnDef="name">
      <mat-header-cell *matHeaderCellDef mat-sort-header> Name </mat-header-cell>
      <mat-cell *matCellDef="let row"> {{row.name.first}} {{row.name.last}} </mat-cell>
    </ng-container>
    <ng-container matColumnDef="email">
      <mat-header-cell *matHeaderCellDef mat-sort-header> E-mail </mat-header-cell>
      <mat-cell *matCellDef="let row"> {{row.email}} </mat-cell>
    </ng-container>
    <ng-container matColumnDef="role">
      <mat-header-cell *matHeaderCellDef mat-sort-header> Role </mat-header-cell>
      <mat-cell *matCellDef="let row"> {{row.role}} </mat-cell>
    </ng-container>
    <ng-container matColumnDef="status">
      <mat-header-cell *matHeaderCellDef mat-sort-header> Status </mat-header-cell>
      <mat-cell *matCellDef="let row"> {{row.status}} </mat-cell>
    </ng-container>
    <ng-container matColumnDef="id">
      <mat-header-cell *matHeaderCellDef fxLayoutAlign="end center">View Details</mat-header-cell>
      <mat-cell *matCellDef="let row" fxLayoutAlign="end center" style="margin-right: 8px">
        <a mat-button mat-icon-button [routerLink]="['/manager/users', { outlets: { detail: ['user', {userId: row.id}] } }]" skipLocationChange>
          <mat-icon>visibility</mat-icon>
        </a>
      </mat-cell>
    </ng-container>
    <mat-header-row *matHeaderRowDef="displayedColumns"></mat-header-row>
    <mat-row *matRowDef="let row; columns: displayedColumns;">
    </mat-row>
  </mat-table>

  <mat-paginator [pageSizeOptions]="[5, 10, 25, 100]"></mat-paginator>
</div>

```

只有主视图，表格看起来像这样的截图：

![](img/b9bdc8f6-da44-4279-bf37-70dcff4e8794.png)

UserTable

如果点击查看图标，`ViewUserComponent` 将在详细视图中渲染，如下所示：

![](img/b31311fa-24cf-44b1-884b-14d612c84a12.png)

主/详细视图

然后可以将 Edit 按钮连接起来，将 `userId` 传递给 `UserProfile`，以便编辑和更新数据。或者，您可以将 `UserProfile` 直接呈现在详细视图中。

带有分页的数据表完成了 LemonMart 的实现目的。现在让我们确保我们所有的测试都通过，然后再继续。

# 更新单元测试

由于我们引入了新的 `userService`，为其创建一个虚假实现，使用与 `authService` 和 `commonTestingProviders` 相同的模式。

1.  为 `UserService` 实现 `IUserService` 接口

```ts
src/app/user/user/user.service.ts
export interface IUserService {
  currentUser: BehaviorSubject<IUser>
  getCurrentUser(): Observable<IUser>
  getUser(id): Observable<IUser>
  updateUser(user: IUser): Observable<IUser>
  getUsers(pageSize: number, searchText: string, pagesToSkip: number): Observable<IUsers>
}
...
export class UserService extends CacheService implements IUserService {
```

1.  实现虚假用户服务

```ts
src/app/user/user/user.service.fake.ts
import { Injectable } from '@angular/core'
import { BehaviorSubject, Observable, of } from 'rxjs'

import { IUser, User } from './user'
import { IUsers, IUserService } from './user.service'

@Injectable()
export class UserServiceFake implements IUserService {
  currentUser = new BehaviorSubject<IUser>(new User())

  constructor() {}

```

```ts
  getCurrentUser(): Observable<IUser> {
    return of(new User())
  }

  getUser(id): Observable<IUser> {
    return of(new User((id = id)))
  }

  updateUser(user: IUser): Observable<IUser> {
    return of(user)
  }

  getUsers(pageSize: number, searchText = '', pagesToSkip = 0): Observable<IUsers> {
    return of({
      total: 1,
      items: [new User()],
    } as IUsers)
  }
}
```

1.  在 `commonTestingProviders` 中添加用户服务的虚假到

```ts
src/app/common/common.testing.ts
export const commonTestingProviders: any[] = [
  ...
  { provide: UserService, useClass: UserServiceFake },
]
```

1.  将`SharedComponentsModule`添加到`commonTestingModules`

```ts
src/app/common/common.testing.ts
export const commonTestingModules: any[] = [
  ...
  SharedComponentsModule
]
```

1.  实例化`UserTableComponent`的默认数据

在修复了提供者和导入后，您会注意到`UserTableComponent`仍然无法创建。这是因为，组件初始化逻辑要求定义`dataSource`。如果未定义，组件将无法创建。但是，我们可以在第二个`beforeEach`方法中轻松修改组件属性，该方法在`TestBed`注入了真实的、模拟的或伪造的依赖项到组件类之后执行。查看下面加粗的变化以进行测试数据设置：

```ts
src/app/manager/user-table/user-table.component.spec.ts ...
  beforeEach(() => {
    fixture = TestBed.createComponent(UserTableComponent)
    component = fixture.componentInstance
 component.dataSource = new MatTableDataSource()
 component.dataSource.data = [new User()]
 component._skipLoading = true
    fixture.detectChanges()
  })
...
```

到目前为止，您可能已经注意到通过更新我们的一些中心配置，一些测试通过了，并且其余的测试可以通过应用我们在整本书中一直在使用的各种模式来解决。例如`user-management.component.spec.ts`使用了我们创建的常用测试模块和提供者：

```ts
src/app/manager/user-management/user-management.component.spec.ts      
providers: commonTestingProviders,
imports: commonTestingModules.concat([ManagerMaterialModule]),
```

当您使用提供者和伪造品时，请记住正在测试哪个模块、组件、服务或类，并小心仅提供依赖项的伪造品。

`ViewUserComponent`是一个特殊情况，我们无法使用我们的常用测试模块和提供者，否则我们将最终创建一个循环依赖。在这种情况下，需要手动指定需要引入的模块。

1.  继续修复单元测试配置，直到所有测试都通过！

在本书中，我们没有涵盖任何功能单元测试，其中我们将测试一些业务逻辑以测试其正确性。相反，我们专注于保持自动生成的测试处于工作状态。我强烈建议使用 Angular 自带的优秀框架来实现单元测试，覆盖关键业务逻辑。

您始终可以选择进一步编写基本的单元测试，使用 Jasmine 在隔离环境中测试类和函数。Jasmine 具有丰富的测试双功能，能够模拟和监视依赖项。编写和维护这种基本单元测试更容易、更便宜。但是，这个主题本身是一个深入的主题，超出了本书的范围。

# 总结

在本章中，我们完成了所有主要的 Angular 应用程序设计考虑以及配方，以便能够轻松地实现业务应用程序。我们讨论了应用面向对象的类设计来使数据的填充或序列化更容易。我们创建了可以通过路由器激活或嵌入另一个带有数据绑定的组件的可重用组件。我们表明您可以将数据`POST`到服务器并缓存响应。我们还创建了一个响应屏幕尺寸变化的丰富多步输入表单。通过利用解析守卫从组件中删除样板代码，我们构建了一个主/细节视图。然后，使用辅助路由实现了数据表格分页。

总的来说，通过采用先路由设计、架构和实施方法，我们对应用程序的设计有了一个很好的高层次理解我们想要实现的目标。此外，通过及早识别重用机会，我们能够优化我们的实施策略，提前实现可重用组件，而不会面临过度设计解决方案的风险。

在下一章中，我们将在 AWS 上建立一个高可用的基础架构来托管 LemonMart。我们将更新项目，使用新的脚本来实现无停机蓝绿部署。
