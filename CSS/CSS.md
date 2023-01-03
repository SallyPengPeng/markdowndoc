## display

block :块对象的默认值。会独占一行，多个元素会另起一行，可以设置width、height、margin和padding属性

none :隐藏对象。与visibility属性的hidden值不同，其不为被隐藏的对象保留其物理空间

inline :内联对象的默认值。设置width、height属性无效。可设置水平方向的margin和padding属性，不能设置垂直方向的padding和margin

inline-block: 转换为行内块元素，只会影响宽高

inline-table :将表格显示为无前后换行的内联对象或内联容器

table :将对象作为块元素级的表格显示

table-caption :将对象作为表格标题显示

table-cell :将对象作为表格单元格显示

table-column :将对象作为表格列显示

table-column-group :将对象作为表格列组显示

table-header-group :将对象作为表格标题组显示

table-footer-group :将对象作为表格脚注组显示

table-row :将对象作为表格行显示

table-row-group :将对象作为表格行组显示

### display:table

table标签（display:table）

1) table可设置宽高、margin、border、padding等属性。属性值的单位可以使用px，百分比值。
2) table的宽度默认由内容的宽高撑开，如果table设置了宽度，宽度默认被它里面的td平均分，如果给某一个td设置宽度，那么table剩余的宽度会被其他的td平均分（有点类似flex布局）
3) 给table设置的高度起到的作用只是min-height的作用，当内容的高度高于设置的高度时，table的高度会被撑高。

tr标签（display:table-row）

1) 给tr设置高度只起到min-height的作用，默认会平分table的高度。
2) tr中的td默认高度会继承tr的高度，若给任一td设置了高度，其他td的高度也同样变高。适合多列等高布局
3) 设置宽度、margin、都不起作用

td标签（display:table-cell）

1) td默认继承tr的高度，且平分table的宽度
2) 若table（display:table）不存在，给td设置的宽高不能用百分比只能用准确的数值
3) 给td设置vertical-align: middle; td元素里面(除float、position:absolute)所有的块级、非块级元素都会相对于td垂直居中
4) 给td设置text-align: center; td元素里面所有非block元素(除float、position:absolute)都会相对于td水平居中，虽然block元素不居中，但其中的文字或inline元素会水平居中

```html
<table>
  <tr>
    <th>First name</th>
    <th>Last name</th>
  </tr>
  <tr>
    <td>John</td>
    <td>Doe</td>
  </tr>
  <tr>
    <td>Jane</td>
    <td>Doe</td>
  </tr>
</table>
```

### display: flex

http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html

设为 Flex 布局以后，子元素的float、clear和vertical-align属性将失效。

