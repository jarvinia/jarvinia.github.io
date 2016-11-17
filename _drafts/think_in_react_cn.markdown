---
layout: post
title:  "React 编程思想"
categories: frontend
comments: true
facebook: true
tags: Chinese
---
> 原文链接：<https://facebook.github.io/react/docs/thinking-in-react.html>

我认为 React 主要功能是可以利用 JavaScript 去建大型且快速的 web 应用。在 Facebook 和 Instagram，React 一直为我们所用。

React 众多优点之一就是，所想即所建，即怎么构想你的应用，你就可以用 React 怎么去构建它们。这篇文档中，我将会一边整理我的构建应用的思路，一边用 React 去创建一个可搜索的数据表。
<!--break-->

## 从视觉稿开始

### ___Start With A Mock___

假设我们已经有一个 JSON API 和一个来自设计师的视觉稿。 很显然，我们的设计师并不怎么出色，因为视觉稿长成这样：

<img src="https://facebook.github.io/react/img/blog/thinking-in-react-mock.png" />

而我们的 JSON API 返回的数据如下：
{% highlight json %}
[
  {category: "Sporting Goods", price: "$49.99", stocked: true, name: "Football"},
  {category: "Sporting Goods", price: "$9.99", stocked: true, name: "Baseball"},
  {category: "Sporting Goods", price: "$29.99", stocked: false, name: "Basketball"},
  {category: "Electronics", price: "$99.99", stocked: true, name: "iPod Touch"},
  {category: "Electronics", price: "$399.99", stocked: false, name: "iPhone 5"},
  {category: "Electronics", price: "$199.99", stocked: true, name: "Nexus 7"}
];
{% endhighlight %}

## 第一步：将用户界面分割成一个组件层级

### ___Step 1: Break The UI Into A Component Hierarchy___

首先要做的一件事就是，将视觉稿中的每个组件（还有子组件）的外围边框画出，并且给它们命名。如果你是和设计师一起工作的话，你会发现他们早已命名好了，其实，他们的 Photoshop 的图层名就可以当做 React 的组件名。

但是怎样才知道哪些能成为一个独立的组件呢？我们将用同样的技术原则去决定你是否需要创建新的方法或用例。这个技术原则就是单一责任原则（ _sigle responsibility principle_ ）。也就是说，一个组件只干一件事。如果这个组件还干了其他事，就需要考虑分割出更小的子组件了。

由于你经常向用户展示 JSON 数据，你会发现好的数据模型可以很好的被映射到相应的用户界面和组件结构。这是因为用户界面和数据需要传递同样的信息结构，它俩密切相关。也就是意味着，将用户界面分割出不同的组件，而每个组件都能准确地表示一个数据模型，而这项工作是非常繁琐的。

<img src="https://facebook.github.io/react/img/blog/thinking-in-react-components.png" />

我们这个简单的应用里有5个组件。我们已经把每个组件所代表的数据标注出来了。

- 可过滤的产品表 `FilterableProductTable` (橘色): 包含了这个数据表整体
- 搜索条 `SearchBar` (蓝色): 接收所有用户数据输入
- 产品表 `ProductTable` (绿色): 根据用户输入，显示和删选数据结果
- 产品类别行 `ProductCategoryRow` (青绿色): 显示产品类别的标题
- 产品行 `ProductRow` (红色): 按行显示一个产品

如果你仔细看产品表`ProductTable`，你会发现表标题标签（ "Name" 标签和 "Price" 标签 ）并不是一个独立的组件。这事关性能，但无论哪种方法都有它的理由。比如，我们之所以将它作为产品表`ProductTable`的一部分，是因为它是渲染数据的一部分，这正是产品表`ProductTable`的职责。但是，如果标题标签逐渐变大变复杂了，比方说，我们再增加一个可供性（ _affordances_ ）的标签，这个时候就必须要有一个独立的产品表头`ProductTableHeader`的组件了。

我们已经在视觉稿中定义好了各个组件，现在把它们分层排列一下。简单点说，就是视觉稿中包含在另外一个组件中的组件，将表示成层级中的子节点。

- `FilterableProductTable`
	- `SearchBar`
	- `ProductTable`
		- `ProductCategoryRow`
		- `ProductRow`

## 第二步：在 React 中建一个静态的页面

