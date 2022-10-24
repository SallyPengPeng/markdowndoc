## cac

**C**ommand **A**nd **C**onquer is a JavaScript library for building CLI apps.(CLI：command-line interface，命令行界面)

```javascript
// 引入cac并创建constance
const cli = require('cac')()

cli
  .command('rm <dir>', 'Remove a dir')
  .option('-r, --recursive', 'Remove recursively')
  .action((dir, options) => {
    console.log('remove ' + dir + (options.recursive ? ' recursively' : ''))
  })


// Display help message when `-h` or `--help` appears
cli.help()
// Display version number when `-v` or `--version` appears
// It's also used in help message
cli.version('0.0.0')
// 必须调用parse，否则cli信息无法打印
cli.parse()
```

使用结果：

```shell
PS D:\sally\code\z-sally> node cac.js rm --help
cac.js/0.0.0

Usage:
  $ cac.js rm <dir>

Options:
  -r, --recursive  Remove recursively     
  -h, --help       Display this message   
  -v, --version    Display version number 
PS D:\sally\code\z-sally> node cac.js rm --version
cac.js/0.0.0 win32-x64 node-v14.15.4
PS D:\sally\code\z-sally> node cac.js rm hahha -r rrr 
remove hahha recursively
PS D:\sally\code\z-sally>
```

注：

1. `rm <dir>`、`rm [dir]`都只能接收一个入参

2. `rm [...dir]`可接收多个入参，且把入参保存在数组dir中

   ```
   ……
   .command('`rm [...dir]`', 'Remove a dir')
   ……
   .action((dir, options) => {
       console.log('remove ' + dir + (options.recursive ? ' recursively' : ''));
       console.log(dir instanceof Array);
     })
   ……  
   ```

   ```shell
   PS D:\sally\code\z-sally> node cac.js rm hahha jjjjj -r hhhh
   remove hahha,jjjjj,hhhh recursively
   true
   ```

## sao

1、新建文件`saofile.js`，用于批量创建文件的`generator`

配置：https://sao.vercel.app/saofile.html#prompts

在文件中使用this访问的变量：https://sao.vercel.app/generator-instance.html#properties

- **prompts**：创建project前，询问user问题并作为变量记录下来，供action时使用

   所有的配置：https://github.com/SBoudrias/Inquirer.js#question

   ```javascript
   prompts() {
       return [
         {
           name: 'name', // 存储下来的变量名
           message: 'What is the name of the new project app', // 提示用户的信息
           default: packageInfo.name || this.outFolder, // 默认的变量value
           filter: (val) => val.toLowerCase(), // 对用户输入数据进行处理
         },
       ];
     },
   ```

- **actions**：用户输入变量结束后执行的动作

  type: add modify move remove

  ```javascript
  actions: [
      {
        // Add files from template directory to target directory
        type: 'add',
        files: '**',
        filters: {
          'config/config.js': !hasPkg
        }
      },
      {
        type: 'modify',
        files: 'package.json',
        // 获取新的文件内容
        handler(data, filepath) {
          if (hasPkg) {
            packageInfo.name = data.name;
            packageInfo.description = data.description;
            packageInfo.version = data.version;
            if (process.env.useBFF !== 'true') {
              // UI 修改依赖为最新脚手架依赖
              packageInfo.dependencies['@gza/quantex-scaffold'] =
                data.dependencies['@gza/quantex-scaffold'];
            } else {
              // BFF 修改私有依赖为最新依赖
              packageInfo.dependencies['@gza/egg-lowdb'] =
                data.dependencies['@gza/egg-lowdb'];
            }
            return packageInfo;
          }
          return data;
        }
      },
      {	
        // 移动文件到指定的目录
        type: 'move',
        // 更改文件名
        patterns: {
          _gitignore: '.gitignore'
        }
      }
    ],
  ```

- **completed**：当所有的actions完成后执行

2、命令行中使用`sao`，新建一个project

```javascript
 sao({ generator, outDir, logLevel, npmClient: "npm", cliOptions })
      .run()
      .catch(err => {
        console.trace(err);
        process.exit(1);
      });
```