## css单位

1、px像素（Pixel）。相对长度单位。像素px是相对于显示器屏幕分辨率而言的

2、em是相对长度单位。相对于当前对象内文本的字体尺寸。如当前对行内文本的字体尺寸未被人为设置，则相对于浏览器的默认字体尺寸。任意浏览器的默认字体高都是16px。所有未经调整的浏览器都符合: 1em=16px。那么12px=0.75em,10px=0.625em

为了简化font-size的换算，需要在css中的body选择器中声明 Font-size=62.5%，这就使em值变为 16px*62.5%=10px, 这样12px=1.2em, 10px=1em, 也就是说只需要将你的原来的px数值除以10，然后换上em作为单位就行了。

em特点：em的值并不是固定的；em会继承父级元素的字体大小。

3、rem是CSS3新增的一个相对单位（root em，根em），区别在于使用rem为元素设定字体大小时，仍然是相对大小，但相对的只是HTML根元素。这个单位可谓集相对大小和绝对大小的优点于一身，通过它既可以做到只修改根元素就成比例地调整所有字体大小，又可以避免字体大小逐层复合的连锁反应。目前，除了IE8及更早版本外，所有浏览器均已支持rem。对于不支持它的浏览器，应对方法也很简单，就是多写一个绝对单位的声明。这些浏览器会忽略用rem设定的字体大小。下面就是

一个例子： p {font-size:14px; font-size:.875rem;}

em和rem的区别一句话概括：em相对于父元素，rem相对于html根元素。

4、vh vw 相对于视口的高度 和宽度。1vh 等于1/100的视口高度，1vw 等于1/100的视口宽度。

例如：浏览器高度900px，宽度为750px, 1 vh = 900px/100 = 9 px，1vw = 750px/100 = 7.5 px

100%和100vh/vw的区别：% 是相对于父元素的大小设定的比率。vw (viewport width) vh (viewport height) 是视窗的大小，100vw = 100%视窗宽度 100vh = 100%视窗高度，用vw，vh设定的大小只和视窗大小有关，和他们的父元素高度宽度没关系

5、vmin 和 vmax：相对视口高度和宽度两者的最小值或者最大值。

比如，浏览器的宽度设置为1200px，高度设置为800px， 1vmax = 1200/100px = 12px， 1vmin = 800/100px = 8px。如果宽度设置为600px,高度设置为1080px, 1vmin就等于6px, 1vmax则未10.8px。

## 取消表单轮廓和禁止文本域拖拽

```css
input, textarea {
	outline: none; // 取消表单轮廓
}
textarea {
    resize: none; // 防止拖拽文本域
}
```

## 不同形状

### 三角

将矩形分成以下几个等分，则1对应的三角能展示的条件为：上边框border-width>0，上、左边框border-style不为transparent

border-width指的是中间的半径长度

