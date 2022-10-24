## sass

https://www.sass.hk/guide/

### 嵌套CSS规则

```scss
$nav-color: #F90;
nav {
    $width: 100px;
    width: $width;
    color: $nav-color;
}
// 编译后
nav {
    width: 100px;
    color: #F90;
}
```

（1）普通嵌套和&

一般情况下，sass在解开一个嵌套规则时就会把父选择器（#content）通过一个空格连接到子选择器的前边（article和aside）形成（#content article和#content aside）。这种在CSS里边被称为后代选择器。

使用&符号时，被父选择器直接替换

```scss
article a {  
	color: blue;  
    &:hover { 
        color: red 
    } 
}
```

解析后：

```scss
article a { 
	color: blue 
} 
article a:hover { 
    color: red 
}
```

（2）群组选择器、子选择器、兄弟选择器嵌套

```scss
.container {  
	h1, h2, 
	h3 {
		margin-bottom: .8em
	} 
}
nav, aside {  
    a {
        color: blue
    } 
}
article {  
    ~ article { 
        border-top: 1px dashed #ccc 
    }  
    > section { 
        background: #eee 
    }  
    dl > {    
        dt { 
            color: #333 
        }    
        dd { 
            color: #555 
        }  
    }  
    nav + & { 
        margin-top: 0 
    } 
}
```

（3）嵌套属性

对于属性的缩写形式，可以像下边这样嵌套，致命例外规则

```scss
nav {
    border: 1px solid #ccc {
        left: 0px;
        right: 0px;
        }
}

// 这比下边这种同等样式的写法要好
nav {
    border: 1px solid #ccc;
    border-left: 0px;
    border-right: 0px;
}
```

### 导入SASS文件@import

（1）部分导入

sass局部文件的文件名以下划线开头。这样，sass就不会在编译时单独编译这个文件输出css，而只把这个文件用作导入

（2）默认变量值

使用sass的!default标签。它很像css属性中!important标签的对立面，不同的是!default用于变量，含义是：如果这个变量被声明赋值了，那就用它声明的值，否则就用这个默认值

###  静默注释

```
body {
  color: #333; // 这种注释内容不会出现在生成的css文件中
  padding: 0; /* 这种注释内容会出现在生成的css文件中 */
}
```

### 混合器@mixin

（1）混合器使用

混合器使用@mixin标识符定义，在样式表中通过@include来使用这个混合器，放在你希望的任何地方。@include调用会把混合器中的所有样式提取出来放在@include被调用的地方。

```scss
@mixin large-text {
  font: {
    family: Arial;
    size: 20px;
    weight: bold;
  }
  color: #ff0000;
}
.page-title {
  @include large-text;
  padding: 4px;
  margin-top: 10px;
}

// 编译为
.page-title {
  font-family: Arial;
  font-size: 20px;
  font-weight: bold;
  color: #ff0000;
  padding: 4px;
  margin-top: 10px; }
```

（2）混合器传参

```scss
@mixin link-colors($normal, $hover, $visited) {

  color: $normal;

  &:hover { color: $hover; }

  &:visited { color: $visited; }

}
```

使用时：

```scss
a {
  @include link-colors(blue, red, green);
}
```

Sass最终生成的是：

```css
a { color: blue; }
a:hover { color: red; }
a:visited { color: green; }
```

或者使用以下方式传参

```
a {
 @include link-colors(
 $normal: blue,
 $visited: green,
 $hover: red
 );
}
```

### 继承

#### @extend

@extend 的作用是将重复使用的样式extend给需要包含这个样式的特殊样式， 将 .error 下的所有样式继承给 .seriousError

```scss
.error {
  border: 1px #f00;
  background-color: #fdd;
}
.error.intrusion {
  background-image: url("/image/hacked.png");
}
.seriousError {
  @extend .error;
  border-width: 3px;
}
```

编译为：

```css
.error, .seriousError {
  border: 1px #f00;
  background-color: #fdd; 
}
.error.intrusion, .seriousError.intrusion {
  background-image: url("/image/hacked.png"); }

.seriousError {
  border-width: 3px; }
```

Sass 不可以将 `@media` 层外的 CSS 规则延伸给指令层内的 CSS，这样会生成大量的无用代码。也就是说，如果在 `@media` （或者其他 CSS 指令）中使用 `@extend`，必须延伸给相同指令层中的选择器

