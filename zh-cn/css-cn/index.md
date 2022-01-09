# CSS


# CSS

1. CSS是什么
2. CSS怎么用
3. CSS选择器（重点）
4. 美化网页（文字，阴影，渐变……）
5. 盒子模型
6. 浮动
7. 定位
8. 网页动画（特效）



## 发展

CSS1.0

CSS2.0： DIV块，CSS与HTML结构分离，SEO

CSS2.1： 浮动，定位

CSS3.0： 圆角，阴影，动画……



## 四种导入方式

```html
<!-- 外部样式：链接式 -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <link rel="stylesheet" href="css/style.css"/>
</head>
<body>
<h1>Big title</h1>

</body>
</html>
```



```css
h1{
    color: red;
}
```



```html
<!-- 外部样式：导入式 -->
<style>
    @import url(css/style.css)
</style>
<!-- 弊端！ 会展现结构之后再渲染！ link会等全部渲染完之后在展现 -->
```





```html
<!-- 内部样式 -->
<style>
	h1{color: red;} /* style标签内是css code */
</style>

<h1>Big title</h1>
```



```html
<!-- 行内样式 -->
<h1 style="color: red">Big title</h1>
```



**优先级： 就近原则： 谁离这个元素更近，谁优先级高。所以行内元素优先级最高，其他的就看谁离的更近 **



## 选择器

#### 作用

选择页面上的某一个元素或者某一类元素。



### 基本选择器

1. 标签选择器
2. 类选择器
3. id选择器



#### 标签选择器

```css
h1{
    color: #121432;
    background: #FFFFFF;
    border-radius: 15px;
    font-size: 80px;
}
```

会选中所有的h1标签！



#### 类选择器

```css
.groupA{
    color: #111111;
}
```

```html
<h1 class="groupA">
    TITLE
</h1>
<p class="groupA">
    THis is amazing!
</p>
```



#### id选择器

```css
#item1{
    font-size: 20px;
}
```

```html
<h1 id="item1">
    TITLE
</h1>
```

id必须全局保证唯一！



#### 优先级

**id选择器 > class选择器 > tag选择器**



## 高级选择器

### 层次选择器

```css
/* 后代选择器 */
body p{ /* body之后的p全部选中 */
    backgroud: red;
}
/* 子选择器 */
body>p{ /* body之后的第一代p */
    backgroud: red;
}
/* 相邻兄弟选择器 */
.active+p{ /* active向下且相邻的同辈的才被选中 */
    backgroud: red;
}
/* 通用兄弟选择器 */
.active~p{ /* active向下的同辈的被选中 */
    backgroud green;
}
```

```html
<body>
    <p>p0</p>
    <p class="active">p1</p>
    <p>p2</p>
    <p>p3</p>
    <ul>
        <li>
            <p>p4</p>
        </li>
        <li>
            <p>p5</p>
        </li>
        <li>
            <p>p6</p>
        </li>
    </ul>
</body>
```



### 结构伪类选择器

```css
ul li:first-child{
    background: green;
}
p:nth-child(1){ /* 选中当前元素的父元素的第n个子元素 */
    background: blue;
}

p:nth-type(2){ /* 选中当前元素的父元素的第n个type为p的子元素 */
    background: yellow;
}

a:hover{
    background: gold;
}
```

```html
<body>
    <h1>title</h1>
    <p class="active">p1</p>
    <p>p2</p>
    <p>p3</p>
    <ul>
        <li>li1</li>
        <li>li2</li>
        <li>li3</li>
    </ul>
    <a href="">123123123</a>
</body>
```



### 属性选择器

```css
.group_link a{
    float: left;
    display: block;
    height: 50px;
    width: 50px;
    border-radius: 8px;
    background: black;
    text-align: center;
    color: white;
    text-decoration: none;
    margin-right: 5px;
    font: bold 20px/50px Arial;
}
/* 选中存在某属性的元素 */
a[id]{
    background: yellow;
}
a[id=first]{
    background: green;
}
a[class*="links"]{ /* *=是包含 */
    background: red;
}
a[href^=http]{ /* ^=是以什么开头 $=结尾*/
    background: red;
}
```

```html
<body>
    <p class="group_link">
        <a href="https://www.google.com" class="first item" id="first">1</a>
        <a href="https://www.apple.com" class="active" target="_blank" title="apple">2</a>
        <a href="https://www.amazon.com" class="active">3</a>
        <a href="https://www.linkedin.com" class="active">4</a>
        <a href="image/some.pdf" class="active">5</a>
        <a href="image/some.jpg" class="dim">6</a>
        <a href="image/some.html" class="dim">7</a>
        <a href="image/some" class="dim">8</a>
    </p>
</body>
```


## 美化网页
### 字体样式

约定俗成，想要重点突出的问题用span标签。

```html
<span id="emphasize_this">HTML</span>
<style>
    #emphasize_this{
        font-size: 30px;
        font-family: Arial, 楷体;
        font-weight: lighter;
        color: brown;
    }
    p{
        font: oblique bolder 12px "Arial";
    }
</style>
```



### 文本样式

颜色，对齐，首行缩进，行高，装饰

```css
h1{
    color: rgb(0,255,255);
    text-align: center;
}

h2{
    color: rgba(0,255,255,0.4588);
}

.p1{
    text-indent: 2em;
    height: 50px;
    line-height: 50px; /*行高跟高度相同就可以上下居中*/
    text-decoration: line-through;
}

img, span{
    vertical-align: middle;
}
```



### 文本阴影

```css
#price{
    text-shadow: #3cc7f5 2px 2px 2px; /* 阴影颜色 水平偏移 垂直偏移 阴影半径 */
}
```



