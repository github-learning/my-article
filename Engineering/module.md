## 模块化的概念和作用

- 模块化 就是 CommonJS、AMD、CMD、UMD、ES6 Module 这些方案

- 将代码拆成多个独立，可复用的模块，快的内部数据与实现是私有的，只是向外暴露一些接口与外部模块进行通信

- 主要是提高代码的可维护性和可扩展性，不同的模块化方案在不同的环境中有不同的应用

## 模块化的基本使用

1. 比如 CJS 是主要面向服务端，适用于 `同步加载`，使用 module.exports 和 require 来进行模块的加载

2. AMD 和 CMD 模块化方案，主要是在浏览器端的模块化方案，解决了`commonJS 模块在浏览器端无法实现异步加载的问题`，他们主要是通过 define 和 require 来实现模块的导入导出

3. UMD 是一种`javascript通用模块定义规范`，让你的模块能在 javascript 所有运行环境中发挥作用。主要就是`一个立即执行函数`， 通过判断一些`标识`，来针对不同模块做不同的处理。

### 举个 🌰

- **浏览器环境**：如果没有加载模块加载器（如 RequireJS），UMD 模块会将自身暴露为全局变量（通常是 `window` 或 `global`），从而可以在浏览器中通过全局对象使用这个模块。
- **CommonJS 环境（Node.js）** ：如果模块加载环境是 Node.js 或类似的 CommonJS 环境，UMD 会使用 `module.exports` 或 `exports` 机制来暴露模块。
- **AMD 环境**：如果模块加载器是 AMD（如 RequireJS），UMD 会通过 `define` 方法来定义模块。

### 代码提示

```js
(function (root, factory) {
  if (typeof module === 'object' && typeof module.exports === 'object') {
    console.log('是commonjs模块规范，nodejs环境');
    module.exports = factory();
  } else if (typeof define === 'function' && define.amd) {
    console.log('是AMD模块规范，如require.js');
    define(factory);
  } else if (typeof define === 'function' && define.cmd) {
    console.log('是CMD模块规范，如sea.js');
    define(function (require, exports, module) {
      module.exports = factory();
    });
  } else {
    console.log('没有模块环境，直接挂载在全局对象上');
    root.umdModule = factory();
  }
})(this, function () {
  return {
    name: '我是一个umd模块',
  };
});
```

4. ESM 是 ES6 在语言标准的层面上，实现了模块功能，而且实现得相当简单，。我们的入口文件 index.html，需要制定 type="module",他才会当成 ESM 模块处理，对于 node 环境想用 ESM 可以使用 mjs 的后缀，或者在 package.json 中 配置 type="module"， 即可，他的一个好处就是可以做`tree-shaking`

## ESM 模块为什么可以实现 tree-shaking , 在 webpack 中如何配置，可以实现 tree-shaking

> **不是必须使用 `.mjs` 扩展名**，你可以在 `.js` 文件中使用 ESM 语法，前提是确保你的运行环境或构建工具正确配置了对 ESM 的支持, 比如 type="nodule"

**Tree Shaking** 是一种优化技术，旨在删除代码中未使用的部分，减少最终输出的文件大小。ESM（ECMAScript Modules）模块格式能够实现 Tree Shaking 主要因为以下几个原因：

1.  **静态结构**： ESM 模块（`.mjs` 文件）具有静态的导入导出结构。这意味着，模块的依赖关系可以在编译时被完全分析，而不需要运行时的解析。与 CommonJS 模块的动态 `require()` 调用不同，ESM 的 `import` 和 `export` 语法是静态的，可以在构建时解析模块之间的依赖关系。

    例如，ESM 的 `import { something } from './module';` 语法是静态的，构建工具（如 Webpack、Rollup）可以在编译时知道哪些导入是未被使用的，并将它们删除。

2.  **无副作用（Side Eddect Free） 模块**：模块的导入不会引发任何额外的运行行为

```js
// 有副作用的模块：导入时直接执行代码
console.log('This module runs on import'); // 导入时会输出
globalThis.someGlobalVar = 42; // 修改全局变量
```

```js
// 无副作用的模块：只定义导出内容
export const a = 10;
export function add(x, y) {
  return x + y;
}
```

### 那 ESM 模块可以实现动态导入吗 ？

尽管 ESM 本身强调静态导入，但它也提供了 **动态导入** 的功能。这使得你能够在运行时动态地导入模块，而不是在编译时决定依赖关系。

动态导入是通过 `import()` 函数来实现的，`import()` 是一个 **异步操作**，它返回一个 `Promise`，可以在运行时按需加载模块。这使得动态导入非常适合按需加载和懒加载（lazy loading）。

**示例：**

```
// 动态导入示例
async function loadModule() {
  const module = await import('./module.js');
  module.foo();  // 使用动态导入的模块
}
```

