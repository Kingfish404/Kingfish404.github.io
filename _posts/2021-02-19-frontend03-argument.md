---
layout: post        # page:单页面,不在归档索引;post:有前后页面，索引;encrypted:放protected文件夹中的加密文档
title: 前端笔记-JS函数参数深究，类比Python     # 标题
date: 2021-02-19    # 日期
categories:         # 集合,暂未使用
author:  jinyu      # 作者
tags: [前端, 笔记]           # 标签，博客分类页面显示
comments: true      # bool,是否可以评论
toc:                # bool,是否有目录
pinned:             # bool,是否置顶
description: parameter是函数定义时的参数，argument是函数调用时的参数。传值调用，传引用调用。
hide: 
---

之前一直没有细看JavaScript这一部分，开发时感觉有点不自信，所以找了几篇资料去看了下，顺便现在写写。

之前也写过挺长时间Python的，Pyhton函数中的传值也有点不自信，虽然在构建深度学习的BP网络时，总是很自然的直接认为参数可以影响外面的值，即传引用调用，而在用一般类型时，认为是传值调用，但是，还是对原理不够清楚，所以现在也一起整理下，系统性的总节。

<!-- more -->

## 调用方式

调用方式主要有两种，分别是**传值调用（Call-by-value）**和**传引用调用（Call-by-reference）**，实际的编译器实现中，还有很多其他的种类。

#### 传值调用（Pass by value）

在传值调用中，传递给函数参数是函数被调用时所传实参的拷贝。在传值调用中实际参数被求值，其值被绑定到函数中对应的变量上（通常是把值复制到新内存区域）。cpp中默认的函数传递方式就是如此`int main(int argc)`,这里的`argc`就是传值调用。

#### 传引用调用（Pass by reference）

在传引用调用调用中，传递给函数的是它的实际参数的隐式引用而不是实参的拷贝。通常函数能够修改这些参数（比如赋值），而且改变对于调用者是可见的。cpp中`void fun(int &num)`，这里的`num`就是传引用调用。

cpp🐂，手术刀级别的操控还是舒服，当然也需要编写者自身水平比较高。

引用类型调用（Nicholas）表示如下：

