 node.js使用的编程语言是JavaScript语言，node.js是javaScript运行环境，能够解析和执行js代码(以前只有浏览器可以解析执行js代码)，

node 可以做web服务器后台、命令行工具

不使用插件情况下，浏览器不支持模块化，必须使用`<script>`标签加载，还必须考虑加载的顺序问题

## node模块

### node.js模块

模块可分为3类：

核心模块Core Module原生模块：fs http path ……

第三方模块

自定义模块：mime cherrio ……

#### require加载

```javascript
require(模块标识符);
```

1、require加载模块是同步的，在node中没有全局作用域，只有模块作用域

- 使用require导入模块，有2个作用：

  1. 加载文件模块并执行里面的代码

  2. 拿到被加载文件模块导出的接口对象exports

- require('mime') 执行步骤;

  - 是核心模块，直接加载核心模块
  - 加载第三方模块，例如 `require('art-template')`
    - 1、
      - 先找到当前文件所处目录中的`node_modules`目录
      - 找到`node_modules/art-template/package.json`文件
      - 找到`node_modules/art-template/package.json`文件中的`main`入口
      - `main`属性中记录了`art-template`的入口模块
      - 然后就加载使用第三方包，实际加载的还是文件
    - 2、如果`package.json`文件不存在，或`main`指定的入口模块不存在，node会自动找该目录下的`index.js`,`index.js`是默认备选项
    - 3、如果以上条件都不成立，则会进入上一级目录中的`node_modules`目录查找，如果上一级还没有，则继续往上上一级查找，如果直到磁盘根目录还找不到，则报错

2、require加载模块注意点：

- 所有模块第一次加载完后会有缓存，二次加载直接读取缓存，故模块中的代码只在第一次加载的时候执行一次
- 每次加载模块都优先从缓存加载，缓存没有才会按照node.js加载模块的规则查找
- 核心模块在node.js源码编译时，已经编译为二进制执行文件，所以加载速度仅次于缓存加载
- 试图加载一个和核心模块同名的自定义模块（第三方模块）是不会成功的，自定义模块要么名字不与核心模块相同，要么使用路径的方式加载

#### 导出exports

模块引用使用require，模块暴露使用module.exports和exports

- `module.exports`和`exports`默认指向的是内存中的同一块地址，`exports`是`module.exports`的一个引用。`require`最终返回的是`module.exports`，`exports`默认是一个空对象

### 特殊成员

在每个模块中，除了`require`、`exports`等模块相关API之外，还有两个特殊成员

#### __dirname

可以用来获取当前文件模块所属路径的绝对路径

#### __filename

可以用来获取当前文件的绝对路径

### process模块

全局变量，无需引入，直接可以使用，提供了有关当前 `Node.js`进程的信息并对其进行控制

#### cwd

`process.cwd()`：返回 当前执行Node.js 进程的工作目录，区别于`__dirname`，其表示的是当前执行文件的所在目录

```javascript
C:\Users\Belle>node
Welcome to Node.js v14.15.4.
Type ".help" for more information.
> process.cwd()
'C:\\Users\\Belle' 
```

#### Env

`process.env`：存储包含用户环境信息的object

```javascript
console.log(process.env);
// 新增属性
process.env.TEST = 1;
onsole.log(process.env.TEST); // 1
// 删除属性
delete process.env.TEST;
onsole.log(process.env.TEST); // undefined
```

#### argv

`process.argv`：返回一个数组，其中包含启动 Node.js 进程时传递的命令行参数。

```javascript
process.argv.forEach((val, index) => {
  console.log(`${index}: ${val}`);
});
```

输出结果：

```shell
$ node process-args.js one two=three four
0: E:\software\nodejs\node.exe // 启动 Node.js 进程的可执行文件的绝对路径名
1: D:\sally\code\z-sally\process-args.js // 正在执行的 JavaScript 文件的路径 
2: one
3: two=three
4: four
```

#### chdir

