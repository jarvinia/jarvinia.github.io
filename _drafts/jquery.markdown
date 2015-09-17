---
layout: post
title:  "初识jQuery"
date:   2015-07-09 14:46:34
categories: jQuery
comments: true
facebook: true
tags: Chinese 
---

###概念
- window
下面的代码会加载所有的图片文字后，再执行function内的代码

{% highlight javascript %}
window.onload = function() {
 
    alert( "welcome" );
 
};
{% endhighlight %}

- document
而下面的代码会在窗口刚加载的适合就已经执行function内的代码

{% highlight javascript %}
$( document ).ready(function() {
 
    // Your code here.
 
});
{% endhighlight %}
