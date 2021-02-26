---
title: 滚动条挤占内容宽度，影响布局
date: 2020-02-26 10:10:35
tags: css
---

>问题: 当容器有一定的高度(设有固定高度或者是最大高度)，但是内容高度不确定，有可能会撑开父容器产生滚动条，也有可能不产生。但是因为滚动条出现，滚动条也需要占位置，容器内容宽度就会被挤压

举个栗子: 

        
    .body {
      height: 800px;
      width: 420px;
    }
    .container {
      padding: 20px
      overflow-y: auto;
    }
    .item {
      width: 90px;
      height: 90px;
      margin-bottom: 10px;
      margin-right: calc(20px / 3); //????这样子设置的有问题
    }
    .item:nth(4n) {
      margin-right: 0px
    }
    <div class="body">
        <div class="container">
          <div class="item">1</div>
          <div class="item">2</div>
          <div class="item">3</div>
          <div class="item">4</div>
          <div class="item">5</div>
          .......
        </div>   
    </div>
    
  没有滚动条的效果图：

![image.png](https://upload-images.jianshu.io/upload_images/12697150-bf768fd63e094ca3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



上面为灰色是`container`灰色块，`item`为里面的小方块，图为四个一排的列表，当`item`超过4个，就会超出父元素高度，`container`就会产生滚动条。要求是灰色容易左右两边留白一样，中间子元素的间距相等。

未产生滚动条`container`容器的内容宽度是420px - 40px = 380px(父元素的宽度减去左右padding的宽度)。剩余的宽度 380px - 360px = 20px  margin-right的值就为 calc(20px / 3)。
但是如果是产生滚动条，内容的宽度要再减去14px, 是366px。如果这样设置margin-right: calc(20px / 3)有滚动条的情况下，第四个元素会挤到第二排。所以`item`的样式margin-right不能给出固定的值。


**解决1. 新属性overlay**
chrome下overflow有个新的属性值overlay，这个属性简直就是为了这个问题而生，他和auto有点像，但是区别就是在触发滚动条时候并不挤压空间。但是非常遗憾，这个属性值目前只有chrome支持

    .container {
        overflow-y: overlay;
    }
    
看起来的效果像是在滚动条显示padding的空白里，没有挤压到内容区域。

有滚动条的效果:
![image.png](https://upload-images.jianshu.io/upload_images/12697150-fd51faeb73ac2495.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**解决2. 使用100%**
设置100%指的是可用宽度，是不含滚动条的宽度,

    .item {
        margin-right: calc((100% - 360px) / 3);
    }

这样子设置之后，在有滚动条和滚动条时，margin-right计算的值不同。
有滚动条的效果：
![image.png](https://upload-images.jianshu.io/upload_images/12697150-d07f746507854aa3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



