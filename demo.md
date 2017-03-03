##本文档就是本人理解各种规范用（建议学过开发的人看）
###模块系统主要解决模块的定义、依赖和导出
##下面开始各种模块系统的演变
### script标签
<script src="module1.js"></script>
<script src="module2.js"></script>
这是最原始的 JavaScript 文件加载方式，如果把每一个文件看做是一个模块，
那么他们的接口通常是暴露在全局作用域下，也就是定义在 window 对象中，
不同模块的接口调用都是一个作用域中，一些复杂的框架，会使用命名空间的概念来组织这些模块的接口，
典型的例子如 YUI 库

这种原始的加载方式暴露了一些显而易见的弊端：

全局作用域下容易造成变量冲突

文件只能按照 script 的书写顺序进行加载

开发人员必须主观解决模块和代码库的依赖关系

在大型项目中各种资源难以管理，长期积累的问题导致代码库混乱不堪

###CommonJS
服务器端的 Node.js 遵循 CommonJS规范，该规范的核心思想是允许模块通过 require 方法来同步加载所要依赖的其他模块，
然后通过 exports 或 module.exports 来导出需要暴露的接口。
require("module");
require("../file.js");
exports.doStuff = function() {};
module.exports = someValue;
优点：

服务器端模块便于重用

NPM 中已经有将近20万个可以使用模块包

简单并容易使用

缺点：

同步的模块加载方式不适合在浏览器环境中，同步意味着阻塞加载，浏览器资源是异步加载的
不能非阻塞的并行加载多个模块

实现：

服务器端的 Node.js

Browserify，浏览器端的 CommonJS 实现，可以使用 NPM 的模块，但是编译打包后的文件体积可能很大

modules-webmake，类似Browserify，还不如 Browserify 灵活

wreq，Browserify 的前身

###AMD
Asynchronous Module Definition 规范其实只有一个主要接口 define(id?, dependencies?, factory)，
它要在声明模块的时候指定所有的依赖 dependencies，并且还要当做形参传到 factory 中，对于依赖的模块提前执行，依赖前置。
define("module", ["dep1", "dep2"], function(d1, d2) {
  return someExportedValue;
});
require(["module", "../file"], function(module, file) { /* ... */ })
优点：

适合在浏览器环境中异步加载模块

可以并行加载多个模块

缺点：

提高了开发成本，代码的阅读和书写比较困难，模块定义方式的语义不顺畅

不符合通用的模块化思维方式，是一种妥协的实现

实现：

RequireJS，
curl

###CMD
Common Module Definition 规范和 AMD 很相似，尽量保持简单，并与 CommonJS 和 Node.js 的 Modules 规范保持了很大的兼容性。
define(function(require, exports, module) {
  var $ = require('jquery');
  var Spinning = require('./spinning');
  exports.doSomething = ...
  module.exports = ...
})
优点：

依赖就近，延迟执行；
可以很容易在 Node.js 中运行

缺点：

依赖 SPM 打包，模块的加载逻辑偏重

实现：

Sea.js；
coolie

###ES6 模块
EcmaScript6 标准增加了 JavaScript 语言层面的模块体系定义。ES6 模块的设计思想，是尽量的静态化，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。
CommonJS 和 AMD 模块，都只能在运行时确定这些东西。
import "jquery";
export function doStuff() {}
module "localModule" {}
优点：

容易进行静态分析

面向未来的 EcmaScript 标准

缺点：

原生浏览器端还没有实现该标准

全新的命令字，新版的 Node.js才支持

实现：

Babel

##理想的模块化
可以兼容多种模块风格，尽量可以利用已有的代码，不仅仅只是 JavaScript 模块化，还有 CSS、图片、字体等资源也需要模块化。
