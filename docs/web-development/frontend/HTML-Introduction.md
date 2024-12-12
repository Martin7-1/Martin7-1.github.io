---
title: HTML 笔记
date: 2022-08-15 16:31:39
description: HTML 笔记
top_img: https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/miku70.jpg
tags:
    - 前端
categories:
    - 前端
---

## 概述

> HTML, Hyper Text Markup Language 超文本标记语言
> 
> 超文本：包括文字、图片、音频、视频、动画等。HTML5 提供了一些新的元素和一些有趣的新特性，同时也建立了一些新的规则。这些元素、特性和规则的建立，提供了许多新的网页功能，如使用网页实现动态渲染图形、图标、图像和动画，以及不需要安装任何插件直接使用网页播放视频等

<!-- more -->

### HTML5 的优势

1. 世界知名浏览器厂商对 HTML5 的支持：微软、Google、苹果、Opera、Mozilla
2. 市场的需求
3. 跨平台

### W3C 标准

> World Wide Web Consortium (万维网联盟)。http://www.w3.org / http://www.chinaw3c.org

成立于1994年，Web技术领域最权威和具影响力的国际中立性技术标准机构。W3C 标准包括：

1. 结构化标准语言（HTML、XML）
2. 表现标准语言（CSS）
3. 行为标准（DOM、ECMAScript）

## HTML 基本结构

> 由开放标签和闭合标签组成，比如 `<body></body>`

### 网页基本信息

```HTML
<!-- DOCTYPE: 告诉浏览器我们要使用的规范-->
<!DOCTYPE html>
<html lang="en">
    <!-- head 标签代表网页头部 -->
    <head>
        <!-- meta 描述性标签，它用来描述我们网站的一些信息-->
        <!-- meta 一般用来做 SEO-->
        <meta charset="UTF-8">
        <meta name="keywords" content="网站关键词">
        <meta name="description" content="这是描述网站的地方">
        <!-- title 标签：网页标题-->
        <title>Title</title>
    </head>

    <!-- body 标签代表网页主体-->
    <body>
        Hello, World!
    </body>
</html>
```

### 网页基本标签

1. 标题标签
2. 段落标签
3. 换行标签
4. 水平线标签
5. 字体样式标签
6. 注释和特殊符号：特殊符号都是 & 开头，分号结尾

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>基本标签学习</title>
</head>

<body>

<!-- 标题标签 -->
<h1>一级标题</h1>
<h2>二级标题</h2>
<h3>三级标题</h3>
<h4>四级标题</h4>
<h5>五级标题</h5>
<h6>六级标题</h6>

<!-- 段落标签 -->
<p>这是一个段落</p>
<p>这是第二个段落</p>

<!-- 换行标签 -->
这是第一行 <br/>
换行之后 <br/>

<!-- 水平线标签 -->
水平线之前
<hr/>
水平线之后

<!-- 字体样式标签 -->
<h1>字体样式标签</h1>

<strong>粗体</strong> <br/>
<em>斜体</em>

<!-- 注释和特殊符号 -->
<br/>

<!-- 空格 -->
空&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;格 <br/>

<!-- 大于号和小于号 -->
大于号: &gt; <br/>
小于号: &lt; <br/>

<!-- 版权符号 -->
&copy;