### 超链接伪类

link  visited  hover  active

```css
p:link{
    ...
}
```



### 背景

```css
/*背景颜色 背景图片*/
div{
    width: 1000px;
    height: 700px;
    border: 1px solid red;
    background-image: url("path");
    /*默认平铺*/
}

.div1{
    background-repeat: 10px 20px no-repeat; /* repeat-y repeat-x ... */
    /* or use background-positon*/
}

.div2{
    background-color: #FFFFFF;
    background-image: linear-gradient(115deg, #FFFFFF 0%, #6284FF 50%, #FF0000 100%)
}
```



### 盒子模型

margin

border

padding

边框，内外边距，div居中，圆角边框，阴影

```css
h1{
    color: red;
}

h2{
    font-size: 16px;
    background: aquamarine;
    line-height: 30px;
    margin: 0px;
}

body{
    margin: 0;
    padding: 0;
    text-decoration: none;
}

#box{
    width: 300px;
    border: 1px solid greenyellow;
}

form{
    background: aquamarine;
}

div:nth-of-type(1)>input{
    border: 3px solid powderblue;
}

div:nth-of-type(2)>input{
    border: 3px dashed powderblue;
}

div:nth-of-type(3)>input{
    border: 3px dot-dash powderblue;
}
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <link rel="stylesheet" href="css/style.css"/>
</head>
<body>
<h1>Login</h1>

<div id="box">
    <h2>Member Login</h2>
    <form action="#">
        <div>
            <span>Username: </span>
            <input type="text"/>
        </div>
        <div>
            <span>Password: </span>
            <input type="password"/>
        </div>
        <div>
            <span>Email: </span>
            <input type="text"/>
        </div>

    </form>
</div>

</body>
</html>
```



### 内外边距

```css
#box{
    width: 300px;
    border: 1px solid greenyellow;
    padding: 0 0 0 0; /*上右下左， 如果只写两个，那么第一个是上下的，第二个是左右的*/
}
```



### 圆角边框

```css
div{
    width: 100px;
    height: 100px;
    border: 10px solid red;
    border-radius: 50px 20px;
}
/* 两个值的话，主对角线和辅对角线对应的1/4圆，我们可以通过设置宽高边界半径来获得半圆或者1/4圆等等*/
```



### 阴影

```css
div{
    width: 100px;
    height: 100px;
    border: 10px solid red;
    box-shadow: 10px 10px 30px yellow;
}
```



### 居中

```css
div{
    width: 300px;
    height: 20px;
    margin: 0 auto; /* div块居中 */
    text-align: center; /* 块内的text居中 */
}

body>img{
    display: block;
    margin: 0 auto;
}

h2{
    font-size: 16px;
    background: aquamarine;
    line-height: 30px;
    margin: 0px;
    text-align: center;
}

```

```html
<h2>
    <img src="./assets/avatar.png" width="15" height="15"/>
    Member Login
</h2>
```



### 浮动

标准文档流

 

块级元素：独占一行

行内元素：可以被包含在块级元素中



float可以让选中的地方脱离背景框的束缚，浮动起来，会把其他元素挤开，网页大小改变后，会重新排版。  

```css
.layer1{
    diplay: inline-block;
    float: left;
}
```



### Dispaly

block: 块元素

inline: 行内元素

inline-block: 块元素，但是可以内联

none: 不显示

```html
<div>div</div>
<span>span</span>
```

```css
span{
    display: inline-block;
}

div{
    display: inline;
}
other{
    clear: both; /*清除浮动效果*/
}
```



### 父级边框塌陷问题

clear: right 右侧不允许有浮动元素

clear: left

clear: both

clear: none



解决方法：

1. 增加父级元素的高度

2. 增加一个 空的div标签，清除浮动

   ```html
   <div id="father">
       <div class="layer01"><img src="path" alt=""/></div>
       <div class="layer02"><img src="path" alt=""/></div>
       <div class="layer03"><img src="path" alt=""/></div>
       <div class="layer04"><img src="path" alt=""/></div>
       <div class="clear"></div>
   </div>
   ```

   ```css
   .clear{
       float: both;
       margin: 0;
       padding: 0;
   }
   #father{
       border: 1px #000 solid;
       overflow: hidden;
   }
   ```





### Overflow

给content设置一个高度和宽度，如果内容超出了这个边界，设置overflow: scroll就可以出现滚动条

我们也可以像上面的father元素一样，在其中设置一个overflow: hidden, 由于father没有设置一个宽高，其宽高由子元素撑起，所以并没有超出的部分会被隐藏。



#### 最认可的方法

或者在father之后加一个伪类

避免了空div的添加

```css
#father:after{
    content:'';
    display: block;
    clear: both;
}
```



#### 对比

- display 方向不可控制
- float 浮动起来的话会脱离标准文档流，所以要解决父级边框塌陷的问题



### 定位

#### 相对定位

position: relative 是相对于自己原来的位置移动

left: 200px;

top: -100px;



#### 绝对定位

position: absolute 

没有父级元素定位的情况下，基于浏览器边框，浏览器是最大的box

有父级元素定位时，基于父级元素定位，不能超出父级元素。

```css
#father{
    position: relative;
}

#son{
    position: absolute;
    right: 30px;
}
```



### 固定定位

无论怎么scroll都不会移动，绝对定位是会动的。

```css
sub{
    position: fixed;
    right: 0;
    bottom: 0;
}
```



### Z-index

```css
layer1{
    z-index: 999; /*高度*/
    opacity: 0.5;
}
```



## 动画
































































































































































