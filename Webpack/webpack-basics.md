# Webpack Basics

## Webpack

Webpack is a static module bundler for modern JavaScript applications. When webpack processes your application, it internally builds a dependency graph which maps every module your project needs and generates one or more bundles.

// TODOS
[ ] 什么是 static module bundler，是指需要 bundle 的资源是静态的吗
[ ] 那么对应什么是 dynamic module? 有动态的 module 吗，还是指后端生成的页面这种资源是动态的
[ ] dependency graph
[ ] webpack4 已经存在默认的 config 文件了，可以什么都不写就是用。默认的 config 都包括什么。w4 的默认 config 和 w5 的默认有什么不同吗？ (https://v4.webpack.js.org/configuration/)

## Core concepts

1. entry: 入口文件，告知 webpack 从哪里开始找出所有文件的依赖，解析并构建 dependency graph，最终输出 bundle。可以有一个或者多个入口
   默认值: './src/index.js'
   // TODOS
   [ ] 什么是 context
   [ ] webpack supports dynamic entry, a function/promise, 思考这可以做什么为我们的项目？假设 webpack 可以获取一些 nodejs 的环境变量，那么还可以做什么通过 dynamic entry
   [ ] 怎么解析/查找使用 alias 的 module
2. output: 告知 webpack 生成的 bundle 文件应该放在哪里，叫什么名字
   默认值: './dist'
   // TODOS:
   [ ] css 是怎么处理成 js module 的？怎么看 dev mode 下的结果
   [ ] 了解基本的关于 nodejs path.resolve 和\_\_dirname
   [ ] output 的配置https://v4.webpack.js.org/configuration/output/
3. loaders/module: by default, webpack only understand js and json. loader allows webpack to process other type files and convert them into js modules. 所以 loader 的主要工作就是将其他 webpack 不懂的文件内容翻译成 webpack 可以理解的内容。比如 css It runs before add the module to final output
   注意: test 属性的值为`/\.txt$/`不要 wrap with single/double quotes
   // TODOS:
   [ ] 编写一个简单的 loader，了解流程和常用的 api

4. plugins: 帮助 webpack 处理除了翻译外的更多工作，比如优化，asset management.
   // TODOS:
   [ ] 自己编写一个简单的 plugins，了解流程和常用的 api
   [ ] plugin 和 loader 有特定的出发顺序吗？
   注意: loaders 是不需要在 config 文件中引入的，但是 plugins 需要，而且常常要 new 一个 plugin

5. mode/environments
   默认值: "production"
   可选值: "production", "development", "none" // 对应不同的 built-in 的优化处理
6. browser compatibility
   ie9+
   ie8 & <8 也可以通过 polyfill 的方式处理，但是应该用不上了

## Questions

[ ] chunks 的基本概念
[ ] webpack 从入口文件开始，是默认能够理解所有的模块方式吗？如果不是默认的解析方式是什么？怎么配置以那种方式解析模块来构建 depency graph
[ ] 理解不同的 js 模块方式，es6，amd, commonjs 等 (https://v4.webpack.js.org/concepts/modules/)
[ ] webpack 与 nodejs 的关系？nodejs 是怎么处理 webpack 的？webpack 可以访问 nodejs 的什么参数？
[ ] webpack 是怎么寻找 config 文件的，是从当前目录开始一直往上找吗（类似 node_modules）？那么谁可以算作 root，怎么指定 root
[ ] 微前端或者多个 package 的结构是否可以分别指定对应的 webpack 设置？可以 extends common 的内容吗

## Further reading

[ ] 深入了解 webpack 的资源（来自官网）

- [ ] [Manually Bundling an Application](https://www.youtube.com/watch?v=UNMkLHzofQI&ab_channel=WebTechTalks)
- [ ] [Live Coding a Simple Module Bundler](https://www.youtube.com/watch?v=Gc9-7PBqOC8&ab_channel=YouGottaLoveFrontend)
- [ ] [Detailed Explanation of a Simple Module Bundler](https://github.com/ronami/minipack)