改变当前node.js进程的目录

```javascript
console.log(`Starting directory: ${process.cwd()}`);
try {
  process.chdir('/tmp');
  console.log(`New directory: ${process.cwd()}`);
} catch (err) {
  console.error(`chdir: ${err}`);
}
```

### Buffer模块

在全局作用域中，无须`require`导入。缓冲区，用来存储二进制数据，其的形式可以理解成一个数组，数组中的每一项，都可以保存8位二进制：`00000000`，也就是一个字节

```javascript
const b1 = Buffer.from('10');
const b2 = Buffer.from('10', 'utf8'); // 创建以utf8编码的字符‘10’的buffer
const b3 = Buffer.from([10]);
const b4 = Buffer.from(b3);
console.log(b1, b2, b3, b4); // <Buffer 31 30> <Buffer 31 30> <Buffer 0a> <Buffer 0a>

const bAlloc1 = Buffer.alloc(10); // 创建一个大小为 10 个字节的缓冲区
const bAlloc2 = Buffer.alloc(10, 1); // 建一个长度为 10 的 Buffer,其中全部填充了值为 `1` 的字节
console.log(bAlloc1); // <Buffer 00 00 00 00 00 00 00 00 00 00>
console.log(bAlloc2); // <Buffer 01 01 01 01 01 01 01 01 01 01>
```

### path模块

对文件路径进行操作

path.parse 将一个路径转为对象：root根路径、dir目录、base包含后缀名的文件名、ext后缀名、name不包含后缀名的文件名

#### join

使用平台特定的分隔符把全部给定的 path 片段连接到一起

```javascript
// join使用平台特定的分隔符把全部给定的 path 片段连接到一起，并规范化生成的路径，".."会向前跳一个目录
path.join('/foo', 'bar', 'baz/asdf', 'quux', '..'); // Returns: '/foo/bar/baz/asdf'
```

#### resolve

把一个路径或路径片段的序列解析为一个绝对路径

```javascript
path.resolve('/foo/bar', './baz');
// Returns: '/foo/bar/baz'

path.resolve('/foo/bar', '/tmp/file/');
// Returns: '/tmp/file'

path.resolve('wwwroot', 'static_files/png/', '../gif/image.gif');
// If the current working directory is /home/myself/node,
// this returns '/home/myself/node/wwwroot/static_files/gif/image.gif'
```

#### relative

根据当前工作目录返回从from到的相对路径to

```javascript
path.relative('C:\\orandea\\test\\aaa', 'C:\\orandea\\impl\\bbb');
// Returns: '..\\..\\impl\\bbb'
```

#### `path.sep`

提供特定于平台的路径段分隔符：

- `\` 在 Windows 上
- `/` 在 POSIX 上

#### basename

`path.basename(path[, ext])`：返回路径的最后一部分

```javascript
path.basename('/foo/bar/baz/asdf/quux.html');
// Returns: 'quux.html'