### ___Step 2: Build A Static Version in React___
<!-- <pre class="codepen" data-height="470" data-type="result" data-href="vXpAgj" data-user="lacker" data-safe="true"><code></code><a href="http://codepen.io/lacker/pen/vXpAgj">Check out this Pen!</a></pre>
<script async src="http://codepen.io/assets/embed/ei.js"></script> -->
<p data-height="265" data-theme-id="0" data-slug-hash="vXpAgj" data-default-tab="js,result" data-user="lacker" data-embed-version="2" data-pen-title="Thinking In React: Step 2" class="codepen"><a href="http://codepen.io/lacker/pen/vXpAgj/">请转向CodePen演示代码.</a></p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

既然我们已经有了组件的层级图，现在可以开始实现这个应用了。最简单的办法是，先建立一个包含数据模型，渲染用户界面的时候尚不交互的版本。而且最好将这些工程解耦，因为建立一个静态版本的时候，需要输入很多代码，而不必思考很久 ，相反，添加交互功能后的版本就需要仔细构思，而增加的代码却不多。我们来看这是为什么。

在建立一个可以渲染数据的静态应用的时候，你将想到的是建立很多组件，而且这些组件可以重用其它组件，通过使用`props`来传入数据。因为我们可以利用`props`从父节点向子节点传入数据。如果你熟悉状态`state`的概念的话，**你就完全不会在建立静态版本的时候使用`state`**。只有状态`state`是预留给交互版本的，因为数据会时刻变化。 考虑到这是个静态版本，所以我们暂时不需要用到它。

你可以选择从上至下或从下至上来建立这个应用。 也就是说，你可以先建立层级图中的顶层组件`FilterableProductTable`或者最底层的组件`ProductRow`。从上至下最为简单，但是在大型工程中，比较倾向从下至上，这样比较容易边写边测试。

最后一步，你需要建立一个可重用的组件库，而这些组件可以渲染你的数据模型。而这个静态版本的组件只有```render()```这个方法。顶层组件`FilterableProductTable`会将你的数据模型看成一个`props`。如果你修改了底层数据模型，只要再次调用```ReactDOM.render()```，用户界面就会更新。很容易发现你的用户界面更新了，在什么地方做了变动，这里面没有什么复杂的东西。React的单向数据流（或称单向绑定）可以保持应用平稳高速地运行。

