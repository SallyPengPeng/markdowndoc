Webpack是一种前端资源构建工具，一个静态模块打包器(module bundler)

在Webpack看来，前端的所有资源文件(js/json/css/img/less/……)都会作为模块处理，它将依据模块的依赖关系进行静态分析，打包生成对应的静态资源(bundle)

![webpack原理](https://images-sally.oss-cn-beijing.aliyuncs.com/images/2022-01-05-webpack%E5%8E%9F%E7%90%86-f147eb15c39e594b5634c4e4a9ff911d-f59.png)

## 核心概念

### entry

入口Entry只是Webpack以那个文件为入口开始打包，分析构建内部依赖图

默认值是 `./src/index.js`

- entry接受三种类型值

```css
1.单入口写法 String 例：
    entry: './app/to/my/entry/file.js'
等同于：
    entry：{ main:"'./app/to/my/entry/file.js'"}
2.单入口数组写法 Array例： 在想要多个依赖文件一起注入，并且将它们的依赖导向(graph)到一个“chunk”时，传入数组的方式就很有用。也就是说合并了多个文件的依赖模块。
    entry：['./app/entry1', './app/entry2']
等同于：
		entry：{ main: ['./app/entry-b1', './app/entry-b2']}
3.对象语法Object例，多入口：
    entry：{ a: './app/entry-a', b: ['./app/entry-b1', './app/entry-b2']}
```

### output

输出Output指示Webpack打包后的资源bundles输出到那里去，以及如何命名

主要输出文件的默认值是 `./dist/main.js`，其他生成文件默认放置在 `./dist` 文件夹中

### loader

webpack 只能理解 JavaScript 和 JSON 文件，这是 webpack 开箱可用的自带能力。**loader** 让 webpack 能够去处理其他类型的文件，并将它们转换为有效模块，以供应用程序使用，以及被添加到依赖图中。loader是一个函数，接受源文件作为参数，返回处理的结果。

#### babel-loader

转换ES6 ES7等JS新特性语法

```shell
# 必须引入部分，其他按照要求引入
npm i babel-loader @babel/core @babel/preset-env -D
```

官方推荐在项目根目录下新建`.babelrc`配置文件解决`babel-loader`的依赖

```json
// plugin 用来支持某个功能，presets 是多个 plugin 的集合
{	
  // preset用到的babel也需要install
  "presets": [
    "@babel/preset-env", // js文件
    "@babel/preset-react", // react文件
  ],
  "plugins": [
    "@babel/proposal-class-properties",
  ]
}
```

#### 处理css、less、scss

在js中引入这些css文件，配置loader后，执行`npm run dev`时会自动将css文件在html文件(服务器根目录下)中添加`style`标签，将样式引入

```shell
# css-loader解析css文件，style-loader将样式通过style标签插入到head中
npm i css-loader style-loader -D
# 处理less
npm i less less-loader -D
# 处理scss
npm i node-sass sass-loader -D
```

#### 处理html文件的图片

```shell
npm i html-loader -D
```

#### 处理图片和字体

```shell
npm i file-loader url-loader -D
```

#### loader使用

```javascript
module.exports = {
  module: {
    rules: [
       // js文件的loader，可以将高级es6的语法转换为es的语法，适配低版本浏览器   
      {
        test: /\.js$/,
        use: 'babel-loader',
        // 排除成员
        exclude: /node_modules/,
      },
      
      // 解析.css文件的loader
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader'], // webpack底层调用这些包的顺序是从右到左
      },
      // 解析.less文件的loader
      {
        test: /\.less$/,
        use: ['style-loader', 'css-loader', 'less-loader'],
      },
      // 解析.scss 和 .sass 文件的loader
      {
        test: /\.s(a|c)ss$/,
        use: ['style-loader', 'css-loader', 'sass-loader'], 
      },
      
      // 处理html文件的img图片(负责引入img，从而能被url-loader处理)
      {
        test: /\.less$/,
        use: ['style-loader', 'css-loader', 'less-loader'],
      },
      // 解析图片文件的loader
      {
        test: /\.(jpg|jpeg|png|bmp|gif)$/,
        use: {
          loader: 'url-loader',
          options: {
            // limit标识如果图片大于5kb，就以路径形式展示，小于的话就用base64格式展示
            // 优点：减少请求数量，减轻服务器压力
            // 缺点：图片体积会更大
            limit: 5 * 1024,
           	name: 'img/[name][hash:8].[ext]'
          }
        },
      },
    ],
  },
}
```

### plugins

loader 用于转换某些类型的模块，而插件则可以用于执行范围更广的任务。包括：打包优化，资源管理，注入环境变量。

#### 自动清理构建目录产物

`clean-webpack-plugin`

#### BannerPlugin

webpack内置插件，只要安装了`webpack`就可以用

#### 插件使用

```javascript
const webpack = require('webpack');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const CleanWebpackPlugin = require('clean-webpack-plugin');
const CopyWebpackPlugin = require('copy-webpack-plugin');

plugins: [
    new HtmlWebpackPlugin({
      filename: 'index.html',
      template: './src/index.html'
    }),
    // 删除打包之前目录下的打包文件
    new CleanWebpackPlugin(),
    // 把单个文件或整个文件夹放到打包目录
    new CopyWebpackPlugin([
      {
        from: path.join(__dirname, 'assets'),
        to: 'assets'
      },
    ]),
  ],
```

### mode

通过选择 development, production 或 none 之中的一个，来设置 mode 参数，你可以启用 webpack 内置在相应环境下的优化。其默认值为 production。

模式(Mode)指示 webpack 使用相应模式的配置。

| 选项        | 描述                                                         | 特点                        |
| ----------- | ------------------------------------------------------------ | --------------------------- |
| development | 会将 DefinePlugin 中 process.env.NODE_ENV 的值设置 为 development。 启用 NamedChunksPlugin 和 NamedModulesPlugin。 | 能让代码本地调试 运行的环境 |
| production  | 会将 DefinePlugin 中 process.env.NODE_ENV 的值设置 为 production。 启用 FlagDependencyUsagePlugin, FlagIncludedChunksPlugin, ModuleConcatenationPlugin, NoEmitOnErrorsPlugin, OccurrenceOrderPlugin, SideEffectsFlagPlugin 和 TerserPlugin。 | 能让代码优化上线 运行的环境 |

## 热更新

webpack 中有几个不同的选项，可以帮助你在代码发生变化后自动编译代码：

webpack's Watch Mode
webpack-dev-server
webpack-dev-middleware

多数场景中，你可能需要使用 webpack-dev-server

只有在开发时才需要使用自动编译工具（只是为了提高开发体验），项目上线时都会直接使用webpack进行打包构建

### watch模式

每次自动构建完后，在浏览器需要手动刷新

有2种方法实现：

(1)在`webpack`指令后面加上`--watch`参数即可。就可以监视本地项目文件的变化，如果其中一个文件被更新，代码将被重新编译，所以你不必手动运行整个构建

```json
"scripts": {
    "watch": "webpack --watch"
  },
```

(2)在webpack的配置文件中，加上`watch: true`

```javascript
module.exports = {
  watch: true, // 默认false
  // 以下是watch可选配置
  watchOptions: {
    // 不监听的文件或者文件夹，支持正则匹配，默认为空
    ignored: /node_modules/,
    // 监听到变化发生后会等300ms再去执行，默认300ms
    aggregateTimeout: 300,
    // 判断文件是否发生变化时通过不停询问系统指定文件有没有变化实现的，默认每秒问1000次
    poll: 1000,
  }
}
```

原理：轮询判断文件的最后编辑时间是否变化。某个文件发生了变化，并不会立刻告诉执行者，而是先缓存起来，等aggregateTimeout。

### webpack-dev-server

devServer会在内存中生成一个打包好的bundle.js，专供开发时使用，打包效率高，修改代码后会自动重新打包以及刷新浏览器。

1. 需要安装devServer    `npm i webpack-dev-server -D`

2. index.html中需要修改为 `<script src="/bundle.js"></script>`

3. 运行

   1. 使用npx运行

   ```shell
   npx webpack-dev-server
   # 可以命令中加上配置 --open自动打开浏览器
   npx webpack-dev-server --hot --open --port 8090
   ```
   2. 使用修改配置package.json运行，执行`npm run dev`

   ```json
   "scripts": {
       "dev": "webpack-dev-server --hot --open --port 8090"
     },
   ```

   devServer的参数也可以在`webpack.config.js`中配置

   ```javascript
   module.exports = {
     devServer: {
       port: 8090, // 修改devServer启动的服务器端口，默认是8080
       open: true, // When open is enabled, the dev server will open the browser
       hot: true, // 以补丁的方式只更新修改的文件
     }
   };
   ```

### webpack-dev-middleware

WDM将webpack输出的文件传输给服务器，适用于灵活的定制场景。

1. 安装 `npm i express webpack-dev-middleware -D`

2. 新建`server.js`

   ```javascript
   const express = require('express');
   const webpack = require('webpack');
   const webpackDevMiddleware = require('webpack-dev-middleware');
   const config = require('./webpack.config.js');
   
   const app = express();
   const compiler = webpack(config);
   
   app.use(webpackDevMiddleware(compiler, {
     publicPath: '/'
   }));
   
   app.listen(3000, function() {
     console.log('http://localhost:3000');
   });
   ```

3. 配置`package.json`中的scripts `"server": "node server.js"`
4. 运行：`npm run server`

注意：如果要使用middleware，必须使用`html-webpack-plugin`插件，否则html文件无法正确的输出到express服务器的根目录

### 热更新原理分析

![热更新原理分析](https://images-sally.oss-cn-beijing.aliyuncs.com/images/2022-01-05-%E7%83%AD%E6%9B%B4%E6%96%B0%E5%8E%9F%E7%90%86%E5%88%86%E6%9E%90-19e6e9c7ed9423109c0d001c2edee623-a80.png)

Webpack Compile：将JS编译成Bundle

HMR Server：将热更新的文件输出给HMR Runtime

Bundle server: 提供文件在浏览器的访问

HMR Runtime: 会被注入到浏览器，更新文件的变化

Bundle.js：构建输出的文件

## 文件指纹

无法和热更新同时使用

打包后输出的文件名的后缀，用于文件版本的管理，对于没有修改的文件，文件指纹不会变化，可以继续使用浏览器的缓存

hash：和整个项目的构建相关，只要项目文件有修改，整个项目构建的hash值都会更改。

chunkhash：和webpack打包chunk有关，不同的entry会生成不同的chunkhash值

contenthash：根据文件内容来定义hash，文件内容不变，则contenthash不变。

| 文件类型                                                  | 示例                          |
| --------------------------------------------------------- | ----------------------------- |
| Js，设置output的filename，使用[chunkhash]                 | '\[name]_[chunkhash:8].js'    |
| css，设置MiniCssExtractPlugin的filename,使用[contenthash] | '\[name]_[contenthash:8].css' |
| 图片，设置file-loader的name,使用[hash]                    | 'img/\[name]_\[hash:8].[ext]' |

注：图片指纹占位符介绍

| 占位符介绍    | 含义                          |
| ------------- | ----------------------------- |
| [ext]         | 资源后缀名                    |
| [name]        | 文件名称                      |
| [path]        | 文件的相对路径                |
| [folder]      | 文件所在的文件夹              |
| [contenthash] | 文件的内容hash，默认是md5生成 |
| [hash]        | 文件内容的hash，默认是md5生成 |
| [emoji]       | 一个随机的纸袋文件内容的emoji |

## 代码压缩

### js压缩

内置了uglifyjs-webpack-plugin

### css压缩

需要使用`optimize-css-assets-webpack-plugin`插件来完成css压缩

但是由于配置css压缩包会覆盖掉js默认的优化配置，导致js代码无法压缩，所以还需要手动把js代码压缩插件导入进来：`terser-webpack-plugin`

1. 安装： `npm i optimize-css-assets-webpack-plugin -D  `

2. 在webpack配置文件中添加配置

   ```javascript
   const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin');
   const cssnano = require('cssnano');
   module.exports = {
     plugins: [new OptimizeCSSAssetsPlugin({
       assetNameRegExp: /\.css$/g,
       cssProcessor: cssnano,
     })]
   
   ```


### html压缩

devServer根据模板在项目根目录下下生成html文件(类似于devServer生成内存中的bundle.js)。

作用：

1. devServer时自动在内存中生成的html文件中引入bundle.js
2. 打包时自动生成index.html
3. 还可以设置压缩参数

步骤：

1. 使用`html-webpack-plugin`插件，需要安装`npm i html-webpack-plugin -D`

2. 在`webpack.config.js`中的`plugins`节点下配置

   ```javascript
   const HtmlWebpackPlugin = require('html-webpack-plugin');
   module.exports = {
     plugins: [
       new HtmlWebpackPlugin({
         filename: 'index.html', // 输出的html文件名，默认index.html
         template: './src/index.html', // 导出文件模板
         chunks: ['vendors'],
         // 压缩参数
         minify: {
           html5: true,
           collapseWhitespace: true,
           preserveLineBreaks: false,
           minifyCSS: true,
           minifyJS:true,
           removeComments: false,
         }
       })
     ],
   }
   ```

## 资源内联

将css或js代码直接内联到html文件中

作用：

1. 代码层面：页面框架的初始化脚本；上报相关大点；css内联避免页面闪动。
2. 请求层面：减少HTTP网络请求数

### js和html内联

raw-loader

### css内联

1. 借助style-loader

   ```javascript
   {
     loader: 'style-loader',
     optios: {
       insertAt: 'toop', // 样式插入到<head>
       singleton: true, // 将所有的style标签合并成一个
     }  
   }
   ```

2. Html-inline-css-webpack-plugin

## 多页面应用

MPA：每次页面跳转时，后台服务器都会给返回一个新的html文档。

思路：动态获取entry和设置html-webpack-plugin数量

利用`glob.sync`(需要安装glob库)，

```javascript
// 动态获取所有的入口文件
entry: glob.sync(path.join(__dirname, './src/*/index.js')),
```

主要实现逻辑：

```javascript
const glob = require('glob');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const setMPA = () => {
  const entry = {};
  const htmlWebpackPlugins = [];
  const entryFiles = glob.sync(path.join(__dirname, './src/*/index.js'));
  entryFiles.forEach(entryFile => {
    const match = entryFile.match(/src\/(.*)\/index.js/);
    const pageName = match && match[1];
    entry[pageName] = entryFile;
    htmlWebpackPlugins.push({
      filename: `${pageName}.html`,
      template: path.join(__dirname, `src/${pageName}/index.html`),
      chunks: [pageName, 'vendors'], // html中引入公共资源文件chunk
      // 压缩参数
      minify: {
        html5: true,
        collapseWhitespace: true,
        preserveLineBreaks: false,
        minifyCSS: true,
        minifyJS:true,
        removeComments: false,
      }
    });
  });
  return { entry, htmlWebpackPlugins };
};
```

## css优化

### 自动添加css前缀

因为浏览器的内核不同，如果要兼容不同内核的浏览器，需要给CSS属性添加前缀。

autoprefixer根据[Can I use规则][https://caniuse.com/]，在网址能搜索到的属性，此插件就可以自动补齐

使用`postcss`，需要用到`postcss-loader`和`autoprefixer`插件

1. 安装：`npm i postcss-loader autoprefixer -D`

2. 修改webpack配置文件中的loader，将`postcss-loader`放置在`css-loader`的右边(调用链从右到左)

   ```javascript
   // webpack的配置文件遵循
   module.exports = {
     module: {
       rules: [
         // 解析.scss 和 .sass 文件的loader
         {
           test: /\.s(a|c)ss$/,
           use: ['style-loader', "css-loader", "postcss-loader", "sass-loader"],
         },
       ],
     },
   };
   ```

3. 项目根目录下添加`postcss`的配置文件`postcss.config.js`

   ```javascript
   module.exports = {
     plugins: [require('autoprefixer')({ browsers: ['last 2 version', '>1%', 'ios 7']})]
   }
   ```

### 将css提取到独立文件中

`mini-css-extract-plugin`是用于将css提取为独立的文件的插件，对每个包含css的js文件都会创建一个css文件，支持按需加载css和sourceMap

只能用在webpack4中，优势：异步加载、不重复编译，性能更好、更容易使用、只针对css

1. 安装：`npm i mini-css-extract-plugin`

2. 在webpack配置文件中引入插件

   ```javascript
   const MiniCssExtractPlugin = require("mini-css-extract-plugin");
   
   // webpack的配置文件遵循
   module.exports = {
     plugins: [
       // 将css提取到独立文件中
       new MiniCssExtractPlugin({
         filename: "[name].css",
       }),
     ],
     module: {
       rules: [
         // 解析.scss 和 .sass 文件的loader
         {
           test: /\.s(a|c)ss$/,
           // 不能使用style-loader，与style-loader功能相斥
           use: [MiniCssExtractPlugin.loader, "css-loader", "sass-loader"],
         },
       ],
     },
   };
   ```

## js优化

### 基础库分离

方法一：将react、react-dom基础包通过cdn引入，不打入 bundle中

使用`html-webpck-externals-plugin`，打包后资源文件会被放在html的body中，通过script标签将资源从给定的cdn地址引入

```javascript
const HtmlWebpackExternalsPlugin = require('html-webpck-externals-plugin');
plugind: [
  new HtmlWebpackExternalsPlugin({
    externals: [
      {
        module: 'react',
        entry: '//11.url/……',  //cdn地址
        global: 'React'
      },
      {
        module: 'react-dom',
        entry: '//11.url/……',  //cdn地址
        global: 'ReactDom'
      },
    ]
  }),
]
```

方法二：修改webpack配置文件，内部使用了`SplitChunksPlugin`插件

```javascript
optimization: {
 	splitChunks: {
    minSize: 0,
      cacahGroups: {
        commons: {
          name: 'commons',
          chunks: 'all',
          minChunks: 2,  
        }
      }
  } 
},
```

在打包后的文件夹，增加了一个`vendors~main~other.js`文件，把都用到的依赖打包到了一个单独的js中。

方法三：预编译资源模块

将 react、react-dom、redux、react-redux 基础包和业务基础包打包成一个文件。

涉及两个插件：

1. DllPlugin：使用一个单独webpack配置创建一个dll文件，并且它还创建一个manifest.json。DllReferencePlugin使用该json文件来做映射依赖性。（这个文件会告诉我们哪些文件已经提取打包好了）

2. DllReferencePlugin：这个插件用于主webpack配置，它引用的dll需要预先构建依赖关系


以vue项目为例

1. 准备一份将Vue打包成DLL的webpack配置文件

   在build目录下新建一个文件：webpack.vue.js

   配置入口：将多个要做成dll的库全放进来

   配置出口：一定要设置library属性，将打包好的结果暴露在全局

   配置plugin：设置打包后dll文件名和manifest文件所在地

   ```javascript
   const path = require("path");
   const webpack = require("webpack");
   
   module.exports = {
     mode: 'development',
     entry: {
       vue: ['vue/dist/vue.js', 'vue-router'],
     },
     output: {
       path: path.resolve(__dirname, '../dist'),
       filename: "[name]_dll.js",
       library: '[name]_dll', // 对外暴露一个全局变量
     },
     plugins: [
       new webpack.DllPlugin({
         name: '[name]_dll',
         // 生成清单文件
         path: path.resolve(__dirname, '../dist/manifest.json'),
       })
     ],
   };
   
   ```

2. 在webpack.base.js中进行插件的配置

   使用DllReferencePlugin指定manifest文件的位置即可

   ```javascript
   new webpack.DllReferencePlugin({
         manifest: path.resolve(__dirname, '../dist/manifest.json')
   }),
   ```

3. 配置插件自动添加script标签到html中

   安装：`npm i add-asset-html-webpack -D`

```javascript
new AddAssetHtmlWebpackPlugin({
      filepath: path.resolve(__dirname, '../dist/vue_dll.js')
    }),
```

### 动态导入

webpack4默认是允许import语法动态导入的，但是需要babel插件支持。

动态导入最大的好处是实现了懒加载，用到哪个模块才会加载哪个模块，可以调高SPA应用程序的首屏加载速度，Vue React Angular框架的路由懒加载原理是一样的。

1. 安装babel插件：`npm i @babel/plugin-syntax-dynamic-import -D`

2. 修改`.babelrc`配置文件，添加`@babel/plugin-syntax-dynamic-import`

   ```javascript
   {
     "plugins": [
       // js优化：动态导入
       "@babel/plugin-syntax-dynamic-import"
     ]
   }
   ```

3. 动态导入

   ```javascript
   function getComponent() {
     return import('jquery').then(({ default: $}) => {
       return $('<div></div>').html('我是main');
     })
   }
   
   getComponent().then(item => {
     item.appendTo('body');
   })
   ```

## source map

当 webpack 打包源代码时，可能会很难追踪到错误和警告在源代码中的原始位置。例如，如果将三个源文件（`a.js`, `b.js` 和 `c.js`）打包到一个 bundle（`bundle.js`）中，而其中一个源文件包含一个错误，那么堆栈跟踪就会简单地指向到 `bundle.js`。这并通常没有太多帮助，因为你可能需要准确地知道错误来自于哪个源文件。

```javascript
module.exports = {
  mode: 'development',
  // 启用source-map功能，使调试信息能够更方便定位到源码目录
  devtool: 'cheap-module-eval-source-map',
}
```

## webpack打包组件和基础库

1. webpack.config.js添加配置，支持压缩版和非压缩资源

```javascript
const TerserPlugin = require('terser-webpack-plugin');
module.exports = {
  mode: 'none', // 避免代码默认被压缩
  entry: {
    'large-number': './src/index.js',
    'large-number.min': './src/index.js',
  },
  output: {
    filename: '[name].js',
    library: 'largeNumber', // 库的名字
    libraryTarget: 'umd', // 设置可以引入的方式 amd cjm esm
    libraryExport: 'default', // 设置调用
  },
  optimization: {
    minimize: true,
    // 设置仅 min.js 压缩代码
    minimizer: [new TerserPlugin({ include: /\.min\.js$/})]
  },
}
```

2. package.json中配置main属性，设置入口文件
3. 编写入口文件

```javascript
if (process.env.NODE_ENV === 'production') {
  module.exports = require('./dist/large-number.js');
} else {
  module.exports = require('./dist/large-number.min.js');
}
```

4. 发布到npm  `npm publish`

## webpack高级配置

### 第三方库引入

可以通过`expose-loader`进行全局变量的注入，同时也可以使用内置插件`webpack.ProvidePlugin`对每个模块的闭包空间，注入一个变量，自动加载模块，而不必导出`import`和`require`

1、expose-loader 将库引入到全局作用域

安装：`npm i expose-loader -D`

配置`loader`

```javascript
// 以jquery为例，假设需要将jQuery引入到所有模块中使用
modele: {
  rules: [{
    test: require.resolve('jquery'),
    use: {
      loader: 'expose-loader',
      options: '$'
    },
  }]
}
```

注：`require.resolve`用来获取模块的绝对路径，所以这里的loader只会作用于jquery模块，并且只在bundle中使用到它时，才进行处理

2、`wabpack.ProvidePlugin`将库自动加载到每个模块

```javascript
const webpack = require('webpack');
```

```javascript
// plugins里面添加配置
plugins: [
    new webpack.ProvidePlugin({
      $: 'jquery',
      jQuery: 'jquery',
    })]),
  ],
```

### 定义环境变量

除了区分不同的配置文件进行打包，还需要在开发时知道当时的环境是开发阶段还是上线阶段，所以可以借助内置插件`DefinePlugin`定义环境变量，最终可以实现开发阶段与上线阶段的api地址自动切换。

```javascript
const webpack = require('webpack');
```

```javascript
// plugins里面添加配置，可以在src打包的代码环境下任意位置直接使用环境变量IS_DEV
plugins: [
    new webpack.DefinePlugin({
       // 必须写成'true', 解析环境变量会将''里面的内容当做js解析
      IS_DEV: 'true',
    })
  ]
```

### HMR的使用

需要对某个模块进行热更新时，可以通过`module.hot.accept`方法进行文件监视
只要模块内容发生变化，就会触发回调函数，从而可以重新读取模块内容，做对应的操作。

```javascript
if (module.hot) {
  module.hot.accept('./hotmodule.js', function() {
    console.log('hotmodule.js更新了');
    let str = require('./hotmodule.js');
    console.log(str);
  })
}
```

## webpack优化

### production模式打包自带优化

- tree shaking

  是一个术语，通常用于打包时移除JavaScript中的未引用的代码(dead-code)，它依赖于ES6模块系统中import和export的静态结构特性（import是静态必须放在顶部导入，require是动态导入，可以在不同条件下引入不同的模块）

  开发时引入一个模块后，如果只使用其中一个功能，上线打包时只会把用到的功能打包进bundle，其他没用到的功能都不会打包进来，可以实现最基础的优化。

  Production模式tree shaking会自动打开，会把没有用到的代码增加一些注释，在uglify阶段删除。

  会被tree shaking的代码：代码不会被执行，不可到达；代码执行的结果不会被用到；代码只会影响死变量（只写不读）

- scope hoisting

  作用是将模块之间的关系进行结果推测，可以让Webpack打包出来的代码文件更小、运行的更快

  scope hoisting的实现原理：分析出模块之间的依赖关系，尽可能的把打散的模块合并到一个函数中去，但前提是不能造成代码冗余。（使用的是ModuleConcatenationPlugin）

  因此只有那些被引用了一次的代码才能被合并。

  由于scope hoisting需要分析出模块之间的依赖关系，因为源码必须采用ES6模块化语句，不然它将无法生效

- 代码压缩

  所有代码使用UglifyJsPlugin插件进行压缩、混淆。

### 优化命令行构建日志

统计信息stats

| Preset       | Description                    |
| ------------ | ------------------------------ |
| "error-only" | 只在发生错误时输出             |
| "minimal"    | 只在发生错误或有新的编译时输出 |
| "none"       | 没有输出                       |
| "normal"     | 标准输出                       |
| "verbose"    | 全部输出                       |

使用`friendly-errors-webpack-plugin`优化控制台信息展示，不同程度会有不同颜色。

success  warning  error，stats设置成errors-only

```javascript
const FriendlyErrorsWebpackPlugin = require('friendly-errors-webpack-plugin';)
plugins: [new FriendlyErrorsWebpackPlugin()],
stats: 'errors-only',
```

### 构建包

![npm构建包](https://images-sally.oss-cn-beijing.aliyuncs.com/images/2022-01-05-npm%E6%9E%84%E5%BB%BA%E5%8C%85-7122991f97d42b79c6b283256eddf8c0-b54.png)

### 构建速度和体积优化

#### 查看构建速度和体积优化

使用 speed-measure-webpack-plugin查看构建速度

```javascript
const SpeedMeasureWebpackPlugin = require('speed-measure-webpack-plugin');
const smp = new SpeedMeasureWebpackPlugin();
module.exports = smp.wrap({
});
```

webpack-bundle-analyzer 分析体积

```javascript
const { BundleAnalyzerPlugin } = require('webpack-bundle-analyzer');
module.exports = {
  plugins: [
    new BundleAnalyzerPlugin(),
  ]
}
```

#### 多进程多实例构建

方法一：使用 HappyPack 解析资源：每次 webapck 解析一个模块，HappyPack 会将它及它的依赖分配给 worker 线程中

```javascript
plugins = [
  new HappyPack({
    id: 'jsx',
    thread: 4,
    loaders: ['babel-loader'],
  }),
];
```

方法二：使用 thread-loader 解析资源：每次 webpack 解析一个模块，thread-loader 会将它及它的依赖分配给 worker 线程中

```javascript
module: {
  rules: [
    {
      test: /\.js$/,
      use: [
        {
          loader: 'thread-loader',
          options: { workers: 3 },
        },
        'babel-loader',
      ],
    },
  ],
},
```

#### 多进程多实例并行压缩

方法一：使用 parallel-uglify-plugin 插件

```javascript
const ParallelUglifyPlugin = require('webpack-parallel-uglify-plugin');
……
plugins: [
  new ParallelUglifyPlugin({
    uglifyJS: {
      output: {
        beauty: false,
        comments: false,
      },
      compress: {
        warnings: false,
        drop_console: true,
        collapse_vars: true,
        reduce_vars: true,
      },
    },
  }),
],
```

方法二：使用uglifyjs-webpack-plugin 开启 parallel 参数，不支持es6语法压缩

```javascript
const UglifyjsPlugin = require('uglifyjs-webpack-plugin');
……
plugins: [
  new UglifyjsPlugin({
    uglifyOptions: {
      warnings: false,
      parse: {},
      compress: {},
      mangle: true,
      output: null,
      toplevel: false,
      nameCache: null,
      ie8: false,
      keep_fnames: false,
    },
    parallel: true,
  }),
],
```

方法三：推荐使用，使用terser-webpack-plugin 开启 parallel 参数

```javascript
const TerserPlugin = require('erser-webpack-plugin');
……
optimization: {
  minizer: [
    new TerserPlugin({
      parallel: 4
    }),
  ],
},
```

#### 合理使用浏览器缓存

在做了众多代码分离的优化后，其目的是为了利用浏览器缓存，达到提高访问速度的效果，所以构建项目时做代码分隔是必须的，例如将固定的第三方模块抽离，下次修改了业务代码，重新发布上线不重启服务器，用户再次访问服务器就不需要再次加载第三方模块了。

但此时会遇到一个问题，如果再次打包上线不重启服务器，客户端会把以前的业务代码和第三方模块同时缓存，再次访问时依旧会访问缓存中的业务代码，所以会导致业务代码也无法更新

需要在output节点的filename中使用placeholder语法，根据代码内容生成文件名的hash。

```javascript
output: {
    // path.resolve解析当前相对路径的绝对路径
    path: path.resolve("./dist"),
    // 生成文件中添加8位的hash值，使更新业务代码时，避免使用缓存，导致页面内容没有更改
    filename: "[name].[contenthash:8].js",
  },
```

之后每次打包业务代码时，如果有改变，会生成新的hash作为文件名，浏览器就不会使用缓存了，而第三方模块不会重新打包生成新的名字，则会继续使用缓存。

开启缓存：会在node_modules下生成.cache目录文件

· babel-loader 开启缓存 

· terser-webpack-plugin 开启缓存 

· 使用 cache-loader 或者 hard-source-webpack-plugin

#### 缩小构建目标

减少文件搜索范围：优化resolve.modules配置(减少模块搜索层级)、优化resolve.mainFields配置、优化resolve.extensions配置、合理使用alias

```javascript
module.exports = {
  resolve: {
    alias: {
      react: path.resolve(__dirname, './node_modules/react/dist/react.min.js')
    },
    modules: [path.resolve(__dirname, 'node_modules')],
    extensions: ['.js'],
    mainFields: ['main'],
  }
}
```

### ts 文件解析

1、安装插件 typescript: ts编译器; ts-loader: ts加载器，用于在webpack中编译ts文件

```shell
npm i -D typescript ts-loader
```

2、`webpack.config.js`配置

```javascript
module.exports = {
  module: {
    rules: [
      {
        test: /\.ts$/,
        use: { loader: "ts-loader" },
        exclude: /node_modules/
      }]
    },
}
```

3、根目录创建`tsconfig.json`，根据需要配置

### 提高构建性能

#### noParse

 在引入一些第三方模块时，并且其内部没有依赖其他模块。如果此时webpack去解析他们的内部依赖关系，是非常浪费时间的，需要组织webpack浪费时间去解析这些明知道没有依赖的库。

可以在webpack配置文件的`module`节点下加上`noParse`，并配置正则来确定不需要解析依赖关系的模块。

```javascript
module: {
    noParse: /jquery|bootstrap/,
}
```

#### IgnorePlugin

在引入一些第三方模块时，例如moment，内干部会做18n国际化处理，所以会包含很多语言包，而语言包打包时会比较占用空间，如果项目只用到中文，或者少数语言，可以忽略掉所有的语言包，然后按需额引入语言包从而使得构建效率更高，打包生成的文件更小。

需要忽略第三方模块内部依赖的其他模块，只需要三步：

1. 首先要找到moment依赖的语言包是什么
2. 使用ignorePlugin插件忽略其依赖
3. 需要使用某些依赖时自动手动引入

使用moment举例

1. 通过查看moment的源码来分析：

   观察以下代码，同时查看moment目录下确实有locale目录，其中放着所有国家的语言包，可以分析得出：locale目录就是moment所依赖的语言包目录。

   ```javascript
   function loadLocale(name) {
     var oldLocale = null,
         aliasedRequire;
     // TODO: Find a better way to register and load all the locales in Node
     if (
       locales[name] === undefined &&
       typeof module !== 'undefined' &&
       module &&
       module.exports
     ) {
       try {
         oldLocale = globalLocale._abbr;
         aliasedRequire = require;
         aliasedRequire('./locale/' + name);
         getSetGlobalLocale(oldLocale);
       } catch (e) {
         // mark as not found to avoid repeating expensive file require call causing high CPU
         // when trying to find en-US, en_US, en-us for every format call
         locales[name] = null; // null means not found
       }
     }
     return locales[name];
   }
   ```

2. 使用ignorePlugin插件来忽略掉moment模块的locale目录，2个参数都是正则对象

   参数1：标识要忽略的资源路径   参数2：要忽略的资源上下文(所在哪个目录)，

   ```javascript
   new webpack.IgnorePlugin(/\.\/locale/, /moment/),
   ```

3. 使用moment时需要手动引入语言包，否则默认使用英文

   ```javascript
   import moment from 'moment';
   import 'moment/locale/zh-cn';
   moment.locale('zh-CN');
   ```

### 打包分析

bundle analysis 项目构建完成后，需要通过一些工具对打包后的bundle进行分析，通过分析才能总结出一些经验，官方推荐的分析方法有两步完成：

1. 使用`--profile --json`参数，以json格式来输出打包后的结果到某个指定文件中

   `webpack --profile --json > stats.json`

2. 将stats.json文件放入工具中进行分析

   官方工具：official.analyze.tool

   - [webpack-chart](https://alexkuz.github.io/webpack-chart/): Interactive pie chart for webpack stats.
   - [webpack-visualizer](https://chrisbateman.github.io/webpack-visualizer/): Visualize and analyze your bundles to see which modules are taking up space and which might be duplicates.
   - [webpack-bundle-analyzer](https://github.com/webpack-contrib/webpack-bundle-analyzer): A plugin and CLI utility that represents bundle content as a convenient interactive zoomable treemap.
   - [webpack bundle optimize helper](https://webpack.jakoblind.no/optimize): This tool will analyze your bundle and give you actionable suggestions on what to improve to reduce your bundle size.
   - [bundle-stats](https://github.com/bundle-stats/bundle-stats): Generate a bundle report(bundle size, assets, modules) and compare the results between different builds.

## loader-runner

定义：loader-runner 允许你在不安装 webpack 的情况下运行 loaders 

作用：

- 作为 webpack 的依赖，webpack 中使用它执行 loader 

- 进行 loader 的开发和调试

loader-runner 的使用 :

```javascript
import { runLoaders } from "loader-runner"; 
runLoaders({ 
  resource: “/abs/path/to/file.txt?query”, // String: 资源的绝对路径(可以增加查询字符串) 
  loaders: [“/abs/path/to/loader.js?query”], // String[]: loader 的绝对路径(可以增加查询字符串) 
  context: { minimize: true }, // 基础上下文之外的额外 loader 上下文 
  readResource: fs.readFile.bind(fs) // 读取资源的函数 
  }, function(err, result) { 
  // err: Error? 
  // result.result: Buffer | String 
})
```

## webpack原理

webpack执行过程：https://juejin.cn/post/6948950633814687758#heading-6

### Tapable和webpack的联系

1. Tapable有很多hooks，webpack的compile.js文件中已经定义了很多hook。
2. 自定义的插件需要写一个apply方法，其中绑定监听所需要的hook。
3. webpack编译时，会初始化`compile`的实例，然后遍历配置文件中定义的所有plugin，将compile对象传入，执行每个plugin的apply方法，从而将compile实例中的hook绑定了插件中的方法。
3. 执行compile.run，则执行了所有的hook，相当于将所有plugin需要的功能都执行一遍了

webpack源码

```javascript
if (Array.isArray(options)) {
  compiler = new MultiCompiler(options.map(options => webpack(options)));
} else if (typeof options === "object") {
  options = new WebpackOptionsDefaulter().process(options);	
  // compiler实例
  compiler = new Compiler(options.context);
  compiler.options = options;
  new NodeEnvironmentPlugin().apply(compiler);
  if (options.plugins && Array.isArray(options.plugins)) {
    for (const plugin of options.plugins) {
      if (typeof plugin === "function") {
        plugin.call(compiler, compiler);
      } else {
        // 执行每个plugin的apply方法
        plugin.apply(compiler);
      }
    }
  }
  compiler.hooks.environment.call();
  compiler.hooks.afterEnvironment.call();
  compiler.options = new WebpackOptionsApply().process(options, compiler);
}
```

模拟compiler.js

```javascript
const {
    SyncHook,
    AsyncSeriesHook
} = require('tapable');

module.exports = class Compiler {
    constructor() {
        this.hooks = {
            accelerate: new SyncHook(['newspeed']),
            break: new SyncHook(),
            calculateRoutes: new AsyncSeriesHook(["source", "target", "routesList"])
        }
    }
    run(){
        this.accelerate(10)
        this.break()
        this.calculateRoutes('Async', 'hook', 'demo')
    }
    accelerate(speed) {
        this.hooks.accelerate.call(speed);
    }
    break() {
        this.hooks.brake.call();
    }
    calculateRoutes() {
        this.hooks.calculateRoutes.promise(...arguments).then(() => {
        }, err => {
            console.error(err);
        });
    }
}
```

模拟插件

```javascript
class MyPlugin{
    apply(compiler){
        compiler.hooks.brake.tap("WarningLampPlugin", () => console.log('WarningLampPlugin'));
        compiler.hooks.accelerate.tap("LoggerPlugin", newSpeed => console.log(`Accelerating to ${newSpeed}`));
        compiler.hooks.calculateRoutes.tapPromise("calculateRoutes tapAsync", (source, target, routesList) => {
            return new Promise((resolve,reject)=>{
                setTimeout(()=>{
                    console.log(`tapPromise to ${source} ${target} ${routesList}`)
                    resolve();
                },1000)
            });
        });
    }
}
```

模拟webpack编译时，插件执行

```javascript
const myPlugin = new MyPlugin();
 
const options = {
    plugins: [myPlugin]
}

const compiler = new Compiler();

for (const plugin of options.plugins) {
    if (typeof plugin === "function") {
        plugin.call(compiler, compiler);
    } else {
      // 使compiler绑定了插件中的方法
        plugin.apply(compiler);
    }
}
compiler.run();
```

### webpack流程

![编译钩子调用顺序](https://images-sally.oss-cn-beijing.aliyuncs.com/images/2022-01-05-%E7%BC%96%E8%AF%91%E9%92%A9%E5%AD%90%E8%B0%83%E7%94%A8%E9%A1%BA%E5%BA%8F-f7a73e0d380ee4f267e022d4bf533cec-534.png)

### 准备工作

1. 新建项目

2. 项目内，新建`bin`目录，将打包工具主程序放入其中

   主程序的顶部应当有：`#! /usr/bin/env node`标识，指定程序执行环境为node

3. 在`package.json`中配置`bin`脚本
4. 通过`npm link`链接到全局包中，供本地测试使用

### 分析webpack打包的bundle文件

其内部是实现了`__webpack_require__`函数，递归导入依赖关系。

```javascript
(function (modules) {
  // webpackBootstrap
  // The module cache
  var installedModules = {};
  function __webpack_require__(moduleId) {
    // Check if module is in cache
    if (installedModules[moduleId]) {
      return installedModules[moduleId].exports;
    }
    // Create a new module (and put it into the cache)
    var module = (installedModules[moduleId] = {
      i: moduleId,
      l: false,
      exports: {},
    });

    // Execute the module function
    modules[moduleId].call(module.exports, module, module.exports, __webpack_require__);

    // Flag the module as loaded
    module.l = true;

    // Return the exports of the module
    return module.exports;
  }
  return __webpack_require__((__webpack_require__.s = "./src/index.js"));
})(
  {
    "./src/index.js":
      function (module, exports, __webpack_require__) {
        eval('let news = __webpack_require__(/*! ./news.js */ "./src/news.js");\r\nconsole.log(news.content);\n\n//# sourceURL=webpack:///./src/index.js?');
      },

    "./src/message.js":
      function (module, exports) {
        eval("module.exports = {\r\n  content: 'today it`s raining!'\r\n};\n\n//# sourceURL=webpack:///./src/message.js?");
      },

    "./src/news.js":
      function (module, exports, __webpack_require__) {
        eval(
          "let message = __webpack_require__(/*! ./message.js */ \"./src/message.js\");\r\nmodule.exports = {\r\n  content: 'Today, we have a explosive news! The content is ' + message.content,\r\n};\n\n//# sourceURL=webpack:///./src/news.js?"
        );
      },
  }
);
```

### loader

loader：webpack只能处理js文件，如果需要处理其他文件，或对js代码做一些操作，则需要用到loader。loader的主要功能是将能与匹配规则匹配上的文件进行加工处理，生成最终的代码后输出，是webpack打包环节中非常最终的一环。

loader分为：前置pre 普通 后置post

可以在rule 中配置enforce: pre | post，强制干预loader的执行顺序

默认loader执行顺序：从下到上，从右到左。

#### 自定义loader

1、编写`myLoader.js`，其实loader就是对外暴露一个函数

```javascript
const loaderUtils = require('loader-utils');
const fs = require('fs');
const path = require('path');

module.exports = function(source) {
  // 获取配置loader时，使用options配置的参数
  const { name } = loaderUtils.getOptions(this);
  // loader的异步处理
  const callback = this.async(); 

  // 关掉缓存
  // this.cacheable(false);

  const json = JSON.stringify(source)
    .replace("foo", "")
    .replace(/\u2028/g, "\\u2028") // 替换行分隔符
    .replace(/\u2029/g, "\\u2029"); // 替换段落分隔符
    
    // 一、抛出异常的方式
    // throw new Error('Error');
    // this.callback(new Error('Error'), json);
    
    // 二、将处理后的结果返回
    // return `export default ${json}`;
    // this.callback(null, json, 2, 3, 4); // 可以回传多个值

    fs.readFile(path.join(__dirname, 'demo.txt'), 'utf-8', (err, data) => {
      callback(null, data);
    });
    
    // loader文件输出
    const url = loaderUtils.interplateName(this, "[hash].[ext]", { content, }); 
    this.emitFile(url, content);
    
};

```

2、同样在webpack.config.js中配置自定义的loader，原理是引用了`loader-runner`，

```javascript
module.exports = {
  module: {
    rules: [
      { test: /\.js$/, use: './loaders/myLoader.js',  }
    ]
  }
}
```

3、添加loader

webpack能支持loader，主要步骤如下：

1. 读取webpack.config.js配置文件的module.rules配置项，进行倒序迭代(rules的每项规则按倒序匹配)
2. 根据正则匹配到对应的文件类型，同时再批量导入loader函数
3. 倒序迭代所有loader函数(loader的加载顺序从右到左，也是倒序)
4. 使用loader- runner处理，返回处理后的代码

### plugin

插件没有loader那种的独立运行环境，只能在webpack中运行。原理是将需要实现的逻辑绑定webpack compile中生命的一些hooks，当webpack编译时，在不同阶段触发这些hook的执行

插件的基本结构：

```javascript
class MyPlugin { 
  apply(compiler) {  // 必须要有apply方法
    compiler.hooks.done.tap('My Plugin', ( 
      stats /* stats is passed as argument when done hook is tapped. */ 
    ) => { console.log('Hello World!'); }); 
  } 
}
module.exports = MyPlugin;
```

## webpack优化

**优化打包速度**

1. 优化babel-loader，使用include exclude 
2. 使用thread-loader多线程
3. 使用dllPlugin 提前打包库
4. 使用缓存 cache 字段 和 babel-cache `cacheDirectory: true`

**优化打包体积**

1. `mini-css-extract-plugin`是用于将css提取为独立的文件的插件，对每个包含css的js文件都会创建一个css文件，支持按需加载css
2. 使用ignorePlugin插件来忽略掉一些占空间的文件，比如 `new webpack.IgnorePlugin(/\.\/locale/, /moment/)`
3. scope hoisting分析出模块之间的依赖关系，尽可能的把打散的模块合并到一个函数中去，但前提是不能造成代码冗余。因此只有那些被引用了一次的代码才能被合并。
4. tree shaking   Production模式会自动打开

## webpack5

### 开启缓存

https://developer.51cto.com/article/683685.html

https://webpack.js.org/configuration/cache/

```
cache: boolean｜ object；
```

- cache.type：缓存类型，支持 'memory' | 'filesystem'，需要设置 filesystem 才能开启持久缓存
- cache.cacheDirectory：缓存文件存放的路径，默认为 node_modules/.cache/webpack
- cache.buildDependencies：额外的依赖文件，当这些文件内容发生变化时，缓存会完全失效而执行完整的编译构建，通常可设置为项目配置文件

- cache.managedPaths：受控目录，Webpack 构建时会跳过新旧代码哈希值与时间戳的对比，直接使用缓存副本，默认值为 ['./node_modules']

- cache.profile：是否输出缓存处理过程的详细日志，默认为 false
- cache.maxAge：缓存失效时间，默认值为 5184000000

