### 在 webpack 中如何配置，可以配置实现 tree-shaking ？

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/6d516eeafed8411f8aa5a1a4a4269e14~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6LW15bCP5bed:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjk2MzkzOTA4MTU4NTQ3OSJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1736607848&x-orig-sign=dpHJYRlDMdHz6DM4sBulOUDU0cQ%3D)

### ES6 模块与 CommonJS（） 模块的差异

1.  CommonJS 模块输出的是一个`值的拷贝`，ES6 模块输出的是`值的引用`；
2.  CommonJS 模块是`运行时加载`，ES6 模块是`编译时输出接口`；

#### CommonJS Code

```js
// lib.js
var counter = 3;
function incCounter() {
  counter++;
}
module.exports = {
  counter: counter,
  incCounter: incCounter,
};

+++++++++++++++++++++++++++

// main.js
var counter = require('./lib').counter;
var incCounter = require('./lib').incCounter;

console.log(counter);  // 3
incCounter();
console.log(counter); // 3
```

#### ESM Code（实时更新）

```js
// lib.js
export let counter = 3;
export function incCounter() {
  counter++;
}

+++++++++++++++++++++++++++

// main.js
import { counter, incCounter } from './lib';
console.log(counter); // 3
incCounter();
console.log(counter); // 4
```

个人理解，CommonJS 对象的导出，在内部做了更精细化的处理，模块化范围更小，小的片段来组合大的功能。而不是大的功能来拆分小的片段

你直接导出一个大对象，只能是克隆了，不然依赖关系你捋不清

相反你导出一个个小片段，利用 tree-shaking , 又能很好的做数据实时更新，

## 几种模块化方案的对比

**最主要的是，他是静态分析**：ES6 模块在编译时就已经确定了模块的依赖关系，这个时候就可以支持树摇优化（Tree Shaking），可以剔除未使用的代码，减少打包后的代码体积。

| 特性              | **CommonJS**     | **AMD**             | **CMD**             | **UMD**                 | **ES6 Module**             |
| ----------------- | ---------------- | ------------------- | ------------------- | ----------------------- | -------------------------- |
| **导入方式**      | `require`        | `require`/ `define` | `require`/ `define` | `define`                | `import`                   |
| **导出方式**      | `module.exports` | `define`            | `module.exports`    | 自定义                  | `export`/ `export default` |
| **同步/异步加载** | 同步             | 异步                | 异步                | 异步                    | 异步 (动态导入支持)        |
| **适用环境**      | Node.js          | 浏览器              | 浏览器              | 浏览器 / Node.js / 全局 | 浏览器 / Node.js           |
| **依赖声明方式**  | 文件级别         | 提前声明依赖        | 运行时声明依赖      | 兼容多种方式            | 静态分析                   |
| **跨环境支持**    | 仅 Node.js       | 仅浏览器            | 仅浏览器            | 浏览器 / Node.js / 全局 | 浏览器 / Node.js           |

## 升华

### 自研组件库打包，对你多模块打包

一些三方库，如果是给浏览器使用基本都是打三个包，**mjs,cjs，umd**，让库或者模块在 js 的所有环境都可以运行。比如我们的自研组件库是为项目服务的，通过 gulp 和 roullp 进行打包，输出三种格式的包供使用者选择

在项目根目录下创建 `rollup.config.js` 文件，并根据需求配置不同的输出格式。以下是一个典型的配置示例，支持 **UMD**, **ESM (mjs)** 和 **CommonJS (cjs)** 格式。

```js
import path from 'path';
import { terser } from 'rollup-plugin-terser'; // 用于压缩代码
import resolve from '@rollup/plugin-node-resolve'; // 解析node_modules中的依赖
import commonjs from '@rollup/plugin-commonjs'; // 转换CommonJS模块为ES模块
import babel from '@rollup/plugin-babel'; // Babel插件，用于转译ES6+代码
import pkg from './package.json'; // 获取package.json的内容

export default {
  input: 'src/index.js', // 入口文件
  external: ['react', 'react-dom'], // 这里是你希望在UMD中排除的外部依赖，例如 React 和 ReactDOM
  plugins: [
    resolve(), // 启用 node_modules 模块解析
    commonjs(), // 转换CommonJS模块
    babel({
      exclude: 'node_modules/**', // 不转译node_modules中的文件
      babelHelpers: 'bundled', // 使用Rollup内建的babel-helper
    }),
    terser(), // 压缩代码，适用于生产环境
  ],
  output: [
    {
      file: path.resolve('dist/index.umd.js'), // 输出UMD格式
      format: 'umd', // 模块格式
      name: 'MyLibrary', // UMD格式需要暴露一个全局变量的名字
      globals: {
        react: 'React', // 外部依赖的全局变量
        'react-dom': 'ReactDOM',
      },
      sourcemap: true, // 开启源映射文件
    },
    {
      file: path.resolve('dist/index.esm.js'), // 输出ES模块（mjs）
      format: 'esm', // 模块格式
      sourcemap: true, // 开启源映射文件
    },
    {
      file: path.resolve('dist/index.cjs.js'), // 输出CommonJS格式
      format: 'cjs', // 模块格式
      sourcemap: true, // 开启源映射文件
    },
  ],
};
```

