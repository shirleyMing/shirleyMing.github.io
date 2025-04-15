---
title: React事件机制
date: 2021-1-19 20:12:27
categories: React
---

#### 1、原生Dom的事件流

![image.png](/images/image20.png)
如上图所示：
在JavaScript中，事件的触发实质上是要经过三个阶段:事件捕获、目标对象本身的事件处理和事件冒泡。
*  事件捕获 （由父级元素将事件一直传递到事件发生的元素）
当某个事件触发时，文档根节点最先接受到事件，然后根据DOM树结构向具体绑定事件的元素传递。该阶段为父元素截获事件提供了机会。
事件传递路径为：
window —> document —> body —> div—> text

* 目标阶段
具体元素已经捕获事件后，执行目标事件本身的处理事件。

* 事件冒泡
根据DOM树结构由具体触发事件的元素向根节点传递。正因为事件在DOM的传递经历这样一个过程，从而为`事件委托`提供了可能。
事件传递路径：
text—> div —> body —> document —> window


#### 2、事件委托（事件代理）
事件委托的实质就是将子元素事件的处理委托给父级元素处理。把事件监听器添加到它们的父元素上。事件监听器会分析从子元素冒泡上来的事件，找到它是哪个子元素的事件。

举个栗子：如果我们有一个列表，列表之中有大量的列表项，我们需要在点击列表项的时候响应一个事件

```
<body>
    <div class="lists">
        <div class="list-item">content1</div>
        <div class="list-item">content2</div>
        <div class="list-item">content3</div>
        <div class="list-item">content4</div>
    </div>

    <script>
        //1. 事件代理
        // var listParentNode = document.querySelector('.lists');
        // listParentNode.addEventListener('click', function(e){
        //     console.log(e.target);
        // })

        //新增一个子元素
        // var newItem = document.createElement('div')
        // newItem.innerHTML = 'newConent';
        // newItem.setAttribute('class', 'list-item');
        // listParentNode.appendChild(newItem)

       
        //2. 给每个列表项一一绑定一个函数
        var nodeLists = document.querySelectorAll('.list-item');
        for(var i = 0; i < nodeLists.length; i++) {
            nodeLists[i].addEventListener('click', function(e){
                console.log(e.target)
            })
        }
        //新增一个子元素
        var listParentNode = document.querySelector('.lists');
        var newItem = document.createElement('div')
        newItem.innerHTML = 'newConent';
        newItem.setAttribute('class', 'list-item');
        listParentNode.appendChild(newItem)

</body>
```
**事件委托优点**： 
1. 减少内存的消耗
上面两种方式， 如果给每个列表项一一都绑定一个函数，那对于内存消耗是非常大的，效率上需要消耗很多性能；所以比较好的方法是 点击事件绑定到他的父层，然后在执行事件的时候再去匹配判断目标元素（这样用事件代理的方式绑定事件，新添加的子元素仍然可以执行事件回调函数）。

2.  动态绑定事件
比如上述的例子中列表项就几个，我们给每个列表项都绑定了事件；
在很多时候，我们需要通过 AJAX 或者用户操作动态的增加或者去除列表项元素，那么在每一次改变的时候都需要重新给新增的元素绑定事件，给即将删去的元素解绑事件；
如果用了事件委托就没有这种麻烦了，因为事件是绑定在父层的，和目标元素的增减是没有关系的，执行到目标元素是在真正响应执行事件函数的过程中去匹配的；
所以使用事件在动态绑定事件的情况下是可以减少很多重复工作的。

不适用的情况： 比如 focus、blur 之类的事件本身没有事件冒泡机制，所以无法委托； mousemove、mouseout这样的事件，虽然有事件冒泡，但是只能不断通过位置去计算定位，对性能消耗高，因此也是不适合于事件委托。

#### 3. React事件机制

![image.png](/images/image21.png)

在 [React事件介绍](https://link.zhihu.com/?target=https%3A//reactjs.org/docs/events.html) 中介绍了合成事件对象以及为什么提供合成事件对象，主要原因是因为 React 想实现一个全浏览器的框架， 为了实现这种目标就需要提供全浏览器一致性的事件系统，以此抹平不同浏览器的差异。

合成事件对象很有意思，一开始听名字会觉得很奇怪，看到英文名更奇怪 `SyntheticEvent`， 实际上合成事件的意思就是使用原生事件合成一个 React 事件， 例如使用原生`click`事件合成了`onClick`事件，使用原生`mouseout`事件合成了`onMouseLeave`事件，原生事件和合成事件类型大部分都是一一对应，只有涉及到兼容性问题时我们才需要使用不对应的事件合成。

合成事件是浏览器的原生事件的跨浏览器包装器。除兼容所有浏览器外，它还拥有和浏览器原生事件相同的接口，包括 `stopPropagation()` 和 `preventDefault()`。


当我们在组件上设置事件处理器时，React并不会在该DOM元素上直接绑定事件处理器. React内部自定义了一套事件系统，在这个系统上统一进行事件订阅和分发。

具体来讲，React利用事件委托机制在Document上统一监听DOM事件，再根据触发的target将事件分发到具体的组件实例。另外上面e是一个合成事件对象(SyntheticEvent), 而不是原始的DOM事件对象。

React事件系统实现可以分为两个阶段：事件注册、事件触发
* 事件注册
**React 的事件注册过程主要做了两件事：document 上注册、存储事件回调**。
**document 上注册**
React在组件加载(mount)和更新(update)时, 根据传入组件内的声明的事件类型的属性（onClick、onChange 等），在 document 上注册事件（`document.addEventListener('click', dispatchEvent)`) 并指定统一的回调函数 `dispatchEvent`(不处理具体的事件，仅对事件进行分发)
**存储事件回调**
React 为了在触发事件时可以查找到对应的回调去执行，会把组件内的所有事件统一地存放到一个对象中（listenerBank）。而存储方式如上图，首先会根据事件类型分类存储，例如 click 事件相关的统一存储在一个对象中，回调函数的存储采用键值对（key/value）的方式存储在对象中，key 是组件的唯一标识 (_rootNodeID)，value 对应的就是事件的回调函数。例如
```
{
    click: {
        key1: fn, //key 是组件的唯一标识 (_rootNodeID)
        key2: fn
    },
    change: {
        key3: fn,
        key4: fn
    }
}
```
ReactBrowserEventEmitter作为事件注册入口，担负着事件注册和事件触发。注册事件的回调函数由EventPluginHub来统一管理，根据事件的类型(type)和组件标识(_rootNodeID)为key唯一标识事件并进行存储。

