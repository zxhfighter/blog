---
layout: post
categories: javascript
title: 判断一个变量是否为数组
---

问题缘起
--------
最近在看less.js的源码，想从中了解一些我还不知道的东西，看到了这样一段代码：

<?prettify lang=javascript linenums=true?>
<pre class="prettyprint"><code>if (!Array.isArray) {
    Array.isArray = function(obj) {
        return Object.prototype.toString.call(obj) === "[object Array]" ||
               (obj instanceof Array);
    };
}
</code></pre>

这段代码先判断Array是否有isArray属性，该属性在ES5中定义，如果判断的变量为数组返回true，否则返回false；在不支持ES5的浏览器中则定义了Array.isArray方法，用来判断变量是否为数组。

先撇开上面的代码，本文先来讨论，有哪些方法可以判断一个变量是否为数组？

typeof运算符
----
大家可能一开始就想到使用`typeof`运算符来获取变量的类型。但是`typeof`
运算符功能实在有效，只能返回如下字符串中的一种："number"、"string"、"boolean"、"object"、"function"、"undefined"。

<?prettify lang=javascript linenums=true?>
<pre class="prettyprint"><code>//尽管null不为对象，但是typeof返回为object
typeof null
typeof undefined
typeof []
typeof 2.322
typeof 'null'
typeof {}
typeof window.alert
typeof new Date();
</code></pre>

instanceof
----
instanceof可以判断对象是否是由某个构造函数构造而来，在浏览器中测试`[] instanceof Array`，返回为true，看来这个方法可行，窃喜。

<?prettify lang=javascript linenums=true?>
<pre class="prettyprint"><code>[] instanceof Array
[] instanceof Object
[] instanceof Date
</code></pre>

constructor属性
----
每一个对象都有一个constructor对象，指向该对象的构造函数，所以可以写出如下的检测代码，首先通过typeof来检测变量是否为对象，然后进一步检测其构造函数是否为Array。

<?prettify lang=javascript linenums=true?>
<pre class="prettyprint"><code>typeof arr == "object" && arr.constructor == Array
</code></pre>

上述代码在检测跨框架（cross-frame）页面中的数组时，就会失败。原因是在不同框架（iframe）中创建的数组不会共享其prototype属性，见下面的例子。

<?prettify lang=javascript linenums=true?>
<pre class="prettyprint"><code>//打开web.qq.com，该网站有很多iframe，可以运行下面的代码
var iframe_arr = new window.frames[0].Array;
iframe_arr instanceof Array // false
iframe_arr.constructor == Array // false
</code></pre>

所以，使用constructor属性来判断有点不靠谱，不靠谱了就是不可行。

Object.prototype.toString检测
-----------------------------
在本文开始，就提出了一种检测方法，该方法就是使用了Object.prototype.toString检测，可以解决上面的跨框架问题。

Object.prototype.toString(o)的执行过程如下：
* 获取对象o的class属性
* 连接字符串："[object " + class属性 + "]"
* 返回上述结果

<?prettify lang=javascript linenums=true?>
<pre class="prettyprint"><code>var toString = Object.prototype.toString();
toString.call([]);
toString.call(new Date());
toString.call(/reg/ig);
</code></pre>

扩展一下，扩展一下
-------------------

下面的代码能够检测各种类型，例如isArray方法、isRegExp方法等等。

<?prettify lang=javascript linenums=true?>
<pre class="prettyprint"><code>function __getClass(object) {
    return Object.prototype.toString.call(object).match(/^\[object\s(.*)\]$/)[1]; 
}

var is = {
    types: ["Array", "Boolean", "Date", "Number", "Object", "RegExp", "String", "Window", "HTMLDocument"];
}

for (var i = 0, c; c = is.types[i++];) {
    is[c] = (function(type){
        return function(obj) {
            return Object.prototype.toString.call(obj) === "[object " + type + "]";
        }
    })(c);
}

is.Array([])
is.Date(new Date())
is.RegExp(/reg/ig)
</code></pre>

总结
==========
Object.prototype.toString方法能够精确的检测对象类型，结合之前可行的instanceof方法，简直就是万全之策、无懈可击了。