### vite 的 出现和 ESM module 标准的 实现 离不开

因为模块化，最终的归宿也是 dist， 我们对于项目的加载快慢，大部分原因取决于你 dist 的大小，而 ES module 的模块，浏览器会把他当成一个 js 去下载，根本就不需要打包了，这样就会大大增加项目的加载速度，像之前大的一些包，做成外链的形式，也是这个意思，不让他存在于包中，而是用到的时候去加载

因为 vite 中是 bundless，

## 番外知识补充

## 1. 什么是编译时，什么运行时 ？

### 1️⃣ **运行时（Runtime）与编译时（Compile-time）**

#### **编译时（Compile-time）**

- **定义**：编译时是指程序在被执行之前，由编译器进行转换、分析和优化的阶段。此时，`代码还没有被执行`。
- **过程**：在编译时，源代码（例如 JavaScript、TypeScript 或其他语言的代码）会被编译成机器码（在某些情况下为中间代码或字节码），并准备好执行。编译器在这一过程中可以进行静态分析、优化（例如消除冗余、代码压缩）等工作。

- **举例**：对于 JavaScript 项目，构建工具（如 Webpack、Rollup、Vite）会在编译时进行模块打包、压缩、Tree Shaking 等操作。

#### **运行时（Runtime）**

- **定义**：运行时是指程序开始执行后，代码实际运行并与外部环境交互的阶段。它包括程序的执行、内存管理、事件处理等。
- **过程**：在运行时，程序的代码已经被加载到内存中，并开始实际的执行。此时，程序可能会根据输入、外部事件或用户操作来进行动态的行为，如 DOM 操作、网络请求、动态模块加载等。
- **举例**：JavaScript 中的事件循环、动态导入（`import()`）、API 调用等都属于运行时操作。

### 2️⃣ **编译时与运行时的区别**

| **维度**     | **编译时**                              | **运行时**                          |
| ------------ | --------------------------------------- | ----------------------------------- |
| **定义**     | 编译器或构建工具在程序执行前进行的操作  | 程序实际执行的阶段                  |
| **工作内容** | 静态分析、优化、代码转换                | 执行代码、动态加载、事件处理等      |
| **示例**     | Webpack 打包、TypeScript 编译、CSS 压缩 | JavaScript 执行、API 请求、事件监听 |
| **时机**     | 程序开始运行之前                        | 程序开始运行后                      |

### 3️⃣ **总结**

- **编译时**：Tree Shaking 发生在编译阶段，构建工具会静态分析代码并移除未使用的部分。只有在编译时，构建工具能够准确地识别哪些代码是多余的。

- **运行时**：运行时是代码执行的阶段，这时的代码已经被加载到内存中，并开始执行。Tree Shaking 无法在运行时进行，因为它依赖于静态分析，无法在代码执行后动态地移除不需要的代码。

### 比如我在编译时，对文件进行压缩，代码丑化，在运行时为什么还可以识别到正确的代码并执行?

- JavaScript 引擎在运行时根据语法规则和执行上下文来识别和执行代码，压缩或丑化仅仅改变了代码的外观，而不会影响其行为

- 在我们的`监控平台`中，会使用 Webpack / Rollup / Vite 结合源映射（Source Maps）技术，在压缩和丑化过程中生成映射文件，以便开发人员在调试时能看到原始代码（尽管用户看到的是压缩或丑化后的代码）。

# pnpm 的理解，以及为什么快

pnpm 中 p 就是 performance 的意思，就是快，性能好，
性能好，主要有以下两个原因：

- 采用软链的机制，节省磁盘空间
- 锁文件更小，更加精确高效管理依赖
- 树形结构 更安全，避免幽灵依赖
- 采用软连 的机制，将项目依赖安装在全局的 store 中，然后在项目中创建硬连接（，相比于 npm , 每个项目都要安装实在的，这样就能节省磁盘空间）

pnpm 的锁文件更小，更进准，可以显著提升安装效率

他是的包依赖是树形结构，相比于 npm 扁平化包管理，可能会出现一些幽灵依赖（比如我有一个想用 lodash 的包，但是项目里既有 lodash / 也有 lodash-es， 那我就去项目里找了，发现发现，我寻思，lodash-es 是什么，就想去 node_modules 去看看，然后没有发现，再看 lodash / 我意识到幽灵依赖了，去 vue 源码看了，有 lodash, 但是没 lodash/-es 然后又去 el, 既有 lodash / lodash-es ，就一个包，查找了两个 vue 源码/ el 源码，才知道 xxx. 要是有一天，挂掉了，存在的潜在危险 ⚠️。