</body>
</html>
```

### 图像标签

`<img src="path" alt="text" title="text" width="x" height="y"/>`

> 1. src: 图像地址，分为相对地址和绝对地址，必须
> 2. alt: 图像的替代文字，图片加载失败的时候返回，必须 
> 3. title: 鼠标悬停提示文字，非必需
> 4. width: 图像宽度，非必需
> 5. height: 图像高度，非必需
> 6. ... 

### 超链接标签及应用

`<a href="path" target="目标窗口位置">链接文本或者图像</a>`

> 1. href: 必填，标识要跳转到哪个页面
> 2. target：标识窗口在哪里打开，_blank：在新标签中打开网页；_self：在自己的网页中打开

**锚链接**

需要一个锚作为标记，然后就可以跳转到标记，如何定义标记：`<a name="top">顶部</a>` 或者 `<a id="top">顶部</a>`（后者是目前使用的），通过 # 来跳转到标记。 `<a href="#top">回到顶部</a>`。对于页面链接也可以使用锚链接，比如 `<a href="https://zyinnju.com#top">跳转到博客的顶部</a>`

**邮件地址**

`<a href="mailto:201250182@smail.nju.edu.cn">发送邮件</a>`

### 块元素和行内元素

1. 块元素：无论内容为多少，该元素都独占一行，比如 `<p>`、`<h1>` 等
2. 行内元素：内容撑开宽度，左右都是行内元素的可以排在同一行，比如 `<a>`、`<strong>`、`<em>` 等

#### 列表标签

列表就是信息资源的一种展示形式。它可以使信息结构化和条理化，并以列表的样式显示出来，以便浏览者能够更加快捷地获得相应地信息

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>列表标签</title>
</head>
<body>

<!-- 有序列表 -->

<ol>
    <li>Java</li>
    <li>Python</li>
    <li>运维</li>
    <li>前端</li>
    <li>C++</li>
</ol>

<hr/>

<!-- 无序列表 应用：导航、侧边栏 -->

<ul>
    <li>Java</li>
    <li>Python</li>
    <li>运维</li>
    <li>前端</li>
    <li>C++</li>
</ul>

<hr/>

<!-- 自定义列表
 dl: 标签
 dt: 列表名称
 dd: 列表内容
 -->

<dl>
    <dt>编程语言</dt>

    <dd>Java</dd>
    <dd>Python</dd>
    <dd>Go</dd>
    <dd>C</dd>
    <dd>PHP</dd>

    <dt>城市</dt>
    <dd>上海</dd>
    <dd>北京</dd>
    <dd>南京</dd>
    <dd>长沙</dd>
</dl>

</body>
</html>
```

### 表格标签

1. 为什么要使用表格：简单通用、结构稳定
2. 基本结构：单元格、行、列、跨行、跨列

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>表格标签</title>
</head>
<body>

<!-- 表格标签
 行: td
 列: td
 -->
<table border="1px">
    <tr>
        <!-- colspan 跨列 -->
        <td colspan="4">1-1</td>
    </tr>
    <tr>
        <!-- rowspan 跨行-->
        <td rowspan="2">2-1</td>
        <td>2-2</td>
        <td>2-3</td>
        <td>2-4</td>
    </tr>
    <tr>
        <td>3-2</td>
        <td>3-3</td>
        <td>3-4</td>
    </tr>

</table>

</body>
</html>
```

### 媒体元素

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>视频和音频标签</title>
</head>
<body>

<!-- 视频 
1. src 路径
2. controls 控制条
3. autoplay 用于自动播放
-->
<video src="path" controls autoplay></video>

<!-- 音频 

-->
<audio src="path" controls autoplay></audio>

</body>
</html>
```

## 页面结构分析

<table>
    <tr>
        <td>元素名</td>
        <td>描述</td>
    </tr>
    <tr>
        <td>header</td>
        <td>标题头部区域的内容（用于页面或页面中的一块区域）</td>
    </tr>
    <tr>
        <td>footer</td>
        <td>标记脚部区域的内容（用于整个页面或页面的一块区域）</td>
    </tr>
    <tr>
        <td>section</td>
        <td>Web页面中的一块独立区域</td>
    </tr>
    <tr>
        <td>article</td>
        <td>独立的文章内容</td>
    </tr>
    <tr>
        <td>aside</td>
        <td>相关内容或应用（常用于侧边栏）</td>
    </tr>
    <tr>
        <td>nav</td>
        <td>导航类辅助内容</td>
    </tr>
</table>

## iframe 内连框架 

> 在一个网页里面嵌套另一个网页

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<!-- iframe 內联框架
src: 地址
width: 宽度
height: 高度
-->
<iframe src="" name="hello" frameborder="0" width="1000px" height="800px"></iframe>

<!-- 通过 a 标签来在 iframe 中超链接其他网页 -->
<a href="BaseLabel.html" target="hello">点击跳转</a>

