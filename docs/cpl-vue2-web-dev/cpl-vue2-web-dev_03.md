# 第三章：优化您的应用程序并使用组件显示数据

在第二章“显示、循环、搜索和过滤数据”中，我们让 Vue 应用程序显示了我们的人员目录，我们可以利用这个机会来优化我们的代码并将其分离成组件。这样可以使代码更易于管理，更容易理解，并且使其他开发人员能够更容易地了解数据流程（或者在几个月后再次查看代码时，您自己也能更容易理解）。

本章将涵盖以下内容：

+   通过减少重复代码和逻辑组织代码来优化我们的 Vue.js 代码

+   如何创建 Vue 组件并在 Vue 中使用它们

+   如何在组件中使用 props 和 slots

+   利用事件在组件之间传递数据

# 优化代码

当我们在解决问题时编写代码时，有一个时刻你需要退后一步，看看你的代码并对其进行优化。这可能包括减少变量和方法的数量或创建方法以减少重复功能。我们当前的 Vue 应用程序如下所示：

```js
      const app = new Vue({
        el: '#app',
        data: {
          people: [...],
          currency: '$',
          filterField: '',
          filterQuery: '',
          filterUserState: ''
        },
        methods: {
          activeStatus(person) {
            return (person.isActive) ? 'Active' : 
             'Inactive';
          },
          activeClass(person) {
            return person.isActive ? 'active' : 
            'inactive';
          },
          balanceClass(person) {
            let balanceLevel = 'success';
            if(person.balance < 2000) {
              balanceLevel = 'error';
            } else if (person.balance < 3000) {
              balanceLevel = 'warning';
            }
            let increasing = false,
            balance = person.balance / 1000;
            if(Math.round(balance) == 
             Math.ceil(balance)) {
              increasing = 'increasing';
            }
            return [balanceLevel, increasing];
          },
          formatBalance(balance) {
            return this.currency + balance.toFixed(2);
          },
          formatDate(date) {
            let registered = new Date(date);
            return registered.toLocaleString('en-US');
          },
          filterRow(person) {
            let result = true;
            if(this.filterField) {
              if(this.filterField === 'isActive') {
                result = (typeof this.filterUserState 
                 === 'boolean') ? (this.filterUserState 
                 === person.isActive) : true;
              } else {
                let query = this.filterQuery,
                    field = person[this.filterField];
                if(typeof field === 'number') {
                  query.replace(this.currency, '');
                  try {
                    result = eval(field + query);
                  } catch(e) {}
                } else {
                  field = field.toLowerCase();
                  result =        
            field.includes(query.toLowerCase());
                }
              }
            }
            return result;
          },
          isActiveFilterSelected() {
            return (this.filterField === 'isActive');
          }
        }
      });
```

查看前面的代码，我们可以进行一些改进。这些改进包括：

+   减少过滤变量的数量并进行逻辑分组

+   组合格式化函数

+   减少硬编码变量和属性的数量

+   将方法重新排序为更合理的顺序

我们将逐个讨论这些要点，以便我们有一个干净的代码库来构建组件。

# 减少过滤变量的数量并进行逻辑分组

目前的过滤器使用了三个变量`filterField`，`filterQuery`和`filterUserState`。目前唯一将这些变量联系在一起的是名称，而不是它们自己的对象以系统地将它们链接在一起。这样做可以避免任何关于它们是否与同一个组件相关或仅仅是巧合的歧义。在数据对象中，创建一个名为`filter`的新对象，并将每个变量移动到其中：

```js
      data: {
        people: [..],
        currency: '$',
        filter: {
          field: '',
          query: '',
          userState: '',
        }
      }
```

要访问数据，请将`filterField`的所有引用更新为`this.filter.field`。注意额外的点，表示它是过滤器对象的一个键。还要记得更新`filterQuery`和`filterUserState`的引用。例如，`isActiveFilterSelected`方法将变为：

```js
      isActiveFilterSelected() {
        return (this.filter.field === 'isActive');
      }
```

您还需要在视图中更新`v-model`和`v-show`属性-有五个不同变量的出现。

在更新过滤变量的同时，我们可以利用这个机会删除一个变量。根据我们当前的过滤方式，一次只能有一个过滤器处于活动状态。这意味着`query`和`userState`变量在任何时候只被使用一次，这给我们合并这两个变量的机会。为此，我们需要更新视图和应用程序代码以适应这种情况。

从过滤数据对象中删除`userState`变量，并将视图中的任何`filter.userState`出现更新为`filter.query`。现在，在 Vue JavaScript 代码中进行*查找和替换*，将`filter.userState`替换为`filter.query`。

在浏览器中查看应用程序，最初似乎可以工作，可以按字段过滤用户。但是，如果按状态过滤，然后切换到任何其他字段，查询字段将不会显示。这是因为使用单选按钮将值设置为布尔值，当尝试将其转换为小写以进行查询字段时，无法执行此操作。为了解决这个问题，我们可以使用原生 JavaScript 的`String()`函数将`filter.query`变量中的任何值转换为字符串。这确保我们的过滤函数可以与任何过滤输入一起使用：

```js
      if(this.filter.field === 'isActive') {
        result = (typeof this.filter.query ===        
       'boolean') ? (this.filter.query ===             
        person.isActive) : true;
         } else {
        let query = String(this.filter.query),
            field = person[this.filter.field];
           if(typeof field === 'number') {
           query.replace(this.currency, '');
          try {
            result = eval(field + query);
          } catch(e) {}
        } else {
```

```js
          field = field.toLowerCase();
          result = field.includes(query.toLowerCase());
        }
```

现在将此添加到我们的代码中，确保我们的查询数据无论值如何都可以使用。现在创建的问题是，当用户在字段之间切换进行过滤时。如果选择活动用户并选择一个单选按钮，则过滤按预期工作，但是，如果现在切换到电子邮件或另一个字段，则输入框将预填充为`true`或`false`。这会立即进行过滤，并且通常不会返回任何结果。当在两个文本过滤字段之间切换时，也会发生这种情况，这不是期望的效果。

我们希望的是，每当选择框更新时，过滤查询都应该清除。无论是单选按钮还是输入框，选择新字段都应该重置过滤查询，这样可以开始新的搜索。

这是通过删除选择框与`filter.field`变量之间的链接，并创建我们自己的方法来处理更新来完成的。然后，在选择框更改时触发该方法。该方法将清除`query`变量并将`field`变量设置为选择框的值。

在选择框上删除`v-model`属性，并添加一个新的`v-on:change`属性。我们将在其中传递一个方法名，每次选择框更新时都会触发该方法。

`v-on`是一个我们之前没有遇到过的新的 Vue 绑定。它允许您将元素的操作绑定到 Vue 方法。例如，`v-on:click`是最常用的一种，它允许您将`click`函数绑定到元素上。我们将在本书的下一节中详细介绍这个。

`v-bind`可以缩写为冒号，`v-on`可以缩写为`@`符号，允许您使用`@click=""`，例如：

```js
      <select v-on:change="changeFilter($event)"     
       id="filterField">
        <option value="">Disable filters</option>
        <option value="isActive">Active user</option>
        <option value="name">Name</option>
        <option value="email">Email</option>
        <option value="balance">Balance</option>
        <option value="registered">Date 
         registered</option>
      </select>
```

此属性在每次更新时触发`changeFilter`方法，并传递`$event`更改的数据。此默认的 Vue 事件对象包含了许多我们可以利用的信息，但我们关注的是`target.value`数据。

