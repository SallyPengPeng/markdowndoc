## XML

### XML和HTML之间的差异

- XML 被设计用来传输和存储数据，其焦点是数据的内容。
- HTML 被设计用来显示数据，其焦点是数据的外观
- HTML都是预定义标签，而XML中没有预定义标签，全都是自定义标签，用来展示一些数据

### XML语法规则

- 比如包含根元素，它是所有其他元素的父元素

- XML声明文件可选，如果存在需要放在文档的第一行
- 所有的xml元素都必须有一个关闭标签
- xml标签对大小写敏感
- 实体引用

| 实体引用 | 符号 | 意思           |
| -------- | ---- | -------------- |
| \&lt;    | <    | less than      |
| \&gt;    | >    | greater than   |
| \&amp;   | &    | ampersand      |
| \&apos;  | '    | apostrophe     |
| \&quot;  | "    | quotation mark |

- 在xml中，空格会被保留，html中多个空格会被裁剪合并成一个
- xml以LF存储换行

## 页面导入样式

### link标签 和 @import区别

1. link是html标签，通过href属性来引入外部文件；@import属于CSS，所以导入语句写在CSS中
2. @import是css2才出现的概念，如果浏览器版本太低，则无法正确导入外部要样式文件
3. 当HTML文件被加载时，link引用的文件会同时被加载，而@import引用的文件则会等页面全部下载完毕才会加载

```html
<!--link标签引入-->
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
```

```html
<!--@import引入-->
<head>
    <style>
        @import url(style.css);
    </style>
</head>
```

> link标签还可以配置其他属性

| 属性   | 值                                                           | 描述                               |
| ------ | ------------------------------------------------------------ | ---------------------------------- |
| href   |                                                              | 规定被连接文档的位置               |
| rel    | alternate author help icon licence next pingback prefetch prev search sidebar stylesheet tag | 规定当前文档与被连接文档之间的关系 |
| type   | MIME_type                                                    | 规定被连接文档的MIME类型           |
| target | _blank<br/>_self<br/>_top<br/>_parent<br/>frame_name         |                                    |

### 导入样式的方式

1、内联样式，行内样式

```html
<div style="display: none;background:red"></div>
```

2、嵌入样式

```html
<head>
    <style>
      .content {
         background: red;
       }
    </style>
</head>
```

3、连接样式

```html
<head>
	<link rel="stylesheet" type="text/css" href="mystyle.css">
</head>
```

4、导入样式

```html
<style>
    @import url(style.css);
</style>
```

## script放的位置

将script放在<head>，浏览器解析HTML，发现script标签时，会先下载完所有这些script，再往下解析其他的HTML

将script放在<body>，是浏览器只能先解析完整个HTML页面，再下载JS

使用defer 和 async都是为了避免阻塞后续文档的加载

1. 没有 defer 或 async，浏览器会立即加载并执行指定的脚本，“立即”指的是在渲染该 script 标签之下的文档元素之前，也就是说不等待后续载入的文档元素，读到就加载并执行。
2. 当Script中有defer属性时，文档解析和脚本加载是异步的，等文档解析完脚本才开始执行。
3. 当Script中有async 属性时，文档解析和脚本加载也是异步的，脚本下载完成后会停止HTML解析，执行脚本，脚本解析完继续HTML解析。
4. 当 defer 和 async 同时存在时，执行效果和 async 一致

```html
<script src="script.js"></script>
<script defer src="script.js"></script>
<script async src="script.js"></script>
```



