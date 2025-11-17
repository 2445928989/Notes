## 如何引入外部CSS文件？

```html
<link href="main.css" rel="stylesheet" />
```

## 如何指定下载资源的默认名？

```html
<a
  href="https://download.mozilla.org/?product=firefox-latest-ssl&os=win64&lang=zh-CN"
  download="firefox-latest-64bit-installer.exe">
  下载最新的 Firefox 中文版 - Windows（64 位）
</a>
```

使用download属性指定即可

### 一些伪类

:hover 鼠标悬停

:link 超链接默认

:visited 访问过的超链接

:first-child 作为第一个子元素

:last-child 作为最后一个子元素

:only-child 作为没有兄弟元素的元素

:invalid 突出显示用户的字段错误

:focus 用键盘选中

:nth-child() 作为第x个子元素 ()内可以填even和odd

### 一些伪元素

::first-line 第一行

::before 会添加在选中元素之前

::after 添加在选中元素之后

### 组合选择器

: 后代选择器(滚木)

选择某元素的后代元素

+: 相邻兄弟选择器

两元素紧接，有同一父元素

\>: 子元素选择器

只选择一级子元素

~: 后续兄弟选择器

选取指定元素之后的兄弟元素

### 属性选择器

\[attr] 有attr属性的元素

\[attr=value] attr属性的值为value

\[attr~=value] attr属性的值有value

\[attr|=value] attr属性的值为value-.....

\[attr^=value] attr属性的值的开头为value字符串

\[attr$=value] attr属性的值的结尾为value字符串

\[attr*=value] attr属性的值包含value字符串

\[attr...... i] attr属性的值.... ，但大小写不敏感

### 将背景设为透明

```
background: linear-gradient(rgba(255, 255, 255, 0.5), rgba(255, 255, 255, 0.5)), url('image.jpg') center/cover no-repeat;
```

### border-box?

content-box实际高度：height+padding+border

border-box实际高度：height。

相当于直接指定height就能锁定box的宽高

### 背景图片不随滚动条改变而改变

```css
background-attachment: fixed;
```

### 平滑滚动条

```css
scroll-behavior: smooth;
```

### 引入外部JS文件不工作？

请把以下代码放到body的最后。肯定是在DOM对象加载好之前就跑script了

```html
<script src="script.js"></script>
```

### 为什么我用form提交的表单报错了？

有可能是你`post`的不是`JSON`格式，在你`app.js`里边加这个

```js
app.use(express.urlencoded({ extended: true }));
```

为什么