在您的 Vue 实例中创建一个新的方法，接受事件参数并更新`query`和`field`变量。`query`变量需要被清除，所以将其设置为空字符串，而`field`变量可以设置为选择框的值：

```js
      changeFilter(event) {
        this.filter.query = '';
        this.filter.field = event.target.value;
      }
```

现在查看您的应用程序应该清除过滤查询，同时仍然按预期运行。

# 组合格式化函数

我们的下一个优化将是在 Vue 实例中合并`formatBalance`和`formatDate`方法。这将允许我们扩展格式化函数，而不会用几个具有类似功能的方法使代码臃肿。有两种方法可以处理格式化样式函数-我们可以自动检测输入的格式，也可以将所需的格式选项作为第二个选项传递。两者都有其优缺点，但我们将逐步介绍两种方法。

# 自动检测格式化

当传入函数时，自动检测变量类型对于更清晰的代码非常有用。在视图中，您可以调用该函数并传递一个要格式化的参数。例如：

```js
      {{ format(person.balance) }}
```

然后，该方法将包含一个`switch`语句，并根据`typeof`值格式化变量。`switch`语句可以评估单个表达式，然后根据输出执行不同的代码。`switch`语句非常强大，因为它允许构建子句-根据结果利用几个不同的代码片段。有关`switch`语句的更多信息可以在 MDN 上阅读。

如果要比较相同的表达式，`switch`语句是`if`语句的一个很好的替代方案。您还可以为一个代码块设置多个情况，并在之前的情况都不满足时包含一个默认情况。作为使用示例，我们的格式化方法可能如下所示：

```js
      format(variable) {
        switch (typeof variable) {
          case 'string':
          // Formatting if the variable is a string
          break;
          case 'number':
          // Number formatting
          break;
          default:
          // Default formatting
          break;
        }
      }
```

需要注意的重要事项是`break;`行。这些结束了每个`switch` case。如果省略了 break，代码将继续执行下一个 case，这有时是期望的效果。

自动检测变量类型和格式化是简化代码的好方法。然而，对于我们的应用程序来说，这不是一个合适的解决方案，因为我们正在格式化日期，当输出`typeof`结果时，它会变成一个字符串，并且无法从我们可能希望格式化的其他字符串中识别出来。

# 传入第二个变量

前面自动检测的替代方法是将第二个变量传递给`format`函数。这样做可以提供更大的灵活性和可扩展性，以便我们可以格式化其他字段。有了第二个变量，我们可以传入一个固定的字符串，与我们`switch`语句中的预选列表匹配，或者我们可以传入字段本身。在视图中使用固定字符串方法的一个示例是：

```js
      {{ format(person.balance, 'currency') }}
```

这个方法完全适用，并且如果我们有几个不同的字段都需要像`balance`一样进行格式化，那就太好了，但是在使用`balance`键和`currency`格式时似乎有一些轻微的重复。

为了妥协，我们将把`person`对象作为第一个参数传递，这样我们就可以访问所有的数据，将字段的名称作为第二个参数。然后我们将使用这个参数来识别所需的格式化方法并返回特定的数据。

# 创建方法

在您的视图中，用一个格式化函数替换`formatDate`和`formatBalance`函数，将`person`变量作为第一个参数传入，并将字段用引号括起来作为第二个参数：

```js
      <td v-bind:class="balanceClass(person)">
        {{ format(person, 'balance') }}
      </td>
      <td>
        {{ format(person, 'registered') }}
      </td>
```

在 Vue 实例中创建一个新的格式化方法，接受两个参数：`person`和`key`。作为第一步，使用 person 对象和`key`变量检索字段：

```js
      format(person, key) {
        let field = person[key],
            output = field.toString().trim();      
        return output;
      }
```

我们还在函数内部创建了一个名为`output`的第二个变量，这将在函数结束时返回，并默认设置为`field`。这样可以确保如果我们的格式化键与传入的键不匹配，将返回未经处理的字段数据，但我们会将字段转换为字符串并修剪变量中的任何空格。现在运行应用程序将返回没有任何格式化的字段。

添加一个`switch`语句，将表达式设置为仅为`key`。在`switch`语句中添加两个情况，一个是`balance`，另一个是`registered`。由于我们不希望在输入不匹配情况时发生任何事情，所以我们不需要`default`语句：

```js
      format(person, key) {
        let field = person[key],
            output = field.toString().trim();

        switch(key) {
 case 'balance':
 break;
 case 'registered':
 break;
 }
        return output;
      }
```

现在我们只需要将原始格式化函数的代码复制到各个情况中：

```js
      format(person, key) {
        let field = person[key],
            output = field.toString().trim();

        switch(key) {
          case 'balance':
            output = this.currency + field.toFixed(2);
            break;

          case 'registered':
           let registered = new Date(field);
 output = registered.toLocaleString('en-US');
          break;
        }
        return output;
      }
```

这个格式化函数现在更加灵活。如果我们需要处理更多字段（例如处理`name`字段），我们可以添加更多的`switch`情况，或者我们可以在现有代码中添加新的情况。例如，如果我们的数据包含一个字段，详细说明用户“停用”帐户的日期，我们可以轻松地以与注册相同的格式显示它：

```js
      case 'registered':
 case 'deactivated':
        let registered = new Date(field);
        output = registered.toLocaleString('en-US');
        break;
```

# 减少硬编码变量和属性的数量，减少冗余

当查看 Vue JavaScript 时，很快就会发现通过引入全局变量并在函数中设置更多的局部变量可以对其进行优化，使其更易读。我们还可以使用现有功能来避免重复。

第一个优化是在我们的`filterRow()`方法中，我们检查`filter.field`是否处于活动状态。这也在我们用于显示和隐藏单选按钮的`isActiveFilterSelected`方法中重复。更新`if`语句以使用此方法，代码如下：

```js
      ...

    if(this.filter.field === 'isActive') {
    result = (typeof this.filter.query === 'boolean') ?       
    (this.filter.query === person.isActive) : true;
      } else {

      ...
```

上述代码已删除`this.filter.field === 'isActive'`代码，并替换为`isActiveFilterSelected()`方法。现在它应该是这样的：

```js
      ...

    if(this.isActiveFilterSelected()) {
    result = (typeof this.filter.query === 'boolean') ?     
     (this.filter.query === person.isActive) : true;
     } else {

      ...
```

当我们在`filterRow`方法中时，我们可以通过在方法开始时将`query`和`field`存储为变量来减少代码。`result`也不是这个的正确关键字，所以让我们将其更改为`visible`。首先，在开始处创建和存储我们的两个变量，并将`result`重命名为`visible`：

```js
      filterRow(person) {
        let visible = true,
 field = this.filter.field,
 query = this.filter.query;      ...
```

在该函数中替换所有变量的实例，例如，方法的第一部分将如下所示：

```js
      if(field) {
          if(this.isActiveFilterSelected()) {
            visible = (typeof query === 'boolean') ?   
            (query === person.isActive) : true;
          } else {

          query = String(query),
          field = person[field];
```

保存文件并在浏览器中打开应用程序，以确保您的优化没有破坏功能。

最后一步是将方法按照你认为合理的顺序重新排序。可以随意添加注释来区分不同类型的方法，例如与 CSS 类或过滤相关的方法。我还删除了`activeStatus`方法，因为我们可以利用我们的`format`方法来*格式化*此字段的输出。优化后，JavaScript 代码现在如下所示：