![image-20220325112430445](https://images-sally.oss-cn-beijing.aliyuncs.com/img/css画图.png)

```scss
.box {
	width: 0;
  height: 0;
  // 向右的等腰三角
  .triangle {
    border: 10px solid transparent;
    border-left-color: pink;
  }
  // 直角三角形
  .triangle1 {
    border-color: transparent red transparent transparent;
    border-style: solid;
    border-width: 22px 8px 0 0;
  }   
}
```

### 圆形

```css
div{
    width: 100px;
    heigt: 100px;
    border-radius: 50px;
    background-color: red;
}
```

### 扇形

```css
div{
    border: 100px solid transparent;
    width: 0;
    height: 0;
    border-radius: 100px;
    border-top-color: red;
}
```

### 0.5px的线

```css
.line {
  border-bottom: 1px solid black;
  transform: scaleY(0.5);
}
```

## 溢出的文字省略号展示

单行文本溢出显示省略号--必须满足3个条件

```css
// 1. 先强制一行内显示文本
white-space: nowrap; (默认normal自动换行)
//2. 超出部分隐藏
overflow: hidden;
//3. 文字用省略号替代超出的部分
text-overflow: ellipsis;
```

多行文本溢出显示省略号（兼容性较差，适用于webKit浏览器或移动端）

```css
overflow: hidden;
text-overflow: ellipsis;
// 弹性伸缩盒子模型展示
display: -webkit-box;
// 限制在一个块元素显示的文本的行数
-webkit-line-clamp: 2;
// 设置或检索伸缩盒对象的子元素的排列方式
-webkit-box-orient: vertical
```

注意：由于上面的三个属性都是 CSS3 的属性，没有浏览器可以兼容，所以要在前面加一个`-webkit-` 来兼容一部分浏览器。

## 居中方式

==盒子水平居中 + 垂直居中==

1、flex布局

```
.parent {
    width: 400px;
    height: 200px;
    background: #f99;
}
.child {
    width: 200px;
    height: 100px;
    background: green;
}
.center{
    display: flex;
    justify-content: center; // 实现水平居中
    align-items: center; // 实现垂直居中
}

<div class="parent center">
    <div class="child">居中</div>
</div>
```

2、margin：auto （flex items 、定位下的 margin: auto 不仅对水平方向有效，对垂直方向同样有效）

https://mp.weixin.qq.com/s/lg9OqjbVShgpmiI2Fl0rrw

```
.parent {
    width: 400px;
    height: 200px;
    background: red;
    display: flex;
}
.child {
    width: 200px;
    height: 100px;
    background: #9ff;
    margin: auto;
}

<div class="parent">
    <div class="child">居中</div>
</div>
```

```
.parent {
  width: 400px;
  height: 200px;
  position: relative;
  background: #ddd;
}
.child {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  margin: auto;
  width: 100px;
  height: 100px;
  background: #9ff;
}
```

3、平移 定位+transform

```
.parent {
    width: 400px;
    height: 200px;
    background: red;
    position: relative;
}
.child {
    width: 200px;
    height: 100px;
    background: #9ff;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate( -50%,-50%);
}

<div class="parent">
    <div class="child">居中</div>
</div>
```

3、table-cell布局

```
.parent {
    width: 400px;
    height: 200px;
    background: red;
    display: table-cell;
    vertical-align: middle;
}
.child {
    width: 200px;
    height: 100px;
    background: #9ff;
    margin: 0 auto;
}

<div class="parent">
    <div class="child">居中</div>
</div>
```

```
.parent {
    width: 400px;
    height: 200px;
    background: red;
    display: table-cell;
    vertical-align: middle;
    text-aligh: center;
}
.child {
    width: 200px;
    height: 100px;
    background: #9ff;
    display: inline-block;
}

<div class="parent">
    <div class="child">居中</div>
</div>
```

5、grid布局

```
.parent {
    width: 400px;
    height: 200px;
    background: red;
    display: grid; // 默认一个网格
    place-content: center;
}
.child {
    width: 200px;
    height: 100px;
    background: #9ff;
}

<div class="parent">
    <div class="child">居中</div>
</div>
```

==行内元素居中==

```
line-height: 盒子高度; // 垂直居中，仅针对单行文本
text-aligh: center; // 水平居中
```

总结：

水平居中

- 行内元素: `text-align: center`
- 块级元素: `margin: 0 auto`
- `absolute + transform`
- `flex + justify-content: center`

垂直居中

- `line-height: height`
- `absolute + transform`
- `flex + align-items: center`
- `table`

水平垂直居中

- `absolute + transform`
- `margin auto + flex`
- `margin auto + position`
- `margin auto + table-cell`
- `flex + justify-content + align-items`
- `grid`

## 布局方式

### 2列布局

1-3 是一列定宽，另一列自适应，后面几种由子级撑起。

1、float+calc()函数完成左列定宽右列自适应

```css
.left {
  /* 左边列开启浮动 */
  float: left;
}
.right {
  /* 右边列开启浮动 */
  float: left;
  /* 宽度减去左列的宽度 */
  width: calc(100% - 200px);
}

```

2、float+margin-left完成左列定宽右列自适应

```css
.left {
  /* 左边列开启浮动 */
  float: left;
}
.right {
  /* 通过外边距的方式使该容器的左边有200px */
  margin-left: 200px;
}
```

3、absolute+margin-left完成左列定宽右列自适应

```css
.left {
  /* 开启定位脱离文档流 */
  position: absolute;
}
.right {
  /* 通过外边距的方式使该容器的左边有200px */
  margin-left: 200px;
}
```

4、float+overflow完成左列定宽右列自适应

```css
.left {
  /* 1. 左侧元素开始浮动 */
  float: left;
}
.right {
  /* 2. 右侧自适应元素设置 overflow 会创建一个BFC 完成自适应 */
  overflow: hidden;
}
```

5、flex

```css
.container {
  display: flex;
}
.right {
  flex: 1;
  /* flex: 1; 表示 flex-grow: 1; 即该项占所有剩余空间 */
}
```

6、grid布局

```css
.container {
  display: grid;
  /* 将其划分为两行，其中一列有本身宽度决定， 一列占剩余宽度*/
  grid-template-columns: auto 1fr;
}
```

**3列布局**

三列布局主要分为两种：

- 第一种是前两列定宽，最后一列自适应，这一种本质上与两列布局没有什么区别，可以参照两列布局实现。
- 第二种是前后两列定宽，中间自适应

1、float


float布局需要清除浮动，因为float会脱离文档流，会造成高度塌陷的问题

```css
.left {
  /* 1. 左列容器开启左浮动 */
  float: left;
}
.content {
  /* 自适应元素设置 overflow 会创建一个BFC 完成自适应 */
  overflow: hidden;
  /*或者：*/
  width: calc(100%-400px);
}
.right {
  /* 2. 右列容器开启右浮动 */
  float: right;
}
```

2、position

绝对定位是脱离文档流的，意味着下面的所有子元素也会脱离文档流，这就导致了这种方法的有效性和可使用性是比较差的。

```css
.left {
  /* 1. 左右两列脱离文档流，并通过偏移的方式到达自己的区域 */
  position: absolute;
  left: 0;
  top: 0;
}
.content {
  /* 2. 使中间自适应的宽度为父级容器减去两个定宽的列 */
  width: calc(100%-400px);
  /* 3. 通过外边距将容器往内缩小 */
  margin-right: 200px;
  margin-left: 200px;
}
.right {
  position: absolute;
  right: 0;
  top: 0;
}
```

3、flex布局

不能兼容IE8及以下浏览器。

```css
.container {
  display: flex;
}
.right {
  flex: 1;
  /* flex: 1; 表示 flex-grow: 1; 即该项占所有剩余空间 */
}
```

4、grid

目前兼容性还达不到广泛推广的地步，在chrome Firefox等浏览器还存在bug没有修复

```css
.container {
  display: grid;
  /* 将其划分为两行，其中一列有本身宽度决定， 一列占剩余宽度*/
  grid-template-columns: auto 1fr auto;
}
```

5、table

表格布局的缺点还是很明显的，相当于其他布局，使用相对繁琐，代码量大，同时也存在缺陷， 当单元格的一个格子超出高度之后，两侧就会一起触发跟着一起变高，这显然不是我们想要看到的情况。

```css
/* table布局 */
.layout .table {
    display: table;
    width: 100%;
    height: 100px;
}
  
.layout .table div {
    display: table-cell;
}
```

**等分布局**

1、浮动+百分比方式

```css
.item {
  /* 开启浮动，每个元素占 25% 的宽度 */
  width: 25%;
  float: left;
}
```

2、行内块级+百分比方式

```css
.item {
  /* 设置每个元素为行内块级元素，每个元素占 24.5% 的宽度 */
  width: 24.5%;
  /* 因为行内块级元素有一些类似于边距的几个像素，导致各占25会超出容器 */
  display: inline-block;
}
```

3、flex

```css
.container {
  /* 开启 flex 布局 */
  display: flex;
}
.item {
  /* 每个元素占相同的宽度 */
  flex: 1;
}
```

4、grid

```css
.container {
  /* 开启 grid 布局 */
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  /* 使用 repeat 函数生成如下代码 */
  /* grid-template-columns: 1fr 1fr 1fr 1fr; */
}
```

## BFC

https://zhuanlan.zhihu.com/p/25321647

Block FormattingContexts（块级格式化上下文）。**具有 BFC 特性的元素可以看作是隔离了的独立容器，容器里面的元素不会在布局上影响到外面的元素，并且 BFC 具有普通容器所没有的一些特性。**BFC属于普通流。

> 常见定位方案：普通流（normal flow） 浮动（float）绝对定位（absolute）

**触发BFC**：只要元素满足下面任一条件即可触发 BFC 特性：

- body 根元素
- 浮动元素：float 除 none 以外的值
- 绝对定位元素：position (absolute、fixed)
- display 为 inline-block、table-cell、flex
- overflow 除了 visible 以外的值 (hidden、auto、scroll)

1、同一个BFC下外边距会发生折叠。如果想要避免外边距的重叠，可以将其放在不同的 BFC 容器中。

以下2个盒子中间只有100px，而不是200

```html
div{
    width: 100px;
    height: 100px;
    background: lightblue;
    margin: 100px;
}
<body>
    <div></div>
    <div></div>
</body>
```

```html
.container {
    overflow: hidden;
}
p {
    width: 100px;
    height: 100px;
    background: lightblue;
    margin: 100px;
}

<div class="container">
    <p></p>
</div>
<div class="container">
    <p></p>
</div>
```

2、BFC可以包含浮动的元素（清除浮动）

浮动的元素会脱离标准流，以下例子中，若外层不是BFC，则会由于容器内元素浮动，脱离了文档流，所以容器只剩下 2px 的边距高度。如果使触发容器的 BFC，那么容器将会包裹着浮动元素。

```html
<div style="border: 1px solid #000;overflow: hidden">
    <div style="width: 100px;height: 100px;background: #eee;float: left;"></div>
</div>
```

3、BFC可以阻止元素被浮动元素覆盖

第2个元素若不加`overflow: hidden`，则第二个盒子会有部分被浮动元素覆盖（浮动元素不会覆盖文本信息）。

```html
<div style="height: 100px;width: 100px;float: left;background: lightblue">我是一个左浮动的元素</div>
<div style="width: 200px; height: 200px;background: #eee;overflow: hidden">我是一个没有设置浮动, 
也没有触发 BFC 元素, width: 200px; height:200px; background: #eee;</div>
```

## 层叠上下文

https://juejin.cn/post/6844903667175260174

如果一个元素含有层叠上下文，(也就是说它是层叠上下文元素)，可以理解为这个元素在`Z轴`上就“高人一等”，最终表现就是它离屏幕观察者更近。

**产生层叠上下文**：

- 根层叠上下文(`html`)

- 定义了`position`属性，且属性值为非`static`值的元素

- css3属性

  - 父元素的display属性值为`flex|inline-flex`，子元素`z-index`属性值不为`auto`的时候，子元素为层叠上下文元素；

  - 元素的`opacity`属性值不是`1`；

  - 元素的`transform`属性值不是`none`；

  - 元素`mix-blend-mode`属性值不是`normal`；

  - 元素的`filter`属性值不是`none`；

  - 元素的`isolation`属性值是`isolate`；

  - `will-change`指定的属性值为上面任意一个；

  - 元素的`-webkit-overflow-scrolling`属性值设置为`touch`。

**判断层叠上下文顺序**：

1. 比较元素在同一层叠上下文，则直接比较他们的层叠等级
2. 比较元素不在同一层叠上下文，比较所处的层叠上下文的层叠等级
3. 当两个元素层叠等级相同、层叠顺序相同时，在DOM结构中后面的元素层叠等级在前面元素之上。

![层叠顺序](https://images-sally.oss-cn-beijing.aliyuncs.com/images/2022-01-05-%E5%B1%82%E5%8F%A0%E9%A1%BA%E5%BA%8F-4a78cb875be54af25de5921562275b9f-037.png)

## 有继承性和无继承性的属性

一、无继承性的属性

1. **display**：规定元素应该生成的框的类型
2. **文本属性**：

- vertical-align：垂直文本对齐
- text-decoration：规定添加到文本的装饰
- text-shadow：文本阴影效果
- white-space：空白符的处理
- unicode-bidi：设置文本的方向

1. **盒子模型的属性**：width、height、margin、border、padding
2. **背景属性**：background、background-color、background-image、background-repeat、background-position、background-attachment
3. **定位属性**：float、clear、position、top、right、bottom、left、min-width、min-height、max-width、max-height、overflow、clip、z-index
4. **生成内容属性**：content、counter-reset、counter-increment
5. **轮廓样式属性**：outline-style、outline-width、outline-color、outline
6. **页面样式属性**：size、page-break-before、page-break-after
7. **声音样式属性**：pause-before、pause-after、pause、cue-before、cue-after、cue、play-during

二、有继承性的属性

1. **字体系列属性**

- font-family：字体系列
- font-weight：字体的粗细
- font-size：字体的大小
- font-style：字体的风格

1. **文本系列属性**

- text-indent：文本缩进
- text-align：文本水平对齐
- line-height：行高
- word-spacing：单词之间的间距
- letter-spacing：中文或者字母之间的间距
- text-transform：控制文本大小写（就是uppercase、lowercase、capitalize这三个）
- color：文本颜色

1. **元素可见性**

- visibility：控制元素显示隐藏

1. **列表布局属性**

- list-style：列表风格，包括list-style-type、list-style-image等

1. **光标属性**

- cursor：光标显示为何种形态

## 隐藏元素的方法

display: none：渲染树不会包含该渲染对象，因此该元素不会在页面中占据位置，也不会响应绑定的监听事件。
visibility: hidden：元素在页面中仍占据空间，但是不会响应绑定的监听事件。
opacity: 0：将元素的透明度设置为 0，以此来实现元素的隐藏。元素在页面中仍然占据空间，并且能够响应元素绑定的监听事件。
position: absolute：通过使用绝对定位将元素移除可视区域内，以此来实现元素的隐藏。
z-index: 负值：来使其他元素遮盖住该元素，以此来实现隐藏。
clip/clip-path ：使用元素裁剪的方法来实现元素的隐藏，这种方法下，元素仍在页面中占据位置，但是不会响应绑定的监听事件。
transform: scale(0,0)：将元素缩放为 0，来实现元素的隐藏。这种方法下，元素仍在页面中占据位置(原大小)，但是不会响应绑定的监听事件。

## line-height和height的区别

line-heigth是行高，决定了元素中文本内容的高度，height则是定义元素自身的高度。

例如：当p标签折行时，若存在`height: 20px`，则背景色始终只有20px；若只存在`line-height:20px`，则代表每一行文本是20px，每一行都有背景色。 

line-height 的赋值方式，如果直接应用在自身，3种方式无区别，应用在子盒子身上，有如下区别：

- 带单位：px 是固定值，而 em 会参考父元素 font-size 值计算自身的行高
- 纯数字：会把比例传递给后代。例如，父级行高为 1.5，子元素字体为 18px，则子元素行高为 1.5 * 18 = 27px
- 百分比：将计算后的值传递给后代

## CSS 优化和提高性能的方法

**加载性能：**

（1）css压缩：将写好的css进行打包压缩，可以减小文件体积。

（2）css单一样式：当需要下边距和左边距的时候，很多时候会选择使用 margin:top 0 bottom 0；但margin-bottom:bottom;margin-left:left;执行效率会更高。

（3）减少使用@import，建议使用link，因为后者在页面加载时一起加载，前者是等待页面加载完成之后再进行加载。

**选择器性能：**

（1）关键选择器（key selector）。选择器的最后面的部分为关键选择器（即用来匹配目标元素的部分）。CSS选择符是从右到左进行匹配的。当使用后代选择器的时候，浏览器会遍历所有子元素来确定是否是指定的元素等等；

（2）如果规则拥有ID选择器作为其关键选择器，则不要为规则增加标签。过滤掉无关的规则（这样样式系统就不会浪费时间去匹配它们了）。

（3）避免使用通配规则，如*{}计算次数惊人，只对需要用到的元素进行选择。

（4）尽量少的去对标签进行选择，而是用class。

（5）尽量少的去使用后代选择器，降低选择器的权重值。后代选择器的开销是最高的，尽量将选择器的深度降到最低，最高不要超过三层，更多的使用类来关联每一个标签元素。

（6）了解哪些属性是可以通过继承而来的，然后避免对这些属性重复指定规则。

**渲染性能：**

（1）慎重使用高性能属性：浮动、定位。

（2）尽量减少页面重排、重绘。

（3）去除空规则：｛｝。空规则的产生原因一般来说是为了预留样式。去除这些空规则无疑能减少css文档体积。

（4）属性值为0时，不加单位。

（5）属性值为浮动小数0.**，可以省略小数点之前的0。

（6）标准化各种浏览器前缀：带浏览器前缀的在前。标准属性在后。

（7）不使用@import前缀，它会影响css的加载速度。

（8）选择器优化嵌套，尽量避免层级过深。

（9）正确使用display的属性，由于display的作用，某些样式组合会无效，徒增样式体积的同时也影响解析性能。

**可维护性、健壮性：**

（1）将具有相同属性的样式抽离出来，整合并通过class在页面中进行使用，提高css的可维护性。

（2）样式与内容分离：将css代码定义到外部css中。

