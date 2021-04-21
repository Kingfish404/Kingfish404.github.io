---
layout: post        # page:单页面,不在归档索引;post:有前后页面，索引;encrypted:放protected文件夹中的加密文档
title: 前端笔记 - 优化    # 标题
date: 2021-02-13    # 日期
categories:         # 集合,暂未使用
author:  jinyu      # 作者
tags: [前端,笔记]              # 标签，博客分类页面显示
comments: true          # bool,是否可以评论
toc: true               # bool,是否有目录
pinned: true            # bool,是否置顶
desc: 新年的第一篇，2021继续学习。前几天给博客上了代码压缩，Lighthouse测了下，感觉不错，就来写下性能优化
hide: 
---

前端性能优化主要有两个点可以写，一个是性能指标如FCP，一个就是优化手段如Lazy load，SSR，compres等。

<!-- more -->

## 博客压缩优化

这次给博客上代码压缩，实际上遇到了一些坑，不过这些坑其实是属于调用的compress库[http://jch.penibelst.de](http://jch.penibelst.de)的问题，但是我感觉在日后的开发中为了兼容性也可以注意。

编写代码时遵守建议的代码规范很重要！很重要！很重要！对于防止出现预期之外的结果有很大帮助，可以节约排错时间。（算了，就是自己菜）

这里遇到的问题有，JS代码无法执行，后来发现是JS代码部分没有加`;`作为行的结尾，平时浏览器会自动为我们加上，不会报错，但是如果进行了代码压缩，代码压缩库没有注意到这一点，就会导致部分代码出现预期之外的结果。

代码注释也尽量要采用`/* content */`的形式，而不是`// content`这种形式，在不严谨的压缩中也会出问题。

例子如下：

```javascript
/* before compress */
var i=0,j=1
console.log(i)
/* 代码压缩前，由于分行的存在，上述代码不会出问题，但是如果压缩后，压缩库存在问题，就会出现以下问题 */

/* after compress */
var i=0,j=1 console.log(i)
/* 显然上述代码无法正常运行，会报`Uncaught SyntaxError: Unexpected identifier`错误
```

## 性能指标

性能指标主要关注的几个点列下来如下：

* FCP（First Contentful Paint) - 第一次出现内容的时间，或者说白屏时间
* LCP (Largest Contentful Paint) - 最大块内容绘制时间，对于用户而言，LCP的感受更加直观
* TBT (Total Blocking Time) - FCP和TTI(Time to Interactive)，主线程的繁忙时间

我的当前博客部署在Github，很多内容也没用上cdn，所以实际的加载效果并不是特别好，LCP time挺长的，不过除此之外也还是有可以优化的地方（懒了，暂时不搞）。

## Lighthouse

Lighthouse，一个开源自动化工具，开源分析页面性能，并且给出修改建议，目前微软的新Edge内置了这个工具，用起来也是非常的方便。

下图是我给我自己的博客用Lighthouse进行的分析，看起来性能优化的还是不错的。