```js
      const app = new Vue({
        el: '#app',
         data: {
          people: [...],
          currency: '$',
          filter: {
            field: '',
            query: ''
          }
        },
        methods: {
          isActiveFilterSelected() {
            return (this.filter.field === 'isActive');
          },
          /**
           * CSS Classes
           */
          activeClass(person) {
             return person.isActive ? 'active' : 
             'inactive';
          },
           balanceClass(person) {
            let balanceLevel = 'success';
            if(person.balance < 2000) {
              balanceLevel = 'error';
            } else if (person.balance < 3000) {
              balanceLevel = 'warning';
            }
                let increasing = false,
                balance = person.balance / 1000;
            if(Math.round(balance) == 
             Math.ceil(balance)) {
              increasing = 'increasing';
            }
            return [balanceLevel, increasing];
          },
          /**
           * Display
           */
          format(person, key) {
            let field = person[key],
            output = field.toString().trim();
            switch(key) {
              case 'balance':
                output = this.currency + 
              field.toFixed(2);
                break;
              case 'registered':
          let registered = new Date(field);
          output = registered.toLocaleString('en-US');
          break;  
        case 'isActive':
          output = (person.isActive) ? 'Active' : 
          'Inactive';
            }
        return output;
          },  
          /**
           * Filtering
           */
          changeFilter(event) {
            this.filter.query = '';
            this.filter.field = event.target.value;
          },
          filterRow(person) {
            let visible = true,
                field = this.filter.field,
                query = this.filter.query; 
            if(field) {  
              if(this.isActiveFilterSelected()) {
                visible = (typeof query === 'boolean') ?
               (query === person.isActive) : true;
              } else { 
                query = String(query),
                field = person[field];
                if(typeof field === 'number') {
                  query.replace(this.currency, '');  
                  try {
                    visible = eval(field + query);
                  } catch(e) {}  
                } else {  
                  field = field.toLowerCase();
                  visible = 
                  field.includes(query.toLowerCase());         
                }
              }
            }
            return visible;
          }
        }
      });
```

# 创建 Vue 组件

现在我们对代码的整洁性有信心了，我们可以开始为应用程序的各个部分创建 Vue 组件。暂时放下你的代码，打开一个新文档，同时熟悉组件的使用。

Vue 组件非常强大，是任何 Vue 应用程序的重要组成部分。它们允许你创建可重用代码的包，包括它们自己的数据、方法和计算值。

对于我们的应用程序，我们有机会创建两个组件：一个用于每个人，一个用于我们应用程序的过滤部分。我鼓励你在可能的情况下始终考虑将应用程序拆分为组件，这有助于将代码分组到相关的功能中。

组件看起来像是迷你的 Vue 实例，因为每个组件都有自己的数据、方法和计算属性，还有一些特定于组件的选项，我们将很快介绍。

当一个组件被注册时，你可以创建一个自定义的 HTML 元素在你的视图中使用，例如：

```js
      <my-component></my-component>
```

在命名组件时，你可以使用短横线分隔的 kebab-case（连字符）、没有标点符号但每个单词首字母大写的 PascalCase 或类似 PascalCase 但第一个单词首字母不大写的 camelCase。Vue 组件不受 W3C Web 组件/自定义元素规则的限制或关联，但按照使用 kebab-case 的约定是一个好习惯。

# 创建和初始化你的组件

Vue 组件使用`Vue.component(tagName, options)`语法进行注册。每个组件必须有一个关联的标签名。`Vue.component`注册**必须**在初始化 Vue 实例之前发生。作为最低要求，每个组件应该有一个`template`属性，表示在使用组件时应该显示什么内容。模板必须始终有一个包装元素；这样自定义的 HTML 标签可以被父容器替换。

例如，你不能将以下内容作为你的模板：

```js
      <div>Hello</div><div>Goodbye</div>
```

如果你传递了这种格式的模板，Vue 将在浏览器的 JavaScript 控制台中抛出错误警告。

自己创建一个简单的固定模板的 Vue 组件：

```js
 Vue.component('my-component', {
 template: '<div>hello</div>'
 });

      const app = new Vue({
        el: '#app',

       // App options
      });
```

声明了这个组件后，它现在会给我们一个`<my-component></my-component>` HTML 标签，可以在我们的视图中使用。

您还可以在 Vue 实例本身上指定组件。如果您在一个站点上有多个 Vue 实例，并希望将组件限制在一个实例中，可以使用一个简单的对象创建组件，并在 Vue 实例的`components`对象中分配`tagName`：

```js
      let Child = {
        template: '<div>hello</div>'
      }

      const app = new Vue({
        el: '#app',

        // App options

        components: {
          'my-component': Child
        }
      });
```

对于我们的应用程序，我们将继续使用`Vue.component()`方法来初始化我们的组件。

# 使用您的组件

在您的视图中，添加您的自定义 HTML 元素组件：

```js
      <div id="app">
        <my-component></my-component>
      </div>
```

在浏览器中查看，应该将`<my-component>` HTML 标签替换为`<div>`和一个 hello 消息。

可能有一些情况下，自定义 HTML 标签不会被解析和接受-这些情况往往出现在`<table>`、`<ol>`、`<ul>`和`<select>`元素中。如果是这种情况，您可以在标准 HTML 元素上使用`is=""`属性：

```js
      <ol>
        <li is="my-component"></li>
      </ol>
```

# 使用组件数据和方法

由于 Vue 组件是 Vue 应用程序的自包含元素，它们各自具有自己的数据和函数。这在同一页面上重复使用组件时非常有用，因为信息是每个组件实例自包含的。`methods`和`computed`函数的声明方式与在 Vue 应用程序上相同，但是数据键应该是一个返回对象的函数。

组件的数据对象必须是一个函数。这样每个组件都有自己独立的数据，而不会混淆并共享不同实例的相同组件之间的数据。该函数仍然必须返回一个对象，就像在 Vue 应用程序中一样。

创建一个名为`balance`的新组件，为您的组件添加一个`data`函数和`computed`对象，并为`template`属性添加一个空的`<div>`：

```js
      Vue.component('balance', {
        template: '<div></div>',
        data() {
          return {

          }
        },
        computed: {

        }
      });
```

接下来，在`cost`数据对象中添加一个键/值对，并将变量添加到模板中。在视图中添加`<balance></balance>`自定义 HTML 元素，您应该看到您的整数：

```js
      Vue.component('balance', {
        template: '<div>{{ cost }}</div>',
        data() {
          return {
            cost: 1234
          }
        },
        computed: {

        }
      });
```

与第一章中的 Vue 实例一样，在`computed`对象中添加一个函数，将货币符号附加到整数上，并确保有两位小数。不要忘记将货币符号添加到您的数据函数中。

更新模板以输出计算值而不是原始成本：

```js
      Vue.component('balance', {
        template: '<div>{{ formattedCost }}</div>',
        data() {
          return {
            cost: 1234,
            currency: '$'
          }
        },
        computed: {
          formattedCost() {
 return this.currency + this.cost.toFixed(2);
 }
        }
      });
```

这是一个基本的组件示例，但是它在组件本身上有固定的`cost`。

# 向组件传递数据-props

将 balance 作为一个组件是很好的，但如果 balance 是固定的，那就不太好了。当您通过 HTML 属性传递参数和属性时，组件真正发挥作用。在 Vue 世界中，这些被称为**props**。Props 可以是静态的或变量的。为了使您的组件期望这些属性，您需要使用`props`属性在组件上创建一个数组。

一个例子是我们想要创建一个`heading`组件：

