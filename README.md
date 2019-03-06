# 面试题集合

- 写 React / Vue 项目时为什么要在组件中写 key，其作用是什么?

- ['1', '2', '3'].map(parseInt) what & why ?

- 什么是防抖和节流？有什么区别？如何实现？

- 介绍下 Set、Map、WeakSet 和 WeakMap 的区别？

- 介绍下深度优先遍历和广度优先遍历，如何实现？

- 请分别用深度优先思想和广度优先思想实现一个拷贝函数？

- ES5/ES6 的继承除了写法以外还有什么区别？

- setTimeout、Promise、Async/Await 的区别?

- Async/Await 如何通过同步的方式实现异步?

- 请写出下面代码的运行结果:

```javascript
async function async1() {
  console.log("async1 start");
  await async2();
  console.log("async1 end");
}
async function async2() {
  console.log("async2");
}
console.log("script start");
setTimeout(function() {
  console.log("setTimeout");
}, 0);
async1();
new Promise(function(resolve) {
  console.log("promise1");
  resolve();
}).then(function() {
  console.log("promise2");
});
console.log("script end");
```

- 已知如下数组：

```javascript
var arr = [[1, 2, 2], [3, 4, 5, 5], [6, 7, 8, 9, [11, 12, [12, 13, [14]]]], 10];
```

> 编写一个程序将数组扁平化去并除其中重复部分数据，最终得到一个升序且不重复的数组

- JS 异步解决方案的发展历程以及优缺点。

- Promise 构造函数是同步执行还是异步执行，那么 then 方法呢？

- 如何实现一个 new?

- 简单讲解一下 http2 的多路复用?

- 谈谈你对 TCP 三次握手和四次挥手的理解?

- express 和 koa 的对比，两者中间件的原理，koa 捕获异常多种情况说一下

- 你项目里用到第三方登录涉及的 oAuth(JWT)协议的实现原理，以及你本地的实现原理，第三方登录怎么样保证安全性

- 说下快排完整性

- react 和 vue 的区别，你开发如何选择技术栈

- express 里面登录的 session 服务怎么样实现分布式服务

- vue 的理解、vue 的双向数据绑定的原理、vue 怎么样实现数组绑定

- js 的继承

- call 和 apply 的区别

- ajax 是同步还是异步，怎么样实现同步、ajax 实现过程

- 闭包的作用理解，以及那些地方用过闭包，以及闭包的缺点，如何实现闭包

- 跨域方法以及怎么样实现的与原理

- 数组去重

- 快排和冒泡原理

- 为什么 css 样式初始化，目的是为了什么

- 为什么浏览器会产生同源策略

- axios 有什么特点

- cookie 和 webstrage 的区别以及 cookie 怎么样使用？原生 cookie 怎么样封装

* 浏览器那些地方用到了异步

* css 弹性布局，那些地方用到过

* position 属性有哪些值，分别有什么含义

* const 和 let 的区别，可以改变 const 定义对象某个属性吗

* 箭头函数，箭头函数的特点

* js 的 this 理解， 如何改变 this 的指向

* cookie 有什么限制

* js 的事件机制

* settimeout 的机制

* 内存泄露的排除定位和解决方法

* 垃圾回收机制

* websocket 实现原理

* 缓存机制，协商协议

* 异步 es5 es6 es7 分别怎么样解决

* 从输入一个 url 到浏览器页面展示都经历了哪些过程

* new 生成了一个对象的过程（核心 return this）

* 请简单说明什么是事件冒泡和事件捕获以及事件委托

* es6 有了解吗，请简单说一下 promise 机制，异步的承诺机制，顺势说一下解决回调地狱的问题

* 手写一下深拷贝，答案提示：JSON.parse(JSON.stringify(obj)) 用 JSON 实现深拷贝

- get 和 post 的请求区别

- 什么情况算是跨域，如何解决跨域问题

- 尾递归问题

- 讲下如何负载均衡

- mysql 的底层引擎，发布

- mysql 语言写的如何，怎么样判断 sql 语句的性能？如何优化？

- 登录原理

- var 的变量提升底层原理是什么？

- websock 的底层原理讲讲

- webpack 的原理

- new 和 Object.create 的区别

- 哪种情况下**proto**和 prototype 的指向是同一个？

- typeof array null undefined NaN 分别是什么

- 把 undefined 和 null 转成 Number 分别是什么

- 如何判断是否为数组？（instanceOf 和 constructor 可以，但是有原型链断裂的风险，Object.toString.call()最稳定）

- 如果让你实现一个 promise 怎么样实现

- redux 原理讲讲

- 了解 web 移动开发吗，移动端适配方案有哪些

- 你有 Native 开发经验吗，讲下 Android 如何调用页面的资源

- margin 坍塌

- BFC 原理

- ES6 的代理是什么

- 前端安全这块了解多少

- 写一个 API，实现 jQuery 的\$(selector)选择器，要求兼容 IE6

- 浏览器是如何实现通过你的代码去找到指定的元素的

- 用 JS 模拟一个双向链表

- Nodejs 的线程管理

- 说说 bind，apply，call 的区别以及 bind 的实现

- react 的 virtual DOM 和 Diff 算法

- React 的生命周期

- Vue 的生命周期

- boostrap 的底层原理

- 图片压缩的原理

- 如何处理高并发的情况下，用户顺序问题

- webpack 的插件大致流程

- 前端监控，pm2，如果我服务器挂了，如何快速发现并且定位错误

- pm2 除了监控还能干什么，如何实现

- express 中间件如何实现

- 了解 TCP 吗，数据结构简单介绍一下以及你的想法