如果需要获得更多帮助来实现这个步骤，可以参考[React文档](https://facebook.github.io/react/docs/ "React docs")。

**插曲: Props vs State**

在React里有两种“模型”数据：`props`和`state`。会区分这两者很重要；如果你不确定这两者区别，请浏览[React文档](https://facebook.github.io/react/docs/interactivity-and-dynamic-uis.html)。

## 第三步: 定义用户界面状态的最小非完整版表达方式

### ___Step 3: Identify The Minimal (but complete) Representation Of UI State___

为了让用户界面可交互，你必须能够触发修改底层数据模型。React利用`state`将这一过程变得更为简单了。

准确地构建应用，首先你需要为你的应用建立一个可变的状态最小集。而这里面的要点就是 __DRY__：不要做重复的工作（_Don't Repeat Yourself_）。想想为什么你的应用需要一个最小的状态集，再考虑除了你随即需要的所有东西。比如，你在建立一个执行清单，只需要有一个保存执行项的数组，而不需要再用一个状态变量用于计数。取而代之，当你想获得执行项的个数时，简单地获得数组长度即可。

现在想想我们的应用里需要用到的所有数据。我们有：

- **产品的初始清单**（_The original list of products_）
- **用户键入的搜索词**（_The search text the user has entered_）
- **复选框的值**（_The value of the checkbox_）
- **产品的过滤清单**（_The filtered list of products_）

我们所有的数据过一遍，看看哪一个才是 "state" 。针对每一个数据，简单地问三个问题：

1. 是否能通过`props`从父节点传递这个数据到子节点？如果可以，那就不是`state`。
2. 这个数据值是否始终不变呢？如果是，那就不是`state`。
3. 你是否能够通过其他的`props`或`state`得到这个数据值呢？如果可以，那就不是`state`。

可以通过`props`传递**产品的初始清单**，所以它就不是`state`。 **搜索词**和**复选框的值**看上去好像是`state`，因为它们不是固定的，而且不能通过其它的数据来获得。最后一个，**产品的过滤清单**也不是`state`，因为它能够通过**产品的初始清单**结合**搜索条**和**复选框的值**来获得。

所以，我们的 "state" 就是：

- **用户键入的搜索词**
- **复选框的值**

## 第四步：定义 State 的作用域

### ___Step 4: Identify Where Your State Should Live___

<p data-height="265" data-theme-id="0" data-slug-hash="ORzEkG" data-default-tab="js,result" data-user="lacker" data-embed-version="2" data-pen-title="Thinking In React: Step 4" class="codepen"><a href="http://codepen.io/lacker/pen/ORzEkG/">请转向CodePen演示代码.</a></p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

那么我们已经定义好了应用状态最小集。接下来，我们需要定义哪一个组件如何变换或者拥有状态值。

记住React中只有顺着组件层级向下的单向数据流。你很可能不能立刻很清楚地知道哪个组件拥有状态值。**这也是新手常难以理解的地方**，但只要跟随下面的步骤就很容易想明白：

对于应用中的`state`属性，需要：

- 定义每一个基于`state`渲染出东西的组件。
- 找出一个**共有的所有者组件**（一个单独的，置于所有在层级中需要`state`属性的组件之上的组件）。
- 共有的所有者组件，或在层级中处于较高位置中的组件需要拥有`state`属性。
- 如果你无法找到这么一个拥有`state`属性的组件，那就创造一个组件，让它拥有`state`属性，并且将它置于层级中，高于那个**共有的所有者组件**。
 
现在我们可以在应用中执行上面的策略：

- `ProductTable`需要基于`state`属性来过滤产品清单，而`SearchBar`需要显示搜索词和复选框。
- `FilterableProductTable`就是**共有的所有者组件**。
- 过滤词和复选框的值存在于整个`FilterableProductTable`之中，这样概念上就说得过去了。

好了，我们就确定好了在`FilterableProductTable`中的`state`属性了。首先，添加一个实例属性`this.state = {filterText: '', inStockOnly: false}`到`FilterableProductTable`的构造函数中去，这样可以反映出应用的初始状态。然后，将`filterText`和`inStockOnly`作为`props`属性传递到`ProductTable`和`SearchBar`。最后，可以利用这些`props`属性从整个`ProductTable`中过滤出产品搜索结果和设置`SearchBar`的表单文本字段的值。

现在你可以看看你的应用运行得如何：在搜索框中输入 "ball" （ 即`filterText`设置成 "ball" ），再刷新页面。这样你就会看见筛选后的结果。


## 第五步：添加反向数据流

### ___Step 5: Add Inverse Data Flow___

<p data-height="265" data-theme-id="0" data-slug-hash="ALxdJq" data-default-tab="js,result" data-user="lacker" data-embed-version="2" data-pen-title="Thinking In React: Step 5" class="codepen"><a href="http://codepen.io/lacker/pen/ALxdJq/">请转向CodePen演示代码.</a></p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

目前为止，我们的应用已经可以顺着层级从上至下，利用_props_和_state_正确地渲染。现在是时候添加反数据流的方式了：在`FilterableProductTable`中，层级中的底层组件需要更新`state`。

React尽可能使得这个数据流明晰点，这样可以很容易地演示你的程序是如何工作的。但是，相比于传统的双向数据绑定，这会输入更多的代码。

如果你尝试在目前这个版本输入或钩复选框，你会发现React毫无反应。这是有意为之的，因为我们已经设置了输入文本字段（ `input` ）的值，而这个值就等同于从`FilterableProductTable`传递`state`。

让我们来仔细想想我们想要让它做什么。我们需要的是，无论用户什么时候去修改表单，我们会根据用户输入去更新`state`。因为每个组件只更新它们各自的`state`，`FilterableProductTable`将会传入一个回调函数（ _callback_ ）给`SearchBar`，由这个函数更新`state`。我们利用一个`onChange`事件去跟踪输入文本的变化。而这个由`FilterableProductTable`传入的回调函数就是`setState()`，这样整个应用就可以工作起来了。

虽然这听上去很复杂的样子，但是真的只有那么几行代码。也很细致地演示了贯穿整个应用的数据流。

## 写在最后

### ___And That's It___

希望能帮助你去理解如何用 React 创建组件和应用。也许在这过程中，你会输入比以往更多的代码，但是你要记住，代码能够被读懂远比写代码重要，而这个模块就是用了很详尽的代码为了让你更容易读懂。如果你要创建一个大型的组件库，你会很感激整个组件清晰，模块化，还有代码可重用，这样你才开始给代码减重:)