#### @extend-Only 选择器

如果使用普通的 CSS 规则，最后会编译出很多用不到的样式，也容易与其他样式名冲突，所以，Sass 引入了“占位符选择器” (placeholder selectors)，看起来很像普通的 id 或 class 选择器，只是 # 或 . 被替换成了 %。可以像 class 或者 id 选择器那样使用，当它们单独使用时，不会被编译到 CSS 文件中

```scss
 %dot-before-status {
     content: '';
     display: inline-block;
     width: 8px;
     height: 8px;
     margin-right: 5px;
     border-radius: 8px;
}
.ant-tag {
    margin-right: 10px;            
    &:nth-child(2)::before {
        @extend %dot-before-status;
        background-color: red;
    }
}

// 编译后
.ant-tag:nth-child(2)::before {
  content: "";
  display: inline-block;
  width: 8px;
  height: 8px;
  margin-right: 5px;
  border-radius: 8px;
}

.ant-tag {
  margin-right: 10px;
}
.ant-tag:nth-child(2)::before {
  background-color: red;
}
```

关于@extend有两个要点：

（1）跟混合器相比，继承生成的css代码相对更少。因为继承仅仅是重复选择器，而不会重复属性，所以使用继承往往比混合器生成的css体积更小。如果你非常关心你站点的速度，请牢记这一点。

（2）继承遵从css层叠的规则。当两个不同的css规则应用到同一个html元素上时，并且这两个不同的css规则对同一属性的修饰存在不同的值，css层叠规则会决定应用哪个样式。相当直观：通常权重更高的选择器胜出，如果权重相同，定义在后边的规则胜出。

### CSS modules

https://stackoverflow.com/questions/43613619/what-does-global-colon-global-do

Modular CSS uses a CSS Modules compiler to scope CSS styles within their respective modules (e.g., React component).

```scss
:global(.ocssContainer) {
  .ui_column {
    padding-left: 0;
  }
}
```

编译后为：

```css
.ErrorMessaging__alertContainer--1I-Cz .ocssContainer .ErrorMessaging__ui_column--3uMUS {
    padding-left: 0;
  }
```

```scss
:global(.ocssContainer) {
  :global(.ui_column) {
    padding-left: 0;
  }
}
```

编译后为：

```css
.ErrorMessaging__alertContainer--1I-Cz .ocssContainer .ui_column {
    padding-left: 0;
  }
```

## less

### 变量

```less
@width: 10px;
@height: @width + 10px;

#header {
  width: @width;
  height: @height;
}
```

```less
@var: red;

#page {
  #header {
    color: @var; // white
  }
  @var: white; // 混合（mixin）和变量的定义不必在引用之前事先定义
}
```

### 混合

```less
.my-mixin {
  color: black;
}
.my-other-mixin() { // 加()后混合器不输出
  background: white;
}
.class {
  .my-mixin();
  .my-other-mixin();
}
```

outputs

```css
.my-mixin {
  color: black;
}
.class {
  color: black;
  background: white;
}
```

### 映射

```less
#colors() {
  primary: blue;
  secondary: green;
}

.button {
  color: #colors[primary];
  border: 1px solid #colors[secondary];
}
```

输出符合预期：

```css
.button {
  color: blue;
  border: 1px solid green;
}
```

### 嵌套

&除了指代父级选择器，还可以作为变量使用

```less
.button {
  &-ok {
    background-image: url("ok.png");
  }
}
```

output:

```css
.button-ok {
  background-image: url("ok.png");
}
```

## less和scss的区别

https://www.cnblogs.com/wangpenghui522/p/5467560.html

1. 编译环境不一样
2. 变量符不一样，Less是@，而Scss是$
3. 输出设置，Less没有输出设置，Sass提供4中输出选项：nested, compact, compressed 和 expanded。
4. Sass支持条件语句，可以使用if{}else{},for{}循环等等。而Less不支持
5. 引入外部CSS文件：scss@import引用的外部文件如果不想编译时多生成同名的.css文件，命名必须以_开头, 文件名如果以下划线_开头的话，Sass会认为该文件是一个引用文件，不会将其编译为同名css文件。Less引用外部文件和css中的@import没什么差异

## 