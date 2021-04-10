# 第一部分：HTML5+CSS3

## 1、父元素和子元素宽高不固定，如何实现水平垂直居中

**第一种方法**

```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        * {
            padding: 0;
            margin: 0;
        }

        html,
        body {
            width: 100%;
            height: 100%;
        }

        body {
            background-color: azure;
        }

        .parent {
            width: 100%;
            height: 100%;
            background-color: orangered;
            text-align: center;
        }

        .child {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            /* 使用css3的transform来实现 */
        }

        .child p {
            width: 300px;
            height: 400px;
            background-color: blue;
        }
    </style>
</head>

<body>
    <div class="parent">
        <div class="child">
            <p></p>
        </div>
    </div>
</body>

</html>
```

![](https://ftp.bmp.ovh/imgs/2021/02/50abace27a301ab5.png)

第二种方法  flex

```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
       *{
        padding: 0;
        margin: 0;
    }
    body,html{
        width: 100%;
        height: 100%;
    }
    .parent{
        display: flex;
        justify-content: center;
        align-items: center;
        width: 100%;
        height:100%;
        background: #FD0C70;
    }
    .parent .child{
        color:#fff;
        width: 200px;
        height: 300px;
        text-align: center;
        background-color: yellow;
    }
    </style>
</head>

<body>
    <div class="parent">
        <div class="child">hello world</div>
    </div>
</body>

</html>
```

![](https://ftp.bmp.ovh/imgs/2021/02/bc9339a0a8bd6e3c.jpg)



## 2、分别实现骰子中的'一点' 和 '三点' 的布局。

**一点**



```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
     .m-container{margin: 5px; padding: 5px; width:100px;height: 100px;border:1px solid green;}
        .m-item {margin: 5px; width: 20px;height: 20px;border-radius: 10px;background-color: black;}


        .box4 {display: flex;justify-content: center;align-items: center;}  /*水平垂直居中*/
    </style>
</head>

<body>
 
<div class="m-container box4">
    <span class="m-item"></span>
  </div>
</body>

</html>
```



![](https://ftp.bmp.ovh/imgs/2021/02/f6d5a9e95d15dc3e.png)











三点

```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
       .box{
        width: 200px;
        height: 200px;
        padding: 20px;
        display: flex;
        flex-direction: row;
        justify-content: space-between; /*设置两端对齐*/
        border: 2px solid #ccc;
        border-radius: 10px;
    }
    .item{
        width: 40px;
        height: 40px;
        border-radius: 50%;
        background-color: #666;
    }
    .item:nth-child(2){
        align-self: center; /*垂直方向居中*/
    }
    .item:nth-child(3){
        align-self: flex-end; /*垂直方尾对齐*/
    }
    </style>
</head>

<body>
    <div class="box">
        <div class="item"></div>
        <div class="item"></div>
        <div class="item"></div>
    <div>
</body>

</html>
```



![](https://ftp.bmp.ovh/imgs/2021/02/b258a354b1f371a5.png)













## 3、简述选择器~和+的区别。

**最大的区别是’+’最多只能匹配到一个元素，而’~’可以匹配到多个。**
**‘+’是指紧跟在后面的某同级元素**
**‘~’是指某些同级元素**
**以以下代码为例**

```
<div class='try'></div>
<p class='one'></p>
<p class='two'></p>
123
```

如果是+

```
.try + p
1
```

那么选中的只是类名为one的p

如果是~

```
.try ~ p
1
```

那么选中的是类名为one和类名为two的p

```
<div class='try'></div>
<h2></h2>
<p class='one'></p>
<p class='two'></p>
1234
```

那么

```
.try + p
1
```

则什么也选择不到

```
.try ~ p
1
```

则没有影响



## 4、简述box-sizing的有效值以及所对应的盒模型规则。

box-sizing  属性允许您以特定的方式定义匹配某个区域的特定元素。

语法：box-sizing: content-box|border-box|inherit;

1）box-sizing:content-box；这是由  CSS2.1  规定的宽度高度行为。宽度和高度分别应用到元素的内容框。在宽度和高度之外绘制元素的内边距和边框。是默认值。如果你设置一个元素的宽为100px，那么这个元素的内容区会有100px  宽，并且任何边框和内边距的宽度都会被增加到最后绘制出来的元素宽度中

2）box-sizing:border-box；为元素指定的任何内边距和边框都将在已设定的宽度和高度内进行绘制。告诉浏览器去理解你设置的边框和内边距的值是包含在width内的。也就是说，如果你将一个元素的width设为100px,那么这100px会包含其它的border和padding，内容区的实际宽度会是width减去border  + padding的计算值。大多数情况下这使得我们更容易的去设定一个元素的宽高。

3）box-sizing:inherit;；规定应从父元素继承  box-sizing 属性的值


## **5、flex中元素的margin是否会合并？**

不会合并

## 6、简述align-items和align-content的区别。

align-items属性适用于所有的flex容器，它是用来设置每个flex元素在交叉轴上的默认对齐方式。如果是多行多行容器，多行和多行之间是有间距的。

align-content有相同的功能，但是align-content属性只适用于多行的flex容器，有一个重点就是多行，并且align-content在对齐的过程中，如果是多行多行容器，多行和多行之间的间距是没有的。

## 7、简述data:属性的用法（如何设置，如何获取），有何优势？

data-*  的值的获取和设置，2种方法：

**1）传统方法**

getAttribute()  获取data-属性值；

setAttribute()  设置data-属性值

2)HTML5新方法

例如 data-href

dataset.href  获取data-href属性值

dataset.href  = 'http://baidu.com'  设置data-href属性值

传统方法无兼容性问题，但是不够优雅、方便

HTML5新方法很优雅、方便，但是有兼容性问题，可以在移动端开发或不支持低版本浏览器的项目中使用

**优势**：自定义的数据可以让页面拥有更好的交互体验（不需要使用 Ajax 或去服务端查询数据）。

## 8、简述 title与h1的区别，b与strong的区别，i与em的区别。 

title与h1的区别:  

定义：title是网站标题，h1是文章主题

作用：title概括网站信息，可以直接告诉搜索引擎和用户这个网站是关于什么主题和内容的，是显示在网页Tab栏里的；h1突出文章主题，面对用户，是显示在网页中的.

 b与strong的区别:  

从视觉上效果观看b与strong是没有区别的,从单词的语义也可以分析得出，b是Bold(加粗)的简写，所以这个B标记所传达的意思只是加粗，没有任何其它的作用，而Strong我们从字面理解就可以知道他是强调的意思，所以我们用这个标记向浏览器传达了一个强调某段文字的消息，他是强调文档逻辑的，并非是通知浏览器应该如何显示。

3)i与em的区别:同样，I是Italic(斜体)，而em是emphasize(强调)。

## 9、什么是标准文档流

标准文档流：网页在解析的时候，遵循于从上向下，从左向右的一个顺序，而这个顺序就是来源于标准文档流。

标准文档流等级，分为两种等级：块级元素和行内元素；

块级元素：

1).霸占一行，不能与其他任何元素并列

2).能接受宽、高

3).如果不设置宽度，那么宽度将默认变为父亲的100%，即和父亲一样宽

行内元素：

1).与其他元素并排

2).不能设置宽、高。默认的宽度就是文字的宽度

## 10、z-index是什么？在position的值什么时候可以触发?

z-index  属性设置元素的堆叠顺序。拥有更高堆叠顺序的元素总是会处于堆叠顺序较低的元素的前面，当脱离文档流内容较多，并且相互重叠的时候，就有可能发生本想完全显示的内容被其他内容遮挡的结果，这时我们就需要人为指定哪个层在上面，哪个在下面，z-index属性就是干这个用的。注意：Z-index  仅能在定位元素上奏效.

在position的值是relative、absolute、fixed、sticky时候可以触发

## 11、CSS3 如何实现圆角边框

```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
    .demo{
    margin:0 auto;
    background: darkcyan;
    width:200px;
    height:200px;
    border:2px solid darkslategray;
    border-radius:30px;
    text-align: center;
    line-height: 200px;
}
    </style>
</head>

<body>
 
    <div class="demo">圆角边框</div>
</body>

</html>
```