```js
      Vue.component('heading', {
        template: '<h1>{{ text }}</h1>',

        props: ['text']
      });
```

然后，组件将在视图中使用如下：

```js
      <heading text="Hello!"></heading>
```

使用 props，我们不需要在数据对象中定义`text`变量，因为在 props 数组中定义它会自动使其在模板中可用。props 数组还可以接受进一步的选项，允许您定义期望的输入类型，以及是否需要输入或省略时使用的默认值。

为 balance 组件添加一个 prop，以便我们可以将 cost 作为 HTML 属性传递。您的视图现在应该如下所示：

```js
      <balance cost="1234"></balance> 
```

现在，我们可以在 JavaScript 中为组件添加 cost prop，并从数据函数中删除固定值：

```js
      template: '<div>{{ formattedCost }}</div>',
 props: ['cost'],
      data() {
        return {
          currency: '$'
        }
      },
```

然而，在浏览器中运行此代码将在 JavaScript 控制台中引发错误。这是因为，本地情况下，传递的 props 被解释为字符串。我们可以通过两种方式解决这个问题；我们可以在`formatCost()`函数中将我们的 prop 转换为数字，或者我们可以使用`v-bind:`HTML 属性告诉 Vue 接受输入为它是什么。

如果您记得，我们在`true`和`false`值的过滤器中使用了这种技术-允许它们作为布尔值而不是字符串使用。在您的`cost`HTML 属性前面添加`v-bind:`：

```js
      <balance v-bind:cost="15234"></balance> 
```

还有一个额外的步骤可以确保 Vue 知道要期望什么样的输入，并通知其他用户您的代码应该传递什么。这可以在组件本身中完成，并且可以与格式一起，允许您指定默认值以及是否需要传递 prop。

将您的`props`数组转换为一个对象，以`cost`作为键。如果您只是定义字段类型，可以使用 Vue 的简写方式来声明，将值设置为字段类型。这些类型可以是 String、Number、Boolean、Function、Object、Array 或 Symbol。由于我们的 cost 属性应该是一个数字，所以将其添加为键：

```js
      props: {
 cost: Number
 },
```

如果没有定义任何内容，组件渲染为`$0.00`会更好。我们可以通过将默认值设置为`0`来实现这一点。要定义默认值，我们需要将我们的属性转换为一个对象本身 - 包含一个`type`键，其值为`Number`。然后，我们可以定义另一个`default`键，并将值设置为`0`：

```js
      props: {
        cost: {
          type: Number,
 default: 0
 }
      },
```

在浏览器中渲染组件应该显示传递到 cost 属性的任何值，但是如果删除这个属性，将会渲染为`$0.00`。

总结一下，我们的组件如下：

```js
      Vue.component('balance', {
        template: '<div>{{ formattedCost }}</div>',

        props: {
          cost: {
            type: Number,
            default: 0
          }
        },

        data() {
          return {
            currency: '$'
          }
        },

        computed: {
          formattedCost() {
            return this.currency +       
            this.cost.toFixed(2);
          }
        }
      });
```

当我们制作列表应用程序的`person`组件时，我们应该能够在此示例上进行扩展。

# 向组件传递数据 - 插槽

有时您可能需要将 HTML 块传递给组件，这些 HTML 块不存储在属性中，或者您希望在组件中显示之前进行格式化。与其尝试在计算变量或类似变量中进行预格式化，不如使用组件的插槽。

插槽就像占位符，允许您在组件的开头和结尾标签之间放置内容，并确定它们将显示在哪里。

一个完美的例子是模态窗口。这些通常有几个标签，并且通常由大量的 HTML 组成，如果您希望在应用程序中多次使用它，就需要复制和粘贴。相反，您可以创建一个`modal-window`组件，并使用插槽传递您的 HTML。

创建一个名为`modal-window`的新组件。它接受一个名为`visible`的属性，默认为`false`，接受一个布尔值。对于模板，我们将使用*Bootstrap modal*中的 HTML 作为一个很好的示例，说明使用插槽的组件如何轻松简化您的应用程序。为了确保组件被样式化，请确保在文档中包含 bootstrap 的*asset 文件*：

```js
      Vue.component('modal-window', {
        template: `<div class="modal fade">
          <div class="modal-dialog" role="document">
            <div class="modal-content">
              <div class="modal-header">
               <button type="button" class="close" 
               data-dismiss="modal" aria-label="Close">
               <span aria-hidden="true">&times;</span>
              </button>
             </div>
          <div class="modal-body">
          </div>
           <div class="modal-footer">
            <button type="button" class="btn btn-  
             primary">Save changes</button>
            <button type="button" class="btn btn-      
             secondary" data-dismiss="modal">Close
            </button>
            </div>
          </div>
         </div>
      </div>`,

      props: {
        visible: {
          type: Boolean,
          default: false
        }
       }
    });
```

我们将使用 visible 属性来确定模态窗口是否打开。在外部容器中添加一个`v-show`属性，接受`visible`变量：

```js
      Vue.component('modal-window', {
          template: `<div class="modal fade" v-
            show="visible">
          ...
        </div>`,

        props: {
          visible: {
            type: Boolean,
            default: false
          }
        }
      });
```

将您的`modal-window`组件添加到应用程序中，暂时将`visible`设置为`true`，以便我们可以理解和看到发生了什么：

```js
      <modal-window :visible="true"></modal-window>
```

现在我们需要向我们的模态框传递一些数据。在两个标签之间添加一个标题和一些段落：

```js
      <modal-window :visible="true">
        <h1>Modal Title</h1>
 <p>Lorem ipsum dolor sit amet, consectetur                
         adipiscing elit. Suspendisse ut rutrum ante, a          
         ultrices felis. Quisque sodales diam non mi            
         blandit dapibus. </p>
 <p>Lorem ipsum dolor sit amet, consectetur             
          adipiscing elit. Suspendisse ut rutrum ante, a             
          ultrices felis. Quisque sodales diam non mi             
          blandit dapibus. </p>
       </modal-window>
```

在浏览器中按刷新按钮不会有任何反应，因为我们需要告诉组件如何处理数据。在模板中，在您希望内容出现的位置添加一个`<slot></slot>`HTML 标签。将其添加到具有`modal-body`类的`div`中：

```js
      Vue.component('modal-window', {
        template: `<div class="modal fade" v-      
        show="visible">
          <div class="modal-dialog" role="document">
            <div class="modal-content">
              <div class="modal-header">
          <button type="button" class="close" data-              
              dismiss="modal" aria-label="Close">
               <span aria-hidden="true">&times;</span>
             </button>
              </div>
              <div class="modal-body">
                <slot></slot>
              </div>
              <div class="modal-footer">
              <button type="button" class="btn btn-  
             primary">Save changes</button>
             <button type="button" class="btn btn-                   
               secondary" data-
            dismiss="modal">Close</button>
           </div>
           </div>
        </div>
        </div>`,

         props: {
          visible: {
            type: Boolean,
            default: false
          }
        }
      });
```

现在查看您的应用程序将在模态窗口中显示您传递的内容。通过这个新组件，应用程序看起来更清晰。

查看 Bootstrap 的 HTML，我们可以看到有一个头部、主体和底部的空间。我们可以使用命名插槽来标识这些部分。这样，我们就可以将特定的内容传递到组件的特定区域。

在模态窗口的头部和底部创建两个新的`<slot>`标签。给这些新的标签添加一个 name 属性，但保留现有的空标签：

