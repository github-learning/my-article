## 什么是工程化,你做过哪些工程化相关的事情

工程化是以一种便自动化，脚本化，结合一些工具的能力，解决一些纯人工处理的低效，非标准的问题，提升效率，质量，性能

## 你做过哪些工程化的事情

### 项目基建搭建

- 代码规范 prettier / eslint
- git 规范 git hooks
- 单元测试 jest
- 工程架构 monorepe + pnpm npm script
- 项目自动部署，发布 ci / cd
- webpack / vite
- 脚本实现多语言 / 多主题解析
- 性能处理
- ast / babel
- micro-app
- ssr
- esm

## babel 的主要流程是什么

- parser(把源码转化成 ast 语法树｜ @babel/parser) ->
- transform（遍历 AST,对 AST 进行必要的改造 | @babel/traverse 遍历 AST, 最后调用 visitor 修改 ast, | @babel/types 创建 修改 删除 需要用到的 types ）
- generate: 把改造后的代码，生成目标代码，以及 sourcemap @babel/generate

## 为什么需要模块化

- 变量命名污染
- 内聚且私有的，不能被外部污染的
- 对于模块的设计，更加清晰，怎么引入其他模块，怎么暴露接口给其他模块
- 可以有效解决依赖顺序的问题
- 多人并行，怎么通过接口，组织方式，来实现代码开发
- esm ,cjs, amd, cmd, umd
