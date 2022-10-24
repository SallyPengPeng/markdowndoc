## yargs

Yargs helps you build interactive command line tools, by parsing arguments and generating an elegant user interface.

可以基于输入的语句实现动态的菜单。

## chalk

使输出变为不同颜色

![chalk](https://images-sally.oss-cn-beijing.aliyuncs.com/images/2022-01-05-chalk-517d38357a7953f37dac6fda9b8ae5d1-b39.png)

```javascript
const yargs = require('yargs');
const chalk = require('chalk');

const argv = yargs
  .usage('Usage: $0 <command> [options]') // 
  .command('start', 'Run the development server', function() {
    console.log(chalk.blue('[quantex-scripts] Running start commander...'));
  }) // 定义命令
  .command('theme', 'Generate the theme css file', function() {
    console.log(chalk.green('[quantex-scripts] Running theme commander...'));
  })
	// 定义输入变量e
  .option('e', {
    description: 'Environment passed to the config',
    alias: 'env', // 不管输入的是e 还是 env，得到参数包含 e  env，值相等
    choices: ['development', 'production'], // 限制输入只能在规定值里面
    requiresArg: true, // 指定选项必须后跟选项值的选项数组。如果缺少任何选项值，则显示使用信息并退出。
  })
  .help('help')
  .alias('h', 'help')
  .version('version', `1.0.1`)
  .alias('v', 'version')
  .example(
    '$0 start -c=test.config.js',
    'Start the development server with custome configration file'
  )
  .epilog('For more information, visit https://gitlab.iquantex.com/frameworks/quantex-scripts.git')
  .showHelpOnFail(false, 'whoops, something went wrong! run with --help').argv;

console.log('1111', argv);  
```

输出：

```shell
PS D:\sally\code\z-sally> node yargs.js start build hahha --ship=4
[quantex-scripts] Running start commander...
1111 { _: [ 'start', 'build', 'hahha' ], ship: 4, '$0': 'yargs.js' }

PS D:\sally\code\z-sally> node yargs.js start build hahha --e=4   
[quantex-scripts] Running start commander...
Invalid values:
  Argument: e, Given: 4, Choices: "development", "production"
  
PS D:\sally\code\z-sally> node yargs.js start build hahha --p='kkk'
[quantex-scripts] Running start commander...
1111 {
  _: [ 'start', 'build', 'hahha' ],
  p: 'kkk',
  port: 'kkk',
  '$0': 'yargs.js'
}
```

## mkdirp

和Linux中的`mkdir -p`类似，只不过是在node中

```javascript
const mkdirp = require('mkdirp')
 
// return value is the first directory created
const made = mkdirp.sync('/tmp/foo/bar/baz')
console.log(`made directories, starting with ${made}`)
```

## chokidar

https://www.jianshu.com/p/e7b530579ab3

https://segmentfault.com/a/1190000012855435

https://github.com/paulmillr/chokidar

用于文件监控。

```javascript
const chokidar = require('chokidar');

const watcher = chokidar.watch('file, dir, glob, or array', {
  persistent: true, // 表示是否保护进程不退出持久监听，默认值为true
  ignoreInitial: true, // 默认false: 在ready事件之前就监听add/addDir事件
  interval: 1000, // 默认100，文件系统轮询的间隔，以毫秒为单位
});

watcher
  .on('add', path => console.log(`File ${path} has been added`))
  .on('change', path => console.log(`File ${path} has been changed`))
  .on('unlink', path => console.log(`File ${path} has been removed`))
  .on('addDir', path => console.log(`Directory ${path} has been added`))
  .on('unlinkDir', path => console.log(`Directory ${path} has been removed`))
  .on('error', error => console.log(`Watcher error: ${error}`))
  .on('ready', () => console.log('Initial scan complete. Ready for changes'))
  .on('all', (event, path) => console.log(event,path))
  .on('raw', (event, path, details) => {
    log('Raw event info:', event, path, details);
  });
```

## express

Fast, unopinionated, minimalist web framework for node.

```javascript
const express = require('express')
const app = express()
 
app.get('/', function (req, res) {
  res.send('Hello World')
})

app.listen(3000)
```

### express

1、`express.static(root, [options])`：提供静态文件支持

https://blog.csdn.net/mimikuer/article/details/78919349

```javascript
app.use(express.static('/dist'));
```

访问静态文件时，不需要加上文件目录，直接访问 `http://localhost:3000/css/style.css`等，就可以得到静态文件。

### application

1、`app.use([path,] callback [, callback...])`：设置 middleware，当请求与设置路径匹配时，会先执行middleware function

```javascript
// path默认是'/' 所有每一个请求都会经过以下middleware function
app.use(function (req, res, next) {
  console.log('Time: %d', Date.now())
  next()
})
```

2、`app.listen(path, [callback])`：返回一个`http.server`对象

## cors

为express提供middleware that can be used to enable CORS with various options.

```javascript
var express = require('express')
var cors = require('cors')
var app = express()

var corsOptions = {
  origin: 'http://example.com',
  optionsSuccessStatus: 200 // some legacy browsers (IE11, various SmartTVs) choke on 204
}
app.use(cors(corsOptions));
// 单个请求使用
app.get('/products/:id', cors(corsOptions), function (req, res, next) {
  res.json({msg: 'This is CORS-enabled for a Single Route'})
})


```

配置：

| 配置项 | 说明                                                       | 格式 |
| ------ | ---------------------------------------------------------- | ---- |
| origin | Configures the **Access-Control-Allow-Origin** CORS header |      |

## mocker-api

？？creates mocks for REST APIs. It will be helpful when you try to test your application without the actual REST API server.

## http-proxy

HTTP programmable proxying library that supports websockets

```javascript
var http = require('http'),
    httpProxy = require('http-proxy');
 
//
// Create a proxy server with custom application logic
//
var proxy = httpProxy.createProxyServer({});
 
//
// Create your custom server and just call `proxy.web()` to proxy
// a web request to the target passed in the options
// also you can use `proxy.ws()` to proxy a websockets request
//
var server = http.createServer(function(req, res) {
  // You can define here your custom logic to handle the request
  // and then proxy the request.
  proxy.web(req, res, { target: 'http://127.0.0.1:5050' });
});
 
console.log("listening on port 5050")
server.listen(5050);
```

## connect-history-api-fallback

SPA应用使用HTMLS History API，会导致一些问题，这个插件就能解决一些问题。无法定位到索引文件的情况下，在满足特定的条件时，这个插件能够定位到指定的索引文件。

```javascript
app.use(
  historyApiFallback({
    index: process.env.PUBLIC_PATH + 'index.html',
  })
); // 处理 404, 必须放在 webpack-middleware 之前, 否则不生效
```

## npm start

1. prestart  "rimraf dist && quantex-scripts theme --env=development" ？

2. cross-env NODE_ENV=development quantex-scripts start

   cross-env NODE_ENV=development 传递NODE_ENV参数到process.env中，实际执行 quantex-scripts start

   1. require('../scripts/gen-tmp-dir'); 

      在 node_modules/@gza/quantex-scaffold/app目录下建立.tmp文件夹

   2. require('../scripts/watch'); 

      1. 监控app/pages文件夹下 add unlink事件，有变化时开启2个子进程
         1. node gen-comp-id.js 在.tmp目录下增加compIds.ts文件，记录所有jsx tsx文件的hash值
         2. node gen-path.js 在.tmp目录下增加path.json文件，记录所有index文件的路径
      2. 监控config.ts文件变化，重置环境变量
      
   3. require('../scripts/gen-all');

      1. require ('../scripts/gen-modify-layouts'); 在.tmp目录下增加modify-layouts.ts文件？？？？
      2. require('../scripts/gen-entry-import'); 在.tmp目录下增加addEntryImports.js文件？？？？
      3. require('../scripts/gen-path'); 在.tmp目录下增加path.json文件，记录所有index文件的路径
   
   4. require('../scripts/start');
   
      1. 使用`express`创建web server
      2. 支持配置 yapi、本地mock
      3. `express.static`支持静态资源文件目录
      4. `historyApiFallback`处理404
      5. express监听，启动server，使用`webpackMiddleware`实现热更新
   

## npm run dist

1. "predist": "rimraf dist && rimraf node_modules/.cache/dist-cache && npm run dll-prod && quantex-scripts theme"
  	1. "rimraf dist && rimraf node_modules/.cache/dist-cache
  	2. npm run dll-prod
       	1. require('../scripts/dll'); 在 node_modules/@gza/quantex-scaffold建立build文件夹，存放打包好的mobx react等文件
2. "dist": "cross-env NODE_ENV=production quantex-scripts build",
  	1. require('../scripts/gen-tmp-dir'); 在node_modules/@gza/quantex-scaffold/app目录下建立.tmp文件夹
  	2. require('../scripts/gen-all');
       	1. require ('../scripts/gen-modify-layouts'); 在.tmp目录下增加modify-layouts.ts文件？？？？
            	2. require('../scripts/gen-entry-import'); 在.tmp目录下增加addEntryImports.js文件？？？？
            	3. require('../scripts/gen-path'); 在.tmp目录下增加path.json文件，记录所有index文件的路径
 	3. require('../scripts/build'); 构建 + 输出 ？需要细看webpack原理、打包过程、插件作用及配置