```js
      template: `<div class="modal fade" v-              
      show="visible">
        <div class="modal-dialog" role="document">
          <div class="modal-content">
            <div class="modal-header">
              <slot name="header"></slot>
              <button type="button" class="close" data-
               dismiss="modal" aria-label="Close">
              <span aria-hidden="true">&times;</span>
             </button>
          </div>
           <div class="modal-body">
            <slot></slot>
          </div>
          <div class="modal-footer">
            <slot name="footer"></slot>
            <button type="button" class="btn btn-  
            primary">Save changes</button><button type="button" class="btn btn-
           secondary" data-
           dismiss="modal">Close</button>
           </div>
        </div>
       </div>
     </div>`,
```

在我们的应用程序中，我们现在可以通过在 HTML 中指定`slot`属性来指定内容放在哪里。这可以放在特定的标签上，也可以放在几个标签周围的容器上。任何没有`slot`属性的 HTML 也将默认为无名插槽：

```js
      <modal-window :visible="true">
        <h1 slot="header">Modal Title</h1>

        <p>Lorem ipsum dolor sit amet, consectetur             
        adipiscing elit. Suspendisse ut rutrum ante, a 
        ultrices felis. Quisque sodales diam non mi 
         blandit dapibus. </p>

        <p slot="footer">Lorem ipsum dolor sit amet,            
         consectetur adipiscing elit. Suspendisse ut 
         rutrum ante, a ultrices felis. Quisque sodales 
           diam non mi blandit dapibus. </p>
      </modal-window>
```

现在，我们可以指定并将我们的内容定向到特定的位置。

插槽的最后一件事是指定默认值。例如，您可能希望大部分时间在页脚显示按钮，但如果需要，可以替换它们。使用`<slot>`，在标签之间放置的任何内容都将显示，除非在应用程序中指定组件时进行覆盖。

创建一个名为`buttons`的新插槽，并将按钮放在底部。尝试用其他内容替换它们。

模板变为：

```js
      template: `<div class="modal fade" v-
      show="visible">
        <div class="modal-dialog" role="document">
          <div class="modal-content">
            <div class="modal-header">
              <slot name="header"></slot>
              <button type="button" class="close" data-
              dismiss="modal" aria-label="Close">
                <span aria-hidden="true">&times;</span>
              </button>
            </div>
            <div class="modal-body">
              <slot></slot>
            </div>
            <div class="modal-footer">
              <slot name="footer"></slot>
              <slot name="buttons">
                <button type="button" class="btn btn-
                 primary">Save changes</button>
                <button type="button" class="btn btn-
                 secondary" data-
                 dismiss="modal">Close</button>
              </slot>
            </div>
          </div>
        </div>
      </div>`,
```

HTML 如下：

```js

     <modal-window :visible="true">
     <h1 slot="header">Modal Title</h1>
      <p>Lorem ipsum dolor sit amet, consectetur 
      adipiscing elit. Suspendisse ut rutrum ante, a 
      ultrices felis. Quisque sodales diam non mi blandit 
      dapibus. </p>

        <p slot="footer">Lorem ipsum dolor sit amet, 
       consectetur adipiscing elit. Suspendisse ut rutrum 
       ante, a ultrices felis. Quisque sodales diam non mi 
       blandit dapibus. </p>

        <div slot="buttons">
 <button type="button" class="btn btn-      
           primary">Ok</button> </div>
       </modal-window>
```

虽然我们不会在人员列表应用程序中使用插槽，但了解 Vue 组件的功能是很好的。如果您希望使用这样的模态框，您可以将可见性设置为默认为 false 的变量。然后，您可以添加一个具有点击方法的按钮，将变量从`false`更改为`true`，从而显示模态框。

# 创建一个可重复使用的组件

组件的美妙之处在于可以在同一个视图中多次使用它们。这使您能够为该数据的布局拥有一个单一的“真实来源”。我们将为人员列表创建一个可重复使用的组件，并为过滤部分创建一个单独的组件。

打开您在前几章中创建的人员列表代码，并创建一个名为`team-member`的新组件。不要忘记在 Vue 应用程序初始化之前定义组件。为组件添加一个`prop`，以允许传递 person 对象。为了验证目的，只需指定它可以是一个`Object`：

```js
      Vue.component('team-member', {
        props: {
          person: Object
        }
      });
```

现在，我们需要将模板整合到组件中，即在我们的视图中（包括）`tr`内的所有内容。

组件中的模板变量只接受没有换行符的普通字符串，因此我们需要执行以下操作之一：

+   内联我们的 HTML 模板-对于小模板非常好，但在这种情况下会牺牲可读性

+   使用`+`字符串连接添加新行-适用于一两行，但会使我们的 JavaScript 膨胀

+   创建一个模板块-Vue 允许我们使用在视图中使用`text/x-template`语法和 ID 定义的外部模板

由于我们的模板相当大，我们将选择第三个选项，在视图末尾声明我们的模板。

在您的 HTML 中，在您的应用程序之外，创建一个新的脚本块，并添加`type`和`ID`属性：

```js
      <script type="text/x-template" id="team-member-            
       template">
      </script>
```

然后，我们将人员模板移动到此块中，并删除`v-for`属性-我们仍将在应用程序本身中使用它：

```js
      <script type="text/x-template" id="team-member-
      template">
        <tr v-show="filterRow(person)">
 <td>
 {{ person.name }}
 </td>
 <td>
 <a v-bind:href="'mailto:' + person.email">{{                
             person.email }}</a>
 </td>
 <td v-bind:class="balanceClass(person)">
 {{ format(person, 'balance') }}
 </td>
 <td>
 {{ format(person, 'registered') }}
 </td>
 <td v-bind:class="activeClass(person)">
 {{ format(person, 'isActive') }}
```

```js
 </td>
 </tr>
      </script>
```

现在，我们需要更新视图，使用`team-member`组件代替固定代码。为了使我们的视图更清晰易懂，我们将利用前面提到的`<template>`HTML 属性。创建一个`<template>`标签，并添加我们之前使用的`v-for`循环。为避免混淆，更新循环以使用`individual`作为每个人的变量。它们可以相同，但如果变量、组件和属性具有不同的名称，代码更容易阅读。将`v-for`更新为`v-for="individual in people"`：

```js
      <table>
       <template v-for="individual in people">
       </template>
      </table>
```

在您的视图的`template`标签内，添加一个新的`team-member`组件实例，将`individual`变量传递给`person`属性。不要忘记在 person 属性前添加`v-bind:`，否则组件将将其解释为一个固定字符串，其值为 individual 的值：

```js
      <table>
        <template v-for="individual in people">
          <team-member v-bind:person="individual"></team-           
            member>
        </template>
      </table>
```

现在，我们需要更新组件，使用我们声明的模板作为`template`属性和脚本块的 ID 作为值：

```js
      Vue.component('team-member', {
        template: '#team-member-template',
        props: {
          person: Object
        }
      });
```

在浏览器中查看应用程序将在 JavaScript 控制台中创建多个错误。这是因为我们引用了一些不再可用的方法-因为它们在父 Vue 实例上，而不是在组件上。如果您想验证组件是否正常工作，请将代码更改为仅输出人员的名称，并按刷新：

```js
      <script type="text/x-template" id="team-member-             
        template">
        <tr v-show="filterRow()">
          <td>
            {{ person.name }}
          </td>
        </tr>
      </script>
```

# 创建组件方法和计算函数

现在，我们需要在子组件上创建我们在 Vue 实例上创建的方法，以便可以使用它们。我们可以将方法从父组件剪切并粘贴到子组件中，希望它们能够正常工作；然而，这些方法依赖于父组件的属性（如过滤数据），我们还有机会利用`computed`属性，它可以缓存数据并加快应用程序的速度。

