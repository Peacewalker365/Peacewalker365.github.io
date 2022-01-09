# HTML5


# HTML5

### W3C标准

World Wide Web Consortium

W3C标准：

1. 结构化标准语言 HTML XML

2. 表现标准语言 CSS

3. 行为标准语言 DOM ECMAScript



## 网页基本信息

```HTML
<!-- comment -->
<!-- DOCTYPE 设定浏览器使用什么规范 -->
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8"> <!-- meta描述性标签，他用来描述网站的一些信息，一般用来做SEO-->
        <meta name="keywords" content="HTML Tutorial">
        <meta name="description" content="Just a HTML Tutorial">
        <title>Title</title> <!-- 浏览器选项卡上的标题 -->
    </head>
    <body>
        
    </body>
</html>
```



## 基本标签

```html
<!--标题标签-->
<h1>一级标签</h1>
<h2>二级标签</h2>
<h3>三级标签</h3>
<h4>四级标签</h4>
<h5>五级标签</h5>
<h6>六级标签</h6>

<!--段落标签-->
<p>1st line</p>
<p>2nd line</p>
<p>3rd line</p>

<!--换行标签： 更紧凑，属于一段-->
1st line<br/>
2nd line<br/>

<!--水平线标签-->
<hr/>

<!--粗体，斜体-->
粗体： <b>bold</b>
斜体： <i>italic</i>

<!--特殊符号-->
空格： 1&nbsp;&nbsp;&nbsp;2
大于： &gt;
小于： &lt;
版权： &copy;
...
```



## 图像标签

```html
<img src="路径 必填" alt="图像的替代文字" title="鼠标悬停提示文字" width="x" height="y" />
JPG GIF PNG BMP
```



## 链接标签

```html
a 标签
<a id="top">顶部 使用id作为标记</a>
<a href="path" target="链接在哪个窗口打开">链接文本或图像</a>
<a href="https://www.google.com">google search</a>
<a href="https://www.google.com">
    <img src="../img/google_icon.jpg" alt="google" title="google">
</a>
<a href="https://www.google.com" target="_blank">google search</a>
_blank 在新标签打开
_parent
_self 默认的 在自己的页面打开

<!--锚链接
1. 需要一个锚
2. 跳转 
-->
<a href="#top">回到顶部</a>
<a href=" somesite.html#top">回到其他网页顶部</a>

<!--功能性链接
邮件链接： mailto:
-->
<a href="mailto:somemail.gmail.com">发邮件</a>
```



## 行内元素和块元素

 块元素： 无论内容多少，独占一行

- p h1-h6...

行内元素： 如果能排得开，几个行内元素可以在一行

- a b i...



## 列表标签

```html
<!-- 有序列表 -->
<ol>
    <li>第一条</li>
    <li>第二条</li>
</ol>
<hr/>
<!-- 无序列表 -->
<ul>
    <li>第一条</li>
    <li>第二条</li>
</ul>
<hr/>
<!-- 自定义列表 -->
<dl>
    <dt>title1</dt>
    
    <dd>1</dd>
    <dd>2</dd>
    
    <dt>title2</dt>
    
    <dd>1</dd>
    <dd>2</dd>
</dl>
```



## 表格标签

```html
<!-- 表格 -->
<table border="1px">
    <tr>
        <td colspan="3">1-1</td>
    </tr>
    <tr>
        <td rowspan="2">2-1</td>
        <td>2-2</td>
        <td>2-3</td>
    </tr>
    <tr>
        <td>3-1</td>
        <td>3-2</td>
    </tr>
</table>
```



## 视频音频

```html
<!-- 音频视频 -->
<video src="../media/xxx.mp4" controls autoplay></video>
<audio src="../media/xxxx.mp3" controls autoplay></audio>
```



## 页面结构

```html
header
footer
section
article
aside
nav
```



## iframe内联框架

```html
<iframe src="path" name="some-name" frameborder="0" width="1000px" height="800px"></iframe>
<a href="https://www.google.com" target="some-name">点击跳转</a>
```



## 表单

```html
<form method="post" action="result.html">
    <p>name: <input name="name" type="text" readonly/> </p>
    <p>password: <input name="pswd" type="password" value="123456" hidden/> </p>
    <p>性别： 
        <input type="radio" value="male" name="gender"/>男
        <input type="radio" value="female" name="gender"/>女
    </p>
    <p>爱好： 
        <input type="checkbox" value="sleep" name="hobby"/>睡觉
        <input type="checkbox" value="coding" name="hobby" checked/>代码
        <input type="checkbox" value="daze" name="hobby"/>发呆
    </p>
    <p>
        <input type="button" name="btn1" value="点击变长"/>
        <input type="image" src="path"/>
    </p>
    <p>下拉框： 
        <select name="列表名称">
            <option value="cn" selected>中国</option>
            <option value="us">美国</option>
        </select>
    </p>
    <p><label for="feedback">文本域： </label>
        <textarea name="feedback" cols="50" row="10">content here</textarea>
    </p>
    <p>文件域： 
        <input type="file" name="files"/>
        <input type="button" value="upload" name="upload"/>
    </p>
    <p>邮件： 
        <input type="email" name="email"/>
        <input type="url" name="url"/> 
    </p>
    <p>数字： 
        <input type=:"number" name="num" max="100" min="1" step="10"/>
    </p>
    <p>滑块： 
        <input type="range" min="0" max="100"/>
    </p>
    <p>搜索： 
        <input type="search" name="search"/>
    </p>
    <p>
        <input type="submit" name="submit-button" value="submit" disabled/>
        <input type="reset" name="reset-button" value="reset"/>
    </p>
</form>
```



| 属性      | 说明                                                         |
| --------- | ------------------------------------------------------------ |
| type      | 指定元素的类型 text password checkbox radio submit reset file hidden image button... |
| name      | 表单元素的名称                                               |
| value     | 元素的初始值，type为radio时必须指定一个值                    |
| size      | 指定表单元素的初始宽度。当type为text或password时，表单元素的大小以字符为单位。对于其他类型，宽度以像素为单位。 |
| maxlength | type为text或password时，输入的最大字符数                     |
| checked   | type为radio或checkbox时，指定按钮是否是被选中                |



### 表单验证

```html
<p>name: <input name="name" type="text" placeholder="请输入用户名" required/> </p>
    <p>password: <input name="pswd" type="password" pattern="^(?=.*\d)(?=.*[a-z])(?=.*[A-Z]).{8,10}$"/> </p> <!--必须包含大小写字母和数字的组合，不能使用特殊字符，长度在8-10之间-->  
```




























































