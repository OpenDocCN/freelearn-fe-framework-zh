为 Nuxt 创建一个与框架无关的 PHP API

在之前的章节中，比如第八章《添加服务器端框架》和第九章《添加服务器端数据库》，你学习了如何使用 Nuxt 的默认服务器与 Node.js JavaScript 框架（如 Koa 和 Express）创建 API。在第十二章《创建用户登录和 API 身份验证》中，你学习了如何使用相同的 Node.js JavaScript 框架 Koa 在外部服务器上创建 API。

在这一章中，我们将指导你如何使用 PHP（超文本预处理器）在外部服务器上创建 API。在第九章《添加服务器端数据库》中，你还学习了如何使用 MongoDB 来管理数据库。然而，在这一章中，我们将使用 MySQL，而你在第十二章《创建用户登录和 API 身份验证》中使用了 Koa。

在这一章中，最重要的是，你将学习关于 PHP 标准和 PHP 标准建议（PSRs）的所有知识。特别是，你将学习关于 PSR-4 用于自动加载，PSR-7 用于 HTTP 消息，以及 PSR-15 用于组合中间件组件和处理 HTTP 服务器请求。我们将整合来自不同供应商（如 Zend Framework 和 The PHP League）基于这些 PSR 标准的包，为我们的 Nuxt 应用创建一个与框架无关的 PHP RESTful API。

在这一章中，我们将涵盖以下主题：

+   介绍 PHP

+   理解 HTTP 消息和 PHP 标准

+   使用 PHP 数据库框架编写 CRUD 操作

+   与 Nuxt 集成

让我们开始吧！

# 第十六章：介绍 PHP

PHP 已经走过了很长的路。它早在 Node.js 之前就存在了，由 Rasmus Lerdorf 于 1994 年创建。最初它代表的是“个人主页”。PHP 的参考实现现在由 PHP 组织（https://www.php.net/）生产。PHP 最初是作为一个模板语言开发的，允许我们将 HTML 与 PHP 代码本身混合在一起，就像 Twig（https://twig.symfony.com/）和 Pug（https://pugjs.org/）现在所做的那样。