暂时从`tr`元素中删除`v-show`属性，因为这涉及到过滤，而这将在我们正确显示行后进行讨论。我们将逐步解决错误，并逐个解决它们，以帮助您理解使用 Vue 进行问题解决。

# CSS 类函数

在浏览器中查看应用程序时，我们遇到的第一个错误是：

属性或方法“balanceClass”未定义。

第一个错误涉及到我们使用的`balanceClass`和`activeClass`函数。这两个函数根据人员的数据添加 CSS 类，一旦组件被渲染，这些数据就不会改变。

因此，我们可以使用 Vue 中的缓存。将方法移到组件中，但将它们放在一个新的`computed`对象中，而不是`methods`对象中。

对于组件，每次调用时都会创建一个新的实例，因此我们可以依赖于通过`prop`传递的`person`对象，不再需要将`person`传递给函数。从函数和视图中删除参数，还要将函数内部对`person`的任何引用更新为`this.person`，以引用存储在组件上的对象：

```js
 computed: {
        /**
         * CSS Classes
         */
        activeClass() {
          return this.person.isActive ? 'active' : 
      'inactive';
        },

        balanceClass() {
          let balanceLevel = 'success';

          if(this.person.balance < 2000) {
            balanceLevel = 'error';
          } else if (this.person.balance < 3000) {
            balanceLevel = 'warning';
          }

          let increasing = false,
              balance = this.person.balance / 1000;

          if(Math.round(balance) == Math.ceil(balance)) {
            increasing = 'increasing';
          }

          return [balanceLevel, increasing];
        }
 },
```

使用此函数的组件模板部分现在应该如下所示：

```js
      <td v-bind:class="balanceClass">
    {{ format(person, 'balance') }}
      </td>
```

# 格式化值函数

当将`format()`函数移动到组件中格式化我们的数据时，我们面临两个选择。我们可以像原样移动它并将其放在`methods`对象中，或者我们可以利用 Vue 的缓存和约定，为每个值创建一个`computed`函数。

我们正在构建可扩展性的应用程序，因此建议为每个值创建计算函数，这也有助于整理我们的模板。在计算对象中创建三个函数，分别命名为`balance`，`dateRegistered`和`status`。将`format`函数的相应部分复制到每个函数中，再次将`person`的引用更新为`this.person`。

我们之前是使用函数参数来检索字段，现在你可以在每个函数中修复该值。您还需要添加一个带有余额函数的货币符号的数据对象 - 将其添加到`props`之后：

```js
      data() {
        return {
          currency: '$'
        }
      },
```

由于`team-member`组件是唯一使用货币符号的地方，我们可以将其从 Vue 应用程序本身中删除。我们还可以从父 Vue 实例中删除格式化函数。

总的来说，我们的 Vue `team-member`组件应该如下所示：

```js
      Vue.component('team-member', {
        template: '#team-member-template',
        props: {
          person: Object 
       },
        data() {
          return {
            currency: '$'
          }
        },
        computed: {
          /**
           * CSS Classes
           */
          activeClass() {
            return this.person.isActive ? 'active' : 
            'inactive';
          },
          balanceClass() {
            let balanceLevel = 'success';   
            if(this.person.balance < 2000) {
              balanceLevel = 'error';
            } else if (this.person.balance < 3000) {
              balanceLevel = 'warning';
            }
          let increasing = false,
                balance = this.person.balance / 1000; 
            if(Math.round(balance) == Math.ceil(balance))                           
            {
              increasing = 'increasing';
            }
            return [balanceLevel, increasing];
          }, 
          /**
           * Fields
           */
          balance() {
            return this.currency +       
            this.person.balance.toFixed(2);
          },
          dateRegistered() {
            let registered = new 
            Date(this.person.registered);
            return registered.toLocaleString('en-US');
          },
          status() {
            return (this.person.isActive) ? 'Active' : 
            'Inactive';
          }
        }
      });
```

与之前相比，我们的`team-member-template`应该看起来非常简单：

```js
      <script type="text/x-template" id="team-member-
      template">
        <tr v-show="filterRow()">
          <td>
            {{ person.name }}
          </td>
          <td>
            <a v-bind:href="'mailto:' + person.email">{{ 
            person.email }}</a>
          </td>
          <td v-bind:class="balanceClass">
            {{ balance }}
          </td>
          <td>
            {{ dateRegistered }}
          </td>
          <td v-bind:class="activeClass">
            {{ status }}
          </td>
        </tr>
      </script>
```

最后，我们的 Vue 实例应该显得更小：

```js
      const app = new Vue({
        el: '#app',
        data: {
          people: [...],
          filter: {
            field: '',
            query: ''
          }
        },
        methods: {
          isActiveFilterSelected() {
            return (this.filter.field === 'isActive');
          },   
          /**
           * Filtering
           */
          filterRow(person) {
            let visible = true,
                field = this.filter.field,
                query = this.filter.query;  
            if(field) {   
              if(this.isActiveFilterSelected()) {
                visible = (typeof query === 'boolean') ? 
                  (query === person.isActive) : true;
              } else {
                query = String(query),
                field = person[field]; 
          if(typeof field === 'number') {
            query.replace(this.currency, '');
                  try {
                    visible = eval(field + query);
                  } catch(e) {}   
                } else {
                  field = field.toLowerCase();
                  visible = 
                  field.includes(query.toLowerCase())  
                }
              }
            }
            return visible;
          }
          changeFilter(event) {
            this.filter.query = '';
            this.filter.field = event.target.value;
          }
        }
      });
```

在浏览器中查看应用程序，我们应该看到我们的人员列表，并在表格单元格中添加了正确的类，并在字段中添加了格式。

# 使过滤再次起作用

在模板中的包含`tr`元素中重新添加`v-show="filterRow()"`属性。由于我们的组件在每个实例上都有缓存的 person，我们不再需要将 person 对象传递给该方法。刷新页面将在 JavaScript 控制台中给出新的错误：

```js
Property or method "filterRow" is not defined on the instance but referenced during render
```

这个错误是因为我们的组件有`v-show`属性，根据我们的过滤和属性来显示和隐藏，但没有相应的`filterRow`函数。由于我们不在其他地方使用它，我们可以将该方法从 Vue 实例移动到组件中，并将其添加到`methods`组件中。删除 person 参数并更新方法以使用`this.person`：

```js
      filterRow() {
        let visible = true,
            field = this.filter.field,
            query = this.filter.query;
            if(field) {
            if(this.isActiveFilterSelected()) {
            visible = (typeof query === 'boolean') ?                 
           (query === this.person.isActive) : true;
            } else {

            query = String(query),
            field = this.person[field];

            if(typeof field === 'number') {
              query.replace(this.currency, '');
              try {
                visible = eval(field + query);
              } catch(e) {}
              } else {

              field = field.toLowerCase();
              visible = 
            field.includes(query.toLowerCase());
            }
          }
        }
        return visible;
      }
```

控制台中的下一个错误是：

```js
Cannot read property 'field' of undefined
```

过滤不起作用的原因是`filterRow`方法在组件上寻找`this.filter.field`和`this.filter.query`，而不是它所属的父 Vue 实例。

作为一个快速修复，您可以使用`this.$parent`来引用父元素上的数据 - 但是，这不是推荐的做法，只能在极端情况下或快速传递数据时使用。