path.basename('/foo/bar/baz/asdf/quux.html', '.html');
// Returns: 'quux'
```

### fs模块

fs 模块对文件进行操作，该模块提供本地文件的读写能力

#### readFile

```js
var fs = require('fs');
var path = require('path');
// __dirname 当前执行的js文件的路径
// __filename 当前执行的js文件的完整路径
var filename = path.join(__dirname, 'hello.txt');
fs.readeFile(filename, 'utf8', function(err, data) {
    if(err) {
        // 文件操作是异步的，try catch只能针对同步操作捕获异常
        throw err;
    }
   // data默认是二进制数据
    console.log(data);
});
```

#### writeFile

`fs.writeFile(file, data[, options], callback)`

#### existsSync

`fs.existsSync(path)`：检查路径是否存在，存在返回`true`，否则返回`false`

#### realpathSync

`fs.realpathSync(path[, options])`：通过解析异步计算规范路径名`.`，`..`和符号链接。

#### readdirSync

`fs.readdirSync(path[, options])`：读取目录的内容。只能读顶层

```javascript
fs.readdirSync(path.join(__dirname, 'test'));
// [ '1test', 'yargs.js' ]
```

#### statSync

`fs.statSync(path[, options])`：获取关于文件的信息，返回`fs.Stats`

```javascript
fs.statSync(path).isDirectory(); // 判断path 是否是路径
```

### http模块

- request：服务器解析用户提交的http请求报文，将结果解析到request对象中，凡是要获取和用户请求相关的数据都可以通过request对象获取

  | request常用属性              | 解析                                                       |
  | ---------------------------- | ---------------------------------------------------------- |
  | request.headers              | 获取所有请求报文头，返回的是一个队形，包含所有的请求报文头 |
  | request.rawHeaders           | 返回的是一个数据，保存的是请求报文头的字符串               |
  | request.httpVersion          | 获取请求客户端所使用的http版本                             |
  | request.method               | 获取客户端所使用的的方法                                   |
  | request.url                  | 获取这次请求的路径                                         |
  | request.socket.remotePort    | 请求的客户端的端口号                                       |
  | request.socket.remoteAddress | 请求的客户端的IP地址                                       |

- response：在服务器端用来向用户做出响应的对象。凡是需要向用户（客户端）响应的操作，都需要通过response对象来进行。

```js
// 引入http模块
var http = require('http');
var server = http.createServer();
// 监听request请求事件，设置请求处理函数
server.on('request', function(req, res) {
   // res.write('Hello Belle');
   // res.end();
   // 设置响应头编码类型
   res.setHeader('Content-Type', 'text/plain; charser=utf-8');
   res.end('Hello Belle'); // 响应数据只能是二进制或字符串，其他类型数据需要转换
})
// 绑定端口号，启动服务
http.listen(3000, function() {})
```

```js
// 以上简单写法
// 引入http模块
var http = require('http');
// 创建http服务，监听，启动
http.createServer(function(req, res) {
  //  设置http响应报文头，告诉浏览器使用的编码,避免乱码
  res.setHeader('Content-Type', "text/html; charset=utf-8");
  // res.write('Hello Belle');
  // res.end();
  // 先write 在end一般不用，一般直接end同时发response
  // req.url 请求的路径，可以根据不同的请求路径做出不同的反应
  res.end('Hello Belle');
}).listen(3000, function() {
  console.log('请访问： http://localhost:8080');
});
```

content-type类型对照： https://tool.oschina.net/commons

| content-type常用类型 |              |
| -------------------- | ------------ |
| text/plain           | 普通格式文本 |
| text/html            | html格式文本 |
| image/png            | png格式编码  |

模拟apache服务器，服务器将静态资源统一放在一个地方，判断客户端如果请求的是静态资源，那么直接拼接路径查找文件

```js
var http = require('http');
var path = require('path');
var fs = require('fs');
// 第三方模块mime，需要npm install mime，处理res header的文件类型
var mime = require('mime'); 