![image.png](https://i.loli.net/2021/02/13/QMhsl2ba93YL1wx.png)

## Chrome dev tool

用Chrome dev tool中的性能分析工具，可以非常精细的定位到问题的位置，具体可以自行f12看看叭（应该没有搞前端的不用chromium系的浏览器吧，哈哈哈），当然Firefox的调试工具也用起来很方便（性感可爱的小狐狸）。

具体参考文档：[https://developers.google.com/web/tools/chrome-devtools/evaluate-performance](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance)

## 优化手段

### 懒加载

许多博客通常会写通过监听onScroll事件来做懒加载，但是现在浏览器也有新的通用api来做这个事情，这个api就是`Intersection Observer API`。

Intersection Observer API提供了一种异步检测目标元素与祖先元素或 viewport 相交情况变化的方法。在MDN上对这个api进行了非常详尽的解释，我这里简单写下吧。

Intersection Observer API的实现方式就是观察者模式，当root元素与设备视窗或者其他指定元素发生交集时执行自行设置的回调函数，当然第一次监听目标元素时也会调用回调函数。

```javascript
// 简单的调用例子
const observer = new IntersectionObserver(
    // 相交时出发回调
    (entries) =>{
        entries.forEach((entry) => {
            console.log(entry.target);
        });
    },
    {
        // 目标元素的父级元素，用于检查目标可见性，默认为浏览器视窗
        root: document.querySelector('#scrollArea'),
        // root元素的外边距
        rootMargin: '0px',
        // 观察者和root相重叠多少算相交
        threshold: 1
    }
);

observer.observe(div);      // 设置观察者的目标
observer.observe(image);    // 同上
```

### 节流，防抖

节流和防抖是闭包的典型应用，通常用于对请求的频率进行控制。

* 防抖 debounce
    - 延迟一定时间执行函数，如果规定函数内再次调用，则重新计时并延迟调用；
    - 适用于交互停止时才触发的操作，比如请求预判，要发ajex请求那种，输入反馈

* 节流 throttle
    - 在规定时间内控制调用函数的频率，一般一个时间段内只出发一次，核心是频率控制，类似游戏的锁帧。


```javascript
// 防抖 - 实现方法1
// 需要实例化debounce
function debounce(fn,delay)
{
    let timer = null;
    return function () {
        let that = this, args = arguments;
        clearTimeout(timer);
        timer = setTimeout(() => {
            timer = null;
            fn.apply(that, args);
        },delay);
    }
}

let debounce_fn = debounce(target_fn,1000);

// 防抖 - 实现方法2
// 可以直接使用debounce
function debounce(fn,delay)
{
    clearTimeout(fn.timer);
    fn.timer = setTImeout(() => {
        fn();
    },delay);
}
```

``` javascript
// 节流 - 需要实例化

// 立即执行版本-时间段开始时执行
function throttle(fn,time)
{
    let isRuning=false;
    return function(){
        if(isRuning){
          return;
        }else{
          isRuning=true;
          setTimeout(()=>{
            fn.apply(this,arguments);
            isRuning=false;
            setTimeout(()=>{
              isRuning=false;
            },time);
          },0)
        }
    }
}


// 延迟执行版本-时间段结束时执行
function throttle(fn,time)
{
    let isRuning=flase;
    return function(){
        if(isRuning){
          return;
        }else{
          setTimeout(()=>{
            fn.apply(this,arguments);
            isRuning=false;
          },time)
        }
    }
}
var thr = throttle(target_fn,1000);
```

### 打包优化

将代码分割成各种捆绑包，按需要加载或者并行加载。可以可以用于实现较小的包和控制资源负载优先级，如果使用正确，可以对负载时间产生重大影响。

这里就有一种非常常用但是我暂时并没有自己配置过的工具，webpack。日后自己研究下。

### 预加载

预加载是一种非常高效的性能优化方法，主要基于link标签来进行实现，link标签不仅仅可以加载css，还能通过设置不同的`rel`来做预加载，下方是一个通过pre提前建立tcp连接的实例，剩余的具体类别介绍也如下：

```html
<link rel="preconnect" href="https//www.example.com/favicon.ico" />
```

* preconncet
    - 提前建立好tcp连接
    - 建立静态资源的CDN域名，跨域请求的域名
* proload
    - 高优先级预加载资源，专注于当前的页面
    - 提前请求下一级页面及其资源，如果预先加载的对象是一个页面，那么返回相应的表示课缓存的响应头
* prefetch
    - 低优先级资源的提示
    - 允许浏览器在后台（空闲时）获取将来可能用得到的资源，并且将他们存储在浏览器的缓存中
* prerendering
    - 在后台提前渲染整个页面，以及所有的资源
    - 提前加载可能预期的下一个页面

### SSR & SSG 

SSR（Servier Side Render），或者又称服务端渲染。在服务器就提前吧页面内容拼接好再发给用户，降低LCP时间。

感觉Jekyll搭建的博客本身也属于服务端渲染的一种，或者更加进一步的说，是SSG（Static Site Generator）。

在做SSG时，就可以提前构建静态页面，压缩页面内容，非常适用于内容不经常变化的网站。像我博客这种经常咕咕咕的页面，用SSG就非常合适了。

### PWA

PWA（Progressive Web App），可以让Web应用获得更加接近原生应用的体验，使用React开发应用时，public下自动生成的manifest.json文件就是PWA应用的配置文件之一。

这里暂时没有仔细去研究，日后有需求了再看叭。

## Reference

* [https://developer.mozilla.org/](https://developer.mozilla.org/)
* [http://jch.penibelst.de](http://jch.penibelst.de)
* [https://github.com/fi3ework/blog/issues/32](https://github.com/fi3ework/blog/issues/32)
* [https://developer.mozilla.org/zh-CN/docs/Web/API/Intersection_Observer_API](https://developer.mozilla.org/zh-CN/docs/Web/API/Intersection_Observer_API)