为了将数据传递给组件，我们将使用另一个 prop - 类似于我们如何将 person 传递给组件。幸运的是，我们已经将我们的过滤数据分组，所以我们可以传递一个对象而不是`query`或`field`的单个属性。在组件上创建一个名为`filter`的新 prop，并确保只允许传递一个`Object`：

```js
      props: {
        person: Object,
        filter: Object
      },
```

我们可以将该属性添加到`team-member`组件中，从而允许我们传递数据：

```js
      <table>
        <template v-for="individual in people">
          <team-member v-bind:person="individual" v-               
           bind:filter="filter"></team-member>
        </template>
      </table>
```

为了使我们的过滤器工作，我们需要传入另一个属性-`isActiveFilterSelected()`函数。创建另一个 prop，标题为`statusFilter`，只允许`Boolean`作为值（因为这是函数的等价值），并将函数传递进去。更新`filterRow`方法以使用这个新值。我们的组件现在看起来像这样：

```js
      Vue.component('team-member', {
        template: '#team-member-template',
        props: {
          person: Object,
          filter: Object,
          statusFilter: Boolean
        },
        data() {
          return {
            currency: '$'
          }
        },
        computed: {
          /**
           * CSS Classes
           */
          activeClass() {
            return this.person.isActive ? 'active' : 
            'inactive';
            },
            balanceClass() {
            let balanceLevel = 'success';

         if(this.person.balance < 2000) {
           balanceLevel = 'error';
          } else if (this.person.balance < 3000) {
            balanceLevel = 'warning';
          }
          let increasing = false,
            balance = this.person.balance / 1000;
           if(Math.round(balance) == Math.ceil(balance)) {
             increasing = 'increasing';
          }
          return [balanceLevel, increasing];
        },
       /**
       * Fields
         */
       balance() {
       return this.currency +    
       this.person.balance.toFixed(2);
       },
      dateRegistered() {
       let registered = new Date(this.registered); 
        return registered.toLocaleString('en-US');
        },
        status() {
           return output = (this.person.isActive) ?    
          'Active' : 'Inactive';
         }
       },
       methods: {
        filterRow() {
         let visible = true,
            field = this.filter.field,
            query = this.filter.query;

         if(field) {  
           if(this.statusFilter) {
             visible = (typeof query === 'boolean') ? 
            (query === this.person.isActive) : true;
           } else {
             query = String(query),
            field = this.person[field];  
              if(typeof field === 'number') {
                query.replace(this.currency, '');  
                 try {
                 visible = eval(field + query);
                } catch(e) {
            } 
           } else {   
            field = field.toLowerCase();
            visible = field.includes(query.toLowerCase());
             }
            }
           }
           return visible;
        }
       }
     });
```

现在，视图中的组件带有额外的 props 如下所示。请注意，当作为 HTML 属性使用时，驼峰式的 prop 变为蛇形式（用连字符分隔）：

```js
      <template v-for="individual in people">
          <team-member v-bind:person="individual" v-               bind:filter="filter" v-bind:status-      
            filter="isActiveFilterSelected()"></team-
            member>
       </template>
```

# 将过滤器作为一个组件

现在我们需要将过滤部分作为一个独立的组件。在这种情况下，这并不是必需的，但这是一个好的实践，并且给我们带来了更多的挑战。

我们在将过滤器作为组件时面临的问题是在过滤组件和`team-member`组件之间传递过滤数据的挑战。Vue 通过自定义事件来解决这个问题。这些事件允许您从子组件向父组件或其他组件传递（或“发射”）数据。

我们将创建一个过滤组件，当过滤改变时，将数据传递回父 Vue 实例。这些数据已经通过`team-member`组件传递以进行过滤。

# 创建组件

与`team-member`组件一样，在您的 JavaScript 中声明一个新的`Vue.component()`，引用模板 ID 为`#filtering-template`。在视图中创建一个新的`<script>`模板块，并给它相同的 ID。用一个`<filtering>`自定义 HTML 模板替换视图中的过滤表单，并将表单放在`filtering-template`脚本块中。

您的视图应该如下所示：

```js
      <div id="app">
       <filtering></filtering>
       <table>
         <template v-for="individual in people">
           <team-member v-bind:person="individual" v-
            bind:filter="filter" v-
            bind:statusfilter="isActiveFilterSelected()">           </team-member>
         </template>
       </table>
      </div>

 <script type="text/x-template" id="filtering-
      template">
        <form>
          <label for="fiterField">
            Field:
            <select v-on:change="changeFilter($event)"                 id="filterField">
           <option value="">Disable filters</option>
           <option value="isActive">Active user</option>
           <option value="name">Name</option>
           <option value="email">Email</option>
           <option value="balance">Balance</option>
           <option value="registered">Date      
            registered</option>
           </select>
         </label>
        <label for="filterQuery" v-show="this.filter.field 
         && !isActiveFilterSelected()">
            Query:
            <input type="text" id="filterQuery" v-
            model="filter.query">
          </label>
          <span v-show="isActiveFilterSelected()">
            Active:
         <label for="userStateActive">
            Yes:
             <input type="radio" v-bind:value="true"       id="userStateActive" v-model="filter.query">
          </label>
            <label for="userStateInactive">
            No:
        <input type="radio" v-bind:value="false" 
        id="userStateInactive" v-model="filter.query">
         </label>
       </span>
      </form>
 </script>
      <script type="text/x-template" id="team-member-
       template">
       // Team member template
    </script>
```

你的 JavaScript 中应该有以下内容：

```js
      Vue.component('filtering', {
        template: '#filtering-template'
      });
```

# 解决 JavaScript 错误

与`team-member`组件一样，您将在 JavaScript 控制台中遇到一些错误。通过复制`filter`数据对象以及父实例中的`changeFilter`和`isActiveFilterSelected`方法来解决这些错误。我们暂时将它们保留在组件和父实例中，但稍后将删除重复部分：

```js
      Vue.component('filtering', {
        template: '#filtering-template',

        data() {
 return {
 filter: {
 field: '',
 query: ''
 }
 }
 },

 methods: {
 isActiveFilterSelected() {
 return (this.filter.field === 'isActive');
 },

 changeFilter(event) {
 this.filter.query = '';
 this.filter.field = event.target.value;
 }
 }
      });
```

运行应用程序将显示过滤器和人员列表，但过滤器不会更新人员列表，因为它们尚未进行通信。

# 使用自定义事件来更改过滤字段

使用自定义事件，您可以使用`$on`和`$emit`函数将数据传递回父实例。对于这个应用程序，我们将在父 Vue 实例上存储过滤数据，并从组件中更新它。然后，`team-member`组件可以从 Vue 实例中读取数据并进行相应的过滤。

第一步是利用父 Vue 实例上的 filter 对象。从组件中删除`data`对象，并通过 prop 传递父对象 - 就像我们在`team-member`组件中所做的一样：

```js
      <filtering v-bind:filter="filter"></filtering>
```

现在，我们将修改`changeFilter`函数以发出事件数据，以便父实例可以更新`filter`对象。

从`filtering`组件中删除现有的`changeFilter`方法，并创建一个名为`change-filter-field`的新方法。在这个方法中，我们只需要使用`$emit`来发出在下拉菜单中选择的字段的名称。`$emit`函数接受两个参数：一个键和一个值。发出一个键为`change-filter-field`的事件，并将`event.target.value`作为数据传递。当使用多个单词的变量（例如`changeFilterField`）时，请确保这些变量在事件名称（`$emit`函数的第一个参数）和 HTML 属性中使用连字符：