http.createServer(function(req, res) {
   var publicDir = path.join(__dirname, 'public');
   var filename = path.join(publicDir, req.url);
    
   fs.readFile(filename, function(err, data) {
       if(err) {
         res.setHeader('Content-Type', "text/plain; charset=utf-8");
         res.end('文件不存在');
       } else {
           res.setHeader('Content-Type', mime.getType(filename));
           res.end(data);
       }
   }); 
});
```

### child_process模块

生成子进程

#### Exec

`child_process.exec`：生成一个 shell，然后`command`在该 shell 内执行

 `child_process.exec(command[, options][, callback])`

```javascript
const { exec } = require('child_process');
exec('cat *.js missing_file | wc -l', (error, stdout, stderr) => {
  if (error) {
    console.error(`exec error: ${error}`);
    return;
  }
  console.log(`stdout: ${stdout}`);
  console.error(`stderr: ${stderr}`);
});
```

#### spawn

`child_process.spawn()`使用给定的命令生成一个新进程，并在 args 中生成命令行参数。如果省略，args 默认为空数组。

### module

1、`require.resolve(request[, options])`：使用`require`的机制去查询，但是不加载模块，仅返回解析的文件名

2、`require.cache`：模块被加载后会在此对象中缓存，通过delete这个对象中特定key对应的值，可以使下一次引入模块时重新加载

## npm

### 使用nexus搭建npm私服

https://blog.csdn.net/qq_35577655/article/details/118963859

https://blog.csdn.net/wc1695040842/article/details/102576067

### npm install原理

https://juejin.cn/post/6844903897270599693

https://mp.weixin.qq.com/s?__biz=Mzg2NDAzMjE5NQ==&mid=2247485074&idx=1&sn=a4b39ef1fcad427e79a8714c41b8eb0b&scene=21#wechat_redirect

![图片](https://images-sally.oss-cn-beijing.aliyuncs.com/images/2022-01-05-npm%20install%E5%8E%9F%E7%90%86-c8ce8b473d1fcfe68d1250cec436a9bf-3ec.png)

`npm`设置`registry`的作用：To resolve packages by name and version, npm talks to a registry website。获取需安装依赖的详细信息。

`npm install`整体流程：

- 检查 `.npmrc` 文件：优先级为：项目级的 `.npmrc` 文件 > 用户级的 `.npmrc` 文件> 全局级的 `.npmrc` 文件 > npm 内置的 `.npmrc` 文件

- 检查项目中有无 `lock` 文件。

- 无 `lock` 文件：

  -  `npm` 远程仓库获取包信息

  - 根据 `package.json` 构建依赖树，构建过程：

    - 构建依赖树时，不管其是直接依赖还是子依赖的依赖，优先将其放置在 `node_modules` 根目录。

    - 当遇到相同模块时，判断已放置在依赖树的模块版本是否符合新模块的版本范围，如果符合则跳过，不符合则在当前模块的 `node_modules` 下放置该模块。
    - 注意这一步只是确定逻辑上的依赖树，并非真正的安装，后面会根据这个依赖结构去下载或拿到缓存中的依赖包

  - 在缓存中依次查找依赖树中的每个包   

    - 不存在缓存：

      - 从 `npm` 远程仓库下载包

      - 校验包的完整性

      - 校验不通过：

        - 重新下载  

      - 校验通过：

        - 将下载的包复制到 `npm` 缓存目录

        - 将下载的包按照依赖结构解压到 `node_modules`

    - 存在缓存：将缓存按照依赖结构解压到 `node_modules`
    
      - 将包解压到 `node_modules`
    
      - 生成 `lock` 文件


有 `lock` 文件：

- 检查 `package.json` 中的依赖版本是否和 `package-lock.json` 中的依赖有冲突。
- 如果没有冲突，直接跳过获取包信息、构建依赖树过程，开始在缓存中查找包信息，后续过程相同

### npm安装

```
npm install <Module Name> # 安装模块
npm install express # 本地安装
npm install express -g # 全局安装
npm uninstall <Module Name> # 卸载模块
```

本地安装：

- 将安装包放在 ./node_modules 下（运行 npm 命令时所在的目录），如果没有 node_modules 目录，会在当前执行 npm 命令的目录下生成 node_modules 目录。

- 可以通过 require() 来引入本地安装的包。

全局安装：

-  将安装包放在 /usr/local 下或者你 node 的安装目录。
-  可以直接在命令行里使用。

### 常用命令

```shell
npm cache clear  # 清除本地缓存，用于对付用相同版本号发布新版本代码的人
npm cache clean -f  # 清空缓存

npm view jquery versions   # 查看npm服务器上所有的jquery版本
npm view jquery version # 查看npm服务器上jquery最新版本
npm ls jquery # 查看本地当前项目jquery的信息（版本及依赖包）,没有安装jquery，则返回empty的结果
npm ls jquery -g # 查看全局安装的jquery的信息（版本及依赖包）

