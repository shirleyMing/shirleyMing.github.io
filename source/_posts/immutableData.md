---
title: Immutable Data
date: 2021-11-30 12:11:06
categories:
---

> 之前对immutable.js没有了解过，抽空整理了相关的知识。本文只是大致介绍一下使用immutable的背景和好处


### 1、 什么是Immutable Data
一旦被创建后， 就不可被更改的数据。

js中的对象一般是可变的(Mutable)的，因为使用了引用赋值，新的对象简单引用了原始对象，改变新的对象将影响了原始对象。

通过使用Immutable Data，可以让我们更容易去处理缓存、回退、数据变化检测等问题，简化我们的开发。

### 2、js中的Immutable Data
在js中， 我们通过 deep clone 来模拟Immutable Data，其实就是每次操作之前对新的数据deep clone 创建一个新的数据。

```
var cloneDeep = require('lodash.clonedeep');

var data = {  
    id: 'data',
    author: {
        name: 'mdemo',
        github: 'https://github.com/demohi'
    }
};

var data1 = cloneDeep(data);

console.log('equal:', data1===data); //false

data1.id = 'data1';  
data1.author.name = 'demohi';

console.log(data.id);// data  
console.log(data1.id);// data1

console.log(data.author.name);//mdemo  
console.log(data1.author.name);//demohi
```
如上面代码所示： 这样创建一个Immutable Data很慢，而且也造成了CPU和内存的消耗， 而immutable.js很好的解决了这个问题。

 ###  3、immutable.js
`immutable.js`是由facebook开源的一个项目，主要是为了解决javascript Immutable Data的问题，通过参考`hash maps tries`和  `vector tries`提供了一种更有效的方式。

简单的来讲，`immutable.js`通过`structural sharing`结构共享 来解决的性能问题。

当我们发生一个set操作的时候，immutable.js会只clone它的父级别以上的部分，其他保持不变，这样大家可以共享同样的部分，可以大大提高性能。
（具体的api可以参照其他文章，这里我就不整理了）
[immutable.js 文档](https://www.jianshu.com/p/0fa8c7456c15)

使用`immutable.js`的好处:
1. 降低可变数据带来的复杂性。
引用赋值虽然节省空间了，但是应用复杂之后，可变状态往往会造成意外的bug。可以通过deepclone解决。
2. 节省空间
因为`immutable.js`使用结构共享，会尽量复用内存
3. Undo/Redo, Copy/Paste ,随意穿越
因为数据每次都不一样，只要把这些数据放在一个数组里存储起来，想回回退到哪里就拿出来，很容易开发出撤销重做这种功能。
4. 拥抱函数式编程
Immutable（持久化数据结构）本身就是函数式编程中的概念。`函数式编程`关心数据的映射，命令式编程关心解决问题的步骤，纯函数式编程比面向对象更适用于前端开发。因为只要输入一致，输出必然一致，这样开发的组件更易于调试和组装。

缺点：学习成本、增加资源文件大小、容易与原声的对象混淆

### 4、react中使用Immutable Data
熟悉React.js的都应该知道，React.js是一个UI = f(states)的框架，为了解决更新的问题，React.js使用了virtual dom，virtual dom通过diff修改dom，来实现高效的dom更新。

但是有一个问题， 当state更新的时候，如果数据没有更改，也会去做虚拟dom的diff， 这样就造成了浪费。

可以直接使用`React.PureComponent`, 它会自动的帮我们进行浅比较(`shallow-compare`),从而控制`shouldComponentUpdate`的返回值。但是，当传入的`props`或`state`不止一层。或者是数组或是对象等，浅比较就失效了，当然我们也可以在 `shouldComponentUpdate() `中使用使用 `deepCopy` 和 `deepCompare `来避免无必要的 `render()`，但 `deepCopy` 和 `deepCompare `一般都是非常耗性能的。这个时候我们就需要 `Immutable`。

(`shouldComponentUpdate`这个方法里return false，react就不会生成新的虚拟DOM，也就不用更新DOM。) 


参考资料:
[immutable.js了解一下](https://www.jianshu.com/p/0fa8c7456c15)
[Immutable 详解及 React 中实践](https://zhuanlan.zhihu.com/p/20295971?columnSlug=purerender)
[使用immutable优化React](https://segmentfault.com/a/1190000010438089)
