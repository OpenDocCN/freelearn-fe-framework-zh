

# 第十章：Angular 应用的国际化、本地化和无障碍性

在开发应用时，你通常针对来自许多不同国家的用户；因此，将国际化本地化添加到您的 Angular 应用中至关重要。**国际化**是指使您的应用可翻译的过程，这样讲不同语言的人就可以无任何问题使用您的应用。通过**本地化**，您将网站内容定制到特定位置。例如，来自美国的用户期望看到美元符号，而来自欧盟的用户在货币值前使用欧元符号。除了来自不同国家的用户外，您的应用也将被不同能力的人使用。一些用户可能无法像其他用户那样使用键盘或阅读屏幕。在一个越来越依赖应用的世界里，以确保所有能力用户都能使用它们的方式开发您的应用非常重要。

在本章中，您将学习如何开发可以服务于尽可能多人的 Angular 应用。首先，我们将深入 Nx 单仓库中实现国际化。在使您的内容可翻译后，您将了解并实现本地化，以便根据用户正确显示日期、货币和其他值。最后，我们将深入研究开发可访问的前端应用的主题，这些应用可以被所有能力用户使用。

本章将涵盖以下主题：

+   在 Angular 应用中添加可翻译内容

+   Angular 应用的本地化

+   使您的 Angular 应用对所有人可访问

# 在 Angular 应用中添加可翻译内容

在本节中，我们将深入研究国际化，通常称为**i18n**（i18n 是国际化的缩写，其中“i”和“n”是单词的首尾字母，18 代表“i”和“n”之间字母的数量）。国际化是为讲不同语言的人开发可用的应用。简单来说，当您实现 i18n 时，您使您的应用内容可翻译。大约世界上 75%的人根本不说英语，所以如果您只以英语（或仅使用另一种语言）显示您的应用内容，您就会错过很多潜在用户。

Angular 框架内置了支持可翻译内容的功能，但我们将不会使用内置的 i18n 解决方案来支持可翻译内容。第一个原因是内置解决方案在翻译文件中使用了 XML 内容。大多数开发者不喜欢 XML，而 JSON 是一个更易读和更灵活的替代品。我们不使用内置 i18n 解决方案进行可翻译内容的第二个原因是它主要关注编译时翻译。在编译时翻译中，翻译键在编译过程中被替换；因此，您必须为每个您想要支持的语言创建和部署特定的构建。还有一些对运行时翻译的支持，这意味着翻译可以在应用程序部署和运行时更改，但文档很少，并且该解决方案很少被使用，因为社区开发了更好的选项并进行了维护。

在 Angular 应用程序中支持可翻译内容的两个最常用的 i18n 库是 **Transloco** 和 **ngx-translate**。我们将使用 Transloco，因为它对独立组件的支持更好，维护更活跃，文档更完善，支持服务器端渲染，并且整体上具有更多的配置选项和功能。我们将逐步将 Transloco 实施到 Nx monorepo 中的 *费用登记* *应用* 中。