# 设置源
npm config set registry <源地址>
npm config get registry <源地址>

# 初始化
npm init # 创建一个package.json文件，一步步生成
npm init -y # 一次性生成package.json文件
npm --version

# 查看设置
npm config # 查看当前设置
npm config ls -l # 查看所有设置，包括默认设置

# 下载包
npm install <package-name> 
npm uninstall <package-name> # 卸载包，保留它的依赖信息
npm uninstall <package-name> --save # 卸载包，依赖信息也去除
npm i webpack -g # 全局安装webpack
npm i webpack -D # 项目安装webpack，不加-D，默认装到dependencies
npm install express@3.21.2 # 安装指定版本的依赖

npm 命令 --help # 查看指定命令的使用帮助
```

### cnpm

npm存储包文件的服务器在国外，有时候速度很慢，使用`cnpm`

```shell
npm install --global cnpm
```

接下来安装时都使用`cnpm`

```shell
cnpm install jquery
```

### 模块Modules和包Packages的区别

1. A module is any file or directory that can be loaded by Node.js 'require()'

   模块可以是任何一个文件或目录（目录下可以有很多个文件），只要能被node.js通过'require()'

2. A  package is a file or directory that is described by a package.json.this can happen in a bunch of different ways

### package.json

```
npm install <package-name> [--save] # 自动将包名放到dependencies
npm install <package-name> --save-dev # 自动将包名放到devDependencies
```

1、dependency


```json
{
    "dependencies": {
        "foo": "1.0.0 - 2.9999.9999",   
        "bar": ">=1.0.2 <2.1.2",        必须大于等于1.0.2版本且小于2.1.2版本
        "baz": ">1.0.2 <=2.3.4",        必须大于1.0.2版本且小于等于2.3.4版本
        "boo": "2.3.1",                 必须匹配这个版本
        "boo": "~2.3.1",                约等于2.3.1，只更新最小版本，相当于2.3.X，即>=2.3.1 <2.4.0
        "thr": "2.3.x",
        "boo": "^2.3.1",                与2.3.1版本兼容，相当于2.X.X, 即>=2.3.1 < 3.0.0,不改变大版本号。
        "qux": "<1.0.0 || >=2.3.1 <2.4.5 || >=2.5.2 <3.0.0",
        "asd": "http://asdf.com/asdf.tar.gz",   在版本上指定一个压缩包的url，当执行npm install 时这个压缩包会被下载并安装到本地。
        "til": "~1.2",   
        "elf": "~1.2.3", 
        "two": "2.x",
        "lat": "latest",             安装最新版本
        "dyl": "file:../dyl",         使用本地路径
    }
}
```

2、scripts：

```javascript
// Pre & Post Scripts
{
  "scripts": {
    "precompress": "{{ executes BEFORE the `compress` script }}",
    "compress": "{{ run command to compress files }}",
    "postcompress": "{{ executes AFTER `compress` script }}"
  }
}
```

注：`npm_lifecycle_event` environment variable is set to whichever stage of the cycle is being executed. 

eg: 执行`npm compress`时，`process.env.npm_lifecycle_event` = 'compress'

3、其他属性：


| key             | 释义                                                         | 举例               |
| --------------- | ------------------------------------------------------------ | ------------------ |
| name(必须)      | 包的名字                                                     |                    |
| version(必须)   | 版本                                                         |                    |
| description     | 包描述                                                       |                    |
| author          | 作者                                                         |                    |
| license         | 许可证                                                       |                    |
| dependencies    | Packages required by your application in production.         |                    |
| devDependencies | Packages that are only needed for local                      |                    |
| files           | describes the entries to be included when your package is installed as a dependency |                    |
| main            | a module ID that is the primary entry point to your program. |                    |
| bin             | one or more executable files that be installed into the PATH | {"gza": "cli.js"}, |
| repository      | Specify the place where your code lives                      |                    |

4、package.json 中的 browser，module，main 字段优先级探索

https://juejin.cn/post/6844903862977953806#heading-5

如果 `npm` 包导出的是 ESM 规范的包，使用 module

如果 `npm` 包只在 web 端使用，并且严禁在 server 端使用，使用 browser。

如果 `npm` 包只在 server 端使用，使用 main

如果 `npm` 包在 web 端和  server 端都允许使用，使用 browser 和 main

### package-lock.json

npm5以后才会有这个文件，保存了安装的所有包以及子依赖包的地址，使查找速度更快

- npm5以后的版本安装包不需要加--save参数，它会自动保存依赖信息
- 当安装包的时候，会自动创建或者更新package-lock.json文件
- 有锁定版本的功能，防止自动升级新版

### 发布npm包

1. 官网注册用户

2. 本地登陆 npm login

3. 升级版本: 执行后会自动修改package.json中的版本

   升级补丁版本号：npm version patch 

   升级小版本号：npm version minor 

   升级大版本号：npm version major 

4. 进入到需要发布包的路径，执行npm publish


## npx

npm5.2以上的版本中提供了一个`npx`命令，npx想要解决的主要问题，就是调用项目内部安装的模块，原理就是在`node_modules`下的`.bin`目录中找到对应的命令执行

```shell
# 使用webpack命令
npx webpack
```

## nrm

```
# nrm管理源 
npm install -g nrm # 安装 
nrm -h # 查看命令
nrm ls  # 查看已有的源 
nrm add <源名称> <源地址>  # 新增源 
nrm use <源名称>   # 切换源
nrm current  # 查看当前源
nrm del <源名称> #删除源
```

## nvm

```
# node version manager
nvm version # 查看版本
nvm install latest
nvm install 版本号
nvm uninstall 版本号
nvm list # 查看安装的node版本
nvm use 版本号 
```

## 中间件

封装http请求细节处理的方法，本质是函数，是在进入具体的业务处理之前，先让特定过滤器处理。用户从请求到响应过程中的处理环境分步骤处理，每个步骤调用一个方法完成，这个方法就是中间件（中间处理环节）。一定在路由前挂载，因为需要在路由里使用，接收三个参数（req,res,next）

**自己实现一个中间件**

```javascript
const m1 = async (req, res, next) => {
    // something...
    let result = await next();
  }
  
  const m2 = async (req, res, next) => {
    // something...
    let result = await next();
  }
  const m3 = async (req, res, next) => {
    // something...
    let result = await next();
    return result;
  }

