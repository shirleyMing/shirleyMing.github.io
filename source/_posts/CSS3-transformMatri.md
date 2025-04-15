---
title: CSS3 transform Matrix
date: 2020-12-15 21:15:52
categories: CSS
---

> 之前大家在CSS3 动画里用到比较多的`transform`属性，实际上`transform`属性的好多方法都是可以通过`matrix`实现的效果。本文以2D效果举例。

### 1、 transform属性
包括几个常用方法
- skew (斜拉)
- scale (缩放)
- rotate (旋转)
- translate (位移)

上面的几种方法都是应用matrix()方法实现的

### 2、matrix方法
用法
```
transform: matrix(a,b,c,d,e,f);
```
实际上对应的矩阵是 ![矩阵.png](/images/image5.png)
以上矩阵中的1，是2D矩阵不涉及Z轴，所以将Z轴置常数1



转换的公式如下
![转换公式.png](/images/image6.png)

其中x, y表示转换元素的坐标（变量), 运用的是`线性代数`矩阵的乘法，可以得出转换后的坐标了。

举例设置 
```
transform: matrix(1, 0, 0, 1, 100, 100);
```

效果就是往右平移100px的同时往下也平移了100px

### 3、matrix矩阵实现缩放，旋转以及拉伸
推导rotate(βdeg) 
![矩阵推导.png](/images/image7.png)

```
# 由(x,y)旋转到(x',y ')
x' = cos(β+α)*r
y' = sin(β+α)*r

已知公式：
r = √x^2+y^2

和差化积公式：
sin(β+α)= sin(β)cos(α) + cos(β)sin(α)
cos(β+α) = cos(β)cos(α) - sin(β)sin(α)

正弦余弦
sin(α) = y/r
cos(α) = x/r

所以得到
x' = cos(β+α)*r = cos(β)cos(α)r-sin(β)sin(α)*r    = cos(β)x - sin(β)y
y' = sin(β+α)*r = sin(β)*cos(α)*r+cos(β)sin(α)*r  = sin(β)x + cos(β)y

整理出矩阵如下
|cos(β)    - sin(β)  |  * | x |     =   | x'|
| sin(β)    cos(β)   |    | y |         | y'|
```
 1. 上面`matrix`矩阵中的参数分别为
a 水平缩放
b 水平拉伸
c 垂直拉伸
d 垂直缩放
e 水平位移
f 垂直位移

2. 实现默认方法的矩阵可以由一定的数学推导得出
![矩阵.png](/images/image8.png)
缩放：scale(a) 等同于 matrix(a, 0, 0, a, 0, 0);
平移：translate(m, n) 等同于 matrix(1, 0, 0, 1, m, n);
旋转：rotate(ndeg) 等同于 matrix(cos(ndeg), sin(ndeg), -sin(ndeg), cos(ndeg), 0, 0);
拉伸：skew(mdeg, ndeg) 等同于 matrix(1, tan(ndeg), tan(mdeg), 1, 0, 0);

矩阵的优势是可以通过计算，然后将平移旋转等效果整合到一个表达式内。
比如 `transform:  skewX(45deg) scale(1.2)`;

换成矩阵
![矩阵.png](/images/image9.png)
 
最后我们只需要设置 `transform: matrix(1.2,0,1.2,1.2,0,0); `

### 4、用处
虽然有了`matrix`转换矩阵,但是日常中直接使用提供出来的`translate`, `rotate`等方法比较方便， 这比使用矩阵方便的多。
确实对于一般交互，用`transform`默认提供的方法足够了，但是一些其它效果，没有直接方法，这时就需要使用`matrix`方法了。
比如参考文档里张鑫旭的镜像对称，可以用`matrix`矩阵方式，可以做图片的镜像对称。（比如有时候拍照，前置摄像头拍出来的照片是反的，如果有需求还原，可以用这个矩阵的方法复原）

另外在SVG、Canvas、WebGL、CSS 3D中都有应用矩阵的方法。

参考资料和演示demo地址
[CSS3演示地址](https://www.css88.com/tool/css3Preview/Transform-Matrix.html)
[Css高阶用法(一) matrix](https://www.jianshu.com/p/5830fe9d18ed)
[理解CSS3 transform中的Matrix(矩阵)](https://www.zhangxinxu.com/wordpress/2012/06/css3-transform-matrix-%e7%9f%a9%e9%98%b5/)
[MDN matrix](https://developer.mozilla.org/en/docs/Web/SVG/Attribute/transform)


