---
layout: post
title: "Javascript学习（二）"
categories: Javascript
comments: true
facebook: true
tags: Chinese
---

这篇文章主要介绍一些关于如何创建对象的知识。参考《Javascript高级程序设计》第六章-面向对象的程序设计。

### 构造函数
- 始终以一个大写字母开头
- 没有显示地创造对象
- 直接将属性和方法赋给this对象
- 没有return语句
- 任何可以用new调用的函数即为构造函数
<!--break-->
使用new操作符，调用构造函数创建实例，具体里面实现了以下步骤：
1. 创建一个新对象
2. 将构造函数的作用域赋给新对象
3. 执行构造函数中的代码
4. 返回新对象

**缺点**：每个方法都要在每个实例中重新创建一遍。

{% highlight javascript %}
function Person(name, age, job) {
  this.name = name;
  this.age  = age;
  this.job  = job;
  this.sayName = new function("alert(this.name)");
}

alert(person1.sayName == person2.sayName); // false，注意这里是两个“=”，不是三个“=”
{% endhighlight %}
但是创建两个同样任务的实例完全没必要，等会再介绍一些原型模式。

### 原型模式
每个函数都有一个prototype原型属性，可以指向一个对象，这个对象可以让所有实例共享它包含的属性和方法。

比如，使用new创建两个实例，而这两个实例都包含一个prototype的**指针**，它们都指向同一个地方。

相关方法：

- ```isPrototypeOf()```
- ```Object.getPrototypeOf()```

代码读取对象的某个属性时，会先搜索实例本身的属性值，如果没有找到就会读取原型中的属性值。

{% highlight javascript %}
function Person() {
} // 构造函数

/* 原型属性 */
Person.prototype.name = "Nicolas";
Person.prototype.age  = "14";
Person.prototype.job  = "student";
Person.prototype.sayName = function(){
  alert(this.name);
};

/* 实例 */
var person1 = new Person();
var person2 = new Person();

person1.name = "Jack";

alert(person1.sayName); // Jack ------ 来自实例
alert(person2.sayName); // Nicolas ------ 来自原型
{% endhighlight %}
给实例添加属性，会屏蔽原型属性，而不会修改原型属性。使用“delete”操作符可以恢复原型属性。**“hasOwnPropert()”可以判断实例属性是否存在。联合使用“in”去判断属性是存在于实例中还是原型中。**
取得对象中可枚举的实例属性，可以使用“Object.keys()”方法。

每创建一个函数，就会自动创建它的prototype对象，这个对象也会**自动获得constractor属性**。如上例，Person构造函数，Person的原型属性和两个实例之间的关系是：Person.prototype指向了原型，而Person.prototype.constructor又指回了**Person**。person1和person2都包含一个内部属性，该属性仅仅指向Person.prototype，他们与构造函数没有之间关系。

重写整个原型对象：
{% highlight javascript %}
function Person() {
}

Person.prototype = {
  name: "Nicolas",
  age: 14,
  job: "student",
  sayName: function() {
    alert(this.name);
  }
}

var person = new Person();
alert(person.constructor == Person); // false
alert(person.constructor == Object); // true
{% endhighlight %}
此时，constructor不再指向Person了。如果想让constructor指向Person，只需要在原型对象里面加一个constructor属性```constructor: Person```。但是只要，constructor变成可枚举的，原生的是不可枚举的。如果想让constructor指向Person，但又不可枚举，可以使用defineProerty。
{% highlight javascript %}
Object.defineProperty(Person.prototype, "constructor", {
  emumerable: false,
  value: Person
});
{% endhighlight %}

接下来看另一个例子：
{% highlight javascript %}
var friend = new Person();

Person.prototype.sayHi = function() {
  alert("hi");
};

friend.sayHi(); // “hi", 注意没有报错
{% endhighlight %}
即使在添加新方法之前创建实例，这个实例仍可以访问新方法。这是因为实例与原型之间是一个指针。所以可以随时为原型添加属性和方法，修改会立即在实例中更新。但如果是重新整个原型对象，情况则不一样。
{% highlight javascript %}
var friend = new Person();

/* 重写原型对象 */
Person.prototype= {
  name: "Nicolas",
  age: 14,
  job: "student",
  sayHi: function() {
    alert("hi");
  }
};

friend.sayHi(); // 报错
{% endhighlight %}
因为在重写原型对象之前，构造函数和实例都指向一个最初的原型对象，再重写之后，构造函数指向了新的原型对象，但是实例还是指向最初的原型对象。