const middlewares = [m1, m2, m3];

function useApp (req, res) {
    const next = () => {
      const middleware = middlewares.shift()
      if (middleware) {
        return Promise.resolve(middleware(req, res, next))
      }else {
        return Promise.resolve("end")
      }
    }
    next()
  }
// 启动中间件
useApp()
```

**express和koa的区别：**

相同：中间件的执行顺序都是**自上而下**的

不同：

- `Express` 中间件链是基于回调的。`Koa` 是基于 Promise 的
- `Express`支持路由。`Koa`中没有路由，默认情况它会匹配/

- `Express`为 线性模型，是一层一层嵌套的回调，`Koa`为 洋葱型模型

  ```javascript
  // express的执行，类似是以以下方式执行的
  app.use((req, res, next)=>{
      console.log('第一个中间件start');
      ((req, res, next)=>{
          console.log('第二个中间件start');
          (function handler(req, res, next) {
              // do something
          })()
          console.log('第二个中间件end');
      })()
      console.log('第一个中间件end');
  });
  
  app.listen(3000);
  // 第一个中间件start
  // 第二个中间件start
  // 第二个中间件end
  // 第一个中间件end
  ```

  koa的中间件执行类似以下方式：(返回顺序也是以上)

  ```javascript
  function mycompose() {
    return function () {
      const ctx = {}
      return Promise.resolve(fn1(ctx, () => {
        return Promise.resolve(fn2(ctx, () => {
        }))
      }))
    }
  }
  mycompose()()
  
  ```

### Express

用于Node.js 的快速、独立、简约的 Web 框架

1、基本使用

```javascript
/ 0. 安装
// 1. 引用
var express = require('express');

