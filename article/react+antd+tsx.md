## 项目初始化

```shell
npx create-react-app user-manage-system --template typescript
```



使用插件覆盖create-react-app本身的webpack配置：

```shell
npm i customize-cra react-app-rewired react-scripts babel-plugin-import --save-dev
```



使用antd

```shell
npm i antd
```



配置eslint .eslantrc.js

```shell
// 参考quantex-scaffold
npm i eslint eslint-config-alloy @typescript-eslint/parser @typescript-eslint/eslint-plugin --save-dev
```

https://eslint.org/docs/rules/

@typescript-eslint/parser： ts 解析器 @typescript-eslint/parser，代替默认的解析器，识别 ts 的一些语法。

@typescript-eslint/eslint-plugin：提供有关 ts 的规则补充。
eslint-plugin-react：支持 .tsx。
eslint-config-alloy：eslint 规则众多，且有些原生规则在 ts 中不兼容，推荐 alloy 的这套配置 ，它提供了 ts + react 的版本，并且不包含代码格式的部分，与 Prettier 完全兼容。

eslint-plugin-import：此插件主要为了校验 `import/export` 语法，防止错误拼写文件路径以及导出名称的问题

eslint-plugin-jsx-a11y：提供 `jsx` 元素可访问性校验

eslint-plugin-react：校验 React

eslint-plugin-react-hooks：根据 Hooks API 校验 Hooks 的使用

https://www.jianshu.com/p/9a2fe17e2da4

忽略校验的：.eslintignore



显示配置文件

```shell
npm run eject
```



配置less-loader，配置css-module

```shell
npm i less less-loader
```

```.js
 // Less 解析配置
 {
   test: lessRegex,
   exclude: lessModuleRegex,
   use: getStyleLoaders(
   {
     importLoaders: 2,
     sourceMap: isEnvProduction && shouldUseSourceMap,
     modules: {
     exportLocalsConvention: 'camelCase',
     localIdentName: '[path][name]__[local]',
     }
   },
   'less-loader'
   ),
   sideEffects: true,
 },
 {
 test: lessModuleRegex,
 use: getStyleLoaders(
   {
     importLoaders: 2,
     sourceMap: isEnvProduction && shouldUseSourceMap,
     modules: {
     exportLocalsConvention: 'camelCase',
     localIdentName: '[path][name]__[local]',
     },
     getLocalIdent: getCSSModuleLocalIdent,
     },
     'less-loader'
   )
 },
```



使用redux

```shell
npm i react-redux redux
```



使用路由

```shell
npm i react-router-dom react-router
```



tsx文件引入alias路径报错

1. 首先`npm run eject `展开扩展，在`webpack config.js` 中 找到 alias 配置别名`"@":path.resolve(__dirname,"../src")`

2. 在tsconfig.json 中，在`compilerOptions` 中配置

   ```javascript
   baseUrl:".",
   paths: {  "@/*":["src/*"] } 
   ```

3. 重启vscode或重新打开项目



tsx文件引入`index.less`报错`cannot find module ……`：

解决办法：增加类型定义文件 `typings.d.ts`文件(文件名任意)，并确保`tsconfig.json`文件的`files` `include` `excluede`等字段控制，不会排除`typings.d.ts`

```typescript
// typings.d.ts
declare module '*.less';
```

原理：ts原则上只能引入js内容的模块，可以通过`declare module`的语句声明其他的模块。



支持拖拽的插件，可支持菜单拖拽

```shell
npm i react-beautiful-dnd
```

<DragDropContext /> - Wraps the part of your application you want to have drag and drop enabled for
<Droppable /> - An area that can be dropped into. Contains <Draggable />s
<Draggable /> - What can be dragged around





使用scss

npm i node-sass sass-loader --save-dev

使用css module，

方法1: 文件名为 index.module.scss

方法2: webpack配置scss文件添加`modules: true`

https://lufanfan.github.io/20190114/cra-css-modules/





tsx文件中引入scss

Can't import CSS/SCSS modules. TypeScript says "Cannot Find Module"

https://stackoverflow.com/questions/40382842/cant-import-css-scss-modules-typescript-says-cannot-find-module

https://blog.csdn.net/qq_41804324/article/details/109388570