您仍然可以访问此网站深入了解 Transloco 的文档：[`jsverse.github.io/transloco/`](https://jsverse.github.io/transloco/)。

## 在您的 Nx monorepo 中安装 Transloco

在您能够支持 HTML 模板和 TypeScript 文件中的可翻译内容之前，您需要在您的 Nx monorepo 中安装 Transloco 并将其添加到您想要使用的应用中。因此，让我们从开始安装 NPM 包，使用以下命令：

```js
npm i @ngneat/transloco
```

在安装包后，您需要将 Transloco 添加到您的项目中，在 `business-tools-monorepo\apps\finance\expenses-registration` 文件夹路径下打开终端，并在终端中运行以下命令：

```js
nx g @ngneat/transloco:ng-add
```

当您运行此命令时，您将在终端内被问及两个问题：

+   使用 `en` 添加英语，使用 `nl` 添加荷兰语作为我的默认语言。您可以提供自己的语言，但请添加 `en` 以确保英语，因为我们在书中会使用它。如果您愿意，您总是可以扩展这些语言。

+   **您是否在使用服务器端渲染？**：False–这是默认设置，所以您只需按 *Enter* 即可）。

在回答完问题后，命令将添加必要的配置以将 Transloco 添加到 *费用登记应用*。您也可以手动添加配置，但使用终端命令更快，并确保一切配置都添加正确。

现在，让我们更仔细地查看 Transloco 为您生成的文件，这些文件已被添加到 Nx monorepo 和 *费用登记应用* 中。

我们将要查看的第一个文件是 `transloco-loader.ts`。在这个文件中，Transloco 创建了一个 Angular 服务，该服务负责根据提供的语言加载翻译文件。以下代码是在 `transloco-loader.ts` 文件中为你生成的：

```js
@Injectable({ providedIn: 'root' })
export class TranslocoHttpLoader implements TranslocoLoader {
    private http = inject(HttpClient);
    getTranslation(lang: string) {
        return this.http.get<Translation>(`/assets/i18n/${lang}.json`);
    }
}
```

如前所述的代码所示，`TranslocoHttpLoader` 服务确实从你的 `assets` 文件夹中获取了一个 JSON 文件。获取到的 JSON 文件将包含特定语言的翻译键和值。你需要为 `getTranslation()` 函数提供一个与你的翻译文件命名相对应的语言键。例如，如果你在 `i18n` 文件夹中有一个包含你的英语翻译的 `en.json` 文件，你将使用 `en` 作为参数调用 `getTranslation()` 函数以获取英语翻译。

个人的观点，我喜欢确保 `getTranslation()` 函数的函数参数有更好的类型，并且只允许特定的字符串值而不是任何字符串。在我的情况下，我有 `en` 和 `nl` 作为我的语言文件，所以我将调整函数参数为这个：

```js
lang: 'en' | 'nl'
```

你可以调整函数参数类型以匹配你支持的语言。

重要提示

需要注意的是，当你部署你的 Angular 应用程序，并且 Transloco 无法加载你的语言文件时，可能是因为你需要提供一个相对路径，但一般来说，这种情况并不常见。

现在我们来看看如何使用带有相对路径的 `getTranslation()` 方法：

```js
getTranslation(langPath:'en' | 'nl') {
  return this.http.get(`./assets/i18n/${langPath}.json`);
}
```

在前面的代码中，你可以找到一个相对路径的示例，但你只需要提供一个相对路径，如果 Transloco 无法使用常规设置加载你的翻译。

现在你已经了解了 `transloco-loader.ts` 文件及其用途，让我们看看 Transloco 还为你创建了什么。

对于你在终端中运行命令时提供的每种语言，Transloco 都为你创建了一个翻译文件。在你的 `assets` 文件夹中，创建了一个 `i18n` 文件夹，在该 `i18n` 文件夹中，你可以找到为你提供的每种语言的 JSON 文件。所以，在我的情况下，我提供了 `en` 和 `nl`，因此 Transloco 为我创建了 `en.json` 和 `nl.json` 文件。你将添加你的翻译键和值到这些 JSON 文件中。默认情况下，这些文件包含一个空的 JSON 对象。在生成空翻译文件后，Transloco 调整了 `app.config.ts` 文件中的 `ApplicationConfig` 对象。

在你的 `ApplicationConfig` 中，Transloco 在提供者数组中添加了 `provideHttpClient()` 和 `provideTransloco()` 配置：

```js
provideHttpClient(),
provideTransloco({
  config: {
    availableLangs: [‹en›, ‹nl›],
    defaultLang: ‹en›,
    // Remove this option if your application doesn›t support changing language in runtime.
    reRenderOnLangChange: true,
    prodMode: !isDevMode(),
  },
  loader: TranslocoHttpLoader
}),
```

`provideTransloco()` 配置函数接收一个自己的配置对象，用于配置 Transloco 实例。默认情况下，Transloco 的配置定义了翻译加载器、默认语言、可用语言、是否处于生产模式以及是否支持在应用程序中运行时更改语言。此外，您还可以添加其他配置，例如备用语言、Transloco 在使用备用语言之前需要尝试加载语言文件多少次，或者是否记录丢失的翻译键。

删除生成的 provideHttpClient() 函数

Transloco 还在您的 providers 数组内部添加了 `provideHttpClient()` 函数。因为您已经在 `ApplicationConfig` 对象的 providers 数组中有一个 `provideHttpClient()` 配置，所以删除 Transloco 添加的此配置非常重要。如果您不删除 Transloco 添加的 `provideHttpClient()`，它将覆盖您自己的 `provideHttpClient()` 配置，该配置还包含您的模拟数据拦截器配置。

由于模拟数据拦截器，您还需要调整一件事。转到 `mock.interceptor.ts` 文件并调整第一个 `if` 检查。我们希望在不修改请求的情况下返回请求（就像我们在生产中已经做的那样）或如果请求 URL 以 `.json` 结尾：

```js
if (!isDevMode() || req.url.endsWith('.json')) return next(req);
```

如果您现在检查浏览器控制台的“网络”标签页，您会看到有一个请求被发送并成功获取了 `en.json` 文件。

现在您已经知道了 Transloco 生成了什么并添加到您的应用程序中，让我们继续学习如何使用 Transloco 来支持多语言内容。

## 使用 Transloco 翻译内容

要使用 Transloco 翻译值，您在翻译文件中创建键值对。您在每个翻译文件中使用相同的键，并为其提供给定语言文件的正确翻译值。键都是小写，单词之间用下划线分隔。例如，如果我想翻译 `Expenses Overview`，我会在 `en.json` 翻译文件中添加以下内容：

```js
{
  "expenses_overview": "Expenses Overview"
}
```

在其他翻译文件中，您添加相同的 `expenses_overview` 键，并为其分配特定语言的翻译值。例如，在我的 `nl.json` 文件中，我会添加相同的键并带有荷兰语的翻译：

```js
{
  "expenses_overview": "Uitgavenoverzicht"
}
```

现在您已经知道了如何向翻译文件添加翻译键和值，让我们看看如何在您的应用程序中使用这些键值对来翻译内容。您可以在 HTML 模板和 TypeScript 文件中翻译值。我们将首先查看 HTML 翻译，之后您将学习如何在 TypeScript 文件中翻译值。

### 在 HTML 模板中翻译值

在 HTML 模板中，你可以使用**结构指令**、**属性指令**或**翻译管道**来翻译值。建议使用结构指令。管道和指令都会创建订阅以观察用户何时更改选定的语言。当使用结构指令时，你只为模板创建一个订阅。相比之下，管道和属性指令每次在模板中使用时都会创建一个订阅，这可能在模板中多次发生。此外，结构指令会缓存翻译，所以如果你在模板中多次使用相同的翻译键，结构指令可以直接从缓存中返回它，从而提高你的性能和内存使用。

因为结构指令是推荐的方法，所以在查看管道和属性指令之前，让我们首先学习如何使用它。因为我们使用的是独立组件，所以在我们可以使用它之前，我们需要将其导入到组件的`imports`数组中：

```js
imports: [……, TranslocoDirective],
```

导入指令后，将其添加到支出概述页面。当使用结构指令时，建议将整个模板包裹在一个`ng-container`元素中，并在`ng-container`元素上添加结构指令：

```js
<ng-container ng-container element and add the directive on ng-container, you can use the t variable from the directive throughout the template to translate values. Let’s replace Expenses Overview text inside the h1 tag with the translatable value using the t variable from the directive and the expenses_overview translation key:

```

`<h1>{{ t variable from the directive as a function and provide it with the translation key you want to display. In the browser, you’ll see t function provided by the *transloco structural directive. But there are also many scenarios where you need to translate something and need dynamic values inside the translated value.

例如，假设你想将`h1`标签内的标题从`Expenses Overview`更改为`Expenses overview for <user name>`。在这种情况下，你需要一种方法将用户名提供给可翻译的值，以便将其插入正确的位置。你也不能仅仅追加用户名，因为你的可翻译值在每种语言中可能不同，或者你的动态值需要在翻译值的中间某个位置插入。幸运的是，有一个简单的解决方案——你可以在你的翻译值中添加参数，并在使用与翻译值关联的翻译键时提供这些参数的值。

要实现这一点，首先在翻译文件中添加另一个（或更改现有的）翻译键：

```js
"expenses_overview_for_user": "Expenses overview for {{user}}"
```

正如你所见，我们通过使用双大括号在我们的翻译值中添加了一个参数。现在，在 HTML 模板中，你可以像这样为用户翻译参数提供一个值：

```js
<h1>{{ t('expenses_overview_for_user', { user: user.fullName }) }}</h1>
```

如您所见，您可以将一个对象传递给`t`函数作为第二个参数。`t`函数的第一个参数是翻译键，第二个参数是可选的，可以用来为翻译参数提供值。在我们的例子中，我们使用一个具有`fullName`属性的`user`对象来向翻译参数提供用户名（我们当前组件中没有这个用户对象；这只是一个例子，您可以使用任何属性或静态值来提供用户翻译参数）。除了参数和简单的翻译键之外，您还可以在翻译文件中分组翻译键。

要分组翻译键，创建一个分组键，例如`expenses_overview_page`，然后在分组键下添加嵌套键，如下所示：

```js
{
  "expenses_overview_page": {
    "title": "Expenses Overview",
    "incl_vat": "Incl. VAT"
  }
}
```

尤其当您的翻译文件变得更大时，这有助于快速定位特定的翻译。使用这种方法，您可能会得到一些重复的翻译值。尽管如此，改进的可维护性比几个重复的翻译值更有意义，但您的做法取决于您和您的团队。当使用分组方法时，您在 HTML 模板中使用以下语法：

```js
<h1>{{ t('expenses_overview_page.title') }}</h1>
```

如您在前面的示例中所见，您首先定义组名，然后是一个点，然后是翻译键。正如您所想象的那样，在每个翻译键前加上组名可能会变得冗余，因此为了简化，您可以在`*transloco`指令中定义此组名：

```js
*transloco="let t; read: 'expenses_overview_page'"
```

当您在指令中定义组名时，您只需在`t`函数中使用内部翻译键：

```js
<h1>{{ t('title') }}</h1>
```

您现在知道如何声明分组或常规的翻译键值对，以及如何在 HTML 模板中使用`*transloco`结构指令来翻译它们。或者，您可以使用`transloco`属性指令或管道来完成您的翻译；然而，如前所述，属性指令是推荐的，因为它在您的 HTML 模板中创建更少的订阅，并且具有缓存功能。

为了提供一个全面的概述，以下是一个使用管道包括翻译参数的示例：

```js
<h1>{{ 'expenses_overview_for_user' | transloco: {user: user.fullname} }}</h1>
```

最后，您可以使用属性指令：

```js
<h1 transloco=" expenses_overview_for_user " transloco pipe to translate translation keys. In the last example, we showed how you can use the transloco attribute directive combined with the translocoParams attribute directive to translate translation keys with parameters. But, as mentioned earlier, using the structural directive is the preferred approach, as it improves your performance and creates fewer subscriptions.
Now you know about all the options to translate values inside your HTML templates using Transloco. Next, you’ll learn about translating values programmatically inside your TypeScript files.
Translating values programmatically in your TypeScript files
Sometimes, you might encounter a situation where you must translate values inside your component classes or services. To translate values programmatically inside your TypeScript files, you can use the `TranslocoService` class. There is only one caveat: you need to know the translation file is loaded before you can translate translation keys inside your TypeScript files.
So, let’s start by creating a `TranslationService` class in the *expenses-registration application*. Inside the `TranslationService`, we will make the `TranslocoService` class publicly available and create a signal from the `events$` observable using the `toSignal()` function:

```

@Injectable({ providedIn: 'root' })

export class TranslationService {

translocoService = inject(TranslocoService);

translationsLoaded = toSignal<boolean>(this.translocoService.events$.pipe(filter(event => event.type === 'translationLoadSuccess'), map(event => !!event)));

}

```js

 We injected the `TranslocoService` inside `TranslationService`. Alternatively, you can inject the `TranslocoService` into each component or service you need to, but I prefer to expose it in the `TranslationService`, where we also declare the `translationsLoaded` signal and possible other configurations and methods. The `translationsLoaded` signal you created will return `true` when the `events$` observable from the `TranslocoService` returns an event of type `translationLoadSuccess`. You can use this signal in combination with a signal effect to translate values inside your TypeScript files safely.
For example, if you want to translate the `expenses_overview_page.title` key inside the `ExpensesOverviewPageComponent`, you start by injecting the `TranslationService` service you created:

```

protected readonly translationService = inject(TranslationService);

```js

 After injecting the service, you can create an effect based on the `translationsLoaded` signal and perform your translations safely inside the effect:

```

translationEventsEffect = effect(() => {

if (this.translationService.translationsLoaded()) {

// 在此处执行您的翻译

}

});

```js

 As you can see in the preceding code, we declare an `effect` and check if the `translationsLoaded` signal from the service returns a `true` value; if it does, you can perform your translations safely. If you know the translation file is already loaded, for example, because the user is logged in and you fetch the translation file before the user is logged in, you don’t need the effect and can directly get the translation. You translate a value by calling the `translate()` function on the `TranslocoService` and providing the function with the translation key you want to translate:

```

this.translationService.translocoService.translate('expenses_overview_page.title')

```js

 Using the `translate()` function will return the translated value synchronously, so you can directly use or assign it however you need to. When you need to translate a key-value pair that includes translation parameters, you use the following syntax:

```

this.translationService.translocoService.translate(' expenses_overview_for_user', {user: user.fullname})

```js

 Alternatively to the `translate()` function, you can also use the `selectTranslate()` function. The `selectTranslate()` function is asynchronous and will fetch the translation file (unless it’s already loaded) and return the translation as an Observable. When using the `selectTranslate()` function, you don’t have to make sure the translation file is already loaded, but you do need to manage the subscription. I like to use the first approach, as you don’t have to create and manage subscriptions each time you want to translate a value inside your component classes, but here is an example of how you can get the translation value for a key using the `selectTranslate()` function:

```

this.translationService.translocoService.selectTranslate('expenses_overview_page.title').subscribe((title) => {

console.log(‹==>›, title);

});

```js

 You now know how to translate values synchronously and asynchronously in your TypeScript files. You also know how to translate values in the HTML template. The last thing to learn is how to change your Transloco instance’s configurations at runtime. After all, you want to give the user the ability to change the language.
Changing the Transloco configurations at runtime
The last step in making your content translatable for your users is adding the option to change the language. First, you need something so the user can select a language; I will add a select box to the navbar for this purpose. In the HTML file of the `NavbarComponent`, I’ve added the following HTML:

```

<div>

<select #selectList (change)=»languageChange.emit(selectList.value)»>

<option *ngFor=»let lang of languages()» (click)=»languageChange.emit(lang)» [value]=»lang»>{{ lang }}</option>

</select>

</div>

```js

 After adding the select box in the HTML file, in the `component` class, I’ve added an input for the `languages` array and an output to output the selected value (I also converted the `navbarItems` input to a signal input):

```

export class NavbarComponent {

navbarItems = input([], { transform: addHome });

languages = input<string[]>([]);

@Output() languageChange = new EventEmitter();

}

```js

 After updating the `NavbarComponent`, you need to provide an array of languages to the `NavbarComponent` inside the HTML template of the `AppComponent` of your `expenses-registration` application. To achieve this, we’re first going to add an additional method in our `LanguageService` to retrieve the available languages as a string array:

```

getLanguages() {

return this.translocoService.getAvailableLangs() as string[];

}

```js

 After adding the method that returns the available languages, we will use this method inside the HTML template of our `AppComponent` and use it to set the `language` input for the `NavbarComponent`:

```

<bt-libs-navbar …… [languages]="translationService.getLanguages()" />

```js

 After supplying the `languages` input with the available languages, you’ll see the options inside the select box. Next, you need to add the `languageChange` output event on the HTML tag of the `NavbarComponent` and use the output value to set a new active language:

```

<bt-libs-navbar …… (languageChange)="translationService.translocoService.setActiveLang($event)" />

```js

 Now, if you change the language in the select box inside your navbar, you’ll notice that the translatable text in your application has been changed to the selected language. That is all you need to know about adding translations in your Angular applications using Transloco. The library has other translation options, such as setting the default language, setting the fallback language, or subscribing to language changes to perform logic reactively when the active language changes.
If you want, you can dive deeper on your own by visiting t[he documentation on the official web](https://jsverse.github.io/transloco/)site: [`jsverse.github.io/transloco/`](https://jsverse.github.io/transloco/).
You now know how to support internationalization for your Angular websites using Transloco. We did all the necessary setup for Transloco and created an additional `TranslationService`. Next, we created translation files with key-value pairs used to declare all your translations. You learned how to use the Transloco directives and pipe to translate values inside your HTML templates and the `TranslocoService` to translate values and change the active language inside your TypeScript files. Now that you know how to add i18n support to your Angular applications, let’s learn about localization.
Localization for Angular applications
Where internationalization ensures your application content can be consumed in different languages, localization ensures that your application uses the correct formats and symbols for the user’s location. For example, dates are formatted differently for users in the USA and in the EU. You want to ensure that you show the correct formats for each user so your application can be used without confusion by as many people as possible. Where internationalization is commonly referred to as i18n, localization is commonly referred to as **l10n**.
Just as with i18n, Angular has its own localization packages, but the implementation of the framework isn’t widely used and requires some additional work to use at runtime. Just as we did with i18n, we can use Transloco for l10n. Transloco provides a dedicated package for l10n containing a date, currency, and decimal pipe. To start using the Transloco l10n pipes, start by installing the NPM package with the following command:

```

npm i @ngneat/transloco-locale

```js

 After installing the package in your Nx monorepo, you need to provide configurations in the `ApplicationConfig` object of the applications you want to use the l10n package. We will add it to the *expenses-registration application* by adding the following provider inside the providers array of the `ApplicationConfig` object:

```

provideTranslocoLocale({

langToLocaleMapping: { en: ‹en-US›, nl: ‹nl-NL› }

})

```js

 As you can see, you need to add the `provideTranslocoLocale()` provider function inside the providers array and supply the function with a parameter to configure the language to localization mapping. In the example, we map the `en` language to the `en-US` localization and the `nl` language to the `nl-NL` localization. By using the `langToLocaleMapping` configuration, the localization will automatically change when you set a new active language. If you don’t use the `langToLocaleMapping` setting and want your localization settings separated, you need to set the active location using the `TranslocoService`:

```

this.translocoService.setLocale('en-US');

```js

 Besides the `langToLocaleMapping` configuration, you can provide the following configurations to the `provideTranslocoLocale()` provider function:

```

export interface TranslocoLocaleConfig {

defaultLocale?: Locale;

defaultCurrency?: Currency;

localeConfig?: LocaleConfig;

langToLocaleMapping?: LangToLocaleMapping;

localeToCurrencyMapping?: LocaleToCurrencyMapping;

}

```js

 After configuring the `provideTranslocoLocale()` provider function, you can start using the Transloco i10n pipes. Let’s start with localizing currencies.
Localizing currencies using the translocoCurrency pipe
When localizing currency values inside your applications, you want to display the correct currency symbol and make sure your numbers are formatted correctly depending on the user. To localize currency values with Transloco, you import the pipe into your component (if you’re using standalone components) and change the Angular currency pipe inside your templates for the `translocoCurrency` pipe. So go ahead and change the currency pipe in your `expenses-overview-page.component.html` file and replace it with the transloco pipe:

```

<td>{{ expense.amount.value.toFixed(2) | en to nl inside the navbar (在生产环境中，后端也应返回不同的数据，因为货币之间的汇率，或者 API 可以提供汇率，以便您可以在前端执行转换)。

您可以为 `translocoCurrency` 管道提供一些额外的选项来进一步配置显示的值：

+   `display`: 此选项控制您想要显示的货币单位。您可以选择的选项有 code、symbol、narrowSymbol 和 name。

+   `numberFormatOptions`: 这是一个对象，用于控制数字的格式。您可以为该对象提供 `Intl.NumberFormatOptions` 属性（`Intl.NumberFormatOptions` 是原生的 JavaScript 格式选项）。

+   `currencyCode`: 使用此选项，您可以指定管道应使用的货币符号。

+   `locale`: 使用此选项，您可以提供区域设置选项，例如 en-US 或 nl-NL。

以下是一个包含参数的 `translocoCurrency` 管道的示例：

```js
translocoCurrency: 'narrowSymbol' : {minimumFractionDigits: 2 } : 'EUR' : 'nl-NL'
```

配置选项是可选的，并将覆盖您在 `provideTranslocoLocale()` 函数内部配置的任何默认设置。

现在您已经了解了货币管道，探索 Transloco 提供的其他本地化管道。

使用 translocoDate 管道本地化日期

在本地化日期时，您想要确保日期格式正确，这取决于用户选择的语言设置（或如果它们是分开的，本地化设置）。例如，欧盟和美国的日期格式不同。例如，在美国，日期以月份开始。在欧盟，它们以日期开始。因此，如果您想为美国用户显示 2024 年 1 月 20 日，这将表示为 1/20/2024，而对于欧盟用户，20-01-2024 将是传统的格式。此外，对于讲英语的用户，月份总是大写，而对于某些欧盟语言来说则不是这样。

为了提供最佳的用户体验，您的应用程序中的日期应正确格式化。`translocoDate` 管道提供了一种简单的方法来实现这一点。只需将管道导入到您的组件中（如果您正在使用独立组件）。类似于货币管道，您可以用 `translocoDate` 管道替换原生的 Angular 日期管道，并且当用户更改语言（或本地化）设置时，您的日期将自动响应。为了演示这一点，您可以在 `expenses-overview-page.component.html` 文件中更改您使用的日期管道：

```js
{{ expense.date | translocoDate pipe, the date format changes when you change the language settings inside the navbar. Additionally, you can provide the translocoDate pipe with some parameters to further configure how formats are displayed or to overwrite the default settings for specific instances in your application. The translocoDate pipe can take the following parameters:

*   `options`: This is an object containing the following properties:
    *   `dateStyle`: Controls how to format the date; you can use full, long, medium, and short formats.
    *   `timeStyle`: Controls how to format the time of the date. You can provide it with full, long, medium, and short.
    *   `fractionalSecondDigits`: Controls the number of fractional seconds to show; you can set it to 0, 1, 2, or 3.
    *   `dayPeriod`: Controls how to show the period of the day (at night, midday, etc.). The options are long, short, and narrow.
    *   `hour12`: Indicates whether you are using 12 or 24 hours. You can provide the `hour12` property with a `true` or `false` value.
    *   `weekday`: Controls how weeks are formatted; the options are long, short, and narrow.
    *   `era`: Controls how the era is formatted; the options are long, short, and narrow.
    *   `year`: Controls how to format the year value; the options are numeric and 2-digit.
    *   `month`: Controls how to format the month value; the options are numeric, 2-digit, long, short, and narrow.
    *   `day`: Controls how to format the day value; the options are numeric and 2-digit.
    *   `hour`: Controls how to format the hour value; the options are numeric and 2-digit.
    *   `minute`: Controls how to format the minute value; the options are numeric and 2-digit.
    *   `second`: Controls how to format the second value; the options are numeric and 2-digit.
    *   `timezone`: Controls how to display the time zone; the options are full, long, medium, and short.
*   `locale`: With this option, you can provide a locale option, such as en-US or nl-NL.

All the additional parameters you can supply to the `translocoDate` pipe are optional; if you don’t supply any additional parameters, the pipe will display dates with the numeric day, month, and year by default. Now that you know how to use the `translocoDate` pipe and what configuration options it has, let’s move on to the last Transloco pipe we will cover: the decimal pipe.
Localizing numbers using the translocoDecimal pipe
Just like currency and date values, numbers are subject to localization. For example, in the USA, large numbers are separated by a comma and decimal numbers are separated from the integer values by a dot; for EU users, this is reversed. This localization issue can be solved in your applications by using the `translocoDecimal` pipe. It will format numbers correctly depending on the language (or localization) settings configured by the user:

```

<!--1,234,567,890  en-US -->

<span> {{ 1234567890 | en-US, 大数以逗号分隔。如果您将其作为小数，小数值将以点分隔：

```js
<!--567,890.15  en-US -->
<span> {{ 567890.15 | translocoDecimal }}</span>
```

就像 `translocoDate` 和 `translocoCurrency` 管道一样，您为 `translocoDecimal` 管道有一些额外的配置选项。`translocoDecimal` 管道可以接受以下参数：

+   `numberFormatOptions`: 这是一个包含 `Intl.NumberFormatOptions` 格式化属性的对象（`Intl.NumberFormatOptions` 是一个原生的 JavaScript 格式化对象）

+   `locale`: 使用此选项，您可以提供区域设置选项，例如 en-US 或 nl-NL

您现在已经学会了如何使用 Transloco 库的不同本地化管道，以及您可以提供哪些配置选项来控制输出。在下一节中，您将学习如何使您的 Angular 应用程序对所有人可访问。

使您的 Angular 应用程序对所有人可访问

在一个越来越依赖网络应用的世界里，确保每个人都能使用您的应用程序至关重要。确保您的 Angular 应用程序对有运动或视觉障碍的人可访问是确保所有能力用户都能有效与之互动的关键。**可访问性**，通常缩写为 **a11y**，涉及设计和开发您的应用程序，使其能够被具有不同需求的人使用，包括那些有残疾的人。

作为一名开发者，我经常忘记并不是每个人都能像我能一样使用键盘或看到屏幕。不仅包括永久性残疾的人，还包括那些暂时无法使用他们的手或视力的人也应该能够继续与你的应用程序互动。在一些国家，法律甚至强制要求你的应用程序实施特定的可访问性标准。一个常用的可访问性标准是**Web 内容可访问性指南 2.2（WCAG 2.2）**。WCAG 2.2 有 13 条准则需要遵循：

1.  **文本替代**：为非文本内容提供文本替代方案，使其对无法看到图像或听到音频的用户可访问。

1.  **基于时间的媒体**：为基于时间的媒体提供替代方案，如音频和视频，以确保无法听到或看到多媒体内容的用户能够访问。

1.  **适应性**：创建可以在不同方式下呈现而不丢失信息或结构的内容，确保依赖于各种辅助技术的用户能够访问。

1.  **可区分性**：确保足够的对比度，提供音频内容的替代方案，并避免可能阻碍访问的干扰，使用户更容易看到和听到内容。

1.  **键盘可访问**：确保所有功能都可通过键盘界面访问，确保无法使用鼠标的用户能够访问。

1.  **充足时间**：为用户提供足够的时间阅读和使用内容，确保那些可能需要更多时间与网页互动的用户能够访问。

1.  **癫痫和身体反应**：不要设计可能导致癫痫发作或身体反应的内容，确保对患有光敏感性癫痫或其他状况的用户可访问。

1.  **可导航性**：使网页可导航且可预测，确保依赖于导航辅助工具或具有认知障碍的用户能够访问。

1.  **输入模式**：确保与不同的输入模式兼容，如触摸、语音和手势，确保那些影响他们与网页内容互动的残疾用户能够访问。

1.  **设备独立性**：确保与各种设备、平台和辅助技术兼容，确保使用不同设备访问网络的用户能够访问。

1.  **可读性**：使文本内容易于阅读和理解，确保认知障碍或使用屏幕阅读器的用户能够访问。

1.  **可预测性**：使网页以可预测的方式运行，确保依赖于一致导航和交互模式的用户能够访问。

1.  **输入辅助**：帮助用户避免和纠正错误，确保信息输入或导航网页表单可能存在困难的用户能够访问。

现在你已经了解了 WCAG 2.2 的 13 条准则，让我们来看看在你的 Angular 应用程序中你可以做些什么来遵守这些准则。

如何使 Angular 应用程序可访问

遵循可访问性指南的最简单方法是使用一个 UI 库，该库将这些关注点从你的手中拿走。一些好的 Angular UI 库包括 Angular Material、PrimeNG 和 Ng Zorro。然而，并非所有应用程序代码都可以使用 UI 库开发，有时你的雇主会开发自己的 UI 组件；在这些情况下，你需要自己应用 WCAG。为了确保你遵循 WCAG，你需要做的第一件事是使用语义化 HTML。

语义化 HTML 是指使用适当的 HTML 元素来可视化屏幕上的元素。通常，开发者喜欢使用过多的`<div>`和`<span>`元素；这些元素并没有告诉屏幕阅读器任何关于它们用途的信息。尽量使用如`<label>`、`<button>`、`<input>`、`<header>`、`<footer>`、`<section>`、`<article>`、`<form>`等 HTML 元素。当使用语义化 HTML 元素时，屏幕阅读器可以更好地向无法看到页面的用户解释页面内容。除了实现语义化 HTML 之外，你还应该确保用户可以使用键盘导航页面。

使用`tabindex`属性

你可以通过添加`tabindex`属性来确保 HTML 元素可以通过键盘上的 tab 键聚焦。这种默认聚焦行为适用于以下元素：具有`href`属性的`<a>`或`<area>`、`<button>`、`<iframe>`、`<input>`、`<object>`、`<select>`、`<textarea>`、`<SVG>`和为`<details>`元素提供摘要的`<summary>`元素。开发者不需要手动添加`tabindex`属性到这些元素，除非他们想要改变它们的默认聚焦行为。

例如，设置负的`tabindex`值将移除元素从焦点导航顺序，从而使其无法聚焦。然而，在修改可聚焦元素的默认行为时，必须谨慎行事，以确保直观且易于访问的用户体验。

使用`tabindex`属性，你可以使 HTML 元素可键盘聚焦，防止元素被键盘聚焦，并确定聚焦顺序。如前所述，当你为`tabindex`属性提供一个负整数时，HTML 元素在使用键盘时将不可聚焦。如果你为`tabindex`属性提供一个`0`，则元素保持默认的 tab 顺序（从上到下，根据 HTML 元素的顺序）。最后，你可以提供正整数。具有正整数的元素将在默认聚焦顺序之前聚焦，从`tabindex 1`开始，到最高的`tabindex`结束。当没有更多的具有正整数的`tabindex`属性时，下一个聚焦的元素是第一个具有默认`tabindex`属性的 HTML 元素：

```js
<button type="button">1st default focus</button>
<button type="button">2nd default focus</button>
<button type="button" tabindex="100">Second focused</button>
<div tabindex="0">3rd default focus</div>
<button type="button" tabindex. The first element that will be focused is the button on the bottom with tabindex 1; after that, the button with tabindex 100 will be focused, and after that, elements will be focused from top to bottom (skipping the two elements we already focused because they have a positive tabindex attribute).
Now that you know how to control the keyboard focus of HTML elements and when to use the `tabindex` attribute, let’s learn about ARIA attributes.
Adding ARIA attributes
**Accessible Rich Internet Applications** (**ARIA**) attributes are used to provide or add semantic meaning to HTML elements. In Angular, you can provide static or dynamic values for your ARIA attributes. Dynamic attributes might be useful if you want your ARIA attributes to be translatable or when they change for specific HTML elements based on some user interaction:

```

`<button [attr.aria-label]="dynamicValue">…</button>`

`<button [attr.aria-label]="'translationkey' | translate">…</button>`

`<button aria-label="static value">…</button>`

```js

 In the preceding code, you see the three options:

*   First, we provide a dynamic value to the `aria-label`. In the first example, `dynamicValue` is a component property that can have different values over time.
*   Then, you see how you can use a translated value for the `aria-label`.
*   Lastly, we provided a static value for the `aria-label`.

There are many different ARIA attributes; let us take a look at the few most commonly used ones.
The aria-label
The `aria-label` attribute is by far the most used ARAI attribute. The `aria-label` attribute can be used to provide a text explanation for visual elements that don’t have any text or to provide a more descriptive explanation of an element that does include text. For example, if you have a button HTML element that is styled as a hamburger menu, the button will not include any text. Without the `aria-label` attribute, a screen reader can’t make anything of this, so you need to provide the `aria-label` attribute with an explanation:

```

<button aria-label 属性，并为其提供了文本“汉堡菜单”，这样屏幕阅读器可以清楚地解释该元素的内容。或者，你也可以使用 aria-label 属性为包含文本的元素提供更好的解释：

```js
<button Submit text. By default, a screen reader will read the text of the HTML element, but only reading out Submit might not give the user enough information about what they are submitting, especially if you can’t see the screen. Adding an aria-label attribute can provide a better explanation for the screen reader to read aloud to the user.
The role attribute
Another important and commonly used ARIA attribute is the `role` attribute. The `role` attribute in HTML defines an element’s specific role or purpose within the web page or application. It can be added to various HTML elements to convey their intended purpose to assistive technologies, such as screen readers, which rely on this information to provide a meaningful experience to users with disabilities.
The `role` attribute can be added for most HTML elements, including but not limited to the following:

*   Semantic elements such as `<nav>`, `<article>`, `<section>`, `<header>`, `<footer>`, and `<main>`.
*   Form elements such as `<input>`, `<button>`, `<select>`, and `<textarea>`.
*   Interactive elements such as `<a>` (anchor links), `<button>`, and `<option>` (within `<select>`).

The following is an example of the `role` attribute:

```

<a href="#" <a> 标签，指定 HTML 元素的 role 为按钮。通常，<a>标签被用作链接，但在这个情况下，我们表明它被用作按钮。

更多信息

除了`role`和`aria-label`属性之外，还有许多其他的 ARIA 属性，例如`aria-hidden`、`aria-checked`、`aria-disabled`和`aria-readonly`。

你可以在以下 URL 找到所有 ARIA 属性的完整列表和详细解释：

[`developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Attributes`](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Attributes)。

现在你已经了解了不同的无障碍性指南以及如何将它们应用到你的 Angular 应用程序中。你学习了为什么为你的页面使用语义化的 HTML 结构很重要，这样屏幕阅读器等辅助工具可以更好地导航和理解你的组件和页面。你学习了如何使用 Tab 键使元素可聚焦，以及如何控制页面中不同元素的聚焦顺序。最后，你学习了 ARIA 属性以及它们如何被用来为辅助技术，如屏幕阅读器，提供额外信息。

摘要

在本章中，你学习了如何让你的应用程序对讲不同语言或位于不同地区的人更加无障碍。你了解了 Transloco 库以及如何用它来实现本地化和国际化。你创建了语言文件以提供翻译键值对，并在你的 HTML 模板中实现了翻译。你学习了如何使用结构指令、属性指令、管道和`TranslocoService`来翻译值。在了解了可翻译内容之后，你学习了如何为来自不同地区的人格式化值。你学习了`translocoCurrency`、`translocoDate`和`translocoDecimal`管道。你看到了如何为本地化配置你的应用程序，以及如何在代码中特定实例中覆盖默认设置。在国际化并本地化你的网站之后，你学习了无障碍性。你熟悉了 WCAG，并了解了如何在 Angular 应用程序中确保它们得到实施。

在下一章中，你将学习如何为你的 Angular 应用程序编写单元测试和端到端测试。

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