// 2. 创建服务器应用程序，也就是原来的http.creatServer
var app = express();

// 路由
// 当服务器收到get 请求 / 的时候，执行回调处理函数
app.get('/', function(req, res) {
  res.end('hello express');
});
// 当服务器收到post 请求 / 的时候，执行回调处理函数
app.post('/', function(req, res) {
  res.end('hello express');
});

// 相当于server.listen
app.listen(3000, function() {
  console.log('app is running at port 3000');
});
```

2、静态服务

```javascript
// 公开指定目录，可以直接通过 /static/XX的方式访问public目录中的所有资源了
// 例如 127.0.0.1:3000/static/image，实际访问./public/XX下的文件
app.use('/static/', express.static('./public/'));
// 当省略第一个参数时，则可以通过省略/public的方式访问
// 例如：127.0.0.1:3000/image
app.use(express.static('./public/'))
app.use('/static', express.static(path.join(__dirname, 'public')))
```

3、获取表单post请求体数据

在express中没有内置获取表单post请求体的API，使用一个第三方包：body-parse

安装：

```shell
npm install --save body-parse
```

配置：

```javascript
var express = require('express');
// 0. 引包
var bodyParser = require('body-parser');
var app = express();

// 配置 body-parser
// 只要加入这个配置，则在req请求对象上会多出来一个属性：body
// 也就是说可以直接通过req.body来获取表单post请求体数据了
// parse application/x-www-form-urlencoded
app.use(bodyParser.urlencoded({ extended: false }));
// parse application/json
app.use(bodyParser.json());

app.use(function(req, res) {
  res.setHeader('Content-Type', 'text/plain');
  res.write('you posted: \n');
  res.end(JSON.stringify(req.body, null, 2));
});
```

4、配置路由模块

路由模块router.js

```javascript
var express = require('express');

// express提供用来包装路由
// 1. 创建一个路由容器
var router = express.Router();

// 2. 把路由都挂载到router路由容器中
router.get('/students', function(req, res) {});
```

入口模块app.js

```javascript
var express = require('express');
var router = require('./router');
var app = express();

