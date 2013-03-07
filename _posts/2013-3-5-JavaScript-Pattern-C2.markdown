---
layout: post
categories: javascript
title: 第二章 JS要点（JS模式）
---

编写可维护的代码
-------------------
这是一个老生常谈的话题，一般读代码的时间可能要比你写代码的时间还要多，可维护的代码需要达到如下几点要求。

* 可读性：有缩进、适当的空白等。
* 一致性：编写的代码前后规范必须一致。
* 可预测性：如果你来写，可以按照现有代码编写出一致性的代码。
* 所有代码看起来是一个人所写的。
* 文档化。

尽量避免全局变量
-------------------
JavaScript使用函数来管理作用域，在函数内使用var定义的变量就成了局部变量，没有使用var定义的变量就成了全局对象（例如window）的一个属性，由于各种库、插件、第三方代码，全局变量很容易被污染，应尽量避免或减少全局变量的使用。

<?prettify lang=javascript linenums=true?>
<pre class="prettyprint"><code>// antipattern
myglobal = "hello";
console.log(myglobal);
console.log(window.myglobal);
console.log(window["myglobal"]);
console.log(this.myglobal);

function sum(x, y) {
    // antipattern: implied global，use var result = x + y
    result = x + y;
    return result;
}

function foo() {
    // antipattern: antipattern，same as var a = ( b = 0 )， b is implied global
    var a = b = 0;
}
</code></pre>

因此得出的最佳实践使用var定义变量（无论全局还是局部），如果一个全局变量没有使用var定义，该全局变量不能算作一个真正的变量，而只是全局对象的一个属性，因为它能够被delete运算符删除，真正的变量是无法被delete删除的（返回false）。

<?prettify lang=javascript linenums=true?>
<pre class="prettyprint"><code>// define trhee globals
var global_var = 1;

// antipattern
global_novar = 2;

(function(){
    // antipattern
    global_fromfunc = 3;
}());

delete global_var;
delete global_noval;
delete global_fromfunc;

typeof global_val;
typeof global_novar;
typeof global_fromfunc;
</code></pre>

在ES5的严格模式中，给未定义的变量赋值会报错。

访问全局变量
------------
除非你重新定义了window，否则一般获取的全局变量就为window，直接调用window还是有一定的风险，属于硬编码，更好的获取全局变量的方式如下。

<?prettify lang=javascript linenums=true?>
<pre class="prettyprint"><code>
var global = (function(){
    // this always return the global object
    return this; 
}());
</code></pre>

单var模式
---------
该模式是指在函数的最上面将函数中出现的所有变量定义一遍，并加以说明，以后有人想查看某个变量的定义，直接来函数头部找就行，提供的好处有：

* 提供一个公共变量区域，便于查找。
* 避免变量在定义之前使用的问题。
* 使用var定义变量，避免变量变成全局变量。
* 更少代码。

需要注意的是，删除变量时或者增加变量时，要注意逗号和分号的正确使用。

<?prettify lang=javascript linenums=true?>
<pre class="prettyprint"><code>// uses single var pattern
function() {
    var a = 1,
        b = 2,
        c = 1 + 2,
        i = 0,
        j;
}
</code></pre>

变量提升
--------
JavaScript能够让你在函数的任意地方定义变量，这些变量实际上都会在函数的头部（所以叫变量提升）定义一遍。

<?prettify lang=javascript linenums=true?>
<pre class="prettyprint"><code>// antipattern
myname = 'global';

function func() {
    console.log(myname);
    var myname = 'local';
    console.log(myname);
}
func();

// the above code behaves as follows
// antipattern
myname = 'global';

function func() {
    // variable hoisting
    var myname;
    console.log(myname);
    var myname = 'local';
    console.log(myname);
}
func();
</code></pre>

for循环
-------
当使用for循环来遍历arguments或者HTMLCollection之类的对象时，先缓存length，避免每次取length花费的开销。
常见的HTMLCollections，如下：
* document.getElementsByName()
* document.getElementsByClassName()
* document.getElementsByTagName()
* document.images
* document.links
* document.forms
* document.forms[0].elements

<?prettify lang=javascript linenums=true?>
<pre class="prettyprint"><code>for (var i = 0, max = document.images.length; i < max; i++) {
    // do something
}
</code></pre>

上面使用++版的for循环需要两个变量，使用--版的for循环能够减少一个变量（JSHint会警告使用++或--，可以配置不显示此类警告）。

<?prettify lang=javascript linenums=true?>
<pre class="prettyprint"><code>var i, myarr = [];
for (i = myarr.length; i--) {
    // do something
}

var myarr = [],
    i = myarr.length;

while (i--) {
    // do something
}
</code></pre>

for-in循环
----------
一个典型的for-in循环如下，该for-in循环跳过了继承的属性和对象中的方法。

<pre class="prettyprint"><code>for (var key in object) {
    // 跳过继承属性
    if (!object.hasOwnProperty(key)) continue;

    // Object.prototype.hasOwnProperty.call(object, key)

    // 跳过方法
    if (typeof object[key] === 'function') continue;

    // dosomething
}
</code></pre>

尽量不要扩展内置对象的原型
--------------------------
尽管给构造函数的原型对象扩展方法是功能强大的体现之一，也正是由于其过于强大的功能，需要限制其使用，如果符合以下条件，可以考虑使用。

* 可预测：符合未来ES规范，例如Array有isArray方法，但是低版本IE不支持，你可以给Array定义一个isArray方法
* 先检测：检测某个功能不存在，然后再添加该功能
* 有说明：对扩展的方法进行详细说明

<?prettify lang=javascript linenums=true?>
<pre class="prettyprint"><code>if (!Array.isArray) {
    Array.isArray = function(obj) {
        return Object.prototype.toString.call(obj) === "[object Array]" ||
               (obj instanceof Array);
    };
}
</code></pre>

swtich模式
----------
下面的switch语句结果是什么？

<?prettify lang=javascript linenums=true?>
<pre class="prettyprint"><code>var x = 0;
var result;
switch (x) {
    case '0':
        result = 0; break;
    case '1':
        result = 1; break;
    default:
        result = 2;
}
result
</code></pre>

上面的代码说明了switch语句中，变量与case中的值比较是严格比较===，因此需要注意做类型转化！

避免使用eval
------------
`eval() is evil`，之所以大家给eval这样的评价，部分原因eval能将传递给它的字符串解释成JavaScript执行，执行出来的结果，对于当前环境，可能造成不可预知的影响，所谓能力越大，破坏力也越强，不能不防，见下面的代码。

<?prettify lang=javascript linenums=true?>
<pre class="prettyprint"><code>// antipattern
var property = 'name';
alert(eval("obj." + property));

// preferred
var property = 'name';
alert(obj[property]);
</code></pre>

在JavaScript中，如果给new Function()传递字符串，或者给setTimeout和setInterval传递字符串时，会隐式的调用eval来解析字符串，因此要避免这些情况，直接传递函数。

parseInt注意事项
----------------
parseInt能够将字符串转化成整数，完整的形式为`parseInt(string, radix)`，第二个参数指明了整数的进制，先看下面的代码。

<?prettify lang=javascript linenums=true?>
<pre class="prettyprint"><code>var month = '06';
var year = '09';
month = parseInt(month)
year = parseInt(year)

month = parseInt(month, 10)
year = parseInt(year, 10)
</code></pre>

编码习惯
--------
缩进、空格、括号、命名、注释、文档，每个团队有每个团队的习惯，尽量遵守即可。