- webpack 项目太大了怎么办

- vuex 的状态管理的原理是什么

- 如何在浏览器端和原生端的代码复用，讲到 weex 又简单讲了它的原理

- 操作系统进程和线程的区别

- 线程的哪些资源共享，哪些资源不共享

- 设计模式有哪些

- Linux 命令用的多吗，怎么样进行进程间通信

- hashmap 原理

- koa generator 能讲一下吗

- hybrid

- webkit 内核渲染页面过程

- react 首次渲染过程

- 给你一颗树，如何找到其公共的父节点

- rem，px，em 的区别

- 使用过的 koa2 中间件
- koa-body 原理
- 介绍自己写过的中间件
- 有没有涉及到 Cluster
- 介绍 pm2
- master 挂了的话 pm2 怎么处理
- 如何和 MySQL 进行通信
- React 声明周期及自己的理解
- 如何配置 React-Router
- 路由的动态加载模块
- 服务端渲染 SSR
- 介绍路由的 history
- 介绍 Redux 数据流的流程
- Redux 如何实现多个组件之间的通信，多个组件使用相同状态如何进行管理
- 多个组件之间如何拆分各自的 state，每块小的组件有自己的状态，它们之间还有一些公共的状态需要维护，如何思考这块
- 使用过的 Redux 中间件
- 如何解决跨域的问题
- 常见 Http 请求头
- 移动端适配 1px 的问题
- 介绍 flex 布局
- 其他 css 方式设置垂直居中
- 居中为什么要使用 transform（为什么不使用 marginLeft/Top）
- 使用过 webpack 里面哪些 plugin 和 loader
- webpack 里面的插件是怎么实现的
- dev-server 是怎么跑起来
- 项目优化
- 抽取公共文件是怎么配置的
- 项目中如何处理安全问题
- 怎么实现 this 对象的深拷贝
- 介绍 redux，主要解决什么问题
- 文件上传如何做断点续传
- 表单可以跨域吗
- promise、async 有什么区别
- 搜索请求如何处理（防抖）
- 搜索请求中文如何请求
- 介绍观察者模式
- 介绍中介者模式
- 观察者和订阅-发布的区别，各自用在哪里
- 介绍 react 优化
- 介绍 http2.0
- 通过什么做到并发请求
- http1.1 时如何复用 tcp 连接
- 介绍 service worker
- 介绍 css3 中 position:sticky
- redux 请求中间件如何处理并发
- 介绍 Promise，异常捕获
- 介绍 position 属性包括 CSS3 新增
- 浏览器事件流向
- 介绍事件代理以及优缺点
- React 组件中怎么做事件代理
- React 组件事件代理的原理
- 介绍 this 各种情况
- 前端怎么控制管理路由
- 使用路由时出现问题如何解决
- React 怎么做数据的检查和变化

- react-router 怎么实现路由切换
- react-router 里的`<Link>`标签和`<a>`标签有什么区别
- `<a>` 标签默认事件禁掉之后做了什么才实现了跳转

- React 层面的性能优化
- 整个前端性能提升大致分几类
- import { Button } from 'antd'，打包的时候只打包 button，分模块加载，是怎么做到的
- 使用 import 时，webpack 对 node_modules 里的依赖会做什么
- JS 异步解决方案的发展历程以及优缺点
- Http 报文的请求会有几个部分
- cookie 放哪里，cookie 能做的事情和存在的价值

- cookie 和 token 都存放在 header 里面，为什么只劫持前者

- cookie 和 session 有哪些方面的区别

- React 中 Dom 结构发生变化后内部经历了哪些变化

- React 挂载的时候有 3 个组件，textComponent、composeComponent、domComponent，区别和关系，Dom 结构发生变化时怎么区分 data 的变化，怎么更新，更新怎么调度，如果更新的时候还有其他任务存在怎么处理

- key 主要是解决哪一类的问题，为什么不建议用索引 index（重绘）
- Redux 中异步的请求怎么处理
- Redux 中间件是什么东西，接受几个参数（两端的柯里化函数）
- 柯里化函数两端的参数具体是什么东西
- 中间件是怎么拿到 store 和 action，然后怎么处理
- state 是怎么注入到组件的，从 reducer 到组件经历了什么样的过程
- koa 中 response.send、response.rounded、response.json 发生了什么事，浏览器为什么能识别到它是一个 json 结构或是 html
- koa-bodyparser 怎么来解析 request
- webpack 整个生命周期，loader 和 plugin 有什么区别
- 介绍 AST（Abstract Syntax Tree）抽象语法树
- 安卓 Activity 之间数据是怎么传递的
- 安卓 4.0 到 6.0 过程中 WebView 对 js 兼容性的变化
- WebView 和原生是如何通信
- 跨域怎么解决，有没有使用过 Apache 等方案
- 对 async、await 的理解，内部原理
- 介绍下 Promise，内部实现
- 清除浮动
- 定位问题（绝对定位、相对定位等）
- 从输入 URL 到页面加载全过程
- tcp3 次握手
- tcp 属于哪一层（1 物理层 -> 2 数据链路层 -> 3 网络层(ip)-> 4 传输层(tcp) -> 5 应用层(http)）
- redux 的设计思想
- 接入 redux 的过程
- 绑定 connect 的过程
- connect 原理
- webpack 介绍
- == 和 ===的区别，什么情况下用相等==
- bind、call、apply 的区别
- 动画的了解
- 介绍下原型链（解决的是继承问题吗）
- 对跨域的了解

[其他相关面试题](https://zhuanlan.zhihu.com/p/48827292)