最后也是了解了 lodash-es

Lodash 的 ES 模块版本，主要用于现代浏览器和支持 ES Module 的环境。
它的设计旨在支持 Tree Shaking 和按需加载。（静态导入导出，import 和 export 是顶层语句，不能在运行时动态更改。无副作用特性（纯函数），） 4. 传统 lodash 就是 cjs, 可以通过一些插件， 5. 6. lodash-webpack-plugin ，可以将 7. 插件会将代码转化为按需引入的形式，相当于 const debounce = require('lodash/debounce');
然后想看看使用的是什么 npm 包， 但是发现没有，最后看了，两个地方使用的包还不一样，）

pnpm-lock.yaml 只记录包的哈希值和必要的元数据，而不重复记录每个包的详细路径或版本信息。

pnpm 锁文件更小，因为它避免了冗余的路径和版本记录，采用内容寻址和符号链接机制，精确且高效地管理依赖。
更高效的安装：由于全局缓存和符号链接，pnpm 安装依赖时的效率比 npm 高得多，尤其是在多次安装相同依赖或大项目中。

```js
{
  "dependencies": {
    "lodash": {
      "version": "4.17.21",
      "resolved": "https://registry.npmjs.org/lodash/-/lodash-4.17.21.tgz",
      "integrity": "sha512-pb9zR5lduR...",
      "dev": true
    },
    "axios": {
      "version": "0.21.1",
      "resolved": "https://registry.npmjs.org/axios/-/axios-0.21.1.tgz",
      "integrity": "sha512-1y4G8djRmZ3...",
      "dev": false
    }
  }
}

```

```js
lockfileVersion: 5.4
dependencies:
  lodash: 4.17.21
  axios: 0.21.1
packages:
  /lodash/4.17.21:
    resolution: { integrity: "sha512-pb9zR5lduR..." }
  /axios/0.21.1:
    resolution: { integrity: "sha512-1y4G8djRmZ3..." }

```

npm 扁平化 ，幽灵依赖，但是一个项目有问题，能单独改包

pnpm 全局仓库，一个有问题，都有影响，但是树接口好

## 那你聊聊上面提到的两个项目？

### `自研组件库 / 监控平台？`

## 升华

一些三方库，如果是给浏览器使用基本都是打三个包，**mjs,cjs，umd**，让库或者模块在 js 的所有环境都可以运行。比如我们的自研组件库是为项目服务的，通过 gulp 和 roullp 进行打包，输出三种格式的包供使用者选择

在项目根目录下创建 `rollup.config.js` 文件，并根据需求配置不同的输出格式。以下是一个典型的配置示例，支持 **UMD**, **ESM (mjs)** 和 **CommonJS (cjs)** 格式。

```js
import path from 'path';
import { terser } from 'rollup-plugin-terser'; // 用于压缩代码
import resolve from '@rollup/plugin-node-resolve'; // 解析node_modules中的依赖
import commonjs from '@rollup/plugin-commonjs'; // 转换CommonJS模块为ES模块
import babel from '@rollup/plugin-babel'; // Babel插件，用于转译ES6+代码
import pkg from './package.json'; // 获取package.json的内容

export default {
  input: 'src/index.js', // 入口文件
  external: ['react', 'react-dom'], // 这里是你希望在UMD中排除的外部依赖，例如 React 和 ReactDOM
  plugins: [
    resolve(), // 启用 node_modules 模块解析
    commonjs(), // 转换CommonJS模块
    babel({
      exclude: 'node_modules/**', // 不转译node_modules中的文件
      babelHelpers: 'bundled', // 使用Rollup内建的babel-helper
    }),
    terser(), // 压缩代码，适用于生产环境
  ],
  output: [
    {
      file: path.resolve('dist/index.umd.js'), // 输出UMD格式
      format: 'umd', // 模块格式
      name: 'MyLibrary', // UMD格式需要暴露一个全局变量的名字
      globals: {
        react: 'React', // 外部依赖的全局变量
        'react-dom': 'ReactDOM',
      },
      sourcemap: true, // 开启源映射文件
    },
    {
      file: path.resolve('dist/index.esm.js'), // 输出ES模块（mjs）
      format: 'esm', // 模块格式
      sourcemap: true, // 开启源映射文件
    },
    {
      file: path.resolve('dist/index.cjs.js'), // 输出CommonJS格式
      format: 'cjs', // 模块格式
      sourcemap: true, // 开启源映射文件
    },
  ],
};
```
