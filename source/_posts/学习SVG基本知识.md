---
title: 初识SVG
date: 2022-08-15 21:08:24
categories: SVG
---

**概念:**
>SVG 是一种基于 XML 语法的图像格式，全称是可缩放矢量图（Scalable Vector Graphics）。其他图像格式都是基于像素处理的，SVG 则是属于对图像的形状描述，所以它本质上是文本文件，体积较小，且不管放大多少倍都不会失真。SVG严格遵从XML语法，并用文本格式的描述性语言来描述图像内容。

**注: 矢量图位图的区别**
矢量图也称为面向对象的图像或绘图图像，繁体版本上称之为向量图，是计算机图形学中用点、直线或者多边形等基于数学方程的几何图元表示图像。矢量图形最大的优点是无论放大、缩小或旋转等不会失真；最大的缺点是难以表现色彩层次丰富的逼真图像效果。

位图亦称为点阵图像或栅格图像，是由称作像素（图片元素）的单个点组成的。这些点可以进行不同的排列和染色以构成图样。当放大位图时，可以看见赖以构成整个图像的无数单个方块。文件类型有*.bmp、*.pcx、*.gif、*.jpg、*.tif等

区别：
1、位图表现的色彩比较丰富，可以表现出色彩丰富的图象。而矢量图形色彩不丰富，无法表现逼真的实物，矢量图常常用来表示标识、图标、Logo等简单直接的图像。
2、文件大小： 由于位图表现的色彩比较丰富，所以占用的空间会很大，颜色信息越多，占用空间越大，图像越清晰，占用空间越大；由于矢量图形表现的图像颜色比较单一，所以所占用的空间会很小。(不是对比同一文件的大小)
3、最大的区别，矢量图形与分辨率无关，可以将它缩放到任意大小和以任意分辨率在输出设备上打印出来，都不会影响清晰度，而位图是由一个一个像素点产生，当放大图像时，像素点也放大了，但每个像素点表示的颜色是单一的，所以在位图放大后就会出现平时所见到的马赛克状。

对比图：

![效果图](/images/image4.png)

---
**1. 呈现方式**
* **SVG 文件可以内嵌到网页中，成为 DOM 的一部分**
  ```
    <html>
    <head></head>
    <body>
    <svg id="mysvg"
        xmlns="http://www.w3.org/2000/svg"
        version="1.1"
        viewBox="0 0 800 600"
        preserveAspectRatio="xMidYMid meet">
        <circle id="mycircle" cx="400" cy="300" r="50"  />
    </svg>
    </body>
    </html>
  ```
    
* **独立SVG文件**
  把文本文件保存成以svg为扩展名的文件，例如sun.svg，这样的文件可以直接用浏览器打开浏览，或者用`<img>、<object>、<embed>、<iframe>`等标签插入网页。
   ```
   <img src="./img/circle.svg">
   ```
   或者
   ```
   .icon {
        background: url("./img/circle.svg");
    }
   ```
---
**2. 文档元素介绍**

* svg标签
  svg 代码都放在顶层标签`<svg>`之中
`viewBox`属性的值有四个数字，分别是左上角的横坐标和纵坐标、视口的宽度和高度
  `width`属性和`height`属性： em,ex,px,pt,pc,cm,mm等长度还可以使用"%"
  svg图像默认大小是300像素（宽） x 150像素（高）。
  ```
    <svg width="300" height="100">   
      <circle cx="100" cy="100" r="50" style="stroke: black; fill: red;"/>
    </svg>
  ```
     注：请注意开头的部分xml声明，与svg的命名空间xmlns、版本version等部分，主要是考虑兼容性的问题；这些部分在HTML5中基本都可以不用写了。

**（一）基本图形元素**
* 圆 circle
  fill: 填充色
    stroke: 描边色
    stroke-width：边框粗细
    stroke-linecap:   在开放子路径被设置描边的情况下，用于开放自路径两端的形状
    stroke-dasharray:  创建虚线
    ```
    <circle cx="50" cy="50" r="50" fill="red" stroke="rgb(0,0,0)" stroke-width="3" />
    ```
* 椭圆 ellipse
    ```
    <ellipse cx="60" cy="60" ry="40" rx="20" stroke="black" stroke-width="5" fill="silver"/>
    ```

* 直线 line 
    ```
    <line x1="0" y1="0" x2="200" y2="0" style="stroke:rgb(0,0,0);stroke-width:5" />
    ```
* 折线 polyline
    ```
     <polyline points="3,3 30,28 3,53"  fill="none" stroke="black" />
    ```

* 矩形 rect
    ```
    <rect x="0" y="0" height="100" width="200" style="stroke: #70d5dd; fill: #dd524b" />
    ```
* 多边形 polygon
    ```
    <polygon fill="green" stroke="orange" stroke-width="1" points="830,130 900,130 900,200 830,200 850,150"/>
    ```