现在，PHP 不仅仅是一个模板语言。多年来，它已经发展成为一种通用脚本语言和面向对象语言，特别适用于服务器端 Web 开发。您仍然可以用它来制作模板，但在现代 PHP 开发中，我们应该充分利用它的全部功能。如果您想了解 PHP 还能做什么，请访问[`www.php.net/manual/en/intro-whatcando.php`](https://www.php.net/manual/en/intro-whatcando.php)。

在撰写本书时，PHP 的当前稳定版本是 7.4.x。如果您刚开始使用 PHP，请从 PHP 7.4 开始。如果您正在使用 PHP 7.2 或 7.3，您应该考虑将其升级到 PHP 7.4，因为它包含了几个错误修复。有关此版本更改的更多信息，请访问[`www.php.net/ChangeLog-7.php`](https://www.php.net/ChangeLog-7.php)。

在本书中，我们将指导您如何在支持 Apache2 的 Ubuntu 上安装或升级到 PHP 7.4。让我们开始吧！

## 安装或升级 PHP

如果您使用的是 macOS，请使用此指南：[`phptherightway.com/mac_setup`](https://phptherightway.com/#mac_setup)。如果您使用的是 Windows，请使用此指南：[`phptherightway.com/windows_setup`](https://phptherightway.com/#windows_setup)。

我们正在使用 Apache2 HTTP 服务器，但如果您的计算机上已安装了 Nginx HTTP 服务器，也可以使用它。现在，按照以下简单步骤安装 PHP：

1.  运行以下命令更新 Ubuntu 服务器上的本地软件包并安装 Apache2：

```js
$ sudo apt update
$ sudo apt install apache2
```

1.  安装 Apache2 后，使用`-v`选项进行验证：

```js
$ apache2 -v
Server version: Apache/2.4.41 (Ubuntu)
Server built: 2019-08-14T14:36:32
```

您可以使用以下命令停止、启动和启用 Apache2 服务，以便在服务器启动时始终启动：

```js
$ sudo systemctl stop apache2
$ sudo systemctl start apache2
$ sudo systemctl enable apache2
```

您可以使用以下命令检查 Apache2 的状态：

```js
$ sudo systemctl status apache2
```

您应该始终在终端中获得`active (running)`的输出：

```js
apache2.service - The Apache HTTP Server
 Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor preset: enabled)
 Active: active (running) since Thu 2020-08-06 13:17:25 CEST; 52min ago
 //...
```

1.  运行以下命令安装 PHP 7.4：

```js
$ sudo apt update
$ sudo apt install php
```

1.  您还应该安装与 PHP 7.4 相关的可能在开发 PHP 应用程序时需要的模块和扩展：

```js
$ sudo apt install -y php7.4-{bcmath,bz2,curl,gd,intl,json,mbstring,xml,zip,mysql}
```

1.  禁用 PHP 7.3（如果您使用的是 PHP 7.3），然后启用 PHP 7.4：

```js
$ sudo a2dismod php7.3
$ sudo a2enmod php7.4
```

如果您是第一次安装 PHP，则无需禁用旧版本。如果您想卸载 PHP 及其所有相关模块，可以使用以下命令：

```js
$ sudo apt-get purge 'php*'
```

1.  重新启动 Apache2 和 PHP 服务：

```js
$ sudo service apache2 restart
```

1.  现在，您可以使用以下命令验证刚刚安装的 PHP：

```js
$ php -v
```

您应该获得以下版本信息：

```js
PHP 7.4.8 (cli) (built: Jul 13 2020 16:46:22) ( NTS )
Copyright (c) The PHP Group
Zend Engine v3.4.0, Copyright (c) Zend Technologies
 with Zend OPcache v7.4.8, Copyright (c), by Zend Technologies
```

现在您已经安装了 Apache2 和 PHP 7.4，接下来应该做的是配置 PHP。我们将在下一节中进行。

## 配置 PHP 7.4

现在 Apache2 和 PHP 已安装，您可能希望配置 PHP，以便根据您的 PHP 应用程序的需要使用它。默认的 PHP 配置文件位于`/etc/php/7.4/apache2/php.ini`，因此请按照以下步骤配置您的 PHP 7.4 版本：

1.  运行以下命令以编辑或配置 PHP 7.4：

```js
$ sudo nano /etc/php/7.4/apache2/php.ini
```

您可能需要更改已上传文件的`upload_max_filesize`的默认允许量：

```js
upload_max_filesize = 2M
```

您可以在[`php.net/upload-max-filesize`](http://php.net/upload-max-filesize)找到有关此配置的更多信息。

对于 PHP 应用程序来说，上传文件的最大限制为 2 MB 可能被认为是很小的。因此，请根据您的需求进行更改，如下所示：

```js
upload_max_filesize = 32M
```

以下是一些其他重要的行/PHP 指令需要考虑：

```js
post_max_size = 48M
memory_limit = 256M
max_execution_time = 600
```

您可以在[`www.php.net/manual/en/ini.core.php`](https://www.php.net/manual/en/ini.core.php)找到有关上述 PHP 指令和其他配置 PHP 的指令的更多信息。

1.  重启 Apache 以使上述修改的 PHP 设置生效：

```js
$ sudo service apache2 restart
```

PHP 7.4 非常强大。如果您不想在本地开发机器上安装 Apache，您可以只安装它并用于开发站点。在下一节中，您将学习如何在没有 Apache 服务器的情况下使用 PHP 7.4。

## 使用内置的 PHP Web 服务器运行 PHP 应用程序

自 PHP 5.4 以来，您可以使用内置的 PHP Web 服务器运行 PHP 脚本和应用程序，而无需像 Apache 或 Nginx 这样的常见 Web 服务器。只要安装了 PHP 7.4，您就可以跳过上述 Apache 安装。要启动 PHP 服务器，只需从项目的根目录打开终端并运行以下命令：

```js
$ php -S 0.0.0.0:8181
```

如果您想从特定的文档根目录开始应用程序，例如从名为`public`的项目目录中的`www`目录开始，请执行以下操作：

```js
$ cd ~/www
$ php -S localhost:8181 -t public
```

让我们创建一个经典的“Hello World”示例，这个内置的 PHP Web 服务器将提供，以查看是否一切设置正确：

1.  创建一个简单的 PHP 文件中的“Hello World”消息页面，如下所示：

```js
// public/index.php
<?php
echo 'Hello world!';
```

1.  转到您的项目目录，并使用上述命令启动内置的 PHP Web 服务器。终端应显示以下信息：

```js
[Sun Mar 22 09:12:37 2020] PHP 7.4.4 Development Server (http://localhost:8181) started
```

1.  现在，在浏览器上加载`localhost:8181`。您应该在屏幕上看到 Hello world!，没有任何错误。

如果您想了解这个内置的 Web 服务器，请访问[`www.php.net/features.commandline.webserver`](https://www.php.net/features.commandline.webserver)。

接下来，您将学习如何使用一些 PHP 标准。您还将了解 HTTP 消息是什么，以及为什么我们需要为现代 PHP 应用程序使用 PSR。

# 理解 HTTP 消息和 PSR

**超文本传输协议**（**HTTP**）是客户端计算机和 Web 服务器之间的通信协议。诸如 Chrome、Safari 或 Firefox 之类的网络浏览器可以是 Web 客户端或用户代理，而计算机上监听某个端口的 Web 应用程序可以是 Web 服务器。Web 客户端不仅仅是浏览器，还包括任何可以与 Web 服务器通信的应用程序，比如 cURL 或 Telnet。

客户端通过互联网打开连接，向服务器发出请求，并等待直到收到服务器的响应。请求包含请求信息，而响应包含状态信息和请求的内容。这两种交换的数据称为 HTTP 消息。它们只是用 ASCII 编码的文本体，并且跨越多行，具有以下结构：

```js
Start-line
HTTP Headers

Body
```

这看起来非常简单和直接，不是吗？尽管可能是这样，让我们详细说明一下这个结构：

+   `Start-line`描述了实现的请求方法（例如`GET`、`PUT`或`POST`）、请求目标（通常是 URI）和响应的 HTTP 版本或状态（例如 200、404 或 500）以及 HTTP 版本。`Start-line`始终是单行。

+   `HTTP Headers`行描述了请求或响应的特定细节（元信息），例如`Host`、`User-Agent`、`Server`、`Content-type`等。

+   空白行表示请求的所有元信息已经发送。

+   `Body`（或消息体）包含请求的交换数据（例如 HTML 表单的内容）或响应的内容（例如 HTML 文档的内容）。消息体是可选的（有时，在请求中不需要它来请求服务器的数据）。

现在，让我们使用 cURL 来看看 HTTP 请求和响应的数据是如何交换的：

1.  使用内置的 PHP Web 服务器在`localhost:8181`上提供您在上一节中学到的 PHP“Hello World”应用程序：

```js
$ php -S localhost:8181 -t public
```

1.  在您的终端上打开一个新标签，并运行以下 cURL 脚本：

```js
$ curl http://0.0.0.0:8181 \
 --trace-ascii \
 /dev/stdout
```

您应该看到请求消息显示在第一部分中，如下所示：

```js
== Info: Trying 0.0.0.0:8181...
== Info: TCP_NODELAY set
== Info: Connected to 0.0.0.0 (127.0.0.1) port 8181 (0)
=> Send header, 76 bytes (0x4c)
0000: GET / HTTP/1.1
0010: Host: 0.0.0.0:8181
0024: User-Agent: curl/7.65.3
003d: Accept: /
004a:
```

在这里，您可以看到空行表示为`004a:`，请求中根本没有消息正文。响应消息显示在第二部分中，如下所示：

```js
== Info: Mark bundle as not supporting multiuse
<= Recv header, 17 bytes (0x11)
0000: HTTP/1.1 200 OK
<= Recv header, 20 bytes (0x14)
0000: Host: 0.0.0.0:8181
<= Recv header, 37 bytes (0x25)
0000: Date: Sat, 21 Mar 2020 20:33:09 GMT
<= Recv header, 19 bytes (0x13)
0000: Connection: close
<= Recv header, 25 bytes (0x19)
0000: X-Powered-By: PHP/7.4.4
<= Recv header, 40 bytes (0x28)
0000: Content-type: text/html; charset=UTF-8
<= Recv header, 2 bytes (0x2)
0000:
<= Recv data, 12 bytes (0xc)
0000: Hello world!
== Info: Closing connection 0
```

在响应的起始行中，您可以看到状态是`200 OK`。但在前面的示例中，我们没有发送任何数据，因此请求消息中没有消息正文。让我们创建另一个非常基本的 PHP 脚本，如下所示：

1.  创建一个带有 PHP `print`函数的 PHP 页面，以便显示`POST`数据，如下所示：

```js
// public/index.php
<?php
print_r($_POST);
```

1.  使用内置的 PHP Web 服务器在`localhost:8181`上提供页面：

```js
$ php -S localhost:8181 -t public
```

1.  在终端上使用 cURL 发送一些数据：

```js
$ curl http://0.0.0.0:8181 \
 -d "param1=value1&param2=value2" \
 --trace-ascii \
 /dev/stdout
```

这次，请求消息将显示在第一部分中，以及消息正文：

```js
== Info: Trying 0.0.0.0:8181...
== Info: TCP_NODELAY set
== Info: Connected to 0.0.0.0 (127.0.0.1) port 8181 (0)
=> Send header, 146 bytes (0x92)
0000: POST / HTTP/1.1
0011: Host: 0.0.0.0:8181
0025: User-Agent: curl/7.65.3
003e: Accept: /
004b: Content-Length: 27
005f: Content-Type: application/x-www-form-urlencoded
0090:
=> Send data, 27 bytes (0x1b)
0000: param1=value1&param2=value2
== Info: upload completely sent off: 27 out of 27 bytes

```

响应消息显示在第二部分中，如下所示：

```js
== Info: Mark bundle as not supporting multiuse
<= Recv header, 17 bytes (0x11)
0000: HTTP/1.1 200 OK
<= Recv header, 20 bytes (0x14)
0000: Host: 0.0.0.0:8181
<= Recv header, 37 bytes (0x25)
0000: Date: Sat, 21 Mar 2020 20:43:06 GMT
<= Recv header, 19 bytes (0x13)
0000: Connection: close
<= Recv header, 25 bytes (0x19)
0000: X-Powered-By: PHP/7.4.4
<= Recv header, 40 bytes (0x28)
0000: Content-type: text/html; charset=UTF-8
<= Recv header, 2 bytes (0x2)
0000:
<= Recv data, 56 bytes (0x38)
0000: Array.(. [param1] => value1\. [param2] => value2.).
Array
(
    [param1] => value1
    [param2] => value2
)
== Info: Closing connection 0
```

1.  在这里，您还可以在终端上使用 cURL 查看`PUT`方法的请求消息和请求消息：

```js
$ curl -X PUT http://0.0.0.0:8181 \
 -d "param1=value1&param2=value2" \
 --trace-ascii \
 /dev/stdout
```

1.  对于 cURL 上的`DELETE`方法也是如此，如下所示：

```js
$ curl -X DELETE http://0.0.0.0:8181 \
 -d "param1=value1&param2=value2" \
 --trace-ascii \
 /dev/stdout
```

1.  最后但同样重要的是，我们还可以使用 Google Chrome 中的开发者工具来帮助我们检查交换的数据。让我们创建另一个简单的 PHP 脚本，它将从 URI 接收数据：

```js
// public/index.php
<?php
print_r($_GET);
```

1.  通过使用`0.0.0.0:8181/?param1=value1&param2=value2`在浏览器上发送一些数据。通过这样做，数据将作为`param1=value1&param2=value2`发送，如下截图所示：

![](img/e4fb51b8-fde2-4b80-b57d-c433f69124cf.png)

如果您想了解更多关于 HTTP 和 HTTP 消息的信息，请访问[`developer.mozilla.org/en-US/docs/Web/HTTP`](https://developer.mozilla.org/en-US/docs/Web/HTTP)了解 HTTP 的一般信息，以及[`developer.mozilla.org/en-US/docs/Web/HTTP/Messages`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Messages)了解特定的 HTTP 消息。

在涉及服务器端开发时，HTTP 消息最好封装在对象中，以便更容易处理。例如，Node.js 具有内置的 HTTP 模块（[`nodejs.dev/the-nodejs-http-module`](https://nodejs.dev/the-nodejs-http-module)）用于 HTTP 通信，在其中，您可以从`http.createServer()`方法的回调中获取 HTTP 消息对象，用于创建 HTTP 服务器：

```js
const http = require('http')

http.createServer((request, response) => {
  response.writeHead(200, {'Content-Type': 'text/plain'})
  response.end('Hello World')
}).listen(8080)
```

如果您正在使用 Node.js 框架，例如 Koa，您可以在`ctx`中找到 HTTP 消息对象，如下所示：

```js
const Koa = require('koa')
const app = new Koa()

app.use(async ctx => {
  ctx
  ctx.request
  ctx.response
})
```

在前面的代码中，`ctx`是 Koa 上下文，而`ctx.request`是 HTTP 请求消息，`ctx.response`是 HTTP 响应消息。在 Express 中也是一样的；您可以按如下方式找到 HTTP 消息：

```js
const express = require('express')
const app = express()

app.get('/', (req, res) => res.send('Hello World!'))
```

与 Node.js 不同，PHP 从来没有内置的 HTTP 消息对象。有很多方法可以手动和直接获取和设置 Web 数据，就像我们在之前的 PHP 示例中看到的那样，通过使用超全局变量（`$_GET`、`$_POST`）和内置函数（`echo`、`print_r`）。如果您想捕获传入的请求，您可以根据情况使用`$_GET`、`$_POST`、`$_FILE`、`$_COOKIE`、`$_SESSION`或其他超全局变量（[`www.php.net/manual/en/language.variables.superglobals.php`](https://www.php.net/manual/en/language.variables.superglobals.php)）。

返回响应也是一样的：您可以使用全局函数，如`echo`、`print`和`header`手动设置响应头。过去，PHP 开发人员和框架有他们自己实现 HTTP 消息的方式。这导致了不同框架有不同的抽象来**表示**HTTP 消息的时代，任何基于特定实现的 HTTP 消息的应用程序几乎无法在项目中与其他框架一起使用。这种行业标准的缺乏使得框架的组件紧密耦合。如果您没有从框架开始，最终您会自己构建一个框架。

但是今天，PHP 社区已经学习并执行了 PHP 标准和建议。您不必完全遵守这些标准和建议；如果您有哲学原因要求您这样做，您可以忽略它们。但它们是一个良好意图的衡量标准，至少在商业和合作方面结束 PHP 战争。最重要的是，PHP 开发人员可以专注于 PHP 标准，而不是以框架无关的方式。当我们谈论 PHP 标准时，我们倾向于指的是 PSR，这是由 PHP 框架互操作性组（PHP-FIG）定义和发布的 PHP 规范。PSR-7：HTTP 消息接口是 PHP-FIG 成员提出的规范之一，并根据他们同意的既定协议进行了投票。

PSR-7 于 2015 年 5 月正式被接受。它基本上用于标准化 HTTP 消息接口。在深入研究 PSR-7 之前，我们还应该了解一些其他 PSR 编号，特别是 PSR-12（取代 PSR-2），PSR-4 和 PSR-15。我们将在本书中为您介绍它们，以便您可以编写可重用的，与框架无关的应用程序和组件，这些应用程序和组件可以独立使用，也可以与其他框架进行互操作，无论它们是全栈还是微框架。让我们开始吧！

## 为什么要使用 PSR？

在内部，PHP 从不告诉开发人员他们应该如何编写他们的 PHP 代码。例如，Python 使用缩进来指示一块代码，而对于其他编程语言，如 PHP 和 JavaScript，代码中的缩进是为了可读性。以下是 Python 将接受的示例：

```js
age = 20
if age == 20:
  print("age is 20")
```

如果没有缩进，Python 将返回错误：

```js
if age == 20:
print("age is 20")
```

空格的数量取决于编码者的偏好，但您必须至少使用一个空格，并且在同一块中的其他行中使用相同数量的空格；否则，Python 将返回错误：

```js
if age == 20:
 print("age is 20")
  print("age is 20")
```

另一方面，在 PHP 中，您可以编写以下内容：

```js
if (age == 20) {
print("age is 20");
}
```

PHP 中也可以使用以下内容：

```js
if (age == 20) {
 print("age is 20");
  print("age is 20");
}
```

Python 在内部强制执行代码的可读性和整洁性。PHP 没有。您可以想象，如果没有一些基本的强制措施，并且根据编码者的经验，PHP 代码可能会变得非常混乱，丑陋和难以阅读。也许 PHP Web 开发的低门槛在其中起了作用。因此，您的 PHP 代码必须遵循通用的代码风格，以便于协作和维护。

有一些特定框架的 PHP 编码标准，但它们基本上是基于（或类似于）PSR 标准的：

+   Zend 编码标准在[`framework.zend.com/manual/2.4/en/ref/coding.standard.html`](https://framework.zend.com/manual/2.4/en/ref/coding.standard.html)

+   Symfony 编码标准在[`symfony.com/doc/master/contributing/code/standards.html`](https://symfony.com/doc/master/contributing/code/standards.html)

+   CakePHP 编码标准在[`book.cakephp.org/3.0/en/contributing/cakephp-coding-conventions.html`](https://book.cakephp.org/3.0/en/contributing/cakephp-coding-conventions.html)

+   FuelPHP 编码标准在[`fuelphp.com/docs/general/coding_standards.html`](https://fuelphp.com/docs/general/coding_standards.html)

+   WordPress 编码标准在[`codex.wordpress.org/WordPress_Coding_Standards`](https://codex.wordpress.org/WordPress_Coding_Standards)

从实用的角度来看，您的代码应该遵循您所依赖的框架，以及特定的框架。但是，如果您只是从框架中使用一些组件或库，那么您可以遵守任何组合的 PSR，或者由 PEAR 制定的编码标准。PEAR 编码标准可以在[`pear.php.net/manual/en/standards.php`](https://pear.php.net/manual/en/standards.php)找到。

本书侧重于各种 PSR，因为本章旨在创建与框架无关的 PHP 应用程序。您不必同意 PSR，但如果您正在寻找一个标准来开始项目，并且在您的组织内没有自己的标准，那么这可能是一个很好的开始。您可以在[`www.php-fig.org/psr/`](https://www.php-fig.org/psr/)找到更多关于 PSR 的信息。

除了我们在这里提到的内容之外，您还应该查看[`phptherightway.com/`](https://phptherightway.com/)的**PHP: The Right Way**。它概述了现代 PHP 编码人员可以用作参考的事项，从设置 PHP，使用**Composer**进行依赖管理（我们将在本章后面介绍），编码风格指南（其中推荐使用 PSR），依赖注入，数据库，模板化，测试框架等等。对于想要避免过去错误并在网络上找到权威 PHP 教程链接的新 PHP 编码人员来说，这是一个很好的开始。对于需要快速参考和来自 PHP 社区的更新的经验丰富的 PHP 编码人员来说，这也是一个很好的地方，或者是他们在过去几年中可能错过的任何内容。

现在，让我们开始研究 PSR，从**PSR-12**开始。

## PSR-12 - 扩展编码风格指南

PSR-12 是 PSR-2 的修订编码风格指南，考虑了 PHP 7。PSR-12 规范于 2019 年 8 月 9 日获得批准。自 2012 年接受 PSR-2 以来，PHP 已经进行了许多更改，对编码风格指南产生了一些影响，其中最显着的是**返回类型声明**，这是在 PHP 7 中引入的，而在 PSR-2 中没有描述。因此，应该定义一个标准来使用它们，以便它们可以被更广泛的 PHP 社区采用，然后再由个别的 PHP 编码人员实施他们的标准，这可能最终会发生冲突。

例如，PHP 7 中添加的**返回类型声明**简单地指定了函数应该返回的值的类型。让我们看一下以下采用**返回类型声明**的函数：

```js
declare(strict_types = 1);

function returnInt(int $value): int
{
    return $value;
}

print(returnInt(2));
```

您将得到`2`作为整数的正确结果。但是，让我们看看如果您改变`returnInt`函数内的代码会发生什么，如下所示：

```js
function returnInt(int $value): int
{
    return $value + 1.0;
}
```

PHP 将放弃以下错误：

```js
PHP Fatal error: Uncaught TypeError: Return value of returnInt() must be of the type int, float returned in ...
```

因此，为了满足 PHP 7 的这一新功能的需求，PSR-12 要求您在冒号后使用一个空格，后面是带有返回类型声明的方法的类型声明。此外，冒号和声明必须与参数列表的右括号在同一行，两个字符之间没有空格。让我们看一个简单的例子，其中有一个`return`类型声明：

```js
class Fruit
{
    public function setName(int $arg1, $arg2): string
    {
        return 'kiwi';
    }
}
```

在 PSR-2 和 PSR-12 中有一些规则保持不变。例如，在这两个 PSR 中，您不能使用制表符进行缩进，而是使用四个单个空格。但是在 PSR-2 中关于块列表的规则已经修订。现在，在 PSR-12 中，使用语句导入类、函数和常量的块必须用单个空行分隔，即使只有一个导入它们的地方。让我们快速看一下符合此规则的一些代码：

```js
<?php

/**
 * The block of comments...
 */

declare(strict_types=1);

namespace VendorName\PackageName;

use VendorName\PackageName\{ClassX as X, ClassY, ClassZ as Z};
use VendorName\PackageName\SomeNamespace\ClassW as W;

use function VendorName\PackageName\{functionX, functionY, functionZ};

use const VendorName\PackageName\{ConstantX, ConstantY, ConstantZ};

/**
 * The block of comments...
 */
class Fruit
{
    //...
}
```

现在，您应该注意，在 PSR-12 中，您必须在开头的`<?php`标记后使用一个空行。但是，在 PSR-2 中，这是不必要的。例如，您可以编写以下内容：

```js
<?php
namespace VendorName\PackageName;

use FruitClass;
use VegetableClass as Veg;
```

值得知道，PSR-2 是从 PSR-1 扩展而来的，它是一个基本的编码标准，但自从 PSR-12 被接受以来，PSR-2 现在已正式弃用。

要为您的代码实施这些 PSR，请访问以下网站：

+   [`www.php-fig.org/psr/psr-1/`](https://www.php-fig.org/psr/psr-1/) 用于 PSR-1：基本编码标准

+   [`www.php-fig.org/psr/psr-2/`](https://www.php-fig.org/psr/psr-2/) 用于 PSR-2：编码风格指南（已弃用）

+   [`www.php-fig.org/psr/psr-12/`](https://www.php-fig.org/psr/psr-12/) 用于 PSR-12：扩展编码风格

如果您想了解 PHP 7 的新功能，例如标量类型声明和返回类型声明，请访问[`www.php.net/manual/en/migration70.new-features.php`](https://www.php.net/manual/en/migration70.new-features.php)。

PSR-12 帮助 PHP 编码人员编写更易读和结构化的代码，因此在使用 PHP 编写代码时值得采用它。现在，让我们继续讨论**PSR-4**，它允许我们在 PHP 中使用自动加载。

## PSR-4 – 自动加载器

在 PHP 的旧日子里，如果您想将第三方库引入您的 PHP 项目，或者从单独的 PHP 文件中引入您的函数和类，您将使用`include`或`require`语句。随着 PHP 自动加载的到来，您将使用`__autoload`魔术方法（自 PHP 7.2 起已弃用）或`spl_autoload`来自动调用您的代码。然后在 PHP 5.3 中出现了**真正的命名空间**支持，开发人员和框架可以设计他们的方法来防止命名冲突。但仍然远非理想，因为不同方法之间存在冲突。您可以想象一种情况，您有两个框架 - 框架 A 和框架 B - 以及个别开发人员彼此不同意并实施自己的方法来实现相同的结果。这是疯狂的。

今天，我们遵守 PSR-4（它是 PSR-0 的后继者）来标准化自动加载的方法，并将开发人员和框架绑定在一起。它指定了从**文件路径**自动加载类的标准。它还描述了文件的位置。因此，一个**完全限定的类名**应该遵循以下形式：

```js
\<NamespaceName>(\<SubNamespaceNames>)\<ClassName>
```

在这个规则中，我们有以下内容：

+   完全限定类的命名空间必须具有顶级供应商命名空间，这是上述代码中的`<NamespaceName>`部分。

+   在上面的代码中，您可以使用一个或多个子命名空间，如`<SubNamespaceNames>`部分所示。

+   然后，您必须使用您的类名结束命名空间，如上述代码中的`<ClassName>`部分所示。

因此，如果您正在编写自动加载程序，建议使用此标准。但是，您不必（也可能不应该）费力地编写自己的自动加载程序来符合 PSR-4。这是因为您可以使用**Composer**来帮助您做到这一点。Composer 是 PHP 的包管理器。它类似于 Node.js 中的 npm。它最初是在 2012 年发布的。从那时起，它已被所有现代 PHP 框架和 PHP 编码人员使用。这意味着您可以更多地专注于您的代码开发，而不必过多担心要引入项目环境中的不同包和库的互操作性。

在开始之前，请确保您的系统上已安装 Composer。根据您的系统，您可以按照以下指南安装 Composer：

+   [`getcomposer.org/doc/00-intro.md`](https://getcomposer.org/doc/00-intro.md)和[`getcomposer.org/download/`](https://getcomposer.org/download/)来自官方 Composer 网站

+   [`php`](https://phptherightway.com/#dependency_management)[therightway.com/dependency_management](https://phptherightway.com/#dependency_management)来自**PHP:正确的方式**

当前版本为 1.10.9。按照以下步骤安装 Composer 并利用其提供的自动加载程序：

1.  通过在终端中运行以下脚本在当前目录中安装 Composer：

```js
$ php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
$ php -r "if (hash_file('sha384', 'composer-setup.php') === 'e5325b19b381bfd88ce90a5ddb7823406b2a38cff6bb704b0acc289a09c8128d4a8ce2bbafcd1fcbdc38666422fe2806') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
```

1.  按照以下步骤运行 Composer 设置文件：

```js
$ sudo php composer-setup.php
```

您应该在终端中获得以下输出：

```js
All settings correct for using Composer
Downloading...

Composer (version 1.10.9) successfully installed to: /home/lau/composer.phar
Use it: php composer.phar
```

1.  按照以下步骤删除 Composer 设置文件：

```js
$ php -r "unlink('composer-setup.php');"
```

1.  通过在终端上运行`php composer.phar`来验证安装。如果您想**全局**使用 Composer，则将 Composer 移动到`/usr/local/bin`（如果您使用 Linux/Unix）：

```js
$ sudo mv composer.phar /usr/local/bin/composer
```

1.  现在，您可以全局运行 Composer。要验证它，只需运行以下命令：

```js
$ composer
```

您应该看到 Composer 的标志，以及其可用的命令和选项：

```js
   ______
  / ____/___ ____ ___ ____ ____ ________ _____
 / / / __ \/ __ __ \/ __ \/ __ \/ ___/ _ \/ ___/
/ /___/ /_/ / / / / / / /_/ / /_/ (__ ) __/ /
\____/\____/_/ /_/ /_/ .___/\____/____/\___/_/
                    /_/
Composer version 1.10.9 2020-07-16 12:57:00
...
...
```

或者，您可以使用`-V`选项直接检查您安装的版本：

```js
$ composer -V
Composer version 1.10.9 2020-07-16 12:57:00
```

1.  现在，您已经在系统上安装了 Composer，只需通过终端导航到项目的根目录，并使用`composer require`，然后是`<package-name>`，来安装您项目中需要的任何第三方软件包（也称为依赖项），如下所示：

```js
$ composer require monolog/monolog
```

1.  安装所需的软件包后，您可以转到项目根目录。您应该看到已创建一个包含项目依赖项的`composer.json`文件，其中包含`require`键：

```js
{
    "require": {
        "monolog/monolog": "².0"
    }
}
```

1.  如果您想下次再次安装所有依赖项，只需运行`install`命令，如下所示：

```js
$ composer install
```

1.  当您安装了项目的依赖项，无论是使用`require`还是`install`命令，Composer 都会生成一个包含所有依赖项的`/vendor/`文件夹。`autoload.php`文件将始终生成并位于`/vendor/`文件夹内。然后，您可以包含此文件并立即开始使用这些软件包提供的类，如下所示：

```js
require __DIR__ . '/vendor/autoload.php';

$log = new Monolog\Logger('name');
$log->pushHandler(new Monolog\Handler\StreamHandler('path/to/your.log', Monolog\Logger::WARNING));
$log->addWarning('Foo');
$log->error('Bar');
```

1.  最重要的是，您甚至可以通过向`composer.json`文件添加`autoload`键以及自定义命名空间来将您的类添加到自动加载程序。例如，您可以将类存储在项目根目录中的`/src/`文件夹中，与`/vendor/`目录位于同一级别：

```js
{
    "autoload": {
        "psr-4": {"Spectre\\": "src/"}
    }
}
```

如果您的源文件位于多个位置，您可以使用数组`[]`将其与您的自定义命名空间关联起来，如下所示：

```js
{
    "autoload": {
        "psr-4": {
            "Spectre\\": [
                "module1/",
                "module2/"
            ]
        }
    }
}
```

Composer 将为`Spectre`命名空间注册一个 PSR-4 自动加载程序。之后，您可以开始编写您的类。例如，您可以创建一个包含`Spectre\Foo`类的`/src/Foo.php`文件。之后，只需在终端上运行`dump-autoload`来重新生成`/vendor/`目录中的`autoload.php`文件。您还可以添加多个自定义命名空间到`autoload`字段，如下所示：

```js
{
    "autoload": {
        "psr-4": {
            "Spectre\\": [
                //...
            ],
            "AnotherNamespace\\": [
                //...
            ]
        }
    }
}
```

除了 PSR-4，Composer 还支持 PSR-0。您可以在`composer.json`文件中添加 PSR-0 键。

有关如何在 Composer 中使用 PSR-0 的更多信息和示例，请访问[`getcomposer.org/doc/04-schema.mdautoload`](https://getcomposer.org/doc/04-schema.md#autoload)。但是，请注意，PSR-0 现在已经不推荐使用。如果您想阅读有关这两个 PSR 的更多信息，请访问[`www.php-fig.org/psr/psr-0/`](https://www.php-fig.org/psr/psr-0/)了解 PSR 0（不推荐使用），以及[`www.php-fig.org/psr/psr-4/`](https://www.php-fig.org/psr/psr-4/)了解 PSR-4。如果您想了解我们在 PHP 中用于记录的 Monolog，请访问[`github.com/Seldaek/monolog`](https://github.com/Seldaek/monolog)。如果您想了解 PHP 中自动加载类的更多信息，请访问[`www.php.net/manual/en/language.oop5.autoload.php`](https://www.php.net/manual/en/language.oop5.autoload.php)。

一旦您掌握了关于 PSR-12 和 PSR-4 的知识，您将更容易构建符合其他 PSR 的 PHP 应用程序。本书关注的另外两个 PSR 是 PSR-7 和 PSR-15。让我们先来看一下**PSR-7**。

## PSR-7 - HTTP 消息接口

早些时候，我们提到 PHP 没有 HTTP 请求和响应消息对象，这就是为什么 PHP 框架和编码人员在过去提出了不同的抽象来表示（或“模拟”）HTTP 消息。幸运的是，在 2015 年，PSR-7 出现了，结束了这些“分歧”和差异。

PSR-7 是一组通用接口（抽象），用于在 HTTP 通信中指定 HTTP 消息和 URI 的公共方法。在面向对象编程（OOP）中，接口实际上是对象（类）必须实现的操作（公共方法）的抽象，而不定义这些操作的复杂性和细节。例如，以下表格显示了当组合 HTTP 消息类时，必须实现的方法，以便符合 PSR-7 规范。

用于访问和修改请求和响应对象的指定方法如下：

|  **访问** |  **修改** |
| --- | --- |
| `getProtocolVersion()` | `withProtocolVersion($version)` |
| `getHeaders()` | `withHeader($name, $value)` |
| `hasHeader($name)` | `withAddedHeader($name, $value)` |
| `getHeader($name)``getHeaderLine($name)` | `withoutHeader($name)` |
| `getBody()` | `withBody(StreamInterface $body)` |

用于访问和修改**请求**对象的指定方法如下：

| **访问** | **修改** |
| --- | --- |

|

+   `getRequestTarget()`

+   `getMethod()`

+   `getUri()`

+   `getServerParams()`

+   `getCookieParams()`

+   `getQueryParams()`

+   `getUploadedFiles()`

+   获取解析后的主体`getParsedBody()`

+   获取属性`getAttributes()`

+   `getAttribute($name, $default = null)`

|

+   使用`withMethod($method)`方法

+   `withRequestTarget($requestTarget)`

+   使用`withUri(UriInterface $uri, $preserveHost = false)`方法

+   `withCookieParams(array $cookies)`

+   `withQueryParams(array $query)`

+   `withUploadedFiles(array $uploadedFiles)`

+   `withParsedBody($data)`

+   `withAttribute($name, $value)`

+   `withoutAttribute($name)`

|

用于访问和修改**响应**对象的指定方法如下：

| **访问** | **修改** |
| --- | --- |

|

+   获取状态码`getStatusCode()`

+   `getReasonPhrase()`

|

+   `withStatus($code, $reasonPhrase = '')`

|

自从 2015 年 5 月 18 日接受了 PSR-7 以来，许多基于它制作的软件包已经问世。只要实现了 PSR-7 中指定的接口和方法，就可以开发自己的版本。然而，除非你有充分的理由这样做，否则你可能会“重复造轮子”，因为已经有了 PSR-7 HTTP 消息软件包。因此，为了快速开始，让我们使用 Zend Framework 中的`zend-diactoros`。我们将“重用”你在前几节中学到的 PSR 知识（PSR-12 和 PSR-4）来创建一个简单的基于 HTTP 消息的“Hello World”服务器端应用程序。让我们开始吧：

1.  在应用程序根目录中创建一个`/public/`目录，并在其中添加一个`index.php`文件。将以下行添加到其中以引导应用程序环境：

```js
// public/index.php
chdir(dirname(__DIR__));
require_once 'vendor/autoload.php';
```

在这两行代码中，我们已将当前目录从`/path/to/public`更改为`/path/to`，以便我们可以通过写`vendor/autoload.php`而不是`../vendor/autoload.php`来导入`autoload.php`文件。

`__DIR__`（魔术）常量用于获取当前文件的目录路径，即`index.php`，位于`/path/to/public/`目录中。然后使用`dirname`函数获取父目录的路径，即`/path/to`。然后使用`chdir`函数来改变当前目录。

请注意，在接下来关于 PSR 的章节中，我们将使用这种模式来引导应用程序环境并导入自动加载文件。请访问以下链接以了解更多关于之前提到的常量和函数：

+   `__DIR__`（魔术）常量：[`www.php.net/manual/en/language.constants.predefined.php`](https://www.php.net/manual/en/language.constants.predefined.php)

+   `dirname`函数：[`www.php.net/manual/en/function.dirname.php`](https://www.php.net/manual/en/function.dirname.php)

+   `chdir`函数：[`www.php.net/manual/en/function.chdir.php`](https://www.php.net/manual/en/function.chdir.php)

还要注意，您必须通过使用内置的 PHP Web 服务器在终端上运行所有传入的 PHP 应用程序，如下所示：

```js
**$ php -S localhost:8181 -t public** 
```

1.  通过 Composer 将`zend-diactoros`安装到应用程序的根目录：

```js
$ composer require zendframework/zend-diactoros
```

1.  要整理传入的请求，您应该在`/public/`目录中的`index.php`文件中创建一个请求对象，如下所示：

```js
$request = Zend\Diactoros\ServerRequestFactory::fromGlobals(
    $_SERVER,
    $_GET,
    $_POST,
    $_COOKIE,
    $_FILES
);
```

1.  现在，我们可以创建一个响应对象并对响应进行操作，如下所示：

```js
$response = new Zend\Diactoros\Response();
$response->getBody()->write("Hello ");
```

1.  请注意`write`方法在流接口（`StreamInterface`）中指定，我们也可以通过多次调用此方法来追加更多数据：

```js
$response->getBody()->write("World!");
```

1.  如果需要，我们可以操作标头。

```js
$response = $response
    ->withHeader('Content-Type', 'text/plain');
```

1.  请注意，头部应在数据写入主体后添加。然后，您已经成功将您在本章开头学到的简单 PHP“Hello World”应用程序转换为具有 PSR-7 的现代 PHP 应用程序！但是，如果您在终端中使用`php -S localhost:8181 -t public`运行此 PSR-7“Hello World”应用程序在浏览器上，您将看不到任何内容。这是因为我们没有使用**PSR-15 HTTP 服务器请求处理程序**和**PSR-7 HTTP 响应发射器**将响应发送到浏览器，我们将在下一节中介绍。如果您现在想看到输出，可以使用`getBody`方法访问数据，然后使用`echo`：

```js
echo $response->getBody();
```

1.  如果您通过 Chrome 的开发者工具检查页面的`Content-type`，您将得到`text/html`而不是我们用`withHeader`方法修改的`text/plain`。我们将在下一章中使用发射器获得正确的内容类型。

有关`zend-diactoros`及其高级用法的更多信息，请访问[`docs.zendframework.com/zend-diactoros/`](https://docs.zendframework.com/zend-diactoros/)。除了 Zend Framework 的`zend-diactoros`之外，您还可以使用其他框架和库的 HTTP 消息包：

+   来自 Guzzle 的 Guzzle 和 PSR-7 在[`docs.guzzlephp.org/en/latest/psr7.html`](http://docs.guzzlephp.org/en/latest/psr7.html)

+   PHP-HTTP 的 HTTPlug 在[`docs.php-http.org/en/latest/`](http://docs.php-http.org/en/latest/)

+   Symfony 的 PSR-7 桥梁在[`symfony.com/doc/master/components/http_foundation.html`](https://symfony.com/doc/master/components/http_foundation.html)

+   Slim 在[`www.slimframework.com`](http://www.slimframework.com)

您应该查看[`www.php-fig.org/psr/psr-7/`](https://www.php-fig.org/psr/psr-7/)上的 PSR-7 文档，以获取有关此 PSR 的更多信息。如果您是 PHP 接口的新手，请访问[`www.php.net/manual/en/language.oop5.interfaces.php`](https://www.php.net/manual/en/language.oop5.interfaces.php)进行进一步阅读。

从 PSR-7 文档中，您可以找到本书中未提及的其他公共方法。它们应该在任何 PSR-7 HTTP 消息包中都可以找到，比如`zend-diactoros`。了解这些方法很有用，这样您就知道可以用它们做什么。您还可以在运行时使用内置的 PHP `get_class_methods` 方法列出您可以在请求和响应对象中使用的所有方法。例如，对于`request`对象，您可以执行以下操作：

```js
$request = Zend\Diactoros\ServerRequestFactory::fromGlobals(
    //...
);
print_r(get_class_methods($request));
```

您将获得一个可以调用的请求方法列表的数组。对于`response`对象也是一样；通过这样做，您将获得一个响应方法列表的数组：

```js
$response = new Zend\Diactoros\Response();
print_r(get_class_methods($response));
```

现在，让我们继续并查看**PSR-15**，在那里我们将了解如何向客户端（浏览器）发出响应。

## PSR-15 - HTTP 服务器请求处理程序（请求处理程序）

PSR-7 是 PHP 社区的一个重要进步，但它只是实现目标的一半，**可能**使 PHP 编码人员摆脱庞大的 MVC 框架，并允许他们从一系列可重用的中间件中组合出不可知的 PHP 应用。它只定义了 HTTP 消息（请求和响应）；它从未定义如何处理它们。因此，我们需要一个请求处理程序来处理请求以产生响应。

与 PSR-7 一样，PSR-15 是一组常见接口，但它们更进一步，并指定了请求处理程序（HTTP 服务器请求处理程序）和中间件（HTTP 服务器请求中间件）的标准。它于 2018 年 1 月 22 日被接受。我们将在下一节中介绍 HTTP 服务器请求中间件。现在，让我们了解 PSR-15 接口中的 HTTP 服务器请求处理程序`RequestHandlerInterface`：

```js
// Psr\Http\Server\RequestHandlerInterface

namespace Psr\Http\Server;

use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;

interface RequestHandlerInterface
{
    public function handle(ServerRequestInterface $request) : 
     ResponseInterface;
}
```

正如您所看到的，这是一个非常简单的接口。它只有一个指定的公共方法`handle`，它只接受一个 PSR-7 HTTP 请求消息，并且**必须**返回一个 PSR-7 HTTP 响应消息。我们将使用 Zend Framework 的`zend-httphandlerrunner`组件来实现这个接口，以提供我们可以用来发出 PSR-7 响应的实用工具。让我们将其连接到应用程序：

1.  通过 Composer 安装`zend-httphandlerrunner`：

```js
$ composer require zendframework/zend-httphandlerrunner
```

1.  一旦我们在项目环境中安装好了，我们可以将之前创建的响应发送到浏览器，如下所示：

```js
//...
$response = $response
    ->withHeader('Content-Type', 'text/plain');

(new Zend\HttpHandlerRunner\Emitter\SapiEmitter)->emit($response);
```

如果您通过 Chrome 的开发者工具再次检查页面的`Content-Type`，您将获得正确的内容类型，即`text/plain`。

有关`zend-httphandlerrunner`的更多信息，请访问[`docs.zendframework.com/zend-httphandlerrunner/`](https://docs.zendframework.com/zend-httphandlerrunner/)。有关 PSR-15 的更多信息，请访问[`www.php-fig.org/psr/psr-15/`](https://www.php-fig.org/psr/psr-15/)。

除了`zend-httphandlerrunner`，您还可以使用 Narrowspark 的 Http Response Emitter [`github.com/narrowspark/http-emitter`](https://github.com/narrowspark/http-emitter) 来处理请求并发出响应。现在，让我们继续看一下 PSR-15 的**第二个接口**：`MiddlewareInterface`。

## PSR-15 - HTTP 服务器请求处理程序（中间件）

PSR-15 中的中间件接口具有以下抽象：

```js
// Psr\Http\Server\MiddlewareInterface

namespace Psr\Http\Server;

use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;
use Psr\Http\Server\RequestHandlerInterface;

interface MiddlewareInterface
{
    public function process(
        ServerRequestInterface $request,
        RequestHandlerInterface $handler
    ) : ResponseInterface;
}
```

再次，您可以看到这是一个非常简单的接口。它只有一个指定的公共方法`process`用于中间件实现。实现这个接口的组件（中间件）将只接受一个 PSR-7 HTTP 请求消息和一个 PSR-15 HTTP 服务器请求处理程序，然后**必须**返回一个 PSR-7 HTTP 响应消息。

我们将使用 Zend Framework 的`zend-stratigility`组件来实现这个接口，以便我们可以在我们的应用程序中创建 PSR-15 中间件。让我们学习如何将其连接到应用程序：

1.  通过 Composer 安装`zend-stratigility`：

```js
$ composer require zendframework/zend-stratigility
```

1.  一旦我们在项目环境中安装了它，我们将导入`middleware`函数和`MiddlewarePipe`类，如下所示：

```js
use function Zend\Stratigility\middleware;

$app = new Zend\Stratigility\MiddlewarePipe();

// Create a request
$request = Zend\Diactoros\ServerRequestFactory::fromGlobals(
    //...
);
```

1.  然后，我们可以使用这个`middleware`函数创建**三个**中间件并将它们附加到管道中，如下所示：

```js
$app->pipe(middleware(function ($request, $handler) {
    $response = $handler->handle($request);
    return $response
        ->withHeader('Content-Type', 'text/plain');
}));

$app->pipe(middleware(function ($request, $handler) {
    $response = $handler->handle($request);
    $response->getBody()->write("User Agent: " . 
     $request->getHeader('user-agent')[0]);
    return $response;
}));

$app->pipe(middleware(function ($request, $handler) {
    $response = new Zend\Diactoros\Response();
    $response->getBody()->write("Hello world!\n");
    $response->getBody()->write("Request method: " .
     $request->getMethod() . "\n");
    return $response;
}));
```

1.  正如您所看到的，“Hello World”代码块我们之前创建的现在是一个与其他中间件堆叠在一起的中间件。最后，我们可以从这些中间件生成一个**最终**响应并将其发出到浏览器，如下所示：

```js
$response = $app->handle($request);
(new Zend\HttpHandlerRunner\Emitter\SapiEmitter)->
  emit($response);
```

您应该在`0.0.0.0:8181`的浏览器上获得类似以下的结果：

```js
Hello world!
Request method: GET
User Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 
 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36
```

有关`zend-stratigility`的更多信息，请访问[`docs.zendframework.com/zend-stratigility/`](https://docs.zendframework.com/zend-stratigility/)。

除了`zend-stratigility`，您还可以使用以下软件包来创建您的中间件：

+   Northwoods Broker at [`github.com/northwoods/broker`](https://github.com/northwoods/broker)

+   Relay at [`relayphp.com/`](https://relayphp.com/)

所以，就是这样。借助几个可互操作的组件，我们已经启动了一个符合 PSR-12、PSR-7 和 PSR-15 的现代 PHP 应用程序，这意味着您可以自由地从广泛的供应商实现中（不可知论）选择用于 HTTP 消息、请求处理程序和中间件的标准。但我们还没有完成。您可能已经注意到，我们创建的应用程序只是一个在`0.0.0.0:8181`上运行的单页面应用程序。它没有其他路由，如`/about`、`/contact`等。因此，我们需要一个实现 PSR-15 的路由器。我们将在下一节中介绍这个。

## PSR-7/PSR-15 路由器

我们将使用来自 The League of Extraordinary Packages（一个 PHP 开发者组）的 Route，以便我们拥有一个 PSR-7 路由系统，并在其上调度我们的 PSR-15 中间件。简而言之，Route 是一个快速的 PSR-7 路由/调度程序包。

它是一个 PSR-15 服务器请求处理程序，可以处理一系列中间件的调用。它是建立在 Nikita Popov 的 FastRoute ([`github.com/nikic/FastRoute`](https://github.com/nikic/FastRoute))之上。

让我们学习如何将其连接到应用程序：

1.  通过 Composer 安装`league/route`：

```js
$ composer require league/route
```

1.  安装后，我们可以按如下方式重构我们的“Hello World”组件以使用路由：

```js
use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;

$request = Zend\Diactoros\ServerRequestFactory::fromGlobals(
    //...
);

$router = new League\Route\Router;

$router->map('GET', '/', function (ServerRequestInterface $request) : ResponseInterface {
    $response = new Zend\Diactoros\Response;
    $response->getBody()->write('<h1>Hello, World!</h1>');
    return $response;
});
```

1.  然后，我们只需使用 Route 的`dispatch`方法创建一个 PSR-7 HTTP 响应，并将其发送到浏览器：

```js
$response = $router->dispatch($request);
(new Zend\HttpHandlerRunner\Emitter\SapiEmitter)->emit($response);
```

查看您可以在[`route.thephpleague.com/4.x/route`](https://route.thephpleague.com/4.x/route)使用的 HTTP 请求方法列表（`get`、`post`、`put`、`delete`等）。此外，我们可以将中间件附加到我们的应用程序。

1.  如果您想锁定整个应用程序，可以将中间件添加到路由器，如下所示：

```js
use function Zend\Stratigility\middleware;

$router = new League\Route\Router;
$router->middleware(<middleware>);
```

1.  如果您想锁定一组路由，可以将中间件添加到该组，如下所示：

```js
$router
    ->group('/private', function ($router) {
        // ... add routes
    })
    ->middleware(<middleware>)
;
```

1.  如果您想锁定特定路由，可以将中间件添加到该路由，如下所示：

```js
$router
    ->map('GET', '/secret', <SomeController>)
    ->middleware(<middleware>)
;
```

1.  例如，您可以使用`zend-stratigility`与 Route：

```js
use function Zend\Stratigility\middleware;

$router = new League\Route\Router;
$router->middleware(middleware(function ($request, $handler) {
    //...
}));
```

1.  如果您不想使用`middleware`函数，或者根本不想使用`zend-stratigility`，您可以创建匿名中间件，如下所示：

```js
use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;
use Psr\Http\Server\MiddlewareInterface;
use Psr\Http\Server\RequestHandlerInterface;

$router = new League\Route\Router;

$router->middleware(new class implements MiddlewareInterface {
    public function process(ServerRequestInterface $request, 
    RequestHandlerInterface $handler) : ResponseInterface
    {
        $response = $handler->handle($request);
        return $response->withHeader('X-Clacks-Overhead', 
        'GNU Terry Pratchett');
    }
});
```

只要您遵守 PSR7 和 PSR-15，通过在中间件中实现`process`方法，就无需`zend-stratigility`。如果您想在单独的 PHP 文件中创建基于类的中间件，请查看提供的示例[`route.thephpleague.com/4.x/middleware/`](https://route.thephpleague.com/4.x/middleware)。

有关 The League of Extraordinary Packages 的 Route 的更多信息，请访问[`route.thephpleague.com/`](https://route.thephpleague.com/)。您还可以查看由这个开发人员组创建的其他软件包，网址为[`thephpleague.com`](https://thephpleague.com)/。除了 The League of Extraordinary 的 Route 之外，您还可以使用以下基于 PSR-7 和 PSR-15 的 HTTP 路由器软件包：

+   `delolmo/symfony-router`在[`github.com/delolmo/symfony-router`](https://github.com/delolmo/symfony-router)

+   `middlewares/aura-router`在[`github.com/middlewares/aura-router`](https://github.com/middlewares/aura-router)

+   `middlewares/fast-route`在[`github.com/middlewares/fast-route`](https://github.com/middlewares/fast-route)

+   `timtegeler/routerunner`在[`github.com/timtegeler/routerunner`](https://github.com/timtegeler/routerunner)

+   `sunrise-php/http-router`在[`github.com/sunrise-php/http-router`](https://github.com/sunrise-php/http-router)

您可能需要一个分发器来与其中一些软件包一起使用。使用 The League of Extraordinary Packages 的 Route 的优势在于它提供了一个路由器和一个分发器在一个软件包中。

有了这个，我们通过使用 PSR-12、PSR-4、PSR-7 和 PSR-15，编写了一个不可知的 PHP 应用程序。但是我们的 PHP API 还没有完成。还有一项任务要做——我们需要为 CRUD 操作添加一个数据库框架。我们将在下一节中指导您完成这项任务。

# 使用 PHP 数据库框架编写 CRUD 操作

正如您可能还记得的那样第九章，*添加服务器端数据库*，**CRUD**代表**c**reate，**r**ead，**u**pdate 和**d**elete。在那一章中，我们使用 MongoDB 来创建 CRUD 操作。在本节中，我们将使用 MySQL 来创建后端身份验证。我们将在刚刚使用 PSRs 创建的 PHP 应用程序中使用 MySQL 和 PHP。因此，让我们首先创建我们在 MySQL 数据库中需要的表。

## 创建 MySQL 表

确保您已在本地计算机上安装了 MySQL 服务器并创建了一个名为`nuxt-php`的数据库。完成这些操作后，请按照以下步骤完成我们 API 的第一部分：

1.  在数据库中插入以下 SQL 查询以创建表：

```js
CREATE TABLE user (
  uuid varchar(255) NOT NULL,
  name varchar(255) NOT NULL,
  slug varchar(255) NOT NULL,
  created_on int(10) unsigned NOT NULL,
  updated_on int(10) unsigned NOT NULL,
  UNIQUE KEY slug (slug)
) ENGINE=MyISAM DEFAULT CHARSET=utf8;
```

您可能已经注意到的第一件事是，我们使用的是`uuid`而不是像我们在第十二章中所做的`id`。UUID 代表**通用唯一标识符**。可能有一些原因和好处会让您选择 UUID 而不是自动递增键来索引数据库表中的记录。例如，您可以在不连接到数据库的情况下创建 UUID。它在应用程序中几乎是唯一的，因此您可以轻松地从不同的数据库中组合数据而永远不会发生冲突。为了在 PHP 应用程序中生成 UUID，我们可以使用 Ben Ramsey 的`ramsey/uuid`来帮助我们生成 RFC 4122（[`tools.ietf.org/html/rfc4122`](https://tools.ietf.org/html/rfc4122)）版本 1、3、4 和 5 的 UUID。

1.  所以，让我们通过 Composer 安装`ramsey/uuid`：

```js
$ composer require ramsey/uuid
```

1.  现在，您可以使用这个包来生成 UUID 的第一个版本，如下所示：

```js
use Ramsey\Uuid\Uuid;

$uuid1 = Uuid::uuid1();
echo $uuid1->toString();
```

如果您想了解更多关于这个包的信息，请访问[`github.com/ramsey/uuid`](https://github.com/ramsey/uuid)。

现在，让我们学习如何使用 PHP 来处理 MySQL 数据库，并了解为什么我们需要一个数据库框架来加快 PHP 开发速度。

## 使用 Medoo 作为数据库框架

在 PHP 的旧时代，开发人员使用 MySQL 函数（[`www.php.net/manual/en/ref.mysql.php`](https://www.php.net/manual/en/ref.mysql.php)）来管理 MySQL 数据库。然后，MySQLi 扩展（[`www.php.net/manual/en/book.mysqli.php`](https://www.php.net/manual/en/book.mysqli.php)）取代了现在已经弃用的 MySQL 函数。然而，现在，开发人员被鼓励使用**PHP 数据对象**（**PDO**）（[`www.php.net/manual/en/book.pdo.php`](https://www.php.net/manual/en/book.pdo.php)）。PDO 是一个内置的 PHP 接口抽象，就像 PSR-7 和 PSR-15 一样。它是一个数据访问抽象层，为访问和管理数据库（例如 MySQL 和 PostgreSQL）提供了一个一致的接口（统一的 API），这意味着无论你使用哪种数据库，你都可以使用相同的函数来查询和获取数据。它支持以下数据库：

|

+   CUBRID

+   MS SQL Server

+   Firebird

+   IBM

|

+   Informix

+   MySQL

+   Oracle

+   ODBC 和 DB2

|

+   PostgreSQL

+   SQLite

+   4D

|

请注意，PDO 是一个数据访问抽象层，而不是数据库抽象层。因此，取决于您使用的数据库，必须安装该数据库的 PDO 驱动程序才能使用 PDO。我们正在使用 MySQL 数据库，因此必须确保安装了`PDO_MYSQL`驱动程序。在 Ubuntu 中，您可以使用以下命令来检查您是否已启用 PDO 扩展，并且`PDO_MYSQL`驱动程序已安装在您的环境中：

```js
$ php -m
```

您应该会得到一系列 PHP 模块。查找`PDO`和`pdo_mysql`：

```js
[PHP Modules]
...
PDO
pdo_mysql
...
```

另一个更具体的选项是检查 PDO 及其驱动程序，如下所示：

```js
$ php -m|grep -i pdo
PDO
pdo_mysql
```

如果您只想搜索 PDO 驱动程序，请执行以下操作：

```js
$ php -m|grep -i pdo_
pdo_mysql
```

您还可以创建一个带有`phpinfo()`的 PHP 页面来查找它们。或者，您可以使用`getAvailableDrivers`方法，如下所示：

```js
print_r(PDO::getAvailableDrivers());
```

您应该会得到一系列 PDO 驱动程序，如下所示：

```js
Array
(
    [0] => mysql
)
```

或者，还有一些内置的 PHP 函数可以帮助您：

```js
extension_loaded ('PDO'); // returns boolean
extension_loaded('pdo_mysql'); // returns boolean
get_loaded_extensions(); // returns array
```

如果您没有看到任何 PDO 驱动程序，则必须安装 MySQL 支持的驱动程序。请按照以下步骤执行：

1.  搜索软件包名称（Ubuntu）：

```js
$ apt-cache search php7.4|grep mysql
php7.4-mysql - MySQL module for PHP
```

1.  安装`php7.4-mysql`并重新启动 Apache 服务器：

```js
$ sudo apt-get install php7.4-mysql
$ sudo service apache2 restart
```

一旦您安装了`PDO_MYSQL`驱动程序，就可以立即开始编写 CRUD 操作。例如，让我们编写一个`insert`操作，如下所示：

1.  创建 MySQL 数据库连接：

```js
$servername = "localhost";
$username = "<username>";
$password = "<password>";
$dbname = "<dbname>";
$connection = new PDO(
    "mysql:host=$servername;dbname=$dbname",
    $username,
    $password
)
```

请注意，`<username>`，`<password>`和`<dbname>`是实际连接详细信息的占位符。您必须根据自己的数据库设置进行更改。

1.  准备 SQL 查询并“绑定”参数：

```js
$stmt = $connection->prepare("
    INSERT INTO user (
        uuid,
        name,
        slug,
        created_on,
        updated_on
    ) VALUES (
        :uuid,
        :name,
        :slug,
        :created_on,
        :updated_on
    )
");
$stmt->bindParam(':uuid', $uuid);
$stmt->bindParam(':name', $name);
$stmt->bindParam(':slug', $slug);
$stmt->bindParam(':created_on', $createdOn);
$stmt->bindParam(':updated_on', $updatedOn);
```

1.  插入一行新数据：

```js
$uuid = "25769c6c-d34d-4bfe-ba98-e0ee856f3e7a";
$name = "John Doe";
$slug = "john-doe";
$createdOn = (new DateTime())->getTimestamp();
$updatedOn = $createdOn;
$stmt->execute();
```

这并不理想，因为您必须每次“准备”语句并绑定参数，这需要相当多的行来操作。因此，我们应该选择一个 PHP 数据库框架来加速开发。**Medoo** ([`medoo.in/`](https://medoo.in/))是其中的一个选择。它非常轻量级，非常容易集成和使用。

让我们安装并连接到我们的应用程序：

1.  通过 Composer 安装 Medoo：

```js
$ composer require catfan/medoo
```

1.  如果一切都设置好了，您可以导入 Medoo 并传递一个配置数组来启动数据库连接，就像我们之前在原始方法中所做的那样：

```js
use Medoo\Medoo;

$database = new Medoo([
  'database_type' => 'mysql',
  'database_name' => '<dbname>',
  'server' => 'localhost',
  'username' => '<username>',
  'password' => '<password>'
]);
```

通过这个数据库框架建立与 MySQL 数据库的连接就到此为止。您可以在本书的 GitHub 存储库中的`/chapter-16/nuxt-php/proxy/backend/core/mysql.php`中找到此片段的实际用法。我们将在接下来的部分中向您展示如何实现它，但现在让我们探索如何使用 Medoo 编写一些基本的 CRUD 操作。

## 插入记录

当您想要向表中插入新记录时，可以使用`insert`方法，如下所示：

```js
$database->insert('user', [
    'uuid' => '41263659-3c1f-305a-bfac-6a7c9eab0507',
    'name' => 'Jane',
    'slug' => 'jane',
    'created_on' => '1568072289'
]);
```

如果您想了解有关此方法的更多细节，请访问[`medoo.in/api/insert`](https://medoo.in/api/insert)。

## 查询记录

当您想要列出表中的记录时，可以使用`select`方法，如下所示：

```js
$database->select('user', [
    'uuid',
    'name',
    'slug',
    'created_on',
    'updated_on',
]);
```

`select`方法会给您一个记录列表。如果您只想选择特定行，可以使用`get`方法，如下所示：

```js
$database->get('user', [
    'uuid',
    'name',
    'slug',
    'created_on',
    'updated_on',
    ], [
    'slug' => 'jane'
]);
```

如果您想了解更多细节，请访问[`medoo.in/api/select`](https://medoo.in/api/select)查看`select`方法和[`medoo.in/api/get`](https://medoo.in/api/get)查看`get`方法。

## 更新记录

当您想要修改表中记录的数据时，可以使用`update`方法，如下所示：

```js
$database->update('user', [
    'name' => 'Janey',
    'slug' => 'jane',
    'updated_on' => '1568091701'
], [
    'uuid' => '41263659-3c1f-305a-bfac-6a7c9eab0507'
]);
```

如果您想了解有关此方法的更多细节，请访问[`medoo.in/api/update`](https://medoo.in/api/update)。

## 删除记录

当您想要从表中删除记录时，可以使用`delete`方法，如下所示：

```js
$database->delete('user', [
    'uuid' => '41263659-3c1f-305a-bfac-6a7c9eab0507'
]);
```

如果您想了解有关此方法的更多细节，请访问[`medoo.in/api/delete`](https://medoo.in/api/delete)。

这就是如何使用 Medoo 和 PDO 编写基本 CRUD 操作的全部内容。

请查看 Medoo 的文档[`medoo.in/doc`](https://medoo.in/doc)以了解您可以使用的其他方法。还有其他替代方案，如[`github.com/doctrine/dbal`](https://github.com/doctrine/dbal)上的 Doctrine DBAL 和[`github.com/illuminate/database`](https://github.com/illuminate/database)上的 Eloquent。

在本节中，您学习了一些 PSR 和 CRUD 操作。接下来，我们将介绍如何将它们全部整合到 Nuxt 中。由于 PHP 和 JavaScript 是两种不同的语言，它们之间唯一的交流方式是通过 API 中的 JSON。

但在编写启用该功能的脚本之前，我们应该研究这两个程序的跨域应用程序结构。自从第十二章以来，我们一直在为我们的 Nuxt 应用程序使用跨域应用程序结构，*创建用户登录和 API 身份验证*，所以这对您来说应该很熟悉。让我们开始吧！

## 构建跨域应用程序目录

再次，就像构建跨域应用程序目录时一样，以下是我们对 Nuxt 和我们的 PHP API 的整体视图：

```js
// Nuxt app
front-end
├── package.json
├── nuxt.config.js
└── pages
    ├── index.vue
    └── ...

// PHP API
backend
├── composer.json
├── vendor
│ └── ...
├── ...
└── ...
```

就 Nuxt 的目录结构而言，它保持不变。我们只需对 API 目录的结构进行轻微更改，如下所示：

```js
// PHP API
backend
├── composer.json
├── middlewares.php
├── routes.php
├── vendor
│ └── ...
├── public
│ └── index.php
├── static
│ └── ...
├── config
│ └── ...
├── core
│ └── ...
├── middleware
│ └── ...
└── module
    └── ...
```

PHP API 的目录结构是一个建议。您可以始终设计自己喜欢并最适合您的结构。因此，一目了然，我们有以下内容：

+   `/vendor/`目录是存放所有第三方包或依赖项的地方。

+   `/public/`目录只包含一个`index.php`文件，该文件启动我们的 API。

+   `/static/`目录用于静态文件，例如网站图标。

+   `/config/`目录存储配置文件，例如 MySQL 文件。

+   `/core/`目录存储我们可以在整个应用程序中使用的常见对象和函数。

+   `/middleware/`目录存储我们的 PSR-15 中间件。

+   `/module/`目录存储我们稍后将创建的自定义模块，就像我们在第十二章中所做的那样，*创建用户登录和 API 身份验证*，使用 Koa。

+   `composer.json`文件始终位于根级别。

+   `middlewares.php`文件是从`/middleware/`目录导入中间件的核心位置。

+   `routes.php`文件是从`/module/`目录导入路由的核心位置。

一旦您准备好结构，就可以开始编写顶级代码，将来自不同位置和目录的其他代码粘合到`/public/`目录中的`index.php`文件中，从而形成一个单一的应用程序。所以，让我们开始吧：

1.  将`foreach`循环放在`routes.php`文件中，以迭代稍后将创建的每个模块：

```js
// backend/routes.php
$modules = require './config/routes.php';

foreach ($modules as $module) {
    require './module/' . $module . 'index.php';
}
```

1.  在`/config/`目录中创建一个`routes.php`文件，该文件将列出您的模块的文件名，如下所示：

```js
// backend/config/routes.php
return [
    'Home/',
    'User/'.
    //...
];
```

1.  在这个 PHP API 中，`middlewares.php`文件将导入一个用于装饰 CRUD 操作输出的中间件片段：

```js
// backend/middlewares.php
require './middleware/outputDecorator.php';
```

此装饰器将以以下格式以 JSON 格式打印 CRUD 操作的输出：

```js
{"status":<status code>,"data":<data>}
```

1.  在`/middleware/`目录中创建一个名为`outputDecorator.php`的文件，其中包含以下代码。这将以前述格式包装操作的输出：

```js
// backend/middleware/outputDecorator.php
use function Zend\Stratigility\middleware;

$router->middleware(middleware(function ($request, $handler) {
    $response = $handler->handle($request);
    $existingContent = (string) $response->getBody();
    $contentDecoded = json_decode($existingContent, true);
    $status = $response->getStatusCode();
    $data = [
        "status" => $status,
        "data" => $contentDecoded
    ];
    $payload = json_encode($data);

    $response->getBody()->rewind();
    $response->getBody()->write($payload);

    return $response
        ->withHeader('Content-Type', 'application/json')
        ->withStatus($status);
}));
```

在这里，我们使用`zend-stratigility`组件的`middleware`方法来创建装饰器中间件。然后，我们使用 The League of Extraordinary 的`league/route`路由器来使用此中间件锁定整个应用程序。

1.  在`/core/`目录中创建一个名为`mysql.php`的文件，该文件返回 MySQL 连接的 Medoo 实例：

```js
// backend/core/mysql.php
$dbconfig = require './config/mysql.php';
$mysql = new Medoo\Medoo([
    'database_type' => $dbconfig['type'],
    'database_name' => $dbconfig['name'],
    'server' => $dbconfig['host'],
    'username' => $dbconfig['username'],
    'password' => $dbconfig['password']
]);
return $mysql;
```

1.  正如我们之前提到的，`/public/`目录只包含一个`index.php`文件。这用于启动我们的程序，因此它包含了您之前学习的关于 PSRs 的脚本：

```js
// backend/public/index.php
chdir(dirname(__DIR__));
require_once 'vendor/autoload.php';

$request = Zend\Diactoros\ServerRequestFactory::fromGlobals(
    //...
);

$router = new League\Route\Router;
try {
    require 'middlewares.php';
    require 'routes.php';
    $response = $router->dispatch($request);
} catch(Exception $exception) {
    // handle errors
}

(new Zend\HttpHandlerRunner\Emitter\SapiEmitter)->emit($response);
```

在这里，您可以看到`middlewares.php`和`routes.php`文件被导入到这个文件中以生成一个 PSR-7 响应。它们被包裹在`try`和`catch`块中，以捕获任何 HTTP 错误，比如 404 和 506 错误。因此，模块的任何输出和错误都将通过最后一行传递到浏览器。希望这给您提供了一个对这个简单 API 的整体了解。现在，让我们继续深入学习`/module/`目录，以更详细地了解如何创建模块和路由。

## 创建 API 的公共路由及其模块

创建 API 的公共路由及其模块与您在本书前几章中学习构建的 API 非常相似；主要区别在于语言。以前我们使用 JavaScript 和 Node.js 框架 Koa，而在本章中的 API 中，我们使用 PHP 和 PSRs 来创建一个与框架无关的 API。所以，让我们开始吧：

1.  在`/module/`目录中创建两个目录：一个名为`Home`，另一个名为`User`。这两个子目录是 API 中的模块。在每个模块中，创建一个`/_routes/`目录和一个`index.php`文件，该文件将从`/_routes/`目录导入路由，如下所示：

```js

└── module
    ├── Home
    │ ├── index.php
    │ └── _routes
    │ └── hello_world.php
    └── User
        ├── index.php
        └── _routes
           └── ...
```

1.  在`Home`模块中，输出一个“Hello world！”消息，并将其映射到`/`路由，如下所示：

```js
// module/Home/_routes/hello_world.php
use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;

$router->get('/', function (ServerRequestInterface $request) : 
  ResponseInterface {
    return new Zend\Diactoros\Response\JsonResponse(
     'Hello world!');
});
```

1.  在`User`模块中，编写 CRUD 操作，以便我们可以创建、读取、更新和删除用户。因此，在`/_routes/`目录中，创建五个文件，分别为`fetch_user.php`、`fetch_users.php`、`insert_user.php`、`update_user.php`和`delete_user.php`。在这些文件中，我们将在`/Controller/`目录中为每个 CRUD 操作映射路由：

```js
└── User
    ├── index.php
    ├── _routes
    │ ├── delete_user.php
    │ ├── fetch_user.php
    │ └── ...
    └── Controller
        └── ...
```

1.  例如，在`fetch_users.php`文件中，我们将定义一个`/users`路由来列出所有用户，如下所示：

```js
// module/User/_routes/fetch_users.php
use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;

$router->get('/users', function (ServerRequestInterface $request) : ResponseInterface {
    $database = require './core/mysql.php';
    $users = (new Spectre\User\Controller\
     Fetch\Users($database))->fetch();
    return new Zend\Diactoros\Response\JsonResponse($users);
});
```

在这里，您可以看到我们将 Medoo 实例作为`$database`导入，并将其传递给执行**Read**操作的控制器，然后调用`fetch`方法以获取所有可用用户。

1.  接下来，我们要做的是创建一些 CRUD 目录：`Insert`，`Fetch`，`Update`和`Delete`。在每个 CRUD 目录中，我们将把 PSR-4 类存储在`/Controller/`目录中，如下所示：

```js
└── Controller
    ├── Controller.php
    ├── Insert
    │ └── User.php
    ├── Fetch
    │ ├── User.php
    │ └── Users.php
    ├── Update
    │ └── User.php
    └── Delete
        └── User.php
```

1.  首先，创建一个`abstract`类，可以被 CRUD 目录中的类扩展。这个类将只在其构造函数中接受`Medoo\Medoo`数据库，如下所示：

```js
// module/User/Controller/Controller.php
namespace Spectre\User\Controller;

use Medoo\Medoo;

abstract class Controller
{
    protected $database;

    public function __construct(Medoo $database)
    {
        $this->database = $database;
    }
}
```

1.  导入上述`abstract`类并将其扩展到需要连接到 MySQL 数据库的任何其他类中，如下所示：

```js
// module/User/Controller/Fetch/Users.php
namespace Spectre\User\Controller\Fetch;

use Spectre\User\Controller\Controller;

class Users extends Controller
{
    public function fetch()
    {
        $columns = [
            'uuid',
            'name',
            'slug',
            'created_on',
            'updated_on',
        ];
        return $this->database->select('user', $columns);
    }
}
```

在这个类中，我们使用`select`方法从 MySQL 数据库的`user`表中获取所有用户。Medoo 将返回一个包含用户列表的`Array`，如果没有用户，则返回一个空的`Array`。然后，使用`zend-diactoros`中的`JsonResponse`方法将结果转换为 JSON，在`fetch_users.php`文件中。

最后，它将被装饰在`/middleware/`目录中的中间件。这将产生以下输出：

```js
{"status":200,"data":[{"uuid":"...","name":"Jane","slug":"jane",...},{...},{...}]}
```

关于 PHP API 就是这样了。很简单，不是吗？在这个练习中，我们将跳过在 API 端处理 CORS 的任务，因为我们将使用 Nuxt Axios 和 Proxy 模块在我们即将创建的 Nuxt 应用程序中无缝轻松地处理 CORS。所以，让我们开始吧！

您可以在本书的 GitHub 存储库中的`/chapter-16/nuxt-php/proxy/backend/`中找到这个 PHP API，以及这个 API 的其余 CRUD 类在`/chapter-16/nuxt-php/proxy/backend/module/User/Controller/`中。

# 与 Nuxt 集成

`@nuxtjs/axios`模块与`@nuxtjs/proxy`模块很好地集成在一起，在许多情况下非常有用。防止 CORS 问题是使用这两个模块的好处之一。您在第六章中学习了如何安装和使用它们，*编写插件和模块*。让我们回顾一下：

1.  通过 npm 安装`@nuxtjs/axios`和`@nuxtjs/proxy`模块：

```js
$ npm install @nuxtjs/axios
$ npm install @nuxtjs/proxy
```

1.  在 Nuxt 配置文件的`modules`选项中注册`@nuxtjs/axios`，如下所示：

```js
// nuxt.config.js
module.exports = {
  modules: [
    '@nuxtjs/axios'
  ],

  axios: {
    proxy: true
  },

  proxy: {
    '/api/': { target: 'http://0.0.0.0:8181', 
     pathRewrite: {'^/api/': ''} }
  }
}
```

请注意，当您与`@nuxtjs/axios`一起使用`@nuxtjs/proxy`时，不需要注册`@nuxtjs/proxy`模块，只要它已安装并在`package.json`的`dependencies`字段中。

在上述配置中，我们使用`/api/`作为`http://0.0.0.0:8181`的代理，这是我们的 PHP API 运行的地方。因此，每当我们在任何 API 端点请求中使用`/api/`时，它都会调用`0.0.0.0:8181`。例如，假设您正在进行 API 调用，如下所示：

```js
$axios.get('/api/users')
```

`@nuxtjs/axios`和`@nuxtjs/proxy`模块将把`/api/users`端点转换为以下内容：

```js
http://0.0.0.0:8181/api/users
```

但由于我们在 PHP API 的路由中不使用`/api/`，所以我们在配置中使用`pathRewrite`在调用过程中将其`移除`。然后，由`@nuxtjs/axios`和`@nuxtjs/proxy`模块发送到 API 的实际 URL 如下：

```js
http://0.0.0.0:8181/users
```

再次访问以下链接，了解有关这两个模块的更多信息：

+   [`axios.nuxtjs.org/`](https://axios.nuxtjs.org/) 用于`@nuxtjs/axios`

+   [`github.com/nuxt-community/proxy-module`](https://github.com/nuxt-community/proxy-module) 用于`@nuxtjs/proxy`

安装和配置完成后，我们可以开始创建用于与 PHP API 通信的**前端 UI**。我们将在下一节中讨论这个问题。

## 创建 CRUD 页面

再次强调，这对您来说并不是完全新的任务，因为这几乎与您在第九章中学习创建的 CRUD 页面相同，即*添加服务器端数据库*。让我们回顾一下：

1.  在`/pages/users/`目录中创建以下页面以发送和获取数据：

```js
users
├── index.vue
├── _slug.vue
├── add
│ └── index.vue
├── update
│ └── _slug.vue
└── delete
    └── _slug.vue
```

1.  例如，使用以下脚本来获取所有可用用户：

```js
// pages/users/index.vue
export default {
  async asyncData ({ error, $axios }) {
    try {
      let { data } = await $axios.get('/api/users')
      return {
        users: data.data
      }
    } catch (err) {
      // handle errors.
    }
  }
}
```

这个 Nuxt 应用程序中的脚本、模板和目录结构与您在第九章中学习创建的应用程序相同，即*添加服务器端数据库*。不同之处在于在那一章中使用了`_id`，但在这一章中，我们使用`_slug`。到目前为止，您应该能够独立完成其余的 CRUD 页面。但是，您可以随时回顾第九章中的以下部分，*添加服务器端数据库*，以获取更多信息：

+   *创建添加页面*

+   *创建更新页面*

+   *创建删除页面*

创建了这些页面后，可以使用`npm run dev`运行 Nuxt 应用程序。您应该在浏览器上看到应用程序在`localhost:3000`上运行。

你可以在本书的 GitHub 存储库中的`/chapter-16/nuxt-php/proxy/frontend/nuxt-universal/`中找到此应用程序的完整源代码。

如果你不想在这个 Nuxt 应用中使用`@nuxtjs/axios`和`@nuxtjs/proxy`模块，你可以在本书的 GitHub 存储库中的`/chapter-16/nuxt-php/cors/`中找到有关如何在 Nuxt 应用中为 PHP API 启用 CORS 的完整源代码。

你还可以在本书的 GitHub 存储库中的`/chapter-16/nuxt-php/`中找到名为`user.sql`的数据库副本。

现在，让我们总结一下你在这一长章节中学到的东西。我们希望你喜欢这一章，并且觉得它很有启发性。

# 总结

在本章中，你不仅成功地将 Nuxt 应用程序与 API 解耦，类似于你在第十二章中所做的，*创建用户登录和 API 身份验证*，而且还成功地用不同的语言 PHP 编写了一个 API，PHP 是 Web 开发中最流行的服务器端脚本语言之一。你学会了如何安装 PHP 和 Apache 以运行 PHP 应用程序，或者使用内置的 PHP Web 服务器进行开发，同时遵守 PSR-12、PSR4、PSR7 和 PSR-15，以构建一个现代的框架无关的应用程序。你还学会了使用 PHP 数据库框架 Medoo 来编写 CRUD 操作，重用了来自第九章的 Nuxt 应用程序，*添加服务器端数据库*，但进行了一些修改，并完美地将前端 UI 和后端 API 粘合在一起。现在，你还更详细地了解了 HTTP 消息，并知道如何使用 PDO 进行现代 PHP 数据库管理。干得好。

在下一章中，你将了解 Nuxt 在实时应用方面还能做些什么。在那里，你将学习**Socket.io**和**RethinkDB**。我们将带你了解这两种技术的安装过程。然后，你将学习如何在 RethinkDB 数据库中执行实时 CRUD 操作，在 JavaScript 中使用 Socket.io 编写实时代码，并将它们与 Nuxt 应用集成。这将是另一个有趣和激动人心的章节，我们将会引导你完成。所以，敬请关注！
