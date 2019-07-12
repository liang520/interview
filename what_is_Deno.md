# Deno 是什么？他和 nodejs 有什么不同之处？

---

Ryan Dahl 创造 nodejs 的大牛，去年花了一年半的时间在新的项目[Deno](https://deno.land/)上。Deno 是什么呢？一个被认为是修复完善 nodejs 内在问题的新的 JavaScript 运行时的工具。

不要理解错了，在我自己看来 node 是一个很好的服务端 JavaScript 运行时工具，同时也因为它的生态系统的庞大和使用的语言是 JavaScript。然鹅，Dahl 也承认有一些东西在 node 里面是支持很少的——安全性、模块化、依赖性等等。

在他的辩护中，他并没有想到这个平台会在如此短的时间里增长这么快。此外，早在 2009 年，JavaScript 仍然是一种奇怪的小语言，每个人都看不起，而且它的许多功能还没有。

## 什么是 Deno？它有什么主要的特性

Deno 是一个基于 V8 引擎的安全的 Typescript 运行时工具（V8 是谷歌的 JavaScript 运行时引擎）。  
它是由以下组成：

> - Rust——Deno 的核心是用 Rust 写的，就如同 nodejs 是用 c++写的一样。
> - Tokio——Rust 编程语言的异步运行时,提供异步事件驱动平台，构建快速，可靠和轻量级网络应用。利用 Rust 的所有权和并发模型确保线程安全（用 Rust 写的事件循环）
> - Typescript——Deno 支持 JavaScript 和 Typescript(开箱即用)
> - V8——谷歌的 JavaScript 运行时引擎，被用在 Chrome 和 node 中

那我们来看一下 Deno 提供了那些特性。

## 安全性（权限控制）

Deno 最重要的特性是聚焦在安全性上。  
与 nodejs 相反，Deno 默认在沙箱中执行代码，它意味着运行时不能去做一些事情：

- 文件系统的访问
- 网络的访问
- 执行其他文件
- 访问环境变量

一起看一下权限控制系统怎么工作的。

```javascript
(async () => {
  const encoder = new TextEncoder();
  const data = encoder.encode("Hello world\n");

  await Deno.writeFile("hello.txt", data);
  await Deno.writeFile("hello2.txt", data);
})();
```

这个脚本创造了两个文本文件，叫做 hello.txt 和 hello2.txt，里面的内容都是`Hello word`文本。当这个代码在沙箱中开始执行的时候，它是没有权限访问文件系统的。

这个也要注意。我们使用的是 Deno 命名空间，而不是 fs 模块，也正如我们在 nodejs 中，Deno 的命名空间提供了许多基础辅助函数。通过使用命名空间，也会失去浏览器兼容性，这个我们后面讨论。

当我们执行以下命令时：

```bash
deno run write-hello.ts
```

我们会看到如下提示：

```bash
⚠️Deno requests write access to "/Users/user/folder/hello.txt". Grant? [a/y/n/d (a = allow always, y = allow once, n = deny once, d = deny always)]
```

我们在每次调用沙箱的时候都会收到权限控制的提示，当然如果我们选择了`allow always`选项，那就只会提示一次。

如果我们选择了`deny`选项，这个`PermissionDenied`的错误将会抛出，这个进程将会终止退出，因为我们没有任何的容错处理。

如果我们执行脚本的时候使用一下命令：

```bash
deno run --allow-write write-hello.ts
```

将不会有提示，然后两个文本文件会被创建。

这个`--allow-write`标识是对于文件系统可用的，这儿也有`--allow-net`、`--allow-env`、`--allow-run`,它们的作用是允许网络请求、允许访问环境变量、允许运行子进程。

## 模块性

Deno 和浏览器一样，加载模块是通过 URL 的，许多朋友他们第一次看见在服务端通过引入 URL 来加载模块是感到困惑的，但是这个模式也是说的通的——一起来看看：

```typescript
import { assertEquals } from "https://deno.land/std/testing/asserts.ts";
```

通过 URL 引入报有什么好处，你可能会这么说。这个答案是简单的：通过 URL,Deno 的包可以不是集中的聚集在一个中心仓库，比如`npm`。不久前就发生了一些事情，这里就不详细[阐述了。](https://www.youtube.com/watch?v=MO8hZlgK5zc)

通过 URL 引入代码，我们可以让包的创建者把代码托管到他们自己想托管的地方——去中心化。
没有多余的 package.json 和 node_modules。

当我们开始运行一个 Deno 的程序时，他会下载所有的依赖包，并且缓存他们，一旦它们缓存到了本地，Deno 就不会再下载他们了，除非你在运行的时候加上`--reload`符号标识。

这儿有一些比较重要的问题：

### 如果存在包的网站蹦了呢？

由于它不再是中心仓库来管理，存包的网站可能因为一些原因崩了。对于正在开发的项目，这是一个很糟糕的情况，对于已经上线的项目，这更是毁灭性的打击。

正如我们前面提到的，Deno 会缓存模块包，由于缓存是存在我们的本地的硬盘上的，Deno 的创建者建议在我们的版本控制系统（即 git）中检查它并将其保存在存储库中。这样，即使网站崩了，开发者仍然保留了下载的版本。

Deno 将缓存存储在`$DENO_DIR`环境变量下指定的目录中。如果我们不自己设置变量，它将被设置为系统的默认缓存目录。我们可以在我们的本地存储库中的某处设置`$DENO_DIR`并将其检入版本控制系统。

### 每次我都必须通过 URL 来引入嘛？

总是通过 URL 来引入文件，可能会变得特别复杂或者说麻烦，比较好的是，Deno 目前有两个选项提供给我们。  
第一个选项是从本地文件重新导出导入的模块，像这样：

```typescript
export { test, assertEquals } from "https://deno.land/std/testing/mod.ts";
```

我们给上面这个文件命名为`local-test-utils.ts`,现在，如果我们想再次使用`test`或者`assertEquals`函数方法，我们仅仅像这样引入就可以了：

```typescript
import { test, assertEquals } from "./local-test-utils.ts";
```

所以这不是一个大问题，不管是否使用的是 URL 引入的。  
第二个选项是创建一个引入地图文件，更具体地说是一个 JSON 文件：

```json
{
  "imports": {
    "http/": "https://deno.land/std/http/"
  }
}
```

引入的时候，这么引入：

```typescript
import { serve } from "http/server.ts";
```

为了它能够工作，我们必须通过`--importmap`标识告诉 Deno 引入地图文件是什么：

```bash
deno run --importmap=import_map.json hello_server.ts
```

### 那么关于包的版本呢？

版本必须被包的提供者支持，但从客户端来看，它只是在 URL 中设置版本号，如下所示：  
`https://unpkg.com/liltest@0.0.5/dist/liltest.js`。

## 浏览器兼容性

Deno 旨在与浏览器兼容。从技术上讲，在使用 ES 模块时，我们不必再使用任何构建工具（如 webpack 来使我们的应用程序可以在浏览器中使用）。

但是，像 Babel 这样的工具会将代码转换为 ES5 版本的 JavaScript，因此，即使在不支持该语言所有最新功能的旧版浏览器中，代码也可以运行。但这也是以在最终文件中包含大量不必要的代码并使输出文件膨胀为代价的。

由我们决定我们的主要目标是什么，并相应地做出选择。

### TypeScript 支持开箱即用

Deno 使得无需任何配置文件即可轻松使用 TypeScript。当然仍然也可以用纯 JavaScript 编写程序并使用 Deno 执行它们而不会有任何麻烦。

### 摘要

Deno 是 TypeScript 和 JavaScript 的新运行时，是一个有趣的项目，现在已经稳定增长了很长一段时间。但是在它被认为是生产就绪之前还有很长的路要走。

由于它的去中心化特点，它需要从集中式软件包注册表（即 npm）中释放 JavaScript 生态系统。

Dahl 表示他希望在夏季结束前发布 1.0 版本，所以如果你对 Deno 的未来发展感兴趣，那么就 star 它吧。
