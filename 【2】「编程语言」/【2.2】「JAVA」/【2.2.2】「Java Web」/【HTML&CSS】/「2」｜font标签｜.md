## font 标签

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>font标签</title>
</head>
<body>
    <!--字体标签
     需求1：在网页上显示，我是自体标签，并修改字体为宋体，颜色为红色

     font标签是字体标签，他可以用来修改文本的字体、颜色、大小
   -->
    <font color ="red"  face="Arial" size="11"> 我是字体标签</font>
</body>
</html>
```



## 特殊标签

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>特殊标签</title>
</head>
<body>

    <!-- 特殊字符
     需求1：把<br>换行标签 变成文本 转换成字符显示在页面上

     常用的特殊字符：
        <       ===>>>    &lt;
        >       ===>>>    &gt;
        空格     ===>>>    &nbsp;
     -->
    我是&lt;br&gt;标签<br/>
    我好&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;帅啊！
</body>
</html>
```



## 标题标签

 ```html
 <!DOCTYPE html>
 <html lang="en">
 <head>
     <meta charset="UTF-8">
     <title>标题标签</title>
 </head>
 <body>
     <!--标题标签
      需求1：演示标题1到标题6
 
 				align属性是对齐属性
      -->
     <h1 align="left">标题1</h1>
     <h2 align="center">标题2</h2>
     <h3 align="right">标题3</h3>
     <h4>标题4</h4>
     <h5>标题5</h5>
     <h6>标题6</h6>
 
 
 </body>
 </html>
 ```



## 超链接

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <!-- a标签是 超链接
        href属性设置链接的地址
        target属性设置那个目标进行跳转
           _self        表示当前页面
           _blank       表示打开新页面进行跳转
    -->
    <a href = "3.标题标签.html">百度</a>
    <br/><hr/>
    <a href = "http://www.baidu.com">百度</a>
    <br/><hr/>
    <a href = "http://www.baidu.com" target="_blank">百度</a>
    <br/><hr/>
    <a href = "http://www.baidu.com" target="_self">百度</a>

</body>
</html>
```



## 列表标签

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <!--需求1：使用无序，列表方式，把东北F4，赵四，刘能、小沈阳、宋小宝，展示出来
        ul 是无序列表
            type属性可以修改列表项前面的符号
        li 是列表项
    -->

    <ul type="none">
        <li>赵四</li>
        <li>刘能</li>
        <li>小沈阳</li>
        <li>宋小宝</li>
    </ul>

    <ol>
        <li>赵四</li>
        <li>刘能</li>
        <li>小沈阳</li>
        <li>宋小宝</li>
    </ol>

</body>
</html>
```



## img标签

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>img标签</title>
</head>
<body>
    <!--需求1：使用img标签显示一张美女的照片。并修改宽高，和边框属性

        img标签是图片标签，用来显示图片
            src属性可以设置图片的路径
            alt属性设为当图片没找到时的内容

        JavaSE中路径也分为相对路径和绝对路径，
            相对路径：从工程名开始算

            绝对路径：盘符：/目录/文件名

        web中路径分为相对路径和绝对路径两种
            相对路径：
                .       表示当前文件所在的目录
                ..      表示当前文件所在的上一级目录
                文件名   表示当前文件所在目录的文件，相当于 ./文件名

            绝对路径：
                正确格式是：http://ip:port/工程名/资源路径
    -->
    <img src="../image/image1.JPG" width="150" height="200" border="20" alt="照片没找到"/>
    <img src="../image/image1.JPG" width="150" height="200" border="20"/>
    <img src="../image/image1.JPG" width="150" height="200" border="20"/>
    <img src="../image/image1.JPG" width="150" height="200" border="20"/>

</body>
</html>
```















