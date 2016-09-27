---
layout: post
title:  "Javascript学习（一）"
categories: Javascript
comments: true
facebook: true
tags: Chinese
---

《Javascript高级程序设计》第一章~第五章内容学习简单要点提取：

- 函数参数

可以不定义函数参数变量，直接使用arguments对象，或者两者混用。如果改变arguments对象，参数值也会随之改变，这只是单向的。所以修改命名参数不会改变arguments对象。
<!--break-->
{% highlight javascript %}
function doSomething(num1, num2) {
	arguments[0] = 3;
	alert(num1, arguments[0], num3);
}
{% endhighlight %}

- 所有参数只能按值传递，不能按引用传递。

{% highlight javascript %}
function addTen(num) {
	num+=10;
	return num;
}
var count=20;
var res=addTen(count);
alert(count); // 20
alert(res); //30
{% endhighlight %}

- 没有重载

- 没有块级作用域

{% highlight javascript %}
for (var i=0; i=i+1; i<10) {
  doSomething();
}
alert(i); // 10
{% endhighlight %}

- 没有var修饰的变量自动成全局变量

- 优化内存占用的最佳方法就是，用null来释放引用。解除引用的目的不是回收该变量的内存，而是让值脱离当前环境，让垃圾收集器将其回收。