```js
      changeFilterField(event) {
        this.$emit('change-filter-field', 
      event.target.value);
      }
```

为了将数据传递给父 Vue 实例上的 changeFilter 方法，我们需要在<filtering>元素中添加一个新的 prop。这使用`v-on`并绑定到自定义事件名称。然后将父方法名称作为属性值。将属性添加到您的元素中：

```js
      <filtering v-bind:filter="filter" v-on:change-filter-field="changeFilter"></filtering>
```

此属性告诉 Vue 在发出`change-filter-field`事件时触发`changeFilter`方法。然后，我们可以调整我们的方法来接受参数作为值：

```js
      changeFilter(field) {
        this.filter.query = '';
        this.filter.field = field;
      }
```

然后清除过滤器并更新字段值，然后通过 props 传递给我们的组件。

# 更新过滤器查询

为了发出查询字段，我们将使用一个之前未使用过的新 Vue 键，称为`watch`。`watch`函数跟踪数据属性并根据输出运行方法。它还能够发出事件。由于我们的文本字段和单选按钮都设置为更新 field.query 变量，所以我们将在此上创建一个新的`watch`函数。

在组件的方法之后创建一个新的`watch`对象：

```js
      watch: {
        'filter.query': function() {
        }
      }
```

关键是您希望监视的变量。由于我们的变量包含一个点，所以需要用引号括起来。在这个函数中，创建一个新的`$emit`事件`change-filter-query`，输出`filter.query`的值：

```js
     watch: {
         'filter.query': function() {
         this.$emit('change-filter-query', 
         this.filter.query)
         }
       }
```

现在我们需要将这个方法和自定义事件绑定到视图中的组件，以便它能够将数据传递给父实例。将属性的值设置为`changeQuery`-我们将创建一个处理此操作的方法：

```js
      <filtering v-bind:filter="filter" v-on:change-      
      filter-field="changeFilter" v-on:change-filter-          
      query="changeQuery"></filtering>
```

在父 Vue 实例中，创建一个名为`changeQuery`的新方法，简单地根据输入更新`filter.query`的值：

```js
     changeQuery(query) {
       this.filter.query = query;
     }
```

我们的过滤现在又可以工作了。更新选择框和输入框（或单选按钮）现在都会更新我们的人员列表。我们的 Vue 实例变得更小了，我们的模板和方法都包含在单独的组件中。

最后一步是避免在`team-member`组件上重复使用`isActiveFilterSelected()`方法，因为这个方法只在`team-member`组件上使用一次，但在`filtering`组件上使用多次。从父 Vue 实例中删除该方法，从`team-member` HTML 元素中删除 prop，并将`filterRow`方法中的`statusFilter`变量替换为传递的函数的内容。

最终的 JavaScript 代码现在如下所示：

```js
      Vue.component('team-member', {
        template: '#team-member-template',
        props: {
          person: Object,
          filter: Object
        },
        data() {
          return {
            currency: '$'
          }
        },
        computed: {
          /**
           * CSS Classes
           */
           activeClass() {
            return this.person.isActive ? 'active' : 'inactive';
          },
          balanceClass() {
            let balanceLevel = 'success';    
            if(this.person.balance < 2000) {
              balanceLevel = 'error';
            } else if (this.person.balance < 3000) {
              balanceLevel = 'warning';
            }
           let increasing = false,
            balance = this.person.balance / 1000;      
            if(Math.round(balance) == Math.ceil(balance))             {
             increasing = 'increasing';
            } 
            return [balanceLevel, increasing];
          },
          /**
           * Fields
           */
          balance() {
            return this.currency +       
          this.person.balance.toFixed(2);
          },
          dateRegistered() {
            let registered = new Date(this.registered);  
            return registered.toLocaleString('en-US');
          },
          status() {
            return output = (this.person.isActive) ? 
           'Active' : 'Inactive';
          }
        },
          methods: {
          filterRow() {
            let visible = true,
            field = this.filter.field,
            query = this.filter.query;         
            if(field) {      
              if(this.filter.field === 'isActive') {
              visible = (typeof query === 'boolean') ? 
             (query === this.person.isActive) : true;
              } else {   
                query = String(query),
                field = this.person[field]; 
                if(typeof field === 'number') {
                  query.replace(this.currency, '');
               try {
              visible = eval(field + query);
            } catch(e) {}

          } else {

            field = field.toLowerCase();
            visible = field.includes(query.toLowerCase());  
              }
           }
          }
            return visible;
          }
          }
         });

     Vue.component('filtering', {
     template: '#filtering-template',
       props: {
       filter: Object
     },
       methods: {
       isActiveFilterSelected() {
        return (this.filter.field === 'isActive');
       },     
        changeFilterField(event) {
        this.filedField = '';
       this.$emit('change-filter-field',                     
        event.target.value);
          },
        },
        watch: {
    'filter.query': function() {
      this.$emit('change-filter-query', this.filter.query)
          }
        }
      });

      const app = new Vue({
        el: '#app',

        data: {
          people: [...],
          filter: {
            field: '',
            query: ''
          }
        },
        methods: { 
          changeFilter(field) {
            this.filter.query = '';
            this.filter.field = field;
          },
          changeQuery(query) {
            this.filter.query = query;
          }
        }
      });
```

视图现在如下所示：

```js
     <div id="app">
        <filtering v-bind:filter="filter" v-on:change-
         filter-field="changeFilter" v-on:change-filter-
          query="changeQuery"></filtering>
       <table>
         <template v-for="individual in people">
          <team-member v-bind:person="individual" v-  
          bind:filter="filter"></team-member>
         </template>
        </table>
     </div>
    <script type="text/x-template" id="filtering-
     template">
       <form>
      <label for="fiterField">
       Field:
      <select v-on:change="changeFilterField($event)" 
         id="filterField">
        <option value="">Disable filters</option>
        <option value="isActive">Active user</option>
        <option value="name">Name</option>
        <option value="email">Email</option>
        <option value="balance">Balance</option>
        <option value="registered">Date     
          registered</option>
         </select>
          </label>
         <label for="filterQuery" v-
         show="this.filter.field && 
          !isActiveFilterSelected()">
         Query:
        <input type="text" id="filterQuery" v-    
         model="filter.query">
          </label>

          <span v-show="isActiveFilterSelected()">
           Active:

            <label for="userStateActive">
              Yes:
            <input type="radio" v-bind:value="true"   
          id="userStateActive" v-model="filter.query">
           </label>
          <label for="userStateInactive">
           No:
            <input type="radio" v-bind:value="false"                 id="userStateInactive" v-model="filter.query">
            </label>
          </span>
        </form>
      </script>
      <script type="text/x-template" id="team-member-
      template">
        <tr v-show="filterRow()">
          <td>
            {{ person.name }}
          </td>
          <td>
            <a v-bind:href="'mailto:' + person.email">{{                person.email }}</a>
          </td>
          <td v-bind:class="balanceClass">
            {{ balance }}
          </td>
          <td>
            {{ dateRegistered }}
          </td>
          <td v-bind:class="activeClass">
            {{ status }}
          </td>
        </tr>
      </script>
```

# 总结

在过去的三章中，您已经学会了如何初始化一个新的 Vue 实例，computed、method 和 data 对象的含义，以及如何列出对象中的数据并对其进行正确显示的操作。您还学会了如何创建组件以及保持代码清洁和优化的好处。

在书的下一节中，我们将介绍 Vuex，它可以帮助我们更好地存储和操作存储的数据。
