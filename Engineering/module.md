## 模块化的概念和作用

- 模块化 就是 CommonJS、AMD、CMD、UMD、ES6 Module 这些方案

- 将代码拆成多个独立，可复用的模块，快的内部数据与实现是私有的，只是像外暴露一些接口与外部模块进行通信

- 主要是提高代码的可维护性和可扩展性，不同的模块化方案在不同的环境中有不同的应用

## 模块化的基本使用

1. 比如 CJS 是主要面向服务端，适用于 同步加载，使用 module.exports 和 require 来进行模块的加载

2. AMD 和 CMD 模块化方案，主要是在浏览器端的模块化方案，解决了 commonJS 模块在浏览器端无法实现异步加载的文同，他们主要是通过 define 和 require 来实现模块的导入导出

3. UMD 是一种 javascript 通用模块定义规范，让你的模块能在 javascript 所有运行环境中发挥作用。主要就是`一个立即执行函数`， 通过判断一些标识，来针对不同模块做不同的处理。

### 🌰

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

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/6d516eeafed8411f8aa5a1a4a4269e14~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6LW15bCP5bed:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMjk2MzkzOTA4MTU4NTQ3OSJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1735657926&x-orig-sign=eVqxskd1IgCbNKPrM8IFxeqOfEg%3D)

### ES6 模块与 CommonJS 模块的差异

1.  CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用；
2.  CommonJS 模块是运行时加载，ES6 模块是编译时输出接口；

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

#### ESM Code

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
