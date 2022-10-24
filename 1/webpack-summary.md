## 对于webpack的理解

1. Webpack是一个模块打包工具，可以使用它管理项目中的模块依赖，并编译输出模块所需的静态文件。

2. 它可以很好地管理、打包开发中所用到的HTML,CSS,JavaScript和静态文件（图片，字体）等，让开发更高效。

3. 对于不同类型的依赖，Webpack有对应的模块加载器，而且会分析模块间的依赖关系，最后合并生成优化的静态资源。

## webpack的基本功能

1. 代码转换：TypeScript 编译成 JavaScript、SCSS 编译成 CSS 等等

2. 文件优化：压缩 JavaScript、CSS、HTML 代码，压缩合并图片等

3. 代码分割：提取多个页面的公共代码、提取首屏不需要执行部分的代码让其异步加载

4. 模块合并：在采用模块化的项目有很多模块和文件，需要构建功能把模块分类合并成一个文件

5. 自动刷新：监听本地源代码的变化，自动构建，刷新浏览器

6. 代码校验：在代码被提交到仓库前需要检测代码是否符合规范，以及单元测试是否通过

7. 自动发布：更新完代码后，自动构建出线上发布代码并传输给发布系统。

## webpack的构建过程

Webpack 的运行流程是一个串行的过程，从启动到结束会依次执行以下流程：

- `初始化参数`：从配置文件读取与合并参数，得出最终的参数
- `开始编译`：用上一步得到的参数初始化 Compiler 对象，加载所有配置的插件，执行对象的 run 方法开始执行编译
- `确定入口`：根据配置中的 entry 找出所有的入口文件
- `编译模块`：从入口文件出发，调用所有配置的 Loader 对模块进行翻译，再找出该模块依赖的模块，再递归本步骤直到所有入口依赖的文件都经过了本步骤的处理
- `完成模块编译`：在经过第4步使用 Loader 翻译完所有模块后，得到了每个模块被翻译后的最终内容以及它们之间的依赖关系
- `输出资源`：根据入口和模块之间的依赖关系，组装成一个个包含多个模块的 Chunk，再把每个 Chunk 转换成一个单独的文件加入到输出列表，这步是可以修改输出内容的最后机会
- `输出完成`：在确定好输出内容后，根据配置确定输出的路径和文件名，把文件内容写入到文件系统

在以上过程中，`Webpack` 会在特定的时间点广播出特定的事件，插件在监听到感兴趣的事件后会执行特定的逻辑，并且插件可以调用 Webpack 提供的 API 改变 Webpack 的运行结果。

简单说

- 初始化：启动构建，读取与合并配置参数，加载 Plugin，实例化 Compiler
- 编译：从 Entry 出发，针对每个 Module 串行调用对应的 Loader 去翻译文件的内容，再找到该 Module 依赖的 Module，递归地进行编译处理
- 输出：将编译后的 Module 组合成 Chunk，将 Chunk 转换成文件，输出到文件系统中

## 常见的loader

js：

1. ts-loader: 将 TypeScript 转换成 JavaScript
2. `babel-loader`：把 ES6 转换成 ES5
3. `awesome-typescript-loader`：将 TypeScript 转换成 JavaScript，性能优于 ts-loader

css：

1. css-loader：加载 CSS，支持模块化、压缩、文件导入等特性
2. style-loader：把 CSS 代码注入到 JavaScript 中，通过 DOM 操作去加载 CSS
3. less-loader、scss-loader：将less、scss代码转换成CSS
4. `postcss-loader`：扩展 CSS 语法，使用下一代 CSS，可以配合 autoprefixer 插件自动补齐 CSS3 前

其他：

1. file-loader：把文件输出到一个文件夹中，在代码中通过相对 URL 去引用输出的文件 (处理图片和字体)
2. url-loader：与 file-loader 类似，区别是用户可以设置一个阈值，大于阈值会交给 file-loader 处理，小于阈值时返回文件 base64 形式编码 (处理图片和字体)
3. json-loader: 加载 JSON 文件（默认包含）
4. eslint-loader：通过 ESLint 检查 JavaScript 代码

## 常见的plugin

1. html-webpack-plugin：根据模板页面生成打包的 html 页面
2. DefinePlugin：创建全局变量
3. mini-css-extract-plugin: 分离样式文件，CSS 提取为独立文件，支持按需加载
4. clean-webpack-plugin: 目录清理
5. copy-webpack-plugin: 拷贝文件
6. webpack-bundle-analyzer: 可视化 Webpack 输出文件的体积 (业务组件、依赖第三方模块)

7. FriendlyErrorsWebpackPlugin：控制台显示信息优化

8. `terser-webpack-plugin`: 支持压缩 ES6 (Webpack4)

