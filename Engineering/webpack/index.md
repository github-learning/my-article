# webpack 打包优化

## 对构建工具的理解

## webpack 构建流程

## loader

## plugin

## 优化构建速度

优化搜索文件
缩小文件搜索范围
resolve： alias（别名）, extensions, modules (制定模块搜索目录)

noParse, 可以用来排出对非模块化库文件的解析 jq, 或者cdn / xx.min.js 的文件都可以不打包lodash

配置loaders 使用test ,exclude ,include , 来缩小搜索范围

避免重复编译三方库
把三方库单独打包到一个文件中，这个文件就是一个单独的依赖库，这个依赖库不会跟着业务代码一起被重新打包，只有当自身依赖发生变化时，才会重新打包
DllPlugin / DllRerencePlugin

使用 HappyPack 开启多进程Loader 转换
loader 对文件转换耗时，js 是单线程 模型，只能一个一个文件进行处理，需要通过happyPack 将任务分解给多个子进程，最后将结果发送给主进程，并进行处理

ParalleUglifyPlugin 开启多进程压缩文件

优化开发体验
开启模块热替换： 模块热替换不刷新整个网页，而是只重新编译发生变化的模块，并用新的模块替代老的模块，所欲预览反应更快，等待时间更少
优化输出质量
压缩文件体积
通过DefinePlugin 区分环境，减少生产环境的代码体积
压缩资源： css / js /图片
tree Shaking：
分离第三方文件： externals , 将三方库使用cdn 加载，而不是打包到bundle 中
gzip 压缩
iconfont / base64 / cdn / 赖加载 / 预加载 / 渐进shi

loader 实现
解析文件，使正常执行
本质上是一个函数，对各种后坠名的文件进行代码转换
实现上，会传入一个source 标识当前匹配到的资源，如果资源存在，会进行一些正则替换，bable 转译，

如果有options ，会调用this.getOptions() 方法，来获取传递进来的参数，对处理函数，进行更细的控制，如果是一步，在每次执行完，都会行进callback

module.
最后返回js 代码

plugin
清除dist 目录
拷贝不需要打包的文件到输出目录
压缩输出代码
webpack 在运行生命周期会广播出许多事件，plugin 监听事件， 在特定阶段钩入想要添加的自定义内容

通过tapable 事件流机制，保证插件的有序 性，

complier 暴露钩子 整个生命周期
compliation 暴露与模块和依赖有关的，粒度更小的事件钩子

plugin 需要再原型上绑定apply 方法，才能访问compiler 实例

插件接受compiler。compliation 都是痛一个引用

找到合适的时间点

emit 事件发生时，读取到最终输出的资源，模块，以及依赖，并进行修改
watch-run: 当依赖的文件发生变化除法

一个函数 或者包含apply的函数/ 方法
compiler.hooks.emit.tap('xxx', compilation=>{
compilation.assets
source:
size:
})

babel 原理（编译）
编译器
解析： 将代码转换成AST词法分析：分割成token
语法分析：分析token 流 ，生成AST

转换： 访问AST 节点，变换操作，生成新的AST
生成： 以新的AST 为基础，生成代码

source map
将编译，打包，压缩后的代码，映射成源码的过程
调试源代码，就需要source-map

线上环境 3 中处理方案
hidden-souce-map 借助sentry
no-sources-source-map : 显示具体行数，以及查看源代码错站，
sourcemap: nginx 将.map 文件只对白名单开放