* 事件触发
![事件触发流程.png](/images/image22.png)

其大致流程如下：
1. 触发事件，开始 DOM 事件流，先后经过三个阶段：事件捕获阶段、处于目标阶段和事件冒泡阶段
2. 当事件冒泡到 document 时，触发统一的事件分发函数 ReactEventListener.dispatchEvent
3. dispatchEvent根据原生事件对象（nativeEvent）找到当前节点（即事件触发节点）对应的 ReactDOMComponent 对象
4. 事件的合成 
    (1) 根据当前事件类型生成对应的合成对象
    (2) 封装原生事件对象和冒泡机制
    (3) 查找当前元素以及它所有父级
    (4)在 listenerBank 中查找事件回调函数并合成到 events 中
5. 批量执行合成事件（events）内的回调函数， 
6. 如果没有阻止冒泡，会将继续进行 DOM 事件流的冒泡（从 document 到 window），否则结束事件触发

-----------
简单点的解释为： 
React不会将事件处理函数直接绑定到真实的节点上，而是把所有的事件绑定到结构的最外层，使用一个统一的事件监听器。这个监听器维持了一个映射，保存所有组件内部的事件监听和处理函数。当事件发生时，首先被这个统一的事件监听器处理，然后在映射里找到真正的事件处理函数并调用。

需要注意的是
1. React合成事件的冒泡并不是真的冒泡，而是节点的遍历。 
2. 并不是所有事件都会委托到document上，前面提到几乎所有的事件代理(delegate)到document，几乎说明存在例外的情况。例如对于audio、video标签，存在一些媒体事件(例如onplay、onpause)，而这些事件是document不具有的，那么只能在这些标签上进行事件绑定，绑定一个入口分发函数(dispatchEvent)。

#### 4.  React事件和原生事件有什么区别
关于合成事件
合成事件官方文档：https://react.html.cn/docs/events.html

合成事件作用
1. 对原生事件封装
在事件回调方法,方法中的参数 e，其实不是原生事件对象e而是react包装过的对象，同时原生事件对象被放在了属性 e.nativeEvent内。
官网中介绍到了 SyntheticEvent是react合成事件的基类，定义了合成事件的基础公共属性和方法。react会根据当前的事件类型来使用不同的合成事件对象，比如鼠标单机事件 - SyntheticMouseEvent，焦点事件-SyntheticFocusEvent等，但是都是继承自SyntheticEvent。
2. 对原生事件的升级和改造
对于有些dom元素事件，我们进行事件绑定之后，react并不是只处理你声明的事件类型，还会额外的增加一些其他的事件，帮助我们提升交互的体验。
最典型的例子就是input的`onChange`事件。
![image.png](/images/image23.png)
3. 浏览器事件的兼容处理
react在给document注册事件的时候也是对兼容性做了处理。下面这个代码就是给document注册事件，内部其实也是做了对 ie浏览器的兼容做了处理。
![image.png](/images/image24.png)

 React事件和原生事件主要区别有:
1. React 组件上声明的事件没有绑定在 React 组件对应的原生 DOM 节点上。
2. React 利用事件委托机制，将几乎所有事件的触发代理（delegate）在 document 节点上，事件对象(event)是合成对象(SyntheticEvent)，不是原生事件对象，但通过 nativeEvent 属性访问原生事件对象。
3. 由于 React 的事件委托机制，React 组件对应的原生 DOM 节点上的事件触发时机总是在 React 组件上的事件之前。
4. 原生事件阻止冒泡肯定会阻止合成事件的触发， 合成事件的阻止冒泡不会影响原生事件, 两者最好不要混合使用，避免出现一些奇怪的问题。


合成事件优点： 
1. 减少内存消耗，提升性能，不需要注册那么多的事件了，一种事件类型只在 document 上注册一次
2. 统一规范，解决兼容问题，简化事件逻辑，事件处理程序接收到的是SyntheticEvent的实例。SyntheticEvent完全符合W3C的标准，因此在事件层次上具有浏览器兼容性。
3. React打算干预事件的分发。v16引入Fiber架构，React为了优化用户的交互体验，会干预事件的分发。不同类型的事件有不同的优先级，比如高优先级的事件可以中断渲染，让用户代码可以及时响应用户交互


站在巨人的肩膀上(参考资料)
[dom事件](https://zhuanlan.zhihu.com/p/165089379)
[React事件机制和未来](https://bobi.ink/2019/07/29/react-event/)  这个文章写得好！