app.use(router);
```

### koa

`Koa`的实现主要依赖自身的`koa-compose`，koa实现的核心原理：

```javascript
function compose (middleware) {
  // 提前判断中间件类型,防止后续错误
  if (!Array.isArray(middleware)) throw new TypeError('Middleware stack must be an array!')
  for (const fn of middleware) {
    // 中间件必须为函数类型
    if (typeof fn !== 'function') throw new TypeError('Middleware must be composed of functions!')
  }
  return function (context, next) {
    // 采用闭包将索引缓存,来实现调用计数
    let index = -1
    return dispatch(0)
    function dispatch (i) {
      // 防止next()方法重复调用
      if (i <= index) return Promise.reject(new Error('next() called multiple times'))
      index = i
      let fn = middleware[i]
      if (i === middleware.length) fn = next
      if (!fn) return Promise.resolve()
      try {
        // 包装next()返回值为Promise对象
        return Promise.resolve(fn(context, dispatch.bind(null, i + 1)));
      } catch (err) {
        // 异常处理
        return Promise.reject(err)
      }
    }
  }
}
```

实际使用：

```javascript
const Koa = require("koa")
const compose = require("koa-compose")
const app = new Koa()
let f1 = async (ctx,next)=>{
    console.log(f1);
    await next()
}
let f2 = async (ctx,next)=>{
    console.log(f2);
    await next()
}
let f3 = async (ctx,next)=>{
    console.log(f3);
    await next()
}
let all = compose([f1,f2,f3])
app.use(all)
app.listen(3000)
```

## MongoDB

1、关系型数据库 和 非关系型数据库

关系型数据库：

- 所有的关系型数据库都需要通过`sql`语言来操作
- 所有的关系型数据库在操作之前都需要设计表结构
- 而且数据表还支持约束：唯一的、主键、默认值、非空

非关系型数据库

## 其他

### node调试环境REPL

类似于浏览器的console

REPL read eval print loop

直接输入node回车，就进入node环境，所有node支持的api不用引入，可以直接使用

例如url.parse('http://www.baidu.com/a/b?a=b');

### 浏览器的组成

- 人机交互部分（UI）
- 网络请求部分（socket）
- JavaScript引擎部分（解析执行JavaScript）
- 渲染引擎部分（渲染HTML CSS等）
- 数据存储部分（cookie HTML5中的本地存储localstorege sessionstorage）

渲染引擎工作原理示意图

![渲染引擎工作原理](https://images-sally.oss-cn-beijing.aliyuncs.com/images/2022-01-05-%E6%B8%B2%E6%9F%93%E5%BC%95%E6%93%8E%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86-fcafff4e25f1e8b769070e1f2130ac32-e04.png)

### IP地址和端口号

域名(www.baidu.com) -》 解析成IP地址

所有需要联网通信的应用程序都会占用一个端口号

IP地址：用来定位计算机   端口号：用来定位具体的应用程序

端口号范围：0-65536

在计算机中有一个默认端口号，最好不要使用，比如http服务的80

### **ES6**模块与**CommonJS**模块

https://juejin.cn/post/6844904067651600391

- CommonJS的`require`语法是同步的，模块规范只适合用在服务端；而ES6模块无论是在浏览器端还是服务端都是可以使用

- CommonJS模块输出的是一个值的浅拷贝，ES6 模块输出的是值的引用
- CommonJS 模块是运行时加载，ES6 模块是编译时输出接口
- 在ES6模块顶层，`this`指向`undefined`；而CommonJS模块的顶层的`this`指向当前模块
- 在ES6模块中可以直接加载CommonJS模块，但是只能整体加载，不能加载单一的输出项；CommonJS不能`require`ES6模块
- 在对待循环加载的时候，CommonJS遇到循环依赖的时候，只会输出已经执行的部分，后续的输出或者变化，不会影响已经输出的变量。而ES6模块相反，使用`import`加载一个变量，变量不会被缓存，真正取值的时候就能取到最终的值

## 问题解决

### node-sass安装失败

https://segmentfault.com/a/1190000022875865

node-sass内部安装步骤：

1. 校验本地node_modules中是否已安装node-sass，版本是否一致;
2. 如未安装或版本不符，从npm源安装node-sass本体;
3. 检测全局缓存和本地中是否有`binding.node`,如有即跳过安装;
4. 没有`binding.node`则从github下载该二进制文件并将其缓存到全局;
5. 假如`binding.node`下载失败，则尝试本地编译出该文件;
6. 将版本信息写到`package-lock.json`;

解决办法：设置sass_binary_site参数，从而设置Sass镜像地址

类似模块问题也是同样设置：

```shell
// node-sass 设置镜像地址
npm config set sass_binary_site https://npm.taobao.org/mirrors/node-sass/
// 其他类似模块
npm config set sharp_dist_base_url https://npm.taobao.org/mirrors/sharp-libvips/
npm config set electron_mirror https://npm.taobao.org/mirrors/electron/
npm config set puppeteer_download_host https://npm.taobao.org/mirrors/
npm config set phantomjs_cdnurl https://npm.taobao.org/mirrors/phantomjs/
npm config set sentrycli_cdnurl https://npm.taobao.org/mirrors/sentry-cli/
npm config set sqlite3_binary_site https://npm.taobao.org/mirrors/sqlite3/
npm config set python_mirror https://npm.taobao.org/mirrors/python/
```

### 删除`nodez-modules`慢

下载`rimraf`，`npm i -g rimraf`。

在项目目录使用 `rimraf node_modules`