![flex布局1](https://images-sally.oss-cn-beijing.aliyuncs.com/images/2022-01-05-flex%E5%B8%83%E5%B1%801-f12516755462f747ec96b621ae9e1928-bb2.png)

![flex布局2](https://images-sally.oss-cn-beijing.aliyuncs.com/images/2022-01-05-flex%E5%B8%83%E5%B1%802-08a2327a2e4b2f384606b4d4a9e1c66e-30f.png)

![flex布局3](https://images-sally.oss-cn-beijing.aliyuncs.com/images/2022-01-05-flex%E5%B8%83%E5%B1%803-35f43367fe7ee1716b8e3e6a52e6e50c-f6e.png)

align-items 和 align-content的区别：https://juejin.cn/post/6844903911690600456#heading-4

|                                              | 默认               | align-items: center | align-content: center |
| -------------------------------------------- | ------------------ | ------------------- | --------------------- |
| 父盒子无高度，单行数据，高度为行数据最大高度 | 顶部对其           | 居中                | 顶部对其              |
| 父盒子设置高度，单行数据，高度为设置高度     | 顶部对其           | 居中                | 顶部对其              |
| 父盒子无高度，多行数据，高度为行数据最大高度 | 平分空间，顶部对其 | 平分空间，每行居中  | 平分空间，顶部对其    |
| 父盒子设置高度，多行数据，高度为设置高度     | 平分空间，顶部对其 | 平分空间，每行居中  | 整体居中              |

总结：多行数据时，align-content使多行数据是一个整体，align-items针对每一行而言

```
flex: 1; === flex: 1 1 auto;
flex: initial ; === flex: 0 1 auto;
flex: none; === flex: 0 0 auto;
// flex-basis属性定义了在分配多余空间之前，项目占据的主轴空间(main size)。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小。
```

### display: grid

http://www.ruanyifeng.com/blog/2019/03/grid-layout-tutorial.html

设为网格布局以后，容器子元素（项目）的float、display: inline-block、display: table-cell、vertical-align和column-*等设置都将失效。

Flex 布局是轴线布局，只能指定"项目"针对轴线的位置，可以看作是一维布局。Grid 布局则是将容器划分成"行"和"列"，产生单元格，然后指定"项目所在"的单元格，可以看作是二维布局。

行列：n行和m列会产生n x m个单元格，行和列的交叉区域，称为"单元格"（cell）

网格线：划分网格的线，称为"网格线"（grid line）。n行有n + 1根水平网格线，m列有m + 1根垂直网格线

![grid布局](https://images-sally.oss-cn-beijing.aliyuncs.com/images/2022-01-05-grid%E5%B8%83%E5%B1%80-25d956a13ea841df3c9b323f8041eb40-96d.png)

1、容器属性

1.1、指定网格布局后，要划分行和列。grid-template-columns属性定义每一列的列宽，grid-template-rows属性定义每一行的行高

```
.container {
  display: grid; // display: inline-grid;
  grid-template-columns: repeat(3, 33.33%); // 定义3列，每列宽度是33.33%
  grid-template-rows: repeat(2, 100px 20px 80px); // 定义6行，1 4行是100，2 5 时20，3 6是80
}
```

==关键字 或 函数==

fr: fraction 片段，比例关系，如果两列分别为1fr和2fr，表示后者是前者的两倍

```
grid-template-columns: 150px 1fr 2fr;
```

minmax()：产生一个长度范围，表示长度就在这个范围之中。它接受两个参数，分别为最小值和最大值

```
// 表示列宽不小于100px，不大于1fr
grid-template-columns: 1fr 1fr minmax(100px, 1fr);
```

auto：表示由浏览器自己决定长度

```
grid-template-columns: 100px auto 100px;
```

网格线名称：属性里面，可以使用方括号，指定每一根网格线的名字，方便以后的引用。

```
grid-template-columns: [c1] 100px [c2] 100px [c3] auto [c4];
grid-template-rows: [r1] 100px [r2] 100px [r3] auto [r4];
// 网格布局允许同一根线有多个名字，比如[fifth-line row-5]
```

==repeat参数==

auto-fill：单元格大小固定，容器大小不确定，希望每一行(列)容纳尽可能多的单元格，使用auto-fill自动填充

```
grid-template-columns: repeat(auto-fill, 100px);
```

==例子==

```
// 两栏布局
.wrapper {
  display: grid;
  grid-template-columns: 70% 30%;
}
```

## visibility

```
visibility: hidden || visible;
```

| 属性    | 解释                       |
| ------- | -------------------------- |
| hidden  | 隐藏元素，仍保留原来的位置 |
| visible | 显示元素                   |
| inherit | 继承上一个父对象的可见性   |

display:none与visibility:hidden的区别：

（1）在渲染树中

display:none会让元素完全从渲染树中消失，渲染时不会占据任何空间；
visibility:hidden不会让元素从渲染树中消失，渲染的元素还会占据相应的空间，只是内容不可见。

（2）是否是继承属性

display:none是非继承属性，子孙节点会随着父节点从渲染树消失，通过修改子孙节点的属性也无法显示；
visibility:hidden是继承属性，子孙节点消失是由于继承了hidden，通过设置visibility:visible可以让子孙节点显示；

（3）修改常规文档流中元素的 display 通常会造成文档的重排，但是修改visibility属性只会造成本元素的重绘；
（4）如果使用读屏器，设置为display:none的内容不会被读取，设置为visibility:hidden的内容会被读取。

## user-select

规定是否能选取元素的文本。在 web 浏览器中，如果在文本上双击，文本会被选取或高亮显示。此属性用于阻止这种行为。

```
user-select: auto|none|text|all;
```

| 值   | 描述                                 |
| ---- | ------------------------------------ |
| auto | 默认，如果浏览器允许，则可以选择文本 |
| none | 防止文本选取                         |
| text | 文本可被用户选取                     |
| all  | 单击选取文本，而不是双击             |

## overflow

```
overflow: visible || hidden || scroll || auto || inherit;
```

| 值      | 描述                                                         |
| ------- | ------------------------------------------------------------ |
| visible | 默认值，内容不会被修改，会呈现在元素框之外                   |
| hidden  | 内容会被修剪，并且其余内容是不可见的                         |
| scroll  | 内容会被修剪辑，但是浏览器会显示滚动条以便查看其余的内容，一直显示 |
| auto    | 如果内容被修改，则浏览器会显示滚动条以便查看其余的内容，溢出时显示 |
| inherit | 规定应该从父元素继承overflow属性的值                         |

## vertical-aligh

使用场景：经常用于设置图片 或者表单（行内块元素）和文字垂直对齐。==只针对于行内元素或者行内块元素==有效

![vertical-aligh](https://images-sally.oss-cn-beijing.aliyuncs.com/images/2022-01-05-vertical-aligh-117ba3c8b037badf1fea827dfabb44ed-999.png)

```
vertical-aligh: baseline | top | middle | bottom | text-top | text-bottom;
```

| 值          | 描述                                   |
| ----------- | -------------------------------------- |
| baseline    | 默认。元素放置在父元素的基线上         |
| top         | 把元素的顶端与行中最高元素的顶端对齐   |
| middle      | 把此元素放置在父元素的中部             |
| bottom      | 把元素的顶端与行中最低的元素的顶端对齐 |
| text-top    | 使元素的顶部与父元素的字体顶部对其     |
| text-bottom | 使元素的底部与父元素的字体底部对其     |

1. 行内块元素默认和文字的基线对齐，vertical-aligh常用于图片和文字居中对齐

```
img { vertical-alith: bottom}
<img href="">文字文字
```

2. 解决图片底部默认空白缝隙问题
   bug：图片底侧会有一个空白缝隙，原因是行内块元素会和文字的基线对齐
   主要解决办法有两种：
   - 给图片添加vertical-aligh: middle | top | bottom等
   - 把图片转为块元素 display：block

## transform

transform 属性向元素应用 2D 或 3D 转换。该属性允许对元素进行旋转、缩放、移动或倾斜。

| 值                       | 描述                                                         |
| ------------------------ | ------------------------------------------------------------ |
| translate(*x*,*y*)       | 定义 2D 转换，盒子大小不变，像X Y轴移动指定距离。原来的空间仍然占据 |
| translate3d(*x*,*y*,*z*) | 定义 3D 转换                                                 |
| scale(*x*,*y*)           | 定义 2D 缩放转换，盒子高度 和宽度 分别缩放指定倍数           |
| rotate(*angle*)          | 定义 2D 旋转，在参数中规定角度，盒子旋转制定角度             |
| ……                       |                                                              |

```css
transform: translate(10px, 10px);
transform:rotate(7deg);
```

有时候⽤translate来改变位置⽽不是定位改变位置：

1. 改变transform或opacity使浏览器为元素创建⼀个 GPU 图层，不会触发浏览器重新布局（reflow）或重绘（repaint），只会触发复合（compositions）。⽽改变绝对定位会会使⽤到 CPU，会触发重新布局，进⽽触发重绘和复合。
2. translate改变位置时，元素依然会占据其原始空间，绝对定位不会

## opacity

设置元素的不透明级别

```css
opacity: value; // 规定不透明度。从 0.0 （完全透明）到 1.0（完全不透明）
```

## position

定位：将盒子定在某一个位置。定位=定位模式+边偏移

### 定位模式

```
position: static || relative || absolute || fixed;
```

| 值       | 语义                                                         |
| -------- | ------------------------------------------------------------ |
| static   | 静态定位。默认定位方式，表示无定位，按照标准流特性拜访，无边偏移 |
| relative | 相对定位。1. 元素移动时，相对于它原来的位置来说的；2. 原来在标准流的位置继续占有，后面的盒子仍然以标准流的方式对待；3. 最典型的应用是给绝对定位当父级；4. 相对定位会压住标准流的盒子 |
| absolute | 绝对定位。1. 如果没有祖先元素或者祖先元素没有定位，则以浏览器为准定位；2. 如果祖先元素有定位（相对、绝对、固定定位），则以最近一级的有定位祖先元素为参考点移动；3. 绝对定位不占有原来的位置 |
| fixed    | 固定定位。1. 以浏览器的可视窗口为参照点移动元素，跟父元素没有任何关系，不随滚动条滚动 ；2. 固定定位不占有原来的位置 |
| sticky   | 黏性定位。1. 以浏览器的可视窗口为参照点移动元素；2. 黏性定位占有原先的位置；3. 必须添加位偏移中的其中1个才生效；4. 跟页面滚动搭配使用，兼容性较差，IE不支持。 |

### 边偏移

边偏移就是定位的盒子移动到最终位置，有top bottom left right 4个属性

| 边偏移属性 | 示例         | 描述                                           |
| ---------- | ------------ | ---------------------------------------------- |
| top        | top: 80px    | 顶端偏移量，定义元素相对于其父元素上边线的距离 |
| bottom     | bottom: 80px | 底端偏移量，定义元素相对于其父元素下边线的距离 |
| left       | left: 80px   | 左端偏移量，定义元素相对于其父元素左边线的距离 |
| right      | right: 80px  | 右端偏移量，定义元素相对于其父元素右边线的距离 |

### 定位叠放次序

```
z-index: 1;
```

- 数值可以是正整数、负整数或0，默认是auto，数值越大，盒子越靠上
- 如果属性值相同，则按照书写顺序，后来居上
- 数字后面不能加单位
- 只有定位的盒子才有z-index属性

### 应用

1. 绝对（固定）定位的盒子，脱离标准流，若想水平居中， 用margin: auto会无效

```css
.center {
    position: absolute；
    left: 50%;
    width: 100px;
    height: 200px;
    margin-left:-50px;
}
```
2. 使行内元素成为行内块元素
```
// 行内元素能够添加高度和宽度的方法
display: block || inline-block; // 行内元素转换为块级或者行内块
float: left || right; // 添加浮动
display: absolute || fixed; // 添加绝对或者固定定位
```
3. 绝对（相对）定位会完全压住下面标准流的所有内容
浮动的盒子只会完全压住下面标准流的所有盒子，不会压住下面标准流的文字（图片）

## 文本属性

### 缩进文本 text-indent

通常用于文本首行缩进text-indent: 2em

```
p {text-indent: 5em;}
p {text-indent: -5em; padding-left: 5em;}
```

### 水平对其text-align

```
text-aligh: left | right | bottom;
text-align-last: justify; // 文本两端对其，中间间隔相等
```

单行文字垂直居中的方法，使文字的行高等于盒子的高度

```
.div{
    height: 40px;
    line-height: 40px;
}
```

### 字间隔 word-spacing

```
p.spread {word-spacing: 30px;}
<p class="spread">
This is a paragraph. The spaces between words will be increased.
</p>
```

### 字母间隔letter-spacing

h1 {letter-spacing: -0.5em}

h4 {letter-spacing: 20px}

### 字符装换 text-tranform

```
text-transform: uppercase ||lowercase || capitalize;
```

### 文本装饰text-decoration

```
text-decoration: overline ||line-through || underline || blink || none
```

| 属性         | 值                          |
| ------------ | --------------------------- |
| overline     | 文本顶部划线                |
| line-through | 文本中间划线                |
| underline    | 文本下沿划线                |
| blink        | 定义闪烁的文本              |
| none         | 无，一般用于给<a>去掉下换线 |

## 文字属性

### 字体类型font-family

```
h1 {font-family: Georgia;}
```

### 字体风格font-style

normal - 文本正常显示

italic - 文本斜体显示

oblique - 文本倾斜显示

### 字体变形font-variant

```
p {font-variant:small-caps;}
```

### 字体加粗font-weight

| 值                     | 描述                                          |
| ---------------------- | --------------------------------------------- |
| normal                 | 默认值，定义标准的字符                        |
| bold                   | 定义粗体字符                                  |
| bolder                 | 定义更粗的字符                                |
| lighter                | 定义更细的字符                                |
| 100 200 300 400 …… 900 | 定义从细到粗的字符，400 = normal ，700 = bold |

```
p.normal {font-weight:normal;}
p.thick {font-weight:bold;}
p.thicker {font-weight:900;}
```

### 字体大小font-size

正文文本大小默认16px

```
h1 {font-size:60px;}   
```

### 字体颜色color

```
h1{color:red;}
```

### 合并属性

必须按照font-style font-weight font-size/line-height font-family的顺序，不能颠倒.

不需要设置的属性可以省略，取默认值，但必须保留font-size和font-family，否则font属性将不生效。

```
p.ex2

{
font:italic bold 12px/30px arial,sans-serif;
{
font:italic bold 12px/1.5 arial,sans-serif;   // 1.5指当前文字行高是当前文字行高度的1.5倍
}
```

## css选择器

选择器分为基础选择器和复合选择器两个大类：基础选择器是由单个选择器组成的。基础选择器包括：标签选择器、类选择器、id选择器和通配符选择器

### 元素选择器/类型选择器（type selector）

```
p {color:gray;}

h2 {color:silver;}
```

### 分组

选择器分组：h1, h2, h3, h4, h5, h6 {color:blue;} // 并集选择器，一般竖着写，例如：

```
.demo,
h1,
p {
…
}
```

声明分组：

```
h1 {
 font: 28px Verdana;
 color: blue;
 background: red;
 }
```

### 通配符选择器

下面的规则可以使文档中的每个元素都为红色：

```
* {color:red;}
h1, h2, h3, h4, h5, h6 {
 color:gray;
 background: white;
 padding: 10px;
 border: 1px solid black;
 font-family: Verdana;
 }
```

### 类选择器，用 .

.important {color:red;}

结合元素选择器：p.important {color:red;}

多类选择器：假设 class 为 important 的所有元素都是粗体，而 class 为 warning 的所有元素为斜体，class 中同时包含 important 和 warning 的所有元素还有一个银色的背景 。就可以写作：

```
.important {font-weight:bold;}
.warning {font-style:italic;}
.important.warning {background:silver;}
```

### ID选择器

\#intro {font-weight:bold;}

ID选择器与类选择器的不同：

（1）ID选择器只能在一个HTML文档中使用一次

（2）ID选择器不能结合使用，因为ID属性不允许有以空格分割的词列表

（3）类选择器和 ID 选择器可能是区分大小写的。这取决于文档的语言。HTML 和 XHTML 将类和 ID 值定义为区分大小写，所以类和 ID 值的大小写必须与文档中的相应值匹配

### 属性选择器(CSS3)

```
img[alt] {border: 5px solid red;}
```

注意以上是标签选择器 + 属性选择器

也可以根据具体属性值选择

```
a[href="http://www.w3school.com.cn/about_us.asp"] {color: red;}

a[href="http://www.w3school.com.cn/"][title="W3School"] {color: red;}
```

根据属性值中的词列表的某个词进行选择，用~

```
p[class~="important"] {color: red;}
```

| 选择器         | 简介                                                         |
| -------------- | ------------------------------------------------------------ |
| E[att]         | 选择具有att属性的E元素                                       |
| E[att="val"]   | 选择具有att属性且属性值等于val的E元素                        |
| E[att^="val"]  | 匹配具有att属性且值以val开头的E元素                          |
| E[att$="val"]  | 匹配具有att属性且值以val结尾的E元素                          |
| E[att*="val"]  | 匹配具有att属性且值中含有val的E元素，做字符串拆分，只要能拆出来 val 这个词 |
| E[att～="val"] | 属性中包含独立的单词为 val                                   |
| E[att\|="val"] | 以val开头的E元素，属性中必须是完整且唯一的单词，或者以 **-** 分隔开 |

### 后代选择器

h1 em {color:red;}  只对 h1 元素中的 em 元素应用样式：

- 元素1和元素2中间用空格隔开
- 元素1是父级，元素2是子级，最终选择的是元素2
- 元素2可以是儿子，也可以是孙子等，只要是元素1的后代即可
- 元素1和元素2可以使任意基础选择器


有一个文档，其中有一个边栏，还有一个主区。边栏的背景为蓝色，主区的背景为白色，这两个区都包含链接列表。不能把所有链接都设置为蓝色，因为这样一来边栏中的蓝色链接都无法看到

```
div.sidebar {background:blue;}
div.maincontent {background:white;}
div.sidebar a:link {color:white;}
div.maincontent a:link {color:blue;}
```

### 子元素选择器

与后代选择器相比，子元素选择器（Child selectors）只能选择作为某元素子元素的元素。

```
h1 > strong {color:red;}
```

### 相邻兄弟选择器

这个选择器读作：“选择紧接在 h1 元素后出现的段落，h1 和 p 元素拥有共同的父元素”。

```
h1 + p {margin-top:50px;}   
```

这个选择器解释为：选择紧接在 table 元素后出现的所有兄弟 ul 元素，该 table 元素包含在一个 body 元素中，body 元素本身是 html 元素的子元素。

```
html > body table + ul {margin-top:20px;}
```

### 伪类

为了确保伪类选择器生效，按照LVHA的顺序书写，顺序：link visited hover active

| 属性     | 描述                                     |
| -------- | ---------------------------------------- |
| :active  | 向被激活的元素添加样式                   |
| :focus   | 向拥有键盘输入焦点的元素添加样式         |
| :hover   | 当鼠标悬浮在元素上方时，向元素添加样式   |
| :link    | 向未被访问的链接添加样式                 |
| :visited | 向已被访问的链接添加样式                 |
| :lang    | 向带有指令lang属性的元素添加样式         |
| :not()   | 用来匹配不符合一组选择器的元素           |
| :is()    | 选择该列表中任意一个选择器可以选择的元素 |

```css
/* 类名不是 `.fancy` 的 <p> 元素 */
p:not(.fancy) {
  color: green;
}

/* 选择header, main, footer里的任意一个悬浮状态的段落(p标签) */
:is(header, main, footer) p:hover {
  color: red;
  cursor: pointer;
}
/* 以上内容相当于以下内容 */
header p:hover,
main p:hover,
footer p:hover {
  color: red;
  cursor: pointer;
}
```

### 结构伪类选择器(CSS3)

结构伪类选择器主要根据文档结构来选择元素，常用于选择父亲里面的第几个孩子。

| 选择符           | 简介                       |                                                              |
| ---------------- | -------------------------- | ------------------------------------------------------------ |
| E:first-child    | 匹配父元素的第一个子元素E  | ul li:first-child  表示ul中的第一个li                        |
| E:last-child     | 匹配父元素的最后一个E元素  | ul li:last-child  表示ul中的最后一个li                       |
| E:nth-child(n)   | 匹配父元素中的第n个子元素E | n可以是数字、关键字和公式<br />1. ul li:nth-child(3)  表示ul中的第3个li;<br />2. 关键字：even偶数，odd奇数<br />3. 公式：常见公式下下表 |
| E:first-of-type  | 指定类型E的第一个          |                                                              |
| E:last-of-type   | 指定类型E的最后一个        |                                                              |
| E:nth-of-type(n) | 指定类型E的第n个           |                                                              |

| 公式 | 取值                           |
| ---- | ------------------------------ |
| 2n   | 偶数                           |
| 2n+1 | 奇数                           |
| 5n   | 0 5 10 15……                    |
| n+5  | 从第5个开始（包括第5个）到租后 |
| -n+5 | 前5个（包括第5个）             |

child 和type的区别：

- nth-child会把所有的盒子都排列序号，然后去看前面的子元素名
- nth-of-type会先把指定元素的盒子排列好顺序，然后找第几个

### 伪元素(CSS3)

| 属性           | 描述                           |
| -------------- | ------------------------------ |
| ::first-letter | 向文本的第一个字母添加特殊样式 |
| ::first-line   | 向文本的首行添加特殊样式       |
| ::before       | 在元素之前添加内容             |
| ::after        | 在元素之后添加内容             |

注意：

- before和after创建一个元素，但是属于行内元素
- 新创建的这个元素在文档树中是找不到的，所以称为伪元素
- before和after必须有content属性
- before在父元素内容的前面创建元素，after在父元素内容的后面插入元素
- 伪元素选择器和标签选择器一样，权重为1
- 每个选择器最多只能出现一个伪元素

```css
p::before {content:"第一章：";}
p::after {content:"Hot!";}
p::first-line {background:red;}
p::first-letter {font-size:30px;}
```

伪元素和伪类的区别：

伪元素：在内容元素的前后插入额外的元素或样式，但是这些元素实际上并不在文档中生成。它们只在外部显示可见，但不会在文档的源代码中找到它们，因此，称为“伪”元素。

伪类：将特殊的效果添加到特定选择器上。它是已有元素上添加类别的，不会产生新的元素。

### 优先级

| **选择器**     | **格式**      | **优先级权重** |
| -------------- | ------------- | -------------- |
| !important     |               | ∞ 无穷大       |
| 行内样式       | style=“”      | 1000           |
| id选择器       | #id           | 0100           |
| 类选择器       | .classname    | 0010           |
| 属性选择器     | [ref=“eee”]   | 0010           |
| 伪类选择器     | li:last-child | 0010           |
| 标签选择器     | div           | 0001           |
| 伪元素选择器   | li:after      | 0001           |
| 相邻兄弟选择器 | h1+p          | 0000           |
| 子选择器       | ul>li         | 0000           |
| 后代选择器     | li a          | 0000           |
| 通配符选择器   | *             | 0000           |

```css
a{color: yellow;} /*特殊性值：0,0,0,1*/
div a{color: green;} /*特殊性值：0,0,0,2*/
.demo a{color: black;} /*特殊性值：0,0,1,1*/
.demo input[type="text"]{color: blue;} /*特殊性值：0,0,2,1*/
.demo *[type="text"]{color: grey;} /*特殊性值：0,0,2,0*/
#demo a{color: orange;} /*特殊性值：0,1,0,1*/
div#demo a{color: red;} /*特殊性值：0,1,0,2*/
```

## 图片属性

### object-fit

属性指定元素的内容应该如何去适应指定容器的高度与宽度。一般用于 img 和 video 标签，一般可以对这些元素进行保留原始比例的剪切、缩放或者直接进行拉伸等。

```
object-fit: fill|contain|cover|scale-down|none|initial|inherit;
```

| 值         | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| fill       | 默认，不保证保持原有的比例，内容拉伸填充整个内容容器。       |
| contain    | 保持原有尺寸比例。内容被缩放。                               |
| cover      | 保持原有尺寸比例。但部分内容可能被剪切。                     |
| none       | 保留原有元素内容的长度和宽度，也就是说内容不会被重置。       |
| scale-down | 保持原有尺寸比例。内容的尺寸与 none 或 contain 中的一个相同，取决于它们两个之间谁得到的对象尺寸会更小一些。 |

## 背景

### 背景色

```css
background-color: transparent || color;
```

### 背景图片

```css
background-image: none || url();
body {background-image: url(/i/eg_bg_04.gif);}
```

### 背景图片水平或垂直平铺

```css
background-repeat: repeat || no-repeat || repeat-x || repeat-y
body
 {
 background-image: url(/i/eg_bg_03.gif);
 background-repeat: repeat-y;
 }

```

### 背景定位 background-position 属性

利用background-position属性可以改变图片在背景中的位置

```
background-position: x y; // x坐标和y坐标。可以使用方位名词或者精确单位
```

| 参数值   | 说明                                                       |
| -------- | ---------------------------------------------------------- |
| length   | 百分数 \| 由浮点数字和单位表示符组成的长度值               |
| position | top \| center \| bottom \| left \| center \|right 方位名词 |

```css
body 
{
  background-image:url('/i/eg_bg_03.gif');
  background-repeat:no-repeat;
  background-position:center;
 }
/* or */
background-position:50% 50%;
background-position:50px 100px;
```

### 背景图像固定或附着

```css
background-attach: scroll || fixed
```

### 背景半透明

```css
background: rgba(0, 0, 0, 0.3)
```
a: alpha的缩写，透明度值，取值0-1之间

### 背景图片尺寸

属性规定背景图片的尺寸

```
background-size: contain | cover | width-value,height-value;;
```

| 属性                     |                                                              |
| ------------------------ | ------------------------------------------------------------ |
| contain                  | 保持图片的宽高比和 当图像和容器具有不同的尺寸时，图像被左/右或顶部/底部裁剪 |
| cover                    | 保持图片的宽高比和当图像和容器具有不同的尺寸时，空区域（左/右/上/右）填充背景色 |
| width-value,height-value | 写死；auto就是以背景图片的比例缩放背景图片                   |

### 背景属性合并写法

background: 背景颜色 背景图片地址 背景平铺 背景图片滚动 背景图片位置（一般如此，也可更改）

## 列表属性

### 列表类型

```css
ul {list-style-type : square}   //disc  circle square none
// disc：实心圆
// circle: 空心圆
```

### 列表项图像

```css
ul li {list-style-image : url(xxx.gif)}
```

### 列表位置

```css
ul {
    list-style-position: inside; // 默认 outside
}
```

### 合并属性

```css
ul {
 list-style:square inside url('/i/arrow.gif');
 }
```

## border

### border-radius

圆角边框。CSS3添加属性

```scss
// length 为左上角、右上角、右下角、左下角的简写
.table {
	border-radius: length;
}
// 圆形
.circle {
    height: 200px;
    width: 200px;
    border-radius: 100px;  // border-radius: 50%;
}
```

### border-width

边框的宽度，可取值 `thin medium thick`，默认值`medium`

### border-style

border-style：none | hidden | dotted | dashed | solid | double | groove | ridge | inset | outset

none： 无轮廓。border-color与border-width将被忽略

hidden：隐藏边框。IE7及以下尚不支持

dotted：点线轮廓

dashed：虚线轮廓。

solid：实线轮廓

double：双线轮廓。两条单线与其间隔的和等于指定的border-width值

groove：根据borde-color的值画3D凹槽轮廓。

ridge：根据borde-color的值画3D凸槽轮廓。

inset：根据borde-color的值画3D凹边轮廓。

outset：根据borde-color的值画3D凸边轮廓。

### **box-sizing(css3)**

| 属性值                  | 描述                               |
| ----------------------- | ---------------------------------- |
| box-sizing: content-box | 盒子大小为width + padding + border |
| box-sizing: border-box  | 盒子大小为width                    |

### border-collapse

border-collapse: collapse;  // 表格相邻边框合并

### outline-width

边框外面轮廓的宽度，可取值 `thin medium thick`，默认值`medium`

##  box-shadow

盒子阴影，CSS3添加属性

设置一个或多个下拉阴影的框

```
box-shadow: h-shadow v-shadow blur spread color inset || none; // 默认为null
```

| 值       | 描述                               |
| -------- | ---------------------------------- |
| h-shadow | 必需。水平阴影的位置。允许负值     |
| v-shadow | 必需。垂直阴影的位置。允许负值     |
| blur     | 可选。模糊距离                     |
| spread   | 可选，阴影的尺寸                   |
| color    | 可选。阴影的颜色                   |
| inset    | 可选。将外部阴影outset改为内部阴影 |

注意：默认是外阴影，但不可以写outset，否则导致 阴影无效；阴影不占用空间，不影响其他盒子；

```css
box-shadow: 2px 0 2px 2px #ccc, -2px 0 2px 2px #ccc; // 左右两边有阴影
```

## text-shadow

文字阴影，CSS3添加属性

```
text-shadow: h-shadow v-shadow blur color;
```

| 值       | 描述                           |
| -------- | ------------------------------ |
| h-shadow | 必需。水平阴影的位置，允许负值 |
| v-shadow | 必需。垂直阴影的位置，允许负值 |
| blur     | 可选。模糊的距离               |
| color    | 可选。阴影的颜色               |

## mix-blend-mode

mix-blend-mode 属性描述了元素的内容应该与元素的直系父元素的内容和元素的背景如何混合。CSS3添加属性

```css
mix-blend-mod: normal | multiply | screen | ……s 
```

## 浮动

多个块级元素纵向排列用标准流；多个块级元素横向排列用浮动

### 浮动特性

1. 浮动元素会脱离标准流，移动到指定位置；浮动的盒子不再保留原先的位置
2. 浮动的元素会一行内展示并且元素顶部对其。如果多个盒子都设置了浮动，则他们会按照属性值一行内显示并且顶端对其排列
3. 浮动的元素会具有行内块元素的特性。如果行内元素有了浮动，则不需转换为块级或行内块级别就可以直接给高度和宽度
4. 浮动元素经常和标准流的父元素搭配使用

### 浮动float

```
float: left || right || both;
```

### 清除浮动

```
clear: left || right || both;
```

* 清除浮动的本质是清除浮动元素造成的影响
* 如果父盒子有高度，则不需要清除浮动
* 清除浮动后，父盒子就会根据浮动的子盒子确定高度

清除浮动的方法(解决父元素高度塌陷)：

- 额外标签法也称为隔墙法，在最后一个浮动元素末尾添加一个空的标签，如
```
<div style="clear: both"></div>
```
- 父级添加overflow属性（创建父级BFC）
```
overflow: hidden || auto || scroll; // 几种属性都可以，注意是给父元素
```
- 父级添加after伪元素
```scss
.clearfix::after {
		content: ""; // after伪元素必须添加属性
    display: block;
    height: 0;
    clear: both;
    visibility: hidden;
}
.clearfix {
    *zoom: 1; // 兼容IE6、7清除浮动
}
```
- 父级添加双伪元素
```scss
.clearfix {
    *zoom: 1;
    &::before, 
    &::after{
        content: "";
        display: table;
    }
}
.clearfix::after {
    clear: both;
}
```

## white-space

指定元素内的空白怎样处理

|                | 换行符 | 空格和制表符 | 文字换行 | 行尾空格 |
| :------------- | :----- | :----------- | :------- | -------- |
| `normal`       | 合并   | 合并         | 换行     | 删除     |
| `nowrap`       | 合并   | 合并         | 不换行   | 删除     |
| `pre`          | 保留   | 保留         | 不换行   | 保留     |
| `pre-wrap`     | 保留   | 保留         | 换行     | 挂起     |
| `pre-line`     | 保留   | 合并         | 换行     | 删除     |
| `break-spaces` | 保留   | 保留         | 换行     | 换行     |

## clip

剪裁绝对定位元素，用于定义一个剪裁矩形。对于一个绝对定位元素，在这个矩形内的内容才可见。出了这个剪裁区域的内容会根据 overflow 的值来处理。剪裁区域可能比元素的内容区大，也可能比内容区小。

```css
clip: rect (top, right, bottom, left)
```

```css
img {
  position:absolute;
  clip:rect(0px 50px 200px 0px)
}
```

clip-path

https://segmentfault.com/a/1190000023301221

## 精灵图

为了减小服务器发送和请求的次数，将图片放在一起，一次性返回，多用于背景图片，background-image，background-repeat，background-position属性的组合进行背景定位

## 鼠标样式

```
{cursor: pointer; }
```

| 属性值（常用） | 描述 |
| -------------- | ---- |
| default        | 默认 |
| pointer        | 小手 |
| move           | 移动 |
| text           | 文本 |
| not-allowed    | 禁止 |

## transition(css3)

transition: 要过渡的属性  花费时间  运动曲线  何时开始；

1. 属性：想要变化的CSS属性，如果需要所有属性都变化，写一个all
2. 花费时间：单位是秒，必须写单位，例如0.5s
3. 运动曲线：默认是ease（可省略）

| 名称        | 描述         |
| ----------- | ------------ |
| linear      | 匀速         |
| ease        | 逐渐慢下来   |
| ease-in     | 加速         |
| ease-out    | 减速         |
| ease-in-out | 先加速后减速 |

4. 何时开始：单位是秒，必须写单位，可以设置延时触发时间，默认是0s(可省略)

```scss
// 谁做过渡给谁加transition
// 将鼠标悬停在一个 div 元素上，逐步改变表格的宽度从 100px 到 400px：
div {
    width: 100px;
    height: 100px;
    background-color: pink;
    // 1. 单个属性变化
    transition: width .5s ease 0s;
    // 2. 多个属性变化
    // trasnsition: width .5s, height .5s;
    // 3. 全部属性变化
    // transition: all .5s;
    &:hover {
        width: 400px;
    }
}
```

## animation

https://juejin.cn/post/6844904034126528520#heading-10

| property                  | Description                                                  |                                                              |
| ------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| @keyframes                | Specifies the animation code                                 |                                                              |
| animation                 | A shorthand property for setting all the animation properties |                                                              |
| animation-delay           | Specifies a delay for the start of an animation              | 也可以接收负数，表示已经运行了x秒                            |
| animation-direction       | Specifies whether an animation should be played forwards, backwards or in alternate cycles | normal \| reverse \| alternate \| alternate-reverse          |
| animation-duration        | Specifies how long time an animation should take to complete one cycle | number                                                       |
| animation-fill-mode       | Specifies a style for the element when the animation is not playing (before it starts, after it ends, or both) | none \| forwards \| backwards \| both                        |
| animation-iteration-count | Specifies the number of times an animation should be played  | number \| 也可以使用"infinite"一直循环                       |
| animation-name            | Specifies the name of the @keyframes animation               |                                                              |
| animation-play-state      | Specifies whether the animation is running or paused         |                                                              |
| animation-timing-function | Specifies the speed curve of the animation                   | ease \| linear \| ease-in \| ease-out \| ease-in-out \| cubic-bezier(n, n, n, n) |

```css
/* The animation code */
@keyframes example {
  from {background-color: red;} // from 相当于0%
  to {background-color: yellow;} // to相当于100%
}

/* The animation code */
@keyframes example2 {
  0%   {background-color: red;}
  25%  {background-color: yellow;}
  50%  {background-color: blue;}
  100% {background-color: green;}
}

/* The element to apply the animation to */
div {
  width: 100px;
  height: 100px;
  background-color: red;
  // animation: example
  animation-name: example;
  animation-delay: 2s;
  animation-duration: 4s;
  animation-iteration-count: 3;
  animation-direction: reverse;
  animation-timing-function: linear; // 
}
```

transition和animation的区别：

transition是过度属性，强调过度，它的实现需要触发一个事件（比如鼠标移动上去，焦点，点击等）才执行动画。设置一个开始关键帧，一个结束关键帧。
animation是动画属性，它的实现不需要触发事件，设定好时间之后可以自己执行，且可以循环一个动画。它可以设置多个关键帧（用@keyframe定义）完成动画。

## filter

定义了元素(通常是<img>)的可视效果(例如：模糊与饱和度)。使用空格分隔多个滤镜。

```
filter: none | blur() | brightness() | contrast() | drop-shadow() | grayscale() | hue-rotate() | invert() | opacity() | saturate() | sepia() | url();
```

## text-overflow

文字无法完全展示时行为

```css
text-overflow: clip | ellipsis
```

## CSS函数

| 函数              | 说明                                                         | 示例                                                        |
| ----------------- | ------------------------------------------------------------ | ----------------------------------------------------------- |
| attr()            | 返回选择元素的属性值                                         | a:after {    content: " (" attr(href) ")"; }                |
| calc()            | 允许计算 CSS 的属性值，比如动态计算长度值                    | width: calc(100% - 100px)                                   |
| rgb()             | 使用红(R)、绿(G)、蓝(B)三个颜色的叠加来生成各式各样的颜色    |                                                             |
| rgba()            | 使用红(R)、绿(G)、蓝(B)、透明度(A)的叠加来生成各式各样的颜色 |                                                             |
| linear-gradient() | 创建一个线性渐变，需要指定两种颜色，还可以实现不同方向（指定为一个角度）的渐变效果，如果不指定方向，默认从下到上渐变 | background-image: linear-gradient(red, yellow, blue)<br />n |
| radial-gradient() | 用径向渐变创建图像（从圆心向圆外渐变）                       | background-image: radial-gradient(red, green, blue);        |

### var

```
定义：--color: blue;
使用：var(--color, ?initialValue) // 当变量不存在时，就会使用初始值
```

- 变量值只能用作属性值，不能用作属性名
- 无论是变量的定义和使用只能在声明块{}里面
- 全局变量：:root 伪类可以看做是一个全局作用域，在里面声明的变量，他下面的所有选择器都可以拿到

```css
// 全局变量
:root { --color: blue; }
.box{color: var(--color)}

// 局部变量
.box{
    --color: red;
    color: var(--color);
}

```

## @类

### @media

https://www.w3school.com.cn/cssref/pr_mediaquery.asp

在媒体查询中用于为不同的媒体类型/设备应用不同的样式。

```css
@media not|only mediatype and (mediafeature and|or|not mediafeature) {
  CSS-Code;
}

// eg
// 如果浏览器窗口的宽度为 600px 或更小时，把 <body> 元素的背景颜色更改为“浅蓝色”
@media only screen and (max-width: 600px) {
  body {
    background-color: lightblue;
  }
}
```

- not、only 和 and 关键字的含义：

  - not：not 关键字反正整个媒体查询的含义。

  - only：only 关键字可防止旧版浏览器应用指定的样式，这些浏览器不支持带媒体特性的媒体查询。它对现代浏览器没有影响。

  - and：and 关键字将媒体特性与媒体类型或其他媒体特性组合在一起。

- mediatype

  | 值     | 描述                                   |
  | ------ | -------------------------------------- |
  | all    | 默认。用于所有媒体类型设备。           |
  | print  | 用于打印机。                           |
  | screen | 用于计算机屏幕、平板电脑、智能手机等。 |
  | speech | 用于朗读页面的屏幕阅读器。             |

- 媒体特性

  | 值（常用） | 描述                                 |
  | ---------- | ------------------------------------ |
  | height     | 视口（viewport）的高度。             |
  | max-width  | 显示区域的最大宽度，例如浏览器窗口。 |
  | min-height | 显示区域的最小高度，例如浏览器窗口。 |
  | min-width  | 显示区域的最小宽度，例如浏览器窗口。 |

## css特性

**层叠性**

相同选择器设置相同的样式，此时一个样式就会覆盖（层叠）另一个冲突的样式。层叠性主要解决样式冲突的问题

层叠性原则：

- 样式冲突，遵循的原则是就近原则，那个样式离结构近，就执行哪个样式
- 样式不冲突，不会层叠

**继承性**

子标签可以继承父标签的某些样式，如颜色和字号，text-，font-，line-，color等属性可以继承



外边距合并，当上下2个盒子同时分别拥有margin-bottom和margin-top时，以二者之间的较大值作为两个盒子之间的margin





















