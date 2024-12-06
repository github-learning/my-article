## 将概念，说用途，将案例，升华

## 说说对 git hooks 的理解

● Git Hooks 是 Git 提供的一组脚本钩子。这些钩子是一些脚本文件，存储在项目的 .git/hooks 目录下。每个钩子对应一个特定的 Git 生命周期事件，比如 add / commit / push / 提交代码、推送代码、合并分支等。 主要作用就是 代码质量控制
都有哪些 hooks，平时是怎么用的

### Husky

Husky 是一个 Git Hooks 管理工具，它让我们可以更简单地配置和使用 Git Hooks，而无需手动操作 .git/hooks 目录中的脚本。

而且一般在项目里会手动配置
"scripts": {
"prepare": "husky"
},

● 当运行 npm install 时，prepare 脚本会执行 Husky 的初始化过程。
● Husky 将确保 .husky 目录中的 Git Hooks 被正确安装到项目中。
● 这样，在团队协作中，其他开发者在克隆代码并运行 npm install 后，Git Hooks 会自动配置，无需手动操作。

### pre-commit / commit-msg

#### pre-commit

检查和修改即将提交的内容，比如代码格式化或文件检查。通常是搭配 lint-staged（只检查 Git 暂存区的文件，提高代码检测速度） 插件做 代码校验和格式化
npx lint-staged
"lint-staged": {
"src/**/\*.{js,cjs,ts,vue}": [
"npm run lint:fix"
],
"src/**/\*.{html,json,css,scss}": [
"npx prettier --write"
]
}

#### commit-msg

检查提交消息的内容是否符合规范：比如 提交的类型是什么，类型后的格式: 空格。提交内容不能为空，提交多个信息，可以怎么做
npx --no-install commitlint --edit $1

整个工作流程

● 提交代码时，Husky 触发 pre-commit，调用 lint-staged 插件 检查和格式化暂存区文件。
● 在填写提交信息后，Husky 触发 commit-msg，调用 commitlint 插件 校验提交消息格式。
结合 CI / CD 怎么做

## 运行代码质量检查

      - name: Run ESLint and Prettier
        run: |
          npx eslint . --fix
          npx prettier --check .

提交后，GitHub Actions 会自动执行：
● 代码检查：ESLint 和 Prettier。

## eslint 8 相对于 9 的升级

## eslint 检测流程，或者实现原理

## prettier / eslint 主要做什么