![](https://ftp.bmp.ovh/imgs/2021/02/8ab6261fd41c7643.png)

## 12、HTML5有哪些缓存方式

**总体情况**


h5之前，存储主要是用cookies。cookies缺点有在请求头上带着数据，大小是4k之内。主Domain污染。
主要应用：购物车、客户登录
对于IE浏览器有UserData，大小是64k,只有IE浏览器支持。



**目标**

解决4k的大小问题
解决请求头常带存储信息的问题
解决关系型存储的问题
跨浏览器



**1.本地存储localstorage**

**存储方式：**
以键值对(Key-Value)的方式存储，永久存储，永不失效，除非手动删除。

**大小：**
每个域名5M

**支持情况：**
**常用的API：**

getItem //取记录

setIten//设置记录

removeItem//移除记录

key//取key所对应的值

clear//清除记录



**存储的内容：**

数组，图片，json，样式，脚本。。。（只要是能序列化成字符串的内容都可以存储）



2.本地存储sessionstorage


HTML5 的本地存储 API 中的 localStorage 与 sessionStorage 在使用方法上是相同的，区别在于 sessionStorage 在关闭页面后即被清空，而 localStorage 则会一直保存。



3.离线缓存（application cache）


本地缓存应用所需的文件
**使用方法：**
①配置manifest文件
页面上：
<!DOCTYPE HTML>
<html manifest="demo.appcache">
...
</html>

**Manifest 文件：**

manifest 文件是简单的文本文件，它告知浏览器被缓存的内容（以及不缓存的内容）。

manifest 文件可分为三个部分：

①*CACHE MANIFEST* – 在此标题下列出的文件将在首次下载后进行缓存

②*NETWORK* – 在此标题下列出的文件需要与服务器的连接，且不会被缓存

③*FALLBACK* – 在此标题下列出的文件规定当页面无法访问时的回退页面（比如 404 页面）

**完整demo：**


CACHE MANIFEST
\# 2016-07-24 v1.0.0
/theme.css
/main.js

NETWORK:
login.jsp

FALLBACK:
/html/ /offline.html

**服务器上：**manifest文件需要配置正确的MIME-type，即 “text/cache-manifest”。

如Tomcat:


<mime-mapping>
<extension>manifest</extension>
<mime-type>text/cache-manifest</mime-type>
</mime-mapping>

**常用API：**

核心是applicationCache对象，有个status属性，表示应用缓存的当前状态：

0（UNCACHED） : 无缓存， 即没有与页面相关的应用缓存

1（IDLE） : 闲置，即应用缓存未得到更新

2 （CHECKING） : 检查中，即正在下载描述文件并检查更新

3 （DOWNLOADING） : 下载中，即应用缓存正在下载描述文件中指定的资源

4 （UPDATEREADY） : 更新完成，所有资源都已下载完毕

5 （IDLE） : 废弃，即应用缓存的描述文件已经不存在了，因此页面无法再访问应用缓存

**相关的事件：**

表示应用缓存状态的改变：

checking : 在浏览器为应用缓存查找更新时触发

error : 在检查更新或下载资源期间发送错误时触发

noupdate : 在检查描述文件发现文件无变化时触发

downloading : 在开始下载应用缓存资源时触发

progress：在文件下载应用缓存的过程中持续不断地下载地触发

updateready : 在页面新的应用缓存下载完毕触发

cached : 在应用缓存完整可用时触发

**Application Cache的三个优势：**



① 离线浏览

② 提升页面载入速度

③ 降低服务器压力

**注意事项：**

\1. 浏览器对缓存数据的容量限制可能不太一样（某些浏览器设置的限制是每个站点 5MB）
\2. 如果manifest文件，或者内部列举的某一个文件不能正常下载，整个更新过程将视为失败，浏览器继续全部使用老的缓存
\3. 引用manifest的html必须与manifest文件同源，在同一个域下
\4. 浏览器会自动缓存引用manifest文件的HTML文件，这就导致如果改了HTML内容，也需要更新版本才能做到更新。
\5. manifest文件中CACHE则与NETWORK，FALLBACK的位置顺序没有关系，如果是隐式声明需要在最前面
\6. FALLBACK中的资源必须和manifest文件同源
\7. 更新完版本后，必须刷新一次才会启动新版本（会出现重刷一次页面的情况），需要添加监听版本事件。
\8. 站点中的其他页面即使没有设置manifest属性，请求的资源如果在缓存中也从缓存中访问
\9. 当manifest文件发生改变时，资源请求本身也会触发更新

[点我参考更多资料！](https://link.zhihu.com/?target=http%3A//www.cnblogs.com/lovesong/p/5021992.html%3Futm_medium%3Dreferral)

**离线缓存与传统浏览器缓存区别：**

\1. 离线缓存是针对整个应用，浏览器缓存是单个文件

\2. 离线缓存断网了还是可以打开页面，浏览器缓存不行

\3. 离线缓存可以主动通知浏览器更新资源



4.Web SQL


关系数据库，通过SQL语句访问
Web SQL 数据库 API 并不是 HTML5 规范的一部分，但是它是一个独立的规范，引入了一组使用 SQL 操作客户端数据库的 APIs。

**支持情况：**
Web SQL 数据库可以在最新版的 Safari, Chrome 和 Opera 浏览器中工作。



**核心方法：**

**①openDatabase**：这个方法使用现有的数据库或者新建的数据库创建一个数据库对象。

②**transaction**：这个方法让我们能够控制一个事务，以及基于这种情况执行提交或者回滚。

③**executeSql**：这个方法用于执行实际的 SQL 查询。

## 13、CSS3新增伪类有那些

```
p:last-of-type     选择其父元素的最后的一个P元素

p:last-child       选择其父元素的最后子元素(一定是P才行)

 

p:first-of-type     选择其父元素的首个P元素

p:first-child      选择其父元素的首个p元素(一定是p才行)

 

p:only-child　　　　选择其父元素的只有一个元素(而且这个元素只能是p元素，不能有其他元素)

p:only-of-type　　　选择其父元素的只有一个p元素(不能有第二个P元素，其他元素可以有)　

 

**选第N个**

p:nth-child(n)   　选择其父元素的第N个 刚好是p的元素

p:nth-last-child(n)   ..............................................从最后一个子元素开始计数

p:nth-of-type(n)　  选择其父元素的n个元素

p:nth-last-of-type(n)  ........................从最后一个子元素开始计数
```



## 14、CSS实现水平/垂直水平居中

css的居中，可以分为水平居中和垂直居中，实现居中的方式有以下几种：

**1.text-align:center**  块状元素，水平居中

**2.margin:0 auto** **水平居中**  以外边框为参照物，上下外边框距为0，左右外边框距浏览器会自动计算平分

**3.line-height** **垂直居中**   通过设置行间距。line-height只适用于单行文本的元素，多行元素不适用。

4.**float属性+relative定位**  给父元素设置float：left ,然后父元素整体向左移动50%（position:relative;left:50%），这时父元素要清除浮动（clear：both）,然后再给子元素整体向左移动50%(position:relative;left:-50%)

**5.根据绝对定位absolute+margin实现水平居中**  给父元素一个相对定位relative，利用绝对定位absolute，配合margin：auto自动计算外边距（position: absolute; top: 0;right: 0; bottom: 0; left: 0;margin: auto;）。相对于相对应于relative的绝对定位absolute，需要定宽。relative只是为了给子元素定位用的

**6.使用absolute绝对定位+translate 移动转换，实现水平垂直居中**   使用百分比来绝对定位一个元素，并配合使用translate，将元素移动定位居中（position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%)）。

**7.利用table布局，默认垂直居中（vertical-align:middle）** 如果不是table布局的话可以：**仿table（display：table-cell）**,一样使用vertical-align:middle实现居中对齐

**8.flex布局**  父元素使用display：flex,设置其属性justify-content.align-items都为center(display: flex; justify-content: center; align-items: center),实现水平居中

**9.flex布局+margin**  父元素使用flex布局（display：flex）,子元素使用margin：auto

 

## 15、简述一下src 与href的区别 

href 是指向bai网络资源所在位置，建立和当du前元zhi素（锚点）或当前文档dao（链接）之间的链接，zhuan用于超链接。
src是指向外shu部资源的位置，指向的内容将会嵌入到文档中当前标签所在位置；在请求src资源时会将其指向的资源下载并应用到文档内，例如js脚本，img图片和frame等元素。
当浏览器解析到该元素时，会暂停其他资源的下载和处理，直到将该资源加载、编译、执行完毕，图片和框架等元素也如此，类似于将所指向资源嵌入当前标签内。这也是为什么将js脚本放在底部而不是头部。

## 16、什么是CSS hack

CSS hack 是CSS中的一bai种作弊手段，因为目前du所有浏览器并没有统一对zhiCSS的支持，例如同样dao是一个margin:1px，可zhuan能在不同的浏览器中出现的效shu果就一定是1px
在这种情况下，我们只能借助于CSS hack来暂时过渡这个阶段，CSS hack就比如是IE能认出的CSS语句，而FF不能认出，这样就能达到我们的目的了，css hack在很多高级的技巧中出现，不过并不是很推荐使用，毕竟未来的css趋势还不是很明了，尽量避免吧
因为很多css错位问题其实并不是浏览器的错误，而是我们本身的编辑出现的错误。

## 17、什么叫做优雅降级和渐进增强？

**一.背景介绍：**

渐进增强和优雅降级是在CSS3出现之后才火起来的。由于低级浏览器，（特）比（别）如（是）IE6等，不支持CSS3的浏览器，但是CSS3的样式又特别优秀不忍放弃 ，所以在高级浏览器中应用CSS3样式，在低级浏览器只保证基本功能。

“渐进增强”是被Steven Champeon创造于2003年3月11日在奥斯汀的SXSW互动大会，并于2003年3月和6月间通过一系列Webmonkey教程文章的公布。 （我找了半天资料，翻了下英文文章也只找到这段历史，无奈脸），谷歌公司是很支持这种开发方式的啊，我认为渐进增强不仅是一个前端开发思想，更加是一个程序开发思想。

 

**二.知识剖析**

1.什么是渐进增强

在网页开发中，渐进增强认为应该专注于内容本身。一开始针对低版本的浏览器构建页面，满足最基本的功能，再针对高级浏 览器进行效果，交互，追加各种功能以达到更好用户体验,换句话说，就是以最低要求，实现最基础功能为基本，向上兼容。以css为例，以下这种写法就是渐进增强。

![img](http://upload-images.jianshu.io/upload_images/5837348-c8b4e503e92f5eaa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 

2.什么是优雅降级

在网页开发中，优雅降级指的是一开始针对一个高版本的浏览器构建页面，先完善所有的功能。然后针对各个不同的浏览器进行测试，修复，保证低级浏览器也有基本功能 就好，低级浏览器被认为“简陋却无妨 (poor, but passable)” 可以做一些小的调整来适应某个特定的浏览器。但由于它们并非我们所关注的焦点，因此除了修复较 大的错误之外，其它的差异将被直接忽略。也就是以高要求，高版本为基准，向下兼容。同样以css为例，优雅降级的写法如下。

![img](http://upload-images.jianshu.io/upload_images/5837348-a8d0316bc2316a68.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 

3.二者区别

1.）如果你采用渐进增强的开发流程，先做一个基本功能版，然后针对各个浏览器进行渐进增加，增加各种功能。相对于优雅降级来说，开发周期长，初期投入资金大。 你想一下不可能拿个基本功能版给客户看呀，多寒酸，搞不好一气之下就不找你做了，然后就炸了。但是呢，也有好处，就是提供了较好的平台稳定性，维护起来资金小， 长期来说降低开发成本。

2.）那采用优雅降级呢，这样可以在较短时间内开发出一个只用于一个浏览器的完整功能版，然后就可以拿给PM找客户谈呀，可以拿去测试，市场试水呀，对于功能尚未确定的 产品，优雅降级不失为一种节约成本的方法。

从技术出发

前缀CSS3（-webkit- / -moz- / -o-*）和正常CSS3在浏览器中的支持情况是这样的：

1.很久以前：浏览器前缀CSS3和正常CSS3都不支持；

2.不久之前：浏览器只支持前缀CSS3，不支持正常CSS3；

3.现在：浏览器既支持前缀CSS3，又支持正常CSS3；

4.未来：浏览器不支持前缀CSS3，仅支持正常CSS3.

.transition { /*渐进增强写法*/

-webkit-border-radius:30px 10px;

-moz-border-radius:30px 10px;

border-radius:30px 10px;

}

.transition { /*优雅降级写法*/

border-radius:30px 10px;

-moz-border-radius:30px 10px;

-webkit-border-radius:30px 10px;

}



 

**三.常见问题**

渐进增强和优雅降级如何抉择

 

四.解决方案

如果开发时间充裕，开发资金到位，就不存在抉择这个问题了，肯定是渐进增强好呀！

但是现实往往很骨感。

这个时候，就要看你开发产品的受众，受众使用的客户端。如果受众年龄覆盖面广，客户端从移动到平板到电脑，比如淘宝这种页面，那没得选，老老实实选渐进增强。

如果受众客户端单一，例如一个移动端页面，再渐进增强PC端就没那么重要了。

各有优势，但现实是绝大多数公司采用的都是渐进增强方式，毕竟业务优先，体验不会凌驾在业务上。

## 18、移动端适配怎么做？

**一、meta viewport（视口）**

移动端初始视口的大小为什么默认是980px？

因为世界上绝大多数PC网页的版心宽度为 980px ，如果网页没有专门做移动端适配，此时用手机访问网页旁边刚好没有留白，不过页面缩放后文字会变得非常小。

为了解决页面缩放的体验问题，在网页代码的头部，加入一行viewport元标签。

<meta name="viewport" content="width=device-width, initial-scale=1">​这里的device-width告诉浏览器，将视口的宽度设置为设备宽度（这个宽度是人为预设的，不设的话就是980px）。
属性含义



```undefined
    initial-scale：第一次进入页面的初始比例
    minimum-scale：允许缩小最小比例
    maximum-scale：允许放大最大比例
    user-scalable：允许使用者缩放，1 or 0 (yes or no)
```

**二、图片适配**

`img { max-width: 100%; }`
 此时图片会自动缩放，同时​图片最大显示为其自身的100%（即最大只可以显示为自身那么大）
 `为什么不用 img { width: 100%; } ？`
 当容器大于图片宽度时，图片会无情的拉伸变形

**三、媒体查询**

为什么要媒体查询？

针对不用的设备提前为网页设定各种 CSS 样式CSS3中的Media Query模块，自动检测屏幕宽度，然后加载相应的CSS文件

语法举例



```css
@media screen and (min-width:1200px){
        body{        
            background-color: red; 
        }
```

当屏幕宽度大于1200px时，背景色变为红色

**四、动态 rem 方案**

为什么要用rem？

和媒体查询配合，实现响应式布局

px、em、rem 有什么不同？

px是pixel（像素），是屏幕上显示数据的最基本的点，在HTML中，默认的单位就是px；em 是一个相对大小，相对于父元素font-size的百分比大小rem 是相对于根元素<html>的font-size

用法示例

1、首先在HTML中设置一个基准值，如font-size: 100px;

2、将像素值除以100（设定的基准值）计算出对应的rem的值

如果一个div的宽度为600*300px ,改为rem即为6rem*3rem

3、根据当前屏幕的宽度和设计稿的宽度来计算此时的HTML的font-size的值

当前手机屏幕宽度为375px，设计稿宽度为640px，375/640*100->fontsize=58.59375



## 19、请问苹果原生浏览器中默认样式如何清除,例如input默认样式？

添加baicss样式:
input[type="button"], input[type="submit"], input[type="reset"] {
-webkit-appearance: none;
}
textarea { -webkit-appearance: none;}
然后自己du定义样式，zhi美化dao修改就好了zhuan。

## 20、CSS清除浮动的方法

为什么会有 Floats

最初，引入 float 属性是为了能让 web 开发人员实现简单的布局，包括在一列文本中浮动的图像，文字环绕在它的左边或右边。但 大家很快意识到，它可以浮动任何东西，而不仅仅是图像，所以浮动的使用范围扩大了。

当我们在网页各处使用 float 的时候，也由此引发的一些副作用，如：父元素塌陷、元素重叠，清除浮动也就是清除这些副作用。



发生了什么

当一个元素被添加float:right等属性的时候发生了什么？

1. float 属性可以使一个元素脱离正常的文档流（normal flow），然后被安放到它所在容器的的左端或者右端，并且其他的文本和行内元素环绕它。

2. float 使用了块状布局，所以元素的 display 会被改变



而 float 元素脱离了 normal flow 就相当于进入了一个平行空间，不再与其后边及父级块级元素发生反应，这一特性使得 float 元素无法撑起父元素的高度，导致父元素塌陷。

第一点中还提到其他的文本和行内元素环绕 float元素，但是 float 后的块级元素会与其发生重叠。



怎么解决

为了解决 float 属性引发的问题，我们在 CSS 中引入了clear:left|right|both属性，其作用分别是在元素的 左侧|右侧|两侧 不允许存在 float 元素，我们可以用他来清除其副作用。
利用 clear 属性我们有两种方法来清除浮动。





1. 为 float 元素后的元素添加 clear 属性

其效果如下：
未添加

![](https://ftp.bmp.ovh/imgs/2021/02/d5ab7d2bab0b0274.png)





添加后

![](https://ftp.bmp.ovh/imgs/2021/02/77107f5570a27d32.png)





2. 利用伪元素

![](https://ftp.bmp.ovh/imgs/2021/02/465f99346ab46e8a.png)



有时我们会遇到上图的情况，float 元素后没有其他元素了，这时该怎么办？固然我们可以在其后添加一个空div，然后像1中一样解决问题，但这种方式并不优雅，伪元素这时候就派上用场了，我们可以写一个.clearfix 工具样式，当给需要清除浮动时，就为其加上这个类。效果如下：

![](https://ftp.bmp.ovh/imgs/2021/02/e2aadae97b048cea.png)





3. 修改父元素的 owerflow 属性

将父元素的 owerflow 属性修改为 owerflow:auto|hidden



这就是三种最主流的方式  

## 21、PC端常用的布局方法



```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title></title>
        <style type="text/css">
            /* 宽度适配 */
            html,
            body {
                width: 100%;
                overflow-x: hidden;/* 外层盒子设置最小宽度的话看不到横向滚动条 */
            }

            /*1. pc端适配的需求：目前我们pc项目的设计稿尺寸是宽度1920，高度最小是1080。
            2.放大或者缩小屏幕，网页可以正常显示 */
            /* 一、两列布局 */
            /* 1.左定宽 右边自适应 或者 右边定宽左边自适应 */
            .content{
                width: 1200px; /* 主容器 */
                min-width: 960px;
                margin: 0 auto;
                background: #fff;
            }
            .left {
                float: left;
                width: 200px;/* 定宽 */
                background: #ccc;
                height: 800px;/* 测试设了一个高度和背景（为了更好看效果） */
            }

            .right {
                margin-left: 100px;
                background: #999;
                height: 800px;/* 测试设了一个高度和背景（为了更好看效果） */
            }
        </style>
    </head>
    <body>
        <div class="content">
            <div class="left">左边</div>
            <div class="right">右边</div>
        </div>
    </body>
</html>
```

![](https://ftp.bmp.ovh/imgs/2021/02/d57596a337b1781c.png)





```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title></title>
        <style type="text/css">
            /* 宽度适配 */
            html,
            body {
                width: 100%;
                overflow-x: hidden;
                /* 外层盒子设置最小宽度的话看不到横向滚动条 */
            }
            /* 一、三列布局 */
            /* 1.左右定宽中间自适应 */
            .content {
                width: 1200px;
                /* 主容器 */
                min-width: 960px;
                margin: 0 auto;
                background: firebrick;/* 测试设了一个背景（为了更好看效果） */
                display: table;
            }

            .left {
                width: 100px;
                /* 定宽 */
                background: #ccc;
                height: 800px;
                /* 测试设了一个高度和背景（为了更好看效果） */
            }
            .right {
                width: 100px;
                /* 定宽 */
                background: fuchsia;
                height: 800px;
                /* 测试设了一个高度和背景（为了更好看效果） */
            }

            .left,
            .right,
            .center {
                display: table-cell;
            }
        </style>
    </head>
    <body>
        <div class="content">
            <div class="left">左边</div>
            <div class="center">中间</div>
            <div class="right">右边</div>
        </div>
    </body>
</html>
```



![](https://riyugo.com/i/2021/02/21/12mawg6.png)





```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>实现三栏水平布局之双飞翼布局</title>
        <style type="text/css">
            .container {
                width: 1200px;
                /* 主容器 */
                min-width: 960px;
                margin: 0 auto;
                background: #ccc;
                /* 测试设了一个背景（为了更好看效果） */
            }

            .left,
            .center,
            .right {
                float: left;
                min-height: 400px;
                /* 测试更好观看效果 统一高度*/
                text-align: center;
            }

            .left {
                margin-left: -100%;
                background: #0000FF;
                width: 200px;
                /* 定宽 */
            }

            .right {
                margin-left: -300px;
                background-color: #FF0000;
                width: 300px;
                /* 定宽 */
            }

            .center {
                background-color: #f2f1f1;
                width: 100%;
            }

            .content {
                margin: 0 300px 0 200px;
            }
        </style>
    </head>
    <body>
        <div class="container">
            　　<div class="center">
                　　<div class="content">中间自适应</div>
            　  </div>
            　　<div class="left">左边</div>
            　　<div class="right">右边</div>
        </div>
    </body>
</html>
```





![](https://riyugo.com/i/2021/02/21/12n1c19.png)









```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>实现三栏水平布局之圣杯布局</title>
        <style type="text/css">
            .container {
                width: 1200px;
                /* 主容器 */
                min-width: 960px;
                margin: 0 auto;
                background: #ccc;/* 测试设了一个背景（为了更好看效果） */
                padding: 0 300px 0 200px;
            }

            .left,
            .center,
            .right {
                position: relative;
                min-height: 200px;
                float: left;
            }

            .left {
                left: -200px;
                margin-left: -100%;
                background: green;/* 测试设了一个背景（为了更好看效果） */
                width: 200px;
            }

            .right {
                right: -300px;
                margin-left: -300px;
                background: red;/* 测试设了一个背景（为了更好看效果） */
                width: 300px;
            }

            .center {
                background: blue;/* 测试设了一个背景（为了更好看效果） */
                width: 100%;
            }
        </style>
    </head>
    <body>
        <div class="container">
            　　<div class="center">center</div>
            　　<div class="left">left</div>
            　　<div class="right">right</div>
        </div>
        <div class="tip_expand">双飞翼布局比圣杯布局多创建了一个div，但不用相对布局了</div>
    </body>
</html>
```

![](https://riyugo.com/i/2021/02/21/12nn9gc.png)



```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>实现三栏水平布局-Flex布局</title>
        <style type="text/css">
            .container {
                display: flex;
                width: 1200px;
                /* 主容器 */
                min-width: 960px;
                margin: 0 auto;
                background: #ccc;
                /* 测试设了一个背景（为了更好看效果） */
                min-height: 800px;
                font-size: 0; /* 间隙处理 */
            }

            .main {
                flex-grow: 1;
                background-color: blue;
                font-size: 24px;
            }

            .left {
                order: -1;/* 对于order属性：定义项目的排列顺序，越小越靠前，默认为0。 */
                flex-basis: 200px;/* 通过项目属性flex-basis 设置left和right的固定宽度 */
                background-color: green;
                font-size: 24px;
            }

            .right {
                flex-basis: 300px;/* 通过项目属性flex-basis 设置left和right的固定宽度 */
                background-color: red;
                font-size: 24px;
            }
        </style>
    </head>
    <body>
        <div class="container">
            　　<div class="main">main</div>
            　　<div class="left">left</div>
            　　<div class="right">right</div>
        </div>
    </body>
</html>
```

![](https://riyugo.com/i/2021/02/21/12o5tc7.png)



## 22、布局左边20%，中间自适应，右边200px，不能用定位

**左右两列200px,中间列自适应
伸缩盒布局**

**思路：**

 

```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>布局:左右两列200px,中间列自适应（一）</title>
    <style type="text/css">
       body {
            margin: 0;
            display: flex;
        }

        .left {
            width: 20%;
            height: 200px;
            background: red;
        }

        .center {
            height: 400px;
            background: blue;
            flex-grow: 1;
        }

        .right {
            width: 200px;
            height: 600px;
            background: pink;
        }
    </style>
</head>

<body>
    <div class='left'>left</div>
    <div class='center'>center</div>
    <div class='right'>right</div>
    
    
    

   


</body>

</html>
```





![](https://riyugo.com/i/2021/02/21/1308wks.png)







## 23、行内元素和块级元素？img算什么？行内元素怎么转化为块元素？

**1.行内元素 一个行内元素只占据它对应标签的边框所包含的空间**。****

**2.块级元素 块级元素占据其父元素(容器)的整个空间,因此创建了一个“块”**。 



***\*img是什么元素？\****

应是行内元素，判断一个元素是行内元素，还是块元素，无非就是看它是否是独占一行。img标签显然没有独占一行，固它是行内元素。这没有问题。

***\*既然img是行内元素，那为什么可以通过CSS设置框高呢？\****

尽管img是行内元素，但同时它也是置换元素，置换元素一般内置框高属性，因此可以设置其框高。

<img src="/i/mouse.jpg" height="200" width="200"  />

**那么什么又是“置换元素”呢？**

置换元素就是会根据标签属性来显示的元素。反之就是非置换元素了。

比如img根据src属性来显示，input根据value属性来显示，因此可知道img和input是置换元素，当然同理textarea, select,也是置换元素；



行内如何转块级元素 



```
以下汇总三种行内元素转为块级元素的方法：

（1）display

（2）float

（3）position（absolute和fixed）

代码

 1 <!DOCTYPE html>
 2 <html lang="zh">
 3 <head>
 4     <meta charset="UTF-8" />
 5     <meta http-equiv="X-UA-Compatible" content="ie=edge" />
 6     <title>行内元素转为块级元素</title>
 7     <style type="text/css">
 8         *{
 9             margin: 0;
10             padding: 0;
11             font-size: 20px;
12             color: red;
13         }
14         div{
15             margin-top: 20px;
16         }
17         a{
18             display:block;
19         }
20         span{
21             float: left;            
22         }
23         i{
24             position:absolute;
25         }
26     </style>
27 </head>
28 <body>
29     <div>
30         方法一：使用display
31     </div>
32     <a href="#">a标签转为块级元素</a>
33     <div>
34         方法一：使用float
35     </div>
36     <span>span标签转为块级元素</span>
37     <div>
38         方法一：使用position（absolute和fixed）
39     </div>
40     <i>i标签转为块级元素</i>
41 </body>
42 </html>
```



## 24、将多个元素设置为同一行?清除浮动的几种方式？

```
1.将多个元素设置为同一行：float，inline-block

清除浮动的方式：

方法一：添加新的元素 、应用 clear：both；

方法二：父级div定义 overflow: hidden；

方法三：利用:after和:before来在元素内部插入两个元素块，从面达到清除浮动的效果。

.clear{zoom:1;}

.clear:after{content:””;clear:both;display:block;height:0;overflow:hidden;visibility:hidden;}
```



## 25、怪异盒模型box-sizing？弹性盒模型|盒布局?

```
在标准模式下的盒模型：盒子总宽度/高度=width/height+padding+border+margin
在怪异模式下的盒模型下，盒子的总宽度和高度是包含内边距padding和边框border宽度在内的，盒子总宽度/高度=width/height + margin = 内容区宽度/高度 + padding + border + margin;
box-sizing有两个值一个是content-box，另一个是border-box。
当设置为box-sizing:content-box时，将采用标准模式解析计算；
当设置为box-sizing:border-box时，将采用怪异模式解析计算。
```



## 26、简述几个CSS hack?

**(1) 图片间隙** 

在div中插入图片，图片会将div下方撑大3px。

hack1：将<div>与<img>写在同一行。

hack2：给<img>添加display：block；

dt li 中的图片间隙。

hack：给<img>添加display：block；

 

**(2) 默认高度**，IE6以下版本中，部分块元素，拥有默认高度（低于18px）

hack1：给元素添加：font-size：0；

hack2：声明：overflow：hidden；

表单行高不一致

hack1：给表单添加声明：float：left；height： ；border： 0；

鼠标指针

hack：若统一某一元素鼠标指针为手型：cursor：pointer；

当li内的a转化块元素时，给a设置float，IE里面会出现阶梯状

hack1：给a加display：inline-block；

hack2：给li加float：left；

## 27、 title和alt的区别？ 

*alt属性*

 

- Alt属性（注意是“属性”而不是“标签”）包括替换说明，对于图像和图像热点是必须的。它只能用在img、area（带有可点击区域的图像映射）和input元素image中（包括applet元素）。对于input元素，alt属性意在用来替换提交按钮的图片。
- 使用alt属性是为了给那些不能看到你文档中图像的浏览者提供文字说明。这包括那些使用本来就不支持图像显示或者图像显示被关闭的浏览器的用户，视觉障碍的用户和使用屏幕阅读器的用户。替换文字是用来替代图像而不是提供额外说明文字的。
- 文字的图像图片设置替换文字是最简单的，图像中包含的文字一般来说就可以作为alt属性值。Alt属性值得长度必须少于100个英文字符或者用户必须保证替换文字尽可能的短。

***title属性***

 

- title属性为设置该属性的元素提供建议性的信息。
- title属性可以用在除了base，basefont，head，html，meta，param，script和title之外的所有标签。但是并不是必须的。可能这正是为什么很多人不明白何时使用它。
- 使用title属性提供非本质的额外信息。大部分的可视化浏览器在鼠标悬浮在特定元素上时显示title文字为提示信息，然而这又由制造商来决定如何渲染title文字。一些浏览器会将title文字显示在状态栏里。
- title属性有一个很好的用途，即为链接添加描述性文字，特别是当连接本身并不是十分清楚的表达了链接的目的。这样就使得访问者知道那些链接将会带他们到什么地方，他们就不会加载一个可能完全不感兴趣的页面。另外一个潜在的应用就是为图像提供额外的说明信息，比如日期或者其他非本质的信息。

## 28、什么是CSS3 transform? animation? 区别是什么？

```
CSS3属性中关于制作动画的三个属性：Transform,Transition,Animation。
1、transform：描述了元素的静态样式，本身不会呈现动画效果，可以对元素进行 旋转rotate、扭曲skew、缩放scale和移动translate以及矩阵变形matrix。
div{
　　transform:scale(2);
}
transition和animation两者都能实现动画效果
transform常常配合transition和animation使用

2、transition样式过渡，从一种效果逐渐改变为另一种效果
transition是一个合写属性

transition:transition-property transition-duration transition-timing-function transition-delay
从左到右分别是：css属性、过渡效果花费时间、速度曲线、过渡开始的延迟时间

div{
    width：100px;
    height:100px;
    transition:transform 2s;
}
div：hover{
    transform:rotate(180deg);
}
transition通常和hover等事件配合使用，需要由事件来触发过渡

我们知道 transition 虽然简单好用，但是我们会发现它受到各种限制。

1：transition需要一个事件来触发，比如hover，所以没法在网页加载时自动发生
2: transition是一次性的，不能重复发生，除非一再触发。
3： transition只能定义开始状态和结束状态，不能定义中间状态，也就是说只有两个状态。
4：一条transition规则，只能定义一个属性的变化，不能涉及多个属性。
3、animation动画 由@keyframes来描述每一帧的样式

div{
　　animation:myAnimation 5s infinite
}
@keyframes myAnimation {
　　0%{left:0;transform:rotate(0);}
　　100%{left:200px;transform:rotate(180deg);}
}
区别：
-（1）transform仅描述元素的静态样式，常常配合transition和animation使用
-（2）transition通常和hover等事件配合使用，animation是自发的，立即播放
-（3）animation可设置循环次数
-（4）animation可设置每一帧的样式和时间，transition只能设置头尾
-（5）transition可与js配合使用，js设定要变化的样式，transition负责动画效果，如：

animation属性类似于transition，他们都是随着时间改变元素的属性值，

其主要区别在于：transition需要触发一个事件才会随着时间改变其CSS属性；
animation在不需要触发任何事件的情况下，也可以显式的随时间变化来改变元素CSS属性，达到一种动画的效果
1）动画不需要事件触发，过渡需要。
2）过渡只有一组（两个：开始-结束） 关键帧，动画可以设置多个。
```



## 29、nth-of-type和nth-child的区别是什么？

- `:nth-child(n)`选择器，该选择器选取父元素的第n 个子元素，**不论元素的类型**。
- `:nth-of-type(n)`选择器，选择器选取父元素的第 n 个**指定类型**的子元素。

```
<h1>这是标题</h1>



<p>第一个段落。</p>



<p>第二个段落。</p>



<p>第三个段落。</p>



<p>第四个段落。</p>



<p>第五个段落。</p>
```

1, `p:nth-child(3n+1)` 包含<h1>这是标题</h1>
2, `p:nth-of-type(3n+1)` 不包含<h1>这是标题</h1>，只选择p元素

> ##### 使用公式 `(an + b)`。描述：表示周期的长度,n 是计数器（从 0 开始）,b 是偏移值。
>
> ##### `odd` 和 `even` 是可用于匹配下标是`奇数`或`偶数`的子元素的关键词（`第一个子元素的下标是 1`）

## 30、:before 和 ::before区别是什么？

解答要点



-  **相同点**

1. 1. 都可以用来表示伪类对象，用来设置对象前的内容
   2. :befor和::before写法是等效的

-  **不同点**

1. 1. :befor是Css2的写法，::before是Css3的写法
   2. :before的兼容性要比::before好 ，不过在H5开发中建议使用::before比较好

## 31、如何让一个div 上下左右居中?

​	【绝对定位50%-本身50%】

​       position:absolute; left:50%; top:50%;

​       transform: translate(-50%,-50%); 



```

.div1{ 
          width:400px; 
          height:400px;
          border:#CCC 1px solid; 
          background:#99f; 
          position:absolute; left:50%; top:50%;/*绝对定位*/
          transform: translate(-50%,-50%); 
/*translate(x,y) 括号的百分比数据，会以本身的长宽做参考，比如，本身的长为100px，高为100px. 那填(50%,50%)就是向右，向下移动50px，添加负号就是向着相反的方向移动*/
} 
 
<div class="div1"></div>  
```



## 32、 解释下 viewport  

 大家知道手机的屏幕尺寸的尺寸较小，iphone5的话是320×568，然后iPhone6是375×667，这怎么小的宽度范围内，如果显示PC端的网页，如果没有做处理的话，那可能显示出来的网页会挤成一团，所以手机厂商为浏览器默认设置了一个虚拟的容器，叫布局视图(layout viewport)，这个容器足够宽，且不依赖于手机屏幕的宽度，能够直接把PC上的网页放进来而不变形，iPhone上这个容器大于等于980px，你能在这个网页上双击放大任意区域来显示局部最合适的大小。



这个时候介绍另外的东西，叫做可视视图(visual viewport)，这个是指你屏幕上浏览器显示区域的宽度，也就是它，决定一个屏幕能够显示的最大宽度（手指放大浏览器局部的时候，能够显示的宽度会变小），但是用户的放大缩小并不影响浏览器运行后的最大可视宽度，这个视图需要开发者进行设定，并且会受到网页内最长的可见元素的影响。



随着移动端的崛起，各个网站需要为移动端制作专门的网页，如果还是以手机虚拟容器的宽度为标准进行适配，那就是不合适的，我们需要让这个布局视图能够适应屏幕的大小，用户不用调整网页的大小就能看到一张尺寸合适的完整网页，而且能够保证显示效果的一致性，同样一个14px的文字或图片，在普通屏和高清屏上，我们看到的都是相对一致的大小。我们需要找到一个纽带，连接虚拟世界和真实世界的尺寸，进行适配。而设备自身的宽度是最合适的，确定的这个宽度的视图，被称为理想视图(ideal viewport)。



如果我们将能够显示的最大宽度设置为手机的宽度，并且将布局宽度也设定为手机的宽度，那不需要调整屏幕大小或者拖动页面，就能显示整张网页，而且屏幕的尺寸数值和屏幕的物理宽度组合在一起，就能够为CSS布局提供一把刻度相对一致的尺子，保证了显示效果。



可视视图的设定：

开发者可以用viewport的initial-scale来调整可视视图相对于屏幕真实宽度的大小比例，initial-scale=1时，可视视图的初始化最大宽度就等于屏幕宽度即理想视图的宽度，它会受到内部html节点的宽度影响，调整到最大的节点的长度(但是一般不超过1500px)。同时，如果设定user-scalable=no，那可视视图的宽度就会严格等于初始化时设定的宽度。



布局视图的设定：

布局视图的宽度等于viewport中设定的width以及可视视图的初始宽度中较大的一个，所以如果不写width属性，那布局视图的宽度也会等于屏幕的宽度，但是在可视视图初始化之后，布局视图的宽度只取决于viewport中设定的width，所以为了在旋转屏幕时能够改变布局视图的宽度，不能不写width或者将width设定为固定的数值，而是应该写上width=device-width





<meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0">



## 33、如何理解HTML结构语义化？

什么是 HTML 语义化？

简单来说，我们可以理解为：用正确的标签做正确的事情。

例如：

段落用 p 标签，标题用 h 系列标签，边栏用 aside 标签，主要内容用 main 标签。

为什么要关注 HTML 语义化？

对人：

- 便于团队的开发和维护。
- 在没有加载 CSS 的情况下也能呈现较好的内容结构与代码结构，易于阅读。

对机器：

- 有利于 SEO ，搜索引擎的爬虫依赖于标签来确定上下文和各个关键字的权重。
- 方便其他设备的解析（如屏幕阅读器、盲人阅读器等），利于无障碍阅读，提高可访问性。

总结

语义化当然并不仅仅在 HTML 中有用处，写 CSS 的时候，写 JavaScript 代码的时候，都应该采用有语义的类名和变量，并且永远不要在这些地方使用拼音。

## 34 CSS3中引入的伪元素有什么？

CSS中的伪元素大家以前看过：`:first-line,:first-letter,:before,:after;`

那么在CSS3中，他对伪元素进行了一定的调整，在以前的基础上增加了一个“：”也就是现在变成了`::first-letter,::first-line,::before,::after`另外他还增加了一个`::selection`。

在css3中，已经明确规定了伪类用一个冒号来表示，而伪元素则用两个冒号来表示。

`::first-line`选择元素的第一行，比如说改变每个段落的第一行文本的样式，我们就可以使用这个

```
p::first-line {font-weight:bold;}
```

`::before`和`::after`这两个主要用来给元素的前面或后面插入内容，这两个常用"content"配合使用，见过最多的就是清除浮动，

```
            .clearfix:before,
            .clearfix:after {
                 content: ".";
                 display: block;
                 height: 0;
                 visibility: hidden;
              }
             .clearfix:after {clear: both;}
             .clearfix {zoom: 1;}
```

对于 IE8 及更早版本中的 `:before`，必须声明 `<!DOCTYPE>`。

`:before` 选择器在被选元素的内容前面插入内容。例如：

```
<div>
    <h1>welcome</h1>
</div>

div:before{
    content:"hello world"
}
```

那么，被选元素是`div`，`div`的内容是`h1`，插入的内容就是`content`属性值“`hello world`”

`::selection`用来改变浏览网页选中文的默认效果

## 35、HTML5有哪些新特性，移除了哪些元素？如何处理HTML5新标签兼容问题？如何区分HTML和HTML5？



第一 HTML5有哪些新特性，移除了哪些元素



题目点评

题目涉及到范围非常的大，如果要面面俱到显然半天都答不完，可以先罗列出H5的一些新特性，不要回答那么具体，等面试官提具体的问题，所以在面试之前也要把这里的技术过一遍，至少每个技术也要做个小程序出来体验一下。

（一）.H5新特性

> 增强了图形渲染、影音、数据存储、多任务处理等处理能力主要表现在

***\*1)\** \**绘画 canvas;\****

 类似windows自带的画图板，可绘制线、框、路径、图……，InternetExplorer 9、Firefox、Opera、Chrome 以及 Safari 支持 <canvas> 及其属性和方法。

 画图需要的要素

 a)  笔，用笔可以画线、圆、矩形、文本等

 b)  颜色

 c)  画板

  

***\*2)\** \**本地离线存储 localStorage\****

 长期存储数据，浏览器关闭后数据不丢失;

 1.特点

 数据永久存储，没有时间限制；大小限制5M(够用了)；只存储字符串。

 2.数据存取方式

> > ```javascript
> > localStorage.a = 3;//设置a为"3"
> > localStorage["a"] = "sfsf";//设置a为"sfsf"，覆盖上面的值
> > localStorage.setItem("b","isaac");//设置b为"isaac"
> > var a1 = localStorage["a"];//获取a的值
> > var a2 = localStorage.a;//获取a的值
> > var b = localStorage.getItem("b");//获取b的值
> > var b2= localStorage.key(0);//获取第一个key的内容
> > localStorage.removeItem("c");//清除c的值
> > localStorage.clear();//清除所有的数据
> > ```

> 推荐使用：
>
> getItem()
>
> setItem()
>
> removeItem()

> 3.事件监听

> > ```javascript
> > if(window.addEventListener){
> >  window.addEventListener("storage",handle_storage,false);//
> > }else if(window.attachEvent){ //兼容IE
> >  window.attachEvent("onstorage",handle_storage);
> > }
> > function handle_storage(e){
> >  }
> > ```

> 对象e为localStorage对象，Chrome、Firefox支持差，IE支持较好。

**3)** **sessionStorage**的数据在浏览器关闭后自动删除;操作参考localStorage

**4)** 用于媒介回放的 ***\*video\**\**和\** \**audio\**** 元素;

**5)** 语意化更好的内容元素，比如**article、footer、header、nav、section;**

**6)** 表单控件，**calendar、date、time、email、url、search;**

**7)** 新的技术**webworker**(专用线程)

**8)** **websocket**socket通信

**9)** **Geolocation** 地理定位

***\**\*（二）移除的元素\*\**\***
```js

> 纯表现的元素

> > -  <basefont> 默认字体，不设置字体，以此渲染
> > -  <font> 字体标签
> > -  <center> 水平居中
> > -  <u> 下划线
> > -  <big> 大字体
> > -  <strike> 中横线
> > -  <tt> 文本等宽

> 框架集

> > -  <frameset>
> > -  <noframes>
> > -  <frame>

```



第二 如何处理HTML5新标签兼容问题？



**方法一 :**

1、使用静态资源的html5shiv包

```jsx

<!--[if lt IE9]>

<script src="http://cdn.static.runoob.com/libs/html5shiv/3.7/html5shiv.min.js"></script>

<![endif]-->
```

2、载入后，初始化新标签的css

header, section, footer, aside, nav, main, article, figure { display: block; }

**方法二：**

IE6/IE7/IE8支持通过document方法产生的标签，利用这一特性让这些浏览器支持HTML5新标签





**第三 如何区分HTML和HTML5？**



区分：

1、在文档bai声明上du，html有很长的一段代码，zhi并且很难记dao住这段代码，都是靠zhuan工具shu直接生成，而html5却是不同，只有简简单单的声明，也方便人们的记忆，更加精简。

2、在结构语义上；html4.0没有体现结构语义化的标签，这样表示网站的头部。html5在语义上却有很大的优势。提供了一些新的html5标签。

基本说明：

1、html5最先由WHATWG(Web 超文本应用技术工作组)命名的一种超文本标记语言，随后和W3C的xhtml2.0(标准)相结合，产生现在最新一代的超文本标记语言；

2、可以简单点理解成为HTML 5 ≈ HTML4.0+CSS3+JS+API。定义的这些标签，更加有利于优化，蜘蛛能识别。节省程序员写代码的时间。最主要还是在SEO的优化上。





## 36、常见浏览器兼容性问题？

所谓的浏览器兼容性问题，是指因为不同的浏览器对同一段代码有不同的解析，造成页面显示效果不统一的情况。在大多数情况下，我们的需求是，无论用户用什么浏览器来查看我们的网站或者登陆我们的系统，都应该是统一的显示效果。所以浏览器的兼容性问题是前端开发人员经常会碰到和必须要解决的问题。

在学习浏览器兼容性之前，我想把前端开发人员划分为两类：
第一类是精确按照设计图开发的前端开发人员，可以说是精确到1px的，他们很容易就会发现设计图的不足，并且在很少的情况下会碰到浏览器的兼容性问题，而这些问题往往都是浏览器的bug，并且他们制作的页面后期易维护，代码重用问题少，可以说是比较牢固放心的代码。
第二类是基本按照设计图来开发的前端开发人员，很多细枝末节差距很大，不如间距，行高，图片位置等等经常会差几px。某种效果的实现也是反复调试得到，具体为什么出现这种效果还模模糊糊，整体布局十分脆弱。稍有改动就乱七八糟。代码为什么这么写还不知所以然。这类开发人员往往经常为兼容性问题所困。修改好了这个浏览器又乱了另一个浏览器。改来改去也毫无头绪。其实他们碰到的兼容性问题大部分不应该归咎于浏览器，而是他们的技术本身了。
这篇文章主要针对的是第一类，严谨型的开发人员，因此这里主要从浏览器解析差异的角度来分析兼容性问题

**浏览器兼容问题一**：不同浏览器的标签默认的外补丁和内补丁不同
问题症状：随便写几个标签，不加样式控制的情况下，各自的margin 和padding差异较大。
碰到频率:100%
解决方案：css里 *{margin:0;padding:0;}
备注：这个是最常见的也是最易解决的一个浏览器兼容性问题，几乎所有的css文件开头都会用通配符*来设置各个标签的内外补丁是0。

**浏览器兼容问题二**：块属性标签float后，又有横行的margin情况下，在ie6显示margin比设置的大
问题症状:常见症状是ie6中后面的一块被顶到下一行
碰到频率：90%（稍微复杂点的页面都会碰到，float布局最常见的浏览器兼容问题）
解决方案：在float的标签样式控制中加入 display:inline;将其转化为行内属性
备注：我们最常用的就是div+css布局了，而div就是一个典型的块属性标签，横向布局的时候我们通常都是用div float实现的，横向的间距设置如果用margin实现，这就是一个必然会碰到的兼容性问题。

**浏览器兼容问题三**：设置较小高度标签（一般小于10px），在ie6，ie7，遨游中高度超出自己设置高度
问题症状：ie6、7和遨游里这个标签的高度不受控制，超出自己设置的高度
碰到频率：60%
解决方案：给超出高度的标签设置overflow:hidden;或者设置行高line-height 小于你设置的高度。
备注：这种情况一般出现在我们设置小圆角背景的标签里。出现这个问题的原因是ie8之前的浏览器都会给标签一个最小默认的行高的高度。即使你的标签是空的，这个标签的高度还是会达到默认的行高。

**浏览器兼容问题四**：行内属性标签，设置display:block后采用float布局，又有横行的margin的情况，ie6间距bug（类似第二种）
问题症状：ie6里的间距比超过设置的间距
碰到几率：20%
解决方案：在display:block;后面加入display:inline;display:table;
备注：行内属性标签，为了设置宽高，我们需要设置display:block;(除了input标签比较特殊)。在用float布局并有横向的margin后，在ie6下，他就具有了块属性float后的横向margin的bug。不过因为它本身就是行内属性标签，所以我们再加上display:inline的话，它的高宽就不可设了。这时候我们还需要在display:inline后面加入display:talbe。

**浏览器兼容问题五**：图片默认有间距
问题症状：几个img标签放在一起的时候，有些浏览器会有默认的间距，加了问题一中提到的通配符也不起作用。
碰到几率：20%
解决方案：使用float属性为img布局
备注：因为img标签是行内属性标签，所以只要不超出容器宽度，img标签都会排在一行里，但是部分浏览器的img标签之间会有个间距。去掉这个间距使用float是正道。

**浏览器兼容问题六**：标签最低高度设置min-height不兼容
问题症状：因为min-height本身就是一个不兼容的css属性，所以设置min-height时不能很好的被各个浏览器兼容
碰到几率：5%
解决方案：如果我们要设置一个标签的最小高度200px，需要进行的设置为：{min-height:200px; height:auto !important; height:200px; overflow:visible;}
备注：在B/S系统前端开时，有很多情况下我们又这种需求。当内容小于一个值（如300px）时。容器的高度为300px；当内容高度大于这个值时，容器高度被撑高，而不是出现滚动条。这时候我们就会面临这个兼容性问题。

**浏览器兼容问题七**：透明度的兼容css设置
方法是：每写一小段代码（布局中的一行或者一块）我们都要在不同的浏览器中看是否兼容，当然熟练到一定的程度就没这么麻烦了。建议经常会碰到兼容性问题的新手使用。很多兼容性问题都是因为浏览器对标签的默认属性解析不同造成的，只要我们稍加设置都能轻松地解决这些兼容问题。如果我们熟悉标签的默认属性的话，就能很好的理解为什么会出现兼容问题以及怎么去解决这些兼容问题。
/* css hack*/
我很少使用hacker的，可能是个人习惯吧，我不喜欢写的代码ie不兼容，然后用hack来解决。不过hacker还是非常好用的。
使用hacker 我可以吧浏览器分为3类：ie6 ；ie7和遨游；其他（ie8 chrome ff safari opera等）
ie6认识的hacker 是下划线_ 和星号 *
ie7 遨游认识的hacker是星号 * （包括上面问题6中的 !important也算是hack的一种。不过实用性较小。）
比如这样一个css设置 height:300px;*height:200px;_height:100px;
ie6浏览器在读到 height:300px的时候会认为高时300px；继续往下读，他也认识*heihgt， 所以当ie6读到*height:200px的时候会覆盖掉前一条的相冲突设置，认为高度是200px。继续往下读，ie6还认识_height,所以他又会覆盖掉200px高的设置，把高度设置为100px；
ie7和遨游也是一样的从高度300px的设置往下读。当它们读到*height200px的时候就停下了，因为它们不认识_height。所以它们会把高度解析为200px；
剩下的浏览器只认识第一个height:300px;所以他们会把高度解析为300px。
因为优先级相同且想冲突的属性设置后一个会覆盖掉前一个，所以书写的次序是很重要的。
最后说一下，严谨型的开发人员会有一套合适自己的RESET.CSS。结合自己的经验尽量规避容易出现不兼容的问题。以减少hack的使用，尽量符合W3C的标准。

## 37、简述前端优化方式 

前端性能优化是个巨大的课题，如果要面面俱到的说的话，估计三天三夜说不完。所以我们就从实际的工程应用角度出发，聊我们最常遇见的前端优化问题。

**1. 减少HTTP请求次数**

尽量合并图片、CSS、JS。比如加载一个页面，如果有5个css文件的话，那么会发出5次http请求，这样会让用户第一次访问你的页面的时候会长时间等待。而如果把这个5个文件合成一个的话，就只需要发出一次http请求，节省网络请求时间，加快页面的加载。

**2. 使用CDN**

网站上静态资源即css、js全都使用cdn分发，图片亦然。

**3. 避免空的src和href**

当link标签的href属性为空、script标签的src属性为空的时候，浏览器渲染的时候会把当前页面的URL作为它们的属性值，从而把页面的内容加载进来作为它们的值。所以要避免犯这样的疏忽。

**4. 为文件头指定Expires**

Exipres是用来设置文件的过期时间的，一般对css、js、图片资源有效。 他可以使内容具有缓存性，这样下回再访问同样的资源时就通过浏览器缓存区读取，不需要再发出http请求。如下例子：

新浪微博的这个css文件的Expires时间是2016-5-04 09:14:14.

![img](https://pic4.zhimg.com/80/v2-4456a97b4ef9f878840876f872e49177_720w.png)



**5. 使用gzip压缩内容**

gzip能够压缩任何一个文本类型的响应，包括html，xml，json。大大缩小请求返回的数据量。

**6. 把CSS放到顶部**

网页上的资源加载时从上网下顺序加载的，所以css放在页面的顶部能够优先渲染页面，让用户感觉页面加载很快。

**7. 把JS放到底部**

加载js时会对后续的资源造成阻塞，必须得等js加载完才去加载后续的文件 ，所以就把js放在页面底部最后加载。
欢迎加入前端直播学习群一起学习交流：512676244

**8. 避免使用CSS表达式**

举个css表达式的例子

[HTML](https://link.zhihu.com/?target=https%3A//www.qdfuns.com/tag.php%3Fname%3Dhtml)复制全屏

1
font-color: expression( (new Date()).getHours()%3 ? “#FFFFFF" : “#AAAAAA" );
2
​这个表达式会持续的在页面上计算样式，影响页面的性能。并且css表达式只被IE支持。

**9. 将CSS和JS放到外部文件中**

目的是缓存文件，可以参考原则4。 但有时候为了减少请求，也会直接写到页面里，需根据PV和IP的比例权衡。

**10. 权衡DNS查找次数**

减少主机名可以节省响应时间。但同时，需要注意，减少主机会减少页面中并行下载的数量。

IE浏览器在同一时刻只能从同一域名下载两个文件。当在一个页面显示多张图片时，IE 用户的图片下载速度就会受到影响。所以新浪会搞N个二级域名来放图片。

下面是新浪微博的图片域名，我们可以看到他有多个域名，这样可以保证这些不同域名能够同时去下载图片，而不用排队。不过如果当使用的域名过多时，响应时间就会慢，因为不用响应域名时间不一致。

![img](https://pic2.zhimg.com/80/v2-2eb9a87e8ad38305e382cee771f1d0dd_720w.png)



**11. 精简CSS和JS**

这里就涉及到css和js的压缩了。比如下面的新浪的一个css文件，把空格回车全部去掉，减少文件的大小。现在的压缩工具有很多，基本主流的前端构建工具都能进行css和js文件的压缩，如grunt，glup等。

**12. 避免跳转**

有种现象会比较坑爹，看起来没什么差别，其实多次了一次页面跳转。比如当URL本该有斜杠（/）却被忽略掉时。例如，当我们要访问 [http://baidu.com](https://link.zhihu.com/?target=http%3A//baidu.com/) 时，实际上返回的是一个包含301代码的跳转，它指向的是 [http://baidu.com/（注意末尾的斜杠）。在nginx服务器可以使用rewrite；Apache服务器中可以使用Alias](https://link.zhihu.com/?target=http%3A//baidu.com/%EF%BC%88%E6%B3%A8%E6%84%8F%E6%9C%AB%E5%B0%BE%E7%9A%84%E6%96%9C%E6%9D%A0%EF%BC%89%E3%80%82%E5%9C%A8nginx%E6%9C%8D%E5%8A%A1%E5%99%A8%E5%8F%AF%E4%BB%A5%E4%BD%BF%E7%94%A8rewrite%EF%BC%9BApache%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%AD%E5%8F%AF%E4%BB%A5%E4%BD%BF%E7%94%A8Alias) 或者 mod_rewrite或者the DirectorySlash来避免。

另一种是不用域名之间的跳转， 比如访问 [http://baidu.com/bbs](https://link.zhihu.com/?target=http%3A//baidu.com/bbs) 跳转到[http://bbs.baidu.com/](https://link.zhihu.com/?target=http%3A//bbs.baidu.com/)。那么可以通过使用Alias或者mod_rewirte建立CNAME（保存一个域名和另外一个域名之间关系的DNS记录）来替代。

**13. 删除重复的JS和CSS**

重复调用脚本，除了增加额外的HTTP请求外，多次运算也会浪费时间。在IE和Firefox中不管脚本是否可缓存，它们都存在重复运算JavaScript的问题。

**14. 配置ETags**

它用来判断浏览器缓存里的元素是否和原来服务器上的一致。比last-modified date更具有弹性，例如某个文件在1秒内修改了10次，Etag可以综合Inode(文件的索引节点(inode)数)，MTime(修改时间)和Size来精准的进行判断，避开UNIX记录MTime只能精确到秒的问题。 服务器集群使用，可取后两个参数。使用ETags减少Web应用带宽和负载
欢迎加入前端全栈开发交流圈一起学习交流：512676244

**15. 可缓存的AJAX**

异步请求同样的造成用户等待，所以使用ajax请求时，要主动告诉浏览器如果该请求有缓存就去请求缓存内容。如下代码片段, cache:true就是显式的要求如果当前请求有缓存的话，直接使用缓存

[HTML](https://link.zhihu.com/?target=https%3A//www.qdfuns.com/tag.php%3Fname%3Dhtml)复制全屏


1
$.ajax(
2
{
3
url : 'url',
4
dataType : "json",
5
cache: true,
6
success : function(son, status){
7
}
8
​



**16. 使用GET来完成AJAX请求**

当使用XMLHttpRequest时，浏览器中的POST方法是一个“两步走”的过程：首先发送文件头，然后才发送数据。因此使用GET获取数据时更加有意义。

**17. 减少DOM元素数量**

这是一门大学问，这里可以引申出一堆优化的细节。想要具体研究的可以看后面推荐书籍。总之大原则减少DOM数量，就会减少浏览器的解析负担。

**18. 避免404**

比如外链的css、js文件出现问题返回404时，会破坏浏览器的并行加载。

**19. 减少Cookie的大小**

Cookie里面别塞那么多东西，因为每个请求都得带着他跑。

**20. 使用无cookie的域**

比如CSS、js、图片等，客户端请求静态文件的时候，减少了 Cookie 的反复传输对主域名的影响。

**21. 不要使用滤镜**

IE独有属性AlphaImageLoader用于修正7.0以下版本中显示PNG图片的半透明效果。这个滤镜的问题在于浏览器加载图片时它会终止内容的呈现并且冻结浏览器。在每一个元素（不仅仅是图片）它都会运算一次，增加了内存开支，因此它的问题是多方面的。

完全避免使用AlphaImageLoader的最好方法就是使用PNG8格式来代替，这种格式能在IE中很好地工作。如果你确实需要使用AlphaImageLoader，请使用下划线_filter又使之对IE7以上版本的用户无效。

**22. 不要在HTML中缩放图片**

比如你需要的图片尺寸是50* 50

那就不用用一张500*500的大尺寸图片，影响加载

**23. 缩小favicon.ico并缓存**

以上是Yslow的23个优化原则，基本可以涵盖现在前端大部分的性能优化原则了，很多更加geek和精细优化方法都是从这些原则里面延伸出来的。 具体想了解更多优化细则的童鞋建议去看看下面的一本书，毕竟页数多讲的也细嘛：

## 38、块级元素？行内元素？空元素？

**块元素**(block element)一般是其他元素的容器元素，能容纳其他块元素或内联元素。块元素就好比一个四方块，可以放其他的四方块，并可以呈现在页面上任何地方。

- 默认情况下块元素，是独占一行的
- 常见的块元素：**div、p、h1-h6、form（只能用来容纳其他块元素）、hr、table、ul、ol**等
- 高度，行高以及外边距和内边距都可控制
- 宽度缺省是它的容器的100%，除非设定一个宽度
- 它可以容纳内联元素和其他块元素

**行内元素**(inline element)也叫内嵌元素或内联元素，一般都是基于语义级(semantic)的基本元素。行内元素只能容纳文本或者其他行内元素，常见行内元素有**a、span、img**。CSS权威指南》中文字显示：任何不是块级元素的可见元素都是行内元素。其表现的特性是“行布局”形式，这里的“行布局”的意思就是说其表现形式始终以行进行显示。比如，我们设定一个内联元素border-bottom:1px solid #000;时其表现是以每行进行重复，**每一行下方都会有一条黑色的细线**。如果是块级元素那么所显示的的黑线只会在块的下方出现。

- 和其他元素都在一行上
- 高，行高及外边距和内边距不可改变
- 宽度就是它的文字或图片的宽度，不可改变
- 行内元素只能容纳文本或者其他行内元素

**块元素和行内元素的区别**

- 块元素，总是在新行上开始；行内元素，和其他元素都在一行上
- 块元素，能容纳其他块元素或行内元素；而内联元素，只能容纳文本或者其他行内元素
- 块元素中高度，行高以及顶和底边距都可控制；内联元素中高，行高及顶和底边距不可改变
- 说白了，行内元素就好像一个**单词**；块级元素就好像一个**段落**，如果不另加定义的话，它将独立一行出现

**文档流**

- 将窗体自上而下分成一行行， 并在每行中按从左至右的顺序排放元素，即为**文档流**
- 每个非浮动块级元素都独占一行， 浮动元素则按规定浮在行的一端。 若当前行容不下， 则另起新行再浮动
- 内联元素也不会独占一行。 几乎所有元素(包括块级，内联和列表元素）均可生成子行， 用于摆放子元素
- 有**三种情况**将使得元素脱离文档流而存在，分别是**浮动，绝对定位， 固定定位**。 但是在IE中浮动元素也存在于文档流中
- 浮动元素不占任何正常文档流空间，而浮动元素的定位还是基于正常的文档流，然后从文档流中抽出并尽可能远的移动至左侧或者右侧。文字内容会围绕在浮动元素周围。当一个元素从正常文档流中抽出后，仍然在文档流中的其他元素将忽略该元素并填补他原先的空间。
- 基于文档流， 我们可以很容易理解以下的定位模式：相对定位（relative）， 即相对于元素在文档流中位置进行偏移，但保留原占位。绝对定位（absolute）， 即完全脱离文档流， 相对于position属性非static值的最近父级元素进行偏移。固定定位（fixed）， 即完全脱离文档流， 相对于视区进行偏移。

空元素

空元素的意思bai是：在HTML元素中，没有内容的 HTML 元素被称为空元素。
基本概述：
1，在用来描述网页的计算机语言中，有一种超文本标记语言，被称为HTML(Hyper Text Markup Language) 。而构成HTML内容的标记语言是由一套标记标签组成。这套标记标签通常被称为 HTML 标签 (HTML tag)。,
2，HTML 标签是由尖括号包围的关键词，比如 <html> ，通常是成对出现的，比如 <b> 和 </b>。
3，这些成对出现的HTML 标签，第一个标签是开始标签，第二个标签是结束标签。大多数HTML 标签在开始标签和结束标签之间都具有内容，而某些标签则没有内容。,
4，HTML中，从开始标签(start tag)到结束标签(end tag)的所有代码，被称为HTML元素。
5，由于HTML元素的内容是开始标签与结束标签之间的内容。而某些 HTML 元素具有空内容。(empty content)，那些含有空内容的HTML元素，就是空元素。空元素是在开始标签中关闭的。
举例说明：
1，<br> 就是没有关闭标签的空元素(<br> 标签定义换行)。,
2，在 XHTML、XML 以及未来版本的 HTML 中，所有元素都必须被关闭。
3，在开始标签中添加斜杠，比如 <br />，是关闭空元素的正确方法，HTML、XHTML 和 XML 都接受这种方式。



## 39、media属性？ 

 **媒体类型**

all -- 所有设备。如没有only和not限定可以省略，省略不写时默认为all
print -- 打印机
screen -- 计算机屏幕
等等

**and & not & only & 逗号 操作符**

and -- 所有媒体属性表达式为真时，媒体查询结果才为真。

not -- 对媒体结果求值取反。如果有逗号操作符，其作用范围至逗号处为止

```
@media not all and (min-width:800px){...}
等价于
@media not (all and (min-width:800px)){...}
而不是
@media (not all) and (min-width:800px){...}
@media not screen and (color), print and (color)
等价于
@media (not (screen and (color))), print and (color)

```

不支持媒体查询的浏览器会解析成media=screen，媒体属性不会求值。从而错误地应用样式。

,  --  逗号相当于or（或者）

**常用媒体属性**

width : 可视宽度
height : 可视高度
device-width: 设备宽度
device-height: 设备高度
orientation: 方向
aspect-ratio: 宽高比
color: 颜色
device-aspect-ratio： 设备宽高比

详细信息参考MDN文档：[css媒体查询](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Media_queries)

## 40、meta标签的name属性值?

就是头文件里的meta标签及其属性等等。（之前只知道utf-8还有Author）

于是去网上查找了相关资料，在这里简单描述一下：

​    name是描述网页的，对应于Content（网页内容）。ame的value值（name=""）指定所提供信息的类型。有些值是已经定义好的。例如description(说明)、keyword(关键字)、refresh(刷新)等。还可以指定其他任意值，如：creationdate(创建日期) 、document ID(文档编号)和level(等级)等。

​    name的content指定实际内容。如：如果指定level(等级)为value，则Content可能是beginner(初)、intermediate(中)、advanced(高)。 

**1.Keywords(关键字)***

​    说明：为搜索引擎提供关键字列表（content里的关键字记得要用逗号隔开）

​    eg:<meta name="Keywords" content="关键词1,关键词2,关键词3,关键词4,……">

**2.Copyright (版权)**

​    说明：标注声明相关版权

​    eg:<meta name="xxx" content="版权归xxx.xxx所有">

**3.Robots (机器人向导)**

​    说明：Robots用来告诉搜索机器人页面需要或者不需要索引。Content的參数有all、none、index、noindex、follow、nofollow，默认是all。

​    all：文件将被检索，且页面上的链接能够被查询。

​    none：文件将不被检索。且页面上的链接不能够被查询。(和 "noindex, no follow" 起同样作用)

​    index：文件将被检索；（让robot/spider登录）

​    follow：页面上的链接能够被查询；

​    noindex：文件将不被检索，但页面上的链接能够被查询；(不让robot/spider登录)

​    nofollow：文件将不被检索，页面上的链接能够被查询。(不让robot/spider顺着此页的连接往下探找) 

​    eg:<meta name="robots" content="All|None|Index|Noindex|Follow|Nofollow">

**4.Renderer（指定默认渲染内核）**

​    说明：指定双核浏览器默认以哪种方式渲染页面

​    eg:<meta name="renderer" content="webkit"> //强制指定webkit内核

**5.Viewport（视窗/移动端）**

​    说明：是用户网页的可视区域，即“视区”。

​    eg:<meta name="viewport" content="width=device-width, initial-scale=1.0">

​    width：控制 viewport 的大小，可以指定的一个值，如 600，或者特殊的值，如 device-width 为设备的宽度（单位为缩放为 100% 时的 CSS 的像素）。

​    height：和 width 相对应，指定高度。

​    initial-scale：初始缩放比例，也即是当页面第一次 load 的时候缩放比例。

​    maximum-scale：允许用户缩放到的最大比例。

​    minimum-scale：允许用户缩放到的最小比例。

​    user-scalable：用户是否可以手动缩放。

​    eg：

​    a. 设置屏幕宽度为设备宽度，禁止用户手动调整缩放

         <meta name="viewport" content="width=device-width,user-scalable=no" />

​    b. 设置屏幕密度为高频，中频，低频自己主动缩放，禁止用户手动调整缩放)<meta name="viewport" content="width=device-width, target-densitydpi=high-dpi, initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0, user-scalable=no"/>

 

## 41、一般做手机页面切图的几种方式 

针对手机端页面，通常情况下，需要对设计图片切两种图片。

①：dpr:2------切两倍图（即设计原图大小，因为设计图是按原来的手机尺寸放大两倍之后的）  一般保存为xxx@2x

②：dpr:3------切三倍图（即设计原图大小的1.5倍，因为设计图是按原来的手机尺寸放大两倍之后的）   一般保存为xxx@3x

淘宝的做法：

![](https://riyugo.com/i/2021/02/22/o15bqq.png)





例如：设计图是720px的宽度。

由于设计图是放大两倍的。所以一倍的大小是=720/2 = 360px;

放大三倍图就是= 360*3 = 720*1.5 = 1080px;

## 42、px/em/rem有什么区别？为什么通常给font-size 设置的字体为62.5%

首先px是像素单位  是多少像素就是多少像素

1、rem单位可谓集相对大小和绝对大小的优点于一身

2、和em不同的是rem总是相对于根元素(如:root{})，而不像em一样使用级联的方式来计算尺寸。这种相对单位使用起来更简单。

3、rem支持IE9及以上，意思是相对于根元素html（网页），不会像em那样，依赖于父元素的字体大小，而造成混乱。使用起来安全了很多。

例如：

```text
<div class="big">
    我是14px=1.4rem<div class="small">我是12px=1.2rem</div>
</div>
```

样式为：

```text
<style>
    html {font-size: 10px;  } /*  公式16px*62.5%=10px  */  
    .big{font-size: 1.4rem}
    .small{font-size: 1.2rem}
</style>
```



![img](https://pic3.zhimg.com/80/v2-a92a2f81145df277235969773d82ff3e_720w.png)



**注意：**

- 值得注意的浏览器支持问题： IE8，Safari 4或 iOS 3.2中不支持rem单位。
- 如果你的用户群都使用最新版的浏览器，那推荐使用rem，如果要考虑兼容性，那就使用px,或者两者同时使用。

**这就是我整体对px、em、rem区别的总结**





## 43、sass和scss有什么区别? 

当我们说起 Sass ，我们经常指的是两种事物：一种 css 预处理器和一种语言。我们经常这样说，“我们正在使用 Sass”，或者 “这是一个 Sass mixin”。同时，Sass （预处理器）有两种不同的语法：

- **Sass**,一种缩进语法
- **SCSS**,一种 CSS-like 语法

## 44、如果对css进行优化如何处理？

 

**加载性能**
这个方面相关的 best practice 太多了，网上随便找一找就是一堆资料，比如不要用 import 啊，压缩啊等等，主要是从减少文件体积、减少阻塞加载、提高并发方面入手的，任何 hint 都逃不出这几个大方向。

**选择器性能**
可以参考 GitHub 的这个分享 [https://speakerdeck.com/jonrohan/githubs-css-performance](https://link.zhihu.com/?target=https%3A//speakerdeck.com/jonrohan/githubs-css-performance)，但 selector 的对整体性能的影响可以忽略不计了，selector 的考察更多是规范化和可维护性、健壮性方面，很少有人在实际工作当中会把选择器性能作为重点关注对象的，但也像 GitHub 这个分享里面说的一样——知道总比不知道好。

**渲染性能**
渲染性能是 CSS 优化最重要的关注对象。页面渲染 junky 过多？看看是不是大量使用了 text-shadow？是不是开了字体抗锯齿？CSS 动画怎么实现的？合理利用 GPU 加速了吗？什么你用了 Flexible Box Model？有没有测试换个 layout 策略对 render performance 的影响？这个方面搜索一下 CSS render performance 或者 CSS animation performance 也会有一堆一堆的资料可供参考。

**可维护性、健壮性**
命名合理吗？结构层次设计是否足够健壮？对样式进行抽象复用了吗？优雅的 CSS 不仅仅会影响后期的维护成本，也会对加载性能等方面产生影响。这方面可以多找一些 OOCSS（不是说就要用 OOCSS，而是说多了解一下）等等不同 CSS Strategy 的信息，取长补短。


## 45、如何对css文件进行压缩合并?  

使用gulp。用法很简单，看下官网例子很快就上手了。
可以用gulp-concat合并文件，gulp-uglify对js进行压缩，gulp-clean-css对css进行压缩，gulp-plumber避免过程出错导致gulp退出。

## 46、什么是组件？什么是模块化？有什么区别？

![img](https://pic1.zhimg.com/80/b25efd3e8af188b5ab36ccb66baddd71_1440w.jpg?source=1940ef5c)





模块化的诉求是**解耦**，组件化的诉求是**好用**

组件化编程: 将js css html包装一起提供方法和效果； 模块化: 将相同的功能抽取出来 存放在一个位置进行编程

## 47、如何实现图片和文字在同一行显示?

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
    .divs .imgs{
        display: inline-block;
        vertical-align: middle;
    }
    .divs .infos{
        display: inline-block;
    }
    </style>
</head>
<body>
    <div class="divs">
        <div class="imgs"><img src="logo.jpg" alt=""></div>
        <div class="infos"><a href="">找回密码</a></div>
    </div>
</body>
</html>
```



![img](https://images0.cnblogs.com/blog/707050/201506/251022555025323.png)



## 48、a标签中 active hover link visited 正确的设置顺序是什么?

**1. <a>标签**

我们先说一说<a>标签是干啥用的。

 

<a> 标签定义超链接，用于从一张页面链接到另一张页面。

<a> 元素最重要的属性是 href 属性，它指示链接的目标。

在所有浏览器中，链接的默认外观是：

- 未被访问的链接带有下划线而且是蓝色的
- 已被访问的链接带有下划线而且是紫色的
- 活动链接带有下划线而且是红色的

<a>标签还有一个很重要的属性：target，它用于规定在何处打开链接文档，取值有：_blank；_parent；_self；_top。这个我后面也会小小地总结一下。

**2.** **a链接的四种状态**

 

伪类是CSS 用于向某些选择器添加特殊的效果。

a标签中有四个：link、visited、hover、active

**（1）link**

说明：设置a对象在未被访问前的样式表属性。

**（2）visited**

说明：设置a对象在其链接地址已被访问过时的样式表属性。

**（3）hover**

说明：设置对象在其鼠标悬停时的样式表属性。

**（4）active**

说明：设置对象在被用户激活（在鼠标点击与释放之间发生的事件）时的样式表属性。

定义CSS时候的顺序不同，也会直接导致链接显示的效果不同。原因可能在于浏览器解释CSS时遵循的“就近原则”。正确的顺序：a:link、a:visited、a:hover、a:active

解释：

 

- link:连接平常的状态 
- visited:连接被访问过之后 
- hover:鼠标放到连接上的时候
- active:连接被按下的时候 

 

**举例来说：**

我想让未访问链接颜色为蓝色，活动链接为绿色，已访问链接为红色：

第一种情况：我定义的顺序是a:visited、a:hover、a:link，这时会发现：把鼠标放到未访问过的蓝色链接上时，它并不变成绿色，只有放在已访问的红色链接上，链接才会变绿。

第二种情况：我把CSS定义顺序调整为：a:link、a:visited、a:hover，这时，无论你鼠标经过的链接有没有被访问过，它都会变成绿色啦。

这是因为，一个鼠标经过的未访问链接同时拥有a:link、a:hover两种属性，在第一种情况下，a:link离它最近，所以它优先满足a:link，而放弃a:hover的重复定义。在第二种情况，无论链接有没有被访问过，它首先要检查是否符合a:hover的标准（即是否有鼠标经过它），满足，则变成绿色，不满足，则继续向上查找，一直找到满足条件的定义为止。

一句话：在CSS中，如果对于相同元素有针对不同条件的定义，宜将最一般的条件放在最上面，并依次向下，保证最下面的是最特殊的条件。这样，浏览器在显示元素时，才会从特殊到一般、逐级向上验证条件，才会使你的每一个CSS语句都起到效果。当然，如果故意打乱顺序，也会造成一些特殊的效果。比如，可以为链接制造出下划线颜色与文字颜色的差异。

其实这个CSS问题早已有高人提出啦，还是个老外呢。他给总结了一个便于记忆的“爱恨原则”（LoVe/HAte），即四种伪类的首字母:LVHA。

再重复一遍正确的顺序：a:link、a:visited、a:hover、a:active .

最后经验补充：

1.鼠标经过的“未访问链接”同时拥有a:link、a:hover两种属性，后面的属性会覆盖前面的属性定义；

2.鼠标经过的“已访问链接”同时拥有a:visited、a:hover两种属性，后面的属性会覆盖前面的属性定义；

所以说，a:hover定义一定要放在a:link、a:visited的后面！

## 49、a标签中，如何禁用href 跳转页面或定位链接

```
1、e.preventDefault();
2、href="javascript:void(0);"
```



## 50、手机端上图片长时间点击会选中图片，如何处理？

```
onselect=function() {return false}
```



## 51、video标签的几个属性和方法

- src ：视频的属性
- poster：视频封面，没有播放时显示的图片
- preload：预加载
- autoplay：自动播放
- loop：循环播放
- controls：浏览器自带的控制条
- width：视频宽度
- height：视频高度

## 52、常见的视频编码格式有几种？视频格式有几种？

avi mpg rmvb rm mp4 3gp

## 53、canvas在标签上设置宽高和在style中设置宽高有什么区别？

在html中，我们给一个元素设置宽高通常会使用css样式设置。而canvas他有自己的宽高属性，我们可以在canvas中设置宽和高，但是canvas的width与height属性与css中设置width与height有没有区别，有什么区别呢，我们可以通过代码去实现对比一下。

第一种： 在canvas中设置宽高

代码：

```html
<!DOCTYPE html>



<html lang="en">



<head>



    <meta charset="UTF-8">



    <meta name="viewport" content="width=device-width, initial-scale=1.0">



    <meta http-equiv="X-UA-Compatible" content="ie=edge">



    <title>Document</title>



    <style>



        canvas {



            border: 1px solid #000;



        }



    </style>



</head>



<body>



    <canvas id="mycanvas" width="200" height="200"></canvas>



</body>



</html>



<script>



    var mycanvas = document.getElementById('mycanvas');



    var ctx = mycanvas.getContext('2d');



    ctx.moveTo(50,50);



    ctx.lineTo(150,50);



    ctx.lineTo(150,150);



    ctx.lineTo(50,150);



    ctx.closePath();



    ctx.fillStyle = 'red';



    ctx.fill();



</script>
```

 

效果：

![img](https://img-blog.csdnimg.cn/20190418212800523.png)

然后我们将宽高调整为500；

代码：

```html
 <canvas id="mycanvas" width="200" height="200"></canvas>
```

效果：

![img](https://img-blog.csdnimg.cn/2019041821295369.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3llbGxvd211c2hyb29t,size_16,color_FFFFFF,t_70)

以上我们可以看的，画布明显变大，里面的图形大小不变。

**第二种：在style中设置宽高**

代码：

```html
<head>



    <style>



        canvas {



            border: 1px solid #000;



            width: 500px;



            height: 500px;



        }



    </style>



</head>



<body>



    <canvas id="mycanvas"></canvas>



</body>
```

效果：

![img](https://img-blog.csdnimg.cn/20190418213325530.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3llbGxvd211c2hyb29t,size_16,color_FFFFFF,t_70)

我们发现，不仅画布变大了，而且里面的图形也会变大变形。

可以简单理解：canvas相当与我们电脑中自带的“画图”工具，有画布，画板，绘图工具构成。当我在canvas中设置宽高，相当于使用鼠标拖动了画布的边框使画布变大，但是里面的内容不会变化。当我在style中设置了宽高，相当于点击放大镜对整个图像进行方法，使得里面的内容也会跟着变化。

## 54、什么是border-image？ 

这里通过三个实例讲解一下border-image:

![这里写图片描述](https://img-blog.csdn.net/20171222111308044?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMjQxNDcwNTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**原图**是这样子的：

![这里写图片描述](https://img-blog.csdn.net/20171222112033887?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMjQxNDcwNTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

接下来我们使用border-image来处理这个图片为**边框图**：

####  

![这里写图片描述](https://img-blog.csdn.net/20171222111435963?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMjQxNDcwNTE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
**代码：**

```js
<div style=" 
	   background:#F4FFFA;
	   width:100%; 
	   height:210px; 
	   border:41px solid #ddd;   
	   border-image:url(./imgs/333.png) 70 repeat ">
</div>
```









## 55、解释在ie低版本下的怪异盒模型和CSS3的怪异盒模型和弹性盒模型

一、怪异盒模型

**怪异盒模型的属性是box-sizing，他有两个属性值：**

1、content-box

　　这是由 CSS2.1 规定的宽度高度行为。宽度和高度分别应用到元素的内容框。在宽度和高度之外绘制元素的内边距和边框。

　　**简而言之就是**，一般的盒子都是属于这种，最显著的特点就是加上padding后，盒子会被撑大，需要减去对应的高度或宽度。

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWcyMDIwLmNuYmxvZ3MuY29tL2Jsb2cvMTk1NTE2MC8yMDIwMDQvMTk1NTE2MC0yMDIwMDQxNTA4MTExMTQ0Mi02MTQxMzA1NjYucG5n?x-oss-process=image/format,png)2、border-box

　　为元素设定的宽度和高度决定了元素的边框盒。就是说，为元素指定的任何内边距和边框都将在已设定的宽度和高度内进行绘制。

通过从已设定的宽度和高度分别减去边框和内边距才能得到内容的宽度和高度。

　　简而言之，为盒子添加高度或宽度之后，再给盒子添加border和padding不会使盒子撑大，边框和padding都限制在盒子内部，常用于移动端。

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWcyMDIwLmNuYmxvZ3MuY29tL2Jsb2cvMTk1NTE2MC8yMDIwMDQvMTk1NTE2MC0yMDIwMDQxNTA4MTEyNTk5NS0yMDM0MDc0MTUxLnBuZw?x-oss-process=image/format,png)

二、弹性盒布局

Flex容器：采用 Flex 布局的元素的父元素；
Flex项目：采用 Flex 布局的元素的父元素的子元素；
容器默认存在两根轴：水平的主轴和垂直的交叉轴。主轴的开始位置（与边框的交叉点）叫做main start，结束位置叫做main end；

交叉轴的开始位置叫做cross start，结束位置叫做cross end。*项目默认沿主轴排列。*

*单个项目占据的主轴空间叫做main size，占据的交叉轴空间叫做cross size。*

能实现下面这种骰子布局，那么恭喜你，就说明弹性盒已经掌握了，下面介绍弹性盒具体的用法及案例

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWcyMDIwLmNuYmxvZ3MuY29tL2Jsb2cvMTk1NTE2MC8yMDIwMDQvMTk1NTE2MC0yMDIwMDQxNTA4MTUzMDgwNi0xNDExODk2NDcucG5n?x-oss-process=image/format,png)

flex容器属性

1、触发弹性盒：display:flex、inline-flex

　　注意，设为 Flex 布局以后，子元素的float、clear和vertical-align属性将失效。

2、flex-direction属性 决定主轴的方向（即项目的排列方向）

　　flex-direction: row | row-reverse | column | column-reverse;

3、flex-wrap属性，定义子元素是否换行显示　　

　　flex-wrap: nowrap(默认值，不换行) | wrap（换行） | wrap-reverse(反向换行);

4、 flex-flow

　　flex-flow属性是flex-direction属性和flex-wrap属性的简写形式，默认值为row nowrap;

5、 justify-content属性 定义了项目在主轴（）上的对齐方式

　　justify-content: flex-start | flex-end | center | space-between(两端对齐) | space-around（自动分配）;

6、align-items属性定义项目在侧轴上如何对齐

　　align-items: flex-start | flex-end | center | baseline | stretch（默认值）;

7、align-content属性定义了多根轴线的对齐方式。对于单行子元素，该属性不起作用。　

　　align-content: flex-start | flex-end | center | space-between | space-around | stretch;
　　align-content在侧轴上执行样式的时候，会把默认的间距给合并。对于单行子元素，该属性不起作用

 

**flex项目属性**

1、align-self属性　

说明：

　　Internet Explorer 和 Safari 浏览器不支持 align-self 属性
　　align-self 属性规定灵活容器内被选中项目的对齐方式。
　　注意：align-self 属性可重写灵活容器的 align-items 属性。
　　属性值
　　　　auto 默认值。元素继承了它的父容器的 align-items 属性。如果没有父容器则为 "stretch"。
　　　　Stretch 元素被拉伸以适应容器。
　　　　Center 元素位于容器的中心。
　　　　flex-start 元素位于容器的开头。
　　　　flex-end 元素位于容器的结尾。

2、order

说明：
　　number排序优先级，数字越大越往后排，默认为0，支持负数。

3、flex　

说明：
　　复合属性。设置或检索弹性盒模型对象的子元素如何分配空间
　　详细属性值：
　　缩写「flex: 1」, 则其计算值为「1 1 0%」
　　缩写「flex: auto」, 则其计算值为「1 1 auto」
　　flex: none」, 则其计算值为「0 0 auto」
　　flex: 0 auto」或者「flex: initial」, 则其计算值为「0 1 auto」，即「flex」初始值

4、flex-xxx　　

　　flex-grow
　　　　一个数字，规定项目将相对于其他灵活的项目进行扩展的量。
　　flex-shrink
　　　　一个数字，规定项目将相对于其他灵活的项目进行收缩的量。
　　flex-basis
　　　　项目的长度

## 56、animation对应的属性

**定义动画的速度曲线**

ease：动画以低速开始，然后加快，在结束前变慢。

linear：匀速

ease-in：动画以低速开始

ease-out：动画以低速结束

ease-in-out：动画以低速开始和结束，相对于ease缓慢，速度更均匀

step-start：按keyframes设置逐帧显示，第一帧为keyframes设置的第一帧。

step-end：按keyframes设置逐帧显示，第一帧为样式的初始值。

steps(<number>[, [ start | end ] ]?)：把keyframes里设置的一帧等分为几帧，start为第一次显示第一帧，end第一次显示样式的初始值，例如：steps(4,start)

cubic-bezier(<number>, <number>, <number>, <number>)：在 cubic-bezier 函数中自己的值。可能的值是从 0 到 1 的数值。贝兹曲线限制了首尾两控制点的位置，通过调整中间两控制点的位置可以灵活得到常用的动画效果

 

**animation-iteration-count**

动画迭代次数，默认就1次，可以设置2次，3次，4次，…infinite表示无限

 

**animation-duration**

指一个动画周期持续时间。单位秒s或毫秒ms.

 

**animation-delay**

指动画延时执行时间。单位秒s或毫秒ms.

 

**animation-direction**

指动画时间轴上帧前进的方向。

normal:默认值，表示一直向前，最后一帧结束后回到第一帧

reverse:与normal的运行方向相反

alternate:往前播放完了之后，然后再倒带，倒带到头了再往后播放

alternate-reverse:与alternate的运行方向相反

 

**animation-fill-mode**

设置动画结束后的状态

none：默认值。不设置对象动画之外的状态，DOM未进行动画前状态

forwards：设置对象状态为动画结束时的状态，100%或to时，当设置animation-direcdtion为reverse时动画结束后显示为keyframes第一帧

backwards：设置对象状态为动画开始时的状态,（测试显示DOM未进行动画前状态）

both：设置对象状态为动画结束或开始的状态，结束时状态优先

 

## 57、说说对transition的了解

***\*transition是一个选择器的属性监听器\****

***\**\*实现原理的猜想：\*\**\***

***\*当\*******\*css的选择器中的transition监听的\*******\*属性被渲染到CSS样式所对应的DOM模型\*******\*时\*******\*，transition对渲染过程使用定时器结合物理定理运动函数的公式实现了一个样式被渲染到模型的过度动画\****

***\*我把那个DOM比作现实中的模板，样式比作原料，过度就是让机器瞬间完成原料涂上去变为缓慢涂上去\****

![img](https://img-blog.csdnimg.cn/20190528181658563.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4NjAzNDM3,size_16,color_FFFFFF,t_70)

## 58、H5新特性有哪些？

​	HTML5 中的一些有趣的新特性：

- 用于绘画的 canvas 元素
- 用于媒介回放的 video 和 audio 元素
- 对本地离线存储的更好的支持
- 新的特殊内容元素，比如 article、footer、header、nav、section
- 新的表单控件，比如 calendar、date、time、email、url、search

## 59    canvas如何绘制一个三角形/圆角矩形 

绘制三角形

```js
var draw = function(x1, y1, x2, y2, x3, y3, color, type) {
    ctx.beginPath();
    ctx.moveTo(x1, y1);
    ctx.lineTo(x2, y2);
    ctx.lineTo(x3, y3);
    ctx[type + 'Style'] = color;
    ctx.closePath();
    ctx[type]();
}
```

`参数解释`：x1(2、3),y1(2、3)-三角形的三个点的坐标；color-绘制颜色；type-绘制类型（'fill'和'stroke'）。

实例如下图所示：

![img](https://files.cnblogs.com/files/jarson-7426/sanjiao.gif)

绘制（圆角）矩形

```js
var draw = function(x, y, width, height, radius, color, type){
    ctx.beginPath();
    ctx.moveTo(x, y+radius);
    ctx.lineTo(x, y+height-radius);
    ctx.quadraticCurveTo(x, y+height, x+radius, y+height);
    ctx.lineTo(x+width-radius, y+height);
    ctx.quadraticCurveTo(x+width, y+height, x+width, y+height-radius);
    ctx.lineTo(x+width, y+radius);
    ctx.quadraticCurveTo(x+width, y, x+width-radius, y);
    ctx.lineTo(x+radius, y);
    ctx.quadraticCurveTo(x, y, x, y+radius);
    ctx[type + 'Style'] = color || params.color;
    ctx.closePath();
    ctx[type]();
}
```

`参数解释`：x,y-左上角点的坐标；width、height-宽高；radius-圆角；color-绘制颜色；type-绘制类型（'fill'和'stroke'）。

实例如下图所示：

![img](https://files.cnblogs.com/files/jarson-7426/rect.gif)

## 60、CSS清除浮动的几种方式 简略回答即可

在非IE浏览器（如Firefox）下，当容器的高度为auto，且容器的内容中有浮动（float为left或right）的元素，在这种情况下，容器的高度不能自动伸长以适应内容的高度，使得内容溢出到容器外面而影响（甚至破坏）布局的现象。这个现象叫浮动溢出，为了防止这个现象的出现而进行的CSS处理，就叫CSS清除浮动。

**1、父级div定义伪类：after和zoom**

![img](https://pic3.zhimg.com/80/v2-a1036e4343cfab5edd05b6bba710cb86_720w.jpg)

- 原理：IE8以上和非IE浏览器才支持：after，原理和方法2有点类似，zoom（IE转有属性）可解决IE6，ie7浮动问题。
- 优点：浏览器支持好，不容易出现怪问题（目前：大型网站都有使用，如：腾讯，网易，新浪等等）
- 缺点：代码多，不少初学者不理解原理，要两句代码结合使用，才能让主流浏览器都支持
- 建议：推荐使用，建议定义公共类，以减少CSS代码

**2、在结尾处添加空div标签clear:both**

![img](https://pic3.zhimg.com/80/v2-6734229fd224b259d863749a5d9e4cde_720w.jpg)

- 原理：添加一个空div，利用css提高的clear:both清楚浮动，让父级div能自动获取到高度
- 优点：简单、代码少、浏览器支持好、不容易出现怪问题
- 缺点：不少初学者不理解原理；如果页面浮动布局多，就要增加很多空div，让人感觉很不爽
- 建议：不推荐使用，但此方法是以前主要使用的一种消除浮动的方法

**3、父级div定义height**

![img](https://pic3.zhimg.com/80/v2-32cc0b3fd2c74b1962264f02c400bc7e_720w.jpg)

- 原理：父级div手动定义height，就解决了父级div无法自动获取到高度的问题
- 有点：简单，代码少，容易掌握
- 缺点：只合适高度固定的布局，要给出精确的高度，如果高度和父级div不一样时，会产生问题
- 建议：不推荐使用，之间以高度固定的布局时使用

**4、父级div定义overflow:hidden**

![img](https://pic3.zhimg.com/80/v2-cf911e59712e5f500842135b681bfafa_720w.jpg)

原理：必须定义width或zoom：1，同时不能定义height，使用overflow:hidden时，浏览器会自动坚持浮动区域的高度

- 优点：简单，代码少，浏览器支持好
- 缺点：不能和position配合使用，因为超出的尺寸的会被隐藏
- 建议：只推荐没有使用position或者对overflow:hidden理解比较深的朋友使用

**5、父级div定义overflow:auto**

![img](https://pic3.zhimg.com/80/v2-c9665327182a2f92e248e8199b475f52_720w.jpg)

- 原理：必须定义width或zoom:1，同时不能定义height，使用overflow:auto时，浏览器会自动检查浮动区域的高度
- 优点：简单，代码少，浏览器支持好
- 缺点：内部宽高超过父级div时，会出现滚动条。
- 建议：不推荐使用，如果你需要出现滚动条或者确保你的代码不会出现滚动条就使用吧。

**6、父级div也一起浮动**

![img](https://pic4.zhimg.com/80/v2-f56c0672a1364eab24fc87bd6b8c4caf_720w.jpg)

- 原理：所有代码一起浮动，就变成了一个整体
- 优点：没有优点
- 缺点：会产生新的浮动问题。
- 建议：不推荐使用，只作了解。

**7、父级div定义display:table**

![img](https://pic4.zhimg.com/80/v2-8af66abeeea8b883411df64fe2e2bed3_720w.jpg)

- 原理：将div属性变成表格
- 优点：没有优点
- 缺点：会产生新的未知问题
- 建议：不推荐使用，只作了解

**8、结尾处加br标签clear:both**

![img](https://pic2.zhimg.com/80/v2-05b8e1ab4972195bb9659a588d2f1de9_720w.jpg)

- 原理：父级div定义zoom:1来解决IE浮动问题，结尾处加br标签clear:both
- 建议：不推荐只用，只作了解

## 61、为什么要初始化CSS样式 

因为浏览器的兼容问题，不同浏览器对有些标签的默认值是不同的，如果没对CSS初始化往往会出现浏览器之间的页面显示差异。

当然，初始化样式会对SEO有一定的影响，但鱼和熊掌不可兼得，但力求影响最小的情况下初始化。

*最简单的初始化方法就是： * {padding: 0; margin: 0;} （不建议）

## 62、CSS3 有哪些新特性 

新增选择器 p:nth-child（n）{color: rgba（255, 0, 0, 0.75）}

弹性盒模型 display: flex;

多列布局 column-count: 5;

媒体查询 @media （max-width: 480px） {.box: {column-count: 1;}}

个性化字体 @font-face{font-family:BorderWeb;src:url（BORDERW0.eot）；}

颜色透明度 color: rgba（255, 0, 0, 0.75）；

圆角 border-radius: 5px;

渐变 background:linear-gradient（red, green, blue）；

阴影 box-shadow:3px 3px 3px rgba（0, 64, 128, 0.3）；

倒影 box-reflect: below 2px;

文字装饰 text-stroke-color: red;

文字溢出 text-overflow:ellipsis;

背景效果 background-size: 100px 100px;

边框效果 border-image:url（bt_blue.png） 0 10;

转换

旋转 transform: rotate（20deg）；

倾斜 transform: skew（150deg, -10deg）；

位移 transform:translate（20px, 20px）；

缩放 transform: scale（。5）；

平滑过渡 transition: all .3s ease-in .1s;

动画 @keyframes anim-1 {50% {border-radius: 50%;}} animation: anim-1 1s;

## 63、解释下CSS sprites，以及你要如何在页面或者网站中使用它

CSS Sprites通常被称为css精灵图， 在国内也被意译为css图片整合和css贴图定位，也有人称他为雪碧图。 就是将导航的背景图，按钮的背景图等有规则的合并成一张背景图，即多张图合 并为一张整图， 然后再利用background-position进行背景图定位的一种技术

为什么需要css sprites

CSS Sprites 并不是一门新的技术了，目前他发展的已经比较成熟，阿里巴巴、百度、谷歌 等各公司的网页中到处都可以发现CSS Sprites 的影子。

他是网页里常见的一种图片应用处理方式，他允许你将一个页面里所涉及到的所=有的零星 的图片都整合到一张大图中去，这样一来，当访问这个页面时，所加载的图片就不会像以前那样 一张一张的慢慢显示出来了，对于当前的网络所流行的速度来说，不超出200kb的单张图片所需 要的加载时间基本是差不多的，节省加载速度的关键不是降低重量，而是减少个数，就因为计算 机都是按照byte计算。页面每显示一张图片都会向服务器发送一次请求。所以，图片越多，所请 求的次数就越多。

为了减少HTTP的请求次数，很多网站的导航背景图、登录框、按钮背景图等使用的并不是 <img>标签，而是CSS Sprite

css sprites的优势

优势：通过整合图片，减少对服务器的请求数量，减少图片的体积从而减轻服务 器的负担，提高网页的加载速度

![img](https://pic1.zhimg.com/80/v2-1d586460993d7f4c694cfa41cef887c8_720w.jpg)

![img](https://pic2.zhimg.com/80/v2-038ea5a7ef805691ee1b21d353b1a50d_720w.jpg)

## 64、a点击出现框，解决方法

```
css中添加
 -webkit-tap-highlight-color: transparent; outline: none;




.pageStr span a {
  border: 1px solid #e4e4e4;
  color: #999;
  cursor: pointer;
  display: inline-block;
  height: 29px;
  line-height: 29px;
  padding: 0 10px;
  text-align: center;
  background:#fff;
   -webkit-tap-highlight-color: transparent; outline: none;
}
```



## 65、如果我不输入<!DOCTYPE HTML>，HTML 5能工作吗？

No，浏览器将无法识别HTML文件，并且HTML 5标签将无法正常工作。

## 66、哪些浏览器支持HTML 5？

几乎所有的浏览器都支持HTML 5，例如Safari，Chrome，火狐，Opera，IE等。

## 67、CSS3中的选择器都有什么？

*通用选择器，ID选择器 ，.类选择器class，标签选择器，标签组合选择器，伪类选择器：，+相邻元素选择器，>子元素选择器，~同辈选择器，
x[title]属性选择器[type="button"]，
x[href^="http"]匹配以href值为http打头的地址,
x[href$=".jpg"]匹配所有的图片链接
input[type=checkbox]:checked{};选择checkbox为当前选中的那个标签。

伪类选择器 ------：
p:empty 选择没有子元素的每个 <p> 元素（包括文本节点）。
:first-child 第一个
:last-child 最后一个
:nth-child(11) 1--11个
x:first/x:after 在x选择器之前或者之后插入内容

伪元素选择器
::before ,::after 通过 css 模拟出来html标签的效果

## 68、CSS3中多列布局的用处是什么？

好用  现在经常做自适应页面，就用的比较多了   

## 69、CSS优先级算法如何计算？

元素选择符： 1
class选择符： 10
id选择符：100
元素标签：1000

1. !important声明的样式优先级最高，如果冲突再进行计算。
2. 如果优先级相同，则选择最后出现的样式。
3. 继承得到的样式的优先级最低。

## 70、HTML5中的本地存储概念 

html5中的Web Storage包括了两种存储方式：sessionStorage和localStorage。
sessionStorage用于本地存储一个会话中的数据，这些数据只有在同一个会话中的页面才能访问并且当会话结束后数据也随之销毁，因此sessionStorage
不是一种持久化的本地存储，仅仅是会话级别的存储。而localStorage用于持久化的本地存储，除非主动删除数据，否则数据是永远不会过期的。
cookie是网站为了标示用户身份而储存在用户本地终端上的数据（通常经过加密）。

## 71、本地存储有生命周期吗？

本地存储没有生命周期，它会一直存在直到用户将其从浏览器清楚或使用JavaScript代码删除它。

## 72、本地存储和Cookies之间的区别是什么？

cookies本地存储客户端/服务器端既可以从客户端也可以从服务器端访问数据。每个请求都会发送cookie数据到服务器。只能在本地浏览器端访问数据。服务器无法访问本地存储，除非特意通过POST或GET发送到服务器。大小每个Cookie 4095个字节。每个域5 MB。有效期cookie有附加的有效期。所以有效期后的cookie和cookie数据会被删除。数据没有有效期限。要么最终用户从浏览器删除它，要么使用JavaScript编程删除。

## 73、WebSQL是什么？WebSQL是HTML 5规范的一部分吗？

WebSQL是客户浏览器端的结构化的关系数据库。这是浏览器内部的本地RDBMS，你可以在这个本地RDBMS上执行SQL查询

## 74、XHTML与HTML的有何异同？

**一、其基础语言不同**

1、XHTML是基于可扩展标bai记语言（XML）。

2、HTML是基于标准通用标记语言（SGML）。

**二、语法严格程度不同**

1、XHTML语法比较严格，存在DTD定义规则。

2、HTML语法要求比较松散，这样对网页编写者来说，比较方便。

**三、可混合应用不同**

1、XHTML可以混合各种XML应用，比如MathML、SVG。

2、HTML不能混合其它XML应用。

**四、大小写敏感度不同**

1、XHTML对大小写敏感，标准的XHTML标签应该使用小写。

2、HTML对大小写不敏感。

五、公布时间不同

1、XHTML是2000年W3C公布发行的。

2、HTML4.01是1999年W3C推荐标准。

## 75、box-sizing属性？

用来控制元素的盒子模型的解析模式，默认为content-box
context-box：W3C的标准盒子模型，设置元素的 height/width 属性指的是content部分的高/宽
border-box：IE传统盒子模型。设置元素的height/width属性指的是border + padding + content部分的高/宽

## 76、Doctype的作用？标准模式与兼容模式各有什么区别？

①<!DOCTYPE>告知浏览器的解析器用什么文档标准解析这个文档。DOCTYPE不存在或格式不正确会导致文档以兼容模式呈现。
 ②标准模式的排版和JS运作模式都是以该浏览器支持的最高标准运行。在兼容模式中，页面以宽松的向后兼容的方式显示,模拟老式浏览器的行为以防止站点无法工作。
 注意点：
 HTML5 只需要写<!DOCTYPEHTML>不需要对DTD进行引用，因为HTML5不基于 SGML，因此不需要对DTD进行引用，但是需要doctype来规范浏览器的行为（让浏览器按照它们应该的方式来运行）。
 而HTML4.01基于SGML,所以需要对DTD进行引用,才能告知浏览器文档所使用的文档类型。



 

## 77、CSS3新增伪类有那些?

p:first-of-type 选择属于其父元素的首个元素
p:last-of-type 选择属于其父元素的最后元素
p:only-of-type 选择属于其父元素唯一的元素
p:only-child 选择属于其父元素的唯一子元素
p:nth-child(2) 选择属于其父元素的第二个子元素
:enabled :disabled 表单控件的禁用状态。
:checked 单选框或复选框被选中。

## 78、介绍一下你对浏览器内核的理解？常见的浏览器内核有哪些？

主要分成两部分：渲染引擎(layout engineer或 Rendering Engine) 和 JS 引擎。

渲染引擎：负责取得网页的内容(html,xml和图像等等)，整理讯息(例如加入css)，以及计算网页的显示方式，输出到显示器或打印机。浏览器的内核的不同对于网页的语法解释会有不同，所以渲染的效果也不同。所有网页浏览器、电子邮件客户端以及它需要编辑、显示网络内容的应用程序都需要内核。

JS引擎：解析和执行JavaScript来实现网页的动态效果。以及交互内容



浏览器内核种类



一.Gecko内核

以Mozilla浏览器为代表，FirefoxGecko是一套开放源代码的、以C++编写的网页排版引擎。Gecko是最流行的排版引擎之一。使用它的比较著名的浏览器是Firefox、Netscape6至9.

二.Trident内核

Trident又被称为MSHTML，是微软开发的一种排版引擎。使用Trident渲染引擎的浏览器主要有：IE、世界之窗浏览器、傲游、Avant、Sleipnir、GreenBrowser、NetCaptor和KKman等。

三.WebKit内核

代表浏览器有Safari、Chromewebkit。是一个开源项目，包含了来自KDE项目和苹果公司的一些组件，主要用于Mac OS系统，特点在于源码结构清晰、渲染速度极快。缺点是对网页代码的兼容性不高，导致一些编写不标准的网页无法正常显示。主要作品有Safari以及Chrome。

四.Presto内核

是由Opera Sofeware开发的浏览器排版引擎，Opera7.0及以上可使用。它取代了旧版Opera4到6使用的Elektra排版引擎，包括加入动态功能，而重新排版。

 



## 79、前端页面有哪三层构成？ 分别是什么？ 作用是什么？

最准确的网页设计思路是把网页分成三个层次，即：结构层、bai样式层、行为层。

HTML：结构层

网页的结构或内容层是该页面的基础HTML代码。正如房屋的框架为房屋的其他部分构建了一个坚实

的基础，HTML的坚实基础创建了一个可以在其上创建网站的平台。

结构层用于存储客户想要阅读或查看的所有内容。HTML结构可以包含文本和图像，它包括访问者用

于浏览网站的超链接。这是在符合标准的HTML5中编码的，可以包括文本，图像和多媒体（视频，音频等）。 

网站内容的每个方面都应该在结构层中表示。这允许关闭JavaScript的客户或无法查看整个网站的

CSS访问权限的客户（如果不是所有功能）。



CSS：样式层

该层指示结构化HTML文档如何看待网站的访问者，并由CSS（层叠样式表）定义。这些文件包含有

关如何在Web浏览器中显示文档的样式说明。样式层通常包括基于屏幕大小和设备更改站点显示的

媒体查询。

网站的所有视觉样式都应位于外部样式表中。您可以使用多个样式表，但请记住，每个CSS文件都需

要HTTP请求才能获取它，从而影响站点性能。 

JavaScript：行为层

行为层使网站具有交互性，允许页面响应用户操作或基于一组条件进行更改。JavaScript是行为层最

常用的语言，但CGI和PHP也经常被使用。

当开发人员引用行为层时，大多数都是指在Web浏览器中直接激活的层。您可以使用此图层直接与

DOM（文档对象模型）进行交互。在内容层中编写有效的HTML对于行为层中的DOM交互非常重

要。在构建行为层时，应该像使用CSS一样使用外部脚本文件来优化速度和性能。

## 80、CSS里的visibility属性有个collapse属性值？在不同浏览器下以后什么区别？

当一个元素的visibility属性被设置成collapse值后，对于一般的元素，它的表现跟hidden是一样的。

1. chrome中，使用collapse值和使用hidden没有区别。
2. firefox，opera和IE，使用collapse值和使用display：none没有什么区别。

## 81、HTML中div与span区别

div和span

1、div和span都可以称为“图层“。
2、图层的作用是为了保证页面可以灵活的布局。相当于“盒子”。
3、div和span是可以定位的，只要确定div的左上角的x轴和y轴坐标即可。
4、最早的网页是采用table进行布局（不灵活，太死板），现代的网页开发中div布局使用最多。
5、div和span的区别？
div独自占用一行（默认情况下，浏览器通常会在其后放置一个换行符）；span不会独自占用一行。

div

1、`<div>`标签定义HTML文档中的一个分隔区块或者一个区域部分。
2、`<div>`标签常用于组合块级元素，以便通过CSS来对这些元素进行格式化。
3、可以对同一个`<div>`标签同时应用class或id属性，但通常情况下我们偏向于只使用其中一种。
4、为了避免麻烦，可以不必为每一个`<div>`标签都加上class或id属性。

span

1、`<span>`元素是无语义的行内元素，它可以**对元素进行分组**，使它们以不同的样式显示。
2、`<span>`标签没有固定的格式表现。当对它应用样式时，它才会产生视觉上的变化。如果不对`<span>`应用样式，那么`<span>`元素中的文本与其他文本不会任何视觉上的差异。
3、`<span>`标签提供了一种将文本的一部分或者文档的一部分独立出来的方式。
4、可以对同一个`<span>`元素应用class或id属性，但是更常见的情况是只应用其中一种。这两者的主要差异是，class用于元素组（类似的元素，或者可以理解为某一类元素），而id用于标识单独的唯一的元素。
5、被`<span>`元素包含的文本，可以使用CSS对它定义样式，或者使用JavaScript对它进行操作。

## 82、iframe的优缺点？

iframe的优点：

1.iframe能够原封不动的把嵌入的网页展现出来。

2.如果有多个网页引用iframe，那么你只需要修改iframe的内容，就可以实现调用的每一个页面内容的更改，方便快捷。

3.网页如果为了统一风格，头部和版本都是一样的，就可以写成一个页面，用iframe来嵌套，可以增加代码的可重用。

4.如果遇到加载缓慢的第三方内容如图标和广告，这些问题可以由iframe来解决。

iframe的缺点：

1.会产生很多页面，不容易管理。

2.iframe框架结构有时会让人感到迷惑，如果框架个数多的话，可能会出现上下、左右滚动条，会分散访问者的注意力，用户体验度差。

3.代码复杂，无法被一些搜索引擎索引到，这一点很关键，现在的搜索引擎爬虫还不能很好的处理iframe中的内容，所以使用iframe会不利于搜索引擎优化。

4.很多的移动设备（PDA 手机）无法完全显示框架，设备兼容性差。

5.iframe框架页面会增加服务器的http请求，对于大型网站是不可取的。

分析了这么多，现在基本上都是用Ajax来代替iframe，所以iframe已经渐渐的退出了前端开发

## 83、请描述一下cookies，sessionStorage 和 localStorage的区别？

⒈localStorage长期存储数据，浏览器关闭数据后不丢失；

⒉sessionStorage数据在浏览器关闭后自动删除；

⒊cookie是网站为了标识用户身份而存储在用户本地终端（Client  Side）上的数据（通常经过加密）。cookie始终在同源的http请求中携带（即使不需要）都会在浏览器和服务器端间来回传递。session storage和local storage不会自动把数据发给服务器，仅在本地保存；

⒋存储大小：cookie数据大小不会超过4K，session storage和local storage虽然也有存储大小的限制，但比cookie大得多，可以达到5M或者更多；

⒌有期时间：local storage存储持久数据，浏览器关闭后数据不丢失，除非自动删除数据。session storage数据在当前浏览器窗口关闭后自动删除。cookie 设置的cookie过期时间之前一直有效，即使窗口或者浏览器关闭；

## 84、重排（reflow）与重绘（repaint）

1. 解析HTML文档，构建DOM树
2. 解析CSS属性，构建CSSOM树
3. 结合DOM树和CSSOM树，构建render树
4. 在render树的基础上进行布局, 计算每个节点的几何结构
5. 把每个节点绘制在屏幕上



一个页面可以简单看成由两个部分组成：

- DOM节点：描述页面的结构
- DOM节点的属性：描述DOM节点如何呈现



我们可以发现Reflow发生在第4步，而Repaint发生在第5步。

**Reflow （重排）**

当涉及到DOM节点的**布局属性**发生变化时，就会重新计算该属性，浏览器会重新描绘相应的元素，此过程叫 **回流**（Reflow）。

**Repaint（重绘）**

当影响DOM元素可见性的属性发生变化 (如 color) 时, 浏览器会重新描绘相应的元素, 此过程称为 **重绘**（Repaint)。因此重排必然会引起重绘。

------

二、引起Repaint和Reflow的一些操作

浏览器在处理重排时，会递归处理DOM节点，所以导致重排的成本高于重绘。

如果你是Web开发者，可能更关注的是哪些具体原因会引起浏览器的重排，下面罗列一下：



1. 调整窗口大小
2. 字体大小
3. 样式表变动
4. 元素内容变化，尤其是输入控件
5. CSS伪类激活，在用户交互过程中发生
6. DOM操作，DOM元素增删、修改
7. width, clientWidth, scrollTop等布局宽高的计算

这些引起回流的操作中，6和7是和JavaScript相关的，所以前端开发人员应该注意2点：

- 避免大量的DOM操作
- 避免过多DOM布局属性的计算

## 85、display:none与visibility：hidden的区别？

display：none 不显示对应的元素，在文档布局中不再分配空间（回流+重绘）
visibility：hidden 隐藏对应元素，在文档布局中仍保留原来的空间（重绘）

## 86、BFC 是什么东西

一、什么是BFC

首先引用一下WC3对[BFC](https://link.zhihu.com/?target=https%3A//www.w3.org/TR/CSS2/visuren.html%23block-formatting)的专业解释

**BFC**（Block Formatting Context）：翻译成中文叫做块级格式化上下文，它决定了元素如何对其内容进行定位，以及与其它元素的关系和相互作用，当涉及到可视化布局时，其提供了一个环境，元素在这个环境中按照一定的规则进行布局排列

换句话说，BFC就是为元素提供一个独立的容器，在该容器里按照一定的规则进行布局排列，该容器内的元素不会影响外部的元素，同理，外部的元素也不会影响内部的元素

二、如何触发BFC

先来了解一下有哪些条件可以触发BFC：

1. `float` 不为 `none`
2. `position` 为 `absolute` 或 `fixed`
3. `overflow` 不为 `visible`
4. `display` 为 `inline-block` 或 `table` 或 `flow-root`

后续的案例中，但凡遇到需要触发BFC的，都可以按照这四个条件来使用

## 87、flex布局有哪些属性

flex是Flexible Box的缩写，意思为“弹性布局”，在使用过程中简单、易用、代码较少，在制作网页的时候经常使用这种方法来进行布局。

在使用的过程中任何一个容器都可以指定为Flex布局.{display:flex},行内元素也可以使用Flex布局.box{display:inline-flex;}。当我们将容器设置为Flex布局以后，容器当中的子元素的float、clear等属性会失效。采用Flex布局的元素，称为Flex容器，他的所有子元素自动成为容器成员，称为Flex项目。

容器可以有如下几个属性：justify-content、align-content、align-items flex-direction、flex-wrap、flex-flow。

flex-direction属性决定主轴的的方向，即容器中项目排列的方向，一般默认的方向是横排列。flex-direction有四个值，分别为row：主轴水平方向，起点在左端，row-reverse：起点在右端，column：主轴为垂直方向，起点在上沿，column-reverse：起点在下沿。

![img](https://pic3.zhimg.com/80/v2-45b2435bcae1cf880c9a36d6c925dfce_720w.jpg)

![img](https://pic2.zhimg.com/80/v2-4a8c16a7f511c07dbc7202159497d9e5_720w.jpg)

![img](https://pic1.zhimg.com/80/v2-a2955cd3fef7a588a45f08772deff5d8_720w.jpg)

![img](https://pic2.zhimg.com/80/v2-0a556a75bb4ccde99cc253b7df613385_720w.jpg)

flex-wrap属性，项目的默认值（nowrap）都在一条线上，wrap换行，第一行在上方，wrap-reverse换行，第一行在下方。

flex-flow是属性flex-direction属性和flex-wrap的简写。默认值（row nowrap）为横向排列不换行。.box{flex-flow:flex-direction flex-wrap}。

.box{justify-content:flex-start|flex-end|center|space-between|sp-ace-around},这几个值的意思分别为左对齐，右对齐，居中，两端对齐，两端对齐但是项目边上的元素和容器之间有空隙。他的默认值为左对齐。

align-items属性有五个值，flex-start：交叉轴的起点对齐，flex-end：终点对齐，center：中点对齐，baseline：项目的第一行文字基线对齐，stretch：当项目未设置高度的时候，占满这个容器的高度。

align-content属性定义了多根轴线的对齐方式。当项目只有一根轴线的时候，该属性是不起作用的。flex-start：交叉轴的起点对齐，flex-end：终点对齐，center：中点对齐，space-between：与交叉轴的两端对齐，轴线之间的间隔平均分布，space-around：每根轴线两侧的间隔都相等，因此，轴线之间的间隔比轴线与边框的间隔大一倍，strentch：轴线占满整个交叉轴。

## 88、对BFC规范(块级格式化上下文：block formatting context)的理解？

BFC规定了内部的Block Box如何布局。
定位方案：

1. 内部的Box会在垂直方向上一个接一个放置。
2. Box垂直方向的距离由margin决定，属于同一个BFC的两个相邻Box的margin会发生重叠。
3. 每个元素的margin box 的左边，与包含块border box的左边相接触。
4. BFC的区域不会与float box重叠。
5. BFC是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。
6. 计算BFC的高度时，浮动元素也会参与计算。

满足下列条件之一就可触发BFC

1. 根元素，即html
2. float的值不为none（默认）
3. overflow的值不为visible（默认）
4. display的值为inline-block、table-cell、table-caption
5. position的值为absolute或fixed

## 89、CSS优化、提高性能的方法有哪些(简短回答)？

1. 避免过度约束
2. 避免后代选择符
3. 避免链式选择符
4. 使用紧凑的语法
5. 避免不必要的命名空间
6. 避免不必要的重复
7. 最好使用表示语义的名字。一个好的类名应该是描述他是什么而不是像什么
8. 避免！important，可以选择其他选择器
9. 尽可能的精简规则，你可以合并不同类里的重复规则



## 90、浏览器是怎样解析CSS选择器的？



CSS选择器的解析是从右向左解析的。若从左向右的匹配，发现不符合规则，需要进行回溯，会损失很多性能。若从右向左匹配，先找到所有的最右节点，对于每一个节点，向上寻找其父节点直到找到根元素或满足条件的匹配规则，则结束这个分支的遍历。两种匹配规则的性能差别很大，是因为从右向左的匹配在第一步就筛选掉了大量的不符合条件的最右节点（叶子节点），而从左向右的匹配规则的性能都浪费在了失败的查找上面。
而在 CSS 解析完毕后，需要将解析的结果与 DOM Tree 的内容一起进行分析建立一棵 Render Tree，最终用来进行绘图。在建立 Render Tree 时（WebKit 中的「Attachment」过程），浏览器就要为每个 DOM Tree 中的元素根据 CSS 的解析结果（Style Rules）来确定生成怎样的 Render Tree。



## 91、单行或者多行文本溢出展示省略号的实现方法



如果实现单行文本的溢出显示省略号同学们应该都知道用text-overflow:ellipsis属性来，当然还需要加宽度width属来兼容部分浏览。

**实现方法：**

```
overflow: hidden;
text-overflow:ellipsis;
white-space: nowrap;
```

**效果如图：**
![dome1](http://www.daqianduan.com/wp-content/uploads/2015/10/dome1.png)

但是这个属性只支持单行文本的溢出显示省略号，如果我们要实现多行文本溢出显示省略号呢。

接下来重点说一说多行文本溢出显示省略号，如下。

**实现方法：**

```
display: -webkit-box;
-webkit-box-orient: vertical;
-webkit-line-clamp: 3;
overflow: hidden;
```

**效果如图：**
![dome2](http://www.daqianduan.com/wp-content/uploads/2015/10/dome2.png)

**适用范围：**
因使用了WebKit的CSS扩展属性，该方法适用于WebKit浏览器及移动端；

注：

1. -webkit-line-clamp用来限制在一个块元素显示的文本的行数。 为了实现该效果，它需要组合其他的WebKit属性。常见结合属性：
2. display: -webkit-box; 必须结合的属性 ，将对象作为弹性伸缩盒子模型显示 。
3. -webkit-box-orient 必须结合的属性 ，设置或检索伸缩盒对象的子元素的排列方式 。

**实现方法：**

```
p{position: relative; line-height: 20px; max-height: 40px;overflow: hidden;}
p::after{content: "..."; position: absolute; bottom: 0; right: 0; padding-left: 40px;
background: -webkit-linear-gradient(left, transparent, #fff 55%);
background: -o-linear-gradient(right, transparent, #fff 55%);
background: -moz-linear-gradient(right, transparent, #fff 55%);
background: linear-gradient(to right, transparent, #fff 55%);
}
```

**效果如图：**
![dome3](http://www.daqianduan.com/wp-content/uploads/2015/10/dome3.png)
**适用范围：**
该方法适用范围广，但文字未超出行的情况下也会出现省略号,可结合js优化该方法。

注：

1. 将height设置为line-height的整数倍，防止超出的文字露出。
2. 给p::after添加渐变背景可避免文字只显示一半。
3. 由于ie6-7不显示content内容，所以要添加标签兼容ie6-7（如：<span>…<span/>）；兼容ie8需要将::after替换成:after。

## 92、display:none 和visbility:hidden的区别是什么啊



使用css让元素不可见的方法有很多种，裁剪、定位到屏幕外边、透明度变换等都是可以的。但是最常用两种方式就是设置元素样式为**display: none**或者**visibility: hidden**。很多公司的面试官也常常会问面试者这两者之间的区别。

display与元素的隐藏

如果给一个元素设置了display: none，那么该元素以及它的所有后代元素都会隐藏，它是前端开发人员使用频率最高的一种隐藏方式。隐藏后的元素无法点击，无法使用屏幕阅读器等辅助设备访问，占据的空间消失。  

```html
<body>



    <div>



        <strong>给元素设置display:none样式</strong>



        <p>A元素</p>



        <p style='display:none;'>B元素</p>



        <p>C元素</p>



    </div>



</body>
```

**效果图：**

![img](https://img-blog.csdn.net/20180624205724765?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4MTI4MTc5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 

visibility与元素的隐藏

给元素设置visibility: hidden也可以隐藏这个元素，但是隐藏元素仍需占用与未隐藏时一样的空间，也就是说虽然元素不可见了，但是仍然会影响页面布局。

```html
<body>



    <div>



        <strong>给元素设置visibility:hidden样式</strong>



        <p>A元素</p>



        <p style='visibility:hidden;'>B元素</p>



        <p>C元素</p>



    </div>



</body>
```

**效果图:**

![img](https://img-blog.csdn.net/20180624221002494)


 

display: none与visibility: hidden的区别

很多前端的同学认为visibility: hidden和display: none的区别仅仅在于display: none隐藏后的元素不占据任何空间，而visibility: hidden隐藏后的元素空间依旧保留 ，实际上没那么简单，visibility是一个非常有故事性的属性

1、visibility具有继承性，给父元素设置visibility:hidden;子元素也会继承这个属性。但是如果重新给子元素设置visibility: visible,则子元素又会显示出来。这个和display: none有着质的区别

2、visibility: hidden不会影响计数器的计数，如图所示，visibility: hidden虽然让一个元素不见了，但是其计数器仍在运行。这和display: none完全不一样

```html
    <body>



        <div>



            <strong>给元素设置visibility:hidden样式</strong>



            <ol>



                <li>元素1</li>



                <li style="visibility:hidden;">元素2</li>



                <li>元素3</li>



                <li>元素4</li>



            </ol>



        </div>



        <div>



            <strong>给元素设置display:none样式</strong>



            <ol>



                <li>元素1</li>



                <li style="display:none;">元素2</li>



                <li>元素3</li>



                <li>元素4</li>



            </ol>



        </div>



    </body>
```

![img](https://img-blog.csdn.net/20180624222342801)

3、CSS3的transition支持visibility属性，但是并不支持display，由于transition可以延迟执行，因此可以配合visibility使用纯css实现hover延时显示效果。提高用户体验。





##  







## 