## loader和plugin的区别

`Loader` 本质就是一个函数，在该函数中对接收到的内容进行转换，返回转换后的结果。 因为 Webpack 只认识 JavaScript，所以 Loader 就成了翻译官，对其他类型的资源进行转译的预处理工作。

`Plugin` 就是插件，基于事件流框架 `Tapable`，插件可以扩展 Webpack 的功能，在 Webpack 运行的生命周期中会广播出许多事件，Plugin 可以监听这些事件，在合适的时机通过 Webpack 提供的 API 改变输出结果。

`Loader` 在 module.rules 中配置，作为模块的解析规则，类型为数组。每一项都是一个 Object，内部包含了 test(类型文件)、loader、options (参数)等属性。

`Plugin` 在 plugins 中单独配置，类型为数组，每一项是一个 Plugin 的实例，参数都通过构造函数传入。

## webpack提高效率的插件

`webpack-dashboard`：可以更友好的展示相关打包信息。

`webpack-merge`：提取公共配置，减少重复配置代码

`speed-measure-webpack-plugin`：简称 SMP，分析出 Webpack 打包过程中 Loader 和 Plugin 的耗时，有助于找到构建过程中的性能瓶颈。

`size-plugin`：监控资源体积变化，尽早发现问题

`HotModuleReplacementPlugin`：模块热替换

## source-map如何使用

https://juejin.cn/post/6844904201311485966

`source map` 是将编译、打包、压缩后的代码映射回源代码的过程。打包压缩后的代码不具备良好的可读性，想要调试源码就需要 soucre map。map文件只要不打开开发者工具，浏览器是不会加载的。

cheap-module-source-map, 生成的 Source Map 文件中没有列信息，但会包含 Loader 生成的 Source Map

线上环境一般有三种处理方案：

- `hidden-source-map`：会生成map文件，但浏览器不会加载source-map。可以将map文件与错误上报工具结合使用。借助第三方错误监控平台 Sentry 使用
- `nosources-source-map`：只会显示具体行数以及查看源代码的错误栈。安全性比 sourcemap 高

模式中有三类关键词:

- inline、hidden、eval：这几个模式是互斥的，描述的是Source Map的引入方式

  inline：Source Map内容通过base64放在js文件中引入。

  hidden：代码中没有sourceMappingURL，浏览器不自动引入Source Map。

  eval：生成代码和Source Map内容混淆在一起，通过eval输出

- nosources：使用这个关键字的Source Map不包含sourcesContent，调试时只能看到文件信息和行信息，无法看到源码。

- cheap[module]

  cheap：不包含列信息，并且源码是进过loader处理过的

  cheap-module：不包含列信息，源码是开发时的代码

## webpack热更新原理

刷新一般分为两种：

- 一种是页面刷新，不保留页面状态，就是简单粗暴，直接`window.location.reload()`。
- 另一种是基于`WDS (Webpack-dev-server)`的模块热替换，只需要局部刷新页面上发生变化的模块，同时可以保留当前的页面状态，比如复选框的选中状态、输入框的输入等。

https://juejin.cn/post/6844904008432222215

`HMR`全称 `Hot Module Replacement`，指在应用程序运行过程中，替换、添加、删除模块，而无需重新刷新整个应用。例如，我们在应用运行过程中修改了某个模块，通过自动刷新会导致整个应用的整体刷新，那页面中的状态信息都会丢失。如果使用的是 `HMR`，就可以实现只将修改的模块实时替换至应用中，不必完全刷新整个应用。

步骤：

1. `webpack-dev-server`生成`compiler`实例，用来编译、输出`bundle.js`，注入了`websocket`的相关逻辑，使浏览器运行时能够与本地建立socket连接；使用`express`框架启动本地`server`，让浏览器可以请求本地的静态资源；启动`websocket`服务

2. 依赖`webpack-dev-middleware`，监听`webpack`编译完成，通过`websocket`给浏览器发布更新通知，带上构建时的 hash，让浏览器与上一次资源进行对比

   > `webpack-dev-server`只负责启动服务和前置准备工作。webpack-dev-middleware`负责所有文件相关的操作，主要是本地文件的编译和输出以及监听

3. 浏览器检查更新，利用`HotModuleReplacementPlugin`，配置了热更新以后，模块会增加一个`hot`属性，调用`module.hot.check`检查更新。浏览器向本地`webpack-dev-server`发起 `Ajax` 请求来获取更改内容(文件列表、hash)，借助获取信息继续向 WDS 发起 `jsonp` 请求获取该chunk的增量更新。

4. `HotModulePlugin`决定后续更新，通过`hotupdate`找到旧模块，将新的模块添加到`moduls`中，通过`webpack_require`执行相关模块的代码

## babel原理