* 路径 path 是最通用，最强力的元素了；使用这个元素你可以实现任何其他的图形，不仅包括上面这些基本形状，也可以实现像贝塞尔曲线那样的复杂形状。这个元素控制位置和形状的只有一个参数d。
    d：一系列绘制指令和绘制参数(点)组合成。绘制指令分为绝对坐标指令和相对坐标指令两种，这两种指令使用的字母是一样的，就是大小写不一样，绝对指令使用大写字母，坐标也是绝对坐标；相对指令使用对应的小写字母，点的坐标表示的都是偏移量。
    M：移动到（moveto）将画笔移动到点(x,y)
    L：画直线到（lineto） 画笔从当前的点绘制线段到点(x,y)
    H: 画笔从当前的点绘制水平线段到点(x,y0)
    V: 画笔从当前的点绘制竖直线段到点(x0,y)
    A: 画笔从当前的点绘制一段圆弧到点(x,y)
    C: 参数: x1 y1, x2 y2, x y   画笔从当前的点绘制一段三次贝塞尔曲线到点(x,y)
    S: 参数: x2 y2, x y   特殊版本的三次贝塞尔曲线(省略第一个控制点)
    Q: 参数: x1 y1, x y   绘制二次贝塞尔曲线到点(x,y)
    T: 参数: x1 y1, x y   绘制二次贝塞尔曲线到点(x,y)
    Z：闭合路径
    ```
    <path d="
      M 18,3
      L 46,3
      L 46,40
      L 61,40
      L 32,68
      L 3,40
      L 18,40
      Z
    "></path>
    ```
    *备注：* 贝塞尔曲线是概念？可以去看看相关文章，本质是给定几个点（一般是4个）可以画出一条曲线。

* 文本 text
  ```
  <text x="50" y="25">Hello World</text>
  ```

**（二）解释性元素  desc元素与title元素**
这两个元素都是辅助性的元素，用于解释相关情境；它们的内容都是文本。当SVG文档被渲染的时候，这2个元素不会被渲染到图形中。这个2个元素之间差别不是太大，title在有些实现中是作为提示信息出现的，所以通常title是放到父元素的第一个位置上。

**（三）结构元素**
 * 组合 g元素
  g元素是一种容器，它组合一组相关的图形元素成为一个整体。这样，我们就可以对这个整体进行操作。这个元素通常可以和desc和title元素配合使用，提供文档的结构信息。结构良好的文档通常可读性和渲染效率都不错。

   ```
    <svg width="300" height="100">  
      <g id="group">
         <g id="myCircle1" fill="#53a600">
            <text x="25" y="20">圆形</text>
            <circle cx="50" cy="50" r="20"/>
          </g>
          <g id="myCircle2" fill="#ccc">
             <text x="25" y="20">圆形</text>
             <circle cx="50" cy="50" r="20"/>
          </g>
      </g>
    </svg>
   ```
*注:* g元素是可以嵌套, 组合起来的图形元素就和单个的元素一样，可以给id值，这样，需要的时候(例如动画和重用一组元素)只用引用这个id值就可以了， 组合一组图形元素可以统一设置这组元素的相关属性(fill,stroke,transform等)。

* 模板 symbol元素
symbol元素用于定义图形模板(模板可以包含很多图形)，这个模板可以被use元素实例化。模板的功能与g元素很相似，都是提供一组图形对象，但是也有一些区别。与g元素不同的地方是：
　　1.symbol元素本身是不会被渲染的，只有symbol模板的实例会被渲染。
　　2.symbol元素可以拥有属性viewBox和preserveAspectRatio，这些允许symbol缩放图形元素。
  ```
  <svg xmlns="http://www.w3.org/2000/svg"
     version="1.1" width="5cm" height="5cm">
    <desc>One group of two rectangles</desc>
    <symbol id="group1" fill="red">
      <rect x="10" y="10" width="100" height="60"/>
      <rect x="120" y="10" width="50" height="50"/>
     </symbol>
    <use href="#group1">
  </svg>
  ```
 * 定义  defs元素 
  SVG允许定义一组对象，然后重用这组对象(注意，不仅仅是图形对象)。最常见的例子如定义渐变色，然后再其他的图形对象中赋给fill属性。渐变色定义的时候是不会渲染的，所以这类型的对象可以放到任何地方。重用对于图形对象中也是经常存在的，而且我们也不希望定义的时候直接渲染，而是想在引用的地方渲染，这个可以用defs元素实现
定义渐变色：
    ```
    <svg width="100" height="200">
      <defs>
        <linearGradient id="Gradient01">
          <stop offset="20%" stop-color="#39F" />
          <stop offset="90%" stop-color="#F3F" />
        </linearGradient>
      </defs>
      <rect x="1cm" y="1cm" width="6cm" height="1cm" fill="url(#Gradient01)"  />
    </svg>
   ```
    图形：
    ```
    <svg width="100" height="200">
       <defs>
           <rect id="MyRect" width="60" height="10"/>
        </defs>
        <use x="20" y="10" href="#MyRect" />
    </svg>
   ```
注意区别：其实作为容器对象的g元素、symbol元素、defs元素都不同程度上提供了重用的作用，只不过每个元素的特性可能少许不同：比如g元素是直接渲染的，symbol和defs不会直接渲染，symbol含有viewBox属性，会创建新的视窗。

* use  复制一个形状 
`href`属性指定所要复制的节点，`x`属性和`y`属性是`<use>`左上角的坐标(在原图基础上的平移距离)。另外，还可以指定width和height坐标。
  ```
  <svg width="100" height="100">
    <circle id="myCircle" cx="5" cy="5" r="4"/>
    <use href="#myCircle" x="10" y="0" fill="blue" />
    <use href="#myCircle" x="20" y="0" fill="white" stroke="blue" />
  </svg>
  ```

**渐变元素、阴影、动画等其他元素见下章**

3、渲染顺序
SVG是严格按照定义元素的顺序来渲染的，这个与HTML靠z-index值来控制分层不一样。在SVG中，写在前面的元素先被渲染，写在后面的元素后被渲染。后渲染的元素会覆盖前面的元素，虽然有时候受透明度影响，看起来不是被覆盖的，但是SVG确实是严格按照先后顺序来渲染的。

***To be continued...***
  







