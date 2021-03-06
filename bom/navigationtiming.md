---
title: Navigation Timing
category: bom
layout: page
date: 2013-09-26
modifiedOn: 2013-09-27
---

Navigation Timing是一个提供客户端网页处理信息的API，主要部署在浏览器内置的performance对象上。它可以用来获取，其他手段难以获取的数据。

Chrome 6+、Firefox 7+、IE 9+、Opera 15+支持这个API。

## performance.timing对象

performance对象的timing属性指向一个对象，它包含了各种与性能有关的时间数据，提供浏览器处理网页各个阶段的耗时。比如，performance.timing.navigationStart就是浏览器对当前网页的启动时间。

{% highlight javascript %}

Date.now() - performance.timing.navigationStart
// 13260687

{% endhighlight %}

上面代码表示距离浏览器开始处理当前网页，已经过了13260687毫秒。

performance.timing对象包含以下属性（全部为只读）：

- **navigationStart**：返回当前浏览器窗口的前一个网页结束向用户提示unload时的Unix毫秒时间戳。如果没有前一个网页，则返回值等同于fetchStart属性的值。

- **unloadEventStart**：如果前一个网页与当前网页在同一个域名内，则返回前一个网页的unload事件发生时的Unix毫秒时间戳。如果没有前一个网页，或者之前的网页跳转不是在同一个域名内，则返回值为0。

- **unloadEventEnd**：如果前一个网页与当前网页在同一个域名内，则返回前一个网页unload事件的回调函数结束时的Unix毫秒时间戳。如果没有前一个网页，或者之前的网页跳转不是在同一个域名内，则返回值为0。

- **redirectStart**：返回第一个HTTP跳转开始时的Unix毫秒时间戳。如果没有跳转，或者不是同一个域名内部的跳转，则返回值为0。

- **redirectEnd**：返回最后一个HTTP跳转结束时（即跳转回应的最后一个字节接受完成时）的Unix毫秒时间戳。如果没有跳转，或者不是同一个域名内部的跳转，则返回值为0。

- **fetchStart**：返回浏览器准备使用HTTP请求读取文档时的Unix毫秒时间戳。该事件在网页查询本地缓存之前发生。

- **domainLookupStart**：返回域名查询开始时的Unix毫秒时间戳。如果使用持久连接，或者信息是从本地缓存获取的，则返回值等同于fetchStart属性的值。

- **domainLookupEnd**：返回域名查询结束时的Unix毫秒时间戳。如果使用持久连接，或者信息是从本地缓存获取的，则返回值等同于fetchStart属性的值。

- **connectStart**：返回HTTP请求开始向服务器发送时的Unix毫秒时间戳。如果使用持久连接（persistent connection），则返回值等同于fetchStart属性的值。

- **connectEnd**：返回浏览器与服务器之间的连接建立时的Unix毫秒时间戳。如果建立的是持久连接，则返回值等同于fetchStart属性的值。连接建立指的是所有握手和认证过程全部结束。

- **secureConnectionStart**：返回浏览器与服务器开始安全链接的握手时的Unix毫秒时间戳。如果当前网页不要求安全连接，则返回0。

- **requestStart**：返回浏览器向服务器发出HTTP请求时（或开始读取本地缓存时）的Unix毫秒时间戳。

- **responseStart**：返回浏览器从服务器收到（或从本地缓存读取）第一个字节时的Unix毫秒时间戳。

- **responseEnd**：返回浏览器从服务器收到（或从本地缓存读取）最后一个字节时（如果在此之前HTTP连接已经关闭，则返回关闭时）的Unix毫秒时间戳。

- **domLoading**：返回当前网页DOM结构开始解析时（即Document.readyState属性变为“loading”、相应的readystatechange事件触发时）的Unix毫秒时间戳。

- **domInteractive**：返回当前网页DOM结构结束解析、开始加载内嵌资源时（即Document.readyState属性变为“interactive”、相应的readystatechange事件触发时）的Unix毫秒时间戳。

- **domContentLoadedEventStart**：返回当前网页DOMContentLoaded事件发生时（即DOM结构解析完毕、所有脚本开始运行时）的Unix毫秒时间戳。

- **domContentLoadedEventEnd**：返回当前网页所有需要执行的脚本执行完成时的Unix毫秒时间戳。

- **domComplete**：返回当前网页DOM结构生成时（即Document.readyState属性变为“complete”，以及相应的readystatechange事件发生时）的Unix毫秒时间戳。

- **loadEventStart**：返回当前网页load事件的回调函数开始时的Unix毫秒时间戳。如果该事件还没有发生，返回0。

- **loadEventEnd**：返回当前网页load事件的回调函数运行结束时的Unix毫秒时间戳。如果该事件还没有发生，返回0。

根据上面这些属性，可以计算出网页加载各个阶段的耗时。比如，网页加载的整个耗时计算方法如下：

{% highlight javascript %}

var pt = performance.timing; 
var pageLoadTime = pt.loadEventEnd - pt.navigationStart;

{% endhighlight %}

## performance.navigation对象

performance.navigation对象提供当前网页访问来源的信息。

它有两个属性：

**（1）performance.navigation.type**

该属性返回一个整数值，表示网页的加载来源，可能有以下4种情况：

- **0**：网页通过点击链接、地址栏输入、表单提交、脚本操作等方式加载，相当于常数performance.navigation.TYPE_NAVIGATENEXT。

- **1**：网页通过“重新加载”按钮或者location.reload()方法加载，相当于常数performance.navigation.TYPE_RELOAD。

- **2**：网页通过“前进”或“后退”按钮加载，相当于常数performance.navigation.TYPE_BACK_FORWARD。

- **255**：任何其他来源的加载，相当于常数performance.navigation.TYPE_UNDEFINED。

**（2）performance.navigation.redirectCount **

该属性表示当前网页经过了多少次重定向跳转。

## performance.now方法

performance.now方法返回当前网页，自从performance.timing.navigationStart到当前时间之间的微秒数（毫秒的千分之一）。它的精度可以达到100万分之一秒。

{% highlight javascript %}

performance.now() 
// 23493457.476999998

Date.now() - (performance.timing.navigationStart + performance.now())
// -0.64306640625

{% endhighlight %}

上面代码表示，performance.timing.navigationStart加上performance.now()，近似等于Date.now()，也就是说，Date.now()可以替代performance.now()。但是，前者返回的是毫秒，后者返回的是微秒，所以后者的精度比前者高1000倍。

## 参考链接

- Mozilla Developer Network, [Navigation Timing](https://developer.mozilla.org/en-US/docs/Navigation_timing)
- W3C, [Navigation Timing](http://www.w3.org/TR/navigation-timing/)
- W3C, [HTML5, A vocabulary and associated APIs for HTML and XHTML](http://www.w3.org/TR/html5/browsers.html)
