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

## Core concepts in depth

1. Entry point
   - 三种定义方式
     - 单一入口文件
       ```js
       module.exports = {
         entry: './path/to/file.js',
       };
       // 是下面方式的简写
       module.exports = {
         entry: {
           main: './path/to/file.js', // main是webpack默认的entry文件的名称，输出也是以main作为名称
         },
       };
       ```
     - 数组。注意虽然是多入口，但是输出文件依然是**一个**
       ```js
       module.exports = {
         entry: ['./path/to/file1.js', './path/to/file2.js'],
       };
       ```
       Q: 这种方式的意义是什么？文档中提到的`there is not much flexibility in extending or scaling your configuration with this syntax`是指什么问题？
       可以参见这部分关于 scalable webpack config 的说明
       ```
       "Scalable webpack configurations" are ones that can be reused and combined with other partial configurations. This is a popular technique used to separate concerns by environment, build target, and runtime. They are then merged using specialized tools like webpack-merge.
       ```
       Q: 既然使用了数组，那么顺序应该是重要的？
       Q: 应该适用于多页面中使用了同一个第三方库的情况，这样就不用重复引用了同样的代码了。而是先提取出公用的代码提前引入？
     - 对象。注意是多入口文件，而是是多出口文件
       注意适合于 multi-page application，每个入口一个对应的 bundle，每个 bundle 都有自己的 dependency graph
       ```js
       module.exports = {
         entry: {
           main: './path/to/main.js',
           vendor: './path/to/vendor.js',
         },
         output: {
           // [name]是上面entry对象key的名字
           // [contenthash]是bundle对应的hash值，每个单个的输出都有自己的hash值。可以通过hash来帮助浏览器决定哪些文件需要重新fetch，换句话说就是利于缓存。
           filename: '[name].[contenthash].bundle.js',
         },
       };
       ```
       Q: 每个 key 对应的值也可以是数组
       注意 plugin 也可以创建 entry point，当是这种情况的时候，可以指定 entry 的值为空对象
       Best practice: Only one entry point for each HTML document
   - 其他
     - [ ] [optimization.splitChunks](https://webpack.js.org/configuration/optimization/#optimizationsplitchunks) 来处理 vendors 的 js
     - [ ] 根据文档，我认为其中一个可以优化的方向是 splitChunks，因为 apex 是多页面应用，client-dashboard, opportunities 都有对应的 bundle 文件，如果我们可以提取公共的代码，比如`apex/library`和`oui`的代码，那么可以更好的对这些部分进行缓存，尽快的完成渲染。注意量化结果
2. Output
   - 主要定义方式
     - 只有唯一输出文件，指定文件位置和名字即可
       ```js
       module.exports = {
         output: {
           filename: 'bundle.js',
         },
       };
       ```
     - 多入口对应多输出，需要指定唯一的名字针对每个 chunk 输出
       ```js
       module.exports = {
         output: {
           filename: '[name].js', // name是entry对应的key的值，比如main，vendor
           path: __dirname + '/dist', // __dirname应该是nodejs的参数，指当前的文件所在的目录
         },
       };
       ```
       Q: 进一步熟悉\_\_dirname 和 nodejs 的 resolve 函数
       Q: 一些其他常用的 key，比如 publicPath
3. Loaders
   transformations that applied to the source code of a module. 在解析到 import 并且资源是其他类型时，将他们进行转换，变成 webpack 可以理解的 js module。在使用前需要下载对应的 loader 的 npm 包

   - 类型
     - preLoaders
     - loaders
     - postLoaders
       Q: 这几种是怎么区分的？怎么在 config.js 文件中定义？
   - 使用方式

     - configuration 文件，最普遍的方式
     - inline: 在每个 import 语句的时候指定，会 override，config.js 中定义的 loaders

       ```js
       // !是不同loader的分割符，如果prefix with !/!! 的话会disable默认的loader
       import Styles from 'style-loader!css-loader?modules!./styles.css';
       ```

     - cli: 使用 webpack 命令的时候指定

   - 特性

     - 可以 chain 多个 loader 处理同一类型的文件、资源，但是注意是逆序的处理，最后定义的 loader 会优先 apply 到资源上
     - loaders 可以是同步的也可以是异步的
       Q: 例子？什么情况下需要异步的 loader？怎么设置？如果多个 loader，那么之后的 loader 会等待异步的结果返回吗？
     - 如果不适用 loader 的 option 属性就表示使用该 loader 默认的 options
     - loader 运行在 nodejs 中，所以任何 nodejs 可以做的事情，loader 都可以

   - Resolve loaders
     loader 使用普通的[module resolution](https://webpack.js.org/concepts/module-resolution/)，通常是从 node_modules 里装载的
     Q: 关于如何[写一个 loader](https://webpack.js.org/contribute/writing-a-loader/)?
     Q: 进一步了解[module resolution](https://webpack.js.org/concepts/module-resolution/)

4. Plugins
   任何 loader 不能做的事情都可以交给 plugin 来处理

   - 原理
     每个 plugin 就是一个至少拥有 apply 方法的 js object，webpack compiler 在运行是会调用 plugin 的 apply 方法，此时可以使用所有 compilation 的 lifecycle

     ```js
     const pluginName = 'ConsoleLogOnBuildWebpackPlugin';

     class ConsoleLogOnBuildWebpackPlugin {
       apply(compiler) {
         compiler.hooks.run.tap(pluginName, (compilation) => {
           console.log('The webpack is starting');
         });
       }
     }
     module.exports = ConsoleLogOnBuildWebpackPlugin;
     ```

   - 使用: 两种方式

     - 在 config 文件中指定
       plugin 在使用时必须 new 一个新的实例

     ```js
     const HtmlWebpackPlugin = require('html-webpack-plugin');
     module.exports = {
       plugins: [new HtmlWebpackPlugin({ template: './src/index.html' })],
     };
     ```

     - 在文件中使用

     ```js
     const webpack = require('webpack'); //to access webpack runtime
     const configuration = require('./webpack.config.js');

     let compiler = webpack(configuration);

     new webpack.ProgressPlugin().apply(compiler);

     compiler.run(function (err, stats) {
       // ...
     });
     ```

5. Configuration: 更多的内容在[configuration](https://webpack.js.org/configuration/)

   - `module.exports`的值可以是对象，function，promise 或者数组
   - Multiple targets: 不同的打包方式, e.g. commonjs, amd
     config.js 文件可以输出一个数组，包括针对不同 target 的输出
     ```js
     module.exports = [
       {
         output: {
           filename: './dist-amd.js',
           libraryTarget: 'amd',
         },
         name: 'amd',
         entry: './app.js',
         mode: 'production',
       },
       {
         output: {
           filename: './dist-commonjs.js',
           libraryTarget: 'commonjs',
         },
         name: 'commonjs',
         entry: './app.js',
         mode: 'production',
       },
     ];
     ```
   - config 文件可以使用 ts, babel and jsx, coffeescript 来写。babel & jsx 的方式有点像 xml

6. Modules

   - webpack module 能够解析通过以下方式定义的 dependencies
     - es2015 `import`
     - commonjs `require`
     - amd `define` or `require`
     - `@import` in css/less/sass
     - url in stylesheet e.g. `url()` or `<img src="..."/>`
   - webpack 原生支持主要的 js & assets modules
     - ECMAScript modules
     - CommonJS modules
     - AMD modules
     - Assets
     - WebAssembly modules
   - resolution: webpack 如何找到对应的 dependency

     - Resolve rules: webpack 使用`enhanced-resolve`，支持三种 path 定义

       - absolute paths: 不需要额外的 resolution
       - relative paths: 根据 context path(import/require 所在文件的路径)，拼接成 absolute path
       - module paths: 会在 config 中的 `resolve.module` 属性中定义寻找对应的 module。module path 也可以是在 config 中使用`resolve.alias`定义好的 alias。同时`package.json`中的一些属性也会影响 resolution。module 是文件还是文件夹也有对应不同的处理策略

       Q: 什么是 enhanced-resolve?

     - Resolve loaders
       类似于上面 resolve files 的规则，但是还可以使用 config 中的`resolveLoader`来定义额外的规则

     - Caching: 每个文件访问都被 cache 了，watch 的时候只有修改的文件会重新 cache。

## Questions

[ ] chunks 的基本概念
[ ] webpack 从入口文件开始，是默认能够理解所有的模块方式吗？如果不是默认的解析方式是什么？怎么配置以那种方式解析模块来构建 depency graph
[ ] 理解不同的 js 模块方式，es6，amd, commonjs 等 (https://v4.webpack.js.org/concepts/modules/)
[ ] webpack 与 nodejs 的关系？nodejs 是怎么处理 webpack 的？webpack 可以访问 nodejs 的什么参数？
[ ] webpack 是怎么寻找 config 文件的，是从当前目录开始一直往上找吗（类似 node_modules）？那么谁可以算作 root，怎么指定 root
[ ] 微前端或者多个 package 的结构是否可以分别指定对应的 webpack 设置？可以 extends common 的内容吗
[ ] 如何查看 webpack 的 dependency graph, some resources as below

      - https://medium.com/@joeclever/three-simple-ways-to-inspect-a-webpack-bundle-7f6a8fe7195d
      - https://survivejs.com/webpack/optimizing/build-analysis/

[ ] compare with other tools, roman, rollup, gulp, grunt

## Further reading

[ ] 深入了解 webpack 的资源（来自官网）

- [ ] [Manually Bundling an Application](https://www.youtube.com/watch?v=UNMkLHzofQI&ab_channel=WebTechTalks)
- [ ] [Live Coding a Simple Module Bundler](https://www.youtube.com/watch?v=Gc9-7PBqOC8&ab_channel=YouGottaLoveFrontend)
- [ ] [Detailed Explanation of a Simple Module Bundler](https://github.com/ronami/minipack)

[ ] [Webpack merge](https://github.com/survivejs/webpack-merge)
[ ] [Webpack free book](https://survivejs.com/webpack/foreword/)
