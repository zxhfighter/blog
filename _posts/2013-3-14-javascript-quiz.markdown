---
layout: post
categories: javascript
title: JavaScript Quiz
---

本文整理了JavaScript相关的一些quiz（也就是难题，偏方之类），在实际的项目中，不会出现`1..z`之类的代码，不过这些quiz能够让你深刻了解这些写法背后的原因，如果你有什么好的quiz，可以在下面留言，翘首以待中。

下面的这些quiz，如果你不参阅相关资料（下面的说明和建议，仅供参考）:

* 能够100%做对，说明你的基本功相当扎实，另外，师傅，请受徒儿一拜
* 做对70%以上，说明有一定的JavaScript基础，但还有一定的提升空间
* 做对40%以上，说明JavaScript掌握的还不够牢固，很多概念模棱两可
* 做对40%以下，那么我建议你仔细重温下如下书籍再来做一遍，这些书会让你如入桃花源，柳暗花明又一村
   * [《JavaScript Patterns》](http://shop.oreilly.com/product/9780596806767.do)
   * [《JavaScript The Definitive Guide》](http://shop.oreilly.com/product/9780596805531.do)
   * [《Eloquent JavaScript》](http://eloquentjavascript.net/)
   * [《JavaScript The Good Parts》](http://shop.oreilly.com/product/9780596517748.do)
   * [《JavaScript Garden》](http://bonsaiden.github.com/JavaScript-Garden/zh/)
   * [《Maintainable JavaScript》](http://shop.oreilly.com/product/0636920025245.do)
   * 还有更多，不过如果你好好消化了上面书籍的内容的话，你会发现，更多的书籍也只不过是内容的重复

## 关于arguments

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：该IIFE返回什么结果？
// PS: IIFE（Immediately-Invoked Function Expression），即自调用函数、立即执行函数
(function(){
   return typeof arguments; 
})();

// quiz-2：该IIFE返回什么结果？
(function(){
   return Object.prototype.toString.call(arguments).slice(8, -1); 
})();

// quiz-3：该IIFE返回什么结果？
(function(){
   return Object.prototype.toString.call([].slice.call(arguments)).slice(8, -1);
})(1,'2',new Date(),4);

// quiz-4：console.log输出什么？
function b (x, y, a) {
    arguments[2] = 10;
    console.log(a);
}
b(1,2,3);

// quiz-5：f()输出什么？
function f(x, y) {
    x = 10;
    console.log(
       arguments[0],
       arguments[1]
    );
}
f();
</pre>

## 关于函数表达式

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：typeof g()结果为？
var f = function g() { return 42; };
typeof g();

// quiz-2：下面的IIFE返回什么？
(function(f){
   return typeof f(); 
})(function(){ return 1; });

// quiz-3：typeof f的值？
var f = (function f() {
    return '1';
}, function g() {
    return 2;
})();
typeof f;
</pre>

## 关于变量声明和运算顺序

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：x的值？
var y = 1, x = y = typeof x;
x

// quiz-2：a的值？
var a = 1;
var b = function a (x) {
    x && a(--x);
};
console.log(a);

// quiz-3：下面表达式的值？
(1, 5-1) * 2

// quiz-4：下面几条语句输出？
var b = 10,
    c = (
            20,
            function(x) {return x + 100},
            function() {return arguments[0]}
        );
a = b + c
({x:10}).x
</pre>

## 关于变量提升

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：写出所有输出结果
var abc = 'global_var';
(function(){
    console.log(typeof abc);
    console.log(typeof fun_1);
    console.log(typeof fun_2);
    var abc = 'local_var';
    function fun_1() {}
    var fun_2 = function (){};
    console.log(typeof abc);
    console.log(typeof fun_1);
    console.log(typeof fun_2);
})(window);

// quiz-2：x的值？
var x = 1;
if (function f(){}) {
    x += typeof f;
}
x

// quiz-3：iife返回结果？
(function(){
   function f() { return 1; }
   return f();
   function f() { return 2; } 
})(window);

// quiz-4：a的值？
function a (x) {
    return x * 2;
}
var a;
console.log(a);

// quiz-5：iife返回结果？
(function(){
   a = function() { return 1; };
   function a () {
        return 2;
   }
   var a;
   return a;
})(window);
</pre>

## 关于delete运算符

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：写出所有输出
var global_var = 1;
(function(x){
   global_var_imply = '2';
   var local_var = 3;
   var arr = [1,2,3,4,5];
   var obj = {x:1, y:2, z:3};
   delete(global_var)
   delete(global_var_imply)
   delete(local_var)
   delete(x)
   delete(arr[2])
   delete(obj.y)
   console.log(typeof global_var);
   console.log(typeof global_var_imply);
   console.log(typeof local_var);
   console.log(typeof x);
   console.log(arr);
   console.log(arr.length);
   console.log(obj);
})(1);
</pre>

## 关于this

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：下面两个iife的返回结果？
var foo = {
    bar: function () {
        return this.baz;
    },
    baz: 1
};

(function(){
   return typeof arguments[0](); 
})(foo.bar);

(function(){
   return typeof arguments[0].call(foo); 
})(foo.bar);


// quiz-2：typeof返回结果？
var foo = {
    bar: function () {
        return this.baz;
    },
    baz: 1
};
typeof (f = foo.bar)();

// quiz-3：写出所有输出
var x = 10;
var foo = {
    x: 20,
    bar: function () {
        var x = 30;
        return this.x;
    }
};
console.log(
    foo.bar(),
    (foo.bar)(),
    (foo.bar = foo.bar)(),
    (foo.bar, foo.bar)()
);
</pre>

## 关于typeof

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：写出下面几个typeof的结果
var x = [typeof x, typeof y][1];
typeof typeof x;
typeof typeof(null);
typeof foo == 'undefined';
typeof foo === 'undefined';
</pre>

## 关于JSON对象

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：iife的返回结果?
(function(foo){
   return typeof foo.bar; 
})({foo: {bar: 1}});
</pre>

## 关于类和对象

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：true or false?
function F() { return F; }
new F() instanceof F

// quiz-2：下面运行结果输出什么？
100.toString.length
100['toString']['length'];
100 .toString();
(100).toString();
1..z;
</pre>

## 关于with

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：输出结果
with (function(x, undefined){}) length
</pre>

## 关于++

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：result的值？
var num1 = 5, num2 = 6, result = num1+++num2;
result
</pre>

## 关于作用域

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：a的值？
if (!("a" in window)) {
    var a = 1;
}
console.log(a);
</pre>

## 关于setTimeout

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：o.doIt()的输出？
var x = 5;
var o = {
    x: 10,
    doIt: function doIt() {
        var x = 20;
        var timer = setTimeout(
            function() {
                console.log(this.x);
            },
            10
        );
    }
};
o.doIt();
</pre>

## 关于字符串和数字的比较和转换

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：写出所有输出
var num1 = '10',
    num2 = '9';
console.log(num1 < num2);
console.log(+num1 < num2);
console.log(num1 + num2);
console.log(+num1 + num2);
</pre>

##关于字符串截断

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：写出所有输出
var msg = 'Hello, world';
msg.substring(1,4)
msg.substr(1,4)
msg.slice(1,4)
</pre>

## 关于valueOf和toString

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：写出所有输出
var obj = {
    x: 8,
    valueOf: function() {
        return this.x + 2;
    },
    toString: function() {
        return this.x.toString();
    }
};

console.log(obj + 1);
obj < "9"
</pre>

## 关于parseInt

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：两个parseInt的值？
parseInt('06')
parseInt('08')
</pre>

## 关于对象初始化

<?prettify lang=javascript linenums=true?><pre class="prettyprint">
// quiz-1：函数执行结果？
({
  x: 10,
  foo: function () {
    function bar() {
      console.log(x);
      console.log(y);
      console.log(this.x);
    }
    with (this) {
      var x = 20;
      var y = 30;
      bar.call(this);
    }
  }
}).foo();
</pre>

关于正确答案，唔...，先卖个关子，亲，不会让你们久等的。