![引用类型内存分配](https://i.loli.net/2021/02/19/TtGicokX6OunYPN.png)

除了上述两种外，实际上，还有一种调用方式，**传共享调用（Call by sharing）**。

####  传共享调用（Call by sharing）

传共享调用和传引用调用的不同之处是，该求值策略传递给函数的参数是对象的引用的拷贝，即对象变量指针的拷贝。也有说法，说这也是传值调用，只不过对于非基本类型，传的是对象的引用，对于基本类型，直接传值。

也就是说如下方代码 `changeStuff` 函数内的 `a` `b` `c` 都分别与 `num` `obj1` `obj2` 指向同一块内存，但不是其拷贝。函数内对 `a` `b` `c` 所做的任何修改，都将反映到 `num` `obj1` `obj2` 上 。不过对于JavaScript，如果实参类型为默认的基本类型，那么就是值拷贝。

```javascript
function changeStuff(a, b, c) {
  a = a * 10;               // 对 a 赋值，修改 a 的指向，新的值是 a * 10
  b.item = "changed";       // 因为 b 与 obj1 指向同一个对象，所以这里会修改原始对象 obj1.item 的内容
  c = {item: "changed"};    // 对 c 重新赋值，修改 c 的指向，其指向的对象内容是 {item: "changed"}
}

var num = 10;
var obj1 = {item: "unchanged"};
var obj2 = {item: "unchanged"};

changeStuff(num, obj1, obj2);

console.log(num);
console.log(obj1.item);    
console.log(obj2.item);
// 10
// changed
// unchanged
```

JS 引用类型变量的值是一个指针，指向堆内存中的实际对象。

这点让我想到了Python，感觉这俩这地方挺像的，Python也是**共享传参**，所以我又去review了下Python基础。

## Pyhton的对象

Pyhton中所有的变量实际上都是指向变量的内存地址，并且这个地址可以通过`id`函数来查找，而且Python在变量内存分配时，还会有许多有意思的地方，类似下方：

```python
a = 1
id(a)   # 4498704688
b = 1
id(b)   # 4498704688
b = 2
id(b)   # 4498704720
c = 1
id(c)   # 4498704688   和a的地址相同
```

从上面我们就可以发现，Python里同样对象使用的是相同的内存空间，且新生成对象时，会优先去已经分配的空间查找是否已经有存在的，如果不存在，再新建空间存放新对象，否则就是直接建一个新引用。

当然上述表述只对Pyhton的不可变对象有效。

### 可变对象和不可变对象

可变对象与不可变对象的区别在于对象本身是否可变。

python内置的一些类型中

- 可变对象：list dict set
- 不可变对象：tuple string int float bool

### Python函数参数

Python函数参数**都只能是引用传递**，所有的变量都是引用，指向对象的地址，并且只有以下两种类型的对象。

**不可变对象**，修改变量值实际上都是新分配了空间，然后改变引用的地址，所以函数内引用的地址发生改变，表象就是变量值变了，但是函数外任然还是引用原值的地址。

**可变对象**，修改变量值是直接操作地址上的值，并没有新分配内存空间，所以函数内修改变量，函数外也会受到影响。

自定义类通常是可变的。要模拟类中的不变性，生成不可变对象，应该覆盖属性设置和删除以引发异常。



> 跑题了，跑题了，这里主题应该是JavaScript，现在回来



## JS代码分析

还是上面那段代码

```javascript
function changeStuff(a, b, c) {
  a = a * 10;               // 对 a 赋值，修改 a 的指向，新的值是 a * 10
  b.item = "changed";       // 因为 b 与 obj1 指向同一个对象，所以这里会修改原始对象 obj1.item 的内容
  c = {item: "changed"};    // 对 c 重新赋值，修改 c 的指向，其指向的对象内容是 {item: "changed"}
}

var num = 10;
var obj1 = {item: "unchanged"};
var obj2 = {item: "unchanged"};

changeStuff(num, obj1, obj2);

console.log(num);
console.log(obj1.item);    
console.log(obj2.item);
// 10
// changed
// unchanged
```

### 变量初始化

```javascript
var num = 10;
var obj1 = {item: "unchanged"};
var obj2 = {item: "unchanged"};
```

![变量初始化](https://i.loli.net/2021/02/19/9b4rtpQGZ8U7SkR.png)

### 调用函数

```javascript
changeStuff(num, obj1, obj2);
```

![调用函数时的内存分配](https://i.loli.net/2021/02/19/35FSHtVZguDKikj.png)

可以看到，变量 `a` 的值就是 `num` 值的拷贝，变量 `b` `c` 分别是 `obj1` `obj2` 的指针的拷贝。

函数的参数其实就是函数作用域内部的变量，函数执行完之后就会销毁。

### 执行函数体

```JavaScript
a = a * 10;
b.item = "changed";
c = {item: "changed"};
```

![执行函数体](https://i.loli.net/2021/02/19/qj28Kl4k6DRfzhV.png)

如图所示，变量 `a` 的值的改变，并不会影响变量 `num`。

而 `b` 因为和 `obj1` 是指向同一个对象，所以使用 `b.item = "changed";` 修改对象的值，会造成 `obj1` 的值也随之改变。

由于是对 `c` 重新赋值了，所以修改 `c` 的对象的值，并不会影响到 `obj2`。

## 结论

从上面的例子可以看出，对于 JS 来说：

- **基本类型是传值调用**
- **引用类型传共享调用**

传值调用本质上传递的是变量的值的拷贝。

传共享调用本质上是传递对象的指针的拷贝，其指针也是变量的值。所以传共享调用也可以说是传值调用。

## REFERENCE

* [Python实例一个类背后发生了什么](https://segmentfault.com/a/1190000004420386)
* [JavaScript 是传值调用还是传引用调用？](https://github.com/nodejh/nodejh.github.io/issues/32)
* [Python可变对象与不可变对象](https://zhuanlan.zhihu.com/p/34395671)
* [如何在Python中创建不可变对象？](https://www.codenong.com/4828080/)