</body>
</html>
```

## 表单语法

1. method: 规定如何发送表单数据，常用方法：GET、POST
2. action：标识向何处发送表单数据

```html
<form method="post" action="result.html">
    <!-- 文本输入框 -->
    <p> name: <input name="name" type="text"/></p>
    <p> password: <input name="pass" type="password"/></p>
    <p>
        <input type="submit" name="Button" value="提交"/>
        <input type="reset" name="Reset" value="重填"/>
    </p>
</form>
```

### 表单元素格式

<table>
    <tr>
        <td>属性</td>
        <td>说明</td>
    </tr>
    <tr>
        <td>type</td>
        <td>指定元素的类型，包括 text、password、checkbox、radio、submit、reset、file、hidden、image 和 button，默认为 text</td>
    </tr>
    <tr>
        <td>name</td>
        <td>指定表单元素的名称</td>
    </tr>
    <tr>
        <td>value</td>
        <td>元素的初始值。type 为 radio、checkbox 时必须指定一个值</td>
    </tr>
    <tr>
        <td>size</td>
        <td>指定表单元素的初始宽度。当 type 为 text 或 password 时，表单元素的大小以字符为单元。对于其他类型，宽度以像素为单位</td>
    </tr>
    <tr>
        <td>maxlength</td>
        <td>type 为 text 或 password 时，输入的最大字符数</td>
    </tr>
    <tr>
        <td>checked</td>
        <td>type 为 radio 或 checkbox 时，指定按钮是否被选中</td>
    </tr>
</table>

> 1. radio: 单选框标签，相同 name 的单选框为一组（即同一组里的单选框同时只能够选择一个，不同组的可以被同时选择）。radio 需要有 value
> 2. checkbox: 多选框标签，也必须要有 value 值。
> 3. button：按钮

```html
<p>性别：
        <label>
            <input type="radio" name="sex" value="boy"/>
        </label> 男
        <label>
            <input type="radio" name="sex" value="girl"/>
        </label> 女
    </p>

    <p>爱好:
        <label>
            <input type="checkbox" name="hobby" value="sleep"/>
        </label> 睡觉
        <label>
            <input type="checkbox" name="hobby" value="code"/>
        </label> 写代码
        <label>
            <input type="checkbox" name="hobby" value="eat">
        </label> 吃饭
    </p>

    <p>按钮:
        <input type="button" name="btn" value="按钮">
    </p>
```

### 列表框和文本域和文件域

**列表框**

```html
<p>下拉框
    <select name="nation">
        <option value="China">中国</option>
        <option value="Japan">日本</option>
        <option value="UK" selected>英国</option>
        <option value="USA">美国</option>        
    </select>
</p>
```

> selected 表示默认选择。

**文本域**

```html
    <p>反馈：
        <label>
<textarea name="textarea" cols="30" rows="5">
    文本内容
</textarea>
        </label>
    </p>
```

**文件域**

```html
<p>
    <input type="file" name="files"/>
    <input type="button" value="上传" name="upload"/>
</p>
```

### 搜索框和简单验证

**邮件验证**

```html
<p>邮箱：
    <!-- 仅仅是通过 @ 来做判断，是很简单的验证 -->
    <input type="email" name="email"/>
</p>
```

**URL 验证**

```html
<p>url：
    <input type="url" name="url"/>
</p>
```

**数字验证**

```html
<p>商品数量：
    <input type="number" name="num" max="100" min="0" step="10"/>
</p>
```

**滑块**

```html
<p>滑块
    <input type="range" min="0" max="100"/>
</p>
```

**搜索**

```html
<p>搜索：
    <input type="search" name="search"/>
</p>
```

### 表单初级验证

1. placeholder（输入框中的提示信息）
   ```html
   <input type="text" placeholder="请输入用户名" name="username"/>
   ```
2. required（非空要求）
   ```html
   <input type="text" name="username" required/>
   ```
3. pattern（正则表达式）
   ```html
   <!-- 邮箱自定义验证 -->
   <input type="text" name="mail" pattern="\w+([-+.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*"/>
   ```