![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ed9091b434fd4854ace75e48661d0b2e~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

## HTML5更新

### 语义元素

使用语义类标签增强了可读性，结构更加清晰，开发者能清晰的看出网页的结构，便于团队的开发与维护。

| 元素    | 解释                                                         | props |
| ------- | ------------------------------------------------------------ | ----- |
| header  | 定义文档或节的页眉                                           |       |
| nav     | 定义导航链接的容器                                           |       |
| section | 定义文档中的节                                               |       |
| article | 定义独立的自包含文章                                         |       |
| aside   | 定义内容之外的内容（比如侧栏）                               |       |
| footer  | 定义文档或节的页脚                                           |       |
| details | 定义用户可打开关闭的额外的细节                               | open  |
| summary | 为 < details > 元素定义了一个可见的标题。点击标题可以查看/隐藏细节。 |       |
| main    | 标签规定文档的主要内容。在一个文档中，不能出现一个以上的 <main> 元素。不能是 < article > 、 < aside > 、 < footer > 、 < header > 或 < nav > 元素的后代。 |       |
| mark    | 突出显示文本，Chrome表现是背景色标黄                         |       |

语义标签默认的样式

```css
 { display: block }
```

### 媒体标签

 （1）audio：音频

```html
<audio src='' controls autoplay loop='true'></audio>
```

属性：

- src 嵌入音频的 URL。 可选属性；你可以在 audio 元素中使用 <source> 元素来替代该属性指定嵌入的音频。

- controls 如果声明了该属性，浏览器将提供一个包含声音，播放进度，播放暂停的控制面板，让用户可以控制音频的播放。
- autoplay 布尔值属性，声明该属性，音频会尽快自动播放，不会等待整个音频文件下载完成。
- loop 布尔属性；如果声明该属性，将循环播放音频。

（2）video视频

```html
<video src='' poster='imgs/aa.jpg' controls></video>
```

属性：

- preload：规定是否在页面加载后载入视频，如果设置了`autoplay`属性，则忽略该属性

- poster：指定视频还没有完全下载完毕，或者用户还没有点击播放前显示的封面。默认显示当前视频文件的第一针画面，当然通过poster也可以自己指定。
- controls 控制面板
- autoplay 自动播放
- width
- height

（3）source标签 因为浏览器对视频、音频格式支持程度不一样，为了能够兼容不同的浏览器，可以通过source来指定视频源。

```html
<video>
 	<source src='aa.flv' type='video/flv'></source>
 	<source src='aa.mp4' type='video/mp4'></source>
</video>
```

### form

**form属性**

| 属性         | 解释                                                         | default |
| ------------ | ------------------------------------------------------------ | ------- |
| method       | 提交时使用的method                                           | get     |
| action       | 表单提交后的动作                                             |         |
| target       | 提交表单后如何展示response。 \_blank \_self \_parent \_top   | \_self  |
| autocomplete | html5新增。规定域应该拥有自动完成功能，自动完成允许浏览器预测对字段的输入。当用户在字段开始键入时，浏览器基于之前键入过的值，应该显示出在字段中填写的选项 on off |         |
| novalidate   | html5新增。boolean 规定在提交表单时不应该验证 form 或 input 域 |         |

**表单元素**

input label select textarea button fieldset legend datalist：描述input预先定义的option

label：定义表单控件的关系，当用户选择label标签时，浏览器会自动将焦点转到和label标签相关的表单控件上。

```javascript
<label for="mobile">Number:</label>
<input type="text" id="mobile"/>

// 或者
<label>Date:<input type="text"/></label>
```

datalist：描述input预先定义的option

```html
<form action="/action_page.php">
  <input list="browsers">
  <datalist id="browsers">
    <option value="Internet Explorer">
    <option value="Firefox">
    <option value="Chrome">
    <option value="Opera">
    <option value="Safari">
  </datalist>
</form>
```

**input类型**

- `<input type="button">`：按钮
- `<input type="checkbox">`：多选框
- `<input type="color">`： 提供了一个颜色拾取器
- `<input type="date">`： 日期选择年月日
- `<input type="datetime-local">`：日期时间控件
- `<input type="month">`：月控件
- `<input type="week">`：周控件
- `<input type="time">`： 时分秒
- `<input type="email">`：能够验证当前输入的邮箱地址是否合法
- `<input type="url">` ： 验证URL
- `<input type="file">`：文件上传的按钮
- `<input type="hidden">`：隐藏的输入域，不对用户可见
- `<input type="number">`： 只能输入数字，自带上下增大减小箭头，max属性可以设置为最大值，min可以设置为最小值，value为默认值。
- `<input type="range">`： 可以提供给一个范围，其中可以设置max和min以及value，其中value属性可以设置为默认值
- `<input type="password">`：输入密码，
- `<input type="radio">`：单选
- `<input type="reset">`：重置按钮
- `<input type="search">`： 输入框后面会给提供一个小叉，可以删除输入的内容，更加人性化。
- `<input type="submit">`：提交按钮
- `<input type="tel">`：电话号码输入
- `<input type="text">`：文本输入

**input属性：**

- value  size maxlength min max multiple placeholder

- readonly disabled 相同：文本框只读，不可修改  
  - disabled会使文本框变灰

  - disabled 通过js也是获取不到的

  - disabled属性对input文本框，单选radio,多选checkbox都适用

- autofocus ：自动获取焦点
- autocomplete=“on” 或者 autocomplete=“off” 使用这个属性需要有两个前提：
  - 表单必须提交过
  - 必须有name属性。
- required：要求输入框不能为空，必须有值才能够提交。
- pattern=" " 里面写入想要的正则模式，例如手机号patte="^(+86)?\d{10}$"
- multiple：可以选择多个文件或者多个邮箱
- form=" form表单的ID"
- step: 指定输入字段的合法数字间隔

### progress标签

用来表示任务的进度（IE、Safari不支持），max用来表示任务的进度，value表示已完成多少

### draggable

dragstart：事件主体是被拖放元素，在开始拖放被拖放元素时触发。

darg：事件主体是被拖放元素，在正在拖放被拖放元素时触发。

dragenter：事件主体是目标元素，在被拖放元素进入某元素时触发。

dragover：事件主体是目标元素，在被拖放在某元素内移动时触发。

dragleave：事件主体是目标元素，在被拖放元素移出目标元素是触发。

drop：事件主体是目标元素，在目标元素完全接受被拖放元素时触发。

dragend：事件主体是被拖放元素，在整个拖放操作结束时触发。

```
<element draggable="true|false|auto">
```

### canvas

canvas是图形容器，使用 JavaScript 在网页上绘制图像，可以控制其每一像素。canvas 拥有多种绘制路径、矩形、圆形、字符以及添加图像的方法。

```html
<canvas id="myCanvas" width="200" height="100"></canvas>
```

#### 绘制形状

**绘制矩形**

`fillRect(x, y, width, height) `绘制一个填充的矩形，默认用黑色填充
`strokeRect(x, y, width, height)` 绘制一个矩形的边框，只用黑色填充边框
`clearRect(x, y, width, height)` 清除指定矩形区域，让清除部分完全透明。

```javascript
function draw() {
  var canvas = document.getElementById('canvas');
  if (canvas.getContext) {
    var ctx = canvas.getContext('2d');

    ctx.fillRect(25, 25, 100, 100);
    ctx.clearRect(45, 45, 60, 60);
    ctx.strokeRect(50, 50, 50, 50);
  }
}
```

**绘制路径**

`beginPath()` 新建一条路径，生成之后，图形绘制命令被指向到路径上生成路径。
`closePath() `闭合路径之后图形绘制命令又重新指向到上下文中。
`stroke() `通过线条来绘制图形轮廓。
`fill() `通过填充路径的内容区域生成实心的图形。
`moveTo(x, y)` 将笔触移动到指定的坐标x以及y上
`lineTo(x,y)  `绘制一条从当前位置到指定x以及y位置的直线

```javascript
function draw() {
  var canvas = document.getElementById('canvas');
  if (canvas.getContext){
    var ctx = canvas.getContext('2d');

    // 填充三角形
    ctx.beginPath();
    ctx.moveTo(25, 25);
    ctx.lineTo(105, 25);
    ctx.lineTo(25, 105);
    ctx.fill();

    // 描边三角形
    ctx.beginPath();
    ctx.moveTo(125, 125);
    ctx.lineTo(125, 45);
    ctx.lineTo(45, 125);
    ctx.closePath();
    ctx.stroke();
  }
}
```

**绘制圆弧**

`arc(x, y, radius, startAngle, endAngle, anticlockwise)` 画一个以（x,y）为圆心的以radius为半径的圆弧（圆），从startAngle开始到endAngle结束，按照anticlockwise给定的方向（默认为顺时针）来生成。`arc()`函数中表示角的单位是弧度

`x,y`为绘制圆弧所在圆上的圆心坐标。`radius`为半径。`startAngle`以及`endAngle`参数用弧度定义了开始以及结束的弧度。这些都是以x轴为基准。参数`anticlockwise`为一个布尔值。为true时，是逆时针方向，否则顺时针方向。

![img](https://images-sally.oss-cn-beijing.aliyuncs.com/images/2022-03-22-548763-20190628140050119-37249460-20220322154037032-2bf9cfa6c1e52ad17d20754ea8969d2d-ee8.png)

```javascript
// 笑脸
function draw() {
  var canvas = document.getElementById("canvas");
  if (canvas.getContext) {
    var ctx = canvas.getContext("2d");

    ctx.beginPath();
    ctx.arc(75, 75, 50, 0, Math.PI * 2, true); // 绘制
    ctx.moveTo(110, 75);
    ctx.arc(75, 75, 35, 0, Math.PI, false); // 口(顺时针)
    ctx.moveTo(65, 65);
    ctx.arc(60, 65, 5, 0, Math.PI * 2, true); // 左眼
    ctx.moveTo(95, 65);
    ctx.arc(90, 65, 5, 0, Math.PI * 2, true); // 右眼
    ctx.stroke();
  }
}
```

#### 样式和颜色

**color**

`fillStyle = color` 设置图形的填充颜色。
`strokeStyle = color` 设置图形轮廓的颜色。
`ctx.globalAlpha` 透明度，从0 -1

```javascript
// 画半透明圆
for (var i=0;i<7;i++){
  ctx.beginPath();
  ctx.arc(75,75,10+10*i,0,Math.PI*2,true);
  ctx.fill();
}
```

**line**

| 属性                  | 解释                                                         | value                                                        |
| --------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| lineWidth             | 设置线条宽度                                                 |                                                              |
| lineCap               | 设置线条末端样式                                             | `butt`，`round` 和 `square`。默认是 `butt。`<br />`butt` 与辅助线齐平的。 `round` 端点处加上了半径为一半线宽的半圆。`square` 端点处加上了等宽且高度为一半线宽的方块。 |
| lineJoin              | 设定线条与线条间接合处的样式                                 | `round`, `bevel` 和 `miter。`默认是 `miter`                  |
| miterLimit            | 限制当两条线相交时交接处最大长度；所谓交接处长度（斜接长度）是指线条交接处内角顶点到外角顶点的长度。 |                                                              |
| getLineDash()         | 返回一个包含当前虚线样式，长度为非负偶数的数组。             |                                                              |
| setLineDash(segments) | 设置当前虚线样式。                                           |                                                              |
| lineDashOffset        | 设置虚线样式的起始偏移量                                     |                                                              |

#### 绘制文本

`fillText(text, x, y [, maxWidth])` 在指定的(x,y)位置填充指定的文本，绘制的最大宽度是可选的.
`strokeText(text, x, y [, maxWidth])` 在指定的(x,y)位置绘制文本边框，绘制的最大宽度是可选的.

#### 变化

*状态的保存和恢复：*

save()  保存画布(canvas)的所有状态
restore()  save 和 restore 方法是用来保存和恢复 canvas 状态的，都没有参数。Canvas 的状态就是当前画面应用的所有样式和变形的一个快照。

*移动translate：*

`translate(x, y)`  移动 canvas 和它的原点到一个不同的位置，x 是左右偏移量，y 是上下偏移量

*旋转rotating：*

`rotate(angle)` 以原点为中心旋转 canvas，是顺时针方向的，以弧度为单位的值

*缩放scaling:*

`scale(x,y)` 缩放画布的水平和垂直的单位。两个参数都是实数，可以为负数，x 为水平缩放因子，y 为垂直缩放因子，如果比1小，会缩小图形， 如果比1大会放大图形。默认值为1， 为实际大小

```js
// mirror horizontally
ctx.scale(-1, 1);
ctx.font = '48px serif';
ctx.fillText('MDN', -135, 120);
```

### SVG

SVG 指可伸缩矢量图形，用于定义用于网络的基于矢量的图形，使用 XML 格式定义图形，图像在放大或改变尺寸的情况下其图形质量不会有损失，它是万维网联盟的标准

SVG和canvas的区别：

（1）SVG： SVG可缩放矢量图形（Scalable Vector Graphics）是基于可扩展标记语言XML描述的2D图形的语言，SVG基于XML就意味着SVG DOM中的每个元素都是可用的，可以为某个元素附加Javascript事件处理器。在 SVG 中，每个被绘制的图形均被视为对象。如果 SVG 对象的属性发生变化，那么浏览器能够自动重现图形。

其特点如下：

- 不依赖分辨率
- 支持事件处理器
- 最适合带有大型渲染区域的应用程序（比如谷歌地图）
- 复杂度高会减慢渲染速度（任何过度使用 DOM 的应用都不快）
- 不适合游戏应用

（2）Canvas： Canvas是画布，通过Javascript来绘制2D图形，是逐像素进行渲染的。其位置发生改变，就会重新进行绘制。

其特点如下：

- 依赖分辨率
- 不支持事件处理器
- 弱的文本渲染能力
- 能够以 .png 或 .jpg 格式保存结果图像
- 最适合图像密集型的游戏，其中的许多对象会被频繁重绘

## data-* 

data-* 属性用于存储页面或应用程序的私有自定义数据。

data-* 属性包括两部分：

- 属性名不应该包含任何大写字母，并且在前缀 "data-" 之后必须有至少一个字符
- 属性值可以是任意字符串

**注释：**用户代理会完全忽略前缀为 "data-" 的自定义属性。

## 列表

无序列表 此列项目使用粗体圆点（典型的小黑圆圈）进行标记

```html
<ul>
  <li>Coffee</li>
  <li>Milk</li>
</ul>
```

有序列表 列表项目使用数字进行标记

```html
<ol>
  <li>Coffee</li>
  <li>Milk</li>
</ol>
```

自定义列表 自定义列表以 <dl> 标签开始。每个自定义列表项以 <dt> 开始。每个自定义列表项的定义以 <dd> 开始。

```html
<dl>
  <dt>Coffee</dt>
  <dd>Black hot drink</dd>
  <dt>Milk</dt>
  <dd>White cold drink</dd>
</dl>
```

## src和href的区别

都是引用外部的文件

**src：** source。表示对资源的引用，它指向的内容会嵌入到当前标签所在的位置。src会将其指向的资源下载并应⽤到⽂档内，如请求js脚本。当浏览器解析到该元素时，会暂停其他资源的下载和处理，直到将该资源加载、编译、执⾏完毕，所以⼀般js脚本会放在页面底部。

```javascript
<img src="img/girl.jpg"> 
<iframe src="top.html"> 
<script src="show.js">
```

**href：** Hypertext Reference，表示超文本引用，它指向一些网络资源，建立和当前元素或本文档的链接关系。当浏览器识别到它他指向的⽂件时，就会并⾏下载资源，不会停⽌对当前⽂档的处理。 常用在a、link等标签上。

```javascript
<a href="http://www.baidu.com"></a> 
<link type="text/css" rel="stylesheet" href="common.css">
```

## DOCTYPE(⽂档类型) 的作⽤

告诉浏览器（解析器）应该以什么样（html或xhtml）的文档类型定义来解析文档，不同的渲染模式会影响浏览器对 CSS 代码甚⾄ JavaScript 脚本的解析。它必须声明在HTML⽂档的第⼀⾏。

## meta

`meta` 标签由 `name` 和 `content` 属性定义，**用来描述网页文档的属性**，比如网页的作者，网页描述，关键词等，除了HTTP标准固定了一些`name`作为大家使用的共识，开发者还可以自定义name。

常用的meta标签： 

（1）`charset`，用来描述HTML文档的编码类型：

```html
<meta charset="UTF-8" >
```

（2） `keywords`，页面关键词：

```html
<meta name="keywords" content="关键词" />
```

（3）`description`，页面描述：

```html
<meta name="description" content="页面描述内容" />
```

（4）`refresh`，页面重定向和刷新：

```html
<meta http-equiv="refresh" content="0;url=" />
```

（5）`viewport`，适配移动端，可以控制视口的大小和比例：

```html
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1">
```

其中，`content` 参数有以下几种：

- `width viewport` ：宽度(数值/device-width)
- `height viewport` ：高度(数值/device-height)
- `initial-scale` ：初始缩放比例
- `maximum-scale` ：最大缩放比例
- `minimum-scale` ：最小缩放比例
- `user-scalable` ：是否允许用户缩放(yes/no）

（6）搜索引擎索引方式：

```html
<meta name="robots" content="index,follow" />
```

其中，`content` 参数有以下几种：

- `all`：文件将被检索，且页面上的链接可以被查询；
- `none`：文件将不被检索，且页面上的链接不可以被查询；
- `index`：文件将被检索；
- `follow`：页面上的链接可以被查询；
- `noindex`：文件将不被检索；
- `nofollow`：页面上的链接不可以被查询。

## 行内和块元素

### 块级元素

- 每个块级元素都是独自占一行；
- 高度，行高，外边距（margin）以及内边距（padding）都可以控制；
- 元素的宽度如果不设置的话，默认为父元素的宽度（父元素宽度100%）；
- 多个块状元素标签写在一起，默认排列方式为从上至下；

```
 <address>  // 定义地址 
 <caption>  // 定义表格标题 
 <dd>      // 定义列表中定义条目 
 <div>     // 定义文档中的分区或节 
 <dl>    // 定义列表 
 <dt>     // 定义列表中的项目 
 <fieldset>  // 定义一个框架集 
 <form>  // 创建 HTML 表单 
 <h1>    // 定义最大的标题
 <h2>    // 定义副标题
 <h3>     // 定义标题
 <h4>     // 定义标题
 <h5>     // 定义标题
 <h6>     // 定义最小的标题
 <hr>     // 创建一条水平线
 <legend>    // 元素为 fieldset 元素定义标题
 <li>     // 标签定义列表项目
 <noframes>    // 为那些不支持框架的浏览器显示文本，于 frameset 元素内部
 <noscript>    // 定义在脚本未被执行时的替代内容
 <ol>     // 定义有序列表
 <ul>    // 定义无序列表
 <p>     // 标签定义段落
 <pre>     // 定义预格式化的文本
 <table>     // 标签定义 HTML 表格
 <tbody>     // 标签表格主体（正文）
 <td>    // 表格中的标准单元格
 <tfoot>     // 定义表格的页脚（脚注或表注）
 <th>    // 定义表头单元格
 <thead>    // 标签定义表格的表头
 <tr>     // 定义表格中的行
```

### 行内元素

- 不会独占一行，相邻的行内元素会排列在同一行里，直到一行排不下才会自动换行，其宽度随元素的内容而变化；

- 高宽无效，对外边距（margin）和内边距（padding）仅设置左右方向有效  上下无效；

- 设置行高有效，等同于给父级元素设置行高；

- 元素的宽度就是它包含的文字或图片的宽度，不可改变；

- 行内元素中不能放块级元素，a 链接里面不能再放链接；

```
 <iframe>
 <a>     // 标签可定义锚 
 <abbr>     // 表示一个缩写形式 
 <acronym>     // 定义只取首字母缩写 
 <b>     // 字体加粗 
 <bdo>     // 可覆盖默认的文本方向 
 <big>     // 大号字体加粗 
 <br>     // 换行 
 <cite>     // 引用进行定义 
 <code>    // 定义计算机代码文本
 <dfn>     // 定义一个定义项目
 <em>     // 定义为强调的内容
 <i>     // 斜体文本效果
 <kbd>     // 定义键盘文本
 <label>     // 标签为 input 元素定义标注（标记）
 <q>     // 定义短的引用
 <samp>     // 定义样本文本
 <select> // 创建单选或多选菜单
 <small>     // 呈现小号字体效果
 <span>     // 组合文档中的行内元素
 <strong> // 加粗
 <sub>     // 定义下标文本
 <sup>     // 定义上标文本
 <textarea>     // 多行的文本输入控件
 <tt>     // 打字机或者等宽的文本效果
 <var>    // 定义变量
```

### 行内块元素

- 高度、行高、外边距以及内边距都可以控制；
- 默认宽度就是它本身内容的宽度，不独占一行，但是之间会有空白缝隙，设置它上一级的 font-size 为 0，才会消除间隙；

```
<button> 
<input>   
<textarea> 
<select> 
<img>
```

## web worker

当在 HTML 页面中执行脚本时，页面的状态是不可响应的，直到脚本已完成。web worker 是运行在后台的 JavaScript，独立于其他脚本，不会影响页面的性能。可以继续做任何愿意做的事情，而此时 web worker 在后台运行。

```javascript
if(typeof(Worker) !== "undefined") {
  // 判断浏览器是否存在web worker
  if(typeof(w) == "undefined") {
    w = new Worker("demo_workers.js");
  }
  // 监听web worker返回信息
  w.onmessage = function(event) {
    document.getElementById("result").innerHTML = event.data;
  };
} else {
  document.getElementById("result").innerHTML = "抱歉，你的浏览器不支持 Web Workers...";
}
```

```javascript
var i=0;

function timedCount()
{
    i=i+1;
  	// web worker返回信息
    postMessage(i);
    setTimeout("timedCount()",500);
}

timedCount();
```

## title与h1的区别、b与strong的区别、i与em的区别

- strong标签有语义，是起到加重语气的效果，而b标签是没有的，b标签只是一个简单加粗标签。b标签之间的字符都设为粗体，strong标签加强字符的语气都是通过粗体来实现的，而搜索引擎更侧重strong标签。
- title属性没有明确意义只表示是个标题，H1则表示层次明确的标题，对页面信息的抓取有很大的影响
- **i内容展示为斜体，em表示强调的文本**

## iframe

 iframe的优点：

1. iframe能够原封不动地把嵌入的网页展现出来。
2. 如果有多个网页引用iframe，那么你只需要修改iframe的内容，就可以实现调用的每一个页面内容的更改，方便快捷。
3. 网页如果为了统一风格，头部和版本都是一样的，就可以写成一个页面，用iframe来嵌套，可以增加代码的可重用。
4. 如果遇到加载缓慢的第三方内容如图标和广告，这些问题可以由iframe来解决。

iframe的缺点：

1. 会产生很多页面，不容易管理。
2. iframe框架结构有时会让人感到迷惑，如果框架个数多的话，可能会出现上下、左右滚动条，会分散访问者的注意力，用户体验度差。
3. 代码复杂，无法被一些搜索引擎索引到，这一点很关键，现在的搜索引擎爬虫还不能很好的处理iframe中的内容，所以使用iframe会不利于搜索引擎优化。
4. 很多的移动设备（PDA 手机）无法完全显示框架，设备兼容性差。
5. iframe框架页面会增加服务器的http请求，对于大型网站是不可取的。会阻塞页面的加载

```html
<iframe src="http://www.runoob.com"></iframe>
```

| 属性   | 解释     |
| ------ | -------- |
| hegith | 高度     |
| width  | 宽度     |
| src    | 资源路径 |

## 实现动画的方式

- Javascript： setTimeout  setTimeInterval

- CSS3: transition  animation，
- HTML5 : canvas  requestAnimationFrame

> window.requestAnimationFrame() 告诉浏览器——你希望执行一个动画，并且要求浏览器在下次重绘之前调用指定的回调函数更新动画。该方法需要传入一个回调函数作为参数，该回调函数会在浏览器下一次重绘之前执行。当系统准备好了重绘条件的时候，才调用绘制动画帧

## table

| 标签    | 解释                           |
| ------- | ------------------------------ |
| table   | 顶级                           |
| caption | 表格说明，表格上方，非表格内容 |
| tr      | th td外层包裹标签              |
| th      | 表头数据，默认粗体             |
| td      | 表格数据                       |
| thead   | 表头最外层包裹                 |
| tbody   |                                |
| tfoot   |                                |

```
<th rowspan="2">电话</th> // 纵跨2行
<th colspan="2">电话</th>  // 横跨2列
```

## a

a标签没有设置href属性时，不能获取焦点

## range

定义滑块控件













